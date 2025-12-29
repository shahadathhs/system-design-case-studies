# Trade-Off Analysis

## Batch (Hadoop) vs. Stream (Flink)
*   **Batch**: Accurate, but results are available "Tomorrow".
*   **Stream**: Fast (Seconds), but harder to handle "Late Arriving Data".
*   **Decision**: **Lambda Architecture**.
    *   We use Streaming for "Real-time Dashboards" (Approximate).
    *   We use S3 Batch jobs for "Monthly Billing Reports" (Exact).

## ClickHouse vs. Postgres vs. Elasticsearch
*   **Postgres**: Dies after 1TB. Row-based storage is bad for aggregation.
*   **Elasticsearch**: Good for logs, but expensive at scale. "Aggregations" are memory hungry.
*   **ClickHouse**: **Chosen**. Columnar storage means querying "Sum of Revenue" only reads the "Revenue" column, ignoring the other 50 columns. It's 100x faster for this use case.

## JSON vs. Protobuf
*   **Decision**: **JSON** (at the edge).
    *   *Reason*: Easier for frontend devs to debug. We convert to Binary (Parquet/ClickHouse format) deeper in the pipeline. We trade a bit of CPU at the ingestion layer for Developer Experience.
