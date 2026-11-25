# PHASE 77 – ADVANCED SYSTEM DESIGN, LARGE-SCALE DISTRIBUTED APPLICATIONS, HIGH AVAILABILITY AND GLOBAL SCALABILITY

**Level:** Expert  
**Category:** System Design, Distributed Systems, Backend Architecture, Cloud Architecture, Database Engineering  
**Prerequisites:** Node.js, Backend Development, Databases, Microservices, Docker, Kubernetes, Cloud Computing, API Design and Distributed Systems Fundamentals.

---

# 1. Learning Objectives

By the end of this phase, you should understand how to:

1. Design applications that support millions of users.
2. Estimate traffic, storage, bandwidth and infrastructure requirements.
3. Identify performance bottlenecks in distributed applications.
4. Design scalable load-balancing architectures.
5. Understand CDN, DNS and global traffic management.
6. Scale relational and NoSQL databases.
7. Implement database replication and sharding strategies.
8. Understand distributed consistency models.
9. Apply CAP and PACELC theorem concepts.
10. Understand distributed consensus and leader election.
11. Design distributed locks and coordination mechanisms.
12. Implement distributed caching and rate limiting.
13. Design fault-tolerant and highly available applications.
14. Build multi-region architectures.
15. Design disaster recovery strategies.
16. Understand the tradeoffs involved in large-scale system architecture.

---

# PART 1: ADVANCED SYSTEM DESIGN FUNDAMENTALS

## 2. What Is System Design?

System design is the process of defining the architecture, components, data flow, communication mechanisms and infrastructure required to build a software system.

It answers questions such as:

- How will the application handle 10 million users?
- How will the application handle 100,000 requests per second?
- How will data be stored and retrieved efficiently?
- What happens when a server crashes?
- How will multiple servers communicate?
- How will the system recover from a regional outage?
- How will the application maintain data consistency?
- How will the system scale as traffic increases?

### Example: Basic Application Architecture

A simple application might look like this:

```text
                 Users
                   |
                   v
              Frontend
                   |
                   v
                Backend
                   |
                   v
                Database
```

This architecture works for small applications.

However, imagine:

- 10 million registered users.
- 1 million daily active users.
- 100,000 concurrent users.
- 50,000 API requests per second.

A single backend server and database may become bottlenecks.

We need a scalable architecture.

### Scalable Application Architecture

```text
                    Users
                      |
                      v
                  DNS / CDN
                      |
                      v
               Load Balancer
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
      Server 1    Server 2    Server 3
          |           |           |
          +-----------+-----------+
                      |
                      v
                  API Gateway
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       Service A   Service B   Service C
          |           |           |
          +-----------+-----------+
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       Database    Redis       Message Queue
                      |
                      v
                Background Workers
```

The architecture is now distributed across multiple components.

---

## 3. Functional vs Non-Functional Requirements

System design begins with requirements.

There are two major categories.

### 3.1 Functional Requirements

Functional requirements describe what the system must do.

Example: E-commerce application.

| Requirement | Description |
|---|---|
| User Registration | Users can create accounts |
| Authentication | Users can log in |
| Product Search | Users can search products |
| Shopping Cart | Users can add products |
| Checkout | Users can place orders |
| Payment | Users can pay for orders |
| Order Tracking | Users can track deliveries |
| Reviews | Users can submit reviews |

### 3.2 Non-Functional Requirements

Non-functional requirements describe how well the system must operate.

| Requirement | Description |
|---|---|
| Scalability | Handle increasing traffic |
| Availability | Remain accessible |
| Reliability | Produce correct results consistently |
| Performance | Respond quickly |
| Security | Protect users and data |
| Durability | Preserve committed data |
| Maintainability | Remain easy to modify |
| Fault Tolerance | Continue operating during failures |
| Consistency | Maintain defined data correctness guarantees |

### Example

Suppose you are designing an e-commerce application.

Functional requirement:

> Users should be able to purchase products.

Non-functional requirement:

> The checkout API should achieve a p95 latency below 500 ms under the expected peak workload.

The second requirement is measurable.

**Important:** Always define measurable non-functional requirements instead of using vague statements such as "the application should be fast."

---

## 4. Important System Design Quality Attributes

### 4.1 Scalability

Scalability is the ability of a system to handle increasing workloads.

Example:

```text
100 users
    |
    v
1 Backend Server

10,000 users
    |
    v
Multiple Backend Servers

1,000,000 users
    |
    v
Distributed Infrastructure
```

### 4.2 Availability

Availability measures how often a system is operational and accessible.

A simplified formula:

```text
Availability =
Uptime / (Uptime + Downtime) × 100
```

Example:

If a service operates for 99 hours and remains unavailable for 1 hour:

```text
Availability = 99 / 100 × 100

             = 99%
```

### Availability Targets

| Availability | Approximate Downtime Per Year |
|---|---:|
| 99% | 3 days 15 hours 36 minutes |
| 99.9% | 8 hours 45 minutes 36 seconds |
| 99.95% | 4 hours 22 minutes 48 seconds |
| 99.99% | 52 minutes 34 seconds |
| 99.999% | 5 minutes 15 seconds |

These are approximate annual downtime budgets for a 365-day year.

### 4.3 Reliability

Reliability is the probability that a system performs its intended function correctly over a specified period.

A service might be available but unreliable.

Example:

The payment API responds successfully, but sometimes charges a customer twice.

The service is technically available, but it is not reliably performing the intended operation.

### 4.4 Durability

Durability means committed data should not be lost.

Example:

A user successfully completes a payment.

The system must preserve the payment record even if a server crashes immediately afterward.

### 4.5 Fault Tolerance

Fault tolerance is the ability to continue providing service despite component failures.

Example:

```text
              Load Balancer
                    |
          +---------+---------+
          |                   |
          v                   v
      Server A             Server B
       ACTIVE               ACTIVE

```

If Server A fails, Server B can continue handling requests.

### 4.6 Maintainability

Maintainability measures how easily a system can be understood, modified, tested and operated.

A highly scalable system that nobody can safely maintain is still a poor design.

---

# PART 2: SYSTEM CAPACITY PLANNING

## 5. What Is Capacity Planning?

Capacity planning estimates the infrastructure required to support a particular workload.

It helps answer:

- How many servers do we need?
- How much CPU and RAM do we need?
- How many database requests will occur?
- How much storage will we consume?
- How much network bandwidth is required?
- How much infrastructure capacity should we reserve?

### Important Metrics

| Metric | Meaning |
|---|---|
| DAU | Daily Active Users |
| MAU | Monthly Active Users |
| RPS | Requests Per Second |
| QPS | Queries Per Second |
| TPS | Transactions Per Second |
| Throughput | Amount of work processed per unit of time |
| Latency | Time required to complete an operation |
| Concurrency | Number of operations in progress |
| Bandwidth | Data transferred per unit of time |

---

## 6. Traffic Estimation

Consider an application with:

```text
Registered Users = 10,000,000

Daily Active Users = 1,000,000

Average Requests Per User Per Day = 100
```

Total daily requests:

```text
1,000,000 × 100

= 100,000,000 requests/day
```

Average requests per second:

```text
100,000,000 / 86,400

≈ 1,157 requests/second
```

However, traffic is not uniformly distributed throughout the day.

Assume peak traffic is 5 times the average:

```text
Peak RPS = 1,157 × 5

         ≈ 5,785 requests/second
```

Therefore, the system should be designed to handle approximately 5,800 requests per second, with additional headroom based on expected bursts and growth.

### Important Concept: Peak Traffic

Never design infrastructure using average traffic alone.

Example:

```text
Average Traffic = 1,000 RPS

Peak Traffic = 5,000 RPS
```

If your infrastructure supports only 1,500 RPS, it may fail during peak hours.

---

## 7. Concurrency Estimation – Little's Law

Little's Law is an important concept in queueing theory.

Formula:

```text
L = λ × W
```

Where:

- L = Average number of requests in the system.
- λ = Average arrival rate.
- W = Average time spent in the system.

Example:

```text
Arrival Rate = 2,000 requests/second

Average Response Time = 0.2 seconds
```

Then:

```text
L = 2,000 × 0.2

  = 400
```

Approximately 400 requests are in progress on average.

### Why Is This Important?

It helps estimate:

- Concurrent requests.
- Worker requirements.
- Connection pool sizes.
- Queue capacity.
- Memory requirements.

**Important:** Average concurrency is not the same as maximum concurrency. Capacity planning must also account for traffic bursts and latency increases.

---

## 8. Storage Estimation

Suppose an application generates:

```text
New Records Per Day = 1,000,000

Average Record Size = 2 KB
```

Daily storage:

```text
1,000,000 × 2 KB

= 2,000,000 KB

≈ 2 GB/day
```

Annual storage:

```text
2 GB × 365

= 730 GB/year
```

But real storage consumption is higher because of:

- Indexes.
- Database metadata.
- Replication.
- Backups.
- Logs.
- Temporary data.
- Storage engine overhead.

For planning, estimate these separately rather than assuming that the raw record size represents total disk consumption.

---

# PART 3: LATENCY AND PERFORMANCE ENGINEERING

## 9. What Is Latency?

Latency is the time required to complete an operation.

Example:

```text
Client sends request
        |
        v
Backend receives request
        |
        v
Database query
        |
        v
Backend processes result
        |
        v
Client receives response
```

Suppose:

| Operation | Time |
|---|---:|
| DNS Lookup | 10 ms |
| Network Connection | 20 ms |
| Backend Processing | 30 ms |
| Database Query | 80 ms |
| Serialization | 10 ms |
| Response Transfer | 20 ms |
| Total | 170 ms |

Total latency is approximately 170 ms.

The actual timing depends on network conditions, connection reuse, parallel operations and other implementation details.

---

## 10. Latency Percentiles

Averages do not tell the whole performance story.

Consider these response times:

```text
20 ms
30 ms
40 ms
50 ms
60 ms
70 ms
100 ms
200 ms
500 ms
2000 ms
```

