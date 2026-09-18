# `sectors-kb` pipeline documentation, proposed updates

Prepared 2026-09-12 from a read-only sweep of the `supertypeai` GitHub org, the `supabase-backend` repo, and one approved read of the `cron.job` catalog. No repository was modified.

This is a change request against `sectors_documentation/pipeline_documentation.md`, written as sections to apply rather than a replacement file, so nothing already correct gets clobbered. Every schedule below was read from a live `cron:` expression or from `cron.job`, not from prose.

Times are WIB (UTC+7). GitHub Actions cron is UTC.

## Why this is needed

The current document names 29 repositories, 18 of which sit inside `<!-- -->` blocks and are invisible to any reader or retrieval system. Meanwhile 46 repositories carry an active cron schedule and 37 have run on schedule since June 2026. Eight documented schedules no longer match the workflow files. An entire category, the database's own pg_cron jobs, is absent.

## Section 1. Correct these schedules

Eight entries state a schedule that no longer matches the repository. Anyone answering a customer question about data freshness from the current document gets these wrong.

| Repository | Document says | Actually runs |
|---|---|---|
| `sectors_dividend_checker` | monthly on day 1 plus Mondays 08:00 UTC | daily 01:00 WIB plus weekdays 01:00 WIB |
| `sectors_yf_data_updater` | only the monthly day-10 job | adds weekdays 18:00 WIB and Saturdays 08:00 WIB |
| `sectors_idx_high_low_price` | weekdays 03:00 UTC | weekdays twice daily, 11:00 and 18:00 WIB |
| `sectors_indices_company_list` | weekdays 11:00 UTC only | adds a monthly day-1 job |
| `sectors_sg_my_data_updater` | three jobs | seven jobs, including a daily and a fortnightly one |
| `idx_sheets_financial_data_extraction` | ten jobs including daily ones | seven jobs, quarter-end dated, the daily jobs are gone |
| `sectors_price_anomaly_updater` | weekdays 14:00 UTC, Mondays 02:00, daily 03:00 | Tue-Sat 10:00 WIB and weekdays 11:00 WIB |
| `sectors_analyst_rating_scraper` | four jobs | one job, daily 17:00 WIB |

`sectors_dividend_checker` is the one to fix first. The document implies dividends refresh monthly; they refresh daily. That is the single most likely question from a customer noticing a missing dividend.

## Section 2. Restore the commented-out block

18 repositories are documented inside HTML comment blocks, so they render as nothing. Among them are the pipelines behind daily prices, quarterly and annual financials, and company profiles, which is to say the most-asked data in the platform.

- `idx_sheets_financial_data_extraction`, still active
- `run_sectors_watchlist_notification`, still active
- `sectors_analyst_rating_scraper`, still active
- `sectors_company_revenue_breakdown`, no active cron
- `sectors_generate_subsector_index`, still active
- `sectors_get_closed_ipo`, still active
- `sectors_get_upcoming_ipo_data`, still active
- `sectors_idx_high_low_price`, still active
- `sectors_idx_profile_updater`, still active
- `sectors_indices_company_list`, still active
- `sectors_ms_transactions_updater`, still active
- `sectors_news`, no active cron
- `sectors_news_endpoint`, no active cron
- `sectors_price_anomaly_updater`, still active
- `sectors_sankey_processing`, no active cron
- `sectors_sg_my_data_updater`, still active
- `sectors_sgx_short_sell`, still active
- `sectors_yf_data_updater`, still active

Recommendation: restore the ones still active with schedules corrected per Section 1, and move the rest to a retired section rather than leaving them commented. A comment block is not a retirement record, it just hides the entry.

## Section 3. New entries

Live, scheduled pipelines with no entry in the current document. Ready to paste.

### [buyback_notify](https://github.com/supertypeai/buyback_notify)

IDX buyback notifications.


- Schedules:
  - `0 10 * * *`, daily 17:00 WIB (daily_run.yml)
- Tables: `idx_client`
- Upstream sources: www.idx.co.id
- Health as of 2026-09-12: last scheduled run 2026-09-11 (success), 13 failures in the last 100 scheduled runs

### [coalresearch](https://github.com/supertypeai/coalresearch)

