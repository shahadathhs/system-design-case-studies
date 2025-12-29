# Trade-Off Analysis

## WebSockets vs. Long Polling
*   **Long Polling**: Easier to scale (stateless-ish). Reliable.
*   **WebSockets**: Lower latency (Full duplex). Lower overhead (No HTTP headers per message).
*   **Decision**: **WebSockets**. Chat requires sub-100ms latency and "typing" indicators which are incredibly chatty on HTTP.

## Consistency vs. Availability
*   **CAP Theorem**: We choose **AP** (Availability) mostly, but Cassandra allows tunable consistency.
*   **Decision**: For Chat logs, we lean to **CP** (Quorum writes) because "Missing Messages" is a worse UX than "Message sending... delayed".
*   For Presence (Online/Offline), we lean to **AP**. If the Online status says "Active" but user just disconnected, it's fine.

## Internal ID vs. UUID
*   **Int (Auto-inc)**: Easy, but leaks business size (`/msg/500` -> `/msg/501`). Hard to shard.
*   **UUID v4**: Huge, random. Fragmentation in DB indexes.
*   **Snowflake (Chosen)**: K-Sortable (Time ordered). Shard-safe. 64-bit (Smaller than UUID).
