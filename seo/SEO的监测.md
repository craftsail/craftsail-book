# SEO 监测

[SEO 落地](SEO落地.md) 讲怎么把页面做对。这篇讲做完以后怎么看数据：页面有没有被抓、有没有被收录、排第几、有没有人点、点进来以后干了什么。

我们用三个工具，各看链路的一段：

```text
Cloudflare          Search Console              GA4
─────────────       ─────────────────────       ──────────────────
爬虫来了没     →    收录了没 → 展示 → 点击   →   到站以后做了什么
页面快不快          排第几、哪些词                停留、转化、AI 来的流量
```

三个工具的数字永远对不上，这是正常的。它们计数的位置不一样：Cloudflare 在网络边缘数请求，Search Console 在 Google 的搜索结果里数展示和点击，GA4 在浏览器里跑 JS 数会话。对不上不要紧，要看的是每一段自己的趋势，以及段与段之间的比例有没有突然变。

后面先讲一次性的设置，再分工具讲看哪几个报表，然后是公式，最后是每天、每周、每月的节奏和周报模板。

---

## 一、先把漏斗画出来

| 阶段 | 问题 | 看哪 | 主要指标 |
| --- | --- | --- | --- |
| 抓取 | Googlebot 来了没，拿到的是 200 吗 | Cloudflare、GSC 抓取统计 | 抓取请求数、状态码分布、响应时间 |
| 收录 | 抓了以后收了没 | GSC 网页索引、站点地图 | 已编入索引数、未编入索引的原因 |
| 展示 | 出现在搜索结果里没 | GSC 效果报告 | 展示次数、平均排名 |
| 点击 | 出现以后有人点没 | GSC 效果报告 | 点击次数、CTR |
| 到站 | 点进来以后留下没 | GA4 | 互动率、平均互动时长 |
| 转化 | 做了我们想让他做的事没 | GA4 | 关键事件 |
| AI | AI 爬了多少、引用了多少、带来多少人 | Cloudflare AI Crawl Control、GSC 生成式 AI 报告、GA4 | AI 抓取数、AI 展示数、AI 来源会话 |

排查问题时从左往右看。点击掉了，先确认展示有没有掉；展示掉了，再看收录；收录掉了，再看抓取。哪一段先断，问题就在哪一段。

---

## 二、一次性设置

这些做一次就行，但没做的话，后面很多数据看不到。

### 1. Search Console

用网域资源（Domain property），不用网址前缀资源。网域资源把 `http`、`https`、`www`、子域全算进来，品牌词过滤和抓取统计也只在根级别的资源上有。

在「站点地图」里提交 `sitemap-index.xml`。

在 GA4 里关联 Search Console（GA4 → 管理 → 产品关联 → Search Console 关联），关联后大约两三天，GA4 里会多出「查询」和「Google 自然搜索流量」两个报表。

以后每次改版、上线新模块、改 robots.txt，都在效果报告的图上右键「添加注释」。GSC 从 2025 年 11 月开始支持自定义注释，每条最多 120 字，每个资源最多 200 条，500 天后自动删除，不能编辑。三个月后回头看曲线时，你会感谢当时记了这一笔。

### 2. GA4

排除内部流量：管理 → 数据流 → 配置代码设置 → 定义内部流量，把自己的 IP 加进去，再到数据过滤器里启用。

把关键事件标出来。GA4 不会自己知道什么算转化，事件触发了但没标成关键事件，报表里就看不到。我们标的是：点「进入 App」、点 GitHub 链接、订阅 RSS、注册。

建一个 AI 渠道。AI 助手带来的点击默认会落进「引荐（Referral）」，混在一起看不出来。管理 → 数据显示 → 渠道组 → 复制默认渠道组，新建一个叫「AI」的渠道，条件是「来源」匹配正则：

