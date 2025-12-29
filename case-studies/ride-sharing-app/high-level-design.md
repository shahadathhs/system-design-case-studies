# High-Level Design

The system will consist of the following components:

1.  **Mobile Clients:** Rider and Driver apps.
2.  **API Gateway:** Entry point for all client requests.
3.  **Matching Service:** Responsible for matching riders with available drivers.
4.  **Location Service:** Handles real-time tracking of rider and driver locations.
5.  **Trip Management Service:** Manages the lifecycle of a trip (request, accepted, in-progress, completed).
6.  **Payment Service:** Handles fare calculation and processing payments.
7.  **Notification Service:** Sends notifications to riders and drivers (e.g., "driver is here").
8.  **Messaging Service:** For in-app communication between riders and drivers.
9.  **Rating Service:** Manages ratings and reviews.
10. **Databases:** Various databases for storing user data, trip data, payment info, etc.

## Diagram

[Link to Diagram](../../diagrams/ride-sharing-app.md)
