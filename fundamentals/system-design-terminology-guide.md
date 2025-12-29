# System Design Terminology Guide
*A Practical Reference for Backend Engineers*

## 1. What Is System Design (In Simple Terms)
**System Design** is simply "Architecture planning."

When you write code, you solve a problem in a *single file* or function.
When you do System Design, you solve a problem by connecting *multiple computers* (servers, databases, caches) together.

**Why companies ask this:**
They don't just want to know if you can write a `for` loop. They want to know:
*   "Can you build something that doesn't crash when 100,000 people use it?"
*   "Do you know where to store pictures vs. passwords?"
*   "Do you understand how pieces fit together?"

**Coding vs. System Design**:
*   **Coding**: "How do I make this function efficient?" (Micro optimization)
*   **System Design**: "How do I make sure this service stays online if a data center burns down?" (Macro optimization)

---

## 2. Core Architecture Terminology

### System
**Definition**: The entire collection of code, servers, and data that makes your product work.
**Analogy**: A car. It has an engine, wheels, brakes (components) that work together to move (function).
**Backend Example**: Your "E-commerce System" includes your Node.js API, your Postgres DB, and your Stripe integration.

### Service
**Definition**: A specific piece of the system doing one logical job.
**Analogy**: The "Payment Department" in a company. They only handle money.
**Backend Example**: A `User Service` (encapsulates user login/signup) or `Notification Service` (sends emails).

### Monolith
**Definition**: All your code lives in one project, runs on one server, and uses one database.
**Analogy**: A Swiss Army Knife. It does everything (knife, spoon, saw), but if you lose it, you lose everything.
**Backend Example**: A single NestJS app (`app.module.ts`) containing User, Order, and Payment logic, deployed to one Heroku dyno.
**Interview Usage**: "Start with a Monolith for simplicity, but acknowledge it allows specific scaling issues later."

### Microservices
**Definition**: Splitting the Monolith into tiny, independent server projects that talk to each other over the network.
**Analogy**: A kitchen with a Head Chef, a Pastry Chef, and a Dishwasher. If the Pastry Chef is sick, the Dishwasher can still work.
**Backend Example**: A `Order API` (running on Port 3000) calls the `Payment API` (running on Port 3001) via HTTP.

### Stateless Service
**Definition**: A server that doesn't remember anything about the last request. Every request is treated like a new stranger.
**Analogy**: Dory from Finding Nemo. "Hi, I'm Dory!" ... 5 seconds later ... "Hi, I'm Dory!"
**Backend Example**: A REST API that checks the JWT in the header for *every* request. It doesn't care if you logged in 5 minutes ago or 5 seconds ago; the token tells the truth. **Essential for scaling**.

### Stateful Service
**Definition**: A server that remembers previous interactions in its own memory.
**Analogy**: A conversation with a friend. They remember what you said 5 minutes ago.
**Backend Example**: Storing a user's uploaded file in the server's local `tmp/` folder. If you have 2 servers, and the user asks Server 2 for the file, Server 2 won't have it. (Bad for scaling).

### API Gateway
**Definition**: The single entry point for all clients (Mobile, Web). It routes traffic to the right microservice.
**Analogy**: A Receptionist. "You want loans? Third floor. You want deposits? Second floor."
**Backend Example**: Nginx or a specific Node.js service that forwards `/users` to the User Service and `/orders` to the Order Service.

### Load Balancer
**Definition**: A device that distributes incoming traffic across multiple servers to ensure no single server is overwhelmed.
**Analogy**: The line manager at a grocery store shouting "Register 3 is open!" to keep the lines moving evenly.
**Backend Example**: An AWS Application Load Balancer (ALB) sitting in front of 5 instances of your Node.js API.

### Reverse Proxy
**Definition**: A server that sits in front of your web servers and forwards client requests to those web servers. It adds security and performance.
**Analogy**: A bodyguard. You talk to him, he talks to the VIP. You never touch the VIP.
**Backend Example**: Nginx configured to handle SSL/HTTPS and then forward traffic to your Node.js app running on `localhost:3000`.

---

## 3. High-Level Design vs. Low-Level Design

### High-Level Design (HLD)
**What it means**: The "Big Picture". Boxes and arrows.
**Focus**: Which components do we need? How do they talk to each other? (e.g., "We need a Load Balancer, an API, and a DB").
**Interview Expectation**: Draw the diagram within 10-15 minutes. Prove you understand the relationships.

### Low-Level Design (LLD)
**What it means**: The "Internals". Classes, Functions, Algorithms, Schema.
**Focus**: How does the `User` class look? What columns are in the `orders` table? How do we handle a race condition?
**Interview Expectation**: Write pseudocode or define JSON payloads.

| Feature | High-Level Design (HLD) | Low-Level Design (LLD) |
| :--- | :--- | :--- |
| **View** | 30,000 feet view | Microscope view |
| **Output** | Architecture Diagram | Database Schema / Class Diagram |
| **Question** | "How does data flow?" | "How is data stored?" |
| **Example** | Client -> Gateway -> API -> DB | `users` table has `id`, `email`, `password_hash` |

