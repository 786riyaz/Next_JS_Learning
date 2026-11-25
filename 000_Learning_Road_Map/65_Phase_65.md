# Phase 65 – Advanced System Design, Distributed Systems and High-Scale Application Architecture

**Level:** Advanced to Expert  
**Domain:** System Design, Backend Engineering, Distributed Systems, Database Architecture, Cloud Computing and Scalable Application Development.

**Primary Technologies:** Node.js, TypeScript, Next.js, Express, MySQL, PostgreSQL, MongoDB, Redis, RabbitMQ, Apache Kafka, Docker, Kubernetes, AWS and Nginx.

---

# 1. Learning Objectives

In this phase, you will learn how to design, architect, scale and operate large-scale software applications.

You will understand how applications such as WhatsApp, YouTube, Netflix, Amazon and Uber are architected at a high level.

We will cover:

1. System Design Fundamentals.
2. Functional Requirements.
3. Non-Functional Requirements.
4. Scalability.
5. Availability.
6. Reliability.
7. Fault Tolerance.
8. Performance.
9. Latency vs Throughput.
10. Vertical Scaling.
11. Horizontal Scaling.
12. Load Balancing.
13. Reverse Proxy.
14. API Gateway.
15. Monolithic Architecture.
16. Microservices Architecture.
17. Modular Monolith.
18. Distributed Systems Fundamentals.
19. CAP Theorem.
20. PACELC Theorem.
21. Consistency Models.
22. Strong Consistency.
23. Eventual Consistency.
24. Distributed Transactions.
25. Two-Phase Commit.
26. Saga Pattern.
27. Database Replication.
28. Database Sharding.
29. Partitioning.
30. Database Indexing.
31. SQL vs NoSQL.
32. Database Denormalization.
33. Caching Strategies.
34. Cache Invalidation.
35. Redis Architecture.
36. CDN Architecture.
37. Message Queues.
38. Event-Driven Architecture.
39. Service Discovery.
40. Circuit Breaker Pattern.
41. Retry Pattern.
42. Bulkhead Pattern.
43. Rate Limiting.
44. Idempotency.
45. Distributed Locking.
46. Leader Election.
47. Consistent Hashing.
48. Observability.
49. Distributed Tracing.
50. High Availability Architecture.
51. Disaster Recovery.
52. Designing a URL Shortener.
53. Designing WhatsApp.
54. Designing YouTube.
55. Designing E-Commerce Systems.
56. Designing Notification Systems.
57. Designing Real-Time Chat Systems.
58. Designing Payment Systems.
59. System Design Interview Preparation.
60. Complete Enterprise System Design Project.

---

# PART 1: SYSTEM DESIGN FUNDAMENTALS

## 2. What Is System Design?

System Design is the process of defining the architecture, components, databases, communication mechanisms and infrastructure required to build a software system.

It describes how different parts of an application work together.

For example, consider an e-commerce application.

It needs:

- User authentication.
- Product management.
- Shopping cart.
- Order management.
- Payment processing.
- Inventory management.
- Notifications.
- Database storage.
- File storage.
- Deployment infrastructure.

System Design determines how all these components interact.

### Simple System Architecture

```text
                 Users
                   |
                   v
                Frontend
                   |
                   v
                Backend
                   |
          +--------+--------+
          |        |        |
          v        v        v
       Database  Cache   Message Queue
          |        |        |
          v        v        v
        Storage  Redis    Workers
```

### Why Is System Design Important?

Without proper system design, applications can experience:

- Slow response times.
- Database bottlenecks.
- Poor scalability.
- Server failures.
- Security problems.
- Difficult maintenance.
- Data inconsistencies.
- Deployment complexity.

System Design helps solve these problems before they become expensive production issues.

---

## 3. What Is High-Level Design (HLD)?

High-Level Design describes the overall architecture of a system.

It focuses on:

- Major components.
- Services.
- Databases.
- Communication.
- Infrastructure.
- Data flow.
- External integrations.

Example:

```text
             E-Commerce Application
                       |
                       v
                  API Gateway
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
    User Service   Order Service   Product Service
        |              |              |
        v              v              v
      MySQL          MySQL           MongoDB
                       |
                       v
                  Payment Service
                       |
                       v
                  Payment Gateway
```

HLD answers:

- Which services are required?
- Which database should be used?
- How do services communicate?
- Where should caching be implemented?
- How will the system scale?

---

## 4. What Is Low-Level Design (LLD)?

Low-Level Design describes the internal implementation of individual components.

It focuses on:

- Classes.
- Interfaces.
- Functions.
- Database schemas.
- API contracts.
- Design patterns.
- Validation.
- Error handling.
- Internal business logic.

Example:

```typescript
interface PaymentProcessor {
  processPayment(
    amount: number,
    currency: string
  ): Promise<PaymentResult>;
}

class StripePaymentProcessor
  implements PaymentProcessor {

  async processPayment(
    amount: number,
    currency: string
  ): Promise<PaymentResult> {
    // Payment processing logic
    return {
      success: true
    };
  }
}
```

### HLD vs LLD

| Feature | HLD | LLD |
|---|---|---|
| Focus | Overall architecture | Internal implementation |
| Components | Services and databases | Classes and functions |
| Audience | Architects and engineering teams | Developers |
| Example | Microservices architecture | Payment service implementation |
| Complexity | System-level | Component-level |

---

# PART 2: REQUIREMENTS ENGINEERING

## 5. What Are Functional Requirements?

Functional Requirements describe what a system must do.

Example: E-commerce application.

| Requirement | Description |
|---|---|
| User Registration | Users can create accounts |
| Login | Users can authenticate |
| Product Listing | Users can view products |
| Search | Users can search products |
| Cart | Users can add products |
| Checkout | Users can place orders |
| Payment | Users can pay |
| Tracking | Users can track orders |

Functional requirements describe system behavior.

## 6. What Are Non-Functional Requirements?

Non-Functional Requirements describe how well the system should operate.

Examples:

- Performance.
- Scalability.
- Availability.
- Reliability.
- Security.
- Maintainability.
- Durability.
- Accessibility.

Example:

| Requirement | Target |
|---|---|
| API Response Time | p95 below 200 ms for selected endpoints |
| Availability | 99.9% monthly |
| Concurrent Users | 100,000 |
| Data Durability | No acknowledged payment records lost |
| Security | Encrypted communication |
| Scalability | Support additional application instances |

These are illustrative targets, not universal standards.

### Functional vs Non-Functional Requirements

| Functional | Non-Functional |
|---|---|
| What the system does | How well it operates |
| Login | Authentication response time |
| Create order | Order processing reliability |
| Upload image | Upload performance |
| Send notification | Notification delivery latency |

---

# PART 3: SCALABILITY

## 7. What Is Scalability?

Scalability is the ability of a system to handle increasing workloads without unacceptable degradation in performance or reliability.

Suppose your application initially has:

```text
100 Users
```

After some time:

```text
10,000 Users
```

Eventually:

```text
1,000,000 Users
```

Your architecture should be capable of growing with the workload.

### What Can Increase?

- Number of users.
- Number of requests.
- Database records.
- Concurrent connections.
- File uploads.
- Background jobs.
- Network traffic.

## 8. Vertical Scaling

Vertical Scaling means increasing the resources of an existing server.

For example:

```text
Before:

CPU: 2 Cores
RAM: 4 GB
Storage: 100 GB


After:

CPU: 16 Cores
RAM: 64 GB
Storage: 1 TB
```

### Advantages

- Simple to implement.
- No application-level distribution required.
- Can improve performance quickly.
- Useful for applications with limited infrastructure complexity.

### Disadvantages

- Hardware limits.
- Potentially expensive upgrades.
- A single-server failure can affect the entire application.
- Eventually reaches a practical ceiling.

## 9. Horizontal Scaling

Horizontal Scaling means adding more servers or instances.

Example:

```text
Before:

       Application
             |
             v
          Server A


After:

          Load Balancer
                 |
        +--------+--------+
        |        |        |
        v        v        v
     Server A Server B Server C
```

### Advantages

- Higher capacity.
- Better fault isolation.
- Easier incremental growth.
- Supports distributed workloads.

### Disadvantages

- More infrastructure complexity.
- Requires coordination between instances.
- Shared state must be handled carefully.
- Monitoring and deployment become more complicated.

### Vertical vs Horizontal Scaling

| Feature | Vertical | Horizontal |
|---|---|---|
| Approach | Upgrade one server | Add servers |
| Complexity | Lower initially | Higher |
| Capacity | Hardware-limited | Can grow across instances |
| Fault tolerance | Limited with one server | Can improve with redundancy |
| Cost | Large upgrades may be expensive | Incremental scaling possible |
| Example | 4 GB to 32 GB RAM | 1 server to 5 servers |

**Important:** Horizontal scaling does not automatically make an application fault-tolerant. You must also consider load balancing, shared state, database availability and failure handling.

---

# PART 4: AVAILABILITY AND RELIABILITY

## 10. What Is Availability?

Availability measures how often a system is operational and accessible.

For example:

```text
Availability = 99.9%
```

This means the system is expected to be available for 99.9% of the measurement period.

### Availability Formula

\[
Availability = \frac{Uptime}{Uptime + Downtime} \times 100
\]

Example:

Suppose a service operates for 999 hours and is unavailable for 1 hour.

\[
Availability = \frac{999}{1000} \times 100
\]

\[
Availability = 99.9\%
\]

## 11. Availability Levels

| Availability | Approximate Annual Downtime |
|---|---:|
| 99% | 3 days 15 hours 36 minutes |
| 99.9% | 8 hours 45 minutes 36 seconds |
| 99.99% | 52 minutes 34 seconds |
| 99.999% | 5 minutes 15 seconds |

These figures assume a 365-day year.

### What Is High Availability?

High Availability means designing systems to remain operational despite component failures.

Example:

```text
              Load Balancer
                    |
            +-------+-------+
            |               |
            v               v
         Server A         Server B
            |               |
            +-------+-------+
                    |
                    v
              Shared Database
```

If Server A fails, traffic can be routed to Server B.

For the database layer, additional replication and failover mechanisms may be required.

---

## 12. What Is Reliability?

Reliability is the ability of a system to perform its intended functions correctly over time.

A system may be available but unreliable.

Example:

An API responds successfully but sometimes returns incorrect payment totals.

The API is technically available, but its behavior is unreliable.

### Availability vs Reliability

| Availability | Reliability |
|---|---|
| System is accessible | System works correctly |
| Focuses on uptime | Focuses on correct operation |
| Measures service availability | Measures dependable behavior |
| Example: Server responds | Example: Payment amount is correct |

---

