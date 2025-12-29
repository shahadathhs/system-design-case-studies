# API Contracts

## Endpoints

### 1. Track Event (Public)
**POST** `/v1/track`
*   **Request**:
    ```json
    {
      "events": [
        {
          "type": "button_click",
          "timestamp": "2023-10-27T10:00:00Z",
          "properties": {
             "id": "signup_btn",
             "page": "/pricing"
          }
        }
      ]
    }
    ```
*   **Headers**: `X-Write-Key: public_key_...`
*   **Response**: `202 Accepted` (Empty body).

## Internal Query API (For Dashboard)
**POST** `/v1/query`
*   **Request**:
    ```json
    {
      "metric": "count_unique",
      "groupBy": ["browser"],
      "filter": { "event_type": "pageview" },
      "range": "last_24h"
    }
    ```
*   **Response**:
    ```json
    {
      "rows": [
        { "browser": "Chrome", "value": 50400 },
        { "browser": "Firefox", "value": 10200 }
      ]
    }
    ```
