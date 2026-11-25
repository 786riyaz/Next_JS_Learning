# Phase 53 – Microservices Architecture, Distributed Systems and Inter-Service Communication

**Level:** Advanced  
**Category:** Backend Engineering / Distributed Systems / Software Architecture  
**Primary Stack:** Node.js, Express.js, TypeScript  
**Additional Technologies:** Docker, RabbitMQ, Apache Kafka, gRPC, Redis, API Gateway, OpenTelemetry

---

# 1. Learning Objectives

In this phase, you will learn how to design, develop, communicate between, deploy and monitor microservices-based applications.

By the end of this phase, you should understand:

1. Monolithic vs Microservices Architecture.
2. Microservices fundamentals.
3. Service decomposition strategies.
4. Domain-Driven Design in Microservices.
5. Database per Service pattern.
6. Inter-service communication.
7. Synchronous vs asynchronous communication.
8. REST-based service communication.
9. gRPC architecture.
10. Message brokers.
11. RabbitMQ.
12. Apache Kafka.
13. Event-Driven Architecture.
14. Eventual consistency.
15. Service discovery.
16. API Gateway architecture.
17. Centralized configuration.
18. Distributed logging.
19. Distributed tracing.
20. Circuit Breaker pattern.
21. Retry and Timeout patterns.
22. Bulkhead pattern.
23. Saga pattern.
24. Transactional Outbox pattern.
25. Idempotent message processing.
26. Microservices security.
27. Docker-based microservices.
28. Microservices deployment.
29. Monitoring and observability.
30. Production-oriented microservices architecture.

---

# PART 1: MONOLITHIC VS MICROSERVICES ARCHITECTURE

## 2. What Is a Monolithic Architecture?

A monolithic architecture is an application architecture in which multiple application modules are developed and deployed as one application.

For example, an e-commerce application may contain:

- Authentication.
- User Management.
- Product Management.
- Order Management.
- Payment Management.
- Inventory Management.
- Notification Management.

In a monolithic application, these modules typically run within one deployable application.

### Monolithic Architecture

```text
             Client
               |
               v
        Backend Application
               |
    ---------------------------
    |       |        |        |
    v       v        v        v
   Auth   Products  Orders  Payments
    |       |        |        |
    ---------------------------
               |
               v
            Database
```

### Example Project Structure

```text
ecommerce/
│
├── src/
│   ├── modules/
│   │   ├── auth/
│   │   ├── users/
│   │   ├── products/
│   │   ├── orders/
│   │   ├── payments/
│   │   └── inventory/
│   │
│   ├── middleware/
│   ├── config/
│   ├── utils/
│   └── app.ts
│
└── package.json
```

All modules belong to one application.

### Advantages

1. Simple initial development.
2. Easier local debugging.
3. Straightforward deployment.
4. Simpler communication between modules.
5. Database transactions can be easier to manage.
6. Lower infrastructure complexity.

### Disadvantages

1. Application size can become large.
2. Deployment affects the entire application.
3. Scaling individual modules independently is difficult.
4. One module's resource consumption can affect others.
5. Large teams can experience development coordination problems.
6. Poor module boundaries can create tight coupling.

**Important:** A monolithic application is not necessarily badly designed. A well-structured modular monolith can be an excellent architecture for many applications.

---

## 3. What Is Microservices Architecture?

Microservices Architecture is an architectural approach in which an application is divided into smaller, independently deployable services.

Each service owns a specific business capability.

For example:

```text
                  Client
                    |
                    v
                API Gateway
                    |
       -----------------------------
       |        |        |         |
       v        v        v         v
     Auth    Product   Order    Payment
    Service  Service  Service   Service
       |        |        |         |
       v        v        v         v
     Auth DB Product DB Order DB Payment DB
```

Each service:

- Has a defined responsibility.
- Exposes an interface.
- Can be deployed independently.
- Can be scaled independently.
- Owns its business logic.
- Ideally owns its data.

### Example

Suppose an e-commerce application has four services.

| Service | Responsibility |
|---|---|
| Auth Service | Registration, login and authentication |
| Product Service | Product information and catalog |
| Order Service | Order creation and management |
| Payment Service | Payment processing and payment status |

The Order Service does not directly execute SQL queries against the Payment Service's database.

Instead, it communicates through an API or messaging mechanism.

### Advantages

1. Independent deployment.
2. Independent scaling.
3. Better isolation of business responsibilities.
4. Different services can use suitable technologies.
5. Teams can work independently.
6. Failures can be isolated with proper resilience mechanisms.

### Disadvantages

1. More infrastructure complexity.
2. Network communication introduces latency and failures.
3. Distributed transactions become difficult.
4. Monitoring becomes more complicated.
5. Testing requires additional infrastructure.
6. Data consistency becomes more challenging.
7. Deployment and operational costs increase.

---

## 4. Monolithic vs Microservices

| Feature | Monolithic | Microservices |
|---|---|---|
| Architecture | Single deployable application | Multiple independent services |
| Deployment | Usually together | Independently |
| Scaling | Usually application-level | Service-level |
| Communication | Mostly in-process | Network-based |
| Database | Often shared | Ideally service-owned |
| Complexity | Lower initially | Higher |
| Fault Isolation | More limited | Can be stronger |
| Team Structure | Often centralized | Can be organized by service |
| Technology | Usually more standardized | Can vary by service |
| Testing | Often simpler initially | More distributed testing |
| Infrastructure | Relatively simple | More complex |

### When Should You Use a Monolith?

Use a monolith when:

- You are building a small application.
- Your team is small.
- Business requirements are still changing.
- Independent deployment is not necessary.
- Infrastructure should remain simple.

### When Should You Consider Microservices?

Consider microservices when:

- Different business capabilities need independent scaling.
- Teams need independent deployment.
- Services have different reliability requirements.
- Business domains have clear boundaries.
- The organization can support distributed-system operations.

**Important recommendation:** Do not choose microservices simply because they are popular. Start with a modular monolith when appropriate and extract services when there is a measurable architectural or organizational reason.

---

# PART 2: MICROSERVICES FUNDAMENTALS

## 5. Characteristics of Microservices

A well-designed microservices system generally has the following characteristics.

### 5.1 Single Business Responsibility

Each service focuses on a particular business capability.

Example:

```text
Product Service
    |
    |-- Create Product
    |-- Update Product
    |-- Delete Product
    |-- Search Products
```

The Product Service should not become responsible for processing payments.

### 5.2 Independent Deployment

Suppose the Payment Service needs an update.

You should be able to deploy the Payment Service without deploying the entire application.

```text
Auth Service       Version 1.2
Product Service    Version 2.1
Order Service      Version 1.5
Payment Service    Version 3.0
```

Different services can have different release cycles.

### 5.3 Independent Scaling

Suppose your application receives:

```text
Product Requests = 10,000/minute
Order Requests   = 1,000/minute
Payment Requests = 200/minute
```

You may need more Product Service instances than Payment Service instances.

```text
                 Load Balancer
                       |
          ---------------------------
          |            |            |
          v            v            v
      Product 1    Product 2    Product 3
```

### 5.4 Service Autonomy

A service should control its own business logic and data contracts.

Other services should not depend on its internal classes, tables or implementation details.

### 5.5 Fault Isolation

If the Notification Service fails, the entire e-commerce system should not necessarily stop working.

You can use asynchronous messaging to process notifications later.

---

# PART 3: SERVICE DECOMPOSITION

## 6. What Is Service Decomposition?

Service decomposition is the process of dividing a large application into independently manageable services.

This is one of the most important microservices design decisions.

Poor decomposition creates tightly coupled services and distributed complexity.

### Example: E-commerce Application

Consider these business capabilities:

```text
E-commerce
    |
    |-- User Management
    |-- Product Catalog
    |-- Inventory
    |-- Orders
    |-- Payments
    |-- Shipping
    |-- Notifications
```

A possible service design:

```text
E-commerce System
       |
       |-- User Service
       |
       |-- Catalog Service
       |
       |-- Inventory Service
       |
       |-- Order Service
       |
       |-- Payment Service
       |
       |-- Shipping Service
       |
       |-- Notification Service
```

## 7. Decompose by Business Capability

A business capability represents something an organization does.

Examples:

- Managing customers.
- Managing products.
- Processing payments.
- Managing inventory.
- Handling shipping.

A service can be designed around one business capability.

This helps establish clear ownership.

## 8. Decompose by Domain and Subdomain

Domain-Driven Design provides another approach.

Suppose an e-commerce business has three subdomains:

```text
E-commerce Domain
       |
       |-- Catalog Subdomain
       |
       |-- Order Subdomain
       |
       |-- Payment Subdomain
```

Each subdomain represents a distinct part of the business domain.

You can design services around these boundaries.

### Service Decomposition Best Practices

1. Understand business responsibilities first.
2. Identify bounded contexts.
3. Avoid splitting services based only on database tables.
4. Minimize synchronous dependencies.
5. Give each service clear ownership.
6. Avoid unnecessarily small services.
7. Keep related business operations together.

### What Is a Distributed Monolith?

A distributed monolith is a system that has multiple services but still behaves like one tightly coupled application.

Example:

```text
Service A
   |
   v
Service B
   |
   v
Service C
   |
   v
Service D
```

Every request depends on all four services.

If Service D fails, the complete operation fails.

The system has distributed deployment but not meaningful service independence.

Avoid this architecture.

---

# PART 4: DOMAIN-DRIVEN DESIGN IN MICROSERVICES

## 9. What Is Domain-Driven Design?

Domain-Driven Design (DDD) is an approach to software development that focuses on understanding and modeling the business domain.

DDD is particularly useful when applications have complex business rules.

Important concepts include:

- Domain.
- Subdomain.
- Entity.
- Value Object.
- Aggregate.
- Aggregate Root.
- Domain Event.
- Repository.
- Bounded Context.
- Ubiquitous Language.

These concepts were introduced in Phase 50. Here, we will focus on how they help define microservice boundaries.

## 10. What Is a Bounded Context?

