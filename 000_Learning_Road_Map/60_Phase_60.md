# Phase 60 – Advanced System Design, Scalability, High Availability and Distributed Systems

**Level:** Advanced to Expert  
**Domain:** System Architecture, Distributed Systems, Backend Engineering, Cloud Computing and Performance Engineering  
**Primary Technologies:** Node.js, Express.js, Next.js, React.js, MySQL, MongoDB, Redis, RabbitMQ, Apache Kafka, Docker, Kubernetes and AWS.

---

# 1. Learning Objectives

In this phase, you will learn how to design applications that can handle thousands or millions of users while maintaining performance, reliability, availability and data consistency.

You will study how large-scale applications such as WhatsApp, YouTube, Netflix, Amazon and Instagram are architected.

By completing this phase, you should understand:

1. System Design Fundamentals.
2. Low-Level Design (LLD).
3. High-Level Design (HLD).
4. Functional and Non-Functional Requirements.
5. Scalability.
6. Vertical Scaling.
7. Horizontal Scaling.
8. Load Balancing.
9. Reverse Proxy.
10. Caching Strategies.
11. Redis Architecture.
12. CDN Architecture.
13. Database Replication.
14. Database Sharding.
15. Database Partitioning.
16. SQL vs NoSQL Architecture.
17. Database Indexing Strategies.
18. CAP Theorem.
19. ACID Properties.
20. BASE Architecture.
21. Distributed Systems Fundamentals.
22. Monolithic Architecture.
23. Microservices Architecture.
24. API Gateway.
25. Service Discovery.
26. Message Queues.
27. Event-Driven Architecture.
28. Kafka Fundamentals.
29. RabbitMQ Fundamentals.
30. Eventual Consistency.
31. Distributed Transactions.
32. Saga Pattern.
33. Circuit Breaker Pattern.
34. Retry and Timeout Patterns.
35. Idempotency.
36. Rate Limiting Architecture.
37. High Availability.
38. Fault Tolerance.
39. Disaster Recovery.
40. Observability.
41. System Design Case Studies.
42. URL Shortener Architecture.
43. WhatsApp-Like Chat Application Architecture.
44. E-Commerce System Architecture.
45. Video Streaming Architecture.

---

# PART 1: SYSTEM DESIGN FUNDAMENTALS

## 2. What Is System Design?

System Design is the process of defining the architecture, components, databases, APIs, communication mechanisms and infrastructure required to build a software system.

It explains how different components work together to satisfy functional and non-functional requirements.

### Example

Suppose you want to develop an Expense Tracker application.

Initially, you might have:

```text
Frontend
   |
   v
Backend
   |
   v
Database
```

This architecture may be sufficient for a small application.

But imagine the application grows to:

- 1 million registered users.
- 100,000 daily active users.
- 10,000 simultaneous users.
- Millions of expense records.
- Thousands of API requests per second.

Your original architecture might not handle this workload efficiently.

You may need:

- Multiple backend instances.
- Load balancing.
- Redis caching.
- Database replication.
- Database partitioning.
- Background workers.
- Message queues.
- Monitoring.
- Automatic scaling.

Designing these components and deciding how they interact is System Design.

## 3. Why Is System Design Important?

System Design helps developers answer important questions:

1. How will the application handle increasing traffic?
2. What happens if a server crashes?
3. How will multiple servers share data?
4. How will database performance be maintained?
5. How will background tasks be processed?
6. How will services communicate?
7. How will data remain consistent?
8. How will the application recover from failures?
9. How will infrastructure costs be controlled?

### Applications That Require System Design

| Application | Major Design Challenges |
|---|---|
| WhatsApp | Real-time messaging, message delivery |
| YouTube | Video storage, encoding and streaming |
| Netflix | Content delivery and global availability |
| Amazon | Inventory, payments and order processing |
| Instagram | Media storage, feeds and caching |
| Uber | Location tracking and real-time matching |
| Google Drive | File storage, synchronization and sharing |

---

## 4. High-Level Design (HLD)

HLD describes the overall architecture of a software system.

It focuses on:

- Major components.
- Services.
- Databases.
- Communication.
- Infrastructure.
- Data flow.
- External integrations.

### Example: Expense Tracker HLD

```text
                Users
                  |
                  v
             Next.js App
                  |
                  v
              Load Balancer
                  |
          +-------+-------+
          |       |       |
          v       v       v
        API 1   API 2   API 3
          |       |       |
          +-------+-------+
                  |
          +-------+-------+
          |       |       |
          v       v       v
        Redis   MySQL   Queue
                          |
                          v
                    Background Worker
```

HLD explains the overall system without going deeply into individual classes or methods.

## 5. Low-Level Design (LLD)

LLD describes the internal implementation of individual components.

It focuses on:

- Classes.
- Functions.
- Methods.
- Interfaces.
- Database schemas.
- Design patterns.
- Object relationships.
- Module structure.

### Example: Expense Tracker LLD

```text
Expense
  |
  +-- id
  +-- userId
  +-- amount
  +-- category
  +-- description
  +-- createdAt

ExpenseService
  |
  +-- createExpense()
  +-- updateExpense()
  +-- deleteExpense()
  +-- getExpense()

ExpenseRepository
  |
  +-- findById()
  +-- findByUserId()
  +-- save()
  +-- delete()
```

### HLD vs LLD

| HLD | LLD |
|---|---|
| Overall architecture | Internal implementation |
| System-level view | Component-level view |
| Services and databases | Classes and methods |
| Communication between services | Communication between objects |
| Focuses on architecture | Focuses on implementation |
| Used for architectural planning | Used for detailed development |

---

## 6. Functional Requirements

Functional Requirements describe what a system should do.

Example: Expense Tracker.

- User registration.
- User login.
- Create expenses.
- Update expenses.
- Delete expenses.
- Generate reports.
- Export Excel.
- Upload receipts.

These are application features.

## 7. Non-Functional Requirements (NFRs)

Non-Functional Requirements describe how well the application should operate.

Examples:

- Performance.
- Scalability.
- Availability.
- Reliability.
- Security.
- Maintainability.
- Fault tolerance.
- Data consistency.

### Example

Suppose your application has the following requirements:

| Requirement | Target |
|---|---|
| API Response Time | 200 ms for typical requests |
| Availability | 99.9% |
| Concurrent Users | 10,000 |
| Database Backup | Daily |
| Recovery Time | 30 minutes |
| Security | HTTPS and authenticated APIs |

These are example targets, not universal standards. Actual targets should come from business requirements and workload measurements.

---

# PART 2: SCALABILITY

## 8. What Is Scalability?

Scalability is the ability of a system to handle increasing workloads by adding or allocating resources.

Workload growth may include:

- More users.
- More requests.
- More database records.
- More file uploads.
- More background jobs.
- More simultaneous connections.

### Example

Initially:

```text
1,000 Users
     |
     v
1 Backend Server
     |
     v
1 Database
```

Later:

```text
100,000 Users
      |
      v
Load Balancer
      |
  +---+---+
  |   |   |
  v   v   v
 API API API
  |   |   |
  +---+---+
      |
      v
  Database
```

The system has expanded to handle more traffic.

## 9. Types of Scalability

There are two primary scaling approaches:

1. Vertical Scaling.
2. Horizontal Scaling.

## 10. Vertical Scaling

Vertical Scaling means increasing the resources of an existing server.

For example:

Before:

```text
CPU: 2 Cores
RAM: 4 GB
Storage: 100 GB
```

After:

```text
CPU: 16 Cores
RAM: 64 GB
Storage: 1 TB
```

The same server becomes more powerful.

### Advantages

- Simple to implement.
- Minimal architectural changes.
- Often easier to manage.
- Useful for applications with limited scaling needs.

### Disadvantages

- Hardware has practical limits.
- Can become expensive.
- Creates a larger single point of failure.
- Scaling may require downtime or migration.

## 11. Horizontal Scaling

Horizontal Scaling means adding more servers or application instances.

Before:

```text
          Users
            |
            v
        Backend 1
```

After:

```text
             Users
               |
               v
          Load Balancer
               |
       +-------+-------+
       |       |       |
       v       v       v
    Server 1 Server 2 Server 3
```

### Advantages

- Can support large workloads.
- Improves fault isolation.
- Allows distributed traffic processing.
- Supports automatic scaling.
- Can improve availability when designed correctly.

### Disadvantages

- More complex architecture.
- Requires load balancing.
- Requires careful state management.
- Introduces distributed communication problems.
- Requires monitoring and orchestration.

### Vertical vs Horizontal Scaling

| Feature | Vertical | Horizontal |
|---|---|---|
| Approach | Upgrade existing server | Add more servers |
| Complexity | Lower initially | Higher |
| Hardware Limit | Yes | Less constrained by one machine |
| Fault Tolerance | Limited by single-server design | Can improve through redundancy |
| Cost | Can become expensive | Depends on workload and architecture |
| Example | Upgrade EC2 instance | Add more EC2 instances |

**Important:** Horizontal scaling works best when application instances are stateless or when their state is managed through shared infrastructure.

---

# PART 3: LOAD BALANCING

## 12. What Is Load Balancing?

Load Balancing distributes incoming requests across multiple servers.

It prevents one server from handling all incoming traffic.

### Without Load Balancing

```text
             Users
               |
               v
           Server 1
               |
               v
            Database
```

If Server 1 receives too many requests, it may become overloaded.

### With Load Balancing

```text
                Users
                  |
                  v
             Load Balancer
                  |
         +--------+--------+
         |        |        |
         v        v        v
      Server 1 Server 2 Server 3
         |        |        |
         +--------+--------+
                  |
                  v
               Database
```

## 13. Types of Load Balancers

### 13.1 Layer 4 Load Balancer

Works primarily at the transport layer.

It distributes traffic using information such as:

- Source IP.
- Destination IP.
- TCP port.
- Connection information.

### 13.2 Layer 7 Load Balancer

Works at the application layer.

It can route requests based on:

- HTTP paths.
- Hostnames.
- Headers.
- Cookies.
- Other HTTP request attributes.

### Comparison

| Layer 4 | Layer 7 |
|---|---|
| Transport layer | Application layer |
| TCP/UDP information | HTTP/HTTPS information |
| Lower-level routing | Application-aware routing |
| Generally less request inspection | More detailed request inspection |
| Example: TCP load balancing | Example: `/api/users` routing |

## 14. Load Balancing Algorithms

### 14.1 Round Robin

Requests are distributed sequentially.

```text
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
Request 5 → Server 2
```

### 14.2 Weighted Round Robin

Servers receive traffic according to assigned weights.

Example:

| Server | Weight |
|---|---:|
| Server 1 | 5 |
| Server 2 | 3 |
| Server 3 | 2 |

Server 1 receives a larger share of traffic.

### 14.3 Least Connections

New requests are routed to the server with the fewest active connections.

### 14.4 IP Hash

Traffic is distributed based on a hash of the client IP address.

