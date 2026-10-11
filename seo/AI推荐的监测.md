# AI 推荐的监测

[新站的 SEO 监测](新站的SEO监测.md) 讲的是 Google 认不认这个站。这篇讲另一件事：用户不去搜索框，直接问 ChatGPT、Claude、Gemini、Perplexity「有什么好用的 xxx」，回答里有没有你。

这件事比 SEO 难看清。没有 Search Console 这样的官方报表，同一个问题问两次，答案往往不一样。网上很多「AI 排名」工具给你一个名次，今天第 2、明天第 5，看起来很精确，其实大部分是噪声。

这篇只讲四件事：这个渠道分几步，每一步怎么量，量出来的数什么时候能信，什么时候算过了这一关。

每条结论后面标了证据等级：

| 等级 | 意思 |
| --- | --- |
| 标准 | RFC、统计学教科书方法 |
| 官方文档 | OpenAI、Anthropic、Perplexity、Google、Cloudflare 自己写的文档 |
| 实验 | 有对照、方法公开的研究 |
| 观察 | 大样本统计，没有对照，只能说相关 |
| 经验 | 我们自己做下来的判断，没有外部数据 |
| 先验 | 我们定的起点数字，还没用真实项目校准 |

---

## 一、这个渠道在问什么

用户问 AI「有什么好用的 PDF 压缩工具」，AI 回答里提到谁，用户多半就从里面挑。对做产品的人来说，问题拆开是三个，按顺序：

```text
抓得到        →        认得你        →        推荐你（以及依据是什么）
AI 爬虫能拿到正文     问到你的名字，它说得对     问品类，它把你列进去，引用了哪些网页
（四）                （五）                    （六）
```

前一步不过，后一步白做。爬虫拿不到你的页面，AI 就只能靠别人怎么写你；别人写错了，它就跟着错。

和 SEO 的关系：AI 回答的依据，大多数还是网页。带搜索的回答（ChatGPT 搜索、Perplexity、Google AI Overviews）是实时去抓网页再写答案；不带搜索的回答，靠的是训练时读过的网页。两种情况，依据都来自你的站和**别人的站上怎么提到你**。

Google 的说法很直接：出现在 AI Overviews 和 AI Mode 里没有额外要求，页面要被编入索引、并且可以带摘要出现在搜索结果里（官方文档）。所以 SEO 落地那一套（[SEO 落地](SEO落地.md)）是 AI 推荐的地基，不是另一套东西。

站外提及比外链更重要。Ahrefs 看了 7.5 万个品牌（DR > 40），和「被 AI Overviews 提到」相关性最强的是品牌在网上被提到的次数（Spearman 0.664），反向链接数只有 0.218（观察，相关不等于因果，样本偏大品牌）。

在 [流量公式与阶段权重](../growth/流量公式与阶段权重.md) 第四节的先验权重表里，AI 推荐是：

| 自变量 | S0 冷启动 | S1 1→10 | S2 10→100 | 依据 |
| --- | --- | --- | --- | --- |
| AI 推荐 | ★★ | ★★★ | ★★★★ | 依赖站外提及，和社区、目录、测评一起涨 |

冷启动阶段它不是主力，因为还没有多少地方提到你。但「抓得到」和「认得你」这两步花不了多少时间，越早做完，后面站外提及攒起来的时候越不浪费。

---

## 二、三个阶段

| 阶段 | 问题 | 做什么 | 什么时候算过关 |
| --- | --- | --- | --- |
| AI-1 抓得到 | AI 爬虫能不能拿到你的正文 | robots.txt 放行要放行的 AI 爬虫；CDN 不拦；不执行 JS 也有正文；放一个 `llms.txt` | 体检的「访问」「发现」两层 pass |
| AI-2 认得你 | 问到你的名字，AI 知不知道你、说得对不对 | 写一份一致的品牌事实；看品牌问题上的认知度；把说错的事实追到源头 | 认知度 n ≥ 30 且 Wilson 下界 ≥ 50%（先验） |
| AI-3 推荐你 | 问品类和场景，AI 推不推荐你，依据是什么 | 按买家原话写问题；选真会被比较的竞品；看引用缺口；每周去一个来源上出现 | 不设终点，每周看可见性和声量的趋势 |

AI-1 一般一两天能做完。AI-2 取决于站外已经有多少地方写过你，新产品可能要几周到几个月。AI-3 是长期的事。

