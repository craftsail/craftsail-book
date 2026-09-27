# SEO 落地

搜索引擎和 AI 读的都是 HTML。它们得能找到你的页面，看懂它，再把它当成一篇单独的文档收进去。

这篇按我们在 craftsail.com 上做过的顺序写：先想清楚哪些页要被收录，把每页的 `<head>` 写对，根目录放好 `robots.txt`、`sitemap.xml`、`llms.txt`，站内所有入口都用真的 `<a href>`。后面再讲框架为什么选 Astro，以及 Blog 和 Help 这两块具体怎么搭。

文中的 HTML 都能在 [craftsail.com](https://craftsail.com) 上「查看网页源代码」对上。产品站、文档站都适用。

---

## 一、哪些页该被收录

本站有自己的内容，就给它一个独立 URL；没有，就链到原文，别做薄页。

产品站一般要收录四类页：这是什么、多少钱、怎么装、怎么用。Blog 和 Help 要另外单独做。Blog 隔三差五给搜索引擎一批新页面，让它养成回来抓的习惯；Help 讲清楚用法，左边 sidebar 上挂满真实链接，爬虫进任何一篇，都能顺着摸到整棵文档树。

聚合资讯、采集来的文章不算自己的内容。别人写过的东西，再做一份「本站转载页」去抢收录，基本是白费力气，英文搜索对采集站尤其不客气。

| 页面类型 | 产品站 | 聚合 / 数据门户 | 做法 |
| --- | --- | --- | --- |
| 首页 | 品牌 + 品类 | 品牌 + 品类 | 独立 title；Organization / WebSite |
| 定价 | `/pricing` | 通常没有 | 价格写在正文，JSON-LD 和页面上一致 |
| Blog | `/blog/` feed 流 + `/blog/{slug}`，每天固定发 | 只有本站数据和观点才发 | 列表倒序分页；文章带图表，结构按类型区分；转载不算 |
| Help | `/help/{path}` + sidebar | `/about/`、来源说明、隐私 | 一篇一个 URL；sidebar 的整棵树都在源码里 |
| App | `/app/`、`/account/`、`/api/` | 同左 | `noindex`，写进 `robots.txt` |
| 导航 | 侧栏、页脚、面包屑 | 筛选控件也要是链接 | 全部 `<a href>` |

照抄别人的 Pricing、Help、Blog 结构也有坑：自己没有对应的内容，抄过来就是一批空壳页。

---

## 二、`<head>` 写给谁看

`<head>` 的读者不止一个：

| 读者 | 它看什么 | 你要准备什么 |
| --- | --- | --- |
| 浏览器 / 手机系统 | 图标、主题色、字体 | favicon、apple-touch-icon、theme-color、字体 preload |
| Google / Bing | title、description、canonical、robots、正文、链接 | 可收录的独立 URL 和准确的摘要 |
| 社交平台 | Open Graph、Twitter Card | 标题、描述、1200×630 的图 |
| 搜索和 AI | JSON-LD、FAQ、标题层级 | 实体、产品或目录、问答 |

下面是 craftsail.com 首页的 `<head>`。开无痕窗口，右键「查看网页源代码」就能对上。

```html
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<meta name="theme-color" content="#faf9f5" />
<meta name="application-name" content="造物局" />
<meta name="apple-mobile-web-app-title" content="造物局" />
<title>造物局 · CraftSail — AI 与开发者趋势数据门户</title>
<meta name="description" content="看见正在发生的变化，找到值得继续研究的人、项目与机会。" />
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1" />
<link rel="canonical" href="https://craftsail.com/" />
<link rel="icon" href="/favicon.svg?v=2" type="image/svg+xml" media="(prefers-color-scheme: light)" />
<link rel="icon" href="/favicon-dark.svg?v=2" type="image/svg+xml" media="(prefers-color-scheme: dark)" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
<link rel="preload" href="/_astro/manrope-latin-wght-normal.DHIcAJRg.woff2" as="font" type="font/woff2" crossorigin />
<link rel="alternate" type="application/rss+xml" title="造物局 · CraftSail" href="/rss.xml" />
<meta property="og:type" content="website" />
<meta property="og:locale" content="zh_CN" />
<meta property="og:site_name" content="造物局 · CraftSail" />
<meta property="og:title" content="造物局 · CraftSail — AI 与开发者趋势数据门户" />
<meta property="og:description" content="看见正在发生的变化，找到值得继续研究的人、项目与机会。" />
<meta property="og:url" content="https://craftsail.com/" />
<meta property="og:image" content="https://craftsail.com/og.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta name="twitter:card" content="summary_large_image" />
```

后面几节一块一块拆开讲。

---

## 三、图标和主题色

```html
<link rel="icon" href="/favicon.svg?v=2" type="image/svg+xml" media="(prefers-color-scheme: light)" />
<link rel="icon" href="/favicon-dark.svg?v=2" type="image/svg+xml" media="(prefers-color-scheme: dark)" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
<meta name="theme-color" content="#faf9f5" />
<meta name="application-name" content="造物局" />
<meta name="apple-mobile-web-app-title" content="造物局" />
```

favicon 准备浅色、深色两套，用 `media="(prefers-color-scheme: ...)"` 切换。只放一套的话，深色模式的标签栏上，浅色 SVG 直接看不见。

后面的 `?v=2` 是给缓存用的。换了图标记得改版本号，不然用户的浏览器和一部分爬虫会一直拿旧图。

`apple-touch-icon` 用 180×180 的 PNG，iOS 添加到主屏幕时用它。底色别透明，iOS 会给你填成黑的；也别自己切圆角，系统会切。

`theme-color` 和 `application-name` 跟排名没关系，只影响浏览器 UI 和添加到主屏幕后显示的名字。

上线前检查一遍：HTML 里写到的每个图标文件都要能打开、返回 `200`，路径和文件名一个字母都不能差。想照顾老浏览器和一些抓取工具，就再补一个 `/favicon.ico`。

这一块不影响排名，但一旦出错，搜索结果的小图标、分享卡片、主屏幕图标会一起坏。海外用户是先看到这些，才决定点不点的。

---

## 四、字体 preload

```html
<link rel="preload" href="/_astro/manrope-latin-wght-normal.DHIcAJRg.woff2" as="font" type="font/woff2" crossorigin />
```

只 preload 首屏真正用到的那一个字重。400、500、600、700 全列上，它们会和首屏的其他资源抢带宽。

`as="font"` 和 `crossorigin` 两个属性都得写。字体请求默认走匿名 CORS，少了 `crossorigin`，preload 下来的文件用不上，浏览器还会再下一遍。格式只用 `woff2`，别 preload `ttf`、`otf`。

路径里那串哈希是 Astro 构建时生成的，换了字体文件，文件名会跟着变，不用手动加 `?v=`。

Core Web Vitals 是 Google 的排序因素之一。preload 能减少首屏文字闪一下的情况（FOUT / FOIT），LCP 和 CLS 都会好一些。我们只 preload 一套标题字体，正文直接用系统字体。

---

## 五、title、description、robots、canonical

```html
<title>造物局 · CraftSail — AI 与开发者趋势数据门户</title>
<meta name="description" content="看见正在发生的变化，找到值得继续研究的人、项目与机会。" />
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1" />
<link rel="canonical" href="https://craftsail.com/" />
```

### 1. title

格式：

```text
[产品名] · [主关键词 / 核心价值]
```

首页写品类，内页把这一页讲的事放前面，品牌放后面：

```text
Acme — invoice API for SaaS
Pricing — Acme
Install the Acme CLI — Acme
```

每个 URL 的 title 都要不一样，全站用同一句是最常见的错。长度控制在英文 50–60 个字符、中文 20–30 个字，再长会被搜索结果截断。

| 页面 | 差的 title | 好的 title |
| --- | --- | --- |
| 首页 | Welcome to Acme | Acme — invoice API for SaaS |
| 定价 | Pricing | Pricing — Acme |
| 文档 | Docs | Install the Acme CLI — Acme |
| 关于 | About | About Acme |
| 分类 / 主题 | News | AI news — Acme（前提是这一页确实只讲这个） |

Google 经常改写 title。你写的越接近页面上的 H1 和正文，被改的可能越小。

### 2. description

Google 说过 meta description 不直接影响排名。但搜索结果标题下面那两行摘要常常取自它，用户点不点就看这两行。写得不好，Google 会自己从正文里挑一段。

每页写一句自己的，英文 140–160 字符，中文 70–90 字。先说这页是什么，再说跟别人哪里不一样。页面上没有的东西，不要写进去。

分类页和工具页要把边界写清楚。比如「近 24 小时公开资讯，标题链到原文，不是官方热搜」，时间范围、来源、点了去哪、它不是什么，一句都交代了。

### 3. 不用写 keywords

`meta keywords` 不用写。Google 2009 年起就不拿它排名了，官方文档到现在还把它列为 unsupported，Bing 也基本不看。

想覆盖哪些词，就让这些词出现在 title、H1 和正文里，每个问题给一个独立 URL，再用站内链接把这些页串起来。

### 4. robots meta

```html
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1" />
```

| 指令 | 含义 |
| --- | --- |
| `index` | 允许收录这一页 |
| `follow` | 允许顺着这一页的链接继续抓 |
| `max-image-preview:large` | 允许搜索结果用大图预览 |
| `max-snippet:-1` | 摘要长度不限 |
| `max-video-preview:-1` | 视频预览时长不限 |

公开页照这样写就行。下面几类要改：

- 登录后的 app：`noindex, nofollow`
- 预发环境、staging：整站 `noindex`，也不要放进 sitemap
- 感谢页、筛选参数页、打印页：一般 `noindex`
- 不存在的路径：返回 HTTP `404`，带 `noindex`

`robots.txt` 管的是能不能抓，meta robots 管的是抓到以后能不能进索引。想把一页从搜索结果里拿掉，要用 `noindex`，光写 `Disallow` 不够。

### 5. canonical

```html
<link rel="canonical" href="https://example.com/pricing" />
```

canonical 用绝对 HTTPS 地址，每个可收录页都指向自己。`www` 和不带 `www`、有没有尾斜杠、`http` 和 `https`，各选定一种，其余全部 301/308 过来。带 UTM、ref、语言参数的地址，canonical 指回干净的 URL。canonical 里不要出现 `#`。

筛选分两种情况。只是同一份内容换个时间窗或排序（比如 `?range=7d`），不用单独给 canonical。筛出来已经是另一份内容了（比如「AI 资讯」和「全部资讯」），那它就该有自己的路径，而不是挂在 query 上。

常见的错误写法：

```html
<link rel="canonical" href="https://example.com/#/pricing" />
<link rel="canonical" href="/pricing" />
<link rel="canonical" href="http://example.com/pricing?utm_source=twitter" />
```

正确写法：

```html
<link rel="canonical" href="https://example.com/pricing" />
```

旧的 query 地址迁到新路径，用 308：

```text
/news/?category=ai   308 →  /news/ai/
```

---

## 六、Open Graph 和 Twitter Card

```html
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Acme" />
<meta property="og:locale" content="en_US" />
<meta property="og:title" content="Acme — invoice API for SaaS" />
<meta property="og:description" content="Create, send, and reconcile invoices from your app." />
<meta property="og:url" content="https://example.com/" />
<meta property="og:image" content="https://example.com/og.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="Acme invoice API" />
<meta name="twitter:card" content="summary_large_image" />
```

OG 图统一用 1200×630 的 PNG 或 JPG，Facebook、LinkedIn、Slack、iMessage、X 都认这个尺寸。

最好每页一张图，至少首页、核心功能页和每篇长文，别共用一张没字的海报。craftsail.com 现在全站共用一张 `og.png`，这是还没做完，不要照抄。

`og:url` 要和 canonical 一致。文章页的 `og:type` 写 `article`，再补上 `article:published_time`。

X 找不到 twitter 标签时会退回去读 OG，但显式写上 `twitter:card=summary_large_image` 更稳。`twitter:site` 等有了官方账号再写，填一个打不开的账号，还不如空着。

写完用这几个工具检查：

- [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/)
- X Card Validator

改过图以后，要在工具里重新抓一次，不然看到的一直是旧缓存。

海外流量很多是从 Slack、X、LinkedIn 和邮件转发过来的。卡片上的图和标题不对，别人就不点了。

---

## 七、JSON-LD

按 Google 推荐的写法，一页放一个 `<script type="application/ld+json">`，用 `@graph` 把几个实体装在一起。

产品站常用这几种：

| 类型 | 作用 | 放在哪 |
| --- | --- | --- |
| `Organization` | 站点是谁、logo、社交账号 | 每个可收录页都可以引用 |
| `WebSite` | 站点名、语言 | 首页；内页用 `@id` 引用 |
| `SoftwareApplication` | 产品、价格 | 首页或产品页；价格要和定价页上写的一致 |
| `FAQPage` | 问答 | 页面上确实能看到这些问答的那一页 |
| `WebApplication` | 在浏览器里用的工具 | 工具页；免费就写 `"0"` |

聚合站、目录站把 `SoftwareApplication` 换成 `DataCatalog` 或 `CollectionPage`，别给一个门户标软件价格。

### 1. 用 `@id` 把实体连起来

```json
{
  "@type": "Organization",
  "@id": "https://example.com/#organization",
  "name": "Acme",
  "url": "https://example.com",
  "logo": "https://example.com/logo.png",
  "sameAs": ["https://github.com/acme/acme"]
}
```

别的地方要用站点信息，写 `"publisher": { "@id": "https://example.com/#organization" }` 引用就行，不用每页复制一份。

`sameAs` 只放打得开的账号，没有 X、没有 LinkedIn 就别写。

### 2. ItemList 指向原文

聚合别人的内容时，列表里每条的 `url` 填原文地址：

```json
{
  "@type": "ListItem",
  "position": 1,
  "name": "Copilot code review: An improved review experience",
  "url": "https://github.blog/changelog/2026-09-18-copilot-code-review-an-improved-review-experience"
}
```

而不是 `https://example.com/news/某个id/`。

很多聚合站给每条 RSS 都生成一个本站文章页，结果是一大堆和原文重复的薄页，canonical 也说不清该指向谁，Google 很容易把整站判成采集站。聚合站真正该被收录的，是主题页、来源目录，以及自己写的评测和文档。

### 3. FAQPage

FAQ 这几年变化不小。Google 从 2023 年起只给少数权威站点展示 FAQ 富结果，2026 年 5 月起干脆不在搜索结果里展示了。但 FAQPage 这个 schema 类型没有废，写了不出富结果，也不会扣分。Bing 和 AI 抓取还会读它，拿来把问答抽成现成的答案，所以我们一直留着。

前提是这些问答在页面上看得见。只在 JSON-LD 里写、正文里没有，属于误导性标记。

FAQ 放在用户会来找答案的页面。产品站放首页或 `/help/` 都行。数据门户的首页如果主要是数据和入口，就把 FAQ 放到 `/about/`，别挤在首屏。

JSON-LD 里的问题和页面上的标题要一字不差。问题用用户搜索时的原话，答案第一句先给结论，细节放后面。比如：

1. 这是什么？
2. 怎么安装？
3. 多少钱？
4. 数据从哪来？（做数据产品的话）

### 4. 几条硬规则

- JSON-LD 写在初始 HTML 里，别等 JS 执行完再插。社交平台的爬虫和不少 AI 爬虫根本不跑 JS
- 标记的内容要和页面上看得到的一致
- 用 [Rich Results Test](https://search.google.com/test/rich-results) 和 [Schema Markup Validator](https://validator.schema.org/) 检查
- 内页别复制首页的整份 `@graph`。分类页用 `CollectionPage` 加面包屑，文档页用 `TechArticle` 或 `FAQPage`，工具页用 `WebApplication`
- 没有真实评价就别写 `aggregateRating`，编评分是明确违规

---

## 八、robots.txt、sitemap.xml、llms.txt

`<head>` 管的是一页怎么被理解，这三个文件管的是整站怎么被发现。

顺便说一个常见的叫错：很多人说 `llm.txt`，约定的文件名其实是 `llms.txt`，多一个 s，规范在 [llmstxt.org](https://llmstxt.org/)。只放一个 `/llm.txt`，agent 不会去读。

### 1. robots.txt

只能放在站点根目录：`https://example.com/robots.txt`。

```txt
User-agent: *
Allow: /
Disallow: /account/
Disallow: /admin/
Disallow: /auth/
Disallow: /api/

Sitemap: https://example.com/sitemap-index.xml
```

几个容易踩的地方：

- `robots.txt` 只能挡抓取，挡不了收录。不想进索引，用 `noindex`
- 别 `Disallow` 掉 `/_astro/`、`/_next/` 这类放 JS、CSS 的目录，Google 渲染页面要用
- `Sitemap:` 写绝对 URL
- 文件是 UTF-8 纯文本，别让它返回一个 HTML 错误页
- 想给 AI 爬虫单独定规则，按 user-agent 分组写。我们默认全开放，对 GEO 更有利
- `Sitemap:` 里写的文件必须打得开。模块还没上线就先写上 `sitemap-projects.xml`，结果 404，还不如不写

见过有的站整站 `Disallow: /`，Google 一页都不抓，title 写得再好也没用。

对照：<https://craftsail.com/robots.txt>

### 2. sitemap.xml

sitemap 是交给搜索引擎的一份 URL 清单。它不保证收录，但能让新页被发现得快一些，新站、文档层级深、外链少的站效果更明显。

站大了就用 sitemap index，下面拆成几个文件。Help 更新少、数量不多，可以和营销页放在一起。Blog 天天更新、数量涨得快，单独放 `sitemap-blog.xml`，`lastmod` 填文章的真实发布时间。

```text
https://example.com/
https://example.com/pricing
https://example.com/help/getting-started/what-is-acme
https://example.com/help/getting-started/install
https://example.com/blog/2026-09-19-signups
https://example.com/about
```

App、登录页、带 `?category=` 的筛选页、随快照变化的 ID，都不要放。Blog 发一篇就加一篇，别攒到周末。

Google 文档里的要求：

- 只放 canonical、可收录、返回 200 的 URL
- 不放 `noindex`、重定向、404、登录墙、带 `#` 的地址
- 用绝对 HTTPS URL
- 单个文件最多 50,000 条或 50MB（未压缩）
- `lastmod` 确定准确才写，不确定就不写
- `<priority>` 和 `<changefreq>` Google 不看，不用费时间调
- 在 `robots.txt` 里写 `Sitemap:`，同时到 Google Search Console 提交

会随时间增减的条目可以进 sitemap，但别写进 `llms.txt`。目录里一堆 404，比没有目录还糟。

对照：<https://craftsail.com/sitemap-index.xml>

### 3. llms.txt

`llms.txt` 是 Jeremy Howard 和 Answer.AI 在 2024 年提出的约定，2026 年 8 月出了 v2。它是一份 Markdown 格式的目录，告诉 agent 这个站是干什么的、先看哪几页。

它不是 W3C 标准，也拦不住任何人。Google 明确说过，Search、AI Overviews 和 AI Mode 都不用它排名。Ahrefs 2026 年统计了 13.7 万个域名，28% 放了这个文件，其中 97% 从来没被请求过。会来读它的主要是编程 agent 和审计工具，ChatGPT、Perplexity 这类回答引擎用得不多。

所以它对 Google SEO 几乎没用，对 Cursor、Claude Code、文档问答和自己搭的 agent 有用。做一个花不了多少时间，但它替代不了 robots.txt、sitemap 和正经的 HTML。

结构一般是：H1 写站名，接一段简介，`Start here` 列最稳定的入口，中间按主题分组，最后是 `Optional`。

```md
# Acme

> Invoice API for SaaS.

## Start here
- [Home](https://example.com/): what this is
- [Pricing](https://example.com/pricing): plans and limits
- [Help](https://example.com/help/getting-started): install and first request
- [Blog](https://example.com/blog/): data posts and guides

## Optional
- [GitHub](https://github.com/acme/acme)
- [Privacy](https://example.com/privacy)
```

只列稳定的 URL，会消失的 ID 不要列。每个链接都要打得开，不能是 hash 路由。`Disallow` 也别往这里写，这个文件没有 robots 的效力。

站上本来就没有独立文档页的话，这里列再多链接也没意义。文档站可以顺便给每页提供一个 Markdown 版本。做到目录这一步就够了，`llms-full.txt` 不是必须的。

Stripe Docs、Anthropic、Cloudflare，还有 Mintlify 托管的文档站都在用。对照：<https://craftsail.com/llms.txt>

---

## 九、所有入口都用 `<a href>`

搜索引擎发现新页面，主要靠顺着 HTML 里的 `<a href="真实地址">` 往下爬。Google 的 [JavaScript SEO](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics) 文档说得很明白：导航用标准的 `<a href>` 指向独立 URL，别拿 `onclick` 当导航，也别拿 `#/path` 当页面地址。

### 1. 爬虫不会点按钮

下面这些写法，人用起来没问题，爬虫基本当它们不存在，或者以为还在当前页：

```html
<div onclick="go('/pricing')">Pricing</div>
<button onclick="router.push('/help')">Help</button>
<a href="#" onclick="openPage('install')">Install</a>
<div class="nav-item" data-href="/pricing">Pricing</div>
```

筛选、Tab、长得像按钮的入口，底下也得是链接：

```html
<nav>
  <a href="/help">Help</a>
  <a href="/help/install/macos">Install</a>
  <a href="/blog/">Blog</a>
  <a href="/pricing">Pricing</a>
</nav>
```

检验方法：关掉 JS，或者直接 `curl` 这个 URL，看源码里有没有 title、H1 和分类链接。这一步过不了，后面 JSON-LD 写得再全也白搭。

收藏、刷新、加载更多、提交表单，这些还是用 `<button>`，它们本来就不是页面跳转。

### 2. 用前端框架也一样

Astro、React、Vue、Next 里的 `<a>`、`<Link>`、`<router-link>`，只要最后渲染出来带真实的 `href`，就没问题。

```jsx
// 可以：最终 HTML 是 <a href="/pricing">
<Link href="/pricing">Pricing</Link>

// 不行：最终是 <div> 或 href="#"
<div onClick={() => router.push('/pricing')}>Pricing</div>
```

检查时看「查看网页源代码」，别看 DevTools 的 Elements 面板。源码里搜不到这些 `href`，说明它们是 JS 跑完才插进去的。Google 也许能渲染出来，Bing、很多 AI 爬虫和社交平台的爬虫就不一定了。

### 3. `#` 只用来跳到页内某一段

```html
<!-- 可以：同一页里跳到章节 -->
<a href="/help/install/macos#requirements">Requirements</a>

<!-- 不行：把整页内容挂在 hash 上 -->
<a href="/#/pricing">Pricing</a>
<a href="/help#install">Install</a>
```

搜索引擎默认把 `#` 后面的部分当成同一个 URL 里的某个位置。`https://example.com/#/pricing` 在收录上经常被当成首页。

### 4. 页头和页脚

页头、页脚里的链接同样要是 `<a href>`。页脚把 Blog、Help、定价都挂上，营销页之间也用真链接互相指，别全靠 JS 路由。只在首页放一个「Docs」按钮是不够的，Help 的侧栏和面包屑见第十三节。

---

## 十、每个答案一个独立 URL

### 1. 用路径，不用 query 或 hash

下面这些地址，人点着能用，但爬虫打开列表页时，源码里往往看不到对应的内容：

```text
/help?page=install
/blog?category=seo
/#/pricing
```

改成：

```text
/help/install/macos
/blog/2026-09-19-signups
/pricing
```

旧的 query 地址 308 到新路径。带搜索词 `q` 或临时筛选的页面不用重定向，加 `noindex`，canonical 指回路径就行。

| 错误 URL | 搜索引擎看到的 | 正确 URL |
| --- | --- | --- |
| `/#/pricing` | 首页 | `/pricing` |
| `/blog#hello` | `/blog` | `/blog/hello` |
| `/help#install` | `/help` | `/help/getting-started/install` |
| `/?page=pricing` | 带参数的首页 | `/pricing` |

Google 2015 年就废弃了 `#!` 那套 AJAX 抓取方案。hash 路由现在只适合不需要被搜到的内部工具。

### 2. 每个内容页都能直接打开

拿一个 URL 贴进无痕窗口，什么都不点，正文就应该已经在 HTML 里了。刷新、后退、从 Google 点进来，看到的都得是同一页。存在的文档返回 `200`，不存在的返回真正的 `404`，别什么路径都回一个 `index.html`。

SPA 常见的 soft 404 就是这么来的：随便输一个地址，状态码还是 200，页面上靠 JS 显示「Not found」。搜索引擎会把这些空页也收进去，白白浪费抓取配额。不存在的路径应该返回 HTTP 404，同时带上 `x-robots-tag: noindex`。

### 3. 可以做得像 SPA，但 HTML 要先出来

| 部分 | 建议 |
| --- | --- |
| 首页、定价、Help、Blog、关于 | 静态生成或 SSR，构建时或请求时输出完整 HTML |
| 列表筛选 | 看起来可以像按钮，底下是独立路径 |
| 产品本身的 App | 可以是 SPA，加 `noindex` |
| 只在浏览器里用的工具 | 说明文字放进 HTML，交互交给 JS |

框架怎么选见第十一节，Help 的 sidebar 见第十三节。

### 4. URL 怎么起

好的路径读起来像导航，也像用户会搜的问题：

```text
/                    首页
/pricing             定价
/help                Help 入口
/help/install/macos  一篇 Help
/blog/               Blog 列表
/blog/{slug}         一篇 Blog
/about               这是什么
/privacy             隐私
```

全部小写，用短横线连接，不要空格和驼峰。一层只放一个主题，别出现 `/help/index.html#/install` 这种。中英文分开放在不同目录或子域，配上 `hreflang`；只有一种语言，就别硬做双语。改了 URL 一定要 301/308 到新地址，sitemap、llms.txt 和站内导航也一起改。

---

## 十一、为什么用 Astro，不用 Next.js

前面几节反复在说一件事：关掉 JS，HTML 里该有的都得有。还有一件事是速度。Core Web Vitals 算进排序，海外用户离服务器又远，首屏每多一百 KB 的 JS，都能实实在在感觉到慢。

营销页、Blog、Help 说到底都是文档。给这类站选框架，我们主要看两件事：默认输出什么样的 HTML，默认往浏览器里塞多少 JS。

craftsail.com 用的是 Astro，第四节那个 `/_astro/...woff2` 路径就是它打出来的。

### 1. 对比

| | Astro | Next.js |
| --- | --- | --- |
| 默认下发的 JS | 0。只有标了 `client:*` 的组件（island）才下发 JS | React 运行时加路由，每页都要水合。Server Components 能少发组件代码，运行时还是要发 |
| 首屏速度 | 纯 HTML + CSS，不怎么调 LCP、INP 就能达标 | 能做到，但要一直压包体积，盯着 `'use client'` 的边界 |
| 默认渲染 | 构建时静态生成，个别页面再开 SSR | SSR、SSG、ISR、RSC 都有，规则多 |
| 内容管理 | Content Collections，Markdown / MDX 加 schema 校验 | 自己接 MDX 或第三方内容库 |
| Blog feed 流 | `paginate()` 生成分页 URL，`@astrojs/rss` 生成 RSS，都是官方的 | 分页路由和 RSS 的 route handler 自己写 |
| Help sidebar | 官方文档主题 Starlight，侧栏现成 | Nextra、Fumadocs 等第三方 |
| sitemap | `@astrojs/sitemap` | 内置 `app/sitemap.ts` |
| 部署 | 产物是静态文件，任何 CDN、Cloudflare Pages、GitHub Pages、Nginx 都能放 | 在 Vercel 上最省心；`output: 'export'` 纯静态导出会丢掉 ISR、middleware、默认的图片优化等 |
| 交互复杂的 App | 不擅长，island 之间共享状态要自己处理 | 擅长 |
| 生态、招人 | 小一些，不过 island 里可以直接用 React / Vue / Svelte 组件 | React 生态最大 |

### 2. Astro 的缺点

用下来，Astro 有几处不太顺手。

页面之间跳转是整页刷新。打开 `prefetch`，或者加上 `<ClientRouter />` 做过渡，大部分时候感觉不出来。

页面上的交互组件（island）彼此独立。几个组件要共享状态，得自己接 nanostores 之类的库。

全静态生成的话，每发一篇文章都要重新构建、部署。一天 3 篇、总量几千篇没什么压力，真到了上万篇，再考虑按需 SSR 或者拆分构建。

登录以后那种交互很重的 App，它也不擅长。

### 3. Next.js 用在内容站上的问题

Next.js 也能把 SEO 做好，只是放在内容站上，要多花不少力气，才能做到 Astro 默认的水平。

纯文字页面也要下发 React 运行时，再水合一遍，这些 JS 对爬虫和读者都没用。它的渲染方式和缓存规则比较多，`'use client'` 放错一个位置，下面整棵组件树都会变成客户端组件。不用 Vercel、自己托管的话，ISR、图片优化、缓存都得自己处理。Blog 和 Help 用不到它的大部分功能，这些成本却一样不少。

### 4. 我们怎么分

| 部分 | 用什么 |
| --- | --- |
| 首页、定价、关于、Blog、Help | Astro 静态生成；Help 用 Starlight 或自己写侧栏组件 |
| 页面上零星的交互：搜索框、订阅表单、图表 hover | 在 Astro 里挂 island，用 `client:idle` 或 `client:visible` |
| 登录后的 App、编辑器、控制台 | React、Next 或别的 SPA 都行，放在 `/app/` 或 `app.` 子域，加 `noindex` |

对外给爬虫和新用户看的页面用 Astro，登录以后的部分再考虑 React。

页面上需要一点交互时，这样挂一个 island：

```astro
---
import SearchBox from '../components/SearchBox.tsx';
---
<!-- 只有这一个组件下发 JS，浏览器空闲时才加载 -->
<SearchBox client:idle />
```

### 5. 基础配置

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';
import sitemap from '@astrojs/sitemap';

export default defineConfig({
  site: 'https://example.com',      // canonical、sitemap、RSS 都靠它拼绝对 URL
  prefetch: { prefetchAll: true },  // 鼠标悬停就预取下一页，补回跨页速度
  integrations: [sitemap()],
});
```

`site` 一定要写，sitemap 和 RSS 靠它生成绝对地址。要不要尾斜杠，用 `trailingSlash` 定下来，和部署平台的行为、canonical、sitemap 保持一致。图片用 `astro:assets` 的 `<Image />`，它会自动带上 `width` 和 `height`，不会引起布局抖动。

### 6. 怎么验收速度

先 `curl` 一个内容页，数一下有几个 `<script>`：

```bash
curl -s https://example.com/blog/ | grep -c '<script'
```

内容页应该是 0，或者很接近 0。然后用 PageSpeed Insights 的移动端测三页：首页、一篇 Blog、一篇 Help。达标线按 Google 定的「良好」：LCP 低于 2.5 秒，INP 低于 200 毫秒，CLS 低于 0.1。

---

## 十二、Blog 做成 feed 流

首页、定价、Help 很少改。搜索引擎多久回来一次，要看你是不是稳定地有新页面，Blog 就是干这个的。

我们的节奏是每天 3 篇。每篇一个独立 URL，发布当天进 sitemap，RSS 同步更新。爬虫发现这个站每天都有新东西，来得就勤了。3 篇是给数据管道定的量，不是为了凑数，同一段话换三个标题发出去，只会多出三个薄页。

### 1. `/blog/` 长什么样

`/blog/` 按发布时间倒序排成一条流，最新的在最上面。每天 3 篇进来，这一页的 HTML 每天都在变，爬虫回来一趟，当天的新链接就都拿到了。

流里每一条只放卡片，不放全文：

```html
<main>
  <h1>Blog</h1>

  <nav aria-label="Blog 分类">
    <a href="/blog/" aria-current="page">全部</a>
    <a href="/blog/type/data/">数据</a>
    <a href="/blog/type/guide/">教程</a>
    <a href="/blog/type/compare/">对比</a>
  </nav>

  <ol class="feed">
    <li>
      <article>
        <a href="/blog/2026-09-19-signups">
          <img
            src="/blog/2026-09-19-signups.png"
            alt="Daily signups 1–19 Sep 2026. Peak 420 on 12 Sep."
            width="600"
            height="315"
          />
          <h2>9 月注册量：12 日到峰值 420</h2>
        </a>
        <p><time datetime="2026-09-19">2026-09-19</time> · 数据</p>
        <p>前 19 天注册 4,860，比 8 月同期多 31%。</p>
      </article>
    </li>
    <!-- 每页 20 条 -->
  </ol>

  <nav aria-label="分页">
    <a href="/blog/2" rel="next">更早的文章</a>
  </nav>
</main>
```

卡片的标题包在 `<a href>` 里，指向文章自己的 URL。卡片上放标题、日期、分类、一两句摘要和一张缩略图就够了。全文只留在文章页，别让列表页和文章页各有一份。`<time datetime>` 填真实的发布时间。

分类用独立路径 `/blog/type/data/`，不用 `?type=data`，也不做成前端的 Tab 切换。

首屏第一张缩略图别加 `loading="lazy"`，它很可能就是 LCP 元素，后面的图再 lazy。首页可以放最新 5 到 10 条同样的卡片，再链到 `/blog/`。

### 2. 无限滚动要有分页 URL 兜底

feed 流最容易出问题的地方，是只做了「滚到底自动加载」。爬虫不会滚动，也不会点「加载更多」，最后只看得到第一屏的 20 篇，更早的文章全靠 sitemap。

Google 对无限滚动的建议，是在背后准备一组能直接打开的分页 URL：

```text
/blog/      最新 20 篇
/blog/2     第 21–40 篇
/blog/3     ...
```

每一页的 HTML 里都要有上一页、下一页的 `<a href>`。「加载更多」本身就写成 `<a href="/blog/2">`：有 JS 时拦截点击，把下一页的卡片接到当前列表后面，再用 `history.pushState` 把地址栏改成 `/blog/2`；没有 JS，它就是一个普通链接。

分页页的 canonical 指向自己，别统统指回 `/blog/`。title 带上页码，比如 `Blog — Page 2 — Acme`。分页页不用进 sitemap，文章页必须进。

### 3. 有数据就画图

纯文字的日报，很容易看起来像模板生成的。有数字就画成图，正文里再写一遍能直接读的数。

爬虫不执行 Canvas，所以图要在初始 HTML 里：用内联 SVG，或者 `<img>` 加上把数字写清楚的 `alt`。图下面配一个 `<table>` 或一段结论，图挂了，文字还在。外面套 `<figure>` 和 `<figcaption>`，caption 写一句正常人能看懂的话，别只写「图 1」。

```html
<figure>
  <img
    src="/blog/2026-09-19-signups.png"
    alt="Daily signups 1–19 Sep 2026. Peak 420 on 12 Sep."
    width="1200"
    height="630"
  />
  <figcaption>9 月前 19 天注册量，12 日最高 420。</figcaption>
</figure>
<table>
  <thead>
    <tr><th>Date</th><th>Signups</th></tr>
  </thead>
  <tbody>
    <tr><td>2026-09-12</td><td>420</td></tr>
  </tbody>
</table>
```

有数据管道，这 3 篇就从管道里出，别手写灌水。哪天没数据，那篇就不发。

### 4. 三篇的结构要不一样

三篇别都套「引言、三点、总结」。类型不一样，HTML 结构也该不一样，人和爬虫才看得出这是三篇文章，而不是一个模板填了三次。

| 类型 | URL 例子 | 结构 |
| --- | --- | --- |
| 数据 / 日报 | `/blog/2026-09-19-signups` | 一句话结论 → 图 → 数字表 → 比昨天多了什么 |
| 教程 | `/blog/install-cli-on-macos` | 每一步一个 `h2`，命令放进 `<pre>` |
| 对比 | `/blog/acme-vs-foo-pricing` | 对照表，每列一个产品，每行一项 |

文章页的 `og:type` 用 `article`，加上 `article:published_time`。JSON-LD 用 `BlogPosting`，`image` 指向那张图。列表页 `/blog/` 用 `CollectionPage`，别把全文都堆到列表里。

### 5. 让新文章被找到

```text
/blog/                      feed 第一页
/blog/2                     feed 分页
/blog/type/data/            分类 feed
/blog/2026-09-19-signups    一篇
/rss.xml                    RSS，全文或摘要都行
/sitemap-blog.xml           只放已发布、200、canonical 的文章
```

每页的 `<head>` 里都放一行 `<link rel="alternate" type="application/rss+xml" href="/rss.xml">`。RSS 保留最新 20 到 50 篇，`pubDate` 用真实发布时间，`link` 用 canonical URL。页面上的 feed 和 RSS 要来自同一份数据、同一个排序，别出现两边对不上的情况。

页头、页脚、上一篇下一篇、相关的 Help 文档，都用 `<a href>` 链到文章。新文章如果只出现在首页的 JS 里，不进 sitemap，也没有站内链接，爬虫会来得很慢。

转载、搬运 RSS、把别人的 changelog 改写一遍，这些都不放进 `/blog/`，直接链原文。

### 6. 用 Astro 实现

文章放在 Content Collection 里。排序单独写成一个函数，feed 页和 RSS 都用它：

```ts
// src/lib/posts.ts
import { getCollection } from 'astro:content';

export async function getPosts() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  return posts.sort((a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf());
}
```

分页页面用 `[...page].astro`，第一页正好是 `/blog/`，后面是 `/blog/2`、`/blog/3`，构建时全部生成静态 HTML：

```astro
---
// src/pages/blog/[...page].astro
import { getPosts } from '../../lib/posts';
import PostCard from '../../components/PostCard.astro';

export async function getStaticPaths({ paginate }) {
  return paginate(await getPosts(), { pageSize: 20 });
}

const { page } = Astro.props;
---
<ol class="feed">
  {page.data.map((post) => <li><PostCard post={post} /></li>)}
</ol>

<nav aria-label="分页">
  {page.url.prev && <a href={page.url.prev} rel="prev">更新的文章</a>}
  {page.url.next && <a href={page.url.next} rel="next">更早的文章</a>}
</nav>
```

RSS：

```js
// src/pages/rss.xml.js
import rss from '@astrojs/rss';
import { getPosts } from '../lib/posts';

export async function GET(context) {
  const posts = (await getPosts()).slice(0, 50);
  return rss({
    title: 'Acme Blog',
    description: 'Daily data posts and guides from Acme.',
    site: context.site,
    items: posts.map((post) => ({
      title: post.data.title,
      description: post.data.description,
      pubDate: post.data.pubDate,
      link: `/blog/${post.id}`,
    })),
  });
}
```

文章的 slug 用日期或英文短语，比如 `2026-09-19-signups`。别用纯数字，不然会和分页的 `/blog/2` 撞路径。

---

## 十三、Help 的侧栏

用户靠侧栏跳转，爬虫也靠侧栏发现页面。「一篇正文，左边一列 `<a href>`」，对文档站来说是花力气最少、SEO 收益最大的结构。

没有侧栏的 Help，通常是一个大单页，或者每次只渲染当前这一篇。爬虫进来只看得到这一篇，更深的文档只能指望 sitemap。

有了侧栏，每篇文档的 HTML 里都带着几十个指向其他文档的真实链接。新文档更容易被发现，同一主题的文档（入门、AI、权限、安装）被链成一组，链接文字本身又是关键词，比如 `Install on Kubernetes`、`Bring your own model`。Stripe、Cloudflare、MDN、GitHub Docs 的侧栏都是真链接，没有一家是点开才加载的 JS 菜单。

### 1. 侧栏的链接要写在 HTML 里

```html
<aside>
  <nav aria-label="Help">
    <a href="/help">Help Center</a>

    <p>Getting started</p>
    <a href="/help/getting-started/what-is-acme">What is Acme</a>
    <a href="/help/getting-started/install">Install on Kubernetes</a>
    <a href="/help/getting-started/license">Get a license</a>
    <a href="/help/getting-started/first-workspace">Create the first workspace</a>

    <p>AI</p>
    <a href="/help/ai/agents">How AI agents edit documents</a>
    <a href="/help/ai/byok">Bring your own model</a>
    <a href="/help/ai/history">See agent edits in history</a>

    <p>Security</p>
    <a href="/help/security/permissions">Document permissions</a>
    <a href="/help/security/audit-logs">Audit logs</a>
    <a href="/help/security/private-cloud">Private cloud boundary</a>
  </nav>
</aside>

<article>
  <h1>Install Acme on Kubernetes</h1>
  <p>...</p>
</article>
```

折叠效果可以用 CSS 或 JS 做，但折叠之前，这些 `<a>` 就得在源码里。不能等用户点开「AI」分组，才把下面的链接插进 DOM。用 `<details open>` 没问题，点击后再 `fetch` 子树就不行。

当前这篇用 `<span>`，或者加 `aria-current="page"`，其他全是链接。链接文字写用户会搜的词，别一排 `Click here`。

用 curl 检查一下：

```bash
curl -sL https://example.com/help/getting-started/install | grep 'href="/help/'
```

源码里应该能搜到 What is Acme、Bring your own model、Audit logs 这些其他文档的链接，而不只是当前这一篇。Google 也许能渲染客户端生成的侧栏，Bing 和很多 AI 爬虫不行。

框架我们推荐 Astro，用 Starlight 或者自己写组件都行，写法见本节第 5 小节。Docusaurus、VitePress、Mintlify 也可以，它们默认就是这种结构。别用纯客户端的 React SPA 去硬做 Help。

### 2. 面包屑和相关链接

侧栏负责上下层级，正文里的相关链接负责把不同主题连起来。

```html
<nav aria-label="Breadcrumb">
  <a href="/help">Help</a>
  ›
  <a href="/help/getting-started">Getting started</a>
  ›
  <span>Install</span>
</nav>

<article>
  <h1>Install Acme on Kubernetes</h1>
  <p>...</p>
</article>

<section>
  <h2>Next</h2>
  <a href="/help/getting-started/license">Get a license</a>
  <a href="/help/ai/byok">Bring your own model</a>
  <a href="/pricing">See pricing</a>
</section>
```

面包屑再配一段 `BreadcrumbList` JSON-LD。层级深的文档站，搜索结果里有机会显示成 `Help > Getting started > Install`。

### 3. 一个 URL 只回答一个问题

文档站别写成一本产品手册的目录。

| 弱的页面 | 更容易被搜到的页面 |
| --- | --- |
| `/help/overview` | `/help/getting-started/what-is-acme` |
| `/help/ai` | `/help/ai/byok`、`/help/ai/agents` |
| `/help/faq` 一页 40 问 | 高频问题各写一篇；入口页的 FAQ 只留 6–8 个链接 |
| `/help/security` | `/help/security/permissions`、`/help/security/audit-logs` |

每篇文档的结构固定下来：H1 写问题或任务，前两段直接给结论，然后是步骤、注意事项和限制，接着放相关链接，最后写上更新时间。

这样的页面，搜索引擎和 AI 才愿意直接拿来当答案；侧栏再把它们连成一张网，它们才找得到。GEO 以后单独写一篇。

### 4. 可以参考的文档站

打开下面这些页面的源代码，侧栏里都是一排排的 `href`：

- https://docs.stripe.com/payments/checkout
- https://developers.cloudflare.com/workers/get-started/guide/
- https://docs.github.com/en/get-started/start-your-journey/about-github-and-git
- https://docusaurus.io/docs/seo
- https://mintlify.com/docs/ai/llmstxt

别参考的：把帮助中心做成 iframe、做成 SPA，或者只有一个搜索框、结果不带链接的知识库。这类系统人还能搜一搜，爬虫什么都看不到。

### 5. 用 Astro 实现

最省事的是 Starlight，Astro 官方的文档主题。侧栏、上一篇下一篇、页内目录、Pagefind 站内搜索都是现成的，分组折叠用的是 `<details>`，所有链接都在源码里。它默认不带面包屑，要自己覆盖组件，或者装社区插件。

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';
import starlight from '@astrojs/starlight';

export default defineConfig({
  site: 'https://example.com',
  integrations: [
    starlight({
      title: 'Acme Help',
      sidebar: [
        { label: 'Getting started', autogenerate: { directory: 'help/getting-started' } },
        { label: 'AI', autogenerate: { directory: 'help/ai' } },
        { label: 'Security', autogenerate: { directory: 'help/security' } },
      ],
    }),
  ],
});
```

文档放在 `src/content/docs/help/getting-started/install.md`，URL 就是 `/help/getting-started/install`。

想和主站用同一套样式，就自己写一个侧栏组件。它在构建时运行，生成的就是整棵树的 `<a>`，浏览器端一行 JS 都没有：

```astro
---
// src/components/HelpSidebar.astro
import { getCollection } from 'astro:content';

const docs = (await getCollection('help')).sort((a, b) => a.data.order - b.data.order);
const groups = Object.groupBy(docs, (doc) => doc.data.group);
const current = Astro.url.pathname;
---
<nav aria-label="Help">
  <a href="/help">Help Center</a>
  {Object.entries(groups).map(([group, items]) => (
    <details open>
      <summary>{group}</summary>
      {items.map((doc) => {
        const href = `/help/${doc.id}`;
        return (
          <a href={href} aria-current={href === current ? 'page' : undefined}>
            {doc.data.title}
          </a>
        );
      })}
    </details>
  ))}
</nav>
```

不管用哪种，都拿第 1 小节那条 `curl | grep 'href="/help/'` 验收。

---

## 十四、每类页面的静态信息

首页那段 `<head>` 只适合首页。内页的 title、description、canonical、og:url 和 JSON-LD 都要单独写。

产品站可以照这张表：

| 页面 | title | JSON-LD |
| --- | --- | --- |
| `/` | 品牌 + 品类 | Organization + WebSite + SoftwareApplication |
| `/pricing` | `Pricing — {品牌}` | 价格写在正文里，和 schema 一致 |
| `/help/{path}` | 这一页回答的问题 | TechArticle / FAQPage |
| `/blog/{slug}` | 文章标题 | BlogPosting；`og:type=article`；有图就写 `image` |
| `/about` | `About {品牌}` | WebPage；需要时加 FAQPage |
| `/app`、`/account` | 不收录 | `noindex`，不进 sitemap |

Organization 用一个固定的 `@id`，比如 `https://example.com/#organization`，内页引用它就行，别把首页整个 `@graph` 复制过去。

首页 `SoftwareApplication` 里的价格，要和定价页上写的一样；`/pricing` 的价格要写在正文里，不能只出现在按钮上。Blog 和 Help 都是一篇一个 URL，都进 sitemap。`llms.txt` 里放 Help 的稳定入口，Blog 只放常青文章，日报不用放。App 加 `noindex`，不进 sitemap。

聚合站把 `SoftwareApplication` 换成目录类型，ItemList 指向原文，FAQ 别放在数据首屏。

---

## 十五、落地清单

### 框架与速度

- [ ] 营销页、Blog、Help 用 Astro 静态生成；登录后的 App 单独部署，加 `noindex`
- [ ] `curl` 下来的内容页几乎没有 `<script>`；交互组件用 `client:idle` / `client:visible`
- [ ] PageSpeed Insights 移动端：LCP < 2.5s，INP < 200ms，CLS < 0.1

### 站点根文件

- [ ] `https://example.com/robots.txt` 能打开，有 `Sitemap:` 行
- [ ] `Sitemap:` 指向的文件都能打开，里面只有 200、canonical、可收录的 URL
- [ ] `https://example.com/llms.txt` 是 Markdown 目录，链接都真实、稳定
- [ ] 预发环境、App、后台、会消失的 ID 都没进 sitemap 和 `llms.txt`

### 每个可收录页的静态信息

- [ ] 自己的 `<title>`
- [ ] 自己的 `meta description`
- [ ] 指向自己的 `canonical`
- [ ] `og:title` / `og:description` / `og:url` / `og:image`（1200×630）
- [ ] `twitter:card = summary_large_image`
- [ ] 需要的 JSON-LD 都写了，内容和页面一致；没有编造评分
- [ ] 没写 `meta keywords`；只有一种语言就没写 `hreflang`
- [ ] 这些标签在「查看网页源代码」里就有，不是 JS 后插的

### Blog

- [ ] `/blog/` 是倒序的 feed 流，卡片标题是 `<a href>`，只放摘要不放全文
- [ ] feed 有分页 URL（`/blog/2`），每页有上一页 / 下一页的 `<a href>`，canonical 指向自己
- [ ] 分类是独立路径（`/blog/type/data/`），不是 query，也不是前端 Tab
- [ ] feed 和 RSS 用同一份数据、同一个排序；每页 `<head>` 有 RSS 的 `alternate`
- [ ] `/blog/{slug}` 一篇一个 URL，`og:type=article`
- [ ] 每天 3 篇就是 3 个新 URL，当天进 `sitemap-blog.xml` 和 RSS
- [ ] 数据类文章的图在 HTML 里（SVG 或 `<img>`），旁边有表格或能直接读的数字
- [ ] 数据、教程、对比三类文章的 HTML 结构不一样
- [ ] 转载不进 `/blog/`

### Help

- [ ] `/help/{path}` 一篇一个 URL，只回答一个问题，没有 `#/`
- [ ] 关掉 JS 打开任意一篇，源码里能搜到侧栏上其他文档的 `href="/help/`
- [ ] 折叠用 CSS 或 `<details>`，不是点击后再插链接
- [ ] 链接文字就是问题本身：`Install on Kubernetes`、`Bring your own model`
- [ ] 每篇有面包屑和文末相关链接，面包屑配 `BreadcrumbList`
- [ ] H1 是问题或任务，前两段先给结论，页脚有更新时间
- [ ] 高频问题各写一篇，没有一页塞 40 问的 FAQ
- [ ] FAQ 只放在会有人来找答案的页面

### URL 与链接

- [ ] 主题、关于、Help、定价、对比、Blog 都是独立路径，没有 `#/`
- [ ] 导航、卡片、筛选、页脚、侧栏、按钮样式的入口都是 `<a href>`
- [ ] 源码里能搜到这些 href
- [ ] 不存在的文档返回 HTTP 404
- [ ] 改版后旧地址 301/308 到新地址

### 提交与验收

- [ ] Google Search Console 验证域名，提交 sitemap
- [ ] 用 URL Inspection 看「已抓取的页面」是不是完整的 HTML
- [ ] 用 Rich Results Test 检查 Organization / FAQPage
- [ ] 往 Slack、X、LinkedIn 各分享一次，确认卡片的图和标题
- [ ] 在没有 JS 的环境里打开一个内页，正文和导航链接都还在

---

## 参考资料

### Google Search Central

- [SEO Starter Guide](https://developers.google.com/search/docs/fundamentals/seo-starter-guide)
- [JavaScript SEO basics](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics)
- [Make your links crawlable](https://developers.google.com/search/docs/crawling-indexing/links-crawlable)
- [Influence your title links](https://developers.google.com/search/docs/appearance/title-link)
- [Meta tags and unsupported tags](https://developers.google.com/search/docs/crawling-indexing/special-tags)（含 `keywords` 不被使用）
- [Robots `meta` tag](https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag)
- [Canonicalization](https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls)
- [Sitemaps](https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview)
- [robots.txt](https://developers.google.com/search/docs/crawling-indexing/robots/intro)
- [Intro to structured data](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)
- [Organization](https://developers.google.com/search/docs/appearance/structured-data/organization)
- [Software app](https://developers.google.com/search/docs/appearance/structured-data/software-app)
- [Pagination and incremental page loading](https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading)（无限滚动要有分页 URL）
- [Core Web Vitals](https://developers.google.com/search/docs/appearance/core-web-vitals)
- [Documentation updates](https://developers.google.com/search/updates)（含 FAQ 富结果下线）
- [AI features and your website](https://developers.google.com/search/docs/appearance/ai-features)
- [Succeeding in AI search](https://developers.google.com/search/blog/2025/05/succeeding-in-ai-search)

### 结构化数据、社交卡片、llms.txt

- [Schema.org](https://schema.org/)
- [Open Graph protocol](https://ogp.me/)
- [X Cards markup](https://developer.x.com/en/docs/x-for-websites/cards/overview/markup)
- [The /llms.txt file, v2](https://llmstxt.org/)
- [Ahrefs: llms.txt study](https://ahrefs.com/blog/llmstxt-study/)

### Astro

- [Why Astro](https://docs.astro.build/en/concepts/why-astro/)
- [Islands architecture](https://docs.astro.build/en/concepts/islands/)
- [Pagination](https://docs.astro.build/en/guides/routing/#pagination)
- [Add an RSS feed](https://docs.astro.build/en/recipes/rss/)
- [Starlight](https://starlight.astro.build/)
- [Next.js static exports](https://nextjs.org/docs/app/guides/static-exports)（含静态导出不支持的功能）

### 可以对照的站点

- 文中 HTML 的来源：<https://craftsail.com/>、<https://craftsail.com/news/ai/>、<https://craftsail.com/about/>、<https://craftsail.com/llms.txt>
- [Stripe Docs](https://docs.stripe.com/payments/checkout)
- [Cloudflare Docs](https://developers.cloudflare.com/workers/get-started/guide/)
- [GitHub Docs](https://docs.github.com/en/get-started/start-your-journey/about-github-and-git)
- [Docusaurus SEO](https://docusaurus.io/docs/seo)
- [Mintlify llms.txt](https://mintlify.com/docs/ai/llmstxt)
