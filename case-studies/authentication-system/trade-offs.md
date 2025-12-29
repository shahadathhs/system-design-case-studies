# Trade-Off Analysis

## JWT vs. Server-Side Sessions

| Feature | JWT (Stateless) | Sessions (Stateful) | **Our Choice: Hybrid** |
| :--- | :--- | :--- | :--- |
| **Revocation** | Impossible without blacklisting | Instant (delete from DB) | **Access Token (JWT) + Refresh Token (Stateful)** |
| **Size** | Large (header + payload + sig) | Small (just an ID) | JWTs are small enough for headers. |
| **DB Load** | Zero (CPU only) | High (Read per request) | **Use JWTs for internal API calls to remove DB bottleneck.** |

**Decision**: We chose the **Hybrid Approach**.
*   **Access Tokens** are JWTs (RS256). This allows specific microservices to verify auth *without* hammering the Auth Service 50,000 times/sec.
*   **Refresh Tokens** are Stateful. This gives us the security control to ban users/devices centrally.

## NoSQL vs. SQL for Users
*   **Alternative Considered**: MongoDB.
*   **Reason for Rejection**: User credentials are structured, relational data. We need strong consistency checks (Constraints on Unique Email). In an Eventual Consistent DB, two users might register `bob@gmail.com` at the same time on different nodes. Postgres prevents this reliably.

## Sync vs. Async Registration
*   **Alternative**: Do everything in the HTTP handler (Stripe create, Email send).
*   **Reason for Rejection**: Too slow. Third-party APIs (Stripe) fail or time out.
*   **Decision**: Registration only touches our Local DB. Everything else is Async/Event-driven.