Average latency:

```text
307 ms
```

However, most requests are much faster than 307 ms.

This is why production systems use percentiles.

| Metric | Meaning |
|---|---|
| p50 | 50% of requests complete within this time |
| p90 | 90% complete within this time |
| p95 | 95% complete within this time |
| p99 | 99% complete within this time |
| p99.9 | 99.9% complete within this time |

Example:

```text
p50 = 80 ms
p95 = 250 ms
p99 = 800 ms
```

This means 99% of requests complete within 800 ms.

### Why Is p99 Important?

Slow requests can cause:

- Poor user experience.
- Request timeouts.
- Thread or worker exhaustion.
- Connection pool saturation.
- Queue buildup.
- Cascading failures.

A system can have an excellent average latency while still having terrible p99 latency.

---

## 11. Timeouts, Retries and Exponential Backoff

Distributed systems experience temporary failures.

Example:

```text
Service A → Service B
```

Service B does not respond.

Service A should not wait forever.

It should use a timeout.

### Retry Strategy

A basic retry strategy:

```javascript
async function requestWithRetry() {
  let attempts = 0;

  while (attempts < 3) {
    try {
      return await callExternalService();
    } catch (error) {
      attempts++;

      if (attempts === 3) {
        throw error;
      }
    }
  }
}
```

However, immediately retrying failed requests can overload the service.

### Exponential Backoff

The delay increases after every failure.

```text
Attempt 1 → 100 ms

Attempt 2 → 200 ms

Attempt 3 → 400 ms

Attempt 4 → 800 ms
```

Formula:

```text
Delay = Initial Delay × 2^Attempt
```

In production, use a maximum delay and add random jitter.

### Why Jitter?

Without jitter, thousands of clients may retry simultaneously.

This creates another traffic spike.

Jitter introduces randomness into retry delays.

### Important Best Practices

- Always configure timeouts.
- Use bounded retries.
- Use exponential backoff.
- Add jitter.
- Retry only appropriate failures.
- Avoid retrying non-idempotent operations without safeguards.
- Use an overall request deadline.
- Avoid retrying at every layer of a service chain.

---

## 12. Idempotency

An operation is idempotent if executing it multiple times has the same intended effect as executing it once.

Example:

```http
PUT /users/100
```

Updating a user to the same values multiple times should produce the same final state.

But consider:

```http
POST /payments
```

If the client retries after a timeout, the payment might be processed twice.

An idempotency key can prevent duplicate processing.

### Example

```http
POST /payments
Idempotency-Key: payment-order-12345
```

Backend logic:

```javascript
async function createPayment(req, res) {
  const key = req.headers["idempotency-key"];

  const existing = await Payment.findOne({
    where: { idempotencyKey: key }
  });

  if (existing) {
    return res.json(existing);
  }

  const payment = await Payment.create({
    idempotencyKey: key,
    amount: req.body.amount,
    status: "PENDING"
  });

  res.json(payment);
}
```

This is an illustrative example. A production implementation must enforce uniqueness at the database level and handle concurrent requests using transactions or atomic insert semantics.

---

# PART 4: SCALING AND LOAD BALANCING

## 13. Vertical Scaling vs Horizontal Scaling

There are two major scaling approaches.

### 13.1 Vertical Scaling

Vertical scaling means increasing the resources of an existing machine.

Example:

```text
Before:

CPU = 2 Cores
RAM = 4 GB

After:

CPU = 16 Cores
RAM = 64 GB
```

Advantages:

- Simple architecture.
- Fewer distributed-system concerns.
- Easier application deployment.
- Useful for certain database workloads.

Disadvantages:

- Hardware limits.
- Potentially expensive upgrades.
- A single machine remains a failure point.
- Scaling may require downtime.

### 13.2 Horizontal Scaling

Horizontal scaling means adding more machines or instances.

```text
                 Load Balancer
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Server 1     Server 2     Server 3
```

Advantages:

- Better capacity expansion.
- Improved redundancy.
- Supports distributed workloads.
- Can scale incrementally.

Disadvantages:

- Requires load balancing.
- Introduces network communication.
- Requires distributed state management.
- Makes debugging and operations more complex.

### Comparison

| Feature | Vertical Scaling | Horizontal Scaling |
|---|---|---|
| Method | Upgrade machine | Add machines |
| Complexity | Lower initially | Higher |
| Hardware Limit | Yes | Depends on architecture |
| Fault Tolerance | Limited on one machine | Can be designed for redundancy |
| Distributed Coordination | Usually less | Often required |
| Typical Use | Databases, smaller workloads | Large web services |

---

## 14. Stateless vs Stateful Applications

### Stateless Application

A stateless server does not depend on locally stored client session state to process the next request.

Example:

```text
Client Request
      |
      v
Server A
      |
      v
Database
```

The next request can go to Server B.

```text
Client Request 1 → Server A

Client Request 2 → Server B

Client Request 3 → Server C
```

This makes horizontal scaling easier.

### Stateful Application

A stateful server stores important session information locally.

Example:

```text
Client
  |
  v
Server A
  |
  v
Local Session Data
```

If the next request goes to Server B, Server B may not have the required session information.

### Solutions

1. Store sessions in Redis.
2. Store sessions in a shared database.
3. Use signed tokens where appropriate.
4. Use sticky sessions when necessary.

**Best practice:** Keep application instances as stateless as practical, and move shared state into appropriately designed external systems.

---

## 15. Load Balancing

A load balancer distributes incoming traffic across multiple servers.

```text
                  Internet
                     |
                     v
                Load Balancer
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Server A   Server B   Server C
```

### Why Do We Need Load Balancing?

- Distribute traffic.
- Avoid overloading one server.
- Improve availability.
- Support horizontal scaling.
- Perform health-based routing.

### 15.1 Round Robin

Requests are distributed sequentially.

```text
Request 1 → Server A

Request 2 → Server B

Request 3 → Server C

Request 4 → Server A
```

### 15.2 Weighted Round Robin

Servers receive traffic based on assigned weights.

```text
Server A = Weight 5

Server B = Weight 3

Server C = Weight 2
```

Server A receives approximately 50% of traffic, B 30%, and C 20%, assuming the load balancer implements weighted distribution accordingly.

### 15.3 Least Connections

The load balancer sends new requests to the server with the fewest active connections.

Useful when requests have significantly different durations.

### 15.4 IP Hash

The client's IP address determines which server receives the request.

This can provide session affinity, but it may create uneven distribution and does not guarantee robust session persistence when client IPs change.

### 15.5 Random Selection

A server is selected randomly.

Some systems use the power-of-two-choices technique: select two candidates and route to the less-loaded one.

### Comparison

| Algorithm | Working Principle | Suitable For |
|---|---|---|
| Round Robin | Sequential distribution | Similar workloads |
| Weighted Round Robin | Distribution by weight | Different server capacities |
| Least Connections | Fewest active connections | Variable request durations |
| IP Hash | Hash based on client IP | Basic affinity |
| Random | Random server selection | Simple distribution |
| Consistent Hashing | Stable key-to-node mapping | Distributed caches and partitioned data |

---

## 16. Layer 4 vs Layer 7 Load Balancing

### Layer 4 Load Balancing

Works at the transport layer.

Commonly uses:

- TCP.
- UDP.
- IP addresses.
- Ports.

It does not need to understand the HTTP request path.

Example:

```text
TCP Connection
      |
      v
L4 Load Balancer
      |
      +------> Server A
      |
      +------> Server B
```

### Layer 7 Load Balancing

Works at the application layer.

It can inspect HTTP information such as:

- URL paths.
- Hostnames.
- HTTP headers.
- Cookies.
- HTTP methods.

Example:

```text
/api/users
      |
      v
  Load Balancer
      |
      v
 User Service


/api/products
      |
      v
  Load Balancer
      |
      v
 Product Service
```

### Comparison

| Feature | Layer 4 | Layer 7 |
|---|---|---|
| OSI Layer | Transport | Application |
| Protocol Awareness | TCP/UDP | HTTP/HTTPS and others |
| URL Routing | No | Yes |
| Header Inspection | No | Yes |
| Processing Overhead | Usually lower | Usually higher |
| Common Use | Network-level distribution | HTTP routing |

---

## 17. Reverse Proxy vs Forward Proxy vs Load Balancer

### Forward Proxy

A forward proxy acts on behalf of clients.

```text
Client
  |
  v
Forward Proxy
  |
  v
Internet
```

Common purposes:

- Access control.
- Privacy.
- Content filtering.
- Network monitoring.

### Reverse Proxy

A reverse proxy acts on behalf of backend servers.

```text
Client
  |
  v
Reverse Proxy
  |
  v
Backend Server
```

Common purposes:

- TLS termination.
- Request routing.
- Compression.
- Caching.
- Security controls.

### Load Balancer

A load balancer distributes traffic among multiple backend instances.

```text
                Load Balancer
                      |
           +----------+----------+
           |          |          |
           v          v          v
        Server A   Server B   Server C
```

A reverse proxy can also perform load balancing. These concepts overlap, but they are not identical.

---

# PART 5: CDN, DNS AND GLOBAL TRAFFIC MANAGEMENT

## 18. What Is a CDN?

CDN stands for Content Delivery Network.

A CDN distributes cached content through geographically distributed edge locations.

Without a CDN:

```text
User in India
      |
      v
Server in USA
      |
      v
Response
```

With a CDN:

```text
             CDN Network

       +---------+---------+
       |         |         |
       v         v         v
    India     Europe      USA
    Edge      Edge       Edge
```

A user can receive cacheable content from a nearby edge location.

### Common CDN Content

- Images.
- CSS.
- JavaScript.
- Fonts.
- Videos.
- Static HTML.
- Public API responses where safe to cache.

### CDN Advantages

1. Reduced latency for cacheable content.
2. Lower origin-server traffic.
3. Improved global content delivery.
4. Better handling of traffic spikes.
5. Additional security capabilities.

