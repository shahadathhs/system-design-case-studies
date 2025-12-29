# API Contracts

## Shorten URL

*   **Endpoint:** `POST /api/v1/shorten`
*   **Request Body:**
    ```json
    {
      "long_url": "https://www.example.com/very/long/url/to/shorten",
      "custom_alias": "my-custom-alias" // Optional
    }
    ```
*   **Response:**
    ```json
    {
      "short_url": "http://short.url/my-custom-alias"
    }
    ```

## Redirect URL

*   **Endpoint:** `GET /{short_url}`
*   **Response:**
    *   **Status Code:** `301 Moved Permanently`
    *   **Location Header:** `https://www.example.com/very/long/url/to/shorten`
