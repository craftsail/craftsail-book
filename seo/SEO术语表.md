# SEO 术语表

前两篇里出现的缩写和专业词，集中放在这里。每条给出英文全称、中文意思，再用一两句话说清它是什么、在我们的实践里怎么用。

按主题分组：先是 SEO 本身和搜索结果页，然后是抓取与收录、页面标签、结构化数据、性能指标、监测工具与指标、流量渠道、内容与链接、AI 搜索，最后是和 SEO 相关的前端架构与网络基础词。

---

## 一、SEO 与搜索结果页

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| SEO | Search Engine Optimization，搜索引擎优化 | 让页面能被搜索引擎找到、看懂、收录，并在结果里排得更前。分站内（页面、结构、速度）和站外（外链、品牌）两部分，这本书主要讲站内。 |
| SERP | Search Engine Results Page，搜索结果页 | 用户搜一个词后看到的那一页。除了十条蓝色链接，还有精选摘要、图片、视频、「用户还搜了」、AI 概览等模块，都在抢注意力。 |
| Featured Snippet | 精选摘要 | SERP 顶部直接展示答案的那个框，常被叫做「零位」（Position 0）。写成「一句话定义 + 列表 / 表格」的段落容易被选中。 |
| Rich Result | 富媒体结果 | 结果里带评分、价格、FAQ 折叠、面包屑等额外信息的条目。靠 JSON-LD 结构化数据触发。 |
| Impression | 展示 | 你的页面在 SERP 上出现了一次，不管用户有没有看到或点击。Search Console 效果报告的四个数之一。 |
| Click | 点击 | 用户从 SERP 点进你的页面。四个数之一。 |
| CTR | Click-Through Rate，点击率 | 点击 ÷ 展示。只有整体 CTR 有意义，不要把各页 CTR 取平均。同一排名下 CTR 明显低于自己的曲线，通常是 title 或 description 的问题。 |
| Average Position，平均排名 | — | 页面在结果里的平均名次。它是所有展示的平均，一个词排第 1、另一个词排第 40，平均下来是 20，单看没有意义，要拆词看。 |
| Ranking，排名 | — | 某个查询词下页面出现的位置。排名是结果不是目标，目标是点击和到站。 |
| Keyword，关键词 | — | 用户在搜索框里输入的词。分品牌词（含产品名）和非品牌词（不含）。非品牌词的点击占比才反映 SEO 本身的效果。 |
| Long-tail Keyword，长尾词 | — | 搜索量小但很具体的词，比如「astro sitemap 分页」。单个词流量小，加起来占多数，竞争也小，是小站的主战场。 |
| Search Intent，搜索意图 | — | 用户搜这个词到底想干什么：了解、比较、购买、还是找某个站。页面类型要和意图匹配，意图是「怎么装」就别塞营销页。 |
| Brand Query，品牌词 | — | 含你产品名的搜索。这部分流量来自别处的曝光，不是 SEO 的功劳，监测时要单独拆出来。 |

