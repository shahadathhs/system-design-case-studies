# Scaling Strategy

## Horizontal Scaling of Workers
*   **Decoupling**: The API doesn't send emails. It only writes to RabbitMQ.
*   **Auto-Scaling**: Kubernetes Horizontal Pod Autoscaler (HPA) monitors Queue Depth (not CPU).
    *   If `queue_depth > 1000`, spin up more Worker Pods.
    *   If `queue_depth == 0`, scale down to min replicas.

## Database Scaling
*   **Write Heavy**: Every notification creates a log entry.
*   **Partitioning**: We partition `notification_logs` by month.
*   **Archiving**: After 1 year, we move partitions to cold storage (S3 Parquet files) to keep the active DB light.

## Rate Limit Scaling
*   **Distributed Rate Limiting**: Shared logic in Redis.
*   **Lua Scripts**: We use atomic Lua scripts in Redis to decrement bucket tokens. This ensures that even if 50 workers try to send at the exact same millisecond, we consider the global limit correctly.
