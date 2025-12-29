# High-Level Design

A food delivery system operates as a three-sided marketplace (customers, restaurants, drivers) and requires a robust, real-time distributed system architecture.

## Core Services (Microservices)

1.  **Customer Service:** Manages customer profiles, authentication, and order history.
2.  **Restaurant Service:** Manages restaurant details, menus, operating hours, and order acceptance.
3.  **Driver Service:** Manages driver profiles, availability, and earnings.
4.  **Order Service:** Manages the lifecycle of an order (creation, status updates, cancellation).
5.  **Matching Service:** Efficiently assigns available drivers to pending orders, considering location, driver status, and other factors.
6.  **Location Service:** Handles real-time tracking of drivers and customer locations for delivery.
7.  **Payment Service:** Integrates with payment gateways, processes transactions, and handles payouts.
8.  **Notification Service:** Sends real-time updates to customers, restaurants, and drivers (e.g., order confirmed, driver en route).
9.  **Rating Service:** Manages ratings and reviews for restaurants and drivers.
10. **Search & Discovery Service:** Allows customers to search for restaurants and menu items, often using geo-based searches.

## Supporting Components

*   **API Gateway:** Central entry point for all client applications (Customer, Restaurant, Driver apps), handling routing, authentication, and rate limiting.
*   **Mobile/Web Clients:** Dedicated applications for customers, restaurants, and drivers.
*   **Message Broker (e.g., Kafka, RabbitMQ):** For asynchronous communication between services, ensuring reliability and decoupling.
*   **Databases:** A mix of relational (for strong consistency needs) and NoSQL (for high-volume, flexible data like location updates or logs) databases.
*   **Caching Layer:** To improve performance and reduce database load for frequently accessed data.

## Diagram

[Link to Diagram](../../diagrams/food-delivery-system.md)
