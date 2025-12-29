# Consistent Hashing

Consistent hashing is a special kind of hashing technique that, when a hash table is re-sized (e.g., when a node is added or removed from a distributed system), minimizes the number of keys that need to be remapped. This makes it particularly useful in distributed systems where nodes are frequently added or removed.

## The Problem

In a typical hashing scheme, if you have `N` servers and want to distribute `K` objects among them, you might use a hash function like `server_index = hash(object_id) % N`.

The problem with this approach is that if you add or remove a server (changing `N`), almost all `object_id`s will hash to a new `server_index`. This means you'd have to move almost all your data, which is incredibly inefficient and costly in a large-scale distributed system.

## How Consistent Hashing Works

Consistent hashing solves this problem by using a hash ring (or circle).

1.  **Hash Space:** Imagine a circle representing the entire range of possible hash values (e.g., from 0 to 2^32 - 1 for a 32-bit hash function).
2.  **Node Placement:** Each server (node) in the system is hashed and placed at a random position on this ring.
3.  **Key Placement:** Each data item (key) is also hashed and placed at a position on the same ring.
4.  **Mapping Keys to Nodes:** To find which server an object belongs to, you traverse the ring clockwise from the object's hash value until you encounter the first server. That server is responsible for storing that object.

## Adding/Removing Nodes

The beauty of consistent hashing comes when nodes are added or removed:

*   **Adding a Node:** When a new server is added to the ring, it only affects the keys that were previously mapped to the *next* server clockwise on the ring. The new server takes responsibility for a portion of the keys that were previously handled by that one server. Most other keys remain mapped to their original servers.
*   **Removing a Node:** When a server is removed, the keys it was responsible for are simply taken over by the next server clockwise on the ring. Again, only a small fraction of the keys are affected.

## Advantages

*   **Minimal Data Movement:** Significantly reduces data remapping when nodes are added or removed.
*   **Scalability:** Allows for easy horizontal scaling by adding or removing nodes without major service disruption.
*   **High Availability:** Can distribute load evenly and handle node failures gracefully.

## Disadvantages & Improvements

*   **Non-Uniform Distribution (Initial):** Simply hashing nodes and keys to random points on the ring might lead to an uneven distribution of keys among nodes. Some nodes might get a disproportionately large share of keys, while others get very few.
*   **Virtual Nodes (Vnodes):** To address uneven distribution, instead of placing a single physical node on the ring, multiple "virtual nodes" (vnodes) representing that physical node are placed. If each physical node has, say, 100 vnodes on the ring, the distribution becomes much more uniform, and the impact of a single node's failure or addition is spread across many points on the ring, leading to better load balancing.

## Use Cases

Consistent hashing is widely used in distributed systems, including:

*   **Distributed Caching:** Memcached, Redis Cluster.
*   **Distributed Databases:** Apache Cassandra, DynamoDB.
*   **Load Balancers:** Distributing requests among servers.