This can help keep a client routed consistently to the same server, but it is not a substitute for properly shared session state.

## 15. Health Checks

Load balancers can periodically check whether backend servers are healthy.

Example:

```http
GET /health
```

Expected response:

```json
{
  "status": "healthy"
}
```

If a server becomes unhealthy, the load balancer can stop sending new traffic to it.

A health check should distinguish between:

- **Liveness:** Is the process running?
- **Readiness:** Can the instance safely accept traffic?

A process can be alive but not ready because its database connection or required dependency is unavailable.

---

# PART 4: REVERSE PROXY

## 16. What Is a Reverse Proxy?

A Reverse Proxy is a server that receives client requests and forwards them to one or more backend servers.

Clients communicate with the reverse proxy rather than directly accessing the backend application.

### Architecture

```text
             Client
                |
                v
           Reverse Proxy
                |
       +--------+--------+
       |        |        |
       v        v        v
    Backend  Backend  Backend
       1        2        3
```

### Common Reverse Proxy Responsibilities

- Request routing.
- TLS termination.
- Load balancing.
- Compression.
- Caching.
- Request filtering.
- Access logging.

### Common Tools

- Nginx.
- HAProxy.
- Envoy.
- Traefik.

### Reverse Proxy vs Forward Proxy

| Reverse Proxy | Forward Proxy |
|---|---|
| Sits in front of servers | Sits in front of clients |
| Protects or routes backend services | Represents clients accessing external services |
| Receives requests for a service | Sends requests on behalf of clients |
| Example: Nginx | Example: Corporate outbound proxy |

---

# PART 5: CACHING STRATEGIES

## 17. What Is Caching?

Caching stores frequently accessed data in a faster storage layer so that future requests can retrieve it more quickly.

Instead of repeatedly querying the database, the application can retrieve cached data.

### Without Caching

```text
User
  |
  v
Backend
  |
  v
Database
  |
  v
Response
```

Every request queries the database.

### With Caching

```text
User
  |
  v
Backend
  |
  v
Cache
  |
  +------ Cache Hit ------> Response
  |
  +------ Cache Miss
             |
             v
          Database
             |
             v
          Update Cache
             |
             v
          Response
```

## 18. Why Is Caching Important?

Caching can:

- Reduce database load.
- Reduce response latency.
- Improve throughput.
- Reduce repeated computation.
- Reduce infrastructure costs.

### Example

Suppose your application has 10,000 users.

Every user requests the same dashboard data repeatedly.

Without caching, your database may receive thousands of similar queries.

With caching, frequently requested data can be reused.

## 19. Types of Caching

### 19.1 Browser Cache

Stores resources in the browser.

Examples:

- Images.
- CSS files.
- JavaScript files.
- Fonts.

### 19.2 CDN Cache

Stores content on geographically distributed edge servers.

### 19.3 Application Cache

Stores application data in memory or a shared caching service.

### 19.4 Database Cache

Stores frequently accessed database data.

### 19.5 Distributed Cache

A cache shared by multiple application instances.

Example: Redis.

## 20. Cache-Aside Pattern

Cache-Aside is one of the most common caching strategies.

The application controls cache reads and writes.

### Workflow

```text
Request
   |
   v
Check Cache
   |
   +------ Hit ------> Return Data
   |
   +------ Miss
          |
          v
       Database
          |
          v
      Store in Cache
          |
          v
       Return Data
```

### Node.js Example

Install:

```bash
npm install redis
```

Example:

```javascript
const { createClient } = require("redis");

const redis = createClient({
  url: process.env.REDIS_URL
});

redis.on("error", (error) => {
  console.error("Redis error:", error);
});

await redis.connect();
```

Cache-aside implementation:

```javascript
async function getUser(userId) {
  const cacheKey = `user:${userId}`;

  const cachedUser = await redis.get(cacheKey);

  if (cachedUser) {
    return JSON.parse(cachedUser);
  }

  const user = await User.findByPk(userId);

  if (!user) {
    return null;
  }

  await redis.set(
    cacheKey,
    JSON.stringify(user),
    {
      EX: 300
    }
  );

  return user;
}
```

The `EX: 300` option sets a five-minute expiration.

### Cache Invalidation

When data changes, the cache may become stale.

Example:

```javascript
async function updateUser(userId, data) {
  await User.update(data, {
    where: {
      id: userId
    }
  });

  await redis.del(`user:${userId}`);
}
```

The next read will retrieve fresh data from the database.

## 21. Other Caching Strategies

| Strategy | Description |
|---|---|
| Cache-Aside | Application checks cache, then database |
| Read-Through | Cache loads data from the source on a miss |
| Write-Through | Writes update cache and underlying storage |
| Write-Behind | Writes are buffered and persisted asynchronously |
| Write-Around | Writes bypass cache and go directly to storage |

### Cache Problems

**1. Cache Invalidation**

Old data remains in cache after the original data changes.

**2. Cache Stampede**

Many requests simultaneously miss the same expired key and overload the database.

**3. Cache Penetration**

Repeated requests for nonexistent records reach the database.

**4. Cache Avalanche**

Many cache entries expire together, causing a sudden increase in database traffic.

### Prevention

- Use appropriate TTL values.
- Add random TTL variation.
- Cache negative lookups selectively.
- Use request coalescing or locks for hot keys.
- Apply rate limits.
- Monitor cache hit ratios.
- Use graceful fallback behavior.

