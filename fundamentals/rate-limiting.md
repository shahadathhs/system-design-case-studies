# Rate Limiting

Rate limiting is a technique used to control the rate at which an API or service is accessed. It restricts the number of requests a user or client can make within a given time window. This is crucial for maintaining the stability, availability, and fairness of a service.

## Why Use Rate Limiting?

*   **Prevent Resource Exhaustion:** Protects your service from being overwhelmed by too many requests, preventing denial-of-service (DoS) attacks and ensuring availability for legitimate users.
*   **Prevent Abuse:** Mitigates various forms of abuse, such as brute-force attacks, scraping, and spamming.
*   **Cost Control:** Reduces operational costs associated with excessive resource usage (e.g., database queries, compute cycles, bandwidth).
*   **Fairness:** Ensures that all users get a fair share of the service's resources by preventing a single user from monopolizing them.

## Rate Limiting Algorithms

Several algorithms can be used to implement rate limiting, each with its own characteristics:

### 1. Leaky Bucket Algorithm

The leaky bucket algorithm models a fixed-capacity bucket with a constant leak rate. Requests arrive and are placed into the bucket. If the bucket overflows, the request is dropped. Requests are processed at a constant rate from the bucket.

*   **Pros:** Smooths out bursts of requests, ensuring a constant output rate.
*   **Cons:** A burst of requests can fill the bucket, leading to dropped requests even if the average rate is low. Does not allow for processing bursts faster if capacity is available.

### 2. Token Bucket Algorithm

The token bucket algorithm is similar to the leaky bucket but offers more flexibility. Requests consume tokens from a bucket. Tokens are added to the bucket at a fixed rate, up to a maximum capacity. If a request arrives and there are no tokens in the bucket, it is dropped or queued.

*   **Pros:** Allows for bursts of requests to be processed (up to the bucket's capacity), as long as there are enough tokens.
*   **Cons:** Can be more complex to implement than leaky bucket.

### 3. Fixed Window Counter

This is the simplest algorithm. It divides time into fixed-size windows (e.g., 1 minute). Each window has a counter, and each request increments the counter. If the counter exceeds a predefined threshold within the window, further requests are blocked until the next window.

*   **Pros:** Simple to implement, low memory usage.
*   **Cons:** Can allow for a "burst" of requests at the edge of a window. For example, if the limit is 100 requests per minute, a client could send 100 requests at 0:59 and another 100 requests at 1:00, effectively sending 200 requests in a short period.

### 4. Sliding Log

This algorithm keeps a log of request timestamps for each user. When a new request arrives, it removes all timestamps older than the current time minus the window duration. If the number of remaining timestamps exceeds the limit, the request is rejected.

*   **Pros:** Very accurate, doesn't suffer from the "edge case" problem of fixed windows.
*   **Cons:** Can be memory-intensive for a large number of requests or a long window.

### 5. Sliding Window Counter

A hybrid approach that addresses the limitations of the fixed window counter. It uses a counter for the current window and also tracks the previous window's counter. When a request arrives, it calculates an estimated count for the current window based on the weighted average of the previous and current window counts.

*   **Pros:** More accurate than the fixed window counter while being less memory-intensive than the sliding log.
*   **Cons:** Still an approximation, not perfectly precise.

## Implementation Considerations

*   **Distributed Rate Limiting:** In a distributed system, rate limiting needs to be coordinated across multiple instances. This often involves using a centralized store like Redis to manage counters or token buckets.
*   **Granularity:** Rate limits can be applied at different levels: per IP address, per user, per API key, per endpoint, etc.
*   **Response to Exceeding Limits:** When a client exceeds the rate limit, the service typically returns an HTTP 429 Too Many Requests status code, often with a `Retry-After` header indicating when the client can try again.
