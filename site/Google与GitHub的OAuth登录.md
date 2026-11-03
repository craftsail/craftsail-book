# Google 与 GitHub 的 OAuth 登录

出海的工具站，登录基本就两个按钮：「Continue with Google」和「Continue with GitHub」。不用自己做密码、找回、邮箱验证，用户也少填一张表。

这篇只讲这两个按钮怎么接：去哪个后台建应用、回调地址怎么填、授权地址和换 token 的接口是什么、整个流程每一步发什么请求、代码怎么写、上线前查什么。

---

## 零、先看全局

两家用的都是 OAuth 2.0 的**授权码模式（Authorization Code）**，再加上 **PKCE** 和 **state**。流程是一样的：

```text
浏览器                       你的服务器                     Google / GitHub
  |  点「用 Google 登录」         |                               |
  |----------------------------->|                               |
  |                              | 生成 state、code_verifier       |
  |                              | 写进 HttpOnly cookie            |
  |  302 → 授权地址?client_id&state&code_challenge...             |
  |<-----------------------------|                               |
  |------------------------------------------------------------->|
  |                    用户登录、点「允许」                          |
  |  302 → 你的回调地址?code=xxx&state=yyy                          |
  |<-------------------------------------------------------------|
  |----------------------------->|                               |
  |                              | 1. 比对 state 和 cookie         |
  |                              | 2. POST token 接口：code +      |
  |                              |    client_secret + verifier    |
  |                              |------------------------------>|
  |                              |<---- access_token (+id_token) |
  |                              | 3. 拿用户信息（id、邮箱、头像）    |
  |                              | 4. 建用户 / 找用户，发自己的会话    |
  |  302 → /dashboard，Set-Cookie: session=...                    |
  |<-----------------------------|                               |
```

要记住的几点：

- **换 token 必须在服务器上做**。`client_secret` 只能放在服务端环境变量里，不能出现在前端代码里。
- **state** 防 CSRF：发出去的和回来的不一致就拒绝。
- **PKCE**（`code_verifier` / `code_challenge`）防授权码被截走后被别人换成 token。Google 一直支持，GitHub 2025 年 7 月开始支持（只接受 `S256`）。
- 第三方给你的 token 只用来**拿一次用户信息**。之后用户在你站上的登录态是你自己发的 session cookie，不是 Google / GitHub 的 token。
- 用户的唯一标识用平台给的**不变 ID**：Google 用 `sub`，GitHub 用数字 `id`。不要用邮箱，也不要用 GitHub 的 `login`（用户名可以改）。

---

## 一、两家的地址一览

| | Google | GitHub |
| --- | --- | --- |
| 建应用的后台 | <https://console.cloud.google.com/auth/clients> | <https://github.com/settings/developers> |
| 授权地址（浏览器跳过去） | `https://accounts.google.com/o/oauth2/v2/auth` | `https://github.com/login/oauth/authorize` |
| 换 token（服务器 POST） | `https://oauth2.googleapis.com/token` | `https://github.com/login/oauth/access_token` |
| 用户信息 | `https://openidconnect.googleapis.com/v1/userinfo`，或直接解 `id_token` | `https://api.github.com/user` |
| 邮箱 | `id_token` 里的 `email`、`email_verified` | `https://api.github.com/user/emails` |
| 撤销 token | `https://oauth2.googleapis.com/revoke` | `DELETE https://api.github.com/applications/{client_id}/grant` |
| 配置发现 | `https://accounts.google.com/.well-known/openid-configuration` | 没有，GitHub 不是 OIDC |
| 登录用的 scope | `openid email profile` | `read:user user:email` |
| 回调地址能填几个 | 多个 | **一个** |
| 用户唯一 ID | `sub`（字符串） | `id`（数字） |

Google 的地址以 discovery 文档为准，可以随时核对：

```bash
curl -s https://accounts.google.com/.well-known/openid-configuration \
  | grep -E '"(authorization|token|userinfo|revocation)_endpoint"|"jwks_uri"'
```

回调地址建议统一成这种格式，dev 和线上各一套：

