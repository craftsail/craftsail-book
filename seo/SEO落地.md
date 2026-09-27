# SEO 落地

搜索引擎和 AI 抓的是 HTML：能不能发现页面、读懂页面、把页面当成独立文档收录。

要做的是：

1. 先决定哪些页值得收录
2. 每个可收录页的静态信息写对
3. 根目录放好 `robots.txt`、`sitemap.xml`、`llms.txt`
4. 可点击入口都是真实的 `<a href>`，指向独立 URL
5. 营销页、Blog、Help 用 Astro 静态吐 HTML：Blog 做成带分页的 feed 流，Help 带整树 sidebar

文中 HTML 来自 [craftsail.com](https://craftsail.com)，方便你对照线上源码。方法对产品站、文档站同样成立。

---

## 一、先决定哪些页该被收录

有本站自己的内容，就给独立 URL。没有，就链到别处，不要做薄页。

产品站通常要收录：这是什么、多少钱、怎么装、怎么用。另外两块要单独做：

- **Blog**：持续给搜索引擎新文档，让它回来抓。`/blog/` 做成按时间倒序的 feed 流
- **Help**：给人查用法，sidebar 上堆真实 `<a href>`，让爬虫一次发现整棵文档树

聚合资讯、采集别人的文章，本身不是标准答案。别人已经写过的内容，不要再做一版「本站转载页」去抢收录。英文世界对采集站更敏感。

| 页面类型 | 产品站 | 聚合 / 数据门户 | 做法 |
| --- | --- | --- | --- |
| 首页 | 品牌 + 品类 | 品牌 + 品类 | 独立 title；Organization / WebSite |
| 定价 | `/pricing` | 通常没有 | 价格写在正文，JSON-LD 和可见内容一致 |
| Blog | `/blog/` feed 流 + `/blog/{slug}`，每天固定发 | 只有本站数据和观点才发 | 列表倒序分页；文章带图表 + 可区分的结构；转载不算 |
| Help | `/help/{path}` + sidebar | `/about/`、来源说明、隐私 | 一篇一个 URL；sidebar 源码里就要有整树 href |
| App | `/app/`、`/account/`、`/api/` | 同左 | `noindex`，写入 `robots.txt` |
| 导航 | 侧栏、页脚、面包屑 | 筛选控件也要是链接 | 全部 `<a href>` |

把别人的 Pricing、Help、Blog 结构原样搬到自己站上，如果对应内容不存在，会做出一批空壳页。

---

## 二、`<head>` 在干什么

`<head>` 同时给几类读取者看：

| 读取者 | 它看什么 | 你要给什么 |
| --- | --- | --- |
| 浏览器 / 手机系统 | 图标、主题色、字体 | favicon、apple-touch-icon、theme-color、font preload |
| Google / Bing | title、description、canonical、robots、正文、链接 | 可收录的独立 URL + 准确摘要 |
| 社交平台 | Open Graph、Twitter Card | 标题、描述、1200×630 图 |
| 搜索和 AI | JSON-LD、FAQ、清晰标题结构 | 实体、产品或目录、问答 |

下面这份是 craftsail.com 首页源码，用来对照各层怎么写。用无痕窗口打开，「查看网页源代码」，就能对上。

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

下面按层拆。

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

- **浅色 / 深色两套 favicon**：用 `media="(prefers-color-scheme: ...)"` 切换。深色浏览器标签栏上，浅色 SVG 会看不见。
- **`?v=2` 是缓存破坏**：换图标时改版本号，否则用户和部分爬虫会一直拿旧图。
- **`apple-touch-icon` 用 180×180 PNG**：iOS 加到主屏幕用。不要透明底（iOS 会填成黑），不要自己做圆角。
- **`theme-color`、`application-name` 不管排名**：它们管浏览器 UI 和「添加到主屏幕」时的名字。

检查：

- HTML 里写到的图标文件都要真实存在，返回 `200`
- 文件名和路径必须一致
- 需要的话再补 `/favicon.ico`，给老浏览器和部分抓取工具兜底

这层做错，搜索结果小图标、分享卡片、主屏幕图标会一起错。它不决定排名，但海外用户会先看标签栏图标和分享卡片，再决定点不点。

---

## 四、字体 preload

```html
<link rel="preload" href="/_astro/manrope-latin-wght-normal.DHIcAJRg.woff2" as="font" type="font/woff2" crossorigin />
```

- 只 preload **首屏真正用到的字重**。不要把 400/500/600/700 全 preload，会抢带宽。
- `as="font"` 和 `crossorigin` 必须写。字体请求默认是匿名 CORS，漏了 `crossorigin`，preload 会失效并重复下载。
- 用 `woff2`，不要 preload `ttf` / `otf`。
- 构建产物带哈希时，换字体文件路径自己变，不必再手改 `?v=`。上面这行是 Astro 打出来的文件名。

Google 把页面体验（Core Web Vitals）算进排序。字体 preload 能减少首屏文字闪烁（FOUT/FOIT），对 LCP / CLS 有帮助。只 preload 一两套展示字体，正文尽量走系统字体栈。

---

## 五、title、description、canonical、robots

```html
<title>造物局 · CraftSail — AI 与开发者趋势数据门户</title>
<meta name="description" content="看见正在发生的变化，找到值得继续研究的人、项目与机会。" />
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1" />
<link rel="canonical" href="https://craftsail.com/" />
```

### 1. Title

```text
[产品名] · [主关键词 / 核心价值]
```

首页用品类句，内页把这一页的主题放前面、品牌放后面：

```text
Acme — invoice API for SaaS
Pricing — Acme
Install the Acme CLI — Acme
```

- 每个 URL 必须有自己的 title，禁止全站同一句
- 大约 50–60 个英文字符，或中文 20–30 字，避免搜索结果被截断
- 主关键词靠前，品牌名可以放后面
- 首页讲品类，内页讲这一页的具体问题

| 页面 | 差的 title | 好的 title |
| --- | --- | --- |
| 首页 | Welcome to Acme | Acme — invoice API for SaaS |
| 定价 | Pricing | Pricing — Acme |
| 文档 | Docs | Install the Acme CLI — Acme |
| 关于 | About | About Acme |
| 分类 / 主题 | News | AI news — Acme（如果这一页真是这个主题） |

Google 经常改写 title。写得越贴近页面真实 H1 和正文，被改写的概率越低。

### 2. Description

Google 明确说过：meta description **不直接参与排名**，但会影响搜索结果摘要和点击率。写不好，Google 会自己从正文抽一段。

- 每页独一无二
- 英文大约 140–160 字符，中文大约 70–90 字
- 先说这一页是什么，再说和别人的差别
- 必须和页面可见正文一致，不要写页面上没有的承诺

分类页、工具页尤其要写清边界。例如「近 24 小时公开资讯，标题链到原文，不是官方热搜」——时间窗、来源、点击去哪、它不是什么，都写进去。

### 3. Keywords 不用做

不要写 `meta keywords`。

Google 从 2009 年起就不用它做排名，官方文档至今把它列为 **unsupported**。Bing 基本也不靠它。

它顶多给你自己看「这页想打哪些词」。真正该做的是 title / H1 / 正文里出现这些词，用独立 URL 覆盖对应问题，再用内部链接把这些页面连起来。不要为了 keywords 堆同义词。

### 4. robots meta

```html
<meta name="robots" content="index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1" />
```

| 指令 | 含义 |
| --- | --- |
| `index` | 允许收录这一页 |
| `follow` | 允许跟踪这一页上的链接 |
| `max-image-preview:large` | 允许搜索结果用大图预览 |
| `max-snippet:-1` | 不限制文字摘要长度 |
| `max-video-preview:-1` | 不限制视频预览时长 |

公开页可以这么写。这些页不行：

- 登录后的 app：`noindex, nofollow`
- 预发环境、staging：整站 `noindex`，并且不要放进 sitemap
- 感谢页、筛选参数页、打印页：通常 `noindex`
- 不存在的路径：HTTP `404`，并且带 `noindex`

`robots.txt` 管「能不能抓」，meta robots 管「抓了能不能进索引」。想从搜索结果里拿掉一页，用 `noindex`，不要只靠 `Disallow`。

### 5. Canonical

```html
<link rel="canonical" href="https://example.com/pricing" />
```

- 用绝对 HTTPS 地址
- 每个可收录页都放 **指向自己的 canonical**
- `www` / 非 `www`、带不带尾斜杠、`http` / `https` 只能留一种，其余 301/308 到 canonical
- UTM、ref、语言参数页，canonical 指回干净 URL
- 不要把 canonical 指到带 `#` 的地址

筛选如果只是同一份文档的时间窗、排序（例如 `?range=7d`），不要单独做 canonical。筛选如果已经构成一篇新文档（例如「AI 资讯」和「全部资讯」），应该是独立路径，而不是 query。

不要这样写：

```html
<link rel="canonical" href="https://example.com/#/pricing" />
<link rel="canonical" href="/pricing" />
<link rel="canonical" href="http://example.com/pricing?utm_source=twitter" />
```

要这样写：

```html
<link rel="canonical" href="https://example.com/pricing" />
```

旧 query 迁到干净路径时，用 308：

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

- **OG 图固定 1200×630 PNG/JPG**。Facebook、LinkedIn、Slack、iMessage、X 通用这个尺寸。
- **每页最好有自己的图**，至少首页、核心功能页、每篇长文不要共用一张无字海报。全站共用一张 `og.png` 是还没做完，不是范本。
- `og:url` 必须和 canonical 一致。
- 文章页用 `og:type=article`，并补 `article:published_time`。
- Twitter/X 会回退到 OG，但显式写 `twitter:card=summary_large_image` 更稳。
- **有官方 X 账号再写 `twitter:site`。** 填一个 404 账号比不填更糟。

验证：

- [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/)
- X Card Validator

改图之后这些工具都要重新 scrape，否则会一直显示缓存。

海外用户大量从 Slack、X、LinkedIn、邮件转发进来。卡片图和标题不对，点击就没了。

---

## 七、JSON-LD

用 Google 推荐的写法：一个 `<script type="application/ld+json">`，里面用 `@graph` 把多个实体串起来。

产品站常见：

| 类型 | 作用 | 放哪 |
| --- | --- | --- |
| `Organization` | 站点是谁、logo、社交账号 | 每个可收录页都可以引用 |
| `WebSite` | 站点名、语言 | 首页，内页用 `@id` 引用 |
| `SoftwareApplication` | 产品、价格 | 首页或产品页；价格必须和定价页可见内容一致 |
| `FAQPage` | 问答 | 页面上真能看到这些问题的那一页 |
| `WebApplication` | 浏览器里用的工具 | 工具页；免费就写 `"0"` |

聚合 / 目录站把 `SoftwareApplication` 换成 `DataCatalog` / `CollectionPage`。不要给门户本身报一个软件价格。

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

其他对象用 `"publisher": { "@id": "https://example.com/#organization" }` 引用，不要把站点信息在每个页面复制一遍。

`sameAs` 只填能打开的账号。没有 X、没有 LinkedIn，就不写。

### 2. ItemList 不要指向本站薄页

如果你在聚合别人的内容，列表里的 `url` 应该是原文：

```json
{
  "@type": "ListItem",
  "position": 1,
  "name": "Copilot code review: An improved review experience",
  "url": "https://github.blog/changelog/2026-09-18-copilot-code-review-an-improved-review-experience"
}
```

不要做成 `https://example.com/news/某个id/`。

聚合站常见做法是：每条 RSS 做一个本站文章页。结果是大量和原文重复的薄页，canonical 还说不清楚，Google 会当成采集站。本站该收录的是主题页、来源目录、自己写的评测和文档。

### 3. FAQPage

- Google 从 2023 年起就把 FAQ 富结果限制在少数权威站点
- 2026 年 5 月起，FAQ 富结果不再在 Google 搜索里展示
- **FAQPage 这个 schema 类型本身没有被废**。用了但不产生富结果，不会因此扣分
- 对 Bing、对 AI 抓取、对「把问答抽成标准答案」，FAQ 标记仍然值得留

前提：**页面上必须真的看得到这些问题和答案。** 不要只在 JSON-LD 里写 FAQ，正文没有。那属于误导性标记。

FAQ 放在用户会来找答案的那一页。产品站可以放首页或 `/help/`。数据门户的首页如果是数据和入口，FAQ 放到 `/about/`，不要塞进首屏。

JSON-LD 里的问题和页面上的标题必须是同一份文案。问题用用户会搜的原话。答案开头先给结论，再补细节。例如：

1. 这是什么？
2. 怎么安装？
3. 多少钱？
4. 数据从哪来？（如果你做的是数据产品）

### 4. 硬规则

- 写在初始 HTML 里，不要等 JavaScript 执行完再插入。社交爬虫和不少 AI 爬虫不跑 JS
- 标记必须和可见内容一致
- 用 [Rich Results Test](https://search.google.com/test/rich-results) 和 [Schema Markup Validator](https://validator.schema.org/) 检查
- 内页不要复制整份首页 `@graph`。分类页用 `CollectionPage` + 面包屑，文档页用 `TechArticle` 或 `FAQPage`，工具页用 `WebApplication`
- 没有真实评价，就不写 `aggregateRating`。编评分是明确违规

---

## 八、robots.txt、sitemap.xml、llms.txt

`<head>` 解决「这一页怎么被理解」。这三个文件解决「整站怎么被发现」。

用户口头上常说 `llm.txt`。业界约定文件名是 **`llms.txt`**（两个 s），规范在 [llmstxt.org](https://llmstxt.org/)。只放 `/llm.txt`，主流 agent 不会按约定去读。

### 1. robots.txt

位置只能是站点根：`https://example.com/robots.txt`。

```txt
User-agent: *
Allow: /
Disallow: /account/
Disallow: /admin/
Disallow: /auth/
Disallow: /api/

Sitemap: https://example.com/sitemap-index.xml
```

- `robots.txt` **不能阻止收录**，只能阻止抓取。不想进索引，用 `noindex`
- 不要 `Disallow: /_astro/`、`/_next/` 或挡住 JS/CSS，Google 需要它们渲染页面
- `Sitemap:` 行用绝对 URL
- 文件放根路径，UTF-8，不要做成 HTML 错误页
- AI 爬虫如果要单独策略，按 user-agent 分组写。默认开放对 GEO 更有利
- **`Sitemap:` 里列出的文件必须能打开。** 模块还没就绪就写上 `sitemap-projects.xml`，文件 404，比少写一行更糟

有的站点把整站 `Disallow: /`，Google 完全不抓，再漂亮的 title 也没用。

对照：<https://craftsail.com/robots.txt>

### 2. sitemap.xml

把希望被收录的 URL 清单交给搜索引擎。它不保证收录，但能加快发现，尤其是新站、文档深、外链少的站点。

大站用 sitemap index，再拆成多个 sitemap 文件。Help 稳定、量小，可以和营销页放一起。Blog 日更、量大，单独一个 `sitemap-blog.xml`，`lastmod` 用文章真实发布时间。

```text
https://example.com/
https://example.com/pricing
https://example.com/help/getting-started/what-is-acme
https://example.com/help/getting-started/install
https://example.com/blog/2026-09-19-signups
https://example.com/about
```

不要放 App、登录墙、带 `?category=` 的筛选页、会随快照消失的 ID。Blog 每发一篇就进 sitemap，不要等周更。

Google 现行文档：

- 只放 **canonical、可收录、返回 200** 的 URL
- 不要放 `noindex`、重定向、404、登录墙、带 `#` 的地址
- 用绝对 HTTPS URL
- 单文件上限 50,000 条或 50MB（未压缩）
- `lastmod` 只有在真实准确时才写；没有就不写
- `<priority>` 和 `<changefreq>` Google 会忽略，不必花时间调
- 提交方式：`robots.txt` 里写 `Sitemap:`，同时在 Google Search Console 提交

会随时间增减的条目可以进 sitemap，但不要写进 `llms.txt`。目录里出现大量 404，比没有目录更糟。

对照：<https://craftsail.com/sitemap-index.xml>

### 3. llms.txt

`llms.txt` 是 2024 年由 Jeremy Howard / Answer.AI 提出的约定，2026 年 8 月更新到 v2。它是一份 Markdown 目录，告诉 agent：这个站点是什么、先读哪些页。

它 **不是** W3C 标准，也 **没有拦截能力**。Google 明确说过：Search、AI Overviews、AI Mode **不会用** `llms.txt` 做排名。Ahrefs 2026 年对 13.7 万个域名的统计里，28% 的站点有这个文件，但 97% 从未被请求；真去读的，更多是编程 agent 和审计工具，不是 ChatGPT / Perplexity 的回答引擎。

所以：

- 对 Google SEO：几乎无直接作用
- 对 Cursor、Claude Code、文档问答、自定义 agent：有用
- 成本很低，值得做
- 不能代替 `robots.txt`、sitemap 和正经 HTML

结构大致是：H1 站点名、一段说明、`Start here` 列稳定入口、后面按主题分组，最后 `Optional`。

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

只列稳定 URL。不列会消失的 ID。链接必须是真实可打开的地址，不要链到 hash 路由。不要把 `Disallow` 写进 `llms.txt`，它没有 robots 语义。

没有独立文档页，目录里列再多链接也没用。文档站还可以同时提供每页的 Markdown 版。做到目录这一层，没有 `llms-full.txt`，也够用。

Stripe Docs、Anthropic、Cloudflare、Mintlify 托管的文档站已经在用。对照：<https://craftsail.com/llms.txt>

---

## 九、点击必须是 `<a href>`

搜索引擎发现新页的主路径，是跟踪 HTML 里的 `<a href="真实地址">`。

Google 官方 [JavaScript SEO](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics) 写得很直接：用标准的 `<a href>` 指向独立 URL。不要用 `onclick` 当导航，也不要用 `#/path` 当页面地址。

### 1. 爬虫不会「点击」你的按钮

这些写法，人能用，机器基本当没看见，或者当成当前页：

```html
<div onclick="go('/pricing')">Pricing</div>
<button onclick="router.push('/help')">Help</button>
<a href="#" onclick="openPage('install')">Install</a>
<div class="nav-item" data-href="/pricing">Pricing</div>
```

筛选、Tab、看起来像按钮的入口，底层也必须是链接：

```html
<nav>
  <a href="/help">Help</a>
  <a href="/help/install/macos">Install</a>
  <a href="/blog/">Blog</a>
  <a href="/pricing">Pricing</a>
</nav>
```

关 JS、用 `curl` 打开那个 URL，源码里要有 title、H1、分类链接。做不到这一点，后面的 JSON-LD 都是装饰。

收藏、刷新、加载更多、提交表单，仍用 `<button>`。那些不是文档导航。

### 2. 前端框架也要最终渲染出 `<a href>`

Astro / React / Vue / Next 的 `<a>`、`<Link>`、`<router-link>` 只要最后输出真实 `href`，就是合格的。

```jsx
// 可以：最终 HTML 是 <a href="/pricing">
<Link href="/pricing">Pricing</Link>

// 不行：最终是 <div> 或 href="#"
<div onClick={() => router.push('/pricing')}>Pricing</div>
```

验收：浏览器里「查看网页源代码」（不是 Elements 面板），搜这些 `href`。如果源码里没有，说明它们是 JS 跑完才出现的。Google 也许能渲染，Bing、很多 AI 爬虫、社交爬虫往往不能。

### 3. 锚点 `#` 只用来跳到同一页的某一段

```html
<!-- 可以：同一页里跳到章节 -->
<a href="/help/install/macos#requirements">Requirements</a>

<!-- 不行：把整页内容挂在 hash 上 -->
<a href="/#/pricing">Pricing</a>
<a href="/help#install">Install</a>
```

`#` 后面的内容，搜索引擎默认当成同一 URL 的片段。`https://example.com/#/pricing` 和首页在收录上经常被看成同一页。

---

## 十、每个标准答案都要有独立 URL

### 1. 独立路径，不要靠 query 或 hash 冒充文档

人能用的筛选，爬虫打开列表页时，源码里往往看不到分类文档：

```text
/help?page=install
/blog?category=seo
/#/pricing
```

该改成：

```text
/help/install/macos
/blog/2026-09-19-signups
/pricing
```

旧 query 地址 308 到干净路径。带 `q`、临时筛选的页不 301，加 `noindex`，canonical 指回路径。

| 错误 URL | 搜索引擎看到的 | 正确 URL |
| --- | --- | --- |
| `/#/pricing` | 首页 | `/pricing` |
| `/blog#hello` | `/blog` | `/blog/hello` |
| `/help#install` | `/help` | `/help/getting-started/install` |
| `/?page=pricing` | 带参数的首页 | `/pricing` |

Google 早在 2015 年就废弃了 `#!` AJAX 抓取方案。Hash 路由只适合不需要收录的内部工具。

### 2. 每个内容页都要能直接打开

- 把这个 URL 贴到无痕窗口，不点任何按钮，正文就在 HTML 里
- 刷新、后退、从 Google 点进来，都还是这一页
- 服务器对这篇文档返回 `200`，对不存在的文档返回真正的 `404`，不要全部回 `index.html`

SPA 常见的 soft 404 是：随便输入一个地址，HTTP 仍是 200，页面上用 JS 写「Not found」。搜索引擎会把大量空页收进去，浪费抓取配额。不存在的路径应返回 HTTP 404，并带 `x-robots-tag: noindex`。

### 3. 看起来可以像 SPA，但必须先吐出 HTML

| 部分 | 建议 |
| --- | --- |
| 首页、定价、Help、Blog、关于 | 静态生成或 SSR，构建期或请求时吐出完整 HTML |
| 列表筛选 | 视觉可以像按钮，底层是独立路径 |
| 真正的产品 App | 可以是 SPA，并 `noindex` |
| 浏览器里才有意义的工具 | 说明文字进 HTML，交互可以靠 JS |

框架怎么选见第十一节。Help 用静态生成或 SSR，sidebar 的链接写在源码里，见第十三节。

### 4. URL

好的路径像用户会搜的问题，也像导航：

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

- 全小写、短横线、不要空格和驼峰
- 一层一个主题，不要 `/help/index.html#/install`
- 中英文站用目录或子域分开，并配 `hreflang`。只有一种语言，就不要假装做了双语
- 改 URL 必须 301/308 到新地址，并更新 sitemap、llms.txt、导航

---

## 十一、框架：内容站用 Astro，不用 Next.js

前面每一节都在要求同一件事：**关掉 JS 打开，HTML 里什么都有**。另一件事是快。Core Web Vitals 进排序，海外用户离你的服务器远，首屏多下发的每一百 KB JS 都是真的慢。

营销页、Blog、Help 本质是文档。给文档站选框架，先看两点：默认吐出什么 HTML，默认下发多少 JS。

craftsail.com 用的是 Astro，第四节那行 `/_astro/...woff2` 就是它的构建产物。

### 1. 对比

| 维度 | Astro | Next.js |
| --- | --- | --- |
| 默认下发的 JS | 0。只有标了 `client:*` 的组件才下发 JS（island） | React 运行时 + 路由，每页都要水合。Server Components 能少发组件代码，运行时省不掉 |
| 首屏速度 | 纯 HTML + CSS，LCP、INP 不用调就容易达标 | 能做到，但要持续压 bundle、盯住 `'use client'` 的边界 |
| 默认渲染 | 构建期静态生成，按页开 SSR | SSR / SSG / ISR / RSC 都有，模型复杂 |
| 内容管理 | Content Collections：Markdown / MDX + schema 校验 | 要自己接 MDX 或第三方内容库 |
| Blog feed 流 | `paginate()` 出分页 URL，`@astrojs/rss` 出 RSS，都是官方的 | 自己写分页路由和 RSS route handler |
| Help sidebar | Starlight 官方文档主题，整树 sidebar 开箱 | Nextra、Fumadocs 等第三方 |
| sitemap | `@astrojs/sitemap` | `app/sitemap.ts` 内置 |
| 部署 | 产物是静态文件，任何 CDN、Cloudflare Pages、GitHub Pages、Nginx 都行 | 完整能力在 Vercel 最顺；`output: 'export'` 纯静态导出会丢掉 ISR、middleware、默认图片优化等 |
| 交互重的 App | 弱。island 之间共享状态要自己处理 | 强项 |
| 生态、招人 | 小一些，但可以在 island 里直接用 React / Vue / Svelte 组件 | React 生态最大 |

### 2. Astro 的短板

- **跨页跳转是整页加载**。开 `prefetch`，或用 `<ClientRouter />` 做页面过渡，体感能补回大半
- **island 各自独立**。多个交互组件共享状态，要用 nanostores 之类自己接
- **全静态时，发文要重新构建部署**。日更 3 篇、几千篇的规模没问题；上万篇再考虑按需 SSR 或拆分构建
- **登录后的复杂 App 不是它的强项**

### 3. Next.js 放在内容站上的问题

- 纯文字页也要下发 React 运行时并水合，这部分 JS 对爬虫和读者都没用
- 渲染、缓存模型多，一个 `'use client'` 放错位置，整棵子树都变成客户端组件
- 离开 Vercel 自托管，ISR、图片优化、缓存都要自己兜
- Blog + Help 用不上它的大部分能力，成本却要全付

Next.js 不是做不好 SEO，是要花额外的功夫才能做到 Astro 的默认值。

### 4. 怎么分

| 部分 | 用什么 |
| --- | --- |
| 首页、定价、关于、Blog、Help | Astro 静态生成；Help 用 Starlight 或自己写 sidebar 组件 |
| 页面上的少量交互：搜索框、订阅表单、图表 hover | Astro 里挂 island：`client:idle`、`client:visible` |
| 登录后的 App、编辑器、控制台 | React、Next、任何 SPA 都行；放 `/app/` 或 `app.` 子域，`noindex` |

给爬虫和新用户看的部分用 Astro，给登录用户用的部分再谈 React。

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

- `site` 必须写，不写 sitemap 和 RSS 拼不出绝对地址
- 尾斜杠只留一种（`trailingSlash`），和部署平台的行为、canonical、sitemap 对齐
- 图片用 `astro:assets` 的 `<Image />`，自动带 `width` / `height`，不产生 CLS

### 6. 验收速度

- `curl -s https://example.com/blog/ | grep -c '<script'`：内容页应该接近 0
- PageSpeed Insights 移动端跑三页：首页、一篇 Blog、一篇 Help
- 目标按 Google 的「良好」线：LCP < 2.5s，INP < 200ms，CLS < 0.1

---

## 十二、Blog：用 feed 流让搜索引擎持续来抓

首页、定价、Help 改得少。搜索引擎要不要经常回访，看你有没有稳定的新文档。Blog 干这个。

节奏可以定死：**每天 3 篇**，每篇独立 URL，当天进 sitemap，RSS 跟着更新。爬虫发现这个站每天都有新 HTML，就会提高抓取频率。3 篇是给管道的配额，不是凑字数。同一段话换标题发三次，等于在生产薄页。

### 1. `/blog/` 做成 feed 流

按发布时间倒序排成一条流，最新的在最上面。每天 3 篇进来，`/blog/` 这一页的 HTML 每天都在变。爬虫回访这一页，一次就拿到当天所有新链接。

流里每一条是卡片，不是全文：

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

- 卡片标题是 `<a href>`，指向文章独立 URL
- 卡片只放标题、日期、类型、一两句摘要、缩略图。全文只在文章页，不要列表页和文章页各一份
- `<time datetime>` 写真实发布时间
- 分类是独立路径 `/blog/type/data/`，不是 `?type=data`，也不是前端 Tab 过滤
- 首屏第一张缩略图不要 `loading="lazy"`，它往往就是 LCP；后面的可以 lazy
- 首页放最新 5–10 条同样的卡片，链到 `/blog/`

### 2. 可以无限滚动，底下必须是分页 URL

feed 流最常见的坑：只做「滚到底自动加载」。爬虫不滚动，也不点「加载更多」，只看得到第一屏 20 篇，更早的文章只能靠 sitemap。

Google 对无限滚动的建议是：背后要有一组能直接打开的分页 URL。

```text
/blog/      最新 20 篇
/blog/2     第 21–40 篇
/blog/3     ...
```

- 每一页 HTML 里都有指向上一页 / 下一页的 `<a href>`
- 「加载更多」底子是 `<a href="/blog/2">`。JS 可以接管它，把下一页拼到当前列表，再用 `history.pushState` 把地址改成 `/blog/2`；关了 JS，它就是一个普通链接
- 每个分页页 canonical 指向自己，不要全部指回 `/blog/`
- 分页页 title 带页码：`Blog — Page 2 — Acme`
- 分页页不必进 sitemap，文章页必须进

### 3. 有数据就上图表

纯文字日报，看起来像模板。数字画成图，正文里再放能读的数。爬虫不执行 Canvas。图必须在初始 HTML 里：

- 内联 SVG，或 `<img>` + 写清数字的 `alt`
- 下面配 `<table>` 或一段结论，图挂了文字还在
- `<figure>` + `<figcaption>`，caption 用普通人能读的句子，不要只写「图 1」

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

有数据管道就从管道出这 3 篇，不要手写灌水。没数据的那篇就别发。

### 4. 结构要能区分

三篇不要套同一套「引言 — 三点 — 总结」。类型不同，HTML 骨架就不同，爬虫和人才能看出这是三份文档，不是一个模板填了三次。

| 类型 | URL 例子 | 骨架 |
| --- | --- | --- |
| 数据 / 日报 | `/blog/2026-09-19-signups` | 一句话结论 → 图 → 数字表 → 比昨天多了什么 |
| 教程 | `/blog/install-cli-on-macos` | 步骤用 `h2`，命令进 `<pre>` |
| 对比 | `/blog/acme-vs-foo-pricing` | 对照表，列是产品，行是项 |

`og:type=article`，补 `article:published_time`。JSON-LD 用 `BlogPosting`，`image` 指向那张图。列表页 `/blog/` 是 `CollectionPage`，不要把全部正文堆进列表。

### 5. 发现路径和订阅

```text
/blog/                      feed 第一页
/blog/2                     feed 分页
/blog/type/data/            分类 feed
/blog/2026-09-19-signups    一篇
/rss.xml                    RSS，全文或摘要均可
/sitemap-blog.xml           只放已发布、200、canonical 的文章
```

- 每页 `<head>` 都带 `<link rel="alternate" type="application/rss+xml" href="/rss.xml">`
- RSS 放最新 20–50 篇，`pubDate` 是真实发布时间，`link` 是 canonical URL
- 页面上的 feed 流和 RSS 用同一份数据、同一个排序，不要两边对不上

页头、页脚、相邻文章、相关 Help 都用 `<a href>` 指过来。新文章只出现在首页 JS 里、不进 sitemap、没有内链，爬虫来得慢。

转载、RSS 搬运、把别人的 changelog 改写一遍，都不进 `/blog/`。那些该链到原文。

### 6. 用 Astro 写 feed 流

文章放在 Content Collection 里，排序抽成一个函数，feed 页和 RSS 共用：

```ts
// src/lib/posts.ts
import { getCollection } from 'astro:content';

export async function getPosts() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  return posts.sort((a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf());
}
```

`[...page].astro` 让第一页落在 `/blog/`，后面是 `/blog/2`、`/blog/3`，构建期全部生成静态 HTML：

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

文章 slug 用日期或英文短语（`2026-09-19-signups`），不要用纯数字，免得和分页 `/blog/2` 撞路径。

---

## 十三、Help 要有侧栏：这是在给搜索引擎铺内链

用户能从侧栏跳，爬虫也能从侧栏发现。Help 做成「一篇正文 + 左侧一排 `<a href>`」，是文档站最划算的 SEO 结构。

没有侧栏的 Help，往往是一个单页，或者每次只渲染当前篇。爬虫进来只能看到这一篇，深处文档要靠 sitemap 碰运气。

有侧栏之后，每一篇文档的 HTML 里都会带上几十个指向兄弟页面的真实链接：

- 新文档更容易被发现
- 主题簇（getting started、AI、权限、安装）被链接成一组
- 锚文本就是关键词：`Install on Kubernetes`、`Bring your own model`

这就是为什么 Stripe、Cloudflare、MDN、GitHub Docs 全都把侧栏做成真链接，而不是折叠后的 JS 菜单。

### 1. 侧栏必须是 HTML 链接，不能是点开才加载的树

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

折叠可以用 CSS / JS，但折叠前源码里就要有这些 `<a>`。不要等点击「AI」分组后才把子链接插入 DOM。`<details open>` 可以，点击后再 `fetch` 子树不行。

当前篇用 `<span>` 或 `aria-current="page"`，其余全部是链接。锚文本写人会搜的词，不要全写 `Click here`。

```bash
curl -sL https://example.com/help/getting-started/install | grep 'href="/help/'
```

源码里应出现 What is Acme、Bring your own model、Audit logs 这些兄弟链接，不只是当前这篇。Google 也许能渲染客户端 sidebar，Bing 和不少 AI 爬虫不能。

优先 Astro（Starlight 或自己写组件，见本节第 5 小节）；Docusaurus、VitePress、Mintlify 也行。它们默认就是「一篇正文 + 左侧一排真链接」。不要用纯客户端 React SPA 硬做 Help。

### 2. 一篇文档里再放相关链接和面包屑

侧栏是纵向发现，正文里的相关链接是横向加分。

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

面包屑配 `BreadcrumbList` JSON-LD。文档层级深的站点，搜索结果里有机会出现 `Help > Getting started > Install`。

### 3. 一个 URL 回答一个问题

文档站不要写成产品手册目录。

| 弱页面 | 更有搜索价值的页面 |
| --- | --- |
| `/help/overview` | `/help/getting-started/what-is-acme` |
| `/help/ai` | `/help/ai/byok`、`/help/ai/agents` |
| `/help/faq` 一页装 40 问 | 高频问题各写一篇；入口页 FAQ 只留 6–8 个链接 |
| `/help/security` | `/help/security/permissions`、`/help/security/audit-logs` |

每篇固定结构：

1. H1 就是问题或任务
2. 前两段直接给结论
3. 步骤 / 注意 / 限制
4. 相关链接
5. 最后更新时间

这就是标准答案页。Help 侧栏把这些页连成网，搜索和 AI 才找得到、愿意引用。GEO 会另写。

### 4. 可以对照的文档站

打开这些页面的「查看源代码」，都能看到侧栏里成排的 `href`：

- https://docs.stripe.com/payments/checkout
- https://developers.cloudflare.com/workers/get-started/guide/
- https://docs.github.com/en/get-started/start-your-journey/about-github-and-git
- https://docusaurus.io/docs/seo
- https://mintlify.com/docs/ai/llmstxt

不要对照的：把帮助中心做成一个 iframe、一个 SPA、一个「搜索框 + 无链接结果」的知识库。那类系统对人也许能搜，对爬虫是黑盒。

### 5. 用 Astro 写 Help sidebar

**省事：Starlight。** Astro 官方的文档主题。sidebar、上一篇 / 下一篇、页内目录、Pagefind 站内搜索都开箱；分组折叠用的是 `<details>`，整树链接在源码里。面包屑默认没有，要自己覆盖组件或装社区插件。

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

文档放 `src/content/docs/help/getting-started/install.md`，URL 就是 `/help/getting-started/install`。

**要和主站同一套样式：自己写 sidebar 组件。** 它在构建期运行，输出就是整树 `<a>`，没有一行客户端 JS：

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

两种做法都用第 1 小节那条 `curl | grep 'href="/help/'` 验收。

---

## 十四、导航、页脚

页头、页脚也要是真实 `<a href>`。Help 的侧栏、面包屑、文末 Next 见上一节，不要只在首页放一个「Docs」按钮。

页脚把 Blog、Help、定价都链上。营销页之间同样用真链接互指，不要只靠 JS 路由。

---

## 十五、按页面类型写静态信息

首页那段 `<head>` 只能当首页用。内页要改 title、description、canonical、og:url、JSON-LD。

产品站：

| 页面 | title | JSON-LD |
| --- | --- | --- |
| `/` | 品牌 + 品类 | Organization + WebSite + SoftwareApplication |
| `/pricing` | `Pricing — {品牌}` | 价格在正文里，和 schema 一致 |
| `/help/{path}` | 这一页的问题 | TechArticle / FAQPage |
| `/blog/{slug}` | 文章标题 | BlogPosting；`og:type=article`；有图就写 `image` |
| `/about` | `About {品牌}` | WebPage；需要时加 FAQPage |
| `/app`、`/account` | 不收录 | `noindex`，不进 sitemap |

Organization 用稳定 `@id`：`https://example.com/#organization`。内页用引用，不要把首页整份 `@graph` 复制过去。

- 首页：`SoftwareApplication` 的价格必须和定价页可见内容一致
- `/pricing`：价格写在正文里，不只写在按钮上
- `/blog/{slug}`、`/help/{path}`：一篇一个 URL，进 sitemap。Help 稳定入口写进 `llms.txt`；Blog 只把常青文写进去，日报不必进 `llms.txt`
- App：`noindex`，不要放进 sitemap

聚合站把 `SoftwareApplication` 换成目录类型，ItemList 指向原文，FAQ 不要塞进数据首屏。

---

## 十六、不要做的事

- 不为每条 RSS / 转载做本站文章页；Blog 日更必须带本站数据或本站步骤
- 图表只画在 Canvas / 客户端 chart 里，源码里没有数字
- 三篇 Blog 共用一个 HTML 骨架，只换标题
- `/blog/` 只做无限滚动，没有能直接打开的分页 URL
- 分页页 canonical 全部指回 `/blog/`
- Help sidebar 靠 JS 点击后才插入链接
- Help 做成 iframe、纯 SPA、或「搜索框 + 无链接结果」的知识库
- `/help/faq` 一页塞几十问，不给高频问题独立 URL
- 没有独立内容，就不新建 Docusaurus / VitePress / Starlight Help
- 营销页、Blog、Help 用整页水合的 React 框架，纯文字页也下发几百 KB JS
- 不写 `meta keywords`，不编评分
- 只有一种语言，就不做 `hreflang`
- 不把 FAQ 硬塞进不该回答问题的页面
- 不把会过期的 ID 写进 `llms.txt`
- sitemap 文件还 404，就不要写进 robots

---

## 十七、落地清单


### 框架与速度

- [ ] 营销页、Blog、Help 用 Astro 静态生成；登录后的 App 单独部署并 `noindex`
- [ ] 内容页 `curl` 下来几乎没有 `<script>`，交互组件用 `client:idle` / `client:visible`
- [ ] PageSpeed Insights 移动端：LCP < 2.5s，INP < 200ms，CLS < 0.1

### 站点根文件

- [ ] `https://example.com/robots.txt` 可打开，含 `Sitemap:` 行
- [ ] `Sitemap:` 指向的文件都能打开，只包含 200、canonical、可收录 URL
- [ ] `https://example.com/llms.txt` 是 Markdown 目录，链接都是真实、稳定的页面
- [ ] 没有把预发环境、App、后台、会消失的 ID 放进 sitemap / `llms.txt`

### 每个可收录页的静态信息

- [ ] 独立 `<title>`
- [ ] 独立 `meta description`
- [ ] 自指 `canonical`
- [ ] `og:title` / `og:description` / `og:url` / `og:image`（1200×630）
- [ ] `twitter:card = summary_large_image`
- [ ] 需要的 JSON-LD，且与可见内容一致
- [ ] 这些标签在「查看源代码」里就有，不是 JS 后插入

### Blog

- [ ] `/blog/` 是倒序 feed 流，卡片标题是 `<a href>`，只放摘要不放全文
- [ ] feed 有分页 URL（`/blog/2`），每页有上一页 / 下一页 `<a href>`，canonical 指向自己
- [ ] 分类是独立路径（`/blog/type/data/`），不是 query 或前端 Tab
- [ ] feed 流和 RSS 同一份数据、同一个排序；每页 `<head>` 有 RSS `alternate`
- [ ] `/blog/{slug}` 一篇一个 URL，`og:type=article`
- [ ] 每天固定 3 篇则 3 个新 URL，当天进 `sitemap-blog.xml` 和 RSS
- [ ] 有数据的篇：图在 HTML 里（SVG 或 `<img>`），旁边有表或可读数字
- [ ] 数据篇 / 教程 / 对比不是同一套 DOM
- [ ] 转载不进 `/blog/`

### Help

- [ ] `/help/{path}` 一篇一个 URL，一个问题，没有 `#/`
- [ ] 关 JS 打开任一 Help 页，源码里能搜到 sidebar 上其它文档的 `href="/help/`
- [ ] 折叠用 CSS / `<details>`，不靠点击再插入链接
- [ ] 锚文本是问题本身：`Install on Kubernetes`、`Bring your own model`
- [ ] 每篇有面包屑、文末相关链接；面包屑配 `BreadcrumbList`
- [ ] H1 是问题或任务，前两段先给结论，页脚有最后更新时间
- [ ] 高频 FAQ 各写一篇，不要一页装 40 问

### URL 与链接

- [ ] 主题、关于、Help、定价、对比、Blog 都是独立路径，没有 `#/`
- [ ] 导航、卡片、筛选、页脚、sidebar、按钮式入口全部是 `<a href>`
- [ ] 源码里能搜到这些 href
- [ ] 不存在的文档返回 HTTP 404
- [ ] 改版旧地址 301/308 到新地址

### 提交与验收

- [ ] Google Search Console 验证域名，提交 sitemap
- [ ] URL Inspection 看「已抓取的页面」是不是完整 HTML
- [ ] Rich Results Test 检查 Organization / FAQPage
- [ ] 分享一次到 Slack / X / LinkedIn，确认卡片图和标题
- [ ] 用无 JS 环境打开一个内页，正文和导航链接仍在

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