## 13. What Is Fault Tolerance?

Fault Tolerance is the ability of a system to continue operating when some components fail.

Example:

```text
          Application
               |
        +------+------+
        |             |
        v             v
     Server A      Server B
        |             |
        X             |
     Failure          |
                      v
                 Continues
```

### Common Fault-Tolerance Techniques

- Redundant servers.
- Database replication.
- Automatic failover.
- Health checks.
- Retry mechanisms.
- Circuit breakers.
- Message queues.
- Backups.
- Multi-zone deployment.

**Important:** Redundancy without proper failure detection and failover does not guarantee fault tolerance.

---

# PART 5: PERFORMANCE

## 14. What Is Latency?

Latency is the time taken for an operation or request to complete.

Example:

A user requests a product.

The API takes 150 milliseconds to return the result.

Latency:

```text
150 ms
```

Lower latency generally means a faster user experience.

## 15. What Is Throughput?

Throughput measures how much work a system completes within a given period.

For APIs, it is often expressed as:

```text
Requests Per Second (RPS)
```

Example:

```text
Server A: 500 requests/second
Server B: 2,000 requests/second
```

Server B has higher throughput under the stated measurement conditions.

## 16. Latency vs Throughput

| Latency | Throughput |
|---|---|
| Time required per operation | Amount of work completed per unit time |
| Measured in milliseconds or seconds | Measured in requests/second, messages/second, etc. |
| Lower is generally better | Higher is generally better |
| Affects response time | Affects processing capacity |

### Example

Imagine a restaurant.

- Latency: How long one customer waits for a meal.
- Throughput: How many meals the restaurant serves per hour.

A restaurant can serve many meals per hour while individual customers still experience long waiting times.

## 17. What Are p50, p95 and p99?

These are latency percentiles.

Suppose you measure 10,000 API requests.

| Metric | Meaning |
|---|---|
| p50 | 50% of requests completed within this time |
| p95 | 95% completed within this time |
| p99 | 99% completed within this time |

Example:

```text
p50 = 50 ms
p95 = 200 ms
p99 = 500 ms
```

This means 95% of requests completed within 200 ms, while 5% took longer.

Percentiles are important because averages can hide slow requests.

---

# PART 6: LOAD BALANCING

## 18. What Is a Load Balancer?

A Load Balancer distributes incoming traffic across multiple servers.

Example:

```text
             10,000 Requests
                    |
                    v
              Load Balancer
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Server A Server B Server C
```

### Why Use Load Balancing?

- Distribute traffic.
- Avoid overloading one server.
- Improve availability.
- Support horizontal scaling.
- Perform health checks.
- Route traffic away from unhealthy instances.

## 19. Types of Load Balancers

### Layer 4 Load Balancer

Operates primarily at the transport layer.

It commonly uses:

- IP addresses.
- TCP ports.
- Connection information.

### Layer 7 Load Balancer

Operates at the application layer.

It can route based on:

- HTTP paths.
- Hostnames.
- Headers.
- Cookies.
- Application-level information.

### Layer 4 vs Layer 7

| Feature | Layer 4 | Layer 7 |
|---|---|---|
| OSI Layer | Transport | Application |
| Routing | IP and port information | HTTP information |
| Content inspection | Limited | More extensive |
| Example | TCP load balancing | HTTP reverse proxy |

## 20. Load Balancing Algorithms

### 1. Round Robin

Requests are distributed sequentially.

```text
Request 1 -> Server A
Request 2 -> Server B
Request 3 -> Server C
Request 4 -> Server A
```

### 2. Weighted Round Robin

Servers receive traffic according to assigned weights.

```text
Server A: Weight 5
Server B: Weight 3
Server C: Weight 2
```

Server A receives a larger share of traffic.

### 3. Least Connections

Traffic is sent to the server with the fewest active connections.

### 4. IP Hash

A hash of the client IP determines the selected server.

### 5. Least Response Time

Traffic is directed toward servers with favorable response times and connection loads, depending on the implementation.

### Important

A load-balancing algorithm does not eliminate the need for health checks, capacity planning or appropriate session management.

---

# PART 7: REVERSE PROXY

## 21. What Is a Reverse Proxy?

A Reverse Proxy is a server that receives client requests and forwards them to backend servers.

Example:

```text
            Client
               |
               v
            Nginx
               |
        +------+------+
        |             |
        v             v
    Node Server   Next.js Server
       :3001          :3002
```

### Why Use a Reverse Proxy?

- Hide internal server addresses.
- Terminate TLS.
- Route requests.
- Load balance.
- Compress responses.
- Serve static assets.
- Apply access restrictions.
- Proxy WebSocket connections.

### Reverse Proxy vs Forward Proxy

| Reverse Proxy | Forward Proxy |
|---|---|
| Sits in front of servers | Sits in front of clients |
| Protects or routes backend services | Represents client requests |
| Common in production hosting | Common in corporate networks |
| Example: Nginx | Example: Corporate outbound proxy |

---

# PART 8: API GATEWAY

## 22. What Is an API Gateway?

An API Gateway is an entry point through which clients access backend services.

It can handle:

- Routing.
- Authentication.
- Authorization.
- Rate limiting.
- Request transformation.
- Logging.
- API versioning.
- Traffic management.

### Architecture

```text
             Frontend
                 |
                 v
             API Gateway
                 |
        +--------+--------+
        |        |        |
        v        v        v
      User     Order   Product
     Service  Service  Service
```

### Example Routes

```text
/api/users      -> User Service
/api/orders     -> Order Service
/api/products   -> Product Service
/api/payments   -> Payment Service
```

### Reverse Proxy vs API Gateway

| Reverse Proxy | API Gateway |
|---|---|
| Primarily forwards traffic | Manages API access and policies |
| Routing and load balancing | Routing, authentication, quotas, transformations |
| Infrastructure-oriented | API-oriented |
| Can be part of an API gateway | Often implemented using reverse-proxy technology |

They can overlap. An API Gateway is often built using reverse-proxy capabilities plus additional API management features.

---

# PART 9: APPLICATION ARCHITECTURES

## 23. Monolithic Architecture

A Monolithic Application contains multiple business modules deployed as one application.

Example:

```text
         E-Commerce Application
                    |
       +------------+------------+
       |            |            |
       v            v            v
    Users         Orders       Products
       |            |            |
       +------------+------------+
                    |
                    v
                 Database
```

### Advantages

- Simple deployment.
- Easier initial development.
- Straightforward debugging.
- Lower infrastructure overhead.
- Simple local development.

### Disadvantages

- Components can become tightly coupled.
- Scaling individual modules is difficult.
- Large deployments can become risky.
- Changes can affect unrelated modules.

## 24. Microservices Architecture

Microservices Architecture divides an application into independently deployable services.

Example:

```text
                API Gateway
                     |
       +-------------+-------------+
       |             |             |
       v             v             v
  User Service  Order Service  Product Service
       |             |             |
       v             v             v
    User DB       Order DB      Product DB
```

### Advantages

- Independent deployment.
- Independent scaling.
- Clear service boundaries.
- Technology flexibility.
- Team autonomy.

### Disadvantages

- Distributed-system complexity.
- Network failures.
- Data consistency challenges.
- Distributed tracing requirements.
- More infrastructure.
- Complex testing.

## 25. Modular Monolith

A Modular Monolith is a single deployable application organized into well-defined modules.

Example:

```text
             Modular Monolith
                    |
       +------------+------------+
       |            |            |
       v            v            v
    User Module  Order Module  Payment Module
       |            |            |
       +------------+------------+
                    |
                 Database
```

Each module has clear responsibilities and controlled dependencies.

### Monolith vs Modular Monolith vs Microservices

| Feature | Monolith | Modular Monolith | Microservices |
|---|---|---|---|
| Deployment | One application | One application | Multiple services |
| Code organization | Can become tightly coupled | Strong module boundaries | Separate service boundaries |
| Scaling | Whole application | Whole application | Individual services |
| Complexity | Low initially | Moderate | High |
| Infrastructure | Simpler | Simpler | More complex |
| Best use | Small applications | Growing business systems | Large distributed systems |

### Recommendation

Do not automatically start with microservices.

A modular monolith is often a good starting point for a growing application. Extract services when there is a clear reason, such as independent scaling, team ownership or deployment requirements.

---

# PART 10: DISTRIBUTED SYSTEMS FUNDAMENTALS

## 26. What Is a Distributed System?

A Distributed System consists of multiple independent computers or services that communicate over a network and work together as one logical system.

Example:

```text
            Distributed Application
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Server A     Server B     Server C
          |            |            |
          +------------+------------+
                       |
                       v
                  Shared Services
```

Examples:

- Banking platforms.
- E-commerce systems.
- Search engines.
- Cloud platforms.
- Messaging applications.
- Video streaming platforms.

## 27. Challenges of Distributed Systems

Distributed systems introduce problems that are less common in a single-process application.

### 1. Network Failure

Two services may be unable to communicate.

### 2. Partial Failure

One service may fail while others continue working.

### 3. Data Consistency

Different database replicas may temporarily contain different values.

### 4. Clock Synchronization

Different servers may have different system clocks.

### 5. Duplicate Messages

Messages can be delivered more than once.

### 6. Network Latency

Communication between services introduces delays.

### 7. Distributed Transactions

One business operation may involve multiple independent services.

### 8. Concurrency

Multiple servers may update related data simultaneously.

---

# PART 11: CAP THEOREM

## 28. What Is the CAP Theorem?

The CAP Theorem describes a trade-off in distributed data systems.

It concerns three properties:

- Consistency.
- Availability.
- Partition Tolerance.

### C – Consistency

Every successful read receives the most recent committed value, according to the system's consistency model.

### A – Availability

Every request to a non-failing node receives a non-error response, even if the response may not contain the latest value.

### P – Partition Tolerance

The system continues operating despite network communication failures between nodes.

## 29. Understanding CAP

Imagine a distributed database with two nodes.

```text
          Node A
             |
             X
       Network Failure
             X
             |
          Node B
```

The nodes cannot communicate.

This is a network partition.

The system must decide how to behave.

### CP-Oriented Behavior

The system preserves consistency by rejecting or delaying certain operations when it cannot safely coordinate.

### AP-Oriented Behavior

The system continues accepting operations, potentially allowing temporarily inconsistent responses.

### Important Clarification

CAP does not simply mean that every database permanently chooses two properties out of three.

The theorem is specifically about behavior during a network partition.

When a partition occurs, a system cannot guarantee both the stated strong consistency and availability properties for every request.

---

# PART 12: PACELC THEOREM

## 30. What Is PACELC?

PACELC extends the CAP discussion.

