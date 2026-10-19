# GSC 与 Bing 的接入操作

这篇只讲后台怎么点：Google Search Console 和 Bing Webmaster Tools 从零接入、验证站点、提交 sitemap、请求收录、接 IndexNow、上线后每天查什么。

sitemap 和 robots.txt 本身怎么写，见 [SEO 落地](SEO落地.md) 第八节。Bing 为什么值得接、为什么不收，见 [Bing 的收录](Bing的收录.md)。数据怎么看，见 [SEO 的监测](SEO的监测.md) 和 [新站的 SEO 监测](新站的SEO监测.md)。

---

## 零、开始前先确认

后台操作之前，先用 `curl` 把这几样核对一遍。这一步没过，后面提交什么都没用。

```bash
# 首页返回 200，不是 301 链或 403
curl -sI https://example.com/ | head -1

# robots.txt 能打开，没有整站 Disallow，写了 Sitemap
curl -s https://example.com/robots.txt

# sitemap 返回 200，Content-Type 是 xml
curl -sI https://example.com/sitemap-index.xml | grep -iE '^HTTP|content-type'

# sitemap 里的 URL 抽查几条，都应该是 200
curl -s https://example.com/sitemap-0.xml | grep -o '<loc>[^<]*' | sed 's/<loc>//' | head -5 | xargs -n1 curl -so /dev/null -w '%{http_code} %{url_effective}\n'

# 页面没有误加 noindex
curl -s https://example.com/ | grep -i 'name="robots"'
```

常见的坑：

- Cloudflare 的 Bot Fight Mode、WAF 规则把 Googlebot / Bingbot 挡了，浏览器打开正常，爬虫拿到 403
- 预览环境的 `noindex` 或 `X-Robots-Tag: noindex` 带到了正式环境
- sitemap 里写的是 `http://` 或带 `www` 的地址，和实际 canonical 不一致
- robots.txt 里 `Sitemap:` 写的文件还没上线，404

---

## 一、Google Search Console

入口：<https://search.google.com/search-console>

### 1. 添加资源：选网域

左上角资源下拉 → 「添加资源」，有两种：

| | 网域（Domain） | 网址前缀（URL prefix） |
| --- | --- | --- |
| 填什么 | `example.com`，不带协议、不带 www | `https://www.example.com/`，完整前缀 |
| 覆盖范围 | http、https、www、所有子域 | 只有这个前缀开头的 URL |
| 验证方式 | 只能 DNS | HTML 文件、HTML 标签、GA、GTM、DNS |
| 新加子域 | 自动包含 | 要另建资源 |

**默认用网域资源**。品牌词过滤、抓取统计这些功能只在根级资源上有。改不了 DNS 的时候（比如托管平台不给权限）才用网址前缀。

两种可以同时建：一个网域资源看全局，再给 `/blog/` 之类的目录建网址前缀资源单独看。

### 2. 验证

**网域资源（DNS TXT）**：

1. GSC 给一串 `google-site-verification=xxxx`，复制
2. 到域名的 DNS 服务商（Cloudflare：DNS → Records → Add record）加一条：类型 `TXT`，名称 `@`，内容粘贴那串
3. 回 GSC 点「验证」。一般几分钟到几小时，不行就过一会儿再点

```bash
# 确认 TXT 记录已经生效
dig +short TXT example.com
```

**网址前缀资源（HTML 标签）**：在首页 `<head>` 里加 GSC 给的 meta，部署后点验证。

```html
<meta name="google-site-verification" content="xxxx" />
```

验证用的 TXT 记录、meta 标签、HTML 文件**不要删**。GSC 会定期复查，删了会掉验证。

### 3. 提交 sitemap

左侧「索引」→「站点地图」：

1. 先在顶部的网址检查框里输入 sitemap 地址，点「测试实际网址」，确认「允许抓取：是」「网页抓取：成功」
2. 回到站点地图页，在「添加新的站点地图」里填 sitemap 地址。网域资源要填完整 URL，网址前缀资源只填路径（如 `sitemap-index.xml`）
3. 点「提交」

**用 sitemap index 的话，只提交 index 一个就行**，下面的子 sitemap GSC 会自己读出来，点开 index 能看到每个子文件的状态。