A Bounded Context defines the boundary within which a particular domain model and its terminology are valid.

Consider an e-commerce application.

The term `Product` may mean different things in different business contexts.

### Catalog Context

```text
Product:
- Product ID
- Name
- Description
- Images
- Brand
- Category
```

### Inventory Context

```text
Product Stock:
- Product ID
- Available Quantity
- Reserved Quantity
- Warehouse ID
```

### Order Context

```text
Ordered Product:
- Product ID
- Product Name Snapshot
- Unit Price
- Quantity
```

These models are related, but they are not necessarily the same entity.

### Bounded Context Architecture

```text
          E-commerce Domain
                 |
       ----------------------
       |         |          |
       v         v          v
    Catalog    Inventory   Orders
    Context    Context     Context
       |         |          |
       v         v          v
    Catalog    Inventory    Order
    Service    Service      Service
```

Each service can own its own model.

### Important Principle

Do not assume that every business concept must have one universal database representation across all services.

Different services may maintain different representations of related information.

---

# PART 5: DATABASE PER SERVICE

## 11. What Is the Database per Service Pattern?

In a microservices architecture, each service should own its data.

Example:

```text
                  API Gateway
                       |
          ---------------------------
          |            |            |
          v            v            v
       User         Product        Order
       Service      Service        Service
          |            |            |
          v            v            v
       User DB      Product DB     Order DB
```

The Order Service should not directly query the User Service's database.

Instead, it should obtain required information through an API or event-driven integration.

### Why Database per Service?

1. Independent data ownership.
2. Reduced coupling.
3. Independent schema evolution.
4. Independent database scaling.
5. Better domain boundaries.
6. Technology flexibility.

### Does Every Service Need a Separate Database Server?

No.

Database per Service means logical data ownership, not necessarily one physical database server per service.

For example, multiple services might initially use separate databases or schemas on one MySQL server.

However, they should not directly manipulate one another's private tables.

As the system grows, services can be moved to independent database instances.

### Database per Service vs Shared Database

| Shared Database | Database per Service |
|---|---|
| Multiple services access shared data | Each service owns its data |
| Easier cross-module SQL queries | Requires API or event communication |
| Greater schema coupling | Lower data coupling |
| Simpler initial infrastructure | More operational complexity |
| Shared database transactions are easier | Distributed consistency must be managed |

### How Do You Join Data Across Services?

Suppose an API needs:

- Order details.
- Customer name.
- Payment status.

You cannot simply execute a SQL JOIN across independent service databases.

Instead, consider:

1. API Composition.
2. Aggregation through an API Gateway or backend-for-frontend.
3. Materialized views.
4. CQRS read models.
5. Events that maintain denormalized data.

---

# PART 6: INTER-SERVICE COMMUNICATION

## 12. What Is Inter-Service Communication?

Inter-service communication is the process through which microservices exchange data and coordinate operations.

For example:

```text
Order Service
     |
     | Request Payment
     v
Payment Service
     |
     | Payment Result
     v
Order Service
```

There are two major communication styles:

1. Synchronous Communication.
2. Asynchronous Communication.

---

## 13. Synchronous Communication

In synchronous communication, one service sends a request and waits for a response.

Example:

```text
Order Service
     |
     | HTTP Request
     v
Payment Service
     |
     | HTTP Response
     v
Order Service
```

Common technologies:

- REST.
- gRPC.
- GraphQL in some service-to-service designs.

### Example

The Order Service asks the Inventory Service whether stock is available.

```http
GET /internal/inventory/products/100
```

Response:

```json
{
  "productId": 100,
  "availableQuantity": 20
}
```

The Order Service waits for this response before proceeding.

### Advantages

- Simple request-response model.
- Immediate result.
- Easy to understand.
- Suitable for operations requiring an immediate answer.

### Disadvantages

- Services become dependent on network availability.
- Latency increases across service calls.
- A slow downstream service can delay upstream services.
- Cascading failures are possible.

---

## 14. Asynchronous Communication

In asynchronous communication, a service sends a message or event without waiting for the receiving service to finish processing it.

A message broker often sits between services.

```text
Order Service
     |
     | Publish Event
     v
 Message Broker
     |
     | Deliver Event
     v
Notification Service
     |
     v
Send Email
```

The Order Service does not need to wait for the email to be sent.

### Advantages

1. Loose coupling.
2. Better handling of temporary service outages.
3. Independent processing.
4. Better support for background jobs.
5. Potentially higher throughput.

### Disadvantages

1. More complicated debugging.
2. Eventual consistency.
3. Duplicate message delivery.
4. Message ordering challenges.
5. Retry and dead-letter handling are required.

### Synchronous vs Asynchronous

| Feature | Synchronous | Asynchronous |
|---|---|---|
| Response | Waits for response | Does not require immediate response |
| Coupling | Often higher | Often lower |
| Latency | Depends on downstream services | Producer can continue sooner |
| Failure handling | Timeouts and retries | Redelivery and dead letters |
| Consistency | Immediate response can be obtained | Often eventual |
| Example | REST, gRPC | RabbitMQ, Kafka |

**Important:** Asynchronous does not automatically mean faster. It means the caller does not need to wait for the complete downstream operation.

---

# PART 7: REST-BASED SERVICE COMMUNICATION

## 15. REST Between Microservices

Microservices can communicate using HTTP REST APIs.

Example architecture:

```text
             API Gateway
                  |
                  v
             Order Service
                  |
                  | HTTP
                  v
            Inventory Service
```

### Inventory Service

```typescript
import express from "express";

const app = express();

app.use(express.json());

app.get("/internal/products/:id/stock", async (req, res) => {
  const productId = Number(req.params.id);

  const stock = await getStock(productId);

  res.json({
    productId,
    availableQuantity: stock
  });
});
```

### Order Service

```typescript
const response = await fetch(
  "http://inventory-service:3002/internal/products/100/stock",
  {
    signal: AbortSignal.timeout(3000)
  }
);

if (!response.ok) {
  throw new Error("Inventory service request failed");
}

const inventory = await response.json();
```

Here:

- The Order Service calls the Inventory Service.
- The request has a timeout.
- The Order Service handles unsuccessful responses.

### Important REST Communication Practices

1. Use timeouts.
2. Validate responses.
3. Authenticate service requests.
4. Avoid exposing internal endpoints publicly.
5. Use retries only for appropriate operations.
6. Propagate correlation and trace IDs.
7. Define API contracts.
8. Handle downstream failures.

### Internal vs Public APIs

Public API:

```text
https://api.example.com/products
```

Internal API:

```text
http://product-service:3002/internal/products
```

Internal services should still be protected by network policies and authentication.

Being internal does not automatically make a service trusted.

---

# PART 8: gRPC

## 16. What Is gRPC?

gRPC is a high-performance Remote Procedure Call framework.

It allows one application to invoke methods exposed by another application.

It commonly uses:

- HTTP/2.
- Protocol Buffers.
- Strongly defined service contracts.
- Generated client and server code.

Example:

```text
Order Service
     |
     | gRPC
     v
Inventory Service
```

Instead of manually defining REST request and response JSON structures, you define a service contract using Protocol Buffers.

## 17. What Is Protocol Buffers?

Protocol Buffers, often called Protobuf, is a language-neutral serialization format.

Example:

```protobuf
syntax = "proto3";

service InventoryService {
  rpc CheckStock (StockRequest) returns (StockResponse);
}

message StockRequest {
  int32 product_id = 1;
}

message StockResponse {
  int32 product_id = 1;
  int32 available_quantity = 2;
}
```

This defines:

- A service.
- An RPC method.
- Request message.
- Response message.

The client and server can generate code from this contract.

## 18. Types of gRPC Communication

gRPC supports four major RPC patterns.

| Type | Description |
|---|---|
| Unary RPC | One request and one response |
| Server Streaming | One request and multiple responses |
| Client Streaming | Multiple requests and one response |
| Bidirectional Streaming | Both sides exchange streams |

### Unary RPC

```text
Client
  |
  | Request
  v
Server
  |
  | Response
  v
Client
```

### Server Streaming

```text
Client
  |
  | Request
  v
Server
  |
  | Response 1
  | Response 2
  | Response 3
  v
Client
```

### Client Streaming

```text
Client
  |
  | Request 1
  | Request 2
  | Request 3
  v
Server
  |
  | Final Response
  v
Client
```

### Bidirectional Streaming

Both client and server send multiple messages during the same RPC.

## 19. REST vs gRPC

| Feature | REST | gRPC |
|---|---|---|
| Protocol | Commonly HTTP/1.1 or HTTP/2 | HTTP/2 |
| Data Format | Usually JSON | Usually Protobuf |
| Contract | Often OpenAPI | `.proto` |
| Browser Support | Direct | Often requires a browser-compatible layer |
| Streaming | Additional mechanisms | Built-in streaming RPC patterns |
| Human Readability | High with JSON | Lower with binary Protobuf |
| Common Use | Public APIs | Internal service communication |

### When Should You Use gRPC?

Consider gRPC when:

- Services need strongly defined contracts.
- Efficient binary serialization is useful.
- Streaming is required.
- Service-to-service communication is frequent.
- You control both client and server implementations.

Use REST when simplicity, broad compatibility and straightforward browser integration are more important.

