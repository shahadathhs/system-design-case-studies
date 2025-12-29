# Consistency vs. Availability (CAP Theorem)

## The CAP Theorem
In a distributed data store, you can only provide **two** of the following three guarantees:
1.  **C**onsistency: Every read receives the most recent write or an error.
2.  **A**vailability: Every request receives a (non-error) response, without the guarantee that it contains the most recent write.
3.  **P**artition Tolerance: The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

**Reality Check**: In distributed systems (cloud), **Network Partitions (P)** are a fact of life. You cannot choose to ignore them. Therefore, you must choose between **CP** and **AP**.

### CP (Consistency + Partition Tolerance)
*   **Behavior**: If a partition occurs, the system blocks writes (or reads) to ensure data doesn't diverge. It returns an error rather than stale data.
*   **Use Case**: Banking transactions, Inventory counts.
*   **Example**: Relational Databases (PostgreSQL, MySQL) in standard replications often prioritize consistency (ACID).

### AP (Availability + Partition Tolerance)
*   **Behavior**: If a partition occurs, nodes accept writes and serve reads even if they can't talk to each other. The data might be temporarily inconsistent.
*   **Use Case**: Social media feeds, Shopping carts (sometimes), DNS.
*   **Example**: Cassandra, DynamoDB (configurable).

## Consistency Models in Practice

### Strong Consistency
*   Data is instantly replicated to all nodes before a write is confirmed.
*   **Latency**: High (must wait for consensus).
*   **Throughput**: Lower.
*   **Tool**: `read concern: 'majority'` in MongoDB, Synchronous replication in Postgres.

### Eventual Consistency
*   Writes are confirmed once the local node (or a quorum) accepts them. Propagation happens asynchronously.
*   "If no new updates are made, eventually all accesses will return the last updated value."
*   **Latency**: Low.
*   **Throughput**: High.
*   **The Problem**: A user might post a comment, refresh the page, and not see it immediately.

### Causal Consistency
*   A middle ground. Operations specifically related by causality are consistent; unrelated operations are eventually consistent.
*   Example: Comments in a thread must appear after the post they belong to.

## Real-World Application
In a microservices architecture, we often have to handle consistency across boundaries without distributed transactions (which are slow and brittle).

**Pattern: The Saga Pattern**
Instead of a distributed ACID transaction (2PC), we use a sequence of local transactions.
*   **Choreography**: Service A emits event "OrderCreated", Service B listens and does "ReserveIntegrity".
*   **Orchestration**: A central Coordinator service tells A, then B, then C what to do.
*   **Compensating Actions**: If step 3 fails, the saga runs "Undo" actions for step 2 and 1. This admits that temporary inconsistency is acceptable for high availability.