提交以后的状态：

| 状态 | 意思 | 怎么办 |
| --- | --- | --- |
| 成功 | 读到了，没有错误 | 点进去看「已发现的网页」数量对不对 |
| 无法获取（Couldn't fetch） | 没拿到文件 | 见下面 |
| 有错误 | 读到了，部分条目有问题 | 点进去看具体是哪几行 |

「无法获取」很常见，新站提交后几天都是这个状态也不稀奇，不一定是坏了。排查顺序：

1. 网址检查里对 sitemap 地址做「测试实际网址」，看是不是 200
2. 查服务器 / Cloudflare 日志，Googlebot 来的请求有没有 403、超时
3. 确认 sitemap 不在登录墙后面、没有被重定向
4. 都正常的话，等几天。Google 拿不到会自己重试几天
5. 还不行，John Mueller 给过的办法是换个文件名重新提交

几件已经不用做的事：

- **ping 地址已废弃**。`google.com/ping?sitemap=` 2023 年底停用，现在返回 404。老插件还在 ping 不会出事，只是没用
- **`priority`、`changefreq` 不用调**，Google 不看。`lastmod` 要写真实的修改时间，乱写 Google 会不再信它
- **不用每次更新都重新提交**。Google 会定期回来读，更新了 `lastmod` 就够了

GSC 的站点地图列表只显示通过 GSC 或 API 提交的，robots.txt 里写的不会出现在列表里，但 Google 一样会读。两个都做。

### 4. 网址检查和请求编入索引

顶部搜索框输入完整 URL，回车：

- **「网址已收录到 Google」**：没事
- **「网址未收录到 Google」**：往下看「网页索引编制」里的原因，比如「已发现 - 尚未编入索引」「已抓取 - 尚未编入索引」「被 noindex 排除」
- 右上角「测试实际网址」：用 Googlebot 现在去抓一次，看渲染后的 HTML 和截图。改完页面先测这个
- 「请求编入索引」：把这个 URL 放进优先抓取队列

请求编入索引**没有公布的配额**，社区经验大约每个资源每天 10–15 次，超了会提示「已超出配额」。所以只给这些页用：

- 上线当天的首页和几个核心页
- 改了 title、修了 noindex 等重要问题的页
- 新词站的主落地页

大量页面靠 sitemap 和内链，别一条条点。重复点同一个 URL 不会更快。

### 5. 关联 GA4、加用户、加注释

- **关联 GA4**：GA4 → 管理 → 产品关联 → Search Console 关联。两三天后 GA4 里会有「查询」和「Google 自然搜索流量」报表
- **加用户**：设置 → 用户和权限 → 添加用户。给合作者「完整」权限就够；「所有者」能加删用户、提交 sitemap，只给自己和服务账号
- **加注释**：效果报告图上右键「添加注释」，改版、改 robots、上新模块都记一笔

### 6. 用 API 自动化

GSC API 能提交 sitemap、查收录状态，适合放进部署流程或每天的定时任务。

准备：

1. Google Cloud 建项目，启用 Google Search Console API
2. 建服务账号，下载 JSON key
3. 在 GSC「用户和权限」里把服务账号邮箱加为用户。**提交 sitemap 需要「所有者」权限**，只查数据给「完整」就行

```python
# pip install google-api-python-client google-auth
from google.oauth2 import service_account
from googleapiclient.discovery import build

creds = service_account.Credentials.from_service_account_file(
    "sa.json", scopes=["https://www.googleapis.com/auth/webmasters"]
)
gsc = build("searchconsole", "v1", credentials=creds)

SITE = "sc-domain:example.com"  # 网域资源这么写；网址前缀资源写 "https://www.example.com/"

# 提交 sitemap
gsc.sitemaps().submit(siteUrl=SITE, feedpath="https://example.com/sitemap-index.xml").execute()

# 看 sitemap 状态
for s in gsc.sitemaps().list(siteUrl=SITE).execute().get("sitemap", []):
    print(s["path"], s.get("lastDownloaded"), s.get("errors"), s.get("warnings"))

# 查单个 URL 的收录状态
r = gsc.urlInspection().index().inspect(
    body={"inspectionUrl": "https://example.com/blog/hello", "siteUrl": SITE}
).execute()
idx = r["inspectionResult"]["indexStatusResult"]
print(idx.get("verdict"), idx.get("coverageState"), idx.get("lastCrawlTime"))
```

