# Database Schema

Given the diverse data types and access patterns, a ride-sharing app would likely use a combination of database technologies.

## User Database (PostgreSQL)

For user profiles, authentication, and core relational data.

| Column Name | Data Type | Description                          |
|-------------|-----------|--------------------------------------|
| `user_id`   | `uuid`    | Unique identifier for user (PK)      |
| `username`  | `varchar` | User's username                      |
| `email`     | `varchar` | User's email (unique)                |
| `password_hash`| `varchar` | Hashed password                      |
| `role`      | `enum`    | 'rider', 'driver', 'admin'           |
| `created_at`| `timestamp`| Account creation timestamp           |

## Driver Details (PostgreSQL)

Extension of User table for driver-specific info.

| Column Name | Data Type | Description                          |
|-------------|-----------|--------------------------------------|
| `driver_id` | `uuid`    | User ID of the driver (PK, FK to users) |
| `vehicle_type`| `varchar` | e.g., 'sedan', 'SUV', 'premium'      |
| `license_plate`| `varchar` | Vehicle license plate number        |
| `status`    | `enum`    | 'available', 'on_trip', 'offline'    |
| `rating`    | `numeric` | Average driver rating (0.0-5.0)      |

## Trip Database (Cassandra / DynamoDB)

For storing trip details, which are often high-volume and append-only, suitable for NoSQL.

| Column Name | Data Type | Description                          |
|-------------|-----------|--------------------------------------|
| `trip_id`   | `uuid`    | Unique identifier for trip (PK)      |
| `rider_id`  | `uuid`    | FK to users table                    |
| `driver_id` | `uuid`    | FK to users table                    |
| `start_location`| `json`    | Lat/Lng of pickup                 |
| `end_location`| `json`    | Lat/Lng of drop-off               |
| `start_time`| `timestamp`| Trip start time                      |
| `end_time`  | `timestamp`| Trip end time                        |
| `status`    | `enum`    | 'requested', 'accepted', 'in_progress', 'completed', 'cancelled' |
| `fare`      | `numeric` | Calculated fare                      |
| `distance`  | `numeric` | Trip distance in miles/km            |

## Location Tracking (Redis with GeoSpatial Index / PostgreSQL with PostGIS)

For real-time location data of active drivers.

| Column Name | Data Type | Description                          |
|-------------|-----------|--------------------------------------|
| `entity_id` | `uuid`    | Driver/Rider ID (PK)                 |
| `location`  | `geopoint`| Current Lat/Lng                     |
| `timestamp` | `timestamp`| Last updated time                    |

## Payment Transactions (PostgreSQL)

For detailed, transactional payment records.

| Column Name | Data Type | Description                          |
|-------------|-----------|--------------------------------------|
| `transaction_id`| `uuid`    | Unique identifier (PK)               |
| `trip_id`   | `uuid`    | FK to trips table                    |
| `rider_id`  | `uuid`    | FK to users table                    |
| `amount`    | `numeric` | Amount charged                       |
| `currency`  | `varchar` | Currency code (e.g., 'USD')          |
| `status`    | `enum`    | 'pending', 'succeeded', 'failed'     |
| `processed_at`| `timestamp`| Transaction processing time          |

## Ratings (PostgreSQL)

For storing rider and driver ratings.

| Column Name | Data Type | Description                          |
|-------------|-----------|--------------------------------------|
| `rating_id` | `uuid`    | Unique identifier (PK)               |
| `trip_id`   | `uuid`    | FK to trips table                    |
| `rater_id`  | `uuid`    | User giving the rating (FK to users) |
| `ratee_id`  | `uuid`    | User receiving the rating (FK to users) |
| `score`     | `integer` | Rating score (1-5)                   |
| `comment`   | `text`    | Optional comment                     |
| `created_at`| `timestamp`| Rating creation time                 |
