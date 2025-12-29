# System Design Case Studies

## Purpose
This repository serves as a **comprehensive system design portfolio**, demonstrating the architectural decision-making process for high-scale, distributed systems. It is not just a collection of diagrams, but a detailed documentation of the **"why"** behind technical choices.

## Target Audience
- **Interviewers**: To evaluate system design thinking, trade-off analysis, and production-readiness without needing to parse thousands of lines of code.
- **Engineers**: As a reference for practical, production-grade architecture patterns.

## Core Philosophy
This portfolio prioritizes:
1.  **Trade-offs over Tools**: Technology choices are justified by requirements and constraints, not hype.
2.  **Scalability & Reliability**: Designs assume high-scale SaaS environments (millions of users).
3.  **Production Mindset**: "Happy path" is easy; this repo focuses on failure handling, monitoring, and edge cases.
4.  **Communication**: Clear, written technical communication is treated as a primary engineering skill.

## Repository Structure

### 📂 [Fundamentals](./fundamentals)
Concise, expert-level refreshers on core backend concepts. These are not textbook definitions but practical guides on *applying* these concepts in production.
- **Scalability**: Horizontal vs. Vertical, Statelessness.
- **Consistency**: CAP theorem in practice, Eventual Consistency patterns.
- **Storage**: SQL vs. NoSQL decision frameworks.
- **Caching**: Strategies beyond "just cache it" (invalidation, thundering herds).
- **Queues**: Asynchronous processing patterns.
- **API Design**: Idempotency, versioning, and contracts.
- **[Terminology Guide](./fundamentals/system-design-terminology-guide.md)**: A plain-english glossary for self-taught developers.
- **API Design**: Idempotency, versioning, and contracts.
- **[Terminology Guide](./fundamentals/system-design-terminology-guide.md)**: A plain-english glossary for self-taught developers.

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
- `low-level-design.md`: Component internals and algorithms.
- `database-schema.md`: Tables, indexes, and access patterns.
- `api-contracts.md`: Interface definitions.
- `scaling-strategy.md`: Growth plan from MVP to 10M+ users.
- `failure-handling.md`: Resilience patterns (Circuit Breakers, Retries).
- `trade-offs.md`: Critical analysis of alternatives rejected.

### 📂 [Diagrams](./diagrams)
Visual aids supporting the written designs, including flowcharts, sequence diagrams, and architecture maps.
