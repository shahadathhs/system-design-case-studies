# Requirements

## Functional Requirements

*   Users can submit a long URL and get a shortened URL.
*   When a user accesses a shortened URL, they should be redirected to the original long URL.
*   Users can optionally specify a custom alias for their shortened URL.
*   Shortened URLs should expire after a configurable amount of time.

## Non-Functional Requirements

*   **High Availability:** The service should be highly available.
*   **Low Latency:** The redirection should be very fast.
*   **Scalability:** The service should be able to handle a large number of requests.
*   **Durability:** Once a URL is shortened, it should not be lost.
