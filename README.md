# Google News Scraper: Build It, Buy Credits, or Use a Structured Endpoint? What You Get, What It Costs, and Where Free Code Breaks (Plan Comparison)

The maintainers of a popular open-source Google News scraper had to rebuild theirs on Google's RSS feeds after a markup change killed the browser-based version. That's the thing you're paying to avoid. Google News itself is easy to request and hard to keep working, because the layout moves, the feed caps at 100 items, and the two fields most projects need (publish time and the real publisher URL) cost extra requests to get.

So the real question isn't whether Google News can be scraped. It's whether you self-host a script you'll maintain, or pay per request and let someone else watch for breakage. Below: what ScraperAPI's Google News endpoint costs in credits, which of its plans fits which volume, and the specific fields a DIY script quietly doesn't give you.

> **Quick answer:** use the free plan first (1,000 credits, about 40 Google News queries at 25 credits each, 5 concurrent connections) to confirm the response shape fits your pipeline. For steady volume, Startup is the first tier with real headroom: **$149/month, or $134.10/month billed annually ($1,609.20/year, saving $178.80)**, for 1,000,000 credits, roughly 40,000 news queries. If you only pull a few hundred headlines a month, Hobby's 100,000 credits cover about 4,000 queries. 👉 [See the live plan prices before you decide](https://scraperapi.com/pricing?fp_ref=coupons)

## All ScraperAPI plans, with the Google News volume each one buys

Google requests are billed at 25 credits per request, so the last column is just credits ÷ 25. Treat it as a budget ceiling, not a promise: ScraperAPI's dashboard has a Domain Cost Estimator that shows the exact cost for a specific URL, and you can set `max_cost` on a request to cap it.

