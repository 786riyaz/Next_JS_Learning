# **Phase 33 – Microservices Architecture with Node.js**



Advanced Level

System Design & Backend Engineering



**Technology context:** Node.js, TypeScript, Express, Next.js 16 App Router, MongoDB, MySQL, Redis, Docker, and message brokers.

## **1. Learning Objectives**

By the end of this phase, you should be able to:

1. Understand monolithic and microservices architectures.
2. Identify when an application should be split into independent services.
3. Design microservices around business capabilities.
4. Implement an API Gateway using Node.js.
5. Understand synchronous and asynchronous service communication.
6. Implement service discovery and centralized configuration.
7. Design authentication and authorization across services.
8. Apply the Database per Service pattern.
9. Understand distributed transactions and the Saga pattern.
10. Implement resilience patterns such as circuit breakers, retries, and timeouts.
11. Add centralized logging, metrics, and distributed tracing.
12. Containerize and deploy Node.js microservices using Docker.

## **2. What Is Software Architecture?**

Software architecture describes how an application is structured, how its components interact, and how responsibilities are distributed.

When building a small application, you might have:

- Frontend
- Backend
- Database

As the application grows, it may contain many different business modules:

- Authentication
- Users
- Products
- Orders
- Payments
- Inventory
- Notifications
- Shipping
- Reporting

The architectural question becomes:

**Should all these modules run inside one application, or should they be separated into independent services?**

Two major architectural approaches are:

1. Monolithic Architecture
2. Microservices Architecture

## **3. Monolithic Architecture**

A monolithic application is an application in which its main application components are developed and deployed as one unit.

For example, consider an e-commerce application.

It contains:

- Authentication module
- User module
- Product module
- Cart module
- Order module
- Payment module

In a monolithic architecture, these modules can share the same application process and often the same database.

