# Phase 69 – Advanced Software Architecture, Enterprise Design Patterns and Scalable Application Engineering

**Level:** Advanced to Expert  
**Domain:** Software Architecture, Design Patterns, Enterprise Application Development, Distributed Systems, System Design and Code Quality.

**Primary Technology Stack:**
- JavaScript / TypeScript
- Node.js
- Express.js
- React / Next.js
- MySQL / MongoDB
- Redis
- RabbitMQ / Kafka
- Docker
- Git
- Jest / Vitest
- AWS

---

# 1. Learning Objectives

In Phase 68, we studied how to design enterprise backend architectures, including Clean Architecture, Hexagonal Architecture, DDD, APIs, authentication, caching and production optimization.

In Phase 69, we will go deeper into **how to design maintainable software using proven architectural principles and design patterns**.

Consider an application that initially has only a few features:

```text
User
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

As it grows, it may contain hundreds of modules, thousands of functions and multiple integrations.

Without proper architecture, the codebase can become difficult to maintain.

For example:

```typescript
async function createOrder(data: any) {
    // Validate order

    // Check inventory

    // Calculate total

    // Process payment

    // Save order

    // Send email

    // Update analytics

    // Generate invoice
}
```

This function handles too many responsibilities.

A better architecture separates these responsibilities into independently testable components.

```text
                 Create Order
                      |
                      v
                Order Use Case
                      |
        +-------------+-------------+
        |             |             |
        v             v             v
    Inventory      Payment       Order
     Service       Service     Repository
                                    |
                                    v
                                 Database

                      |
                      v
                Event Publisher
                      |
                      v
                  Job Queue
                      |
             +--------+--------+
             |                 |
             v                 v
         Email Worker      Invoice Worker
```

This phase will teach you how to design such systems.

## Complete Phase 69 Roadmap

### Software Architecture Principles

1. Software Architecture Principles.
2. SOLID Principles.
3. DRY Principle.
4. KISS Principle.
5. YAGNI Principle.
6. Separation of Concerns.
7. High Cohesion.
8. Low Coupling.
9. Dependency Management.

### Creational Design Patterns

10. Singleton Pattern.
11. Factory Pattern.
12. Abstract Factory Pattern.
13. Builder Pattern.
14. Prototype Pattern.

### Structural Design Patterns

15. Adapter Pattern.
16. Decorator Pattern.
17. Facade Pattern.
18. Proxy Pattern.
19. Composite Pattern.
20. Bridge Pattern.

### Behavioral Design Patterns

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

### Enterprise Architecture Patterns

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

### Architecture Quality and Engineering

47. Architecture Decision Records.
48. Architecture Testing.
49. Refactoring Legacy Applications.
50. Complete Enterprise Architecture Project.

---

# PART 1: SOFTWARE ARCHITECTURE PRINCIPLES

## 2. What Are Software Architecture Principles?

Software Architecture Principles are fundamental guidelines used to design software systems that are:

- Maintainable.
- Scalable.
- Testable.
- Reusable.
- Extensible.
- Reliable.
- Understandable.

Consider two implementations.

### Poor Architecture

```typescript
class OrderService {
    async createOrder(data: any) {
        const mysql = require("mysql2");

        const connection = mysql.createConnection({
            host: "localhost",
            user: "root",
            password: "password"
        });

        // Database operations
        // Payment operations
        // Email operations
    }
}
```

Problems:

- Database configuration is embedded in business logic.
- The service is tightly coupled to MySQL.
- Payment and email responsibilities are mixed together.
- Testing becomes difficult.
- Changing infrastructure requires changing business logic.

### Better Architecture

```typescript
interface OrderRepository {
    save(order: Order): Promise<void>;
}

interface PaymentGateway {
    charge(amount: number): Promise<boolean>;
}

interface NotificationService {
    sendOrderConfirmation(order: Order): Promise<void>;
}

class CreateOrderUseCase {
    constructor(
        private orderRepository: OrderRepository,
        private paymentGateway: PaymentGateway,
        private notificationService: NotificationService
    ) {}

    async execute(order: Order) {
        const paymentSuccessful =
            await this.paymentGateway.charge(order.total);

        if (!paymentSuccessful) {
            throw new Error("Payment failed");
        }

        await this.orderRepository.save(order);

        await this.notificationService
            .sendOrderConfirmation(order);
    }
}
```

Now each dependency can have multiple implementations.

For example:

```text
PaymentGateway
      |
      +---- StripePaymentGateway
      |
      +---- RazorpayPaymentGateway
      |
      +---- FakePaymentGateway
```

The application logic does not need to know which implementation is being used.

---

# PART 2: SOLID PRINCIPLES

## 3. What Is SOLID?

SOLID is a collection of five object-oriented design principles.

| Letter | Principle |
|---|---|
| S | Single Responsibility Principle |
| O | Open/Closed Principle |
| L | Liskov Substitution Principle |
| I | Interface Segregation Principle |
| D | Dependency Inversion Principle |

These principles help create flexible and maintainable software.

---

## 4. Single Responsibility Principle (SRP)

**Definition:** A class or module should have one responsibility, or more precisely, one primary reason to change.

### Incorrect Example

```typescript
class UserService {
    createUser() {
        // Create user
    }

    sendEmail() {
        // Send email
    }

    generatePDF() {
        // Generate PDF
    }

    uploadImage() {
        // Upload image
    }
}
```

This class has multiple responsibilities.

Changes to email, PDF generation or image storage could all affect the same class.

### Better Example

```typescript
class UserService {
    createUser() {
        // Create user
    }
}

class EmailService {
    sendEmail() {
        // Send email
    }
}

class PDFService {
    generatePDF() {
        // Generate PDF
    }
}

class ImageService {
    uploadImage() {
        // Upload image
    }
}
```

### Architecture

```text
             Application
                  |
       +----------+----------+
       |          |          |
       v          v          v
   UserService EmailService PDFService
```

### Real-World Example

In an Expense Tracker:

- `ExpenseService` manages expense operations.
- `ReportService` generates reports.
- `EmailService` sends notifications.
- `FileService` handles attachments.

### Benefits

- Easier testing.
- Easier maintenance.
- Better separation of responsibilities.
- Reduced coupling.

**Important:** SRP does not mean creating one class for every individual function. It means grouping behavior that belongs to the same responsibility.

---

## 5. Open/Closed Principle (OCP)

**Definition:** Software entities should be open for extension but closed for modification.

This means you should be able to introduce new behavior without repeatedly changing stable existing code.

### Incorrect Example

```typescript
class PaymentService {
    processPayment(type: string, amount: number) {
        if (type === "stripe") {
            // Stripe payment
        }

        if (type === "razorpay") {
            // Razorpay payment
        }

        if (type === "paypal") {
            // PayPal payment
        }
    }
}
```

Every time you add a payment provider, you modify the same method.

### Better Example

Define an interface:

```typescript
interface PaymentGateway {
    processPayment(amount: number): Promise<void>;
}
```

Implement different providers:

```typescript
class StripePayment implements PaymentGateway {
    async processPayment(amount: number) {
        console.log("Stripe payment:", amount);
    }
}

class RazorpayPayment implements PaymentGateway {
    async processPayment(amount: number) {
        console.log("Razorpay payment:", amount);
    }
}

class PayPalPayment implements PaymentGateway {
    async processPayment(amount: number) {
        console.log("PayPal payment:", amount);
    }
}
```

Use the abstraction:

```typescript
class PaymentService {
    constructor(
        private gateway: PaymentGateway
    ) {}

    async pay(amount: number) {
        await this.gateway.processPayment(amount);
    }
}
```

Adding another payment provider does not require modifying `PaymentService`.

### Benefits

- Extensibility.
- Reduced conditional logic.
- Better testing.
- Lower regression risk.

---

## 6. Liskov Substitution Principle (LSP)

**Definition:** Objects of a subtype should be usable wherever objects of their parent type are expected without breaking the correctness of the program.

In simpler terms:

A child class should behave consistently with the contract established by its parent abstraction.

### Incorrect Example

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

The problem is that `Penguin` cannot correctly fulfill the behavior expected from `Bird`.

### Better Example

Separate the capabilities:

```typescript
interface Bird {
    eat(): void;
}

interface FlyingBird extends Bird {
    fly(): void;
}

class Sparrow implements FlyingBird {
    eat() {
        console.log("Eating");
    }

    fly() {
        console.log("Flying");
    }
}

class Penguin implements Bird {
    eat() {
        console.log("Eating");
    }
}
```

Now each implementation follows its contract.

### When Is LSP Important?

- Inheritance-heavy applications.
- Payment provider abstractions.
- Storage provider abstractions.
- Notification systems.
- Plugin architectures.

---

## 7. Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on methods they do not use.

### Incorrect Example

```typescript
interface Employee {
    work(): void;
    manageTeam(): void;
    approveExpenses(): void;
    generateReports(): void;
}
```

A normal employee may not need management or approval methods.

### Better Example

```typescript
interface Worker {
    work(): void;
}

interface Manager {
    manageTeam(): void;
}

interface ExpenseApprover {
    approveExpenses(): void;
}

interface ReportGenerator {
    generateReports(): void;
}
```

Now classes implement only the capabilities they require.

```typescript
class Developer implements Worker {
    work() {
        console.log("Writing code");
    }
}

class FinanceManager implements Worker, ExpenseApprover {
    work() {
        console.log("Managing finance");
    }

    approveExpenses() {
        console.log("Approving expenses");
    }
}
```

### Benefits

- Smaller interfaces.
- Less unnecessary dependency.
- Easier implementation.
- Better modularity.

---

## 8. Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend directly on low-level modules. Both should depend on abstractions.

### Incorrect Architecture

```text
OrderService
     |
     v
MySQLRepository
     |
     v
MySQL Database
```

The high-level business service depends directly on a specific database implementation.

### Better Architecture

```text
             OrderService
                  |
                  v
          OrderRepository Interface
                  |
          +-------+-------+
          |               |
          v               v
      MySQL Repo      MongoDB Repo
```

### Example

```typescript
interface OrderRepository {
    save(order: Order): Promise<void>;
}

class MySQLOrderRepository implements OrderRepository {
    async save(order: Order) {
        console.log("Saving in MySQL");
    }
}

class MongoOrderRepository implements OrderRepository {
    async save(order: Order) {
        console.log("Saving in MongoDB");
    }
}

class OrderService {
    constructor(
        private repository: OrderRepository
    ) {}

    async create(order: Order) {
        await this.repository.save(order);
    }
}
```

### SOLID Revision

| Principle | Main Idea |
|---|---|
| SRP | One primary responsibility |
| OCP | Extend without repeatedly modifying stable code |
| LSP | Subtypes must honor their contracts |
| ISP | Small, focused interfaces |
| DIP | Depend on abstractions |

---

# PART 3: DRY, KISS AND YAGNI

## 9. DRY Principle

DRY stands for **Don't Repeat Yourself**.

The goal is to avoid unnecessary duplication of knowledge or logic.

### Incorrect Example

```typescript
function calculateProductTotal(price: number, quantity: number) {
    return price * quantity;
}

function calculateOrderTotal(price: number, quantity: number) {
    return price * quantity;
}

