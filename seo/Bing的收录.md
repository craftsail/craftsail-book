# Bing 的收录

做新站的人，上线第一件事是提交 Google Search Console。Bing 常被当成顺手的事，接不接都行。

翻了几十篇新词新站比赛的复盘，Bing 出现的频率比想象中高：

- 有人 Google 几乎没给流量，大部分是 Bing 来的，「刚提交四天就给了流量，有时候排名甚至还能到第一」
- 有人 Google 收录从 160 多页掉到 12 页，剩下的流量基本靠 Bing 撑着
- 有人主词 CTR 跌到 0.1%，多语言、typo 词加上 Yandex / Bing，一天还有 8K UV，「不要只靠一个主词活着」
- 有人的老站 Google 只给了新站流量就归零，Bing 一直有流量，于是继续上内页、加外链，大半年后 Google 才开始给曝光
- 也有人 Bing 一直不收自己的站，反而先收了对手的站，到最后也没想明白为什么

这篇讲 Bing 怎么收录、怎么接、为什么不收、收了以后流量是什么样的。能核对的地方都附了来源，社区经验单独标出来。

---

## 一、为什么还要管 Bing

### 1. 份额不大，但集中在桌面

StatCounter 2026 年 9 月的数据：

| 范围 | Bing 份额 |
| --- | --- |
| 全球，所有设备 | 5.29% |
| 全球，桌面 | 14.62% |
| 美国，所有设备 | 9.91% |
| 美国，桌面 | 18.83% |
| 英国，桌面 | 18.33% |

Bing 是 Windows 和 Edge 的默认搜索，所以桌面份额比整体高很多，移动端很少。9 月这个数比前 11 个月（桌面 8.8%–10.7%）跳了一大截，StatCounter 还会修正，先别当成趋势。

做工具站、开发者产品的，用户多在电脑前，Bing 的实际占比往往比 5% 高。

### 2. 一份索引，好几个出口

进了 Bing 的索引，能出现的地方不止 bing.com：

| 出口 | 和 Bing 索引的关系 |
| --- | --- |
| Yahoo | 网页结果来自 Bing |
| DuckDuckGo | 自己也爬，但传统网页链接结果主要来自 Bing |
| Ecosia、Qwant | 以前主要靠 Bing；2025 年起在法国、2026 年 7 月起在德国逐步换成自建的欧洲索引（EUSP / Staan），其余地区仍部分用 Bing |
| Microsoft Copilot、Bing 的 AI 摘要 | 直接用 Bing 的索引做 grounding |
| Windows 搜索、Edge 侧栏 | Bing |

### 3. ChatGPT 这条要说清楚

2024–2025 年流行一个说法：ChatGPT 搜索用的是 Bing，所以 Bing 收录 = 进 ChatGPT。2026 年的证据已经不支持这么说：

- OpenAI 的 Nick Turley 2025 年 4 月在 Google 反垄断案里作证，说 ChatGPT 用 Bing 的搜索数据，自建索引承担 80% 搜索的目标「还要好几年」
- RESONEO 2026 年 7–8 月分析了 1,249 个回答、8.8 万条结果。7 月 21 日之前 ChatGPT 的数据流里会标出每条结果来自哪条管线：自建索引占大头，其次是抓取的 Google 结果，Bing 几乎没出现。自建索引里的 URL 只有 1.5% 出现在同一查询的 Bing 前 20
- 另一方面，微软说它的 grounding 服务（Web IQ，基于 Bing 索引）在给 ChatGPT 供数，有日志研究发现 Deep Research 的搜索都走 Bing

所以现在能说的是：**进 ChatGPT 主要靠 OAI-SearchBot 能抓到你**（robots.txt 别挡它），Bing 是其中一条备用来源。Copilot 那边才是确定吃 Bing 索引的。

### 4. 中国用户

Statista 的数据，2025 年 3 月中国占 Bing 全球访问的 28.25%，超过美国。中国大陆打开 bing.com 会跳到 cn.bing.com（国内版），桌面端份额 2025 年 11 月还有 33% 左右，移动端一年里从 18.8% 掉到 6%。

比赛复盘里有人直接说：「对于 bing，我自己的几个网站测试的情况是，获取到的大部分流量是来自中国的」。这些流量做广告变现可以，「开始我有个误区，认为中国流量广告没有收入，但其实也是有收入的」；做海外订阅付费就要谨慎。

在 GA4 里，会话来源能看到 `cn.bing.com` 和 `bing.com` 分开的两行，可以自己核对比例。