| Plan | Credits / month and threads | Google News queries / month | Price | Open |
| --- | --- | --- | --- | --- |
| Free plan | 1,000 / 5 | ~40 | $0 | [Start free](https://scraperapi.com/signup?fp_ref=coupons) |
| Hobby | 100,000 / 20 | ~4,000 | Live price on page | [View Hobby](https://scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 / 50 | ~40,000 | **$149/mo**, or $134.10/mo annual | [View Startup](https://scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 / 100 | ~120,000 | Live price on page | [View Business](https://scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | 5,000,000 / 200 | ~200,000 | Live price on page | [View Scaling](https://scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 / 300 | ~420,000 | Live price on page | [View Professional](https://scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 / 500 | ~860,000 | Live price on page | [View Advanced](https://scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ / 500+ | ~880,000+ | Custom | [View Enterprise](https://scraperapi.com/pricing?fp_ref=coupons) |

Two differences between the tiers matter more than the credit count:

- **Proxy geotargeting.** Hobby and Startup are limited to US and EU proxies. Country-level targeting starts at Business. Separately, the news endpoint takes its own `country_code` and `tld` parameters (20 Google domains, from `us` and `uk` through `jp`, `br`, `ae`, `sa`), plus Google's `hl` and `gl`. Those tell Google which edition to serve; they're not the same thing as where your proxy sits, so check both if you need local editions outside the US and EU.
- **Analytics history.** 30 days on Hobby and Startup, unlimited from Business up. If you're tracking coverage over quarters rather than days, that alone pushes you up a tier.

## What one Google News query actually costs

ScraperAPI's credit table prices a standard page at 1 credit, e-commerce pages (Amazon, Walmart, eBay) at 5, and Google and Bing at 25 per request, subdomains included. The Google News endpoint is a Google request, so budget at that rate. Optional features stack on top: bot-protection bypass adds 10, JS rendering (`render=true`) adds 10, premium proxies add 10, and ultra premium is 30 (75 with rendering). Geotargeting, sessions, output format and auto parsing are free.

That single number explains the whole plan ladder. The free plan's 1,000 credits are 40 news queries, enough to validate a response shape and nothing more. A 1,000,000-credit Startup plan is 40,000 queries, or about 1,300 a day if you spread it across a month. Work out your daily query count before you shop, because the jump from 4,000 to 40,000 queries is the difference between a research script and a monitoring product.

Two billing details worth knowing before you commit. Credits reset when the subscription renews, so unused ones don't roll over. Auto-upgrade to the next tier is available on Hobby through Professional; pay-as-you-go past your limit, at a fixed per-credit rate with a monthly cap you set, is available on Scaling, Professional, Advanced and Enterprise. Annual plans can renew early to pull in a fresh batch of credits.

👉 [Check what a plan's credit allowance turns into for your query volume](https://scraperapi.com/pricing?fp_ref=coupons)

## Where a self-built Google News scraper breaks

A February 2025 Python walkthrough for scraping Google News parses three CSS classes: `div.W8yrY` for story containers, `a.gPFEn` for headlines, `div.vr1PYe` for the source name. It also spoofs a Chrome user agent so the request "looks like it comes from a human user," caps the output at 10 items, and rebuilds article links by prepending `https://news.google.com` and cutting off the query string.

That code was published over a year and a half ago and depends on class names Google controls. The same fragility already forced one maintained project to abandon its browser-driven approach entirely and switch to reading feeds. Class names are the first thing to break, and when they do, the script doesn't error loudly. It returns zero rows.

The other costs are structural, not accidental:

- **Publisher URLs.** Google News hands you redirect links. Resolving them to the actual article on the publisher's site takes two small extra requests per article, run five at a time. A 100-article pull becomes 200 additional requests.
- **Result ceilings.** Google News feeds return at most 100 articles per query, which is why feed-based libraries default their `limit` to 99. No amount of code gets you past the cap on a single query.
- **Article text.** Optional full-text extraction requires driving Puppeteer to each article, and the maintainers of the feed-based library say plainly that it "fails on many websites," returning an empty string when it does. If your plan is summarisation or an LLM pipeline, budget for a percentage of empty bodies.
- **IP reputation.** Rate limiting on cloud and datacenter hosts is common enough that one library ships a third-party fallback option specifically for it.
- **Environment drift.** The tutorials assume Python 3.7+ or 3.11, Node 18+, pinned dependency versions, a Dockerfile, a scheduler. None of that is hard. All of it is yours to keep running.

👉 [Run a test query on the free plan and compare the output to your script](https://scraperapi.com/signup?fp_ref=coupons)

## Which fields do you need? Match the tool to the answer

This is the part that decides the purchase more often than price does. A tutorial-grade script returns three fields: source, headline, link. No publish date, no body, no image, and only the first 10 items.

| What you need to control | Tutorial script | Feed-based library | ScraperAPI news endpoint |
| --- | --- | --- | --- |
| Time window | Not supported | `timeframe`: `12h`, `7d`, `6m`, `1y` (default `7d`) | `time_period`: `1H`, `1D`, `1W`, `1M`, `1Y`, or `date_range_start`/`date_range_end` |
| Region / edition | Edit the URL by hand | `hl` / `gl` / `ceid` | `country_code` + `tld`, plus `hl` / `gl` |
| Result count | Hard-capped in code | `limit`, default 99 | `num` for page size, `start` for offset paging |
| Output | Printed text | JSON with `datetime`, `sourceUrl`, `provider` | JSON or CSV |
| Batch runs | You write the loop | You write the loop | Async endpoint takes a `queries` array and posts to a webhook |

The publish timestamp is the field that changes what you can build. The feed-based library returns `datetime` in ISO form (for example `2026-10-14T09:12:00.000Z`) alongside a human string like `"3 hours ago"`. Without a machine-readable time, you can't dedupe stories across runs, measure pickup speed, or filter to the last six hours. ScraperAPI's news endpoint gives you JSON or CSV and a `search_information` block with the query, total result count and timing, followed by an `articles` array; check the sample response for the exact per-article field names before you wire your schema to it.

The async endpoint deserves a mention for anyone running monitoring rather than one-off pulls: it accepts a `queries` array in a single POST and supports webhook callbacks, so twenty tracked keywords don't need twenty sequential requests.

## Picking by scenario

**Just headlines and links, a few hundred a month.** Don't buy credits. The open-source route covers this: an MIT-licensed npm package reads feeds directly, resolves publisher URLs, exposes `timeframe` and `hl`/`gl`/`ceid`, runs as a single HTTP request on serverless platforms, and ships an MCP server for Claude, Cursor and similar clients. The cost is upkeep, not money.

**Publish-time filtering at volume, with nobody on call for breakage.** This is where a paid endpoint pays for itself. 40,000 queries a month on Startup covers a mid-size monitoring operation; 4,000 on Hobby covers a research team pulling daily snapshots of a dozen topics.

**Multi-region coverage.** Check the TLD list against your markets first, then check plan geotargeting. If you need country-level proxy targeting, that's Business and above regardless of how many credits you need.

**Full article text for summarisation.** Neither a plan upgrade nor a pricier tier fixes extraction failures on hard sites. Expect empty strings and handle them.

## What else is on the market

Apify sells a ready-made Google News scraper that handles query-based search, time ranges and scheduling; its free plan is permanent and requires no credit card, which makes it the cheapest place to test a hosted workflow. Oxylabs offers a free trial of up to 2,000 results and returns sources, titles, URLs and dates in JSON, HTML or Markdown. SerpApi exposes a `google_news` engine returning structured JSON and markets itself on skipping parsing and proxy work. Bright Data's repository splits its guidance in two, a free method for small projects and a dedicated Google News API for large-scale extraction. The MIT npm package above is the strongest free option if you're willing to own the maintenance.

Each of these fits a different shape of team. None of them removes the 100-article ceiling per query, because that ceiling belongs to Google.

## FAQ

**Can you scrape Google News?** Yes, in practice. Commercial APIs, open-source libraries and hand-written scripts all do it today. The version that survives is usually the one that reads Google's feeds rather than parsing its rendered HTML.

**Is it legal?** Scraping publicly accessible pages is broadly treated as permissible in many jurisdictions, but copyright and personal-data rules differ by country, and vendor blog posts saying "it's legal" are marketing, not legal advice. If you're republishing text or storing personal data, get your own answer.

**Why did my script work yesterday and return nothing today?** Two usual causes. Google changed markup your CSS selectors depended on, or it rate-limited your server's IP, which is common on cloud and datacenter hosts. Reading feeds rather than HTML removes the first cause; the second needs proxies or a hosted endpoint.

**How many results can I get per query?** 100, the feed maximum. Everything past that needs a different query or a paging parameter.

## Before you check out

Match the plan to your monthly query count, not to the credit number that sounds biggest. Then confirm two things: whether your monthly allowance covers your peak week (credits don't roll over), and whether your target editions are inside the plan's geotargeting and TLD coverage. If you're unsure, 1,000 free credits is about 40 real queries against your actual keywords, which is enough to see whether the response fits your schema before you spend anything.

👉 [Start with the free plan and size the paid tier from real query counts](https://scraperapi.com/signup?fp_ref=coupons)
