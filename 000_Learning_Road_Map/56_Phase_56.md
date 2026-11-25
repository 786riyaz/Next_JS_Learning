# Phase 56 – Advanced System Design and Distributed Systems

**Level:** Advanced  
**Domain:** System Design, Software Architecture, Distributed Systems, Scalability and High-Performance Backend Engineering  
**Primary Technologies:** Node.js, Redis, MySQL, MongoDB, Kafka, RabbitMQ, Docker, Kubernetes and AWS

---

# 1. Learning Objectives

In this phase, you will learn how to design large-scale applications that can handle millions of users, large amounts of data and high request volumes.

By completing this phase, you should understand:

1. System Design fundamentals.
2. Monolithic vs Microservices architecture.
3. Distributed Systems fundamentals.
4. Vertical and Horizontal Scaling.
5. Load Balancing strategies.
6. Database Replication.
7. Database Sharding.
8. Database Partitioning.
9. Advanced Caching Strategies.
10. Redis architecture.
11. Message Queues and Event-Driven Architecture.
12. CAP Theorem.
13. Consistency Models.
14. ACID vs BASE.
15. Distributed Transactions.
16. Eventual Consistency.
17. Rate Limiting.
18. API Gateway Architecture.
19. Fault Tolerance.
20. Circuit Breaker Pattern.
21. Retry and Backoff Strategies.
22. Idempotency.
23. Distributed Locking.
24. Database Indexing at Scale.
25. URL Shortener System Design.
26. WhatsApp-like Application Design.
27. YouTube-like Application Design.
28. E-commerce System Design.
29. Notification System Design.
30. System Design Interview Problem Solving.

---

# PART 1: SYSTEM DESIGN FUNDAMENTALS

## 2. What Is System Design?

System Design is the process of designing the architecture, components, databases, communication mechanisms and infrastructure required to build a software application.

It focuses on how different components of an application work together.

Consider a simple application.

You build a website where users can:

- Register.
- Login.
- Create posts.
- Upload images.
- Like posts.
- Comment on posts.
- Follow other users.

Initially, you might implement everything inside one Node.js application.

However, as the number of users increases, several problems can appear:

- Slow API responses.
- Database overload.
- High CPU usage.
- Memory consumption.
- Increased network traffic.
- Application downtime.
- Difficulty deploying new features.

System Design helps you solve these problems through appropriate architectural decisions.

### Example: Simple Application Architecture

```text
             Users
               |
               |
           Frontend
               |
               |
          Backend API
               |
               |
            Database
```

### Example: Scalable Application Architecture

```text
                    Users
                      |
                      |
                  CloudFront
                      |
                      |
                 Load Balancer
                      |
              -------------------
              |        |        |
              |        |        |
            API 1    API 2    API 3
              |        |        |
              -------------------
                      |
             ---------------------
             |         |         |
             |         |         |
            Redis    Database   Queue
                       |
                    Replicas
```

The second architecture introduces multiple components to improve scalability, availability and performance.

**Important:** More components do not automatically mean better architecture. Every additional component introduces operational complexity, cost and potential failure points.

---

## 3. Functional and Non-Functional Requirements

Before designing any system, understand its requirements.

There are two primary categories.

### 3.1 Functional Requirements

Functional requirements describe what the system should do.

Example: E-commerce application.

- Users can register.
- Users can log in.
- Users can browse products.
- Users can add products to carts.
- Users can place orders.
- Users can make payments.
- Admins can manage products.

These requirements define application functionality.

### 3.2 Non-Functional Requirements

Non-functional requirements describe how the system should operate.

Examples:

- Performance.
- Scalability.
- Availability.
- Reliability.
- Security.
- Maintainability.
- Fault tolerance.
- Data consistency.

Example:

```text
Functional Requirement:

Users should be able to place orders.

Non-Functional Requirement:

The order API should respond within
300 milliseconds for 95% of requests,
excluding external payment processing.
```

This example defines a performance target.

### Functional vs Non-Functional Requirements

| Feature | Functional | Non-Functional |
|---|---|---|
| Meaning | What the system does | How the system performs |
| Focus | Business functionality | System quality |
| Example | User login | Login response time |
| Example | Create order | Order availability |
| Example | Upload image | Upload performance |

---

## 4. Important System Design Metrics

System design requires measuring application performance.

The most important metrics are:

### 4.1 Latency

Latency is the time required to complete an operation.

Example:

A user requests product information.

The API responds in 200 milliseconds.

```text
Request Start
     |
     |
   200 ms
     |
     |
Response Received
```

Lower latency generally means a faster user experience.

### 4.2 Throughput

Throughput measures how much work a system completes per unit of time.

For web applications, it is commonly measured in:

- Requests per second (RPS).
- Transactions per second (TPS).
- Messages processed per second.

Example:

```text
Application Throughput:

1000 requests / second
```

This means the application processes approximately 1,000 requests every second.

### 4.3 Availability

Availability measures the proportion of time a system is operational and accessible.

It is commonly expressed as a percentage.

Example:

| Availability | Approximate Annual Downtime |
|---|---:|
| 99% | 3.65 days |
| 99.9% | 8.76 hours |
| 99.99% | 52.56 minutes |
| 99.999% | 5.26 minutes |

These are theoretical annual downtime equivalents for a 365-day year.

### 4.4 Reliability

Reliability refers to the ability of a system to operate correctly over time.

A reliable system should:

- Avoid data corruption.
- Handle failures.
- Recover from errors.
- Preserve important business operations.

### 4.5 Scalability

Scalability is the ability of a system to handle increasing workload by adding resources.

Example:

Your application initially supports 1,000 concurrent users.

Later, it needs to support 100,000 concurrent users.

Your architecture should be able to expand to accommodate the workload.

### 4.6 SLI, SLO and SLA

These concepts are frequently used in production engineering.

**SLI – Service Level Indicator**

A measurable indicator of service performance.

Example:

- Request success rate.
- API latency.
- Availability.

**SLO – Service Level Objective**

A target for an SLI.

Example:

```text
99.9% of valid API requests
should succeed over 30 days.
```

**SLA – Service Level Agreement**

An agreement defining the service level expected by customers, often with remedies or service credits if commitments are not met.

### Comparison

| Concept | Meaning |
|---|---|
| SLI | What you measure |
| SLO | What target you want to achieve |
| SLA | What you promise contractually |

---

# PART 2: MONOLITHIC VS MICROSERVICES ARCHITECTURE

## 5. What Is Monolithic Architecture?

A monolithic application is an application in which multiple business functionalities are implemented and deployed as one application unit.

Example:

You develop an e-commerce application using:

- Node.js.
- Express.
- MySQL.
- React.

Your backend contains:

- Authentication.
- User management.
- Product management.
- Orders.
- Payments.
- Notifications.

All these modules run inside one backend application.

### Monolithic Architecture

```text
             Frontend
                 |
                 |
           Backend Application
                 |
       -------------------------
       |      |       |        |
       |      |       |        |
      Auth  Users  Products  Orders
       |      |       |        |
       -------------------------
                 |
                 |
              MySQL
```

### Advantages

1. Simple initial development.
2. Easy deployment.
3. Easier local debugging.
4. Straightforward function calls between modules.
5. Lower infrastructure complexity.
6. Suitable for small and medium-sized applications.

### Disadvantages

1. Entire application may need deployment for a small change.
2. Scaling individual modules is difficult.
3. Large codebases can become difficult to maintain.
4. A failure in shared application infrastructure can affect multiple features.
5. Teams may become tightly coupled.
6. Different modules may have conflicting resource requirements.

**Important:** A monolithic application can still be well-structured.

A modular monolith separates business functionality into modules while maintaining a single deployment unit.

## 6. What Is Microservices Architecture?

Microservices architecture divides an application into smaller, independently deployable services.

Each service focuses on a specific business capability.

Example:

An e-commerce platform can contain:

- Authentication Service.
- User Service.
- Product Service.
- Order Service.
- Payment Service.
- Inventory Service.
- Notification Service.

Each service can have its own deployment lifecycle and data ownership.

### Microservices Architecture

```text
                    Frontend
                       |
                       |
                   API Gateway
                       |
       -----------------------------------
       |         |         |             |
       |         |         |             |
      Auth     Product    Order        User
    Service    Service   Service      Service
                          |
                          |
                    Payment Service
                          |
                          |
                   Notification Service
```

Each service may have its own database or logically isolated data store.

```text
Auth Service       -> Auth Database

Product Service    -> Product Database

Order Service      -> Order Database

Payment Service    -> Payment Database

User Service       -> User Database
```

### Advantages

1. Independent deployment.
2. Independent scaling.
3. Better service ownership.
4. Technology flexibility.
5. Fault isolation when properly designed.
6. Easier organizational separation for large teams.

### Disadvantages

1. Distributed communication complexity.
2. Network failures.
3. Distributed data consistency challenges.
4. More infrastructure.
5. More complicated debugging.
6. Service discovery requirements.
7. Increased monitoring requirements.
8. Distributed security management.

### Monolith vs Microservices

| Feature | Monolith | Microservices |
|---|---|---|
| Architecture | One application unit | Multiple services |
| Deployment | Usually together | Independently |
| Scaling | Application-level | Service-level |
| Communication | Usually in-process | Network-based |
| Complexity | Lower initially | Higher operationally |
| Data ownership | Often shared | Preferably service-owned |
| Testing | Easier initially | More integration complexity |
| Best for | Small and medium applications | Systems with suitable scale and organizational needs |

### When Should You Choose Microservices?

Do not select microservices simply because they are popular.

Start with a modular monolith when:

- The application is relatively small.
- The team is small.
- Business requirements are changing quickly.
- Independent service scaling is not yet necessary.
- Operational resources are limited.

Consider microservices when:

- Business domains have clear boundaries.
- Teams need independent deployments.
- Individual services have different scaling requirements.
- Independent ownership provides meaningful benefits.
- You can support distributed operations.

---

# PART 3: DISTRIBUTED SYSTEMS

## 7. What Is a Distributed System?

A distributed system is a collection of independent computers or services that communicate over a network to perform a larger task.

Each component runs independently but contributes to the overall system.

Examples:

- Netflix.
- WhatsApp.
- Amazon.
- Google Search.
- Banking platforms.
- Distributed databases.

