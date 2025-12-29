# API Contracts

## Base URL

`https://api.system.com/notifications/v1`

## Endpoints

### 1. Send Notification (Internal Only)

**POST** `/send`

- **Headers**: `X-Internal-Secret: ...`
- **Request**:
  ```json
  {
    "userId": "uuid-123",
    "template": "order_confirmed",
    "data": {
      "orderId": "AB-99",
      "amount": "$50.00"
    },
    "priority": "HIGH"
  }
  ```
- **Response (202 Accepted)**:
  ```json
  {
    "notificationId": "uuid-gen-555",
    "status": "QUEUED"
  }
  ```

### 2. Broadcast (Admin Only)

**POST** `/broadcast`

- **Request**:
  ```json
  {
    "segment": "all_users",
    "exclude_active_last_24h": true,
    "template": "black_friday_promo"
  }
  ```

### 3. Webhook (External)

**POST** `/webhooks/sendgrid`

- **Payload**: Standard SendGrid Event format (Delivered, Bounced, Spam Report).
- **Logic**: Updates `notification_logs`. If `Bounced`, updates User profile to `email_valid = false`.
