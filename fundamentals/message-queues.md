# Message Queues & Asynchronous Processing

## Why Async?
In a synchronous request (REST/HTTP), the client waits for the server to finish. If the task takes 5 seconds (e.g., generating a PDF, sending an email), the user experiences 5s of lag.
**Solution**: Accept the request, put it in a specific "bucket" (Queue), return "202 Accepted", and let a background worker process it later.

## Core Components
1.  **Producer**: The API service pushing a job.
2.  **Message Broker**: The middleware (RabbitMQ, Kafka, Redis, SQS).
3.  **Consumer (Worker)**: The background service processing the job.

## Models

### 1. Point-to-Point (Work Queues)
*   A message is processed by **exactly one** consumer.
*   **Use Case**: Resizing an uploaded image. We have 5 worker nodes; we don't want all 5 to resize the same image. One worker grabs it, processes it, and acknowledges (ACK) it.

### 2. Publish-Subscribe (Pub/Sub)
*   A message is broadcast to **all** subscribers.
*   **Use Case**: A user signs up.
    *   Service A sends "Welcome Email."
    *   Service B creates "Analytics Profile."
    *   Service C updates "Search Index."
    *   The "UserSignup" event is published once, and all 3 services consume it independently.

## Technology Choices

| Tool | Type | Best For | Trade-offs |
| :--- | :--- | :--- | :--- |
| **RabbitMQ** | General Purpose Broker | Complex routing, standard pub/sub, heavy reliability. | Lower throughput than Kafka. Harder to maintain scaling. push-based. |
| **Apache Kafka** | Distributed Streaming Platform | Massive scale (LinkedIn scale), log aggregation, event sourcing. | High complexity. Pull-based. Messages potentially replayable (retention). |
| **Redis streams** | Lightweight | Simple async jobs where full durability isn't critical. | Less reliability guarantees than Rabbit/Kafka. Memory limited. |
| **AWS SQS** | Managed Queue | Hands-off serverless setups. | Cost at scale. Vendor lock-in. |

## Key Concepts
*   **ACK/NACK**: Workers must acknowledge success. If a worker dies while processing, the broker must re-queue the message (At-least-once delivery).
*   **Dead Letter Queue (DLQ)**: If a message fails 5 times, don't retry forever. Move it to a DLQ for manual inspection so it doesn't clog the pipe.
*   **Idempotency**: Since "At-least-once" is the standard, a worker might receive the same message twice. The handler MUST be able to run twice without breaking data (e.g., `SET status='paid'` is safe; `UPDATE amount = amount - 10` is not safe without checks).