### Important CDN Concepts

| Concept | Description |
|---|---|
| Edge Location | CDN server near users |
| Origin Server | Original content server |
| Cache Hit | Requested content exists in cache |
| Cache Miss | CDN must fetch from origin |
| TTL | How long cached content remains valid |
| Cache Invalidation | Removing or refreshing cached content |
| Origin Shield | Intermediate cache layer protecting origin |
| Cache Key | Identifies which cached response is used |

### Cache-Control Example

```http
Cache-Control: public, max-age=3600
```

This permits shared caching for up to one hour, subject to cache behavior and other directives.

For sensitive personalized responses, do not use public caching unless the cache key and authorization model have been deliberately designed to prevent data leakage.

### Stale-While-Revalidate

This strategy allows a cache to serve a stale response while refreshing it in the background.

It can improve perceived performance when slightly stale content is acceptable.

---

## 19. DNS and Global Traffic Management

DNS stands for Domain Name System.

It translates domain names into network addresses.

Example:

```text
example.com
      |
      v
 DNS Resolver
      |
      v
 DNS Server
      |
      v
 IP Address
```

### Important DNS Record Types

| Record | Purpose |
|---|---|
| A | Maps hostname to IPv4 address |
| AAAA | Maps hostname to IPv6 address |
| CNAME | Maps one hostname to another |
| MX | Mail exchange |
| TXT | Text records, often used for verification and email policies |
| NS | Authoritative name servers |
| SRV | Service location information |

### DNS-Based Global Routing

Suppose your application has servers in three regions:

```text
                Global DNS
                     |
          +----------+----------+
          |          |          |
          v          v          v
       US Region  EU Region  Asia Region
```

DNS-based traffic management can direct users based on:

- Geographic location.
- Network latency.
- Endpoint health.
- Routing policies.

### Important Limitation

DNS failover is not always immediate.

Resolvers and clients may cache DNS responses. Existing connections may also remain active.

Therefore, DNS-based routing alone is not sufficient for instant failover.

---

# PART 6: DATABASE SCALING

## 20. Database Scaling Fundamentals

As application traffic increases, the database can become the primary bottleneck.

Common problems include:

- Too many concurrent queries.
- Slow queries.
- Lock contention.
- Connection exhaustion.
- High disk I/O.
- Large indexes.
- Write contention.
- Replication lag.

Before introducing complex distributed database architecture, optimize:

1. Database schema.
2. Indexes.
3. Query execution plans.
4. Connection pooling.
5. Transaction duration.
6. Caching.
7. Data retention.

---

## 21. Database Replication

Replication means maintaining copies of database data across multiple database servers.

### Primary-Replica Architecture

```text
             Application
                  |
                  v
              Primary DB
                  |
           Replication Stream
                  |
          +-------+-------+
          |               |
          v               v
       Replica 1       Replica 2
```

The primary handles writes, and replicas can serve eligible read requests.

### Advantages

- Read scalability.
- Redundancy.
- Improved recovery options.
- Separation of read and write workloads.

### Replication Types

#### 21.1 Synchronous Replication

The primary waits for the required replica acknowledgements before confirming a write.

Advantages:

- Stronger durability guarantees under the configured failure model.
- Can reduce the risk of losing acknowledged writes.

Disadvantages:

- Higher write latency.
- Availability may decrease if required replicas are unavailable.

#### 21.2 Asynchronous Replication

The primary acknowledges writes before all replicas have received them.

Advantages:

- Lower write latency.
- Primary writes can continue without waiting for every replica.

Disadvantages:

- Replication lag.
- Recent acknowledged writes may be lost during certain failover scenarios.

### Replication Lag

Replication lag occurs when a replica has not yet applied the latest primary changes.

Example:

```text
Primary DB:

User Name = "Ahmed"

Replica DB:

User Name = "Ali"
```

The replica may temporarily return stale data.

### Read-After-Write Problem

Suppose a user updates their profile.

The application writes to the primary, then immediately reads from a replica.

The replica may return the old value.

Solutions include:

- Read from the primary after a write.
- Use session-based read-your-writes routing.
- Wait until replication catches up when the database provides a suitable mechanism.
- Use synchronous replication where appropriate.

---

## 22. Database Partitioning vs Sharding

These terms are related but not identical.

### Partitioning

Partitioning divides a large dataset into smaller logical or physical pieces.

Example: Partition orders by year.

```text
Orders Table
     |
     +---- Orders_2024
     |
     +---- Orders_2025
     |
     +---- Orders_2026
```

Partitions may exist within one database system.

### Sharding

Sharding distributes data across multiple database servers.

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
       Users      Users      Users
       1-1000     1001-2000  2001-3000
```

### Comparison

| Feature | Partitioning | Sharding |
|---|---|---|
| Purpose | Divide data | Distribute data across nodes |
| Infrastructure | May be one database | Multiple database nodes |
| Management | Often simpler | More complex |
| Scaling | Depends on implementation | Can increase distributed capacity |
| Cross-Partition Queries | Possible | Cross-shard queries can be expensive |

---

## 23. Sharding Strategies

### 23.1 Range-Based Sharding

Data is distributed according to ranges.

```text
Shard 1: User ID 1–1000

Shard 2: User ID 1001–2000

Shard 3: User ID 2001–3000
```

Problem:

Some ranges may receive much more traffic than others.

This creates hot shards.

### 23.2 Hash-Based Sharding

A hash function determines the shard.

```javascript
function getShard(userId, totalShards) {
  return hash(userId) % totalShards;
}
```

Advantages:

- Can distribute data more evenly.
- Avoids simple sequential range concentration.

Disadvantages:

- Changing shard count can move many keys.
- Range queries become difficult.

### 23.3 Geographic Sharding

Data is distributed based on geographic location.

```text
India Users  → Asia Shard

Europe Users → Europe Shard

USA Users    → US Shard
```

Advantages:

- Lower regional access latency.
- Supports data residency requirements.

Disadvantages:

- Cross-region operations become more complicated.
- Users moving between regions may require data migration or routing changes.

### 23.4 Tenant-Based Sharding

In multi-tenant SaaS applications, data can be distributed by tenant.

```text
Tenant A → Shard 1

Tenant B → Shard 2

Tenant C → Shard 3
```

This can help isolate large tenants and manage resource consumption.

### Choosing a Shard Key

A good shard key should consider:

- High cardinality.
- Even distribution.
- Query patterns.
- Write distribution.
- Avoiding hot partitions.
- Operational flexibility.

A poor shard key can create severe hotspots.

---

## 24. Consistent Hashing

Consistent hashing is used to distribute keys across nodes while minimizing remapping when nodes are added or removed.

Traditional hashing:

```javascript
shard = hash(key) % numberOfShards;
```

If the number of shards changes, many keys may map to different shards.

Consistent hashing addresses this problem.

### Conceptual Architecture

```text
                 Hash Ring

            Node A
          /        \
         /          \
      Node D       Node B
         \          /
          \        /
            Node C
```

Both nodes and keys are mapped onto a logical hash ring.

A key is assigned to the next node encountered in the chosen direction around the ring.

### Virtual Nodes

A physical node can be represented by multiple positions on the ring.

Benefits:

- Better distribution.
- Easier rebalancing.
- Less uneven ownership.

### Common Use Cases

- Distributed caching.
- Distributed storage.
- Partition assignment.
- Request routing.

---

# PART 7: DISTRIBUTED SYSTEMS THEORY

## 25. What Is a Distributed System?

A distributed system consists of multiple independent computers that communicate over a network and work together to provide a service.

Example:

```text
                Client
                   |
                   v
               API Gateway
                   |
        +----------+----------+
        |          |          |
        v          v          v
      Auth       Order      Payment
     Service    Service     Service
        |          |          |
        v          v          v
      Auth DB    Order DB   Payment DB
```

These services may run on different machines, containers or geographic regions.

### Common Distributed System Challenges

- Network failures.
- Partial failures.
- Message delays.
- Duplicate messages.
- Out-of-order messages.
- Clock differences.
- Data consistency.
- Leader failures.
- Network partitions.
- Distributed transactions.

A major difference between distributed and single-machine systems is that components can fail independently while other components continue operating.

---

## 26. CAP Theorem

CAP theorem is one of the most important concepts in distributed systems.

CAP stands for:

- Consistency.
- Availability.
- Partition Tolerance.

### Consistency

For the CAP discussion, consistency refers to a strong guarantee such as linearizability: operations appear to occur atomically in an order consistent with real-time ordering.

### Availability

Every request received by a non-failing node receives a non-error response.

This does not necessarily mean that the response contains the latest data.

### Partition Tolerance

The system continues operating despite communication failures between parts of the distributed system.

### What Does CAP Actually Mean?

When a network partition occurs, a distributed data system cannot guarantee both:

1. Linearizable consistency.
2. Availability for every request.

It must make a tradeoff for affected operations.

### CP-Oriented Behavior

The system may reject or delay some requests to preserve strong consistency.

```text
Network Partition
       |
       v
Reject some operations
       |
       v
Preserve consistency
```

### AP-Oriented Behavior

The system may continue accepting requests on both sides of a partition, potentially allowing temporarily divergent data.

```text
Network Partition
       |
       v
Accept requests
       |
       v
Reconcile data later
```

### Important Clarification

CAP does not mean that every system permanently chooses only two out of three properties.

The tradeoff becomes relevant during a network partition. Without a partition, a system may provide both consistency and availability, subject to its design and failure model.

---

## 27. PACELC Theorem

PACELC extends the CAP discussion.

It stands for:

```text
If Partition (P):

Choose between Availability (A)
and Consistency (C).

Else (E):

