# API Contracts

## Base URL
`https://api.system.com/auth/v1`

## Endpoints

### 1. Register
**POST** `/register`
*   **Request**:
    ```json
    {
      "email": "jane@example.com",
      "password": "SecurePassword123!"
    }
    ```
*   **Response (201 Created)**: Returns session tokens immediately.

### 2. Login
**POST** `/login`
*   **Request**:
    ```json
    {
      "email": "jane@example.com",
      "password": "SecurePassword123!"
    }
    ```
*   **Response (200 OK)**:
    ```json
    {
      "accessToken": "ey...", // JWT, 15 min expiration
      "refreshToken": "7c...", // Opaque or JWT, 7 days
      "user": {
        "id": "uuid...",
        "email": "jane@example.com"
      }
    }
    ```

### 3. Refresh Token
**POST** `/refresh`
*   **Headers**: `Authorization: Bearer <RefreshToken>` (or in HTTP-only cookie)
*   **Response (200 OK)**:
    ```json
    {
      "accessToken": "new_ey...",
      "refreshToken": "new_7c..."
    }
    ```
*   **Error (401 Unauthorized)**: Token reused or expired. Client must goto Login.

### 4. Logout
**POST** `/logout`
*   **Request**: `{ "refreshToken": "..." }`
*   **Action**: Adds the token to Redis blacklist and marks session revoked in Postgres.

### 5. Get Public Keys (JWKS)
**GET** `/.well-known/jwks.json`
*   **Usage**: Used by other microservices to download the Public Key for verification.
*   **Response**: Standard JWKS format.
