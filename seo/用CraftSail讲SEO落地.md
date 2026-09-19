# 用 CraftSail 讲 SEO 落地

搜索引擎和 AI 抓的是 HTML：能不能发现页面、读懂页面、把页面当成独立文档收录。

样本是 [造物局 CraftSail](https://craftsail.com)。造物局是 AI / 开发者趋势数据门户，聚合公开资讯、本站热点、研究线索和项目发现，不是 SaaS 官网。不要拿「定价页 + Help」的模板往上套。

要做的是：

1. 每个可收录页面的静态信息写对
2. 站点根目录放好 `robots.txt`、`sitemap.xml`、`llms.txt`
3. 可点击入口都是真实的 `<a href>`，指向独立 URL
4. 每个标准答案都有自己的地址

---

## 一、CraftSail 要收录什么

产品站要收录的是：这是什么、多少钱、怎么装、怎么用。

数据门户要收录的是：这是什么、数据从哪来、这个主题现在发生了什么、这个项目为什么值得看。

把 Pricing、Help、Blog 原样搬过来，会做出一批空壳页。

| 方法里的对象 | CraftSail 对应 | 做法 |
| --- | --- | --- |
| 首页 | `/` 数据门户 | 品牌 + 品类 title；Organization / WebSite / DataCatalog |
| 定价页 | 无 | 不写 SoftwareApplication 报价 |
| Blog 文章 | **不做** `/news/{id}/` | 资讯 canonical 是原文；本站独特内容是线索、热点、来源、项目 |
| Help 文档 | `/about/`、`/news/sources/`、热度说明、`/privacy/` | 可见 FAQ + 真链接互指，不新建文档站 |
| App | `/account/`、`/admin/`、`/auth/`、`/submit/`、`/api/` | `noindex`，并写入 `robots.txt` |
| 侧栏内链 | 导航、页脚、面包屑、主题链接 | 筛选控件改成 `<a href>` |

有本站自己的内容，就给独立 URL。没有，就链到原文，不要做薄页。

你做的如果是产品站，同一条仍然成立。定价、安装、对比、文档才是你的标准答案。不要为了「多收录」去复制别人已经写过的资讯。

---

## 二、首页 `<head>` 在干什么

首页 `<head>` 同时给几类读取者看：

| 读取者 | 它看什么 | 你要给什么 |
| --- | --- | --- |
| 浏览器 / 手机系统 | 图标、主题色、字体 | favicon、apple-touch-icon、theme-color、font preload |
| Google / Bing | title、description、canonical、robots、正文、链接 | 可收录的独立 URL + 准确摘要 |
| 社交平台 | Open Graph、Twitter Card | 标题、描述、1200×630 图 |
| 搜索和 AI | JSON-LD、FAQ、清晰标题结构 | 实体、产品或目录、问答 |

CraftSail 首页这段是线上真实 HTML。用无痕窗口打开 [craftsail.com](https://craftsail.com)，「查看网页源代码」，就能对上。

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

- `/favicon.svg`、`/favicon-dark.svg`、`/apple-touch-icon.png` 都要真实存在，返回 `200`
- 文件名和 HTML 里写的路径必须一致
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
- 构建产物带哈希（CraftSail 用 Astro 打出来的 `manrope-latin-wght-normal.DHIcAJRg.woff2`），换字体文件时路径自己变，不必再手改 `?v=`。

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

CraftSail 首页用的是品类句：

```text
造物局 · CraftSail — AI 与开发者趋势数据门户
```

内页把主题放前面，品牌放后面：

```text
AI 动态 · 造物局
关于造物局 · 造物局
IP 查询与网络分流检测 · 造物局
```

- 每个 URL 必须有自己的 title，禁止全站同一句
- 大约 50–60 个英文字符，或中文 20–30 字，避免搜索结果被截断
- 主关键词靠前，品牌名可以放后面
- 首页讲品类，内页讲这一页的具体问题

| 页面 | 差的 title | CraftSail 实际 title |
| --- | --- | --- |
| 首页 | Welcome to CraftSail | 造物局 · CraftSail — AI 与开发者趋势数据门户 |
| 资讯分类 | News | AI 动态 · 造物局 |
| 关于 | About | 关于造物局 · 造物局 |
| 工具 | IP Tool | IP 查询与网络分流检测 · 造物局 |
| 热点 | Trends | 本站热点 · 造物局 |

Google 经常改写 title。写得越贴近页面真实 H1 和正文，被改写的概率越低。CraftSail 首页 H1 是「造物局」，title 里同时出现品牌、英文名和品类。

### 2. Description

Google 明确说过：meta description **不直接参与排名**，但会影响搜索结果摘要和点击率。写不好，Google 会自己从正文抽一段。

- 每页独一无二
- 英文大约 140–160 字符，中文大约 70–90 字
- 先说这一页是什么，再说和别人的差别
- 必须和页面可见正文一致，不要写页面上没有的承诺

CraftSail 没有全站复用首页那句。分类页会写明数据边界：

> AI 动态的近 24 小时公开资讯。来自本站订阅来源，标题链接直达原文，不是官方热搜。

这句话覆盖了时间窗、来源、点击去哪、它不是什么。数据门户很容易被理解成「官方热搜」，先把误解挡住。

### 3. Keywords 不用做

CraftSail **没有** `meta keywords`。

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
- CraftSail 的 404：HTTP `404`，并且带 `noindex`

`robots.txt` 管「能不能抓」，meta robots 管「抓了能不能进索引」。想从搜索结果里拿掉一页，用 `noindex`，不要只靠 `Disallow`。

### 5. Canonical

```html
<link rel="canonical" href="https://craftsail.com/" />
<link rel="canonical" href="https://craftsail.com/news/ai/" />
```

- 用绝对 HTTPS 地址
- 每个可收录页都放 **指向自己的 canonical**
- `www` / 非 `www`、带不带尾斜杠、`http` / `https` 只能留一种，其余 301/308 到 canonical
- UTM、ref、语言参数页，canonical 指回干净 URL
- 不要把 canonical 指到带 `#` 的地址

CraftSail 的路径统一带尾斜杠。`www.craftsail.com` 重定向到 `https://craftsail.com`。资讯分类从旧 query 迁到干净路径：

```text
/news/?category=ai   308 →  /news/ai/
```

`range=7d` 仍是查询参数。它改变的是同一份文档的时间窗，不是一篇新文档，所以不单独做 canonical。

不要这样写：

```html
<link rel="canonical" href="https://craftsail.com/#/news/ai" />
<link rel="canonical" href="/news/ai/" />
<link rel="canonical" href="http://craftsail.com/news/ai/?utm_source=twitter" />
```

要这样写：

```html
<link rel="canonical" href="https://craftsail.com/news/ai/" />
```

---

## 六、Open Graph 和 Twitter Card

```html
<meta property="og:type" content="website" />
<meta property="og:site_name" content="造物局 · CraftSail" />
<meta property="og:locale" content="zh_CN" />
<meta property="og:title" content="造物局 · CraftSail — AI 与开发者趋势数据门户" />
<meta property="og:description" content="看见正在发生的变化，找到值得继续研究的人、项目与机会。" />
<meta property="og:url" content="https://craftsail.com/" />
<meta property="og:image" content="https://craftsail.com/og.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="造物局 · CraftSail：AI 资讯与项目发现。" />
<meta name="twitter:card" content="summary_large_image" />
```

- **OG 图固定 1200×630 PNG/JPG**。Facebook、LinkedIn、Slack、iMessage、X 通用这个尺寸。
- **每页最好有自己的图**，至少首页、核心功能页、每篇长文不要共用一张无字海报。CraftSail 目前全站共用 `og.png`，这是还没做完的一层，不是范本。
- `og:url` 必须和 canonical 一致。
- 文章页用 `og:type=article`，并补 `article:published_time`。CraftSail 的线索页走这条。
- Twitter/X 会回退到 OG，但显式写 `twitter:card=summary_large_image` 更稳。
- **有官方 X 账号再写 `twitter:site`。** CraftSail 没有，所以没写。填一个 404 账号比不填更糟。

验证：

- [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/)
- X Card Validator

改图之后这些工具都要重新 scrape，否则会一直显示缓存。

海外用户大量从 Slack、X、LinkedIn、邮件转发进来。卡片图和标题不对，点击就没了。

---

## 七、JSON-LD

CraftSail 用 Google 推荐的写法：一个 `<script type="application/ld+json">`，里面用 `@graph` 把多个实体串起来。首页实际有四类：

| 类型 | 作用 | 放哪 |
| --- | --- | --- |
| `Organization` | 站点是谁、logo、GitHub | 每个可收录页都可以引用 |
| `WebSite` | 站点名、语言 | 首页，内页用 `@id` 引用 |
| `DataCatalog` | 这是一份数据目录，不是软件报价 | 首页 |
| `CollectionPage` | 这一页是一组条目 | 首页、资讯、热点、榜单 |

产品站常见的是 `SoftwareApplication` + 首页 FAQ。CraftSail 不是软件产品，首页也不承担问答，所以：

- 不用 `SoftwareApplication` 报价
- FAQ 放在 `/about/`，用 `FAQPage`
- 工具页 `/tools/ip/` 才用 `WebApplication`，价格写 `"0"`
- 项目详情有独立内容时，再用 `SoftwareApplication` 描述那个项目，而不是描述门户本身

### 1. 用 `@id` 把实体连起来

```json
{
  "@type": "Organization",
  "@id": "https://craftsail.com/#organization",
  "name": "造物局",
  "alternateName": "CraftSail",
  "url": "https://craftsail.com",
  "logo": "https://craftsail.com/logo.png",
  "sameAs": ["https://github.com/craftsail/craftsail"]
}
```

其他对象用 `"publisher": { "@id": "https://craftsail.com/#organization" }` 引用，不要把站点信息在每个页面复制一遍。

`sameAs` 只填能打开的账号。CraftSail 目前只有 GitHub。没有 X、没有 LinkedIn，就不写。

### 2. 首页 ItemList 指向原文，不指向本站薄页

CraftSail 首页 JSON-LD 里的资讯列表，`url` 是原文地址：

```json
{
  "@type": "ListItem",
  "position": 1,
  "name": "Copilot code review: An improved review experience",
  "url": "https://github.blog/changelog/2026-09-18-copilot-code-review-an-improved-review-experience"
}
```

不是 `https://craftsail.com/news/某个id/`。

聚合站常见做法是：每条 RSS 做一个本站文章页。结果是大量和原文重复的薄页，canonical 还说不清楚，Google 会当成采集站。

CraftSail 的做法：

- 资讯标题继续链到原文
- 本站收录的是主题页、来源目录、热点、线索、项目详情
- 搜索索引里的资讯条目，也指向原文

别人已经写过的内容，不要再做一版「本站转载页」去抢收录。英文世界对采集站更敏感。

### 3. FAQPage

- Google 从 2023 年起就把 FAQ 富结果限制在少数权威站点
- 2026 年 5 月起，FAQ 富结果不再在 Google 搜索里展示
- **FAQPage 这个 schema 类型本身没有被废**。用了但不产生富结果，不会因此扣分
- 对 Bing、对 AI 抓取、对「把问答抽成标准答案」，FAQ 标记仍然值得留

前提：**页面上必须真的看得到这些问题和答案。** 不要只在 JSON-LD 里写 FAQ，正文没有。那属于误导性标记。

CraftSail 把 FAQ 放在 `/about/`，不放首页。首页是数据门户，首屏应该是数据和入口。JSON-LD 里的问题和页面上的标题必须是同一份文案，例如：

1. 造物局是什么？
2. 资讯从哪里来？
3. 本站热点是官方热搜吗？
4. 数据多久更新一次？
5. 可以提交自己的项目吗？
6. 标题翻译可以代替原文吗？

问题用用户会搜的原话。答案开头先给结论，再补细节。

### 4. 硬规则

- 写在初始 HTML 里，不要等 JavaScript 执行完再插入。社交爬虫和不少 AI 爬虫不跑 JS
- 标记必须和可见内容一致
- 用 [Rich Results Test](https://search.google.com/test/rich-results) 和 [Schema Markup Validator](https://validator.schema.org/) 检查
- 内页不要复制整份首页 `@graph`。资讯分类页用 `CollectionPage` + 面包屑，关于页加重 `FAQPage`，工具页用 `WebApplication`
- 没有真实评价，就不写 `aggregateRating`。编评分是明确违规

---

## 八、robots.txt、sitemap.xml、llms.txt

`<head>` 解决「这一页怎么被理解」。这三个文件解决「整站怎么被发现」。

用户口头上常说 `llm.txt`。业界约定文件名是 **`llms.txt`**（两个 s），规范在 [llmstxt.org](https://llmstxt.org/)。只放 `/llm.txt`，主流 agent 不会按约定去读。

### 1. robots.txt

位置只能是站点根：`https://craftsail.com/robots.txt`。

CraftSail 的线上版本：

```txt
User-agent: *
Allow: /
Disallow: /catalog-shell/
Disallow: /account/
Disallow: /admin/
Disallow: /auth/
Disallow: /oauth/
Disallow: /go/
Disallow: /api/
Disallow: /submit/

Sitemap: https://craftsail.com/sitemap-index.xml
Sitemap: https://craftsail.com/sitemap-projects.xml
```

- `robots.txt` **不能阻止收录**，只能阻止抓取。不想进索引，用 `noindex`
- 不要 `Disallow: /_astro/` 或挡住 JS/CSS，Google 需要它们渲染页面
- `Sitemap:` 行用绝对 URL
- 文件放根路径，UTF-8，不要做成 HTML 错误页
- AI 爬虫如果要单独策略，按 user-agent 分组写。默认开放对 GEO 更有利
- **`Sitemap:` 里列出的文件必须能打开。** 项目模块还没稳定时，`sitemap-projects.xml` 会 404。列出打不开的文件，比少写一行更糟。模块没就绪，就不要提前写进 robots

有的站点把整站 `Disallow: /`，Google 完全不抓，再漂亮的 title 也没用。

### 2. sitemap.xml

把希望被收录的 URL 清单交给搜索引擎。它不保证收录，但能加快发现，尤其是新站、文档深、外链少的站点。

CraftSail 用 sitemap index：`https://craftsail.com/sitemap-index.xml`，再指向 `sitemap-0.xml`。当前收录的是稳定文档，例如：

```text
https://craftsail.com/
https://craftsail.com/about/
https://craftsail.com/news/
https://craftsail.com/news/ai/
https://craftsail.com/news/dev/
https://craftsail.com/news/launch/
https://craftsail.com/news/growth/
https://craftsail.com/news/twitter/
https://craftsail.com/news/sources/
https://craftsail.com/trends/
https://craftsail.com/rankings/
https://craftsail.com/ideas/
https://craftsail.com/tools/ip/
https://craftsail.com/privacy/
```

没有 `/news/{id}/`。没有 `/account/`。没有带 `?category=` 的地址。

Google 现行文档：

- 只放 **canonical、可收录、返回 200** 的 URL
- 不要放 `noindex`、重定向、404、登录墙、带 `#` 的地址
- 用绝对 HTTPS URL
- 单文件上限 50,000 条或 50MB（未压缩）
- `lastmod` 只有在真实准确时才写。CraftSail 用快照的真实生成时间；没有就不写
- `<priority>` 和 `<changefreq>` Google 会忽略，不必花时间调
- 提交方式：`robots.txt` 里写 `Sitemap:`，同时在 Google Search Console 提交

线索、提及会随快照增减。它们可以进 sitemap，但不要写进 `llms.txt`。目录里出现大量 404，比没有目录更糟。

### 3. llms.txt

`llms.txt` 是 2024 年由 Jeremy Howard / Answer.AI 提出的约定，2026 年 8 月更新到 v2。它是一份 Markdown 目录，告诉 agent：这个站点是什么、先读哪些页。

它 **不是** W3C 标准，也 **没有拦截能力**。Google 明确说过：Search、AI Overviews、AI Mode **不会用** `llms.txt` 做排名。Ahrefs 2026 年对 13.7 万个域名的统计里，28% 的站点有这个文件，但 97% 从未被请求；真去读的，更多是编程 agent 和审计工具，不是 ChatGPT / Perplexity 的回答引擎。

所以：

- 对 Google SEO：几乎无直接作用
- 对 Cursor、Claude Code、文档问答、自定义 agent：有用
- 成本很低，值得做
- 不能代替 `robots.txt`、sitemap 和正经 HTML

CraftSail 的 `https://craftsail.com/llms.txt`：

```md
# 造物局

> 造物局（CraftSail），为独立开发者与小团队聚合 AI 资讯、项目发现和网络工具。

造物局（CraftSail）聚合公开 AI 与开发资讯、本站热点、研究线索和项目发现。资讯标题链到原文，热度是本站聚合指标，不是官方热搜。

## Start here
- [首页](https://craftsail.com/): AI 与开发者趋势数据门户
- [最新资讯](https://craftsail.com/news/): 近 24 小时公开来源
- [AI 动态](https://craftsail.com/news/ai/): 按主题阅读 AI 资讯
- [资讯来源](https://craftsail.com/news/sources/): 订阅来源与采集状态
- [关于造物局](https://craftsail.com/about/): 这是什么、数据从哪来

## 资讯与热点
- [开发与开源](https://craftsail.com/news/dev/)
- [产品发布](https://craftsail.com/news/launch/)
- [出海增长](https://craftsail.com/news/growth/)
- [Twitter/X 订阅](https://craftsail.com/news/twitter/)
- [本站热点](https://craftsail.com/trends/)
- [本站榜单](https://craftsail.com/rankings/)
- [研究线索](https://craftsail.com/ideas/)

## 项目
- [发现项目](https://craftsail.com/projects/)
- [GitHub 增长榜](https://craftsail.com/rankings/github/)

## Optional
- [GitHub 仓库](https://github.com/craftsail/craftsail)
- [RSS](https://craftsail.com/rss.xml)
- [隐私说明](https://craftsail.com/privacy/)
- [IP 与网络检测](https://craftsail.com/tools/ip/)
```

只列稳定 URL。不列会消失的线索 ID。链接必须是真实可打开的地址，不要链到 hash 路由。不要把 `Disallow` 写进 `llms.txt`，它没有 robots 语义。

Stripe Docs、Anthropic、Cloudflare、Mintlify 托管的文档站已经在用。文档站还可以同时提供每页的 Markdown 版。CraftSail 目前做到目录这一层，没有 `llms-full.txt`，够用。

---

## 九、点击必须是 `<a href>`

搜索引擎发现新页的主路径，是跟踪 HTML 里的 `<a href="真实地址">`。

Google 官方 [JavaScript SEO](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics) 写得很直接：用标准的 `<a href>` 指向独立 URL。不要用 `onclick` 当导航，也不要用 `#/path` 当页面地址。

### 1. 爬虫不会「点击」你的按钮

这些写法，人能用，机器基本当没看见，或者当成当前页：

```html
<div onclick="go('/news/ai')">AI 动态</div>
<button onclick="router.push('/about')">关于</button>
<a href="#" onclick="openPage('install')">Install</a>
<div class="nav-item" data-href="/news/ai">AI 动态</div>
```

正确写法。CraftSail 资讯筛选看起来像按钮，底层是链接：

```html
<nav>
  <a href="/news/">全部动态</a>
  <a href="/news/ai/">AI 动态</a>
  <a href="/news/dev/">开发与开源</a>
  <a href="/news/launch/">产品发布</a>
  <a href="/news/growth/">出海增长</a>
  <a href="/news/twitter/">Twitter/X</a>
</nav>
```

关 JS、用 `curl` 打开 `/news/ai/`，源码里要有 title、H1、分类链接。做不到这一点，后面的 JSON-LD 都是装饰。

收藏、刷新、加载更多、开始检测，仍用 `<button>`。那些不是文档导航。

### 2. 前端框架也要最终渲染出 `<a href>`

Astro / React / Vue / Next 的 `<a>`、`<Link>`、`<router-link>` 只要最后输出真实 `href`，就是合格的。

```jsx
// 可以：最终 HTML 是 <a href="/news/ai/">
<Link href="/news/ai/">AI 动态</Link>

// 不行：最终是 <div> 或 href="#"
<div onClick={() => router.push('/news/ai/')}>AI 动态</div>
```

验收：浏览器里「查看网页源代码」（不是 Elements 面板），搜 `href="/news/`。如果源码里没有这些链接，说明它们是 JS 跑完才出现的。Google 也许能渲染，Bing、很多 AI 爬虫、社交爬虫往往不能。

### 3. 锚点 `#` 只用来跳到同一页的某一段

```html
<!-- 可以：同一页里跳到章节 -->
<a href="/about/#editorial">内容与收录原则</a>
<a href="/news/sources/#twitter">Twitter 来源</a>

<!-- 不行：把整页内容挂在 hash 上 -->
<a href="/#/news/ai">AI 动态</a>
<a href="/news#ai">AI 动态</a>
```

`#` 后面的内容，搜索引擎默认当成同一 URL 的片段。`https://craftsail.com/#/news/ai` 和首页在收录上经常被看成同一页。

---

## 十、每个标准答案都要有独立 URL

### 1. 独立 URL，而不是 `?category=`

CraftSail 已经是 Astro 静态生成，不是 hash 路由。但资讯筛选原来是按钮，JS 去改 query：

```text
/news/?category=ai
/news/?platform=twitter
```

人能用。爬虫打开 `/news/`，源码里看不到分类文档。

改完之后：

```text
/news/          全部资讯
/news/ai/       AI 动态
/news/dev/      开发与开源
/news/launch/   产品发布
/news/growth/   出海增长
/news/twitter/  Twitter/X 订阅
```

旧 query 地址 308 到干净路径。带 `q`、`source` 的筛选页不 301，加 `noindex`，canonical 指回路径。

产品站常见写法：

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

CraftSail 不存在的路径返回 HTTP 404，并带 `x-robots-tag: noindex`。SPA 常见的 soft 404 是：随便输入一个地址，HTTP 仍是 200，页面上用 JS 写「Not found」。搜索引擎会把大量空页收进去，浪费抓取配额。

### 3. 看起来可以像 SPA，但必须先吐出 HTML

| 部分 | 建议 | CraftSail |
| --- | --- | --- |
| 首页、主题、关于 | 静态生成或 SSR | Astro 构建期吐出完整 HTML |
| 列表筛选 | 视觉可以像按钮，底层是独立路径 | `/news/ai/` |
| 真正的产品 App | 可以是 SPA，并 `noindex` | `/submit/`、`/account/`、`/api/` |
| 浏览器里才有意义的工具 | 说明文字进 HTML，交互可以靠 JS | `/tools/ip/` 有 WebApplication 和检测说明；探测本身在浏览器里跑 |

对文档站，优先选 Docusaurus、VitePress、Mintlify、Astro 这类 SSG。不要用纯客户端 React SPA 硬做 Help。CraftSail 没有另建文档站：关于、来源、隐私已经够回答「这是什么、数据从哪来」。没有独立内容，就不要为了「看起来像大公司」去搭 Docusaurus。

### 4. URL

好的路径像用户会搜的问题，也像导航：

```text
/                    首页，数据门户
/news/               最新资讯
/news/ai/            AI 动态
/news/sources/       资讯来源
/trends/             本站热点
/ideas/              研究线索
/projects/           发现项目
/about/              这是什么
/tools/ip/           网络检测
/privacy/            隐私
```

- 全小写、短横线、不要空格和驼峰
- 一层一个主题，不要 `/news/index.html#/ai`
- 中英文站用目录或子域分开，并配 `hreflang`。CraftSail 现在只有 `zh-CN`，没做双语，就不假装做了
- 改 URL 必须 301/308 到新地址，并更新 sitemap、llms.txt、导航

---

## 十一、导航、页脚、面包屑

用户能从导航跳，爬虫也能从导航发现。CraftSail 没有文档侧栏，但每一页的页头、页脚、面包屑都是真实 `<a href>`：

- 新页面更容易被发现
- 资讯、热点、来源、关于被链接成一组
- 锚文本就是关键词：`AI 动态`、`资讯来源`、`关于这个网站`

分类页还有面包屑：

```html
<nav aria-label="面包屑导航">
  <a href="/">首页</a>
  ›
  <a href="/news/">最新资讯</a>
  ›
  <span>AI 动态</span>
</nav>
```

并配 `BreadcrumbList` JSON-LD。搜索结果里有机会出现 `首页 > 最新资讯 > AI 动态`。

如果你做的是 Help / Docs，把侧栏做成「一篇正文 + 左侧一排真链接」。折叠可以用 CSS / JS，但折叠前源码里就要有这些 `<a>`。不要等点击分组后才把子链接插入 DOM。

对照 Stripe Docs、Cloudflare Docs、GitHub Docs。不要对照把帮助中心做成 iframe、纯 SPA、或「搜索框 + 无链接结果」的知识库。

---

## 十二、按页面类型写静态信息

首页那段 `<head>` 只能当首页用。内页要改 title、description、canonical、og:url、JSON-LD。

| 页面 | title | JSON-LD |
| --- | --- | --- |
| `/` | `造物局 · CraftSail — AI 与开发者趋势数据门户` | Organization + WebSite + DataCatalog + CollectionPage |
| `/news/{theme}/` | 主题在前，如 `AI 动态 · 造物局` | CollectionPage + 面包屑；ItemList 指向原文 |
| `/trends/` | `本站热点 · 造物局` | CollectionPage；正文写明不是官方热搜 |
| `/ideas/{id}/` | 线索标题 | Article；`og:type=article` |
| `/about/` | `关于造物局 · 造物局` | WebPage + FAQPage |
| `/tools/ip/` | `IP 查询与网络分流检测 · 造物局` | WebApplication，价格写 `0` |
| `/projects/{slug}/` | `{name}：为什么值得用` | SoftwareApplication；有 GitHub 再写 `sameAs` |
| `/submit/`、`/account/` | 不收录 | `noindex`，不进 sitemap |

Organization 用稳定 `@id`：`https://craftsail.com/#organization`。内页用引用，不要把首页整份 `@graph` 复制过去。

产品站套法一样，对象换成定价、安装、对比、文档：

- 首页：品牌 + 品类，`SoftwareApplication` 的价格必须和定价页可见内容一致
- `/pricing`：价格写在正文里，不只写在按钮上
- `/blog/{slug}`、`/help/{path}`：一篇一个 URL，进 sitemap 和 `llms.txt`
- App：`noindex`，不要放进 sitemap

---

## 十三、这次没做的事

- 不为每条 RSS / 推文做本站文章页
- 不新建 Docusaurus / VitePress Help
- 不写 `meta keywords`，不编评分
- 不做中英 `hreflang`（现在只有 `zh-CN`）
- 不把首页 FAQ 硬塞进数据门户首屏
- 不把会过期的线索 ID 写进 `llms.txt`
- 没有官方 X 账号，就不写 `twitter:site`
- 项目 sitemap 还没稳定时，不把 `sitemap-projects.xml` 写进 robots

---

## 十四、落地清单

### 先问三件事

1. **哪些页是本站自己的标准答案？** 只给这些页独立 URL。
2. **人点的入口，源码里是不是 `<a href>`？** 按钮、`onclick`、`#/path` 都不算。
3. **关 JS 打开，正文和链接还在不在？** 不在就还没做完。

`robots.txt`、`sitemap.xml`、`llms.txt`、JSON-LD 都排在这三问后面。

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

### URL 与链接

- [ ] 主题、关于、文档、定价、对比都是独立路径，没有 `#/`
- [ ] 导航、卡片、筛选、页脚、按钮式入口全部是 `<a href>`
- [ ] 源码里能搜到这些 href
- [ ] 不存在的文档返回 HTTP 404
- [ ] 改版旧地址 301/308 到新地址

### 提交与验收

- [ ] Google Search Console 验证域名，提交 sitemap
- [ ] URL Inspection 看「已抓取的页面」是不是完整 HTML
- [ ] Rich Results Test 检查 Organization / FAQPage
- [ ] 分享一次到 Slack / X / LinkedIn，确认卡片图和标题
- [ ] 用无 JS 环境打开一个内页，正文和导航链接仍在

```bash
curl -sL https://craftsail.com/news/ai/ | grep -E '<title>|<h1|rel="canonical"|application/ld\+json|href="/news/'
```

要对上这些：

- 源码里就有 title、H1、canonical、JSON-LD、分类 `<a href>`
- `/llms.txt` 是 Markdown，链接都能打开
- 不存在的路径返回 HTTP 404
- 旧 `/news/?category=ai` 308 到 `/news/ai/`

CraftSail 用 Playwright 把这些断言锁进测试，再用 `curl` 抽查首页、分类资讯、关于、热点。

Search Console、Rich Results、社交卡片是上线后的发布清单，不要和写代码混在一起。

国内获客靠渠道、销售、信息流。小团队出海付不起那套，公开网络才是入口。先让每个答案有独立 URL、真实 `<a href>`、能进 sitemap，再写 title、FAQ、JSON-LD、`llms.txt`。没有独立文档页，`llms.txt` 里列再多链接也没用。GEO 会另写。

CraftSail 这边具体做了：分类做成文档、资讯不做成转载站、FAQ 不塞首页、不稳定的 URL 不写进目录。

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
- [Documentation updates](https://developers.google.com/search/updates)（含 FAQ 富结果下线）
- [AI features and your website](https://developers.google.com/search/docs/appearance/ai-features)
- [Succeeding in AI search](https://developers.google.com/search/blog/2025/05/succeeding-in-ai-search)

### 结构化数据、社交卡片、llms.txt

- [Schema.org](https://schema.org/)
- [Open Graph protocol](https://ogp.me/)
- [X Cards markup](https://developer.x.com/en/docs/x-for-websites/cards/overview/markup)
- [The /llms.txt file, v2](https://llmstxt.org/)
- [Ahrefs: llms.txt study](https://ahrefs.com/blog/llmstxt-study/)

### 可以对照的站点

- CraftSail 首页、分类、关于、llms.txt：<https://craftsail.com/>、<https://craftsail.com/news/ai/>、<https://craftsail.com/about/>、<https://craftsail.com/llms.txt>
- [Stripe Docs](https://docs.stripe.com/payments/checkout)
- [Cloudflare Docs](https://developers.cloudflare.com/workers/get-started/guide/)
- [GitHub Docs](https://docs.github.com/en/get-started/start-your-journey)