Redis documents cache-aside, TTL-based expiration, invalidation and cache-stampede mitigation as important caching design considerations. [Redis](https://redis.io/docs/latest/develop/use-cases/cache-aside/?utm_source=chatgpt.com)

---

# PART 6: REDIS ARCHITECTURE

## 22. What Is Redis?

Redis is an in-memory data store commonly used for:

- Caching.
- Session storage.
- Rate limiting.
- Queues.
- Real-time counters.
- Leaderboards.
- Pub/Sub messaging.
- Temporary data.

Redis supports data structures such as:

- Strings.
- Hashes.
- Lists.
- Sets.
- Sorted Sets.
- Streams.

## 23. Why Is Redis Fast?

Redis primarily serves data from memory.

This avoids many disk-access costs associated with traditional database reads.

However, performance also depends on:

- Network latency.
- Data structures.
- Command complexity.
- Memory pressure.
- Persistence configuration.
- Deployment architecture.

## 24. Redis Use Cases

| Use Case | Example |
|---|---|
| Caching | Store frequently accessed user profiles |
| Session Storage | Store session information |
| Rate Limiting | Track API request counts |
| Leaderboards | Game rankings using sorted sets |
| Real-Time Counters | Website visit counts |
| Pub/Sub | Send real-time notifications |
| Streams | Process ordered event records |

### Redis vs MySQL

| Redis | MySQL |
|---|---|
| Primarily in-memory | Primarily persistent relational storage |
| Supports data structures | Relational tables |
| Excellent for low-latency caching | Excellent for transactional relational data |
| Commonly used as a cache | Commonly used as primary database |
| Persistence is configurable | Durable relational database |

**Important:** Redis does not automatically replace your primary database. Choose it according to the data model, durability and consistency requirements.

---

# PART 7: CDN ARCHITECTURE

## 25. What Is a CDN?

CDN stands for Content Delivery Network.

A CDN is a geographically distributed network of servers that delivers content from locations closer to users.

Examples:

- Cloudflare.
- Amazon CloudFront.
- Akamai.
- Fastly.

### Without CDN

Suppose your server is located in Mumbai.

A user in London requests an image.

```text
London User
     |
     v
Mumbai Server
     |
     v
Return Image
```

The request travels a long distance.

### With CDN

```text
London User
     |
     v
London CDN Edge
     |
     v
Cached Image
```

If the content is cached at the edge, the CDN can serve it without contacting the origin server.

### CDN Benefits

- Reduced latency.
- Reduced origin-server traffic.
- Faster asset delivery.
- Better global content distribution.
- DDoS protection features, depending on provider and configuration.

### What Can Be Cached?

- Images.
- CSS.
- JavaScript.
- Fonts.
- Videos.
- Public static files.
- Carefully selected API responses.

### Important

Private user data should not be cached publicly.

For personalized API responses, configure cache-control policies carefully to prevent one user's data from being served to another.

---

# PART 8: DATABASE SCALABILITY

## 26. Database Replication

Database Replication means maintaining copies of database data across multiple database servers.

It can improve read scalability and availability.

### Primary-Replica Architecture

```text
              Application
                   |
                   v
               Primary DB
                   |
             Replication
                   |
          +--------+--------+
          |                 |
          v                 v
       Replica 1         Replica 2
```

### Primary Database

Commonly handles:

- Writes.
- Transaction coordination.
- Authoritative data changes.

### Replica Database

Commonly handles:

- Read queries.
- Reporting.
- Analytics.
- Read-heavy workloads.

### Benefits

- Improved read scalability.
- Redundancy.
- Better reporting workload isolation.
- Potential failover options.

### Challenges

- Replication lag.
- Failover complexity.
- Stale reads.
- Conflict handling in some architectures.

### Example

Your application receives:

```text
10,000 Read Requests
1,000 Write Requests
```

You might route:

- Writes to the primary.
- Suitable reads to replicas.

However, read-after-write operations may need to use the primary or another consistency mechanism.

## 27. Database Sharding

Sharding divides a large database into multiple smaller databases called shards.

Each shard stores a subset of the total data.

### Example

Suppose you have 100 million users.

You divide the data across four database shards.

```text
                Application
                     |
                     v
                Shard Router
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Shard 1    Shard 2    Shard 3
                              |
                              v
                           Shard 4
```

A simple range strategy could be:

| Shard | User IDs |
|---|---|
| Shard 1 | 1–25 million |
| Shard 2 | 25–50 million |
| Shard 3 | 50–75 million |
| Shard 4 | 75–100 million |

### Sharding Strategies

**1. Range-Based Sharding**

Records are assigned based on ranges.

**2. Hash-Based Sharding**

A hash function determines the shard.

Example:

```javascript
const shardNumber = hash(userId) % 4;
```

**3. Geographic Sharding**

Records are assigned based on geographical location.

**4. Directory-Based Sharding**

A lookup service determines where each record is stored.

### Advantages

- Distributes storage.
- Distributes database workload.
- Supports large datasets.
- Can improve throughput.

### Disadvantages

- More complex architecture.
- Cross-shard queries are difficult.
- Transactions become more complex.
- Rebalancing can be challenging.
- Choosing a poor shard key can create hotspots.

## 28. Database Partitioning

Partitioning divides a large table into smaller logical sections.

Example:

An expenses table contains records from 2020 to 2026.

You can partition records by year.

```text
Expenses Table
      |
      +-- 2023 Partition
      |
      +-- 2024 Partition
      |
      +-- 2025 Partition
      |
      +-- 2026 Partition
```

Queries that filter on the partition key may access fewer partitions.

### Partitioning vs Sharding

| Partitioning | Sharding |
|---|---|
| Divides data into partitions | Distributes data across shards |
| May exist within one database system | Commonly involves multiple database instances |
| Helps manage large tables | Helps distribute database workload |
| Can be transparent to application code | Often requires shard routing |

The exact meaning of partitioning depends on the database technology.

---

# PART 9: DATABASE INDEXING

## 29. What Is a Database Index?

A Database Index is a data structure that helps the database locate records more efficiently.

Without an index, a database may need to scan many rows.

### Without Index

Suppose you execute:

```sql
SELECT *
FROM users
WHERE email = 'user@example.com';
```

If there is no suitable index, the database may perform a full table scan.

### With Index

```sql
CREATE INDEX idx_users_email
ON users(email);
```

The database can use the index to locate matching records more efficiently.

### Common Index Types

- B-Tree indexes.
- Hash indexes.
- Composite indexes.
- Unique indexes.
- Full-text indexes.
- Spatial indexes.

### Composite Index

Example:

```sql
CREATE INDEX idx_expenses_user_date
ON expenses(user_id, created_at);
```

This index may help queries that filter by `user_id` and use `created_at`.

The order of columns matters.

### Indexing Trade-Offs

| Benefits | Costs |
|---|---|
| Faster suitable reads | More storage |
| Better query performance | Slower writes in some cases |
| Faster sorting for supported query patterns | Index maintenance |
| Better lookup efficiency | Additional operational complexity |

**Important:** Adding indexes to every column is not good database design. Create indexes based on actual query patterns and inspect query plans.

---

# PART 10: SQL VS NOSQL ARCHITECTURE

## 30. SQL Databases

SQL databases are relational database systems.

Examples:

- MySQL.
- PostgreSQL.
- Microsoft SQL Server.
- Oracle Database.

Characteristics:

- Tables.
- Rows.
- Columns.
- Relationships.
- Constraints.
- SQL queries.
- Transaction support.

## 31. NoSQL Databases

NoSQL databases use data models other than the traditional relational table model.

Examples:

- MongoDB.
- Redis.
- Cassandra.
- DynamoDB.

Common categories:

| Category | Example |
|---|---|
| Document Database | MongoDB |
| Key-Value Database | Redis |
| Wide-Column Database | Cassandra |
| Graph Database | Neo4j |

## 32. SQL vs NoSQL

| Feature | SQL | NoSQL |
|---|---|---|
| Data Model | Relational | Multiple models |
| Schema | Generally structured | Depends on database |
| Relationships | Strong relational support | Varies by technology |
| Query Language | SQL | Database-specific APIs/query languages |
| Transactions | Widely supported | Depends on database |
| Scaling | Vertical and horizontal options | Often designed for distributed scaling |
| Example | MySQL | MongoDB |

### When to Use MySQL?

Use MySQL when:

- Data has clear relationships.
- Referential integrity matters.
- Relational queries are important.
- Transactions are central.
- The schema is well-defined.

### When to Use MongoDB?

Consider MongoDB when:

- Data naturally fits documents.
- Flexible document structures are useful.
- The application frequently retrieves related information as a document.
- The workload benefits from its document model.

**Important:** SQL is not automatically slower or less scalable than NoSQL. Database selection depends on workload, consistency requirements, data model and operational constraints.

---

# PART 11: DISTRIBUTED SYSTEMS

## 33. What Is a Distributed System?

A Distributed System consists of multiple independent computers or services that communicate over a network and work together to provide a larger system.

Example:

```text
             Client
                |
                v
            API Gateway
                |
       +--------+--------+
       |        |        |
       v        v        v
     User    Order    Payment
    Service  Service  Service
       |        |        |
       v        v        v
    User DB  Order DB Payment DB
```

Each service may run independently on different servers.

### Examples

- Banking systems.
- E-commerce systems.
- Messaging applications.
- Cloud platforms.
- Distributed databases.
- Search engines.

## 34. Challenges in Distributed Systems

Distributed systems introduce problems that are less prominent in a single-process application.

Examples:

1. Network failures.
2. Network latency.
3. Partial failures.
4. Data inconsistency.
5. Duplicate messages.
6. Distributed transactions.
7. Service discovery.
8. Clock differences.
9. Retry storms.
10. Fault isolation.

### What Is a Partial Failure?

A Partial Failure occurs when one component fails while the rest of the system continues operating.

Example:

```text
API Gateway       Working
User Service     Working
Order Service    Working
Payment Service  Failed
```

The system is partially operational.

A well-designed application should handle this situation without unnecessarily bringing down unrelated functionality.

---

# PART 12: CAP THEOREM

## 35. What Is CAP Theorem?

CAP Theorem is a principle in distributed database systems.

It describes a trade-off involving three properties:

1. Consistency.
2. Availability.
3. Partition Tolerance.

### Consistency

Every successful read receives the latest successfully committed value, according to the system's consistency model.

### Availability

Every request to a non-failing node receives a response, although that response may not contain the latest data.

### Partition Tolerance

The system continues operating despite network communication failures between parts of the distributed system.

## 36. Understanding CAP

Suppose your distributed database has two nodes:

```text
Node A -------- Node B
```

A network partition occurs.

```text
Node A     X     Node B
          Network
          Failure
```

The nodes cannot communicate.

The system must make trade-offs.

### CP-Oriented Behavior

The system prioritizes consistency.

It may reject or delay some requests to avoid returning conflicting or stale data.

### AP-Oriented Behavior

The system prioritizes availability.

It may continue responding with potentially stale data and reconcile differences later.

### Important Clarification

CAP does not mean that a distributed system simply chooses two permanent properties from three.

The key trade-off arises when a network partition occurs. During that partition, the system cannot guarantee both strong consistency and availability for every request.

### CAP Summary

| Property | Meaning |
|---|---|
| Consistency | Reads satisfy the chosen consistency guarantee |
| Availability | Requests receive responses |
| Partition Tolerance | Operation continues despite network partition |

---

# PART 13: ACID AND BASE

## 37. What Is ACID?

ACID describes important properties of database transactions.

ACID stands for:

- Atomicity.
- Consistency.
- Isolation.
- Durability.

### Atomicity

A transaction either completes fully or does not complete.

Example:

A bank transfer must debit one account and credit another.

Both operations should succeed together or be rolled back.

### Consistency

A transaction preserves database constraints and invariants.

Example:

An account balance should not violate the application's defined rules.

### Isolation

Concurrent transactions should not interfere in ways that violate the selected isolation level.

### Durability

Once a transaction is committed, the database should preserve it according to its durability guarantees.

## 38. ACID Example

Suppose Account A has ₹10,000 and Account B has ₹5,000.

Transfer ₹2,000 from A to B.

Before:

```text
Account A = ₹10,000
Account B = ₹5,000

Total = ₹15,000
```

After:

```text
Account A = ₹8,000
Account B = ₹7,000

Total = ₹15,000
```

If the credit operation fails, the debit should not remain committed as a completed transfer.

## 39. What Is BASE?

BASE is an architectural approach often associated with highly distributed systems.

It stands for:

- Basically Available.
- Soft State.
- Eventually Consistent.

### Basically Available

The system aims to remain responsive, potentially with reduced functionality or stale data.

### Soft State

Data may change over time because of asynchronous updates or background reconciliation.

### Eventually Consistent

If no new updates occur and replication or reconciliation completes, replicas are expected to converge.

### ACID vs BASE

| ACID | BASE |
|---|---|
| Transaction-focused guarantees | Distributed availability-oriented approach |
| Strong transaction semantics | Often eventual consistency |
| Common in relational systems | Common in distributed architectures |
| Useful for financial transactions | Useful for some distributed feeds and catalogs |

BASE does not mean data correctness is unimportant. It means the architecture may accept temporary inconsistency while converging toward a consistent state.

---

# PART 14: MONOLITHIC VS MICROSERVICES ARCHITECTURE

## 40. What Is Monolithic Architecture?

A Monolithic Application is built and deployed as one main application unit.

Example:

```text
           Application
                |
     +----------+----------+
     |          |          |
     v          v          v
   Users      Orders    Payments
     |          |          |
     +----------+----------+
                |
                v
             Database
```

All modules are part of one deployable application.

### Advantages

- Easier initial development.
- Simpler deployment.
- Easier local debugging.
- Straightforward in-process communication.
- Often lower operational overhead.

### Disadvantages

- Components may become tightly coupled.
- Large deployments become harder to manage.
- Scaling individual features independently is difficult.
- Changes in one area can affect others.

## 41. What Is Microservices Architecture?

Microservices Architecture divides an application into smaller independently deployable services.

Each service focuses on a specific business capability.

Example:

```text
                  API Gateway
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
  User Service    Order Service   Payment Service
       |               |               |
       v               v               v
    User DB         Order DB       Payment DB
```

### Advantages

- Independent deployment.
- Independent scaling.
- Better service ownership boundaries.
- Technology flexibility.
- Fault isolation when properly designed.

### Disadvantages

- Distributed system complexity.
- Network communication overhead.
- Harder debugging.
- More complicated transactions.
- Greater infrastructure and monitoring requirements.
- Service versioning and compatibility challenges.

## 42. Monolith vs Microservices

| Feature | Monolith | Microservices |
|---|---|---|
| Deployment | One main application unit | Multiple services |
| Communication | Often in-process | Network-based |
| Scaling | Usually application-level | Per-service |
| Complexity | Lower initially | Higher |
| Data | Often shared database | Often service-owned data |
| Debugging | Generally simpler | Requires distributed tracing |
| Suitable For | Small and medium applications | Systems with clear independent domains and operational maturity |

**Important:** Microservices are not automatically better.

A modular monolith is often an excellent architecture for a growing application. Split services when independent deployment, scaling, ownership or fault isolation provides measurable value.

---

# PART 15: API GATEWAY

## 43. What Is an API Gateway?

An API Gateway is an entry point that receives client API requests and routes them to appropriate backend services.

### Architecture

```text
             Frontend
                |
                v
            API Gateway
                |
      +---------+---------+
      |         |         |
      v         v         v
    User      Order    Payment
   Service   Service   Service
```

### Responsibilities

- Request routing.
- Authentication integration.
- Rate limiting.
- Request transformation.
- API version routing.
- Logging.
- Monitoring.
- TLS termination, depending on architecture.

### Examples

- Amazon API Gateway.
- Kong.
- Nginx.
- Apigee.
- Envoy Gateway.

### API Gateway vs Reverse Proxy

| API Gateway | Reverse Proxy |
|---|---|
| API-focused gateway functionality | General reverse request forwarding |
| Often includes API policies and authentication integration | Often focuses on routing, TLS and load balancing |
| May aggregate backend services | Usually forwards traffic |
| Common in microservices | Common in many web architectures |

An API Gateway is often implemented using reverse-proxy technology.

---

# PART 16: SERVICE DISCOVERY

## 44. What Is Service Discovery?

Service Discovery allows services to find the network locations of other services.

In a distributed system, service instances may:

- Start.
- Stop.
- Restart.
- Scale up.
- Scale down.
- Move to different machines.

Their addresses may change.

Service discovery helps clients locate available instances.

### Without Service Discovery

```javascript
const paymentServiceUrl =
  "http://192.168.1.20:5000";
```

Hardcoding addresses becomes difficult when services move or scale.

### With Service Discovery

```text
Order Service
     |
     v
Service Discovery
     |
     v
Available Payment Instances
     |
     +------ Payment 1
     |
     +------ Payment 2
     |
     +------ Payment 3
```

### Common Approaches

- DNS-based discovery.
- Kubernetes Services.
- Service registries.
- Consul.
- Cloud service discovery systems.

---

# PART 17: MESSAGE QUEUES

## 45. What Is a Message Queue?

A Message Queue allows one component to send messages to another component asynchronously.

The producer sends a message.

The message broker stores or routes it.

A consumer processes it.

### Architecture

```text
           Producer
               |
               v
          Message Queue
               |
               v
            Consumer
               |
               v
          Process Task
```

### Example: Expense Tracker

When a user uploads a receipt:

1. Backend receives the file.
2. Backend stores the file.
3. Backend publishes a processing job.
4. A worker reads the job.
5. The worker extracts information.
6. The worker saves the result.

The user does not need to wait for every background operation to finish.

## 46. Why Use Message Queues?

- Asynchronous processing.
- Workload buffering.
- Decoupling.
- Retry support.
- Improved traffic smoothing.
- Background job processing.
- Independent scaling of consumers.

### Common Message Brokers

- RabbitMQ.
- Apache Kafka.
- Amazon SQS.
- Redis Streams.
- Google Cloud Pub/Sub.

---

# PART 18: RABBITMQ

## 47. What Is RabbitMQ?

RabbitMQ is a message broker commonly used for asynchronous communication between applications.

It supports messaging patterns using exchanges, queues and bindings.

### Important Components

| Component | Meaning |
|---|---|
| Producer | Sends messages |
| Exchange | Routes messages |
| Queue | Stores messages |
| Binding | Connects exchange to queue |
| Consumer | Processes messages |
| Acknowledgment | Confirms message processing |

### RabbitMQ Architecture

```text
             Producer
                 |
                 v
              Exchange
                 |
           +-----+-----+
           |           |
           v           v
        Queue 1     Queue 2
           |           |
           v           v
       Consumer 1  Consumer 2
```

### Common Exchange Types

1. Direct Exchange.
2. Fanout Exchange.
3. Topic Exchange.
4. Headers Exchange.

### Example Use Cases

- Sending emails.
- Processing invoices.
- Generating reports.
- Background image processing.
- Order notifications.

---

# PART 19: APACHE KAFKA

## 48. What Is Apache Kafka?

Apache Kafka is a distributed event-streaming platform.

It is used to publish, store, process and consume event streams.

Kafka is especially useful when applications need to process large volumes of events and allow multiple consumers to independently read them.

Official documentation: [Apache Kafka Documentation](https://kafka.apache.org/documentation/?from_theconsensus=1&utm_source=chatgpt.com)

## 49. Kafka Architecture

```text
              Producer
                  |
                  v
             Kafka Cluster
                  |
          +-------+-------+
          |       |       |
          v       v       v
       Topic   Topic   Topic
          |
          v
       Partitions
          |
          v
       Consumers
```

### Important Kafka Concepts

**1. Producer**

Publishes events to Kafka.

**2. Consumer**

Reads events from Kafka.

**3. Topic**

A named stream of events.

**4. Partition**

A topic is divided into partitions for parallelism and scalability.

**5. Broker**

A Kafka server that stores and serves topic partitions.

**6. Consumer Group**

A group of consumers that cooperatively process topic partitions.

**7. Offset**

The position of a record within a partition.

## 50. Kafka Example

Suppose an e-commerce application creates an order.

It publishes:

```json
{
  "event": "OrderCreated",
  "orderId": "ORD1001",
  "userId": "USER101",
  "amount": 2500
}
```

Multiple consumers can process the event:

```text
                 Order Service
                       |
                       v
                   Kafka Topic
                       |
          +------------+------------+
          |            |            |
          v            v            v
     Notification   Analytics   Inventory
       Service       Service     Service
```

The notification service sends an email.

The analytics service updates statistics.

The inventory service processes stock changes.

Each consumer group can process the event independently.

## 51. RabbitMQ vs Kafka

| Feature | RabbitMQ | Kafka |
|---|---|---|
| Primary Model | Message broker | Distributed event streaming |
| Data Storage | Queue-based message storage | Durable partitioned event log |
| Consumption | Message delivery and acknowledgment | Consumers read using offsets |
| Replay | Depends on queue and message configuration | Native log-based replay within retention |
| Common Use | Background jobs and task queues | Event streaming and data pipelines |
| Scaling | Supports distributed broker deployments | Partition-based parallelism |

Neither is universally better.

Choose according to message routing, replay, retention, throughput, delivery semantics and operational requirements.

---

# PART 20: EVENT-DRIVEN ARCHITECTURE

## 52. What Is Event-Driven Architecture?

Event-Driven Architecture (EDA) is an architecture in which components communicate through events.

An event represents something that has already happened.

Examples:

- UserRegistered.
- OrderCreated.
- PaymentCompleted.
- InvoiceGenerated.
- ProductUpdated.

### Architecture

```text
              Order Service
                    |
                    v
                OrderCreated
                    |
                    v
               Event Broker
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Email     Inventory  Analytics
       Service    Service    Service
```

### Benefits

- Loose coupling.
- Asynchronous processing.
- Independent consumers.
- Better workload buffering.
- Easier event-driven integration.

### Challenges

- Eventual consistency.
- Duplicate event processing.
- Event ordering.
- Retry management.
- Dead-letter queues.
- Debugging distributed workflows.

---

# PART 21: EVENTUAL CONSISTENCY

## 53. What Is Eventual Consistency?

Eventual Consistency is a consistency model in which replicas or services may temporarily contain different data but are expected to converge if updates stop and synchronization succeeds.

### Example

An e-commerce application updates inventory.

```text
Primary Inventory: 20
Replica Inventory: 18
```

For a short period, a replica may show an older value.

After replication:

```text
Primary Inventory: 18
Replica Inventory: 18
```

The values converge.

### When Is Eventual Consistency Useful?

- Social media feeds.
- Analytics dashboards.
- Search indexes.
- Recommendation systems.
- Distributed caches.
- Replicated read models.

### When Is Stronger Consistency Important?

- Financial balances.
- Inventory reservation.
- Payment processing.
- Account ownership changes.
- Critical authorization decisions.

The correct choice depends on business invariants and acceptable stale-read behavior.

---

# PART 22: DISTRIBUTED TRANSACTIONS

## 54. What Is a Distributed Transaction?

A Distributed Transaction is a transaction that involves multiple independent services or databases.

Example:

An e-commerce order requires:

1. Creating an order.
2. Reserving inventory.
3. Processing payment.
4. Creating a shipment.

These operations may belong to different services.

### Problem

Suppose:

- Order creation succeeds.
- Inventory reservation succeeds.
- Payment fails.

What should happen?

The system must handle the partially completed workflow.

## 55. Two-Phase Commit (2PC)

Two-Phase Commit is a distributed transaction protocol.

It consists of:

1. Prepare Phase.
2. Commit Phase.

### Prepare Phase

The coordinator asks participants whether they can commit.

### Commit Phase

If all participants vote to commit, the coordinator instructs them to commit.

Otherwise, the transaction is aborted.

### Simplified Architecture

```text
             Coordinator
                  |
          +-------+-------+
          |       |       |
          v       v       v
       Service  Service Service
          1       2       3
```

2PC can provide atomic commit behavior across participating systems, but it introduces coordination overhead and blocking/failure-handling complexity.

It is not always the best choice for large microservice workflows.

---

# PART 23: SAGA PATTERN

## 56. What Is the Saga Pattern?

The Saga Pattern manages a long-running business transaction as a sequence of smaller local transactions.

Each successful step has a corresponding compensating action when a later step fails.

### Example: E-Commerce Order

Normal workflow:

```text
Create Order
     |
     v
Reserve Inventory
     |
     v
Process Payment
     |
     v
Create Shipment
     |
     v
Order Completed
```

Suppose payment fails.

The application may execute compensating actions:

```text
Payment Failed
      |
      v
Release Inventory
      |
      v
Cancel Order
```

### Saga Components

- Local transactions.
- Events or commands.
- Compensation logic.
- Workflow coordination.

### Saga Styles

**1. Choreography**

Services react to events without a central coordinator.

**2. Orchestration**

A central orchestrator controls the workflow.

### Choreography Example

```text
Order Service
     |
     v
OrderCreated Event
     |
     v
Inventory Service
     |
     v
InventoryReserved Event
     |
     v
Payment Service
     |
     v
PaymentCompleted Event
```

### Orchestration Example

```text
          Saga Orchestrator
                  |
       +----------+----------+
       |          |          |
       v          v          v
     Order    Inventory   Payment
    Service    Service    Service
```

### Important

Compensation is a new business operation, not necessarily a perfect rollback.

For example, a payment refund may take time and may not instantly reverse the original external payment.

---

# PART 24: CIRCUIT BREAKER PATTERN

## 57. What Is Circuit Breaker?

Circuit Breaker is a design pattern that prevents repeated calls to a failing service.

It helps stop cascading failures.

### Example

Your Order Service calls the Payment Service.

The Payment Service becomes unavailable.

Without protection, every incoming order may continue waiting for the failing dependency.

This can exhaust threads, connections and resources.

### Circuit Breaker States

1. Closed.
2. Open.
3. Half-Open.

### Closed State

Requests are allowed.

Failures are monitored.

### Open State

Requests are rejected or failed quickly without calling the unhealthy service.

### Half-Open State

A limited number of test requests are allowed to determine whether the dependency has recovered.

### Architecture

```text
             Order Service
                   |
                   v
             Circuit Breaker
                   |
            +------+------+
            |             |
            v             v
          Closed         Open
            |             |
            v             v
       Payment API    Fail Fast
```

### State Transition

```text
Closed
   |
   | Failure Threshold Reached
   v
Open
   |
   | Recovery Delay
   v
Half-Open
   |
   +---- Success ----> Closed
   |
   +---- Failure ----> Open
```

### Benefits

- Prevents cascading failures.
- Reduces unnecessary requests.
- Allows failing services time to recover.
- Improves system resilience.

---

# PART 25: RETRY AND TIMEOUT PATTERNS

## 58. What Is a Timeout?

A Timeout defines how long an application waits for an operation to complete.

Example:

Your Node.js service calls an external payment API.

If the API does not respond within five seconds, your application stops waiting and handles the failure.

### Example

```javascript
async function callPaymentService() {
  const controller = new AbortController();

  const timeout = setTimeout(() => {
    controller.abort();
  }, 5000);

  try {
    const response = await fetch(
      "https://payment.example.com/api/pay",
      {
        method: "POST",
        signal: controller.signal
      }
    );

    return await response.json();
  } finally {
    clearTimeout(timeout);
  }
}
```

This is an illustrative request timeout. Production payment integrations also require appropriate error classification, idempotency and reconciliation.

## 59. What Is a Retry Pattern?

Retry means attempting an operation again after a temporary failure.

Example:

```text
Request 1 → Failed
Request 2 → Failed
Request 3 → Success
```

### Exponential Backoff

Instead of retrying immediately, increase the delay after each failure.

Example:

```text
Retry 1: 1 second
Retry 2: 2 seconds
Retry 3: 4 seconds
Retry 4: 8 seconds
```

### Add Jitter

Jitter introduces randomness into retry delays.

This prevents many clients from retrying simultaneously.

### Important Retry Rules

- Retry only transient failures.
- Use bounded retry attempts.
- Set an overall deadline.
- Add jitter.
- Avoid retrying non-idempotent operations without safeguards.
- Avoid retry multiplication across multiple service layers.

---

# PART 26: IDEMPOTENCY

## 60. What Is Idempotency?

An operation is Idempotent if performing it multiple times produces the same intended result as performing it once.

### Example

Setting a user's email to the same value is idempotent.

```http
PUT /api/users/100/email
```

Sending the same request multiple times should leave the email unchanged.

### Non-Idempotent Example

Creating an order:

```http
POST /api/orders
```

If the client retries after a timeout, the server might create two orders.

This is a duplicate-processing problem.

## 61. Idempotency Keys

An Idempotency Key is a unique identifier associated with an operation.

Example:

```http
POST /api/payments
Idempotency-Key: 8f8a2d19-unique-key
```

The server records the key and result.

If the same request is received again with the same key, the server returns the stored result rather than repeating the operation.

### Simplified Workflow

```text
Client Request
      |
      v
Check Idempotency Key
      |
      +------ Exists ------> Return Previous Result
      |
      +------ New
              |
              v
         Process Request
              |
              v
         Store Result
              |
              v
         Return Result
```

Idempotency is especially important for:

- Payments.
- Order creation.
- Booking systems.
- Subscription operations.
- Message processing.

---

# PART 27: HIGH AVAILABILITY

## 62. What Is High Availability?

High Availability (HA) is the ability of a system to remain operational for a high percentage of time.

It is generally achieved through redundancy, monitoring, failover and fault isolation.

### Single-Server Architecture

```text
Users
  |
  v
Server 1
  |
  v
Database
```

If Server 1 fails, the application becomes unavailable.

### High-Availability Architecture

```text
                Users
                  |
                  v
             Load Balancer
                  |
           +------+------+
           |             |
           v             v
        Server 1       Server 2
           |             |
           +------+------+
                  |
                  v
             Database HA
```

### HA Techniques

- Multiple application instances.
- Load balancing.
- Database replication.
- Automatic failover.
- Multi-AZ deployments.
- Health checks.
- Redundant networking.
- Automated recovery.

## 63. What Is Availability Percentage?

Availability is commonly calculated as:

\[
Availability = \frac{Uptime}{Total\ Time} \times 100
\]

### Common Availability Targets

| Availability | Approximate Downtime per Year |
|---|---:|
| 99% | 3 days 15 hours 36 minutes |
| 99.9% | 8 hours 46 minutes |
| 99.95% | 4 hours 23 minutes |
| 99.99% | 52 minutes 34 seconds |
| 99.999% | 5 minutes 16 seconds |

These are approximate values for a 365-day year.

### Important

Higher availability generally increases cost and operational complexity.

Do not design every application for five-nines availability without a business requirement.

---

# PART 28: FAULT TOLERANCE

## 64. What Is Fault Tolerance?

Fault Tolerance is the ability of a system to continue providing its required functionality despite certain component failures.

Example:

If one backend server fails, other healthy servers continue processing requests.

### Techniques

- Redundancy.
- Replication.
- Failover.
- Circuit breakers.
- Bulkheads.
- Graceful degradation.
- Retry controls.
- Health checks.

## 65. What Is Graceful Degradation?

Graceful Degradation means that when some components fail, the application continues providing its most important features.

Example:

An e-commerce website's recommendation service fails.

Instead of taking down the entire website:

- Product pages remain available.
- Checkout remains available.
- Recommendations are temporarily hidden.

This is graceful degradation.

## 66. What Is the Bulkhead Pattern?

The Bulkhead Pattern isolates resources so that failure in one area does not exhaust resources used by other areas.

Example:

```text
              Application
                   |
          +--------+--------+
          |                 |
          v                 v
     Payment Pool      Email Pool
          |                 |
          v                 v
     Payment Calls     Email Jobs
```

If email processing becomes slow, it should not consume all resources required for payment processing.

---

# PART 29: DISASTER RECOVERY

## 67. What Is Disaster Recovery?

Disaster Recovery (DR) is the process of restoring applications, infrastructure and data after a major disruption.

Possible incidents:

- Data center failure.
- Accidental data deletion.
- Ransomware.
- Database corruption.
- Regional cloud outage.
- Infrastructure misconfiguration.

## 68. RTO and RPO

Two important disaster recovery metrics are:

### Recovery Time Objective (RTO)

RTO defines the maximum acceptable time required to restore a service after a disruption.

Example:

```text
RTO = 2 Hours
```

The service should be restored within two hours.

### Recovery Point Objective (RPO)

RPO defines the maximum acceptable data loss measured in time.

Example:

```text
RPO = 15 Minutes
```

The recovery process should lose no more than approximately 15 minutes of data, according to the defined recovery model.

### RTO vs RPO

| RTO | RPO |
|---|---|
| Recovery time | Recovery data point |
| Measures downtime tolerance | Measures data loss tolerance |
| Example: Restore within 2 hours | Example: Lose at most 15 minutes of data |

## 69. Disaster Recovery Strategies

| Strategy | Description |
|---|---|
| Backup and Restore | Restore from backups |
| Pilot Light | Keep a minimal recovery environment running |
| Warm Standby | Maintain a reduced-capacity environment |
| Hot Standby | Maintain a ready-to-use parallel environment |
| Active-Active | Multiple environments serve traffic simultaneously |

### Important

Backups are not enough.

You must periodically test:

- Backup integrity.
- Restoration.
- Database recovery.
- Application deployment.
- Configuration recovery.
- DNS and traffic failover.

AWS's Reliability guidance emphasizes redundancy, failure isolation, backup validation, recovery objectives and resilience testing. [AWS Documentation](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/?utm_source=chatgpt.com)

---

# PART 30: OBSERVABILITY

## 70. What Is Observability?

Observability is the ability to understand the internal state of a system by examining the data it produces.

It helps engineers investigate:

- Slow requests.
- Application errors.
- Database problems.
- Infrastructure failures.
- Distributed service issues.
- Unexpected traffic patterns.

## 71. Three Pillars of Observability

The three commonly recognized pillars are:

1. Logs.
2. Metrics.
3. Traces.

### 71.1 Logs

Logs are records of events that occurred inside an application.

Example:

```json
{
  "timestamp": "2026-11-25T10:30:00Z",
  "level": "error",
  "service": "payment-service",
  "message": "Payment processing failed",
  "requestId": "req-1001"
}
```

### 71.2 Metrics

Metrics are numerical measurements collected over time.

Examples:

- CPU usage.
- Memory usage.
- Request rate.
- Error rate.
- Response latency.
- Database connection count.

### 71.3 Distributed Tracing

Distributed Tracing tracks a request as it travels through multiple services.

Example:

```text
Client Request
     |
     v
API Gateway
     |
     v
Order Service
     |
     v
Payment Service
     |
     v
Database
```

Tracing helps identify which component caused latency or failure.

## 72. Monitoring vs Observability

| Monitoring | Observability |
|---|---|
| Tracks known conditions | Helps investigate unknown conditions |
| Uses predefined metrics and alerts | Uses logs, metrics, traces and context |
| Answers whether something is wrong | Helps explain why something is wrong |
| Example: CPU exceeds 90% | Example: Identify which request path caused latency |

### Common Observability Tools

- Prometheus.
- Grafana.
- OpenTelemetry.
- Jaeger.
- Datadog.
- New Relic.
- AWS CloudWatch.

---

# PART 31: PERFORMANCE ENGINEERING

## 73. What Is Latency?

Latency is the time taken to complete an operation.

Example:

An API request takes 200 milliseconds to return a response.

Its latency is 200 ms.

## 74. What Is Throughput?

Throughput measures how much work a system completes over a given period.

Example:

```text
API Throughput = 1,000 requests/second
```

### Latency vs Throughput

| Latency | Throughput |
|---|---|
| Time required for an operation | Number of operations completed per unit time |
| Measured in ms or seconds | Measured in requests/sec or transactions/sec |
| Lower is generally better | Higher is generally better, within capacity and correctness constraints |

## 75. What Is P95 and P99 Latency?

Percentiles describe the distribution of request latency.

- P50: 50% of requests complete within this time.
- P95: 95% of requests complete within this time.
- P99: 99% of requests complete within this time.

Example:

| Metric | Latency |
|---|---:|
| P50 | 80 ms |
| P95 | 250 ms |
| P99 | 800 ms |

This means 95% of requests completed within 250 ms, while the slowest 5% took longer.

P95 and P99 are often more useful than averages because averages can hide slow requests.

## 76. Performance Optimization Techniques

- Database indexing.
- Caching.
- Query optimization.
- Connection pooling.
- Load balancing.
- CDN.
- Compression.
- Asynchronous processing.
- Pagination.
- Efficient algorithms.
- Reducing unnecessary network calls.
- Profiling.

---

# PART 32: RATE LIMITING ARCHITECTURE

## 77. Rate Limiting in Distributed Systems

A basic rate limiter may work on a single server.

However, when your application runs on multiple servers, each server needs to share rate-limit state if you want a global limit.

### Problem

Suppose your policy is:

```text
Maximum 100 requests per minute per user.
```

Your application has three backend servers.

If each server independently permits 100 requests, a user may make approximately 300 requests across the entire deployment.

### Distributed Solution

Use a shared store such as Redis.

```text
             Load Balancer
                   |
          +--------+--------+
          |        |        |
          v        v        v
       Server 1 Server 2 Server 3
          |        |        |
          +--------+--------+
                   |
                   v
                 Redis
                   |
                   v
           Shared Rate Limits
```

### Common Rate-Limiting Algorithms

1. Fixed Window Counter.
2. Sliding Window Log.
3. Sliding Window Counter.
4. Token Bucket.
5. Leaky Bucket.

### Token Bucket

The token bucket algorithm maintains a bucket containing tokens.

Requests consume tokens.

Tokens are replenished at a configured rate.

If no tokens remain, the request is rejected or delayed.

This supports controlled bursts while maintaining an average request rate.

---

# PART 33: SYSTEM DESIGN CASE STUDY 1 – URL SHORTENER

## 78. Problem Statement

Design a URL shortener similar to Bitly.

A user submits:

```text
https://example.com/products/electronics/mobile/12345
```

The application generates:

```text
https://short.example/AbC123
```

When someone opens the short URL, the application redirects them to the original URL.

## 79. Functional Requirements

- Create short URLs.
- Redirect short URLs.
- Support custom aliases.
- Track click counts.
- Set expiration dates.
- Allow users to manage their links.

## 80. Non-Functional Requirements

- Low-latency redirects.
- High availability.
- Scalability.
- Unique short codes.
- Reliable storage.
- Abuse protection.

## 81. Database Schema

```sql
CREATE TABLE urls (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,

  short_code VARCHAR(20) NOT NULL UNIQUE,

  original_url TEXT NOT NULL,

  user_id BIGINT,

  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

  expires_at TIMESTAMP NULL
);
```

## 82. High-Level Architecture

```text
               User
                 |
                 v
             Load Balancer
                 |
                 v
             URL Service
                 |
          +------+------+
          |             |
          v             v
        Redis         MySQL
          |
          v
     Cached URL
```

## 83. Short Code Generation

A common approach is to generate a unique identifier and encode it using Base62.

Base62 characters:

```text
0123456789
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

Example:

```text
ID: 125
Base62: 21
```

The actual generated code length depends on the encoding and identifier size.

## 84. Redirect Workflow

```text
User Opens Short URL
         |
         v
Extract Short Code
         |
         v
Check Redis
         |
    +----+----+
    |         |
    v         v
  Found     Not Found
    |         |
    |         v
    |       MySQL
    |         |
    +----+----+
         |
         v
   Original URL
         |
         v
     HTTP Redirect
```

### Design Considerations

- Use a unique constraint on short codes.
- Handle code collisions.
- Cache frequently accessed URLs.
- Protect against malicious redirects.
- Apply abuse detection.
- Use asynchronous analytics where appropriate.

---

# PART 34: SYSTEM DESIGN CASE STUDY 2 – WHATSAPP-LIKE CHAT APPLICATION

## 85. Problem Statement

Design a real-time messaging application.

Required features:

- User registration.
- One-to-one messaging.
- Group messaging.
- Message history.
- Online/offline status.
- Message delivery status.
- Image and file sharing.
- Push notifications.

## 86. Functional Requirements

1. Users can send messages.
2. Users can receive messages in real time.
3. Users can create groups.
4. Users can see message history.
5. Users can share media.
6. Users can see delivery status.

## 87. Non-Functional Requirements

- Low messaging latency.
- High availability.
- Message durability.
- Horizontal scalability.
- Secure communication.
- Reliable delivery.

## 88. High-Level Architecture

```text
                  Mobile/Web Clients
                          |
                          v
                    Load Balancer
                          |
                          v
                   WebSocket Gateway
                          |
                +---------+---------+
                |                   |
                v                   v
           Message Service     Presence Service
                |                   |
                v                   v
             Message DB           Redis
                |
                v
            Message Queue
                |
        +-------+-------+
        |               |
        v               v
   Notification     Delivery Worker
      Service
        |
        v
   Push Provider
```

## 89. Why WebSockets?

WebSockets provide a persistent, bidirectional communication channel between client and server.

Unlike traditional HTTP request-response communication, WebSockets allow the server to send messages to clients over an established connection.

### Example

```javascript
const WebSocket = require("ws");

const server = new WebSocket.Server({
  port: 8080
});

server.on("connection", (socket) => {
  console.log("Client connected");

  socket.on("message", (message) => {
    console.log(message.toString());
  });

  socket.send("Welcome to chat");
});
```

This is a basic demonstration. Production chat systems require authentication, authorization, connection management, heartbeats, reconnection, message persistence and distributed routing.

## 90. Message Delivery Flow

```text
Sender
   |
   v
WebSocket Gateway
   |
   v
Message Service
   |
   v
Persist Message
   |
   v
Message Queue
   |
   v
Recipient Gateway
   |
   v
Recipient
```

### Message Status

A messaging system may track:

| Status | Meaning |
|---|---|
| Sent | Message accepted by the sending service |
| Delivered | Message reached the recipient device or defined delivery endpoint |
| Read | Recipient marked the message as read |

The exact meaning of each status should be clearly defined.

### Scaling Challenges

- Millions of persistent connections.
- Connection routing.
- Message ordering.
- Duplicate delivery.
- Offline message storage.
- Group fan-out.
- Presence tracking.
- Media storage.
- Encryption.

### Important Design Decision

Use durable message storage as the source of truth.

Do not rely exclusively on WebSocket connections for message persistence.

---

# PART 35: SYSTEM DESIGN CASE STUDY 3 – E-COMMERCE APPLICATION

## 91. Problem Statement

Design an e-commerce application similar to Amazon.

### Functional Requirements

- User registration.
- Product catalog.
- Product search.
- Shopping cart.
- Order placement.
- Inventory management.
- Payment processing.
- Order tracking.
- Notifications.

## 92. High-Level Architecture

```text
                  Client
                    |
                    v
                CDN / WAF
                    |
                    v
               API Gateway
                    |
       +------------+------------+
       |            |            |
       v            v            v
    User          Product       Cart
   Service        Service      Service
       |            |            |
       v            v            v
    User DB      Product DB    Redis
                    |
                    v
                Order Service
                    |
              +-----+-----+
              |           |
              v           v
        Inventory       Payment
         Service        Service
              |           |
              v           v
          Inventory    Payment DB
              DB
                    |
                    v
                Event Broker
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Email     Analytics  Shipping
       Service    Service   Service
```

## 93. Important Design Decisions

### Product Catalog

- Use database indexes.
- Cache popular products.
- Use search infrastructure when needed.
- Use CDN for product images.

### Shopping Cart

- Redis can support frequently accessed cart state.
- Persist cart data when durability is required.
- Validate prices and inventory on the server.

### Orders

- Use transactional order creation.
- Apply idempotency to order submissions.
- Track order states.
- Avoid duplicate processing.

### Payments

- Integrate trusted payment providers.
- Use idempotency keys.
- Verify payment callbacks or webhooks.
- Maintain payment records.
- Reconcile uncertain payment outcomes.

### Inventory

- Prevent overselling.
- Use atomic stock updates or reservations.
- Handle concurrent purchase attempts.
- Release expired reservations.

---

# PART 36: SYSTEM DESIGN CASE STUDY 4 – VIDEO STREAMING APPLICATION

## 94. Problem Statement

Design a video streaming platform similar to YouTube or Netflix.

### Functional Requirements

- Upload videos.
- Process videos.
- Store videos.
- Stream videos.
- Search videos.
- Manage user accounts.
- Track viewing history.
- Generate recommendations.

## 95. High-Level Architecture

```text
                  User
                    |
                    v
                   CDN
                    |
                    v
               API Gateway
                    |
       +------------+------------+
       |            |            |
       v            v            v
    User         Video        Search
   Service      Service       Service
       |            |
       v            v
    User DB      Metadata DB
                    |
                    v
                Object Storage
                    |
                    v
               Processing Queue
                    |
                    v
              Video Workers
                    |
                    v
              Transcoding
                    |
                    v
             Processed Videos
                    |
                    v
                   CDN
                    |
                    v
                  Users
```

## 96. Why Video Transcoding?

Different users have different:

- Network speeds.
- Device capabilities.
- Screen resolutions.
- Playback requirements.

A video may be transcoded into several resolutions and bitrates.

Example:

```text
Original Video
      |
      v
 Video Processing
      |
  +---+---+---+
  |   |   |   |
  v   v   v   v
360p 480p 720p 1080p
```

## 97. Adaptive Bitrate Streaming

Adaptive Bitrate Streaming adjusts video quality based on network conditions and device capabilities.

A player may switch between quality levels during playback.

Common streaming formats include:

- HLS.
- MPEG-DASH.

The video files or segments are distributed through a CDN to reduce latency and origin load.

---

# PART 37: SYSTEM DESIGN INTERVIEW APPROACH

## 98. How to Approach a System Design Interview

Use a structured process.

### Step 1: Clarify Requirements

Ask:

- Who are the users?
- What features are required?
- What is out of scope?
- What are the expected traffic levels?

### Step 2: Define Functional Requirements

List the main operations.

Example:

```text
Create URL
Redirect URL
Delete URL
Track Clicks
```

### Step 3: Define Non-Functional Requirements

Examples:

- Availability.
- Latency.
- Scalability.
- Consistency.
- Security.

### Step 4: Estimate Scale

Estimate:

- Daily active users.
- Requests per second.
- Storage requirements.
- Read/write ratio.
- Bandwidth.
- Concurrent connections.

### Step 5: Design APIs

Example:

```http
POST /api/urls
GET /:shortCode
DELETE /api/urls/:id
```

### Step 6: Design Database

Define:

- Entities.
- Relationships.
- Indexes.
- Constraints.
- Partitioning strategy if needed.

### Step 7: Draw High-Level Architecture

Identify:

- Client.
- Load balancer.
- Backend services.
- Database.
- Cache.
- Queue.
- Storage.
- External services.

### Step 8: Identify Bottlenecks

Examples:

- Database reads.
- Large file downloads.
- Slow external APIs.
- CPU-heavy background tasks.

### Step 9: Introduce Scaling Strategies

Consider:

- Caching.
- Replication.
- Horizontal scaling.
- Queue-based processing.
- CDN.
- Database partitioning.

### Step 10: Discuss Failure Handling

Explain:

- Timeouts.
- Retries.
- Circuit breakers.
- Backups.
- Failover.
- Monitoring.

### Step 11: Discuss Trade-Offs

Every architecture has trade-offs.

Explain why you selected one approach over another.

---

# PART 38: COMMON SYSTEM DESIGN MISTAKES

## 99. Mistakes Developers Should Avoid

| Mistake | Why It Is a Problem |
|---|---|
| Using microservices too early | Adds unnecessary operational complexity |
| No database indexes | Causes inefficient queries |
| Caching everything | Creates invalidation and consistency problems |
| No timeouts | Requests may wait indefinitely |
| Unlimited retries | Can amplify outages |
| No idempotency | Can create duplicate operations |
| One database for every workload | Can create contention |
| Storing session state locally | Complicates horizontal scaling |
| No observability | Makes failures difficult to diagnose |
| Ignoring network failures | Distributed systems depend on unreliable networks |
| No backup testing | Backups may not restore successfully |
| Ignoring data consistency | Can create business correctness problems |
| Overengineering | Increases cost and maintenance |

---

# PART 39: SYSTEM DESIGN INTERVIEW QUESTIONS AND ANSWERS

## Beginner-Level Questions

### Q1. What is System Design?

System Design is the process of defining the architecture and interactions of components required to build a software system.

### Q2. What is HLD?

High-Level Design describes the overall architecture, components, databases and communication patterns.

### Q3. What is LLD?

Low-Level Design describes internal implementation details such as classes, methods, interfaces and data structures.

### Q4. What are Functional Requirements?

They describe what the application should do.

### Q5. What are Non-Functional Requirements?

They describe quality attributes such as scalability, performance, availability and security.

### Q6. What is Scalability?

Scalability is the ability of a system to handle increasing workloads by adding or allocating resources.

### Q7. What is Vertical Scaling?

Increasing the resources of an existing server.

### Q8. What is Horizontal Scaling?

Adding more servers or application instances.

### Q9. What is Load Balancing?

Distributing incoming traffic across multiple backend servers.

### Q10. What is a Reverse Proxy?

A server that receives client requests and forwards them to backend servers.

## Intermediate-Level Questions

### Q11. What is Caching?

Caching stores frequently accessed data in a faster storage layer to reduce latency and backend load.

### Q12. What is Cache-Aside?

An application first checks the cache, fetches data from the database on a cache miss, and stores the result in the cache.

### Q13. What is Redis?

Redis is an in-memory data store commonly used for caching, sessions, counters and real-time data structures.

### Q14. What is a CDN?

A Content Delivery Network distributes content through geographically distributed edge locations.

### Q15. What is Database Replication?

Maintaining copies of database data across multiple database servers.

### Q16. What is Database Sharding?

Distributing different subsets of data across multiple database instances.

### Q17. What is Database Partitioning?

Dividing a large table into smaller logical partitions.

### Q18. What is a Database Index?

A data structure that helps a database locate records efficiently.

### Q19. What is a Distributed System?

A collection of independent computers or services that communicate over a network to provide a larger system.

### Q20. What is CAP Theorem?

CAP describes the trade-off between consistency and availability when a distributed system experiences a network partition.

## Advanced-Level Questions

### Q21. What is ACID?

Atomicity, Consistency, Isolation and Durability are transaction properties.

### Q22. What is BASE?

Basically Available, Soft State and Eventually Consistent describe an approach often used in distributed architectures.

### Q23. What is Microservices Architecture?

An architecture that divides an application into independently deployable services focused on business capabilities.

### Q24. What is an API Gateway?

An entry point that routes client API requests to appropriate backend services and may provide additional API policies.

### Q25. What is Service Discovery?

A mechanism that allows services to locate available instances of other services.

### Q26. What is a Message Queue?

A system that stores and routes messages between producers and consumers.

### Q27. What is Apache Kafka?

A distributed event-streaming platform used to publish, store, process and consume event streams.

### Q28. What is RabbitMQ?

A message broker commonly used for asynchronous messaging and task queues.

### Q29. What is Event-Driven Architecture?

An architecture in which components communicate through events.

### Q30. What is Eventual Consistency?

A consistency model where replicas may temporarily differ but are expected to converge after updates stop and synchronization succeeds.

### Q31. What is the Saga Pattern?

A pattern that manages distributed business transactions through a sequence of local transactions and compensating actions.

### Q32. What is Circuit Breaker?

A pattern that stops repeated calls to a failing dependency to prevent cascading failures.

### Q33. What is Idempotency?

An operation is idempotent if repeating it produces the same intended result as performing it once.

### Q34. What is High Availability?

The ability of a system to remain operational for a high percentage of time.

### Q35. What is Fault Tolerance?

The ability of a system to continue providing required functionality despite certain component failures.

## Expert-Level Questions

### Q36. What is the difference between Availability and Fault Tolerance?

Availability measures how often a system is operational. Fault tolerance describes its ability to continue operating despite failures.

### Q37. What is the difference between RTO and RPO?

RTO measures acceptable recovery time. RPO measures acceptable data loss in time.

### Q38. What is Graceful Degradation?

The ability to preserve critical application features when non-critical components fail.

### Q39. What is the Bulkhead Pattern?

A pattern that isolates resources to prevent one failing component from exhausting resources needed by other components.

### Q40. What is Exponential Backoff?

A retry strategy that increases the delay between successive retry attempts.

### Q41. Why Is Jitter Added to Retry Delays?

Jitter prevents many clients from retrying simultaneously and creating synchronized traffic spikes.

### Q42. What is a Cache Stampede?

A situation where many requests simultaneously miss an expired cache entry and overload the underlying database.

### Q43. What is a Dead-Letter Queue?

A queue that stores messages that could not be processed successfully after the configured handling or retry policy.

### Q44. What is a Consumer Group in Kafka?

A group of consumers that cooperatively processes partitions of a topic.

### Q45. What is a Kafka Offset?

A position identifying a record within a Kafka partition.

### Q46. What is the difference between Choreography and Orchestration?

Choreography uses event-driven coordination between services. Orchestration uses a central coordinator to control the workflow.

### Q47. Why Is Database Sharding Difficult?

It introduces shard routing, cross-shard queries, rebalancing, transaction complexity and potential data hotspots.

### Q48. Why Should Applications Be Stateless?

Stateless application instances are easier to scale horizontally because they do not depend on local session state.

### Q49. What is Observability?

Observability is the ability to understand system behavior using logs, metrics, traces and related context.

### Q50. What is the difference between Latency and Throughput?

Latency measures the time taken for an operation. Throughput measures how many operations a system completes per unit of time.

---

# PART 40: HANDS-ON PRACTICAL ASSIGNMENTS

## Level 1 – System Design Fundamentals

| No. | Assignment | Expected Result |
|---:|---|---|
| 1 | Design a simple Expense Tracker HLD | Architecture diagram |
| 2 | Create an Expense Tracker LLD | Classes and module structure |
| 3 | Define functional requirements | Feature specification |
| 4 | Define non-functional requirements | Quality requirements |
| 5 | Compare HLD and LLD | Design documentation |

## Level 2 – Scalability and Infrastructure

| No. | Assignment | Expected Result |
|---:|---|---|
| 6 | Implement multiple Express instances | Multiple backend servers |
| 7 | Configure Nginx load balancing | Traffic distribution |
| 8 | Configure health-check endpoints | Server health status |
| 9 | Implement stateless authentication | Horizontal scaling support |
| 10 | Configure a reverse proxy | Centralized request routing |

## Level 3 – Caching and Databases

| No. | Assignment | Expected Result |
|---:|---|---|
| 11 | Install Redis | Working Redis instance |
| 12 | Implement cache-aside | Cached database reads |
| 13 | Implement cache invalidation | Fresh cached data |
| 14 | Implement cache TTL | Expiring cache entries |
| 15 | Add MySQL indexes | Improved suitable queries |
| 16 | Analyze query execution plans | Query optimization |
| 17 | Study database replication | Primary-replica architecture |
| 18 | Design a sharding strategy | Shard key and routing plan |
| 19 | Design table partitioning | Partitioned database schema |

## Level 4 – Distributed Systems

| No. | Assignment | Expected Result |
|---:|---|---|
| 20 | Build two independent Express services | Service-to-service communication |
| 21 | Implement an API Gateway | Centralized routing |
| 22 | Configure service discovery | Dynamic service lookup |
| 23 | Install RabbitMQ | Working message broker |
| 24 | Implement producer and consumer | Asynchronous processing |
| 25 | Install Kafka locally | Working Kafka environment |
| 26 | Create a Kafka topic | Event stream |
| 27 | Implement Kafka producer | Event publishing |
| 28 | Implement Kafka consumer | Event processing |
| 29 | Build an event-driven workflow | Decoupled services |

## Level 5 – Reliability and Performance

| No. | Assignment | Expected Result |
|---:|---|---|
| 30 | Implement timeout handling | Bounded network requests |
| 31 | Implement exponential backoff | Controlled retries |
| 32 | Implement idempotency keys | Duplicate-request protection |
| 33 | Implement circuit breaker | Failure isolation |
| 34 | Implement a dead-letter queue | Failed-message handling |
| 35 | Implement distributed rate limiting | Shared request limits |
| 36 | Configure application health checks | Readiness and liveness |
| 37 | Create a backup and restore procedure | Tested recovery |
| 38 | Define RTO and RPO | Recovery targets |
| 39 | Configure application metrics | Performance visibility |
| 40 | Implement distributed tracing | Request tracing |

## Level 6 – System Design Projects

| No. | Assignment | Expected Result |
|---:|---|---|
| 41 | Design a URL Shortener | HLD and database design |
| 42 | Design a WhatsApp-like application | Messaging architecture |
| 43 | Design an E-Commerce application | Service architecture |
| 44 | Design a Video Streaming application | Media delivery architecture |
| 45 | Design a Notification System | Queue-based notification architecture |

---

# PART 41: COMPLETE PROJECT – SCALABLE EXPENSE TRACKER

## 100. Project Objective

Build a scalable Expense Tracker that can support increasing users, requests and data volumes.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + React + TypeScript |
| Backend | Node.js + Express |
| Database | MySQL |
| Cache | Redis |
| Message Broker | RabbitMQ |
| Reverse Proxy | Nginx |
| Containerization | Docker |
| Deployment | AWS EC2 |
| Monitoring | Prometheus + Grafana |
| Testing | Jest + Supertest + Playwright |

## 101. Architecture

```text
                    Users
                      |
                      v
                     CDN
                      |
                      v
                 Nginx / LB
                      |
             +--------+--------+
             |        |        |
             v        v        v
          API 1     API 2     API 3
             |        |        |
             +--------+--------+
                      |
              +-------+-------+
              |               |
              v               v
            Redis           MySQL
              |
              |
         +----+----------------+
         |                     |
         v                     v
      Cache                 Primary DB
                               |
                               v
                          Read Replica

             API / Background Jobs
                      |
                      v
                  RabbitMQ
                      |
              +-------+-------+
              |               |
              v               v
          Email Worker    Report Worker
```

## 102. Required Features

### User Management

- Registration.
- Login.
- Authentication.
- Authorization.
- Profile management.

### Expense Management

- Create expenses.
- Update expenses.
- Delete expenses.
- Search expenses.
- Filter expenses.
- Generate reports.

### Caching

- Cache user profiles.
- Cache dashboard summaries.
- Cache frequently requested categories.
- Invalidate cache after updates.

### Background Processing

- Generate Excel reports.
- Send email notifications.
- Process uploaded receipts.
- Generate scheduled summaries.

### Scalability

- Multiple API instances.
- Load balancing.
- Redis.
- Database indexes.
- Database replication where needed.
- Queue-based background processing.

### Reliability

- Health checks.
- Request timeouts.
- Controlled retries.
- Circuit breakers for external dependencies.
- Database backups.
- Error monitoring.

### Observability

- Request logs.
- Error logs.
- API latency metrics.
- Database metrics.
- Queue depth.
- Cache hit ratio.

---

# PART 42: QUICK REVISION NOTES

| Concept | Meaning |
|---|---|
| System Design | Designing complete software architecture |
| HLD | Overall architecture |
| LLD | Detailed component implementation |
| Scalability | Ability to handle growing workload |
| Vertical Scaling | Increasing resources of one server |
| Horizontal Scaling | Adding more servers |
| Load Balancer | Distributes incoming traffic |
| Reverse Proxy | Forwards client requests to backend servers |
| Caching | Stores frequently accessed data |
| Redis | In-memory data store |
| CDN | Geographically distributed content delivery |
| Replication | Maintaining data copies |
| Sharding | Distributing data across database instances |
| Partitioning | Dividing tables into smaller sections |
| Indexing | Improves suitable database lookups |
| SQL | Relational database model |
| NoSQL | Non-relational database models |
| Distributed System | Multiple computers working together |
| CAP | Consistency, Availability, Partition Tolerance |
| ACID | Atomicity, Consistency, Isolation, Durability |
| BASE | Basically Available, Soft State, Eventually Consistent |
| Monolith | One main deployable application |
| Microservices | Independently deployable business services |
| API Gateway | Central API entry point |
| Service Discovery | Locating available service instances |
| Message Queue | Asynchronous message communication |
| RabbitMQ | Message broker |
| Kafka | Distributed event-streaming platform |
| Event-Driven Architecture | Architecture based on events |
| Eventual Consistency | Replicas converge over time |
| Distributed Transaction | Transaction involving multiple systems |
| Saga | Sequence of local transactions and compensations |
| Circuit Breaker | Prevents repeated calls to failing dependencies |
| Retry | Reattempting an operation |
| Exponential Backoff | Increasing retry delays |
| Idempotency | Repeated operation has the same intended result |
| High Availability | Maintaining operational availability |
| Fault Tolerance | Continuing despite component failures |
| Graceful Degradation | Preserving critical features during failures |
| RTO | Maximum acceptable recovery time |
| RPO | Maximum acceptable data loss interval |
| Observability | Understanding system behavior |
| Logs | Records of events |
| Metrics | Numerical system measurements |
| Tracing | Following requests across services |
| Latency | Time required for an operation |
| Throughput | Operations completed per unit time |
| P95 | Latency threshold for 95% of requests |
| P99 | Latency threshold for 99% of requests |

---

# PART 43: PHASE 60 COMPLETION CHECKLIST

### System Design Fundamentals

- [ ] Understand System Design.
- [ ] Understand HLD.
- [ ] Understand LLD.
- [ ] Understand Functional Requirements.
- [ ] Understand Non-Functional Requirements.

### Scalability and Infrastructure

- [ ] Understand Scalability.
- [ ] Understand Vertical Scaling.
- [ ] Understand Horizontal Scaling.
- [ ] Understand Load Balancing.
- [ ] Understand Load Balancing Algorithms.
- [ ] Understand Health Checks.
- [ ] Understand Reverse Proxy.

### Caching and Database Architecture

- [ ] Understand Caching.
- [ ] Understand Cache-Aside.
- [ ] Understand Read-Through.
- [ ] Understand Write-Through.
- [ ] Understand Cache Invalidation.
- [ ] Understand Cache Stampede.
- [ ] Understand Redis.
- [ ] Understand CDN.
- [ ] Understand Database Replication.
- [ ] Understand Database Sharding.
- [ ] Understand Database Partitioning.
- [ ] Understand Database Indexing.
- [ ] Understand SQL vs NoSQL.

### Distributed Systems

- [ ] Understand Distributed Systems.
- [ ] Understand Partial Failures.
- [ ] Understand CAP Theorem.
- [ ] Understand ACID.
- [ ] Understand BASE.
- [ ] Understand Monolithic Architecture.
- [ ] Understand Microservices Architecture.
- [ ] Understand API Gateway.
- [ ] Understand Service Discovery.

### Messaging and Event-Driven Architecture

- [ ] Understand Message Queues.
- [ ] Understand RabbitMQ.
- [ ] Understand Kafka.
- [ ] Understand Producers and Consumers.
- [ ] Understand Topics and Partitions.
- [ ] Understand Consumer Groups.
- [ ] Understand Event-Driven Architecture.
- [ ] Understand Eventual Consistency.

### Distributed Transactions and Reliability

- [ ] Understand Distributed Transactions.
- [ ] Understand Two-Phase Commit.
- [ ] Understand Saga Pattern.
- [ ] Understand Choreography.
- [ ] Understand Orchestration.
- [ ] Understand Circuit Breaker.
- [ ] Understand Retry Pattern.
- [ ] Understand Exponential Backoff.
- [ ] Understand Idempotency.

### High Availability and Recovery

- [ ] Understand High Availability.
- [ ] Understand Fault Tolerance.
- [ ] Understand Graceful Degradation.
- [ ] Understand Bulkhead Pattern.
- [ ] Understand Disaster Recovery.
- [ ] Understand RTO.
- [ ] Understand RPO.
- [ ] Understand Availability Percentages.

### Performance and Observability

- [ ] Understand Latency.
- [ ] Understand Throughput.
- [ ] Understand P95.
- [ ] Understand P99.
- [ ] Understand Distributed Rate Limiting.
- [ ] Understand Logs.
- [ ] Understand Metrics.
- [ ] Understand Distributed Tracing.
- [ ] Understand Observability.

### Practical Implementation

- [ ] Build a URL Shortener.
- [ ] Build a Redis Cache Layer.
- [ ] Configure Nginx Load Balancing.
- [ ] Build a RabbitMQ Worker.
- [ ] Build a Kafka Producer and Consumer.
- [ ] Implement Circuit Breaker.
- [ ] Implement Idempotency.
- [ ] Design a WhatsApp-like System.
- [ ] Design an E-Commerce System.
- [ ] Design a Video Streaming System.
- [ ] Build a Scalable Expense Tracker.

---

# Official Documentation and Learning Resources

| Technology / Concept | Documentation |
|---|---|
| AWS Reliability Architecture | [AWS Well-Architected Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/?utm_source=chatgpt.com) |
| Redis | [Redis Documentation](https://redis.io/docs/latest/?utm_source=chatgpt.com) |
| Redis Caching | [Redis Cache-Aside Guide](https://redis.io/docs/latest/develop/use-cases/cache-aside/?utm_source=chatgpt.com) |
| Apache Kafka | [Apache Kafka Documentation](https://kafka.apache.org/documentation/?from_theconsensus=1&utm_source=chatgpt.com) |
| RabbitMQ | [RabbitMQ Documentation](https://www.rabbitmq.com/docs?utm_source=chatgpt.com) |
| Nginx | [Nginx Documentation](https://nginx.org/en/docs/?utm_source=chatgpt.com) |
| Docker | [Docker Documentation](https://docs.docker.com/?utm_source=chatgpt.com) |
| Kubernetes | [Kubernetes Documentation](https://kubernetes.io/docs/home/?utm_source=chatgpt.com) |
| MySQL | [MySQL Documentation](https://dev.mysql.com/doc/?utm_source=chatgpt.com) |
| MongoDB | [MongoDB Documentation](https://www.mongodb.com/docs/?utm_source=chatgpt.com) |
| OpenTelemetry | [OpenTelemetry Documentation](https://opentelemetry.io/docs/?utm_source=chatgpt.com) |
| Prometheus | [Prometheus Documentation](https://prometheus.io/docs/?utm_source=chatgpt.com) |
| Grafana | [Grafana Documentation](https://grafana.com/docs/?utm_source=chatgpt.com) |

---

# Phase 60 – Final Summary

In this phase, you learned how to design scalable, distributed and highly available software systems.

The most important concepts are:

1. **HLD and LLD:** Design the overall architecture and internal components.
2. **Scalability:** Handle increasing workloads.
3. **Load Balancing:** Distribute requests across multiple servers.
4. **Caching:** Reduce database load and latency.
5. **Redis:** Provide fast shared data storage.
6. **CDN:** Deliver content closer to users.
7. **Database Replication:** Maintain multiple copies of data.
8. **Sharding:** Distribute data across database instances.
9. **CAP Theorem:** Understand consistency and availability trade-offs during network partitions.
10. **ACID and BASE:** Understand different data consistency approaches.
11. **Microservices:** Build independently deployable services.
12. **Message Queues:** Process background work asynchronously.
13. **Kafka:** Build distributed event-streaming systems.
14. **Saga Pattern:** Coordinate distributed business transactions.
15. **Circuit Breaker:** Prevent cascading failures.
16. **Idempotency:** Prevent duplicate processing.
17. **High Availability:** Keep systems operational.
18. **Disaster Recovery:** Restore services after major failures.
19. **Observability:** Understand application behavior using logs, metrics and traces.
20. **System Design:** Combine these concepts to build real-world applications.

### Recommended Practical Learning Order

| Order | Topic | Priority |
|---:|---|---|
| 1 | HLD and LLD | Essential |
| 2 | Vertical and Horizontal Scaling | Essential |
| 3 | Nginx and Load Balancing | Essential |
| 4 | Redis and Caching | Essential |
| 5 | Database Indexing and Replication | Essential |
| 6 | Message Queues and RabbitMQ | Important |
| 7 | Kafka and Event-Driven Architecture | Advanced |
| 8 | Microservices and API Gateway | Advanced |
| 9 | Saga, Circuit Breaker and Idempotency | Advanced |
| 10 | High Availability and Disaster Recovery | Advanced |
| 11 | Observability and Distributed Tracing | Advanced |
| 12 | Real-World System Design Projects | Expert |

---

# Next Phase Preview

## Phase 61 – Advanced Software Architecture, Design Patterns and Enterprise Application Development

Topics planned:

1. Software Architecture Fundamentals.
2. Architectural Styles.
3. Layered Architecture.
4. Clean Architecture.
5. Hexagonal Architecture.
6. Onion Architecture.
7. Domain-Driven Design (DDD).
8. Domain Modeling.
9. Bounded Contexts.
10. Aggregates and Entities.
11. Value Objects.
12. Repository Pattern.
13. Unit of Work Pattern.
14. Dependency Injection.
15. Inversion of Control.
16. SOLID Principles.
17. DRY, KISS and YAGNI.
18. Creational Design Patterns.
19. Structural Design Patterns.
20. Behavioral Design Patterns.
21. Factory Pattern.
22. Abstract Factory Pattern.
23. Builder Pattern.
24. Singleton Pattern.
25. Adapter Pattern.
26. Decorator Pattern.
27. Facade Pattern.
28. Proxy Pattern.
29. Strategy Pattern.
30. Observer Pattern.
31. Command Pattern.
32. Template Method Pattern.
33. Mediator Pattern.
34. Specification Pattern.
35. CQRS.
36. Event Sourcing.
37. Domain Events.
38. Dependency Management.
39. Modular Monolith Architecture.
40. Enterprise Application Architecture.
41. Refactoring and Technical Debt.
42. Architecture Decision Records (ADR).
43. Real-World Node.js Architecture.
44. Real-World Next.js Architecture.
45. Enterprise Project Structure.

**When you're ready, say "OK, Phase 61" and we'll continue with detailed notes.**