# Low-Level Design

## The Fan-Out Pattern (Broadcasts)
**Challenge**: Marketing wants to send "Black Friday Sale" to 1 Million Users.
**Naive Approach**: A loop in the API handler. `for user in users: send()`.
**Problem**: The API times out. If it crashes at user #50,000, we don't know who received it.

**Solution: 3-Stage Pipeline**
1.  **Ingestion**: API accepts simple command: `Broacast { template: 'sale', segment: 'all_users' }`. Pushes **1** message to `broadcast_queue`.
2.  **Splitter Strategy**:
    *   `BroadcastWorker` picks up the job.
    *   Streams User IDs from DB in chunks of 1,000.
    *   Publishes 1,000 "Send" jobs to `notification_queue`.
    *   *Result*: 1 Broadcast Job → 1,000 Batch Jobs (Total 1M users).
3.  **Sender Workers**: Massive pool of workers process the `notification_queue` in parallel.

## Idempotency & Deduplication
*   **Token**: `idempotency_key = hash(user_id + campaign_id)`.
*   **Check**: Before sending, Worker checks Redis: `SETNX key "sent"`.
    *   If returns 1 (True): Proceed.
    *   If returns 0 (False): Already sent, discard.

## Rate Limiting (The "Thundering Herd" on Downstream)
We must not crash our own servers or get banned by AWS SES.
*   **Global Rate Limits**: Implementation using **Redis Token Bucket**.
*   **Limit**: Max 1,000 emails/sec across all workers.
*   **Mechanism**: Workers request a "permit" from Redis before calling the 3rd party API. If no permit, they sleep/retry.

## Security
*   **PII**: Do not store message content (Reset Token) in plain text logs.
*   **Webhooks**: Validate signatures from Twilio/SendGrid (`X-Twilio-Signature`) to prevent fake status updates.