function calculateCartTotal(price: number, quantity: number) {
    return price * quantity;
}
```

The same calculation is duplicated.

### Better Example

```typescript
function calculateTotal(price: number, quantity: number) {
    return price * quantity;
}
```

Use it everywhere:

```typescript
calculateTotal(100, 2);
calculateTotal(200, 3);
calculateTotal(500, 1);
```

### Important Warning

DRY does not mean that every similar-looking piece of code must be shared.

Two pieces of code may look similar but represent different business rules that are expected to evolve independently.

Prematurely combining them can create excessive coupling.

---

## 10. KISS Principle

KISS stands for **Keep It Simple, Stupid**.

The principle encourages simple solutions instead of unnecessary complexity.

### Complicated Example

```typescript
function isAdult(age: number): boolean {
    return age >= 18 ? true : false;
}
```

### Simpler Example

```typescript
function isAdult(age: number): boolean {
    return age >= 18;
}
```

### Another Example

Instead of creating an elaborate abstraction for a simple calculation:

```typescript
class ComplexCalculationFactory {
    createCalculationStrategy() {
        return new CalculationStrategy();
    }
}
```

A simple function may be enough:

```typescript
function calculateDiscount(price: number, discount: number) {
    return price - (price * discount) / 100;
}
```

**KISS does not mean avoiding architecture. It means using only the complexity needed to solve the problem.**

---

## 11. YAGNI Principle

YAGNI stands for **You Aren't Gonna Need It**.

Do not build features or abstractions before there is a real requirement for them.

### Example

You are developing an expense tracker.

Current requirement:

```text
Users can create expenses.
```

You decide to implement:

- Multi-region deployment.
- Ten different payment providers.
- A plugin marketplace.
- A distributed event-sourcing platform.

These features may never be required.

YAGNI encourages implementing the actual requirement first.

### DRY vs KISS vs YAGNI

| Principle | Focus |
|---|---|
| DRY | Avoid unnecessary duplication |
| KISS | Keep solutions simple |
| YAGNI | Avoid unnecessary future functionality |

---

# PART 4: SEPARATION OF CONCERNS

## 12. What Is Separation of Concerns?

Separation of Concerns means dividing software into components with distinct responsibilities.

Example:

```text
                 Express Application
                         |
           +-------------+-------------+
           |             |             |
           v             v             v
       Controller      Service      Repository
           |             |             |
           v             v             v
       HTTP Logic    Business Logic  Data Access
```

### Controller

Handles HTTP requests and responses.

```typescript
async function createUserController(req: any, res: any) {
    const user = await userService.createUser(req.body);

    res.status(201).json(user);
}
```

### Service

Handles business logic.

```typescript
class UserService {
    async createUser(data: any) {
        if (!data.email) {
            throw new Error("Email required");
        }

        return userRepository.create(data);
    }
}
```

### Repository

Handles database operations.

```typescript
class UserRepository {
    async create(data: any) {
        return User.create(data);
    }
}
```

### Why Is It Important?

If you change the database from MySQL to MongoDB, the controller should not need to change simply because the database changed.

Similarly, if you change the HTTP framework, the core business rules should remain largely unaffected.

---

# PART 5: HIGH COHESION AND LOW COUPLING

## 13. What Is Cohesion?

Cohesion describes how closely related the responsibilities within a module are.

**High Cohesion** means the module focuses on a well-defined purpose.

Example:

```text
Payment Module
    |
    +---- Process Payment
    |
    +---- Refund Payment
    |
    +---- Verify Payment
    |
    +---- Payment Status
```

All operations are related to payment management.

### Low Cohesion Example

```text
Utility Module
    |
    +---- Create User
    |
    +---- Send Email
    |
    +---- Calculate Tax
    |
    +---- Generate Invoice
    |
    +---- Upload Image
```

These responsibilities are unrelated.

## 14. What Is Coupling?

Coupling describes how strongly one module depends on another.

### Tight Coupling

```text
OrderService
     |
     v
MySQLOrderRepository
     |
     v
MySQL
```

Changing the database implementation directly affects the service.

### Loose Coupling

```text
OrderService
     |
     v
OrderRepository Interface
     |
     +---- MySQL
     |
     +---- MongoDB
```

The service depends on an abstraction.

### High Cohesion vs Low Coupling

| High Cohesion | Low Coupling |
|---|---|
| Related responsibilities stay together | Unnecessary dependencies are reduced |
| Focuses on module organization | Focuses on module relationships |
| Improves readability | Improves flexibility |
| Makes modules easier to understand | Makes modules easier to change independently |

The desired architecture generally aims for **high cohesion and low coupling**.

---

# PART 6: DEPENDENCY MANAGEMENT

## 15. What Is Dependency Management?

Dependency Management controls how software components depend on other components.

Consider:

```typescript
class OrderService {
    private repository = new MySQLOrderRepository();
    private payment = new StripePaymentGateway();
}
```

This class creates its dependencies internally.

It becomes difficult to replace them or test the service independently.

A better approach:

```typescript
class OrderService {
    constructor(
        private repository: OrderRepository,
        private payment: PaymentGateway
    ) {}
}
```

Dependencies are provided externally.

### Composition Root

A Composition Root is the place where application dependencies are constructed and connected.

Example:

```typescript
const orderRepository = new MySQLOrderRepository();

const paymentGateway = new StripePaymentGateway();

const orderService = new OrderService(
    orderRepository,
    paymentGateway
);
```

In a large application, this wiring is usually centralized in application startup or a dependency container.

### Benefits

- Easier testing.
- Easier replacement of implementations.
- Centralized configuration.
- Explicit dependencies.
- Reduced coupling.

---

# PART 7: CREATIONAL DESIGN PATTERNS

## 16. What Are Creational Design Patterns?

Creational Design Patterns deal with object creation.

They help control how objects are instantiated and configured.

The five patterns we will study are:

1. Singleton.
2. Factory.
3. Abstract Factory.
4. Builder.
5. Prototype.

---

## 17. Singleton Pattern

### Definition

The Singleton Pattern ensures that a class has only one shared instance within a given application context.

### Example Use Cases

- Application configuration.
- Shared connection manager.
- Logger.
- Certain caches.

### Example

```typescript
class Logger {
    private static instance: Logger;

    private constructor() {}

    static getInstance(): Logger {
        if (!Logger.instance) {
            Logger.instance = new Logger();
        }

        return Logger.instance;
    }

    log(message: string) {
        console.log(message);
    }
}
```

Usage:

```typescript
const logger1 = Logger.getInstance();

const logger2 = Logger.getInstance();

console.log(logger1 === logger2);
```

Output:

```text
true
```

### Architecture

```text
         Logger.getInstance()
                 |
                 v
           Logger Instance
                 |
          +------+------+
          |             |
          v             v
        User          Order
       Module         Module
```

### Advantages

- Controlled instance creation.
- Shared access.
- Useful for certain application-wide resources.

### Disadvantages

- Introduces global shared state.
- Can make testing difficult.
- Can hide dependencies.
- Can create concurrency concerns in some designs.

**Important:** A Singleton in Node.js generally means one instance within a module/runtime context, not necessarily one instance across all processes or servers.

---

## 18. Factory Pattern

### Definition

The Factory Pattern centralizes object creation and hides the concrete class selection from the caller.

### Example

Suppose your application supports different notification channels.

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

class SMSNotification implements Notification {
    send(message: string) {
        console.log("SMS:", message);
    }
}

class PushNotification implements Notification {
    send(message: string) {
        console.log("Push:", message);
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
                return new SMSNotification();

            case "push":
                return new PushNotification();

            default:
                throw new Error("Unsupported notification type");
        }
    }
}
```

Usage:

```typescript
const notification = NotificationFactory.create("email");

notification.send("Your order is confirmed");
```

### Advantages

- Centralizes object creation.
- Hides implementation details.
- Simplifies callers.
- Useful for provider selection.

### Factory Pattern Use Cases

- Payment providers.
- Notification providers.
- Database adapters.
- File storage providers.
- Export formats.

---

## 19. Abstract Factory Pattern

### Definition

Abstract Factory provides an interface for creating families of related objects without specifying their concrete classes.

### Example

Suppose your application supports two UI themes:

- Light Theme.
- Dark Theme.

Each theme needs:

- Button.
- Input.
- Modal.

### Interfaces

```typescript
interface Button {
    render(): void;
}

interface Input {
    render(): void;
}
```

Light Theme:

```typescript
class LightButton implements Button {
    render() {
        console.log("Light Button");
    }
}

class LightInput implements Input {
    render() {
        console.log("Light Input");
    }
}
```

Dark Theme:

```typescript
class DarkButton implements Button {
    render() {
        console.log("Dark Button");
    }
}

class DarkInput implements Input {
    render() {
        console.log("Dark Input");
    }
}
```

Abstract Factory:

```typescript
interface UIFactory {
    createButton(): Button;
    createInput(): Input;
}
```

Concrete Factories:

```typescript
class LightThemeFactory implements UIFactory {
    createButton() {
        return new LightButton();
    }

    createInput() {
        return new LightInput();
    }
}

class DarkThemeFactory implements UIFactory {
    createButton() {
        return new DarkButton();
    }

    createInput() {
        return new DarkInput();
    }
}
```

### Usage

```typescript
function createUI(factory: UIFactory) {
    const button = factory.createButton();
    const input = factory.createInput();

    button.render();
    input.render();
}

createUI(new DarkThemeFactory());
```

### Factory vs Abstract Factory

| Factory | Abstract Factory |
|---|---|
| Creates an individual product or implementation | Creates families of related products |
| Often selects one concrete class | Selects a related product family |
| Simpler | More elaborate |
| Example: Notification factory | Example: Light and dark UI families |

---

## 20. Builder Pattern

### Definition

The Builder Pattern constructs complex objects step by step.

It is useful when an object has many optional or configurable properties.

### Without Builder

```typescript
const user = {
    name: "Riyaz",
    email: "riyaz@example.com",
    phone: "9999999999",
    address: "Ahmedabad",
    role: "admin",
    active: true
};
```

This is fine for a simple object, but complex construction may involve many conditions and validation rules.

### Builder Example

```typescript
class User {
    constructor(
        public name: string,
        public email: string,
        public phone?: string,
        public address?: string,
        public role?: string
    ) {}
}

class UserBuilder {
    private name = "";
    private email = "";
    private phone?: string;
    private address?: string;
    private role?: string;

    setName(name: string) {
        this.name = name;
        return this;
    }

    setEmail(email: string) {
        this.email = email;
        return this;
    }

    setPhone(phone: string) {
        this.phone = phone;
        return this;
    }

    setAddress(address: string) {
        this.address = address;
        return this;
    }

    setRole(role: string) {
        this.role = role;
        return this;
    }

    build() {
        if (!this.name || !this.email) {
            throw new Error("Name and email are required");
        }

        return new User(
            this.name,
            this.email,
            this.phone,
            this.address,
            this.role
        );
    }
}
```

Usage:

```typescript
const user = new UserBuilder()
    .setName("Riyaz")
    .setEmail("riyaz@example.com")
    .setRole("admin")
    .build();
```

### When to Use Builder?

- Complex configuration objects.
- Report generation.
- Query construction.
- Test data creation.
- Complex domain object creation.

---

## 21. Prototype Pattern

### Definition

The Prototype Pattern creates new objects by copying an existing object.

### Example

```typescript
const originalUser = {
    name: "Riyaz",
    role: "admin",
    settings: {
        theme: "dark"
    }
};

const copiedUser = structuredClone(originalUser);

copiedUser.name = "Ahmed";

console.log(originalUser.name);
console.log(copiedUser.name);
```

Output:

```text
Riyaz
Ahmed
```

### Shallow Copy vs Deep Copy

Shallow copy:

```typescript
const copy = { ...originalUser };
```

Nested objects remain shared references.

Deep copy:

```typescript
const copy = structuredClone(originalUser);
```

For supported structured-cloneable values, this creates an independent copy of nested data.

### When to Use Prototype?

- Creating objects from templates.
- Cloning configuration.
- Reusing predefined object structures.
- Generating test data.

---

# PART 8: STRUCTURAL DESIGN PATTERNS

## 22. What Are Structural Design Patterns?

Structural Design Patterns describe how objects and classes are composed to form larger structures.

We will study:

1. Adapter.
2. Decorator.
3. Facade.
4. Proxy.
5. Composite.
6. Bridge.

---

## 23. Adapter Pattern

### Definition

The Adapter Pattern allows incompatible interfaces to work together.

### Real-World Example

Your application expects a payment interface:

```typescript
interface PaymentGateway {
    pay(amount: number): Promise<void>;
}
```

But an external provider exposes:

```typescript
class ExternalPaymentProvider {
    async makePaymentInPaise(amount: number) {
        console.log("Paid:", amount);
    }
}
```

The method name and expected unit differ.

Create an adapter:

```typescript
class PaymentAdapter implements PaymentGateway {
    constructor(
        private provider: ExternalPaymentProvider
    ) {}

    async pay(amount: number) {
        await this.provider.makePaymentInPaise(
            Math.round(amount * 100)
        );
    }
}
```

Usage:

```typescript
const provider = new ExternalPaymentProvider();

const gateway = new PaymentAdapter(provider);

await gateway.pay(500);
```

### Architecture

```text
Application
     |
     v
PaymentGateway Interface
     |
     v
PaymentAdapter
     |
     v
External Payment Provider
```

### Use Cases

- Third-party API integrations.
- Legacy system integration.
- Different payment providers.
- Cloud storage providers.
- External logging systems.

---

## 24. Decorator Pattern

### Definition

The Decorator Pattern adds behavior to an object without modifying its original class.

### Example

Suppose you have a notification service.

```typescript
interface NotificationService {
    send(message: string): void;
}

class BasicNotification implements NotificationService {
    send(message: string) {
        console.log("Notification:", message);
    }
}
```

Create a decorator:

```typescript
class LoggingNotificationDecorator
    implements NotificationService {

    constructor(
        private notification: NotificationService
    ) {}

    send(message: string) {
        console.log("Logging notification");

        this.notification.send(message);
    }
}
```

Another decorator:

```typescript
class TimestampNotificationDecorator
    implements NotificationService {

    constructor(
        private notification: NotificationService
    ) {}

    send(message: string) {
        const timestamp = new Date().toISOString();

        this.notification.send(
            `${timestamp}: ${message}`
        );
    }
}
```

Usage:

```typescript
const basic = new BasicNotification();

const logging = new LoggingNotificationDecorator(basic);

const decorated =
    new TimestampNotificationDecorator(logging);

decorated.send("Order created");
```

### Architecture

```text
          Timestamp Decorator
                  |
                  v
           Logging Decorator
                  |
                  v
           Basic Notification
```

### Use Cases

- Logging.
- Caching.
- Authorization.
- Metrics.
- Compression.
- Request instrumentation.

**Note:** JavaScript and TypeScript also have language-level decorator features. The design pattern described here is an object-composition pattern, not necessarily the same thing as TypeScript decorator syntax.

---

## 25. Facade Pattern

### Definition

The Facade Pattern provides a simplified interface to a complex subsystem.

### Example

Placing an order may require several operations:

```text
Order
 |
 +---- Inventory
 |
 +---- Payment
 |
 +---- Shipping
 |
 +---- Notification
```

Without a facade, the caller may need to coordinate every service.

### Implementation

```typescript
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
    constructor(
        private inventory: InventoryService,
        private payment: PaymentService,
        private shipping: ShippingService
    ) {}

    placeOrder() {
        this.inventory.reserve();

        this.payment.charge();

        this.shipping.createShipment();
    }
}
```

Usage:

```typescript
const facade = new OrderFacade(
    new InventoryService(),
    new PaymentService(),
    new ShippingService()
);

facade.placeOrder();
```

### Benefits

- Simplifies complex operations.
- Reduces caller knowledge of internal components.
- Provides a clear entry point.

**Important:** A facade should not become a giant class containing all business logic. It should coordinate a subsystem through a simpler interface.

---

## 26. Proxy Pattern

### Definition

The Proxy Pattern provides a substitute object that controls access to another object.

### Example: Authorization Proxy

```typescript
interface DocumentService {
    readDocument(): string;
}

class RealDocumentService implements DocumentService {
    readDocument() {
        return "Confidential document";
    }
}

class DocumentProxy implements DocumentService {
    constructor(
        private realService: DocumentService,
        private isAuthorized: boolean
    ) {}

    readDocument() {
        if (!this.isAuthorized) {
            throw new Error("Access denied");
        }

        return this.realService.readDocument();
    }
}
```

Usage:

```typescript
const service = new DocumentProxy(
    new RealDocumentService(),
    true
);

console.log(service.readDocument());
```

### Types of Proxies

- Virtual Proxy.
- Protection Proxy.
- Remote Proxy.
- Caching Proxy.
- Logging Proxy.

### Use Cases

- Authorization checks.
- Lazy initialization.
- Caching.
- Remote service communication.
- Request interception.

---

## 27. Composite Pattern

### Definition

The Composite Pattern organizes objects into tree structures and allows individual objects and groups to be treated uniformly.

### Example: File System

```text
Root
 |
 +---- Documents
 |        |
 |        +---- resume.pdf
 |        +---- notes.txt
 |
 +---- Images
          |
          +---- photo.jpg
```

### Implementation

```typescript
interface FileSystemItem {
    getSize(): number;
}

class File implements FileSystemItem {
    constructor(
        private size: number
    ) {}

    getSize() {
        return this.size;
    }
}

class Folder implements FileSystemItem {
    private items: FileSystemItem[] = [];

    add(item: FileSystemItem) {
        this.items.push(item);
    }

    getSize() {
        return this.items.reduce(
            (total, item) => total + item.getSize(),
            0
        );
    }
}
```

Usage:

```typescript
const root = new Folder();

root.add(new File(100));

const documents = new Folder();

documents.add(new File(200));
documents.add(new File(300));

root.add(documents);

console.log(root.getSize());
```

Output:

```text
600
```

### Use Cases

- File systems.
- Organization hierarchies.
- UI component trees.
- Product category trees.
- Permission hierarchies.

---

## 28. Bridge Pattern

### Definition

The Bridge Pattern separates an abstraction from its implementation so that both can evolve independently.

### Example

Suppose you support different notification types and delivery providers.

Notification types:

- Alert.
- Reminder.

Delivery providers:

- Email.
- SMS.

Without Bridge, you might create:

```text
EmailAlert
EmailReminder
SMSAlert
SMSReminder
```

As combinations grow, the number of classes increases.

### Better Architecture

```text
Notification
     |
     +---- Alert
     |
     +---- Reminder
             |
             v
       MessageSender
             |
        +----+----+
        |         |
        v         v
      Email      SMS
```

### Example

```typescript
interface MessageSender {
    send(message: string): void;
}

class EmailSender implements MessageSender {
    send(message: string) {
        console.log("Email:", message);
    }
}

class SMSSender implements MessageSender {
    send(message: string) {
        console.log("SMS:", message);
    }
}

abstract class Notification {
    constructor(
        protected sender: MessageSender
    ) {}

    abstract notify(message: string): void;
}

class AlertNotification extends Notification {
    notify(message: string) {
        this.sender.send(`ALERT: ${message}`);
    }
}

class ReminderNotification extends Notification {
    notify(message: string) {
        this.sender.send(`REMINDER: ${message}`);
    }
}
```

Usage:

```typescript
const alert = new AlertNotification(
    new EmailSender()
);

alert.notify("Server is down");
```

### Adapter vs Bridge

| Adapter | Bridge |
|---|---|
| Makes incompatible interfaces work together | Separates abstractions from implementations |
| Often used when integrating existing systems | Often designed into the architecture |
| Focuses on compatibility | Focuses on independent variation |

---

# PART 9: BEHAVIORAL DESIGN PATTERNS

## 29. What Are Behavioral Design Patterns?

Behavioral patterns define how objects communicate, coordinate and distribute responsibilities.

We will study:

1. Strategy.
2. Observer.
3. Command.
4. State.
5. Chain of Responsibility.
6. Template Method.
7. Mediator.
8. Iterator.
9. Visitor.
10. Specification.

---

## 30. Strategy Pattern

### Definition

The Strategy Pattern defines a family of algorithms and makes them interchangeable.

### Example: Discount Calculation

Suppose an e-commerce application supports:

- Regular discount.
- Festival discount.
- Premium customer discount.

Without Strategy:

```typescript
function calculateDiscount(
    type: string,
    price: number
) {
    if (type === "regular") {
        return price * 0.05;
    }

    if (type === "festival") {
        return price * 0.20;
    }

    if (type === "premium") {
        return price * 0.15;
    }

    return 0;
}
```

### Better Implementation

```typescript
interface DiscountStrategy {
    calculate(price: number): number;
}

class RegularDiscount implements DiscountStrategy {
    calculate(price: number) {
        return price * 0.05;
    }
}

class FestivalDiscount implements DiscountStrategy {
    calculate(price: number) {
        return price * 0.20;
    }
}

class PremiumDiscount implements DiscountStrategy {
    calculate(price: number) {
        return price * 0.15;
    }
}
```

Context:

```typescript
class DiscountService {
    constructor(
        private strategy: DiscountStrategy
    ) {}

    calculate(price: number) {
        return this.strategy.calculate(price);
    }
}
```

Usage:

```typescript
const discount = new DiscountService(
    new FestivalDiscount()
);

console.log(discount.calculate(1000));
```

Output:

```text
200
```

### Use Cases

- Payment calculation.
- Shipping calculation.
- Tax calculation.
- Discount calculation.
- Sorting algorithms.
- Authentication providers.

---

## 31. Observer Pattern

### Definition

The Observer Pattern establishes a one-to-many relationship where multiple observers receive notifications when a subject changes.

### Example

When an order is created:

- Email notification is sent.
- Inventory is updated.
- Analytics is recorded.
- Notification is sent to the user.

```text
                 Order Created
                       |
                       v
                  Event Subject
                       |
        +--------------+--------------+
        |              |              |
        v              v              v
    Email Observer Inventory      Analytics
                    Observer       Observer
```

### Implementation

```typescript
type Observer = (event: string) => void;

class EventEmitter {
    private observers: Observer[] = [];

    subscribe(observer: Observer) {
        this.observers.push(observer);
    }

    unsubscribe(observer: Observer) {
        this.observers = this.observers.filter(
            item => item !== observer
        );
    }

    notify(event: string) {
        for (const observer of this.observers) {
            observer(event);
        }
    }
}
```

Usage:

```typescript
const orderEvents = new EventEmitter();

const emailObserver = (event: string) => {
    console.log("Email:", event);
};

const analyticsObserver = (event: string) => {
    console.log("Analytics:", event);
};

orderEvents.subscribe(emailObserver);
orderEvents.subscribe(analyticsObserver);

orderEvents.notify("ORDER_CREATED");
```

### Observer vs Pub/Sub

| Observer | Pub/Sub |
|---|---|
| Subject directly maintains observers | Publisher and subscriber are commonly decoupled through a broker or event channel |
| Often in-process | Often supports distributed communication |
| Simpler | More infrastructure may be required |
| Example: UI state listeners | Example: Kafka consumers |

---

## 32. Command Pattern

### Definition

The Command Pattern encapsulates a request as an object.

This allows operations to be queued, logged, retried or undone where appropriate.

### Example: Expense Operations

```typescript
interface Command {
    execute(): Promise<void>;
}

class CreateExpenseCommand implements Command {
    constructor(
        private amount: number
    ) {}

    async execute() {
        console.log("Creating expense:", this.amount);
    }
}
```

Command Invoker:

```typescript
class CommandInvoker {
    async execute(command: Command) {
        await command.execute();
    }
}
```

Usage:

```typescript
const command = new CreateExpenseCommand(500);

const invoker = new CommandInvoker();

await invoker.execute(command);
```

### Use Cases

- Background jobs.
- Task queues.
- Undo/redo operations.
- Audit trails.
- Workflow execution.
- Scheduled operations.

### Command vs Strategy

| Command | Strategy |
|---|---|
| Encapsulates an action or request | Encapsulates an algorithm |
| Often supports queuing and execution tracking | Selects interchangeable behavior |
| Example: Create Order | Example: Calculate Discount |

---

## 33. State Pattern

### Definition

The State Pattern allows an object's behavior to change based on its internal state.

### Example: Order Lifecycle

```text
Created
   |
   v
Pending Payment
   |
   v
Paid
   |
   v
Processing
   |
   v
Shipped
   |
   v
Delivered
```

An order should not be allowed to move from every state to every other state.

### Implementation

```typescript
interface OrderState {
    pay(order: Order): void;
    ship(order: Order): void;
    deliver(order: Order): void;
}
```

Order class:

```typescript
class Order {
    private state: OrderState;

    constructor(initialState: OrderState) {
        this.state = initialState;
    }

    setState(state: OrderState) {
        this.state = state;
    }

    pay() {
        this.state.pay(this);
    }

    ship() {
        this.state.ship(this);
    }

    deliver() {
        this.state.deliver(this);
    }
}
```