## 二、抓取、索引与收录

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| Crawl，抓取 | — | 爬虫下载你的页面。抓取是收录的前提。抓不到、抓出错、抓到的是空壳 HTML，后面都白搭。 |
| Crawler / Bot / Spider，爬虫 | — | 自动访问网页的程序。Google 的叫 Googlebot，Bing 的叫 Bingbot，AI 公司的有 GPTBot、ClaudeBot、PerplexityBot 等。 |
| Googlebot | — | Google 的爬虫。会执行 JS，但排队渲染有延迟，且预算有限，所以 HTML 里该有的东西别指望它跑 JS 才出来。 |
| Crawl Budget，抓取预算 | — | 搜索引擎愿意在你的站上花的抓取量。小站一般够用，但 soft 404、重复参数页、无限翻页会白白吃掉预算。 |
| Index，索引 / 收录 | — | 页面被搜索引擎抓取、理解之后，放进它的数据库。被收录才有可能出现在结果里。 |
| Index Rate，索引率 | — | 已收录页数 ÷ 应收录页数（sitemap 里的数）。低于 80% 就要去查是哪类页没进。 |
| Time to Index，收录时长 | — | 从文章发布到出现在索引里的天数。新站通常几天到几周，稳定的 Blog 可以到一两天内。 |
| Discovered – currently not indexed | 已发现，尚未编入索引 | Search Console 里的一种状态：Google 知道这个 URL，但还没抓。多半是新页面在排队，或站点权重不够、内容太薄。 |
| Crawled – currently not indexed | 已抓取，尚未编入索引 | 已经抓过，但判定不值得收。常见原因是薄页、重复内容、采集内容。 |
| Soft 404 | 软 404 | 页面不存在，但服务器返回 200，只是靠 JS 显示「Not found」。搜索引擎会当成正常页收进去，浪费预算还拉低站点质量。不存在的路径必须返回真正的 404。 |
| robots.txt | — | 放在根目录的文本文件，告诉爬虫哪些路径能抓、哪些不能。它只管抓取，管不了收录：写了 `Disallow` 的页面照样可能因为外链而出现在结果里。 |
| User-agent | 用户代理 | 请求头里标识「我是谁」的字符串。robots.txt 按 user-agent 分组写规则，可以给 Googlebot 和 GPTBot 定不同策略。 |
| Meta Robots | — | 页面 `<head>` 里的 `<meta name="robots">` 标签，控制抓到以后能不能进索引、能不能跟链接。 |
| noindex | — | 告诉搜索引擎「别把这页放进索引」。登录后的 App、staging、感谢页、带筛选参数的页都该加。想从结果里删掉一页，要用它，光 robots.txt 不够。 |
| nofollow | — | 告诉搜索引擎「别顺着这页（或这个链接）上的链接传递权重」。用在链接上时是 `rel="nofollow"`。 |
| X-Robots-Tag | — | 把 robots 指令放在 HTTP 响应头里，而不是 HTML 里。适合 PDF、图片这类没有 `<head>` 的资源，也适合给 404 加 noindex。 |
| Sitemap | 站点地图 | `sitemap.xml`，列出你希望被收录的全部 URL。只放 canonical、返回 200、可收录的地址；不放 noindex、重定向、404、带 `#` 的。大站分多个文件再用索引文件汇总。 |
| llms.txt | — | 放在根目录、给 AI 模型读的 Markdown 文件，简述站点是什么、重要页面在哪。是新兴约定，不是标准，成本低就顺手放一份。 |
| Canonical | 规范地址 | `<link rel="canonical">`，告诉搜索引擎「这一页的正式地址是哪个」。同一内容有多个 URL（www / 非 www、有无尾斜杠、带 UTM 参数）时，全部指回一个干净的绝对 HTTPS 地址。 |
| Duplicate Content，重复内容 | — | 多个 URL 内容相同或高度相似。搜索引擎只会挑一个收，剩下的分散权重。用 canonical 或 301 合并。 |
| Thin Content，薄页 | — | 没有实质内容的页面：只有标题、只有一段转载、只是列表的另一种排序。薄页多了整站质量会被拉低。 |
| Redirect，重定向 | — | 访问旧地址自动跳到新地址。改 URL 一定要做，否则积累的收录和外链全丢。 |
| 301 / 308 | Moved Permanently / Permanent Redirect | 永久重定向的两个状态码。搜索引擎会把旧地址的权重转到新地址。308 和 301 的区别是保留请求方法，对 SEO 效果一样。 |
| 302 / 307 | Found / Temporary Redirect | 临时重定向。搜索引擎会继续把旧地址当正式地址，改 URL 不要用这两个。 |
| 404 | Not Found | 页面不存在。该 404 的就老实返回 404，别为了「用户体验」返回 200。 |
| hreflang | — | `<link rel="alternate" hreflang="...">`，告诉搜索引擎同一内容的其他语言版本在哪。中英文站分目录或子域时要配。只有一种语言就别做。 |
| Trailing Slash，尾斜杠 | — | URL 末尾的 `/`。`/blog` 和 `/blog/` 是两个地址，全站要选定一种，另一种 301 过去，canonical 和 sitemap 保持一致。 |
| Slug | — | URL 里标识一篇内容的那段，比如 `/blog/astro-vs-nextjs` 里的 `astro-vs-nextjs`。全小写、短横线连接、只放关键词。 |

