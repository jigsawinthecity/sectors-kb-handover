# Knowledge base, open questions

Prepared 2026-09-12, after a read-only sweep of the `supertypeai` GitHub org, the `supabase-backend` repo, the live `cron.job` catalog, and the Next.js app.

Everything answerable from code has been answered and is written up in `pipeline-inventory.md`, `sectors-kb-updates.md` and `terms.md`. What follows is everything that is **not** recoverable from code and still blocks a business-facing knowledge base.

## How to use this

Each question carries a suggested owner and a reason it matters. Answer what you can inline. Leave the rest, and I will split the unanswered ones into a separate engineering-only file to send on.

- **[BUS]** business or product call, no code involved
- **[ENG]** needs someone who knows the pipeline or the SQL
- **[EITHER]** either could answer

Short answers are fine. "Don't know, ask X" is a useful answer, it tells me who to route to.

Target: the business team answers customers **directly**, so every answer here eventually becomes language someone pastes into a reply.

---

# Part A. Pipeline verdicts

Thirteen pipelines are stale, never-run, or persistently failing. A knowledge base that documents a pipeline implies it works, so each needs a verdict before it appears.

For each: **live**, **known-broken** (running but output is stale, and we accept that for now), **paused**, or **retired**?

## A1. Failing while still scheduled

1. **[ENG]** `sectors_analyst_rating_scraper`, 90 failures in its last 95 scheduled runs, last success May 2026. Are analyst ratings currently frozen in the product? If a customer views analyst ratings today, what are they seeing, and how old is it?

   > **ANSWERED [BUS/ENG]**: Reported fixed 2026-09-01, source switched from TradingView to Stockbit. **UNVERIFIED**: no commit or successful run in `sectors_analyst_rating_scraper` since 2026-05-07 / 2026-05-02, repo still scrapes TradingView. Need the repo where the fix landed. KB note: never name the upstream provider in customer-facing copy.
   >
   > **RESOLVED 2026-09-16**: the source was changed to Stockbit and the pipeline still runs monthly. Reason for the move: the TradingView path used Selenium and broke whenever TradingView changed its page structure, while Stockbit exposes an API. Customer-facing copy must not name either provider (Q52).
2. **[ENG]** `idx_sheets_financial_data_extraction`, last run 2026-08-30 failed. This feeds `idx_financial_sheets_quarterly` and `idx_financial_sheets_annual`. Is quarterly financial statement data currently behind?

   > **ANSWERED**: Not behind. No new quarterly report was released last quarter, so nothing to ingest. Verified: batches 1-4 all succeeded 20-23 Aug 2026. Caveat: all four batches failed 27-30 Aug and have not run since, so the pipeline is red going into next quarter-end.
3. **[ENG]** `sectors_idx_highlights`, most recent run failed. This generates the weekly Instagram highlights. Customer-visible or internal only?

   > **ANSWERED**: Retired. Internal social media generation only, no longer in use. Exclude entirely from the customer-facing KB.

## A2. Stale, no scheduled run since June 2026

4. **[ENG]** `mutual_fund_pipeline`, last ran 2026-03-20, both recorded runs failed. Is Indonesian mutual fund data a live product surface or an abandoned experiment?

   > **ANSWERED**: Not in use at the moment. Treat as not a live product surface.
5. **[ENG]** `sectors_forecast_growth_rate`, last ran 2025-10-01. The doc says it is run manually each quarter with the Action as a reminder only. Is that still the process, and was it run for the most recent quarters?

   > **ANSWERED**: Run annually and manually, not quarterly. The current doc's "manual each quarter" wording is wrong and should be corrected.
6. **[ENG]** `sectors_price_anomaly_updater`, last ran 2026-04-27. Is price anomaly detection still a feature?

   > **ANSWERED**: Migrated to `sectors_guard_validator`. Verified: that repo is a FastAPI service deployed on fly.io (`Procfile`, `fly.toml`) with `app/validators` and daily/quarterly/annual/dividend/filings/stocksplit/idxic checks plus email notification. The old `sectors_price_anomaly_updater` repo is retired but still linked from `sectors-kb` pipeline docs, remove it there. OPEN: guard alerts internally, so whether a customer-facing price anomaly feature still exists is unconfirmed.
7. **[ENG]** `sectors_idx_fear_and_greed_index`, last ran 2026-02-02. Is the fear and greed index still published? Customers may still see a stale number.

   > **ANSWERED**: Data is not used anywhere. Retired, exclude from the KB. Confirm nothing stale still renders in the app.
8. **[ENG]** `sectors_get_esg_score`, last ran 2026-06-01. How current is ESG data, and what should we tell a customer who asks when it was last updated?

   > **ANSWERED**: Sourced from `sustainability.idx.co.id/api`, originating from Morningstar. Currently run manually, so there is no fixed cadence to promise. See Q53 on whether Morningstar may be named publicly.

## A3. Cron defined, never triggered

9. **[ENG]** `sectors_us_cron`, `sectors_ic_data_updater`, `sectors_get_etf_data`, `sectors_dcf_calculation` all carry a `cron:` that has never produced a scheduled run. Dead code, or scheduled somewhere else?

   > **ANSWERED**: Not running. Covered by the US answer in Q10, treat these four as not scheduled and not live.
10. **[ENG]** Related: is US market data a current product surface at all? Several US repos exist (`sectors_us_insider_trading` is live and running, `sectors_us_cron` and `sectors_us_profile_updater` are not). What do we tell a customer who asks whether you cover US equities?

   > **ANSWERED**: No US coverage at this time. The KB should say Sectors does not currently cover US equities. `sectors_us_insider_trading` still runs but is not a stated product surface, confirm whether its output is exposed anywhere.

