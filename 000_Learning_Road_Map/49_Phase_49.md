# Phase 49 – System Design, Software Architecture and Scalable Application Design

**Level:** Advanced  
**Domain:** Software Architecture, System Design, Distributed Systems, Backend Engineering  
**Primary Technologies:** Next.js, Node.js, Express, MySQL, MongoDB, Redis, Kafka, RabbitMQ, Docker, Kubernetes, AWS

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand System Design and Software Architecture.
2. Differentiate High-Level Design (HLD) and Low-Level Design (LLD).
3. Understand functional and non-functional requirements.
4. Design scalable, reliable and maintainable applications.
5. Understand latency, throughput and performance.
6. Differentiate vertical and horizontal scaling.
7. Understand monolithic, modular monolith and microservices architectures.
8. Understand Service-Oriented Architecture (SOA).
9. Design client-server architectures.
10. Understand load balancers, reverse proxies and API gateways.
11. Design scalable database architectures.
12. Understand database replication and sharding.
13. Implement caching strategies using Redis.
14. Understand message queues and event-driven architecture.
15. Differentiate Kafka and RabbitMQ.
16. Understand Content Delivery Networks (CDNs).
17. Understand distributed systems.
18. Understand the CAP theorem and consistency models.
19. Implement fault-tolerance patterns.
20. Understand circuit breakers, retries, timeouts and idempotency.
21. Understand distributed transactions.
22. Implement observability using logs, metrics and traces.
23. Design scalable full-stack applications.
24. Solve real-world system design problems.

---

# PART 1: INTRODUCTION TO SYSTEM DESIGN

# 2. What Is System Design?

System Design is the process of defining the architecture, components, databases, APIs, communication mechanisms and infrastructure required to build a software system.

In simple terms:

**System Design explains how different parts of an application work together to solve a particular problem.**

Suppose you want to build an expense tracker.

You need to consider:

- How will users register and log in?
- Where will user information be stored?
- How will expenses be created?
- How will users retrieve their expenses?
- How will the application handle thousands of users?
- How will the application remain available if a server fails?
- How will you protect sensitive information?
- How will you scale the database?

These are system design questions.

### Example: Expense Tracker Architecture

```text
                 Users
                   |
                   v
              Next.js Frontend
                   |
                   v
               Backend API
                   |
          +--------+--------+
          |        |        |
          v        v        v
       Auth    Expenses   Reports
          |        |        |
          +--------+--------+
                   |
                   v
                Database
                   |
                   v
                  MySQL
```

This is a basic system design.

As the application grows, you may introduce:

- Load balancing.
- Redis caching.
- Database replication.
- Message queues.
- Multiple application servers.
- Monitoring.
- CDN.
- Background workers.

System design helps you decide when and why these components are necessary.

---

# 3. Why Is System Design Important?

Consider two applications.

### Application A

```text
Frontend
   |
   v
Backend
   |
   v
Database
```

It works well for 100 users.

### Application B

```text
                  Users
                    |
                    v
                   CDN
                    |
                    v
               Load Balancer
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Server 1  Server 2  Server 3
          |         |         |
          +---------+---------+
                    |
             +------+------+
             |             |
             v             v
           Redis        Database
                           |
                    +------+------+
                    |             |
                    v             v
                Primary       Replicas
```

Application B is designed to handle higher traffic and provide additional resilience.

However, more components also introduce operational complexity.

The objective of system design is not to build the most complicated architecture.

It is to build the **simplest architecture that satisfies the system's requirements**.

### Benefits

| Benefit | Explanation |
|---|---|
| Scalability | Handles increasing traffic |
| Reliability | Reduces the impact of failures |
| Maintainability | Makes the system easier to modify |
| Performance | Improves response time |
| Availability | Keeps the application accessible |
| Security | Helps define appropriate security boundaries |
| Cost Optimization | Avoids unnecessary infrastructure |
| Extensibility | Makes future improvements easier |

---

# 4. What Is High-Level Design (HLD)?

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
                Client
                  |
                  v
             API Gateway
                  |
        +---------+---------+
        |         |         |
        v         v         v
      Auth     Product    Order
     Service   Service   Service
        |         |         |
        v         v         v
      Auth DB  Product DB  Order DB
```

This diagram explains the system's major components.

It does not explain every class, function or database column.

### HLD Questions

- Which architectural style should we use?
- Which database should we select?
- How will services communicate?
- How will the system scale?
- How will we handle failures?
- Where will caching be implemented?

---

# 5. What Is Low-Level Design (LLD)?

Low-Level Design describes the internal implementation of individual components.

It focuses on:

- Classes.
- Functions.
- Interfaces.
- Database schemas.
- API contracts.
- Design patterns.
- Object relationships.
- Validation logic.

Example:

Suppose you are designing an Expense Service.

LLD might include:

```text
ExpenseController
       |
       v
ExpenseService
       |
       v
ExpenseRepository
       |
       v
MySQL Database
```

### Example Class Design

```typescript
interface Expense {
    id: number;
    userId: number;
    amount: number;
    category: string;
    description: string;
}

class ExpenseService {
    async createExpense(data: Expense) {
        // Business logic
    }

    async getExpenses(userId: number) {
        // Retrieve user expenses
    }

    async deleteExpense(id: number, userId: number) {
        // Validate ownership and delete
    }
}
```

### HLD vs LLD

| Feature | HLD | LLD |
|---|---|---|
| Full Form | High-Level Design | Low-Level Design |
| Focus | Overall architecture | Internal implementation |
| Components | Services, databases, infrastructure | Classes, functions, modules |
| Detail | Broad | Detailed |
| Audience | Architects, technical leads, developers | Developers and engineers |
| Example | Microservices architecture | ExpenseService implementation |

---

# 6. Functional vs Non-Functional Requirements

Before designing a system, understand its requirements.

There are two major categories.

## 6.1 Functional Requirements

Functional requirements describe **what the system should do**.

Example: Expense Tracker.

- Users can register.
- Users can log in.
- Users can add expenses.
- Users can update expenses.
- Users can delete expenses.
- Users can view monthly reports.
- Users can export reports.

These describe application functionality.

## 6.2 Non-Functional Requirements

Non-functional requirements describe **how well the system should operate**.

Examples:

- Response time should be under an agreed threshold.
- The system should support 100,000 active users.
- Data should be protected.
- The application should achieve a specified availability target.
- The system should recover from infrastructure failures.
- The application should be maintainable.

### Comparison

| Feature | Functional Requirements | Non-Functional Requirements |
|---|---|---|
| Focus | What the system does | How the system performs |
| Example | User login | Login response time |
| Example | Create expense | Support 10,000 requests per second |
| Example | Generate report | Generate report within 3 seconds |
| Example | Upload image | Support a maximum file size of 10 MB |

**Important:** Non-functional requirements should be measurable whenever possible.

Instead of saying:

"The application should be fast."

Define:

"95% of API requests should complete within 300 milliseconds under the expected workload."

---

# PART 2: SYSTEM PERFORMANCE AND SCALABILITY

# 7. What Is Scalability?

Scalability is the ability of a system to handle increasing workload by increasing available resources or improving how efficiently those resources are used.

Suppose your application initially has:

```text
100 users
```

After six months:

```text
10,000 users
```

After one year:

```text
1,000,000 users
```

Your architecture must be able to accommodate increased demand.

### Example

```text
Initial:
100 Users
    |
    v
1 Backend Server
    |
    v
1 Database
```

Growing application:

```text
10,000 Users
      |
      v
Load Balancer
      |
  +---+---+
  |       |
  v       v
Server 1 Server 2
  |       |
  +---+---+
      |
      v
   Database
```

Large application:

```text
1,000,000 Users
        |
        v
       CDN
        |
        v
  Load Balancer
        |
  +-----+-----+
  |     |     |
  v     v     v
 API   API   API
  |     |     |
  +-----+-----+
        |
   +----+----+
   |         |
   v         v
 Redis    Database
             |
       +-----+-----+
       |           |
       v           v
    Primary     Replicas
```

Scalability is not just adding servers. You must also consider database capacity, network bandwidth, caching, queues and bottlenecks.

---

# 8. Vertical Scaling

Vertical Scaling means increasing the resources of an existing server.

For example:

```text
Before:

CPU: 2 Cores
RAM: 4 GB
Storage: 100 GB
```

After upgrading:

```text
CPU: 8 Cores
RAM: 32 GB
Storage: 1 TB
```

The same server receives more resources.

### Architecture

```text
       Application
            |
            v
       Single Server
            |
            v
       Upgrade Hardware
            |
            v
       More CPU / RAM
```

### Advantages

- Simple to implement.
- Minimal application changes.
- Easier initial management.
- Useful for applications with modest workloads.

### Disadvantages

- Hardware has practical limits.
- Larger instances can be expensive.
- A single-server failure can affect the entire application.
- Scaling may require downtime depending on the infrastructure.

---

# 9. Horizontal Scaling

Horizontal Scaling means increasing capacity by adding more servers or instances.

Example:

```text
Before:

       Application
            |
            v
         Server 1
```

After:

```text
             Users
               |
               v
          Load Balancer
               |
        +------+------+ 
        |      |      |
        v      v      v
      Server 1 Server 2 Server 3
```

Requests are distributed across multiple servers.

### Advantages

- Supports larger workloads.
- Can improve fault tolerance.
- Allows independent capacity expansion.
- Works well with stateless application instances.

### Disadvantages

- More infrastructure complexity.
- Requires traffic distribution.
- Requires shared or external session management when needed.
- Distributed communication introduces latency and failure scenarios.

### Vertical vs Horizontal Scaling

| Feature | Vertical Scaling | Horizontal Scaling |
|---|---|---|
| Approach | Upgrade one machine | Add more machines |
| Complexity | Lower initially | Higher |
| Hardware Limit | Yes | Depends on architecture and platform |
| Fault Tolerance | Limited with one instance | Can improve with redundancy |
| Cost | Larger instances can be expensive | Cost depends on instance count and workload |
| Example | Upgrade EC2 instance | Add more EC2 instances |

**Important:** Horizontal scaling is especially effective when application instances are stateless.

---

# 10. Stateless vs Stateful Applications

A stateless application does not depend on local, persistent session state between requests.

A stateful application maintains information about previous interactions in a way that affects subsequent requests.

### Stateful Example

Suppose your application stores login sessions in local memory.

```javascript
const sessions = new Map();
```

If a user logs in on Server 1, the session may exist only on Server 1.

What happens if the next request reaches Server 2?

Server 2 may not recognize the session.

### Architecture

```text
          Load Balancer
                |
          +-----+-----+
          |           |
          v           v
       Server 1    Server 2
          |
          v
     Local Session
