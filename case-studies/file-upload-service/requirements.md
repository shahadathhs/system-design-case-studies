# Requirements

## Functional Requirements
1.  **Direct Upload**: Users upload directly to Cloud Storage (S3 / GCS) to avoid swamping the API.
2.  **Processing**:
    *   Images: Resize to thumbnails (Small, Medium, Large).
    *   Format: Convert HEIC/PNG to WebP/AVIF.
3.  **Security**:
    *   Virus Scanning (ClamAV) before making public.
    *   Access Control (Private vs Public buckets).
4.  **Resumable Uploads**: Support for 2GB+ video files.

## Non-Functional Requirements
1.  **Reliability**: Zero data corruption.
2.  **Performance**:
    *   Uploads: Dependent on user bandwidth, but low overhead.
    *   Downloads: < 50ms TTFB (Time to First Byte) via CDN.
3.  **Cost Efficiency**: Use Tiered Storage (Hot vs Cold) to manage PB-scale costs.
4.  **Scalability**: Stateless processing workers.