## 三、页面 `<head>` 与标签

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| HTML | HyperText Markup Language，超文本标记语言 | 网页的骨架。搜索引擎和 AI 读的就是它。关掉 JS 之后 HTML 里还在的内容，才算真的「有」。 |
| `<head>` | — | HTML 的头部，放 title、meta、canonical、JSON-LD、OG 等不显示在页面上但给机器读的信息。每一页都要单独写。 |
| Title Tag | `<title>` | 页面标题，SERP 上的蓝色大字。推荐格式「[产品名] · [主关键词 / 核心价值]」，每页唯一，控制在 60 个字符内。 |
| Meta Description | `<meta name="description">` | 页面摘要，SERP 上标题下面那两行灰字。不直接影响排名，但直接影响 CTR。每页单独写，150 字符左右。 |
| H1 / H2 / H3 | Heading，标题层级 | 正文的标题结构。一页一个 H1，和 title 意思一致但不必完全相同；H2、H3 分节，帮机器理解文章结构。 |
| Alt Text | `alt` 属性，替代文本 | 图片加载不出来时显示的文字，也是搜索引擎理解图片内容的依据。写图里有什么，不要堆关键词。 |
| OG | Open Graph | Facebook 定的一套 meta 标签（`og:title`、`og:image`、`og:url` 等），社交平台、聊天软件生成链接预览卡片时读它。`og:url` 要和 canonical 一致。 |
| Twitter Card | — | Twitter / X 自己的一套预览标签（`twitter:card` 等），大部分情况下会回退读 OG。 |
| Preload | `<link rel="preload">` | 提前加载关键资源（比如标题字体）。减少字体闪烁，对 LCP 和 CLS 有帮助。只 preload 真正首屏用到的。 |
| FOUT / FOIT | Flash of Unstyled Text / Flash of Invisible Text | 网页字体还没加载完时，文字先用系统字体显示再切换（FOUT），或者先空白再出现（FOIT）。preload 字体和 `font-display` 可以缓解。 |
| Breadcrumb，面包屑 | — | 页面顶部「首页 > Blog > 文章名」的导航链。给用户看层级，也给搜索引擎看结构，配 BreadcrumbList 结构化数据后能在 SERP 显示。 |
| Anchor Text，锚文本 | — | 链接上的文字。搜索引擎用它理解被链页面讲什么，所以侧栏、正文里的链接文字要写成关键词，别写「点这里」。 |
| Internal Link，内链 | — | 站内页面之间的链接。爬虫顺着内链发现新页；侧栏、面包屑、相关文章都是内链。必须是真的 `<a href>`。 |
| Backlink / External Link，外链 | — | 别的网站指向你的链接。是站外 SEO 的核心信号，但小团队短期内很难控制，先把站内做对。 |
| UTF-8 | 8-bit Unicode Transformation Format | 字符编码。`<meta charset="utf-8">` 放在 `<head>` 第一行，避免中文乱码。 |

