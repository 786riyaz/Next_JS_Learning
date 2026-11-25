# Phase 50 – Advanced Backend Architecture, Design Patterns and Clean Architecture

**Level:** Advanced  
**Domain:** Backend Engineering, Software Architecture, Object-Oriented Design, Design Patterns  
**Primary Technologies:** Node.js, TypeScript, Express.js, MySQL, MongoDB, Redis, Jest

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand Clean Architecture and its importance.
2. Understand Layered Architecture.
3. Understand Hexagonal Architecture.
4. Understand Onion Architecture.
5. Understand Domain-Driven Design (DDD).
6. Apply SOLID principles.
7. Understand and implement common design patterns.
8. Understand Dependency Injection and Inversion of Control.
9. Implement Repository and Service Layer patterns.
10. Understand DTOs and Data Mapping.
11. Understand Domain Models and Use Cases.
12. Understand CQRS and Event Sourcing.
13. Implement transaction management.
14. Understand the Unit of Work pattern.
15. Design modular Node.js applications.
16. Build scalable Express.js backend architecture.
17. Write testable and maintainable backend code.
18. Structure production-ready backend projects.

---

# PART 1: SOFTWARE ARCHITECTURE FUNDAMENTALS

# 2. What Is Software Architecture?

Software Architecture defines the high-level structure of a software application.

It determines:

- How the application is divided into components.
- How components communicate.
- Where business logic is placed.
- How data is accessed.
- How dependencies are managed.
- How the application handles changes.
- How the system can be tested and maintained.

Consider a basic Node.js application:

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

This is a simple architectural structure.

As an application grows, you need to establish clear boundaries between different responsibilities.

For example:

- Controllers should handle HTTP requests and responses.
- Services should coordinate application operations.
- Domain objects should contain business rules.
- Repositories should handle data access.
- Infrastructure should contain database and external-service implementations.

The objective is to prevent business logic from becoming tightly coupled to frameworks and infrastructure.

---

# 3. Why Is Good Backend Architecture Important?

Consider two applications.

### Application A: Poor Architecture

```text
src/
    server.js
    app.js
    routes.js
    database.js
```

Suppose all application logic is written inside route handlers.

```javascript
app.post("/expenses", async (req, res) => {
    // Validate request

    // Check authentication

    // Calculate expense

    // Apply business rules

    // Execute SQL query

    // Send notification

    // Return response
});
```

As the application grows, this code becomes difficult to maintain.

Problems include:

- Business logic is mixed with HTTP logic.
- Database queries are difficult to reuse.
- Testing becomes difficult.
- Changes can break unrelated functionality.
- Responsibilities are unclear.

### Application B: Structured Architecture

```text
src/
    modules/
        expenses/
            expense.routes.ts
            expense.controller.ts
            expense.service.ts
            expense.repository.ts
            expense.model.ts
            expense.validation.ts

    infrastructure/
        database/

    middleware/

    config/

    app.ts
    server.ts
```

Each component has a specific responsibility.

### Benefits

| Benefit | Explanation |
|---|---|
| Maintainability | Easier to modify existing functionality |
| Testability | Components can be tested independently |
| Reusability | Business logic can be reused |
| Scalability | Modules can evolve independently |
| Separation of Concerns | Responsibilities remain clearly separated |
| Flexibility | Infrastructure can be changed with fewer business-logic changes |

---

# PART 2: LAYERED ARCHITECTURE

# 4. What Is Layered Architecture?

Layered Architecture divides an application into logical layers.

Each layer has a specific responsibility.

A common backend architecture contains:

1. Presentation Layer.
2. Application Layer.
3. Domain Layer.
4. Infrastructure Layer.

### Architecture Diagram

```text
             Client
                |
                v
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
                |
                v
             Database
```

Let's understand each layer.

## 4.1 Presentation Layer

The Presentation Layer handles communication with external clients.

In an Express.js application, it commonly contains:

- Routes.
- Controllers.
- Request validation.
- HTTP response formatting.
- Authentication middleware.

Example:

```typescript
// expense.controller.ts

import { Request, Response } from "express";
import { ExpenseService } from "./expense.service";

export class ExpenseController {
    constructor(
        private expenseService: ExpenseService
    ) {}

    async create(req: Request, res: Response) {
        const expense = await this.expenseService.createExpense(
            req.body
        );

        return res.status(201).json({
            success: true,
            data: expense
        });
    }
}
```

The controller receives an HTTP request and delegates the work to the application logic.

It should not contain complicated business rules.

## 4.2 Application Layer

The Application Layer coordinates application operations.

It contains:

- Use cases.
- Application services.
- Transaction coordination.
- Workflow orchestration.

Example:

```typescript
// create-expense.usecase.ts

export class CreateExpenseUseCase {
    constructor(
        private expenseRepository: ExpenseRepository
    ) {}

    async execute(data: CreateExpenseInput) {
        const expense = {
            userId: data.userId,
            amount: data.amount,
            category: data.category,
            description: data.description
        };

        return this.expenseRepository.create(expense);
    }
}
```

This layer coordinates the operation.

It should not depend directly on Express or a specific database implementation.

## 4.3 Domain Layer

The Domain Layer contains business concepts and rules.

Examples:

- Expense.
- Order.
- Payment.
- Customer.
- Product.
- Business policies.

Example:

```typescript
// expense.ts

export class Expense {
    constructor(
        public readonly userId: number,
        public readonly amount: number,
        public readonly category: string
    ) {
        if (amount <= 0) {
            throw new Error(
                "Expense amount must be greater than zero"
            );
        }

        if (!category.trim()) {
            throw new Error(
                "Expense category is required"
            );
        }
    }
}
```

The domain object protects its business invariants.

A business invariant is a rule that must remain true for the domain object or operation to be valid.

## 4.4 Infrastructure Layer

The Infrastructure Layer contains implementation details.

Examples:

- MySQL repositories.
- MongoDB repositories.
- Redis clients.
- Email providers.
- Payment gateway integrations.
- File storage.
- External API clients.

Example:

```typescript
// mysql-expense.repository.ts

export class MySqlExpenseRepository
    implements ExpenseRepository {

    async create(data: CreateExpenseInput) {
        const [result] = await pool.execute(
            `INSERT INTO expenses
             (user_id, amount, category)
             VALUES (?, ?, ?)`,
            [
                data.userId,
                data.amount,
                data.category
            ]
        );

        return result;
    }
}
```

### Layer Responsibilities

| Layer | Responsibility |
|---|---|
| Presentation | HTTP requests and responses |
| Application | Use cases and workflow coordination |
| Domain | Business rules and domain models |
| Infrastructure | Database, cache and external integrations |

### Important Rule

In a well-designed layered architecture, the direction of dependencies should be deliberate.

For example, the domain should not depend on Express or MySQL.

---

# PART 3: CLEAN ARCHITECTURE

# 5. What Is Clean Architecture?

Clean Architecture is an architectural approach that organizes software around business rules while keeping external technologies separate.

It was popularized by Robert C. Martin.

Its central principle is:

**Business logic should not depend on external implementation details.**

External implementation details include:

- Web frameworks.
- Databases.
- User interfaces.
- Third-party APIs.
- Messaging systems.

### Clean Architecture Diagram

```text
              External World
                     |
          +----------+----------+
          |                     |
          v                     v
     Web Framework           Database
          |                     |
          v                     v
     Controllers            Repository
          |                     |
          +----------+----------+
                     |
                     v
             Application Layer
                     |
                     v
                Domain Layer
```

A more conceptual representation uses concentric layers:

```text
+---------------------------------------+
|            Frameworks                 |
|                                       |
|   +-------------------------------+   |
|   |       Interface Adapters      |   |
|   |                               |   |
|   |   +-----------------------+   |   |
|   |   |   Application Logic   |   |   |
|   |   |                       |   |   |
|   |   |   +---------------+   |   |   |
|   |   |   | Domain Rules  |   |   |   |
|   |   |   +---------------+   |   |   |
|   |   +-----------------------+   |   |
|   +-------------------------------+   |
+---------------------------------------+
```

The exact layer names and implementation can vary.

The critical idea is the dependency direction.

---

# 6. The Dependency Rule

The Dependency Rule states:

**Source-code dependencies should point inward, toward higher-level business policies.**

For example:

```text
Infrastructure
      |
      v
Application
      |
      v
Domain
```

The domain should not import the database driver.

Bad example:

```typescript
import mysql from "mysql2/promise";

export class ExpenseService {
    async createExpense(data: any) {
        const connection = await mysql.createConnection({
            host: "localhost"
        });

        // Business logic mixed with database access
    }
}
```

Why is this problematic?

The service directly depends on MySQL.

Changing the database may require modifying business logic.

Better approach:

```typescript
interface ExpenseRepository {
    create(data: CreateExpenseInput): Promise<Expense>;
}
```

The application layer depends on the interface.

An infrastructure class implements it.

```typescript
class MySqlExpenseRepository
    implements ExpenseRepository {

    async create(
        data: CreateExpenseInput
    ): Promise<Expense> {
        // MySQL implementation
    }
}
```

Now the application can use another implementation without changing its core business logic.

For example:

```text
ExpenseRepository
        |
        +------ MySQL Implementation
        |
        +------ MongoDB Implementation
        |
        +------ In-Memory Test Implementation
```

### Benefits

- Database independence.
- Easier testing.
- Reduced coupling.
- Better maintainability.
- Easier replacement of infrastructure.

---

# 7. Dependency Inversion Principle in Clean Architecture

The Dependency Inversion Principle states:

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
2. Abstractions should not depend on details. Details should depend on abstractions.

Example:

```text
              Expense Service
                     |
                     v
             Expense Repository
                  Interface
                     ^
                     |
             MySQL Repository
```

The service depends on the repository interface, not the MySQL implementation.

