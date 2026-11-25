# **Phase 29 – Microservices Architecture and API Gateway**



Advanced Level

Next.js 16.x

Node.js + TypeScript



## **1. Learning Objectives**

In this phase, you will learn how large-scale backend applications are designed using independently deployable services.

By the end, you should understand:

1. What Microservices Architecture is.
2. Monolithic vs Modular Monolith vs Microservices.
3. Service boundaries and Bounded Contexts.
4. How an API Gateway works.
5. Synchronous and asynchronous service communication.
6. Service discovery and centralized configuration.
7. Authentication and authorization between services.
8. Distributed logging, monitoring, and tracing.
9. How to build microservices using Node.js and TypeScript.
10. How Next.js applications communicate with backend microservices.
11. Docker, deployment, scaling, and common microservices challenges.

## **2. What Is Microservices Architecture?**

Microservices Architecture is a software architecture in which an application is divided into multiple small, independently deployable services.

Each service is responsible for a specific business capability.

For example, consider an e-commerce application.

Its functionality may include:

- User registration and authentication.
- Product management.
- Shopping cart.
- Order management.
- Payment processing.
- Inventory management.
- Notifications.

In a traditional monolithic application, these functionalities may all exist inside one backend application.

In a microservices architecture, they can be separated into individual services.

[Designing an E-commerce Platform in 45 Minutes: A Complete System Design Interview Guide](https://images.openai.com/static-rsc-4/NPRJxSE_cIsiPiefcQV2TWyRq8cxqtmpeG1dmZWV-6V1xiSb3-53LKhHdrrd5YmL4Yfp6jgoOo5BKut_3K5fuOq9wEiV7Xmb_jlCSRHE39JefwOvSeYenT0trBczo2VyJUWvtdpvddWW0k-Zp2PjbfaD5t3lWMy5g_SofmAMLUs?purpose=inline)

[designgurus.substack.com](https://designgurus.substack.com/p/designing-an-e-commerce-platform?utm_source=chatgpt.com)

For example:

```
                 E-commerce Application
                           |
                           v
                      API Gateway
                           |
        +------------------+------------------+
        |                  |                  |
        v                  v                  v
   User Service      Product Service     Order Service
        |                  |                  |
        v                  v                  v
    User DB            Product DB           Order DB
                                               |
                                  +------------+------------+
                                  |                         |
                                  v                         v
                            Payment Service          Inventory Service
                                  |                         |
                                  v                         v
                              Payment DB                Inventory DB
```

Each service:

- Owns a specific business responsibility.
- Can have its own codebase or independently managed module.
- Can be deployed independently.
- Can scale independently.
- Communicates with other services through defined interfaces.
- Can own its own data store.

**Important:** Microservices are not simply separate folders or separate Node.js files. They are independently deployable services with clear responsibilities and communication boundaries.

## **3. Why Do We Need Microservices?**

Imagine your e-commerce application is growing.

Initially, it has:

- 100 users.
- 10 products.
- 5 orders per day.

A single Node.js application may be sufficient.

Later, the application grows:

- 1 million users.
- 100,000 products.
- 50,000 orders per day.
- Multiple payment providers.
- Multiple warehouses.
- International customers.

Now different parts of the application may have different scaling and development requirements.

For example:

- Product Service receives many read requests.
- Order Service handles order creation and status changes.
- Payment Service communicates with external payment providers.
- Notification Service sends emails, SMS messages, and push notifications.

Microservices allow these services to evolve and scale independently.

### **Main Benefits**

| Benefit                | Explanation                                                          |
| ---------------------- | -------------------------------------------------------------------- |
| Independent deployment | Deploy one service without redeploying the entire application        |
| Independent scaling    | Scale only the services that need more resources                     |
| Technology flexibility | Different services can use different technologies when justified     |
| Fault isolation        | One service failure does not necessarily stop the entire application |
| Team ownership         | Different teams can own different services                           |
| Maintainability        | Smaller services can have focused responsibilities                   |

### **Main Disadvantages**

| Disadvantage           | Explanation                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| Operational complexity | More services require more deployment and infrastructure management |
| Network failures       | Service communication can fail or become slow                       |
| Data consistency       | Data spread across services is harder to keep consistent            |
| Debugging complexity   | A single request may travel through many services                   |
| Security complexity    | Service-to-service communication needs protection                   |
| Infrastructure cost    | Multiple services may require more resources                        |

**Key takeaway:** Microservices solve certain scaling and organizational problems, but they also introduce distributed-systems complexity. They are not automatically better than a monolith.

## **4. Monolithic vs Modular Monolith vs Microservices**

Before implementing microservices, you should understand three common backend architectures.

### **4.1 Monolithic Architecture**

A Monolithic Architecture is an application in which multiple business functionalities are developed and deployed as one application unit.

Example:

```
             E-commerce Backend
                      |
       +--------------+--------------+
       |              |              |
       v              v              v
   User Module    Product Module   Order Module
       |              |              |
       +--------------+--------------+
                      |
                      v
                   Database
```

All modules are part of the same application and typically share its deployment lifecycle.

Example Node.js structure:

```
ecommerce/
│
├── src/
│   ├── users/
│   ├── products/
│   ├── orders/
│   ├── payments/
│   └── notifications/
│
├── package.json
└── server.ts
```

You deploy the application as one unit.

**Advantages:**

- Simple to develop initially.
- Easy to debug locally.
- Straightforward deployment.
- Easier transactions within one database.
- Less infrastructure overhead.

**Disadvantages:**

- The application can become difficult to maintain as it grows.
- One deployment can affect the entire application.
- Independent scaling of individual modules is difficult.
- Teams can become tightly coupled.

### **4.2 Modular Monolith**

A Modular Monolith is a single deployable application divided into well-defined, loosely coupled business modules.

```
              E-commerce Application
                         |
       +-----------------+-----------------+
       |                 |                 |
       v                 v                 v
   User Module       Order Module      Product Module
       |                 |                 |
       +-----------------+-----------------+
                         |
                    Shared Runtime
                         |
                      Database
```

Unlike a poorly structured monolith, a modular monolith enforces boundaries between modules.

Example:

```
src/
│
├── modules/
│   ├── users/
│   │   ├── domain/
│   │   ├── application/
│   │   └── infrastructure/
│   │
│   ├── orders/
│   │   ├── domain/
│   │   ├── application/
│   │   └── infrastructure/
│   │
│   └── products/
│       ├── domain/
│       ├── application/
│       └── infrastructure/
│
└── main.ts
```

Each module owns its business logic and should expose a clear interface to other modules.

**Advantages:**

- Simpler than microservices.
- Clear separation of business responsibilities.
- Easier refactoring.
- Lower infrastructure and operational costs.
- Can be split into microservices later if necessary.

**Disadvantages:**

- Modules still share one deployment unit.
- A failure in the application process can affect all modules.
- Independent scaling is limited.

### **4.3 Microservices Architecture**

In Microservices Architecture, modules become independently deployable services.

```
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

Each service can have its own deployment pipeline, runtime, database, and scaling policy.

### **4.4 Comparison Table**

| Feature                    | Monolith               | Modular Monolith                    | Microservices        |
| -------------------------- | ---------------------- | ----------------------------------- | -------------------- |
| Deployment                 | One unit               | One unit                            | Independent services |
| Code organization          | May be tightly coupled | Strong module boundaries            | Service boundaries   |
| Scaling                    | Whole application      | Whole application                   | Individual services  |
| Database                   | Usually shared         | Usually shared, logically separated | Service-owned data   |
| Complexity                 | Low initially          | Moderate                            | High                 |
| Network communication      | Usually in-process     | Usually in-process                  | Network-based        |
| Independent team ownership | Limited                | Possible, with coordination         | Stronger fit         |
| Infrastructure             | Simple                 | Simple to moderate                  | More complex         |

Practical recommendation

For most new applications, a well-structured Modular Monolith is a sensible starting point.

Move to microservices when you have concrete reasons such as independent scaling, separate team ownership, deployment requirements, or different operational needs.

Microsoft's architecture guidance also recommends pragmatic service boundaries rather than splitting a system into excessively small services.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+2



## **5. Service Boundaries and Bounded Contexts**

One of the most important decisions in microservices architecture is deciding:

**Where should one service end and another service begin?**

This is called defining service boundaries.

### **5.1 What Is a Service Boundary?**

A Service Boundary defines the responsibility and ownership of a service.

For an e-commerce application, you might define:

| Service              | Responsibility                            |
| -------------------- | ----------------------------------------- |
| User Service         | User profiles and accounts                |
| Product Service      | Product catalogue and product information |
| Cart Service         | Shopping cart                             |
| Order Service        | Order creation and lifecycle              |
| Payment Service      | Payment processing                        |
| Inventory Service    | Stock availability                        |
| Notification Service | Email, SMS and push notifications         |

Each service should have a focused business responsibility.

### **5.2 How Does DDD Help Define Microservices?**

In Phase 27, we studied Domain-Driven Design and Bounded Contexts.

A Bounded Context represents a boundary within which a particular domain model and business language are valid.

For example:

```
E-commerce Domain
│
├── Customer Context
│
├── Product Context
│
├── Order Context
│
├── Payment Context
│
└── Inventory Context
```

These bounded contexts can help identify possible microservices.

However, remember:

**One Bounded Context does not automatically have to become one Microservice.**

A bounded context can remain a module inside a modular monolith.

You should decide based on business requirements, deployment independence, ownership, and operational complexity.

### **5.3 Avoid Creating Microservices Based Only on Technical Layers**

Bad architecture:

```
Frontend Service
Backend Service
Database Service
Validation Service
```

This divides the system by technical responsibility rather than business capability.

A better design:

```
User Service
Order Service
Payment Service
Inventory Service
```

Each service owns a business capability and the relevant application logic.

### **5.4 Signs of a Good Service Boundary**

A good service boundary generally has:

1. High cohesion: related business operations stay together.
2. Low coupling: services do not need constant coordination.
3. Data ownership: a service controls its own data.
4. Independent deployment: changes do not routinely require coordinated releases.
5. Clear contracts: other services interact through defined APIs or events.

If two services constantly call each other to complete one simple operation, they may be split incorrectly.

Microsoft's guidance recommends grouping functionality that changes together and avoiding excessively chatty communication between services.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **6. API Gateway Pattern**

An API Gateway is a single entry point through which clients access multiple backend services.

Without an API Gateway, a frontend might need to communicate directly with every service.

### **6.1 Without an API Gateway**

[System Design для самых маленьких. Reference к интервью / Хабр](https://images.openai.com/static-rsc-4/jLJqhHTmMv7wFBx1nh1rQyZ2iunKXS1I_7h6JJ1xFV_9j4Ugk1-vtQc_RRVUpk75xARrCubdKqFJgdLP_U3Co743G0zk2o0Xip-aXM9uzkWHqIRfYt9nfBhANYNqNcP7ExPDHwx_BsNaeJLyIiLEZfgngqtXov3OgDHYKktPdEw?purpose=inline)

[habr.com](https://habr.com/ru/articles/747112/?utm_source=chatgpt.com)

```
              Frontend
                 |
       +---------+---------+
       |         |         |
       v         v         v
   User API  Product API  Order API
       |         |         |
       v         v         v
     User DB  Product DB  Order DB
```

Problems:

- Frontend needs to know multiple service URLs.
- Authentication may be repeated across services.
- API changes can require frontend changes.
- Request routing becomes more complicated.
- Different services may expose inconsistent response formats.

### **6.2 With an API Gateway**

[System Design Interview: API Gateway vs. Service Mesh (The Senior’s Perspective)](https://images.openai.com/static-rsc-4/9TQEASW-zedX2Uag6p0vyfu0FTyEXJtOIg6ByPHbFXBRsN7sAAZrs6AefnOF0vlkCR3Um1D153OFI9QcwpI157tGKz4nlaKSt0XANXqb5ejC8FAyb3ypkoqpEHSGvunnKEBjKafpDPauuaEZQmVfqU80OoUrdJDTjcgFqudL6cQ?purpose=inline)

[designgurus.substack.com](https://designgurus.substack.com/p/system-design-interview-api-gateway?utm_source=chatgpt.com)

```
                 Frontend
                     |
                     v
                API Gateway
                     |
        +------------+------------+
        |            |            |
        v            v            v
    User Service Product Service Order Service
        |            |            |
        v            v            v
      User DB     Product DB     Order DB
```

The frontend communicates with one public API entry point.

The gateway forwards requests to the appropriate backend services.

### **6.3 Responsibilities of an API Gateway**

| Responsibility         | Explanation                                                            |
| ---------------------- | ---------------------------------------------------------------------- |
| Request routing        | Forward requests to the correct service                                |
| Authentication         | Verify client credentials or tokens                                    |
| Rate limiting          | Limit requests from clients                                            |
| TLS termination        | Handle HTTPS connections                                               |
| Request transformation | Modify request formats when required                                   |
| Response aggregation   | Combine results from multiple services                                 |
| Logging                | Record incoming requests                                               |
| API versioning         | Manage public API versions                                             |
| Load balancing         | Distribute requests across service instances, depending on the gateway |

**Important:** An API Gateway should not become a place for all business logic. Business rules belong in the services that own them.

### **6.4 Example API Gateway Routing**

Suppose your gateway runs at:

```
https://api.example.com
```

You can define routes like:

| Public endpoint   | Internal service |
| ----------------- | ---------------- |
| `/api/users/*`    | User Service     |
| `/api/products/*` | Product Service  |
| `/api/orders/*`   | Order Service    |
| `/api/payments/*` | Payment Service  |

The frontend only needs to know the gateway URL.

### **6.5 API Gateway vs Reverse Proxy**

These concepts overlap, but they are not identical.

| Reverse Proxy                                  | API Gateway                                                             |
| ---------------------------------------------- | ----------------------------------------------------------------------- |
| Forwards requests to backend servers           | Routes API requests to backend services                                 |
| Often focuses on routing and traffic handling  | Often includes API-specific policies                                    |
| Can perform TLS termination and load balancing | Can add authentication, rate limiting, transformations, and aggregation |
| Used for websites and APIs                     | Primarily used as an API entry point                                    |

An API Gateway is often implemented using reverse-proxy technology.

Examples include:

- NGINX.
- Kong Gateway.
- Amazon API Gateway.
- Traefik.

### **6.6 API Gateway vs Next.js Backend for Frontend (BFF)**

This is particularly important when working with Next.js.

A Backend for Frontend (BFF) is a backend layer designed to serve the needs of a particular frontend application.

For example:

```
                 Web Browser
                      |
                      v
                 Next.js BFF
                      |
             +--------+--------+
             |        |        |
             v        v        v
          User API Product API Order API
```

Next.js can act as a BFF by using:

- Route Handlers.
- Server Components.
- Server-side `fetch()`.
- Proxy or rewrites where appropriate.

However, Next.js BFF and API Gateway are not automatically the same thing.

| Next.js BFF                          | API Gateway                         |
| ------------------------------------ | ----------------------------------- |
| Tailored to a frontend experience    | Central entry point for APIs        |
| Can aggregate data for pages         | Routes requests to backend services |
| Can adapt backend responses for UI   | Applies shared API policies         |
| Often belongs to a specific frontend | May serve multiple clients          |

A Next.js BFF can sit behind an API Gateway, or it may perform gateway-like functions in a smaller architecture.

Next.js officially documents its Backend for Frontend pattern and explains that its backend capabilities provide an API layer, rather than a complete replacement for every backend architecture.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **7. Service-to-Service Communication**

Microservices must communicate with each other to complete business workflows.

There are two major communication approaches:

1. Synchronous communication.
2. Asynchronous communication.

### **7.1 Synchronous Communication**

In synchronous communication, one service sends a request to another service and waits for its response.

The most common example is HTTP REST API communication.

Example:

A customer places an order.

The Order Service needs to check whether the requested product exists.

```
Customer
   |
   v
Order Service
   |
   | HTTP GET
   v
Product Service
   |
   | HTTP Response
   v
Order Service
   |
   v
Create Order
```

Example:

```
const response = await fetch(
  "http://product-service:4001/products/product-101"
);

if (!response.ok) {
  throw new Error("Unable to retrieve product");
}

const product = await response.json();
```

The Order Service waits until the Product Service responds.

**Advantages:**

- Simple request-response model.
- Easy to understand.
- Immediate response.
- Useful for operations requiring current information.

**Disadvantages:**

- Services become dependent on each other's availability.
- Network latency increases response time.
- A slow service can slow down the entire request.
- Cascading failures are possible.

### **7.2 Asynchronous Communication**

In asynchronous communication, one service sends a message or publishes an event without waiting for every consumer to complete its work.

A message broker can deliver that message to other services.

Example:

A customer places an order.

```
                 Order Service
                       |
                       v
                Order Created Event
                       |
                       v
                  Message Broker
                       |
           +-----------+-----------+
           |           |           |
           v           v           v
      Inventory     Payment    Notification
       Service      Service      Service
```

The Order Service publishes an event:

```
{
  "eventId": "evt-101",
  "eventType": "OrderCreated",
  "orderId": "order-101",
  "userId": "user-101"
}
```

Other services consume the event independently.

For example:

- Inventory Service reserves stock.
- Payment Service starts payment processing.
- Notification Service sends an order confirmation.

The producer does not have to wait for all consumers to finish.

### **7.3 Common Message Brokers**

[RabbitMQ: A Powerful Message Broker for Scalable Applications](https://images.openai.com/static-rsc-4/Tw-RC9j1cPRUkiiNYNdrZD0evCh5YvnMd8llFrtHCsJ6iu3LIpjdaO3fyenX2hso0UhD3aHmZ8BLMXrwn4TC2xzBrvbaxC_uptCHK3p-xf7s2mOQTMx7hbJHqNIW0q9WjAPvpjg_4VIfvII2B8V4YsPphWNuD6QGjDkpp-irt9g?purpose=inline)

**RabbitMQ**

Message broker supporting queues, exchanges, routing and acknowledgments.

[LinkedIn and Apache Kafka](https://images.openai.com/static-rsc-4/Qf_pyVbjl6VyXox2GiUSg1g5HuJSK4nGw_KRbhrki80L_USPOeWcSqcVzO79B5soQKSc0QoHyCd5z5_2oFfiXLtMjH4OtVJMpileWJ04ZZnEh_aSdLfNzkTQIBn1tNBGLZzIyRfsztHUfwmP23RlxmlXMT3HxvNRPuVzgYr9w_I?purpose=inline)

**Apache Kafka**

Distributed event streaming platform designed for durable event streams and high-throughput processing.

Other options include:

- Amazon SQS.
- Amazon SNS.
- Redis Streams.

### **7.4 Synchronous vs Asynchronous Communication**

| Feature          | Synchronous                    | Asynchronous                                           |
| ---------------- | ------------------------------ | ------------------------------------------------------ |
| Communication    | Request and response           | Message or event                                       |
| Waiting          | Caller waits                   | Caller generally does not wait for consumer completion |
| Coupling         | More temporal coupling         | Less temporal coupling                                 |
| Speed            | Depends on downstream response | Producer can continue after publishing                 |
| Failure handling | Timeouts and retries           | Retries, acknowledgments, dead-letter queues           |
| Example          | HTTP REST                      | RabbitMQ, Kafka                                        |
| Best suited for  | Immediate response             | Background processing and event-driven workflows       |

### **7.5 When Should You Use Each?**

Use synchronous communication when:

- A user needs an immediate answer.
- A service must retrieve current data.
- The operation naturally follows request-response semantics.

Use asynchronous communication when:

- Processing can happen in the background.
- Multiple services need to react to an event.
- You want to reduce direct runtime dependency between services.
- The operation may take a long time.

**Important:** Asynchronous communication does not eliminate coupling entirely. Services still depend on message contracts, event semantics, and delivery guarantees.

### **7.6 Example: Complete Order Workflow**

Consider this e-commerce workflow:

```
1. Customer places order
          |
          v
2. Order Service creates pending order
          |
          v
3. OrderCreated event published
          |
          v
4. Inventory Service reserves stock
          |
          v
5. Payment Service processes payment
          |
          v
6. PaymentSucceeded event published
          |
          v
7. Order Service marks order as paid
          |
          v
8. Notification Service sends confirmation
```

In a real distributed workflow, payment and inventory operations can fail independently. You need idempotency, retries, event correlation, and possibly a Saga with compensating actions.

This connects directly to Phase 25, where we studied Event-Driven Architecture, Message Brokers, and the Saga Pattern.

## **8. Service Discovery**

In a microservices environment, services need to locate one another.

This is called Service Discovery.

### **8.1 Why Is Service Discovery Needed?**

Imagine you have three instances of the Product Service:

```
Product Service Instance 1
192.168.1.10:4001

Product Service Instance 2
192.168.1.11:4001

Product Service Instance 3
192.168.1.12:4001
```

The Order Service needs to communicate with the Product Service.

But which instance should it call?

If you hardcode one IP address, problems arise when:

- An instance crashes.
- An instance is replaced.
- More instances are added.
- Infrastructure addresses change.

Service Discovery solves this problem.

### **8.2 Two Types of Service Discovery**

**A. Client-Side Discovery**

The client looks up available service instances and chooses one.

```
Order Service
      |
      v
Service Registry
      |
      v
Available Product Instances
      |
      v
Order Service selects an instance
```

The client is responsible for selecting an instance.

**B. Server-Side Discovery**

The client sends the request to a load balancer or service endpoint.

That component finds an available service instance and forwards the request.

```
Order Service
      |
      v
Load Balancer
      |
      +--------+--------+
      |        |        |
      v        v        v
    Pod 1    Pod 2    Pod 3
```

The client does not need to know individual instance addresses.

### **8.3 Common Service Discovery Solutions**

| Technology          | Purpose                                                     |
| ------------------- | ----------------------------------------------------------- |
| Kubernetes Services | Stable networking endpoint for a group of Pods              |
| Consul              | Service discovery and service networking                    |
| AWS Cloud Map       | Service discovery for cloud resources                       |
| Eureka              | Service registry commonly used in Spring Cloud environments |
| DNS                 | Resolves service names to addresses                         |

### **8.4 Service Discovery in Docker Compose**

Docker Compose provides service-name-based DNS on its application network.

Example:

```
services:
  order-service:
    build: ./order-service
    ports:
      - "4002:4002"
    depends_on:
      - product-service

  product-service:
    build: ./product-service
    ports:
      - "4001:4001"
```

The Order Service can call the Product Service using:

```
const productResponse = await fetch(
  "http://product-service:4001/products/product-101"
);
```

Here, `product-service` is the Docker Compose service name.

You do not need to hardcode its container IP address.

**Important:** `depends_on` controls startup ordering, but it does not guarantee that the dependency is ready to accept requests. Use health checks and retry strategies when readiness matters.

## **9. Centralized Configuration**

Every microservice needs configuration.

Examples:

- Database connection strings.
- Service URLs.
- JWT verification keys.
- Message broker connection details.
- API keys.
- Feature flags.
- Environment-specific settings.

If configuration is scattered across services, managing it becomes difficult.

Centralized Configuration provides a consistent way to manage configuration across services and environments.

### **9.1 Example**

Imagine you have:

```
User Service
Product Service
Order Service
Payment Service
```

Each service needs configuration for:

```
NODE_ENV
PORT
DATABASE_URL
MESSAGE_BROKER_URL
LOG_LEVEL
```

Each service should have its own configuration and secrets appropriate to its responsibilities.

### **9.2 Environment Variables in Node.js**

Example:

```
NODE_ENV=production
PORT=4002

MONGODB_URI=mongodb://localhost:27017/orders

PAYMENT_SERVICE_URL=http://payment-service:4003
```

Access them:

```
const port = Number(process.env.PORT ?? 4002);

const databaseUrl = process.env.MONGODB_URI;

if (!databaseUrl) {
  throw new Error("MONGODB_URI is required");
}
```

For production, use secure configuration management rather than committing secrets into source control.

### **9.3 Common Configuration Management Solutions**

- AWS Systems Manager Parameter Store.
- AWS Secrets Manager.
- HashiCorp Vault.
- Kubernetes ConfigMaps.
- Kubernetes Secrets.

### **9.4 Configuration vs Secrets**

| Configuration    | Secrets                      |
| ---------------- | ---------------------------- |
| Application port | Database password            |
| Log level        | API secret key               |
| Service URL      | JWT signing key              |
| Feature toggle   | Payment provider credentials |

Do not expose secret values in:

- Client-side JavaScript.
- Public API responses.
- Application logs.
- Git repositories.
- Browser URLs.

Also remember that Kubernetes Secrets are not automatically encrypted merely because they are named "Secrets"; configure appropriate encryption at rest and access controls.

## **10. Inter-Service Authentication and Authorization**

In a microservices environment, services communicate with one another.

You must secure these communications.

For example:

```
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

How does Payment Service know that a request from Order Service is legitimate?

This is where service-to-service authentication comes in.

### **10.1 Authentication vs Authorization**

| Authentication                  | Authorization                                                 |
| ------------------------------- | ------------------------------------------------------------- |
| Verifies identity               | Checks permissions                                            |
| Who are you?                    | What are you allowed to do?                                   |
| Example: Validate service token | Example: Allow payment processing only to authorized services |

### **10.2 Common Security Approaches**

**1. JWT-Based Service Authentication**

A service can present a signed token to another service.

Example claims:

```
{
  "iss": "order-service",
  "sub": "order-service",
  "aud": "payment-service",
  "scope": "payments:create",
  "exp": 1795600000
}
```

The receiving service verifies:

- Signature.
- Issuer.
- Audience.
- Expiration.
- Required scope or permissions.

Do not simply trust a decoded JWT payload. A token must be cryptographically verified.

**2. OAuth 2.0 Client Credentials Flow**

This is a common approach for machine-to-machine authentication.

```
Order Service
     |
     | Client ID + Client Secret
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

**3. Mutual TLS (mTLS)**

In mutual TLS, both communicating services authenticate each other using certificates.

It can provide strong service identity at the transport layer.

### **10.3 Defense in Depth**

Do not rely only on the API Gateway.

A secure design should include:

- Authentication at the public edge.
- Authorization in the service that owns the operation.
- Service-to-service authentication.
- Network restrictions.
- Least-privilege credentials.
- Secret rotation.
- TLS for service communication.

**Important:** An internal network does not automatically make a request trustworthy. Verify service identity and permissions.

## **11. Distributed Logging, Monitoring and Observability**

In a monolithic application, one log file may contain the entire request flow.

In microservices, a single user operation may involve several independent services.

Example:

```
Customer places order
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

If the order fails, how do you find which service caused the problem?

This is why Observability is important.

### **11.1 What Is Observability?**

Observability is the ability to understand the internal state and behavior of a system using the data it produces.

The three major pillars are:

**Logs**

What happened?

**Metrics**

How much / how often?

**Traces**

Where did the request go?

**A. Logging**

Logs are records of application events.

Example:

```
{
  "level": "error",
  "service": "payment-service",
  "message": "Payment provider request failed",
  "orderId": "order-101",
  "timestamp": "2026-11-25T12:00:00Z"
}
```

**B. Metrics**

Metrics are numerical measurements collected over time.

Examples:

- Requests per second.
- Error rate.
- Average response time.
- CPU usage.
- Memory usage.
- Database connection count.

**C. Distributed Tracing**

Distributed tracing follows a request across multiple services.

Example:

```
Trace ID: trace-123

API Gateway       20 ms
    |
Order Service    100 ms
    |
Inventory         80 ms
    |
Payment Service  900 ms
```

This reveals that Payment Service is taking most of the time.

### **11.2 OpenTelemetry**

OpenTelemetry is an open-source observability framework for generating, collecting, and exporting telemetry data.

It supports:

- Traces.
- Metrics.
- Logs.

It can be used with observability backends such as:

- Jaeger.
- Prometheus.
- Grafana.
- Datadog.

### **11.3 Correlation IDs**

A Correlation ID helps connect logs from different services that belong to the same request or business operation.

Example:

```
Request ID: req-101

API Gateway:
  req-101 - Request received

Order Service:
  req-101 - Order created

Payment Service:
  req-101 - Payment initiated

Notification Service:
  req-101 - Confirmation sent
```

A Correlation ID is useful for log correlation. Distributed tracing provides a richer model with trace IDs, span IDs, parent-child relationships, and timing information.

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn

## **12. Microservices with Node.js and Next.js**

Now let's build a simplified architecture using the technologies you already know.

We will use:

- Next.js 16 – Frontend and BFF.
- Node.js + Express – Backend services.
- MongoDB – Service-specific databases.
- Docker Compose – Local service orchestration.
- HTTP REST – Synchronous communication.

### **12.1 Overall Architecture**

[Epoch 9: Dockerize All Services | Hieu Nguyen (Shandy)](https://images.openai.com/static-rsc-4/wioe_U-ptAm1D7NkPIdUOGsaC9PvUTmMFCEOCly4lv9iqxoWN5k_ke_Xs0QVw5yHuB33G_eHm3y6CPYQDbrh-FLXpjl_f4eLzbMf5TR73M3Gicu0LjiX1ObW7CH8sbgxDf1xJINizB_pQ-EYxEfPxSJgMIXuHAUoBH6vPhFDdnQ?purpose=inline)

[shandyprofile.github.io](https://shandyprofile.github.io/posts/microservices-ecomerce-09/?utm_source=chatgpt.com)

```
                 Browser
                    |
                    v
               Next.js App
                    |
                    v
              Next.js BFF
                    |
              API Gateway
                    |
       +------------+------------+
       |            |            |
       v            v            v
  User Service  Product Service Order Service
       |            |            |
       v            v            v
    User DB      Product DB     Order DB
```

For this learning project, the Next.js application can call the API Gateway directly through server-side code.

The gateway routes requests to the appropriate backend service.

### **12.2 Project Structure**

```
ecommerce-microservices/
│
├── frontend/
│   └── next-app/
│       ├── src/
│       │   └── app/
│       └── package.json
│
├── services/
│   │
│   ├── user-service/
│   │   ├── src/
│   │   │   ├── routes/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   └── server.ts
│   │   └── package.json
│   │
│   ├── product-service/
│   │   ├── src/
│   │   │   ├── routes/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   └── server.ts
│   │   └── package.json
│   │
│   └── order-service/
│       ├── src/
│       │   ├── routes/
│       │   ├── controllers/
│       │   ├── services/
│       │   └── server.ts
│       └── package.json
│
├── gateway/
│   └── nginx.conf
│
└── docker-compose.yml
```

Each service has its own `package.json`, application startup, and deployment configuration.

### **12.3 Create the Product Service**

First, create the Product Service.

Install dependencies:

```
cd services/product-service

npm init -y

npm install express

npm install -D typescript tsx @types/node @types/express
```

Create a simple Express server:

```
// services/product-service/src/server.ts

import express from "express";

const app = express();

app.use(express.json());

const products = [
  {
    id: "product-101",
    name: "Laptop",
    price: 50000,
  },
  {
    id: "product-102",
    name: "Keyboard",
    price: 1500,
  },
];

app.get("/products", (req, res) => {
  res.json(products);
});

app.get("/products/:id", (req, res) => {
  const product = products.find(
    (item) => item.id === req.params.id
  );

  if (!product) {
    return res.status(404).json({
      message: "Product not found",
    });
  }

  return res.json(product);
});

app.listen(4001, () => {
  console.log("Product Service running on port 4001");
});
```

This service is responsible for product-related operations.

It should own product data and its business rules.

### **12.4 Create the Order Service**

Install the same basic dependencies in the Order Service.

```
// services/order-service/src/server.ts

import express from "express";

const app = express();

app.use(express.json());

const PRODUCT_SERVICE_URL =
  process.env.PRODUCT_SERVICE_URL ??
  "http://localhost:4001";

app.post("/orders", async (req, res) => {
  try {
    const { userId, productId, quantity } = req.body;

    if (
      typeof userId !== "string" ||
      typeof productId !== "string" ||
      !Number.isInteger(quantity) ||
      quantity <= 0
    ) {
      return res.status(400).json({
        message: "Invalid order input",
      });
    }

    const productResponse = await fetch(
      `${PRODUCT_SERVICE_URL}/products/${encodeURIComponent(productId)}`,
      { signal: AbortSignal.timeout(3000) }
    );

    if (!productResponse.ok) {
      return res.status(404).json({
        message: "Product unavailable",
      });
    }

    const product = await productResponse.json();

    const order = {
      id: crypto.randomUUID(),
      userId,
      productId,
      quantity,
      total: product.price * quantity,
      status: "pending",
    };

    // Persist the order in the Order Service's own database.

    return res.status(201).json(order);
  } catch (error) {
    console.error("Order creation failed", error);

    return res.status(503).json({
      message: "Order service temporarily unavailable",
    });
  }
});

app.listen(4002, () => {
  console.log("Order Service running on port 4002");
});
```

This example demonstrates synchronous communication.

The Order Service calls Product Service to retrieve product information.

For a production system, also implement authentication, input schemas, error classification, retries where safe, and durable order persistence.

### **12.5 Create a Simple API Gateway Using NGINX**

Create:

```
gateway/nginx.conf
```

Example:

```
events {}

http {
    upstream user_service {
        server user-service:4000;
    }

    upstream product_service {
        server product-service:4001;
    }

    upstream order_service {
        server order-service:4002;
    }

    server {
        listen 80;

        location /api/users/ {
            proxy_pass http://user_service/users/;
        }

        location /api/products/ {
            proxy_pass http://product_service/products/;
        }

        location /api/orders/ {
            proxy_pass http://order_service/orders/;
        }
    }
}
```

This is a basic routing example, not a production-ready API Gateway configuration.

A production gateway should also handle:

- TLS.
- Authentication and authorization.
- Rate limiting.
- Request-size limits.
- Timeouts.
- Health-aware routing.
- Security headers.
- Logging.

**Note about NGINX path rewriting:** The trailing slash in `proxy_pass` affects how the request URI is forwarded. Test the final public paths against your exact service routes.

### **12.6 Connect Next.js to the Gateway**

Create an environment variable:

```
API_GATEWAY_URL=http://localhost:8080
```

Then create a Next.js Route Handler:

```
// frontend/next-app/src/app/api/orders/route.ts

import { NextRequest, NextResponse } from "next/server";

export async function POST(request: NextRequest) {
  try {
    // Authenticate and authorize the user here.

    const body = await request.json();

    const response = await fetch(
      `${process.env.API_GATEWAY_URL}/api/orders`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(body),
        signal: AbortSignal.timeout(5000),
        cache: "no-store",
      }
    );

    const result = await response.json();

    return NextResponse.json(result, {
      status: response.status,
    });
  } catch (error) {
    console.error("Backend request failed", error);

    return NextResponse.json(
      { message: "Backend service unavailable" },
      { status: 503 }
    );
  }
}
```

The browser calls:

```
POST /api/orders
```

Next.js forwards the request to the API Gateway.

The gateway forwards it to the Order Service.

### **12.7 Understand the Complete Request Flow**

```
1. Browser submits order
          |
          v
2. Next.js Route Handler
          |
          v
3. API Gateway
          |
          v
4. Order Service
          |
          v
5. Product Service
          |
          v
6. Order Service creates order
          |
          v
7. Response returns to browser
```

This demonstrates how a Next.js application can serve as the frontend and BFF while Node.js services own backend business capabilities.

**Important architecture distinction:**

- Next.js owns the frontend experience and possibly frontend-specific aggregation.
- API Gateway owns centralized API routing and gateway policies.
- Order Service owns order business logic and order data.
- Product Service owns product business logic and product data.

Do not let the frontend directly access internal service databases.

## **13. Docker and Containerization for Microservices**

When you have multiple microservices, you need a consistent way to package and run them.

This is where Docker becomes useful.

### **13.1 What Is Containerization?**

Containerization packages an application with its dependencies into a container image.

A container can run consistently across different environments when the runtime and required external services are configured correctly.

For example:

```
Product Service
      |
      v
Product Service Docker Image
      |
      v
Product Service Container
```

Each microservice can have its own Docker image and container.

### **13.2 Create a Dockerfile for Product Service**

```
# services/product-service/Dockerfile

FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

EXPOSE 4001

CMD ["npm", "start"]
```

This assumes your `package.json` contains suitable `build` and `start` scripts and that a TypeScript compiler configuration exists.

For production, consider a multi-stage build to avoid shipping development dependencies.

### **13.3 Create Docker Compose**

At the project root:

```
services:
  product-service:
    build: ./services/product-service
    ports:
      - "4001:4001"
    environment:
      PORT: 4001

  order-service:
    build: ./services/order-service
    ports:
      - "4002:4002"
    environment:
      PORT: 4002
      PRODUCT_SERVICE_URL: http://product-service:4001
    depends_on:
      - product-service
```

Start services:

```
docker compose up --build
```

Stop services:

```
docker compose down
```

View running containers:

```
docker compose ps
```

View logs:

```
docker compose logs -f
```

### **13.4 Docker vs Kubernetes**

These are different technologies.

| Docker                                                   | Kubernetes                                                                     |
| -------------------------------------------------------- | ------------------------------------------------------------------------------ |
| Containerization platform and tooling                    | Container orchestration platform                                               |
| Builds and runs containers                               | Manages containerized workloads across a cluster                               |
| Provides Docker Compose for local multi-container setups | Provides service discovery, scheduling, scaling, and self-healing capabilities |
| Suitable for local development and simpler deployments   | Useful for more complex clustered deployments                                  |

### **13.5 What Is Kubernetes?**

Kubernetes is a platform for deploying, managing, and scaling containerized applications.

Important Kubernetes concepts:

| Concept    | Meaning                              |
| ---------- | ------------------------------------ |
| Pod        | Smallest deployable workload unit    |
| Deployment | Manages desired application replicas |
| Service    | Provides a stable network endpoint   |
| ConfigMap  | Stores non-sensitive configuration   |
| Secret     | Stores sensitive configuration data  |
| Ingress    | Manages external HTTP/HTTPS access   |
| Namespace  | Logical separation within a cluster  |
| HPA        | Horizontal Pod Autoscaler            |

Example:

```
Kubernetes Cluster
        |
        +---------------------+
        |                     |
        v                     v
 Product Service         Order Service
        |                     |
   +----+----+           +----+----+
   |         |           |         |
   v         v           v         v
 Pod 1     Pod 2        Pod 1     Pod 2
```

Kubernetes can maintain multiple instances of a service and distribute traffic through a Kubernetes Service.

**Important:** You do not need Kubernetes for every microservices project. Docker Compose, managed container platforms, or simpler hosting solutions may be enough.

## **14. Deployment and Scaling Strategies**

One of the major benefits of microservices is independent deployment and scaling.

### **14.1 Independent Deployment**

Imagine you have:

```
User Service      Version 1.2
Product Service   Version 2.0
Order Service     Version 1.5
Payment Service   Version 1.1
```

You update only the Payment Service.

You can deploy:

```
Payment Service   Version 1.2
```

The other services do not necessarily need redeployment.

However, compatibility between service contracts must be maintained.

### **14.2 Horizontal vs Vertical Scaling**

**Vertical Scaling**

Increasing the resources of one machine.

Example:

```
Before:
2 CPU cores
4 GB RAM

After:
8 CPU cores
16 GB RAM
```

**Horizontal Scaling**

Adding more application instances.

Example:

```
Before:
Order Service
     |
   1 Instance

After:
       Load Balancer
        /        \
       v          v
   Instance 1  Instance 2
```

| Vertical Scaling                       | Horizontal Scaling                          |
| -------------------------------------- | ------------------------------------------- |
| Increase resources of one machine      | Add more instances                          |
| Has physical or service limits         | Can expand across multiple machines         |
| Often simpler initially                | Requires distributed coordination           |
| Does not inherently provide redundancy | Can improve availability with proper design |

### **14.3 Auto Scaling**

Auto Scaling automatically adjusts the number of service instances based on configured conditions.

For example:

```
CPU usage < 30%
      |
      v
2 instances

CPU usage > 70%
      |
      v
4 instances
```

Other scaling signals can include:

- Request rate.
- Queue length.
- Response latency.
- Memory consumption.
- Custom business metrics.

### **14.4 Deployment Strategies**

| Strategy              | Explanation                                                  |
| --------------------- | ------------------------------------------------------------ |
| Rolling Deployment    | Gradually replaces old instances with new ones               |
| Blue-Green Deployment | Maintains two environments and switches traffic              |
| Canary Deployment     | Sends a small percentage of traffic to the new version first |
| Recreate Deployment   | Stops old version and starts new version                     |

For critical services, Canary or Blue-Green deployments can reduce release risk.

### **14.5 Database Migrations in Microservices**

Each service should manage its own database schema and migrations.

For example:

```
User Service
    |
    v
User Database
    |
    v
User Migrations

Order Service
    |
    v
Order Database
    |
    v
Order Migrations
```

Do not assume that every service can update the same shared schema independently.

Use backward-compatible migration strategies when deploying services independently:

1. Add new fields or tables.
2. Deploy code that supports old and new formats.
3. Migrate existing data.
4. Remove obsolete fields only after all consumers are compatible.

## **15. Important Microservices Design Patterns**

Microservices require additional patterns to handle distributed-system problems.

You have already studied some of these in Phase 25 and Phase 26. Let's connect them to this architecture.

### **15.1 Circuit Breaker Pattern**

A Circuit Breaker prevents repeated calls to a failing service.

Imagine Payment Service is unavailable.

Without a circuit breaker:

```
Order Service
     |
     v
Payment Service
     |
     v
Repeated failures
```

The Order Service keeps making requests, wasting resources.

With a circuit breaker:

```
Order Service
     |
     v
Circuit Breaker
     |
     v
Payment Service
```

The circuit breaker generally has three states:

| State     | Behavior                                      |
| --------- | --------------------------------------------- |
| Closed    | Requests are allowed                          |
| Open      | Requests are blocked temporarily              |
| Half-Open | A limited number of test requests are allowed |

If the service recovers, the circuit can return to Closed.

### **15.2 Retry Pattern**

A Retry Pattern repeats an operation after a temporary failure.

Example:

```
Attempt 1 → Failed
Attempt 2 → Failed
Attempt 3 → Success
```

Use exponential backoff to avoid overwhelming a struggling service.

Example delay:

```
Attempt 1: 1 second
Attempt 2: 2 seconds
Attempt 3: 4 seconds
Attempt 4: 8 seconds
```

Add jitter to avoid many clients retrying at exactly the same time.

**Important:** Only retry operations that are safe to retry. For payment and order creation, use idempotency keys to avoid duplicate effects.

### **15.3 Timeout Pattern**

Every network call should have a reasonable timeout.

Without a timeout, a request may wait too long for an unavailable service.

```
const response = await fetch(
  "http://payment-service:4003/payments",
  {
    method: "POST",
    signal: AbortSignal.timeout(5000),
  }
);
```

The request is aborted if it exceeds the configured duration.

### **15.4 Bulkhead Pattern**

The Bulkhead Pattern isolates resources so that one failing dependency does not exhaust resources used by unrelated operations.

For example:

```
Order Service
     |
     +---- Payment Connection Pool
     |
     +---- Inventory Connection Pool
     |
     +---- Notification Connection Pool
```

If Notification Service becomes slow, it should not consume all resources needed for payment processing.

### **15.5 Saga Pattern**

A Saga manages a business transaction across multiple services through a sequence of local transactions and compensating actions.

Example:

```
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

If payment fails:

```
Payment Failed
      |
      v
Release Inventory
      |
      v
Cancel Order
```

The compensating action reverses the business effect where possible.

A Saga does not provide one global ACID transaction across all services.

### **15.6 Idempotency**

An operation is idempotent when repeating it produces the same intended final effect as executing it once.

For example, a client retries a payment request because the network response was lost.

Without idempotency, the customer might be charged twice.

An idempotency key can help:

```
POST /payments
Idempotency-Key: order-101-payment-attempt-1
```

The Payment Service stores the result associated with the key and returns the existing result when the same operation is repeated.

### **15.7 Database per Service Pattern**

Each service owns its data.

```
User Service       → User Database
Product Service    → Product Database
Order Service      → Order Database
Payment Service    → Payment Database
```

Other services should access the data through the owning service's API or published events, rather than querying its internal database directly.

This avoids hidden database-level coupling.

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1

## **16. Common Microservices Mistakes**

### **Mistake 1: Creating Too Many Microservices**

A common mistake is splitting every small feature into its own service.

For example:

```
Login Service
Logout Service
Register Service
Password Service
Email Service
```

These functionalities may belong together in a User or Identity Service.

Too many small services increase network communication, deployment overhead, and maintenance complexity.

### **Mistake 2: Sharing One Database Between All Services**

Incorrect:

```
User Service --------+
                     |
Order Service -------+---- Shared Database
                     |
Payment Service -----+
```

This creates database-level coupling.

Better:

```
User Service -------- User DB

Order Service ------- Order DB

Payment Service ----- Payment DB
```

Each service owns its data.

### **Mistake 3: Directly Accessing Another Service's Database**

For example, Order Service should not directly query the Payment Service's database.

Instead:

```
Order Service
      |
      v
Payment Service API
      |
      v
Payment Database
```

Or it can consume published payment events.

### **Mistake 4: Too Much Synchronous Communication**

Consider:

```
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

If every operation waits for the next service, response times can increase and failures can cascade.

Use asynchronous events for operations that do not require an immediate response.

### **Mistake 5: Ignoring Distributed Transactions**

There is no single ordinary database transaction spanning independent databases.

Use patterns such as:

- Saga.
- Transactional Outbox.
- Idempotent Consumers.
- Eventual Consistency.

### **Mistake 6: No Observability**

Without centralized logs, metrics, and traces, debugging production failures becomes difficult.

### **Mistake 7: Treating the API Gateway as the Entire Backend**

The gateway should not contain every business rule.

It should primarily manage API-level concerns and forward requests to the appropriate service.

### **Mistake 8: Assuming Microservices Automatically Improve Performance**

Microservices introduce network communication.

A monolithic in-process function call is generally cheaper than an HTTP call to another service.

Microservices can improve independent scalability and organizational flexibility, but they do not automatically make individual requests faster.

### **Mistake 9: No API Versioning or Contract Management**

If one service changes its API, existing consumers may break.

Use backward-compatible API changes, contract testing, and a clear deprecation strategy.

### **Mistake 10: Deploying Microservices Without CI/CD**

Every service needs an appropriate process for:

- Automated testing.
- Building container images.
- Security scanning.
- Deployment.
- Health checks.
- Rollback.

## **17. Microservices vs Service-Oriented Architecture (SOA)**

These two architectures are related, but they are not identical.

| Feature        | SOA                                                            | Microservices                              |
| -------------- | -------------------------------------------------------------- | ------------------------------------------ |
| Main goal      | Enterprise-wide service integration                            | Independently deployable business services |
| Service size   | Can be relatively large                                        | Usually smaller and business-focused       |
| Communication  | Often enterprise messaging or middleware                       | Lightweight APIs and messaging             |
| Data ownership | Can involve shared enterprise data                             | Usually service-owned data                 |
| Deployment     | Can be independently deployed, but often centrally coordinated | Strong emphasis on independent deployment  |
| Governance     | Often centralized                                              | Often more decentralized                   |

**Important:** The distinction is not absolute. Modern SOA and Microservices can share many architectural principles.

## **18. Interview Questions and Answers**

### **Basic Level**

**Q1. What is Microservices Architecture?**

Microservices Architecture divides an application into small, autonomous, independently deployable services, each responsible for a business capability.

**Q2. What is a Monolithic Architecture?**

A Monolithic Architecture is an application deployed as one unit, where multiple functionalities run within the same application boundary.

**Q3. What is a Modular Monolith?**

A Modular Monolith is a single deployable application organized into well-defined business modules with clear internal boundaries.

**Q4. What is an API Gateway?**

An API Gateway is an entry point that routes client requests to appropriate backend services and can provide cross-cutting API capabilities such as authentication and rate limiting.

**Q5. What is Service Discovery?**

Service Discovery is the mechanism through which services locate available instances of other services.

**Q6. What is the Database per Service pattern?**

Each microservice owns and manages its own database or data store.

**Q7. What is Docker?**

Docker is a platform and toolset used to build, package, distribute, and run applications in containers.

### **Intermediate Level**

**Q8. What is the difference between synchronous and asynchronous communication?**

Synchronous communication waits for a response, whereas asynchronous communication allows the producer to continue without waiting for the consumer to finish processing.

**Q9. What is the difference between an API Gateway and a Reverse Proxy?**

A Reverse Proxy forwards traffic to backend servers. An API Gateway generally provides additional API-specific functionality such as authentication, rate limiting, and request transformation.

**Q10. What is the Backend for Frontend pattern?**

BFF is an architectural pattern where a backend layer is tailored to the requirements of a particular frontend application.

**Q11. What is the difference between horizontal and vertical scaling?**

Vertical scaling increases resources on one machine. Horizontal scaling adds more application instances or machines.

**Q12. What is Circuit Breaker?**

Circuit Breaker prevents repeated calls to a failing dependency by temporarily blocking requests and allowing controlled recovery attempts.

**Q13. What is the difference between Docker and Kubernetes?**

Docker provides containerization tooling, while Kubernetes orchestrates containerized applications across a cluster.

**Q14. What is Service-to-Service Authentication?**

It verifies the identity of one service when it communicates with another service, using mechanisms such as OAuth 2.0 client credentials or mutual TLS.

### **Advanced Level**

**Q15. Why should microservices not share a database?**

Sharing a database can create hidden coupling through common schemas, queries, and migrations. Independent data ownership helps services evolve separately.

**Q16. What is Eventual Consistency?**

Eventual Consistency means distributed data may temporarily differ but is expected to converge when updates and events are successfully processed.

**Q17. What is the Saga Pattern?**

Saga coordinates a distributed business transaction through local transactions and compensating actions.

**Q18. What is the Bulkhead Pattern?**

Bulkhead isolates resources so failures or resource exhaustion in one dependency do not affect unrelated operations.

**Q19. What is Distributed Tracing?**

Distributed Tracing follows a request across multiple services using trace and span information to identify latency and failures.

**Q20. What is Idempotency?**

Idempotency ensures that repeating an operation does not produce additional unintended effects.

**Q21. What is the difference between API Gateway and Service Discovery?**

API Gateway provides a client-facing entry point and request routing. Service Discovery helps services locate available instances of other services.

**Q22. Why might a Modular Monolith be better than Microservices?**

It provides clear module boundaries without introducing network communication, distributed transactions, and additional deployment infrastructure.

**Q23. How do you prevent cascading failures in microservices?**

Use timeouts, circuit breakers, bulkheads, controlled retries, rate limiting, and graceful degradation.

**Q24. How would you design an e-commerce microservices system?**

I would identify business boundaries such as User, Product, Order, Payment, and Inventory. Each service would own its data and business logic, communicate through APIs or events, and be deployed independently. I would add an API Gateway, service authentication, observability, and appropriate distributed transaction patterns.

## **19. Practical Assignment – Build an E-commerce Microservices System**

Hands-on project

Build a small e-commerce application using Next.js and Node.js microservices.

### **Technology Stack**

| Component        | Technology              |
| ---------------- | ----------------------- |
| Frontend         | Next.js 16 + TypeScript |
| Backend          | Node.js + Express       |
| Database         | MongoDB                 |
| API Gateway      | NGINX                   |
| Containerization | Docker                  |
| Messaging        | RabbitMQ                |
| Testing          | Vitest + Playwright     |
| Monitoring       | OpenTelemetry           |

### **Task 1 – Create Microservices**

Create these five services:

1. User Service
2. Product Service
3. Order Service
4. Payment Service
5. Notification Service

Each service should have:

- Its own codebase or independently managed application.
- Its own API.
- Its own business logic.
- Its own data ownership.
- Its own environment configuration.

### **Task 2 – Create the API Gateway**

Configure routes:

| Endpoint          | Service         |
| ----------------- | --------------- |
| `/api/users/*`    | User Service    |
| `/api/products/*` | Product Service |
| `/api/orders/*`   | Order Service   |
| `/api/payments/*` | Payment Service |

### **Task 3 – Implement Synchronous Communication**

Implement:

- Order Service calls Product Service.
- Order Service retrieves product information.
- Order Service validates the requested quantity.

### **Task 4 – Implement Asynchronous Communication**

Use RabbitMQ.

Publish these events:

- `OrderCreated`
- `PaymentSucceeded`
- `PaymentFailed`
- `InventoryReserved`
- `OrderCancelled`

### **Task 5 – Implement Authentication**

- User login.
- JWT verification.
- Service-to-service authentication.
- Role-based authorization.
- Secure environment variables.

### **Task 6 – Dockerize the Services**

Create Dockerfiles and Docker Compose configuration.

Run all services locally.

### **Task 7 – Implement Reliability**

Implement:

- Timeouts.
- Retry with exponential backoff.
- Circuit breaker.
- Idempotency.
- Error handling.

### **Task 8 – Add Observability**

Implement:

- Structured logging.
- Correlation IDs.
- Distributed tracing.
- Health-check endpoints.

### **Task 9 – Deploy**

Deploy the frontend and backend services.

Practice independent service deployment and rollback.

### **Assignment Completion Checklist**

**Project progress**

# **0%**

0 of 15 tasks completed

Create User Service

Create Product Service

Create Order Service

Create Payment Service

Create Notification Service

Implement API Gateway

Implement synchronous REST communication

Implement RabbitMQ event communication

Implement service authentication

Create separate service data stores

Dockerize all services

Implement timeouts and retries

Implement idempotency and Circuit Breaker

Add centralized logging and tracing

Deploy services independently

## **20. Quick Revision Notes**

| Concept                   | Remember                                     |
| ------------------------- | -------------------------------------------- |
| Microservices             | Independently deployable business services   |
| Monolith                  | One application deployment unit              |
| Modular Monolith          | One deployment with well-defined modules     |
| Service Boundary          | Defines a service's business responsibility  |
| Bounded Context           | Boundary of a domain model                   |
| API Gateway               | Client-facing API entry point                |
| BFF                       | Backend tailored to a frontend               |
| Synchronous               | Request and response                         |
| Asynchronous              | Message or event-based communication         |
| Message Broker            | Transfers messages between services          |
| Service Discovery         | Finds service instances                      |
| Centralized Configuration | Consistent configuration management          |
| Service Authentication    | Verifies service identity                    |
| Observability             | Understanding system behavior                |
| Logs                      | Records of events                            |
| Metrics                   | Numerical measurements                       |
| Traces                    | Request journey across services              |
| Docker                    | Containerization                             |
| Kubernetes                | Container orchestration                      |
| Circuit Breaker           | Stops repeated calls to failing dependencies |
| Retry                     | Attempts an operation again                  |
| Bulkhead                  | Isolates resources                           |
| Saga                      | Coordinates distributed transactions         |
| Idempotency               | Prevents duplicate effects                   |

### **The 7 Most Important Things to Remember**

1. Microservices are independently deployable services organized around business capabilities.
2. Start with good domain boundaries, not arbitrary technical divisions.
3. An API Gateway provides a common entry point for clients.
4. Use synchronous communication for immediate responses and asynchronous communication for background workflows.
5. Each service should own its data.
6. Distributed systems require resilience, security, and observability.
7. Microservices introduce complexity, so a Modular Monolith is often the better starting point.

## **21. Phase 29 Completion Checklist**

**Learning progress**

# **0/12**

I understand Monolithic, Modular Monolith and Microservices.

I understand how to define service boundaries.

I understand the relationship between DDD and Microservices.

I can explain API Gateway and BFF.

I understand synchronous and asynchronous communication.

I understand Service Discovery.

I understand centralized configuration.

I understand service-to-service authentication.

I understand Logs, Metrics and Distributed Tracing.

I can explain Docker and Kubernetes.

I understand scaling and deployment strategies.

I understand Circuit Breaker, Retry, Bulkhead and Saga.

## **22. Next Phase Preview**

Phase 30 – Advanced Backend Architecture

# **GraphQL, Apollo Server and Advanced API Design**

In the next phase, we will explore GraphQL and compare it with REST APIs.

**Topics planned:**

1. What is GraphQL?
2. REST vs GraphQL.
3. GraphQL Schema Definition Language (SDL).
4. Types, Queries and Mutations.
5. Resolvers.
6. Arguments and Variables.
7. Input Types and Enums.
8. GraphQL Relationships.
9. Apollo Server.
10. GraphQL with Node.js and TypeScript.
11. GraphQL with Next.js.
12. Authentication and Authorization.
13. DataLoader and the N+1 Problem.
14. Pagination and Filtering.
15. Error Handling.
16. Subscriptions and Real-Time GraphQL.
17. GraphQL Security and Performance.
18. GraphQL with MongoDB.

**Phase 29 is complete.** When you're ready, say **“OK, Phase 30”** to continue with detailed notes.

### **References**

- [Microsoft – Microservices Architecture Style](https://learn.microsoft.com/en-us/azure/architecture/microservices/?utm_source=chatgpt.com).
- [Microsoft – Identify Microservice Boundaries](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/microservice-boundaries?source=recommendations\&utm_source=chatgpt.com).
- [AWS – What Is Microservices Architecture?](https://aws.amazon.com/what-is/microservices-architecture/?utm_source=chatgpt.com).
- [Next.js – Backend for Frontend](https://nextjs.org/docs/app/guides/backend-for-frontend?utm_source=chatgpt.com).
- [Microsoft – Microservices Best Practices](https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/microservices?utm_source=chatgpt.com).