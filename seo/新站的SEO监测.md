# 新站的 SEO 监测

[SEO 的监测](SEO的监测.md) 讲的是 Search Console、GA4、Cloudflare 各看什么、怎么算。网上大多数教程也是这个路子，默认你每天有几百上千个点击，可以看 CTR 曲线、拆品牌词、按查询词找机会。

刚起量的站不是这样。收录两三百到一千页，一天几十到几千次展示，点击个位数到几十，GA4 里自然搜索一天几个会话。照着大站的教程看，会遇到三件事：

- 报表里大半是空的，或者被隐藏了
- 数字小，今天涨 50%、明天跌 50%，都不说明问题
- 很多功能根本不给你开，比如品牌词过滤、查询组

这篇只讲这个阶段和大站不一样的地方：哪些数据不能信，哪些该看，按什么节奏看，什么时候算过了这一关。

---

## 一、这个阶段在问什么

大站的问题是「流量为什么掉了」「哪些词还能再涨」。新站的问题只有一个：**Google 认不认这个站**。

拆开是四个小问题，按顺序：

```text
新页能不能被发现和收录   →   收录的页有没有展示   →   展示在不在涨   →   有没有人点、点完干了什么
   （收录报告、网址检查）          （效果报告·网页）        （效果报告·周/月）        （效果报告、GA4）
```

前两个占这一阶段八成的精力。只要新文发布后几天内就被收录、开始有展示，点击迟早会来；收录这一步卡住，后面怎么调标题都没用。

Google 的 John Mueller 说过，Google 要弄清一个新站「在整个互联网里处于什么位置」，可能要几个月、半年，有时更久。Gary Illyes 在 2026 年 10 月 Search Central Live（巴塞罗那）上给过一组内部统计的时长（以下来自参会者整理，不是官方文档）：

| 过程 | 一般情况 | 慢的时候 |
| --- | --- | --- |
| 发现一个新 URL | 约 20 小时 | 几周，或者永远不发现 |
| 处理一份 sitemap | 约 24 小时 | 最长 14 天，质量差可能不处理 |
| 从抓取到编入索引 | 约 1.5 小时 | 几个月，或者永远不收 |
| 改标题、摘要后生效 | 1–2 天 | 几周到几个月 |
| 从核心更新里恢复 | 3–6 个月 | 半年到一年 |

「永远不」那一列，原因几乎都是质量。技术问题修得快，质量问题只能慢慢证明。

---

## 二、哪些数据在小站上不能信

### 1. 查询词大部分是隐藏的

Search Console 为了隐私，不显示搜的人太少的查询（匿名查询）。点击和展示还算在总数里，只是表格里看不到是哪个词。判断标准是这个词在全球被多少人搜过，不是你拿到了多少点击。新站做的恰恰是长尾词，所以被藏得最多。

- Ahrefs 统计了 88 万多个 GSC 资源（2025 年 4 月），匿名查询占点击的 46.77%，大量网站在 45%–80% 之间
- SEO Gets 看了 9 个站（样本很小，只能当参考），小站能对上查询词的点击平均只有 18.3%，范围 0%–37%；大站平均 76.3%
- 同一组数据里，**按网页看的点击基本是全的**，小站在 97%–101% 之间

自己核对：效果报告图上的总点击，减去「查询」表里所有行的点击之和，差的就是被藏起来的。小站差一大半很正常。

还有两个坑：

- 在查询维度上加任何筛选（包括「包含」「正则」），匿名查询会整体消失，总数会变小。所以按正则排除品牌词以后，非品牌点击是偏少的
- 表格最多显示 1,000 行。新站一般到不了这个数，到了就用 API 或 BigQuery 导出

**结论：新站看效果报告，主维度是「网页」，不是「查询」。** 查询表只当线索看，别拿它算总量。

### 2. 平均排名会骗人

平均排名是「每次展示里，你站最靠前那条结果的位置」，再按展示次数加权平均。只有真被看到（产生了展示）才算数，排在第 5 页没人翻到的不计入。

放到新站上：

- 一个页面一周 12 次展示，平均排名 8.3，这个数几乎没意义，下周换几次展示就变成 15
- 新页开始给更多长尾词展示，往往排得更靠后，平均排名会变差，但其实是好事
- 图上那条站点级平均排名线，在新站上主要反映「这周新出现了多少靠后的词」，不要盯

