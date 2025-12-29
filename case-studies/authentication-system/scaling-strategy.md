# Scaling Strategy

## Phase 1: 0 - 100k Users (Monolith)

- **App**: Single Node.js instance (pm2 clustered).
- **DB**: Single Postgres instance on moderate hardware.
- **Strategy**: Vertical scaling. Just enable caching.

## Phase 2: 100k - 1M Users (Horizontal Scale)

- **App**: Stateless Auth Service behind a Load Balancer (AWS ALB). Auto-scaling group of 3-10 nodes.
- **DB**: Read Replicas.
  - Writes (Signups) go to Master.
  - Reads (Profile fetching) go to 2 Read Replicas.
- **Cache**: Redis Cluster for sessions. Offload DB hits.

## Phase 3: 10M+ Users (Optimization)

At this scale, the `users` table is huge.

- **Database Partitioning**:
  - Partition `sessions` table by range (date) to quickly drop old sessions.
  - We likely _don't_ need to shard `users` yet (Postgres handles 100M rows fine with indexes), but if needed, we shard by `user_id`.
- **Geo-Replication**:
  - Deploy Auth Service replicas in EU and US regions.
  - Use Global Accelerator.
  - **Latency**: Users verify tokens against the local cache/replica.

## Async Workflows

- **Sign-up**: The API creates the user row synchronously.
- **Post-processing**: The API pushes a "UserRegistered" event to Kafka.
  - Service B sends the "Welcome Email".
  - Service C creates the "Stripe Customer".
  - _Why?_ Keeps the Registration API response time fast (< 200ms).