---

## 二、Bing 怎么发现和收录

Bing Webmaster Guidelines 2026 年 2 月改过一版，开头从「Bing 怎么发现、收录、排名网站」改成了「Bing 怎么发现、抓取、收录、评估和呈现内容，范围包括 Bing 搜索、Copilot 和 grounding API 结果」。遵守它的好处也写了三条：能被收录和排名、能被 AI 回答引用、能持续拿到流量。

发现 URL 的途径，官方推荐顺序：

1. **IndexNow**：主动推送
2. **XML sitemap**
3. **站内可抓取的链接**
4. **相关网站的外链**

和 Google 比，有几处明显不一样：

| | Google | Bing |
| --- | --- | --- |
| 主动推送 | 只有 Indexing API，限招聘和直播页面 | IndexNow，任何页面都能推 |
| sitemap 的 lastmod | 准确时会用 | 明确说是「关键信号」，用来决定重抓还是跳过 |
| sitemap 的 changefreq、priority | 忽略 | 也忽略 |
| robots.txt 的 crawl-delay | 不支持 | 支持，设了会明显变慢 |
| 收录门槛 | 宽，先收再说 | 更挑，薄内容、重复内容抓了也不收 |
| 渲染 JS | 会渲染，有延迟 | 能力弱，链接和正文最好在 HTML 源码里 |

最后一行 [SEO 落地](SEO落地.md) 里讲过：侧栏、分页、正文都要在服务端输出，「Google 也许能渲染，Bing 和很多 AI 爬虫不行」。

### 排名看什么

官方指南列过六个因素：

- **相关性**：页面和搜索意图的匹配程度
- **质量和可信度**：页面本身、作者或网站的声誉、讨论的水平
- **用户互动**：点没点、点进去是停留还是马上回到 Bing、有没有改搜索词
- **新鲜度**：持续更新的站更受欢迎
- **位置**：用户所在国家城市、页面语言、托管位置、访客来源
- **加载速度**

第三方 SEO 博客普遍认为 Bing 比 Google 更看重标题、H1、URL 里的精确匹配关键词，域名包含关键词也更吃香。这是经验说法，不是官方表述，但和新词站「域名、标题都覆盖主词」的做法一致。

---

## 三、上线当天接 Bing：步骤

### 1. 验证站点

最快的办法是从 Google Search Console 导入。授权以后，站点、用户权限、sitemap 一起带过来，不用重新验证。

不导入的话有四种验证方式：DNS 自动验证（只限支持 Domain Connect 的注册商）、CNAME 记录、meta 标签、上传 XML 文件。

### 2. 提交 sitemap

在 Webmaster Tools 里提交，或者在 robots.txt 里写上：

```text
Sitemap: https://example.com/sitemap.xml
```

Bing 2025 年 7 月的官方博客（Fabrice Canel、Krishna Madhavan）讲了 sitemap 在 AI 搜索里的写法：

- 新提交的 sitemap 会立刻抓，之后每天复查
- `lastmod` 用完整的 ISO 8601 时间，比如 `2026-10-19T08:00:00+00:00`
- 页面没改就别动 `lastmod`。Bing 早年的统计里，写错 lastmod 最常见的情况是所有 URL 的时间都一样，也就是 sitemap 生成时间
- 只放可收录、规范化（canonical）的 URL，noindex、分页重复、薄内容不要放
- 单个 sitemap 最多 5 万条

Canel 在 X 上被问到「这是不是就是给 AI 搜索做普通 SEO」，回答是：「Absolutely at 💯. Optimize for AI Search by following SEO guidelines.」

### 3. 接 IndexNow

IndexNow 是 Bing 和 Yandex 2021 年发起的开放协议。页面新增、更新、删除时，主动告诉搜索引擎去抓。

**放 key 文件**：生成一个 8–128 位的 key（字母、数字、短横线），在网站根目录放一个 `{key}.txt`，内容就是 key 本身。

**推单个 URL**（GET）：

```bash
curl "https://api.indexnow.org/indexnow?url=https://example.com/new-page&key=YOUR_KEY"
```

**批量推**（POST，一次最多 1 万条，必须同一个 host）：

```bash
curl -X POST "https://api.indexnow.org/indexnow" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{
    "host": "example.com",
    "key": "YOUR_KEY",
    "keyLocation": "https://example.com/YOUR_KEY.txt",
    "urlList": [
      "https://example.com/a",
      "https://example.com/b"
    ]
  }'
```