The Indonesian mining data stack. Scrapes ESDM government sources (minerba, modi, geoportal), APNI, and LBMA prices into the mining tables: companies, financials, ownership, performance, licences, auctions, sites, reserves, contracts, production, exports and sales destinations. Note this is the pipeline; the consuming app is onlyphantom/mining, which is a separate Next.js app on SQLite rather than Supabase.


- Schedules:
  - `0 0 * * 1`, Mon 07:00 WIB (commodity_price_weekly_scraper.yaml)
  - `0 0 1 * *`, monthly on day 1, 07:00 WIB (mining_license_auction_monthly_scraper.yaml)
  - `0 0 2 * *`, monthly on day 2, 07:00 WIB (mining_license_merge_upsert.yaml)
  - `0 0 1 * *`, monthly on day 1, 07:00 WIB (mining_license_monthly_scraper.yaml)
  - `17 0 * * *`, daily 07:17 WIB (mining_news_daily_scrape.yaml)
  - `17 2 * * 0`, Sun 09:17 WIB (mining_news_weekly_scraper.yaml)
  - `0 0 1 * *`, monthly on day 1, 07:00 WIB (modi_monthly_scraper.yaml)
  - `0 1 * * *`, daily 08:00 WIB (turso_daily_sync.yaml)
  - `0 0 * * 2`, Tue 07:00 WIB (turso_weekly_sync.yaml)
- Tables: `company_financials`, `commodity_price`, `company`, `company_ownership`, `company_performance`, `export_destination`, `global_commodity_data`, `mining_contract`, `mining_license`, `mining_license_auctions`, `mining_news`, `mining_site`, `resources_and_reserves`, `sales_destination`, `total_commodities_production`
- Upstream sources: minerbaone.esdm.go.id, localhost, minerba.esdm.go.id, modi.esdm.go.id, prices.lbma.org.uk, www.apni.or.id, apiform88.herokuapp.com, geoportal.esdm.go.id
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 1 failures in the last 100 scheduled runs

### [sectors_ai_search_test](https://github.com/supertypeai/sectors_ai_search_test)

QA harness that scores Sectors AI search against a fixed question set. Not a data pipeline; it produces test scores, not customer data.

Classified as a consumer, not a data pipeline: it reads market tables and writes none.

- Schedules:
  - `0 19 */7 * *`, every 7 days 02:00 WIB (actions.yaml)
- Tables: none detected by scan, needs an owner to confirm
- Upstream sources: api.sectors.app
- Health as of 2026-09-12: last scheduled run 2026-09-08 (success), 6 failures in the last 67 scheduled runs

### [sectors_api_reactivation_outreach](https://github.com/supertypeai/sectors_api_reactivation_outreach)

Daily cURL call to the Sectors API reactivation endpoint, which emails inactive API users. Customer-facing email, so worth the business team knowing it runs.


- Schedules:
  - `0 3 * * *`, daily 10:00 WIB (email_reactivation.yaml)
- Tables: none detected by scan, needs an owner to confirm
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 0 failures in the last 100 scheduled runs

### [sectors_chrome_extension](https://github.com/supertypeai/sectors_chrome_extension)

Sectors Ticker Lens browser extension. Reads company report tables through the API, writes nothing.

Classified as a consumer, not a data pipeline: it reads market tables and writes none.

- Schedules:
  - `0 0 1,15 * *`, monthly on day 1,15, 07:00 WIB (update_company_list.yml)
- Tables: `idx_active_company_profile`, `sgx_company_report`
- Upstream sources: sectors.app, api.sectors.app
- Health as of 2026-09-12: last scheduled run 2026-09-01 (success), 1 failures in the last 7 scheduled runs

### [sectors_corporate_actions](https://github.com/supertypeai/sectors_corporate_actions)

Scrapes IDX corporate actions from new.sahamidx.com. Rights issues and reverse stock splits are not available there and are scraped separately.


- Schedules:
  - `0 11 * * *`, daily 18:00 WIB (daily_rups_scraping.yaml)
  - `0 18 * * 5`, Fri 01:00 WIB (weekly_ca_bonus_scraper.yaml)
  - `0 18 * * 5`, Fri 01:00 WIB (weekly_right_issue_scraper.yaml)
  - `0 18 * * 5`, Fri 01:00 WIB (weekly_warrant_scraper.yaml)
