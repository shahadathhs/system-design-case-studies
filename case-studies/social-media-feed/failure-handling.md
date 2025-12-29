# Failure Handling

## Fanout Service Failure

If the Fanout Service fails, new posts will not be pushed to user feeds. We can use a message queue to make this process more resilient. When a new post is created, the API server can add a message to a queue. The Fanout Service can then process messages from the queue. If the Fanout Service fails, the messages will remain in the queue and can be processed when the service comes back online.

## Feed Store Failure

If the Feed Store (Redis) fails, users will not be able to retrieve their feeds. We can use a replica of the Feed Store to provide high availability. If the primary Feed Store fails, we can failover to the replica.
