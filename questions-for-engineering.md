# Knowledge base, questions for engineering

Prepared 2026-09-12. These are the questions a read-only sweep of the code could not answer, and that need someone who knows the pipelines or the SQL.

Context: we are building an internal knowledge base so the business team can answer customer technical questions directly. Everything recoverable from code has already been extracted (pipeline schedules, cron jobs, metric formulas). What remains is intent, provenance and current operational state.

Short answers are fine. "Ask X" is a useful answer.


## A1. Failing while still scheduled

1. `sectors_analyst_rating_scraper`, 90 failures in its last 95 scheduled runs, last success May 2026. Are analyst ratings currently frozen in the product? If a customer views analyst ratings today, what are they seeing, and how old is it?
2. `idx_sheets_financial_data_extraction`, last run 2026-08-30 failed. This feeds `idx_financial_sheets_quarterly` and `idx_financial_sheets_annual`. Is quarterly financial statement data currently behind?
3. `sectors_idx_highlights`, most recent run failed. This generates the weekly Instagram highlights. Customer-visible or internal only?

## A2. Stale, no scheduled run since June 2026

4. `mutual_fund_pipeline`, last ran 2026-03-20, both recorded runs failed. Is Indonesian mutual fund data a live product surface or an abandoned experiment?
5. `sectors_forecast_growth_rate`, last ran 2025-10-01. The doc says it is run manually each quarter with the Action as a reminder only. Is that still the process, and was it run for the most recent quarters?
6. `sectors_price_anomaly_updater`, last ran 2026-04-27. Is price anomaly detection still a feature?
7. `sectors_idx_fear_and_greed_index`, last ran 2026-02-02. Is the fear and greed index still published? Customers may still see a stale number.
8. `sectors_get_esg_score`, last ran 2026-06-01. How current is ESG data, and what should we tell a customer who asks when it was last updated?

## A3. Cron defined, never triggered

9. `sectors_us_cron`, `sectors_ic_data_updater`, `sectors_get_etf_data`, `sectors_dcf_calculation` all carry a `cron:` that has never produced a scheduled run. Dead code, or scheduled somewhere else?
10. Related: is US market data a current product surface at all? Several US repos exist (`sectors_us_insider_trading` is live and running, `sectors_us_cron` and `sectors_us_profile_updater` are not). What do we tell a customer who asks whether you cover US equities?

## A4. Ownership

11. Who owns each live pipeline day to day? The KB should name an escalation target per dataset, not "engineering".
12. When a pipeline fails, who is alerted and how? Is anyone watching, or was the 90-failure streak on analyst ratings genuinely unnoticed?

## B1. Flagged in `terms.md`

14. Quick ratio is forced to `NULL` for `sub_sector_id = 19`, with no comment in the SQL. Which sub-sector is 19, and why the exclusion? Is it still wanted?
15. `calculated_dividend_ttm` versus `dividend_ttm`: the comprehensive report prefers the first and falls back to the second. What is the difference, how is `calculated_dividend_ttm` derived, and which should be quoted to a customer?
16. Where does `forward_eps` come from? Which provider or model, and what vintage? A customer asking "whose forecast is this" currently has no answer.
18. `pb_mrq` divides market cap by book value with no zero guard, while `pe_ttm`, `ps_ttm` and `pcf_ttm` all guard. Deliberate or oversight?
19. `enterprise_to_ebitda` returns `0` rather than `NULL` when EBITDA is zero, which puts loss-making companies at the top of an ascending screener sort. Intended?

## B2. Market cap, the biggest one

20. Is the three-method market cap approach (`mcap_method` 1, 2, 3) documented anywhere customer-facing? This is the most likely source of "your numbers disagree with Bloomberg" and right now the business team has no sanctioned explanation.
21. Is `mcap_method` exposed through the API, or internal only? If exposed, the KB should tell the business team how to read it.

## B3. Prices and adjustments

23. Are prices in `idx_daily_data` split-adjusted, unadjusted, or mixed? `idx_stock_split_cumulative` exists and `dps_split` implies split adjustment for dividends, but the close price basis is not stated anywhere.
24. Are prices dividend-adjusted (total return) or price-only?
25. What does `dps_split` mean exactly, dividend per share adjusted for splits up to when, the payment date or today?
26. How far back does daily price history go per market (IDX, SGX, KLSE)? A customer asking "how much history do I get" needs a number.
27. What is in `historical_dividends` JSONB, how far back, and what populates it?

## B4. Currency

