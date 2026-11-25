# Phase 63 – Advanced GraphQL, Apollo Server, GraphQL APIs and Enterprise API Development

**Level:** Advanced to Expert  
**Domain:** Backend Development, API Architecture, Distributed Systems and Full-Stack Development  
**Primary Technologies:** GraphQL, Apollo Server 5, Apollo Client, Node.js, Express.js, TypeScript, React, Next.js, MySQL, MongoDB, Prisma and DataLoader.

---

# 1. Learning Objectives

In this phase, you will learn GraphQL from its fundamentals to enterprise-level API development.

We will cover:

1. API Architecture Revision.
2. REST API vs GraphQL.
3. GraphQL Fundamentals.
4. GraphQL Architecture.
5. GraphQL Schema Definition Language (SDL).
6. GraphQL Types and Fields.
7. Object Types.
8. Scalar Types.
9. Enum Types.
10. Input Types.
11. Lists and Non-Null Types.
12. Queries.
13. Mutations.
14. Subscriptions.
15. Resolvers.
16. Resolver Arguments.
17. Resolver Context.
18. Resolver Information.
19. Root Resolvers.
20. GraphQL Schema Design.
21. Schema-First Development.
22. Code-First Development.
23. GraphQL with Node.js.
24. GraphQL with Express.
25. Apollo Server.
26. Apollo Client.
27. GraphQL with React.
28. GraphQL with Next.js.
29. GraphQL with MongoDB.
30. GraphQL with MySQL.
31. GraphQL with Prisma.
32. Authentication.
33. Authorization.
34. Role-Based Access Control.
35. DataLoader.
36. N+1 Query Problem.
37. Query Complexity.
38. Query Depth Limiting.
39. Pagination.
40. Cursor-Based Pagination.
41. Error Handling.
42. Custom Scalars.
43. Custom Directives.
44. GraphQL Middleware.
45. File Uploads.
46. Real-Time Subscriptions.
47. GraphQL Caching.
48. GraphQL Security.
49. GraphQL Testing.
50. GraphQL Federation.
51. Microservices with GraphQL.
52. Enterprise GraphQL Architecture.
53. GraphQL Interview Questions.
54. Complete GraphQL Project.

---

# PART 1: API ARCHITECTURE REVISION

## 2. What Is an API?

API stands for **Application Programming Interface**.

It allows different software applications to communicate with each other.

For example, consider an Expense Tracker application.

You have:

- Next.js frontend.
- Node.js backend.
- MySQL database.

The frontend needs expense information from the backend.

The communication looks like this:

```text
       Next.js Frontend
              |
              | API Request
              v
       Node.js Backend
              |
              | Database Query
              v
          MySQL DB
              |
              | Database Result
              v
       Node.js Backend
              |
              | API Response
              v
       Next.js Frontend
```

The API defines how the frontend communicates with the backend.

## 3. Common API Architectures

| API Architecture | Description |
|---|---|
| REST | Uses resources and HTTP methods |
| GraphQL | Uses a schema and flexible data queries |
| SOAP | XML-based messaging protocol |
| gRPC | High-performance RPC framework using Protocol Buffers |
| WebSocket API | Supports persistent bidirectional communication |
| Webhooks | Allows systems to send event notifications to other systems |

In this phase, our primary focus is GraphQL.

---

# PART 2: WHAT IS GRAPHQL?

## 4. Introduction to GraphQL

GraphQL is a query language for APIs and a server-side runtime for executing those queries.

It was developed at Facebook and made open source in 2015.

GraphQL allows clients to request exactly the fields they need.

Example:

Suppose your backend contains this user:

```json
{
  "id": 1,
  "name": "John",
  "email": "john@example.com",
  "age": 25,
  "phone": "1234567890",
  "address": "Ahmedabad"
}
```

Your frontend only needs the user's name and email.

With GraphQL:

```graphql
query {
  user(id: "1") {
    name
    email
  }
}
```

Response:

```json
{
  "data": {
    "user": {
      "name": "John",
      "email": "john@example.com"
    }
  }
}
```

The client receives only the selected fields.

### Important Characteristics

1. Strongly typed schema.
2. Client-controlled selection of response fields.
3. Usually one HTTP endpoint for multiple operations.
4. Supports queries, mutations and subscriptions.
5. Supports nested relationships.
6. Has a standardized type system.
7. Can connect to multiple data sources.
8. Supports introspection and schema documentation.

**Important:** GraphQL does not require a particular database or programming language. It can work with MySQL, MongoDB, PostgreSQL, REST APIs and other data sources.

---

# PART 3: REST API VS GRAPHQL

## 5. REST API

REST stands for Representational State Transfer.

It is an architectural style for designing network-based applications.

In REST, APIs generally expose different endpoints for different resources.

Example:

```text
GET    /api/users
GET    /api/users/1
GET    /api/users/1/orders
POST   /api/users
PUT    /api/users/1
DELETE /api/users/1
```

Each endpoint performs a particular operation.

## 6. GraphQL API

GraphQL usually exposes a single endpoint:

```text
POST /graphql
```

The client sends different GraphQL operations to this endpoint.

Example:

```graphql
query {
  user(id: "1") {
    name
    email
    orders {
      id
      total
    }
  }
}
```

The GraphQL server determines how to retrieve the requested data.

## 7. REST vs GraphQL Comparison

| Feature | REST | GraphQL |
|---|---|---|
| API structure | Multiple resource endpoints | Usually one GraphQL endpoint |
| Data fetching | Server-defined response | Client selects fields |
| Over-fetching | Can occur | Reduced through field selection |
| Under-fetching | May require multiple requests | Nested queries can combine related data |
| HTTP methods | GET, POST, PUT, PATCH, DELETE | Commonly POST for operations; GET may be used for queries |
| Schema | Often described separately | Built-in schema system |
| Type system | Not mandatory | Strong schema type system |
| Caching | HTTP caching is well established | Requires GraphQL-aware caching strategies |
| Real-time | Often uses WebSockets or SSE separately | Supports subscription operations |
| Complexity | Often simpler for basic APIs | Requires query execution and demand controls |

### What Is Over-fetching?

Over-fetching happens when the client receives more data than it needs.

REST example:

```http
GET /api/users/1
```

Response:

```json
{
  "id": 1,
  "name": "John",
  "email": "john@example.com",
  "age": 25,
  "phone": "1234567890",
  "address": "Ahmedabad",
  "createdAt": "2026-01-01"
}
```

But the frontend only needs the name.

GraphQL allows:

```graphql
query {
  user(id: "1") {
    name
  }
}
```

### What Is Under-fetching?

Under-fetching happens when one request does not provide all the data needed by the client.

REST example:

```http
GET /api/users/1
GET /api/users/1/orders
GET /api/users/1/addresses
```

GraphQL can request all these relationships in one operation:

```graphql
query {
  user(id: "1") {
    name

    orders {
      id
      total
    }

    addresses {
      city
      country
    }
  }
}
```

### When Should You Use REST?

Use REST when:

- Your API has simple resource-based operations.
- HTTP caching is important.
- Your team prefers straightforward endpoint contracts.
- You are building public APIs with conventional HTTP semantics.
- Your application does not need highly flexible data selection.

### When Should You Use GraphQL?

Use GraphQL when:

- Multiple frontend applications need different data shapes.
- Your application has many related entities.
- Frontend teams need flexibility.
- You want a centralized typed API contract.
- You need to aggregate data from several backend services.

**Important:** GraphQL is not automatically faster than REST. Poor resolver design can make a GraphQL API slower than a well-designed REST API.

---

# PART 4: GRAPHQL ARCHITECTURE

## 8. How GraphQL Works

Consider an Expense Tracker.

The application contains:

- Users.
- Expenses.
- Categories.
- Reports.

A client requests:

```graphql
query {
  expenses {
    id
    amount
    category {
      name
    }
  }
}
```

The GraphQL execution process looks like this:

```text
             GraphQL Client
                    |
                    v
             GraphQL Request
                    |
                    v
               GraphQL API
                    |
                    v
              Parse Document
                    |
                    v
             Validate Schema
                    |
                    v
             Execute Operation
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Expense   Category   Other
       Resolver  Resolver   Resolver
          |         |         |
          +---------+---------+
                    |
                    v
             Business Services
                    |
                    v
               Repositories
                    |
                    v
                 Database
                    |
                    v
             GraphQL Response
                    |
                    v
               GraphQL Client
```

## 9. Main Components of GraphQL

| Component | Responsibility |
|---|---|
| Schema | Defines available API capabilities |
| Types | Define the structure of data |
| Query | Reads data |
| Mutation | Changes data |
| Subscription | Receives real-time events |
| Resolver | Retrieves or modifies data |
| Context | Shares request-level information |
| Scalar | Represents a primitive value |
| Input Type | Defines structured input |
| Directive | Modifies schema or operation behavior |

The schema defines what clients can request. Resolvers determine how requested fields are fulfilled. [GraphQL](https://graphql.org/learn/schema/?utm_source=chatgpt.com)

---

# PART 5: GRAPHQL DEVELOPMENT ENVIRONMENT

## 10. Create a GraphQL Node.js Project

We will use:

- Node.js.
- TypeScript.
- Apollo Server 5.
- GraphQL.
- Express later for integration.

Apollo Server 5 is the current major version covered by Apollo's documentation. Apollo Server 3 is end-of-life, so avoid starting new projects with Apollo Server 3. [apollographql.com](https://www.apollographql.com/docs/apollo-server?trk=article-ssr-frontend-pulse_little-text-block\&utm_source=chatgpt.com)

Create a project:

```bash
mkdir graphql-project

cd graphql-project

npm init -y
```

Set up ES modules:

```bash
npm pkg set type="module"
```

Install dependencies:

```bash
npm install @apollo/server graphql
```

Install development dependencies:

```bash
npm install -D typescript tsx @types/node
```

Initialize TypeScript:

```bash
npx tsc --init
```

Example project structure:

```text
graphql-project/
│
├── src/
│   ├── schema/
│   │   └── schema.ts
│   │
│   ├── resolvers/
│   │   └── resolvers.ts
│   │
│   ├── data/
│   │   └── users.ts
│   │
│   └── index.ts
│
├── package.json
└── tsconfig.json
```

Add scripts to `package.json`:

```json
{
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "start": "node dist/index.js",
    "typecheck": "tsc --noEmit"
  }
}
```

---

# PART 6: GRAPHQL SCHEMA DEFINITION LANGUAGE

## 11. What Is SDL?

SDL stands for **Schema Definition Language**.

It is a language used to define GraphQL schemas.

Example:

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  age: Int
}
```

This defines a User object type.

The `User` type has four fields:

- id.
- name.
- email.
- age.

The schema defines what information can be requested from the GraphQL API.

## 12. GraphQL Schema

A GraphQL schema defines the types and operations supported by an API.

Example:

```graphql
type User {
  id: ID!
  name: String!
}

