# Phase 61 – Advanced Software Architecture, Design Patterns and Enterprise Application Development

**Level:** Advanced to Expert  
**Domain:** Software Architecture, Object-Oriented Design, Enterprise Application Development and Backend Engineering  
**Primary Technologies:** JavaScript, TypeScript, Node.js, Express.js, React.js, Next.js, MySQL, MongoDB, Redis and Docker.

---

# 1. Learning Objectives

In this phase, you will learn how to design maintainable, reusable, scalable and testable software applications using professional software architecture principles.

You will understand how to structure large applications, separate responsibilities, manage dependencies and apply design patterns to solve common software engineering problems.

By completing this phase, you should understand:

1. Software Architecture Fundamentals.
2. Architectural Styles.
3. Layered Architecture.
4. Clean Architecture.
5. Hexagonal Architecture.
6. Onion Architecture.
7. Domain-Driven Design (DDD).
8. Domain Modeling.
9. Bounded Contexts.
10. Aggregates and Entities.
11. Value Objects.
12. Repository Pattern.
13. Unit of Work Pattern.
14. Dependency Injection.
15. Inversion of Control.
16. SOLID Principles.
17. DRY, KISS and YAGNI.
18. Creational Design Patterns.
19. Structural Design Patterns.
20. Behavioral Design Patterns.
21. Factory Pattern.
22. Abstract Factory Pattern.
23. Builder Pattern.
24. Singleton Pattern.
25. Adapter Pattern.
26. Decorator Pattern.
27. Facade Pattern.
28. Proxy Pattern.
29. Strategy Pattern.
30. Observer Pattern.
31. Command Pattern.
32. Template Method Pattern.
33. Mediator Pattern.
34. Specification Pattern.
35. CQRS.
36. Event Sourcing.
37. Domain Events.
38. Dependency Management.
39. Modular Monolith Architecture.
40. Enterprise Application Architecture.
41. Refactoring and Technical Debt.
42. Architecture Decision Records (ADR).
43. Real-World Node.js Architecture.
44. Real-World Next.js Architecture.
45. Enterprise Project Structure.

---

# PART 1: SOFTWARE ARCHITECTURE FUNDAMENTALS

## 2. What Is Software Architecture?

Software Architecture is the high-level structure of a software system.

It defines:

- How application components are organized.
- How different modules communicate.
- How responsibilities are separated.
- How data flows through the application.
- How dependencies are managed.
- How the application can evolve over time.

In simple terms:

**Software Architecture defines how an application is structured and how its different parts work together.**

### Example: Basic Application Architecture

Suppose you develop an Expense Tracker using:

- React.
- Node.js.
- Express.js.
- MySQL.

A basic architecture might look like this:

```text
             React Frontend
                   |
                   v
              Express API
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

This architecture may work for a small application.

However, as the application grows, you may introduce:

- Authentication modules.
- Authorization modules.
- Payment integrations.
- Reporting modules.
- Background workers.
- Notification services.
- Caching.
- External APIs.

Without a proper architecture, the application can become difficult to maintain.

## 3. Why Is Software Architecture Important?

Good architecture helps with:

1. Maintainability.
2. Scalability.
3. Testability.
4. Reusability.
5. Extensibility.
6. Separation of concerns.
7. Team collaboration.
8. Code readability.
9. Dependency management.
10. Long-term development.

### Poor Architecture Example

```javascript
app.post("/expenses", async (req, res) => {
  // Validate request

  // Verify user

  // Check permissions

  // Execute SQL query

  // Calculate tax

  // Send notification

  // Generate report

  // Return response
});
```

Everything is handled inside one route handler.

Problems:

- Difficult to test.
- Difficult to maintain.
- Difficult to reuse.
- Difficult to debug.
- Responsibilities are mixed.

### Better Architecture

```text
Route
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

Each component has a specific responsibility.

---

# PART 2: ARCHITECTURAL STYLES

## 4. What Is an Architectural Style?

An Architectural Style is a general approach to organizing the components and relationships of a software system.

Different architectural styles solve different problems.

Common architectural styles include:

- Layered Architecture.
- Clean Architecture.
- Hexagonal Architecture.
- Onion Architecture.
- Microservices Architecture.
- Event-Driven Architecture.
- Pipe-and-Filter Architecture.
- Client-Server Architecture.

Let's understand the important ones.

---

# PART 3: LAYERED ARCHITECTURE

## 5. What Is Layered Architecture?

Layered Architecture divides an application into multiple layers.

Each layer has a defined responsibility.

A common backend application contains:

1. Presentation Layer.
2. Application Layer.
3. Business Logic Layer.
4. Data Access Layer.
5. Database Layer.

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
       Business Logic Layer
                |
                v
        Data Access Layer
                |
                v
             Database
```

## 6. Presentation Layer

The Presentation Layer handles communication with external users or systems.

In an Express application, this may include:

- Routes.
- Controllers.
- Request parsing.
- Response formatting.
- HTTP status codes.

Example:

```javascript
// expense.controller.js

const expenseService = require("./expense.service");

async function createExpense(req, res, next) {
  try {
    const expense = await expenseService.createExpense(
      req.user.id,
      req.body
    );

    return res.status(201).json({
      success: true,
      data: expense
    });
  } catch (error) {
    next(error);
  }
}

module.exports = {
  createExpense
};
```

The controller handles HTTP communication.

It does not contain the complete business logic.

## 7. Application Layer

The Application Layer coordinates application use cases.

Examples:

- Create Expense.
- Update Expense.
- Delete Expense.
- Generate Monthly Report.
- Transfer Money.

It coordinates business operations and infrastructure dependencies.

## 8. Business Logic Layer

The Business Logic Layer contains application-specific business rules.

Example:

```javascript
// expense.domain.js

function validateExpenseAmount(amount) {
  if (amount <= 0) {
    throw new Error("Expense amount must be positive");
  }

  if (amount > 1000000) {
    throw new Error("Expense amount exceeds allowed limit");
  }
}

module.exports = {
  validateExpenseAmount
};
```

These rules should not depend unnecessarily on Express or database-specific code.

## 9. Data Access Layer

The Data Access Layer handles data persistence.

Example:

```javascript
// expense.repository.js

class ExpenseRepository {
  async create(data) {
    return Expense.create(data);
  }

  async findById(id) {
    return Expense.findByPk(id);
  }

  async findByUserId(userId) {
    return Expense.findAll({
      where: {
        userId
      }
    });
  }
}

module.exports = ExpenseRepository;
```

### Layered Architecture Advantages

- Easy to understand.
- Clear separation of responsibilities.
- Suitable for many business applications.
- Easy to introduce developers to the codebase.

### Disadvantages

- Layers can become tightly coupled.
- Business logic may become dependent on infrastructure.
- Changes can propagate through several layers.
- Excessive layers may introduce unnecessary complexity.

---

# PART 4: CLEAN ARCHITECTURE

## 10. What Is Clean Architecture?

Clean Architecture is an architectural approach that organizes software around business rules and application use cases.

Its main principle is:

**The core business logic should not depend on external frameworks, databases or delivery mechanisms.**

This means your business logic should not need to know whether you use:

- Express.js.
- Fastify.
- MySQL.
- MongoDB.
- Redis.
- AWS.
- Another HTTP framework.

The architecture protects core business rules from infrastructure changes.

## 11. Clean Architecture Layers

A common Clean Architecture structure contains four conceptual layers:

1. Entities.
2. Use Cases.
3. Interface Adapters.
4. Frameworks and Drivers.

### Architecture Diagram

```text
       Frameworks & Drivers
       +-------------------+
       | Express           |
       | MySQL             |
       | Redis             |
       | External APIs     |
       +-------------------+
                 |
                 v
       +-------------------+
       | Interface         |
       | Adapters          |
       |                   |
       | Controllers       |
       | Presenters        |
       | Repositories      |
       +-------------------+
                 |
                 v
       +-------------------+
       | Use Cases         |
       |                   |
       | Create Expense    |
       | Update Expense    |
       | Delete Expense    |
       +-------------------+
                 |
                 v
       +-------------------+
       | Entities          |
       |                   |
       | Core Business     |
       | Rules             |
       +-------------------+
```

## 12. Entities

Entities represent core business concepts and rules.

Example:

```javascript
class Expense {
  constructor({
    id,
    userId,
    amount,
    description
  }) {
    if (amount <= 0) {
      throw new Error("Invalid expense amount");
    }

    this.id = id;
    this.userId = userId;
    this.amount = amount;
    this.description = description;
  }
}

module.exports = Expense;
```

This entity does not depend on Express or Sequelize.

## 13. Use Cases

Use Cases represent operations that the application supports.

Examples:

- Create Expense.
- Update Expense.
- Delete Expense.
- Get User Expenses.

Example:

```javascript
class CreateExpense {
  constructor(expenseRepository) {
    this.expenseRepository = expenseRepository;
  }

  async execute(input) {
    if (input.amount <= 0) {
      throw new Error("Amount must be positive");
    }

    return this.expenseRepository.create({
      userId: input.userId,
      amount: input.amount,
      description: input.description
    });
  }
}

