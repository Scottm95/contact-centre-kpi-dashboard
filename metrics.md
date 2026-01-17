
# KPI Dictionary (Contact Centre KPI Dashboard)

**Grain:** one row per inbound call (call-level).  
**Period in repo:** November sample (~26k calls). Logic supports multi-month data.

| KPI | Definition | Calculation notes | Used in |
|---|---|---|---|
| Total Calls | Total inbound calls for the queue/LOB. | Count of call rows. | KPI cards, Daily trend |
| Answered Calls | Calls answered by an agent. | Calls where `abandoned_flag = 0`. | Supporting calculations |
| Abandoned Calls | Calls abandoned while waiting in queue. | Calls where `abandoned_flag = 1`. | KPI cards, Daily trend |
| Abandon Rate % | % of calls abandoned in queue. | Abandoned Calls ÷ Total Calls. | KPI cards, Daily trend |
| Avg Handle Time (AHT) | Average handling duration for answered calls. | Average of `handle_seconds` (abandoned_flag = 0). | Summary / trend |
| Avg Queue Time (Answered) | Average time spent in queue for answered calls. | Average `queue_seconds` where answered. | Queue time section |
| Avg Time to Abandon | Average queue time before abandonment. | Average `time_to_abandon_seconds` for abandoned calls. | Queue time section |
| Handle Time Bucket % | % of calls in each duration bucket (e.g., 0–60s, 1–5m, …). | Bucket `handle_seconds` then % of total calls. | Distribution section |
| Disconnect Type % | % of calls by disconnect type (customer/agent/system). | % of total calls by `disconnect_type`. | Disconnect section |