[Monolithic vs. Microservices: The Great Architecture Debate | Last9](https://images.openai.com/static-rsc-4/6qsNwPDnwR9VcWcNSRyDUrgKoQEOTzVoHzkGT-lfHRW7zXvNcgUeRKAoBEsepmvKtyMGLfChhQCIYAI2skRALn2GfHsOC5nTEvVfPebWzIxXEIbsQykuTWrHPSn-Ize3CCrHVaL-q5we8WJD_nkRCTgV3BMDntmakLylgPBVaVA?purpose=inline)

[last9.io](https://last9.io/blog/monolithic-vs-microservices/?utm_source=chatgpt.com)

### **3.1 Example project structure**

```
ecommerce/
│
├── src/
│   ├── config/
│   │
│   ├── modules/
│   │   ├── auth/
│   │   ├── users/
│   │   ├── products/
│   │   ├── cart/
│   │   ├── orders/
│   │   ├── payments/
│   │   └── notifications/
│   │
│   ├── middleware/
│   ├── routes/
│   ├── utils/
│   │
│   └── server.ts
│
├── package.json
└── tsconfig.json
```

This is a modular monolith: the code is organized into modules, but the application is still deployed as one unit.

### **3.2 How it works**

```
                 Client
                   |
                   v
              Express App
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
     Users      Products     Orders
       |           |           |
       +-----------+-----------+
                   |
                   v
                Database
```

For example, when a user places an order:

1. The request reaches the Express application.
2. The order module validates the order.
3. The product module checks product information.
4. The inventory logic checks available stock.
5. The payment module processes payment.
6. The order is stored in the database.

These modules communicate through function calls or shared application interfaces.

### **3.3 Advantages**

- Simple initial development.
- Easy local debugging.
- Straightforward deployment.
- Lower infrastructure complexity.
- Internal function calls avoid network communication.
- Database transactions can be easier when modules share a database.

### **3.4 Disadvantages**

- The application can become difficult to maintain if modules are tightly coupled.
- A change to one module may require deploying the entire application.
- Scaling one business capability independently is difficult.
- A failure in a shared process can affect the entire application.
- Large codebases can become harder for multiple teams to work on independently.

**Important:** A monolith is not inherently a bad architecture. A well-designed modular monolith can be a very effective production architecture.

## **4. Microservices Architecture**

Microservices architecture structures an application as a collection of small, independently deployable services.

Each service is responsible for a specific business capability.

For example:

| Service              | Responsibility                      |
| -------------------- | ----------------------------------- |
| Auth Service         | Registration, login, authentication |
| User Service         | User profiles and preferences       |
| Product Service      | Product catalog                     |
| Cart Service         | Shopping cart                       |
| Order Service        | Order creation and management       |
| Payment Service      | Payment processing                  |
| Inventory Service    | Stock management                    |
| Notification Service | Email, SMS, push notifications      |

[A Pragmatic Journey from Monolith to Microservices | by Mehmet Ozkaya | Medium](https://images.openai.com/static-rsc-4/ZzpiN487ZhjMKjzKvWwKMWN-PkCGsV2uiOLGu4Sm3cZU3hL67cCWzO4DvthxXnRQo6Vjn97wLzSu_YSuQq8u5WoBbe1eD8_B8j4NfKuP61hOI3kx4pr9cah0lnmeKk6XApBNW6oKw2gbXuncIQmGi_jmPbgkVztN0JxRx576kOc?purpose=inline)

[mehmetozkaya.medium.com](https://mehmetozkaya.medium.com/a-pragmatic-journey-from-monolith-to-microservices-309271d8e54d?utm_source=chatgpt.com)

Each service can have:

- Its own codebase or independently managed module.
- Its own deployment lifecycle.
- Its own API.
- Its own data ownership.
- Its own scaling requirements.

Microservices communicate over network protocols, such as HTTP, gRPC, or asynchronous messaging.

### **4.1 Example architecture**

```
                    Client
                      |
                      v
                 API Gateway
                      |
       +--------------+--------------+
       |              |              |
       v              v              v
   Auth Service  Product Service  Order Service
       |              |              |
       v              v              v
    Auth DB        Product DB       Order DB
                                      |
                                      v
                               Payment Service
                                      |
                                      v
                                  Payment DB
```

This is a simplified diagram. A real system may also include an event broker, service discovery, observability infrastructure, and separate infrastructure for each service.

### **4.2 Example request flow**

Suppose a customer places an order.

1. The client sends a request to the API Gateway.
2. The Gateway routes the request to the Order Service.
3. The Order Service validates the request and customer identity.
4. It communicates with the Inventory Service to reserve stock.
5. It coordinates payment with the Payment Service.
6. It records the order and its status.
7. An event may be published to the Notification Service.
8. The client receives the order response.

Notice that services communicate over network boundaries rather than simply calling one another's internal functions.

This introduces new engineering challenges: network failures, latency, partial failures, retries, duplicate messages, and distributed consistency.

## **5. Monolith vs Microservices**

Let's compare both architectures carefully.

| Feature           | Monolithic                           | Microservices                              |
| ----------------- | ------------------------------------ | ------------------------------------------ |
| Deployment        | Usually one application unit         | Multiple independently deployable services |
| Code organization | Modules inside one application       | Separate service boundaries                |
| Communication     | Often function calls                 | HTTP, gRPC, messaging                      |
| Database          | Often shared                         | Each service owns its data                 |
| Scaling           | Usually scale the whole application  | Scale individual services                  |
| Failure isolation | Process-level isolation is limited   | Can isolate failures between services      |
| Development       | Simpler initially                    | More infrastructure and coordination       |
| Testing           | Easier integration testing initially | Requires service and contract testing      |
| Monitoring        | Comparatively simple                 | Distributed observability required         |
| Data consistency  | Often easier with local transactions | Requires distributed consistency patterns  |

### **5.1 When should you use a monolith?**

A monolith is often a good choice when:

- You are building a new product.
- The development team is small.
- Business requirements are changing rapidly.
- The application has limited traffic.
- You do not need independent service deployments.
- Operational simplicity is important.

### **5.2 When should you use microservices?**

Microservices may be appropriate when:

- Different business capabilities have different scaling requirements.
- Multiple teams need independent ownership and deployments.
- Services have clearly defined business boundaries.
- The organization can operate distributed infrastructure.
- Fault isolation and independent release cycles are important.

### **5.3 Important: Microservices are not automatically better**

Microservices introduce:

- Network latency.
- Distributed failures.
- More deployments.
- Service discovery.
- Distributed authentication.
- Data consistency challenges.
- Distributed tracing.
- More infrastructure cost.

For a small application, microservices may create unnecessary complexity.

**Professional recommendation:** Start with a well-structured modular monolith unless there is a clear business or technical reason to split services. You can extract services gradually when their boundaries and scaling needs become clear.

Microsoft's architecture guidance also emphasizes that microservices require more than breaking an application into smaller codebases: deployment, data ownership, communication, and operations must all be designed for independence.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **6. Microservices Principles and Characteristics**

A well-designed microservice generally follows these principles.

### **6.1 Single Business Capability**

Each service should focus on a specific business responsibility.

Example:

```
Product Service
    |
    +-- Create Product
    +-- Update Product
    +-- Delete Product
    +-- Get Product
    +-- Search Products
```

The Product Service should not also be responsible for payment processing or sending OTPs.

### **6.2 Independent Deployment**

A service should be deployable without requiring all other services to be redeployed.

For example:

```
Product Service v1.0
Order Service v2.0
Payment Service v1.5
```

You should be able to deploy a new Product Service version while the other services continue running, provided API and event compatibility are maintained.

### **6.3 Independent Scaling**

Suppose your application receives:

- 10,000 product requests per minute.
- 500 order requests per minute.
- 100 payment requests per minute.

You may need more Product Service instances than Payment Service instances.

```
Product Service: 5 instances
Order Service:   3 instances
Payment Service: 2 instances
```

The exact number depends on workload, resource requirements, and measured capacity.

### **6.4 Loose Coupling**

Services should minimize unnecessary dependencies on each other's internal implementation.

For example, the Order Service should not directly import internal classes from the Payment Service.

Instead, communicate through a stable API or message contract.

### **6.5 High Cohesion**

Related business logic should stay together.

For example, payment authorization, payment status, and payment-provider integration belong in the Payment Service rather than being scattered across unrelated services.

### **6.6 Data Ownership**

Each service should own its business data.

The Order Service owns order records. The Product Service owns product catalog records.

Other services should access that information through published interfaces or replicated data rather than directly modifying another service's database.

These principles are closely related to bounded contexts and independent service boundaries.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **7. Service Decomposition**

Service decomposition means deciding how to divide an application into microservices.

This is one of the most important architectural decisions.

### **7.1 Decomposition by technical layer — usually a poor boundary**

Imagine splitting an application like this:

```
Frontend Service
Backend Service
Database Service
```

This is not necessarily microservices architecture.

These are technical layers, not independently owned business capabilities.

A change to a product feature might still require coordination across all three.

### **7.2 Decomposition by business capability**

A better approach is to identify the main business capabilities.

For e-commerce:

```
E-commerce
    |
    +-- Customer Management
    +-- Product Catalog
    +-- Shopping Cart
    +-- Order Management
    +-- Payment Processing
    +-- Inventory Management
    +-- Shipping
    +-- Notifications
```

Each capability may become a service if there is a genuine reason for independent ownership.

### **7.3 Decomposition using DDD bounded contexts**

A bounded context defines a boundary within which a particular domain model and business language apply.

For example:

- Product Context
- Ordering Context
- Payment Context
- Shipping Context

A term may have different meanings in different contexts.

For example, an `Order` in the Ordering Context may represent the complete customer purchase, while a `Shipment` in the Shipping Context represents the fulfillment details associated with that order.

You should not assume every bounded context must become a separate microservice. A bounded context can initially be implemented as a module inside a modular monolith.

### **7.4 Avoid creating too many services**

A common beginner mistake is creating a separate service for every entity:

```
Product Service
Product Image Service
Product Category Service
Product Brand Service
Product Price Service
```

This can create excessive network communication and operational complexity.

Instead, consider business cohesion, ownership, deployment requirements, and scaling requirements.

### **7.5 Service boundary checklist**

Before creating a new service, ask:

1. Does it represent a meaningful business capability?
2. Does it have clear ownership?
3. Can it manage its data independently?
4. Does it need independent scaling?
5. Does it need an independent release cycle?
6. Are its interactions with other services well-defined?
7. Is the operational cost justified?

## **8. API Gateway Pattern**

Very important

An API Gateway is a single entry point through which clients access multiple backend services.

Without an API Gateway, a frontend may need to know the addresses of many services.

```
Client
  |
  +----> Auth Service
  |
  +----> Product Service
  |
  +----> Order Service
  |
  +----> Payment Service
```

This creates several problems:

- The frontend must know multiple service URLs.
- Authentication logic may be duplicated.
- CORS and rate limiting need to be managed across services.
- Service addresses become exposed to clients.

With an API Gateway:

[API Development Series Part 10: API Architecture Patterns - Wasil Zafar](https://images.openai.com/static-rsc-4/2t2mfE8TGA8KEOxORVvXGlUZhOaI0xm2m7269txDbLZ8AjLfvBn1NEMaDdZU5gWpU46o2KZ-RXjnhZAeKSAwsAn7YfVjCDes4UTjisWYPlJOYOKg4qkT9a26Id-PrqTsYG1_l-M1aPFPo0eCW88HiVq6_oBU5CqNyHHuzTgWyzw?purpose=inline)

[wasilzafar.com](https://www.wasilzafar.com/pages/series/api-development/api-dev-architecture-patterns.html?utm_source=chatgpt.com)

```
             Client
                |
                v
           API Gateway
                |
       +--------+--------+
       |        |        |
       v        v        v
      Auth   Product   Order
     Service Service  Service
```

The client communicates with one public endpoint.

For example:

```
https://api.example.com/api/auth/login
https://api.example.com/api/products
https://api.example.com/api/orders
```

The Gateway routes requests to internal services.

### **8.1 Responsibilities of an API Gateway**

An API Gateway may handle:

- Request routing.
- Authentication checks.
- Rate limiting.
- TLS termination.
- Request logging.
- CORS configuration.
- Response aggregation.
- API version routing.
- Request size limits.

However, the Gateway should not become the location for all business logic. Services must still enforce their own authorization and business rules.

### **8.2 Build a simple API Gateway using Express**

Install dependencies:

```
npm install express http-proxy-middleware
npm install -D typescript @types/node @types/express
```

Project structure:

```
api-gateway/
│
├── src/
│   └── server.ts
│
├── package.json
└── tsconfig.json
```

`src/server.ts`

```
import express from "express";
import { createProxyMiddleware } from "http-proxy-middleware";

const app = express();

const PRODUCT_SERVICE_URL =
  process.env.PRODUCT_SERVICE_URL ??
  "http://localhost:4001";

const ORDER_SERVICE_URL =
  process.env.ORDER_SERVICE_URL ??
  "http://localhost:4002";

app.use(
  "/api/products",
  createProxyMiddleware({
    target: PRODUCT_SERVICE_URL,
    changeOrigin: true,
    pathRewrite: {
      "^/api/products": "/products",
    },
  })
);

app.use(
  "/api/orders",
  createProxyMiddleware({
    target: ORDER_SERVICE_URL,
    changeOrigin: true,
    pathRewrite: {
      "^/api/orders": "/orders",
    },
  })
);

app.get("/health", (_req, res) => {
  res.json({
    status: "healthy",
    service: "api-gateway",
  });
});

app.listen(4000, () => {
  console.log("API Gateway running on port 4000");
});
```

Now requests are routed like this:

| Client request          | Destination                     |
| ----------------------- | ------------------------------- |
| `GET /api/products`     | Product Service `/products`     |
| `GET /api/products/101` | Product Service `/products/101` |
| `GET /api/orders`       | Order Service `/orders`         |

This is a simplified routing example, not a complete production gateway. A production gateway also needs authentication, authorization strategy, timeouts, request limits, observability, and appropriate error handling.

### **8.3 API Gateway vs Reverse Proxy**

A reverse proxy forwards requests to backend servers.

An API Gateway is an architectural entry point that can include reverse-proxy routing plus API-specific features such as authentication, rate limiting, aggregation, and API version management.

A reverse proxy can be part of an API Gateway implementation, but the concepts are not identical.

## **9. Inter-Service Communication**

Microservices need to communicate to complete business workflows.

There are two major communication approaches:

1. Synchronous communication.
2. Asynchronous communication.

### **9.1 Synchronous communication**

A service sends a request to another service and waits for a response.

Example:

```
Order Service
      |
      | HTTP Request
      v
Inventory Service
      |
      | HTTP Response
      v
Order Service
```

Common protocols:

- HTTP/REST
- gRPC

Example:

```
const response = await fetch(
  "http://inventory-service:4003/inventory/101"
);

if (!response.ok) {
  throw new Error("Inventory service failed");
}

const inventory = await response.json();
```

The Order Service cannot complete this particular operation until it receives a response or the request fails or times out.

**Use synchronous communication when:**

- An immediate response is required.
- The operation needs data from another service now.
- The dependency is limited and carefully controlled.

### **9.2 Asynchronous communication**

A service sends a message or event without waiting for another service to finish processing it.

Example:

```
Order Service
      |
      | Publish ORDER_CREATED
      v
   Message Broker
      |
      +-------------------+
      |                   |
      v                   v
Notification Service  Analytics Service
```

Common technologies:

- RabbitMQ
- Apache Kafka
- Redis Streams
- Cloud message queues

Example event:

```
{
  "eventId": "evt-101",
  "eventType": "ORDER_CREATED",
  "orderId": "order-101",
  "customerId": "user-25"
}
```

The Notification Service can consume this event and send an email without delaying the initial order response.

### **9.3 Synchronous vs asynchronous communication**

| Feature                   | Synchronous              | Asynchronous                         |
| ------------------------- | ------------------------ | ------------------------------------ |
| Caller waits for response | Yes                      | Usually no                           |
| Typical protocol          | HTTP, gRPC               | Messaging, event broker              |
| Coupling                  | More runtime dependency  | Often lower runtime dependency       |
| Immediate result          | Available                | Not necessarily                      |
| Failure handling          | Timeout, retry, fallback | Retry, redelivery, dead-letter queue |
| Example                   | Check stock              | Send order notification              |

**Important distinction:** Using `async/await` in Node.js does not automatically make HTTP communication architecturally asynchronous.

An HTTP request can use non-blocking asynchronous I/O in JavaScript while still being synchronous request/response communication at the system level.

Microsoft's inter-service communication guidance explains this distinction and the trade-offs between request/response APIs and asynchronous messaging.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



### **9.4 Avoid long synchronous call chains**

Consider:

```
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

If the entire chain must succeed before the client gets a response, every additional dependency can increase latency and create another failure point.

A better design may use synchronous communication for essential immediate decisions and asynchronous events for downstream work.

For example, sending an email should generally not be necessary to complete an order-placement HTTP response.

## **10. Service Discovery**

In a microservices environment, services need to find the network locations of other services.

This is called **service discovery**.

Suppose you have:

```
Product Service
Order Service
Payment Service
Inventory Service
```

The Order Service needs to communicate with the Inventory Service.

How does it know where the Inventory Service is running?

### **10.1 Static service discovery**

You manually configure the service URL.

```
INVENTORY_SERVICE_URL=http://localhost:4003
```

Then:

```
const inventoryUrl =
  process.env.INVENTORY_SERVICE_URL;

const response = await fetch(
  `${inventoryUrl}/inventory/101`
);
```

Advantages:

- Simple.
- Easy to understand.
- Suitable for small systems and local development.

Disadvantages:

- URLs must be maintained manually.
- Scaling and changing instances require configuration updates.
- Less convenient in dynamic environments.

### **10.2 Dynamic service discovery**

A service registry maintains information about available service instances.

```
              Service Registry
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Product     Order     Inventory
       Service     Service    Service
```

A service registers its address. Other services discover it through the registry or a discovery mechanism.

Common technologies include:

- Consul
- Kubernetes
- Cloud-provider service discovery systems.

### **10.3 Docker Compose service discovery**

You do not always need a dedicated service registry.

Docker Compose creates a default network for an application. Containers can generally reach one another using their Compose service names.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.docker.com\&sz=32)

Docker Docs



For example:

```
services:
  product-service:
    build: ./product-service
    ports:
      - "4001:4001"

  order-service:
    build: ./order-service
    ports:
      - "4002:4002"
    environment:
      INVENTORY_SERVICE_URL: http://inventory-service:4003

  inventory-service:
    build: ./inventory-service
    ports:
      - "4003:4003"
```

Inside the `order-service` container, use:

```
http://inventory-service:4003
```

Do not use `localhost:4003` to reach another container. Inside a container, `localhost` refers to that same container.

### **10.4 Service discovery vs load balancing**

These are related but different concepts.

- Service discovery finds available service instances.
- Load balancing distributes requests across available instances.

Example:

```
Order Service
      |
      v
Service Discovery
      |
      v
Inventory Instances
   |       |       |
   v       v       v
  I-1     I-2     I-3
```

A load balancer may distribute requests among the discovered instances.

## **11. Centralized Configuration**

In a microservices architecture, many services need configuration values.

Examples:

- Database URLs.
- Redis connection strings.
- Message broker addresses.
- JWT verification settings.
- External API credentials.
- Feature flags.
- Service URLs.

If configuration is scattered across multiple files and deployments, managing it becomes difficult.

### **11.1 Basic configuration using environment variables**

Each service can have its own `.env` file.

Product Service:

```
PORT=4001
MONGODB_URI=mongodb://localhost:27017/products
```

Order Service:

```
PORT=4002
MONGODB_URI=mongodb://localhost:27017/orders
INVENTORY_SERVICE_URL=http://localhost:4003
```

Payment Service:

```
PORT=4004
PAYMENT_PROVIDER_SECRET=secret-value
```

Never commit actual production secrets to Git.

### **11.2 Centralized configuration management**

For larger systems, configuration may be managed through a dedicated configuration platform.

Examples:

- HashiCorp Consul
- AWS Systems Manager Parameter Store
- AWS Secrets Manager
- Kubernetes ConfigMaps and Secrets.

### **11.3 Configuration vs secrets**

| Configuration      | Secret             |
| ------------------ | ------------------ |
| Service port       | Database password  |
| Log level          | JWT signing key    |
| Feature flag       | Payment API secret |
| Public service URL | Private API token  |

Secrets require stronger access controls, encryption, rotation, and careful handling.

## **12. Distributed Authentication and Authorization**

Authentication becomes more interesting when an application contains multiple services.

Suppose a user logs into your application.

The Auth Service verifies the user's credentials and issues an access token.

Other services must then establish whether the token is valid and what the user is allowed to do.

### **12.1 JWT-based architecture**

```
              Client
                 |
                 v
            API Gateway
                 |
                 v
             Auth Service
                 |
                 v
            Access Token
                 |
       +---------+---------+
       |         |         |
       v         v         v
    Product    Order    Payment
    Service   Service   Service
```

### **12.2 Authentication flow**

1. User submits email and password.
2. Auth Service verifies the credentials.
3. Auth Service issues a signed access token.
4. Client sends the token with API requests.
5. Gateway or service verifies the token.
6. The receiving service checks permissions before executing the operation.

Example request:

```
GET /products/101
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### **12.3 JWT verification in Node.js**

Install:

```
npm install jose
```

Example shared verification function:

```
import { jwtVerify } from "jose";

const secret = process.env.JWT_SECRET;

if (!secret) {
  throw new Error("JWT_SECRET is required");
}

const secretKey = new TextEncoder().encode(secret);

export async function verifyAccessToken(
  token: string
) {
  const { payload } = await jwtVerify(
    token,
    secretKey,
    {
      algorithms: ["HS256"],
      issuer: "auth-service",
      audience: "ecommerce-api",
    }
  );

  return payload;
}
```

In a real system, use an asymmetric signing approach when appropriate:

- Auth Service holds the private signing key.
- Other services receive the public verification key.
- Other services can verify tokens without possessing the signing secret.

You must also validate claims such as issuer, audience, expiration, and the required identity and authorization attributes.

### **12.4 Gateway authentication vs service authentication**

A common mistake is assuming that authentication at the API Gateway is enough.

The Gateway is an entry point, but services may also be reachable through internal networks, background workers, or other service-to-service paths.

Each service must enforce its own authorization rules.

For example:

```
if (user.role !== "ADMIN") {
  throw new Error("Forbidden");
}
```

Do not trust a client-provided role or user ID. Obtain identity and authorization information from verified credentials or trusted internal mechanisms.

### **12.5 Service-to-service authentication**

Services also need to authenticate one another.

Common options include:

- OAuth 2.0 client credentials.
- Short-lived service tokens.
- Mutual TLS (mTLS).
- Workload identity provided by the hosting platform.

Use least-privilege permissions so that a service receives only the access it needs.

## **13. Database per Service Pattern**

Important architecture principle

The Database per Service pattern means each microservice owns its data store and controls access to it.

Example:

```
Product Service
      |
      v
  Product DB

Order Service
      |
      v
   Order DB

Payment Service
      |
      v
  Payment DB

User Service
      |
      v
   User DB
```

### **13.1 Why should each service own its database?**

Suppose the Order Service directly queries the Product Service's database.

```
Order Service
      |
      v
Product Database
```

This creates tight coupling.

If the Product Service changes its schema, the Order Service may break.

Instead:

```
Order Service
      |
      v
Product Service API
      |
      v
Product Database
```

Or the Product Service can publish product events that allow the Order Service to maintain its own local copy of the fields it needs.

### **13.2 Can different services use different databases?**

Yes.

For example:

| Service              | Database       |
| -------------------- | -------------- |
| Product Service      | MongoDB        |
| Order Service        | PostgreSQL     |
| Payment Service      | MySQL          |
| Notification Service | MongoDB        |
| Analytics Service    | Data warehouse |

Each service can choose storage technology appropriate to its needs, although using many database technologies increases operational complexity.

### **13.3 Why direct cross-service database access is problematic**

It creates:

- Schema coupling.
- Deployment dependencies.
- Security problems.
- Unclear data ownership.
- Difficult migrations.
- Hidden dependencies between services.

### **13.4 How do services get data from other services?**

There are several approaches:

1. Call another service's API.
2. Consume its published events.
3. Maintain a local read model.
4. Use API composition to combine responses.

For example, the Order Service might store:

```
{
  "orderId": "order-101",
  "customerId": "user-25",
  "productId": "product-101",
  "productName": "Laptop",
  "unitPriceAtPurchase": 75000,
  "quantity": 1
}
```

Notice that `unitPriceAtPurchase` is a snapshot of the price at the time of purchase. The order should not automatically change when the current catalog price changes.

### **13.5 Distributed transactions**

When each service owns a separate database, a traditional local database transaction cannot atomically cover every service's database.

For example:

```
Order DB
   |
   v
Payment DB
   |
   v
Inventory DB
```

A failure in one service can leave the overall business workflow partially completed.

Common solutions include:

- Saga pattern.
- Compensating transactions.
- Transactional Outbox.
- Idempotent message processing.
- Eventual consistency.

You studied these concepts in the earlier distributed systems phases; they are essential to microservices.

## **14. API Composition and Aggregation**

Sometimes the frontend needs data from multiple services to display one page.

For example, an order details page might need:

- Order information.
- Customer information.
- Product information.
- Payment status.

Without aggregation:

```
Frontend
   |
   +----> Order Service
   |
   +----> User Service
   |
   +----> Product Service
   |
   +----> Payment Service
```

The frontend makes multiple requests and combines the results.

With API composition:

```
Frontend
   |
   v
API Gateway / Aggregator
   |
   +----> Order Service
   |
   +----> User Service
   |
   +----> Product Service
   |
   +----> Payment Service
   |
   v
Combined Response
```

### **14.1 Example using Node.js**

```
app.get("/api/order-details/:id", async (req, res) => {
  try {
    const { id } = req.params;

    const orderResponse = await fetch(
      `http://order-service:4002/orders/${id}`
    );

    if (!orderResponse.ok) {
      return res.status(orderResponse.status).json({
        message: "Unable to retrieve order",
      });
    }

    const order = await orderResponse.json();

    const [userResponse, productResponse] =
      await Promise.all([
        fetch(
          `http://user-service:4003/users/${order.userId}`
        ),
        fetch(
          `http://product-service:4001/products/${order.productId}`
        ),
      ]);

    if (!userResponse.ok || !productResponse.ok) {
      return res.status(502).json({
        message: "Unable to retrieve order details",
      });
    }

    const [user, product] = await Promise.all([
      userResponse.json(),
      productResponse.json(),
    ]);

    return res.json({
      order,
      user,
      product,
    });
  } catch {
    return res.status(502).json({
      message: "A downstream service is unavailable",
    });
  }
});
```

This is a teaching example. A production implementation should add timeouts, authorization propagation, request cancellation, response validation, tracing, and explicit partial-failure behavior.

### **14.2 API composition vs API Gateway**

| API Gateway                           | API Composition                      |
| ------------------------------------- | ------------------------------------ |
| Provides a common entry point         | Combines data from multiple services |
| Routes incoming requests              | Aggregates multiple responses        |
| Can provide cross-cutting concerns    | Can provide a composed response      |
| May be implemented as a reverse proxy | Usually requires application logic   |

A Gateway can also implement API composition, but these are distinct responsibilities.

**Warning:** Do not create an aggregator that synchronously calls dozens of services for every request. This creates a chatty architecture and makes the response dependent on many downstream services.

For complex read-heavy workflows, consider materialized views or CQRS.

## **15. Resilience Patterns**

Distributed systems fail in ways that ordinary single-process applications do not.

A service can fail because:

- Its server is down.
- Its database is unavailable.
- A network connection times out.
- It is overloaded.
- DNS resolution fails.
- A dependent service responds too slowly.

Microservices must be designed to handle partial failures.

### **15.1 Timeout Pattern**

A timeout limits how long a service waits for another service.

Without a timeout:

```
const response = await fetch(serviceUrl);
```

A request could wait much longer than your application can tolerate.

Use an explicit timeout:

```
const response = await fetch(serviceUrl, {
  signal: AbortSignal.timeout(3000),
});
```

Here, the request is aborted after approximately three seconds.

A timeout does not guarantee that the downstream service stopped processing. It only means the caller stopped waiting.

### **15.2 Retry Pattern**

A retry means attempting an operation again after a temporary failure.

Example:

```
async function fetchWithRetry(
  url: string,
  attempts = 3
) {
  for (let attempt = 1; attempt <= attempts; attempt++) {
    try {
      const response = await fetch(url, {
        signal: AbortSignal.timeout(3000),
      });

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }

      return response;
    } catch (error) {
      if (attempt === attempts) {
        throw error;
      }

      const delay = 500 * 2 ** (attempt - 1);

      await new Promise((resolve) =>
        setTimeout(resolve, delay)
      );
    }
  }
}
```

This illustrates exponential backoff:

```
Attempt 1 -> wait 500 ms
Attempt 2 -> wait 1000 ms
Attempt 3 -> wait 2000 ms
```

Production retry policies should also use jitter, classify retryable errors, enforce an overall deadline, and avoid retrying non-idempotent operations blindly.

### **15.3 Circuit Breaker Pattern**

Very important

A Circuit Breaker prevents an application from repeatedly calling a failing service.

It has three common states:

1. Closed
2. Open
3. Half-open

[Spring Cloud in One Shot — The Ultimate Cheatsheet | Coding Shuttle](https://images.openai.com/static-rsc-4/U6YBKnmGdNYF0_-_2V6dwkldRYSU9s8wMonmxcKyYD8VWR-np3QiN6aw-CwJjCIsZBXdwkpCGxKri6hzyvc7eT29wqyybbl8ZjLQZe-iSxOhhUJSQQDsFIjek2uIse6VC6hOs8TlsOz9xcsJKm06zKqxxHDGD_q_8TLyH0oD4mM?purpose=inline)

[codingshuttle.com](https://www.codingshuttle.com/blogs/spring-cloud-explained-from-zero-to-production-microservices-cheat-sheet/?utm_source=chatgpt.com)

**Closed state**

Requests are allowed to reach the service.

```
Application ---> Payment Service
```

**Open state**

The service has failed repeatedly, so the circuit breaker rejects requests without calling it.

```
Application ---> Circuit Breaker
                       |
                       v
                  Reject Request
```

**Half-open state**

After a waiting period, the circuit breaker permits a limited test request.

- If the request succeeds, the breaker closes.
- If the request fails, it opens again.

### **15.4 Implement a Circuit Breaker in Node.js**

You can use a library such as `opossum`.

Install:

```
npm install opossum
npm install -D @types/opossum
```

Example:

```
import CircuitBreaker from "opossum";

async function callPaymentService(
  orderId: string
) {
  const response = await fetch(
    "http://payment-service:4004/payments",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ orderId }),
      signal: AbortSignal.timeout(3000),
    }
  );

  if (!response.ok) {
    throw new Error("Payment service failed");
  }

  return response.json();
}