Official resource: [gRPC Documentation](https://grpc.io/docs/).

---

# PART 9: MESSAGE BROKERS

## 20. What Is a Message Broker?

A message broker is infrastructure that receives messages from producers and delivers them to consumers.

It sits between applications.

Example:

```text
Order Service
     |
     | Publish Message
     v
 Message Broker
     |
     | Deliver Message
     v
Notification Service
```

The producer and consumer do not need to communicate directly.

### Important Terminology

| Term | Meaning |
|---|---|
| Producer | Sends messages |
| Consumer | Receives and processes messages |
| Broker | Manages message delivery |
| Queue | Holds messages for consumers |
| Exchange | Routes messages in RabbitMQ |
| Topic | Named event stream in Kafka |
| Acknowledgement | Confirms successful handling |
| Dead-Letter Queue | Holds messages that cannot be processed normally |

### Why Use Message Brokers?

- Decouple services.
- Process tasks asynchronously.
- Buffer traffic spikes.
- Support retries.
- Distribute workloads.
- Enable event-driven architectures.

---

# PART 10: RABBITMQ

## 21. What Is RabbitMQ?

RabbitMQ is a message broker that implements the Advanced Message Queuing Protocol (AMQP), among other protocols.

It is commonly used for:

- Background jobs.
- Task queues.
- Asynchronous service communication.
- Work distribution.
- Event routing.

### RabbitMQ Architecture

```text
Producer
   |
   v
Exchange
   |
   v
Binding / Routing Key
   |
   v
Queue
   |
   v
Consumer
```

### Important RabbitMQ Components

#### 1. Producer

The application that publishes messages.

#### 2. Exchange

Receives messages from producers and routes them to queues.

#### 3. Queue

Stores messages until consumers process them.

#### 4. Binding

Connects an exchange to a queue.

#### 5. Routing Key

Helps determine where a message should be routed.

#### 6. Consumer

Receives and processes messages.

---

## 22. RabbitMQ Exchange Types

RabbitMQ provides four commonly used exchange types.

| Exchange | Description |
|---|---|
| Direct | Routes using exact routing-key matching |
| Fanout | Broadcasts to all bound queues |
| Topic | Routes using pattern matching |
| Headers | Routes using message headers |

### 22.1 Direct Exchange

Routes a message when the routing key exactly matches a binding key.

Example:

```text
Routing Key: order.created

                 Direct Exchange
                        |
                        v
                  Queue: orders
```

### 22.2 Fanout Exchange

Broadcasts messages to all bound queues.

```text
                Fanout Exchange
                       |
          -------------+-------------
          |            |            |
          v            v            v
       Queue A      Queue B      Queue C
```

Useful when multiple services need the same event.

### 22.3 Topic Exchange

Uses wildcard patterns.

Example routing keys:

```text
order.created
order.cancelled
payment.completed
payment.failed
```

Binding patterns:

```text
order.*
payment.#
```

In RabbitMQ topic routing:

- `*` matches one word.
- `#` matches zero or more words.

### 22.4 Headers Exchange

Routes based on message header values instead of routing keys.

---

## 23. RabbitMQ Message Acknowledgement

A consumer should acknowledge a message after successful processing.

Example:

```text
Message Received
       |
       v
Process Message
       |
       |-- Success --> ACK
       |
       |-- Failure --> NACK / Reject
```

Acknowledgement prevents a message from being treated as successfully completed before processing finishes.

### ACK

Confirms successful processing.

### NACK

Rejects a message.

Depending on configuration, it may be requeued or routed elsewhere.

### Why Are Acknowledgements Important?

Suppose:

1. Consumer receives a payment event.
2. Consumer starts processing.
3. Consumer crashes.
4. Message has not been acknowledged.

The broker can redeliver the message according to queue and acknowledgement configuration.

This is why consumers must be designed to handle duplicate delivery.

---

## 24. RabbitMQ Example with Node.js

Install:

```bash
npm install amqplib
npm install -D @types/amqplib
```

### Producer

```typescript
import amqp from "amqplib";

async function publishOrderCreated() {
  const connection = await amqp.connect(
    process.env.RABBITMQ_URL!
  );

  const channel = await connection.createConfirmChannel();

  const exchange = "orders";

  await channel.assertExchange(
    exchange,
    "topic",
    { durable: true }
  );

  const event = {
    eventId: crypto.randomUUID(),
    eventType: "order.created",
    orderId: 1001,
    userId: 50
  };

  channel.publish(
    exchange,
    "order.created",
    Buffer.from(JSON.stringify(event)),
    {
      persistent: true,
      contentType: "application/json"
    }
  );

  await channel.waitForConfirms();

  await channel.close();
  await connection.close();
}
```

This example uses a publisher-confirm channel so the producer can wait for broker confirmation.

For production, manage long-lived connections and channels instead of opening and closing them for every message.

### Consumer

```typescript
import amqp from "amqplib";

async function consumeOrders() {
  const connection = await amqp.connect(
    process.env.RABBITMQ_URL!
  );

  const channel = await connection.createChannel();

  await channel.assertExchange(
    "orders",
    "topic",
    { durable: true }
  );

  await channel.assertQueue(
    "notification.orders",
    { durable: true }
  );

  await channel.bindQueue(
    "notification.orders",
    "orders",
    "order.created"
  );

  await channel.prefetch(10);

  await channel.consume(
    "notification.orders",
    async message => {
      if (!message) return;

      try {
        const event = JSON.parse(
          message.content.toString()
        );

        await processOrderNotification(event);

        channel.ack(message);

      } catch (error) {
        console.error("Message processing failed", error);

        // Requeue policy must be designed carefully.
        channel.nack(message, false, false);
      }
    }
  );
}
```

In production, configure dead-letter routing for rejected messages and implement bounded retry policies. Immediate requeueing of a permanently failing message can create an infinite processing loop.

Official resource: [RabbitMQ Documentation](https://www.rabbitmq.com/docs).

---

# PART 11: APACHE KAFKA

## 25. What Is Apache Kafka?

Apache Kafka is a distributed event-streaming platform.

It is designed to publish, store, process and consume streams of events.

Kafka is commonly used for:

- Event-driven architectures.
- Real-time analytics.
- Log aggregation.
- Data pipelines.
- Event sourcing.
- High-throughput event processing.

### Kafka Architecture

```text
                 Kafka Cluster
                       |
          ---------------------------
          |            |            |
          v            v            v
       Broker 1     Broker 2     Broker 3
          |            |            |
          ---------------------------
                       |
                    Topics
                       |
                 Partitions
```

### Important Kafka Components

| Component | Description |
|---|---|
| Producer | Publishes events |
| Consumer | Reads events |
| Broker | Kafka server |
| Topic | Named event stream |
| Partition | Ordered log within a topic |
| Offset | Position of a record within a partition |
| Consumer Group | Group of consumers sharing topic partitions |
| Replication | Copies partition data across brokers |

Kafka's official documentation describes topics as durable event streams organized into partitions, with producers and consumers operating independently. [Apache Kafka](https://kafka.apache.org/documentation/?utm_source=chatgpt.com)

---

## 26. What Is a Kafka Topic?

A topic is a named stream of events.

Example:

```text
orders
payments
users
notifications
inventory
```

An Order Service can publish events to:

```text
orders
```

A Notification Service can consume from the same topic.

### Topic Example

```text
Topic: orders

Event 1: order.created
Event 2: order.updated
Event 3: order.cancelled
Event 4: order.completed
```

Unlike a traditional queue, Kafka retains records according to its retention configuration even after consumers read them.

Different consumer groups can independently read the same event stream.

---

## 27. What Is a Kafka Partition?

A partition is an ordered, append-only log of records within a topic.

Example:

```text
Topic: orders

Partition 0:
[Event 1] [Event 2] [Event 3]

Partition 1:
[Event 4] [Event 5] [Event 6]

Partition 2:
[Event 7] [Event 8] [Event 9]
```

Partitions allow Kafka to distribute data and processing across multiple brokers and consumers.

### Why Use Partitions?

- Horizontal scalability.
- Parallel consumption.
- Distribution across brokers.
- Per-partition ordering.

### Important Ordering Rule

Kafka guarantees ordering within a partition, not globally across all partitions.

If events for the same order must be processed in order, use the order ID as the event key.

Example:

```text
Key: order-1001
```

This helps ensure that related events are routed to the same partition under the standard keyed partitioning strategy.

---

## 28. What Is a Kafka Consumer Group?

A consumer group allows multiple consumer instances to share the processing of a topic.

Example:

```text
Topic: orders
Partitions: 4

Consumer Group: notification-service

Consumer 1 --> Partition 0
Consumer 2 --> Partition 1
Consumer 3 --> Partition 2
Consumer 4 --> Partition 3
```

Each partition is assigned to one consumer within the group at a time.

If two different consumer groups subscribe to the same topic, each group can independently process the events.

Example:

```text
                 orders topic
                      |
           -----------------------
           |                     |
           v                     v
  Notification Group       Analytics Group
           |                     |
           v                     v
    Send Emails             Build Reports
```

This is a powerful feature of Kafka.

### Important

A consumer group generally cannot usefully parallelize a single topic partition across multiple consumers at the same time.

If a topic has four partitions, a consumer group can have at most four actively assigned consumers for those partitions.

---

## 29. Kafka Delivery Semantics

There are three important delivery concepts.

### At-Most-Once

A message is processed zero or one time.

Messages may be lost, but duplicate processing is avoided by the chosen strategy.

### At-Least-Once

A message is delivered one or more times.

Duplicates are possible.

Consumers must be idempotent.

### Exactly-Once

A processing design aims to ensure that a particular operation's effects are applied exactly once within a defined transactional boundary.

Kafka supports transactions and exactly-once processing features for certain Kafka-to-Kafka workflows.

However, exactly-once processing across an arbitrary external database, payment provider or HTTP service is not automatically guaranteed.

External side effects still require idempotency and appropriate coordination.

### Comparison

| Delivery Semantics | Guarantee |
|---|---|
| At-Most-Once | Zero or one processing attempt |
| At-Least-Once | One or more delivery attempts |
| Exactly-Once | Exactly-once effect within defined supported boundaries |

---

## 30. Kafka Example with Node.js

Install:

```bash
npm install kafkajs
```

### Producer

```typescript
import { Kafka } from "kafkajs";

const kafka = new Kafka({
  clientId: "order-service",
  brokers: ["localhost:9092"]
});

const producer = kafka.producer();

async function publishOrderCreated() {
  await producer.connect();

  await producer.send({
    topic: "orders",
    messages: [
      {
        key: "order-1001",

        value: JSON.stringify({
          eventId: crypto.randomUUID(),
          eventType: "order.created",
          orderId: 1001,
          userId: 50
        })
      }
    ]
  });
}
```

### Consumer

```typescript
import { Kafka } from "kafkajs";

const kafka = new Kafka({
  clientId: "notification-service",
  brokers: ["localhost:9092"]
});

const consumer = kafka.consumer({
  groupId: "notification-group"
});

async function startConsumer() {
  await consumer.connect();

  await consumer.subscribe({
    topic: "orders",
    fromBeginning: false
  });

  await consumer.run({
    eachMessage: async ({ message }) => {
      const event = JSON.parse(
        message.value!.toString()
      );

      await processOrderNotification(event);
    }
  });
}
```

For production, configure:

- Consumer error handling.
- Graceful shutdown.
- Retry policies.
- Dead-letter topics or equivalent failure handling.
- Schema compatibility.
- Message processing idempotency.
- Appropriate offset-commit behavior.

Official resource: [Apache Kafka Documentation](https://kafka.apache.org/documentation/).

---

# PART 12: RABBITMQ VS KAFKA

## 31. RabbitMQ vs Apache Kafka

Both technologies support asynchronous communication, but they have different design characteristics.

| Feature | RabbitMQ | Kafka |
|---|---|---|
| Primary Model | Message broker | Distributed event-streaming platform |
| Main Abstraction | Exchanges and queues | Topics and partitions |
| Message Consumption | Usually queue-based delivery | Consumer groups and offsets |
| Message Retention | Queue lifecycle and acknowledgement policies | Configurable retention |
| Ordering | Depends on queue and consumer configuration | Per-partition ordering |
| Replay | Not its primary design model | Core capability |
| Routing | Flexible exchange routing | Topic and partition routing |
| Common Use | Task queues and messaging | Event streams and data pipelines |
| Throughput | Workload-dependent | Designed for high-throughput event streaming |

### When Should You Use RabbitMQ?

Use RabbitMQ when:

- You need task queues.
- You need flexible message routing.
- You need background job processing.
- You need acknowledgement-based work distribution.
- You need request-response messaging.

### When Should You Use Kafka?

Use Kafka when:

- You need durable event streams.
- Multiple independent consumers need to process the same events.
- You need replay.
- You need high-throughput event processing.
- You need event pipelines and analytics.

**Important:** These technologies overlap. The correct choice depends on message volume, retention, routing, replay, ordering and operational requirements.

---

# PART 13: EVENT-DRIVEN ARCHITECTURE

## 32. What Is Event-Driven Architecture?

Event-Driven Architecture (EDA) is an architectural style in which components communicate by producing and consuming events.

An event represents something that has happened.

Examples:

```text
UserRegistered
OrderCreated
PaymentCompleted
PaymentFailed
ProductStockUpdated
ShipmentDispatched
```

### Basic Architecture

```text
              Event Producer
                    |
                    v
                Event Broker
                    |
         -----------------------
         |          |          |
         v          v          v
      Consumer A Consumer B Consumer C
```

### Example: User Registration

When a user registers:

1. Auth Service creates the user.
2. Auth Service publishes `user.registered`.
3. Notification Service sends a welcome email.
4. Analytics Service records the registration.
5. CRM Service updates customer information.

```text
                 Auth Service
                      |
                      v
              user.registered
                      |
                      v
                 Message Broker
                      |
          ---------------------------
          |            |            |
          v            v            v
     Notification   Analytics      CRM
       Service       Service     Service
```

The Auth Service does not need to know the internal implementation of all consumers.

---

## 33. What Is an Event?

An event is an immutable record of something that has already happened.

Example:

```json
{
  "eventId": "evt-12345",
  "eventType": "order.created",
  "eventVersion": 1,
  "occurredAt": "2026-11-25T12:00:00Z",
  "data": {
    "orderId": 1001,
    "userId": 50,
    "totalAmount": 2500
  }
}
```

### Event Naming Best Practices

Use past-tense business events.

Good:

```text
order.created
payment.completed
user.registered
inventory.reserved
```

Avoid confusing event names:

```text
createOrder
doPayment
updateUser
```

The second group sounds more like commands.

### Event vs Command

| Event | Command |
|---|---|
| Describes something that happened | Requests an action |
| Past tense | Imperative or action-oriented |
| A fact | An instruction |
| Example: `order.created` | Example: `CreateOrder` |

A command can fail or be rejected.

An event describes a fact that has already occurred.

---

# PART 14: EVENTUAL CONSISTENCY

## 34. What Is Eventual Consistency?

Eventual consistency means different services or data replicas may temporarily contain different states, but they are expected to converge if updates stop and processing succeeds.

Example:

A user places an order.

```text
Order Service:
Order Status = CREATED

Inventory Service:
Stock Reservation = PENDING

Payment Service:
Payment Status = PENDING
```

After asynchronous processing:

```text
Order Service:
Order Status = CONFIRMED

Inventory Service:
Stock Reservation = COMPLETED

Payment Service:
Payment Status = SUCCESS
```

The system moves toward a consistent business state over time.

### Why Does Eventual Consistency Occur?

Because services:

- Maintain independent databases.
- Communicate asynchronously.
- Process events at different speeds.
- May experience temporary failures.

### Advantages

- Lower coupling.
- Better service autonomy.
- Better scalability.
- Less dependence on distributed database transactions.

### Disadvantages

- Users may temporarily see stale information.
- Workflows become more complicated.
- Retry handling is required.
- Business operations need intermediate states.

### Important Design Principle

Your application should explicitly represent intermediate states.

Example:

```text
PENDING
PROCESSING
CONFIRMED
FAILED
CANCELLED
```

Do not assume that every distributed business operation completes instantly.

---

# PART 15: SERVICE DISCOVERY

## 35. What Is Service Discovery?

Service discovery is the process of locating available service instances dynamically.

Suppose the Inventory Service runs three instances:

```text
inventory-service-1:3002
inventory-service-2:3002
inventory-service-3:3002
```

The Order Service needs to communicate with Inventory.

How does it know which instance to call?

Service discovery solves this problem.

### Without Service Discovery

The Order Service contains a hardcoded address:

```typescript
const INVENTORY_URL = "http://192.168.1.10:3002";
```

This becomes difficult when:

- Instances change.
- Containers restart.
- Services scale.
- IP addresses change.

### With Service Discovery

```text
             Order Service
                   |
                   v
             Service Registry
                   |
          ---------------------
          |         |         |
          v         v         v
      Inventory  Inventory  Inventory
      Instance 1 Instance 2 Instance 3
```

The service discovery mechanism helps locate healthy instances.

### Common Service Discovery Approaches

1. DNS-based discovery.
2. Service registry.
3. Client-side discovery.
4. Server-side discovery.
5. Platform-native discovery.

### Kubernetes Service Discovery

In Kubernetes, Services provide stable network endpoints for accessing groups of Pods.

Example:

```text
http://inventory-service
```

The Kubernetes networking layer routes traffic to available backend Pods.

### Why Is Service Discovery Important?

- Dynamic infrastructure.
- Horizontal scaling.
- Instance replacement.
- Load balancing.
- Service availability.

---

# PART 16: API GATEWAY ARCHITECTURE

## 36. What Is an API Gateway?

An API Gateway acts as a central entry point for clients accessing multiple backend services.

Without an API Gateway:

```text
Frontend
   |
   |-- Auth Service
   |
   |-- Product Service
   |
   |-- Order Service
   |
   |-- Payment Service
```

The frontend needs to understand multiple backend endpoints.

With an API Gateway:

```text
              Frontend
                  |
                  v
              API Gateway
                  |
       -------------------------
       |          |            |
       v          v            v
     Auth      Product       Order
    Service    Service      Service
```

### API Gateway Responsibilities

- Request routing.
- Authentication integration.
- Rate limiting.
- TLS termination.
- Request validation.
- Response aggregation.
- Logging.
- Monitoring.

### Example Routes

```text
/api/auth/*       -> Auth Service
/api/products/*   -> Product Service
/api/orders/*     -> Order Service
/api/payments/*   -> Payment Service
```

### What Is Backend for Frontend (BFF)?

BFF means Backend for Frontend.

It creates separate backend layers for different client types.

Example:

```text
Mobile App
    |
    v
Mobile BFF
    |
    v
Microservices

Web App
    |
    v
Web BFF
    |
    v
Microservices
```

A mobile application may need smaller responses than a desktop web application.

BFF allows each client to have an API layer optimized for its needs.

---

# PART 17: CENTRALIZED CONFIGURATION

## 37. What Is Centralized Configuration?

In a microservices architecture, different services require configuration values.

Examples:

- Database URLs.
- Redis URLs.
- Message broker addresses.
- JWT issuer.
- API endpoints.
- Feature flags.
- Timeout settings.

Managing these values separately can become difficult.

Centralized configuration provides a controlled way to manage configuration across services.

### Example

```text
Configuration System
         |
    -------------
    |     |     |
    v     v     v
  Auth  Order  Payment
```

### Configuration Best Practices

1. Never hardcode secrets.
2. Use environment variables for deployment-specific configuration.
3. Validate required configuration during startup.
4. Restrict secret access.
5. Rotate credentials.
6. Keep production and development configurations separate.
7. Use a secret manager for sensitive credentials.

### Environment Variable Example

```env
SERVICE_NAME=order-service
PORT=3003

DB_HOST=localhost
DB_PORT=3306

REDIS_URL=redis://localhost:6379

RABBITMQ_URL=amqp://localhost
```

Do not commit production `.env` files or credentials to GitHub.

---

# PART 18: DISTRIBUTED LOGGING

## 38. What Is Distributed Logging?

In a monolithic application, logs may come from one application process.

In microservices, a single user request may travel through several services.

Example:

```text
API Gateway
    |
    v
Order Service
    |
    v
Payment Service
    |
    v
Notification Service
```

Each service produces its own logs.

You need a way to collect and correlate these logs.

### Distributed Logging Architecture

```text
Auth Service Logs --------\
Order Service Logs --------\
Payment Service Logs -------+--> Log Collector
Inventory Service Logs -----/         |
Notification Service Logs -/          v
                                 Centralized Logs
```

### Common Tools

- Elasticsearch.
- Logstash.
- Kibana.
- Grafana Loki.
- Fluent Bit.
- OpenSearch.

### Structured Logging

Instead of:

```text
Order created successfully
```

Use structured JSON:

```json
{
  "level": "info",
  "service": "order-service",
  "event": "order.created",
  "orderId": 1001,
  "userId": 50,
  "timestamp": "2026-11-25T12:00:00Z"
}
```

Structured logs are easier to search and analyze.

### Important Logging Practices

- Include service names.
- Include request IDs.
- Include trace IDs.
- Include event IDs.
- Record meaningful errors.
- Redact passwords and tokens.
- Avoid unnecessary personal information.

---

# PART 19: DISTRIBUTED TRACING AND OBSERVABILITY

## 39. What Is Distributed Tracing?

Distributed tracing tracks a request as it travels across multiple services.

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
Inventory Service
   |
   v
Payment Service
   |
   v
Notification Service
```

If the request takes five seconds, which service caused the delay?

Distributed tracing helps identify the answer.

### Trace

A trace represents the complete journey of a request.

### Span

A span represents an individual operation within a trace.

Example:

```text
Trace ID: abc123

API Gateway       50ms
Order Service    100ms
Inventory        200ms
Payment         3000ms
Notification     150ms
```

The Payment Service is responsible for most of the latency.

### Trace Architecture

```text
                 User Request
                      |
                      v
                 API Gateway
                      |
                 Trace Context
                      |
          -------------------------
          |           |           |
          v           v           v
        Order     Inventory    Payment
        Span        Span        Span
          |           |           |
          -------------------------
                      |
                      v
              Tracing Backend
```

### Common Tools

- OpenTelemetry.
- Jaeger.
- Grafana Tempo.
- Zipkin.
- Grafana.

### What Is OpenTelemetry?

OpenTelemetry is an observability framework and set of APIs, SDKs and tools for collecting telemetry.

It supports:

- Traces.
- Metrics.
- Logs.

Official resource: [OpenTelemetry Documentation](https://opentelemetry.io/docs/).

---

## 40. What Is the Difference Between Logging, Monitoring and Tracing?

| Concept | Purpose |
|---|---|
| Logging | Records events and application messages |
| Monitoring | Tracks system health and metrics |
| Tracing | Tracks request flow across services |
| Observability | Helps understand internal system behavior from external outputs |

### Example

A user reports:

"My order is taking too long."

You can investigate using:

**Logs:** Find errors related to the order.

**Metrics:** Check whether Payment Service latency is unusually high.

**Traces:** Identify which service call caused the delay.

All three are important.

---

# PART 20: RESILIENCE DESIGN PATTERNS

## 41. Why Is Resilience Important?

Microservices communicate over networks.

Networks can fail.

Services can become slow.

Databases can become unavailable.

Message brokers can experience outages.

A reliable architecture must expect these failures.

Important resilience patterns include:

1. Timeout.
2. Retry.
3. Circuit Breaker.
4. Bulkhead.
5. Fallback.

---

## 42. Timeout Pattern

A timeout defines how long a service waits for another service to respond.

Example:

```typescript
const response = await fetch(
  "http://payment-service:3004/payments",
  {
    method: "POST",
    signal: AbortSignal.timeout(3000)
  }
);
```

The request is aborted if the timeout is exceeded.

### Why Are Timeouts Important?

Without timeouts, requests may wait indefinitely.

This can exhaust:

- Connections.
- Memory.
- Worker capacity.
- Application resources.

### Timeout Best Practices

- Set reasonable timeouts.
- Use different timeouts for different operations.
- Consider downstream service latency.
- Propagate cancellation where supported.
- Avoid excessively long request chains.

---

## 43. Retry Pattern

Retry means attempting an operation again after a temporary failure.

Example:

```text
Request
   |
   v
Service Call
   |
   |-- Success --> Continue
   |
   |-- Temporary Failure
            |
            v
          Retry
```

### Exponential Backoff

Instead of retrying every second, increase the delay after each failure.

Example:

```text
Attempt 1: 100 ms
Attempt 2: 200 ms
Attempt 3: 400 ms
Attempt 4: 800 ms
```

A formula:

```text
delay = initialDelay × 2^(attempt - 1)
```

### Add Jitter

Jitter adds randomness to retry delays.

For example:

```text
Base Delay: 400 ms
Randomized Delay: 350–450 ms
```

This reduces synchronized retry spikes when many clients fail at the same time.

### Node.js Retry Example

```typescript
async function retryOperation(
  operation: () => Promise<any>,
  maxRetries = 3
) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await operation();

    } catch (error) {
      if (attempt === maxRetries) {
        throw error;
      }

      const baseDelay = 200 * 2 ** (attempt - 1);

      const jitter = Math.random() * 100;

      await new Promise(resolve =>
        setTimeout(resolve, baseDelay + jitter)
      );
    }
  }
}
```

### Important Retry Rules

Do not retry every failure.

Usually retry:

- Temporary network failures.
- Selected timeouts.
- Temporary service unavailability.
- Certain throttling responses.

Do not blindly retry:

- Validation errors.
- Authentication failures.
- Authorization failures.
- Permanent business-rule failures.

**Critical:** Retrying a non-idempotent operation such as payment creation can create duplicates. Use idempotency keys or another deduplication mechanism.

---

## 44. Circuit Breaker Pattern

The Circuit Breaker pattern prevents repeated calls to a service that is currently failing.

Imagine the Payment Service is unavailable.

Without a circuit breaker:

```text
Order Service
     |
     | Request
     v