```

This creates a problem when scaling horizontally.

### Better Approach: Shared Session Storage

```text
             Load Balancer
                   |
            +------+------+
            |             |
            v             v
         Server 1      Server 2
            |             |
            +------+------+
                   |
                   v
                 Redis
                   |
                   v
             Shared Sessions
```

Now both servers can access the shared session store.

### Stateless Architecture

```text
Client
  |
  v
Load Balancer
  |
  +---------+---------+
  |         |         |
  v         v         v
Server 1  Server 2  Server 3
  |         |         |
  +---------+---------+
            |
            v
      Shared Database
```

Each server can handle requests without depending on local session state.

### Important

Stateless application servers do not mean the entire system has no state.

The state is stored in appropriate external systems such as:

- MySQL.
- MongoDB.
- Redis.
- Object storage.

---

# 11. Latency

Latency is the time taken for an operation or request to complete.

For an API:

```text
Client Sends Request
        |
        v
Server Receives Request
        |
        v
Process Request
        |
        v
Database Query
        |
        v
Return Response
```

The total time experienced by the client is request latency.

Example:

```text
API Response Time = 200 ms
```

This means the measured request took 200 milliseconds.

### Common Causes of High Latency

- Slow database queries.
- Network delays.
- Excessive API calls.
- Large response payloads.
- Synchronous background processing.
- Inefficient algorithms.
- External API dependencies.
- Cache misses.

### Latency Optimization

- Add database indexes.
- Use caching.
- Reduce unnecessary network calls.
- Optimize SQL queries.
- Compress responses where appropriate.
- Use a CDN.
- Move long-running tasks to background workers.

---

# 12. Throughput

Throughput measures how much work a system completes within a given period.

For example:

```text
1000 requests per second
```

means the system processes 1,000 requests every second under the measured conditions.

Common throughput units:

- Requests per second (RPS).
- Transactions per second (TPS).
- Messages per second.
- MB/s.
- GB/s.

### Latency vs Throughput

| Feature | Latency | Throughput |
|---|---|---|
| Meaning | Time taken by an operation | Amount of work completed per unit time |
| Unit | ms, seconds | RPS, TPS, MB/s |
| Focus | Speed of individual operation | Overall processing capacity |
| Example | API responds in 100 ms | API processes 5,000 requests/sec |

A system can have high throughput but still have high latency under certain workloads.

They are related but not identical.

---

# 13. Response Time Percentiles

When evaluating application performance, average response time is not enough.

Consider these API response times:

```text
20 ms
25 ms
30 ms
35 ms
40 ms
50 ms
100 ms
150 ms
500 ms
2000 ms
```

The average may hide the slowest requests.

Performance monitoring often uses percentiles.

### Important Percentiles

| Metric | Meaning |
|---|---|
| p50 | 50% of requests completed within this time |
| p90 | 90% of requests completed within this time |
| p95 | 95% of requests completed within this time |
| p99 | 99% of requests completed within this time |

Example:

```text
p50 = 100 ms
p95 = 400 ms
p99 = 900 ms
```

This means:

- 50% of requests complete within 100 ms.
- 95% complete within 400 ms.
- 99% complete within 900 ms.

The remaining 1% take longer than 900 ms.

**Important:** Percentiles are calculated from a measured request population over a defined time window. They are not necessarily the same as individual request measurements.

---

# 14. Availability

Availability measures how often a system is operational and able to provide its intended functionality.

It is commonly expressed as a percentage.

Example:

```text
Availability = 99.9%
```

### Availability Targets

| Availability | Approximate Downtime Per 30-Day Month |
|---|---:|
| 99% | 7 hours 12 minutes |
| 99.9% | 43 minutes 12 seconds |
| 99.95% | 21 minutes 36 seconds |
| 99.99% | 4 minutes 19 seconds |
| 99.999% | 26 seconds |

These are theoretical downtime budgets assuming a 30-day month.

### Formula

```text
Availability =
(Uptime / Total Time) × 100
```

Example:

Suppose a system operates for 720 hours in a month and is unavailable for 1 hour.

```text
Uptime = 719 hours

Availability = (719 / 720) × 100

Availability ≈ 99.861%
```

### How to Improve Availability

- Use multiple application instances.
- Use load balancing.
- Deploy across multiple Availability Zones.
- Configure database backups.
- Implement health checks.
- Use failover mechanisms.
- Monitor application health.
- Design for component failures.

AWS reliability guidance emphasizes backups, fault isolation, recovery planning and testing. [AWS Documentation](https://docs.aws.amazon.com/wellarchitected/latest/framework/reliability.html?utm_source=chatgpt.com)

---

# 15. Reliability vs Availability

These concepts are related but not identical.

**Reliability** describes the ability of a system to perform its intended function consistently over time.

**Availability** describes whether the system is operational and accessible when required.

Example:

An application might be available but return incorrect financial calculations.

It is available, but it is not functioning reliably.

### Comparison

| Feature | Reliability | Availability |
|---|---|---|
| Focus | Correct and consistent operation | Operational accessibility |
| Example | Correct transaction processing | Application is reachable |
| Failure | Incorrect results | Service unavailable |
| Improvement | Testing, validation, fault handling | Redundancy, failover, monitoring |

---

# 16. Durability

Durability means the ability to preserve data over time, even when failures occur.

Example:

Suppose a user creates an expense.

After receiving a successful response, the expense should not disappear because an application server restarts.

Durability can be improved through:

- Database persistence.
- Replication.
- Backups.
- Transaction logs.
- Storage redundancy.
- Disaster recovery procedures.

### Reliability vs Availability vs Durability

| Concept | Main Question |
|---|---|
| Reliability | Does the system operate correctly? |
| Availability | Is the system accessible? |
| Durability | Is stored data preserved? |

---

# 17. SLI, SLO and SLA

These concepts are important in production system design.

### SLI – Service Level Indicator

An SLI is a metric used to measure service performance.

Examples:

- API availability.
- Request latency.
- Error rate.
- Successful transaction rate.

### SLO – Service Level Objective

An SLO is a target for an SLI.

Example:

```text
99.9% of valid API requests should succeed
over a rolling 30-day period.
```

### SLA – Service Level Agreement

An SLA is a formal agreement defining service expectations and potentially the consequences of not meeting them.

### Comparison

| Term | Meaning | Example |
|---|---|---|
| SLI | What you measure | API availability |
| SLO | Target you aim to meet | 99.9% availability |
| SLA | Formal service commitment | Contractual availability guarantee |

---

# PART 3: ARCHITECTURAL STYLES

# 18. What Is Software Architecture?

Software Architecture defines the structure of an application and the relationships between its components.

It includes:

- Component organization.
- Data flow.
- Communication.
- Technology choices.
- Deployment structure.
- Security boundaries.
- Scaling strategy.

There are several architectural styles.

We will study:

1. Monolithic Architecture.
2. Modular Monolith.
3. Microservices Architecture.
4. Service-Oriented Architecture.
5. Event-Driven Architecture.
6. Client-Server Architecture.

---

# 19. Monolithic Architecture

A Monolithic Architecture is an approach where the application's main functionality is packaged and deployed as one application.

Example:

```text
                Application
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Auth       Products    Orders
       Module      Module     Module
          |          |          |
          +----------+----------+
                     |
                     v
                  Database
```

The modules may be logically separated, but they are part of one deployable application.

### Example: Node.js Application

```text
expense-tracker/
    src/
        controllers/
        services/
        models/
        routes/
        middleware/
        config/

    package.json
    server.js
```

You deploy the complete application as one unit.

### Advantages

- Simple deployment.
- Easier local development.
- Direct function calls between modules.
- Easier initial debugging.
- Lower infrastructure complexity.
- Often suitable for small and medium applications.

### Disadvantages

- Large codebases can become difficult to maintain.
- Components may become tightly coupled.
- Independent deployment of modules is difficult.
- Scaling individual components is harder.
- A failure can affect the entire application process.

### When to Use?

- Small applications.
- Startups.
- MVPs.
- Applications with a small development team.
- Systems with relatively simple operational requirements.

---

# 20. Modular Monolith

A Modular Monolith is a single deployable application with clearly defined internal modules and boundaries.

Example:

```text
                 Application
                      |
        +-------------+-------------+
        |             |             |
        v             v             v
    Auth Module   Expense Module  Report Module
        |             |             |
        +-------------+-------------+
                      |
                      v
                   Database
```

Each module owns a specific business responsibility.

For example:

```text
modules/
    auth/
        controller.js
        service.js
        repository.js

    expenses/
        controller.js
        service.js
        repository.js

    reports/
        controller.js
        service.js
        repository.js
```

### Why Is It Useful?

It provides internal separation without requiring distributed services.

You can maintain:

- Clear module boundaries.
- Independent business logic.
- Better testing.
- Easier future extraction into services.

### Monolith vs Modular Monolith

| Feature | Traditional Monolith | Modular Monolith |
|---|---|---|
| Deployment | One application | One application |
| Module Boundaries | May be weak | Explicit |
| Internal Coupling | Can become high | Designed to be lower |
| Development | Can become difficult at scale | More structured |
| Operational Complexity | Relatively low | Relatively low |

For many applications, a modular monolith is a strong starting architecture.

---

# 21. Microservices Architecture

Microservices Architecture structures an application as a collection of independently deployable services.

Each service is responsible for a specific business capability.

Example: E-commerce application.

```text
                       API Gateway
                            |
           +----------------+----------------+
           |                |                |
           v                v                v
      User Service    Product Service    Order Service
           |                |                |
           v                v                v
        User DB          Product DB         Order DB
```

Additional services might include:

```text
Payment Service
Inventory Service
Notification Service
Shipping Service
```

### Characteristics

- Independent deployment.
- Business-capability boundaries.
- Network-based communication.
- Service-specific scaling.
- Independent data ownership.
- Failure isolation when correctly designed.

### Advantages

- Independent deployment.
- Independent scaling.
- Team autonomy.
- Technology flexibility.
- Better isolation of certain failures.
- Easier ownership of individual business capabilities.

### Disadvantages

- Network latency.
- Distributed failure handling.
- More complex monitoring.
- Data consistency challenges.
- Service discovery.
- Deployment complexity.
- Increased infrastructure costs.

Martin Fowler's microservices guidance emphasizes both independent deployment and the additional complexity introduced by distributed systems. [martinfowler.com](https://martinfowler.com/microservices/?utm_source=chatgpt.com)

---

# 22. Monolith vs Microservices

| Feature | Monolith | Microservices |
|---|---|---|
| Deployment | One application | Multiple services |
| Database | Often shared | Often independently owned |
| Communication | Function calls or internal messaging | Network APIs or messaging |
| Scaling | Usually application-level | Service-level |
| Complexity | Lower initially | Higher |
| Failure Isolation | More limited | Can be stronger when designed correctly |
| Development | Simpler for small teams | Better suited to independent service teams |
| Infrastructure | Simpler | More infrastructure |
| Best Use | Small and medium systems | Systems with strong reasons for service separation |

### Important Recommendation

Do not adopt microservices simply because an application is becoming popular.

Start with a well-structured architecture.

Extract services when there is a concrete reason, such as:

- Independent scaling requirements.
- Different release cycles.
- Clear business boundaries.
- Team ownership requirements.
- Isolation of resource-intensive workloads.

The monolith-first approach is discussed in Martin Fowler's architecture guidance. [martinfowler.com](https://martinfowler.com/bliki/MonolithFirst.html?source=your_stories_page---------------------------\&utm_source=chatgpt.com)

---

# 23. Service-Oriented Architecture (SOA)

SOA stands for Service-Oriented Architecture.

It organizes software functionality into reusable services that communicate through defined interfaces.

Example:

```text
                Enterprise Application
                         |
                  Service Layer
                         |
       +-----------------+----------------+
       |                 |                |
       v                 v                v
   Customer          Payment          Inventory
   Service           Service           Service
