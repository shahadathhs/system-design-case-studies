# Analytics Event Pipeline

## System Overview

A massive-scale data ingestion and aggregation pipeline. It captures user interactions (clicks, pageviews, feature usage) and updates real-time dashboards for business intelligence.

## Real-World Usage

Comparable to **Segment, Mixpanel, or the internals of Google Analytics**.

- **Key Challenge**: Writing terabytes of data without losing a single event and without slowing down the client application.

## Expected Scale

- **Throughput**: 50,000 events/sec (avg), 200,000 events/sec (peak).
- **Volume**: 5 TB/day.
- **Latency**:
  - Ingestion: < 10ms (Fire & Forget).
  - Dashboard Update: < 1 minute (Near Real-Time).