返回码：

| 码 | 意思 |
| --- | --- |
| 200 | 收到了 |
| 202 | 收到了，key 还在验证 |
| 400 | 格式不对 |
| 403 | key 无效，找不到 key 文件或内容不匹配 |
| 422 | URL 不属于这个 host，或者格式不合规 |
| 429 | 推得太频繁 |

推给任意一家，会自动共享给其他参与的搜索引擎。截至 2026 年 10 月，名单里有 Bing、Yandex、Seznam、Naver、Yep、Internet Archive、Amazonbot。**Google 不在里面**，网上说「Google 在试用」的都找不到官方依据。

比赛复盘里的说法是：「IndexNow 可以帮你铺开多个搜索引擎的流量，即使谷歌跑不出来，也许 Bing/Yandex 能跑出来，增大成功概率。」

几个注意：

- 只推真正变了的 URL。没变的重复推会消耗额度、触发 429，也可能拉低信任
- 推送不等于收录。搜索引擎自己决定抓不抓、收不收
- Cloudflare 有现成的 Crawler Hints（基于 IndexNow），WordPress、Astro 都有插件。自己写的话，在部署流水线里拿本次变更的 URL 列表 POST 一次就行

### 4. 手动提交和 URL Submission API

Webmaster Tools 里还有「提交网址」和 URL Submission API。官方帮助页现在的措辞是：IndexNow「强烈推荐」，手动提交、提交 API、Content Submission「仍然支持，但可能弃用」。

额度说明写的是每个站每天最多 1 万条、UTC 零点重置，但实际额度按站点给，站点验证时长是依据之一。有个 2026 年 10 月的开源项目用 API 读到自己的额度只有每天 100 条、每月 2,200 条；同一个站提交了 3.34 万条，抓了 2,600 条，收录 2,000 条。

所以首页和几个核心页可以手动提交一下，日常更新交给 IndexNow。

### 5. 检查清单

- [ ] robots.txt 没挡 Bingbot，也没有 `Crawl-delay`
- [ ] 核心页面在 HTML 源码里能看到正文和链接
- [ ] sitemap 只有可收录的 URL，`lastmod` 是真实修改时间
- [ ] IndexNow key 文件能访问，部署时自动推送
- [ ] Webmaster Tools 里用 URL Inspection 测一下首页的 Live URL
- [ ] GA4 里能按会话来源区分 bing.com 和 cn.bing.com

---

## 四、Bing 为什么不收

在 Webmaster Tools 的 URL Inspection 里，没收录的页面大致是三种状态：

| 状态 | 通常是什么问题 |
| --- | --- |
| 抓取错误 / 被屏蔽 | 技术问题：robots.txt、noindex、5xx、超时、content-type 不对 |
| 已发现但未抓取（Discovered but not crawled） | Bing 知道这个 URL，还没排上抓取：站新、外链少、服务器慢、内链太深 |
| 已抓取但未收录 / URL cannot appear on Bing | 抓过了，决定不收：多半是质量或信任问题 |

官方帮助页「Why is my site not in the index」给的原因：

- **站太新**：即使提交了 sitemap 和 URL，Bing 也要先找到链接、抓取、评估
- **抓取不稳定**：抓不顺的站，Bing 可能直接不选
- **没有外链**：新站最常见的问题
- **信任和质量信号**：新域名、外链少、内容高度相似或照搬厂商资料，Bing 可能延迟或限制展示。**Webmaster Tools 里没有任何设置能强制 Bing 展示页面**

微软问答社区里的顾问补充过几点：

- 「已发现但未抓取」加上「URL cannot appear on Bing」，常常是全站的质量或信任评估，不是技术问题
- 这类状态来自 Bing 的搜索质量评估，Webmaster 支持团队不能手动加白名单
- 有站的 IndexNow Insights 标了「Needs attention / Content quality」，提示是「等几天积累热度信号，再由 Bingbot 评估收录」
- 有人买的二手域名在一堆恶意网站和杀毒软件黑名单里，怎么提交都不抓。买老域名前先查历史

开头那个「Bing 先收了对手的站」的情况，比较可能的解释是：对方域名或外链先被 Bing 发现、抓取顺序靠前，或者对方站在 Bing 的信任评估里先过了。Bing 收录比 Google 更挑，也更依赖外链发现，同一个词谁先被收很难只靠提交来决定。

### 排查顺序