const paymentBreaker = new CircuitBreaker(
  callPaymentService,
  {
    timeout: 3500,
    errorThresholdPercentage: 50,
    resetTimeout: 10000,
    volumeThreshold: 5,
  }
);

paymentBreaker.on("open", () => {
  console.log("Payment circuit opened");
});

paymentBreaker.on("halfOpen", () => {
  console.log("Payment circuit half-open");
});

paymentBreaker.on("close", () => {
  console.log("Payment circuit closed");
});

export async function processPayment(
  orderId: string
) {
  return paymentBreaker.fire(orderId);
}
```

The configuration is illustrative; tune thresholds using observed traffic and failure behavior.

**Critical payment warning:** Retrying a payment request can cause duplicate charges if the first request succeeded but its response was lost. Use idempotency keys and payment-provider reconciliation. A circuit breaker does not solve duplicate transaction problems.

### **15.5 Bulkhead Pattern**

The bulkhead pattern isolates resources so one failing workload does not consume all resources needed by other workloads.

For example:

```
Application
    |
    +---- Product connection pool
    |
    +---- Payment connection pool
    |
    +---- Notification worker pool
```

If the Notification Service becomes slow, it should not exhaust all available resources and prevent payments from being processed.

Ways to implement isolation:

- Separate connection pools.
- Separate worker queues.
- Concurrency limits.
- Dedicated service instances.
- Resource limits at the container level.

### **15.6 Fallback Pattern**

A fallback provides an alternative response when a dependency fails.

Example:

If a recommendation service is unavailable, the product page can still display the product without personalized recommendations.

```
async function getRecommendations(userId: string) {
  try {
    return await fetchRecommendations(userId);
  } catch {
    return [];
  }
}
```

This is appropriate only if an empty recommendation list is an acceptable business fallback.

### **Resilience patterns comparison**

| Pattern         | Purpose                                  |
| --------------- | ---------------------------------------- |
| Timeout         | Stop waiting indefinitely                |
| Retry           | Recover from transient failures          |
| Circuit Breaker | Stop repeatedly calling failing services |
| Bulkhead        | Isolate resource usage                   |
| Fallback        | Provide an alternative response          |
| Rate Limiting   | Control request volume                   |
| Idempotency     | Prevent duplicate effects                |

Microsoft's microservices design guidance describes circuit breakers, retries, bulkheads, and Saga as important patterns for distributed reliability.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

GitHub+2



## **16. Distributed Logging, Monitoring and Tracing**

When you have one application, debugging is relatively straightforward: inspect its logs.

With microservices, a single user request may travel through multiple services.

For example:

```
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
  |
  v