```

SOA is often associated with enterprise integration and shared services.

### SOA vs Microservices

| Feature | SOA | Microservices |
|---|---|---|
| Focus | Reusable enterprise services | Independently deployable business services |
| Service Size | Often broader | Often smaller, business-capability focused |
| Communication | May use enterprise messaging or ESB | Commonly lightweight APIs and messaging |
| Data | May involve shared enterprise systems | Strong emphasis on service-owned data |
| Governance | Often more centralized | Often more decentralized |

These are architectural tendencies, not strict rules.

---

# 24. Client-Server Architecture

Client-Server Architecture divides a system into:

- Client.
- Server.

The client requests services.

The server processes requests and returns responses.

Example:

```text
Browser
   |
   v
HTTP Request
   |
   v
Backend Server
   |
   v
Database
```

### Example in Full-Stack Development

```text
Next.js Frontend
       |
       v
Express Backend
       |
       v
MySQL Database
```

### Responsibilities

| Component | Responsibility |
|---|---|
| Client | User interface and interaction |
| Server | Business logic and data processing |
| Database | Persistent data storage |

---

# PART 4: LOAD BALANCING AND API INFRASTRUCTURE

# 25. What Is Load Balancing?

Load Balancing distributes incoming traffic across multiple servers or application instances.

Suppose you have three backend servers.

Without load balancing, all traffic may go to one server.

With load balancing, requests can be distributed across available instances.

### Architecture

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
```

### Benefits

- Better traffic distribution.
- Improved availability when configured with healthy targets.
- Better resource utilization.
- Easier horizontal scaling.
- Reduced pressure on individual servers.

---

# 26. Load Balancing Algorithms

Common algorithms include:

## 26.1 Round Robin

Requests are distributed in sequence.

```text
Request 1 -> Server 1
Request 2 -> Server 2
Request 3 -> Server 3
Request 4 -> Server 1
Request 5 -> Server 2
```

## 26.2 Weighted Round Robin

Servers receive traffic according to assigned weights.

Example:

```text
Server 1: Weight 5
Server 2: Weight 3
Server 3: Weight 2
```

Server 1 receives a larger share of traffic.

## 26.3 Least Connections

Requests are directed toward a server with fewer active connections, according to the load balancer's connection accounting.

## 26.4 IP Hash

A client identifier, commonly derived from the source IP address, is used to select a server.

This can provide affinity but may create uneven distribution.

### Comparison

| Algorithm | Description |
|---|---|
| Round Robin | Distributes requests sequentially |
| Weighted Round Robin | Distributes according to weights |
| Least Connections | Prefers fewer active connections |
| IP Hash | Selects based on client IP information |

The correct algorithm depends on application behavior, connection duration and traffic characteristics.

---

# 27. What Is a Reverse Proxy?

A Reverse Proxy is a server that receives client requests and forwards them to backend servers.

The client communicates with the reverse proxy rather than directly communicating with the internal application server.

### Architecture

```text
Client
   |
   v
Nginx Reverse Proxy
   |
   +----------+
   |          |
   v          v
Backend 1  Backend 2
```

### Common Responsibilities

- Request routing.
- TLS termination.
- Compression.
- Static file serving.
- Caching.
- Security controls.
- Load balancing.

### Example Nginx Configuration

```nginx
server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://localhost:3000;

        proxy_set_header Host $host;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Here, Nginx receives requests and forwards them to the application running on port 3000.

---

# 28. What Is an API Gateway?

An API Gateway is an entry point through which clients access backend APIs or services.

It can provide:

- Routing.
- Authentication integration.
- Rate limiting.
- Request transformation.
- API versioning.
- Logging.
- Traffic management.

### Architecture

```text
                  Clients
                     |
                     v
                API Gateway
                     |
        +------------+------------+
        |            |            |
        v            v            v
    Auth API     Product API   Order API
```

### Why Use an API Gateway?

Suppose your application has 10 microservices.

Without an API Gateway, the frontend may need to know the address of each service.

With an API Gateway:

```text
Frontend
   |
   v
API Gateway
   |
   +---- Auth Service
   |
   +---- Product Service
   |
   +---- Order Service
   |
   +---- Payment Service
```

The frontend uses a centralized entry point.

### API Gateway vs Reverse Proxy vs Load Balancer

| Feature | Reverse Proxy | Load Balancer | API Gateway |
|---|---|---|---|
| Request Forwarding | Yes | Yes | Yes |
| Traffic Distribution | Sometimes | Core responsibility | Often |
| Authentication Integration | Possible | Depends on product | Common |
| API Routing | Possible | Limited or product-dependent | Core responsibility |
| Rate Limiting | Possible | Often supported | Common |
| API Management | Limited | Not primary purpose | Common |

These roles can overlap. Some products provide multiple capabilities.

---

# PART 5: DATABASE ARCHITECTURE AND SCALING

# 29. Why Does Database Scaling Matter?

As your application grows, database workload increases.

Suppose your application receives:

```text
100 users
```

The database handles a manageable number of requests.

Later:

```text
100,000 users
```

Now the database may experience:

- More concurrent connections.
- More read queries.
- More write queries.
- Higher memory usage.
- Higher disk I/O.
- Slow queries.
- Lock contention.

Database architecture must evolve as workload increases.

---

# 30. SQL vs NoSQL Architecture

### SQL Databases

Examples:

- MySQL.
- PostgreSQL.
- SQL Server.

They generally use relational tables and support structured query capabilities.

Example:

```text
users
-----
id
name
email

expenses
--------
id
user_id
amount
category
```

### NoSQL Databases

Examples:

- MongoDB.
- DynamoDB.
- Cassandra.

NoSQL databases use different data models, including document, key-value, wide-column and graph models.

MongoDB example:

```json
{
  "_id": "101",
  "name": "User A",
  "email": "user@example.com",
  "expenses": [
    {
      "amount": 500,
      "category": "Food"
    }
  ]
}
```

### SQL vs NoSQL

| Feature | SQL | NoSQL |
|---|---|---|
| Data Model | Relational tables | Multiple data models |
| Schema | Commonly predefined | Varies by database |
| Relationships | Strong relational capabilities | Depends on database model |
| Transactions | Supported by many relational systems | Depends on database |
| Query Language | SQL | Database-specific |
| Example | MySQL | MongoDB |

### When to Choose SQL?

- Financial transactions.
- Strong relational requirements.
- Complex joins.
- Structured business data.
- Transaction-heavy workflows.

### When to Choose NoSQL?

- Document-oriented data.
- Flexible data structures.
- Specific high-scale access patterns.
- Key-value workloads.
- Use cases supported by the selected database's consistency and query model.

**Important:** NoSQL does not automatically mean faster or more scalable than SQL. Database selection should follow data relationships, consistency requirements, query patterns and operational constraints.

---

# 31. Database Indexing

An Index helps a database locate rows more efficiently.

Suppose you have a table:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255)
);
```

You frequently search users by email:

```sql
SELECT *
FROM users
WHERE email = 'user@example.com';
```

Without an appropriate index, the database may need to scan many rows.

Create an index:

```sql
CREATE INDEX idx_users_email
ON users(email);
```

Now the database can use the index to locate matching rows more efficiently.

### Index Benefits

- Faster lookups.
- Improved filtering.
- Faster joins in suitable cases.
- Improved sorting in suitable cases.

### Index Disadvantages

- Additional storage.
- Slower writes due to index maintenance.
- Additional maintenance overhead.
- Poorly selected indexes can waste resources.

### Important

Indexes should be designed based on actual query patterns.

Use:

```sql
EXPLAIN
SELECT *
FROM users
WHERE email = 'user@example.com';
```

to inspect the query execution plan in MySQL.

---

# 32. Database Replication

Replication means maintaining copies of data across multiple database instances.

A common architecture is:

- Primary database.
- Read replicas.

### Architecture

```text
                Application
                     |
                     v
                Primary DB
                     |
             Replication Stream
                     |
           +---------+---------+
           |                   |
           v                   v
       Read Replica 1      Read Replica 2
```

### Primary Database

Typically handles writes.

Example:

```sql
INSERT INTO expenses
(user_id, amount)
VALUES
(101, 500);
```

### Read Replica

Can serve eligible read queries.

Example:

```sql
SELECT *
FROM expenses
WHERE user_id = 101;
```

### Benefits

- Distributes read traffic.
- Improves read capacity.
- Can support reporting workloads.
- Can improve recovery options when configured appropriately.

### Disadvantages

- Replication lag.
- Additional infrastructure.
- More complex routing.
- Failover management.

### Important: Replication Lag

Replication may be asynchronous.

This means the primary database can contain newer data than a replica.

Example:

```text
Primary DB:
Expense = 500

Replica:
Expense = 450
```

For a short period, the replica may return stale data.

For read-after-write consistency, the application may need to read from the primary or use a replication-aware strategy.

---

# 33. Database Sharding

Sharding divides data across multiple database instances.

Each database instance stores a portion of the total dataset.

Example:

Suppose you have 1 million users.

You divide them into three shards.

```text
              Application
                   |
                   v
               Shard Router
                   |
          +--------+--------+
          |        |        |
          v        v        v
       Shard 1  Shard 2  Shard 3
       Users    Users    Users
       1-333k   334-666k 667k-1M
```

### Why Use Sharding?

A single database may eventually reach its practical storage or processing limits.

Sharding distributes data and workload across multiple database instances.

### Common Sharding Strategies

#### 1. Range-Based Sharding

Divide records based on ranges.

Example:

```text
User ID 1–1000     -> Shard 1
User ID 1001–2000  -> Shard 2
User ID 2001–3000  -> Shard 3
```

#### 2. Hash-Based Sharding

Use a hash function to determine the shard.

```text
shard = hash(userId) % numberOfShards
```

#### 3. Geographic Sharding

Store data according to geographical regions.

Example:

```text
India Users  -> India Shard
US Users     -> US Shard
Europe Users -> Europe Shard
```

### Replication vs Sharding

| Feature | Replication | Sharding |
|---|---|---|
| Purpose | Maintain data copies | Divide data |
| Data | Same logical dataset | Different subsets |
| Main Benefit | Read scaling and resilience | Data and workload distribution |
| Complexity | Moderate | High |
| Example | Primary + replicas | Multiple user-data shards |

A system can use both replication and sharding.

---

# PART 6: CACHING AND REDIS

# 34. What Is Caching?

Caching stores frequently accessed data in a faster storage layer so that future requests can be served more quickly.

Suppose your application frequently retrieves product information.

Without caching:

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
Return Product
```

Every request queries the database.

With caching:

```text
Client
  |
  v
Backend
  |
  v
Cache
  |
  +---- Cache Hit ----> Return Data
  |
  +---- Cache Miss ---> Database
                           |
                           v
                       Store in Cache
                           |
                           v
                       Return Data
```

### Benefits

- Reduced database load.
- Lower response latency.
- Improved throughput.
- Better performance for frequently accessed data.

### Disadvantages

- Cache invalidation complexity.
- Stale data.
- Additional infrastructure.
- Memory constraints.

---

# 35. What Is Redis?

Redis is an in-memory data store commonly used for:

- Caching.
- Session storage.
- Rate limiting.
- Counters.
- Queues and streams.
- Real-time application data.

Official documentation: [Redis Documentation](https://redis.io/docs/latest/)

### Architecture

```text
Frontend
   |
   v
Backend
   |
   v
Redis Cache
   |
   +---- Hit ----> Return Data
   |
   +---- Miss ---> MySQL
                     |
                     v
                 Store Result
                     |
                     v
                 Redis Cache
```

### Example

Suppose your API retrieves product information.

```javascript
const product = await redis.get(`product:${id}`);

if (product) {
    return JSON.parse(product);
}

const productFromDB = await Product.findByPk(id);

if (productFromDB) {
    await redis.set(
        `product:${id}`,
        JSON.stringify(productFromDB),
        "EX",
        300
    );
}

return productFromDB;
```

This is a simplified Redis client example. Exact commands depend on the Redis client version and API you use.

### What Does EX 300 Mean?

It sets the key's expiration time to 300 seconds.

That is five minutes.

---

# 36. Caching Strategies

## 36.1 Cache-Aside

The application checks the cache first.

If data is missing, it retrieves data from the database and stores it in the cache.

```text
Application
    |
    v
Check Cache
    |
    +---- Hit ----> Return Data
    |
    +---- Miss ---> Database
                      |
                      v
                  Store Cache
                      |
                      v
                  Return Data
```

This is one of the most commonly used caching patterns.

## 36.2 Write-Through

The application writes to the cache and the cache layer updates the backing store as part of the write operation.

```text
Application
    |
    v
Cache Layer
    |
    +------> Database
    |
    v
Return Result
```

The exact ordering and consistency guarantees depend on the implementation.

## 36.3 Write-Behind

The application writes to the cache first.

The backing database is updated asynchronously later.

```text
Application
    |
    v
Cache
    |
    v
Return Success
    |
    v
Background Write
    |
    v
Database
```

This can improve write performance, but it introduces durability and data-loss risks if the cache or write pipeline fails before the database is updated.

### Comparison

| Strategy | Read Behavior | Write Behavior | Main Trade-off |
|---|---|---|---|
| Cache-Aside | Check cache, then database | Application updates database and manages cache | Cache invalidation |
| Write-Through | Read from cache | Cache layer updates backing store | Write latency |
| Write-Behind | Read from cache | Database update is asynchronous | Durability and consistency |

---

# 37. Cache Invalidation

Cache invalidation means removing or updating cached data when the underlying data changes.

Suppose:

```text
Product Price = ₹500
```

The value is cached.

Later, the database changes:

```text
Product Price = ₹600
```

But Redis still contains:

```text
₹500
```

The application may return stale information.

### One Approach: Delete Cache After Update

```javascript
await Product.update(
    { price: 600 },
    {
        where: {
            id: productId
        }
    }
);

await redis.del(`product:${productId}`);
```

The next read retrieves the latest value from the database.

### Important Caching Concepts

- TTL.
- Cache invalidation.
- Cache stampede.
- Cache penetration.
- Cache eviction.
- Cache consistency.

---

# 38. Cache Stampede

A cache stampede occurs when many requests attempt to rebuild the same expired or missing cache entry simultaneously.

Example:

```text
Cache Expires
     |
     v
1000 Requests Arrive
     |
     v
1000 Database Queries
     |
     v
Database Overloaded
```

### Prevention

- Request coalescing.
- Distributed locking.
- Early refresh.
- Randomized TTL.
- Stale-while-revalidate patterns.

A practical implementation should avoid allowing every request to perform the same expensive database query.

---

# PART 7: MESSAGE QUEUES AND EVENT-DRIVEN ARCHITECTURE

# 39. What Is a Message Queue?

A Message Queue allows different parts of an application to communicate asynchronously.

Instead of waiting for another service to complete an operation, a service can publish a message for later processing.

### Without a Queue

Suppose a user places an order.

The backend must:

1. Save the order.
2. Process payment.
3. Send an email.
4. Generate an invoice.
5. Notify inventory.

```text
Order API
    |
    v
Save Order
    |
    v
Payment Processing
    |
    v
Send Email
    |
    v
Generate Invoice
    |
    v
Update Inventory
    |
    v
Return Response
```

The user may have to wait for all operations.

### With a Queue

```text
             Order API
                 |
                 v
             Save Order
                 |
                 v
            Publish Event
                 |
                 v
             Message Queue
                 |
        +--------+--------+
        |        |        |
        v        v        v
      Email   Invoice  Inventory
      Worker  Worker   Worker
```

The order API can return after its required synchronous work is complete and the event has been durably accepted according to the messaging system's guarantees.

Background workers process additional tasks.

### Benefits

- Asynchronous processing.
- Reduced response time for user-facing operations.
- Better decoupling.
- Workload buffering.
- Independent worker scaling.
- Improved resilience when configured correctly.

---

# 40. What Is Event-Driven Architecture?

Event-Driven Architecture is an architectural style where components communicate through events.

An event represents something that has happened.

Examples:

```text
UserRegistered
OrderCreated
PaymentCompleted
ExpenseAdded
InvoiceGenerated
```

### Architecture

```text
          Order Service
                |
                v
          OrderCreated Event
                |
                v
           Event Broker
                |
        +-------+-------+
        |       |       |
        v       v       v
      Email  Inventory Analytics
     Service  Service   Service
```

Each consumer reacts to the event independently.

### Event vs Command

| Feature | Event | Command |
|---|---|---|
| Meaning | Something happened | Request to perform an action |
| Naming | Past tense | Action-oriented |
| Example | `OrderCreated` | `CreateOrder` |
| Nature | Describes a fact | Requests work |

---

# 41. What Is RabbitMQ?

RabbitMQ is a message broker that supports messaging patterns such as queues and exchanges.

It is commonly used for:

- Background jobs.
- Task processing.
- Service communication.
- Work distribution.
- Routing messages.

Official documentation: [RabbitMQ Documentation](https://www.rabbitmq.com/docs)

### RabbitMQ Architecture

```text
Producer
   |
   v
Exchange
   |
   v
Queue
   |
   v
Consumer
```

### Main Components

| Component | Purpose |
|---|---|
| Producer | Publishes messages |
| Exchange | Routes messages |
| Queue | Stores messages |
| Binding | Connects exchanges and queues |
| Consumer | Processes messages |
| Acknowledgment | Confirms message processing |

### Common Exchange Types

- Direct.
- Fanout.
- Topic.
- Headers.

RabbitMQ is useful for task distribution and flexible message routing.

---

# 42. What Is Apache Kafka?

Apache Kafka is a distributed event-streaming platform.

It is designed for publishing, storing and processing streams of events.

Common use cases:

- Event streaming.
- Activity tracking.
- Log aggregation.
- Real-time analytics.
- Data integration.
- Event-driven microservices.

Official documentation: [Apache Kafka Documentation](https://kafka.apache.org/documentation/)

### Kafka Architecture

```text
Producer
   |
   v
Kafka Topic
   |
   +---------+---------+
   |                   |
   v                   v
Consumer Group A   Consumer Group B
   |                   |
   v                   v
Analytics          Notification
Service            Service
```

### Important Kafka Concepts

| Concept | Meaning |
|---|---|
| Producer | Publishes records |
| Consumer | Reads records |
| Topic | Logical event stream |
| Partition | Ordered subset of a topic |
| Broker | Kafka server |
| Consumer Group | Group of consumers sharing partitions |
| Offset | Position of a record in a partition |
| Replication | Copies partitions across brokers |

### Kafka Topics and Partitions

Suppose you create:

```text
orders
```

This topic has three partitions:

```text
orders
  |
  +---- Partition 0
  |
  +---- Partition 1
  |
  +---- Partition 2
```

Partitions allow data distribution and parallel consumption.

Ordering is guaranteed within a partition, not globally across all partitions.

---

# 43. Kafka vs RabbitMQ

| Feature | Kafka | RabbitMQ |
|---|---|---|
| Main Model | Distributed event log | Message broker |
| Storage | Persistent partitioned logs | Queues with configurable durability |
| Message Consumption | Consumers track offsets | Consumers receive messages and acknowledge them |
| Ordering | Within a partition | Depends on queue and routing configuration |
| Replay | Supports rereading retained records | Different replay model; not its primary purpose |
| Main Use | Event streaming and high-volume data pipelines | Task queues and flexible routing |
| Scaling | Partition-based | Queue and broker topology dependent |

### When to Use RabbitMQ?

- Background task processing.
- Job queues.
- Work distribution.
- Flexible routing.
- Request/reply messaging.

### When to Use Kafka?

- High-volume event streams.
- Event history.
- Real-time analytics.
- Data integration.
- Multiple independent consumers.

**Important:** Kafka and RabbitMQ are not interchangeable in every situation. Select based on delivery requirements, ordering, replay, routing, throughput and operational needs.

---

# PART 8: CONTENT DELIVERY NETWORKS

# 44. What Is a CDN?

CDN stands for Content Delivery Network.

A CDN distributes content through geographically distributed edge locations.

The purpose is to deliver content closer to users.

Suppose your server is located in Mumbai.

A user from Europe requests an image.

Without a CDN:

```text
European User
      |
      v
Mumbai Server
      |
      v
Image Response
```

The request travels a relatively long network path.

With a CDN:

```text
European User
      |
      v
Nearest CDN Edge
      |
      v