type Query {
  users: [User!]!
  user(id: ID!): User
}
```

This schema says:

1. The API has a User type.
2. The API supports fetching all users.
3. The API supports fetching a particular user.
4. The user lookup requires an ID.

### Schema Structure

```text
GraphQL Schema
      |
      +---- Object Types
      |
      +---- Scalar Types
      |
      +---- Enum Types
      |
      +---- Input Types
      |
      +---- Query
      |
      +---- Mutation
      |
      +---- Subscription
```

---

# PART 7: GRAPHQL TYPE SYSTEM

## 13. Scalar Types

GraphQL provides five built-in scalar types.

| Scalar | Description |
|---|---|
| Int | Signed 32-bit integer |
| Float | Floating-point number |
| String | Text |
| Boolean | True or false |
| ID | Identifier, commonly serialized as a string |

Example:

```graphql
type User {
  id: ID!
  name: String!
  age: Int
  salary: Float
  active: Boolean!
}
```

### Important

GraphQL's built-in `Int` is a signed 32-bit integer, not an arbitrary-size integer.

For large integer identifiers or monetary values requiring exact precision, consider custom scalars or serialized strings.

## 14. Object Types

An Object Type describes a structured entity.

Example:

```graphql
type Product {
  id: ID!
  name: String!
  price: Float!
  description: String
}
```

A Product contains multiple fields.

## 15. Enum Types

An Enum restricts a field to a predefined set of values.

Example:

```graphql
enum ExpenseStatus {
  PENDING
  APPROVED
  REJECTED
}
```

Usage:

```graphql
type Expense {
  id: ID!
  amount: Float!
  status: ExpenseStatus!
}
```

Now an expense status must be one of the declared enum values.

Enums are useful for:

- User roles.
- Order status.
- Payment status.
- Expense status.
- Notification types.

## 16. Input Types

Input Types define structured data that clients can send to mutations or field arguments.

Example:

```graphql
input CreateUserInput {
  name: String!
  email: String!
  age: Int
}
```

Usage:

```graphql
type Mutation {
  createUser(input: CreateUserInput!): User!
}
```

Client request:

```graphql
mutation {
  createUser(
    input: {
      name: "John"
      email: "john@example.com"
      age: 25
    }
  ) {
    id
    name
  }
}
```

### Object Type vs Input Type

| Object Type | Input Type |
|---|---|
| Used to describe returned data | Used to describe input data |
| Declared using `type` | Declared using `input` |
| Can contain object relationships | Cannot directly contain output object types |
| Used in query results | Used in arguments and mutations |

## 17. List Types

Square brackets `[]` represent a list.

```graphql
type Query {
  users: [User]
}
```

This means the field returns a list of User values, but the list and its individual items may be null.

## 18. Non-Null Types

The exclamation mark `!` means a value cannot be null.

Example:

```graphql
type User {
  id: ID!
  name: String!
  email: String
}
```

Here:

- `id` cannot be null.
- `name` cannot be null.
- `email` can be null.

### List and Non-Null Combinations

| Type | Meaning |
|---|---|
| `[User]` | Nullable list of nullable users |
| `[User]!` | Non-null list of nullable users |
| `[User!]` | Nullable list of non-null users |
| `[User!]!` | Non-null list of non-null users |

This distinction is very important in GraphQL schema design.

For example:

```graphql
type Query {
  users: [User!]!
}
```

The result must be a list, and every element must be a valid non-null User.

---

# PART 8: GRAPHQL QUERIES

## 19. What Is a Query?

A Query is used to retrieve data from a GraphQL API.

REST equivalent:

```http
GET /api/users
```

GraphQL:

```graphql
query {
  users {
    id
    name
    email
  }
}
```

The client specifies which fields it wants.

## 20. Query With Arguments

Schema:

```graphql
type Query {
  user(id: ID!): User
}
```

Request:

```graphql
query {
  user(id: "101") {
    id
    name
    email
  }
}
```

The `id` argument identifies the requested user.

## 21. Named Queries

A named query gives an operation a descriptive name.

```graphql
query GetUser {
  user(id: "101") {
    id
    name
  }
}
```

Naming operations improves debugging, logging and monitoring.

## 22. Query Variables

Instead of hardcoding values, you can use variables.

```graphql
query GetUser($userId: ID!) {
  user(id: $userId) {
    id
    name
    email
  }
}
```

Variables:

```json
{
  "userId": "101"
}
```

Benefits:

- Reusable queries.
- Better separation of data and operations.
- Improved client-side caching.
- Easier validation.

## 23. Aliases

Aliases allow you to rename response fields or request the same field multiple times with different arguments.

Example:

```graphql
query {
  firstUser: user(id: "101") {
    name
  }

  secondUser: user(id: "102") {
    name
  }
}
```

Response:

```json
{
  "data": {
    "firstUser": {
      "name": "John"
    },
    "secondUser": {
      "name": "David"
    }
  }
}
```

## 24. Fragments

Fragments allow you to reuse field selections.

Example:

```graphql
fragment UserDetails on User {
  id
  name
  email
}
```

Use the fragment:

```graphql
query {
  users {
    ...UserDetails
  }
}
```

Fragments are especially useful when multiple components need the same fields.

## 25. Directives in Queries

GraphQL provides built-in directives.

### @include

Includes a field when a condition is true.

```graphql
query GetUser($includeEmail: Boolean!) {
  user(id: "101") {
    id
    name
    email @include(if: $includeEmail)
  }
}
```

### @skip

Skips a field when a condition is true.

```graphql
query GetUser($skipEmail: Boolean!) {
  user(id: "101") {
    id
    name
    email @skip(if: $skipEmail)
  }
}
```

---

# PART 9: GRAPHQL MUTATIONS

## 26. What Is a Mutation?

A Mutation is used to modify data.

Common operations:

- Create.
- Update.
- Delete.
- Approve.
- Reject.
- Transfer.

REST example:

```http
POST /api/users
```

GraphQL example:

```graphql
mutation {
  createUser(
    input: {
      name: "John"
      email: "john@example.com"
    }
  ) {
    id
    name
    email
  }
}
```

## 27. Mutation With Variables

```graphql
mutation CreateUser($input: CreateUserInput!) {
  createUser(input: $input) {
    id
    name
    email
  }
}
```

Variables:

```json
{
  "input": {
    "name": "John",
    "email": "john@example.com"
  }
}
```

## 28. Update Mutation

Schema:

```graphql
input UpdateUserInput {
  name: String
  email: String
}

type Mutation {
  updateUser(
    id: ID!
    input: UpdateUserInput!
  ): User
}
```

Request:

```graphql
mutation {
  updateUser(
    id: "101"
    input: {
      name: "John Updated"
    }
  ) {
    id
    name
  }
}
```

## 29. Delete Mutation

Schema:

```graphql
type Mutation {
  deleteUser(id: ID!): Boolean!
}
```

Request:

```graphql
mutation {
  deleteUser(id: "101")
}
```

Response:

```json
{
  "data": {
    "deleteUser": true
  }
}
```

### Important

GraphQL does not automatically provide database transactions.

If a mutation changes multiple database records, your resolver or application service must implement the appropriate transaction logic.

---

# PART 10: GRAPHQL SUBSCRIPTIONS

## 30. What Is a Subscription?

A Subscription allows clients to receive real-time updates from a GraphQL server.

Queries retrieve data.

Mutations change data.

Subscriptions listen for events.

Example:

Suppose your Expense Tracker has an approval system.

When an administrator approves an expense, other connected users should receive an update.

Subscription:

```graphql
subscription ExpenseApproved {
  expenseApproved {
    id
    amount
    status
  }
}
```

The server publishes an event when an expense is approved.

### Common Use Cases

- Chat applications.
- Live notifications.
- Real-time dashboards.
- Stock price updates.
- Order tracking.
- Collaborative applications.

### Subscription Architecture

```text
          Client A
              |
              | Subscribe
              v
         GraphQL Server
              |
              v
          Pub/Sub System
              |
              v
         Expense Approved
              |
              v
          Publish Event
              |
        +-----+-----+
        |           |
        v           v
     Client A    Client B
        |           |
        +-----+-----+
              |
              v
        Real-Time Update
```

GraphQL subscriptions are commonly implemented using a long-lived transport such as WebSockets. The GraphQL specification does not mandate one particular transport. [GraphQL](https://graphql.org/learn/subscriptions/?utm_source=chatgpt.com)

---

# PART 11: RESOLVERS

## 31. What Is a Resolver?

A Resolver is a function responsible for providing the value of a GraphQL field.

Consider:

```graphql
type Query {
  hello: String!
}
```

The resolver:

```typescript
const resolvers = {
  Query: {
    hello: () => {
      return "Hello GraphQL";
    }
  }
};
```

When a client executes:

```graphql
query {
  hello
}
```

The resolver returns:

```json
{
  "data": {
    "hello": "Hello GraphQL"
  }
}
```

## 32. Resolver Function Structure

A resolver commonly receives four arguments:

```typescript
(parent, args, context, info)
```

| Argument | Meaning |
|---|---|
| parent | Value returned by the parent resolver |
| args | Arguments provided to the field |
| context | Shared request-level data |
| info | Execution and schema information |

Example:

```typescript
const resolvers = {
  Query: {
    user: (
      parent: unknown,
      args: { id: string },
      context: unknown,
      info: unknown
    ) => {
      return getUserById(args.id);
    }
  }
};
```

In production, use generated or explicitly defined resolver types rather than leaving all arguments as `unknown`.

## 33. Root Resolvers

The root resolver handles fields defined on the root operation types.

Example:

```graphql
type Query {
  users: [User!]!
  user(id: ID!): User
}

