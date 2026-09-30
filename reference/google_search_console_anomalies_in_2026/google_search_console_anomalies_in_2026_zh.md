# Google Search Console 分析：搜索词挖掘、排名衰退与索引诊断

**系列：** SEO 与 AI 搜索引擎优化框架 · 2026 年 5 月

**作者：** Joseph W. Anady — ThatDevPro 创始人兼首席工程师（计算机工程学士、网络安全硕士）

**更新时间：** 2026 年 5 月

## 核心要点

Google Search Console（GSC）提供直接来自 Google 的第一方数据，包括搜索词、点击次数、展示次数、排名、索引状态和核心网页指标（Core Web Vitals，CWV）。要提升展示量，应按页面、搜索词、国家或地区、设备和排名拆分分析。对于排名在第 3–15 位、有展示却没有点击的页面，应重点优化标题、搜索结果摘要、内部链接和搜索词定位。GSC 的价值在于帮助诊断问题，而不只是展示指标。

## 内容概览

本文兼顾配置指南与审计手册，围绕 Google 第一方搜索数据、索引状态、效果趋势和 AI 概览（AI Overviews，AIO）的影响展开，主要包括：

- GSC 报告的数据结构，以及多个资源的验证和权限管理。
- 搜索效果分析，包括流量衰退预警与快速增长页面识别。
- AI 概览报告、索引诊断、核心网页指标和增强功能报告。
- 链接调查与网址检查。
- 向 BigQuery 批量导出数据、常用 SQL 分析，以及在 Bubbles 级别的 Debian 源站上部署自动监控。

示例包含 Bash、Python、SQL 和服务配置。HTML 渲染实现另见 `framework-cross-stack-implementation.md`。

## 目录