```text
https://example.com/api/auth/callback/google
https://example.com/api/auth/callback/github
http://localhost:4321/api/auth/callback/google
http://localhost:4321/api/auth/callback/github
```

---

## 二、Google：在后台建应用

入口：<https://console.cloud.google.com/>。2025 年起，原来「API 和服务 → OAuth 同意屏幕 / 凭据」那一套挪进了 **Google Auth Platform**（左侧菜单搜 “Google Auth Platform”），分成「品牌塑造 Branding」「目标对象 Audience」「客户端 Clients」「数据访问 Data Access」几页。

### 1. 建项目

顶部项目下拉 → 新建项目，名字随便，比如 `example-prod`。建议 dev 和 prod 用同一个项目、两个客户端；或者干脆两个项目，互不影响。

### 2. 品牌塑造（Branding）

<https://console.cloud.google.com/auth/branding>

| 字段 | 填什么 |
| --- | --- |
| App name | 产品名，会出现在授权页「登录以继续使用 xxx」 |
| User support email | 你的邮箱或 Google 群组 |
| App logo | **先别传**。传了 logo 就要走品牌验证，没验证前授权页可能显示警告 |
| App domain | 首页、隐私政策、服务条款三个链接，都要是能打开的页面 |
| Authorized domains | 填根域名 `example.com`。回调地址的域名必须在这里 |
| Developer contact | 你的邮箱，Google 发通知用 |

### 3. 目标对象（Audience）

<https://console.cloud.google.com/auth/audience>

- **User type** 选 **External（外部）**。Internal 只有 Google Workspace 组织内的人能登，选错会报 `org_internal`。
- **Publishing status**：新建是 **Testing（测试）**。测试状态下只有「测试用户」列表里的账号能登，最多 100 个，其他人会看到 `403 access_denied`。
- 准备上线时点 **Publish app（发布应用）**，切到 **In production**。只用 `openid email profile` 这三个非敏感 scope 的，发布后不需要提交审核，任何 Google 账号都能登。

### 4. 数据访问（Data Access）

<https://console.cloud.google.com/auth/scopes>

添加 scope：`openid`、`.../auth/userinfo.email`、`.../auth/userinfo.profile`。登录只要这三个。要读 Gmail、Drive 之类的就是敏感 / 受限 scope，要提交验证，周期以周计，和登录分开做。

### 5. 建客户端（Clients）

<https://console.cloud.google.com/auth/clients> → **Create client**

| 字段 | 填什么 |
| --- | --- |
| Application type | **Web application** |
| Name | `web-prod`，只给自己看 |
| Authorized JavaScript origins | `https://example.com`。只做服务端跳转可以不填；用 Google 官方按钮 / One Tap 时必须填 |
| Authorized redirect URIs | `https://example.com/api/auth/callback/google`，dev 再加一条 `http://localhost:4321/api/auth/callback/google` |

点创建后弹出 **Client ID** 和 **Client secret**。

> 注意：2025 年 6 月起，**Client secret 只在创建时显示一次**，关掉弹窗后后台只显示后 4 位。当场下载 JSON 或复制进密码管理器。丢了只能在客户端详情页「Add secret」轮换一个新的，Client ID 不变。

另外两个规矩：

- 回调地址必须 **完全一致**：协议、域名、端口、路径、末尾斜杠都算。不支持通配符，不能用 IP（`localhost` 除外），线上必须 `https`。
- 改完配置**几分钟到几小时**才生效，刚改完报 `redirect_uri_mismatch` 先等一会儿。
- 长期没人用的客户端会被 Google 自动删除（官方说法是 6 个月无活动），删前会发邮件。

### 6. 放进环境变量

```bash
GOOGLE_CLIENT_ID=1234567890-xxxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxxxxxxx
GOOGLE_REDIRECT_URI=https://example.com/api/auth/callback/google
```

---

## 三、Google：流程里每一步的请求

### 第 1 步：跳到授权地址

