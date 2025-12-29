# Scaling Strategy

## Connection Handling (C10K -> C10M)
*   **Problem**: Max TCP ports (65k) and File Descriptors.
*   **Kernel Tuning**: Increase `nofile` limit to 1 Million.
*   **Load Balancing**:
    *   Classic L4 Load Balancers don't work great for WebSockets scaling because connections are sticky.
    *   **Strategy**: Custom logic. Client requests `GET /node` from a Discovery Service. Discovery Service returns IP of a Gateway Node with < 50k connections. Client connects directly to that IP.

## Redis Bottleneck
*   **The Fan-Out Problem**: In a group of 100k users, 1 message = 100k Redis PUBLISH commands?
*   **Optimization**:
    *   **Shard Redis**: Group chat channels are hashed to specific Redis instances.
    *   **Batching**: Gateway pulls messages in batches of 50ms.

## Data Archival
Cassandra gets full.
*   **TTL**: Set Cassandra TTL to 1 year.
*   **Cold Storage**: A background job streams expired SSTables to S3 (Parquet format) for compliance/audit logs, removing them from the hot cluster.
