# Low-Level Design

## Handling Large Files: Multipart Uploads

For files > 100MB, a single PUT request is flaky. If it fails at 99%, you restart from zero.
**Strategy**: S3 Multipart Upload API.

1.  **Initiate**: Client asks API for an `uploadId`.
2.  **Split**: Client splits file locally into 5MB chunks.
3.  **Parallel Upload**: Client uploads chunks in parallel using presigned URLs for each part.
4.  **Complete**: Client tells API "I'm done". API tells S3 to stitch the parts.

## Security: File Validation

A user might rename `virus.exe` to `image.png`.

- **Extension Check**: Shallow validation.
- **MIME Type Check**: Shallow validation.
- **Magic Bytes (Signature)**: **Deep validation**.
  - The Worker reads the first few bytes. PNG starts with `89 50 4E 47`.
  - If magic bytes don't match `.png`, reject the file.
- **ClamAV**: Run virus definition scan.

## Image Processing Pipeline

- **Tool**: `sharp` (Node.js) or `ffmpeg` (CLI).
- **Formats**:
  - Convert all input to **WebP** (modern, small) and **JPEG** (fallback).
  - Strip metadata (EXIF GPS data) for user privacy.
- **Resolution**:
  - `thumbnail`: 150x150 (cover).
  - `medium`: 1080w (feed).
  - `original`: (archive).

## CDN & Access Control

- **Public Assets**: `https://cdn.com/key`. Open to world.
- **Private Assets** (e.g., Chat attachments):
  - Use **CloudFront Signed URLs**.
  - The URL includes an expiry (e.g., 1 hour) and signature.
  - Validates permission at the edge.
