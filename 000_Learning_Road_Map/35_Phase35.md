# **Phase 35 – Advanced Backend Architecture, Scalability and System Design**



Advanced Level

Backend Engineering

Next.js 16.x · TypeScript · Node.js



## **1. Learning objectives**

In this phase, you will learn how to design backend applications that remain reliable and performant as the number of users, requests, and amount of data increase.

By the end, you should be able to:

- Explain how scalability works and distinguish vertical from horizontal scaling.
- Design applications using load balancers, reverse proxies, and stateless services.
- Scale databases using indexes, read replicas, partitioning, and sharding.
- Use connection pools, caches, queues, and background workers effectively.
- Design systems that tolerate failures and remain highly available.
- Estimate system capacity and identify performance bottlenecks.
- Apply system-design principles to real-world applications such as e-commerce platforms, chat applications, and dashboards.

## **2. Complete topic roadmap**

1. Scalability Fundamentals
2. Vertical vs Horizontal Scaling
3. Load Balancing
4. Reverse Proxy Architecture
5. Stateless vs Stateful Applications
6. Database Scaling Strategies
7. Database Indexing and Query Optimization
8. Read Replicas and Replication
9. Database Sharding and Partitioning
10. Connection Pooling
11. CDN and Edge Architecture
12. Asynchronous Job Processing
13. Background Workers and Queues
14. Distributed Rate Limiting
15. Fault Tolerance and High Availability
16. Capacity Planning
17. System Design Fundamentals
18. Designing Real-World Scalable Applications

## **3. Scalability fundamentals**

### **3.1 What is scalability?**

**Scalability** is the ability of a system to handle an increasing workload by adding resources while maintaining acceptable performance, reliability, and cost efficiency.

A workload can increase in several ways:

- More concurrent users.
- More HTTP requests per second.
- More database queries.
- More records stored in the database.
- More file uploads and downloads.
- More background jobs.
- More real-time connections.

Imagine you build an e-commerce application.

Initially:

- 100 users visit your website daily.
- 10 users place orders.
- Your Node.js server and MongoDB database handle the workload easily.

After your application becomes popular:

- 100,000 users visit daily.
- 5,000 users browse products simultaneously.
- 500 users place orders within a short period.
- Thousands of product images are requested.

Your original infrastructure may no longer handle the workload efficiently.

You need to scale the system.

### **3.2 Scalability vs performance**

These concepts are related, but they are not identical.

| Concept      | Meaning                                               |
| ------------ | ----------------------------------------------------- |
| Performance  | How efficiently a system handles its current workload |
| Scalability  | How well a system handles increasing workload         |
| Reliability  | Whether a system continues to operate correctly       |
| Availability | Whether a system is accessible when users need it     |
| Throughput   | Amount of work completed per unit of time             |
| Latency      | Time taken to complete an operation                   |

For example:

- An API responds in 100 ms: this describes latency and performance.
- An API handles 10,000 requests per second: this describes throughput.
- An API maintains acceptable response times after traffic increases tenfold: this demonstrates scalability.

### **3.3 Important scalability metrics**

| Metric                    | Example     | What it tells you                    |
| ------------------------- | ----------- | ------------------------------------ |
| Requests per second (RPS) | 2,000       | Incoming request rate                |
| Concurrent users          | 5,000       | Users active at the same time        |
| Average latency           | 120 ms      | Average response time                |
| p95 latency               | 350 ms      | 95% of requests finish within 350 ms |
| p99 latency               | 900 ms      | 99% of requests finish within 900 ms |
| CPU utilization           | 75%         | CPU resource consumption             |
| Memory utilization        | 80%         | Memory resource consumption          |
| Queue depth               | 10,000 jobs | Pending background work              |
| Database connections      | 150         | Active database connections          |

**Important: Average latency alone can be misleading.**

Suppose an API handles 1,000 requests:

- 950 requests finish in 100 ms.
- 50 requests take 2 seconds.

The average may look acceptable, but some users experience significant delays.

This is why production systems monitor percentiles such as p95 and p99.

### **3.4 Identify the bottleneck**

A bottleneck is the component that limits the performance or throughput of the entire system.

Consider this architecture:

**Users / Clients**

**Next.js / Node.js Application**

**Redis Cache**

Fast data access

**Database**

Persistent storage

Even if your Node.js server is powerful, your application can remain slow if:

- Database queries are inefficient.
- The database has insufficient connections.
- External APIs respond slowly.
- CPU-intensive tasks block the Node.js event loop.
- Large files are served directly from the application server.

**Key principle:** Scaling the wrong component does not solve the bottleneck.

For example, adding five Node.js servers will not necessarily improve performance if every request waits for one overloaded database.

## **4. Vertical scaling vs horizontal scaling**

There are two fundamental approaches to increasing infrastructure capacity.

### **4.1 Vertical scaling (scale up)**

Vertical scaling means increasing the resources of an existing server.

For example:

## **Vertical scaling**

**Before**

# **2 CPU**

4 GB RAM

One server

**After**

# **8 CPU**

32 GB RAM

Same server, more resources

Illustrative resource sizes, not a required upgrade configuration.

You upgrade the machine's:

- CPU.
- RAM.
- Storage.
- Network capacity.

Example: upgrading a VPS from 2 vCPUs and 4 GB RAM to 8 vCPUs and 32 GB RAM.

**Advantages**

- Simple architecture.
- Usually requires fewer application changes.
- No need to coordinate multiple application instances.
- Useful for databases and smaller applications.

**Disadvantages**

- Hardware has an upper limit.
- Larger machines can become expensive.
- A single machine can remain a single point of failure.
- Maintenance or machine failure can interrupt the entire service.

### **4.2 Horizontal scaling (scale out)**

Horizontal scaling means adding more servers or application instances.

Instead of making one server more powerful, you distribute workload across multiple servers.

## **Horizontal scaling**

**Incoming traffic**

**Load Balancer**

**Server 1**

Running

**Server 2**

Running

**Server 3**

Running

**Shared Database / Cache**

For example, instead of running one Node.js server, you run three instances:

- Server 1 handles some requests.
- Server 2 handles other requests.
- Server 3 handles the remaining requests.

A load balancer distributes incoming traffic among them.

**Advantages**

- Capacity can grow by adding instances.
- Better fault isolation.
- Instances can be deployed or replaced independently.
- Useful for large applications and distributed systems.

**Disadvantages**

- Requires traffic distribution.
- Application instances should generally be stateless.
- Session management becomes more complex.
- Shared resources such as databases can become bottlenecks.
- Deployment and monitoring require more infrastructure.

### **4.3 Vertical vs horizontal scaling**

| Feature          | Vertical scaling                | Horizontal scaling          |
| ---------------- | ------------------------------- | --------------------------- |
| Method           | Upgrade one machine             | Add more machines           |
| Complexity       | Lower                           | Higher                      |
| Maximum capacity | Machine-dependent               | Can grow across machines    |
| Fault tolerance  | Limited if single machine       | Can improve with redundancy |
| Cost             | Larger machine may be expensive | More instances add cost     |
| Architecture     | Usually simpler                 | Requires distributed design |
| Example          | 2 CPU → 8 CPU                   | 1 server → 4 servers        |

### **4.4 Which one should you use?**

A small application hosted on a VPS may start with vertical scaling.

As traffic increases, you may:

1. Optimize application code.
2. Optimize database queries.
3. Add caching.
4. Add a load balancer.
5. Run multiple application instances.
6. Scale database infrastructure independently.

In practice, production systems often use a combination of vertical and horizontal scaling.

**Interview question:** Is horizontal scaling always better than vertical scaling?

No. Horizontal scaling introduces distributed-system complexity. For a small application, upgrading one server may be simpler and more cost-effective.

## **5. Load balancing**

### **5.1 What is a load balancer?**

A **load balancer** distributes incoming network requests across multiple servers or application instances.

Its primary purpose is to prevent one server from receiving all the traffic when other servers have available capacity.

Without a load balancer:

Users

**Server 1**

All traffic

One server receives every request.

With a load balancer:

Users

**Load Balancer**

Server 1

Server 2

Server 3

### **5.2 Types of load balancers**

**Layer 4 load balancing (Transport Layer)**

Works at the TCP/UDP level.

It can distribute connections based on information such as:

- Source IP address.
- Destination IP address.
- Port number.
- Transport protocol.

It generally does not need to inspect HTTP application content.

**Layer 7 load balancing (Application Layer)**

Works at the HTTP/HTTPS level.

It can make routing decisions using:

- URL path.
- Hostname.
- HTTP headers.
- Cookies.
- Other HTTP request attributes.

Example:

| Incoming request           | Destination       |
| -------------------------- | ----------------- |
| `example.com/api/products` | Product service   |
| `example.com/api/orders`   | Order service     |
| `admin.example.com`        | Admin application |
| `example.com/uploads`      | File service      |

### **5.3 Load-balancing algorithms**

**1. Round Robin**

Distributes requests sequentially.

For three servers:

```
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
Request 5 → Server 2
```

Useful when servers have similar capacity and request costs are relatively uniform.

**2. Weighted Round Robin**

Distributes more requests to servers with higher assigned weights.

