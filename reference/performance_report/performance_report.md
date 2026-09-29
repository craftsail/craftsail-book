# Performance report (Search results): Overview and basic setup
> https://support.google.com/webmasters/answer/7576553

The Performance report shows important metrics about how your site performs in Google Search results.

You can use this report to:

- See how your search traffic changes over time, where it's coming from, and what search queries are most likely to show your site.
- Learn which queries are bringing traffic to your site, and use this to improve your SEO efforts.
- See which pages have the highest (and lowest) click-through rate from Google search results.

> 💡 If you're new to website analysis, start here for an introduction on using performance reports.

👉 **Open the Performance report**

---

## Configuring the report

The default view of the report shows click and impression data for your site in Google Search results for the past three months. You can modify this view by choosing different **metrics**, **dimensions**, and **date ranges**.

### Choosing metrics

Metrics are the numbers you see in the chart and table. You can choose which metrics to display by toggling the appropriate heading above the chart.

The following metrics are available:

| Metric | Description |
| --- | --- |
| **Clicks** | The number of times a user clicked your site from Google Search results. |
| **Impressions** | How many times your site appeared in Search results. |
| **CTR (click-through rate)** | The click count divided by the impression count. |
| **Average position** | The average position of the topmost result from your site. |

About **Average position**:

- **In the chart**: The value is the average position of the topmost result from your entire site.
- **In the table**: The value is the average position in Search results for the specific URL or grouping dimension shown in the table row.

> Learn how clicks and impressions are counted.

### Choosing dimensions

The dimension defines how the data in the table is grouped. For example, the "queries" dimension groups your data by the search query users typed. You can choose a dimension by selecting the appropriate tab above the table:

- Queries
- Pages
- Countries
- Devices
- Search appearance
- Dates

> ⚠️ Even if a query appears in your list, you might not see your site in results if you run the same query in Google Search. This is because Search results are specific to the time, place, device, and recent history of the person searching.

See "Dimensions and data groupings" for more information.

### Date range and search type

You can change the date range or search type using the filters at the top of the report:

- **Date range**: To change the time period, click the date filter. When you choose the 24-hour view, the graph's data points represent hours and include preliminary data.
- **Search type**: Use the search type filter to see data for web (text-based or multimodal), image, video, or news searches.
  - **Web: text-based**: Includes traditional search traffic originating from text queries entered into the standard Google search bar.
  - **Web: multimodal**: Includes web search results where an image was used as part of the search.

See "Advanced filtering and comparison" for more information.

### Time granularity

You can choose time granularity to define how data points are aggregated in the chart and table. Select **Hourly**, **Daily**, **Weekly**, or **Monthly** from the dropdown menu above the chart to spot trends.

| Granularity | Use case | Availability |
| --- | --- | --- |
| **Hourly** | Analyze the most recent data | Only in the 24-hour view |
| **Daily** | Detect issues or traffic spikes | Not available in the 24-hour view |
| **Weekly / Monthly** | Analyze longer-term trends by smoothing out daily fluctuations (like those caused by weekends or holidays), helping you focus on overall growth or decline in site traffic | Not available in the 24-hour view |

---

## Reading the chart and table

Report data is presented in two sections: the **chart** and the **table**.

### Reading the chart

The chart displays total clicks, total impressions, average CTR, and average position for your property over the selected time period.

- **Aggregation**: Data on the chart is aggregated by **property**. For example, if two results from the same site appear for one query, they count as a single impression in the chart total.
- **Preliminary data**: The newest data can be preliminary, meaning it's still being collected and might change in the next few hours. Preliminary data is indicated by a **dotted line** on the graph.

### Reading the table

The table shows data grouped by the dimension you selected (for example, by query, page, or country).

- **Aggregation**: Table data aggregation changes depending on the dimension you select:
  - Data grouped by **Queries, Countries, Devices, or Dates** is aggregated by **property**.
  - Data grouped by **Pages or Search appearance** is aggregated by **page**.
- **Discrepancies**: The chart totals can sometimes differ from the table totals. This is usually due to differences in aggregation (property vs. page). See "Troubleshooting and data discrepancies" to learn more.
- **Missing table**: In certain cases where the table does not add any additional information to the chart (such as when comparing two groups with simple, exclusive data), the table might be omitted from the report.

---

## Sharing and exporting data

### Sharing the report

You can share access to the report by clicking the **Share** button. This link grants access only to the current view of the report. It doesn't grant access to other pages for your resource or enable the shared user to perform any actions on your property or account.

### Exporting report data

Many reports provide an export button to download both the chart and table data. Values shown as either `~` or `-` in the report (not available/not a number) will be zeros in the downloaded data.