```text
https://accounts.google.com/o/oauth2/v2/auth
  ?client_id=GOOGLE_CLIENT_ID
  &redirect_uri=https://example.com/api/auth/callback/google
  &response_type=code
  &scope=openid%20email%20profile
  &state=随机串
  &nonce=随机串
  &code_challenge=BASE64URL(SHA256(code_verifier))
  &code_challenge_method=S256
  &prompt=select_account
```

| 参数 | 说明 |
| --- | --- |
| `response_type=code` | 授权码模式，固定 |
| `scope` | 空格分隔，URL 里编码成 `%20` |
| `state` | 随机串，同时写进 cookie，回来时比对 |
| `nonce` | 随机串，会原样出现在 `id_token` 里，防重放 |
| `code_challenge` | PKCE，见第五节代码 |
| `prompt=select_account` | 每次让用户选账号。不加的话浏览器只登了一个 Google 账号时会直接跳回 |
| `access_type=offline` | 只有要长期替用户调 Google API 时才加，会多给一个 `refresh_token`。单纯登录不要加 |
| `login_hint` | 可选，预填邮箱 |
| `hd` | 可选，只允许某个 Workspace 域名的账号（服务器上仍要校验） |

### 第 2 步：Google 回调你

```text
https://example.com/api/auth/callback/google?state=...&code=4/0Axxxx&scope=...
```

用户点了取消会是 `?error=access_denied&state=...`，要处理，跳回登录页就行。授权码只能用一次，几分钟内过期。

### 第 3 步：服务器拿 code 换 token

```bash
curl -X POST https://oauth2.googleapis.com/token \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d client_id=$GOOGLE_CLIENT_ID \
  -d client_secret=$GOOGLE_CLIENT_SECRET \
  -d code=4/0Axxxx \
  -d code_verifier=xxxx \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example.com/api/auth/callback/google
```

返回：

```json
{
  "access_token": "ya29.xxx",
  "expires_in": 3599,
  "scope": "openid https://www.googleapis.com/auth/userinfo.email https://www.googleapis.com/auth/userinfo.profile",
  "token_type": "Bearer",
  "id_token": "eyJhbGciOiJSUzI1NiIs..."
}
```

这里的 `redirect_uri` 必须和第 1 步的一字不差。

### 第 4 步：从 id_token 拿用户

`id_token` 是 JWT，中间一段 base64url 解出来：

```json
{
  "iss": "https://accounts.google.com",
  "aud": "1234567890-xxxx.apps.googleusercontent.com",
  "sub": "110169484474386276334",
  "email": "someone@gmail.com",
  "email_verified": true,
  "name": "Some One",
  "picture": "https://lh3.googleusercontent.com/a/xxx",
  "nonce": "第 1 步的 nonce",
  "iat": 1762070000,
  "exp": 1762073600
}
```

要校验的：

- `iss` 是 `https://accounts.google.com` 或 `accounts.google.com`
- `aud` 等于你的 Client ID
- `exp` 没过期
- `nonce` 等于 cookie 里存的
- 用 `email` 前确认 `email_verified === true`

`id_token` 是你的服务器直接通过 HTTPS 从 Google 拿到的，按 Google 文档可以不验签；想更稳就用 `jose` 拿 `https://www.googleapis.com/oauth2/v3/certs` 验签。也可以不解 JWT，直接带 `access_token` 请求 userinfo：

```bash
curl -s https://openidconnect.googleapis.com/v1/userinfo \
  -H "Authorization: Bearer ya29.xxx"
```

### 另一种接法：Google 官方按钮 / One Tap

前端引 `https://accounts.google.com/gsi/client`，用户点完直接在前端拿到 `id_token`（字段叫 `credential`），POST 给服务器验签。好处是有 One Tap 弹窗、转化高；代价是必须在服务器上验签（这个 token 经过了浏览器），而且要配 Authorized JavaScript origins。Chrome 上已经走 FedCM。先把上面的跳转式做好，One Tap 当作加分项。

---

## 四、GitHub：建应用和流程

### 1. OAuth App 还是 GitHub App

GitHub 有两种应用：

