# PMF 验证与第一批用户

[工具产品出海运营](工具产品出海运营.md) 和 [工具产品看数据](工具产品看数据.md) 讲的是产品站稳以后怎么放大。这篇讲更早的一步：产品刚上线，没几个人用，怎么证明**有人真的需要它**，以及第一批海外个人用户（ToC）从哪来。

这一步有个名字，叫 PMF（Product-Market Fit，产品与市场匹配）。放大之前必须先过这一关，否则前两篇里的渠道、看板、周报，都是在给一个没人要的东西做优化。

Marc Andreessen 在 2007 年第一次把 PMF 讲清楚：

> Product/market fit means being in a good market with a product that can satisfy that market.
>
> 没有 PMF 的时候：用户没从产品里拿到价值，口碑传不开，使用量涨得慢，媒体评价平平……
>
> 有 PMF 的时候：用户买得比你做得还快，使用量涨得比你加服务器还快，钱在账户里越堆越多。

这段话说得漂亮，但没法操作。「涨得快」是多快？「用户要」怎么证明？这篇要做的，就是把它拆成一组**能测的证据**，像写论文一样一条一条摆出来。

```text
第 1–2 周            第 3–6 周                第 7–12 周                 之后
───────────        ─────────────────        ─────────────────────      ──────────
找问题               最小产品 + 前 100 人      测 PMF：留存、调查、付费      没过 → 换人群 / 问题 / 产品
访谈、看搜索          手动拉人，一个一个来       攒证据，写 PMF 卷宗          过了 → 读前两篇，开始放大
```

---

## 一、先读懂几种说法

关于 PMF，被引用最多的有这几种说法，它们说的是同一件事的不同侧面。

| 来源 | 说法 | 对我们的用处 |
| --- | --- | --- |
| Marc Andreessen（2007） | 好市场 + 能满足它的产品 | 市场比产品重要；市场好，烂产品也能被拉着走 |
| Andy Rachleff（PMF 这个词的提出者） | 用户不「尖叫」，就没有 PMF | 礼貌的好评不算数 |
| Sean Ellis | 40% 以上的活跃用户说「用不了会非常失望」 | 一个能测的阈值 |
| Brian Balfour | 留存曲线在某个点**走平** | 最硬的滞后指标 |
| Paul Graham | 做一小群人非常想要的东西，而不是一大群人有点想要的东西 | 起步阶段要窄 |
| Dan Olsen | PMF 金字塔：目标用户 → 未满足的需求 → 价值主张 → 功能 → 体验 | 没过的时候，知道从哪一层改 |
| First Round | PMF 分四级：萌芽、发展、强、极强；三个维度：满意、需求、效率 | PMF 不是有和没有两种状态，是一级一级升上去的 |
| Sequoia Arc | 三种 PMF：着火型（Hair on Fire）、忍了型（Hard Fact）、未来型（Future Vision） | 判断你的难点在哪 |

### 1. Sequoia 的三种类型：工具产品多半是「着火型」

| 类型 | 用户状态 | 难点 | 例子 |
| --- | --- | --- | --- |
| 着火型 | 问题很急，正在到处找办法 | 需求明显，所以**竞争者多**，光是更快更便宜不够，要有真正不同的体验 | Zoom、Slack |
| 忍了型 | 问题一直在，但大家认命了 | 用户**习惯**难改，要足够好才值得换 | Notion |
| 未来型 | 用户还没意识到这是个问题 | 用户**不信**，要先教育市场 | 早期的 iPhone |

在线工具、格式转换、AI 小工具，大多是着火型：用户已经在 Google 上搜了。好处是不用教育市场，坏处是搜索结果第一页已经有十几家。**这时候 PMF 的关键不是「有没有需求」，而是「为什么选你」。**后面的证据链会一直追问这一点。

### 2. First Round 的四级：起步阶段的目标只是第一级

First Round 根据投过的几百家公司，把 PMF 分成四级。它的标准是按 B2B 写的，翻译成个人用户（ToC）的工具产品，大概是这样：

| 级别 | First Round 原标准（B2B） | 换成 ToC 工具产品 | 这一级只盯什么 |
| --- | --- | --- | --- |
| 1 萌芽 | 3–5 个客户，大多来自熟人介绍；客户每周都在用 | 几十个陌生人每周都在用；有人主动付钱或者主动问怎么付钱 | 满意度，不看效率 |
| 2 发展 | 5–25 个客户，NRR ≥ 100%，毛利 ≥ 50% | 几百个付费用户；至少一个渠道能持续带来新人；留存曲线走平 | 可重复的需求 |
| 3 强 | 25–100 个客户，有一个验证过的可扩展渠道，10% 的新客来自口碑 | 口碑和自然搜索占新用户的明显比例；回本周期可控 | 效率 |
| 4 极强 | 100+ 客户，两个以上可扩展渠道 | — | — |

First Round 说，从萌芽走到极强，一般要 2 到 6 年。**这篇要解决的是第 1 级，并为第 2 级攒证据。**