Pending state:

```typescript
class PendingState implements OrderState {
    pay(order: Order) {
        console.log("Payment completed");

        order.setState(new PaidState());
    }

    ship() {
        throw new Error("Cannot ship unpaid order");
    }

    deliver() {
        throw new Error("Cannot deliver unpaid order");
    }
}
```

Paid state:

```typescript
class PaidState implements OrderState {
    pay() {
        throw new Error("Already paid");
    }

    ship(order: Order) {
        console.log("Order shipped");

        order.setState(new ShippedState());
    }

    deliver() {
        throw new Error("Order has not shipped");
    }
}
```

Shipped state:

```typescript
class ShippedState implements OrderState {
    pay() {
        throw new Error("Already paid");
    }

    ship() {
        throw new Error("Already shipped");
    }

    deliver() {
        console.log("Order delivered");
    }
}
```

### Benefits

- Centralized state-specific behavior.
- Explicit transition rules.
- Easier testing.
- Reduced complex conditional statements.

**Important:** For critical workflows, persist state transitions and enforce them transactionally. An in-memory State object alone is not a complete production workflow implementation.

---

## 34. Chain of Responsibility Pattern

### Definition

The Chain of Responsibility Pattern passes a request through a sequence of handlers.

Each handler decides whether to process the request, reject it or pass it to the next handler.

### Example: Express Middleware

```text
Incoming Request
       |
       v
Authentication
       |
       v
Authorization
       |
       v
Validation
       |
       v
Controller
```

### Example

```typescript
interface Handler {
    setNext(handler: Handler): Handler;

    handle(request: any): any;
}

abstract class BaseHandler implements Handler {
    private nextHandler?: Handler;

    setNext(handler: Handler): Handler {
        this.nextHandler = handler;

        return handler;
    }

    handle(request: any): any {
        if (this.nextHandler) {
            return this.nextHandler.handle(request);
        }
    }
}
```

A handler:

```typescript
class AuthenticationHandler extends BaseHandler {
    handle(request: any) {
        if (!request.user) {
            throw new Error("Authentication required");
        }

        console.log("Authenticated");

        return super.handle(request);
    }
}
```

Another handler:

```typescript
class ValidationHandler extends BaseHandler {
    handle(request: any) {
        if (!request.body?.amount) {
            throw new Error("Invalid amount");
        }

        console.log("Validated");

        return super.handle(request);
    }
}
```

Usage:

```typescript
const auth = new AuthenticationHandler();

const validation = new ValidationHandler();

auth.setNext(validation);

auth.handle({
    user: { id: 101 },
    body: { amount: 500 }
});
```

### Use Cases

- Express middleware.
- Authentication pipelines.
- Validation pipelines.
- Request processing.
- Logging pipelines.

---

## 35. Template Method Pattern

### Definition

The Template Method Pattern defines the overall structure of an algorithm while allowing subclasses to customize specific steps.

### Example: Report Generation

Different report types share a common process:

```text
Generate Report
      |
      v
Fetch Data
      |
      v
Format Data
      |
      v
Export Report
```

The formatting or exporting steps may differ.

### Implementation

```typescript
abstract class ReportGenerator {
    async generate() {
        const data = await this.fetchData();

        const formatted = this.formatData(data);

        await this.export(formatted);
    }

    abstract fetchData(): Promise<any>;

    abstract formatData(data: any): any;

    abstract export(data: any): Promise<void>;
}
```

PDF Report:

```typescript
class PDFReportGenerator extends ReportGenerator {
    async fetchData() {
        return ["Expense A", "Expense B"];
    }

    formatData(data: any) {
        return data.join("\n");
    }

    async export(data: any) {
        console.log("Exporting PDF:", data);
    }
}
```

### Use Cases

- Report generation.
- Import pipelines.
- Data processing.
- File conversion.
- Payment workflows.

---

## 36. Mediator Pattern

### Definition

The Mediator Pattern centralizes communication between multiple objects to reduce direct dependencies between them.

### Without Mediator

```text
User Module ------ Order Module
    |                  |
    |                  |
    v                  v
Payment Module ---- Email Module
```

Modules communicate directly with one another.

### With Mediator

```text
          User Module
               |
               v
            Mediator
          /    |    \
         v     v     v
      Order Payment Email
```

### Example

```typescript
class OrderMediator {
    constructor(
        private paymentService: PaymentService,
        private notificationService: NotificationService
    ) {}

    async processOrder(order: any) {
        await this.paymentService.pay(order.total);

        await this.notificationService.send(
            "Order processed"
        );
    }
}
```

### Use Cases

- Complex UI interactions.
- Workflow coordination.
- Application orchestration.
- Communication between independent components.

### Mediator vs Observer

Observer notifies subscribers of events.

Mediator coordinates interactions between components.

---

## 37. Iterator Pattern

### Definition

The Iterator Pattern provides a standard way to traverse a collection without exposing its internal structure.

JavaScript already supports iteration through the iterable protocol.

Example:

```typescript
const users = ["Riyaz", "Ahmed", "Ali"];

for (const user of users) {
    console.log(user);
}
```

Custom iterator:

```typescript
class NumberCollection {
    private numbers = [10, 20, 30];

    [Symbol.iterator]() {
        let index = 0;

        const numbers = this.numbers;

        return {
            next() {
                if (index < numbers.length) {
                    return {
                        value: numbers[index++],
                        done: false
                    };
                }

                return {
                    value: undefined,
                    done: true
                };
            }
        };
    }
}
```

Usage:

```typescript
const collection = new NumberCollection();

for (const number of collection) {
    console.log(number);
}
```

### Use Cases

- Collections.
- Pagination.
- Streaming data.
- Custom data structures.

---

## 38. Visitor Pattern

### Definition

The Visitor Pattern separates operations from the object structures on which they operate.

It is useful when you need to add new operations to a relatively stable object hierarchy.

### Example

Suppose you have different document types:

```typescript
interface Document {
    accept(visitor: DocumentVisitor): void;
}

interface DocumentVisitor {
    visitPDF(pdf: PDFDocument): void;
    visitWord(word: WordDocument): void;
}
```

Concrete objects:

```typescript
class PDFDocument implements Document {
    accept(visitor: DocumentVisitor) {
        visitor.visitPDF(this);
    }
}

class WordDocument implements Document {
    accept(visitor: DocumentVisitor) {
        visitor.visitWord(this);
    }
}
```

Visitor:

```typescript
class ExportVisitor implements DocumentVisitor {
    visitPDF(pdf: PDFDocument) {
        console.log("Exporting PDF");
    }

    visitWord(word: WordDocument) {
        console.log("Exporting Word");
    }
}
```

Usage:

```typescript
const visitor = new ExportVisitor();

const pdf = new PDFDocument();

pdf.accept(visitor);
```

### Use Cases

- Compiler abstract syntax trees.
- Document processing.
- Complex object structure operations.

### Trade-Off

Adding new visitor operations is easy, but adding new element types can require modifying every visitor.

---

## 39. Specification Pattern

### Definition

The Specification Pattern encapsulates business rules as reusable, composable objects.

### Example

An expense is eligible for approval only when:

- Amount is less than a specified limit.
- Status is pending.
- Category is permitted.

### Implementation

```typescript
interface Specification<T> {
    isSatisfiedBy(candidate: T): boolean;
}
```

Create a specification:

```typescript
type Expense = {
    amount: number;
    status: string;
};

class AmountSpecification
    implements Specification<Expense> {

    constructor(
        private maximumAmount: number
    ) {}

    isSatisfiedBy(expense: Expense) {
        return expense.amount <= this.maximumAmount;
    }
}
```

Another specification:

```typescript
class PendingSpecification
    implements Specification<Expense> {

    isSatisfiedBy(expense: Expense) {
        return expense.status === "pending";
    }
}
```

Combine them:

```typescript
class AndSpecification<T> implements Specification<T> {
    constructor(
        private first: Specification<T>,
        private second: Specification<T>
    ) {}

    isSatisfiedBy(candidate: T) {
        return (
            this.first.isSatisfiedBy(candidate) &&
            this.second.isSatisfiedBy(candidate)
        );
    }
}
```

Usage:

```typescript
const eligibleExpense = new AndSpecification(
    new AmountSpecification(5000),
    new PendingSpecification()
);

const expense = {
    amount: 3000,
    status: "pending"
};

console.log(
    eligibleExpense.isSatisfiedBy(expense)
);
```

Output:

```text
true
```

### Use Cases

- Complex business validation.
- Eligibility rules.
- Pricing rules.
- Approval policies.
- Search filters.

---

# PART 10: ENTERPRISE ARCHITECTURE PATTERNS

Now we move from object-level design patterns to patterns used in enterprise and distributed applications.

These patterns help solve problems that appear when applications communicate across modules, services, databases and message brokers.

---

## 40. CQRS – Command Query Responsibility Segregation

### Definition

CQRS separates operations that change application state from operations that retrieve application state.

There are two primary sides:

**Command Side:** Changes data.

**Query Side:** Reads data.

### Traditional Architecture

```text
             Application
                  |
          +-------+-------+
          |               |
          v               v
        Write            Read
          |               |
          +-------+-------+
                  |
                  v
                MySQL
```

### CQRS Architecture

```text
              Application
                   |
          +--------+--------+
          |                 |
          v                 v
      Command Side       Query Side
          |                 |
          v                 v
    Write Database      Read Database
```

### Example

Commands:

```text
CreateExpense
UpdateExpense
ApproveExpense
DeleteExpense
```

Queries:

```text
GetExpense
GetAllExpenses
GetMonthlyReport
GetExpenseStatistics
```

### TypeScript Example

Command:

```typescript
type CreateExpenseCommand = {
    userId: number;
    amount: number;
    description: string;
};
```

Command handler:

```typescript
class CreateExpenseHandler {
    constructor(
        private repository: ExpenseRepository
    ) {}

    async execute(command: CreateExpenseCommand) {
        return this.repository.create(command);
    }
}
```

Query handler:

```typescript
class GetExpenseHandler {
    constructor(
        private repository: ExpenseRepository
    ) {}

    async execute(id: number) {
        return this.repository.findById(id);
    }
}
```

### Why Use CQRS?

- Different read and write requirements.
- Complex reporting.
- Read-heavy systems.
- Independent optimization of read and write paths.
- Separate models for different use cases.

### Important

CQRS does not require separate databases.

You can implement CQRS with separate application handlers using the same database.

A more advanced implementation may use separate read models and write models.

### CQRS vs CRUD

| CRUD | CQRS |
|---|---|
| Same model commonly handles reads and writes | Separate command and query responsibilities |
| Simpler | More complex |
| Good for standard applications | Useful for complex business workflows |
| Usually one data model | May use separate models |

---

## 41. Event Sourcing

### Definition

Event Sourcing stores changes as a sequence of events rather than storing only the latest state.

Instead of updating a record and losing its previous state, the application records the events that caused the state to change.

### Traditional Database

```text
orders
------
id
status

101
Delivered
```

Only the latest status is stored in this example.

### Event Sourcing

```text
order_events
------------
id
order_id
event_type
event_data
created_at
```

Example events:

```text
OrderCreated
PaymentCompleted
OrderPacked
OrderShipped
OrderDelivered
```

### Architecture

```text
            Command
               |
               v
          Event Handler
               |
               v
           Event Store
               |
               v
          Event Stream
               |
               v
         State Projection
               |
               v
          Read Database
```

### Example Event History

```json
[
  {
    "type": "OrderCreated",
    "orderId": 101
  },
  {
    "type": "PaymentCompleted",
    "orderId": 101
  },
  {
    "type": "OrderShipped",
    "orderId": 101
  }
]
```

The current order state can be reconstructed by applying these events in sequence.

### Advantages

- Complete historical record.
- Auditability.
- Ability to reconstruct past state.
- Useful for event-driven systems.
- Supports alternative projections.

### Disadvantages