```
Server 1: Weight 5
Server 2: Weight 3
Server 3: Weight 2
```

A higher-weight server receives a larger share of traffic.

**3. Least Connections**

Sends a new request or connection to the server with the fewest active connections.

Useful when connections have different durations.

**4. IP Hash**

Uses a hash of the client's IP address to select a server.

This can help keep a client mapped to the same server, but it does not guarantee reliable session persistence in every environment. NAT, changing IPs, and uneven traffic can cause problems.

**5. Least Response Time**

Chooses a server using response-time and load-related measurements, depending on the load balancer implementation.

### **5.4 Health checks**

A load balancer should avoid routing traffic to an unhealthy application instance.

A common health endpoint in Express:

```
import express from "express";

const app = express();

app.get("/health", (_req, res) => {
  res.status(200).json({
    status: "ok",
    timestamp: new Date().toISOString(),
  });
});

app.listen(3000);
```

A basic health check confirms that the application process can respond.

In production, distinguish between:

- **Liveness:** Is the process alive and functioning?
- **Readiness:** Can this instance safely receive traffic?
- **Startup:** Has the application completed initialization?

Do not make a liveness check depend on every external service. A temporary database outage should not necessarily cause every application process to restart repeatedly.

### **5.5 Common load-balancing products**

- NGINX
- HAProxy
- AWS Elastic Load Balancing
- Google Cloud Load Balancing
- Azure Load Balancer

**Important:** A load balancer distributes traffic; it does not automatically make an application scalable. The application, database, cache, and downstream services must also handle the increased workload.

## **6. Reverse proxy architecture**

### **6.1 What is a reverse proxy?**

A **reverse proxy** is a server that sits between clients and backend servers.

The client sends a request to the reverse proxy. The reverse proxy forwards that request to an appropriate backend server and returns the backend response to the client.

The client generally does not need to know which internal server processed its request.

**Browser / Client**

**NGINX Reverse Proxy**

TLS termination · Routing · Compression

**Next.js**

Port 3000

**Express API**

Port 4000

### **6.2 Reverse proxy vs forward proxy**

| Feature                          | Forward proxy                               | Reverse proxy                        |
| -------------------------------- | ------------------------------------------- | ------------------------------------ |
| Represents                       | Client                                      | Server                               |
| Position                         | In front of clients                         | In front of backend servers          |
| Main purpose                     | Control client access to external resources | Protect and route backend traffic    |
| Client knows destination server? | Usually yes                                 | Usually sees the proxy as the server |
| Example                          | Corporate internet proxy                    | NGINX in front of Node.js            |

### **6.3 Why use a reverse proxy?**

1. **Load balancing:** Distribute traffic among multiple backend instances.
2. **TLS termination:** Handle HTTPS encryption at the proxy.
3. **Routing:** Route different paths to different services.
4. **Compression:** Compress eligible responses.
5. **Security:** Keep internal services inaccessible directly from the public internet.
6. **Request limits:** Apply request-size and connection restrictions.
7. **Static file serving:** Serve static resources efficiently.

### **6.4 NGINX reverse proxy example**

Suppose:

- Next.js runs on port 3000.
- Express runs on port 4000.
- NGINX accepts public HTTP traffic.

Example NGINX configuration:

```
server {
    listen 80;
    server_name example.com;

    location /api/ {
        proxy_pass http://127.0.0.1:4000/;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For
                         $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        proxy_pass http://127.0.0.1:3000;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For
                         $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

The trailing slash in `proxy_pass` affects URI forwarding. Verify the desired path mapping before using this configuration.

Example:

```
https://example.com/
       ↓
NGINX
       ↓
Next.js :3000

https://example.com/api/users
       ↓
NGINX
       ↓
Express :4000
```

**Important:** When an application is behind a proxy, configure trusted proxy handling correctly. Do not blindly trust arbitrary client-supplied `X-Forwarded-For` headers.

## **7. Stateless vs stateful applications**

This concept is extremely important for horizontal scaling.

### **7.1 What is a stateful application?**

A stateful application stores information about a client's session or interaction inside its own process memory or local storage.

Example:

```
const userSessions = new Map<string, string>();

userSessions.set("session-101", "user-501");
```

Here, the server stores session information in its local memory.

Imagine you have two servers:

```
Server 1:
session-101 → user-501

Server 2:
No session-101
```

A user logs in through Server 1. Later, the load balancer sends another request to Server 2.

Server 2 does not know about the session.

The user may unexpectedly appear logged out.

### **7.2 What is a stateless application?**

A stateless application does not depend on local application memory to retain client session state between requests.

Each request contains the information needed to identify and process the request, or the application retrieves the required state from a shared external system.

Examples:

- Signed access token.
- Session ID stored in a cookie, with session data in Redis.
- Session ID validated against a shared database.

Example:

```
Browser
   |
   | Request + session cookie
   |
Load Balancer
   |
   ├── Server 1
   ├── Server 2
   └── Server 3
          |
          ↓
       Redis
          |
          ↓
     Session data
```

All servers can access the same session store.

### **7.3 Stateless vs stateful**

| Feature            | Stateful                      | Stateless                                |
| ------------------ | ----------------------------- | ---------------------------------------- |
| Session storage    | Often local                   | External/shared or carried in request    |
| Horizontal scaling | More difficult                | Easier                                   |
| Server dependency  | May depend on previous server | Requests can reach different instances   |
| Session sharing    | Requires synchronization      | Shared store or token verification       |
| Failure recovery   | Local state can be lost       | External state can survive instance loss |
| Example            | In-memory session map         | Redis-backed session                     |

### **7.4 How to make a Node.js application stateless**

Avoid storing important user-session data only in local memory.

Instead, use:

- Redis for shared sessions and temporary state.
- Database for persistent business data.
- Signed tokens for verifiable identity claims.
- Object storage for uploaded files.
- Shared queues for background jobs.

For example, a session might be represented by:

```
Cookie:
session_id = random-session-id

Redis:
session:random-session-id
    userId: 501
    role: admin
    expiresAt: ...
```

Every Node.js instance can look up the same session in Redis.

**Important distinction:** Stateless does not mean the whole system has no state. It means an individual application instance does not need to retain client state locally between requests.

Sticky sessions can sometimes be used as a transitional solution, but they do not replace shared session storage for resilient horizontal scaling.

## **8. Database scaling strategies**

A database can become the bottleneck even when your application servers scale successfully.

Consider:

```
10,000 users
     |
Load Balancer
     |
5 Node.js instances
     |
     ↓
One MongoDB server
```

All five application instances send database queries to one database server.

The database may become overloaded because of:

- Too many concurrent queries.
- Inefficient indexes.
- Large result sets.
- Too many database connections.
- Write contention.
- Disk or memory limitations.

There are several database scaling strategies.

### **8.1 Strategy 1: Optimize before scaling**

Before adding database servers:

1. Inspect slow queries.
2. Add appropriate indexes.
3. Avoid fetching unnecessary fields.
4. Use pagination.
5. Avoid N+1 query patterns.
6. Reduce unnecessary database round trips.
7. Use connection pooling.
8. Cache frequently accessed data.

A poorly optimized query can remain expensive even on a powerful database server.

### **8.2 Strategy 2: Vertical database scaling**

Increase database server resources:

- More CPU.
- More RAM.
- Faster SSD storage.
- Higher IOPS.
- Better network capacity.

This is often the simplest first step for a growing database.

### **8.3 Strategy 3: Read replicas**

Create additional database instances that receive replicated data and handle read requests.

```
             Node.js Application
                     |
              Database Router
                 /       \
                /         \
          Write Queries  Read Queries
               |              |
               ↓              ↓
          Primary DB      Read Replica 1
                               |
                               ↓
                         Read Replica 2
```

The primary database handles writes. Read replicas serve eligible reads.

### **8.4 Strategy 4: Partitioning**

Divide a large table or collection into smaller logical parts.

For example, an orders table can be partitioned by month:

```
orders
  |
  ├── orders_2026_01
  ├── orders_2026_02
  ├── orders_2026_03
  └── orders_2026_04
