# File Upload Service

## System Overview
A service handling the ingestion, processing (virus scan, transcoding), and delivery of user-generated content (UGC). It ensures that massive file uploads do not block the main API threads.

## Real-World Usage
Comparable to the media handling backend of **Instagram, Dropbox, or YouTube**.
*   **Key Challenge**: Handling large binary payloads efficiently and serving them securely globally.

## Expected Scale
*   **Volume**: 1 Million uploads/day.
*   **Storage**: 50 TB new data/day.
*   **Read Traffic**: 100k requests/sec (via CDN).
*   **File Sizes**: Images (5MB) to Videos (2GB).