type Mutation {
  createUser(input: CreateUserInput!): User!
}
```

Resolver:

```typescript
const resolvers = {
  Query: {
    users: () => getUsers(),

    user: (
      parent: unknown,
      args: { id: string }
    ) => getUserById(args.id)
  },

  Mutation: {
    createUser: (
      parent: unknown,
      args: { input: CreateUserInput }
    ) => createUser(args.input)
  }
};
```

## 34. Nested Resolvers

Suppose a user has many expenses.

Schema:

```graphql
type User {
  id: ID!
  name: String!
  expenses: [Expense!]!
}

type Expense {
  id: ID!
  amount: Float!
}
```

Resolver:

```typescript
const resolvers = {
  User: {
    expenses: (user: User) => {
      return getExpensesByUserId(user.id);
    }
  }
};
```

When the client requests:

```graphql
query {
  users {
    name
    expenses {
      amount
    }
  }
}
```

GraphQL resolves the User fields and then invokes the nested `expenses` resolver for each user.

---

# PART 12: GRAPHQL CONTEXT

## 35. What Is Context?

Context is an object shared across the resolver execution of a particular request.

It is commonly used to provide:

- Authenticated user information.
- Database clients.
- DataLoaders.
- Logger instances.
- Request metadata.
- Service instances.

Example:

```typescript
interface Context {
  user: {
    id: string;
    role: string;
  } | null;

  services: {
    userService: UserService;
  };
}
```

A resolver can access context:

```typescript
const resolvers = {
  Query: {
    me: (
      parent: unknown,
      args: unknown,
      context: Context
    ) => {
      return context.user;
    }
  }
};
```

### Important

Context should be created for each request.

Do not store one user's authentication information in a global shared variable. That can expose user data across requests.

---

# PART 13: GRAPHQL WITH NODE.JS AND APOLLO SERVER

## 36. What Is Apollo Server?

Apollo Server is a GraphQL server implementation for Node.js.

It provides functionality for:

- Executing GraphQL operations.
- Connecting schemas and resolvers.
- Integrating with HTTP frameworks.
- Request context.
- Plugins.
- Error handling.
- Schema management.

Apollo Server 5 supports standalone usage and integrations with frameworks such as Express. [apollographql.com](https://www.apollographql.com/docs/apollo-server?trk=article-ssr-frontend-pulse_little-text-block\&utm_source=chatgpt.com)

## 37. Create Your First Apollo Server

Create `src/index.ts`:

```typescript
import { ApolloServer } from "@apollo/server";

import { startStandaloneServer } from "@apollo/server/standalone";

const typeDefs = `#graphql
  type Query {
    hello: String!
  }
`;

const resolvers = {
  Query: {
    hello: () => "Hello GraphQL"
  }
};

const server = new ApolloServer({
  typeDefs,
  resolvers
});

const { url } = await startStandaloneServer(server, {
  listen: {
    port: 4000
  }
});

console.log(`Server running at ${url}`);
```

Run:

```bash
npm run dev
```

You now have a basic GraphQL API.

### Test Query

```graphql
query {
  hello
}
```

Expected response:

```json
{
  "data": {
    "hello": "Hello GraphQL"
  }
}
```

Official reference: [Apollo Server Getting Started](https://www.apollographql.com/docs/apollo-server/getting-started). [apollographql.com](https://www.apollographql.com/docs/apollo-server/getting-started?utm_source=chatgpt.com)

---

# PART 14: COMPLETE GRAPHQL USER API

## 38. Create User Data

Create `src/data/users.ts`:

```typescript
export interface User {
  id: string;
  name: string;
  email: string;
  age: number;
}

export const users: User[] = [
  {
    id: "1",
    name: "John",
    email: "john@example.com",
    age: 25
  },
  {
    id: "2",
    name: "David",
    email: "david@example.com",
    age: 28
  }
];
```

## 39. Create GraphQL Schema

Create `src/schema/schema.ts`:

```typescript
export const typeDefs = `#graphql

  type User {
    id: ID!
    name: String!
    email: String!
    age: Int!
  }

  input CreateUserInput {
    name: String!
    email: String!
    age: Int!
  }

  input UpdateUserInput {
    name: String
    email: String
    age: Int
  }

  type Query {
    users: [User!]!
    user(id: ID!): User
  }

  type Mutation {
    createUser(input: CreateUserInput!): User!
    updateUser(id: ID!, input: UpdateUserInput!): User
    deleteUser(id: ID!): Boolean!
  }

`;
```

## 40. Create Resolvers

Create `src/resolvers/resolvers.ts`:

```typescript
import {
  users,
  type User
} from "../data/users.js";

interface CreateUserInput {
  name: string;
  email: string;
  age: number;
}

interface UpdateUserInput {
  name?: string;
  email?: string;
  age?: number;
}

export const resolvers = {
  Query: {
    users: (): User[] => {
      return users;
    },

    user: (
      _parent: unknown,
      args: { id: string }
    ): User | undefined => {
      return users.find(user => user.id === args.id);
    }
  },

  Mutation: {
    createUser: (
      _parent: unknown,
      args: { input: CreateUserInput }
    ): User => {
      const user: User = {
        id: String(Date.now()),
        ...args.input
      };

      users.push(user);

      return user;
    },

    updateUser: (
      _parent: unknown,
      args: {
        id: string;
        input: UpdateUserInput;
      }
    ): User | null => {
      const user = users.find(
        user => user.id === args.id
      );

      if (!user) {
        return null;
      }

      Object.assign(user, args.input);

      return user;
    },

    deleteUser: (
      _parent: unknown,
      args: { id: string }
    ): boolean => {
      const index = users.findIndex(
        user => user.id === args.id
      );

      if (index === -1) {
        return false;
      }

      users.splice(index, 1);

      return true;
    }
  }
};
```

This is an educational in-memory implementation. A production API should use persistent storage, input validation, authorization and proper ID generation.

## 41. Connect Schema and Resolvers

Update `src/index.ts`:

```typescript
import { ApolloServer } from "@apollo/server";

import {
  startStandaloneServer
} from "@apollo/server/standalone";

import { typeDefs } from "./schema/schema.js";

import { resolvers } from "./resolvers/resolvers.js";

const server = new ApolloServer({
  typeDefs,
  resolvers
});

const { url } = await startStandaloneServer(server, {
  listen: {
    port: 4000
  }
});

console.log(`GraphQL API running at ${url}`);
```

## 42. Test Your API

### Fetch All Users

```graphql
query {
  users {
    id
    name
    email
  }
}
```

### Fetch One User

```graphql
query {
  user(id: "1") {
    name
    email
  }
}
```

### Create User

```graphql
mutation {
  createUser(
    input: {
      name: "Michael"
      email: "michael@example.com"
      age: 30
    }
  ) {
    id
    name
    email
  }
}
```

### Update User

```graphql
mutation {
  updateUser(
    id: "1"
    input: {
      name: "John Updated"
    }
  ) {
    id
    name
  }
}
```

### Delete User

```graphql
mutation {
  deleteUser(id: "2")
}
```

---

# PART 15: SCHEMA-FIRST VS CODE-FIRST DEVELOPMENT

## 43. What Is Schema-First Development?

Schema-First Development means defining the GraphQL schema first and implementing resolvers afterward.

Example:

```graphql
type User {
  id: ID!
  name: String!
}

type Query {
  users: [User!]!
}
```

Then implement resolvers:

```typescript
const resolvers = {
  Query: {
    users: () => getUsers()
  }
};
```

### Advantages

- API contract is clear.
- Frontend and backend teams can work independently.
- Schema is easy to review.
- GraphQL SDL is language-independent.

## 44. What Is Code-First Development?

Code-First Development means defining GraphQL types and schema through programming-language code.

Conceptually:

```typescript
const UserType = createObjectType({
  name: "User",
  fields: {
    id: "ID!",
    name: "String!"
  }
});
```

Actual code-first libraries provide their own APIs for decorators, classes or schema builders.

### Advantages

- Types can be shared or derived from application code.
- Useful in strongly typed environments.
- May reduce duplicate declarations.
- Can integrate well with TypeScript.

### Comparison

| Schema-First | Code-First |
|---|---|
| SDL defines the schema | Programming code defines the schema |
| Clear API contract | Code-centric workflow |
| Language-independent | Often framework-specific |
| Useful for API-first development | Useful for code-centric teams |

Neither approach is universally better.

---

# PART 16: GRAPHQL WITH EXPRESS.JS

## 45. Why Integrate GraphQL With Express?

You may already have an Express backend with:

- Authentication middleware.
- Logging middleware.
- Rate limiting.
- Existing REST routes.
- Database connections.

You can add GraphQL to the existing application instead of replacing everything.

### Install Packages

```bash
npm install express cors @apollo/server graphql

npm install @as-integrations/express5

npm install -D @types/express @types/cors
```

For Express 4, use the corresponding `@as-integrations/express4` integration instead. Apollo documents separate integrations for Express 4 and Express 5. [apollographql.com](https://www.apollographql.com/docs/apollo-server/integrations/integration-index?utm_source=chatgpt.com)

## 46. Express + Apollo Server Example

```typescript
import express from "express";

import cors from "cors";

import { ApolloServer } from "@apollo/server";

import {
  expressMiddleware
} from "@as-integrations/express5";

const app = express();

const typeDefs = `#graphql
  type Query {
    hello: String!
  }
`;

const resolvers = {
  Query: {
    hello: () => "Hello from Express GraphQL"
  }
};

interface Context {
  requestId: string;
}

const server = new ApolloServer<Context>({
  typeDefs,
  resolvers
});

await server.start();