This is different from simply placing database code in another file.

Separating files does not automatically create proper architectural boundaries.

### When Should You Use Clean Architecture?

It is particularly useful when:

- Business rules are complex.
- The application has multiple infrastructure integrations.
- You expect long-term maintenance.
- You need strong testability.
- Multiple teams work on the system.

For a very small CRUD application, a simpler layered architecture may be sufficient.

---

# PART 4: HEXAGONAL ARCHITECTURE

# 8. What Is Hexagonal Architecture?

Hexagonal Architecture is also known as Ports and Adapters Architecture.

It was introduced by Alistair Cockburn.

The idea is to isolate the application's core business logic from external systems.

The application communicates with external systems through ports.

Adapters implement those ports.

### Architecture

```text
             External Systems
                    |
             +------+------+
             |             |
             v             v
         HTTP Adapter  CLI Adapter
             |             |
             v             v
         +---------------------+
         |                     |
         |   Application Core  |
         |                     |
         +---------------------+
             |             |
             v             v
       Database Adapter  Email Adapter
             |             |
             v             v
          Database      Email Service
```

The hexagon is conceptual. It does not mean an application must have exactly six components.

---

# 9. What Are Ports?

A Port is an interface that defines how the application communicates with something external.

Example:

```typescript
// ports/expense.repository.ts

export interface ExpenseRepository {
    create(
        expense: CreateExpenseInput
    ): Promise<Expense>;
}
```

This port describes what the application needs from a persistence system.

Another port:

```typescript
export interface EmailService {
    sendEmail(
        to: string,
        subject: string,
        body: string
    ): Promise<void>;
}
```

The application needs an email-sending capability but does not need to know which provider implements it.

---

# 10. What Are Adapters?

Adapters are implementations that connect external technologies to application ports.

Example:

```typescript
export class SendGridEmailAdapter
    implements EmailService {

    async sendEmail(
        to: string,
        subject: string,
        body: string
    ) {
        // Send email using SendGrid
    }
}
```

Another adapter could use Amazon SES.

```typescript
export class SesEmailAdapter
    implements EmailService {

    async sendEmail(
        to: string,
        subject: string,
        body: string
    ) {
        // Send email using Amazon SES
    }
}
```

Both implement the same port.

### Primary and Secondary Adapters

| Adapter Type | Purpose | Examples |
|---|---|---|
| Primary / Driving | Initiates application behavior | REST controller, CLI, GraphQL resolver |
| Secondary / Driven | Supports application behavior | Database repository, email provider, payment gateway |

### Example

```text
REST Controller
      |
      v
Create Expense Use Case
      |
      v
Expense Repository Port
      |
      v
MySQL Adapter
      |
      v
MySQL Database
```

### Benefits

- External systems can be replaced.
- Business logic is easier to test.
- Infrastructure is isolated.
- Multiple interfaces can use the same application logic.

---

# PART 5: ONION ARCHITECTURE

# 11. What Is Onion Architecture?

Onion Architecture organizes software into concentric layers around the domain model.

It emphasizes keeping the domain independent from infrastructure.

### Architecture

```text
+--------------------------------------+
|           Infrastructure             |
|                                      |
|   +------------------------------+   |
|   |        Application           |   |
|   |                              |   |
|   |   +----------------------+   |   |
|   |   |       Domain         |   |   |
|   |   |                      |   |   |
|   |   +----------------------+   |   |
|   +------------------------------+   |
+--------------------------------------+
```

### Typical Layers

1. Domain Model.
2. Domain Services and Interfaces.
3. Application Services.
4. Infrastructure and Presentation.

The domain is at the center.

Outer layers depend on inner layers.

### Clean vs Hexagonal vs Onion

| Feature | Clean Architecture | Hexagonal Architecture | Onion Architecture |
|---|---|---|---|
| Main Idea | Business rules independent of infrastructure | Ports and adapters | Domain at the center |
| Core Concept | Dependency Rule | Ports and Adapters | Concentric layers |
| Focus | Dependency direction | External integration boundaries | Domain independence |
| Common Use | Complex business applications | Systems with multiple external interfaces | Domain-heavy applications |

These approaches overlap considerably and can be combined.

---

# PART 6: DOMAIN-DRIVEN DESIGN (DDD)

# 12. What Is Domain-Driven Design?

Domain-Driven Design (DDD) is an approach to software development that focuses on modeling the business domain.

The term was introduced by Eric Evans.

A Domain is the business area that the software is designed to solve.

Examples:

| Application | Domain |
|---|---|
| Expense Tracker | Expense Management |
| E-commerce | Commerce |
| Banking App | Financial Services |
| Hospital Software | Healthcare Management |
| Booking App | Reservation Management |

DDD encourages developers to understand business concepts and express them clearly in the code.

---

# 13. Ubiquitous Language

Ubiquitous Language means using a shared vocabulary between developers, business stakeholders and domain experts.

Suppose you are building an e-commerce system.

Business stakeholders use terms such as:

- Customer.
- Cart.
- Order.
- Payment.
- Shipment.
- Refund.

Your code should use these concepts consistently.

Bad naming:

```typescript
class DataManager {
    processInfo() {}
}
```

Better naming:

```typescript
class OrderService {
    createOrder() {}

    cancelOrder() {}

    calculateOrderTotal() {}
}
```

The second example communicates business intent more clearly.

---

# 14. Entity

An Entity is an object that has a unique identity and remains distinguishable even when its attributes change.

Example: User.

```typescript
class User {
    constructor(
        public readonly id: number,
        public name: string,
        public email: string
    ) {}
}
```

Suppose:

```text
User ID: 101
Name: Riyaz
Email: riyaz@example.com
```

The user changes the name.

```text
User ID: 101
Name: Riyaz Khan
Email: riyaz@example.com
```

It is still the same user because its identity remains unchanged.

### Entity Characteristics

- Has an identity.
- Can change over time.
- Represents a business concept.
- Is commonly persisted.

---

# 15. Value Object

A Value Object is defined by its attributes rather than a unique identity.

Example: Money.

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
}
```

Two Money objects with the same amount and currency can be considered equivalent.

```typescript
const price1 = new Money(500, "INR");

const price2 = new Money(500, "INR");
```

Their equality is based on their values, not object identity.

### Entity vs Value Object

| Feature | Entity | Value Object |
|---|---|---|
| Identity | Has unique identity | No independent identity |
| Equality | Usually identity-based | Value-based |
| Mutability | May change | Often immutable |
| Example | User, Order | Money, Address |
| Lifecycle | Exists over time | Usually replaced when changed |

---

# 16. Aggregate

An Aggregate is a group of related domain objects treated as a consistency boundary.

It has an Aggregate Root that controls access to the aggregate.

Example:

```text
              Order
          Aggregate Root
                 |
       +---------+---------+
       |                   |
       v                   v
   Order Item 1        Order Item 2
```

The Order is the Aggregate Root.

External code should generally interact with Order rather than directly modifying its internal Order Items.

### Example

```typescript
class Order {
    private items: OrderItem[] = [];

    constructor(
        public readonly id: string
    ) {}

    addItem(item: OrderItem) {
        if (item.quantity <= 0) {
            throw new Error(
                "Quantity must be greater than zero"
            );
        }

        this.items.push(item);
    }

    getItems() {
        return [...this.items];
    }
}
```

The aggregate protects its internal rules.

### Why Use Aggregates?

- Maintain business invariants.
- Control object modification.
- Define transaction boundaries.
- Reduce uncontrolled coupling.

---

# 17. Repository in DDD

A Repository provides an abstraction for retrieving and storing domain objects.

Example:

```typescript
interface OrderRepository {
    findById(id: string): Promise<Order | null>;

    save(order: Order): Promise<void>;
}
```

Infrastructure implementation:

```typescript
class MySqlOrderRepository
    implements OrderRepository {

    async findById(id: string) {
        // Fetch order from MySQL
        return null;
    }

    async save(order: Order) {
        // Persist order in MySQL
    }
}
```

The domain and application layers depend on the abstraction.

They do not need to know the SQL implementation.

---

# 18. Bounded Context

A Bounded Context defines the boundary within which a particular domain model and language apply.

Consider an e-commerce application.

```text
              E-commerce System
                       |
          +------------+------------+
          |            |            |
          v            v            v
      Shopping      Payment      Shipping
       Context      Context       Context
```

The concept of a Customer may differ between contexts.

### Shopping Context

Customer means someone who browses and purchases products.

### Payment Context

Customer means an entity with payment credentials or billing information.

### Shipping Context

Customer means a recipient with a delivery address.

A Bounded Context helps avoid forcing one universal model across unrelated business responsibilities.

---

# 19. Domain Services vs Application Services

These are different concepts.

### Domain Service

Contains domain logic that does not naturally belong to a single Entity or Value Object.

Example:

```typescript
class DiscountPolicy {
    calculateDiscount(
        total: number,
        customerType: string
    ) {
        if (customerType === "premium") {
            return total * 0.10;
        }

        return 0;
    }
}
```

### Application Service

Coordinates a use case.

Example:

```typescript
class CreateOrderService {
    constructor(
        private orderRepository: OrderRepository,
        private discountPolicy: DiscountPolicy
    ) {}

    async execute(input: CreateOrderInput) {
        // Coordinate order creation
        // Apply relevant domain rules
        // Save order
    }
}
```

### Comparison

| Feature | Domain Service | Application Service |
|---|---|---|
| Focus | Business rules | Use-case coordination |
| Contains | Domain logic | Workflow orchestration |
| Depends On | Domain concepts | Domain abstractions and application ports |
| Example | Discount calculation | Create order workflow |

---

# PART 7: SOLID PRINCIPLES

# 20. What Are SOLID Principles?

SOLID is a collection of five object-oriented design principles.

They help developers create software that is:

- Maintainable.
- Flexible.
- Testable.
- Extensible.
- Less tightly coupled.

SOLID stands for:

| Letter | Principle |
|---|---|
| S | Single Responsibility Principle |
| O | Open/Closed Principle |
| L | Liskov Substitution Principle |
| I | Interface Segregation Principle |
| D | Dependency Inversion Principle |

Let's understand each principle.

---

# 21. Single Responsibility Principle (SRP)

A class should have one primary responsibility and one coherent reason to change.

### Bad Example

```typescript
class UserService {
    createUser() {}