module.exports = CreateExpense;
```

The use case depends on a repository abstraction rather than directly importing a particular database implementation.

## 14. Interface Adapters

Interface Adapters translate data between external systems and application use cases.

Examples:

- Controllers.
- Presenters.
- Repository implementations.
- API request/response mappers.

## 15. Frameworks and Drivers

This is the outermost layer.

It contains implementation details such as:

- Express.
- MySQL.
- MongoDB.
- Redis.
- File storage.
- External APIs.

### Dependency Rule

Dependencies should point inward.

```text
External Frameworks
         |
         v
Interface Adapters
         |
         v
Use Cases
         |
         v
Entities
```

The core business logic should not depend on the outer infrastructure.

### Why Is This Important?

Suppose you currently use MySQL.

Later, you decide to migrate to MongoDB.

With a well-designed architecture, you should be able to replace the database adapter without rewriting your core business rules.

### Clean Architecture Advantages

- Framework independence.
- Database independence at the application-core level.
- Better testability.
- Easier maintenance.
- Better separation of concerns.

### Disadvantages

- More initial setup.
- More interfaces and abstractions.
- Can become unnecessarily complex for small applications.
- Requires architectural discipline.

---

# PART 5: HEXAGONAL ARCHITECTURE

## 16. What Is Hexagonal Architecture?

Hexagonal Architecture is also known as Ports and Adapters Architecture.

It was introduced by Alistair Cockburn.

Its central idea is to keep the application core independent of external systems.

The application interacts with external systems through ports.

Adapters implement those ports.

### Architecture

```text
             External Systems
                    |
          +---------+---------+
          |                   |
          v                   v
       REST API            Database
          |                   |
          v                   v
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
       Use Cases          Domain Rules
```

## 17. What Are Ports?

A Port is an interface that defines how the application communicates with something external.

Example:

```typescript
interface ExpenseRepository {
  create(expense: Expense): Promise<Expense>;

  findById(id: string): Promise<Expense | null>;
}
```

This interface describes what the application needs.

It does not specify how MySQL or MongoDB implements the functionality.

## 18. What Are Adapters?

An Adapter implements a port or translates an external interaction into the application's expected format.

Example:

```typescript
class MySQLExpenseRepository
  implements ExpenseRepository {

  async create(expense: Expense) {
    // Persist expense using MySQL
  }

  async findById(id: string) {
    // Retrieve expense from MySQL
    return null;
  }
}
```

Another adapter could use MongoDB.

```typescript
class MongoExpenseRepository
  implements ExpenseRepository {

  async create(expense: Expense) {
    // Persist expense using MongoDB
  }

  async findById(id: string) {
    // Retrieve expense from MongoDB
    return null;
  }
}
```

Both implement the same application-facing contract.

### Advantages

- External systems can be replaced.
- Business logic remains isolated.
- Testing becomes easier.
- Multiple delivery mechanisms can be supported.

### Common Use Cases

- Applications integrating multiple payment providers.
- Systems supporting multiple databases.
- Applications exposing REST and GraphQL APIs.
- Systems with external messaging integrations.

---

# PART 6: ONION ARCHITECTURE

## 19. What Is Onion Architecture?

Onion Architecture organizes software into concentric layers.

The domain model sits at the center.

Infrastructure and external dependencies are placed toward the outside.

### Architecture

```text
        +---------------------------+
        |       Infrastructure      |
        |  +---------------------+  |
        |  |  Application Layer  |  |
        |  |  +---------------+  |  |
        |  |  | Domain Layer  |  |  |
        |  |  +---------------+  |  |
        |  +---------------------+  |
        +---------------------------+
```

### Main Layers

1. Domain Model.
2. Domain Services.
3. Application Services.
4. Infrastructure.

### Important Principle

Dependencies point toward the domain core.

The domain should not depend on infrastructure.

### Clean vs Hexagonal vs Onion

| Architecture | Main Idea |
|---|---|
| Clean Architecture | Protect business rules using inward dependencies |
| Hexagonal Architecture | Application core communicates through ports and adapters |
| Onion Architecture | Domain model at the center with outer dependency layers |

These architectures share many principles and can be combined in practical systems. They are not mutually exclusive competing technologies.

---

# PART 7: DOMAIN-DRIVEN DESIGN (DDD)

## 20. What Is Domain-Driven Design?

Domain-Driven Design is an approach to software development that focuses on understanding and modeling the business domain.

It emphasizes collaboration between developers and domain experts.

Instead of designing software entirely around database tables or framework requirements, DDD encourages developers to model the actual business concepts and rules.

### Example

Suppose you are building an e-commerce application.

The business domain includes:

- Customers.
- Products.
- Orders.
- Payments.
- Inventory.
- Shipping.
- Discounts.

DDD helps represent these concepts in the software architecture.

## 21. What Is a Domain?

A Domain is the business area that your application addresses.

Examples:

| Application | Domain |
|---|---|
| Expense Tracker | Personal Finance |
| E-commerce | Online Retail |
| Hospital Management | Healthcare Administration |
| Banking Application | Financial Services |
| Learning Platform | Education |

## 22. What Is a Subdomain?

A Subdomain is a smaller area of a larger business domain.

Example: E-commerce.

```text
E-Commerce Domain
       |
       +-- Product Catalog
       |
       +-- Order Management
       |
       +-- Payment Processing
       |
       +-- Inventory Management
       |
       +-- Shipping
```

DDD commonly classifies subdomains into:

1. Core Subdomain.
2. Supporting Subdomain.
3. Generic Subdomain.

### Core Subdomain

Provides the main competitive advantage of the business.

### Supporting Subdomain

Supports the core business capabilities.

### Generic Subdomain

Provides common functionality that may already have established solutions.

---

# PART 8: BOUNDED CONTEXTS

## 23. What Is a Bounded Context?

A Bounded Context is a defined boundary within which a particular domain model and its terminology have a consistent meaning.

This is an important DDD concept.

### Example: E-Commerce

The word "Product" may mean different things in different business areas.

In the Product Catalog context:

```text
Product
- Name
- Description
- Images
- Price
- Category
```

In the Inventory context:

```text
Product
- Product ID
- Available Quantity
- Reserved Quantity
- Warehouse ID
```

The same business concept has different representations depending on the context.

### Architecture

```text
             E-Commerce System
                     |
        +------------+------------+
        |            |            |
        v            v            v
    Catalog       Inventory     Orders
    Context        Context      Context
        |            |            |
        v            v            v
    Product       Stock         Order
      Model        Model         Model
```

### Why Bounded Contexts Matter

- Prevents one giant domain model.
- Clarifies ownership.
- Reduces coupling.
- Helps define service boundaries.
- Makes business rules easier to understand.

---

# PART 9: ENTITIES AND VALUE OBJECTS

## 24. What Is an Entity?

An Entity is an object that has a distinct identity and can change over time.

Example:

A user may change their name or email, but the user remains the same user.

```typescript
class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string
  ) {}
}
```

Two users can have the same name but different IDs.

Their identities are different.

## 25. What Is a Value Object?

A Value Object is an object whose identity is determined by its values rather than a unique identifier.

Example:

Money.

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

Two Money objects with the same amount and currency represent the same value, assuming the domain's equality rules.

### Entity vs Value Object

| Entity | Value Object |
|---|---|
| Has identity | No independent identity |
| Can change over time | Usually immutable |
| Compared by identity | Compared by values |
| Example: User | Example: Money |
| Example: Order | Example: Address |

### Important

In financial applications, avoid using floating-point numbers for exact monetary calculations.

Use integer minor units or an appropriate decimal representation.

For example:

```text
₹125.50 = 12550 paise
```

---

# PART 10: AGGREGATES AND AGGREGATE ROOTS

## 26. What Is an Aggregate?

An Aggregate is a group of related domain objects treated as a consistency boundary.

An Aggregate Root is the main entity through which external code accesses and modifies the aggregate.

### Example: Order Aggregate

```text
Order Aggregate
      |
      v
   Order Root
      |
      +-- Order Items
      |
      +-- Shipping Address
      |
      +-- Order Status
```

External code should interact through the Order root rather than independently modifying internal objects in ways that violate business rules.

### Example

```typescript
class Order {
  private items: OrderItem[] = [];

  addItem(item: OrderItem) {
    if (item.quantity <= 0) {
      throw new Error("Invalid quantity");
    }

    this.items.push(item);
  }

  getItems() {
    return [...this.items];
  }
}
```

### Why Are Aggregates Important?

- Enforce business invariants.
- Control object modifications.
- Define transaction boundaries.
- Reduce accidental inconsistent state.

---

# PART 11: REPOSITORY PATTERN

## 27. What Is the Repository Pattern?

The Repository Pattern provides an abstraction between business/application logic and data persistence.

It allows the application to work with data through a consistent interface.

### Without Repository

```javascript
async function createExpense(data) {
  return ExpenseModel.create(data);
}
```

The service directly depends on Sequelize.

### With Repository

```text
Expense Service
      |
      v
Expense Repository
      |
      v
Sequelize / MySQL
```

### Repository Interface

```typescript
interface ExpenseRepository {
  create(data: CreateExpenseInput): Promise<Expense>;

  findById(id: string): Promise<Expense | null>;