## 四、结构化数据

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| Structured Data，结构化数据 | — | 用机器能直接读的格式，告诉搜索引擎「这一页是什么类型、关键字段是什么」。是触发富媒体结果的前提。 |
| JSON-LD | JavaScript Object Notation for Linked Data | 结构化数据最常用的写法：一段 `<script type="application/ld+json">` 放在 `<head>` 里，Google 推荐的格式。字段要和页面上显示的内容一致，不能页面写 $9 JSON 写 $5。 |
| Schema.org | — | 定义结构化数据词汇表的组织和网站。JSON-LD 里的 `@type` 都取自这里。 |
| Organization | — | Schema 类型，描述公司或团队：名字、logo、官网、社交账号。首页放。 |
| WebSite | — | Schema 类型，描述整个站点。可以配 SearchBox 声明站内搜索地址。 |
| WebPage / CollectionPage | — | 单页和列表页的 Schema 类型。Blog 列表、Help 目录用 CollectionPage。 |
| BlogPosting / TechArticle | — | 文章类 Schema。Blog 文章用 BlogPosting，技术文档用 TechArticle。带标题、作者、发布时间、修改时间、配图。 |
| FAQPage | — | 问答页 Schema。一页里有明确的「问题 + 回答」结构时用，SERP 上可能显示折叠的问答。 |
| BreadcrumbList / ItemList / ListItem | — | 面包屑和列表的 Schema。ListItem 是列表里的一项，带 `position`。 |
| SoftwareApplication / WebApplication | — | 描述软件产品的 Schema。带名称、类别、操作系统、价格。产品站首页或定价页放。 |
| Product | — | 描述商品的 Schema，带价格、库存、评分。SaaS 一般用 SoftwareApplication 而不是它。 |
| DataCatalog / Dataset | — | 描述数据集的 Schema。数据门户类站点用，Google 有专门的数据集搜索。 |

## 五、性能指标

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| CWV | Core Web Vitals，核心网页指标 | Google 定义的三个用户体验指标：LCP、INP、CLS。是排名因素之一，也是 Search Console 里单独一个报告。 |
| LCP | Largest Contentful Paint，最大内容绘制 | 首屏最大的那块内容（通常是主图或标题）多久渲染出来。好：2.5 秒内。JS 太重、图片没压、字体阻塞都会拖慢它。 |
| INP | Interaction to Next Paint，交互到下一次绘制 | 用户点击、输入后，页面多久有视觉反馈。好：200 毫秒内。2024 年取代了 FID。主线程被大块 JS 占住时会变差。 |
| FID | First Input Delay，首次输入延迟 | INP 的前身，只测第一次交互，已被 INP 取代。旧文章里还会看到。 |
| CLS | Cumulative Layout Shift，累积布局偏移 | 页面加载过程中内容跳动的程度。好：0.1 以内。图片没写宽高、字体切换、广告插入都会导致。 |
| TTFB | Time to First Byte，首字节时间 | 浏览器发出请求到收到第一个字节的时间。反映服务器和网络延迟，海外用户远时特别明显，CDN 和静态化能改善。 |
| FCP | First Contentful Paint，首次内容绘制 | 页面第一次出现任何内容的时间。比 LCP 早，是辅助指标。 |
| P75 | 75th Percentile，第 75 百分位 | Core Web Vitals 的统计口径：把所有用户的测量值排序，取 75% 位置的那个值。意思是「四分之三的用户体验至少这么好」。 |
| RUM | Real User Monitoring，真实用户监测 | 从真实访客的浏览器采集的性能数据，和实验室跑分相对。Cloudflare 的 Web Analytics、CrUX 都是 RUM。 |
| CrUX | Chrome User Experience Report | Google 从 Chrome 用户收集的 RUM 数据集，Search Console 和 PageSpeed 里的「真实用户数据」来源于此。流量太小的站不会有数据。 |
| PageSpeed Insights | — | Google 的在线测速工具。上面显示实验室数据（Lighthouse 跑分）和真实用户数据（CrUX）。以真实用户数据为准。 |
| Lighthouse | — | Chrome 内置的网页审计工具，给性能、可访问性、SEO 打分。实验室环境，分数波动大，看趋势和具体建议，别盯着分。 |

