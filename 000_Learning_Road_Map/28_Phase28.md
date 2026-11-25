# **Phase 28 – Clean Architecture, Hexagonal Architecture and Dependency Injection**



Advanced Level

Next.js 16.x

TypeScript



## **1. Learning Objectives**

In this phase, you will learn how to design applications so that business logic remains independent of frameworks, databases, external APIs, and infrastructure.

By the end, you should be able to:

1. Understand Clean Architecture and its Dependency Rule.
2. Separate business rules from application workflows and infrastructure.
3. Understand Hexagonal Architecture, Ports, and Adapters.
4. Apply the Dependency Inversion Principle (DIP).
5. Understand Dependency Injection (DI) and Inversion of Control (IoC).
6. Organize a Node.js and Next.js application using these principles.
7. Write unit tests without requiring a real database or external API.
8. Decide when architectural separation is useful and when it introduces unnecessary complexity.

## **2. Why Do We Need Clean Architecture?**

Imagine you are developing an e-commerce application using:

- Next.js for the frontend and backend.
- MongoDB for database storage.
- Stripe for payment processing.
- Nodemailer for sending emails.

A typical implementation might look like this:

```
// app/api/orders/route.ts

import { MongoClient } from "mongodb";
import Stripe from "stripe";
import nodemailer from "nodemailer";

export async function POST(request: Request) {
  const body = await request.json();

  // Save order in MongoDB
  const client = new MongoClient(process.env.MONGODB_URI!);
  await client.connect();

  const db = client.db("ecommerce");

  const order = await db.collection("orders").insertOne({
    userId: body.userId,
    amount: body.amount,
  });

  // Process payment using Stripe
  const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

  await stripe.paymentIntents.create({
    amount: body.amount * 100,
    currency: "inr",
  });

  // Send email
  const transporter = nodemailer.createTransport({
    // SMTP configuration
  });

  await transporter.sendMail({
    to: body.email,
    subject: "Order Confirmation",
    text: "Your order has been placed.",
  });

  return Response.json({ success: true });
}
```

This code may work, but it has architectural problems.

**Problem 1: Too many responsibilities**

The route handler performs HTTP handling, business operations, database operations, payment processing, and email delivery.

**Problem 2: Tight coupling**

The business workflow directly depends on MongoDB, Stripe, and Nodemailer.

**Problem 3: Difficult testing**

Testing the order workflow may require database connections and external service mocks.

**Problem 4: Difficult replacement**

Suppose you want to replace MongoDB with MySQL or Stripe with another payment provider. Changes may spread throughout your application.

**Problem 5: Business logic is not independent**

Your core business rules are mixed with technical implementation details.

Clean Architecture addresses these problems by organizing code around business rules rather than around frameworks or tools.

## **3. What Is Clean Architecture?**

Clean Architecture is a software architectural approach that separates an application into layers, keeping its most important business rules independent of external technologies.

It is commonly associated with Robert C. Martin (Uncle Bob).

Its central idea:

**The business logic should not depend on implementation details such as databases, frameworks, or external services.**

