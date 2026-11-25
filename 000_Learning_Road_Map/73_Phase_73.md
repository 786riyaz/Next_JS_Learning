# PHASE 73 – Advanced Software Architecture, Enterprise Design Patterns, Domain-Driven Design and Large-Scale Application Engineering

**Level:** Advanced → Expert  
**Domain:** Software Architecture, Backend Engineering, Enterprise Application Design, DDD, Microservices and System Design.

**Primary Technologies:**
- Node.js
- TypeScript
- Express.js
- Next.js
- MySQL
- MongoDB
- Redis
- RabbitMQ / Kafka
- Docker
- Kubernetes
- AWS
- Prisma / Sequelize
- PostgreSQL

**Primary Architecture Concepts:**
- Clean Architecture
- Hexagonal Architecture
- Onion Architecture
- Domain-Driven Design
- CQRS
- Event Sourcing
- Microservices
- Event-Driven Architecture
- Enterprise Design Patterns
- Multi-Tenant Architecture
- Architecture Decision Records
- C4 Model

---

# SECTION 1 – SOFTWARE ARCHITECTURE FUNDAMENTALS

## 1. What Is Software Architecture?

Software architecture describes the high-level structure of a software system.

It defines:

- How components are organized.
- How components communicate.
- How responsibilities are distributed.
- How data flows through the application.
- How business logic is structured.
- How the application handles failures.
- How the application scales.
- How security is implemented.
- How the application evolves over time.

In simple terms:

**Software architecture is the blueprint of a software system.**

### Example: Basic E-Commerce Application

Suppose you are developing an e-commerce application.

It contains:

- Authentication.
- Users.
- Products.
- Categories.
- Shopping cart.
- Orders.
- Payments.
- Inventory.
- Notifications.

A poorly structured application might put everything into a single large file.

```text
src/
  app.js
```

This becomes difficult to maintain.

A better architecture separates responsibilities:

```text
src/
  modules/
    users/
    products/
    orders/
    payments/
    inventory/

  infrastructure/
    database/
    cache/
    messaging/

  shared/
    middleware/
    utils/
    errors/

  app.ts
  server.ts
```

Each module has a clear responsibility.

---

## 2. Why Is Software Architecture Important?

Good architecture helps with:

| Benefit | Explanation |
|---|---|
| Maintainability | Easier to modify existing code |
| Scalability | Supports increasing workloads |
| Testability | Business logic can be tested independently |
| Reusability | Components can be reused |
| Extensibility | New functionality can be added |
| Reliability | Failures can be isolated |
| Security | Security responsibilities can be organized |
| Team Collaboration | Teams can work on separate modules |
| Performance | Components can be optimized independently |

### Example

Suppose your application initially supports only online payments.

Later, you want to support:

- Credit cards.
- UPI.
- Wallets.
- PayPal.

If payment logic is tightly coupled to order logic, adding payment methods becomes difficult.

A better architecture separates payment processing behind an interface.

```text
             Order Service
                  |
                  v
           Payment Interface
                  |
       +----------+----------+
       |          |          |
       v          v          v
    UPI Payment Card Payment Wallet Payment
```

This makes the system easier to extend.

---

## 3. Functional vs Non-Functional Requirements

Software architecture must address both functional and non-functional requirements.

### Functional Requirements

Describe what the application should do.

Examples:

- Users can register.
- Users can log in.
- Users can create orders.
- Admins can manage products.
- Customers can make payments.

### Non-Functional Requirements

Describe how the application should operate.

Examples:

- The application should support 100,000 users.
- API responses should generally complete within 300 ms.
- The system should recover from infrastructure failures.
- Sensitive data must be encrypted.
- The application should support horizontal scaling.

### Comparison

| Functional Requirements | Non-Functional Requirements |
|---|---|
| Define application features | Define quality attributes and constraints |
| What should the system do? | How well should it work? |
| Create order | Process requests reliably |
| Login user | Authenticate securely |
| Search products | Return results within a performance target |
| Upload documents | Handle large uploads safely |

**Important:** Architecture decisions should be driven by actual requirements, not by the popularity of a technology.

---

# SECTION 2 – ARCHITECTURAL STYLES

## 4. Monolithic Architecture

A monolithic application is deployed as a single application unit.

It may contain multiple logical modules, but they are packaged and deployed together.

### Architecture

```text
                Client
                  |
                  v
             Monolithic App
                  |
       +----------+----------+
       |          |          |
       v          v          v
     Users     Products    Orders
       |          |          |
       +----------+----------+
                  |
                  v
               Database
```

### Example Structure

```text
src/
  controllers/
    userController.ts
    productController.ts
    orderController.ts

  services/
    userService.ts
    productService.ts
    orderService.ts

  models/
    User.ts
    Product.ts
    Order.ts

  routes/
    userRoutes.ts
    productRoutes.ts
    orderRoutes.ts

  app.ts
```

### Advantages

1. Simple to develop initially.
2. Easy local debugging.
3. Straightforward deployment.
4. Lower infrastructure complexity.
5. Easier transactions within one database.
6. Suitable for small and medium applications.

### Disadvantages

1. Application components may become tightly coupled.
2. Deployment affects the entire application.
3. Scaling individual modules independently is difficult.
4. Large codebases can become difficult to maintain.
5. A failure in shared components can affect the whole application.

### When to Use

- Small applications.
- MVP products.
- Internal business applications.
- Small development teams.
- Applications with relatively straightforward business domains.

**Important:** Monolithic architecture is not inherently bad. A well-designed modular monolith can be an excellent enterprise architecture.

---

## 5. Modular Monolithic Architecture

A modular monolith is a monolithic application divided into well-defined business modules.

Each module owns its responsibilities and exposes controlled interfaces.

### Architecture

```text
                Monolithic Application
                          |
        +-----------------+-----------------+
        |                 |                 |
        v                 v                 v
   User Module       Order Module      Product Module
        |                 |                 |
        v                 v                 v
   User Logic        Order Logic       Product Logic
        |                 |                 |
        +-----------------+-----------------+
                          |
                       Database
```

### Example Structure

```text
src/
  modules/

    users/
      users.controller.ts
      users.service.ts
      users.repository.ts
      users.routes.ts

    products/
      products.controller.ts
      products.service.ts
      products.repository.ts
      products.routes.ts

    orders/
      orders.controller.ts
      orders.service.ts
      orders.repository.ts
      orders.routes.ts

  shared/
    database/
    errors/
    middleware/

  app.ts
```

### Important Rules

- Modules should expose explicit public interfaces.
- Internal implementation details should remain private.
- Avoid direct database access across module boundaries.
- Avoid circular dependencies.
- Keep business logic within its owning module.
- Avoid sharing every internal model between modules.

### Modular Monolith vs Traditional Monolith

| Traditional Monolith | Modular Monolith |
|---|---|
| May have weak module boundaries | Explicit module boundaries |
| Components may depend on internal implementation | Modules communicate through defined interfaces |
| Can become tightly coupled | Encourages controlled coupling |
| Often difficult to extract modules | Easier to extract modules later |

### Practical Recommendation

For many Node.js applications, start with a modular monolith.

Move to microservices only when independent deployment, scaling, team ownership or isolation requirements justify the additional complexity.

---

## 6. Microservices Architecture

Microservices architecture structures an application as a collection of independently deployable services.

Each service owns a specific business capability.

### Architecture

```text
                    API Gateway
                         |
        +----------------+----------------+
        |                |                |
        v                v                v
    User Service    Product Service   Order Service
        |                |                |
        v                v                v
    User DB          Product DB        Order DB

                         |
                         v
                   Payment Service
                         |
                         v
                      Payment DB
```

### Characteristics

- Independent deployment.
- Business capability boundaries.
- Service-level ownership.
- Network-based communication.
- Independent scaling.
- Distributed data management.
- Failure isolation.

### Advantages

1. Services can be deployed independently.
2. Different services can scale independently.
3. Teams can own separate business capabilities.
4. Technology choices can vary where justified.
5. Failures can be isolated more effectively.

### Disadvantages

1. Distributed-system complexity.
2. Network failures.
3. Distributed transactions.
4. Data consistency challenges.
5. More complicated monitoring.
6. Higher infrastructure costs.
7. More demanding deployment operations.

### When to Use

- Large business domains.
- Multiple independent engineering teams.
- Independent scaling requirements.
- Different service availability requirements.
- Strong deployment autonomy requirements.

---

## 7. Monolith vs Modular Monolith vs Microservices

| Feature | Monolith | Modular Monolith | Microservices |
|---|---|---|---|
| Deployment | Single unit | Single unit | Independent services |
| Module boundaries | May be weak | Strong | Service-level boundaries |
| Database | Usually shared | Usually shared with controlled ownership | Prefer service-owned data |
| Scaling | Whole application | Whole application | Individual services |
| Operational complexity | Low | Low–medium | High |
| Network communication | Mostly in-process | Mostly in-process | Network-based |
| Development complexity | Low initially | Medium | High |
| Best suited for | Small applications | Growing applications | Large distributed systems |

### Decision Rule

```text
Small Application
       |
       v
    Monolith
       |
       v
Growing Complexity
       |
       v
 Modular Monolith
       |
       v
Independent Scaling / Deployment Needed?
       |
    +--+--+
    |     |
   Yes    No
    |     |
    v     v
Microservices  Keep Modular Monolith
```

---

## 8. Service-Oriented Architecture (SOA)

SOA stands for Service-Oriented Architecture.

It organizes software capabilities into services that communicate through defined contracts.

SOA is commonly associated with enterprise integration, shared services and enterprise service buses.

### Architecture

```text
             Enterprise Applications
                       |
                       v
                 Service Bus
                       |
          +------------+------------+
          |            |            |
          v            v            v
      Customer      Billing      Inventory
      Service       Service       Service
```

### Important Characteristics

- Service contracts.
- Service reuse.
- Enterprise integration.
- Interoperability.
- Message-based communication.
- Centralized integration infrastructure in some designs.

### SOA vs Microservices

