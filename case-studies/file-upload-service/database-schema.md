# Database Schema

## Tables

### 1. `media_assets`

Tracks the lifecycle of a file.

```sql
CREATE TABLE media_assets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    uploader_id UUID NOT NULL,
    original_filename VARCHAR(255),
    s3_key_original VARCHAR(500), -- 'raw/uuid-123.png'
    s3_bucket VARCHAR(100),
    mime_type VARCHAR(50),
    size_bytes BIGINT,

    status VARCHAR(20), -- 'UPLOADING', 'PROCESSING', 'READY', 'FAILED', 'INFECTED'

    variants JSONB,
    -- { "thumb": "processed/uuid-thumb.webp", "medium": "..." }

    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ
);
```

### 2. `upload_quotas`

Prevent abuse.

```sql
CREATE TABLE upload_quotas (
    user_id UUID PRIMARY KEY,
    daily_bytes_uploaded BIGINT DEFAULT 0,
    reset_at TIMESTAMPTZ
);
```

## JSONB Usage

We use `variants JSONB` because the number of image sizes might change over time. It allows flexibility without `ALTER TABLE` every time we decide to add a "XXL" size.
