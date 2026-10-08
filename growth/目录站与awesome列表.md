# 目录站、导航站与 awesome 列表

[流量公式与阶段权重](流量公式与阶段权重.md) 里，目录站是第 5 个自变量：冷启动 ★★★，1→10 ★★，10→100 ★。它是新站上线第一周就能做、成本最低的渠道，所以几乎每篇出海教程都会让你「提交到 100 个目录」。

这篇把它单独拆开：目录站到底给你什么、哪些说法是错的、该提交哪些、怎么量效果。按研究卡片的格式写，最后给出能放进模型里的参数。

---

## 一、先说结论

- **目录站主要的价值不是 SEO 外链。** 网上推荐的「免费 dofollow 目录」，大部分实测是 `nofollow`。有一项研究测了 101 个目录和发布平台，只找到 10 个 dofollow，其中 9 个要付费
- **真正有用的是四件事**：少数几个目录带来的有效引荐；让 Google 和 AI 更快发现一个新域名；在「X 的替代品」这类比较页面上出现；所有平台用同一句定位，让 AI 把你和一个品类对上
- **大部分目录不带人。** 有人测了 11 个博客目录 90 天，8 个没有任何可测流量，2 个只有几次点击，1 个第一个月 47 次、之后每月 20–30 次
- **用钱或徽章换 dofollow，在 Google 的政策里属于链接垃圾。** 少量做问题不大，成批做就是政策里写的那种模式，而且这些链接多半会被直接忽略
- **控制在 10 小时以内。** 挑 15–25 个对口的认真填，比海投 200 个划算

---

## 二、目录站给你的五样东西

| 价值 | 怎么起作用 | 能有多大 | 依据 |
| --- | --- | --- | --- |
| 引荐流量 | 有人在目录里看到你，点进来 | 大多数目录接近 0；发布板一次几百到几千；比较类目录长期细水长流 | 第三节的实测 |
| 被发现 | Googlebot 顺着已知页面的链接找到新站。`nofollow` 也是提示，不是禁令 | 对一个没有任何外链的新域名，有用 | Google：发现新 URL 一般约 20 小时，没有链接指过来可能一直不发现 |
| 外链权重 | dofollow 链接传递排名信号 | 很小。多数是 nofollow；低质量目录的链接 Google 会忽略 | Google 垃圾内容政策；Mueller 关于忽略垃圾外链的说法 |
| AI 推荐 | AI 回答「最好的 X 工具」时，参考列表文章、比较站、评测站 | 间接。列表文章是主要来源，G2 / Capterra 更像「入选资格」 | 第六节 |
| 一致的描述 | 各处都是同一句定位、同一个品类 | 帮 AI 和用户把你归到对的品类里 | [工具产品出海运营](工具产品出海运营.md) 第四节 |

在增长公式里，目录站是**累积型带一个小脉冲**：提交当天或审核通过当天有一点流量，之后大部分目录归零，少数比较类目录和外链会长期留下。

---

## 三、实测数据

### 1. 海投 200 个目录，三个月的结果

一位独立开发者在 DEV Community 写了自己的复盘。他有 5 个网站，DR（Ahrefs 的域名评分）一直卡在 20，于是把所有能找到的免费目录都提交了一遍：

| 项 | 数字 |
| --- | --- |
| 提交的目录 | 200+ |
| 真正上线的 | 约 110 |
| 死站、停放域名、表单提交没反应 | 60+ |
| 号称免费、实际只收费的 | 30+ |
| 确认是 dofollow 的 | 约 70 |
| DR | 20 → 29 |
| 引用域名 | 15 → 72 |
| 第一个月花的时间 | 约 15 小时，提交约 80 个，上线约 30 个 |

两个值得记下的观察：大多数目录页 2–4 周才被 Google 抓到；一条权重 63 的博客上的 dofollow 评论链接，比 20 个低权重目录加起来还有用。

注意他报告的是 DR 和引用域名，没报告流量和排名。DR 是 Ahrefs 的指标，不是 Google 的，DR 上去了不代表排名和流量上去了。卖目录提交服务的公司也会给出「DR 从 10 涨到 35」这类案例，那是在卖东西，不能当证据。