---

## 三、怎么量

### 1. 回答每次都不一样

同一个问题，同一个 AI，问十次，十个答案。这不是工具的毛病，是模型本来就这样：采样有随机性，带搜索的回答每次抓到的网页也不同。

SparkToro 和 Gumshoe 在 2025 年 11–12 月让 600 个志愿者，把 12 个问题在 ChatGPT、Claude、Google AI 上一共跑了 2,961 次，每个问题每个平台 60–100 次（观察，未经同行评审）：

- ChatGPT 和 Google AI Overviews 两次给出同一份品牌列表的概率不到 1%
- 名次几乎每次都变；「提到没提到」比名次稳定得多，品类越窄越稳定

所以：

- **单次回答不能当结论。** 同一问题要多次采样，结论是一个比率
- **比率是估计值，要带区间。** 20 次里提到 5 次，真实的比率可能是 11%，也可能是 47%
- **名次只看「首位」「前三」这种粗的比率**，不看平均名次

craftsail-growth 默认每个问题、每个引擎每天跑 3 次（先验：在 token 成本和每周样本量之间取的折中，一周 21 次，接近 n ≥ 30 的门槛要两周）。

### 2. 五个比率

先分清两类问题：

- **品牌问题**：问题里带你的产品名，比如「Acme 是什么」「Acme 和 Foo 哪个好」
- **非品牌问题**：问题里不带你的名字，比如「有什么好用的 PDF 压缩工具」

分母都是「成功的回答」：调用失败、超时、空回答不算。下面的公式和 craftsail-growth 里算的一字不差。

**可见性**：非品牌问题上，回答里提到你的比例。这是 AI-3 最主要的数。

```text
visibility = answers that mention the brand / successful answers
             (unbranded questions only)
```

**认知度**：品牌问题上，回答里提到你的比例。它回答的是 AI 知不知道你，是 AI-2 的数。认知度高、可见性低，说明 AI 认识你，只是不推荐你。

```text
recognition = answers that mention the brand / successful answers
              (branded questions only)
```

**首位 / 前三**：你和竞品按在回答里第一次出现的位置排序，你排第一、排进前三的比例。

```text
top 1 = answers where the brand is named first / successful unbranded answers
top 3 = answers where the brand is among the first three named / successful unbranded answers
        (brand and competitors ordered by first appearance in the answer)
```

**声量（share of voice）**：在你和竞品被提到的总次数里，你占多少。一个回答里，你只算一次，每个被提到的竞品也各算一次。

```text
share of voice = brand mentions / (brand mentions + competitor mentions)
                 (unbranded questions; the brand counts once per answer,
                  each competitor named in an answer counts once)
```

声量的分母取决于你列了哪些竞品。竞品列表一改，声量就不能和以前比（见第六节）。

**引用自有域名**：带搜索的回答会列出引用的网页。两个数：有多少回答引用了你的站，所有引用里有多少指向你的站。

```text
answers citing your domain = unbranded answers with a link to your site
                             / successful unbranded answers
citation share             = citations pointing to your site / all citations
```

AI 提到你，不一定引用你的站；它可能引用的是一篇测评、一个 Reddit 帖子。这两个数分开看：提到你说明你在候选里，引用你的站说明它把你的页面当成了依据。

### 3. 区间：Wilson 95%

比率要带区间。用 Wilson 区间，不用「p ± 1.96 × 标准误」那个正态近似：样本小、比率接近 0 或 1 时，正态近似会算出负数或者宽度为 0 的区间，Wilson 区间不会（标准）。

```text
p = x / n        (x answers that mention you, n successful answers)
z = 1.96         (95%)
center = (p + z²/2n) / (1 + z²/n)
half   = z · √( p(1−p)/n + z²/4n² ) / (1 + z²/n)
interval = center ± half
small sample: n < 30
```

例子：

```text
5 of 20  → 25%, interval 11.2% – 46.9%, small sample
4 of 6   → 67%, interval 30.0% – 90.3%, small sample
```

20 次里提到 5 次，真实的可见性从 11% 到 47% 都说得通。n < 30 一律标成小样本（先验：常用的经验门槛，n = 30 时，p = 50% 的区间仍有约 ±17 个百分点宽）。

### 4. 变没变：Newcombe 差值区间