First Round 还给了一个没过关时的改法，叫 4P：改**人群**（Persona）、改**问题**（Problem）、改**承诺**（Promise，也就是价值主张）、改**产品**（Product）。第八节会用到它。

---

## 二、PMF 要用一组证据来证明

没有哪一个数能单独证明 PMF。**要像写论文一样，列出一组互相印证的证据。**有的证据产品上线前就能拿到，有的要上线几个月才有；有的是嘴上说的，有的是用行动证明的。

证据分七级，越往下越硬：

| 级别 | 要证明什么 | 证据 | 什么时候能拿到 | 硬度 |
| --- | --- | --- | --- | --- |
| E1 | 问题真实存在 | 访谈里有人讲出**上一次**遇到这个问题的具体经历；搜索量；论坛里的抱怨 | 上线前 | 软 |
| E2 | 问题够痛 | 用户已经在花钱、花时间、凑合用别的办法解决它 | 上线前 | 中 |
| E3 | 愿意付出代价 | 留邮箱、预付、排队、主动转给别人 | 上线前后 | 中 |
| E4 | 用上了 | 激活率：新用户里完成核心动作的比例 | 上线后几天 | 中 |
| E5 | 回来了 | 按注册时间分组看留存，曲线走平；有一批高频用户 | 上线后 4–12 周 | 硬 |
| E6 | 离不开 | Sean Ellis 调查中「非常失望」≥ 40% | 有 40 个以上活跃用户以后 | 硬 |
| E7 | 付钱，还会自己传开 | 付费转化、续费；不花钱的新用户（口碑、品牌词搜索、直接访问）在涨 | 上线后 1–3 个月 | 最硬 |

**判断原则：**

- 只有 E1–E3，叫**有需求的信号**，不叫 PMF
- E5 + E6 同时成立，叫**在某个人群里有了 PMF**
- 再加上 E7，才能放心去放大

最常见的自我欺骗，是拿 E1 当 E6 用：「好多人说这个想法很好」。The Mom Test 整本书都在讲，为什么这句话一文不值。

---

## 三、第 1–2 周：找问题

### 1. 窄比宽重要

Paul Graham 在《How to Get Startup Ideas》里说：

> 你可以做一个很多人有一点点想要的东西，也可以做一个少数人非常想要的东西。选后者。

他还给了一个检验问题：

> 谁会想要它想要到这种程度：就算是两个人做的、很烂的第一版，他们也愿意用？

对工具产品来说，「图片压缩工具」太宽了，「给 Etsy 卖家批量压缩商品图、保留 EXIF 里的版权信息」才够窄。窄的好处有三个：找得到人，说得清为什么选你，也更容易做到第一名。

### 2. 访谈：The Mom Test 三条规则

Rob Fitzpatrick 的 The Mom Test 是做用户访谈的必读书。书名的意思是：问题要问得连你妈都没法对你说谎。三条规则：

1. **聊他的生活，不聊你的想法。**不要问「你觉得这个工具怎么样」，要问「你现在是怎么处理这件事的」
2. **问过去的具体经历，不问对将来的看法。**「你会用吗」「你会付钱吗」得到的回答，基本都是过于乐观的客套话
3. **少说多听。**

判断一次访谈有没有用，不看对方说了多少好话，看他有没有**付出点什么**：时间（约下一次、试用）、名声（介绍给同事）、钱（预付）。只收到了夸奖，这次访谈就是失败的。

### 3. 访谈用的 5 个问题

YC 合伙人、Pebble 创始人 Eric Migicovsky 在 Startup School 里给了 5 个可以每次都问的问题：

```text
1. 做 [这件事] 最难的部分是什么？
2. 跟我讲讲你上一次遇到这个问题的情况。
3. 为什么这件事难？
4. 你试过什么办法来解决它？
5. 那些办法你不满意的地方是什么？
```

再追问三个数：这个问题让他花了多少钱或时间？多久遇到一次？有没有为它付过钱？

第 4 题最关键。**如果他从没试过任何办法，这个问题多半不够痛。**第 5 题的答案，就是你的功能清单和卖点。

海外用户用英文问：

```text
1. What's the hardest part about [doing X]?
2. Tell me about the last time you ran into that.
3. Why was that hard?
4. What, if anything, have you tried to solve it?
5. What don't you love about the solutions you've tried?
```

### 4. 海外 ToC 用户去哪找来访谈

| 地方 | 怎么做 |
| --- | --- |
| Reddit 相关版块 | 找最近在抱怨这个问题的帖子，在帖子里回答问题，然后私信问能不能聊 15 分钟。先看版规 |
| 竞品在 Chrome 商店、App Store、G2、Trustpilot 的差评 | 看差评在骂什么，整理出前三个痛点；有些平台上可以公开回复 |
| X 搜索 | 搜 `"[竞品名]" (sucks OR hate OR alternative)`、`"is there a tool that"` |
| Discord、Facebook 群、Slack 社区 | 先当成员，别一进去就发链接 |
| Indie Hackers、Hacker News 评论 | 适合开发者工具 |
| 已经在用你产品的人 | 最好的访谈对象，注册后的欢迎邮件里直接邀请 |

