# Database Schema

An e-commerce platform typically uses a polyglot persistence approach, employing different types of databases optimized for specific microservices.

## 1. User Service (PostgreSQL)

For user authentication, profiles, and authorization.

| Column Name | Data Type | Description                              |
|-------------|-----------|------------------------------------------|
| `user_id`   | `uuid`    | Unique User ID (PK)                      |
| `email`     | `varchar` | User's email (unique, for login)         |
| `password_hash`| `varchar` | Hashed password                          |
| `first_name`| `varchar` | User's first name                        |
| `last_name` | `varchar` | User's last name                         |
| `created_at`| `timestamp`| Account creation timestamp               |
| `address_id`| `uuid`    | FK to Addresses table (for default address) |

**Addresses Table**

| Column Name | Data Type | Description                              |
|-------------|-----------|------------------------------------------|
| `address_id`| `uuid`    | Unique Address ID (PK)                   |
| `user_id`   | `uuid`    | FK to Users table                        |
| `street`    | `varchar` | Street address                           |
| `city`      | `varchar` | City                                     |
| `state`     | `varchar` | State/Province                           |
| `zip_code`  | `varchar` | Postal code                              |
| `country`   | `varchar` | Country                                  |

## 2. Product Service (PostgreSQL + Elasticsearch)

*   **PostgreSQL:** For master product data (SKUs, pricing, descriptions).
*   **Elasticsearch:** For fast, full-text search and filtering.

**Products Table (PostgreSQL)**

| Column Name    | Data Type  | Description                              |
|----------------|------------|------------------------------------------|
| `product_id`   | `uuid`     | Unique Product ID (PK)                   |
| `name`         | `varchar`  | Product name                             |
| `description`  | `text`     | Full product description                 |
| `price`        | `numeric`  | Current price                            |
| `category_id`  | `uuid`     | FK to Categories table                   |
| `seller_id`    | `uuid`     | FK to Users table (if marketplace)       |
| `image_urls`   | `jsonb`    | Array of image URLs                      |
| `attributes`   | `jsonb`    | JSON for size, color, etc.               |
| `created_at`   | `timestamp`| Product creation timestamp               |
| `updated_at`   | `timestamp`| Last update timestamp                    |

**Categories Table (PostgreSQL)**

| Column Name    | Data Type  | Description                              |
|----------------|------------|------------------------------------------|
| `category_id`  | `uuid`     | Unique Category ID (PK)                  |
| `name`         | `varchar`  | Category name                            |
| `parent_id`    | `uuid`     | Self-referencing FK for subcategories    |

## 3. Inventory Service (PostgreSQL / DynamoDB)

Requires strong consistency for stock levels.

**Stock Table**

| Column Name    | Data Type  | Description                              |
|----------------|------------||
| `product_id`   | `uuid`     | FK to Products table (PK)                |
| `warehouse_id` | `uuid`     | PK, FK to Warehouses table               |
| `quantity`     | `integer`  | Available stock quantity (must be > 0)   |
| `reserved_quantity`| `integer`| Quantity reserved in carts/orders       |
| `updated_at`   | `timestamp`| Last stock update timestamp              |

## 4. Cart Service (Redis / Cassandra)

For transient shopping cart data, low latency required.

**Carts (Redis Hash or JSON document in Cassandra)**

| Key/Column   | Data Type  | Description                               |
|--------------|------------|-------------------------------------------|
| `user_id`    | `uuid`     | User ID (PK)                              |
| `product_id` | `uuid`     | Product ID (composite PK with user_id)    |
| `quantity`   | `integer`  | Quantity of product in cart               |
| `added_at`   | `timestamp`| When product was added                    |

## 5. Order Service (PostgreSQL)

For transactional order data.

**Orders Table**

| Column Name    | Data Type  | Description                              |
|----------------|------------|------------------------------------------|
| `order_id`     | `uuid`     | Unique Order ID (PK)                     |
| `user_id`      | `uuid`     | FK to Users table                        |
| `total_amount` | `numeric`  | Total cost of the order                  |
| `status`       | `enum`     | 'pending', 'processed', 'shipped', 'delivered', 'cancelled' |
| `shipping_address_id`| `uuid` | FK to Addresses table (shipping)       |
| `billing_address_id` | `uuid` | FK to Addresses table (billing)        |
| `payment_id`   | `uuid`     | FK to Payments table                     |
| `created_at`   | `timestamp`| Order creation timestamp                 |
| `updated_at`   | `timestamp`| Last update timestamp                    |

**Order Items Table**

| Column Name    | Data Type  | Description                              |
|----------------|------------|------------------------------------------|
| `order_item_id`| `uuid`     | Unique Order Item ID (PK)                |
| `order_id`     | `uuid`     | FK to Orders table                       |
| `product_id`   | `uuid`     | FK to Products table                     |
| `quantity`     | `integer`  | Quantity of this product in the order    |
| `unit_price`   | `numeric`  | Price at the time of order               |

## 6. Payment Service (PostgreSQL)

For secure and ACID-compliant payment transaction records.

**Payments Table**

| Column Name    | Data Type  | Description                              |
|----------------|------------|------------------------------------------|
| `payment_id`   | `uuid`     | Unique Payment ID (PK)                   |
| `order_id`     | `uuid`     | FK to Orders table                       |
| `user_id`      | `uuid`     | FK to Users table                        |
| `amount`       | `numeric`  | Amount of the payment                    |
| `currency`     | `varchar`  | Currency code                            |
| `status`       | `enum`     | 'pending', 'succeeded', 'failed', 'refunded' |
| `transaction_ref`| `varchar`| Reference from payment gateway           |
| `created_at`   | `timestamp`| Payment creation timestamp               |
| `updated_at`   | `timestamp`| Last update timestamp                    |

## 7. Review Service (MongoDB / Cassandra)

For product reviews, can be eventually consistent and document-oriented.

**Reviews Collection/Table**

| Key/Column   | Data Type  | Description                              |
|--------------|------------|------------------------------------------|
| `review_id`  | `uuid`     | Unique Review ID (PK)                    |
| `product_id` | `uuid`     | FK to Products table                     |
| `user_id`    | `uuid`     | FK to Users table                        |
| `rating`     | `integer`  | Rating (1-5 stars)                       |
| `comment`    | `text`     | User's review text                       |
| `created_at` | `timestamp`| Review creation timestamp                |