Notification Service
```

If the request fails, which service caused the failure?

This is why observability is essential.

### **16.1 Centralized logging**

Centralized logging means collecting logs from multiple services in a common location.

Example:

```
Product Service ----+
Order Service ------+----> Central Log Platform
Payment Service ----+
Inventory Service --+
```

Common tools:

- ELK Stack
- Grafana Loki
- Datadog

Use structured JSON logs.

Example:

```
console.log(
  JSON.stringify({
    timestamp: new Date().toISOString(),
    level: "info",
    service: "order-service",
    message: "Order created",
    orderId: "order-101",
  })
);
```

Avoid logging passwords, access tokens, payment credentials, and other sensitive information.

### **16.2 Correlation ID**

A correlation ID helps identify all logs related to one request or business operation.

Example:

```
Request ID: req-101
     |
     +---- API Gateway
     |
     +---- Order Service
     |
     +---- Inventory Service
     |
     +---- Payment Service
```

Each service should propagate the request context to downstream services.

Example:

```
const response = await fetch(
  "http://inventory-service:4003/inventory/101",
  {
    headers: {
      "x-correlation-id": correlationId,
    },
  }
);
```

Use a trusted middleware to generate or validate the correlation ID rather than blindly trusting arbitrary client-provided values.

### **16.3 Metrics**

Metrics are numerical measurements that describe application behavior.

Examples:

| Metric             | Meaning                           |
| ------------------ | --------------------------------- |
| Request count      | Number of requests                |
| Error rate         | Percentage of failed requests     |
| Request latency    | Time taken to respond             |
| CPU usage          | CPU consumption                   |
| Memory usage       | RAM consumption                   |
| Queue depth        | Number of pending messages        |
| Active connections | Current connections               |
| Database latency   | Time taken by database operations |

A popular monitoring stack is:

```
Services
   |
   v