Payment Service
     |
     X Failure

Order Service
     |
     | Request
     v
Payment Service
     |
     X Failure
```

The Order Service continues sending requests, wasting resources.

### Circuit Breaker States

There are three main states.

#### 1. Closed

Requests are allowed.

The circuit is operating normally.

#### 2. Open

Requests are blocked because the failure threshold has been exceeded.

#### 3. Half-Open

A limited number of test requests are allowed to determine whether the downstream service has recovered.

### Architecture

```text
              Circuit Breaker
                     |
          ---------------------
          |         |         |
          v         v         v
        CLOSED    OPEN    HALF-OPEN
          |         |         |
          v         v         v
       Requests   Block    Test Request
       Allowed             |
                           |
                   ----------------
                   |              |
                   v              v
                Success         Failure
                   |              |
                   v              v
                 CLOSED          OPEN
```

### Example

Suppose:

```text
Failure Threshold = 5
```

After five qualifying failures, the circuit opens.

After a configured wait period, it transitions to half-open.

If test requests succeed, the circuit closes.

If they fail, it opens again.

### Why Use Circuit Breakers?

- Prevent cascading failures.
- Reduce unnecessary network traffic.
- Protect downstream services.
- Allow recovery time.
- Fail quickly.

---

## 45. Bulkhead Pattern

The Bulkhead pattern isolates resources so that one workload cannot consume everything.

The name comes from compartments inside ships.

If one compartment floods, the others remain protected.

### Example Without Bulkheads

```text
Order Service
     |
     v