约不到视频就改用文字：一封邮件问 3 个问题，回复率反而更高。

### 5. 访谈记录

每次访谈后 10 分钟内填完：

```text
日期 / 渠道 / 对方身份：
他现在怎么解决（工具、流程、花多少钱）：
上一次遇到的具体经历（原话）：
痛的程度（1–5），依据是什么行为：
试过的替代方案和不满意的地方：
他付出了什么（时间 / 介绍 / 钱 / 无）：
他用的原话里值得写进落地页的：
```

访谈 15–20 个人以后，把「痛的程度」和「付出了什么」汇总成一张表，看痛点是否集中在同一两类人身上。**PMF 通常先在一个细分人群里成立**，这张表会告诉你是哪一群。

### 6. 桌面研究：用数据交叉验证

访谈的样本少，还要用数据对一遍：

- **搜索量**：核心词加上长尾词，每月合计有多少人搜（Keyword Planner、Ahrefs、Google Trends）
- **竞品的收入**：竞品有没有公开收入，Chrome 插件有多少用户，App 在榜单上排第几
- **抱怨有多集中**：竞品差评里，同一个问题出现的次数

E1、E2 的证据到这里就收集完了。

---

## 四、第 3–6 周：最小产品和前 100 个用户

### 1. 做到能验证就够

最小产品只需要把**一个核心动作**做好。Dan Olsen 的金字塔里，功能和体验是最上面两层，不是地基。要做的是：

- 一个页面、一个功能、一个用户能直接用的入口
- 埋好第一个关键事件：核心动作是否成功完成（见 [工具产品看数据](工具产品看数据.md) 的埋点表）
- 一个收集反馈的入口：页面右下角的反馈框，或者直接留一个邮箱
- 能收钱：哪怕只放一个 Lemon Squeezy / Paddle 的付款链接

Michael Seibel 反复强调：尽快上线，然后迭代。拖得越久，越舍不得放弃最初的假设。

### 2. 做不能规模化的事

Paul Graham 的《Do Things That Don't Scale》是这一阶段最重要的一篇文章：

> 创始人最常要做的一件不能规模化的事，是手动招募用户。几乎所有创业公司都得这么做。你不能等用户来找你，你得去把他们找来。

Stripe 的创始人有个出名的做法，叫「Collison 安装」：别人说「我可以试试」，他们不说「好的我发你链接」，而是说「那把你电脑给我」，当场装好。

他还说：

> 我从没见过哪家创业公司，因为太努力让早期用户满意而走进了死胡同。

对海外的工具产品来说，「手动」意味着：

- 每个注册用户，创始人都亲自发一封邮件（不是模板群发），问他为什么来、想解决什么
- 用户在 Reddit、X 上提到一个问题，你去回复，帮他把问题解决掉，用不用你的产品都行
- 有人卡住了，约一个 15 分钟的屏幕共享，看着他用
- 用户要的小功能，一两天内上线，然后亲自告诉他「你要的做好了」

这些事在用户多了以后都做不了，但前 100 个用户，每一个都值得这样对待。

### 3. 前 1000 个用户从哪来

Lenny Rachitsky 研究了一批知名消费级产品是怎么拿到前 1000 个用户的，归纳出 7 种做法：线下找人、线上社区找人、邀请朋友、制造稀缺（等候名单）、找意见领袖、找媒体、上线前先建社区。他的两个结论对我们很有用：

> 大多数创业公司的早期用户，**只来自其中一种做法**。
>
> 拿到前 1000 个用户的做法，和拿到之后 1 万个用户的做法，**非常不一样**。

所以别七种全上，选一个最可能找到你那群人的，做到底。换成出海的 ToC 工具，常用的是这几种：

| 做法 | 具体怎么做 | 适合 |
| --- | --- | --- |
| 线上社区 | Reddit、Discord、垂直论坛里先回答问题，再介绍工具 | 几乎所有 |
| 搜索 | 做几个长尾词的落地页，直接满足搜索需求 | 需求本身就有明确搜索词的 |
| 平台内分发 | 上架 Chrome 商店、VS Code 市场、Figma 社区、Raycast 商店 | 工具活在某个平台里 |
| Build in public | 在 X 上连续发做产品的过程和数据 | 独立开发者工具 |
| 一次发布 | Show HN、Product Hunt、目录站 | 已经有人用过、改过几轮以后再发 |

YC 的 Kat Manalac 在《How to Launch (Again and Again)》里说：发布不是一次性的，而是一连串小发布——静默上线、给朋友、给陌生人、发到社区、开放申请、发到社交媒体、预售、发布新功能……每一次都是一次测试，看哪种说法、哪个渠道的反应最好。

### 4. 写给社区的帖子

在社区里被接受的帖子长这样：讲清楚你是谁、你遇到了什么问题、你怎么解决的，最后才提工具，并且明确要反馈，不要点赞。