Prometheus
   |
   v
Grafana
```

### **16.4 Distributed Tracing**

Distributed tracing tracks a request as it travels across multiple services.

[System Design Series Part 10: Monitoring & Observability - Wasil Zafar](https://images.openai.com/static-rsc-4/Mermx16W1wLZuJRFgewqDV7oguFzQ3sihuxSbqFiFAjWm3CbN_V3PW2v8GAWMXN0_baBcG2-pau-v1AIA3Xnj1_7Hdbt8m2QahaMeM31S8yW3G19tWllUyjR6qCOLGFfKwj7Nc5V6sSVD_huXHzNX_gszHlyu9zsQx2YUM0aK_8?purpose=inline)

[wasilzafar.com](https://www.wasilzafar.com/pages/series/system-design/system-design-monitoring-observability.html?utm_source=chatgpt.com)

For example:

```
Request: req-101

API Gateway       20 ms
Order Service     50 ms
Inventory Service 80 ms
Payment Service   400 ms
```

This helps identify which service contributes most to latency.

### **16.5 OpenTelemetry**

OpenTelemetry is a vendor-neutral framework for generating, collecting, and exporting telemetry.

It supports:

- Traces
- Metrics
- Logs

For Node.js, start with the official instrumentation documentation:

[OpenTelemetry JavaScript documentation](https://opentelemetry.io/docs/languages/js/?utm_source=chatgpt.com).

A typical architecture:

```
Node.js Services
       |
       v
