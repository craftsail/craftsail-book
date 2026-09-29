> https://www.orbitmedia.com/blog/ga4-seo/

排名其实不是 SEO 的目标，流量才是。这意味着我们需要用 GA4 来做 SEO 报告，因为 SEO 工具里没有流量数据。

流量也不是 SEO 的终极目标，获客（线索）才是。这意味着我们需要能转化为线索的优质流量。这同样意味着我们需要用 GA4，因为转化数据就在那里。

欢迎阅读这份用 Google Analytics 追踪 SEO 表现的迷你指南。我们整理了七个报告，演示如何用 GA4 做 SEO 分析，从顶层的流量到底层的获客，全面展示你在 Google 搜索中的表现。

我们会尽量保持简单，只使用主报告区域。不需要 GA4 的探索（Explorations），也不需要在 Looker Studio 里搭建自定义 SEO 看板。全部使用标准指标和基础筛选器完成，无需任何复杂配置。

只靠 GA4 数据，怎么检查搜索表现？方法如下……

* 1 追踪来自搜索的整体流量变化
* 2 追踪特定 URL 的搜索流量
* 3 查看 SEO 流量的转化率
* 4 查看特定 URL 的 SEO 流量转化率*
* 5 查看低排名页面的互动率
* 6 追踪点击你 Google 商家资料（Google Business Profile）的访客
* 7 设置搜索流量下跌的邮件通知

* 这一项能带来非常有价值的洞察

## 1. 用 GA4 追踪 SEO 带来的整体流量
**Acquisition（获客）> Traffic acquisition（流量获取）：对比日期范围**

首先回答最基本的问题：网站从搜索获得了多少流量？搜索总流量是涨了还是跌了？

这个 GA4 报告可能是最不具备可操作性的，但它展示了全局。它显示所有 URL 的全部搜索流量，配合日期范围对比，你可以快速看出网站是否在朝正确的方向发展。

操作又快又简单。进入 Acquisition > Traffic Acquisition，选择日期范围，打开 "Compare"（对比）开关即可。然后查看 "Organic Search"（自然搜索）那一行的 % 变化，就能知道搜索总流量是涨还是跌。
![image](../../attachments/pasted-muko0dlc-1.png)

*注意：选择日期范围时要留意季节性。同比（年对年）更好，但这会让衡量近期工作的效果变得困难。多试几个日期范围才能看到完整的情况。还要有耐心，SEO 调整见效需要时间，往往要好几个月。*

这是对 SEO 健康度的快速检查，但它说明不了全部问题。这个数字把网站所有页面的所有搜索流量加在一起，是一个汇总值。在上面的报告中，搜索流量环比看起来持平，但不同 URL 的搜索流量可能在大涨或大跌。

所以，我们来看看 GA4 中页面级别的自然流量变化。因为 GA4 中真正的洞察总是藏在更深一层。

## 2. 查看特定页面的搜索流量变化
**Engagement（互动）> Landing page（着陆页）：筛选 session medium 完全匹配 organic，对比日期范围**

有些页面比其他页面更重要。落地在服务介绍页的访客，成为线索的可能性往往是从博客文章进入的访客的 10 倍。流量并不都是等价的。

如果博客的搜索流量上涨了，但首页的搜索流量下跌了，GA4 的自然搜索流量可能整体是上涨的！……你会很开心。但实际上，你的合格访客变少了……你根本不该开心。

我们需要深入一层，按 URL 查看自然搜索流量的变化。方法如下：

* 进入 Engagement > Landing page
* 使用日期范围对比（试试"上一周期"和"去年同期"）
* 点击 "Add filter +"，设置筛选器只显示来自搜索的流量
      Dimension（维度）："Session medium"
      Match Type（匹配类型）："exactly matches"（完全匹配）
      Value（值）："organic"
* 点击 "Apply"（应用）
*注：在筛选器中使用其他维度也能得到类似结果：Session primary channel group、First user primary channel group、First user medium。*
![image](../../attachments/pasted-muko4t59-1.png)

这是一个非常有用的 GA4 SEO 报告！

