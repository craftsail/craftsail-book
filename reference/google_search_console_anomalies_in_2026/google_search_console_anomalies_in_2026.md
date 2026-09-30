# Google Search Console Analysis: Query Mining, Position Decay, and Indexing

**Series:** SEO & AI Engine Optimization Framework · May 2026

**Author:** Joseph W. Anady — Founder & Lead Engineer, ThatDevPro (BA Computer Engineering, MA Cybersecurity)

**Updated:** May 2026

## Quick Answer

Google Search Console (GSC) is the only first-party source of Google query, click, impression, position, indexing, and Core Web Vitals data. To grow impressions, segment by page, query, country, device, and position. Pages ranking in positions 3–15 with impressions but no clicks need better titles, snippets, internal links, and clearer query targeting. Use GSC as a diagnostic instrument, not just a dashboard.

## Overview

This installation and audit reference covers first-party Google search data, indexing state, performance trends, and AI Overview impact measurement. It explains:

- GSC report schemas and verification across multiple properties.
- Performance analysis for decay alerts and rising-star detection.
- AI Overview reporting, indexing diagnostics, Core Web Vitals, and enhancements.
- Links investigation and URL Inspection.
- Bulk Data Export to BigQuery, SQL workflows, and automated monitoring on a Bubbles-class Debian origin.

Code examples include Bash, Python, SQL, and service configuration. For HTML rendering work, see `framework-cross-stack-implementation.md`.

## Contents