```text
Title: I got tired of [具体问题], so I built a small tool for it — looking for feedback

I'm a [身份]. Every week I had to [具体场景], and [现有方案] kept [具体的毛病].
So I built [产品名]: [一句话它做什么].

It's free to try, no signup: [链接]

It's early and rough. I'd really like to know:
- Does this solve the problem for you, or am I missing something?
- What would make you use it every week?

Happy to answer anything.
```

发了以后，在评论区待满几个小时，每条都认真回。

### 5. 给注册用户的第一封邮件

创始人用自己的邮箱，纯文本，不要 HTML 模板：

```text
Subject: quick question

Hi [名字],

I'm [你的名字], I made [产品名]. Thanks for trying it.

Can I ask what made you look for a tool like this today?
Just hit reply — I read every email.

[你的名字]
```

这封邮件的回复率通常比任何问卷都高，回复的内容就是你最好的访谈素材。

### 6. 这一阶段的增长速度

Paul Graham 在《Startup = Growth》里给了 YC 期间的参考：

> 每周增长 5–7% 是好的增长；能做到每周 10% 就非常出色。只有 1% 的话，说明还没摸清门道。

增长率用什么算？他说：**最好是收入；还没开始收费的，用活跃用户。**不要用注册数，不要用访客数。

---

## 五、第 7–12 周：测 PMF

有了几十上百个真实用户，就可以开始收集硬证据了。

### 1. E4 激活：用上了没

```text
激活率 = 完成核心动作的新用户 ÷ 新用户
```

激活率低，说明**用户来了但没用上**。先去看流失最大的那一步和会话录屏，这时候测 PMF 意义不大，因为大部分人根本没体验到价值。

### 2. E5 留存：曲线走平了没

这是**最重要**的证据。

Brian Balfour：

> 按注册时间把用户分组，画出每组里仍然活跃的比例随时间的变化，这就是留存曲线。如果它在某个点走平了，你多半已经在某个市场或者某个人群里找到了 PMF。

Lenny 的说法：

> 第一个月会有一个很深的下降，这没关系。你想知道的是，它有没有在某个地方走平。

画法见 [工具产品看数据](工具产品看数据.md) 的留存一节。「活跃」按**核心动作**算，周期按工具的使用频率定（天、周或月）。

**走没走平，要看参考线。**Lenny 整理了按品类划分的 6 个月用户留存：

| 品类 | 好 | 很好 |
| --- | --- | --- |
| 消费者社交 | 约 25% | 约 45% |
| 消费者交易类 | 约 30% | 约 50% |
| **消费者订阅软件（Consumer SaaS）** | **约 40%** | **约 70%** |
| 中小企业 SaaS | 约 60% | 约 80% |
| 企业 SaaS | 约 70% | 约 90% |

Andrew Chen 给的消费级产品参考更早期一些：**一年后的留存大于 30%**，折算到第一个月，大约是**次日 60%、第 7 天 30%、第 30 天 15%**。

注意：这些参考线是**付费或者重度产品**的留存。在线工具大量是一次性访客，如果从「所有访客」开始算，一定很难看。从**激活的用户**开始算，再按人群、渠道拆开。

**按人群拆开看，比看总数重要。**Balfour 的原话是：找出留下来的人和没留下来的人有什么不同；说不出区别，就说明你还不知道你的用户是谁。拆的维度：

```text
来源渠道 / 国家 / 设备 / 注册前用了哪个工具 / 访谈里说的身份 / 调查里选的身份
```

总的留存曲线一路往下，但某个渠道或者某类人的曲线走平了，这就是 PMF 的苗头。**把力气全放到这群人身上。**

### 3. E5 补充：有没有一批重度用户

Andrew Chen 的活跃天数分布图（Power User Curve，也叫 L30）：横轴是「过去 30 天里活跃了几天」，纵轴是用户数。如果图的右端翘起来（呈微笑形），说明有一批人几乎天天在用。

他也说，不是每个产品都应该是微笑形。每周用一次的工具看 L7，每月用一次的工具不看这个图。

右端那批人，就是 Sean Ellis 调查里最可能选「非常失望」的人，也是你访谈的优先对象。

### 4. E6 Sean Ellis 调查：离不开吗

Sean Ellis 研究了近 100 家创业公司，发现一个规律：回答「非常失望」的用户超过 40% 的公司，大多数增长强劲；低于 40% 的，大多数在挣扎。

Superhuman 的 Rahul Vohra 把它变成了一套可以反复用的方法，一共四个问题：

```text
1. 如果不能再用 [产品名]，你会有什么感觉？
   A. 非常失望   B. 有点失望   C. 不会失望   D. 不适用，我已经不用了
2. 你觉得什么样的人最能从 [产品名] 中受益？
3. 你从 [产品名] 得到的最主要的好处是什么？
4. 我们怎么改进 [产品名]，会让它对你更有用？
```

```text
1. How would you feel if you could no longer use [Product]?
   Very disappointed / Somewhat disappointed / Not disappointed / N/A — I no longer use it
2. What type of people do you think would most benefit from [Product]?
3. What is the main benefit you receive from [Product]?
4. How can we improve [Product] for you?
```