### Example

Consider a large e-commerce platform.

```text
                 Client
                   |
                   |
               API Gateway
                   |
       ---------------------------
       |            |            |
       |            |            |
   User Service Product      Order Service
                 Service
       |            |            |
       |            |            |
    Database     Database     Database
```

These services may run on different machines.

They communicate through HTTP APIs, RPC or messaging systems.

### Important Characteristics

1. Independent execution.
2. Network-based communication.
3. Concurrent processing.
4. Partial failures.
5. Distributed data.
6. Replication.
7. Coordination between components.

## 8. Why Are Distributed Systems Difficult?

In a single application, calling a function is generally straightforward.

```javascript
const user = getUser(userId);
```

In a distributed application, calling another service involves network communication.

```javascript
const response = await fetch(
  "http://user-service:3000/users/123"
);
```

This introduces additional failure possibilities.

### Common Distributed System Problems

**1. Network Failure**

The network connection may fail.

**2. Service Failure**

A service may crash or become unavailable.

**3. Timeout**

A service may take longer than expected to respond.

**4. Duplicate Requests**

A client may retry a request after an uncertain response.

**5. Data Inconsistency**

Different services may temporarily have different versions of related data.

**6. Partial Failure**

One service may fail while the remaining services continue operating.

### Example: Order and Payment

```text
Order Service
     |
     |
Payment Service
     |
     |
Payment Database
```

Suppose:

1. The order service creates an order.
2. It calls the payment service.
3. The payment service successfully charges the customer.
4. The network connection fails before the order service receives the response.

What happened?

The payment succeeded, but the order service does not know that.

If it blindly retries the payment request, the customer might be charged twice.

This is why distributed systems need:

- Idempotency.
- Timeouts.
- Retry policies.
- Message tracking.
- Reconciliation.
- Distributed transaction patterns.

---

# PART 4: SCALABILITY AND LOAD BALANCING

## 9. Vertical Scaling

Vertical scaling means increasing the resources of an existing server.

Example:

```text
Before:

2 CPU
4 GB RAM

After:

16 CPU
64 GB RAM
```

### Advantages

- Simple implementation.
- No additional application instances required.
- Useful for workloads that benefit from larger machines.

### Disadvantages

- Hardware limits.
- Potentially expensive upgrades.
- A single server can remain a single point of failure.
- Scaling may require downtime depending on the environment.

## 10. Horizontal Scaling

Horizontal scaling means adding more servers or application instances.

Example:

```text
Before:

          Server 1


After:

          Load Balancer
           /    |    \
          /     |     \
      Server 1 Server 2 Server 3
```

### Advantages

- Higher capacity.
- Better failure isolation.
- Supports distributed workloads.
- Allows independent instance replacement.

### Disadvantages

- Requires load balancing.
- Requires shared or distributed state management.
- More complex deployment.
- Network communication becomes more important.

### Vertical vs Horizontal Scaling

| Feature | Vertical | Horizontal |
|---|---|---|
| Method | Increase server resources | Add servers |
| Complexity | Usually lower | Higher |
| Hardware limits | Yes | Less constrained by one machine |
| Failure isolation | Limited | Can be improved |
| Best use | Smaller workloads and specialized systems | Large distributed workloads |

## 11. Load Balancing

Load balancing distributes incoming traffic across multiple servers.

Example:

```text
                 Users
                   |
                   |
              Load Balancer
                   |
         ---------------------
         |         |         |
         |         |         |
       Server 1  Server 2  Server 3
```

### Common Load Balancing Algorithms

#### 11.1 Round Robin

Requests are distributed sequentially.

```text
Request 1 -> Server 1
Request 2 -> Server 2
Request 3 -> Server 3
Request 4 -> Server 1
```

#### 11.2 Weighted Round Robin

Servers receive traffic based on assigned weights.

Example:

```text
Server 1: Weight 5
Server 2: Weight 3
Server 3: Weight 2
```

Server 1 receives approximately 50% of traffic, Server 2 receives 30%, and Server 3 receives 20%, assuming equal request distribution.

#### 11.3 Least Connections

Traffic is directed to the server with the fewest active connections.

Useful when requests have different processing durations.

#### 11.4 IP Hash

The client's IP address is hashed to select a server.

This can provide a form of session affinity, but it does not guarantee robust session persistence under every topology or client network configuration.

#### 11.5 Least Response Time

Traffic is directed based on response-time and connection-related metrics, depending on the load balancer implementation.

### Load Balancer Types

| Type | Layer | Example |
|---|---|---|
| Layer 4 | Transport | AWS NLB, HAProxy TCP |
| Layer 7 | Application | AWS ALB, Nginx HTTP |

### Important Concept: Stateless Application Servers

When horizontally scaling an application, avoid keeping important user session state only in server memory.

Bad architecture:

```javascript
const sessions = {};

sessions[userId] = {
  userId,
  cart: []
};
```

If the next request reaches another server, that server may not have the session data.

Better architecture:

```text
Server 1 ----\
              \
Server 2 ------ Redis
              /
Server 3 ----/
```

Use shared state storage such as Redis or a database when appropriate.

---

# PART 5: DATABASE SCALABILITY

## 12. Database Replication

Replication means maintaining copies of data across multiple database servers.

It is used for:

- High availability.
- Read scalability.
- Disaster recovery.
- Data distribution.

### Primary-Replica Architecture

```text
                Application
                     |
                     |
                Primary DB
                     |
               Replication
                     |
          ---------------------
          |                   |
       Replica 1           Replica 2
```

The primary database handles writes.

Replicas can serve read operations, depending on the database engine and replication configuration.

### Example

Your application receives:

- 10,000 read requests.
- 500 write requests.

Instead of sending all requests to the primary database, you can distribute suitable read operations to replicas.

```text
                 Application
                      |
              -----------------
              |               |
            Writes           Reads
              |               |
          Primary DB       Read Replica
```

### Advantages

1. Read scalability.
2. Better availability options.
3. Workload distribution.
4. Additional recovery options.

### Disadvantages

1. Replication lag.
2. Increased infrastructure cost.
3. More operational complexity.
4. Potential read-after-write consistency issues.

### What Is Replication Lag?

Replication lag is the delay between a write on the primary database and its visibility on a replica.

Example:

1. User updates their profile.
2. Primary database stores the update.
3. Replica has not received the update yet.
4. User immediately requests their profile from the replica.
5. The old data may be returned.

This is an important distributed data consistency problem.

**Solution options:**

- Read recent writes from the primary.
- Use session consistency mechanisms.
- Wait for replication to catch up where supported.
- Use a suitable database consistency configuration.

---

## 13. Database Sharding

Sharding means distributing data across multiple database servers using a partitioning strategy.

Each database server stores only a subset of the total dataset.

### Without Sharding

```text
          Application
               |
               |
           Database
               |
          All User Data
```

Suppose the database stores 100 million users.

The database may eventually become difficult to scale vertically.

### With Sharding

```text
               Application
                    |
                    |
                Shard Router
                    |
          ----------------------
          |          |         |
          |          |         |
       Shard 1    Shard 2   Shard 3
          |          |         |
       Users      Users     Users
       1-33M      34-66M    67-100M
```

Each shard stores a portion of the total dataset.

### Common Sharding Strategies

#### 13.1 Range-Based Sharding

Data is distributed according to ranges.

Example:

```text
Shard 1: User IDs 1 - 100000
Shard 2: User IDs 100001 - 200000
Shard 3: User IDs 200001 - 300000
```

Advantages:

- Simple routing.
- Efficient range queries.

Disadvantages:

- Uneven data distribution.
- Hotspots if new records concentrate in one range.

#### 13.2 Hash-Based Sharding

A hash function determines which shard stores a record.

Example:

```javascript
const shardNumber = hash(userId) % 3;
```

Possible result:

```text
Shard 0
Shard 1
Shard 2
```

Advantages:

- Often distributes data more evenly.
- Reduces simple sequential range hotspots.

Disadvantages:

- Range queries become difficult.
- Changing the number of shards can require significant data redistribution.

#### 13.3 Geographic Sharding

Data is distributed based on geographical location.

Example:

```text
India Users    -> India Database
Europe Users   -> Europe Database
US Users       -> US Database
```

This can improve latency and help with data residency requirements.

However, cross-region data access and consistency become more complicated.

### What Is a Shard Key?

A shard key is the field used to determine where a record is stored.

Example:

```json
{
  "_id": "user_123",
  "country": "India",
  "name": "User A"
}
```

Possible shard keys:

- `_id`.
- `country`.
- `userId`.
- `tenantId`.

A good shard key should provide:

- Even distribution.
- High cardinality where appropriate.
- Efficient query routing.
- Low hotspot risk.

### Sharding vs Replication

| Feature | Replication | Sharding |
|---|---|---|
| Purpose | Copies data | Splits data |
| Data | Same logical dataset copied | Different subsets |
| Main benefit | Availability and read scaling | Storage and write distribution |
| Complexity | Moderate | Higher |

**Important:** Sharding and replication can be combined. A shard can itself have replicas.

---

## 14. Database Partitioning

Partitioning means dividing a large table or dataset into smaller logical or physical pieces.

It is commonly used to improve manageability and query performance.

Example:

An application stores millions of orders.

Instead of keeping all records in one unpartitioned table, you can partition by order date.

```text
Orders Table
     |
     |-- Partition 2024
     |
     |-- Partition 2025
     |
     |-- Partition 2026
```

### Types of Partitioning

**Range Partitioning**

Data is divided into ranges.

Example: Orders grouped by date.

**List Partitioning**

Data is divided based on predefined values.

Example: Country or region.

**Hash Partitioning**

Data is distributed using a hash function.

### Partitioning vs Sharding

| Feature | Partitioning | Sharding |
|---|---|---|
| Meaning | Divide data into partitions | Distribute data across database nodes |
| Scope | Often within one database system | Across multiple database systems or nodes |
| Main goal | Manage large datasets and improve query efficiency | Distribute workload and storage |
| Complexity | Depends on implementation | Usually higher |

The terms can overlap. Some database systems implement sharding through distributed partitioning.

---

# PART 6: CACHING AND REDIS

## 15. What Is Caching?

Caching means storing frequently accessed data in a faster storage layer to reduce repeated computation or database access.

