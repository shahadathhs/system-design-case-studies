# Failure Handling

## What breaks first?
1.  **Redis**: If the Cache fills up, sessions might get evicted early, forcing re-logins.
2.  **Database Connection Pool**: Under load, Node.js might exhaust DB connections.

## Mitigation Strategies

### 1. Redis Failure
*   **Scenario**: Redis Master dies.
*   **Fallback**: Sentinel promotes a Follower.
*   **Impact**: A few seconds of "Internal Server Error" for logins. Token verification (stateless) continues to work fine.

### 2. Database Failure
*   **Circuit Breaker**: If DB returns timeouts, the Auth Service "trips" the breaker.
    *   **Degraded Mode**:
        *   STOP accepting new Signups/Logins (fail fast).
        *   CONTINUE verifying existing tokens (using Public Key logic).
        *   This protects the ecosystem from cascading failure.

### 3. Rate Limiting
*   **DDoS Protection**: If one IP sends 10,000 login requests, we block it at the Gateway/Cloudflare level, not the app level.
*   **Account Locking**: After 5 failed password attempts, lock account for 30 minutes to prevent brute-forcing.

## Partial Failures
*   **Event Handling**: If the "UserRegistered" event fails to publish to Kafka (Broker down).
    *   **Outbox Pattern**: We write the message to a local `outbox` table in Postgres *in the same transaction* as creating the user.
    *   A separate background process reads `outbox` and retries pushing to Kafka.
    *   *Result*: Zero data loss. Eventual consistency guaranteed.