| SOA | Microservices |
|---|---|
| Often enterprise-wide service integration | Focuses on independently deployable business services |
| May use a shared ESB | Often favors lightweight communication |
| Can include larger shared services | Usually favors smaller bounded business capabilities |
| Reuse is often emphasized | Service autonomy is emphasized |
| Can use centralized governance | Often uses decentralized ownership with shared standards |

These are architectural tendencies, not absolute rules.

---

## 9. Event-Driven Architecture

Event-Driven Architecture (EDA) uses events to communicate that something has happened.

Example:

When an order is created, the application publishes:

```json
{
  "eventId": "evt-101",
  "eventType": "OrderCreated",
  "orderId": "ORD-101",
  "userId": "USER-10",
  "total": 2500
}
```

Other services can subscribe to the event.

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
        +------------+------------+
        |            |            |
        v            v            v
   Inventory     Payment     Notification
    Service      Service       Service
```

### Benefits

- Loose coupling.
- Asynchronous communication.
- Independent consumers.
- Easier event-based integrations.
- Better workload decoupling.

### Challenges

- Event ordering.
- Duplicate messages.
- Eventual consistency.
- Message retries.
- Schema evolution.
- Event observability.

### Important Distinction

A command asks a component to perform an action.

An event describes something that has already happened.

| Command | Event |
|---|---|
| CreateOrder | OrderCreated |
| ReserveInventory | InventoryReserved |
| ProcessPayment | PaymentProcessed |
| CancelOrder | OrderCancelled |

---

# SECTION 3 – ARCHITECTURAL PATTERNS

## 10. Layered Architecture

Layered Architecture divides an application into logical layers.

A typical backend application contains:

1. Presentation Layer.
2. Application Layer.
3. Domain Layer.
4. Infrastructure Layer.

### Architecture

```text
        Presentation Layer
                |
                v
        Application Layer
                |
                v
           Domain Layer
                |
                v
        Infrastructure Layer
```

### Responsibilities

| Layer | Responsibility |
|---|---|
| Presentation | HTTP routes, controllers and request handling |
| Application | Use cases and application orchestration |
| Domain | Business rules and domain models |
| Infrastructure | Database, cache, external APIs and messaging |

### Example

```text
src/
  presentation/
    controllers/
    routes/

  application/
    use-cases/

  domain/
    entities/
    interfaces/

  infrastructure/
    database/
    repositories/
```

### Important Rule

Business rules should not depend directly on Express, MySQL or Redis.

The domain should remain independent of infrastructure implementation.

---

## 11. Clean Architecture

Clean Architecture organizes code so that business rules remain independent of external technologies.

The central idea is the **Dependency Rule**:

Source-code dependencies should point inward toward higher-level business policies.

### Architecture

```text
        +----------------------------+
        |       Frameworks           |
        |                            |
        |    +------------------+    |
        |    |   Interface      |    |
        |    |   Adapters       |    |
        |    |                  |    |
        |    |  +------------+  |    |
        |    |  | Use Cases  |  |    |
        |    |  |            |  |    |
        |    |  | +--------+ |  |    |
        |    |  | | Domain | |  |    |
        |    |  | +--------+ |  |    |
        |    |  +------------+  |    |
        |    +------------------+    |
        +----------------------------+
```

### Example

Suppose you have a `CreateOrder` use case.

The use case should not need to know whether the order is saved using:

- MySQL.
- PostgreSQL.
- MongoDB.
- An in-memory repository.

It should depend on an abstraction.

### Domain Interface

```typescript
export interface OrderRepository {
  save(order: Order): Promise<void>;
}
```

### Use Case

```typescript
export class CreateOrder {

  constructor(
    private readonly orderRepository: OrderRepository
  ) {}

  async execute(order: Order): Promise<void> {

    if (order.total <= 0) {
      throw new Error("Invalid order total");
    }

    await this.orderRepository.save(order);
  }
}
```

### Infrastructure Implementation

```typescript
export class MySQLOrderRepository
  implements OrderRepository {

  async save(order: Order): Promise<void> {

    // Persist order using MySQL.

  }
}
```

The use case depends on the repository interface, not the MySQL implementation.

### Benefits

- Independent business logic.
- Easier testing.
- Easier database replacement.
- Better separation of concerns.
- Reduced framework coupling.

---

## 12. Hexagonal Architecture

Hexagonal Architecture is also known as Ports and Adapters Architecture.

It separates the core application from external systems.

The core exposes ports, and external adapters implement or use those ports.

### Architecture

```text
              External World
                    |
          +---------+---------+
          |                   |
          v                   v
       REST API           Message Queue
          |                   |
          v                   v
       Inbound              Inbound
       Adapter              Adapter
          |                   |
          +---------+---------+
                    |
                    v
             Application Core
                    |
          +---------+---------+
          |                   |
          v                   v
     Database Port       Payment Port
          |                   |
          v                   v
     MySQL Adapter       Stripe Adapter
```

### Ports

Interfaces that define how the core communicates with external components.

Examples:

```typescript
export interface PaymentGateway {
  charge(
    amount: number,
    currency: string
  ): Promise<string>;
}
```

### Adapter

An implementation of a port.

```typescript
export class StripePaymentAdapter
  implements PaymentGateway {

  async charge(
    amount: number,
    currency: string
  ): Promise<string> {

    // Call payment provider.

    return "payment-101";
  }
}
```

### When to Use

- Applications with multiple external integrations.
- Systems that need testable business logic.
- Applications where infrastructure may change.
- Complex enterprise applications.

---

## 13. Onion Architecture

Onion Architecture organizes the application around the domain model.

The domain sits at the center.

Infrastructure and external services remain outside.

### Architecture

```text
       +---------------------------+
       |      Infrastructure       |
       |                           |
       |   +-------------------+   |
       |   | Application       |   |
       |   |                   |   |
       |   |  +-------------+  |   |
       |   |  | Domain      |  |   |
       |   |  |             |  |   |
       |   |  | Entities    |  |   |
       |   |  | Value Obj.  |  |   |
       |   |  +-------------+  |   |
       |   +-------------------+   |
       +---------------------------+
```

### Core Principles

- Domain independence.
- Dependency inversion.
- Explicit boundaries.
- Business rules remain central.
- Infrastructure is replaceable.

---

## 14. Clean vs Hexagonal vs Onion Architecture

These architectures share many principles.

| Feature | Clean | Hexagonal | Onion |
|---|---|---|---|
| Main focus | Business rules and use cases | Ports and adapters | Domain-centered dependency structure |
| Core concept | Dependency Rule | Ports and adapters | Domain at the center |
| Infrastructure | Outer layer | Adapters | Outer layer |
| Testing | Highly testable | Highly testable | Highly testable |
| Best suited for | Complex applications | Integration-heavy systems | Domain-heavy applications |

**Important:** They are related architectural approaches, not completely incompatible alternatives. A project can combine their ideas.

---

# SECTION 4 – DOMAIN-DRIVEN DESIGN (DDD)

## 15. What Is Domain-Driven Design?

Domain-Driven Design is an approach to software development that focuses on modeling the business domain.

It was popularized by Eric Evans.

DDD is especially useful when business rules are complex.

Examples:

- Banking.
- Insurance.
- E-commerce.
- Healthcare administration.
- Logistics.
- Financial systems.
- Enterprise resource planning.

### Example

Consider an e-commerce system.

A simple application might have:

```text
User
Product
Order
Payment
```

But real business requirements may include:

- Order cancellation rules.
- Inventory reservation.
- Refund policies.
- Discount eligibility.
- Payment reconciliation.
- Shipment tracking.
- Tax calculations.

DDD helps model these business concepts explicitly.

### Important Principle

**The domain model should represent the business, not merely the database schema.**

---

## 16. Strategic DDD

Strategic DDD focuses on understanding the business domain and defining boundaries.

Important concepts:

1. Domain.
2. Subdomain.
3. Core Domain.
4. Supporting Subdomain.
5. Generic Subdomain.
6. Bounded Context.
7. Ubiquitous Language.
8. Context Mapping.

---

## 17. Domain and Subdomain

A domain is the business area your software serves.

Example:

```text
E-Commerce Domain
```

It can be divided into subdomains.

```text
E-Commerce
    |
    +---- Customer Management
    |
    +---- Product Catalog
    |
    +---- Inventory
    |
    +---- Order Management
    |
    +---- Payment Processing
    |
    +---- Shipping
```

### Types of Subdomains

| Type | Meaning | Example |
|---|---|---|
| Core | Provides competitive business value | Recommendation engine |
| Supporting | Supports the core business | Order management |
| Generic | Common functionality | Email delivery |

### Why Is This Important?

Not every business capability deserves the same engineering investment.

A company may build its competitive advantage around recommendations but use an external service for email delivery.

---

## 18. Bounded Context

A Bounded Context is an explicit boundary within which a particular domain model and its terminology have a defined meaning.

This is one of the most important DDD concepts.

### Example

In an e-commerce system, the word `Customer` may mean different things in different contexts.

In the Sales Context:

```text
Customer
- Customer ID
- Name
- Email
- Billing Address
```

In the Shipping Context:

```text
Recipient
- Recipient Name
- Delivery Address
- Contact Number
- Delivery Instructions
```

The concepts are related, but their models have different responsibilities.

### Architecture

```text
                  E-Commerce
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
    Sales           Shipping        Payment
    Context         Context         Context
       |               |               |
       v               v               v
   Customer          Recipient       Payer
```

### Important Rule

Do not force every business context to use one giant shared model.

Bounded contexts help prevent a domain model from becoming tightly coupled to unrelated business concerns.

---

## 19. Ubiquitous Language

Ubiquitous Language means developers and domain experts use the same business terminology within a bounded context.

Example:

Suppose a logistics application uses the terms:

- Shipment.
- Dispatch.
- Delivery Attempt.
- Consignment.
- Returned Shipment.

Developers should use these terms consistently in:

- Code.
- Documentation.
- Business discussions.
- Tests.
- Architecture diagrams.

### Poor Naming

```typescript
class DataProcessor {
  execute(data: any) {}
}
```

### Domain-Oriented Naming

```typescript
class ShipmentDispatchService {
  dispatchShipment(shipment: Shipment) {}
}
```

The second example communicates business intent more clearly.

---

## 20. Context Mapping

Context Mapping describes how bounded contexts relate to each other.

Common relationships include:

- Shared Kernel.
- Customer-Supplier.
- Conformist.
- Anti-Corruption Layer.
- Open Host Service.
- Published Language.
- Partnership.

### Example

```text
Sales Context
      |
      v