    sendEmail() {}

    generatePDF() {}

    saveToDatabase() {}
}
```

This class handles multiple responsibilities.

Changes to email delivery, PDF generation and persistence all affect the same class.

### Better Example

```typescript
class UserService {
    createUser() {}
}

class EmailService {
    sendEmail() {}
}

class PdfService {
    generatePDF() {}
}

class UserRepository {
    saveToDatabase() {}
}
```

Each class has a clear responsibility.

### Important

SRP does not mean every method must be placed in its own class.

The goal is to group related responsibilities and avoid unrelated reasons for change.

---

# 22. Open/Closed Principle (OCP)

Software entities should be open for extension but closed for modification.

This means you should be able to introduce new behavior without repeatedly modifying stable existing code.

### Bad Example

```typescript
class PaymentService {
    processPayment(
        type: string,
        amount: number
    ) {
        if (type === "stripe") {
            // Stripe payment
        } else if (type === "paypal") {
            // PayPal payment
        } else if (type === "razorpay") {
            // Razorpay payment
        }
    }
}
```

Every new payment provider requires modifying the existing method.

### Better Example

```typescript
interface PaymentProvider {
    pay(amount: number): Promise<void>;
}
```

Implementations:

```typescript
class StripePayment implements PaymentProvider {
    async pay(amount: number) {
        // Stripe implementation
    }
}

class RazorpayPayment implements PaymentProvider {
    async pay(amount: number) {
        // Razorpay implementation
    }
}
```

Service:

```typescript
class PaymentService {
    constructor(
        private provider: PaymentProvider
    ) {}

    async process(amount: number) {
        await this.provider.pay(amount);
    }
}
```

Now new providers can be added without modifying PaymentService.

---

# 23. Liskov Substitution Principle (LSP)

Objects of a subtype should be usable wherever their base type is expected without breaking the expected behavior.

### Bad Example

```typescript
class Bird {
    fly() {
        console.log("Flying");
    }
}

class Penguin extends Bird {
    fly() {
        throw new Error("Penguins cannot fly");
    }
}
```

The Penguin violates the expected behavior of Bird because not all birds can fly.

### Better Example

```typescript
interface Bird {
    eat(): void;
}

interface FlyingBird extends Bird {
    fly(): void;
}

class Sparrow implements FlyingBird {
    eat() {}

    fly() {}
}

class Penguin implements Bird {
    eat() {}
}
```

Now the abstraction matches the actual capabilities.

### Main Idea

Inheritance should represent valid behavioral relationships, not just similarities in names.

---

# 24. Interface Segregation Principle (ISP)

Clients should not be forced to depend on methods they do not use.

### Bad Example

```typescript
interface Worker {
    work(): void;

    eat(): void;

    sleep(): void;
}
```

A Robot may work but does not eat or sleep.

### Better Example

```typescript
interface Workable {
    work(): void;
}

interface Eatable {
    eat(): void;
}

interface Sleepable {
    sleep(): void;
}
```

Human:

```typescript
class Human implements
    Workable,
    Eatable,
    Sleepable {

    work() {}

    eat() {}

    sleep() {}
}
```

Robot:

```typescript
class Robot implements Workable {
    work() {}
}
```

### Main Idea

Create small, focused interfaces rather than one large interface containing unrelated operations.

---

# 25. Dependency Inversion Principle (DIP)

High-level modules should depend on abstractions rather than concrete implementations.

### Bad Example

```typescript
class MySqlDatabase {
    connect() {}
}

class UserService {
    private database = new MySqlDatabase();

    createUser() {
        this.database.connect();
    }
}
```

UserService is tightly coupled to MySqlDatabase.

### Better Example

```typescript
interface Database {
    connect(): void;
}
```

Implementation:

```typescript
class MySqlDatabase implements Database {
    connect() {}
}
```

Service:

```typescript
class UserService {
    constructor(
        private database: Database
    ) {}

    createUser() {
        this.database.connect();
    }
}
```

Now the dependency is injected.

### SOLID Summary

| Principle | Main Idea |
|---|---|
| SRP | One cohesive responsibility |
| OCP | Extend behavior without repeatedly modifying stable code |
| LSP | Subtypes should preserve expected behavior |
| ISP | Use focused interfaces |
| DIP | Depend on abstractions |

---

# PART 8: DESIGN PATTERNS

# 26. What Are Design Patterns?

Design Patterns are reusable approaches to commonly occurring software design problems.

They are not ready-made code libraries.

They describe solutions that can be adapted to a particular application.

Three major categories:

1. Creational Patterns.
2. Structural Patterns.
3. Behavioral Patterns.

### Categories

| Category | Purpose | Examples |
|---|---|---|
| Creational | Object creation | Factory, Singleton |
| Structural | Object composition | Adapter, Decorator |
| Behavioral | Object interaction | Strategy, Observer |

---

# 27. Singleton Pattern

The Singleton Pattern ensures that a class has one shared instance within the relevant application context.

Example:

```typescript
class DatabaseConnection {
    private static instance: DatabaseConnection;

    private constructor() {}

    static getInstance() {
        if (!DatabaseConnection.instance) {
            DatabaseConnection.instance =
                new DatabaseConnection();
        }

        return DatabaseConnection.instance;
    }
}
```

Usage:

```typescript
const db1 = DatabaseConnection.getInstance();

const db2 = DatabaseConnection.getInstance();

console.log(db1 === db2); // true
```

### Common Use Cases

- Configuration managers.
- Shared application-level clients.
- Certain resource managers.

### Important Considerations

Singleton is not automatically the best choice for every shared resource.

It can create:

- Global state.
- Difficult tests.
- Hidden dependencies.
- Problems in multi-process deployments.

In Node.js, a module-level database pool is often sufficient for one process.

Each worker process or container may have its own instance.

---

# 28. Factory Pattern

The Factory Pattern centralizes object creation.

It hides the creation details from the calling code.

Example:

```typescript
interface Notification {
    send(message: string): void;
}
```

Implementations:

```typescript
class EmailNotification implements Notification {
    send(message: string) {
        console.log("Email:", message);
    }
}

class SmsNotification implements Notification {
    send(message: string) {
        console.log("SMS:", message);
    }
}
```

Factory:

```typescript
class NotificationFactory {
    static create(type: string): Notification {
        switch (type) {
            case "email":
                return new EmailNotification();

            case "sms":
                return new SmsNotification();

            default:
                throw new Error(
                    "Unsupported notification type"
                );
        }
    }
}
```

Usage:

```typescript
const notification =
    NotificationFactory.create("email");

notification.send("Welcome!");
```

### Benefits

- Centralized object creation.
- Reduced direct dependency on concrete classes.
- Easier addition of implementations.

---

# 29. Strategy Pattern

The Strategy Pattern allows selecting an algorithm or behavior at runtime.

Example: Discount calculation.

```typescript
interface DiscountStrategy {
    calculate(amount: number): number;
}
```

Implementations:

```typescript
class RegularDiscount implements DiscountStrategy {
    calculate(amount: number) {
        return amount * 0.05;
    }
}

class PremiumDiscount implements DiscountStrategy {
    calculate(amount: number) {
        return amount * 0.15;
    }
}
```

Context:

```typescript
class DiscountService {
    constructor(
        private strategy: DiscountStrategy
    ) {}

    calculate(amount: number) {
        return this.strategy.calculate(amount);
    }
}
```

Usage:

```typescript
const discountService = new DiscountService(
    new PremiumDiscount()
);

console.log(
    discountService.calculate(1000)
);
```

Output:

```text
150
```

### Use Cases

- Payment strategies.
- Pricing algorithms.
- Authentication methods.
- Shipping calculations.
- Tax calculations.

---

# 30. Observer Pattern

The Observer Pattern establishes a one-to-many relationship between objects.

When one object changes state, registered observers are notified.

Example:

```text
        Order Created
              |
              v
         Event Publisher
              |
       +------+------+ 
       |      |      |
       v      v      v
     Email  Analytics Inventory
```

Simple implementation:

```typescript
interface Observer {
    update(event: string): void;
}

class EmailObserver implements Observer {
    update(event: string) {
        console.log("Email received:", event);
    }
}

class AnalyticsObserver implements Observer {
    update(event: string) {
        console.log("Analytics received:", event);
    }
}
```

Publisher:

```typescript
class EventPublisher {
    private observers: Observer[] = [];

    subscribe(observer: Observer) {
        this.observers.push(observer);
    }

    publish(event: string) {
        for (const observer of this.observers) {
            observer.update(event);
        }
    }
}
```

Usage:

```typescript
const publisher = new EventPublisher();

publisher.subscribe(new EmailObserver());

publisher.subscribe(new AnalyticsObserver());

publisher.publish("ORDER_CREATED");
```

### Observer vs Event-Driven Architecture

The Observer Pattern often operates within one application process.

Event-Driven Architecture can operate across multiple independent services using a message broker.

---

# 31. Adapter Pattern

The Adapter Pattern allows incompatible interfaces to work together.

Example:

Your application expects:

```typescript
interface PaymentGateway {
    charge(amount: number): Promise<void>;
}
```

But a third-party provider exposes:

```typescript
class ExternalPaymentSDK {
    async makePayment(
        amountInPaise: number
    ) {
        // Provider implementation
    }
}
```

Adapter:

```typescript
class PaymentAdapter implements PaymentGateway {
    constructor(
        private sdk: ExternalPaymentSDK
    ) {}