| | OAuth App | GitHub App |
| --- | --- | --- |
| 适合 | **只做登录** | 要读写用户的仓库、装到组织上 |
| 权限 | scope，粗粒度 | 细粒度权限 + 安装 |
| token | `gho_` 开头，**不过期**，直到用户撤销 | `ghu_` 开头，8 小时过期，带 `refresh_token` |
| 回调地址 | 1 个 | 最多 10 个 |

只做「用 GitHub 登录」，选 **OAuth App**，最简单。

### 2. 建 OAuth App

个人账号：<https://github.com/settings/developers> → OAuth Apps → **New OAuth App**（直达 <https://github.com/settings/applications/new>）

组织名下：`https://github.com/organizations/<组织名>/settings/applications`。建议放组织下，换人不丢。

| 字段 | 填什么 |
| --- | --- |
| Application name | 产品名，授权页上显示 |
| Homepage URL | `https://example.com` |
| Application description | 一句话，可选 |
| Authorization callback URL | `https://example.com/api/auth/callback/github` |
| Enable Device Flow | 不勾，CLI 工具才用 |

建好后页面上有 **Client ID**，点 **Generate a new client secret** 生成密钥——同样只显示一次。可以顺手传个 logo，GitHub 不审。

**回调地址只能填一个**，所以 dev 和线上建**两个 OAuth App**：`Example`（线上）和 `Example Dev`（回调填 `http://localhost:4321/api/auth/callback/github`）。

请求里的 `redirect_uri` 规则：host 和端口必须和后台填的一致，路径可以是它的子路径。后台填 `https://example.com/api/auth/callback/github`，那么 `https://example.com/api/auth/callback/github/xxx` 也行，`https://www.example.com/...` 不行。本地用 `127.0.0.1` 这类回环地址时端口可以不同。

```bash
GITHUB_CLIENT_ID=Ov23lixxxxxxxx
GITHUB_CLIENT_SECRET=xxxxxxxxxxxxxxxxxxxxxxxx
GITHUB_REDIRECT_URI=https://example.com/api/auth/callback/github
```

### 3. 第 1 步：跳到授权地址

```text
https://github.com/login/oauth/authorize
  ?client_id=GITHUB_CLIENT_ID
  &redirect_uri=https://example.com/api/auth/callback/github
  &scope=read:user%20user:email
  &state=随机串
  &code_challenge=BASE64URL(SHA256(code_verifier))
  &code_challenge_method=S256
```

| 参数 | 说明 |
| --- | --- |
| `scope` | `read:user` 读资料，`user:email` 读邮箱（包括设为私密的）。不填 scope 也能拿公开资料，但私密邮箱拿不到 |
| `state` | 同 Google |
| `code_challenge` | 2025 年 7 月起支持，可选但建议带，只支持 `S256` |
| `allow_signup` | 默认 `true`，授权页上允许没 GitHub 账号的人注册 |
| `prompt=select_account` | 让用户选账号（GitHub 支持多账号切换） |
| `login` | 可选，预填用户名 |

用户之前授权过、scope 没变的，GitHub 会直接跳回，不再显示授权页。

### 4. 第 2 步：GitHub 回调你

```text
https://example.com/api/auth/callback/github?code=xxxx&state=...
```

取消授权是 `?error=access_denied&error_description=...&state=...`。授权码 **10 分钟**过期，只能用一次。

### 5. 第 3 步：换 token

```bash
curl -X POST https://github.com/login/oauth/access_token \
  -H 'Accept: application/json' \
  -d client_id=$GITHUB_CLIENT_ID \
  -d client_secret=$GITHUB_CLIENT_SECRET \
  -d code=xxxx \
  -d code_verifier=xxxx \
  -d redirect_uri=https://example.com/api/auth/callback/github
```

返回：

```json
{ "access_token": "gho_xxx", "token_type": "bearer", "scope": "read:user,user:email" }
```

两个坑：

