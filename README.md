# System Design Case Studies

A comprehensive collection of detailed architectural case studies, fundamental concepts, and operational guides for high-scale distributed systems. This repository documents production-grade design patterns, trade-off analyses, and infrastructure decisions for modern backend engineering.

## Repository Structure

### 📂 [Fundamentals](./fundamentals/README.md)

Explore core backend concepts, from scalability and consistency to database strategies, caching, and networking. This section provides a structured learning path with detailed explanations of essential system design topics.

### 📂 [Case Studies](./case-studies)

Deep dives into realistic storage-heavy and compute-heavy systems. Each case study follows a strict, standardized structure, analyzing requirements, high-level and low-level designs, database schemas, API contracts, scaling strategies, failure handling, and trade-offs.

*   **[Analytics Event Pipeline](./case-studies/analytics-event-pipeline)**: Big data ingestion and processing (Buffering, Batching, Data Lakes).
*   **[Authentication System](./case-studies/authentication-system)**: Secure, scalable identity management (JWT, OAuth, Token Rotation).
*   **[E-commerce Platform](./case-studies/e-commerce-platform)**: Designing a large-scale online retail system (Product Catalog, Order Processing, Inventory, Payments).
*   **[File Upload Service](./case-studies/file-upload-service)**: Handling large binary data (Multipart, S3, CDN, Security).
*   **[Food Delivery System](./case-studies/food-delivery-system)**: A three-sided marketplace with real-time logistics (Matching, Location Tracking, Order Management).
*   **[HR System](./case-studies/hr-system)**: Human Resources Management System (Employee Data, Payroll, Time-off, Compliance).
*   **[Notification System](./case-studies/notification-system)**: High-throughput, multi-channel alerting (Fan-out, Priority Queues).
*   **[Real-Time Chat System](./case-studies/real-time-chat-system)**: Low-latency messaging at scale (WebSockets, Message Ordering).
*   **[Ride-Sharing App](./case-studies/ride-sharing-app)**: Real-time geospatial matching, dynamic pricing, and trip management.
*   **[Social Media Feed](./case-studies/social-media-feed)**: Designing a scalable news feed for social platforms (Fanout-on-Write, Fanout-on-Read).
*   **[URL Shortener](./case-studies/url-shortener)**: A foundational system design problem (Hash Generation, Redirection, Custom Aliases).

#### Case Study Documentation Format

Each system is documented with:

-   `requirements.md`: Functional & non-functional scoping.
-   `high-level-design.md`: Architecture overview and data flow.
-   `low-level-design.md`: Detailed component interactions and workflows.
-   `database-schema.md`: Database designs and data models.
-   `api-contracts.md`: API endpoints, request/response formats.
-   `scaling-strategy.md`: Approaches to handle growth and load.
-   `failure-handling.md`: Strategies for resilience and fault tolerance.
-   `trade-offs.md`: Critical analysis of design alternatives.

### 📂 [Diagrams](./diagrams)

Visual aids supporting the written designs, including flowcharts, sequence diagrams, and architecture maps.