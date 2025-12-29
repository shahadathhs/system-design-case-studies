# API Design Principles

## REST (Representational State Transfer)

The de-facto standard for web APIs.

- **Resource-Oriented**: `/users`, `/users/123/orders`.
- **Stateless**: Each request contains all info needed (Auth tokens).
- **Standard Methods**:
  - `GET`: Read (Safe, Cacheable).
  - `POST`: Create (Not Idempotent).
  - `PUT`: Replace/Update (Idempotent).
  - `PATCH`: Partial Update.
  - `DELETE`: Remove.

## Crucial Design Patterns

### 1. Idempotency Keys

**Problem**: Client sends `POST /payment`. Network times out. Client retries. User gets charged twice.
**Solution**: Client generates a unique `Idempotency-Key: UUID`. Server stores this key. If it sees the same key again, it returns the _cached previous response_ instead of executing the charge again.

### 2. Pagination

Never return `SELECT *`!

- **Offset Pagination**: `LIMIT 10 OFFSET 100`. Simple, but slow on large tables (`OFFSET` scan) and inconsistent if concurrent writes occur.
- **Cursor Pagination**: `LIMIT 10 WHERE id > last_seen_id`. Constant time complexity O(1). Infinite scroll friendly. Preferred for feeds.

### 3. Rate Limiting

Protect your resources.

- **Token Bucket Algorithm**: Allow bursts but cap sustained rate.
- **Leaky Bucket**: Smooth out traffic processing.
- Return `429 Too Many Requests` with `Retry-After` header.

### 4. Versioning

APIs evolve. Do not break existing clients (Mobile apps can't update instantly).

- **URI**: `/api/v1/users` (Most visible, easiest to route).
- **Header**: `Accept: application/vnd.company.v1+json` (Cleaner URLs, harder to test in browser).

### 5. HATEOAS (Hypermedia As The Engine of Application State)

- **Theory**: API returns links to next actions alongside data.
- **Practice**: Ideally helpful, but often considered "Academic Overkill" for internal APIs. rarely fully implemented in industry. Simple documentation (OpenAPI/Swagger) is preferred.

## Error Handling

Stop sending `200 OK` with `{ "error": "failed" }` in the body!
Use HTTP Status Codes correctly:

- `400 Bad Request`: Client error (validation).
- `401 Unauthorized`: Who are you? (missing token).
- `403 Forbidden`: I know you, but you can't do this (scopes).
- `404 Not Found`: Resource doesn't exist.
- `500 Internal Server Error`: Our fault. Log this.