## 六、监测工具与指标

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| GSC | Google Search Console | Google 给站长的免费后台。看展示、点击、CTR、排名，看哪些页收录了、哪些没，看抓取统计和 Core Web Vitals。SEO 监测的第一数据源。 |
| Performance Report，效果报告 | — | GSC 的核心报告，四个数：点击、展示、CTR、平均排名。可以按查询词、页面、国家、设备拆。 |
| Page Indexing，网页索引 | — | GSC 里看哪些 URL 已收录、哪些没有以及为什么。配合 sitemap 算索引率。 |
| Crawl Stats，抓取统计 | — | GSC 里看 Googlebot 每天抓了多少次、响应码分布、抓取目的（发现新页还是刷新旧页）。 |
| URL Inspection，网址检查 | — | GSC 里查单个 URL 的收录状态，可以手动请求索引。新文章发出来可以用一下，但别指望它替代 sitemap。 |
| Data Sampling / Anonymized Queries | 数据抽样 / 匿名查询 | GSC 的坑：低频查询词不会显示，按页面和按查询词看的合计数对不上是正常的。 |
| GA | Google Analytics | Google 的网站分析工具。看用户从哪来、到哪页、做了什么。 |
| GA4 | Google Analytics 4 | 现行版本的 GA，2023 年替代了 Universal Analytics。基于事件模型，所有行为都是事件。 |
| UA | Universal Analytics | GA 的上一代，2023 年 7 月停止收数。旧文章里的「会话」「跳出率」定义和 GA4 不同。 |
| Session，会话 | — | 一个用户在一段连续时间内的访问。默认 30 分钟无操作就结束。 |
| Engaged Session，互动会话 | — | GA4 的定义：停留超过 10 秒、或有关键事件、或看了 2 页以上的会话。 |
| Engagement Rate，互动率 | — | 互动会话 ÷ 总会话。GA4 用它取代了跳出率。自然搜索来的互动率低，说明页面和搜索意图不匹配。 |
| Bounce Rate，跳出率 | — | GA4 里定义为 1 − 互动率。和 UA 时代「只看一页就走」的定义不一样。 |
| Key Event，关键事件 | — | GA4 里你标记为「重要」的事件，比如注册、付费、下载。以前叫 Conversion（转化）。自然搜索关键事件率 = 自然搜索会话中触发关键事件的比例。 |
| Landing Page，落地页 | — | 用户进站的第一页。SEO 看自然搜索的落地页，知道哪些文章在带流量。 |
| Channel Grouping，渠道分组 | — | GA4 按来源把流量分成 Organic Search、Direct、Referral、Organic Social 等。AI 来源默认被归到 Referral，要自己建自定义渠道。 |
| Organic Search，自然搜索 | — | 从搜索引擎的非付费结果点进来的流量。SEO 的成果就看这个渠道。 |
| Direct，直接访问 | — | 没有来源信息的流量：输网址、书签、以及很多丢了 referrer 的情况（App 内打开、部分 AI 工具）。 |
| Referral，引荐 | — | 从其他网站链接点进来的流量。 |
| Referrer | HTTP Referer | 请求头里记录「用户从哪个页面点过来」。GA 的来源判断靠它。HTTPS 到 HTTP、部分客户端会丢。 |
| UTM | Urchin Tracking Module | URL 上的 `utm_source`、`utm_medium`、`utm_campaign` 等参数，手动标记流量来源。发社媒、发 newsletter 时带上。canonical 要指回不带 UTM 的地址。 |
| BigQuery | — | Google 的数据仓库。GA4 可以免费把原始事件导出到 BigQuery，用 SQL 做 GA4 界面做不了的分析。 |
| Cloudflare Analytics | — | Cloudflare 从边缘节点统计的请求数据。和 GA 不同，它不依赖浏览器 JS，所以能看到爬虫、AI bot 的访问，GA 看不到这些。 |
| WAF | Web Application Firewall，Web 应用防火墙 | Cloudflare 里过滤恶意请求的组件。误把搜索引擎或 AI 爬虫拦掉会直接影响收录，要定期看规则日志。 |
| Click-to-Session Ratio，点击到会话比 | — | GA4 自然搜索会话 ÷ GSC 点击。正常在 0.6 到 0.9 之间，因为部分用户禁 JS、拦截统计、或者秒退。明显偏低说明统计代码有问题。 |
| Opportunity Clicks，机会点击 | — | 按自己的 CTR 曲线，某页在当前排名下「应该」拿到的点击减去实际点击。用来排优先级：先改差距最大的页。 |
| WoW | Week over Week，周环比 | 本周和上周比。SEO 数据天级波动大，周环比比日环比更稳。 |
| Alert Threshold，告警线 | — | 指标下滑到多少要人工介入。比如非品牌点击周环比跌 20%、Googlebot 错误率超 5%。 |