  findByUserId(userId: string): Promise<Expense[]>;
}
```

### Repository Implementation

```typescript
class MySQLExpenseRepository
  implements ExpenseRepository {

  async create(data: CreateExpenseInput) {
    return ExpenseModel.create(data);
  }

  async findById(id: string) {
    return ExpenseModel.findByPk(id);
  }

  async findByUserId(userId: string) {
    return ExpenseModel.findAll({
      where: {
        userId
      }
    });
  }
}
```

### Advantages

- Separates persistence from business logic.
- Improves testing.
- Provides a consistent data access interface.
- Makes infrastructure replacement easier.

### Important

Do not create repository abstractions automatically for every database operation.

A repository is valuable when it provides meaningful separation or domain-oriented behavior. Excessive abstraction can make simple code harder to understand.

---

# PART 12: UNIT OF WORK PATTERN

## 28. What Is Unit of Work?

The Unit of Work Pattern tracks changes made during a business operation and coordinates persistence within a transaction.

It helps ensure that related database operations succeed or fail together.

### Example

Suppose an application transfers money.

Two operations are required:

1. Debit Account A.
2. Credit Account B.

Both should happen in one database transaction.

### Without Transaction

```text
Debit Account A
       |
       v
Credit Account B
       |
       v
Failure
```

The account balances may become inconsistent.

### With Unit of Work / Transaction

```text
Begin Transaction
       |
       v
Debit Account A
       |
       v
Credit Account B
       |
       v
Commit Transaction
```

If something fails, roll back.

### Sequelize Example

```javascript
const { sequelize } = require("./models");

async function transferMoney(fromId, toId, amount) {
  return sequelize.transaction(async (transaction) => {
    const fromAccount = await Account.findByPk(fromId, {
      transaction,
      lock: transaction.LOCK.UPDATE
    });

    const toAccount = await Account.findByPk(toId, {
      transaction,
      lock: transaction.LOCK.UPDATE
    });

    if (!fromAccount || !toAccount) {
      throw new Error("Account not found");
    }

    if (fromAccount.balance < amount) {
      throw new Error("Insufficient balance");
    }

    await fromAccount.decrement("balance", {
      by: amount,
      transaction
    });

    await toAccount.increment("balance", {
      by: amount,
      transaction
    });

    return {
      success: true
    };
  });
}
```

This example assumes valid positive amounts and a suitable database transaction isolation/locking configuration. Production financial transfers also need currency rules, authorization, idempotency and audit records.

---

# PART 13: DEPENDENCY INJECTION

## 29. What Is Dependency Injection?

Dependency Injection (DI) is a design technique in which an object receives the dependencies it needs from an external source rather than creating them internally.

### Without Dependency Injection

```javascript
class ExpenseService {
  constructor() {
    this.repository = new MySQLExpenseRepository();
  }

  async create(data) {
    return this.repository.create(data);
  }
}
```

Problem:

`ExpenseService` is tightly coupled to `MySQLExpenseRepository`.

Replacing MySQL with another implementation requires changing the service.

### With Dependency Injection

```javascript
class ExpenseService {
  constructor(repository) {
    this.repository = repository;
  }

  async create(data) {
    return this.repository.create(data);
  }
}
```

Now:

```javascript
const repository = new MySQLExpenseRepository();

const expenseService = new ExpenseService(repository);
```

For testing:

```javascript
const fakeRepository = {
  async create(data) {
    return {
      id: "1",
      ...data
    };
  }
};

const expenseService = new ExpenseService(
  fakeRepository
);
```

### Benefits

- Loose coupling.
- Easier testing.
- Easier replacement of dependencies.
- Better modularity.
- Improved flexibility.

## 30. Types of Dependency Injection

### Constructor Injection

Dependencies are provided through the constructor.

```javascript
class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }
}
```

### Setter Injection

Dependencies are provided through a setter method.

```javascript
class UserService {
  setRepository(repository) {
    this.repository = repository;
  }
}
```

### Method Injection

Dependencies are passed directly to a method.

```javascript
async function generateReport(reportService, input) {
  return reportService.generate(input);
}
```

Constructor injection is often a good default for required dependencies because it makes dependencies explicit.

---

# PART 14: INVERSION OF CONTROL (IoC)

## 31. What Is Inversion of Control?

Inversion of Control is a broader design principle in which control over object creation, lifecycle or execution is delegated to an external mechanism.

Normally, an object might create its dependencies itself.

With IoC, another component manages those dependencies.

### Without IoC

```javascript
class OrderService {
  constructor() {
    this.paymentService = new PaymentService();
  }
}
```

The OrderService controls dependency creation.

### With IoC

```javascript
class OrderService {
  constructor(paymentService) {
    this.paymentService = paymentService;
  }
}
```

An external component provides the dependency.

### Dependency Injection vs IoC

| Dependency Injection | Inversion of Control |
|---|---|
| A technique | A broader principle |
| Supplies dependencies externally | Delegates control to another component |
| One way to implement IoC | Can be implemented through several mechanisms |
| Example: Constructor injection | Example: Framework-managed lifecycle |

---

# PART 15: SOLID PRINCIPLES

## 32. What Is SOLID?

SOLID is a collection of five object-oriented design principles.

They help create software that is:

- Maintainable.
- Extensible.
- Testable.
- Reusable.
- Less tightly coupled.

SOLID stands for:

1. Single Responsibility Principle.
2. Open/Closed Principle.
3. Liskov Substitution Principle.
4. Interface Segregation Principle.
5. Dependency Inversion Principle.

---

## 33. S – Single Responsibility Principle (SRP)

A class or module should have one primary responsibility and one coherent reason to change.

### Incorrect Example

```javascript
class User {
  createUser() {}

  sendEmail() {}

  generateReport() {}

  saveToDatabase() {}
}
```

This class handles unrelated responsibilities.

### Better Example

```javascript
class UserService {
  createUser() {}
}

class EmailService {
  sendEmail() {}
}

class ReportService {
  generateReport() {}
}

class UserRepository {
  saveToDatabase() {}
}
```

Responsibilities are separated.

**Important:** SRP does not mean every class must contain only one method. It means related behavior should be grouped around a coherent responsibility.

---

## 34. O – Open/Closed Principle (OCP)

Software entities should be open for extension but closed for modification.

This means new behavior should often be introduced through extensions rather than repeatedly modifying stable existing code.

### Incorrect Example

```javascript
function calculateDiscount(type, amount) {
  if (type === "regular") {
    return amount * 0.05;
  }

  if (type === "premium") {
    return amount * 0.10;
  }

  if (type === "vip") {
    return amount * 0.20;
  }
}
```

Every new discount type requires modifying this function.

### Better Example

```javascript
class RegularDiscount {
  calculate(amount) {
    return amount * 0.05;
  }
}

class PremiumDiscount {
  calculate(amount) {
    return amount * 0.10;
  }
}

class VipDiscount {
  calculate(amount) {
    return amount * 0.20;
  }
}
```

A new discount implementation can be added without changing existing implementations.

---

## 35. L – Liskov Substitution Principle (LSP)

Objects of a subtype should be usable wherever objects of the base type are expected without breaking the expected behavior.

### Example

Suppose:

```javascript
class Bird {
  move() {
    return "Moving";
  }
}

class Sparrow extends Bird {
  move() {
    return "Flying";
  }
}
```

Sparrow can be used where Bird behavior is expected.

However, consider a model that assumes every bird can fly:

```javascript
class Bird {
  fly() {}
}
```

A penguin cannot satisfy that behavior.

A better design separates flying capability.

```javascript
class Bird {}

class FlyingBird extends Bird {
  fly() {
    return "Flying";
  }
}

class Penguin extends Bird {
  swim() {
    return "Swimming";
  }
}
```

The important point is behavioral substitutability, not merely inheritance syntax.

---

## 36. I – Interface Segregation Principle (ISP)

Clients should not be forced to depend on methods they do not use.

### Incorrect Example

```typescript
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}
```

A robot worker does not need to eat or sleep.

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

Different classes implement only the capabilities they need.

---

## 37. D – Dependency Inversion Principle (DIP)

High-level modules should not depend directly on low-level modules.

Both should depend on abstractions.

### Incorrect Example

```javascript
class PaymentService {
  constructor() {
    this.gateway = new StripeGateway();
  }
}
```

The high-level payment service depends directly on a specific provider.

### Better Example

```typescript
interface PaymentGateway {
  charge(amount: number): Promise<boolean>;
}
```

Implementations:

```typescript
class StripeGateway implements PaymentGateway {
  async charge(amount: number) {
    return true;
  }
}

class RazorpayGateway implements PaymentGateway {
  async charge(amount: number) {
    return true;
  }
}
```

Service:

```typescript
class PaymentService {
  constructor(
    private gateway: PaymentGateway
  ) {}

  async pay(amount: number) {
    return this.gateway.charge(amount);
  }
}
```

Now the payment service depends on an abstraction rather than one concrete provider.

### SOLID Summary

| Principle | Meaning |
|---|---|
| S – SRP | Single Responsibility |
| O – OCP | Open for Extension, Closed for Modification |
| L – LSP | Liskov Substitution |
| I – ISP | Interface Segregation |
| D – DIP | Dependency Inversion |

---

# PART 16: DRY, KISS AND YAGNI

## 38. What Is DRY?

DRY stands for Don't Repeat Yourself.

It encourages reducing duplicated knowledge and logic.

### Incorrect

```javascript
function calculatePriceA(price) {
  return price + price * 0.18;
}