- **一定要带 `Accept: application/json`**，不带默认返回 `access_token=gho_xxx&scope=...&token_type=bearer` 这种表单格式。
- **出错时 HTTP 状态码也是 200**，body 里是 `{"error":"bad_verification_code", ...}`。不能只看状态码，要看有没有 `access_token`。

### 6. 第 4 步：拿用户和邮箱

```bash
curl -s https://api.github.com/user \
  -H "Authorization: Bearer gho_xxx" \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  -H "User-Agent: example-app"
```

```json
{
  "login": "octocat",
  "id": 583231,
  "avatar_url": "https://avatars.githubusercontent.com/u/583231?v=4",
  "name": "The Octocat",
  "email": null
}
```

`email` 经常是 `null`（用户把邮箱设成了私密），再查一次：

```bash
curl -s https://api.github.com/user/emails \
  -H "Authorization: Bearer gho_xxx" \
  -H "Accept: application/vnd.github+json" \
  -H "User-Agent: example-app"
```

```json
[
  { "email": "octocat@github.com", "primary": true, "verified": true, "visibility": "private" },
  { "email": "583231+octocat@users.noreply.github.com", "primary": false, "verified": true, "visibility": null }
]
```

取 `primary && verified` 的那条。`users.noreply.github.com` 是 GitHub 的转发占位邮箱，发不了营销邮件，别当真实邮箱用。

GitHub API 请求**必须带 `User-Agent`**，不带直接 403。

---

## 五、代码

用 Web 标准的 `Request` / `Response` 写，Astro 的 API 路由、Next.js 的 Route Handler、Hono、Cloudflare Workers 都能直接用。没用任何第三方库，方便看清每一步。

### 工具函数

```ts
// src/lib/oauth.ts
const enc = new TextEncoder();

function b64url(bytes: ArrayBuffer | Uint8Array) {
  const s = btoa(String.fromCharCode(...new Uint8Array(bytes)));
  return s.replace(/\+/g, '-').replace(/\//g, '_').replace(/=+$/, '');
}

export function randomString(len = 32) {
  return b64url(crypto.getRandomValues(new Uint8Array(len)));
}

export async function pkceChallenge(verifier: string) {
  return b64url(await crypto.subtle.digest('SHA-256', enc.encode(verifier)));
}

// 存 state / verifier 的临时 cookie：10 分钟，HttpOnly，SameSite=Lax
export function tempCookie(name: string, value: string) {
  return `${name}=${value}; Path=/; HttpOnly; Secure; SameSite=Lax; Max-Age=600`;
}

export function readCookie(req: Request, name: string) {
  const m = req.headers.get('cookie')?.match(new RegExp(`(?:^|; )${name}=([^;]*)`));
  return m?.[1];
}
```

`SameSite` 必须是 `Lax`，不能是 `Strict`：从 accounts.google.com / github.com 跳回来是跨站导航，`Strict` 的 cookie 不会带上，state 永远对不上。本地 `http://localhost` 调试时 Chrome 允许 `Secure`，其他浏览器不行的话 dev 环境去掉 `Secure`。

### 发起登录

```ts
// src/pages/api/auth/login/[provider].ts
import { randomString, pkceChallenge, tempCookie } from '../../../../lib/oauth';

const providers = {
  google: {
    authorize: 'https://accounts.google.com/o/oauth2/v2/auth',
    clientId: import.meta.env.GOOGLE_CLIENT_ID,
    redirectUri: import.meta.env.GOOGLE_REDIRECT_URI,
    scope: 'openid email profile',
  },
  github: {
    authorize: 'https://github.com/login/oauth/authorize',
    clientId: import.meta.env.GITHUB_CLIENT_ID,
    redirectUri: import.meta.env.GITHUB_REDIRECT_URI,
    scope: 'read:user user:email',
  },
} as const;

export async function GET({ params }: { params: { provider: string } }) {
  const p = providers[params.provider as keyof typeof providers];
  if (!p) return new Response('Not found', { status: 404 });

  const state = randomString();
  const verifier = randomString(48);
  const nonce = randomString();

  const url = new URL(p.authorize);
  url.search = new URLSearchParams({
    client_id: p.clientId,
    redirect_uri: p.redirectUri,
    response_type: 'code',
    scope: p.scope,
    state,
    code_challenge: await pkceChallenge(verifier),
    code_challenge_method: 'S256',
    prompt: 'select_account',
    ...(params.provider === 'google' ? { nonce } : {}),
  }).toString();

  const headers = new Headers({ Location: url.toString() });
  headers.append('Set-Cookie', tempCookie('oauth_state', state));
  headers.append('Set-Cookie', tempCookie('oauth_verifier', verifier));
  headers.append('Set-Cookie', tempCookie('oauth_nonce', nonce));
  return new Response(null, { status: 302, headers });
}
```

