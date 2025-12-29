# Low-Level Design

## 1. Order Placement Workflow

1.  **Customer** browses restaurants/menus via the **Customer App**.
2.  **Customer App** queries **Search & Discovery Service** (which might cache data from **Restaurant Service** and **Menu Service**).
3.  **Customer** selects items and places an order.
4.  **Customer App** sends `POST /api/v1/orders` request to **API Gateway**.
5.  **API Gateway** routes to **Order Service**.
6.  **Order Service**:
    *   Validates order (e.g., items available, restaurant open).
    *   Creates a `PENDING` order entry in its database.
    *   Publishes an `OrderCreatedEvent` to the **Message Broker**.
    *   Responds to **Customer App** with order ID and `PENDING` status.
7.  **Payment Service** subscribes to `OrderCreatedEvent`, processes payment via **External Payment Gateway**. Publishes `PaymentProcessedEvent` (success/failure).
8.  **Order Service** subscribes to `PaymentProcessedEvent`, updates order status (e.g., `PAID` or `PAYMENT_FAILED`).
9.  **Notification Service** sends confirmation to customer.
10. **Restaurant App** receives `OrderCreatedEvent` (via push notification or polling **Order Service**).
11. **Restaurant** confirms/rejects order. **Restaurant App** sends update to **Order Service**.
12. **Order Service** updates status (e.g., `ACCEPTED`, `REJECTED`). Publishes `OrderStatusUpdatedEvent`.

## 2. Driver Matching and Tracking

1.  **Order Service** publishes `OrderReadyForMatchingEvent` (after payment and restaurant acceptance).
2.  **Matching Service** subscribes to this event.
3.  **Matching Service**:
    *   Queries **Location Service** for available drivers near the restaurant.
    *   Considers driver ratings, current load, etc.
    *   Sends delivery request to suitable drivers (via **Driver Service** and **Notification Service**).
4.  **Driver App** receives request. **Driver** accepts/rejects.
5.  **Driver App** informs **Driver Service**, which updates **Order Service**.
6.  **Order Service** updates status to `DRIVER_ACCEPTED`. Publishes `OrderStatusUpdatedEvent`.
7.  **Location Service** continuously receives real-time GPS updates from **Driver App**. Stores and provides driver location to **Customer App** and **Restaurant App**.

## 3. Order Delivery and Completion

1.  **Driver** arrives at restaurant, marks `PICKED_UP`.
2.  **Order Service** updates status, publishes `OrderStatusUpdatedEvent`.
3.  **Customer App** shows driver en route with real-time location.
4.  **Driver** arrives at customer, marks `DELIVERED`.
5.  **Order Service** updates status to `DELIVERED`, publishes `OrderStatusUpdatedEvent`.
6.  **Payment Service** finalizes payment (e.g., adds tip).
7.  **Rating Service** prompts customer to rate driver/restaurant.

## 4. Search and Discovery

1.  **Customer App** searches for "pizza" near "London".
2.  **API Gateway** routes to **Search & Discovery Service**.
3.  **Search & Discovery Service** queries **Elasticsearch** (pre-indexed from **Restaurant Service** and **Menu Service**).
4.  Filters results by location (using geospatial queries), cuisine, rating.
5.  Returns relevant restaurants/menu items.