```text
(?i)^(chatgpt\.com|chat\.openai\.com|perplexity\.ai|www\.perplexity\.ai|claude\.ai|gemini\.google\.com|copilot\.microsoft\.com|copilot\.com|grok\.com|chat\.deepseek\.com|meta\.ai|chat\.mistral\.ai)$
```

然后把这个渠道拖到「引荐」上面。GA4 按顺序匹配，排在引荐下面就永远轮不到它。自定义渠道组对历史数据也生效，建好马上就能看到过去的 AI 流量。

要知道这个数是偏少的。ChatGPT、Perplexity 的手机 App 点出来的链接经常不带来源，会被算成「直接」。

### 3. Cloudflare

打开 Web Analytics（真实用户测速，RUM）。它在浏览器里测 LCP、INP、CLS，不用 cookie，不需要同意弹窗。

打开 AI Crawl Control，看 AI 爬虫。

记住一件事：Cloudflare 首页那个「流量」面板统计的是边缘请求，里面有大量爬虫和机器人，不能当访客数用。有人实测过它的访客数是 GA4 的四五倍。要看真人，看 Web Analytics 或 GA4。

---

## 三、Search Console 看什么

### 1. 效果报告：四个数

效果 → 搜索结果。四个指标：

```text
点击次数   用户从 Google 搜索结果点进你网站的次数
展示次数   你的网址出现在搜索结果里的次数
CTR       点击次数 ÷ 展示次数
平均排名   每次展示时你排在最上面那条结果的位置，按展示次数加权平均
```

平均排名是加权的，展示多的词权重大。所以它会被词的结构带偏：你新写了一批长尾文章，排在 30 名开外，展示涨了，平均排名反而会变差。平均排名变差不一定是坏事，要拆开看。

默认显示最近 3 个月，最多能看 16 个月。界面上每个维度最多导出 1000 行，要更多用 API（每次最多 25,000 行，可以翻页）或者 BigQuery 批量导出。数据一般晚两天左右。

### 2. 拆开品牌词和非品牌词

搜「造物局」「craftsail」点进来的人本来就知道你，这部分流量跟 SEO 做得好不好关系不大。SEO 真正带来的是非品牌词。两者混在一起，CTR 和排名都会被品牌词抬得很高。

GSC 从 2025 年 11 月开始有品牌词过滤，2026 年 3 月对所有符合条件的网站开放：新增过滤条件 → 查询 → 选「品牌」或「非品牌」。它用 AI 判断，能认出错别字和其他语言的写法。但它只在网域资源、查询量够的站上出现。

我们的站量还小，看不到这个选项，就用正则自己拆：新增过滤条件 → 查询 → 自定义（正则表达式）：

```text
(?i)craftsail|craft sail|造物局
```

选「不匹配」就是非品牌词。

### 3. 按网页看，特别是新文章

效果报告里切到「网页」标签，按网址过滤 `/blog/`，就能看到 Blog 的整体表现。每篇新文章发布后，过滤它的 URL，看第一次出现展示是哪天。这个数后面会用来算收录时长。

「查询」标签和「网页」标签配合看：点一个网页，再切到查询，就是这个网页排上了哪些词。一个页面往往排着几十个词，改标题之前先看一眼，别为了一个词把其他词丢了。

### 4. 网页索引和站点地图

索引 → 网页。看两条线：已编入索引、未编入索引。点「未编入索引」下面的原因，最常见的几个：

| 原因 | 意思 | 怎么办 |
| --- | --- | --- |
| 已发现 - 尚未编入索引 | 知道这个 URL，还没来抓 | 新站常见，等；长期不动就加内链 |
| 已抓取 - 尚未编入索引 | 抓了，觉得不值得收 | 内容太薄或和别的页太像，改内容或合并 |
| 重复网页，用户未选定规范网页 | 有几个 URL 内容一样 | 检查 canonical |
| 备用网页（有适当的规范标记） | 你自己 canonical 到别处了 | 正常，不用管 |
| 已被 noindex 标记排除 | 你写了 noindex | 确认是不是故意的 |
| 未找到 (404) | 地址打不开 | 真删了就不用管；不该 404 的要修 |