    async charge(amount: number) {
        const amountInPaise = amount * 100;

        await this.sdk.makePayment(
            amountInPaise
        );
    }
}
```

Now your application uses a consistent interface.

### Use Cases

- Payment gateway integrations.
- Third-party API integrations.
- Legacy system integration.
- Different database clients.

---

# 32. Decorator Pattern

The Decorator Pattern adds behavior to an object without modifying its original implementation.

Example:

You have a basic notification service.

```typescript
interface NotificationService {
    send(message: string): void;
}

class BasicNotification implements NotificationService {
    send(message: string) {
        console.log(message);
    }
}
```

Decorator:

```typescript
class LoggingDecorator implements NotificationService {
    constructor(
        private notification: NotificationService
    ) {}

    send(message: string) {
        console.log("Sending notification");

        this.notification.send(message);

        console.log("Notification sent");
    }
}
```

Usage:

```typescript
const notification = new LoggingDecorator(
    new BasicNotification()
);

notification.send("Welcome!");
```

### Common Use Cases

- Logging.
- Metrics.
- Caching.
- Authorization wrappers.
- Request processing.

### Important

The Decorator Pattern is a design pattern.

Decorators in TypeScript are a language feature with their own semantics. The two concepts are related but are not identical.

---

# PART 9: DEPENDENCY INJECTION AND INVERSION OF CONTROL

# 33. What Is Dependency Injection?

Dependency Injection (DI) is a technique where an object receives the dependencies it needs from outside instead of creating them internally.

### Without Dependency Injection

```typescript
class UserService {
    private repository = new UserRepository();

    createUser() {
        return this.repository.create();
    }
}
```

UserService creates its own repository.

This creates tight coupling.

### With Dependency Injection

```typescript
class UserService {
    constructor(
        private repository: UserRepository
    ) {}

    createUser() {
        return this.repository.create();
    }
}
```

Now the dependency is supplied externally.

Usage:

```typescript
const repository = new UserRepository();

const userService = new UserService(repository);
```

### Benefits

- Easier unit testing.
- Reduced coupling.
- Flexible implementations.
- Better modularity.

---

# 34. What Is Inversion of Control?

Inversion of Control (IoC) is a broader design principle in which control over certain aspects of object creation or execution is transferred from a component to an external mechanism or framework.

Dependency Injection is one way to implement IoC.

### Without IoC

```text
UserService
    |
    v
Creates Repository
```

### With IoC

```text
Composition Root / Container
       |
       +------> Creates Repository
       |
       +------> Creates UserService
```

The application composition root wires the dependencies together.

### DI vs IoC

| Feature | IoC | DI |
|---|---|---|
| Meaning | Broad principle of reversing control | Technique for supplying dependencies |
| Scope | Architectural principle | Implementation technique |
| Example | Framework controls request lifecycle | Service receives repository through constructor |
| Relationship | Broader concept | One implementation of IoC |

---

# 35. Dependency Injection Types

There are three common forms.

## 35.1 Constructor Injection

Dependencies are supplied through the constructor.

```typescript
class UserService {
    constructor(
        private repository: UserRepository
    ) {}
}
```

This is generally the preferred approach for required dependencies.

## 35.2 Setter Injection

Dependencies are supplied through a setter method.

```typescript
class UserService {
    private repository!: UserRepository;

    setRepository(repository: UserRepository) {
        this.repository = repository;
    }
}
```

Useful when dependencies are optional or configured after construction.

## 35.3 Property Injection

Dependencies are assigned directly to properties, often through a framework or container.

```typescript
class UserService {
    repository!: UserRepository;
}
```

This approach can hide required dependencies and make testing less straightforward.

### Comparison

| Type | Injection Method | Typical Use |
|---|---|---|
| Constructor | Constructor parameter | Required dependencies |
| Setter | Setter method | Optional or replaceable dependencies |
| Property | Direct property assignment | Framework-managed cases |

---

# PART 10: REPOSITORY AND SERVICE LAYER PATTERNS

# 36. What Is the Repository Pattern?

The Repository Pattern provides an abstraction between business/application logic and data persistence.

It allows application logic to work with a collection-like interface without knowing the database implementation.

### Without Repository Pattern

```typescript
class UserService {
    async getUser(id: number) {
        return db.query(
            "SELECT * FROM users WHERE id = ?",
            [id]
        );
    }
}
```

The service contains database-specific logic.

### With Repository Pattern

Interface:

```typescript
interface UserRepository {
    findById(id: number): Promise<User | null>;

    create(user: CreateUserInput): Promise<User>;
}
```

Implementation:

```typescript
class MySqlUserRepository implements UserRepository {
    async findById(id: number) {
        // MySQL query
        return null;
    }

    async create(user: CreateUserInput) {
        // Insert user into MySQL
        return {} as User;
    }
}
```

Service:

```typescript
class UserService {
    constructor(
        private repository: UserRepository
    ) {}

    async getUser(id: number) {
        return this.repository.findById(id);
    }
}
```

### Benefits

- Database abstraction.
- Easier testing.
- Reusable persistence logic.
- Cleaner application services.

### Important

Do not create unnecessary repository layers that simply wrap every ORM method without adding meaningful abstraction.

A repository should express the application's data-access needs, not duplicate the entire ORM API.

---

# 37. What Is the Service Layer Pattern?

The Service Layer Pattern organizes application operations into service classes.

A service coordinates business workflows.

Example:

```typescript
class ExpenseService {
    constructor(
        private expenseRepository: ExpenseRepository
    ) {}

    async createExpense(input: CreateExpenseInput) {
        if (input.amount <= 0) {
            throw new Error(
                "Invalid expense amount"
            );
        }

        return this.expenseRepository.create(input);
    }

    async getUserExpenses(userId: number) {
        return this.expenseRepository.findByUserId(
            userId
        );
    }
}
```

### Controller vs Service vs Repository

```text
HTTP Request
     |
     v
Controller
     |
     v
Service
     |
     v
Repository
     |
     v
Database
```

| Component | Responsibility |
|---|---|
| Controller | HTTP request and response |
| Service | Application workflow and business coordination |
| Repository | Data access |
| Model / Entity | Domain data and rules |

---

# PART 11: DTO AND DATA MAPPING

# 38. What Is a DTO?

DTO stands for Data Transfer Object.

A DTO is an object used to transfer data between different parts of a system.

DTOs help define exactly what information should enter or leave an application boundary.

### Why Are DTOs Needed?

Suppose your database contains:

```json
{
  "id": 101,
  "name": "User A",
  "email": "user@example.com",
  "passwordHash": "hashed_password",
  "role": "admin",
  "createdAt": "2026-11-25T10:00:00Z"
}
```

You should not automatically return the entire database record to the client.

Instead, define a response DTO:

```typescript
interface UserResponseDTO {
    id: number;
    name: string;
    email: string;
}
```

Mapping:

```typescript
function toUserResponseDTO(user: User): UserResponseDTO {
    return {
        id: user.id,
        name: user.name,
        email: user.email
    };
}
```

Now sensitive internal fields are not exposed.

### DTO Types

Common DTOs include:

- CreateUserDTO.
- UpdateUserDTO.
- UserResponseDTO.
- CreateOrderDTO.
- OrderResponseDTO.

### DTO vs Entity

| Feature | DTO | Entity |
|---|---|---|
| Purpose | Transfer data | Represent domain identity |
| Business Rules | Usually minimal | May contain business rules |
| Identity | Not necessarily | Has identity |
| Structure | Designed for an interface | Designed for the domain |
| Example | UserResponseDTO | User Entity |

---

# 39. What Is Data Mapping?

Data Mapping converts data from one representation to another.

Example:

Database representation:

```json
{
  "user_id": 101,
  "user_name": "User A"
}
```

Domain representation:

```typescript
{
    id: 101,
    name: "User A"
}
```

Response representation:

```json
{
  "id": 101,
  "name": "User A"
}
```

A mapper can perform the transformation.

```typescript
class UserMapper {
    static toDomain(row: any): User {
        return new User(
            row.user_id,
            row.user_name
        );
    }

    static toResponse(user: User): UserResponseDTO {
        return {
            id: user.id,
            name: user.name
        };
    }
}
```

### Why Is Mapping Important?

- Prevents database structure from leaking into APIs.
- Separates internal models from external contracts.
- Makes schema changes easier to manage.
- Helps control sensitive data exposure.

---

# PART 12: USE CASES AND APPLICATION SERVICES

# 40. What Is a Use Case?

A Use Case represents a specific operation the application provides.

Examples:

- Register User.
- Login User.
- Create Expense.
- Update Expense.
- Delete Expense.
- Generate Monthly Report.
- Place Order.
- Process Payment.

A use case explains what the application does.

### Example

```typescript
interface ExpenseRepository {
    create(
        expense: CreateExpenseInput
    ): Promise<Expense>;
}
```

Use case:

```typescript
class CreateExpenseUseCase {
    constructor(
        private repository: ExpenseRepository
    ) {}

    async execute(input: CreateExpenseInput) {
        if (input.amount <= 0) {
            throw new Error(
                "Amount must be positive"
            );
        }

        return this.repository.create(input);
    }
}
```

Controller:

```typescript
class ExpenseController {
    constructor(
        private createExpenseUseCase: CreateExpenseUseCase
    ) {}