function calculatePriceB(price) {
  return price + price * 0.18;
}
```

### Better

```javascript
function calculatePriceWithTax(price, taxRate) {
  return price + price * taxRate;
}
```

## 39. What Is KISS?

KISS stands for Keep It Simple, Stupid.

It encourages simple solutions over unnecessarily complicated implementations.

Example:

If a basic function solves a problem clearly, do not introduce a complex abstraction without a real need.

## 40. What Is YAGNI?

YAGNI stands for You Aren't Gonna Need It.

It encourages developers not to implement speculative features before they are actually required.

Example:

Do not build a complex plugin architecture for an application that currently needs only one fixed payment provider.

### DRY vs KISS vs YAGNI

| Principle | Focus |
|---|---|
| DRY | Avoid duplicated knowledge |
| KISS | Keep solutions simple |
| YAGNI | Avoid unnecessary future features |

**Important:** These principles should be balanced. Excessive abstraction in the name of DRY can make code harder to understand.

---

# PART 17: DESIGN PATTERNS FUNDAMENTALS

## 41. What Is a Design Pattern?

A Design Pattern is a reusable, general solution to a commonly occurring software design problem.

It is not a complete implementation that must be copied exactly.

It is a proven approach that developers adapt to their particular requirements.

### Why Use Design Patterns?

- Reusable solutions.
- Better code organization.
- Improved communication between developers.
- Reduced coupling.
- Easier maintenance.
- More predictable design.

## 42. Categories of Design Patterns

Design patterns are commonly grouped into three categories:

1. Creational Patterns.
2. Structural Patterns.
3. Behavioral Patterns.

### Creational Patterns

Deal with object creation.

Examples:

- Factory.
- Abstract Factory.
- Builder.
- Singleton.
- Prototype.

### Structural Patterns

Deal with object and class composition.

Examples:

- Adapter.
- Decorator.
- Facade.
- Proxy.
- Composite.
- Bridge.

### Behavioral Patterns

Deal with communication and responsibility between objects.

Examples:

- Strategy.
- Observer.
- Command.
- Template Method.
- Mediator.
- State.
- Iterator.

---

# PART 18: CREATIONAL DESIGN PATTERNS

## 43. Factory Pattern

The Factory Pattern centralizes object creation.

Instead of creating objects directly in many places, a factory decides which object should be instantiated.

### Problem

Suppose your application supports multiple payment methods.

```javascript
const payment = new StripePayment();
```

Later, you may need Razorpay or PayPal.

### Factory Implementation

```javascript
class StripePayment {
  pay(amount) {
    return `Paid ${amount} using Stripe`;
  }
}

class RazorpayPayment {
  pay(amount) {
    return `Paid ${amount} using Razorpay`;
  }
}

class PaymentFactory {
  static create(provider) {
    switch (provider) {
      case "stripe":
        return new StripePayment();

      case "razorpay":
        return new RazorpayPayment();

      default:
        throw new Error("Unsupported provider");
    }
  }
}
```

Usage:

```javascript
const payment = PaymentFactory.create("stripe");

console.log(payment.pay(1000));
```

### When to Use?

- Multiple payment providers.
- Multiple notification channels.
- Different storage implementations.
- Different report formats.

---

## 44. Abstract Factory Pattern

Abstract Factory provides an interface for creating families of related objects.

Example:

A UI system supports:

- Windows UI components.
- macOS UI components.

Each family contains buttons and checkboxes.

### Architecture

```text
          UI Factory
              |
       +------+------+
       |             |
       v             v
 Windows Factory  Mac Factory
       |             |
   +---+---+     +---+---+
   |       |     |       |
 Button Checkbox Button Checkbox
```

### Example

```typescript
interface Button {
  render(): string;
}

interface Checkbox {
  render(): string;
}

interface UIFactory {
  createButton(): Button;
  createCheckbox(): Checkbox;
}
```

The concrete factory returns compatible objects from the same family.

### When to Use?

- Multiple UI themes.
- Multiple platform-specific implementations.
- Related product families.
- Different cloud-provider resource implementations.

---

## 45. Builder Pattern

The Builder Pattern constructs complex objects step by step.

### Problem

A large object may have many optional properties.

```javascript
const user = {
  name: "John",
  email: "john@example.com",
  phone: "1234567890",
  address: "Ahmedabad",
  role: "admin"
};
```

Creating complex objects with many constructor arguments can become difficult to maintain.

### Builder Implementation

```javascript
class UserBuilder {
  setName(name) {
    this.name = name;
    return this;
  }

  setEmail(email) {
    this.email = email;
    return this;
  }

  setRole(role) {
    this.role = role;
    return this;
  }

  build() {
    if (!this.name || !this.email) {
      throw new Error("Name and email are required");
    }

    return {
      name: this.name,
      email: this.email,
      role: this.role || "user"
    };
  }
}
```

Usage:

```javascript
const user = new UserBuilder()
  .setName("John")
  .setEmail("john@example.com")
  .setRole("admin")
  .build();
```

### When to Use?

- Complex configuration objects.
- Test data creation.
- Query builders.
- Report generation.
- Request construction.

---

## 46. Singleton Pattern

Singleton ensures that a class has one shared instance within a defined scope.

Example:

A database connection manager.

```javascript
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance;
    }

    this.connected = false;

    Database.instance = this;
  }

  connect() {
    this.connected = true;

    console.log("Database connected");
  }
}

const db1 = new Database();
const db2 = new Database();

console.log(db1 === db2);
```

Output:

```text
true
```

### Common Use Cases

- Configuration manager.
- Shared service registry.
- Certain connection managers.
- Application-wide immutable configuration.

### Disadvantages

- Hidden global state.
- Difficult testing.
- Tight coupling.
- Potential lifecycle issues.

**Important:** Node.js modules are cached after loading, so a module exporting one shared object often provides singleton-like behavior without implementing a Singleton class.

Also, a Singleton is usually limited to one process or runtime. Multiple Node.js workers or containers have separate memory.

---

# PART 19: STRUCTURAL DESIGN PATTERNS

## 47. Adapter Pattern

The Adapter Pattern allows incompatible interfaces to work together.

### Example

Your application expects:

```javascript
payment.pay(amount);
```

But an external provider exposes:

```javascript
paymentProvider.executePayment(amount);
```

Create an adapter:

```javascript
class PaymentAdapter {
  constructor(provider) {
    this.provider = provider;
  }

  pay(amount) {
    return this.provider.executePayment(amount);
  }
}
```

Now the application can use the expected interface.

### When to Use?

- Third-party API integrations.
- Payment provider integrations.
- Legacy systems.
- Database driver abstraction.

---

## 48. Decorator Pattern

The Decorator Pattern adds behavior to an object without changing its original implementation.

Example:

A basic notification service sends an email.

You want to add logging.

```javascript
class EmailService {
  send(message) {
    console.log("Email sent:", message);
  }
}

class LoggingDecorator {
  constructor(service) {
    this.service = service;
  }

  send(message) {
    console.log("Sending notification");

    this.service.send(message);

    console.log("Notification completed");
  }
}
```

Usage:

```javascript
const email = new EmailService();

const decoratedEmail = new LoggingDecorator(email);

decoratedEmail.send("Welcome!");
```

### When to Use?

- Logging.
- Metrics.
- Authorization wrappers.
- Caching.
- Additional notification behavior.

---

## 49. Facade Pattern

The Facade Pattern provides a simplified interface to a complex subsystem.

### Example

An order workflow involves:

- Inventory.
- Payment.
- Shipping.
- Notifications.

Without a facade, the client may need to coordinate all services.

### Implementation

```javascript
class InventoryService {
  reserve() {
    console.log("Inventory reserved");
  }
}

class PaymentService {
  charge() {
    console.log("Payment processed");
  }
}

class ShippingService {
  createShipment() {
    console.log("Shipment created");
  }
}

class OrderFacade {
  constructor() {
    this.inventory = new InventoryService();
    this.payment = new PaymentService();
    this.shipping = new ShippingService();
  }

  async placeOrder() {
    this.inventory.reserve();

    this.payment.charge();

    this.shipping.createShipment();
  }
}
```

Usage:

```javascript
const order = new OrderFacade();

await order.placeOrder();
```

This is a simplified demonstration. Real payment and inventory workflows require transactions, failure handling and often a Saga or another coordination mechanism.

---

## 50. Proxy Pattern

The Proxy Pattern provides a substitute object that controls access to another object.

Common uses:

- Access control.
- Lazy loading.
- Caching.
- Logging.
- Remote service access.

### Example

```javascript
const user = {
  name: "John",
  role: "admin"
};

const userProxy = new Proxy(user, {
  get(target, property) {
    console.log(`Accessing ${String(property)}`);

    return target[property];
  }
});