改了一个页面、上了一个目录，想知道可见性有没有变。不能看两个比率谁大，要看**两者之差**的区间。用 Newcombe 的混合 score 法，它由前后两个 Wilson 区间算出来，在小样本下覆盖率接近名义的 95%（标准）。

```text
difference d = p_after − p_before
95% interval of d: Newcombe hybrid score method (from both Wilson intervals)
up    if the whole interval > 0
down  if the whole interval < 0
within noise otherwise
```

```text
18/30 → 6/30 : interval −58.6 to −15.3 points → down
 6/20 → 5/20 : interval crosses 0            → within noise
```

第一个例子，从 60% 掉到 20%，差值区间整个在 0 下面，算真的掉了。第二个，从 30% 到 25%，区间跨过 0，算噪声。**整个区间在 0 的一侧才算变**，跨过 0 就说「看不出来」，不说涨也不说跌。

### 5. 不能比的东西

| 别这样比 | 为什么 |
| --- | --- |
| 跨引擎比引用数 | 各家每个回答列的引用条数差别很大，有的列七八条，有的一两条，有的不列。比引用占比可以，比条数没意义（经验） |
| API 和网页版混在一起 | 工具一般用 API 采样，和用户在 ChatGPT 网页、App 里看到的不是同一个环境：模型版本、是否开搜索、系统提示都可能不同。两者各自内部比，不要混成一条曲线（经验） |
| 改了问题前后比 | 问题本身就是测量对象。改一个词，量的就是另一件事。改问题要另起一个版本，从新版本开始重新攒样本（经验） |
| 改了竞品列表前后比声量 | 声量的分母变了（经验） |
| 用户真实问法和你的问题比 | SparkToro 那次让 142 个人各自写「给常出差的家人买耳机」的问题，几乎没有两条一样（观察）。你的问题只是代表，不是用户问题的全集 |

### 6. 引用稳定度

带搜索的回答，每天引用哪些域名，是一个分布。把相邻两天的分布比一比，变化小说明这个问题的来源已经被少数几个站占住了；变化大说明来源还开放，新站有机会挤进去。用 Bray–Curtis 距离（标准，原本是生态学里比较两个样地物种组成的方法）：

```text
share_d(x) = citations of domain x on day d / all citations on day d
distance(d1, d2) = 1 − Σ_x min(share_d1(x), share_d2(x))   (Bray–Curtis on shares)
stability = 100 × (1 − average distance between consecutive days)
< 40 wide open · 40–69 contested · ≥ 70 locked in
```

40 和 70 两条线是先验（我们按产品里的分布定的起点，没有外部依据）。

这个数**只用来判断来源还开不开放**，决定这个问题值不值得去争，不用来评价你自己做得好不好。

---

## 四、抓得到

### 1. robots.txt 和各家 AI 爬虫

各家都把爬虫按用途拆成了几个，挡哪一个，后果不一样：

| 爬虫 | 谁家 | 干什么 | 挡掉的后果 | 守 robots.txt |
| --- | --- | --- | --- | --- |
| `GPTBot` | OpenAI | 抓取可能用于训练基础模型的内容 | 内容不用于训练 | 是 |
| `OAI-SearchBot` | OpenAI | 让网站出现在 ChatGPT 搜索结果里 | 不会出现在 ChatGPT 搜索的回答里 | 是 |
| `ChatGPT-User` | OpenAI | 用户在对话里让 ChatGPT 去打开某个网页 | 由用户触发，robots.txt 可能不适用 | 可能不适用 |
| `ClaudeBot` | Anthropic | 收集网页内容，用于模型训练 | 之后的内容不进训练数据 | 是 |
| `Claude-SearchBot` | Anthropic | 提升搜索结果质量 | 可能降低你在搜索结果里的可见性和准确性 | 是 |
| `Claude-User` | Anthropic | 用户提问时代为访问网页 | 可能降低你在用户发起的搜索里的可见性 | 是 |
| `PerplexityBot` | Perplexity | 在 Perplexity 搜索结果里展示并链接网站；不用于训练基础模型 | 不出现在 Perplexity 搜索结果里 | 是 |
| `Perplexity-User` | Perplexity | 用户提问时代为访问网页 | 一般不理 robots.txt | 一般不理 |
| `Google-Extended` | Google | 不是一个爬虫，是 robots.txt 里的控制令牌：管 Google 抓到的内容能不能用于训练 Gemini、以及 Gemini Apps 的 grounding | 不影响 Google 搜索收录，也不是排名信号 | 是 |

