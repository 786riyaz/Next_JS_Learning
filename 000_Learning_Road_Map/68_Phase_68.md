# Phase 68 – Advanced Backend Engineering, Enterprise API Architecture, Performance and Production Optimization

**Level:** Advanced to Expert  
**Domain:** Backend Engineering, Node.js, Enterprise Architecture, API Development, Security, Performance Engineering and Production Systems.

**Primary Technology Stack:**
- Node.js
- TypeScript
- Express.js
- Next.js
- MySQL
- MongoDB
- Redis
- Sequelize / Prisma
- REST API
- GraphQL
- gRPC
- Docker
- Nginx
- AWS
- Prometheus
- Grafana

---

# 1. Learning Objectives

In this phase, you will learn how to design, develop, secure, optimize and deploy enterprise-grade backend applications.

You already have experience with Node.js, Express, MySQL, MongoDB, Sequelize, Next.js and deployment tools. We will build on those foundations and move toward enterprise backend engineering.

Consider an e-commerce application.

Initially, its architecture might look like this:

```text
Next.js Frontend
       |
       v
Express.js Backend
       |
       v
MySQL Database
```

As the application grows, it may need:

- Authentication and authorization.
- Multiple user roles.
- Payment processing.
- Background jobs.
- Distributed caching.
- API versioning.
- Multiple backend modules.
- Third-party integrations.
- Real-time notifications.
- Rate limiting.
- Performance monitoring.
- Horizontal scaling.

The architecture becomes more sophisticated:

```text
                  Next.js Frontend
                         |
                         v
                    Nginx / CDN
                         |
                         v
                     API Gateway
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
         User API    Order API    Product API
             |           |           |
             +-----------+-----------+
                         |
              +----------+----------+
              |          |          |
              v          v          v
            MySQL      Redis     Message Queue
              |                     |
              v                     v
           Database             Background Workers
```

Your objective is to understand how to build and maintain systems like this.

## Complete Phase 68 Roadmap

1. Advanced Backend Architecture.
2. Enterprise Application Architecture.
3. Monolithic Architecture.
4. Modular Monolith.
5. Microservices Architecture.
6. Clean Architecture.
7. Hexagonal Architecture.
8. Domain-Driven Design (DDD).
9. Bounded Contexts.
10. Entities and Value Objects.
11. Aggregates and Aggregate Roots.
12. Repository Pattern.
13. Unit of Work Pattern.
14. Dependency Injection.
15. Inversion of Control.
16. Advanced REST API Design.
17. RESTful Resource Modeling.
18. API Versioning.
19. API Documentation with OpenAPI.
20. GraphQL Architecture.
21. gRPC.
22. Protocol Buffers.
23. API Gateway Architecture.
24. Backend for Frontend (BFF).
25. Authentication Architecture.
26. Authorization Architecture.
27. Role-Based Access Control (RBAC).
28. Attribute-Based Access Control (ABAC).
29. OAuth 2.0.
30. OpenID Connect.
31. JWT Security.
32. Session Management.
33. Refresh Token Rotation.
34. Multi-Tenant Architecture.
35. Backend Caching.
36. Distributed Rate Limiting.
37. Request Validation.
38. Error Handling Architecture.
39. Background Job Processing.
40. Worker Architecture.
41. Distributed Task Queues.
42. API Performance Optimization.
43. Database Access Optimization.
44. Memory Management.
45. Node.js Event Loop Performance.
46. Horizontal Backend Scaling.
47. Graceful Shutdown.
48. Health Checks.
49. Production Logging and Monitoring.
50. Backend Security and Load Testing.
51. Production Deployment.
52. Complete Enterprise Node.js Backend Project.

---

# PART 1: ADVANCED BACKEND ARCHITECTURE

## 2. What Is Backend Architecture?

Backend Architecture describes how the server-side components of an application are organized and how they communicate.

It defines:

- Application structure.
- Business logic organization.
- Database access.
- API communication.
- Authentication.
- Authorization.
- Error handling.
- External integrations.
- Background processing.
- Deployment strategy.

A poorly structured backend becomes difficult to maintain as the application grows.

### Basic Backend Architecture

```text
Client
  |
  v
Routes
  |
  v
Controllers
  |
  v
Services
  |
  v
Database
```

### Advanced Backend Architecture

```text
                  Client
                    |
                    v
                API Gateway
                    |
                    v
               Controllers
                    |
                    v
              Application Layer
                    |
                    v
               Domain Layer
                    |
                    v
            Infrastructure Layer
                    |
        +-----------+-----------+
        |           |           |
        v           v           v
      MySQL       Redis      External APIs
```

### Responsibilities

| Layer | Responsibility |
|---|---|
| API Gateway | Entry point for requests |
| Controller | Handles HTTP requests |
| Application | Coordinates use cases |
| Domain | Contains business rules |
| Infrastructure | Database and external integrations |

The goal is to separate business logic from technical implementation details.

---

# PART 2: MONOLITHIC ARCHITECTURE

## 3. What Is a Monolithic Architecture?

A Monolithic Architecture is an application architecture in which the major application components are deployed as one application unit.

Example:

```text
             E-Commerce Application
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
    Users            Orders          Products
       |               |               |
       +---------------+---------------+
                       |
                       v
                    MySQL
```

All modules may share:

- One codebase.
- One deployment unit.
- One runtime.
- One database.

### Example Project Structure

```text
backend/
│
├── src/
│   ├── modules/
│   │   ├── users/
│   │   ├── orders/
│   │   ├── products/
│   │   └── payments/
│   │
│   ├── middleware/
│   ├── config/
│   ├── database/
│   ├── utils/
│   └── app.ts
│
├── package.json
└── tsconfig.json
```

### Advantages

- Easy to develop initially.
- Simple deployment.
- Easy local debugging.
- Straightforward transactions.
- Less infrastructure complexity.

### Disadvantages

- Large codebase can become difficult to maintain.
- Modules may become tightly coupled.
- Independent scaling is difficult.
- Deployment affects the whole application.
- A defect can affect multiple features.

### When Should You Use a Monolith?

Use a monolith when:

- Building a small or medium-sized application.
- Working with a small development team.
- Business requirements are still evolving.
- Independent service deployment is unnecessary.

**Important:** Monolithic architecture is not inherently bad. A well-structured monolith can support substantial production workloads.

---

# PART 3: MODULAR MONOLITH

## 4. What Is a Modular Monolith?

A Modular Monolith is a single deployable application organized into independent, well-defined business modules.

Unlike an unstructured monolith, each module has clear responsibilities and controlled dependencies.

Example:

```text
                  Backend Application
                          |
         +----------------+----------------+
         |                |                |
         v                v                v
    User Module       Order Module     Product Module
         |                |                |
         v                v                v
    User Service      Order Service    Product Service
         |                |                |
         +----------------+----------------+
                          |
                          v
                       Database
```

### Example Folder Structure

```text
src/
│
├── modules/
│   │
│   ├── users/
│   │   ├── user.controller.ts
│   │   ├── user.service.ts
│   │   ├── user.repository.ts
│   │   ├── user.routes.ts
│   │   └── user.types.ts
│   │
│   ├── orders/
│   │   ├── order.controller.ts
│   │   ├── order.service.ts
│   │   ├── order.repository.ts
│   │   ├── order.routes.ts
│   │   └── order.types.ts
│   │
│   └── products/
│       ├── product.controller.ts
│       ├── product.service.ts
│       ├── product.repository.ts
│       ├── product.routes.ts
│       └── product.types.ts
│
├── shared/
├── config/
├── database/
└── app.ts
```

### Advantages

- Clear module boundaries.
- Easier maintenance.
- Lower initial infrastructure complexity than microservices.
- Better testability.
- Easier future extraction into separate services.

### Modular Monolith vs Traditional Monolith

| Traditional Monolith | Modular Monolith |
|---|---|
| Modules may be tightly coupled | Modules have defined boundaries |
| Shared code can become disorganized | Shared code is controlled |
| Business logic may spread everywhere | Logic is organized by domain |
| Difficult to extract components | Easier to extract modules |

**Recommendation:** For many Node.js applications, a modular monolith is an excellent starting architecture.

---

# PART 4: MICROSERVICES ARCHITECTURE

## 5. What Is Microservices Architecture?

Microservices Architecture divides an application into independently deployable services.

Each service owns a specific business capability.

Example:

```text
                  API Gateway
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
   User Service   Order Service   Product Service
       |               |               |
       v               v               v
   User DB          Order DB        Product DB

                       |
                       v
                 Payment Service
                       |
                       v
                   Payment DB
```

### Characteristics

- Independent deployment.
- Business-oriented service boundaries.
- Network-based communication.
- Independent scaling.
- Separate service ownership.
- Distributed failure handling.

### Example Services

| Service | Responsibility |
|---|---|
| User Service | Registration and profiles |
| Product Service | Product catalog |
| Order Service | Order lifecycle |
| Payment Service | Payment processing |
| Notification Service | Email and push notifications |
| Inventory Service | Stock management |

### Advantages

- Independent deployment.
- Independent scaling.
- Technology flexibility.
- Better team ownership.
- Fault isolation when properly designed.

### Disadvantages

- Distributed system complexity.
- Network failures.
- Distributed transactions.
- More infrastructure.
- More complicated debugging.
- Service discovery requirements.
- Data consistency challenges.

### Monolith vs Modular Monolith vs Microservices

| Feature | Monolith | Modular Monolith | Microservices |
|---|---|---|---|
| Deployment | One unit | One unit | Multiple units |
| Code organization | Can be unstructured | Strong module boundaries | Independent services |
| Database | Usually shared | Often shared | Service-owned data |
| Communication | In-process | In-process | Network and messaging |
| Operational complexity | Low initially | Low to moderate | Higher |
| Independent scaling | Limited | Limited | Supported |
| Initial development | Simple | Moderate | Complex |

**Important:** Do not introduce microservices simply because they are popular. Introduce them when organizational or technical requirements justify their operational cost.

---

# PART 5: CLEAN ARCHITECTURE

## 6. What Is Clean Architecture?

Clean Architecture is an architectural approach that separates business rules from infrastructure and external frameworks.

Its central principle is:

**Dependencies should point inward, toward business rules.**

A common representation has four layers:

```text
+-----------------------------------------+
|             Frameworks                  |
|                                         |
|  Express, MySQL, Redis, External APIs    |
|                                         |
|  +-----------------------------------+  |
|  |          Interface Adapters       |  |
|  |                                   |  |
|  | Controllers, Repositories         |  |
|  |                                   |  |
|  |  +-----------------------------+  |  |
|  |  |      Application Layer      |  |  |
|  |  |                             |  |  |
|  |  | Use Cases                    |  |  |
|  |  |                             |  |  |
|  |  |  +-----------------------+  |  |  |
|  |  |  |     Domain Layer      |  |  |  |
|  |  |  |                       |  |  |  |
|  |  |  | Business Rules        |  |  |  |
|  |  |  +-----------------------+  |  |  |
|  |  +-----------------------------+  |  |
|  +-----------------------------------+  |
+-----------------------------------------+
```

## 7. Clean Architecture Layers

### 1. Domain Layer

Contains core business rules.

Examples:

- User entity.
- Order entity.
- Payment rules.
- Business validation.

It should not depend on Express, MySQL or Redis.

### 2. Application Layer

Contains use cases.

Examples:

- RegisterUser.
- CreateOrder.
- ProcessPayment.
- CancelOrder.

It coordinates business operations.

### 3. Interface Adapter Layer

Converts data between the external world and the application.

Examples:

- Controllers.
- Presenters.
- Repository implementations.
- Request and response mappers.

### 4. Framework and Infrastructure Layer

Contains technical implementations.

Examples:

- Express.
- Sequelize.
- Prisma.
- MySQL.
- Redis.
- External APIs.

---

## 8. Clean Architecture Practical Example

Suppose we want to create a user.

### Step 1: Define the Repository Interface

```typescript
// domain/repositories/user.repository.ts

export interface UserRepository {
    create(data: {
        name: string;
        email: string;
    }): Promise<void>;

    findByEmail(email: string): Promise<boolean>;
}
```

This interface defines what the application needs.

It does not specify MySQL or MongoDB.

### Step 2: Create the Use Case