Shared Connection Pool
     |
     |-- Inventory Requests
     |-- Payment Requests
     |-- Notification Requests
```

If Payment Service requests become stuck, they may consume all available connections.

Other operations can also fail.

### With Bulkheads

```text
Order Service
     |
     |-- Payment Connection Pool
     |
     |-- Inventory Connection Pool
     |
     |-- Notification Connection Pool
```

Each dependency has isolated resources or concurrency limits.

### Benefits

- Limits resource exhaustion.
- Reduces failure propagation.
- Protects critical operations.
- Improves fault isolation.

Microsoft's microservices design guidance identifies Bulkhead as a pattern for isolating resources such as connection pools, CPU and memory. [Microsoft Learn](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/patterns?utm_source=chatgpt.com)

---

# PART 21: SAGA PATTERN

## 46. What Is the Saga Pattern?

A Saga is a sequence of local transactions distributed across multiple services.

Each service performs its own transaction and communicates the result to the next service.

If a later operation fails, compensating actions can reverse or counteract earlier business operations.

### Example: E-commerce Order

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
Confirm Order
```

Suppose payment fails.

The system may perform:

```text
Payment Failed
      |
      v
Release Inventory
      |
      v
Cancel Order
```

### Why Saga?

Because each microservice owns its own database.

You cannot simply start one SQL transaction across all independent databases and expect a normal local `ROLLBACK` to undo every operation.

Saga manages the workflow through local transactions and compensations.

---

## 47. Types of Saga

### 47.1 Choreography-Based Saga

Services coordinate through events.

There is no central workflow controller.

```text
Order Service
     |
     | OrderCreated
     v
Inventory Service
     |
     | InventoryReserved
     v
Payment Service
     |
     | PaymentCompleted
     v
Shipping Service
```

Each service listens for relevant events and performs its operation.

### Advantages

- Lower central orchestration coupling.
- Services react independently.
- Useful for relatively simple workflows.

### Disadvantages

- Workflow becomes difficult to visualize.
- Event dependencies can become complex.
- Debugging may be harder.

### 47.2 Orchestration-Based Saga

A central orchestrator coordinates the workflow.

```text
             Order Saga
              Orchestrator
                   |
        -----------------------
        |          |          |
        v          v          v
    Inventory   Payment   Shipping
     Service    Service    Service
```

The orchestrator decides which operation should happen next.

### Advantages

- Centralized workflow visibility.
- Easier to understand complex business processes.
- Centralized compensation handling.

### Disadvantages

- Additional orchestration component.
- Risk of putting too much business logic in the orchestrator.

### Choreography vs Orchestration

| Choreography | Orchestration |
|---|---|
| Event-driven coordination | Central coordinator |
| No central workflow controller | Explicit workflow controller |
| Suitable for simpler flows | Suitable for complex flows |
| More distributed workflow logic | More centralized workflow logic |

---

# PART 22: TRANSACTIONAL OUTBOX PATTERN

