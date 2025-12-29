# High-Level Design

## Architecture

We use a **Stateful Gateway** pattern for connections, backed by **Stateless Services** for logic.

### Components

1.  **Chat Gateway (WebSocket Server)**: Maintains persistent TCP/WS connections with clients. Lightweight Node.js/Go instances.
2.  **Service Discovery (Redis Key-Value)**: Maps `[User_ID] -> [Gateway_Server_IP]`.
3.  **Chat Service**: Handles persistence, group routing, and push notification triggers.
4.  **Message Store (Cassandra/ScyllaDB)**: Write-heavy storage for chat logs.
5.  **Pub/Sub (Redis)**: Broadcasts messages between Gateway nodes.

## Request Flow: Sending A Message

1.  **User A** sends message via WebSocket to **Gateway 1**.
2.  **Gateway 1** pushes event to **Chat Service**.
3.  **Chat Service**:
    - Persists message to **Cassandra**.
    - identifies Recipient (User B).
    - Queries **Redis** to find where User B is connected.
4.  If User B is Online (on **Gateway 2**):
    - Chat Service publishes to **Redis Channel**.
    - **Gateway 2** subscribes, picks up message, pushes to User B.
5.  If User B is Offline:
    - Trigger **Push Notification Service**.

## Diagram

[Link to Diagram](../../diagrams/real-time-chat-system.md)