Example:

Your application has a product API.

```text
GET /products/123
```

Without caching:

```text
User
  |
  |
Node.js API
  |
  |
MySQL Database
```

Every request queries the database.

With caching:

```text
User
  |
  |
Node.js API
  |
  |
Redis Cache
  |
  | Cache Miss
  |
MySQL Database
```

If the product exists in Redis, the API returns it directly.

### Benefits of Caching

1. Lower database load.
2. Faster response times.
3. Improved throughput.
4. Reduced repeated computation.
5. Better scalability for read-heavy workloads.

## 16. Caching Strategies

### 16.1 Cache-Aside

The application manages the cache.

Flow:

1. Check cache.
2. If data exists, return it.
3. Otherwise, query database.
4. Store the result in cache.
5. Return the result.

Example:

```javascript
async function getProduct(productId) {
  const cacheKey = `product:${productId}`;

  const cachedProduct = await redis.get(cacheKey);

  if (cachedProduct) {
    return JSON.parse(cachedProduct);
  }

  const product = await db.products.findByPk(productId);

  if (!product) {
    return null;
  }

  await redis.set(
    cacheKey,
    JSON.stringify(product),
    "EX",
    300
  );

  return product;
}
```

### 16.2 Write-Through Cache

When the application writes data, it updates the cache and the underlying data store as part of the write workflow.

Conceptually:

```text
Application
     |
     |
   Cache
     |
     |
  Database
```

This can help maintain cache freshness, but it introduces write-path complexity.

### 16.3 Write-Behind Cache

The application writes to the cache first, and the cache layer asynchronously writes changes to the database.

This can improve write performance in some systems.

However, it introduces durability and data-loss risks if the cache or asynchronous persistence mechanism fails.

Use only when the architecture has an appropriate durability strategy.

### 16.4 Read-Through Cache

The application requests data through the cache layer.

If the data is missing, the cache layer obtains it from the underlying data source.

### Comparison

| Strategy | Read Behavior | Write Behavior |
|---|---|---|
| Cache-Aside | Application checks cache | Application updates database and manages invalidation |
| Read-Through | Cache fetches missing data | Separate write strategy |
| Write-Through | Reads from cache | Updates cache and data store |
| Write-Behind | Reads from cache | Persists asynchronously |

## 17. Cache Invalidation

Cache invalidation means removing or updating cached data when the underlying data changes.

Example:

A product price changes from ₹1,000 to ₹1,200.

But Redis still contains ₹1,000.

Users may see outdated pricing.

Solution:

```javascript
async function updateProduct(productId, updates) {
  await db.products.update(
    updates,
    {
      where: { id: productId }
    }
  );

  await redis.del(`product:${productId}`);
}
```

The next request fetches the updated product from the database and stores it in Redis.

### Common Cache Problems

#### Cache Stampede

Many requests attempt to load the same expired key simultaneously.

Possible solutions:

- Request coalescing.
- Locking.
- TTL randomization.
- Background refresh.

#### Cache Penetration

Requests repeatedly query keys that do not exist in the database.

Possible solutions:

- Negative caching.
- Input validation.
- Bloom filters for suitable workloads.

#### Cache Avalanche

A large number of cache entries expire around the same time.

Possible solutions:

- Randomized TTL.
- Gradual expiration.
- Cache warming.

## 18. Redis Advanced Concepts

Redis is an in-memory data structure store commonly used for caching, queues, counters and real-time workloads.

### Redis Data Structures

| Data Structure | Use Case |
|---|---|
| String | Cache values and counters |
| Hash | User profiles and structured fields |
| List | Simple queues |
| Set | Unique collections |
| Sorted Set | Leaderboards and ranking |
| Stream | Append-only event streams |
| Bitmap | Compact boolean tracking |
| HyperLogLog | Approximate cardinality counting |

### Example: Redis Counter

```javascript
await redis.incr("page:views");
```

This atomically increments a counter.

### Example: Redis Sorted Set

A gaming leaderboard:

```javascript
await redis.zadd(
  "leaderboard",
  1000,
  "player1"
);

await redis.zadd(
  "leaderboard",
  1500,
  "player2"
);
```

Retrieve the highest-ranked players:

```javascript
const topPlayers = await redis.zrevrange(
  "leaderboard",
  0,
  9,
  "WITHSCORES"
);
```

### Important Redis Concepts

- TTL.
- Eviction policies.
- Persistence.
- Replication.
- Sentinel.
- Cluster mode.
- Pub/Sub.
- Streams.
- Transactions.
- Atomic operations.

---

# PART 7: MESSAGE QUEUES AND EVENT-DRIVEN ARCHITECTURE

## 19. What Is a Message Queue?

A message queue allows one application component to send a message that another component processes later.

It enables asynchronous communication.

Example:

A user places an order.

The application needs to:

- Save the order.
- Send an email.
- Generate an invoice.
- Update inventory.
- Trigger analytics.

Instead of waiting for every operation, the application can queue background tasks.

```text
                Order API
                    |
                    |
                Create Order
                    |
                    |
                 SQS Queue
                    |
          -----------------------
          |          |          |
       Email      Invoice    Inventory
       Worker     Worker      Worker
```

### Advantages

1. Decouples services.
2. Supports asynchronous processing.
3. Handles temporary workload spikes.
4. Improves request responsiveness.
5. Allows independent scaling of consumers.

## 20. Message Queue vs Message Broker

A message queue is a messaging mechanism where messages are stored for consumers.

A message broker is a broader messaging system that may provide:

- Queues.
- Topics.
- Routing.
- Message persistence.
- Acknowledgements.
- Delivery guarantees.

Examples:

- RabbitMQ.
- Apache Kafka.
- Amazon SQS.
- ActiveMQ.

## 21. Apache Kafka

Kafka is a distributed event streaming platform.

It is designed to handle large volumes of events and provide durable event storage and consumption.

### Kafka Architecture

```text
             Producers
                 |
                 |
             Kafka Topic
                 |
        -------------------
        |         |       |
     Consumer  Consumer Consumer
       A          B       C
```

### Important Kafka Concepts

**Producer**

Publishes messages to Kafka.

**Consumer**

Reads messages from Kafka.

**Topic**

A named category of events.

Example:

```text
orders
payments
notifications
```

**Partition**

A topic is divided into partitions to support parallel processing and scalability.

**Offset**

Identifies a message's position within a partition.

**Consumer Group**

A group of consumers that share the work of processing topic partitions.

### Example

```text
Topic: orders

Partition 0
  Message 1
  Message 2
  Message 3

Partition 1
  Message 4
  Message 5
  Message 6
```

Multiple consumers in a consumer group can process different partitions concurrently.

### Kafka vs RabbitMQ

| Feature | Kafka | RabbitMQ |
|---|---|---|
| Primary model | Distributed event log | Message broker |
| Message retention | Configurable retention | Queue-based retention and acknowledgement |
| Replay | Strong native support using offsets | Requires suitable queue and message design |
| Ordering | Within a partition | Queue ordering, subject to delivery and concurrency behavior |
| Common use | Event streaming and analytics | Task queues and routing |
| Consumer model | Pull-based | Supports push-based delivery and consumer acknowledgements |

Neither is universally better. Select based on message patterns, throughput, ordering, routing and operational requirements.

---

# PART 8: CAP THEOREM AND CONSISTENCY

## 22. What Is the CAP Theorem?

CAP stands for:

- Consistency.
- Availability.
- Partition Tolerance.

The CAP theorem describes a trade-off in distributed data systems when a network partition occurs.

It states that when a network partition prevents nodes from communicating reliably, a distributed system cannot simultaneously guarantee both:

1. Strong consistency.
2. Availability for every request.

Partition tolerance is necessary in a distributed system that must continue operating despite network partitions.

### 22.1 Consistency

In the CAP context, consistency means operations behave as though they occur in a single, up-to-date order.

For example:

A user updates their account balance.

Every successful read should reflect the committed update according to the system's consistency guarantees.

### 22.2 Availability

Every request received by a non-failed node receives a response, although the response may not contain the latest data.

### 22.3 Partition Tolerance

The system continues operating despite communication failures between parts of the distributed system.

### CAP Example

Suppose you have two database nodes.

```text
       Database Node A
              |
              |
        Network Failure
              X
              |
              |
       Database Node B
```

The two nodes cannot communicate.

Now a user requests a balance update.

The system has to make a choice based on its guarantees:

**Consistency-oriented behavior:**

A node may reject or delay the operation because it cannot verify the latest state.

**Availability-oriented behavior:**

A node may accept the operation, but this can produce temporarily inconsistent data.

### CAP Summary

| Choice During Partition | Behavior |
|---|---|
| CP-oriented | Prefer consistency; some requests may be rejected or unavailable |
| AP-oriented | Prefer availability; temporary inconsistency may occur |

**Important:** CAP does not mean you permanently choose only two of three properties in every situation. The trade-off becomes relevant during a network partition.

---

## 23. Consistency Models

Distributed systems use different consistency models.

### 23.1 Strong Consistency

After a successful write, subsequent reads observe that write according to the system's guarantees.

Example:

A banking balance update should not be followed by a read showing an outdated balance when the system promises strong consistency for that operation.

### 23.2 Eventual Consistency

Replicas may temporarily contain different values, but if updates stop and replication succeeds, they eventually converge.

Example:

A social media profile update may take a short time to appear everywhere.

### 23.3 Causal Consistency

Operations that are causally related are observed in an order that respects their causal relationship.

Example:

A user creates a post, and another user comments on it.

The comment should not appear before the post it references.

### 23.4 Read-Your-Writes Consistency

After a user successfully updates data, their subsequent reads should reflect their own update.

Example:

A user changes their profile picture and should see the new picture immediately.

### Comparison

| Model | Main Guarantee |
|---|---|
| Strong consistency | Reads reflect completed writes according to the consistency model |
| Eventual consistency | Replicas converge over time |
| Causal consistency | Causally related operations preserve their order |
| Read-your-writes | Users see their own successful writes |

---

# PART 9: ACID VS BASE

## 24. ACID Properties

ACID is a set of properties associated with reliable database transactions.

ACID stands for:

- Atomicity.
- Consistency.
- Isolation.
- Durability.

### 24.1 Atomicity

A transaction either completes entirely or has no effect.