```

Partitioning can improve data management and certain query patterns, depending on the database and query design.

### **8.5 Strategy 5: Sharding**

Distribute data across multiple database servers.

For example:

```
User ID 1–100000     → Shard 1
User ID 100001–200000 → Shard 2
User ID 200001–300000 → Shard 3
```

Each shard stores only a portion of the total data.

Sharding is a more advanced distributed database architecture. It introduces challenges involving shard keys, cross-shard queries, transactions, rebalancing, and operational complexity.

### **8.6 Database scaling comparison**

| Strategy           | What changes?               | Primary benefit                                           |
| ------------------ | --------------------------- | --------------------------------------------------------- |
| Query optimization | Queries and indexes         | Less unnecessary database work                            |
| Vertical scaling   | Machine resources           | More capacity per machine                                 |
| Read replicas      | Number of database copies   | More read capacity                                        |
| Partitioning       | Organization of data        | Manageability and query efficiency for suitable workloads |
| Sharding           | Distribution across servers | Distributes storage and workload                          |

## **9. Database indexing and query optimization**

### **9.1 What is a database index?**

A database index is a data structure that helps the database locate records without scanning every row or document.

Think of an index in a book.

Without an index, you may need to read every page to find a topic.

With an index, you can quickly locate the relevant page.

The same idea applies to databases.

### **9.2 Example without an index**

Suppose you have one million users:

```
SELECT *
FROM users
WHERE email = 'user@example.com';
```

Without a suitable index, the database may need to examine many rows.

This is called a table scan or sequential scan, depending on the database.

### **9.3 Create an index in MySQL**

```
CREATE INDEX idx_users_email
ON users(email);
```

Now the database can use the index to locate matching records more efficiently when the query planner determines that using it is beneficial.

### **9.4 Create an index in MongoDB**

```
db.users.createIndex(
  { email: 1 },
  { unique: true }
);
```

`1` indicates ascending index order.

`-1` indicates descending index order.

### **9.5 Types of indexes**

| Index type   | Purpose                                                       |
| ------------ | ------------------------------------------------------------- |
| Single-field | Index one field                                               |
| Compound     | Index multiple fields                                         |
| Unique       | Prevent duplicate indexed values                              |
| Text         | Text search capabilities                                      |
| Multikey     | Index array values in MongoDB                                 |
| TTL          | Automatically expire eligible MongoDB documents               |
| Partial      | Index only documents matching a filter in supported databases |
| Geospatial   | Spatial and location queries                                  |

### **9.6 Compound indexes**

Suppose your application frequently executes:

```
SELECT *
FROM orders
WHERE user_id = 501
ORDER BY created_at DESC;
```

You could create:

```
CREATE INDEX idx_orders_user_created
ON orders(user_id, created_at);
```

A compound index can support queries using its leading fields.

For MongoDB:

```
db.orders.createIndex({
  userId: 1,
  createdAt: -1
});
```

**Important:** Index field order matters. A compound index on `(user_id, created_at)` is not equivalent to an index on `(created_at, user_id)`.

### **9.7 Index trade-offs**

Indexes are not free.

They consume:

- Disk storage.
- Memory or cache capacity.
- Additional write processing.
- Maintenance resources.

Every insert, update, or delete may require relevant indexes to be maintained.

Do not create indexes on every field without understanding your query workload.

### **9.8 Query optimization using EXPLAIN**

In MySQL:

```
EXPLAIN
SELECT *
FROM orders
WHERE user_id = 501;
```

In PostgreSQL:

```
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE user_id = 501;
```

In MongoDB:

```
db.orders
  .find({ userId: 501 })
  .explain("executionStats");
```

Look for:

- Index usage.
- Number of documents or rows examined.
- Number of documents or rows returned.
- Execution time.
- Expensive sorting.
- Unexpected collection or table scans.

PostgreSQL's `EXPLAIN` displays the query planner's execution plan, including scan and join choices.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.postgresql.org\&sz=32)

PostgreSQL



### **9.9 Example: inefficient vs efficient API query**

Inefficient:

```
const users = await User.find({});
```

This may retrieve the entire collection.

Better:

```
const users = await User.find(
  { isActive: true },
  {
    name: 1,
    email: 1,
  }
)
  .sort({ createdAt: -1 })
  .limit(20)
  .lean();
```

This example:

- Filters active users.
- Selects only necessary fields.
- Sorts the results.
- Limits the number of returned records.
- Uses lean objects rather than full Mongoose documents.

For this query, evaluate whether an index such as `{ isActive: 1, createdAt: -1 }` is appropriate for the actual workload.

**Important:** An index does not guarantee that a query becomes faster. Measure the query plan and execution statistics.

## **10. Read replicas and replication**

### **10.1 What is database replication?**

Replication means maintaining copies of database data across multiple database servers.

A common arrangement is:

- Primary: handles writes.
- Secondary/replica: receives replicated changes.
- Additional replicas: may serve read requests or support availability.

```
             Application
                  |
                  ↓
             Primary DB
                  |
           Replication stream
              /       \
             ↓         ↓
         Replica 1  Replica 2
```

### **10.2 Synchronous replication**

The primary waits for an acknowledgement from one or more replicas according to the database's synchronous replication configuration.

**Advantages:**

- Can provide stronger replication guarantees.
- Helps reduce the chance of losing acknowledged transactions under the configured failure model.

**Disadvantages:**

- Can increase write latency.
- Availability may be affected when required replicas are unavailable.

### **10.3 Asynchronous replication**

The primary commits a write without waiting for a replica to confirm that it has received the change.

**Advantages:**

- Usually lower write latency.
- Replicas can serve read traffic without blocking primary writes.

**Disadvantages:**

- Replicas can lag behind the primary.
- A recent write may not immediately appear on a replica.

### **10.4 Replication lag**

Suppose a user updates their profile:

```
Primary:
Name = Riyaz Updated

Replica:
Name = Riyaz Old
```

If the next read is sent to a lagging replica, the user may temporarily see old data.

This is called **replication lag**.

It can produce a read-after-write consistency problem.

Possible solutions include:

- Read recent changes from the primary.
- Use a database's consistency or read-preference options.
- Wait for a required replication acknowledgement where supported.
- Design the UI to tolerate eventual consistency when appropriate.

### **10.5 MongoDB replica sets**

MongoDB supports replica sets.

A replica set typically contains:

- One primary.
- One or more secondary members.
- Optional voting members such as an arbiter, depending on topology.

The primary receives writes. Secondaries replicate the primary's operation log.

Example connection string:

```
MONGODB_URI=mongodb://host1:27017,host2:27017,host3:27017/mydb?replicaSet=rs0
```

In production, use the correct authentication, TLS, and deployment-specific connection configuration.

MongoDB replica sets provide redundancy and automatic election mechanisms, but application code still needs to handle temporary connection failures and retryable operations correctly.

### **10.6 Read preference in MongoDB**

MongoDB provides read preference modes such as:

- `primary`
- `primaryPreferred`
- `secondary`
- `secondaryPreferred`
- `nearest`

Example:

```
const users = await User.find({ isActive: true })
  .read("secondaryPreferred");
```

This allows reads to prefer secondaries, but the choice should be based on consistency requirements.

### **10.7 Read replicas vs high availability**

Do not confuse read scaling with high availability.

| Read replica                          | High availability configuration                |
| ------------------------------------- | ---------------------------------------------- |
| Primarily increases read capacity     | Primarily improves service continuity          |
| May be read-only                      | Supports failover according to platform design |
| Can have replication lag              | Focuses on recovery and availability           |
| Does not guarantee automatic failover | May include automatic failover                 |

For example, Amazon RDS distinguishes read replicas for read scaling from Multi-AZ deployments for high availability. Multi-AZ configurations can use synchronous standby replication and managed failover, depending on the deployment type.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.aws.amazon.com\&sz=32)

Amazon Relational Database Service



## **11. Database partitioning and sharding**

These concepts are often confused in interviews.

### **11.1 What is partitioning?**

Partitioning divides a large logical table or collection into smaller parts.

The database may still manage these partitions within one database system.

Example:

```
orders
  |
  ├── Partition 1: January
  ├── Partition 2: February
  ├── Partition 3: March
  └── Partition 4: April
```

A query that filters by date may access only relevant partitions when partition pruning is supported and the query is written appropriately.

### **11.2 Common partitioning strategies**

**1. Range partitioning**

Divide data by ranges.

```
Orders:
January  → Partition 1
February → Partition 2
March    → Partition 3
```

**2. List partitioning**

Divide data using predefined values.

```
Region:
India   → Partition 1
USA     → Partition 2
UK      → Partition 3
```

**3. Hash partitioning**

Use a hash function to distribute records.

```
hash(userId) % 4
```

The result selects one of four partitions.

### **11.3 What is sharding?**

Sharding distributes data across multiple independent database servers.

Each server stores a subset of the dataset.

```
                  Application
                       |
                  Query Router
                  /    |    \
                 /     |     \
                ↓      ↓      ↓
             Shard 1 Shard 2 Shard 3
             Users   Users   Users
             1–100k  100k–  200k–
                     200k   300k
```

### **11.4 Shard key**

A shard key determines how data is distributed across shards.

Example:

```
{
  userId: 501,
  name: "Example User"
}
```

If `userId` is the shard key, the database uses that value according to its sharding strategy to determine where the record belongs.

A good shard key should generally:

- Distribute writes and storage reasonably evenly.
- Avoid creating hot shards.
- Match common query patterns.
- Support the required scaling strategy.
- Avoid excessively concentrating new records on one shard.

### **11.5 Sharding problems**

Poor shard-key selection can create:

- Uneven data distribution.
- Hotspots.
- Cross-shard queries.
- Expensive distributed transactions.
- Operational complexity.
- Rebalancing overhead.

For example, a monotonically increasing timestamp as a shard key can concentrate new writes on a narrow range of shards in some range-based sharding designs.

### **11.6 Partitioning vs sharding**

| Feature        | Partitioning                           | Sharding                                       |
| -------------- | -------------------------------------- | ---------------------------------------------- |
| Meaning        | Dividing a dataset into logical parts  | Distributing data across database servers      |
| Infrastructure | Can be within one database instance    | Multiple database nodes                        |
| Main purpose   | Data organization and query efficiency | Distributed storage and workload               |
| Complexity     | Usually lower                          | Higher                                         |
| Example        | Monthly order partitions               | User data distributed across multiple clusters |

### **11.7 MongoDB sharding concept**

MongoDB sharded clusters use components such as:

- Shards.
- `mongos` query routers.
- Config servers.
- Shard keys.
- Balancing mechanisms.

Conceptually:

```
Application
     |
     ↓
