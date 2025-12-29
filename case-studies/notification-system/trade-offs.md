# Trade-Off Analysis

## RabbitMQ vs. Kafka vs. Redis Pub/Sub

| Feature | RabbitMQ (Chosen) | Kafka | Redis |
| :--- | :--- | :--- | :--- |
| **Model** | Push (Smart Broker) | Pull (Dumb Broker) | Push (Fire & Forget) |
| **Priority** | Native Priority Queues | Not Supported | Manual implementation |
| **Complexity** | Medium | High | Low |

**Decision**: **RabbitMQ**.
*   **Why?** We need **Priority Queues** (OTP > Marketing). RabbitMQ handles this out of the box. Kafka treats all messages in a partition sequentially.
*   **Why not Redis?** Redis Pub/Sub has no persistence. If a worker is down, the message is lost. We need durability.

## Worker Pull vs. Push
*   **Push (HTTP)**: API calls Worker via Webhook.
    *   *Refused*: If Worker is overloaded, API times out.
*   **Pull (Queue)**: Worker grabs job when ready.
    *   *Accepted*: Natural backpressure mechanism.

## Buying vs. Building
*   **Decision**: We build the *routing logic* but allow **Twilio/SendGrid** to handle the *last mile*.
*   **Reason**: Managing IP reputation for email delivery is a full-time business. We cannot compete with SendGrid's deliverability rates.
