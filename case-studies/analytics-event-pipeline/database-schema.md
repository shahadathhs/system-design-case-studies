# Database Schema

## Technology: ClickHouse
Optimized for `SELECT count(*) FROM table WHERE time > X`.

## Tables

### 1. `events_raw` (The Firehose)
Engine: `MergeTree`.
Partitioned by Day. Ordered by `(event_type, timestamp)`.
```sql
CREATE TABLE events_raw (
    timestamp DateTime,
    event_id UUID,
    user_id UUID,
    event_type String, -- 'pageview', 'click'
    url String,
    
    -- Properties bag
    device_os String,
    browser String,
    metadata String -- JSON blob for extra fields
) 
ENGINE = MergeTree()
PARTITION BY toYYYYMMDD(timestamp)
ORDER BY (event_type, timestamp);
```
*   **ORDER BY**: Crucial. Sorting by `event_type` makes queries like "Count all pageviews" incredibly fast because data is physically co-located on disk.

### 2. `events_hourly_agg` (Materialized View)
Pre-computes stats as data arrives.
```sql
CREATE MATERIALIZED VIEW events_hourly_agg
ENGINE = SummingMergeTree()
ORDER BY (event_type, toStartOfHour(timestamp))
AS SELECT
    event_type,
    toStartOfHour(timestamp) as hour,
    count() as count
FROM events_raw
GROUP BY event_type, hour;
```
*   **Result**: When querying "Total Pageviews yesterday", ClickHouse sums up 24 rows instead of scanning 50 Million rows.

## S3 Implementation
*   Bucket: `analytics-lake`.
*   Prefix: `raw/year=2023/month=12/day=01/hour=10/chunk-001.parquet`.
