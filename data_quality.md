
# Data Preparation & Quality (Contact Centre KPI Dashboard)

The source data is a Genesys Cloud call-level export (one row per inbound call). Preparation focuses on making timing fields and flags reliable for KPI reporting.

**Grain Check** - verified the dataset is one row per call before calculations.

## Type enforcement & parsing
- Ensured date fields are correctly typed (e.g., `call_date`)
- Ensured timing fields are numeric seconds (e.g., `handle_seconds`, `queue_seconds`, `time_to_abandon_seconds`)

## KPI field rules
- Treated `abandoned_flag` as the primary answered vs abandoned indicator
- Applied consistent denominator rules for rates (% abandoned = abandoned/total calls)

## Null/edge handling
- Handled blanks/nulls in timing fields to avoid skewing averages
- Ensured abandon-time metrics only evaluate calls marked abandoned

## Categoricals
- Standardised `disconnect_type` values where needed for consistent grouping

## Sanity checks (lightweight)
- Confirmed row counts and daily totals match expectations after cleaning
- Spot-checked KPI outputs (abandon rate, average queue time, handle-time buckets) against raw distributions
