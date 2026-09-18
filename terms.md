# Sectors term and metric definitions

Prepared 2026-09-12. Every formula below was read out of source: the `supabase-backend` migration SQL, pipeline code in the `supertypeai` org, or the Next.js app at `~/Desktop/sectors`. Nothing here is inferred from a column name or a label.

Each entry names the exact file where the definition lives, so it can be re-verified when the code changes. Where the same term carries different definitions in different layers, that is stated rather than resolved, because resolving it is a product decision, not a documentation one.

## How to read a definition's authority

A number reaches a customer through four layers, and any of them can redefine it:

1. **Pipeline code** writes raw values into a table. Definitions that live here are invisible to SQL.
2. **Table columns** hold what the pipeline wrote.
3. **Views and materialized views** compute derived metrics from those columns. Most valuation metrics live here.
4. **The app or API** can relabel or recompute on the way out.

When the layers disagree, the customer sees layer 4. Section 8 lists every disagreement found.

## 1. Coverage and universe

**Active company**
`delisting_date IS NULL` on `idx_company_profile`. That is the whole test.
Source: `idx_active_company_profile` view, `supabase/migrations/20260806103209_remote_schema.sql`.
Consequence for support: "how many companies do you cover" is answered by counting this view, and a company stays in it until a delisting date is written. If the profile pipeline misses a delisting, the company stays "active".

**Public float / free float**
The largest `share_percentage` among shareholder entries whose `name` is exactly the string `'Public'`, read from the `shareholders` JSONB on the company profile.
Source: `public_float` CTE, `supabase/migrations/20260810154425_20260810224335_update_idx_financial_views.sql` line 1289.
Caution: this depends on an exact string match. A company whose filing labels the same block "Publik", "Public Shareholders", or anything else yields a null free float rather than a wrong one. If a customer reports a missing free float, this is the first thing to check.

## 2. Price and market capitalization

**Market cap**
Not computed in SQL. It is written by the pipeline into `idx_daily_data.market_cap`, by one of three methods, recorded per row in `mcap_method`:

- **Method 1**: taken directly from Yahoo Finance's `marketCap` for the latest date.
- **Method 2**: back-filled for earlier dates as `close x (marketCap / latest close)`, which is an implied share count from the API.
- **Method 3**: back-filled as `close x share count held in the database`, used when the API returns nothing.

Source: `sectors_yf_data_updater/yfdataupdater.py` lines 503 to 533.

This is the single most important definition in this document. Historical market cap is mostly **derived by holding one share count constant across time**, not a point-in-time actual. A company that issued shares mid-history will show a back-filled market cap that never reflects the share count on those dates. When a customer says your historical market cap disagrees with another provider, this is almost always why, and `mcap_method` tells you which path produced the row.

**Daily close change**
`(latest_close - previous_close) / previous_close`, where previous close is the most recent trading date strictly before the latest.
Source: same migration, line 628 region.

**Price change over 7 / 14 / 30 / 365 days**
`(latest close - close at window start) / close at window start`. Returned as a fraction, not a percentage.
Source: same migration, line 521 onward.

## 3. Valuation multiples

All computed in `idx_calc_metrics_daily`, source `supabase/migrations/20260810154425_20260810224335_update_idx_financial_views.sql`.

| Term | Formula | Note |
|---|---|---|
| `pe_ttm` | `market_cap / earnings_ttm` | Market-cap based, null when earnings are zero |
| `ps_ttm` | `market_cap / revenue_ttm` | Null when revenue is zero |
| `pb_mrq` | `market_cap / book_value_equity` (most recent quarter) | No zero guard, so a zero book value yields infinity rather than null |
| `pcf_ttm` | `market_cap / operating_cashflow_ttm` | |
| `enterprise_to_revenue` | `(market_cap + total_debt - cash) / revenue_ttm` | |
| `enterprise_to_ebitda` | `(market_cap + total_debt - cash) / ebitda_ttm` | Returns **0**, not null, when EBITDA is zero |
| `forward_pe` | `latest_close / forward_eps` | **Price based, not market-cap based** |
| `peg_ratio` | `pe_ttm / (yoy_quarter_earnings_growth x 100)` | Growth is a single quarter year-over-year, not a multi-year rate |