- Tables: `idx_warrant`, `idx_buybacks`, `idx_company_profile`
- Upstream sources: www.new.sahamidx.com, www.sahamidx.com, stockbit.com
- Health as of 2026-09-12: last scheduled run 2026-09-11 (success), 17 failures in the last 100 scheduled runs

### [sectors_guard_validator](https://github.com/supertypeai/sectors_guard_validator)

Backend service that validates IDX datasets for anomalies and sends email notifications. Covers annual and quarterly financials, daily prices, dividends, filings and stock splits, writing outcomes to the validation tables. This is the data quality layer, so the business team should know it exists before answering any question about whether a number has been checked.


- Schedules:
  - `0 8 15 * *`, monthly on day 15, 15:00 WIB (check-api.yml)
  - `0 0 * * *`, daily 07:00 WIB (data-validation.yml)
  - `0 2 16 3,4,6 *`, month 3,4,6 day 16, 09:00 WIB (data-validation.yml)
  - `0 3 1 * *`, monthly on day 1, 10:00 WIB (data-validation.yml)
  - `0 3 * * 1`, Mon 10:00 WIB (data-validation.yml)
  - `0 1 * * 1`, Mon 08:00 WIB (data-validation.yml)
  - `0 4 * * 2`, Tue 11:00 WIB (data-validation.yml)
  - `30 4 * * 2`, Tue 11:30 WIB (data-validation.yml)
  - `0 5 * * *`, daily 12:00 WIB (data-validation.yml)
  - `15 9 * * *`, daily 16:15 WIB (data-validation.yml)
  - `0 7 1 * *`, monthly on day 1, 14:00 WIB (data-validation.yml)
  - `0 7 16 * *`, monthly on day 16, 14:00 WIB (data-validation.yml)
  - `0 8 1 * *`, monthly on day 1, 15:00 WIB (data-validation.yml)
  - `0 9 1 * *`, monthly on day 1, 16:00 WIB (data-validation.yml)
  - `0 10 1 1,7 *`, month 1,7 day 1, 17:00 WIB (data-validation.yml)
  - `0 11 29 * *`, monthly on day 29, 18:00 WIB (data-validation.yml)
  - `0 6 * * 1-5`, Mon-Fri 13:00 WIB (data-validation.yml)
  - `0 2 * * 1`, Mon 09:00 WIB (data-validation.yml)
  - `0 2 * * 1-5`, Mon-Fri 09:00 WIB (data-validation.yml)
  - `30 5 * * 1`, Mon 12:30 WIB (data-validation.yml)
  - `30 4 * * *`, daily 11:30 WIB (data-validation.yml)
  - `0 0 * * *`, daily 07:00 WIB (fetch-cron-sheet.yml)
  - `0 0 * * *`, daily 07:00 WIB (fetch-sheet.yml)
  - `28 16 * * *`, daily 23:28 WIB (weekly-cron-report.yml)
- Tables: `validation_results`, `validation_configs`, `idx_daily_data`, `idx_dividend`, `validation_batch_results`, `idx_company_profile`, `idx_active_company_profile`, `idx_filings`, `rpc_functions`, `test_table`
- Upstream sources: localhost, sectors-guard.vercel.app, 127.0.0.1, fonts.googleapis.com, links.sgx.com
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 0 failures in the last 100 scheduled runs

### [sectors_idx_filing_pipeline](https://github.com/supertypeai/sectors_idx_filing_pipeline)

Harvests IDX insider ownership announcements, parses the filings out of their PDFs, repairs what it can, and writes the result to Supabase as filings and news. Runs unattended, designed so manual intervention is only needed when genuinely unavoidable.


- Schedules:
  - `0 */2 * * *`, every 2 hours (idx_filings_v2.yaml)
  - `0 3 1 * *`, monthly on day 1, 10:00 WIB (refresh_company_map.yaml)
- Tables: `idx_filings`, `idx_company_report`, `idx_pipeline`
- Upstream sources: www.idx.co.id
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 9 failures in the last 97 scheduled runs

### [sectors_idx_highlights](https://github.com/supertypeai/sectors_idx_highlights)

Generates the weekly IDX highlights post for the @sectorsapp Instagram account. Read-only against market tables.

Classified as a consumer, not a data pipeline: it reads market tables and writes none.

- Schedules:
  - `0 1 * * 6`, Sat 08:00 WIB (main.yml)
