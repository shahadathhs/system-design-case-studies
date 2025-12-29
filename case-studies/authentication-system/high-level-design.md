# High-Level Design

## Architecture

We use a **Microservice Architecture** where the Auth Service is a standalone component.

### Components

1.  **Client (Web/Mobile)**: Holds the tokens.
2.  **API Gateway**: The entry point. Handles simple rate limiting and initial routing.
3.  **Auth Service**: Node.js/NestJS cluster. Handles business logic.
4.  **User DB (PostgreSQL)**: Stores user profiles and hashed credentials.
5.  **Token Store (Redis)**: Stores Refresh Token allow-list (or deny-list) and short-lived session data.
6.  **Internal Services**: Other microservices (Billing, Chat) that need to know "Who is this user?".

## Request Flow: The Login

1.  **Client** POSTs credentials over HTTPS to `api.domain.com/auth/login`.
2.  **Auth Service** hashes the password and compares it with the DB.
3.  **Auth Service** generates a JWT Pair:
    - **Access Token**: Short-lived (15 min). Stateless (Self-contained).
    - **Refresh Token**: Long-lived (7 days). Stateful (Stored in Redis + DB).
4.  **Auth Service** returns tokens to Client.

## Integration: Verifying Requests

- **Stateless Verification (Fast)**: Service A receives a request with an Access Token. It verifies the **Digital Signature** using the public key (RS256). It does **NOT** call the Auth Service.
  - _Pro_: Zero latency overhead.
  - _Con_: Cannot revoke access instantly (must wait 15 mins for expiry).
- **Stateful Verification (Start of session)**: Usually handled at the Gateway level for critical operations. Checks Redis to ensure the user hasn't been banned.

## Diagram

[Link to Diagram](../../diagrams/authentication-system.md)