Example:

Transfer ₹1,000 from Account A to Account B.

Two operations are required:

1. Deduct ₹1,000 from A.
2. Add ₹1,000 to B.

Both operations should succeed together.

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 1000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 1000
WHERE id = 2;

COMMIT;
```

If an operation fails, the application should roll back the transaction.

### 24.2 Consistency

A transaction should preserve database constraints and defined invariants.

Example:

An account balance should not become negative if the database and business rules prohibit negative balances.

### 24.3 Isolation

Concurrent transactions should behave according to the selected isolation level.

Common isolation levels:

- Read Uncommitted.
- Read Committed.
- Repeatable Read.
- Serializable.

### 24.4 Durability

Once a transaction is committed, its changes should survive failures according to the database's durability guarantees.

## 25. BASE Properties

BASE is an architectural model commonly associated with highly available distributed systems.

BASE stands for:

- Basically Available.
- Soft State.
- Eventual Consistency.

### Basically Available

The system attempts to remain responsive even when some components fail.

### Soft State

Data may change over time due to asynchronous replication or background processing, even without a new user request.

### Eventual Consistency

Data across replicas eventually converges when updates stop and replication completes.

### ACID vs BASE

| Feature | ACID | BASE |
|---|---|---|
| Focus | Transaction correctness | Availability and distributed operation |
| Consistency | Transactional consistency | Eventual consistency is common |
| Transactions | Strong transaction guarantees | Often uses asynchronous workflows |
| Typical use | Banking and financial transactions | Distributed systems and some NoSQL workloads |

**Important:** ACID and BASE are not strict opposites, and real systems may combine transactional guarantees with eventual consistency across services.

---

# PART 10: DISTRIBUTED TRANSACTIONS

## 26. What Is a Distributed Transaction?

A distributed transaction involves multiple independent services or databases.

Example:

An e-commerce order requires:

1. Order creation.
2. Inventory reservation.
3. Payment processing.
4. Shipment creation.

These operations may belong to separate services.

```text
             Order Service
                   |
          -------------------
          |        |        |
          |        |        |
      Inventory  Payment  Shipping
       Service   Service   Service
```

A normal database transaction cannot automatically guarantee atomicity across all these independent systems.

There are several approaches.

## 27. Two-Phase Commit (2PC)

Two-Phase Commit is a distributed transaction protocol.

It coordinates participating systems through two phases.

### Phase 1: Prepare

The coordinator asks all participants whether they can commit.

```text
Coordinator
    |
    |-- Prepare
    |
---------------------
|         |         |
DB A     DB B      DB C
```

Each participant prepares its transaction and responds.

### Phase 2: Commit or Abort

If all participants vote to commit, the coordinator instructs them to commit.

If any participant cannot commit, the transaction is aborted.

```text
Coordinator
    |
    |-- Commit
    |
---------------------
|         |         |
DB A     DB B      DB C
```

### Advantages

- Provides coordinated transaction semantics.
- Useful in environments requiring strong distributed transaction guarantees.

### Disadvantages

- Coordination overhead.
- Potential blocking if the coordinator fails.
- More complicated operational behavior.
- Not suitable for every microservices architecture.

---

## 28. Saga Pattern

The Saga Pattern manages a distributed business transaction through a sequence of local transactions.

If a later step fails, previously completed steps can be compensated.

Example:

```text
Create Order
     |
     |
Reserve Inventory
     |
     |
Process Payment
     |
     |
Create Shipment
```

Suppose payment fails.

The system can execute compensating actions:

```text
Payment Failed
      |
      |
Release Inventory
      |
      |
Cancel Order
```

### Saga Components

**Local Transaction**

A transaction executed by one service.

**Compensating Transaction**

A business operation that reverses or offsets a previous operation.

### Example

| Step | Operation | Compensation |
|---|---|---|
| 1 | Create order | Cancel order |
| 2 | Reserve inventory | Release inventory |
| 3 | Process payment | Refund payment |
| 4 | Create shipment | Cancel shipment where possible |

### Saga Implementation Styles

**Choreography**

Services react to events and trigger the next step.

```text
Order Created
     |
     |
Inventory Reserved
     |
     |
Payment Completed
     |
     |
Shipment Created
```

**Orchestration**

A central orchestrator controls the workflow.

```text
           Saga Orchestrator
             /    |    \
            /     |     \
         Order Inventory Payment
         Service Service  Service
```

### 2PC vs Saga

| Feature | 2PC | Saga |
|---|---|---|
| Approach | Coordinated commit | Sequence of local transactions |
| Consistency | Distributed atomic commit when correctly implemented | Eventual business consistency |
| Locking | May hold resources while waiting | Usually avoids global transaction locks |
| Failure handling | Commit or abort | Compensating transactions |
| Common use | Distributed transactional systems | Microservices business workflows |

---

# PART 11: RATE LIMITING

## 29. What Is Rate Limiting?

Rate limiting controls how many requests a client can make within a specified period.

Example:

```text
Maximum Requests:

100 requests per minute
per user.
```

If a user exceeds the limit, the API may return:

```http
HTTP/1.1 429 Too Many Requests
```

### Why Is Rate Limiting Important?

1. Prevents API abuse.
2. Reduces excessive server load.
3. Helps protect against brute-force attacks.
4. Controls resource consumption.
5. Provides fair resource allocation.

## 30. Rate Limiting Algorithms

### 30.1 Fixed Window Counter

The system counts requests within fixed time windows.

Example:

```text
Window: 10:00 - 10:01

Maximum: 100 requests
```

At 10:01, the counter resets.

**Problem:** A user can send 100 requests just before the window ends and another 100 immediately after it starts.

This can create a burst of 200 requests in a short interval.

### 30.2 Sliding Window Log

Stores timestamps of recent requests.

Only requests within the rolling time interval are counted.

This provides precise limiting but can consume considerable storage.

### 30.3 Sliding Window Counter

Combines current and previous window counts using a weighted estimate.

It reduces fixed-window boundary problems with less storage than a full request log.

### 30.4 Token Bucket

Tokens are added to a bucket at a fixed rate.

Each request consumes a token.

If no tokens remain, the request is rejected or delayed.

```text
          Token Bucket
          Capacity: 10

          [1][2][3][4]
          [5][6][7][8]
          [9][10]

               |
               |
           API Request
               |
          Consume Token
```

Token Bucket allows controlled bursts while limiting long-term request rates.

### 30.5 Leaky Bucket

Requests enter a queue and are processed at a controlled rate.

It smooths traffic bursts.

### Comparison

| Algorithm | Main Characteristic |
|---|---|
| Fixed Window | Simple time-window counter |
| Sliding Window Log | Accurate rolling request count |
| Sliding Window Counter | Efficient approximation of rolling limits |
| Token Bucket | Allows controlled bursts |
| Leaky Bucket | Smooths outgoing request rate |

---

## 31. Implementing Rate Limiting in Node.js

For production applications running multiple instances, use shared storage such as Redis rather than relying only on in-memory counters.

Example using Express and `express-rate-limit`:

```bash
npm install express express-rate-limit
```

```javascript
const express = require("express");

const rateLimit = require("express-rate-limit");

const app = express();

const limiter = rateLimit({
  windowMs: 60 * 1000,

  limit: 100,

  standardHeaders: "draft-8",

  legacyHeaders: false,

  message: {
    error: "Too many requests. Please try again later."
  }
});

app.use("/api", limiter);

app.get("/api/users", (req, res) => {
  res.json({
    message: "Users fetched successfully"
  });
});

app.listen(3000);
```

This is a basic single-process example.

For a horizontally scaled deployment, configure a shared rate-limit store, such as a compatible Redis store, and ensure client IP identification is correct behind your trusted proxy infrastructure.

### Important Rate Limiting Considerations

- Limit by IP address.
- Limit by authenticated user ID.
- Apply stricter limits to login endpoints.
- Apply separate limits to expensive operations.
- Consider tenant-level quotas.
- Return HTTP 429.
- Provide retry information when appropriate.
- Avoid trusting arbitrary client-supplied identity headers.

---

# PART 12: API GATEWAY ARCHITECTURE

## 32. What Is an API Gateway?

An API Gateway is an entry point that routes client requests to backend services.

It can handle:

- Request routing.
- Authentication.
- Authorization.
- Rate limiting.
- Logging.
- Request validation.
- API versioning.
- Response transformation.

### Without API Gateway

```text
Frontend
   |
   |-- User Service
   |
   |-- Product Service
   |
   |-- Order Service
   |
   |-- Payment Service
```

The frontend must know about multiple backend services.

### With API Gateway

```text
              Frontend
                  |
                  |
              API Gateway
                  |
       -------------------------
       |       |       |       |
       |       |       |       |
      User  Product  Order  Payment
    Service Service Service Service
```

The frontend communicates through a common entry point.

### Example Routes

```text
GET /api/users
    -> User Service

GET /api/products
    -> Product Service

POST /api/orders
    -> Order Service

POST /api/payments
    -> Payment Service
```

### API Gateway vs Load Balancer

| Feature | API Gateway | Load Balancer |
|---|---|---|
| Main purpose | API management and routing | Traffic distribution |
| Authentication integration | Common | Available through configuration/integrations |
| Rate limiting | Common capability | Depends on implementation |
| Request transformation | Often supported | Varies |
| Service routing | Yes | Yes |
| Main use | API entry point | Distribute traffic |

They can be used together.

---

# PART 13: FAULT TOLERANCE AND RESILIENCE PATTERNS

## 33. What Is Fault Tolerance?

Fault tolerance is the ability of a system to continue performing its required functions when components fail.

Example:

You have three application instances.

One instance crashes.

The load balancer detects the unhealthy instance and routes requests to the remaining healthy instances.

```text
              Load Balancer
                   |
          -------------------
          |        |        |
          |        |        |
        Server 1 Server 2 Server 3
          |        |        |
         UP       DOWN      UP
```

The application can continue serving requests if the remaining capacity is sufficient.

## 34. Circuit Breaker Pattern

The Circuit Breaker Pattern prevents repeated calls to a service that is currently failing.

Example:

Your Order Service depends on Payment Service.

Payment Service becomes unavailable.

Without a circuit breaker, Order Service may continue making thousands of failing requests.

This wastes resources and can make the failure worse.

### Circuit Breaker States

There are three primary states.

**1. Closed**

Requests are allowed through.

```text
Order Service
      |
      |