要看排名，就挑展示够多的单个「网页 × 查询」组合看。

### 3. CTR 样本太小

CTR 的置信区间在小样本下非常宽。按 95% Wilson 区间算：

| 展示 | 点击 | 看到的 CTR | 真实 CTR 大概在 |
| --- | --- | --- | --- |
| 100 | 3 | 3% | 1.0% – 8.5% |
| 1,000 | 30 | 3% | 2.1% – 4.3% |

100 次展示的 CTR，从 1% 到 8.5% 都说得通。改了标题以后 CTR 从 2% 变成 5%，不一定是标题起了作用。我们的规矩是：**单页累计展示不到 500 次，不拿 CTR 下结论**；要比改标题前后，至少各攒 28 天。

### 4. 你够不着的功能

| 功能 | 新站的情况 | 替代办法 |
| --- | --- | --- |
| 品牌词过滤（Branded / Non-branded） | 官方文档写明展示量低的站点不提供，没公布门槛，也不能手动加品牌词 | 查询维度用自定义正则排除品牌词，记住这样会漏掉匿名查询 |
| 查询组（Search Console Insights） | 官方说只给查询量大的资源 | 自己按网页或目录分组看 |
| 生成式 AI 报告 | 2026 年 6 月开始分批开放 | 等；先看 GA4 的 AI 渠道 |

这几个功能什么时候出现，本身就是一个信号，后面第七节会讲。

### 5. GA4 会藏数据，也会漏数据

**藏：数据阈值。** 开了 Google 信号、报告身份用的是「混合」或「观察到的」时，用户数少的行会被隐藏，报表右上角会出现一个三角形提示。Google 不公布门槛，流量越小越容易触发。

解决办法：管理 → 数据显示 → 报告身份 → 改成「基于设备」。这只改报表的计算方式，不动原始数据，随时可以改回来。新站一般没有 User-ID，也用不上跨设备，基于设备就够了。

**漏：广告拦截。** uBlock Origin、Brave 默认拦 GA4 脚本，被拦的访问不会以任何形式进 GA4。面向开发者的站被拦得特别多，各家估算从 25% 到 60% 不等（多数来自卖替代分析工具的厂商，数字偏高，但方向没错）。

所以新站要有第二个数来对：Cloudflare Web Analytics 或者服务端日志。GA4 和它的比例稳定就行，不用追求对上。

**放大：自己的访问。** 一天 20 个会话的站，你自己和同事刷几次就是三分之一。内部流量过滤（见 [SEO 的监测](SEO的监测.md) 第二节）在新站上不是可选项。

### 6. 数据有延迟

效果报告的数据一般 2–3 天后才稳定，最新那一两天是初步数据，之后还会变。24 小时视图里全是初步数据。新站每天的数字本来就小，再赶上未完成的数据，很容易看到假的「暴跌」。**新站不看昨天，看上周。**

### 7. `site:` 搜索不是收录数

`site:example.com` 给的数字是为速度优化的估算。Mueller 举过例子：Search Console 里 170 个有效页，`site:` 只显示三五个。也可能反过来大很多。收录数只看 Search Console 的「网页索引」和「站点地图」报告。

---

## 三、收录：这一阶段最该看的

### 1. 先把 sitemap 拆开

收录报告默认看全站，几百页混在一起，看不出是哪一块出了问题。把 sitemap index 按内容类型拆成几个文件，分别提交，比如：

```text
sitemap-index.xml
├── sitemap-pages.xml     首页、关于、产品页
├── sitemap-help.xml      文档
└── sitemap-blog.xml      文章
```

然后在「网页索引」报告左上角的下拉里，可以只看某一个 sitemap 的收录情况。这样就能分别算每一块的索引率：

```text
索引率 = 某 sitemap 里已编入索引的 URL ÷ 该 sitemap 提交的 URL
```

一般来说，文档、产品页这类内容稳定、内链多的，索引率应该很高；文章、聚合、程序化生成的页面，索引率低得多。哪一块明显偏低，问题就在哪一块。sitemap 怎么拆、`lastmod` 怎么写，见 [SEO 落地](SEO落地.md) 第八节。

### 2. 两种「未编入索引」，在新站上意思不一样

