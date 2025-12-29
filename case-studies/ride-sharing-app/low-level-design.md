# Low-Level Design

## Ride Request and Matching

1.  **Rider** opens the app and requests a ride, specifying pickup and drop-off locations.
2.  **Rider App** sends a request to the **API Gateway**.
3.  **API Gateway** forwards the request to the **Matching Service**.
4.  **Matching Service**:
    *   Queries the **Location Service** to find available drivers within a certain radius of the rider.
    *   Filters drivers based on criteria (e.g., driver rating, vehicle type).
    *   Sends ride requests to the most suitable drivers.
5.  **Driver App** receives the ride request.
6.  **Driver** accepts or declines the request.
7.  **Matching Service** updates the ride status in the **Trip Management Service**.
8.  **Notification Service** sends a notification to the rider that a driver has accepted their request.

## Real-time Location Tracking

1.  **Driver App** continuously sends location updates to the **Location Service** via WebSockets or MQTT.
2.  **Location Service** stores and broadcasts these updates to relevant **Rider Apps**.
3.  **Rider App** displays the driver's real-time location on the map.

## Trip Completion and Payment

1.  **Driver** marks the trip as completed in their app.
2.  **Driver App** sends a request to the **Trip Management Service**.
3.  **Trip Management Service** notifies the **Payment Service**.
4.  **Payment Service**:
    *   Calculates the final fare based on distance, time, and dynamic pricing.
    *   Charges the rider's stored payment method.
    *   Updates payment status.
5.  **Notification Service** sends a receipt to the rider.
6.  **Rating Service** prompts both rider and driver to rate each other.
