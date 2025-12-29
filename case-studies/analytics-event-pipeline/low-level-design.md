# Low-Level Design

## Data Ingestion Optimization

- **Collector API**: Go service using `fasthttp`. Zero allocations.
- **Buffering**: We do NOT write to Kafka on every request.
  - buffer events in memory (channel) for 100ms or 50 items.
  - Flush batch to Kafka.
  - _Trade-off_: If Collector hits OOM, we lose 100ms of data (Acceptable for Analytics).

## Kafka Partitioning Strategy

- **Topic**: `events_v1`.
- **Partitions**: 64.
- **Key**: `user_id`.
  - _Why?_ Ensures all events for a single user land on the same partition. This guarantees strict ordering for "Sessionization" logic (e.g., did they click 'Signup' _after_ 'Pricing'?).

## ClickHouse Optimization

ClickHouse hates small inserts.

- **Aggregator Service**: Reads from Kafka. Buffers for 5 seconds.
- **Bulk Insert**: Inserts 50,000 rows at once into ClickHouse.
- **Merging**: ClickHouse background threads merge the small parts into bigger files (LSM Tree-like).

## Handling Schema Drift

**Scenario**: Dev adds a new field `button_color`.

1.  **Strict Schema**: We use Protobuf/Avro schema registry.
2.  **Flexible Column**: We treat the payload as a JSON blob stored in a `String` column in raw tables, but extract common fields (`url`, `event_type`) to their own columns for speed.
