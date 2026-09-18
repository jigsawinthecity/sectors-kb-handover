# Sectors knowledge base, business team edition

Internal reference for answering customer questions directly. Drafted 2026-09-16 from
the answered question set in `questions-round-1.md`, the pipeline sweep in
`pipeline-inventory.md`, metric definitions in `terms.md`, and real support transcripts.

**How to use this.** Every page is written so the relevant part can be pasted into a
reply and edited for tone. Anything marked **Internal only** must never reach a
customer. Anything marked **Open** has no approved answer yet, so route it rather than
improvise. Replies stay human-written; this is a reference, not a script.

**Tone.** Professional, helpful, proactive. The team is fluent in Indonesian and replies
to customers in their own language. This document is English only.

**Required disclaimer**, verbatim, on anything that could read as a recommendation:

> does not constitute investment advice, a recommendation, or an offer to buy or sell
> any security

---

## Part 1. The ten things customers actually ask

Ordered by how often they appear in support transcripts.

### 1.1 How do credits work?

Cost scales with how much an endpoint returns, not per call flat.

| Call | Cost |
|---|---|
| A structured query | 1 credit |
| A natural-language query, the `?q=` parameter | 3 credits |
| Full closing prices for all listed companies, 900+ in one call | about 32 credits |

Insider plan includes 5,000 credits per month, refreshed automatically. **No rollover**,
unused credits expire at month end. Workshop credits are separate: 3,000 credits valid
for one full year, and they sit alongside the monthly allowance rather than replacing it.

When credits run out the call returns nothing. It is a hard stop, not a throttle.

Failed calls are free. 400, 401, 403, 429 and 5xx cost no credits. Only 2xx and 404
bill. Full billing detail lives in the Credits and Billing FAQ on the site, which is the
source of truth if this section and the product ever disagree.

Per-endpoint credit costs are stated on each endpoint's page in the API docs, so point a
customer there rather than quoting a cost from memory: <https://docs.sectors.app/>

**Is 5,000 credits enough?** For a daily full-universe close pull, yes. That is one call
at about 32 credits per trading day, roughly 700 a month, well inside the allowance. The
screener is not required for it. Customers planning a per-ticker loop are the ones who
run out, so ask how they intend to pull before answering.

### 1.2 What do I get on each plan?

| | Free | Standard | Insider |
|---|---|---|---|
| IDX coverage | 99.9% | 99.9% | 99.9% |
| SGX coverage | 80% | 80% | 80% |
| Share price, dividends, splits | Yes | Yes | Yes |
| Peer comparison, calendar, news, AI tagging | Yes | Yes | Yes |
| ESG | Ratings and scores | Full | Full |
| Financial statements | Core | Core plus segment revenue | Full |
| Analysis tools | Ratios and DCF | Full | Full |
| Broker analysis | Top 3 brokers | Full | Full |
| Management and ownership | Board and ownership | Full | Full |
| Search | Without AI | AI-powered | AI-powered |
| Data export | Yes | Unlimited | Unlimited |
| Sector-specific metrics | No | Yes | Yes |
| Insider transactions | No | Yes | Yes |
| Sectors AI Chat | No | Yes | Yes |
| Personal watchlist | No | Yes | Yes |
| API access | Yes, with quest credits | Yes | Yes |
| Stock screener | No | **No** | Yes |
| API credits | None on signup, 500 after onboarding quests | No | 5,000 per month |
| Workflow automations | No | No | Yes |
| External AI integration | No | No | Yes |
| Mining, Metals and Minerals | No | No | Yes |
| Workshops | No | No | Free access to all |

Two that catch people out: the **screener is Insider-only**, not Standard, and the
**monthly 5,000 API credits are Insider-only** even though Standard has API access.

A free account can use the API with the 500 credits claimed from the onboarding
quests. No upgrade is needed to spend them.

There is a permanent free tier. It is not a trial.

### 1.3 How much history do I get?

Depth differs by dataset. There is no single number for the product.

