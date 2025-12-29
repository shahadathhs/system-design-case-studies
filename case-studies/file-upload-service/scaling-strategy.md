# Scaling Strategy

## CDN Offloading

- **Reads**: 99% of read traffic never hits our origin servers. It hits CloudFront Edge locations.
- **Cache Hit Ratio**: We optimize this by setting `Cache-Control: public, max-age=31536000, immutable` for all processed assets.

## Global Ingestion (Transfer Acceleration)

- **Problem**: User in Australia uploading to S3 `us-east-1` is slow due to latency.
- **Solution**: **S3 Transfer Acceleration**.
  - User uploads to an AWS Edge Location in Sydney.
  - AWS routes data over its optimized internal backbone fiber to US East.
  - _Cost_: Higher, but 50-300% faster uploads.

## Storage Tiering (Lifecycle Policies)

- **Hot**: Stored in `Standard` S3. (Days 0-90).
- **Warm**: Move to `S3 Standard-IA` (Infrequent Access). (Days 90-365).
- **Cold**: Move to `S3 Glacier Deep Archive`. (Day 365+).
  - _Impact_: Reduces storage bill by ~90% for old data.

## Serverless Processing

- **Workers**: We use AWS Lambda for image resizing.
- **Scaling**: Lambda automatically scales from 0 to 1,000 concurrency based on the SQS queue depth. No servers to patch.