console.log(userProxy.name);
```

The Proxy intercepts property access.

---

# PART 20: BEHAVIORAL DESIGN PATTERNS

## 51. Strategy Pattern

The Strategy Pattern defines a family of interchangeable algorithms.

Each strategy implements a common interface.

### Example

An application supports multiple discount calculations.

```javascript
class RegularDiscount {
  calculate(amount) {
    return amount * 0.05;
  }
}

class PremiumDiscount {
  calculate(amount) {
    return amount * 0.10;
  }
}

class VipDiscount {
  calculate(amount) {
    return amount * 0.20;
  }
}
```

Context:

```javascript
class DiscountService {
  constructor(strategy) {
    this.strategy = strategy;
  }

  calculate(amount) {
    return this.strategy.calculate(amount);
  }
}
```

Usage:

```javascript
const discount = new DiscountService(
  new PremiumDiscount()
);

console.log(discount.calculate(1000));
```

Output:

```text
100
```

### When to Use?

- Payment methods.
- Discount calculations.
- Shipping calculations.
- Authentication strategies.
- File-processing strategies.

---

## 52. Observer Pattern

The Observer Pattern allows one object to notify multiple dependent objects when something changes.

### Example

When a user registers:

- Send welcome email.
- Create default profile.
- Record analytics event.

```text
User Registered
      |
      v
   Publisher
      |
   +--+--+
   |  |  |
   v  v  v
 Email Profile Analytics
```

### JavaScript Example

```javascript
class EventEmitter {
  constructor() {
    this.listeners = {};
  }

  subscribe(event, callback) {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }

    this.listeners[event].push(callback);
  }

  publish(event, data) {
    const callbacks = this.listeners[event] || [];

    for (const callback of callbacks) {
      callback(data);
    }
  }
}
```

Usage:

```javascript
const emitter = new EventEmitter();

emitter.subscribe("userRegistered", (user) => {
  console.log("Send welcome email:", user.email);
});

emitter.subscribe("userRegistered", (user) => {
  console.log("Create profile:", user.id);
});

emitter.publish("userRegistered", {
  id: 1,
  email: "user@example.com"
});
```

### Important

This example is an in-memory Observer implementation.

It does not provide durable delivery across process restarts. For distributed applications, use an appropriate message broker or event infrastructure.

---

## 53. Command Pattern

The Command Pattern encapsulates a request as an object.

This makes it easier to:

- Queue operations.
- Log operations.
- Retry operations.
- Undo certain operations.
- Schedule commands.

### Example

```javascript
class CreateExpenseCommand {
  constructor(expenseService, data) {
    this.expenseService = expenseService;
    this.data = data;
  }

  async execute() {
    return this.expenseService.create(this.data);
  }
}
```

Usage:

```javascript
const command = new CreateExpenseCommand(
  expenseService,
  {
    amount: 500,
    description: "Food"
  }
);

await command.execute();
```

---

## 54. Template Method Pattern

The Template Method Pattern defines the general structure of an algorithm while allowing subclasses to customize certain steps.

### Example

Different report types share a common workflow:

1. Fetch data.
2. Process data.
3. Format report.
4. Export report.

```javascript
class ReportGenerator {
  async generate() {
    const data = await this.fetchData();

    const processed = this.processData(data);

    return this.export(processed);
  }

  async fetchData() {
    throw new Error("Implement fetchData");
  }

  processData(data) {
    return data;
  }

  export(data) {
    return data;
  }
}
```

A concrete report implementation overrides the required steps.

---

## 55. Mediator Pattern

The Mediator Pattern centralizes communication between related objects.

Instead of objects communicating directly with many other objects, they communicate through a mediator.

### Example

A registration workflow involves:

- User Service.
- Email Service.
- Profile Service.
- Analytics Service.

The mediator coordinates the interaction.

```text
User Service
     |
     v
  Mediator
     |
 +---+---+---+
 |   |   |   |
 v   v   v   v
Email Profile Analytics
```

### When to Use?

- Complex UI interactions.
- Workflow coordination.
- Systems with many interacting components.

---

# PART 21: SPECIFICATION PATTERN

## 56. What Is the Specification Pattern?

The Specification Pattern encapsulates a business rule or selection condition into a reusable object.

It is useful when domain rules become complex and need to be combined.

### Example

Suppose an expense can be approved only if:

- Amount is below ₹50,000.
- Status is pending.
- User belongs to the correct department.

Instead of spreading these conditions across multiple services, create a specification.

```javascript
class ExpenseApprovalSpecification {
  isSatisfiedBy(expense) {
    return (
      expense.amount < 50000 &&
      expense.status === "pending" &&
      expense.departmentId === expense.managerDepartmentId
    );
  }
}
```

Usage:

```javascript
const specification =
  new ExpenseApprovalSpecification();

if (specification.isSatisfiedBy(expense)) {
  console.log("Expense can be approved");
}
```

### Benefits

- Reusable business rules.
- Easier testing.
- Better domain modeling.
- Clearer complex conditions.

---

# PART 22: CQRS

## 57. What Is CQRS?

CQRS stands for Command Query Responsibility Segregation.

It separates operations that modify data from operations that read data.

### Commands

Commands change application state.

Examples:

- Create Expense.
- Update Expense.
- Delete Expense.
- Approve Order.

### Queries

Queries retrieve information without changing application state.

Examples:

- Get Expenses.
- Get User Profile.
- Get Monthly Report.
- Search Products.

### Architecture

```text
             Client
                |
                v
             API Layer
                |
         +------+------+
         |             |
         v             v
      Commands       Queries
         |             |
         v             v
   Write Model     Read Model
         |             |
         v             v
     Database      Read Store
```

### Example

Command:

```javascript
class CreateExpenseCommand {
  constructor(data) {
    this.data = data;
  }
}
```

Handler:

```javascript
class CreateExpenseHandler {
  constructor(repository) {
    this.repository = repository;
  }

  async handle(command) {
    return this.repository.create(command.data);
  }
}
```

Query:

```javascript
class GetUserExpensesQuery {
  constructor(userId) {
    this.userId = userId;
  }
}
```

Handler:

```javascript
class GetUserExpensesHandler {
  constructor(repository) {
    this.repository = repository;
  }

  async handle(query) {
    return this.repository.findByUserId(
      query.userId
    );
  }
}
```

### Advantages

- Separate read and write responsibilities.
- Easier to optimize complex queries.
- Supports different read and write models.
- Can improve scalability in appropriate workloads.

### Disadvantages

- More complexity.
- Data synchronization challenges.
- Potential eventual consistency.
- Additional infrastructure in distributed implementations.

**Important:** CQRS does not require separate databases. You can implement it within a single application and database.

---

# PART 23: EVENT SOURCING

## 58. What Is Event Sourcing?

Event Sourcing is an architectural pattern in which application state is represented by a sequence of events.

Instead of storing only the latest state, the system stores the events that produced that state.

### Traditional Database Approach

An account table might contain:

```text
Account Balance = ₹8,000
```

The previous balance changes may not be represented in that record.

### Event Sourcing Approach

The system stores:

```text
AccountOpened
MoneyDeposited: ₹10,000
MoneyWithdrawn: ₹2,000
```

Current balance is calculated by replaying events:

```text
₹10,000 - ₹2,000 = ₹8,000
```

### Architecture

```text
            Command
               |
               v
          Command Handler
               |
               v
          Event Store
               |
               v
         Event Processor
               |
               v
          Current State
```

### Advantages

- Complete historical record.
- Auditability.
- Ability to reconstruct state.
- Useful for event-driven systems.

### Disadvantages

- More complex implementation.
- Event versioning.
- Event replay challenges.
- Storage growth.
- Eventual consistency in some architectures.
- Difficult correction of historical events.

### CQRS vs Event Sourcing

| CQRS | Event Sourcing |
|---|---|
| Separates read and write responsibilities | Stores state changes as events |
| Can use one database | Uses an event log/store as the source of truth |
| Does not require event storage | Requires event persistence |
| Can exist without Event Sourcing | Can be combined with CQRS |

---

# PART 24: DOMAIN EVENTS

## 59. What Is a Domain Event?

A Domain Event represents something meaningful that has happened within the business domain.

Examples:

- UserRegistered.
- ExpenseCreated.
- OrderPlaced.
- PaymentCompleted.
- InventoryReserved.

### Example

```javascript
class ExpenseCreatedEvent {
  constructor(expense) {
    this.expenseId = expense.id;
    this.userId = expense.userId;
    this.amount = expense.amount;
    this.occurredAt = new Date();
  }
}
```

A domain event can trigger additional business operations.

```text
Expense Created
       |
       v
Domain Event
       |
  +----+----+
  |         |
  v         v
Analytics Notification
```

### Domain Event vs Integration Event

| Domain Event | Integration Event |
|---|---|
| Represents an internal business occurrence | Communicates an event across system boundaries |
| Usually internal to a domain model | Intended for external consumers or services |
| Can contain domain-specific details | Should have a stable external contract |

---

# PART 25: MODULAR MONOLITH ARCHITECTURE

## 60. What Is a Modular Monolith?

A Modular Monolith is a single deployable application divided into well-defined modules.

Each module owns a particular business capability.

Example:

```text
             Expense Tracker
                    |
       +------------+------------+
       |            |            |
       v            v            v
    User          Expense     Reporting
    Module         Module       Module
       |            |            |
       +------------+------------+
                    |
                    v
               Application