| 状态 | 意思 | 新站上多半是 | 怎么办 |
| --- | --- | --- | --- |
| 已发现 - 尚未编入索引 | 知道有这个 URL，还没来抓 | 全站还没被信任，排队靠后；或者这批页内链太少、埋得太深 | 给它们加内链，从已经收录、有展示的页链过去；别急着申请编入索引 |
| 已抓取 - 尚未编入索引 | 抓了，看过了，决定先不收 | 内容太薄，或者和站内、站外已有的页太像 | 改内容、合并、或者 `noindex`；改完点「验证修正」 |

这里最容易走错的一步是去查「抓取预算」。Google 的文档写得很清楚，需要管抓取预算的是三类站：100 万页以上且每周都有变化的；1 万页以上且每天大量变化的；以及「已发现 - 尚未编入索引」占比很大的。1,000 页以内的站，就算落在第三类，原因也基本不是抓取能力，而是 Google 对全站质量的判断。Mueller 的原话大意是：这看的是你整个网站的质量，包括布局和设计；如果站里有大量低质量页面，爬虫可能就不太想收这个站的页面。

所以新站「未编入索引」多的时候，先问这几件事：

- 有没有一批模板页，只换了一个变量（城市名、工具名、日期）？
- 有没有每条 RSS、每个标签都生成了一个页面？
- 这些页有没有从站内别的地方链进来？
- 去掉这批页，剩下的站是不是更像一个「有东西」的站？

程序化页面和 AI 批量生成的页面要格外小心。Google 的垃圾内容政策里有「规模化内容滥用（scaled content abuse）」，不管是人写还是 AI 写，只要是为了排名大批量生产、对用户没增加价值的页面都算。2026 年 8 月和 9 月的两次垃圾内容更新都把它列在范围里。新站本来就没有信任，一批薄页很容易把整个站拖下去。

### 3. 「请求编入索引」省着用

网址检查里的「请求编入索引」每个资源每天有上限，Google 不公布数字，实践里一般是 10 个左右，有人 5 个就被挡了。它只是把 URL 放进优先抓取队列，不保证收录。Mueller 的态度是，一个站需要频繁手动提交，本身可能说明 Google 对它的质量有疑虑。

新站这样用：

- 网站刚上线：提交首页，让 Google 开始抓
- 重要的新页面（产品页、核心文档）：发布当天提交
- 普通文章：靠 sitemap 和内链，不手动提交
- 「已抓取 - 尚未编入索引」：改完内容再提交，没改不要提

批量提交工具、浏览器插件都绕不过这个上限。Indexing API 只给招聘和直播页面用，不是通用入口。

### 4. 每天把所有页检查一遍

这是小站独有的优势。URL Inspection API 每个资源每天可以查 2,000 次、每分钟 600 次。1,000 页以内的站，每天把 sitemap 里所有 URL 跑一遍都用不完配额。

每个 URL 记下这几个字段，存成一张表，每天一行：

| 字段 | 位置 | 用来干什么 |
| --- | --- | --- |
| `coverageState` | `indexStatusResult` | 收录状态，就是报告里那几种原因 |
| `lastCrawlTime` | `indexStatusResult` | 最近一次被抓的时间，看哪些页一直没人来 |
| `googleCanonical` / `userCanonical` | `indexStatusResult` | 两者不同，说明 Google 没听你的 canonical |
| `verdict` | `indexStatusResult` | PASS / NEUTRAL / FAIL |

注意这个 API 只能查，不能提交收录。

有了这张表，就能算出报告里给不了的东西：

```text
收录时长     = 第一次 coverageState 变成「已提交并编入索引」的日期 − 发布日期
7 天收录率   = 发布 7 天内被编入索引的新页 ÷ 这段时间发布的新页
从未被抓取   = sitemap 里超过 14 天、lastCrawlTime 仍为空的 URL
```

这三个数在新站阶段比点击更重要。收录时长越来越短，说明 Google 来得越来越勤、对站越来越信任。

### 5. 抓取统计：看 Googlebot 来得勤不勤

设置 → 抓取统计信息。新站要看的不是总量，是两件事：

- 每天的抓取请求数是不是在慢慢涨。新站一开始一天几十次很正常
- 「按用途」里「发现」占多少。发现占比高，说明 Google 在主动找新页；几乎全是「刷新」，说明它只是在复查老页面

响应时间和 5xx 也看一眼。新站常用便宜主机或冷启动的 Serverless，Googlebot 来的时候慢或者报错，它就会少来。

---

## 四、展示：先看展示，再看点击

### 1. 有展示的网页数

