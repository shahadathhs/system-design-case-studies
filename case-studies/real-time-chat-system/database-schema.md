# Database Schema

## Technology: Cassandra / ScyllaDB

**Why not Postgres?**

- Chat logs are **Write-Once, Read-Many**.
- Volume: 50k writes/sec requires heavy sharding in Postgres.
- Cassandra is optimized for massive write throughput and time-series queries.

## Tables (CQL)

### 1. `messages`

Partitioned by `channel_id` (Chatroom). Ordered by `message_id` (Time).

```sql
CREATE TABLE messages (
    channel_id UUID,
    bucket INT, -- Time bucket (e.g., Month) to prevent partitions getting too big
    message_id BIGINT, -- Snowflake ID
    sender_id UUID,
    content TEXT,
    created_at TIMESTAMP,
    PRIMARY KEY ((channel_id, bucket), message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);
```

- **Query**: "Get last 50 messages for Channel X" is a single disk seek.

### 2. `unseen_messages` (Counter)

To show "buffer badge" (e.g., "5 new messages").
Implementation choices:

- **Cassandra Counters**: simple but can drift.
- **Redis**: `HINCRBY user_unread_counts channel_id 1`. (Preferred for speed).

## Postgres Tables (Relational Metadata)

We still use SQL for "Entity Data".

- `users`: Profiles.
- `channels`: Metadata (Name, Topic).
- `channel_members`: `(channel_id, user_id)` mapping.