（以上都是官方文档。）

几个要点：

- **训练和搜索是分开的。** 不想被拿去训练，挡 `GPTBot`、`ClaudeBot`、`Google-Extended`；想出现在 AI 回答里，放行 `OAI-SearchBot`、`Claude-SearchBot`、`PerplexityBot`。早期产品要的是曝光，我们默认全放行（经验）
- **`Google-Extended` 管不了 AI Overviews。** AI Overviews 和 AI Mode 用的是普通的 Googlebot 和搜索索引；要限制内容出现在里面，用 `nosnippet`、`data-nosnippet`、`max-snippet` 或 `noindex`（官方文档）
- **一个爬虫只看一组规则。** RFC 9309 规定，爬虫找和自己名字匹配的那一组；找到了，`User-agent: *` 那组就不再适用（标准）。所以给 `GPTBot` 单独写了一组，`*` 组里的 `Disallow` 对它就不生效，反过来也一样
- **robots.txt 返回 5xx，等于全站禁止。** 返回 4xx（比如 404），爬虫可以随便抓（标准）
- **改了不是立刻生效。** OpenAI 和 Perplexity 都说调整要大约 24 小时（官方文档）；RFC 9309 允许爬虫缓存 robots.txt 最多 24 小时（标准）
- robots.txt 不是访问控制，只是声明（标准）。真要挡，只能靠服务器

一个放行的写法：

```txt
User-agent: *
Allow: /
Disallow: /account/
Disallow: /api/

Sitemap: https://example.com/sitemap-index.xml
```

所有爬虫都走 `*` 组。只有你要对某一家区别对待时，才单独写一组，而且那一组要把 `Disallow` 写全。

### 2. CDN / WAF 默认拦截

robots.txt 写对了，请求还可能在 CDN 那一层就被挡掉。最常见的是 Cloudflare：

- 2025 年 7 月起，新接入 Cloudflare 的域名会在开通时被问要不要放 AI 爬虫进来，默认改成了拦（官方文档，Cloudflare 博客）
- 2026 年 9 月 15 日起，新域名的默认值改为：在展示广告的页面上，拦「Training」和「Agent」两类 AI 机器人，「Search」类放行；同时用于训练和搜索的混合用途爬虫，算在 Training 里一起拦（官方文档）

各家 CDN、主机商、安全插件的默认值不一样，也会变。所以不要靠记，要实测：用各家爬虫的 User-Agent 请求几个页面，看返回的是不是 200 和正文，而不是 403、挑战页或者验证码。craftsail-growth 的体检就是这么做的，被拦了会报「WAF or CDN rejects AI crawler user agents」。

### 3. 不执行 JavaScript 时，正文在不在

Vercel 和 MERJ 在 2024 年 12 月看了自己网络上的爬虫流量：GPTBot、OAI-SearchBot、ChatGPT-User、ClaudeBot、PerplexityBot 都不执行 JavaScript；它们会下载 JS 文件，但不运行（观察）。会渲染 JS 的是 Gemini（用 Googlebot 的基础设施）和 AppleBot。各家 AI 的官方文档都没有写自己渲不渲染 JS。

所以：**纯客户端渲染的页面，在大多数 AI 爬虫眼里是空页。** 检查方法和 [SEO 落地](SEO落地.md) 第十节一样：关掉 JavaScript，或者直接 `curl` 一下，正文、标题、导航链接应该已经在 HTML 里。

```bash
curl -s -A "GPTBot" https://example.com/pricing | grep -c "<p"
```

体检里对应的问题是「Static HTML has no content」。

### 4. llms.txt

`llms.txt` 是 Jeremy Howard 2024 年 9 月提出的约定，2026 年 8 月出了 v2。它是放在站点根目录的一份 Markdown：H1 写站名（唯一必须的部分），接一段引用块写简介，再按 H2 分组列链接，`Optional` 放次要的（官方文档，指提案本身）。写法见 [SEO 落地](SEO落地.md) 第八节。

谁在读，证据是这样的：

