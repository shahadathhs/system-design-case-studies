# System Design Interview Playbook
*How to Lead the Room, Structure Your Answers, and Get the Job*

## 1. How System Design Interviews Are Evaluated
Many candidates fail because they focus solely on "Designing the correct architecture."
**The Reality**: There is no "correct" architecture. The goal is to **simulate a planning meeting with a colleague.**

**What Interviewers Look For:**
1.  **Process**: Do you jump to coding, or do you plan first?
2.  **Communication**: Can you explain *why* you chose Postgres over Mongo?
3.  **Collaboration**: Do you ask clarifying questions, or do you guess?
4.  **Limits**: Do you know where your design breaks? (Senior engineers know everything breaks eventually).

**What They Ignore:**
*   Minor syntax errors in diagrams.
*   Forgetting specific port numbers.
*   "Perfect" solutions (they don't exist).

---

## 2. The Universal 7-Step Answering Framework
Memorize this. Write it on the whiteboard immediately. It stops you from getting lost.

1.  **Clarify Requirements**: "What exactly are we building?"
2.  **Define Scale**: "how many users? How much data?" (The constraints).
3.  **Identify Core Entities**: "We need Users, Tweets, and Follows."
4.  **High-Level Design (HLD)**: Draw the big boxes (Client, API, DB).
5.  **Data Storage Decisions**: Choose DBs and Schemas.
6.  **Drill-Down / Bottlenecks**: "Where will this break?" (Scaling).
7.  **Trade-offs & Improvements**: "If I had more time..."

---

## 3. How to Start Strong (First 2 Minutes)
**The Trap**: Interviewer says "Design Twitter." You grab the marker and draw a Database.
**The Fix**: Put the marker down. Talk.

**What to Say**:
> "Sure. Before I start drawing, I'd like to understand the scope. Are we focusing on the 'News Feed' generation part or the 'Posting a Tweet' part? Also, is this for an MVP or do we assume massive scale from day one?"

**Why this works**: It shows you don't build things blindly. You are a **Product-Minded Engineer**.

---

## 4. High-Level Design (HLD): What They Expect
**Goal**: Show the data flow.
**Depth**: 10,000 foot view.

**The Diagram**:
1.  Draw a **Client** (Mobile/Web).
2.  Draw a **Load Balancer** (Always start with this).
3.  Draw an **API Service** (NestJS/Node).
4.  Draw a **Database** (Postgres).

**Explanation**:
> "The client hits the Load Balancer, which routes to a stateless Node.js API. The API allows the user to POST data, which is validated and stored in Postgres."

**Beginner Mistake**: Drawing 50 microservices immediately. Start with a Monolith/Simple Service, then split it *only when you find a bottleneck*.

---

## 5. Low-Level Design (LLD): When to Go Deeper
After the HLD, the interviewer will pick **one** box and say "Zoom in here."
*   "How does the News Feed actually update?"
*   "How is the password stored?"

**How to Execute**:
*   Propose an algorithm or schema.
*   "For the Feed, we could query the DB every time (Pull), or we could pre-calculate feeds (Push)."
*   **Ask**: "Which approach would you prefer I explore?"

---

## 6. How to Talk About Databases
This is where you prove your backend skills.

**SQL vs NoSQL**:
*   **Default to SQL (Postgres)**.
    *   *Reason*: "Most data is relational (data links to users). We need ACID guarantees for payments/profiles."
*   **Switch to NoSQL (Cassandra/Dynamo)** only for:
    *   Massive scale writes (Chat logs).
    *   Unstructured data (Product catalogs).

**Indexing**:
*   "We will index the `email` column because we search by it often."
*   **Trade-off**: "This slows down writes slightly, but reads are 1000x faster."

---

## 7. How to Talk About Scaling (Without Overdoing It)
**The Strategy**: Scale based on **Bottlenecks**, not Hype.

**Common Bottlenecks & Fixes**:
1.  **Read Heavy?** -> Add **Read Replicas** to DB. Add **Cache (Redis)**.
2.  **Write Heavy?** -> Use **Async Queues (RabbitMQ)** to buffer writes. Use **Sharding** (last resort).
3.  **Static Content Heavy?** -> Use a **CDN**.
4.  **Compute Heavy?** -> Add more API Nodes (**Horizontal Scaling**).

**Phrasing**:
> "Since we have 100 reads for every 1 write, I'll add a Redis Cache layer to protect the Database."

---

## 8. Failure, Reliability & Trade-Offs (Senior Signal)
A Junior designs for the Happy Path. A Senior designs for the Crash.

**Key Concepts**:
*   **SPOF (Single Point of Failure)**: "If this DB master dies, we go down. So I'll add a Standby Replica."
*   **Circuit Breakers**: "If the Payment API is down, we should just show 'Try Again' instead of crashing the whole checkout."
*   **Trade-offs**:
    *   "I chose Strong Consistency effectively reducing Availability (CAP theorem)."
    *   "I chose a Cache for speed, accepting that users might see stale data for 30 seconds."

---

## 9. How to Answer Follow-Up Questions Calmly
**Scenario**: "What if traffic triples overnight?"
**Response**: Don't panic.
> "If traffic triples, our Node.js layer auto-scales. The DB might struggle. We should introduce Read Replicas. If that's not enough, we might need to shard based on User ID."

**Scenario**: "I don't like Redis. Why did you choose it?"
**Response**: Don't be defensive.
> "That's fair. I chose it for standard session caching performance. Do you have a specific constraint in mind where Memcached or a DB look-up would be better?"

---

## 10. Common System Design Mistakes
1.  **Tool Dropping**: "I'll use Kafka, Kubernetes, and GraphQL." (Why? Does a To-Do list need Kafka?).
2.  **Silent Drawing**: Drawing for 5 minutes without speaking. (The interviewer assumes you are stuck).
3.  **Ignoring Requirements**: Building a Chat app when they asked for a Video Streaming app.

---

## 11. Sample Interview Walkthroughs

### Scenario A: Design a URL Shortener (TinyURL)
1.  **Clarify**: "Is it just shortening? Do we need analytics? Custom URLs?" (Assume Yes).
2.  **Scale**: "10M links created/month. Read-heavy (100:1)."
3.  **Entities**: `ShortLink` (id, original_url, short_code, created_at).
4.  **HLD**: Client -> API -> DB.
5.  **Storage**:
    *   **Problem**: How to generate `short_code`?
    *   **Solution**: Base62 encoding of a unique ID.
    *   **DB**: Relational (Postgres) or Key-Value (DynamoDB). Start with Relational.
6.  **Scale**: Traffic is high. Cache `short_code -> original_url` in Redis. 99% of hits served from Cache.
7.  **Failure**: If Web Server dies, LB routes to another. If DB dies, replica takes over.

### Scenario B: Design a Rate Limiter
1.  **Clarify**: "Client side or Server side? By IP or User ID?" (Server, User ID).
2.  **Algorithm**: Token Bucket.
3.  **Storage**: Needs to be FAST.
    *   **Choice**: Redis Key-Value Store.
    *   **Key**: `ratelimit:{user_id}`. **Value**: `count`.
    *   **Operation**: `INCR`, `EXPIRE`. atomic operations.
4.  **Trade-off**: If Redis crashes, everyone has unlimited access for a minute (Fail Open). This is better than blocking everyone (Fail Closed).

---

## 12. How to Practice System Design Alone
1.  **The "Shower" Method**: Pick an app (Instagram). Explain *out loud* how you'd build the feed while in the shower.
2.  **Whiteboard**: Buy a real whiteboard or paper. Draw the boxes. Muscle memory matters.
3.  **Read This Repo**: Go through the Case Studies in this repo. Try to re-draw them without looking.
4.  **Mock Interviews**: Record yourself. Watch it back. Do you sound confident? Do you say "um" too much?

*Good luck. You know the code; now you just need to explain the plan.*