OpenTelemetry SDK
       |
       v
OpenTelemetry Collector
       |
       +--------+---------+
       |        |         |
       v        v         v
     Traces   Metrics    Logs
       |        |         |
       v        v         v
    Jaeger   Prometheus  Loki
```

The collector can export telemetry to compatible monitoring platforms.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://opentelemetry.io\&sz=32)

OpenTelemetry



### **16.6 Health checks**

Every service should expose health endpoints.

A basic Express example:

```
app.get("/health", (_req, res) => {
  res.status(200).json({
    status: "healthy",
    service: "order-service",
  });
});
```

For more advanced systems, distinguish:

- **Liveness:** Is the process running and functioning?
- **Readiness:** Is the service ready to receive traffic?
- **Startup:** Has the application completed its startup process?

A process can be alive but not ready because it has not connected to its database.

## **17. API Versioning and Service Contracts**

Microservices are independently deployed, so one service may be updated before another.

This creates a compatibility challenge.

Suppose Order Service version 1 expects:

```
{
  "productId": "101"
}
```

Product Service version 2 changes the field to:

```
{
  "id": "101"
}
```

If Order Service still expects `productId`, the integration may break.

### **17.1 API versioning**

One common approach is URL-based versioning:

```
/api/v1/products
/api/v2/products
```

Example:

```
app.get("/api/v1/products/:id", getProductV1);

app.get("/api/v2/products/:id", getProductV2);
```

Versioning can also be handled through headers or media types.

### **17.2 Backward-compatible changes**

Prefer additive changes where possible.

For example, adding an optional field:

```
{
  "id": "101",
  "name": "Laptop",
  "description": "New optional field"
}
```

Existing clients can continue using the fields they already understand.

### **17.3 Contract testing**

Contract testing verifies that services agree on their communication interfaces.

For example:

- Order Service expects a certain Product Service response.
- Product Service must continue providing a compatible response.
- A test verifies the expected contract.

Tools and approaches include:

- Pact
- OpenAPI specifications.
- Schema validation for event messages.
- Integration tests.

Do not assume that successful unit tests for individual services guarantee that their integration works.

## **18. Docker and Containerization for Microservices**

Docker allows you to package an application and its runtime dependencies into a container image.

This makes it easier to run services consistently across development, testing, and production environments.

### **18.1 Why Docker is useful**

Imagine your system has:

```
API Gateway
Product Service
Order Service
Payment Service
Redis
RabbitMQ
MongoDB
```

Installing and managing every dependency manually on one machine can become complicated.

Docker allows you to run each component in a separate container.

[CityFix - Technical Review | 1Month](https://images.openai.com/static-rsc-4/O49K3-bDzaO_YTPZp9HCBh6bYDOB4O8BGLJUoabjqktjKhV2qXzyxUuKP2r_Ypxv4XMzNDKrfNiStXPVyDQqNIMQ8TFq3cRKeuuc3KMXJpMge5c-r0k99AXBiosATN4O3Nx57mP6eLL77U9n8y63SUgTucsWN97Hp0mhjwvvdlA?purpose=inline)

[1month-ai.dev](https://1month-ai.dev/tech-review-cityfix.html?utm_source=chatgpt.com)

### **18.2 Example microservices structure**

```
ecommerce/
│
├── api-gateway/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── product-service/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── order-service/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── inventory-service/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
└── docker-compose.yml
```

Each service has its own Dockerfile and can be built independently.

### **18.3 Example Dockerfile**

For a TypeScript Node.js service:

```
FROM node:22-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

FROM node:22-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production

COPY package*.json ./

RUN npm ci --omit=dev

COPY --from=builder /app/dist ./dist

EXPOSE 4001

CMD ["node", "dist/server.js"]
```

This assumes your TypeScript build produces `dist/server.js` and the runtime dependencies are correctly declared in `package.json`.

For production, also consider running as a non-root user, pinning image versions, scanning images, and using a suitable minimal base image.

### **18.4 Docker Compose**

Docker Compose can run multiple services together.

Example:

```
services:
  api-gateway:
    build: ./api-gateway
    ports:
      - "4000:4000"
    environment:
      PRODUCT_SERVICE_URL: http://product-service:4001
      ORDER_SERVICE_URL: http://order-service:4002
    depends_on:
      - product-service
      - order-service

  product-service:
    build: ./product-service
    expose:
      - "4001"

  order-service:
    build: ./order-service
    expose:
      - "4002"