## 七、内容与链接

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| E-E-A-T | Experience, Expertise, Authoritativeness, Trustworthiness，经验、专业、权威、可信 | Google 质量评估指南里衡量内容质量的四个维度。不是直接的排名算法，但体现在算法更新的方向上。自己做过的事写出来，就是 Experience。 |
| Helpful Content，有用内容 | — | Google 2022 年起的一系列更新，打击为搜索引擎而写、对人没用的内容。采集站、AI 批量生成的薄文是主要目标。 |
| Content Farm / Scraper Site，内容农场 / 采集站 | — | 靠转载、拼凑、批量生成来堆页面数的网站。英文搜索对这类站惩罚很重，聚合类产品要避免长得像它。 |
| Topical Authority，主题权威 | — | 一个站在某个主题下有足够多、足够深、互相链接的内容，搜索引擎就更愿意在这个主题下推它。Help 文档的侧栏就是在做这件事。 |
| Pillar Page / Topic Cluster，支柱页 / 主题簇 | — | 一篇总览页加一组细分文章，互相链接。是组织 Blog 和 Help 的常用结构。 |
| Evergreen Content，常青内容 | — | 不随时间过期的内容，比如「怎么安装」「概念解释」。持续带流量，和有时效的资讯互补。 |
| Cannibalization，关键词内耗 | — | 自己站内多篇文章抢同一个词，互相分流，谁也排不上。合并或者明确分工。 |
| Domain Authority，域名权重 | DA | 第三方工具（Moz、Ahrefs 的 DR）估算的域名综合实力，不是 Google 的官方指标。新站低是正常的，看趋势即可。 |
| PageRank | — | Google 早期的核心算法，按链接投票算页面重要性。公开分值早已取消，但链接传递权重的思路还在。 |
| Link Juice，链接权重 | — | 口语说法，指链接从一页传到另一页的「投票」。nofollow 的链接不传。 |
| Orphan Page，孤页 | — | 站内没有任何链接指向它的页面。爬虫只能靠 sitemap 发现，收录慢、排名弱。 |
| Pagination，分页 | — | 列表太长时拆成多页。每页 canonical 指向自己，title 带页码，分页页可以不进 sitemap，但文章页必须进。 |
| Feed | — | 按时间倒序排列的内容流。Blog 首页做成 feed，让搜索引擎每次来都能看到新内容。 |
| RSS | Really Simple Syndication | 一种 XML 格式的订阅源。放在 `/rss.xml`，`<head>` 里用 `<link rel="alternate">` 声明。读者用阅读器订阅，部分爬虫和 AI 也读它发现新文章。 |
| Publish Date / Modified Date，发布 / 修改时间 | — | 文章的两个时间戳，写进 JSON-LD 和 `article:published_time`。修改时间要真的改了内容才更新，不要每次构建都刷新。 |

