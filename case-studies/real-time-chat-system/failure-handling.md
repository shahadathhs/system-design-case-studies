# Failure Handling

## Gateway Node Failure

**Scenario**: Server holding 100k connections crashes.

1.  **Impact**: 100k users disconnect instantly.
2.  **Thundering Herd**: 100k clients try to reconnect strictly at once.
3.  **Mitigation**: **Client-Side Jitter**. Clients wait `Random(0, 30s)` before reconnecting.
4.  **State Recovery**:
    - When Redis detects the Gateway is dead (Heartbeat fail), it marks those 100k users as Offline.

## Message Loss Prevention

User sends message -> Gateway crashes before saving to DB.

- **Ack Mechanism**: Client keeps message in local queue (Redux/SQlite) with status `Sending`.
- **Wait**: Client waits for `messageAck` from Server.
- **Timeout**: If no Ack in 5s, Client retries.
- **Result**: At-least-once delivery guaranteed. Server handles deduplication via Snowflake ID.

## Cassandra Outage

- **Quorum Writes**: We use `replication_factor=3` and `write_consistency=QUORUM` (2/3 nodes must ack).
- **Impact**: Even if 1 node dies, writes succeed.