现在你看到的是每个具体页面的搜索流量变化。你会立刻发现，在任何日期范围内都有大赢家和大输家。页面级别的波动非常大，而只看整体搜索流量时，这些波动都被掩盖了。

现在你拿到了更具可操作性的 SEO 洞察。

## 当某个页面的自然流量下跌时……
SEO 分析通常涉及以下问题。我根据自己的经验，按可能的有用程度排序列出。

* 仔细检查该页面的内容
*是否缺少某些答案？细节？怎样能让它更好？*
* 查看该页面排名的关键词（Google Search Console、Semrush、Ahrefs）
*标题、小标题和正文是否与这些关键词相匹配？*
* 寻找其他主题相关的页面
*有没有页面可以链接到这个页面？*
* 查看该主题的需求（Google Trends）
*流量下跌是季节性的吗？*
* 查看该 URL 的页面速度（GT Metrix、Google PageSpeed Insights）
*有什么拖慢了这个页面吗？*
* 搜索相关关键词，查看搜索引擎结果页（SERP）
*是否在和新的 SERP 功能竞争？*
无论诊断结果如何，应对流量下跌的处方往往是一样的：页面内 SEO 优化、内部链接、数字公关与外链建设，以及检查技术 SEO 因素。但对于 SERP 本身的变化，你无能为力。

## 当某个页面的自然流量上涨时……
庆祝完之后，想办法把这次胜利的价值最大化。

* 检查内容中的内部链接（尤其是博客文章）
*这篇文章是否应该链接到其他页面？比如某个转化率很高的页面？*
* 检查行动号召（CTA）（销售页用"联系我们"，博客文章用"订阅"）
*这个页面是否在引导访客走向转化？*
* 考虑相关主题（尤其是博客文章）
*能否写一篇类似的内容，瞄准一个相邻的关键词？*

别指望在 Google Analytics 里找到关键词数据。关键词数据需要用 Google Search Console 或追踪 SEO 活动的付费工具。但一旦你掌握了在 Google Analytics 中查找自然搜索流量的方法，就该沿着漏斗往下走，查看互动指标和转化了。

## 3. 查看自然搜索流量的转化率
*Acquisition > Traffic acquisition：自定义报告，添加"转化率"指标*

有些 SEO 从业者只关注排名和流量。但最优秀的 SEO 关注的是流量质量和转化率，他们关心的是线索。

> Eli Schwartz，《Product-Led SEO》作者
>
> "实际上，你可以忽略外部 SEO 工具的任何数据，因为 SEO 的成功不应以你出现在搜索结果页的哪个位置来衡量——那只是一个假想用户搜索了一个你期望的关键词时可能看到的页面。只有当你的理想客户点击了这些结果并落地到你的网站时，关键词可见度才有意义。"

那么，我们一起沿着漏斗往下走。

要查看自然流量的整体转化率，可以使用上面的同一个报告，但首先要确保转化率是其中的一个指标。这意味着需要自定义报告来显示该指标。方法如下：

* 进入 Acquisition > Traffic acquisition
* 点击右上角的铅笔图标
* 在 Customize Report（自定义报告）窗口中，点击 "Metrics"（指标）
* 在列表末尾的 "Add metric"（添加指标）框中搜索 "rate"，选择 "Session conversion rate"（会话转化率）
* 点击 Apply
*注：不用在意日期范围对比。按流量来源划分的转化率随时间波动不大。不过如果好奇，可以自己验证一下。*

现在你可以看到每个渠道组的转化率了。在下面的示例中，我选择了我最喜欢的指标，并按我喜欢的顺序排列（Users、Sessions、Engagement rate、Avg engagement time、Session conversion rate、Conversions）。
![image](../../attachments/pasted-mukxda0c-1.png)

但这个报告有个问题：它把所有类型的转化都混在了一起，包括联系表单提交（线索）和邮件订阅（订阅者）。商业意图和信息意图这两类访客意图被混在了一起。