- [1. 文档用途](#1-文档用途)
- [2. 客户信息与配置变量](#2-客户信息与配置变量)
- [3. 2026 年 GSC 数据结构](#3-2026-年-gsc-数据结构)
- [4. 账号、资源与权限架构](#4-账号资源与权限架构)
- [5. 搜索效果报告详解](#5-搜索效果报告详解)
- [6. AI 概览报告详解](#6-ai-概览报告详解)
- [7. 索引报告诊断](#7-索引报告诊断)
- [8. 核心网页指标与网页体验报告](#8-核心网页指标与网页体验报告)
- [9. 增强功能报告](#9-增强功能报告)
- [10. 链接报告调查](#10-链接报告调查)
- [11. 网址检查工具的使用方法](#11-网址检查工具的使用方法)
- [12. 向 BigQuery 批量导出数据](#12-向-bigquery-批量导出数据)
- [13. 常用 GSC 分析流程](#13-常用-gsc-分析流程)
- [14. 在 Bubbles 上部署 GSC 自动监控](#14-在-bubbles-上部署-gsc-自动监控)
- [配套框架](#配套框架)
- [常见问题](#常见问题)

## 1. 文档用途

### 1.1 本文的定位

本文是 GSC 分析的操作参考。其他 SEO 工作流最终都应回到 GSC 验证结果：排名跟踪工具提供估算，爬虫工具模拟抓取，日志分析工具帮助推断，而 GSC 提供 Google 自身的报告。

原文对 2026 年背景的概述如下：AI 概览约出现在 48% 的 Google 搜索中；2025 年第四季度推出的搜索效果 AIO 报告首次单独呈现 AIO 展示数据；核心网页指标已于 2024 年 3 月用 INP 取代 FID；自 2024 年 2 月全面开放的 BigQuery 批量数据导出（Bulk Data Export，BDE）突破了 1,000 行限制，并提供界面中未展示的网址级展示数据结构。

### 1.2 GSC 在 2026 年能衡量什么、不能衡量什么

**能够衡量或查看：**

- 网页、图片、视频、新闻、Discover 和 Google 新闻中的展示次数、点击次数、平均排名和点击率（CTR）。
- 网址级索引状态。
- 基于 Chrome 用户体验报告（CrUX）真实用户数据的网址组级核心网页指标。
- 各类增强功能对应的结构化数据有效性。
- 外部链接、内部链接数量和抓取统计。
- 人工处置、安全问题和站点地图提交状态。

**不在其衡量范围内：**

- 站内用户行为：由 GA4 衡量，参见 `framework-attribution.md`。
- Google 以外的搜索引擎。
- Google AIO 和 AI Mode 之外的 AI 引擎引用，参见 `framework-aicitations.md`。
- 付费搜索。

GSC 也不提供完整的搜索词数据。原文称，点击次数低于约 10 次的搜索词会被匿名化。排名是平均值：某个搜索词显示平均第 5 位，可能意味着一半展示排第 2 位，另一半排第 8 位。

### 1.3 三种使用模式

| 模式 | 适用工作 | 阅读顺序 |
| --- | --- | --- |
| A：配置模式 | 验证新资源、配置权限、关联 GA4 和 Google Ads、设置告警、提交站点地图、启用 BDE，并部署监控 | 按第 2–14 节顺序执行 |
| B：审计模式 | 检查现有 GSC 配置的完整性、数据质量和分析覆盖范围 | 直接阅读第 13 节 |
| C：混合模式 | 先审计建立基线，再补齐不符合要求的配置；适合接手已有部分配置的项目 | 先审计，再按问题补充配置 |

### 1.4 Claude Code CLI 的建议执行顺序

1. 阅读第 2 节，收集客户信息与配置变量。
2. 执行第 13 节的审计，建立覆盖范围基线。
3. 对权限不足的资源，按第 4 节完成验证和授权。
4. 应用第 5 节的搜索效果分析方法，再执行第 6 节的 AIO 分析。
5. 根据未编入索引的原因，执行第 7 节的诊断。
6. 检查第 8 节的核心网页指标和第 9 节的增强功能。
7. 执行第 10 节的链接调查。
8. 按第 11 节使用网址检查进行抽查。
9. 当 1,000 行限制影响分析时，按第 12 节启用 BDE。
10. 按周、按月运行第 13 节的查询。
11. 在自托管 Debian 源站上部署第 14 节的监控系统。

### 1.5 常见冲突与处理规则

| 问题 | 处理规则 |
| --- | --- |
| 已有网址前缀资源，但没有网域资源 | 添加网域资源；网址前缀资源只覆盖其中一部分 |
| GSC 由上一家代理机构验证，当前团队没有所有者权限 | 要求添加所有者权限；受限用户无法管理设置、移除请求和批量导出 |
| AI 概览展示混在常规搜索效果中 | 原文建议使用“搜索结果呈现”中的 AI Overviews 行，并称其于 2025 年第四季度全面开放 |
| 大量网址处于“已发现，尚未编入索引”状态 | 排查抓取预算或质量信号问题，见第 7 节 |
| CWV 真实用户数据中没有网址 | 网站的 Chrome 用户样本不足以进入 CrUX；改用 PageSpeed Insights 实验室数据 |
| 无法查看 16 个月以前的数据 | 受界面日期范围限制；使用 BDE 和 BigQuery 延长保留时间 |
| 多个站点触及 GSC API 配额 | 使用专门负责 GSC 报告的服务账号 |
| BDE 因数据结构不匹配而失败 | 原文要求使用空 BigQuery 导出数据集，已有表可能阻止导出 |

### 1.6 所需工具

- Google Search Console：`search.google.com/search-console`。
- Google Workspace 或 Gmail 账号。
- 域名注册商的 DNS 管理权限，用于 TXT 验证，优先采用此方法。
- Google Tag Manager 和 GA4，用于替代验证方式和产品关联。
- Bash 4+，用于 Shell 自动化；Python 3.11+，用于 API 操作。
- 自托管 Debian 源站（Bubbles 级别），用于自动监控；原文方案不使用第三方 CDN 或代理服务。
- 使用 BDE 时，还需要已启用结算的 Google Cloud 项目及 BigQuery 数据集。

### 1.7 与其他框架的关系

| 文档 | 用途 |
| --- | --- |
| `framework-initialaudit.md` | 网站初始审计，第一步是验证 GSC |
| `framework-ongoingaudit.md` | 使用 GSC 数据进行月度、季度审计 |
| `framework-attribution.md` | 协调 GSC 点击数据与 GA4 的多触点归因 |
| `framework-aioverviews.md` | 第 6 节 AI 概览报告的操作参考 |
| `framework-internallinking.md` | 第 10 节内部链接报告的使用方法 |
| `framework-linkbuilding.md` | 主要外链来源网站分析 |
| `framework-pageexperience.md` | 第 8 节核心网页指标分析 |
| `framework-schema.md` | 第 9 节增强功能分析 |
| `framework-technicalseo.md` | 第 7 节索引诊断 |
| `framework-reporting.md` | 面向客户的交付报告 |

## 2. 客户信息与配置变量

以下 YAML 用于记录业务信息、资源清单、验证方式、基线指标、API 权限和监控配置。字段名保留英文，便于直接用于自动化。

```yaml
# GSC 分析：客户信息与配置变量

# --- 业务与网站信息 ---
business_name: ""
primary_domain: ""
www_or_apex: ""
protocol: "https"
international_subdomains: []
language_subdirectories: []

# --- 资源清单 ---
domain_property_exists: false
url_prefix_properties: []
properties_owned: []
properties_full_user: []
properties_restricted: []

# --- 验证 ---
domain_verified_via: ""                  # dns_txt, html_file, html_tag, ga4, gtm
verification_redundancy: false
dns_provider: ""

# --- 产品关联 ---
ga4_property_linked: false
ads_account_linked: false
youtube_channel_linked: false
discover_eligibility_confirmed: false

# --- 搜索效果基线 ---
clicks_28d: 0
impressions_28d: 0
ctr_28d: 0.0
average_position_28d: 0.0
top_10_queries_by_clicks_28d: []
top_10_landing_pages_by_clicks_28d: []

# --- AI 概览基线（参见 framework-aioverviews.md） ---
ai_overview_tab_available: false
ai_overview_impressions_28d: 0
ai_overview_clicks_28d: 0
ai_overview_ctr_28d: 0.0
ai_overview_queries_cited: []

# --- 索引基线（参见 framework-technicalseo.md） ---
pages_indexed_count: 0
pages_not_indexed_count: 0
sitemap_count: 0
sitemap_urls_submitted_total: 0
sitemap_urls_indexed_total: 0
top_3_non_indexing_reasons: []

# --- 网页体验基线（参见 framework-pageexperience.md） ---
cwv_mobile_good_pct: 0.0
cwv_desktop_good_pct: 0.0
lcp_field_data_p75_ms: 0
inp_field_data_p75_ms: 0
cls_field_data_p75: 0.0
https_pct: 100.0

# --- 增强功能基线（参见 framework-schema.md） ---
enhancement_types_active: []
enhancement_errors_count: 0
enhancement_valid_count: 0

# --- 链接基线 ---
top_linking_sites_count: 0
top_linking_anchor_terms: []
internal_links_top_pages: []

# --- API 与 BigQuery 访问 ---
gsc_api_service_account_email: ""
service_account_property_access: false
bulk_data_export_enabled: false
bigquery_project_id: ""
bigquery_dataset_id: ""
bigquery_dataset_location: ""

# --- 监控基础设施 ---
monitoring_host: ""
monitoring_host_ip: ""                   # Bubbles 示例：169.155.162.118
nginx_vhost_path: ""                     # /var/www/sites/[domain]/
cron_schedule: ""
alert_email_recipient: ""
alert_smtp_relay: ""

# --- 报告交付 ---
delivery_cadence: ""
delivery_format: ""                      # pdf, looker_studio, metabase, grafana
client_dashboard_url: ""
```

按本文流程，开始正式分析前，应完成网域资源验证，确保当前团队拥有所有者或完整用户权限，并积累至少 28 天的数据。不满足这些前提时，先返回验证与配置流程。

## 3. 2026 年 GSC 数据结构

GSC 数据分为六个主要部分，每个部分包含一个或多个报告。

### 3.1 搜索效果（Performance）

涵盖搜索结果、Discover 和 Google 新闻。搜索结果中的网页、图片、视频和新闻通过“搜索类型”区分；Discover 与 Google 新闻分别有独立报告。

- **维度：** 搜索词、网页、国家或地区、设备、搜索结果呈现、搜索类型和日期。原文描述为：界面中可以筛选每个维度，并与另一个维度组合。
- **指标：** 点击次数、展示次数、CTR 和平均排名。
- **默认视图：** 最近三个月，不显示匿名化搜索词。
- **最长日期范围：** 16 个月。

原文称，自 2025 年第四季度起，“搜索结果呈现”维度包含独立的 AI Overviews 行，详见第 6 节。

界面对每个维度组合最多显示 1,000 行，CSV 和 Google 表格导出也受此限制。Search Analytics API 每次请求最多返回 25,000 行，可分页读取；BDE 导出到 BigQuery 没有这一行数上限。

### 3.2 索引编制（Indexing）

包含网页、视频网页、站点地图和移除工具。“网页”报告将网址分为“已编入索引”和“未编入索引”，并按原因细分，见第 7 节。每个子类别最多展示 1,000 个示例网址；原文建议通过 URL Inspection API 或 BDE 获取更完整的信息。

站点地图报告显示每份已提交站点地图的最后读取时间、状态、类型和发现的网址数量。发现数量不等于已编入索引数量，两者差距是判断索引健康状况的线索。

移除工具支持临时移除、过时内容移除和安全搜索过滤，但不会永久删除索引。

### 3.3 体验（Experience）

包括核心网页指标和 HTTPS。CWV 使用真实 Chrome 用户的 CrUX 数据，并按网址相似性分组。原文列出的“良好”阈值为：LCP 低于 2.5 秒、INP 低于 200 毫秒、CLS 低于 0.1，详见第 8 节。

HTTPS 报告显示已编入索引的网址中使用 HTTPS 的比例，本文将 100% 设为目标。旧版“移动设备易用性”报告已于 2023 年 12 月弃用；原文将后续移动端适配检查归入网址检查流程。

### 3.4 增强功能（Enhancements）

按 Google 检测到的结构化数据类型，报告有效项、警告和错误数量。只有检测到对应结构化数据，相关类型才会出现。

原文列举的类型包括：面包屑、徽标、站点链接搜索框、FAQ、HowTo、商品、评价摘要、视频、招聘信息、活动、食谱、练习题、数学求解器、数据集、课程、电影、软件应用和特别公告。

### 3.5 链接（Links）

包括外部链接和内部链接。外部链接分为获得最多链接的网页、链接最多的网站和最常见链接文字；内部链接按网址统计数量。报告使用抽样数据，并非完整链接清单。

### 3.6 设置、抓取统计、人工处置、安全问题、关联与 BDE

| 功能 | 提供的信息 |
| --- | --- |
| 抓取统计 | Googlebot 每日请求量、平均响应时间、总下载量，以及按请求目的、响应码、文件类型和 Googlebot 类型划分的分布 |
| 人工处置 | 人工审核施加的处罚 |
| 安全问题 | 网站被入侵或感染恶意软件等问题 |
| 关联 | 与 GA4、Google Ads、YouTube、Play Console 的关联 |
| 用户和权限 | 资源访问权限 |
| 批量数据导出 | BDE 集成配置，见第 12 节 |

### 3.7 数据时效与隐私聚合

| 数据类型 | 原文描述的更新节奏 |
| --- | --- |
| 搜索效果 | 延迟 24–48 小时，排名数据可能延迟 72 小时 |
| 索引 | 每周更新，部分分类每日更新 |
| CWV 真实用户数据 | 使用最近 28 天滚动窗口，每日更新 |
| 增强功能 | 随抓取更新 |
| 链接 | 通常以周为单位更新 |

隐私聚合会隐藏低量搜索词。原文称，点击次数低于约 10 次的搜索词会被匿名化，并从搜索词表中省略；API 和 BDE 虽然提供更多行，也受同样规则影响。最小聚合单位是按日统计的“搜索词、页面、国家或地区、设备、搜索结果呈现、搜索类型”组合及其展示、点击次数。

## 4. 账号、资源与权限架构

### 4.1 资源类型

| 类型 | 覆盖范围 | 验证与适用场景 |
| --- | --- | --- |
| 网域资源（Domain property） | 整个可注册域名，包括所有子域名、协议和路径 | 原文使用 DNS TXT 验证；适合绝大多数网站 |
| 网址前缀资源（URL prefix property） | 指定的网址前缀 | 支持多种验证方式；适合按子目录细分报告，或无法获得 DNS 权限时使用 |

例如，`example.com` 网域资源覆盖 `www.example.com`、`blog.example.com` 和所有协议变体。而 `https://example.com/` 与 `https://www.example.com/` 是两个不同的网址前缀资源。

推荐配置为：一个网域资源，加上需要单独报告的重要子目录或子域名对应的网址前缀资源。

### 4.2 验证方式

- **DNS TXT：** 在域名根区域添加 `google-site-verification=[token]`。生效时间通常为 5 分钟到 48 小时，记录必须持续保留。
- **HTML 文件：** 将 Google 提供的文件放到网站根目录，例如 `/var/www/sites/[domain]/google[token].html`。
- **HTML 标签：** 在首页的 `head` 中放入 `<meta name="google-site-verification" content="[token]">`。
- **Google Analytics：** 原文要求同一账号拥有 Analytics 管理权限。
- **Google Tag Manager：** 原文要求首页安装 GTM 代码，验证用户具有发布权限。

原文建议为重要资源同时保留 DNS TXT、GTM 和 HTML 文件验证，以减少单一验证方式失效带来的影响。

### 4.3 用户角色

| 角色 | 权限与限制 | 常见用途 |
| --- | --- | --- |
| 所有者（Owner） | 可验证资源、增删用户、修改设置、管理移除请求、编辑 BDE、管理关联和处理重新审核请求 | 资源负责人 |
| 完整用户（Full user） | 可读取所有报告、提交网址和站点地图、请求编入索引、处理人工处置；不能增删用户、修改设置、编辑 BDE 或管理关联 | 代理机构常用权限 |
| 受限用户（Restricted user） | 大部分报告只读；不能提交网址、提交站点地图、请求编入索引或查看设置 | 仅需查看报告的相关人员 |

代理机构至少应获得完整用户权限；如果承担全部 SEO 工作，原文建议申请所有者权限。

### 4.4 代理机构授权方式

**推荐方式：** 客户保留主要所有者身份，代理机构作为完整用户或额外所有者加入；用于自动报告的服务账号以受限用户身份加入，参见第 14 节。合作结束后移除代理机构和服务账号，客户仍可正常访问。

**应避免的方式：** 代理机构是唯一所有者，使用其自身 GTM 容器验证，客户始终没有所有者权限。合作开始时就应确保客户保留主要所有者身份。

### 4.5 关联 GA4、Google Ads 与 YouTube

- **GA4：** 通过 GSC“设置 → 关联”完成。原文要求拥有 GA4 编辑权限和 GSC 所有者权限。关联后，GA4 在“获客 → Search Console”中提供 GSC 报告，见 `framework-attribution.md`。
- **Google Ads：** 关联后启用付费与自然搜索报告，见 `framework-ppc-seo-coordination.md`。
- **YouTube：** 原文称，关联后可在视频标签页查看 YouTube 搜索效果。

## 5. 搜索效果报告详解

### 5.1 四个核心指标

| 指标 | 含义与解读 |
| --- | --- |
| 点击次数（Clicks） | 用户从 Google 搜索结果点击进入网站的次数。原文描述为：同一用户在同一会话中点击两次，计为两次 |
| 展示次数（Impressions） | 资源网址满足 Google 展示定义的出现次数。原文称，通常需要无需滚动即可看见，轮播等组件存在例外；图片需缩略图进入用户视口才计展示 |
| 点击率（CTR） | 当前筛选范围内的点击次数 ÷ 展示次数。原文强调，搜索词级 CTR 比资源整体 CTR 更适合判断摘要效果 |
| 平均排名（Average position） | 每次展示中，该资源排名最高的网址所处位置，按展示次数加权得到的平均值 |

### 5.2 聚合规则

排名默认按资源级别聚合。筛选单个搜索词，得到该搜索词的排名；筛选单个页面，得到触发该页面的所有搜索词的平均排名。“网页”标签页按页面聚合，“搜索词”标签页按搜索词聚合，因此相同数据在两个视图中可能显示不同的排名值。

点击和展示直接求和；CTR 则在每个聚合层级重新计算。资源级 CTR 不等于各搜索词 CTR 的算术平均值。

### 5.3 抽样与 1,000 行限制

界面每个维度最多显示 1,000 行。其他搜索词仍计入更高层级的汇总值，但不会逐行展示。CSV 导出同样受此限制。

Search Analytics API 每次请求最多返回 25,000 行，支持分页，且受项目级每日配额限制。BDE 导出到 BigQuery 没有这一行数上限。

### 5.4 对比界面

支持按日期范围、搜索词、页面、国家或地区、设备和搜索结果呈现进行对比。最常用的是日期对比：选择“最近 28 天”，点击“比较”，再选择“上一时间段”或“去年同期”。

结果包含点击、展示、CTR 和排名的变化列：

- 按点击变化降序排列，找出增长中的搜索词；升序排列，找出下滑搜索词。
- 在排名小于 10 的条件下，按展示变化升序排列，可找出排名保持稳定但展示下降的搜索词。原文将其视为 AI 概览等搜索结果功能分流的可能信号，见第 6 节。
- 将两个搜索词或两个页面叠加到一张图表中，便于分析关键词内耗，或对比迁移前后的旧网址与新网址，见第 13.3 节。

### 5.5 正则表达式筛选

GSC 使用 Google 的 RE2 正则表达式语法。原文给出的常用示例如下：

```text
^how to                          # 以“how to”开头的搜索词
\b(price|cost|pricing)\b         # 包含价格相关词汇的搜索词
^(?!.*brand_name)                # 不包含 brand_name 的搜索词
\bnear me\b                      # 包含“near me”的搜索词
/blog/                           # blog 目录下的页面
/(blog|articles|insights)/       # 三个目录中任意一个目录下的页面
\?utm                            # 带有 UTM 参数的页面
```

在筛选器中，将“网址包含”或“搜索词包含”切换为“自定义（正则表达式）”，再选择“匹配正则表达式”或“不匹配正则表达式”。正则筛选可与维度筛选组合使用。

### 5.6 搜索结果呈现维度

该维度按搜索结果页（SERP）功能拆分展示与点击。原文列举的类型包括：AI 概览、站点链接搜索框、FAQ、HowTo、食谱、商品、评价摘要、视频、图片、焦点新闻、新闻、Discover、站点链接、练习题、数学求解器、招聘信息、活动、课程信息、电影、软件应用和 Web Light。其中 AI 概览被描述为自 2025 年第四季度加入，见第 6 节。

### 5.7 日期范围

默认范围为最近三个月，最长 16 个月，最短一天。原文描述为：日期筛选包含起止日期，按用户时区计算，默认时区为太平洋时间。界面和 API 均受 16 个月限制；原文建议通过 BDE 和 BigQuery 保留更长期的数据。

### 5.8 国家或地区与设备维度

国家或地区按搜索用户所在地筛选。国际化网站可据此单独分析各市场表现。

设备分为移动设备、桌面设备和平板电脑。移动端与桌面端的 CTR 和排名可能存在明显差异。原文指出，移动端自然结果上方往往有更多搜索结果功能，因此移动端 CTR 通常较低。

## 6. AI 概览报告详解

### 6.1 推出时间线

原文将 AI 概览的演进概括为：2024 年年中进入美国 Google 搜索，此前以“搜索生成体验”（Search Generative Experience）形式出现；最初其展示数据被计入 GSC 常规网页搜索效果；2025 年第四季度，“搜索结果呈现”维度推出独立的 AI Overviews 行；到 2026 年第一季度，具有足够 AIO 引用量的资源普遍可用。

### 6.2 什么算作 AI 概览展示

按原文定义，资源网址在搜索结果页的 AI 概览中被列为来源，就计为一次 AI 概览展示，无论用户是否点击。

原文还描述了与自然网页展示分别计数的方式：同一资源既被 AI 概览引用，又在同一搜索结果页的自然结果中排第 4 位，就分别获得一次 AI 概览展示和一次网页展示；各呈现类型的展示在资源层级汇总。

### 6.3 什么算作 AI 概览点击

用户点击 AI 概览中指向该资源的引用链接，才计为一次点击。点击概览正文、“查看更多”展开按钮或“继续提问”不计入。

由于用户常直接阅读概览而不点击来源，原文认为 AIO 引用 CTR 通常低于自然排名第 1 位的 CTR。文中引用 Surfer SEO 于 2025 年 12 月对 173,902 个网址的研究，称 AIO 引用 CTR 平均为 2.1%–3.4%，而自然排名第 1 位为 22%–28%；同时称 AIO 引用点击的转化率约为普通自然搜索访问的 23 倍，因此单次点击价值更高。

### 6.4 将 AI 概览影响与自然搜索影响分开

原文建议利用“搜索结果呈现”的 AI Overviews 筛选，分别记录 AIO 与其他自然搜索的表现：

1. 筛选 AI Overviews，记录展示、点击和 CTR。
2. 筛选非 AI Overviews，记录同样的指标。
3. 对比内容修改前后各 28 天的数据。
4. 若 AIO 增长、非 AIO 下降，表示可见度从传统自然结果转移到 AIO 引用；两者变化之和为净变化。
5. 若两者均增长，说明两种渠道同时受益。
6. 若 AIO 增长、非 AIO 基本不变，表示获得 AIO 引用的同时保持了自然搜索表现。

建议按月执行，追踪 AIO 占总可见度的比例。

### 6.5 AI 概览“第 0 位”与自然排名第 1 位

原文将 AI 概览在结果页顶部的位置称为“第 0 位”，并称 GSC 的 AI Overviews 行不提供排名指标。

文中认为：按单次展示衡量，自然排名第 1 位带来的点击更多；按单次点击衡量，AI 概览带来的转化价值更高。原文引用 Surfer 于 2025 年 12 月的研究，称部分 AIO 搜索词的自然 CTR 下降幅度可达 61%。在这些场景下，获得 AIO 引用可能成为重要的曝光渠道，因此应将其作为独立优化任务。详见 `framework-aioverviews.md`。

### 6.6 AI 概览数据的限制

原文描述，AI Overviews 数据聚合在“搜索结果呈现”维度，而非独立的逐搜索词报表；其建议做法是先筛选 AI Overviews，再切换到“搜索词”标签页查看搜索词层面的表现。

对于“竞争对手被引用而本站未被引用”的搜索词，GSC 不提供相应竞品数据。原文列举 Profound、Otterly、Athena HQ、BrightEdge AI Catalyst 和 Semrush AI Toolkit 等工具，用于抽样追踪竞争对手的 AI 概览引用情况。

### 6.7 Discover 与 Google 新闻搜索效果

**Discover：** 报告 Google Discover 移动端信息流中的展示和点击。Google 认为适合的网站可自动获得展示资格；其展示数据与网页搜索分别统计。

**Google 新闻：** 原文将其描述为 Google 新闻和焦点新闻轮播中的展示与点击，并将展示资格与通过 Publisher Center 纳入 Google 新闻关联起来。参见 `framework-newsseo.md`。

## 7. 索引报告诊断

“索引编制 → 网页”报告按原因对网址分类。本节逐项说明原文列出的状态与处理方式。

### 7.1 已编入索引的子类别

| 状态 | 含义与处理 |
| --- | --- |
| 已提交并编入索引（Submitted and indexed） | 健康状态 |
| 已编入索引，但未在站点地图中提交（Indexed, not submitted in sitemap） | Google 自行发现并收录。如果该网址应出现在站点地图中，就补充进去；如果不应被索引，则应用 `noindex` 或规范网址标签 |
| 已编入索引，但被 robots.txt 屏蔽（Indexed though blocked by robots.txt） | Google 根据入站链接编入索引，却无法抓取内容。先允许抓取，再选择保留可抓取内容或添加 `noindex`。仅靠 robots.txt 屏蔽不能阻止编入索引 |

### 7.2 未编入索引的子类别与处理流程

#### 已抓取，尚未编入索引（Crawled, currently not indexed）

Google 已抓取，但决定不编入索引。原文将常见原因归为质量信号不足，例如内容单薄、与更有价值页面重复或主题不匹配。

处理步骤：

1. 按 `framework-hcs.md` 审查页面质量。
2. 若内容单薄或重复，通过 301 重定向合并，或添加 `noindex`。
3. 若内容独特且充实，按 `framework-infogain.md` 提高信息增益，增加内部链接，再请求编入索引。

#### 已发现，尚未编入索引（Discovered, currently not indexed）

Google 知道该网址存在，但尚未抓取。原文将抓取预算耗尽视为最常见原因。

检查抓取统计。如果相对于网站规模，Googlebot 抓取频率偏低，就排查服务器响应时间、robots.txt 限制和内部链接密度。增加来自首页和高权重页面的内部链接，并通过网址检查提交。系统性问题参见 `framework-technicalseo.md`。

#### 重复网页，用户未选定规范网页（Duplicate without user selected canonical）

Google 判定该网址重复，并自行选择规范网址。添加明确的 `<link rel="canonical">` 标签，指向预期规范网址，再通过网址检查验证。

#### 重复网页，Google 选择的规范网页与用户不同（Duplicate, Google chose different canonical than user）

先判断 Google 的选择是否合理；原文指出其选择往往是合理的。如果合理，接受合并。如果用户声明的网址才是正确选择，应增加指向该网址的内部和外部链接，并处理近乎相同的内容重叠。

#### 软 404（Soft 404）

页面返回 HTTP 200，但 Google 将其视为 404。触发因素包括内容单薄、主要内容缺失或内容类似错误提示，常见于高度依赖 JavaScript 的页面。

原文建议先检查初始响应：

```bash
curl -A "Googlebot" -s [url] | head -100
```

如果初始 HTML 缺少主要内容，按 `framework-contentfirst.md` 通过服务端渲染修复；如果内容单薄，补充实质内容；如果页面本就不应存在，返回 410。

#### 备用网页，具有适当的规范标记（Alternate page with proper canonical tag）

该网址将另一个网址声明为规范网址，Google 已采纳。属于健康状态。

#### 网页会自动重定向（Page with redirect）

网址返回 301 或 302。如果重定向符合预期，属于健康状态；若重定向链超过三跳，或目标返回 404，则需要检查。

#### 被 robots.txt 屏蔽（Blocked by robots.txt）

如果该网址应该被索引，就在 robots.txt 中允许抓取。

#### 被 noindex 标签排除（Excluded by 'noindex' tag）

如果是有意设置，属于正常状态。如果本应编入索引的网址意外带有 `noindex`，应检查 CMS 配置或测试环境设置是否误带入线上。

#### 服务器错误：5xx（Server error）

持续的 5xx 错误可能导致网址从索引中移除。检查服务器日志并修复原因，通过“设置 → 抓取统计”监控错误率，再请求重新抓取。

#### 未找到：404（Not found）

如果删除符合预期，网址会在数周内逐步退出索引；原文建议用 410 Gone 加快处理。如果不是有意删除，应恢复页面。

#### 网页已编入索引，但没有内容（Page indexed without content）

原文将这一少见状态解释为：JavaScript 执行后内容仍为空。处理方式与软 404 相同。

#### 因 403 或 401 被屏蔽（URL blocked due to 403 or 401）

由认证或授权限制导致。根据该访问限制是否符合预期，决定保留限制还是修复。

### 7.3 站点地图提交与监控

为每个资源提交站点地图或站点地图索引。格式可为 XML、RSS 或 Atom，本文优先使用 XML。网址超过 50,000 个或文件超过 50 MB 时，应拆分并使用站点地图索引。

建议按内容类型拆分：

- `/sitemap.xml`：站点地图索引。
- `/sitemap-pages.xml`：普通页面。
- `/sitemap-posts.xml`：文章。
- `/sitemap-products.xml`：商品。

分组有利于定位索引问题。提交站点地图并不保证收录，提交量与已编入索引数量之间的差距才是分析线索。原文举例：提交 5,000 个、收录 4,800 个属于健康情况；提交 5,000 个、只收录 2,000 个则提示系统性问题，需要按上面的未收录原因继续排查。

### 7.4 移除流程

移除工具适合紧急隐藏网址，为永久移除措施争取时间，不应作为长期移除的主要方式。临时移除约隐藏六个月；过时内容移除用于更新缓存摘要。

永久移除应通过添加 `noindex`、返回 404，或按原文偏好返回 410，然后等待重新抓取。

## 8. 核心网页指标与网页体验报告

### 8.1 三项指标与原文列出的 2026 年阈值

| 指标 | 衡量内容 | 良好 | 需要改进 | 较差 |
| --- | --- | --- | --- | --- |
| LCP：最大内容绘制 | 首屏最大可见内容元素的渲染时间 | 低于 2.5 秒 | 2.5–4.0 秒 | 高于 4.0 秒 |
| INP：交互到下一次绘制 | 用户交互到下一次画面绘制的时间 | 低于 200 毫秒 | 200–500 毫秒 | 高于 500 毫秒 |
| CLS：累积布局偏移 | 页面生命周期中的视觉稳定性与布局偏移 | 低于 0.1 | 0.1–0.25 | 高于 0.25 |

INP 于 2024 年 3 月取代 FID，衡量整个页面生命周期的响应能力，而不只是首次交互。网址只有三项都达到“良好”，整体才算“良好”；最差的一项决定最终评级。

### 8.2 真实用户数据与实验室数据

GSC 的 CWV 报告仅使用真实用户数据：由选择参与数据收集的 Chrome 用户提供，经 CrUX 聚合为最近 28 天的滚动窗口，并每日更新。

Chrome 用户量不足的网站可能不会出现在报告中。此时可使用 PageSpeed Insights 或 Lighthouse，对关键页面模板进行实验室测试。

如果 Lighthouse 表现良好，但真实用户数据较差，说明实际使用条件存在实验室未能复现的问题。建议先在 GSC 找出评级较差的网址组，再选择代表性网址运行 PageSpeed Insights，结合实验室数据和审计建议诊断。

### 8.3 网址组抽样

GSC 按网址组报告 CWV，Google 将模板和流量模式相近的网址归为一组。原文将组内表现描述为各网址的中位水平。

部署修复后，需要等待最近 28 天的窗口逐步滚动，效果才会完整体现在报告中。

### 8.4 移动端与桌面端分开分析

同一网址组在移动端和桌面端可能获得不同评级。移动设备计算能力较弱，因此 INP 差异可能尤其明显。

### 8.5 验证修复

部署修复后，对相应网址组点击“验证修复”（Validate fix）。Google 会在验证窗口内抽样检查，通常需要 28 天。详见 `framework-pageexperience.md`。

### 8.6 HTTPS 状态

HTTPS 报告显示已编入索引的网址中通过 HTTPS 提供服务的比例。本文目标是 100%，并要求修复仅支持 HTTP 或含混合内容的网址。

## 9. 增强功能报告

Google 在资源中检测到某类增强功能后，会提供相应报告，显示有效项、警告、错误及各状态的示例网址。

### 9.1 原文列出的 2026 年报告类型

| 类型 | 结构化数据或用途 |
| --- | --- |
| 站点链接搜索框（Sitelinks searchbox） | 使用 `WebSite` 和 `potentialAction: SearchAction`；原文称其仍具有富媒体搜索结果展示资格 |
| FAQ | 使用 `FAQPage`；原文称，自 2023 年 8 月起展示资格限于权威来源，并建议因 AIO 可能读取其内容而继续实现 |
| HowTo | 使用 `HowTo`；原文称自 2024 年起仅限移动端富媒体搜索结果，2025 年进一步收窄资格 |
| 商品（Product） | 包含价格、库存、评价和评分；分为商品摘要与商家信息，对电商尤其重要，见 `framework-ecommerceseo.md` |
| 评价摘要（Review snippet） | 使用 `Review` 或 `AggregateRating`，需遵守真实性要求；伪造评价可能触发人工处置 |
| 视频（Video） | 使用 `VideoObject`，用于视频轮播展示资格 |
| 招聘信息（Job posting） | 使用 `JobPosting`，可用于 Google Jobs |
| 活动（Event） | 使用 `Event`，可用于活动富媒体搜索结果 |
| 食谱（Recipe） | 使用 `Recipe`，在饮食领域竞争较激烈 |
| 面包屑（Breadcrumbs） | 使用 `BreadcrumbList`，用于搜索结果摘要中的路径 |
| 徽标（Logo） | 使用带有 `logo` 的 `Organization`，可用于知识面板 |
| 站点链接（Sitelinks） | 品牌搜索结果下的站点链接，并非直接由结构化数据驱动 |
| 其他类型 | 练习题、数学求解器、数据集、课程、电影、软件应用、图片元数据和特别公告 |

### 9.2 验证流程

1. 点击错误类别，查看受影响网址。
2. 修复代表性网址上的结构化数据。
3. 使用富媒体搜索结果测试（Rich Results Test）验证。
4. 点击“验证修复”。

Google 通常在数天到数周内重新抓取。警告不会直接阻止展示资格。详见 `framework-schema.md`。

## 10. 链接报告调查

### 10.1 链接最多的网站（Top Linking Sites）

列出指向该资源的外部域名，并按所链接的页面数量排序。报告为抽样数据，可用于了解外链基线，并与 Ahrefs 或 Semrush 交叉核对。

结合 `framework-linkbuilding.md`，可识别值得进一步合作的自然外链来源、原文建议评估是否拒绝的采集站或垃圾链接来源，以及确认链接建设活动是否已被 Google 索引发现。

### 10.2 最常见链接文字（Top Linking Text）

展示入站链接的锚文本分布，用于识别是否过度集中于完全匹配的商业关键词，从而增加算法处罚风险。原文认为健康的分布应包含品牌名、裸网址、通用文字、部分匹配和完全匹配锚文本。

### 10.3 获得最多链接的网页（Top Linked Pages）

找出外部入站链接最多的网址，用于识别自然获得外链的内容，并通过内部链接让这些页面更容易被发现。

### 10.4 内部链接（Internal Links）

按网址统计站内入站链接数量。数量很少的页面可能是孤立页面，数量很多的页面可能是内容中心页。

结合 `framework-internallinking.md`，可完成以下检查：

- 找出需要补充内部链接的孤立页面。
- 验证中心页与主题子页组成的链接结构。
- 检查分页、标签页、站内搜索结果等低价值页面是否获得过多链接。

GSC 内链数量为抽样结果，可能不同于 Screaming Frog、Sitebulb 等完整抓取工具。原文将 10%–20% 的差异视为正常范围，超过 50% 则可能由抽样偏差或 Google 尚未抓取链接来源页面导致。

## 11. 网址检查工具的使用方法

### 11.1 两种视图：已编入索引版本与实时版本

网址检查包含两个视图：Google 上次抓取并编入索引的版本，以及当前在线版本。对比两者，可以发现已发布内容与 Google 索引副本的差异。

| 视图 | 原文列出的信息 |
| --- | --- |
| 已编入索引版本 | 最近抓取时间、索引状态、抓取所用代理、是否允许抓取、网页获取状态、是否允许索引、用户声明的规范网址、Google 选择的规范网址、解析到的结构化数据及渲染截图 |
| 实时版本 | 重新获取后的 HTTP 响应、渲染 HTML、JavaScript 执行后的 DOM、JavaScript 控制台消息、实时解析到的结构化数据和渲染截图 |

如果索引视图仍显示三个月前的旧内容，实时视图可以帮助判断当前内容是否具备编入索引的条件。如果实时视图缺少结构化数据，可能是在实时渲染阶段解析失败。

### 11.2 实时测试流程

要验证页面改动是否对抓取程序可见，可先保存发布前后的 HTML 并对比：

```bash
# 发布前保存基线。
curl -A "Googlebot" -s https://example.com/path/ > /tmp/baseline.html
# 发布改动。
# 发布后重新获取并对比。
curl -A "Googlebot" -s https://example.com/path/ > /tmp/after.html
diff /tmp/baseline.html /tmp/after.html
# 在 GSC 网址检查中运行实时测试，确认渲染后的 HTML 和 DOM 包含改动，再点击“请求编入索引”。
```

这一流程可用于发现 CMS 仅在客户端渲染内容的情况：页面对用户看似正常，但 `curl` 获取的初始响应中没有改动。原文将其视为可能导致改动无法产生预期 SEO 效果的问题。

### 11.3 请求编入索引的每日配额

原文估计，每个资源每天可请求约 10–12 个网址编入索引，同时说明 Google 未精确公布该配额。

应优先用于确实需要及时重新抓取的情况：

- Google 尚未发现的新页面。
- 新内容需要及时体现的重要更新页面。
- 刚修复问题、希望尽快反映修复结果的页面。

原文建议不要将配额耗费在常规内容更新上。

### 11.4 渲染后 HTML 与初始响应 HTML 的对比

原文建议利用实时测试中的“查看测试的网页”面板，分析 JavaScript 执行后的 HTML，并与初始 HTTP 响应进行对比，以识别对 JavaScript 的依赖。

如果初始 HTML 没有主要内容，而渲染后的 HTML 才有，说明页面依赖 JavaScript。原文进一步认为，AI 概览的解析依赖初始 HTML，因此这种页面会影响 AIO 读取，参见 `framework-aioverviews.md` 第 4.5 节。

### 11.5 使用 URL Inspection API 自动检查

URL Inspection API 支持程序化检查，每个资源每天最多 2,000 个网址，可用于自动索引审计、关键网址监控和批量诊断。

```python
from googleapiclient.discovery import build
from google.oauth2 import service_account

SCOPES = ['https://www.googleapis.com/auth/webmasters.readonly']
creds = service_account.Credentials.from_service_account_file(
    '/etc/gsc-monitor/service-account.json', scopes=SCOPES)
service = build('searchconsole', 'v1', credentials=creds)
resp = service.urlInspection().index().inspect(body={
    'inspectionUrl': 'https://example.com/page/',
    'siteUrl': 'sc-domain:example.com'}).execute()
print(resp['inspectionResult']['indexStatusResult']['verdict'])
```

## 12. 向 BigQuery 批量导出数据

### 12.1 工作机制

BDE 每天将 Search Analytics 数据导出到 BigQuery 数据集，突破界面的 1,000 行限制与 API 单页 25,000 行限制，提供网址级的每日展示与点击数据。

配置需要 GSC 所有者权限，以及已启用结算的 Google Cloud 项目。原文给出的步骤为：

1. 创建 Google Cloud 项目。
2. 启用 BigQuery API。
3. 创建空 BigQuery 数据集。
4. 为 GSC 提供的 BDE 服务账号授予该数据集的 BigQuery Data Editor 角色。
5. 在 GSC“设置 → 批量数据导出”中填写项目 ID 与数据集 ID。
6. 确认导出。

首次导出通常在 48 小时内发生，之后每日执行，约比数据所属日期晚 24 小时。

### 12.2 数据结构

BDE 创建三张表。

**`searchdata_site_impression`：资源级展示表。**

原文将每一行描述为“日期、搜索词、国家或地区、搜索类型、设备”的组合，包含以下列：

- `data_date`、`site_url`、`query`、`is_anonymized_query`。
- `country`、`search_type`、`device`。
- `impressions`、`clicks`、`sum_position`。

**`searchdata_url_impression`：网址级展示表。**

原文将每一行描述为“日期、网址、搜索词、国家或地区、搜索类型、设备、搜索结果呈现”的组合。除上述列外，还列出 `url`、`is_anonymized_discover`，以及表示搜索结果功能的布尔字段：

- `is_amp_top_stories`、`is_amp_blue_link`、`is_job_listing`、`is_job_details`。
- `is_tpf_qa`、`is_tpf_faq`、`is_tpf_howto`、`is_weblite`、`is_action`。
- `is_events_listing`、`is_events_details`、`is_ai_overview`、`is_organic_shopping`。
- `is_review_snippet`、`is_special_announcement`、`is_recipe_feature`、`is_recipe_rich_snippet`。
- `is_subscribed_content`、`is_page_experience`、`is_practice_problems`、`is_math_solvers`。
- `is_translated_result`、`is_edu_q_and_a`、`is_product_snippets`、`is_merchant_listings`、`is_learning_videos`。

原文使用 `is_ai_overview = TRUE` 筛选获得 AIO 引用的网址与搜索词组合，并据此进行 AI 概览归因分析。

**`ExportLog`：导出日志。**

原文将其描述为每次每日导出对应一行，记录状态、表名、行数以及可能出现的错误。

### 12.3 SQL 示例

原文使用以下公式从 `sum_position` 计算平均排名：

```text
SUM(sum_position) / SUM(impressions) + 1.0
```

其中 `+ 1.0` 用于将内部从 0 开始的排名转换为 GSC 报告中从 1 开始的排名。

**查询最近 28 天按 AI 概览引用展示次数排序的搜索词：**

```sql
SELECT
query,
SUM(impressions) AS impressions,
SUM(clicks) AS clicks,
SAFE_DIVIDE(SUM(clicks), SUM(impressions)) AS ctr
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 28 DAY) AND CURRENT_DATE()
AND is_ai_overview = TRUE
AND is_anonymized_query = FALSE
GROUP BY query
ORDER BY impressions DESC
LIMIT 100;
```

**对比两个 28 天窗口的 AI 概览影响：**

```sql
WITH baseline AS (
SELECT url,
SUM(CASE WHEN is_ai_overview THEN impressions ELSE 0 END) AS aio,
SUM(CASE WHEN NOT is_ai_overview THEN impressions ELSE 0 END) AS org
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN '2026-03-01' AND '2026-03-28'
GROUP BY url
),
current AS (
SELECT url,
SUM(CASE WHEN is_ai_overview THEN impressions ELSE 0 END) AS aio,
SUM(CASE WHEN NOT is_ai_overview THEN impressions ELSE 0 END) AS org
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN '2026-04-15' AND '2026-05-12'
GROUP BY url
)
SELECT COALESCE(b.url, c.url) AS url,
COALESCE(c.aio, 0) - COALESCE(b.aio, 0) AS aio_delta,
COALESCE(c.org, 0) - COALESCE(b.org, 0) AS org_delta
FROM baseline b FULL OUTER JOIN current c USING (url)
WHERE COALESCE(c.aio, 0) > 0 OR COALESCE(b.aio, 0) > 0
ORDER BY aio_delta DESC;
```

### 12.4 成本考虑

原文估算 BigQuery 存储费用约为每 GB 每月 0.02 美元，查询费用约为每扫描 1 TB 数据 5 美元。中等规模资源每月通常导出 100 MB–1 GB，大部分代理项目的月费用预计为个位数美元。

原文描述的免费沙盒额度为 10 GB 存储、每月 1 TB 查询量，表保留时间为 60 天，并列出 BigQuery ML 和计划查询等限制；生产环境的长期保留需要付费方案。

使用 BDE 的主要保留优势在于：GSC 界面最长可查看 16 个月，而 BDE 本身没有这一固定保留上限，因此适合长期历史分析。

### 12.5 验证 BDE 配置

```bash
# 检查是否已创建表
bq ls your_project:your_dataset

# 检查最近一次导出的数据日期
bq query --use_legacy_sql=false 'SELECT MAX(data_date) FROM `dataset.searchdata_url_impression`'
```

如果 48 小时内仍未创建表，检查 `ExportLog` 中的错误消息。

## 13. 常用 GSC 分析流程

下文将表名简写为 `dataset.searchdata_url_impression`。使用时，请替换成实际项目与数据集对应的完整表路径。

### 13.1 流量衰退检测

**目的：** 找出自然搜索点击显著下降的页面。建议每周运行。

```sql
WITH last_28 AS (
SELECT url, SUM(clicks) AS now FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 28 DAY) AND CURRENT_DATE()
GROUP BY url),
prior_28 AS (
SELECT url, SUM(clicks) AS prior FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 56 DAY) AND DATE_SUB(CURRENT_DATE(), INTERVAL 29 DAY)
GROUP BY url)
SELECT l.url, l.now, p.prior, (l.now - p.prior) AS delta,
SAFE_DIVIDE(l.now - p.prior, p.prior) AS pct_change
FROM last_28 l JOIN prior_28 p ON l.url = p.url
WHERE p.prior >= 50
AND SAFE_DIVIDE(l.now - p.prior, p.prior) <= -0.25
ORDER BY delta ASC LIMIT 50;
```

该查询筛选上一窗口至少有 50 次点击、当前窗口下降至少 25% 的网址。继续排查排名下跌、AI 概览分流或季节性变化。

界面中的对应操作为：“搜索效果 → 比较最近 28 天与上一时间段 → 网页”，按点击差值升序排列。

### 13.2 快速增长页面检测

**目的：** 找出点击快速增长的页面，与衰退检测相反。

```sql
WITH last_28 AS (
SELECT url, SUM(clicks) AS now FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 28 DAY) AND CURRENT_DATE()
GROUP BY url),
prior_28 AS (
SELECT url, SUM(clicks) AS prior FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 56 DAY) AND DATE_SUB(CURRENT_DATE(), INTERVAL 29 DAY)
GROUP BY url)
SELECT l.url, l.now, COALESCE(p.prior, 0) AS prior,
l.now - COALESCE(p.prior, 0) AS delta
FROM last_28 l LEFT JOIN prior_28 p ON l.url = p.url
WHERE l.now >= 25
AND (p.prior IS NULL OR l.now - COALESCE(p.prior, 0) >= 25)
ORDER BY delta DESC LIMIT 50;
```

对这类页面可进一步投入：从相关高权重页面增加内部链接，以信息增益为目标扩充内容，并通过邮件通讯或社交渠道扩大传播。

### 13.3 关键词内耗检测

**目的：** 找出多个网址竞争同一搜索词的情况。原文将两个及以上网址竞争称为内耗；下方查询具体筛选至少三个网址的搜索词。

```sql
SELECT query, COUNT(DISTINCT url) AS competing_urls,
STRING_AGG(url ORDER BY clicks DESC LIMIT 5) AS top_5_urls,
SUM(clicks) AS total_clicks, SUM(impressions) AS total_impressions
FROM (
SELECT query, url, SUM(clicks) AS clicks, SUM(impressions) AS impressions
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 28 DAY) AND CURRENT_DATE()
AND is_anonymized_query = FALSE AND clicks >= 1
GROUP BY query, url
) sub
GROUP BY query
HAVING COUNT(DISTINCT url) >= 3
ORDER BY total_clicks DESC LIMIT 50;
```

处理方式是将较弱页面重定向并合并到表现最好的页面，或者区分搜索意图，让各网址分别覆盖不同子主题。

### 13.4 CTR 优化机会检测

**目的：** 找出展示量高但 CTR 低的搜索词与页面组合。重点优化标题、元描述和结构化数据等搜索结果摘要要素。

```sql
SELECT query, url,
SUM(impressions) AS impressions, SUM(clicks) AS clicks,
SAFE_DIVIDE(SUM(clicks), SUM(impressions)) AS ctr,
SUM(sum_position) / SUM(impressions) + 1.0 AS avg_position
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 28 DAY) AND CURRENT_DATE()
AND is_anonymized_query = FALSE
GROUP BY query, url
HAVING SUM(impressions) >= 1000
AND SAFE_DIVIDE(SUM(clicks), SUM(impressions)) < 0.02
AND SUM(sum_position) / SUM(impressions) + 1.0 <= 10
ORDER BY impressions DESC LIMIT 50;
```

原文建议：强化标题的价值表达，让元描述更直接回应搜索意图，补充 `Review` 或 `AggregateRating` 结构化数据，并添加 `Breadcrumbs` 以改善结果路径的可读性。

### 13.5 页面平均排名下跌预警

**目的：** 找出相较前一周平均排名明显下降的网址。高流量资源可每日运行。

```sql
WITH last_7 AS (
SELECT url, SUM(sum_position) / SUM(impressions) + 1.0 AS pos
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND CURRENT_DATE()
GROUP BY url),
prior_7 AS (
SELECT url, SUM(sum_position) / SUM(impressions) + 1.0 AS pos
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 14 DAY) AND DATE_SUB(CURRENT_DATE(), INTERVAL 8 DAY)
GROUP BY url)
SELECT l.url, l.pos AS now, p.pos AS prior, l.pos - p.pos AS delta
FROM last_7 l JOIN prior_7 p ON l.url = p.url
WHERE p.pos <= 10 AND l.pos - p.pos >= 3.0
ORDER BY delta DESC LIMIT 25;
```

该查询筛选前一周排名在前 10、本周至少下降 3 位的网址。应调查算法更新、服务器问题或内容改动的影响。

### 13.6 匿名搜索词占比

隐私聚合会隐藏低点击量搜索词。估算匿名部分的占比，有助于理解整体覆盖范围。

```sql
SELECT data_date,
SUM(CASE WHEN is_anonymized_query THEN clicks ELSE 0 END) AS anon_clicks,
SUM(CASE WHEN is_anonymized_query THEN impressions ELSE 0 END) AS anon_impr,
SUM(CASE WHEN NOT is_anonymized_query THEN clicks ELSE 0 END) AS named_clicks,
SUM(CASE WHEN NOT is_anonymized_query THEN impressions ELSE 0 END) AS named_impr
FROM `dataset.searchdata_url_impression`
WHERE data_date BETWEEN DATE_SUB(CURRENT_DATE(), INTERVAL 28 DAY) AND CURRENT_DATE()
GROUP BY data_date ORDER BY data_date;
```

原文估计，成熟资源的匿名搜索词通常占总展示量的 20%–50%。

### 13.7 审计评分表

| 编号 | 检查项 | 通过／未通过 |
| --- | --- | --- |
| GSC1 | 网域资源已通过 DNS TXT 验证 | |
| GSC2 | 已启用两种或以上验证方式，实现冗余 | |
| GSC3 | 站点地图已提交并成功处理 | |
| GSC4 | 已通过“设置 → 关联”连接 GA4 | |
| GSC5 | 已添加用于自动化的服务账号 | |
| GSC6 | 每周检查搜索效果 | |
| GSC7 | 每月使用 AI Overviews 行分析 | |
| GSC8 | 每月审查未编入索引的子类别 | |
| GSC9 | 已监控人工处置和安全问题 | |
| GSC10 | 已按 `framework-pageexperience.md` 处理 CWV | |
| GSC11 | 已按 `framework-schema.md` 修复增强功能错误 | |
| GSC12 | 已使用网址检查抽查实时版本与索引版本 | |
| GSC13 | 已配置 BDE 导出到 BigQuery | |
| GSC14 | 每周运行衰退检测查询 | |
| GSC15 | 每月运行关键词内耗查询 | |
| GSC16 | 每月运行 CTR 机会查询 | |
| GSC17 | 已按第 14 节配置自动告警 | |
| GSC18 | 已通过自托管 Metabase 或 Grafana 提供仪表盘 | |

满分 18 分。原文将至少 16 分，且关键项 GSC1、GSC3、GSC4、GSC10、GSC13 全部通过，定义为一流水平。

## 14. 在 Bubbles 上部署 GSC 自动监控

原文方案在自托管 Debian 源站上运行，示例为 Bubbles 级别主机 `169.155.162.118`。系统每日拉取 GSC 数据，通过 Gmail SMTP 发送衰退告警，并使用自托管 Metabase 或 Grafana 展示仪表盘，不依赖第三方 CDN、边缘代理或托管分析服务。

### 14.1 架构

单台 Debian 主机，由 nginx 通过 80 和 443 端口提供外部访问，各组件职责如下：

| 组件 | 职责 |
| --- | --- |
| nginx | 通过虚拟主机提供 Metabase 或 Grafana 访问，并使用 `htpasswd` 认证 |
| systemd 定时器 | 每日执行 Python 脚本，通过 Search Analytics API 拉取数据 |
| SQLite | 存储每日聚合数据 |
| BigQuery（可选） | 接收 BDE 原始数据 |
| Gmail SMTP | 发送告警邮件 |

该模式沿用原文作者现有的 TDG 监控部署方式。

### 14.2 服务账号配置

```bash
gcloud services enable searchconsole.googleapis.com --project=PROJECT
gcloud iam service-accounts create gsc-monitor --project=PROJECT
gcloud iam service-accounts keys create ~/secrets/gsc-monitor.json \
--iam-account=gsc-monitor@PROJECT.iam.gserviceaccount.com
```

将服务账号邮箱作为完整用户添加到每个 GSC 资源中。

### 14.3 每日拉取脚本

脚本使用服务账号认证，拉取各资源最近三天的搜索效果数据以补齐缺口，写入或更新 SQLite 的 `daily_aggregates` 表，运行衰退查询，并在发现符合条件的页面时发送 Gmail 告警。

```python
#!/usr/bin/env python3
import os, smtplib, sqlite3
from datetime import date, timedelta
from email.mime.text import MIMEText
from googleapiclient.discovery import build
from google.oauth2 import service_account

SA, DB = '/etc/gsc-monitor/service-account.json', '/var/lib/gsc-monitor/data.db'
PROPS = [{'site': 'sc-domain:example.com', 'name': 'example'}]
TO, FROM = 'joseph@thatdeveloperguy.com', 'gsc-monitor@thatdeveloperguy.com'
PW = '/etc/gsc-monitor/smtp-password'
SCOPES = ['https://www.googleapis.com/auth/webmasters.readonly']

def svc():
    c = service_account.Credentials.from_service_account_file(SA, scopes=SCOPES)
    return build('searchconsole', 'v1', credentials=c)

def pull(s, site, a, b):
    return s.searchanalytics().query(siteUrl=site, body={
        'startDate': a.isoformat(), 'endDate': b.isoformat(),
        'dimensions': ['date','query','page'], 'rowLimit': 25000}).execute().get('rows', [])

def db():
    os.makedirs(os.path.dirname(DB), exist_ok=True)
    c = sqlite3.connect(DB)
    c.execute("""CREATE TABLE IF NOT EXISTS daily_aggregates (
    site TEXT, d TEXT, q TEXT, u TEXT, clicks INT, impr INT, ctr REAL, pos REAL,
        PRIMARY KEY (site, d, q, u))""")
    c.commit(); return c

def store(c, site, rows):
    for r in rows:
        d, q, u = r['keys']
        c.execute("INSERT OR REPLACE INTO daily_aggregates VALUES (?,?,?,?,?,?,?,?)",
        (site, d, q, u, r['clicks'], r['impressions'], r['ctr'], r['position']))
    c.commit()

def decay(c, site):
    t = date.today()
    cur = c.execute("""WITH a AS (SELECT u, SUM(clicks) n FROM daily_aggregates
        WHERE site=? AND d BETWEEN ? AND ? GROUP BY u),
    b AS (SELECT u, SUM(clicks) p FROM daily_aggregates
        WHERE site=? AND d BETWEEN ? AND ? GROUP BY u)
    SELECT a.u, a.n, b.p, (a.n-b.p) FROM a JOIN b ON a.u=b.u
        WHERE b.p>=20 AND (1.0*(a.n-b.p)/b.p)<=-0.30 ORDER BY (a.n-b.p) ASC""",
        (site, (t-timedelta(days=7)).isoformat(), t.isoformat(),
        site, (t-timedelta(days=14)).isoformat(), (t-timedelta(days=8)).isoformat()))
    return cur.fetchall()

def alert(subj, body):
    pw = open(PW).read().strip()
    m = MIMEText(body); m['Subject'], m['From'], m['To'] = subj, FROM, TO
    with smtplib.SMTP_SSL('smtp.gmail.com', 465) as s:
        s.login(FROM, pw); s.send_message(m)

def main():
    s, c = svc(), db()
    end, start = date.today()-timedelta(days=1), date.today()-timedelta(days=3)
    for p in PROPS:
        store(c, p['site'], pull(s, p['site'], start, end))
        ds = decay(c, p['site'])
        if ds:
            body = f"GSC decay for {p['name']}\n\nURL | Now | Prior | Delta\n"
            for r in ds[:10]: body += f"{r[0]} | {r[1]} | {r[2]} | {r[3]}\n"
            alert(f"GSC Decay: {p['name']}", body)

if __name__ == '__main__': main()
```

保存为 `/usr/local/bin/gsc-monitor`，并通过 `chmod +x` 添加执行权限。

### 14.4 systemd 定时器

服务文件 `/etc/systemd/system/gsc-monitor.service`：

```ini
[Unit]
Description=GSC daily pull and decay alert
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/gsc-monitor
User=gsc-monitor
Group=gsc-monitor
```

定时器文件 `/etc/systemd/system/gsc-monitor.timer`：

```ini
[Unit]
Description=Run GSC monitor daily

[Timer]
OnCalendar=*-*-* 07:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

启用定时器：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now gsc-monitor.timer
```

### 14.5 Gmail SMTP 配置

在 Google 账号的“安全性 → 两步验证 → 应用专用密码”中生成密码，将其保存到 `/etc/gsc-monitor/smtp-password`，文件权限设为 `0600`，所有者设为 `gsc-monitor`。

脚本连接 `smtp.gmail.com:465` 进行认证。发送量较大时，原文建议替换为 Postfix 中继或 Mailgun。

### 14.6 使用 Metabase 或 Grafana 展示仪表盘

通过 Docker 启动 Metabase：

```bash
sudo docker run -d --name metabase -p 127.0.0.1:3000:3000 \
-v /var/lib/gsc-monitor/data.db:/data/data.db \
-v metabase-data:/metabase-data \
-e MB_DB_FILE=/metabase-data/metabase.db \
metabase/metabase:latest
```

原文随后要求在 Metabase 界面中添加指向 `/data/data.db` 的 SQLite 连接。

nginx 虚拟主机配置文件：`/etc/nginx/sites-available/gsc.example.com`。

```nginx
server {
    listen 443 ssl http2;
    server_name gsc.example.com;
    ssl_certificate /etc/letsencrypt/live/gsc.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/gsc.example.com/privkey.pem;
    auth_basic "GSC Dashboards";
    auth_basic_user_file /etc/nginx/htpasswd-gsc;
    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto https;
    }
}
server { listen 80; server_name gsc.example.com; return 301 https://$host$request_uri; }
```

创建认证用户并配置证书：

```bash
sudo htpasswd -c /etc/nginx/htpasswd-gsc operator
sudo certbot --nginx -d gsc.example.com
```

Grafana 是另一种选择，更适合时间序列可视化，同样可通过 Docker 或 apt 安装。

### 14.7 安全加固

- `/etc/gsc-monitor/` 与 `/var/lib/gsc-monitor/` 由 `gsc-monitor` 拥有，目录权限设为 `0750`，普通文件权限设为 `0640`。
- 服务账号密钥只允许 `gsc-monitor` 读取。
- SMTP 密码文件权限设为 `0600`。
- nginx 虚拟主机要求 `htpasswd` 认证。
- 使用 Let's Encrypt 提供 TLS，并通过 `certbot.timer` 自动续期。
- 入站仅开放 22、80、443 端口；SSH 仅允许密钥认证。

### 14.8 日常维护

| 频率 | 操作 |
| --- | --- |
| 每日，前 30 天 | 使用 `journalctl -u gsc-monitor.service` 检查执行情况与错误，确认 `daily_aggregates` 有新增数据 |
| 每周，稳定运行后 | 查看告警邮件和 Metabase 仪表盘，调查异常资源 |
| 每月 | 轮换服务账号密钥；SQLite 数据增长明显时清理旧数据；为 Debian 安装补丁；Metabase 内存增长时重启 |
| 每季度 | 检查各资源的数据覆盖情况，核对 `PROPS` 列表和服务账号权限，审查仪表盘虚拟主机的 nginx 访问日志 |

## 文档信息

- **版本：** 2.0。
- **创建日期：** 2026-05-14。
- **维护者：** ThatDeveloperGuy。

GSC 提供 Google 第一方搜索数据，各报告分别呈现 Google 如何理解网站：搜索词、点击、展示、索引状态、核心网页指标、结构化数据有效性、链接以及原文讨论的 AIO 引用。将其作为诊断工具，可以更早发现衰退、识别尚未达到高峰的增长页面，并分析 AIO 的影响。

建议配合 `framework-initialaudit.md` 与 `framework-ongoingaudit.md` 建立审计节奏，结合 `framework-attribution.md` 协调 GA4 分析，并参考 `framework-aioverviews.md` 制定 AIO 工作流程。

## 配套框架

| 文档 | 主题 |
| --- | --- |
| `framework-initialaudit.md`、`framework-ongoingaudit.md` | 使用 GSC 数据的初始与持续审计 |
| `framework-attribution.md` | 与 GA4 协同的多触点归因 |
| `framework-aioverviews.md` | AI 概览引用工作流程 |
| `framework-internallinking.md` | 内部链接报告的使用 |
| `framework-linkbuilding.md` | 主要外链来源报告的使用 |
| `framework-pageexperience.md` | 核心网页指标工作流程 |
| `framework-schema.md` | 增强功能工作流程 |
| `framework-technicalseo.md` | 索引诊断工作流程 |
| `framework-reporting.md` | 客户交付报告 |
| `framework-ga4.md` | GSC 引流后的站内行为衡量 |
| `framework-contentfirst.md` | 以可抓取的基础内容保障可索引性 |
| `framework-hcs.md` | 用有用内容系统的标准分析“已抓取，尚未编入索引” |
| `framework-infogain.md` | 信息增益标准 |
| `framework-newsseo.md` | Google 新闻展示渠道 |
| `framework-ecommerceseo.md` | 商品增强功能细节 |
| `framework-ppc-seo-coordination.md` | Google Ads 关联的使用 |
| `framework-aicitations.md` | 其他 AI 引擎的引用分析 |

## 常见问题

### GSC 的网域资源与网址前缀资源有什么区别？

网域资源覆盖整个可注册域名，包括所有子域名、协议和路径；原文采用 DNS TXT 验证，适合绝大多数网站。网址前缀资源只覆盖指定前缀，因此 `https://example.com/` 和 `https://www.example.com/` 是两个独立资源。后者支持多种验证方式，适合子目录报告或无法获得 DNS 权限的场景。

### GSC 如何统计 AI 概览的展示与点击？

原文将网址在 AI 概览中被引用定义为展示，与同一搜索结果页的自然网页展示分别计数；用户点击指向该资源的引用链接才计为点击。点击概览正文、“查看更多”或“继续提问”不计入。原文称，这些数据通过 2025 年第四季度推出的“搜索结果呈现 → AI Overviews”行单独查看。

### “已发现，尚未编入索引”是什么意思？如何修复？

表示 Google 已知晓网址，但尚未抓取。原文首先建议检查抓取预算：如果 Googlebot 抓取频率相对网站规模过低，就排查服务器响应时间、robots.txt 限制和内部链接密度。增加来自首页及高权重页面的内部链接，再通过网址检查提交。

### 如何找出自然搜索点击下滑的页面？

每周运行衰退检测查询，对比最近 28 天与前一个 28 天窗口，找出此前至少有 50 次点击、随后下降至少 25% 的网址。再调查排名下降、AI 概览分流或季节性变化。界面操作为：“搜索效果 → 比较最近 28 天与上一时间段 → 网页”，按点击差值升序排列。

### 为什么使用 BigQuery 批量导出，而不只看 GSC 界面？

界面每个维度组合最多显示 1,000 行，历史范围最长 16 个月；Search Analytics API 单次请求最多返回 25,000 行。BDE 可突破这些行数限制，保存每日网址级数据。

原文称 BDE 自 2024 年 2 月全面开放，并包含用于 AIO 归因的 `is_ai_overview` 字段。配置需要所有者权限、已启用结算的 Google Cloud 项目，以及空的 BigQuery 数据集。

## 相关框架主题

- **Google AI Overviews 与 AI Mode：** 优化模式与引用工程。
- **技术 SEO：** 抓取、索引、规范网址与 JavaScript 渲染。
- **网页体验：** LCP、INP、CLS、移动设备易用性和 HTTPS。
- **持续审计：** 月度与季度节奏，以及回归问题检测。
- **归因分析：** 多触点、末次点击与营销归因模型。
- **Schema.org 与结构化数据：** JSON-LD、`@id` 图谱和 `sameAs` 网络。