| Dataset | Depth |
|---|---|
| IDX daily price, via the API | 2020 onward, about six years |
| IDX daily price, on request | Ten years |
| Dividends (`idx_dividend`) | 2020-01-03 onward |
| Broker summary | 2025-01-02 onward |
| Financial statements | 2018 onward |
| Foreign flow, from daily price data (`idx_daily_data`) | 2020-01-02 onward, ten years on request |
| Foreign flow, from broker data (`idx_broker`) | 2025-01-02 onward |
| KSEI 1% ownership register | March onward, monthly |

Separately from depth, the API caps how much you can pull per request: **default 30
days, maximum 90 days per call**. A customer asking "how much history" usually means
this, not the archive depth, so answer both.

So the answer is **six years through the API, ten years on request**. Both are correct,
they describe different things: the API serves from 2020-01-02, and a deeper archive can
be supplied on request.

A customer was previously told "up to 5 years via our API". That is wrong, it is six.
Correct it if the same customer returns.

### 1.4 What do you cover?

Verified 2026-09-14 against the database.

| Market | Coverage |
|---|---|
| IDX | 962 active companies, all boards |
| SGX | 618 active counters, 40 of them suspended |
| KLSE | 1,005 companies |

IDX board split: Main 260, Development 457, Acceleration 44, New Economy 3, Watchlist
198. The standard coverage figure to quote is **99.9% of IDX-listed companies**.

For SGX, the **full comprehensive report** covers the top 200 counters. A customer
looking at a counter outside that set sees a thinner profile by design, not a data gap.
Say so plainly rather than treating it as a fault.

Also covered: 18 IDX and regional indices (LQ45, IDX30, IDXHIDIV20, IDXBUMN20, IDXV30,
IDXG30, IDXQ30, IDXESGL, KOMPAS100, SRI-KEHATI, SMinfra18, JII70, FTSE Indonesia, IHSG,
STI, KLSE, ECONOMIC30, IDXVESTA28), and six mining commodities on the Insider plan
(coal, nickel, gold, copper, tin, bauxite).

Also covered, as listed to a prospect: ownership data including KSEI, insider filings,
and corporate actions (AGM and RUPS, public expose, warrants, rights issues, bonus
shares, dividends and upcoming dividends, stock splits and reverse splits). Scale line
that has been used: about **67,900 data points processed every day**, with detail on
<https://sectors.app/data-operations>.

Not covered: **US equities**. If asked, the answer is that Sectors does not currently
cover US markets.

Sector classification follows the official IDX-IC system, 33 sub-sectors, stable.

### 1.5 Can I display or resell your data?

Start here, then route. This is the question most likely to need someone else.

What the Terms of Service say: content is provided as-is for personal, non-commercial
use, or for the internal business use of a company. Copying, republishing, public
display, distribution, sale or licensing for a commercial purpose needs prior written
permission. Commercial use includes any business activity whether or not it turns a
profit, and any integration of Sectors content into a commercial product. That requires
a Sectors for Enterprise agreement.

For a startup building a paid product on Sectors data, a startup licence can be
arranged. Put them in touch with the business team rather than quoting the ToS at them.
See 1.5c.

**Insider does not grant commercial use.** If a customer's end users are paying
subscribers of their app, or the data is shown to anyone other than themselves, they
need a commercial licence, whichever plan they currently hold.

**Open, do not answer**: whether Sectors holds redistribution rights from IDX Data
Services, whether Sectors' own IDX licensing limits what it can grant downstream, and
whether attribution or user-count reporting is owed. Route to legal. Customers have
already asked these and they have no approved answer.

**Internal only**: the Terms of Service are inconsistent here. One section permits
internal business use, another treats any business activity as commercial and
prohibited without Enterprise. Until that is resolved, do not quote the ToS as though it
settles the question.

### 1.5c Commercial licensing: Enterprise and the startup licence

Two routes exist for a product that shows Sectors data to its own users: Sectors for
Enterprise, and a startup licence. **Which route fits, and on what terms, is a judgement
for the business team** based on the client's size, use case and usage. The past
enquiries below are reference only, not a price list or a template to promise.