Choose between Latency (L)
and Consistency (C).
```

### Meaning

During a network partition:

```text
P → A or C
```

When there is no partition:

```text
E → L or C
```

### Example

A distributed database may prioritize low latency by serving reads from nearby replicas.

However, those replicas may temporarily contain stale data.

Another database configuration may wait for stronger consistency guarantees, increasing latency.

### Why Is PACELC Important?

It helps evaluate tradeoffs even during normal operating conditions.

---

## 28. Consistency Models

Consistency models describe the guarantees a distributed system provides when multiple clients access shared data.

### 28.1 Strong Consistency / Linearizability

After a successful write, subsequent reads observe that write, assuming the operations are correctly ordered.

Example:

```text
Write Balance = 500

Next Read Balance = 500
```

Useful for operations such as:

- Account balance updates.
- Inventory reservation.
- Critical financial state.

### 28.2 Eventual Consistency

Replicas may temporarily contain different values, but if updates stop and the system successfully converges, replicas eventually become consistent.

Example:

```text
Primary:

Username = "Riyaz"

Replica 1:

Username = "Riyaz"

Replica 2:

Username = "Ahmed"
```

Later:

```text
Replica 2:

Username = "Riyaz"
```

### 28.3 Read-Your-Writes Consistency

A user should be able to see their own successful writes in subsequent reads.

### 28.4 Monotonic Reads

Once a client observes a particular version of data, subsequent reads should not return an older version.

### 28.5 Causal Consistency

Operations that are causally related should be observed in a compatible order.

### Comparison

| Model | Guarantee |
|---|---|
| Linearizability | Operations respect real-time ordering |
| Sequential Consistency | Operations appear in a single order consistent with each process's program order |
| Causal Consistency | Causally related operations preserve their order |
| Eventual Consistency | Replicas converge when updates stop and propagation succeeds |
| Read-Your-Writes | A client sees its own completed writes |
| Monotonic Reads | A client does not move backward to older observed data |

---

## 29. Quorum Reads and Writes

Quorum systems use replica acknowledgements to determine when an operation can complete.

Let:

```text
N = Total Replicas

W = Required Write Acknowledgements

R = Required Read Responses
```

A commonly useful overlap condition is:

```text
R + W > N
```

This ensures that read and write replica sets overlap in the simple replica-selection model.

### Example

```text
N = 3

W = 2

R = 2
```

Then:

```text
R + W = 4

4 > 3
```

The read and write quorums must overlap.

### Important Limitation

Quorum overlap alone does not guarantee linearizability.

The actual consistency guarantee depends on:

- Replica protocol.
- Version ordering.
- Conflict resolution.
- Concurrent writes.
- Read repair.
- Leader or coordinator behavior.

---

# PART 8: DISTRIBUTED CONSENSUS AND COORDINATION

## 30. What Is Distributed Consensus?

Distributed consensus is the process by which multiple nodes agree on a value or sequence of operations.

Example:

```text
Node A
Node B
Node C
Node D
Node E
```

All nodes must agree on which operations have been committed.

Consensus is important for:

- Distributed databases.
- Cluster coordination.
- Leader election.
- Replicated logs.
- Metadata management.

### Common Consensus Algorithms

- Paxos.
- Raft.

---

## 31. Raft Consensus Algorithm

Raft is a consensus algorithm designed to be understandable and practical.

It uses three main node roles.

### 31.1 Leader

The leader:

- Accepts client writes.
- Replicates log entries.
- Coordinates commitment.
- Sends heartbeats.

### 31.2 Follower

The follower:

- Receives replicated log entries.
- Responds to leader heartbeats.
- Participates in voting.

### 31.3 Candidate

A candidate requests votes when it starts an election.

### Leader Election

```text
          Node A
          Leader
             |
      +------+------+
      |             |
      v             v
   Node B         Node C
   Follower       Follower
```

If the leader becomes unavailable:

```text
Leader Failure
      |
      v
Election Timeout
      |
      v
Candidate Election
      |
      v
New Leader Selected
```

A majority quorum is needed to elect a leader.

For five voting nodes, a majority requires three votes.

### Why Is Raft Important?

It helps distributed systems agree on a consistent replicated log despite certain node failures.

**Important:** Consensus protocols do not eliminate network partitions or all failure modes. They define how a cluster behaves under specific assumptions.

---

## 32. Split Brain Problem

Split brain occurs when multiple parts of a distributed system incorrectly believe they are the active leader or primary.

Example:

```text
Network Partition

Cluster A              Cluster B
   |                      |
   v                      v
Leader A                Leader B
   |                      |
   v                      v
Accepts Writes           Accepts Writes
```

Both sides may accept conflicting updates.

### Prevention Strategies

- Consensus-based leader election.
- Quorum-based decisions.
- Fencing tokens.
- Strong ownership rules.
- Proper failover mechanisms.

### Fencing Tokens

A fencing token is a monotonically increasing number used to distinguish newer lock owners from older ones.

Example:

```text
Worker A → Token 101

Worker B → Token 102
```

Even if Worker A resumes after losing its lease, the protected resource can reject its stale token.

This is stronger than relying only on a lock's expiration time.

---

## 33. Distributed Locks

A distributed lock coordinates access to a shared resource across multiple machines.

Example:

Two workers attempt to process the same order.

```text
Worker A ──┐
           |
           v
       Distributed Lock
           |
           v
       Order Resource
           ^
           |
Worker B ──┘
```

Only the valid lock holder should perform the protected operation.

### Common Implementations

- Redis-based locks.
- Database locks.
- ZooKeeper coordination.
- etcd leases.
- Consul sessions.

### Lock Requirements

A robust distributed lock design must consider:

1. Atomic acquisition.
2. Ownership identity.
3. Lease expiration.
4. Safe release.
5. Process crashes.
6. Network delays.
7. Stale lock holders.
8. Fencing or equivalent protection.

### Important Warning

A Redis lock with a TTL is not automatically a complete correctness solution.

If a worker pauses long enough for its lock to expire, another worker may acquire the lock while the first worker resumes.

Use fencing tokens or enforce ownership at the protected resource when correctness requires it.

---

# PART 9: DISTRIBUTED CACHING

## 34. What Is Distributed Caching?

Distributed caching stores frequently accessed data across a shared cache infrastructure.

Example:

```text
                Backend Servers
                 /     |     \
                /      |      \
               v       v       v
              Redis Cluster
                    |
                    v
                 Database
```

Instead of every backend request querying the database, the application can retrieve frequently accessed data from Redis.

### Benefits

- Reduced database load.
- Lower response latency.
- Better throughput.
- Shared cache across multiple application instances.

---

## 35. Caching Strategies

### 35.1 Cache-Aside

The application checks the cache first.

If the data is missing, it reads from the database and populates the cache.

```text
Application
     |
     v
  Check Cache
     |
  +--+--+
  |     |
 Hit   Miss
  |     |
  v     v
Return  Database
Data      |
          v
       Cache Data
          |
          v
       Return Data
```

Example:

```javascript
async function getUser(userId) {
  const cacheKey = `user:${userId}`;

  const cached = await redis.get(cacheKey);

  if (cached) {
    return JSON.parse(cached);
  }

  const user = await User.findByPk(userId);

  if (!user) {
    return null;
  }

  await redis.set(
    cacheKey,
    JSON.stringify(user),
    "EX",
    300
  );

  return user;
}
```

### 35.2 Read-Through

The cache itself coordinates cache misses and data loading through a configured loader.

### 35.3 Write-Through

Data is written to the cache and backing store as part of the write path.

### 35.4 Write-Behind

The cache accepts writes and persists them to the backing store asynchronously.

This can improve write performance but introduces durability and data-loss risks if the cache fails before persistence.

### Comparison

| Strategy | Read Behavior | Write Behavior |
|---|---|---|
| Cache-Aside | Application checks cache | Application updates database and invalidates or refreshes cache |
| Read-Through | Cache loads missing data | Depends on write strategy |
| Write-Through | Reads from cache | Cache synchronously updates backing store |
| Write-Behind | Reads from cache | Cache persists writes asynchronously |

---

## 36. Cache Stampede

A cache stampede occurs when a popular cached item expires and many requests simultaneously attempt to reload it.

Example:

```text
10,000 Requests
       |
       v
 Cache Expired
       |
       v
10,000 Database Queries
       |
       v
Database Overloaded
```

### Prevention

- TTL jitter.
- Request coalescing.
- Background refresh.
- Locking or single-flight mechanisms.
- Stale-while-revalidate.
- Controlled cache warming.

### TTL Jitter

Instead of giving every cached item exactly the same expiration time, introduce a small random variation.

This reduces synchronized expiration.

---

## 37. Cache Invalidation

Cache invalidation is the process of removing or updating stale cached data.

Example:

```text
User Profile Updated
       |
       v
Database Updated
       |
       v
Cache Invalidated
       |
       v
Next Read Loads Fresh Data
```

Possible strategies:

- Delete cache key after a successful database write.
- Update cache after the database write.
- Use versioned cache keys.
- Use short TTLs where temporary staleness is acceptable.

**Important:** Cache invalidation is a consistency problem. The database and cache are separate systems, so failures can occur between their updates.

---

# PART 10: DISTRIBUTED RATE LIMITING

## 38. What Is Rate Limiting?

Rate limiting controls how many requests a client can make during a defined period.

Example:

```text
Maximum Requests = 100

Time Window = 1 Minute
```

If a client exceeds the configured limit, the server may respond with:

```http
HTTP/1.1 429 Too Many Requests
```

### Why Is Rate Limiting Important?

- Prevent abuse.
- Protect infrastructure.
- Reduce denial-of-service impact.
- Enforce API quotas.
- Ensure fair resource allocation.

---

## 39. Rate Limiting Algorithms

### 39.1 Fixed Window

The request counter resets at fixed intervals.

Example:

```text
Limit = 100 Requests Per Minute
```

Problem:

A client can send 100 requests just before the window ends and another 100 immediately afterward.

This creates a burst of 200 requests over a very short period.

### 39.2 Sliding Window

Tracks request activity across a moving time interval.

This reduces fixed-window boundary bursts.

### 39.3 Token Bucket

A bucket stores tokens.

Each request consumes a token.

Tokens are replenished at a defined rate.

```text
Token Bucket
     |
     v