Payment Service
```

**2. Open**

Requests are blocked temporarily.

```text
Order Service
      |
      |
 Circuit Breaker
      X
 Payment Service
```

**3. Half-Open**

A limited number of test requests are allowed to determine whether the service has recovered.

```text
Order Service
      |
      |
 Circuit Breaker
      |
   Test Request
      |
 Payment Service
```

### State Transitions

```text
       CLOSED
          |
          | Failures exceed threshold
          |
          v
        OPEN
          |
          | Recovery timeout
          |
          v
      HALF-OPEN
          |
       ----------
       |        |
     Success  Failure
       |        |
       v        v
     CLOSED   OPEN
```

### Advantages

- Prevents repeated unnecessary requests.
- Reduces cascading failures.
- Gives failing services time to recover.
- Improves overall system resilience.

---

## 35. Retry and Exponential Backoff

Retry means attempting an operation again after a failure.

Example:

Your application calls an external payment provider.

The request times out.

You may retry if the operation is safe to retry.

### Basic Retry

```javascript
async function fetchData() {
  for (let attempt = 1; attempt <= 3; attempt++) {
    try {
      const response = await fetch(
        "https://api.example.com/data"
      );

      if (!response.ok) {
        throw new Error("Request failed");
      }

      return await response.json();

    } catch (error) {
      if (attempt === 3) {
        throw error;
      }
    }
  }
}
```

This example is intentionally simple. A production retry policy should distinguish retryable failures from permanent failures and enforce request timeouts.

### Exponential Backoff

Instead of retrying immediately, increase the delay between attempts.

Example:

```text
Attempt 1: Immediate
Attempt 2: Wait 1 second
Attempt 3: Wait 2 seconds
Attempt 4: Wait 4 seconds
Attempt 5: Wait 8 seconds
```

Formula:

\[
Delay = BaseDelay \times 2^{Attempt}
\]

A practical system usually adds jitter.

### Exponential Backoff with Jitter

Jitter introduces randomness into retry delays.

This helps prevent many clients from retrying simultaneously.

Example:

```javascript
function getRetryDelay(attempt) {
  const baseDelay = 1000;

  const maxDelay = 10000;

  const exponentialDelay =
    Math.min(maxDelay, baseDelay * 2 ** attempt);

  return Math.random() * exponentialDelay;
}
```

### Important Retry Rules

1. Retry only appropriate errors.
2. Set maximum retry attempts.
3. Use timeouts.
4. Use exponential backoff.
5. Add jitter.
6. Avoid retrying non-idempotent operations without protection.
7. Respect provider rate limits and retry instructions.

---

# PART 14: IDEMPOTENCY

## 36. What Is Idempotency?

An operation is idempotent if repeating it produces the same intended result as performing it once.

Example:

```http
PUT /users/123
```

Setting a user's name to the same value repeatedly can be idempotent.

However:

```http
POST /payments
```

May create a new payment every time it is executed.

If the client retries because the response was lost, duplicate payments can occur.

### Why Is Idempotency Important?

Distributed systems experience:

- Network timeouts.
- Duplicate messages.
- Client retries.
- Worker retries.
- Uncertain responses.

Idempotency prevents duplicate business effects.

## 37. Idempotency Key

An idempotency key uniquely identifies a logical operation.

Example:

```http
POST /payments
Idempotency-Key: order-payment-123
```

If the client sends the same operation again with the same key, the server should return the previously stored result rather than creating a second payment.

### Example Database Table

```sql
CREATE TABLE idempotency_keys (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    idempotency_key VARCHAR(255) NOT NULL UNIQUE,
    response_body JSON,
    status VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Simplified Node.js Example

```javascript
async function processPayment(idempotencyKey, amount) {
  const existing = await db.idempotencyKeys.findOne({
    where: {
      idempotency_key: idempotencyKey
    }
  });

  if (existing?.status === "COMPLETED") {
    return existing.response_body;
  }

  // Real payment processing should use a transaction,
  // concurrency-safe key reservation and provider support.

  const payment = await paymentProvider.charge({
    amount,
    idempotencyKey
  });

  await db.idempotencyKeys.upsert({
    idempotency_key: idempotencyKey,
    response_body: payment,
    status: "COMPLETED"
  });

  return payment;
}
```

This is a conceptual example. A production implementation must handle concurrent requests using database uniqueness constraints, atomic state transitions, transaction boundaries and appropriate recovery for operations that are still processing.

**Important:** Idempotency is essential for payment processing, order creation and background message consumers.

---

# PART 15: DISTRIBUTED LOCKING

## 38. What Is a Distributed Lock?

A distributed lock ensures that only one process or service instance performs a particular critical operation at a time, according to the lock protocol.

Example:

Suppose two workers attempt to process the same job.

```text
Worker 1 ----\
              \
               Shared Resource
              /
Worker 2 ----/
```

Without coordination, both workers might perform the operation.

A distributed lock can prevent concurrent execution.

### Common Use Cases

- Scheduled jobs.
- Inventory updates.
- Resource allocation.
- Payment reconciliation.
- Background task coordination.
- Preventing duplicate processing.

## 39. Redis Distributed Lock

Redis supports atomic operations that can be used to implement simple locks.

Example:

```bash
SET lock:order:123 unique-token NX PX 10000
```

Explanation:

- `SET`: Set a key.
- `NX`: Only if the key does not exist.
- `PX 10000`: Set a 10-second expiration.

If the command succeeds, the caller has acquired the lock.

### Important: Safe Lock Release

Do not blindly delete the lock.

Another process might have acquired a new lock after the original lock expired.

The owner should delete the key only if the stored token matches its own unique token.

Conceptual Lua script:

```lua
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
else
    return 0
end
```

### Distributed Locking Challenges

- Lock expiration.
- Network partitions.
- Process pauses.
- Ownership verification.
- Fencing tokens.
- Clock assumptions.
- Lock renewal.

**Important:** Distributed locks are not a substitute for database constraints or atomic business operations. For critical financial or inventory operations, use database transactions, conditional updates and other correctness mechanisms as appropriate.

---

# PART 16: DATABASE INDEXING AT SCALE

## 40. What Is a Database Index?

A database index is a data structure that helps the database locate records without scanning every row.

Example:

```sql
SELECT *
FROM users
WHERE email = 'user@example.com';
```

Without an appropriate index, the database may perform a full table scan.

Create an index:

```sql
CREATE INDEX idx_users_email
ON users(email);
```

The database can use the index to find matching records more efficiently.

## 41. Types of Database Indexes

### 41.1 Primary Index

An index associated with the primary key.

Example:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100)
);
```

### 41.2 Unique Index

Ensures indexed values are unique.

```sql
CREATE UNIQUE INDEX idx_users_email
ON users(email);
```

### 41.3 Composite Index

An index containing multiple columns.

```sql
CREATE INDEX idx_orders_user_date
ON orders(user_id, created_at);
```

This can help queries such as:

```sql
SELECT *
FROM orders
WHERE user_id = 123
ORDER BY created_at DESC;
```

The usefulness of a composite index depends on query shape, column order and database optimizer behavior.

### 41.4 Full-Text Index

Designed for text search.

Example:

```sql
CREATE FULLTEXT INDEX idx_products_description
ON products(description);
```

Supported features and syntax vary by database engine.

### 41.5 Spatial Index

Used for spatial and geographical data.

## 42. B-Tree Index

Many relational databases use B-tree or B+ tree structures for common indexes.

Simplified representation:

```text
                 [50]
                /    \
               /      \
          [20, 30]   [70, 90]
```

The database traverses the tree to locate values efficiently.

### Why Not Index Every Column?

Indexes have costs.

1. Additional storage.
2. Slower writes.
3. Additional maintenance.
4. Potentially inefficient query plans.
5. More complex schema management.

### Database Performance Best Practices

- Use `EXPLAIN`.
- Index frequently queried columns.
- Analyze composite indexes.
- Avoid unnecessary indexes.
- Avoid selecting unnecessary columns.
- Use pagination carefully.
- Monitor slow queries.
- Avoid N+1 queries.
- Use connection pooling.

---

# PART 17: DESIGNING A URL SHORTENER

## 43. What Is a URL Shortener?

A URL shortener converts long URLs into short URLs.

Example:

Original URL:

```text
https://example.com/products/electronics/mobile/iphone
```

Short URL:

```text
https://short.ly/aB12x
```

When users open the short URL, they are redirected to the original URL.

### Functional Requirements

1. Create a short URL.
2. Redirect users.
3. Prevent unwanted duplicate mappings according to the chosen business rules.
4. Track link analytics.
5. Support expiration.
6. Support optional custom aliases.

### Non-Functional Requirements

- Low redirect latency.
- High availability.
- Scalability.
- Reliable mapping storage.
- Abuse prevention.
- Security.

## 44. High-Level Architecture

```text
                 Users
                   |
                   |
                API Gateway
                   |
             ----------------
             |              |
             |              |
        URL Creation     Redirect API
             |              |
             |              |
             -------- Redis
                    |
                    |
                  Database
```

### Database Schema

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

### Creating Short Codes

One approach is Base62 encoding.

Base62 uses:

```text
0123456789
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

It contains 62 characters.

Suppose you encode a numeric identifier:

```text
123456 -> W7E
```

The actual result depends on the encoding implementation.

Another approach is generating random codes.

### Base62 Concept

```javascript
const characters =
  "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";

function encodeBase62(number) {
  let result = "";

  while (number > 0) {
    const remainder = number % 62;

    result = characters[remainder] + result;

    number = Math.floor(number / 62);
  }

  return result || "0";
}

console.log(encodeBase62(123456));
```

### Redirect Flow

1. User opens short URL.
2. API receives short code.
3. API checks Redis.
4. If cache miss, query database.
5. Retrieve original URL.
6. Cache the mapping.
7. Return HTTP redirect.