## 48. What Is the Transactional Outbox Pattern?

The Transactional Outbox pattern solves a common problem in event-driven microservices.

Suppose the Order Service performs:

1. Save an order to MySQL.
2. Publish an `order.created` event to RabbitMQ.

What happens if the database commit succeeds but RabbitMQ is unavailable?

```text
Save Order
    |
    v
Database Commit Successful
    |
    v
Publish Event
    |
    X
RabbitMQ Unavailable
```

Now the order exists, but other services were never notified.

This is called the dual-write problem.

### Solution: Transactional Outbox

Store the business record and the event in the same local database transaction.

```text
Order Service
     |
     v
Database Transaction
     |
     |-- Insert Order
     |
     |-- Insert Outbox Event
     |
     v
Commit
     |
     v
Outbox Publisher
     |
     v
RabbitMQ / Kafka
```

### Example Outbox Table

```sql
CREATE TABLE outbox_events (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    event_id VARCHAR(100) NOT NULL UNIQUE,
    event_type VARCHAR(100) NOT NULL,
    payload JSON NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    published_at TIMESTAMP NULL
);
```

### Save Order and Event Together

```typescript
async function createOrder(data) {
  const connection = await pool.getConnection();

  try {
    await connection.beginTransaction();

    const [orderResult] = await connection.execute(
      `INSERT INTO orders (user_id, total_amount)
       VALUES (?, ?)`,
      [data.userId, data.totalAmount]
    );

    const orderId = orderResult.insertId;

    const event = {
      eventId: crypto.randomUUID(),
      eventType: "order.created",
      orderId,
      userId: data.userId
    };

    await connection.execute(
      `INSERT INTO outbox_events
       (event_id, event_type, payload)
       VALUES (?, ?, ?)`,
      [
        event.eventId,
        event.eventType,
        JSON.stringify(event)
      ]
    );

    await connection.commit();

    return {
      orderId,
      event
    };

  } catch (error) {
    await connection.rollback();
    throw error;

  } finally {
    connection.release();
  }
}
```

### Outbox Publisher

A background worker periodically reads unpublished events and publishes them to the broker.

Simplified process:

```text
Read Unpublished Events
          |
          v
Publish to Broker
          |
          v
Wait for Broker Confirmation
          |
          v
Mark Event as Published
```

### Important: Duplicate Publishing

Suppose:

1. Publisher sends an event.
2. Broker accepts it.
3. Publisher crashes before marking the event as published.
4. Publisher restarts.
5. It sends the event again.

Therefore, the Outbox pattern commonly provides reliable at-least-once publishing, not automatic exactly-once delivery.

Consumers must be idempotent.

The Outbox pattern is documented as a core microservices data-consistency pattern in Microsoft and AWS architecture guidance. [Microsoft Learn](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/patterns?utm_source=chatgpt.com)

---

# PART 23: IDEMPOTENT MESSAGE PROCESSING

## 49. What Is Idempotent Message Processing?

An operation is idempotent when processing the same message multiple times produces the same intended business result as processing it once.

Example:

A payment event is delivered twice.

```text
payment.completed
payment.completed
```

Without deduplication, the application might record the payment twice.

### Solution: Event ID Deduplication

Every event should have a unique identifier.

Example:

```json
{
  "eventId": "evt-1001",
  "eventType": "payment.completed",
  "paymentId": 500
}
```

Create a processed-events table:

```sql
CREATE TABLE processed_events (
    event_id VARCHAR(100) PRIMARY KEY,
    processed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Consumer Logic

The consumer should:

1. Begin a database transaction.
2. Attempt to insert the event ID.
3. If the event ID already exists, treat the message as a duplicate.
4. If it is new, perform the business operation.
5. Commit the transaction.
6. Acknowledge the message.

Conceptual example:

```typescript
async function processPaymentEvent(event) {
  const connection = await pool.getConnection();

  try {
    await connection.beginTransaction();

    await connection.execute(
      `INSERT INTO processed_events (event_id)
       VALUES (?)`,
      [event.eventId]
    );

    await connection.execute(
      `UPDATE payments
       SET status = 'completed'
       WHERE id = ?`,
      [event.paymentId]
    );

    await connection.commit();

  } catch (error: any) {
    await connection.rollback();

    if (error.code === "ER_DUP_ENTRY") {
      return;
    }

    throw error;

  } finally {
    connection.release();
  }
}
```

This is a simplified pattern. The deduplication record and business operation must be committed atomically in the same database.

If the consumer also performs external side effects, such as calling a payment provider, additional idempotency mechanisms are required.

---

# PART 24: MICROSERVICES SECURITY

## 50. Why Is Microservices Security Different?

A monolithic application may have one main backend security boundary.

A microservices architecture introduces multiple network communication boundaries.

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
```

Each communication boundary must be protected.

### Important Security Practices

1. Authenticate users at the appropriate entry points.
2. Authenticate service-to-service communication.
3. Enforce authorization in every service.
4. Use TLS.
5. Apply network segmentation.
6. Manage secrets securely.
7. Validate incoming messages.
8. Protect internal APIs.
9. Restrict service permissions.
10. Monitor suspicious communication.

---

## 51. Service-to-Service Authentication

One service should not automatically trust another service simply because it runs inside the same network.

Common approaches include:

- OAuth 2.0 Client Credentials.
- Mutual TLS (mTLS).
- Workload identity.
- Short-lived service credentials.

### OAuth Client Credentials Flow

Example:

```text
Order Service
     |
     | Client Authentication
     v
Authorization Server
     |
     | Access Token
     v
Order Service
     |
     | Bearer Token
     v
Payment Service
```

The Payment Service validates that the token is issued by a trusted authority and grants the required permission.

### What Is Mutual TLS?

In normal TLS, the server presents its certificate to authenticate itself to the client.

In mutual TLS, both client and server authenticate each other using certificates.

It is commonly used for secure service-to-service communication.

### Important

Authentication and authorization remain separate.

A service may be authenticated but still lack permission to perform a particular operation.

---

# PART 25: DOCKER-BASED MICROSERVICES

## 52. Why Use Docker for Microservices?

Docker allows services to be packaged with their runtime dependencies.

Each microservice can run inside its own container.

Example:

```text
                 Docker Host
                      |
       ------------------------------
       |          |         |       |
       v          v         v       v
      Auth      Product   Order   Payment
    Container  Container Container Container
```

### Benefits

- Consistent runtime environments.
- Independent deployment.
- Easier scaling.
- Process isolation.
- Reproducible builds.
- Simplified local development.

---

## 53. Example Microservices Project Structure

```text
ecommerce/
│
├── api-gateway/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
│
├── auth-service/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
│
├── product-service/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
│
├── order-service/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
│
├── payment-service/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
│
├── notification-service/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
│
└── docker-compose.yml
```

---

## 54. Example Dockerfile

For Order Service:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

EXPOSE 3003

CMD ["npm", "run", "start:prod"]
```

For production, consider a multi-stage build, a non-root user, pinned base-image versions and excluding development dependencies from the runtime image.

---

## 55. Docker Compose for Microservices

Example:

```yaml
services:

  order-service:
    build: ./order-service
    ports:
      - "3003:3003"
    environment:
      PORT: 3003
      PAYMENT_SERVICE_URL: http://payment-service:3004
    depends_on:
      - payment-service

  payment-service:
    build: ./payment-service
    ports:
      - "3004:3004"
    environment:
      PORT: 3004

  redis:
    image: redis:7
    ports:
      - "6379:6379"
```

Inside the Docker Compose network, the Order Service can communicate with the Payment Service using:

```text
http://payment-service:3004
```

The service name is resolved through Docker's network DNS.

### Important

`depends_on` controls container startup ordering, but it does not automatically guarantee that the dependent application is ready to accept requests.

Use health checks and application-level retry logic where needed.

---

# PART 26: MICROSERVICES DEPLOYMENT

## 56. How Are Microservices Deployed?

Microservices can be deployed using:

- Virtual machines.
- Docker containers.
- Kubernetes.
- Managed container platforms.
- Serverless platforms.

### Basic Deployment Architecture

```text
                Internet
                   |
                   v
              Load Balancer
                   |
                   v
              API Gateway
                   |
       -------------------------
       |          |            |
       v          v            v
    Order      Product       Payment
    Service    Service       Service
       |          |            |
       v          v            v
    Order DB  Product DB    Payment DB
```

### Independent Deployment

Suppose you update the Product Service.

You can deploy only:

```text
Product Service v2.0
```

Other services can continue running their existing versions, provided the service contracts remain compatible.

### Deployment Strategies

| Strategy | Description |
|---|---|
| Rolling Deployment | Gradually replaces instances |
| Blue-Green Deployment | Switches traffic between two environments |
| Canary Deployment | Releases to a small percentage of traffic first |
| Recreate | Stops the old version and deploys the new version |

### Important Deployment Practices

1. Maintain backward-compatible API contracts.
2. Use health checks.
3. Support graceful shutdown.
4. Monitor deployment errors.
5. Roll back when required.
6. Keep database migrations compatible with rolling deployments.
7. Avoid deploying all services unnecessarily.

---

# PART 27: MONITORING AND OBSERVABILITY

## 57. What Should You Monitor?

A production microservices system should monitor both individual services and the overall system.

### Service-Level Metrics

- Request count.
- Request latency.
- Error rate.
- CPU usage.
- Memory usage.
- Active connections.
- Queue processing rate.

### Distributed-System Metrics

- Message queue depth.
- Consumer lag.
- Failed event count.
- Retry count.
- Dead-letter queue size.
- Service-to-service latency.
- Circuit breaker state.
- Database connection utilization.

### Important Monitoring Tools

| Tool | Purpose |
|---|---|
| Prometheus | Metrics collection |
| Grafana | Dashboards and visualization |
| OpenTelemetry | Telemetry instrumentation |
| Jaeger | Distributed tracing |
| Grafana Tempo | Trace storage |
| Loki | Log aggregation |
| Elasticsearch | Search and log analytics |

### Example Monitoring Flow

```text
Microservices
     |
     |-- Logs
     |-- Metrics
     |-- Traces
     |
     v