## A4. Ownership

11. **[EITHER]** Who owns each live pipeline day to day? The KB should name an escalation target per dataset, not "engineering".

   > **ANSWERED**: No per-dataset owner. All escalation goes to the engineering team. KB names one target, engineering, for every dataset.
12. **[ENG]** When a pipeline fails, who is alerted and how? Is anyone watching, or was the 90-failure streak on analyst ratings genuinely unnoticed?

   > **ANSWERED**: Alerts go to the engineering team. Other teams can check results themselves in the Supabase `validation_results` table. Verified: that table is written by `sectors_guard_validator` (`app/database/models.py`, `app/api/routes.py`) and its schema is declared in `sectors_guard`. This is the record a business person can cite for Q43.
13. **[BUS]** When a pipeline is broken, is there an existing process for telling customers, or does it only surface reactively when someone complains?

   > **ANSWERED**: No customer notification process exists. On alert the team works the fix and pushes the update live. KB implication: staleness is communicated reactively, so the team should not promise proactive notice.

---

# Part B. Definitional gaps

These block accurate answers about what a number means. Mostly engineering.

## B1. Flagged in `terms.md`

14. **[ENG]** Quick ratio is forced to `NULL` for `sub_sector_id = 19`, with no comment in the SQL. Which sub-sector is 19, and why the exclusion? Is it still wanted?

   > **ANSWERED**: `sub_sector_id = 19` is Banks. Quick ratio is forced NULL because standard current assets and current liabilities do not apply to a bank balance sheet. Still wanted. KB wording: a null quick ratio on a bank is correct and expected, not missing data.
15. **[ENG]** `calculated_dividend_ttm` versus `dividend_ttm`: the comprehensive report prefers the first and falls back to the second. What is the difference, how is `calculated_dividend_ttm` derived, and which should be quoted to a customer?

   > **ANSWERED**: `dividend_ttm` is total dividend paid per share over the trailing twelve months, in IDR. `calculated_dividend_ttm` is computed in the `public.idx_comprehensive_report` matview, CTE `safe_dividend_ttm`: sum of raw per-share cash dividends from `idx_dividend` with ex-date in the last 365 days, left joined to `idx_stock_split`. The 'safe' part: if a split occurred in the last year, every dividend paid before the split date is dropped, because `dividend_original` is pre-split per share and would otherwise overstate the total. The comprehensive report prefers `calculated_dividend_ttm` and falls back to `dividend_ttm`, so quote the calculated one.
16. **[ENG]** Where does `forward_eps` come from? Which provider or model, and what vintage? A customer asking "whose forecast is this" currently has no answer.

   > **ANSWERED**: `forward_eps` sits in `idx_key_stats` and is joined into `idx_calc_metrics_daily` as `iks.forward_eps`. Written by `sectors_yf_data_updater` (`yfdataupdater.py`, `metric_dict = {"forwardEps": "forward_eps"}`) from Yahoo Finance. It is a third-party consensus figure, not a Sectors model, and its vintage is whatever Yahoo held at the last run. See Q52 on whether Yahoo Finance may be named publicly.
17. **[BUS]** The app's dividend screener labels a column "Forward Dividend Yield" but computes it from the most recent **completed** year in `historical_dividends`. Is the label intentional? If yes I document it with a caveat. If no, it is a product fix and the KB should not enshrine it.

   > **LOCATED, still needs a product call**: Next.js app, `src/app/(screeners)/indonesia/top-dividend-stocks-indonesia/page.tsx` lines 76 to 90, label defined in `screenerMobile.ts`. It reads `total_yield` for the most recent completed year from the `historical_dividends` JSONB, so the value is backward looking. Label fix or documented caveat?
   >
   > **LOCATED EXACTLY**: computed in `sectors/src/app/(screeners)/indonesia/top-dividend-stocks-indonesia/page.tsx` lines 70-98, which reads `company.historical_dividends`, takes `Math.max(...Object.keys(annual_yield))` and assigns that year's `total_yield` to `forward_dividend_yield`. Labelled in `(screeners)/indonesia/top-dividend-stocks-indonesia/DivColumns.tsx:16` and `(screeners)/components/screenerMobile.ts:21`. Source data is the `historical_dividends` JSONB built from `idx_dividend`. CORRECTION to the earlier note: it is the highest year key present, not the last *completed* year, so if the current year already has a dividend the column shows a partial-year yield that reads low against a full year. IDX therefore has two defects, a wrong label and a partial-year value. SGX and KLSE carry genuine `forward_dividend_yield` columns in `sgx_companies` and `klse_companies` and use the same label correctly, which is likely why this went unnoticed.
18. **[ENG]** `pb_mrq` divides market cap by book value with no zero guard, while `pe_ttm`, `ps_ttm` and `pcf_ttm` all guard. Deliberate or oversight?

   > **LOCATED, still needs a decision**: all guards are in the final SELECT of `idx_calc_metrics_daily`. `pe_ttm` uses `CASE WHEN lfi.earnings_ttm <> 0 ... ELSE NULL`, `ps_ttm` and `pcf_ttm` the same shape. `pb_mrq` is a bare division with no CASE. Suggested fix: `CASE WHEN lfi.bve_mrq IS NOT NULL AND lfi.bve_mrq <> 0 THEN lm_1.market_cap::double precision / lfi.bve_mrq::double precision ELSE NULL::double precision END AS pb_mrq`. Separate product call: negative book value currently yields a negative P/B that sorts below healthy companies ascending, use `> 0` if those should be null too.