```javascript
app.get("/:code", async (req, res) => {
  const { code } = req.params;

  let originalUrl = await redis.get(`url:${code}`);

  if (!originalUrl) {
    const record = await db.urls.findOne({
      where: {
        short_code: code
      }
    });

    if (!record) {
      return res.status(404).send("URL not found");
    }

    if (
      record.expires_at &&
      new Date(record.expires_at) <= new Date()
    ) {
      return res.status(410).send("URL expired");
    }

    originalUrl = record.original_url;

    await redis.set(
      `url:${code}`,
      originalUrl,
      "EX",
      3600
    );
  }

  return res.redirect(302, originalUrl);
});
```

This example assumes your application validates URLs when creating mappings and uses appropriate redirect protections.

### Important System Design Decisions

- Random code vs sequential ID.
- Collision handling.
- Database uniqueness.
- Cache TTL.
- Link expiration.
- Abuse detection.
- Analytics storage.
- Redirect type: 301 vs 302.

### Scaling Considerations

If your URL shortener receives millions of redirects:

- Use Redis caching.
- Use database read replicas.
- Partition or shard mapping data when necessary.
- Use asynchronous analytics processing.
- Use CDN or edge functionality where appropriate.
- Apply rate limiting.

---

# PART 18: DESIGNING A WHATSAPP-LIKE APPLICATION

## 45. Functional Requirements

A messaging application should support:

1. User registration.
2. Login.
3. One-to-one messaging.
4. Group messaging.
5. Real-time message delivery.
6. Online/offline status.
7. Message history.
8. Read receipts.
9. Delivery receipts.
10. Media sharing.
11. Push notifications.

## 46. High-Level Architecture

```text
                  Mobile / Web Clients
                           |
                           |
                      API Gateway
                           |
                ----------------------
                |                    |
                |                    |
           Auth Service        WebSocket Gateway
                                     |
                                     |
                              Message Service
                                     |
                   ------------------------------
                   |             |              |
                   |             |              |
                Message DB    Redis          Queue
                                |
                                |
                         Presence / Sessions
```

Additional services:

```text
Media Service
Notification Service
Group Service
User Service
```

## 47. Why WebSockets?

Traditional HTTP follows a request-response model.

The client sends a request and the server responds.

For real-time messaging, continuously polling the server is inefficient.

WebSockets provide a persistent, bidirectional communication channel.

```text
Client  <----------------> Server
          WebSocket
```

Both sides can send messages over the established connection.

### Example WebSocket Server in Node.js

Install:

```bash
npm install ws
```

Server:

```javascript
const WebSocket = require("ws");

const server = new WebSocket.Server({
  port: 8080
});

server.on("connection", (socket) => {
  console.log("Client connected");

  socket.on("message", (message) => {
    console.log("Received:", message.toString());

    socket.send(
      JSON.stringify({
        type: "message_received",
        message: message.toString()
      })
    );
  });

  socket.on("close", () => {
    console.log("Client disconnected");
  });
});
```

This is a basic WebSocket example, not a complete secure messaging implementation.

## 48. Message Delivery Flow

Suppose User A sends a message to User B.

```text
User A
   |
   |
WebSocket Gateway
   |
   |
Message Service
   |
   |
Message Database
   |
   |
Recipient Connection Lookup
   |
   |
User B WebSocket
```

If User B is online:

- Deliver through the active connection.

If User B is offline:

- Persist the message.
- Queue or retain delivery work.
- Send a push notification where appropriate.
- Deliver messages when the user reconnects.

### Message Status

```text
SENT
  |
  |
DELIVERED
  |
  |
READ
```

These statuses represent different events.

- Sent: The sender's system accepted the message.
- Delivered: The recipient's device or service acknowledged delivery, depending on the implementation.
- Read: The recipient opened or acknowledged the message.

## 49. Group Messaging

Suppose a group has 1,000 members.

A user sends one message.

The system needs to deliver it to the group members.

There are two common approaches.

### Fan-Out on Write

The system distributes message delivery work when the message is created.

Advantages:

- Faster recipient reads.
- Delivery work is prepared in advance.

Disadvantages:

- High write amplification for large groups.
- More storage and processing.

### Fan-Out on Read

The system stores the message once and retrieves relevant messages when members read the group.

Advantages:

- Less duplicated message storage.
- Efficient for some very large groups.

Disadvantages:

- More work during reads.
- Potentially higher read latency.

### Hybrid Approach

Use fan-out on write for smaller groups and fan-out on read or hybrid strategies for very large groups.

## 50. Media Sharing

Do not store large image and video files directly inside the primary message database.

A better architecture:

```text
User
 |
 |
Media API
 |
 |
Object Storage
 |
 |
S3
 |
 |
CDN
 |
 |
Recipient
```

The message database stores metadata:

```json
{
  "messageId": "msg_123",
  "senderId": "user_1",
  "receiverId": "user_2",
  "type": "image",
  "mediaUrl": "https://cdn.example.com/image.jpg",
  "createdAt": "2026-11-25T12:00:00Z"
}
```

For sensitive applications, media encryption and access control must be designed carefully.

### Scaling Challenges

- Millions of concurrent WebSocket connections.
- Message ordering.
- Offline delivery.
- Duplicate messages.
- Group fan-out.
- Presence management.
- Media storage.
- Push notification delivery.
- Encryption.
- Multi-device synchronization.

---

# PART 19: DESIGNING A YOUTUBE-LIKE APPLICATION

## 51. Functional Requirements

A video platform should support:

1. User registration.
2. Video upload.
3. Video processing.
4. Video playback.
5. Search.
6. Likes and comments.
7. Subscriptions.
8. Recommendations.
9. View tracking.
10. Video management.

## 52. High-Level Architecture

```text
                  Users
                    |
                    |
                 Frontend
                    |
                    |
                API Gateway
                    |
          ---------------------
          |                   |
          |                   |
      Video Service       User Service
          |
          |
      Upload Service
          |
          |
      Object Storage
          |
          |
      S3 Bucket
          |
          |
    Processing Queue
          |
          |
   Video Processing Workers
          |
          |
      Transcoding
          |
          |
   Multiple Resolutions
          |
          |
      CDN Delivery
          |
          |
          Users
```

## 53. Video Upload Process

Suppose a user uploads a video.

The system performs:

1. User requests upload authorization.
2. Backend creates an upload record.
3. Backend generates a pre-signed upload URL.
4. Client uploads the video to object storage.
5. Storage event triggers processing.
6. Video processing workers transcode the file.
7. Generate multiple resolutions.
8. Store processed files.
9. Update video status.
10. Deliver video through a CDN.

### Video Transcoding

A video may be available in several resolutions:

```text
Original Video
      |
      |
 Transcoding
      |
 -------------
 |     |     |
480p  720p  1080p
```

Different users have different network speeds and screen sizes.

Multiple resolutions allow adaptive video delivery.

### Adaptive Bitrate Streaming

Adaptive bitrate streaming allows the player to switch between video quality levels based on network conditions.

Common technologies:

- HLS.
- MPEG-DASH.

The player selects appropriate segments and quality levels.

## 54. Video Storage Architecture

Use object storage for large media files.

```text
                 Video Service
                      |
                      |
                  S3 Storage
                      |
                      |
                  CloudFront
                      |
                      |
                    Users
```

Benefits:

- Scalable object storage.
- CDN caching.
- Reduced application server load.
- Better global content delivery.

## 55. Video Platform Scaling Challenges

- Large upload sizes.
- Transcoding workloads.
- Storage costs.
- Video delivery bandwidth.
- Search indexing.
- Recommendation processing.
- View-count accuracy.
- CDN cache management.
- Copyright and content moderation.

For a large platform, video processing should generally happen asynchronously rather than blocking the upload API request.

---

# PART 20: DESIGNING AN E-COMMERCE SYSTEM

## 56. Functional Requirements

An e-commerce system should support:

1. User registration.
2. Authentication.
3. Product browsing.
4. Product search.
5. Shopping cart.
6. Checkout.
7. Payment processing.
8. Inventory management.
9. Order management.
10. Notifications.
11. Refunds.
12. Admin management.

## 57. High-Level Architecture

```text
                    Users
                      |
                      |
                 API Gateway
                      |
       ---------------------------------
       |        |        |       |      |
       |        |        |       |      |
      Auth   Product   Cart    Order  Search
     Service Service  Service Service Service
                                 |
                         -----------------
                         |               |
                         |               |
                     Inventory        Payment
                      Service         Service
                         |
                         |
                     Database
                         |
                         |
                       Queue
                         |
                         |
                    Notification
                       Service
```

## 58. Database Design

Possible tables:

### Users

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(255) UNIQUE
);
```

### Products

```sql
CREATE TABLE products (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255),
    price DECIMAL(10,2),
    stock_quantity INT
);
```

### Orders

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    total_amount DECIMAL(10,2),
    status VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Order Items

```sql
CREATE TABLE order_items (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    order_id BIGINT,
    product_id BIGINT,
    quantity INT,
    price DECIMAL(10,2)
);
```

## 59. Order Processing Workflow

```text
User Checkout
     |
     |
Create Order
     |
     |
Reserve Inventory
     |
     |
Process Payment
     |
     |
Confirm Order
     |
     |
Send Notification
     |
     |
Prepare Shipment
```

A production system needs to handle failures at every step.

For example:

Payment succeeds, but inventory confirmation fails.

You need a reliable business workflow, potentially using a Saga Pattern.

### Important E-commerce Concepts

**Inventory Reservation**

Temporarily reserve stock before finalizing an order.

**Optimistic Locking**

Use a version number or conditional update to prevent conflicting updates.

**Idempotent Payment Processing**

Prevent duplicate charges when requests are retried.

**Transactional Outbox**

Store a business change and the corresponding event in the same local database transaction, then publish the event asynchronously.

**Eventual Consistency**

Different services may reflect an order update at slightly different times.

---

# PART 21: DESIGNING A NOTIFICATION SYSTEM

## 60. What Is a Notification System?

A notification system delivers messages to users through different channels.

Examples:

- Email.
- SMS.
- Push notifications.
- In-app notifications.
- WhatsApp messages.

### Functional Requirements

1. Receive notification requests.
2. Select delivery channel.
3. Apply user preferences.
4. Send notifications.
5. Track delivery status.
6. Retry failed deliveries.
7. Avoid duplicate notifications.
8. Support scheduled notifications.

## 61. Architecture

```text
              Application
                   |
                   |
            Notification API
                   |
                   |
                Queue
                   |
          --------------------
          |        |         |
          |        |         |
        Email     SMS       Push
        Worker   Worker    Worker
          |        |         |
          |        |         |
        Email    SMS       Push
       Provider Provider  Provider
