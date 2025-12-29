# API Contracts

## Protocols
1.  **REST**: For Auth, History fetching, File uploads.
2.  **WebSocket**: For real-time event streaming.

## REST Endpoints

### 1. Get History
**GET** `/channels/{channelId}/messages?beforeId=12345&limit=50`
*   **Response**: List of messages.

### 2. Join Channel
**POST** `/channels/{channelId}/join`

## WebSocket Events

### Client -> Server
*   **Event**: `sendMessage`
    ```json
    { "channelId": "uuid...", "content": "Hello world", "tempId": "local-unique" }
    ```
*   **Event**: `typing`
    ```json
    { "channelId": "uuid..." }
    ```

### Server -> Client
*   **Event**: `newMessage`
    ```json
    {
      "id": "snowflake-111",
      "senderId": "user-a",
      "content": "Hello world",
      "timestamp": "2023-01-01T12:00:00Z"
    }
    ```
*   **Event**: `messageAck`
    *   Confirms to sender that server received the message.