- Tables: `idx_daily_data`, `index_daily_data`, `idx_sector_report`, `idx_ipo_details_12m`, `idx_ipo_details`, `idx_company_profile`, `idx_subsector_metadata`, `idx_calc_metrics_daily`, `idx_ipo_perf`, `idx_upcoming_dividend`, `idx_stock_split`, `idx_company_report`
- Upstream sources: storage.googleapis.com
- Health as of 2026-09-12: last scheduled run 2026-09-12 (failure), 6 failures in the last 57 scheduled runs

### [sectors_idx_shareholders_composition](https://github.com/supertypeai/sectors_idx_shareholders_composition)

Scrapes shareholder composition from KSEI.


- Schedules:
  - `0 8 1 * *`, monthly on day 1, 15:00 WIB (main.yml)
- Tables: `idx_shareholders_composition`
- Upstream sources: web.ksei.co.id
- Health as of 2026-09-12: last scheduled run 2026-09-01 (success), 1 failures in the last 4 scheduled runs

### [sectors_idx_suspension](https://github.com/supertypeai/sectors_idx_suspension)

Scrapes IDX stock suspension announcements, parses the PDF announcements into structured rows, and stores them. Incomplete records are written to CSV rather than dropped.


- Schedules:
  - `0 3 * * *`, daily 10:00 WIB (idx_suspension_ci.yaml)
- Tables: `idx_company_profile`, `idx_suspension`
- Upstream sources: www.idx.co.id
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 5 failures in the last 100 scheduled runs

### [sectors_ksei_ownership](https://github.com/supertypeai/sectors_ksei_ownership)

Extracts major shareholder ownership from KSEI, Indonesia's central securities depository, enriches it (registry-resolved names, classification, AI labelling of government entities, carry-forward of prior months) and loads it into the 1 percent ownership register. Source is currently a monthly Excel export; a future version will move to an API, changing only the fetch step.


- Schedules:
  - `0 1 1-10 * *`, monthly on day 1-10, 08:00 WIB (upsert-one-percent-ownership.yml)
- Tables: `idx_one_percent_ownership` (stated in the repo README)
- Upstream sources: www.idx.co.id, openrouter.ai
- Health as of 2026-09-12: last scheduled run 2026-09-10 (success), 5 failures in the last 22 scheduled runs

### [sectors_newsletter_automation](https://github.com/supertypeai/sectors_newsletter_automation)

Claude skills for Sectors content: newsletters, carousels and market videos. Reads user and market tables, writes none.

Classified as a consumer, not a data pipeline: it reads market tables and writes none.

- Schedules:
  - `0 18 28 * *`, monthly on day 28, 01:00 WIB (draft-monthly-market-pulse.yml)
  - `0 18 29 * *`, monthly on day 29, 01:00 WIB (draft-monthly-market-pulse.yml)
  - `0 18 30 * *`, monthly on day 30, 01:00 WIB (draft-monthly-market-pulse.yml)
  - `0 18 31 * *`, monthly on day 31, 01:00 WIB (draft-monthly-market-pulse.yml)
  - `0 18 * * 3`, Wed 01:00 WIB (draft-sector-spotlight.yml)
  - `0 18 * * 2`, Tue 01:00 WIB (draft-single-company-deep-dive.yml)
  - `0 18 * * 3`, Wed 01:00 WIB (draft-three-stock-story.yml)
  - `0 18 * * 0`, Sun 01:00 WIB (draft-weekly-insights.yml)
  - `0 0 * * 3`, Wed 07:00 WIB (lifecycle-nudge-cancel.yml)
  - `0 0 * * 4`, Thu 07:00 WIB (lifecycle-nudge-enroll.yml)
  - `0 20 * * 2`, Tue 03:00 WIB (sync-tier-tags.yml)
- Tables: `api_user`, `user_watchlist`, `user_workflow`, `idx_broker_summary_daily`, `idx_broker_registry`, `idx_daily_data`, `api_apiresponsetime`
- Upstream sources: api.sectors.app, mailroom.supertype.ai, storage.googleapis.com, openrouter.ai, sectors.app, generativelanguage.googleapis.com, api.openai.com, api.anthropic.com
- Health as of 2026-09-12: last scheduled run 2026-09-10 (success), 5 failures in the last 18 scheduled runs

### [sectors_social_media](https://github.com/supertypeai/sectors_social_media)

