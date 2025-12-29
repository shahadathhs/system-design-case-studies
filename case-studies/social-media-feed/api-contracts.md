# API Contracts

## Create Post

*   **Endpoint:** `POST /api/v1/posts`
*   **Request Body:**
    ```json
    {
      "content": "This is my new post!"
    }
    ```
*   **Response:**
    ```json
    {
      "post_id": "uuid-of-the-new-post"
    }
    ```

## Get Feed

*   **Endpoint:** `GET /api/v1/feed`
*   **Response:**
    ```json
    [
      {
        "post_id": "uuid-of-post-1",
        "user_id": "uuid-of-user-1",
        "content": "This is the first post.",
        "created_at": "timestamp"
      },
      {
        "post_id": "uuid-of-post-2",
        "user_id": "uuid-of-user-2",
        "content": "This is the second post.",
        "created_at": "timestamp"
      }
    ]
    ```
