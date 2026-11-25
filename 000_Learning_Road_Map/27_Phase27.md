# **Phase 27 – Domain-Driven Design (DDD) and Advanced Domain Modeling**



Advanced Level

Next.js 16.x

Node.js + TypeScript + MongoDB



## **1. Learning objectives**

In the previous phase, we studied CQRS, Event Sourcing, event stores, projections, and read models.

Now we will learn how to design software around complex business requirements using **Domain-Driven Design (DDD)**.

DDD is particularly useful when an application has complicated business rules, multiple business departments, and different workflows that must work together.

By the end of this phase, you will understand:

1. What Domain-Driven Design is.
2. Understanding business domains.
3. Core, supporting, and generic subdomains.
4. Bounded Contexts.
5. Ubiquitous Language.
6. Entities and Value Objects.
7. Aggregates and Aggregate Roots.
8. Domain Services.
9. Application Services.
10. Domain Events.
11. Repository patterns in DDD.
12. Context Mapping.
13. Anti-Corruption Layer.
14. DDD using Node.js, TypeScript, and MongoDB.
15. Designing enterprise applications using DDD.

## **2. What is Domain-Driven Design (DDD)?**

Domain-Driven Design is a software design approach that focuses on understanding and modeling the business domain.

The central idea is:

**Software architecture should reflect the business concepts, rules, and processes of the organization.**

Let's understand this through an example.

Suppose you are building an e-commerce application.

A typical developer might immediately think about:

- Users collection.
- Products collection.
- Orders collection.
- Payments collection.
- API endpoints.
- React components.

These are technical implementation details.

But first, you should understand the business:

- How does a customer place an order?
- When is an order considered confirmed?
- Can a cancelled order be shipped?
- When should inventory be reserved?
- What happens if payment fails?
- Can a customer return an already delivered product?
- How are refunds calculated?

These are business-domain questions.

DDD encourages you to understand these rules before deciding how to organize your code.