```

### Notification Flow

1. Order service publishes an event.
2. Notification service receives the event.
3. It checks user preferences.
4. It selects the appropriate channel.
5. It creates a notification job.
6. Worker processes the job.
7. Provider sends the notification.
8. Delivery status is recorded.

### Why Use a Queue?

Suppose 100,000 users need notifications.

Sending every notification synchronously from the order API would make the API slow and potentially unstable.

A queue allows the system to process notifications in the background.

### Important Features

- Retry policy.
- Dead Letter Queue.
- Idempotency.
- Rate limiting.
- Delivery tracking.
- Template management.
- Provider failover.
- User preferences.
- Scheduled delivery.

---

# PART 22: ADVANCED ARCHITECTURAL PATTERNS

## 62. Event-Driven Architecture

Event-Driven Architecture (EDA) is an architecture in which components communicate through events.

An event represents something that has happened.

Examples:

- UserRegistered.
- OrderCreated.
- PaymentCompleted.
- ProductUpdated.
- ShipmentDelivered.

### Architecture

```text
          Order Service
                |
                |
           OrderCreated
                |
                |
           Event Broker
          /      |      \
         /       |       \
    Inventory  Payment  Notification
     Service   Service    Service
```

### Advantages

- Loose coupling.
- Asynchronous communication.
- Independent service scaling.
- Easier integration of new event consumers.

### Disadvantages

- Eventual consistency.
- Duplicate event handling.
- Difficult debugging.
- Event ordering challenges.
- More complicated testing.

## 63. Transactional Outbox Pattern

Suppose an Order Service must:

1. Save an order.
2. Publish an `OrderCreated` event.

A dangerous implementation is:

```text
Save Order
    |
    |
Publish Event
```

What happens if the database transaction succeeds but event publishing fails?

The order exists, but other services never receive the event.

### Transactional Outbox Solution

Save the order and an event record in the same database transaction.

```text
           Database Transaction
             /            \
            /              \
       Save Order      Save Outbox Event
            \              /
             \            /
                 COMMIT
                   |
                   |
             Outbox Worker
                   |
                   |
               Event Broker
```

The outbox worker publishes pending events.

Consumers must still handle duplicate event delivery idempotently.

## 64. CQRS Pattern

CQRS stands for Command Query Responsibility Segregation.

It separates operations that change data from operations that read data.

### Traditional Architecture

```text
Application
     |
     |
 Database
```

The same data model supports reads and writes.

### CQRS Architecture

```text
              Application
                    |
              -------------
              |           |
              |           |
           Commands     Queries
              |           |
              |           |
          Write Model  Read Model
              |           |
              |           |
          Write DB      Read DB
```

### Command

A command changes state.

Example:

```text
CreateOrder
UpdateProduct
CancelOrder
```

### Query

A query retrieves data.

Example:

```text
GetOrder
GetProducts
GetUserProfile
```

### Benefits

- Read and write workloads can scale independently.
- Read models can be optimized for specific queries.
- Complex business logic can be separated from query logic.

### Disadvantages

- More components.
- Data synchronization.
- Eventual consistency in many implementations.
- Increased operational complexity.

**Important:** CQRS does not require microservices or event sourcing. It can be implemented inside a monolithic application.

## 65. Event Sourcing

Event Sourcing stores changes as a sequence of events rather than storing only the latest state.

Example:

Instead of storing only:

```json
{
  "accountId": 123,
  "balance": 5000
}
```

Store events:

```text
AccountCreated
MoneyDeposited(10000)
MoneyWithdrawn(3000)
MoneyWithdrawn(2000)
```

Current balance:

```text
10000 - 3000 - 2000 = 5000
```

### Architecture

```text
Commands
   |
   |
Event Handler
   |
   |
Event Store
   |
   |
Events
   |
   |
Projection
   |
   |