整体转化率可能因为订阅者很多而看起来很高，但网站实际产生的线索可能很少。或者转化率可能因为博客流量很大而看起来很低，但网站实际上产生了大量合格线索。所以再次强调，汇总数据通常具有误导性。

我们深入一层，分别衡量这两类转化。

## 自然搜索访客的邮件订阅转化率是多少？

更好的做法是分别查看具有某一种意图、完成某一种转化的访客。例如，要查看从博客文章进入的自然搜索访客订阅邮件的比例，再多做两步：

* 点击 "Add filter +"，设置筛选器只显示从博客文章进入的访客（见下方截图）
     Dimension："Landing page + query string"
     Match Type："contains"（包含）
     Value："blog"
* 在 "Session conversion rate" 下方的下拉菜单中选择邮件订阅转化类型
*注：这里假设所有博客内容都在同一目录下（本例中为 /blog/），当然，前提是转化已正确配置！*
![image](../../attachments/pasted-mukxfg1i-1.png)
上面报告中的互动率也很有意思。在这个账户中，落地在博客文章上的自然搜索访客比其他流量来源的访客互动性更高，达到 72%，远高于平均水平。平均互动率为 55%。

## 自然搜索访客的获客转化率是多少？
我们来到漏斗底部，检查 SEO 真正的 ROI：营销合格线索（MQL）。

但搜索访客的获客转化率被那些低意图的博客读者拉偏了。这些访客从来没有使用我们服务的意图，所以应该先把他们从数据中剔除。

要排除从博客文章开始访问的访客，把筛选器改为显示不是从博客文章开始访问的访客（Landing page 不包含 blog）。

接着，在 "Session conversion rate" 指标下方的下拉菜单中选择你的获客转化事件。在下面的截图中可以看到，我们的转化事件叫 "contact_lead"，你的可能叫别的名字。
![image](../../attachments/pasted-muky4nwc-1.png)

**注意：GA4 并不总是准确的！**

上面的报告还显示了总转化次数，但这个数字并非 100% 准确。GA4 总是会少报流量和转化。这是因为有些访客没有接受 Cookie 同意横幅中的 Cookie，有些使用了隐私工具或处于无痕模式……或者他们直接打了电话。转化率大概没问题，但不要用 GA4 来衡量转化总数。

要查看更准确的转化数，请查看你的 CRM（它应该已与联系表单打通）。把这些数字与 GA4 中的数字对比，就能估算出你的 Analytics 账户的准确度。我们上次检查时，有 20% 的转化没有被 GA4 记录下来。

对于电商网站，这些洞察可能更有价值，尤其是当你把 GA4 与 Google Merchant Center 关联起来时。分析专家 Brie Anderson 解释道：

> Brie Anderson，BEAST Analytics
> "做电商的朋友们——一定要把你的 GA4 媒体资源关联到 Merchant Center。关联之后，你会看到一个叫 'Organic Shopping'（自然购物）的渠道组。然后把默认维度从 'channel group' 改为 'session source/medium'，这样就会按搜索引擎拆分引荐流量。再添加一个次级维度 'session campaign'。
>
> 同时使用这两个维度可以获得很有价值的洞察。你不仅能看到哪些搜索引擎在带来流量，还能看到每个来源的用户互动程度如何。次级维度 'session campaign' 还能进一步区分 Merchant Center 的自然流量，这类流量会被自动打上 'Free Shopping Listings'（免费购物商品详情）的广告系列名称。"

## 4. 按着陆页查看自然流量的转化率
Engagement > Landing page：筛选 session medium 完全匹配 organic，自定义报告添加"转化率"指标

SEO 是一场逐页展开的战斗。每个关键词都是一场竞争，每个页面都是一个参赛者。但你应该重点关注哪些参赛者？哪些页面带来的影响最大？每个 URL 的转化率是多少？

了解这些能帮你确定 SEO 工作的重点。

这其实就是上面第 2 个报告中创建的那个报告，只是去掉了日期范围对比，并加上了转化率。创建方法如下：

* 进入 Engagement > Landing page
* 点击 "Add filter +"，设置筛选器只显示来自搜索的流量（见下方截图）
      Dimension："Session medium"
      Match Type："exactly matches"
      Value："organic"