mongos Router
  /    |    \
 ↓     ↓     ↓
Shard1 Shard2 Shard3
```

The application connects through `mongos`, which routes operations to the appropriate shard or shards.

Do not introduce sharding simply because a database is large. First evaluate indexing, query efficiency, vertical capacity, and read replicas.

## **12. Database connection pooling**

### **12.1 What is a database connection?**

A database connection is a communication channel established between an application and a database server.

For example:

```
Node.js Application
       |
       | Establish connection
       ↓
    MongoDB
```

Creating a database connection has costs:

- Network communication.
- Authentication.
- Connection negotiation.
- Resource allocation.
- Server-side connection management.

Creating a brand-new connection for every HTTP request is inefficient.

### **12.2 What is connection pooling?**

A **connection pool** maintains a reusable collection of database connections.

Instead of creating a new connection for every request, the application borrows an available connection from the pool and returns it when the operation completes.

**Node.js Application**

Multiple incoming requests

**Connection Pool**

C1

C2

C3

C4

Reusable connections

**Database Server**

### **12.3 Connection pooling example**

Suppose you configure a pool with a maximum of 10 connections.

```
Maximum pool connections = 10

Request 1 → Connection 1
Request 2 → Connection 2
Request 3 → Connection 3

Request 4 → Waits if no connection is available
```

When an operation finishes, its connection becomes available for another operation.

### **12.4 MySQL connection pool using mysql2**

Install:

```
npm install mysql2
```

Create `src/lib/db.ts`:

```
import mysql from "mysql2/promise";

export const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,

  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0,
});
```

Use the pool:

```
import { pool } from "@/lib/db";

export async function getUsers() {
  const [rows] = await pool.execute(
    "SELECT id, name, email FROM users LIMIT 20"
  );

  return rows;
}
```

The pool manages connection reuse.

### **12.5 MongoDB connection pooling with Mongoose**

Mongoose uses the MongoDB Node.js driver, which maintains connection pools.

Example:

```
import mongoose from "mongoose";

export async function connectDB() {
  if (mongoose.connection.readyState === 1) {
    return;
  }

  await mongoose.connect(process.env.MONGODB_URI!, {
    maxPoolSize: 10,
    minPoolSize: 2,
    serverSelectionTimeoutMS: 5000,
  });
}
```

The actual pool is maintained by the MongoDB driver.

For a serverless application, connection lifecycle and instance reuse need particular attention. Do not assume one process-wide pool is shared across all serverless instances.

### **12.6 Connection pool sizing**

Suppose:

```
Application Server 1: 20 max connections
Application Server 2: 20 max connections
Application Server 3: 20 max connections
Application Server 4: 20 max connections
```

Potential application connections:

\\[ 4 \times 20 = 80 \\]

If your database supports 100 connections, you must still reserve capacity for:

- Administrative connections.
- Monitoring.
- Other applications.
- Background workers.
- Failover and maintenance operations.

**Important:** Increasing pool size does not automatically improve performance. Too many concurrent database connections can overwhelm the database.

## **13. CDN and edge architecture**

### **13.1 What is a CDN?**

A **Content Delivery Network (CDN)** is a geographically distributed network of servers that delivers content closer to users.

Without a CDN:

```
User in India
      |
      ↓
Application server in USA
      |
      ↓
Static image
```

Every request may need to travel to the origin server.

With a CDN:

```
User in India
      |
      ↓
Nearby CDN Edge
      |
      ├── Cache HIT → Return cached content
      |
      └── Cache MISS → Fetch from origin
```

A CDN reduces origin traffic and can improve content delivery latency.

### **13.2 What can a CDN cache?**

Common examples:

- Images.
- CSS files.
- JavaScript bundles.
- Fonts.
- Videos.
- Public static files.
- Carefully configured public API responses.
- Public HTML pages.

Cloudflare's CDN, for example, caches eligible static resources by default, while dynamic content generally requires explicit cache configuration.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://developers.cloudflare.com\&sz=32)

Cloudflare Cache (CDN) docs+1



### **13.3 Cache HIT vs Cache MISS**

**Cache HIT**

The requested content is available at the edge.

```
User → CDN → Cached Image
```

The CDN can respond without contacting your origin for that request.

**Cache MISS**

The content is not available in the edge cache.

```
User → CDN → Origin Server
                   |
                   ↓
              Response
                   |
                   ↓
              CDN Cache
                   |
                   ↓
                 User
```

The CDN retrieves the content from the origin and may cache it according to its caching rules.

### **13.4 CDN vs Redis**

These technologies both use caching but serve different purposes.

| Feature      | CDN                               | Redis                                        |
| ------------ | --------------------------------- | -------------------------------------------- |
| Main purpose | Distribute content globally       | Fast application data access                 |
| Location     | Distributed edge locations        | Usually application-region infrastructure    |
| Common data  | Images, CSS, JS, public responses | Sessions, counters, frequently accessed data |
| Used by      | Browsers and HTTP clients         | Backend services                             |
| Example      | Cloudflare                        | Redis server                                 |

### **13.5 CDN architecture for a Next.js application**

Suppose your website is deployed in one region.

```
                    Users
                  /   |   \
                 /    |    \
                ↓     ↓     ↓
             CDN Edge Locations
                    |
                    ↓
              Origin / Hosting
                    |
              Next.js Application
                    |
                    ↓
               Database
```

Static assets can be served from CDN locations while dynamic application requests reach the origin.

### **13.6 Edge computing**

Edge computing moves selected computation closer to the user instead of processing everything in a central region.

Possible tasks include:

- Request routing.
- Redirects.
- Lightweight authentication-related checks.
- Geographic routing.
- Bot filtering.
- Simple request transformations.

In Next.js 16, `proxy.ts` can perform request interception and lightweight routing logic. It should not replace authoritative authorization checks in your data-access layer or backend operations.

**Important:** Edge runtime capabilities and Node.js runtime capabilities differ. Do not assume every Node.js package or database driver can run in an edge environment.

## **14. Asynchronous job processing**

### **14.1 What is synchronous processing?**

In synchronous request processing, the client waits while the server performs the operation and sends a response.

Example: uploading a large PDF and immediately extracting its content.

```
Client
  |
  | Upload PDF
  ↓
API Server
  |
  | Extract PDF
  | Analyze content
  | Save results
  |
  ↓
Response
```

If processing takes 30 seconds, the client may need to wait for 30 seconds.

This can cause:

- Slow API responses.
- HTTP timeouts.
- High application resource consumption.
- Poor user experience.

### **14.2 What is asynchronous processing?**

Asynchronous job processing separates the initial request from the actual work.

The API accepts the job, stores it in a queue, and returns a response. A background worker processes the job separately.

```
Client
  |
  | Submit job
  ↓
API Server
  |
  | Create job
  ↓
Message Queue
  |
  | Worker consumes job
  ↓
Background Worker
  |
  | Process PDF
  | Save result
  ↓
Database
```

The client does not need to wait for the entire operation.

### **14.3 Example: Resume parser**

Imagine your AI resume parser accepts 500 resumes.

Processing each resume involves:

1. Reading the uploaded file.
2. Extracting PDF text.
3. Sending the content to a local LLM or AI service.
4. Validating structured output.
5. Saving candidate information.
6. Generating an Excel report.

Performing all of this inside one HTTP request is risky.

A better workflow:

```
User uploads 500 resumes
          |
          ↓
     Upload API
          |
          ↓
      Job Queue
          |
     ┌────┼────┐
     ↓    ↓    ↓
  Worker Worker Worker
     |    |    |
     ↓    ↓    ↓
  Process resumes
          |
          ↓
       Database
          |
          ↓
   Job status updated
```

### **14.4 Return a job ID**

An API can respond immediately:

```
export async function POST() {
  const job = {
    id: crypto.randomUUID(),
    status: "queued",
  };

  // Persist and enqueue the actual job here.

  return Response.json(
    {
      message: "Job accepted",
      jobId: job.id,
      status: job.status,
    },
    { status: 202 }
  );
}
```

HTTP `202 Accepted` means the request has been accepted for processing, not that the work has completed.

The client can then query:

```
GET /api/jobs/job-123
```

Example response:

```
{
  "jobId": "job-123",
  "status": "processing",
  "progress": 45
}
```

Later:

```
{
  "jobId": "job-123",
  "status": "completed",
  "progress": 100
}
```

### **14.5 When should you use background jobs?**

Use background processing for:

- Sending emails.
- Generating PDFs.
- Processing images.
- Video transcoding.
- Resume parsing.
- Bulk imports and exports.
- Payment reconciliation.
- Scheduled reports.
- Large data synchronization.
- Notifications.

Microsoft's background-job guidance highlights long-running work, image processing, workflows, and remote service calls as common use cases.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **15. Background workers and queues**

### **15.1 What is a background worker?**

A background worker is a separate process or service that executes jobs outside the main request-response lifecycle.

Example:

```
                    Application
                         |
                  Job Submission
                         |
                         ↓
                      Queue
                         |
              ┌──────────┴──────────┐
              ↓                     ↓
         Worker 1               Worker 2
              |                     |
              ↓                     ↓
         Process Job A         Process Job B
