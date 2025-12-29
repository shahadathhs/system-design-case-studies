# Scalability Basics

## Overview

Scalability is the property of a system to handle a growing amount of work by adding resources to the system. In backend engineering, this is the differentiator between a "toy app" and a "production system."

## Vertical vs. Horizontal Scaling

### Vertical Scaling (Scaling Up)

Adding more power (CPU, RAM, SSD) to an existing machine.

- **Pros**: Simple to implement. No code changes required. Good for initial growth.
- **Cons**:
  - **Hard Limit**: You eventually hit the ceiling of available hardware.
  - **Downtime**: Often requires restart/maintenance windows.
  - **Single Point of Failure (SPOF)**: If the big box dies, the system dies.
  - **Cost**: High-end hardware has a non-linear price curve.

### Horizontal Scaling (Scaling Out)

Adding more machines (nodes) to a pool of resources.

- **Pros**:
  - **Infinite Scale**: Theoretically limited only by budget and architecture.
  - **Resilience**: Loss of one node doesn't stop the system.
  - **Cost-Effective**: Can use commodity hardware.
  - **Zero-Downtime**: Rolling updates are possible.
- **Cons**:
  - **Complexity**: Requires load balancers, service discovery, and stateless apps.
  - **Data Consistency**: Distributing data across nodes is hard (CAP theorem).

**Verdict**: We always design for **horizontal scalability** from day one for stateless services.

## The Stateless Architecture

To scale horizontally, backend services must be **stateless**.

- **Definition**: A server should not store any client session data in its local memory or file system.
- **Reasoning**: Requests from user A might hit Server 1 now and Server 2 later. If session data is on Server 1, Server 2 won't know who User A is.
- **Solution**: Move state to external shared stores:
  - **Session Data** → Redis / Memcached.
  - **Persistent Data** → Database (PostgreSQL).
  - **Files** → Object Storage (S3).

## Load Balancing

The traffic cop of distributed systems.

- **Layer 4 (Transport)**: Routes based on IP range and port (TCP/UDP). Faster, less context.
- **Layer 7 (Application)**: Routes based on URL path, cookies, headers. Smarter, allows features like sticky sessions (though we try to avoid them) and SSL termination.

**Algorithms**:

- **Round Robin**: Simple, fair if requests are uniform.
- **Least Connections**: Better for long-lived connections (e.g., WebSockets).
- **Consistent Hashing**: Essential for distributed caches to minimize remapping when nodes change.

## Common Pitfalls

1.  **Stateful Services**: Storing uploads on local disk effectively kills horizontal scaling.
2.  **Database Bottlenecks**: Scaling the app tier is easy; scaling the DB is the hard part.
3.  **Premature Optimization**: Don't build Microservices for 100 users. A Modular Monolith on a horizontally scaled group of instances is usually sufficient up to high scale.
