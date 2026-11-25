# **Phase 30 – GraphQL, Apollo Server and Advanced API Design**



Advanced Level

Next.js 16.x

Node.js + TypeScript



## **1. Learning Objectives**

In this phase, you will learn how GraphQL works, how it differs from REST APIs, and how to implement GraphQL in a full-stack application.

By the end, you should be able to:

1. Understand GraphQL and why it was introduced.
2. Compare REST and GraphQL architectures.
3. Understand GraphQL Schema Definition Language (SDL).
4. Work with Types, Queries, Mutations, and Resolvers.
5. Use Arguments, Variables, Input Types, and Enums.
6. Understand relationships between GraphQL types.
7. Build GraphQL APIs using Apollo Server.
8. Integrate GraphQL with Node.js, TypeScript, and MongoDB.
9. Integrate GraphQL into a Next.js App Router application.
10. Implement authentication and authorization.
11. Understand the N+1 problem and DataLoader.
12. Implement pagination, filtering, and sorting.
13. Handle errors and secure GraphQL APIs.
14. Understand GraphQL Subscriptions and real-time communication.
15. Identify when GraphQL is a better choice than REST.

## **2. What Is GraphQL?**

GraphQL is a query language for APIs and a server-side runtime that executes queries against a defined data schema.

It was developed at Facebook (now Meta) and publicly released in 2015.

The central idea is:

**The client specifies exactly what data it needs, and the GraphQL server returns data matching that selection.**

Let's understand this with an example.

Imagine you are building an e-commerce application.

A product contains:

```
{
  "id": "product-101",
  "name": "Laptop",
  "price": 50000,
  "description": "High-performance laptop",
  "category": "Electronics",
  "stock": 15,
  "brand": "Lenovo"
}
```

Suppose your frontend only needs:

- Product name.
- Product price.
- Product brand.

With a typical REST API, you might request:

```
GET /api/products/product-101
```

The API may return the entire product object, even though the frontend only needs three fields.

With GraphQL, the client can request exactly those fields:

```
query {
  product(id: "product-101") {
    name
    price
    brand
  }
}
```

The GraphQL server can return:

```
{
  "data": {
    "product": {
      "name": "Laptop",
      "price": 50000,
      "brand": "Lenovo"
    }
  }
}
```

The client controls the selection of fields, while the server controls which fields are available and how they are resolved.