- More complicated architecture.
- Event schema evolution.
- Projection management.
- Event replay considerations.
- Data retention and privacy challenges.

### Important Concepts

**Event Store:** Stores events.

**Event Stream:** Ordered events belonging to an aggregate or entity.

**Projection:** A read model built from events.

**Snapshot:** A stored representation of state at a particular point to reduce replay work.

**Event Versioning:** Managing changes to event schemas over time.

### Event Sourcing vs Traditional Persistence

| Traditional Persistence | Event Sourcing |
|---|---|
| Stores current state | Stores state-changing events |
| History may be separate | History is fundamental |
| Simpler | More complex |
| Easier for ordinary CRUD | Useful for audit-heavy domains |

Do not use Event Sourcing for every application. It is valuable when historical changes and event-driven behavior are central requirements.

---

## 42. Transactional Outbox Pattern

### Problem

Suppose an application creates an order and publishes an event.

Two operations are required:

1. Save order in MySQL.
2. Publish `OrderCreated` to Kafka or RabbitMQ.

What happens if the database commit succeeds but the broker is unavailable?

The order exists, but the event may be lost.

### Transactional Outbox Solution

Store the business change and the event record in the same database transaction.

```text
             Order Service
                   |
                   v
               Database
                   |
        +----------+----------+
        |                     |
        v                     v
     Orders              Outbox Events
                              |
                              v
                         Outbox Worker
                              |
                              v
                         Message Broker
```

### Example Schema

```sql
CREATE TABLE outbox_events (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    event_type VARCHAR(100) NOT NULL,
    payload JSON NOT NULL,
    published_at DATETIME NULL,
    created_at DATETIME NOT NULL
);
```

### Transaction Example

```typescript
await sequelize.transaction(async (transaction) => {
    const order = await Order.create(
        {
            userId: 101,
            total: 500
        },
        { transaction }
    );

    await OutboxEvent.create(
        {
            eventType: "ORDER_CREATED",
            payload: {
                orderId: order.id
            }
        },
        { transaction }
    );
});
```

A separate worker publishes unpublished outbox records.

### Benefits

- Prevents the database/message dual-write gap.
- Improves event delivery reliability.
- Supports retry mechanisms.
- Keeps event recording atomic with business data changes.

**Important:** The outbox worker may publish the same event more than once. Consumers should be idempotent.

---

## 43. Saga Pattern

### Definition

The Saga Pattern coordinates a distributed business transaction through a sequence of local transactions.

It is useful when an operation spans multiple services and a single database transaction cannot cover the whole workflow.

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
Arrange Shipping
     |
     v
Complete Order
```

What happens if payment fails after inventory is reserved?

The system needs a compensating action to release inventory.

### Saga Workflow

```text
Create Order
     |
     v
Reserve Inventory
     |
     v
Process Payment
     |
     +------ Success
     |          |
     |          v
     |      Arrange Shipping
     |
     +------ Failure
                |
                v
         Release Inventory
                |
                v
          Cancel Order
```

### Two Saga Coordination Models

#### 1. Choreography

Each service reacts to events from other services.

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
InventoryReserved Event
     |
     v
Payment Service
```

#### 2. Orchestration

A central orchestrator coordinates the workflow.

```text
             Saga Orchestrator
                    |
         +----------+----------+
         |          |          |
         v          v          v
      Order     Inventory    Payment
      Service    Service     Service
```

### Choreography vs Orchestration

| Choreography | Orchestration |
|---|---|
| Services react to events | Central coordinator manages workflow |
| Less central coordination | Explicit workflow control |
| Can become difficult to trace | Easier to understand complex sequences |
| Suitable for simpler event-driven workflows | Suitable for complex workflows |

### What Is a Compensating Transaction?

A compensating transaction attempts to semantically reverse an earlier completed business action.

Example:

```text
Reserve Inventory
       |
       v
Payment Fails
       |
       v
Release Inventory
```

A compensation is not always a perfect rollback.

For example, a refund is a new financial operation, not necessarily a deletion of the original payment.

---

## 44. Circuit Breaker Pattern

### Definition

The Circuit Breaker Pattern prevents repeated calls to a dependency that is currently failing.

It helps stop cascading failures.

### Example

Your Node.js application depends on an external payment API.

If that API is unavailable, continuously sending requests may waste resources and increase latency.

### Three States

1. Closed.
2. Open.
3. Half-Open.

### Circuit Breaker Lifecycle

```text
             CLOSED
                |
          Failures exceed
             threshold
                |
                v
              OPEN
                |
          Wait for timeout
                |
                v
           HALF-OPEN
             /     \
         Success   Failure
           |          |
           v          v
         CLOSED      OPEN
```

### State Explanation

**Closed:** Requests are allowed normally.

**Open:** Requests are rejected immediately or handled through fallback behavior.

**Half-Open:** A limited number of test requests are allowed.

### Example

```typescript
class CircuitBreaker {
    private failures = 0;

    private state: "CLOSED" | "OPEN" = "CLOSED";

    constructor(
        private threshold: number
    ) {}

    async execute<T>(
        operation: () => Promise<T>
    ): Promise<T> {
        if (this.state === "OPEN") {
            throw new Error("Circuit is open");
        }

        try {
            const result = await operation();

            this.failures = 0;

            return result;

        } catch (error) {
            this.failures++;

            if (this.failures >= this.threshold) {
                this.state = "OPEN";
            }

            throw error;
        }
    }
}
```

This is a simplified illustration. A production circuit breaker should include:

- A recovery timeout.
- Half-open state.
- Limited test requests.
- Failure classification.
- Concurrency-safe state handling.
- Metrics.

### Use Cases

- Payment APIs.
- Email providers.
- External authentication services.
- Third-party APIs.
- Internal microservice calls.

---

## 45. Bulkhead Pattern

### Definition

The Bulkhead Pattern isolates resources so that failure in one area does not exhaust the resources needed by other areas.

The name comes from ship compartments that prevent flooding from spreading through the entire vessel.

### Example

Your application has three external integrations:

- Payment API.
- Email API.
- Shipping API.

If all requests share one unrestricted connection pool, a slow email provider may consume resources needed for payment processing.

### Architecture

```text
              Backend
                 |
      +----------+----------+
      |          |          |
      v          v          v
   Payment     Email     Shipping
   Pool        Pool       Pool
      |          |          |
      v          v          v
  Provider A Provider B Provider C
```

Each dependency has isolated capacity.

### Benefits

- Limits failure propagation.
- Protects critical operations.
- Improves system resilience.
- Controls concurrency.

### Use Cases

- Separate connection pools.
- Worker concurrency limits.
- Separate queues.
- Thread pools.
- External service integrations.

---

## 46. Retry Pattern

### Definition

The Retry Pattern attempts an operation again when it fails under conditions where retrying may succeed.

Example:

A temporary network failure occurs when calling an external service.

A retry may resolve the issue.

### Basic Retry

```typescript
async function retry<T>(
    operation: () => Promise<T>,
    attempts: number
): Promise<T> {
    let lastError: unknown;

    for (let i = 0; i < attempts; i++) {
        try {
            return await operation();
        } catch (error) {
            lastError = error;
        }
    }

    throw lastError;
}
```

### Exponential Backoff

Instead of retrying immediately:

```text
Attempt 1 -> Wait 1 second
Attempt 2 -> Wait 2 seconds
Attempt 3 -> Wait 4 seconds
Attempt 4 -> Wait 8 seconds
```

### Add Jitter

Jitter adds randomness to retry delays.

This helps avoid many clients retrying simultaneously.

### Important Rules

- Retry only appropriate failures.
- Set a maximum attempt count.
- Use timeouts.
- Use backoff and jitter.
- Ensure operations are idempotent where necessary.
- Respect provider retry guidance and `Retry-After` headers.

Do not retry a payment request blindly if you cannot determine whether the first attempt succeeded.

---

## 47. Timeout Pattern

### Definition

The Timeout Pattern places a maximum time limit on an operation.

Without timeouts, an application may wait indefinitely for a dependency.

### Example

```typescript
async function fetchWithTimeout(
    url: string,
    timeoutMs: number
) {
    const controller = new AbortController();

    const timer = setTimeout(
        () => controller.abort(),
        timeoutMs
    );

    try {
        return await fetch(url, {
            signal: controller.signal
        });
    } finally {
        clearTimeout(timer);
    }
}
```

Usage:

```typescript
const response = await fetchWithTimeout(
    "https://api.example.com/users",
    5000
);
```

### Why Use Timeouts?

- Prevent indefinite waiting.
- Protect server resources.
- Improve failure detection.
- Support controlled retries.
- Reduce cascading failures.

### Timeout vs Retry

| Timeout | Retry |
|---|---|
| Limits operation duration | Repeats an operation |
| Protects against slow dependencies | Handles potentially temporary failures |
| Does not guarantee success | Does not guarantee success |

They are often used together.

---

## 48. Rate Limiter Pattern

### Definition

The Rate Limiter Pattern controls how frequently an operation can occur.

Example:

```text
Maximum 100 API requests per minute per user.
```

### Common Algorithms

| Algorithm | Description |
|---|---|
| Fixed Window | Counts requests within fixed intervals |
| Sliding Window | Evaluates a moving time window |
| Token Bucket | Requests consume accumulated tokens |
| Leaky Bucket | Controls the rate at which requests are processed |

### Token Bucket Example

```text
Capacity: 10 tokens

Initial tokens: 10

Each request consumes: 1 token

Token refill: 1 token per second
```

If no tokens remain, requests are rejected or delayed.

### Where to Apply Rate Limiting?

- Login endpoints.
- OTP endpoints.
- Password reset.
- Public APIs.
- Search endpoints.
- Expensive report generation.
- Payment initiation.

---

## 49. Anti-Corruption Layer

### Definition

An Anti-Corruption Layer protects your domain model from the data structures and assumptions of an external or legacy system.

### Example

Your application uses:

```typescript
type Customer = {
    id: string;
    fullName: string;
    email: string;
};
```

An external legacy system returns:

```json
{
  "CUST_NO": "101",
  "CUST_NAME": "Riyaz Khan",
  "EMAIL_ADDR": "riyaz@example.com"
}
```

Instead of allowing the legacy structure to spread through your application, create an adapter.

```typescript
function mapLegacyCustomer(data: any): Customer {
    return {
        id: String(data.CUST_NO),
        fullName: data.CUST_NAME,
        email: data.EMAIL_ADDR
    };
}
```

### Architecture

```text
Legacy System
      |
      v
External Data Format
      |
      v
Anti-Corruption Layer
      |
      v
Application Domain Model
```

### Benefits

- Protects domain logic.
- Reduces external dependencies.
- Simplifies migrations.
- Isolates legacy behavior.
- Makes integrations easier to replace.

---

## 50. Strangler Fig Pattern

### Definition

The Strangler Fig Pattern gradually replaces a legacy system by routing selected functionality to new implementations.

Instead of rewriting the entire application at once, functionality is migrated incrementally.

### Example

An old application contains:

```text
Legacy Application
    |
    +---- Users
    |
    +---- Orders
    |
    +---- Payments
    |
    +---- Reports
```

You want to modernize it using Node.js microservices.

### Migration

First:

```text
               API Gateway
                    |
             +------+------+
             |             |
             v             v
         New User API   Legacy App
```

Later:

```text
                API Gateway
                     |
          +----------+----------+
          |          |          |
          v          v          v
       User API   Order API   Legacy App
                                |
                                v
                            Payments
```

Finally:

```text
                API Gateway
                     |
          +----------+----------+
          |          |          |
          v          v          v
       User API   Order API  Payment API
```

The legacy system is gradually retired.

### Benefits

- Lower migration risk.
- Incremental delivery.
- Smaller changes.
- Easier validation.
- Reduced need for a large one-time rewrite.

---

## 51. API Composition Pattern

### Definition

API Composition combines information from multiple services to provide a unified response.

### Example

A dashboard needs:

- User information.
- Order information.
- Payment information.

Without composition, the frontend may need to call three services.

### Architecture

```text
                 Frontend
                    |
                    v
              API Composer
                    |
          +---------+---------+
          |         |         |
          v         v         v
        User      Order     Payment
       Service   Service   Service
```

