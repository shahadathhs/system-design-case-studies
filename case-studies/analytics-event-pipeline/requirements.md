# Requirements

## Functional Requirements

1.  **Ingestion**: HTTP API to accept events from Web, Mobile, and Backend servers.
2.  **Aggregation**: Calculate metrics (DAU, Revenue, Click-Through-Rate) in real-time.
3.  **Storage**:
    - Raw Data: Infinite retention (Data Lake).
    - Aggregated Data: Fast query access (Data Warehouse).
4.  **Querying**: Support ad-hoc SQL queries for Data Scientists.

## Non-Functional Requirements

1.  **Low Ingestion Latency**: The `track()` call must never block the main app.
2.  **High Throughput**: Must swallow massive write spikes.
3.  **Data Consistency**: Deduplication (Exactly-Once processing where possible).
4.  **Durability**: Zero data loss once acknowledged.