```

You can scale the workers independently of your API servers.

### **15.2 What is a message queue?**

A message queue temporarily stores jobs or messages until consumers process them.

Common queue technologies:

- BullMQ
- RabbitMQ
- Apache Kafka
- Amazon SQS
- Azure Service Bus

### **15.3 BullMQ with Node.js and Redis**

BullMQ is a popular option for background job processing in Node.js applications.

Install:

```
npm install bullmq ioredis
```

Suggested structure:

```
src/
  queues/
    email.queue.ts
  workers/
    email.worker.ts
  services/
    email.service.ts
  jobs/
    email.types.ts
```

Create a queue:

```
// src/queues/email.queue.ts

import { Queue } from "bullmq";

export const emailQueue = new Queue("email", {
  connection: {
    host: process.env.REDIS_HOST,
    port: Number(process.env.REDIS_PORT),
  },
});
```

Add a job:

```
// src/services/email.service.ts

import { emailQueue } from "@/queues/email.queue";

export async function sendWelcomeEmail(
  email: string
) {
  await emailQueue.add("welcome-email", {
    email,
  });
}
```

Create a worker:

```
// src/workers/email.worker.ts

import { Worker } from "bullmq";

const worker = new Worker(
  "email",
  async (job) => {
    if (job.name === "welcome-email") {
      const { email } = job.data;

      console.log("Sending email to:", email);

      // Call your email provider here.
    }
  },
  {
    connection: {
      host: process.env.REDIS_HOST,
      port: Number(process.env.REDIS_PORT),
    },
  }
);

worker.on("completed", (job) => {
  console.log("Job completed:", job.id);
});

worker.on("failed", (job, error) => {
  console.error("Job failed:", job?.id, error);
});
```

Run the worker as a separate process:

```
npx tsx src/workers/email.worker.ts
```

In production, deploy the API and worker as separate services or processes.

### **15.4 Job lifecycle**

A job may move through these states:

```
Waiting
   |
   ↓
Active
   |
   ├── Completed
   |
   └── Failed
          |
          ↓
       Retried
          |
          ↓
       Completed / Failed
```

### **15.5 Important queue concepts**

**Retry:** Attempt a failed job again.

**Backoff:** Wait before retrying, often with an increasing delay.

**Dead-letter queue:** Store messages that cannot be successfully processed after the configured handling attempts.

**Idempotency:** Processing the same logical job multiple times should not create duplicate business effects.

**Concurrency:** Number of jobs a worker processes simultaneously.

**Priority:** Process higher-priority jobs before lower-priority ones, when supported by the queue.

**Delayed jobs:** Schedule a job to run later.

### **15.6 Queue-based load leveling**

Suppose your application receives 1,000 jobs per second, but your worker fleet can safely process 300 jobs per second.

The queue absorbs the temporary difference.

```
Incoming jobs: 1,000/sec
Worker capacity: 300/sec
Backlog increases: 700/sec
```

This is useful during short traffic spikes, but it cannot solve a permanently insufficient processing rate.

If the producer continuously creates more work than the workers complete, the queue will keep growing.

Monitor:

- Queue depth.
- Oldest message age.
- Job completion rate.
- Failure rate.
- Retry count.
- Processing duration.

Azure's queue-based load-leveling guidance explains how queues buffer demand spikes, while emphasizing idempotent consumers, durable messages, and controlled consumer scaling.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



**Critical production rule:** Never assume a queued job runs exactly once. Design for duplicate delivery, retries, worker crashes, and graceful shutdown.

## **16. Distributed rate limiting**

### **16.1 What is rate limiting?**

Rate limiting restricts how many requests a client can make within a defined period.

For example:

```
Maximum requests: 100
Time window: 1 minute
```

A client can make up to 100 requests per minute. Requests beyond the configured limit may receive:

```
HTTP/1.1 429 Too Many Requests
```

Rate limiting helps protect:

- APIs from excessive traffic.
- Login endpoints from brute-force attempts.
- Databases from excessive query volume.
- External APIs from unnecessary usage.
- Infrastructure from accidental or malicious request floods.

### **16.2 Why is distributed rate limiting necessary?**

Imagine you run three Node.js servers.

Each server has its own in-memory rate limiter.

```
Server 1: Allows 100 requests
Server 2: Allows 100 requests
Server 3: Allows 100 requests
```

A user can send requests to all three servers.

The user may effectively get 300 requests instead of the intended 100.

The problem is that each server maintains a separate counter.

### **16.3 Solution: Shared Redis rate limiter**

Store rate-limit counters in a shared Redis instance.

```
                 Users
                   |
              Load Balancer
              /     |     \
             ↓      ↓      ↓
          Server 1 Server 2 Server 3
              \      |      /
               \     |     /
                ↓    ↓    ↓
                   Redis
               Shared counters
```

All application instances use the same counter.

### **16.4 Fixed-window algorithm**

Suppose the limit is 100 requests per minute.

Redis stores:

```
rate-limit:user-501:2026-11-25-17:00 = 72
```

The counter increases with every request.

When the counter exceeds 100, reject the request.

A simplified Redis Lua script:

```
local current = redis.call("INCR", KEYS[1])

if current == 1 then
  redis.call("EXPIRE", KEYS[1], ARGV[1])
end

return current
```

Call this script atomically through Redis.

The application then checks whether the returned counter exceeds the configured limit.

### **16.5 Rate-limiting algorithms**

| Algorithm              | How it works                                       | Characteristics                      |
| ---------------------- | -------------------------------------------------- | ------------------------------------ |
| Fixed Window           | Counts requests in fixed time windows              | Simple, but boundary bursts possible |
| Sliding Window Log     | Records request timestamps                         | Accurate, but storage-intensive      |
| Sliding Window Counter | Estimates a rolling window using adjacent counters | Lower storage than a full log        |
| Token Bucket           | Tokens refill over time; requests consume tokens   | Allows controlled bursts             |
| Leaky Bucket           | Processes requests at a controlled rate            | Smooths traffic                      |

### **16.6 Token bucket example**

Configuration:

```
Bucket capacity: 10 tokens
Refill rate: 2 tokens/second
Request cost: 1 token
```

A client can initially make 10 requests immediately.

Then the bucket refills at two tokens per second.

This allows short bursts while limiting sustained traffic.

### **16.7 Rate-limiting by user or endpoint**

Different endpoints may need different limits.

| Endpoint         | Example policy                        |
| ---------------- | ------------------------------------- |
| Login            | Strict limit per account and IP       |
| Password reset   | Strict limit                          |
| Product listing  | Higher limit                          |
| Payment creation | Strict limit plus idempotency         |
| File upload      | Limit request frequency and file size |
| Public search    | Moderate limit                        |

**Important:** Distributed rate limiting should use atomic operations and trusted identity information. Avoid relying only on an untrusted client-supplied IP header.

## **17. Fault tolerance and high availability**

### **17.1 What is fault tolerance?**

Fault tolerance is the ability of a system to continue providing its required functionality when one or more components fail.

Imagine an e-commerce application:

- One Node.js server crashes.
- One database replica becomes unavailable.
- A payment provider times out.
- Redis becomes temporarily unreachable.

A fault-tolerant architecture should handle these failures without causing an uncontrolled failure across the entire application.

### **17.2 What is high availability?**

High availability means designing a system to remain accessible for a high proportion of the required operating time.

Availability is commonly represented as a percentage.

| Availability | Approximate annual downtime  |
| ------------ | ---------------------------- |
| 99%          | 3 days, 15 hours, 36 minutes |
| 99.9%        | 8 hours, 46 minutes          |
| 99.95%       | 4 hours, 23 minutes          |
| 99.99%       | 52 minutes, 34 seconds       |
| 99.999%      | 5 minutes, 15 seconds        |

These are theoretical annual downtime equivalents, not guarantees of actual recovery time.

### **17.3 Single point of failure (SPOF)**

A Single Point of Failure is a component whose failure can make the entire system unavailable.

Example:

```
Users
  |
  ↓
One Application Server
  |
  ↓
One Database Server
```

If the application server fails, users cannot access the application.

If the database server fails, the application may be unable to serve data-dependent requests.

A more resilient architecture introduces redundancy:

```
                  Users
                    |
               Load Balancer
                /       \
               ↓         ↓
           Server 1   Server 2
                \       /
                 \     /
                 Database
                    |
               Replication
                    |
                 Standby