Request Arrives
     |
     v
Token Available?
     |
   +---+
   |   |
  Yes  No
   |   |
   v   v
Allow Reject
```

Advantages:

- Supports controlled bursts.
- Controls long-term request rates.
- Flexible rate limiting.

### 39.4 Leaky Bucket

Requests enter a queue and are processed at a controlled rate.

It smooths bursts but may introduce queueing delay.

### Comparison

| Algorithm | Burst Handling | Main Characteristic |
|---|---|---|
| Fixed Window | Weak at boundaries | Simple counters |
| Sliding Window | Better | Moving time window |
| Token Bucket | Allows controlled bursts | Replenishing tokens |
| Leaky Bucket | Smooths bursts | Controlled output rate |

### Distributed Rate Limiting with Redis

When an application has multiple backend instances, local counters are insufficient.

Example:

```text
Server A ──┐
           |
Server B ──+──> Redis
           |
Server C ──┘
```

All servers must coordinate access to shared rate-limit state.

Redis Lua scripts or appropriate atomic Redis operations can help ensure that checking and updating counters happen safely.

---

# PART 11: HIGH AVAILABILITY AND FAULT TOLERANCE

## 40. What Is High Availability?

High availability means designing a system to minimize downtime and maintain service availability.

A highly available architecture avoids relying on a single critical component.

### Single-Server Architecture

```text
Users
  |
  v
Server
  |
  v
Database
```

If the server fails, the application becomes unavailable.

### Highly Available Architecture

```text
                   Users
                     |
                     v
                Load Balancer
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Server A   Server B   Server C
          |          |          |
          +----------+----------+
                     |
                     v
                Database Cluster
```

Now, the failure of one application server does not necessarily cause downtime.

However, the database cluster and load balancer also need appropriate redundancy.

---

## 41. Single Point of Failure (SPOF)

A single point of failure is a component whose failure can bring down a critical part of the system.

Example:

```text
             Users
               |
               v
        Single Load Balancer
               |
               v
         Multiple Servers
```

Even though multiple backend servers exist, the single load balancer remains a potential failure point.

### Common SPOFs

- Single load balancer.
- Single database.
- Single Redis instance.
- Single message broker.
- Single availability zone.
- Single network gateway.
- Single identity provider.

### Prevention

- Redundant instances.
- Multi-AZ deployment.
- Automated failover.
- Replication.
- Health checks.
- Independent failure domains.

---

## 42. Active-Active vs Active-Passive Architecture

### Active-Active

Multiple systems actively serve traffic.

```text
          Load Balancer
                |
       +--------+--------+
       |                 |
       v                 v
    Region A          Region B
    ACTIVE            ACTIVE
```

Advantages:

- Traffic distribution.
- Better regional utilization.
- Potentially faster regional failover.

Challenges:

- Cross-region consistency.
- Conflict resolution.
- Data ownership.
- Operational complexity.

### Active-Passive

One system serves traffic while another remains ready to take over.

```text
         Primary Region
            ACTIVE
               |
               v
          Replication
               |
               v
         Backup Region
            PASSIVE
```

Advantages:

- Simpler write ownership.
- Easier data consistency in many designs.
- Lower active infrastructure usage.

Disadvantages:

- Failover may take longer.
- Standby capacity may be underutilized.
- Failover procedures need testing.

### Comparison

| Feature | Active-Active | Active-Passive |
|---|---|---|
| Traffic | Multiple regions serve traffic | Primary region serves traffic |
| Complexity | Higher | Often lower |
| Failover | Potentially faster | May require promotion |
| Data Consistency | More challenging | Often simpler |
| Resource Utilization | Higher | Standby resources may be underused |

---

## 43. Disaster Recovery

Disaster recovery is the process of restoring application services after a major failure.

Examples:

- Complete data-center outage.
- Regional cloud outage.
- Accidental database deletion.
- Data corruption.
- Ransomware.
- Infrastructure misconfiguration.

### Important DR Metrics

#### Recovery Time Objective (RTO)

RTO is the maximum acceptable time to restore a service after a disruption.

Example:

```text
RTO = 30 Minutes
```

The business wants the service restored within 30 minutes.

#### Recovery Point Objective (RPO)

RPO is the maximum acceptable amount of data loss measured in time.

Example:

```text
RPO = 5 Minutes
```

The business can tolerate losing up to approximately five minutes of recent data.

### RTO vs RPO

| Metric | Meaning |
|---|---|
| RTO | How quickly must the system recover? |
| RPO | How much recent data loss is acceptable? |

### Important Backup Concepts

- Full backups.
- Incremental backups.
- Differential backups.
- Point-in-time recovery.
- Offsite backups.
- Immutable backups.
- Backup encryption.
- Restore testing.

**Critical:** Replication is not a replacement for backups. If a user accidentally deletes important data, replication may copy that deletion to every replica.

---

# PART 12: MULTI-REGION ARCHITECTURE

## 44. What Is Multi-Region Architecture?

A multi-region architecture deploys application infrastructure across multiple geographic cloud regions.

Example:

```text
                   Global Users
                        |
                        v
                 Global Traffic Manager
                        |
             +----------+----------+
             |          |          |
             v          v          v
          US Region  EU Region  Asia Region
             |          |          |
             v          v          v
          Services   Services   Services
             |          |          |
             v          v          v
          Database  Database  Database
```

### Why Use Multiple Regions?

- Lower latency for global users.
- Disaster recovery.
- Regional availability.
- Data residency requirements.
- Geographic traffic distribution.

### Challenges

- Cross-region network latency.
- Replication lag.
- Data conflicts.
- Regional failover.
- Increased infrastructure cost.
- More complex monitoring.
- Data sovereignty requirements.

### Multi-Region Data Strategies

1. Single-writer with regional read replicas.
2. Multi-writer replication.
3. Regionally partitioned data.
4. Tenant-based regional ownership.
5. Event-based data synchronization.

The right strategy depends on business requirements and consistency needs.

---

# PART 13: DISTRIBUTED TRANSACTIONS

## 45. What Is a Distributed Transaction?

A distributed transaction involves multiple independent services or databases.

Example: E-commerce order placement.

```text
Order Service
      |
      v
Inventory Service
      |
      v
Payment Service
      |
      v
Shipping Service
```

A successful order may require multiple operations:

1. Create order.
2. Reserve inventory.
3. Charge payment.
4. Arrange shipping.

What happens if payment succeeds but inventory reservation fails?

This is a distributed transaction problem.

---

## 46. Two-Phase Commit (2PC)

Two-phase commit coordinates multiple participants to commit or abort a transaction.

### Phase 1: Prepare

The coordinator asks all participants whether they can commit.

```text
Coordinator
     |
     +----> Database A: Prepare
     |
     +----> Database B: Prepare
```

### Phase 2: Commit

If all participants vote to commit, the coordinator instructs them to commit.

Otherwise, it instructs them to abort.

```text
All Participants Ready
          |
          v
       COMMIT
```

### Advantages

- Coordinates atomic outcomes under the protocol's assumptions.
- Useful for certain database transaction environments.

### Disadvantages

- Can block while participants wait for the coordinator.
- More coordination overhead.
- Failure recovery can be complex.
- Not ideal for many long-running microservice workflows.

---

## 47. Saga Pattern

Saga manages a distributed business transaction as a sequence of local transactions.

If one step fails, previously completed steps are compensated.

Example:

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
```

Suppose payment fails.

Compensation:

```text
Release Inventory
     |
     v
Cancel Order
```

### Saga Types

#### Choreography

Services communicate through events.

```text
Order Created
      |
      v
Inventory Service
      |
      v
Inventory Reserved
      |
      v
Payment Service
```

No single central orchestrator controls every step.

#### Orchestration

A central orchestrator controls the workflow.

```text
           Saga Orchestrator
             /    |    \
            v     v     v
         Order Inventory Payment
         Service Service  Service
```

### Comparison

| Feature | Choreography | Orchestration |
|---|---|---|
| Coordination | Events | Central coordinator |
| Coupling | Can become implicit | Workflow is explicit |
| Complexity | Distributed across services | Concentrated in orchestrator |
| Visibility | Requires event tracing | Central workflow state |
| Suitable For | Simpler event workflows | Complex business processes |

---

## 48. Transactional Outbox Pattern

A common problem:

The application updates a database and publishes an event.

What if the database write succeeds but event publishing fails?

The two operations are not automatically atomic.

The Transactional Outbox Pattern addresses this.

### Architecture

```text
             Application
                  |
                  v
             Database
              /     \
             v       v
          Business  Outbox
            Data     Table
                       |
                       v
                  Outbox Worker
                       |
                       v
                   Message Broker
                       |
                       v
                  Other Services
```

The business update and outbox record are committed in the same database transaction.

A background publisher later sends the outbox event.

### Important Consideration

Outbox publishing may happen more than once if the publisher crashes after sending but before marking the event as published.

Therefore, consumers should be idempotent.

---

# PART 14: GLOBAL SaaS ARCHITECTURE

## 49. Designing a Global SaaS Application

Consider a SaaS product serving customers in:

- India.
- USA.
- Europe.
- Australia.

The system needs:

- Authentication.
- Tenant isolation.
- Regional infrastructure.
- Subscription management.
- API services.
- Database storage.
- Caching.
- Background processing.
- Monitoring.
- Disaster recovery.

### Example Architecture

```text
                    Global Users
                         |
                         v
                    Global DNS
                         |
                         v
                       CDN
                         |
                         v
                Global Load Balancer
                         |
           +-------------+-------------+
           |             |             |
           v             v             v
        Asia Region   US Region    EU Region
           |             |             |
           v             v             v
       API Gateway   API Gateway  API Gateway
           |             |             |
           v             v             v
       App Services  App Services App Services
           |             |             |
           +-------------+-------------+
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
          Database     Redis      Message Queue
             |           |           |
             v           v           v
          Replicas    Cache       Workers
```