```typescript
// application/use-cases/register-user.ts

import { UserRepository } from "../../domain/repositories/user.repository";

export class RegisterUser {
    constructor(
        private userRepository: UserRepository
    ) {}

    async execute(data: {
        name: string;
        email: string;
    }) {
        const exists = await this.userRepository.findByEmail(
            data.email
        );

        if (exists) {
            throw new Error("User already exists");
        }

        await this.userRepository.create(data);
    }
}
```

The use case depends on the repository interface.

### Step 3: Implement the Repository

```typescript
// infrastructure/repositories/mysql-user.repository.ts

import { UserRepository } from "../../domain/repositories/user.repository";

export class MySQLUserRepository implements UserRepository {
    async create(data: {
        name: string;
        email: string;
    }): Promise<void> {
        // Insert using Sequelize or another database library.
        console.log("Creating user:", data);
    }

    async findByEmail(email: string): Promise<boolean> {
        // Query MySQL.
        return false;
    }
}
```

### Step 4: Connect Dependencies

```typescript
const repository = new MySQLUserRepository();

const registerUser = new RegisterUser(repository);
```

Now the use case does not know which database implementation is being used.

You could replace MySQL with MongoDB by creating another implementation of the same interface.

### Benefits

- Easier unit testing.
- Lower coupling.
- Database independence at the application boundary.
- Easier infrastructure replacement.
- Clear business logic.

**Important:** Clean Architecture does not require every application to have many layers or interfaces. Apply abstractions where they provide meaningful separation.

---

# PART 6: HEXAGONAL ARCHITECTURE

## 9. What Is Hexagonal Architecture?

Hexagonal Architecture is also known as Ports and Adapters Architecture.

It separates application logic from external systems through interfaces called ports.

```text
                REST API
                   |
                 Adapter
                   |
                   v
        +-----------------------+
        |                       |
        |      Application     |
        |                       |
        |     Business Logic    |
        |                       |
        +-----------------------+
             |           |
           Port        Port
             |           |
          Adapter     Adapter
             |           |
             v           v
           MySQL       Redis
```

### Ports

Ports define the interfaces through which the application communicates with external systems.

Examples:

```typescript
interface PaymentGateway {
    charge(amount: number): Promise<boolean>;
}
```

### Adapters

Adapters implement those interfaces.

```typescript
class StripePaymentAdapter implements PaymentGateway {
    async charge(amount: number): Promise<boolean> {
        // Integrate with a payment provider.
        return true;
    }
}
```

### Why Use It?

Suppose you initially use one payment provider and later switch to another.

The application can continue using the same `PaymentGateway` interface.

Only the adapter changes.

### Clean vs Hexagonal Architecture

Both aim to isolate business logic from infrastructure.

| Clean Architecture | Hexagonal Architecture |
|---|---|
| Emphasizes dependency direction and layers | Emphasizes ports and adapters |
| Commonly represented as concentric layers | Commonly represented as a central application |
| Separates business and infrastructure concerns | Separates core logic from external actors |
| Use cases are central | Ports define interaction boundaries |

They are compatible approaches, not mutually exclusive architectures.

---

# PART 7: DOMAIN-DRIVEN DESIGN (DDD)

## 10. What Is Domain-Driven Design?

Domain-Driven Design is an approach to software development that emphasizes understanding and modeling the business domain.

It is especially useful when business rules are complex.

Example: An e-commerce platform.

Its business domain includes:

- Customers.
- Orders.
- Payments.
- Inventory.
- Shipping.
- Refunds.

Instead of designing the system only around technical tables and controllers, DDD starts with business concepts and rules.

### Core DDD Concepts

1. Domain.
2. Subdomain.
3. Ubiquitous Language.
4. Bounded Context.
5. Entity.
6. Value Object.
7. Aggregate.
8. Aggregate Root.
9. Repository.
10. Domain Event.

---

## 11. What Is a Domain?

A Domain is the business area that the software is designed to support.

Examples:

- Banking.
- Healthcare.
- E-commerce.
- Expense Management.
- Logistics.
- Education.

For an expense management system:

```text
Expense Management Domain
          |
   +------+------+
   |      |      |
   v      v      v
 Expense Approval Reimbursement
```

## 12. What Is a Subdomain?

A Subdomain is a smaller business area within the overall domain.

Example:

```text
E-Commerce Domain
       |
       +---- Product Management
       |
       +---- Order Management
       |
       +---- Payment Management
       |
       +---- Inventory Management
       |
       +---- Shipping Management
```

Subdomains help organize complex business systems.

## 13. What Is Ubiquitous Language?

Ubiquitous Language is a shared vocabulary used by developers and business stakeholders.

Example:

Suppose the business uses these terms:

- Order.
- Payment.
- Refund.
- Shipment.
- Delivered.
- Cancelled.

Developers should use these concepts consistently in code and discussions.

Avoid using unrelated technical names for the same business concept.

For example, if the business calls an object `Order`, do not randomly call it `PurchaseRecord` in another module unless the distinction is intentional.

---

# PART 8: BOUNDED CONTEXTS

## 14. What Is a Bounded Context?

A Bounded Context is a boundary within which a particular domain model and its terminology have a clearly defined meaning.

Example:

In an e-commerce system, a customer can have different representations in different contexts.

```text
                  E-Commerce
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Sales        Shipping      Support
       Context       Context      Context
          |            |            |
          v            v            v
       Customer     Recipient    Customer
```

The Sales Context might care about:

- Customer ID.
- Billing address.
- Purchase history.

The Shipping Context might care about:

- Recipient name.
- Delivery address.
- Contact number.

The Support Context might care about:

- Support history.
- Customer complaints.
- Account status.

These models are related, but they do not need to be identical.

### Why Use Bounded Contexts?

- Avoid giant shared domain models.
- Clarify service responsibilities.
- Reduce coupling.
- Support team ownership.
- Improve business modeling.

### Bounded Context vs Microservice

A bounded context is a domain-model boundary.

A microservice is a deployment and runtime boundary.

They often align, but they are not the same thing.

---

# PART 9: ENTITIES AND VALUE OBJECTS

## 15. What Is an Entity?

An Entity is an object identified by its identity rather than only by its attributes.

Example:

```typescript
class User {
    constructor(
        public readonly id: string,
        public name: string,
        public email: string
    ) {}
}
```

Even if the user's name changes, the user remains the same entity because its identity remains unchanged.

## 16. What Is a Value Object?

A Value Object is an object defined by its values rather than a unique identity.

Example:

```typescript
class Money {
    constructor(
        public readonly amount: number,
        public readonly currency: string
    ) {
        if (!Number.isFinite(amount)) {
            throw new Error("Invalid amount");
        }
    }
}
```

Two Money objects with the same amount and currency may be considered equal by value.

### Entity vs Value Object

| Entity | Value Object |
|---|---|
| Has identity | No independent identity |
| Identity remains stable | Equality based on values |
| Can change attributes | Preferably immutable |
| Example: User | Example: Money |
| Example: Order | Example: Address |

### Why Use Value Objects?

They help encapsulate domain rules.

For example, instead of passing an arbitrary number as a payment amount, a `Money` object can enforce currency and amount constraints.

---

# PART 10: AGGREGATES AND AGGREGATE ROOTS

## 17. What Is an Aggregate?

An Aggregate is a group of related domain objects treated as a consistency boundary.

Example:

An Order may contain:

```text
Order
  |
  +---- Order Items
  |
  +---- Shipping Details
  |
  +---- Payment Reference
```

An Aggregate Root is the primary entity through which the aggregate is accessed and modified.

For an order aggregate, `Order` may be the root.

### Example

```typescript
class Order {
    private items: {
        productId: string;
        quantity: number;
    }[] = [];

    constructor(
        public readonly id: string
    ) {}

    addItem(productId: string, quantity: number) {
        if (quantity <= 0) {
            throw new Error("Quantity must be positive");
        }

        this.items.push({
            productId,
            quantity
        });
    }

    getItems() {
        return [...this.items];
    }
}
```

The application modifies order items through the `Order` object.

### Why Use Aggregates?

- Protect business invariants.
- Define transaction boundaries.
- Control modifications.
- Reduce inconsistent states.

**Important:** An aggregate is not simply a database table or a collection of every related entity. It should be kept as small as practical.

---

# PART 11: REPOSITORY PATTERN

## 18. What Is the Repository Pattern?

The Repository Pattern abstracts data access behind an interface.

The business or application layer interacts with a repository rather than directly executing database queries.

```text
Controller
    |
    v
Service
    |
    v
Repository Interface
    |
    v
Repository Implementation
    |
    v
Database
```

### Example

Interface:

```typescript
interface ProductRepository {
    findById(id: number): Promise<Product | null>;

    save(product: Product): Promise<void>;
}
```

Implementation:

```typescript
class MySQLProductRepository implements ProductRepository {
    async findById(id: number) {
        // Query database.
        return null;
    }

    async save(product: Product) {
        // Save using Sequelize.
    }
}
```

Service:

```typescript
class ProductService {
    constructor(
        private repository: ProductRepository
    ) {}

    async getProduct(id: number) {
        return this.repository.findById(id);
    }
}
```

### Benefits

- Separates business logic from data access.
- Simplifies testing.
- Centralizes persistence logic.
- Allows alternative implementations.

### Important Consideration

Do not automatically create a repository wrapper around every ORM method.

If the wrapper adds no meaningful abstraction, it can increase complexity without providing much value.

---

# PART 12: UNIT OF WORK PATTERN

## 19. What Is the Unit of Work Pattern?

The Unit of Work Pattern coordinates multiple changes so that they can be committed or rolled back together.

Example:

When placing an order:

1. Create an order.
2. Create order items.
3. Reduce inventory.
4. Record a payment reference.

These database changes may need to happen within one transaction.

### Architecture

```text
             Order Service
                   |
                   v
                Unit of Work
                   |
        +----------+----------+
        |          |          |
        v          v          v
      Order     Inventory   Payment
    Repository Repository Repository
        |          |          |
        +----------+----------+
                   |
                   v
               Transaction
```

### Sequelize Example

```typescript
import { sequelize } from "./database";

async function createOrder(data: any) {
    return sequelize.transaction(async (transaction) => {

        const order = await Order.create(
            {
                userId: data.userId,
                total: data.total
            },
            { transaction }
        );

        await OrderItem.bulkCreate(
            data.items.map((item: any) => ({
                orderId: order.id,
                productId: item.productId,
                quantity: item.quantity
            })),
            { transaction }
        );

        return order;
    });
}
```

If the transaction fails, database changes inside it are rolled back.

**Important:** A database transaction does not automatically include an external payment provider or message broker. Those require additional reliability patterns such as Saga and Transactional Outbox.

---

# PART 13: DEPENDENCY INJECTION

## 20. What Is Dependency Injection?

Dependency Injection (DI) is a technique in which an object receives its dependencies from outside instead of creating them internally.

### Without Dependency Injection

```typescript
class UserService {
    private repository = new MySQLUserRepository();
}
```

Problem:

The service is tightly coupled to `MySQLUserRepository`.

### With Dependency Injection

```typescript
class UserService {
    constructor(
        private repository: UserRepository
    ) {}
}
```

Now the repository is provided externally.

### Benefits

- Loose coupling.
- Better testability.
- Easier replacement of implementations.
- Clear dependencies.
- Better architecture.

### Example Testing

```typescript
const fakeRepository = {
    async findByEmail() {
        return false;
    },

    async create() {
        return;
    }
};

const service = new RegisterUser(fakeRepository);
```

You can test business logic without connecting to a real database.

---

# PART 14: INVERSION OF CONTROL

## 21. What Is Inversion of Control?

Inversion of Control (IoC) is a broader design principle in which control over object creation or execution is delegated to an external mechanism.

Dependency Injection is one way of implementing IoC.

### Normal Control

```text
Application
    |
    v
Creates Dependencies
    |
    v
Executes Business Logic
```

### Inversion of Control

```text
Application Container
        |
        v
Creates Dependencies
        |
        v
Injects Dependencies
        |
        v
Executes Business Logic
```

### IoC vs DI

| IoC | DI |
|---|---|
| Broad design principle | Specific implementation technique |
| Transfers control to a framework/container or external mechanism | Supplies dependencies from outside |
| Can be implemented in multiple ways | Commonly implements IoC |

---

# PART 15: ADVANCED REST API DESIGN

## 22. What Is REST?

REST stands for Representational State Transfer.