URL Inspection API 的限额：每个资源每天 2,000 次、每分钟 600 次。它**只查状态，不会请求收录**。网上说的「用 API 批量请求收录」指的是 Indexing API，官方只允许招聘和直播页面用，别的页面用了不保证有效。

每天用 URL Inspection API 把 sitemap 里的 URL 全查一遍、算收录率，做法见 [新站的 SEO 监测](新站的SEO监测.md)。

---

## 二、Bing Webmaster Tools

入口：<https://www.bing.com/webmasters>，用微软、Google 或 Facebook 账号登录都行。

### 1. 添加站点：从 GSC 导入

最省事的是导入：

1. 登录后在 My Sites 页点「Import」（已有站点的话，左侧「Add a site」里也有）
2. 用**建 GSC 资源的那个 Google 账号**登录，点「Allow」
3. 列表里会显示 GSC 里已验证的站点、sitemap 数量和你的权限，勾选要导入的，点「Import」

站点、用户权限、sitemap 一起带过来，不用重新验证。

导入后**一定去 Sitemaps 页看一眼**。有人遇到过站点导进来了、sitemap 没带过来，要手动补。

不导入的话，「Add a site」手动填 URL，四种验证方式：

| 方式 | 怎么做 |
| --- | --- |
| XML 文件 | 下载 `BingSiteAuth.xml`，放到网站根目录 |
| Meta 标签 | `<meta name="msvalidate.01" content="xxxx" />` 放进首页 `<head>` |
| CNAME | DNS 加一条 Bing 给的 CNAME 记录 |
| DNS 自动验证 | 只有支持 Domain Connect 的服务商能用 |

### 2. 提交 sitemap

左侧「Sitemaps」（新界面里可能在 Configure My Site 下面）→「Submit sitemap」→ 填完整 URL → 提交。

和 Google 一样，有 sitemap index 就只交 index。Bing 新提交的 sitemap 会立刻抓，之后每天复查。

Bing 对 `lastmod` 比 Google 更看重，官方 2025 年说它是决定重抓还是跳过的「关键信号」。要求：

- 完整 ISO 8601 时间，如 `2026-10-19T08:00:00+00:00`
- 页面没改就别动 `lastmod`，别所有 URL 都填 sitemap 生成时间
- `priority`、`changefreq` 同样不看

提交后点进 sitemap 能看到每个 URL 的状态：已收录、排除、未抓取、被屏蔽、重定向、低质量。这是排查 Bing 不收录最直接的地方。

### 3. 接 IndexNow

Bing 现在把 IndexNow 列为「强烈推荐」的提交方式。推一次，Yandex、Seznam、Naver 等参与的搜索引擎也会收到。Google 不支持。

**第一步：放 key 文件**

Bing Webmaster Tools 左侧「IndexNow」里可以生成 key，也可以自己生成（8–128 位，字母、数字、短横线）：

```bash
KEY=$(openssl rand -hex 16)
echo -n "$KEY" > public/$KEY.txt   # 放到网站根目录，部署后能访问
curl -s https://example.com/$KEY.txt
```

**第二步：部署时推送变更的 URL**

```bash
curl -X POST "https://api.indexnow.org/indexnow" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{
    "host": "example.com",
    "key": "'"$KEY"'",
    "keyLocation": "https://example.com/'"$KEY"'.txt",
    "urlList": ["https://example.com/blog/new-post", "https://example.com/"]
  }'
```

返回 200 或 202 就是收到了。403 是 key 文件打不开或内容不对，422 是 URL 不属于这个 host，429 是推太频繁。

只推本次新增、修改、删除的 URL，别每次部署把整个 sitemap 推一遍。

**不想写代码的办法**：