Published Order Event
      |
      v
Shipping Context
```

The Shipping Context consumes the published event and translates it into its own model.

This reduces direct coupling between the two contexts.

---

# SECTION 5 – TACTICAL DDD

## 21. Entity

An Entity is an object identified by its identity rather than only by its attributes.

Example:

```typescript
class Customer {

  constructor(
    public readonly id: string,
    public name: string,
    public email: string
  ) {}

}
```

Two customer objects can have identical names and email addresses but still represent different customers if their identities differ.

### Characteristics

- Has identity.
- Can change over time.
- Represents a business concept.
- Has a lifecycle.

---

## 22. Value Object

A Value Object is defined by its attributes rather than a unique identity.

Example:

```typescript
class Money {

  constructor(
    public readonly amount: number,
    public readonly currency: string
  ) {

    if (amount < 0) {
      throw new Error("Amount cannot be negative");
    }

  }

  equals(other: Money): boolean {

    return (
      this.amount === other.amount &&
      this.currency === other.currency
    );

  }

}
```

Two `Money` objects with the same amount and currency are considered equivalent.

### Common Value Objects

- Money.
- Address.
- Email Address.
- Phone Number.
- Date Range.
- Percentage.
- Coordinates.

### Entity vs Value Object

| Entity | Value Object |
|---|---|
| Has identity | No independent identity |
| Can change state | Usually immutable |
| Compared by identity | Compared by value |
| Example: Customer | Example: Money |

---

## 23. Aggregate

An Aggregate is a cluster of domain objects treated as a consistency boundary.

An Aggregate has an Aggregate Root.

The root controls access to the aggregate's internal objects.

### Example: Order Aggregate

```text
               Order Aggregate
                      |
                      v
                 Order Root
                      |
         +------------+------------+
         |            |            |
         v            v            v
      OrderItem   OrderItem    ShippingAddress
```

The `Order` is the Aggregate Root.

Other parts of the application should interact with its internal objects through the root's public behavior.

### Why Aggregates Matter

They help enforce business invariants.

Example:

An order cannot be confirmed if it has no items.

```typescript
class Order {

  private items: OrderItem[] = [];

  addItem(item: OrderItem): void {

    this.items.push(item);

  }

  confirm(): void {

    if (this.items.length === 0) {
      throw new Error(
        "Cannot confirm an empty order"
      );
    }

    // Change order state.

  }

}
```

The business rule is enforced inside the domain model.

---

## 24. Aggregate Root

An Aggregate Root is the main entry point for modifying an aggregate.

Example:

```text
Order
  |
  +---- OrderItem
  |
  +---- OrderItem
  |
  +---- Address
```

External components should not independently modify `OrderItem` if doing so would bypass the order's business rules.

### Important Design Rules

- Keep aggregates reasonably small.
- Protect business invariants.
- Avoid unnecessary cross-aggregate transactions.
- Reference other aggregates by identity where appropriate.
- Use domain events to communicate important changes.

---

## 25. Domain Events

A Domain Event represents something meaningful that happened in the business domain.

Examples:

- OrderPlaced.
- PaymentReceived.
- InventoryReserved.
- ShipmentDispatched.
- CustomerRegistered.

Example:

```typescript
class OrderPlaced {

  constructor(
    public readonly orderId: string,
    public readonly customerId: string,
    public readonly occurredAt: Date
  ) {}

}
```

### Why Use Domain Events?

- Communicate domain changes.
- Decouple business processes.
- Trigger other use cases.
- Support event-driven integrations.
- Create an audit trail where required.

### Domain Event Flow

```text
Order Aggregate
       |
       v
OrderPlaced Event
       |
       v
Event Handler
       |
       +---- Send Notification
       |
       +---- Update Analytics
       |
       +---- Request Inventory Reservation
```

A domain event is an in-process domain concept. An integration event is a message published for other systems or bounded contexts.

Do not assume every domain event should automatically be published externally.

---

## 26. Application Service

An Application Service coordinates a use case.

It generally does not contain the core business rules themselves.

Example:

```typescript
class PlaceOrderUseCase {

  constructor(
    private readonly orderRepository: OrderRepository,
    private readonly eventPublisher: EventPublisher
  ) {}

  async execute(command: PlaceOrderCommand) {

    const order = Order.create(
      command.customerId,
      command.items
    );

    await this.orderRepository.save(order);

    await this.eventPublisher.publish(
      new OrderPlaced(
        order.id,
        order.customerId,
        new Date()
      )
    );

  }

}
```

This example illustrates orchestration, but publishing an event after a database commit introduces a dual-write failure risk. In production, consider the transactional outbox pattern, covered later in this phase.

---

## 27. Domain Service

A Domain Service contains business logic that does not naturally belong to a single entity or value object.

Example:

```typescript
class DiscountCalculationService {

  calculateDiscount(
    total: number,
    customerType: string
  ): number {

    if (customerType === "premium") {
      return total * 0.15;
    }

    if (customerType === "regular") {
      return total * 0.05;
    }

    return 0;

  }

}
```

Use a domain service when the rule is genuinely domain-level and does not naturally belong to one entity.

Do not create services for every small operation.

---

# SECTION 6 – REPOSITORY AND UNIT OF WORK PATTERNS

## 28. Repository Pattern

The Repository Pattern abstracts data persistence behind a collection-like interface.

The domain or application layer interacts with a repository abstraction rather than directly accessing database implementation details.

### Architecture

```text
Application Service
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

### Interface

```typescript
export interface UserRepository {

  findById(id: string): Promise<User | null>;

  findByEmail(email: string): Promise<User | null>;

  save(user: User): Promise<void>;

}
```

### Implementation

```typescript
export class MySQLUserRepository
  implements UserRepository {

  async findById(id: string): Promise<User | null> {

    // Query MySQL.

    return null;

  }

  async findByEmail(
    email: string
  ): Promise<User | null> {

    // Query MySQL.

    return null;

  }

  async save(user: User): Promise<void> {

    // Persist user.

  }

}
```

### Benefits

- Persistence abstraction.
- Easier unit testing.
- Database implementation can change.
- Business logic remains isolated.

### Common Mistake

Creating a generic repository that exposes every possible ORM operation can leak infrastructure details into the domain.

Prefer repository interfaces that reflect business needs.

---

## 29. Unit of Work Pattern

The Unit of Work Pattern tracks changes made during a business transaction and coordinates their persistence.

Example:

An order operation may need to:

1. Create an order.
2. Create order items.
3. Update an inventory reservation record.

These changes should be committed atomically when they belong to the same database transaction.

### Architecture

```text
Place Order Use Case
        |
        v
     Unit of Work
        |
        +---- Order Repository
        |
        +---- Item Repository
        |
        +---- Reservation Repository
        |
        v
   Database Transaction
        |
        v
       Commit
```

### Simplified TypeScript Example

```typescript
interface UnitOfWork {
  begin(): Promise<void>;
  commit(): Promise<void>;
  rollback(): Promise<void>;
}
```

Usage:

```typescript
async function executeOrder(
  unitOfWork: UnitOfWork
) {

  await unitOfWork.begin();

  try {

    // Save order.
    // Save order items.
    // Save related records.

    await unitOfWork.commit();

  } catch (error) {

    await unitOfWork.rollback();

    throw error;

  }

}
```

Use a database transaction when the operation must be atomic within one database.

A Unit of Work does not automatically solve transactions across multiple independent databases.

---

# SECTION 7 – CQRS AND EVENT SOURCING

## 30. What Is CQRS?

CQRS stands for Command Query Responsibility Segregation.

It separates operations that change state from operations that read state.

### Command

A command requests a state change.

Examples:

- CreateOrder.
- UpdateCustomer.
- CancelOrder.
- ReserveInventory.

### Query

A query retrieves information without changing state.

Examples:

- GetOrder.
- ListProducts.
- GetCustomerProfile.
- SearchOrders.

### Traditional Architecture

```text
           Application
                |
                v
             Database
                |
        +-------+-------+
        |               |
        v               v
      Write            Read
```

### CQRS Architecture

```text
             Client
                |
                v
          Application API
                |
        +-------+-------+
        |               |
        v               v
     Commands         Queries
        |               |
        v               v
   Write Model       Read Model
        |               |
        v               v
   Write Store       Read Store
```

### Benefits

- Independent read and write optimization.
- Clearer separation of responsibilities.
- Better support for complex read models.
- Useful for read-heavy systems.

### Disadvantages

- More implementation complexity.
- Data synchronization challenges.
- Eventual consistency may occur.
- Additional infrastructure.

**Important:** CQRS does not require separate databases. You can apply it within a single application and database.

---

## 31. CQRS Example in Node.js

### Command

```typescript
export class CreateOrderCommand {

  constructor(
    public readonly customerId: string,
    public readonly total: number
  ) {}

}
```

### Command Handler

```typescript
export class CreateOrderHandler {

  constructor(
    private readonly repository: OrderRepository
  ) {}

  async handle(
    command: CreateOrderCommand
  ) {

    const order = Order.create(
      command.customerId,
      command.total
    );

    await this.repository.save(order);

    return order.id;

  }

}
```

### Query Handler

```typescript
export class GetOrderHandler {

  constructor(
    private readonly repository: OrderReadRepository
  ) {}

  async handle(orderId: string) {

    return this.repository.findById(orderId);

  }

}
```

The write path and read path can evolve independently.

---

## 32. What Is Event Sourcing?

Event Sourcing stores state changes as an ordered sequence of events rather than storing only the latest state.

### Traditional Database

An order record might contain:

```json
{
  "orderId": "ORD-101",
  "status": "SHIPPED",
  "total": 2500
}
```

You see the current state, but not necessarily the full history of how it changed.

### Event-Sourced Database

```text
OrderCreated
     |
     v
PaymentReceived
     |
     v
InventoryReserved
     |
     v
OrderConfirmed
     |
     v
ShipmentDispatched
```