### Example

```typescript
async function getDashboard(userId: string) {
    const [user, orders, payments] = await Promise.all([
        userService.getUser(userId),
        orderService.getOrders(userId),
        paymentService.getPayments(userId)
    ]);

    return {
        user,
        orders,
        payments
    };
}
```

### Advantages

- Simplifies frontend integration.
- Combines distributed data.
- Reduces frontend coordination.

### Challenges

- Partial failures.
- Latency from slow services.
- Data consistency.
- Large response payloads.

Use timeouts and define what should happen if one downstream service is unavailable.

---

## 52. Database per Service Pattern

### Definition

In microservices architecture, each service owns its own data storage and is responsible for accessing it.

### Architecture

```text
          User Service
               |
               v
            User DB


          Order Service
               |
               v
            Order DB


         Payment Service
               |
               v
           Payment DB
```

### Why Use It?

- Independent data ownership.
- Reduced database coupling.
- Independent schema evolution.
- Service-level autonomy.

### Important Rule

A service should not directly query another service's private database.

Instead, it should communicate through APIs or events.

### Challenge

How do you get data from multiple services?

Common solutions:

- API Composition.
- Events.
- CQRS read models.
- Data replication for specific use cases.

---

## 53. Distributed Transactions

### Definition

A Distributed Transaction coordinates changes across multiple independent systems.

Example:

Placing an order requires:

1. Order database update.
2. Inventory database update.
3. Payment system operation.

These operations may belong to different services.

### Why Are Distributed Transactions Difficult?

Because each service manages its own resources.

A local database transaction cannot automatically roll back an external payment or another service's database commit.

### Common Approaches

- Saga Pattern.
- Transactional Outbox.
- Event-driven coordination.
- Two-Phase Commit where appropriate.

### Two-Phase Commit (2PC)

2PC is a protocol involving two major phases:

**Phase 1: Prepare**

Participants confirm whether they can commit.

**Phase 2: Commit or Abort**

The coordinator tells participants to commit or roll back.

```text
           Transaction Coordinator
                     |
                 Prepare
                     |
         +-----------+-----------+
         |                       |
         v                       v
      Service A              Service B
         |                       |
         +-----------+-----------+
                     |
                Commit/Abort
```

### 2PC vs Saga

| 2PC | Saga |
|---|---|
| Coordinates atomic commit across participants | Coordinates local transactions |
| Requires transaction coordination | Uses events or orchestration |
| Can hold resources while waiting | Uses compensating actions |
| Can introduce blocking and availability concerns | Supports long-running workflows |
| Useful in suitable transactional environments | Common in microservices |

For many distributed business workflows, Saga is more practical than trying to create a single ACID transaction across independent services.

---

## 54. Idempotency Patterns

### Definition

An operation is Idempotent when repeating it produces the same intended final effect as performing it once.

Mathematically:

```text
f(f(x)) = f(x)
```

### Example: HTTP PUT

Setting a user's name to the same value repeatedly generally has the same final effect.

### Why Is Idempotency Important?

Distributed systems can retry requests because of:

- Network timeouts.
- Connection interruptions.
- Duplicate message delivery.
- Worker crashes.
- Client retries.

Without idempotency, duplicate requests may create duplicate orders or payments.

### Idempotency Key

Example:

```http
POST /api/v1/payments
Idempotency-Key: order-101-payment-attempt-1
```

The backend stores the key and the operation result.

If the same key is submitted again, the system returns the stored result rather than creating a second payment operation.

### Example Table

```sql
CREATE TABLE idempotency_records (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    idempotency_key VARCHAR(255) NOT NULL UNIQUE,
    request_hash VARCHAR(255) NOT NULL,
    response_body JSON NULL,
    status VARCHAR(30) NOT NULL,
    created_at DATETIME NOT NULL
);
```

### Important Design Considerations

- Scope keys to the authenticated user or client.
- Use unique constraints.
- Validate that repeated keys have compatible request content.
- Handle concurrent requests safely.
- Define key expiration.
- Persist operation results.
- Do not assume an external provider supports the same idempotency semantics as your application.

---

# PART 11: ENTERPRISE INTEGRATION PATTERNS

## 55. What Are Enterprise Integration Patterns?

Enterprise Integration Patterns describe reusable approaches for connecting different systems.

They are especially useful in distributed applications.

### Important Patterns

| Pattern | Purpose |
|---|---|
| Message Channel | Communication path between systems |
| Message Router | Routes messages based on conditions |
| Message Filter | Removes unwanted messages |
| Message Translator | Converts one message format to another |
| Message Aggregator | Combines multiple messages |
| Message Splitter | Splits one message into multiple messages |
| Content-Based Router | Routes based on message content |
| Dead Letter Channel | Stores messages that cannot be processed |
| Publish-Subscribe Channel | Delivers events to multiple subscribers |
| Request-Reply | Enables request-response messaging |

## 56. Message Router

A Message Router determines where a message should go.

Example:

```text
               Incoming Order
                      |
                      v
                Message Router
                 /         \
                v           v
           High Value    Normal Value
              Order         Order
                |             |
                v             v
          Priority Queue   Normal Queue
```

## 57. Message Filter

A Message Filter allows only messages that satisfy a condition.

Example:

```typescript
function shouldProcessOrder(order: any) {
    return order.status === "confirmed";
}
```

## 58. Message Translator

A Message Translator converts a message from one format to another.

Example:

```typescript
function translateLegacyOrder(data: any) {
    return {
        orderId: String(data.ORDER_NO),
        total: Number(data.ORDER_TOTAL),
        customerId: String(data.CUST_NO)
    };
}
```

## 59. Message Aggregator

A Message Aggregator combines multiple related messages.

Example:

```text
Order Created
     |
     +---- Inventory Reserved
     |
     +---- Payment Completed
     |
     +---- Shipping Created
                  |
                  v
             Aggregator
                  |
                  v
            Order Completed
```

## 60. Dead Letter Channel

A Dead Letter Channel stores messages that cannot be processed successfully after defined retries.

Example:

```text
Message Queue
     |
     v
Worker
     |
     v
Processing Failed
     |
     v
Retry Attempts
     |
     v
Dead Letter Queue
```

A dead-letter process should support:

- Error inspection.
- Alerts.
- Manual replay.
- Controlled reprocessing.
- Root-cause analysis.

---

# PART 12: ARCHITECTURE QUALITY AND ENGINEERING

## 61. Architecture Decision Records (ADR)

### Definition

An Architecture Decision Record documents an important technical decision, its context and its consequences.

For example:

Your team must choose between:

- MySQL.
- MongoDB.

Instead of making the decision informally, document it.

### ADR Structure

```text
ADR-001: Database Selection

Status:
Accepted

Context:
The application requires relational data,
transactions and reporting.

Decision:
Use MySQL.

Alternatives:
MongoDB and PostgreSQL.

Consequences:
The application uses relational schema design,
SQL migrations and transaction-based workflows.
```

### Why Use ADRs?

- Preserves architectural reasoning.
- Helps new developers.
- Avoids repeating discussions.
- Documents trade-offs.
- Supports future architecture reviews.

### Common ADR Topics

- Database selection.
- Monolith vs microservices.
- Authentication architecture.
- Caching strategy.
- Event broker selection.
- Deployment platform.
- API versioning.

---

## 62. Architecture Testing

### Definition

Architecture Testing verifies that the codebase follows intended architectural rules.

Normal unit tests check individual behavior.

Architecture tests check structural constraints.

### Examples of Architecture Rules

- Controllers must not directly access database models.
- Domain modules must not import Express.
- Application services must not depend on HTTP response objects.
- Modules must not import private internals of unrelated modules.
- Infrastructure implementations must satisfy application interfaces.

### Example Dependency Rule

```text
Presentation
     |
     v
Application
     |
     v
Domain

Infrastructure
     |
     v
Implements Domain/Application Interfaces
```

The domain should not depend on infrastructure.

### Tools

- Dependency Cruiser.
- Madge.
- ESLint import restrictions.
- ArchUnit-style tools for supported languages.
- Custom dependency tests.

### Example Using Dependency Cruiser

You can define rules that prevent:

```text
domain -> express
domain -> database
```

This helps stop architectural erosion as the project grows.

---

## 63. Refactoring Legacy Applications

### Definition

Refactoring is improving the internal structure of software without intentionally changing its external behavior.

### Example

Original code:

```typescript
async function createExpense(req: any, res: any) {
    if (!req.body.amount) {
        return res.status(400).json({
            message: "Amount required"
        });
    }

    const expense = await Expense.create(req.body);

    res.json(expense);
}
```

Refactored:

Controller:

```typescript
async function createExpenseController(req: any, res: any) {
    const expense = await expenseService.create(req.body);

    res.status(201).json(expense);
}
```

Service:

```typescript
class ExpenseService {
    constructor(
        private repository: ExpenseRepository
    ) {}

    async create(data: any) {
        if (!data.amount || data.amount <= 0) {
            throw new Error("Invalid expense amount");
        }

        return this.repository.create(data);
    }
}
```

Repository:

```typescript
class MySQLExpenseRepository implements ExpenseRepository {
    async create(data: any) {
        return Expense.create(data);
    }
}
```

Now the responsibilities are separated.

### Safe Refactoring Process

1. Understand existing behavior.
2. Identify high-risk areas.
3. Add automated tests.
4. Refactor one area at a time.
5. Verify behavior after each change.
6. Monitor production after deployment.
7. Remove obsolete code only after confirming it is unused.

### Common Refactoring Techniques

- Extract Method.
- Extract Class.
- Rename Variable.
- Replace Conditional with Polymorphism.
- Introduce Parameter Object.
- Encapsulate Field.
- Move Method.
- Replace Magic Numbers with Constants.

**Important:** Refactoring without adequate tests can introduce regressions. Characterization tests are useful when working with legacy systems whose behavior is poorly documented.

---

# PART 13: COMPLETE ENTERPRISE ARCHITECTURE PROJECT

# Project: Enterprise E-Commerce Platform

We will combine the most important principles and patterns from this phase into one realistic project.

## 64. Project Requirements

Build an e-commerce platform supporting:

1. User registration.
2. Authentication.
3. Product catalog.
4. Product categories.
5. Shopping cart.
6. Order creation.
7. Inventory management.
8. Payment processing.
9. Shipping.
10. Refunds.
11. Email notifications.
12. Order tracking.
13. Admin dashboard.
14. Audit logs.
15. Analytics.
16. Background jobs.
17. Distributed communication.
18. Failure recovery.

## 65. Recommended Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + TypeScript |
| API | Express.js |
| Database | MySQL |
| ORM | Sequelize / Prisma |
| Cache | Redis |
| Message Broker | RabbitMQ or Kafka |
| Background Jobs | BullMQ |
| Authentication | Session or OIDC |
| Validation | Zod |
| Logging | Pino |
| Testing | Vitest / Jest |
| Monitoring | Prometheus + Grafana |
| Containerization | Docker |
| Reverse Proxy | Nginx |
| Deployment | AWS / VPS |

## 66. Architecture Design

Start with a modular monolith.

```text
                    Next.js Frontend
                           |
                           v
                       API Layer
                           |
              +------------+------------+
              |            |            |
              v            v            v
          User Module  Product Module Order Module
              |            |            |
              +------------+------------+
                           |
                    Application Layer
                           |
                      Domain Layer
                           |
                 Infrastructure Layer
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
           MySQL         Redis       Message Queue
                                         |
                                         v
                                      Workers
```

This architecture keeps the initial system manageable.

As business and operational requirements grow, selected modules can be extracted into independent services.

## 67. Suggested Project Structure

