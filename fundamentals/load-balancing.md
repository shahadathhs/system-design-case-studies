# Load Balancing & Traffic Management

## The Concept
If you run a restaurant with 1 waiter, a line forms. If you hire 5 waiters, you need a "Host" at the front door to assign customers to tables.
**Load Balancer (LB)** = The Host.
**Servers** = The Waiters.

## Types of Load Balancing

### Layer 4 (Transport Layer)
*   **Routing**: Based on IP Address and TCP Port.
*   **Logic**: "A packet came for Port 80. I'll forward it to Server A's Port 80."
*   **Pros**: Extremely fast. Computationally cheap.
*   **Cons**: "Dumb". It doesn't know if the user requested `/video` or `/text`. It can't optimize based on content.

### Layer 7 (Application Layer)
*   **Routing**: Based on HTTP Headers, URL Path, Cookies.
*   **Logic**: "User requested `/api/billing`. I will send this to the Billing Microservice."
*   **Pros**: Smart. Enables Microservices routing, SSL Termination, Auth logic.
*   **Cons**: Slower (Needs to decrypt HTTPS, read headers, re-encrypt).
*   **Common Tool**: AWS ALB, Nginx, HAProxy.

## Algorithms: How to Choose a Server?

### 1. Round Robin
*   *Logic*: 1, 2, 3, 1, 2, 3...
*   *Best for*: Servers with equal specs and requests that take equal time.

### 2. Least Connections
*   *Logic*: "Server 1 has 5 active users. Server 2 has 2 active users. Send next to Server 2."
*   *Best for*: Long-lived connections (WebSockets, File Uploads).

### 3. IP Hash
*   *Logic*: Hash the user's IP. Result is 2. Send to Server 2.
*   *Feature*: "Sticky". The same user always reaches the same server. **Avoid relies on this for session state** (makes autoscaling hard).

## Health Checks (The Savior)
The LB doesn't just route blindly. It **checks**.
*   **Active Check**: LB sends `GET /health` to Server A every 5s.
*   **Logic**:
    *   Response 200 OK -> Healthy.
    *   Response 500 or Timeout -> Unhealthy.
*   **Action**: LB stops sending traffic to Unhealthy servers.
*   **Result**: Users never see an error page, even if 1 of your 3 servers crashes.

## Reverse Proxy (Nginx)
A type of LB that sits in front of *one* or *many* servers.
**Functions**:
1.  **SSL Termination**: Handles the heavy encryption work so your Node.js app doesn't have to.
2.  **Compression**: Gzip/Brotli responses.
3.  **Caching**: Serve static files without hitting the App.
4.  **Security**: Block bad IPs before they reach the App.