| 说法 | 证据 | 等级 |
| --- | --- | --- |
| 它和 robots.txt 不是一回事：robots.txt 说谁能来抓，llms.txt 是 agent 需要信息时按需读的 | llmstxt.org | 官方文档 |
| Google 搜索不需要它：出现在 AI 功能里，不用做新的机器可读文件或「AI 文本文件」 | Google AI features 文档 | 官方文档 |
| OpenAI、Anthropic、Gemini 自己的开发者文档都放了 llms.txt | llmstxt.org | 官方文档 |
| 13.7 万个域名里 28% 放了，其中 97% 一次都没被请求过；请求里 AI 检索类爬虫只占 1.1%，更多是 SEO 审计工具和编程 agent | Ahrefs，2026 年 5 月 | 观察 |

结论：它几乎不影响 ChatGPT、Perplexity 这类回答引擎推不推荐你，对编程 agent 和文档问答有用。做一个花不了十分钟，但别指望它，也别让它替代 robots.txt、sitemap 和服务端渲染的 HTML。最要紧的是里面的链接都打得开，体检会检查这一条（「llms.txt links to broken or blocked pages」）。

### 过关

craftsail-growth 的体检把问题按修复顺序分成四层：访问、发现、理解、引用。每一层的状态这样算：

```text
layer status = fail  if any critical finding in the layer
               warn  if any warning (and no critical)
               pass  otherwise
layers after the first failing layer are blocked
```

AI-1 过关 = 最近一次体检里，**访问层和发现层都是 pass**，也就是这两层没有任何严重或警告级别的问题。访问层管 robots.txt、CDN 拦截、状态码、`noindex`、空壳页；发现层管 sitemap 和 llms.txt。

---

## 五、认得你

### 1. 品牌问题和非品牌问题分开统计

品牌问题的答案几乎总会提到你，因为问题里就有你的名字。把它和非品牌问题放在一起算可见性，比率会被抬高，而且抬多少取决于你写了几个品牌问题。这就是第三节把可见性和认知度分开的原因。

两类问题回答的是不同的事：

| | 品牌问题 | 非品牌问题 |
| --- | --- | --- |
| 问的是 | AI 知不知道你，说得对不对 | AI 推不推荐你 |
| 指标 | 认知度 | 可见性、首位 / 前三、声量、引用 |
| 阶段 | AI-2 | AI-3 |
| 例子 | 「Acme 是什么？」「Acme 多少钱？」「Acme 和 Foo 比怎么样？」 | 「有什么好用的发票 API？」「SaaS 用什么开票工具？」 |

品牌问题写 3–5 个就够：是什么、多少钱、和主要竞品比、适合谁。

### 2. 写一份一致的品牌事实

AI 是靠很多地方对你的描述，把你和一个品类对上的（[目录站与 awesome 列表](../growth/目录站与awesome列表.md) 第六节）。各处说法不一致，AI 要么对不上，要么挑一个旧的说法讲。

先写一份，放在你自己手里，之后首页、about 页、目录站、GitHub README、社区简介都照它写：

| 字段 | 写什么 | 例子 |
| --- | --- | --- |
| 一句话定义 | 按 [工具产品出海运营](../growth/工具产品出海运营.md) 第三节的格式：帮谁、在什么场景、做成什么、比现有方案好在哪 | Acme 帮 SaaS 团队一次调用开出合规发票，比自己对接税务接口快一周 |
| 别名 | 用户可能怎么叫你：旧名、缩写、大小写写法、带不带空格 | Acme、AcmeHQ、acme.dev |
| 目标用户 | 具体到人 | 年收入 100 万美元以下的 SaaS 团队 |
| 价格 | 和定价页一致，写清楚币种和周期 | 免费 100 张 / 月；Pro 每月 29 美元 |
| 带出处的数字 | 每个数字后面放一个链接 | 「处理 2 万张发票」→ 链到公开的统计页 |

带出处很重要。GEO 那篇论文在 GEO-bench 上做了对照实验：给内容加引用来源、加引语、加统计数字，在「按位置加权的字数」这个指标上相对提升 30%–40%；堆关键词几乎没用（实验）。原来排名第五的网页，加引用来源后可见性提升 115.1%（实验）。这是对页面内容的实验，结论是：可核对的事实比形容词更容易被 AI 采用。

### 3. 读回答：说错的事实追到源头

每周读一遍品牌问题的回答，不看比率，看内容。常见的错：

- 价格是旧的
- 把你和同名的另一个产品混了
- 说你有某个功能，其实没有；或者说你没有，其实有
- 把你归到错的品类