Cached Image
```

The content may be delivered from a nearby edge location.

### CDN Benefits

- Lower latency for cached content.
- Reduced origin-server load.
- Better content delivery.
- Improved global reach.
- Support for traffic absorption and some DDoS protections, depending on the provider.

### Common CDN Providers

- Cloudflare.
- Amazon CloudFront.
- Akamai.
- Fastly.

### CDN Use Cases

- Images.
- Videos.
- CSS.
- JavaScript.
- Fonts.
- Downloadable files.
- Cacheable API responses.

---

# 45. CDN Architecture

```text
                 Users
                   |
                   v
                CDN Edge
                   |
           +-------+-------+
           |               |
           v               v
       Cache Hit        Cache Miss
           |               |
           v               v
      Return Content   Origin Server
                           |
                           v
                      Store Cache
                           |
                           v
                      Return Content
```

### Important CDN Concepts

| Concept | Meaning |
|---|---|
| Edge Location | Distributed point of content delivery |
| Origin Server | Original content source |
| Cache Hit | Requested content is available in cache |
| Cache Miss | Content must be fetched from origin |
| TTL | Cache lifetime |
| Purge | Remove cached content |
| Cache Key | Identifies a cached response |

### Example

Suppose a product image is requested 10,000 times.

Without CDN, the origin server may handle every request.

With a CDN, many requests can be served from edge caches.

---

# PART 9: DISTRIBUTED SYSTEMS

# 46. What Is a Distributed System?

A Distributed System consists of multiple independent computers or processes that communicate over a network and work together as one logical system.

Examples:

- Microservices applications.
- Distributed databases.
- Kafka clusters.
- Cloud infrastructure.
- Distributed caching systems.

### Architecture

```text
              Distributed System
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
        Node 1      Node 2      Node 3
          |           |           |
          +-----------+-----------+
                      |
                   Network
```

Each node may operate independently.

The nodes communicate to coordinate tasks and exchange data.

### Why Are Distributed Systems Difficult?

Because:

- Networks can fail.
- Messages can be delayed.
- Nodes can crash.
- Messages can arrive more than once.
- Data may become temporarily inconsistent.
- Services may become unavailable independently.

AWS reliability guidance highlights that networked components must be designed to handle latency, packet loss, partitions and repeated messages. [AWS Documentation](https://docs.aws.amazon.com/wellarchitected/latest/framework/rel04.html?utm_source=chatgpt.com)

---

# 47. Network Partitions

A Network Partition occurs when parts of a distributed system cannot communicate with each other.

Example:

```text
       Node A
          |
          X
          |
       Node B
```

The nodes are operational, but communication between them is interrupted.

This can cause:

- Replication delays.
- Inconsistent data views.
- Failed requests.
- Split-brain risks in poorly designed systems.

Distributed systems must define what they do when communication fails.

---

# 48. CAP Theorem

The CAP theorem describes a trade-off in distributed data systems during a network partition.

CAP stands for:

- C – Consistency.
- A – Availability.
- P – Partition Tolerance.

### Consistency

In the CAP context, consistency means that reads behave as though they access a single, up-to-date copy of the data according to the system's consistency model.

### Availability

Every request to a non-failing node receives a response, although the response may not contain the latest data.

### Partition Tolerance

The system continues operating despite communication failures between nodes.

### CAP Concept

```text
             CAP
              |
      +-------+-------+
      |       |       |
      v       v       v
 Consistency Availability Partition
                         Tolerance
```

When a network partition occurs, a distributed system must make a trade-off between providing consistent responses and remaining available.

### CP Systems

A CP-oriented system may reject or delay some operations during a partition to preserve consistency.

### AP Systems

An AP-oriented system may continue serving requests, accepting that some responses may be stale or inconsistent until reconciliation occurs.

### Important Clarification

CAP does not mean that every distributed database permanently chooses only two properties.

The trade-off concerns behavior during a network partition. Without a partition, the system may provide both consistency and availability under its normal operating conditions.

---

# 49. Consistency Models

Consistency describes how updates become visible to readers in a distributed system.

Important models include:

### 49.1 Strong Consistency

A successful write becomes visible to subsequent reads according to the system's defined consistency guarantee.

Example:

A banking balance update should be visible according to the transaction's consistency requirements.

### 49.2 Eventual Consistency

Replicas may temporarily return different values, but if updates stop and replication succeeds, they eventually converge.

Example:

A social media follower count may take a short time to update everywhere.

### 49.3 Read-Your-Writes Consistency

A client can read its own successful updates.

Example:

After changing a profile name, the user should see the new name in subsequent reads.

### 49.4 Monotonic Reads

Once a client has observed a particular version of data, subsequent reads should not move backward to an older version.

### Comparison

| Model | Behavior |
|---|---|
| Strong Consistency | Reads reflect the required latest committed state |
| Eventual Consistency | Replicas converge over time |
| Read-Your-Writes | Client sees its own writes |
| Monotonic Reads | Client does not move backward to older observed data |

---

# PART 10: FAULT TOLERANCE AND RESILIENCE

# 50. What Is Fault Tolerance?

Fault Tolerance is the ability of a system to continue providing required functionality despite certain component failures.

Example:

Suppose your application runs on three backend servers.

If one server fails, the remaining servers continue handling traffic.

```text
             Load Balancer
                   |
           +-------+-------+
           |       |       |
           v       v       v
        Server 1 Server 2 Server 3
           |       X       |
           |               |
           +-------+-------+
                   |
                   v
              Application
```

### Common Fault-Tolerance Techniques

- Redundancy.
- Replication.
- Health checks.
- Failover.
- Timeouts.
- Retries.
- Circuit breakers.
- Bulkheads.
- Graceful degradation.

---

# 51. What Is Redundancy?

Redundancy means maintaining additional components or resources that can take over when primary components fail.

Example:

```text
Primary Database
       |
       v
Standby Database
```

If the primary database fails, a configured failover mechanism can promote the standby.

Redundancy may be implemented across:

- Servers.
- Databases.
- Availability Zones.
- Network paths.
- Storage systems.

### Important

Redundancy does not automatically guarantee high availability.

You must also consider:

- Failure detection.
- Failover automation.
- Data consistency.
- Shared dependencies.
- Recovery time.
- Testing.

---

# 52. What Is Failover?

Failover is the process of switching operations from a failed component to a healthy alternative.

Example:

```text
Normal Operation:

Primary Server
      |
      v
Handles Traffic

Failure:

Primary Server
      X

Failover:

Backup Server
      |
      v
Handles Traffic
```

Failover can be:

- Automatic.
- Manual.
- Application-managed.
- Infrastructure-managed.

---

# 53. What Is Graceful Degradation?

Graceful Degradation means the system continues providing essential functionality when optional components fail.

Example:

Suppose an e-commerce application has:

- Product browsing.
- Shopping cart.
- Recommendations.
- Reviews.
- Checkout.

If the recommendation service fails, users should still be able to browse products and complete purchases.

```text
Recommendation Service Failed
            |
            v
Disable Recommendations
            |
            v
Core Application Continues
```

This is preferable to making the entire application unavailable.

---

# PART 11: RESILIENCE DESIGN PATTERNS

# 54. Timeout Pattern

A Timeout limits how long an operation can wait for a response.

Suppose your Node.js application calls an external payment service.

Without a timeout, a request may wait too long.

Example:

```javascript
async function callPaymentService() {
    const response = await fetch(
        "https://payment.example.com/charge",
        {
            method: "POST",
            signal: AbortSignal.timeout(5000)
        }
    );

    return response.json();
}
```

The request is limited to five seconds.

### Why Are Timeouts Important?

- Prevent indefinite waiting.
- Protect application resources.
- Limit the impact of slow dependencies.
- Improve predictable failure handling.

**Important:** A timeout does not prove that the remote operation failed. The remote service may have completed the operation even though the response was delayed or lost.

For payment operations, this is one reason idempotency is important.

---

# 55. Retry Pattern

A Retry Pattern attempts an operation again after a failure.

Example:

```text
Request
   |
   v
External API
   |
   v
Temporary Failure
   |
   v
Retry
   |
   v
Success or Final Failure
```

### Node.js Example

```javascript
async function retryOperation(
    operation,
    maxRetries = 3
) {
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
            return await operation();
        } catch (error) {
            if (attempt === maxRetries) {
                throw error;
            }

            await new Promise(resolve =>
                setTimeout(resolve, attempt * 1000)
            );
        }
    }
}
```

This example uses a simple increasing delay.

Production retry policies should also consider:

- Retryable error types.
- Maximum total time.
- Exponential backoff.
- Jitter.
- Retry budgets.
- Idempotency.
- Server-provided retry instructions.

### Exponential Backoff

Instead of retrying every second:

```text
Retry 1: 1 second
Retry 2: 2 seconds
Retry 3: 4 seconds
Retry 4: 8 seconds
```

Jitter adds randomness to avoid many clients retrying simultaneously.

---

# 56. Circuit Breaker Pattern

A Circuit Breaker prevents repeated calls to a dependency that is failing.

It has three main states:

1. Closed.
2. Open.
3. Half-Open.

### Closed State

Requests are allowed.

```text
Application
    |
    v
External Service
```

### Open State

The dependency is failing.

New requests are rejected or handled through a fallback without repeatedly calling the failing service.

```text
Application
    |
    v
Circuit Breaker
    |
    v
Request Rejected / Fallback
```

### Half-Open State

After a recovery interval, a limited number of test requests are allowed.

If successful, the circuit returns to Closed.

If failures continue, it returns to Open.

### Architecture

```text
             CLOSED
                |
          Failures Exceed
             Threshold
                |
                v
              OPEN
                |
          Wait Recovery Time
                |
                v
           HALF-OPEN
             /     \
            /       \
       Success     Failure
          |           |
          v           v
        CLOSED       OPEN
```

### Why Use Circuit Breakers?

- Prevent cascading failures.
- Reduce pressure on failing services.
- Avoid wasting resources.
- Allow recovery.
- Improve system resilience.

---

# 57. Bulkhead Pattern

The Bulkhead Pattern isolates resources so that failure in one area does not exhaust resources in another.

The name comes from ship compartments designed to limit flooding.

Example:

Suppose your application calls three external services:

- Payment.
- Email.
- Recommendations.

Without isolation, one slow dependency may consume all available request-handling resources.

With bulkheads:

```text
Application
    |
    +---------+---------+
    |         |         |
    v         v         v
Payment    Email    Recommendation
Pool       Pool       Pool
```

Each dependency has isolated capacity.

A failure in one pool is less likely to affect the others.

---

# 58. Idempotency

An operation is idempotent when repeating the same operation produces the same intended result as performing it once.

Example:

Setting a user's name to `"Riyaz"` repeatedly produces the same final state.

But creating a payment repeatedly may charge the user multiple times if the operation is not protected.

### Example Problem

```text
Client Sends Payment Request
          |
          v
Payment Service Processes Payment
          |
          v
