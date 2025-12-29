# Trade-Off Analysis

## Base64 vs. Binary Uploads

- **Base64 (JSON)**: `POST /api/upload { "data": "data:image/png;base64,..." }`
  - _Pros_: Simple for small files (Avatars).
  - _Cons_: Increases payload size by 33%. Blocks Node.js event loop requiring decoding string -> buffer.
  - _Decision_: **Rejected**. We generally block payload > 10MB at the gateway.

## Proxy vs. Direct Upload

- **Proxy (Client -> API -> S3)**:
  - _Pros_: API can validate file _content_ before storage.
  - _Cons_: API pays for bandwidth. API is bottleneck.
- **Direct (Client -> S3)**:
  - _Pros_: Infinite scalability.
  - _Cons_: User can upload garbage.
  - _Decision_: **Direct Upload**. We solve the "garbage" problem with the Async Virus Scanner/Worker.

## Sequential ID vs. UUID

- **Decision**: **UUID**.
- **Reason**: If we use Sequential, a scraper can iterate `cdn.com/assets/1.jpg`, `2.jpg` to steal all data. UUIDs are unguessable.