```

### Important Characteristics

- One main deployment unit.
- Clear internal module boundaries.
- Limited cross-module dependencies.
- Separate business responsibilities.
- Easier operational management than many microservices.

### Example Project Structure

```text
src/
  modules/
    users/
      users.controller.js
      users.service.js
      users.repository.js
      users.routes.js

    expenses/
      expenses.controller.js
      expenses.service.js
      expenses.repository.js
      expenses.routes.js

    reports/
      reports.controller.js
      reports.service.js
      reports.repository.js
      reports.routes.js

  shared/
    database/
    logger/
    errors/

  app.js
  server.js
```

### Modular Monolith vs Microservices

| Modular Monolith | Microservices |
|---|---|
| Single main deployment unit | Multiple deployments |
| In-process communication | Network communication |
| Easier local development | More distributed infrastructure |
| Clear internal module boundaries | Independent service boundaries |
| Lower operational complexity | Higher operational complexity |

A modular monolith is often an excellent starting point for enterprise applications.

---

# PART 26: ENTERPRISE APPLICATION ARCHITECTURE

## 61. What Is an Enterprise Application?

An Enterprise Application is software designed to support complex organizational processes, business workflows and large-scale operations.

Examples:

- ERP Systems.
- CRM Systems.
- Banking Systems.
- Hospital Management Systems.
- Inventory Management Systems.
- HR Management Systems.

### Common Characteristics

- Multiple modules.
- Role-based permissions.
- Complex workflows.
- Large datasets.
- Integrations.
- Audit trails.
- Reporting.
- Security requirements.
- Long-term maintainability.

## 62. Example Enterprise Architecture

Suppose you develop an ERP system.

Modules:

```text
                  ERP System
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
    Finance         Inventory          HR
       |               |               |
       v               v               v
    Accounts         Stock          Employees
       |               |               |
       +---------------+---------------+
                       |
                       v
                 Shared Platform
                       |
             +---------+---------+
             |         |         |
             v         v         v
          Auth      Logging    Reporting
```

Each module should have clear responsibilities and controlled interactions with other modules.

---

# PART 27: REFACTORING AND TECHNICAL DEBT

## 63. What Is Refactoring?

Refactoring is the process of improving the internal structure of existing code without intentionally changing its externally observable behavior.

Example:

Before:

```javascript
async function createExpense(req, res) {
  // Validation
  // Database query
  // Business rules
  // Response handling
}
```

After:

```javascript
async function createExpense(req, res, next) {
  try {
    const result = await expenseService.create(
      req.user.id,
      req.body
    );

    res.status(201).json(result);
  } catch (error) {
    next(error);
  }
}
```

The internal responsibilities have been separated.

## 64. What Is Technical Debt?

Technical Debt is the future cost created by choosing a less-than-ideal implementation or architecture.

Examples:

- Duplicate code.
- Missing tests.
- Tightly coupled modules.
- Poor naming.
- Large controller functions.
- Unnecessary dependencies.
- Inconsistent error handling.
- Outdated libraries.

### Technical Debt Management

1. Identify debt.
2. Measure its impact.
3. Prioritize high-risk areas.
4. Refactor incrementally.
5. Add tests.
6. Document architectural decisions.

### Refactoring vs Rewriting

| Refactoring | Rewriting |
|---|---|
| Improves existing code incrementally | Replaces a substantial part or all of the implementation |
| Usually preserves behavior | May change behavior |
| Lower migration risk when controlled | Can introduce significant migration risk |
| Often supported by existing tests | Requires careful migration and parity testing |

---

# PART 28: ARCHITECTURE DECISION RECORDS (ADR)

## 65. What Is an ADR?

An Architecture Decision Record documents an important architectural decision and the reasoning behind it.

Example:

You choose MySQL instead of MongoDB for an expense management application.

The ADR documents:

- The problem.
- The context.
- The decision.
- Alternatives.
- Consequences.

### ADR Template

```markdown
# ADR-001: Choose MySQL as Primary Database

## Status
Accepted

## Context
The application stores relational financial records.

## Decision
Use MySQL as the primary database.

## Alternatives
- MongoDB
- PostgreSQL

## Consequences
- Relational constraints are supported.
- SQL queries are available.
- Transaction management is important.
- Schema migrations must be maintained.
```

### Why ADRs Matter

- Preserve architectural reasoning.
- Help new developers understand decisions.
- Avoid repeatedly discussing settled decisions.
- Document trade-offs.
- Improve team communication.

---

# PART 29: REAL-WORLD NODE.JS ENTERPRISE PROJECT STRUCTURE

## 66. Recommended Node.js Structure

For a medium-to-large Express application, consider a modular structure with clear boundaries.

```text
expense-tracker/
│
├── src/
│   │
│   ├── modules/
│   │   │
│   │   ├── auth/
│   │   │   ├── auth.routes.js
│   │   │   ├── auth.controller.js
│   │   │   ├── auth.service.js
│   │   │   └── auth.validation.js
│   │   │
│   │   ├── users/
│   │   │   ├── users.routes.js
│   │   │   ├── users.controller.js
│   │   │   ├── users.service.js
│   │   │   └── users.repository.js
│   │   │
│   │   ├── expenses/
│   │   │   ├── expenses.routes.js
│   │   │   ├── expenses.controller.js
│   │   │   ├── expenses.service.js
│   │   │   ├── expenses.repository.js
│   │   │   └── expenses.validation.js
│   │   │
│   │   └── reports/
│   │       ├── reports.routes.js
│   │       ├── reports.controller.js
│   │       └── reports.service.js
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   ├── redis/
│   │   ├── queue/
│   │   └── storage/
│   │
│   ├── shared/
│   │   ├── errors/
│   │   ├── logger/
│   │   ├── middleware/
│   │   └── utils/
│   │
│   ├── config/
│   │
│   ├── app.js
│   └── server.js
│
├── tests/
│
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

### Responsibilities

| Folder | Responsibility |
|---|---|
| modules | Business modules |
| infrastructure | External technical integrations |
| shared | Common utilities and middleware |
| config | Application configuration |
| tests | Automated tests |
| app.js | Express application setup |
| server.js | Server startup |

### Important

This is a starting structure, not a mandatory standard.

As the application grows, you can introduce separate domain, application, infrastructure and presentation directories within each module.

---

# PART 30: REAL-WORLD NEXT.JS ARCHITECTURE

## 67. Recommended Next.js Project Structure

For a large Next.js application using the App Router:

```text
src/
│
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── register/
│   │
│   ├── dashboard/
│   │
│   ├── expenses/
│   │
│   ├── reports/
│   │
│   ├── api/
│   │   └── health/
│   │
│   ├── layout.tsx
│   └── page.tsx
│
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types/
│   │
│   ├── expenses/
│   │   ├── components/
│   │   ├── services/
│   │   ├── schemas/
│   │   └── types/
│   │
│   └── reports/
│
├── components/
│   ├── ui/
│   ├── layout/
│   └── common/
│
├── lib/
│   ├── api/
│   ├── auth/
│   ├── database/
│   └── utils/
│
├── hooks/
│
├── types/
│
└── styles/
```

### Architectural Responsibilities

**App Router**

Responsible for:

- Routing.
- Layouts.
- Pages.
- Loading states.
- Error boundaries.
- Route Handlers.

**Features**

Contains functionality organized by business feature.

**Components**

Contains reusable UI components.

**Lib**

Contains shared infrastructure and utility code.

**Types**

Contains shared TypeScript types.

### Important Next.js Design Practices

- Keep server-only secrets in server-side modules.
- Separate client and server components intentionally.
- Validate data at server boundaries.
- Keep authorization checks on the server.
- Avoid putting all business logic in page components.
- Use server-side data access where appropriate.
- Avoid creating unnecessary API layers when Server Components can directly use a server-side application service.

---

# PART 31: COMPLETE ENTERPRISE ARCHITECTURE PROJECT

## 68. Project: Enterprise Expense Management System

Build an enterprise-style application using the concepts learned in this phase.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + React + TypeScript |
| Backend | Node.js + Express |
| Database | MySQL |
| ORM | Sequelize |
| Cache | Redis |
| Authentication | Session or JWT |
| Validation | Zod |
| Testing | Jest + Supertest + Playwright |
| Containerization | Docker |
| Deployment | AWS / VPS |

## 69. Architecture

```text
                  Frontend
                 Next.js
                    |
                    v
                API Layer
                    |
                    v
               Controllers
                    |
                    v
             Application Layer
                    |
         +----------+----------+
         |          |          |
         v          v          v
      Expense     User       Report
      Use Case    Use Case   Use Case
         |          |          |
         +----------+----------+
                    |
                    v
               Domain Layer
                    |
                    v
             Repository Ports
                    |
                    v
            Infrastructure Layer
                    |
         +----------+----------+
         |          |          |
         v          v          v
       MySQL      Redis     External APIs
```

## 70. Required Modules

### Authentication Module

- Registration.
- Login.
- Logout.
- Password reset.
- Session management.

### User Module