It describes a trade-off:

- If there is a Partition, choose between Availability and Consistency.
- Else, during normal operation, choose between Latency and Consistency.

PACELC stands for:

```text
P - Partition
A - Availability
C - Consistency
E - Else
L - Latency
C - Consistency
```

### Simplified Explanation

```text
          Network Partition?
                  |
             +----+----+
             |         |
            Yes        No
             |         |
             v         v
        A vs C      L vs C
```

### Why Is PACELC Useful?

A system may not experience a network partition most of the time.

Even under normal conditions, it must decide whether to:

- Return data quickly from a nearby replica.
- Wait for stronger consistency across replicas.

This helps explain trade-offs in distributed database design.

---

# PART 13: CONSISTENCY MODELS

## 31. What Is Consistency?

Consistency describes the rules governing what values clients can observe when reading and writing distributed data.

Different systems provide different consistency guarantees.

## 32. Strong Consistency

Strong consistency generally means a successful read returns the latest committed write, according to the system's consistency guarantees.

Example:

```text
Account Balance = ₹10,000

User withdraws ₹2,000.

New Balance = ₹8,000.

A subsequent read returns ₹8,000.
```

Strong consistency is important in operations where stale data could cause serious business problems.

Examples:

- Account balances.
- Inventory reservation.
- Financial transactions.
- Payment state transitions.

## 33. Eventual Consistency

Eventual consistency means replicas may temporarily disagree, but if no new updates occur and replication continues successfully, they eventually converge.

Example:

```text
Primary Database:
Product Price = ₹500

Replica A:
Product Price = ₹500

Replica B:
Product Price = ₹450
```

After replication catches up:

```text
Replica B:
Product Price = ₹500
```

### Where Is Eventual Consistency Useful?

- Social media likes.
- View counters.
- Analytics.
- Search indexes.
- Activity feeds.
- Product recommendation data.

## 34. Strong vs Eventual Consistency

| Feature | Strong Consistency | Eventual Consistency |
|---|---|---|
| Latest data | Required according to consistency guarantee | May be temporarily stale |
| Latency | May require coordination | Often lower |
| Availability during partitions | May be restricted | Can be favored |
| Use case | Financial operations | Analytics and feeds |
| Complexity | Coordination requirements | Reconciliation and stale reads |

---

# PART 14: DISTRIBUTED TRANSACTIONS

## 35. What Is a Distributed Transaction?

A Distributed Transaction is a business operation that involves multiple independent services or databases.

Example:

An e-commerce order may involve:

1. Order Service.
2. Inventory Service.
3. Payment Service.
4. Shipping Service.

```text
           Create Order
                |
        +-------+-------+
        |       |       |
        v       v       v
     Inventory Payment Shipping
```

A failure in one service can leave the overall business operation incomplete.

Example:

- Inventory reserved.
- Payment successful.
- Order creation fails.

The system needs a recovery strategy.

---

# PART 15: TWO-PHASE COMMIT

## 36. What Is Two-Phase Commit (2PC)?

Two-Phase Commit is a distributed transaction protocol used to coordinate a transaction across multiple participants.

It has two phases.

### Phase 1: Prepare

The coordinator asks all participants whether they can commit.

```text
        Coordinator
             |
       +-----+-----+
       |           |
       v           v
   Database A  Database B
       |           |
       v           v
     READY       READY
```

Each participant prepares the transaction and reports its result.

### Phase 2: Commit or Abort

If all participants are prepared, the coordinator asks them to commit.

Otherwise, it asks them to abort.

```text
         Coordinator
              |
        +-----+-----+
        |           |
        v           v
     Commit      Commit
        |           |
        v           v
    Database A  Database B
```

### Advantages

- Coordinates atomic outcomes across participants.
- Useful where transactional guarantees are required.

### Disadvantages

- Coordination overhead.
- Participants may hold resources while waiting.
- Coordinator failures complicate recovery.
- Can reduce availability during failures.

2PC is not the same as a saga. They solve distributed transaction problems using different approaches.

---

# PART 16: SAGA PATTERN

## 37. What Is the Saga Pattern?

The Saga Pattern manages a distributed business transaction as a sequence of local transactions.

Each step commits independently.

If a later step fails, compensating actions are executed to reverse the business effects of earlier steps where possible.

### Example: E-Commerce Order

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
Arrange Shipping
     |
     v
Order Completed
```

Suppose payment fails.

The system executes a compensating action:

```text
Reserve Inventory
     |
     v
Payment Failed
     |
     v
Release Inventory
     |
     v
Cancel Order
```

### What Is a Compensating Transaction?

A Compensating Transaction is a new operation that semantically reverses an earlier operation.

For example:

| Original Operation | Compensation |
|---|---|
| Reserve inventory | Release inventory |
| Create booking | Cancel booking |
| Charge payment | Refund payment |
| Allocate resource | Deallocate resource |

A compensation is not necessarily a literal database rollback.

For example, a payment refund is a new financial transaction, not deletion of the original charge.

## 38. Saga Implementation Styles

### Choreography

Services communicate through events.

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
```

### Orchestration

A central orchestrator coordinates each step.

```text
          Saga Orchestrator
                  |
        +---------+---------+
        |         |         |
        v         v         v
     Order    Inventory   Payment
    Service    Service    Service
```

### Choreography vs Orchestration

| Feature | Choreography | Orchestration |
|---|---|---|
| Coordination | Distributed through events | Central coordinator |
| Coupling | Can be lower initially | Orchestrator knows workflow |
| Visibility | Harder to visualize complex workflows | Easier to inspect workflow |
| Complexity | Event relationships can become complex | Orchestrator can become complex |
| Suitable for | Simple event-driven flows | Complex business processes |

---

# PART 17: DATABASE REPLICATION

## 39. What Is Database Replication?

Database Replication means maintaining copies of data across multiple database servers.

Example:

```text
           Primary Database
                  |
          Replication Stream
                  |
          +-------+-------+
          |               |
          v               v
      Replica A        Replica B
```

### Why Use Replication?

- Read scalability.
- High availability.
- Disaster recovery.
- Geographic distribution.
- Redundancy.

## 40. Primary-Replica Architecture

The primary database handles writes.

Replicas receive changes and can serve reads.

```text
             Application
                  |
          +-------+-------+
          |               |
          v               v
         WRITE           READ
          |               |
          v               v
       Primary         Replica
          |               ^
          +---------------+
            Replication
```

### Potential Problem: Replication Lag

Suppose:

1. User updates their profile.
2. The write succeeds on the primary.
3. User immediately reads from a lagging replica.
4. The replica returns old data.

This is called a stale read.

### Solutions

- Read-after-write routing to primary.
- Synchronous replication where appropriate.
- Session consistency mechanisms.
- Version checks.
- Carefully designed replica reads.

---

# PART 18: DATABASE SHARDING

## 41. What Is Database Sharding?

Sharding is a technique for distributing database data across multiple independent database partitions or servers.

Each shard stores a subset of the total data.

Example:

```text
            Application
                 |
          Shard Router
                 |
       +---------+---------+
       |         |         |
       v         v         v
    Shard A   Shard B   Shard C
    Users     Users     Users
    1-1000    1001-2000 2001-3000
```

### Why Use Sharding?

Suppose your database has 500 million users.

One database server may no longer be suitable for the required storage, throughput or operational constraints.

Sharding distributes the workload.

## 42. Sharding Strategies

### 1. Range-Based Sharding

Data is divided by ranges.

```text
Shard A: User ID 1-1000
Shard B: User ID 1001-2000
Shard C: User ID 2001-3000
```

### 2. Hash-Based Sharding

A hash function determines the shard.

```javascript
function getShard(userId, shardCount) {
  return hash(userId) % shardCount;
}
```

### 3. Geographic Sharding

Data is distributed by geographical region.

```text
India Users    -> India Region
US Users       -> US Region
Europe Users   -> Europe Region
```

### 4. Directory-Based Sharding

A lookup service maintains a mapping between records and shards.

## 43. Sharding Challenges

- Cross-shard joins.
- Cross-shard transactions.
- Uneven data distribution.
- Rebalancing.
- Hot shards.
- Operational complexity.
- Unique ID generation.

### Replication vs Sharding

| Replication | Sharding |
|---|---|
| Copies data | Splits data |
| Improves availability and read scaling | Improves data distribution and capacity |
| Multiple copies of records | Different records across shards |
| Example: Primary and replicas | Example: User ranges across databases |

---

# PART 19: DATABASE PARTITIONING

## 44. What Is Database Partitioning?

Partitioning divides a large database table into smaller logical or physical parts.

Example:

A `transactions` table contains 100 million records.

You can partition it by month.

```text
transactions
     |
     +---- January Partition
     |
     +---- February Partition
     |
     +---- March Partition
     |
     +---- April Partition
```

### Partitioning Types

- Range Partitioning.
- List Partitioning.
- Hash Partitioning.
- Composite Partitioning.

### Partitioning vs Sharding

Partitioning can happen within one database system.

Sharding generally distributes data across separate database instances or servers.

The exact terminology varies across database technologies.

---

# PART 20: DATABASE INDEXING

## 45. What Is a Database Index?

A Database Index is a data structure that helps the database locate records more efficiently.

Without an index, a query may need to scan many rows.

Example:

```sql
SELECT *
FROM users
WHERE email = 'user@example.com';
```

If `email` is indexed, the database can often locate the matching row without scanning the entire table.

## 46. Create an Index

```sql
CREATE INDEX idx_users_email
ON users(email);
```

### Composite Index

```sql
CREATE INDEX idx_orders_user_date
ON orders(user_id, created_at);
```

This index can help queries filtering by `user_id`, and queries filtering by `user_id` and `created_at`, subject to the database's index rules and query plan.

### Important Index Types

| Index | Purpose |
|---|---|
| B-tree | General-purpose ordered indexing |
| Hash | Equality lookup in supported implementations |
| Full-text | Text searching |
| Spatial | Geographical data |
| Composite | Multiple columns |
| Unique | Enforce uniqueness |

## 47. Indexing Best Practices

- Index frequently filtered columns.
- Index appropriate join columns.
- Avoid unnecessary indexes.
- Analyze query execution plans.
- Consider composite index column order.
- Monitor write performance.
- Remove unused indexes carefully.

**Important:** Indexes improve some read operations but introduce storage overhead and can slow writes because the index structures must also be maintained.

---

# PART 21: SQL VS NOSQL

## 48. What Is an SQL Database?

SQL databases are relational database systems.

Examples:

- MySQL.
- PostgreSQL.
- SQL Server.
- Oracle Database.

They commonly organize data into tables and relationships.

Example:

