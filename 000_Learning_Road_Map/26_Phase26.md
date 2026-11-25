# **Phase 26 – CQRS, Event Sourcing and Advanced Data Architecture**



Advanced Level

Next.js 16.x

Node.js + TypeScript + MongoDB



## **1. Learning objectives**

In Phase 25, we studied distributed systems, message brokers, RabbitMQ, Kafka, Saga, and the Transactional Outbox pattern.

Now we will study advanced data architecture: how applications separate data-writing operations from data-reading operations, how they preserve historical changes, and how they build read models from events.

By the end of this phase, you will understand:

1. CQRS – Command Query Responsibility Segregation.
2. Commands vs queries.
3. Read models and write models.
4. CQRS architecture.
5. Event Sourcing in depth.
6. Event stores.
7. Event replay and snapshots.
8. CQRS combined with Event Sourcing.
9. Projections and materialized views.
10. Event versioning and schema evolution.
11. Data synchronization.
12. Implementing CQRS using Node.js, TypeScript, and MongoDB.
13. When CQRS and Event Sourcing are appropriate.
14. Real-world architecture and interview questions.

## **2. What is CQRS?**

CQRS stands for **Command Query Responsibility Segregation**.

It is an architectural pattern that separates operations that change application data from operations that read application data.

To understand why this matters, consider a typical application.

A user can:

- Create an order.
- Update an order.
- Cancel an order.
- View an order.
- Search orders.
- View order statistics.

Traditionally, the same model and database structure may be used for both reading and writing.

CQRS separates these responsibilities.

