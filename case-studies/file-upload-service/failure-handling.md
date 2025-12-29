# Failure Handling

## Orphaned S3 Objects

**Scenario**: User uploads file to S3, but closes tab before telling API "I'm done".

- **Result**: "Ghost" file in S3 that isn't in DB. Costs money.
- **Fix**: S3 Lifecycle Rule.
  - `Delete incomplete multipart uploads after 7 days`.
  - `Expire objects in "ingest" bucket after 24 hours` (The Worker moves valid files to "public" bucket; anything left in "ingest" is trash).

## Processing Failures

**Scenario**: `ffmpeg` segfaults on a corrupt video file.

- **DLQ**: Lambda retries 2 times. If fails, moves SQS message to `media-processing-dlq`.
- **Status Update**: Worker marks `media_assets` status as `FAILED`. User sees error in UI.

## Virus Detected

**Scenario**: ClamAV finds malware.

- **Action**:
  1.  Delete file from S3 immediately.
  2.  Mark valid in DB as `INFECTED`.
  3.  Flag User ID for Security Review (Potential attacker).