新站在效果报告里最有用的一个数，不是点击，而是**过去 28 天里至少有 1 次展示的网页数**：

```text
展示覆盖率 = 有展示的网页数 ÷ 已编入索引的网页数
```

收录了不代表有展示。被收录但从来没出现在任何搜索结果里的页，等于没有。展示覆盖率在涨，说明越来越多的页被 Google 拿去回答问题了，哪怕排得很靠后。

方法：效果报告 → 网页 → 日期选 28 天 → 导出，数行数；再和 sitemap 对一下，找出收录了但 28 天零展示的页。这些页多半是选题没人搜，或者和别的页抢同一批词。

### 2. 每篇新文的「第一次展示」

新文发布后，每天在效果报告里按网页筛一下（或者用 API 拉），记下它第一次出现展示的日期。和上一节的收录时长放在一起：

```text
发布 → 收录（URL Inspection API） → 第一次展示（效果报告）
```

收录了好几天还没展示，说明这篇在 Google 眼里排得很靠后，或者没找到对应的搜索需求。

### 3. 用周和月看趋势

新站按天看，曲线就是锯齿。效果报告的图表可以切成按周、按月汇总，日期选 3 个月或 6 个月。要看的只有一件事：**展示的每周总数，是不是一周比一周高**。

点击在这个阶段跟着展示走，滞后几周。展示稳步涨、点击没动，不要慌；展示几周不涨，才需要回头看收录和内容。

### 4. 查询表还是要看，但用法不同

查询词大多被藏了，剩下能看到的那部分是线索：

- 有没有出现你没写过、但相关的词。这是下一篇文章的选题
- 哪个页面在给哪些词展示。和你写这页时想的词对不上，就改标题和开头那一段，让它更贴近实际被搜的词
- 有没有排在第 11–20 名、展示还不少的词。这些词稍微改一下就可能上第一页，方法见 [Striking Distance Keywords](../reference/striking_distance_keywords_which_page_2_wins_are_read/striking_distance_keywords_which_page_2_wins_are_read_zh.md)

但新站这类词很少，有几个就做几个，不要拿大站的「每周找 20 个机会词」来要求自己。

---

## 五、GA4：数小，就看人，不看率

### 1. 先做三件事

1. 报告身份改成「基于设备」（见第二节）
2. 定义内部流量并启用过滤
3. 在 GA4 里关联 Search Console

关联以后 GA4 会多出「查询」和「Google 自然搜索流量」两个报表。它们的数据来自 Search Console，同样有匿名查询的问题，新站直接去 Search Console 看就行。

### 2. 率先别看，看绝对数

一个月 3 个注册、60 个自然搜索会话，转化率 5%，下个月 2 个注册、40 个会话，还是 5%，这个「率」什么也没告诉你。新站的周报里写绝对数：

- 自然搜索会话：本周多少，最近 4 周各多少
- 关键事件：本周发生几次，分别来自哪个落地页

### 3. 一个人一个人地看

流量小的时候，最有价值的是 GA4 探索里的**用户探索（User explorer）**。挑几个从自然搜索进来、触发了关键事件的用户，点进去看他的完整事件流：从哪一页进来，看了什么，停了多久，最后点了什么。

大站要靠统计找规律，新站可以直接看每一个真实的人。10 个人的路径看完，比任何漏斗报表都清楚。

### 4. 早期大部分流量不是来自 Google

刚起步的站，Google 自然搜索通常不是第一大来源。目录站、Hacker News、Reddit、X、newsletter、AI 助手的引用，往往更多。流量获取报告里「引荐」那一行展开到会话来源，按来源一个个看。哪个渠道来的人互动率高、触发了关键事件，就多在那里露面。

AI 来源单独建一个渠道，正则见 [SEO 的监测](SEO的监测.md) 第二节。

---

## 六、顺手做：Bing Webmaster Tools 和 IndexNow

新站值得花 10 分钟把 Bing 也接上：

- Bing Webmaster Tools 可以直接从 Search Console 导入站点，不用重新验证
- 提交同一份 sitemap
- 接 IndexNow：根目录放一个 key 文件，发布或更新页面时推一下 URL，Bing 和其他支持 IndexNow 的搜索引擎会很快来抓。Astro、WordPress 都有现成插件

