# Real-Time Chat System

## System Overview
A high-throughput messaging platform supporting 1:1 DMs, Group Chats, and Presence features. It serves as the communications backbone for a social platform or enterprise tool.

## Real-World Usage
Comparable to **WhatsApp, Discord, or Slack**.
*   **Key Challenge**: Maintaining persistent connections and ensuring strictly ordered delivery.

## Expected Scale
*   **DAU**: 5 Million.
*   **Concurrent Connections**: 1 Million active WebSockets.
*   **Message Rate**:
    *   Traffic: 50k messages/sec.
    *   Storage: 4 Billion messages/day.
*   **Latency**: End-to-end delivery < 200ms.
