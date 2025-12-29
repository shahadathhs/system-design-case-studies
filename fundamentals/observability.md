# Observability: Logs, Metrics, & Traces

## Monitoring vs Observability
*   **Monitoring**: "Is the system healthy?" (Yes/No).
*   **Observability**: "Why is the system unhealthy?" (Context).

## The Three Pillars

### 1. Logs (The Events)
**Definition**: A discrete record of something happening.
**Text**: `2023-10-01 12:00:00 [ERROR] User 123 failed login: Invalid Password.`
**Best Practices**:
*   **Structured Logging**: Use JSON. `{ "level": "error", "userId": 123, "msg": "failed" }`.
    *   *Why?* Computers can search JSON (find all errors for User 123). They can't search text easily.
*   **Levels**:
    *   `INFO`: "User logged in".
    *   `WARN`: "Disk 80% full" (Action needed soon).
    *   `ERROR`: "DB connection failed" (Action needed NOW).
    *   `DEBUG`: "Function X called with args Y" (Dev only).

### 2. Metrics (The Numbers)
**Definition**: Aggregated data over time.
**Examples**:
*   `http_requests_per_second`
*   `cpu_usage_percent`
*   `p99_latency_ms`
**Usage**: Dashboards (Grafana). "Look, the line went up!"
**Cardinality Warning**: Don't put User IDs in metrics tags. Millions of unique tags will crash your metrics DB (Prometheus).

### 3. Tracing (The Journey)
**Definition**: Tracking a single request ID as it flows through 5 different microservices.
**The Span**: A segment of the trace.
    *   Span A: Frontend -> Gateway (20ms).
    *   Span B: Gateway -> Auth Service (50ms) [ERROR].
**Usage**: "Why is the Signup page slow?" -> "Oh, the Email Service took 5 seconds."

## Alerting: Waking You Up
**SLA (Service Level Agreement)**: Contract with customer. "99.9% Uptime".
**SLO (Service Level Objective)**: Internal goal. "99.95% Uptime".
**SLI (Service Level Indicator)**: The actual metric. "Current Uptime is 99.96%".

**Golden Rule of Alerting**:
*   Alert on **Symptoms** (User is seeing errors), not **Causes** (CPU is high).
*   If CPU is 100% but requests are fast, do not wake me up.