19. **[ENG]** `enterprise_to_ebitda` returns `0` rather than `NULL` when EBITDA is zero, which puts loss-making companies at the top of an ascending screener sort. Intended?

   > **ENGINEERING DECISION, flag with options**: keep `0` and document as a quirk, or change to `NULL` to match `pe_ttm`/`ps_ttm`/`pcf_ttm`. If NULL: one-line change in the `idx_calc_metrics_daily` definition plus a refresh, and ascending screener sorts stop putting loss-makers first. Recommendation: NULL, since 0 is indistinguishable from a genuinely cheap company.

   > **LOCATED, still needs a decision**: in SQL, final SELECT of `idx_calc_metrics_daily`: `CASE WHEN icmq.ebitda_ttm <> 0 THEN (lm.market_cap + icmq.ev_not_with_mcap)::numeric / icmq.ebitda_ttm ELSE 0::numeric END`. Only metric in the block whose fallback is 0 rather than NULL, so it reads as a typo against its neighbours. Confirm intent before the KB documents it.

   >
   > **CLOSED 2026-09-16**: engineering has actioned it.
## B2. Market cap, the biggest one

20. **[ENG]** Is the three-method market cap approach (`mcap_method` 1, 2, 3) documented anywhere customer-facing? This is the most likely source of "your numbers disagree with Bloomberg" and right now the business team has no sanctioned explanation.

   > **METHODS, from `sectors_yf_data_updater/yfdataupdater.py` lines 503-533, recorded per row in `idx_daily_data.mcap_method`**: (1) taken directly from Yahoo Finance `marketCap` for the latest date; (2) back-filled for earlier dates as `close x (marketCap / latest close)`, an implied constant share count; (3) back-filled as `close x share count held in the database`, when the API returns nothing. Method 2 is the disagreement source: it holds one share count across history. RESOLVED: multiple methods exist as fallbacks, leave as is. KB explains the fallback chain, not a defect.
21. **[ENG]** Is `mcap_method` exposed through the API, or internal only? If exposed, the KB should tell the business team how to read it.

   > **BLOCKED on Q20**: `mcap_method` is a column on `idx_daily_data`. Confirm whether it is returned by any API endpoint or stays internal.
   >
   > **ANSWERED**: `mcap_method` is not exposed through the API. Internal only, so the business team can use it to explain a discrepancy but must not tell a customer to read it.
22. **[BUS]** What is the sanctioned sentence for "your historical market cap doesn't match another provider"? I can draft it, but the claim needs your sign-off before it goes in front of customers.

   > **APPROVED STANCE**: market cap is gathered from official sources; where unavailable it is calculated by method 2 (`close x implied share count`). Draft line for the KB: "Market capitalisation is taken from official sources where available. Where it is not, we calculate it from the closing price and the most recent available share count, which can differ from providers who use the share count in force on each historical date."

## B3. Prices and adjustments

23. **[ENG]** Are prices in `idx_daily_data` split-adjusted, unadjusted, or mixed? `idx_stock_split_cumulative` exists and `dps_split` implies split adjustment for dividends, but the close price basis is not stated anywhere.

   > **ANSWERED**: Mixed. Prices come directly from IDX, so adjustment follows IDX's own reporting, which is inconsistent. KB wording: Sectors passes through the exchange's price basis and does not re-adjust, so a split-era series may be partly adjusted.
24. **[ENG]** Are prices dividend-adjusted (total return) or price-only?

   > **ANSWERED**: Nothing is done to the close price. Price-only series, not total return, so the price drops on the ex-date as normal.
25. **[ENG]** What does `dps_split` mean exactly, dividend per share adjusted for splits up to when, the payment date or today?

   > **LOCATED, needs confirming**: `dps_split` is not a table column, it appears in the migration SQL for `idx_company_report` in `yield_ttm = sum(dps_split) / latest_close`. It corresponds to `idx_dividend.dividend`, documented as auto-adjusted for stock splits, against `dividend_original` which is unadjusted. Reading: adjusted to today's share basis, not the basis at payment date. CONFIRMED as documented. To verify empirically: pick a symbol with a row in `idx_stock_split`, compare `dividend` against `dividend_original` in `idx_dividend` for dividends dated before that split; if `dividend` is scaled by the split ratio and `dividend_original` is not, the reading holds. Runs through the `sectors-dbquery` approved-query flow.
26. **[ENG]** How far back does daily price history go per market (IDX, SGX, KLSE)? A customer asking "how much history do I get" needs a number.

   > **ANSWERED**: Database holds about 10 years, earliest date 2020-01-02. The API caps the window: default last 30 days, maximum 90 days per request. KB must state both, customers hit the API cap, not the history depth.
27. **[ENG]** What is in `historical_dividends` JSONB, how far back, and what populates it?

   > **ANSWERED**: Earliest date in `idx_dividend` is 2020-01-03. Populated from `idx_dividend`, structure per year `{year, total_dividend, total_yield, breakdown:[{date,total,yield}]}` (`sectors-kb/database_documentation.md`).

## B4. Currency

28. **[ENG]** `yf_currency` exists on the company profile. Are all IDX figures in IDR, or do some companies report in USD? What does the customer receive?

   > **ANSWERED**: All clients receive IDR for the IDX company report. `yf_currency` does not change what the customer gets for IDX.
29. **[ENG]** `sectors_get_conversion_rate` writes a USD to IDR rate into a JSON file in the repo, not a table. Where is that rate actually consumed, and how stale can it be?

   > **ANSWERED**: `main.py` calls `api.exchangerate-api.com`, writes `conversion_rate.json` into the repo, runs daily 01:00 WIB (`cron: '0 18 * * *'`), last updated 2026-09-13. Consumed by raw URL, not a table: `sectors_sg_my_data_updater/sg_my_scraper.py` reads the local file and falls back to `raw.githubusercontent.com/.../master/conversion_rate.json`; also referenced by `neubidics/src/lib/money.ts` and `sectors_newsletter/src/lib/utils.tsx`. The file carries a `datetime` field, so staleness is checkable. Pairs covered: USD to IDR/SGD/MYR, plus AUD/EUR/GBP/HKD/CNY/IDR/MYR/PHP/THB/TWD into SGD.