    async create(req: Request, res: Response) {
        const result =
            await this.createExpenseUseCase.execute(
                req.body
            );

        return res.status(201).json(result);
    }
}
```

### Why Use Use Cases?

- Each application operation has a clear boundary.
- Business workflows are easier to test.
- Controllers remain simple.
- Application behavior is easier to understand.

### Service vs Use Case

| Feature | Application Service | Use Case |
|---|---|---|
| Focus | Application operations | A specific application goal |
| Scope | May coordinate multiple operations | Usually one defined workflow |
| Naming | ExpenseService | CreateExpenseUseCase |
| Structure | Often groups related operations | Often one class/function per use case |

These terms overlap in practice. The important thing is that application behavior remains clear and well organized.

---

# PART 13: CQRS

# 41. What Is CQRS?

CQRS stands for Command Query Responsibility Segregation.

It separates operations that change application state from operations that retrieve application state.

There are two categories:

1. Commands.
2. Queries.

### Command

A Command requests a state change.

Examples:

- CreateExpense.
- UpdateExpense.
- DeleteExpense.
- PlaceOrder.

### Query

A Query retrieves data without changing application state.

Examples:

- GetExpenses.
- GetUserProfile.
- GetMonthlyReport.
- GetOrderDetails.

### Architecture

```text
              Client
                 |
                 v
             Application
                 |
          +------+------+
          |             |
          v             v
       Commands       Queries
          |             |
          v             v
     Write Model    Read Model
          |             |
          v             v
       Database     Read Database
```

### Simple Example

Command:

```typescript
class CreateExpenseCommand {
    constructor(
        public readonly userId: number,
        public readonly amount: number,
        public readonly category: string
    ) {}
}
```

Handler:

```typescript
class CreateExpenseHandler {
    constructor(
        private repository: ExpenseRepository
    ) {}

    async execute(command: CreateExpenseCommand) {
        return this.repository.create({
            userId: command.userId,
            amount: command.amount,
            category: command.category
        });
    }
}
```

Query:

```typescript
class GetExpensesQuery {
    constructor(
        public readonly userId: number
    ) {}
}
```

Query Handler:

```typescript
class GetExpensesHandler {
    constructor(
        private repository: ExpenseRepository
    ) {}

    async execute(query: GetExpensesQuery) {
        return this.repository.findByUserId(
            query.userId
        );
    }
}
```

### Why Use CQRS?

- Separates read and write responsibilities.
- Allows different read models.
- Can improve complex query performance.
- Helps organize complex business operations.

### Important

CQRS does not require:

- Microservices.
- Different databases.
- Event Sourcing.
- Message brokers.

You can implement CQRS within a single application and database.

---

# PART 14: EVENT SOURCING

# 42. What Is Event Sourcing?

Event Sourcing is an architectural pattern in which the application stores state changes as a sequence of events.

Instead of storing only the current state, it preserves the events that produced that state.

### Traditional Database Approach

Suppose a bank account has:

```text
Balance = ₹5,000
```

The database stores the current balance.

### Event Sourcing Approach

The system stores events:

```text
AccountOpened: ₹0
MoneyDeposited: ₹10,000
MoneyWithdrawn: ₹3,000
MoneyWithdrawn: ₹2,000
```

The current balance is calculated by replaying the events.

```text
0 + 10000 - 3000 - 2000 = 5000
```

### Architecture

```text
          Commands
              |
              v
        Command Handler
              |
              v
        Business Rules
              |
              v
         Event Store
              |
              v
       Event Stream
              |
       +------+------+
       |             |
       v             v
   Projections    Consumers
       |
       v
    Read Model
```

### Benefits

- Complete event history.
- Auditability.
- Ability to reconstruct state.
- Historical analysis.
- Event-driven integration.

### Disadvantages

- More complex architecture.
- Event versioning challenges.
- Event replay costs.
- Read-model synchronization.
- Eventual consistency concerns.

### Important

Event Sourcing is not simply logging database changes.

Events should represent meaningful business facts.

For example:

```text
OrderPlaced
PaymentReceived
OrderCancelled
```

These are domain events.

---

# 43. CQRS vs Event Sourcing

| Feature | CQRS | Event Sourcing |
|---|---|---|
| Main Purpose | Separate reads and writes | Store state changes as events |
| Read Model | May be separate | Commonly built through projections |
| Write Model | Commands | Events are the persisted source of truth |
| Required Together? | No | No |
| Complexity | Can be relatively low | Usually higher |

They can be used independently or together.

---

# PART 15: TRANSACTION MANAGEMENT

# 44. What Is a Database Transaction?

A Transaction is a group of database operations treated as one logical unit of work.

Example: Bank transfer.

Suppose:

```text
Account A = ₹10,000
Account B = ₹5,000
```

Transfer ₹2,000 from A to B.

Two operations are required:

1. Deduct ₹2,000 from A.
2. Add ₹2,000 to B.

Both operations must succeed together.

### Without a Transaction

```text
Deduct from A
      |
      v
Success
      |
      v
Add to B
      |
      v
Failure
```

Now money has been deducted but not credited.

This is an inconsistent state.

### With a Transaction

```text
BEGIN TRANSACTION

Deduct from A

Add to B

COMMIT
```

If an operation fails:

```text
ROLLBACK
```

The database reverses the uncommitted transaction.

---

# 45. ACID Properties

Database transactions are commonly described using ACID.

| Property | Meaning |
|---|---|
| Atomicity | All operations succeed or none are committed |
| Consistency | Valid database constraints remain satisfied |
| Isolation | Concurrent transactions are controlled according to the isolation level |
| Durability | Committed changes survive failures according to the database's durability guarantees |

### Example

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 2000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 2000
WHERE id = 2;

COMMIT;
```

If an error occurs:

```sql
ROLLBACK;
```

In production, you should also validate account existence, sufficient funds, transaction ownership and concurrency conditions.

---

# 46. Transaction Management in Node.js

Example using MySQL with `mysql2/promise`:

```typescript
import mysql from "mysql2/promise";

const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "password",
    database: "bank"
});

async function transferMoney(
    fromAccount: number,
    toAccount: number,
    amount: number
) {
    const connection = await pool.getConnection();

    try {
        await connection.beginTransaction();

        const [senderRows]: any =
            await connection.execute(
                `SELECT balance
                 FROM accounts
                 WHERE id = ?
                 FOR UPDATE`,
                [fromAccount]
            );

        if (!senderRows.length) {
            throw new Error("Sender account not found");
        }

        if (senderRows[0].balance < amount) {
            throw new Error("Insufficient balance");
        }

        await connection.execute(
            `UPDATE accounts
             SET balance = balance - ?
             WHERE id = ?`,
            [amount, fromAccount]
        );

        await connection.execute(
            `UPDATE accounts
             SET balance = balance + ?
             WHERE id = ?`,
            [amount, toAccount]
        );

        await connection.commit();

        return {
            success: true
        };

    } catch (error) {
        await connection.rollback();

        throw error;

    } finally {
        connection.release();
    }
}
```

### Important Details

- `beginTransaction()` starts the transaction.
- `FOR UPDATE` locks selected rows for the transaction in MySQL's supported transactional storage engine.
- `commit()` makes the changes permanent.
- `rollback()` cancels uncommitted changes.
- `release()` returns the connection to the pool.

This example is simplified. A production transfer implementation must also validate the recipient, amount, account status, currency and concurrency requirements.

---

# PART 16: UNIT OF WORK PATTERN

# 47. What Is the Unit of Work Pattern?

The Unit of Work Pattern tracks changes made during a business operation and coordinates their persistence as one unit.

It is commonly used with repositories and database transactions.

### Without Unit of Work

```text
Service
   |
   +---- Save Order
   |
   +---- Save Order Items
   |
   +---- Update Inventory
```

If one operation fails, other changes may already have been committed.

### With Unit of Work

```text
          Service
             |
             v
         Unit of Work
             |
      +------+------+ 
      |      |      |
      v      v      v
    Order  Items  Inventory
      |      |      |
      +------+------+ 
             |
             v
          Commit
```

The Unit of Work coordinates the persistence operations.

### Example Interface

```typescript
interface UnitOfWork {
    begin(): Promise<void>;

    commit(): Promise<void>;

    rollback(): Promise<void>;
}
```

Implementation:

```typescript
class MySqlUnitOfWork implements UnitOfWork {
    constructor(
        private connection: any
    ) {}

    async begin() {
        await this.connection.beginTransaction();
    }

    async commit() {
        await this.connection.commit();
    }

    async rollback() {
        await this.connection.rollback();
    }
}
```

### Unit of Work vs Repository

| Feature | Repository | Unit of Work |
|---|---|---|
| Purpose | Data access abstraction | Coordinate persistence changes |
| Responsibility | Retrieve and save objects | Manage a unit of work |
| Example | OrderRepository | Transaction coordinator |
| Scope | Data collection or aggregate | Business operation |

A Unit of Work is especially useful when multiple repositories must participate in the same database transaction.

It does not automatically create an atomic transaction across multiple independent databases.

---

# PART 17: MODULAR BACKEND ARCHITECTURE

# 48. What Is Modular Architecture?

Modular Architecture divides an application into independent logical modules.

Each module owns a specific responsibility.

Example:

```text
Expense Tracker
      |
      +---- Auth Module
      |
      +---- User Module
      |
      +---- Expense Module
      |
      +---- Category Module
      |
      +---- Report Module
```

### Benefits

- Better code organization.
- Clear ownership.
- Reduced coupling.
- Easier testing.
- Easier feature development.

### Example Project Structure

```text
src/
│
├── modules/
│   │
│   ├── auth/
│   │   ├── auth.routes.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   └── auth.validation.ts
│   │
│   ├── users/
│   │   ├── user.routes.ts
│   │   ├── user.controller.ts
│   │   ├── user.service.ts
│   │   ├── user.repository.ts
│   │   ├── user.model.ts
│   │   └── user.validation.ts
│   │
│   ├── expenses/
│   │   ├── expense.routes.ts
│   │   ├── expense.controller.ts
│   │   ├── expense.service.ts
│   │   ├── expense.repository.ts
│   │   ├── expense.model.ts
│   │   └── expense.validation.ts
│   │
│   └── reports/
│       ├── report.routes.ts
│       ├── report.controller.ts
│       └── report.service.ts
│
├── infrastructure/
│   ├── database/
│   ├── redis/
│   ├── messaging/
│   └── email/
│
├── shared/
│   ├── errors/
│   ├── utils/
│   └── types/
│
├── middleware/
│
├── config/
│
├── app.ts
└── server.ts
```