前端按钮就是两个普通链接：

```html
<a href="/api/auth/login/google">Continue with Google</a>
<a href="/api/auth/login/github">Continue with GitHub</a>
```

### 回调：Google

```ts
// src/pages/api/auth/callback/google.ts
import { readCookie } from '../../../../lib/oauth';
import { upsertUser, createSession } from '../../../../lib/auth';

export async function GET({ request }: { request: Request }) {
  const url = new URL(request.url);
  const code = url.searchParams.get('code');
  const state = url.searchParams.get('state');
  if (url.searchParams.get('error') || !code) return Response.redirect(new URL('/login', url), 302);
  if (!state || state !== readCookie(request, 'oauth_state')) {
    return new Response('Invalid state', { status: 400 });
  }

  const res = await fetch('https://oauth2.googleapis.com/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      client_id: import.meta.env.GOOGLE_CLIENT_ID,
      client_secret: import.meta.env.GOOGLE_CLIENT_SECRET,
      code,
      code_verifier: readCookie(request, 'oauth_verifier') ?? '',
      grant_type: 'authorization_code',
      redirect_uri: import.meta.env.GOOGLE_REDIRECT_URI,
    }),
  });
  const token = await res.json();
  if (!token.id_token) return new Response(`Token error: ${token.error}`, { status: 400 });

  // 直接从 Google 的 token 接口拿到，可以不验签，但声明要逐项检查
  const claims = JSON.parse(atob(token.id_token.split('.')[1].replace(/-/g, '+').replace(/_/g, '/')));
  const ok =
    ['https://accounts.google.com', 'accounts.google.com'].includes(claims.iss) &&
    claims.aud === import.meta.env.GOOGLE_CLIENT_ID &&
    claims.exp * 1000 > Date.now() &&
    claims.nonce === readCookie(request, 'oauth_nonce');
  if (!ok) return new Response('Invalid id_token', { status: 400 });

  const user = await upsertUser({
    provider: 'google',
    providerUserId: claims.sub,
    email: claims.email_verified ? claims.email : null,
    name: claims.name,
    avatar: claims.picture,
  });
  return createSession(user, new URL('/dashboard', url));
}
```

### 回调：GitHub

```ts
// src/pages/api/auth/callback/github.ts
import { readCookie } from '../../../../lib/oauth';
import { upsertUser, createSession } from '../../../../lib/auth';

const gh = (path: string, token: string) =>
  fetch(`https://api.github.com${path}`, {
    headers: {
      Authorization: `Bearer ${token}`,
      Accept: 'application/vnd.github+json',
      'X-GitHub-Api-Version': '2022-11-28',
      'User-Agent': 'example-app',
    },
  }).then((r) => r.json());