Three of these deserve a support note.

`pe_ttm` divides market cap by total earnings, while `forward_pe` divides price per share by EPS. Both are standard, and they agree when share count is consistent, but they are computed on different bases. A customer comparing the two is not comparing like with like.

`enterprise_to_ebitda` returning zero for a zero-EBITDA company means a screener sorting ascending puts loss-making companies at the top. Worth knowing before explaining a screener result.

`peg_ratio` uses one quarter's year-over-year earnings growth. The conventional PEG uses a longer-run growth rate. For a company with a lumpy quarter, this number will look extreme and it is not a bug.

## 4. Dividends, where three definitions coexist

This is the area most likely to produce a customer dispute, because "dividend yield" means three different things in three places.

**`yield_ttm`** (in `idx_company_report`)
`sum(dps_split) / latest_close`, that is split-adjusted dividend per share over the trailing period divided by the latest close.
Source: same migration, line 782.

**`dividend_yield`** (in the comprehensive report)
`COALESCE(calculated_dividend_ttm, dividend_ttm) / close`.
Source: same migration, line 1617.

**`forward_dividend_yield`** (in the app's dividend screener)
Not forward-looking at all. It is `total_yield` for the **most recent completed year** pulled out of the `historical_dividends` JSONB.
Source: `src/app/(screeners)/indonesia/top-dividend-stocks-indonesia/page.tsx` lines 76 to 90, labelled "Forward Dividend Yield" in `screenerMobile.ts`.

A customer asking why the "forward" yield does not match a forward estimate elsewhere is right to ask. The label does not describe the calculation. Flagging this for a product decision rather than a documentation fix.

**`payout_ratio`**
`dividend_ttm / (net_income_ttm / current_shares)` when net income is positive, otherwise a stored fallback value.
Source: same migration, line 1614.

## 5. Foreign flow and broker activity

**Foreign net**
`sum(f_bval) - sum(f_sval)` from `idx_broker_summary_daily`, aggregated by day, by symbol, by broker, or market-wide depending on the view.
Source: `supabase/migrations/20260827054516_ff_foreign_flow_mv.sql`.

Views: `idx_ff_market_daily` (whole market), `idx_ff_symbol_daily` (per symbol), `idx_ff_broker_daily` (per broker), `idx_ff_broker_symbol_daily` (per broker per symbol).

**Foreign activity** is the same two columns added rather than subtracted, that is gross foreign turnover.

**Critical definitional rule**: foreign always means the exchange's own per-row flag, `f_bval` and `f_sval`, which is a beneficial-owner measure and matches a published IDX foreign-flow print. It must never be measured from `idx_broker_registry.is_foreign`, which is the executing broker's domicile and books an Indonesian family office trading through a foreign house as foreign money. Over 2026 the wrong definition returned -79.5T against the correct -92.6T. This rule is already documented in the `sectors-dbquery` skill and is repeated here because it is the definition most likely to be got wrong by someone writing a new query.

**Freshness note**: `refresh_ff_mv` runs weekdays 20:30 WIB, after market close. Foreign flow for the current session is not available during trading hours. This is the answer to "why is today's foreign flow empty".

**Sanctioned pipeline-timing answer (business, 2026-09-15)**: customers building an end-of-day pipeline should be told to schedule their run **around 20:00 WIB**, by which point EOD data is finalised. Note the tension with the line above: the `refresh_ff_mv` cron is `30 13 * * 1-5`, which is 20:30 WIB, so a run started at 20:00 can beat the foreign flow refresh. For a customer whose pipeline specifically pulls foreign flow, 21:00 WIB is the safe instruction. Confirm which figure is the one to publish before this reaches a KB page.

**Market-wide aggregate, API versus database (business, 2026-09-15)**: there is **no market-wide aggregate foreign flow endpoint** on the API today, so a customer must sum per-ticker calls themselves. A market-wide endpoint is under consideration. This is an API-layer gap, not a data gap: `idx_ff_market_daily` already computes the whole-market figure in the database, which is why an internal query can answer in one shot while a customer cannot. Keep the two straight when answering.

## 6. Financial statement aggregates

**TTM (trailing twelve months)**
The sum of the **four most recent quarters** by rank, `rn <= 4`. Applies to earnings, revenue, operating cashflow, EBITDA, EBIT, interest expense, operating income, diluted EPS and free cash flow. Diluted share count is averaged rather than summed.
Source: `financials_ttm` CTE, same migration, line 247.

TTM is quarter-count based, not date based. A company that has not reported for two quarters still produces a TTM figure from its last four available quarters, which may span more than twelve calendar months. Check `latest_date` alongside any TTM number.

**Year-over-year quarterly growth**
`(latest quarter - same quarter last year) / abs(same quarter last year)`, where "same quarter last year" is the fifth-ranked quarter, `rn = 5`. The absolute value in the denominator means growth from a negative base produces a signed result that can read oddly.
Source: same migration, line 344.

**Net profit margin**: `earnings / total_revenue`, null when revenue is non-positive.

**Interest coverage**: `ebit / interest_expense`.

**Debt to equity (MRQ)**: `total_debt / total_equity`.

**Quick ratio (MRQ)**: `(total_current_asset - inventory) / total_current_liabilities`, and it is **forced to null for sub-sector 19**. Someone needs to confirm which sub-sector that is and why, since it is a deliberate exclusion with no comment in the SQL.

## 7. Risk and technical measures

**Max drawdown**
The minimum drawdown value over a trailing 365-day window, coalescing nulls to zero.
Source: `max_drawdown_data` CTE, same migration, line 542.

**RSD close (relative standard deviation)**
`stddev(close) / avg(close)` over a trailing 365-day window. A coefficient of variation, used as a volatility proxy.
Source: `stdev_data` CTE, same migration, line 549.

## 8. Where the layers disagree

Every conflict found. These are the questions most likely to arrive as complaints.

| Term | Conflict |
|---|---|
| Dividend yield | Three definitions: `yield_ttm`, `dividend_yield`, and the app's `forward_dividend_yield`. Different numerators and different denominators. |
| "Forward" dividend yield | Labelled forward, computed from the most recent completed year. Not a forward estimate. |
| P/E | `pe_ttm` is market-cap based; `forward_pe` is price-per-share based. |
| Market cap, historical | Only the latest date is a real API value. Earlier dates are back-filled from a constant implied share count. `mcap_method` records which. |
| Zero-denominator handling | `pe_ttm`, `ps_ttm`, `pcf_ttm` return null. `enterprise_to_ebitda` returns 0. `pb_mrq` has no guard at all. |
| Free float | Depends on an exact `'Public'` string match in a JSONB blob. |

## 9. Gaps that need a human answer

Things that could not be resolved from code, listed for the people who know:

1. **Sub-sector 19 is excluded from quick ratio** with no comment explaining why. Which sub-sector, and is the exclusion still wanted?
2. **`calculated_dividend_ttm` versus `dividend_ttm`.** The comprehensive report prefers the former and falls back to the latter. What is the difference, and which should be quoted to a customer?
3. **Where does `forward_eps` come from?** It is written by a pipeline, so the estimate source and its vintage are not visible in SQL. Both matter for answering "whose forecast is this".
4. **Is the "Forward Dividend Yield" label intentional?** If yes, the KB documents it with a caveat. If no, it is a product fix and the KB should not enshrine it.
5. **`pb_mrq` has no zero guard.** Deliberate or an oversight?
6. **SGX, KLSE and mining terms are not covered here.** This pass covered the IDX metric surface, which is where the migration SQL is densest. The SGX and KLSE reports and the mining stack have their own definitions and need a second pass.

## Method and limits

- Formulas were read from the migration that most recently created each object, per the `Migration ref` lines in `DATABASE_SCHEMA.md`. If an object was changed directly in the cloud without a migration, this document reflects the repo rather than production. The `supabase-backend` README acknowledges cloud drift as a recurring condition, so this is a real risk, not a theoretical one.
- Only definitions reachable from the IDX metric views, the foreign flow views, the yfinance pipeline and the Next.js app were surveyed. The API layer between the database and the customer was not read, and could relabel again.
- Where a term appears in several views with the same formula, one location is cited rather than all.