```

This diagram illustrates redundancy, not a complete production failover design. The load balancer, networking, DNS, storage, and other dependencies must also be considered.

### **17.4 Redundancy**

Redundancy means maintaining additional resources that can take over when primary resources fail.

Examples:

- Multiple application instances.
- Multiple availability zones.
- Database replicas.
- Redundant network paths.
- Multiple queue consumers.
- Backup storage.

### **17.5 Health checks and automatic recovery**

A production platform should detect unhealthy instances.

For example:

```
Load Balancer
     |
     ├── Server 1 → Healthy
     |
     ├── Server 2 → Unhealthy
     |
     └── Server 3 → Healthy
```

The load balancer stops routing eligible traffic to Server 2.

The hosting platform may restart or replace the unhealthy instance.

### **17.6 Timeout**

A timeout prevents an operation from waiting indefinitely.

Example:

```
const controller = new AbortController();

const timeout = setTimeout(() => {
  controller.abort();
}, 5000);

try {
  const response = await fetch(
    "https://api.example.com/products",
    {
      signal: controller.signal,
    }
  );

  if (!response.ok) {
    throw new Error("Upstream request failed");
  }

  return await response.json();
} finally {
  clearTimeout(timeout);
}
```

The request is aborted after five seconds.

### **17.7 Retry with exponential backoff**

A temporary failure may recover if the operation is retried.

A simple exponential backoff strategy:

\\[ Delay = InitialDelay \times 2^{Attempt} \\]

Example:

```
Attempt 1: Wait 1 second
Attempt 2: Wait 2 seconds
Attempt 3: Wait 4 seconds
Attempt 4: Wait 8 seconds
```

In production, add jitter to reduce the chance that many clients retry simultaneously.

Do not retry every error.

For example:

- A network timeout may be retryable.
- HTTP 503 may be retryable under an appropriate policy.
- HTTP 400 usually is not.
- A payment request should use an idempotency key before retrying.

### **17.8 Circuit breaker pattern**

A circuit breaker temporarily stops requests to a dependency that is repeatedly failing.

It has three common states:

**Closed**

Normal

Requests flow to the dependency. Failures are monitored.

Failure threshold reached

**Open**

Blocked

Requests are rejected or handled through a fallback without calling the failing dependency.

Recovery timeout expires

**Half-open**

Testing

A limited number of test requests are allowed. Success can close the circuit; failure can reopen it.

Example:

Your application calls a payment gateway.

If the gateway repeatedly fails, the circuit breaker stops continuously sending requests to it.

This protects your application from wasting resources on a failing dependency.

### **17.9 Bulkhead pattern**

The bulkhead pattern isolates resources so that failure or overload in one component does not consume all resources used by other components.

Example:

```
Node.js Application
       |
       ├── Payment Connection Pool
       |
       ├── Email Connection Pool
       |
       └── Reporting Worker Pool
```

If the reporting system becomes overloaded, it should not consume every resource required by payment processing.

### **17.10 Graceful degradation**

Graceful degradation means reducing non-essential functionality when a dependency fails while keeping essential functionality available.

Example:

If product recommendations are unavailable:

- Continue displaying products.
- Hide the recommendation section.
- Keep checkout working.

This is preferable to returning an error for the entire product page.

### **17.11 Recovery objectives**

Two important disaster-recovery terms:

**RTO — Recovery Time Objective**

How quickly must the service be restored after a failure?

Example: RTO = 30 minutes.

**RPO — Recovery Point Objective**

How much data loss, measured in time, can the business tolerate?

Example: RPO = 5 minutes means the recovery plan should limit potential data loss to approximately five minutes of recent changes.

### **17.12 Reliability patterns summary**

| Pattern              | Purpose                                |
| -------------------- | -------------------------------------- |
| Redundancy           | Avoid dependence on one component      |
| Health checks        | Detect unhealthy instances             |
| Timeout              | Prevent indefinite waiting             |
| Retry                | Recover from temporary failures        |
| Circuit breaker      | Stop calls to failing dependencies     |
| Bulkhead             | Isolate resource pools                 |
| Graceful degradation | Keep essential functionality available |
| Backup and recovery  | Restore data and service after failure |

## **18. Capacity planning**

### **18.1 What is capacity planning?**

Capacity planning estimates the infrastructure resources required to support an expected workload.

It helps answer questions such as:

- How many application instances are required?
- How much CPU and RAM do we need?
- How many database connections are required?
- How many requests can one server handle?
- How many background workers are needed?
- How much storage will be required next year?

### **18.2 Understand the workload first**

Suppose you are building an e-commerce platform.

Assume:

```
Daily active users: 100,000
Average requests per user: 20
Peak multiplier: 3
```

Daily requests:

\\[ 100,000 \times 20 = 2,000,000 \\]

Average requests per second:

\\[ \frac{2,000,000}{86,400} \approx 23.15 \\]

Peak requests per second:

\\[ 23.15 \times 3 \approx 69.45 \\]

Your application should be tested against a workload of approximately 70 requests per second under these assumptions.

These numbers are illustrative, not production sizing guarantees.

### **18.3 Estimate application instances**

Suppose load testing shows that one application instance can safely handle 50 requests per second while meeting your latency target.

Your estimated peak demand is 70 requests per second.

\\[ Instances = \frac{PeakRPS}{SafeRPSPerInstance} \\]

\\[ Instances = \frac{70}{50} = 1.4 \\]

You would need at least two instances for the estimated traffic, before adding any desired redundancy or extra headroom.

For production planning, test:

- Typical workload.
- Peak workload.
- Sudden traffic spikes.
- Failure of one instance.
- Slow database conditions.

### **18.4 Little's Law**

Little's Law is useful for understanding concurrent work in a stable system.

\\[ L = \lambda \times W \\]

Where:

- \\(L\\) = average number of items in the system.
- \\(\lambda\\) = average arrival rate.
- \\(W\\) = average time spent in the system.

Example:

```
Arrival rate: 100 requests/second
Average response time: 0.2 seconds
```

\\[ L = 100 \times 0.2 = 20 \\]

The system has approximately 20 requests in progress on average under those assumptions.

This is useful for thinking about concurrency, connection usage, and queue sizes.

### **18.5 Database connection planning**

Suppose:

```
API instances: 5
Maximum pool size per instance: 15
Worker instances: 3
Maximum pool size per worker: 5
```

Total potential connections:

\\[ (5 \times 15) + (3 \times 5) = 90 \\]

Your database must be able to accommodate this workload alongside other consumers and operational connections.

### **18.6 Storage capacity planning**

Suppose your application stores:

```
New orders per day: 10,000
Average order record size: 2 KB
```

Daily raw storage:

\\[ 10,000 \times 2KB = 20MB \\]

Annual raw storage:

\\[ 20MB \times 365 = 7,300MB \\]

That is approximately 7.3 GB per year, before indexes, backups, replication, and storage overhead.

### **18.7 Load testing**

Load testing helps determine how the system behaves under realistic traffic.

Common tools:

- k6
- Apache JMeter
- Artillery
- Locust

Example using k6:

```
import http from "k6/http";
import { check, sleep } from "k6";

export const options = {
  vus: 50,
  duration: "1m",
};

export default function () {
  const response = http.get(
    "https://example.com/api/products"
  );

  check(response, {
    "status is 200": (r) => r.status === 200,
  });

  sleep(1);
}
```

This creates a basic test with 50 virtual users over one minute. It is a starting example, not a complete performance test.

### **18.8 Types of performance tests**

| Test                | Purpose                                            |
| ------------------- | -------------------------------------------------- |
| Load testing        | Test expected workload                             |
| Stress testing      | Find system limits                                 |
| Spike testing       | Test sudden traffic increases                      |
| Soak testing        | Test sustained workload over time                  |
| Scalability testing | Observe behavior as resources or workload increase |
| Volume testing      | Test large data volumes                            |

## **19. System design fundamentals**

### **19.1 What is system design?**

System design is the process of defining the architecture, components, data flow, interfaces, and infrastructure required to build a software system.

It answers questions such as:

- What components does the application need?
- How will users communicate with the backend?
- Where will data be stored?
- How will services communicate?
- How will the system scale?
- What happens when a component fails?
- How will security and performance be maintained?

### **19.2 Functional vs non-functional requirements**

Before designing a system, understand its requirements.

**Functional requirements** describe what the system must do.

For an e-commerce application:

- Users can register and log in.
- Users can browse products.
- Users can add products to carts.
- Users can place orders.
- Users can make payments.
- Admins can manage products.

**Non-functional requirements** describe how well the system must operate.

Examples:

- Response time should remain acceptable.
- The application should support 100,000 daily active users.
- Payment operations must be reliable.
- User data must be protected.
- The application should remain available during an instance failure.

| Functional requirement   | Non-functional requirement                 |
| ------------------------ | ------------------------------------------ |
| User can log in          | Login response should be fast              |
| User can place an order  | Orders must not be duplicated              |
| User can upload images   | Uploads must be secure and reliable        |
| Admin can view reports   | Reports should support large datasets      |
| User can search products | Search should remain responsive under load |

### **19.3 Important system design components**

| Component          | Responsibility                            |
| ------------------ | ----------------------------------------- |
| Client             | Browser, mobile app, or external consumer |
| DNS                | Resolves domain names                     |
| CDN                | Delivers cached content closer to users   |
| Load balancer      | Distributes incoming traffic              |
| Reverse proxy      | Routes and forwards requests              |
| API Gateway        | Central entry point for backend APIs      |
| Application server | Executes business logic                   |
| Cache              | Stores frequently accessed data           |
| Database           | Stores persistent application data        |
| Message broker     | Transfers messages asynchronously         |
| Worker             | Processes background jobs                 |
| Object storage     | Stores files and media                    |
| Monitoring         | Tracks system health and performance      |

### **19.4 Horizontal scalability and stateless design**

A scalable architecture should avoid depending on one particular application instance.

For example:

```
                  Users
                    |
                    ↓
                   CDN
                    |
                    ↓
              Load Balancer
               /     |     \
              ↓      ↓      ↓
            API 1  API 2  API 3
               \     |     /
                \    |    /
                  Redis
                    |
                    ↓
                 Database
