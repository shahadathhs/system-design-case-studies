# Caching Strategies

## The Golden Rule of Caching

"There are only two hard things in Computer Science: cache invalidation and naming things." — Phil Karlton.

Caching is the easiest way to speed up a system and the easiest way to break its consistency.

## Layers of Caching

1.  **Client-Side**: Browser Caching (HTTP Headers `Cache-Control`, `ETag`). Data lives on the user's device. 0ms latency.
2.  **CDN (Content Delivery Network)**: Cloudflare/AWS CloudFront. Caches static assets (images, CSS, JS) at the edge, closer to the user.
3.  **Application Caching**: In-memory caching within the API (e.g., storing a calculated value in a variable). Risky in distributed systems due to drift.
4.  **Distributed Caching**: Redis / Memcached. Shared state across all API nodes.

## Caching Patterns

### 1. Cache-Aside (Lazy Loading)

- **Flow**: App checks Cache. If miss, App checks DB, writes to Cache, returns to User.
- **Pros**: Resilient handling of cache failures; data model in cache can differ from DB.
- **Cons**: First request is slow (Latency spike); potential for stale data if DB is updated directly outside the app.

### 2. Write-Through

- **Flow**: App writes to Cache, and Cache writes to DB synchronously.
- **Pros**: Data in cache is never stale; read latency is low.
- **Cons**: Write latency is higher (2 writes); polluting cache with data that might never be read.

### 3. Write-Back (Write-Behind)

- **Flow**: App writes to Cache (ack immediately). Cache asynchronously writes to DB.
- **Pros**: Extremely fast writes; absorbs spikes.
- **Cons**: **DATA LOSS RISK**. If the cache node dies before flushing to DB, data is gone. Use only for non-critical data (like likes/counters).

## Cache Eviction Policies

- **LRU (Least Recently Used)**: Discard items not used for the longest time. Standard for Redis.
- **LFU (Least Frequently Used)**: Discard items with the fewest hits.
- **TTL (Time To Live)**: Expires key after X seconds. Essential to prevent "infinite" stale data.

## The Thundering Herd Problem

**Scenario**: A popular cache key expires. 10,000 requests hit the API simultaneously. All miss the cache. All hit the database at once. DB crashes.
**Solutions**:

- **Jitter**: Add random variation to TTL (e.g., 60s + random(0-10s)) so keys don't all expire at once.
- **Mutex/Locking**: If cache miss, only ONE thread is allowed to query the DB; others wait for that thread to update the cache.

## Redis vs. Memcached

- **Memcached**: Pure, simple, multithreaded key-value store. Good for simple string caching.
- **Redis**: Single-threaded (mostly), supports complex data structures (Lists, Sets, Sorted Sets, HyperLogLog, Geospatial). Supports persistence (RDB/AOF) and Pub/Sub.
- **Choice**: default to **Redis** for versatility.
