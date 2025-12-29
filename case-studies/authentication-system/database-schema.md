# Database Schema

## Technology
*   **Primary DB**: PostgreSQL 15.
*   **Session store**: Redis (Cluster mode).

## Entity Relationship Diagram (ERD)
*Not rendered here, but describes links between Users, Roles, and Devices.*

## Tables

### 1. `users`
Core identity table.
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255), -- Nullable for OAuth users
    is_email_verified BOOLEAN DEFAULT FALSE,
    mfa_enabled BOOLEAN DEFAULT FALSE,
    mfa_secret VARCHAR(100), -- Encrypted at rest
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
-- Index: CREATE INDEX idx_users_email ON users(email);
```

### 2. `auth_providers` (OAuth)
Links users to external providers (Google, GitHub).
```sql
CREATE TABLE auth_providers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    provider_name VARCHAR(50) NOT NULL, -- 'google', 'github'
    provider_subject_id VARCHAR(255) NOT NULL, -- Google's ID for user
    profile_data JSONB, -- Store avatar, raw profile
    UNIQUE (provider_name, provider_subject_id)
);
```

### 3. `refresh_token_families` (Reuse Detection)
Instead of storing the raw token, we track the conceptual "session" chain.
```sql
CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    device_name VARCHAR(100), -- 'Chrome on MacOS'
    ip_address INET,
    last_active_at TIMESTAMPTZ,
    expires_at TIMESTAMPTZ NOT NULL,
    is_revoked BOOLEAN DEFAULT FALSE,
    current_refresh_token_hash VARCHAR(255) -- Hash of the active RT
);
```

### Redis Schema
Used for rapid validation and blacklisting.
*   **Key**: `blacklist:jti:{token_jti}` -> `true` (TTL: remaining token lifetime).
*   **Key**: `session:{user_id}:{device_id}` -> JSON (Cached session metadata).

## Why this Schema?
1.  **Separation of OAuth**: Allows a single user to link multiple social accounts.
2.  **Sessions vs Tokens**: We track `sessions` in PG to allow "View Active Devices" features in the UI.
