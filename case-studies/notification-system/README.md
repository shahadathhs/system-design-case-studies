# Notification System

## System Overview
A multi-channel notification engine capable of delivering transactional and marketing messages via Email, SMS, Push (Mobile), and In-App Webhooks.

## Real-World Usage
Comparable to **Twilio, Courier, or OneSignal**. It abstracts the complexity of multiple downstream providers (AWS SES, SendGrid, FCM, APNS) from internal product teams.

## Expected Scale
*   **Throughput**: 5,000 notifications/sec (average), 50,000/sec (bursts).
*   **Volume**: 50 Million emails/day.
*   **Latency**:
    *   Transactional (OTP, Reset Password): < 5 seconds delivery.
    *   Marketing: < 30 minutes for bulk sends.