export async function GET({ request }: { request: Request }) {
  const url = new URL(request.url);
  const code = url.searchParams.get('code');
  const state = url.searchParams.get('state');
  if (url.searchParams.get('error') || !code) return Response.redirect(new URL('/login', url), 302);
  if (!state || state !== readCookie(request, 'oauth_state')) {
    return new Response('Invalid state', { status: 400 });
  }

  const res = await fetch('https://github.com/login/oauth/access_token', {
    method: 'POST',
    headers: { Accept: 'application/json', 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      client_id: import.meta.env.GITHUB_CLIENT_ID,
      client_secret: import.meta.env.GITHUB_CLIENT_SECRET,
      code,
      code_verifier: readCookie(request, 'oauth_verifier') ?? '',
      redirect_uri: import.meta.env.GITHUB_REDIRECT_URI,
    }),
  });
  const token = await res.json();
  // GitHub 出错也返回 200，看字段
  if (!token.access_token) return new Response(`Token error: ${token.error}`, { status: 400 });

  const profile = await gh('/user', token.access_token);
  const emails: { email: string; primary: boolean; verified: boolean }[] = await gh('/user/emails', token.access_token);
  const primary = emails.find((e) => e.primary && e.verified)?.email ?? null;

  const user = await upsertUser({
    provider: 'github',
    providerUserId: String(profile.id),
    email: primary,
    name: profile.name ?? profile.login,
    avatar: profile.avatar_url,
  });
  return createSession(user, new URL('/dashboard', url));
}
```

### 表结构

一个用户可以同时绑 Google 和 GitHub，所以账号单独一张表：

```sql
CREATE TABLE users (
  id          TEXT PRIMARY KEY,
  email       TEXT UNIQUE,          -- 可以为空
  name        TEXT,
  avatar      TEXT,
  created_at  TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE oauth_accounts (
  provider          TEXT NOT NULL,  -- 'google' | 'github'
  provider_user_id  TEXT NOT NULL,  -- Google 的 sub，GitHub 的 id
  user_id           TEXT NOT NULL REFERENCES users(id),
  created_at        TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (provider, provider_user_id)
);

CREATE TABLE sessions (
  id          TEXT PRIMARY KEY,     -- 随机 token 的 SHA-256
  user_id     TEXT NOT NULL REFERENCES users(id),
  expires_at  TIMESTAMP NOT NULL
);
```

`upsertUser` 的逻辑：

1. 按 `(provider, provider_user_id)` 找，找到就是老用户
2. 找不到，且邮箱是**已验证**的，按邮箱找 `users`：找到就把这个第三方账号绑上去（同一个人先用 Google 后用 GitHub 登录，会合并成一个账号）
3. 都没有就新建用户

第 2 步只能用已验证邮箱，否则别人在 GitHub 上填一个你的邮箱就能登进你的账号。保守一点的做法是不自动合并，让用户登录后在设置页手动绑定。

`createSession` 生成随机 token，库里存它的哈希，写 `session` cookie（`HttpOnly; Secure; SameSite=Lax`，30 天），同时把三个 `oauth_*` 临时 cookie 用 `Max-Age=0` 清掉。

### 不想自己写

| 库 | 说明 |
| --- | --- |
| [Better Auth](https://www.better-auth.com) | 框架无关，TypeScript，自带表结构、账号合并、session，目前独立开发者用得最多 |
| [Auth.js](https://authjs.dev) | 原 NextAuth，Next.js 生态 |
| [Arctic](https://arcticjs.dev) | 只封装 OAuth 那几步（拼地址、换 token），session 自己做，适合想看懂流程又不想手写的 |

Lucia 2025 年起不再作为库维护，改成了教程站 <https://lucia-auth.com>，讲的就是上面这套手写流程。

用库的话，回调地址填库要求的路径，比如 Better Auth 是 `/api/auth/callback/google`、`/api/auth/callback/github`，和本文一致。

---

## 六、常见报错

| 报错 | 哪家 | 原因 | 怎么办 |
| --- | --- | --- | --- |
| `Error 400: redirect_uri_mismatch` | Google | 请求里的 `redirect_uri` 和后台不完全一致 | 报错页点「错误详情」能看到实际发出的地址，对比协议、www、端口、末尾斜杠；刚改完等几分钟 |
| `Error 401: invalid_client` | Google | Client ID 写错，或客户端被删 | 核对环境变量；看客户端是否还在 |
| `Error 403: access_denied`（应用还在测试） | Google | 发布状态是 Testing，登录账号不在测试用户里 | 加测试用户，或在 Audience 页发布 |
| `Error 403: org_internal` | Google | User type 选了 Internal | 改成 External |
| 「Google 未验证此应用」 | Google | 用了敏感 scope，或传了 logo 没验证 | 登录只留三个基础 scope；去掉 logo 或做品牌验证 |
| `redirect_uri_mismatch`（授权页上提示） | GitHub | `redirect_uri` 不是后台回调地址或其子路径 | dev / 线上用两个 OAuth App |
| `bad_verification_code` | GitHub | code 过期（10 分钟）或已经用过 | 回调里不要重复换；检查有没有被预取 / 刷新 |
| `incorrect_client_credentials` | GitHub | secret 错了，或用了另一个 App 的 secret | 核对 dev / prod 两套变量 |
| `invalid_grant` | Google | code 过期 / 重复使用，或 `code_verifier` 对不上 | 同上；确认 verifier cookie 带回来了 |
| 回调时 state 不一致 | 两家 | cookie 用了 `SameSite=Strict`；或发起和回调不是同一个域名（`www` 与裸域） | 改成 `Lax`；统一域名，裸域 301 到一个 |
| GitHub API 403 | GitHub | 没带 `User-Agent` | 加上 |
| 用户邮箱是 `null` | GitHub | 邮箱设为私密，且没要 `user:email` | 加 scope，查 `/user/emails` |

---

## 七、上线前检查

```bash
# 发起登录应该 302 到授权地址，并带上三个临时 cookie
curl -sI https://example.com/api/auth/login/google | grep -iE '^(HTTP|location|set-cookie)'
curl -sI https://example.com/api/auth/login/github | grep -iE '^(HTTP|location|set-cookie)'

# 授权地址里的 redirect_uri 必须是线上域名，不能是 localhost
curl -sI https://example.com/api/auth/login/google | grep -i location | grep -o 'redirect_uri=[^&]*'

# 不带 state 直接打回调，应该是 400
curl -s -o /dev/null -w '%{http_code}\n' 'https://example.com/api/auth/callback/github?code=fake'
```

清单：

- [ ] Google：Audience 已经 **Publish**，状态是 In production
- [ ] Google：Branding 里的首页、隐私政策、服务条款链接能打开
- [ ] Google：线上客户端的回调地址是 `https://` + 正式域名
- [ ] GitHub：线上和 dev 是两个 OAuth App，线上那个回调是正式域名
- [ ] 两个 secret 只在服务端环境变量里，没进 git，没进前端打包产物（`grep -r GOCSPX dist/` 查一下）
- [ ] 换个没登过的 Google 账号、一个邮箱设为私密的 GitHub 账号各走一遍
- [ ] 同一邮箱先 Google 后 GitHub 登录，确认合并逻辑符合预期
- [ ] 用户点「取消」能回到登录页，不是 500
- [ ] 隐私政策里写明从 Google / GitHub 拿了哪些字段（姓名、邮箱、头像）
- [ ] 有删除账号的入口（上架 iOS 必须有，见 [iOS 实操](../growth/iOS个人账号付费与提交App.md)）；iOS App 里提供了 Google / GitHub 登录的，按审核指南 4.8 还要同时提供 Sign in with Apple 或同等隐私的登录方式

---

## 参考

- Google：[Using OAuth 2.0 for Web Server Applications](https://developers.google.com/identity/protocols/oauth2/web-server)、[OpenID Connect](https://developers.google.com/identity/openid-connect/openid-connect)、[Manage OAuth Clients](https://support.google.com/cloud/answer/15549257)、[Usability and safety updates to Google Auth Platform](https://developers.googleblog.com/usability-and-safety-updates-to-google-auth-platform/)
- GitHub：[Authorizing OAuth apps](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)、[Differences between GitHub Apps and OAuth apps](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/differences-between-github-apps-and-oauth-apps)、[PKCE support for OAuth and GitHub App authentication（2025-07-14）](https://github.blog/changelog/2025-07-14-pkce-support-for-oauth-and-github-app-authentication/)
- [RFC 6749 OAuth 2.0](https://www.rfc-editor.org/rfc/rfc6749)、[RFC 7636 PKCE](https://www.rfc-editor.org/rfc/rfc7636)