站点地图报告里能看到每个 sitemap 提交了多少 URL。拿「已编入索引」对照提交数，就是索引率，公式见第六节。

单个 URL 用网址检查：顶部搜索框输入完整 URL，能看到上次抓取时间、Google 选定的 canonical、抓到的 HTML。「测试实际网址」可以看 Googlebot 现在能拿到什么。

### 5. 抓取统计

设置 → 抓取统计信息 → 打开报告。只有根级资源才有。

看三样：

总抓取请求数。它包括成功和失败的请求，不是越多越好。

平均响应时间。持续往上走，说明服务器扛不住了。请求数在降、响应时间在涨，通常就是 Google 因为你慢而主动减速。静态站放在 Cloudflare 上，这个数一般很低。

按用途细分：「发现」是第一次抓的新 URL，「刷新」是重抓老 URL。我们每天发 3 篇 Blog，发现类的比例应该稳定在一个水平上，突然掉下去，多半是新文章没进 sitemap，或者内链断了。

主机状态有三项：robots.txt 获取、DNS 解析、服务器连接。看过去 90 天有没有出问题，任意一项红了，Google 会减慢甚至暂停抓取。

几千页以内的站基本不用操心抓取预算，这个报告主要用来发现服务器和配置问题。

### 6. 生成式 AI 报告

效果 → 生成式 AI。2026 年 6 月先在英国上线，8 月 31 日对全球所有网站开放，数据从 2026 年 5 月 18 日开始，没有更早的。

它统计你的网址在 AI 概览（AI Overviews）和 AI 模式里出现的次数，可以按网页、国家、设备、日期看。

它只有展示次数，没有点击、没有 CTR、没有查询词。这些展示也同时算在普通效果报告里。展示少的站可能看不到这个报告。

它能回答的问题是：哪些页面被 AI 引用了。拿它和 GA4 里 AI 渠道的落地页对照，能看出哪些页面既被引用又带来了人。

### 7. 数据的坑

GSC 的数据不是没出过错。

2025 年 5 月 13 日到 2026 年 4 月 27 日，GSC 的展示次数因为日志错误被多算了，桌面端、图片搜索尤其明显。点击次数不受影响，但展示、CTR、平均排名在这段时间都不准，而且不会回补。所以这段时间的展示不要拿来做同比，同比用点击。

2026 年 5 月 7 日起 Google 不再展示 FAQ 富结果，FAQ 相关的展示会掉一截，这不是你的问题。

2026 年还有好几次 Discover 和生成式 AI 报告的短期日志错误。