- Profile management.
- Role management.
- User administration.

### Expense Module

- Create expense.
- Update expense.
- Delete expense.
- Search expenses.
- Expense approval.

### Report Module

- Monthly reports.
- Category reports.
- Department reports.
- Excel export.

### Notification Module

- Email notifications.
- In-app notifications.
- Background processing.

### Architecture Requirements

- Apply SOLID principles.
- Use dependency injection.
- Separate business logic from HTTP handling.
- Use repository abstractions where useful.
- Validate all external inputs.
- Use transactions for related database changes.
- Write unit and integration tests.
- Document important architecture decisions.
- Avoid unnecessary abstractions.

---

# PART 32: DESIGN PATTERN INTERVIEW QUESTIONS

## Beginner-Level

### Q1. What is Software Architecture?

Software Architecture defines the overall structure and organization of a software system.

### Q2. What is a Design Pattern?

A reusable general solution to a commonly occurring software design problem.

### Q3. What is Layered Architecture?

An architecture that divides an application into layers with different responsibilities.

### Q4. What is Clean Architecture?

An architectural approach that protects core business rules from external framework and infrastructure dependencies.

### Q5. What is Hexagonal Architecture?

An architecture that separates the application core from external systems using ports and adapters.

### Q6. What is Domain-Driven Design?

An approach that focuses software design on understanding and modeling the business domain.

### Q7. What is Dependency Injection?

A technique in which an object receives its dependencies from an external source.

### Q8. What is Inversion of Control?

A principle in which control over object creation or execution is delegated to an external mechanism.

### Q9. What is SOLID?

Five object-oriented design principles that improve maintainability and extensibility.

### Q10. What is the Repository Pattern?

An abstraction that separates data persistence from application or domain logic.

## Intermediate-Level

### Q11. What is the Single Responsibility Principle?

A module should have one coherent responsibility and one primary reason to change.

### Q12. What is the Open/Closed Principle?

Software should be open to extension without requiring repeated modification of stable existing code.

### Q13. What is the Liskov Substitution Principle?

Subtypes should be usable wherever their base types are expected without breaking expected behavior.

### Q14. What is the Interface Segregation Principle?

Clients should not depend on interfaces containing methods they do not need.

### Q15. What is the Dependency Inversion Principle?

High-level and low-level modules should depend on abstractions rather than directly on each other.

### Q16. What is the Factory Pattern?

A pattern that centralizes object creation.

### Q17. What is the Singleton Pattern?

A pattern intended to provide one shared instance within a defined scope.

### Q18. What is the Adapter Pattern?

A pattern that makes incompatible interfaces work together.

### Q19. What is the Decorator Pattern?

A pattern that adds behavior to an object without modifying its original implementation.

### Q20. What is the Strategy Pattern?

A pattern that makes interchangeable algorithms available through a common interface.

## Advanced-Level

### Q21. What is the Observer Pattern?

A pattern in which subscribers are notified when a publisher emits an event or changes state.

### Q22. What is the Command Pattern?

A pattern that encapsulates a request as an object.

### Q23. What is the Facade Pattern?

A pattern that provides a simplified interface to a complex subsystem.

### Q24. What is the Proxy Pattern?

A pattern that controls access to another object through an intermediary.

### Q25. What is the Builder Pattern?

A pattern that constructs complex objects step by step.

### Q26. What is a Bounded Context?

A defined boundary within which a domain model and its terminology have consistent meaning.

### Q27. What is an Aggregate Root?

The main entity through which an aggregate is accessed and modified.

### Q28. What is a Value Object?

An object whose identity is determined by its values rather than an independent identifier.

### Q29. What is CQRS?

An architectural pattern that separates command operations from query operations.

### Q30. What is Event Sourcing?

A pattern in which application state is represented through a sequence of persisted events.

### Q31. What is a Domain Event?

A representation of something meaningful that has happened in the business domain.

### Q32. What is a Unit of Work?

A pattern that coordinates related changes and their persistence within a transaction.

### Q33. What is a Modular Monolith?

A single deployable application divided into clearly defined internal modules.

### Q34. What is Technical Debt?

Future maintenance and development cost caused by architectural or implementation compromises.

### Q35. What is an Architecture Decision Record?

A document recording an important architectural decision, its context and consequences.

## Scenario-Based Questions

### Q36. Your controller contains SQL queries, validation, email sending and business logic. What would you change?

Separate responsibilities into controllers, validation modules, application services, repositories and notification services.

### Q37. Your application supports three payment providers. Which pattern could help?

Strategy Pattern or Factory Pattern, depending on the design problem.

### Q38. You want to replace MySQL without changing business rules. Which architectural approach helps?

Clean Architecture or Hexagonal Architecture with repository abstractions.

### Q39. Your application has multiple business modules with tightly coupled models. What DDD concept can help?

Bounded Contexts can clarify ownership and domain boundaries.

### Q40. Your application needs separate optimized read and write models. Which pattern is relevant?

CQRS.

### Q41. You need a complete history of all account balance changes. Which pattern might help?

Event Sourcing.

### Q42. Your application has many modules but does not yet need independent deployments. What architecture would you consider?

A Modular Monolith.

### Q43. You need to integrate a third-party API with a different interface. Which pattern is appropriate?

Adapter Pattern.

### Q44. Multiple services must be coordinated during an order workflow. Which architecture patterns might help?

Saga Pattern, orchestration or event-driven choreography.

### Q45. Your code contains repeated conditional logic for discount calculations. Which principle or pattern could improve it?

Open/Closed Principle and Strategy Pattern.

---

# PART 33: HANDS-ON PRACTICAL ASSIGNMENTS

## Level 1 – Architecture Fundamentals

| No. | Assignment | Expected Result |
|---:|---|---|
| 1 | Design a Layered Architecture application | Layer diagram |
| 2 | Design a Clean Architecture application | Dependency diagram |
| 3 | Design a Hexagonal Architecture application | Ports and adapters |
| 4 | Compare Clean, Onion and Hexagonal Architecture | Comparison document |
| 5 | Create an Expense Tracker domain model | Domain entities |

## Level 2 – DDD and Dependency Management

| No. | Assignment | Expected Result |
|---:|---|---|
| 6 | Identify E-commerce subdomains | Domain breakdown |
| 7 | Define Bounded Contexts | Context map |
| 8 | Create User Entity | Entity implementation |
| 9 | Create Money Value Object | Immutable value object |
| 10 | Design Order Aggregate | Aggregate root |
| 11 | Implement Repository Pattern | Repository abstraction |
| 12 | Implement Unit of Work | Transaction management |
| 13 | Implement Dependency Injection | Loose coupling |
| 14 | Apply Dependency Inversion | Interface-based design |

## Level 3 – SOLID Principles

| No. | Assignment | Expected Result |
|---:|---|---|
| 15 | Refactor a large controller using SRP | Separated responsibilities |
| 16 | Implement OCP for discount calculations | Extensible discount system |
| 17 | Implement LSP using proper inheritance | Behavioral substitutability |
| 18 | Apply ISP to a worker system | Smaller interfaces |
| 19 | Apply DIP to a payment module | Abstraction-based dependencies |
| 20 | Refactor duplicate code using DRY | Reusable functionality |

## Level 4 – Design Patterns

| No. | Assignment | Expected Result |
|---:|---|---|
| 21 | Implement Factory Pattern | Payment factory |
| 22 | Implement Abstract Factory | Multiple UI families |
| 23 | Implement Builder Pattern | Complex object builder |
| 24 | Implement Singleton Pattern | Shared instance |
| 25 | Implement Adapter Pattern | Third-party integration |
| 26 | Implement Decorator Pattern | Additional behavior |
| 27 | Implement Facade Pattern | Simplified subsystem |
| 28 | Implement Proxy Pattern | Access control |
| 29 | Implement Strategy Pattern | Multiple algorithms |
| 30 | Implement Observer Pattern | Event notification |
| 31 | Implement Command Pattern | Encapsulated requests |
| 32 | Implement Template Method | Reusable workflow |
| 33 | Implement Mediator Pattern | Centralized coordination |
| 34 | Implement Specification Pattern | Reusable business rules |

## Level 5 – Advanced Architecture

| No. | Assignment | Expected Result |
|---:|---|---|
| 35 | Implement CQRS | Separate read/write handlers |
| 36 | Implement Event Sourcing | Persisted event stream |
| 37 | Implement Domain Events | Business event publishing |
| 38 | Build a Modular Monolith | Independent internal modules |
| 39 | Create Architecture Decision Records | Decision documentation |
| 40 | Refactor an existing Node.js application | Improved architecture |

## Level 6 – Enterprise Project

| No. | Assignment | Expected Result |
|---:|---|---|
| 41 | Design an enterprise Expense Tracker | HLD |
| 42 | Implement Clean Architecture in Node.js | Layered application |
| 43 | Implement SOLID principles | Maintainable code |
| 44 | Implement multiple payment providers | Strategy + Factory |
| 45 | Build an enterprise modular application | Complete project |

---

# PART 34: QUICK REVISION NOTES

