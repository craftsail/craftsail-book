<div align="center">

<h1><img src="./assets/logo.png" alt="craft sail logo" width="48" align="absmiddle" /> <span>CraftSail</span></h1>

<h3>技术很好，运营不行，只是产品死得体面一点</h3>


</div>

## 为什么写

能做出来，不等于有人找得到，也不等于有人付钱。

同一个功能，你刚做出来，别人很快就能跟上，甚至做得比你好。不加班就卷不过别人，加班又是个无底洞——典型的囚徒困境。

国内高端岗位少，肯为软件付钱的客户也少。大家挤在一起堆功能、砍价格，利润越来越薄。

要卷，就出去卷。赚海外的剪刀差。

[造物局 CraftSail](https://craftsail.com) 做过的方法，写在这里。能在线上核对的才进目录。

## 样本

CraftSail 是面向独立开发者和小团队的 AI / 开发者趋势数据门户。

每一步都应该能在 [craftsail.com](https://craftsail.com) 上打开、用 `curl` 核对。

## 现在可以读

| 章节 | 讲什么 |
| --- | --- |
| [把运营当成一道建模题](growth/把运营当成一道建模题.md) | 引子：技术人为什么转做海外运营，为什么说这是降维打击；用数学建模的办法拆运营——多看、多拆、多试；craftsail-book 和 craftsail-growth 一个学一个练；第一道题「流量从哪里来」 |
| [流量公式与阶段权重](growth/流量公式与阶段权重.md) | 把增长写成公式：Bass 扩散模型拆成「各渠道外部推力 + 口碑内生拉力」，渠道按脉冲 / 线性 / 累积 / 复利四种时间形状建模（残留、饱和、滞后、衰减）；21 个自变量清单、冷启动 / 1→10 / 10→100 三阶段权重表、用自己的数据估权重的方法、可运行的模拟脚本和每个方向的研究路线 |
| [SEO 落地](seo/SEO落地.md) | 独立 URL、真实链接、`<head>`、robots、sitemap、llms.txt；为什么内容站用 Astro 不用 Next.js；Blog 做成带分页的 feed 流，Help 用带 href 的 sidebar |
| [SEO 的监测](seo/SEO的监测.md) | 用 Search Console、GA4、Cloudflare 看抓取、收录、排名、点击、到站和 AI 流量；CTR 曲线、机会点击、收录时长等公式；每天 / 每周 / 每月的节奏和周报模板 |
| [新站的 SEO 监测](seo/新站的SEO监测.md) | 收录 200–1000 页的新站怎么看 GSC 和 GA4：匿名查询、平均排名、小样本 CTR、GA4 阈值哪些不能信；按 sitemap 看索引率，用 URL Inspection API 每天查全站，算收录时长和展示覆盖率；新站版周报，以及什么时候算过了这一关 |
| [AI 推荐的监测](seo/AI推荐的监测.md) | 用户问 AI 时有没有你：抓得到、认得你、推荐你三个阶段；可见性、认知度、声量、引用怎么量，为什么要多次采样和给区间；robots 和各家 AI 爬虫、llms.txt；引用缺口和站外提及；节奏、过关信号和常见误判 |
| [PMF 验证与第一批用户](growth/PMF验证与第一批用户.md) | 刚起步时怎么证明有人真的需要：七级证据链、Mom Test 访谈、手动拉来前 100 个海外 ToC 用户、留存走平、Sean Ellis 40% 调查（带置信区间）、早期定价、PMF 卷宗模板、没过时改哪一层、12 周计划，附参考文献 |
| [工具产品出海运营](growth/工具产品出海运营.md) | 工具类产品从选题到付费的整条链路：选题打分、一句话定位、渠道怎么选、首发清单、激活、留存、定价和收款、分阶段目标、每周实验流程、合规 |
| [工具产品看数据](growth/工具产品看数据.md) | 北极星和指标树、埋点事件表、UTM 规范、渠道质量、激活漏斗、留存队列、MRR 拆分、LTV / CAC、数字掉了怎么查、样本量和置信区间、看板、周报模板、SQL |
| [目录站与 awesome 列表](growth/目录站与awesome列表.md) | 早期网站提交目录到底值不值：实测多数「免费 dofollow」其实是 nofollow，Google 把低质量目录和换徽章链接列为链接垃圾；真正的价值是少数目录的引荐、新域名被发现、比较页和 AI 推荐的入选资格；五类目录怎么挑、awesome 列表怎么上和自建、三批提交计划、跟踪表、模型参数 |
| [怎么找社区](growth/怎么找社区.md) | 早期找用户聚集的社区：先列词表，再用六种方法找（搜索运算符、竞品引荐来源、SparkToro、平台内搜索、精选列表、关键词监控）；按发帖数、每帖评论、推广规则打分挑 3–5 个；用 HN 和 Stack Exchange 公开 API 量化话题热度；Reddit / HN / Stack Overflow / Discord 的硬规则；以 CraftSail 为例的实测数据 |
| [冷外联](growth/冷外联.md) | 主动给陌生人发邮件和私信：2026 年回复率基准（平均约 3.4%）、跟进和个性化的效果；小产品该找「放大器」而不是终端用户；联系方式哪些能用（不能抓 GitHub 邮箱）、四段式写法和英文模板；Gmail / Yahoo / Microsoft 发件要求、LinkedIn 和 X 的限制；美国、欧盟、英国、德国、加拿大的法律差异；以 CraftSail 为例的计划 |
| [SEO 术语表](seo/SEO术语表.md) | SEO、GSC、GA4、CTR、LCP、GEO、SSR 等缩写的英文全称和中文解释，按搜索结果页、抓取收录、页面标签、结构化数据、性能、监测、AI 搜索、前端架构分组 |

### 出海账号

X、LinkedIn、Reddit、YouTube、Threads、Bluesky 上的账号怎么从 0 做起来。按建模的办法：先读论文和源码，再搭公式、跑模拟、定阶段权重。

| 章节 | 讲什么 |
| --- | --- |
| [账号增长公式](account/账号增长公式.md) | 篇章的总纲：十条有依据的规律（Kwak 互关率、MusicLab 不可预测、Goel 一层广播、Bakshy 普通影响者、LinkedIn 弱关系实验、SEISMIC 帖子寿命、Buffer 频率和停更、X Premium 和链接）；从 X 2026 年开源代码里读出的真实权重；账号状态方程和交叉点 F\* = D0 / k，可运行的模拟；16 个自变量、A0 / A1 / A2 三阶段的时间分配、硬规则、周表和估参数的方法；CraftSail 的账号计划；这个篇章后续 13 篇的目录 |
| [科技博主的选题雷达](account/科技博主的选题雷达.md) | 科技博主去哪找选题、怎么自动化、怎么提高阅读量：2026 年还能用的数据源（GitHub 7 月关了 star 名单、9 月新增 star 历史接口，OSS Insight 热门 3 月停了，Papers with Code 已关站）；「好玩 / 有价值 / 趋势」三类选题的信号；打分卡和刷星识别（StarScout 约 600 万疑似假 star）；可直接运行的 [topic_radar.py](account/tools/topic_radar.py) 和实测输出；GitHub Actions 每天自动跑；哪些步骤交给机器、哪些必须人做；Upworthy 3 万多组标题实验得出的写法 |
| [科技博主的视频工具箱](account/科技博主的视频工具箱.md) | 做视频的方法论和工具：YouTube 按「每次展示的观看时长」排序，Test & Compare 也按观看时长份额判胜负；Guo 2014 年 690 万次观看研究的 6 分钟坎；Mayer 多媒体学习原则（附独立元分析的折扣）；录屏、剪辑、程序化视频（Remotion / HyperFrames / Manim）、字幕、配音怎么选；Claude Code 和 Codex 的视频 skill（Remotion、HyperFrames 官方 skill，video-use，openai/skills 已弃用改插件）和安装前的安全检查；把方法论写成自己的频道 skill；开源速览、教程、论文讲解三条流水线 |
| [避免同质化：四个内容方法论](account/避免同质化的四个内容方法论.md) | 流水线做久了内容会长得一样：X 源码里的作者去重和负反馈、YouTube 2025 年「不真实内容」政策、广告磨损实验（读者在乎时只有实质变化有用）、生成式 AI 让内容彼此更像（Doshi & Hauser 2024）；四个各管一个维度的方法论——Hero / Hub / Help 管分量和节奏，新闻价值（Harcup & O'Neill 2017）管角度，STEPPS 和 Berger & Milkman 管传播动机，探索与利用（Thompson 抽样）管实验；可运行的 [content_mix.py](account/tools/content_mix.py) 算同质化程度、排下周计划；一个选题一周的四种发法和发前自检 |

## 相关

- 线上样本：[craftsail.com](https://craftsail.com)
- 产品仓库：[github.com/craftsail/craftsail](https://github.com/craftsail/craftsail)