### 2. 101 个目录的链接实测

另一位作者检查了 101 个目录和发布平台的四层：链接的 `rel`、页面的 `meta robots`、HTTP 头、跳转链接有没有被 `robots.txt` 挡住：

| 结果 | 数量 |
| --- | --- |
| dofollow | 10，其中 9 个要付 29–49 美元 |
| 真正免费的 dofollow | 1 |
| 用 `nofollow` | 13 |
| 页面是 `noindex` | 3 |
| 根本不给链接 | 10 |
| 无法验证（防爬、纯 JS 渲染等） | 65 |

HackerNoon 上有一篇单独检查了 20 个常被推荐的平台：

| 平台 | `rel` |
| --- | --- |
| SaaSHub | `nofollow` |
| AlternativeTo | `nofollow noopener` |
| Uneed | `nofollow noopener` |
| Indie Hackers | `nofollow noopener` |
| Product Hunt | `ugc` |
| Hashnode | `nofollow ugc` |
| dev.to 文章正文里的链接 | 无 nofollow |
| dev.to 个人资料里的网址 | `ugc` |

我们自己在 2026 年 10 月用 curl 复核了两个：SaaSHub 产品页上「访问官网」的按钮是 `rel="nofollow"`；GitHub 上 awesome 列表 README 里的外链也是 `rel="nofollow"`。网上很多「SaaSHub 给 dofollow」「awesome 列表给 dofollow」的说法，至少现在是错的。

### 3. 引荐流量

| 来源 | 数字 | 可信度 |
| --- | --- | --- |
| 11 个博客目录，90 天 | 8 个零流量，2 个几次点击，1 个首月 47 次、之后每月 20–30 次 | C，个人测试 |
| 50+ 个 AI 工具目录 | 1 个带来约 500 次访问，大多数不到 10 次 | C，作者在卖提交服务 |
| Product Hunt 第 4 名 | 发布当天 1,240 访客（此前 5 天日均 271） | C，个案 |
| BetaList、Uneed、DevHunt 这类小发布板 | 通常几百到两千左右 | C，汇总文章的估计 |
| Uneed 第 2 名 | 作者说比 Product Hunt 带来的点击多 | C，个案 |

结论很一致：**目录的流量分布是幂律的，少数几个贡献几乎全部**。哪几个有用取决于你的品类，只能自己测。

---

## 四、外链：Google 怎么看

### 1. 政策原文

Google 的垃圾内容政策里，链接垃圾的例子包括：

- 用钱、商品或服务换链接
- 过度的互换链接（「你链我，我链你」），或者只为互链而存在的合作伙伴页
- 用自动化程序或服务给自己建链接
- **低质量的目录或书签站链接**

同时写明：付费链接只要加上 `rel="nofollow"` 或 `rel="sponsored"`，就不违反政策。

Mueller 多次说过，Google 会直接忽略垃圾外链，不需要你去处理；对卖链接的站，Google 可能直接忽略整站的所有链接。所以低质量目录的典型结果不是被惩罚，而是**白做**。

### 2. 徽章换链接

很多独立开发者的发布平台用这种模式：免费上架，条件是在你的首页或页脚放它们的徽章（带链接）；不想放就付几美元到几十美元。有的还要求你去评论别人的产品。

| 做法 | 风险 |
| --- | --- |
| 在一个「Launch / Press」页面放 1–3 个对口平台的徽章 | 低 |
| 全站页脚放十几个目录的徽章 | 接近政策里「过度互换链接」的样子，而且这些互链多半被忽略 |
| 付钱买 dofollow | 严格说就是买链接 |

我们的做法：只在真正发布过的平台放徽章，放在单独的页面，不进全站页脚；不为 dofollow 付钱。愿意付钱的场景只有一种：那个目录确实有你的目标用户在逛，买的是曝光，不是链接。

### 3. 自己核对 `rel`

上架以后，把你的列表页拉下来，看指向你网站的那条链接：

```bash
curl -sL -A "Mozilla/5.0" "https://目录站/你的列表页" \
  | grep -o '<a [^>]*你的域名[^>]*>'
```