**发给谁：**只发给真的用过的人。Superhuman 的标准是**过去两周内至少用过两次**。发给所有注册用户，大部分人没怎么用过，分数没有意义。

**怎么算：**

```text
PMF 分数 = 选「非常失望」的人 ÷ (总回答数 − 选「不适用」的人)
```

**多少人回答才算数：**Vohra 说，大约 **40 个回答**，结果的方向就基本可信了。PostHog 的建议是至少 30 个，100 个以上更有把握。

**但要给分数带上误差。**用 [工具产品看数据](工具产品看数据.md) 里的 Wilson 区间算一下：

```text
40 个回答，16 个「非常失望」，得分 40%   →  95% 区间约 26% – 55%
100 个回答，40 个「非常失望」，得分 40%  →  95% 区间约 31% – 50%
```

40 个回答得了 40%，只能说**可能**过线了。要说「确定过线」，需要更多回答，或者分数明显高于 40%。建模的老习惯：数字要带上误差。

**怎么用结果（Superhuman 的四步）：**

1. **分群**：看选「非常失望」的都是什么人（第 2 题的答案 + 你知道的用户属性），用他们的话写出一份「高期望用户画像」。Superhuman 第一次只得了 22%，只看目标人群后涨到 33%
2. **找原因**：第 3 题里，「非常失望」的人说的最主要好处是什么，这就是你真正的卖点，落地页、H1、一句话定位都要用这个词
3. **排路线图**：选「不会失望」的人的意见**直接忽略**。选「有点失望」、并且第 3 题跟核心用户说的是同一个好处的人，是最值得争取的，他们第 4 题的回答就是待办清单。路线图一半用来加强核心用户喜欢的，一半用来解决这群人的阻碍
4. **重复**：每个月或者每个季度重测一次，把分数当成主要目标

Superhuman 用这个方法，在不到一年的时间里把分数从 22% 做到了 58%。Hiten Shah 2015 年调查了 731 个 Slack 用户，51% 选了「非常失望」。

**什么时候发：**用户第二次或第三次完成核心动作后，在产品里弹出；或者用户注册 14 天后发邮件。Tally、Typeform、PostHog 的问卷功能都可以做。

### 5. E7 付费：愿意掏钱吗

Lenny 在总结 PMF 信号时引用了一个观点：

> 如果你能让人在产品做出来之前就付钱，没有比这更好的兴趣和 PMF 信号了。

他甚至建议，就算是一个你以后打算免费的消费级产品，也可以试着收一次钱，看看你到底创造了多少价值。

**尽早收费**，理由有三个：

- 付费是最硬的证据，比任何问卷都硬
- 付费用户的反馈，比免费用户的反馈有用得多
- 定价错了可以改，不收费就永远不知道

**参考数据：**RevenueCat 每年发布订阅应用的报告，是目前 ToC 订阅数据里样本最大的公开来源之一。它统计的是移动 App，网页工具可以参考量级：

| 指标 | 数值 | 来源 |
| --- | --- | --- |
| 下载后 35 天内付费（硬付费墙，先付费才能用） | 中位数 10.7% | 2026 报告 |
| 下载后 35 天内付费（免费增值） | 中位数 2.1% | 2026 报告 |
| 试用转付费，北美 | 中位数 34.2%，前 25% 高于 47.9% | 2026 报告 |
| 试用转付费，按试用时长 | 17–32 天：42.5%；4 天以下：25.5% | 2026 报告 |
| 首日开始试用 | 82% 的试用在安装后第一天开始 | 2025 报告 |
| 新兴市场（拉美、中东非洲）下载转付费 | 中位数低于 0.2% | 2025 报告 |

几点启发：

- 免费增值（freemium）的转化率只有硬付费墙的五分之一左右。免费额度给多了，就永远测不出用户愿不愿意付钱
- 用户是否付费，基本在头几天就决定了，所以付费墙和定价要在第一次使用时就让用户看到
- 不同国家差很多，**PMF 要先在付费能力强的市场验证**（北美、西欧、澳新、日本），别被大量低付费意愿地区的流量稀释了

### 6. E7 口碑：会自己传开吗

PostHog 把口碑增长列为 PMF 的领先指标。看这几个数：

```text
品牌词搜索           GSC 里带产品名的查询，展示和点击在涨吗
直接访问             GA4 里的「直接」渠道，新用户在涨吗
自报归因             注册时问「从哪知道我们的」，选「朋友推荐」的比例
社区里的提及         有没有人在 Reddit、X 上不经你提醒就提到你（用 Syften、F5Bot 之类的工具监控）
```

Balfour 也把直接访问和留存放在一起看，因为直接访问通常就是口碑带来的。

线性增长就算及格，指数增长更好。**你什么都没做的那一周，还有没有新用户进来？**这是最朴素的口碑测试。

---

## 六、定价：先定一个，再调

### 1. 起步阶段的收费方式

Rob Walling 观察了几百个自己出钱创业的人，总结出一个「阶梯法」：