1. 在 Bing 搜 `site:example.com`，看有没有任何页面被收录；查单页用 `url:https://example.com/page`
2. URL Inspection 测 Live URL，确认 Bingbot 能拿到 200 和完整 HTML
3. Sitemaps 报告里点开 sitemap，看哪些 URL 被标成排除、未抓取、被屏蔽、重定向、低质量
4. Site Scan 跑一次技术检查
5. 技术都没问题：加外链、加内链、把薄页合并成完整的页
6. 全站一页都没收、技术完全干净：在 Bing 搜索结果页底部用「反馈」提交复查，或者联系 Webmaster 支持

还有一种情况：Webmaster Tools 里显示已收录，搜索里却搜不到。微软问答里 2026 年有不少这类报告，一般是排名太靠后或者还在评估，等一等或找支持。

---

## 五、收录以后：流量的样子

### 1. 「混乱期」

社区复盘里有人给 Bing 的一个现象取了名字：「混乱期，就是同一个关键词域名轮流给曝光的时期」，而且「bing 的混乱期比谷歌久一些」。

这和 Bing 官方说的「用户互动」因素对得上：新页面先轮流给展示，看点击、停留、有没有回到搜索页，再决定排名。新词刚出来、大家站都差不多的时候，Bing 的测试期更长，所以新站更容易先在 Bing 拿到流量。但这只是社区观察，没有官方说法，排名「最后还是掉了」也很常见。

### 2. 托底

几位复盘作者的共同经历是：Google 那边出问题（收录掉、主词被打、算法更新）时，Bing 和 Yandex 的流量还在。还有一例是对手被 Google 惩罚、搜索结果里消失了，Bing、Yahoo 上还能搜到。

两家的算法和收录节奏不同步，同时接上相当于多了一条腿。多语言站更明显，因为 Bing 和 Yandex 在部分国家和语种里的份额比全球平均高。

### 3. 能拿到多少

比赛复盘里，Bing 能成为主要来源的情况集中在：新词、竞争还没形成、Google 还没给信任的头几周，或者 Google 出了问题以后。正常运营的站里 Bing 一般是 Google 的零头。别指望靠 Bing 做大，但接上它几乎没有成本。

---

## 六、在 Webmaster Tools 里看什么

| 报告 | 看什么 |
| --- | --- |
| Search Performance | 点击、展示、CTR、平均排名，最长 24 个月，比 GSC 的 16 个月长 |
| URL Inspection | 单页收录状态、SEO 和结构化数据检查、Live URL 测试 |
| Sitemaps | 每份 sitemap 里的 URL 状态 |
| IndexNow Insights | 推送了多少、有哪些被标了问题 |
| Site Explorer | 站点结构、重定向、抓取问题、robots 屏蔽 |
| Backlinks | Bing 看到的外链、引荐域名、锚文本；能看竞品外链 |
| Keyword Research | Bing 的搜索词和搜索量 |
| Site Scan | 按需跑技术 SEO 检查 |
| Crawl Control | 按小时设抓取强度（robots.txt 优先） |
| AI Performance | 被 Copilot、Bing AI 摘要和部分合作方引用的次数 |

### AI Performance

2026 年 2 月 10 日公开预览，验证过站点就能看。指标有：

- **总引用数**
- **平均被引页数**：每天平均有多少个不同 URL 被 AI 引用
- **Grounding 查询**：AI 检索到你内容时用的关键词，是抽样不是全量
- **页面级引用**和趋势

数据保留约三个月，没有点击率。第三方博客说 2026 年 6 月加了意图、主题、引用份额和对比视图，10 月有 Topics Portfolio 测试版，以官方博客为准。

这个报告**只覆盖微软自己和合作方**，不包括 ChatGPT、Perplexity、Gemini、Google AI 概览。拿它当「AI 有没有引用我」的唯一依据会偏，配合 [AI 推荐的监测](AI推荐的监测.md) 里的抽样方法一起看。

---

## 七、给新词站的节奏

把复盘里的做法和官方建议合在一起：

**上线当天**

- GSC、Bing Webmaster Tools（从 GSC 导入）、Yandex 一起接上，提交 sitemap
- 部署时自动推 IndexNow
- 首页和核心页在 Bing 里手动提交一次

**第一周**

- 每天看 GA4 里 Bing 来的会话，区分 cn.bing.com
- Bing 先给流量是常见情况，别因为这个大改页面，先观察排名稳不稳
- 外链照常加。Bing 比 Google 更依赖外链发现新站

