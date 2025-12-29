# API Contracts

## 1. Product Service

### Get Product Details

*   **Endpoint:** `GET /api/v1/products/{product_id}`
*   **Response:**
    ```json
    {
      "product_id": "uuid-product-123",
      "name": "Fancy Gadget Pro",
      "description": "A high-tech gadget for all your needs.",
      "price": 99.99,
      "category": "Electronics",
      "image_urls": ["url1", "url2"],
      "attributes": {"color": "black", "storage": "128GB"},
      "available_stock": 50,
      "avg_rating": 4.5
    }
    ```

### Search Products

*   **Endpoint:** `GET /api/v1/products/search?q={query}&category={category_id}&min_price={min}&max_price={max}`
*   **Response:**
    ```json
    [
      { /* Product details */ },
      { /* Product details */ }
    ]
    ```

## 2. Cart Service

### Add Item to Cart

*   **Endpoint:** `POST /api/v1/cart`
*   **Request Body:**
    ```json
    {
      "user_id": "uuid-user-abc",
      "product_id": "uuid-product-123",
      "quantity": 2
    }
    ```
*   **Response:** `HTTP 200 OK`

### Get Cart Contents

*   **Endpoint:** `GET /api/v1/cart/{user_id}`
*   **Response:**
    ```json
    [
      {
        "product_id": "uuid-product-123",
        "name": "Fancy Gadget Pro",
        "price": 99.99,
        "quantity": 2
      }
    ]
    ```

## 3. Order Service

### Create Order (Checkout)

*   **Endpoint:** `POST /api/v1/orders`
*   **Request Body:**
    ```json
    {
      "user_id": "uuid-user-abc",
      "items": [
        {"product_id": "uuid-product-123", "quantity": 2},
        {"product_id": "uuid-product-456", "quantity": 1}
      ],
      "shipping_address_id": "uuid-address-xyz",
      "billing_address_id": "uuid-address-xyz",
      "payment_method_token": "stripe-token-123"
    }
    ```
*   **Response:**
    ```json
    {
      "order_id": "uuid-order-789",
      "status": "pending",
      "total_amount": 299.97
    }
    ```

### Get Order Details

*   **Endpoint:** `GET /api/v1/orders/{order_id}`
*   **Response:**
    ```json
    {
      "order_id": "uuid-order-789",
      "user_id": "uuid-user-abc",
      "status": "processed",
      "total_amount": 299.97,
      "items": [
        {"product_id": "uuid-product-123", "name": "Fancy Gadget Pro", "quantity": 2, "unit_price": 99.99},
        {"product_id": "uuid-product-456", "name": "Wireless Charger", "quantity": 1, "unit_price": 99.99}
      ],
      "shipping_address": { /* address details */ },
      "billing_address": { /* address details */ },
      "created_at": "timestamp"
    }
    ```

## 4. Payment Service

### Process Payment

*   **Endpoint:** `POST /api/v1/payments`
*   **Request Body:**
    ```json
    {
      "order_id": "uuid-order-789",
      "user_id": "uuid-user-abc",
      "amount": 299.97,
      "currency": "USD",
      "payment_method_token": "stripe-token-123"
    }
    ```
*   **Response:**
    ```json
    {
      "payment_id": "uuid-payment-abc",
      "status": "succeeded",
      "order_id": "uuid-order-789"
    }
    ```
