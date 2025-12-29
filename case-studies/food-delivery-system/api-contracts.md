# API Contracts

## 1. Customer Service

### Customer Login

*   **Endpoint:** `POST /api/v1/customer/auth/login`
*   **Request Body:**
    ```json
    {
      "email": "customer@example.com",
      "password": "securepassword"
    }
    ```
*   **Response:** (Standard JWT/OAuth tokens)

## 2. Search & Discovery Service

### Search Restaurants/Menus

*   **Endpoint:** `GET /api/v1/search/restaurants?lat={latitude}&lon={longitude}&radius={km}&query={query_text}&cuisine={type}`
*   **Response:**
    ```json
    [
      {
        "restaurant_id": "uuid-rest-123",
        "name": "Pizza Palace",
        "cuisine": "Italian",
        "rating": 4.5,
        "delivery_time_estimate": "30-45 min",
        "min_order_value": 15.00,
        "distance": 2.5
      }
    ]
    ```

## 3. Order Service

### Place New Order

*   **Endpoint:** `POST /api/v1/customer/orders`
*   **Request Body:**
    ```json
    {
      "customer_id": "uuid-customer-abc",
      "restaurant_id": "uuid-rest-123",
      "delivery_address": { /* address details with lat/lon */ },
      "items": [
        {"menu_item_id": "uuid-pizza", "quantity": 1, "notes": "extra cheese"},
        {"menu_item_id": "uuid-coke", "quantity": 2}
      ],
      "payment_method_token": "stripe-token-xyz"
    }
    ```
*   **Response:**
    ```json
    {
      "order_id": "uuid-order-456",
      "status": "pending",
      "total_amount": 25.50,
      "estimated_delivery_time": "45 min"
    }
    ```

### Get Order Status

*   **Endpoint:** `GET /api/v1/customer/orders/{order_id}/status`
*   **Response:**
    ```json
    {
      "order_id": "uuid-order-456",
      "status": "driver_assigned", // or 'preparing', 'picked_up', etc.
      "driver_location": { "latitude": 34.00, "longitude": -118.00 }, // if driver assigned
      "estimated_delivery_time": "20 min left"
    }
    ```

## 4. Restaurant Service

### Confirm Order (by Restaurant)

*   **Endpoint:** `POST /api/v1/restaurant/orders/{order_id}/confirm`
*   **Request Body:**
    ```json
    {
      "restaurant_id": "uuid-rest-123",
      "preparation_time_minutes": 25 // Optional, to update ETA
    }
    ```
*   **Response:** `HTTP 200 OK`

### Update Order Preparation Status

*   **Endpoint:** `PUT /api/v1/restaurant/orders/{order_id}/status`
*   **Request Body:**
    ```json
    {
      "restaurant_id": "uuid-rest-123",
      "status": "ready_for_pickup"
    }
    ```
*   **Response:** `HTTP 200 OK`

## 5. Driver Service

### Go Online/Offline

*   **Endpoint:** `POST /api/v1/driver/status`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver-789",
      "status": "online" // or "offline"
    }
    ```
*   **Response:** `HTTP 200 OK`

### Update Driver Location (Real-time)

*   **Endpoint:** `PUT /api/v1/driver/location` (often a WebSocket stream)
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver-789",
      "latitude": 34.05,
      "longitude": -118.25
    }
    ```
*   **Response:** (Via WebSocket acknowledgment)

### Accept Delivery Offer

*   **Endpoint:** `POST /api/v1/driver/offers/{order_id}/accept`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver-789"
    }
    ```
*   **Response:** `HTTP 200 OK`

### Mark Order Picked Up

*   **Endpoint:** `POST /api/v1/driver/orders/{order_id}/picked-up`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver-789"
    }
    ```
*   **Response:** `HTTP 200 OK`

### Mark Order Delivered

*   **Endpoint:** `POST /api/v1/driver/orders/{order_id}/delivered`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver-789"
    }
    ```
*   **Response:** `HTTP 200 OK`
