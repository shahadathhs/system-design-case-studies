# API Contracts

## Endpoints

### 1. Initiate Upload

**POST** `/upload/sign`

- **Request**:
  ```json
  {
    "filename": "cat.png",
    "contentType": "image/png",
    "sizeBytes": 5000000
  }
  ```
- **Response**:
  ```json
  {
    "mediaId": "uuid-123",
    "uploadUrl": "https://s3.aws.com/...?Sig=...",
    "fields": { "key": "raw/uuid-123", "AWSAccessKeyId": "..." }
  }
  ```
  _(Note: S3 POST Policy requires specific form fields)_

### 2. Report Completion (Optional)

**POST** `/upload/{mediaId}/complete`

- Triggers immediate check, though we primarily rely on S3 Events.

### 3. Get Asset

**GET** `/assets/{mediaId}`

- **Response**:
  ```json
  {
    "status": "READY",
    "variants": {
      "thumbnail": "https://cdn.com/processed/uuid-thumb.webp",
      "original": "https://cdn.com/processed/uuid-orig.webp"
    }
  }
  ```