[A Crash Course in GraphQL - ByteByteGo Newsletter](https://images.openai.com/static-rsc-4/I6r9LN3FUOvpFpmpryj4RI0bR4B-3OLSAN3tqU-ojYLAJ7jUUQOQecFQtd7Db3gMZTIx9iiPJus59RRqR3wIuRydODwz5kgaC8-6z8_XA1xmFmXvwnIBokm3GGH_RCr-XR5MQr8exLxk4MqfJ4KnGfIfnQ1WZa0hMr1a_uTEsGA?purpose=inline)

[blog.bytebytego.com](https://blog.bytebytego.com/p/a-crash-course-in-graphql?utm_source=chatgpt.com)

### **Important Characteristics**

- Usually exposes one GraphQL endpoint, such as `/graphql`.
- Uses a strongly typed schema.
- Supports nested data selection.
- Provides Queries for reading data.
- Provides Mutations for modifying data.
- Supports Subscriptions for real-time updates when a compatible transport is configured.
- Allows clients to request specific fields.

**Important:** GraphQL is not a database. It is an API query language and execution system. You can use GraphQL with MongoDB, MySQL, PostgreSQL, REST services, or other data sources.

## **3. Why Was GraphQL Introduced?**

Let's understand the limitations of traditional REST APIs that GraphQL can address.

### **3.1 Over-fetching**

Over-fetching means receiving more data than the client actually needs.

Suppose your product card only needs:

```
{
  "name": "Laptop",
  "price": 50000
}
```

But the REST endpoint returns:

```
{
  "id": "product-101",
  "name": "Laptop",
  "price": 50000,
  "description": "High-performance laptop",
  "category": "Electronics",
  "stock": 15,
  "brand": "Lenovo",
  "createdAt": "2026-11-25T10:00:00Z",
  "updatedAt": "2026-11-25T10:30:00Z"
}
```

The frontend receives unnecessary fields.

GraphQL lets the client select only the fields it needs.

### **3.2 Under-fetching**

Under-fetching happens when one API request does not provide enough information, requiring additional requests.

Imagine a product details page needs:

- Product information.
- Category information.
- Reviews.
- Brand information.

With REST, the frontend might need:

```
GET /api/products/101
GET /api/products/101/reviews
GET /api/categories/5
GET /api/brands/10
```

That is four requests.

With GraphQL, it may be possible to request related data in one operation:

```
query {
  product(id: "101") {
    name
    price

    category {
      name
    }

    brand {
      name
    }

    reviews {
      rating
      comment
    }
  }
}
```

The GraphQL server resolves the requested fields and returns the combined result.

**Important:** One GraphQL request does not necessarily mean one database query. Poorly designed resolvers can generate many database queries. We will study this problem later.

### **3.3 Different Frontend Requirements**

Imagine you have three clients:

- Website.
- Mobile application.
- Admin dashboard.

Each client may need different fields.

GraphQL allows each client to select its own response shape without necessarily requiring a separate endpoint for every screen.

### **3.4 Summary**

| Problem                       | GraphQL capability              |
| ----------------------------- | ------------------------------- |
| Over-fetching                 | Request only required fields    |
| Under-fetching                | Fetch nested related data       |
| Different client requirements | Clients define their selections |
| Multiple REST endpoints       | Often one GraphQL endpoint      |
| API discoverability           | Schema and introspection        |
| Type consistency              | Strongly typed schema           |

GraphQL does not automatically make APIs faster. Resolver design, caching, database access, and query complexity still determine performance.

## **4. GraphQL vs REST API**

This is one of the most important topics in GraphQL.

Both GraphQL and REST are approaches for designing APIs, but they organize requests and responses differently.

### **4.1 REST API**

REST commonly uses multiple resource-oriented endpoints.

For example:

```
GET    /api/products
GET    /api/products/101
POST   /api/products
PUT    /api/products/101
DELETE /api/products/101
```

Each endpoint represents a resource or operation.

### **4.2 GraphQL API**

GraphQL commonly exposes one endpoint:

```
POST /graphql
```

The client sends different GraphQL operations to that endpoint.

Example:

```
query {
  products {
    id
    name
    price
  }
}
```

Another operation:

```
mutation {
  createProduct(
    input: {
      name: "Laptop"
      price: 50000
    }
  ) {
    id
    name
  }
}
```

Both operations can use the same `/graphql` endpoint.

### **4.3 Complete Comparison**

| Feature            | REST                             | GraphQL                                           |
| ------------------ | -------------------------------- | ------------------------------------------------- |
| API structure      | Resource-oriented endpoints      | Schema and operations                             |
| Endpoints          | Usually multiple                 | Often one                                         |
| Data selection     | Defined by endpoint              | Defined by client selection                       |
| Over-fetching      | Can occur                        | Can be reduced                                    |
| Under-fetching     | May require multiple requests    | Nested selection can combine data                 |
| HTTP methods       | GET, POST, PUT, PATCH, DELETE    | Commonly POST; GET can be used for queries        |
| Response structure | Defined by endpoint              | Follows the selection set                         |
| Versioning         | Often URL or header versioning   | Often schema evolution                            |
| Caching            | HTTP caching is well established | Requires deliberate caching strategy              |
| File uploads       | Commonly multipart/form-data     | Usually handled separately or through conventions |
| Real-time          | WebSockets or SSE                | Subscriptions over a chosen transport             |
| Complexity         | Often simpler initially          | Schema and resolver design add complexity         |

### **4.4 REST Versioning vs GraphQL Schema Evolution**

REST APIs commonly use versioned endpoints:

```
/api/v1/products
/api/v2/products
```

GraphQL commonly evolves the schema by adding fields and deprecating old ones.

Example:

```
type Product {
  id: ID!
  name: String!

  oldPrice: Float @deprecated(reason: "Use price instead")

  price: Float!
}
```

Clients can migrate gradually.

You should avoid removing fields while active clients still depend on them.

### **4.5 When Should You Choose REST?**

REST is a good choice when:

- Your API has straightforward resource-based operations.
- HTTP caching is important.
- You want a simple public API.
- Your frontend and backend have predictable data requirements.
- You are building a conventional CRUD application.

### **4.6 When Should You Choose GraphQL?**

GraphQL is useful when:

- Multiple clients need different response shapes.
- Frontend screens require nested related data.
- The API has many related resources.
- You want a strongly typed query schema.
- You want clients to select fields without creating many endpoint variations.

### **4.7 Can REST and GraphQL Coexist?**

Yes.

For example:

```
                  Next.js Frontend
                         |
                         v
                    GraphQL API
                         |
              +----------+----------+
              |                     |
              v                     v
          MongoDB              REST APIs
                                    |
                                    v
                              External Services
```

GraphQL can act as an aggregation layer over existing REST services.

**Important:** GraphQL is not a replacement for every REST endpoint. Many production systems use both.

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+2

## **5. GraphQL Schema Definition Language (SDL)**

The GraphQL Schema Definition Language (SDL) is a syntax used to define the types and operations available in a GraphQL API.

Think of SDL as the contract between the client and the server.

Example:

```
type Product {
  id: ID!
  name: String!
  price: Float!
}
```

This schema tells the client:

- A Product has an `id`.
- A Product has a `name`.
- A Product has a `price`.
- All three fields are required in the returned Product object.

### **5.1 What Are GraphQL Types?**

GraphQL provides several built-in scalar types.

| Type      | Meaning               | Example         |
| --------- | --------------------- | --------------- |
| `Int`     | Signed 32-bit integer | `100`           |
| `Float`   | Floating-point number | `99.99`         |
| `String`  | Text                  | `"Laptop"`      |
| `Boolean` | True or false         | `true`          |
| `ID`      | Identifier            | `"product-101"` |

GraphQL also supports custom scalar types, such as DateTime, when you define their serialization and parsing behavior.

### **5.2 Understanding the Exclamation Mark (**`!`**)**

The exclamation mark indicates a non-null type.

Example:

```
type Product {
  id: ID!
  name: String!
  description: String
}
```

Meaning:

- `id: ID!` – Must not be null.
- `name: String!` – Must not be null.
- `description: String` – May be null.

Compare:

```
type Product {
  tags: [String]
}
```

This allows the list itself to be null, and individual list elements may also be null.

Now:

```
type Product {
  tags: [String!]!
}
```

This means:

- The list itself cannot be null.
- Individual elements cannot be null.

Example valid response:

```
{
  "tags": ["electronics", "laptop", "computer"]
}
```

Example invalid response:

```
{
  "tags": ["electronics", null]
}
```

### **5.3 Object Types**

An Object Type represents an object containing fields.

```
type User {
  id: ID!
  name: String!
  email: String!
}
```

Another example:

```
type Order {
  id: ID!
  total: Float!
  status: String!
}
```

### **5.4 Enum Types**

An Enum restricts a field to a predefined set of values.

```
enum OrderStatus {
  PENDING
  PAID
  CANCELLED
  SHIPPED
  DELIVERED
}
```

Use it inside an object:

```
type Order {
  id: ID!
  status: OrderStatus!
}
```

This prevents arbitrary values such as:

```
"COMPLETED_SUCCESSFULLY"
"UNKNOWN_STATUS"
"FINISHED"
```

unless they are explicitly defined in the enum.

### **5.5 Input Types**

Input Types define structured data accepted by fields, particularly mutations.

```
input CreateProductInput {
  name: String!
  price: Float!
  description: String
  categoryId: ID!
}
```

A mutation can accept this input:

```
type Mutation {
  createProduct(input: CreateProductInput!): Product!
}
```

### **5.6 Scalar vs Object vs Enum vs Input**

| Type   | Purpose                                 |
| ------ | --------------------------------------- |
| Scalar | Represents a primitive value            |
| Object | Represents structured output data       |
| Enum   | Restricts a value to predefined choices |
| Input  | Defines structured input data           |

**Important:** GraphQL input types and output object types are different. An output Object Type cannot simply be used as an input argument.

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+1

## **6. Query – Reading Data**

A Query is a GraphQL operation used to retrieve data.

It is conceptually similar to GET in REST.

Example:

```
query {
  products {
    id
    name
    price
  }
}
```

### **6.1 Define the Query Type**

Every GraphQL schema must define a Query root type.

```
type Query {
  products: [Product!]!
}
```

This means:

- `products` returns a list.
- The list cannot be null.
- Each Product inside the list cannot be null.

### **6.2 Query a Single Product**

Schema:

```
type Query {
  product(id: ID!): Product
}
```

Client operation:

```
query {
  product(id: "product-101") {
    id
    name
    price
  }
}
```

Here, `id` is an argument.

### **6.3 Named Queries**

You can give a query an operation name.

```
query GetProduct {
  product(id: "product-101") {
    id
    name
    price
  }
}
```

Operation names help with:

- Debugging.
- Logging.
- Monitoring.
- Identifying frontend operations.

### **6.4 Query with Variables**

Hardcoding values inside queries is not ideal.

Instead, use variables.

```
query GetProduct($productId: ID!) {
  product(id: $productId) {
    id
    name
    price
  }
}
```

Variables:

```
{
  "productId": "product-101"
}
```

Benefits:

- Reusable operations.
- Better separation of query and data.
- Easier validation.
- Safer handling of dynamic values.

### **6.5 Multiple Fields in One Query**

```
query DashboardData {
  products {
    id
    name
  }

  orders {
    id
    status
    total
  }
}
```

This asks for two root fields in one operation.

### **6.6 Aliases**

Aliases let you request the same field more than once using different arguments.

```
query {
  firstProduct: product(id: "101") {
    name
    price
  }

  secondProduct: product(id: "102") {
    name
    price
  }
}
```

Response:

```
{
  "data": {
    "firstProduct": {
      "name": "Laptop",
      "price": 50000
    },
    "secondProduct": {
      "name": "Keyboard",
      "price": 1500
    }
  }
}
```

### **6.7 Fragments**

Fragments allow you to reuse field selections.

```
fragment ProductBasicInfo on Product {
  id
  name
  price
}
```

Use the fragment:

```
query {
  products {
    ...ProductBasicInfo
  }
}

fragment ProductBasicInfo on Product {
  id
  name
  price
}
```

In a frontend project, fragments help avoid repeating the same selection across multiple operations.

### **6.8 Directives**

Directives modify how fields are included or skipped.

Two built-in directives are:

- `@include`
- `@skip`

Example:

```
query GetUser($includeEmail: Boolean!) {
  user(id: "user-101") {
    name
    email @include(if: $includeEmail)
  }
}
```

Variables:

```
{
  "includeEmail": true
}
```

If `includeEmail` is false, the email field is omitted from the result.

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+1

## **7. Mutation – Creating, Updating and Deleting Data**

A Mutation is a GraphQL operation used to modify data or perform a side effect.

It is conceptually similar to POST, PUT, PATCH, or DELETE in REST.

Common mutation operations:

- Create a product.
- Update a product.
- Delete a product.
- Register a user.
- Place an order.
- Process a payment.

### **7.1 Define Mutation Type**

```
type Mutation {
  createProduct(
    name: String!
    price: Float!
  ): Product!
}
```

### **7.2 Execute Mutation**

```
mutation {
  createProduct(
    name: "Laptop"
    price: 50000
  ) {
    id
    name
    price
  }
}
```

Response:

```
{
  "data": {
    "createProduct": {
      "id": "product-101",
      "name": "Laptop",
      "price": 50000
    }
  }
}
```

Notice that the client chooses which fields to receive even after creating data.

### **7.3 Mutation with Input Type**

A cleaner approach is to use an input object.

Schema:

```
input CreateProductInput {
  name: String!
  price: Float!
  description: String
}

type Mutation {
  createProduct(input: CreateProductInput!): Product!
}
```

Mutation:

```
mutation CreateProduct($input: CreateProductInput!) {
  createProduct(input: $input) {
    id
    name
    price
  }
}
```

Variables:

```
{
  "input": {
    "name": "Laptop",
    "price": 50000,
    "description": "High-performance laptop"
  }
}
```

### **7.4 Mutation Execution Order**

A useful GraphQL rule:

- Top-level Query fields can execute in parallel.
- Top-level Mutation fields execute serially.

However, serial execution of mutation fields does not mean the entire operation is an atomic database transaction.

If one mutation field succeeds and another fails, GraphQL does not automatically roll back the successful field.

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+1

## **8. Resolvers – How GraphQL Fetches Data**

A Resolver is a function responsible for obtaining the value of a GraphQL field.

This is one of the most important concepts in GraphQL.

Remember:

**The Schema defines what can be requested. Resolvers define how the requested data is obtained.**

### **8.1 Simple Example**

Schema:

```
type Query {
  hello: String!
}
```

Resolver:

```
const resolvers = {
  Query: {
    hello: () => {
      return "Hello from GraphQL";
    },
  },
};
```

When the client executes:

```
query {
  hello
}
```

The resolver runs and returns:

```
{
  "data": {
    "hello": "Hello from GraphQL"
  }
}
```

### **8.2 Resolver Structure**

A resolver commonly receives four arguments:

```
(parent, args, context, info)
```

| Argument  | Meaning                                         |
| --------- | ----------------------------------------------- |
| `parent`  | Result returned by the parent resolver          |
| `args`    | Arguments supplied to the field                 |
| `context` | Shared request-level information                |
| `info`    | Information about the current GraphQL execution |

Example:

```
const resolvers = {
  Query: {
    product: (
      parent: unknown,
      args: { id: string },
      context: unknown,
      info: unknown
    ) => {
      console.log(args.id);

      return {
        id: args.id,
        name: "Laptop",
        price: 50000,
      };
    },
  },
};
```

In production, define proper TypeScript types for context and resolver arguments rather than using `unknown` everywhere.

### **8.3 Parent Resolver**

Suppose your schema is:

```
type Product {
  id: ID!
  name: String!
  category: Category
}

type Category {
  id: ID!
  name: String!
}
```

Query:

```
query {
  product(id: "101") {
    name

    category {
      name
    }
  }
}
```

The Product resolver returns a product.

The Category resolver receives that product as its parent.

Example:

```
const resolvers = {
  Query: {
    product: async (_parent: unknown, args: { id: string }) => {
      return {
        id: args.id,
        name: "Laptop",
        categoryId: "category-1",
      };
    },
  },

  Product: {
    category: async (parent: { categoryId: string }) => {
      return {
        id: parent.categoryId,
        name: "Electronics",
      };
    },
  },
};
```

The `Product.category` resolver receives the object returned by the Product resolver.

This is how nested GraphQL data is resolved.

### **8.4 Default Resolvers**

You do not have to write a resolver for every single field.

If a returned object already has a property matching the requested field name, GraphQL's default field resolver can retrieve that property.

For example:

```
const resolvers = {
  Query: {
    product: () => ({
      id: "101",
      name: "Laptop",
      price: 50000,
    }),
  },
};
```

For this schema:

```
type Product {
  id: ID!
  name: String!
  price: Float!
}
```

You only need to define the root `product` resolver. The default field resolver can retrieve `id`, `name`, and `price` from the returned object.

### **8.5 Resolver vs Controller**

| REST Controller                            | GraphQL Resolver                        |
| ------------------------------------------ | --------------------------------------- |
| Handles an HTTP route                      | Handles a GraphQL field                 |
| Often returns a complete response          | Resolves a particular field             |
| Receives HTTP request and response objects | Receives parent, args, context and info |
| Route-oriented                             | Schema-field-oriented                   |

**Important:** A resolver should not become a giant business-logic function. Keep business operations in service or application-layer functions, and let resolvers delegate to them.

## **9. GraphQL Context**

GraphQL Context is a shared object available to resolvers during an operation.

It commonly contains:

- Authenticated user.
- Database connection or repositories.
- DataLoaders.
- Logger.
- Request metadata.
- Service clients.

Example:

```
type GraphQLContext = {
  user: {
    id: string;
    role: "USER" | "ADMIN";
  } | null;

  productRepository: ProductRepository;
};
```

A resolver can access it:

```
const resolvers = {
  Query: {
    me: async (
      _parent: unknown,
      _args: unknown,
      context: GraphQLContext
    ) => {
      if (!context.user) {
        throw new Error("Unauthenticated");
      }

      return context.user;
    },
  },
};
```

The context is typically created for each incoming GraphQL operation request.

It is not the same as React Context.

| GraphQL Context                                 | React Context                             |
| ----------------------------------------------- | ----------------------------------------- |
| Server-side resolver information                | React component-tree information          |
| Shared across resolvers for an operation        | Shared with descendant components         |
| Can contain authenticated user and repositories | Can contain UI state and application data |

[image](https://www.google.com/s2/favicons?domain=https://www.apollographql.com\&sz=32)

Apollo GraphQL Docs+1

## **10. Apollo Server**

Apollo Server is a popular GraphQL server implementation for Node.js.

It helps you:

- Define GraphQL schemas.
- Register resolvers.
- Execute GraphQL operations.
- Share request-level context.
- Integrate with HTTP frameworks.
- Add plugins and monitoring.

### **10.1 Install Apollo Server**

For a new TypeScript project:

```
mkdir graphql-server
cd graphql-server

npm init -y

npm install @apollo/server graphql

npm install -D typescript tsx @types/node
```

The current Apollo Server getting-started guide uses `@apollo/server` and `graphql`, and its standalone setup supports a straightforward first server.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.apollographql.com\&sz=32)

Apollo GraphQL Docs



### **10.2 Create the Project Structure**

```
graphql-server/
│
├── src/
│   ├── schema/
│   │   └── typeDefs.ts
│   │
│   ├── resolvers/
│   │   └── resolvers.ts
│   │
│   ├── data/
│   │   └── products.ts
│   │
│   └── server.ts
│
├── package.json
└── tsconfig.json
```

### **10.3 Define the Schema**

```
// src/schema/typeDefs.ts

export const typeDefs = `#graphql

  type Product {
    id: ID!
    name: String!
    price: Float!
    description: String
  }

  type Query {
    products: [Product!]!
    product(id: ID!): Product
  }

`;
```

### **10.4 Create Sample Data**

```
// src/data/products.ts

export type Product = {
  id: string;
  name: string;
  price: number;
  description?: string;
};

export const products: Product[] = [
  {
    id: "101",
    name: "Laptop",
    price: 50000,
    description: "High-performance laptop",
  },
  {
    id: "102",
    name: "Keyboard",
    price: 1500,
    description: "Mechanical keyboard",
  },
];
```

### **10.5 Define Resolvers**

```
// src/resolvers/resolvers.ts

import { products } from "../data/products";

export const resolvers = {
  Query: {
    products: () => products,

    product: (
      _parent: unknown,
      args: { id: string }
    ) => {
      return products.find(
        (product) => product.id === args.id
      ) ?? null;
    },
  },
};
```

### **10.6 Create the Apollo Server**

```
// src/server.ts

import { ApolloServer } from "@apollo/server";

import { startStandaloneServer } from "@apollo/server/standalone";

import { typeDefs } from "./schema/typeDefs";

import { resolvers } from "./resolvers/resolvers";

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

const { url } = await startStandaloneServer(server, {
  listen: {
    port: 4000,
  },
});

console.log(`GraphQL Server ready at ${url}`);
```

Add scripts to `package.json`:

```
{
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "start": "tsx src/server.ts"
  }
}
```

Start:

```
npm run dev
```

Your GraphQL endpoint will be available at:

```
http://localhost:4000/
```

### **10.7 Test Your First Query**

```
query GetProducts {
  products {
    id
    name
    price
  }
}
```

You can use Apollo Sandbox or another GraphQL client to send operations to the endpoint.

### **10.8 Test a Single Product**

```
query GetProduct {
  product(id: "101") {
    id
    name
    price
    description
  }
}
```

You have now created a working GraphQL server.

**Note:** This is a development example with in-memory data. Apollo's standalone server is useful for learning and prototyping. For more advanced HTTP integration, middleware, and subscriptions, use an appropriate framework integration such as Express.

## **11. GraphQL with MongoDB**

Now let's connect GraphQL with MongoDB.

The architecture will look like this:

```
GraphQL Client
      |
      v
Apollo Server
      |
      v
Resolvers
      |
      v
Application / Repository Layer
      |
      v
MongoDB
```

The resolver receives the operation and delegates database work to the appropriate repository or data-access function.

### **11.1 Install MongoDB Driver**

```
npm install mongodb
```

### **11.2 Configure MongoDB Connection**

```
// src/database/mongodb.ts

import { MongoClient } from "mongodb";

const uri = process.env.MONGODB_URI;

if (!uri) {
  throw new Error("MONGODB_URI is required");
}

const client = new MongoClient(uri);

let connection: Promise<MongoClient> | undefined;

export async function getDatabase() {
  connection ??= client.connect();

  const connectedClient = await connection;

  return connectedClient.db("ecommerce");
}
```

This example reuses the connection promise within the running Node.js process.

For production, configure connection lifecycle, pool sizing, shutdown handling, and deployment-specific connection management.

### **11.3 Define GraphQL Schema**

```
// src/schema/typeDefs.ts

export const typeDefs = `#graphql

  type Product {
    id: ID!
    name: String!
    price: Float!
    description: String
  }

  input CreateProductInput {
    name: String!
    price: Float!
    description: String
  }

  type Query {
    products: [Product!]!
    product(id: ID!): Product
  }

  type Mutation {
    createProduct(input: CreateProductInput!): Product!
  }

`;
```

### **11.4 Create MongoDB Resolvers**

```
// src/resolvers/resolvers.ts

import { ObjectId } from "mongodb";

import { getDatabase } from "../database/mongodb";

type CreateProductInput = {
  name: string;
  price: number;
  description?: string;
};

export const resolvers = {
  Query: {
    products: async () => {
      const db = await getDatabase();

      const products = await db
        .collection("products")
        .find()
        .toArray();

      return products.map((product) => ({
        ...product,
        id: product._id.toString(),
      }));
    },

    product: async (
      _parent: unknown,
      args: { id: string }
    ) => {
      if (!ObjectId.isValid(args.id)) {
        return null;
      }

      const db = await getDatabase();

      const product = await db
        .collection("products")
        .findOne({
          _id: new ObjectId(args.id),
        });

      if (!product) {
        return null;
      }

      return {
        ...product,
        id: product._id.toString(),
      };
    },
  },

  Mutation: {
    createProduct: async (
      _parent: unknown,
      args: { input: CreateProductInput }
    ) => {
      const db = await getDatabase();

      const result = await db
        .collection("products")
        .insertOne({
          name: args.input.name,
          price: args.input.price,
          description: args.input.description ?? null,
        });

      return {
        id: result.insertedId.toString(),
        ...args.input,
      };
    },
  },
};
```

This is a simplified integration example. A production implementation should:

- Validate input with Zod or equivalent application-level validation.
- Enforce authorization.
- Handle MongoDB errors.
- Keep persistence code out of large resolver functions.
- Use a repository layer for more complex applications.

### **11.5 Important: GraphQL ID vs MongoDB ObjectId**

These are different concepts.

| GraphQL ID                           | MongoDB ObjectId                                            |
| ------------------------------------ | ----------------------------------------------------------- |
| GraphQL scalar type                  | MongoDB identifier type                                     |
| Serialized as a string               | 12-byte identifier represented as 24 hexadecimal characters |
| Does not prescribe a database format | Specific MongoDB implementation                             |
| Used in GraphQL schemas              | Used in MongoDB documents                                   |

You can expose a MongoDB ObjectId as a GraphQL ID by converting it to a string.

Example:

```
id: document._id.toString()
```

When receiving the ID:

```
const objectId = new ObjectId(id);
```

Validate it before conversion.

## **12. Authentication and Authorization in GraphQL**

GraphQL does not automatically provide authentication or authorization.

You must implement these controls.

### **12.1 Authentication**

Authentication answers:

**Who is making this request?**

For example, a client sends a JWT:

```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

The server verifies the token and identifies the user.

### **12.2 Authorization**

Authorization answers:

**What is this user allowed to do?**

For example:

- Normal users can view products.
- Admins can create products.
- Users can view their own orders.
- Admins can view all orders.

### **12.3 Add User to GraphQL Context**

Define the context:

```
type AuthenticatedUser = {
  id: string;
  role: "USER" | "ADMIN";
};

type GraphQLContext = {
  user: AuthenticatedUser | null;
};
```

In the Apollo Server setup:

```
const { url } = await startStandaloneServer(server, {
  listen: {
    port: 4000,
  },

  context: async ({ req }): Promise<GraphQLContext> => {
    const authorization = req.headers.authorization;

    if (!authorization) {
      return {
        user: null,
      };
    }

    // Verify the JWT signature and claims here.
    const user = await verifyAccessToken(authorization);

    return {
      user,
    };
  },
});
```

`verifyAccessToken()` represents your own verified authentication implementation; it must not simply decode the token.

### **12.4 Protect a Resolver**

```
const resolvers = {
  Mutation: {
    createProduct: async (
      _parent: unknown,
      args: { input: CreateProductInput },
      context: GraphQLContext
    ) => {
      if (!context.user) {
        throw new Error("Authentication required");
      }

      if (context.user.role !== "ADMIN") {
        throw new Error("Forbidden");
      }

      // Create product
    },
  },
};
```

### **12.5 Important Security Rule**

Do not assume that hiding a button in the frontend protects the operation.

Authorization must be enforced on the server.

Also, authorization should be checked at the appropriate data and business-operation boundaries. A user must not be able to retrieve another user's private order simply by changing an ID.

[image](https://www.google.com/s2/favicons?domain=https://www.apollographql.com\&sz=32)

Apollo GraphQL Docs+1

## **13. The N+1 Problem in GraphQL**

The N+1 problem is one of the most important GraphQL performance problems.

Let's understand it carefully.

Suppose you have:

- 1 query to fetch 10 orders.
- Each order has a customer.
- Each customer is fetched separately.

The database calls become:

```
1 query to fetch all orders
10 queries to fetch 10 customers

Total = 11 database queries
```

This is called the N+1 problem.

### **13.1 Example Without DataLoader**

```
const resolvers = {
  Query: {
    orders: async () => {
      return db.collection("orders").find().toArray();
    },
  },

  Order: {
    customer: async (order: { userId: string }) => {
      return db.collection("users").findOne({
        _id: new ObjectId(order.userId),
      });
    },
  },
};
```

Suppose the query is:

```
query {
  orders {
    id
    total

    customer {
      name
    }
  }
}
```

If there are 10 orders, the customer resolver may run 10 times and make 10 database queries.

### **13.2 What Is DataLoader?**

DataLoader is a utility commonly used in GraphQL applications to batch and cache data-fetching operations.

Instead of querying each customer individually, DataLoader collects IDs and performs a batch lookup.

### **13.3 How DataLoader Works**

Without DataLoader:

```
Order 1 → Find User 1
Order 2 → Find User 2
Order 3 → Find User 3
Order 4 → Find User 4
```

With DataLoader:

```
Order 1 ─┐
Order 2 ─┤
Order 3 ─┼──> DataLoader ──> One batch query
Order 4 ─┘
```

### **13.4 Install DataLoader**

```
npm install dataloader
```

### **13.5 Create a User DataLoader**

```
// src/loaders/userLoader.ts

import DataLoader from "dataloader";

import { ObjectId } from "mongodb";

import { getDatabase } from "../database/mongodb";

type User = {
  _id: ObjectId;
  name: string;
  email: string;
};

export function createUserLoader() {
  return new DataLoader<string, User | null>(
    async (userIds) => {
      const db = await getDatabase();

      const users = await db
        .collection<User>("users")
        .find({
          _id: {
            $in: userIds.map((id) => new ObjectId(id)),
          },
        })
        .toArray();

      const userMap = new Map(
        users.map((user) => [
          user._id.toString(),
          user,
        ])
      );

      return userIds.map(
        (id) => userMap.get(id) ?? null
      );
    }
  );
}
```

Notice:

- DataLoader collects user IDs.
- MongoDB retrieves all matching users using `$in`.
- The results are mapped back into the original ID order.
- Missing users are represented as `null`.

### **13.6 Add DataLoader to Context**

```
type GraphQLContext = {
  user: AuthenticatedUser | null;
  userLoader: ReturnType<typeof createUserLoader>;
};
```

Create a new loader for each request:

```
context: async ({ req }) => {
  const user = await authenticateRequest(req);

  return {
    user,
    userLoader: createUserLoader(),
  };
}
```

### **13.7 Use DataLoader in the Resolver**

```
const resolvers = {
  Order: {
    customer: async (
      order: { userId: string },
      _args: unknown,
      context: GraphQLContext
    ) => {
      return context.userLoader.load(order.userId);
    },
  },
};
```

Now customer lookups can be batched during the same execution window.

**Important:** Create DataLoader instances per request, especially when the data is user-specific. A global loader can accidentally share cached results across users or requests.

DataLoader is useful for more than MongoDB. It can batch SQL queries, REST requests, and other data-source lookups.

[image](https://www.google.com/s2/favicons?domain=https://www.apollographql.com\&sz=32)

Apollo GraphQL Docs

## **14. Pagination, Filtering and Sorting**

Imagine your e-commerce database contains 100,000 products.

You should not return all products in one GraphQL response.

You need pagination.

### **14.1 Offset-Based Pagination**

Example:

```
type Query {
  products(
    limit: Int!
    offset: Int!
  ): [Product!]!
}
```

Client query:

```
query {
  products(limit: 10, offset: 20) {
    id
    name
    price
  }
}
```

This means:

- Skip the first 20 records.
- Return the next 10 records.

MongoDB example:

```
const products = await db
  .collection("products")
  .find()
  .skip(offset)
  .limit(limit)
  .toArray();
```

**Advantages:**

- Easy to understand.
- Simple to implement.
- Useful for smaller datasets.

**Disadvantages:**

- Large offsets can become expensive.
- Changes to the dataset between requests can lead to inconsistent page contents.

### **14.2 Cursor-Based Pagination**

Cursor-based pagination uses a position marker to retrieve the next set of records.

Example schema:

```
type ProductEdge {
  cursor: String!
  node: Product!
}

type PageInfo {
  hasNextPage: Boolean!
  endCursor: String
}

type ProductConnection {
  edges: [ProductEdge!]!
  pageInfo: PageInfo!
}

type Query {
  products(
    first: Int!
    after: String
  ): ProductConnection!
}
```

Query:

```
query {
  products(first: 10) {
    edges {
      cursor

      node {
        id
        name
        price
      }
    }

    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

For the next page:

```
query {
  products(first: 10, after: "cursor-from-previous-page") {
    edges {
      node {
        id
        name
      }
    }

    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

The cursor identifies where the next page should begin.

### **14.3 Filtering**

Example schema:

```
type Query {
  products(
    category: String
    minPrice: Float
    maxPrice: Float
  ): [Product!]!
}
```

Query:

```
query {
  products(
    category: "Electronics"
    minPrice: 10000
    maxPrice: 60000
  ) {
    id
    name
    price
  }
}
```

### **14.4 Sorting**

You can use an enum:

```
enum ProductSort {
  PRICE_ASC
  PRICE_DESC
  NAME_ASC
  NAME_DESC
}

type Query {
  products(sort: ProductSort): [Product!]!
}
```

Query:

```
query {
  products(sort: PRICE_ASC) {
    name
    price
  }
}
```

### **14.5 Offset vs Cursor Pagination**

| Offset Pagination                             | Cursor Pagination                               |
| --------------------------------------------- | ----------------------------------------------- |
| Uses limit and offset                         | Uses cursor and page size                       |
| Simple to implement                           | More involved                                   |
| Large offsets can be expensive                | Can be efficient with suitable indexes          |
| Can be unstable with frequent inserts/deletes | More stable when based on a unique, ordered key |
| Good for simple admin tables                  | Good for feeds and large datasets               |

For cursor pagination, use a deterministic ordering and preferably a unique tie-breaker such as `_id`.

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+1

## **15. GraphQL Error Handling**

GraphQL errors are different from ordinary REST error responses.

A GraphQL response can contain:

- `data`
- `errors`

### **15.1 Successful Response**

```
{
  "data": {
    "product": {
      "id": "101",
      "name": "Laptop"
    }
  }
}
```

### **15.2 Error Response**

Suppose the client requests a product that does not exist.

The resolver may return `null`, or it may throw an error depending on the API's design.

Example:

```
{
  "data": {
    "product": null
  }
}
```

### **15.3 Response with Partial Data**

Imagine a query requests:

- Product information.
- Reviews.

Product fetching succeeds, but Review fetching fails.

A GraphQL response may contain partial data and errors:

```
{
  "data": {
    "product": {
      "name": "Laptop",
      "reviews": null
    }
  },
  "errors": [
    {
      "message": "Unable to fetch reviews"
    }
  ]
}
```

This is an important GraphQL feature.

A field-level execution error does not necessarily prevent unrelated fields from returning data.

### **15.4 GraphQLError**

GraphQL libraries provide `GraphQLError` for structured errors.

```
import { GraphQLError } from "graphql";

const resolvers = {
  Query: {
    privateData: (
      _parent: unknown,
      _args: unknown,
      context: GraphQLContext
    ) => {
      if (!context.user) {
        throw new GraphQLError(
          "Authentication required",
          {
            extensions: {
              code: "UNAUTHENTICATED",
            },
          }
        );
      }

      return {
        message: "Private information",
      };
    },
  },
};
```

Common application-level error codes:

| Code                    | Meaning                           |
| ----------------------- | --------------------------------- |
| `UNAUTHENTICATED`       | User is not authenticated         |
| `FORBIDDEN`             | User lacks permission             |
| `BAD_USER_INPUT`        | Invalid input                     |
| `NOT_FOUND`             | Requested resource does not exist |
| `INTERNAL_SERVER_ERROR` | Unexpected server failure         |

These codes are conventions; define a consistent error policy for your API.

### **15.5 Error Handling Best Practices**

- Do not expose database stack traces.
- Do not expose passwords or secret values.
- Return meaningful error messages.
- Distinguish authentication from authorization failures.
- Log detailed internal errors securely.
- Preserve partial data when appropriate.
- Use consistent error codes.

[image](https://www.google.com/s2/favicons?domain=https://spec.graphql.org\&sz=32)

spec.graphql.org+1

## **16. GraphQL Subscriptions and Real-Time Communication**

A Subscription is a GraphQL operation used to receive updates when particular server-side events occur.

It is useful for real-time features.

Examples:

- Chat messages.
- Live order status.
- Notifications.
- Live dashboards.
- Collaborative applications.

### **16.1 Query vs Mutation vs Subscription**

| Operation    | Purpose               | Example             |
| ------------ | --------------------- | ------------------- |
| Query        | Read data             | Get product         |
| Mutation     | Modify data           | Create order        |
| Subscription | Receive event updates | Receive new message |

### **16.2 Subscription Schema**

```
type Message {
  id: ID!
  content: String!
  senderId: ID!
}

type Subscription {
  messageReceived(chatId: ID!): Message!
}
```

### **16.3 Client Subscription**

```
subscription ListenForMessages($chatId: ID!) {
  messageReceived(chatId: $chatId) {
    id
    content
    senderId
  }
}
```

The client stays subscribed and receives new messages when the server publishes matching events.

### **16.4 How Subscriptions Work**

[Implementing Real-Time Communication with WebSockets and GraphQL](https://images.openai.com/static-rsc-4/MuDbHPCiDmkgEgnnD9IbhWPbJ-gBZ7oZv_a0VdS-thBAPiflTJliqrUlFQhdZ426IRcJ2p4mNxAyNLWYyE-cDIgJpb3WAeN3xt4fQFWvp19QFFACVOY7CuhgRZ7xN0YmYB6lH4Bs4fyF63Ya-q9Osjh-6yZZZovjV0YP3cgsVZg?purpose=inline)

[linkedin.com](https://www.linkedin.com/pulse/implementing-real-time-communication-websockets-graphql-sheetal-g-1va0f?utm_source=chatgpt.com)

```
Client
   |
   | WebSocket connection
   |
   v
GraphQL Server
   |
   v
Subscription Resolver
   |
   v
Pub/Sub System
   |
   v
New Event Published
   |
   v
Client receives update
```

### **16.5 Subscription Transport**

GraphQL defines the subscription operation, but it does not prescribe one specific transport protocol.

Common choices include:

- WebSockets.
- Server-Sent Events (SSE).

WebSockets are commonly used for bidirectional real-time communication.

### **16.6 Apollo Server Subscription Support**

An important current-version detail:

Apollo Server's `startStandaloneServer()` does not provide built-in subscription support for a non-federated graph.

A typical setup uses:

- Express integration.
- `graphql-ws`.
- A WebSocket server.
- A shared executable GraphQL schema.

Example dependencies:

```
npm install @as-integrations/express5 express graphql-ws ws @graphql-tools/schema
```

Apollo Server's current subscription documentation describes using a WebSocket integration such as `graphql-ws` rather than the older, unmaintained `subscriptions-transport-ws` package.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+1



### **16.7 Subscriptions vs Socket.IO**

| GraphQL Subscriptions                  | Socket.IO                                                        |
| -------------------------------------- | ---------------------------------------------------------------- |
| GraphQL operation-based real-time data | General-purpose event-based communication                        |
| Uses GraphQL schema and selection sets | Uses application-defined events                                  |
| Commonly uses WebSockets               | Uses WebSockets with additional transport fallbacks and features |
| Fits GraphQL applications              | Fits chat, multiplayer and general real-time systems             |

For your chat application projects, Socket.IO can be a suitable choice. If the entire application is already GraphQL-based, GraphQL Subscriptions may be a natural fit.

## **17. GraphQL Security and Performance Optimization**

GraphQL gives clients considerable flexibility.

That flexibility also creates security and performance risks.

### **17.1 Query Depth Limiting**

Consider this query:

```
query {
  user {
    friends {
      friends {
        friends {
          friends {
            name
          }
        }
      }
    }
  }
}
```

Deeply nested queries can consume considerable resources.

Limit the maximum allowed query depth.

### **17.2 Query Complexity Limiting**

A query can be shallow but still expensive.

Example:

```
query {
  users {
    orders {
      products {
        reviews {
          comments {
            author {
              name
            }
          }
        }
      }
    }
  }
}
```

Query complexity analysis estimates the cost of an operation and rejects excessively expensive requests.

### **17.3 Pagination Limits**

Avoid unbounded queries:

```
query {
  products {
    id
    name
  }
}
```

Prefer:

```
query {
  products(first: 20) {
    edges {
      node {
        id
        name
      }
    }
  }
}
```

Also enforce a server-side maximum page size. Do not rely on clients to choose reasonable values.

### **17.4 Rate Limiting**

Limit how many requests a client can make within a time window.

For example:

```
100 requests per minute per authenticated client
```

A GraphQL request can be much more expensive than another request, so rate limiting may also need to account for query complexity.

### **17.5 Avoid Excessive Introspection in Production**

GraphQL introspection lets clients inspect the schema.

It is useful for development tools and documentation.

For public production APIs, decide deliberately whether to allow it. Disabling introspection alone does not secure an API; authorization, validation, query limits, and demand controls remain necessary.

### **17.6 Use HTTPS**

Always protect sensitive API traffic using HTTPS.

Also configure:

- Authentication.
- Authorization.
- Request timeouts.
- Input validation.
- Safe error handling.
- Query depth and complexity limits.
- Rate limiting.
- Pagination.
- Secure caching.

### **17.7 Persisted Queries and Trusted Documents**

A trusted-document approach allows a server to accept only approved GraphQL operations.

This can reduce exposure to arbitrary client-supplied queries and improve control over API usage.

It is particularly useful when your API is used by your own controlled frontend applications.

[image](https://www.google.com/s2/favicons?domain=https://graphql.org\&sz=32)

GraphQL+1

## **18. GraphQL with Next.js 16 App Router**

Now let's understand how to use GraphQL in a Next.js application.

There are two important parts:

1. GraphQL Server – Provides the API.
2. GraphQL Client – Sends operations and consumes responses.

You can use Next.js as a GraphQL client, or you can host a GraphQL API separately and connect Next.js to it.

### **18.1 Recommended Architecture**

For a full-stack application:

```
                 Browser
                    |
                    v
                Next.js 16
                    |
                    v
               Apollo Client
                    |
                    v
              GraphQL Server
                    |
                    v
                Resolvers
                    |
                    v
                MongoDB
```

For larger systems, the GraphQL server can also aggregate data from multiple microservices.

### **18.2 Install Apollo Client**

Inside your Next.js project:

```
npm install @apollo/client@latest @apollo/client-integration-nextjs graphql rxjs
```

The official Apollo integration supports both React Server Components and Client Components in the Next.js App Router.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.apollographql.com\&sz=32)

Apollo GraphQL Docs



### **18.3 Configure Apollo Client for Server Components**

Create:

```
src/lib/ApolloClient.ts
```

```
import { HttpLink } from "@apollo/client";

import {
  ApolloClient,
  InMemoryCache,
  registerApolloClient,
} from "@apollo/client-integration-nextjs";

export const { getClient, query } = registerApolloClient(() => {
  return new ApolloClient({
    cache: new InMemoryCache(),

    link: new HttpLink({
      uri: process.env.GRAPHQL_API_URL!,
    }),
  });
});
```

Environment variable:

```
GRAPHQL_API_URL=http://localhost:4000/
```

Use an absolute URL for server-side requests.

### **18.4 Query GraphQL from a Server Component**

Create:

```
src/graphql/queries/products.ts
```

```
import { gql } from "@apollo/client";

export const GET_PRODUCTS = gql`
  query GetProducts {
    products {
      id
      name
      price
    }
  }
`;
```

Now use it:

```
// src/app/products/page.tsx

import { query } from "@/lib/ApolloClient";

import { GET_PRODUCTS } from "@/graphql/queries/products";

export default async function ProductsPage() {
  const { data } = await query({
    query: GET_PRODUCTS,
  });

  return (
    <main>
      <h1>Products</h1>

      {data.products.map((product: any) => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>₹{product.price}</p>
        </div>
      ))}
    </main>
  );
}
```

This is a Server Component.

The GraphQL query runs on the server.

The browser does not need to make that initial GraphQL request itself.

### **18.5 Apollo Client in Client Components**

Sometimes you need client-side interactions:

- Search.
- Filtering.
- Pagination.
- Refetching.
- Mutations.
- Interactive dashboards.

For that, configure Apollo's Client Component integration.

Create:

```
src/app/ApolloWrapper.tsx
```

```
"use client";

import { HttpLink } from "@apollo/client";

import {
  ApolloClient,
  InMemoryCache,
} from "@apollo/client";

import {
  ApolloNextAppProvider,
} from "@apollo/client-integration-nextjs";

function makeClient() {
  const httpLink = new HttpLink({
    uri: process.env.NEXT_PUBLIC_GRAPHQL_API_URL,
  });

  return new ApolloClient({
    cache: new InMemoryCache(),
    link: httpLink,
  });
}

export function ApolloWrapper({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <ApolloNextAppProvider makeClient={makeClient}>
      {children}
    </ApolloNextAppProvider>
  );
}
```

Public environment variable:

```
NEXT_PUBLIC_GRAPHQL_API_URL=http://localhost:4000/
```

The URL is public configuration, not a secret.

Wrap your root layout:

```
// src/app/layout.tsx

import { ApolloWrapper } from "./ApolloWrapper";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <ApolloWrapper>
          {children}
        </ApolloWrapper>
      </body>
    </html>
  );
}
```

### **18.6 Use GraphQL in a Client Component**

```
"use client";

import { gql } from "@apollo/client";

import { useSuspenseQuery } from "@apollo/client/react";

const GET_PRODUCTS = gql`
  query GetProducts {
    products {
      id
      name
      price
    }
  }
`;

export function ProductList() {
  const { data } = useSuspenseQuery(GET_PRODUCTS);

  return (
    <div>
      {data.products.map((product: any) => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>₹{product.price}</p>
        </div>
      ))}
    </div>
  );
}
```

Because this uses Suspense, render it within a suitable Suspense boundary.

### **18.7 Server Component vs Client Component for GraphQL**

| Server Component                       | Client Component                    |
| -------------------------------------- | ----------------------------------- |
| Server-side query execution            | Browser and hydration-aware queries |
| Good for initial page data             | Good for interactive data           |
| Does not require a browser Apollo hook | Can use Apollo React hooks          |
| Can access server-only configuration   | Must not expose secret credentials  |
| Useful for SEO-friendly pages          | Useful for interactive dashboards   |

### **18.8 Important: Avoid Sharing User-Specific Server Cache**

Apollo's server-side client integration is designed to manage request-scoped clients for Server Components.

Be especially careful with authenticated data:

- Do not use a global server cache that shares one user's private data with another.
- Pass the correct authentication context.
- Ensure server-side caching is private or disabled for sensitive results.
- Do not assume a Client Component cache is an authorization mechanism.

### **18.9 Does Next.js Need to Host the GraphQL Server?**

No.

A common architecture is:

```
Next.js Application
       |
       v