每找到一条，去看这个回答引用了哪些网页（不带搜索的回答没有引用，就用错的那句话去搜）。大多数错误能追到某一页：你自己站上没更新的旧页、一篇过时的测评、一个目录上的旧描述。改自己的页；别人的站，发邮件请他们改。

错的说法会扩散：其他文章、其他 AI 回答会照抄它（经验）。所以追源头比在自己首页上再写一遍有用。

### 过关

```text
认知度 n ≥ 30  且  Wilson 95% 区间下界 ≥ 50%
```

两个数都是先验：n ≥ 30 沿用第三节的小样本门槛；下界 50% 的意思是「有把握说，问到你的名字时，至少一半的回答知道你」。以后用真实项目校准。

按这个标准，n = 30 时至少要 21 次提到你（70%，区间 52.1%–83.3%）；20 次（66.7%）的下界是 48.8%，还不够。样本越多，门槛越低：n = 100 时 60 次就够。

---

## 六、推荐你

### 1. 问题怎么写

问题是测量对象。问题写偏了，量得再准也是在量一件没人问的事。

- **用买家原话。** 去 Reddit、社区、客服记录、销售电话里找用户怎么描述自己的问题。用户说「怎么给海外客户开发票」，不说「跨境电子发票 SaaS 解决方案」
- **按品类和场景写。** 品类：「有什么好用的发票 API」。场景：「SaaS 怎么自动给每笔订阅开发票」。场景问题往往竞争少，小产品更容易进去
- **一个问题只问一件事。** 「最好的、便宜的、开源的发票工具」是三个问题
- **改问题就是改测量对象。** 改一个字就算新版本，从头攒样本，不和旧版本比（经验）

先写 10–20 个非品牌问题。问题数 × 引擎数 × 每天次数就是每天的调用量：

```text
calls per day   = enabled questions × engines with a key × runs per day
tokens per call ≈ 60 (question) + 900 (answer) = 960
tokens per day  ≈ calls per day × 960
```

```text
20 questions × 3 engines × 3 runs = 180 calls ≈ 172,800 tokens a day
```

（每次调用的 token 数是我们的估计，回答长度因引擎而异，先验。）

### 2. 竞品怎么选

声量的分母是你列的竞品。列错了，声量就没意义。

- 列买家**真的会拿来和你比**的产品：用户在对比文章里、评论里、销售电话里提到的那几个
- 不要列行业巨头凑数。你做发票 API，列 SAP，声量永远是个位数，也不说明任何问题
- 3–5 个就够。少于 3 个，声量太容易被一个竞品左右
- 回答里反复出现、但你没列的产品，就是该加的竞品

### 3. 引用缺口

带搜索的回答会列出依据的网页。这是整个 AI 推荐里最能落到行动上的数据。

```text
citation gap on a question = competitor sites are cited AND your site is not
                             (over the answers in the chosen period)
```

一个问题上，竞品的站被引用了、你的站没有，就是一个缺口。对每个缺口，看被引用的是哪些站，你会发现反复出现的就那么几类：

- 「最好的 X 工具」「X 的 10 个替代品」这类列表文章
- 目录站、awesome 列表
- Reddit、Hacker News、Stack Overflow 上的讨论
- 测评站、对比站

Wix Studio 的研究看了 7.5 万个回答、100 多万条引用，列表类文章在商业类问题里占引用的 40.9%（观察，转引自 [目录站与 awesome 列表](../growth/目录站与awesome列表.md) 第六节）。这些列表文章的作者，又常常是从目录站和 awesome 列表里找候选的。

第三节第 6 条的引用稳定度在这里用得上：稳定度高（≥ 70）的问题，来源已经被几个站占住，要上的就是那几个站；稳定度低（< 40）的问题，来源还在换，你自己写一篇好的页面也有机会被引。

### 4. 去这些来源上出现

把反复出现的来源列成一张短表，**每周去一个**：

| 来源类型 | 怎么上 | 参考 |
| --- | --- | --- |
| 目录站、awesome 列表 | 按规则提交，描述用第五节那份品牌事实 | [目录站与 awesome 列表](../growth/目录站与awesome列表.md) |
| 社区讨论 | 先当成员，回答真实的问题，在合适的时候提到你 | [怎么找社区](../growth/怎么找社区.md) |
| 列表文章、测评 | 联系作者，给出能核对的信息，请他评估是否加进去 | [冷外联](../growth/冷外联.md) |

