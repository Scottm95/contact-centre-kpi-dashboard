
# Contact Centre KPI Analysis (Genesys export + SQL + Power BI)


## Overview
Power BI dashboard built from a Genesys Cloud call-level export (one row per inbound call) to report operational KPIs for a single queue / line of business. The repo includes an anonymised November sample (26k calls) but the approach supports multi-month reporting.



## Problem
- Stakeholders needed a clear, repeatable view of queue performance (volume, abandon rate, queue time, AHT) for one LOB.
- Raw Genesys exports required cleaning and shaping before reliable KPI reporting was possible.
- Needed both headline KPIs and distributions (queue wait/abandon behaviour, handle-time buckets, disconnect types).


## Approach
- **Data prep (Power Query):** cleaned and typed call-level fields and prepared a reporting-ready fact table (`fact_calls.csv`).
- **SQL analysis:** produced daily KPIs, monthly summary, queue-time/abandon distributions, handle-time bucketing, and disconnect breakdowns (see `sql_code.md`).
- **Power BI (model + DAX):** recreated KPI logic as measures and built a single-page dashboard for trend + distribution views.


## Tools
- **Genesys Cloud export**
- **SQL**
- **Power BI**
- **Power Query**
- **DAX**


## Results (November sample)
- Abandon rate remained stable around 9-10% with no major spikes.
- Abandoned calls typically dropped quickly (answered queue time 12s vs abandon time 6s).
- Most common handle-time bucket was 15-30 minutes (25% of calls); 60+ minutes were a small minority.
- Majority of calls ended via customer disconnect; agent disconnects next; system disconnects were a small share.



## Data model
This report uses a single call-level fact table (one row per call from the Genesys export).  
A KPI dictionary is in `metrics.md`.


## Data quality checks
Prep and validation checks are listed in `data_quality.md`.


## Screenshots
[ContactCentreDashboard.png](https://github.com/Scottm95/contact-centre-kpi-dashboard/blob/main/ContactCentreDashboard.PNG)



## Files
- `ContactCentreDashboard.png` – dashboard screenshot
- `fact_calls.csv` – cleaned call-level dataset (Nov sample)
- `sql_code.md` – SQL used for KPI calculations and distributions
- `metrics.md` – KPI definitions (dictionary)
- `data_quality.md` – cleaning/validation notes




