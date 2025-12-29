# Scaling Strategy

## Kafka Scaling
*   **Throughput**: 200k events/sec.
*   **Partitions**: We use 64 partitions. This supports up to 64 concurrent consumer threads in the Flink Aggregator cluster.
*   **Retention**: We only keep data in Kafka for 7 days. Long-term storage is S3. This keeps the Kafka disks manageable.

## ClickHouse Sharding
*   **Vertical Scaling First**: A single ClickHouse node is insanely fast (can ingest millions of rows/sec). We start with 1 massive node.
*   **Replication**: We use `ReplicatedMergeTree` with 2 replicas for High Availability (if one node dies, we don't lose query ability).
*   **Sharding (Future)**: If we exceed 1 PB, we implement the `Distributed` table engine to scatter queries across 10 nodes.

## Data Lake (S3)
*   **Partitioning**: `s3://bucket/raw/YYYY/MM/DD/HH`.
*   **Format**: **Parquet**. It is columnar (like ClickHouse) and highly compressed (Snappy).
*   **Athena**: We can run SQL queries directly on S3 using AWS Athena for "Cold Data" queries that don't need sub-second speed.