This is a practical modular backend structure.

It can be adapted to Layered Architecture or Clean Architecture.

---

# 49. Feature-Based vs Layer-Based Project Structure

### Layer-Based Structure

```text
src/
    controllers/
    services/
    repositories/
    models/
    routes/
```

All controllers are grouped together, all services together, and so on.

### Feature-Based Structure

```text
src/
    modules/
        users/
        expenses/
        reports/
```

Each feature contains its related components.

### Comparison

| Feature | Layer-Based | Feature-Based |
|---|---|---|
| Organization | By technical layer | By business feature |
| Related Files | May be spread across folders | Usually grouped together |
| Small Projects | Often simple | Also suitable |
| Large Projects | Can become difficult to navigate | Often easier to maintain |
| Feature Ownership | Less localized | More localized |

For larger backend applications, feature-based modular organization is often easier to manage.

---

# PART 18: TESTING ARCHITECTURE

# 50. Why Is Testability Important?

A well-designed application should allow you to test business logic without requiring every external dependency.

For example, testing an ExpenseService should not always require:

- A running MySQL server.
- Redis.
- An external payment gateway.
- An HTTP server.

Dependency Injection and abstractions make isolated tests possible.

---

# 51. Unit Testing

Unit Testing verifies a small unit of code in isolation.

Example:

```typescript
function calculateTotal(
    amount: number,
    tax: number
) {
    return amount + tax;
}
```

Test:

```typescript
describe("calculateTotal", () => {
    it("should calculate the total", () => {
        expect(
            calculateTotal(100, 10)
        ).toBe(110);
    });
});
```

### Purpose

- Validate individual functions.
- Verify business rules.
- Detect regressions.
- Test edge cases.

---

# 52. Integration Testing

Integration Testing verifies that multiple components work together.

Examples:

- Service + Repository.
- Repository + MySQL.
- API + Database.
- Application + Redis.

Example:

```text
Test
 |
 v
Expense Service
 |
 v
MySQL Repository
 |
 v
Test Database
```

It checks whether the components interact correctly.

---

# 53. End-to-End Testing

End-to-End Testing verifies a complete user workflow.

Example:

```text
User Opens Application
        |
        v
Login
        |
        v
Create Expense
        |
        v
View Expense
        |
        v
Update Expense
        |
        v
Delete Expense
```

E2E tests verify the complete flow from the user's perspective.

### Unit vs Integration vs E2E

| Type | Scope | Example |
|---|---|---|
| Unit | Individual unit | Calculate expense total |
| Integration | Multiple components | Expense service with MySQL |
| E2E | Complete workflow | Login and create expense |

---

# 54. Mocking and Dependency Injection

Suppose you want to test an ExpenseService.

Instead of connecting to MySQL, create a mock repository.

```typescript
const mockRepository = {
    create: async (data: any) => ({
        id: 1,
        ...data
    })
};
```

Use it:

```typescript
const expenseService = new ExpenseService(
    mockRepository as any
);
```

Now the service can be tested without a real database.

This demonstrates why dependency inversion and dependency injection improve testability.

---

# PART 19: COMPLETE PRODUCTION-READY EXPRESS ARCHITECTURE

# 55. Project Overview

Let's combine the concepts into a practical Node.js + Express + TypeScript backend.

Project:

```text
Expense Tracker API
```

Features:

- User registration.
- User login.
- Create expense.
- Retrieve expenses.
- Update expense.
- Delete expense.
- Monthly reports.

Technology stack:

| Layer | Technology |
|---|---|
| Runtime | Node.js |
| Language | TypeScript |
| Framework | Express.js |
| Database | MySQL |
| ORM | Sequelize or Prisma |
| Cache | Redis |
| Validation | Zod |
| Testing | Jest |
| Logging | Pino |
| Authentication | JWT or secure sessions |

---

# 56. Recommended Project Structure

```text
expense-tracker-api/
│
├── src/
│   │
│   ├── application/
│   │   ├── use-cases/
│   │   │   ├── create-expense.usecase.ts
│   │   │   ├── update-expense.usecase.ts
│   │   │   └── delete-expense.usecase.ts
│   │   │
│   │   └── dto/
│   │       ├── create-expense.dto.ts
│   │       └── expense-response.dto.ts
│   │
│   ├── domain/
│   │   ├── entities/
│   │   │   └── expense.entity.ts
│   │   │
│   │   ├── repositories/
│   │   │   └── expense.repository.ts
│   │   │
│   │   └── errors/
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   │   └── mysql.connection.ts
│   │   │
│   │   ├── repositories/
│   │   │   └── mysql-expense.repository.ts
│   │   │
│   │   └── cache/
│   │
│   ├── presentation/
│   │   ├── controllers/
│   │   │   └── expense.controller.ts
│   │   │
│   │   ├── routes/
│   │   │   └── expense.routes.ts
│   │   │
│   │   └── middleware/
│   │
│   ├── config/
│   │   └── env.ts
│   │
│   ├── shared/
│   │   ├── errors/
│   │   └── utils/
│   │
│   ├── container.ts
│   ├── app.ts
│   └── server.ts
│
├── tests/
│
├── .env
├── .env.example
├── package.json
└── tsconfig.json
```

This structure follows Clean Architecture-inspired dependency boundaries.

It is not the only correct structure.

---

# 57. Step 1 – Define the Domain Entity

File:

```text
src/domain/entities/expense.entity.ts
```

```typescript
export class Expense {
    constructor(
        public readonly id: number | null,
        public readonly userId: number,
        public readonly amount: number,
        public readonly category: string,
        public readonly description: string
    ) {
        if (amount <= 0) {
            throw new Error(
                "Expense amount must be greater than zero"
            );
        }

        if (!category.trim()) {
            throw new Error(
                "Category is required"
            );
        }
    }
}
```

This entity contains business validation.

It does not know about Express, Sequelize or MySQL.

---

# 58. Step 2 – Define the Repository Interface

File:

```text
src/domain/repositories/expense.repository.ts
```

```typescript
import { Expense } from "../entities/expense.entity";

export interface ExpenseRepository {
    create(expense: Expense): Promise<Expense>;

    findById(id: number): Promise<Expense | null>;

    findByUserId(userId: number): Promise<Expense[]>;

    update(expense: Expense): Promise<Expense>;

    delete(id: number): Promise<void>;
}
```

This is the contract that infrastructure must implement.

---

# 59. Step 3 – Define the Use Case

File:

```text
src/application/use-cases/create-expense.usecase.ts
```

```typescript
import { Expense } from "../../domain/entities/expense.entity";

import { ExpenseRepository } from "../../domain/repositories/expense.repository";

interface CreateExpenseInput {
    userId: number;
    amount: number;
    category: string;
    description: string;
}

export class CreateExpenseUseCase {
    constructor(
        private expenseRepository: ExpenseRepository
    ) {}

    async execute(input: CreateExpenseInput) {
        const expense = new Expense(
            null,
            input.userId,
            input.amount,
            input.category,
            input.description
        );

        return this.expenseRepository.create(expense);
    }
}
```

The use case coordinates the operation.

It depends on the repository interface.

---

# 60. Step 4 – Implement the MySQL Repository

File:

```text
src/infrastructure/repositories/mysql-expense.repository.ts
```

```typescript
import { Pool } from "mysql2/promise";

import { Expense } from "../../domain/entities/expense.entity";

import { ExpenseRepository } from "../../domain/repositories/expense.repository";

export class MySqlExpenseRepository
    implements ExpenseRepository {

    constructor(
        private pool: Pool
    ) {}

    async create(expense: Expense): Promise<Expense> {
        const [result]: any = await this.pool.execute(
            `INSERT INTO expenses
             (user_id, amount, category, description)
             VALUES (?, ?, ?, ?)`,
            [
                expense.userId,
                expense.amount,
                expense.category,
                expense.description
            ]
        );

        return new Expense(
            result.insertId,
            expense.userId,
            expense.amount,
            expense.category,
            expense.description
        );
    }

    async findById(id: number): Promise<Expense | null> {
        const [rows]: any = await this.pool.execute(
            `SELECT * FROM expenses WHERE id = ?`,
            [id]
        );

        if (!rows.length) {
            return null;
        }

        const row = rows[0];

        return new Expense(
            row.id,
            row.user_id,
            Number(row.amount),
            row.category,
            row.description
        );
    }

    async findByUserId(userId: number): Promise<Expense[]> {
        const [rows]: any = await this.pool.execute(
            `SELECT * FROM expenses WHERE user_id = ?`,
            [userId]
        );

        return rows.map((row: any) =>
            new Expense(
                row.id,
                row.user_id,
                Number(row.amount),
                row.category,
                row.description
            )
        );
    }

    async update(expense: Expense): Promise<Expense> {
        if (expense.id === null) {
            throw new Error("Expense ID is required");
        }

        await this.pool.execute(
            `UPDATE expenses
             SET amount = ?, category = ?, description = ?
             WHERE id = ?`,
            [
                expense.amount,
                expense.category,
                expense.description,
                expense.id
            ]
        );

        return expense;
    }

    async delete(id: number): Promise<void> {
        await this.pool.execute(
            `DELETE FROM expenses WHERE id = ?`,
            [id]
        );
    }
}
```

This class contains MySQL-specific implementation details.

---

# 61. Step 5 – Create the Controller