---

## 4. Database & Storage Terminology

### What is a Database?
**Simple Definition**: A specialized software to store and retrieve data efficiently. It is **not** just a file.

### Table vs Row vs Column (SQL)
*   **Table**: A folder for a specific type of thing (e.g., `Users`).
*   **Row**: A specific item (e.g., `Sajib's Account`).
*   **Column**: A property of that item (e.g., `Email Address`).

### Primary Key (PK)
**Definition**: The unique ID that identifies a specific row.
**Analogy**: Your Social Security Number or Passport Number.
**Backend Example**: `user_id` (UUID) in Postgres.

### Foreign Key (FK)
**Definition**: A reference to a Primary Key in *another* table. It links them validly.
**Analogy**: Your Passport Number written on a Flight Ticket. It links the Ticket to You.
**Backend Example**: The `orders` table has `user_id`. This points to the `users` table.

### Index (Crucial)
**Definition**: A cheat-sheet or "Table of Contents" for the database.
**Why**: Without an index, finding "User with email `bob@gmail.com`" requires scanning 1 million rows. *With* an index, it takes 1 millisecond.
**Trade-off**: Makes **Reads** fast, but **Writes** slightly slower (because you have to update the cheat-sheet).

### Transaction
**Definition**: A set of operations that succeed or fail as a unit.
**Analogy**: Buying a coffee. You give cash AND get coffee. If you give cash but get no coffee, the deal is off (Transaction Rollback).
**Backend Example**: `BEGIN; INSERT INTO orders...; UPDATE inventory...; COMMIT;`

### ACID
The rules that guarantee a Transaction is reliable.
*   **A**tomicity: All or nothing.
*   **C**onsistency: Data is valid (no breaking rules).
*   **I**solation: Transactions don't interfere with each other.
*   **D**urability: Once saved, it stays saved (even if power fails).

### SQL vs NoSQL
*   **SQL (Relational)**: Structured. Tables. strict rules. (Postgres, MySQL). **Use for**: Billing, Users.
*   **NoSQL (Non-Relational)**: Flexible. Documents/JSON. Loose rules. (MongoDB). **Use for**: Catalogs, Feeds, Logs.

### When NOT to use a database
Don't use a DB for ephemeral or temporary state that is frequently accessed. Use a **Cache** (Redis) or **Memory**.

---

## 5. Scalability Terminology

### Scalability
**Definition**: The system's ability to handle **more** (more users, more data) without crashing.

### Vertical Scaling (Scaling Up)
**Definition**: Buying a bigger computer.
**Analogy**: Your backpack is full, so you buy a bigger backpack.
**Backend Example**: Upgrading your AWS EC2 instance from `t2.micro` (1GB RAM) to `m5.large` (8GB RAM).

### Horizontal Scaling (Scaling Out)
**Definition**: Buying **more** computers.
**Analogy**: Your backpack is full, so you ask your friend to carry a second backpack.
**Backend Example**: Running 5 copies of your Node.js API behind a Load Balancer.

### Throughput
**Definition**: How much work the system can do per second.
**Measurement**: Requests Per Second (RPS) or Transactions Per Second (TPS).

### Latency
**Definition**: How long one specific request takes to finish.
**Measurement**: Milliseconds (ms).
**Goal**: Low Latency (Fast).

### Bottleneck
**Definition**: The slowest part of the pipeline that slows down everything else.
**Analogy**: A traffic jam caused by 4 lanes merging into 1.
**Backend Example**: Your Node.js API is fast, but your Database is maxed out at 100% CPU. The DB is the bottleneck.

### Single Point of Failure (SPOF)
**Definition**: If this one thing breaks, the *entire* system stops working.
**Backend Example**: Your database server. If you only have one, and it dies, your app is dead. (Solution: Replication/Backup).

### High Availability (HA)
**Definition**: The system serves traffic basically all the time (e.g., 99.99% uptime).
**How**: By removing SPOFs. (Have 2 databases, 2 servers, etc).

---

## 6. Caching & Performance Terms

### Cache
**Definition**: Fast, temporary storage for data that is expensive to fetch.
**Analogy**: Your backpack's side pocket. It's faster to grab your water bottle from there than digging into the main bag.
**Backend Example**: Storing a user's profile in RAM (Redis) so you don't have to query Postgres every time they refresh the page.

### Cache Hit / Miss
*   **Hit**: Data was found in the cache. (Fast!)
*   **Miss**: Data wasn't there. Must go to the slow DB to fetch it. (Slow).

### TTL (Time To Live)
**Definition**: An expiration timer. "Delete this data after 1 hour."
**Why**: Prevents the cache from holding old/stale data forever.

### Cache Invalidation
**Definition**: The act of removing or updating an item in the cache because the real data changed.
**Difficulty**: "There are only two hard things in Computer Science: cache invalidation and naming things."
**Backend Example**: User changes their password. You MUST delete their old session from Redis immediately.

### Redis
**Definition**: The most popular tool for caching. It's an in-memory database. Super fast.