- 没有 `rel`，或者只有 `noopener` / `noreferrer`：dofollow
- 有 `nofollow`、`ugc`、`sponsored`：不传递排名信号（Google 当作提示，不是禁令）
- 再看页面有没有 `<meta name="robots" content="noindex">`，有的话这一页本身就不进索引

一些站用 JS 渲染或者有防爬，curl 拿不到，就在浏览器里右键查看源代码。

---

## 五、目录的分类和取舍

### 1. 五类目录

| 类别 | 例子 | 形状 | 主要价值 | 成本 |
| --- | --- | --- | --- | --- |
| 发布板 | Product Hunt、Uneed、MicroLaunch、DevHunt、Peerlist、Fazier、BetaList | 脉冲 | 发布当天的流量、第一批反馈、信任背书 | 准备 1–2 周，发布当天要在线回复 |
| 比较 / 替代类 | AlternativeTo、SaaSHub、G2、Capterra | 累积 | 搜「X alternative」的人，意图很强；AI 推荐的入选资格 | 填资料 30 分钟；G2 等要攒评价 |
| AI 工具目录 | There's An AI For That、Futurepedia、Toolify | 累积 + 小脉冲 | 流量大，AI 工具品类的曝光 | 多数要付费：TAAFT 约 347 美元，Futurepedia 247–497 美元，Toolify 约 99 美元（以提交页为准） |
| awesome 列表 | GitHub 上各主题的 awesome-* | 累积 | 开发者受众、长期的引荐 | 一个 PR；要符合列表规则 |
| 公司资料类 | Crunchbase、LinkedIn 公司页、Wellfound | 累积 | 实体信息一致，帮搜索引擎和 AI 认出你是谁 | 低 |

还有一类是海量的通用「免费目录」和书签站，很多是新近用模板批量搭的。它们是政策里说的「低质量目录」，不做。

### 2. 怎么挑：五个问题

每个候选目录问一遍，答「否」超过两个就跳过：

1. 你的目标用户会在这里找工具吗？（看它的分类里有没有你的竞品，竞品页面有没有评论和投票）
2. 它自己有真实的搜索流量吗？（用 Ahrefs / Semrush 的免费额度或 Similarweb 粗看量级，几个来源差 10 倍很正常，只看量级）
3. 列表页会被 Google 收录吗？（搜一下 `site:目录域名 竞品名`）
4. 免费吗？如果收费，买的是曝光还是链接？
5. 需要你放徽章或回链吗？

### 3. 按产品形态的优先级

| 产品 | 先做 | 后做或不做 |
| --- | --- | --- |
| AI 工具 | 2–3 个 AI 工具目录（先用免费队列）、AlternativeTo、SaaSHub | 一次性付费买十几个 AI 目录 |
| 开发者工具 | awesome 列表、DevHunt、Show HN、AlternativeTo | 通用 SaaS 目录 |
| B2B SaaS | G2、Capterra、SaaSHub、AlternativeTo、Crunchbase | 面向大众的发布板 |
| 内容 / 数据门户（比如 craftsail.com） | 对口的 awesome 列表（资源类、数据集类）、垂直资源站、Crunchbase | AI 工具目录、发布板（除非有可用的工具） |
| 浏览器插件 / 平台插件 | 平台自己的商店（是另一个自变量）、AlternativeTo | 通用目录 |

---

## 六、AI 推荐和目录的关系

用户问 ChatGPT「最好的 X 工具」，AI 引用的来源里，目录站本身占比不高，但影响入选：

- Wix Studio AI Search Lab 研究了 7.5 万个 AI 回答、100 多万条引用：列表类文章占 21.9%，在商业类问题里占 40.9%，SaaS 类尤其偏向列表文章
- DerivateX 在 40 个 B2B SaaS 品类上让 ChatGPT 各推荐 10 次，233 次推荐里，G2 和 Capterra 的直接引用都是 0；评测聚合站只占全部引用的 0.9%
- 另一项 2026 年的研究发现，ChatGPT 推荐的工具几乎都在 Capterra 和 G2 上有评价；Blastra 看了 137 家 B2B SaaS，G2 评价数和被 ChatGPT 引用的相关系数约 0.48
- 一种解释是：G2 这类站更像「入选资格」，不是被引用的链接。也就是说，不在上面可能就进不了候选名单
- 2026 年 2 月，G2 从 Gartner 收购了 Capterra、Software Advice 和 GetApp

