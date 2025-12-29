# Database Schema

## Tech Stack
*   **PostgreSQL**: Reliability for logs and templates.
*   **MongoDB**: (Optional) For archiving massive historical logs if PG partitioning gets unwieldy. We will stick to partitioned PG for simplicity.

## Tables

### 1. `notification_templates`
Manages the content.
```sql
CREATE TABLE notification_templates (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE, -- 'welcome_email'
    channel VARCHAR(20), -- 'EMAIL', 'SMS'
    subject_template TEXT,
    body_template TEXT, -- Handlebars/Mustache syntax
    version INT DEFAULT 1
);
```

### 2. `notification_logs` (Partitioned)
The source of truth for "Did user X get the message?".
**Partitioning**: By Month.
```sql
CREATE TABLE notification_logs (
    id UUID PRIMARY KEY,
    user_id UUID NOT NULL, -- No FK for performance
    campaign_id UUID,
    type VARCHAR(50), -- 'OTP', 'MARKETING'
    channel VARCHAR(20),
    status VARCHAR(20), -- 'QUEUED', 'SENT', 'DELIVERED', 'FAILED'
    provider_response JSONB, -- Store external ID
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ
) PARTITION BY RANGE (created_at);
```

### 3. `user_preferences`
```sql
CREATE TABLE user_preferences (
    user_id UUID,
    channel VARCHAR(20),
    category VARCHAR(50), -- 'marketing', 'security'
    is_enabled BOOLEAN DEFAULT TRUE,
    PRIMARY KEY (user_id, channel, category)
);
```

## Indexes
*   `idx_logs_user` on `notification_logs(user_id)`: To show "My Activity" to users.
*   `idx_logs_status` on `notification_logs(status)`: For finding stuck messages.