每一次都记成一个实验：假设（「上了 X 之后，问题 Q 的可见性会上升」）、指标、观察窗口、上线日期。窗口到了用第三节第 4 条的 Newcombe 区间判断：整个区间在 0 以上才算有效。

站外提及攒起来很慢，一周一个，三个月十二个，比一周海投五十个、三个月后一个都说不清有没有用要好（经验）。

---

## 七、节奏

AI 回答每天都在变，看得越勤越容易被噪声牵着走。

### 一次

- 体检：访问层、发现层修到 pass（第四节）
- 写品牌事实（第五节）
- 写 3–5 个品牌问题、10–20 个非品牌问题，确认版本（第六节）
- 列 3–5 个竞品

### 每天（自动，不用人看）

- 每个问题、每个引擎采样几次（默认 3 次），存下回答原文、提到的品牌和顺序、引用的网页
- 只在出现异常时提醒：体检里访问层出了新的严重问题；某个引擎连续调用失败

### 每周（30 分钟）

- 认知度和它的区间（AI-2 阶段）
- 可见性、声量，按引擎、按问题看，带区间；哪些问题的 Newcombe 区间说是真的变了
- 读几条品牌问题的回答，记下说错的事实
- 引用缺口：本周新出现的缺口、反复出现的来源
- 本周去了哪个来源，记成实验

### 每月

- 窗口到期的实验：保留、延长还是放弃
- 回答里反复出现但没列的竞品，要不要加
- 问题还是不是买家的原话，要不要出新版本
- 各问题的引用稳定度，决定下个月去争哪些问题

### 周报模板

| 指标 | 来源 | 本周 | 上周 | 4 周前 |
| --- | --- | --- | --- | --- |
| 体检：访问层 / 发现层 | 体检 | | | |
| 成功回答数（品牌 / 非品牌） | 采样 | | | |
| 认知度（区间） | 品牌问题 | | | |
| 可见性（区间） | 非品牌问题 | | | |
| 首位 / 前三 | 非品牌问题 | | | |
| 声量 | 非品牌问题 | | | |
| 引用你站的回答占比 | 非品牌问题 | | | |
| 引用占比 | 非品牌问题 | | | |
| 真的变了的问题（↑ / ↓） | Newcombe 区间 | | | |
| 引用缺口数 | 引用 | | | |
| 本周说错的事实 | 读回答 | | | |
| 本周去的来源 | 实验记录 | | | |
| 问题版本 / 竞品列表有没有改 | 设置 | | | |

最后一行很重要：改过问题或竞品的那一周，可见性和声量不能和之前比。

---

## 八、什么时候算过了这一关

| 阶段 | 信号 | 判定 | 怎么算 |
| --- | --- | --- | --- |
| AI-1 | 体检访问层 pass | 自动 | 最近一次体检，访问层没有严重或警告问题 |
| AI-1 | 体检发现层 pass | 自动 | 最近一次体检，发现层没有严重或警告问题 |
| AI-2 | AI 认识你 | 自动 | 品牌问题上的认知度至少 30 个回答，95% 区间下界不低于 50% |
| AI-3 | 不设终点 | 人看 | 每周看可见性和声量的趋势，用 Newcombe 区间判断真的变了没有 |

阈值都是先验，不是任何 AI 公司的规则。我们会用真实项目校准，再写回这里。

AI-3 不设终点，是因为它没有一个「过了就不用管」的状态：竞品在做，引擎在换模型，来源在变。能做的是让每周的动作可以被量出来。

---

## 九、常见误判

| 看到的 | 以为是 | 多半是 |
| --- | --- | --- |
| 昨天可见性 60%，今天 30% | 掉了 | 单日样本太小，一天几次采样，区间宽得什么都说得通 |
| 「AI 排名」从第 2 变第 5 | 被竞品超了 | 名次本来就每次都变；看首位、前三的比率和区间 |
| Perplexity 引用了你 8 次，ChatGPT 才 2 次 | Perplexity 更喜欢你 | 各家每个回答列的引用条数不一样，只能比占比 |
| 认知度 90% | AI 很推荐我 | 那是品牌问题；推不推荐看非品牌问题上的可见性 |
| 改了问题以后可见性涨了一倍 | 优化见效了 | 量的已经是另一件事，新旧版本不能比 |
| 加了一个竞品，声量掉了 | 我们变差了 | 分母变大了 |
| 20 次里提到 6 次，变成 5 次 | 在下降 | 差值区间跨过 0，看不出变化 |
| 放了 llms.txt，两周了没变化 | 没做对 | 回答引擎基本不读它 |
| robots.txt 全放行了，AI 还是抓不到 | AI 不来 | CDN / WAF 在前面拦了；或者正文要 JS 才渲染出来 |
| 挡了 `Google-Extended`，AI Overviews 里还有我 | 没生效 | `Google-Extended` 本来就不管 AI Overviews |
| 工具里提到了，自己在 ChatGPT 里问却没有 | 工具不准 | API 和网页版环境不同，单次手问也只是一个样本 |