30. **[ENG]** For SGX and KLSE, what currency are financials and market cap in, and is any conversion applied?

   > **ANSWERED**: SGX reported in SGD, KLSE in MYR, conversion applied accordingly using the rates above (`sg_my_scraper.py`, `financial_data_yf.py`).

## B5. Classification

31. **[ENG]** Where does the sector and sub-sector taxonomy come from, IDX's own or a Sectors-defined mapping?

   > **ANSWERED**: Official IDX-IC (Indonesia Stock Exchange) sector classification, not a Sectors-defined mapping.
32. **[ENG]** Is the taxonomy versioned? If a company gets reclassified, does history move with it?

   > **NO ANSWER YET**: no reclassification has happened so far, so there is no established behaviour to document. Revisit when IDX-IC first moves a company.

   > **RESTATED**: IDX-IC reclassifies companies over time. If a company moves from sub-sector A to B, does its historical data stay under A as of that date, or move to B with the company? Determines whether a historical sector aggregate is reproducible or shifts retroactively.
33. **[ENG]** How many sub-sectors are there, and is the list stable enough to publish?

   > **ANSWERED**: 33 sub-sectors, list is stable and publishable.
34. **[ENG]** Index membership comes from IDX zip downloads via `sectors_indices_company_list`. Which indices are covered, and how quickly does a membership change appear?

   > **ANSWERED, with corrections**: `index_name.csv` covers **18** indices, not sixteen: LQ45, IDX30, IDXHIDIV20, IDXBUMN20, IDXV30, IDXG30, IDXQ30, IDXESGL, KOMPAS100, SRI-KEHATI, SMinfra18, JII70, FTSE Indonesia, IHSG, STI, KLSE, ECONOMIC30, IDXVESTA28. Cadence is **monthly**, not quarterly: membership list `cron: '0 0 1 * *'` (1st of month), index daily price weekdays 18:00 WIB. So a membership change can take up to a month to appear.

## B6. Coverage, per dataset

For each of these the KB needs "what is covered, from when, and how often it updates". Code gave me the schedule; it did not give me coverage or history depth.

35. **[ENG]** IDX: how many companies, and does coverage include all boards (Main, Development, Acceleration)?

   > **ANSWERED, VERIFIED 2026-09-14** via approved query `market-coverage-counts`: 962 active companies (delisting_date is null). Board split: Main 260, Development 457, Acceleration 44, New Economy 3, Watchlist 198. The KB should say whether Watchlist-board companies count as covered.
36. **[ENG]** SGX: how many counters, and does it include REITs, trusts and secondary listings?

   > **CORRECTED, VERIFIED 2026-09-14** via `market-coverage-counts`: **618** active counters (`is_active IS NOT FALSE`), not 576, of which 40 are suspended. The 180-with-detailed-reports figure is still unverified and needs its own definition before publishing; a customer on one of the other counters sees a thin profile by design, not a data gap.
37. **[ENG]** KLSE: how many companies, and is coverage comparable to SGX or partial?

   > **ANSWERED, VERIFIED 2026-09-14** via `market-coverage-counts`: 1005 companies. Note `klse_companies` carries no active or delisting flag, so this is every row, delisted names included if any exist.
38. **[ENG]** Mining (`coalresearch`): which commodities, how many companies or sites, and what is the update cadence in practice? This one lives in SQLite on a separate app, so it may behave differently from everything else.

   > **ANSWERED, with correction**: `coalresearch/constants.py` `COMMODITY_MAP` covers **six** commodities, not four: Coal, Nickel, Gold, Copper, Tin, Bauxite (mapped from Indonesian source terms such as BATUBARA, BIJIH NIKEL, BAUKSIT). Company and site counts still need a read of `db.sqlite`.
39. **[ENG]** KSEI 1 percent ownership: the pipeline reads a monthly Excel export. When in the month does new data actually land, and how far behind the register date is it?

   > **ANSWERED**: March is the first month held. New data lands at the beginning of each month, covering the previous month-end register. So the register is roughly one month behind at best.