File:

```text
src/presentation/controllers/expense.controller.ts
```

```typescript
import { Request, Response, NextFunction } from "express";

import { CreateExpenseUseCase } from "../../application/use-cases/create-expense.usecase";

export class ExpenseController {
    constructor(
        private createExpenseUseCase: CreateExpenseUseCase
    ) {}

    create = async (
        req: Request,
        res: Response,
        next: NextFunction
    ) => {
        try {
            const result =
                await this.createExpenseUseCase.execute({
                    userId: req.body.userId,
                    amount: req.body.amount,
                    category: req.body.category,
                    description: req.body.description
                });

            return res.status(201).json({
                success: true,
                data: result
            });

        } catch (error) {
            next(error);
        }
    };
}
```

In a production application, `userId` should normally come from authenticated server-side identity, not be trusted from an arbitrary request body.

---

# 62. Step 6 – Dependency Injection

File:

```text
src/container.ts
```

```typescript
import { pool } from "./infrastructure/database/mysql.connection";

import { MySqlExpenseRepository } from "./infrastructure/repositories/mysql-expense.repository";

import { CreateExpenseUseCase } from "./application/use-cases/create-expense.usecase";

import { ExpenseController } from "./presentation/controllers/expense.controller";

const expenseRepository =
    new MySqlExpenseRepository(pool);

const createExpenseUseCase =
    new CreateExpenseUseCase(expenseRepository);

export const expenseController =
    new ExpenseController(createExpenseUseCase);
```

This file acts as the composition root.

It creates concrete implementations and connects them to application components.

---

# 63. Step 7 – Create Routes

File:

```text
src/presentation/routes/expense.routes.ts
```

```typescript
import { Router } from "express";

import { expenseController } from "../../container";

const router = Router();

router.post(
    "/",
    expenseController.create
);

export default router;
```

---

# 64. Step 8 – Configure Express

File:

```text
src/app.ts
```

```typescript
import express from "express";

import expenseRoutes from "./presentation/routes/expense.routes";

const app = express();

app.use(express.json());

app.use("/api/expenses", expenseRoutes);

export default app;
```

File:

```text
src/server.ts
```

```typescript
import app from "./app";

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
    console.log(
        `Server running on port ${PORT}`
    );
});
```

---

# 65. Complete Request Flow

Now understand how all the components work together.

```text
             Client
                |
                v
          HTTP POST Request
                |
                v
          Expense Routes
                |
                v
         Expense Controller
                |
                v
       CreateExpenseUseCase
                |
                v
          Expense Entity
                |
                v
       ExpenseRepository Interface
                |
                v
       MySqlExpenseRepository
                |
                v
            MySQL DB
                |
                v
         Return Created Expense
```

### Important Architectural Observation

The dependency direction and runtime execution direction are not necessarily identical.

For example:

- The controller calls the use case.
- The use case calls the repository interface.
- The concrete MySQL repository implements that interface.

The runtime request travels toward the database, while the source-code dependency rule keeps the application and domain independent of infrastructure details.

---

# PART 20: COMMON ARCHITECTURAL MISTAKES

# 66. Mistakes to Avoid

| Mistake | Problem | Better Approach |
|---|---|---|
| Business logic inside controllers | Difficult to test and reuse | Move business workflows into application services or use cases |
| Database queries everywhere | Tight coupling | Use repositories or suitable persistence boundaries |
| One giant service class | Too many responsibilities | Split by cohesive business capability |
| Overusing interfaces | Unnecessary complexity | Introduce abstractions where they provide value |
| Using design patterns everywhere | Overengineering | Apply patterns to actual recurring problems |
| Ignoring domain rules | Invalid application state | Protect invariants in domain models |
| Exposing database entities directly | Data leakage and unstable API contracts | Use response DTOs |
| Circular dependencies | Difficult initialization and testing | Redesign module boundaries |
| Global mutable state | Hidden dependencies | Prefer explicit dependency injection |
| Mixing HTTP and domain logic | Framework coupling | Keep domain independent of HTTP |
| Creating unnecessary microservices | Operational complexity | Use modular architecture where appropriate |
| No transaction boundaries | Inconsistent data | Use transactions for related database changes |
| Treating distributed operations as atomic | Incorrect failure assumptions | Consider Saga and compensation |
| Excessive abstraction | Harder code navigation | Keep architecture proportional to complexity |

---

# PART 21: INTERVIEW QUESTIONS AND ANSWERS

# 67. Basic Interview Questions

### Q1. What is Software Architecture?

Software Architecture defines the structure of an application, including components, responsibilities, communication and dependencies.

### Q2. What is Layered Architecture?

It divides an application into layers such as presentation, application, domain and infrastructure.

### Q3. What is Clean Architecture?

Clean Architecture organizes software so that business rules remain independent of external technologies.

### Q4. What is the Dependency Rule?

Source-code dependencies should point inward toward higher-level business policies.

### Q5. What is Hexagonal Architecture?

It is an architectural approach that isolates application logic using ports and adapters.

### Q6. What is Onion Architecture?

It is an architecture that places the domain model at the center and organizes dependencies in concentric layers.

### Q7. What is Domain-Driven Design?

DDD is an approach that models software around business concepts and domain rules.

### Q8. What is an Entity?

An Entity is a domain object with a unique identity.

### Q9. What is a Value Object?

A Value Object is defined by its attributes rather than a unique identity.

### Q10. What is an Aggregate Root?

An Aggregate Root is the main entry point for modifying an aggregate and protecting its invariants.

---

# 68. SOLID Interview Questions

### Q11. What is SRP?

A class should have one cohesive responsibility and one primary reason to change.

### Q12. What is OCP?

Software should allow new behavior to be added without repeatedly modifying stable existing code.

### Q13. What is LSP?

Subtypes should preserve the behavioral expectations of their base abstractions.

### Q14. What is ISP?

Clients should not depend on methods they do not need.

### Q15. What is DIP?

High-level and low-level modules should depend on abstractions rather than concrete implementations.

---

# 69. Design Pattern Interview Questions

### Q16. What is a Design Pattern?

A design pattern is a reusable approach to a commonly occurring software design problem.

### Q17. What is the Singleton Pattern?

It provides one shared instance within a defined application context.

### Q18. What is the Factory Pattern?

It centralizes object creation and hides construction details.

### Q19. What is the Strategy Pattern?

It allows different algorithms or behaviors to be selected through a common interface.

### Q20. What is the Observer Pattern?

It allows multiple observers to receive notifications when a subject publishes an update.

### Q21. What is the Adapter Pattern?

It allows incompatible interfaces to work together.

### Q22. What is the Decorator Pattern?

It adds behavior to an object without changing its original implementation.

---

# 70. Advanced Interview Questions

### Q23. What is Dependency Injection?

Dependency Injection is a technique where dependencies are supplied externally rather than created internally by a class.

### Q24. What is Inversion of Control?

IoC is a principle where control over object creation or execution is transferred to an external mechanism.

### Q25. What is the Repository Pattern?

It abstracts data persistence behind an interface.

### Q26. What is the Service Layer Pattern?

It organizes application operations into services that coordinate workflows.

### Q27. What is a DTO?

A DTO is an object designed to transfer data across application boundaries.

### Q28. What is CQRS?

CQRS separates operations that change state from operations that retrieve state.

### Q29. What is Event Sourcing?

Event Sourcing stores application state changes as a sequence of events.

### Q30. What is the Unit of Work Pattern?

It coordinates multiple persistence operations as one logical unit of work.

---

# 71. Scenario-Based Interview Questions

### Q31. Your controller contains 500 lines of code. What would you do?

I would separate HTTP handling from business logic and move application workflows into services or use cases. Persistence operations would be handled through appropriate repository abstractions.

### Q32. Your application uses MySQL, but you may migrate to MongoDB. How would you prepare?

I would keep persistence details behind repository interfaces and avoid leaking database-specific types into the domain and application layers.

However, changing databases may still require redesigning queries and data models because relational and document databases have different capabilities.

### Q33. How would you test a service without connecting to a database?

I would inject a repository interface and provide a mock or in-memory implementation during testing.

### Q34. Your application has multiple payment providers. Which design pattern would you use?

I would consider the Strategy Pattern with a common PaymentProvider interface. A Factory can select the appropriate implementation.

### Q35. How would you prevent sensitive database fields from reaching the frontend?

I would use response DTOs or explicit mapping functions to expose only approved fields.

### Q36. How would you handle a bank transfer involving two account updates?

I would use a database transaction with appropriate row locking and validation to ensure both changes commit together.

### Q37. When should you use CQRS?

I would consider CQRS when read and write responsibilities have different complexity, performance or scaling requirements.

### Q38. When should you use Event Sourcing?

I would consider it when maintaining a complete business event history, auditability or reconstructable state is a major requirement and the additional complexity is justified.

### Q39. What is the difference between Clean Architecture and Layered Architecture?

Layered Architecture organizes software into logical layers. Clean Architecture adds a strong emphasis on dependency direction and keeping business rules independent of infrastructure.

### Q40. Should every Node.js application use Clean Architecture?

No. The architecture should match the application's complexity, business requirements, team size and expected lifetime. A small CRUD application may work well with a simpler structure.

---

# PART 22: PRACTICAL ASSIGNMENTS

# 72. Assignment 1 – Implement Layered Architecture

Create a Node.js application with:

```text
controllers/
services/
repositories/
models/
routes/
```

Implement:

- User registration.
- User login.
- User retrieval.
- User updates.
- User deletion.

**Expected outcome:** Understand separation of concerns.

---

# 73. Assignment 2 – Implement Clean Architecture

Create an Expense Tracker API using:

```text
domain/
application/
infrastructure/
presentation/
```

Requirements:

- Create expense.
- Update expense.
- Delete expense.
- Retrieve expenses.
- Generate monthly report.