```text
第一步：做一个一次性收费、只靠一个渠道获客的小产品
第二步：重复第一步，直到收入能替代你的工资
第三步：再去做订阅制的产品
```

他的理由：第一次做产品的人，最大的坑是做得太复杂；低价产品的用户终身价值（LTV）撑不起付费获客，只能靠免费渠道（SEO、插件商店）；**只挑一个渠道**，做到在里面排进前两三名。

这对刚起步的工具产品很有参考价值。不一定非要一上来就做订阅：

| 你的工具 | 起步收费方式 |
| --- | --- |
| 每周都要用 | 订阅，月付 + 年付 |
| 偶尔用 | 按次、点数包 |
| 活在某个平台里（插件） | 一次性买断，或者买断 + 一年更新 |
| AI 调用成本高 | 点数包，或者订阅里含固定额度 |

### 2. 定多少钱：Van Westendorp 四个问题

没有销售数据的时候，可以用 Van Westendorp 价格敏感度测试（1976 年提出）。在访谈或者问卷里问四个问题：

```text
1. 多贵的时候，你会觉得太贵，根本不考虑？
2. 多便宜的时候，你会觉得便宜得让人怀疑质量？
3. 多贵的时候，你会觉得开始贵了，但还会考虑？
4. 多便宜的时候，你会觉得很划算？
```

```text
1. At what price would it be so expensive that you wouldn't consider it?
2. At what price would it be so cheap that you'd doubt the quality?
3. At what price would it start to feel expensive, but you'd still consider it?
4. At what price would it feel like a bargain?
```

把每个问题的回答画成累计曲线。「太便宜」和「太贵」两条线的交点叫最优价格点，四条线的几个交点围出一个可接受的价格区间。

它的局限是：它问的是「觉得」，不是「掏钱」。所以只用它**定一个起始价**，最后以真实的付款转化为准。

### 3. 定价的三个起步建议

- **宁可定高了往下调，不要定低了往上调**。给老用户涨价很难，给新用户打折很容易
- 定价页上直接写**年付**，年付用户的留存天然更高
- 在产品里加一个付款前的**「这个价格合理吗」**小问题，能帮你收集到很多定价反馈

---

## 七、PMF 卷宗：把证据写下来

第 12 周左右，写一份 PMF 卷宗。它就是一篇小论文：给自己看，给合伙人看，将来融资也用得上。

### 1. 模板

```text
# [产品名] PMF 卷宗   日期：

## 一句话
[产品名] 帮 [谁] 在 [场景] [完成什么]，比 [现有方案] [好在哪]。

## 核心人群
来自 Sean Ellis 调查第 2 题 + 留存分群：
- 身份：
- 场景：
- 他们用的原话：

## 证据
| 级别 | 证据 | 数据 | 样本量 | 95% 区间 | 结论 |
| E1 问题存在 | 访谈 | 20 人中 14 人讲出了具体经历 | 20 | | ✓ |
| E2 问题够痛 | 访谈 | 14 人中 9 人正在付费使用替代品 | 14 | | ✓ |
| E3 愿意付出 | 等候名单 / 预付 | | | | |
| E4 用上了 | 激活率 | | | | |
| E5 回来了 | 核心人群第 8 周留存 | | | | 走平了吗 |
| E6 离不开 | Sean Ellis 分数（核心人群 / 全部） | | | | |
| E7 付钱 | 访客付费率、试用转付费、首次续费率 | | | | |
| E7 口碑 | 品牌词、直接访问、推荐占比的趋势 | | | | |
| 增长 | 周增长率（收入或活跃用户） | | | | 目标 5–7% |

## 反面证据
哪些数据不支持 PMF，为什么：

## 结论
[ ] 未过：换人群 / 换问题 / 换承诺 / 换产品（选一个，写原因）
[ ] 核心人群内成立：收窄定位，只服务这群人，继续攒 E7
[ ] 成立：开始放大（读 工具产品出海运营、工具产品看数据）

## 下一轮要验证的假设
```

「反面证据」一栏一定要写。写论文的时候，审稿人最先问的就是这个。

### 2. 判定表

| 情况 | 判断 | 下一步 |
| --- | --- | --- |
| E1–E3 都没有 | 问题不存在或者不痛 | 回到第三节，换问题 |
| E1–E3 有，E4 很低 | 需求有，产品没让用户用上 | 改上手流程，看录屏 |
| E4 可以，E5 一路降到 0 | 用了一次就走，没有非用不可的理由 | 看有没有哪群人留下了；没有就改价值主张 |
| E5 某个人群走平了，总的没有 | **核心人群内有 PMF** | 收窄定位，只做这群人 |
| E6 低于 25% | 离 PMF 还远 | 按 Superhuman 的四步做 |
| E6 在 25%–40% | 接近 | 只看核心人群重算，处理「有点失望」那群人的阻碍 |
| E6 ≥ 40%，但没人付钱 | 喜欢，但价值不够付费 | 调付费墙位置、定价、收费方式 |
| E5、E6、E7 都过 | **PMF 成立** | 开始放大 |

---

## 八、没过怎么办：一次只改一层

