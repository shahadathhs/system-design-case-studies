# Requirements

## Functional Requirements
1.  **1:1 Chat**: Private messaging.
2.  **Group Chat**: Up to 100 members.
3.  **Presence**: Online/Offline/Typing indicators.
4.  **Message Status**: Sent, Delivered, Read receipts.
5.  **Multi-Device**: Sync history across Mobile and Desktop.
6.  **Push Notifications**: Notify if the user is offline.

## Non-Functional Requirements
1.  **Low Latency**: Real-time feel (< 100ms internal processing).
2.  **Ordering**: Messages must appear in the strict order they were sent.
3.  **Consistency**: No lost messages. (At-least-once delivery).
4.  **Scalability**: Support millions of open connections.
5.  **Availability**: Partition tolerance. System should function even if some nodes fail.