app.use(
  "/graphql",
  cors(),
  express.json(),
  expressMiddleware(server, {
    context: async ({ req }) => ({
      requestId: String(req.headers["x-request-id"] ?? "unknown")
    })
  })
);

app.listen(4000, () => {
  console.log("Express server running on port 4000");
});
```

### Important

Apollo Server's standalone server is convenient for basic projects.

Express integration is more appropriate when you need HTTP middleware and more control over your existing application.

---

# PART 17: APOLLO CLIENT

## 47. What Is Apollo Client?

Apollo Client is a GraphQL client library used by frontend applications.

It allows React applications to:

- Execute queries.
- Execute mutations.
- Subscribe to updates.
- Cache query results.
- Manage loading and error states.
- Update cached data.

### Architecture

```text
       React Application
              |
              v
         Apollo Client
              |
              v
          GraphQL API
              |
              v
         Apollo Server
              |
              v
           Database
```

## 48. Install Apollo Client

For a React application:

```bash
npm install @apollo/client graphql rxjs
```

## 49. Configure Apollo Client

Create `src/apollo-client.ts`:

```typescript
import {
  ApolloClient,
  InMemoryCache,
  HttpLink
} from "@apollo/client";

export const client = new ApolloClient({
  link: new HttpLink({
    uri: "http://localhost:4000/graphql"
  }),

  cache: new InMemoryCache()
});
```

## 50. Configure Apollo Provider

```tsx
import { ApolloProvider } from "@apollo/client/react";

import { client } from "./apollo-client";

function App() {
  return (
    <ApolloProvider client={client}>
      <Users />
    </ApolloProvider>
  );
}
```

## 51. Execute GraphQL Queries in React

```tsx
import { gql } from "@apollo/client";

import { useQuery } from "@apollo/client/react";

const GET_USERS = gql`
  query GetUsers {
    users {
      id
      name
      email
    }
  }
`;

interface User {
  id: string;
  name: string;
  email: string;
}