没过是常态。First Round 说，从萌芽到极强要 2–6 年；Superhuman 从 22% 做到 58% 花了将近一年。关键是**知道该改哪一层，并且一次只改一层**。

Dan Olsen 的金字塔从下往上是：目标用户 → 未满足的需求 → 价值主张 → 功能 → 体验。First Round 的 4P 是：人群、问题、承诺、产品。两者对照：

| 改什么 | 什么时候改 | 例子 |
| --- | --- | --- |
| **人群** | 留存分群里，有一群人明显比别人好 | 「所有需要压缩图片的人」→「Etsy 卖家」 |
| **问题** | 访谈里用户反复提另一个更痛的问题 | 从「压缩图片」换成「批量改商品图尺寸」 |
| **承诺** | 用户说的主要好处和你宣传的不一样 | 你宣传「压缩率高」，用户说「不丢颜色」 |
| **产品** | 人群和问题都对，但用户用了说不好用 | 改上手流程、补上缺的那个功能 |

Olsen 提醒：金字塔最下面一层一动，上面全要重来。所以**改人群是代价最大的**，但也往往是回报最大的。Superhuman 分数的第一次大涨，靠的就是只看目标人群。

改完以后，给新的一组人重测一遍。不同时间注册的用户分开看，别混在一起。

---

## 九、常见的自我欺骗

| 现象 | 为什么不算数 |
| --- | --- |
| 朋友、同行都说好 | 他们在客气，The Mom Test 讲的就是这个 |
| Product Hunt 当天冲到第几名 | 一次性的流量，不代表留存 |
| 注册数涨得很快 | 注册了不用等于没有 |
| 一个大 V 转了一次 | 流量脉冲，一两周就会回落 |
| 付费全靠大折扣 | 测到的是对折扣的反应，不是对产品的需求 |
| 收入全来自一两个大客户 | PostHog 转述过 OpenView 的提醒：100 万美元年收入本身也不能证明 PMF，收入可能来自一两个大客户、高价买来的用户，或者照着某个客户定制的产品 |
| 总的留存还行，但没分群 | 平均数可能掩盖了一个流失很快的大群体和一个留存很好的小群体 |
| 调查发给所有注册用户 | 没怎么用过的人会拉低分数，也会把改进意见带偏 |
| 只看低付费地区的大量流量 | 用户多，但付费能力验证不了 |
| 「再加一个功能就会好」 | 如果最下面的人群和需求不对，加功能没用 |

---

## 十、12 周计划

| 周 | 做什么 | 产出 |
| --- | --- | --- |
| 1 | 选一个窄问题；桌面研究：搜索量、竞品、差评 | 问题描述、竞品表 |
| 2 | 访谈 10–20 人（Mom Test，5 个问题） | 访谈记录表，E1、E2 |
| 3 | 落地页 + 等候名单或预付款链接；在社区里回答问题 | E3 |
| 4 | 最小产品上线，埋好核心事件；静默上线给等候名单 | 第一批用户 |
| 5 | 每个注册用户发一封创始人邮件；看录屏；一天一次小改动 | E4 |
| 6 | 选一个渠道做到底（社区 / 长尾落地页 / 插件商店）；开始收费 | 第一笔收入 |
| 7 | 一次小发布（Show HN 或者一个目录站）；继续手动服务 | 更多用户 |
| 8 | 留存分群第一次看；访谈留下来的人和走掉的人 | E5 初看 |
| 9 | 发 Sean Ellis 调查（过去两周用过两次以上的人） | E6 |
| 10 | 按 Superhuman 四步分析；定核心人群；改落地页的说法 | 高期望用户画像 |
| 11 | 定价测试：Van Westendorp + 真实付费转化 | E7 |
| 12 | 写 PMF 卷宗，做判定 | 继续 / 收窄 / 换一层 |

一周一次复盘，比看 [工具产品看数据](工具产品看数据.md) 里的完整周报简单得多，只填这几个数：

```text
本周活跃用户（完成核心动作）    周增长率
新用户                        激活率
付费用户                      收入
访谈了几个人                   学到了什么（一句话）
本周改了什么                   下周要验证的假设
```

---

## 十一、PMF 之后

PMF 成立以后，问题就从「有没有人要」变成了「怎么让更多人找到、怎么赚得更有效率」。这时候再去读：

- [工具产品出海运营](工具产品出海运营.md)：渠道、发布、定价、留存、分阶段目标
- [工具产品看数据](工具产品看数据.md)：完整的指标树、埋点、看板、周报
- [SEO 落地](../seo/SEO落地.md) 和 [SEO 的监测](../seo/SEO的监测.md)：长期获客的主渠道

Brian Balfour 还提醒过一件事：**光有 PMF 不够**。他举过 Soldsie 的例子：用户推荐意愿高，留存好，也有自然增长，但无论怎么努力，增长都很慢。他认为要长成大公司，需要同时匹配四样东西：市场、产品、渠道、商业模式。Casey Winters 也说，留存曲线走平只证明有人离不开你，还要能**赚钱地**把人拉进来才行。对工具产品来说，这意味着 PMF 之后的第一件事，是找到一个成本够低的获客渠道。

