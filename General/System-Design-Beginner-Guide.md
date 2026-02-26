# System Design - Complete Beginner's Guide

## What is System Design?

**System Design** is the process of defining how different parts of a software application work together to solve a problem at scale. Think of it like designing a blueprint for a building before construction.

**In interviews**, companies want to see:
- How you think through complex problems
- Whether you understand how real-world applications work
- If you can make smart trade-offs (speed vs cost, simplicity vs features)

It's **NOT about having the "perfect" answer** - it's about your thought process and communication.

---

## How to Approach System Design Interviews

### The Step-by-Step Framework:

**1. Clarify Requirements (5 minutes)**
- Ask questions! Don't assume.
- "How many users will use this?"
- "Do we need to store data forever or can we delete old data?"
- "Does it need to be real-time or can we have delays?"

**2. Define Scope (2 minutes)**
- List what you WILL build
- List what you WON'T build (out of scope)
- Example: "We'll focus on posting tweets, not on direct messages"

**3. High-Level Design (10 minutes)**
- Draw simple boxes and arrows
- Client → Server → Database
- Explain what each component does

**4. Deep Dive (15 minutes)**
- Pick 2-3 important parts to explain in detail
- Discuss potential problems and solutions
- Talk about scaling, failures, performance

**5. Discuss Trade-offs (5 minutes)**
- "If we do X, we get Y benefit but Z cost"
- Show you understand there's no perfect solution

---

## Key Concepts to Learn (Fundamentals)

### 1. **Client-Server Model**
- **What**: Your phone (client) talks to a computer far away (server)
- **Example**: When you open Instagram, your phone requests photos from Instagram's servers
- **Why it matters**: Foundation of all web applications

### 2. **Database**
- **What**: Where you store data permanently
- **Types**: 
  - SQL (structured, like Excel tables) - PostgreSQL, MySQL
  - NoSQL (flexible, like JSON documents) - MongoDB
- **When to use**:
  - SQL: Banking, e-commerce (need strict rules)
  - NoSQL: Social media feeds, logs (flexible data)

### 3. **Caching**
- **What**: Storing frequently-used data in fast memory
- **Example**: Instead of fetching your profile picture from database every time, keep it in cache
- **Tool**: Redis, Memcached
- **Why**: Makes things 10-100x faster

### 4. **Load Balancer**
- **What**: Distributes incoming requests across multiple servers
- **Example**: Like a restaurant host seating customers at different tables so no server is overwhelmed
- **Why**: Handles more users, prevents one server from dying

### 5. **CDN (Content Delivery Network)**
- **What**: Servers located worldwide that store copies of your images/videos
- **Example**: Netflix stores movies in servers close to you, not just in California
- **Why**: Faster loading, less distance = less time

### 6. **API (Application Programming Interface)**
- **What**: A way for different software to talk to each other
- **Example**: When a weather app shows weather, it calls a weather API
- **Types**: REST (most common), GraphQL

### 7. **Scalability**
- **Vertical**: Make one server more powerful (add more RAM)
- **Horizontal**: Add more servers
- **When**: Vertical is easier but has limits; horizontal scales infinitely

---

## Simple Examples to Practice

### Example 1: URL Shortener (like bit.ly)

**Problem**: Convert long URLs to short ones

**Simple Design**:
1. User enters long URL → Server generates short code (abc123)
2. Store mapping in database: `abc123 → https://verylongurl.com`
3. When someone visits `bit.ly/abc123`, look up database, redirect

**Key Points**:
- Use hashing to generate unique codes
- Database needs to be fast (use cache)
- Handle millions of URLs (need multiple servers)

---

### Example 2: Design a Parking Lot System

**Problem**: Track which parking spots are available

**Simple Design**:
1. Database stores: spot number, is_occupied, vehicle_type
2. When car enters: find available spot, mark as occupied
3. When car leaves: mark spot as available, calculate payment

**Key Points**:
- Different spot sizes (compact, large, handicap)
- Real-time availability
- Handle concurrent requests (two people trying same spot)

---

### Example 3: Design Instagram Photo Feed

**Problem**: Show photos from people you follow

**Simple Design**:
1. **Upload**: User uploads photo → Store in file storage (S3)
2. **Feed**: Get list of people you follow → Fetch their recent photos from database
3. **Display**: Show photos from newest to oldest

**Key Points**:
- Photos stored in CDN for fast loading
- Cache popular photos
- Database stores metadata (who posted, when, likes)
- Use pagination (load 10 photos at a time, not all)

---

## Tips for Freshers

1. **Draw everything** - Interviewers love visual diagrams
2. **Think out loud** - Share your reasoning
3. **Start simple** - Basic design first, then add complexity
4. **Ask questions** - Shows you care about requirements
5. **Admit what you don't know** - "I'm not familiar with X, but I would approach it by..."

## Resources to Learn

- **YouTube**: "System Design Primer" by Gaurav Sen (easy Hindi/English mix)
- **Website**: GitHub "System Design Primer" (comprehensive guide)
- **Practice**: Design everyday apps (WhatsApp, Uber, YouTube)

---

## Additional Practice Problems for Beginners