function Users() {
  const {
    loading,
    error,
    data
  } = useQuery<{ users: User[] }>(GET_USERS);

  if (loading) {
    return <p>Loading...</p>;
  }

  if (error) {
    return <p>Error: {error.message}</p>;
  }

  return (
    <div>
      {data?.users.map(user => (
        <div key={user.id}>
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}
```

## 52. Execute Mutations in React

```tsx
import { gql } from "@apollo/client";

import { useMutation } from "@apollo/client/react";

const CREATE_USER = gql`
  mutation CreateUser($input: CreateUserInput!) {
    createUser(input: $input) {
      id
      name
      email
    }
  }
`;

function CreateUserForm() {
  const [createUser, { loading, error }] =
    useMutation(CREATE_USER);

  async function handleCreate() {
    await createUser({
      variables: {
        input: {
          name: "John",
          email: "john@example.com",
          age: 25
        }
      }
    });
  }

  return (
    <div>
      <button
        onClick={handleCreate}
        disabled={loading}
      >
        Create User
      </button>

      {error && <p>{error.message}</p>}
    </div>
  );
}
```

For larger applications, use GraphQL Code Generator to generate TypeScript types from your schema and operations instead of manually maintaining query result types.

---

# PART 18: GRAPHQL WITH NEXT.JS

## 53. GraphQL in Next.js

Next.js can use GraphQL APIs from:

- Server Components.
- Client Components.
- Route Handlers.
- Server Actions.
- Backend services.

There are two common approaches:

1. Use `fetch()` to send GraphQL requests.
2. Use a GraphQL client such as Apollo Client.

## 54. Fetch GraphQL Data From a Server Component

```tsx
interface User {
  id: string;
  name: string;
  email: string;
}

interface UsersResponse {
  data: {
    users: User[];
  };
}

async function getUsers(): Promise<User[]> {
  const response = await fetch(
    "http://localhost:4000/graphql",
    {
      method: "POST",

      headers: {
        "Content-Type": "application/json"
      },

      body: JSON.stringify({
        query: `
          query GetUsers {
            users {
              id
              name
              email
            }
          }
        `
      })
    }
  );

  if (!response.ok) {
    throw new Error("Failed to fetch users");
  }

  const result =
    await response.json() as UsersResponse;

  return result.data.users;
}

export default async function UsersPage() {
  const users = await getUsers();

  return (
    <div>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}
```

**Important:** This example uses a type assertion for simplicity. It does not validate the server's actual response. In production, validate external data and handle GraphQL's `errors` response property as well.

## 55. Apollo Client With Next.js App Router

Apollo provides a dedicated integration for Next.js App Router.

It supports:

- React Server Components.
- Client Components.
- Server-side data fetching.
- Cache hydration.

Install:

```bash
npm install @apollo/client@latest \
  @apollo/client-integration-nextjs \
  graphql rxjs
```

Follow the current Apollo integration guide because provider configuration differs from a conventional React application. [apollographql.com](https://www.apollographql.com/docs/react/integrations/nextjs?utm_source=chatgpt.com)

Documentation: [Apollo Client – Next.js App Router](https://www.apollographql.com/docs/react/integrations/nextjs).

---

# PART 19: GRAPHQL WITH DATABASES

## 56. GraphQL With MongoDB

GraphQL does not replace MongoDB.

GraphQL is the API layer, while MongoDB is the database layer.

Architecture:

```text
        React / Next.js
               |
               v
          GraphQL API
               |
               v
           Resolvers
               |
               v
           Services
               |
               v
          MongoDB Driver
               |
               v
            MongoDB
```

Example:

```typescript
interface User {
  id: string;
  name: string;
  email: string;
}

const resolvers = {
  Query: {
    users: async (
      _parent: unknown,
      _args: unknown,
      context: Context
    ): Promise<User[]> => {
      return context.db
        .collection<User>("users")
        .find({})
        .toArray();
    }
  }
};
```

This is illustrative. The actual context should contain a properly initialized MongoDB database client.

## 57. GraphQL With MySQL

GraphQL can use MySQL through:

- Sequelize.
- Prisma.
- TypeORM.
- mysql2.
- Native SQL queries.

Example service:

```typescript
interface Expense {
  id: number;
  amount: number;
  description: string;
}

async function getExpenses(): Promise<Expense[]> {
  const [rows] = await pool.query(
    "SELECT id, amount, description FROM expenses"
  );

  return rows as Expense[];
}
```

The resolver calls the service:

```typescript
const resolvers = {
  Query: {
    expenses: () => getExpenses()
  }
};
```

### Important

Keep database logic inside repositories or services rather than writing SQL directly inside every resolver.

---

# PART 20: GRAPHQL WITH PRISMA

## 58. Why Use Prisma With GraphQL?

Prisma generates type-safe database clients.

It helps connect GraphQL resolvers with database operations.

Example schema:

```prisma
model Expense {
  id          Int      @id @default(autoincrement())
  amount      Float
  description String
  createdAt   DateTime @default(now())
}
```

Generate the Prisma Client using the command appropriate for your Prisma version and configuration.

Example resolver:

```typescript
const resolvers = {
  Query: {
    expenses: async (
      _parent: unknown,
      _args: unknown,
      context: Context
    ) => {
      return context.prisma.expense.findMany();
    }
  }
};
```

Context:

```typescript
interface Context {
  prisma: PrismaClient;
}
```

### Benefits

- Type-safe database operations.
- Query result inference.
- Easier relationship management.
- Reduced manual database type definitions.
- Better TypeScript integration.

---

# PART 21: AUTHENTICATION AND AUTHORIZATION

## 59. Authentication vs Authorization

These two concepts are different.

### Authentication

Authentication determines who the user is.

Example:

```text
User submits credentials
        |
        v
Backend verifies credentials
        |
        v
User identity established
```

### Authorization

Authorization determines what the authenticated user is allowed to do.

Example:

```text
Admin -> Can approve expenses

Manager -> Can review team expenses

Employee -> Can create own expenses
```

| Authentication | Authorization |
|---|---|
| Verifies identity | Checks permissions |
| Who are you? | What can you access? |
| Login/session/token verification | Role and permission checking |

## 60. GraphQL Authentication

A common implementation uses a JWT or session cookie.

Example context:

```typescript
interface AuthUser {
  id: string;
  role: "ADMIN" | "MANAGER" | "EMPLOYEE";
}

interface Context {
  user: AuthUser | null;
}
```

Authentication middleware or context construction verifies the credential.

Conceptual example:

```typescript
async function buildContext(token?: string): Promise<Context> {
  if (!token) {
    return {
      user: null
    };
  }

  const user = await verifyTokenAndLoadUser(token);

  return {
    user
  };
}
```

The `verifyTokenAndLoadUser` function represents your actual authentication implementation.

## 61. Protect GraphQL Resolvers

Example:

```typescript
function requireAuth(context: Context) {
  if (!context.user) {
    throw new Error("Authentication required");
  }

  return context.user;
}
```

Use it:

```typescript
const resolvers = {
  Query: {
    me: (
      _parent: unknown,
      _args: unknown,
      context: Context
    ) => {
      const user = requireAuth(context);

      return user;
    }
  }
};
```

## 62. Role-Based Access Control

RBAC stands for Role-Based Access Control.

Example roles:

```graphql
enum Role {
  ADMIN
  MANAGER
  EMPLOYEE
}
```

Authorization helper:

```typescript
function requireRole(
  context: Context,
  allowedRoles: string[]
) {
  const user = requireAuth(context);

  if (!allowedRoles.includes(user.role)) {
    throw new Error("Access denied");
  }

  return user;
}
```

Use:

```typescript
const resolvers = {
  Mutation: {
    approveExpense: (
      _parent: unknown,
      args: { id: string },
      context: Context
    ) => {
      requireRole(context, ["ADMIN", "MANAGER"]);

      return approveExpense(args.id);
    }
  }
};
```

### Important Security Rule

Authorization must be checked at the appropriate business-operation and data-access boundaries.

Do not assume that hiding a field in the frontend provides security.

Also verify ownership. An authenticated user should not automatically be able to access every other user's records.

---

# PART 22: THE N+1 QUERY PROBLEM

## 63. What Is the N+1 Problem?

The N+1 problem occurs when fetching a collection of records causes additional individual database queries for each record.

Consider this query:

```graphql
query {
  users {
    id
    name

    expenses {
      id
      amount
    }
  }
}
```

Suppose you have 100 users.

A naive implementation may execute:

1. One query to fetch all users.
2. One query for the expenses of user 1.
3. One query for the expenses of user 2.
4. One query for the expenses of user 3.
5. Continue for all 100 users.

Total:

```text
1 + 100 = 101 queries
```

This is the N+1 problem.

## 64. How to Solve N+1?

Common solutions:

- DataLoader.
- Database joins.
- Batch queries.
- ORM eager loading.
- Query optimization.

## 65. What Is DataLoader?

DataLoader is a utility that batches and caches data-loading requests.

Instead of querying the database separately for every user, DataLoader collects IDs and fetches the records in a batch.

Install:

```bash
npm install dataloader
```

Example:

```typescript
import DataLoader from "dataloader";

interface Expense {
  id: string;
  userId: string;
  amount: number;
}

const expenseLoader = new DataLoader<
  string,
  Expense[]
>(async (userIds) => {
  const expenses = await getExpensesByUserIds(
    [...userIds]
  );

  return userIds.map(userId =>
    expenses.filter(expense => expense.userId === userId)
  );
});
```

The resolver:

```typescript
const resolvers = {
  User: {
    expenses: (
      user: User,
      _args: unknown,
      context: Context
    ) => {
      return context.loaders.expensesByUser.load(user.id);
    }
  }
};
```

### Important

Create DataLoader instances per request in GraphQL context.

Request-scoped caching prevents one user's data from being accidentally reused in another user's request.

DataLoader is a widely used batching approach for reducing N+1 database access patterns. [GraphQL](https://graphql.org/learn/performance/?utm_source=chatgpt.com)

---

# PART 23: PAGINATION

## 66. What Is Pagination?

Pagination divides a large collection of records into smaller groups.

Suppose your database contains 100,000 expenses.

Returning all records in one request is inefficient.

Instead, return a limited number of records per request.

## 67. Offset-Based Pagination

Example schema:

```graphql
type Query {
  expenses(
    limit: Int!
    offset: Int!
  ): [Expense!]!
}
```

Request:

```graphql
query {
  expenses(
    limit: 10
    offset: 20
  ) {
    id
    amount
  }
}
```

This requests 10 expenses, skipping the first 20.

SQL equivalent:

```sql
SELECT *
FROM expenses
ORDER BY id
LIMIT 10 OFFSET 20;
```

### Advantages

- Easy to implement.
- Simple page-number navigation.
- Useful for small or moderately sized datasets.

### Disadvantages

- Large offsets can become expensive.
- Concurrent inserts/deletes can shift page boundaries.
- Deep pagination may perform poorly.

## 68. Cursor-Based Pagination

Cursor-Based Pagination uses a position marker rather than a numeric offset.

Example schema:

```graphql
type Expense {
  id: ID!
  amount: Float!
}

type ExpenseEdge {
  cursor: String!
  node: Expense!
}

type PageInfo {
  hasNextPage: Boolean!
  endCursor: String
}

type ExpenseConnection {
  edges: [ExpenseEdge!]!
  pageInfo: PageInfo!
}

type Query {
  expenses(
    first: Int!
    after: String
  ): ExpenseConnection!
}
```

Request:

```graphql
query {
  expenses(first: 10) {
    edges {
      cursor
      node {
        id
        amount
      }
    }

    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

Next page:

```graphql
query {
  expenses(
    first: 10
    after: "CURSOR_VALUE"
  ) {
    edges {
      cursor
      node {
        id
        amount
      }
    }

    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

### Offset vs Cursor Pagination

| Offset Pagination | Cursor Pagination |
|---|---|
| Uses limit and offset | Uses cursor and position |
| Simple page numbers | Better for sequential navigation |
| Large offsets can be expensive | Can perform well with indexed cursor fields |
| Sensitive to inserted/deleted rows | More stable with suitable ordering |
| Useful for smaller datasets | Useful for large, changing datasets |

**Important:** Cursor pagination should use a deterministic ordering, usually including a unique tie-breaker such as an ID.

---

# PART 24: QUERY COMPLEXITY AND DEPTH LIMITING

## 69. Why Do GraphQL Queries Need Limits?

GraphQL allows clients to request nested data.

An unrestricted query may consume substantial database and server resources.

Example:

```graphql
query {
  users {
    expenses {
      user {
        expenses {
          user {
            expenses {
              id
            }
          }
        }
      }
    }
  }
}
```

This kind of deeply nested query can become expensive.

## 70. What Is Query Depth Limiting?

Query Depth Limiting restricts how deeply a GraphQL operation can nest fields.

Example policy:

```text
Maximum query depth = 5
```

A query with depth 10 may be rejected before execution.

## 71. What Is Query Complexity?

Query Complexity estimates how expensive a GraphQL operation will be.

For example:

| Field | Cost |
|---|---:|
| user | 1 |
| users | 5 |
| expenses | 10 |
| reports | 20 |
| analytics | 50 |

A query requesting expensive fields may exceed a configured maximum cost.

## 72. Additional Demand Controls

Production GraphQL APIs should consider:

- Maximum query depth.
- Maximum query complexity.
- Pagination.
- Field and alias limits.
- Request rate limiting.
- Batch operation limits.
- Request timeouts.
- Trusted or persisted documents.

The GraphQL security guidance specifically recommends demand controls such as depth, breadth, batching and complexity limits. [GraphQL](https://graphql.org/learn/security/?utm_source=chatgpt.com)

---

# PART 25: ERROR HANDLING

## 73. How GraphQL Handles Errors

GraphQL responses may contain:

- `data`.
- `errors`.
- Both `data` and `errors`.

Example successful response:

```json
{
  "data": {
    "user": {
      "id": "1",
      "name": "John"
    }
  }
}
```

Example error response:

```json
{
  "errors": [
    {
      "message": "User not found"
    }
  ],
  "data": {
    "user": null
  }
}
```

A resolver failure can produce a partial response when other fields succeed.

## 74. Custom Error Handling With Apollo

Apollo provides error classes for controlling GraphQL error codes.

Example:

```typescript
import {
  GraphQLError
} from "graphql";

function requireAuth(context: Context) {
  if (!context.user) {
    throw new GraphQLError(
      "Authentication required",
      {
        extensions: {
          code: "UNAUTHENTICATED"
        }
      }
    );
  }

  return context.user;
}
```

For authorization failure:

```typescript
throw new GraphQLError(
  "You do not have permission",
  {
    extensions: {
      code: "FORBIDDEN"
    }
  }
);
```

### Common Error Categories

| Error | Meaning |
|---|---|
| BAD_USER_INPUT | Invalid input |
| UNAUTHENTICATED | User is not authenticated |
| FORBIDDEN | User lacks permission |
| NOT_FOUND | Requested resource does not exist |
| INTERNAL_SERVER_ERROR | Unexpected server failure |

These codes can be application-defined; ensure your API uses a consistent error contract.

---

# PART 26: CUSTOM SCALARS

## 75. What Is a Custom Scalar?

A Custom Scalar defines a value type beyond GraphQL's built-in scalar types.

Built-in types:

- Int.
- Float.
- String.
- Boolean.
- ID.

But real applications may need:

- DateTime.
- Email.
- URL.
- JSON.
- Decimal.
- BigInt.

Example schema:

```graphql
scalar DateTime

type Expense {
  id: ID!
  amount: Float!
  createdAt: DateTime!
}
```

A custom scalar implementation defines how values are parsed and serialized.

Example using `GraphQLScalarType`:

```typescript
import {
  GraphQLScalarType,
  Kind
} from "graphql";

const DateTimeScalar = new GraphQLScalarType({
  name: "DateTime",

  serialize(value) {
    return new Date(value as string | number | Date).toISOString();
  },

  parseValue(value) {
    const date = new Date(value as string);

    if (Number.isNaN(date.getTime())) {
      throw new Error("Invalid DateTime");
    }

    return date;
  },

  parseLiteral(ast) {
    if (ast.kind !== Kind.STRING) {
      throw new Error("DateTime must be a string");
    }

    const date = new Date(ast.value);

    if (Number.isNaN(date.getTime())) {
      throw new Error("Invalid DateTime");
    }

    return date;
  }
});
```

Add it to resolvers:

```typescript
const resolvers = {
  DateTime: DateTimeScalar
};
```

**Important:** Define a clear serialization format and timezone policy for dates.

---

# PART 27: CUSTOM DIRECTIVES

## 76. What Is a GraphQL Directive?

A Directive provides additional instructions to GraphQL schema definitions or operations.

Built-in directives include:

- `@include`.
- `@skip`.
- `@deprecated`.
- `@specifiedBy`.

Example:

```graphql
type User {
  id: ID!
  name: String!
  oldEmail: String @deprecated(reason: "Use email instead")
  email: String!
}
```

This marks `oldEmail` as deprecated.

## 77. Custom Directive Example

Schema:

```graphql
directive @auth(
  role: String
) on FIELD_DEFINITION
```

Usage:

```graphql
type Query {
  adminDashboard: String! @auth(role: "ADMIN")
}
```

A custom directive requires an implementation in the GraphQL server. Declaring the directive alone does not enforce authorization.

For most enterprise systems, authorization should also be enforced in business services or data-access boundaries.

---

# PART 28: GRAPHQL MIDDLEWARE

## 78. What Is GraphQL Middleware?

Middleware is functionality that runs around or before resolver execution.

Common responsibilities:

- Authentication.
- Authorization.
- Logging.
- Performance measurement.
- Rate limiting.
- Input validation.

A simplified conceptual flow:

```text
        GraphQL Request
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
            Resolver
               |
               v
         Business Logic
               |
               v
           Response
```

Apollo Server also supports plugins for lifecycle hooks and request instrumentation.

Use middleware or plugins for cross-cutting behavior, but keep business rules in the application layer.

---

# PART 29: FILE UPLOADS

## 79. File Uploads in GraphQL

GraphQL itself does not define a universal file-upload protocol.

A common production approach is to upload files directly to object storage.

Example:

```text
        Next.js Frontend
               |
               v
       Request Upload URL
               |
               v
         GraphQL API
               |
               v
       Generate Signed URL
               |
               v
        AWS S3 / Storage
               |
               v
         Direct Upload
               |
               v
       Save File Metadata
               |
               v
          MySQL DB
```

### Why Use Signed URLs?

- Reduces load on the GraphQL server.
- Supports large files.
- Enables direct object-storage uploads.
- Separates file storage from application logic.

For example, an Expense Tracker can upload receipts directly to Amazon S3 and store the resulting file metadata in MySQL.

---

# PART 30: REAL-TIME GRAPHQL SUBSCRIPTIONS

## 80. Implementing Subscriptions

A common Node.js approach uses:

- GraphQL subscriptions.
- `graphql-ws`.
- WebSocket server.
- Pub/Sub implementation.

Install:

```bash
npm install graphql-ws ws
```

A basic subscription schema:

```graphql
type Expense {
  id: ID!
  amount: Float!
  status: String!
}

type Subscription {
  expenseUpdated: Expense!
}
```

Conceptually:

```text
       Client
         |
         | WebSocket Connection
         v
     GraphQL Server
         |
         v
      Pub/Sub
         |
         v
    Expense Updated
         |
         v
    Publish Event
         |
         v
    Connected Clients
```

### Important Apollo Server Note

Apollo Server handles GraphQL execution, but WebSocket subscription transport requires separate integration and lifecycle management.

Do not assume that installing Apollo Server automatically enables subscriptions.

Also consider:

- Authentication during WebSocket connection.
- Reconnection behavior.
- Subscription authorization.
- Event filtering.
- Cleanup when clients disconnect.
- Scaling Pub/Sub across multiple server instances.

---

# PART 31: GRAPHQL CACHING

## 81. What Is GraphQL Caching?

Caching stores previously retrieved data so repeated requests can be served more efficiently.

GraphQL caching can occur at multiple levels.

### 1. Client-Side Cache

Apollo Client provides an in-memory normalized cache.

### 2. Server-Side Cache

The GraphQL server can cache reusable data or expensive computations.

### 3. Database Cache

Redis can cache frequently requested database results.

### 4. HTTP Cache

Queries can sometimes use HTTP caching, depending on transport, headers and operation characteristics.

## 82. Apollo Client Cache Example

```typescript
import {
  ApolloClient,
  InMemoryCache,
  HttpLink
} from "@apollo/client";

const client = new ApolloClient({
  link: new HttpLink({
    uri: "http://localhost:4000/graphql"
  }),

  cache: new InMemoryCache({
    typePolicies: {
      User: {
        keyFields: ["id"]
      }
    }
  })
});
```

Apollo Client uses normalized caching to store entities and references.

### Important

Caching is not automatically safe for every piece of data.

Consider:

- User-specific information.
- Permissions.
- Cache invalidation.
- Mutations.
- Expiration.
- Tenant boundaries.

Never share sensitive user-specific cached data across users.

---

# PART 32: GRAPHQL SECURITY

## 83. Common GraphQL Security Risks

GraphQL APIs can introduce security issues if operations are not properly controlled.

Common risks include:

1. Unauthenticated access.
2. Broken object-level authorization.
3. Excessive query depth.
4. Expensive queries.
5. Excessive aliases.
6. Large pagination limits.
7. Information leakage through errors.
8. Insecure file uploads.
9. Introspection exposure.
10. Resource exhaustion.

## 84. Security Best Practices

### Authentication

Verify the user's identity before allowing protected operations.

### Authorization

Check permissions at the business-operation and data-access levels.

### Input Validation

Validate user input with Zod or another runtime validation library.

### Pagination

Avoid returning unbounded lists.

### Depth Limiting

Restrict deeply nested operations.

### Query Complexity

Reject operations that exceed an acceptable cost.

### Rate Limiting

Limit requests based on users, IPs or other appropriate identifiers.

### Error Sanitization

Avoid exposing database credentials, SQL errors or internal stack traces.

### HTTPS

Use HTTPS for production traffic.

### Introspection

Consider restricting introspection for private production APIs where appropriate.

However, disabling introspection is not a replacement for proper authorization or other security controls.

These recommendations align with the official GraphQL security guidance and OWASP GraphQL security recommendations. [GraphQL](https://graphql.org/learn/security/?utm_source=chatgpt.com)

---

# PART 33: GRAPHQL TESTING

## 85. Why Is GraphQL Testing Important?

Testing ensures that:

- Queries return correct results.
- Mutations change data correctly.
- Authentication works.
- Authorization rules are enforced.
- Validation rejects invalid inputs.
- Database errors are handled.
- Subscriptions deliver expected events.

## 86. Types of GraphQL Testing

| Testing Type | Purpose |
|---|---|
| Unit Testing | Tests individual resolver and service functions |
| Integration Testing | Tests resolvers with database/services |
| API Testing | Tests complete GraphQL operations |
| Security Testing | Tests access controls and malicious inputs |
| Performance Testing | Tests response time and resource consumption |
| E2E Testing | Tests complete frontend-to-backend flows |

## 87. Example Resolver Unit Test

Suppose you have:

```typescript
function add(a: number, b: number): number {
  return a + b;
}
```

Test:

```typescript
import { describe, it, expect } from "vitest";

describe("add", () => {
  it("should return the sum", () => {
    expect(add(10, 20)).toBe(30);
  });
});
```

For GraphQL integration testing, execute actual operations against a test server and test the returned `data` and `errors`.

---

# PART 34: GRAPHQL FEDERATION

## 88. What Is GraphQL Federation?

GraphQL Federation allows multiple independently managed GraphQL services to work together as one unified API.

It is especially useful in microservice architectures.

Suppose your enterprise application has:

- User Service.
- Expense Service.
- Payment Service.
- Notification Service.

Each service owns its own GraphQL schema.

Federation combines these services into a unified graph.

### Architecture

```text
                Frontend
                   |
                   v
             GraphQL Gateway
                   |
            Unified API Graph
                   |
        +----------+----------+
        |          |          |
        v          v          v
    User         Expense    Payment
   Service       Service    Service
        |          |          |
        v          v          v
      User       Expense    Payment
       DB          DB         DB
```

### Benefits

- Independent service ownership.
- Independent deployment.
- Distributed schema ownership.
- Better organizational boundaries.
- Unified API for clients.

### Important

Federation adds operational complexity.

For small applications, a single GraphQL server is usually easier to maintain.

---

# PART 35: MICROSERVICES WITH GRAPHQL

## 89. GraphQL as an API Gateway

GraphQL can act as an aggregation layer over multiple microservices.

Example:

```text
               Next.js Frontend
                       |
                       v
                GraphQL Gateway
                       |
           +-----------+-----------+
           |           |           |
           v           v           v
       User API    Expense API   Payment API
           |           |           |
           v           v           v
        User DB     Expense DB   Payment DB
```

The client can request information from multiple services through one GraphQL operation.

Example:

```graphql
query Dashboard {
  me {
    name
  }

  expenses {
    amount
  }

  payments {
    status
  }
}
```

The GraphQL layer aggregates the requested data.

### Challenges

- Network latency.
- Service failures.
- Distributed authorization.
- Cross-service transactions.
- Data consistency.
- Query complexity.
- Observability.

**Important:** GraphQL is not a replacement for microservices. It is an API layer that can sit in front of them.

---

# PART 36: ENTERPRISE GRAPHQL PROJECT STRUCTURE

## 90. Recommended Project Structure

For a large GraphQL application using Node.js, Express, Apollo Server and TypeScript:

```text
graphql-enterprise/
│
├── src/
│   │
│   ├── modules/
│   │   │
│   │   ├── users/
│   │   │   ├── users.schema.ts
│   │   │   ├── users.resolver.ts
│   │   │   ├── users.service.ts
│   │   │   ├── users.repository.ts
│   │   │   └── users.types.ts
│   │   │
│   │   ├── expenses/
│   │   │   ├── expenses.schema.ts
│   │   │   ├── expenses.resolver.ts
│   │   │   ├── expenses.service.ts
│   │   │   ├── expenses.repository.ts
│   │   │   └── expenses.types.ts
│   │   │
│   │   └── payments/
│   │       ├── payments.schema.ts
│   │       ├── payments.resolver.ts
│   │       ├── payments.service.ts
│   │       ├── payments.repository.ts
│   │       └── payments.types.ts
│   │
│   ├── graphql/
│   │   ├── schema.ts
│   │   ├── context.ts
│   │   ├── scalars.ts
│   │   └── directives.ts
│   │
│   ├── middleware/
│   │   ├── authentication.ts
│   │   └── rate-limiter.ts
│   │
│   ├── dataloaders/
│   │   ├── user.loader.ts
│   │   └── expense.loader.ts
│   │
│   ├── database/
│   │   └── prisma.ts
│   │
│   ├── config/
│   │   └── environment.ts
│   │
│   ├── app.ts
│   └── server.ts
│
├── tests/
│
├── package.json
└── tsconfig.json
```

### Responsibilities

| Layer | Responsibility |
|---|---|
| Schema | Defines GraphQL contracts |
| Resolver | Connects GraphQL fields to application logic |
| Service | Contains business rules |
| Repository | Handles data access |
| DataLoader | Batches related data loading |
| Context | Provides request-level dependencies |
| Middleware | Handles cross-cutting concerns |
| Database | Manages database connections |

### Enterprise Best Practices

1. Keep resolvers small.
2. Keep business logic in services.
3. Keep database queries in repositories.
4. Validate all external input.
5. Use request-scoped DataLoaders.
6. Implement authentication and authorization.
7. Use pagination for large datasets.
8. Apply query demand controls.
9. Monitor resolver performance.
10. Use generated TypeScript types where appropriate.
11. Avoid circular module dependencies.
12. Document schema fields.
13. Version and deprecate schema fields carefully.
14. Use integration tests for important operations.

---

# PART 37: COMPLETE REAL-WORLD PROJECT

## 91. Project: Enterprise Expense Tracker Using GraphQL

Build a complete Expense Tracker using GraphQL as the primary API layer.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + React + TypeScript |
| API | GraphQL |
| Server | Apollo Server 5 |
| HTTP Framework | Express 5 |
| Database | MySQL |
| ORM | Prisma or Sequelize |
| Runtime Validation | Zod |
| Client | Apollo Client |
| Authentication | JWT or secure sessions |
| Cache | Redis |
| File Storage | AWS S3 |
| Testing | Vitest + Supertest |
| E2E Testing | Playwright |
| Deployment | Docker + AWS/VPS |

## 92. Required Modules

### Authentication Module

- Register.
- Login.
- Logout.
- Refresh session/token.
- Forgot password.
- Reset password.

### User Module

- Create user.
- Fetch profile.
- Update profile.
- Delete account.
- Change password.

### Expense Module

- Create expense.
- Update expense.
- Delete expense.
- Fetch expenses.
- Search expenses.
- Filter expenses.
- Approve expenses.
- Reject expenses.

### Category Module

- Create category.
- Update category.
- Delete category.
- Fetch categories.

### Reports Module

- Monthly reports.
- Yearly reports.
- Category reports.
- User reports.
- Department reports.
- Excel export.

### Notification Module

- Real-time expense updates.
- Approval notifications.
- Rejection notifications.
- Payment notifications.

## 93. Example Enterprise GraphQL Schema

```graphql
enum Role {
  ADMIN
  MANAGER
  EMPLOYEE
}

enum ExpenseStatus {
  PENDING
  APPROVED
  REJECTED
}

type User {
  id: ID!
  name: String!
  email: String!
  role: Role!
  expenses: [Expense!]!
}

type Category {
  id: ID!
  name: String!
}

type Expense {
  id: ID!
  amount: Float!
  description: String!
  status: ExpenseStatus!
  category: Category!
  user: User!
  createdAt: DateTime!
}

input CreateExpenseInput {
  amount: Float!
  description: String!
  categoryId: ID!
}

input UpdateExpenseInput {
  amount: Float
  description: String
  categoryId: ID
}

type Query {
  me: User

  users: [User!]!

  expenses(
    first: Int!
    after: String
  ): ExpenseConnection!

  expense(id: ID!): Expense

  categories: [Category!]!
}

type Mutation {
  createExpense(
    input: CreateExpenseInput!
  ): Expense!

  updateExpense(
    id: ID!
    input: UpdateExpenseInput!
  ): Expense!

  deleteExpense(id: ID!): Boolean!

  approveExpense(id: ID!): Expense!

  rejectExpense(id: ID!): Expense!
}

type Subscription {
  expenseUpdated: Expense!
}
```

This is a conceptual schema. A complete implementation must define `DateTime`, `ExpenseConnection`, its edge and page information types, resolvers and subscription infrastructure.

## 94. Final Architecture

```text
                Next.js Frontend
                       |
                       v
                 Apollo Client
                       |
                       v
                 GraphQL API
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Queries      Mutations   Subscriptions
          |            |            |
          +------------+------------+
                       |
                       v
                    Resolvers
                       |
                       v
                 Application Services
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Users       Expenses     Reports
       Module       Module      Module
          |            |            |
          +------------+------------+
                       |
                       v
                   Repositories
                       |
          +------------+------------+
          |            |            |
          v            v            v
        MySQL         Redis        AWS S3
```

---

# PART 38: GRAPHQL INTERVIEW QUESTIONS

## Beginner-Level Questions

### Q1. What is GraphQL?

GraphQL is a query language for APIs and a runtime for executing queries against a defined schema.

### Q2. Who developed GraphQL?

Facebook developed GraphQL and open-sourced it in 2015.

### Q3. What is the main advantage of GraphQL?

Clients can request exactly the fields they need.

### Q4. What is a GraphQL schema?

A schema defines the types, fields and operations available through a GraphQL API.

### Q5. What is SDL?

Schema Definition Language is the language used to define GraphQL schemas.

### Q6. What is a Resolver?

A resolver is a function that provides the value for a GraphQL field.

### Q7. What are GraphQL operations?

The three main operation types are Query, Mutation and Subscription.

### Q8. What is a Query?

An operation used to retrieve data.

### Q9. What is a Mutation?

An operation used to modify data.

### Q10. What is a Subscription?

An operation used to receive real-time updates.

## Intermediate-Level Questions

### Q11. What are Scalar Types?

Primitive GraphQL types such as Int, Float, String, Boolean and ID.

### Q12. What is an Enum?

A type that restricts a value to a predefined set of options.

### Q13. What is an Input Type?

A type that defines structured input data for operations.

### Q14. What is the difference between type and input?

`type` describes output objects, while `input` defines structured input values.

### Q15. What does `!` mean in GraphQL?

It indicates a non-null value.

### Q16. What does `[User!]!` mean?

A non-null list containing only non-null User objects.

### Q17. What are Query Variables?

Variables allow dynamic values to be supplied to GraphQL operations.

### Q18. What are Aliases?

Aliases allow a client to rename fields in the response.

### Q19. What are Fragments?

Fragments are reusable field selections.

### Q20. What is GraphQL Context?

A request-level object shared across resolver execution.

## Advanced-Level Questions

### Q21. What are the four resolver arguments?

Parent, args, context and info.

### Q22. What is the N+1 problem?

A performance issue where fetching multiple records causes additional individual queries for each record.

### Q23. How do you solve the N+1 problem?

Use DataLoader, batching, joins or ORM eager loading.

### Q24. What is DataLoader?

A utility that batches and caches data-loading requests.

### Q25. What is Schema-First Development?

Defining the GraphQL schema before implementing its resolvers.

### Q26. What is Code-First Development?

Building the GraphQL schema through programming-language code.

### Q27. What is Apollo Server?

A GraphQL server implementation for Node.js.

### Q28. What is Apollo Client?

A GraphQL client library used by frontend applications.

### Q29. What is GraphQL Federation?

A way to combine independently managed GraphQL services into one unified API.

### Q30. What is the difference between authentication and authorization?

Authentication verifies identity; authorization checks permissions.

## Expert-Level Questions

### Q31. What is Query Complexity?

A method of estimating the computational cost of a GraphQL operation.

### Q32. What is Query Depth Limiting?

A security and performance technique that restricts nested query depth.

### Q33. What is Cursor-Based Pagination?

A pagination technique that uses a cursor to continue from a particular position in a dataset.

### Q34. What is the difference between offset and cursor pagination?

Offset pagination uses numeric positions; cursor pagination uses record-position markers.

### Q35. What are Custom Scalars?

Custom scalar types that represent application-specific values such as DateTime or Decimal.

### Q36. What are GraphQL Directives?

Special annotations that modify how schemas or operations are interpreted or executed.

### Q37. Does GraphQL support file uploads natively?

The GraphQL specification does not define a universal file-upload protocol. Implementations commonly use signed URLs or multipart upload conventions.

### Q38. Does GraphQL automatically solve caching?

No. Client, server and HTTP caching require appropriate configuration and strategies.

### Q39. Why is authorization important in GraphQL?

Because clients can request different fields and nested resources, authorization must be enforced at the appropriate data and operation boundaries.

### Q40. Is GraphQL faster than REST?

Not automatically. Performance depends on schema design, resolver implementation, caching and database access.

## Enterprise and Practical Questions

### Q41. How do you integrate GraphQL with Express?

Use an Apollo Server integration package and mount its middleware on an Express route.

### Q42. How do you integrate GraphQL with Next.js?

Use fetch or a GraphQL client such as Apollo Client from the appropriate server or client components.

### Q43. How do you connect GraphQL with MySQL?

Use an ORM or database driver inside repositories and services called by resolvers.

### Q44. How do you secure GraphQL APIs?

Use authentication, authorization, validation, rate limiting, pagination, query complexity controls and safe error handling.

### Q45. How do you implement real-time GraphQL?

Use subscriptions with a suitable transport such as WebSockets and a Pub/Sub mechanism.

### Q46. What is the purpose of GraphQL Federation?

It combines independently managed GraphQL services into a unified API.

### Q47. What is persisted-query or trusted-document functionality?

It allows a server to accept approved GraphQL operations through stored documents or their identifiers.

### Q48. Why should resolvers remain small?

Resolvers should primarily connect GraphQL fields to business services. This improves separation of concerns and testability.

### Q49. What is partial data in GraphQL?

A response containing successfully resolved fields alongside errors from other fields.

### Q50. When should you avoid GraphQL?

When the application has simple resource-based APIs, does not benefit from flexible field selection, or would incur unnecessary operational complexity.

---

# PART 39: HANDS-ON PRACTICAL ASSIGNMENTS

## Level 1 – GraphQL Fundamentals

| No. | Assignment | Expected Result |
|---:|---|---|
| 1 | Create your first GraphQL server | Working API |
| 2 | Create an object type | User schema |
| 3 | Create scalar fields | Typed properties |
| 4 | Create enum types | Restricted values |
| 5 | Create input types | Structured inputs |
| 6 | Practice list and non-null types | Type modifiers |
| 7 | Create basic queries | Read operations |
| 8 | Create query variables | Dynamic queries |
| 9 | Practice aliases | Renamed fields |
| 10 | Create reusable fragments | Reusable selections |

## Level 2 – Queries and Mutations

| No. | Assignment | Expected Result |
|---:|---|---|
| 11 | Create User Query | Fetch all users |
| 12 | Create Single User Query | Fetch by ID |
| 13 | Create CreateUser Mutation | Insert user |
| 14 | Create UpdateUser Mutation | Update user |
| 15 | Create DeleteUser Mutation | Delete user |
| 16 | Create Expense Query | Fetch expenses |
| 17 | Create Expense Mutation | Create expense |
| 18 | Create Category Query | Fetch categories |
| 19 | Create nested resolvers | Resolve relationships |
| 20 | Create custom error handling | Consistent errors |

## Level 3 – Advanced GraphQL

| No. | Assignment | Expected Result |
|---:|---|---|
| 21 | Create custom scalars | DateTime |
| 22 | Create custom directives | Directive practice |
| 23 | Implement authentication | Protected operations |
| 24 | Implement RBAC | Role permissions |
| 25 | Implement pagination | Limited datasets |
| 26 | Implement cursor pagination | Cursor connections |
| 27 | Implement DataLoader | Batched queries |
| 28 | Prevent N+1 queries | Optimized database access |
| 29 | Implement query depth controls | Query protection |
| 30 | Implement query complexity controls | Cost restrictions |

## Level 4 – Framework Integration

| No. | Assignment | Expected Result |
|---:|---|---|
| 31 | Integrate Apollo Server | GraphQL server |
| 32 | Integrate GraphQL with Express | Express API |
| 33 | Integrate Apollo Client | React client |
| 34 | Integrate GraphQL with Next.js | Next.js integration |
| 35 | Connect GraphQL with MongoDB | MongoDB backend |
| 36 | Connect GraphQL with MySQL | SQL backend |
| 37 | Integrate Prisma | Typed database queries |
| 38 | Implement GraphQL subscriptions | Real-time API |
| 39 | Implement Apollo caching | Client cache |
| 40 | Implement file upload using signed URLs | Object storage |

## Level 5 – Enterprise Development

| No. | Assignment | Expected Result |
|---:|---|---|
| 41 | Build modular GraphQL architecture | Organized codebase |
| 42 | Implement Repository Pattern | Data abstraction |
| 43 | Implement centralized authentication | Secure API |
| 44 | Implement authorization policies | Protected resources |
| 45 | Add GraphQL testing | Test suite |
| 46 | Add performance monitoring | Observability |
| 47 | Implement GraphQL Federation | Unified API |
| 48 | Integrate multiple microservices | Distributed API |
| 49 | Deploy GraphQL using Docker | Containerized API |
| 50 | Build Enterprise Expense Tracker | Complete project |

---

# PART 40: QUICK REVISION NOTES

| Concept | Meaning |
|---|---|
| GraphQL | Query language and API execution runtime |
| Schema | Defines available API capabilities |
| SDL | Schema Definition Language |
| Query | Retrieves data |
| Mutation | Changes data |
| Subscription | Receives real-time events |
| Resolver | Resolves a field's value |
| Context | Shares request-level dependencies |
| Scalar | Primitive GraphQL type |
| Enum | Restricted set of values |
| Input | Defines structured input |
| Non-Null | Prevents null values |
| Alias | Renames a response field |
| Fragment | Reusable field selection |
| Directive | Additional schema or operation instruction |
| Apollo Server | GraphQL server implementation |
| Apollo Client | GraphQL frontend client |
| DataLoader | Batches and caches data loads |
| N+1 Problem | Excessive individual data queries |
| Pagination | Divides large results into smaller groups |
| Cursor Pagination | Pagination using a position marker |
| Query Complexity | Estimates query execution cost |
| Depth Limiting | Restricts nested query depth |
| Custom Scalar | Application-specific scalar type |
| Middleware | Handles cross-cutting behavior |
| Federation | Combines GraphQL services |
| Pub/Sub | Event publishing and subscription mechanism |
| RBAC | Role-Based Access Control |
| Runtime Validation | Checks actual input values |
| Schema-First | Defines schema before resolvers |
| Code-First | Defines schema through code |

---

# PART 41: PHASE 63 COMPLETION CHECKLIST

### GraphQL Fundamentals

- [ ] Understand GraphQL architecture.
- [ ] Understand REST vs GraphQL.
- [ ] Understand SDL.
- [ ] Create GraphQL schemas.
- [ ] Understand scalar types.
- [ ] Understand object types.
- [ ] Understand enum types.
- [ ] Understand input types.
- [ ] Understand list and non-null modifiers.

### Queries and Mutations

- [ ] Create basic queries.
- [ ] Use query variables.
- [ ] Use aliases.
- [ ] Use fragments.
- [ ] Create mutations.
- [ ] Create subscriptions.
- [ ] Understand nested resolvers.
- [ ] Understand resolver arguments.
- [ ] Understand GraphQL context.

### Apollo and Framework Integration

- [ ] Create an Apollo Server.
- [ ] Integrate Apollo Server with Express.
- [ ] Integrate Apollo Client with React.
- [ ] Integrate GraphQL with Next.js.
- [ ] Connect GraphQL with MongoDB.
- [ ] Connect GraphQL with MySQL.
- [ ] Integrate Prisma.

### Performance

- [ ] Understand the N+1 problem.
- [ ] Implement DataLoader.
- [ ] Implement pagination.
- [ ] Implement cursor pagination.
- [ ] Understand client caching.
- [ ] Understand server caching.
- [ ] Implement query complexity controls.
- [ ] Implement depth limiting.

### Security

- [ ] Implement authentication.
- [ ] Implement authorization.
- [ ] Implement RBAC.
- [ ] Validate input.
- [ ] Implement rate limiting.
- [ ] Sanitize error responses.
- [ ] Protect expensive queries.
- [ ] Understand trusted documents.

### Enterprise Development

- [ ] Understand GraphQL Federation.
- [ ] Understand GraphQL microservice architecture.
- [ ] Implement custom scalars.
- [ ] Understand custom directives.
- [ ] Implement real-time subscriptions.
- [ ] Implement GraphQL testing.
- [ ] Build a modular GraphQL backend.
- [ ] Complete the Expense Tracker project.

---

# Phase 63 – Final Summary

You have covered GraphQL from basic API queries to enterprise-level API architecture.

The most important concepts to master are:

1. **GraphQL Schema:** Defines the contract between frontend and backend.
2. **Queries:** Retrieve data.
3. **Mutations:** Modify data.
4. **Subscriptions:** Deliver real-time updates.
5. **Resolvers:** Connect GraphQL fields to application logic.
6. **Context:** Shares authentication and application dependencies.
7. **Apollo Server:** Implements the GraphQL backend.
8. **Apollo Client:** Connects frontend applications to GraphQL.
9. **DataLoader:** Reduces N+1 query problems.
10. **Pagination:** Efficiently retrieves large datasets.
11. **Authentication and Authorization:** Protect API operations.
12. **Query Complexity:** Controls expensive operations.
13. **Federation:** Combines multiple GraphQL services.
14. **Enterprise Architecture:** Separates schema, resolvers, services and repositories.

### Recommended Learning Priority

| Priority | Topic |
|---|---|
| Essential | GraphQL Fundamentals and SDL |
| Essential | Queries, Mutations and Resolvers |
| Essential | Schema and Type System |
| Essential | Apollo Server |
| Essential | GraphQL with Express |
| Essential | Authentication and Authorization |
| Important | Apollo Client and React |
| Important | GraphQL with Next.js |
| Important | Prisma and Database Integration |
| Important | DataLoader and N+1 |
| Important | Pagination and Caching |
| Advanced | Subscriptions and WebSockets |
| Advanced | Query Complexity and Security |
| Expert | Federation and Microservices |

---

# Official Documentation and Learning Resources

1. [GraphQL Official Documentation](https://graphql.org/learn/) – Core GraphQL concepts.
2. [GraphQL Schemas and Types](https://graphql.org/learn/schema/) – Schema design and type system.
3. [GraphQL Queries](https://graphql.org/learn/queries/) – Query syntax, variables, aliases and fragments.
4. [GraphQL Execution](https://graphql.org/learn/execution/) – Resolver execution.
5. [GraphQL Subscriptions](https://graphql.org/learn/subscriptions/) – Real-time GraphQL.
6. [GraphQL Security](https://graphql.org/learn/security/) – Production security.
7. [Apollo Server Documentation](https://www.apollographql.com/docs/apollo-server/) – Backend development.
8. [Apollo Client Documentation](https://www.apollographql.com/docs/react/) – React integration.
9. [Apollo Client with Next.js](https://www.apollographql.com/docs/react/integrations/nextjs) – Next.js App Router integration.
10. [DataLoader](https://github.com/graphql/dataloader) – Batching and caching.
11. [GraphQL Code Generator](https://the-guild.dev/graphql/codegen/docs/getting-started) – TypeScript code generation.
12. [OWASP GraphQL Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html) – Security recommendations.

---

# Next Phase Preview

## Phase 64 – Advanced WebSockets, Real-Time Applications, Socket.IO and Event-Driven Architecture

Topics planned:

1. Network Communication Fundamentals.
2. HTTP vs WebSocket.
3. TCP vs UDP.
4. Full-Duplex Communication.
5. WebSocket Protocol.
6. WebSocket Handshake.
7. WebSocket Lifecycle.
8. WebSocket Events.
9. WebSocket Client.
10. WebSocket Server.
11. Node.js WebSocket Development.
12. Socket.IO Fundamentals.
13. Socket.IO Architecture.
14. Socket.IO Client.
15. Socket.IO Server.
16. Events and Event Listeners.
17. Custom Events.
18. Rooms.
19. Namespaces.
20. Broadcasting.
21. Private Messaging.
22. Group Messaging.
23. Authentication.
24. Authorization.
25. Middleware.
26. Connection Management.
27. Reconnection Strategies.
28. Heartbeats and Ping/Pong.
29. Acknowledgements.
30. Error Handling.
31. Real-Time Notifications.
32. Typing Indicators.
33. Online/Offline Status.
34. Presence Management.
35. Redis Adapter.
36. Horizontal Scaling.
37. Message Queues.
38. Event-Driven Architecture.
39. Pub/Sub Pattern.
40. Message Brokers.
41. RabbitMQ.
42. Apache Kafka Fundamentals.
43. Event Sourcing.
44. WebSocket Security.
45. Rate Limiting.
46. Performance Optimization.
47. WebSocket Testing.
48. Real-Time React Applications.
49. Real-Time Next.js Applications.
50. Production Deployment.
51. Monitoring and Observability.
52. Microservices Communication.
53. Socket.IO Interview Questions.
54. Complete Real-Time Chat Application.

**Say "OK, Phase 64" when you're ready to continue.**