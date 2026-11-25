# **Phase 24 – Advanced Full-Stack Architecture and Scalability**



Advanced Level

Next.js 16.x

App Router + TypeScript + Node.js



## **1. Learning objectives**

In this phase, we will move beyond individual Next.js features and learn how to design complete, scalable full-stack applications.

You will learn:

1. Monolithic, modular monolithic, and microservices architectures.
2. Feature-based project architecture.
3. Frontend and backend separation.
4. Layered architecture: presentation, application, domain, and infrastructure.
5. Repository and service patterns.
6. Database architecture and connection management.
7. Redis caching and distributed caching.
8. Background jobs, queues, and workers.
9. Rate limiting and API protection.
10. Scalability, load balancing, and horizontal scaling.
11. Logging, monitoring, and error tracking.
12. How to design a production-ready full-stack application.

By the end of this phase, you should be able to plan the architecture of a real-world e-commerce, CRM, expense tracker, or SaaS application.

## **2. What is software architecture?**

Software architecture describes how an application is structured and how its components communicate with each other.

It defines:

- Where business logic lives.
- How frontend and backend communicate.
- How data is stored and retrieved.
- How authentication and authorization work.
- How external services are integrated.
- How the application handles increasing traffic.
- How the system can be maintained and extended.

Consider an e-commerce application.

A customer visits the website, searches for a product, adds it to the cart, and completes an order.

Behind this simple workflow, multiple components interact:

