# SQL vs. NoSQL

## The Decision Framework
The choice between Relational (SQL) and Non-Relational (NoSQL) databases is often driven by data structure and access patterns, not just "scale."

## SQL (Relational)
*   **Examples**: PostgreSQL, MySQL.
*   **Structure**: Strictly defined schemas, normalized data to reduce redundancy.
*   **Strengths**:
    *   **ACID Compliance**: reliable transactions are critical for financial data.
    *   **Complex Queries**: `JOIN`s allow powerful ad-hoc reporting and flexible data retrieval.
    *   **Data Integrity**: Foreign keys enforce valid relationships.
*   **Weaknesses**:
    *   **Vertical Scaling**: Harder to scale writes horizontally (sharding is complex and often manual).
    *   **Rigidness**: Schema migrations on huge tables can lock the DB or cause downtime.

**When to use**:
*   Core business data (Users, Billing, Orders).
*   Applications where data integrity and relationships are paramount.
*   *Default Choice*: "Use Postgres until you have a specific reason not to."

## NoSQL (Non-Relational)
Broadly categorized into four types:

### 1. Document Stores
*   **Examples**: MongoDB, Firestore.
*   **Structure**: JSON-like documents. Flexible schema.
*   **When to use**: Content Management, Catalogs, profiles where fields vary wildly.
*   **Trade-off**: limited `JOIN` capabilities; data is often denormalized (duplicated) for read speed.

### 2. Key-Value Stores
*   **Examples**: Redis, DynamoDB.
*   **Structure**: Hash map. O(1) access.
*   **When to use**: Caching, Session management, Shopping Carts, Preferences.
*   **Trade-off**: Very limited querying abilities (usually only by Key).

### 3. Wide-Column Stores
*   **Examples**: Cassandra, HBase.
*   **Structure**: 2D Key-Value store. Optimized for massive write throughput.
*   **When to use**: Time-series log data, IoT sensor feeds, Chat history.
*   **Trade-off**: Modeling is extremely difficult; you must model for specific queries.

### 4. Graph Databases
*   **Examples**: Neo4j.
*   **Structure**: Nodes and Edges.
*   **When to use**: Social networks, Recommendation engines, Fraud detection rings.
*   **Trade-off**: Niche use case, scaling is distinct.

## Polyglot Persistence
Modern systems rarely use just one.
*   **PostgreSQL** for Users and Orders.
*   **Redis** for Caching and Sessions.
*   **Elasticsearch** for Full-text Search.
*   **S3** for Blobs/Files.

## Migration & Hybrid Guidelines
*   **PostgreSQL JSONB**: Postgres supports JSON documents. It often eliminates the need for MongoDB in early-stage projects, giving you SQL reliability + NoSQL flexibility.
*   **Don't optimize for scale you don't have**: Sharding Postgres is painful. Only move to NoSQL for scale if you are exceeding the write limits of a vertically scaled Postgres instance (which is very high).