## 八、AI 搜索

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| GEO | Generative Engine Optimization，生成式引擎优化 | 让 ChatGPT、Perplexity、Google AI 概览这类生成式搜索在回答里引用你、链到你。做法和 SEO 大部分重叠：干净的 HTML、清楚的定义句、可引用的数据。额外要做的是对 AI 爬虫开放、放 llms.txt。 |
| AIO / AI Overviews | AI 概览 | Google SERP 顶部由 AI 生成的摘要块。它会引用几个来源并给链接。被引用能带流量，没被引用会被它挡住点击。 |
| AI Mode | — | Google 搜索的对话式模式，整页都是 AI 生成的回答。是 AI 概览的加强版。 |
| LLM | Large Language Model，大语言模型 | ChatGPT、Claude、Gemini、DeepSeek 背后的模型。它们读网页的方式和爬虫类似：要 HTML 里有内容，要能直接抽出答案。 |
| AI Crawler，AI 爬虫 | — | AI 公司的爬虫。分两类：训练用（GPTBot、ClaudeBot、Google-Extended）和实时检索用（ChatGPT-User、PerplexityBot、Claude-SearchBot）。在 Cloudflare 里能看到它们，GA 看不到。 |
| AI Referral，AI 引荐流量 | — | 用户从 ChatGPT、Perplexity 等对话里点链接进站。GA4 默认归到 Referral，要按来源域名（chatgpt.com、perplexity.ai 等）建自定义渠道。 |
| Citation，引用 | — | AI 回答里给出的来源链接。GEO 的直接目标。 |
| Generative AI Report，生成式 AI 报告 | — | Search Console 里新出的报告，展示你的页面在 Google AI 功能里的展示和点击。 |
| Zero-click Search，零点击搜索 | — | 用户在 SERP 或 AI 概览里直接看到答案，不点任何链接。展示涨、点击不涨时，多半是这个原因。 |

## 九、前端架构与网络基础

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| JS | JavaScript | 网页里的脚本语言。SEO 的基本原则：关掉 JS，HTML 里该有的都得在。 |
| CSS | Cascading Style Sheets，层叠样式表 | 网页的样式。不影响收录，但影响 CLS 和渲染速度。 |
| DOM | Document Object Model，文档对象模型 | 浏览器把 HTML 解析成的树结构。JS 靠改 DOM 来改页面，爬虫读的是初始 HTML 还是渲染后的 DOM，决定了 SPA 能不能被收录。 |
| SPA | Single Page Application，单页应用 | 只有一个 HTML 壳，所有页面靠 JS 在浏览器里切换。URL 变了内容却要 JS 才有，爬虫抓到的是空壳。适合登录后的 App，不适合要收录的内容页。 |
| CSR | Client-Side Rendering，客户端渲染 | 页面内容由浏览器执行 JS 生成。SPA 的默认模式。 |
| SSR | Server-Side Rendering，服务端渲染 | 每次请求由服务器生成完整 HTML 再返回。爬虫能直接读到内容。Next.js 的默认模式之一。 |
| SSG | Static Site Generation，静态站点生成 | 构建时把所有页面预先生成为 HTML 文件，部署到 CDN。最快、最稳、对爬虫最友好。Astro 的默认模式，适合内容站。 |
| ISR | Incremental Static Regeneration，增量静态再生 | Next.js 的模式：先静态生成，隔一段时间在后台重新生成。介于 SSR 和 SSG 之间。 |
| RSC | React Server Components，React 服务端组件 | React 的新架构，组件在服务端渲染成 HTML 和序列化数据。Next.js App Router 基于它。对 SEO 没问题，但复杂度高。 |
| Hydration，水合 | — | 服务端渲染出的静态 HTML 到浏览器后，JS 接管、绑定事件的过程。SSR 和 SSG 页面都需要，水合的 JS 越少，页面越快。 |
| Islands Architecture，岛屿架构 | — | Astro 的做法：页面默认是纯静态 HTML，只有需要交互的组件（岛）才加载 JS。内容站首屏 JS 接近零。 |
| MDX | Markdown + JSX | 在 Markdown 里嵌组件的格式。Astro、Next.js 的文档站常用它写文章。 |
| AJAX | Asynchronous JavaScript and XML | 页面不刷新、用 JS 在后台请求数据的技术统称。用 AJAX 加载的内容不在初始 HTML 里，爬虫可能看不到。 |
| API | Application Programming Interface，应用编程接口 | 程序之间的调用约定。`/api/` 路径下的接口不该被收录，写进 robots.txt 并加 noindex。 |
| HTTP / HTTPS | HyperText Transfer Protocol (Secure)，超文本传输协议 | 网页传输协议。HTTPS 是加密版，现在是必须项，全站 HTTP 要 301 到 HTTPS。 |
| Status Code，状态码 | — | HTTP 响应的三位数字。200 正常，301/308 永久跳转，302/307 临时跳转，404 不存在，500 服务器错误。爬虫按状态码决定怎么处理页面。 |
| DNS | Domain Name System，域名系统 | 把域名解析成 IP 地址的系统。Cloudflare 接管 DNS 后才能用它的 CDN、WAF 和 Analytics。 |
| CDN | Content Delivery Network，内容分发网络 | 把静态资源缓存到全球各地的边缘节点，用户就近访问。海外用户离服务器远，CDN 对 TTFB 和 LCP 影响很大。 |
| Edge，边缘 | — | CDN 离用户最近的那些节点。Cloudflare 的统计就在边缘做，所以看得到爬虫。 |
| CORS | Cross-Origin Resource Sharing，跨域资源共享 | 浏览器限制不同域名之间请求的机制。字体、API 跨域时要配对应的响应头，否则加载失败影响 LCP。 |
| IP | Internet Protocol address，网络协议地址 | 设备在网络上的地址。Cloudflare 靠 IP 段和 user-agent 一起验证爬虫真假。 |
| SVG / PNG / JPG / WebP / AVIF | 图片格式 | SVG 是矢量图，适合 logo、图标；PNG 无损，适合截图；JPG 有损，适合照片；WebP 和 AVIF 是新格式，体积更小，现代浏览器都支持。图片一定要写 `width` 和 `height`。 |
| Lazy Loading，懒加载 | `loading="lazy"` | 图片滚动到可见区域才加载。首屏图片不要懒加载，会拖慢 LCP；首屏以下的都可以。 |
| Minify / Bundle，压缩 / 打包 | — | 把 JS、CSS 去掉空格注释并合并成少数文件。构建工具默认会做，但打包出来还是太大的话，问题在代码本身。 |
| DevTools | Chrome Developer Tools | 浏览器开发者工具。「禁用 JavaScript」后刷新页面，是检查 HTML 里到底有没有内容的最快办法。 |
| Staging，预发环境 | — | 上线前的测试环境。整站 noindex，不放进 sitemap，最好加密码。被收录的 staging 会和正式站形成重复内容。 |

