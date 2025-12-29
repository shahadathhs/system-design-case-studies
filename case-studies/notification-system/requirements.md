# Requirements

## Functional Requirements

1.  **Multi-Channel**: Support Email, SMS, Push, In-App.
2.  **Templates**: Dynamic content insertion ("Hello {{first_name}}").
3.  **Preferences**: Users can opt-out of specific channels or categories (e.g., "No Marketing SMS").
4.  **Priority**: Critical alerts (Security) must take precedence over Marketing.
5.  **Tracking**: Track Delivery Status (Sent, Delivered, Read/Opened, Failed).
6.  **Scheduling**: "Send this email tomorrow at 9 AM".
7.  **Rate Limiting**: Prevent spamming a single user inadvertently.

## Non-Functional Requirements

1.  **Reliability**: At-least-once delivery. Don't lose OTPs.
2.  **Scalability**: Handle "Fan-out" (Sending to 1 Million users at once) without crashing.
3.  **Extensibility**: Easy to add new providers (e.g., Slack, WhatsApp).
4.  **Idempotency**: Do not double-send emails.