[Breaking Down Clean Architecture — Core Ideas, Benefits, Drawbacks, and Use Cases](https://images.openai.com/static-rsc-4/RsLRrJY9VNvB2LrVEqEaDi515HakGdt1KkQUQ4DIdwExGhquIjuXcP1mepGTRZniWKJyPzr6Edhzfw14_oXANvEAKXNEpZcg6kUEoP6QzA2PPGq3dXy72_1o6kMIbA5impkTGsSagOfdD89Yvcaq11RCv9pGAM1LHnWPNls4TDU?purpose=inline)

[blog.levelupcoding.com](https://blog.levelupcoding.com/p/breaking-down-clean-architecture-core-ideas-benefits-drawbacks-and-use-cases?utm_source=chatgpt.com)

The traditional Clean Architecture model has four major layers:

| Layer                  | Responsibility                                                        |
| ---------------------- | --------------------------------------------------------------------- |
| Entities               | Core business rules                                                   |
| Use Cases              | Application-specific business workflows                               |
| Interface Adapters     | Convert data between external systems and internal application models |
| Frameworks and Drivers | Databases, web frameworks, UI, external APIs                          |

The most important rule is that dependencies point inward.

In other words:

- Inner layers must not depend on outer layers.
- Outer layers can depend on inner layers.
- Business rules should not know which database or framework is being used.

We will examine each layer in detail.

## **4. The Four Layers of Clean Architecture**

### **4.1 Entities – Enterprise Business Rules**

Entities represent the core business concepts and rules of your application.

They should not depend on:

- Next.js
- React
- Express
- MongoDB
- MySQL
- Stripe
- Any external framework or library

Consider an e-commerce application.

An order might have rules such as:

- An order must contain at least one item.
- An order cannot have a negative total.
- Only a pending order can be cancelled.
- A completed order cannot be cancelled.

These are business rules. They should exist independently of the database or API.

Example:

```
// domain/entities/Order.ts

export type OrderItem = {
  productId: string;
  quantity: number;
  price: number;
};

export class Order {
  constructor(
    public readonly id: string,
    public readonly userId: string,
    public readonly items: OrderItem[],
    public status: "pending" | "paid" | "cancelled"
  ) {
    if (items.length === 0) {
      throw new Error("An order must contain at least one item");
    }

    for (const item of items) {
      if (item.quantity <= 0 || item.price < 0) {
        throw new Error("Invalid order item");
      }
    }
  }

  getTotal(): number {
    return this.items.reduce(
      (total, item) => total + item.price * item.quantity,
      0
    );
  }

  cancel(): void {
    if (this.status !== "pending") {
      throw new Error("Only pending orders can be cancelled");
    }

    this.status = "cancelled";
  }

  markAsPaid(): void {
    if (this.status !== "pending") {
      throw new Error("Only pending orders can be paid");
    }

    this.status = "paid";
  }
}
```

Notice that this entity has no knowledge of MongoDB, Stripe, or Next.js.

It only knows about order-related business rules.

**Important:** An Entity is not necessarily an ORM model or a database document. An entity represents a business concept and its identity and rules. A database document is a persistence representation.

### **4.2 Use Cases – Application Business Rules**

A Use Case describes an operation that the application allows someone to perform.

Examples:

- Register a user.
- Place an order.
- Cancel an order.
- Process a payment.
- Update a product.
- Generate an invoice.

An Entity describes business rules for an object.

A Use Case coordinates entities and other services to complete an application operation.

For example:

**Use Case: Place Order**

1. Receive order details.
2. Validate the requested items.
3. Create an order.
4. Save the order.
5. Start payment processing.
6. Return the result.

The Use Case should not know how MongoDB saves data or how Stripe processes payments.

It should work with abstractions.

```
// application/use-cases/PlaceOrder.ts

import { Order } from "../../domain/entities/Order";
import type { OrderRepository } from "../ports/OrderRepository";
import type { PaymentGateway } from "../ports/PaymentGateway";

type PlaceOrderInput = {
  userId: string;
  items: {
    productId: string;
    quantity: number;
    price: number;
  }[];
};

export class PlaceOrder {
  constructor(
    private readonly orderRepository: OrderRepository,
    private readonly paymentGateway: PaymentGateway
  ) {}

  async execute(input: PlaceOrderInput) {
    const order = new Order(
      crypto.randomUUID(),
      input.userId,
      input.items,
      "pending"
    );

    await this.orderRepository.save(order);

    const payment = await this.paymentGateway.charge({
      orderId: order.id,
      amount: order.getTotal(),
    });

    return {
      orderId: order.id,
      total: order.getTotal(),
      paymentId: payment.paymentId,
    };
  }
}
```

Here:

- `PlaceOrder` is the Use Case.
- `Order` is the Entity.
- `OrderRepository` is an abstraction.
- `PaymentGateway` is an abstraction.

The Use Case does not import MongoDB or Stripe.

That is the key idea.

*Note:* This is a simplified teaching example. A production payment workflow needs additional handling for payment failures, order status transitions, retries, idempotency, and consistency between payment and order records.

### **4.3 Interface Adapters**

Interface Adapters convert data between the outside world and the application's internal models.

For example, consider a client sending this HTTP request:

```
{
  "user_id": "user-101",
  "products": [
    {
      "product_id": "product-1",
      "qty": 2,
      "unit_price": 500
    }
  ]
}
```

Your internal application may expect:

```
{
  userId: "user-101",
  items: [
    {
      productId: "product-1",
      quantity: 2,
      price: 500
    }
  ]
}
```

An adapter transforms the external request into the format expected by the application.

Common Interface Adapters include:

| Adapter                    | Responsibility                                     |
| -------------------------- | -------------------------------------------------- |
| Controllers                | Convert HTTP requests into Use Case inputs         |
| Presenters                 | Convert Use Case outputs into response formats     |
| Repository implementations | Convert domain data into database operations       |
| API adapters               | Convert external API formats into internal formats |

Example controller:

```
// interfaces/controllers/PlaceOrderController.ts

import type { PlaceOrder } from "../../application/use-cases/PlaceOrder";

export class PlaceOrderController {
  constructor(private readonly placeOrder: PlaceOrder) {}

  async handle(body: any) {
    const input = {
      userId: body.user_id,
      items: body.products.map((product: any) => ({
        productId: product.product_id,
        quantity: product.qty,
        price: product.unit_price,
      })),
    };

    const result = await this.placeOrder.execute(input);

    return {
      order_id: result.orderId,
      total_amount: result.total,
      payment_id: result.paymentId,
    };
  }
}
```

The controller translates between HTTP-oriented data and application-oriented data.

In a real application, validate the request with a schema library such as Zod rather than relying on `any`.

### **4.4 Frameworks and Drivers**

This is the outermost layer.

It contains technical tools and external systems.

Examples:

- Next.js
- React
- Express
- MongoDB
- MySQL
- Redis
- Stripe
- AWS S3
- Nodemailer

These tools implement the technical details required by the application.

For example:

```
// infrastructure/database/MongoOrderRepository.ts

import type { Collection } from "mongodb";
import type { Order } from "../../domain/entities/Order";
import type { OrderRepository } from "../../application/ports/OrderRepository";

export class MongoOrderRepository implements OrderRepository {
  constructor(private readonly collection: Collection) {}

  async save(order: Order): Promise<void> {
    await this.collection.insertOne({
      _id: order.id,
      userId: order.userId,
      items: order.items,
      status: order.status,
    });
  }

  async findById(id: string): Promise<Order | null> {
    const document = await this.collection.findOne({ _id: id });

    if (!document) return null;

    return new Order(
      document._id,
      document.userId,
      document.items,
      document.status
    );
  }
}
```

The repository implementation knows MongoDB.

The Use Case does not.

If you replace MongoDB with MySQL, you can write another implementation without changing the core Use Case.

### **Understanding the dependency direction**

[Mobile Clean Architecture — Part one — Clean Architecture Fundamentals | by Phillip Johnson | Medium](https://images.openai.com/static-rsc-4/zLe13AAQU0aYzI-yC-Kdf7Rms-wDccJ4uEePlvj-17fRNAFcATAc_ftWqptBhWzLT0B5XkQRfSzBC_AoFlSQkC22Xma1uHbKB-z1k5fsKLeiGXzSIRNjx-dMtA5aadHtwyGdPCGRwgTJQ3KrjDSf8_veDgb-HwRTH81rDArnrE0?purpose=inline)

[philliptjohnson.medium.com](https://philliptjohnson.medium.com/mobile-clean-architecture-part-one-clean-architecture-fundamentals-df630f1a3b29?utm_source=chatgpt.com)

Think of the architecture this way:

```
Frameworks & Drivers
        |
        v
Interface Adapters
        |
        v
Application Use Cases
        |
        v
Domain Entities
```

This is a conceptual view of the dependency direction, not necessarily the runtime execution sequence.

In a real request, execution often starts at the outer layer and travels inward:

```
HTTP Request
    |
    v
Next.js Route Handler
    |
    v
Controller
    |
    v
PlaceOrder Use Case
    |
    v
Order Entity
```

The Use Case then calls its repository and payment abstractions. Their concrete implementations are provided from the outer layer.

The source-code dependency direction and runtime call direction are not the same thing.

The Dependency Rule is the foundation of Clean Architecture.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.informit.com\&sz=32)

InformIT+1



## **5. The Dependency Rule**

The Dependency Rule states:

**Source-code dependencies must point inward, toward higher-level business policies.**

Let's understand this using two examples.

### **Incorrect dependency**

```
PlaceOrder Use Case
        |
        v
MongoDB Repository
        |
        v
MongoDB Driver
```

Here, the Use Case directly depends on a MongoDB-specific implementation.

If you replace MongoDB, the Use Case may need changes.

### **Correct dependency**

```
        Application Layer
        -----------------
        PlaceOrder Use Case
                 |
                 v
        OrderRepository Interface
                 ^
                 |
        MongoDB Repository
        -----------------
        Infrastructure Layer
```

The interface is owned by the inner application layer.

The MongoDB repository implements that interface.

This is called dependency inversion.

**Remember:** The Use Case should define what it needs. Infrastructure should provide how that requirement is fulfilled.

## **6. Hexagonal Architecture**

Hexagonal Architecture is also known as **Ports and Adapters Architecture**.

It was introduced by Alistair Cockburn.

Its objective is to isolate the application core from external technologies such as:

- Databases
- User interfaces
- External APIs
- Message brokers
- File systems

[Гексагональная архитектура для Node.js-приложения, или Как сделать код более поддерживаемым | DOU](https://images.openai.com/static-rsc-4/uzF0XRbb0NgKqcI5h9F-W8NnLwd4dj_7s9duD6GPd3ooKnZJKqw4dlcQwJrCk3sAfEMvedhvBSicH8P8CFYo7MKdGyoFaR1Ucl9W-Pr2frYb_lumz1Hz5vF140H3tj60NlG46P0vnE0KwljYVXvmyKmCgH8k9aMqVVDeJORgpqw?purpose=inline)

[dou.ua](https://dou.ua/lenta/articles/hexagonal-architecture-for-nodejs/?utm_source=chatgpt.com)

Imagine your application as a central component surrounded by different external systems.

The application core communicates with those systems through defined interfaces.

Those interfaces are called Ports.

The components that implement or use those interfaces are called Adapters.

For example:

```
                 REST API
                    |
                    v
              HTTP Adapter
                    |
                    v
        +------------------------+
        |                        |
        |    APPLICATION CORE    |
        |                        |
        |      Place Order       |
        |                        |
        +------------------------+
             |            |
             v            v
       Database Port   Payment Port
             |            |
             v            v
       Mongo Adapter   Stripe Adapter
             |            |
             v            v
          MongoDB       Stripe API
```

The application core does not need to know whether the external system is MongoDB, MySQL, Stripe, or another provider.

It only knows the interfaces it requires.

AWS describes this architecture as a way to decouple business logic from data stores and user interfaces, making components easier to replace and test.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.aws.amazon.com\&sz=32)

AWS Prescriptive Guidance+1



## **7. Ports and Adapters**

Let's examine these two concepts separately.

### **7.1 What Is a Port?**

A Port is an interface that defines how the application communicates with something outside itself.

There are two major categories.

**A. Driving Ports (Primary Ports)**

These define how external actors can interact with the application.

Examples:

- Place an order.
- Register a user.
- Cancel an order.
- Retrieve order details.

A driving port may be represented by a Use Case interface.

```
// application/ports/PlaceOrderPort.ts

export interface PlaceOrderPort {
  execute(input: PlaceOrderInput): Promise<PlaceOrderResult>;
}
```

**B. Driven Ports (Secondary Ports)**

These define what the application needs from external systems.

Examples:

- Save an order.
- Find a user.
- Process a payment.
- Send an email.
- Upload an image.

Example:

```
// application/ports/OrderRepository.ts

import type { Order } from "../../domain/entities/Order";

export interface OrderRepository {
  save(order: Order): Promise<void>;

  findById(id: string): Promise<Order | null>;
}
```

Another example:

```
// application/ports/PaymentGateway.ts

export interface PaymentGateway {
  charge(input: {
    orderId: string;
    amount: number;
  }): Promise<{
    paymentId: string;
  }>;
}
```

These interfaces are technology-independent.

They do not mention MongoDB, Stripe, or any particular provider.

### **7.2 What Is an Adapter?**

An Adapter is an implementation that connects an external technology to a Port.

For example:

| Port            | Adapter                |
| --------------- | ---------------------- |
| OrderRepository | MongoOrderRepository   |
| OrderRepository | MySQLOrderRepository   |
| PaymentGateway  | StripePaymentAdapter   |
| PaymentGateway  | RazorpayPaymentAdapter |
| EmailService    | NodemailerAdapter      |
| EmailService    | ResendAdapter          |

Example payment adapter:

```
// infrastructure/payment/StripePaymentAdapter.ts

import Stripe from "stripe";
import type { PaymentGateway } from "../../application/ports/PaymentGateway";

export class StripePaymentAdapter implements PaymentGateway {
  constructor(private readonly stripe: Stripe) {}

  async charge(input: {
    orderId: string;
    amount: number;
  }) {
    const paymentIntent = await this.stripe.paymentIntents.create({
      amount: Math.round(input.amount * 100),
      currency: "inr",
      metadata: {
        orderId: input.orderId,
      },
    });

    return {
      paymentId: paymentIntent.id,
    };
  }
}
```

Now suppose you want to replace Stripe with Razorpay.

You can implement another adapter:

```
// infrastructure/payment/RazorpayPaymentAdapter.ts

import type { PaymentGateway } from "../../application/ports/PaymentGateway";

export class RazorpayPaymentAdapter implements PaymentGateway {
  async charge(input: {
    orderId: string;
    amount: number;
  }) {
    // Call Razorpay API here.

    return {
      paymentId: "razorpay-payment-id",
    };
  }
}
```

Your `PlaceOrder` Use Case does not need to change.

You only change which adapter is injected.

Key takeaway

A Port defines the contract. An Adapter provides the implementation.

### **7.3 Primary vs Secondary Adapters**

| Primary Adapter                    | Secondary Adapter                                           |
| ---------------------------------- | ----------------------------------------------------------- |
| Initiates an application operation | Performs an external operation requested by the application |
| Usually drives the application     | Usually supports the application                            |
| Examples: REST controller, CLI, UI | Examples: MongoDB repository, Stripe adapter, email adapter |
| Calls the application's ports      | Implements the application's ports                          |

For example:

- A Next.js Route Handler is an entry point.
- A controller translates its request.
- The Use Case executes the operation.
- The MongoDB adapter saves data.
- The Stripe adapter processes payment.

**Important:** A Next.js page, Route Handler, or Server Action is not automatically a complete Clean Architecture controller. It is an external entry point that can delegate to an application-layer controller or Use Case.

## **8. Dependency Inversion Principle (DIP)**

Dependency Inversion Principle is one of the five SOLID principles.

It says:

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
2. Abstractions should not depend on details. Details should depend on abstractions.

Let's understand it practically.

### **8.1 Without Dependency Inversion**

Imagine a notification service.

```
class EmailService {
  sendEmail(message: string) {
    console.log("Email sent:", message);
  }
}

class OrderService {
  private emailService = new EmailService();

  placeOrder() {
    // Place order logic

    this.emailService.sendEmail("Order placed");
  }
}
```

Problem:

`OrderService` directly creates and depends on `EmailService`.

What if you want to send notifications through SMS or WhatsApp?

You would need to modify the high-level service.

### **8.2 With Dependency Inversion**

First, define an abstraction.

```
interface NotificationService {
  send(message: string): void;
}
```

Now implement it:

```
class EmailService implements NotificationService {
  send(message: string) {
    console.log("Email sent:", message);
  }
}
```

Another implementation:

```
class SMSService implements NotificationService {
  send(message: string) {
    console.log("SMS sent:", message);
  }
}
```

Now update the high-level module:

```
class OrderService {
  constructor(
    private readonly notificationService: NotificationService
  ) {}

  placeOrder() {
    // Place order logic

    this.notificationService.send("Order placed");
  }
}
```

Now `OrderService` does not know whether the notification is sent by email or SMS.

It only knows the `NotificationService` contract.

The dependency is inverted:

```
Before:

OrderService ---> EmailService


After:

OrderService ---> NotificationService
                       ^
                       |
                  EmailService

                       ^
                       |
                   SMSService
```

This makes the system easier to extend and test.

Microsoft's architecture guidance explains that DIP makes it possible for implementations to depend on abstractions controlled by higher-level code, rather than forcing high-level code to depend on implementation details.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



### **8.3 DIP vs Dependency Injection**

These terms are related, but they are not the same.

| Dependency Inversion Principle                | Dependency Injection                  |
| --------------------------------------------- | ------------------------------------- |
| A design principle                            | A technique or pattern                |
| Defines how dependencies should be structured | Defines how dependencies are supplied |
| Encourages abstractions                       | Supplies concrete implementations     |
| Part of SOLID                                 | Helps implement DIP                   |

**DIP tells you what dependency relationship to design. DI helps you provide the dependency.**

## **9. Dependency Injection (DI)**

Dependency Injection is a technique in which an object receives its dependencies from outside instead of creating them internally.

Let's understand this with a simple example.

### **9.1 Without Dependency Injection**

```
class UserRepository {
  findUser(id: string) {
    return {
      id,
      name: "Riyaz",
    };
  }
}

class UserService {
  private repository = new UserRepository();

  getUser(id: string) {
    return this.repository.findUser(id);
  }
}
```

Here, `UserService` creates its own repository.

This creates tight coupling.

### **9.2 With Dependency Injection**

```
interface UserRepository {
  findUser(id: string): {
    id: string;
    name: string;
  };
}

class MongoUserRepository implements UserRepository {
  findUser(id: string) {
    return {
      id,
      name: "Riyaz",
    };
  }
}

class UserService {
  constructor(
    private readonly repository: UserRepository
  ) {}

  getUser(id: string) {
    return this.repository.findUser(id);
  }
}
```

Now create the dependency outside:

```
const repository = new MongoUserRepository();

const userService = new UserService(repository);

const user = userService.getUser("user-101");

console.log(user);
```

The `UserService` receives its dependency through its constructor.

This is Constructor Injection.

### **9.3 Three Common Types of Dependency Injection**

**1. Constructor Injection**

Dependencies are supplied through the constructor.

```
class UserService {
  constructor(
    private readonly repository: UserRepository
  ) {}
}

const service = new UserService(
  new MongoUserRepository()
);
```

Generally preferred

Why?

- Dependencies are explicit.
- Required dependencies are available when the object is created.
- Testing is straightforward.
- The object cannot be constructed without its required dependencies.

**2. Setter Injection**

Dependencies are supplied through a method after object creation.

```
class UserService {
  private repository!: UserRepository;

  setRepository(repository: UserRepository) {
    this.repository = repository;
  }
}

const service = new UserService();

service.setRepository(new MongoUserRepository());
```

Use it when a dependency is optional or needs to be changed after construction.

Be careful: the service can be used before its dependency has been set.

**3. Property Injection**

Dependencies are assigned directly to a property.

```
class UserService {
  repository!: UserRepository;
}

const service = new UserService();

service.repository = new MongoUserRepository();
```

This is less explicit and can allow invalid object states. Constructor injection is usually a better default.

Martin Fowler discusses these injection styles and recommends separating dependency configuration from dependency use.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.martinfowler.com\&sz=32)

martinfowler.com+1



## **10. Inversion of Control (IoC)**

Inversion of Control is a broader design principle in which control over some part of program execution is transferred from your application code to another component or framework.

Normally, your code controls the execution flow:

```
function application() {
  const user = getUser();

  validateUser(user);

  saveUser(user);
}

application();
```

Your code decides when each operation runs.

With a framework, the framework may control the main execution flow and call your code when appropriate.

For example, in Next.js:

```
// app/page.tsx

export default function HomePage() {
  return <h1>Welcome</h1>;
}
```

You do not manually call `HomePage()` to render the page.

Next.js discovers the file and invokes the component as part of its rendering process.

That is an example of framework-level Inversion of Control.

### **IoC vs DI**

| IoC                                                     | DI                                             |
| ------------------------------------------------------- | ---------------------------------------------- |
| Broad principle                                         | Specific technique                             |
| Transfers control to another component or framework     | Supplies dependencies from outside             |
| Can appear in frameworks, event systems, and containers | Often used to implement IoC                    |
| Example: Next.js invokes your page component            | Example: injecting a repository into a service |

**Remember: Dependency Injection is one way to achieve Inversion of Control, but not every form of IoC is DI.**&#x20;

[image](https://www.google.com/s2/favicons?domain=https://martinfowler.com\&sz=32)

martinfowler.com+1



## **11. Composition Root**

A Composition Root is the place where your application's dependencies are created and connected.

In simple terms:

**It is the place where you wire everything together.**

Consider these components:

```
PlaceOrder Use Case
    |
    +---- OrderRepository
    |
    +---- PaymentGateway
```

The Use Case needs two dependencies.

The Composition Root creates their concrete implementations and injects them.

Example:

```
// infrastructure/container/orderContainer.ts

import { MongoOrderRepository } from "../database/MongoOrderRepository";
import { StripePaymentAdapter } from "../payment/StripePaymentAdapter";

import { PlaceOrder } from "../../application/use-cases/PlaceOrder";

import { getDatabase } from "../database/mongodb";
import Stripe from "stripe";

export async function createPlaceOrderUseCase() {
  const db = await getDatabase();

  const orderRepository = new MongoOrderRepository(
    db.collection("orders")
  );

  const stripe = new Stripe(
    process.env.STRIPE_SECRET_KEY!
  );

  const paymentGateway = new StripePaymentAdapter(stripe);

  return new PlaceOrder(
    orderRepository,
    paymentGateway
  );
}
```

The Composition Root knows the concrete implementations.

The Use Case does not.

This is an important architectural boundary.

**Where should the Composition Root exist?**

It depends on the application:

- Node.js application: startup or bootstrap module.
- Express application: application setup file.
- Next.js application: server-only factory or composition module called from server entry points.
- Test environment: test setup where fake implementations are injected.

Avoid constructing a new database connection or a new dependency graph on every request without considering connection pooling, lifecycle, and reuse.

## **12. Clean Architecture with Node.js and TypeScript**

Now let's combine everything into a practical Node.js application.

Suppose we are building an e-commerce backend with:

- Node.js
- TypeScript
- MongoDB
- Stripe
- Express or Next.js as the HTTP entry point

### **12.1 Recommended Folder Structure**

```
src/
│
├── domain/
│   ├── entities/
│   │   └── Order.ts
│   │
│   └── errors/
│       └── DomainError.ts
│
├── application/
│   ├── ports/
│   │   ├── OrderRepository.ts
│   │   └── PaymentGateway.ts
│   │
│   └── use-cases/
│       └── PlaceOrder.ts
│
├── interfaces/
│   ├── controllers/
│   │   └── PlaceOrderController.ts
│   │
│   └── presenters/
│       └── OrderPresenter.ts
│
├── infrastructure/
│   ├── database/
│   │   ├── mongodb.ts
│   │   └── MongoOrderRepository.ts
│   │
│   ├── payment/
│   │   └── StripePaymentAdapter.ts
│   │
│   └── container/
│       └── orderContainer.ts
│
└── main.ts
```

### **12.2 Understand the Responsibilities**

| Folder           | Responsibility                                |
| ---------------- | --------------------------------------------- |
| `domain`         | Business entities and domain rules            |
| `application`    | Use Cases and ports                           |
| `interfaces`     | Controllers and presenters                    |
| `infrastructure` | Database and external service implementations |
| `main.ts`        | Application startup and dependency wiring     |

### **12.3 Dependency Direction**

```
infrastructure
      |
      v
interfaces
      |
      v
application
      |
      v
domain
```

This is a simplified dependency view. In practice, Infrastructure also implements interfaces declared in Application, so its code imports those abstractions.

The important point is that `domain` and `application` should not import concrete Infrastructure modules.

### **12.4 Complete Request Flow**

Consider a customer placing an order.

```
Customer
   |
   v
HTTP Request
   |
   v
Controller
   |
   v
PlaceOrder Use Case
   |
   +----> Order Entity
   |
   +----> OrderRepository Port
   |             |
   |             v
   |       MongoDB Adapter
   |
   +----> PaymentGateway Port
                 |
                 v
           Stripe Adapter
   |
   v
Presenter
   |
   v
HTTP Response
```

The key architectural benefit is that business workflows are independent of the specific database, payment provider, and HTTP framework.

## **13. Clean Architecture with Next.js 16**

Now let's apply the same concepts to a Next.js App Router project.

Next.js gives us:

- Server Components
- Client Components
- Route Handlers
- Server Actions
- Framework-level routing and rendering

But these framework features should not force our business logic to depend on Next.js.

### **13.1 Recommended Next.js Folder Structure**

```
src/
│
├── app/
│   ├── api/
│   │   └── orders/
│   │       └── route.ts
│   │
│   ├── orders/
│   │   └── page.tsx
│   │
│   └── layout.tsx
│
├── modules/
│   └── orders/
│       │
│       ├── domain/
│       │   └── entities/
│       │       └── Order.ts
│       │
│       ├── application/
│       │   ├── ports/
│       │   │   ├── OrderRepository.ts
│       │   │   └── PaymentGateway.ts
│       │   │
│       │   └── use-cases/
│       │       └── PlaceOrder.ts
│       │
│       ├── infrastructure/
│       │   ├── MongoOrderRepository.ts
│       │   ├── StripePaymentAdapter.ts
│       │   └── orderContainer.ts
│       │
│       └── interfaces/
│           └── PlaceOrderController.ts
│
└── shared/
    └── infrastructure/
        └── mongodb.ts
```

This structure uses a feature-based organization: all order-related business logic is grouped inside the `orders` module.

### **13.2 Step 1: Create the Domain Entity**

```
// modules/orders/domain/entities/Order.ts

export type OrderItem = {
  productId: string;
  quantity: number;
  price: number;
};

export class Order {
  constructor(
    public readonly id: string,
    public readonly userId: string,
    public readonly items: OrderItem[],
    public status: "pending" | "paid" | "cancelled"
  ) {
    if (!items.length) {
      throw new Error("Order must contain items");
    }
  }

  getTotal() {
    return this.items.reduce(
      (total, item) => total + item.price * item.quantity,
      0
    );
  }
}
```

This file does not import anything from Next.js.

### **13.3 Step 2: Define the Repository Port**

```
// modules/orders/application/ports/OrderRepository.ts

import type { Order } from "../../domain/entities/Order";

export interface OrderRepository {
  save(order: Order): Promise<void>;
}
```

This interface belongs to the Application layer because the Use Case needs persistence.

### **13.4 Step 3: Define the Payment Port**

```
// modules/orders/application/ports/PaymentGateway.ts

export interface PaymentGateway {
  charge(input: {
    orderId: string;
    amount: number;
  }): Promise<{
    paymentId: string;
  }>;
}
```

The application does not need to know which payment provider is being used.

### **13.5 Step 4: Create the Use Case**

```
// modules/orders/application/use-cases/PlaceOrder.ts

import { Order } from "../../domain/entities/Order";

import type { OrderRepository } from "../ports/OrderRepository";
import type { PaymentGateway } from "../ports/PaymentGateway";

export class PlaceOrder {
  constructor(
    private readonly repository: OrderRepository,
    private readonly paymentGateway: PaymentGateway
  ) {}

  async execute(input: {
    userId: string;
    items: {
      productId: string;
      quantity: number;
      price: number;
    }[];
  }) {
    const order = new Order(
      crypto.randomUUID(),
      input.userId,
      input.items,
      "pending"
    );

    await this.repository.save(order);

    const payment = await this.paymentGateway.charge({
      orderId: order.id,
      amount: order.getTotal(),
    });

    return {
      orderId: order.id,
      paymentId: payment.paymentId,
      total: order.getTotal(),
    };
  }
}
```

Notice the imports:

- It imports the Domain Entity.
- It imports Application interfaces using `import type`.
- It does not import MongoDB.
- It does not import Stripe.
- It does not import Next.js.

This is exactly what we want.

### **13.6 Step 5: Implement the MongoDB Adapter**

```
// modules/orders/infrastructure/MongoOrderRepository.ts

import type { Collection } from "mongodb";

import type { OrderRepository } from "../application/ports/OrderRepository";
import type { Order } from "../domain/entities/Order";

export class MongoOrderRepository implements OrderRepository {
  constructor(private readonly collection: Collection) {}

  async save(order: Order) {
    await this.collection.insertOne({
      _id: order.id,
      userId: order.userId,
      items: order.items,
      status: order.status,
    });
  }
}
```

This class depends on the application interface and implements it.

### **13.7 Step 6: Implement the Stripe Adapter**

```
// modules/orders/infrastructure/StripePaymentAdapter.ts

import Stripe from "stripe";

import type { PaymentGateway } from "../application/ports/PaymentGateway";

export class StripePaymentAdapter implements PaymentGateway {
  constructor(private readonly stripe: Stripe) {}

  async charge(input: {
    orderId: string;
    amount: number;
  }) {
    const result = await this.stripe.paymentIntents.create({
      amount: Math.round(input.amount * 100),
      currency: "inr",
      metadata: {
        orderId: input.orderId,
      },
    });

    return {
      paymentId: result.id,
    };
  }
}
```

### **13.8 Step 7: Create the Composition Root**

```
// modules/orders/infrastructure/orderContainer.ts

import "server-only";

import Stripe from "stripe";

import { PlaceOrder } from "../application/use-cases/PlaceOrder";

import { MongoOrderRepository } from "./MongoOrderRepository";
import { StripePaymentAdapter } from "./StripePaymentAdapter";

import { getDatabase } from "@/shared/infrastructure/mongodb";

export async function createPlaceOrderUseCase() {
  const db = await getDatabase();

  const repository = new MongoOrderRepository(
    db.collection("orders")
  );

  const stripe = new Stripe(
    process.env.STRIPE_SECRET_KEY!
  );

  const paymentGateway = new StripePaymentAdapter(stripe);

  return new PlaceOrder(repository, paymentGateway);
}
```

Here:

- MongoDB implementation is created.
- Stripe implementation is created.
- Both dependencies are injected into `PlaceOrder`.
- The composition module is marked server-only.

The `server-only` marker helps prevent accidental imports of this module into a Client Component. Next.js documents this technique for protecting server-side modules and secrets.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **13.9 Step 8: Connect the Use Case to a Route Handler**

```
// app/api/orders/route.ts

import { NextRequest, NextResponse } from "next/server";

import { createPlaceOrderUseCase } from "@/modules/orders/infrastructure/orderContainer";

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();

    // Validate the body with Zod in a real application.
    const useCase = await createPlaceOrderUseCase();

    const result = await useCase.execute({
      userId: body.userId,
      items: body.items,
    });

    return NextResponse.json(result, {
      status: 201,
    });
  } catch (error) {
    console.error("Order creation failed", error);

    return NextResponse.json(
      { message: "Unable to create order" },
      { status: 500 }
    );
  }
}
```

The Route Handler is responsible for HTTP concerns.

It should not contain the full business workflow.

For a production system, add authentication and authorization, request validation, safe error mapping, and proper handling of payment failures. Also consider whether the Route Handler should call a dedicated controller instead of directly invoking the Use Case.

### **13.10 What About Server Actions?**

The same Use Case can be called from a Server Action.

```
// app/orders/actions.ts

"use server";

import { createPlaceOrderUseCase } from "@/modules/orders/infrastructure/orderContainer";

export async function placeOrderAction(formData: FormData) {
  // Authenticate and authorize the user here.
  // Validate and parse FormData here.

  const useCase = await createPlaceOrderUseCase();

  return useCase.execute({
    userId: String(formData.get("userId")),
    items: JSON.parse(String(formData.get("items"))),
  });
}
```

This is a simplified illustration. Never trust a `userId` supplied by the browser; derive the authenticated identity from a verified server-side session. Validate the parsed input before passing it to the Use Case.

**Important:** A Server Action is a framework entry point, not a replacement for the Application layer. It should delegate to your Use Case.

Next.js supports Route Handlers as HTTP endpoints and Server Components as server-side rendering units, but your business logic can remain in separate application modules.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **14. Testing Architecture Boundaries**

One of the biggest benefits of Clean Architecture is testability.

Because the Use Case depends on interfaces rather than concrete infrastructure, you can test it without connecting to MongoDB or Stripe.

### **14.1 Why Is This Useful?**

Consider a conventional implementation:

```
class OrderService {
  async placeOrder() {
    const db = await connectToMongoDB();

    // Database operations
    // Stripe operations
    // Business logic
  }
}
```

Testing this service may require:

- A MongoDB connection.
- Stripe mocks or credentials.
- Environment variables.
- Additional setup.

With dependency injection, you can supply fake implementations.

### **14.2 Create a Fake Repository**

```
// tests/fakes/FakeOrderRepository.ts

import type { OrderRepository } from "../../src/modules/orders/application/ports/OrderRepository";
import type { Order } from "../../src/modules/orders/domain/entities/Order";

export class FakeOrderRepository implements OrderRepository {
  public orders: Order[] = [];

  async save(order: Order) {
    this.orders.push(order);
  }
}
```

### **14.3 Create a Fake Payment Gateway**

```
// tests/fakes/FakePaymentGateway.ts

import type { PaymentGateway } from "../../src/modules/orders/application/ports/PaymentGateway";

export class FakePaymentGateway implements PaymentGateway {
  async charge() {
    return {
      paymentId: "fake-payment-101",
    };
  }
}
```

### **14.4 Test the Use Case**

Example using Vitest:

```
// tests/PlaceOrder.test.ts

import { describe, it, expect } from "vitest";

import { PlaceOrder } from "../src/modules/orders/application/use-cases/PlaceOrder";

import { FakeOrderRepository } from "./fakes/FakeOrderRepository";
import { FakePaymentGateway } from "./fakes/FakePaymentGateway";

describe("PlaceOrder", () => {
  it("should create an order and process payment", async () => {
    const repository = new FakeOrderRepository();
    const paymentGateway = new FakePaymentGateway();

    const useCase = new PlaceOrder(
      repository,
      paymentGateway
    );

    const result = await useCase.execute({
      userId: "user-101",
      items: [
        {
          productId: "product-1",
          quantity: 2,
          price: 500,
        },
      ],
    });

    expect(repository.orders).toHaveLength(1);

    expect(result.total).toBe(1000);

    expect(result.paymentId).toBe("fake-payment-101");
  });
});
```

This test does not require a real MongoDB database or Stripe account.

### **14.5 What Should You Test at Each Layer?**

| Layer                  | Testing approach                                      |
| ---------------------- | ----------------------------------------------------- |
| Domain                 | Unit tests for entities and business rules            |
| Application            | Unit tests using fake ports                           |
| Interface Adapters     | Test data conversion and response formatting          |
| Infrastructure         | Integration tests against real or test infrastructure |
| Next.js Route Handlers | HTTP-level tests                                      |
| Full application       | End-to-end tests using Playwright                     |

### **14.6 Test the Architecture, Not Just the Features**

You should also verify that:

- Domain files do not import Next.js.
- Domain files do not import MongoDB.
- Application Use Cases do not import Stripe.
- Client Components do not import server-only modules.
- Infrastructure implements application-owned interfaces.

For larger codebases, architectural linting or dependency-boundary tools can help enforce these restrictions.

## **15. Comparing Clean Architecture, Hexagonal Architecture, DDD and Layered Architecture**

These concepts are related, but they are not identical.

| Architecture           | Main focus                     | Main idea                            |
| ---------------------- | ------------------------------ | ------------------------------------ |
| Layered Architecture   | Separation of responsibilities | Organize code into layers            |
| Clean Architecture     | Dependency direction           | Keep business rules independent      |
| Hexagonal Architecture | External integrations          | Connect through Ports and Adapters   |
| DDD                    | Domain complexity              | Model business concepts and language |

### **15.1 Layered Architecture**

Traditional layered architecture might look like:

```
Presentation Layer
        |
        v
Business Layer
        |
        v
Data Access Layer
        |
        v
Database
```

Each layer has a defined responsibility.

A common implementation has the Business Layer depending directly on the Data Access Layer.

That can work, but it may create tight coupling.

### **15.2 Clean Architecture**

Clean Architecture emphasizes the Dependency Rule.

```
Frameworks
    |
    v
Adapters
    |
    v
Use Cases
    |
    v
Entities
```

Its goal is to keep the inner business rules independent of outer implementation details.

### **15.3 Hexagonal Architecture**

Hexagonal Architecture emphasizes ports and adapters.

```
REST Adapter
     |
     v
Application Core
     |
     +---- Database Port
     |          ^
     |          |
     |     Mongo Adapter
     |
     +---- Payment Port
                ^
                |
           Stripe Adapter
```

It focuses on how the application communicates with the outside world.

### **15.4 DDD**

DDD focuses on modeling complex business domains.

For example, an e-commerce system may have:

- Order context
- Payment context
- Inventory context
- Customer context

Each bounded context can have its own domain model and business language.

You can combine DDD with Clean Architecture or Hexagonal Architecture.

For example:

```
E-commerce Application
│
├── Orders Bounded Context
│   ├── Domain
│   ├── Application
│   ├── Ports
│   └── Adapters
│
├── Payments Bounded Context
│   ├── Domain
│   ├── Application
│   ├── Ports
│   └── Adapters
│
└── Inventory Bounded Context
    ├── Domain
    ├── Application
    ├── Ports
    └── Adapters
```

DDD helps define the business model and boundaries. Clean and Hexagonal Architecture help organize dependencies and integrations.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.aws.amazon.com\&sz=32)

AWS Prescriptive Guidance+1



## **16. Common Architectural Mistakes**

### **Mistake 1: Putting business logic inside Route Handlers**

Incorrect:

```
export async function POST(request: Request) {
  // Validation
  // Business rules
  // Database operations
  // Payment operations
  // Email operations
}
```

Better:

```
export async function POST(request: Request) {
  const input = await request.json();

  const useCase = await createPlaceOrderUseCase();

  const result = await useCase.execute(input);

  return Response.json(result);
}
```

Keep the HTTP layer focused on HTTP responsibilities.

### **Mistake 2: Making the Domain Layer depend on MongoDB**

Incorrect:

```
import { ObjectId } from "mongodb";

export class Order {
  id: ObjectId;
}
```

Better:

```
export class Order {
  constructor(
    public readonly id: string
  ) {}
}
```

Keep database-specific representations outside the Domain layer.

### **Mistake 3: Creating Interfaces for Everything**

Not every class needs an interface.

For example, creating an interface for a simple utility function that has no meaningful alternative implementation may add unnecessary complexity.

Create abstractions where they establish useful boundaries, such as:

- Database access.
- Payment providers.
- Email providers.
- External APIs.
- Infrastructure dependencies.

### **Mistake 4: Confusing Dependency Injection with Dependency Inversion**

DI is a technique. DIP is a principle.

Using constructor injection does not automatically mean your architecture follows DIP. The dependency must also point toward an appropriate abstraction.

### **Mistake 5: Making Every Layer Too Complicated**

Clean Architecture is not about creating as many folders, interfaces, and classes as possible.

It is about protecting important business logic from unnecessary external dependencies.

### **Mistake 6: Putting All Business Logic in the Repository**

A repository should handle persistence.

It should not become the place for every business rule, payment workflow, notification operation, and application decision.

### **Mistake 7: Assuming Next.js Server Components Are the Application Layer**

A Server Component is a framework-level rendering component.

It is not automatically a Use Case or a Domain Service.

Keep business workflows in application modules where they can be reused and tested independently.

### **Mistake 8: Overengineering Small Applications**

For a simple CRUD application, a full four-layer architecture for every feature may be unnecessary.

Start with clear separation of responsibilities. Introduce additional abstractions when complexity or change requirements justify them.

## **17. Interview Questions and Answers**

### **Basic Level**

**Q1. What is Clean Architecture?**

Clean Architecture is an architectural approach that separates business logic from external technologies such as frameworks, databases, and APIs.

**Q2. What is the Dependency Rule?**

The Dependency Rule states that source-code dependencies should point inward toward higher-level business policies.

**Q3. What are the four layers of Clean Architecture?**

1. Entities
2. Use Cases
3. Interface Adapters
4. Frameworks and Drivers

**Q4. What is an Entity?**

An Entity represents a business concept with identity and associated business rules.

**Q5. What is a Use Case?**

A Use Case represents an application operation, such as placing an order or registering a user.

**Q6. What is an Adapter?**

An Adapter translates communication between an external system and the application's internal interfaces or models.

**Q7. What is a Port?**

A Port is an abstraction that defines how the application communicates with an external actor or service.

### **Intermediate Level**

**Q8. What is Hexagonal Architecture?**

Hexagonal Architecture, also known as Ports and Adapters, isolates application logic from external systems through defined interfaces.

**Q9. What is Dependency Inversion Principle?**

High-level modules and low-level modules should depend on abstractions rather than directly depending on each other's implementations.

**Q10. What is Dependency Injection?**

Dependency Injection is a technique where an object receives its dependencies from outside instead of creating them internally.

**Q11. What is the difference between DIP and DI?**

DIP is a design principle; DI is a technique used to supply dependencies and help implement that principle.

**Q12. What is a Composition Root?**

A Composition Root is the location where an application's concrete dependencies are created and wired together.

**Q13. What is Inversion of Control?**

IoC is a broader principle in which control over part of the application's execution is transferred to another component or framework.

**Q14. Why should the Domain layer not depend on MongoDB?**

Because the Domain layer should represent business rules independently of persistence technology. This allows the database implementation to change without modifying core business logic.

### **Advanced Level**

**Q15. Can a Use Case depend on a repository interface?**

Yes. The repository interface should be defined in an inner layer, such as Application, and the concrete repository should implement it in Infrastructure.

**Q16. Why is the repository interface usually defined in the Application layer rather than Infrastructure?**

Because the Use Case defines what persistence operations it needs. Infrastructure supplies the implementation of that requirement.

**Q17. What is the difference between Clean Architecture and Hexagonal Architecture?**

Clean Architecture describes a broader layered organization and dependency rule. Hexagonal Architecture emphasizes communication through Ports and Adapters. Both aim to isolate business logic from infrastructure.

**Q18. Can Clean Architecture be used with Next.js?**

Yes. Next.js can serve as the outer framework layer while the business domain and application Use Cases remain independent of Next.js.

**Q19. How do you test a Use Case without a real database?**

Inject a fake or mock repository implementing the same interface as the real database repository.

**Q20. Does using Dependency Injection automatically guarantee Clean Architecture?**

No. DI helps supply dependencies, but the architecture must still respect dependency direction and separation of responsibilities.

**Q21. What happens if the payment provider changes from Stripe to Razorpay?**

Ideally, only the payment adapter and its configuration change. The Payment Gateway interface and application Use Case remain unchanged, provided the new provider can satisfy the same contract and semantics.

**Q22. Does Clean Architecture require a dependency injection framework?**

No. Constructor injection and a simple Composition Root are sufficient for many applications. A DI container is optional.

## **18. Practical Assignment**

Hands-on project

Build a small **E-commerce Order Management System** using Clean Architecture.

**Technology stack**

- Next.js 16 App Router
- TypeScript
- MongoDB
- Zod
- Vitest
- Stripe or a fake payment provider

### **Requirements**

**Task 1 – Domain Layer**

Create:

- `Order` entity.
- `OrderItem` type.
- Order status rules.
- Order total calculation.
- Order cancellation logic.

**Task 2 – Application Layer**

Create these Use Cases:

- `PlaceOrder`
- `CancelOrder`
- `GetOrderById`

Create the necessary Ports:

- `OrderRepository`
- `PaymentGateway`

**Task 3 – Infrastructure Layer**

Implement:

- `MongoOrderRepository`
- `StripePaymentAdapter`
- `FakePaymentAdapter`

**Task 4 – Interface Layer**

Create:

- Order controller.
- Order response presenter.
- Input validation with Zod.

**Task 5 – Next.js Integration**

Create these endpoints:

| Method | Endpoint                  | Purpose      |
| ------ | ------------------------- | ------------ |
| POST   | `/api/orders`             | Create order |
| GET    | `/api/orders/[id]`        | Get order    |
| PATCH  | `/api/orders/[id]/cancel` | Cancel order |

**Task 6 – Testing**

Write tests for:

- Order total calculation.
- Invalid order creation.
- Order cancellation.
- Place Order Use Case.
- Payment failure handling.
- Repository behavior.

### **Completion Checklist**

**Assignment progress**

# **0%**

0 of 10 completed

Create the Order Entity and business rules

Define repository and payment interfaces

Implement the PlaceOrder Use Case

Implement the MongoDB repository

Implement a payment adapter

Create the Composition Root

Connect a Next.js Route Handler

Write unit tests using fake dependencies

Verify Domain and Application do not import infrastructure

Document the architecture and dependency direction

## **19. Quick Revision Notes**

| Concept                | Remember                                        |
| ---------------------- | ----------------------------------------------- |
| Clean Architecture     | Business rules independent of technical details |
| Entities               | Core business rules and identity                |
| Use Cases              | Application-specific workflows                  |
| Interface Adapters     | Convert external and internal data              |
| Frameworks and Drivers | Database, frameworks, APIs, and tools           |
| Dependency Rule        | Dependencies point inward                       |
| Hexagonal Architecture | Ports and Adapters                              |
| Port                   | Defines a communication contract                |
| Adapter                | Implements or uses a Port                       |
| DIP                    | Depend on abstractions, not implementations     |
| DI                     | Supply dependencies from outside                |
| IoC                    | Transfer control to another component           |
| Composition Root       | Creates and wires dependencies                  |
| Constructor Injection  | Supply dependencies through constructor         |
| Fake Repository        | Test without a real database                    |

### **The Five Most Important Things to Remember**

1. **Business logic should not depend on frameworks or databases.**
2. **Use Cases should depend on interfaces, not concrete infrastructure implementations.**
3. **Infrastructure implements interfaces defined by inner layers.**
4. **Dependency Injection makes implementations replaceable and testing easier.**
5. **Clean Architecture is about controlling dependencies, not creating more folders.**

## **20. Phase 28 Completion Checklist**

**Your learning progress**

# **0/10**

I understand Clean Architecture and its four layers.

I understand the Dependency Rule.

I can distinguish Entities from Use Cases.

I understand Ports and Adapters.

I understand Dependency Inversion Principle.

I understand Dependency Injection and its three common types.

I can explain IoC and Composition Root.

I can organize a Next.js project using Clean Architecture.

I can test Use Cases using fake dependencies.

I understand when Clean Architecture is useful.

## **21. Next Phase Preview**

Phase 29 – Advanced Backend Architecture

# **Microservices Architecture and API Gateway**

In the next phase, we will study how large applications are divided into independently deployable services.

**Topics planned:**

1. What are Microservices?
2. Monolithic vs Modular Monolith vs Microservices.
3. Microservices Architecture.
4. Service Boundaries and Bounded Contexts.
5. API Gateway Pattern.
6. Service-to-Service Communication.
7. Synchronous vs Asynchronous Communication.
8. Service Discovery.
9. Centralized Configuration.
10. Inter-service Authentication and Authorization.
11. Distributed Logging and Monitoring.
12. Microservices with Node.js and Next.js.
13. Docker and Containerization.
14. Deployment and Scaling Strategies.
15. Common Microservices Challenges.

**Phase 28 is complete.** When you're ready, say **“OK, Phase 29”** and we'll continue with detailed notes.