```

Start the services:

```
docker compose up --build
```

Stop the services:

```
docker compose down
```

View logs:

```
docker compose logs -f
```

**Important:** `depends_on` controls startup ordering, but it does not by itself guarantee that a dependent service is ready to handle requests. Use health checks and appropriate readiness handling.

Docker Compose networking provides service-name-based communication within its network.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.docker.com\&sz=32)

Docker Docs



### **18.5 Docker Compose vs Kubernetes**

| Feature                  | Docker Compose                              | Kubernetes                          |
| ------------------------ | ------------------------------------------- | ----------------------------------- |
| Main purpose             | Define and run multi-container applications | Orchestrate containerized workloads |
| Local development        | Very convenient                             | Possible, but more setup            |
| Service discovery        | Compose networking                          | Kubernetes Services and DNS         |
| Scaling                  | Basic service scaling                       | Advanced workload scaling           |
| Self-healing             | Limited                                     | Built-in workload reconciliation    |
| Production orchestration | Limited                                     | Designed for orchestration at scale |

Kubernetes is not mandatory for every microservices application. A small deployment can use Docker Compose or managed container platforms, depending on its operational requirements.

## **19. Deployment and Scaling**

Microservices should be designed to deploy and scale independently.

### **19.1 Independent deployments**

Example:

```
Product Service v1.2
Order Service v2.0
Payment Service v1.4
Inventory Service v1.1
```

A new Product Service version can be deployed without rebuilding every other service, provided compatibility is preserved.

### **19.2 Horizontal scaling**

Horizontal scaling means running more instances of a service.

```
                Load Balancer
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       Product-1  Product-2  Product-3
```

If product traffic increases, you can add Product Service instances without necessarily scaling the Payment Service.

### **19.3 Stateless services**

A stateless service does not depend on process-local state to handle a user's next request.

For example, avoid storing essential user sessions only in an individual Node.js process if requests may reach different instances.

Use shared session storage, signed tokens, or another suitable approach.

### **19.4 Deployment strategies**

Common strategies include:

- Rolling deployment.
- Blue-green deployment.
- Canary deployment.

| Strategy   | How it works                                             |
| ---------- | -------------------------------------------------------- |
| Rolling    | Replace instances gradually                              |
| Blue-green | Switch traffic between two environments                  |
| Canary     | Send a small portion of traffic to the new version first |

### **19.5 Microservices hosting options**

Depending on the size and requirements of your application, you can use:

- Virtual machines with Docker.
- Managed container platforms.
- Kubernetes.
- Serverless container platforms.
- Cloud-native managed services.

Do not introduce Kubernetes merely because you have two or three services. Choose infrastructure based on operational needs.

## **20. Complete E-commerce Microservices Architecture**

Now let's combine everything into one realistic system.

[Designing an E-commerce Platform in 45 Minutes: A Complete System Design Interview Guide](https://images.openai.com/static-rsc-4/NPRJxSE_cIsiPiefcQV2TWyRq8cxqtmpeG1dmZWV-6V1xiSb3-53LKhHdrrd5YmL4Yfp6jgoOo5BKut_3K5fuOq9wEiV7Xmb_jlCSRHE39JefwOvSeYenT0trBczo2VyJUWvtdpvddWW0k-Zp2PjbfaD5t3lWMy5g_SofmAMLUs?purpose=inline)

[designgurus.substack.com](https://designgurus.substack.com/p/designing-an-e-commerce-platform?utm_source=chatgpt.com)

### **20.1 Services**

| Service              | Responsibility            | Example database   |
| -------------------- | ------------------------- | ------------------ |
| API Gateway          | Routing, API entry point  | None required      |
| Auth Service         | Authentication and tokens | PostgreSQL         |
| User Service         | Profiles and preferences  | MongoDB            |
| Product Service      | Product catalog           | MongoDB            |
| Cart Service         | Shopping cart             | Redis or database  |
| Order Service        | Order management          | MySQL              |
| Inventory Service    | Stock management          | MySQL              |
| Payment Service      | Payment processing        | PostgreSQL         |
| Notification Service | Email and SMS             | MongoDB            |
| Analytics Service    | Reporting and analysis    | Analytics database |

These database choices are illustrative. You should choose storage based on consistency, query patterns, durability, and operational capabilities.

### **20.2 Order placement workflow**

Suppose a customer places an order.

```
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
Inventory Service
   |
   v
Reserve Stock
   |
   v
Payment Service
   |
   v
Process Payment
   |
   v
Confirm Order
   |
   v
Publish ORDER_CONFIRMED
   |
   +---------------------+
   |                     |
   v                     v
Notification Service  Analytics Service
   |                     |
   v                     v