```

All API instances share external infrastructure where shared state is required.

### **19.5 API design principles**

A scalable API should consider:

- Consistent URL conventions.
- Proper HTTP methods and status codes.
- Pagination.
- Filtering and sorting.
- Request validation.
- Authentication and authorization.
- Rate limiting.
- Idempotency for retryable operations.
- API versioning.
- Timeouts and error handling.

Example:

```
GET /api/v1/products?page=1&limit=20
```

This is generally preferable to returning an unbounded product collection.

### **19.6 Database design principles**

A scalable database design should consider:

- Appropriate indexes.
- Query patterns.
- Data ownership.
- Data consistency.
- Transaction boundaries.
- Connection limits.
- Replication.
- Backup and recovery.
- Partitioning and sharding when justified.

### **19.7 Observability**

Observability helps you understand what is happening inside a running system.

Three important pillars:

**Logs:** What happened?

Example:

```
2026-11-25 17:00:00
Order creation failed
orderId=ORD-501
requestId=req-123
```

**Metrics:** How is the system performing?

Examples:

- Request rate.
- Error rate.
- CPU utilization.
- Memory usage.
- p95 latency.
- Queue depth.
- Database connections.

**Distributed traces:** How did one request travel through multiple services?

Example:

```
API Gateway: 10 ms
Order Service: 40 ms
Payment Service: 800 ms
Database: 30 ms
```

This helps identify the slow component.

### **19.8 System design trade-offs**

There is rarely one universally best architecture.

For example:

| Decision        | Benefit                 | Trade-off                            |
| --------------- | ----------------------- | ------------------------------------ |
| Redis cache     | Faster reads            | Cache invalidation complexity        |
| Read replicas   | More read capacity      | Replication lag                      |
| Microservices   | Independent deployment  | Distributed-system complexity        |
| Synchronous API | Immediate response      | Request can wait on dependencies     |
| Background jobs | Faster initial response | Eventual completion and job tracking |
| Sharding        | Distributed storage     | More operational complexity          |

Good system design means selecting trade-offs based on actual requirements.

## **20. Designing a real-world scalable e-commerce application**

Now let's combine the concepts into one architecture.

### **20.1 Requirements**

Assume we are designing an e-commerce application with:

- 100,000 daily active users.
- Product browsing and search.
- User registration and authentication.
- Shopping cart.
- Checkout and payments.
- Order management.
- Product image uploads.
- Email notifications.
- Admin dashboard.

### **20.2 High-level architecture**

**Web / Mobile Clients**

Next.js · React · Mobile app

**CDN + WAF + Load Balancer**

Content delivery · Traffic distribution · Protection

**API Gateway / Reverse Proxy**

Routing · Authentication checks · Rate limiting

User Service

Product Service

Order Service

Payment Service

Cart Service

Notification Service

**Redis**

Cache · Sessions · Rate limits

**Databases**

Persistent business data

**Message Broker**

Asynchronous events

**Object Storage**

Images · Documents

**Background Workers**

Email · Reports · Order workflows · Image processing

This is a logical architecture. A real implementation may combine several services into one modular backend initially.

### **20.3 Product browsing workflow**

When a user requests:

```
GET /api/v1/products
```

The request follows this flow:

1. Client sends the request.
2. CDN checks whether an eligible public response is cached.
3. If necessary, the load balancer forwards the request.
4. API Gateway routes it to the Product Service.
5. Product Service checks Redis.
6. If cached data exists, it returns the result.
7. Otherwise, it queries the database.
8. It stores eligible results in Redis.
9. The response is returned to the client.

### **20.4 Order creation workflow**

When a user places an order:

```
Client
  |
  ↓
Order API
  |
  ↓
Validate request and authorization
  |
  ↓
Create order with idempotency protection
  |
  ↓
Database transaction
  |
  ↓
Persist order and outbox event
  |
  ↓
Message Broker
  |
  ├── Payment Workflow
  ├── Inventory Workflow
  └── Notification Workflow
