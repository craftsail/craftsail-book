# Striking Distance Keywords: Which Page 2 Wins Are Real

- **Author:** The Reachavo Team
- **Published:** August 12, 2026
- **Reading time:** 7 minutes
> https://reachavo.com/blog/striking-distance-keywords

You pulled the page-two list. Twelve queries sitting between positions 11 and 20, every one of them on a page that already exists, every one of them already earning impressions. You rewrote three of them over a weekend. Six weeks later, nothing has moved.

That is the normal outcome, and “SEO takes time” is only sometimes the explanation. Striking distance keywords really are the cheapest win available to most sites. But more often than anybody admits, the list was fine and the reading of it was wrong — starting with what the number 11 in that export actually is.

So: what an average position really describes, how to price a win using your own click data instead of somebody else’s chart, and the two cases where the right move is to leave the page alone.

## This post in one image

**Which page-two wins are actually real — infographic**
![image](../../attachments/pasted-mumg3nuk-1.png)
## What position 11 actually means
Search Console does not tell you where you rank. It tells you an average, and Google is direct about it in the performance report documentation: the metric:

> “averages the position value for all impressions (because the position of the link will be different each time it is seen)”.

Which means a query showing 11.4 could be any of these:

- Position 4 on mobile and 30 on desktop.
- Position 8 for the first six weeks of your range and 15 since.
- Position 3 in one country and 40 in another.
- Genuinely 11 or 12, everywhere, all range.

Only the last one is the thing everybody pictures when they read “striking distance”. Google’s own summary of the metric is worth keeping in mind while you scan the list:

> “The position value is a complex metric that can be misleading if you don’t understand the subtleties.”

The practical effect is that the band is a shortlist of things to open, not a leaderboard. A query averaging 11.4 out of a 4-and-30 split is not a page that needs rewriting — it’s a page that already wins on one device and loses on another, which is a different job entirely.

## Finding your striking distance keywords
Open the Performance report, set the date range, and tick **Average position** above the chart before you export. It’s off by default, and it’s the column the whole exercise depends on.

Use three months or more. A 28-day export on a small site usually has too few impressions on page two for anything to separate itself from noise, and you end up ranking a list of ones and twos.

Then sort by impressions, not by position. A query at 19 with four thousand impressions is worth more of your attention than one at 11 with forty. Position tells you how far you have to travel; impressions tell you whether the trip is worth taking.

If you would rather not build that pivot by hand, our free striking distance finder takes the raw export and does it in the browser — no account, and the file never leaves the page.

## Price the win before you spend the weekend
The obvious next question is what moving from 14 to 8 would actually be worth. The obvious answer — look up a click-through-rate-by-position table — is the one to avoid.

Every published CTR curve is an average over other people’s sites, other people’s industries and other people’s queries. Branded searches inflate the top of the curve enormously, and they behave nothing like the unbranded keyword you’re chasing. A results page carrying ads, a generative summary and a product carousel loses most of its clicks before the organic list begins. Two sites in one industry can differ by several times over at the same position.

You already hold the only curve that predicts your traffic, in the same export. Bucket your queries by rounded position and, for each bucket, divide total clicks by total impressions.

That last part matters more than it sounds. **Do not average the CTR column.** Averaging the per-query rates lets a query with three impressions count as much as one with thirty thousand, and across a long tail of tiny queries it produces a curve where position 7 appears to beat position 2. Aggregate the totals instead.

Check how much data sits behind each bucket while you’re there. On most sites the curve stops meaning anything somewhere around position 15, simply because there aren’t enough impressions down there to support a rate. Our free CTR curve calculator does the aggregation and greys out the buckets that are too thin to trust, which is usually more of them than people expect.

Use the result comparatively, not predictively. “Position 5 earns roughly three times position 12 on our site” is a sound basis for ordering a to-do list. “This will earn 210 more clicks a month” is a forecast built on an average of averages.

## The page you shouldn’t touch
Here is the half that gets left out. A striking-distance keyword sits on a URL, and that URL almost never ranks for one thing.

Rewriting a page to chase the page-two keyword means changing its title, its headings and its emphasis. If that page is currently earning across a dozen other terms, you are trading a portfolio for a single position. Win the keyword, lose four others, and the net is negative — but the only number anyone checks afterwards is the one they were aiming at.

So before you edit anything, look at what the URL earns in total. Organic Keywords lists every keyword a page ranks for, which turns this from a guess into a two-minute check.

The rule of thumb: if the striking-distance keyword accounts for a small share of what the URL already earns, don’t retarget the page. Write a new one, or leave it. The pages worth rewriting are the ones where the page-two keyword is the point of the page and it’s underperforming anyway.

## The more likely diagnosis
When a page-two keyword refuses to move despite a page that deserves it, the common cause isn’t thin content. It’s that two of your own URLs are competing for the query.

The shape is recognisable: two pages both earning real impressions on one query, both in mediocre positions, neither pulling away. One page at 3 and another at 47 is not that — that’s a page that ranks and a page that doesn’t.

There’s a wrinkle in the metric that hides this, too. Google reports position as “the topmost position occupied by a link to your property or page in search results”, so at property level a split can sit behind a number that looks perfectly healthy.

Finding it needs an export that pairs queries with pages, and the standard Search Console export doesn’t — the Performance report gives you a file of queries and a file of pages, with nothing joining them. You have to filter by page and export the queries one page at a time, or pull both dimensions from the API. Our free cannibalisation checker takes whichever of those you produce and shows how the impressions are splitting.

When it’s real, consolidating usually beats adding content: merge the weaker page into the stronger one and redirect it, so the links and the relevance land in one place. Check what each page earns elsewhere first, for the reason in the previous section.

## What actually moves one
Assume the page is the right page, nothing of yours is competing with it, and the impressions justify the effort. What then?

Mostly the unglamorous version. Open the results page and look at the format of what ranks above you — if the top five are comparison tables and you published an essay, the gap isn’t quality, it’s shape. Check that the page answers the specific question in the query rather than the adjacent one it was originally written for. Make the title and the first heading match what’s being asked. Link to it from the pages on your site that are already about the subject.

Google’s SEO Starter Guide keeps circling the same unexciting point, and it’s the right one: the page has to be the better answer. Pages sit on page two because they’re about something slightly beside the query far more often than because they’re two hundred words short.

## The short version
Position 11 is an average, not a rank, so treat the band as a list of things to open rather than a queue of near-misses. Sort by impressions. Build your own CTR curve out of the same export and aggregate it properly instead of quoting anyone’s table. Before rewriting a page, check what else it earns — and if two of your URLs are on the query, that diagnosis comes before any content work.

Then record where you started. Search Console will hand you a three-month average, which is exactly the wrong instrument for telling whether last week’s edit landed; rank tracking checks the same keywords on the same schedule so you can see the move rather than infer it.

And if the list runs short, striking distance only ever covers queries you already rank for. The ones you’re absent from entirely need the competitor-relative version, which is the subject of the post on keyword gap analysis.
