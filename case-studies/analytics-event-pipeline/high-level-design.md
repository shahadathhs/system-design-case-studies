# High-Level Design

## Architecture

We use the **Lambda Architecture** (Batch + Speed Layer), simplified for modern tools.

### Components

1.  **Collector API**: Nginx/Go load balancers. Validates JSON schema. Pushes to Kafka.
2.  **Message Broker (Kafka)**: The durable buffer. Holds 7 days of logs.
3.  **Stream Processor (Flink/Spark Streaming / Kafka Streams)**:
    - Reads from Kafka.
    - Aggregates in windows (e.g., "Count clicks per min").
    - Writes to ClickHouse.
4.  **S3 Loader (Secor/Kafka Connect)**: Dumps raw logs to S3 (Data Lake) for backup.
5.  **OLAP DB (ClickHouse/Druid)**: Stores pre-aggregated data for sub-second dashboards.

## Data Flow

1.  **Client** POSTs JSON to `/v1/track`.
2.  **Collector API** responds `202 Accepted` immediately. Pushes to **Kafka Topic** `raw_events`.
3.  **Path A (Speed Layer)**:
    - **Consumer A** reads `raw_events`.
    - Aggregates "Page Views by Country".
    - Upserts to **ClickHouse**.
4.  **Path B (Batch/Archival Layer)**:
    - **Consumer B** buffers 100MB chunks.
    - Writes Parquet files to **S3**.
5.  **Dashboard** queries **ClickHouse** to show graphs.

## Diagram

[Link to Diagram](../../diagrams/analytics-event-pipeline.md)