对早期网站的含义：

1. 目录本身被 AI 直接引用的不多，**真正被引用的是「最好的 X 工具」「X 的 10 个替代品」这类文章**。而这类文章的作者，常常就是从 AlternativeTo、SaaSHub、AI 工具目录、awesome 列表里找候选的。目录是列表文章的上游
2. 各个目录上的描述要一致。AI 是靠很多地方对你的描述，把你和一个品类对上的
3. 别忘了 Bing。ChatGPT 搜索的结果和 Bing 索引高度相关，目录页在 Bing 里也要能搜到

---

## 七、awesome 列表

### 1. 它是什么

GitHub 上以 `awesome-` 开头的精选列表，比如 `awesome-python`、`awesome-selfhosted`。总入口是 `sindresorhus/awesome`，但**那里只收列表，不收单个项目**。要上榜，是给对应主题的列表提 PR，规则由各个列表自己定。

### 2. 价值和限制

- 链接是 `nofollow`（GitHub 对 README 里的外链统一处理），不传递排名信号
- 开发者会翻这些列表找工具，很多「最好的 X 工具」文章也从这里取材，热门列表的引荐是长期的
- 想看效果：如果你的项目是 GitHub 仓库，看仓库的 Insights → Traffic → Referring sites；如果是网站，看 GA4 的引荐里 `github.com`
- 有人估计，一个热门列表能给小项目每周带来 5–20 个访客，没有严谨数据，当作量级参考

### 3. 怎么上

1. 找列表：GitHub 搜 `awesome <你的主题>`，或者看 topic `awesome-list`；优先最近半年还有合并记录的
2. 读它的 `contributing.md`：有的要求最低 star 数（比如 awesome-rust 要 50+，awesome-cli-apps 要 20+），有的不收商业产品，有的只收开源
3. 按它的格式加一行，一般是 `- [名字](链接) - 一句话描述。`，描述开头大写、句号结尾，放到对的分类最后
4. PR 标题和描述写清楚：这是什么、为什么适合这个列表、你和它的关系（是作者就直说）
5. 被拒或者没人理很正常，不要催，也不要在同一个列表反复提

只有网站、没有开源仓库的，可以看那些收「资源」「网站」「数据集」「工具」的列表。不要硬塞进只收开源库的列表。

### 4. 自己建一个列表

如果你的领域里没有好的 awesome 列表，可以自己做一个，这本身就是一个会被引用的内容资产。要进 `sindresorhus/awesome` 总入口，条件包括：

- 列表存在至少 30 天
- 跑过 `awesome-lint`
- 默认分支叫 `main`
- 用 CC0 这类许可，不用 MIT 这类代码许可
- 第一节叫 `Contents`，有 `contributing.md`
- 只收真正好的东西，不收已归档、不维护的项目
- 提 PR 时要先审过别人的 2 个 PR

要点是「精选，不是收集」。把自己的产品放进自己的列表可以，但列表要真的有用，不能是一个变相的广告页。

---

## 八、怎么做

### 1. 先准备

素材包见 [工具产品出海运营](工具产品出海运营.md) 第四节第 5 部分：产品名、一句话定位（60 字符内）、短描述（160 字符内）、长描述、分类和标签、512×512 logo、3–5 张截图、演示视频或 GIF、定价说明、创始人链接。

另外两件事：

- 提前注册账号：AlternativeTo 新账号要满 7 天才能提交新软件
- 准备一个有内容的落地页：目录审核时会打开你的网站，空壳会被拒

### 2. 分三批