看到数据突然断崖，先去 Google 的 [Search Console 数据异常](https://support.google.com/webmasters/answer/6211453) 页面对一下日期，再看图上有没有系统注释。点击稳、展示掉，多半是统计问题；点击和展示一起掉，才是真的掉了。

---

## 四、GA4 看什么

GSC 管点击之前，GA4 管点击之后。GA4 看不到搜索词，词要去 GSC 看。

### 1. 自然搜索流量

报告 → 获取 → 流量获取，主维度选「会话默认渠道组」，看「自然搜索（Organic Search）」这一行。加一个次级维度「会话来源/媒介」，能把 Google 和 Bing 分开。

### 2. 落地页

报告 → 互动 → 着陆页，加过滤条件「会话媒介」完全匹配 `organic`。这里看的是搜索进来的人落在哪些页、每个页留不留得住人。

关联了 GSC 以后，报告 → Search Console → Google 自然搜索流量，这张表把 GSC 的点击、展示、排名和 GA4 的互动、关键事件放在同一行，按落地页看最方便。这组报告默认可能没挂在左侧菜单里，要在「报告库」里把 Search Console 集合发布出来。

### 3. 互动率

GA4 把满足任意一个条件的会话算作「互动会话」：停留超过 10 秒、触发了关键事件、看了 2 个及以上页面。

```text
互动率 = 互动会话数 ÷ 会话数
```

网上常见的说法是自然搜索的互动率在 55% 到 75% 之间算健康，低于 40% 说明页面和搜索意图对不上。这只是别人的经验值，不同类型的页面差别很大：一篇 Help 回答了问题、用户看完就走，停留不到 10 秒也可能是好事。所以我们主要拿自己的页面跟自己比，同一类页面里特别低的那几个，才值得去看。

流量高、互动低：标题承诺的和页面给的不一样，或者页面太慢。
互动还行、关键事件为零：内容有用，但页面上没给下一步。

### 4. 关键事件

报告 → 互动 → 关键事件，按会话默认渠道组拆。自然搜索的关键事件率公式见第六节。

不是所有自然流量都一样值钱。进了 Help 和产品页的人，比进了日报 Blog 的人更可能转化，按落地页分组看关键事件率，能看出来哪类页面在带人往下走。

### 5. AI 渠道

建好第二节那个 AI 渠道以后，在流量获取里就能看到单独一行。再用探索（Explore）建一张表：行是落地页，列是会话数、互动率、关键事件，过滤条件是渠道 = AI。它能告诉你 AI 在引用哪些页面。

据说 GA4 后来也加了内置的 AI 助手渠道，但覆盖的平台不全，Perplexity 就不在里面，历史数据也不回补，所以我们还是用自己建的。

---

## 五、Cloudflare 看什么

### 1. 先分清两种统计

| | 边缘统计（Analytics & Logs → HTTP 流量） | Web Analytics |
| --- | --- | --- |
| 在哪统计 | Cloudflare 边缘，每个请求都算 | 浏览器里的 JS 信标 |
| 包含爬虫 | 包含 | 基本不含 |
| 「访问」的定义 | 来源不是本站的一次页面浏览 | 同左，没有 GA 那种会话 |
| 适合看 | 爬虫、状态码、带宽、缓存命中 | 真人访问量、Core Web Vitals |

边缘统计里的访客数不要拿来汇报，它把机器人全算进去了。

### 2. 搜索引擎爬虫

安全性 → 分析，看机器人分析（Bot analysis）。请求被分成「自动化」「可能自动化」「可能是人」「已验证机器人」。Googlebot、Bingbot 属于已验证机器人里的搜索引擎类。免费套餐能看到的维度比较少，有 Bot Management 的套餐才能细看到每个检测 ID。

我们看两件事：

一是 Googlebot 有没有被误伤。WAF 规则、质询、限速如果挡到了 Googlebot，GSC 那边就会看到抓取失败。写规则时用 `cf.client.bot` 或 `cf.verified_bot_category` 把搜索引擎放行。注意 `cf.client.bot` 会放行所有已验证机器人，包括 AI 爬虫和 SEO 工具。

二是 Googlebot 拿到的状态码。按主机和路径过滤，看 5xx 有没有、4xx 在不在涨。这些数可以和 GSC 抓取统计里的「按响应划分」对照着看。GSC 那边有延迟，Cloudflare 这边几乎是实时的。

### 3. AI 爬虫

AI Crawl Control 里有三个标签：

概览：请求量、状态码、热门路径，爬虫按运营方分组（OpenAI、Microsoft、Google、ByteDance、Anthropic、Meta 等）。

爬虫：每个爬虫的运营方、带宽、请求数，以及允许还是阻止。

指标：请求随时间的变化、状态码分布、最常被抓的路径。引荐数据（哪些 AI 平台带来了访问、落在哪些路径）只有付费套餐有。

可以按日期、爬虫、运营方、主机名、路径过滤，能导出 CSV。

Googlebot 不在这里，它归机器人管理那边统计。

Cloudflare 2025 年年中公布过一组数：Google 大约每抓 14 个 HTML 页面带来 1 个访客，有的 AI 公司是 1,700 比 1，最高的一家是 73,000 比 1。放行哪些 AI 爬虫是个取舍，我们默认全放，靠第六节的「抓取回报比」看值不值。

### 4. Core Web Vitals

Web Analytics → Core Web Vitals。看 LCP、INP、CLS 的 P75，每个指标按 Google 的阈值分成良好、需要改进、较差三档。

每个图下面有调试视图，列出拖后腿最严重的 5 个页面元素。可以按 URL、浏览器、国家过滤，海外用户多的站按国家看特别有用。

CLS 只有 Chromium 内核的浏览器能测，Safari 和 Firefox 的用户不算在里面。

PageSpeed Insights 是实验室数据加 Chrome 用户体验报告（CrUX）的 28 天滚动数据，Cloudflare 这里是你自己站点的实时真实用户数据。改了东西想马上看效果，看 Cloudflare。

---

## 六、公式

先约定几个记号：

```text
C   点击次数（GSC）
I   展示次数（GSC）
P   平均排名（GSC）
q   一个查询词
p   一篇文章 / 一个页面
```

### 1. CTR 要用合计算，不要平均

```text
CTR = ΣC ÷ ΣI
```

几十个词的 CTR 直接取平均，展示只有 3 次的词和展示 3 万次的词权重一样，结果没法看。任何时候算一组词或一组页面的 CTR，都是先把点击和展示分别加起来，再相除。

### 2. 自己的 CTR 曲线

网上那些「第 1 名 CTR 27%、第 5 名 6%、第 10 名 2%」的数字，是别人很多站的平均，放到你的站上不一定对。有 AI 概览的查询，第 1 名的 CTR 已经低得多了：Ahrefs 2026 年 2 月的分析里，这类词第 1 名的 CTR 从 2023 年底的 7.3% 降到了 2025 年底的 1.6%。所以用自己的数据算：

```text
CTR_exp(k) = ΣC(q) ÷ ΣI(q)    其中 q 满足：非品牌词，且 round(P(q)) 落在第 k 档
```

取最近 3 个月，只用非品牌词，品牌词会把前几名的 CTR 抬得离谱。量小的站按单个排名分档样本不够，就分成四档：

```text
第 1 档：排名 1–3
第 2 档：排名 4–6
第 3 档：排名 7–10
第 4 档：排名 11–20
```

20 名以后展示太少，算出来没有意义。这条曲线每月重算一次，只拿来比较，不拿来预测。

### 3. CTR 差距

```text
CTR_gap(q) = CTR(q) ÷ CTR_exp(P(q) 所在档) − 1
```

排在第一页、差距低于 −30% 的词，说明排名够了，但搜索结果里那条摘要没人想点。改 title 和 description，不用改正文。

### 4. 机会点击

「临门一脚」的词：排在 4 到 15 名之间，再往上挪几名，点击会翻几倍。

```text
Opp(q) = I(q) × (CTR_exp(第 1 档) − CTR(q))      条件：4 ≤ P(q) ≤ 15，非品牌词
```

按 Opp 从大到小排，就是下个月的工作清单。再按排名把清单分成两类：

```text
P ≤ 10   且 CTR_gap < −30%   →  改标题、改摘要
P 11–15                       →  补内容、加内链，把排名往上推
```

举个例子：一个词排第 12，每月展示 3,000 次，CTR 1.2%，每月 36 次点击。推到第 4 名，按 7% 的 CTR 算，每月 210 次点击。

动手之前先看这个页面还排着哪些别的词，看最近 3 个月里这个词是不是在几个页面之间来回跳（关键词互相抢），平均排名还可能是不同国家的排名平均出来的，按国家拆开看一眼。

### 5. 索引率

```text
索引率 = 已编入索引的 URL 数 ÷ sitemap 里提交的 URL 数
```

Help 和营销页应该接近 100%，低了就去看「未编入索引」的原因。Blog 按月看，日报类的文章不一定每篇都被收，但索引率持续往下走，说明 Google 觉得这批文章不够好。

### 6. 收录时长和新文覆盖率

我们每天发 3 篇，最关心的是新文章多快能被搜到。

```text
T_index(p)   = GSC 里 p 第一次出现展示的日期 − p 的发布日期
新文覆盖率_7 = 发布 7 天内出现过展示的新文章数 ÷ 这段时间发布的新文章数
```

每周取当周所有新文章 T_index 的中位数。这个数在缩短，说明 Google 回访变勤了，Blog 的 feed 流和 sitemap 在起作用。变长了，先查新文章有没有进 sitemap、`/blog/` 第一页上有没有它。

GSC 数据晚两天，所以按周看，不按天看。

### 7. 抓取发现占比

```text
发现占比 = 抓取统计里「发现」类请求数 ÷ 总抓取请求数
```

来自 GSC 抓取统计的「按用途」。持续发新内容的站，这个比例应该稳定。突然掉下去，说明 Googlebot 找不到新 URL 了。

### 8. Googlebot 错误率

```text
Googlebot 5xx 率 = 返回 5xx 的 Googlebot 请求 ÷ Googlebot 请求总数
Googlebot 4xx 率 = 返回 4xx 的 Googlebot 请求 ÷ Googlebot 请求总数
```

来自 Cloudflare。5xx 应该接近 0，出现就要当天查。4xx 看趋势，删过页面有一些 404 是正常的，突然涨起来通常是改了 URL 没做跳转，或者有内链写错了。

### 9. 点击到会话比

```text
点击到会话比 = GA4 中 google / organic 的会话数 ÷ GSC 网页搜索的点击次数
```

这两个数不会相等。GA4 会漏掉拦截了脚本、没同意 cookie 的用户，一次点击也可能产生多个会话。但比例应该稳定在一个区间里。某天突然掉一大截，多半是 GA4 的代码掉了、同意弹窗改了，而不是 SEO 出了问题。

### 10. 非品牌点击占比

```text
非品牌占比 = 非品牌词点击 ÷ 总点击
```

这是衡量 SEO 本身有没有在长的主要指标。总点击涨了，但非品牌占比没涨，说明涨的是品牌知名度，不是搜索排名。

### 11. 自然搜索关键事件率

```text
关键事件率 = 自然搜索会话里触发的关键事件数 ÷ 自然搜索会话数
```

按落地页类型分组算（首页、Help、Blog、定价），比算一个全站总数有用。

### 12. 抓取回报比

```text
抓取回报比(运营方) = 该运营方爬虫的请求数 ÷ 该运营方平台带来的会话数
```

爬虫请求数来自 Cloudflare AI Crawl Control，会话数来自 GA4 的 AI 渠道（或者 Cloudflare 付费套餐的引荐数据）。Google 那边用 Googlebot 请求数除以自然搜索会话。这个比值越小，说明对方抓你的内容，回报给你的访问越多。某个爬虫抓了很多、什么都没带来，就可以考虑在 AI Crawl Control 里把它关掉。

### 13. 周环比和告警线

```text
Δ = (本周值 − 上周值) ÷ 上周值
```

日数据要和上周同一天比，不要和前一天比，周末和工作日的流量差很多。

我们用的告警线：自然搜索会话日环比（对比上周同一天）超过 ±20%，看一眼；点击周环比低于 −20%，要查原因。

### 14. 下滑页面

```text
页面变化(p) = p 最近 28 天的点击 ÷ p 前 28 天的点击 − 1
```

每月把低于 −30% 的页面拉出来，结合展示和排名看是哪种情况：

| 展示 | 排名 | 点击 | 多半是 |
| --- | --- | --- | --- |
| 稳 | 稳 | 掉 | 搜索结果页变了（多了 AI 概览、精选摘要），或者别人的标题更吸引人 |
| 掉 | 掉 | 掉 | 排名掉了：内容过时、别人写得更好、算法更新 |
| 掉 | 稳 | 掉 | 搜这个词的人少了，季节性或者热度过了 |
| 涨 | 变差 | 涨 | 新排上了一批长尾词，正常 |
| 掉 | — | 稳 | 可能是 GSC 统计问题，先查数据异常页 |

---

## 七、节奏

### 每天（5 分钟）

- Cloudflare：Googlebot 有没有 5xx，WAF 有没有误伤搜索引擎
- GA4：自然搜索会话和上周同一天比，有没有超过 ±20%
- 前天发的文章，在 GSC 网址检查里看一眼有没有被抓

### 每周（30 分钟）

- 填周报（模板在下一节）
- GSC 非品牌词：点击、展示、CTR，和上周比
- 本周新文章的收录时长中位数、7 天覆盖率
- 机会点击清单前 20 个词，挑 3 个动手
- GSC 网页索引报告里有没有新冒出来的错误

### 每月（1–2 小时）

- 重算自己的 CTR 曲线
- 下滑页面清单，按上面那张表分类处理
- 索引率，「未编入索引」各原因的数量变化
- 抓取统计：响应时间、发现占比、主机状态
- Core Web Vitals P75，按国家看最差的几个
- AI：生成式 AI 报告里被引用最多的页面、AI 渠道的会话和落地页、各 AI 爬虫的抓取回报比
- 对一遍 GSC 数据异常页面，把本月的改动补成注释

---

## 八、周报模板

每周一份，放在一张表里往下追加。

| 指标 | 公式 / 来源 | 本周 | 上周 | 变化 | 告警线 |
| --- | --- | --- | --- | --- | --- |
| 非品牌点击 | GSC，正则排除品牌词 | | | | 周环比 < −20% |
| 非品牌展示 | GSC | | | | |
| 非品牌 CTR | ΣC ÷ ΣI | | | | |
| 非品牌占比 | 非品牌点击 ÷ 总点击 | | | | 连续 4 周下降 |
| 自然搜索会话 | GA4，渠道 = 自然搜索 | | | | 周环比 < −20% |
| 点击到会话比 | GA4 google/organic 会话 ÷ GSC 点击 | | | | 偏离近 4 周均值 20% 以上 |
| 自然搜索互动率 | GA4 | | | | |
| 自然搜索关键事件率 | GA4 | | | | |
| 新文章数 | 发布记录 | | | | |
| 收录时长中位数 | 第一次展示日期 − 发布日期 | | | | 超过 7 天 |
| 新文 7 天覆盖率 | 7 天内有展示的新文 ÷ 新文 | | | | 低于 50% |
| 索引率 | 已编入索引 ÷ sitemap 提交数 | | | | Help 低于 95% |
| Googlebot 5xx 率 | Cloudflare | | | | 大于 0 |
| LCP P75 | Cloudflare Web Analytics | | | | 超过 2.5s |
| AI 展示 | GSC 生成式 AI 报告 | | | | |
| AI 来源会话 | GA4 AI 渠道 | | | | |
| 本周改动 | 同步写进 GSC 注释 | | | | |

告警线是我们自己定的，跑一两个月后按自己站点的波动范围调。

---

## 九、数据多了以后

界面够用就先用界面。等到词和页面多到界面看不过来，再把数据导出来算。

GSC 可以开 BigQuery 批量导出（设置 → 批量数据导出），每天自动写一份，没有 1000 行的限制，也没有 16 个月的限制，从开通那天起往后保留。GA4 也可以免费导出到 BigQuery。

在 BigQuery 里算第六节的 CTR 曲线，大概是这样：

```sql
-- 用 GSC 批量导出的 searchdata_site_impression 表
-- sum_top_position 从 0 开始计数，平均排名 = sum_top_position / impressions + 1
WITH q AS (
  SELECT
    query,
    SUM(clicks) AS clicks,
    SUM(impressions) AS impressions,
    SUM(sum_top_position) / SUM(impressions) + 1 AS position
  FROM `project.searchconsole.searchdata_site_impression`
  WHERE data_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 90 DAY)
    AND NOT is_anonymized_query
    AND NOT REGEXP_CONTAINS(LOWER(query), r'craftsail|craft sail|造物局')
  GROUP BY query
)
SELECT
  CASE
    WHEN position <= 3 THEN '1: 1-3'
    WHEN position <= 6 THEN '2: 4-6'
    WHEN position <= 10 THEN '3: 7-10'
    WHEN position <= 20 THEN '4: 11-20'
  END AS bucket,
  SUM(clicks) / SUM(impressions) AS ctr_exp,
  SUM(impressions) AS impressions
FROM q
WHERE position <= 20
GROUP BY bucket
ORDER BY bucket;
```

---

## 参考资料

### Google

- [Performance report (Search results)](https://support.google.com/webmasters/answer/7576553)
- [Crawl Stats report](https://support.google.com/webmasters/answer/9679690)
- [Generative AI performance report (Search)](https://support.google.com/webmasters/answer/16984139)
- [Introducing Search Generative AI performance reports](https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports)
- [Introducing the branded queries filter](https://developers.google.com/search/blog/2025/11/search-console-branded-filter)
- [Custom annotations in Search Console](https://developers.google.com/search/blog/2025/11/custom-chart-annotations)
- [Data anomalies in Search Console](https://support.google.com/webmasters/answer/6211453)

### Search Console 数据问题

- [Google Search Console fixes 50-week data logging issue（Search Engine Roundtable）](https://www.seroundtable.com/google-search-console-fix-data-logging-issue-41260.html)
- [Google Search Console anomalies in 2026: a timeline（Damteq）](https://www.damteq.co.uk/resources/articles/search-console-anomalies-2026/)
- [Google Search Console AI reports rolled out worldwide（Search Engine Journal）](https://www.searchenginejournal.com/google-search-console-ai-reports-rolled-out-worldwide/587836/)

### CTR 曲线与机会词

- [Striking distance keywords: which page 2 wins are real（Reachavo）](https://reachavo.com/blog/striking-distance-keywords)
- [Google Search Console audit: 2026 framework + SQL queries（thatdevpro）](https://www.thatdevpro.com/insights/framework-gscanalysis/)
- [Quick Wins Finder（GSC Wizard）](https://www.gscwizard.com/tools/quick-wins.html)

### GA4

- [How to use GA4 for SEO: 7 reports（Orbit Media）](https://www.orbitmedia.com/blog/ga4-seo/)
- [GA4 organic search traffic: the complete 2026 guide（The Sharp Digital）](https://www.thesharpdigital.com/blog/ga4-organic-search-traffic-the-complete-2026-guide)
- [The "AI traffic" channel in GA4（Terminus）](https://www.terminusapp.com/blog/ai-traffic-channel-in-ga4/)
- [How to track AI referral traffic in GA4（Orbit Media）](https://www.orbitmedia.com/blog/track-ai-traffic-ga4/)

### Cloudflare

- [Analyze AI traffic（AI Crawl Control 文档）](https://developers.cloudflare.com/ai-crawl-control/features/analyze-ai-traffic/)
- [Verified bots](https://developers.cloudflare.com/bots/concepts/bot/verified-bots/)
- [Verified bot categories](https://developers.cloudflare.com/bots/reference/verified-bot-categories)
- [Allow traffic from search engine bots and other verified bots](https://developers.cloudflare.com/waf/custom-rules/use-cases/allow-traffic-from-verified-bots/)
- [Core Web Vitals（Web Analytics 文档）](https://developers.cloudflare.com/web-analytics/data-metrics/core-web-vitals/)
- [A quick overview of Cloudflare Analytics](https://developers.cloudflare.com/analytics/faq/about-analytics/)
- [The crawl before the fall of referrals（Cloudflare Blog）](https://blog.cloudflare.com/ai-search-crawl-refer-ratio-on-radar/)
- [Cloudflare Analytics vs GA4: why the 5x difference?](https://ceaksan.com/en/premium/cloudflare-analytics-vs-ga4)