---

## 参考文献

按这篇里出现的顺序排列。标了「必读」的，建议通读原文。

### PMF 的定义和判断

- 必读　Marc Andreessen, [The only thing that matters](https://pmarchive.com/guide_to_startups_part4.html)（2007）：PMF 这个概念最常被引用的出处，讲了市场比团队和产品都重要
- 必读　Rahul Vohra, [How Superhuman Built an Engine to Find Product/Market Fit](https://review.firstround.com/how-superhuman-built-an-engine-to-find-product-market-fit/)，First Round Review：Sean Ellis 调查的完整用法、四个问题、分群、路线图怎么排
- First Round, [Levels of PMF](https://www.firstround.com/levels) 和 [PMF Method](https://www.firstround.com/pmf)：四个级别、三个维度、4P
- Lenny's Podcast, [A framework for finding product-market fit（Todd Jackson）](https://www.lennysnewsletter.com/p/a-framework-for-finding-product-market)
- Sequoia, [The Arc Product-Market Fit Framework](https://sequoiacap.com/article/pmf-framework)：着火型、忍了型、未来型
- Lenny Rachitsky, [How to know if you've got product-market fit](https://www.lennysnewsletter.com/p/how-to-know-if-youve-got-productmarket)：汇总了 Andreessen、Rachleff、Seibel 等人的判断信号
- PostHog, [How to measure product-market fit](https://posthog.com/blog/measure-product-market-fit)：领先指标和滞后指标
- Dan Olsen, [The Lean Product Playbook](https://www.amazon.com/Lean-Product-Playbook-Innovate-Products/dp/1118960874)：PMF 金字塔
- [Product-market fit（Wikipedia）](https://en.wikipedia.org/wiki/Product-market_fit)

### 留存和使用深度

- 必读　Brian Balfour, [The Never Ending Road To Product Market Fit](https://brianbalfour.com/essays/product-market-fit)：留存曲线走平
- Brian Balfour, [Why Product Market Fit Isn't Enough](https://brianbalfour.com/essays/product-market-fit-isnt-enough)：四个匹配
- Lenny Rachitsky, [What is good retention](https://www.lennysnewsletter.com/p/what-is-good-retention-issue-29)：按品类划分的留存参考线
- Andrew Chen, [The Power User Curve](https://andrewchen.com/power-user-curve/)：L30、微笑曲线
- Andrew Chen, [10 magic metrics for product/market fit（X）](https://x.com/andrewchen/status/1184170125525577728)
- Casey Winters, [Product-Market Fit Requires Arbitrage](https://www.caseyaccidental.com/p/product-market-fit-arbitrage)

### 找问题和用户访谈

- 必读　Rob Fitzpatrick, [The Mom Test](https://www.momtestbook.com/)：怎么问才不被客套话骗
- 必读　Eric Migicovsky, [How to Talk to Users（YC Startup School 讲稿）](https://www.ycombinator.com/blog/startup-school-week-1-recap-kevin-hale-and-eric-migicovsky/)：5 个问题
- Gustaf Alströmer, [How to talk to users（YC Library）](https://www.ycombinator.com/library/Iq-how-to-talk-to-users)
- Paul Graham, [How to Get Startup Ideas](https://www.paulgraham.com/startupideas.html)：窄而深、做「烂第一版也有人用」的东西

### 第一批用户和发布

- 必读　Paul Graham, [Do Things that Don't Scale](https://www.paulgraham.com/ds.html)：手动招募用户
- Paul Graham, [Startup = Growth](https://www.paulgraham.com/growth.html)：每周 5–7% 的增长参考
- Lenny Rachitsky, [How the biggest consumer apps got their first 1,000 users](https://www.lennysnewsletter.com/p/how-the-biggest-consumer-apps-got)：7 种做法，大多数公司只靠了其中一种
- Kat Manalac, [How to Launch (Again and Again)（YC）](https://www.ycombinator.com/library/6i-how-to-launch-again-and-again)：发布是一连串小发布
- [YC: Michael Seibel on starting a startup and finding product-market fit](https://www.ycombinator.com/blog/michael-seibel-on-starting-a-startup-finding-product-market-fit-and-fundraising)

### 定价和付费

- Rob Walling, [The Stair Step Method of Bootstrapping](https://robwalling.com/essays/2015/03/26/the-stair-step-method-of-bootstrapping)：一次性收费、单一渠道起步
- RevenueCat, [State of Subscription Apps 2026](https://www.revenuecat.com/state-of-subscription-apps) 和 [2025](https://www.revenuecat.com/state-of-subscription-apps-2025)：ToC 订阅的转化、试用、地区差异
- [Van Westendorp's Price Sensitivity Meter（Wikipedia）](https://en.wikipedia.org/wiki/Van_Westendorp%27s_Price_Sensitivity_Meter)

### 统计

- [Wilson score interval（Wikipedia）](https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval#Wilson_score_interval)：小样本比例的置信区间