### Important Architectural Decisions

#### 1. Tenant Placement

Determine which region owns each tenant's data.

#### 2. Data Isolation

Options include:

- Shared database with tenant IDs.
- Separate schemas.
- Separate databases.
- Dedicated infrastructure for large tenants.

#### 3. Regional Routing

Route tenants to the region where their data is hosted.

#### 4. Regional Failover

Define whether tenants can fail over to another region and what consistency guarantees apply.

#### 5. Data Residency

Some customers may require data to remain within a specific jurisdiction.

#### 6. Tenant-Level Rate Limiting

One tenant should not consume all shared infrastructure resources.

---

# PART 15: PRACTICAL SYSTEM DESIGN EXAMPLE

# 50. Design a Large-Scale E-Commerce Platform

Let's design an e-commerce system similar in broad functionality to Amazon.

## 50.1 Functional Requirements

1. User registration.
2. Login.
3. Product listing.
4. Product search.
5. Shopping cart.
6. Order placement.
7. Payment processing.
8. Inventory management.
9. Order tracking.
10. Product reviews.

## 50.2 Non-Functional Requirements

| Requirement | Target |
|---|---|
| Availability | 99.99% target for critical services |
| Product Browsing | Low-latency responses |
| Checkout | Strong correctness guarantees |
| Scalability | Support large traffic spikes |
| Security | Secure authentication and payment handling |
| Durability | Preserve confirmed orders and payments |
| Recovery | Defined RTO and RPO |

These are example targets, not universal requirements.

## 50.3 High-Level Architecture

```text
                       Users
                         |
                         v
                    Global DNS
                         |
                         v
                        CDN
                         |
                         v
                   Load Balancer
                         |
                         v
                    API Gateway
                         |
       +-----------------+-----------------+
       |                 |                 |
       v                 v                 v
   User Service     Product Service    Order Service
       |                 |                 |
       v                 v                 v
    User DB          Product DB         Order DB
                         |
                         v
                    Search Engine

       +-----------------+-----------------+
       |                 |                 |
       v                 v                 v
 Inventory Service  Payment Service  Notification Service
       |                 |                 |
       v                 v                 v
 Inventory DB        Payment DB        Message Queue
                                           |
                                           v
                                    Background Workers

                     Redis Cluster
                          |
                          v
                  Shared Cache Layer
```

## 50.4 Database Design

### Users

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(150) UNIQUE,
    created_at TIMESTAMP
);
```

### Products

```sql
CREATE TABLE products (
    id BIGINT PRIMARY KEY,
    name VARCHAR(200),
    price DECIMAL(10, 2),
    stock INT,
    created_at TIMESTAMP
);
```

### Orders

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    user_id BIGINT,
    total_amount DECIMAL(10, 2),
    status VARCHAR(30),
    created_at TIMESTAMP
);
```

### Order Items

```sql
CREATE TABLE order_items (
    id BIGINT PRIMARY KEY,
    order_id BIGINT,
    product_id BIGINT,
    quantity INT,
    price DECIMAL(10, 2)
);
```

These are simplified starting schemas. A production design would add foreign keys or equivalent integrity controls, indexes, currency handling, immutable pricing snapshots, idempotency keys and appropriate constraints.

## 50.5 API Design

```http
POST /api/auth/register

POST /api/auth/login

GET /api/products

GET /api/products/:id

POST /api/cart/items

GET /api/cart

POST /api/orders

GET /api/orders/:id

POST /api/payments
```

## 50.6 Product Search Architecture

Product search should not necessarily run as a complex database query on every request.

A search engine can handle:

- Full-text search.
- Filtering.
- Sorting.
- Faceted navigation.
- Relevance ranking.

Example:

```text
Product Database
       |
       v
Indexing Pipeline
       |
       v
Search Engine
       |
       v
Search API
       |
       v
User
```

The search index may be updated asynchronously, so search results can be temporarily stale.

## 50.7 Checkout Flow

A simplified order workflow:

```text
User Checkout
      |
      v
Create Pending Order
      |
      v
Reserve Inventory
      |
      v
Initiate Payment
      |
      v
Payment Confirmation
      |
      v
Confirm Order
      |
      v
Send Notification
```

The workflow should be designed to handle:

- Payment timeouts.
- Duplicate payment requests.
- Inventory reservation expiration.
- Duplicate messages.
- Service crashes.
- Payment provider webhooks.
- Compensation when a step fails.

Use a Saga-style workflow and idempotent operations where appropriate.

## 50.8 Caching Strategy

| Data | Strategy |
|---|---|
| Product Details | Cache-Aside |
| Product Categories | Long TTL |
| User Sessions | Shared session store |
| Shopping Cart | Database or Redis with durability considerations |
| Inventory | Carefully controlled cache or direct authoritative reads |
| Search Results | Short-lived caching where useful |

Never treat cached inventory as the final authority for accepting an order.

## 50.9 Scaling Strategy

| Component | Scaling Approach |
|---|---|
| Frontend | CDN and static delivery |
| API Servers | Horizontal scaling |
| Product Reads | Read replicas and caching |
| Orders | Database optimization and partitioning as needed |
| Search | Dedicated search cluster |
| Background Tasks | Worker scaling |
| Redis | Replication or clustering |
| Message Queue | Partitioning and consumer scaling |

---

# PART 16: FAILURE SCENARIOS AND RESILIENCE

## 51. What Happens When a Component Fails?

A good system design considers failures before they happen.

### Scenario 1: Backend Server Failure

Solution:

- Load balancer health checks.
- Multiple instances.
- Automatic replacement.
- Graceful shutdown.

### Scenario 2: Database Primary Failure

Solution:

- Database failover.
- Replica promotion.
- Connection retry.
- Recovery procedures.
- Data consistency verification.

### Scenario 3: Redis Failure

Possible behavior:

- Cache misses increase.
- Database traffic increases.
- Response latency increases.

Solutions:

- Cache cluster redundancy.
- Circuit breakers.
- Request throttling.
- Controlled fallback behavior.
- Protection against cache stampedes.

### Scenario 4: Message Queue Backlog

Possible causes:

- Workers are unavailable.
- Consumer throughput is too low.
- A downstream dependency is failing.

Solutions:

- Increase consumer capacity.
- Monitor queue age and depth.
- Apply backpressure.
- Scale based on backlog.
- Implement dead-letter queues.
- Investigate poison messages.

### Scenario 5: Traffic Spike

Solutions:

- Autoscaling.
- CDN caching.
- Rate limiting.
- Load shedding.
- Queue-based buffering.
- Graceful degradation.

### Scenario 6: Entire Region Failure

Solutions:

- Global traffic management.
- Secondary region.
- Replicated data.
- Documented failover procedures.
- Tested disaster recovery.

---

## 52. Circuit Breaker Pattern

Circuit breakers prevent repeated calls to a failing dependency.

They commonly have three states.

### Closed

Requests are allowed.

### Open

Requests are blocked temporarily.

### Half-Open

A limited number of test requests are allowed to determine whether the dependency has recovered.

```text
       CLOSED
          |
          | Failures exceed threshold
          v
        OPEN
          |
          | Wait period
          v
      HALF-OPEN
          |
      +---+---+
      |       |
   Success  Failure
      |       |
      v       v
   CLOSED   OPEN
```

### Why Use Circuit Breakers?

- Prevent cascading failures.
- Reduce unnecessary dependency traffic.
- Allow failing services to recover.
- Improve system resilience.

---

## 53. Bulkhead Pattern

The bulkhead pattern isolates resources so that failure in one workload does not consume all available resources.

Example:

```text
              Application
                   |
          +--------+--------+
          |                 |
          v                 v
      Payment Pool      Search Pool
          |                 |
          v                 v
     Payment Calls      Search Calls
```

If search requests become slow, they should not exhaust every connection or worker needed by payment requests.

---

## 54. Backpressure

Backpressure prevents an overloaded component from accepting more work than it can process.

Example:

```text
Producer
   |
   v
Message Queue
   |
   v
Consumers
```

If consumers cannot keep up, the queue grows.

Backpressure mechanisms can:

- Slow producers.
- Limit in-flight messages.
- Reject excess work.
- Pause consumption.
- Reduce concurrency.

Backpressure is particularly important in streaming systems and asynchronous pipelines.

---

# PART 17: OBSERVABILITY AND RELIABILITY ENGINEERING

## 55. SLI, SLO and SLA

These terms are essential in production engineering.

### SLI – Service Level Indicator

An SLI is a metric measuring actual service behavior.

Examples:

- Successful request percentage.
- p95 latency.
- Error rate.
- Availability.

### SLO – Service Level Objective

An SLO is the target for an SLI.

Example:

```text
99.9% of valid API requests
should succeed over a rolling 30-day period.
```

### SLA – Service Level Agreement

An SLA is a formal agreement with customers, often containing consequences if defined service commitments are missed.

### Comparison

| Term | Meaning |
|---|---|
| SLI | What we measure |
| SLO | What we target |
| SLA | What we promise contractually |

---

## 56. Error Budgets

An error budget is the amount of unreliability permitted by an SLO.

Suppose:

```text
SLO = 99.9%
```

Allowed failure rate:

```text
100% - 99.9% = 0.1%
```

This 0.1% is the error budget.

If the system consumes its error budget too quickly, the team may prioritize reliability work over new feature releases.

---

## 57. Distributed Tracing

Distributed tracing tracks a request as it travels across multiple services.

Example:

```text
Client
  |
  v
API Gateway
  |
  v
Order Service
  |
  v
Inventory Service
  |
  v
Payment Service
```

A trace can show:

- Total request duration.
- Time spent in each service.
- Database query duration.
- Failed service calls.
- Dependency relationships.

### Important Terms

| Term | Meaning |
|---|---|
| Trace | Complete request journey |
| Span | One operation within a trace |
| Trace ID | Identifier for the complete trace |
| Span ID | Identifier for an individual span |
| Context Propagation | Passing trace context between services |