```text
ecommerce/
│
├── src/
│   │
│   ├── modules/
│   │   │
│   │   ├── users/
│   │   ├── products/
│   │   ├── categories/
│   │   ├── cart/
│   │   ├── orders/
│   │   ├── inventory/
│   │   ├── payments/
│   │   └── shipping/
│   │
│   ├── application/
│   │   ├── commands/
│   │   ├── queries/
│   │   └── use-cases/
│   │
│   ├── domain/
│   │   ├── entities/
│   │   ├── value-objects/
│   │   ├── events/
│   │   └── specifications/
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   ├── repositories/
│   │   ├── cache/
│   │   ├── messaging/
│   │   └── external-services/
│   │
│   ├── patterns/
│   │   ├── factories/
│   │   ├── adapters/
│   │   ├── decorators/
│   │   └── strategies/
│   │
│   ├── workers/
│   ├── middleware/
│   ├── config/
│   ├── shared/
│   ├── app.ts
│   └── server.ts
│
├── tests/
├── migrations/
├── docs/
├── Dockerfile
└── package.json
```

## 68. Where Will We Use Each Design Pattern?

| Pattern | Project Usage |
|---|---|
| Singleton | Shared application configuration or logger instance |
| Factory | Payment gateway selection |
| Abstract Factory | Related infrastructure implementations |
| Builder | Complex order creation |
| Prototype | Cloning supported configuration objects |
| Adapter | Integrating external payment provider |
| Decorator | Logging and metrics around services |
| Facade | Simplified order workflow interface |
| Proxy | Controlled access to expensive resources |
| Composite | Product category hierarchy |
| Bridge | Notification type and delivery provider |
| Strategy | Shipping and discount calculations |
| Observer | In-process domain event notifications |
| Command | Order creation and cancellation commands |
| State | Order lifecycle |
| Chain of Responsibility | Request middleware |
| Template Method | Report generation |
| Mediator | Coordinating application components |
| Iterator | Collection traversal |
| Visitor | Document processing |
| Specification | Discount and approval rules |
| CQRS | Separate read and write workflows |
| Event Sourcing | Order history, if justified |
| Outbox | Reliable event publication |
| Saga | Order, inventory and payment workflow |
| Circuit Breaker | External provider failure isolation |
| Bulkhead | Resource isolation |
| Retry | Temporary network failure recovery |
| Timeout | External API limits |
| Rate Limiter | API traffic control |
| Anti-Corruption Layer | Legacy integration |
| Strangler Fig | Gradual legacy migration |
| API Composition | Dashboard aggregation |
| Database per Service | Extracted microservices |
| Idempotency | Prevent duplicate payment effects |

---

## 69. Complete Order Processing Workflow

Suppose a customer places an order.

### Step 1: Create Order

The frontend sends:

```http
POST /api/v1/orders
```

Request:

```json
{
  "items": [
    {
      "productId": 101,
      "quantity": 2
    }
  ],
  "paymentMethod": "card"
}
```

### Step 2: Validate Request

Use request validation and business rules.

### Step 3: Create Order Command

```typescript
type CreateOrderCommand = {
    userId: number;
    items: {
        productId: number;
        quantity: number;
    }[];
};
```

### Step 4: Reserve Inventory

Inventory service checks and reserves stock.

### Step 5: Process Payment

Use the Strategy Pattern or Adapter Pattern to integrate the selected payment provider.

### Step 6: Save Order

Use a database transaction to persist the order and related records.

### Step 7: Publish Event

Use the Transactional Outbox Pattern.

```text
OrderCreated
```

### Step 8: Process Background Tasks

Workers handle:

- Email.
- Invoice.
- Analytics.
- Notifications.

### Step 9: Handle Failures

If payment fails:

- Release inventory.
- Mark the order appropriately.
- Record the failure.
- Retry only when safe.

### Complete Workflow

```text
                  Customer
                     |
                     v
                 Create Order
                     |
                     v
                 Validate Data
                     |
                     v
               Reserve Inventory
                     |
                     v
                Process Payment
                     |
              +------+------+
              |             |
              v             v
           Success        Failure
              |             |
              v             v
          Save Order    Release Stock
              |             |
              v             v
          Outbox Event   Mark Failed
              |
              v
          Message Queue
              |
      +-------+-------+
      |       |       |
      v       v       v
    Email  Invoice Analytics
```

### Important Production Considerations

- Use idempotency keys for payment operations.
- Use a state machine for order transitions.
- Use database constraints for inventory consistency.
- Use outbox events for reliable publication.
- Make workers idempotent.
- Define retry and dead-letter policies.
- Use Saga compensation for distributed failures.
- Record audit events.
- Monitor the complete order lifecycle.

---

# PART 14: INTERVIEW QUESTIONS

## Beginner-Level Questions

**Q1. What is software architecture?**  
The organization of software components, their responsibilities and their interactions.

**Q2. What are design patterns?**  
Reusable approaches to commonly occurring software design problems.

**Q3. What is SOLID?**  
Five principles for designing maintainable object-oriented software.

**Q4. What is SRP?**  
A module should have one primary responsibility or reason to change.

**Q5. What is OCP?**  
Software should allow extension without repeatedly modifying stable existing code.

**Q6. What is LSP?**  
Subtypes should honor the contracts of their parent abstractions.

**Q7. What is ISP?**  
Clients should not depend on methods they do not use.

**Q8. What is DIP?**  
High-level and low-level modules should depend on abstractions.

**Q9. What is DRY?**  
Don't Repeat Yourself.

**Q10. What is KISS?**  
Keep solutions as simple as practical.

**Q11. What is YAGNI?**  
Avoid implementing functionality before it is actually needed.

**Q12. What is cohesion?**  
How closely related the responsibilities within a module are.

**Q13. What is coupling?**  
The degree of dependency between components.

**Q14. What is a Singleton?**  
A pattern that provides one shared instance within a defined application context.

**Q15. What is a Factory?**  
A pattern that centralizes object creation.

## Intermediate-Level Questions

**Q16. What is Abstract Factory?**  
A pattern for creating families of related objects.

**Q17. What is Builder?**  
A pattern for constructing complex objects step by step.

**Q18. What is Prototype?**  
A pattern for creating objects by copying existing objects.

**Q19. What is Adapter?**  
A pattern that makes incompatible interfaces work together.

**Q20. What is Decorator?**  
A pattern that adds behavior through object composition.

**Q21. What is Facade?**  
A simplified interface to a complex subsystem.

**Q22. What is Proxy?**  
A substitute object that controls access to another object.

**Q23. What is Composite?**  
A pattern that organizes objects into tree structures.

**Q24. What is Bridge?**  
A pattern that separates abstractions from their implementations.

**Q25. What is Strategy?**  
A pattern for interchangeable algorithms.

**Q26. What is Observer?**  
A pattern that notifies subscribers when a subject changes.

**Q27. What is Command?**  
A pattern that encapsulates an operation as an object.

**Q28. What is State?**  
A pattern that changes object behavior based on its current state.

**Q29. What is Chain of Responsibility?**  
A pattern that passes requests through multiple handlers.

**Q30. What is Mediator?**  
A pattern that coordinates communication between components.

## Advanced-Level Questions

**Q31. What is CQRS?**  
An architectural pattern separating command and query responsibilities.

**Q32. What is Event Sourcing?**  
A persistence approach that stores state-changing events.

**Q33. What is Transactional Outbox?**  
A pattern that atomically stores business changes and event records.

**Q34. What is Saga?**  
A pattern for coordinating distributed transactions through local transactions and compensations.

**Q35. What is Circuit Breaker?**  
A pattern that stops repeated calls to a failing dependency.

**Q36. What is Bulkhead?**  
A pattern that isolates resources to limit failure propagation.

**Q37. What is exponential backoff?**  
A retry strategy that increases waiting time between attempts.

**Q38. What is an Anti-Corruption Layer?**  
A layer that protects an application's domain model from external or legacy models.

**Q39. What is Strangler Fig?**  
A gradual approach to replacing a legacy system.

**Q40. What is API Composition?**  
Combining data from multiple services into one response.

## Expert-Level Questions

**Q41. What is the difference between CQRS and Event Sourcing?**

CQRS separates read and write responsibilities. Event Sourcing changes how application state is persisted by recording events. They can be used independently or together.

**Q42. Can CQRS be implemented with one database?**

Yes. Separate command and query handlers can use the same database.

**Q43. Why is the Transactional Outbox Pattern necessary?**

It prevents the dual-write problem between database commits and message publication.

**Q44. Does Saga guarantee exactly-once execution?**

No. Saga workflows need idempotency and reliable state tracking.

**Q45. What is the difference between Choreography and Orchestration?**

Choreography relies on services reacting to events. Orchestration uses a coordinator to manage the workflow.

**Q46. Why is a Circuit Breaker useful?**

It prevents repeated calls to failing dependencies and helps avoid cascading failures.

**Q47. How does Bulkhead improve reliability?**

It isolates resource capacity so one overloaded dependency does not consume resources needed by other operations.

**Q48. What is the difference between Adapter and Anti-Corruption Layer?**

An Adapter converts interfaces. An Anti-Corruption Layer is a broader boundary that protects a domain model from external concepts and rules. It may contain adapters, translators and other integration logic.

**Q49. How would you gradually migrate a monolithic application to microservices?**

Use the Strangler Fig Pattern: introduce routing boundaries, extract one business capability at a time, validate behavior and gradually retire the legacy implementation.

**Q50. How would you prevent duplicate payment processing in a distributed application?**

Use idempotency keys, durable operation records, provider-side idempotency where supported, state validation, database constraints and reconciliation.

---

# PART 15: HANDS-ON PRACTICAL ASSIGNMENTS

Complete these assignments progressively.

## Level 1 – Software Principles

| No. | Assignment | Expected Result |
|---:|---|---|
| 1 | Refactor a large class using SRP | Single-responsibility classes |
| 2 | Implement OCP using payment providers | Extensible payment architecture |
| 3 | Fix an LSP violation | Correct abstraction hierarchy |
| 4 | Split a large interface using ISP | Focused interfaces |
| 5 | Apply DIP to a database service | Repository abstraction |
| 6 | Refactor duplicate code using DRY | Reusable logic |
| 7 | Simplify complex code using KISS | Readable implementation |
| 8 | Remove unnecessary features using YAGNI | Reduced complexity |
| 9 | Refactor a tightly coupled backend | Loose coupling |
| 10 | Create cohesive application modules | Better module boundaries |

## Level 2 – Creational Patterns

| No. | Assignment | Expected Result |
|---:|---|---|
| 11 | Implement Singleton Logger | Shared logger |
| 12 | Create a Notification Factory | Provider selection |
| 13 | Create a Payment Factory | Payment abstraction |
| 14 | Implement Abstract Factory for themes | Related object families |
| 15 | Build a User Builder | Complex object construction |
| 16 | Build a Report Builder | Configurable reports |
| 17 | Implement Prototype cloning | Object cloning |
| 18 | Compare shallow and deep copies | Correct copy behavior |
| 19 | Refactor object creation logic | Centralized creation |
| 20 | Write unit tests for factories | Testable creation |

## Level 3 – Structural Patterns

| No. | Assignment | Expected Result |
|---:|---|---|
| 21 | Implement Adapter for payment provider | External integration |
| 22 | Implement Adapter for cloud storage | Storage abstraction |
| 23 | Implement Logging Decorator | Additional behavior |
| 24 | Implement Caching Decorator | Cached operations |
| 25 | Create Order Facade | Simplified workflow |
| 26 | Implement Authorization Proxy | Access control |
| 27 | Create a File System Composite | Tree structure |
| 28 | Implement a Product Category Composite | Category hierarchy |
| 29 | Build a Notification Bridge | Independent variation |
| 30 | Compare Adapter and Bridge | Architecture analysis |

## Level 4 – Behavioral Patterns

| No. | Assignment | Expected Result |
|---:|---|---|
| 31 | Implement Strategy for discounts | Interchangeable algorithms |
| 32 | Implement Strategy for shipping | Shipping calculation |
| 33 | Build an Observer notification system | Event notifications |
| 34 | Build Command-based expense operations | Encapsulated actions |
| 35 | Implement Order State Pattern | Order lifecycle |
| 36 | Implement middleware chain | Request pipeline |
| 37 | Build a Report Template Method | Reusable workflow |
| 38 | Implement Mediator for order processing | Component coordination |
| 39 | Create a custom Iterator | Collection traversal |
| 40 | Implement Specification for expense approval | Business rule composition |