Bing 自己的份额不大，但很多观察认为 ChatGPT 搜索、Copilot 的结果很大程度来自 Bing 的索引（这一点 OpenAI 没有公开确认）。Bing Webmaster Tools 从 2026 年 2 月起有 AI Performance 报告（预览），能看到页面被 Copilot 和 Bing AI 摘要引用的次数。对新站来说，这是少数几个能直接看到「AI 有没有引用我」的地方。

---

## 七、节奏

新站数据少，看得太勤只会被噪声牵着走。

### 发布当天

- 新页能打开，返回 200，`<head>` 对，在 sitemap 里
- 重要页面在网址检查里点一次「测试实际网址」，再「请求编入索引」

### 每天（自动，不用人看）

- 脚本用 URL Inspection API 跑一遍 sitemap 里的所有 URL，写进表
- 只在出现异常时提醒：有页面从「已编入索引」掉出去；Googlebot 5xx；某个 sitemap 的索引率一天掉了 5 个百分点以上

### 每周（30 分钟）

- 各 sitemap 的索引率、本周新页的收录时长
- 效果报告按周看：展示总数、有展示的网页数
- 本周新出现展示的页面，和它们的查询词
- 「已抓取 - 尚未编入索引」里新增了什么，属于哪一块
- GA4：自然搜索会话、关键事件绝对数；挑 3–5 个用户看路径

### 每月（1 小时）

- 28 天零展示的已收录页：改、合并，还是 `noindex`
- 回头看一个月前改过标题的页面，展示和排名有没有变化（样本够了再看 CTR）
- 核对一下品牌词过滤、查询组、AI 报告有没有出现

### 周报模板

| 指标 | 来源 | 本周 | 上周 | 4 周前 |
| --- | --- | --- | --- | --- |
| sitemap 提交 URL 数 | sitemap | | | |
| 已编入索引 | GSC 网页索引 | | | |
| 索引率（分 sitemap） | 已编入索引 ÷ 提交 | | | |
| 新页数 | 发布记录 | | | |
| 收录时长中位数 | URL Inspection API | | | |
| 7 天收录率 | URL Inspection API | | | |
| 已抓取 - 尚未编入索引 | GSC 网页索引 | | | |
| 已发现 - 尚未编入索引 | GSC 网页索引 | | | |
| 有展示的网页数（28 天） | GSC 效果报告 · 网页 | | | |
| 展示覆盖率 | 有展示网页 ÷ 已编入索引 | | | |
| 展示（周） | GSC | | | |
| 点击（周） | GSC | | | |
| Googlebot 每日抓取请求 | GSC 抓取统计 | | | |
| 自然搜索会话 | GA4 | | | |
| 关键事件次数 | GA4 | | | |
| GA4 ÷ Cloudflare 访问比 | GA4、Cloudflare Web Analytics | | | |
| 本周改动 | 同步写进 GSC 注释 | | | |

和 [SEO 的监测](SEO的监测.md) 里的周报比，这一版把 CTR、非品牌占比、周环比告警线都去掉了，加了收录相关的几行。数字小的时候，周环比 −20% 可能只是少了 3 个点击。

---

## 八、什么时候算过了这一关

没有官方标准，下面是我们自己用的信号，出现得越多，越说明 Google 已经把这个站当成一个正常的站：

| 信号 | 说明 |
| --- | --- |
| 新页 1–3 天内收录，并开始有展示 | Google 来得勤，且愿意收 |
| 各 sitemap 的索引率稳定在高位，不再上下跳 | 全站质量判断稳定了 |
| 「已发现 - 尚未编入索引」持续减少 | 排队靠前了 |
| 抓取统计里每天的请求数明显高于刚上线时 | 抓取需求上来了 |
| 有展示的网页数占已收录的大多数 | 收录的页基本都在被用 |
| 图上总点击和查询表点击之和的差距在缩小 | 你开始排上有人搜的词，不只是长尾 |
| GSC 里出现了品牌词过滤 | 展示量过了 Google 的门槛，品牌被识别了 |
| GA4 不再频繁出现阈值提示 | 用户量上来了 |

到这一步，就可以换回 [SEO 的监测](SEO的监测.md) 那一套：看 CTR 曲线、拆品牌词、按查询找机会点击、设周环比告警。

---

## 九、常见误判