| 批次 | 时间 | 做什么 | 数量 |
| --- | --- | --- | --- |
| 第一批 | 上线第 1 周 | 比较类（AlternativeTo、SaaSHub）、公司资料类、2–3 个最对口的垂直目录或 awesome 列表 | 5–10 个 |
| 第二批 | 第 2–4 周 | 对口的发布板，挑一个认真发（不要同一周发好几个，分不清效果）；AlternativeTo 上去竞品页面提交「替代品」 | 3–5 个 |
| 第三批 | 第 2 个月 | 看前两批的数据，同类型里有效的再加几个；AI 工具目录先走免费队列 | 5–10 个 |

总数在 15–25 个，总时间控制在 10 小时左右。

### 3. 每个目录记一行

| 字段 | 例子 |
| --- | --- |
| 目录 | AlternativeTo |
| 类别 | 比较类 |
| 提交日期 / 上线日期 | 10-08 / 10-14 |
| 列表页 URL | |
| 链接 `rel` | nofollow |
| 列表页是否被 Google 收录 | 是 |
| 花费（时间 / 钱） | 40 分钟 / 0 |
| 是否要徽章 | 否 |
| 30 天引荐会话 / 注册 | |
| 90 天引荐会话 / 注册 | |

### 4. 归因

- 能自己填链接的目录，链接后面加 `?ref=目录名` 或 UTM（规范见 [工具产品看数据](工具产品看数据.md)）
- 不能改链接的，就看 GA4 引荐来源和 Cloudflare 的 Referer
- 注册时问「你从哪知道我们的」：有人是在目录里看到名字、过几天去 Google 搜的，这在 GA4 里会记成自然搜索

---

## 九、怎么量

| 看什么 | 在哪看 | 什么时候看 |
| --- | --- | --- |
| 每个目录的引荐会话、注册、激活 | GA4 → 流量获取 → 会话来源；Cloudflare 引荐 | 上线后 30 天、90 天 |
| 目录页有没有被收录 | `site:` 只能粗看；用 Search Console 的「链接」报告看外部链接来源 | 30 天后 |
| 新站被发现的速度 | Search Console 抓取统计、网址检查里的「引荐网页」 | 上线前两周 |
| 有没有出现在 AI 回答里 | 定期拿用户会问的问题问各家 AI，记下有没有提到你 | 每月 |
| 外链（可选） | Ahrefs / Semrush 的引用域名 | 每月 |

90 天后做一次清点：给你带来过注册的目录有几个？一般只有 2–3 个。这几个以后有产品更新就去更新资料；其余的不用再管。

---

## 十、模型参数

按 [流量公式与阶段权重](流量公式与阶段权重.md) 的研究卡片，把目录站填进去。区间来自上面的 C 级数据和我们自己的判断，要用自己的周表校准。

```text
## 自变量：目录站 / 导航站 / awesome 列表

形状：累积型，提交或上线当天有一个小脉冲
适合：所有新站；AI 工具、开发者工具、SaaS 尤其适合
不适合：拿低质量目录刷外链

### 参数
见效滞后 L：审核 1 天到 2 周；被 Google 抓到 2–4 周
脉冲残留 λ：发布板约 0.1–0.3（按天）；比较类、awesome 列表接近 1（长期细水长流）
半饱和点 K：15–25 个对口目录以后，每多一个的边际价值接近 0
单位投入产出：多数目录接近 0；有效的 2–3 个贡献几乎全部引荐（幂律）
衰减：发布板几天内归零；比较类随竞品页面的流量长期存在；目录本身也会过时或关停

### 各阶段权重
S0：★★★  新域名没有任何外链和提及，最便宜的「被看见」
S1：★★   只维护有效的那几个，更新资料、攒评价
S2：★    边际很小；G2 / Capterra 的评价对 B2B 的 AI 推荐仍有意义

### 证据等级
A：Google 垃圾内容政策（低质量目录、换链接、付费链接）
B：Wix Studio AI 引用研究、DerivateX / Blastra 的 AI 推荐研究
C：DEV Community 的 200 目录复盘、101 目录链接实测、HackerNoon 的 rel 检查、各种流量案例
```

---

## 十一、常见误判

