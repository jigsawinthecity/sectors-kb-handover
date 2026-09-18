# Sectors data pipeline inventory

Generated 2026-09-12 by a read-only sweep of the `supertypeai` GitHub org. Nothing in this file was hand-written, every schedule comes from the `cron:` expression in the repo's workflow YAML and every run date comes from the Actions API. Local times are WIB (UTC+7).

This is step 1 of the knowledge base work: an inventory for review, not the handover document. It exists to answer one question before any docs get written, which pipelines are actually alive.

## Headline numbers

| | count |
|---|---|
| Repos in the org | 160 |
| Repos with any GitHub Actions workflow | 66 |
| Repos with at least one active cron | 46 |
| Repos whose cron has actually run since 2026-06-01 | 37 |
| Repos named in the current `sectors-kb` pipeline doc | 26 |
| Of those, buried inside `<!-- -->` comment blocks | 15 |

The gap that matters: the existing doc names 26 repos, and 46 repos run on a schedule right now.

## Live pipelines

Cron defined and a scheduled run recorded since 2026-06-01. These are the ones the knowledge base has to describe correctly.

### LativOS_pipeline

to insert data to lativos

- Visibility: private | Language: JavaScript | Last push: 2026-07-22
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 7 failures in last 52 scheduled runs
- Schedules:
    - `0 23 * * *` → daily 06:00 WIB (seed-daily-data.yml: Seed Daily Data)
- Writes/reads tables: none detected by pattern scan, needs manual read

### amt_deployment


- Visibility: private | Language: Python | Last push: 2026-05-22
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 9 failures in last 99 scheduled runs
- Schedules:
    - `14 23 * * *` → daily 06:14 WIB (deploy.yaml: Daily Water Level Prediction)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: history.openweathermap.org

### buyback_notify


- Visibility: public | Language: Python | Last push: 2026-07-26
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-11 (success), 13 failures in last 100 scheduled runs
- Schedules:
    - `0 10 * * *` → daily 17:00 WIB (daily_run.yml: Daily Buyback Check)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: www.idx.co.id

### coalresearch


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 1 failures in last 100 scheduled runs
- Schedules:
    - `0 0 * * 1` → weekly Mon 07:00 WIB (commodity_price_weekly_scraper.yaml: Weekly Commodity Price Scraper)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (mining_license_auction_monthly_scraper.yaml: Monthly Mining License Auction Scraper)
    - `0 0 2 * *` → monthly day 2 07:00 WIB (mining_license_merge_upsert.yaml: Monthly Mining License Data Upsert)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (mining_license_monthly_scraper.yaml: Monthly Mining License Scraper)
    - `17 0 * * *` → daily 07:17 WIB (mining_news_daily_scrape.yaml: Mining News Daily Pipeline)
    - `17 2 * * 0` → weekly Sun 09:17 WIB (mining_news_weekly_scraper.yaml: Mining News Weekly Pipeline)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (modi_monthly_scraper.yaml: Monthly Minerba Company (MODI V2) Scraper)
    - `0 1 * * *` → daily 08:00 WIB (turso_daily_sync.yaml: Turso Daily Sync)
    - `0 0 * * 2` → weekly Tue 07:00 WIB (turso_weekly_sync.yaml: Turso Weekly Sync)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: minerbaone.esdm.go.id, localhost, minerba.esdm.go.id, modi.esdm.go.id, prices.lbma.org.uk, www.apni.or.id, apiform88.herokuapp.com, geoportal.esdm.go.id

### idx_sheets_financial_data_extraction

Extract financials data from IDX sheets