Image generator for Sectors social media content, including the daily filings post. Reads market tables, writes no market data.

Classified as a consumer, not a data pipeline: it reads market tables and writes none.

- Schedules:
  - `0 1 * * 6`, Sat 08:00 WIB (foreign_flow_social.yml)
- Tables: `idx_company_report`, `idx_workflow_data`, `index_daily_data`, `idx_upcoming_dividend`, `idx_daily_data`, `idx_news`, `idx_company_profile`
- Upstream sources: www.idx.co.id, emitennews.com, market.bisnis.com, sectors.app, www.idnfinancials.com, storage.googleapis.com, bcasekuritas.co.id, indonesiaminer.com
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 0 failures in the last 1 scheduled runs

### [sectors_us_insider_trading](https://github.com/supertypeai/sectors_us_insider_trading)

Parses SEC Form 4 XML filings into the US insider trading tables: sec_person, company_owner, form_4_filing and form_4_transaction.


- Schedules:
  - `0 0 * * 0`, Sun 07:00 WIB (actions_batch_2.yml)
  - `0 0 * * 0`, Sun 07:00 WIB (actions_batch_3.yml)
- Tables: `sec_person`, `company_owner`, `form_4_filing`, `form_4_transaction` (stated in the repo README)
- Health as of 2026-09-12: last scheduled run 2026-09-06 (success), 1 failures in the last 100 scheduled runs

### [sectors_workflow_backend](https://github.com/supertypeai/sectors_workflow_backend)

Event router for user-defined workflows. Reads workflow definitions and market data from Supabase, builds events from rules, and dispatches to Slack, email, WhatsApp and Sheets. This is what powers customer-facing workflow alerts.


- Schedules:
  - `0 0 1 * *`, monthly on day 1, 07:00 WIB (cleanup-wa-images.yaml)
  - `0 12 * * 1-5`, Mon-Fri 19:00 WIB (daily-workflows.yml)
- Tables: `user_screener`, `api_apikey`
- Upstream sources: sectors.app, api.sectors.app, api.telegram.org, hook1..., hook2..., storage.googleapis.com, www.googleapis.com, docs.google.com
- Health as of 2026-09-12: last scheduled run 2026-09-11 (success), 12 failures in the last 100 scheduled runs

### [sgx_buyback_pipeline](https://github.com/supertypeai/sgx_buyback_pipeline)

Fetches SGX share buyback announcements and processes them into the buyback table. Modular pipeline: a network layer against the SGX API, then per-dataset processing.


- Schedules:
  - `10 6 * * *`, daily 13:10 WIB (management_tracking.yaml)
  - `0 21 1 * *`, monthly on day 1, 04:00 WIB (refresh_sgx_companies.yaml)
  - `30 3 * * *`, daily 10:30 WIB (sgx_agm_scraper.yaml)
  - `10 5 * * *`, daily 12:10 WIB (sgx_buyback_scraper.yaml)
  - `10 2 * * *`, daily 09:10 WIB (sgx_filings_scraper.yaml)
  - `0 9 1 * *`, monthly on day 1, 16:00 WIB (sgx_management.yaml)
  - `10 3 * * *`, daily 10:10 WIB (sgx_reit_transaction_scraper.yaml)
  - `10 4 * * *`, daily 11:10 WIB (sgx_takeover_scraper.yaml)
  - `0 18 * * 1-5`, Mon-Fri 01:00 WIB (sgx_upcoming_dividend_scraper.yaml)
  - `0 7 * * *`, daily 14:00 WIB (shareholders_tracking.yaml)
  - `0 8 1 * *`, monthly on day 1, 15:00 WIB (sync_screener_shareholders.yaml)
- Tables: `sgx_buybacks`, `sgx_scraper`, `sgx_companies`, `sgx_news`, `sgx_upcoming_dividend`, `sgx_reit_property_transaction`, `sgx_agm`
- Upstream sources: links.sgx.com, www.sgx.com, api.sgx.com, api.frankfurter.app, sias.org.sg
- Health as of 2026-09-12: last scheduled run 2026-09-12 (success), 6 failures in the last 96 scheduled runs

### [summarize-agm-result](https://github.com/supertypeai/summarize-agm-result)

Fetches IDX disclosures, downloads the matching PDF attachments, extracts text with pdftotext, and summarizes AGM results through an LLM into the AGM table.