要只查看具有商业意图（不是从博客文章进入）并且转化为真实线索（而非其他类型转化）的访客，我们再加几步：

* 在筛选器中再添加一个条件，只显示不是从博客文章进入的访客
      Dimension：Landing page
      Match Type："does not contain"（不包含）
      Value："blog"
* 在 "Session conversion rate" 下方的下拉菜单中选择获客转化类型

报告应该是这样的：
![image](../../attachments/pasted-muky91om-1.png)
注：跳过联系页面。那个页面本来就不是为搜索优化的。人们很可能是在搜索品牌名后点击了附加链接（sitelink）才落地到这里。它本身就是表单页，转化率高是理所当然的！

现在你可以清楚地看到哪些页面对最终业绩有影响。这些就是你应该优先处理的页面。先优化它们，用上所有的 SEO 手段，给予它们尽可能多的关注。正是它们在填充销售管道。

顺便花点时间在其他渠道推广这些高绩效页面：
* 如果是文章，就持续在社交媒体上推广
* 如果是服务页面，可以考虑用 Google Ads 买一点流量

当然，对低绩效页面做一些转化优化，任何时候都不算坏事。这里有我们整理的获客最佳实践大清单，里面应该至少有几个新点子。

也有一些数字营销人员认为，就连转化率和线索都不是最好的指标。营销人员应该密切关注成交和新业务。

## 5. 查看低排名页面的互动率
**Engagement > Landing page：筛选 session medium 完全匹配 organic**

这个页面完全应该有排名啊，问题出在哪？

页面细节丰富，关键词相关性也高。与其他在该关键词下有排名的页面相比，它的 Page Authority 也很强。它应该有排名，对吧？两项条件都满足了，SEO 基础工作也肯定做到位了。

但也许访客没有与页面互动。也许访客落地后很快就点了返回按钮。Google 把这种缺乏用户互动的行为视为"pogo sticking"（跳回搜索结果）。用户在发出"这个页面不好"的信号，排名因此受损。
![image](../../attachments/pasted-mukyf78b-1.png)

GA4 就是检查可能存在的"负面用户交互信号"的地方。如果用常规方法无法解释排名为什么低，就去 Google Analytics 查看互动率。如果互动率很低，你可能已经找到了问题所在。

报告和上面用的一样（Landing page，筛选 "session medium" 匹配 "organic"），但这次我们关注的是互动率指标。
![image](../../attachments/pasted-mukyga3i-1.png)
如果互动率很低，用户行为可能正在向 Google 发送信号。Google 发现，当它把访客送到这个页面时，他们很快又跳回了搜索结果。这被称为低"停留时间"（dwell time，即从自然搜索进入后在页面上停留的时间），这对排名不利，但在 GA4 中很容易看出来。
有很多用户体验最佳实践可以提升互动率，进而改善 SEO 表现：

* 增加深度和细节
* 增加格式（小标题、列表、加粗等）
* 增加图片和视频
* 增加内部链接
其中任何一项都能改善你的 SEO，它们都会对搜索排名产生强烈的间接影响。
快速抓住访客！让他们在你的内容中持续浏览下去！

## 6. 追踪来自 Google 商家资料的流量
**Acquisition > Traffic Acquisition：主维度设为 "Session campaign"**

搜索你公司名称的访客已经具备品牌认知。这类搜索者有"导航意图"，也就是说他们想访问你的网站。他们会看到你的网站排在最上方，右侧是你的 Google 商家资料。他们点击的是你的搜索结果，还是你的商家资料？在 GA4 中能区分吗？

可以。你可以追踪点击 Google 商家资料中"Website"（网站）按钮的访客，但前提是要给这个链接加上一点追踪代码。这段代码在技术上是三个参数（utm_source、utm_medium、utm_campaign），用 URL 生成器（URL Builder）添加到链接末尾。