```

This architecture may use the Saga pattern for multi-service business operations.

Do not assume that a message being published means every downstream operation has completed.

Track the order state and handle failures explicitly.

### **20.5 Database architecture**

A possible initial design:

| Data           | Storage                                       |
| -------------- | --------------------------------------------- |
| Users          | MongoDB or MySQL                              |
| Products       | MongoDB or MySQL                              |
| Orders         | MySQL                                         |
| Payments       | MySQL with transaction and audit requirements |
| Sessions       | Redis or a database                           |
| Product images | S3-compatible object storage                  |
| Search index   | Dedicated search engine when required         |

For a modular monolith, a single well-designed relational database can be a very good starting point. A database-per-service model is an architectural choice, not a mandatory requirement for every application.

### **20.6 Scaling the architecture**

As traffic increases:

**Application layer**

- Add more API instances.
- Use a load balancer.
- Keep application instances stateless.
- Scale workers separately.

**Database layer**

- Optimize indexes and queries.
- Increase database resources.
- Add read replicas where useful.
- Consider partitioning or sharding only when justified.

**Caching layer**

- Cache frequently accessed public data.
- Use appropriate TTLs.
- Invalidate or update cache entries after relevant mutations.

**Background processing**

- Scale workers according to queue depth and processing capacity.
- Set concurrency limits.
- Use retries and dead-letter handling.
- Protect downstream services.

**Infrastructure**

- Deploy across appropriate failure domains.
- Monitor latency, errors, queue age, and resource usage.
- Automate recovery and backups.

### **20.7 Recommended project structure**

For a Next.js 16 App Router frontend and a separate Node.js backend:

```
ecommerce-platform/
│
├── apps/
│   ├── web/
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── components/
│   │   │   ├── features/
│   │   │   └── lib/
│   │   └── package.json
│   │
│   └── api/
│       ├── src/
│       │   ├── modules/
│       │   │   ├── users/
│       │   │   ├── products/
│       │   │   ├── orders/
│       │   │   ├── payments/
│       │   │   └── notifications/
│       │   │
│       │   ├── queues/
│       │   ├── workers/
│       │   ├── middleware/
│       │   ├── infrastructure/
│       │   └── server.ts
│       │
│       └── package.json
│
├── packages/
│   ├── shared-types/
│   └── validation/
│
├── docker-compose.yml
└── README.md
```

### **20.8 Deployment architecture**

A possible deployment:

| Component        | Example hosting                             |
| ---------------- | ------------------------------------------- |
| Next.js frontend | Vercel                                      |
| Node.js API      | AWS EC2, ECS, or another container platform |
| Database         | MongoDB Atlas / Amazon RDS                  |
| Redis            | Managed Redis                               |
| Message broker   | RabbitMQ / SQS                              |
| Images           | Amazon S3 / Cloudinary                      |
| CDN              | Cloudflare                                  |
| Monitoring       | OpenTelemetry-compatible platform           |

Choose services based on cost, operational requirements, traffic, and team capability.

## **21. Common mistakes in scalable backend architecture**

1. **Scaling application servers without examining database bottlenecks.** More API instances can increase database pressure.
2. **Storing sessions only in local memory.** Requests routed to other instances may fail authentication.
3. **Creating unlimited database connections.** Connection exhaustion can make the database unavailable.
4. **Using read replicas for every read.** Read-after-write requirements may need primary reads.
5. **Sharding too early.** It adds operational and query complexity.
6. **Adding indexes without measuring query plans.** Excessive indexes increase write and storage costs.
7. **Serving all static files directly from application servers.** Use suitable object storage and CDN delivery.
8. **Performing long-running work inside HTTP requests.** Use background jobs when the client does not need to wait.
9. **Assuming queue messages execute exactly once.** Duplicate processing must be handled.
10. **Scaling workers without protecting the database.** More consumers can overwhelm downstream systems.
11. **Retrying requests indefinitely.** Retries can amplify outages and cause duplicate operations.
12. **Not using timeouts.** Requests may consume resources while waiting on unavailable dependencies.
13. **Ignoring queue age.** A queue can be processing jobs while still falling behind.
14. **Treating a health endpoint as proof of complete system health.** Liveness, readiness, and dependency health are different checks.
15. **Confusing replication with backup.** Replication can copy accidental deletions or corrupted data; maintain independent backups.
16. **Assuming a load balancer guarantees high availability.** It can itself become a failure point unless appropriately deployed.
17. **Ignoring observability.** Without metrics, logs, and traces, identifying bottlenecks becomes guesswork.
18. **Using microservices without a business or operational reason.** A modular monolith is often simpler and more efficient initially.

## **22. Interview questions and answers**

### **Basic level**

**1. What is scalability?**

Scalability is the ability of a system to handle increasing workload by adding resources while maintaining acceptable performance and reliability.

**2. What is the difference between vertical and horizontal scaling?**

Vertical scaling increases the resources of an existing machine. Horizontal scaling adds more machines or instances to distribute the workload.

**3. What is a load balancer?**

A load balancer distributes incoming traffic across multiple backend servers to improve resource utilization, scalability, and availability.

**4. What is a reverse proxy?**

A reverse proxy receives client requests and forwards them to backend servers. It can provide routing, TLS termination, caching, and load balancing.

**5. What is a stateless application?**

A stateless application does not depend on local server memory to maintain client state between requests. Shared external storage or request credentials can provide the necessary state.

**6. What is a CDN?**

A CDN is a geographically distributed network that delivers cached content closer to users, reducing origin traffic and improving content delivery.

**7. What is connection pooling?**

Connection pooling reuses existing database connections instead of creating a new connection for every request.

### **Intermediate level**

**8. What is database replication?**

Replication maintains copies of database data across multiple database servers.

**9. What is replication lag?**

Replication lag is the delay between a change being committed on the primary database and becoming available on a replica.

**10. What is database sharding?**

Sharding distributes a dataset across multiple database servers using a shard key.

**11. What is database partitioning?**

Partitioning divides a large logical table or dataset into smaller parts based on a defined partitioning strategy.

**12. What is the difference between partitioning and sharding?**

Partitioning divides data into logical segments, potentially within one database instance. Sharding distributes data across multiple database servers.

**13. What is asynchronous job processing?**

It moves long-running operations out of the HTTP request-response lifecycle and processes them through background workers.

**14. Why do we use Redis for distributed rate limiting?**

Redis provides shared counters that multiple application instances can access, allowing rate limits to be enforced consistently across instances.

**15. What is a queue-based load-leveling pattern?**

It places a message queue between producers and consumers to buffer workload spikes and let consumers process work at a controlled rate.

### **Advanced level**

**16. What is the difference between synchronous and asynchronous replication?**

Synchronous replication waits for acknowledgements according to the configured replication policy. Asynchronous replication allows the primary to commit without waiting for replica acknowledgement, which can result in replication lag.

**17. What is a hot shard?**

A hot shard is a shard receiving a disproportionate amount of workload, causing uneven resource utilization and potentially limiting overall system performance.

**18. What is a circuit breaker?**

A circuit breaker temporarily prevents requests from reaching a repeatedly failing dependency, protecting the application from cascading failures.

**19. What is the bulkhead pattern?**

The bulkhead pattern isolates resources so that failures or overload in one component do not exhaust resources needed by other components.

**20. What is graceful degradation?**

Graceful degradation means keeping essential functionality available while temporarily reducing non-essential functionality during failures.

**21. What is Little's Law?**

Little's Law describes the relationship between average work in a system, arrival rate, and average time spent in the system:

\\[ L = \lambda W \\]

**22. What is the difference between RTO and RPO?**

- RTO: Maximum acceptable time to restore service.
- RPO: Maximum acceptable data loss measured in time.

**23. Why should background workers be idempotent?**

Because jobs can be delivered multiple times or retried after failures. Idempotency prevents duplicate business effects.

**24. Why is horizontal scaling difficult for stateful applications?**

Because individual instances may maintain different local state. Requests routed to another instance may not have access to the original state.

**25. Why is sharding not the first database optimization?**

Because indexing, query optimization, caching, and vertical scaling are generally simpler. Sharding introduces additional routing, consistency, operational, and cross-shard query complexity.

## **23. Practical assignment**



Recommended project

**Build a scalable e-commerce backend**



Use the following technologies:

- Next.js 16.x with App Router and TypeScript.
- Node.js and Express.
- MongoDB or MySQL.
- Redis.
- BullMQ.
- Docker.
- NGINX.

### **Assignment tasks**

**Part 1 – Application architecture**

- Build a REST API with separate user, product, and order modules.
- Keep business logic separate from controllers.
- Add request validation and authentication.
- Implement pagination, filtering, and sorting.

**Part 2 – Database optimization**

- Create indexes for frequently queried fields.
- Inspect query plans using `EXPLAIN` or MongoDB `explain()`.
- Implement database connection pooling.
- Measure query execution time.

**Part 3 – Caching**

- Connect Redis.
- Implement cache-aside for product listings.
- Configure TTL.
- Invalidate relevant cache entries after product updates.

**Part 4 – Background processing**

- Configure BullMQ.
- Implement an email notification queue.
- Create a separate worker process.
- Implement retry and failure handling.
- Make job processing idempotent.

**Part 5 – Scalability**

- Run multiple API instances.
- Configure NGINX as a reverse proxy.
- Distribute traffic across the instances.
- Store shared sessions in Redis.
- Configure distributed rate limiting.

**Part 6 – Reliability**

- Add health-check endpoints.
- Configure request timeouts.
- Implement retry policies where safe.
- Add a circuit breaker for an external dependency.
- Log failures with request IDs.

**Part 7 – Performance testing**

- Test the API with k6.
- Test 50 concurrent virtual users.
- Record average, p95, and p99 latency.
- Monitor CPU, memory, and database connections.
- Identify and fix at least one bottleneck.

## **24. Quick revision notes**

| Topic                     | Remember                                       |
| ------------------------- | ---------------------------------------------- |
| Scalability               | Ability to handle increasing workload          |
| Vertical scaling          | Add resources to one machine                   |
| Horizontal scaling        | Add more machines                              |
| Load balancer             | Distributes incoming traffic                   |
| Reverse proxy             | Forwards requests to backend servers           |
| Stateless application     | Does not depend on local session state         |
| Database index            | Helps locate records efficiently               |
| Replication               | Maintains copies of database data              |
| Read replica              | Handles eligible read traffic                  |
| Replication lag           | Delay between primary and replica              |
| Partitioning              | Divides a dataset into logical parts           |
| Sharding                  | Distributes data across servers                |
| Connection pool           | Reuses database connections                    |
| CDN                       | Delivers cached content from distributed edges |
| Background worker         | Processes jobs outside the request lifecycle   |
| Message queue             | Buffers work for consumers                     |
| Distributed rate limiting | Uses shared state across instances             |
| Circuit breaker           | Stops calls to failing dependencies            |
| Bulkhead                  | Isolates resources                             |
| High availability         | Keeps a service accessible                     |
| Capacity planning         | Estimates required resources                   |
| RTO                       | Recovery Time Objective                        |
| RPO                       | Recovery Point Objective                       |
| Little's Law              | \\(L = \lambda W\\)                            |

## **25. Phase completion checklist**

**Your learning progress**

# **0%**

0 of 19 topics completed

Understand scalability, performance, latency, and throughput

Understand vertical and horizontal scaling

Understand Layer 4 and Layer 7 load balancing

Configure and understand NGINX reverse proxy

Understand stateless and stateful architecture

Understand database scaling strategies

Create and analyze database indexes

Understand replication and read replicas

Understand partitioning and sharding

Implement database connection pooling

Understand CDN and edge architecture

Understand asynchronous job processing

Implement a background worker and queue

Understand distributed rate limiting

Understand fault tolerance and high availability

Understand timeout, retry, circuit breaker, and bulkhead patterns

Calculate basic capacity requirements

Understand system design requirements and trade-offs

Design a scalable e-commerce architecture

Reset checklist

## **26. Official documentation and further reading**

1. [Azure – Queue-Based Load Leveling Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling?utm_source=chatgpt.com) — queue architecture and workload management.
2. [Azure – Best Practices for Background Jobs](https://learn.microsoft.com/en-us/azure/architecture/best-practices/background-jobs?utm_source=chatgpt.com) — worker design, retries, idempotency, and scaling.
3. [AWS – Scaling and High Availability in Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/gettingstartedguide/scaling-ha.html?utm_source=chatgpt.com) — read replicas and Multi-AZ.
4. [PostgreSQL – EXPLAIN](https://www.postgresql.org/docs/current/sql-explain.html?utm_source=chatgpt.com) — query execution plans.
5. [Cloudflare – Getting Started with Cache](https://developers.cloudflare.com/cache/get-started/?utm_source=chatgpt.com) — CDN and edge caching.

## **Next phase: Phase 36 – Advanced Observability, Monitoring, Logging and Production Operations**

The next phase will cover:

1. Logging Architecture and Structured Logging
2. Centralized Logging
3. Log Levels and Correlation IDs
4. Metrics and Monitoring
5. Prometheus and Grafana
6. Distributed Tracing
7. OpenTelemetry
8. Application Performance Monitoring (APM)
9. Error Tracking
10. Alerting and Incident Management
11. Production Debugging
12. Health Checks and Service Monitoring
13. SLIs, SLOs and SLAs
14. Error Budgets
15. Production Readiness and Operational Excellence

**Phase 35 complete.** When you're ready, say **“OK, Phase 36”** to continue.