- Visibility: private | Language: Python | Last push: 2026-08-23
- In `sectors-kb`: commented out
- Last scheduled run: 2026-08-30 (failure), 13 failures in last 84 scheduled runs
- Schedules:
    - `0 0 7,14,21,28 3,5,8,11 *` → yearly month 3,5,8,11 day 7,14,21,28 07:00 WIB (batch_2.yaml: Batch 2 Process)
    - `0 0 8,15,22,29 3,5,8,11 *` → yearly month 3,5,8,11 day 8,15,22,29 07:00 WIB (batch_3.yaml: Batch 3 Process)
    - `0 0 9,16,23,30 3,5,8,11 *` → yearly month 3,5,8,11 day 9,16,23,30 07:00 WIB (batch_4.yaml: Batch 4 Process)
    - `0 0 31 3 *` → yearly month 3 day 31 07:00 WIB (jisdor_updater.yaml: JISDOR Updater)
    - `0 0 30 6 *` → yearly month 6 day 30 07:00 WIB (jisdor_updater.yaml: JISDOR Updater)
    - `0 0 30 9 *` → yearly month 9 day 30 07:00 WIB (jisdor_updater.yaml: JISDOR Updater)
    - `0 0 31 12 *` → yearly month 12 day 31 07:00 WIB (jisdor_updater.yaml: JISDOR Updater)
- Writes/reads tables: `idx_financial_sheets_quarterly`, `idx_financial_sheets_annual`, `idx_active_company_profile`
- Upstream sources: www.idx.co.id, api.exchangerate-api.com

### run_sectors_watchlist_notification


- Visibility: public | Language: - | Last push: 2026-09-07
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-07 (success), 0 failures in last 36 scheduled runs
- Schedules:
    - `0 5 * * 1` → weekly Mon 12:00 WIB (main.yml: Run Watchlist Notification)
- Writes/reads tables: none detected by pattern scan, needs manual read

### sectors_ai_search_test


- Visibility: private | Language: Python | Last push: 2026-09-08
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-08 (success), 6 failures in last 67 scheduled runs
- Schedules:
    - `0 19 */7 * *` → monthly day */7 02:00 WIB (actions.yaml: Run Sector search scoring)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: api.sectors.app

### sectors_api_reactivation_outreach


- Visibility: public | Language: - | Last push: 2026-09-12
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 0 failures in last 100 scheduled runs
- Schedules:
    - `0 3 * * *` → daily 10:00 WIB (email_reactivation.yaml: Trigger Email Reactivation API)
- Writes/reads tables: none detected by pattern scan, needs manual read

### sectors_chrome_extension


- Visibility: public | Language: JavaScript | Last push: 2026-09-01
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-01 (success), 1 failures in last 7 scheduled runs
- Schedules:
    - `0 0 1,15 * *` → monthly day 1,15 07:00 WIB (update_company_list.yml: Update Active Company List)
- Writes/reads tables: `idx_active_company_profile`, `sgx_company_report`
- Upstream sources: sectors.app, api.sectors.app

### sectors_corporate_actions


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-11 (success), 17 failures in last 100 scheduled runs
- Schedules:
    - `0 11 * * *` → daily 18:00 WIB (daily_rups_scraping.yaml: Daily Scraper Rups)
    - `0 18 * * 5` → weekly Fri 01:00 WIB (weekly_ca_bonus_scraper.yaml: Weekly Scraper Bonus)
    - `0 18 * * 5` → weekly Fri 01:00 WIB (weekly_right_issue_scraper.yaml: Weekly Scraper Right)
    - `0 18 * * 5` → weekly Fri 01:00 WIB (weekly_warrant_scraper.yaml: Weekly Scraper Warrant)
- Writes/reads tables: `idx_warrant`, `idx_buybacks`, `idx_company_profile`
- Upstream sources: www.new.sahamidx.com, www.sahamidx.com, stockbit.com

### sectors_dividend_checker


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: documented
- Last scheduled run: 2026-09-11 (success), 0 failures in last 100 scheduled runs
- Schedules:
    - `0 18 * * *` → daily 01:00 WIB (main.yml: Check and Update Dividend Weekly)
    - `0 18 * * 1-5` → weekly 1-5 01:00 WIB (scrape_upcoming_dividend.yml: Scrape Upcoming Dividend Data)
- Writes/reads tables: `idx_dividend`, `idx_upcoming_dividend`, `idx_company_profile`
- Upstream sources: www.new.sahamidx.com, sectors-news-endpoint.fly.dev

### sectors_generate_sub_sector_desc


- Visibility: private | Language: Jupyter Notebook | Last push: 2025-07-15
- In `sectors-kb`: documented
- Last scheduled run: 2026-09-01 (success), 0 failures in last 14 scheduled runs
- Schedules:
    - `0 0 1 * *` → monthly day 1 07:00 WIB (actions.yml: Update subsector description monthly)