对于来自 Google 商家资料的链接，source 是 "google"，medium 是 "organic"，campaign 可以是类似 "gbp-chicago" 这样的名称（"Google Business Profile Chicago" 的缩写）。用我们的广告系列 URL 生成器设置后，大致如下所示。
![image](../../attachments/pasted-mukyikbq-1.png)
现在，把这个带有三个 UTM 参数的链接设置为 Google 商家资料中指向你网站的链接。当访客点击该链接时，GA4 会把这次点击归因到这个按钮，并使用你指定的 source、medium 和 campaign 名称。

你可以在 Acquisition > Traffic acquisition 报告中看到它们。只需把维度（通过第一列顶部的下拉菜单）改为 "Session campaign"。

在下面的示例中，我用搜索工具只显示名称中包含 "gmb" 的广告系列。可以看到有多个地点的商家资料，每个都有自己的广告系列名称，但只有一个地点获得了点击。可以想象，当本地 SEO 是 SEO 策略的一部分时，这有多重要。
![image](../../attachments/pasted-mukyj40h-1.png)

## 7. 自然搜索流量下跌时获得通知
**Report Snapshot（报告快照）> View all insights（查看所有洞察）> 创建带邮件通知的自定义洞察**

如果自然流量暴跌，你可能好几周都不会察觉。很少有营销人员会那么密切地盯着流量报告。但 GA4 可以替你盯着，在流量崩盘时给你发送邮件通知。这就是带邮件通知的"自定义洞察"（custom insight），在 Universal Analytics 时代它叫"自定义提醒"（custom alert）。

以下是设置搜索流量下跌时发邮件的自定义洞察的方法：

* 进入 Reports（即 "Reports snapshot"）
* 在洞察卡片中点击 "View all insights →"
* 点击右上角的 "Create" 按钮
* 在 "Start from scratch"（从头开始）下点击 "Create new" 按钮
* 将 "Evaluation frequency"（评估频率）设为每周
* 在 Segment（细分）中，不要用 "All users"，点击 "Change"，使用以下设置只监控搜索流量：
      Dimension：First user medium
      Match Type：exactly matches
      Value："organic"
* 将 "Comparison period"（对比周期）设为 Previous week（上一周）
* 洞察名称可以用 "Organic Search Traffic Drop"（自然搜索流量下跌）或类似名称
* 在 "Manage notifications"（管理通知）下输入你的邮箱地址
* 点击右上角的 "Create" 按钮

仔细看这张截图，了解所有设置的样子：
![image](../../attachments/pasted-mukylc7e-1.png)
不要等小问题变成大问题。让 GA4 提醒你出现的问题，然后你就能马上介入查看发生了什么：

* 是一两个爆款 URL 出了问题吗？如果是，查看那些 SERP 和内容。
* 是全站范围的自然流量下跌吗？如果是，检查技术 SEO 因素和 Google 算法更新。
* 是 GA4 本身的报告问题吗？如果是，检查 Google Tag Manager 和 Cookie 同意设置。

## 用数据让人负起责任
如果你聘请了代理商提供 SEO 服务，或者在内部雇用了 SEO 专员，就用你的 Google Analytics 4 数据让他们对结果负责。虽然 GA4 并不以 SEO 指标著称，但它是一个重要的 SEO 报告分析工具，仍然是观察流量和转化率的最佳场所。

它也是做分析、发现洞察的绝佳工具。希望我们已经回答了这个问题：Google Analytics 能用于 SEO 吗？能。

快速回顾：我们介绍了七种分析 SEO 流量的方法。但除了这些报告之外，还要记住 Google Analytics 的一些基本事实：

* 出于隐私和技术原因，GA4 会少报实际流量。根据我们的研究，我们真实的流量和转化可能要高出 20% 甚至更多。
* GA4 没有任何关键词数据。要获取关键词数据，需要把 Google Search Console 关联到 GA4，或者直接去 GSC 查看。
* GA4 数据可以集成到其他工具中。很多数字营销人员会把工具打通，然后在 Google Sheets 中制作 SEO 报告；或者把数据导入 BigQuery，再在 Looker Studio 中搭建 SEO 看板。我们就是这么做的。