28. `yf_currency` exists on the company profile. Are all IDX figures in IDR, or do some companies report in USD? What does the customer receive?
29. `sectors_get_conversion_rate` writes a USD to IDR rate into a JSON file in the repo, not a table. Where is that rate actually consumed, and how stale can it be?
30. For SGX and KLSE, what currency are financials and market cap in, and is any conversion applied?

## B5. Classification

31. Where does the sector and sub-sector taxonomy come from, IDX's own or a Sectors-defined mapping?
32. Is the taxonomy versioned? If a company gets reclassified, does history move with it?
33. How many sub-sectors are there, and is the list stable enough to publish?
34. Index membership comes from IDX zip downloads via `sectors_indices_company_list`. Which indices are covered, and how quickly does a membership change appear?

## B6. Coverage, per dataset

35. IDX: how many companies, and does coverage include all boards (Main, Development, Acceleration)?
36. SGX: how many counters, and does it include REITs, trusts and secondary listings?
37. KLSE: how many companies, and is coverage comparable to SGX or partial?
38. Mining (`coalresearch`): which commodities, how many companies or sites, and what is the update cadence in practice? This one lives in SQLite on a separate app, so it may behave differently from everything else.
39. KSEI 1 percent ownership: the pipeline reads a monthly Excel export. When in the month does new data actually land, and how far behind the register date is it?
40. Broker summary: how far back, and is it every broker or a subset?
41. Filings: what filing types are covered, and what is the lag from IDX publication to appearing in the product?
42. News: which sources, and is it all Indonesian-language, English, or both?

## B7. Data quality

43. `sectors_guard_validator` validates several IDX tables and emails on anomalies. Who receives those emails, and is there a record a business person can check before answering "has this number been validated"?
44. What happens when validation fails, is bad data blocked from publishing or flagged after the fact?

# Part E. Structure and scope

71. Should the KB cover Sectors product features (screener, watchlist, workflows) alongside data, or data only?
72. Does the business team handle the Sectors API, sectors.app, the Chrome extension and Sectors Insider, or only some?
73. Are there scheduled jobs outside GitHub Actions and pg_cron, for example fly.io, Vercel cron or Supabase Edge Functions? I found Actions by looking, then pg_cron by looking again. A third category would not surprise me, and would mean more undocumented freshness behaviour.
74. `sectors_news` is documented with a schedule and actively pushed, but carries no GitHub Actions cron. Where is it scheduled? A fly.io host appears in related code.
75. `run_sectors_watchlist_notification` contains only a workflow file. Where does the logic live?
76. Confirm `LativOS_pipeline`, which seeds generated fake FMCG data, has no path into any Sectors table.

# Part G. Conflicts raised by business answers, 2026-09-15

Both came out of a customer evaluation of the Insider plan for an end-of-day foreign flow pipeline. Business answered; the answers disagree with what the code and data say, so neither can be published yet.

77. **Foreign flow history start year.** Business says the daily net foreign inflow series begins at the start of 2025. The data says the first date in `idx_broker_summary_daily`, the table the foreign flow views are built on, is 2026-01-01. Which is right, and does the API's foreign-flow endpoint read `idx_broker_summary_daily` at all or a separate store with deeper history? A customer sizing a backtest needs one number.

78. **EOD finalisation time for a customer pipeline.** Business says schedule around 20:00 WIB. `refresh_ff_mv` is `30 13 * * 1-5`, which is 20:30 WIB, so a 20:00 run misses the foreign flow refresh. Is 20:00 correct for everything except foreign flow, and should foreign flow customers be told 21:00? Give one instruction per dataset rather than one for the whole API.

79. **Which foreign flow definition does the customer actually want.** Ask this before quoting any foreign flow figure or endpoint, because the two definitions give materially different numbers and the customer usually has not realised there is a choice. The two are:

   - **Beneficial-owner basis**, the exchange's own per-row `f_bval` and `f_sval` flags on `idx_broker_summary_daily`. This is what Sectors serves, and it matches IDX's published foreign-flow print.
   - **Executing-broker domicile basis**, `idx_broker_registry.is_foreign`, which classifies by the domicile of the brokerage handling the trade. This books an Indonesian family office trading through a foreign house as foreign money.

   Over 2026 the domicile basis returned -79.5T against the beneficial-owner basis at -92.6T, so the gap is large enough to break a reconciliation or a backtest. A customer benchmarking against IDX's own print wants the first; a customer studying brokerage routing or flow through foreign houses wants the second, and Sectors does not serve that today. Record the customer's answer in the enquiry thread before sizing anything.
