# CI/CD Pipelines & Deployment Strategies

## What is CI/CD?
The bridge between "It works on localhost" and "It's live for users".

### CI: Continuous Integration
**Goal**: Catch bugs fast.
**Trigger**: A `git push` to a branch.
**Steps**:
1.  **Linting**: Is the code style correct? (Unused variables, etc).
2.  **Unit Tests**: Do functions return what we expect?
3.  **Build**: Does the TypeScript compile?
**Result**: If Red (Fail), you cannot merge. If Green (Pass), proceed.

### CD: Continuous Deployment / Delivery
**Goal**: Release to Production safely.
**Trigger**: Merging to `main` branch.
**Steps**:
1.  **Build Container**: Create Docker Image `api:v123`.
2.  **Push Registry**: Upload image to AWS ECR / Docker Hub.
3.  **Migration**: Run database schema changes (`npm run migrate`).
4.  **Deploy**: Tell Kubernetes/Server "Use image `api:v123` now."

## Deployment Strategies
How do we switch from Version 1 to Version 2 without kicking users off?

### 1. Rolling Deployment (Standard)
*   **Method**: In a cluster of 5 servers, update 1 at a time.
*   **Pros**: Zero downtime. Low cost (no extra servers needed).
*   **Cons**: Slow. Takes time to finish. Hard to rollback instantly.

### 2. Blue-Green Deployment
*   **Method**:
    *   **Blue (Live)**: Running V1.
    *   **Green (Idle)**: Deploy V2 here.
    *   **Switch**: Once Green is tested and ready, update the Load Balancer to point to Green.
*   **Pros**: Instant switch. Instant rollback (just point LB back to Blue).
*   **Cons**: double the cost (you need 2x servers for the duration).

### 3. Canary Deployment
*   **Method**: Deploy V2 to a small subset of users (1% or internal staff).
*   **Monitor**: Watch error rates.
*   **Promote**: If stable, increase to 10%, 50%, 100%.
*   **Pros**: Safety. If V2 has a bug, only 1% of users are annoyed.

## Managing Configs & Secrets
**Rule #1**: Never commit `.env` files to Git.

### Environment Variables
*   **In Code**: `process.env.DB_PASSWORD`.
*   **In Dev**: values from `.env` file (local on your laptop).
*   **In Prod**: Values injected by the Platform (AWS/Heroku).

### Secrets Management
*   **Problem**: Storing secrets in plain text Env Vars in the UI is risky.
*   **Solution**: **Secret Manager** (Vault, AWS Secrets Manager).
    *   App boots up -> Authenticates with AWS -> Requests secrets -> Keeps in memory.