- [1. Document Purpose](#1-document-purpose)
- [2. Client Variables Intake](#2-client-variables-intake)
- [3. The GSC Data Schema 2026](#3-the-gsc-data-schema-2026)
- [4. Account, Property, and Permission Architecture](#4-account-property-and-permission-architecture)
- [5. Performance Report Deep Dive](#5-performance-report-deep-dive)
- [6. AI Overviews Tab Specifics](#6-ai-overviews-tab-specifics)
- [7. Indexing Report Diagnostics](#7-indexing-report-diagnostics)
- [8. Core Web Vitals and Page Experience Report](#8-core-web-vitals-and-page-experience-report)
- [9. Enhancement Reports](#9-enhancement-reports)
- [10. Links Report Investigation](#10-links-report-investigation)
- [11. URL Inspection Tool Methodology](#11-url-inspection-tool-methodology)
- [12. Bulk Data Export to BigQuery](#12-bulk-data-export-to-bigquery)
- [13. Common GSC Analysis Workflows](#13-common-gsc-analysis-workflows)
- [14. Automated GSC Monitoring on Bubbles](#14-automated-gsc-monitoring-on-bubbles)
- [Companions](#companions)
- [Frequently Asked Questions](#frequently-asked-questions)

## 1. Document Purpose

### 1.1 What This Document Is

This is the canonical operational reference for GSC analysis. GSC is the diagnostic instrument every other SEO workflow points back to. Rank trackers approximate, crawl tools simulate, log analyzers infer; GSC reports.

The 2026 reality: AI Overviews (AIO) appear on roughly 48 percent of all Google searches. The AIO tab in Performance, rolled out Q4 2025, separates AIO impression data for the first time. CWV thresholds moved to INP from FID in March 2024. Bulk Data Export to BigQuery (BDE), generally available since February 2024, removes the 1000 row cap and exposes URL level impression schema the UI never showed.

### 1.2 What GSC Does and Does Not Measure in 2026

GSC measures impressions, clicks, average position, and CTR across Web, Image, Video, News, Discover, and Google News. It measures indexing state at URL level. It measures Core Web Vitals at URL group level using Chrome User Experience Report (CrUX) field data. It measures structured data validity per enhancement type. It surfaces backlink data, internal link counts, crawl statistics. It applies manual actions and security issues. It exposes sitemap submission state.

GSC does not measure on site behavior (that is GA4, see `framework-attribution.md`). It does not measure non Google search engines. It does not measure AI engine citations outside Google AIO and AI Mode (see `framework-aicitations.md`). It does not measure paid search. It does not provide complete query data; queries under approximately ten clicks are anonymized. Position is an average; a query showing position 5 may have ranked position 2 for half the impressions and position 8 for the other half.

### 1.3 Three Operating Modes

**Mode A, Install Mode.** Verify a new property, configure access, link to GA4 and Google Ads, set up alerts, submit sitemaps, enable BDE, deploy the monitoring stack from Section 14. Follow Sections 2 through 14 in order.

**Mode B, Audit Mode.** Evaluate an existing GSC installation for completeness, data quality, analytical coverage. Skip to Section 13.

**Mode C, Hybrid Mode.** Audit first to baseline current state, then install for failed criteria. Common path for engagements inheriting partial configuration.

### 1.4 How Claude Code CLI Should Consume This Document

1. Read Section 2, collect client variables.
2. Run Section 13 audit to baseline coverage.
3. Apply Section 4 verification and permission for any property without proper access.
4. Apply Section 5 Performance methodology, then Section 6 AIO discipline.
5. Apply Section 7 indexing diagnostics per non indexed reason.
6. Apply Section 8 CWV review and Section 9 enhancements review.
7. Apply Section 10 links investigation.
8. Apply Section 11 URL Inspection for spot checks.
9. Apply Section 12 BDE where 1000 row cap binds.
10. Run Section 13 queries weekly and monthly.
11. Deploy Section 14 monitoring on a self hosted Debian origin.

### 1.5 Conflict Resolution Rules

| Conflict | Rule |
| --- | --- |
| URL prefix property exists but no domain property | Add domain property. URL prefix is a subset. |
| GSC verified by previous agency, no current owner access | Demand Owner add. Restricted lacks settings, removals, bulk export. |
| AI Overview impressions bundled into standard Performance | Use Search Appearance AI Overviews row (Q4 2025 GA). |
| Indexing report shows Discovered, currently not indexed in volume | Crawl budget or quality signal failure. Apply Section 7. |
| CWV shows zero URLs in field data | Site lacks Chrome volume for CrUX. Switch to lab data via PageSpeed Insights. |
| Property data ends abruptly 16 months ago | UI date range limit. Use BDE BigQuery for longer retention. |
| Multiple sites hitting GSC API quotas | Use a service account dedicated to GSC reporting. |
| BDE fails on schema mismatch | BigQuery requires empty export dataset. Pre existing tables block export. |

### 1.6 Required Tools

- Google Search Console at search.google.com/search-console
- A Google account with Workspace or Gmail
- Domain registrar DNS access for TXT verification (preferred)
- Google Tag Manager and GA4 for alternative verification and integration
- Bash 4+ for shell automation, Python 3.11+ for API work
- A self hosted Debian origin (Bubbles class) for automated monitoring; no third party CDN or proxy services
- For BDE: a Google Cloud project with billing enabled and a BigQuery dataset

### 1.7 Relationship to Neighboring Frameworks

- `framework-initialaudit.md`: initial site audit; GSC verification is the first step.
- `framework-ongoingaudit.md`: monthly and quarterly audit cadence consuming GSC output.
- `framework-attribution.md`: multi touch coordination of GSC click data with GA4.
- `framework-aioverviews.md`: operational reference for the AI Overview tab content in Section 6.
- `framework-internallinking.md`: internal links report in Section 10.
- `framework-linkbuilding.md`: top linking sites.
- `framework-pageexperience.md`: CWV report in Section 8.
- `framework-schema.md`: enhancements in Section 9.
- `framework-technicalseo.md`: indexing diagnostics in Section 7.
- `framework-reporting.md`: client deliverables.

## 2. Client Variables Intake

```yaml
# GSC ANALYSIS CLIENT VARIABLES

# --- Business and Site Identity ---
business_name: ""
primary_domain: ""
www_or_apex: ""
protocol: "https"
international_subdomains: []
language_subdirectories: []

# --- Property Inventory ---
domain_property_exists: false
url_prefix_properties: []
properties_owned: []
properties_full_user: []
properties_restricted: []

# --- Verification ---
domain_verified_via: ""                  # dns_txt, html_file, html_tag, ga4, gtm
verification_redundancy: false
dns_provider: ""

# --- Integration ---
ga4_property_linked: false
ads_account_linked: false
youtube_channel_linked: false
discover_eligibility_confirmed: false

# --- Performance Baseline ---
clicks_28d: 0
impressions_28d: 0
ctr_28d: 0.0
average_position_28d: 0.0
top_10_queries_by_clicks_28d: []
top_10_landing_pages_by_clicks_28d: []

# --- AI Overview Baseline (see framework-aioverviews.md) ---
ai_overview_tab_available: false
ai_overview_impressions_28d: 0
ai_overview_clicks_28d: 0
ai_overview_ctr_28d: 0.0
ai_overview_queries_cited: []

# --- Indexing Baseline (see framework-technicalseo.md) ---
pages_indexed_count: 0
pages_not_indexed_count: 0
sitemap_count: 0
sitemap_urls_submitted_total: 0
sitemap_urls_indexed_total: 0
top_3_non_indexing_reasons: []

# --- Experience Baseline (see framework-pageexperience.md) ---
cwv_mobile_good_pct: 0.0
cwv_desktop_good_pct: 0.0
lcp_field_data_p75_ms: 0
inp_field_data_p75_ms: 0
cls_field_data_p75: 0.0
https_pct: 100.0

# --- Enhancements Baseline (see framework-schema.md) ---
enhancement_types_active: []
enhancement_errors_count: 0
enhancement_valid_count: 0

# --- Links Baseline ---
top_linking_sites_count: 0
top_linking_anchor_terms: []
internal_links_top_pages: []

# --- API and BigQuery Access ---
gsc_api_service_account_email: ""
service_account_property_access: false
bulk_data_export_enabled: false
bigquery_project_id: ""
bigquery_dataset_id: ""
bigquery_dataset_location: ""

# --- Monitoring Infrastructure ---
monitoring_host: ""
monitoring_host_ip: ""                   # 169.155.162.118 for Bubbles
nginx_vhost_path: ""                     # /var/www/sites/[domain]/
cron_schedule: ""
alert_email_recipient: ""
alert_smtp_relay: ""

# --- Reporting ---
delivery_cadence: ""
delivery_format: ""                      # pdf, looker_studio, metabase, grafana
client_dashboard_url: ""
```

GSC analysis cannot begin until domain property is verified, current team has Owner or Full User access, and at least 28 days of data have accumulated. Sites failing those prerequisites route back to verification first.

## 3. The GSC Data Schema 2026

GSC organizes data into six top level sections, each with one or more reports.

### 3.1 Performance

Covers Search results (Web, Image, Video, News combined into Search type), Discover, and Google News. Each is its own tab. Dimensions: Queries, Pages, Countries, Devices, Search appearance, Search type, Dates. Each can be filtered and combined with up to one other dimension in the UI. Metrics: Clicks, Impressions, CTR, Average position. Default view: last three months excluding anonymized queries. Maximum date range: sixteen months.

Search appearance is where AI Overview separation lives. As of Q4 2025, Search appearance has a dedicated AI Overviews row (Section 6).

The UI 1000 row cap applies per dimension combination. CSV and Sheets export preserve the limit. The Search Analytics API allows 25000 rows per request with pagination. BDE BigQuery has no row cap.

### 3.2 Indexing

Pages, Video pages, Sitemaps, Removals. The Pages report categorizes URLs into Indexed and Not indexed buckets with subcategories beneath each (Section 7). Samples up to 1000 example URLs per subcategory; full lists require URL Inspection API or BDE.

The Sitemaps report shows each submitted sitemap with last read date, status, type, discovered URL count. Discovered count does not equal indexed count; the gap is the indexing health signal. Removals supports temporary removal, outdated content removal, SafeSearch filtering. Does not deindex pages permanently.

### 3.3 Experience

Covers Core Web Vitals and HTTPS. CWV uses CrUX field data sampled from real Chrome users and grouped by URL similarity. 2026 thresholds: LCP under 2.5s is Good, INP under 200ms is Good, CLS under 0.1 is Good (Section 8). HTTPS reports percentage of indexed URLs over HTTPS; 2026 expectation 100 percent. The legacy Mobile Usability report was deprecated December 2023; mobile friendly evaluation is now in URL Inspection.

### 3.4 Enhancements

Reports valid, warning, error counts per schema type Google detected. Types in 2026: Breadcrumbs, Logos, Sitelinks searchbox, FAQ, HowTo, Product, Review snippet, Video, Job posting, Event, Recipe, Practice problem, Math solver, Dataset, Course, Movie, Software application, Special announcement. A type appears only after Google detects matching schema.

### 3.5 Links

External links (Top linked pages, Top linking sites, Top linking text) and Internal links (count per URL). Sampled, not exhaustive.

### 3.6 Settings, Crawl Stats, Manual Actions, Security, Associations, BDE

Settings is the catchall. Crawl stats shows aggregate Googlebot requests per day, average response time, total download size, request purpose distribution, response code distribution, file type distribution, Googlebot type distribution. Manual actions reports human applied penalties. Security issues reports hack and malware findings. Associations reports linked GA4, Google Ads, YouTube, Play Console. Users and permissions reports access. Bulk data export configures the BDE integration (Section 12).

### 3.7 Data Freshness and Privacy Aggregation

Performance data lags 24 to 48 hours; position can lag 72 hours. Indexing data refreshes weekly with some categories daily. CWV field data uses a 28 day trailing window refreshed daily. Enhancement data refreshes per crawl. Links data refreshes on the order of weeks.

Privacy aggregation: queries under approximately 10 clicks are anonymized and omitted from the queries table. The API and BDE expose more rows but the same rule applies. Minimum unit is daily aggregated impression and click counts per (query, page, country, device, appearance, type) tuple.

## 4. Account, Property, and Permission Architecture

### 4.1 Property Types

Two types: Domain property and URL prefix property.

A Domain property verifies ownership of an entire registrable domain including every subdomain, every protocol, every path. Verification via DNS TXT only. A Domain property on example.com covers www.example.com, blog.example.com, every protocol variant. Recommended for nearly all sites.

A URL prefix property verifies a specific URL prefix only. https://example.com/ and https://www.example.com/ are separate properties. Supports multiple verification methods. Useful for granular reporting on a subdirectory or where DNS access is not available.

Recommended estate: one Domain property plus URL prefix properties for major subdirectories or subdomains where granular reporting is wanted.

### 4.2 Verification Methods

DNS TXT is the canonical method for Domain properties. The record format is google-site-verification=[token] at the zone apex. Propagation takes five minutes to 48 hours. The record must remain in place.

HTML file verification places a Google supplied file at document root, example /var/www/sites/[domain]/google[token].html. HTML tag verification places `<meta name="google-site-verification" content="[token]">` in the homepage head. Google Analytics verification works if the same account holds Analytics admin. Google Tag Manager verification works if the GTM snippet is in the homepage head and the verifying user has Publish permission.

Recommended posture for critical properties: DNS TXT, GTM, and HTML file active in parallel. Losing any one does not deverify.

### 4.3 User Roles

Three roles: Owner, Full user, Restricted user.

Owner can do everything: verify, add and remove users, configure settings, manage Removals, edit Bulk Data Export, link associations, accept reconsideration requests.

Full user can read every report, submit URLs, submit sitemaps, request indexing, respond to manual actions. Cannot add users, configure settings, edit BDE, or manage associations. Typical agency role.

Restricted user reads most reports view only. Cannot submit URLs, sitemaps, request indexing, or read settings. Suitable for stakeholders.

For an agency engagement: insist on Full user at minimum. For engagements where the agency owns the entire SEO function: ask for Owner.

### 4.4 Delegated Agency Setups

Clean pattern: client retains primary Ownership, agency is added as Full user or secondary Owner, service account email (Section 14) is added as Restricted user for automated reporting. When engagement ends, agency and service account are removed; client never loses access.

Dirty pattern: agency is the only Owner, verification is via the agency's GTM container, client never receives Owner credentials. Defend against this by insisting on client primary Ownership at engagement start.

### 4.5 Linking GSC to GA4, Google Ads, YouTube

GSC and GA4 are linked through GSC Settings Associations, requires Edit access on GA4 and Owner on GSC. Once linked, GA4 surfaces GSC reports under Acquisition Search Console. See `framework-attribution.md`. GSC to Google Ads linking enables the Paid and Organic report, see `framework-ppc-seo-coordination.md`. GSC to YouTube linking enables YouTube Search performance under the Video tab.

## 5. Performance Report Deep Dive

### 5.1 The Four Metrics

Clicks counts users who clicked from a Google Search result through to the site. A single user clicking twice in the same session counts twice.

Impressions counts the appearances of a property URL meeting Google's impression definition. A result must be visible without scrolling to count, with exceptions for carousels and packs. Image results count an impression only when the thumbnail is loaded into the user's viewport.

CTR is clicks divided by impressions for the filtered slice. CTR at the property level is meaningless; CTR at the query level tells you snippet effectiveness.

Average position is the mean of the highest position any URL on the property achieved per impression, weighted by impression count.

### 5.2 Aggregation Rules

Position is averaged at the property level by default. Filter by a single query for that query's position; filter by a single page for the average across all queries that returned that page. Pages tab uses page level aggregation; Queries tab uses query level aggregation. The two views can show different position values for the same data.

Clicks and impressions aggregate by sum. CTR is recomputed at each aggregation level; property level CTR is not the average of query level CTRs.

### 5.3 Sampling and the 1000 Row Cap

The UI displays up to 1000 rows per dimension. Remaining queries contribute to totals at higher aggregation levels but do not appear row by row. CSV export inherits the cap. The Search Analytics API allows up to 25000 rows per request with pagination, with daily quota limits per project. BDE BigQuery has no row cap.

### 5.4 The Comparison Interface

Compare supports date range, query, page, country, device, and search appearance comparison. Date range is the most used: select last 28 days, click Compare, choose Previous period or Same period last year.

Output includes delta columns for clicks, impressions, CTR, position. Sorting by delta clicks descending surfaces growing queries; ascending surfaces declining queries. Sorting by delta impressions ascending with a position under 10 filter surfaces queries where ranking held but impressions dropped, often a signal of SERP feature consumption (AI Overview, Section 6).

Query comparison and page comparison overlay two queries or two pages in a single chart. Useful for cannibalization (Section 13.3) and comparing old URL with new URL after migration.

### 5.5 Filter Regex Syntax

GSC supports regex filters using Google's RE2 syntax, a subset of standard regex. Common patterns:

```text
^how to                          # queries starting with "how to"
\b(price|cost|pricing)\b         # queries containing price words
^(?!.*brand_name)                # queries NOT containing brand_name
\bnear me\b                      # near me queries
/blog/                           # pages in the blog directory
/(blog|articles|insights)/       # pages in any of three directories
\?utm                            # pages with UTM parameters
```

Enable by clicking the filter, switching from "URLs containing" or "Queries containing" to "Custom (regex)". Match type "Matches regex" or "Does not match regex". Layer regex with dimension filters for complex analysis.

### 5.6 The Search Appearance Dimension

Search appearance separates impressions and clicks by SERP feature. 2026 rows include AI Overviews (Q4 2025, Section 6), Sitelinks searchbox, FAQ, HowTo, Recipe, Product, Review snippet, Video, Image, Top stories, News, Discover, Sitelinks, Practice problems, Math solvers, Job posting, Event, Course info, Movie, Software application, Web Light.

### 5.7 Date Range Behavior

Default range: last three months. Maximum: sixteen months. Minimum: one day. Filters are inclusive in the user's timezone. Default timezone is Pacific. The sixteen month limit is a hard constraint in UI and API. For longer term analysis, BDE BigQuery is the only option.

### 5.8 Country and Device Dimensions

Country filters by searcher country. International sites filter by country to isolate per market performance. Device filters by mobile, desktop, tablet. CTR and position differ significantly between mobile and desktop. Mobile CTR is often lower because mobile SERPs contain more SERP features above the organic results.

## 6. AI Overviews Tab Specifics

### 6.1 Rollout History

AI Overviews appeared in US Google Search mid 2024 (initially "Search Generative Experience"). AIO impressions were initially bundled into standard web search in GSC Performance. In Q4 2025, Google rolled out a dedicated AI Overviews row in the Search appearance dimension. By Q1 2026 it is generally available across properties with sufficient AIO citation volume.

### 6.2 What Counts as an AI Overview Impression

An AI Overview impression counts when the property URL is cited as a source in an AI Overview on a SERP, regardless of whether the user clicked. AI Overview impressions are additional to organic web impressions on the same SERP. A property cited in the AI Overview and ranked position 4 organic on the same SERP earns one AI Overview impression and one Web impression. Total impressions across appearance dimensions sum at the property level.

### 6.3 What Counts as an AI Overview Click

An AI Overview click counts when a user clicks the citation link in the AI Overview that points to this property. Clicks on the AI Overview text itself, on "View more" expansion, or on "Ask follow up" do not count.

CTR is typically lower than position 1 organic CTR because users often consume the AI Overview text without clicking citations. Surfer SEO December 2025 sampled 173,902 URLs and found AI Overview citation CTR averages 2.1 to 3.4 percent, versus position 1 organic CTR of 22 to 28 percent. The conversion rate of AI Overview citation clicks is approximately 23 times the standard organic visitor rate, so per click value is higher.

### 6.4 Isolating AI Overview Impact from Organic

The Search appearance dimension with AI Overviews filter isolates AIO impressions and clicks. To compute AIO impact net of organic:

1. Filter Search appearance to AI Overviews. Note impressions, clicks, CTR.
2. Filter Search appearance to NOT AI Overviews. Note impressions, clicks, CTR.
3. Compare 28 day periods before and after a content change.
4. AIO delta positive, non AIO delta negative = visibility shifted from classic organic into AIO citation. Net change is sum.
5. Both deltas positive = compounded win.
6. AIO delta positive, non AIO delta flat = AIO citation won without disturbing organic. Best case.
Apply monthly to track AIO share of total visibility.

### 6.5 AI Overview Position 0 Versus Organic Position 1

There is no AI Overview position metric. The AI Overview appears at position 0 on SERPs where it shows, but GSC does not report position for the AI Overviews row. Per impression, position 1 is more valuable for clicks. Per click, AI Overview is more valuable for conversion. On queries where AI Overview compresses organic CTR (Surfer December 2025 found up to 61 percent compression on AIO queries), the AI Overview citation may be the only material visibility available. Targeting AI Overview citation is a distinct and often higher leverage activity than chasing position 1 organic. See `framework-aioverviews.md`.

### 6.6 AI Overview Tab Data Limitations

The AI Overviews row is bundled at the Search appearance level. There is no per query breakdown in the UI. To get query level AI Overview presence, filter Search appearance to AI Overviews and combine with Queries tab.

For competitive AI Overview tracking (queries where competitors are cited but this site is not), GSC does not surface the data. Third party tools like Profound, Otterly, Athena HQ, BrightEdge AI Catalyst, and Semrush AI Toolkit sample the AI Overview surface and report competitive citation state.

### 6.7 Performance Discover and Performance News

Discover reports impressions and clicks on the Google Discover feed (mobile). Eligibility automatic for sites Google deems suitable. Discover impressions track separately from Web.

Google News reports impressions and clicks on the Google News surface and Top Stories carousel. Eligibility requires inclusion in Google News via Publisher Center, see `framework-newsseo.md`.

## 7. Indexing Report Diagnostics

The Indexing Pages report categorizes URLs with reason codes. This section covers each 2026 non indexing reason and remediation.

### 7.1 Indexed Subcategories

**Submitted and indexed.** Healthy state.

**Indexed, not submitted in sitemap.** URLs Google discovered and indexed without sitemap submission. If they should be in sitemap, add. If they should not be indexed, apply noindex or canonical tag.

**Indexed though blocked by robots.txt.** Warning. Google indexed based on inbound links but cannot crawl content. Allow crawl in robots.txt, then either let Google fully crawl or add noindex. Robots.txt block does not prevent indexing; only noindex does.

### 7.2 Not Indexed Subcategories with Remediation Flow

**Crawled, currently not indexed.** Google crawled and decided not to index. Most common cause: quality signal failure (thin content, duplicate of higher value page, topical mismatch). Remediation: audit page quality against `framework-hcs.md`. If thin or duplicate, consolidate via 301 or noindex. If unique and substantive, improve Information Gain per `framework-infogain.md`, add internal links, request indexing.

**Discovered, currently not indexed.** Google knows about the URL but has not crawled it. Most common cause: crawl budget exhaustion. Remediation: audit crawl stats. If Googlebot crawl rate is low relative to site size, investigate server response time, robots.txt restrictions, internal linking density. Improve internal linking from the homepage and high authority pages. Submit to URL Inspection. For systemic issues see `framework-technicalseo.md`.

**Duplicate without user selected canonical.** Google identified the URL as a duplicate and selected its own canonical. Remediation: add an explicit `<link rel="canonical">` tag pointing to the intended canonical. Validate via URL Inspection.

**Duplicate, Google chose different canonical than user.** The property URL declares a canonical but Google chose a different URL. Remediation: investigate whether Google's choice is correct (often is). If yes, accept consolidation. If user declared canonical is correct, strengthen its signals via internal linking, external links pointing to the canonical not the duplicates, and resolve near identical content overlap.

**Soft 404.** Google considers the page a 404 despite a 200 response. Triggered by thin content, missing primary content, or error like content. Common on JavaScript heavy pages. Remediation: verify substrate via `curl -A "Googlebot" -s [url] | head -100`. If primary content is absent in first byte, fix via server rendering per `framework-contentfirst.md`. If thin, expand substantively. If the page should not exist, return 410.

**Alternate page with proper canonical tag.** URL points to another URL as canonical and Google is honoring that. Healthy state.

**Page with redirect.** URL returns 301 or 302. Healthy if redirects are intentional. Audit if redirect chain exceeds three hops or targets a 404.

**Blocked by robots.txt.** URL is disallowed in robots.txt. If it should be indexed, allow in robots.txt.

**Excluded by 'noindex' tag.** Healthy state if intentional. Audit if URLs intended to index are accidentally serving noindex (CMS or staging leak).

**Server error (5xx).** Repeated 5xx errors result in deindexing. Remediation: investigate server logs, fix the cause, monitor crawl error rate via Settings Crawl stats, request recrawl.

**Not found (404).** If intentional, URL drops from the index over weeks. Accelerate with 410 Gone. If unintentional, restore the page.

**Page indexed without content.** Rare. Indicates content was empty after JavaScript execution. Same remediation as Soft 404.

**URL blocked due to 403 or 401.** Authentication or authorization blocks. Remediation depends on whether the block is intentional.

### 7.3 Sitemap Submission and Monitoring

Submit a sitemap or sitemap index for every property. Formats: XML sitemap, XML sitemap index (required when URLs exceed 50000 or 50MB), RSS or Atom. XML is canonical. Segment by content type: /sitemap.xml index referencing /sitemap-pages.xml, /sitemap-posts.xml, /sitemap-products.xml. Segmentation makes indexing diagnostics easier.

Sitemap submission alone does not guarantee indexing. The gap between URLs submitted and indexed is the diagnostic signal. 5000 submitted and 4800 indexed is healthy. 5000 submitted and 2000 indexed indicates systemic failure traceable to one of the Not indexed subcategories above.

### 7.4 Removals Workflow

Use Removals for urgent removal pending proper deindexing, not as the primary method. Temporary URL removal hides for approximately six months. Outdated content removal updates cached snippets. Permanent removal: apply noindex, return 404 or (preferred) 410, and wait for natural recrawl.

## 8. Core Web Vitals and Page Experience Report

### 8.1 The Three Metrics and 2026 Thresholds

Largest Contentful Paint (LCP) measures render time of the largest visible content element above the fold. Under 2.5s is Good, 2.5 to 4.0 is Needs improvement, over 4.0 is Poor.

Interaction to Next Paint (INP) measures time from user interaction to the next paint. INP replaced FID in March 2024. Under 200ms is Good, 200 to 500 is Needs improvement, over 500 is Poor. INP measures responsiveness throughout the page lifecycle, not just the first interaction.

Cumulative Layout Shift (CLS) measures visual stability by summing layout shifts during the page's lifecycle. Under 0.1 is Good, 0.1 to 0.25 is Needs improvement, over 0.25 is Poor.

A URL is rated Good only if all three metrics are Good. The strictest metric determines the rating.

### 8.2 Field Data versus Lab Data

GSC CWV uses field data exclusively, real Chrome user data sampled via CrUX from opt in users, aggregated over a trailing 28 day window and refreshed daily.

Sites with low Chrome user volume may not surface in the report. For these sites, use lab data via PageSpeed Insights or Lighthouse on key page templates. A page scoring Poor in field data despite Good in Lighthouse is suffering from real world conditions lab tests do not replicate. Diagnostic pattern: identify Poor URL groups in GSC, then run PageSpeed Insights on representative URLs for lab data and the audit report.

### 8.3 URL Group Sampling

GSC reports CWV at URL group level. Google groups URLs with similar templates and traffic patterns. Group performance reflects the median across URLs. When a fix is deployed, expect a 28 day lag before it fully surfaces because the trailing window must roll over.

### 8.4 Mobile and Desktop Separation

The CWV report separates mobile and desktop. URL groups can have different ratings on mobile versus desktop. INP varies dramatically between the two because mobile devices have less compute.

### 8.5 Validation Flow

After deploying a fix, mark the URL group Validate fix. Google samples over a validation window (typically 28 days). See `framework-pageexperience.md`.

### 8.6 HTTPS Status

The HTTPS report shows the percentage of indexed URLs served over HTTPS. 2026 expectation is 100 percent. Any HTTP only or mixed content URL is remediated.

## 9. Enhancement Reports

Each Enhancement type Google detects on the property gets a dedicated report. Reports show Valid, Warning, and Error counts and example URLs per status.

### 9.1 Reports Active in 2026

**Sitelinks searchbox.** WebSite schema with potentialAction SearchAction. Active and rich result eligible.

**FAQ.** FAQPage schema. Rich result eligibility limited since August 2023 to authoritative sources. Continue to implement because AI Overview synthesis reads it.

**HowTo.** HowTo schema. Mobile only rich result since 2024. Eligibility further narrowed in 2025.

**Product.** Product schema with price, availability, review, rating. Two entries: Product snippets and Merchant listings. Critical for e commerce, see `framework-ecommerceseo.md`.

**Review snippet.** Review or AggregateRating. Strict authenticity policies; fabricated reviews trigger manual action.

**Video.** VideoObject schema. Drives Video carousel eligibility.

**Job posting.** JobPosting schema. Surfaces in Google Jobs.

**Event.** Event schema. Surfaces in Event rich result.

**Recipe.** Recipe schema. Highly competitive in food vertical.

**Breadcrumbs.** BreadcrumbList. Displays as breadcrumb path in SERP snippet.

**Logo.** Organization with logo. Surfaces in Knowledge Panel.

**Sitelinks.** Site link extensions under brand SERPs; not directly schema driven.

**Other active types.** Practice problems, Math solvers, Dataset, Course, Movie, Software application, Image metadata, Special announcement.

### 9.2 Validation Flow

When errors show, click the error category to see affected URLs. Fix the schema on representative URLs. Use Rich Results Test to validate. Click Validate fix. Google recrawls within days to weeks. Warnings are non blocking. See `framework-schema.md`.

## 10. Links Report Investigation

### 10.1 Top Linking Sites

External domains linking to the property, ranked by linked page count. Sampled, not exhaustive. Useful for baseline backlink understanding and cross referencing with Ahrefs or Semrush.

Pairing with `framework-linkbuilding.md`: identify natural backlinks worth deepening into partnerships, scraper sites or spam linkers to disavow, and validate link building campaigns are surfacing in Google's index.

### 10.2 Top Linking Text

Anchor text patterns across the inbound link profile. Useful for detecting overly aggressive exact match commercial keyword concentration that risks algorithmic penalty. Healthy profile: branded, naked URL, generic, partial match, and exact match anchors.

### 10.3 Top Linked Pages

URLs receiving the most inbound external links. Useful for identifying which content earns natural links and ensuring internal linking surfaces top linked pages prominently.

### 10.4 Internal Links

Count of internal inbound links per URL. Very low count is orphan candidate. Very high count is hub page.

Pairing with `framework-internallinking.md`: identify orphan pages needing strengthening, validate hub and spoke architecture, detect inadvertent over linking of low value pages (pagination, tag pages, internal search results).

GSC internal link count is sampled and may differ from full crawl tools (Screaming Frog, Sitebulb). 10 to 20 percent discrepancy is normal; over 50 percent indicates sampling artifact or that Google has not crawled the link source pages.

## 11. URL Inspection Tool Methodology

### 11.1 The Two Views: Indexed and Live

URL Inspection shows two views: indexed view (the version Google last crawled and indexed) and live view (the version that exists right now). The diff surfaces drift between published content and Google's indexed copy.

Indexed view shows: last crawl date, indexing status, crawled as, crawl allowed, page fetch, indexing allowed, user declared canonical, Google selected canonical, schema parsed, screenshot of indexed render.

Live view re fetches and shows: HTTP response, rendered HTML, rendered DOM after JavaScript execution, JavaScript console messages, schema parsed from live fetch, screenshot of live render.

If indexed view shows stale content from three months ago, live view reveals whether the current content would index. If live view shows missing schema, the schema is failing parsing at the live render stage.

### 11.2 The Live Test Methodology

To validate that a page change is bot visible:

```bash
# Before push, capture baseline.
curl -A "Googlebot" -s https://example.com/path/ > /tmp/baseline.html
# Push the change.
# After push, capture and diff.
curl -A "Googlebot" -s https://example.com/path/ > /tmp/after.html
diff /tmp/baseline.html /tmp/after.html
# In GSC URL Inspection, run Live test. Verify rendered HTML and DOM include the change. Click Request Indexing.
```

Catches the common failure where a CMS renders content client side, the curl response lacks the change, and the page change has no SEO effect despite working for human users.

### 11.3 Request Indexing Daily Budget

Request Indexing has a daily quota per property, approximately 10 to 12 URLs per day. The quota is not published precisely. Use for genuinely time sensitive recrawl needs: new pages where Google has not yet discovered them, recently updated pages where new content matters now, recently fixed pages where the fix should be reflected quickly. Do not waste budget on routine content updates.

### 11.4 Comparing Rendered HTML with First Byte HTML

The Live test View tested page panel shows rendered HTML (post JavaScript DOM) versus first byte HTML (initial HTTP response). The diff reveals JavaScript dependencies. A page where first byte HTML lacks primary content but rendered HTML has it is JavaScript dependent. AI Overview parsing (reading mode bots, see `framework-aioverviews.md` Section 4.5) operates on first byte HTML. A page needing JavaScript to render primary content fails AI Overview parsing regardless of how it renders for users.

### 11.5 URL Inspection API for Automation

URL Inspection API supports programmatic inspection up to 2000 URLs per day per property. Use for automated indexing audits, monitoring critical URLs, batch diagnostics.

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

## 12. Bulk Data Export to BigQuery

### 12.1 The Mechanism

Bulk Data Export (BDE) exports daily Search Analytics data to a BigQuery dataset. It removes the 1000 row UI cap and 25000 row API page limit. BDE exposes full daily impression and click data at the URL level.

Setup requires Owner permission and a Google Cloud project with billing enabled. Steps: create the project, enable BigQuery API, create an empty BigQuery dataset, grant the BDE service account (provided by GSC) BigQuery Data Editor role on the dataset, enter the GCP project ID and dataset ID in GSC Settings Bulk data export, confirm export.

First export occurs within 48 hours. Subsequent exports run daily, approximately 24 hours after the data date.

### 12.2 The Schema

BDE creates three tables:

searchdata_site_impression. One row per (date, query, country, search_type, device) tuple with site level metrics. Columns: data_date, site_url, query, is_anonymized_query, country, search_type, device, impressions, clicks, sum_position.

searchdata_url_impression. One row per (date, url, query, country, search_type, device, search_appearance) tuple with URL level metrics plus boolean flags for SERP feature appearance. Includes all searchdata_site_impression columns plus url, is_anonymized_discover, and boolean flags including is_amp_top_stories, is_amp_blue_link, is_job_listing, is_job_details, is_tpf_qa, is_tpf_faq, is_tpf_howto, is_weblite, is_action, is_events_listing, is_events_details, is_ai_overview, is_organic_shopping, is_review_snippet, is_special_announcement, is_recipe_feature, is_recipe_rich_snippet, is_subscribed_content, is_page_experience, is_practice_problems, is_math_solvers, is_translated_result, is_edu_q_and_a, is_product_snippets, is_merchant_listings, is_learning_videos.

The is_ai_overview column allows BDE based AI Overview attribution analysis. Filter is_ai_overview = TRUE to see which URL and query combinations earned AIO citation.

ExportLog. One row per daily export with status, table name, row count, error if applicable.

### 12.3 SQL Patterns

Average position from sum_position: SUM(sum_position) / SUM(impressions) + 1.0. The +1.0 is required because sum_position uses 0 indexing internally while GSC reports 1 indexed positions.

Top queries by AI Overview citation impressions, last 28 days:

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

AI Overview impact analysis comparing 28 day periods:

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

### 12.4 Cost Considerations

BigQuery pricing: storage roughly 0.02 USD per GB per month, query roughly 5 USD per TB scanned. A typical mid sized property exports 100MB to 1GB per month. BigQuery cost for most agency engagements is single digit dollars per month.

Sandbox tier (free) allows 10GB storage and 1TB query per month but disallows BigQuery ML, scheduled queries, and has 60 day table retention. Production retention requires paid tier. The retention argument for paid BDE: GSC UI caps at 16 months. BDE has no inherent retention cap; for long term historical analysis, BDE is the only option.

### 12.5 BDE Setup Validation

```bash
# Check that tables were created
bq ls your_project:your_dataset

# Check most recent export date
bq query --use_legacy_sql=false 'SELECT MAX(data_date) FROM `dataset.searchdata_url_impression`'
```

If tables are not created within 48 hours, check ExportLog for error messages.

## 13. Common GSC Analysis Workflows

Table reference shortened to dataset.searchdata_url_impression for brevity; substitute your fully qualified project and dataset path.

### 13.1 The Decay Detection Query

Surfaces pages losing organic clicks at significant rate. Run weekly.

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

URLs with at least 50 clicks in the prior window that lost 25 percent or more. Investigate for ranking drop, AI Overview consumption, or seasonal shift. UI version: Performance, Compare Last 28 days vs Previous period, Pages tab, sort by Clicks Difference ascending.

### 13.2 The Rising Star Query

Inverse of decay; surfaces pages gaining clicks rapidly.

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

Rising stars are amplification candidates: add internal links from related high authority pages, expand content with Information Gain, surface in newsletter or social distribution.

### 13.3 The Cannibalization Query

Surfaces queries where two or more URLs compete for the same query.

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

Remediation: consolidate the strongest performer with internal redirects from weaker URLs, or differentiate intent so each URL targets a sub topic.

### 13.4 The CTR Opportunity Query

Surfaces pages with high impressions but low CTR. The snippet (title, meta description, schema) is the leverage point.

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

Remediation: rewrite title tag with stronger value proposition, rewrite meta description to address query intent more directly, add Review or AggregateRating schema, add Breadcrumbs schema for SERP path clarity.

### 13.5 The Page Level Position Drop Alert

Surfaces URLs that lost significant average position week over week. Run daily for high traffic properties.

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

URLs ranking in top 10 prior week that lost at least 3 positions current week. Investigate for algorithmic update impact, server issue, or content change.

### 13.6 The Anonymous Query Share

Privacy aggregation hides queries with low click volume. Estimating the anonymous share is useful for understanding total reach:

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

The anonymous share is typically 20 to 50 percent of total impressions for mature properties.

### 13.7 The Audit Rubric

| ID | Criterion | Pass/Fail |
| --- | --- | --- |
| GSC1 | Domain property verified via DNS TXT |  |
| GSC2 | Verification redundancy active (2+ methods) |  |
| GSC3 | Sitemap submitted and processed successfully |  |
| GSC4 | GSC linked to GA4 via Settings Associations |  |
| GSC5 | Service account added for automation |  |
| GSC6 | Performance reviewed weekly |  |
| GSC7 | AI Overviews row used monthly |  |
| GSC8 | Not indexed subcategories audited monthly |  |
| GSC9 | Manual actions and Security issues monitored |  |
| GSC10 | CWV addressed per `framework-pageexperience.md` |  |
| GSC11 | Enhancement errors remediated per `framework-schema.md` |  |
| GSC12 | URL Inspection used for live vs indexed spot checks |  |
| GSC13 | BDE to BigQuery configured |  |
| GSC14 | Decay detection query running weekly |  |
| GSC15 | Cannibalization query running monthly |  |
| GSC16 | CTR opportunity query running monthly |  |
| GSC17 | Automated alerts configured via Section 14 |  |
| GSC18 | Dashboard via self hosted Metabase or Grafana |  |
Score 18. World class: 16 or higher with zero critical fails on GSC1, GSC3, GSC4, GSC10, GSC13.

## 14. Automated GSC Monitoring on Bubbles

Self hosted monitoring on a Debian origin (Bubbles class at 169.155.162.118) provides daily GSC pulls, decay alerts via Gmail SMTP, and dashboard rendering via self hosted Metabase or Grafana. No third party CDN, edge proxy, or hosted analytics service.

### 14.1 Architecture

Single Debian host. Public access on ports 80 and 443 via nginx. Components: nginx serving Metabase or Grafana under a vhost with htpasswd; a systemd timer running a Python script daily pulling GSC via Search Analytics API; SQLite storing daily aggregates plus optional BigQuery for raw BDE feed; Gmail SMTP for alerts. Pattern mirrors existing TDG monitoring deployments.

### 14.2 Service Account Setup

```bash
gcloud services enable searchconsole.googleapis.com --project=PROJECT
gcloud iam service-accounts create gsc-monitor --project=PROJECT
gcloud iam service-accounts keys create ~/secrets/gsc-monitor.json \
--iam-account=gsc-monitor@PROJECT.iam.gserviceaccount.com
```

Add the service account email as Full user on each GSC property.

### 14.3 Daily Pull Script

Authenticate via service account, pull last three days of Performance data per property (filling gaps), upsert into SQLite daily_aggregates, run the decay query, send Gmail alert on decay candidates.

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

Save as `/usr/local/bin/gsc-monitor`; add execute permission with `chmod +x`.

### 14.4 Systemd Timer

`/etc/systemd/system/gsc-monitor.service`:

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

`/etc/systemd/system/gsc-monitor.timer`:

```ini
[Unit]
Description=Run GSC monitor daily

[Timer]
OnCalendar=*-*-* 07:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

Enable the timer:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now gsc-monitor.timer
```

### 14.5 Gmail SMTP Setup

Generate a Gmail app password via Google Account, Security, 2 Step Verification, App passwords. Store in /etc/gsc-monitor/smtp-password mode 0600 owned by gsc-monitor. Script authenticates against smtp.gmail.com:465. For higher volume, substitute Postfix relay or Mailgun.

### 14.6 Metabase or Grafana for Dashboards

Metabase via Docker:

```bash
sudo docker run -d --name metabase -p 127.0.0.1:3000:3000 \
-v /var/lib/gsc-monitor/data.db:/data/data.db \
-v metabase-data:/metabase-data \
-e MB_DB_FILE=/metabase-data/metabase.db \
metabase/metabase:latest
```

Add SQLite connection in Metabase UI pointing to /data/data.db.

Nginx vhost at /etc/nginx/sites-available/gsc.example.com:

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

Create the authentication user and configure the certificate:

```bash
sudo htpasswd -c /etc/nginx/htpasswd-gsc operator
sudo certbot --nginx -d gsc.example.com
```

Grafana is the alternative for stronger time series visualization; install similarly via Docker or apt.

### 14.7 Hardening

- /etc/gsc-monitor/ and /var/lib/gsc-monitor/ owned by gsc-monitor, mode 0750 directories, 0640 files
- Service account key read only by gsc-monitor
- SMTP password file mode 0600
- nginx vhost requires htpasswd
- TLS via Let's Encrypt with certbot.timer auto renewal
- Inbound: ports 22, 80, 443 only. SSH key auth only

### 14.8 Operational Procedures

**Daily (first 30 days):** journalctl -u gsc-monitor.service for execution and errors. Confirm new rows in daily_aggregates.

**Weekly (steady state):** scan alert emails. Review Metabase dashboards. Investigate properties showing unusual patterns.

**Monthly:** rotate service account key. Prune old SQLite data if growth is significant. Patch Debian. Restart Metabase if memory grew.

**Quarterly:** review per property data coverage. Validate PROPS list and service account access. Audit nginx access log on the dashboard vhost.

## Document Information

- **Version:** 2.0
- **Created:** 2026-05-14
- **Maintained by:** ThatDeveloperGuy

GSC is the only first party Google search data source. Every report exposes a facet of how Google sees the site: queries, clicks, impressions, indexing state, CWV, schema validity, links, AIO citation. A practitioner who treats GSC as a dashboard misses the diagnostic surface entirely. A practitioner who reads it as the canonical instrument catches decay in week one, surfaces rising stars before they peak, and proves AIO impact with the precision the data finally supports.

Apply alongside `framework-initialaudit.md` and `framework-ongoingaudit.md` for audit cadence, `framework-attribution.md` for GA4 coordination, and `framework-aioverviews.md` for the AIO playbook.

## Companions

- `framework-initialaudit.md`, `framework-ongoingaudit.md`, audit cadence consuming GSC output
- `framework-attribution.md`, multi touch coordination with GA4
- `framework-aioverviews.md`, AI Overview citation playbook
- `framework-internallinking.md`, internal links report use
- `framework-linkbuilding.md`, top linking sites report use
- `framework-pageexperience.md`, CWV playbook
- `framework-schema.md`, enhancements playbook
- `framework-technicalseo.md`, indexing diagnostics playbook
- `framework-reporting.md`, client deliverables
- `framework-ga4.md`, behavior measurement downstream of GSC
- `framework-contentfirst.md`, substrate doctrine driving indexability
- `framework-hcs.md`, Helpful Content System for Crawled not indexed
- `framework-infogain.md`, Information Gain criteria
- `framework-newsseo.md`, Google News surface
- `framework-ecommerceseo.md`, Product enhancement specifics
- `framework-ppc-seo-coordination.md`, Google Ads link use
- `framework-aicitations.md`, broader AI citation across other engines

## Frequently asked questions

### What is the difference between a Domain property and a URL prefix property in GSC?

A Domain property verifies an entire registrable domain including every subdomain, protocol, and path, using DNS TXT verification only, and is recommended for nearly all sites. A URL prefix property verifies only a specific prefix, so https://example.com/ and https://www.example.com/ are separate properties; it supports multiple verification methods and is useful for granular subdirectory reporting or where DNS access is unavailable.

### How does GSC count AI Overview impressions and clicks?

An AI Overview impression counts when your URL is cited as a source in an AIO on a SERP, regardless of clicks, and is additional to organic web impressions on the same SERP. An AIO click counts only when a user clicks the citation link to your property. Clicks on the AIO text, View more, or Ask follow-up do not count. The Q4 2025 AI Overviews row in Search appearance separates this data.

### What does Discovered, currently not indexed mean and how do I fix it?

It means Google knows about the URL but has not crawled it, most commonly due to crawl budget exhaustion. Remediation: audit crawl stats. If Googlebot crawl rate is low relative to site size, investigate server response time, robots.txt restrictions, and internal linking density. Improve internal linking from the homepage and high-authority pages, then submit the URL via URL Inspection.

### How do I find pages losing organic clicks (decay) in GSC?

Use the decay detection query weekly: it compares the last 28 days of clicks per URL against the prior 28-day window, flagging URLs with at least 50 clicks in the prior window that lost 25 percent or more. Investigate for ranking drop, AI Overview consumption, or seasonal shift. UI version: Performance, Compare Last 28 days vs Previous period, Pages tab, sort by Clicks Difference ascending.

### Why use Bulk Data Export to BigQuery instead of the GSC UI?

The UI caps at 1000 rows per dimension combination and 16 months of retention; the Search Analytics API allows 25000 rows per request. BDE, generally available since February 2024, has no row cap and exposes full daily URL-level impression data the UI never showed, including the is_ai_overview column for AIO attribution. It requires Owner permission and a billing-enabled Google Cloud project with an empty BigQuery dataset.

## Related frameworks

- Google AI Overviews & AI Mode: optimization patterns and citation engineering
- Technical SEO: crawling, indexing, canonicalization, JS rendering
- Page Experience: LCP, INP, CLS, mobile usability, HTTPS
- Ongoing Audit: monthly + quarterly cadence, regression detection
- Attribution: multi-touch, last-click, marketing attribution models
- Schema.org & Structured Data: JSON-LD, @id graphs, sameAs networks