```text
users
-----
id
name
email

orders
------
id
user_id
total
```

## 49. What Is a NoSQL Database?

NoSQL databases use data models other than the traditional relational table model.

Examples:

- MongoDB.
- Redis.
- Cassandra.
- DynamoDB.

Common categories:

1. Document databases.
2. Key-value databases.
3. Wide-column databases.
4. Graph databases.

## 50. SQL vs NoSQL

| Feature | SQL | NoSQL |
|---|---|---|
| Data Model | Relational | Various models |
| Schema | Usually predefined | Often flexible |
| Relationships | Native relational model | Model-dependent |
| Joins | Common | Varies by database |
| Transactions | Supported | Depends on database |
| Query Language | SQL | Database-specific |
| Examples | MySQL, PostgreSQL | MongoDB, Redis, Cassandra |

### When Should You Use SQL?

- Financial systems.
- Complex relationships.
- Reporting.
- Transaction-heavy applications.
- Strong relational constraints.

### When Should You Use NoSQL?

- Flexible document structures.
- Key-value access.
- Specific high-throughput workloads.
- Distributed data models.
- Graph relationships, when using a graph database.

**Important:** Do not choose NoSQL simply because an application needs to scale. Modern relational databases can scale substantially. Choose according to data relationships, consistency requirements, access patterns and operational needs.

---

# PART 22: DATABASE DENORMALIZATION

## 51. What Is Database Normalization?

Normalization organizes relational data to reduce unnecessary duplication and update anomalies.

Example:

Instead of storing user information repeatedly in every order:

```text
orders
------
id
user_name
user_email
total
```

Separate the data:

```text
users
-----
id
name
email

orders
------
id
user_id
total
```

## 52. What Is Denormalization?

Denormalization intentionally duplicates or precomputes data to optimize certain read operations.

Example:

```text
orders
------
id
user_id
user_name
total
```

The order record includes the customer's name so a common read does not need to join the users table.

### Advantages

- Faster reads for selected workloads.
- Fewer joins.
- Convenient reporting.
- Precomputed views.

### Disadvantages

- Duplicate data.
- Update complexity.
- Consistency challenges.
- More storage.

### Normalization vs Denormalization

| Normalization | Denormalization |
|---|---|
| Reduces redundancy | Introduces selected redundancy |
| Supports data integrity | Optimizes read patterns |
| Often requires joins | Can reduce joins |
| Useful for transactional models | Useful for read-heavy workloads |

---

# PART 23: CACHING STRATEGIES

## 53. What Is Caching?

Caching stores frequently accessed data in a faster storage layer to reduce repeated expensive operations.

Example:

Without cache:

```text
Client
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

With cache:

```text
Client
  |
  v
Backend
  |
  v
Redis Cache
  |
  +---- Cache Hit ----> Response
  |
  +---- Cache Miss ---> Database
                           |
                           v
                       Save Cache
                           |
                           v
                        Response
