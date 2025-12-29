# API Contracts

## Rider API

### 1. Request Ride

*   **Endpoint:** `POST /api/v1/rider/request-ride`
*   **Request Body:**
    ```json
    {
      "rider_id": "uuid-rider",
      "pickup_location": { "latitude": 34.0522, "longitude": -118.2437 },
      "dropoff_location": { "latitude": 34.0000, "longitude": -118.5000 },
      "vehicle_type": "standard"
    }
    ```
*   **Response:**
    ```json
    {
      "trip_id": "uuid-trip",
      "status": "requested",
      "estimated_fare": 25.50,
      "estimated_pickup_time": "2 minutes"
    }
    ```

### 2. Get Trip Status

*   **Endpoint:** `GET /api/v1/rider/trips/{trip_id}/status`
*   **Response:**
    ```json
    {
      "trip_id": "uuid-trip",
      "status": "accepted",
      "driver_info": {
        "driver_id": "uuid-driver",
        "name": "Jane Doe",
        "vehicle_type": "standard",
        "license_plate": "ABC-123",
        "current_location": { "latitude": 34.0525, "longitude": -118.2440 }
      }
    }
    ```

### 3. Rate Driver

*   **Endpoint:** `POST /api/v1/rider/trips/{trip_id}/rate-driver`
*   **Request Body:**
    ```json
    {
      "rider_id": "uuid-rider",
      "driver_id": "uuid-driver",
      "score": 5,
      "comment": "Great ride!"
    }
    ```
*   **Response:** `HTTP 200 OK`

## Driver API

### 1. Update Location

*   **Endpoint:** `PUT /api/v1/driver/location`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver",
      "latitude": 34.0500,
      "longitude": -118.2500
    }
    ```
*   **Response:** `HTTP 200 OK`

### 2. Get Available Ride Requests

*   **Endpoint:** `GET /api/v1/driver/ride-requests`
*   **Response:**
    ```json
    [
      {
        "trip_id": "uuid-trip-1",
        "rider_id": "uuid-rider-1",
        "pickup_location": { "latitude": 34.0522, "longitude": -118.2437 },
        "dropoff_location": { "latitude": 34.0000, "longitude": -118.5000 },
        "estimated_fare": 25.50,
        "estimated_distance": 10.2
      }
    ]
    ```

### 3. Accept Ride

*   **Endpoint:** `POST /api/v1/driver/trips/{trip_id}/accept`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver"
    }
    ```
*   **Response:**
    ```json
    {
      "trip_id": "uuid-trip",
      "status": "accepted"
    }
    ```

### 4. Complete Trip

*   **Endpoint:** `POST /api/v1/driver/trips/{trip_id}/complete`
*   **Request Body:**
    ```json
    {
      "driver_id": "uuid-driver"
    }
    ```
*   **Response:**
    ```json
    {
      "trip_id": "uuid-trip",
      "status": "completed",
      "final_fare": 26.75
    }
    ```
