<img src="icon.png" alt="fairfetch" width="80" align="right">

# fairfetch MCP tools: web data for AI agents, priced per result

10 data tools by [fairfetch](https://apify.com/fairfetch), an independent developer on Apify Store.
Each tool is an Apify Actor; [Apify's hosted MCP server](https://docs.apify.com/platform/integrations/mcp)
makes them available to Claude, Cursor, VS Code and other MCP clients. You pay per result returned, plus
a small start fee per run. There is no subscription and no platform usage fee on top. Apify handles
sign-in, billing and the servers.

## Tools

| Tool (Apify Actor) | What it returns | Price per result | Per run |
|---|---|---|---|
| [`fairfetch/google-trends-scraper`](https://apify.com/fairfetch/google-trends-scraper) | Google Trends: interest over time, by region or city, related queries, up to 5 terms compared, Trending Now | $0.30 / 1,000 rows | $0.00005 |
| [`fairfetch/keyword-search-volume`](https://apify.com/fairfetch/keyword-search-volume) | Google Ads keyword data (via DataForSEO): monthly search volume, CPC, competition, 12-month trend, keyword ideas | $3 / 1,000 keywords, $0.10 per fresh lookup of up to 1,000 keywords | $0.00005 |
| [`fairfetch/google-ads-transparency-scraper`](https://apify.com/fairfetch/google-ads-transparency-scraper) | Google Ads Transparency Center: an advertiser's ads with image and YouTube links, dates, regions, EU reach, ad text where Google shows it | $1 / 1,000 ads | $0.00005 |
| [`fairfetch/app-store-google-play-scraper`](https://apify.com/fairfetch/app-store-google-play-scraper) | App Store and Google Play apps, ratings, reviews (no reviewer names or IDs), search results and top charts | $1 / 1,000 apps, $0.10 / 1,000 reviews | $0.00005 |
| [`fairfetch/career-site-jobs-api`](https://apify.com/fairfetch/career-site-jobs-api) | Company job boards on Greenhouse, Lever, Ashby, Workday and SmartRecruiters, with new/closed job tracking | $1.50 / 1,000 jobs | $0.0005 |
| [`fairfetch/remote-jobs-scraper`](https://apify.com/fairfetch/remote-jobs-scraper) | Remote jobs from Remote OK and Jobicy in one schema, de-duplicated, with new/closed job tracking | $1 / 1,000 jobs | $0.0005 |
| [`fairfetch/document-to-markdown`](https://apify.com/fairfetch/document-to-markdown) | PDF, Word, PowerPoint, Excel, CSV and web pages from URLs to Markdown and text (no OCR for scanned PDFs) | $1 / 1,000 documents, $0.10 / 1,000 pages (PDF page or slide with text, or 1,000 spreadsheet rows) | $0.00005 |
| [`fairfetch/website-screenshot-api`](https://apify.com/fairfetch/website-screenshot-api) | Screenshots (PNG, JPEG) or PDFs of public web pages: full page, mobile emulation, dark mode, cookie banners hidden | $3 / 1,000 screenshots, $10 / 1,000 full-page screenshots or PDFs | $0.0051 ($0.005 run start + $0.0001 Apify start fee at 2 GB) |
| [`fairfetch/au-supermarket-prices`](https://apify.com/fairfetch/au-supermarket-prices) | Coles, Woolworths and ALDI Australia prices, specials and unit prices, with change tracking | $1.50 / 1,000 products, $0.30 / 1,000 products checked in only-changes mode | $0.00005 |
| [`fairfetch/eventbrite-events-scraper`](https://apify.com/fairfetch/eventbrite-events-scraper) | Eventbrite events by city or online, keyword, category and date, with venues and ticket price ranges | $1 / 1,000 events, $2 / 1,000 events with event-page details | $0.00005 |

Prices as read from the Apify API on 27 Oct 2026; the pricing on each tool's Apify page is
authoritative. "Per run" is charged once per run: Apify's standard start fee (one per GB of run
memory, at the tool's default memory, which is what MCP calls use) plus the tool's own run-start fee
where it has one.

All tools return structured JSON rows whose fields are documented on each tool's Apify page, don't
charge result prices for failed inputs (not found, blocked, invalid), and run with Apify's limited
permissions.

## Cost control: read this before giving an agent these tools

**Through this MCP URL there is no per-run cost cap.** Apify starts each tool call with the agent's input
and the tool's default memory only, so the "maximum cost per run" (`maxTotalChargeUsd`) that you can set
for runs in Apify Console or the API doesn't apply. What limits spending:

1. **Your Apify account's usage limit.** Set it before connecting an agent: Apify Console > Billing >
   Limits. Apify suspends platform services for the account when the limit is reached.
2. **A prepaid agent token** (see below) can't spend more than its balance.
3. **Each tool's own input limits.** An agent can put many terms, URLs or companies into one call, and
   costs grow with them. The main limits, and the cost of a large single call at today's prices:

| Tool | Input fields that bound a call | Example of a large call |
|---|---|---|
| `google-trends-scraper` | `maxItems` caps rows per run (default 0 = no cap); rows grow with terms, regions and time range | 10 terms over 5 years, interest over time only (2,610 rows): about $0.78 |
| `keyword-search-volume` | `keywords`: up to 20,000 per run; `maxIdeas`: default 100, up to 5,000 (ideas are off by default) | 20,000 keywords plus 5,000 keyword ideas (21 lookups): about $77.10 |
| `google-ads-transparency-scraper` | `maxAdsPerAdvertiser`: default 100, up to 50,000 per entry in `advertisers`; `maxAdvertisersPerName`: default 1, up to 10 | one advertiser at the 50,000-ad maximum: about $50 |
| `app-store-google-play-scraper` | `maxResultsPerSearch`: default 20, up to 200; `maxAppsPerChart`: default 100, up to 200; `maxReviewsPerApp`: default 100, up to 100,000 (reviews are off by default) | one Google Play app with 100,000 reviews: about $10 |
| `career-site-jobs-api` | `maxJobsPerCompany` (default 0 = every open job) for each entry in `companies` | every open job at a 731-role company (Stripe, tested): about $1.10 |
| `remote-jobs-scraper` | `maxJobs` (default 0 = every job on the boards) | every job on both boards (about 920 on 18 Oct 2026): about $0.92 |
| `document-to-markdown` | one document per entry in `urls`; `maxPages` per document (default 0 = all, up to 10,000); `maxFileSizeMb` (default 50) | 100 PDFs of 100 pages: about $1.10 |
| `website-screenshot-api` | one capture per entry in `urls` (no count limit) | 1,000 full-page screenshots: about $10.01 |
| `au-supermarket-prices` | `maxItemsPerQuery`: default 30, up to 5,000 per query and supermarket (0 = all, up to 5,000) | one query at 5,000 products from each of the 3 supermarkets: about $22.50 |
| `eventbrite-events-scraper` | `maxEventsPerSearch`: default 100, up to 1,000; `maxEvents` (default 0 = no total cap) | one search at 1,000 events with page details: about $2 |

For direct API or Console runs (not MCP), every tool also stops cleanly at the maximum cost per run you
set and keeps the results returned up to then.

## Connect

MCP server URL (Streamable HTTP):

```
https://mcp.apify.com/?tools=fairfetch/google-trends-scraper,fairfetch/keyword-search-volume,fairfetch/google-ads-transparency-scraper,fairfetch/app-store-google-play-scraper,fairfetch/career-site-jobs-api,fairfetch/remote-jobs-scraper,fairfetch/document-to-markdown,fairfetch/website-screenshot-api,fairfetch/au-supermarket-prices,fairfetch/eventbrite-events-scraper
```

**Claude Code**

```bash
claude mcp add --transport http fairfetch "https://mcp.apify.com/?tools=fairfetch/google-trends-scraper,fairfetch/keyword-search-volume,fairfetch/google-ads-transparency-scraper,fairfetch/app-store-google-play-scraper,fairfetch/career-site-jobs-api,fairfetch/remote-jobs-scraper,fairfetch/document-to-markdown,fairfetch/website-screenshot-api,fairfetch/au-supermarket-prices,fairfetch/eventbrite-events-scraper"
```

Then run `/mcp` in Claude Code, pick `fairfetch` and sign in to Apify (or run `claude mcp login fairfetch`).

**Cursor** (`~/.cursor/mcp.json`, or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "fairfetch": {
      "url": "https://mcp.apify.com/?tools=fairfetch/google-trends-scraper,fairfetch/keyword-search-volume,fairfetch/google-ads-transparency-scraper,fairfetch/app-store-google-play-scraper,fairfetch/career-site-jobs-api,fairfetch/remote-jobs-scraper,fairfetch/document-to-markdown,fairfetch/website-screenshot-api,fairfetch/au-supermarket-prices,fairfetch/eventbrite-events-scraper"
    }
  }
}
```

**Other MCP clients**: add a remote (Streamable HTTP) server with the URL above. In VS Code, run
**MCP: Add Server** and choose HTTP. In the Claude apps, use Settings > Connectors > Add custom connector.

To load only some tools, keep just those Actors in the `tools=` list, e.g.
`https://mcp.apify.com/?tools=fairfetch/google-trends-scraper`.

**Authentication.** By default you sign in to Apify (OAuth) in your browser: most clients start the sign-in
when you first connect, and Claude Code starts it from `/mcp` as shown above. For headless agents, send
the header `Authorization: Bearer <APIFY_TOKEN>` with a token from
[Apify Console > Settings > API & Integrations](https://console.apify.com/settings/integrations).
Apify's free plan includes $5 of platform usage per month ([pricing](https://apify.com/pricing)).

**Agents without an Apify account** can pay with x402 or MPP and get a temporary, prepaid Apify token to
use as the Bearer token. Apify marks this as experimental; see its
[agentic payments docs](https://docs.apify.com/agents/payments).

## About

fairfetch is one developer publishing on Apify Store. The tools read public web pages, feeds and APIs
without logging in to those sites (keyword data comes from the DataForSEO API). They are not affiliated
with or endorsed by Google, DataForSEO, Apple, Greenhouse, Lever, Ashby, Workday, SmartRecruiters, Remote OK, Jobicy, Coles, Woolworths, ALDI or Eventbrite; those names only describe where the data comes from. Some
sources' terms restrict automated access: each tool's Apify page has a Legal section, and you are
responsible for how you use the data.

Questions, bugs and feature requests: the Issues tab on each tool's Apify page. This repository only
holds the listing, so its own issue tracker is turned off.

MIT licensed (this repository contains only documentation, configuration and the icon).