### CDN (Content Delivery Network)
**Definition**: A network of servers around the world that store copies of your static files (images, CSS).
**Analogy**: Validating your parking ticket. Instead of driving to the central office in NYC, you validate it at a kiosk in your local NJ mall.
**Backend Example**: Serving `logo.png` from Cloudflare servers in London for a user in London, instead of your main server in US.

---

## 7. Asynchronous & Messaging Terms

### Synchronous vs Asynchronous
*   **Synchronous**: Blocking. "I ask, I wait, You answer." (Telephone call).
*   **Asynchronous**: Non-blocking. "I send a message, I go do other work, You answer later." (Text message).

### Message Queue
**Definition**: A buffer that holds tasks until a worker is ready to process them.
**Analogy**: The ticket printer in a restaurant kitchen. The waiter enters orders (Producer), the tickets wait in line (Queue), the chef grabs one (Consumer).
**Backend Example**: RabbitMQ, AWS SQS.

### Producer / Consumer
*   **Producer**: The code creating the task (e.g., `POST /signup`).
*   **Consumer**: The background script processing the task (e.g., `send-welcome-email.js`).

### Event-Driven Architecture
**Definition**: Systems designed around "Events" (things that happened). Services react to events.
**Backend Example**: User Service emits `UserCreated`. Email Service listens and sends email. Analytics Service listens and logs data.

### At-Least-Once Delivery
**Definition**: The guarantee that the message WILL be delivered, but it *might* be delivered twice (e.g., network timeout on acknowledgment).
**Implication**: Your code must handle duplicates safely (Idempotency).

---

## 8. Reliability & Failure Terminology

### Failure vs. Default
**Reality**: "Everything fails, all the time." (Werner Vogels, Amazon CTO).

### Partial Failure
**Definition**: When one part of the system is broken, but the rest works.
**Backend Example**: The "Reviews" service is down, so the Product page loads, but the review section is empty. This is better than the whole page crashing.

### Retry
**Definition**: Trying the same request again if it fails.
**Rule**: Only retry "Transient" errors (network blips). Do NOT retry "400 Bad Request" (User errors).

### Circuit Breaker
**Definition**: A safety switch. If a service fails 10 times in a row, stop calling it for 1 minute to let it recover.
**Analogy**: The fuse box in your house. If you plug in too many heaters, it cuts power to prevent a fire.

### Graceful Degradation
**Definition**: When the system offers simplified functionality rather than crashing completely.
**Analogy**: An escalator that breaks becomes stairs. It still works, just not as well.

---

## 9. Security Terminology (Backend-Focused)

### Authentication (AuthN)
**Definition**: "Who are you?"
**Backend Example**: Checking email/password and issuing a JWT.

### Authorization (AuthZ)
**Definition**: "What are you allowed to do?"
**Backend Example**: Checking if `user.role === 'admin'`.

### JWT (JSON Web Token)
**Definition**: A secure, signed piece of JSON that proves who the user is. It allows **Stateless** auth.
**Analogy**: A stamped wristband at a club. The bouncer doesn't need to check the guest list every time you re-enter; the wristband proves you paid.

### OAuth
**Definition**: A protocol to let users log in using their Google/Facebook account.
**Analogy**: Using your Driver's License to check into a hotel. The hotel trusts the DMV (Google) to verify your ID.

### Hashing (vs Encryption)
*   **Hashing**: One-way. (Password -> `x8f3...`). You cannot turn the hash back into the password. **Used for Passwords.**
*   **Encryption**: Two-way. (Credit Card -> `x8f3...` -> Credit Card). You can get the data back with a key. **Used for Sensitive Data.**

### Rate Limiting
**Definition**: Limiting how many requests a user can make in a minute.
**Why**: Prevents spam and bot attacks.

---

## 10. System Design Interview Language

| Phrase | What they ACTUALLY mean |
| :--- | :--- |
| **"Design a scalable system"** | "Assume this will be huge. Don't use a single server. Use Microservices/Load Balancers." |
| **"What are the trade-offs?"** | "Tell me what is BAD about your choice." (e.g., "SQL is consistent but harder to scale horizontally"). |
| **"What breaks first?"** | "Where is the bottleneck?" (Usually the Database). |
| **"How to scale to 1 million users?"** | "Add Caching, add Read Replicas to DB, use a CDN." |
| **"Make it highly available"** | "Duplicate everything. No Single Point of Failure." |

---

## 11. How to Study System Design Terms Effectively

1.  **Don't memorize definitions**.
    *   Bad: "ACID stands for Atomicity..."
    *   Good: "We need ACID because if the money leaves my account but doesn't reach yours, that's a disaster."
2.  **Draw it out**.
    *   Don't just read about Load Balancers. Draw a box, label it "LB", and draw arrows to 3 "API" boxes. Visualizing helps retention.
3.  **Read Engineering Blogs**.
    *   Companies like Uber, Netflix, and Discord write blogs about *real* problems.
    *   *Task*: Read "How Discord stores billions of messages." You will see these terms (Cassandra, Latency, Cache) in action.
4.  **Practice "Why"**.
    *   Ask yourself: "Why do I need Redis here? Why can't I just put it in a JS variable?"

*End of Guide*