**Sectors for Enterprise** (<https://sectors.app/enterprise>). Standard facts that can be
quoted:

- Covers commercial applications with unlimited end users.
- Custom SLA with **higher rate limits** and **99% uptime** (see 4.4). There are no
  per-minute rate limits.
- White-label use, without attribution, is available only on Enterprise.
- Extra datasets or a longer historical range can be supplied if a client needs
  something specific.

**Startup licence.** A commercial licence for early-stage companies, issued as a signed
agreement. Its terms, including price, duration, subscription requirement and rate
limits, are **negotiated between the business team and the client** and are not fixed.

**Reference from past enquiries:**

- A prospect planning a public retail analysis platform, funded by subscriptions and
  display ads, was quoted Enterprise at $3,999 per month paid annually, with a call
  offered to discuss a startup discount. The reply confirmed that subscription and
  advertising models were acceptable.
- A startup received a startup licence agreement tied to an active Insider Annual
  subscription with at least six months remaining, running to the end of 2027, with
  attribution to Sectors required. They were told an extension was possible and that a
  dedicated Startup Tier was planned. Do not promise that tier or its price.
- Solo developers building paid apps were told they needed a commercial licence and
  that a startup discount on Enterprise was possible.

For licensing questions without a written answer, apply 1.5 and 1.5b: answer from the
Terms of Service where they are explicit, otherwise route to legal.

### 1.5b Storing our data locally, bulk extraction, academic use

Three licensing questions that arrive with the redistribution one and have different
answers.

**Storing data locally for personal research.** A quantitative researcher pulling data
into Parquet, DuckDB or a backtesting tool, for their own use and not redistributing,
is asking whether permanent local storage is allowed. **Open, no approved answer.**
Route it with the redistribution question in 1.5. Do not say yes on the basis that it
sounds harmless, and do not say no; the customer's stated purpose, a reproducible
point-in-time research dataset, is reasonable and the answer needs to come from whoever
owns the licence terms.

**Caching or storing API responses in the customer's own database.** Answer from the
Terms of Service: systematically retrieving data to build a database or collection
needs Sectors' prior written permission. If the customer's signed agreement grants that
permission, point to it. If nothing is in writing, including what happens to stored
data after an agreement ends, route to legal.

**Bulk or snapshot extraction.** Customers initialising a full historical database ask
for a bulk option rather than paging the API. **Open.** There is no documented bulk
product. Take the requirement and route it rather than improvising a credit estimate.

**Academic and student use.** There is no dedicated student or academic pricing. Say so
plainly, then point at the free tier and the API playground so a student can evaluate
without paying: <https://sectors.app/api>

**Personal, non-commercial app building.** Someone building a private analysis tool for
themselves, not selling or publishing it, falls inside personal non-commercial use. The
route is the API on a paid plan; there is no scraping access and none will be granted.
Note there is no real-time data, so a trading-oriented tool needs to know it is working
with closing prices.

### 1.6 Your number disagrees with another provider

Usually a standardization difference, not an error. The approved framing, drawn from the
public data operations page:

- Numbers are standardized with accounting judgment so companies can be compared
  against each other, rather than passed through exactly as each company reported them.
- Every figure is externally verifiable: traceable to its source document and
  mathematically checkable.
- Sectors follows IDX standards by default, OJK, Basel III and Bank Indonesia
  classification for banks, and IDX-IC for sector classification.
- Extraction is AI-assisted with human review, and anomalies are flagged automatically.

For **market capitalisation** specifically, the approved line:

> Market capitalisation is taken from official sources where available. Where it is not,
> we calculate it from the closing price and the most recent available share count,
> which can differ from providers who use the share count in force on each historical
> date.

**Internal only**: the fallback is recorded per row as `mcap_method`. Method 1 is the
official figure, method 2 back-fills using an implied constant share count, method 3
uses the share count held in the database. Method 2 is why historical market cap can
diverge for a company that changed its share count. `mcap_method` is not exposed through
the API, so never tell a customer to look at it.

For **financial statements**, a real precedent to reuse: a customer reported TLKM
financials lagging another platform. Cause was the company filing late with the
exchange, so the figure did not exist when the pipeline ran. The reply acknowledged it,
confirmed a pipeline rerun, and explained the guardrail added to catch it next time.
That shape of answer works: cause, action taken, prevention.

**Known accuracy issues, raised by a subscriber comparing against another provider:**

- **EBITDA** can differ, because Sectors derives it rather than taking the reported
  figure, and depreciation and amortisation treatments differ between the company's
  statements and the calculation. A fix is in progress to take reported EBITDA directly
  where it is available. If a customer raises EBITDA, acknowledge the cause and say the
  change is underway, rather than defending the number.
- **CPLTD**, current portion of long-term debt, is **not held at all**. Sectors carries
  current liabilities and non-current liabilities, which do match other providers. Say it
  is not available rather than letting a customer hunt for it.

### 1.7 When is end-of-day data ready?

Verified from actual run records.

| Step | Time (WIB) |
|---|---|
| IDX market close | 16:00 |
| Daily price scrape runs | 18:00 |
| Prices available through the API | about 18:02 |
| Derived metrics refreshed: P/E, market cap change, company reports | 03:00 next day |
| Safe time to quote for the app front end | 05:00 next day |

**Quote 05:00 WIB the next morning**, for both the API and the app. The API is usually
ready by about 18:02 the same evening, but the front end should not be promised earlier
than 05:00, so one safe answer covers both surfaces.

For an API customer building an end-of-day pipeline, it is worth adding that data is
normally there the same evening, so a pipeline running 18:00 to 19:00 WIB will find
closing prices ready. Phrase that as normal behaviour, not a commitment.

There is **no intraday or real-time data**. Closing prices only. Say this early when a
customer describes a trading use case. Daily close endpoint:
<https://docs.sectors.app/api-references/v2/indonesia/transaction/daily>

There is also no delayed intraday feed. End of day is the only refresh.

**Internal note**: 05:00 WIB is the deliberate safe answer, not an error. The API is
typically refreshed by about 18:02 WIB the same day; the front end is quoted at 05:00 the
next morning to leave room for the overnight refresh.

### 1.8 Broker summary and foreign flow

Broker summary covers **every broker**, not a subset, one summary per broker per trading
day. Broker codes are the two-letter exchange member identifiers, and the full list
comes from the Broker Registry endpoint.

There is no lower-timeframe or intraday broker data.

Two endpoints, and customers often ask for the wrong one:

- **Broker Summary by Symbol**, top buyers and sellers for a stock over a date range,
  with broker-level buy, sell and net values:
  <https://docs.sectors.app/api-references/v2/indonesia/brokers/broker-summary-by-symbol>
- **Broker Activity by Code**, everything one broker traded over a range of up to 14
  days, grouped by date, optionally filtered to one symbol. Broker codes are the
  two-letter exchange member identifiers such as MG, AK, CC, and the valid list comes
  from the Broker Registry endpoint. Costs 1 credit:
  <https://docs.sectors.app/api-references/v2/indonesia/brokers/broker-activity-by-code>

Broker summary history starts **2025-01-02**. Foreign flow exists in two sets, so ask
which one the customer needs before quoting a start date:

- **From daily price data**: 2020-01-02 onward, and up to ten years on request.
- **From broker data**: 2025-01-02 onward, alongside the broker summary.

There is **no market-wide aggregate endpoint** for net foreign flow. A customer wanting
a whole-market daily figure has to sum per ticker, which across 900+ tickers is a real
credit cost. Flag that cost before they commit, and note that a market-wide endpoint has
been raised as a request.

### 1.8b Data we do not carry, and custom requests

**OHLCV** is available. It was added after an early enquiry that was told only daily
close existed, so anyone working from that older answer should be corrected.

**Not carried by default**: macro and rates data such as INDONIA, JIBOR, and credit
default swaps. These are not on the platform, but they have been sourced for clients
before as a custom arrangement. The reply that works: say it is not available by
default, then ask for the use case and the budget they have in mind, and pass it on.

**Product demos.** Prospects, especially developers and institutions, ask for a live
demo before integrating. These are accommodated, scheduled around the customer's
availability. Positioning lines that have been used: an API-first financial data
platform, 99.9% coverage of Indonesian listed companies, more than 40,000 monthly active
users, in use by financial institutions for research and product development.

### 1.9 Billing and cancellation

To cancel: profile icon, bottom left, then Account Settings, then Plan and Usage, then
the first card showing the Insider plan, then Cancel Subscription. Billing stops from
the next period.

Upgrading: <https://sectors.app/pricing>, register first, then Switch to Insider. The
account upgrades automatically once payment confirms.

### 1.10 Onboarding, workshops and recordings

Onboarding quest has a video walkthrough:
<https://www.youtube.com/watch?v=nEEn97JgT-8> Workshop recordings are under the
**Recordings** tab, not Past Workshops, which is where people get stuck. Paid
subscribers get the three most recent workshop recordings; Insider includes free access
to all workshops.

**Credits for evaluation.** A free account gets no API credits on registration.
Completing the onboarding quests unlocks a claim of **500 API credits**, which is the
standard answer for someone who wants to test first. The credits work on the free
account, so no upgrade is needed.
Requests for more testing credits, or for a custom price, are discussed internally and
answered case by case; tell the customer that is happening rather than refusing.

### 1.11 How are you different from other AI stock tools?

Asked against tools such as WarrenAI from Investing.com, ArtaAI from Mainsaham and
zeta-ai.pro. The reply that has been used: Sectors is the only financial data platform
with complete ownership data, every insider buy and sell, itemised balance sheets and
risk data for Indonesia-listed stocks, none of which exist on other platforms including
Bloomberg and Yahoo Finance. Point to <https://sectors.app/pricing> for the full feature
list.

Positioning used with a commercial prospect: Sectors is the data backbone for Indonesian
securities firms and provides an automation connector to Bursa Efek Indonesia. Do not
name specific clients in a reply without confirming the relationship is current and
can be disclosed.

Related resources that have been sent to prospects:

- **Sectors AI Chat**, conversational access to the data: <https://sectors.app/chat>
- **MCP integration guide**, for connecting AI agents and workflows:
  <https://docs.sectors.app/recipes/sectors-for-ai-agents/00-sectors-mcp-guide#what-is-mcp>
- **Recipes**, implementation guides and use cases: <https://docs.sectors.app/recipes/>
- **Sectors Workflow**, an automation tool that chains Sectors tools into trigger-based
  research and analysis flows: <https://sectors.app/workflow>, introduced in release
  3.4.0: <https://sectors.app/release/3.4.0>. Offer a call to map it to the customer's
  own analysis process.

### 1.12 Enterprise proof of concept

Reference from a past enquiry: a securities firm evaluating the News API for the news
feature in its trading app. PT Supertype Teknologi Nusantara sent a "Sectors News API
POC Testing and Integration Guide". Reuse its shape for the next institutional POC:

1. **Dedicated testing account** on a sectors.app email address, provisioned with
   **3,000 API credits**.
2. **Getting a key**: sign in at <https://sectors.app/>, open the Automation menu, then
   the Sectors API submenu, then API Management, then Create Key.
3. **Endpoint** for first tests: `https://api.sectors.app/v2/news/`, used to pull news,
   explore IDX metadata, and test pagination and parameters.
4. **Docs**, including the IDX extension parameters:
   <https://docs.sectors.app/api-references/v2/indonesia/news/news#idx-extension-parameters-extension-idx>
5. **Playground**: open the news page in the docs, click Try it, enter the key and any
   query parameters, and the response appears top right. Code samples are given in
   Python, JavaScript, PHP, Go, Java and Ruby.
6. **Support**: offer a walkthrough session, online or in person, plus technical
   discussion and live testing help. The guide recommends the walkthrough strongly.

POC scope to state: explore the API, test requests and responses, evaluate news
structure and metadata, try filtering and the IDX extensions, and assess integration
flows. Contact on the guide was Aulia S. Rosyad from the business team.

**Internal only**: the guide PDF contains the testing account password in plain text.
Never paste it into a ticket, chat or this document, and prefer sending credentials
separately from the guide next time.

---

## Part 2. Reading the data correctly

The definitions most likely to cause a dispute. Formulas are internal; the plain-English
meaning is what goes to a customer.

### 2.1 Three different dividend yields

Three numbers carry the name "yield" and they are not the same.

- **`yield_ttm`**: split-adjusted dividend per share over the trailing twelve months,
  divided by the latest close. This is the standard one.
- **`dividend_yield`** in the comprehensive report: trailing twelve-month dividend
  divided by close, preferring a calculated figure that drops any dividend paid before a
  recent stock split, because pre-split per-share amounts would overstate the total.
- **`forward_dividend_yield`** on the IDX dividend screener: **not forward-looking**. See
  below.

If a customer compares two of these and finds a difference, that is why.

### 2.2 "Forward Dividend Yield" on the IDX screener

**Open, known defect.** On the IDX dividend screener this column is labelled forward
but shows the yield of the most recent year present in the dividend history. If the
current year already has a dividend, it shows a partial year, which reads low against a
full year.

On SGX and KLSE the same label is backed by a genuine forward figure and is correct.

Until this is fixed: if an IDX customer queries it, acknowledge that the column is
backward-looking on IDX and give them `yield_ttm` instead.

### 2.3 Prices

Prices come directly from IDX, so adjustment follows the exchange's own reporting, which
is not always consistent. Sectors does not re-adjust.

Nothing is done to the close price for dividends. It is a price-only series, so the
price drops on the ex-date as normal. It is not a total-return series.

Dividends themselves are split-adjusted to the current share basis, with an unadjusted
original also held.

### 2.4 Currency

IDX customers always receive IDR for the company report. SGX figures are in SGD, KLSE in
MYR, with conversion applied where needed. Conversion rates refresh daily.

### 2.5 Nulls, and which ones are fine

A null usually is not a fault. Four causes, in the order to check them:

1. **Entitlement.** The customer's plan does not include that surface. Roughly 27 areas
   are gated, including broker profiles, foreign flow, historical revenue and cost,
   shareholder composition, filings, orderbook, news, screener tables, SGX ownership and
   property, and the watchlist.
2. **Not applicable by design.** For example quick ratio is deliberately null for banks,
   because current assets and current liabilities do not mean the same thing on a bank
   balance sheet. That null is correct.
3. **Genuinely absent upstream.** The company did not report it, or it was labelled in a
   way the pipeline could not match. Free float is the common case: it depends on the
   shareholder block being labelled exactly "Public", so a company labelling it
   differently yields a null rather than a wrong number.
4. **A broken pipeline.** Only this one is a fault.

Causes 1 to 3 can be explained on the spot. If none of them fit, escalate, because the
validation system should have caught it.

### 2.6 Why a historical number changed

Three reasons. Give as much detail as the customer's background warrants.

- The company restated its own figures.
- History was back-filled when previously missing data arrived.
- Derived metrics are recomputed overnight, so a metric built on updated inputs moves.

---

## Part 3. Freshness, and when something is actually late

**Do not publish these timings as a commitment.** There is no freshness SLA and none is
planned. This table is internal. Work to the tight column so you know when something is
genuinely late; quote the moderate column to customers.

| Dataset | Tight, internal | Moderate, quote this |
|---|---|---|
| IDX daily price, API | 18:15 WIB same day | 05:00 WIB next day |
| IDX daily price, app front end | 05:00 WIB next day | 05:00 WIB next day |
| Derived metrics: P/E, market cap change, reports | 04:00 WIB next day | 09:00 WIB next day |
| Financial statements | 2 business days after IDX publication | 5 business days |
| News | 8 hours | 24 hours |
| Filings, insider transactions | 4 hours | 24 hours |
| Broker summary, foreign flow | next morning | 2 business days |
| ESG, analyst ratings, forecasts | no threshold, updated manually or monthly | no threshold |

Update cadences behind those numbers: prices daily at 18:00 WIB on trading days; news
every four hours; filings every two hours; index membership monthly on the 1st; KSEI
ownership at the start of each month for the previous month-end register; analyst
ratings monthly; ESG and growth forecasts manually.

Note that index membership refreshing monthly means a change can take up to a month to
appear. That is expected behaviour, not a fault.

---

## Part 4. When data is wrong or missing

### 4.1 A company is missing

Narrow it down in this order:

1. **Delisted.** Delisted companies are tracked separately.
2. **Suspended.** Suspensions are recorded and the company remains listed.
3. **Outside coverage.** An SGX counter outside the top 200 has a thinner profile by
   design. For IDX, coverage is 99.9%, so a missing IDX company is rare and worth
   escalating if causes 1, 2 and 4 do not fit.
4. **Entitlement.** See the gated list in 2.5.

### 4.2 A number is wrong

There is no proactive corrections process. If a customer finds an error, the reply
should state the pipeline fix made and the guard added to prevent recurrence. That is
the precedent set in past replies and it works well.

Turnaround after an issue is raised: a new data point or a correction normally within
**two working days**; a fix to a single field inside an existing data point normally
within **one day**. These are normal turnarounds, not guarantees, so phrase them as
"normally" rather than "we will".

**Internal only**: data quality is monitored by an automated validation system that
checks the IDX tables and emails engineering on anomalies. Results are logged and other
teams can read them. There is **no per-number validated marker**, so never tell a
customer that a specific figure was validated. When validation catches something, the
bad value is flagged and replaced from a fallback source; it is not blocked before
publishing, so a wrong value can be briefly visible.

### 4.3 Can I get data you do not have?

Yes, ask. Requests are assessed against what is already planned, and if it is not
planned the team considers whether to add it. Not a flat no, not a commitment.

### 4.4 Can I get an uptime guarantee?

On the standard plans there is no contractual uptime number. What is true: database
failures return a 503 with `code: service_unavailable` and clients should retry; failed
calls cost no credits; rate limits return 429 and are documented per endpoint.

Enterprise is different. It carries a custom SLA with higher rate limits, **99% uptime**
and response time guarantees, plus unlimited end users, white-glove onboarding, direct
and local billing, and 50+ dedicated development hours.

---

## Part 5. Accounts and privacy

### 5.1 Account deletion by request

The customer writes in. Deletion completes within 48 hours of the team's reply.

### 5.2 Automatic deletion of inactive signups

Only affects accounts that signed up by email, never activated, and did not respond to
**three activation reminders** sent at spaced intervals. Google and GitHub signups are
never affected, and neither is any activated account.

If a customer asks why their account disappeared, this is almost always why, and it is
worth saying plainly that three reminders were sent first.

### 5.3 What is deleted

API keys, usage logs and related records are removed with the account.

**Open**: whether saved workflows and screeners are removed along with the account.
Treat them as deleted, and confirm with engineering before telling a customer anything
more definite.

---

## Part 6. What the product actually runs on

Not for customers. Useful when a customer reports something and you need to judge
whether it is plausible.

Data flows through scheduled pipelines running in five different places: GitHub Actions,
scheduled jobs inside the database, a validation service on fly.io, the news pipelines
on Google Cloud Run, and an external scheduler that triggers the daily price scrape. The
watchlist notification runs on Vercel. The practical consequence: **no single place
shows every schedule**, so "when does X update" is answered from the freshness table
above, never from memory.

Currently not running, so do not describe these as live features: mutual fund data, the
fear and greed index, price anomaly as a standalone product surface, US market data, and
weekly Instagram highlights.

Analyst ratings were frozen between May and September 2026 and now run monthly again
after a source change. If a customer noticed a gap in that window, it was real.

**Internal only, never state publicly**: the identity of upstream data providers.
Externally, the answer is that Sectors uses publicly available data sources, standardized
and verified in house. This applies to every third-party source without exception.

---

## Part 7. Escalation

Anything the business team cannot answer goes to engineering. There is no separate
product owner and no per-dataset owner; engineering also makes product design and
planning decisions, so there is no routing judgment to make.

Escalate immediately, rather than answering, when:

- The question is about redistribution rights, local storage rights, or licensing from
  an exchange.
- A prospect asks for a bulk historical extraction or a custom dataset the platform does
  not carry.
- A customer reports a number that does not fit any of the four null causes in 2.5.
- Data is later than the tight column in Part 3.
- The question touches an item marked **Open** in this document.

Open items as of 2026-09-16:

| Item | Owner | Section |
|---|---|---|
| Permanent local storage of data for personal quantitative research | Legal | 1.5b |
| Whether a bulk or snapshot historical extraction option exists | Product | 1.5b |
| EBITDA fix, taking reported figures where available, current status | Engineering | 1.6 |
| Where ten years of price history lives, given the table starts 2020 | Engineering | 1.3 |
| IDX Data Services redistribution rights, downstream limits, attribution, user-count reporting | Legal | 1.5 |
| Storing or caching data where no written permission exists | Legal | 1.5b |
| Terms of Service conflict on internal business use | Legal or product | 1.5 |
| Whether workflows and screeners cascade on account deletion | Engineering | 5.3 |
| "Forward Dividend Yield" label and partial-year value on IDX | Product | 2.2 |
| Confirm the delisted-company lookup before citing it by name | Engineering | 4.1 |

---

## Part 8. Links to send customers

Point people at the documentation rather than retyping an answer. Every link below has
already been used in a real reply.

### Documentation and product

| What it answers | Link |
|---|---|
| API documentation, home | <https://docs.sectors.app/> |
| Getting started, what data exists and what it supports | <https://docs.sectors.app/get-started/v2/overview> |
| API playground, try a response before subscribing | <https://sectors.app/api> |
| Pricing and plan comparison | <https://sectors.app/pricing> |
| Onboarding quest walkthrough, video | <https://www.youtube.com/watch?v=nEEn97JgT-8> |
| Enterprise plan | <https://sectors.app/enterprise> |
| Data operations, sources and processing | <https://sectors.app/data-operations> |
| Sectors AI Chat | <https://sectors.app/chat> |
| Sectors Workflow | <https://sectors.app/workflow> |
| MCP guide for AI agents | <https://docs.sectors.app/recipes/sectors-for-ai-agents/00-sectors-mcp-guide> |
| Recipes | <https://docs.sectors.app/recipes/> |

### Endpoints customers ask for by name

| Data | Link |
|---|---|
| Daily close price | <https://docs.sectors.app/api-references/v2/indonesia/transaction/daily> |
| News, with IDX extension parameters | <https://docs.sectors.app/api-references/v2/indonesia/news/news> |
| Broker summary by symbol, top buyers and sellers per stock | <https://docs.sectors.app/api-references/v2/indonesia/brokers/broker-summary-by-symbol> |
| Broker activity by code, one broker across stocks, up to 14 days | <https://docs.sectors.app/api-references/v2/indonesia/brokers/broker-activity-by-code> |
| Insider trading filings | `https://api.sectors.app/v1/filings/` |
| Institutional buy and sell, monthly, inside the company report | `https://api.sectors.app/v1/company/report/{ticker}/?sections=overview,financials` |

The last two are live API endpoints rather than documentation pages, so send them with a
line of explanation rather than on their own.

### Where to find things in the app

- **Workshop recordings**: the Recordings tab, not Past Workshops. This is the single
  most common "I cannot find it" ticket.
- **Cancel a subscription**: profile icon bottom left, Account Settings, Plan and Usage,
  first card, Cancel Subscription.

---

## Sources

Every figure here traces to one of: the answered question set in
`questions-round-1.md`; the database, queried 2026-09-14 through the approved query
`market-coverage-counts`; pipeline run records read from the repositories on 2026-09-14
and 2026-09-16; the app source for plan entitlements and screener behaviour; the public
Terms of Service, pricing and data operations pages; support transcripts; a second batch
of sales and licensing email threads (`More QnA`, added 2026-09-17); and the News API POC
guide prepared for a past institutional enquiry.

When this document and the product disagree, the product is right and this document
needs updating. Flag it rather than working around it.
