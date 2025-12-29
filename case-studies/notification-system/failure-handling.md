# Failure Handling

## The "Retry" Problem

Sending duplicates is bad (Annoying). Not sending is bad (Lost OTP).

- **Strategy**: Idempotent Providers.
- **Implementation**: Most providers (e.g., Stripe, SendGrid) accept an `Idempotency-Key`. We pass our internal `notification_id` as this key. If we retry, the provider sees the key and ignores the duplicate.

## Circuit Breakers

**Scenario**: SendGrid is down (500 Errors).

1.  **Detection**: Worker sees > 10% error rate in last minute.
2.  **Open Circuit**: Stop calling SendGrid.
3.  **Fallback**: Route traffic to **Twilio SendGrid Backup** or **AWS SES**.
    - _Requirement_: We must maintain accounts with multiple providers for critical paths (OTP).

## Dead Letter Queues (DLQ)

If a message fails validation or the user email is permanently bounced:

- Do NOT retry infinitely.
- After 3 attempts, move to `dead_letter_queue`.
- Admin dashboard allows inspection and "Replay" of DLQ messages.