### Easy Level:
1. Design a simple calculator app
2. Design a basic chat application
3. Design a file storage system (like Google Drive)
4. Design a basic e-commerce shopping cart
5. Design a simple notification system

### Medium Level:
1. Design Twitter
2. Design Uber
3. Design a rate limiter
4. Design a web crawler
5. Design YouTube

---

## Common Interview Questions

### Q: What's the difference between horizontal and vertical scaling?
**A**: Vertical scaling = making one machine more powerful (add RAM/CPU). Horizontal scaling = adding more machines. Horizontal is better for large scale but more complex.

### Q: When would you use SQL vs NoSQL?
**A**: Use SQL when you need strict data relationships and transactions (banking, inventory). Use NoSQL when you need flexibility and speed (social media, real-time analytics).

### Q: What is a single point of failure?
**A**: A component that, if it fails, brings down the entire system. Example: having only one database server. Solution: add redundancy (multiple servers).

### Q: How do you handle millions of users?
**A**: 
1. Use load balancers to distribute traffic
2. Add caching to reduce database load
3. Use CDN for static content
4. Scale horizontally (add more servers)
5. Use database sharding (split data across multiple databases)

---

## Key Metrics to Consider

1. **Latency**: How fast does the system respond? (milliseconds)
2. **Throughput**: How many requests can it handle per second?
3. **Availability**: What percentage of time is it working? (99.9% = "three nines")
4. **Consistency**: Do all users see the same data at the same time?
5. **Scalability**: Can it handle 10x or 100x more users?

---

## Common Design Patterns

### 1. **Master-Slave Pattern**
- One main database (master) handles writes
- Multiple read databases (slaves) handle reads
- Used when you have more reads than writes

### 2. **Microservices**
- Break application into small, independent services
- Each service does one thing well
- Example: User service, Payment service, Notification service

### 3. **Event-Driven Architecture**
- Components communicate through events/messages
- Example: When user places order → triggers payment event → triggers shipping event
- Tool: Kafka, RabbitMQ

---

## Glossary of Important Terms

- **API Gateway**: Single entry point for all client requests
- **Sharding**: Splitting database into smaller pieces
- **Replication**: Copying data to multiple servers
- **Idempotency**: Running same operation multiple times gives same result
- **Eventual Consistency**: Data becomes consistent over time, not immediately
- **CAP Theorem**: You can only have 2 of: Consistency, Availability, Partition Tolerance

---

Remember: As a fresher, you're not expected to know everything. Show curiosity, logical thinking, and willingness to learn. Good luck! 🚀

---

**Created on**: February 6, 2026  
**Purpose**: Study guide for system design interview preparation









## IN DEPTH ------------------------------------------------------------------------------------



# System Design - Complete Beginner's Guide

## What is System Design?

**System Design** is the process of defining how different parts of a software application work together to solve a problem at scale. Think of it like designing a blueprint for a building before construction.

**In interviews**, companies want to see:
- How you think through complex problems
- Whether you understand how real-world applications work
- If you can make smart trade-offs (speed vs cost, simplicity vs features)

It's **NOT about having the "perfect" answer** - it's about your thought process and communication.

---

## How to Approach System Design Interviews

### The Step-by-Step Framework:

**1. Clarify Requirements (5 minutes)** - *Most Important Step*

Ask questions to understand WHAT you're building:

**Functional Requirements** (What should the system do?):
- "Should users be able to edit/delete posts?"
- "Do we need notifications?"
- "Should it work on mobile and web?"

**Non-Functional Requirements** (How should it perform?):
- **Scale**: "How many daily users? 1K, 1M, 100M?"
- **Performance**: "Should page load in <1 sec?"
- **Storage**: "How long to keep data? 1 year? Forever?"
- **Availability**: "Can we have downtime or must be 24/7?"
- **Latency**: "Real-time (WhatsApp) or delayed OK (email)?"

**Example Dialog**:
```
Interviewer: Design a URL shortener
You: Great! A few questions:
- How many URLs shortened per day? 
- Do shortened URLs expire?
- Can users customize short URLs?
- Do we need analytics (click count)?
- What's the expected traffic? 100 reads per write?
```

---

**2. Define Scope (2 minutes)** - *Set Boundaries*

Write down clearly:

**IN SCOPE** (What you'll design):
- User can shorten a URL
- User can access original URL via short link
- Basic analytics (click count)

**OUT OF SCOPE** (What you won't design):
- User authentication/login
- Detailed analytics dashboard
- URL validation
- Custom domains

*Why?* You can't design everything in 45 min. Focus on core features.

---

**3. High-Level Design (10 minutes)** - *The Big Picture*

Draw boxes and arrows on whiteboard:

```
[Mobile App]  ──┐
                 ├──> [Load Balancer] ──> [API Servers] ──> [Database]
[Web Browser] ──┘                              │
                                               └──> [Cache]
```

**Explain each box**:
- Load Balancer: Distributes traffic across servers
- API Servers: Business logic (3-5 servers)
- Database: Permanent storage
- Cache: Fast temporary storage

**Define APIs** (what requests happen):
- `POST /shorten` - Create short URL
- `GET /{shortCode}` - Redirect to original URL
- `GET /stats/{shortCode}` - Get click analytics

*Walk through one flow*: "When user clicks bit.ly/abc123..."

---

**4. Deep Dive (15 minutes)** - *Show Your Expertise*

Pick 2-3 critical components and go deep:

**A. Database Schema**:
```sql
Table: url_mappings
- short_code (string, PRIMARY KEY)
- original_url (string)
- created_at (timestamp)
- user_id (integer, optional)
- click_count (integer)
```

**B. Short Code Generation Algorithm**:
- Option 1: Hash original URL → Take first 7 chars
  - Problem: Collisions possible
- Option 2: Counter-based (encode ID in base62)
  - Better: No collisions, predictable length
  
**C. Scaling Issues & Solutions**:
- Problem: Database slow with 1B URLs
  - Solution: Add Redis cache for popular URLs
- Problem: Single database can't handle writes
  - Solution: Database sharding (split by first char of code)

---

**5. Discuss Trade-offs (5 minutes)** - *No Right Answer*

Show you understand engineering decisions:

| Decision | Pros | Cons | When to Use |
|----------|------|------|-------------|
| Use Cache | Fast reads, reduce DB load | Data might be stale | Read-heavy systems |
| Use SQL DB | ACID guarantees, relations | Slower at scale | Financial systems |
| Use NoSQL | Fast, flexible schema | No joins, eventual consistency | Social media |
| Vertical Scaling | Simple, no code changes | Limited, expensive | Early stage startup |
| Horizontal Scaling | Unlimited growth | Complex, needs load balancer | High traffic apps |

**Example**: "I chose Redis cache because 90% of traffic hits 10% of URLs. This reduces DB load by 80% but adds complexity of cache invalidation."

---

## Key Concepts to Learn (Fundamentals)

### 1. **Client-Server Model**

**Simple Explanation**: Your phone/browser (client) sends requests to a remote computer (server) which sends back responses.

**Real Example**:
```
You (client) → "Show me cat videos" → YouTube Server
YouTube Server → "Here's the video file" → You (client)
```

**Why it matters**: 
- Separation of concerns: UI on client, logic on server
- Multiple clients (iOS, Android, Web) can use same server
- Server can serve millions of clients

**Deep Dive**:
- **Request-Response Cycle**: Every action is a request → server processes → sends response
- **Stateless**: Server doesn't remember previous requests (that's why we use cookies/tokens)
- **Protocols**: HTTP/HTTPS for web, WebSocket for real-time (chat apps)

---

### 2. **Database** - *Your System's Memory*

**SQL (Relational) Databases**:

Think of Excel sheets with strict rules:
- **Structure**: Tables with fixed columns
- **Relationships**: Connect tables (User table ↔ Orders table)
- **ACID Properties**:
  - **A**tomic: All or nothing (if bank transfer fails, money returns)
  - **C**onsistent: Data always valid (can't have negative balance)
  - **I**solated: Two people can't book same seat
  - **D**urable: Once saved, never lost

**When to use SQL**:
- Banking (money transfers must be exact)
- E-commerce (inventory counts, orders)
- When data has relationships (users → posts → comments)

**Examples**: PostgreSQL, MySQL, Oracle

---Detailed Examples to Practice

### Example 1: URL Shortener (like bit.ly) - **COMPLETE DESIGN**

---

#### **Step 1: Requirements Clarification**

**Functional**:
- Shorten long URL to 6-7 character code
- Redirect short URL to original URL
- Track click analytics
- Optional: Custom short URLs

**Non-Functional**:
- **Scale**: 100M URLs, 10B redirects/month
- **Availability**: 99.9% uptime
- **Latency**: <200ms redirect time
- **Read:Write Ratio**: 100:1 (mostly reads)

---

#### **Step 2: Capacity Estimation**

**Storage**:
- 100M URLs × 500 bytes/URL = 50 GB/year
- Keep for 10 years = 500 GB total ✅ (fits in single DB)

**Bandwidth**:
- Writes: 100M URLs/month ≈ 40 writes/sec
- Reads: 10B redirects/month ≈ 4000 reads/sec

**Cache Memory** (80-20 rule):
- Cache 20% of daily requests
- 4000 req/sec × 86400 sec = 345M requests/day
- 20% × 345M × 500 bytes = 35 GB cache

---

#### **Step 3: API Design**

```
1. POST /api/shorten
   Request: { "original_url": "https://very-long-url.com/..." }
   Response: { "short_url": "https://bit.ly/aB3Xy7" }

2. GET /{shortCode}
   Response: HTTP 301 Redirect to original URL

3. GET /api/stats/{shortCode}
   Response: { "clicks": 1234, "created": "2026-01-01" }
```

---

#### **Step 4: Database Schema**

```sql
Table: url_mappings
┌──────────────┬───────────────────────────────┬────────────┐
│ short_code   │ original_url                  │ created_at │
├──────────────┼───────────────────────────────┼────────────┤
│ aB3Xy7       │ https://long-url.com/...      │ 2026-01-01 │
│ xY9Zk2       │ https://another-url.com/...   │ 2026-01-02 │
└──────────────┴───────────────────────────────┴────────────┘
PRIMARY KEY: short_code (indexed for fast lookups)
INDEX: created_at (for analytics queries)
```

---

#### **Step 5: Short Code Generation (Critical Algorithm)**

**Option 1: MD5 Hash** ❌
```
original_url → MD5 hash → Take first 7 chars
Problem: Collisions (two URLs might get same code)
```

**Option 2: Auto-increment ID + Base62 Encoding** ✅
```javascript
// Counter: 1, 2, 3, 4...
// Convert to Base62: [a-zA-Z0-9]

ID: 1 → Base62: "b"
ID: 62 → Base62: "ba"
ID: 125000000 → Base62: "aB3Xy7" (7 chars)

// Benefits:
- No collisions
- Predictable length
- 62^7 = 3.5 trillion unique codes
```

**Code**:
```javascript
function base62Encode(num) {
  const chars = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
  let result = '';
  while (num > 0) {
    result = chars[num % 62] + result;
    num = Math.floor(num / 62);
  }
  return result.padStart(7, '0');
}
```

---

#### **Step 6: High-Level Architecture**

```
┌──────────────┐
│ User Browser │
└──────┬───────┘
       │
       ▼
┌──────────────────┐
│  Load Balancer   │  (Nginx)
└────────┬─────────┘
         │
    ┌────┴────┐
    ▼         ▼
┌────────┐ ┌────────┐
│ API    │ │ API    │  (Node.js/Python)
│ Server │ │ Server │
└───┬────┘ └───┬────┘
    │          │
    └────┬─────┘
         ▼
    ┌─────────┐
    │  Redis  │  (Cache hot URLs)
    └────┬────┘
         │
         ▼
    ┌──────────┐
    │ PostgreSQL│  (Persistent storage)
    └──────────┘
```

---

#### **Step 7: Deep Dive - Read Flow (Redirect)**

```
1. User clicks bit.ly/aB3Xy7
2. Load balancer → API Server
3. Server checks Redis: GET "aB3Xy7"
   - Cache HIT → Return URL (1ms) ⚡
   - Cache MISS → Query DB → Update cache → Return (50ms)
4. Server returns HTTP 301 redirect
5. Browser navigates to original URL
6. Async: Increment click counter in DB
```

**Why 301 vs 302 redirect?**
- **301** (Permanent): Browser caches, faster but can't track clicks
- **302** (Temporary): Always hits server, slower but tracks every click
- **Decision**: Use 302 for analytics

---

#### **Step 8: Deep Dive - Write Flow (Shorten URL)**

```
1. POST /api/shorten with long URL
2. Check if URL already shortened (query DB by original_url)
   - If exists → Return existing short code
   - If new → Continue
3. Generate new ID from counter
4. Encode ID to Base62 → short_code
5. Store in DB: (short_code, original_url, timestamp)
6. Optionally pre-warm cache (write to Redis)
7. Return short_url to user
```

**Counter Service** (for ID generation):
- Use Redis INCR (atomic operation)
- OR use Database auto-increment
- OR use Snowflake ID (distributed unique IDs)

---

#### **Step 9: Scaling Strategies**

**Problem 1**: Database bottleneck at 10K writes/sec
**Solution**: 
- Shard database by short_code range (A-M → DB1, N-Z → DB2)
- OR use consistent hashing

**Problem 2**: Cache memory full
**Solution**:
- LRU eviction (remove least used URLs)
- OR Probabilistic cache (only cache URLs with >10 clicks)

**Problem 3**: Single point of failure (one DB)
**Solution**:
- Master-slave replication
- If master dies, promote slave to master

**Problem 4**: Analytics slow (counting clicks)
**Solution**:
- Use message queue (Kafka)
- Async write to analytics DB (Cassandra for time-series)
- Don't block redirect with analytics

---

#### **Step 10: Advanced Features**

**Custom Short URLs**:
```
User wants: bit.ly/my-startup
- Check if "my-startup" available in DB
- If available → Reserve it
- If taken → Return error
```

**Expiration**:
```sql
ALTER TABLE url_mappings ADD COLUMN expires_at TIMESTAMP;
-- Background job deletes expired URLs every hour
```

**Rate Limiting** (prevent abuse):
```
User can shorten max 100 URLs/hour
- Use Redis: INCR user_ip:count
- Set TTL 1 hour
```

---

### Example 2: Instagram Photo Feed - **COMPLETE DESIGN**

---

#### **Step 1: Requirements**

**Functional**:
- Upload photos with caption
- Follow/unfollow users
- View feed (photos from people you follow)
- Like/comment on photos
- View user profile

**Non-Functional**:
- 500M daily active users
- Each user follows 200 people
- Each user uploads 2 photos/day = 1B photos/day
- Feed load time <500ms
- Photo upload <2 seconds

---

#### **Step 2: Capacity Estimation**

**Storage** (Photos):
- 1B photos/day × 1MB avg size = 1 PB/day (!!)
- Store for 5 years = 1.8 EB (need distributed storage)

**Storage** (Metadata):
- 1B photos × 500 bytes = 500 GB/day
- 5 years = 900 TB metadata

**Bandwidth**:
- Upload: 1B photos/day × 1MB = 12 GB/sec
- Read: 500M users × 20 photos/session × 1MB = 116 TB/sec (!)
  - Solution: CDN caches most photos

---

#### **Step 3: Database Schema**

```sql
Table: users
- user_id (PRIMARY KEY)
- username, email, profile_pic
- created_at

Table: photos
- photo_id (PRIMARY KEY)
- user_id (FOREIGN KEY)
- photo_url (S3 path)
- caption
- created_at

Table: follows
- follower_id (who follows)
- followee_id (who is followed)
- created_at
- PRIMARY KEY (follower_id, followee_id)

Table: likes
- photo_id, user_id, created_at
- PRIMARY KEY (photo_id, user_id)

Table: comments
- comment_id, photo_id, user_id, text, created_at
```

---

#### **Step 4: High-Level Architecture**

```
┌──────────────┐
│  Mobile App  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ CDN (Images) │ ← Most traffic (99%)
└──────────────┘
       │
       ▼
┌──────────────┐
│Load Balancer │
└──────┬───────┘
       │
   ┌───┴────┐
   ▼        ▼
┌─────┐  ┌─────┐
│ API │  │ API │  (Photo service, Feed service, User service)
└──┬──┘  └──┬──┘
   │        │
   └────┬───┘
        │
    ┌───┴────┬─────────┬───────────┐
    ▼        ▼         ▼           ▼
┌────────┐ ┌────┐  ┌──────┐  ┌─────────┐
│  S3    │ │Redis│ │ SQL  │  │ Kafka   │
│(Photos)│ │Cache│ │  DB  │  │ (Events)│
└────────┘ └────┘  └──────┘  └─────────┘
```

---

#### **Step 5: Photo Upload Flow**

```
1. User selects photo → App compresses (reduce size)
2. POST /api/upload
3. API server generates unique photo_id
4. Upload photo to S3: s3://bucket/user123/photo456.jpg
5. S3 triggers Lambda → Generate thumbnails (3 sizes)
6. Store metadata in SQL:
   INSERT INTO photos (photo_id, user_id, photo_url, caption)
7. Publish event to Kafka: "UserX uploaded photo"
8. Fan-out service pushes to followers' feeds (async)
9. Return success to user
```

---

#### **Step 6: Feed Generation (Two Approaches)**

**Approach 1: Pull Model (Generate on demand)** ⏱️
```
User opens app → GET /api/feed
1. Query: SELECT followee_id FROM follows WHERE follower_id = me
2. Get 200 people I follow
3. Query: SELECT * FROM photos WHERE user_id IN (200 people)
         ORDER BY created_at DESC LIMIT 20
4. Return 20 most recent photos

Pros: Real-time, no pre-computation
Cons: Slow (join 200 users), high DB load
```

**Approach 2: Push Model (Pre-generate feeds)** ✅ ⚡
```
When UserX uploads photo → Fan-out worker runs
1. Get all followers of UserX: 1M followers
2. For each follower → INSERT photo into their feed cache (Redis)
3. User opens app → Read pre-built feed from Redis (super fast!)

Pros: Fast feed load (<100ms)
Cons: Slow upload for celebrities (1M writes), wasted for inactive users
```

**Hybrid Approach** (Instagram's actual method):
- Regular users (<10K followers): Push model
- Celebrities (>10K followers): Pull on demand
- Feed = Mix of pre-built cache + on-demand fetch

---

#### **Step 7: Ranking Algorithm (Simple)**

```
Feed not just chronological, rank by:
- Recency: Newer photos rank higher
- Affinity: Photos from close friends rank higher
- Engagement: Photos with many likes rank higher

Score = 0.5 * recency + 0.3 * affinity + 0.2 * engagement

Then sort feed by score
(Real Instagram uses ML models)
```

---

#### **Step 8: Scaling Strategies**

**Problem 1**: 1B photos/day, single DB can't handle
**Solution**: Shard by photo_id
- photo_id 0-99M → DB1
- photo_id 100M-199M → DB2
- Use consistent hashing

**Problem 2**: Feed query slow (joining 200 users)
**Solution**: 
- Denormalize: Store feed in separate table
- Use Redis sorted sets (ZADD feed:user123 timestamp photo_id)

**Problem 3**: Hot celebrity (10M likes on one photo)
**Solution**:
- Cache like counts in Redis
- Don't update DB in real-time
- Batch update every 1 min

**Problem 4**: Image storage too expensive
**Solution**:
- Compress images (Instagram uses WebP, HEIC)
- Delete original after 30 days, keep only thumbnails
- Move old photos to Glacier (cheap cold storage)

---

### Example 3: Design WhatsApp - **COMPLETE DESIGN**

---

#### **Step 1: Requirements**

**Functional**:
- One-on-one chat
- Group chat (max 256 people)
- Send text, images, videos
- Online/offline status
- Delivery receipts (single tick, double tick, blue tick)
- End-to-end encryption

**Non-Functional**:
- 2B users, 100M online simultaneously
- <100ms message delivery
- 99.999% availability (5 mins downtime/year)
- Messages stored for 30 days

---

#### **Step 2: Core Challenge**

**Real-time bidirectional communication**:
- HTTP request-response doesn't work (server can't push to client)
- Need **WebSocket** (persistent connection)

```
Traditional HTTP:
Client → Request → Server
Client ← Response ← Server
(connection closes)

WebSocket:
Client ←→ Persistent Connection ←→ Server
(server can push anytime)
```

---

#### **Step 3: High-Level Architecture**

```
┌──────────┐         ┌──────────┐
│  Alice   │         │   Bob    │
│  (Phone) │         │ (Phone)  │
└────┬─────┘         └────┬─────┘
     │ WebSocket          │ WebSocket
     │                    │
     ▼                    ▼
┌─────────────────────────────┐
│   WebSocket Servers         │ (Sticky sessions)
│   (Node.js, Socket.io)      │
└──────────┬──────────────────┘
           │
      ┌────┴────┐
      ▼         ▼
 ┌────────┐ ┌────────┐
 │ Redis  │ │ Kafka  │ (Message queue)
 │ PubSub │ └────────┘
 └────────┘
      │
      ▼
 ┌──────────┐
 │ Cassandra│ (Store messages)
 └──────────┘
```

---

#### **Step 4: Message Send Flow**

```
1. Alice types "Hi Bob" → Send via WebSocket to Server A
2. Server A receives message
3. Generate message_id (unique)
4. Publish to Kafka topic: "message.send"
5. Store in Cassandra:
   INSERT INTO messages (message_id, from_user, to_user, text, timestamp)
6. Send back to Alice: ✓ (single tick = sent to server)
7. Check if Bob online:
   - If online: Find Bob's WebSocket server → Push message
   - If offline: Store in Redis pending_messages:bob
8. Bob receives → Send back to Alice: ✓✓ (double tick = delivered)
9. Bob reads message → Send to Alice: Blue ticks (read receipt)
```

---

#### **Step 5: Online/Offline Status**

```
User connects → WebSocket established → Mark user online in Redis
SET user:bob:status "online" EX 60  (expire in 60 sec)

WebSocket server sends heartbeat every 30 sec
EXPIRE user:bob:status 60  (refresh TTL)

If user disconnects or no heartbeat → Status becomes offline
```

---

#### **Step 6: Group Chat (256 people)**

**Naive approach**: Send message to all 256 people individually ❌
- 256 database writes, slow

**Better approach**: Fan-out pattern ✅
```
1. Alice sends message to group_123
2. Store message once:
   INSERT INTO group_messages (group_id, message_id, from_user, text)
3. Get all group members (cached in Redis)
4. Publish to Kafka: "group_message:group_123"
5. Fan-out worker reads from Kafka
6. For each online member → Push via WebSocket
7. For offline members → Store in pending queue
```

**Optimization**: Don't send heavy data (images) 256 times
- Upload image to S3 once
- Send only URL to group members

---

#### **Step 7: Scaling Strategies**

**Problem 1**: 100M concurrent WebSocket connections
**Solution**: 
- Each server handles 64K connections (OS limit)
- Need 1,562 servers minimum
- Use consistent hashing: user_id → server
- Store mapping in Redis: user:alice:server → "server_42"

**Problem 2**: Message delivery when user switches servers
**Solution**: 
- Use Redis Pub/Sub
- All WebSocket servers subscribe to user's channel
- When message arrives, publish to Redis channel
- Server with user's connection delivers it

**Problem 3**: Database can't handle 1M writes/sec
**Solution**:
- Use Cassandra (distributed, write-optimized)
- Shard by chat_id (each chat independent)

**Problem 4**: Messages lost if server crashes
**Solution**:
- Kafka acts as buffer (persists messages)
- If server crashes, replay from Kafka

---

This should give you deep understanding with practical examples! Each example shows real-world constraints and solutions that actual companies use.
**Example Scenario**:
```
Without Cache:
User requests profile → Server queries DB (50ms) → Slow!

With Cache:
First request → Query DB (50ms) → Store in Redis
Next 1000 requests → Get from Redis (1ms) → Fast!
```

**Cache Strategies**:
- **Cache-Aside**: App checks cache first, if miss → fetch from DB → update cache
- **Write-Through**: Write to cache AND DB together
- **Write-Behind**: Write to cache, async write to DB (risky but fast)

**Cache Eviction** (when cache full):
- **LRU** (Least Recently Used): Remove oldest unused items
- **TTL** (Time To Live): Auto-delete after X seconds

**Pro Tip**: Cache the 20% data that gets 80% traffic (Pareto principle)

---

### 4. **Load Balancer** - *Traffic Cop*

**Problem**: One server can handle ~10K concurrent users. What if you have 100K users?

**Solution**: Use multiple servers + Load Balancer to distribute traffic

**How it works**:
```
         [Load Balancer]
              |
    ┌─────────┼─────────┐
    ▼         ▼         ▼
[Server 1] [Server 2] [Server 3]
```

**Load Balancing Algorithms**:

1. **Round Robin**: Rotate requests (S1 → S2 → S3 → S1...)
   - Simple but doesn't consider server load
   
2. **Least Connections**: Send to server with fewest active connections
   - Better for varying request times
   
3. **IP Hash**: Same user always goes to same server
   - Good for session persistence
   
4. **Weighted**: Powerful servers get more traffic
   - Use when servers have different capacities

**Types**:
- **Hardware LB**: Physical device (F5, Citrix) - expensive, fast
- **Software LB**: Nginx, HAProxy - cheap, flexible
- **Cloud LB**: AWS ELB, Google Cloud Load Balancer - managed

**Health Checks**: LB pings servers every 5 sec. If server down, stop sending traffic.

**Benefits**:
- High availability (if one server dies, others handle traffic)
- Easy scaling (add more servers without code changes)
- Zero-downtime deployments (update servers one by one)

---

### 5. **CDN (Content Delivery Network)** - *Bring Data Closer*

**Problem**: User in India accessing server in USA = 200ms delay (speed of light limit!)

**Solution**: Copy static files (images, videos, CSS) to servers worldwide

**How it works**:
```
User in India → Request cat.jpg
CDN checks: Do I have cat.jpg in India server?
- YES → Serve from India (20ms) ✅
- NO → Fetch from USA (200ms) → Cache in India → Serve
```

**What to put in CDN**:
- ✅ Images, videos, CSS, JavaScript (static, rarely change)
- ❌ User-specific data, API responses (dynamic, frequently change)

**Popular CDNs**: Cloudflare, AWS CloudFront, Akamai, Fastly

**Advanced**: CDN also provides DDoS protection, SSL, compression

---

### 6. **API (Application Programming Interface)** - *Contract Between Systems*

**Simple Analogy**: Restaurant menu is an API
- You (client) order "Burger with fries" (request)
- Kitchen (server) doesn't show you how they cook
- You get burger (response)

**REST API** (Most Common):

Standard HTTP methods:
- **GET** `/users/123` - Fetch user
- **POST** `/users` - Create new user
- **PUT** `/users/123` - Update entire user
- **PATCH** `/users/123` - Update specific field
- **DELETE** `/users/123` - Remove user

**Example**:
```javascript
// Request
GET /api/posts?user_id=123&limit=10

// Response
{
  "posts": [
    {"id": 1, "text": "Hello", "likes": 5},
    {"id": 2, "text": "World", "likes": 3}
  ],
  "total": 2
}
```

**REST Best Practices**:
- Use nouns, not verbs (`/users` not `/getUsers`)
- Use HTTP status codes (200 OK, 404 Not Found, 500 Error)
- Versioning: `/v1/users`, `/v2/users`

**GraphQL** (Modern Alternative):

Client requests exactly what they need:
```graphql
query {
  user(id: "123") {
    name
    posts(limit: 5) {
      title
      likes
    }
  }
}
```

**When to use**:
- REST: Simple, standard, easier to cache
- GraphQL: Complex data fetching, mobile apps (reduce requests)

---

### 7. **Scalability** - *Handling Growth*

**Vertical Scaling (Scale UP)**:
- Add more RAM/CPU to existing server
- **Pros**: Simple, no code changes, no new servers
- **Cons**: Expensive, hardware limit (can't add infinite RAM)
- **Example**: Upgrade server from 8GB → 64GB RAM

**When to use**: Early stage, quick fix, database servers

---

**Horizontal Scaling (Scale OUT)**:
- Add more servers to handle load
- **Pros**: Unlimited growth, redundancy, cheaper (use commodity hardware)
- **Cons**: Complex (need load balancer, distributed systems)
- **Example**: 1 server → 10 servers

**When to use**: High traffic apps, need high availability

---

**Database Scaling**:

**1. Read Replicas** (for read-heavy apps):
```
        [Master DB] ← All WRITES go here
             |
    ┌────────┼────────┐
    ▼        ▼        ▼
[Replica 1] [Replica 2] [Replica 3] ← READ from these
```
- Master handles writes, replicates to slaves
- Reads distributed across replicas
- **Use case**: Social media (90% reads, 10% writes)

**2. Sharding** (split data across databases):
```
Users A-M → Database 1
Users N-Z → Database 2
```
- **Horizontal partitioning**: Split rows
- **Vertical partitioning**: Split columns
- **Challenge**: Joins across shards are hard

**3. Denormalization** (duplicate data for speed):
- Instead of joining 3 tables, store everything in 1 table
- Trade-off: Faster reads, harder updates, more storage

---

### 8. **Message Queue** - *Async Communication*

**Problem**: Sending email takes 5 seconds. Should user wait?

**Solution**: Put task in queue, return immediately, process later

```
User uploads video → Add to queue → Return "Processing..." → 
Background worker processes → Notify user when done
```

**Tools**: RabbitMQ, Apache Kafka, AWS SQS, Redis Queue

**Use Cases**:
- Email sending
- Video processing
- Image thumbnails
- Batch jobs

**Benefits**:
- **Decoupling**: API server doesn't do heavy work
- **Reliability**: If processing fails, retry from queue
- **Scalability**: Add more workers to process faster

---

### 9. **Microservices vs Monolith**

**Monolith** (One big application):
```
[Single App: Users + Posts + Payments + Notifications]
```
- **Pros**: Simple, easy to develop, single deployment
- **Cons**: Hard to scale, one bug crashes everything

**Microservices** (Split into small services):
```
[User Service] [Post Service] [Payment Service] [Notification Service]
```
- **Pros**: Scale independently, different tech stacks, team autonomy
- **Cons**: Complex networking, harder debugging, need API gateway

**When to use**:
- Start with Monolith (simpler)
- Move to Microservices when: 10+ developers, need independent scaling, different tech needed

---

## Simple Examples to Practice

### Example 1: URL Shortener (like bit.ly)

**Problem**: Convert long URLs to short ones

**Simple Design**:
1. User enters long URL → Server generates short code (abc123)
2. Store mapping in database: `abc123 → https://verylongurl.com`
3. When someone visits `bit.ly/abc123`, look up database, redirect

**Key Points**:
- Use hashing to generate unique codes
- Database needs to be fast (use cache)
- Handle millions of URLs (need multiple servers)

---

### Example 2: Design a Parking Lot System

**Problem**: Track which parking spots are available

**Simple Design**:
1. Database stores: spot number, is_occupied, vehicle_type
2. When car enters: find available spot, mark as occupied
3. When car leaves: mark spot as available, calculate payment

**Key Points**:
- Different spot sizes (compact, large, handicap)
- Real-time availability
- Handle concurrent requests (two people trying same spot)

---

### Example 3: Design Instagram Photo Feed

**Problem**: Show photos from people you follow

**Simple Design**:
1. **Upload**: User uploads photo → Store in file storage (S3)
2. **Feed**: Get list of people you follow → Fetch their recent photos from database
3. **Display**: Show photos from newest to oldest

**Key Points**:
- Photos stored in CDN for fast loading
- Cache popular photos
- Database stores metadata (who posted, when, likes)
- Use pagination (load 10 photos at a time, not all)

---

## Tips for Freshers

1. **Draw everything** - Interviewers love visual diagrams
2. **Think out loud** - Share your reasoning
3. **Start simple** - Basic design first, then add complexity
4. **Ask questions** - Shows you care about requirements
5. **Admit what you don't know** - "I'm not familiar with X, but I would approach it by..."

## Resources to Learn

- **YouTube**: "System Design Primer" by Gaurav Sen (easy Hindi/English mix)
- **Website**: GitHub "System Design Primer" (comprehensive guide)
- **Practice**: Design everyday apps (WhatsApp, Uber, YouTube)

---

## Additional Practice Problems for Beginners

### Easy Level:
1. Design a simple calculator app
2. Design a basic chat application
3. Design a file storage system (like Google Drive)
4. Design a basic e-commerce shopping cart
5. Design a simple notification system

### Medium Level:
1. Design Twitter
2. Design Uber
3. Design a rate limiter
4. Design a web crawler
5. Design YouTube

---

## Common Interview Questions

### Q: What's the difference between horizontal and vertical scaling?
**A**: Vertical scaling = making one machine more powerful (add RAM/CPU). Horizontal scaling = adding more machines. Horizontal is better for large scale but more complex.

### Q: When would you use SQL vs NoSQL?
**A**: Use SQL when you need strict data relationships and transactions (banking, inventory). Use NoSQL when you need flexibility and speed (social media, real-time analytics).

### Q: What is a single point of failure?
**A**: A component that, if it fails, brings down the entire system. Example: having only one database server. Solution: add redundancy (multiple servers).

### Q: How do you handle millions of users?
**A**: 
1. Use load balancers to distribute traffic
2. Add caching to reduce database load
3. Use CDN for static content
4. Scale horizontally (add more servers)
5. Use database sharding (split data across multiple databases)

---

## Key Metrics to Consider

1. **Latency**: How fast does the system respond? (milliseconds)
2. **Throughput**: How many requests can it handle per second?
3. **Availability**: What percentage of time is it working? (99.9% = "three nines")
4. **Consistency**: Do all users see the same data at the same time?
5. **Scalability**: Can it handle 10x or 100x more users?

---

## Common Design Patterns

### 1. **Master-Slave Pattern**
- One main database (master) handles writes
- Multiple read databases (slaves) handle reads
- Used when you have more reads than writes

### 2. **Microservices**
- Break application into small, independent services
- Each service does one thing well
- Example: User service, Payment service, Notification service

### 3. **Event-Driven Architecture**
- Components communicate through events/messages
- Example: When user places order → triggers payment event → triggers shipping event
- Tool: Kafka, RabbitMQ

---

## Glossary of Important Terms

- **API Gateway**: Single entry point for all client requests
- **Sharding**: Splitting database into smaller pieces
- **Replication**: Copying data to multiple servers
- **Idempotency**: Running same operation multiple times gives same result
- **Eventual Consistency**: Data becomes consistent over time, not immediately
- **CAP Theorem**: You can only have 2 of: Consistency, Availability, Partition Tolerance

---

Remember: As a fresher, you're not expected to know everything. Show curiosity, logical thinking, and willingness to learn. Good luck! 🚀

---

**Created on**: February 6, 2026  
**Purpose**: Study guide for system design interview preparation