Apollo Client
       |
       v
Separate Node.js GraphQL Server
       |
       v
MongoDB
```

This is often a good separation of responsibilities.

You can also host GraphQL within a Next.js Route Handler, but that requires choosing a compatible GraphQL server integration and carefully handling the HTTP runtime and request lifecycle.

For learning Apollo Server and backend architecture, I recommend running the GraphQL server as a separate Node.js application first.

## **19. GraphQL Code Generation**

When working with GraphQL and TypeScript, you should understand GraphQL Code Generator.

GraphQL Code Generator generates TypeScript types and other code from your GraphQL schema and operations.

### **Why Do We Need It?**

Consider:

```
const { data } = useQuery(GET_PRODUCTS);

console.log(data.products);
```

Without generated types, TypeScript may not know the exact shape of `data`.

You may end up using `any`, which reduces type safety.

Code generation can create types for:

- GraphQL schema.
- Queries.
- Mutations.
- Variables.
- Operation results.

### **Example**

Suppose you write:

```
query GetProduct($id: ID!) {
  product(id: $id) {
    id
    name
    price
  }
}
```

Code generation can create TypeScript types conceptually like:

```
type GetProductQueryVariables = {
  id: string;
};

type GetProductQuery = {
  product: {
    id: string;
    name: string;
    price: number;
  } | null;
};
```

The generated types should reflect your actual schema, nullability, and selected fields.

**Benefits:**

1. Better autocomplete.
2. Fewer typing mistakes.
3. Better refactoring support.
4. Consistency between client and server.
5. Less manually maintained TypeScript.

## **20. GraphQL Architecture and Folder Structure**

For a serious Node.js and MongoDB project, avoid putting everything inside one `server.ts` file.

A more organized architecture:

```
graphql-api/
│
├── src/
│   │
│   ├── modules/
│   │   │
│   │   ├── products/
│   │   │   ├── domain/
│   │   │   │   └── Product.ts
│   │   │   │
│   │   │   ├── application/
│   │   │   │   └── CreateProduct.ts
│   │   │   │
│   │   │   ├── infrastructure/
│   │   │   │   └── MongoProductRepository.ts
│   │   │   │
│   │   │   └── graphql/
│   │   │       ├── typeDefs.ts
│   │   │       └── resolvers.ts
│   │   │
│   │   ├── users/
│   │   │   ├── domain/
│   │   │   ├── application/
│   │   │   ├── infrastructure/
│   │   │   └── graphql/
│   │   │
│   │   └── orders/
│   │       ├── domain/
│   │       ├── application/
│   │       ├── infrastructure/
│   │       └── graphql/
│   │
│   ├── graphql/
│   │   ├── schema.ts
│   │   └── context.ts
│   │
│   ├── database/
│   │   └── mongodb.ts
│   │
│   └── server.ts
│
├── package.json
└── tsconfig.json
```

This structure combines:

- Feature-based organization.
- Domain-Driven Design.
- Clean Architecture.
- GraphQL schema and resolver separation.

The GraphQL layer should act as an interface adapter. It should delegate business operations to application Use Cases rather than owning all the business logic.

## **21. Common GraphQL Mistakes**

### **Mistake 1: Treating GraphQL as a Database**

GraphQL does not store data.

It retrieves and modifies data through resolvers that communicate with data sources.

### **Mistake 2: Writing All Logic Inside Resolvers**

Bad:

```
const resolvers = {
  Mutation: {
    createOrder: async (_parent: unknown, args: any) => {
      // Validate everything
      // Calculate pricing
      // Check inventory
      // Process payment
      // Save order
      // Send email
    },
  },
};
```

Better:

```
const resolvers = {
  Mutation: {
    createOrder: async (
      _parent: unknown,
      args: CreateOrderArgs,
      context: GraphQLContext
    ) => {
      return context.orderService.createOrder(args.input);
    },
  },
};
```

Keep business workflows in the application layer.

### **Mistake 3: Ignoring the N+1 Problem**

Nested GraphQL fields can cause excessive database queries.

Use DataLoader and appropriate database query strategies.

### **Mistake 4: Returning Unlimited Records**

Always paginate large collections and enforce maximum page sizes.

### **Mistake 5: No Authorization at Field or Business Boundaries**

A user should not be able to access private data simply because the field exists in the schema.

### **Mistake 6: Using&#x20;**`any`**&#x20;Everywhere**

Define proper types for:

- Context.
- Arguments.
- Repository results.
- Mutation inputs.
- Resolver outputs.

Use GraphQL Code Generator where appropriate.

### **Mistake 7: Assuming One GraphQL Request Means One Database Query**

One GraphQL operation can trigger many resolver executions and database requests.

### **Mistake 8: Using Subscriptions Without Considering Infrastructure**

Subscriptions require persistent connections and a suitable transport and event-delivery architecture.

Plan for reconnects, authentication, horizontal scaling, and distributed event delivery.

### **Mistake 9: Assuming GraphQL Automatically Solves Caching**

GraphQL does not automatically provide the same caching behavior as resource-oriented HTTP GET endpoints.

Plan caching at the client, server, resolver, and data-source levels as appropriate.

### **Mistake 10: Exposing Internal Errors**

Do not send database stack traces or secret values to clients.

## **22. Interview Questions and Answers**

### **Basic Level**

**Q1. What is GraphQL?**

GraphQL is a query language for APIs and a runtime that executes client operations against a defined schema.

**Q2. Who developed GraphQL?**

GraphQL was developed at Facebook, now Meta.

**Q3. What is SDL?**

Schema Definition Language is the syntax used to define GraphQL types and operations.

**Q4. What are the three GraphQL operation types?**

1. Query – Read data.
2. Mutation – Modify data.
3. Subscription – Receive real-time updates.

**Q5. What is a Resolver?**

A Resolver is a function that provides the value for a GraphQL field.

**Q6. What is an Argument?**

An Argument is a named input value passed to a GraphQL field.

**Q7. What is an Input Type?**

An Input Type defines a structured object that clients can provide as an argument, commonly for mutations.

**Q8. What is an Enum?**

An Enum restricts a field or argument to a predefined set of values.

### **Intermediate Level**

**Q9. What is the difference between REST and GraphQL?**

REST typically exposes multiple resource-oriented endpoints, while GraphQL exposes a schema through which clients request specific fields using operations.

**Q10. What is Over-fetching?**

Receiving more data than the client needs.

**Q11. What is Under-fetching?**

Needing multiple requests because one endpoint does not return all required related data.

**Q12. What is Apollo Server?**

Apollo Server is a GraphQL server implementation that executes GraphQL operations using a schema and resolvers.

**Q13. What is GraphQL Context?**

Context is a shared request-level object accessible to resolvers, commonly containing authenticated user information and data-source utilities.

**Q14. What is a Fragment?**

A Fragment is a reusable selection set of fields.

**Q15. What is an Alias?**

An Alias gives a field a different response key, allowing the same field to be requested with different arguments.

**Q16. What is DataLoader?**

DataLoader is a utility that batches and caches data-fetching operations, commonly used to solve the N+1 problem.

**Q17. What is GraphQL Code Generator?**

It generates TypeScript types and other artifacts from GraphQL schemas and operations.

### **Advanced Level**

**Q18. What is the N+1 problem?**

It occurs when fetching a collection requires one initial query and then an additional query for each related object.

**Q19. How do you solve the N+1 problem?**

Use DataLoader, batching, joins or aggregation pipelines, and appropriate data-fetching strategies.

**Q20. What is the difference between Offset and Cursor Pagination?**

Offset pagination uses record positions. Cursor pagination uses a marker identifying where the next page should begin.

**Q21. Does GraphQL support partial responses?**

Yes. A response may contain successfully resolved data alongside errors for fields that failed during execution.

**Q22. Does GraphQL support authentication?**

GraphQL itself does not prescribe authentication. The server can authenticate requests and pass the authenticated user through context.

**Q23. What is GraphQL Subscription?**

A long-lived operation that delivers updates when relevant server-side events occur, commonly over WebSockets or SSE.

**Q24. Does GraphQL automatically prevent SQL injection or NoSQL injection?**

No. Developers must validate and safely handle inputs and use secure database APIs.

**Q25. How do you protect GraphQL from expensive queries?**

Use depth limits, complexity limits, pagination, timeouts, rate limiting, authorization, and trusted documents where appropriate.

**Q26. Can GraphQL work with MongoDB?**

Yes. Resolvers can retrieve and modify MongoDB data through its native driver or an ORM/ODM.

**Q27. Can GraphQL work with REST APIs?**

Yes. Resolvers can call existing REST APIs and combine their results into one GraphQL schema.

**Q28. Can GraphQL and Next.js Server Components work together?**

Yes. Apollo Client provides an App Router integration for querying GraphQL from Server Components and Client Components.

**Q29. Are top-level mutations executed in parallel?**

No. Top-level Mutation fields execute serially, but that does not provide an atomic database transaction.

**Q30. Is GraphQL always better than REST?**

No. The correct choice depends on the application, client requirements, caching needs, team experience, and operational complexity.

## **23. Practical Assignment – Build a GraphQL E-commerce API**

Hands-on project

Build a GraphQL API for an e-commerce application.

### **Technology Stack**

| Component      | Technology           |
| -------------- | -------------------- |
| Frontend       | Next.js 16           |
| Backend        | Node.js + TypeScript |
| GraphQL Server | Apollo Server        |
| GraphQL Client | Apollo Client        |
| Database       | MongoDB              |
| Validation     | Zod                  |
| Batching       | DataLoader           |
| Testing        | Vitest               |

### **Task 1 – Define the Schema**

Create these GraphQL types:

- User
- Product
- Category
- Order
- OrderItem
- Review

Create enums:

- OrderStatus
- UserRole
- ProductSort

Create input types:

- CreateUserInput
- CreateProductInput
- UpdateProductInput
- CreateOrderInput

### **Task 2 – Implement Queries**

Create:

```
type Query {
  products: [Product!]!

  product(id: ID!): Product

  users: [User!]!

  user(id: ID!): User

  orders: [Order!]!

  order(id: ID!): Order
}
```

Add filtering, sorting, and pagination.

### **Task 3 – Implement Mutations**

Create:

```
type Mutation {
  createUser(input: CreateUserInput!): User!

  createProduct(input: CreateProductInput!): Product!

  updateProduct(
    id: ID!
    input: UpdateProductInput!
  ): Product!

  deleteProduct(id: ID!): Boolean!

  createOrder(input: CreateOrderInput!): Order!
}
```

### **Task 4 – Implement Resolvers**

Separate:

- Query resolvers.
- Mutation resolvers.
- Product relationship resolvers.
- Order relationship resolvers.
- User relationship resolvers.

### **Task 5 – Connect MongoDB**

Create collections for:

- Users.
- Products.
- Categories.
- Orders.
- Reviews.

### **Task 6 – Implement Authentication**

- Register user.
- Login user.
- JWT authentication.
- Role-based authorization.
- Protect order information.

### **Task 7 – Implement DataLoader**

Solve the N+1 problem for:

- Order → User.
- Product → Category.
- Product → Reviews.

### **Task 8 – Implement Pagination**

Support:

- Offset pagination.
- Cursor pagination.
- Sorting.
- Filtering.
- Maximum page-size limits.

### **Task 9 – Integrate Next.js**

Build these pages:

- Product listing.
- Product details.
- User profile.
- Order history.
- Admin product management.

Use Apollo Client with the Next.js App Router.

### **Task 10 – Advanced Features**

Implement:

- GraphQL error handling.
- Query depth limits.
- Query complexity controls.
- Rate limiting.
- GraphQL Subscriptions for order updates.
- Unit and integration tests.

### **Assignment Progress Checklist**

**Project progress**

# **0%**

0 of 16 tasks completed

Create GraphQL project using TypeScript

Install and configure Apollo Server

Define GraphQL schema using SDL

Implement Query resolvers

Implement Mutation resolvers

Connect MongoDB

Implement User and Product relationships

Implement JWT authentication

Implement role-based authorization

Solve N+1 using DataLoader

Implement pagination and filtering

Integrate Apollo Client with Next.js

Build Product and Order pages

Implement GraphQL error handling

Implement GraphQL security controls

Write unit and integration tests

## **24. Quick Revision Notes**

| Concept           | Remember                                          |
| ----------------- | ------------------------------------------------- |
| GraphQL           | Query language and API execution runtime          |
| SDL               | Defines GraphQL schema                            |
| Schema            | Contract describing available data and operations |
| Scalar            | Primitive data type                               |
| Object Type       | Structured output type                            |
| Enum              | Predefined allowed values                         |
| Input Type        | Structured input data                             |
| Query             | Reads data                                        |
| Mutation          | Modifies data                                     |
| Subscription      | Receives real-time updates                        |
| Resolver          | Fetches a field's value                           |
| Arguments         | Inputs passed to fields                           |
| Context           | Shared request-level information                  |
| Fragment          | Reusable selection set                            |
| Alias             | Changes response field name                       |
| Directive         | Modifies field execution or inclusion             |
| Apollo Server     | GraphQL server implementation                     |
| Apollo Client     | GraphQL client library                            |
| DataLoader        | Batches and caches data loading                   |
| N+1 Problem       | Excessive related-data queries                    |
| Cursor Pagination | Pagination using position markers                 |
| Code Generator    | Generates TypeScript types from GraphQL           |
| Query Complexity  | Estimates the cost of an operation                |

### **The 8 Most Important Things to Remember**

1. GraphQL allows clients to select the fields they need.
2. SDL defines the schema and API contract.
3. Queries read data; Mutations modify data; Subscriptions deliver updates.
4. Resolvers determine how fields are fetched.
5. Apollo Server executes GraphQL operations on the backend.
6. DataLoader helps solve the N+1 problem.
7. Authentication and authorization must be enforced on the server.
8. GraphQL requires deliberate performance, pagination, and security controls.

## **25. Phase 30 Completion Checklist**

**Learning progress**

# **0/17**

I understand GraphQL and its purpose.

I can compare REST and GraphQL.

I understand GraphQL SDL and scalar types.

I understand Object, Input and Enum types.

I can write Queries and Mutations.

I understand Variables, Aliases and Fragments.

I understand Resolvers and their arguments.

I understand GraphQL Context.

I can create an Apollo Server.

I can connect GraphQL with MongoDB.

I understand authentication and authorization.

I understand the N+1 problem and DataLoader.

I can implement pagination and filtering.

I understand GraphQL error handling.

I understand Subscriptions and WebSockets.

I can integrate Apollo Client with Next.js.

I understand GraphQL security and performance.

## **26. Next Phase Preview**

Phase 31 – Advanced API Architecture

# **WebSockets, Socket.IO and Real-Time Applications**

In the next phase, we will learn how to build real-time applications using Node.js and Next.js.

**Topics planned:**

1. What Is Real-Time Communication?
2. HTTP Request-Response vs WebSockets.
3. What Is WebSocket?
4. WebSocket Handshake.
5. Full-Duplex Communication.
6. WebSocket API in Node.js.
7. Socket.IO Architecture.
8. Events and Event Emitters.
9. Rooms and Namespaces.
10. Broadcasting.
11. Authentication and Authorization.
12. Real-Time Chat Application.
13. Online and Offline Presence.
14. Typing Indicators and Read Receipts.
15. Redis Adapter and Horizontal Scaling.
16. WebSockets with Next.js.
17. WebSockets vs Socket.IO vs SSE.
18. Production Deployment and Security.

**Phase 30 is complete.** When you're ready, say **“OK, Phase 31”** and we'll continue with detailed notes.

### **References and Official Documentation**

1. [GraphQL – Queries](https://graphql.org/learn/queries/?utm_source=chatgpt.com).
2. [GraphQL – Schemas and Types](https://graphql.org/learn/schema/?utm_source=chatgpt.com).
3. [GraphQL – Mutations](https://graphql.org/learn/mutations/?utm_source=chatgpt.com).
4. [GraphQL – Execution and Resolvers](https://graphql.org/learn/execution/?utm_source=chatgpt.com).
5. [Apollo Server – Getting Started](https://www.apollographql.com/docs/apollo-server/getting-started?utm_source=chatgpt.com).
6. [Apollo Client – Next.js App Router Integration](https://www.apollographql.com/docs/react/integrations/nextjs?utm_source=chatgpt.com).
7. [Apollo Server – Authentication and Authorization](https://www.apollographql.com/docs/apollo-server/security/authentication?utm_source=chatgpt.com).
8. [Apollo Server – Subscriptions](https://www.apollographql.com/docs/apollo-server/data/subscriptions?utm_source=chatgpt.com).
9. [GraphQL – Security Best Practices](https://graphql.org/learn/security/?utm_source=chatgpt.com).
10. [GraphQL – Pagination](https://graphql.org/learn/pagination/?utm_source=chatgpt.com).