40. **[ENG]** Broker summary: how far back, and is it every broker or a subset?

   > **ANSWERED, corrected 2026-09-16**: broker summary history starts **2025-01-02**, not 2026-01-01, and covers every broker, not a subset. Foreign flow is deeper, **2020-01-02 onward**, since it is carried on the daily price data rather than the broker tables.

   > **BUSINESS ANSWER 2026-09-15, CONFLICTS with the above**: business states the daily net foreign inflow series "spans from the beginning of 2025", and that extending the depth is in progress. The earlier line, read from the data, gives 2026-01-01 as the first date in `idx_broker_summary_daily`, which is the table the foreign flow views are built on. One of these is wrong, or they measure different things (the API's foreign-flow endpoint may be served from a separate store rather than from `idx_broker_summary_daily`). Do not quote a start year to a customer until this is settled. Resolve by reading `min(date)` from `idx_broker_summary_daily` and from whatever the foreign-flow endpoint actually reads, through the `sectors-dbquery` approved-query flow.
   >
   > **ANSWERED**: every broker, not a subset. Depths corrected: broker summary from 2025-01-02, foreign flow from 2020-01-02. NOTE: a customer was told foreign flow begins at the start of 2025, which understates the real depth by five years. Worth correcting if that customer returns.
41. **[ENG]** Filings: what filing types are covered, and what is the lag from IDX publication to appearing in the product?

   > **ANSWERED**: Filings are insider trading transactions filed with the authorities, IDX and SGX. Cadence verified: `sectors_idx_filing_pipeline` runs `cron: "0 */2 * * *"`, every two hours, plus a monthly company-map refresh. Worst-case lag is about two hours after IDX publishes.
42. **[ENG]** News: which sources, and is it all Indonesian-language, English, or both?

   > **ANSWERED**: Both Indonesian and English sources, all translated into English on the way in. Customers receive English.

## B7. Data quality

43. **[ENG]** `sectors_guard_validator` validates several IDX tables and emails on anomalies. Who receives those emails, and is there a record a business person can check before answering "has this number been validated"?

   > **ANSWERED**: Internal only, for spotting anomalies and outrageous numbers. There is no per-number 'validated' marker anywhere, so the team must never tell a customer a specific figure was validated. `validation_results` (1,548 rows) is a run log, not a certificate.
44. **[ENG]** What happens when validation fails, is bad data blocked from publishing or flagged after the fact?

   > **ANSWERED**: Bad data is flagged, then replaced using a fallback data source. It is not blocked before publishing, so a wrong value can be briefly visible before the fallback lands.
45. **[BUS]** Is there a corrections policy? If a number was wrong and gets fixed, are customers told?

   > **ANSWERED**: No proactive corrections policy. If a customer finds it, the team responds with the pipeline amendment made and the guard put in place to prevent recurrence. KB wording should promise a response, not proactive notice.

---

# Part C. Customer-facing policy

Only business or product can answer these. They shape what the team is allowed to say.

## C1. Commitments

46. **[BUS]** Is there a stated data freshness SLA, or is freshness best-effort?

   > **ANSWERED, do not publish**: internal reference only, for the business team to answer front-facing questions. State the LATEST observed timing (the worst case, not the fastest), so the team never promises better than the pipeline delivers. Definition kept for reference: a freshness SLA is a published promise about data age, e.g. 'IDX closing prices available by 19:00 WIB each trading day' or 'financial statements within two business days of IDX publication'. It gives the KB a line past which staleness is a fault rather than normal. None exists today and none will be published.
47. **[BUS]** Is there an uptime commitment on the API?

   > **ANSWERED, partially**: No contractual uptime number in the public API terms. Posture: 503 with `code: service_unavailable` on database failures (unreachable DB, pgbouncer reaping) with explicit 'clients should retry' guidance (changelog 2026-07-31); failed calls cost nothing (400, 401/403, 429, 5xx are free, only 2xx and 404 bill credits); 429 documented on every endpoint. CAUTION: the enterprise deck already commits to **99% uptime** under Custom SLA, so a number is in front of customers regardless. Recommendation: do not publish a second figure unmeasured; `api_apiresponsetime` holds 987k rows, so a real 90-day availability figure is computable before deciding.
48. **[BUS]** What can the team promise about a fix timeline when data is wrong or missing?

   > **ANSWERED**: After an issue is raised, a new data point or a correction is normally delivered within 2 working days; a fix to one field inside an existing data point, within 1 day. KB should state these as normal turnaround, not a guarantee.
49. **[BUS]** Are there contractual differences by plan, for example enterprise customers with different guarantees?

   > **ANSWERED**: Enterprise plan (`Indonesia's Financial Database.pdf`, sectors.app/enterprise) includes commercial usage licensing, a Custom SLA with higher rate limits, 99% uptime and response time guarantees, unlimited end users with no seat limits, white-glove onboarding, direct billing with local ID/SG transfers, and 50+ dev hours. Public claims the KB must stay consistent with: 99.9% of Indonesian listed companies indexed, 100% IDX sector coverage, 67,900+ data points processed daily, near-complete IDX and SGX coverage refreshed daily, 40,000 workflows monthly.

## C2. Licensing and redistribution

50. **[BUS]** What are customers allowed to do with the data, redistribute, resell, display publicly, train models on it?

   > **ANSWERED from the Terms of Service** (`sectors/src/app/terms-of-service/page.tsx`): Content is provided AS IS for personal, non-commercial use or internal business use. No copying, reproducing, aggregating, republishing, public display, translation, transmission, distribution, sale or licensing for any commercial purpose without prior written permission. Commercial use requires a Sectors for Enterprise agreement. **CONFLICT TO RESOLVE**: the IP section permits internal business use, the 'Personal and Non-Commercial Use' section says any business activity is commercial and prohibited without Enterprise. Most API customers are companies, so support will hit this. Needs a legal or product ruling. Model training is not addressed anywhere in the ToS.
   >
   > **PARTIAL ANSWER**: a startup programme exists, and customers in that position should be routed to it. The ToS conflict itself is still unresolved, and the transcripts show a real enquiry of exactly this shape, a paid retail product asking whether it may display Sectors data to its own subscribers, plus whether Sectors holds IDX Data Services redistribution rights and whether attribution or user-count reporting is owed. That last part is unanswered anywhere.
51. **[BUS]** Are there source-imposed restrictions to pass on? Data comes from IDX, SGX, Bursa, KSEI, Yahoo Finance, Morningstar via RapidAPI, Sustainalytics, ESDM and others, several of which have their own terms.

   > **CLARIFIED and ANSWERED**: the question was whether an upstream provider's own licence restricts what Sectors may pass on (for example IDX or Morningstar terms forbidding redistribution even by a licensee). Customer-facing line: sources are publicly available data. Checking each upstream licence is a legal task, not a KB one.
52. **[BUS]** Is Yahoo Finance as an upstream source something we state publicly, or is it internal? It underpins prices, market cap and several financial fields, so the answer affects how the team explains provenance.

   > **ANSWERED**: Internal only. The business team may know Yahoo Finance is upstream; it must never appear in customer-facing wording. Externally: publicly available data sources.
53. **[BUS]** Same question for Morningstar, Sustainalytics and sahamidx.

   > **ANSWERED**: Same as Q52, internal only, for Morningstar, Sustainalytics and sahamidx.

## C3. Plans, limits, access

54. **[BUS]** API rate limits and credit costs per endpoint, where is the current source of truth?

   > **ANSWERED**: 1 API credit per structured query, 3 credits for a natural-language query via the `?q=` parameter. Credits and billing detail live in `sectors/src/app/faq/CreditsBillingFAQ.tsx` (monthly allowance, reset timing, top-ups, expiry, consumption order), which is the current source of truth for the team.

   > **PARTIALLY ANSWERED 2026-09-15, business**: Insider tier rate limit is **300 requests per minute**. Credit cost is **1 credit per call regardless of the date range requested**, so a 90-day pull costs the same 1 credit as a 1-day pull. Combined with the existing billing rule (only 2xx and 404 bill credits, 400/401/403/429/5xx are free), this gives the team a complete answer for range-based endpoints. Still open: per-endpoint credit costs where they differ from 1, and the canonical source of truth document for both figures.
55. **[BUS]** What happens when a customer exhausts credits, hard stop or throttle?

   > **ANSWERED**: Hard stop. With no credits left the user receives no response. Not a throttle.
56. **[BUS]** Which datasets are gated by plan? A customer seeing nulls may be hitting an entitlement boundary rather than a data gap, and the team needs to tell those apart.

   > **ANSWERED, verified in the app**: 27 call sites carry `promoFeature="Premium Data & Unlimited Export"`, plus one `"Watchlist Management"`. Gated surfaces: broker profile, foreign flow, historical cost, historical revenue, credit risk compare, bank liquidity, shareholder composition, filings, portfolio, orderbook, group pages, tag lists, sector filings, news, IDX and SGX company tables, SGX property table, SGX financial sankey, SGX ownership, IDX screener table, watchlist. A null on one of these is an entitlement boundary, not a data gap.
57. **[BUS]** Is there a free tier, and what is excluded from it?

   > **ANSWERED, from `sectors/src/components/pricing/PricingComparisonTable.tsx`**: Yes, a forever-free tier. Free includes near-full coverage (IDX 99.99%, SGX 80%), share price, historical dividends and splits, peer comparison, calendar and announcements, ESG ratings and scores, core financial statements, ratios and DCF, top 3 brokers, board and ownership, news, sentiment and AI tagging, search without AI, and data export. Free excludes: sector-specific metrics, stock screener, insider transactions, API access, Sectors AI Chat, personal watchlist, API credits, workflow automations, external AI integration, Mining/Metals/Minerals, workshops. Note the screener is Insider-only, not Standard, and API credits are Insider-only at 5,000 per month.

## C4. Account and privacy

58. **[BUS]** `delete_users_after_outreach_job` deletes user accounts **daily**. What exactly does it delete, on what criteria, and is it communicated to users beforehand? This is the question most likely to become a complaint, and the business team should not learn about it from the customer.

   > **ANSWERED**: Targets only users who signed up with email (not Google or GitHub) and never activated their account. The job counts activation reminders sent and when the last one went out; after 3 reminders at spaced intervals with no action, the account is deleted automatically. So users are contacted repeatedly beforehand, and no active or OAuth account is affected. Verified: pg_cron job 30, `SELECT delete_users_after_outreach();`, schedule `0 0 * * *`, active, so it runs daily at midnight. KB wording should make clear it only ever removes never-activated email signups.
59. **[BUS]** What is the data retention policy for user accounts, watchlists and workflows?

   > **CLARIFIED, awaiting answer**: this means data retention, not user retention. Three parts: when an account is deleted, do watchlists and workflows go with it or survive in backups; how long are records of a closed account kept; is anything retained deliberately, such as billing records for tax. If no policy has been set, say so and the KB will state that rather than invent one.
   >
   > **ANSWERED**: API keys, usage logs and similar cascade on delete, so they are wiped with the account. Workflows and screeners may survive, because those tables were created directly in Supabase rather than managed by Django, so the cascade may not cover them. Assume all deleted, and verify the workflow and screener cascade before the KB states it. A free user retains everything in general, though screener and workflow are gated.
60. **[BUS]** What does a customer do to request deletion, and how long does it take?

   > **ANSWERED**: The customer writes in to request deletion. Completed within 48 hours of the team's reply to that email.

---

# Part D. Support scenarios

The KB's most-used pages. Each needs an approved answer, and I can draft from the technical findings once you confirm the stance.

61. **[BUS]** "Your number disagrees with another provider." Preferred framing?

   > **ANSWERED, source approved**: frame from the public data-operations page (`sectors.app/data-operations`, PDF 'Data Ops for the Indonesia Financial Market'). Usable lines: numbers are standardized with accounting judgment so companies are comparable rather than passed through as reported; every data point is externally verifiable, traceable to its source document and mathematically checkable; IDX standards by default, OJK/Basel III/Bank Indonesia classification for banks, IDX-IC for sector classification; AI-assisted extraction with human-in-the-loop review and automatic anomaly flagging. So a disagreement with another provider is usually a standardization difference, and the reply invites the customer to trace the figure to source. Pair with the market cap line in Q22.
62. **[BUS]** "Data looks stale." How much staleness is normal before it is a fault?

   > **MEASURED, thresholds awaiting confirmation**. IDX daily price timeline (WIB), from actual run records: market closes 16:00; `sectors_idx_daily_data` fires 18:00:00, triggered by **cron-job.org** calling `workflow_dispatch` (Actions cron drifted 3-5h on the 11:00 UTC slot, per the workflow file comment); scrape completes 18:01:30-18:02:45 across the last 8 runs, median about 1m40s; `sectors_yf_data_updater` runs its own idx_daily_data job at the same 18:00 slot. So a raw price row is live about two minutes after 18:00 WIB, roughly two hours after close. CAVEAT: the `MV Daily Refresh` pg_cron job (`refresh_idx_reports()`) is scheduled `0 20 * * 1-5` in the database's own timezone. RESOLVED 2026-09-16: a database timestamp of `2026-09-15 11:01:09+00` renders in UTC and matches the 18:01 WIB scrape completion, so pg_cron runs on UTC and `0 20` is **03:00 WIB the next morning**. Derived metrics (P/E, market cap change, company report) therefore run about nine hours behind the raw price: price live 18:02 WIB, metrics refreshed 03:00 WIB next day. Suggested thresholds: price missing after 18:15 WIB on a trading day is a fault; derived metrics missing the next morning is a fault.
63. **[BUS]** "A company is missing." Delisted, suspended, never covered, or entitlement, how should the team narrow it down?

   > **ANSWERED**: Coverage is all IDX-listed companies, and the top 200 SGX counters for the full comprehensive report. Delisted companies are recorded in `idx_delisted_12m`. NOTE: that name did not appear in the public-schema table list read on 2026-09-14 (`idx_suspension` did), so confirm whether it is a view or lives in another schema before the KB cites it. Narrowing order for the team: delisted (check the delisted record), suspended (`idx_suspension`), outside coverage (an SGX counter beyond the top 200), then entitlement (Q56).
   >
   > **ANSWERED**: most likely a view, used to check whether a company is still live. Not in the `public` table list, which is consistent with that. Verify once before the KB cites it by name.
64. **[BUS]** "A field is null." Same question. Nulls are common and mostly benign.

   > **RESTATED as 'when is a null acceptable'**: a null has at least four causes and the team must tell them apart, (1) entitlement, one of the 27 gated surfaces in Q56, (2) not applicable by design, e.g. quick ratio on a bank (Q14), (3) genuinely absent upstream, e.g. free float when the shareholder label is not exactly 'Public', (4) a broken pipeline. Causes 1, 2 and 3 are acceptable nulls the team can explain on the spot; only cause 4 is a fault. Needed: confirmation of that split, and the order to check them in.
   >
   > **ANSWERED**: some nulls are acceptable and expected; anything genuinely wrong would have been flagged by Sectors Guard. So the team's check is: is this a gated surface, a not-applicable field, or an upstream gap? If none of those, escalate, because Guard should have caught it.
65. **[BUS]** "Why did a historical number change?" Restatements and back-fills both cause this.

   > **ANSWERED**: explain the causes, and let the business team judge from the customer's background how much detail to give. Historical numbers change for three reasons, company restatements, back-fills of previously missing history, and nightly materialized view refreshes that recompute derived metrics. All three are stated plainly in the KB; the team chooses how much to pass on.
66. **[BUS]** "Can I get data you don't have?" Is there a request path, or is it a no?

   > **ANSWERED**: There is a request path. A customer can request data; the team checks whether it is already planned and, if not, whether it will be added. Not a flat no, and not a commitment.
67. **[BUS]** "Is this investment advice?" There is presumably a required disclaimer. What is it verbatim?

   > **ANSWERED, verbatim disclaimer**: "does not constitute investment advice, a recommendation, or an offer to buy or sell any security".
68. **[BUS]** At what point does a question stop being the business team's and become engineering's? A clear boundary keeps the KB from being used past its competence, which matters more when the team answers customers directly.

   > **ANSWERED**: Anything the business team cannot answer defaults to engineering, which also owns product design and planning decisions. So there is one escalation target and no judgment call about which team to route to, consistent with Q11.

---

# Part E. Structure and scope

69. **[BUS]** Which ten questions does the business team actually get most often? Everything above is derived from what the code exposes. Your inbox is better evidence and would reorder the whole thing.

   > **ANSWERED, source `/Users/evelyn/Desktop/QnA` (real support transcripts, Indonesian and English)**. Recurring themes, in rough frequency order: (1) credits, how the system works, monthly allowance, no rollover, workshop credits valid one year, per-endpoint cost; (2) what the Insider plan includes, and where the feature list lives; (3) history depth and coverage ('berapa tahun ke belakang'); (4) API licensing and redistribution for a paid product, including whether Sectors holds IDX Data Services rights and any attribution or user-count reporting duty; (5) data disagreement with another provider, e.g. TLKM financials behind by a quarter; (6) EOD timing, when data is final after the 16:00-16:15 WIB close, and whether intraday exists; (7) broker summary and foreign flow, depth, market-wide aggregates, credit cost of looping 600+ tickers; (8) subscription cancellation and Stripe billing; (9) onboarding and workshop recordings access; (10) which endpoints exist for insider trading and institutional flow. Note the licensing enquiry came from a startup building a paid retail product, exactly the ToS conflict in Q50.
70. **[BUS]** Are there questions customers ask that have no good answer today? Those are the highest-value KB pages.

   > **NO RECORD KEPT**. Derive candidates from the transcripts instead: the IDX redistribution-licence question, market-wide foreign flow aggregates, intraday availability, and per-endpoint credit costs all received partial or improvised answers in `/Users/evelyn/Desktop/QnA`.
71. **[EITHER]** Should the KB cover Sectors product features (screener, watchlist, workflows) alongside data, or data only?

   > **ANSWERED**: Cover all features, not data only. Important for enterprise customers, who ask about screener, watchlist, workflows and API together.
72. **[EITHER]** Does the business team handle the Sectors API, sectors.app, the Chrome extension and Sectors Insider, or only some?

   > **ANSWERED**: The business team is front-facing, so incoming questions are not limited by surface. The KB must cover the Sectors API, sectors.app, the Chrome extension and Sectors Insider.
73. **[ENG]** Are there scheduled jobs outside GitHub Actions and pg_cron, for example fly.io, Vercel cron or Supabase Edge Functions? I found Actions by looking, then pg_cron by looking again. A third category would not surprise me, and would mean more undocumented freshness behaviour.

   > **ANSWERED, verified**: five execution locations, not two. (1) GitHub Actions; (2) pg_cron in Supabase; (3) fly.io, `sectors_guard_validator`; (4) Google Cloud Run with Cloud Scheduler, the news pipelines; (5) cron-job.org calling `workflow_dispatch`, the IDX daily price scrape. Plus Vercel, see Q75. Answering a freshness question from Actions cron alone gives the wrong answer on the two most customer-visible datasets, prices and news.
74. **[ENG]** `sectors_news` is documented with a schedule and actively pushed, but carries no GitHub Actions cron. Where is it scheduled? A fly.io host appears in related code.

   > **ANSWERED, verified**: moved off GitHub Actions to Cloud Run jobs in project `datasheet-398802`, region `us-central1`, 2 vCPU / 4 GiB. Cloud Scheduler jobs `sectors-news-idx-4hourly` (`15 */4 * * *` UTC) and `sectors-news-sgx-4hourly` (`0 */4 * * *` UTC); the Actions cron lines are commented out and `workflow_dispatch` is kept as a manual escape hatch. Detail in `deployments/README.md`. Open: the repo also carries a `.dagu` directory, a third orchestration remnant worth asking about.
75. **[ENG]** `run_sectors_watchlist_notification` contains only a workflow file. Where does the logic live?

   > **ANSWERED, verified**: the workflow is only a trigger, `cron: '0 5 * * 1'` (Mondays 12:00 WIB), running `curl -X GET "https://sectors-watchlist-notification.vercel.app/api/email"`. The logic lives in a **Vercel** deployment, a sixth execution location.
76. **[ENG]** Confirm `LativOS_pipeline`, which seeds generated fake FMCG data, has no path into any Sectors table.

   > **CONFIRMED**: no path into any Sectors table. `scripts/seed-daily-data.js` writes only `bank_balance`, `bank_top_transaction`, `bank_new_vendor_flag`, `marketplace_review` and `analytic_revenue_monthly`, none of which exist in the Sectors public schema, and it targets its own `SUPABASE_URL`. Exclude from the KB entirely.

---

# Part F. Language

77. **[BUS]** English only, or Indonesian too? Several upstream sources and much of the customer base are Indonesian, and a bilingual KB doubles step 5.

   > **ANSWERED**: English only. The team is fluent in Indonesian and replies to customers in their own language; the KB is a reference, not reply text. Every customer reply stays human-written.
78. **[BUS]** Is there an existing tone or style guide for customer replies I should match?

   > **ANSWERED**: Professional, helpful, proactive.

---

# Conflicts to resolve before the KB publishes

Found 2026-09-16 by comparing answers in this file against the support transcripts
in `/Users/evelyn/Desktop/QnA` and against the database.

1. **History depth. RESOLVED 2026-09-16.** Six years through the API, from 2020-01-02,
   and ten years on request from the deeper archive. Both figures are correct and
   describe different things, so the KB states both. Request window is capped at 90 days
   with a 30-day default. A customer was previously told "up to 5 years via our API",
   which is wrong.
2. **Credit costs. RESOLVED.** Both figures are correct and not in conflict: 1 credit
   for a structured query, 3 for a natural-language `?q=` query, and about 32 credits
   for the full-close endpoint because that single call returns closing prices for all
   900+ companies. The KB should say cost scales with how much the endpoint returns,
   and give these three as the worked examples.
3. **Depth differs by dataset. RESOLVED, and it is the answer itself.** Daily price
   history runs to 2020-01-02 via the API, and foreign flow with it, since foreign
   volume rides on the daily price rows. Broker summary is shallower, 2025-01-02 onward.
   Depth is per dataset, not one number for the product, so the KB gives a per-dataset
   table rather than a headline figure. Support previously told a customer foreign flow
   starts at the beginning of 2025, understating it by five years.

# Staleness threshold options (Q62)

Pick one column. Timings verified from run records, see Q62.

**DECIDED 2026-09-16**: the business team works to the **tight** column internally, so
they know when something is genuinely late, and quotes the **moderate** column to
customers. Never quote the tight column outward.

| Dataset | Tight (internal) | Moderate (quote to customers) | Loose |
|---|---|---|---|
| IDX daily price | 18:15 WIB same day | 19:00 WIB same day | next morning |
| Derived metrics (P/E, mcap change, reports) | 04:00 WIB next day | 09:00 WIB next day | 24h |
| Financial statements | 2 business days after IDX publication | 5 business days | next quarterly run |
| News | 8 hours | 24 hours | none |
| Filings | 4 hours | 24 hours | none |
| Broker summary, foreign flow | next morning | 2 business days | none |
| ESG, analyst ratings, forecasts | none, manual or monthly | none | none |

# Not a KB item

A Supabase Row Level Security advisory surfaced during this work. Out of scope for the
knowledge base and excluded by decision on 2026-09-16.