It is an architectural style for designing networked applications.

REST APIs commonly use HTTP methods and resource-oriented URLs.

Example:

```text
GET    /api/v1/users
GET    /api/v1/users/101
POST   /api/v1/users
PATCH  /api/v1/users/101
DELETE /api/v1/users/101
```

### Important REST Principles

- Resource-oriented design.
- Stateless communication.
- Uniform interface.
- Cacheability where appropriate.
- Layered architecture.
- Resource representations.

## 23. HTTP Methods

| Method | Purpose | Example |
|---|---|---|
| GET | Retrieve resource | Get users |
| POST | Create or process | Create user |
| PUT | Replace resource representation | Replace user |
| PATCH | Partially modify resource | Update email |
| DELETE | Remove resource | Delete user |

### PUT vs PATCH

PUT usually represents replacing the target resource's state.

PATCH represents applying partial modifications.

Example:

PUT:

```json
{
  "name": "Riyaz",
  "email": "riyaz@example.com",
  "phone": "9999999999"
}
```

PATCH:

```json
{
  "email": "new@example.com"
}
```

## 24. HTTP Status Codes

| Status | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 202 | Accepted for processing |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthenticated |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Unprocessable Content |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

### 401 vs 403

**401 Unauthorized**

The request lacks valid authentication credentials.

**403 Forbidden**

The server understands the request but refuses to authorize the action.

---

# PART 16: REST API RESOURCE MODELING

## 25. How to Design RESTful URLs

Prefer nouns representing resources.

Good:

```text
GET /api/v1/products
GET /api/v1/products/101
POST /api/v1/orders
GET /api/v1/users/101/orders
```

Avoid unnecessarily embedding actions in URLs:

```text
GET /getAllProducts
POST /createNewOrder
GET /fetchUserDetails
```

Actions can still be appropriate for domain operations that are not naturally represented as CRUD resource changes.

For example:

```text
POST /api/v1/orders/101/cancel
```

This may be reasonable when cancellation represents a business command with validation and side effects.

## 26. Pagination

Pagination limits the number of records returned.

### Offset Pagination

```http
GET /api/v1/products?page=2&limit=20
```

SQL:

```sql
SELECT *
FROM products
ORDER BY id
LIMIT 20 OFFSET 20;
```

Advantages:

- Simple.
- Easy to implement.
- Supports page navigation.

Disadvantages:

- Large offsets can be expensive.
- Data changes can shift page boundaries.

### Cursor Pagination

```http
GET /api/v1/products?limit=20&cursor=1050
```

Example:

```sql
SELECT *
FROM products
WHERE id > 1050
ORDER BY id
LIMIT 20;
```

Advantages:

- Better suited to large sequential datasets.
- Avoids large offsets.
- More stable for certain changing datasets.

For cursor pagination, use a deterministic ordering and a cursor that captures the relevant ordering keys. A unique tie-breaker is important when multiple records share the same timestamp.

## 27. Filtering and Sorting

Example:

```http
GET /api/v1/products?category=electronics&minPrice=1000&sort=price
```

Backend:

```typescript
const {
    category,
    minPrice,
    sort
} = req.query;
```

Always validate and allowlist filter and sort fields.

Do not directly concatenate untrusted query parameters into SQL.

---

# PART 17: API VERSIONING

## 28. What Is API Versioning?

API Versioning allows an API to evolve without unexpectedly breaking existing consumers.

Suppose version 1 returns:

```json
{
  "firstName": "Riyaz",
  "lastName": "Khan"
}
```

Version 2 returns:

```json
{
  "fullName": "Riyaz Khan"
}
```

Existing applications may depend on the old structure.

Versioning allows both contracts to coexist during migration.

### Common API Versioning Strategies

#### 1. URL Versioning

```text
/api/v1/users
/api/v2/users
```

#### 2. Header Versioning

```http
Accept: application/vnd.example.v2+json
```

#### 3. Query Parameter Versioning

```text
/api/users?version=2
```

### Comparison

| Strategy | Advantages | Disadvantages |
|---|---|---|
| URL | Easy to understand | Version appears in URL |
| Header | Keeps resource URL stable | Less visible |
| Query | Easy to test | Can complicate caching and conventions |

### Best Practices

- Avoid unnecessary breaking changes.
- Maintain a deprecation policy.
- Document migration paths.
- Communicate sunset dates.
- Monitor usage of old versions.
- Remove old versions only after a planned transition.

---

# PART 18: OPENAPI AND SWAGGER

## 29. What Is OpenAPI?

OpenAPI is a standard, language-independent specification for describing HTTP APIs.

It documents:

- Endpoints.
- HTTP methods.
- Parameters.
- Request bodies.
- Response schemas.
- Authentication schemes.
- Status codes.

### Example OpenAPI Specification

```yaml
openapi: 3.2.1

info:
  title: User API
  version: 1.0.0

paths:
  /users:
    get:
      summary: Get all users

      responses:
        "200":
          description: Users retrieved successfully
```

### Why Use OpenAPI?

- API documentation.
- Client generation.
- Contract testing.
- Team collaboration.
- API discovery.
- Integration with API tooling.

### Swagger vs OpenAPI

| OpenAPI | Swagger |
|---|---|
| API specification | Tooling ecosystem associated with the specification |
| Defines API contracts | Provides tools to document and interact with APIs |
| Machine-readable format | Includes UI and other tools |