Response Gets Lost
          |
          v
Client Retries
          |
          v
Second Payment Created
```

This can result in duplicate charges.

### Solution: Idempotency Key

The client sends a unique key:

```http
Idempotency-Key: 8f3a2c-example
```

The server stores the key with the operation result.

If the same key is submitted again, the server returns the existing result instead of creating another payment.

### Architecture

```text
Client
  |
  v
Payment Request
  |
  v
Check Idempotency Key
  |
  +---- Exists ----> Return Previous Result
  |
  +---- New -------> Process Payment
                       |
                       v
                 Store Result
                       |
                       v
                  Return Result
```

### Important

Idempotency is especially important for:

- Payments.
- Order creation.
- Financial transfers.
- Booking systems.
- Message processing.

---

# PART 12: DISTRIBUTED TRANSACTIONS

# 59. What Is a Distributed Transaction?

A Distributed Transaction involves operations across multiple services or databases.

Suppose a user places an order.

The system must:

1. Create the order.
2. Reserve inventory.
3. Process payment.
4. Confirm the order.

These operations may involve separate services and databases.

### Architecture

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
Notification Service
```

The challenge is ensuring that the business process reaches a valid final state even when one operation fails.

---

# 60. What Is the Saga Pattern?

The Saga Pattern manages a distributed business transaction through a sequence of local transactions.

If a step fails, the system executes compensating actions where possible.

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
Confirm Order
```

Suppose payment fails.

The system may execute:

```text
Payment Failed
      |
      v
Release Inventory
      |
      v
Cancel Order
```

These are compensating transactions.

### Saga Architecture

```text
Transaction Steps
       |
       v
Create Order
       |
       v
Reserve Stock
       |
       v
Process Payment
       |
       +------ Success ------> Confirm Order
       |
       +------ Failure ------> Compensate
                                  |
                                  v
                            Release Stock
                                  |
                                  v
                             Cancel Order
```

### Two Saga Coordination Styles

#### 1. Choreography

Services communicate through events.

Each service reacts to events and publishes new events.

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
StockReserved Event
      |
      v
Payment Service
```

#### 2. Orchestration

A central orchestrator controls the workflow.

```text
           Saga Orchestrator
                   |
         +---------+---------+
         |         |         |
         v         v         v
       Order   Inventory  Payment
      Service   Service   Service
```

### Comparison

| Feature | Choreography | Orchestration |
|---|---|---|
| Coordination | Event-driven | Central coordinator |
| Coupling | Services react to events | Services communicate with orchestrator |
| Visibility | Can become difficult in large workflows | Centralized workflow visibility |
| Complexity | Distributed logic | Orchestrator complexity |
| Best Use | Simpler event-driven workflows | Complex business processes |

### Important

Compensation is not the same as rolling back a database transaction.

For example, a completed payment may require a refund rather than simply deleting the payment record.

---

# PART 13: OBSERVABILITY

# 61. What Is Observability?

Observability is the ability to understand a system's internal state by examining its outputs.

It helps engineers answer:

- Why is the application slow?
- Which service is failing?
- Why are requests returning errors?
- Where is latency occurring?
- Which dependency is causing problems?

Three major pillars:

1. Logs.
2. Metrics.
3. Traces.

---

# 62. Logging

Logs are timestamped records of events that occur in an application.

Example:

```text
2026-11-25 10:30:00 INFO User logged in
2026-11-25 10:31:00 ERROR Database connection failed
2026-11-25 10:32:00 WARN Payment service timeout
```

### Common Log Levels

| Level | Purpose |
|---|---|
| DEBUG | Detailed diagnostic information |
| INFO | Normal application events |
| WARN | Unexpected but recoverable conditions |
| ERROR | Application errors |
| FATAL | Critical failures requiring immediate attention |

### Structured Logging

Instead of plain text:

```javascript
console.log("User logged in");
```

Use structured logs:

```json
{
  "level": "info",
  "event": "user_login",
  "userId": 101,
  "timestamp": "2026-11-25T10:30:00Z"
}
```

Structured logs are easier to search, filter and analyze.

Never log passwords, session tokens or other sensitive credentials.

---

# 63. Metrics

Metrics are numerical measurements collected over time.

Examples:

- CPU usage.
- Memory usage.
- Request count.
- Error rate.
- API latency.
- Database connections.
- Queue depth.

Example:

```text
HTTP Requests: 10,000
Errors: 100
Error Rate: 1%
```

### Common Metrics

| Metric | Purpose |
|---|---|
| CPU Usage | Measures processor utilization |
| Memory Usage | Measures memory consumption |
| Request Rate | Measures incoming traffic |
| Error Rate | Measures failed requests |
| Latency | Measures response time |
| Queue Depth | Measures pending messages |
| Database Connections | Measures database connection usage |

---

# 64. Distributed Tracing

Distributed Tracing follows a request as it moves through multiple services.

Suppose a user places an order.

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
Inventory Service
   |
   v
Database
```

A distributed trace records the request's path through these components.

### Trace Example

```text
Total Request: 900 ms

API Gateway:      50 ms
Order Service:   100 ms
Payment Service: 500 ms
Inventory:       150 ms
Database:        100 ms
```

This helps identify that Payment Service is the main source of latency.

### Important Concepts

| Concept | Meaning |
|---|---|
| Trace | End-to-end request journey |
| Span | Individual operation within a trace |
| Trace ID | Identifier for the complete trace |
| Span ID | Identifier for one operation |
| Context Propagation | Passing tracing information between services |

### OpenTelemetry

OpenTelemetry is a framework for generating, collecting and exporting telemetry data.

Official documentation: [OpenTelemetry](https://opentelemetry.io/docs/)

---

# 65. Logs vs Metrics vs Traces

| Feature | Logs | Metrics | Traces |
|---|---|---|---|
| Purpose | Record events | Measure system behavior | Track request journeys |
| Example | Database connection failed | CPU usage = 80% | Request spent 500 ms in payment service |
| Data | Event records | Numerical measurements | Connected spans |
| Best Use | Debugging individual events | Monitoring trends and alerts | Diagnosing distributed request latency |

All three are important for production observability.

---

# PART 14: SYSTEM DESIGN CASE STUDIES

# 66. Case Study 1 – Design a URL Shortener

A URL Shortener converts long URLs into short URLs.

Example:

```text
Original URL:

https://example.com/products/category/mobile/phone/123456
```

Short URL:

```text
https://short.example/abc123
```

When a user visits the short URL, the system redirects them to the original URL.

## 66.1 Functional Requirements

- Create short URLs.
- Redirect short URLs.
- Support custom aliases.
- Track click counts.
- Support expiration dates.
- Allow users to manage their links.

## 66.2 Non-Functional Requirements

- Low redirect latency.
- High availability.
- Scalability.
- Collision prevention.
- Reliable URL storage.

## 66.3 High-Level Architecture

```text
             User
              |
              v
          Load Balancer
              |
              v
          URL Service
              |
        +-----+-----+
        |           |
        v           v
      Redis      Database
        |
        v
   Cached URL
```

## 66.4 Database Schema

```sql
CREATE TABLE urls (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    short_code VARCHAR(20) NOT NULL UNIQUE,
    original_url TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NULL
);
```

## 66.5 Short Code Generation

You can generate a unique identifier and encode it using Base62.

Base62 uses:

```text
0-9
a-z
A-Z
```

Example:

```text
ID: 125
Base62: 21
```

The exact mapping depends on the chosen encoding implementation.

### Important

A random or encoded identifier must be checked for collisions before being stored.

A database unique constraint should enforce uniqueness.

## 66.6 Redirect Flow

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
  Hit       Miss
   |         |
   |         v
   |      Database
   |         |
   +----+----+
        |
        v
   Original URL
        |
        v
   HTTP Redirect
```

For permanent redirects, HTTP 301 may be appropriate.

For temporary redirects, HTTP 302 or 307 may be appropriate depending on the semantics required.

---

# 67. Case Study 2 – Design an E-commerce System

### Functional Requirements

- User registration.
- Product listing.
- Product search.
- Shopping cart.
- Order creation.
- Payment processing.
- Inventory management.
- Order tracking.
- Notifications.

### High-Level Architecture

```text
                       Users
                         |
                         v
                    CDN / WAF
                         |
                         v
                    API Gateway
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      User Service  Product Service  Order Service
          |              |              |
          v              v              v
        User DB       Product DB       Order DB
                                         |
                               +---------+---------+
                               |                   |
                               v                   v
                         Payment Service      Inventory Service
                               |                   |
                               v                   v
                          Payment DB          Inventory DB
                               |
                               v
                         Message Broker
                               |
                         +-----+-----+
                         |           |
                         v           v
                       Email      Analytics
                       Worker     Service
```

### Important Design Decisions

- Product browsing can use caching.
- Search may use a dedicated search engine at scale.
- Orders should be persisted transactionally.
- Payment operations require idempotency.
- Inventory updates require concurrency control.
- Notifications can be asynchronous.
- Order workflows may use the Saga pattern.

---

# 68. Case Study 3 – Design a Chat Application

### Functional Requirements

- User registration.
- One-to-one messaging.
- Group messaging.
- Message history.
- Online status.
- Message delivery status.
- File sharing.
- Notifications.

### Architecture

```text
                  Clients
                     |
                     v
               Load Balancer
                     |
                     v
              WebSocket Servers
                     |
           +---------+---------+
           |                   |
           v                   v
       Message Service      Presence Service
           |                   |
           v                   v
        Message DB            Redis
           |
           v
       Message Broker
           |
      +----+----+
      |         |
      v         v
 Notifications  Workers
```

### Why WebSockets?

WebSockets provide a persistent, bidirectional communication channel between a client and server.

This is useful for real-time messaging.

### Why Redis?

Redis can be used for:

- Online user presence.
- Temporary session information.
- Pub/Sub messaging.
- Caching.
- Rate limiting.

### Why a Message Broker?

A broker can decouple message persistence, notification delivery and other background operations.

### Important Challenges

- Message ordering.
- Duplicate message delivery.
- Reconnection.
- Offline message delivery.
- Multi-device synchronization.
- Group message fan-out.
- Message durability.

---

# 69. Case Study 4 – Design a Video Streaming Platform

### Functional Requirements

- User registration.
- Video upload.
- Video transcoding.
- Video playback.
- Search.
- Recommendations.
- Watch history.

### Architecture

```text
                   Users
                     |
                     v
                    CDN
                     |
                     v
               Application API
                     |
            +--------+--------+
            |                 |
            v                 v
        User Service       Video Service
                                |
                                v
                         Object Storage
                                |
                                v
                          Message Queue
                                |
                                v
                       Transcoding Workers
                                |
                                v
                        Encoded Video Files
                                |
                                v
                               CDN
```

### Important Concepts