- Writes/reads tables: `idx_subsector_metadata`
- Upstream sources: rfiycxgjbnkefczvbosm.supabase.co

### sectors_generate_subsector_index


- Visibility: public | Language: Python | Last push: 2026-09-07
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-07 (success), 5 failures in last 21 scheduled runs
- Schedules:
    - `0 1 * * 1` → weekly Mon 08:00 WIB (main.yml: Update subsector P/E Value, Health & Resilience Index, & Growth Index description weekly)
    - `0 3 1 * *` → monthly day 1 10:00 WIB (main_sgx.yaml: Update SGX sector, P/E Value, Health & Resilience Index, & Growth Index descriptions)
    - `0 2 * * 1` → weekly Mon 09:00 WIB (main_sgx.yaml: Update SGX sector, P/E Value, Health & Resilience Index, & Growth Index descriptions)
- Writes/reads tables: `idx_subsector_metadata`, `sgx_daily_data`, `sgx_sector_metadata`

### sectors_get_closed_ipo

Get Sectors Closed Ipo data to update the ipo price and underwriter of latest closed ipo company

- Visibility: public | Language: Python | Last push: 2026-09-10
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-10 (success), 6 failures in last 99 scheduled runs
- Schedules:
    - `0 19 */3 * *` → monthly day */3 02:00 WIB (actions.yaml: Update ipo price for latest closed ipo company)
- Writes/reads tables: `idx_ipo_details`, `idx_company_profile`
- Upstream sources: e-ipo.co.id

### sectors_get_conversion_rate


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: documented
- Last scheduled run: 2026-09-11 (success), 0 failures in last 100 scheduled runs
- Schedules:
    - `0 18 * * *` → daily 01:00 WIB (actions.yml: Update Conversion Rate Daily)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: api.exchangerate-api.com

### sectors_get_esg_score


- Visibility: public | Language: Python | Last push: 2026-03-26
- In `sectors-kb`: documented
- Last scheduled run: 2026-06-01 (success), 0 failures in last 3 scheduled runs
- Schedules:
    - `0 0 1 * *` → monthly day 1 07:00 WIB (monthly_esg.yaml: Monthly ESG Pipeline)
- Writes/reads tables: `idx_esg_score`, `idx_company_profile`
- Upstream sources: www.sustainalytics.com, idx.co.id, sustainability.idx.co.id

### sectors_get_upcoming_ipo_data


- Visibility: public | Language: Python | Last push: 2026-09-07
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-07 (success), 0 failures in last 58 scheduled runs
- Schedules:
    - `0 0 * * 1` → weekly Mon 07:00 WIB (actions.yaml: run main.py)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: e-ipo.co.id

### sectors_guard_validator


