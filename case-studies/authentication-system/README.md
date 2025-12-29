# Authentication & Identity System

## System Overview
A centralized Identity and Access Management (IAM) service responsible for global user authentication, authorization, and session management. This system serves as the gatekeeper for all other microservices in our ecosystem.

## Real-World Usage
Comparable to **Auth0, Okta, or AWS Cognito**. It handles sign-ups, logins, social auth (OAuth), password resets, and token verification for internal services.

## Expected Scale
*   **Users**: 10 Million registered users.
*   **Daily Active Users (DAU)**: 1 Million.
*   **Throughput**: 
    *   **Login/Write Heavy**: Peaks of 2,000 requests/sec (e.g., morning rush).
    *   **Verify/Read Heavy**: Every internal API call validates tokens, requiring extremely high read throughput (50,000+ requests/sec) if not cached properly.
