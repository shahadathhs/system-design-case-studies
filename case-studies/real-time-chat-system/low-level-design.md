# Low-Level Design

## Message Ordering (The Sequence Problem)
In a distributed system, clocks are unreliable. We cannot use `timestamp` for ordering.
*   **Solution**: **Snowflake IDs** (Twitter standard) or **KSUID**.
*   **Structure**: 64-bit integer.
    *   41 bits: Timestamp (Millisecond precision).
    *   10 bits: Machine/Node ID.
    *   12 bits: Sequence number (Per millisecond).
*   **Result**: Sortable by time, unique, distributed generation without coordination.

## Online Presence & Heartbeats
*   **Mechanism**: WebSocket connection is "live".
*   **Heartbeat**: Client pings server every 30s. Server updates Redis `last_seen` timestamp.
*   **Disconnect**: If WebSocket closes (TCP FIN), Server immediately marks `status: offline`.
*   **The "Zombie" Problem**: If network cuts (WiFi off) without TCP FIN.
    *   **TTL**: Redis key expires after 45s. If no heartbeat, user is considered offline automatically.

## Group Chat Logic
*   **Read Optimization**: We do not copy the message 100 times for 100 members.
*   **Storage**: One message row linked to `group_id`.
*   **Delivery**:
    *   Chat Service looks up `group_members` in Redis/DB.
    *   Iterates members.
    *   Publishes to Redis Channel for each *Online* member.

## Security
*   **Encryption**: TLS 1.3 (In transit).
*   **End-to-End Encryption (E2EE)**:
    *   (Optional Extension) Signal Protocol. Keys exchanged beforehand. Server stores encrypted blobs.
    *   *Note*: This case study assumes server-side history (Slack style), not strictly E2EE (WhatsApp style).