Send Email           Update Reports
```

This diagram is simplified. In production, the workflow should use a defined Saga state machine or equivalent orchestration/choreography approach, durable messaging, idempotent consumers, and compensating actions.

### **20.3 What happens if payment fails?**

Suppose stock was successfully reserved but payment failed.

The system must not leave inventory reserved indefinitely.

A possible Saga:

1. Create pending order.
2. Reserve stock.
3. Attempt payment.
4. Payment fails.
5. Release reserved stock.
6. Mark order as payment failed.

The stock release is a compensating transaction.

### **20.4 What happens if the notification service is unavailable?**

The order should not necessarily fail just because an email cannot be sent.

A durable message should remain available for processing when the Notification Service recovers.

This is one reason asynchronous messaging is useful in microservices.

## **21. Common Microservices Mistakes**

| Mistake                                  | Why it is a problem                       | Better approach                       |
| ---------------------------------------- | ----------------------------------------- | ------------------------------------- |
| Splitting too early                      | Adds operational complexity               | Start with modular boundaries         |
| Creating too many services               | Increases communication overhead          | Decompose by business capability      |
| Sharing one database across all services | Creates tight coupling                    | Give each service data ownership      |
| Excessive synchronous calls              | Increases latency and failure propagation | Use events where appropriate          |
| No timeouts                              | Requests can wait too long                | Define deadlines                      |
| Retrying every request                   | Can create duplicate operations           | Retry selectively and use idempotency |
| No observability                         | Hard to locate distributed failures       | Centralized logs, metrics, traces     |
| No API versioning strategy               | Changes can break consumers               | Maintain compatible contracts         |
| Trusting only API Gateway authentication | Internal paths may bypass it              | Enforce authorization at services     |
| Treating messages as exactly-once        | Duplicate delivery is possible            | Use idempotent consumers              |
| No failure recovery                      | Partial workflows remain inconsistent     | Use Saga and compensating actions     |
| Using Kubernetes unnecessarily           | Adds operational overhead                 | Select infrastructure proportionally  |

## **22. Interview Questions and Answers**

### **Basic Level**

**Q1. What is microservices architecture?**

Microservices architecture is an approach in which an application is divided into independently deployable services, each responsible for a specific business capability.

**Q2. What is the difference between monolithic and microservices architecture?**

A monolith is deployed as one application unit, whereas microservices are separated into independently deployable services that communicate through network interfaces or messaging.

**Q3. What is an API Gateway?**

An API Gateway is a common entry point that routes client requests to appropriate backend services and may provide cross-cutting API functionality.

**Q4. What is service discovery?**

Service discovery allows services to find the network locations of other available service instances.

**Q5. What is the Database per Service pattern?**

Each service owns its data store and controls access to its data through its own interfaces or events.

**Q6. What is horizontal scaling?**

Horizontal scaling means adding more instances of a service to handle increased workload.

### **Intermediate Level**

**Q7. What is the difference between synchronous and asynchronous communication?**

Synchronous communication waits for a response from another service. Asynchronous communication sends a message or event without waiting for the receiving service to finish processing it.

**Q8. What is a circuit breaker?**

A circuit breaker temporarily blocks requests to an unhealthy dependency to prevent repeated failures and reduce cascading problems.

**Q9. What is the Saga pattern?**

Saga is a distributed transaction pattern in which multiple services perform local transactions and use compensating actions when a workflow cannot complete.

**Q10. Why is a shared database problematic in microservices?**

It creates schema and deployment coupling and weakens independent data ownership.

**Q11. What is API composition?**

API composition combines information from multiple services into a single response.

**Q12. What is distributed tracing?**

Distributed tracing tracks a request across multiple services so developers can understand its execution path and latency.

### **Advanced Level**

**Q13. How do you maintain consistency between microservices?**

Use local transactions, durable event messaging, the Saga pattern, transactional outbox, idempotent consumers, and eventual consistency where appropriate.

**Q14. How do you prevent cascading failures?**

Use timeouts, circuit breakers, bulkheads, rate limits, controlled retries, and asynchronous communication.

**Q15. How do you authenticate service-to-service requests?**

Use short-lived service tokens, OAuth 2.0 client credentials, workload identity, or mutual TLS depending on the infrastructure.

**Q16. What happens if a message consumer processes the same event twice?**

An idempotent consumer detects duplicate processing through an event ID or another reliable deduplication mechanism and prevents repeated business effects.

**Q17. How do you deploy microservices independently?**

Use independent build and deployment pipelines, compatible API contracts, container images, health checks, and controlled rollout strategies.

**Q18. When should you avoid microservices?**

Avoid them when the application and organization do not justify the operational complexity, independent scaling, deployment, or team ownership benefits.

## **23. Practical Assignment**

Major hands-on project

Build a **Mini E-commerce Microservices System** using Node.js, TypeScript, Express, MongoDB, MySQL, Redis, and Docker Compose.

### **Project architecture**

```
ecommerce-microservices/
│
├── api-gateway/
│
├── auth-service/
│
├── user-service/
│
├── product-service/
│
├── order-service/
│
├── inventory-service/
│
├── payment-service/
│
├── notification-service/
│
├── shared/
│
├── docker-compose.yml
│
└── README.md
```

`shared/` should contain only genuinely shared infrastructure contracts or utilities. Avoid creating a shared package that tightly couples all services through internal business logic.

### **Required features**

**Project progress**

# **0%**

0 of 15 tasks completed

Create separate Node.js + TypeScript applications for the Gateway, Product Service, and Order Service.

Create independent Express servers and health endpoints.

Implement API Gateway routing.

Connect Product Service to its own MongoDB database.

Connect Order Service to its own database.

Implement JWT verification and service-level authorization.

Implement HTTP communication from Order Service to Product Service.

Add request timeouts and controlled retries.

Add correlation IDs to requests and structured logs.

Implement a basic asynchronous order-created notification workflow.

Add idempotency protection for event consumers.

Create Dockerfiles for each service.

Run the services using Docker Compose.

Document service ownership, API contracts, and failure-handling behavior.

Test what happens when Product Service or Notification Service becomes unavailable.

Reset checklist

### **Testing scenarios**

Test these cases:

1. Request a product through the Gateway.
2. Create an order through the Gateway.
3. Stop the Product Service and request a product.
4. Stop the Notification Service after an order is created.
5. Restart the Notification Service and verify pending message processing.
6. Send the same event twice and verify that it does not create duplicate business effects.
7. Send an expired or invalid JWT.
8. Check that one service cannot directly modify another service's database.
9. Run multiple instances of a service and test request distribution.

### **Expected outcome**

You should understand how a request travels through multiple independently running Node.js services, how they exchange data, and how the system behaves when one service fails.

## **24. Quick Revision Notes**

| Concept                    | Remember                                               |
| -------------------------- | ------------------------------------------------------ |
| Monolith                   | Application deployed as one unit                       |
| Modular Monolith           | One deployment with clearly separated internal modules |
| Microservices              | Independently deployable business services             |
| Service decomposition      | Splitting by business capabilities and boundaries      |
| Bounded Context            | Boundary for a domain model                            |
| API Gateway                | Common entry point for client requests                 |
| Service discovery          | Finding available service instances                    |
| Synchronous communication  | Request and wait for response                          |
| Asynchronous communication | Send message without waiting for processing            |
| Database per Service       | Each service owns its data                             |
| API composition            | Combine responses from multiple services               |
| JWT                        | Signed token used for identity and claims              |
| Service-to-service auth    | Secure communication between backend services          |
| Timeout                    | Limit waiting time                                     |
| Retry                      | Attempt a transiently failed operation again           |
| Circuit Breaker            | Stop repeated calls to failing dependencies            |
| Bulkhead                   | Isolate resource usage                                 |
| Fallback                   | Alternative behavior when a dependency fails           |
| Saga                       | Coordinate local transactions across services          |
| Correlation ID             | Connect logs belonging to one request                  |
| Distributed tracing        | Track a request across services                        |
| Docker                     | Package applications into containers                   |
| Docker Compose             | Run multiple containers as an application              |
| Kubernetes                 | Container orchestration platform                       |
| Horizontal scaling         | Add more service instances                             |
| Contract testing           | Verify compatibility between service interfaces        |

## **25. Official Documentation and Further Reading**

1. [Microsoft – Microservices Architecture](https://learn.microsoft.com/en-us/azure/architecture/microservices/?utm_source=chatgpt.com) – Architecture fundamentals and design principles.
2. [Inter-Service Communication](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/interservice-communication?utm_source=chatgpt.com) – HTTP, gRPC, and asynchronous messaging.
3. [Microservices.io – Architecture Patterns](https://microservices.io/patterns/?utm_source=chatgpt.com) – Database per Service, Saga, API Gateway, and more.
4. [Docker Compose Documentation](https://docs.docker.com/compose/?utm_source=chatgpt.com) – Container orchestration for local development.
5. [Docker Compose Networking](https://docs.docker.com/compose/how-tos/networking/?utm_source=chatgpt.com) – Container networking and service discovery.
6. [OpenTelemetry for JavaScript](https://opentelemetry.io/docs/languages/js/?utm_source=chatgpt.com) – Distributed tracing and instrumentation.
7. [Opossum Circuit Breaker](https://github.com/nodeshift/opossum?utm_source=chatgpt.com) – Node.js circuit breaker library.
8. [HTTP Proxy Middleware](https://www.npmjs.com/package/http-proxy-middleware?utm_source=chatgpt.com) – Express proxy and API Gateway implementation.

## **Phase 33 Completion Checklist**

**Learning progress**

# **0%**

Monolithic and microservices architecture

Microservices principles and characteristics

Service decomposition and bounded contexts

API Gateway implementation

Synchronous and asynchronous communication

Service discovery and load balancing

Centralized configuration and secrets

Distributed authentication and authorization

Database per Service

API composition and aggregation

Timeout, retry, and circuit breaker

Bulkhead and fallback patterns

Distributed logging, metrics, and tracing

Health checks and contract testing

Docker and Docker Compose

Deployment, scaling, and fault tolerance

Complete e-commerce microservices architecture

0 of 17 topics completed

## **Next Phase Preview**

Phase 34

# **API Security, OWASP and Advanced Backend Security**

We will learn how to secure production-grade APIs and backend applications.

**Topics will include:**

1. Web Application Security Fundamentals
2. OWASP Top 10
3. SQL Injection and NoSQL Injection
4. Cross-Site Scripting (XSS)
5. Cross-Site Request Forgery (CSRF)
6. Server-Side Request Forgery (SSRF)
7. Broken Access Control and IDOR
8. Authentication and Authorization Security
9. JWT Security and Session Hijacking
10. CORS and Security Headers
11. Rate Limiting and Brute-Force Protection
12. Input Validation and Output Encoding
13. Password Hashing and Secrets Management
14. File Upload Security
15. API Security Best Practices
16. Security Testing and Vulnerability Scanning
17. Production Security Checklist

**When you're ready, say: OK, Phase 34.**