Read Model
```

### Advantages

- Complete event history.
- Auditability.
- Ability to reconstruct state.
- Useful for historical analysis.

### Disadvantages

- Event schema evolution.
- Storage growth.
- Replay complexity.
- Eventual consistency.
- More complicated application design.

---

# PART 23: SYSTEM DESIGN INTERVIEW APPROACH

## 66. How to Approach a System Design Interview

System design interviews are not primarily about writing code.

They evaluate your ability to make architectural decisions and explain trade-offs.

Follow a structured approach.

### Step 1: Clarify Requirements

Ask:

- Who are the users?
- What are the core features?
- What is the expected traffic?
- How much data will be stored?
- What is the expected availability?
- Are strong consistency guarantees required?

### Step 2: Define Functional Requirements

Write down the main features.

Example: URL Shortener.

- Create short URL.
- Redirect.
- Track analytics.
- Expire links.

### Step 3: Define Non-Functional Requirements

Example:

- Low redirect latency.
- High availability.
- Scalable storage.
- Reliable redirects.

### Step 4: Estimate Scale

Estimate:

- Daily active users.
- Requests per second.
- Read/write ratio.
- Storage requirements.
- Bandwidth.

### Step 5: Design High-Level Architecture

Identify:

- Frontend.
- API Gateway.
- Application services.
- Database.
- Cache.
- Message queue.
- Storage.

### Step 6: Design Database Schema

Define:

- Entities.
- Relationships.
- Primary keys.
- Indexes.
- Constraints.

### Step 7: Identify Bottlenecks

Potential bottlenecks:

- Database reads.
- Database writes.
- Network bandwidth.
- CPU-intensive processing.
- External APIs.

### Step 8: Introduce Scaling Solutions

Examples:

- Redis.
- Read replicas.
- Sharding.
- Load balancing.
- Queues.
- CDN.
- Horizontal scaling.

### Step 9: Discuss Failure Scenarios

Consider:

- Database failure.
- Network failure.
- Service timeout.
- Duplicate messages.
- Regional outage.

### Step 10: Explain Trade-offs

For every major design decision, explain:

- Why you selected it.
- What benefit it provides.
- What limitations it introduces.
- What alternative approaches exist.

---

# PART 24: COMMON SYSTEM DESIGN MISTAKES

## 67. Mistakes You Should Avoid

| Mistake | Better Approach |
|---|---|
| Using microservices for every application | Start with modular architecture |
| Keeping sessions in application memory | Use shared session storage |
| Querying databases for every request | Introduce suitable caching |
| Ignoring database indexes | Analyze query plans |
| Making every API synchronous | Use asynchronous processing where appropriate |
| Retrying requests indefinitely | Use bounded retries and backoff |
| Ignoring duplicate messages | Implement idempotent consumers |
| Assuming network calls always succeed | Use timeouts and failure handling |
| Storing large files in relational databases without reason | Use object storage where appropriate |
| Ignoring observability | Implement metrics, logs and tracing |
| Using distributed locks everywhere | Prefer atomic database operations when possible |
| Ignoring data consistency | Define consistency requirements explicitly |
| Sharding too early | Optimize and measure before sharding |
| Adding too many infrastructure components | Choose the simplest architecture that meets requirements |

---

# PART 25: SYSTEM DESIGN INTERVIEW QUESTIONS AND ANSWERS

## Beginner-Level Questions

### Q1. What is System Design?

System Design is the process of defining the architecture and components required to build a software system.

### Q2. What is scalability?

Scalability is the ability of a system to handle increased workload by adding resources.

### Q3. What is availability?

Availability measures how much time a system remains accessible and operational.

### Q4. What is latency?

Latency is the time required to complete an operation.

### Q5. What is throughput?

Throughput is the amount of work a system processes per unit of time.

### Q6. What is a monolithic architecture?

An architecture in which application functionality is deployed as one application unit.

### Q7. What is microservices architecture?

An architecture that divides an application into independently deployable services organized around business capabilities.

### Q8. What is horizontal scaling?

Adding more servers or application instances to handle increased workload.

### Q9. What is vertical scaling?

Increasing the resources of an existing server.

### Q10. What is load balancing?

Distributing incoming traffic across multiple servers or targets.

## Intermediate-Level Questions

### Q11. What is database replication?

Maintaining copies of database data across multiple database nodes.

### Q12. What is database sharding?

Distributing different subsets of data across multiple database nodes.

### Q13. What is database partitioning?

Dividing a large dataset or table into smaller partitions.

### Q14. What is caching?

Storing frequently accessed data in a faster layer to reduce repeated processing or database queries.

### Q15. What is Redis?

Redis is an in-memory data structure store used for caching, counters, queues and other workloads.

### Q16. What is a message queue?

A mechanism that allows components to exchange messages asynchronously.

### Q17. What is Kafka?

Kafka is a distributed event streaming platform.

### Q18. What is the CAP theorem?

The CAP theorem describes the consistency-availability trade-off during network partitions in distributed data systems.

### Q19. What is eventual consistency?

A consistency model in which replicas may temporarily differ but eventually converge when updates stop and replication completes.

### Q20. What is the difference between ACID and BASE?

ACID focuses on transaction properties, while BASE describes an approach emphasizing availability and eventual consistency in distributed systems.

## Advanced-Level Questions

### Q21. What is a distributed transaction?

A transaction involving multiple independent services or databases.

### Q22. What is the Saga Pattern?

A pattern that manages distributed business workflows through local transactions and compensating actions.

### Q23. What is Two-Phase Commit?

A distributed transaction protocol with prepare and commit/abort phases.

### Q24. What is rate limiting?

A technique for controlling the number of requests a client can make within a given time.

### Q25. What is the Token Bucket algorithm?

An algorithm that uses tokens to control request rates while allowing configured bursts.

### Q26. What is the Circuit Breaker Pattern?

A resilience pattern that temporarily blocks calls to a failing service to prevent cascading failures.

### Q27. What is idempotency?

The property that repeating an operation produces the same intended result as performing it once.

### Q28. What is distributed locking?

A coordination mechanism used to control concurrent access to a shared resource across multiple processes or machines.

### Q29. What is CQRS?

An architectural pattern that separates command operations from query operations.

### Q30. What is Event Sourcing?

An approach that stores state changes as an append-only sequence of events.

### Q31. What is the Transactional Outbox Pattern?

A pattern that stores business data and corresponding event records in the same local transaction before publishing events asynchronously.

### Q32. What is a cache stampede?

A situation where many requests simultaneously attempt to reload the same expired cache entry.

### Q33. What is a cache avalanche?

A situation where many cache entries expire around the same time, potentially overloading the underlying data store.

### Q34. What is a cache penetration problem?

Repeated requests for nonexistent data bypass the cache and reach the database.

### Q35. What is replication lag?

The delay between a write occurring on the primary database and that update becoming visible on a replica.

---

# PART 26: PRACTICAL HANDS-ON ASSIGNMENTS

The following assignments are designed to move from conceptual understanding to implementation.

## Level 1: Fundamentals

| No. | Assignment | Expected Result |
|---|---|---|
| 1 | Design a simple monolithic application | Monolithic architecture diagram |
| 2 | Convert it into a modular monolith | Clearly separated business modules |
| 3 | Design a microservices architecture | Independent service diagram |
| 4 | Implement a basic load balancer using Nginx | Traffic distribution |
| 5 | Deploy multiple Node.js instances | Horizontal scaling |
| 6 | Implement Redis caching | Faster read operations |
| 7 | Add database indexes | Improved query execution plan |
| 8 | Implement API rate limiting | Controlled request volume |

## Level 2: Intermediate

| No. | Assignment | Expected Result |
|---|---|---|
| 9 | Implement cache-aside | Redis and MySQL integration |
| 10 | Implement cache invalidation | Consistent cached product data |
| 11 | Implement a Redis leaderboard | Sorted Set operations |
| 12 | Implement a background job queue | Asynchronous processing |
| 13 | Build a RabbitMQ messaging demo | Producer and consumer |
| 14 | Build a Kafka producer and consumer | Event streaming |
| 15 | Implement an idempotent API | Duplicate request protection |
| 16 | Implement retry with exponential backoff | Resilient API integration |
| 17 | Implement a circuit breaker | Failure isolation |
| 18 | Implement a distributed lock | Coordinated job execution |

## Level 3: Advanced

| No. | Assignment | Expected Result |
|---|---|---|
| 19 | Implement a URL shortener | Short URL generation and redirection |
| 20 | Implement a real-time chat application | WebSocket communication |
| 21 | Implement group messaging | Multi-user message delivery |
| 22 | Build an asynchronous notification service | Queue-based notification processing |
| 23 | Build an order processing system | Distributed business workflow |
| 24 | Implement the Saga Pattern | Compensating transactions |
| 25 | Implement the Transactional Outbox Pattern | Reliable event publishing |
| 26 | Implement CQRS | Separate read and write models |
| 27 | Implement database replication | Primary and replica architecture |
| 28 | Design a sharded database | Distributed data storage |

## Level 4: System Design Projects

### Project 1: URL Shortener

Features:

- Create short URLs.
- Redirect.
- Custom aliases.
- Expiration.
- Redis caching.
- Analytics.
- Rate limiting.

### Project 2: WhatsApp Clone

Features:

- Authentication.
- Real-time messaging.
- Group messaging.
- Online/offline presence.
- Message persistence.
- Delivery receipts.
- Redis.
- WebSockets.

### Project 3: YouTube Clone

Features:

- Video uploads.
- S3 storage.
- Video processing.
- Multiple resolutions.
- CDN delivery.
- Comments.
- Likes.
- Search.

### Project 4: E-commerce Platform

Features:

- Product service.
- Cart service.
- Order service.
- Payment integration.
- Inventory management.
- Redis caching.
- SQS or Kafka.
- Notification service.

### Project 5: Notification System

Features:

- Email notifications.
- SMS notifications.
- Push notifications.
- Queue processing.
- Retry handling.
- Dead Letter Queue.
- Idempotency.
- Delivery tracking.

---

# PART 27: IMPORTANT SYSTEM DESIGN CONCEPTS – QUICK REVISION

| Concept | Explanation |
|---|---|
| System Design | Designing application architecture |
| Functional Requirements | What the system does |
| Non-Functional Requirements | How the system performs |
| Latency | Time required to complete an operation |
| Throughput | Work completed per unit of time |
| Availability | Proportion of time a system is accessible |
| Scalability | Ability to handle increased workload |
| Monolith | Single application deployment unit |
| Microservices | Independently deployable services |
| Distributed System | Multiple independent networked components |
| Vertical Scaling | Increasing server resources |
| Horizontal Scaling | Adding more servers |
| Load Balancer | Distributes incoming traffic |
| Replication | Maintaining copies of data |
| Sharding | Distributing data across nodes |
| Partitioning | Dividing data into smaller parts |
| Cache-Aside | Application checks cache before database |
| Write-Through | Writes update cache and data store |
| Kafka | Distributed event streaming |
| RabbitMQ | Message broker |
| CAP | Consistency, Availability and Partition Tolerance |
| ACID | Atomicity, Consistency, Isolation, Durability |
| BASE | Basically Available, Soft State, Eventual Consistency |
| 2PC | Two-Phase Commit |
| Saga | Distributed workflow with compensating transactions |
| Rate Limiting | Controls request volume |
| API Gateway | Central API entry point |
| Circuit Breaker | Prevents repeated calls to failing services |
| Exponential Backoff | Increasing retry delays |
| Idempotency | Repeated operation has the same intended effect |
| Distributed Lock | Coordinates access to shared resources |
| CQRS | Separates commands and queries |
| Event Sourcing | Stores state changes as events |
| Transactional Outbox | Stores business changes and events atomically |

---

# PART 28: PHASE 56 COMPLETION CHECKLIST

Use this checklist to evaluate your understanding.

### System Design Fundamentals

- [ ] Explain System Design.
- [ ] Understand functional and non-functional requirements.
- [ ] Understand latency and throughput.
- [ ] Understand availability and reliability.
- [ ] Understand SLI, SLO and SLA.

### Architecture

- [ ] Understand monolithic architecture.
- [ ] Understand modular monoliths.
- [ ] Understand microservices.
- [ ] Understand distributed systems.
- [ ] Understand service communication.

### Scalability

- [ ] Understand horizontal scaling.
- [ ] Understand vertical scaling.
- [ ] Understand load balancing.
- [ ] Understand stateless application design.

### Database Architecture

- [ ] Understand database replication.
- [ ] Understand replication lag.
- [ ] Understand database sharding.
- [ ] Understand partitioning.
- [ ] Understand database indexes.
- [ ] Understand composite indexes.

### Caching

- [ ] Understand caching.
- [ ] Understand cache-aside.
- [ ] Understand write-through.
- [ ] Understand write-behind.
- [ ] Understand cache invalidation.
- [ ] Understand Redis data structures.
- [ ] Understand cache stampede, avalanche and penetration.

### Distributed Systems

- [ ] Understand CAP theorem.
- [ ] Understand consistency models.
- [ ] Understand ACID and BASE.
- [ ] Understand distributed transactions.
- [ ] Understand Saga Pattern.
- [ ] Understand 2PC.

### Reliability

- [ ] Understand rate limiting.
- [ ] Understand API Gateway.
- [ ] Understand circuit breakers.
- [ ] Understand retries and exponential backoff.
- [ ] Understand idempotency.
- [ ] Understand distributed locking.

### Advanced Architecture

- [ ] Understand event-driven architecture.
- [ ] Understand Transactional Outbox.
- [ ] Understand CQRS.
- [ ] Understand Event Sourcing.

### Practical System Design

- [ ] Design a URL shortener.
- [ ] Design a WhatsApp-like application.
- [ ] Design a YouTube-like application.
- [ ] Design an e-commerce platform.
- [ ] Design a notification system.

---

# Phase 56 – Final Summary

This phase introduced the architectural principles used to design large-scale applications.

The most important concepts to remember are:

1. **System Design:** How application components work together.
2. **Scalability:** How applications handle increasing workloads.
3. **Microservices:** How large applications are divided into independent services.
4. **Distributed Systems:** How independent services communicate and coordinate.
5. **Replication:** How databases maintain copies of data.
6. **Sharding:** How data is distributed across database nodes.
7. **Caching:** How frequently accessed data is retrieved faster.
8. **CAP Theorem:** Consistency and availability trade-offs during network partitions.
9. **Saga Pattern:** How distributed business transactions are coordinated.
10. **Rate Limiting:** How request traffic is controlled.
11. **Circuit Breaker:** How cascading failures are prevented.
12. **Idempotency:** How duplicate operations are prevented.
13. **Event-Driven Architecture:** How services communicate through events.
14. **CQRS:** How read and write operations can be separated.
15. **Event Sourcing:** How application state changes can be stored as events.

### Recommended Learning Order for Practical Implementation

I recommend implementing these concepts in this order:

```text
1. Redis Caching
       |
       v
2. Horizontal Scaling
       |
       v
3. Load Balancing
       |
       v
4. Rate Limiting
       |
       v
5. Message Queues
       |
       v
6. Idempotency
       |
       v
7. Circuit Breaker
       |
       v
8. Database Replication
       |
       v
9. Distributed Transactions
       |
       v
10. Complete System Design Projects
```

---

# Next Phase Preview

## Phase 57 – Advanced Software Architecture, Design Patterns and Enterprise Application Development

Topics planned:

1. Software Architecture Fundamentals.
2. Layered Architecture.
3. Clean Architecture.
4. Hexagonal Architecture.
5. Onion Architecture.
6. Domain-Driven Design (DDD).
7. SOLID Principles.
8. DRY, KISS and YAGNI.
9. Gang of Four Design Patterns.
10. Creational Design Patterns.
11. Structural Design Patterns.
12. Behavioral Design Patterns.
13. Repository Pattern.
14. Unit of Work Pattern.
15. Dependency Injection.
16. Inversion of Control.
17. Strategy Pattern.
18. Factory Pattern.
19. Adapter Pattern.
20. Observer Pattern.
21. Decorator Pattern.
22. Middleware Architecture.
23. Domain Events.
24. Application Services.
25. Domain Services.
26. DTO and Data Mapping.
27. Validation Architecture.
28. Error Handling Architecture.
29. Logging and Observability Architecture.
30. Enterprise Node.js Application Architecture.
31. Testing Architecture.
32. Refactoring Legacy Applications.
33. Designing Maintainable Large-Scale Applications.

**Say "OK, Phase 57" when you're ready to continue.**