**Expected outcome:** Understand dependency direction.

---

# 74. Assignment 3 – Implement Hexagonal Architecture

Create a notification system.

Requirements:

- Email adapter.
- SMS adapter.
- Push notification adapter.
- Notification port.
- Application service.

The application core should not depend directly on a particular provider.

**Expected outcome:** Understand ports and adapters.

---

# 75. Assignment 4 – Apply SOLID Principles

Create a payment module.

Requirements:

- Stripe.
- Razorpay.
- PayPal.
- Common payment interface.
- Payment service.

Apply all five SOLID principles where appropriate.

**Expected outcome:** Understand maintainable object-oriented design.

---

# 76. Assignment 5 – Implement Design Patterns

Create examples of:

1. Singleton.
2. Factory.
3. Strategy.
4. Observer.
5. Adapter.
6. Decorator.

Use practical Node.js examples.

**Expected outcome:** Understand where and why design patterns are useful.

---

# 77. Assignment 6 – Implement Dependency Injection

Create:

```text
UserService
UserRepository
MySqlUserRepository
MongoUserRepository
```

The UserService should depend on an interface rather than a specific database implementation.

**Expected outcome:** Understand dependency inversion.

---

# 78. Assignment 7 – Implement CQRS

Create an Expense Tracker API.

Commands:

- Create Expense.
- Update Expense.
- Delete Expense.

Queries:

- Get Expense.
- Get All Expenses.
- Get Monthly Report.

Keep command and query responsibilities separate.

**Expected outcome:** Understand CQRS.

---

# 79. Assignment 8 – Implement Database Transactions

Create a banking module.

Requirements:

- Create accounts.
- Deposit money.
- Withdraw money.
- Transfer money.
- Rollback failed transactions.

**Expected outcome:** Understand ACID and transaction management.

---

# 80. Assignment 9 – Implement Unit of Work

Create an order-management module.

When an order is placed:

1. Create order.
2. Save order items.
3. Update inventory.
4. Commit the transaction.

If an operation fails, rollback the database transaction.

**Expected outcome:** Understand coordinated persistence.

---

# 81. Assignment 10 – Complete Backend Architecture Project

Build a production-oriented Expense Tracker API.

### Requirements

- Node.js.
- Express.js.
- TypeScript.
- MySQL.
- Redis.
- Authentication.
- Authorization.
- Clean Architecture.
- SOLID.
- Repository Pattern.
- Service Layer.
- DTOs.
- Dependency Injection.
- Unit Testing.
- Integration Testing.
- Database Transactions.
- Centralized Error Handling.
- Structured Logging.
- API Documentation.
- Docker Deployment.

**Expected outcome:** Build a maintainable backend application using professional architecture principles.

---

# PART 23: QUICK REVISION

# 82. Architecture Concepts

| Concept | Meaning |
|---|---|
| Layered Architecture | Organizes software into logical layers |
| Clean Architecture | Keeps business rules independent of infrastructure |
| Hexagonal Architecture | Uses ports and adapters |
| Onion Architecture | Places domain logic at the center |
| DDD | Models software around business domains |
| Ubiquitous Language | Shared terminology between business and developers |
| Entity | Object with unique identity |
| Value Object | Object defined by its values |
| Aggregate | Group of domain objects with a consistency boundary |
| Aggregate Root | Main entry point for modifying an aggregate |
| Bounded Context | Boundary around a domain model |
| Dependency Injection | Supplies dependencies externally |
| Inversion of Control | Transfers control to an external mechanism |

---

# 83. SOLID Principles

| Principle | Meaning |
|---|---|
| SRP | One cohesive responsibility |
| OCP | Open for extension, closed for modification |
| LSP | Subtypes preserve expected behavior |
| ISP | Focused interfaces |
| DIP | Depend on abstractions |

---

# 84. Design Patterns

| Pattern | Category | Purpose |
|---|---|---|
| Singleton | Creational | Shared instance |
| Factory | Creational | Centralized object creation |
| Strategy | Behavioral | Selectable algorithms |
| Observer | Behavioral | Notify multiple observers |
| Adapter | Structural | Connect incompatible interfaces |
| Decorator | Structural | Add behavior dynamically |

---

# 85. Backend Architecture Patterns

| Pattern | Purpose |
|---|---|
| Repository | Abstract data access |
| Service Layer | Coordinate application operations |
| DTO | Transfer data |
| Data Mapper | Convert between representations |
| Use Case | Represent a specific application operation |
| CQRS | Separate commands and queries |
| Event Sourcing | Persist business events |
| Unit of Work | Coordinate persistence operations |

---

# 86. Phase 50 Completion Checklist

### Architecture

- [ ] Software Architecture Fundamentals.
- [ ] Layered Architecture.
- [ ] Clean Architecture.
- [ ] Dependency Rule.
- [ ] Hexagonal Architecture.
- [ ] Ports and Adapters.
- [ ] Onion Architecture.

### Domain-Driven Design

- [ ] Domain.
- [ ] Ubiquitous Language.
- [ ] Entity.
- [ ] Value Object.
- [ ] Aggregate.
- [ ] Aggregate Root.
- [ ] Repository.
- [ ] Bounded Context.
- [ ] Domain Services.
- [ ] Application Services.

### SOLID

- [ ] Single Responsibility Principle.
- [ ] Open/Closed Principle.
- [ ] Liskov Substitution Principle.
- [ ] Interface Segregation Principle.
- [ ] Dependency Inversion Principle.

### Design Patterns

- [ ] Singleton.
- [ ] Factory.
- [ ] Strategy.
- [ ] Observer.
- [ ] Adapter.
- [ ] Decorator.

### Dependency Management

- [ ] Dependency Injection.
- [ ] Inversion of Control.
- [ ] Constructor Injection.
- [ ] Setter Injection.
- [ ] Property Injection.

### Backend Patterns

- [ ] Repository Pattern.
- [ ] Service Layer Pattern.
- [ ] DTO.
- [ ] Data Mapping.
- [ ] Use Cases.
- [ ] CQRS.
- [ ] Event Sourcing.
- [ ] Unit of Work.

### Transactions and Testing

- [ ] Database Transactions.
- [ ] ACID Properties.
- [ ] Transaction Management in Node.js.
- [ ] Unit Testing.
- [ ] Integration Testing.
- [ ] End-to-End Testing.
- [ ] Mocking.

### Practical Implementation

- [ ] Layered Node.js Application.
- [ ] Clean Architecture Application.
- [ ] Modular Express.js Application.
- [ ] Dependency Injection.
- [ ] Repository Implementation.
- [ ] Complete Backend Project.

---

# 87. Recommended Learning Resources

| Topic | Resource |
|---|---|
| Clean Architecture | [Clean Coder Blog – Robert C. Martin](https://blog.cleancoder.com/) |
| Domain-Driven Design | [DDD Community](https://www.dddcommunity.org/) |
| SOLID Principles | [Microsoft Learn – Object-Oriented Programming](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/) |
| Design Patterns | [Refactoring Guru](https://refactoring.guru/design-patterns) |
| TypeScript | [TypeScript Documentation](https://www.typescriptlang.org/docs/) |
| Node.js | [Node.js Documentation](https://nodejs.org/docs/latest/api/) |
| Express.js | [Express Documentation](https://expressjs.com/) |
| MySQL Transactions | [MySQL Documentation](https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-model.html) |
| Jest | [Jest Documentation](https://jestjs.io/docs/getting-started) |
| Prisma | [Prisma Documentation](https://www.prisma.io/docs) |

---

# 88. Final Summary

In Phase 50, you learned how to structure backend applications using professional software architecture principles.

The most important concept is:

**Business logic should remain independent of infrastructure, while application components should communicate through clear and well-defined boundaries.**

A well-structured backend can follow this architecture:

```text
                 Client
                    |
                    v
              Presentation
                    |
                    v
               Application
                    |
                    v
                  Domain
                    |
                    v
              Abstractions
                    ^
                    |
              Infrastructure
                    |
          +---------+---------+
          |         |         |
          v         v         v
        MySQL     Redis    External APIs
```

### Topics You Should Master Particularly Well

1. Clean Architecture.
2. Layered Architecture.
3. Dependency Rule.
4. Hexagonal Architecture.
5. Domain-Driven Design.
6. SOLID Principles.
7. Dependency Injection.
8. Repository Pattern.
9. Service Layer Pattern.
10. DTO and Data Mapping.
11. Use Cases.
12. CQRS.
13. Event Sourcing.
14. Database Transactions.
15. Unit of Work.
16. Unit and Integration Testing.

---

# Next Phase Preview

## Phase 51 – Advanced API Development, REST, GraphQL and API Security

Upcoming topics:

1. API Architecture Fundamentals.
2. REST API Design Principles.
3. REST Constraints.
4. HTTP Methods.
5. HTTP Status Codes.
6. Request and Response Design.
7. API Versioning.
8. Pagination.
9. Filtering and Sorting.
10. Search APIs.
11. API Rate Limiting.
12. API Throttling.
13. API Authentication.
14. API Authorization.
15. JWT Architecture.
16. OAuth 2.0.
17. OpenID Connect.
18. API Keys.
19. Refresh Token Rotation.
20. CORS.
21. CSRF.
22. XSS.
23. SQL Injection.
24. API Input Validation.
25. GraphQL Fundamentals.
26. GraphQL Queries and Mutations.
27. GraphQL Resolvers.
28. GraphQL Schema Design.
29. GraphQL Authentication.
30. GraphQL Performance Optimization.
31. Webhooks.
32. API Documentation with Swagger/OpenAPI.
33. API Testing.
34. API Security Best Practices.
35. Production-Ready API Project.

**Phase 51 will focus on designing, implementing, securing, documenting and testing professional APIs using Node.js and Express.js.**