- Cloudflare：Caching → Configuration → 打开 Crawler Hints，Cloudflare 会根据缓存变化自动发 IndexNow。免费版就有，域名要开橙色云代理
- WordPress：Rank Math、Yoast、AIOSEO 都内置；或者装微软的 IndexNow 插件
- Astro / Next.js：有现成的集成包，或者在 CI 里 diff 出变更的页面再 POST

**第三步：看效果**：Bing Webmaster Tools 左侧「IndexNow」能看到收到了多少 URL、哪些有问题（IndexNow Insights）。

### 4. URL Inspection 和手动提交

- **URL Inspection**：输入 URL 看收录状态，「Live URL」测 Bingbot 现在能不能抓到。状态是「Discovered but not crawled」「URL cannot appear on Bing」时怎么办，见 [Bing 的收录](Bing的收录.md) 第四节
- **URL Submission**：左侧「URL Submission」手动提交，每天额度在页面上能看到，按站点给，很多新站远低于 1 万条。官方说这个功能「可能弃用」，首页和核心页交一下就行，日常交给 IndexNow

### 5. API

右上角「Settings」→「API access」生成 API key。key 跟着账号走，一个 key 能管你验证过的所有站。常用的几个接口（以 [Bing Webmaster API 文档](https://learn.microsoft.com/en-us/dotnet/api/microsoft.bing.webmaster.api.interfaces.iwebmasterapi) 为准）：

```bash
API=https://ssl.bing.com/webmaster/api.svc/json
K=你的APIKEY

# 提交 sitemap
curl -X POST "$API/SubmitFeed?apikey=$K" -H "Content-Type: application/json; charset=utf-8" \
  -d '{"siteUrl":"https://example.com/","feedUrl":"https://example.com/sitemap-index.xml"}'

# 查当天还剩多少 URL 提交额度
curl "$API/GetUrlSubmissionQuota?siteUrl=https://example.com/&apikey=$K"

# 批量提交 URL（每批最多 500 条，占用上面的额度）
curl -X POST "$API/SubmitUrlBatch?apikey=$K" -H "Content-Type: application/json; charset=utf-8" \
  -d '{"siteUrl":"https://example.com/","urlList":["https://example.com/a","https://example.com/b"]}'
```

Content-Type 不带 `charset=utf-8` 会被拒。Bing 的 URL Submission 帮助页现在写着要 OAuth 2.0，但参考文档和示例还是 `?apikey=`，两种都试一下。

日常推送用 IndexNow 就够了，API 主要用来查额度和自动提交 sitemap。

---

## 三、两边对照

| 操作 | Google Search Console | Bing Webmaster Tools |
| --- | --- | --- |
| 推荐资源类型 | 网域资源，DNS 验证 | 从 GSC 导入 |
| 提交 sitemap | 索引 → 站点地图 | Sitemaps |
| robots.txt 里的 `Sitemap:` | 读，但不显示在列表里 | 读 |
| ping | 已废弃 | 已废弃 |
| `lastmod` | 准确就用 | 重抓的关键信号 |
| 单页检查 | 网址检查 → 测试实际网址 | URL Inspection → Live URL |
| 单页请求收录 | 请求编入索引，约每天 10–15 次 | URL Submission，额度按站点给 |
| 主动推送 | 没有（Indexing API 限招聘、直播） | IndexNow |
| 查收录 API | URL Inspection API，每天 2,000 次 | URL Inspection 只在后台 |
| 数据保留 | 16 个月 | 24 个月 |
| AI 引用报告 | 效果报告里不单列 | AI Performance |

---

## 四、上线当天的清单

**部署前**

- [ ] robots.txt 写了 `Sitemap:`，没有误挡
- [ ] sitemap 只有 200、canonical、可收录的 URL，`lastmod` 是真实时间
- [ ] 页面没有遗留的 `noindex`
- [ ] Cloudflare WAF / Bot Fight Mode 没挡搜索引擎爬虫
- [ ] IndexNow key 文件放在根目录

**部署后**

- [ ] GSC：添加网域资源，DNS 验证
- [ ] GSC：网址检查测 sitemap 地址，再提交 sitemap index
- [ ] GSC：首页和核心页「测试实际网址」→「请求编入索引」
- [ ] GSC：关联 GA4
- [ ] Bing：从 GSC 导入，确认 sitemap 带过来了
- [ ] Bing：首页 URL Inspection 测 Live URL，手动提交首页和核心页
- [ ] IndexNow：推一次首页和核心页，确认返回 200 / 202
- [ ] 新词站：Yandex Webmaster 也接上，提交同一份 sitemap

**之后每次发布**

- 新页面自动进 sitemap，`lastmod` 自动更新
- 部署流程自动推 IndexNow
- 重要页面在 GSC 手动请求一次编入索引

---

## 五、上线后每天、每周看什么

| 频率 | GSC | Bing |
| --- | --- | --- |
| 上线后头三天，每天 | 站点地图状态是否变成「成功」；核心页网址检查是否已收录；`site:example.com` | Sitemaps 状态；IndexNow 有没有报错；`site:example.com` |
| 第一周，每天 | 网页索引编制报告里已编入 / 未编入的数量和原因 | Search Performance 有没有展示 |
| 之后每周 | 未编入索引的原因有没有新增；sitemap 「已发现的网页」和实际页数是否一致 | Sitemaps 里排除、低质量的 URL；Backlinks |
| 每月 | 抓取统计；生成式 AI 报告 | AI Performance |

`site:` 搜索只能当粗略参考，数量不准，确认单页收录以网址检查为准。

---

## 参考

### Google

- [Search Console 帮助：添加网站资源](https://support.google.com/webmasters/answer/34592?hl=zh-Hans)
- [Search Console 帮助：站点地图报告](https://support.google.com/webmasters/answer/7451001?hl=zh-Hans)
- [Google Search Central：构建和提交站点地图](https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap)
- [Google Search Central Blog: Sitemaps ping endpoint is going away](https://developers.google.com/search/blog/2023/06/sitemaps-lastmod-ping)（2023 年 6 月，ping 废弃和 lastmod 建议）
- [Search Console API: sitemaps.submit](https://developers.google.com/webmaster-tools/v1/sitemaps/submit)
- [Search Console API: usage limits](https://developers.google.com/webmaster-tools/limits)
- [SEOTesting: Domain vs URL prefix](https://seotesting.com/google-search-console/domain-vs-url-prefix/)
- [JC Chouinard: Sitemap could not be read / couldn't fetch](https://www.jcchouinard.com/sitemap-could-not-be-read-couldnt-fetch-in-google-search-console/)
- [Incremys: Google Search Console quota](https://www.incremys.com/en/resources/blog/google-search-console-quota)（请求编入索引配额的社区估计）

### Bing

- [Bing Webmaster Blog: Import sites from Search Console](https://blogs.bing.com/webmaster/september-2019/Import-sites-from-Search-Console-to-Bing-Webmaster-Tools)
- [Bing Webmaster Blog: Keeping content discoverable with sitemaps in AI powered search](https://blogs.bing.com/webmaster/2025/7/Keeping-Content-Discoverable-with-Sitemaps-in-AI-Powered-Search/)（2025 年 7 月）
- [IndexNow documentation](https://www.indexnow.org/documentation)
- [Bing Webmaster API: IWebmasterApi](https://learn.microsoft.com/en-us/dotnet/api/microsoft.bing.webmaster.api.interfaces.iwebmasterapi?view=bing-webmaster-dotnet)
- [Bing Webmaster Blog: Accessing Bing Webmaster Tools API using cURL](https://blogs.bing.com/webmaster/november-2019/Accessing-Bing-webmaster-tools-api-using-cURL)
- [Bing Webmaster Blog: Cloudflare supports IndexNow](https://blogs.bing.com/webmaster/november-2021/Cloudflare-Supports-IndexNow-via-one-click-Integration)
- [Rank Math: How to submit a sitemap to Bing](https://rankmath.com/kb/submit-sitemap-to-bing/)
- [GEO Toolbox: Bing Webmaster Tools setup, IndexNow and AI Performance](https://geotoolbox.ai/blog/bing-webmaster-tools)（2026 年 10 月）
- [Jetfuel: Bing Webmaster Tools setup, sitemaps & IndexNow](https://jetfuel.agency/how-to-set-up-bing-webmaster-tools-for-your-site-step-by-step-guide/)