| 看到的 | 以为是 | 多半是 |
| --- | --- | --- |
| 查询表里点击加起来只有图上的三分之一 | GSC 坏了 | 匿名查询，小站正常 |
| `site:` 只搜出 30 条，GSC 说收录 300 | 被降权了 | `site:` 本来就不准 |
| 平均排名从 12 变成 25 | 排名掉了 | 新页开始给更多靠后的长尾词展示 |
| 改了标题，CTR 从 2% 到 6% | 标题改对了 | 一周 50 次展示，样本太小 |
| 「已发现 - 尚未编入索引」几百个 | 抓取预算不够 | 全站质量或内链问题 |
| 昨天点击掉了一半 | 出事了 | 最近两天是初步数据，或者只是 6 个变 3 个 |
| GA4 自然搜索会话比 GSC 点击少很多 | 跟踪坏了 | 广告拦截、同意横幅、一次会话多次点击；比例稳定就行 |
| GA4 报表里很多行不见了 | 数据丢了 | 数据阈值，改成基于设备的报告身份 |
| 没有品牌词过滤 | 没开对 | 展示量还没到门槛 |
| 请求编入索引按钮灰了 | 被惩罚了 | 当天配额用完了 |

---

## 参考资料

### Google 官方

- [Crawl budget management](https://developers.google.com/crawling/docs/crawl-budget)（哪些站需要管抓取预算）
- [Build and submit a sitemap](https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap)（`lastmod` 的要求）
- [Troubleshoot Google Search crawling errors](https://developers.google.com/search/docs/crawling-indexing/troubleshoot-crawling-errors)
- [Page indexing report](https://support.google.com/webmasters/answer/7440203)
- [URL Inspection tool](https://support.google.com/webmasters/answer/9012289)
- [Performance report](https://support.google.com/webmasters/answer/7576553)
- [Search Console data: about the data](https://support.google.com/webmasters/answer/96568)（匿名查询、1,000 行上限、2–3 天延迟）
- [Spam policies for Google web search](https://developers.google.com/search/docs/essentials/spam-policies)（规模化内容滥用）

### 匿名查询和平均排名

- [Ahrefs: Anonymized queries make up nearly half of GSC traffic](https://ahrefs.com/blog/gsc-anonymized-queries/)
- [SEO Gets: What are anonymous queries](https://seogets.com/blog/what-are-anonymous-queries)（小、中、大站对比，9 个站）
- [Search Engine Land: Google removes "very rare" language from help doc](https://searchengineland.com/google-removes-language-in-help-doc-calling-hidden-search-console-query-data-very-rare-386328)
- [SEOTesting: What does average position mean](https://seotesting.com/google-search-console/average-position/)

### 收录与新站

- [Search Engine Journal: Google shows how long crawling, indexing & recovery can take](https://www.searchenginejournal.com/google-crawling-indexing-recovery-timing-ranges/591829/)（Gary Illyes，2026 年 10 月）
- [Search Engine Journal: Google on fixing Discovered Currently Not Indexed](https://www.searchenginejournal.com/fixing-discovered-currently-not-indexed/491432/)
- [Search Engine Journal: A site: search doesn't show all pages](https://www.searchenginejournal.com/google-a-site-search-doesnt-show-all-pages/416662/)
- [PPC Land: Explaining URL Inspection tool](https://ppc.land/url-inspection-tool/)（请求编入索引的配额）
- [PPC Land: Explaining scaled content abuse](https://ppc.land/scaled-content-abuse/)

### Search Console 新功能

- [Search Engine Journal: Google answers questions about the branded queries filter](https://www.searchenginejournal.com/google-answers-questions-about-search-consoles-branded-queries-filter/569549/)
- [Search Engine Journal: Google uses AI to group queries in Search Console](https://www.searchenginejournal.com/google-uses-ai-to-group-queries-in-search-console-data/559360/)

### GA4

- [PMG: Reporting identity in GA4](https://www.pmg.com/insights-and-news/reporting-identity-in-google-analytics-4)
- [Optimize Smart: Fixing data threshold issue in GA4](https://optimizesmart.com/blog/fixing-data-threshold-issue-in-google-analytics-4-ga4/)
- [Logly: Why Google Analytics numbers are lower than reality](https://logly.uk/blog/why-google-analytics-underreports/)

### Bing

- [Bing Webmaster Tools: setup, sitemaps & IndexNow](https://jetfuel.agency/how-to-set-up-bing-webmaster-tools-for-your-site-step-by-step-guide/)
- [IndexNow](https://www.indexnow.org/)