- Schedules:
  - `0 0 * 4-6 *`, month 4-6 day *, 07:00 WIB (ci.yml)
  - `0 0 * 1-3,7-12 1`, Mon 07:00 WIB (ci.yml)
- Tables: `idx_agm`
- Upstream sources: www.idx.co.id, openrouter.ai, api.openai.com, generativelanguage.googleapis.com
- Health as of 2026-09-12: last scheduled run 2026-08-24 (success), 19 failures in the last 64 scheduled runs

## Section 4. New section, database-side scheduled jobs

This category does not exist in the current document and cannot be recovered from any repository. The `supabase-backend` migrations create the `pg_cron` extension but contain no `cron.schedule` calls, and that repo's own README warns that migra strips pg_cron schedules from generated migrations. `cron.job` in the live database is the only source of truth.

Anything below that refreshes a materialized view sets the real freshness ceiling for every number served from that view. A pipeline can write a row at 18:00 and the customer still not see it until the MV refreshes.

| Job | Cron | Runs | Command |
|---|---|---|---|
| `MV Daily Refresh` | `0 20 * * 1-5` | weekdays 03:00 WIB | `SELECT refresh_idx_reports()` |
| `refresh_idx_broker_report` | `0 20 * * 1-5` | weekdays 03:00 WIB | `REFRESH MATERIALIZED VIEW CONCURRENTLY public.idx_broker_report` |
| `refresh_sgx_company_report` | `0 22 * * *` | daily 05:00 WIB | `REFRESH sgx_company_report then sgx_sector_report` |
| `refresh_klse_company_report` | `0 22 * * *` | daily 05:00 WIB | `REFRESH MATERIALIZED VIEW CONCURRENTLY klse_company_report` |
| `refresh_idx_daily_mcap_ffill` | `50 3,21 * * *` | daily 10:50 and 04:50 WIB | `REFRESH MATERIALIZED VIEW idx_daily_mcap_ffill` |
| `idx_workflow_data` | `30 11 * * 1-5` | weekdays 18:30 WIB | `refresh materialized view idx_workflow_data` |
| `refresh sgx_workflow_data` | `30 11 * * 1-5` | weekdays 18:30 WIB | `refresh materialized view sgx_workflow_data` |
| `refresh_broker_ytd_payload` | `15 13 * * 1-5` | weekdays 20:15 WIB | `SELECT refresh_broker_ytd()` |
| `refresh_ff_mv` | `30 13 * * 1-5` | weekdays 20:30 WIB | `SELECT refresh_foreign_flow()` |
| `refresh peer_company` | `0 16 * * 1-5` | weekdays 23:00 WIB | `REFRESH MATERIALIZED VIEW peers_data` |
| `check_and_execute_split_job` | `0 18 * * *` | daily 01:00 WIB | `select * from check_and_execute_split()` |
| `delete_multi_sotck_split` | `0 14 * * 1-5` | weekdays 21:00 WIB | `select delete_dup_stock_splits()` |
| `delete_users_after_outreach_job` | `0 0 * * *` | daily 07:00 WIB | `SELECT delete_users_after_outreach()` |
| `refresh_delete_expired_tokens` | `0 0 * * *` | daily 07:00 WIB | `SELECT delete_expired_tokens()` |
| `update_yield_job` | `0 0 2 1 *` | 2 January annually | `SELECT * from update_yield_for_prev_year()` |

Notes that matter for customer answers:

- **`MV Daily Refresh`**: Sequential refresh of nine IDX report MVs in dependency order, 15 minute statement timeout: idx_calc_metrics_quarterly, annual, daily, idx_aggregated_calc, idx_sector_reports_calc, idx_company_report_calc, idx_company_report, idx_comprehensive_report, idx_mcap_change_heatmap. The nine individually-registered jobs for these same views are all inactive because this job replaced them.
- **`refresh_broker_ytd_payload`**: Runs after market close, so broker YTD figures do not reflect the current session until the evening.
- **`refresh_ff_mv`**: Runs after market close. This is why foreign flow for the current day is not populated during trading hours.
- **`delete_multi_sotck_split`**: Job name contains a typo in production; do not correct it in docs, it is the real identifier.
- **`delete_users_after_outreach_job`**: Deletes user accounts daily. A data-retention behaviour the business team should be aware of before answering any question about account deletion.