[System Design Interview Cheat Sheet: The 12 API Design Patterns You Need to Know](https://images.openai.com/static-rsc-4/Ej1xrRAbaNBc_feaxADql1gkqaP06hZgJeeMV7xP2OTSGEYZ3qgdnSWYmGU-eUlVJvS9zFAZPHP2yE_FafmAAfFA7_caO14aeJ5-lIkDOXfUOYZJrBb-UYm6FOjE4nLM9-f_3NsVQyD8Gk_O5TkQDIaeqWpDbSrhqMdVJ4N_dVA?purpose=inline)

[designgurus.substack.com](https://designgurus.substack.com/p/12-essential-api-design-patterns?utm_source=chatgpt.com)

```
              Application
                   |
          +--------+--------+
          |                 |
          v                 v
       COMMAND            QUERY
          |                 |
          v                 v
      Write Model        Read Model
          |                 |
          v                 v
      Write Store        Read Store
```

- **Command side:** Responsible for changing application state.
- **Query side:** Responsible for retrieving application data.

The two sides can use different models and, when justified, different databases.

### **2.1 Why do we need CQRS?**

Suppose an e-commerce application has 100,000 customers.

Customers frequently perform read operations:

- View products.
- Search products.
- View orders.
- Check order status.
- View reviews.

Write operations happen less frequently:

- Create an order.
- Update inventory.
- Add a review.
- Cancel an order.

If the same database model handles every operation, complex reporting queries and heavy read traffic can affect write performance.

CQRS allows us to optimize these workloads separately.

### **2.2 Traditional architecture vs CQRS**

**Traditional architecture:**

```
             Application
                  |
                  v
             Product Model
                  |
                  v
               Database
```

The same model is responsible for reads and writes.

**CQRS architecture:**

```
              Application
                   |
          +--------+--------+
          |                 |
          v                 v
      Write Model       Read Model
          |                 |
          v                 v
       Write DB           Read DB
```

The write model focuses on business rules and data integrity.

The read model focuses on efficient retrieval and presentation.

### **2.3 Advantages of CQRS**

1. Read and write operations can be optimized independently.
2. Complex read queries do not need to dictate the write model.
3. Different storage technologies can be used for different workloads.
4. Read workloads can be scaled independently.
5. Read models can be tailored to specific screens or reports.
6. Business operations become easier to identify and organize.

### **2.4 Disadvantages of CQRS**

1. More architectural complexity.
2. Data synchronization becomes important.
3. Read models may become temporarily stale.
4. More code and infrastructure may be required.
5. Debugging and testing require understanding both sides.

**Important:** CQRS does not require two separate databases. You can implement separate command and query models over the same database. Separate databases are an architectural choice, not a mandatory CQRS requirement.

## **3. Commands and Queries**

The most important concept in CQRS is the difference between commands and queries.

### **3.1 What is a command?**

A command represents an instruction to change application state.

Examples:

- `CreateOrder`
- `UpdateProduct`
- `CancelOrder`
- `RegisterUser`
- `ChangePassword`
- `ReserveInventory`

A command expresses an intention.

Example:

```
{
  "type": "CreateOrder",
  "userId": "USER-101",
  "productId": "PROD-501",
  "quantity": 2
}
```

The command handler validates the request, applies business rules, and performs the required state changes.

### **3.2 What is a query?**

A query requests information without changing application state.

Examples:

- `GetOrderById`
- `GetProducts`
- `GetUserProfile`
- `GetOrderHistory`
- `GetSalesReport`

Example:

```
{
  "type": "GetOrderById",
  "orderId": "ORD-1001"
}
```

The query handler retrieves and returns data.

### **3.3 Command vs Query**

| Feature                   | Command                               | Query          |
| ------------------------- | ------------------------------------- | -------------- |
| Purpose                   | Change state                          | Retrieve data  |
| Database operation        | Usually write                         | Usually read   |
| Changes application state | Yes                                   | No             |
| Typical result            | Success, failure, or created resource | Requested data |
| Example                   | `CreateOrder`                         | `GetOrder`     |

A query should not have business side effects. For example, `GetOrderDetails` should not silently update the order status.

### **3.4 Important principle: CQS**

CQRS is related to another principle called Command Query Separation (CQS).

CQS says that an individual operation should either:

- Change state, or
- Return information.

It should not do both as its primary responsibility.

CQRS applies a similar separation at the architectural level.

**Difference:**

- CQS is a design principle for operations.
- CQRS is an architectural pattern that separates command and query responsibilities.

## **4. Understanding the CQRS architecture**

Let's build a conceptual e-commerce architecture.

[Understanding CQRS (Command Query Responsibility Segregation) | by Bhavesh sharma | Medium](https://images.openai.com/static-rsc-4/nGMN7as4bUa4c0g1JCiUF-kSxwnnsxiRR7YO3pskedcF0fSQmQItbH7wU-aFx66Zvcyu0Cgt9Qo7xuKGcx1u2fYSmwx5tHecZ3V5llcKmey8QyyLkZsJQ24rYawb6xA6IxnJhaXT7-V8IfkY0hZPzXRKW86sbhNfONkDbmyy6Ho?purpose=inline)

[medium.com](https://medium.com/%40bhaveshyasharma/understanding-cqrs-command-query-responsibility-segregation-87731b582110?utm_source=chatgpt.com)

```
                    Client
                       |
                       v
                  Next.js App
                       |
                       v
                 API / Server Actions
                       |
              +--------+--------+
              |                 |
              v                 v
         Command Side       Query Side
              |                 |
              v                 v
        Command Handler    Query Handler
              |                 |
              v                 v
        Business Rules     Read Repository
              |                 |
              v                 v
         Write Database     Read Database
```

### **4.1 Command side**

The command side handles operations that change application state.

Example:

```
CreateOrder
     |
     v
Validate Input
     |
     v
Check Business Rules
     |
     v
Save Order
     |
     v
Publish OrderCreated Event
```

### **4.2 Query side**

The query side handles operations that retrieve data.

Example:

```
GetCustomerOrders
       |
       v
Query Handler
       |
       v
Read Repository
       |
       v
Read Database
       |
       v
Return Orders
```

### **4.3 Can the read and write databases be different?**

Yes.

For example:

| Responsibility | Database                         |
| -------------- | -------------------------------- |
| Write model    | PostgreSQL                       |
| Read model     | MongoDB                          |
| Event storage  | Kafka or a dedicated event store |
| Cache          | Redis                            |

This is one possible architecture, not a requirement.

The write database can be relational because it needs strong transactional guarantees, while the read database can use a document-oriented structure optimized for a particular screen.

### **4.4 CQRS does not automatically mean microservices**

You can implement CQRS inside:

- A monolithic application.
- A modular monolith.
- A microservices architecture.

CQRS describes the separation of reads and writes. Microservices describe service deployment and ownership boundaries.

They solve different architectural problems.

## **5. Read models and write models**

A model is a representation of data designed for a particular application responsibility.

CQRS uses different models for writing and reading.

### **5.1 Write model**

The write model represents the business state and rules needed to process commands.

Example: An order might be represented as:

```
type OrderWriteModel = {
  id: string;
  userId: string;
  items: {
    productId: string;
    quantity: number;
    unitPrice: number;
  }[];
  status: "pending" | "paid" | "cancelled";
  version: number;
};
```

The write model needs information required to enforce business rules.

For example:

- Is the order already cancelled?
- Is the requested quantity valid?
- Can this order be paid?
- Can this order be shipped?

### **5.2 Read model**

The read model is optimized for retrieving information.

The product listing page might need:

```
type ProductListReadModel = {
  id: string;
  name: string;
  image: string;
  price: number;
  averageRating: number;
  reviewCount: number;
};
```

The customer order-history page might need:

```
type OrderHistoryReadModel = {
  orderId: string;
  createdAt: string;
  itemCount: number;
  total: number;
  status: string;
};
```

Notice that the read model can have a different structure from the write model.

It may contain precomputed values to avoid expensive joins or calculations.

### **5.3 Why are separate models useful?**

Suppose the database contains 10 million orders.

Your admin dashboard needs:

- Total revenue.
- Total orders.
- Average order value.
- Orders grouped by month.
- Orders grouped by status.

Calculating everything from the transactional write model for every dashboard request can become expensive.

You could maintain a dedicated dashboard read model:

```
{
  "totalOrders": 120000,
  "totalRevenue": 45000000,
  "averageOrderValue": 375,
  "ordersByStatus": {
    "pending": 1200,
    "paid": 110000,
    "cancelled": 8800
  }
}
```

The dashboard can retrieve this precomputed data efficiently.

### **5.4 DTO – Data Transfer Object**

A DTO is a data structure designed to transfer information between application boundaries.

For example:

```
type OrderSummaryDTO = {
  orderId: string;
  customerName: string;
  total: number;
  status: string;
};
```

A query handler can return this DTO without exposing the internal domain model.

This is especially useful when your database structure is more complicated than the data required by the UI.

### **5.5 Write model vs read model**

| Feature             | Write model          | Read model             |
| ------------------- | -------------------- | ---------------------- |
| Main responsibility | Change state         | Retrieve information   |
| Optimization        | Business correctness | Query performance      |
| Structure           | Domain-oriented      | Screen/report-oriented |
| Validation          | Business rules       | Usually minimal        |
| Operations          | Commands             | Queries                |
| Examples            | Order aggregate      | Order history DTO      |

Microsoft's CQRS guidance explains both shared-database and separate-database implementations, including the trade-offs of synchronization.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **6. Projections and materialized views**

### **6.1 What is a projection?**

A projection transforms a sequence of events or source records into a representation suitable for querying.

Example:

Suppose an order produces these events:

```
OrderCreated
PaymentCompleted
OrderShipped
OrderDelivered
```

A projection can transform them into:

```
{
  "orderId": "ORD-1001",
  "status": "delivered",
  "paymentStatus": "paid",
  "createdAt": "2026-11-25T10:00:00Z"
}
```

The result is easier for the frontend to retrieve.

### **6.2 How does a projection work?**

[Event Sourcing Explained with code examples in Java | by Code Wiz | Javarevisited | Medium](https://images.openai.com/static-rsc-4/1APJ7SOmq4kQvvjHXM2WVyFLS0DfXgSSALshHOD1-p3IoDPybB2DE5noVi164VlL_OXoBPN6KRS_PJI3t3Zqq6m1CGik754e2rEmxvEKt6ORE7xmN--LKqorK6AGXkiwMQEd1iGyKYf_b5ZLWZ6pHi5XGfY1MXS42-n8fsiRyts?purpose=inline)

[medium.com](https://medium.com/javarevisited/event-sourcing-explained-with-code-examples-in-java-da4a8d035c92?utm_source=chatgpt.com)

```
           Event Store
                |
                v
          Event Consumer
                |
                v
          Projection Logic
                |
                v
          Read Database
                |
                v
           Next.js UI
```

The projection reads events and updates the read model.

### **6.3 Example: Order summary projection**

Suppose these events arrive:

```
{
  "type": "OrderCreated",
  "orderId": "ORD-1001",
  "total": 2500
}
```

```
{
  "type": "PaymentCompleted",
  "orderId": "ORD-1001"
}
```

```
{
  "type": "OrderShipped",
  "orderId": "ORD-1001"
}
```

The projection updates the read model as follows:

| Event              | Read-model status |
| ------------------ | ----------------- |
| `OrderCreated`     | pending           |
| `PaymentCompleted` | paid              |
| `OrderShipped`     | shipped           |

Example:

```
type OrderProjection = {
  orderId: string;
  total: number;
  status: string;
};

function applyOrderEvent(
  current: OrderProjection | null,
  event: any
): OrderProjection {
  switch (event.type) {
    case "OrderCreated":
      return {
        orderId: event.orderId,
        total: event.total,
        status: "pending",
      };

    case "PaymentCompleted":
      if (!current) {
        throw new Error("Order projection does not exist");
      }

      return {
        ...current,
        status: "paid",
      };

    case "OrderShipped":
      if (!current) {
        throw new Error("Order projection does not exist");
      }

      return {
        ...current,
        status: "shipped",
      };

    default:
      return current!;
  }
}
```

This is a simplified illustration. Production projectors must also handle event ordering, duplicate delivery, missing events, and invalid state transitions.

### **6.4 What is a materialized view?**

A materialized view is a stored, precomputed representation of data that is optimized for queries.

For example:

```
Original Data:
Orders
Customers
Payments
Products
```

A query might otherwise require joining multiple collections or tables.

A materialized view can store:

```
{
  "orderId": "ORD-1001",
  "customerName": "Customer A",
  "productNames": ["Laptop", "Mouse"],
  "paymentStatus": "paid",
  "total": 65000
}
```

The UI can retrieve the result with a simpler query.

### **6.5 Projection vs materialized view**

| Projection                          | Materialized view                       |
| ----------------------------------- | --------------------------------------- |
| Transformation process              | Stored query-optimized result           |
| Defines how data is derived         | Stores the derived data                 |
| Can consume events                  | Can be populated by a projection        |
| Example: Apply `OrderShipped` event | Example: Current order summary document |

The projection is the logic; the materialized view is one possible output.

### **6.6 Can you have multiple projections?**

Yes.

One event stream can update several read models.

```
               Event Store
                    |
                    v
               Event Stream
                    |
          +---------+---------+
          |         |         |
          v         v         v
      Projection  Projection Projection
          A         B         C
          |         |         |
          v         v         v
       Customer  Analytics  Admin
       Read DB    Read DB   Read DB
```

For example:

- Customer order history.
- Admin order dashboard.
- Sales analytics.
- Inventory report.

Each projection serves a different query requirement.

### **6.7 Rebuilding a projection**

Suppose you add a new field to the admin dashboard.

The old read model does not contain this field.

If the original events are retained, you can replay them through a new projection and rebuild the read model.

This is one of the most useful benefits of combining CQRS with Event Sourcing.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn



## **7. What is Event Sourcing?**

### **7.1 Traditional CRUD vs Event Sourcing**

In a traditional CRUD application, you generally store the current state.

Example:

```
{
  "_id": "ORD-1001",
  "status": "shipped",
  "total": 2500
}
```

When the order status changes from `pending` to `paid`, you update the document.

```
await db.collection("orders").updateOne(
  { _id: "ORD-1001" },
  {
    $set: {
      status: "paid",
    },
  }
);
```

The previous state may no longer be available unless you separately implement audit history.

In Event Sourcing, you store the events that caused the state changes.

```
OrderCreated
PaymentCompleted
OrderShipped
```

The current state is calculated by applying these events in order.

[Event Sourcing: Storing System State as Events](https://images.openai.com/static-rsc-4/9QNgF4_cAxfMbic17voOIBd124eGVxrduxEXNX6jMj7DktHoYRed0FU7Y-k0Dcj1iw3kknG4V2LzOjdpQjaYSW60iGI9Fn9a4BDerBKamhYYOgdccMD-d7MCaoCVCytWz_Djt-3vaPVSIzJAETR3rc-jkCtnb00-_0QyepHTUJE?purpose=inline)

[systemdr.systemdrd.com](https://systemdr.systemdrd.com/p/event-sourcing-storing-system-state?utm_source=chatgpt.com)

### **7.2 What is an event store?**

An event store is a persistent storage system that stores an append-only sequence of events.

An event store should support important operations such as:

- Append events.
- Read events for an entity.
- Preserve event order.
- Check expected stream version.
- Support historical reconstruction.

Example event records:

```
[
  {
    "eventId": "EVT-001",
    "aggregateId": "ORD-1001",
    "version": 1,
    "type": "OrderCreated",
    "data": {
      "userId": "USER-101",
      "total": 2500
    }
  },
  {
    "eventId": "EVT-002",
    "aggregateId": "ORD-1001",
    "version": 2,
    "type": "PaymentCompleted",
    "data": {
      "transactionId": "TXN-1001"
    }
  },
  {
    "eventId": "EVT-003",
    "aggregateId": "ORD-1001",
    "version": 3,
    "type": "OrderShipped",
    "data": {
      "trackingId": "TRACK-1001"
    }
  }
]
```

Notice that every event has a version.

For this particular order:

- Version 1: Order created.
- Version 2: Payment completed.
- Version 3: Order shipped.

### **7.3 What is an event stream?**

An event stream is the ordered sequence of events belonging to a particular entity or aggregate.

For example:

```
Stream: ORD-1001

Version 1 -> OrderCreated
Version 2 -> PaymentCompleted
Version 3 -> OrderShipped
```

Another order has its own stream:

```
Stream: ORD-1002

Version 1 -> OrderCreated
Version 2 -> OrderCancelled
```

### **7.4 Why append-only?**

In Event Sourcing, historical events are treated as immutable.

You do not normally edit an old event to change history.

Suppose an order was incorrectly marked as shipped.

Instead of deleting the original event, you can append a corrective event such as:

```
{
  "type": "ShipmentStatusCorrected",
  "orderId": "ORD-1001",
  "reason": "Shipping integration reported an incorrect status"
}
```

The original event remains part of the audit trail.

### **7.5 What is rehydration?**

Rehydration is the process of reconstructing an entity's current state by replaying its events.

Example:

Initial state:

```
const initialState = {
  status: "not_created",
  total: 0,
};
```

Apply `OrderCreated`:

```
{
  status: "pending",
  total: 2500
}
```

Apply `PaymentCompleted`:

```
{
  status: "paid",
  total: 2500
}
```

Apply `OrderShipped`:

```
{
  status: "shipped",
  total: 2500
}
```

The current state is reconstructed from the historical events.

### **7.6 What are the advantages of Event Sourcing?**

- Complete business history.
- Auditability.
- Historical reconstruction.
- Ability to rebuild projections.
- Better visibility into why a state changed.
- Support for certain complex domain workflows.
- Event replay for new read models.

### **7.7 What are the disadvantages?**

- More complicated data modeling.
- Event versioning requirements.
- Projection management.
- Event replay can be expensive.
- Debugging is more complex.
- Data privacy and retention require careful design.
- Migration away from Event Sourcing can be expensive.

**Important:** Event Sourcing is not a replacement for ordinary CRUD in every application. Use it selectively when historical reconstruction, auditability, or event-driven domain behavior justifies its complexity.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **8. Snapshots in Event Sourcing**

### **8.1 What is a snapshot?**

A snapshot is a saved representation of an entity's state at a particular event version.

Why do we need snapshots?

Suppose an account has 100,000 events.

To reconstruct the current account balance, the application would otherwise need to replay all 100,000 events.

This can become expensive.

A snapshot stores the calculated state at a particular point.

[Microservice Pattern : Event Sourcing Pattern | by Abhinav Thakur | Apr, 2026 | Medium](https://images.openai.com/static-rsc-4/w3iWlDTI4u_2Iv9GxSnkaUhrFsrgqOlMtNOQytxJf4GaWOPWMNBtUdVGznCQioq0a-3iVO9j_RJENmf6mTk9ER-dGxLtMbQcROtfvHaa7So0bsRD9i8htzYMxKvTM-k4th9M9OJ7abjYGh0lnw06QGJoNMsx5-Dt0J327IWBKps?purpose=inline)

[medium.com](https://medium.com/%40abhi.strike/microservice-pattern-event-sourcing-pattern-e4db625392a1?utm_source=chatgpt.com)

Example:

```
Event 1
Event 2
Event 3
...
Event 1000
      |
      v
  Snapshot 1
      |
Event 1001
Event 1002
...
Event 2000
      |
      v
  Snapshot 2
      |
Event 2001
Event 2002
```

Suppose Snapshot 2 contains the state after event 2,000.

To reconstruct the current state, you:

1. Load Snapshot 2.
2. Read events after version 2,000.
3. Apply those events to the snapshot.

You do not need to replay the entire history.

### **8.2 Snapshot example**

Suppose the account events are:

```
AccountOpened: balance = 0
MoneyDeposited: amount = 1000
MoneyWithdrawn: amount = 200
MoneyDeposited: amount = 500
```

The snapshot after event 4 is:

```
{
  "accountId": "ACC-1001",
  "version": 4,
  "state": {
    "balance": 1300
  }
}
```

Later events:

```
MoneyWithdrawn: amount = 100
MoneyDeposited: amount = 200
```

The final balance is:

```
1300 - 100 + 200 = 1400
```

### **8.3 Snapshot vs event store**

| Event store                       | Snapshot                       |
| --------------------------------- | ------------------------------ |
| Stores original historical events | Stores a calculated state      |
| Source of truth                   | Performance optimization       |
| Append-only                       | Can be replaced or regenerated |
| Complete history                  | State at a particular version  |
| Used for replay                   | Reduces replay work            |

**Remember:** A snapshot does not replace the event store. The event stream remains the source of truth.

Microsoft's Event Sourcing guidance recommends snapshots when long event streams make rehydration expensive.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **9. Optimistic concurrency control**

### **9.1 What is a concurrency problem?**

Suppose two users try to modify the same order simultaneously.

Both users load version 5.

```
User A reads Order Version 5
User B reads Order Version 5
```

User A changes the order and saves version 6.

User B then tries to save based on version 5.

If the system blindly accepts both changes, User B may overwrite User A's changes.

This is a concurrency conflict.

### **9.2 What is optimistic concurrency control?**

Optimistic concurrency assumes conflicts are relatively uncommon.

Before saving, the application checks whether the data version is still the version it originally read.

Example:

```
Current Version = 5

User A:
Expected Version = 5
Append Event -> Version 6
Success

User B:
Expected Version = 5
Append Event -> Version 6
Rejected: Version Conflict
```

User B must reload the latest state, reevaluate the command, and retry if it is still valid.

### **9.3 Event stream version**

An event stream can have versions:

```
Order ID: ORD-1001

Version 1 -> OrderCreated
Version 2 -> PaymentCompleted
Version 3 -> OrderShipped
```

The application can append a new event only if the expected current version matches the stored stream version.

### **9.4 Why is this important?**

Consider a ticket booking system.

There is one seat left.

Two customers attempt to reserve it simultaneously.

Both read:

```
Available Seats = 1
```

Without concurrency control, both may reserve the same seat.

With optimistic concurrency, only one command can successfully append the next event based on the expected stream version.

The other request receives a conflict and must reevaluate availability.

This is a key technique for protecting business invariants in event-sourced systems.

## **10. Event versioning and schema evolution**

### **10.1 Why do events need versions?**

Events are stored for long periods.

Suppose your application originally stored:

```
{
  "type": "OrderCreated",
  "data": {
    "orderId": "ORD-1001",
    "total": 2500
  }
}
```

Later, your application requires a currency field.

New events might look like:

```
{
  "type": "OrderCreated",
  "version": 2,
  "data": {
    "orderId": "ORD-1002",
    "total": 2500,
    "currency": "INR"
  }
}
```

Now your event store contains events created by different versions of the application.

Your event handlers must understand both.

### **10.2 Three common approaches**

**1. Upcasting**

An upcaster converts an old event into a newer in-memory representation during reading.

Example:

```
type OrderCreatedV1 = {
  type: "OrderCreated";
  version: 1;
  data: {
    orderId: string;
    total: number;
  };
};

type OrderCreatedV2 = {
  type: "OrderCreated";
  version: 2;
  data: {
    orderId: string;
    total: number;
    currency: string;
  };
};

function upcastOrderCreated(
  event: OrderCreatedV1 | OrderCreatedV2
): OrderCreatedV2 {
  if (event.version === 1) {
    return {
      type: "OrderCreated",
      version: 2,
      data: {
        ...event.data,
        currency: "INR",
      },
    };
  }

  return event;
}
```

The original event remains unchanged in storage.

**2. Versioned event types**

You can introduce a new event name or version:

```
OrderCreatedV1
OrderCreatedV2
```

This makes schema changes explicit.

**3. Backward-compatible event design**

You can design event changes so older consumers can continue working.

For example, adding an optional field is often easier to support than changing the meaning or type of an existing field.

### **10.3 Event schema evolution rules**

- Do not casually rename existing event fields.
- Avoid changing the meaning of an existing event.
- Include event version metadata.
- Maintain backward-compatible handlers where possible.
- Test historical events against new projection code.
- Preserve old event payloads.
- Use upcasters when necessary.

**Important:** Events are long-lived business records, not disposable API response objects. Schema evolution must be planned.

## **11. CQRS combined with Event Sourcing**

Now let's combine the two patterns.

CQRS separates reading from writing.

Event Sourcing stores changes as an append-only sequence of events.

Together, they can form a powerful architecture.

[API Development Series Part 2: Data Layer & Persistence - Wasil Zafar](https://images.openai.com/static-rsc-4/rGr-pIPaOpLVQheCyGBzHC3bYcpA3Vlbk08lFgCOrl5Fn-Kr7rgAHy6meTwg1YeCIs74TLKucFjPXtctwGagbtESUCdtjYBNhDamk0wlyFJ8mFGkKZUSNRJSr7AiiHn5k9nxbq5bAZpm_zCIoBoc4BXPoh4afhjoFBRgI-7PqT0?purpose=inline)

[wasilzafar.com](https://www.wasilzafar.com/pages/series/api-development/api-dev-data-persistence.html?utm_source=chatgpt.com)

### **11.1 Complete architecture**

```
                  Client
                     |
                     v
                Next.js App
                     |
              +------+------+
              |             |
              v             v
           Commands       Queries
              |             |
              v             v
        Command Handler  Query Handler
              |             |
              v             |
        Domain Aggregate    |
              |             |
              v             |
          Event Store       |
              |             |
              v             |
        Event Publisher     |
              |             |
              v             |
        Projection Handler  |
              |             |
              v             |
         Read Database <----+
              |
              v
          Read Result
```

### **11.2 Write workflow**

Suppose a customer places an order.

1. The client sends a `CreateOrder` command.
2. The command handler loads the relevant aggregate.
3. Business rules are validated.
4. New events are generated.
5. Events are appended to the event store.
6. The command returns its result.

### **11.3 Read workflow**

1. The client requests order information.
2. The query handler reads the read model.
3. The read model returns a DTO.
4. The frontend displays the result.

### **11.4 Projection workflow**

1. A new event is stored.
2. An event consumer receives it.
3. Projection logic processes the event.
4. The read model is updated.
5. Future queries return the updated information.

### **11.5 Important: eventual consistency**

The event store may contain a new event before the read model has been updated.

For a short period:

```
Write Model: Order Status = Paid
Read Model:  Order Status = Pending
```

This is an expected possibility in an asynchronous CQRS architecture.

The application should handle it intentionally.

For example, after creating an order, the command response can return the order ID and initial status directly, rather than immediately querying a projection that may not yet have processed the event.

## **12. Implementing CQRS using Node.js, TypeScript, and MongoDB**

Now let's create a practical educational implementation.

We will build a small order-management system.

Our requirements:

- Create an order.
- Cancel an order.
- Retrieve order details.
- Store historical events.
- Maintain a separate read model.

For this exercise, MongoDB will store:

1. Event streams.
2. Order read models.

We will use separate collections within one database. This demonstrates CQRS without requiring two database servers.

### **12.1 Architecture**

```
Next.js Application
        |
        +------------------+
        |                  |
        v                  v
 CreateOrder          GetOrderById
        |                  |
        v                  v
 Command Handler      Query Handler
        |                  |
        v                  v
 Event Store         Read Collection
        |                  ^
        v                  |
 Projection Handler ------+
```

### **12.2 Folder structure**

```
src/
├── app/
│   ├── orders/
│   │   └── page.tsx
│   │
│   └── api/
│       └── orders/
│           └── route.ts
│
├── modules/
│   └── orders/
│       ├── commands/
│       │   ├── create-order.ts
│       │   └── cancel-order.ts
│       │
│       ├── queries/
│       │   └── get-order.ts
│       │
│       ├── domain/
│       │   └── order.aggregate.ts
│       │
│       ├── events/
│       │   └── order.events.ts
│       │
│       ├── projections/
│       │   └── order.projection.ts
│       │
│       └── repositories/
│           ├── event.repository.ts
│           └── order-read.repository.ts
│
└── lib/
    └── mongodb.ts
```

### **12.3 Define event types**

`order.events.ts`:

```
export type OrderCreatedEvent = {
  eventId: string;
  aggregateId: string;
  version: number;
  type: "OrderCreated";
  occurredAt: Date;
  data: {
    userId: string;
    total: number;
  };
};

export type OrderCancelledEvent = {
  eventId: string;
  aggregateId: string;
  version: number;
  type: "OrderCancelled";
  occurredAt: Date;
  data: {
    reason: string;
  };
};

export type OrderEvent =
  | OrderCreatedEvent
  | OrderCancelledEvent;
```

This uses a discriminated union so TypeScript can identify each event type.

### **12.4 Define the domain aggregate**

An aggregate is a domain object that protects business rules and consistency boundaries.

`order.aggregate.ts`:

```
import type { OrderEvent } from "../events/order.events";

export type OrderState = {
  id: string;
  userId: string;
  total: number;
  status: "pending" | "cancelled";
  version: number;
};

export class OrderAggregate {
  private state: OrderState | null = null;

  apply(event: OrderEvent) {
    switch (event.type) {
      case "OrderCreated":
        this.state = {
          id: event.aggregateId,
          userId: event.data.userId,
          total: event.data.total,
          status: "pending",
          version: event.version,
        };
        break;

      case "OrderCancelled":
        if (!this.state) {
          throw new Error("Order does not exist");
        }

        this.state = {
          ...this.state,
          status: "cancelled",
          version: event.version,
        };
        break;
    }
  }

  create(
    id: string,
    userId: string,
    total: number
  ): OrderEvent[] {
    if (this.state) {
      throw new Error("Order already exists");
    }

    if (total <= 0) {
      throw new Error("Order total must be positive");
    }

    return [
      {
        eventId: crypto.randomUUID(),
        aggregateId: id,
        version: 1,
        type: "OrderCreated",
        occurredAt: new Date(),
        data: {
          userId,
          total,
        },
      },
    ];
  }

  cancel(reason: string): OrderEvent[] {
    if (!this.state) {
      throw new Error("Order does not exist");
    }

    if (this.state.status === "cancelled") {
      throw new Error("Order is already cancelled");
    }

    return [
      {
        eventId: crypto.randomUUID(),
        aggregateId: this.state.id,
        version: this.state.version + 1,
        type: "OrderCancelled",
        occurredAt: new Date(),
        data: {
          reason,
        },
      },
    ];
  }

  loadFromHistory(events: OrderEvent[]) {
    for (const event of events) {
      this.apply(event);
    }
  }

  getState() {
    return this.state;
  }
}
```

The aggregate:

- Reconstructs its state from events.
- Validates business rules.
- Generates new events.
- Does not directly access MongoDB.

This separation makes domain logic easier to test.

### **12.5 Create MongoDB collections and indexes**

`lib/mongodb.ts`:

```
import "server-only";

import { MongoClient } from "mongodb";

const uri = process.env.MONGODB_URI;

if (!uri) {
  throw new Error("MONGODB_URI is missing");
}

const client = new MongoClient(uri);

export const clientPromise = client.connect();
```

Create the collections:

```
import { clientPromise } from "@/lib/mongodb";

export async function initializeDatabase() {
  const client = await clientPromise;
  const db = client.db("cqrs_demo");

  const events = db.collection("order_events");

  await events.createIndex(
    {
      aggregateId: 1,
      version: 1,
    },
    {
      unique: true,
    }
  );

  await events.createIndex({
    aggregateId: 1,
    version: 1,
  });

  await db.collection("processed_events").createIndex(
    {
      eventId: 1,
    },
    {
      unique: true,
    }
  );
}
```

The unique compound index prevents two events from being appended with the same aggregate ID and version.

### **12.6 Create the event repository**

`event.repository.ts`:

```
import "server-only";

import { clientPromise } from "@/lib/mongodb";
import type { OrderEvent } from "../events/order.events";

export async function getOrderEvents(orderId: string) {
  const client = await clientPromise;

  return client
    .db("cqrs_demo")
    .collection<OrderEvent>("order_events")
    .find({
      aggregateId: orderId,
    })
    .sort({
      version: 1,
    })
    .toArray();
}

export async function appendOrderEvents(
  events: OrderEvent[]
) {
  const client = await clientPromise;

  if (events.length === 0) return;

  const collection = client
    .db("cqrs_demo")
    .collection<OrderEvent>("order_events");

  await collection.insertMany(events, {
    ordered: true,
  });
}
```

The repository provides two operations:

- Read the history of an order.
- Append new events to its history.

In this simplified implementation, event storage is append-only.

### **12.7 Implement the CreateOrder command**

`create-order.ts`:

```
import "server-only";

import { OrderAggregate } from "../domain/order.aggregate";
import { appendOrderEvents } from "../repositories/event.repository";

export async function createOrder(
  userId: string,
  total: number
) {
  const aggregate = new OrderAggregate();

  const orderId = crypto.randomUUID();

  const events = aggregate.create(
    orderId,
    userId,
    total
  );

  await appendOrderEvents(events);

  return {
    orderId,
    status: "pending",
  };
}
```

Notice that the command handler does not directly update the read model.

Its responsibility is to validate and persist the business event.

### **12.8 Implement the CancelOrder command**

`cancel-order.ts`:

```
import "server-only";

import { OrderAggregate } from "../domain/order.aggregate";

import {
  appendOrderEvents,
  getOrderEvents,
} from "../repositories/event.repository";

export async function cancelOrder(
  orderId: string,
  reason: string
) {
  const history = await getOrderEvents(orderId);

  const aggregate = new OrderAggregate();

  aggregate.loadFromHistory(history);

  const events = aggregate.cancel(reason);

  await appendOrderEvents(events);

  return {
    orderId,
    status: "cancelled",
  };
}
```

The aggregate reconstructs its current state from historical events before deciding whether cancellation is valid.

**Production concurrency requirement:** This basic example has a race condition if two commands read the same event-stream version simultaneously. The unique `(aggregateId, version)` index helps detect conflicting appends, but the command handler must catch the duplicate-key conflict, reload the stream, reevaluate the command, and retry or return a conflict response. A production event store should implement this as an explicit expected-version append operation.

### **12.9 Create the order projection**

`order.projection.ts`:

```
import "server-only";

import { clientPromise } from "@/lib/mongodb";
import type { OrderEvent } from "../events/order.events";

export async function projectOrderEvent(
  event: OrderEvent
) {
  const client = await clientPromise;

  const db = client.db("cqrs_demo");

  const session = client.startSession();

  try {
    await session.withTransaction(async () => {
      await db.collection("processed_events").insertOne(
        {
          eventId: event.eventId,
          processedAt: new Date(),
        },
        {
          session,
        }
      );

      const orders = db.collection("order_read_models");

      switch (event.type) {
        case "OrderCreated":
          await orders.insertOne(
            {
              orderId: event.aggregateId,
              userId: event.data.userId,
              total: event.data.total,
              status: "pending",
              version: event.version,
            },
            {
              session,
            }
          );
          break;

        case "OrderCancelled":
          await orders.updateOne(
            {
              orderId: event.aggregateId,
            },
            {
              $set: {
                status: "cancelled",
                cancellationReason: event.data.reason,
                version: event.version,
              },
            },
            {
              session,
            }
          );
          break;
      }
    });
  } finally {
    await session.endSession();
  }
}
```

Why do we use a transaction here?

We want the following operations to succeed or fail together:

1. Record that the event has been processed.
2. Update the read model.

If the projection update fails, the processed-event record should also roll back.

This example requires a MongoDB replica set or sharded cluster for multi-document transactions. MongoDB documents these transaction requirements in its Node.js driver guide.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.mongodb.com\&sz=32)

MongoDB Docs



### **12.10 Implement the query handler**

`get-order.ts`:

```
import "server-only";

import { clientPromise } from "@/lib/mongodb";

export async function getOrderById(orderId: string) {
  const client = await clientPromise;

  const order = await client
    .db("cqrs_demo")
    .collection("order_read_models")
    .findOne({
      orderId,
    });

  return order;
}
```

The query handler reads from the read model rather than replaying the event history for every request.

### **12.11 Create a Route Handler**

`app/api/orders/route.ts`:

```
import { NextRequest, NextResponse } from "next/server";

import { createOrder } from "@/modules/orders/commands/create-order";
import { getOrderById } from "@/modules/orders/queries/get-order";

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();

    if (
      typeof body.userId !== "string" ||
      typeof body.total !== "number" ||
      body.total <= 0
    ) {
      return NextResponse.json(
        { error: "Invalid order data" },
        { status: 400 }
      );
    }

    // Authenticate and authorize the user here.
    const result = await createOrder(
      body.userId,
      body.total
    );

    return NextResponse.json(result, {
      status: 201,
    });
  } catch {
    return NextResponse.json(
      { error: "Unable to create order" },
      { status: 500 }
    );
  }
}

export async function GET(request: NextRequest) {
  const orderId = request.nextUrl.searchParams.get("id");

  if (!orderId) {
    return NextResponse.json(
      { error: "Order ID is required" },
      { status: 400 }
    );
  }

  const order = await getOrderById(orderId);

  if (!order) {
    return NextResponse.json(
      { error: "Order not found or projection not ready" },
      { status: 404 }
    );
  }

  return NextResponse.json(order);
}
```

This demonstrates the separation between commands and queries.

For a real application, authenticate the user from a trusted server-side session, validate with a schema library such as Zod, and ensure the user is authorized to access the requested order. Do not trust a client-supplied `userId` as proof of identity.

### **12.12 How do events reach the projection?**

In a production system, an event publisher or broker consumer would invoke:

```
await projectOrderEvent(event);
```

For example:

```
CreateOrder Command
        |
        v
    Event Store
        |
        v
    Event Publisher
        |
        v
    RabbitMQ / Kafka
        |
        v
  Projection Consumer
        |
        v
  projectOrderEvent()
        |
        v
  Read Model Collection
```

For this educational exercise, you can initially invoke the projection directly after saving an event. Then replace that direct call with a queue or event consumer.

Remember: the event store and projection are separate responsibilities. The projection must be recoverable from the authoritative event history.

## **13. MongoDB Change Streams and CQRS**

Since MongoDB is one of the databases used in our examples, it is important to understand Change Streams.

### **13.1 What are MongoDB Change Streams?**

MongoDB Change Streams allow an application to listen for database changes in real time.

For example, when a document is inserted, updated, or deleted, your application can receive a change notification.

[Change Data Capture (CDC): Advanced Notes with MongoDB and Kafka | by Sukanya Mondal | Medium](https://images.openai.com/static-rsc-4/xFCqaqRDcynqewOHDcD93gaa4RqxO7OMkv0kb02L5wQgZ3auUrVS-c6HPnANUsptn3FWlU8QDJjCPTLB9oejzxM9NBV1v7axI_ElEp4aE6ZBmW7yDJXfy6BCKtukzulhN-x62njIpPvd9dWnyJHYVInpSxwYzFZYmHU3JTqKSyc?purpose=inline)

[medium.com](https://medium.com/%40sukanyam.014/change-data-capture-cdc-advanced-notes-with-mongodb-and-kafka-bb1645831ee0?utm_source=chatgpt.com)

Example:

```
import { MongoClient } from "mongodb";

const client = new MongoClient(
  process.env.MONGODB_URI!
);

async function watchOrders() {
  await client.connect();

  const db = client.db("ecommerce");

  const collection = db.collection("orders");

  const changeStream = collection.watch();

  for await (const change of changeStream) {
    console.log("Database change:", change);
  }
}

watchOrders().catch(console.error);
```

When an order document changes, the application receives a change event.

### **13.2 Use Change Streams to update a read model**

Consider this architecture:

```
Order Write Collection
          |
          v
    Change Stream
          |
          v
  Projection Handler
          |
          v
 Order Read Collection
```

This allows you to synchronize a read model based on database changes.

### **13.3 Important: Change Streams vs Event Sourcing**

These are not the same thing.

| MongoDB Change Streams                              | Event Sourcing                                |
| --------------------------------------------------- | --------------------------------------------- |
| Observes database changes                           | Stores business events as the source of truth |
| Reports insert/update/delete operations             | Records meaningful domain events              |
| Depends on MongoDB change history and resume tokens | Uses an application-managed event history     |
| Can support synchronization and integration         | Supports reconstruction of domain state       |
| Does not automatically record business intent       | Events should represent business intent       |

For example:

A Change Stream may report that an order's `status` field changed from `pending` to `paid`.

An Event Sourcing system may record:

```
{
  "type": "PaymentCompleted",
  "orderId": "ORD-1001",
  "transactionId": "TXN-1001"
}
```

The second event communicates the business meaning of the change.

### **13.4 Can Change Streams replace an event store?**

Generally, no.

Change Streams are useful for reacting to database changes, synchronizing data, and building downstream integrations.

But they do not automatically provide all event-store capabilities, such as:

- Business-intent event modeling.
- Per-aggregate stream versioning.
- Expected-version append operations.
- Domain-state reconstruction from an immutable event history.

MongoDB also requires a replica set or sharded cluster for Change Streams. Resume tokens can help consumers resume after interruptions, subject to the available oplog history.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.mongodb.com\&sz=32)

MongoDB Docs+2



Official resources:

- [MongoDB – Change Streams](https://www.mongodb.com/docs/manual/changestreams/?utm_source=chatgpt.com)
- [MongoDB Node.js Driver – Change Streams](https://www.mongodb.com/docs/drivers/node/current/monitoring-and-logging/change-streams/?utm_source=chatgpt.com)

## **14. Event Store vs Message Broker vs Read Database**

These three components have different responsibilities.

| Component      | Responsibility                                  | Example                                         |
| -------------- | ----------------------------------------------- | ----------------------------------------------- |
| Event Store    | Permanently records authoritative domain events | EventStoreDB or append-only database collection |
| Message Broker | Delivers events to consumers                    | RabbitMQ or Kafka                               |
| Read Database  | Stores query-optimized projections              | MongoDB or PostgreSQL                           |

### **14.1 Understand the complete flow**

```
             Command Handler
                    |
                    v
                Event Store
                    |
                    v
             Event Publisher
                    |
                    v
              Message Broker
                    |
          +---------+---------+
          |                   |
          v                   v
    Order Projection    Analytics Projection
          |                   |
          v                   v
     Read Database        Analytics DB
          |
          v
       Next.js UI
```

### **14.2 Why is Kafka not automatically an event store?**

Kafka retains messages and allows consumers to replay them.

However, an event store commonly provides domain-specific capabilities such as:

- Loading the complete stream for an aggregate.
- Appending with an expected version.
- Enforcing per-stream concurrency.
- Reconstructing an aggregate.

Kafka is excellent for event distribution and streaming. It should not automatically be treated as a complete replacement for an event store.

The Microsoft Event Sourcing guidance makes this distinction explicitly.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://learn.microsoft.com\&sz=32)

Microsoft Learn+1



## **15. When should you use CQRS and Event Sourcing?**

These patterns solve different problems, and neither is mandatory for every application.

### **15.1 When to use CQRS**

Consider CQRS when:

- Your application has significantly different read and write workloads.
- Read queries are complicated.
- Different screens need different data representations.
- Read and write scaling requirements differ.
- Business operations are complex.
- Separate read models would simplify the application.

### **15.2 When not to use CQRS**

Avoid unnecessary CQRS when:

- Your application is primarily simple CRUD.
- Your database queries are already efficient.
- Your read/write workload is relatively balanced.
- Separate models provide little benefit.
- Your team cannot justify the additional synchronization complexity.

### **15.3 When to use Event Sourcing**

Consider Event Sourcing when:

- You need a complete business audit trail.
- Historical reconstruction is important.
- You need to understand why a state changed.
- Business events are central to the domain.
- Rebuilding projections is valuable.
- The system needs a reliable history of state transitions.

### **15.4 When not to use Event Sourcing**

Avoid it when:

- Only the latest state matters.
- Ordinary CRUD is sufficient.
- Historical reconstruction is not required.
- The domain is simple.
- Your team cannot manage event versioning, projections, and replay.
- The additional operational complexity is not justified.

### **15.5 Decision table**

| Application                     | CQRS                       | Event Sourcing                 |
| ------------------------------- | -------------------------- | ------------------------------ |
| Simple portfolio website        | Usually unnecessary        | Usually unnecessary            |
| Basic expense tracker           | Optional                   | Usually unnecessary            |
| Large e-commerce platform       | Can be useful              | Useful selectively             |
| Banking ledger                  | Often useful               | Often a strong fit             |
| Ticket reservation platform     | Can be useful              | Useful for reservation history |
| Analytics dashboard             | Read-model patterns useful | Not necessarily required       |
| Basic blog CMS                  | Usually unnecessary        | Usually unnecessary            |
| Complex insurance claims system | Often useful               | Can be useful                  |

**Architecture recommendation:** Start with simple CRUD and introduce CQRS or Event Sourcing only where a concrete business or technical requirement justifies them.

## **16. Common CQRS and Event Sourcing mistakes**

| Mistake                                    | Why it is a problem                                         | Better approach                          |
| ------------------------------------------ | ----------------------------------------------------------- | ---------------------------------------- |
| Applying CQRS to every CRUD operation      | Unnecessary complexity                                      | Introduce CQRS selectively               |
| Treating commands and queries as identical | Responsibilities become mixed                               | Separate handlers                        |
| Updating historical events                 | Destroys audit history                                      | Append corrective events                 |
| No event versioning                        | Old events may break new code                               | Use versioned schemas                    |
| Replaying every event on every request     | Expensive state reconstruction                              | Use snapshots                            |
| Updating read models without deduplication | Duplicate projections                                       | Use idempotent consumers                 |
| Ignoring optimistic concurrency            | Concurrent commands can conflict                            | Use expected stream versions             |
| Assuming read models update instantly      | Users may see stale data                                    | Design for eventual consistency          |
| Treating Kafka as a complete event store   | Missing domain-specific stream guarantees                   | Separate event storage from distribution |
| Treating Change Streams as Event Sourcing  | Database changes do not necessarily capture business intent | Model domain events explicitly           |
| No projection recovery strategy            | Read model corruption becomes difficult to fix              | Support projection rebuilding            |
| No event retention strategy                | Storage and compliance problems                             | Define retention and privacy policies    |
| Overcomplicated event schemas              | Harder to maintain compatibility                            | Use stable, meaningful contracts         |

## **17. Interview questions and answers**

### **Basic-level questions**

**Q1. What is CQRS?**

CQRS stands for Command Query Responsibility Segregation. It separates operations that modify application state from operations that retrieve data.

**Q2. What is a command?**

A command represents an instruction to change application state, such as `CreateOrder`.

**Q3. What is a query?**

A query retrieves information without changing application state, such as `GetOrderById`.

**Q4. What is a write model?**

A write model represents application data and business rules needed to process commands.

**Q5. What is a read model?**

A read model is a representation of data optimized for retrieval and presentation.

**Q6. What is Event Sourcing?**

Event Sourcing stores state changes as an append-only sequence of events rather than storing only the latest state.

**Q7. What is an event store?**

An event store persists events and supports retrieving and appending events to entity streams.

**Q8. What is an event stream?**

It is an ordered sequence of events belonging to an entity or aggregate.

**Q9. What is rehydration?**

Rehydration reconstructs the current state of an entity by applying its historical events.

**Q10. What is a projection?**

A projection transforms events or source data into a representation optimized for reading.

### **Intermediate-level questions**

**Q11. What is the difference between CQRS and Event Sourcing?**

CQRS separates read and write responsibilities. Event Sourcing stores historical state changes as events.

**Q12. Does CQRS require separate databases?**

No. It can use different models over one database or separate databases for reads and writes.

**Q13. What is a materialized view?**

It is a stored, precomputed representation of data optimized for efficient querying.

**Q14. What is a snapshot?**

A snapshot stores the calculated state of an aggregate at a particular event-stream version to reduce replay work.

**Q15. What is optimistic concurrency control?**

It checks whether the data version is unchanged before accepting a write, detecting conflicting concurrent updates.

**Q16. What is event versioning?**

Event versioning manages changes to event schemas over time so historical events remain processable.

**Q17. What is an upcaster?**

An upcaster transforms an older event representation into a newer in-memory representation without changing the stored historical event.

**Q18. What is eventual consistency in CQRS?**

It means the write model may contain a new update before the corresponding read model has processed it.

**Q19. Can CQRS be implemented in a monolith?**

Yes. CQRS is an architectural pattern, not a microservices requirement.

**Q20. What is the difference between a projection and a materialized view?**

A projection is the transformation logic. A materialized view is a stored result of that transformation.

### **Advanced-level questions**

**Q21. Why should historical events be immutable?**

Because events represent facts about what happened. Changing them would compromise historical accuracy and auditability.

**Q22. How do you prevent concurrent event writes?**

Use optimistic concurrency with an expected stream version and an atomic append operation.

**Q23. How do you rebuild a read model?**

Replay the authoritative event history through the projection logic and write the results into a newly built read model.

**Q24. Why are snapshots required?**

They reduce the amount of historical data that must be replayed when reconstructing an aggregate.

**Q25. What is the difference between Event Sourcing and MongoDB Change Streams?**

Event Sourcing stores business events as the source of truth. Change Streams report database-level changes so applications can react to them.

**Q26. What happens if a projection fails?**

The consumer should retry safely, preserve processing progress, and support replay or rebuilding from the event source.

**Q27. Can a read model become inconsistent with the event store?**

Yes. Projection failures, duplicate processing, or delayed events can cause inconsistencies. Idempotency, monitoring, and replay help recover.

**Q28. Why is Event Sourcing useful for financial systems?**

It provides a historical record of business transactions and makes it possible to reconstruct balances and audit how they changed. It still requires correct financial invariants and transaction design.

**Q29. What is the difference between an event store and a message broker?**

An event store is the authoritative history of domain events. A message broker distributes messages to consumers.

**Q30. When should you avoid Event Sourcing?**

When simple CRUD is sufficient, historical reconstruction is unnecessary, and the additional complexity does not provide meaningful value.

## **18. Practical assignment – Build a CQRS and Event Sourcing application**

Major hands-on assignment

Build an **Order Management System** using:

- Next.js 16.
- TypeScript.
- Node.js.
- MongoDB.
- RabbitMQ or Kafka (optional initially).
- Redis (optional).

### **Part A – CQRS fundamentals**

- Create separate command handlers.
- Create separate query handlers.
- Define write models.
- Define read models.
- Create order DTOs.
- Keep query operations free from business side effects.

### **Part B – Event Sourcing**

- Create an event store collection.
- Define `OrderCreated`.
- Define `PaymentCompleted`.
- Define `OrderCancelled`.
- Implement event appending.
- Implement event history retrieval.
- Implement aggregate rehydration.
- Implement optimistic concurrency.

### **Part C – Projections**

- Create an order summary projection.
- Create an admin dashboard projection.
- Update projections when events arrive.
- Implement event deduplication.
- Implement projection rebuilding.

### **Part D – Snapshots and versioning**

- Create a snapshot collection.
- Save aggregate state after a defined number of events.
- Rehydrate from a snapshot.
- Add event version metadata.
- Implement an upcaster for an older event.

### **Part E – MongoDB integration**

- Configure the MongoDB connection.
- Create the event collection.
- Create the read-model collection.
- Add unique indexes for event IDs and aggregate versions.
- Implement transactions where needed.
- Test concurrent commands.

### **Part F – Frontend integration**

- Create an order creation form.
- Display order details.
- Display order history.
- Display the current order status.
- Handle projection delays in the UI.

### **Expected architecture**

```
              Next.js Frontend
                     |
            +--------+--------+
            |                 |
            v                 v
         Commands           Queries
            |                 |
            v                 v
       Command Handler   Query Handler
            |
            v
       Order Aggregate
            |
            v
        Event Store
            |
            v
       Event Consumer
            |
            v
         Projection
            |
            v
        Read Model
            |
            v
       Query Handler
```

**Assignment success criteria:** You should be able to create an order, inspect its historical events, reconstruct its current state, and retrieve a separate query-optimized representation.

## **19. Quick revision notes**

| Topic                  | Remember                                           |
| ---------------------- | -------------------------------------------------- |
| CQRS                   | Separates reads and writes                         |
| CQS                    | An operation changes state or returns information  |
| Command                | Instruction to change state                        |
| Query                  | Retrieves information                              |
| Write model            | Business rules and state changes                   |
| Read model             | Query-optimized representation                     |
| DTO                    | Data Transfer Object                               |
| Event Sourcing         | Stores state changes as events                     |
| Event store            | Persistent event history                           |
| Event stream           | Ordered events for an aggregate                    |
| Rehydration            | Reconstruct state by replaying events              |
| Snapshot               | Saved state at a specific event version            |
| Projection             | Transforms events into a read representation       |
| Materialized view      | Stored, precomputed query result                   |
| Optimistic concurrency | Prevents conflicting writes through version checks |
| Event versioning       | Handles historical event schema changes            |
| Upcasting              | Converts old event representations                 |
| Eventual consistency   | Read and write models can temporarily differ       |
| Change Streams         | Listen for database changes                        |
| Event store vs broker  | Historical storage vs message distribution         |

### **Most important concepts to remember**

**CQRS**

Separates commands that modify data from queries that retrieve data.

**Event Sourcing**

Stores business events as the authoritative history of state changes.

**Projection**

Converts historical events into a read-optimized data structure.

**Snapshot**

Saves a calculated state to reduce event replay time.

**Optimistic concurrency**

Prevents two commands from incorrectly appending events based on the same outdated version.

## **20. Official documentation and resources**

1. [Microsoft – CQRS Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs?utm_source=chatgpt.com) – CQRS architecture and implementation choices.
2. [Microsoft – Event Sourcing Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing?utm_source=chatgpt.com) – Event streams, snapshots, and projections.
3. [Microsoft – Event Sourcing Considerations](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing?trk=article-ssr-frontend-pulse_little-text-block\&utm_source=chatgpt.com) – Event design and schema evolution.
4. [MongoDB – Node.js Transactions](https://www.mongodb.com/docs/drivers/node/current/crud/transactions/?utm_source=chatgpt.com) – Atomic database operations.
5. [MongoDB – Node.js Change Streams](https://www.mongodb.com/docs/drivers/node/current/monitoring-and-logging/change-streams/?utm_source=chatgpt.com) – Real-time database change monitoring.
6. [MongoDB – Transaction Guide](https://www.mongodb.com/docs/drivers/node/current/crud/transactions/?utm_source=chatgpt.com) – Transaction behavior and limitations.

## **21. Phase 26 completion checklist**

**Your learning progress**

# **0%**

0 of 23 completed

Understand CQRS architecture

Understand commands vs queries

Understand CQS vs CQRS

Understand write models and read models

Understand DTOs

Understand projections

Understand materialized views

Understand Event Sourcing

Understand event stores and event streams

Understand rehydration

Understand snapshots

Understand optimistic concurrency

Understand event versioning

Understand upcasting

Understand CQRS with Event Sourcing

Understand eventual consistency

Understand MongoDB Change Streams

Understand event store vs message broker

Implement event repository

Implement command handlers

Implement query handlers

Implement read-model projections

Complete the order management assignment

Reset checklist

## **22. Next phase preview**

[Mastering Domain-Driven Design for Modern Enterprise Applications-Part 1 | by Bhargava Koya - Fullstack .NET Developer | Medium](https://images.openai.com/static-rsc-4/ShovfCxg-M2Kqp4NcNZmLazrrwR3dDl2IvJVahk3LuROzzt4w4W1YY0vBGLuF1MEQ5DIAVGby5ZeKzSi4qe-KFTewXBKSoKvLOlJLFAKsyCrM3AZkrwqT1lOtBk512KvZsmnUCKmM2sYqy3WxCQvy9X7fcPIT3y9BJKoz8r7FC8?purpose=inline)

**Phase 27 – Domain-Driven Design (DDD) and Advanced Domain Modeling**

Next Phase

Advanced software design and business domain architecture

Planned topics:

1. What is Domain-Driven Design?
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
14. DDD with Node.js, TypeScript, and MongoDB.
15. Designing real-world enterprise applications.

**When you're ready, say: OK, Phase 27.**