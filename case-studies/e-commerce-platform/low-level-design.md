# Low-Level Design

## Product Browsing and Search

1.  **User** opens the e-commerce website/app.
2.  **Frontend** sends a request to the **API Gateway** to fetch product categories or search results.
3.  **API Gateway** routes the request to the **Product Service**.
4.  **Product Service** queries its product database (e.g., Elasticsearch for search, PostgreSQL for product details) to retrieve relevant information.
5.  **Product Service** returns data to the **API Gateway**, which forwards it to the **Frontend**.
6.  **Frontend** displays the products to the user.

## Adding to Cart and Checkout

1.  **User** adds a product to their shopping cart.
2.  **Frontend** sends a request to the **API Gateway** to add the item to the cart.
3.  **API Gateway** routes to the **Cart Service**.
4.  **Cart Service** stores the cart items, possibly in a fast key-value store like Redis.
5.  **User** proceeds to checkout.
6.  **Frontend** sends a checkout request to the **API Gateway**.
7.  **API Gateway** routes to the **Order Service**.
8.  **Order Service**:
    *   Creates a new order entry.
    *   Communicates with the **Inventory Service** to reserve stock.
    *   Communicates with the **Payment Service** to process the payment.
    *   If payment and inventory reservation are successful, the order status is updated to "placed".
9.  **Notification Service** sends an order confirmation to the user.

## Order Fulfillment (Simplified)

1.  **Seller Dashboard** (or internal system) retrieves new orders from the **Order Service**.
2.  **Seller** updates order status (e.g., "shipped") via the **Order Service**.
3.  **Order Service** updates the order status and notifies the **Notification Service** to inform the customer.
