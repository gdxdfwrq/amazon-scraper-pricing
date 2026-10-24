# amazon scraper tool: Credit Costs, Real Per-Page Pricing and the Cheaper Alternatives Most Comparisons Skip

Searching for an amazon scraper tool usually means one thing: you need product data, prices, reviews or search rankings from Amazon at some kind of scale, and you've probably already discovered that a simple requests script gets blocked within minutes. The real decision isn't "which tool exists" — dozens exist. It's which one survives Amazon's defenses, what it actually costs per page once you're past the marketing numbers, and whether a managed API beats building your own proxy stack.

The short version: for most developers and small teams, a scraping API with a dedicated Amazon endpoint is the practical route. ScraperAPI is one of the most established names — it handles proxies, CAPTCHAs and retries for you, and its Amazon endpoint costs 5 credits per request rather than 1, which matters a lot for budgeting. It's not the cheapest option per request, though, and depending on your volume, alternatives like Scrape.do or Oxylabs can cost a fraction per thousand pages. Below is the full breakdown with verified numbers.

> **Quick take:** Start with ScraperAPI's free trial — 5,000 credits over 7 days, no credit card required. If your volume stays around 20,000 Amazon pages a month (100,000 credits at 5 credits per request), the $49 Hobby plan covers it. If you're pushing 100K+ pages monthly and per-request cost is your deciding factor, check the comparison table further down before committing. [👉 Start the free trial and test it on real Amazon pages](https://www.scraperapi.com/signup?fp_ref=coupons)

## What an Amazon scraper tool actually has to do

Amazon is one of the hardest scraping targets on the open web. It rate-limits by IP, serves different layouts depending on the visitor's geography, runs aggressive bot detection, and changes its markup often enough that a homegrown parser breaks a few times a year.

So when you pay for a managed scraper API, you're really buying four things bundled together:

1. **Proxy rotation** — a pool of IPs that keeps requests from all coming from one address. ScraperAPI advertises 40 million+ IPs across residential, datacenter and mobile networks.
2. **Anti-bot handling** — CAPTCHA solving, retries, and routing through harder-to-block IP pools when needed.
3. **Rendering** — a headless browser for pages that load content with JavaScript.
4. **Parsing** — ideally, structured JSON instead of raw HTML, so you never maintain fragile CSS selectors.

The last item is the one that separates tools into two tiers. General-purpose APIs hand you HTML and let you figure out the rest. APIs with dedicated Amazon endpoints return product title, price, ASIN, rating and reviews as clean JSON. If Amazon changes its page structure, that's their problem, not yours.

## ScraperAPI plans and pricing (all plans, verified pricing)

Here's the full lineup currently shown on ScraperAPI's pricing page. Every paid plan includes JS rendering, premium residential and mobile IPs, auto-parsing, CAPTCHA bypass, unlimited bandwidth and a 99.9% uptime guarantee. Annual billing takes 10% off each monthly price.

| Plan | Price (monthly) | Annual (per month) | Credits | Threads | Geo targeting |
| --- | --- | --- | --- | --- | --- |
| Free trial | Free, 7 days | — | 5,000 | — | — |
| Free plan | $0 | — | 1,000/mo | 5 | US & EU |
| Hobby | $49 | $44.10 | 100,000 | 20 | US & EU |
| Startup | $149 | $134.10 | 1,000,000 | 50 | US & EU |
| Business | $299 | $269.10 | 3,000,000 | 100 | Country-level |
| Scaling | $475 | $427.50 | 5,000,000 | 200 | Country-level |
| Professional | $975 | $877.50 | 10,500,000 | 300 | 70 countries |
| Advanced | $1,975 | $1,777.50 | 21,500,000 | 500 | 70 countries |
| Enterprise | Custom | Custom | 22M+ | 500+ | Custom |

[👉 See all current plans on the pricing page](https://www.scraperapi.com/pricing/?fp_ref=coupons)

That's the surface. Two details buried in the billing rules change what you'll actually pay, and they're the parts most comparison articles skip.

### Amazon requests cost 5 credits, not 1 — do the math before choosing a plan

Credits are not requests. A standard page costs 1 credit, but Amazon costs **5 credits per request** because Amazon is a harder target. Google and Bing cost 25, LinkedIn costs 30, and sites behind Cloudflare, Datadome or PerimeterX protection add another 10 credits per request when the bypass kicks in.

Then the parameters stack on top. JS rendering (`render=true`) adds 10 credits. Premium proxies (`premium=true`) add 10. Ultra-premium runs 30, and ultra-premium plus rendering runs 75 per request.

Here's what that means in practice. The Hobby plan's 100,000 credits is not 100,000 pages:

- Plain Amazon requests (5 credits each): **20,000 pages**
- Amazon pages with JS rendering (15 credits each): **~6,600 pages**
- A rendering + premium combination (25 credits each): **4,000 pages**

A reviewer at proxyscraping.org ran a similar calculation on the Startup plan and put it bluntly: on a job needing rendering plus premium proxies on a protected target, a single successful scrape can cost 20–30 times the headline per-credit price, and a million credits might only cover 40,000–100,000 actual pages instead of a million. Budget against your real per-page cost, not the credit count on the pricing page.

ScraperAPI gives you two tools to keep this under control, and both are worth using from day one. The **Domain Cost Estimator** in your dashboard shows the exact credit cost for any URL before you scale up, and a `max_cost` parameter caps what any single scrape can consume. If you're evaluating plans, run ten of your real target URLs through the estimator first — it turns guesswork into arithmetic.

> On the flip side, ScraperAPI only charges for successful requests; internal retries on failed attempts don't burn credits. And if you exceed your plan's concurrent thread limit, you get a 429 response with no extra charge — requests are rejected rather than billed and queued.

### Three limits that live in the fine print

These are the restrictions you'll only find by reading the pricing page and docs carefully, and each one disqualifies a plan for certain users:

- **Geo targeting is US & EU only on Hobby and Startup.** Country-level targeting starts at Business ($299/month). If you need Amazon prices from Japan, India or other individual marketplaces, the $49 and $149 plans won't get you there. The full 70-country list unlocks at Business and above.
- **Sticky sessions are short.** The `session_number` parameter reuses the same IP, but sessions expire 15 minutes after their last use. Fine for a scraping run; a problem if you need long-lived sessions for account-based workflows.
- **No IP-level control.** Everything routes through one endpoint. You can't pick a specific IP or ASN, you can only steer by parameters and trust the rotation logic.

One more operational detail: when credits run out, scraping pauses by default. Hobby through Professional plans can opt into **auto-upgrade** (bumps you to the next plan automatically), and Scaling and above support **pay-as-you-go** at a fixed per-credit rate up to a monthly cap you set. If your monitoring jobs can't tolerate a mid-month pause, set one of these up before it happens at 2 a.m.

[👉 Open the Hobby plan pricing and start with 100,000 credits](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## How ScraperAPI's Amazon endpoint works

For Amazon specifically, the structured data endpoint is the feature doing the heavy lifting. You hit `/structured/amazon/search` (product pages have their own endpoints) and get back parsed JSON — titles, prices, ratings, ASINs — instead of a wall of HTML you'd have to parse yourself.

Other parameters that matter for Amazon work:

- `country_code` — pull the German or UK version of a listing (from Business tier up)
- `device_type=mobile` — see what mobile users see
- `autoparse=true` — automatic JSON parsing on supported domains
- `session_number` — same IP across a sequence of requests
- Async endpoint (`async.scraperapi.com/jobs`) — submit large batches without holding connections open
- DataPipeline — a low-code interface for scheduling recurring jobs and pushing results to a webhook

If you're building a price monitor, the practical flow is: run your target URLs through the Domain Cost Estimator, check whether plain requests (5 credits) work on your target pages before enabling rendering (15 credits), and only reach for premium pools when the default pool actually starts failing. Most standard Amazon pages clear without premium, which is exactly why starting on Hobby is reasonable.

[👉 Test your target Amazon pages with 5,000 free credits](https://www.scraperapi.com/signup?fp_ref=coupons)

## How it stacks up against the other Amazon scraper options

A comparison published by scraping API vendor Scrape.do (updated October 2026, with speed and success-rate figures from their March 2026 test) put eight dedicated Amazon APIs head to head. Note the source runs their own product in this table, so treat their conclusions accordingly — but the pricing data was checked against each provider's pricing page in October 2026, and ScraperAPI's own figures here match its official pricing.

| Provider | Success rate | Cost per 1K Amazon requests | Starting price | Best sellers endpoint |
| --- | --- | --- | --- | --- |
| Scrape.do | 100% | $0.12 | $29/mo (+ free plan) | Yes |
| ScrapingDog | 89.06% | $0.20 | $40/mo | No |
| Oxylabs | 100% | $0.50 | $30/mo | Yes |
| Decodo | 100% | $1.00 | $19/mo | Yes |
| ScrapingBee | 99.37% | $1.27 | $19/mo | No |
| Bright Data | 97.83% | $1.50 | Pay-as-you-go | Yes |
| **ScraperAPI** | **100%** | **~$2.45** | **$49/mo** | **No** |
| WebScrapingAPI | 93.27% | $5.90 | $59/mo | Yes |

A few honest observations, including ones that favor competitors:

- **On raw per-request cost, ScraperAPI is not the winner.** At roughly $2.45 per 1,000 Amazon requests on its entry plan, it costs about 20 times what Scrape.do charges on the same metric. If your project is high-volume, low-margin price tracking, that gap compounds fast.
- **Where ScraperAPI holds its ground:** a 100% success rate in that test, and breadth beyond Amazon. Its structured endpoints cover Google Search, Google Shopping, Walmart and eBay product pages too, plus SERP scraping, so one API and one credit pool handles a multi-site workflow. Several cheaper alternatives are Amazon-only specialists.
- **Endpoint gaps to know about:** that same comparison notes ScraperAPI lacks a dedicated best-sellers endpoint, and it doesn't natively extract product variations (color/size/model) — only Oxylabs does that natively in the table. If variation crawling is core to your project, factor that in.
- **Bright Data** is the pick if you want ready-made datasets or the largest proxy network (150M+ IPs), though its per-record pricing model ($1.50–$1.30) works differently from credit systems.

## Which plan fits which situation

Working from the verified numbers:

- **Testing an idea or validating feasibility:** the free tier. 5,000 trial credits is genuinely enough to run a real task, and the ongoing free plan (1,000 credits/month, 5 threads) keeps small experiments alive indefinitely at $0.
- **Price monitoring or SERP tracking under ~20,000 Amazon pages/month:** Hobby at $49 ($44.10 on annual billing). No country targeting outside US/EU, but 20 threads and the structured Amazon endpoint handle most standard use cases.
- **Scale with a growing batch of marketplaces:** Startup to Business. The jump to Business at $299 matters mainly if you need country-level geotargeting — if you only scrape amazon.com, Startup's 1M credits (200,000 Amazon pages) may be plenty.
- **Heavy rendering + premium workloads at volume:** the math changes completely here. At 75 credits per ultra-premium rendered request, even Professional's 10.5M credits is only 140,000 pages. Compare against bandwidth-billed residential proxy plans before choosing this route.
- **Pure high-volume Amazon scraping where cost dominates:** competitors in the table above charge $0.12–$0.50 per 1,000 requests at entry tier. If Amazon is all you'll ever scrape, the arithmetic favors them.

## Before you commit

Two things worth checking before your first paid month. First, confirm your credit burn with the Domain Cost Estimator using your actual target URLs — the difference between 5-credit and 15-credit pages decides which plan tier you really need, and it's the mistake most first-time buyers make. Second, decide now what happens when credits run out mid-month: enable auto-upgrade or pay-as-you-go, or accept the pause.

And if you're still unsure whether this fits your workflow, the free trial settles it with real data instead of reviews. [👉 Sign up for ScraperAPI's 7-day free trial](https://www.scraperapi.com/signup?fp_ref=coupons)