## 十、运营与增长

| 术语 | 全称 | 含义 |
| --- | --- | --- |
| SaaS | Software as a Service，软件即服务 | 按订阅收费的在线软件。这本书面向的产品形态。 |
| PLG | Product-Led Growth，产品驱动增长 | 靠产品本身（免费试用、自助上手）而不是销售拉客户。SEO 是 PLG 的主要获客渠道之一。 |
| CTA | Call to Action，行动号召 | 页面上引导用户下一步的按钮或文字：注册、试用、下载。文章末尾放一个就够。 |
| MQL | Marketing Qualified Lead，营销合格线索 | 营销侧判断「有意向」的潜在客户，比如注册了、看了定价页。用来衡量 SEO 流量的质量。 |
| CRM | Customer Relationship Management，客户关系管理 | 管理客户和线索的系统。SEO 带来的注册最终要进 CRM 才能追到付费。 |
| ROI | Return on Investment，投资回报率 | 投入产出比。SEO 的 ROI 周期长，头三到六个月基本看不到回报，要按季度算。 |
| KPI | Key Performance Indicator，关键绩效指标 | 用来衡量目标的少数几个数。SEO 的 KPI 建议盯非品牌点击、自然搜索关键事件、收录时长，别盯排名。 |
| Newsletter | 邮件通讯 | 定期发给订阅者的邮件。和 RSS 一样是内容分发渠道，链接带 UTM。 |
| PR | Public Relations，公关 | 让媒体、KOL 提到你。带来品牌词搜索和外链，是站外 SEO 的主要来源。 |
| Product Hunt / Hacker News | — | 独立开发者发布产品的两个社区。首发能带一波流量和外链，是很多产品第一批品牌词搜索的来源。 |