| Concept | Meaning |
|---|---|
| Software Architecture | Overall structure of a software system |
| Architectural Style | General approach to organizing components |
| Layered Architecture | Application divided into responsibility-based layers |
| Clean Architecture | Business rules independent of infrastructure |
| Hexagonal Architecture | Ports and adapters around application core |
| Onion Architecture | Domain-centered dependency structure |
| DDD | Domain-Driven Design |
| Domain | Business area addressed by software |
| Subdomain | Smaller business area |
| Bounded Context | Boundary of a domain model |
| Entity | Object with unique identity |
| Value Object | Object identified by its values |
| Aggregate | Group of related domain objects |
| Aggregate Root | Main entry point to an aggregate |
| Repository | Abstraction for data persistence |
| Unit of Work | Coordinates transactional persistence |
| Dependency Injection | External provision of dependencies |
| IoC | Inversion of Control |
| SOLID | Five object-oriented design principles |
| SRP | Single Responsibility Principle |
| OCP | Open/Closed Principle |
| LSP | Liskov Substitution Principle |
| ISP | Interface Segregation Principle |
| DIP | Dependency Inversion Principle |
| DRY | Don't Repeat Yourself |
| KISS | Keep It Simple, Stupid |
| YAGNI | You Aren't Gonna Need It |
| Factory | Centralized object creation |
| Abstract Factory | Creates related object families |
| Builder | Step-by-step object construction |
| Singleton | Shared instance within a defined scope |
| Adapter | Makes incompatible interfaces work together |
| Decorator | Adds behavior to an object |
| Facade | Simplifies access to a subsystem |
| Proxy | Controls access to another object |
| Strategy | Interchangeable algorithms |
| Observer | Publisher and subscriber communication |
| Command | Encapsulates a request as an object |
| Template Method | Defines an algorithm skeleton |
| Mediator | Centralizes object communication |
| Specification | Encapsulates business rules |
| CQRS | Separates commands and queries |
| Event Sourcing | Stores state changes as events |
| Domain Event | Represents an important business occurrence |
| Modular Monolith | One deployment with independent internal modules |
| Refactoring | Improves internal code structure |
| Technical Debt | Future cost caused by implementation compromises |
| ADR | Architecture Decision Record |

---

# PART 35: PHASE 61 COMPLETION CHECKLIST

### Software Architecture

- [ ] Understand Software Architecture.
- [ ] Understand Architectural Styles.
- [ ] Understand Layered Architecture.
- [ ] Understand Clean Architecture.
- [ ] Understand Hexagonal Architecture.
- [ ] Understand Onion Architecture.

### Domain-Driven Design

- [ ] Understand DDD.
- [ ] Understand Domains and Subdomains.
- [ ] Understand Bounded Contexts.
- [ ] Understand Entities.
- [ ] Understand Value Objects.
- [ ] Understand Aggregates.
- [ ] Understand Aggregate Roots.
- [ ] Understand Domain Events.

### Design and Dependency Management

- [ ] Understand Repository Pattern.
- [ ] Understand Unit of Work.
- [ ] Understand Dependency Injection.
- [ ] Understand Inversion of Control.
- [ ] Understand Dependency Inversion.

### SOLID and Coding Principles

- [ ] Understand SRP.
- [ ] Understand OCP.
- [ ] Understand LSP.
- [ ] Understand ISP.
- [ ] Understand DIP.
- [ ] Understand DRY.
- [ ] Understand KISS.
- [ ] Understand YAGNI.

### Design Patterns

- [ ] Factory Pattern.
- [ ] Abstract Factory Pattern.
- [ ] Builder Pattern.
- [ ] Singleton Pattern.
- [ ] Adapter Pattern.
- [ ] Decorator Pattern.
- [ ] Facade Pattern.
- [ ] Proxy Pattern.
- [ ] Strategy Pattern.
- [ ] Observer Pattern.
- [ ] Command Pattern.
- [ ] Template Method Pattern.
- [ ] Mediator Pattern.
- [ ] Specification Pattern.

### Advanced Architecture

- [ ] Understand CQRS.
- [ ] Understand Event Sourcing.
- [ ] Understand Modular Monolith.
- [ ] Understand Enterprise Architecture.
- [ ] Understand Refactoring.
- [ ] Understand Technical Debt.
- [ ] Understand Architecture Decision Records.

### Practical Implementation

- [ ] Build a Layered Node.js application.
- [ ] Implement Clean Architecture.
- [ ] Implement Dependency Injection.
- [ ] Apply SOLID principles.
- [ ] Implement Repository Pattern.
- [ ] Implement Unit of Work.
- [ ] Implement Factory Pattern.
- [ ] Implement Strategy Pattern.
- [ ] Implement Observer Pattern.
- [ ] Implement CQRS.
- [ ] Build a Modular Monolith.
- [ ] Create an Enterprise Expense Tracker.

---

# Official Documentation and Learning Resources

| Resource | Documentation |
|---|---|
| Clean Architecture Concepts | [The Clean Architecture – Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) |
| Domain-Driven Design | [DDD Community](https://www.dddcommunity.org/) |
| Microsoft Architecture Guidance | [Microsoft Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/) |
| Martin Fowler – Patterns | [Martin Fowler](https://martinfowler.com/) |
| Refactoring | [Refactoring Guru](https://refactoring.guru/) |
| SOLID Principles | [Microsoft .NET Architecture Guidance](https://learn.microsoft.com/en-us/dotnet/architecture/) |
| Node.js | [Node.js Documentation](https://nodejs.org/docs/latest/api/) |
| TypeScript | [TypeScript Documentation](https://www.typescriptlang.org/docs/) |
| Express.js | [Express Documentation](https://expressjs.com/) |
| Next.js | [Next.js Documentation](https://nextjs.org/docs) |
| Sequelize | [Sequelize Documentation](https://sequelize.org/docs/v6/) |
| Prisma | [Prisma Documentation](https://www.prisma.io/docs) |

---

# Phase 61 – Final Summary

In this phase, you learned how professional software applications are structured and how to apply reusable architectural principles.

The most important concepts are:

1. **Layered Architecture:** Separates an application into responsibility-based layers.
2. **Clean Architecture:** Protects business logic from external dependencies.
3. **Hexagonal Architecture:** Uses ports and adapters to isolate application logic.
4. **Onion Architecture:** Places the domain model at the center.
5. **DDD:** Models software around business concepts.
6. **Bounded Contexts:** Define clear domain boundaries.
7. **Entities and Value Objects:** Represent domain concepts correctly.
8. **Repository Pattern:** Separates data persistence from application logic.
9. **Dependency Injection:** Provides dependencies externally.
10. **SOLID:** Improves object-oriented software design.
11. **Design Patterns:** Provide reusable approaches to common design problems.
12. **CQRS:** Separates read and write responsibilities.
13. **Event Sourcing:** Represents application state through persisted events.
14. **Modular Monolith:** Organizes one deployable application into independent modules.
15. **Refactoring:** Improves code structure without intentionally changing behavior.

### Recommended Practical Learning Order

| Order | Topic | Priority |
|---:|---|---|
| 1 | Layered Architecture | Essential |
| 2 | Clean Architecture | Essential |
| 3 | SOLID Principles | Essential |
| 4 | Dependency Injection | Essential |
| 5 | Repository Pattern | Essential |
| 6 | Factory and Strategy Patterns | Important |
| 7 | Observer and Adapter Patterns | Important |
| 8 | Domain-Driven Design | Advanced |
| 9 | CQRS and Event Sourcing | Advanced |
| 10 | Modular Monolith Architecture | Advanced |
| 11 | Enterprise Application Design | Expert |

---

# Next Phase Preview

## Phase 62 – Advanced TypeScript, Type System Internals and Enterprise TypeScript Development

Topics planned:

1. TypeScript Fundamentals Revision.
2. Type System Architecture.
3. Static vs Dynamic Typing.
4. Type Inference.
5. Type Annotations.
6. Structural Typing.
7. Nominal Typing Concepts.
8. Primitive and Reference Types.
9. Union Types.
10. Intersection Types.
11. Literal Types.
12. Type Aliases.
13. Interfaces.
14. Interface Declaration Merging.
15. Type Assertions.
16. Type Narrowing.
17. Control Flow Analysis.
18. Discriminated Unions.
19. Type Guards.
20. Generics.
21. Generic Constraints.
22. Generic Defaults.
23. Conditional Types.
24. Mapped Types.
25. Template Literal Types.
26. Indexed Access Types.
27. `keyof` Operator.
28. `typeof` Operator.
29. `infer` Keyword.
30. Utility Types.
31. Advanced Function Types.
32. Overloads.
33. Variadic Tuple Types.
34. Recursive Types.
35. Type-Level Programming.
36. Decorators.
37. Declaration Files.
38. Module Augmentation.
39. Namespaces vs Modules.
40. Advanced TypeScript Configuration.
41. Strict Mode.
42. TypeScript Compiler Internals.
43. TypeScript with Node.js.
44. TypeScript with Express.
45. TypeScript with React.
46. TypeScript with Next.js.
47. TypeScript with Sequelize.
48. TypeScript with Prisma.
49. Enterprise TypeScript Architecture.
50. Advanced TypeScript Interview Questions.

**Say "OK, Phase 62" when you're ready to continue.**