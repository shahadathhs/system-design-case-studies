# Database Sharding

Database sharding is a method for distributing a single dataset across multiple databases, which can then be hosted on multiple servers. This allows a database to scale horizontally by spreading the load and data over many machines, rather than relying on a single, larger server (vertical scaling). Each individual database is called a "shard."

## Why Shard a Database?

*   **Scalability:** Allows horizontal scaling to handle more data and higher request throughput than a single database server.
*   **Performance:** By distributing the data, queries operate on smaller datasets, leading to faster response times.
*   **Availability:** A failure in one shard does not necessarily bring down the entire system, potentially improving overall availability.
*   **Reduced Cost:** Running many smaller, commodity servers can be cheaper than one very large, high-end server.

## Sharding Strategies

Choosing the right sharding strategy is critical and depends heavily on the application's access patterns and future growth.

### 1. Key-Based (Hash-Based) Sharding

*   **How it works:** A hash function is applied to a primary key (or another chosen column) of the data, and the result of the hash function determines which shard the data belongs to.
*   **Pros:** Simple to implement, provides a relatively even distribution of data across shards.
*   **Cons:** Adding or removing shards (resizing the cluster) is difficult, as it changes the hash function's output for many keys, requiring extensive data rebalancing. Hot spots can still occur if certain hash values are accessed disproportionately.

### 2. Range-Based Sharding

*   **How it works:** Data is distributed based on a range of values of a chosen shard key. For example, users with IDs 1-1000 go to Shard A, 1001-2000 go to Shard B, and so on.
*   **Pros:** Easy to implement range queries (e.g., "get all users created in January") as they can be directed to specific shards. Easy to add new shards for new ranges.
*   **Cons:** Can lead to uneven data distribution and "hot spots." If the chosen range key is temporal (like a timestamp), newer data might all land on a single shard, overwhelming it.

### 3. Directory-Based Sharding

*   **How it works:** A lookup service (or "directory") maintains a map of which data belongs to which shard. This service stores the mapping between the shard key and the physical shard location.
*   **Pros:** Highly flexible. Allows for dynamic rebalancing of data between shards as the system grows or shrinks, as the mapping can be updated in the directory.
*   **Cons:** The directory service itself can become a single point of failure or a performance bottleneck if not highly available and scalable. Adds complexity to the architecture.

### 4. Geo-Based Sharding

*   **How it works:** Data is sharded based on geographic location. For example, users from Europe go to European shards, users from North America go to North American shards.
*   **Pros:** Reduces latency for users by keeping their data geographically closer. Helps with data sovereignty regulations.
*   **Cons:** Complex to manage if users travel or if data needs to be accessed globally.

## Challenges of Sharding

*   **Distributed Joins:** Joining data across multiple shards can be complex and inefficient.
*   **Distributed Transactions:** Ensuring ACID properties across multiple shards is significantly harder.
*   **Resharding:** The process of adding or removing shards and rebalancing data is often complex and can incur downtime.
*   **Complexity:** Adds significant operational and architectural complexity.
*   **Single Point of Failure (Shard Key):** If the chosen shard key is poorly selected, it can lead to hot spots or uneven distribution.

## Key Considerations

*   **Shard Key Selection:** The most important decision. A good shard key distributes data evenly and supports common query patterns.
*   **Data Rebalancing:** Planning for how to add/remove shards and rebalance data without downtime.
*   **Application Logic:** Application code needs to be aware of the sharding strategy to direct queries to the correct shard.