| 以为 | 实际 |
| --- | --- |
| 提交 100 个目录，SEO 就起来了 | 多数是 nofollow 或低质量链接，Google 忽略；DR 涨了不等于排名涨了 |
| 「免费 dofollow 目录清单」可以照着做 | 清单常常是目录站自己写的，很多实测是 nofollow，或者要徽章 / 付费 |
| SaaSHub、awesome 列表给 dofollow | 2026 年 10 月实测都是 nofollow，价值在引荐和曝光 |
| 目录没带来流量就是没用 | 新域名被发现、AI 入选资格、品牌搜索这些不会出现在引荐报告里 |
| 一个发布板效果好，就同一周发五个 | 分不清哪个有效，而且每次发布都要在线回复，质量会掉 |
| 付钱上 AI 目录一定回本 | 按 C 级数据，大多数目录带不来人，先走免费队列看数据 |
| 买个「提交到 500 个目录」的服务省事 | 自动化建链接是政策里明确的链接垃圾 |

---

## 参考资料

### Google

- [Spam policies for Google web search](https://developers.google.com/search/docs/essentials/spam-policies)（链接垃圾：低质量目录、换链接、付费链接）
- [Qualify outbound links](https://developers.google.com/search/docs/crawling-indexing/qualify-outbound-links)（`nofollow`、`sponsored`、`ugc`）
- [Search Engine Journal: Google on spammy backlinks](https://www.searchenginejournal.com/google-on-spammy-backlinks-negative-impact-on-rankings/511433/)
- [Search Engine Journal: John Mueller on links Google ignores](https://www.searchenginejournal.com/links-that-google-ignores/299617/)

### 实测

- [DEV Community: My 3-month startup directory submission journey](https://dev.to/jim_l_efc70c3a738e9f4baa7/my-3-month-startup-directory-submission-journey-what-actually-moved-the-needle-gef)
- [DEV Community: I measured the link value of 101 directories and launchpads](https://dev.to/safak_tepecik_bc95fdaea2b/i-measured-the-link-value-of-101-directories-and-launchpads-one-free-dofollow-link-came-out-of-it-4pj2)
- [HackerNoon: I checked the rel attribute on 20 platforms](https://hackernoon.com/i-checked-the-rel-attribute-on-20-platforms-most-backlinks-pass-nothing)
- [Free blog directories that still send traffic in 2026](https://www.lilachbullock.com/free-blog-directories-that-still-send-traffic/)
- [A successful Product Hunt launch: the numbers](https://thehackstack.substack.com/p/a-successful-product-hunt-launch)

### 目录

- [AlternativeTo FAQ](https://alternativeto.net/faq)
- [AlternativeTo: Free submission guide 2026](https://launchdirectories.com/directory/alternativeto)
- [SaaSHub: Free submission guide 2026](https://launchdirectories.com/directory/saashub)
- [AI tool directory submission fees 2026](https://www.aicentralresources.com/best-ai-tool-directories-submission-fees-comparison-list)（竞争对手写的，价格以各站提交页为准）
- [Smol Launch: Best directories to submit your AI tool in 2026](https://smollaunch.com/best-of/best-directories-to-submit-ai-tool-2026)

### awesome 列表

- [sindresorhus/awesome](https://github.com/sindresorhus/awesome)
- [sindresorhus/awesome: contributing.md](https://github.com/sindresorhus/awesome/blob/main/contributing.md)
- [DEV Community: How to get your open source project into awesome lists](https://dev.to/battyterm/how-to-get-your-open-source-project-into-awesome-lists-and-why-its-worth-the-effort-5h45)

### AI 推荐

- [Search Engine Land: AI citations favor listicles, articles, product pages](https://searchengineland.com/ai-citations-favor-listicles-articles-product-pages-study-472364)
- [DerivateX: B2B SaaS AI citation study](https://derivatex.agency/report/b2b-saas-ai-citation-study/)
- [StriveLabs: Do G2 and Capterra feed AI answers? The evidence conflicts](https://strivelabs.ai/blog/g2-capterra-ai-answers/)
- [Blastra: Directories + authority drive AI citations](https://blastra.io/blog/the-compounding-rule-saas-ai-citations/)