```

## 54. Why Use Caching?

- Reduce database load.
- Improve response time.
- Reduce repeated computation.
- Handle traffic spikes.
- Reduce infrastructure costs in suitable workloads.

## 55. Cache-Aside Pattern

The application checks the cache first.

If the data is missing, it reads from the database and stores the result in the cache.

Example:

```javascript
async function getUser(userId) {
  const cacheKey = `user:${userId}`;

  const cachedUser = await redis.get(cacheKey);

  if (cachedUser) {
    return JSON.parse(cachedUser);
  }

  const user = await database.users.findById(userId);

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

This is a simplified example. Production code should also handle serialization errors, cache failures and stale data.

## 56. Other Caching Strategies

### Read-Through Cache

The cache layer loads missing data from the database.

### Write-Through Cache

Data is written to the cache and the underlying data store as part of the write flow.

### Write-Behind Cache

The cache accepts writes and updates the underlying data store asynchronously.

This requires careful durability and failure handling.

### Cache Strategies Comparison

| Strategy | Read Behavior | Write Behavior |
|---|---|---|
| Cache-Aside | Application checks cache | Application updates database and invalidates/updates cache |
| Read-Through | Cache loads missing data | Usually independent |
| Write-Through | Cache serves reads | Writes propagate to underlying store |
| Write-Behind | Cache serves reads | Underlying write happens asynchronously |

---

# PART 24: CACHE INVALIDATION

## 57. What Is Cache Invalidation?

Cache Invalidation is the process of removing or updating cached data when the underlying source changes.

Example:

```text
Product Price = ₹500
```

The cache stores:

```text
product:101 -> ₹500
```

The administrator changes the price:

```text
Product Price = ₹600
```

If the cache is not updated or invalidated, users may continue seeing ₹500.

### Common Cache Invalidation Strategies

1. TTL expiration.
2. Explicit deletion.
3. Write-through updates.
4. Event-driven invalidation.
5. Versioned cache keys.

Example:

```javascript
await redis.del("product:101");
```

### Why Is Cache Invalidation Difficult?

Because cached data may exist in:

- Redis.
- Application memory.
- Browser storage.
- CDN.
- Reverse proxy.
- Database replicas.

A change in one layer may not automatically update the others.

---

# PART 25: REDIS ARCHITECTURE

## 58. What Is Redis?

Redis is an in-memory data store that supports several data structures and messaging capabilities.

Common uses:

- Caching.
- Sessions.
- Rate limiting.
- Distributed coordination.
- Queues.
- Pub/Sub.
- Real-time presence.

## 59. Redis Data Structures

| Data Structure | Use Case |
|---|---|
| String | Cache values, counters |
| Hash | User profiles |
| List | Queues and ordered collections |
| Set | Unique values |
| Sorted Set | Leaderboards |
| Stream | Event streams |
| Bitmap | Compact Boolean tracking |
| HyperLogLog | Approximate cardinality |

### Example: String

```redis
SET user:101 "Riyaz"

GET user:101
```

### Example: Hash

```redis
HSET user:101 name "Riyaz" email "user@example.com"

HGETALL user:101
```

### Example: Sorted Set

```redis
ZADD leaderboard 100 user:101

ZADD leaderboard 200 user:102

ZREVRANGE leaderboard 0 9 WITHSCORES
```

### Redis vs Database

Redis and a relational database solve different problems.

Redis is not automatically a replacement for MySQL or PostgreSQL.

A common architecture is:

```text
Application
    |
    v
Redis Cache
    |
    v
MySQL Database
```

---

# PART 26: CDN ARCHITECTURE

## 60. What Is a CDN?

CDN stands for Content Delivery Network.

A CDN distributes content across geographically distributed edge locations.

When users request static assets, the CDN can serve them from a nearby edge location.

### Architecture

```text
               User
                 |
                 v
             CDN Edge
                 |
          +------+------+
          |             |
       Cache Hit     Cache Miss
          |             |
          v             v
       Response      Origin Server
```

### What Can a CDN Cache?

- Images.
- CSS.
- JavaScript.
- Fonts.
- Public static files.
- Selected API responses.

### Advantages

- Lower latency.
- Reduced origin-server load.
- Better global delivery.
- Improved availability for cached assets.
- Bandwidth optimization.

### CDN vs Redis Cache

| CDN | Redis |
|---|---|
| Distributed edge caching | Application data caching |
| Near users geographically | Usually close to backend services |
| Static assets and selected responses | Sessions, objects, counters |
| HTTP caching mechanisms | Data structure operations |

---

# PART 27: MESSAGE QUEUES

## 61. What Is a Message Queue?

A Message Queue allows services to communicate asynchronously.

Example:

A user places an order.

Instead of completing every operation inside the HTTP request:

1. Save the order.
2. Publish a background job.
3. Worker processes the job.
4. Notification service sends an update.

```text
       Order API
           |
           v
       Message Queue
           |
      +----+----+
      |         |
      v         v
   Worker A  Worker B
      |         |
      v         v
   Email     Analytics
```

### Benefits

- Decoupling.
- Background processing.
- Load buffering.
- Retry handling.
- Independent worker scaling.

### Important Queue Concepts

- Producer.
- Consumer.
- Queue.
- Acknowledgement.
- Retry.
- Dead-letter queue.
- Prefetch.
- Message durability.
- Idempotent processing.

---

# PART 28: EVENT-DRIVEN ARCHITECTURE

## 62. What Is Event-Driven Architecture?

Event-Driven Architecture allows components to communicate through events.

Example:

```text
Order Service
     |
     v
OrderCreated Event
     |
     v
Message Broker
     |
     +-------------+
     |             |
     v             v
Payment Service  Notification Service
```

The Order Service does not need to directly invoke every downstream service.

### Event vs Command

| Event | Command |
|---|---|
| Describes something that happened | Requests an operation |
| Past-tense naming is common | Imperative naming is common |
| Example: OrderCreated | Example: CreateOrder |
| May have many subscribers | Usually has an intended handler |

---

# PART 29: SERVICE DISCOVERY

## 63. What Is Service Discovery?

Service Discovery allows services to find the network locations of other services dynamically.

Suppose you have three instances of User Service.

```text
User Service A: 10.0.0.1
User Service B: 10.0.0.2
User Service C: 10.0.0.3
```

The Order Service needs to discover a healthy User Service instance.

A service discovery system provides this information.

### Architecture

```text
         Service Registry
                |
       +--------+--------+
       |                 |
       v                 v
   User Service      Order Service
```

### Common Tools

- Kubernetes Services and DNS.
- Consul.
- AWS Cloud Map.
- Eureka.

### Why Is It Required?

In dynamic environments, service instances may:

- Start.
- Stop.
- Restart.
- Change IP addresses.
- Scale automatically.

Hardcoding service IP addresses is fragile.

---

# PART 30: RESILIENCE PATTERNS

## 64. Circuit Breaker Pattern

A Circuit Breaker prevents repeated calls to a failing dependency.

Imagine your application depends on a payment service.

If the payment service is unavailable, continuously sending requests can make the situation worse.

A circuit breaker has three common states:

| State | Meaning |
|---|---|
| Closed | Requests are allowed |
| Open | Requests are blocked temporarily |
| Half-Open | A limited number of test requests are allowed |

### Architecture

```text
       Application
            |
            v
      Circuit Breaker
            |
      +-----+-----+
      |           |
      v           v
    Closed       Open
      |           |
      v           v
   Service    Fast Failure
```

### Example Flow

1. Payment service works normally.
2. Repeated requests fail.
3. Circuit opens.
4. Requests are rejected or handled through fallback behavior.
5. After a configured delay, circuit becomes half-open.
6. A test request is sent.
7. If successful, circuit closes.

---

## 65. Retry Pattern

The Retry Pattern attempts an operation again after a failure.

Example:

```javascript
async function retry(
  operation,
  attempts = 3
) {
  for (let attempt = 1; attempt <= attempts; attempt++) {
    try {
      return await operation();
    } catch (error) {
      if (attempt === attempts) {
        throw error;
      }

      const delay = 1000 * 2 ** (attempt - 1);

      await new Promise(resolve =>
        setTimeout(resolve, delay)
      );
    }
  }
}
```

### Retry Best Practices

- Retry only transient failures.
- Use exponential backoff.
- Add jitter.
- Limit attempts.
- Set timeouts.
- Avoid retrying non-idempotent operations blindly.

### Example

Retrying a temporary network timeout may be appropriate.

Retrying an invalid password error is not useful.

---

## 66. Bulkhead Pattern

The Bulkhead Pattern isolates resources so that a failure in one component does not consume all resources required by others.

The name comes from compartments in ships.

Example:

```text
             Application
                  |
        +---------+---------+
        |                   |
        v                   v
    Payment Pool        Email Pool
        |                   |
        v                   v
    Payment Calls       Email Calls
```

If email requests become slow, they should not consume all resources needed for payment processing.

### Techniques

- Separate thread or worker pools.
- Independent connection pools.
- Queue isolation.
- Resource quotas.
- Separate service instances.

---

# PART 31: RATE LIMITING

## 67. What Is Rate Limiting?

Rate Limiting restricts how many requests a user or client can make during a specific period.

Example:

```text
Maximum 100 requests per minute.
```

### Why Is It Important?

- Prevent abuse.
- Reduce server overload.
- Protect APIs.
- Control resource consumption.
- Reduce denial-of-service exposure.

## 68. Rate Limiting Algorithms

### 1. Fixed Window

Counts requests during a fixed time interval.

```text
10:00 - 10:01 -> Maximum 100 requests
10:01 - 10:02 -> Maximum 100 requests
```

Problem: Bursts near window boundaries.

### 2. Sliding Window Log

Stores request timestamps and checks which requests fall within the current window.

### 3. Sliding Window Counter

Approximates a rolling window using current and previous window counts.

### 4. Token Bucket

Tokens are added at a configured rate.

Each request consumes a token.

### 5. Leaky Bucket

Requests enter a queue and are processed at a controlled rate.

### Comparison

| Algorithm | Main Characteristic |
|---|---|
| Fixed Window | Simple counter per interval |
| Sliding Window Log | Accurate rolling-window tracking |
| Sliding Window Counter | Lower-memory approximation |
| Token Bucket | Allows controlled bursts |
| Leaky Bucket | Smooths outgoing request rate |

---

# PART 32: IDEMPOTENCY

## 69. What Is Idempotency?

An operation is idempotent if performing it multiple times produces the same intended result as performing it once.

Example:

```http
PUT /api/users/101
```

Setting a user's name to "John" repeatedly can be idempotent.

However:

```http
POST /api/payments
```

Repeatedly submitting the same payment request may create duplicate charges unless the operation is protected.

## 70. Idempotency Keys

An Idempotency Key uniquely identifies a logical operation.

Client sends:

```http
POST /api/payments
Idempotency-Key: payment-request-abc123
```

The server:

1. Checks whether the key has already been processed.
2. If yes, returns the stored result.
3. If no, processes the operation.
4. Stores the result associated with the key.

### Example Database Table

```text
idempotency_records
-------------------
id
idempotency_key
request_hash
response
status
created_at
```

### Important

A robust implementation should:

- Enforce uniqueness of the key.
- Verify the request matches the original request.
- Handle concurrent duplicate submissions.
- Store results transactionally where possible.
- Define key expiration.
- Avoid processing the same business operation twice.

---

# PART 33: DISTRIBUTED LOCKING

## 71. What Is a Distributed Lock?

A Distributed Lock coordinates access to a shared resource across multiple processes or servers.

Example:

Two application instances try to process the same scheduled job.

Without coordination, both may process it.

```text
Server A ----+
             |
             v
        Shared Resource
             ^
             |
Server B ----+
```

A distributed lock helps ensure that only one eligible process holds the lock at a time.

### Redis Lock Example

A simplified Redis command:

```redis
SET lock:job-101 unique-token NX PX 10000
```

Meaning:

- `NX`: Only set if the key does not exist.
- `PX 10000`: Set a 10-second expiration.

### Important Production Considerations

- Use unique lock tokens.
- Release only the lock you own.
- Use atomic compare-and-delete logic.
- Consider lock expiration.
- Handle process pauses.
- Consider fencing tokens for protected resources.
- Do not assume a distributed lock automatically guarantees correctness under every failure mode.

A lock may expire while the original worker is still running. The system must protect against stale lock holders.

---

# PART 34: LEADER ELECTION

## 72. What Is Leader Election?

Leader Election is a process through which distributed nodes select one node to coordinate a particular operation.

Example:

```text
       Distributed Cluster
                |
       +--------+--------+
       |        |        |
       v        v        v
     Node A   Node B   Node C
       |
       v
     Leader
```

The leader may coordinate:

- Scheduled jobs.
- Cluster operations.
- Metadata changes.
- Partition assignment.
- Background maintenance.

### Why Is Leader Election Needed?

If every server performs the same scheduled job independently, the job may execute multiple times.

A leader-election mechanism can coordinate which instance performs it.

### Important

Leader election requires careful handling of:

- Node failures.
- Lease expiration.
- Network partitions.
- Split-brain scenarios.
- Fencing tokens.

---

# PART 35: CONSISTENT HASHING

## 73. What Is Consistent Hashing?

Consistent Hashing is a technique for distributing keys across multiple nodes while minimizing key redistribution when nodes are added or removed.

Traditional hashing:

```javascript
const server = hash(key) % serverCount;
```

If the server count changes, many keys may map to different servers.

Consistent hashing reduces the amount of remapping.

### Hash Ring

```text
             Node A
               |
         +-----------+
        /             \
       /               \
 Node D                 Node B
       \               /
        \             /
         +-----------+
               |
             Node C
```

Each node and key is mapped to a position on a logical ring.

A key is assigned to the next node clockwise.

### Virtual Nodes

A physical server may be represented by multiple positions on the ring.

This helps improve load distribution.

### Use Cases

- Distributed caching.
- Sharded storage.
- Request routing.
- Distributed databases.

---

# PART 36: OBSERVABILITY

## 74. What Is Observability?

Observability is the ability to understand a system's internal behavior from its external outputs.

The three major pillars are:

1. Logs.
2. Metrics.
3. Traces.

## 75. Logs

Logs record events that happen inside an application.

Example:

```javascript
console.log("Order created", {
  orderId: "101",
  userId: "501"
});
```

Production systems should use structured logging.

Example JSON:

```json
{
  "level": "info",
  "event": "order_created",
  "orderId": "101",
  "userId": "501",
  "timestamp": "2026-11-25T18:00:00Z"
}
```

Avoid logging passwords, access tokens or unnecessary personal information.

## 76. Metrics

Metrics are numerical measurements of system behavior.

Examples:

- CPU utilization.
- Memory usage.
- Request count.
- Error rate.
- Response latency.
- Database connection count.
- Queue backlog.

## 77. Traces

Distributed Tracing follows a request across multiple services.

Example:

```text
Frontend
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

A trace helps identify where time was spent and where a failure occurred.

---

# PART 37: DISTRIBUTED TRACING

## 78. What Is Distributed Tracing?

Distributed Tracing records the journey of a request across multiple services.

Each operation is represented by a span.

Related spans belong to a trace.

Example:

```text
Trace: Create Order

Span 1: API Gateway          20 ms
Span 2: Order Service        50 ms
Span 3: Inventory Service    80 ms
Span 4: Payment Service     200 ms
Span 5: Database             30 ms
```

The trace helps engineers identify the slow component.

### Important Concepts

| Term | Meaning |
|---|---|
| Trace | Complete request journey |
| Span | Individual operation |
| Trace ID | Identifier for the full trace |
| Span ID | Identifier for one operation |
| Parent Span | Operation that initiated another span |
| Context Propagation | Passing tracing information between services |

### Tools

- OpenTelemetry.
- Jaeger.
- Grafana Tempo.
- Zipkin.

---

# PART 38: HIGH AVAILABILITY ARCHITECTURE

## 79. How to Design a Highly Available Application?

A high-availability architecture requires redundancy and failure handling across critical components.

Example:

```text
                    Internet
                       |
                       v
                  Load Balancer
                       |
             +---------+---------+
             |                   |
             v                   v
        App Server A        App Server B
             |                   |
             +---------+---------+
                       |
                       v
                  Database Cluster
                       |
               +-------+-------+
               |               |
               v               v
           Primary          Replica
```

### Important Requirements

- Multiple application instances.
- Health checks.
- Load balancing.
- Database replication.
- Automated failover.
- Redundant networking.
- Backup strategy.
- Monitoring and alerting.
- Graceful degradation.

### What Is Graceful Degradation?

Graceful Degradation means preserving essential functionality when some non-essential components fail.

Example:

If product recommendations fail, the e-commerce application should still allow users to search and purchase products.

---

# PART 39: DISASTER RECOVERY

## 80. What Is Disaster Recovery?

Disaster Recovery (DR) is the process of restoring systems and data after a major failure.

Examples:

- Data center failure.
- Accidental database deletion.
- Ransomware incident.
- Regional cloud outage.
- Storage corruption.

## 81. Backup vs Disaster Recovery

| Backup | Disaster Recovery |
|---|---|
| Creates recoverable copies of data | Restores services after disruption |
| Focuses on data protection | Focuses on service restoration |
| One part of DR | Includes backups, infrastructure and procedures |

## 82. RPO and RTO

### RPO – Recovery Point Objective

RPO defines the maximum acceptable amount of data loss measured in time.

Example:

```text
RPO = 15 minutes
```

The recovery strategy should limit data loss to approximately 15 minutes or less.

### RTO – Recovery Time Objective

RTO defines the maximum acceptable time to restore the service.

Example:

```text
RTO = 1 hour
```

The service should be restored within one hour.

### RPO vs RTO

| RPO | RTO |
|---|---|
| Maximum acceptable data loss | Maximum acceptable recovery duration |
| Measured in time | Measured in time |
| Focuses on data | Focuses on service restoration |

### Disaster Recovery Strategies

- Regular backups.
- Point-in-time recovery.
- Database replication.
- Cross-region backups.
- Infrastructure as Code.
- Automated restoration.
- Recovery testing.

**Important:** A backup that has never been tested is not a proven recovery strategy.

---

# PART 40: DESIGNING A URL SHORTENER

## 83. Problem Statement

Design a service similar to Bitly.

Users submit long URLs and receive shorter URLs.

Example:

```text
Original:

https://example.com/products/electronics/mobile/iphone

Short:

https://short.example/abc123
```

## 84. Functional Requirements

- Create short URL.
- Redirect short URL.
- Support custom aliases.
- Support expiration.
- Track clicks.
- Support user accounts.
- Allow URL management.

## 85. Non-Functional Requirements

- Fast redirects.
- High availability.
- Unique short codes.
- Scalability.
- Abuse prevention.
- Reliable analytics.

## 86. Database Design

```text
urls
----
id
short_code
original_url
user_id
created_at
expires_at
```

## 87. Short Code Generation

A unique ID can be converted into a Base62 string.

Base62 uses:

```text
0-9
a-z
A-Z
```

There are 62 possible characters.

Example:

```text
ID: 125000

Base62: W7G
```

The actual code depends on the encoding algorithm.

### API Design

Create:

```http
POST /api/urls
```

Request:

```json
{
  "originalUrl": "https://example.com/products/101"
}
```

Response:

```json
{
  "shortUrl": "https://short.example/abc123"
}
```

Redirect:

```http
GET /abc123
```

Server:

1. Looks up the short code.
2. Checks expiration and status.
3. Records analytics asynchronously.
4. Redirects to the original URL.

## 88. URL Shortener Architecture

```text
             Client
                |
                v
           API Gateway
                |
                v
          URL Service
                |
         +------+------+
         |             |
         v             v
       Redis         Database
         |
         v
    Cached URL Mapping
```

### Optimization

Use Redis for frequently accessed URL mappings.

Store click analytics asynchronously using a queue or event stream.

### Important Security Considerations

- Validate URL schemes.
- Prevent dangerous redirects.
- Avoid internal network access through URL preview features.
- Apply abuse detection.
- Prevent malicious URL distribution.

---

# PART 41: DESIGNING WHATSAPP

## 89. Problem Statement

Design a messaging application similar to WhatsApp.

### Functional Requirements

- User registration.
- One-to-one messaging.
- Group messaging.
- Online status.
- Typing indicators.
- Message delivery status.
- Read receipts.
- Media sharing.
- Push notifications.

### Non-Functional Requirements

- Low message latency.
- Reliable message storage.
- High availability.
- Secure communication.
- Horizontal scalability.
- Support for multiple devices.

## 90. High-Level Architecture

```text
              Mobile / Web Clients
                       |
              +--------+--------+
              |                 |
              v                 v
            REST API         WebSocket
              |                 |
              v                 v
          API Gateway      Chat Gateway
              |                 |
       +------+-------+         |
       |              |         |
       v              v         v
   User Service   Chat Service |
       |              |         |
       v              v         |
   User Database   Message DB   |
                      |         |
                      v         |
                    Redis <-----+
                      |
                      v
                Message Broker
                      |
             +--------+--------+
             |                 |
             v                 v
       Notification       Analytics
         Service           Service
```

## 91. Message Sending Workflow

1. User A sends a message.
2. Chat Gateway authenticates the connection.
3. Chat Service validates the conversation.
4. Message is persisted.
5. An event is published.
6. Recipient's connected devices receive the message.
7. If the recipient is offline, the application stores the message for later synchronization.
8. Delivery and read states are updated separately.

### Important Design Considerations

- Message IDs.
- Conversation IDs.
- Ordering.
- Idempotency.
- Message persistence.
- Delivery acknowledgements.
- Offline synchronization.
- End-to-end encryption.
- Multi-device sessions.

A production messaging system needs more than a WebSocket connection. Durable storage and synchronization are essential.

---

# PART 42: DESIGNING YOUTUBE

## 92. Problem Statement

Design a video-sharing platform.

### Functional Requirements

- Upload videos.
- Watch videos.
- Search videos.
- Like and comment.
- Subscribe to channels.
- Generate thumbnails.
- Transcode videos.
- Track view counts.

### Non-Functional Requirements

- High availability.
- Large file storage.
- Global video delivery.
- Efficient video processing.
- Low playback latency.
- Scalable search.

## 93. Architecture

```text
                 Users
                   |
                   v
                CDN
                   |
                   v
             Video Platform
                   |
        +----------+----------+
        |          |          |
        v          v          v
      User      Video      Search
     Service   Service     Service
                   |
                   v
                Upload
                   |
                   v
               Object Storage
                   |
                   v
              Message Queue
                   |
                   v
              Video Workers
                   |
          +--------+--------+
          |                 |
          v                 v
      Transcoding       Thumbnail
          |             Generation
          +--------+--------+
                   |
                   v
               CDN Storage
```

### Video Upload Workflow

1. User requests an upload.
2. Backend authorizes the user.
3. Backend generates a signed upload URL.
4. Client uploads directly to object storage.
5. Upload completion triggers a processing event.
6. Worker transcodes the video.
7. Thumbnails are generated.
8. Metadata is updated.
9. Video becomes available for playback.

### Why Use Object Storage?

Video files are large and should generally not be stored directly inside ordinary relational database rows.

Object storage provides a suitable architecture for large media objects.

Examples:

- AWS S3.
- Google Cloud Storage.
- Azure Blob Storage.

---

# PART 43: DESIGNING E-COMMERCE SYSTEMS

## 94. Problem Statement

Design an e-commerce platform.

### Functional Requirements

- User authentication.
- Product catalog.
- Product search.
- Shopping cart.
- Checkout.
- Payment.
- Inventory management.
- Order tracking.
- Notifications.

## 95. Architecture

```text
                  Frontend
                     |
                     v
                 API Gateway
                     |
       +-------------+-------------+
       |             |             |
       v             v             v
   User Service  Product Service  Cart Service
       |             |             |
       v             v             v
     MySQL         MongoDB        Redis
                     |
                     v
                Order Service
                     |
          +----------+----------+
          |                     |
          v                     v
    Inventory Service      Payment Service
          |                     |
          v                     v
       Database             Payment Gateway
          |
          v
       Message Broker
          |
          v
    Notification Service
```

### Checkout Workflow

1. Validate cart.
2. Check product availability.
3. Reserve inventory.
4. Create order.
5. Process payment.
6. Confirm order.
7. Publish order-confirmed event.
8. Send notification.

### Important

Use a Saga-style workflow or another suitable distributed transaction strategy when multiple services participate in checkout.

Avoid assuming that a single SQL transaction can atomically coordinate unrelated databases and external payment gateways.

---

# PART 44: DESIGNING NOTIFICATION SYSTEMS

## 96. Problem Statement

Design a system that sends notifications through multiple channels.

Channels:

- Email.
- SMS.
- Push notifications.
- In-app notifications.
- WebSocket notifications.

## 97. Architecture

```text
          Business Services
                  |
                  v
             Event Broker
                  |
                  v
          Notification Service
                  |
          +-------+-------+
          |       |       |
          v       v       v
        Email    SMS     Push
       Worker   Worker   Worker
          |       |       |
          v       v       v
       Provider Provider Provider
```

### Important Features

- User notification preferences.
- Retry handling.
- Delivery tracking.
- Template management.
- Rate limiting.
- Dead-letter queues.
- Duplicate prevention.
- Notification history.

### Why Separate Notification Workers?

Sending emails and push notifications can be slow or fail because of external providers.

Background workers prevent these operations from blocking important business requests.

---

# PART 45: DESIGNING REAL-TIME CHAT SYSTEMS

## 98. Real-Time Chat Architecture

A chat system typically needs:

- WebSocket gateway.
- Authentication.
- Conversation management.
- Message persistence.
- Presence tracking.
- Notification processing.
- Media storage.

```text
             Client Applications
                      |
                      v
               Load Balancer
                      |
             +--------+--------+
             |                 |
             v                 v
        Chat Server A     Chat Server B
             |                 |
             +--------+--------+
                      |
                    Redis
                      |
             +--------+--------+
             |                 |
             v                 v
         Message DB       Message Broker
                               |
                               v
                       Notification Workers
```

### Message Delivery States

A useful message state model:

```text
PENDING
   |
   v
SENT
   |
   v
DELIVERED
   |
   v
READ
```

The exact states and transitions depend on the application.

### Important

Do not treat successful WebSocket transmission as proof that a recipient has read a message.

These are separate business events.

---

# PART 46: DESIGNING PAYMENT SYSTEMS

## 99. Problem Statement

Design a payment processing system.

### Functional Requirements

- Create payment.
- Authorize payment.
- Capture payment.
- Refund payment.
- Track payment status.
- Generate transaction history.
- Handle failed payments.

### Non-Functional Requirements

- High reliability.
- Strong auditability.
- Security.
- Idempotency.
- Accurate financial records.
- Controlled access.
- Reconciliation.

## 100. Architecture

```text
                Client
                  |
                  v
              API Gateway
                  |
                  v
             Payment Service
                  |
          +-------+-------+
          |               |
          v               v
      Payment DB      Payment Gateway
          |
          v
      Event Broker
          |
          v
   Notification Service
```

### Payment Workflow

1. Client requests payment.
2. Backend validates the request.
3. Backend creates a payment record.
4. Payment gateway processes the transaction.
5. Backend verifies the gateway response or webhook.
6. Payment status is updated.
7. An event is published.
8. Order Service receives payment status.
9. Notification Service informs the user.

### Critical Payment Design Rules

- Use idempotency keys.
- Never trust frontend payment status.
- Verify payment webhooks.
- Validate webhook signatures.
- Store transaction references.
- Maintain audit trails.
- Handle delayed and duplicate webhooks.
- Reconcile internal records with payment provider records.
- Never store raw card security codes.

---

# PART 47: SYSTEM DESIGN INTERVIEW FRAMEWORK

## 101. How to Approach a System Design Interview

Use the following structured process.

### Step 1: Understand the Problem

Ask:

- What are we building?
- Who are the users?
- What are the main use cases?
- What is out of scope?

### Step 2: Identify Functional Requirements

List the essential system features.

### Step 3: Identify Non-Functional Requirements

Define:

- Availability.
- Scalability.
- Performance.
- Security.
- Reliability.
- Consistency.

### Step 4: Estimate Traffic

Estimate:

- Daily active users.
- Requests per second.
- Read/write ratio.
- Storage requirements.
- Bandwidth.
- Peak traffic.

### Step 5: Define APIs

Example:

```http
POST /api/orders

GET /api/orders/:id

GET /api/products

POST /api/payments
```

### Step 6: Design Database

Identify:

- Entities.
- Relationships.
- Indexes.
- Constraints.
- Partitioning needs.

### Step 7: Draw High-Level Architecture

Show:

- Clients.
- Gateway.
- Services.
- Databases.
- Cache.
- Message broker.
- Storage.

### Step 8: Identify Bottlenecks

Consider:

- Database load.
- Hot keys.
- Network latency.
- Slow external services.
- Queue backlogs.

### Step 9: Improve Scalability and Reliability

Consider:

- Caching.
- Replication.
- Sharding.
- Load balancing.
- Retries.
- Circuit breakers.
- Redundancy.

### Step 10: Discuss Trade-Offs

Explain why you selected a particular approach and what you sacrificed.

**Important:** There is rarely one universally correct system design. A good design is appropriate for its requirements, constraints and expected workload.

---

# PART 48: COMPLETE ENTERPRISE PROJECT

## 102. Project: Enterprise Expense Management Platform

Build a scalable expense management application.

This project combines the major concepts from this phase.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + React + TypeScript |
| Backend | Node.js + Express |
| Database | MySQL or PostgreSQL |
| ORM | Prisma or Sequelize |
| Cache | Redis |
| Message Broker | RabbitMQ |
| Real-Time | Socket.IO |
| File Storage | AWS S3 |
| Authentication | Secure sessions or JWT |
| Deployment | Docker |
| Reverse Proxy | Nginx |
| Infrastructure | AWS |
| Monitoring | Prometheus + Grafana |
| Testing | Vitest + Playwright |

## 103. Business Modules

```text
Expense Management Platform
             |
   +---------+---------+
   |         |         |
   v         v         v
  User     Expense   Approval
 Module    Module    Module
   |         |         |
   +---------+---------+
             |
             v
        Payment Module
             |
             v
       Notification Module
```

### User Module

- Registration.
- Login.
- Roles.
- Permissions.
- User profiles.

### Expense Module

- Create expense.
- Update expense.
- Upload receipts.
- Categorize expenses.
- Search expenses.
- Generate reports.

### Approval Module

- Manager approval.
- Rejection.
- Multi-level approval.
- Approval history.

### Payment Module

- Reimbursement.
- Payment tracking.
- Payment history.
- Reconciliation.

### Notification Module

- Email notifications.
- In-app notifications.
- Real-time updates.
- Approval reminders.

---

## 104. High-Level System Architecture

```text
                     Users
                       |
                       v
                Next.js Frontend
                       |
                       v
                  API Gateway
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
    User Service  Expense Service  Approval Service
        |              |              |
        v              v              v
     Database       Database       Database
                       |
             +---------+---------+
             |                   |
             v                   v
          Redis              RabbitMQ
             |                   |
             v                   v
       Socket.IO Server     Background Workers
             |                   |
             v                   v
      Real-Time Updates     Notifications
                                 |
                                 v
                            Email/SMS
```

This is a logical architecture. You do not necessarily need a separate database for every service in the initial implementation.

---

## 105. Example: Expense Approval Workflow

Suppose an employee creates an expense of ₹10,000.

Workflow:

```text
Employee Creates Expense
          |
          v
Expense Service
          |
          v
Save Expense
          |
          v
Publish ExpenseCreated
          |
          v
Approval Service
          |
          v
Manager Receives Notification
          |
          v
Manager Approves
          |
          v
ExpenseApproved Event
          |
          +-----------------+
          |                 |
          v                 v
Payment Service      Notification Service
          |                 |
          v                 v
Process Payment     Notify Employee
```

### What Concepts Are Being Used?

| Concept | Implementation |
|---|---|
| REST API | Expense creation |
| Database transaction | Saving expense |
| Message Queue | Publishing business events |
| Event-Driven Architecture | Approval workflow |
| Redis | Caching |
| Socket.IO | Real-time updates |
| Saga | Multi-step reimbursement workflow |
| Idempotency | Prevent duplicate reimbursement |
| Observability | Track workflow execution |
| Horizontal Scaling | Multiple backend instances |

---

# PART 49: SYSTEM DESIGN INTERVIEW QUESTIONS

## Beginner-Level

### Q1. What is System Design?

The process of defining software architecture, components, data storage and communication mechanisms.

### Q2. What is HLD?

High-Level Design describes the overall architecture.

### Q3. What is LLD?

Low-Level Design describes the internal implementation of components.

### Q4. What are Functional Requirements?

Requirements describing what a system must do.

### Q5. What are Non-Functional Requirements?

Requirements describing quality attributes such as performance, availability and security.

### Q6. What is Scalability?

The ability to handle increasing workloads.

### Q7. What is Vertical Scaling?

Increasing the resources of an existing server.

### Q8. What is Horizontal Scaling?

Adding more servers or instances.

### Q9. What is Availability?

The degree to which a system is operational and accessible.

### Q10. What is Reliability?

The ability to perform intended functions correctly over time.

## Intermediate-Level

### Q11. What is Fault Tolerance?

The ability to continue operating despite component failures.

### Q12. What is Load Balancing?

Distributing traffic across multiple servers.

### Q13. What is a Reverse Proxy?

A server that forwards client requests to backend servers.

### Q14. What is an API Gateway?

An entry point that routes and manages API requests.

### Q15. What is a Monolithic Application?

An application deployed as one primary unit.

### Q16. What is Microservices Architecture?

An architecture composed of independently deployable services.

### Q17. What is a Modular Monolith?

A single deployable application organized into strongly separated modules.

### Q18. What is Latency?

The time taken for an operation to complete.

### Q19. What is Throughput?

The amount of work completed per unit time.

### Q20. What is a CDN?

A geographically distributed content delivery network.

## Advanced-Level

### Q21. What is the CAP Theorem?

A theorem describing consistency and availability trade-offs during network partitions in distributed systems.

### Q22. What is PACELC?

A model describing partition-time consistency/availability and normal-operation latency/consistency trade-offs.

### Q23. What is Eventual Consistency?

A model in which replicas may temporarily differ but eventually converge.

### Q24. What is Database Replication?

Maintaining copies of data across multiple database instances.

### Q25. What is Database Sharding?

Distributing subsets of data across multiple database instances.

### Q26. What is Database Partitioning?

Dividing a table or dataset into smaller partitions.

### Q27. What is Denormalization?

Intentionally duplicating or precomputing data to optimize access patterns.

### Q28. What is Cache-Aside?

A caching pattern in which the application checks the cache and loads missing data from the database.

### Q29. What is Cache Invalidation?

Removing or updating cached data when its source changes.

### Q30. What is the Saga Pattern?

A distributed transaction pattern composed of local transactions and compensating actions.

## Expert-Level

### Q31. What is Two-Phase Commit?

A protocol coordinating distributed transactions through prepare and commit/abort phases.

### Q32. What is a Circuit Breaker?

A pattern that stops repeated calls to a failing dependency.

### Q33. What is the Bulkhead Pattern?

A resource-isolation technique that prevents failures in one component from consuming resources needed by others.

### Q34. What is Idempotency?

The property that repeating an operation produces the same intended result as performing it once.

### Q35. What is Distributed Locking?

Coordinating access to shared resources across multiple processes or machines.

### Q36. What is Leader Election?

Selecting one node to coordinate operations in a distributed system.

### Q37. What is Consistent Hashing?

A key-distribution technique that minimizes remapping when nodes change.

### Q38. What is Service Discovery?

A mechanism for locating available service instances dynamically.

### Q39. What is Distributed Tracing?

Tracking a request as it travels through multiple services.

### Q40. What is RPO?

Recovery Point Objective, the maximum acceptable data-loss interval.

## Real-World Design Questions

### Q41. How would you design a URL shortener?

Use a URL service, unique short-code generation, database mapping, Redis caching and redirect handling.

### Q42. How would you design WhatsApp?

Use real-time communication gateways, durable message storage, presence management, distributed messaging and notification services.

### Q43. How would you design YouTube?

Use object storage, video processing workers, metadata databases, search infrastructure and CDN delivery.

### Q44. How would you design an e-commerce application?

Use user, product, cart, order, inventory, payment and notification modules with appropriate transaction management.

### Q45. How would you design a payment system?

Use idempotency, transaction records, secure gateway integration, webhook verification and reconciliation.

### Q46. How would you scale a Node.js application?

Use multiple instances, load balancing, caching, database optimization, background workers and horizontal scaling.

### Q47. How would you handle database overload?

Optimize queries, add suitable indexes, introduce caching, use replicas, partition workloads and scale the database where appropriate.

### Q48. How would you prevent duplicate payments?

Use idempotency keys, database constraints, transactional state transitions and payment reconciliation.

### Q49. How would you handle a failed microservice?

Use timeouts, retries with backoff, circuit breakers, queues and graceful degradation.

### Q50. How would you design a highly available enterprise application?

Use redundant application instances, load balancing, replicated databases, fault isolation, monitoring, backups and tested disaster-recovery procedures.

---

# PART 50: HANDS-ON PRACTICAL ASSIGNMENTS

## Level 1 – System Design Fundamentals

| No. | Assignment | Expected Result |
|---:|---|---|
| 1 | Understand HLD and LLD | Design fundamentals |
| 2 | Identify functional requirements | Requirements document |
| 3 | Identify non-functional requirements | Quality attributes |
| 4 | Compare monolith and microservices | Architecture comparison |
| 5 | Design a basic blog application | HLD diagram |
| 6 | Understand vertical scaling | Infrastructure planning |
| 7 | Understand horizontal scaling | Scaling architecture |
| 8 | Calculate availability | SLA understanding |
| 9 | Understand latency and throughput | Performance analysis |
| 10 | Design a basic REST API system | API architecture |

## Level 2 – Infrastructure and Scalability

| No. | Assignment | Expected Result |
|---:|---|---|
| 11 | Configure Nginx reverse proxy | Reverse proxy |
| 12 | Configure load balancing | Traffic distribution |
| 13 | Implement health checks | Failure detection |
| 14 | Configure multiple Node.js instances | Horizontal scaling |
| 15 | Configure Redis caching | Cache implementation |
| 16 | Implement cache invalidation | Data freshness |
| 17 | Configure CDN for static assets | Edge delivery |
| 18 | Optimize database indexes | Query optimization |
| 19 | Configure database replication | Read scaling |
| 20 | Study sharding strategies | Data distribution |

## Level 3 – Distributed Systems

| No. | Assignment | Expected Result |
|---:|---|---|
| 21 | Understand CAP theorem | Distributed trade-offs |
| 22 | Understand PACELC | Consistency trade-offs |
| 23 | Implement idempotent API | Duplicate prevention |
| 24 | Implement retry mechanism | Failure recovery |
| 25 | Implement circuit breaker | Dependency protection |
| 26 | Implement rate limiting | Traffic control |
| 27 | Implement distributed locking | Resource coordination |
| 28 | Implement a background queue | Async processing |
| 29 | Implement Saga workflow | Distributed transaction |
| 30 | Implement service discovery | Dynamic service lookup |

## Level 4 – Enterprise Architecture

| No. | Assignment | Expected Result |
|---:|---|---|
| 31 | Build modular monolith | Modular backend |
| 32 | Design microservices architecture | Service boundaries |
| 33 | Implement API Gateway | Central API entry |
| 34 | Implement event-driven architecture | Event communication |
| 35 | Configure RabbitMQ | Message broker |
| 36 | Configure Kafka | Event streaming |
| 37 | Implement distributed tracing | Trace visibility |
| 38 | Implement centralized logging | Log aggregation |
| 39 | Configure monitoring | Metrics and alerts |
| 40 | Design disaster recovery | Recovery strategy |

## Level 5 – Real-World System Design

| No. | Assignment | Expected Result |
|---:|---|---|
| 41 | Design URL shortener | Scalable redirect system |
| 42 | Design WhatsApp | Real-time messaging architecture |
| 43 | Design YouTube | Video streaming architecture |
| 44 | Design e-commerce platform | Commerce architecture |
| 45 | Design notification service | Multi-channel notifications |
| 46 | Design payment platform | Reliable payment architecture |
| 47 | Design ride-booking application | Location-based distributed system |
| 48 | Design social media platform | Feed and social graph architecture |
| 49 | Design enterprise expense platform | Complete business architecture |
| 50 | Build the enterprise expense platform | Working full-stack system |

---

# PART 51: QUICK REVISION NOTES

| Concept | Meaning |
|---|---|
| System Design | Designing overall software architecture |
| HLD | High-Level Design |
| LLD | Low-Level Design |
| Functional Requirements | What a system does |
| Non-Functional Requirements | How well it operates |
| Scalability | Ability to handle growth |
| Vertical Scaling | Increasing server resources |
| Horizontal Scaling | Adding more servers |
| Availability | Service accessibility |
| Reliability | Correct operation over time |
| Fault Tolerance | Operating despite failures |
| Latency | Time taken for an operation |
| Throughput | Work completed per unit time |
| Load Balancer | Distributes traffic |
| Reverse Proxy | Forwards client requests to servers |
| API Gateway | Central API entry point |
| Monolith | Single deployable application |
| Modular Monolith | One deployment with clear module boundaries |
| Microservices | Independently deployable services |
| Distributed System | Multiple communicating computers |
| CAP Theorem | Partition-time consistency/availability trade-off |
| PACELC | Partition and normal-operation trade-offs |
| Strong Consistency | Reads observe latest committed data under defined guarantees |
| Eventual Consistency | Replicas eventually converge |
| 2PC | Two-Phase Commit |
| Saga | Local transactions with compensations |
| Replication | Maintaining data copies |
| Sharding | Distributing data across database instances |
| Partitioning | Dividing datasets into partitions |
| Indexing | Faster data lookup |
| Normalization | Reducing redundancy |
| Denormalization | Intentional data duplication |
| Caching | Storing frequently used data in a faster layer |
| Cache Invalidation | Removing or updating stale cached data |
| Redis | In-memory data store |
| CDN | Distributed content delivery network |
| Message Queue | Asynchronous messaging |
| Event-Driven Architecture | Communication through events |
| Service Discovery | Locating service instances |
| Circuit Breaker | Preventing repeated calls to failing dependencies |
| Retry | Repeating failed operations |
| Bulkhead | Isolating resources |
| Rate Limiting | Controlling request frequency |
| Idempotency | Safe repeated execution |
| Distributed Lock | Coordinating shared resource access |
| Leader Election | Selecting a coordinating node |
| Consistent Hashing | Stable key distribution |
| Observability | Understanding system behavior |
| Distributed Tracing | Following requests across services |
| RPO | Maximum acceptable data loss |
| RTO | Maximum acceptable recovery time |

---

# PART 52: PHASE 65 COMPLETION CHECKLIST

### System Design Fundamentals

- [ ] Understand System Design.
- [ ] Understand HLD.
- [ ] Understand LLD.
- [ ] Identify functional requirements.
- [ ] Identify non-functional requirements.
- [ ] Understand scalability.
- [ ] Understand availability.
- [ ] Understand reliability.
- [ ] Understand fault tolerance.
- [ ] Understand latency and throughput.

### Infrastructure

- [ ] Understand vertical scaling.
- [ ] Understand horizontal scaling.
- [ ] Understand load balancing.
- [ ] Understand reverse proxies.
- [ ] Understand API gateways.
- [ ] Understand CDN architecture.
- [ ] Understand high availability.

### Application Architecture

- [ ] Understand monolithic architecture.
- [ ] Understand modular monoliths.
- [ ] Understand microservices.
- [ ] Understand distributed systems.
- [ ] Understand service discovery.
- [ ] Understand event-driven architecture.

### Distributed Systems

- [ ] Understand CAP theorem.
- [ ] Understand PACELC.
- [ ] Understand consistency models.
- [ ] Understand distributed transactions.
- [ ] Understand Two-Phase Commit.
- [ ] Understand Saga Pattern.
- [ ] Understand distributed locking.
- [ ] Understand leader election.
- [ ] Understand consistent hashing.

### Database Architecture

- [ ] Understand database replication.
- [ ] Understand database sharding.
- [ ] Understand partitioning.
- [ ] Understand indexing.
- [ ] Understand SQL vs NoSQL.
- [ ] Understand normalization.
- [ ] Understand denormalization.
- [ ] Understand caching.
- [ ] Understand cache invalidation.

### Reliability and Security

- [ ] Understand circuit breakers.
- [ ] Understand retry strategies.
- [ ] Understand bulkheads.
- [ ] Understand rate limiting.
- [ ] Understand idempotency.
- [ ] Understand disaster recovery.
- [ ] Understand RPO and RTO.

### Enterprise Architecture

- [ ] Understand message queues.
- [ ] Understand RabbitMQ and Kafka.
- [ ] Understand observability.
- [ ] Understand distributed tracing.
- [ ] Understand high availability architecture.
- [ ] Understand production monitoring.
- [ ] Complete at least one system design exercise.

### Practical Development

- [ ] Design a URL shortener.
- [ ] Design a chat application.
- [ ] Design a video platform.
- [ ] Design an e-commerce platform.
- [ ] Design a payment system.
- [ ] Design an expense management platform.

---

# Phase 65 – Final Summary

System Design is one of the most important skills for an experienced backend and full-stack engineer.

Knowing how to write APIs, queries and components is not enough to build large-scale applications.

You must also understand:

1. How to design application architecture.
2. How to scale backend services.
3. How to distribute traffic.
4. How to manage database growth.
5. How to handle network failures.
6. How to maintain data consistency.
7. How to coordinate distributed transactions.
8. How to build fault-tolerant systems.
9. How to optimize latency and throughput.
10. How to monitor production systems.
11. How to design highly available infrastructure.
12. How to make architecture decisions based on trade-offs.

## Recommended Priority

| Priority | Topics |
|---|---|
| Essential | HLD, LLD, Requirements Engineering |
| Essential | Scalability and Load Balancing |
| Essential | Monolith vs Microservices |
| Essential | Database Design and Indexing |
| Essential | Caching and Redis |
| Essential | CAP Theorem and Consistency |
| Essential | Replication and Sharding |
| Important | Message Queues and Event-Driven Architecture |
| Important | Saga Pattern |
| Important | API Gateway and Service Discovery |
| Important | Circuit Breaker and Idempotency |
| Advanced | Distributed Locking |
| Advanced | Consistent Hashing |
| Advanced | Distributed Tracing |
| Advanced | Disaster Recovery |
| Expert | Large-Scale System Design |

---

# Official Documentation and Learning Resources

These are useful references for implementing the concepts covered in this phase.

1. **[AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)** – Reliability, security, performance and operational excellence.
2. **[Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework)** – Cloud architecture and system design principles.
3. **[Microsoft Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/)** – Enterprise architecture patterns.
4. **[Redis Documentation](https://redis.io/docs/latest/)** – Caching and distributed data structures.
5. **[MySQL Documentation](https://dev.mysql.com/doc/)** – Relational database architecture.
6. **[PostgreSQL Documentation](https://www.postgresql.org/docs/)** – Database internals and performance.
7. **[MongoDB Documentation](https://www.mongodb.com/docs/)** – Document database architecture and replication.
8. **[RabbitMQ Documentation](https://www.rabbitmq.com/docs)** – Messaging and distributed processing.
9. **[Apache Kafka Documentation](https://kafka.apache.org/documentation/)** – Distributed event streaming.
10. **[Kubernetes Documentation](https://kubernetes.io/docs/home/)** – Container orchestration.
11. **[OpenTelemetry Documentation](https://opentelemetry.io/docs/)** – Observability and distributed tracing.
12. **[Nginx Documentation](https://nginx.org/en/docs/)** – Reverse proxy and load balancing.

---

# Next Phase Preview

## Phase 66 – Advanced Database Engineering, Database Internals, Query Optimization and Distributed Database Systems

Topics planned:

1. Database Architecture Fundamentals.
2. Database Storage Engines.
3. Database Internals.
4. How Databases Store Data.
5. Pages and Blocks.
6. Heap Files.
7. B-Tree and B+ Tree.
8. Hash Indexing.
9. Clustered vs Non-Clustered Indexes.
10. Composite Indexes.
11. Covering Indexes.
12. Index Selectivity.
13. Query Execution Plans.
14. SQL Query Optimization.
15. Cost-Based Query Optimization.
16. Database Statistics.
17. Table Scans vs Index Scans.
18. Nested Loop Join.
19. Hash Join.
20. Merge Join.
21. Query Performance Analysis.
22. Transactions Internals.
23. ACID Properties.
24. Isolation Levels.
25. Dirty Reads.
26. Non-Repeatable Reads.
27. Phantom Reads.
28. MVCC.
29. Locking Mechanisms.
30. Deadlocks.
31. Optimistic vs Pessimistic Locking.
32. Write-Ahead Logging.
33. Checkpoints.
34. Crash Recovery.
35. Database Replication Internals.
36. Synchronous vs Asynchronous Replication.
37. Replication Lag.
38. Database Partitioning.
39. Database Sharding.
40. Shard Rebalancing.
41. Distributed SQL Databases.
42. Distributed NoSQL Databases.
43. Database Consistency Models.
44. Database Connection Pooling.
45. Database Connection Management.
46. Database Backup and Recovery.
47. Database Security.
48. Database Monitoring.
49. Database Performance Tuning.
50. Production Database Architecture.

**Say "OK, Phase 66" when you're ready to continue.**