Observability Platform
     |
     |-- Dashboards
     |-- Alerts
     |-- Trace Analysis
     |-- Log Search
```

---

# PART 28: COMPLETE MICROSERVICES PROJECT

## 58. Project: E-commerce Microservices Architecture

Now let's design a complete system.

### Business Requirements

Build an e-commerce application with:

- User registration and login.
- Product management.
- Inventory management.
- Order management.
- Payment processing.
- Notifications.
- Shipping management.

### Services

| Service | Responsibility | Database |
|---|---|---|
| Auth Service | Authentication and users | Auth DB |
| Product Service | Product catalog | Product DB |
| Inventory Service | Stock management | Inventory DB |
| Order Service | Order lifecycle | Order DB |
| Payment Service | Payments | Payment DB |
| Notification Service | Email and SMS | Notification DB |
| Shipping Service | Shipment management | Shipping DB |

Each service owns its data.

### Architecture

```text
                       CLIENT
                         |
                         v
                    API GATEWAY
                         |
       --------------------------------------
       |          |          |              |
       v          v          v              v
     AUTH      PRODUCT      ORDER        PAYMENT
    SERVICE    SERVICE     SERVICE       SERVICE
       |          |          |              |
       v          v          v              v
     AUTH DB  PRODUCT DB   ORDER DB      PAYMENT DB
                            |
                            |
                      Message Broker
                            |
            --------------------------------
            |               |              |
            v               v              v
        INVENTORY       NOTIFICATION     SHIPPING
         SERVICE          SERVICE        SERVICE
            |               |              |
            v               v              v
       INVENTORY DB    NOTIFICATION DB  SHIPPING DB
```

---

## 59. Order Creation Workflow

Suppose a customer places an order.

### Step 1: Client Request

```http
POST /api/orders
```

Request:

```json
{
  "items": [
    {
      "productId": 100,
      "quantity": 2
    }
  ]
}
```

### Step 2: API Gateway

The Gateway:

- Validates the request's authentication context.
- Routes the request to Order Service.

### Step 3: Order Service

The Order Service:

- Validates the requested products.
- Creates an order in `PENDING` status.
- Saves an `order.created` event in its Outbox table.
- Commits the transaction.

### Step 4: Publish Event

An Outbox worker publishes:

```json
{
  "eventId": "evt-1001",
  "eventType": "order.created",
  "orderId": 500,
  "items": [
    {
      "productId": 100,
      "quantity": 2
    }
  ]
}
```

### Step 5: Inventory Service

Inventory Service consumes the event.

It checks stock and reserves the requested quantity.

If successful, it publishes:

```text
inventory.reserved
```

If unsuccessful, it publishes:

```text
inventory.reservation_failed
```

### Step 6: Payment Service

After inventory reservation succeeds, the Payment Service processes the payment.

It publishes:

```text
payment.completed
```

or:

```text
payment.failed
```

### Step 7: Order Service

The Order Service receives the result and updates the order.

```text
Payment Completed
       |
       v
Order Status = CONFIRMED
```

If payment fails:

```text
Payment Failed
       |
       v
Release Inventory
       |
       v
Order Status = CANCELLED
```

### Complete Workflow

```text
Customer
   |
   v
API Gateway
   |
   v
Order Service
   |
   v
Create Pending Order
   |
   v
Outbox Event
   |
   v
Message Broker
   |
   v
Inventory Service
   |
   |-- Reservation Failed --> Cancel Order
   |
   |-- Reservation Success
              |
              v
        Payment Service
              |
         ------------
         |          |
         v          v
      Success     Failure
         |          |
         v          v
     Confirm     Compensate
      Order       Inventory
         |
         v
    Notification
         |
         v
    Customer Email
```

This workflow uses:

- Database per Service.
- Event-Driven Architecture.
- Message Broker.
- Transactional Outbox.
- Saga.
- Idempotent Consumers.
- Eventual Consistency.

---

# PART 29: COMMON MICROSERVICES MISTAKES

## 60. Mistakes to Avoid

| Mistake | Better Approach |
|---|---|
| Splitting every module into a service | Decompose around business capabilities |
| Creating too many small services | Keep services independently meaningful |
| Sharing all database tables | Establish service data ownership |
| Using synchronous communication everywhere | Use asynchronous messaging where appropriate |
| Ignoring network failures | Implement timeouts and resilience |
| Retrying every failed operation | Retry only appropriate transient failures |
| Ignoring duplicate messages | Implement idempotent consumers |
| Publishing events after database commit without coordination | Use the Outbox pattern |
| Ignoring event versioning | Maintain backward-compatible event contracts |
| Exposing internal services publicly | Use appropriate network and access controls |
| Ignoring distributed tracing | Implement correlation and tracing |
| Deploying without health checks | Add readiness and liveness checks |
| Assuming Docker Compose readiness from startup order | Use health checks and retry logic |
| Introducing Kubernetes too early | Start with infrastructure appropriate to the system |
| Ignoring Saga compensation | Design failure and recovery paths |
| Treating internal traffic as trusted | Authenticate and authorize service calls |

---

# PART 30: INTERVIEW QUESTIONS AND ANSWERS

## 61. Basic Questions

### Q1. What is microservices architecture?

It is an architectural approach in which an application is divided into smaller, independently deployable services organized around business capabilities.

### Q2. What is a monolithic application?

A monolithic application is deployed as one application unit, even if it contains multiple internal modules.

### Q3. What is the main advantage of microservices?

Independent deployment, scaling and ownership of business capabilities.

### Q4. What is the main disadvantage of microservices?

Increased distributed-system complexity, including network failures, data consistency and operational overhead.

### Q5. What is service decomposition?

It is the process of dividing an application into services with clear business responsibilities.

### Q6. What is Database per Service?

It is a pattern in which each microservice owns and manages its own data.

### Q7. What is inter-service communication?

It is communication between microservices through APIs, RPC or messaging.

### Q8. What is synchronous communication?

It is communication in which a caller waits for a response from another service.

### Q9. What is asynchronous communication?

It is communication in which a sender does not need to wait for the receiver to finish processing a message.

### Q10. What is a message broker?

It is infrastructure that receives, routes and delivers messages between producers and consumers.

## 62. Intermediate Questions

### Q11. What is RabbitMQ?

RabbitMQ is a message broker commonly used for queues, routing and asynchronous message delivery.

### Q12. What is Apache Kafka?

Kafka is a distributed event-streaming platform that stores and processes event streams.

### Q13. What is a Kafka topic?

A topic is a named stream of events.

### Q14. What is a Kafka partition?

A partition is an ordered log of records within a Kafka topic.

### Q15. What is a Kafka consumer group?

A consumer group allows multiple consumers to share processing of a topic's partitions.

### Q16. What is an exchange in RabbitMQ?

An exchange receives messages and routes them to queues according to its exchange type and bindings.

### Q17. What is a RabbitMQ acknowledgement?

It confirms that a message has been successfully handled by a consumer.

### Q18. What is Event-Driven Architecture?

It is an architecture in which components communicate by producing and consuming events.

### Q19. What is eventual consistency?

It means different parts of a distributed system may temporarily contain different states but are expected to converge.

### Q20. What is service discovery?

It is the mechanism used to locate available service instances dynamically.

## 63. Advanced Questions

### Q21. What is the API Gateway pattern?

It provides a single entry point for client requests and routes them to backend services.

### Q22. What is gRPC?

gRPC is a Remote Procedure Call framework commonly using HTTP/2 and Protocol Buffers.

### Q23. What is Protocol Buffers?

It is a language-neutral serialization format used to define structured data and service contracts.

### Q24. What is the Circuit Breaker pattern?

It prevents repeated calls to an unhealthy downstream service.

### Q25. What are the three Circuit Breaker states?

Closed, Open and Half-Open.

### Q26. What is the Bulkhead pattern?

It isolates resources so that one workload cannot exhaust resources needed by other workloads.

### Q27. What is the Saga pattern?

It coordinates multiple local transactions across services using compensating actions.

### Q28. What is the Outbox pattern?

It stores business data and the event to be published in the same local database transaction.

### Q29. Why is idempotent message processing important?

Because messages may be delivered more than once, and duplicate processing must not create duplicate business effects.

### Q30. What is distributed tracing?

It tracks a request across multiple services using traces and spans.

### Q31. What is a correlation ID?

It is an identifier used to associate related operations and log entries across components.

### Q32. What is the difference between logging and tracing?

Logging records events, while tracing tracks the journey and timing of operations across services.

### Q33. What is the difference between choreography and orchestration?

Choreography coordinates through events and independent service reactions. Orchestration uses a central coordinator to control the workflow.

### Q34. What is the dual-write problem?

It occurs when an application must update a database and publish a message, but the two operations cannot be committed atomically together.

### Q35. How does the Outbox pattern solve the dual-write problem?

It stores the business update and event record in the same local database transaction, then publishes the event asynchronously.

### Q36. Does Kafka guarantee exactly-once processing for every external system?

No. Kafka's exactly-once capabilities apply within defined supported processing boundaries. External side effects require additional coordination and idempotency.

### Q37. What is a distributed monolith?

It is a system with multiple deployed services that remain tightly coupled through dependencies and coordinated changes.

### Q38. What is a BFF?

Backend for Frontend is a backend layer tailored to the needs of a specific frontend client.

### Q39. What is mTLS?

Mutual TLS is a protocol configuration in which both client and server authenticate each other using certificates.

### Q40. When should you avoid microservices?

Avoid them when the application is small, the domain boundaries are unclear, the team is small or the operational complexity is not justified.

---

# PART 31: PRACTICAL ASSIGNMENTS

## 64. Assignment 1 – Monolith vs Microservices

Design an e-commerce application in two ways:

1. Modular monolithic architecture.
2. Microservices architecture.

Compare:

- Deployment.
- Scaling.
- Database ownership.
- Communication.
- Complexity.
- Failure handling.

## 65. Assignment 2 – Service Decomposition

Design these services:

```text
Auth Service
Product Service
Inventory Service
Order Service
Payment Service
Notification Service
```

Define:

- Responsibility.
- Database ownership.
- API endpoints.
- Events published.
- Events consumed.

## 66. Assignment 3 – REST Service Communication

Create two Express applications:

- Order Service.
- Inventory Service.

Implement a REST call from Order Service to Inventory Service.

Add:

- Request timeout.
- Error handling.
- Service authentication.
- Response validation.

## 67. Assignment 4 – gRPC

Create two services using Node.js and gRPC.

Implement:

```text
InventoryService
    |
    | CheckStock
    v
