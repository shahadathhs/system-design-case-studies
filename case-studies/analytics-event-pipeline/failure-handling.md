# Failure Handling

## The "Duplicate Event" Problem

- **Cause**: Producer sends event, network lags. Producer retries. Kafka has 2 events.
- **Fix at Ingestion**: We accept duplicates in the Raw layer.
- **Fix at Query**: `SELECT count(DISTINCT user_id)` is slow.
- **Optimization**: ClickHouse `ReplacingMergeTree`.
  - It automatically removes duplicates with the same `(event_id)` in the background.
  - _Result_: Eventually consistent uniqueness.

## Stream Processor Failure

- **Checkpointing**: Flink/Spark Streaming periodically saves state (offset 5000) to RocksDB/HDFS.
- **Crash**: If the processor crashes, it restarts and resumes reading from Offset 5000.
- _Impact_: A small spike in latency (dashboard lag), but zero data loss.

## Kafka Outage

- **Durable Buffering**: The Collector API buffers in RAM. If Kafka is down for > 1 minute, the API starts dropping events to save itself (Circuit Breaker).
- **Priority**: We prioritize **Liveness** of the host application over **Accuracy** of analytics. We never crash the User's app just because our Analytics pipe is full.
