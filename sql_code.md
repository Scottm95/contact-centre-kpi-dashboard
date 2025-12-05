# SQL Code Used for Contact Centre KPI Analysis

---

## 1. Create `fact_calls` table from the Genesys export

```sql
-- 1. Create fact_calls table
CREATE TABLE fact_calls AS 
SELECT
    call_date,
    "Media Type" AS media_type,
    "Direction" AS direction,
    "Initial Direction" AS initial_direction,
    "Disconnect Type" AS disconnect_type,
    duration_seconds,
    queue_seconds,
    talk_seconds,
    hold_seconds,
    acw_seconds,
    handle_seconds,
    time_to_abandon_seconds,
    abandoned_flag
FROM calls_subset;



-- 2. Daily KPI view
SELECT
    call_date,

    -- Volumes
    COUNT(*) AS total_calls,
    SUM(abandoned_flag) AS abandoned_calls,
    ROUND(100.0 * SUM(abandoned_flag) / COUNT(*), 1) AS percent_abandoned,

    -- Time KPIs
    ROUND(AVG(handle_seconds), 1) AS average_handle_time_seconds,
    ROUND(AVG(handle_seconds) / 60.0, 1) AS average_handle_time_minutes,

    ROUND(AVG(queue_seconds), 1) AS average_queue_time_seconds,

    ROUND(AVG(talk_seconds), 1) AS average_talk_time_seconds,
    ROUND(AVG(talk_seconds) / 60.0, 1) AS average_talk_time_minutes,

    ROUND(AVG(hold_seconds), 1) AS average_hold_time_seconds,
    ROUND(AVG(hold_seconds) / 60.0, 1) AS average_hold_time_minutes,

    ROUND(AVG(acw_seconds), 1) AS average_acw_time_seconds,
    ROUND(AVG(acw_seconds) / 60.0, 1) AS average_acw_time_minutes
FROM fact_calls
GROUP BY call_date
ORDER BY call_date;



-- 3. Overall KPI view
SELECT 
    COUNT(*) AS total_calls,
    SUM(abandoned_flag) AS total_abandoned_calls,
    ROUND(100.0 * SUM(abandoned_flag) / COUNT(*), 1) AS percent_abandoned,

    ROUND(AVG(handle_seconds), 1) AS average_handle_time_seconds,
    ROUND(AVG(handle_seconds) / 60.0, 1) AS average_handle_time_minutes,

    ROUND(AVG(queue_seconds), 1) AS average_queue_time_seconds,

    ROUND(AVG(talk_seconds), 1) AS average_talk_time_seconds,
    ROUND(AVG(talk_seconds) / 60.0, 1) AS average_talk_time_minutes,

    ROUND(AVG(hold_seconds), 1) AS average_hold_time_seconds,
    ROUND(AVG(hold_seconds) / 60.0, 1) AS average_hold_time_minutes,

    ROUND(AVG(acw_seconds), 1) AS average_acw_time_seconds,
    ROUND(AVG(acw_seconds) / 60.0, 1) AS average_acw_time_minutes
FROM fact_calls;



-- 4.1 Handle time range
SELECT 
    MAX(handle_seconds),
    MIN(handle_seconds),
    AVG(handle_seconds)
FROM fact_calls;



-- 4.2 Handle time completeness check
SELECT 
    COUNT(*) AS total_calls,
    COUNT(handle_seconds) AS calls_with_handle_time,
    COUNT(*) - COUNT(handle_seconds) AS calls_missing_handle
FROM fact_calls;



-- 5. Handle time buckets
WITH bucketed AS (
    SELECT 
        CASE 
            WHEN handle_seconds < 60 THEN '0–60s'
            WHEN handle_seconds < 300 THEN '1–5 mins'
            WHEN handle_seconds < 900 THEN '5–15 mins'
            WHEN handle_seconds < 1800 THEN '15–30 mins'
            WHEN handle_seconds < 3600 THEN '30–60 mins'
            ELSE '60+ mins'
        END AS handle_bucket
    FROM fact_calls
    WHERE handle_seconds IS NOT NULL
)
SELECT 
    handle_bucket,
    COUNT(*) AS calls_in_bucket,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER(), 1) AS percent_of_calls
FROM bucketed
GROUP BY handle_bucket
ORDER BY CASE handle_bucket
    WHEN '0–60s' THEN 1
    WHEN '1–5 mins' THEN 2
    WHEN '5–15 mins' THEN 3
    WHEN '15–30 mins' THEN 4
    WHEN '30–60 mins' THEN 5
    ELSE 6
END;



-- 6. Abandoned call summary
SELECT
    COUNT(*) AS total_calls,
    SUM(abandoned_flag) AS total_abandoned,
    COUNT(*) - SUM(abandoned_flag) AS total_answered
FROM fact_calls;



-- 7. Average queue time: abandoned vs answered
SELECT
    CASE 
        WHEN abandoned_flag = 1 THEN 'Abandoned'
        ELSE 'Answered'
    END AS call_outcome,
    
    COUNT(*) AS total_calls,
    ROUND(AVG(queue_seconds), 1) AS average_queue_time_seconds
FROM fact_calls
GROUP BY 
    CASE 
        WHEN abandoned_flag = 1 THEN 'Abandoned'
        ELSE 'Answered'
    END
ORDER BY call_outcome;



-- 8. Time-to-abandon buckets
WITH abandon_bucketed AS (
    SELECT 
        CASE 
            WHEN time_to_abandon_seconds < 5   THEN '<5s'
            WHEN time_to_abandon_seconds < 10  THEN '5–10s'
            WHEN time_to_abandon_seconds < 20  THEN '10–20s'
            WHEN time_to_abandon_seconds < 30  THEN '20–30s'
            WHEN time_to_abandon_seconds < 60  THEN '30–60s'
            ELSE '60s+'
        END AS abandon_bucket
    FROM fact_calls
    WHERE abandoned_flag = 1
)
SELECT 
    abandon_bucket,
    COUNT(*) AS abandons_in_bucket,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER(), 1) AS percent_of_abandons
FROM abandon_bucketed
GROUP BY abandon_bucket
ORDER BY CASE abandon_bucket
    WHEN '<5s' THEN 1
    WHEN '5–10s' THEN 2
    WHEN '10–20s' THEN 3
    WHEN '20–30s' THEN 4
    WHEN '30–60s' THEN 5
    ELSE 6
END;


-- 9. Disconnect type breakdown
SELECT
    disconnect_type,
    COUNT(*) AS total_disconnects,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER(), 1) AS percent_of_total
FROM fact_calls
GROUP BY disconnect_type
ORDER BY total_disconnects DESC;