- Object storage.
- Video transcoding.
- Adaptive bitrate streaming.
- CDN.
- Background processing.
- Metadata database.
- Search indexing.

Video files should generally be stored in object storage rather than directly inside the application server's local filesystem.

---

# PART 15: DESIGNING A SCALABLE FULL-STACK APPLICATION

# 70. Example – Scalable Expense Tracker

Let's design an expense tracker using technologies you have studied.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + Express |
| Database | MySQL |
| Cache | Redis |
| Authentication | Secure Sessions or JWT |
| Background Jobs | RabbitMQ |
| Reverse Proxy | Nginx |
| Containerization | Docker |
| Deployment | AWS |
| Monitoring | Prometheus + Grafana |

### Architecture

```text
                       Users
                         |
                         v
                        CDN
                         |
                         v
                   Load Balancer
                         |
                         v
                    Next.js App
                         |
                         v
                    API Gateway
                         |
              +----------+----------+
              |          |          |
              v          v          v
           Auth API   Expense API  Report API
              |          |          |
              +----------+----------+
                         |
                  +------+------+
                  |             |
                  v             v
                Redis         MySQL
                  |             |
                  |             v
                  |         Read Replica
                  |
                  v
             Cache Results

Expense API
    |
    v
Message Queue
    |
    v
Background Worker
    |
    v
Generate Reports / Notifications
```

### Scaling Strategy

#### Frontend

- CDN caching.
- Static asset optimization.
- Next.js rendering strategy.
- Horizontal scaling when needed.

#### Backend

- Stateless application instances.
- Load balancing.
- Horizontal scaling.
- Request validation.
- Rate limiting.

#### Database

- Proper indexes.
- Query optimization.
- Connection pooling.
- Read replicas where appropriate.

#### Caching

- Redis for frequently accessed data.
- TTL.
- Cache invalidation.

#### Background Jobs

- Queue-based report generation.
- Asynchronous email delivery.
- Retry mechanisms.

#### Monitoring

- API latency.
- Error rate.
- Database performance.
- Redis memory.
- Queue depth.

---

# PART 16: COMMON SYSTEM DESIGN MISTAKES

# 71. Mistakes to Avoid

| Mistake | Problem | Better Approach |
|---|---|---|
| Starting with microservices unnecessarily | Increases operational complexity | Begin with a well-structured modular monolith when appropriate |
| No caching strategy | Repeated database queries | Cache suitable frequently accessed data |
| Poor database indexing | Slow queries | Analyze query patterns and execution plans |
| One server for everything | Single point of failure and scaling limits | Add redundancy when required |
| No timeouts | Requests may wait indefinitely | Set dependency-specific timeouts |
| Unlimited retries | Can overload failing services | Use bounded retries with backoff and jitter |
| No idempotency | Duplicate financial operations | Use idempotency keys |
| Ignoring replication lag | Stale reads | Design read-after-write behavior |
| Poor cache invalidation | Stale data | Define invalidation and TTL policies |
| Synchronous background work | Slow API responses | Use queues for suitable workloads |
| No observability | Difficult troubleshooting | Implement logs, metrics and traces |
| Overengineering | Higher cost and complexity | Design according to actual requirements |
| No failure planning | Cascading failures | Implement resilience patterns |
| No backup testing | Recovery may fail | Regularly test restoration |

---

# PART 17: INTERVIEW QUESTIONS AND ANSWERS

# 72. Basic Interview Questions

### Q1. What is System Design?

System Design is the process of defining the architecture, components, databases, APIs and infrastructure required to build a software system.

### Q2. What is HLD?

High-Level Design describes the overall system architecture and its major components.

### Q3. What is LLD?

Low-Level Design describes the internal implementation of components, including classes, functions and database schemas.

### Q4. What is scalability?

Scalability is the ability of a system to handle increasing workload by increasing resources or improving efficiency.

### Q5. What is vertical scaling?

Vertical scaling increases the resources of an existing machine.

### Q6. What is horizontal scaling?

Horizontal scaling adds more machines or application instances.

### Q7. What is latency?

Latency is the time taken to complete an operation or request.

### Q8. What is throughput?

Throughput is the amount of work completed per unit of time.

### Q9. What is availability?

Availability measures how often a system is operational and accessible.

### Q10. What is reliability?

Reliability is the ability of a system to perform its intended function consistently.

---

# 73. Intermediate Interview Questions

### Q11. What is the difference between a monolith and microservices?

A monolith is generally deployed as one application, whereas microservices consist of independently deployable services communicating through network interfaces or messaging.

### Q12. What is a modular monolith?

It is a single deployable application with clearly separated internal modules.

### Q13. What is load balancing?

Load balancing distributes traffic across multiple servers or instances.

### Q14. What is a reverse proxy?

A reverse proxy receives client requests and forwards them to backend servers.

### Q15. What is an API Gateway?

An API Gateway provides a centralized entry point for accessing backend APIs and services.

### Q16. What is database replication?

Replication maintains copies of data across multiple database instances.

### Q17. What is database sharding?

Sharding divides a dataset across multiple database instances.

### Q18. What is Redis?

Redis is an in-memory data store commonly used for caching, session storage and other low-latency data operations.

### Q19. What is a message queue?

A message queue allows components to communicate asynchronously by publishing and consuming messages.

### Q20. What is CDN?

A Content Delivery Network distributes content through geographically distributed edge locations.

---

# 74. Advanced Interview Questions

### Q21. What is the CAP theorem?

The CAP theorem describes the trade-off between consistency and availability when a network partition occurs in a distributed system.

### Q22. What is eventual consistency?

Eventual consistency means that replicas may temporarily contain different values but converge if updates stop and synchronization succeeds.

### Q23. What is a circuit breaker?

A circuit breaker prevents repeated calls to a failing dependency and allows the dependency time to recover.

### Q24. What is the Bulkhead Pattern?

It isolates resources so that a failure in one component does not exhaust resources required by other components.

### Q25. What is idempotency?

An operation is idempotent when repeating it produces the same intended result as executing it once.

### Q26. What is the Saga Pattern?

Saga manages a distributed transaction through a sequence of local transactions and compensating actions.

### Q27. What is distributed tracing?

Distributed tracing tracks a request as it moves across multiple services.

### Q28. What is the difference between Kafka and RabbitMQ?

Kafka is primarily a distributed event-streaming platform, whereas RabbitMQ is a message broker supporting queue-based messaging and flexible routing.

### Q29. What is cache invalidation?

Cache invalidation is the process of removing or updating cached data when the original data changes.

### Q30. What is graceful degradation?

Graceful degradation allows essential application functionality to continue when optional components fail.

---

# 75. Scenario-Based Interview Questions

### Q31. Your application is receiving 100,000 requests per second. How would you scale it?

I would:

1. Analyze traffic patterns.
2. Identify application and database bottlenecks.
3. Add horizontal application scaling.
4. Introduce load balancing.
5. Cache frequently accessed data.
6. Optimize database queries.
7. Use database replication where appropriate.
8. Introduce asynchronous processing for suitable workloads.
9. Monitor latency, throughput and errors.

The architecture should be based on measured bottlenecks rather than adding infrastructure blindly.

### Q32. Your API response time has increased from 100 ms to 2 seconds. How would you investigate?

I would:

1. Inspect latency percentiles.
2. Review application logs.
3. Analyze distributed traces.
4. Check database query performance.
5. Inspect Redis hit rate.
6. Check external API dependencies.
7. Review CPU and memory usage.
8. Identify the slowest operation.

### Q33. Your database is overloaded with read queries. What would you do?

I would:

- Add appropriate indexes.
- Optimize queries.
- Introduce caching.
- Use read replicas where appropriate.
- Review connection pooling.
- Consider workload-specific database scaling.

### Q34. Your application server crashes frequently. How would you improve reliability?

I would:

- Investigate crashes.
- Add health checks.
- Run multiple instances.
- Configure load balancing.
- Add resource limits and monitoring.
- Implement automatic recovery.
- Remove single points of failure where justified.

### Q35. Your payment API sometimes processes the same transaction twice. How would you fix it?

I would implement idempotency keys, unique transaction constraints, transaction-state validation and appropriate database transaction handling.

### Q36. Your Redis cache contains outdated data. What would you do?

I would review TTL policies, cache invalidation, write-through or cache-aside behavior and the possibility of concurrent updates.

### Q37. One microservice is failing and causing the entire application to slow down. How would you fix it?

I would consider:

- Timeouts.
- Circuit breakers.
- Bulkheads.
- Bounded retries.
- Graceful degradation.
- Asynchronous processing.

### Q38. Your application needs to process 1 million emails. Should the API send all emails synchronously?

No. I would publish email jobs to a queue and process them using scalable background workers.

### Q39. Your system needs high availability. What would you implement?

I would consider:

- Multiple application instances.
- Load balancing.
- Multi-AZ infrastructure.
- Database failover.
- Backups.
- Health checks.
- Monitoring.
- Disaster recovery testing.

### Q40. When would you migrate a monolith to microservices?

I would consider migration when clear business, scaling, team ownership or independent deployment requirements justify the additional distributed-system complexity.

---

# PART 18: PRACTICAL ASSIGNMENTS

# 76. Assignment 1 – Design an Expense Tracker

Design a system with:

- User registration.
- Login.
- Expense creation.
- Expense updates.
- Monthly reports.
- Data export.

Prepare:

1. Functional requirements.
2. Non-functional requirements.
3. HLD diagram.
4. Database schema.
5. API list.
6. LLD for the Expense module.

**Expected outcome:** Understand the complete system design process.

---

# 77. Assignment 2 – Monolith vs Modular Monolith

Create a Node.js application.

Organize it into:

```text
auth/
expenses/
reports/
users/
```

Ensure modules have clearly defined responsibilities.

**Expected outcome:** Understand modular application design.

---

# 78. Assignment 3 – Horizontal Scaling

Deploy a Node.js application using multiple instances.

Configure Nginx or a suitable load balancer to distribute traffic.

Test whether the application continues to respond when one instance fails.

**Expected outcome:** Understand horizontal scaling and traffic distribution.

---

# 79. Assignment 4 – Redis Caching

Create a product API.

Implement:

- Cache-aside.
- TTL.
- Cache invalidation.
- Cache hit/miss tracking.

**Expected outcome:** Understand practical caching.

---

# 80. Assignment 5 – Database Replication

Study MySQL replication.

Understand:

- Primary database.
- Read replica.
- Replication lag.
- Read/write routing.
- Failover.

**Expected outcome:** Understand database replication architecture.

---

# 81. Assignment 6 – Database Sharding Design

Design a user-management system with 10 million users.

Explore:

- Range-based sharding.
- Hash-based sharding.
- Geographic sharding.
- Shard routing.
- Rebalancing.

You do not need to deploy 10 million records to complete this assignment.

**Expected outcome:** Understand data partitioning.

---