The current state is derived by applying the events.

### Architecture

```text
           Command
              |
              v
        Domain Aggregate
              |
              v
         Event Store
              |
              v
        Event Stream
              |
        +-----+-----+
        |           |
        v           v
   Projection A  Projection B
        |           |
        v           v
   Order Read   Analytics Read
     Model         Model
```

### Advantages

- Historical state.
- Complete business event history.
- Auditability.
- Event replay.
- Multiple read models.
- Useful for complex temporal business requirements.

### Disadvantages

- More complex data modeling.
- Event schema evolution.
- Replay complexity.
- Event versioning.
- Storage growth.
- Eventual consistency.
- More complicated debugging.

### When to Use

- Financial systems.
- Audit-heavy applications.
- Complex business workflows.
- Systems requiring historical reconstruction.
- Applications with multiple projections.

Do not use Event Sourcing simply because you are using Kafka or because you want an event-driven architecture.

They are different concepts.

---

## 33. CQRS vs Event Sourcing

| CQRS | Event Sourcing |
|---|---|
| Separates commands and queries | Stores state changes as events |
| Can use traditional database tables | Uses an event log or event store |
| Does not require event history | Event history is central |
| Can be relatively simple | Usually more complex |
| Can be used independently | Often combined with CQRS |

---

# SECTION 8 – DISTRIBUTED TRANSACTION PATTERNS

## 34. Saga Pattern

A Saga manages a business transaction across multiple independent services.

Instead of using one global database transaction, it coordinates a sequence of local transactions.

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
Arrange Shipment
     |
     v
Confirm Order
```

What happens if payment fails?

The application may need to release reserved inventory and cancel the order.

### Compensating Transactions

```text
Create Order
     |
     v
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

The compensating action does not necessarily restore the exact historical state. It performs a business operation that counteracts the earlier action.

### Saga Implementation Types

1. Choreography.
2. Orchestration.

### Choreography

Services react to events and publish subsequent events.

```text
Order Service
     |
     v
OrderCreated
     |
     v
Inventory Service
     |
     v
InventoryReserved
     |
     v
Payment Service
     |
     v
PaymentCompleted
```

### Orchestration

A central coordinator controls the workflow.

```text
           Order Saga
                |
       +--------+--------+
       |        |        |
       v        v        v
   Inventory  Payment  Shipping
    Service   Service  Service
```

### Choreography vs Orchestration

| Choreography | Orchestration |
|---|---|
| Distributed event coordination | Central workflow coordinator |
| No central orchestrator | Explicit orchestrator |
| Can become difficult to trace | Workflow is easier to visualize |
| Useful for simple event chains | Useful for complex business workflows |

Microsoft's architecture guidance describes Saga as a sequence of local transactions with compensating transactions when a step fails. [Microsoft Learn](https://learn.microsoft.com/en-us/azure/architecture/patterns/saga?utm_source=chatgpt.com)

---

## 35. Outbox Pattern

The Outbox Pattern solves a common dual-write problem.

Suppose an application must:

1. Save an order in MySQL.
2. Publish an `OrderCreated` event to Kafka.

What if the database commit succeeds but event publishing fails?

The database and message broker become inconsistent.

### Without Outbox

```text
Create Order
     |
     +---- Save to MySQL
     |
     +---- Publish Event
```

One operation can succeed while the other fails.

### With Outbox

Store the business change and the event record in the same database transaction.

```text
              Order Service
                    |
                    v
              DB Transaction
                    |
             +------+------+
             |             |
             v             v
          Orders       Outbox Table
             |             |
             +------+------+
                    |
                  Commit
                    |
                    v
              Outbox Relay
                    |
                    v
                 Kafka
                    |
                    v
              Event Consumers
```

### Example Outbox Table

```sql
CREATE TABLE outbox_events (

    id VARCHAR(100) PRIMARY KEY,

    event_type VARCHAR(100) NOT NULL,

    aggregate_id VARCHAR(100) NOT NULL,

    payload JSON NOT NULL,

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    published_at TIMESTAMP NULL

);
```

### Transaction Example

```typescript
await database.transaction(async (tx) => {

  await tx.orders.create({
    id: order.id,
    total: order.total
  });

  await tx.outboxEvents.create({
    id: crypto.randomUUID(),
    eventType: "OrderCreated",
    aggregateId: order.id,
    payload: {
      orderId: order.id,
      total: order.total
    }
  });

});
```

A separate relay publishes unpublished events.

### Important Considerations

- Outbox relays may publish duplicates.
- Consumers must be idempotent.
- Track publication status.
- Implement retries.
- Monitor outbox backlog.
- Plan cleanup and retention.
- Preserve ordering where business requirements demand it.

---

## 36. Anti-Corruption Layer (ACL)

An Anti-Corruption Layer protects one domain model from another system's incompatible model.

Example:

Your new application integrates with an old ERP system.

The ERP returns:

```json
{
  "cust_no": "C1001",
  "cust_nm": "John",
  "addr_1": "Ahmedabad"
}
```

Your application expects:

```typescript
interface Customer {
  id: string;
  name: string;
  address: string;
}
```

The ACL performs translation.

```typescript
function mapLegacyCustomer(
  legacy: any
): Customer {

  return {
    id: legacy.cust_no,
    name: legacy.cust_nm,
    address: legacy.addr_1
  };

}
```

### Architecture

```text
New Application
      |
      v
Application Domain Model
      |
      v
Anti-Corruption Layer
      |
      v
Legacy ERP API
```

### When to Use

- Legacy integrations.
- External vendor APIs.
- Different bounded contexts.
- Gradual system modernization.

Microsoft recommends using this pattern to isolate incompatible subsystem models, while noting that the translation layer adds complexity and latency. [Microsoft Learn](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer?utm_source=chatgpt.com)

---

## 37. Strangler Fig Pattern

The Strangler Fig Pattern supports gradual migration from a legacy system.

Instead of replacing the entire application at once, you move functionality incrementally.

### Initial Architecture

```text
Client
  |
  v
Legacy Monolith
```

### Migration Architecture

```text
                 Client
                   |
                   v
               API Gateway
                   |
           +-------+-------+
           |               |
           v               v
      Legacy System   New Services
```

### Final Architecture

```text
Client
  |
  v
API Gateway
  |
  +---- User Service
  |
  +---- Order Service
  |
  +---- Product Service
```

### Migration Steps

1. Identify a business capability to migrate.
2. Introduce a routing façade.
3. Implement the capability in a new service.
4. Route selected requests to the new implementation.
5. Migrate and validate data ownership.
6. Monitor both systems.
7. Retire the corresponding legacy functionality.
8. Repeat.

This approach reduces the risk of a large-scale rewrite. [Microsoft Learn](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig?trk=public_post_comment-text\&utm_source=chatgpt.com)

---

# SECTION 9 – API AND CLIENT ARCHITECTURE PATTERNS

## 38. API Gateway Pattern

An API Gateway provides a centralized entry point for API clients.

It can perform:

- Routing.
- Authentication.
- Rate limiting.
- Request validation.
- TLS termination.
- API versioning.
- Logging.
- Request aggregation.

### Architecture

```text
                Clients
                   |
                   v
               API Gateway
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
    User API   Product API   Order API
```

### Important Rule

An API Gateway should not become a giant business-logic layer.

Core business rules belong in the appropriate application or domain service.

---

## 39. Backend for Frontend (BFF)

BFF stands for Backend for Frontend.

It provides a backend tailored to the needs of a particular frontend client.

Example:

An application has:

- Web application.
- Mobile application.
- Admin dashboard.

Each client has different data and interaction requirements.

### Architecture

```text
                 Clients
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Web BFF  Mobile BFF  Admin BFF
          |         |         |
          +---------+---------+
                    |
             Backend Services
                    |
          +---------+---------+
          |         |         |
          v         v         v
        Users    Products   Orders
```

### Advantages

- Client-specific API responses.
- Reduced frontend complexity.
- Less unnecessary network traffic.
- Independent frontend evolution.

### Disadvantages

- More backend services.
- Some duplicated orchestration.
- Additional deployment and monitoring.

### When to Use

When web, mobile and admin clients have substantially different API needs.

---

# SECTION 10 – RESILIENCE AND RELIABILITY PATTERNS

## 40. Circuit Breaker Pattern

The Circuit Breaker Pattern prevents repeated calls to an unhealthy dependency.

It has three common states:

1. Closed.
2. Open.
3. Half-Open.

### Closed

Requests flow normally.

### Open

Requests are rejected or handled through a fallback without repeatedly calling the failing dependency.

### Half-Open

A limited number of test requests are allowed to determine whether the dependency has recovered.

### Architecture

```text
             Application
                  |
                  v
             Circuit Breaker
                  |
          +-------+-------+
          |               |
          v               v
        Closed           Open
          |               |
          v               v
    External API       Fallback
          |
          v
      Failure Threshold
          |
          v
        Open
          |
          v
      Half-Open
          |
      +---+---+
      |       |
      v       v
  Recovered  Failed
      |       |
      v       v
   Closed   Open
```

### When to Use

- Payment provider integrations.
- Third-party APIs.
- Service-to-service communication.
- Unreliable external dependencies.

---

## 41. Bulkhead Pattern

Bulkhead isolates resources so that failure in one component does not exhaust the entire application.

The name comes from ship compartments that prevent flooding from spreading.

### Example

Suppose an application communicates with:

- Payment Service.
- Notification Service.
- Recommendation Service.

If recommendation requests become slow, they should not consume all application resources.

### Architecture

```text
             Application
                  |
        +---------+---------+
        |         |         |
        v         v         v
     Payment   Notification Recommendation
      Pool        Pool        Pool
```

Each dependency receives separate resource limits.

### Benefits

- Failure isolation.
- Resource protection.
- Better service availability.
- Reduced cascading failures.

---

## 42. Retry Pattern

The Retry Pattern attempts an operation again after a transient failure.

Example:

A third-party API returns a temporary 503 response.

The application may retry after a delay.

### Exponential Backoff