---

# PART 18: CHAOS ENGINEERING

## 58. What Is Chaos Engineering?

Chaos engineering is the practice of deliberately introducing controlled failures to test system resilience.

Examples:

- Stop an application instance.
- Introduce network latency.
- Simulate packet loss.
- Terminate a worker.
- Disable a non-critical dependency.
- Simulate a database failover.

### Basic Workflow

```text
Define Steady State
        |
        v
Create Hypothesis
        |
        v
Design Experiment
        |
        v
Run Controlled Failure
        |
        v
Observe Results
        |
        v
Improve System
```

### Example

Hypothesis:

> If one backend instance fails, the application should continue serving requests without a major increase in error rate.

Experiment:

1. Deploy three backend instances.
2. Generate representative traffic.
3. Stop one instance.
4. Observe error rate and latency.
5. Confirm traffic moves to healthy instances.
6. Record results.

### Important Principles

- Start with a small blast radius.
- Define steady-state metrics.
- Use controlled experiments.
- Have rollback procedures.
- Do not introduce uncontrolled production failures.
- Record and fix discovered weaknesses.

---

# PART 19: ADVANCED ARCHITECTURAL DESIGN PRINCIPLES

## 59. Graceful Degradation

Graceful degradation means that when part of a system fails, the system continues offering essential functionality.

Example:

An e-commerce website has a recommendation service.

If recommendations fail:

```text
Product Page
     |
     +----> Product Information: Available
     |
     +----> Pricing: Available
     |
     +----> Recommendations: Unavailable
```

The product page should still work.

Not every dependency should be allowed to take down the entire application.

## 60. Load Shedding

Load shedding deliberately rejects lower-priority work when a system approaches capacity.

Example:

During extreme traffic:

- Continue checkout.
- Continue payment-status queries.
- Temporarily disable recommendations.
- Reject excessive non-essential requests.

This preserves critical business functionality.

## 61. Graceful Shutdown

When an application instance is removed:

1. Stop accepting new requests.
2. Allow in-flight requests to finish within a deadline.
3. Complete or safely release ongoing work.
4. Close database connections.
5. Close message consumers.
6. Exit.

This prevents unnecessary errors during deployments and autoscaling.

## 62. Autoscaling

Autoscaling automatically adjusts infrastructure capacity based on workload.

Possible scaling signals:

- CPU utilization.
- Memory utilization.
- Requests per second.
- Queue depth.
- Queue age.
- Concurrent requests.
- p95 latency.

Example:

```text
CPU > 70%
    |
    v
Add Application Instances

CPU < 30%
    |
    v
Remove Excess Instances
```

Real autoscaling policies need:

- Minimum instance count.
- Maximum instance count.
- Cooldown or stabilization windows.
- Scaling thresholds.
- Startup time considerations.
- Capacity headroom.

**Important:** Scaling only on CPU can miss bottlenecks such as database saturation or growing message queues.

---

# PART 20: DESIGNING SYSTEMS FOR MILLIONS OF USERS

## 63. Evolution of an Application Architecture

A system should evolve based on actual requirements rather than starting with unnecessary complexity.

### Stage 1: Small Application

```text
Frontend
   |
   v
Backend
   |
   v
Database
```

Suitable for a small application.

### Stage 2: Growing Application

```text
             CDN
              |
              v
        Load Balancer
              |
       +------+------+
       |             |
       v             v
    Backend 1     Backend 2
       |             |
       +------+------+
              |
              v
           Database
```

### Stage 3: High-Traffic Application

```text
                 CDN
                  |
                  v
             Load Balancer
                  |
                  v
              API Gateway
                  |
        +---------+---------+
        |         |         |
        v         v         v
      Auth      Product    Order
     Service   Service   Service
        |         |         |
        v         v         v
      Auth DB  Product DB  Order DB
                  |
                  v
              Redis Cache
                  |
                  v
              Message Queue
                  |
                  v
             Worker Cluster
```

### Stage 4: Global Application

```text
                 Global DNS
                      |
                      v
                 Global CDN
                      |
                      v
              Traffic Management
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       US Region   EU Region   Asia Region
          |           |           |
          v           v           v
      Services    Services    Services
          |           |           |
          v           v           v
      Databases   Databases   Databases
          |           |           |
          +-----------+-----------+
                      |
               Replication Layer
```

### Key Lesson

Do not introduce microservices, sharding or multi-region replication simply because an application might become popular.

Introduce complexity when measurable requirements justify it.

---

# PART 21: COMMON SYSTEM DESIGN MISTAKES

## 64. Mistakes to Avoid

| Mistake | Why It Is a Problem | Better Approach |
|---|---|---|
| Designing only for average traffic | Peak traffic causes overload | Estimate peak workload |
| Using one database indefinitely | Creates bottlenecks | Optimize and scale deliberately |
| Adding microservices too early | Adds operational complexity | Start with simpler architecture |
| Ignoring replication lag | Can produce stale reads | Define read consistency |
| Treating Redis as permanent storage | Data may be evicted or lost | Define durability requirements |
| Retrying every failure | Can create retry storms | Use bounded retries and jitter |
| Ignoring idempotency | Duplicate operations | Use idempotency keys |
| Relying only on backups | Recovery may be too slow | Define RTO and RPO |
| Using one region for everything | Regional failure risk | Consider multi-region DR |
| Ignoring database indexes | Slow queries | Analyze execution plans |
| Sharing all resources across tenants | Noisy-neighbor problems | Tenant quotas and isolation |
| Treating cache as authoritative | Stale or incorrect decisions | Preserve authoritative data ownership |
| Assuming DNS failover is instant | DNS caching delays routing changes | Combine routing and health strategies |
| Ignoring observability | Failures become difficult to diagnose | Metrics, logs and traces |
| Never testing recovery | DR plans may not work | Conduct restore and failover exercises |

---

# PART 22: ADVANCED INTERVIEW QUESTIONS

Below are 60 questions organized by difficulty.

## Beginner-Level Questions

1. What is system design?
2. What is the difference between functional and non-functional requirements?
3. What is scalability?
4. What is the difference between vertical and horizontal scaling?
5. What is availability?
6. What is reliability?
7. What is fault tolerance?
8. What is a single point of failure?
9. What is a load balancer?
10. What is a CDN?
11. What is a reverse proxy?
12. What is database replication?
13. What is database sharding?
14. What is distributed caching?
15. What is rate limiting?

## Intermediate-Level Questions

16. What is the difference between Layer 4 and Layer 7 load balancing?
17. Explain Round Robin and Least Connections algorithms.
18. What is the difference between stateless and stateful services?
19. What is replication lag?
20. What is read-after-write consistency?
21. Explain the cache-aside pattern.
22. What is a cache stampede?
23. Explain the token bucket algorithm.
24. What is consistent hashing?
25. What is the difference between partitioning and sharding?
26. What is the purpose of a message queue?
27. What is the Saga pattern?
28. What is the Transactional Outbox pattern?
29. What is a circuit breaker?
30. What is the bulkhead pattern?

## Advanced-Level Questions

31. Explain the CAP theorem with an example.
32. What is the PACELC theorem?
33. What is linearizability?
34. Explain eventual consistency.
35. What is quorum-based replication?
36. What are the limitations of the condition R + W > N?
37. Explain the Raft consensus algorithm.
38. What is leader election?
39. What is split brain?
40. What are fencing tokens?
41. Explain distributed locking.
42. What is the difference between 2PC and Saga?
43. What is the difference between active-active and active-passive architecture?
44. How would you design a multi-region database?
45. How would you prevent duplicate payment processing?

## Expert-Level Questions

46. Design a system that handles 100,000 requests per second.
47. Design a globally distributed e-commerce application.
48. How would you handle a complete regional outage?
49. How would you design a distributed rate limiter?
50. How would you handle a database hot partition?
51. How would you perform online database sharding?
52. How would you reduce p99 latency in a distributed application?
53. How would you design a system that guarantees read-your-writes consistency?
54. How would you prevent cascading failures?
55. How would you design a highly available payment system?
56. How would you handle duplicate and out-of-order messages?
57. How would you design a multi-tenant SaaS architecture for millions of users?
58. How would you design a disaster recovery strategy with an RPO of one minute?
59. How would you handle cache failure during a major traffic spike?
60. How would you design an application that supports millions of concurrent users?

---

# PART 23: PRACTICAL ASSIGNMENTS

Complete these assignments progressively. The first assignments focus on architecture and calculations; later assignments involve distributed implementation and failure testing.

## Level 1 – Fundamentals

1. Design a basic URL shortener architecture.
2. Identify functional and non-functional requirements for an e-commerce application.
3. Create a system architecture diagram for a blogging platform.
4. Compare vertical and horizontal scaling with practical examples.
5. Calculate the availability of a service from uptime and downtime data.
6. Estimate daily API traffic for 100,000 DAU.
7. Calculate average RPS from daily request volume.
8. Calculate peak RPS using a peak-to-average ratio.
9. Estimate storage requirements for 10 million records.
10. Apply Little's Law to estimate concurrency.

## Level 2 – Performance and Load Balancing

11. Create an Nginx reverse proxy configuration.
12. Configure two backend instances behind a load balancer.
13. Test Round Robin distribution.
14. Implement a simple least-connections load-balancing simulation.
15. Configure health checks for backend services.
16. Implement request timeouts in Node.js.
17. Implement exponential backoff with jitter.
18. Implement an idempotency-key mechanism for an API.
19. Measure API p50, p95 and p99 latency.
20. Perform a load test and identify the first performance bottleneck.

## Level 3 – Database Scaling

21. Create a database primary-replica architecture.
22. Configure read/write separation in an application.
23. Demonstrate replication lag and stale reads.
24. Implement read-your-writes routing.
25. Partition an orders table by date.
26. Implement range-based sharding in a sample application.
27. Implement hash-based sharding.
28. Build a consistent hashing simulation.
29. Compare different shard keys using simulated traffic.
30. Design an online resharding strategy.