---

## 参考资料

### AI 爬虫

- [OpenAI: Overview of OpenAI crawlers](https://developers.openai.com/api/docs/bots)（GPTBot、OAI-SearchBot、ChatGPT-User 各自的用途；约 24 小时生效）
- [Anthropic: Does Anthropic crawl data from the web, and how can site owners block the crawler?](https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler)（ClaudeBot、Claude-User、Claude-SearchBot；Crawl-delay；不建议靠封 IP）
- [Perplexity: Perplexity Crawlers](https://docs.perplexity.ai/guides/bots)（PerplexityBot 不用于训练；Perplexity-User 一般不理 robots.txt；约 24 小时生效）
- [Google: Google's common crawlers](https://developers.google.com/crawling/docs/crawlers-fetchers/google-common-crawlers)（Google-Extended 是控制令牌，不影响搜索收录和排名）
- [Google: AI features and your website](https://developers.google.com/search/docs/appearance/ai-features)（AI Overviews / AI Mode 没有额外要求；不需要 AI 文本文件；用 nosnippet 等控制）
- [RFC 9309: Robots Exclusion Protocol](https://www.rfc-editor.org/rfc/rfc9309.html)（分组匹配、4xx / 5xx 的处理、缓存 24 小时、不是访问控制）
- [Cloudflare: Content Independence Day](https://blog.cloudflare.com/content-independence-day-no-ai-crawl-without-compensation/)（2025 年 7 月默认拦 AI 爬虫）
- [Cloudflare: Block AI bots](https://developers.cloudflare.com/bots/additional-configurations/block-ai-bots/)（2026 年 9 月 15 日起的新默认值；Search / Agent / Training 三类）
- [Vercel: The rise of the AI crawler](https://vercel.com/blog/the-rise-of-the-ai-crawler)（主要 AI 爬虫不执行 JavaScript，2024 年 12 月）
- [llms.txt 提案（v2）](https://llmstxt.org/)
- [Ahrefs: llms.txt study](https://ahrefs.com/blog/llmstxt-study/)（13.7 万个域名，97% 未被请求，2026 年 5 月）

### 生成式引擎优化

- [Aggarwal et al., GEO: Generative Engine Optimization, KDD 2024（arXiv 2311.09735）](https://arxiv.org/abs/2311.09735)（[全文 HTML](https://arxiv.org/html/2311.09735)：加引用、引语、统计数字提升 30%–40%；堆关键词无效；排名第五的网页加引用后提升 115.1%）
- [Ahrefs: An Analysis of AI Overview Brand Visibility Factors (75K Brands Studied)](https://ahrefs.com/blog/ai-overview-brand-correlation/)（品牌提及 0.664，外链 0.218，2025 年 5 月）
- [Search Engine Journal: AI Recommendations Change With Nearly Every Query: SparkToro](https://www.searchenginejournal.com/ai-recommendations-change-with-nearly-every-query-sparktoro/566242/)（2,961 次采样，同一品牌列表不到 1%）

### 统计

- [Wikipedia: Binomial proportion confidence interval](https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval)（Wilson 1927；正态近似在小样本下不可靠）
- [SigmaXL: 2 Proportions Test and Confidence Interval](https://www.sigmaxl.com/2PropTest.html)（推荐 Newcombe-Wilson score 差值区间，出自 Newcombe, R. G. (1998), Interval estimation for the difference between independent proportions: comparison of eleven methods. *Statistics in Medicine*, 17: 873–890）
- [Wikipedia: Bray–Curtis dissimilarity](https://en.wikipedia.org/wiki/Bray%E2%80%93Curtis_dissimilarity)（Bray & Curtis 1957，取值 0–1）