```text
Attempt 1: Immediately
Attempt 2: After 1 second
Attempt 3: After 2 seconds
Attempt 4: After 4 seconds
Attempt 5: After 8 seconds
```

Add jitter to prevent many clients from retrying simultaneously.

### Important Rules

- Retry only transient failures.
- Respect retry budgets and deadlines.
- Avoid retrying non-idempotent operations without protection.
- Use bounded attempts.
- Avoid retry storms.
- Combine with circuit breakers where appropriate.

---

## 43. Timeout Pattern

A timeout limits how long an application waits for an operation.

Example:

```typescript
const controller = new AbortController();

const timeout = setTimeout(() => {
  controller.abort();
}, 3000);

try {

  const response = await fetch(
    "https://api.example.com/data",
    {
      signal: controller.signal
    }
  );

  return await response.json();

} finally {

  clearTimeout(timeout);

}
```

This limits the client-side wait for the request.

A complete production system should also propagate deadlines and timeouts across service boundaries.

---

## 44. Retry vs Circuit Breaker vs Bulkhead vs Timeout

| Pattern | Main Purpose |
|---|---|
| Retry | Recover from transient failures |
| Circuit Breaker | Stop calls to unhealthy dependencies |
| Bulkhead | Isolate resource consumption |
| Timeout | Prevent indefinite waiting |

These patterns can be combined.

For example:

```text
Request
   |
   v
Timeout
   |
   v
Circuit Breaker
   |
   v
Bulkhead
   |
   v
Retry Policy
   |
   v
External Service
```

Order and implementation depend on the specific client library and failure model.

Microsoft's design pattern catalog describes these patterns as complementary reliability mechanisms. [Microsoft Learn](https://learn.microsoft.com/azure/architecture/patterns?WT.mc_id=AZ-MVP-5002880\&utm_source=chatgpt.com)

---

# SECTION 11 – OBJECT-ORIENTED AND ENTERPRISE DESIGN PATTERNS

## 45. Dependency Injection (DI)

Dependency Injection is a technique in which an object receives its dependencies from an external source rather than constructing them itself.

### Without DI

```typescript
class UserService {

  private repository = new MySQLUserRepository();

}
```

The service is tightly coupled to MySQL.

### With DI

```typescript
class UserService {

  constructor(
    private readonly repository: UserRepository
  ) {}

}
```

Now the implementation can be changed without modifying the service.

### Composition Root

The application creates and connects dependencies in one place.

```typescript
const userRepository = new MySQLUserRepository();

const userService = new UserService(
  userRepository
);
```

### Benefits

- Loose coupling.
- Better testing.
- Easier substitution.
- Clear dependency management.

---

## 46. Inversion of Control (IoC)

Inversion of Control means a component does not control every aspect of its dependencies and execution.

Instead, control is delegated to a framework, container or external composition mechanism.

### Traditional Control

```text
Application
    |
    v
Creates Dependencies
    |
    v
Executes Logic
```

### Inversion of Control

```text
IoC Container
      |
      v
Creates Dependencies
      |
      v
Injects Dependencies
      |
      v
Application Executes Logic
```

### DI vs IoC

| DI | IoC |
|---|---|
| A technique for supplying dependencies | A broader design principle |
| Constructor injection is an example | Framework-controlled lifecycle is another example |
| Helps reduce coupling | Transfers control to an external mechanism |

Dependency Injection is one common way to implement Inversion of Control.

---

## 47. Factory Pattern

The Factory Pattern encapsulates object creation.

Example:

```typescript
interface PaymentMethod {
  pay(amount: number): Promise<void>;
}

class UpiPayment implements PaymentMethod {
  async pay(amount: number) {
    console.log("UPI payment:", amount);
  }
}

class CardPayment implements PaymentMethod {
  async pay(amount: number) {
    console.log("Card payment:", amount);
  }
}
```

Factory:

```typescript
class PaymentFactory {

  static create(type: string): PaymentMethod {

    switch (type) {

      case "upi":
        return new UpiPayment();

      case "card":
        return new CardPayment();

      default:
        throw new Error("Unsupported payment method");

    }

  }

}
```

Usage:

```typescript
const payment = PaymentFactory.create("upi");

await payment.pay(2500);
```

### When to Use

When object creation depends on runtime conditions or complex configuration.

---

## 48. Strategy Pattern

The Strategy Pattern allows interchangeable algorithms behind a common interface.

Example:

Different discount strategies.

```typescript
interface DiscountStrategy {
  calculate(total: number): number;
}
```

```typescript
class RegularDiscount implements DiscountStrategy {

  calculate(total: number): number {
    return total * 0.05;
  }

}
```

```typescript
class PremiumDiscount implements DiscountStrategy {

  calculate(total: number): number {
    return total * 0.15;
  }

}
```

Context:

```typescript
class CheckoutService {

  constructor(
    private readonly strategy: DiscountStrategy
  ) {}

  calculateFinalPrice(total: number): number {

    return total - this.strategy.calculate(total);

  }

}
```

Usage:

```typescript
const checkout = new CheckoutService(
  new PremiumDiscount()
);

console.log(
  checkout.calculateFinalPrice(1000)
);
```

### Benefits

- Replaces large conditional blocks.
- Supports new algorithms.
- Improves testability.
- Follows Open/Closed Principle.

---

## 49. Adapter Pattern

The Adapter Pattern allows incompatible interfaces to work together.

Example:

Your application expects:

```typescript
interface NotificationProvider {
  send(message: string): Promise<void>;
}
```

But a third-party provider exposes a different method.

```typescript
class ExternalSMSProvider {

  async sendMessage(text: string) {
    console.log(text);
  }

}
```

Adapter:

```typescript
class SMSAdapter implements NotificationProvider {

  constructor(
    private readonly provider: ExternalSMSProvider
  ) {}

  async send(message: string): Promise<void> {

    await this.provider.sendMessage(message);

  }

}
```

### Use Cases

- Third-party API integration.
- Legacy systems.
- Database driver replacement.
- Payment gateway integration.

---

## 50. Decorator Pattern

The Decorator Pattern adds behavior to an object without changing its original implementation.

Example:

A notification service needs logging.

```typescript
interface NotificationService {
  send(message: string): Promise<void>;
}
```

Base implementation:

```typescript
class EmailNotification implements NotificationService {

  async send(message: string): Promise<void> {

    console.log("Email:", message);

  }

}
```

Decorator:

```typescript
class LoggingNotificationDecorator
  implements NotificationService {

  constructor(
    private readonly service: NotificationService
  ) {}

  async send(message: string): Promise<void> {

    console.log("Sending notification");

    await this.service.send(message);

    console.log("Notification sent");

  }

}
```

Usage:

```typescript
const notification = new LoggingNotificationDecorator(
  new EmailNotification()
);

await notification.send("Order confirmed");
```

---

## 51. Observer Pattern

The Observer Pattern allows one object to notify multiple subscribers when its state changes or an event occurs.

Example:

```text
Order Created
     |
     +---- Email Subscriber
     |
     +---- Analytics Subscriber
     |
     +---- Notification Subscriber
```

Simplified implementation:

```typescript
type Listener = (event: string) => void;

class EventEmitter {

  private listeners: Listener[] = [];

  subscribe(listener: Listener) {
    this.listeners.push(listener);
  }

  emit(event: string) {

    for (const listener of this.listeners) {
      listener(event);
    }

  }

}
```

Usage:

```typescript
const emitter = new EventEmitter();

emitter.subscribe((event) => {
  console.log("Email:", event);
});

emitter.subscribe((event) => {
  console.log("Analytics:", event);
});

emitter.emit("OrderCreated");
```

This is a basic in-process Observer implementation, not a durable distributed messaging system.

---

## 52. Important Design Patterns Summary

| Pattern | Purpose |
|---|---|
| Factory | Centralize object creation |
| Strategy | Interchange algorithms |
| Adapter | Connect incompatible interfaces |
| Decorator | Add behavior dynamically |
| Observer | Notify multiple subscribers |
| Repository | Abstract data persistence |
| Unit of Work | Coordinate database changes |
| Dependency Injection | Supply dependencies externally |
| Singleton | Maintain one shared instance |
| Facade | Simplify a complex subsystem |
| Builder | Construct complex objects step by step |
| Proxy | Control access to another object |
| Command | Represent an operation as an object |

**Important:** Design patterns are tools, not mandatory structures. Do not introduce a pattern unless it solves a real design problem.

---

# SECTION 12 – MULTI-TENANT ARCHITECTURE

## 53. What Is Multi-Tenancy?

Multi-tenancy is an architecture in which one software system serves multiple customers or organizations, called tenants.

Example:

A SaaS CRM application may serve:

- Company A.
- Company B.
- Company C.

Each organization should only access its own data.

### Architecture

```text
                 SaaS Application
                         |
           +-------------+-------------+
           |             |             |
           v             v             v
        Tenant A      Tenant B      Tenant C
           |             |             |
           v             v             v
        Company A     Company B     Company C
```

### Multi-Tenant Database Strategies

#### Strategy 1: Shared Database, Shared Schema

All tenants share tables.

Each tenant's records include a `tenant_id`.

```sql
CREATE TABLE customers (
    id BIGINT PRIMARY KEY,
    tenant_id BIGINT NOT NULL,
    name VARCHAR(150)
);
```

Example query:

```sql
SELECT *
FROM customers
WHERE tenant_id = ?;
```

Advantages:

- Lower cost.
- Easier centralized management.
- Efficient resource utilization.

Disadvantages:

- Tenant isolation must be enforced everywhere.
- Large tenants may affect smaller tenants.
- Tenant-specific customization is harder.

#### Strategy 2: Shared Database, Separate Schema

Each tenant has a separate schema.

```text
Database
   |
   +---- tenant_a
   |
   +---- tenant_b
   |
   +---- tenant_c
```

Advantages:

- Better logical isolation.
- Tenant-specific schema management.

Disadvantages:

- More complicated migrations.
- Increased operational overhead.

#### Strategy 3: Separate Database Per Tenant

```text
Application
    |
    +---- Tenant A Database
    |
    +---- Tenant B Database
    |
    +---- Tenant C Database
```

Advantages:

- Stronger data isolation.
- Independent backup and restoration.
- Tenant-specific performance management.

Disadvantages:

- Higher cost.
- More database operations.
- More complicated provisioning and migrations.

### Comparison

| Strategy | Isolation | Cost | Operational Complexity |
|---|---|---|---|
| Shared schema | Logical | Low | Low–Medium |
| Separate schema | Stronger logical isolation | Medium | Medium–High |
| Separate database | Strongest of these three options | High | High |

### Critical Security Rule

Never trust a `tenant_id` supplied by the client.

Derive the tenant identity from an authenticated and authorized context.

Example:

```typescript
async function getCustomer(
  customerId: string,
  authenticatedTenantId: string
) {

  return Customer.findOne({
    where: {
      id: customerId,
      tenantId: authenticatedTenantId
    }
  });

}
```

Tenant isolation must apply to:

- Database queries.
- Cache keys.
- File storage.
- Search indexes.
- Background jobs.
- Message consumers.
- Logs and analytics.

---

# SECTION 13 – PLUGIN-BASED ARCHITECTURE

## 54. What Is Plugin-Based Architecture?

Plugin-Based Architecture allows functionality to be added through independently developed modules or plugins.

Example:

A CMS may support plugins for:

- SEO.
- Payment gateways.
- Analytics.
- Authentication.
- Reporting.

### Architecture

```text
             Core Application
                    |
          +---------+---------+
          |         |         |
          v         v         v
        Plugin A Plugin B  Plugin C
```

### Benefits

- Extensibility.
- Modular functionality.
- Easier feature expansion.
- Optional capabilities.
- Independent plugin development.

### Challenges

- Plugin compatibility.
- Version management.
- Security isolation.
- Dependency conflicts.
- Plugin lifecycle management.

### Simple Plugin Interface

```typescript
interface ApplicationPlugin {

  name: string;

  register(): Promise<void>;

  initialize(): Promise<void>;

}
```

Plugin:

```typescript
class AnalyticsPlugin implements ApplicationPlugin {

  name = "analytics";

  async register(): Promise<void> {
    console.log("Registering analytics");
  }

  async initialize(): Promise<void> {
    console.log("Initializing analytics");
  }

}
```

A production plugin system also needs version compatibility, permissions, lifecycle rules and failure isolation.

---

# SECTION 14 – ARCHITECTURE DECISION RECORDS

## 55. What Is an Architecture Decision Record (ADR)?

An ADR is a document that records an important architecture decision.

It captures:

- The context.
- The decision.
- The alternatives.
- The consequences.

### Example

Suppose your team must choose between:

- Monolithic architecture.
- Microservices architecture.

An ADR records why the team selected a modular monolith.

### ADR Structure

```text
ADR-001: Choose Modular Monolith

Status:
Accepted

Context:
The application is initially being developed
by a small engineering team.

Decision:
Use a modular monolithic architecture.

Alternatives:
1. Traditional monolith.
2. Microservices.

Consequences:
- Simpler deployment.
- Lower operational complexity.
- Strong module boundaries required.
- Independent service deployment is not initially available.
```

### Why ADRs Matter

- Preserve architectural reasoning.
- Help onboard developers.
- Prevent repeated debates.
- Document trade-offs.
- Support future architecture changes.

### Suggested ADR Folder

```text
docs/
  architecture/
    decisions/
      0001-modular-monolith.md
      0002-mysql-database.md
      0003-redis-caching.md
      0004-event-driven-notifications.md
```

---

# SECTION 15 – C4 MODEL AND ARCHITECTURE DIAGRAMS

## 56. What Is the C4 Model?

The C4 Model is a way to visualize software architecture at different levels of abstraction.

C4 stands for:

- Context.
- Containers.
- Components.
- Code.