StockResponse
```

Learn how to:

- Define `.proto` files.
- Generate client/server code.
- Implement unary RPC.
- Handle errors.
- Configure deadlines.

## 68. Assignment 5 – RabbitMQ

Implement:

- Producer.
- Exchange.
- Queue.
- Binding.
- Consumer.
- Acknowledgements.
- Retry handling.
- Dead-letter queue.

Use an `order.created` event.

## 69. Assignment 6 – Kafka

Implement:

- Topic creation.
- Producer.
- Consumer.
- Consumer group.
- Multiple partitions.
- Event keys.
- Offset management.

Publish order events and process them in two independent consumer groups.

## 70. Assignment 7 – Event-Driven Architecture

Build a registration workflow.

When a user registers:

1. Auth Service creates the account.
2. Auth Service publishes `user.registered`.
3. Notification Service sends an email.
4. Analytics Service records the event.

## 71. Assignment 8 – Resilience Patterns

Implement:

- Timeout.
- Retry with exponential backoff.
- Jitter.
- Circuit Breaker.
- Bulkhead.
- Fallback.

Simulate an unavailable Payment Service.

## 72. Assignment 9 – Transactional Outbox

Implement:

- Orders table.
- Outbox events table.
- Order creation transaction.
- Background publisher.
- Broker confirmation.
- Retry mechanism.

Ensure that a committed order cannot be silently lost from the event-publishing workflow.

## 73. Assignment 10 – Saga

Implement an order workflow:

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

Add compensating actions for failures.

## 74. Assignment 11 – Observability

Implement:

- Structured JSON logging.
- Correlation IDs.
- Distributed tracing.
- Request latency metrics.
- Error-rate monitoring.

Track a request across at least three services.

## 75. Assignment 12 – Complete Microservices Project

Build a complete e-commerce microservices application.

### Required Services

- Auth Service.
- Product Service.
- Inventory Service.
- Order Service.
- Payment Service.
- Notification Service.

### Required Technologies

- Node.js.
- Express.js.
- TypeScript.
- MySQL.
- Redis.
- RabbitMQ or Kafka.
- Docker.
- API Gateway.
- OpenTelemetry.

### Required Features

**Architecture**

- Independent services.
- Database per Service.
- Service communication.
- Service discovery.

**Communication**

- REST.
- gRPC.
- Asynchronous messaging.

**Reliability**

- Retry.
- Timeout.
- Circuit Breaker.
- Bulkhead.
- Dead-letter queues.

**Data Consistency**

- Eventual consistency.
- Saga.
- Transactional Outbox.
- Idempotent consumers.

**Security**

- JWT.
- Service-to-service authentication.
- TLS.
- Secret management.

**Observability**

- Centralized logging.
- Metrics.
- Distributed tracing.

**Deployment**

- Docker.
- Docker Compose.
- Health checks.
- Graceful shutdown.

---

# PART 32: QUICK REVISION

## 76. Important Concepts Summary

| Concept | Key Point |
|---|---|
| Monolithic Architecture | One deployable application |
| Microservices | Independently deployable business services |
| Service Decomposition | Dividing applications into services |
| Bounded Context | Boundary of a domain model |
| Database per Service | Each service owns its data |
| Distributed Monolith | Multiple services with tight coupling |
| Synchronous Communication | Caller waits for response |
| Asynchronous Communication | Caller does not wait for processing |
| REST | HTTP-based resource communication |
| gRPC | RPC framework using HTTP/2 and commonly Protobuf |
| Protocol Buffers | Structured binary serialization format |
| Message Broker | Routes and delivers messages |
| RabbitMQ | Message broker |
| Kafka | Distributed event-streaming platform |
| Exchange | RabbitMQ message-routing component |
| Queue | Stores messages for consumers |
| Topic | Named Kafka event stream |
| Partition | Ordered log within a Kafka topic |
| Consumer Group | Group of consumers sharing partitions |
| Event | Record of something that happened |
| Command | Instruction requesting an operation |
| Event-Driven Architecture | Components communicate through events |
| Eventual Consistency | Data converges over time |
| Service Discovery | Locates service instances |
| API Gateway | Central client entry point |
| BFF | Backend tailored to a frontend |
| Distributed Logging | Centralized log collection |
| Distributed Tracing | Tracks requests across services |
| Timeout | Limits waiting time |
| Retry | Repeats an operation after eligible failures |
| Exponential Backoff | Increasing retry delays |
| Circuit Breaker | Blocks calls to failing services |
| Bulkhead | Isolates resources |
| Saga | Coordinates distributed local transactions |
| Choreography | Event-driven workflow coordination |
| Orchestration | Central workflow coordination |
| Outbox | Atomically stores business data and event |
| Idempotency | Prevents duplicate business effects |
| mTLS | Mutual certificate-based authentication |
| Docker | Containerization technology |
| Observability | Understanding system behavior through telemetry |

---

# PART 33: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

### Microservices Architecture

- [Microsoft – Design Patterns for Microservices](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/patterns)
- [AWS – Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/)
- [Microservices.io – Pattern Language](https://microservices.io/patterns/)

### RabbitMQ

- [RabbitMQ Documentation](https://www.rabbitmq.com/docs)
- [RabbitMQ Tutorials](https://www.rabbitmq.com/tutorials)

### Apache Kafka

- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Kafka Introduction](https://kafka.apache.org/documentation/#introduction)

### gRPC

- [gRPC Documentation](https://grpc.io/docs/)
- [Protocol Buffers Documentation](https://protobuf.dev/)

### Observability

- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)

### Node.js Microservices

- [NestJS Microservices Documentation](https://docs.nestjs.com/microservices/basics)
- [NestJS RabbitMQ Microservices Tutorial](https://docs.nestjs.com/tutorials/microservices-rabbitmq)

---

# PHASE 53 – COMPLETION CHECKLIST

Before considering this phase complete, make sure you can explain and implement the following.

### Architecture

- [ ] Understand monolithic architecture.
- [ ] Understand microservices architecture.
- [ ] Compare monoliths and microservices.
- [ ] Understand service decomposition.
- [ ] Understand bounded contexts.
- [ ] Understand Database per Service.
- [ ] Identify distributed monolith problems.

### Communication

- [ ] Understand synchronous communication.
- [ ] Understand asynchronous communication.
- [ ] Implement REST-based service communication.
- [ ] Understand gRPC.
- [ ] Understand Protocol Buffers.
- [ ] Understand message brokers.

### Messaging

- [ ] Understand RabbitMQ.
- [ ] Understand RabbitMQ exchanges.
- [ ] Understand queues and bindings.
- [ ] Understand message acknowledgements.
- [ ] Understand Kafka.
- [ ] Understand topics and partitions.
- [ ] Understand consumer groups.
- [ ] Understand delivery semantics.

### Distributed Systems

- [ ] Understand Event-Driven Architecture.
- [ ] Understand events vs commands.
- [ ] Understand eventual consistency.
- [ ] Understand service discovery.
- [ ] Understand API Gateway.
- [ ] Understand centralized configuration.

### Resilience

- [ ] Implement timeouts.
- [ ] Implement retry with exponential backoff.
- [ ] Understand jitter.
- [ ] Implement Circuit Breaker.
- [ ] Understand Bulkhead.
- [ ] Understand fallback mechanisms.

### Distributed Transactions

- [ ] Understand Saga.
- [ ] Understand choreography.
- [ ] Understand orchestration.
- [ ] Understand Transactional Outbox.
- [ ] Understand idempotent consumers.
- [ ] Understand duplicate message delivery.

### Deployment and Monitoring

- [ ] Build services using Docker.
- [ ] Run services using Docker Compose.
- [ ] Understand independent deployment.
- [ ] Understand service health checks.
- [ ] Understand distributed logging.
- [ ] Understand distributed tracing.
- [ ] Understand OpenTelemetry.
- [ ] Understand metrics and monitoring.

### Security

- [ ] Understand service-to-service authentication.
- [ ] Understand OAuth Client Credentials.
- [ ] Understand mTLS.
- [ ] Understand internal API security.
- [ ] Understand secret management.

---

# What Comes Next?

**Phase 54 – Advanced DevOps, Kubernetes, Service Mesh and Production Infrastructure**

The next phase will cover:

1. Advanced Docker Architecture.
2. Docker Networking.
3. Docker Volumes and Storage.
4. Multi-Stage Docker Builds.
5. Docker Security.
6. Kubernetes Architecture.
7. Pods, Deployments and ReplicaSets.
8. Services and Ingress.
9. ConfigMaps and Secrets.
10. Persistent Volumes and Storage Classes.
11. Kubernetes Networking.
12. Horizontal Pod Autoscaling.
13. Resource Requests and Limits.
14. Liveness, Readiness and Startup Probes.
15. Helm Charts.
16. Kubernetes Operators.
17. Service Mesh Architecture.
18. Istio Fundamentals.
19. mTLS with Service Mesh.
20. Traffic Management.
21. Canary Deployments.
22. Blue-Green Deployments.
23. GitOps.
24. Argo CD.
25. Infrastructure Monitoring.
26. Production Logging and Tracing.
27. Kubernetes Security.
28. Production Deployment Architecture.

**Phase 53 is complete. When you're ready, say "OK, Phase 54" and we'll continue with detailed notes.**