**之后**

- 每周看一次 Bing 的 Search Performance 和 Sitemaps 报告
- 新页面、改过的页面靠 IndexNow 推，`lastmod` 保持真实
- 每月看一次 AI Performance

**什么时候要多花精力**

- Bing 流量里海外用户占比高，或者 Copilot 引用在涨：值得单独优化标题和内容
- 流量绝大部分是 cn.bing.com、产品靠海外订阅：保持收录就行，别为它改方向

---

## 参考

### 官方

- [Bing Webmaster Guidelines](https://www.bing.com/webmasters/help/webmaster-guidelines-30fba23a)
- [Bing: Why is my site not in the index?](https://www.bing.com/webmasters/help/why-is-my-site-not-in-the-index-2141dfab)
- [Bing Webmaster Blog: Keeping content discoverable with sitemaps in AI powered search](https://blogs.bing.com/webmaster/2025/7/Keeping-Content-Discoverable-with-Sitemaps-in-AI-Powered-Search/)（2025 年 7 月）
- [Bing Webmaster Blog: Introducing AI Performance in Bing Webmaster Tools](https://blogs.bing.com/webmaster/2026/2/Introducing-AI-Performance-in-Bing-Webmaster-Tools-Public-Preview/)（2026 年 2 月）
- [Bing Webmaster Blog: submit up to 10,000 URLs per day](https://blogs.bing.com/webmaster/january-2019/bingbot-Series-Get-your-content-indexed-fast-by-now-submitting-up-to-10,000-URLs-per-day-to-Bing)
- [IndexNow documentation](https://www.indexnow.org/documentation)

### 指南更新和报道

- [Search Engine Roundtable: Microsoft updates Bing Webmaster Guidelines (a bit)](https://www.seroundtable.com/bing-webmaster-guidelines-updated-41002.html)（2026 年 2 月）
- [Search Engine Roundtable: Bing says optimize for AI search by following SEO guidelines](https://www.seroundtable.com/bing-ai-search-seo-guidelines-39866.html)
- [Search Engine Journal: Bing ranking factors revealed in update to Webmaster Guidelines](https://www.searchenginejournal.com/bing-ranking-factors-revealed-in-update-to-webmaster-guidelines/373381/)
- [Search Engine Land: Bing Webmaster Tools officially adds AI Performance report](https://searchengineland.com/bing-webmaster-tools-ai-performance-report-468751)
- [Search Influence: What 20,000 Copilot citations taught us](https://www.searchinfluence.com/blog/bing-ai-performance-report-copilot-citations/)
- [GEO Toolbox: Bing Webmaster Tools setup, IndexNow and AI Performance](https://geotoolbox.ai/blog/bing-webmaster-tools)（2026 年 10 月）

### 收录问题

- [Microsoft Q&A: Discovered but not crawled](https://learn.microsoft.com/en-us/answers/questions/5920490/bing-webmaster-tools-showing-discovered-but-not-cr)
- [Microsoft Q&A: Domain stuck in Discovered but not crawled despite successful live test](https://learn.microsoft.com/en-us/answers/questions/5935516/domain-stuck-in-discovered-but-not-crawled-despite)
- [Microsoft Q&A: Not indexing even after submitting the sitemap](https://learn.microsoft.com/en-us/answers/questions/5968472/my-website-is-not-indexing-on-bing-even-after-subm)
- [GitHub: Bing URL Submission API 实测额度](https://github.com/pixer-11/korea-travel-guide/pull/8)

### 份额和用户

- [StatCounter: Desktop search engine market share worldwide](https://gs.statcounter.com/search-engine-market-share/desktop/worldwide)
- [Search Engine Land: Microsoft Bing usage and revenue stats](https://searchengineland.com/guide/bing-users)
- [新浪财经：中国成为微软必应全球第一大市场](https://finance.sina.com.cn/roll/2025-07-18/doc-inffwuer6447686.shtml)
- [heise: Ecosia and Qwant launch German web index](https://www.heise.de/en/news/Ecosia-and-Qwant-launch-German-web-index-11385019.html)

### ChatGPT 和 Bing

- [Falia: What search engine does ChatGPT use? No longer Bing](https://falia.co/en/insights/ai-visibility/what-search-engine-does-chatgpt-use/)（汇总 RESONEO、Peec 的研究）
- [GEO Toolbox: How ChatGPT search works](https://geotoolbox.ai/blog/how-chatgpt-search-works)