Official specification: [OpenAPI Specification](https://spec.openapis.org/oas/latest.html). The current specification describes a language-independent interface for HTTP APIs. [OpenAPI Initiative Publications](https://spec.openapis.org/oas/latest.html?utm_source=chatgpt.com)

---

# PART 19: GRAPHQL ARCHITECTURE

## 30. What Is GraphQL?

GraphQL is a query language for APIs and a runtime for executing those queries against a schema.

It allows clients to request the fields they need.

### REST Example

```http
GET /api/users/101
GET /api/users/101/orders
GET /api/users/101/profile
```

Multiple requests may be required.

### GraphQL Example

One query can request related data:

```graphql
query {
  user(id: "101") {
    name
    email

    orders {
      id
      total
    }

    profile {
      avatar
    }
  }
}
```

### GraphQL Architecture

```text
             Frontend
                |
                v
           GraphQL API
                |
                v
             Schema
                |
                v
            Resolvers
                |
       +--------+--------+
       |        |        |
       v        v        v
     Users    Orders   Products
       |        |        |
       +--------+--------+
                |
                v
             Database
```

## 31. GraphQL Operation Types

### Query

Reads data.

```graphql
query {
  users {
    id
    name
  }
}
```

### Mutation

Changes data.

```graphql
mutation {
  createUser(
    name: "Riyaz"
    email: "riyaz@example.com"
  ) {
    id
    name
  }
}
```

### Subscription

Receives updates over a long-lived operation.

```graphql
subscription {
  orderUpdated {
    id
    status
  }
}
```

GraphQL subscriptions are commonly backed by a publish/subscribe mechanism. [GraphQL](https://graphql.org/learn/subscriptions/?utm_source=chatgpt.com)

## 32. GraphQL Schema

```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  users: [User!]!
  user(id: ID!): User
}

type Mutation {
  createUser(
    name: String!
    email: String!
  ): User!
}
```

### Important Symbols

| Symbol | Meaning |
|---|---|
| `!` | Non-null |
| `[User]` | List of users |
| `ID` | Identifier |
| `String` | String value |
| `Int` | Integer |
| `Boolean` | Boolean |

## 33. GraphQL Resolvers

A Resolver provides the implementation for a schema field.

```typescript
const resolvers = {
    Query: {
        users: async () => {
            return User.findAll();
        },

        user: async (_: unknown, args: { id: string }) => {
            return User.findByPk(args.id);
        }
    }
};
```

### GraphQL Challenges

- N+1 query problem.
- Query complexity.
- Authorization at field and object levels.
- Caching complexity.
- Schema evolution.
- Potentially expensive nested queries.

### What Is the N+1 Problem?

Suppose you fetch 100 users and then fetch orders for every user separately.

```text
1 query: Fetch users

100 queries: Fetch each user's orders
```

Total: 101 queries.

Solutions include:

- DataLoader.
- Batch loading.
- Eager loading.
- Query optimization.

### REST vs GraphQL

| REST | GraphQL |
|---|---|
| Resource-oriented endpoints | Schema and field-oriented queries |
| Multiple endpoints | Often one primary endpoint |
| Server defines response shape | Client selects response fields |
| HTTP caching can be straightforward | Requires deliberate caching strategy |
| Simpler for many CRUD APIs | Useful for complex client data requirements |

GraphQL is not automatically better than REST. Choose based on consumer needs, caching, security and operational complexity.

Official references: [GraphQL Schema](https://graphql.org/learn/schema/) and [GraphQL Queries](https://graphql.org/learn/queries/). [GraphQL](https://graphql.org/learn/schema/?utm_source=chatgpt.com)

---

# PART 20: gRPC

## 34. What Is gRPC?

gRPC is a high-performance Remote Procedure Call framework.

It allows one service to call methods exposed by another service.

It commonly uses:

- HTTP/2.
- Protocol Buffers.
- Generated client and server code.
- Streaming communication.

### Architecture

```text
          Order Service
                |
                v
             gRPC
                |
                v
          Payment Service
```

### REST vs gRPC

| REST | gRPC |
|---|---|
| Commonly JSON | Commonly Protocol Buffers |
| Often HTTP/1.1 or HTTP/2 | HTTP/2-based |
| Human-readable payloads | Compact binary payloads |
| Broad browser compatibility | Browser use often requires gRPC-Web or a gateway |
| Resource-oriented | RPC-oriented |
| Common for public APIs | Common for internal service communication |

## 35. Protocol Buffers

Protocol Buffers is a language-neutral mechanism for serializing structured data.

Example:

```protobuf
syntax = "proto3";

service UserService {
  rpc GetUser (UserRequest) returns (UserResponse);
}

message UserRequest {
  int32 id = 1;
}

message UserResponse {
  int32 id = 1;
  string name = 2;
  string email = 3;
}
```

The `.proto` file defines the service and message contracts.

### gRPC Communication Types

1. Unary RPC.
2. Server Streaming RPC.
3. Client Streaming RPC.
4. Bidirectional Streaming RPC.

### When to Use gRPC?

- Internal service-to-service communication.
- Strongly defined service contracts.
- High-throughput communication.
- Streaming requirements.
- Polyglot microservices.

Official reference: [gRPC Documentation](https://grpc.io/docs/). [gRPC](https://grpc.io/docs/?utm_source=chatgpt.com)

---

# PART 21: API GATEWAY ARCHITECTURE

## 36. What Is an API Gateway?

An API Gateway is an entry point that receives client requests and routes them to backend services.

```text
                Frontend
                    |
                    v
                API Gateway
                    |
       +------------+------------+
       |            |            |
       v            v            v
    User API     Order API    Product API
```

### Responsibilities

- Request routing.
- Authentication integration.
- Rate limiting.
- TLS termination.
- Request transformation.
- Logging.
- API version routing.
- Traffic management.

### Example

```text
GET /api/users/101
        |
        v
    API Gateway
        |
        v
   User Service
```

### API Gateway vs Reverse Proxy

| API Gateway | Reverse Proxy |
|---|---|
| API-specific capabilities | General server-side request forwarding |
| May provide authentication integration | May provide TLS termination |
| May provide API rate limits | May provide load balancing |
| May transform API requests | May forward or modify requests |

A reverse proxy can implement some gateway functions, but an API gateway is generally a broader API-management concept.

---

# PART 22: BACKEND FOR FRONTEND (BFF)

## 37. What Is Backend for Frontend?

Backend for Frontend is an architectural pattern in which different frontend clients have dedicated backend interfaces.

Example:

```text
             API Gateway
                  |
       +----------+----------+
       |          |          |
       v          v          v
    Web BFF   Mobile BFF   Admin BFF
       |          |          |
       +----------+----------+
                  |
          Backend Services
```

### Why Use BFF?

A mobile application and a web application may need different response structures.

For example:

Web application:

```json
{
  "user": {},
  "orders": [],
  "recommendations": []
}
```

Mobile application:

```json
{
  "userName": "Riyaz",
  "recentOrders": []
}
```

A BFF can aggregate and tailor data for each frontend.

### Advantages

- Frontend-specific optimization.
- Reduced unnecessary data transfer.
- Independent client evolution.
- Simplified frontend integration.

### Disadvantages

- More backend components.
- Potential duplicated logic.
- Additional deployment and monitoring.

---

# PART 23: AUTHENTICATION ARCHITECTURE

## 38. What Is Authentication?

Authentication verifies the identity of a user or system.

It answers:

**Who are you?**

Example:

A user logs in with:

```text
Email
Password
```

The backend validates credentials and establishes an authenticated session.

### Typical Authentication Flow

```text
             Client
                |
                v
          Login Request
                |
                v
           Auth Service
                |
                v
          Verify Password
                |
                v
         Create Session/Token
                |
                v
        Return Authentication
```

### Authentication Methods

- Password authentication.
- Session-based authentication.
- Token-based authentication.
- Multi-factor authentication.
- Passkeys.
- OAuth-based delegated login.

---

# PART 24: AUTHORIZATION ARCHITECTURE

## 39. What Is Authorization?

Authorization determines what an authenticated user is allowed to do.

It answers:

**What are you allowed to access?**

Example:

An authenticated user may be allowed to view their own expenses but not another employee's expenses.

### Authentication vs Authorization

| Authentication | Authorization |
|---|---|
| Verifies identity | Checks permissions |
| Happens before protected access | Evaluates access to resources/actions |
| Example: Login | Example: Admin-only endpoint |
| Who are you? | What can you do? |

### Example

```typescript
if (!user) {
    throw new Error("Authentication required");
}

if (user.role !== "admin") {
    throw new Error("Access denied");
}
```

Real authorization must also check ownership and resource-specific permissions, not just a broad role.

---

# PART 25: ROLE-BASED ACCESS CONTROL (RBAC)

## 40. What Is RBAC?

Role-Based Access Control assigns permissions to roles and assigns roles to users.

Example:

```text
Users
  |
  +---- Admin
  |
  +---- Manager
  |
  +---- Employee
```

Permissions:

| Role | Permissions |
|---|---|
| Admin | Manage all users and settings |
| Manager | Approve expenses |
| Employee | Submit and view own expenses |

### Database Design

```text
users
-----
id
name

roles
-----
id
name

permissions
-----------
id
name

user_roles
----------
user_id
role_id

role_permissions
----------------
role_id
permission_id
```

### Permission-Based Middleware

```typescript
function authorize(permission: string) {
    return (req: any, res: any, next: any) => {

        const permissions = req.user?.permissions ?? [];

        if (!permissions.includes(permission)) {
            return res.status(403).json({
                message: "Forbidden"
            });
        }

        next();
    };
}
```

Usage:

```typescript
app.delete(
    "/api/users/:id",
    authenticate,
    authorize("users.delete"),
    deleteUser
);
```

### Why Prefer Permissions Over Hardcoded Roles?

Suppose you add a new role:

```text
Finance Manager
```

If authorization depends only on hardcoded role names, changes become difficult.

A permission-based model is more flexible.

---

# PART 26: ATTRIBUTE-BASED ACCESS CONTROL (ABAC)

## 41. What Is ABAC?

Attribute-Based Access Control determines access using attributes of:

- User.
- Resource.
- Action.
- Environment.

Example:

An employee can approve an expense only if:

- The employee is a manager.
- The expense belongs to their department.
- The amount is below their approval limit.
- The expense is still pending.

### Example Rule

```text
ALLOW approval IF:

user.role = manager

AND

user.department = expense.department

AND

expense.amount <= user.approval_limit

AND

expense.status = pending
```

### RBAC vs ABAC

| RBAC | ABAC |
|---|---|
| Uses roles and permissions | Uses attributes and policies |
| Simpler | More flexible |
| Good for broad access rules | Good for contextual rules |
| Example: Admin can delete users | Example: Manager can approve department expenses under a limit |

ABAC can become complex. Use a policy engine or well-defined authorization service when rules become difficult to manage.

---

# PART 27: OAUTH 2.0

## 42. What Is OAuth 2.0?

OAuth 2.0 is an authorization framework that allows a client application to obtain limited access to a protected resource.

Example:

You use an application that wants permission to access selected information from another service.

OAuth allows access to be delegated without giving the client the user's password.

### Main Participants

| Component | Responsibility |
|---|---|
| Resource Owner | User who owns the protected data |
| Client | Application requesting access |
| Authorization Server | Issues tokens |
| Resource Server | Hosts protected resources |

### Authorization Code Flow

```text
                User
                 |
                 v
            Client App
                 |
                 v
        Authorization Server
                 |
                 v
          User Authentication
                 |
                 v
       Authorization Code
                 |
                 v
          Client Backend
                 |
                 v
            Token Exchange
                 |
                 v
            Access Token
                 |
                 v
          Resource Server
```

### Important OAuth Terms

**Access Token:** Used to access protected resources.

**Refresh Token:** Used to obtain new access tokens under the authorization server's rules.

**Scope:** Defines the access being requested.

Example:

```text
profile
email
orders.read
```

### Important Security Note

OAuth 2.0 is primarily an authorization framework. It does not by itself define a standardized user authentication layer.

For current OAuth security practice, study RFC 9700. It recommends protections such as PKCE for public clients and strict redirect URI validation. [IETF Datatracker](https://datatracker.ietf.org/doc/html/rfc9700/?utm_source=chatgpt.com)

---

# PART 28: OPENID CONNECT

## 43. What Is OpenID Connect?

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0.

It provides standardized user authentication information.

### OAuth vs OpenID Connect

| OAuth 2.0 | OpenID Connect |
|---|---|
| Authorization framework | Authentication and identity layer |
| Access tokens | ID tokens and access tokens |
| Grants access to resources | Provides standardized identity claims |
| Answers what access is allowed | Helps establish who authenticated |

### ID Token

An ID Token is a security token containing claims about the authenticated user.

Example payload:

```json
{
  "iss": "https://identity.example.com",
  "sub": "user-101",
  "aud": "client-123",
  "exp": 1795600000,
  "iat": 1795596400
}
```

A real implementation must validate the token's signature and claims.

---

# PART 29: JWT SECURITY

## 44. What Is JWT?

JWT stands for JSON Web Token.

It is a compact format for representing claims between parties.

A JWT commonly contains three parts:

```text
HEADER.PAYLOAD.SIGNATURE
```

### Header

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

```json
{
  "userId": 101,
  "role": "admin"
}
```

### Signature

The signature protects the token's integrity.

### JWT Structure

```text
             JWT
              |
     +--------+--------+
     |        |        |
     v        v        v
   Header   Payload  Signature
```

### Important Security Principles

1. Verify the signature.
2. Validate expiration.
3. Validate issuer.
4. Validate audience.
5. Use an explicit algorithm allowlist.
6. Avoid putting sensitive information in payloads.
7. Protect token storage.
8. Plan token revocation and rotation.
9. Use short-lived access tokens where appropriate.

**Important:** JWT payloads are generally encoded, not encrypted. Anyone possessing the token can usually decode its payload.

### JWT Authentication Middleware

```typescript
import jwt from "jsonwebtoken";

function authenticate(req: any, res: any, next: any) {
    const authorization = req.headers.authorization;

    if (!authorization?.startsWith("Bearer ")) {
        return res.status(401).json({
            message: "Authentication required"
        });
    }

    const token = authorization.substring(7);

    try {
        req.user = jwt.verify(
            token,
            process.env.JWT_SECRET!,
            {
                algorithms: ["HS256"],
                issuer: "my-api",
                audience: "my-client"
            }
        );

        next();

    } catch {
        return res.status(401).json({
            message: "Invalid or expired token"
        });
    }
}
```

This is an illustrative example. Production code must also handle configuration validation, key rotation and authorization.

---

# PART 30: SESSION MANAGEMENT

## 45. What Is Session-Based Authentication?

Session-Based Authentication stores session state on the server.

The client receives a session identifier, commonly through a cookie.

### Flow

```text
Client
  |
  v
Login
  |
  v
Backend
  |
  v
Create Session
  |
  v
Session Store
  |
  v
Set Session Cookie
  |
  v
Future Requests
  |
  v
Validate Session
```

### Example

```text
Browser Cookie:
session_id=abc123
```

The server uses this identifier to find the corresponding session.

### Where to Store Sessions?

- Redis.
- Database.
- Dedicated session store.

For distributed backend instances, a shared session store is generally preferable to in-memory sessions.

### Cookie Security

Important cookie attributes:

| Attribute | Purpose |
|---|---|
| HttpOnly | Prevents JavaScript access |
| Secure | Sends cookie only over secure connections |
| SameSite | Controls cross-site cookie behavior |
| Path | Limits URL path scope |
| Max-Age | Controls cookie lifetime |

---

# PART 31: REFRESH TOKEN ROTATION

## 46. What Is Refresh Token Rotation?

Refresh Token Rotation replaces a refresh token when it is used to obtain a new token pair.

Example:

```text
Refresh Token A
       |
       v
Token Refresh Request
       |
       v
Validate Token A
       |
       v
Issue New Tokens
       |
       +------> Access Token B
       |
       +------> Refresh Token B
```

The old refresh token becomes invalid.

### Why Use Rotation?

If an old refresh token is stolen and reused after rotation, the server may detect suspicious reuse.

### Recommended Controls

- Store refresh tokens securely.
- Rotate tokens.
- Track token families or sessions.
- Revoke compromised token families.
- Apply expiration.
- Protect token endpoints.
- Detect reuse.

For browser applications, consider whether a secure, HttpOnly cookie-based session architecture is more appropriate than exposing refresh tokens to JavaScript.

---

# PART 32: MULTI-TENANT ARCHITECTURE

## 47. What Is Multi-Tenancy?

Multi-Tenancy is an architecture in which one application serves multiple customers or organizations, called tenants.

Example:

A SaaS expense management platform serves:

```text
             Expense Platform
                    |
        +-----------+-----------+
        |           |           |
        v           v           v
     Company A   Company B   Company C
```

Each organization must access only its own data.

### Common Multi-Tenant Models

#### 1. Shared Database, Shared Schema

All tenants use the same tables.

```text
expenses
--------
id
tenant_id
user_id
amount
```

Every tenant-owned query must enforce tenant isolation.

#### 2. Shared Database, Separate Schema

Each tenant has a separate schema.

```text
tenant_a.expenses
tenant_b.expenses
tenant_c.expenses
```

#### 3. Separate Database Per Tenant

```text
Tenant A -> Database A

Tenant B -> Database B

Tenant C -> Database C
```

### Comparison

| Model | Isolation | Complexity | Cost |
|---|---|---|---|
| Shared schema | Logical | Lower | Usually lower |
| Separate schema | Stronger separation | Medium | Medium |
| Separate database | Strongest database-level separation | Higher | Higher |

### Critical Security Rule

Never trust a `tenant_id` supplied by the client without authorization.

Derive the tenant identity from the authenticated and authorized context.

Example:

```typescript
const tenantId = req.user.tenantId;

const expenses = await Expense.findAll({
    where: {
        tenantId
    }
});
```

The tenant filter must be enforced consistently across reads, writes, updates and deletes.

---

# PART 33: BACKEND CACHING

## 48. What Is Backend Caching?

Caching stores frequently accessed data in a faster storage layer to reduce repeated database work.

Example:

```text
             Client
                |
                v
             Backend
                |
                v
              Redis
             /     \
         Cache Hit  Cache Miss
            |          |
            v          v
         Response   MySQL
                       |
                       v
                  Store Cache
                       |
                       v
                    Response
```

## 49. Cache-Aside Pattern

The application checks the cache first.

If data is unavailable, it reads from the database and populates the cache.

```typescript
async function getProduct(id: number) {
    const key = `product:${id}`;

    const cached = await redis.get(key);

    if (cached) {
        return JSON.parse(cached);
    }

    const product = await Product.findByPk(id);

    if (!product) {
        return null;
    }

    await redis.set(
        key,
        JSON.stringify(product),
        "EX",
        300
    );

    return product;
}
```

### Cache Invalidation

When product information changes, invalidate the corresponding cache.

```typescript
await Product.update(data, {
    where: { id }
});

await redis.del(`product:${id}`);
```

### Common Caching Strategies

| Strategy | Meaning |
|---|---|
| Cache-Aside | Application checks and populates cache |
| Read-Through | Cache layer loads missing data |
| Write-Through | Writes update cache and backing store according to the cache design |
| Write-Behind | Cache accepts writes and persists them asynchronously |
| Refresh-Ahead | Refreshes entries before expiration |

### Cache Problems

- Cache stampede.
- Cache penetration.
- Cache avalanche.
- Stale data.
- Invalidation complexity.
- Hot keys.

### Cache Stampede

Many requests simultaneously attempt to load the same expired value.

Possible solutions:

- Request coalescing.
- Distributed locks where appropriate.
- Staggered expiration.
- Background refresh.
- Serving stale data when safe.

---

# PART 34: DISTRIBUTED RATE LIMITING

## 50. What Is Rate Limiting?

Rate Limiting controls how many requests a client can make during a period.

Example:

```text
100 requests per minute per user
```

If the limit is exceeded:

```http
HTTP/1.1 429 Too Many Requests
```

### Why Use Rate Limiting?

- Prevent abuse.
- Protect backend resources.
- Control API consumption.
- Reduce brute-force attempts.
- Improve service stability.

### Common Algorithms

#### 1. Fixed Window

Counts requests within fixed time intervals.

#### 2. Sliding Window

Calculates requests across a moving time range.

#### 3. Token Bucket

Tokens accumulate at a defined rate. Each request consumes a token.

#### 4. Leaky Bucket

Requests are processed at a controlled rate.

### Distributed Rate Limiting

If an application runs on multiple instances, rate limits must be coordinated.

```text
             Load Balancer
                    |
         +----------+----------+
         |                     |
         v                     v
     Server A              Server B
         |                     |
         +----------+----------+
                    |
                    v
                  Redis
                    |
                    v
            Shared Rate Limits
```

Otherwise, each server might enforce a separate limit and allow more requests than intended.

---

# PART 35: REQUEST VALIDATION

## 51. What Is Request Validation?

Request Validation checks whether incoming data meets the application's requirements.

Example:

A user registration request requires:

- Valid name.
- Valid email.
- Valid password.
- Required fields.
- Acceptable lengths.

### Example Using Zod

```typescript
import { z } from "zod";

const registerSchema = z.object({
    name: z.string().min(2).max(100),

    email: z.string().email(),

    password: z.string().min(12)
});
```

Validation:

```typescript
const result = registerSchema.safeParse(req.body);

if (!result.success) {
    return res.status(400).json({
        message: "Validation failed",
        errors: result.error.flatten()
    });
}

const data = result.data;
```

### Why Validate on the Backend?

Frontend validation improves user experience but cannot be trusted as a security boundary.

Attackers can call backend APIs directly.

### Validate

- Request body.
- Query parameters.
- Route parameters.
- Headers where relevant.
- Uploaded files.
- File types and sizes.
- Business rules.

---

# PART 36: ERROR HANDLING ARCHITECTURE

## 52. What Is Centralized Error Handling?

Centralized Error Handling provides a consistent mechanism for handling application errors.

Instead of writing separate error-response logic in every controller, errors are passed to a shared handler.

### Express Example

```typescript
class AppError extends Error {
    constructor(
        public statusCode: number,
        message: string
    ) {
        super(message);
    }
}
```

Controller:

```typescript
async function getUser(req: any, res: any, next: any) {
    try {
        const user = await User.findByPk(req.params.id);

        if (!user) {
            throw new AppError(404, "User not found");
        }

        res.json(user);

    } catch (error) {
        next(error);
    }
}
```

Central error middleware:

```typescript
import { ErrorRequestHandler } from "express";

const errorHandler: ErrorRequestHandler = (
    err,
    req,
    res,
    next
) => {
    const statusCode = err.statusCode || 500;

    res.status(statusCode).json({
        success: false,
        message:
            statusCode === 500
                ? "Internal server error"
                : err.message
    });
};
```

Register it after routes:

```typescript
app.use(errorHandler);
```

### Error Categories

| Error | Example |
|---|---|
| Validation | Invalid email |
| Authentication | Invalid token |
| Authorization | Insufficient permissions |
| Business | Insufficient stock |
| Database | Connection failure |
| External Service | Payment provider unavailable |
| Unexpected | Unhandled application error |

### Important Best Practices

- Never expose stack traces to clients in production.
- Log detailed errors internally.
- Use consistent error structures.
- Preserve request correlation IDs.
- Avoid swallowing errors.
- Distinguish expected business failures from unexpected infrastructure failures.

---

# PART 37: BACKGROUND JOB PROCESSING

## 53. What Is Background Job Processing?

Background Job Processing moves time-consuming tasks outside the main HTTP request-response lifecycle.

Example:

A user places an order.

The backend needs to:

- Save the order.
- Send a confirmation email.
- Generate an invoice.
- Notify the warehouse.
- Update analytics.

Not all these operations need to complete before responding to the user.

### Without Background Jobs

```text
Client
  |
  v
API Request
  |
  v
Create Order
  |
  v
Send Email
  |
  v
Generate Invoice
  |
  v
Update Analytics
  |
  v
Response
```

The user waits for everything.

### With Background Jobs

```text
Client
  |
  v
API Request
  |
  v
Create Order
  |
  v
Add Jobs to Queue
  |
  v
Return Response
```

Workers process the jobs separately.

```text
                 Queue
                   |
        +----------+----------+
        |                     |
        v                     v
     Worker 1              Worker 2
        |                     |
        v                     v
   Send Email             Generate Invoice
```

### Common Background Jobs

- Email delivery.
- PDF generation.
- Image processing.
- Video conversion.
- Report generation.
- Data imports.
- Notifications.
- Scheduled cleanup.

---

# PART 38: WORKER ARCHITECTURE

## 54. What Is a Worker?

A Worker is a process or execution unit that performs tasks outside the main request-handling path.

Example:

```text
                Node.js API
                    |
                    v
                   Redis
                    |
                    v
                  Queue
                    |
          +---------+---------+
          |                   |
          v                   v
      Email Worker       Report Worker
          |                   |
          v                   v
      Email API             PDF Engine
```

### API vs Worker

| API Server | Worker |
|---|---|
| Handles client requests | Handles background jobs |
| Focuses on request latency | Focuses on task completion |
| Often short-lived operations | Can process longer operations |
| Returns HTTP responses | Updates job state or emits events |

### Worker Best Practices

- Make jobs idempotent.
- Add retry policies.
- Use exponential backoff.
- Set job timeouts.
- Record job failures.
- Use dead-letter handling.
- Limit concurrency.
- Monitor queue length.

---

# PART 39: DISTRIBUTED TASK QUEUES

## 55. What Is a Task Queue?

A Task Queue stores jobs until workers are ready to process them.

Example using BullMQ and Redis:

```text
Node.js API
    |
    v
BullMQ Queue
    |
    v
Redis
    |
    v
BullMQ Worker
    |
    v
Process Job
```

### Installing BullMQ

```bash
npm install bullmq ioredis
```

### Create Queue

```typescript
import { Queue } from "bullmq";

const emailQueue = new Queue("email", {
    connection: {
        host: "127.0.0.1",
        port: 6379
    }
});
```

### Add Job

```typescript
await emailQueue.add("send-welcome-email", {
    userId: 101,
    email: "riyaz@example.com"
});
```

### Create Worker

```typescript
import { Worker } from "bullmq";

const worker = new Worker(
    "email",
    async (job) => {
        console.log("Processing:", job.data);

        // Send email.
    },
    {
        connection: {
            host: "127.0.0.1",
            port: 6379
        }
    }
);
```

### Why Use a Queue?

- Decouple API requests from background processing.
- Handle bursts of work.
- Retry failed jobs.
- Control concurrency.
- Scale workers independently.

**Important:** A queue does not guarantee exactly-once business effects. Workers must handle duplicate deliveries safely.

---

# PART 40: API PERFORMANCE OPTIMIZATION

## 56. What Is API Performance Optimization?

API Performance Optimization improves response time, throughput and resource utilization.

### Important Metrics

| Metric | Meaning |
|---|---|
| Latency | Time taken for a request |
| Throughput | Requests processed per unit of time |
| RPS | Requests per second |
| CPU Usage | Processor utilization |
| Memory Usage | Memory consumed |
| Error Rate | Failed request percentage |
| p50 | Median latency |
| p95 | 95th percentile latency |
| p99 | 99th percentile latency |

### Example

```text
p50 = 80 ms
p95 = 250 ms
p99 = 800 ms
```

This means 95% of measured requests complete within approximately 250 ms.

### Optimization Techniques

1. Database query optimization.
2. Proper indexing.
3. Caching.
4. Pagination.
5. Connection pooling.
6. Avoiding unnecessary database queries.
7. Efficient serialization.
8. Compression.
9. Asynchronous I/O.
10. Background jobs.
11. Horizontal scaling.

### N+1 Query Problem

Bad approach:

```typescript
const users = await User.findAll();

for (const user of users) {
    user.orders = await Order.findAll({
        where: {
            userId: user.id
        }
    });
}
```

This can execute one user query plus one query per user.

Better approach:

```typescript
const users = await User.findAll({
    include: [
        {
            model: Order
        }
    ]
});
```

This is one common eager-loading approach. Always inspect the actual SQL and query plan because a JOIN can also become expensive.

---

# PART 41: DATABASE ACCESS OPTIMIZATION

## 57. Common Database Optimization Techniques

### 1. Use Appropriate Indexes

```sql
CREATE INDEX idx_orders_user_id
ON orders(user_id);
```

### 2. Select Only Required Columns

Avoid:

```sql
SELECT *
FROM users;
```

Prefer:

```sql
SELECT id, name, email
FROM users;
```

### 3. Use Pagination

```sql
SELECT id, name
FROM users
ORDER BY id
LIMIT 50;
```

### 4. Avoid N+1 Queries

Use appropriate joins, batching or eager loading.

### 5. Use Connection Pooling

Avoid creating a new database connection for every request.

### 6. Inspect Query Plans

For MySQL:

```sql
EXPLAIN
SELECT *
FROM orders
WHERE user_id = 101;
```

For supported MySQL versions, `EXPLAIN ANALYZE` can execute a query and show actual execution details.

### 7. Use Transactions Carefully

Keep transactions as short as practical.

Long-running transactions can increase locking and resource pressure.

---

# PART 42: MEMORY MANAGEMENT

## 58. How Does Node.js Manage Memory?

Node.js runs JavaScript using the V8 engine.

V8 manages memory through areas including:

- Heap.
- Stack.
- Garbage-collected memory regions.

### Heap Memory

Stores dynamically allocated JavaScript objects.

Example:

```typescript
const users = [];

for (let i = 0; i < 100000; i++) {
    users.push({
        id: i,
        name: `User ${i}`
    });
}
```

This allocates many objects.

### Stack Memory

Stores execution-related information such as function calls and local execution state.

### Garbage Collection

Garbage Collection reclaims memory occupied by objects that are no longer reachable.

Example:

```typescript
let user: any = {
    name: "Riyaz"
};

user = null;
```

If no other references point to the original object, it becomes eligible for garbage collection.

### Memory Leak

A Memory Leak occurs when objects remain reachable even though the application no longer needs them.

Example:

```typescript
const cache = new Map();

function addUser(user: any) {
    cache.set(user.id, user);
}
```

If entries are never removed, the map can grow indefinitely.

### Common Causes

- Global references.
- Unbounded caches.
- Unremoved event listeners.
- Long-lived closures.
- Large in-memory collections.
- Unreleased resources.

### Monitoring Memory

```typescript
console.log(process.memoryUsage());
```

Important fields include:

- `rss`
- `heapTotal`
- `heapUsed`
- `external`
- `arrayBuffers`

---

# PART 43: NODE.JS EVENT LOOP PERFORMANCE

## 59. What Is the Event Loop?

The Event Loop allows Node.js to coordinate asynchronous operations.

It enables Node.js to handle many I/O operations without requiring a separate JavaScript thread for every request.

### Simplified Flow

```text
             JavaScript
                 |
                 v
             Call Stack
                 |
                 v
             Event Loop
                 |
        +--------+--------+
        |                 |
        v                 v
    Async I/O          Timers
        |                 |
        +--------+--------+
                 |
                 v
              Callbacks
```

### Important Event Loop Phases

- Timers.
- Pending callbacks.
- Idle and prepare.
- Poll.
- Check.
- Close callbacks.

Node.js 20 and later use the updated libuv timer behavior introduced with libuv 1.45.0. Avoid relying on simplistic assumptions about exact ordering between timers and `setImmediate()`. [nodejs.org](https://nodejs.org/learn/asynchronous-work/event-loop-timers-and-nexttick?utm_source=chatgpt.com)

## 60. What Blocks the Event Loop?

CPU-intensive synchronous operations can block the event loop.

Example:

```typescript
app.get("/report", (req, res) => {
    let total = 0;

    for (let i = 0; i < 1000000000; i++) {
        total += i;
    }

    res.json({ total });
});
```

While this loop runs, the JavaScript thread cannot process other JavaScript callbacks normally.

### Better Approaches

- Move CPU-intensive work to worker threads.
- Use background workers.
- Use specialized processing services.
- Break large tasks into manageable chunks.
- Avoid synchronous file and CPU-heavy operations in request handlers.

### Worker Threads

Worker Threads execute JavaScript in separate threads and are useful for CPU-intensive work.

They are not normally needed for routine asynchronous network or database I/O.

Official reference: [Node.js Worker Threads](https://nodejs.org/api/worker_threads.html).

---

# PART 44: HORIZONTAL BACKEND SCALING

## 61. What Is Horizontal Scaling?

Horizontal Scaling increases capacity by running multiple application instances.

Example:

```text
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
                   Database
```

### Why Scale Horizontally?

- More concurrent requests.
- Better availability.
- Independent instance replacement.
- Capacity expansion.

### Stateless Backend

A Stateless Backend does not rely on local instance memory to preserve essential user session state between requests.

Shared state may be stored in:

- Redis.
- Database.
- External object storage.
- Dedicated services.

This makes load balancing and horizontal scaling easier.

### Stateful vs Stateless

| Stateful | Stateless |
|---|---|
| Depends on local session or process state | Request can be handled by any suitable instance |
| Harder to scale horizontally | Easier to scale horizontally |
| May require sticky sessions | Often works without sticky sessions |
| Example: Local in-memory sessions | Example: Shared session store |

---

# PART 45: GRACEFUL SHUTDOWN

## 62. What Is Graceful Shutdown?

Graceful Shutdown is the process of stopping an application without abruptly terminating active work.

Example:

A deployment replaces an old Node.js instance with a new version.

The old instance should:

1. Stop accepting new requests.
2. Allow in-flight requests to complete.
3. Close database connections.
4. Stop workers safely.
5. Close Redis connections.
6. Exit.

### Lifecycle

```text
         Shutdown Signal
                |
                v
        Stop Accepting Requests
                |
                v
       Complete In-Flight Requests
                |
                v
        Stop Background Workers
                |
                v
        Close DB and Redis
                |
                v
              Exit
```

### Express Example

```typescript
import http from "node:http";

const server = http.createServer(app);

server.listen(3000);

async function shutdown(signal: string) {
    console.log(`${signal} received`);

    server.close(async (error) => {
        if (error) {
            console.error(error);
            process.exitCode = 1;
        }

        try {
            await sequelize.close();

            await redis.quit();

        } catch (err) {
            console.error("Shutdown error:", err);
            process.exitCode = 1;
        }
    });
}

process.on("SIGTERM", () => shutdown("SIGTERM"));

process.on("SIGINT", () => shutdown("SIGINT"));
```

This is a simplified example. Production implementations should include:

- A shutdown timeout.
- Idempotent shutdown handling.
- Worker draining.
- WebSocket connection handling.
- Forced termination after a bounded grace period.

---

# PART 46: HEALTH CHECKS

## 63. What Is a Health Check?

A Health Check determines whether an application or one of its dependencies is functioning.

### Liveness Check

Answers:

**Is the process alive?**

Example:

```http
GET /health/live
```

### Readiness Check

Answers:

**Can the application currently accept traffic?**

Example:

```http
GET /health/ready
```

A readiness check might verify that required dependencies are available.

### Example

```typescript
app.get("/health/live", (req, res) => {
    res.status(200).json({
        status: "alive"
    });
});
```

Readiness:

```typescript
app.get("/health/ready", async (req, res) => {
    try {
        await sequelize.authenticate();

        res.status(200).json({
            status: "ready"
        });

    } catch {
        res.status(503).json({
            status: "not-ready"
        });
    }
});
```

### Liveness vs Readiness

| Liveness | Readiness |
|---|---|
| Checks process health | Checks ability to serve traffic |
| Failed check may trigger restart | Failed check removes instance from traffic |
| Should be lightweight | May check required dependencies |

Avoid making liveness depend on every external service. A temporary database outage should not necessarily cause every application instance to restart continuously.

---

# PART 47: PRODUCTION LOGGING

## 64. What Is Structured Logging?

Structured Logging records logs in a consistent, machine-readable format.

Instead of:

```text
User created successfully
```

Use:

```json
{
  "level": "info",
  "message": "User created successfully",
  "userId": 101,
  "requestId": "req-123",
  "timestamp": "2026-11-25T18:00:00Z"
}
```

### Why Use Structured Logs?

- Easy searching.
- Better filtering.
- Correlation.
- Automated analysis.
- Incident investigation.

### Log Levels

| Level | Meaning |
|---|---|
| fatal | Application cannot continue |
| error | Operation failed |
| warn | Potential problem |
| info | Important operational event |
| debug | Diagnostic information |
| trace | Very detailed execution information |

### Example Using Pino

```bash
npm install pino pino-http
```

```typescript
import pino from "pino";

const logger = pino();

logger.info({
    userId: 101,
    action: "user_created"
}, "User created");
```

### Never Log

- Passwords.
- Access tokens.
- Refresh tokens.
- Database credentials.
- Full payment card details.
- Unnecessary sensitive personal data.

---

# PART 48: API MONITORING AND OBSERVABILITY

## 65. What Is Observability?

Observability is the ability to understand a system's internal behavior through its outputs.

Three important pillars:

1. Logs.
2. Metrics.
3. Traces.

### Logs

Describe individual events.

Example:

```text
Payment request failed.
```

### Metrics

Measure system behavior over time.

Examples:

- Request count.
- Error rate.
- CPU usage.
- Memory usage.
- Response latency.

### Traces

Show how a request travels through multiple services.

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

### Distributed Tracing

A Trace represents the journey of an operation across components.

A Span represents one operation within that trace.

Example:

```text
Trace: Create Order

Span 1: API Gateway       10 ms
Span 2: Order Service     30 ms
Span 3: Payment Service  150 ms
Span 4: Database          20 ms
```

This helps identify where latency originates.

### Common Tools

| Tool | Purpose |
|---|---|
| Prometheus | Metrics collection |
| Grafana | Dashboards |
| OpenTelemetry | Instrumentation and telemetry |
| Jaeger | Distributed tracing |
| Loki | Log aggregation |
| Pino | Node.js structured logging |

---

# PART 49: BACKEND SECURITY

## 66. What Is Backend Security?

Backend Security protects APIs, application logic, data and infrastructure from unauthorized access and abuse.

### Important Security Areas

#### 1. Authentication Security

- Secure password hashing.
- MFA where appropriate.
- Secure token handling.
- Session expiration.
- Brute-force protection.

#### 2. Authorization Security

- Object-level authorization.
- Role and permission checks.
- Tenant isolation.
- Ownership validation.

#### 3. Input Security

- Request validation.
- SQL injection prevention.
- NoSQL injection prevention.
- File upload validation.
- Output encoding where relevant.

#### 4. Network Security

- HTTPS.
- TLS.
- Firewall rules.
- Private database networking.
- Secure service-to-service communication.

#### 5. Secret Management

Do not hardcode:

```typescript
const password = "my-database-password";
```

Use environment variables or a secrets manager:

```typescript
const password = process.env.DB_PASSWORD;
```

In production, use a managed secret store and rotation procedures where appropriate.

## 67. OWASP API Security

The OWASP API Security Top 10:2023 includes risks such as:

- Broken Object Level Authorization.
- Broken Authentication.
- Broken Object Property Level Authorization.
- Unrestricted Resource Consumption.
- Broken Function Level Authorization.
- Unrestricted Access to Sensitive Business Flows.
- Server-Side Request Forgery.
- Security Misconfiguration.
- Improper Inventory Management.
- Unsafe Consumption of APIs.

For API development, pay particular attention to object-level authorization.

Example:

A user changes:

```http
GET /api/orders/101
```

to:

```http
GET /api/orders/102
```

The backend must verify that the authenticated user is authorized to access order 102.

Knowing an ID must never be sufficient authorization.

The OWASP API Security project provides detailed guidance on these risks. [OWASP API Security Top 10](https://api-security.owasp.org/?utm_source=chatgpt.com)

---

# PART 50: LOAD TESTING

## 68. What Is Load Testing?

Load Testing measures application behavior under expected traffic.

Example:

Suppose an API normally handles 100 requests per second.

You want to understand how it behaves at:

- 100 RPS.
- 500 RPS.
- 1,000 RPS.
- 5,000 RPS.

### Types of Performance Testing

| Type | Purpose |
|---|---|
| Load Testing | Test expected workload |
| Stress Testing | Test beyond expected capacity |
| Spike Testing | Test sudden traffic increases |
| Endurance Testing | Test long-running workload |
| Volume Testing | Test large amounts of data |
| Scalability Testing | Measure behavior as resources increase |

### Example Using k6

Install k6 and create:

```javascript
import http from "k6/http";
import { check } from "k6";

export const options = {
    vus: 50,
    duration: "30s"
};

export default function () {
    const response = http.get(
        "http://localhost:3000/api/products"
    );

    check(response, {
        "status is 200": (r) => r.status === 200
    });
}
```

Run:

```bash
k6 run load-test.js
```

### Important Metrics

- Requests per second.
- Average response time.
- p95 latency.
- p99 latency.
- Error rate.
- CPU usage.
- Memory usage.
- Database connections.
- Queue length.

### Important Rule

Do not optimize only for average response time.

A system can have a good average but very poor p99 latency.

---

# PART 51: PRODUCTION DEPLOYMENT

## 69. What Is Production Deployment?

Production Deployment is the process of releasing an application into an environment used by real customers.

A production backend should include:

- Environment configuration.
- Secure secrets.
- Process management.
- Reverse proxy.
- HTTPS.
- Database connectivity.
- Logging.
- Monitoring.
- Backups.
- Deployment automation.
- Health checks.

### Example Production Architecture

```text
                    Internet
                       |
                       v
                    Cloud DNS
                       |
                       v
                     Nginx
                       |
                       v
                  Load Balancer
                       |
           +-----------+-----------+
           |           |           |
           v           v           v
        Node.js     Node.js     Node.js
        Server 1    Server 2    Server 3
           |           |           |
           +-----------+-----------+
                       |
          +------------+------------+
          |            |            |
          v            v            v
        MySQL        Redis       Queue
          |                         |
          v                         v
      Database                  Workers
```

### Deployment Checklist

1. Build the application.
2. Validate environment variables.
3. Run automated tests.
4. Apply compatible database migrations.
5. Deploy application instances.
6. Check health endpoints.
7. Verify logs and metrics.
8. Monitor error rates.
9. Keep a rollback plan.

### Blue-Green Deployment

Two environments are maintained:

- Blue: Current production.
- Green: New version.

Traffic switches after the new version is validated.

### Rolling Deployment

Instances are updated gradually.

### Canary Deployment

A small portion of traffic is sent to the new version before broader rollout.

### Deployment Comparison

| Strategy | Main Benefit |
|---|---|
| Rolling | Gradual replacement |
| Blue-Green | Fast traffic switching and rollback |
| Canary | Gradual exposure and risk reduction |

---

# PART 52: COMPLETE ENTERPRISE NODE.JS BACKEND PROJECT

# Project: Enterprise Expense Management Platform

We will design an enterprise-grade backend for an expense management application.

This project combines architecture, security, API development, performance and production deployment.

## 70. Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + React |
| Backend | Node.js + Express |
| Language | TypeScript |
| Database | MySQL |
| ORM | Sequelize or Prisma |
| Cache | Redis |
| Background Queue | BullMQ |
| Real-Time | Socket.IO |
| Authentication | Session or JWT/OIDC architecture |
| API Documentation | OpenAPI |
| Validation | Zod |
| Logging | Pino |
| Monitoring | Prometheus + Grafana |
| Testing | Vitest/Jest + Supertest |
| Load Testing | k6 |
| Deployment | Docker + Nginx |
| CI/CD | GitHub Actions |
| Hosting | AWS EC2 or VPS |

## 71. Business Requirements

The platform must support:

1. User registration.
2. User login.
3. Organization creation.
4. Multiple organizations.
5. Employee management.
6. Expense submission.
7. Expense categories.
8. Expense approval.
9. Manager approval.
10. Expense rejection.
11. Reimbursement tracking.
12. PDF report generation.
13. Email notifications.
14. Real-time notifications.
15. Audit logs.
16. Role-based permissions.
17. Organization-level data isolation.
18. Reporting dashboards.

## 72. High-Level Architecture

```text
                  Next.js Frontend
                         |
                         v
                     Nginx
                         |
                         v
                    Express API
                         |
        +----------------+----------------+
        |                |                |
        v                v                v
    Auth Module     Expense Module    Approval Module
        |                |                |
        +----------------+----------------+
                         |
                  Application Layer
                         |
                    Domain Layer
                         |
               Infrastructure Layer
                         |
         +---------------+---------------+
         |               |               |
         v               v               v
       MySQL           Redis         External APIs
                         |
                         v
                     BullMQ Queue
                         |
                         v
                    Worker Process
                         |
              +----------+----------+
              |                     |
              v                     v
         Email Worker           PDF Worker
```

## 73. Suggested Folder Structure

```text
expense-backend/
│
├── src/
│   │
│   ├── modules/
│   │   │
│   │   ├── auth/
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── auth.repository.ts
│   │   │   └── auth.routes.ts
│   │   │
│   │   ├── users/
│   │   ├── organizations/
│   │   ├── expenses/
│   │   ├── approvals/
│   │   ├── reimbursements/
│   │   └── notifications/
│   │
│   ├── application/
│   │   └── use-cases/
│   │
│   ├── domain/
│   │   ├── entities/
│   │   ├── value-objects/
│   │   └── repositories/
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   ├── redis/
│   │   ├── queues/
│   │   └── external-services/
│   │
│   ├── middleware/
│   ├── config/
│   ├── shared/
│   ├── workers/
│   ├── app.ts
│   └── server.ts
│
├── tests/
├── migrations/
├── docs/
├── Dockerfile
├── package.json
└── tsconfig.json
```

## 74. Example API Design

### Authentication

```text
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/logout
POST /api/v1/auth/refresh
GET  /api/v1/auth/me
```

### Users

```text
GET    /api/v1/users
GET    /api/v1/users/:id
POST   /api/v1/users
PATCH  /api/v1/users/:id
DELETE /api/v1/users/:id
```

### Expenses

```text
GET    /api/v1/expenses
GET    /api/v1/expenses/:id
POST   /api/v1/expenses
PATCH  /api/v1/expenses/:id
DELETE /api/v1/expenses/:id
```

### Approvals

```text
GET  /api/v1/approvals/pending
POST /api/v1/expenses/:id/approve
POST /api/v1/expenses/:id/reject
```

### Reports

```text
GET /api/v1/reports/monthly
GET /api/v1/reports/by-category
GET /api/v1/reports/by-employee
```

## 75. Suggested Database Schema

```text
organizations
-------------
id
name
created_at

users
-----
id
name
email
password_hash
created_at

organization_members
-------------------
id
organization_id
user_id
role_id

roles
-----
id
name

permissions
-----------
id
name

role_permissions
----------------
role_id
permission_id

expenses
--------
id
organization_id
user_id
category_id
amount
description
status
created_at

expense_categories
------------------
id
organization_id
name

expense_approvals
-----------------
id
expense_id
approver_id
status
comments
approved_at

reimbursements
--------------
id
expense_id
amount
status
paid_at

audit_logs
----------
id
organization_id
user_id
action
entity_type
entity_id
created_at
```

## 76. Example Expense Workflow

```text
Employee
   |
   v
Submit Expense
   |
   v
Expense Created
   |
   v
Pending Approval
   |
   v
Manager Reviews
   |
   +-------------------+
   |                   |
   v                   v
 Approved            Rejected
   |
   v
Reimbursement Pending
   |
   v
Payment Processed
   |
   v
Completed
```

### Important Business Rules

- Employees can view their own expenses.
- Managers can review authorized department expenses.
- Only authorized users can approve expenses.
- An already approved expense cannot be approved again.
- An expense cannot be reimbursed twice.
- Every approval or rejection must be auditable.
- Every operation must enforce organization-level access.

## 77. Background Processing

When an expense is approved:

```text
Approval API
     |
     v
Update Expense Status
     |
     v
Create Notification Job
     |
     v
BullMQ
     |
     v
Worker
     |
     +---------> Send Email
     |
     +---------> Send In-App Notification
     |
     +---------> Generate Report
```

For reliability, the database update and event publication should be designed so that a committed approval cannot silently lose its notification event. A Transactional Outbox is one common solution.

## 78. Production Architecture

```text
                 Internet
                    |
                    v
                   Nginx
                    |
                    v
               Load Balancer
                    |
          +---------+---------+
          |                   |
          v                   v
      API Server 1        API Server 2
          |                   |
          +---------+---------+
                    |
         +----------+----------+
         |          |          |
         v          v          v
       MySQL      Redis      Queue
                               |
                               v
                            Workers
                               |
                   +-----------+-----------+
                   |                       |
                   v                       v
                Email                   Reports
```

### Project Completion Criteria

You should be able to:

- Design the architecture.
- Create the database schema.
- Implement authentication.
- Implement authorization.
- Implement RBAC.
- Implement tenant isolation.
- Develop REST APIs.
- Document APIs with OpenAPI.
- Implement centralized error handling.
- Add caching.
- Implement background workers.
- Add structured logging.
- Implement health checks.
- Perform load testing.
- Deploy using Docker and Nginx.
- Monitor production performance.

---

# PART 53: INTERVIEW QUESTIONS

## Beginner-Level

**Q1. What is backend architecture?**  
The organization of server-side components, responsibilities and communication.

**Q2. What is a monolithic application?**  
An application deployed as one application unit.

**Q3. What is a modular monolith?**  
A single deployable application with clearly separated modules.

**Q4. What are microservices?**  
Independently deployable services organized around business capabilities.

**Q5. What is REST?**  
An architectural style for networked applications.

**Q6. What is API versioning?**  
A strategy for evolving API contracts without unexpectedly breaking consumers.

**Q7. What is OpenAPI?**  
A standard specification for describing HTTP APIs.

**Q8. What is GraphQL?**  
A query language and runtime for APIs.

**Q9. What is gRPC?**  
A Remote Procedure Call framework.

**Q10. What is API Gateway?**  
An entry point that routes and manages API requests.

## Intermediate-Level

**Q11. What is Clean Architecture?**  
An approach that isolates business rules from infrastructure.

**Q12. What is Hexagonal Architecture?**  
An architecture that separates core logic from external systems using ports and adapters.

**Q13. What is DDD?**  
Domain-Driven Design, which models software around business concepts.

**Q14. What is a bounded context?**  
A boundary within which a domain model and its terminology have a defined meaning.

**Q15. What is a repository pattern?**  
An abstraction for data access.

**Q16. What is dependency injection?**  
Providing dependencies to an object from outside.

**Q17. What is inversion of control?**  
A principle in which control is delegated to an external mechanism.

**Q18. What is RBAC?**  
Role-Based Access Control.

**Q19. What is ABAC?**  
Attribute-Based Access Control.

**Q20. What is BFF?**  
Backend for Frontend.

## Advanced-Level

**Q21. What is an aggregate root?**  
The entity through which an aggregate is accessed and modified.

**Q22. What is a value object?**  
An object defined by its values rather than its identity.

**Q23. What is the Unit of Work pattern?**  
A pattern for coordinating related persistence operations.

**Q24. What is OAuth 2.0?**  
An authorization framework for delegated access.

**Q25. What is OpenID Connect?**  
An identity layer built on OAuth 2.0.

**Q26. What is the difference between OAuth and OIDC?**  
OAuth focuses on authorization; OIDC adds standardized authentication and identity claims.

**Q27. What is JWT?**  
A compact format for representing claims.

**Q28. What is refresh token rotation?**  
Replacing refresh tokens during renewal and invalidating previous tokens.

**Q29. What is multi-tenancy?**  
Serving multiple customers through a shared application architecture.

**Q30. What is distributed rate limiting?**  
Coordinating request limits across multiple application instances.

## Expert-Level

**Q31. What is a cache stampede?**  
A situation where many requests simultaneously regenerate an expired cache entry.

**Q32. What is the N+1 query problem?**  
An inefficient pattern where additional queries are executed for each returned record.

**Q33. What is graceful shutdown?**  
Stopping an application while safely draining ongoing work and closing resources.

**Q34. What is the difference between liveness and readiness?**  
Liveness checks whether a process is alive; readiness checks whether it can accept traffic.

**Q35. What is distributed tracing?**  
Tracking a request across multiple services using traces and spans.

**Q36. What is the event loop?**  
The mechanism that coordinates JavaScript execution and asynchronous callbacks in Node.js.

**Q37. What is a memory leak?**  
Unintended retention of memory that is no longer needed.

**Q38. What is horizontal scaling?**  
Increasing capacity by adding application instances.

**Q39. What is load testing?**  
Measuring system behavior under a specified workload.

**Q40. What is a canary deployment?**  
Gradually exposing a new version to a limited portion of traffic.

## Real-World Architecture Questions

**Q41. How would you structure a large Express application?**

Use modular boundaries, controllers, application services, domain logic, repositories and infrastructure components.

**Q42. When would you choose a modular monolith instead of microservices?**

When the application needs clear internal boundaries but does not yet require independent deployment or scaling.

**Q43. How would you prevent unauthorized access to another user's order?**

Perform object-level authorization on the backend using the authenticated user's identity and ownership or permission rules.

**Q44. How would you scale a Node.js backend?**

Use stateless instances, load balancing, shared state, caching, connection pooling and background workers.

**Q45. How would you handle long-running tasks?**

Move them to a durable background queue and process them using workers.

**Q46. How would you reduce API response time?**

Profile the request, optimize database queries, use caching, reduce payload sizes and remove unnecessary work.

**Q47. How would you prevent duplicate payment processing?**

Use idempotency keys, payment state machines, database constraints and reconciliation.

**Q48. How would you manage sessions across multiple backend instances?**

Use a shared session store such as Redis and secure session cookies.

**Q49. How would you deploy a backend without interrupting users?**

Use rolling, blue-green or canary deployment with readiness checks and graceful shutdown.

**Q50. How would you design a production-grade backend?**

Start with requirements, define boundaries, design APIs and data models, implement security, add observability, test under load and deploy with reliable infrastructure.

---

# PART 54: HANDS-ON PRACTICAL ASSIGNMENTS

## Level 1 – Backend Architecture

| No. | Assignment | Expected Result |
|---:|---|---|
| 1 | Build a basic monolithic API | Working Express application |
| 2 | Convert it into a modular monolith | Modular structure |
| 3 | Create separate user and product modules | Module boundaries |
| 4 | Implement Clean Architecture | Layered architecture |
| 5 | Implement Hexagonal Architecture | Ports and adapters |
| 6 | Create a domain model | Business entities |
| 7 | Implement bounded contexts | Domain boundaries |
| 8 | Create value objects | Domain validation |
| 9 | Implement aggregate roots | Business invariants |
| 10 | Implement repository pattern | Data abstraction |

## Level 2 – Enterprise Design Patterns

| No. | Assignment | Expected Result |
|---:|---|---|
| 11 | Implement Unit of Work | Transaction coordination |
| 12 | Implement dependency injection | Loose coupling |
| 13 | Build a testable service | Mock dependencies |
| 14 | Separate domain and infrastructure | Architecture boundaries |
| 15 | Implement domain events | Event-based business logic |
| 16 | Design service boundaries | Service architecture |
| 17 | Create an API gateway | Request routing |
| 18 | Implement BFF | Frontend-specific backend |
| 19 | Design a microservices example | Distributed architecture |
| 20 | Compare monolith and microservices | Architecture decision document |

## Level 3 – API Engineering

| No. | Assignment | Expected Result |
|---:|---|---|
| 21 | Build REST CRUD APIs | Resource endpoints |
| 22 | Implement pagination | Paginated results |
| 23 | Implement filtering and sorting | Query controls |
| 24 | Implement API versioning | Versioned APIs |
| 25 | Generate OpenAPI documentation | API specification |
| 26 | Build a GraphQL API | Schema and resolvers |
| 27 | Solve GraphQL N+1 queries | DataLoader/batching |
| 28 | Build a gRPC service | RPC communication |
| 29 | Implement request validation | Zod schemas |
| 30 | Implement centralized errors | Error middleware |

## Level 4 – Security

| No. | Assignment | Expected Result |
|---:|---|---|
| 31 | Implement authentication | Secure login |
| 32 | Implement RBAC | Role permissions |
| 33 | Implement ABAC | Attribute-based policies |
| 34 | Implement OAuth login | Authorization flow |
| 35 | Implement OIDC integration | Identity authentication |
| 36 | Implement JWT verification | Token validation |
| 37 | Implement refresh token rotation | Secure renewal |
| 38 | Implement session management | Redis-backed sessions |
| 39 | Build a multi-tenant API | Tenant isolation |
| 40 | Perform an OWASP API security review | Security assessment |

## Level 5 – Performance and Production

| No. | Assignment | Expected Result |
|---:|---|---|
| 41 | Implement Redis caching | Cache-aside pattern |
| 42 | Implement distributed rate limiting | Shared rate limits |
| 43 | Build a background queue | BullMQ |
| 44 | Build separate worker processes | Worker architecture |
| 45 | Fix an N+1 query problem | Optimized database access |
| 46 | Implement graceful shutdown | Safe process termination |
| 47 | Add liveness and readiness endpoints | Health monitoring |
| 48 | Add structured logging | Production logs |
| 49 | Run k6 load tests | Performance report |
| 50 | Deploy an enterprise backend | Production deployment |

---

# PART 55: QUICK REVISION NOTES

| Concept | Meaning |
|---|---|
| Backend Architecture | Organization of backend components |
| Monolith | Single deployable application |
| Modular Monolith | One deployment with clear module boundaries |
| Microservices | Independently deployable services |
| Clean Architecture | Separation of business rules and infrastructure |
| Hexagonal Architecture | Ports and adapters |
| DDD | Domain-Driven Design |
| Domain | Business problem area |
| Subdomain | Smaller business area |
| Bounded Context | Boundary of a domain model |
| Entity | Object with identity |
| Value Object | Object defined by values |
| Aggregate | Domain consistency boundary |
| Aggregate Root | Main entry point to an aggregate |
| Repository | Data-access abstraction |
| Unit of Work | Coordinates persistence operations |
| Dependency Injection | Supplies dependencies externally |
| IoC | Inversion of Control |
| REST | Resource-oriented API architectural style |
| API Versioning | Managing API contract evolution |
| OpenAPI | HTTP API specification |
| GraphQL | API query language and runtime |
| gRPC | Remote Procedure Call framework |
| Protocol Buffers | Structured data serialization |
| API Gateway | API entry point and management layer |
| BFF | Backend for Frontend |
| Authentication | Identity verification |
| Authorization | Permission checking |
| RBAC | Role-Based Access Control |
| ABAC | Attribute-Based Access Control |
| OAuth 2.0 | Authorization framework |
| OIDC | Authentication and identity layer |
| JWT | Token format |
| Session | Server-managed authentication state |
| Refresh Token Rotation | Replacing refresh tokens |
| Multi-Tenancy | Multiple customers sharing an application |
| Caching | Storing frequently used data |
| Cache-Aside | Application-managed cache lookup |
| Rate Limiting | Restricting request frequency |
| Request Validation | Checking incoming data |
| Centralized Error Handling | Shared error processing |
| Background Job | Work outside the main request path |
| Worker | Process that executes jobs |
| Task Queue | Stores jobs for workers |
| Latency | Time required to complete an operation |
| Throughput | Work completed per unit of time |
| Memory Leak | Unintended memory retention |
| Event Loop | Coordinates JavaScript asynchronous execution |
| Horizontal Scaling | Adding more instances |
| Graceful Shutdown | Safely stopping an application |
| Liveness | Whether the process is alive |
| Readiness | Whether the process can accept traffic |
| Structured Logging | Machine-readable log records |
| Observability | Understanding internal system behavior |
| Distributed Tracing | Tracking requests across services |
| Load Testing | Measuring performance under workload |
| Canary Deployment | Gradually releasing a new version |

---

# PART 56: PHASE 68 COMPLETION CHECKLIST

### Backend Architecture

- [ ] Understand monolithic architecture.
- [ ] Understand modular monolith.
- [ ] Understand microservices.
- [ ] Understand Clean Architecture.
- [ ] Understand Hexagonal Architecture.
- [ ] Understand DDD.
- [ ] Understand bounded contexts.
- [ ] Understand entities and value objects.
- [ ] Understand aggregates.
- [ ] Implement repository pattern.
- [ ] Implement Unit of Work.
- [ ] Understand dependency injection.
- [ ] Understand inversion of control.

### API Engineering

- [ ] Understand REST.
- [ ] Design RESTful resources.
- [ ] Understand HTTP methods.
- [ ] Understand HTTP status codes.
- [ ] Implement pagination.
- [ ] Implement filtering and sorting.
- [ ] Understand API versioning.
- [ ] Generate OpenAPI documentation.
- [ ] Build GraphQL APIs.
- [ ] Understand gRPC.
- [ ] Understand Protocol Buffers.
- [ ] Understand API Gateway.
- [ ] Understand BFF.

### Authentication and Authorization

- [ ] Understand authentication.
- [ ] Understand authorization.
- [ ] Implement RBAC.
- [ ] Understand ABAC.
- [ ] Understand OAuth 2.0.
- [ ] Understand OpenID Connect.
- [ ] Implement JWT verification.
- [ ] Understand session management.
- [ ] Implement refresh token rotation.
- [ ] Understand multi-tenant architecture.

### Performance Engineering

- [ ] Implement Redis caching.
- [ ] Understand cache invalidation.
- [ ] Implement distributed rate limiting.
- [ ] Understand request validation.
- [ ] Implement centralized error handling.
- [ ] Implement background jobs.
- [ ] Build worker processes.
- [ ] Implement distributed task queues.
- [ ] Optimize database queries.
- [ ] Understand Node.js memory management.
- [ ] Understand event loop performance.
- [ ] Implement horizontal scaling.

### Production Engineering

- [ ] Implement graceful shutdown.
- [ ] Implement liveness checks.
- [ ] Implement readiness checks.
- [ ] Implement structured logging.
- [ ] Understand metrics and tracing.
- [ ] Implement production monitoring.
- [ ] Understand backend security.
- [ ] Perform API security testing.
- [ ] Perform load testing.
- [ ] Understand production deployment strategies.
- [ ] Deploy an enterprise Node.js backend.

---

# PART 57: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

Use these resources for deeper technical study.

### Node.js and Backend Engineering

1. [Node.js Documentation](https://nodejs.org/docs/latest/api/) — Node.js APIs.
2. [Node.js Event Loop](https://nodejs.org/learn/asynchronous-work/event-loop-timers-and-nexttick) — Event loop internals.
3. [Express Documentation](https://expressjs.com/) — Backend application development.
4. [TypeScript Documentation](https://www.typescriptlang.org/docs/) — TypeScript architecture and type system.

### API Development

5. [OpenAPI Specification](https://spec.openapis.org/oas/latest.html) — API contracts.
6. [GraphQL Documentation](https://graphql.org/learn/) — GraphQL schemas, queries and mutations.
7. [gRPC Documentation](https://grpc.io/docs/) — RPC architecture.
8. [Protocol Buffers](https://protobuf.dev/) — Data serialization.

### Architecture and Security

9. [Microsoft Architecture Guides](https://learn.microsoft.com/en-us/azure/architecture/) — Enterprise architecture patterns.
10. [OWASP API Security](https://api-security.owasp.org/) — API security guidance.
11. [OAuth 2.0 Security Best Current Practice](https://datatracker.ietf.org/doc/html/rfc9700) — Modern OAuth security.
12. [OpenID Connect](https://openid.net/developers/how-connect-works/) — Identity and authentication.

### Performance and Monitoring

13. [Redis Documentation](https://redis.io/docs/latest/) — Caching and data structures.
14. [BullMQ Documentation](https://docs.bullmq.io/) — Background job processing.
15. [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/) — Metrics.
16. [Grafana Documentation](https://grafana.com/docs/) — Monitoring dashboards.
17. [OpenTelemetry Documentation](https://opentelemetry.io/docs/) — Observability.
18. [k6 Documentation](https://grafana.com/docs/k6/latest/) — Load testing.

---

# Phase 68 – Final Summary

This phase moves you from writing backend APIs to engineering complete backend systems.

You should now understand:

- How to organize a large Node.js backend.
- When to use a monolith, modular monolith or microservices.
- How Clean Architecture and Hexagonal Architecture work.
- How DDD helps model complex business logic.
- How to use dependency injection and repository patterns.
- How to design enterprise REST APIs.
- How GraphQL and gRPC differ from REST.
- How OAuth 2.0 and OpenID Connect work.
- How JWT and session authentication differ.
- How to implement RBAC and ABAC.
- How to build multi-tenant applications.
- How to implement Redis caching and distributed rate limiting.
- How to process background jobs.
- How to optimize Node.js performance.
- How to monitor, test and deploy production backends.

## Recommended Learning Priority

| Priority | Topics |
|---|---|
| Essential | Modular Monolith |
| Essential | Clean Architecture |
| Essential | Dependency Injection |
| Essential | Repository Pattern |
| Essential | Advanced REST API Design |
| Essential | API Validation and Error Handling |
| Essential | Authentication and Authorization |
| Essential | RBAC and Multi-Tenancy |
| Essential | Redis Caching |
| Essential | Background Jobs |
| Important | DDD and Bounded Contexts |
| Important | OpenAPI |
| Important | OAuth 2.0 and OIDC |
| Important | API Gateway and BFF |
| Important | Graceful Shutdown |
| Important | Logging and Monitoring |
| Advanced | GraphQL |
| Advanced | gRPC |
| Advanced | ABAC |
| Advanced | Distributed Rate Limiting |
| Advanced | Horizontal Scaling |
| Expert | Production Load Testing |
| Expert | Enterprise Architecture |
| Expert | Complete Backend System Design |

**My recommendation:** Build the Enterprise Expense Management Platform as a modular monolith first. Implement authentication, permissions, tenant isolation, database transactions, Redis caching, background jobs and monitoring. Once those boundaries are clear, you can experiment with extracting individual modules into microservices.

---

# Next Phase Preview

## Phase 69 – Advanced Software Architecture, Enterprise Design Patterns and Scalable Application Engineering

Topics planned:

1. Software Architecture Principles.
2. SOLID Principles.
3. DRY, KISS and YAGNI.
4. Separation of Concerns.
5. High Cohesion and Low Coupling.
6. Dependency Management.
7. Creational Design Patterns.
8. Singleton Pattern.
9. Factory Pattern.
10. Abstract Factory Pattern.
11. Builder Pattern.
12. Prototype Pattern.
13. Structural Design Patterns.
14. Adapter Pattern.
15. Decorator Pattern.
16. Facade Pattern.
17. Proxy Pattern.
18. Composite Pattern.
19. Bridge Pattern.
20. Behavioral Design Patterns.
21. Strategy Pattern.
22. Observer Pattern.
23. Command Pattern.
24. State Pattern.
25. Chain of Responsibility.
26. Template Method.
27. Mediator Pattern.
28. Iterator Pattern.
29. Visitor Pattern.
30. Specification Pattern.
31. CQRS.
32. Event Sourcing.
33. Transactional Outbox.
34. Saga Pattern.
35. Circuit Breaker Pattern.
36. Bulkhead Pattern.
37. Retry Pattern.
38. Timeout Pattern.
39. Rate Limiter Pattern.
40. Anti-Corruption Layer.
41. Strangler Fig Pattern.
42. API Composition.
43. Database per Service.
44. Distributed Transactions.
45. Idempotency Patterns.
46. Enterprise Integration Patterns.
47. Architecture Decision Records.
48. Architecture Testing.
49. Refactoring Legacy Applications.
50. Complete Enterprise Architecture Project.

**Say "OK, Phase 69" when you're ready to continue.**