Nine further jobs are registered but inactive: `refresh_idx_company_report`, `refresh_idx_comprehensive_report`, `refresh_idx_company_report_calc`, `refresh_idx_calc_metrics_daily`, `refresh_idx_calc_metrics_annual`, `refresh_idx_calc_metrics_quarterly`, `refresh_idx_aggregated_calc`, `refresh_idx_sector_reports_calc`, `refresh_idx_mcap_change_heatmap`. These are not a fault. `refresh_idx_reports()` refreshes exactly those nine views in dependency order, so they were consolidated into the single `MV Daily Refresh` job. Documenting them as disabled without that explanation would be worse than not documenting them, because the obvious reading is that IDX company reports stopped refreshing.

## Section 5. Pipelines needing a keep-or-kill decision

Cron still defined, no scheduled run since June 2026, or failing persistently. Each needs an owner verdict before it appears in a knowledge base, because the business team will read a documented pipeline as a working one.

| Repository | Last scheduled run | Signal |
|---|---|---|
| `mutual_fund_pipeline` | 2026-03-20 | 2 failures in last 2 runs |
| `sectors_analyst_rating_scraper` | 2026-05-02 | 90 failures in last 95 runs |
| `sectors_forecast_growth_rate` | 2025-10-01 | 1 failures in last 1 runs |
| `sectors_idx_fear_and_greed_index` | 2026-02-02 | 6 failures in last 100 runs |
| `sectors_price_anomaly_updater` | 2026-04-27 | 0 failures in last 100 runs |
| `sectors_dcf_calculation` | never ran on schedule | cron defined but never triggered |
| `sectors_get_etf_data` | never ran on schedule | cron defined but never triggered |
| `sectors_ic_data_updater` | never ran on schedule | cron defined but never triggered |
| `sectors_us_cron` | never ran on schedule | cron defined but never triggered |

Three more are live but failing and need the same verdict:

- `sectors_analyst_rating_scraper`, 90 failures in its last 95 scheduled runs, last success May 2026. Analyst ratings are very likely frozen.
- `idx_sheets_financial_data_extraction`, last run 2026-08-30 failed.
- `sectors_idx_highlights`, most recent run failed.

## Section 6. Explicitly out of scope

Scheduled repositories in the org that are not Sectors data pipelines. Listing them so the next person doing this sweep does not re-investigate them.

- `LativOS_pipeline`: Seeds the LativOS Supabase tables with generated fake Indonesian FMCG data. This is demo data for a separate product, not Sectors market data, and must not be described as a Sectors data source.
- `amt_deployment`: AMT model deployment and inference, using OpenWeatherMap data. Separate client project, unrelated to Sectors market data.
- `onlyphantom/mining`: Sectors Insider market intelligence platform, a Next.js app on SQLite with Drizzle, outside the `supertypeai` org and carrying no workflows. It consumes mining data; `coalresearch` produces it.

## Section 7. Open questions for the pipeline owners

Things this sweep could not determine from code, blocking accurate documentation:

1. `sectors_news` is documented with a schedule and is actively pushed, but carries no GitHub Actions cron. Where is it actually scheduled? A fly.io host appears in related code.
2. `run_sectors_watchlist_notification` contains only a workflow file. Where does the logic live?
3. `LativOS_pipeline` seeds generated fake data. Confirm it has no path into any Sectors table.
4. Are there scheduled jobs outside GitHub Actions and pg_cron, for instance on fly.io, Vercel cron, or Supabase Edge Functions? Two categories were found only by looking; a third would not surprise.
5. For each pipeline in Section 5, keep, retire, or known-broken?

## Method and limits

- Schedules come from `cron:` expressions in workflow YAML, excluding commented lines, and from `cron.job`.
- Run history comes from the Actions API, capped at the last 100 scheduled runs per repository, so a failure count on a frequent pipeline covers days while the same count on a weekly one covers years.
- Table lists come from pattern-matching pipeline source. Repositories that build clients dynamically or write through an API can show fewer tables than they touch. Treat a table list as a floor.
- Upstream sources are non-boilerplate URLs found in code, so a documentation link can appear beside a real data source.
- Live versus stale is judged purely on last scheduled run date, so a healthy monthly pipeline that last ran in May reads as stale here.