## Level 4 – Distributed Systems

31. Simulate a network partition between three nodes.
32. Create a simple leader-election simulation.
33. Study Raft using a small replicated-log experiment.
34. Implement a distributed lock using Redis.
35. Demonstrate a stale lock holder problem.
36. Implement fencing-token validation in a protected resource.
37. Build a cache-aside implementation using Redis.
38. Simulate a cache stampede.
39. Implement TTL jitter.
40. Implement a distributed token-bucket rate limiter.

## Level 5 – Reliability and Transactions

41. Implement a circuit breaker in Node.js.
42. Implement bulkhead isolation for two independent workloads.
43. Create a queue-based background-processing system.
44. Implement consumer retry and dead-letter handling.
45. Implement an idempotent message consumer.
46. Implement a Saga workflow for order and inventory services.
47. Implement the Transactional Outbox pattern.
48. Simulate a payment timeout and safe retry.
49. Implement graceful shutdown for a Node.js service.
50. Implement backpressure for a worker queue.

## Level 6 – Enterprise and Global Architecture

51. Design an active-active multi-region application.
52. Design an active-passive disaster recovery architecture.
53. Define RTO and RPO for a payment system.
54. Create and test a database backup restoration procedure.
55. Design a global SaaS architecture with tenant-based regional routing.
56. Create an architecture diagram for a system supporting 10 million users.
57. Design a distributed notification platform.
58. Design a globally distributed product catalog.
59. Perform a controlled failure experiment against a backend cluster.
60. Write a complete system design document for a high-availability e-commerce platform.

---

# PART 24: QUICK REVISION NOTES

## 65. Important Concepts Summary

| Concept | Key Point |
|---|---|
| System Design | Designing complete software architecture |
| Scalability | Handling increasing workloads |
| Availability | Service accessibility |
| Reliability | Correct operation over time |
| Durability | Preserving committed data |
| Vertical Scaling | Increasing machine resources |
| Horizontal Scaling | Adding more machines |
| Load Balancer | Distributing requests |
| Reverse Proxy | Acting on behalf of backend servers |
| CDN | Geographically distributed content delivery |
| RPS | Requests per second |
| QPS | Queries per second |
| p99 Latency | Latency within which 99% of requests complete |
| Little's Law | L = λ × W |
| Replication | Maintaining copies of data |
| Replication Lag | Delay in applying updates to replicas |
| Partitioning | Dividing data into logical or physical partitions |
| Sharding | Distributing data across database nodes |
| Consistent Hashing | Minimizing key remapping when nodes change |
| CAP | Consistency, Availability and Partition Tolerance |
| PACELC | CAP tradeoff plus latency/consistency tradeoff without partition |
| Linearizability | Operations respect real-time ordering |
| Eventual Consistency | Replicas converge when updates stop and propagation succeeds |
| Quorum | Required number of replica responses |
| Raft | Consensus algorithm |
| Leader Election | Selecting a coordinating node |
| Split Brain | Multiple nodes incorrectly acting as primary |
| Fencing Token | Rejecting operations from stale lock holders |
| Distributed Lock | Coordinating shared resource access |
| Cache-Aside | Application manages cache reads and misses |
| Cache Stampede | Many requests reload an expired item |
| Token Bucket | Rate limiting with controlled bursts |
| Circuit Breaker | Preventing calls to failing dependencies |
| Bulkhead | Isolating resources |
| Backpressure | Controlling incoming work |
| Saga | Distributed workflow with compensating actions |
| 2PC | Two-phase distributed commit |
| Outbox | Reliable event publication from a local transaction |
| RTO | Maximum acceptable recovery time |
| RPO | Maximum acceptable data-loss window |
| Active-Active | Multiple regions actively serve traffic |
| Active-Passive | One primary region with standby capacity |
| SLI | Measured reliability indicator |
| SLO | Reliability target |
| SLA | Formal service commitment |
| Error Budget | Allowed unreliability under an SLO |
| Chaos Engineering | Controlled failure experiments |

---

# PART 25: COMPLETION CHECKLIST

Before considering Phase 77 complete, verify that you can explain and apply the following.

### System Design

- [ ] Explain functional and non-functional requirements.
- [ ] Identify scalability, reliability and availability requirements.
- [ ] Design high-level system architecture.
- [ ] Estimate traffic, storage and bandwidth.
- [ ] Calculate concurrency using Little's Law.

### Performance and Scaling

- [ ] Explain latency percentiles.
- [ ] Implement timeouts and retries.
- [ ] Explain exponential backoff and jitter.
- [ ] Compare vertical and horizontal scaling.
- [ ] Explain stateless and stateful architecture.
- [ ] Understand Layer 4 and Layer 7 load balancing.

### Infrastructure

- [ ] Explain reverse proxies.
- [ ] Explain CDN caching.
- [ ] Understand DNS-based routing.
- [ ] Design a multi-region deployment.

### Database Engineering

- [ ] Explain replication and replication lag.
- [ ] Understand synchronous and asynchronous replication.
- [ ] Compare partitioning and sharding.
- [ ] Implement a basic sharding strategy.
- [ ] Understand consistent hashing.
- [ ] Explain quorum reads and writes.

### Distributed Systems

- [ ] Explain CAP theorem.
- [ ] Explain PACELC theorem.
- [ ] Compare consistency models.
- [ ] Understand distributed consensus.
- [ ] Explain Raft and leader election.
- [ ] Understand split brain.
- [ ] Explain distributed locks and fencing tokens.

### Reliability

- [ ] Implement distributed caching.
- [ ] Explain cache stampede prevention.
- [ ] Implement distributed rate limiting.
- [ ] Understand circuit breakers and bulkheads.
- [ ] Explain backpressure and load shedding.
- [ ] Understand Saga and Transactional Outbox.

### High Availability

- [ ] Compare active-active and active-passive architecture.
- [ ] Identify single points of failure.
- [ ] Define RTO and RPO.
- [ ] Design a disaster recovery strategy.
- [ ] Understand graceful degradation.
- [ ] Understand chaos engineering.

---

# PART 26: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

Use these resources to deepen your understanding and explore implementation details.

| Resource | Topics |
|---|---|
| [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html?utm_source=chatgpt.com) | Reliability, security, performance and cost |
| [Google SRE Book](https://sre.google/sre-book/table-of-contents/?utm_source=chatgpt.com) | Site reliability engineering |
| [Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework?utm_source=chatgpt.com) | Cloud architecture and system design |
| [Redis Documentation](https://redis.io/docs/latest/?utm_source=chatgpt.com) | Caching, distributed locks and data structures |
| [Nginx Documentation](https://nginx.org/en/docs/?utm_source=chatgpt.com) | Reverse proxy and load balancing |
| [Kubernetes Documentation](https://kubernetes.io/docs/home/?utm_source=chatgpt.com) | Container orchestration and autoscaling |
| [PostgreSQL Documentation](https://www.postgresql.org/docs/?utm_source=chatgpt.com) | Replication, partitioning and database internals |
| [Apache Kafka Documentation](https://kafka.apache.org/documentation/?utm_source=chatgpt.com) | Distributed messaging and event streaming |
| [etcd Documentation](https://etcd.io/docs/?utm_source=chatgpt.com) | Distributed coordination and consensus |
| [OpenTelemetry Documentation](https://opentelemetry.io/docs/?utm_source=chatgpt.com) | Distributed tracing, metrics and observability |

---

# PHASE 77 – FINAL SUMMARY

In this phase, you covered the architecture and engineering principles used to build large-scale distributed applications.

The most important concepts are:

1. **System Design:** Requirements, architecture and capacity planning.
2. **Scalability:** Vertical scaling, horizontal scaling and load balancing.
3. **Performance:** Throughput, latency percentiles, retries and backpressure.
4. **Global Delivery:** CDN, DNS and multi-region architecture.
5. **Database Scaling:** Replication, partitioning, sharding and consistent hashing.
6. **Distributed Systems:** CAP, PACELC, consistency models and consensus.
7. **Coordination:** Leader election, distributed locks and fencing tokens.
8. **Caching:** Distributed caching and cache invalidation.
9. **Reliability:** Circuit breakers, bulkheads and graceful degradation.
10. **Transactions:** Saga, 2PC and Transactional Outbox.
11. **High Availability:** Active-active, active-passive and disaster recovery.
12. **Production Engineering:** SLI, SLO, SLA, error budgets and chaos engineering.

**The most important takeaway:** Large-scale system design is not simply about adding more servers. It is about making deliberate tradeoffs between performance, consistency, availability, reliability, complexity and cost.

---

# NEXT PHASE

## PHASE 78 – ADVANCED DATA ENGINEERING, DISTRIBUTED DATA PLATFORMS, STREAMING PIPELINES AND REAL-TIME ANALYTICS

Upcoming topics:

1. Data Engineering Fundamentals.
2. OLTP vs OLAP.
3. Data Warehousing.
4. Data Lakes and Data Lakehouses.
5. ETL vs ELT.
6. Batch Processing.
7. Stream Processing.
8. Apache Kafka Advanced Architecture.
9. Kafka Producers and Consumers.
10. Kafka Partitions and Consumer Groups.
11. Change Data Capture (CDC).
12. Event Streaming Architecture.
13. Apache Spark Fundamentals.
14. Apache Flink Fundamentals.
15. Data Pipeline Architecture.
16. Data Orchestration.
17. Workflow Scheduling.
18. Data Modeling.
19. Dimensional Modeling.
20. Star Schema and Snowflake Schema.
21. Data Quality Engineering.
22. Data Validation.
23. Data Governance.
24. Data Lineage.
25. Data Catalogs.
26. Real-Time Analytics.
27. Time-Series Data.
28. Analytical Databases.
29. Data Observability.
30. Enterprise Data Platform Architecture.

**When you're ready, say: "OK, Continue to Phase 78."**