[Domain-Driven Design (DDD) Explained with Real-World Examples | by Alexandre Higino | Medium](https://images.openai.com/static-rsc-4/Gq6ZckxtVmR4ZjvMLnn8mPmSl4UIaAOKSE1GyeXTA13X_xdOZhxavxO9lFpXU9OYCfv1E7C7NH7Itm39cjFCs5eH_ATGzZsXEjPCkaqfNJhLgQ2lmY2z6WU0Eo4wKF15N0S41NFyvk0dXENDx4mp9BlJ6LFrG46ycKML4sn1fk4?purpose=inline)

[medium.com](https://medium.com/%40aleehiginoo/domain-driven-design-ddd-explained-with-real-world-examples-da76a17eec14?utm_source=chatgpt.com)

### **2.1 What is a domain?**

A domain is the business area or problem space that an application is designed to address.

Examples:

| Application                | Business domain                 |
| -------------------------- | ------------------------------- |
| Banking application        | Banking and financial services  |
| E-commerce application     | Online commerce                 |
| Hospital management        | Healthcare operations           |
| Expense tracker            | Personal finance management     |
| Hotel booking application  | Hospitality and reservations    |
| Learning management system | Education and course management |
| Ride-sharing application   | Transportation and mobility     |

For example, the domain of a banking application includes:

- Accounts.
- Deposits.
- Withdrawals.
- Transfers.
- Loans.
- Interest calculations.
- Transactions.
- Fraud detection.

A domain is not simply a database or a collection of API endpoints. It represents the business problem and its rules.

### **2.2 Domain vs application**

These terms are different.

**Domain:** The business problem and its concepts.

**Application:** The software system built to solve that problem.

Example:

```
Business Domain:
Online Banking

Application:
Banking Web Application

Technical Implementation:
Next.js
Node.js
MongoDB
Redis
AWS
```

The technologies implement the domain; they do not define the domain itself.

### **2.3 Why do we need DDD?**

Imagine building a large banking application.

If developers organize the entire system only around technical concepts, they might create:

```
controllers/
models/
routes/
services/
utils/
```

This can work initially, but as business complexity increases, business rules may become scattered across controllers, services, and database queries.

DDD encourages organization around business capabilities and concepts.

For example:

```
src/
├── accounts/
├── transactions/
├── payments/
├── loans/
└── fraud-detection/
```

Each module represents a meaningful business area.

### **2.4 Benefits of DDD**

- Better understanding of business requirements.
- Clearer business rules.
- More maintainable domain logic.
- Reduced coupling between business areas.
- Better communication between developers and domain experts.
- Easier modeling of complex workflows.
- Better separation of business logic and infrastructure.

### **2.5 When is DDD useful?**

DDD is especially useful when:

- Business rules are complicated.
- Different departments use different terminology.
- The application has multiple business capabilities.
- The system has complex state transitions.
- Business workflows change frequently.
- The application needs to evolve over many years.

For a simple CRUD application, full DDD may introduce unnecessary complexity.

## **3. Understanding subdomains**

A large business domain can be divided into smaller areas called subdomains.

Suppose you are building an e-commerce platform.

Its overall domain is online commerce.

However, it contains several subdomains:

```
E-commerce Domain
       |
       +------------------+
       |                  |
       v                  v
   Product Management  Order Management
       |
       +------------------+
       |                  |
       v                  v
   Inventory           Payments
       |
       +------------------+
       |                  |
       v                  v
   Shipping          Customer Support
```

DDD commonly classifies subdomains into three categories.

### **3.1 Core subdomain**

A Core Subdomain represents the primary business capability that differentiates the organization.

It is often where the business creates its competitive advantage.

Example: An online fashion marketplace might differentiate itself through its product recommendation and personalization engine.

Other examples:

- A ride-sharing company's driver-matching algorithm.
- A bank's fraud detection system.
- A logistics company's route optimization system.
- A trading platform's pricing engine.

Core subdomains usually deserve the greatest investment in domain modeling and engineering.

### **3.2 Supporting subdomain**

A Supporting Subdomain is necessary for the business but does not usually provide its primary competitive advantage.

Examples:

- Internal employee management.
- Basic order administration.
- Customer support workflows.
- Supplier management.

These capabilities matter, but they may not differentiate the company.

### **3.3 Generic subdomain**

A Generic Subdomain provides common functionality that many businesses need.

Examples:

- Authentication.
- Email delivery.
- Basic reporting.
- Standard document generation.
- Common access-control mechanisms.

You may implement these capabilities using existing libraries or third-party services instead of developing everything from scratch.

### **3.4 Comparison**

| Feature             | Core                   | Supporting                   | Generic                        |
| ------------------- | ---------------------- | ---------------------------- | ------------------------------ |
| Purpose             | Competitive advantage  | Supports business operations | Common functionality           |
| Business uniqueness | Usually high           | Often moderate               | Usually low                    |
| Investment priority | Highest                | Requirement-dependent        | Often reuse existing solutions |
| Example             | Fraud detection engine | Supplier management          | Authentication                 |

### **3.5 Real-world example: Food delivery application**

Imagine a platform similar to a food delivery service.

| Subdomain                 | Classification | Reason                             |
| ------------------------- | -------------- | ---------------------------------- |
| Restaurant recommendation | Core           | Can differentiate the platform     |
| Delivery partner matching | Core           | Can create a competitive advantage |
| Restaurant management     | Supporting     | Required for operations            |
| Customer support          | Supporting     | Supports service delivery          |
| Email delivery            | Generic        | Common technical capability        |
| Authentication            | Generic        | Standard functionality             |

**Important:** Subdomain classification depends on the business strategy. Authentication could be a highly specialized core capability for an identity company, even though it is generic for many other businesses.

## **4. Bounded Contexts**

This is one of the most important concepts in Domain-Driven Design.

### **4.1 What is a Bounded Context?**

A Bounded Context is an explicit boundary within which a particular domain model and its terminology have a specific, consistent meaning.

Consider an e-commerce application.

Different departments work with the same business concepts, but they may understand them differently.

For example, consider the word **Product**.

In the Catalog department:

```
type Product = {
  id: string;
  name: string;
  description: string;
  images: string[];
  price: number;
};
```

In the Inventory department:

```
type Product = {
  id: string;
  sku: string;
  warehouseId: string;
  availableQuantity: number;
  reservedQuantity: number;
};
```

In the Shipping department:

```
type Product = {
  id: string;
  weight: number;
  dimensions: {
    length: number;
    width: number;
    height: number;
  };
};
```

All three represent a product, but each context needs different information.

Should we create one enormous Product model containing every possible property?

Usually, no.

Each context should have its own model containing the information and behavior relevant to its responsibilities.

[Domain-Driven Design (DDD) Demystified](https://images.openai.com/static-rsc-4/puXph2wmrLPhb93c3YsLWwKOzifGzEN1-4Bvan_CYUmxQzKxHblaoQ3oZO8XksHRU1vyxCiCOgB9z1Q0nDIz7iSEoo76l9CSd3_AEeZ8S0ee0hcPuuMqP5G9_LYNa2KD9DGfhFlfu5pkSWtGT3wIQ6RoO5a-beWO1UPcRlOXl4Y?purpose=inline)

[blog.bytebytego.com](https://blog.bytebytego.com/p/domain-driven-design-ddd-demystified?utm_source=chatgpt.com)

### **4.2 Example: E-commerce bounded contexts**

```
              E-commerce Domain
                      |
       +--------------+--------------+
       |              |              |
       v              v              v
    Catalog        Ordering       Inventory
    Context        Context        Context
       |              |              |
    Product        Order          StockItem
    Category       OrderItem      Reservation
    Pricing        Checkout       Warehouse
       |
       +--------------+--------------+
                      |
              +-------+-------+
              |               |
              v               v
           Payment          Shipping
           Context          Context
              |               |
           Payment          Shipment
           Refund            Tracking
           Transaction       Delivery
```

Each bounded context has its own:

- Domain model.
- Business rules.
- Terminology.
- Entities.
- Value objects.
- Aggregates.
- Application services.

### **4.3 Why is a bounded context important?**

Without clear boundaries, the same model can become overloaded with responsibilities.

For example:

```
type Product = {
  id: string;

  // Catalog
  name: string;
  description: string;
  images: string[];

  // Inventory
  availableQuantity: number;
  reservedQuantity: number;

  // Shipping
  weight: number;
  dimensions: object;

  // Pricing
  discount: number;
  tax: number;
};
```

This creates several problems:

- Different teams modify the same model.
- Changes in one area affect unrelated areas.
- Business responsibilities become unclear.
- Validation rules become complicated.
- Modules become tightly coupled.

Bounded Contexts reduce these problems by defining clear model boundaries.

### **4.4 Can the same entity exist in multiple bounded contexts?**

Yes.

A customer can be represented differently in different contexts.

| Context   | Customer representation              |
| --------- | ------------------------------------ |
| Identity  | User ID, email, password credentials |
| Ordering  | Buyer ID, delivery information       |
| Marketing | Preferences, campaigns, engagement   |
| Support   | Customer ID, tickets, interactions   |

The identity may be shared, but the models and behaviors do not have to be identical.

**Important:** A bounded context is a model boundary, not necessarily a separate microservice or database. One bounded context may be implemented inside a modular monolith, or it may consist of several cooperating physical services.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://martinfowler.com\&sz=32)

martinfowler.com+2



## **5. Ubiquitous Language**

### **5.1 What is Ubiquitous Language?**

Ubiquitous Language is a shared vocabulary used by developers, business stakeholders, domain experts, and other team members within a bounded context.

The same business concepts should have consistent names in:

- Business discussions.
- Requirements.
- Documentation.
- Domain models.
- Code.
- Tests.

Suppose an e-commerce team uses these terms:

- Order.
- Checkout.
- Payment.
- Refund.
- Shipment.
- Delivery.

These terms should have precise meanings.

### **5.2 Why is it important?**

Imagine a developer calls an order "completed" immediately after payment.

But the business team defines a completed order as one that has been delivered.

This difference can cause serious bugs.

Instead, define the terms explicitly:

| Term              | Business meaning                  |
| ----------------- | --------------------------------- |
| Order Created     | Customer submitted an order       |
| Payment Completed | Payment was successfully captured |
| Order Confirmed   | Business accepted the order       |
| Order Shipped     | Package was handed to the carrier |
| Order Delivered   | Customer received the package     |

Now developers and business stakeholders share the same vocabulary.

### **5.3 Ubiquitous Language in code**

Poor naming:

```
function updateStatus(data: any) {
  // What status?
  // What operation?
  // What business meaning?
}
```

Better naming:

```
function confirmOrder(orderId: string) {
  // Explicit business operation
}
```

Another example:

```
class Order {
  confirm() {
    // Business rules for order confirmation
  }

  cancel(reason: string) {
    // Business rules for cancellation
  }

  markAsShipped(trackingId: string) {
    // Business rules for shipping
  }
}
```

### **5.4 Different contexts can have different languages**

The word "Account" can mean:

- A login account in Identity.
- A financial account in Banking.
- A customer account in CRM.
- A ledger account in Accounting.

The same word does not necessarily represent the same domain concept.

DDD allows each bounded context to define its own language.

[Microservice Architecture — Decomposition Patterns | CodeNx](https://images.openai.com/static-rsc-4/3NefpGxCB0KVEnxpHVR4aNxaNFpaCmSNKGnHGga-PdRivB3WOtGcADgsEeiguQH_xP-yXtLluXMLm0sUM_yOUMpvZiYDwZEiHksnwEjs9U40Iwhjfdwe4efA5VcFNDqLwo0BHaaEGlQz5J0_eF4m0rh7MoeOs2NxwUxSdsmqmtU?purpose=inline)

[medium.com](https://medium.com/codenx/microservice-architecture-decomposition-patterns-part-2-bee6d2da386b?utm_source=chatgpt.com)

**Remember:** Good domain modeling begins with understanding what business terms actually mean, not simply translating database column names into classes.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **6. Entities in DDD**

### **6.1 What is an Entity?**

An Entity is a domain object that has a unique identity and continues to exist as the same conceptual object even when its attributes change.

Example: A customer.

```
class Customer {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string
  ) {}
}
```

Suppose we have:

```
const customer = new Customer(
  "USER-101",
  "Ahmed",
  "ahmed@example.com"
);
```

Later, the customer changes their name:

```
customer.name = "Ahmed Khan";
```

It is still the same customer because its identity remains `USER-101`.

### **6.2 Entity identity vs attributes**

Consider two customers:

```
Customer A
ID: USER-101
Name: Ahmed
Email: ahmed@example.com

Customer B
ID: USER-102
Name: Ahmed
Email: ahmed@example.com
```

They have identical names and email values in this illustration, but they are separate entities because their identities differ.

An entity is primarily distinguished by its identity, not just its attributes.

### **6.3 Examples of entities**

| Entity       | Identity    |
| ------------ | ----------- |
| User         | User ID     |
| Order        | Order ID    |
| Product      | Product ID  |
| Employee     | Employee ID |
| Bank Account | Account ID  |
| Shipment     | Shipment ID |
| Booking      | Booking ID  |

### **6.4 Entity with business behavior**

In DDD, entities should often contain the business behavior that belongs to them.

Example:

```
class Order {
  constructor(
    public readonly id: string,
    private status: "pending" | "confirmed" | "cancelled"
  ) {}

  confirm() {
    if (this.status === "cancelled") {
      throw new Error("Cancelled orders cannot be confirmed");
    }

    this.status = "confirmed";
  }

  cancel() {
    if (this.status === "confirmed") {
      throw new Error("This order requires a cancellation workflow");
    }

    this.status = "cancelled";
  }

  getStatus() {
    return this.status;
  }
}
```

The entity protects its own business rules.

### **6.5 Important properties of entities**

- Have a unique identity.
- Have a lifecycle.
- Can change attributes.
- Often contain domain behavior.
- Are compared primarily by identity.

Microsoft's domain-model guidance describes entities as objects whose identity and continuity matter independently of their current attributes.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **7. Value Objects**

### **7.1 What is a Value Object?**

A Value Object represents a descriptive concept in the domain that is defined by its values rather than by a unique identity.

Examples:

- Money.
- Address.
- Email address.
- Date range.
- Product dimensions.
- Percentage.
- Coordinates.

Consider money:

```
{
  amount: 1000,
  currency: "INR"
}
```

A Money object represents an amount in a particular currency.

Two Money objects with the same amount and currency are considered equivalent by value.

### **7.2 Entity vs Value Object**

**Entity**

## **Identity**

Defined by who or what it is.

Examples:

- User
- Order
- Product

**Value Object**

## **Values**

Defined by what it contains.

Examples:

- Money
- Address
- Dimensions

### **7.3 Example: Entity**

```
class User {
  constructor(
    public readonly id: string,
    public name: string
  ) {}
}
```

Two users with the same name are not necessarily the same user.

### **7.4 Example: Value Object**

```
class Money {
  constructor(
    public readonly amount: number,
    public readonly currency: string
  ) {
    if (!Number.isFinite(amount) || amount < 0) {
      throw new Error("Invalid money amount");
    }

    if (!currency.trim()) {
      throw new Error("Currency is required");
    }

    Object.freeze(this);
  }

  equals(other: Money) {
    return (
      this.amount === other.amount &&
      this.currency === other.currency
    );
  }

  add(other: Money): Money {
    if (this.currency !== other.currency) {
      throw new Error("Currency mismatch");
    }

    return new Money(
      this.amount + other.amount,
      this.currency
    );
  }
}
```

Usage:

```
const price = new Money(1000, "INR");
const tax = new Money(180, "INR");

const total = price.add(tax);

console.log(total.amount); // 1180
```

The Money object:

- Validates its values.
- Prevents currency mismatches.
- Provides meaningful domain operations.
- Returns a new object instead of modifying itself.

### **7.5 Value Objects should generally be immutable**

Immutable means that an object cannot be changed after creation.

For example:

```
const address = {
  city: "Ahmedabad",
  state: "Gujarat",
};
```

Instead of changing an existing Address object, create a replacement:

```
const updatedAddress = {
  ...address,
  city: "Surat",
};
```

This makes the behavior easier to reason about.

### **7.6 Example: Address Value Object**

```
class Address {
  constructor(
    public readonly street: string,
    public readonly city: string,
    public readonly state: string,
    public readonly postalCode: string,
    public readonly country: string
  ) {
    if (!street.trim() || !city.trim()) {
      throw new Error("Invalid address");
    }

    Object.freeze(this);
  }

  equals(other: Address) {
    return (
      this.street === other.street &&
      this.city === other.city &&
      this.state === other.state &&
      this.postalCode === other.postalCode &&
      this.country === other.country
    );
  }
}
```

### **7.7 Entity vs Value Object comparison**

| Feature    | Entity                | Value Object                |
| ---------- | --------------------- | --------------------------- |
| Identity   | Has unique identity   | No independent identity     |
| Equality   | Usually by identity   | By values                   |
| Mutability | Can change            | Generally immutable         |
| Lifecycle  | Has its own lifecycle | Usually replaced as a whole |
| Example    | Customer              | Address                     |
| Example    | Order                 | Money                       |
| Example    | Product               | Dimensions                  |

### **7.8 Can an Address be an Entity?**

Yes.

Classification depends on the business context.

In an e-commerce application, an address may simply be a value describing where an order should be delivered.

In a utility company, a service address may have its own identity, history, inspections, and service status. In that domain, it may be an entity.

The important question is not "What is an address technically?"

It is "Does the business need to track this concept independently over time?"

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1

## **8. Aggregates**

### **8.1 What is an Aggregate?**

An Aggregate is a group of related domain objects that must maintain certain business consistency rules together.

It forms a consistency boundary.

Example: An Order aggregate may contain:

- Order.
- Order Items.
- Shipping Address.
- Pricing information.

[domain driven design - Why is the product of the order item a reference to the ID of the product and not a value object? - Software Engineering Stack Exchange](https://images.openai.com/static-rsc-4/BfeVgBHxA0dTNgebCcSKBBUtde86jWiOoZcW3ZVg9H36opa1uyjwkpUlMIeiGDqHwCrS9E4ucWSUXayyuk27QW6d-I3kjyrOzYj79dD5jsKPLWkyOQpqmGnIvUBPXw3NwRGqoEyryQC1mLIZOJ34SW9pxkL50GUV0SyY0yQL32Y?purpose=inline)

[softwareengineering.stackexchange.com](https://softwareengineering.stackexchange.com/questions/441341/why-is-the-product-of-the-order-item-a-reference-to-the-id-of-the-product-and-no?utm_source=chatgpt.com)

```
              Order Aggregate
                     |
                     v
                Order Root
                     |
           +---------+---------+
           |         |         |
           v         v         v
       OrderItem  OrderItem  Address
        Entity     Entity    Value Object
```

### **8.2 Why do we need aggregates?**

Suppose an order contains three items.

The business rules require:

- An order must have at least one item before confirmation.
- Item quantities must be positive.
- A cancelled order cannot be confirmed.
- The total must match the order items and applicable charges.

If different parts of the application modify the order and its items independently, the data can become inconsistent.

An aggregate gives these rules a defined boundary.

### **8.3 Aggregate Root**

The Aggregate Root is the main entity through which the outside application accesses and modifies the aggregate.

For an Order aggregate:

```
Order (Aggregate Root)
   |
   +---- OrderItem
   |
   +---- OrderItem
   |
   +---- ShippingAddress
```

Other application components should perform modifications through the Order root rather than directly modifying its child objects.

### **8.4 Example: Order Aggregate**

```
type OrderItem = {
  productId: string;
  quantity: number;
  unitPrice: number;
};

class Order {
  private items: OrderItem[] = [];

  private status: "draft" | "confirmed" | "cancelled" = "draft";

  constructor(
    public readonly id: string,
    public readonly userId: string
  ) {}

  addItem(item: OrderItem) {
    if (this.status !== "draft") {
      throw new Error("Cannot modify a non-draft order");
    }

    if (item.quantity <= 0) {
      throw new Error("Quantity must be positive");
    }

    if (item.unitPrice < 0) {
      throw new Error("Price cannot be negative");
    }

    this.items.push(item);
  }

  confirm() {
    if (this.items.length === 0) {
      throw new Error("Order must contain at least one item");
    }

    if (this.status !== "draft") {
      throw new Error("Only draft orders can be confirmed");
    }

    this.status = "confirmed";
  }

  getTotal() {
    return this.items.reduce(
      (sum, item) =>
        sum + item.quantity * item.unitPrice,
      0
    );
  }

  getStatus() {
    return this.status;
  }

  getItems() {
    return this.items.map((item) => ({ ...item }));
  }
}
```

Usage:

```
const order = new Order(
  "ORD-1001",
  "USER-101"
);

order.addItem({
  productId: "PROD-501",
  quantity: 2,
  unitPrice: 1000,
});

order.addItem({
  productId: "PROD-502",
  quantity: 1,
  unitPrice: 500,
});

order.confirm();

console.log(order.getTotal()); // 2500
```

The application interacts with the aggregate through its public methods.

### **8.5 Aggregate boundaries**

A common DDD mistake is placing too many entities inside one aggregate.

For example:

```
E-commerce Aggregate
    |
    +---- User
    |
    +---- Product
    |
    +---- Order
    |
    +---- Payment
    |
    +---- Shipment
    |
    +---- Inventory
```

This is usually a poor aggregate design.

Why?

- Too many responsibilities.
- Excessive transactional coupling.
- Difficult concurrency management.
- Unnecessary loading of unrelated data.
- Difficult scaling.

Instead, define separate aggregates:

```
User Aggregate
Product Aggregate
Order Aggregate
Payment Aggregate
Shipment Aggregate
Inventory Aggregate
```

Each aggregate protects its own consistency rules.

### **8.6 Should aggregates directly reference other aggregates?**

Generally, use the identifier of another aggregate rather than directly embedding its entire domain object.

Example:

```
class Order {
  constructor(
    public readonly id: string,
    public readonly customerId: string
  ) {}
}
```

The Order aggregate stores `customerId`, not the entire Customer aggregate.

This keeps aggregate boundaries independent.

### **8.7 How do you identify an aggregate?**

Ask:

1. Which objects must remain consistent together?
2. Which business rules must be enforced atomically?
3. Which operations modify these objects together?
4. Can some changes happen asynchronously?
5. Is the aggregate becoming too large?

**Important:** An aggregate is not simply a group of related database documents. It is a business consistency boundary.

Microsoft's tactical DDD guidance emphasizes defining aggregates around transactional invariants and making the aggregate root the entry point for modifications.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+2



## **9. Domain Services**

### **9.1 What is a Domain Service?**

A Domain Service contains business logic that does not naturally belong to a single Entity or Value Object.

Some business operations involve multiple domain objects or concepts.

For example, consider a money transfer between two bank accounts.

The operation involves:

- Source account.
- Destination account.
- Transfer amount.
- Transfer rules.

Should the entire transfer operation belong to the source account?

Not necessarily. It is a domain operation involving multiple entities.

A Domain Service can represent this business operation.

### **9.2 Example: Bank transfer**

```
class BankTransferService {
  transfer(
    from: BankAccount,
    to: BankAccount,
    amount: Money
  ) {
    if (from.id === to.id) {
      throw new Error("Cannot transfer to the same account");
    }

    from.withdraw(amount);

    to.deposit(amount);
  }
}
```

The service coordinates the business operation.

The individual accounts still protect their own rules.

### **9.3 Another example: Discount calculation**

Suppose an e-commerce platform calculates a discount based on:

- Customer membership.
- Order amount.
- Promotional campaign.
- Product category.

This logic may not belong to a single Product or Customer entity.

A Domain Service can represent it:

```
class DiscountService {
  calculateDiscount(
    orderTotal: number,
    membershipLevel: "basic" | "gold" | "platinum"
  ): number {
    if (membershipLevel === "platinum") {
      return orderTotal * 0.15;
    }

    if (membershipLevel === "gold") {
      return orderTotal * 0.10;
    }

    return orderTotal * 0.02;
  }
}
```

Usage:

```
const discountService = new DiscountService();

const discount = discountService.calculateDiscount(
  10000,
  "gold"
);

console.log(discount); // 1000
```

This is an illustrative business rule. In a real system, the applicable discount policy should come from the actual business requirements.

### **9.4 Characteristics of Domain Services**

- Contain domain-specific business logic.
- Do not primarily handle HTTP requests.
- Do not primarily manage database connections.
- Often coordinate multiple domain objects.
- Should be expressed using business terminology.

**Important:** Do not put every business operation into a Domain Service. If the behavior naturally belongs to an entity or value object, keep it there.

## **10. Application Services**

### **10.1 What is an Application Service?**

An Application Service coordinates a business use case.

It orchestrates the necessary steps but should generally not own the core business rules.

For example, when a customer places an order, an application service might:

1. Load the relevant domain object.
2. Call domain methods.
3. Save the updated aggregate.
4. Publish domain events through the appropriate infrastructure mechanism.
5. Return the result.

[Domain-Driven Design - Part 2 - Tactical Design - DEV Community](https://images.openai.com/static-rsc-4/XYrmWP8F9cWROHdhaRRXBCxmJULjQcMN4eCXxf42Wkkma8wWVlKMUrBfU-KIU56AI-uRrQHN2Gx1jriSbnF3vO46QNTA0p4Psc_H4_vDxlEXqQwAgiAk16QV3lBrnfcQmgLtAxyzz0UmwhRWK18kBXOz8W31ANbtJvIWzv0GXt4?purpose=inline)

[dev.to](https://dev.to/axeldlv/domain-driven-design-part-2-tactical-design-243n?utm_source=chatgpt.com)

### **10.2 Example: Create order**

```
class CreateOrderService {
  constructor(
    private readonly orderRepository: OrderRepository
  ) {}

  async execute(input: {
    userId: string;
    items: {
      productId: string;
      quantity: number;
      unitPrice: number;
    }[];
  }) {
    const order = new Order(
      crypto.randomUUID(),
      input.userId
    );

    for (const item of input.items) {
      order.addItem(item);
    }

    order.confirm();

    await this.orderRepository.save(order);

    return {
      orderId: order.id,
      total: order.getTotal(),
      status: order.getStatus(),
    };
  }
}
```

The Application Service coordinates the use case.

The Order aggregate enforces the business rules.

### **10.3 Domain Service vs Application Service**

This distinction is important in DDD interviews.

| Feature                     | Domain Service         | Application Service        |
| --------------------------- | ---------------------- | -------------------------- |
| Main responsibility         | Business rules         | Use-case orchestration     |
| Contains business decisions | Yes                    | Normally delegates them    |
| Coordinates repositories    | Usually not directly   | Often yes                  |
| Handles workflows           | Domain-level operation | Application-level workflow |
| Example                     | Discount calculation   | Place order                |
| Example                     | Bank transfer rules    | Execute transfer use case  |

### **10.4 A simple analogy**

Imagine a restaurant.

- Domain Service: Determines whether a discount applies to a customer's order.
- Application Service: Coordinates the process of placing the order.
- Repository: Saves and retrieves the order.
- Controller: Receives the HTTP request.

Each component has a separate responsibility.

## **11. Domain Events**

### **11.1 What is a Domain Event?**

A Domain Event represents something important that has happened in the business domain.

Examples:

- `OrderPlaced`
- `PaymentReceived`
- `OrderCancelled`
- `InventoryReserved`
- `CustomerRegistered`
- `ShipmentDelivered`

A Domain Event is a statement about a completed business occurrence.

Example:

```
type OrderPlaced = {
  type: "OrderPlaced";
  orderId: string;
  customerId: string;
  total: number;
  occurredAt: Date;
};
```

### **11.2 Why do we need Domain Events?**

Suppose a customer places an order.

Several business activities may need to happen:

- Inventory needs to be reserved.
- Payment needs to be processed.
- An email needs to be sent.
- Analytics needs to be updated.

Without domain events, the Order module might directly depend on all these systems.

With domain events:

```
Order Aggregate
      |
      v
   OrderPlaced
      |
      v
  Event Dispatcher
      |
      +------> Inventory Handler
      |
      +------> Payment Handler
      |
      +------> Notification Handler
```

This reduces direct coupling between business components.

### **11.3 Domain Event vs Integration Event**

These terms are related but have different purposes.

**Domain Event**

Represents something that happened inside a particular bounded context.

Example:

```
{
  type: "OrderPlaced",
  orderId: "ORD-1001"
}
```

**Integration Event**

A message published for other bounded contexts or external services to consume.

Example:

```
{
  "eventId": "EVT-1001",
  "type": "OrderPlaced",
  "version": 1,
  "source": "ordering",
  "data": {
    "orderId": "ORD-1001",
    "customerId": "USER-101"
  }
}
```

An integration event should have an explicit contract suitable for external consumers.

A domain event does not necessarily need to be published outside its own context.

### **11.4 Domain Event vs Command**

| Feature | Command                     | Domain Event                  |
| ------- | --------------------------- | ----------------------------- |
| Meaning | Request to do something     | Something happened            |
| Example | `PlaceOrder`                | `OrderPlaced`                 |
| Timing  | Before successful execution | After the business occurrence |
| Intent  | Expresses an action         | Records a fact                |

### **11.5 Example: Domain event inside an Order**

```
type OrderEvent = {
  type: "OrderPlaced";
  orderId: string;
  total: number;
};

class Order {
  private events: OrderEvent[] = [];

  constructor(
    public readonly id: string,
    private readonly total: number
  ) {}

  place() {
    if (this.total <= 0) {
      throw new Error("Invalid order total");
    }

    this.events.push({
      type: "OrderPlaced",
      orderId: this.id,
      total: this.total,
    });
  }

  pullEvents() {
    const events = [...this.events];

    this.events = [];

    return events;
  }
}
```

Usage:

```
const order = new Order("ORD-1001", 2500);

order.place();

const events = order.pullEvents();

console.log(events);
```

The aggregate records the event when the business operation succeeds.

### **11.6 Important distinction**

A Domain Event is a domain concept.

An Integration Event is a communication contract.

A Message Broker is infrastructure used to deliver messages.

Do not confuse these three.

## **12. Repository Pattern in DDD**

### **12.1 What is a Repository?**

A Repository provides an abstraction for retrieving and persisting domain objects.

It allows domain and application code to work with domain objects without depending directly on database implementation details.

For example:

```
interface OrderRepository {
  findById(id: string): Promise<Order | null>;

  save(order: Order): Promise<void>;

  delete(id: string): Promise<void>;
}
```

The interface describes what the application needs.

It does not specify whether the implementation uses:

- MongoDB.
- PostgreSQL.
- MySQL.
- Another persistence mechanism.

### **12.2 Why use Repository Pattern?**

Without a repository:

```
class CreateOrderService {
  async execute() {
    const db = await connectToMongoDB();

    await db.collection("orders").insertOne({
      // order data
    });
  }
}
```

The application service is tightly coupled to MongoDB.

With a repository:

```
class CreateOrderService {
  constructor(
    private readonly orderRepository: OrderRepository
  ) {}

  async execute(order: Order) {
    await this.orderRepository.save(order);
  }
}
```

Now the application service depends on an abstraction.

### **12.3 MongoDB repository implementation**

```
import "server-only";

import { clientPromise } from "@/lib/mongodb";

class MongoOrderRepository implements OrderRepository {
  async findById(id: string): Promise<Order | null> {
    const client = await clientPromise;

    const document = await client
      .db("ecommerce")
      .collection("orders")
      .findOne({
        id,
      });

    if (!document) {
      return null;
    }

    // Map persistence document to domain object.
    return Order.restore(document);
  }

  async save(order: Order): Promise<void> {
    const client = await clientPromise;

    await client
      .db("ecommerce")
      .collection("orders")
      .updateOne(
        { id: order.id },
        {
          $set: order.toPersistence(),
        },
        { upsert: true }
      );
  }

  async delete(id: string): Promise<void> {
    const client = await clientPromise;

    await client
      .db("ecommerce")
      .collection("orders")
      .deleteOne({ id });
  }
}
```

The `restore()` and `toPersistence()` methods are illustrative domain mapping methods. They must be implemented by the Order class.

### **12.4 Repository vs DAO**

DAO stands for Data Access Object.

Both patterns abstract data access, but their emphasis differs.

| Feature         | Repository                     | DAO                             |
| --------------- | ------------------------------ | ------------------------------- |
| Main focus      | Domain objects and collections | Data persistence operations     |
| Abstraction     | Domain-oriented                | Database-oriented               |
| Typical methods | `save(order)`, `findById(id)`  | `insertOne()`, `executeQuery()` |
| Common use      | DDD                            | General data access             |

In DDD, repositories are typically defined around aggregate roots.

For example:

```
OrderRepository
CustomerRepository
ProductRepository
```

You generally do not need a separate repository for every child entity inside an aggregate.

Microsoft's DDD guidance describes repositories as abstractions that allow application code to work with domain objects without being tied to a particular persistence technology.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **13. Context Mapping**

### **13.1 What is Context Mapping?**

Context Mapping describes the relationships and integration patterns between different bounded contexts.

Suppose an e-commerce system has:

- Ordering Context.
- Inventory Context.
- Payment Context.
- Shipping Context.
- Customer Context.

These contexts need to communicate.

Context Mapping helps you understand:

- Which context depends on another.
- Which team owns each model.
- How information is exchanged.
- Whether one model influences another.
- Where translation is needed.

[Coupling and Cohesion: The Two Principles for Effective Architecture](https://images.openai.com/static-rsc-4/WZPQ61_N3S-6I5I9t13jWYV4g-htt5ZS3mpwcUadeIXj967nguRLMNbA3t9OJyXjZoYosBebmX_EXztzx1QSUqhccjgeDf-VogUWc4-9MQaAyuxdWQLuzmC_rg4FAbSq2alWH_Ynw4FbMylRWdSjFk3sttEXMgXyyswzp-OK3wQ?purpose=inline)

[blog.bytebytego.com](https://blog.bytebytego.com/p/coupling-and-cohesion-the-two-principles?utm_source=chatgpt.com)

### **13.2 Example context map**

```
              Customer Context
                     |
                     v
              Ordering Context
                /         \
               v           v
       Inventory Context  Payment Context
               \           /
                v         v
                Shipping Context
```

This is a simplified conceptual relationship diagram, not a recommended direct dependency structure.

A real context map should identify ownership and integration mechanisms explicitly.

### **13.3 Common Context Mapping relationships**

DDD defines several strategic relationship patterns.

Let's understand the most important ones.

**1. Partnership**

Two teams cooperate closely and coordinate changes to their models and integrations.

Example:

- Ordering team.
- Inventory team.

Both teams agree on changes to an order-reservation workflow.

**2. Shared Kernel**

Two contexts share a carefully controlled subset of their domain model.

Example:

Two closely collaborating contexts share a small set of common business types.

Use this carefully because shared code can create coupling.

**3. Customer-Supplier**

One context provides services or data to another context.

Example:

```
Catalog Context
       |
       v
Ordering Context
```

The Ordering Context is the customer of product information provided by Catalog.

**4. Conformist**

A downstream context accepts the upstream context's model as it is rather than requiring it to change.

Example:

Your application integrates with an external payment platform and must follow its API and data contracts.

**5. Anti-Corruption Layer**

A downstream context uses a translation layer to protect its own model from an external model.

We will study this in detail next.

**6. Open Host Service**

A context exposes a well-defined protocol or service that multiple consumers can use.

Example:

A Catalog API used by ordering, search, and recommendation systems.

**7. Published Language**

Contexts communicate using a documented, shared data format or contract.

Examples:

- JSON API contracts.
- Versioned event schemas.
- Standardized message formats.

**8. Separate Ways**

Two contexts do not integrate because the cost of integration is greater than its value.

Example:

An internal analytics tool and an unrelated employee scheduling tool may not need direct integration.

**9. Big Ball of Mud**

A system with poorly defined boundaries and tangled dependencies.

This is an undesirable situation, not a recommended integration strategy.

[image](https://www.google.com/s2/favicons?domain=https://martinfowler.com\&sz=32)

martinfowler.com+1

## **14. Anti-Corruption Layer (ACL)**

### **14.1 What is an Anti-Corruption Layer?**

An Anti-Corruption Layer is a translation layer that protects your domain model from the concepts and structures of an external system.

The word "corruption" here means unwanted influence from an external model—not data corruption in the database.

Imagine your application integrates with a third-party payment provider.

The provider returns:

```
{
  "txn_ref": "TXN-1001",
  "txn_status": "CAPTURE_SUCCESS",
  "paid_amt": 2500,
  "curr": "INR"
}
```

Your application uses:

```
type Payment = {
  transactionId: string;
  status: "pending" | "completed" | "failed";
  amount: number;
  currency: string;
};
```

You do not want payment-provider terminology to spread throughout your entire domain.

Instead, create an adapter.

[A Crash Course on Domain-Driven Design](https://images.openai.com/static-rsc-4/qVqEFGe_RTFrqExuZodgtI4gVPiWw09g-pF6kImn9DYXW0bEDFlvJ9-5ZBuukeKF_lNHjoRhNlh2ZYCtXjqYcHVOxwzZiPiX-YAiLAEaBcTyPjQN1IYElbc0l1E_St4mwzrk1Fxyl38RSJZzPNXuxcrlZuasVJOgOGeyIOk79pM?purpose=inline)

[blog.bytebytego.com](https://blog.bytebytego.com/p/a-crash-course-on-domain-driven-design?utm_source=chatgpt.com)

### **14.2 Without an ACL**

```
Your Application
       |
       v
External Payment API
       |
       v
Provider-Specific JSON
       |
       v
Business Logic
```

Your business logic becomes dependent on external field names and status codes.

### **14.3 With an ACL**

```
Your Domain Model
       |
       v
Anti-Corruption Layer
       |
       v
External Payment API
```

The ACL translates the provider's representation into your application's language.

### **14.4 Example implementation**

```
type ProviderPaymentResponse = {
  txn_ref: string;
  txn_status: string;
  paid_amt: number;
  curr: string;
};

type PaymentResult = {
  transactionId: string;
  status: "pending" | "completed" | "failed";
  amount: number;
  currency: string;
};

function mapProviderPayment(
  response: ProviderPaymentResponse
): PaymentResult {
  const statusMap: Record<
    string,
    PaymentResult["status"]
  > = {
    CAPTURE_SUCCESS: "completed",
    PENDING: "pending",
    CAPTURE_FAILED: "failed",
  };

  const status = statusMap[response.txn_status];

  if (!status) {
    throw new Error("Unsupported payment status");
  }

  return {
    transactionId: response.txn_ref,
    status,
    amount: response.paid_amt,
    currency: response.curr,
  };
}
```

Usage:

```
const providerResponse = {
  txn_ref: "TXN-1001",
  txn_status: "CAPTURE_SUCCESS",
  paid_amt: 2500,
  curr: "INR",
};

const payment = mapProviderPayment(providerResponse);

console.log(payment);
```

Your domain logic now works with your own Payment model.

### **14.5 Where is ACL useful?**

- Third-party payment integrations.
- Legacy system integration.
- External ERP integration.
- CRM integrations.
- Shipping provider APIs.
- Banking APIs.
- External identity providers.

### **14.6 ACL vs Adapter Pattern**

An Adapter Pattern converts one interface into another.

An Anti-Corruption Layer is a broader architectural boundary that protects your domain model from an external model.

An ACL may use one or more adapters internally.

## **15. DDD layered architecture**

DDD often uses layered architecture to separate business logic from technical implementation.

[Implementing Domain Driven Design | ABP.IO Documentation](https://images.openai.com/static-rsc-4/yrz_hqxb7rYMSX9chnLq3lQ2Epso8rUqbMGxNF6QF3GG-VMTMRGEOT6E05NFixtD8ezB5RWpo1OQjMW6Ic1fI_Ans4fzO-8SQ9He3B2jMyFyvTDkCp3dDenGyGnCbFe9lwK3zu30SVZMxcPTD7GafupQ2elD1vxqKu_dpP9u_FY?purpose=inline)

[docs.abp.io](https://docs.abp.io/en/abp/4.0/Domain-Driven-Design-Implementation-Guide?utm_source=chatgpt.com)

A common arrangement contains four layers.

### **15.1 Presentation Layer**

Responsible for interacting with the outside world.

Examples:

- Next.js pages.
- React components.
- Route Handlers.
- Server Actions.
- HTTP controllers.

Its responsibility is to receive requests and return responses.

### **15.2 Application Layer**

Responsible for coordinating use cases.

Examples:

- Create order.
- Cancel order.
- Register user.
- Transfer money.
- Process refund.

It coordinates domain objects and infrastructure interfaces.

### **15.3 Domain Layer**

Contains the business model and business rules.

Examples:

- Entities.
- Value Objects.
- Aggregates.
- Domain Services.
- Domain Events.
- Repository interfaces.

This is the core of DDD.

It should not depend directly on Next.js, MongoDB, Express, or other infrastructure frameworks.

### **15.4 Infrastructure Layer**

Contains technical implementations.

Examples:

- MongoDB repository implementations.
- Redis cache implementation.
- RabbitMQ publishers.
- Email providers.
- Payment gateways.
- File storage.
- Logging.

### **15.5 Layer comparison**

| Layer          | Responsibility               | Example              |
| -------------- | ---------------------------- | -------------------- |
| Presentation   | Handles user interaction     | Route Handler        |
| Application    | Coordinates use cases        | CreateOrderService   |
| Domain         | Enforces business rules      | Order Aggregate      |
| Infrastructure | Implements technical details | MongoOrderRepository |

### **15.6 Dependency direction**

A useful principle is that business logic should not depend on infrastructure details.

```
Presentation
     |
     v
Application
     |
     v
Domain
     ^
     |
Infrastructure implements domain interfaces
```

For example:

The Domain Layer defines:

```
interface OrderRepository {
  save(order: Order): Promise<void>;
}
```

The Infrastructure Layer implements:

```
class MongoOrderRepository implements OrderRepository {
  async save(order: Order) {
    // MongoDB persistence
  }
}
```

The application service depends on the interface, not the MongoDB implementation.

This is related to the Dependency Inversion Principle and Hexagonal Architecture.

## **16. DDD project structure using Next.js and TypeScript**

Now let's organize a practical DDD-style application.

Imagine an e-commerce application with multiple bounded contexts.

### **16.1 Suggested folder structure**

```
src/
│
├── app/
│   ├── (store)/
│   │   ├── products/
│   │   ├── cart/
│   │   └── checkout/
│   │
│   ├── admin/
│   │   └── dashboard/
│   │
│   └── api/
│       └── orders/
│
├── modules/
│   │
│   ├── ordering/
│   │   ├── domain/
│   │   │   ├── entities/
│   │   │   ├── value-objects/
│   │   │   ├── aggregates/
│   │   │   ├── events/
│   │   │   ├── services/
│   │   │   └── repositories/
│   │   │
│   │   ├── application/
│   │   │   ├── commands/
│   │   │   ├── queries/
│   │   │   └── services/
│   │   │
│   │   ├── infrastructure/
│   │   │   ├── persistence/
│   │   │   └── messaging/
│   │   │
│   │   └── presentation/
│   │       └── dto/
│   │
│   ├── catalog/
│   │   ├── domain/
│   │   ├── application/
│   │   ├── infrastructure/
│   │   └── presentation/
│   │
│   ├── inventory/
│   │   ├── domain/
│   │   ├── application/
│   │   ├── infrastructure/
│   │   └── presentation/
│   │
│   └── payments/
│       ├── domain/
│       ├── application/
│       ├── infrastructure/
│       └── presentation/
│
└── shared/
    ├── kernel/
    ├── database/
    └── logger/
```

### **16.2 Why organize by bounded context?**

Instead of placing every entity in one global `models` folder, each business context owns its domain model.

For example:

```
modules/
├── ordering/
│   └── domain/
│       └── order.ts
│
├── inventory/
│   └── domain/
│       └── stock-item.ts
│
└── payments/
    └── domain/
        └── payment.ts
```

The three contexts can have different models and business rules.

### **16.3 Should every application use this entire structure?**

No.

For a small application, this structure can be excessive.

Start with a simpler modular structure and introduce more layers as business complexity increases.

DDD is about meaningful business boundaries, not maximizing the number of folders and classes.

## **17. Common DDD mistakes**

| Mistake                                    | Why it is problematic                                   | Better approach                                   |
| ------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------- |
| Treating DDD as folder organization        | Folders do not create domain boundaries                 | Model actual business responsibilities            |
| Creating one global domain model           | Different business contexts have different requirements | Use bounded contexts                              |
| Using entities for everything              | Not every concept needs identity                        | Prefer Value Objects when identity is unnecessary |
| Creating one enormous aggregate            | Creates excessive coupling and concurrency problems     | Keep aggregates focused                           |
| Exposing child entities directly           | Bypasses aggregate business rules                       | Modify through the Aggregate Root                 |
| Putting all business logic in services     | Can create an anemic domain model                       | Put behavior in appropriate domain objects        |
| Putting database queries in entities       | Couples domain logic to infrastructure                  | Use repositories                                  |
| Confusing Application and Domain Services  | Responsibilities become unclear                         | Separate orchestration from business rules        |
| Sharing every model between contexts       | Creates tight coupling                                  | Use context-specific models and contracts         |
| Making every module a microservice         | Adds unnecessary operational complexity                 | Start with modular boundaries                     |
| Using one shared database model everywhere | Different contexts become dependent                     | Allow each context to own its model               |
| Overusing abstractions                     | Code becomes unnecessarily complicated                  | Introduce patterns where they solve real problems |

## **18. Interview questions and answers**

### **Basic-level questions**

**Q1. What is Domain-Driven Design?**

DDD is a software design approach that focuses on modeling software around the business domain, its concepts, terminology, and rules.

**Q2. What is a domain?**

A domain is the business area or problem space that an application addresses.

**Q3. What is a subdomain?**

A subdomain is a smaller business area within a larger domain.

**Q4. What are the three types of subdomains?**

- Core.
- Supporting.
- Generic.

**Q5. What is a Bounded Context?**

A Bounded Context is a boundary within which a particular domain model and its language have a consistent meaning.

**Q6. What is Ubiquitous Language?**

It is the shared vocabulary used by developers and business experts within a bounded context.

**Q7. What is an Entity?**

An Entity is a domain object primarily identified by its unique identity and lifecycle.

**Q8. What is a Value Object?**

A Value Object is an object defined by its values rather than an independent identity. It is generally immutable.

**Q9. What is an Aggregate?**

An Aggregate is a group of domain objects that forms a business consistency boundary.

**Q10. What is an Aggregate Root?**

It is the main entity through which external components access and modify an aggregate.

### **Intermediate-level questions**

**Q11. What is the difference between an Entity and a Value Object?**

An Entity has a unique identity, while a Value Object is defined by its values.

**Q12. What is a Domain Service?**

A Domain Service contains business logic that does not naturally belong to one particular entity or value object.

**Q13. What is an Application Service?**

An Application Service coordinates a business use case by calling domain objects and other required dependencies.

**Q14. What is a Domain Event?**

A Domain Event represents an important business occurrence that has already happened.

**Q15. What is the Repository Pattern?**

It abstracts data persistence and provides access to domain objects without exposing database implementation details.

**Q16. What is Context Mapping?**

Context Mapping describes the relationships and integration patterns between bounded contexts.

**Q17. What is an Anti-Corruption Layer?**

It is a translation layer that protects one domain model from an external system's model.

**Q18. What is the difference between Domain Services and Application Services?**

Domain Services implement business rules. Application Services coordinate use cases.

**Q19. What is the difference between a Repository and a DAO?**

A Repository generally abstracts access to domain objects or aggregate roots, while a DAO focuses more directly on data-access operations.

**Q20. Does every bounded context require a separate microservice?**

No. A bounded context can be implemented as a module within a monolithic application or as one or more physical services.

### **Advanced-level questions**

**Q21. Why should an aggregate have only one root?**

The Aggregate Root provides a controlled entry point for modifications and protects the consistency rules of the aggregate.

**Q22. How do you identify aggregate boundaries?**

Identify which objects must maintain business invariants together within a transaction. Keep unrelated objects in separate aggregates.

**Q23. Why should aggregates avoid direct references to other aggregates?**

Direct object references can create tight coupling and large consistency boundaries. Referencing another aggregate by its ID generally preserves independence.

**Q24. Can the same business entity have different representations in different bounded contexts?**

Yes. Each context models only the attributes and behaviors required by its own business responsibilities.

**Q25. What is the difference between a Domain Event and an Integration Event?**

A Domain Event represents an occurrence inside a bounded context. An Integration Event is a published contract used to communicate with other contexts or external systems.

**Q26. What is an anemic domain model?**

It is a model in which entities mostly contain data while business behavior is implemented elsewhere. It can be appropriate for simple CRUD applications, but may be unsuitable for complex business domains.

**Q27. What is the relationship between DDD and microservices?**

DDD helps identify business boundaries and models. Those boundaries can inform microservice design, but DDD does not require microservices.

**Q28. What is the difference between DDD and Clean Architecture?**

DDD focuses on modeling the business domain. Clean Architecture focuses on organizing dependencies so that business logic remains independent of infrastructure and frameworks.

**Q29. Why should Value Objects be immutable?**

Immutability prevents unexpected modifications and makes domain behavior easier to reason about and test.

**Q30. How would you design a complex e-commerce system using DDD?**

I would:

1. Identify business subdomains.
2. Define bounded contexts.
3. Establish Ubiquitous Language.
4. Model entities and value objects.
5. Identify aggregate boundaries.
6. Implement domain and application services.
7. Define repository interfaces.
8. Establish integration contracts.
9. Use domain events for business occurrences.
10. Introduce CQRS or microservices only where justified.

## **19. Practical assignment – Design an E-commerce System using DDD**

Major hands-on assignment

Build a domain model for an e-commerce platform.

The platform should support:

- Customer registration.
- Product management.
- Inventory management.
- Shopping carts.
- Order creation.
- Payment processing.
- Refunds.
- Shipping.
- Customer support.

### **Part A – Domain analysis**

- Identify the main business domain.
- Identify core subdomains.
- Identify supporting subdomains.
- Identify generic subdomains.
- List important business rules.
- Identify domain experts or stakeholders for each area.

### **Part B – Bounded Contexts**

- Define the Catalog Context.
- Define the Ordering Context.
- Define the Inventory Context.
- Define the Payment Context.
- Define the Shipping Context.
- Define the Customer Support Context.
- Create a Context Map.

### **Part C – Domain modeling**

- Identify entities.
- Identify value objects.
- Define entity identities.
- Implement immutable value objects.
- Identify aggregate boundaries.
- Define aggregate roots.
- Add business behavior to domain objects.

### **Part D – Services and events**

- Create a Domain Service.
- Create an Application Service.
- Define Domain Events.
- Define Integration Events.
- Implement repository interfaces.
- Create a MongoDB repository implementation.

### **Part E – Architecture**

- Separate Presentation, Application, Domain, and Infrastructure.
- Organize modules by bounded context.
- Define dependencies between modules.
- Implement an Anti-Corruption Layer for a payment provider.
- Avoid sharing internal domain models across contexts.

### **Part F – Testing**

- Test entity business rules.
- Test Value Object validation.
- Test Aggregate Root invariants.
- Test Domain Services.
- Test Application Services.
- Test repository implementations.
- Test integration between bounded contexts.

### **Expected architecture**

```
                 E-commerce System
                         |
       +-----------------+-----------------+
       |                 |                 |
       v                 v                 v
    Catalog           Ordering          Inventory
    Context           Context           Context
       |                 |                 |
       v                 v                 v
    Product            Order            Stock
    Category          OrderItem        Reservation
       |                 |
       |                 v
       |              Payment
       |              Context
       |                 |
       +-----------------+
                         |
                         v
                     Shipping
                     Context
```

### **Design challenge**

Answer these questions before writing code:

1. Is Product an Entity in the Catalog Context? Why?
2. Is Money a Value Object?
3. Should OrderItem belong to the Order Aggregate?
4. Should Inventory be part of the Order Aggregate?
5. Should the Order Context directly modify the Payment Context's database?
6. Which operations should generate Domain Events?
7. How should the Payment Provider's response be translated?
8. Which business rules must be enforced atomically?

These questions are more important than simply creating folders and classes.

## **20. Quick revision notes**

| Concept               | Remember                                   |
| --------------------- | ------------------------------------------ |
| DDD                   | Model software around the business domain  |
| Domain                | Business problem space                     |
| Subdomain             | Smaller business area                      |
| Core Subdomain        | Competitive advantage                      |
| Supporting Subdomain  | Supports business operations               |
| Generic Subdomain     | Common business functionality              |
| Bounded Context       | Boundary of a consistent domain model      |
| Ubiquitous Language   | Shared business vocabulary                 |
| Entity                | Object defined by identity                 |
| Value Object          | Object defined by values                   |
| Aggregate             | Business consistency boundary              |
| Aggregate Root        | Entry point for aggregate modifications    |
| Domain Service        | Business logic not belonging to one entity |
| Application Service   | Coordinates use cases                      |
| Domain Event          | Business occurrence                        |
| Integration Event     | Cross-context communication contract       |
| Repository            | Domain-oriented persistence abstraction    |
| Context Mapping       | Relationships between bounded contexts     |
| Anti-Corruption Layer | Protects a domain from external models     |
| Presentation Layer    | Handles external requests                  |
| Application Layer     | Coordinates use cases                      |
| Domain Layer          | Contains business rules                    |
| Infrastructure Layer  | Implements technical dependencies          |

### **Most important distinctions**

**Entity vs Value Object**

Entity = Identity. Value Object = Values.

**Domain Service vs Application Service**

Domain Service = Business rules. Application Service = Workflow coordination.

**Aggregate vs Aggregate Root**

Aggregate = Consistency boundary. Aggregate Root = Main entity controlling that boundary.

**Domain Event vs Integration Event**

Domain Event = Internal business occurrence. Integration Event = Cross-context communication.

**DDD vs Microservices**

DDD = Domain modeling approach. Microservices = Deployment and service architecture approach.

## **21. Official documentation and learning resources**

1. [Microsoft – Domain Analysis to Model Microservices](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis?utm_source=chatgpt.com) – Strategic DDD and bounded contexts.
2. [Microsoft – Tactical DDD](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/tactical-ddd?source=recommendations\&utm_source=chatgpt.com) – Entities, Value Objects, Aggregates, and Domain Services.
3. [Microsoft – Designing a Microservice Domain Model](https://learn.microsoft.com/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/microservice-domain-model?utm_source=chatgpt.com) – Rich domain models and aggregate roots.
4. [Microsoft – DDD-Oriented Microservices](https://learn.microsoft.com/en-us/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/ddd-oriented-microservice?utm_source=chatgpt.com) – Layered domain architecture.
5. [Martin Fowler – Bounded Context](https://martinfowler.com/bliki/BoundedContext.html?utm_source=chatgpt.com) – Understanding model boundaries.
6. [Microsoft – API Design and Domain Models](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/api-design?utm_source=chatgpt.com) – Applying domain concepts to APIs.

## **22. Phase 27 completion checklist**

**Your learning progress**

# **0%**

0 of 23 completed

Understand Domain-Driven Design

Understand domains and subdomains

Understand Core, Supporting and Generic subdomains

Understand Bounded Contexts

Understand Ubiquitous Language

Understand Entities and identity

Understand Value Objects and immutability

Implement Money Value Object

Understand Aggregates

Understand Aggregate Roots

Implement an Order Aggregate

Understand Domain Services

Understand Application Services

Understand Domain Events

Understand Domain Events vs Integration Events

Understand Repository Pattern

Understand Repository vs DAO

Understand Context Mapping

Understand Context Mapping relationships

Understand Anti-Corruption Layer

Understand DDD layered architecture

Organize a Next.js project using bounded contexts

Complete the e-commerce DDD assignment

Reset checklist

## **23. Next phase preview**

[Hexagonal Architecture in Software Development | by Lubos Maxina | Jamf Engineering | Medium](https://images.openai.com/static-rsc-4/HdLhMWj0p_eMTkzKOw0qDdj2iGwnCxARzeF1e1O0ZwoT086nOciezvZrxV21iejxD1TDSrgtKR2dKa_Nomrk14GKcls3lQ-bO0Ybj2TmxfM9IhxU4ZD6gbiDjbuRr5ZIiqi7P65K8n_7xXt9_uXsB1xhKTvPjKhtt9PXzz-OAbI?purpose=inline)

**Phase 28 – Clean Architecture, Hexagonal Architecture and Dependency Injection**

Next Phase

Advanced application architecture and maintainable software design

Planned topics:

1. What is Clean Architecture?
2. The Dependency Rule.
3. Entities and Use Cases.
4. Interface Adapters.
5. Frameworks and Drivers.
6. Hexagonal Architecture.
7. Ports and Adapters.
8. Dependency Inversion Principle.
9. Dependency Injection.
10. Inversion of Control.
11. Composition Root.
12. Clean Architecture with Node.js and TypeScript.
13. Clean Architecture with Next.js.
14. Testing architecture boundaries.
15. Comparing Clean Architecture, DDD, and Layered Architecture.

**When you're ready, say: OK, Phase 28.**