Official reference: [C4 Model](https://c4model.com/).

### Level 1: System Context Diagram

Shows the software system and its relationships with users and external systems.

```text
          Customer
              |
              v
       E-Commerce System
              |
       +------+------+
       |             |
       v             v
 Payment Provider  Shipping Provider
```

### Level 2: Container Diagram

Shows the major applications and data stores.

```text
              E-Commerce System
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Next.js       Node.js       MySQL
       Frontend       API          Database
```

A container in C4 means an independently running application or data store, not necessarily a Docker container.

### Level 3: Component Diagram

Shows the internal components of a container.

```text
              Node.js API
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
    User       Product       Order
 Controller   Controller   Controller
       |           |           |
       v           v           v
    Services    Services    Services
```

### Level 4: Code Diagram

Shows classes, interfaces and their relationships.

```text
OrderController
       |
       v
CreateOrderUseCase
       |
       v
OrderRepository
       |
       v
MySQLOrderRepository
```

### When to Use Each Level

| Diagram | Audience | Purpose |
|---|---|---|
| Context | Business and technical teams | Understand system boundaries |
| Container | Architects and developers | Understand major applications |
| Component | Developers | Understand internal structure |
| Code | Developers | Understand detailed implementation |

---

# SECTION 16 – ARCHITECTURE DOCUMENTATION

## 57. What Should an Architecture Document Contain?

A professional architecture document commonly contains:

1. Project overview.
2. Business objectives.
3. Functional requirements.
4. Non-functional requirements.
5. System context.
6. Architectural style.
7. Component diagram.
8. Data architecture.
9. API architecture.
10. Authentication architecture.
11. Authorization architecture.
12. Security design.
13. Deployment architecture.
14. Scalability strategy.
15. Reliability strategy.
16. Monitoring and observability.
17. Disaster recovery.
18. Technology decisions.
19. ADR references.
20. Known trade-offs and risks.

### Architecture Documentation Structure

```text
docs/
  architecture/
    overview.md
    requirements.md
    context-diagram.md
    container-diagram.md
    component-diagram.md
    data-architecture.md
    security-architecture.md
    deployment-architecture.md
    adr/
```

---

# SECTION 17 – ENTERPRISE E-COMMERCE ARCHITECTURE PROJECT

## 58. Project Overview

Build a production-style e-commerce platform using enterprise architecture principles.

### Requirements

The application must support:

- Customer registration.
- Authentication.
- Product management.
- Categories.
- Product search.
- Shopping cart.
- Orders.
- Payments.
- Inventory.
- Shipping.
- Notifications.
- Admin dashboard.
- Multi-tenancy as an optional extension.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + Express |
| Language | TypeScript |
| Database | MySQL |
| Cache | Redis |
| Messaging | RabbitMQ or Kafka |
| Authentication | OAuth 2.0 / OIDC |
| ORM | Sequelize or Prisma |
| Containerization | Docker |
| Orchestration | Kubernetes |
| Cloud | AWS |
| CI/CD | GitHub Actions |
| Monitoring | Prometheus + Grafana |
| Tracing | OpenTelemetry |

---

## 59. Recommended Architecture

Start with a **modular monolith**.

```text
                  Next.js Frontend
                         |
                         v
                   API Gateway
                         |
                         v
                  Node.js Backend
                         |
       +-----------------+-----------------+
       |                 |                 |
       v                 v                 v
   User Module      Product Module     Order Module
       |                 |                 |
       v                 v                 v
  User Domain       Product Domain      Order Domain
       |                 |                 |
       +-----------------+-----------------+
                         |
                         v
                    MySQL Database
                         |
                         v
                       Redis
```

This design gives you a structured application without introducing distributed-system complexity prematurely.

---

## 60. Recommended Project Structure

```text
ecommerce-platform/

  apps/

    api/
      src/

        modules/

          users/
            domain/
              entities/
              value-objects/
              events/

            application/
              commands/
              queries/
              services/

            infrastructure/
              repositories/

            presentation/
              controllers/
              routes/

          products/
            domain/
            application/
            infrastructure/
            presentation/

          orders/
            domain/
            application/
            infrastructure/
            presentation/

          payments/
            domain/
            application/
            infrastructure/
            presentation/

          inventory/
            domain/
            application/
            infrastructure/
            presentation/

        shared/

          database/
          messaging/
          cache/
          errors/
          logging/
          security/

        app.ts
        server.ts

    web/
      src/
        app/
        components/
        features/
        services/
        hooks/

  infrastructure/
    docker/
    kubernetes/
    terraform/

  docs/
    architecture/
    adr/

  tests/
    integration/
    e2e/
```

---

## 61. Domain Boundaries

Define separate bounded contexts.

```text
              E-Commerce Platform
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
    Customer         Catalog          Sales
    Context          Context          Context
       |               |               |
       v               v               v
  Customer Model  Product Model    Order Model
```

Additional contexts:

- Payment.
- Inventory.
- Shipping.
- Notification.

### Important Design Decision

Do not create a separate microservice for every table.

A service should represent a meaningful business capability, not simply a database entity.

---

## 62. Example Order Domain

An order should enforce its business rules.

```typescript
type OrderStatus =
  | "PENDING"
  | "CONFIRMED"
  | "CANCELLED";

class Order {

  private constructor(
    public readonly id: string,
    public readonly customerId: string,
    private _status: OrderStatus,
    public readonly total: number
  ) {}

  static create(
    id: string,
    customerId: string,
    total: number
  ): Order {

    if (total <= 0) {
      throw new Error("Invalid order total");
    }

    return new Order(
      id,
      customerId,
      "PENDING",
      total
    );

  }

  confirm(): void {

    if (this._status !== "PENDING") {
      throw new Error(
        "Only pending orders can be confirmed"
      );
    }

    this._status = "CONFIRMED";

  }

  cancel(): void {

    if (this._status === "CANCELLED") {
      throw new Error("Order already cancelled");
    }

    this._status = "CANCELLED";

  }

  get status(): OrderStatus {
    return this._status;
  }

}
```

The order controls its own valid state transitions.

---

## 63. Order Creation Workflow

```text
Customer
   |
   v
Create Order Request
   |
   v
Order Use Case
   |
   v
Validate Order
   |
   v
Save Order
   |
   v
Publish OrderCreated
   |
   +---- Inventory Reservation
   |
   +---- Payment Processing
   |
   +---- Customer Notification
```

For production:

- Use idempotency keys for order creation.
- Persist domain changes transactionally.
- Use the Outbox Pattern for reliable event publishing.
- Use Saga orchestration or choreography where distributed coordination is needed.
- Implement compensating operations.
- Track order state transitions.

---

## 64. Recommended Architectural Evolution

Do not start with all services distributed.

Use a staged approach:

### Stage 1 – Modular Monolith

Build:

- User module.
- Product module.
- Order module.
- Payment module.
- Inventory module.

Keep them within one application with clear boundaries.

### Stage 2 – Introduce Event-Driven Communication

Use an event broker for asynchronous tasks such as:

- Notifications.
- Analytics.
- Search indexing.

### Stage 3 – Extract Independent Services

If justified, extract high-value capabilities such as:

- Payment processing.
- Search.
- Notification delivery.

### Stage 4 – Introduce Distributed Architecture

Add:

- Independent deployments.
- Service-owned databases.
- Distributed tracing.
- Circuit breakers.
- Sagas.
- Outbox relays.

### Stage 5 – Enterprise Scale

Add:

- Multi-region architecture.
- Advanced traffic management.
- Platform engineering.
- Centralized security governance.
- Advanced observability.

---

# SECTION 18 – ARCHITECTURAL ANTIPATTERNS

## 65. What Is an Architectural Antipattern?

An architectural antipattern is a commonly repeated design approach that causes problems over time.

### 65.1 Big Ball of Mud

The application has no clear structure or boundaries.

```text
Everything
   |
   +---- Everything
   |
   +---- Everything
   |
   +---- Everything
```

**Solution:** Establish module boundaries and explicit responsibilities.

### 65.2 God Object

One class or module performs too many responsibilities.

Example:

```typescript
class ApplicationManager {

  createUser() {}

  createOrder() {}

  processPayment() {}

  sendEmail() {}

  updateInventory() {}

  generateReport() {}

}
```

**Solution:** Separate responsibilities into appropriate domain or application services.

### 65.3 Distributed Monolith

An application is split into multiple services, but they remain tightly coupled.

Characteristics:

- Services must be deployed together.
- Many synchronous dependencies.
- Shared database tables.
- Frequent cross-service changes.

**Solution:** Improve service boundaries, data ownership and deployment independence.

### 65.4 Shared Database Across Microservices

Multiple services directly modify the same database tables.

This creates coupling and makes independent evolution difficult.

**Solution:** Establish clear data ownership and communicate through APIs or events.

### 65.5 Overengineering

Using complex architecture before the business requires it.

Example:

Using 15 microservices for a small CRUD application.

**Solution:** Start with the simplest architecture that satisfies current requirements.

### 65.6 Anemic Domain Model

Domain objects contain only data, while all business logic is placed in external service classes.

This may be appropriate for simple CRUD applications, but can be problematic in complex domains where invariants are scattered.

**Solution:** Place important domain rules inside entities, aggregates and domain services where appropriate.

---

# SECTION 19 – IMPORTANT ARCHITECTURAL COMPARISONS

## 66. Architecture Decision Matrix

| Requirement | Suitable Architecture |
|---|---|
| Simple CRUD application | Monolith |
| Growing business application | Modular Monolith |
| Complex business domain | DDD + Modular Architecture |
| Independent service scaling | Microservices |
| Complex enterprise integration | SOA or Microservices |
| Highly asynchronous workflows | Event-Driven Architecture |
| Complex read/write workloads | CQRS |
| Historical event reconstruction | Event Sourcing |
| Legacy modernization | Strangler Fig |
| Multiple frontend clients | BFF |
| External integration isolation | Hexagonal Architecture / ACL |
| Multi-customer SaaS | Multi-Tenant Architecture |

## 67. Domain Model vs Database Model

| Domain Model | Database Model |
|---|---|
| Represents business concepts | Represents persisted data |
| Focuses on business behavior | Focuses on storage |
| Contains domain invariants | Contains storage constraints |
| Can include entities and value objects | Contains tables, documents and relationships |
| Technology-independent where possible | Database-specific |

Do not automatically map every database table to a domain entity.

## 68. Domain Event vs Integration Event

| Domain Event | Integration Event |
|---|---|
| Represents a domain occurrence | Communicates an occurrence externally |
| Often internal to a bounded context | Crosses process or context boundaries |
| Can use internal domain types | Should use stable published contracts |
| May not be persisted | Often published through durable messaging |

## 69. Command vs Event vs Query

| Type | Meaning | Example |
|---|---|---|
| Command | Request an action | CreateOrder |
| Event | Report something that happened | OrderCreated |
| Query | Request information | GetOrder |

---

# SECTION 20 – INTERVIEW QUESTIONS

Below are 50 important software architecture interview questions.

## Beginner Level

1. What is software architecture?
2. What is the difference between architecture and design?
3. What is monolithic architecture?
4. What is modular monolithic architecture?
5. What is microservices architecture?
6. What is SOA?
7. What is event-driven architecture?
8. What is layered architecture?
9. What is separation of concerns?
10. What are functional and non-functional requirements?

## Intermediate Level

11. What is Clean Architecture?
12. What is Hexagonal Architecture?
13. What is Onion Architecture?
14. Explain the Dependency Rule.
15. What is Dependency Injection?
16. What is Inversion of Control?
17. What is the Repository Pattern?
18. What is Unit of Work?
19. What is the Factory Pattern?
20. What is the Strategy Pattern?
21. What is the Adapter Pattern?
22. What is the Decorator Pattern?
23. What is the Observer Pattern?
24. What is an API Gateway?
25. What is Backend for Frontend?

## Advanced Level

26. What is Domain-Driven Design?
27. What is a bounded context?
28. What is ubiquitous language?
29. What is an aggregate?
30. What is an aggregate root?
31. What is a value object?
32. What is a domain event?
33. What is a domain service?
34. What is CQRS?
35. What is Event Sourcing?
36. What is the Saga Pattern?
37. What is the Outbox Pattern?
38. What is an Anti-Corruption Layer?
39. What is the Strangler Fig Pattern?
40. What is a multi-tenant architecture?

## Expert Level

41. How would you design a modular monolith for an e-commerce application?
42. How would you identify bounded contexts in a complex business domain?
43. When should a monolith be decomposed into microservices?
44. How would you implement distributed transactions without a global database transaction?
45. How would you prevent duplicate event processing?
46. How would you design a multi-tenant SaaS platform?
47. How would you implement CQRS with MySQL and Redis?
48. How would you modernize a legacy application using the Strangler Fig Pattern?
49. How would you design an extensible plugin architecture?
50. Design an enterprise-grade e-commerce architecture using DDD, CQRS, event-driven communication and microservices.

---

# SECTION 21 – PRACTICAL ASSIGNMENTS

Complete these assignments progressively.

## Level 1 – Architecture Fundamentals

1. Explain software architecture with a real-world example.
2. Create functional and non-functional requirements for an e-commerce application.
3. Draw a monolithic architecture diagram.
4. Draw a modular monolithic architecture diagram.
5. Draw a microservices architecture diagram.
6. Compare SOA and microservices.
7. Explain event-driven architecture.
8. Create a layered architecture project structure.
9. Identify coupling in an existing Node.js application.
10. Document an architecture decision.

## Level 2 – Architectural Styles

11. Convert a poorly structured Express application into a modular monolith.
12. Create separate User, Product and Order modules.
13. Define module interfaces.
14. Remove circular dependencies.
15. Implement Clean Architecture in a Node.js project.
16. Implement Hexagonal Architecture.
17. Create an infrastructure adapter for MySQL.
18. Replace MySQL with an in-memory repository in tests.
19. Create an architecture diagram.
20. Compare Clean, Onion and Hexagonal Architecture.

## Level 3 – Domain-Driven Design

21. Identify the domain of an e-commerce application.
22. Identify its subdomains.
23. Identify its core domain.
24. Create bounded contexts.
25. Create a ubiquitous language glossary.
26. Design Customer and Order entities.
27. Implement a Money value object.
28. Implement an Order aggregate.
29. Implement aggregate business invariants.
30. Create domain events.

## Level 4 – Application and Domain Services

31. Implement an application service.
32. Implement a domain service.
33. Create a repository interface.
34. Implement a MySQL repository.
35. Implement a Unit of Work.
36. Write unit tests for domain entities.
37. Implement dependency injection.
38. Create a composition root.
39. Implement an Adapter Pattern.
40. Implement a Strategy Pattern.

## Level 5 – CQRS and Event Sourcing

41. Implement separate command handlers.
42. Implement query handlers.
43. Create independent read and write models.
44. Implement a simple event store.
45. Replay events to rebuild application state.
46. Create a materialized read model.
47. Implement event versioning.
48. Implement idempotent event processing.
49. Document CQRS trade-offs.
50. Design an event-sourced order system.

## Level 6 – Distributed Architecture

51. Implement a Saga workflow.
52. Implement a Saga compensating action.
53. Compare choreography and orchestration.
54. Implement the Outbox Pattern.
55. Build an outbox relay.
56. Handle duplicate messages.
57. Implement an Anti-Corruption Layer.
58. Create a Strangler Fig migration plan.
59. Implement an API Gateway.
60. Design a BFF for web and mobile clients.

## Level 7 – Reliability and Enterprise Patterns

61. Implement a Circuit Breaker.
62. Implement exponential backoff with jitter.
63. Implement request timeouts.
64. Implement a Bulkhead.
65. Create a Factory Pattern.
66. Create a Decorator Pattern.
67. Create an Observer Pattern.
68. Create a plugin registration system.
69. Create an architecture decision record.
70. Create C4 Context, Container and Component diagrams.

## Level 8 – Multi-Tenant and Enterprise Architecture

71. Design shared-schema multi-tenancy.
72. Implement tenant-aware database queries.
73. Create tenant-aware cache keys.
74. Implement tenant-level authorization.
75. Design separate-schema multi-tenancy.
76. Compare database-per-tenant architecture.
77. Implement tenant-specific configuration.
78. Design tenant provisioning.
79. Design tenant data isolation tests.
80. Create a multi-tenant SaaS architecture document.

## Level 9 – Complete Architecture Project

81. Create an enterprise e-commerce domain model.
82. Define bounded contexts.
83. Build a modular monolith.
84. Implement Clean Architecture.
85. Implement repositories and Unit of Work.
86. Implement CQRS for order management.
87. Implement domain events.
88. Integrate RabbitMQ or Kafka.
89. Implement the Outbox Pattern.
90. Implement a Saga for order processing.
91. Add payment and inventory modules.
92. Add multi-tenant support.
93. Add API Gateway.
94. Add observability and distributed tracing.
95. Containerize the application.

## Level 10 – Architecture Documentation and Evaluation

96. Create a C4 Context Diagram.
97. Create a C4 Container Diagram.
98. Create a C4 Component Diagram.
99. Write five ADR documents.
100. Document architectural trade-offs.
101. Create a scalability strategy.
102. Create a reliability strategy.
103. Create a security architecture.
104. Create a migration strategy.
105. Perform an architecture review.

---

# SECTION 22 – QUICK REVISION NOTES

| Concept | Important Point |
|---|---|
| Software Architecture | High-level structure of a software system |
| Monolith | Single deployable application |
| Modular Monolith | Single deployment with strong module boundaries |
| Microservices | Independently deployable business services |
| SOA | Service-oriented enterprise integration |
| EDA | Communication through events |
| Layered Architecture | Separation into logical layers |
| Clean Architecture | Business rules independent of frameworks |
| Hexagonal Architecture | Ports and adapters |
| Onion Architecture | Domain-centered dependency structure |
| DDD | Modeling complex business domains |
| Domain | Business area |
| Subdomain | Smaller business capability |
| Bounded Context | Boundary for a consistent domain model |
| Ubiquitous Language | Shared business terminology |
| Entity | Object with identity |
| Value Object | Object defined by its values |
| Aggregate | Domain consistency boundary |
| Aggregate Root | Entry point to an aggregate |
| Domain Event | Important business occurrence |
| Application Service | Coordinates a use case |
| Domain Service | Encapsulates domain logic |
| Repository | Abstracts persistence |
| Unit of Work | Coordinates database changes |
| CQRS | Separates commands and queries |
| Event Sourcing | Stores state changes as events |
| Saga | Coordinates distributed transactions |
| Outbox | Reliably persists events with business changes |
| ACL | Protects domain boundaries through translation |
| Strangler Fig | Incremental legacy modernization |
| API Gateway | Central API entry point |
| BFF | Backend tailored to a frontend |
| Circuit Breaker | Stops calls to unhealthy dependencies |
| Bulkhead | Isolates resources |
| Retry | Reattempts transient failures |
| Timeout | Limits waiting time |
| DI | Supplies dependencies externally |
| IoC | Inverts control to an external mechanism |
| Factory | Encapsulates object creation |
| Strategy | Interchangeable algorithms |
| Adapter | Bridges incompatible interfaces |
| Decorator | Adds behavior dynamically |
| Observer | Notifies subscribers |
| Multi-Tenancy | One system serving multiple tenants |
| Plugin Architecture | Extensible application through plugins |
| ADR | Records architecture decisions |
| C4 Model | Visualizes architecture at four levels |

---

# SECTION 23 – PHASE 73 COMPLETION CHECKLIST

### Architecture Fundamentals
- [ ] Understand software architecture.
- [ ] Understand functional and non-functional requirements.
- [ ] Understand monolithic architecture.
- [ ] Understand modular monoliths.
- [ ] Understand microservices.
- [ ] Understand SOA.
- [ ] Understand event-driven architecture.

### Architectural Styles
- [ ] Understand Layered Architecture.
- [ ] Understand Clean Architecture.
- [ ] Understand Hexagonal Architecture.
- [ ] Understand Onion Architecture.
- [ ] Implement dependency inversion.
- [ ] Implement module boundaries.

### Domain-Driven Design
- [ ] Understand domains and subdomains.
- [ ] Identify bounded contexts.
- [ ] Understand ubiquitous language.
- [ ] Implement entities.
- [ ] Implement value objects.
- [ ] Implement aggregates.
- [ ] Implement aggregate roots.
- [ ] Implement domain events.
- [ ] Understand context mapping.

### Application Patterns
- [ ] Implement application services.
- [ ] Implement domain services.
- [ ] Implement repository interfaces.
- [ ] Implement Unit of Work.
- [ ] Implement Dependency Injection.
- [ ] Understand Inversion of Control.
- [ ] Implement Factory.
- [ ] Implement Strategy.
- [ ] Implement Adapter.
- [ ] Implement Decorator.
- [ ] Implement Observer.

### Distributed Architecture
- [ ] Understand CQRS.
- [ ] Understand Event Sourcing.
- [ ] Implement Saga.
- [ ] Implement Outbox.
- [ ] Understand choreography and orchestration.
- [ ] Implement Anti-Corruption Layer.
- [ ] Understand Strangler Fig.
- [ ] Understand API Gateway.
- [ ] Understand BFF.

### Enterprise Architecture
- [ ] Design multi-tenant architecture.
- [ ] Implement tenant isolation.
- [ ] Understand plugin architecture.
- [ ] Create ADRs.
- [ ] Create C4 diagrams.
- [ ] Document architecture decisions.
- [ ] Complete the enterprise e-commerce architecture project.

---

# SECTION 24 – OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

### 1. Software Architecture

1. [Microsoft Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/)
2. [AWS Architecture Center](https://aws.amazon.com/architecture/)
3. [Martin Fowler – Architecture](https://martinfowler.com/architecture/)
4. [C4 Model](https://c4model.com/)

### 2. Domain-Driven Design

5. [Domain-Driven Design – Eric Evans](https://www.domainlanguage.com/ddd/)
6. [Microsoft DDD Architecture Guidance](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/)
7. [Microsoft Architecture Patterns](https://learn.microsoft.com/en-us/azure/architecture/patterns/)

### 3. Architectural Patterns

8. [Clean Architecture – Robert C. Martin](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/)
9. [Azure Anti-Corruption Layer Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)
10. [Azure Strangler Fig Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)
11. [Azure Saga Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/saga)
12. [Azure Microservices Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/patterns)

### 4. CQRS and Event Sourcing

13. [AWS Event Sourcing Pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/event-sourcing-pattern.html)
14. [Azure CQRS Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs)
15. [Martin Fowler – CQRS](https://martinfowler.com/bliki/CQRS.html)
16. [Martin Fowler – Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html)

### 5. Node.js and TypeScript

17. [Node.js Documentation](https://nodejs.org/docs/latest/api/)
18. [TypeScript Documentation](https://www.typescriptlang.org/docs/)
19. [Express Documentation](https://expressjs.com/)
20. [NestJS Documentation](https://docs.nestjs.com/)

### 6. Architecture Decision Records

21. [ADR GitHub Repository](https://github.com/joelparkerhenderson/architecture-decision-record)
22. [C4 Model Documentation](https://c4model.com/)

---

# PHASE 73 – FINAL SUMMARY

You have now studied the major architectural approaches and design patterns used to build complex enterprise applications.

The overall architecture journey looks like this:

```text
                BUSINESS REQUIREMENTS
                         |
                         v
                 DOMAIN UNDERSTANDING
                         |
                         v
                  SOFTWARE ARCHITECTURE
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
       Modular         Clean          Hexagonal
       Monolith      Architecture    Architecture
          |              |              |
          +--------------+--------------+
                         |
                         v
                 DOMAIN-DRIVEN DESIGN
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
       Entities      Aggregates     Domain Events
                         |
                         v
                  APPLICATION DESIGN
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
        CQRS        Event Sourcing     Repository
                         |
                         v
                DISTRIBUTED PATTERNS
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
        Saga          Outbox          API Gateway
                         |
                         v
                 ENTERPRISE SYSTEM
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      Multi-Tenant    Scalability    Reliability
                         |
                         v
                 ARCHITECTURE REVIEW
```

## Five Important Takeaways

1. **Architecture should follow business requirements.** Do not select microservices, CQRS or Event Sourcing without a concrete reason.

2. **Domain-Driven Design helps manage business complexity.** Bounded contexts, aggregates and domain events create meaningful boundaries.

3. **Clean, Hexagonal and Onion Architecture protect business logic from infrastructure dependencies.**

4. **Distributed systems require explicit consistency and failure-management strategies.** Saga, Outbox, idempotency and event-driven communication are important tools.

5. **Good architecture is evolutionary.** A modular monolith can evolve into distributed services as business and operational requirements change.

---

# NEXT PHASE

## PHASE 74 – Advanced Enterprise Backend Engineering, High-Performance Node.js, API Optimization and Production-Grade Application Development

Topics planned:

1. Advanced Node.js Runtime Architecture.
2. V8 Engine Internals.
3. Node.js Event Loop Internals.
4. libuv Architecture.
5. Worker Threads.
6. Child Processes.
7. Cluster Architecture.
8. CPU-Bound vs I/O-Bound Workloads.
9. Memory Management.
10. Garbage Collection.
11. Memory Leaks.
12. Heap Snapshots.
13. CPU Profiling.
14. Flame Graphs.
15. Async Context Management.
16. AsyncLocalStorage.
17. Advanced TypeScript Backend Architecture.
18. Advanced Express.js Architecture.
19. NestJS Enterprise Architecture.
20. Advanced API Design.
21. REST API Optimization.
22. GraphQL Architecture.
23. gRPC.
24. API Versioning.
25. API Pagination Strategies.
26. Cursor-Based Pagination.
27. API Caching.
28. Redis Advanced Caching.
29. Database Connection Pooling.
30. Query Optimization.
31. N+1 Query Problems.
32. Database Index Optimization.
33. Read Replicas.
34. Database Partitioning.
35. Connection Management.
36. Backend Performance Testing.
37. Load Testing.
38. Stress Testing.
39. k6.
40. Artillery.
41. Node.js Production Logging.
42. Advanced Observability.
43. Distributed Tracing.
44. Graceful Shutdown.
45. Zero-Downtime Deployment.
46. Backend Reliability Engineering.
47. Production Incident Debugging.
48. High-Performance API Project.
49. Enterprise Backend Architecture Project.
50. Complete Production-Ready Node.js Platform.

**Phase 73 is complete. Continue to Phase 74 when you're ready.**