- Visibility: public | Language: Python | Last push: 2026-08-20
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 0 failures in last 100 scheduled runs
- Schedules:
    - `0 8 15 * *` → monthly day 15 15:00 WIB (check-api.yml: Check Periwatch API)
    - `0 0 * * *` → daily 07:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 2 16 3,4,6 *` → yearly month 3,4,6 day 16 09:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 3 1 * *` → monthly day 1 10:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 3 * * 1` → weekly Mon 10:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 1 * * 1` → weekly Mon 08:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 4 * * 2` → weekly Tue 11:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `30 4 * * 2` → weekly Tue 11:30 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 5 * * *` → daily 12:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `15 9 * * *` → daily 16:15 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 7 1 * *` → monthly day 1 14:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 7 16 * *` → monthly day 16 14:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 8 1 * *` → monthly day 1 15:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 9 1 * *` → monthly day 1 16:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 10 1 1,7 *` → yearly month 1,7 day 1 17:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 11 29 * *` → monthly day 29 18:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 6 * * 1-5` → weekly 1-5 13:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 2 * * 1` → weekly Mon 09:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 2 * * 1-5` → weekly 1-5 09:00 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `30 5 * * 1` → weekly Mon 12:30 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `30 4 * * *` → daily 11:30 WIB (data-validation.yml: IDX Data Validation Schedules)
    - `0 0 * * *` → daily 07:00 WIB (fetch-cron-sheet.yml: Fetch Cron Google Sheet)
    - `0 0 * * *` → daily 07:00 WIB (fetch-sheet.yml: Fetch Google Sheet)
    - `28 16 * * *` → daily 23:28 WIB (weekly-cron-report.yml: Weekly Cron Job Failure Report)
- Writes/reads tables: `validation_results`, `validation_configs`, `idx_daily_data`, `idx_dividend`, `validation_batch_results`, `idx_company_profile`, `idx_active_company_profile`, `idx_filings`
- Upstream sources: localhost, sectors-guard.vercel.app, 127.0.0.1, fonts.googleapis.com, links.sgx.com

### sectors_idx_filing_pipeline


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 9 failures in last 97 scheduled runs
- Schedules:
    - `0 */2 * * *` → every 2h (idx_filings_v2.yaml: IDX Filings V2)
    - `0 3 1 * *` → monthly day 1 10:00 WIB (refresh_company_map.yaml: Refresh Company Map)
- Writes/reads tables: `idx_filings`, `idx_company_report`
- Upstream sources: www.idx.co.id

### sectors_idx_high_low_price


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-11 (success), 1 failures in last 100 scheduled runs
- Schedules:
    - `0 4,11 * * 1-5` → weekly 1-5 11:00 WIB (main_batch3.yml: All Time Price Data Fetching batch 3)
- Writes/reads tables: `idx_all_time_price`, `idx_active_company_profile`

### sectors_idx_highlights


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (failure), 6 failures in last 57 scheduled runs
- Schedules:
    - `0 1 * * 6` → weekly Sat 08:00 WIB (main.yml: Generate idx highlights content for idx)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: storage.googleapis.com

### sectors_idx_profile_updater


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-11 (success), 0 failures in last 98 scheduled runs
- Schedules:
    - `0 18 */3 * *` → monthly day */3 01:00 WIB (actions.yaml: run main.py)
    - `0 0 7 * *` → monthly day 7 07:00 WIB (shareholders-scraper-batch-1.yaml: Scrape Shareholders Data Batch 1)
    - `0 0 14 * *` → monthly day 14 07:00 WIB (shareholders-scraper-batch-2.yaml: Scrape Shareholders Data Batch 2)
    - `0 0 21 * *` → monthly day 21 07:00 WIB (shareholders-scraper-batch-3.yaml: Scrape Shareholders Data Batch 3)
    - `0 0 28 * *` → monthly day 28 07:00 WIB (shareholders-scraper-batch-4.yaml: Scrape Shareholders Data Batch 4)
    - `0 18 * * 1-5` → weekly 1-5 01:00 WIB (update_delisting_date.yaml: Update Delisting Dates Daily)
- Writes/reads tables: `idx_company_profile`, `idx_active_company_profile`, `idx_ipo_details`
- Upstream sources: www.idx.co.id

### sectors_idx_shareholders_composition


- Visibility: private | Language: Python | Last push: 2026-09-01
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-01 (success), 1 failures in last 4 scheduled runs
- Schedules:
    - `0 8 1 * *` → monthly day 1 15:00 WIB (main.yml: Shareholders Composition Monthly Data Scraper)
- Writes/reads tables: `idx_shareholders_composition`
- Upstream sources: web.ksei.co.id

### sectors_idx_suspension


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 5 failures in last 100 scheduled runs
- Schedules:
    - `0 3 * * *` → daily 10:00 WIB (idx_suspension_ci.yaml: Scrape IDX Suspension and Submit to Database)
- Writes/reads tables: `idx_company_profile`, `idx_suspension`
- Upstream sources: www.idx.co.id

### sectors_indices_company_list


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-11 (success), 8 failures in last 97 scheduled runs
- Schedules:
    - `0 11 * * 1-5` → weekly 1-5 18:00 WIB (main.yml: Update index daily price)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (monthly_update_indices.yaml: Update indices company list)
- Writes/reads tables: `idx_company_profile`, `index_daily_data`
- Upstream sources: www.idx.co.id

### sectors_ksei_ownership


- Visibility: private | Language: Jupyter Notebook | Last push: 2026-09-02
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-10 (success), 5 failures in last 22 scheduled runs
- Schedules:
    - `0 1 1-10 * *` → monthly day 1-10 08:00 WIB (upsert-one-percent-ownership.yml: Upsert IDX One-Percent Ownership)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: www.idx.co.id, openrouter.ai

### sectors_ms_transactions_updater


- Visibility: private | Language: Python | Last push: 2026-09-10
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-10 (success), 0 failures in last 14 scheduled runs
- Schedules:
    - `0 13 10 * *` → monthly day 10 20:00 WIB (actions.yml: Update idx_instituion_transactions with InstitutionTransactionsUpdater)
- Writes/reads tables: `idx_active_company_profile`, `idx_institution_transactions`, `idx_company_profile`
- Upstream sources: morning-star.p.rapidapi.com, ms-finance.p.rapidapi.com, www.morningstar.com

### sectors_newsletter_automation


- Visibility: public | Language: JavaScript | Last push: 2026-09-07
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-10 (success), 5 failures in last 18 scheduled runs
- Schedules:
    - `0 18 28 * *` → monthly day 28 01:00 WIB (draft-monthly-market-pulse.yml: Draft monthly market pulse)
    - `0 18 29 * *` → monthly day 29 01:00 WIB (draft-monthly-market-pulse.yml: Draft monthly market pulse)
    - `0 18 30 * *` → monthly day 30 01:00 WIB (draft-monthly-market-pulse.yml: Draft monthly market pulse)
    - `0 18 31 * *` → monthly day 31 01:00 WIB (draft-monthly-market-pulse.yml: Draft monthly market pulse)
    - `0 18 * * 3` → weekly Wed 01:00 WIB (draft-sector-spotlight.yml: Draft sector spotlight)
    - `0 18 * * 2` → weekly Tue 01:00 WIB (draft-single-company-deep-dive.yml: Draft single company deep dive)
    - `0 18 * * 3` → weekly Wed 01:00 WIB (draft-three-stock-story.yml: Draft three stock story)
    - `0 18 * * 0` → weekly Sun 01:00 WIB (draft-weekly-insights.yml: Draft weekly insights)
    - `0 0 * * 3` → weekly Wed 07:00 WIB (lifecycle-nudge-cancel.yml: Lifecycle nudge — monthly cancel check)
    - `0 0 * * 4` → weekly Thu 07:00 WIB (lifecycle-nudge-enroll.yml: Lifecycle nudge — enroll)
    - `0 20 * * 2` → weekly Tue 03:00 WIB (sync-tier-tags.yml: Sync tier tags)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: api.sectors.app, mailroom.supertype.ai, storage.googleapis.com, openrouter.ai, sectors.app, generativelanguage.googleapis.com, api.openai.com, api.anthropic.com

### sectors_sg_my_data_updater


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-12 (success), 7 failures in last 98 scheduled runs
- Schedules:
    - `0 18 * * *` → daily 01:00 WIB (daily_updater.yaml: Update daily data of sgx_companies and klse_companies)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (historical.yaml: Update sgx_klse historical financial data from yahoo finance)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (klse_yf_financial_monthly.yml: Update sgx_klse monthly financial data from yahoo finance)
    - `0 1 * * 1` → weekly Mon 08:00 WIB (main.yml: Update sgx_klse weekly financial data (dividend_ttm) from yahoo finance)
    - `0 1 * * *` → daily 08:00 WIB (mas_scraper_daily.yaml: Daily Exchange Rate Update)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (monthly_updater.yaml: Update monthly data of sgx_companies and klse_companies)
    - `0 0 * * 1` → weekly Mon 07:00 WIB (new_sector_updater.yml: SGX & KLSE Sector & Subsector Data Update)
    - `0 1 1 * *` → monthly day 1 08:00 WIB (quarterly_rates_monthly.yaml: Monthly Quarterly Rates Update)
    - `0 18 * * *` → daily 01:00 WIB (sgx_daily_data.yaml: Update SGX Daily Data)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (sgx_financials_annual.yml: SGX Annual Financials Scraper)
    - `0 1 * * 1` → weekly Mon 08:00 WIB (sgx_periodic_financial_weekly.yml: SGX Periodic Financials Weekly)
    - `0 0 */14 * *` → monthly day */14 07:00 WIB (sgx_public_date.yml: SGX Company Public Date Update)
    - `0 0 1 * *` → monthly day 1 07:00 WIB (sgx_yf_financial_monthly.yml: Update sgx_klse monthly financial data from yahoo finance)
- Writes/reads tables: `sgx_companies`, `klse_companies`, `sgx_financials_annual`
- Upstream sources: api.sgx.com, www.sgx.com, openrouter.ai, www.reitas.sg, www.bursamalaysia.com, my.bursamalaysia.com, fc.yahoo.com, eservices.mas.gov.sg

### sectors_sgx_short_sell


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-11 (success), 7 failures in last 98 scheduled runs
- Schedules:
    - `0 14 * * 1-5` → weekly 1-5 21:00 WIB (main.yml: Update daily sgx short sell transaction)
- Writes/reads tables: `sgx_short_sell`, `sgx_companies`, `sgx_daily_data`
- Upstream sources: api2.sgx.com

### sectors_social_media


- Visibility: public | Language: Python | Last push: 2026-09-11
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 0 failures in last 1 scheduled runs
- Schedules:
    - `0 1 * * 6` → weekly Sat 08:00 WIB (foreign_flow_social.yml: Generate and Post Foreign Flow Leaderboard)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: www.idx.co.id, emitennews.com, market.bisnis.com, sectors.app, www.idnfinancials.com, storage.googleapis.com, bcasekuritas.co.id, indonesiaminer.com

### sectors_stock_split_checker


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: documented
- Last scheduled run: 2026-09-12 (success), 14 failures in last 100 scheduled runs
- Schedules:
    - `0 0 * * *` → daily 07:00 WIB (main.yml: Check and Update Stock Split Daily)
- Writes/reads tables: `idx_stock_split`
- Upstream sources: www.new.sahamidx.com, sectors-news-endpoint.fly.dev

### sectors_us_insider_trading


- Visibility: public | Language: Python | Last push: 2026-09-06
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-06 (success), 1 failures in last 100 scheduled runs
- Schedules:
    - `0 0 * * 0` → weekly Sun 07:00 WIB (actions_batch_2.yml: update insider transactions related table batch 2)
    - `0 0 * * 0` → weekly Sun 07:00 WIB (actions_batch_3.yml: update insider transactions related table batch 3)
- Writes/reads tables: none detected by pattern scan, needs manual read

### sectors_workflow_backend


- Visibility: private | Language: Python | Last push: 2026-07-03
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-11 (success), 12 failures in last 100 scheduled runs
- Schedules:
    - `0 0 1 * *` → monthly day 1 07:00 WIB (cleanup-wa-images.yaml: Cleanup WhatsApp Images)
    - `0 12 * * 1-5` → weekly 1-5 19:00 WIB (daily-workflows.yml: Daily Workflows)
- Writes/reads tables: none detected by pattern scan, needs manual read
- Upstream sources: sectors.app, api.sectors.app, api.telegram.org, hook1..., hook2..., storage.googleapis.com, www.googleapis.com, docs.google.com

### sectors_yf_data_updater


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: commented out
- Last scheduled run: 2026-09-12 (success), 13 failures in last 100 scheduled runs
- Schedules:
    - `0 11 * * 1-5` → weekly 1-5 18:00 WIB (idx_daily_data.yaml: Update idx daily data)
    - `0 1 * * 6` → weekly Sat 08:00 WIB (idx_financials_annual.yaml: Update idx_financials_annual with IdxYFDataUpdater)
    - `0 1 * * 6` → weekly Sat 08:00 WIB (idx_financials_quarterly.yaml: Update idx_financials_quarterly with IdxYFDataUpdater)
    - `0 13 10 * *` → monthly day 10 20:00 WIB (idx_key_stats.yaml: Update idx_key_stats with IdxYFDataUpdater)
    - `0 1 * * 6` → weekly Sat 08:00 WIB (us_financials_annual.yaml: Update US financials_annual with USYFDataUpdater)
    - `0 1 * * 6` → weekly Sat 08:00 WIB (us_financials_quarterly.yaml: Update US financials_quarterly with USYFDataUpdater)
    - `0 13 10 * *` → monthly day 10 20:00 WIB (us_key_stats.yaml: Update US key_stats with USYFDataUpdater)
- Writes/reads tables: `idx_active_company_profile`, `idx_ipo_perf`, `idx_daily_data`, `idx_company_profile`, `idx_stock_split_cumulative`
- Upstream sources: finance.yahoo.com

### sgx_buyback_pipeline


- Visibility: public | Language: Python | Last push: 2026-09-12
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-09-12 (success), 6 failures in last 96 scheduled runs
- Schedules:
    - `10 6 * * *` → daily 13:10 WIB (management_tracking.yaml: Track SGX Management)
    - `0 21 1 * *` → monthly day 1 04:00 WIB (refresh_sgx_companies.yaml: Refresh SGX Companies)
    - `30 3 * * *` → daily 10:30 WIB (sgx_agm_scraper.yaml: Scrape SGX AGM)
    - `10 5 * * *` → daily 12:10 WIB (sgx_buyback_scraper.yaml: Scrape SGX BuyBack)
    - `10 2 * * *` → daily 09:10 WIB (sgx_filings_scraper.yaml: Scrape SGX Filings)
    - `0 9 1 * *` → monthly day 1 16:00 WIB (sgx_management.yaml: Scrape SGX Management)
    - `10 3 * * *` → daily 10:10 WIB (sgx_reit_transaction_scraper.yaml: Scrape SGX REIT Property Transaction)
    - `10 4 * * *` → daily 11:10 WIB (sgx_takeover_scraper.yaml: Scrape SGX Takeover Offers)
    - `0 18 * * 1-5` → weekly 1-5 01:00 WIB (sgx_upcoming_dividend_scraper.yaml: Scrape SGX Upcoming Dividend)
    - `0 7 * * *` → daily 14:00 WIB (shareholders_tracking.yaml: Track SGX Shareholders)
    - `0 8 1 * *` → monthly day 1 15:00 WIB (sync_screener_shareholders.yaml: Sync SGX Screener Shareholders)
- Writes/reads tables: `sgx_buybacks`
- Upstream sources: links.sgx.com, www.sgx.com, api.sgx.com, api.frankfurter.app, sias.org.sg

### summarize-agm-result


- Visibility: public | Language: Python | Last push: 2026-09-08
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-08-24 (success), 19 failures in last 64 scheduled runs
- Schedules:
    - `0 0 * 4-6 *` → yearly month 4-6 day * 07:00 WIB (ci.yml: CI)
    - `0 0 * 1-3,7-12 1` → weekly Mon 07:00 WIB (ci.yml: CI)
- Writes/reads tables: `idx_agm`
- Upstream sources: www.idx.co.id, openrouter.ai, api.openai.com, generativelanguage.googleapis.com

## Stale pipelines

Cron still defined, but the last scheduled run is older than 2026-06-01. Either deliberately paused, silently disabled by GitHub after inactivity, or broken. Each needs a keep-or-kill decision before it goes in the KB.

### mutual_fund_pipeline

Pipeline to get equity mutual fund in Indonesia

- Visibility: private | Language: Python | Last push: 2026-04-16
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-03-20 (failure), 2 failures in last 2 scheduled runs
- Schedules:
    - `0 0 15,20 * *` → monthly day 15,20 07:00 WIB (scraper.yml: Mutual Fund FFS Scraper)

### sectors_analyst_rating_scraper

Scraping program for technical and analyst rating of Stocks

- Visibility: private | Language: Python | Last push: 2026-05-07
- In `sectors-kb`: commented out
- Last scheduled run: 2026-05-02 (success), 90 failures in last 95 scheduled runs
- Schedules:
    - `0 10 * * *` → daily 17:00 WIB (scrape_technical_rating_daily.yaml: Scrape Technical Rating Daily)

### sectors_forecast_growth_rate


- Visibility: public | Language: Python | Last push: 2025-07-16
- In `sectors-kb`: documented
- Last scheduled run: 2025-10-01 (failure), 1 failures in last 1 scheduled runs
- Schedules:
    - `0 0 1 1,4,7,10 *` → yearly month 1,4,7,10 day 1 07:00 WIB (trading_view_action.yml: Update trading view forecast)

### sectors_idx_fear_and_greed_index


- Visibility: public | Language: Jupyter Notebook | Last push: 2026-02-02
- In `sectors-kb`: **not documented**
- Last scheduled run: 2026-02-02 (success), 6 failures in last 100 scheduled runs
- Schedules:
    - `0 18 * * 1-5` → weekly 1-5 01:00 WIB (calculate_fear_and_greed.yaml: Update Fear and Greed Data)

### sectors_price_anomaly_updater


- Visibility: public | Language: Python | Last push: 2026-04-27
- In `sectors-kb`: commented out
- Last scheduled run: 2026-04-27 (success), 0 failures in last 100 scheduled runs
- Schedules:
    - `0 3 * * 2-6` → weekly 2-6 10:00 WIB (mcap_handler.yml: Incomplete Stock and Null Market Cap Handler)
    - `0 4 * * 1-5` → weekly 1-5 11:00 WIB (null_data_handler.yml: Null Price Notification)

## Cron defined, never ran on schedule

A `cron:` expression exists but the Actions API records no scheduled run. Most likely dead code left in the workflow file.

### sectors_dcf_calculation


- Visibility: public | Language: Python | Last push: 2024-07-23
- In `sectors-kb`: documented
- Last scheduled run: **never** (cron defined but no scheduled run recorded)
- Schedules:
    - `0 0 1 */3 *` → yearly month */3 day 1 07:00 WIB (actions.yaml: Calculate Intrinsic Value - Discounted Cash Flow)

### sectors_get_etf_data


- Visibility: private | Language: Python | Last push: 2024-06-21
- In `sectors-kb`: **not documented**
- Last scheduled run: **never** (cron defined but no scheduled run recorded)
- Schedules:
    - `0 0 7,21 * *` → monthly day 7,21 07:00 WIB (actions.yaml: run main.py)

### sectors_ic_data_updater


- Visibility: public | Language: Python | Last push: 2024-06-07
- In `sectors-kb`: **not documented**
- Last scheduled run: **never** (cron defined but no scheduled run recorded)
- Schedules:
    - `0 0 1 1 *` → yearly month 1 day 1 07:00 WIB (action.yaml: investing.py)

### sectors_us_cron


- Visibility: public | Language: Python | Last push: 2024-06-21
- In `sectors-kb`: **not documented**
- Last scheduled run: **never** (cron defined but no scheduled run recorded)
- Schedules:
    - `0 0 * * 2-6` → weekly 2-6 07:00 WIB (update_price_chg.yml: Execute update_price_chg_30d and update_price_chg_60d)

## Documented but not scheduled

Repos named in the current `sectors-kb` pipeline doc that have no active cron. Manual-run pipelines, or entries that outlived their pipeline.

- `sectors_company_revenue_breakdown` (inside a comment block), last push 2025-07-16
- `sectors_get_company_desc`, last push 2025-09-01
- `sectors_get_market_cap_worldwide_data`, last push 2026-09-03
- `sectors_get_resized_logo`, last push 2025-01-23
- `sectors_news` (inside a comment block), last push 2026-09-12
- `sectors_news_endpoint` (inside a comment block), last push 2026-06-26
- `sectors_sankey_processing` (inside a comment block), last push 2025-06-03
- `sectors_wsj_data_updater`, last push 2025-01-23

## Known limits of this sweep

- Only GitHub Actions cron is covered. Supabase cron jobs, Fly.io schedulers and anything triggered from the app are not visible here and have to be gathered separately. The existing `sectors-kb` doc mentions Supabase cron jobs, so this inventory is incomplete by construction.
- Table detection is a regex over `.table(...)`, `.from_(...)` and `.from(...)`. Pipelines that build the client dynamically, use raw SQL, or write through an API show as none detected. That is a detection gap, not evidence the pipeline writes nothing.
- Upstream hosts are every non-boilerplate URL found in code, so the list can include a documentation link alongside the real data source. Treat as a lead, not a fact.
- Repos are classed live or stale purely on last scheduled run date. A pipeline that runs monthly and last ran in May reads as stale here and may be perfectly healthy.
- Failure counts cover at most the last 100 scheduled runs, so a high count on a frequent pipeline covers days while the same count on a weekly one covers years.