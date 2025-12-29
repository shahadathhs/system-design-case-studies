# Database Schema

A food delivery system leverages a mix of relational databases (for transactional data requiring strong consistency) and NoSQL databases (for high-volume, flexible, or real-time data).

## 1. User Service (PostgreSQL)

Manages customer, restaurant, and driver profiles, authentication, and roles.

**Users Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `user_id`     | `uuid`    | Unique User ID (PK)                      |
| `email`       | `varchar` | User's email (unique, for login)         |
| `password_hash`| `varchar` | Hashed password                          |
| `role`        | `enum`    | 'customer', 'restaurant_admin', 'driver', 'admin' |
| `phone_number`| `varchar` | Contact phone number                     |
| `address_id`  | `uuid`    | FK to Addresses table (default address)  |
| `created_at`  | `timestamp`| Account creation timestamp               |

**Addresses Table** (can be shared or separate per user role)

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `address_id`  | `uuid`    | Unique Address ID (PK)                   |
| `user_id`     | `uuid`    | FK to Users table                        |
| `street`      | `varchar` | Street address                           |
| `city`        | `varchar` | City                                     |
| `state`       | `varchar` | State/Province                           |
| `zip_code`    | `varchar` | Postal code                              |
| `latitude`    | `numeric` | Latitude for geo-location                |
| `longitude`   | `numeric` | Longitude for geo-location               |

## 2. Restaurant Service (PostgreSQL)

Manages restaurant details, menus, and operating hours.

**Restaurants Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `restaurant_id`| `uuid`   | Unique Restaurant ID (PK)                |
| `name`        | `varchar` | Restaurant name                          |
| `description` | `text`    | Description of the restaurant            |
| `address_id`  | `uuid`    | FK to Addresses table (restaurant location) |
| `cuisine_type`| `varchar` | e.g., 'Italian', 'Mexican', 'Fast Food'  |
| `rating`      | `numeric` | Average customer rating                  |
| `admin_user_id`| `uuid`   | FK to Users table (restaurant admin)     |
| `status`      | `enum`    | 'open', 'closed', 'paused'               |

**Menu Items Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `item_id`     | `uuid`    | Unique Menu Item ID (PK)                 |
| `restaurant_id`| `uuid`   | FK to Restaurants table                  |
| `name`        | `varchar` | Item name                                |
| `description` | `text`    | Item description                         |
| `price`       | `numeric` | Price of the item                        |
| `category`    | `varchar` | e.g., 'Appetizers', 'Main Courses'       |
| `is_available`| `boolean` | Whether item is currently available      |

## 3. Driver Service (PostgreSQL)

Manages driver profiles and availability.

**Drivers Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `driver_id`   | `uuid`    | FK to Users table (PK)                   |
| `vehicle_type`| `varchar` | e.g., 'car', 'motorcycle', 'bicycle'     |
| `license_number`| `varchar`| Driver's license number                  |
| `status`      | `enum`    | 'online', 'offline', 'on_delivery'       |
| `current_order_id`| `uuid` | FK to Orders table (if on delivery)      |
| `rating`      | `numeric` | Average customer rating (0.0-5.0)        |

## 4. Order Service (PostgreSQL)

Manages the order lifecycle with strong transactional guarantees.

**Orders Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `order_id`    | `uuid`    | Unique Order ID (PK)                     |
| `customer_id` | `uuid`    | FK to Users table                        |
| `restaurant_id`| `uuid`   | FK to Restaurants table                  |
| `driver_id`   | `uuid`    | FK to Drivers table                      |
| `status`      | `enum`    | 'pending', 'accepted_by_restaurant', 'preparing', 'ready_for_pickup', 'driver_assigned', 'picked_up', 'delivered', 'cancelled' |
| `total_amount`| `numeric` | Total amount of the order                |
| `delivery_address_id`| `uuid`| FK to Addresses table (customer)       |
| `payment_id`  | `uuid`    | FK to Payments table                     |
| `created_at`  | `timestamp`| Order creation timestamp               |
| `updated_at`  | `timestamp`| Last status update timestamp           |

**Order Items Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `order_item_id`| `uuid`   | Unique Order Item ID (PK)                |
| `order_id`    | `uuid`    | FK to Orders table                       |
| `menu_item_id`| `uuid`    | FK to Menu Items table                   |
| `quantity`    | `integer` | Quantity ordered                         |
| `unit_price`  | `numeric` | Price at time of order                   |
| `notes`       | `text`    | e.g., "no onions"                        |

## 5. Location Service (Redis with GeoSpatial Index / PostGIS)

For real-time tracking of drivers and customers.

**Location (Redis Geospatial Set or PostGIS Table)**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `entity_id`   | `uuid`    | Driver/Customer ID (PK)                  |
| `latitude`    | `numeric` | Current latitude                         |
| `longitude`   | `numeric` | Current longitude                        |
| `timestamp`   | `timestamp`| Last update time                         |

## 6. Payment Service (PostgreSQL)

For secure and ACID-compliant payment transaction records.

**Payments Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `payment_id`  | `uuid`    | Unique Payment ID (PK)                   |
| `order_id`    | `uuid`    | FK to Orders table                       |
| `customer_id` | `uuid`    | FK to Users table                        |
| `amount`      | `numeric` | Amount of the payment                    |
| `currency`    | `varchar` | Currency code (e.g., 'USD')              |
| `status`      | `enum`    | 'pending', 'succeeded', 'failed', 'refunded' |
| `transaction_ref`| `varchar`| Reference from payment gateway           |
| `created_at`  | `timestamp`| Payment creation timestamp               |

## 7. Rating Service (PostgreSQL)

For storing ratings and reviews.

**Ratings Table**

| Column Name   | Data Type | Description                              |
|---------------|-----------|------------------------------------------|
| `rating_id`   | `uuid`    | Unique Rating ID (PK)                    |
| `order_id`    | `uuid`    | FK to Orders table                       |
| `rater_id`    | `uuid`    | User giving the rating                   |
| `ratee_id`    | `uuid`    | User/Restaurant receiving the rating     |
| `score`       | `integer` | Rating score (1-5)                       |
| `comment`     | `text`    | Optional comment                         |
| `type`        | `enum`    | 'driver', 'restaurant'                   |
| `created_at`  | `timestamp`| Rating creation timestamp                |