[PrestaShop Coding Standards & Best Practices (2026 Guide)](https://images.openai.com/static-rsc-4/R-9cpEuZmTmUArOQefQgjVSQ9EE_jV2PR5eThBLQYIw6Iue70uubyXPzYFmuxcCg9I0JRN_q7EiIE3Jx1g5vQqwG-rJJwejmYNW-d7hazalXuxJ8r686qLJm72vDPcHv4SgWXMybA2zyLkB9B04riQ1HVLcW13Xl3R0Ytjq-NOc?purpose=inline)

[fmemodules.com](https://www.fmemodules.com/en/blog/378-prestashop-coding-standards--best-practices-every-developer-should-follow?utm_source=chatgpt.com)

1. Frontend displays the product listing.
2. Backend retrieves products.
3. Database stores product information.
4. Authentication identifies the customer.
5. Cart service manages cart data.
6. Order service creates orders.
7. Payment service processes payments.
8. Notification service sends confirmation emails.

Architecture determines how these responsibilities are organized.

**Important:** Good architecture is not about using the maximum number of technologies. It is about organizing responsibilities so that the application remains understandable, secure, maintainable, and scalable.

## **3. Monolithic architecture**

### **3.1 What is a monolithic architecture?**

A monolithic application is an application in which its main application components are developed and deployed as one unit.

For example:

```
my-ecommerce/
├── app/
│   ├── products/
│   ├── orders/
│   ├── users/
│   └── admin/
│
├── components/
├── lib/
├── models/
├── services/
└── package.json
```

The same application handles:

- Product management.
- User management.
- Orders.
- Payments.
- Admin operations.

It can still have well-separated modules internally. Monolithic does not mean that all code must be written in one file.

### **3.2 How does it work?**

```
              Client
                |
                v
          Next.js Application
                |
      +---------+---------+
      |         |         |
   Products   Orders    Users
      |         |         |
      +---------+---------+
                |
             Database
```

All modules run as part of the same application deployment.

### **3.3 Advantages**

- Simple initial development.
- Easier deployment.
- Easier local debugging.
- Less infrastructure complexity.
- Straightforward communication between modules.
- Suitable for small and medium applications.

### **3.4 Disadvantages**

- Poor internal separation can create tightly coupled code.
- Large applications can become difficult to maintain.
- A deployment may require rebuilding the whole application.
- Scaling one workload independently is difficult.
- A failure in a critical shared process can affect the whole application.

### **3.5 When should you use it?**

Use a monolithic architecture when:

- Building an MVP.
- Developing a small or medium SaaS application.
- Working with a small engineering team.
- Business requirements are still changing.
- Independent deployment of services is not yet necessary.

For many Next.js applications, a well-designed monolith is an excellent starting point.

## **4. Modular monolithic architecture**

### **4.1 What is a modular monolith?**

A modular monolith is a single deployable application divided into independent logical modules.

Each module owns a specific business responsibility.

For example:

```
src/
├── app/
│   ├── products/
│   ├── orders/
│   ├── users/
│   └── admin/
│
├── modules/
│   ├── products/
│   ├── orders/
│   ├── users/
│   ├── payments/
│   └── notifications/
│
└── shared/
```

Each module can contain its own:

- Components.
- Business logic.
- Services.
- Repository functions.
- Validation schemas.
- Types.
- Tests.

### **4.2 Example: Product module**

```
modules/
└── products/
    ├── components/
    │   └── ProductCard.tsx
    │
    ├── services/
    │   └── product.service.ts
    │
    ├── repositories/
    │   └── product.repository.ts
    │
    ├── schemas/
    │   └── product.schema.ts
    │
    ├── types/
    │   └── product.types.ts
    │
    └── index.ts
```

`product.repository.ts`:

```
import "server-only";
import { db } from "@/lib/db";

export async function findProducts() {
  return db.product.findMany({
    where: {
      isActive: true,
    },
  });
}
```

`product.service.ts`:

```
import { findProducts } from "../repositories/product.repository";

export async function getActiveProducts() {
  return findProducts();
}
```

`app/products/page.tsx`:

```
import { getActiveProducts } from "@/modules/products/services/product.service";

export default async function ProductsPage() {
  const products = await getActiveProducts();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product.id}>
          {product.name}
        </div>
      ))}
    </main>
  );
}
```

### **4.3 Why is this better?**

Suppose you need to change the product database query.

You can modify the product repository without changing the UI or order module.

This improves separation of concerns.

**Separation of concerns** means each part of an application should have a clearly defined responsibility.

### **4.4 Monolith vs modular monolith**

| Feature             | Basic monolith               | Modular monolith            |
| ------------------- | ---------------------------- | --------------------------- |
| Deployment          | One application              | One application             |
| Code organization   | Can be loosely organized     | Explicit module boundaries  |
| Business logic      | Often mixed                  | Separated by responsibility |
| Maintainability     | Depends heavily on structure | Generally easier            |
| Complexity          | Low initially                | Moderate                    |
| Independent scaling | No                           | No                          |

For a growing Next.js application, a modular monolith is often a practical architecture.

## **5. Microservices architecture**

### **5.1 What are microservices?**

Microservices architecture divides an application into multiple independently deployable services.

Each service handles a specific business capability.

For example:

- User service.
- Product service.
- Order service.
- Payment service.
- Notification service.

Each service can have its own deployment, codebase, and data ownership.

[Designing an E-commerce Platform in 45 Minutes: A Complete System Design Interview Guide](https://images.openai.com/static-rsc-4/NPRJxSE_cIsiPiefcQV2TWyRq8cxqtmpeG1dmZWV-6V1xiSb3-53LKhHdrrd5YmL4Yfp6jgoOo5BKut_3K5fuOq9wEiV7Xmb_jlCSRHE39JefwOvSeYenT0trBczo2VyJUWvtdpvddWW0k-Zp2PjbfaD5t3lWMy5g_SofmAMLUs?purpose=inline)

[designgurus.substack.com](https://designgurus.substack.com/p/designing-an-e-commerce-platform?utm_source=chatgpt.com)

### **5.2 How does it work?**

```
                 Client
                   |
                   v
              API Gateway
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
   User Service Product Service Order Service
       |           |           |
    User DB     Product DB    Order DB
                               |
                               v
                         Payment Service
                               |
                               v
                         Payment Provider
```

The services communicate through:

- HTTP APIs.
- gRPC.
- Message queues.
- Event-driven messaging.

### **5.3 Example services**

| Service              | Responsibility                  |
| -------------------- | ------------------------------- |
| User Service         | Registration, login, profiles   |
| Product Service      | Product catalog and inventory   |
| Order Service        | Order creation and order status |
| Payment Service      | Payment processing              |
| Notification Service | Email and SMS notifications     |

### **5.4 Advantages**

- Independent deployment.
- Independent scaling.
- Technology flexibility.
- Better isolation of certain failures.
- Teams can work independently on services.
- Large systems can evolve independently.

### **5.5 Disadvantages**

- More infrastructure.
- More complicated deployment.
- Network communication introduces latency and failure modes.
- Distributed transactions are difficult.
- Monitoring and debugging become more complex.
- Data consistency requires additional design.

### **5.6 When should you use microservices?**

Consider microservices when:

- The application has many independent business domains.
- Different teams need independent ownership.
- Some workloads need significantly different scaling.
- Independent deployments provide clear business value.
- The organization can support distributed infrastructure.

Do not start with microservices just because the application is expected to become large.

### **5.7 Monolith vs microservices**

| Feature        | Monolith                    | Microservices                    |
| -------------- | --------------------------- | -------------------------------- |
| Deployment     | One application             | Multiple services                |
| Communication  | Often direct function calls | Network or messaging             |
| Scaling        | Usually application-level   | Service-level                    |
| Database       | Often shared                | Ideally owned per service        |
| Infrastructure | Simpler                     | More complex                     |
| Debugging      | Generally simpler           | Distributed tracing often needed |
| Team structure | Often centralized           | Can support independent teams    |

**Interview point:** Microservices are an architectural trade-off, not an automatic upgrade over monolithic architecture.

## **5. Layered architecture**

A modular monolith organizes code by business modules. Layered architecture organizes code by responsibility within those modules.

A common architecture has four layers:

1. Presentation layer.
2. Application layer.
3. Domain layer.
4. Infrastructure layer.

[Clean Architecture: Building Modular and Maintainable Software Systems | by Ravini Kuruppu | Medium](https://images.openai.com/static-rsc-4/rPq55hlFT0EkMWErturrb_khVNfFDHtiMuuaq8mocBTvky5me75XTKAHL3DjIWHB1vpfajn62i8EperffWzJOTZ5zT2rd4snAIgC0eOQJPoGcnD3cRD-58KSvGFmdBDZE_IF_3ymCtCzpQDIIQblbZTiPWd1Y4femKSsmVJvQKk?purpose=inline)

[medium.com](https://medium.com/%40ravinikuruppu/clean-architecture-c4d71fd0d191?utm_source=chatgpt.com)

### **5.1 Presentation layer**

Responsible for receiving user requests and presenting responses.

In Next.js, it can include:

- Pages.
- Layouts.
- React components.
- Route Handlers.
- Server Actions.
- Request validation at the boundary.

Example:

```
// app/products/page.tsx

import { getProducts } from "@/modules/products/services/product.service";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product) => (
        <article key={product.id}>
          {product.name}
        </article>
      ))}
    </main>
  );
}
```

The page is responsible for presenting data. It should not contain complicated database queries or business rules.

### **5.2 Application layer**

Responsible for coordinating application use cases.

Examples:

- Register a user.
- Create an order.
- Cancel an order.
- Add a product to a cart.
- Generate an invoice.

Example:

```
// modules/orders/services/order.service.ts

import { createOrderRecord } from "../repositories/order.repository";
import { getProductById } from "@/modules/products/repositories/product.repository";

export async function createOrder(
  userId: string,
  productId: string,
  quantity: number
) {
  if (quantity <= 0) {
    throw new Error("Quantity must be greater than zero");
  }

  const product = await getProductById(productId);

  if (!product) {
    throw new Error("Product not found");
  }

  if (product.stock < quantity) {
    throw new Error("Insufficient stock");
  }

  const total = product.price * quantity;

  return createOrderRecord({
    userId,
    productId,
    quantity,
    total,
  });
}
```

This service coordinates the workflow.

For a real payment workflow, stock checking, inventory reservation, and order creation must be protected against concurrent requests and handled transactionally where appropriate. The example illustrates the layer, not a complete production order implementation.

### **5.3 Domain layer**

Responsible for core business rules and concepts.

For example:

- What makes an order valid?
- Can a cancelled order be shipped?
- Can a customer receive a discount?
- What are the rules for refund eligibility?

Example:

```
// modules/orders/domain/order.ts

export type OrderStatus =
  | "pending"
  | "paid"
  | "shipped"
  | "cancelled";

export function canCancelOrder(status: OrderStatus) {
  return status === "pending" || status === "paid";
}
```

The domain layer should not depend directly on Next.js, HTTP request objects, or database implementation details.

### **5.4 Infrastructure layer**

Responsible for external technical systems.

Examples:

- MongoDB.
- MySQL.
- Redis.
- Payment gateways.
- Email providers.
- File storage.
- Message queues.

Example:

```
// modules/orders/repositories/order.repository.ts

import "server-only";
import { db } from "@/lib/db";

export async function createOrderRecord(data: {
  userId: string;
  productId: string;
  quantity: number;
  total: number;
}) {
  return db.order.create({
    data: {
      ...data,
      status: "pending",
    },
  });
}
```

### **5.5 How the layers communicate**

```
User Request
     |
     v
Presentation Layer
     |
     v
Application Layer
     |
     v
Domain Rules
     |
     v
Infrastructure Layer
     |
     v
Database / External Services
```

The direction of dependencies matters.

Ideally, business rules should not be tightly coupled to a particular database or framework.

For example, if you replace MongoDB with PostgreSQL, you should not have to rewrite your order validation rules.

### **5.6 Layered architecture vs feature-based architecture**

These are not competing approaches. They can be combined.

- Feature-based architecture groups code by business capability.
- Layered architecture separates responsibilities within each capability.

For a large application, using both can provide a clean structure.

## **6. Frontend and backend separation**

### **6.1 What is frontend-backend separation?**

It means separating the user interface from the backend business operations and data access.

A common architecture is:

```
                Browser
                   |
                   v
             Next.js Frontend
                   |
                   v
                HTTP API
                   |
                   v
             Node.js Backend
                   |
          +--------+--------+
          |        |        |
        Auth     Services  Validation
          |        |        |
          +--------+--------+
                   |
                   v
                Database
```

There are multiple ways to implement this.

### **6.2 Approach A: Next.js full-stack application**

Next.js handles both frontend and server-side operations.

```
next-app/
├── app/
│   ├── products/
│   └── api/
│       └── products/
│           └── route.ts
│
├── modules/
│   └── products/
│
└── lib/
```

The application can use:

- Server Components for data reading.
- Server Actions for UI-driven mutations.
- Route Handlers for public HTTP APIs and webhooks.
- Server-only services and repositories for database operations.

This is suitable for many SaaS and e-commerce applications.

### **6.3 Approach B: Separate Next.js and Express backend**

You can also use Next.js only for frontend responsibilities and Node.js/Express for backend APIs.

```
project/
├── frontend/
│   └── Next.js Application
│
└── backend/
    ├── routes/
    ├── controllers/
    ├── services/
    ├── repositories/
    ├── models/
    └── app.ts
```

Communication:

```
Browser
   |
   v
Next.js Frontend
   |
   | HTTP Request
   v
Express API
   |
   v
Service Layer
   |
   v
Database
```

Example frontend request:

```
"use client";

import { useEffect, useState } from "react";

type Product = {
  id: string;
  name: string;
};

export default function ProductsPage() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    async function loadProducts() {
      const response = await fetch(
        `${process.env.NEXT_PUBLIC_API_URL}/products`
      );

      if (!response.ok) {
        throw new Error("Unable to load products");
      }

      const data = await response.json();
      setProducts(data);
    }

    loadProducts().catch(console.error);
  }, []);

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

Here, the API URL is public configuration, not a secret.

### **6.4 Which architecture should you choose?**

| Requirement                                | Recommended approach                   |
| ------------------------------------------ | -------------------------------------- |
| Small or medium application                | Next.js full-stack                     |
| SaaS application                           | Next.js full-stack or separate backend |
| Multiple frontend clients                  | Shared backend API                     |
| Mobile app plus web app                    | Shared backend API often makes sense   |
| Existing Express backend                   | Keep Express and integrate Next.js     |
| Independently deployable business services | Consider service-based architecture    |

**Important Next.js distinction:** Next.js can act as a Backend for Frontend (BFF), but it is not automatically a replacement for every backend system. Route Handlers provide public HTTP endpoints; they do not remove the need for proper backend architecture, authorization, and data-access boundaries.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **7. Repository pattern and service layer**

These two patterns are especially important when building scalable backend applications.

### **7.1 What is the repository pattern?**

A repository is an abstraction responsible for data access.

It separates database operations from business logic.

Without a repository, database queries may be scattered throughout:

- Pages.
- Route Handlers.
- Server Actions.
- Business services.

This makes database changes difficult.

With a repository:

```
Route Handler / Server Action
              |
              v
         Service Layer
              |
              v
        Repository Layer
              |
              v
           Database
```

### **7.2 Example without repository**

```
export async function GET() {
  const products = await db.product.findMany({
    where: {
      isActive: true,
    },
  });

  return Response.json(products);
}
```

This works, but if every endpoint writes its own queries, database logic becomes scattered.

### **7.3 Example with repository**

`product.repository.ts`:

```
import "server-only";
import { db } from "@/lib/db";

export async function findActiveProducts() {
  return db.product.findMany({
    where: {
      isActive: true,
    },
  });
}

export async function findProductById(id: string) {
  return db.product.findUnique({
    where: { id },
  });
}
```

`product.service.ts`:

```
import {
  findActiveProducts,
  findProductById,
} from "../repositories/product.repository";

export async function getProducts() {
  return findActiveProducts();
}

export async function getProduct(id: string) {
  const product = await findProductById(id);

  if (!product) {
    throw new Error("Product not found");
  }

  return product;
}
```

`app/api/products/route.ts`:

```
import { getProducts } from "@/modules/products/services/product.service";

export async function GET() {
  const products = await getProducts();

  return Response.json(products);
}
```

### **7.4 What is the service layer?**

The service layer implements or coordinates application business operations.

For example:

```
Product Repository
   ├── findProducts()
   ├── findProductById()
   └── updateProduct()

Product Service
   ├── getProducts()
   ├── getProductDetails()
   ├── updateProductStock()
   └── deactivateProduct()
```

The repository answers:

"How do I retrieve or store this data?"

The service answers:

"What should the application do?"

### **7.5 Repository vs service**

| Repository                   | Service                              |
| ---------------------------- | ------------------------------------ |
| Data access                  | Business operations                  |
| Database queries             | Business rules                       |
| CRUD operations              | Coordinates workflows                |
| Knows persistence details    | Uses repositories and other services |
| Example: `findProductById()` | Example: `createOrder()`             |

**Important:** Do not create unnecessary abstraction layers for a tiny application. Introduce them when they make the system easier to understand, test, and maintain.

## **8. Database architecture and connection management**

Database architecture becomes increasingly important as application traffic grows.

### **8.1 Application-to-database connection**

A basic architecture:

```
             Next.js Application
                      |
                      v
                Database Client
                      |
                      v
                 Connection
                      |
                      v
                  Database
```

The application uses a database client to execute queries.

### **8.2 Why should you reuse database connections?**

Consider this code:

```
export async function getUsers() {
  const client = await connectToDatabase();

  return client.db().collection("users").find().toArray();
}
```

If your connection function creates a brand-new connection every time, this can create unnecessary connection overhead.

Instead, reuse a client connection where appropriate.

Example MongoDB connection module:

```
// lib/mongodb.ts

import "server-only";

import { MongoClient } from "mongodb";

const uri = process.env.MONGODB_URI;

if (!uri) {
  throw new Error("MONGODB_URI is missing");
}

const options = {};

declare global {
  // eslint-disable-next-line no-var
  var mongoClientPromise: Promise<MongoClient> | undefined;
}

const clientPromise =
  global.mongoClientPromise ??
  new MongoClient(uri, options).connect();

if (process.env.NODE_ENV !== "production") {
  global.mongoClientPromise = clientPromise;
}

export default clientPromise;
```

Usage:

```
import clientPromise from "@/lib/mongodb";

export async function getUsers() {
  const client = await clientPromise;

  const db = client.db("myapp");

  return db.collection("users").find().toArray();
}
```

This is a common development pattern for avoiding repeated client creation during hot reload.

In production, the module-level client is reused within each long-lived Node.js process. In serverless deployments, separate execution environments may each maintain their own client and connection pool.

### **8.3 What is a connection pool?**

A connection pool is a collection of reusable database connections.

Instead of establishing a new connection for every request, the application borrows an available connection from the pool and returns it after the operation.

[Effective API Optimization Techniques for Modern Applications | by Code Wiz | Javarevisited | Medium](https://images.openai.com/static-rsc-4/n3jTiQaGNibMgekod9CIqoHdZbpzAmB4CzPosjz5NZIZtcWTEO9WcClaGyEAVIRdh1FI_NJHqHCmMdY-eAOodDDY84ED-ODY2GPSeyQ3gHfGLTPglPOGKYv9J_N37dbX4w6og-xEDl0gTbgDiSs7ojL8ntKmqVxR5r8PqBJ-jvY?purpose=inline)

[medium.com](https://medium.com/javarevisited/effective-api-optimization-techniques-for-modern-applications-5363bd8149ff?utm_source=chatgpt.com)

Advantages:

- Reduces connection creation overhead.
- Reuses existing connections.
- Helps control concurrent database access.
- Improves efficiency under normal workloads.

### **8.4 Connection pool sizing**

Suppose:

- Application instance A has a maximum pool size of 20.
- Application instance B has a maximum pool size of 20.
- Application instance C has a maximum pool size of 20.

The total potential database connections could be 60.

If you increase application instances without considering the database's connection limit, you can overload the database.

Plan connection pools based on:

- Number of application instances.
- Number of database clients per instance.
- Expected concurrency.
- Database connection limits.
- Other applications using the same database.

### **8.5 Database indexing**

Indexes help databases find records efficiently.

Example MongoDB index:

```
db.products.createIndex({ category: 1 });
```

This can help queries that filter by category.

Example compound index:

```
db.orders.createIndex({
  userId: 1,
  createdAt: -1
});
```

This can help queries that retrieve a user's orders ordered by creation time, depending on the actual query and data distribution.

**Important:** Indexes are not free. They consume storage and add overhead to writes. Create indexes based on actual query patterns and inspect query plans.

### **8.6 Read replicas**

A read replica is a secondary database instance that receives replicated data from the primary database and can serve read operations.

```
              Application
                   |
            +------+------+
            |             |
            v             v
        Write Query    Read Query
            |             |
            v             v
       Primary DB     Read Replica
            |             ^
            +--- Replication
```

Benefits:

- Offloads read traffic from the primary.
- Can increase read capacity.
- Helps support read-heavy applications.

Limitations:

- Replication can lag.
- Reads may return slightly stale data.
- Writes still go to the primary in a typical setup.
- More infrastructure and operational complexity.

## **9. Redis and distributed caching**

### **9.1 What is Redis?**

**Redis**

An in-memory data store commonly used for caching, sessions, queues, and rate limiting.

[Official Redis documentation](https://redis.io/docs/latest/?utm_source=chatgpt.com)

Redis stores data in memory, making many read and write operations very fast.

Common use cases:

- Caching.
- Session storage.
- Rate limiting.
- Job queues.
- Temporary data.
- Distributed coordination.

### **9.2 Why do we need Redis?**

Suppose your product page executes this database query on every request:

```
const products = await db.product.findMany({
  where: {
    isActive: true,
  },
});
```

If 10,000 users repeatedly request the same product list, the database may perform the same work thousands of times.

You can cache the result in Redis.

```
             User Request
                   |
                   v
              Next.js App
                   |
                   v
               Redis GET
                   |
            +------+------+
            |             |
          Cache Hit     Cache Miss
            |             |
            v             v
        Return Data   Query Database
                          |
                          v
                     Store in Redis
                          |
                          v
                      Return Data
```

### **9.3 Cache-aside pattern**

Cache-aside is a common caching strategy.

Workflow:

1. Application checks Redis.
2. If the key exists, return cached data.
3. If the key does not exist, query the database.
4. Store the result in Redis with an expiration time.
5. Return the result.

Example using `ioredis`:

```
import "server-only";

import Redis from "ioredis";
import { db } from "@/lib/db";

const redis = new Redis(process.env.REDIS_URL!);

export async function getProducts() {
  const cacheKey = "products:active";

  const cached = await redis.get(cacheKey);

  if (cached) {
    return JSON.parse(cached);
  }

  const products = await db.product.findMany({
    where: {
      isActive: true,
    },
  });

  await redis.set(
    cacheKey,
    JSON.stringify(products),
    "EX",
    300
  );

  return products;
}
```

Here, `EX 300` means the key expires after 300 seconds, or five minutes.

For a real application, also consider Redis client lifecycle, connection error handling, serialization, and what happens when Redis is unavailable.

### **9.4 Cache invalidation**

Suppose a product's price changes.

The old cached product information may still exist.

You need to invalidate or update the relevant cache key.

```
await db.product.update({
  where: { id: productId },
  data: { price: newPrice },
});

await redis.del("products:active");
```

This is a basic invalidation approach.

More advanced systems may use:

- Versioned cache keys.
- Tag-based invalidation.
- Event-driven invalidation.
- Short TTLs.
- Cache warming.

### **9.5 What is distributed caching?**

A local in-memory cache belongs to one application instance.

Imagine three application instances:

```
        Load Balancer
              |
      +-------+-------+
      |       |       |
      v       v       v
    App A   App B   App C
      |       |       |
   Local    Local    Local
   Cache    Cache    Cache
```

Each instance may have a different cached value.

A shared Redis instance provides a common cache:

```
        Load Balancer
              |
      +-------+-------+
      |       |       |
      v       v       v
    App A   App B   App C
      |       |       |
      +-------+-------+
              |
              v
             Redis
              |
              v
           Database
```

This is particularly useful when multiple instances need consistent access to shared cached data.

Redis cache-aside guidance also discusses TTLs, cache invalidation, and cache stampedes.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs



### **9.6 Redis vs Next.js caching**

These are different mechanisms.

| Feature                 | Next.js caching                              | Redis                                                   |
| ----------------------- | -------------------------------------------- | ------------------------------------------------------- |
| Main purpose            | Cache Next.js data and rendered output       | General-purpose shared data store                       |
| Integration             | Built into Next.js                           | External service                                        |
| Storage                 | Depends on cache handler and deployment      | Redis server                                            |
| Shared across instances | Requires suitable shared cache configuration | Yes, when instances use the same Redis deployment       |
| Common uses             | Cached data, route output, revalidation      | Sessions, rate limits, jobs, arbitrary application data |

Next.js Cache Components can use custom cache handlers. In a multi-instance deployment, the default local cache is not automatically shared.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



**Important:** You do not need Redis for every Next.js project. Introduce it when shared caching, distributed sessions, queues, or other requirements justify the operational cost.

## **10. Background jobs and message queues**

### **10.1 What is a background job?**

A background job is a task that runs outside the immediate user request-response workflow.

Examples:

- Sending emails.
- Generating PDF invoices.
- Processing uploaded files.
- Sending bulk WhatsApp messages.
- Importing Excel records.
- Generating reports.
- Processing images.
- Synchronizing external APIs.

Consider a user who uploads 500 resumes.

A poor implementation processes all 500 files inside a single HTTP request.

```
User uploads 500 resumes
          |
          v
    HTTP Request
          |
          v
  Process all resumes
          |
          v
     Return response
```

Problems:

- The request may take too long.
- The server may time out.
- The user must wait.
- A single failure may interrupt the entire operation.

Instead, use background processing.

### **10.2 What is a message queue?**

A message queue stores tasks until workers are ready to process them.

[The Power of Queue Architecture | Casey Mershon](https://images.openai.com/static-rsc-4/U1LuBd6EA49SOgbxsQ_nyXyGHV0OVfpWuNr6p9mIcuHosZ27w9bLlmbaLnOpxBfhmAWHAZkApesnUgtlbPNSme1E8fSDLcCFvcoSMjeu1Mp8e_Dta-RWWpyLdkSCiUTt3x132whMwdRwpoG7W8yKn8fq7ET1i0X-4dFw6_ofybU?purpose=inline)

[caseymershon.co](https://www.caseymershon.co/blog/the-power-of-queue-architecture?utm_source=chatgpt.com)

There are three important components:

**1. Producer**

The producer creates jobs and adds them to a queue.

**2. Queue**

The queue stores jobs until a worker processes them.

**3. Worker**

The worker retrieves jobs and executes the corresponding task.

Example:

```
        Next.js Application
                |
                v
             Producer
                |
                v
          Redis Job Queue
                |
        +-------+-------+
        |               |
        v               v
     Worker 1        Worker 2
        |               |
        v               v
   Process File    Send Email
```

### **10.3 What is BullMQ?**

[BullMQ Solution Partner](https://images.openai.com/static-rsc-4/DORX9TuJQ1U1wDhhCl0dco78GUix1v56jCep6CMU1hNDg4BqjFWQ4m512e6zczj1mnAC7t00T-wnhucSTkh7YnSqQGw0IW2uC58Xa7uClJNNeqCIaC3StbtMDVOrhxp9gWyZHy9Pkk85_B2DLJtp3RGgK5wP6RBQGpuKKPfXn7c?purpose=inline)

**BullMQ**

A TypeScript-friendly Node.js queue library commonly used with Redis.

[BullMQ documentation](https://docs.bullmq.io/?utm_source=chatgpt.com)

BullMQ supports:

- Job queues.
- Multiple workers.
- Job priorities.
- Delayed jobs.
- Retries.
- Scheduled jobs.
- Concurrency.
- Rate limiting.
- Job events.

[image](https://www.google.com/s2/favicons?domain=https://docs.bullmq.io\&sz=32)

BullMQ+1

### **10.4 Install BullMQ**

```
npm install bullmq ioredis
```

Assume Redis is running and you have configured:

```
REDIS_URL=redis://localhost:6379
```

### **10.5 Create a queue**

`lib/queues/email.queue.ts`:

```
import "server-only";

import { Queue } from "bullmq";
import IORedis from "ioredis";

const connection = new IORedis(
  process.env.REDIS_URL!,
  {
    maxRetriesPerRequest: null,
  }
);

export const emailQueue = new Queue("email-queue", {
  connection,
});
```

### **10.6 Add jobs to the queue**

`modules/notifications/email.service.ts`:

```
import "server-only";

import { emailQueue } from "@/lib/queues/email.queue";

export async function queueWelcomeEmail(
  email: string,
  name: string
) {
  await emailQueue.add(
    "welcome-email",
    {
      email,
      name,
    },
    {
      attempts: 3,
      backoff: {
        type: "exponential",
        delay: 1000,
      },
    }
  );
}
```

Instead of sending the email immediately, the application creates a job.

### **10.7 Create a worker**

A worker processes jobs from the queue.

`workers/email.worker.ts`:

```
import "server-only";

import { Worker } from "bullmq";
import IORedis from "ioredis";

const connection = new IORedis(
  process.env.REDIS_URL!,
  {
    maxRetriesPerRequest: null,
  }
);

const worker = new Worker(
  "email-queue",
  async (job) => {
    if (job.name === "welcome-email") {
      const { email, name } = job.data;

      console.log(`Sending welcome email to ${email}`);

      // Call your actual email provider here.
      // await sendEmail(email, name);

      return {
        success: true,
      };
    }
  },
  {
    connection,
    concurrency: 5,
  }
);

worker.on("completed", (job) => {
  console.log(`Job ${job.id} completed`);
});

worker.on("failed", (job, error) => {
  console.error(`Job ${job?.id} failed`, error);
});
```

This worker must run as a separate long-running process, not simply as a file imported by a Server Component.

Example script:

```
{
  "scripts": {
    "worker:email": "tsx workers/email.worker.ts"
  }
}
```

Run:

```
npm run worker:email
```

In production, deploy the worker as its own managed process or service.

### **10.8 Job lifecycle**

A typical BullMQ job passes through these states:

```
Waiting
   |
   v
Active
   |
   +-------> Completed
   |
   +-------> Failed
                  |
                  v
               Retrying
                  |
                  v
                Active
```

BullMQ also supports delayed and prioritized jobs.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.bullmq.io\&sz=32)

BullMQ+1



### **10.9 What is retry with exponential backoff?**

Suppose an email provider temporarily becomes unavailable.

Instead of immediately abandoning the task, the queue can retry it.

Exponential backoff increases the delay between attempts.

For example:

| Attempt      | Approximate delay |
| ------------ | ----------------- |
| First retry  | 1 second          |
| Second retry | 2 seconds         |
| Third retry  | 4 seconds         |

Example:

```
await emailQueue.add(
  "send-email",
  {
    email: "customer@example.com",
  },
  {
    attempts: 4,
    backoff: {
      type: "exponential",
      delay: 1000,
    },
  }
);
```

The exact retry schedule depends on the configured strategy.

### **10.10 Important background job concepts**

**Idempotency**

A job is idempotent when repeating the same operation does not cause an unintended duplicate effect.

For example, if a payment confirmation job retries, it must not charge the customer again.

**Dead-letter handling**

Jobs that repeatedly fail should be retained or moved into a failure-handling workflow so they can be investigated.

**Concurrency**

Concurrency controls how many jobs a worker can process at the same time.

**Job monitoring**

Monitor:

- Waiting jobs.
- Active jobs.
- Completed jobs.
- Failed jobs.
- Retry counts.
- Processing duration.

BullMQ provides worker concurrency, job retries, and job lifecycle management.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.bullmq.io\&sz=32)

BullMQ+1



### **10.11 When should you use background jobs?**

| Task                        | Background job? |
| --------------------------- | --------------- |
| Send welcome email          | Yes             |
| Generate a large PDF report | Usually         |
| Import 500 Excel records    | Yes             |
| Resize thousands of images  | Yes             |
| Retrieve a single product   | Usually no      |
| Validate a simple form      | Usually no      |
| Process a large video       | Yes             |

**Important:** A queue does not automatically guarantee exactly-once processing. Design jobs to tolerate retries, duplicate delivery, and worker crashes.

## **11. API rate limiting and protection**

### **11.1 What is rate limiting?**

Rate limiting restricts how many requests a user, IP address, API key, or other identity can make within a specified period.

Example:

A login endpoint allows:

- 5 attempts per minute per relevant identity.
- Further attempts receive HTTP 429.

Without rate limiting, attackers may:

- Brute-force passwords.
- Abuse expensive endpoints.
- Generate excessive API traffic.
- Exhaust database connections.
- Increase infrastructure costs.

### **11.2 How does rate limiting work?**

```
            Incoming Request
                    |
                    v
             Rate Limiter
                    |
              +-----+-----+
              |           |
           Allowed      Limited
              |           |
              v           v
          Process API   HTTP 429
```

### **11.3 Common rate-limiting algorithms**

| Algorithm      | Explanation                                         |
| -------------- | --------------------------------------------------- |
| Fixed window   | Limits requests within fixed time intervals         |
| Sliding window | Calculates request limits over a moving time period |
| Token bucket   | Requests consume tokens that refill over time       |
| Leaky bucket   | Processes requests at a controlled rate             |

### **11.4 Example using Redis**

The following example demonstrates a basic fixed-window counter.

```
import "server-only";

import Redis from "ioredis";

const redis = new Redis(process.env.REDIS_URL!);

export async function checkRateLimit(
  identifier: string
) {
  const key = `rate-limit:${identifier}`;

  const count = await redis.incr(key);

  if (count === 1) {
    await redis.expire(key, 60);
  }

  return {
    allowed: count <= 5,
    remaining: Math.max(0, 5 - count),
  };
}
```

This example permits five requests during a Redis key's 60-second lifetime.

For production, use an atomic operation or Lua script for the increment-and-expiration workflow, handle Redis failures deliberately, and consider a sliding-window or token-bucket strategy where appropriate.

### **11.5 Apply rate limiting in a Route Handler**

`app/api/login/route.ts`:

```
import { NextRequest, NextResponse } from "next/server";
import { checkRateLimit } from "@/lib/rate-limit";

export async function POST(request: NextRequest) {
  // Illustrative only: use a trusted client identity.
  const identifier = "login-endpoint";

  const result = await checkRateLimit(identifier);

  if (!result.allowed) {
    return NextResponse.json(
      {
        error: "Too many requests",
      },
      {
        status: 429,
        headers: {
          "Retry-After": "60",
        },
      }
    );
  }

  // Continue with login validation and authentication.
  return NextResponse.json({
    success: true,
  });
}
```

In a real application, the identifier should be based on an appropriate trusted signal, such as an authenticated account ID, API key, or correctly configured client IP. Do not blindly trust a user-supplied `X-Forwarded-For` header.

### **11.6 Which endpoints need rate limiting?**

- Login.
- Registration.
- Password reset.
- OTP generation.
- Public search APIs.
- File upload.
- Payment initiation.
- Expensive report generation.
- Public webhook endpoints where appropriate.

Rate limiting should be combined with authentication, authorization, input validation, request-size limits, and monitoring.

The OWASP API Security guidance identifies unrestricted resource consumption as an important API security risk. [OWASP – Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/?utm_source=chatgpt.com).

## **12. Scalability and load balancing**

### **12.1 What is scalability?**

Scalability is the ability of a system to handle increasing workloads by adding or allocating resources.

Suppose your application initially serves 100 users per day.

After a successful launch, it serves 100,000 users per day.

Your architecture should be able to handle the increased workload without unacceptable performance degradation.

There are two primary scaling approaches.

### **12.2 Vertical scaling**

Vertical scaling means increasing the resources of an existing server.

For example:

[The Ultimate Guide to Scaling Your Data Pipelines in Data Engineering | Wednesday Solutions](https://images.openai.com/static-rsc-4/E2rPoT3PUSJterbf_ZWrtAKfze5NL7nQ7JjXeEwm4h1ErA-rWqA_2VJpBRnJQ7BRveHvy7fd6Qgb7_-BqFAAdzETqaWUwTv2Yi1N3ODPs2qLmMSaTSeD2wzdbYlSSg150-DZm_G1rjg0i8fvndoZnqEDoCKA2VMnT61fHQEBTb0?purpose=inline)

[wednesday.is](https://www.wednesday.is/writing-articles/the-ultimate-guide-to-scaling-your-data-pipelines-in-data-engineering?utm_source=chatgpt.com)

| Before         | After         |
| -------------- | ------------- |
| 2 CPU cores    | 8 CPU cores   |
| 4 GB RAM       | 32 GB RAM     |
| Smaller server | Larger server |

Advantages:

- Relatively simple.
- No need to coordinate multiple application instances.
- Can be effective for moderate workloads.

Disadvantages:

- Hardware has limits.
- Larger servers can become expensive.
- A single server may remain a failure point.

### **12.3 Horizontal scaling**

Horizontal scaling means adding more application instances.

[API Development Series Part 13: Performance & Rate Limiting - Wasil Zafar](https://images.openai.com/static-rsc-4/dDLM6g6DeqyvR_DfZL152EaxKvGcnTHj6luMUvwjQCES-t2o39vUwh6G2AyM4cSZqpwKhwEYX0zguXnoaAq6tcRn5pD5aIL29lwtSiapY00-TdAzdadkQeHUyNIZKoJPOtpTd-oKu5XBRU9Jpbapaksu2WcTA6M6C3njshckT4c?purpose=inline)

[wasilzafar.com](https://www.wasilzafar.com/pages/series/api-development/api-dev-performance-rate-limiting.html?utm_source=chatgpt.com)

Example:

```
                Users
                  |
                  v
             Load Balancer
                  |
       +----------+----------+
       |          |          |
       v          v          v
    Server A   Server B   Server C
       |          |          |
       +----------+----------+
                  |
                  v
              Database
```

Advantages:

- Can handle more concurrent traffic.
- Allows independent instance replacement.
- Supports redundancy.
- Can scale according to traffic.

Disadvantages:

- More infrastructure.
- Shared state must be managed.
- Database connections must be controlled.
- Deployment and monitoring become more complex.

### **12.4 Vertical vs horizontal scaling**

| Feature        | Vertical                | Horizontal                      |
| -------------- | ----------------------- | ------------------------------- |
| Method         | Upgrade existing server | Add more servers                |
| Complexity     | Lower initially         | Higher                          |
| Hardware limit | Yes                     | Less constrained by one machine |
| Redundancy     | Limited                 | Can provide multiple instances  |
| Cost           | Depends on server size  | Depends on instance count       |
| Typical use    | Small to medium systems | Growing, high-traffic systems   |

### **12.5 What is a load balancer?**

A load balancer distributes incoming traffic across multiple application servers.

For example:

```
                1,000 Requests
                       |
                       v
                 Load Balancer
                       |
           +-----------+-----------+
           |           |           |
           v           v           v
       Server A    Server B    Server C
       334 req     333 req     333 req
```

These request numbers are illustrative. Actual distribution depends on the load-balancing algorithm and server health.

Common algorithms include:

- Round robin.
- Least connections.
- Weighted round robin.
- IP hash.

### **12.6 What is a reverse proxy?**

A reverse proxy sits between clients and backend servers.

Examples include Nginx and managed cloud load balancers.

```
Browser
   |
   v
Nginx Reverse Proxy
   |
   v
Next.js Server
   |
   v
Database
```

A reverse proxy can provide:

- TLS termination.
- Request routing.
- Compression.
- Payload size limits.
- Rate limiting.
- Protection against slow connections.
- Static asset handling.

For self-hosted Next.js, the official guidance recommends using a reverse proxy such as Nginx in front of the application server.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **12.7 Stateless application servers**

A stateless application server does not depend on its local memory or local disk to retain important user state between requests.

Consider:

```
const sessions = new Map<string, string>();
```

This is unsuitable as the only session store in a horizontally scaled production application.

Why?

- Server A stores a session.
- The next request reaches Server B.
- Server B does not know about Server A's in-memory session.

A better approach is shared session storage, such as:

- Database-backed sessions.
- Redis-backed sessions.
- Signed or encrypted cookies, where appropriate.

The application instances can then process requests without relying on a particular server's local memory.

## **13. Scaling Next.js across multiple instances**

Running one Next.js instance is relatively straightforward.

Running multiple instances introduces additional concerns.

### **13.1 Multi-instance deployment**

```
               CDN / Load Balancer
                       |
             +---------+---------+
             |         |         |
             v         v         v
           Next A    Next B    Next C
             |         |         |
             +---------+---------+
                       |
               Shared Services
              /        |        \
             v         v         v
          Database   Redis    Object Storage
```

You should consider:

1. Shared sessions or stateless authentication.
2. Shared cache storage.
3. Consistent application builds.
4. Server Action encryption keys.
5. Cache tag coordination.
6. Database connection pooling.
7. Centralized logs and monitoring.

### **13.2 Why is a shared cache important?**

By default, Next.js instances have their own local cache.

If one instance invalidates a cached value, other instances may continue serving stale data.

For multi-instance deployments, configure a shared cache handler and coordinate cache tag invalidation. Next.js documents custom cache handlers and the multi-instance deployment requirements.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **13.3 Shared Server Action encryption key**

Next.js uses an encryption key for Server Function closure variables.

When multiple instances serve the same build, they must use a consistent encryption key.

The relevant environment variable is:

```
NEXT_SERVER_ACTIONS_ENCRYPTION_KEY=your-base64-encoded-key
```

Generate a secure key and configure it consistently across the instances that serve the same build.

Do not use a random placeholder in production. The key must be base64 encoded and have a valid AES key length.

### **13.4 Consistent builds**

Multiple instances should serve the same application build.

Otherwise, users may encounter problems when requests move between instances running different versions.

Next.js supports deployment identifiers for version-skew protection during rolling deployments.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **13.5 Next.js deployment requirements**

A standard Next.js application can run on a Node.js server using `next start`.

Additional infrastructure is needed when you want features such as shared caches, coordinated invalidation, or multiple-instance consistency.

Reference: [Next.js – Deploying to Platforms](https://nextjs.org/docs/app/guides/deploying-to-platforms?utm_source=chatgpt.com).

## **14. Reliability and fault tolerance**

Scalable architecture is not only about handling more users. It must also handle failures.

### **14.1 What is fault tolerance?**

Fault tolerance is the ability of a system to continue operating, possibly with reduced functionality, when some components fail.

Example:

Your application depends on:

- MongoDB.
- Redis.
- Payment gateway.
- Email provider.

Suppose the email provider becomes unavailable.

Should customers be prevented from placing orders just because an email cannot be sent?

Usually not.

You can save the order successfully and queue the confirmation email for later processing.

### **14.2 Graceful degradation**

Graceful degradation means that when an optional component fails, the core application can continue working.

Example:

```
Customer places order
         |
         v
   Save order to DB
         |
         v
    Order created
         |
         v
  Queue confirmation email
         |
    +----+----+
    |         |
 Success     Failure
    |         |
    v         v
 Completed   Retry later
```

The order should not be lost merely because an email service is temporarily unavailable.

### **14.3 Timeouts**

A timeout limits how long an application waits for an external operation.

Example:

```
const response = await fetch(
  "https://api.example.com/data",
  {
    signal: AbortSignal.timeout(5000),
  }
);
```

The request is aborted if it exceeds the five-second timeout.

Always consider timeouts for:

- External API calls.
- Payment gateways.
- Database operations.
- File uploads.
- Long-running tasks.

### **14.4 Retries**

Retries can help recover from temporary failures.

However, retrying every failure is dangerous.

For example:

- A temporary network failure may be retryable.
- An invalid password should not be retried automatically.
- An invalid payment request should not be repeatedly submitted.

Use bounded retries, backoff, and idempotency where appropriate.

### **14.5 Circuit breaker pattern**

A circuit breaker prevents repeated requests to a failing dependency.

It typically has three states:

| State     | Meaning                           |
| --------- | --------------------------------- |
| Closed    | Requests are allowed              |
| Open      | Requests are blocked temporarily  |
| Half-open | A limited test request is allowed |

Example:

```
       Closed
          |
   Repeated failures
          |
          v
        Open
          |
     Wait period
          |
          v
      Half-open
        /    \
   Success   Failure
      |         |
      v         v
    Closed    Open
```

This can prevent an unhealthy external service from continuously consuming your application's resources.

## **15. Logging, monitoring, and observability**

### **15.1 What is logging?**

Logging records events that occur inside an application.

Example:

```
console.log("Order created");
console.error("Payment failed");
```

This is useful during development, but production applications need structured and searchable logs.

Example:

```
console.info({
  event: "order_created",
  orderId: "ORD-123",
  userId: "USER-456",
  timestamp: new Date().toISOString(),
});
```

Avoid logging:

- Passwords.
- Access tokens.
- Session cookies.
- Payment card details.
- Sensitive personal information.

### **15.2 What is monitoring?**

Monitoring tracks the health and behavior of your application over time.

Common metrics:

| Metric               | Meaning                        |
| -------------------- | ------------------------------ |
| CPU usage            | Processor utilization          |
| Memory usage         | RAM consumption                |
| Request rate         | Requests received per second   |
| Error rate           | Percentage of failed requests  |
| Latency              | Time taken to process requests |
| Database connections | Active database connections    |
| Queue depth          | Number of pending jobs         |

### **15.3 What is observability?**

Observability helps you understand the internal state of a system through its external outputs.

Three important pillars are:

**Logs**

What happened?

**Metrics**

How often and how much?

**Traces**

Where did a request spend its time?

### **15.4 Distributed tracing**

Suppose an order request passes through:

```
Next.js
   |
   v
Order Service
   |
   v
Payment Service
   |
   v
Payment Provider
   |
   v
Database
```

If the request takes 8 seconds, you need to identify which component is slow.

Distributed tracing records the journey of a request across services.

Each operation can be represented by a span, and related spans share a trace ID.

This is particularly useful in microservices architectures.

### **15.5 Useful monitoring tools**

| Tool          | Common use                                |
| ------------- | ----------------------------------------- |
| OpenTelemetry | Instrumentation and telemetry             |
| Prometheus    | Metrics collection                        |
| Grafana       | Dashboards and visualization              |
| Sentry        | Error tracking                            |
| PM2           | Node.js process management and monitoring |
| Winston       | Structured application logging            |

Official resources:

- [OpenTelemetry](https://opentelemetry.io/docs/?utm_source=chatgpt.com)
- [Prometheus](https://prometheus.io/docs/?utm_source=chatgpt.com)
- [Grafana](https://grafana.com/docs/?utm_source=chatgpt.com)
- [Sentry](https://docs.sentry.io/?utm_source=chatgpt.com)

## **16. Designing a production-ready e-commerce architecture**

Now let's combine the concepts from this phase.

Imagine we are building an e-commerce platform with:

- Customer website.
- Admin dashboard.
- Product catalog.
- Shopping cart.
- Orders.
- Payments.
- Email notifications.
- Reports.

### **16.1 Recommended starting architecture**

For a growing application, start with a modular monolith unless you have a clear reason to split services.

[Designing a Secure, Async-First Modular Monolith to Accelerate Developer–Data Scientist Collaboration at Equinor - Bouvet Norge](https://images.openai.com/static-rsc-4/2EqfF-O4nmymKpkcHRE0_XcCezGXLlhVxXCf9dP0jSAGXrbDECJdENljqhmv0r7IcpW3yxeQdnFAveeQxZxmAUKQdOCL_B1qCaZeWlqZvjO2uCVDk81ItbDQaLiqMxsjzOchKjWO2tEd_0im_DobBrDpFWVOLG_H4qUSvcOQuM8?purpose=inline)

[bouvet.no](https://www.bouvet.no/bouvet-deler/designing-a-secure-async-first-modular-monolith-to-accelerate-developerdata-scientist-collaboration-at-equinor?utm_source=chatgpt.com)

```
                 Customers / Admins
                         |
                         v
                  Next.js Application
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
          Products     Orders      Users
             |           |           |
             +-----------+-----------+
                         |
                   Service Layer
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
         MongoDB       Redis     Payment API
             |           |
             |           v
             |       Job Queue
             |           |
             |           v
             |         Worker
             |           |
             |           v
             |       Email Provider
             |
             v
        Persistent Data
```

### **16.2 Suggested technology stack**

| Layer           | Technology                                         |
| --------------- | -------------------------------------------------- |
| Frontend        | Next.js 16, React, TypeScript                      |
| UI              | Tailwind CSS                                       |
| Backend         | Next.js server-side modules                        |
| HTTP APIs       | Route Handlers                                     |
| Mutations       | Server Actions where appropriate                   |
| Database        | MongoDB or PostgreSQL                              |
| Database access | MongoDB driver or Prisma/Drizzle                   |
| Cache           | Redis when justified                               |
| Background jobs | BullMQ                                             |
| Authentication  | Auth.js, Better Auth, or another suitable provider |
| File storage    | S3 or Cloudinary                                   |
| Payment         | A payment provider                                 |
| Deployment      | Vercel or Node.js VPS                              |
| Reverse proxy   | Nginx for self-hosting                             |
| Monitoring      | Sentry and OpenTelemetry                           |

### **16.3 Suggested folder structure**

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
│   │   ├── dashboard/
│   │   └── products/
│   │
│   ├── api/
│   │   ├── products/
│   │   ├── orders/
│   │   └── webhooks/
│   │
│   └── layout.tsx
│
├── modules/
│   ├── products/
│   │   ├── components/
│   │   ├── services/
│   │   ├── repositories/
│   │   ├── schemas/
│   │   ├── domain/
│   │   └── types/
│   │
│   ├── orders/
│   │   ├── components/
│   │   ├── services/
│   │   ├── repositories/
│   │   ├── schemas/
│   │   ├── domain/
│   │   └── types/
│   │
│   ├── users/
│   ├── payments/
│   └── notifications/
│
├── infrastructure/
│   ├── database/
│   ├── redis/
│   ├── storage/
│   └── payments/
│
├── lib/
│   ├── auth.ts
│   ├── env.ts
│   └── logger.ts
│
└── workers/
    ├── email.worker.ts
    └── report.worker.ts
```

This structure separates route handling, business modules, infrastructure, and background workers.

### **16.4 Important architectural rules**

1. UI components should not contain database queries.
2. Database access should remain server-only.
3. Business rules should be separated from HTTP handling.
4. Validate all incoming requests.
5. Check authorization on the server.
6. Do not expose secrets to Client Components.
7. Use transactions for operations that require atomicity.
8. Use queues for long-running tasks.
9. Use caching where repeated reads justify it.
10. Monitor errors, latency, and resource usage.
11. Keep module dependencies clear.
12. Avoid introducing microservices without a concrete requirement.

## **17. Common architectural mistakes**

| Mistake                                            | Problem                             | Better approach                              |
| -------------------------------------------------- | ----------------------------------- | -------------------------------------------- |
| Putting everything in `page.tsx`                   | Difficult to maintain               | Separate UI, services, and repositories      |
| Mixing database queries with business rules        | Tight coupling                      | Use repository and service layers            |
| Creating microservices too early                   | Unnecessary infrastructure          | Start with a modular monolith                |
| Creating a new DB connection per request           | Connection overhead                 | Reuse clients and configure pools            |
| Storing sessions in local memory                   | Breaks across instances             | Use shared session storage                   |
| Using local cache across multiple instances        | Inconsistent cached data            | Use coordinated shared caching               |
| Processing large tasks in HTTP requests            | Timeouts and poor responsiveness    | Use background queues                        |
| No rate limiting                                   | API abuse and resource exhaustion   | Apply appropriate rate limits                |
| No retry strategy                                  | Temporary failures become permanent | Use bounded retries and backoff              |
| No idempotency                                     | Duplicate operations                | Use idempotency keys and safe job processing |
| Logging sensitive information                      | Security and privacy risks          | Use structured, sanitized logs               |
| No database indexes                                | Slow queries                        | Analyze queries and add appropriate indexes  |
| No monitoring                                      | Problems go unnoticed               | Track logs, metrics, and traces              |
| Sharing internal service code with browser bundles | Security risk                       | Use server-only boundaries                   |

## **18. Interview questions and answers**

### **Basic-level questions**

**Q1. What is software architecture?**

Software architecture describes the structure of an application, its components, their responsibilities, and how they communicate.

**Q2. What is monolithic architecture?**

It is an architecture in which the main application components are developed and deployed as one unit.

**Q3. What is modular monolithic architecture?**

It is a single deployable application organized into independent business modules.

**Q4. What are microservices?**

Microservices divide an application into independently deployable services, each responsible for a specific business capability.

**Q5. What is separation of concerns?**

It is the principle of separating different responsibilities into different components or modules.

**Q6. What is a repository pattern?**

It is a design pattern that separates data access logic from business logic.

**Q7. What is a service layer?**

It contains application operations and business workflows, coordinating repositories and other dependencies.

**Q8. What is Redis?**

Redis is an in-memory data store used for caching, sessions, rate limiting, queues, and other low-latency data operations.

**Q9. What is a background job?**

A background job is a task processed separately from the immediate user request-response cycle.

**Q10. What is a message queue?**

A message queue stores tasks until workers retrieve and process them.

### **Intermediate-level questions**

**Q11. What is the difference between monolithic and microservices architecture?**

A monolith is deployed as one application, whereas microservices consist of independently deployable services that communicate through network or messaging mechanisms.

**Q12. What is the difference between vertical and horizontal scaling?**

Vertical scaling increases resources on one server. Horizontal scaling adds more servers or application instances.

**Q13. What is a load balancer?**

A load balancer distributes incoming requests across multiple servers.

**Q14. What is a database connection pool?**

It is a collection of reusable database connections that helps reduce connection overhead and control concurrent database access.

**Q15. What is cache-aside?**

It is a caching strategy in which an application checks the cache first, retrieves data from the database on a cache miss, and stores the result in the cache.

**Q16. What is rate limiting?**

Rate limiting restricts the number of requests an identity can make within a specific time period.

**Q17. What is idempotency?**

An operation is idempotent when repeating the same operation does not create unintended additional effects.

**Q18. What is a reverse proxy?**

A reverse proxy receives client requests and forwards them to backend servers. It can also provide TLS termination, routing, and request protection.

**Q19. What is graceful degradation?**

It means the application continues providing its essential functionality when an optional dependency becomes unavailable.

**Q20. What is a connection pool exhaustion problem?**

It occurs when all available database connections are occupied, causing new requests to wait or fail.

### **Advanced-level questions**

**Q21. When should you move from a monolith to microservices?**

When independent deployment, team ownership, workload scaling, or business-domain separation provides enough benefit to justify the additional distributed-system complexity.

**Q22. Why is Redis useful in horizontally scaled applications?**

It provides shared data storage that multiple application instances can access, avoiding dependence on each instance's local memory.

**Q23. How would you handle 100,000 users accessing an application?**

I would consider:

1. Load balancing and horizontal scaling.
2. CDN and static asset caching.
3. Database indexing and query optimization.
4. Connection pool management.
5. Redis caching.
6. Rate limiting.
7. Background processing.
8. Monitoring and autoscaling.
9. Database capacity and replication.
10. Load testing.

The actual design depends on concurrency, request patterns, database workload, and latency requirements—not just the total user count.

**Q24. Why should long-running tasks use a queue?**

Queues separate expensive or unreliable tasks from the user-facing request. They provide retries, scheduling, concurrency control, and independent worker execution.

**Q25. How do you maintain cache consistency across multiple Next.js instances?**

Use a shared cache backend and coordinate invalidation across instances. Next.js supports custom cache handlers for this purpose.

**Q26. What is a circuit breaker?**

It is a resilience pattern that temporarily stops calls to a failing dependency and later allows controlled test requests to determine whether it has recovered.

**Q27. What are the three pillars of observability?**

- Logs.
- Metrics.
- Traces.

**Q28. How would you prevent duplicate payment processing?**

Use provider-supported idempotency keys, verify payment status server-side, and make the payment workflow safe against retries and duplicate callbacks.

**Q29. What is the difference between a modular monolith and microservices?**

A modular monolith has clearly separated modules but one deployment unit. Microservices have independently deployable services with network or messaging boundaries.

**Q30. What architectural approach would you recommend for a new Next.js SaaS application?**

I would normally begin with a modular monolith, feature-based modules, a service layer, a data-access layer, and clear server/client boundaries. I would introduce Redis, background workers, and separate services when the actual requirements justify them.

## **19. Practical assignment – Build a scalable SaaS application architecture**

Major hands-on assignment

Build the architecture for a **Project Management SaaS Application**.

Think of a simplified application similar to Trello or Asana.

### **Application requirements**

Users should be able to:

- Register and log in.
- Create organizations.
- Create projects.
- Add team members.
- Create tasks.
- Assign tasks to users.
- Upload attachments.
- Receive email notifications.
- Generate project reports.
- View activity history.

### **Part A – Architecture**

- Use a modular monolithic architecture.
- Create separate modules for users, organizations, projects, tasks, and notifications.
- Separate presentation, application, domain, and infrastructure responsibilities.
- Create a clear folder structure.
- Define communication between modules.

### **Part B – Backend**

- Create repository functions.
- Create service functions.
- Implement Route Handlers.
- Implement request validation.
- Implement authorization.
- Implement appropriate database transactions.

### **Part C – Database**

- Design the database schema.
- Create user and organization relationships.
- Create project and task relationships.
- Add appropriate indexes.
- Implement database connection reuse.
- Consider connection pool limits.

### **Part D – Redis**

- Configure Redis.
- Cache frequently accessed project information.
- Implement cache expiration.
- Invalidate cached information after updates.
- Understand cache-aside.

### **Part E – Background jobs**

- Install BullMQ.
- Create an email queue.
- Create an email worker.
- Queue notification jobs.
- Implement retries.
- Handle failed jobs.

### **Part F – Security and scalability**

- Implement rate limiting.
- Implement secure session handling.
- Ensure server-side authorization.
- Avoid storing important state in local application memory.
- Understand horizontal scaling.
- Plan for shared caching.

### **Part G – Monitoring**

- Implement structured logging.
- Track API errors.
- Track request latency.
- Monitor database connections.
- Monitor background job failures.

## **20. Quick revision notes**

| Concept               | Remember                                          |
| --------------------- | ------------------------------------------------- |
| Software architecture | Structure of an application                       |
| Monolith              | One deployable application                        |
| Modular monolith      | One deployment, separate business modules         |
| Microservices         | Independently deployable services                 |
| Presentation layer    | UI and request handling                           |
| Application layer     | Coordinates use cases                             |
| Domain layer          | Core business rules                               |
| Infrastructure layer  | Database and external systems                     |
| Repository            | Data access                                       |
| Service               | Business operations                               |
| Connection pool       | Reusable DB connections                           |
| Database index        | Improves suitable query lookups                   |
| Read replica          | Secondary database for read traffic               |
| Redis                 | In-memory data store                              |
| Cache-aside           | Check cache, then database on a miss              |
| Distributed cache     | Shared cache across application instances         |
| Queue                 | Stores jobs for processing                        |
| Producer              | Creates jobs                                      |
| Worker                | Processes jobs                                    |
| BullMQ                | Node.js job queue                                 |
| Idempotency           | Safe repeated execution                           |
| Rate limiting         | Controls request frequency                        |
| Vertical scaling      | Increase server resources                         |
| Horizontal scaling    | Add more instances                                |
| Load balancer         | Distributes incoming requests                     |
| Reverse proxy         | Forwards requests to backend servers              |
| Stateless application | Does not depend on local request-to-request state |
| Circuit breaker       | Protects against failing dependencies             |
| Observability         | Logs, metrics, and traces                         |

## **21. Phase 24 completion checklist**

**Your learning progress**

# **0%**

0 of 22 completed

Understand monolithic architecture

Understand modular monolithic architecture

Understand microservices

Understand layered architecture

Understand frontend and backend separation

Understand repository pattern

Understand service layer

Understand database connection pooling

Understand database indexing and read replicas

Understand Redis

Implement cache-aside

Understand distributed caching

Understand background jobs and queues

Understand BullMQ producers and workers

Understand retries and idempotency

Understand API rate limiting

Understand vertical and horizontal scaling

Understand load balancing and reverse proxies

Understand multi-instance Next.js deployments

Understand fault tolerance and circuit breakers

Understand logging, metrics and distributed tracing

Design the Project Management SaaS architecture

Reset checklist

## **22. Official documentation and resources**

| Resource                                                                                                                       | What to study                                |
| ------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------- |
| [Next.js Backend for Frontend](https://nextjs.org/docs/app/guides/backend-for-frontend?utm_source=chatgpt.com)                 | Route Handlers and backend responsibilities  |
| [Next.js Self-Hosting](https://nextjs.org/docs/app/guides/self-hosting?utm_source=chatgpt.com)                                 | Multi-instance deployments and shared caches |
| [Next.js Cache Handlers](https://nextjs.org/docs/app/api-reference/config/next-config-js/cacheHandlers?utm_source=chatgpt.com) | External cache integration                   |
| [Redis Cache-Aside](https://redis.io/docs/latest/develop/use-cases/cache-aside/?utm_source=chatgpt.com)                        | Distributed caching                          |
| [BullMQ Introduction](https://docs.bullmq.io/guide/introduction?utm_source=chatgpt.com)                                        | Queues and workers                           |
| [BullMQ Retries](https://docs.bullmq.io/guide/retrying-failing-jobs?utm_source=chatgpt.com)                                    | Retry strategies                             |
| [BullMQ Concurrency](https://docs.bullmq.io/guide/workers/concurrency?utm_source=chatgpt.com)                                  | Worker concurrency                           |
| [Next.js Self-Hosting and Reverse Proxy](https://nextjs.org/docs/pages/guides/self-hosting?utm_source=chatgpt.com)             | Nginx and deployment                         |
| [OWASP API Security](https://owasp.org/www-project-api-security/?utm_source=chatgpt.com)                                       | API protection                               |

## **23. Next phase preview**

[イベント駆動開発: IoT企業のリアルタイムデータ処理 - okpy](https://images.openai.com/static-rsc-4/uhP6PRfElSKT60F1tZTom76ENSplJxjYTGW4BrdlBW-zQcP9jrOimjPT-dfd4P4m7qqd5skVu8stsrnp6b3FvcjOcjgsKoLCmIgkoBWzON2ZyXEHVLWwVlflWzOoJ--d2U61S3Wlg8viITJ9hbP2bznuzk7RDApuFEatSv1PQ1w?purpose=inline)

**Phase 25 – Distributed Systems and Event-Driven Architecture**

Next Phase

Advanced backend engineering

Planned topics:

1. Distributed systems fundamentals.
2. Synchronous vs asynchronous communication.
3. Event-driven architecture.
4. Event producers and consumers.
5. Pub/Sub architecture.
6. Message brokers.
7. RabbitMQ fundamentals.
8. Apache Kafka fundamentals.
9. Eventual consistency.
10. Distributed transactions.
11. Saga pattern.
12. Transactional Outbox pattern.
13. Event-driven microservices.
14. Real-world distributed system design.

**When you're ready, say: OK, Phase 25.**