## Level 5 – Enterprise Patterns

| No. | Assignment | Expected Result |
|---:|---|---|
| 41 | Implement CQRS | Separate commands and queries |
| 42 | Build an event store prototype | Event persistence |
| 43 | Implement event projections | Read model |
| 44 | Implement Transactional Outbox | Reliable event publishing |
| 45 | Build an order Saga | Distributed workflow |
| 46 | Implement Circuit Breaker | Dependency resilience |
| 47 | Implement Bulkhead | Resource isolation |
| 48 | Implement Retry with backoff | Failure recovery |
| 49 | Implement Idempotency Keys | Duplicate request protection |
| 50 | Design a Strangler Fig migration | Legacy migration plan |

## Level 6 – Architecture Engineering

| No. | Assignment | Expected Result |
|---:|---|---|
| 51 | Create an Architecture Decision Record | Decision documentation |
| 52 | Implement architecture dependency tests | Structural enforcement |
| 53 | Refactor a legacy Express project | Improved structure |
| 54 | Design an API Composition service | Aggregated responses |
| 55 | Build a Message Router | Event routing |
| 56 | Implement a Message Translator | Message transformation |
| 57 | Build a Message Aggregator | Event aggregation |
| 58 | Implement a Dead Letter Queue workflow | Failed-message handling |
| 59 | Design a Database-per-Service architecture | Data ownership |
| 60 | Build the Enterprise E-Commerce Platform | Complete architecture |

---

# PART 16: QUICK REVISION NOTES

## Software Principles

| Concept | Meaning |
|---|---|
| SOLID | Five object-oriented design principles |
| SRP | One primary responsibility |
| OCP | Extend behavior without repeatedly modifying stable code |
| LSP | Subtypes honor their contracts |
| ISP | Small, focused interfaces |
| DIP | Depend on abstractions |
| DRY | Avoid unnecessary duplication |
| KISS | Keep solutions simple |
| YAGNI | Do not build unnecessary features |
| Cohesion | Related responsibilities within a module |
| Coupling | Dependency between modules |
| Separation of Concerns | Separate different responsibilities |

## Creational Patterns

| Pattern | Meaning |
|---|---|
| Singleton | One shared instance per defined context |
| Factory | Centralized object creation |
| Abstract Factory | Creates related object families |
| Builder | Step-by-step object construction |
| Prototype | Creates objects by copying |

## Structural Patterns

| Pattern | Meaning |
|---|---|
| Adapter | Converts incompatible interfaces |
| Decorator | Adds behavior through composition |
| Facade | Simplifies complex subsystems |
| Proxy | Controls access to an object |
| Composite | Organizes tree structures |
| Bridge | Separates abstraction and implementation |

## Behavioral Patterns

| Pattern | Meaning |
|---|---|
| Strategy | Interchangeable algorithms |
| Observer | Notifies subscribers |
| Command | Encapsulates operations |
| State | Behavior depends on state |
| Chain of Responsibility | Sequential handlers |
| Template Method | Reusable algorithm structure |
| Mediator | Coordinates components |
| Iterator | Traverses collections |
| Visitor | Adds operations to object structures |
| Specification | Encapsulates business rules |

## Enterprise Patterns

| Pattern | Meaning |
|---|---|
| CQRS | Separates reads and writes |
| Event Sourcing | Stores state-changing events |
| Transactional Outbox | Reliable event publication |
| Saga | Coordinates distributed workflows |
| Circuit Breaker | Stops calls to failing dependencies |
| Bulkhead | Isolates resources |
| Retry | Reattempts eligible failed operations |
| Timeout | Limits operation duration |
| Rate Limiter | Controls operation frequency |
| Anti-Corruption Layer | Protects domain models |
| Strangler Fig | Gradually replaces legacy systems |
| API Composition | Combines multiple service responses |
| Database per Service | Each service owns its data |
| Distributed Transaction | Coordinates multiple systems |
| Idempotency | Repeated execution has the same intended effect |
| ADR | Documents architectural decisions |
| Architecture Testing | Enforces structural rules |
| Refactoring | Improves internal structure without intentionally changing behavior |

---

# PART 17: PHASE 69 COMPLETION CHECKLIST

### Software Architecture Principles

- [ ] Understand software architecture.
- [ ] Understand SOLID.
- [ ] Implement SRP.
- [ ] Implement OCP.
- [ ] Understand LSP.
- [ ] Implement ISP.
- [ ] Implement DIP.
- [ ] Understand DRY.
- [ ] Understand KISS.
- [ ] Understand YAGNI.
- [ ] Understand high cohesion.
- [ ] Understand low coupling.
- [ ] Understand separation of concerns.
- [ ] Understand dependency management.

### Creational Patterns

- [ ] Implement Singleton.
- [ ] Implement Factory.
- [ ] Implement Abstract Factory.
- [ ] Implement Builder.
- [ ] Implement Prototype.

### Structural Patterns

- [ ] Implement Adapter.
- [ ] Implement Decorator.
- [ ] Implement Facade.
- [ ] Implement Proxy.
- [ ] Implement Composite.
- [ ] Implement Bridge.

### Behavioral Patterns

- [ ] Implement Strategy.
- [ ] Implement Observer.
- [ ] Implement Command.
- [ ] Implement State.
- [ ] Implement Chain of Responsibility.
- [ ] Implement Template Method.
- [ ] Implement Mediator.
- [ ] Implement Iterator.
- [ ] Implement Visitor.
- [ ] Implement Specification.

### Enterprise Architecture

- [ ] Understand CQRS.
- [ ] Understand Event Sourcing.
- [ ] Implement Transactional Outbox.
- [ ] Understand Saga.
- [ ] Implement Circuit Breaker.
- [ ] Understand Bulkhead.
- [ ] Implement Retry with backoff.
- [ ] Implement Timeout.
- [ ] Implement Rate Limiting.
- [ ] Understand Anti-Corruption Layer.
- [ ] Understand Strangler Fig.
- [ ] Implement API Composition.
- [ ] Understand Database per Service.
- [ ] Understand Distributed Transactions.
- [ ] Implement Idempotency.
- [ ] Understand Enterprise Integration Patterns.

### Architecture Quality

- [ ] Write Architecture Decision Records.
- [ ] Implement architecture tests.
- [ ] Refactor a legacy application.
- [ ] Build an enterprise architecture project.
- [ ] Document architectural trade-offs.

---

# PART 18: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

### Software Design and Architecture

1. [Refactoring Guru – Design Patterns](https://refactoring.guru/design-patterns) — Detailed design pattern explanations and examples.
2. [Martin Fowler](https://martinfowler.com/) — Software architecture, refactoring and enterprise patterns.
3. [Microsoft Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/) — Enterprise architecture guidance.
4. [Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework) — Reliability, security, performance and operational excellence.

### JavaScript and TypeScript

5. [TypeScript Documentation](https://www.typescriptlang.org/docs/) — Interfaces, classes, generics and type design.
6. [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide) — JavaScript language fundamentals.
7. [Node.js Documentation](https://nodejs.org/docs/latest/api/) — Node.js runtime APIs.

### Distributed Systems

8. [Martin Fowler – Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) — Event sourcing concepts.
9. [Microsoft – CQRS Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs) — CQRS architecture.
10. [Microsoft – Saga Distributed Transactions](https://learn.microsoft.com/en-us/azure/architecture/patterns/saga) — Saga pattern.
11. [Microsoft – Circuit Breaker](https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker) — Failure isolation.
12. [Enterprise Integration Patterns](https://www.enterpriseintegrationpatterns.com/) — Messaging and integration patterns.

### Testing and Architecture Quality

13. [Vitest Documentation](https://vitest.dev/) — Unit and integration testing.
14. [Jest Documentation](https://jestjs.io/docs/getting-started) — JavaScript testing.
15. [Dependency Cruiser](https://github.com/sverweij/dependency-cruiser) — Dependency and architecture rules.
16. [ESLint Documentation](https://eslint.org/docs/latest/) — Code quality and import restrictions.

---

# Phase 69 – Final Summary

In this phase, you learned how to design software at a deeper architectural level.

The most important concepts are:

1. **SOLID:** Build maintainable object-oriented software.
2. **DRY, KISS and YAGNI:** Avoid duplication and unnecessary complexity.
3. **High Cohesion and Low Coupling:** Create independent, focused modules.
4. **Creational Patterns:** Control object creation.
5. **Structural Patterns:** Organize objects and their relationships.
6. **Behavioral Patterns:** Manage communication and responsibilities.
7. **CQRS:** Separate read and write responsibilities.
8. **Event Sourcing:** Store state changes as events.
9. **Transactional Outbox:** Improve reliable event publication.
10. **Saga:** Coordinate distributed business transactions.
11. **Circuit Breaker and Bulkhead:** Improve system resilience.
12. **Idempotency:** Prevent duplicate business effects.
13. **Strangler Fig:** Modernize legacy applications incrementally.
14. **Architecture Testing:** Protect architectural boundaries.
15. **Refactoring:** Improve existing code without intentionally changing its behavior.

## Which Concepts Should You Master First?

| Priority | Concepts |
|---|---|
| Essential | SOLID |
| Essential | DRY, KISS, YAGNI |
| Essential | High Cohesion and Low Coupling |
| Essential | Factory Pattern |
| Essential | Strategy Pattern |
| Essential | Adapter Pattern |
| Essential | Decorator Pattern |
| Essential | Observer Pattern |
| Essential | State Pattern |
| Essential | Repository and Dependency Inversion |
| Important | Builder Pattern |
| Important | Facade Pattern |
| Important | Proxy Pattern |
| Important | Command Pattern |
| Important | Specification Pattern |
| Important | CQRS |
| Important | Transactional Outbox |
| Advanced | Event Sourcing |
| Advanced | Saga |
| Advanced | Circuit Breaker |
| Advanced | Bulkhead |
| Advanced | Anti-Corruption Layer |
| Advanced | Strangler Fig |
| Expert | Distributed Transactions |
| Expert | Enterprise Integration Patterns |
| Expert | Architecture Testing |

**My recommendation:** Do not try to memorize all 25 design patterns. Understand the problem each pattern solves, implement it in TypeScript, and learn when *not* to use it. In real enterprise development, a few well-applied patterns are more valuable than dozens of unnecessary abstractions.

---

# Next Phase Preview

## Phase 70 – Advanced Distributed Systems, Microservices Communication and Enterprise Reliability Engineering

Topics planned:

1. Distributed Systems Fundamentals.
2. Distributed Computing.
3. CAP Theorem.
4. PACELC Theorem.
5. Consistency Models.
6. Strong Consistency.
7. Eventual Consistency.
8. Causal Consistency.
9. Distributed Consensus.
10. Leader Election.
11. Distributed Locks.
12. Fencing Tokens.
13. Distributed Coordination.
14. Service Discovery.
15. Client-Side Discovery.
16. Server-Side Discovery.
17. Load Balancing Algorithms.
18. Consistent Hashing.
19. Distributed Caching.
20. Distributed Databases.
21. Database Sharding.
22. Horizontal Partitioning.
23. Replication.
24. Primary-Replica Architecture.
25. Multi-Region Architecture.
26. Message Delivery Semantics.
27. At-Most-Once Delivery.
28. At-Least-Once Delivery.
29. Exactly-Once Processing Claims and Limitations.
30. Message Ordering.
31. Event-Driven Microservices.
32. Kafka Advanced Architecture.
33. RabbitMQ Advanced Architecture.
34. Stream Processing.
35. Backpressure.
36. Distributed Tracing.
37. Failure Detection.
38. Fault Tolerance.
39. High Availability.
40. Disaster Recovery.
41. Recovery Point Objective (RPO).
42. Recovery Time Objective (RTO).
43. Chaos Engineering.
44. Resilience Testing.
45. Distributed System Observability.
46. Multi-Region Failover.
47. Zero-Downtime Architecture.
48. Enterprise Reliability Engineering.
49. Complete Distributed E-Commerce Architecture.
50. Distributed Systems Interview Preparation.

**Say "OK, Phase 70" when you're ready to continue.**