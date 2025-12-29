# Database Schema

We will use a NoSQL database like Cassandra or DynamoDB for this service, as the primary requirement is to have a scalable key-value store.

## URL Table

| Column Name   | Data Type | Description                                 |
|---------------|-----------|---------------------------------------------|
| `short_url`   | `string`  | The shortened URL (primary key).            |
| `long_url`    | `string`  | The original long URL.                      |
| `created_at`  | `timestamp`| The timestamp when the URL was shortened.    |
| `expires_at`  | `timestamp`| The timestamp when the URL should expire. |

We will use the `short_url` as the partition key to distribute the data across multiple nodes.
