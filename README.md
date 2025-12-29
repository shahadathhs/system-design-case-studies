# System Design Case Studies

A collection of detailed architectural case studies and operational guides for high-scale distributed systems. This repository documents production-grade design patterns, trade-off analyses, and infrastructure decisions for modern backend engineering.

## Repository Structure

### 📂 [Fundamentals](./fundamentals)

Concise, expert-level refreshers on core backend concepts. These guides focus on _applying_ these concepts in production.

- **Scalability**: Horizontal vs. Vertical, Statelessness.
- **Consistency**: CAP theorem in practice, Eventual Consistency patterns.
- **Storage**: SQL vs. NoSQL decision frameworks.
- **Caching**: Strategies beyond "just cache it" (invalidation, thundering herds).
- **Queues**: Asynchronous processing patterns.
- **API Design**: Idempotency, versioning, and contracts.
- **API Design**: Idempotency, versioning, and contracts.
- **[Terminology Guide](./fundamentals/system-design-terminology-guide.md)**: A glossary of essential system design terms.
- **[Interview Playbook](./fundamentals/system-design-interview-playbook.md)**: A 7-step framework for system design discussions.

### 🌐 Networking & DevOps

- **[Networking Basics](./fundamentals/networking-basics.md)**: IPs, Ports, TCP/UDP, and the Request Lifecycle.
- **[DNS Explained](./fundamentals/dns-explained.md)**: How domain names work in production.
- **[Load Balancing](./fundamentals/load-balancing.md)**: L4/L7 LB, Algorithms, and Reverse Proxies.
- **[Containers & K8s](./fundamentals/containers-and-kubernetes.md)**: Docker and Orchestration basics.
- **[CI/CD Pipelines](./fundamentals/ci-cd-pipelines.md)**: Deployment strategies (Blue-Green, Canary).
- **[Observability](./fundamentals/observability.md)**: Logs, Metrics, and Distributed Tracing.

### 📂 [Case Studies](./case-studies)

Deep dives into five realistic storage-heavy and compute-heavy systems. Each case study follows a strict, standardized structure:

1.  **[Authentication System](./case-studies/authentication-system)**: Secure, scalable identity management (JWT, OAuth, Token Rotation).
2.  **[Notification System](./case-studies/notification-system)**: High-throughput, multi-channel alerting (Fan-out, Priority Queues).
3.  **[Real-Time Chat](./case-studies/real-time-chat-system)**: Low-latency messaging at scale (WebSockets, Message Ordering).
4.  **[File Upload Service](./case-studies/file-upload-service)**: Handling large binary data (Multipart, S3, CDN, Security).
5.  **[Analytics Pipeline](./case-studies/analytics-event-pipeline)**: Big data ingestion and processing (Buffering, Batching, Data Lakes).

#### Case Study Documentation Format

Each system is documented with:

- `requirements.md`: Functional & non-functional scoping.
- `high-level-design.md`: Architecture overview and data flow.
- `trade-offs.md`: Critical analysis of alternatives rejected.

### 📂 [Diagrams](./diagrams)

Visual aids supporting the written designs, including flowcharts, sequence diagrams, and architecture maps.