# 82. Assignment 7 – Message Queue

Create a Node.js application using RabbitMQ.

Requirements:

- Producer.
- Queue.
- Consumer.
- Message acknowledgment.
- Retry handling.
- Dead-letter queue.

**Expected outcome:** Understand asynchronous processing.

---

# 83. Assignment 8 – Kafka Event Streaming

Create a simple event system.

Requirements:

- Producer.
- Topic.
- Partitions.
- Consumer group.
- Offset management.

**Expected outcome:** Understand event streaming.

---

# 84. Assignment 9 – Circuit Breaker

Create a Node.js application that calls an external service.

Implement:

- Timeout.
- Retry.
- Circuit breaker.
- Fallback response.

**Expected outcome:** Understand resilience patterns.

---

# 85. Assignment 10 – Distributed Transaction

Design an e-commerce order workflow.

Requirements:

1. Create order.
2. Reserve inventory.
3. Process payment.
4. Confirm order.
5. Compensate when payment fails.

**Expected outcome:** Understand the Saga Pattern.

---

# 86. Assignment 11 – Observability

Integrate:

- Structured logging.
- Metrics.
- Distributed tracing.

Use suitable tooling such as OpenTelemetry, Prometheus and Grafana.

**Expected outcome:** Understand production monitoring.

---

# 87. Assignment 12 – Complete System Design Project

Design a scalable e-commerce application.

Technology stack:

```text
Next.js
Node.js
Express
MySQL
Redis
RabbitMQ
Docker
Kubernetes
AWS
Nginx
Prometheus
Grafana
```

Requirements:

- HLD.
- LLD.
- API design.
- Database design.
- Horizontal scaling.
- Caching.
- Message queues.
- Load balancing.
- Fault tolerance.
- Observability.
- Security.
- Deployment architecture.

**Expected outcome:** Design a production-oriented full-stack system.

---

# PART 19: QUICK REVISION

# 88. System Design Concepts at a Glance

| Concept | Meaning |
|---|---|
| System Design | Designing complete software architecture |
| HLD | High-Level Design |
| LLD | Low-Level Design |
| Functional Requirements | What the system does |
| Non-Functional Requirements | How well the system operates |
| Scalability | Ability to handle increasing workload |
| Vertical Scaling | Increasing resources on one machine |
| Horizontal Scaling | Adding more machines |
| Latency | Time taken by an operation |
| Throughput | Work completed per unit of time |
| Availability | Operational accessibility |
| Reliability | Consistent correct operation |
| Durability | Preserving stored data |
| SLI | Service Level Indicator |
| SLO | Service Level Objective |
| SLA | Service Level Agreement |
| Monolith | One deployable application |
| Modular Monolith | One deployable application with clear modules |
| Microservices | Independently deployable services |
| SOA | Service-Oriented Architecture |
| Load Balancer | Distributes traffic |
| Reverse Proxy | Forwards client requests to backend servers |
| API Gateway | Centralized API entry point |
| Replication | Maintaining data copies |
| Sharding | Dividing data across databases |
| Redis | In-memory data store |
| CDN | Content Delivery Network |
| RabbitMQ | Message broker |
| Kafka | Distributed event-streaming platform |
| CAP | Consistency, Availability, Partition Tolerance |
| Circuit Breaker | Prevents repeated calls to failing dependencies |
| Bulkhead | Isolates resources |
| Idempotency | Repeating an operation produces the same intended result |
| Saga | Distributed transaction pattern |
| Observability | Understanding system behavior through telemetry |

---

# 89. Important Architecture Patterns at a Glance

| Pattern | Purpose |
|---|---|
| Load Balancing | Distribute traffic |
| Caching | Reduce repeated expensive operations |
| Database Replication | Improve read capacity and resilience |
| Database Sharding | Distribute data |
| Message Queue | Asynchronous communication |
| Event-Driven Architecture | Communicate through events |
| Circuit Breaker | Isolate failing dependencies |
| Retry | Recover from temporary failures |
| Timeout | Limit waiting time |
| Bulkhead | Isolate resource pools |
| Saga | Coordinate distributed transactions |
| CDN | Deliver content closer to users |
| API Gateway | Centralize API access |
| CQRS | Separate command and query models |
| Event Sourcing | Store changes as an event history |

---

# 90. Phase 49 Completion Checklist

### System Design Fundamentals

- [ ] System Design.
- [ ] HLD.
- [ ] LLD.
- [ ] Functional Requirements.
- [ ] Non-Functional Requirements.
- [ ] Scalability.
- [ ] Latency.
- [ ] Throughput.
- [ ] Availability.
- [ ] Reliability.
- [ ] Durability.
- [ ] SLI, SLO and SLA.

### Architecture

- [ ] Monolithic Architecture.
- [ ] Modular Monolith.
- [ ] Microservices.
- [ ] SOA.
- [ ] Client-Server Architecture.
- [ ] Stateless Applications.
- [ ] Stateful Applications.

### Infrastructure

- [ ] Load Balancing.
- [ ] Reverse Proxy.
- [ ] API Gateway.
- [ ] CDN.
- [ ] Horizontal Scaling.
- [ ] Vertical Scaling.

### Database Architecture

- [ ] SQL vs NoSQL.
- [ ] Database Indexing.
- [ ] Replication.
- [ ] Sharding.
- [ ] Read Replicas.
- [ ] Replication Lag.

### Caching and Messaging

- [ ] Redis.
- [ ] Cache-Aside.
- [ ] Write-Through.
- [ ] Write-Behind.
- [ ] Cache Invalidation.
- [ ] Cache Stampede.
- [ ] Message Queues.
- [ ] RabbitMQ.
- [ ] Kafka.
- [ ] Event-Driven Architecture.

### Distributed Systems

- [ ] CAP Theorem.
- [ ] Consistency Models.
- [ ] Network Partitions.
- [ ] Fault Tolerance.
- [ ] Redundancy.
- [ ] Failover.
- [ ] Graceful Degradation.

### Resilience Patterns

- [ ] Timeout.
- [ ] Retry.
- [ ] Exponential Backoff.
- [ ] Circuit Breaker.
- [ ] Bulkhead.
- [ ] Idempotency.
- [ ] Saga Pattern.

### Observability

- [ ] Logging.
- [ ] Metrics.
- [ ] Distributed Tracing.
- [ ] OpenTelemetry.
- [ ] Monitoring.

### System Design Practice

- [ ] URL Shortener.
- [ ] E-commerce System.
- [ ] Chat Application.
- [ ] Video Streaming Platform.
- [ ] Scalable Expense Tracker.

---

# 91. Official Documentation and Learning Resources

| Topic | Resource |
|---|---|
| AWS Architecture | [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html?utm_source=chatgpt.com) |
| Google Cloud Architecture | [Google Cloud Well-Architected Framework](https://docs.cloud.google.com/architecture/framework?utm_source=chatgpt.com) |
| Microservices | [Martin Fowler – Microservices Guide](https://martinfowler.com/microservices/?utm_source=chatgpt.com) |
| Redis | [Redis Documentation](https://redis.io/docs/latest/?utm_source=chatgpt.com) |
| RabbitMQ | [RabbitMQ Documentation](https://www.rabbitmq.com/docs?utm_source=chatgpt.com) |
| Apache Kafka | [Apache Kafka Documentation](https://kafka.apache.org/documentation/?utm_source=chatgpt.com) |
| MySQL | [MySQL Documentation](https://dev.mysql.com/doc/?utm_source=chatgpt.com) |
| MongoDB | [MongoDB Documentation](https://www.mongodb.com/docs/?utm_source=chatgpt.com) |
| Nginx | [Nginx Documentation](https://nginx.org/en/docs/?utm_source=chatgpt.com) |
| Kubernetes | [Kubernetes Documentation](https://kubernetes.io/docs/home/?utm_source=chatgpt.com) |
| OpenTelemetry | [OpenTelemetry Documentation](https://opentelemetry.io/docs/?utm_source=chatgpt.com) |
| Prometheus | [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/?utm_source=chatgpt.com) |
| Grafana | [Grafana Documentation](https://grafana.com/docs/?utm_source=chatgpt.com) |

---

# 92. Final Summary

In Phase 49, you learned how to design complete software systems rather than focusing only on individual application components.

The central principle is:

**A good system architecture balances scalability, performance, reliability, maintainability, security and cost according to actual business requirements.**

### Complete System Architecture

```text
                         Users
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
              +------------+------------+
              |            |            |
              v            v            v
          Auth API     Product API   Order API
              |            |            |
              +------------+------------+
                           |
                  +--------+--------+
                  |                 |
                  v                 v
                Redis            Database
                                    |
                            +-------+-------+
                            |               |
                            v               v
                         Primary        Replicas
                            |
                            v
                       Message Broker
                            |
                  +---------+---------+
                  |                   |
                  v                   v
             Background           Analytics
              Workers              Service

                           |
                           v
                      Monitoring
                           |
                           v
                       Alerting
```

### Most Important Topics to Master

Prioritize these:

1. HLD and LLD.
2. Functional and non-functional requirements.
3. Scalability.
4. Latency and throughput.
5. Availability and reliability.
6. Monolithic vs microservices architecture.
7. Load balancing.
8. API Gateway and reverse proxy.
9. Database indexing, replication and sharding.
10. Redis caching.
11. RabbitMQ and Kafka.
12. CDN.
13. Distributed systems.
14. CAP theorem.
15. Circuit breaker and retry patterns.
16. Idempotency.
17. Saga Pattern.
18. Observability.
19. Real-world system design case studies.

---

# Next Phase Preview

## Phase 50 – Advanced Backend Architecture, Design Patterns and Clean Architecture

The next phase will cover:

1. What Is Clean Architecture?
2. Layered Architecture.
3. Hexagonal Architecture.
4. Onion Architecture.
5. Domain-Driven Design (DDD).
6. SOLID Principles.
7. Single Responsibility Principle.
8. Open/Closed Principle.
9. Liskov Substitution Principle.
10. Interface Segregation Principle.
11. Dependency Inversion Principle.
12. Repository Pattern.
13. Service Layer Pattern.
14. Factory Pattern.
15. Singleton Pattern.
16. Strategy Pattern.
17. Observer Pattern.
18. Adapter Pattern.
19. Decorator Pattern.
20. Dependency Injection.
21. Inversion of Control.
22. DTO and Data Mapping.
23. Domain Models.
24. Use Cases and Application Services.
25. CQRS.
26. Event Sourcing.
27. Transaction Management.
28. Unit of Work Pattern.
29. Modular Backend Architecture.
30. Testing Architecture.
31. Maintainable Node.js Project Structure.
32. Scalable Express Application Architecture.
33. Production-Ready Backend Project.

**Phase 50 will focus on writing maintainable, testable and extensible backend applications using professional software architecture principles and design patterns.**