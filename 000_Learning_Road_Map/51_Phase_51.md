# Phase 51 – Advanced API Development, REST, GraphQL and API Security

**Level:** Advanced  
**Category:** Backend Development / API Engineering / Application Security  
**Primary Stack:** Node.js, Express.js, TypeScript, MySQL  
**Additional Technologies:** REST, GraphQL, JWT, OAuth 2.0, OpenID Connect, OpenAPI, Swagger

---

# 1. Learning Objectives

By the end of this phase, you should understand how to design, develop, secure, document, test and optimize production-ready APIs.

You will learn:

1. API architecture and communication patterns.
2. HTTP fundamentals and REST principles.
3. Advanced REST API design.
4. HTTP methods and status codes.
5. API versioning and backward compatibility.
6. Pagination, filtering, sorting and searching.
7. Rate limiting and throttling.
8. Authentication and authorization.
9. JWT architecture and refresh token rotation.
10. OAuth 2.0 and OpenID Connect.
11. CORS, CSRF, XSS and SQL injection.
12. GraphQL architecture and implementation.
13. Webhooks and event-driven integrations.
14. OpenAPI and Swagger documentation.
15. API testing and security testing.
16. Production-ready API architecture.

---

# PART 1: API ARCHITECTURE FUNDAMENTALS

## 2. What Is an API?

API stands for **Application Programming Interface**.

An API is a communication interface that allows two software systems to exchange data and interact with each other.

For example, consider an expense tracker application.

Your frontend needs to retrieve expenses from your backend.

The frontend sends an HTTP request:

```http
GET /api/expenses
```

The backend processes the request and returns:

```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "title": "Internet Bill",
      "amount": 999
    },
    {
      "id": 2,
      "title": "Groceries",
      "amount": 2500
    }
  ]
}
```

The frontend uses this response to display expenses.

### Basic API Architecture

```text
Frontend Application
       |
       | HTTP Request
       |
       v
    Backend API
       |
       | Business Logic
       |
       v
     Database
       |
       | Database Result
       |
       v
    Backend API
       |
       | JSON Response
       |
       v
Frontend Application
```

### Common API Types

| API Type | Description |
|---|---|
| Public API | Available to external developers or applications |
| Private API | Used internally within an organization |
| Partner API | Shared with selected business partners |
| Internal Service API | Used for communication between backend services |

## 3. API Architectural Styles

Different applications use different API communication styles.

### 3.1 REST API

REST stands for **Representational State Transfer**.

REST is an architectural style for designing network-based applications.

Example:

```http
GET /api/users
GET /api/users/10
POST /api/users
PUT /api/users/10
DELETE /api/users/10
```

REST commonly uses:

- HTTP.
- Resources.
- HTTP methods.
- Status codes.
- JSON representations.
- Stateless communication.

### 3.2 GraphQL

GraphQL is a query language and runtime for APIs.

It allows clients to request exactly the fields they need.

Example:

```graphql
query {
  user(id: 10) {
    name
    email
  }
}
```

Possible response:

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

### 3.3 RPC

RPC stands for **Remote Procedure Call**.

It allows one application to invoke a procedure or function exposed by another application.

Example:

```text
createUser()
getUser()
deleteUser()
```

### 3.4 gRPC

gRPC is a high-performance RPC framework.

It commonly uses:

- Protocol Buffers.
- HTTP/2.
- Strongly defined service contracts.
- Unary and streaming communication.

It is commonly used for communication between backend services.

### REST vs GraphQL vs RPC vs gRPC

| Feature | REST | GraphQL | RPC | gRPC |
|---|---|---|---|---|
| Main concept | Resources | Queries | Procedures | Remote procedures |
| Communication | HTTP | Usually HTTP | Varies | HTTP/2 |
| Data format | Commonly JSON | Commonly JSON | Varies | Protocol Buffers |
| Client controls fields | Limited | Yes | Depends | Depends on service |
| Streaming | Additional mechanisms | Subscriptions | Depends | Supported |
| Common use | Public APIs | Flexible frontend APIs | Service operations | Microservices |

**Important:** REST is an architectural style, not simply JSON sent over HTTP.

---

# PART 2: HTTP FUNDAMENTALS

## 4. Understanding HTTP

HTTP stands for **Hypertext Transfer Protocol**.

It defines how clients and servers communicate.

For example:

```text
Browser
   |
   | HTTP Request
   |
   v
Web Server
   |
   | HTTP Response
   |
   v
Browser
```

HTTP is a request-response protocol.

A client sends a request, and a server returns a response.

## 5. Anatomy of an HTTP Request

Example:

```http
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer ACCESS_TOKEN

{
  "name": "John",
  "email": "john@example.com"
}
```

An HTTP request contains several components.

| Component | Description |
|---|---|
| Method | Operation being requested |
| URL | Resource location |
| HTTP Version | Protocol version |
| Headers | Additional request information |
| Body | Data sent to the server |

### Important HTTP Headers

| Header | Purpose |
|---|---|
| Content-Type | Format of the request or response body |
| Accept | Response formats the client can accept |
| Authorization | Authentication credentials |
| Cache-Control | Caching instructions |
| User-Agent | Identifies the client software |
| Origin | Identifies the requesting origin |
| Cookie | Sends browser cookies |
| If-None-Match | Conditional request using an ETag |

## 6. Anatomy of an HTTP Response

Example:

```http
HTTP/1.1 201 Created
Content-Type: application/json
Cache-Control: no-store

{
  "success": true,
  "message": "User created successfully",
  "data": {
    "id": 101,
    "name": "John"
  }
}
```

An HTTP response contains:

1. Status code.
2. Response headers.
3. Response body.

---

## 7. HTTP Methods

HTTP methods define the intended operation on a resource.

### 7.1 GET

Used to retrieve data.

```http
GET /api/users
```

Example:

```javascript
app.get("/api/users", async (req, res) => {
  const users = await userService.getAllUsers();

  res.status(200).json({
    success: true,
    data: users
  });
});
```

GET should not be used to perform state-changing operations.

### 7.2 POST

Used to create a resource or perform an operation that is not naturally represented by another method.

```http
POST /api/users
```

Example:

```javascript
app.post("/api/users", async (req, res) => {
  const user = await userService.createUser(req.body);

  res.status(201).json({
    success: true,
    data: user
  });
});
```

### 7.3 PUT

Used to replace the state of a resource.

```http
PUT /api/users/10
```

Request:

```json
{
  "name": "John Smith",
  "email": "john@example.com",
  "phone": "9876543210"
}
```

Conceptually, PUT replaces the resource representation with the supplied representation.

If optional fields are omitted, their handling should be explicitly defined by your API contract.

### 7.4 PATCH

Used to partially modify a resource.

```http
PATCH /api/users/10
```

Request:

```json
{
  "name": "John Smith"
}
```

Only the specified property is modified.

### 7.5 DELETE

Used to delete a resource.

```http
DELETE /api/users/10
```

### 7.6 HEAD

Similar to GET, but the server returns response headers without the response body.

```http
HEAD /api/users/10
```

Useful for checking resource metadata.

### 7.7 OPTIONS

Used to discover communication options supported by a resource.

```http
OPTIONS /api/users
```

Browsers also use OPTIONS requests for CORS preflight checks.

### Safe and Idempotent Methods

These are important HTTP concepts.

**Safe Method**

A method is safe when its intended operation is read-only.

Examples:

- GET
- HEAD
- OPTIONS

**Idempotent Method**

A method is idempotent when repeating the same request has the same intended effect on the server as performing it once.

| Method | Safe | Idempotent |
|---|---|---|
| GET | Yes | Yes |
| HEAD | Yes | Yes |
| OPTIONS | Yes | Yes |
| POST | No | Generally no |
| PUT | No | Yes |
| PATCH | No | Depends on implementation |
| DELETE | No | Yes |

For example:

```http
DELETE /api/users/10
```

Sending this request once deletes user 10.

Sending it again should not delete another resource or create an additional deletion effect.

The response might differ, but the intended final state remains the same.

**Important:** HTTP idempotency does not mean the server cannot log repeated requests. It refers to the intended effect on the resource.

The HTTP specification defines these method properties in RFC 9110. [RFC Editor](https://www.rfc-editor.org/info/rfc9110/?utm_source=chatgpt.com)

---

## 8. HTTP Status Codes

HTTP status codes communicate the result of a request.

There are five major categories.

| Category | Meaning |
|---|---|
| 1xx | Informational |
| 2xx | Success |
| 3xx | Redirection |
| 4xx | Client Error |
| 5xx | Server Error |

### Important Status Codes

#### 2xx – Success

| Code | Meaning | Use Case |
|---|---|---|
| 200 | OK | Successful GET request |
| 201 | Created | Resource successfully created |
| 202 | Accepted | Request accepted for asynchronous processing |
| 204 | No Content | Successful operation without response body |

#### 3xx – Redirection

| Code | Meaning |
|---|---|
| 301 | Moved Permanently |
| 302 | Found |
| 304 | Not Modified |
| 307 | Temporary Redirect |
| 308 | Permanent Redirect |

#### 4xx – Client Errors

| Code | Meaning | Use Case |
|---|---|---|
| 400 | Bad Request | Invalid request structure |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated but not permitted |
| 404 | Not Found | Resource does not exist or is not exposed |
| 405 | Method Not Allowed | Unsupported HTTP method |
| 409 | Conflict | Resource state conflict |
| 410 | Gone | Resource is permanently unavailable |
| 413 | Content Too Large | Request body exceeds allowed size |
| 415 | Unsupported Media Type | Unsupported request content type |
| 422 | Unprocessable Content | Valid syntax but invalid application data |
| 429 | Too Many Requests | Rate limit exceeded |

#### 5xx – Server Errors

| Code | Meaning |
|---|---|
| 500 | Internal Server Error |
| 501 | Not Implemented |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

### 401 vs 403

These are frequently confused.

**401 Unauthorized**

The request lacks valid authentication credentials.

Example:

```json
{
  "success": false,
  "message": "Authentication required"
}
```

**403 Forbidden**

The server understands the user's identity but does not permit the requested action.

Example:

```json
{
  "success": false,
  "message": "You do not have permission to access this resource"
}
```

### 400 vs 422

- 400: The request cannot be processed because its syntax or structure is invalid.
- 422: The request is syntactically valid but fails application-level validation.

For example, an invalid JSON document might receive 400.

A valid JSON document containing an invalid email address might receive 422.

---

# PART 3: ADVANCED REST API DESIGN

## 9. What Is REST?

REST is an architectural style introduced by Roy Fielding.

A RESTful API models application data as resources.

Examples of resources:

- Users.
- Products.
- Orders.
- Expenses.
- Categories.
- Payments.

Resources are identified using URLs.

```text
/api/users
/api/users/10
/api/products
/api/products/100
/api/orders
/api/orders/500
```

### REST Constraints

REST has six architectural constraints.

| Constraint | Explanation |
|---|---|
| Client-Server | Separates frontend and backend responsibilities |
| Stateless | Each request contains the information needed to process it |
| Cacheable | Responses indicate whether they can be cached |
| Uniform Interface | Standardized resource interaction |
| Layered System | Client does not need to know internal infrastructure layers |
| Code on Demand | Optional ability to transfer executable code |

### Statelessness

Statelessness means the server does not rely on hidden conversational state from previous requests to understand the current request.

For example:

```http
GET /api/profile
Authorization: Bearer ACCESS_TOKEN
```

The server validates the access token and identifies the user.

It does not need to remember which page the user visited previously.

**Important:** Stateless HTTP APIs can still use databases, sessions, caches and other server-side storage. Statelessness is about how requests are understood and processed, not a prohibition on server-side data.

## 10. RESTful URL Design

Prefer resource-oriented URLs.

### Good URL Design

```http
GET /api/users
GET /api/users/10

POST /api/users

PUT /api/users/10

PATCH /api/users/10

DELETE /api/users/10
```

### Poor URL Design

```http
GET /api/getUsers
POST /api/createUser
POST /api/deleteUser
GET /api/getUserById/10
```

Why?

Because HTTP methods should communicate the operation, while URLs should primarily identify resources.

### Nested Resources

Suppose an expense belongs to a user.

You can design:

```http
GET /api/users/10/expenses
GET /api/users/10/expenses/50
```

However, do not create unnecessarily deep URL structures.

For example:

```http
/api/users/10/accounts/20/expenses/50/categories/5
```

may be unnecessarily complicated if the expense can be accessed directly.

---

## 11. Request and Response Design

A consistent response structure improves frontend integration.

### Successful Response

```json
{
  "success": true,
  "message": "Expenses retrieved successfully",
  "data": [
    {
      "id": 1,
      "title": "Internet Bill",
      "amount": 999
    }
  ]
}
```

### Error Response

```json
{
  "success": false,
  "message": "Validation failed",
  "errors": [
    {
      "field": "amount",
      "message": "Amount must be greater than zero"
    }
  ]
}
```

### Standardized Error Responses

RFC 9457 defines the Problem Details format for HTTP APIs.

Example:

```json
{
  "type": "https://example.com/problems/validation-error",
  "title": "Validation Error",
  "status": 422,
  "detail": "The supplied expense amount is invalid.",
  "instance": "/api/expenses"
}
```

This is particularly useful when building APIs consumed by multiple applications.

---

## 12. API Versioning

API versioning allows you to introduce changes without unexpectedly breaking existing clients.

Suppose your original response is:

```json
{
  "id": 10,
  "name": "John"
}
```

Later, you change the structure:

```json
{
  "user": {
    "id": 10,
    "fullName": "John"
  }
}
```

An older frontend expecting `name` may stop working.

Versioning helps manage such changes.

### Common Versioning Strategies

#### 12.1 URL Versioning

```http
GET /api/v1/users
GET /api/v2/users
```

Advantages:

- Easy to understand.
- Easy to test.
- Easy to configure in routing.

This is a common choice for public REST APIs.

#### 12.2 Header Versioning

```http
GET /api/users
Accept: application/vnd.example.v2+json
```

The version is communicated through a request header.

#### 12.3 Query Parameter Versioning

```http
GET /api/users?version=2
```

This is easy to implement, but less commonly preferred for large public API contracts.

### Versioning Best Practices

1. Do not introduce breaking changes without a migration strategy.
2. Maintain old versions for an announced period.
3. Communicate deprecation clearly.
4. Publish migration documentation.
5. Monitor which clients still use old versions.
6. Remove obsolete versions only according to a published policy.

---

# PART 4: PAGINATION, FILTERING AND SEARCH

## 13. What Is Pagination?

Pagination divides a large dataset into smaller portions.

Imagine your database contains 100,000 expenses.

Returning all records in a single API response is inefficient.

Instead, return a limited number of records.

### 13.1 Offset-Based Pagination

Example:

```http
GET /api/expenses?page=1&limit=10
```

Page 1:

```sql
SELECT *
FROM expenses
ORDER BY created_at DESC, id DESC
LIMIT 10 OFFSET 0;
```

Page 2:

```sql
SELECT *
FROM expenses
ORDER BY created_at DESC, id DESC
LIMIT 10 OFFSET 10;
```

General formula:

```text
OFFSET = (page - 1) * limit
```

Example:

```javascript
const page = Math.max(1, Number(req.query.page) || 1);

const limit = Math.min(
  100,
  Math.max(1, Number(req.query.limit) || 10)
);

const offset = (page - 1) * limit;
```

### Response Example

```json
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 10,
    "totalRecords": 100,
    "totalPages": 10
  }
}
```

**Problem with offset pagination:** Large offsets can become expensive because the database may need to scan and skip many rows.

### 13.2 Cursor-Based Pagination

Cursor pagination uses a reference to the last item returned.

Example:

```http
GET /api/expenses?limit=10&cursor=100
```

The cursor may represent the last record's ID or a combination of ordering values.

Example SQL:

```sql
SELECT *
FROM expenses
WHERE id < ?
ORDER BY id DESC
LIMIT 10;
```

For more reliable ordering when timestamps can be identical, use a compound cursor.

```sql
SELECT *
FROM expenses
WHERE
  created_at < ?
  OR (
    created_at = ?
    AND id < ?
  )
ORDER BY created_at DESC, id DESC
LIMIT 10;
```

### Offset vs Cursor Pagination

| Feature | Offset | Cursor |
|---|---|---|
| Implementation | Simple | More complex |
| Jump to page | Easy | Not naturally supported |
| Large datasets | Can become slow | Often more efficient |
| Changing datasets | Can cause shifting results | More stable |
| Common use | Admin dashboards | Feeds and large datasets |

---

## 14. Filtering

Filtering allows users to retrieve records matching specific conditions.

Example:

```http
GET /api/expenses?category=food
```

Multiple filters:

```http
GET /api/expenses?category=food&status=paid
```

Date filtering:

```http
GET /api/expenses?startDate=2026-01-01&endDate=2026-01-31
```

SQL example:

```sql
SELECT *
FROM expenses
WHERE category = ?
AND status = ?
AND created_at >= ?
AND created_at < ?;
```

Always parameterize SQL values.

Never directly concatenate untrusted input into SQL statements.

---

## 15. Sorting

Sorting allows clients to specify the order of returned records.

Example:

```http
GET /api/expenses?sortBy=amount&order=desc
```

Possible SQL:

```sql
SELECT *
FROM expenses
ORDER BY amount DESC;
```

However, SQL column identifiers generally cannot be supplied as ordinary parameterized values.

Therefore, use an allowlist.

```javascript
const allowedSortFields = [
  "amount",
  "created_at",
  "title"
];

const sortBy = allowedSortFields.includes(req.query.sortBy)
  ? req.query.sortBy
  : "created_at";

const order = req.query.order === "asc"
  ? "ASC"
  : "DESC";
```

Then construct the query using only the validated identifier and order.

---

## 16. Search APIs

Search APIs allow users to find records using keywords.

Example:

```http
GET /api/products/search?q=laptop
```

A simple MySQL search:

```sql
SELECT *
FROM products
WHERE name LIKE ?
```

Parameter:

```javascript
["%laptop%"]
```

For more advanced search requirements, consider:

- MySQL Full-Text Search.
- PostgreSQL Full-Text Search.
- Elasticsearch.
- OpenSearch.
- Dedicated search services.

**Important:** Search terms, filter values, sorting fields and pagination limits should all be validated.

---

# PART 5: RATE LIMITING AND THROTTLING

## 17. What Is Rate Limiting?

Rate limiting restricts how many requests a client can make within a particular period.

Example:

```text
Maximum 100 requests per minute per client.
```

If a client exceeds the limit, the server returns:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
```

Response:

```json
{
  "success": false,
  "message": "Too many requests. Please try again later."
}
```

### Why Is Rate Limiting Important?

It helps protect against:

1. Brute-force attacks.
2. Excessive API consumption.
3. Automated scraping.
4. Accidental request loops.
5. Resource exhaustion.
6. Some denial-of-service patterns.

Rate limiting alone is not a complete DDoS defense.

## 18. Rate Limiting Algorithms

### 18.1 Fixed Window

Allows a fixed number of requests during a fixed time window.

Example:

```text
100 requests per minute.
```

Problem: A client may send 100 requests at the end of one window and another 100 immediately after the next window starts.

### 18.2 Sliding Window

Tracks requests across a continuously moving time window.

This provides smoother rate enforcement.

### 18.3 Token Bucket

The client receives tokens at a defined rate.

Each request consumes a token.

If no tokens remain, requests are rejected or delayed.

### 18.4 Leaky Bucket

Requests enter a queue and are processed at a controlled rate.

This helps smooth sudden traffic bursts.

### Rate Limiting vs Throttling

| Rate Limiting | Throttling |
|---|---|
| Defines request limits | Controls request processing rate |
| May reject excess requests | May delay or slow excess requests |
| Protects resource consumption | Controls traffic flow |

### Express Rate Limiting

Install:

```bash
npm install express-rate-limit
```

Example:

```javascript
import rateLimit from "express-rate-limit";

const apiLimiter = rateLimit({
  windowMs: 60 * 1000,
  limit: 100,
  standardHeaders: "draft-8",
  legacyHeaders: false,
  message: {
    success: false,
    message: "Too many requests"
  }
});

app.use("/api", apiLimiter);
```

For distributed deployments, use a shared store such as Redis rather than relying exclusively on per-process in-memory counters.

Also consider separate policies for login, password reset, file uploads and expensive search endpoints.

---

# PART 6: AUTHENTICATION AND AUTHORIZATION

## 19. Authentication vs Authorization

These are two different security concepts.

### Authentication

Authentication answers:

**Who are you?**

Example:

A user logs in using email and password.

```text
Email + Password
       |
       v
Authentication
       |
       v
User Identity Verified
```

### Authorization

Authorization answers:

**What are you allowed to do?**

Example:

An authenticated user may be allowed to view their own expenses but not another user's expenses.

```text
Authenticated User
       |
       v
Authorization Check
       |
       v
Permission Granted / Denied
```

### Comparison

| Authentication | Authorization |
|---|---|
| Verifies identity | Checks permissions |
| Happens before access decisions | Follows identity verification |
| Login, tokens, sessions | Roles, permissions, ownership |
| Answers who you are | Answers what you can do |

---

## 20. Authentication Methods

Common approaches include:

1. Session-based authentication.
2. Token-based authentication.
3. JWT authentication.
4. API key authentication.
5. OAuth-based delegated authorization.
6. OpenID Connect-based authentication.

### Session-Based Authentication

A typical flow:

```text
User
 |
 | Login
 v
Backend
 |
 | Verify Credentials
 v
Create Session
 |
 | Store Session in Database / Redis
 v
Send Session Cookie
 |
 v
Browser
```

The browser sends the session cookie with subsequent requests.

The backend looks up the session and identifies the user.

### Token-Based Authentication

A typical flow:

```text
User
 |
 | Login
 v
Backend
 |
 | Verify Credentials
 v
Generate Access Token
 |
 v
Client
 |
 | Authorization: Bearer TOKEN
 v
Backend
 |
 | Verify Token
 v
Protected Resource
```

---

# PART 7: JWT ARCHITECTURE

## 21. What Is JWT?

JWT stands for **JSON Web Token**.

It is a compact token format used to represent claims between systems.

A JWT commonly looks like:

```text
xxxxx.yyyyy.zzzzz
```

It contains three parts:

```text
HEADER.PAYLOAD.SIGNATURE
```

### 21.1 Header

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

It identifies the token type and cryptographic algorithm.

### 21.2 Payload

Example:

```json
{
  "sub": "101",
  "email": "john@example.com",
  "role": "user",
  "iat": 1795600000,
  "exp": 1795600900
}
```

The payload contains claims.

Important registered claims include:

| Claim | Meaning |
|---|---|
| iss | Issuer |
| sub | Subject |
| aud | Audience |
| exp | Expiration time |
| nbf | Not valid before |
| iat | Issued at |
| jti | JWT ID |

### 21.3 Signature

The signature helps verify that the token has not been modified and that it was signed by a trusted issuer.

**Important:** A typical signed JWT is not encrypted.

Anyone who obtains the token can generally decode its header and payload.

Therefore, never store:

- Passwords.
- API secrets.
- Private keys.
- Sensitive personal information.

inside a normal signed JWT.

## 22. JWT Authentication Example

Install:

```bash
npm install jsonwebtoken
npm install -D @types/jsonwebtoken
```

Generate a token:

```typescript
import jwt from "jsonwebtoken";

const accessToken = jwt.sign(
  {
    role: "user"
  },
  process.env.JWT_SECRET!,
  {
    subject: "101",
    issuer: "expense-api",
    audience: "expense-client",
    expiresIn: "15m"
  }
);
```

Verify a token:

```typescript
const decoded = jwt.verify(
  token,
  process.env.JWT_SECRET!,
  {
    algorithms: ["HS256"],
    issuer: "expense-api",
    audience: "expense-client"
  }
);
```

In production, also validate the expected token type and relevant claims.

Use an explicit algorithm allowlist. Do not select verification behavior based on an untrusted token header alone.

### Access Token vs Refresh Token

| Access Token | Refresh Token |
|---|---|
| Used to access protected resources | Used to obtain new access tokens |
| Short lifetime | Longer lifetime |
| Sent with API requests | Sent to refresh endpoint |
| Frequently JWT | Often opaque and random |
| Limited permissions | Used for token renewal |

## 23. Refresh Token Rotation

Suppose an access token expires after 15 minutes.

Instead of asking the user to log in again, the client can use a refresh token.

### Basic Flow

```text
Login
  |
  v
Access Token + Refresh Token
  |
  v
Access Token Expires
  |
  v
Send Refresh Token
  |
  v
Backend Validates Refresh Token
  |
  v
Issue New Access Token
```

### What Is Refresh Token Rotation?

Refresh token rotation means issuing a new refresh token whenever an existing refresh token is successfully used.

The old token is invalidated.

Example:

```text
Refresh Token A
      |
      | Refresh Request
      v
Refresh Token B
      |
      | Refresh Request
      v
Refresh Token C
```

If an already-used refresh token appears again, the server can detect possible token theft and revoke the associated token family or session.

### Best Practices

1. Use short-lived access tokens.
2. Store refresh tokens securely.
3. Rotate refresh tokens.
4. Detect refresh token reuse.
5. Revoke tokens during logout.
6. Never log raw tokens.
7. Use secure transport.
8. Consider device/session-specific token management.

For browser applications, an HttpOnly, Secure and appropriately configured SameSite cookie is often preferable for session or refresh-token storage.

Do not assume localStorage is safe for sensitive tokens: JavaScript-accessible storage can be exposed by successful XSS.

---

# PART 8: API KEYS, OAUTH 2.0 AND OPENID CONNECT

## 24. What Is API Key Authentication?

An API key is a credential used to identify an application, integration or client.

Example:

```http
GET /api/products
X-API-Key: YOUR_API_KEY
```

API keys are commonly used for:

- Third-party integrations.
- Server-to-server API access.
- Developer platforms.
- Usage tracking.
- API quota management.

### API Key Best Practices

1. Generate high-entropy keys.
2. Store hashed keys where practical.
3. Support key rotation.
4. Assign scopes and permissions.
5. Apply rate limits.
6. Revoke compromised keys.
7. Never expose private server-side API keys in frontend code.

**Important:** An API key identifies a client or integration. It does not automatically prove the identity of a human user.

---

## 25. What Is OAuth 2.0?

OAuth 2.0 is an authorization framework.

It allows an application to obtain limited access to resources on behalf of a user or another application.

Example:

You have an application that allows users to connect their Google account.

Instead of asking users to give your application their Google password, your application redirects them to Google's authorization system.

The user grants permission.

Google issues an authorization response that your application can exchange for tokens.

### OAuth 2.0 Participants

| Role | Meaning |
|---|---|
| Resource Owner | User who owns the protected data |
| Client | Application requesting access |
| Authorization Server | Server issuing authorization tokens |
| Resource Server | Server hosting protected resources |

### OAuth 2.0 Authorization Code Flow

```text
User
 |
 | 1. Click Login / Connect Account
 v
Client Application
 |
 | 2. Redirect to Authorization Server
 v
Authorization Server
 |
 | 3. User Authenticates and Consents
 v
User
 |
 | 4. Authorization Code
 v
Client Application
 |
 | 5. Exchange Code for Tokens
 v
Authorization Server
 |
 | 6. Access Token
 v
Client Application
 |
 | 7. Access Protected Resource
 v
Resource Server
```

### Common OAuth 2.0 Grant Types

| Grant Type | Common Use |
|---|---|
| Authorization Code + PKCE | Browser and mobile applications |
| Client Credentials | Machine-to-machine communication |
| Device Authorization | Devices with limited input capability |
| Refresh Token | Renewing access tokens |

### Important Security Note

The OAuth 2.0 Implicit Grant and Resource Owner Password Credentials Grant should not be used for new implementations.

Modern OAuth security guidance recommends Authorization Code with PKCE for appropriate clients.

RFC 9700, published in January 2025, documents current OAuth 2.0 security best practices and updates earlier guidance. [RFC Editor](https://www.rfc-editor.org/rfc/rfc9700.html?utm_source=chatgpt.com)

### What Is PKCE?

PKCE stands for **Proof Key for Code Exchange**.

It protects authorization code flows against authorization code interception and injection attacks.

It uses two values:

- `code_verifier`
- `code_challenge`

Simplified process:

```text
Client generates random code_verifier
             |
             v
Create SHA-256 code_challenge
             |
             v
Send code_challenge to Authorization Server
             |
             v
Receive Authorization Code
             |
             v
Send code + code_verifier to Token Endpoint
             |
             v
Authorization Server verifies the challenge
             |
             v
Issue Tokens
```

Use the S256 challenge method.

---

## 26. What Is OpenID Connect?

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0.

OAuth 2.0 primarily addresses authorization.

OpenID Connect adds standardized authentication and identity information.

For example:

```text
Login with Google
Login with Microsoft
Login with another OIDC Provider
```

### Important Tokens

| Token | Purpose |
|---|---|
| Access Token | Access protected APIs |
| ID Token | Communicate authentication and identity claims |
| Refresh Token | Obtain new access tokens when permitted |

### OAuth 2.0 vs OpenID Connect

| OAuth 2.0 | OpenID Connect |
|---|---|
| Authorization framework | Authentication and identity layer |
| Grants access to resources | Communicates user authentication |
| Access token is central | ID token is introduced |
| Does not define standard user identity claims by itself | Defines standardized identity claims |

**Important:** Do not use an ID token as an API access token. The API should validate an access token intended for that API.

The OpenID Connect Core specification defines the ID Token and its validation requirements. [openid.net](https://openid.net/specs/openid-connect-core-1_0.html?utm_source=chatgpt.com)

---

# PART 9: API AUTHORIZATION MODELS

## 27. Role-Based Access Control (RBAC)

RBAC means **Role-Based Access Control**.

Users receive roles, and roles receive permissions.

Example:

```text
Admin
  |
  |-- Create User
  |-- Update User
  |-- Delete User
  |-- View Reports

Manager
  |
  |-- View Reports
  |-- Update Records

User
  |
  |-- View Own Records
  |-- Create Own Records
```

Example:

```typescript
function authorize(...allowedRoles: string[]) {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({
        message: "Authentication required"
      });
    }

    if (!allowedRoles.includes(req.user.role)) {
      return res.status(403).json({
        message: "Access denied"
      });
    }

    next();
  };
}
```

Usage:

```typescript
app.delete(
  "/api/users/:id",
  authenticate,
  authorize("admin"),
  deleteUserController
);
```

## 28. Attribute-Based Access Control (ABAC)

ABAC uses attributes to make authorization decisions.

Example:

A manager can approve an expense if:

- The expense belongs to their department.
- The expense is below their approval limit.
- The expense is in a permitted status.

ABAC is more flexible than simple role-based rules.

## 29. Object-Level Authorization

This is one of the most important API security concepts.

Consider:

```http
GET /api/expenses/100
```

Suppose expense 100 belongs to user 10.

If user 20 requests expense 100, should the API return it?

No, unless user 20 has an explicit permission to access it.

### Vulnerable Query

```sql
SELECT *
FROM expenses
WHERE id = ?;
```

This query does not check ownership.

### Safer Query

```sql
SELECT *
FROM expenses
WHERE id = ?
AND user_id = ?;
```

Example:

```typescript
const expense = await expenseRepository.findByIdAndUserId(
  expenseId,
  authenticatedUser.id
);
```

This prevents a user from accessing another user's resource simply by changing an ID.

This vulnerability is known as **Broken Object Level Authorization (BOLA)** and was identified as API1 in OWASP API Security Top 10 2023. [OWASP API Security Top 10](https://api-security.owasp.org/editions/2023/en/0xa1-broken-object-level-authorization/?utm_source=chatgpt.com)

---

# PART 10: API SECURITY

## 30. OWASP API Security Top 10

OWASP publishes a list of major API security risks.

The 2023 edition includes:

| Risk | Name |
|---|---|
| API1:2023 | Broken Object Level Authorization |
| API2:2023 | Broken Authentication |
| API3:2023 | Broken Object Property Level Authorization |
| API4:2023 | Unrestricted Resource Consumption |
| API5:2023 | Broken Function Level Authorization |
| API6:2023 | Unrestricted Access to Sensitive Business Flows |
| API7:2023 | Server Side Request Forgery |
| API8:2023 | Security Misconfiguration |
| API9:2023 | Improper Inventory Management |
| API10:2023 | Unsafe Consumption of APIs |

Source: [OWASP API Security Top 10](https://owasp.org/projects/api-security-project?utm_source=chatgpt.com)

Let's understand the important risks.

### 30.1 Broken Object Level Authorization

Users access resources belonging to other users.

Example:

```http
GET /api/invoices/100
```

The API checks whether the invoice exists but does not check whether the user is authorized to access it.

**Prevention:** Check ownership and permissions for every object access.

### 30.2 Broken Authentication

Weak authentication can allow attackers to impersonate users.

Examples:

- Weak password policies.
- Poor token validation.
- Missing login rate limits.
- Insecure password reset.
- Long-lived stolen tokens.

**Prevention:** Use secure authentication flows, short-lived access tokens, refresh token rotation, MFA where appropriate and rate limiting.

### 30.3 Broken Object Property Level Authorization

Suppose a user updates their profile:

```json
{
  "name": "John",
  "role": "admin"
}
```

If the backend accepts every property from the request, the user may be able to change their role.

This is a mass-assignment vulnerability.

**Prevention:** Explicitly select the properties a user is allowed to update.

```typescript
const allowedFields = {
  name: req.body.name,
  phone: req.body.phone
};
```

Never automatically update database records with the entire untrusted request body.

### 30.4 Unrestricted Resource Consumption

An API allows excessively expensive requests.

Examples:

- Requesting millions of records.
- Uploading enormous files.
- Running expensive search queries.
- Repeated password reset attempts.

**Prevention:**

- Rate limiting.
- Maximum pagination limits.
- Request size limits.
- Query timeouts.
- Upload restrictions.
- Resource quotas.

### 30.5 Broken Function Level Authorization

A normal user accesses an administrative endpoint.

Example:

```http
DELETE /api/admin/users/10
```

The server verifies that the user is logged in but does not verify that the user is an administrator.

**Prevention:** Apply authorization checks to every sensitive function.

### 30.6 Server-Side Request Forgery (SSRF)

SSRF occurs when an attacker causes the server to make unintended requests to other systems.

Example:

Your API accepts an image URL:

```json
{
  "imageUrl": "https://example.com/image.jpg"
}
```

The backend downloads that URL.

An attacker might try to make the server access an internal address instead.

**Prevention:**

- Allowlist permitted hosts.
- Block private and reserved IP ranges where appropriate.
- Validate redirects.
- Restrict outbound network access.
- Avoid fetching arbitrary URLs.

### 30.7 Security Misconfiguration

Examples:

- Debug mode enabled in production.
- Exposed stack traces.
- Weak CORS configuration.
- Missing security headers.
- Public database ports.
- Default credentials.

**Prevention:** Use secure configuration and environment-specific deployment settings.

---

## 31. Input Validation

Never trust client input.

Example request:

```json
{
  "title": "Internet Bill",
  "amount": 999,
  "categoryId": 2
}
```

The backend should validate:

- Title is present.
- Title has an acceptable length.
- Amount is a valid positive number.
- Category ID is valid.
- Unexpected properties are rejected or ignored according to the API contract.

### Using Zod

Install:

```bash
npm install zod
```

Example:

```typescript
import { z } from "zod";

const expenseSchema = z.object({
  title: z.string().min(3).max(100),

  amount: z.number().positive(),

  categoryId: z.number().int().positive()
}).strict();
```

Validate:

```typescript
const result = expenseSchema.safeParse(req.body);

if (!result.success) {
  return res.status(422).json({
    success: false,
    errors: result.error.issues
  });
}

const expenseData = result.data;
```

The `.strict()` method rejects unknown properties.

This helps prevent unexpected fields from entering your application logic.

---

## 32. SQL Injection

SQL injection occurs when untrusted input changes the intended structure of a SQL query.

### Vulnerable Code

```javascript
const email = req.body.email;

const query = `
  SELECT *
  FROM users
  WHERE email = '${email}'
`;
```

An attacker may supply SQL syntax that changes the query's meaning.

### Safe Code

```javascript
const [rows] = await db.execute(
  "SELECT * FROM users WHERE email = ?",
  [email]
);
```

Use:

- Prepared statements.
- Parameterized queries.
- Safe ORM methods.
- Strict input validation.

Remember that ORMs do not automatically protect every raw SQL query.

---

## 33. Cross-Origin Resource Sharing (CORS)

CORS stands for **Cross-Origin Resource Sharing**.

It is a browser security mechanism that controls whether frontend JavaScript running on one origin can access resources from another origin.

An origin consists of:

```text
Protocol + Hostname + Port
```

Example:

```text
Frontend: http://localhost:3000
Backend:  http://localhost:5000
```

These are different origins because their ports differ.

### CORS Example

Install:

```bash
npm install cors
```

Configure:

```typescript
import cors from "cors";

app.use(
  cors({
    origin: [
      "http://localhost:3000",
      "https://myfrontend.com"
    ],
    methods: ["GET", "POST", "PUT", "PATCH", "DELETE"],
    credentials: true
  })
);
```

### What Is a CORS Preflight Request?

For certain cross-origin requests, the browser first sends an OPTIONS request.

Example:

```http
OPTIONS /api/users
Origin: https://frontend.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type, authorization
```

The server responds with permitted origins, methods and headers.

The browser then decides whether to send the actual request.

### Important CORS Rules

1. Do not use wildcard origins for credentialed requests.
2. Allow only trusted origins.
3. Configure allowed methods carefully.
4. Configure allowed headers carefully.
5. Understand that CORS is not authentication.
6. Understand that CORS is not authorization.

CORS controls browser access to responses. It does not prevent non-browser clients from calling your API.

---

## 34. Cross-Site Request Forgery (CSRF)

CSRF stands for **Cross-Site Request Forgery**.

It occurs when an attacker tricks a victim's browser into sending an unintended authenticated request to a website.

Example:

1. User logs into a banking website.
2. The browser stores an authentication cookie.
3. User visits a malicious website.
4. That website attempts to trigger an action on the banking website.
5. The browser may automatically attach the banking cookie.

### Prevention

- Use appropriate SameSite cookie settings.
- Use anti-CSRF tokens where required.
- Validate Origin or Referer where appropriate.
- Avoid state-changing GET endpoints.
- Require reauthentication for sensitive actions.

**Important distinction:**

CSRF is particularly relevant when browsers automatically attach authentication credentials, such as cookies.

Using bearer tokens manually attached by JavaScript changes the CSRF threat model, but it does not eliminate security risks. XSS can still steal or misuse JavaScript-accessible tokens.

---

## 35. Cross-Site Scripting (XSS)

XSS occurs when an attacker injects malicious script into a page viewed by other users.

Example:

A comment contains:

```html
<script>
  alert("XSS");
</script>
```

If an application renders this as executable HTML, it can create an XSS vulnerability.

### Types of XSS

| Type | Description |
|---|---|
| Stored XSS | Malicious script is stored and later displayed |
| Reflected XSS | Malicious input is reflected in a response |
| DOM-Based XSS | Client-side JavaScript introduces the vulnerability |

### Prevention

1. Escape output appropriately.
2. Avoid unsafe HTML insertion.
3. Sanitize HTML when rich HTML is genuinely required.
4. Use a suitable Content Security Policy.
5. Avoid dangerous DOM APIs.
6. Keep dependencies updated.

**Important:** A JSON API does not automatically execute script content. XSS usually becomes a problem when frontend code renders untrusted data unsafely.

---

# PART 11: GRAPHQL

## 36. What Is GraphQL?

GraphQL is a query language and runtime for APIs.

It was designed to let clients request the exact data they need.

Imagine an application needs a user and that user's expenses.

With REST, it might call:

```http
GET /api/users/10
GET /api/users/10/expenses
```

With GraphQL, it can request both through one query:

```graphql
query {
  user(id: 10) {
    name
    email

    expenses {
      title
      amount
    }
  }
}
```

Possible response:

```json
{
  "data": {
    "user": {
      "name": "John",
      "email": "john@example.com",
      "expenses": [
        {
          "title": "Internet Bill",
          "amount": 999
        }
      ]
    }
  }
}
```

GraphQL commonly uses one endpoint:

```http
POST /graphql
```

However, GraphQL is not simply a replacement for REST. Both approaches have different strengths.

---

## 37. GraphQL Core Concepts

### 37.1 Schema

A schema defines the structure of your GraphQL API.

Example:

```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  users: [User!]!
  user(id: ID!): User
}
```

### 37.2 Types

GraphQL supports different types.

| Type | Description |
|---|---|
| String | Text |
| Int | Integer |
| Float | Floating-point number |
| Boolean | True or false |
| ID | Unique identifier |
| Object Type | Custom object |
| Enum | Fixed set of values |
| Input Type | Structured input |

### 37.3 Non-Null Operator

The `!` symbol means the field cannot be null.

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

### 37.4 List Types

```graphql
users: [User]
```

A list of users where individual elements may be null.

```graphql
users: [User!]
```

A list of non-null users, but the list itself may be null.

```graphql
users: [User!]!
```

A non-null list containing only non-null users.

---

## 38. GraphQL Queries

Queries retrieve data.

Example:

```graphql
query GetUser {
  user(id: "10") {
    id
    name
    email
  }
}
```

GraphQL lets the client specify which fields it needs.

Example:

```graphql
query {
  user(id: "10") {
    name
  }
}
```

Only the name is requested.

---

## 39. GraphQL Mutations

Mutations modify data.

Example:

```graphql
mutation CreateUser {
  createUser(
    name: "John"
    email: "john@example.com"
  ) {
    id
    name
    email
  }
}
```

A mutation can:

- Create a record.
- Update a record.
- Delete a record.
- Trigger a business operation.

---

## 40. GraphQL Resolvers

Resolvers are functions that provide data for GraphQL fields.

Example:

```typescript
const resolvers = {
  Query: {
    users: async () => {
      return userService.getAllUsers();
    },

    user: async (_, args) => {
      return userService.getUserById(args.id);
    }
  }
};
```

A resolver receives arguments and returns the requested data.

### Resolver Parameters

A resolver commonly receives:

```typescript
(parent, args, context, info)
```

| Parameter | Meaning |
|---|---|
| parent | Result from the parent resolver |
| args | Arguments supplied by the client |
| context | Shared request context |
| info | Execution information about the GraphQL query |

### Context Example

```typescript
const context = {
  user: authenticatedUser,
  db
};
```

Resolvers can access the authenticated user through the context.

However, authentication information alone does not guarantee authorization. Each sensitive resolver or underlying service must enforce the appropriate permissions.

---

## 41. GraphQL Schema Example

```graphql
type Expense {
  id: ID!
  title: String!
  amount: Float!
  category: String!
}

type Query {
  expenses: [Expense!]!
  expense(id: ID!): Expense
}

type Mutation {
  createExpense(
    title: String!
    amount: Float!
    category: String!
  ): Expense!

  deleteExpense(id: ID!): Boolean!
}
```

This schema defines:

- Expense object.
- Query operations.
- Mutation operations.
- Field types.
- Required and optional values.

---

## 42. GraphQL Performance: N+1 Problem

The N+1 problem occurs when resolving a list of records triggers an additional database query for every record.

Suppose:

- One query retrieves 100 users.
- One additional query retrieves expenses for each user.

Total:

```text
1 + 100 = 101 database queries
```

This can significantly affect performance.

### Solutions

1. DataLoader.
2. Batch database queries.
3. Eager loading.
4. Query optimization.
5. Appropriate caching.

### Conceptual DataLoader Example

```typescript
const expenseLoader = new DataLoader(
  async (userIds: readonly string[]) => {
    const expenses = await expenseRepository.findByUserIds(
      [...userIds]
    );

    return userIds.map(userId =>
      expenses.filter(expense => expense.userId === userId)
    );
  }
);
```

Instead of executing one query per user, DataLoader can batch requests.

**Important:** DataLoader instances are commonly created per request to avoid accidental cross-user data leakage through shared caches.

## 43. GraphQL Security and Optimization

GraphQL introduces additional concerns.

### Query Depth Limiting

Prevent excessively nested queries.

### Query Complexity Limiting

Restrict expensive query structures.

### Rate Limiting

Limit requests based on users, clients or query cost.

### Authorization

Check permissions in resolvers and the service/data-access layer.

### Avoid Sensitive Field Exposure

Do not expose fields simply because they exist in your database.

### Persisted Queries

Allow approved or registered queries to be executed without accepting arbitrary query text in some architectures.

### GraphQL vs REST

| Feature | REST | GraphQL |
|---|---|---|
| API endpoints | Multiple | Commonly one |
| Data selection | Server-defined representations | Client selects fields |
| Over-fetching | Possible | Can be reduced |
| Under-fetching | May require multiple requests | Often reduced |
| Caching | HTTP caching is straightforward | Requires additional strategy |
| Schema | OpenAPI or other contracts | GraphQL schema |
| Learning curve | Relatively simple | More concepts |

**Important:** GraphQL can reduce unnecessary data transfer, but a poorly designed GraphQL API can be slower and more expensive than a well-designed REST API.

---

# PART 12: WEBHOOKS

## 44. What Is a Webhook?

A webhook is an HTTP callback used by one system to notify another system about an event.

For example, a payment provider sends your backend a notification when a payment succeeds.

```text
Payment Provider
       |
       | Payment Completed
       |
       v
Webhook Endpoint
       |
       | Verify Signature
       |
       v
Process Event
       |
       v
Update Database
```

Example endpoint:

```http
POST /api/webhooks/payment
```

Payload:

```json
{
  "event": "payment.success",
  "eventId": "evt_12345",
  "paymentId": "pay_100",
  "amount": 5000
}
```

### Webhooks vs Polling

| Webhooks | Polling |
|---|---|
| Provider sends event | Client repeatedly checks |
| Event-driven | Time-driven |
| Can reduce unnecessary requests | May generate unnecessary requests |
| Requires a reachable callback endpoint | Requires periodic client requests |

## 45. Webhook Security

Never blindly trust a webhook payload.

A malicious user could attempt to send a fake payment success notification.

### Verify Webhook Signatures

A common design uses HMAC.

Simplified conceptual example:

```typescript
import crypto from "crypto";

function verifySignature(
  rawBody: Buffer,
  receivedSignature: string,
  secret: string
) {
  const expected = crypto
    .createHmac("sha256", secret)
    .update(rawBody)
    .digest("hex");

  const received = Buffer.from(receivedSignature, "hex");
  const expectedBuffer = Buffer.from(expected, "hex");

  if (received.length !== expectedBuffer.length) {
    return false;
  }

  return crypto.timingSafeEqual(
    received,
    expectedBuffer
  );
}
```

This example illustrates the principle. Real webhook providers may use different signature formats, timestamp schemes and canonicalization rules. Follow the provider's exact documentation.

### Important Webhook Best Practices

1. Verify signatures.
2. Use the original raw request body when required by the signature scheme.
3. Check timestamps where supported.
4. Prevent replay attacks.
5. Store processed event IDs.
6. Make event handling idempotent.
7. Return acknowledgements quickly.
8. Process expensive work asynchronously.
9. Implement retry handling.
10. Maintain event-processing logs.

### Why Is Idempotency Important?

Webhook providers may deliver the same event multiple times.

Your application must avoid processing the same payment twice.

For example:

```text
Event ID: evt_123
      |
      v
Check Event ID in Database
      |
      |-- Already Processed --> Ignore Duplicate
      |
      |-- New Event ---------> Process and Store ID
```

---

# PART 13: OPENAPI AND SWAGGER

## 46. What Is OpenAPI?

OpenAPI is a standard specification for describing HTTP APIs.

It allows developers and tools to understand an API without inspecting its implementation.

An OpenAPI document describes:

- API endpoints.
- HTTP methods.
- Parameters.
- Request bodies.
- Response schemas.
- Authentication mechanisms.
- Status codes.

The OpenAPI Specification is maintained independently of Swagger tooling.

The official specification site currently lists OpenAPI 3.2.1 among its published versions. [OpenAPI Initiative Publications](https://spec.openapis.org/oas/?utm_source=chatgpt.com)

## 47. What Is Swagger?

Swagger is an ecosystem of tools used to work with OpenAPI descriptions.

Common tools include:

- Swagger UI.
- Swagger Editor.
- Swagger Codegen.
- Swagger-related tooling for API documentation.

### OpenAPI vs Swagger

| OpenAPI | Swagger |
|---|---|
| API description specification | Tooling ecosystem |
| Defines API structure | Helps create, visualize and interact with APIs |
| Machine-readable contract | Documentation and development tools |

## 48. OpenAPI Example

A simplified OpenAPI document:

```yaml
openapi: 3.1.0

info:
  title: Expense Tracker API
  version: 1.0.0

servers:
  - url: https://api.example.com

paths:
  /expenses:
    get:
      summary: Get all expenses

      responses:
        "200":
          description: Expenses retrieved successfully

    post:
      summary: Create an expense

      requestBody:
        required: true

        content:
          application/json:
            schema:
              type: object

              required:
                - title
                - amount

              properties:
                title:
                  type: string

                amount:
                  type: number

      responses:
        "201":
          description: Expense created successfully
```

### Important OpenAPI Components

| Component | Purpose |
|---|---|
| openapi | Specification version |
| info | API metadata |
| servers | API base URLs |
| paths | Available endpoints |
| parameters | Query, path, header and cookie parameters |
| requestBody | Request body structure |
| responses | Possible responses |
| components | Reusable definitions |
| securitySchemes | Authentication definitions |
| tags | Organize operations |

### Why Use OpenAPI?

1. Generate interactive API documentation.
2. Generate client SDKs.
3. Validate API contracts.
4. Support frontend/backend collaboration.
5. Automate API testing.
6. Reduce documentation inconsistencies.

Official specification: [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.1.html?utm_source=chatgpt.com)

---

# PART 14: API TESTING

## 49. Why Is API Testing Important?

API testing verifies that your backend behaves correctly.

You should test:

- Correct requests.
- Invalid requests.
- Authentication.
- Authorization.
- Database operations.
- Error handling.
- Rate limits.
- Pagination.
- Security boundaries.

## 50. Types of API Testing

| Testing Type | Purpose |
|---|---|
| Unit Testing | Test individual functions |
| Integration Testing | Test interaction between components |
| End-to-End Testing | Test complete workflows |
| Contract Testing | Verify API request/response contracts |
| Performance Testing | Measure response time and throughput |
| Security Testing | Identify security weaknesses |

### Example API Test with Supertest

Install:

```bash
npm install -D jest supertest
npm install -D @types/supertest
```

Example:

```typescript
import request from "supertest";
import app from "../src/app";

describe("GET /api/expenses", () => {
  it("should return expenses", async () => {
    const response = await request(app)
      .get("/api/expenses")
      .set("Authorization", "Bearer valid-test-token");

    expect(response.status).toBe(200);

    expect(response.body.success).toBe(true);
  });
});
```

This is an illustrative test. In a real test suite, use controlled test data, test authentication setup and isolated database state.

### Important Test Scenarios

For a create-expense API:

| Scenario | Expected Result |
|---|---|
| Valid request | 201 Created |
| Missing title | Validation error |
| Negative amount | Validation error |
| Invalid token | 401 Unauthorized |
| No permission | 403 Forbidden |
| Database unavailable | Controlled server error |
| Duplicate idempotency key | No duplicate resource creation |

---

# PART 15: PRODUCTION-READY API ARCHITECTURE

## 51. Production API Architecture

A production API should separate responsibilities.

Example:

```text
                 Client
                   |
                   v
               API Gateway
                   |
                   v
              Express Server
                   |
          ---------------------
          |                   |
          v                   v
     Authentication      Rate Limiting
          |                   |
          ---------+----------
                   |
                   v
              Middleware
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

### Recommended Project Structure

```text
src/
│
├── config/
│   ├── database.ts
│   └── environment.ts
│
├── modules/
│   │
│   ├── auth/
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── auth.repository.ts
│   │   ├── auth.routes.ts
│   │   └── auth.validation.ts
│   │
│   ├── users/
│   │   ├── user.controller.ts
│   │   ├── user.service.ts
│   │   ├── user.repository.ts
│   │   ├── user.routes.ts
│   │   └── user.validation.ts
│   │
│   └── expenses/
│       ├── expense.controller.ts
│       ├── expense.service.ts
│       ├── expense.repository.ts
│       ├── expense.routes.ts
│       └── expense.validation.ts
│
├── middleware/
│   ├── authenticate.ts
│   ├── authorize.ts
│   ├── errorHandler.ts
│   ├── rateLimiter.ts
│   └── validateRequest.ts
│
├── utils/
│   ├── ApiError.ts
│   └── ApiResponse.ts
│
├── docs/
│   └── openapi.yaml
│
├── app.ts
└── server.ts
```

---

## 52. Complete REST API Example: Expense Tracker

Let's build the conceptual structure for an expense creation API.

### Endpoint

```http
POST /api/v1/expenses
```

Request:

```json
{
  "title": "Internet Bill",
  "amount": 999,
  "categoryId": 2
}
```

The authenticated user is identified from the server-side authentication context.

Do not trust a `userId` supplied by the frontend to determine ownership.

### Step 1: Validation Schema

```typescript
import { z } from "zod";

export const createExpenseSchema = z.object({
  title: z.string().min(3).max(100),

  amount: z.number().positive(),

  categoryId: z.number().int().positive()
}).strict();
```

### Step 2: Authentication Middleware

Simplified example:

```typescript
import jwt from "jsonwebtoken";

export function authenticate(req, res, next) {
  const authorization = req.headers.authorization;

  if (!authorization?.startsWith("Bearer ")) {
    return res.status(401).json({
      success: false,
      message: "Authentication required"
    });
  }

  const token = authorization.slice(7);

  try {
    const decoded = jwt.verify(
      token,
      process.env.JWT_SECRET!,
      {
        algorithms: ["HS256"],
        issuer: "expense-api",
        audience: "expense-client"
      }
    );

    req.user = {
      id: decoded.sub
    };

    next();

  } catch {
    return res.status(401).json({
      success: false,
      message: "Invalid or expired token"
    });
  }
}
```

In a complete TypeScript project, define an Express request type augmentation for `req.user`, and validate that the token contains the expected subject and claims.

### Step 3: Controller

```typescript
export async function createExpenseController(req, res, next) {
  try {
    const result = createExpenseSchema.safeParse(req.body);

    if (!result.success) {
      return res.status(422).json({
        success: false,
        errors: result.error.issues
      });
    }

    const expense = await expenseService.createExpense({
      ...result.data,
      userId: req.user.id
    });

    return res.status(201).json({
      success: true,
      message: "Expense created successfully",
      data: expense
    });

  } catch (error) {
    next(error);
  }
}
```

### Step 4: Service Layer

```typescript
export class ExpenseService {
  constructor(private expenseRepository) {}

  async createExpense(data) {
    if (data.amount <= 0) {
      throw new Error("Invalid expense amount");
    }

    return this.expenseRepository.create(data);
  }
}
```

### Step 5: Repository

```typescript
export class ExpenseRepository {
  constructor(private db) {}

  async create(data) {
    const [result] = await this.db.execute(
      `INSERT INTO expenses
       (title, amount, category_id, user_id)
       VALUES (?, ?, ?, ?)`,
      [
        data.title,
        data.amount,
        data.categoryId,
        data.userId
      ]
    );

    return {
      id: result.insertId,
      ...data
    };
  }
}
```

### Step 6: Routes

```typescript
import express from "express";

const router = express.Router();

router.post(
  "/",
  authenticate,
  createExpenseController
);

export default router;
```

### Step 7: Register Routes

```typescript
app.use(
  "/api/v1/expenses",
  expenseRoutes
);
```

### Complete Request Flow

```text
POST /api/v1/expenses
          |
          v
     Express Router
          |
          v
 Authentication
          |
          v
    Controller
          |
          v
 Input Validation
          |
          v
    Service Layer
          |
          v
  Repository Layer
          |
          v
       MySQL
          |
          v
    JSON Response
```

This example demonstrates the separation of concerns:

- Middleware handles authentication.
- Controller handles HTTP concerns.
- Validation checks incoming data.
- Service handles application logic.
- Repository handles persistence.

For production, also add centralized error handling, category ownership validation, transaction handling where needed, logging, rate limits and automated tests.

---

# PART 16: ADDITIONAL API ENGINEERING CONCEPTS

## 53. API Caching

Caching stores frequently requested data so that the application does not need to recompute or retrieve it repeatedly.

Example:

```http
GET /api/products
```

Possible response header:

```http
Cache-Control: public, max-age=300
```

This indicates that the response may be cached for five minutes, subject to the relevant HTTP caching rules.

### Common Caching Layers

```text
Browser Cache
     |
     v
CDN Cache
     |
     v
API Cache
     |
     v
Database
```

Common technologies:

- Redis.
- CDN caching.
- Browser caching.
- Reverse proxy caching.

### ETag

An ETag identifies a particular version of a resource representation.

Example:

```http
ETag: "abc123"
```

A client can later send:

```http
If-None-Match: "abc123"
```

If the resource has not changed, the server may return:

```http
304 Not Modified
```

This can reduce unnecessary data transfer.

**Security note:** Do not publicly cache personalized or sensitive responses without a deliberate and correct cache policy.

---

## 54. Idempotency Keys

Suppose a user clicks the Pay button twice.

The frontend may accidentally send two requests:

```http
POST /api/payments
```

Without protection, the user might be charged twice.

An idempotency key can help prevent duplicate processing.

Example:

```http
POST /api/payments
Idempotency-Key: payment-request-123
```

The server stores the key and the result of the first successful processing attempt.

If the same request is repeated with the same key, the server returns the existing result instead of creating another payment.

### Typical Processing Flow

```text
Receive Request
      |
      v
Check Idempotency Key
      |
      |-- Existing Key --> Return Stored Result
      |
      |-- New Key -------> Process Payment
                              |
                              v
                       Store Result + Key
                              |
                              v
                         Return Result
```

The key should be associated with the authenticated user or client, and the server should detect reuse of the same key with a different request payload.

---

## 55. API Gateway

An API Gateway is an entry point that manages incoming API requests and routes them to appropriate backend services.

Example:

```text
              Client
                |
                v
           API Gateway
                |
       ---------------------
       |         |         |
       v         v         v
   User API  Order API  Payment API
       |         |         |
       v         v         v
    Database  Database  Database
```

### Common API Gateway Responsibilities

- Request routing.
- Authentication integration.
- Rate limiting.
- TLS termination.
- Request transformation.
- Monitoring.
- Logging.
- API key validation.

An API Gateway is useful in microservice architectures, but a small monolithic application does not necessarily need one.

---

# PART 17: COMMON API DEVELOPMENT MISTAKES

## 56. Mistakes to Avoid

| Mistake | Better Approach |
|---|---|
| Using GET to change data | Use appropriate state-changing methods |
| Returning 200 for every result | Use meaningful HTTP status codes |
| Trusting frontend validation | Validate again on the backend |
| Exposing database errors | Return safe, structured errors |
| Accepting arbitrary request fields | Use explicit schemas and allowlists |
| Ignoring object ownership | Enforce object-level authorization |
| Putting tokens in URLs | Use appropriate authorization headers or secure cookies |
| Storing secrets in JWT payloads | Keep sensitive values out of tokens |
| Allowing unlimited requests | Apply rate limits and resource quotas |
| Using wildcard CORS carelessly | Allow trusted origins only |
| Ignoring webhook signatures | Verify signatures and replay protection |
| Returning every database record | Implement pagination |
| Building undocumented APIs | Maintain OpenAPI documentation |
| Logging passwords and tokens | Redact sensitive information |
| Using offset pagination for every dataset | Consider cursor pagination |
| Ignoring API compatibility | Version and deprecate deliberately |

---

# PART 18: PRODUCTION API SECURITY CHECKLIST

## 57. Security Checklist

Before deploying an API, verify the following.

### Authentication

- [ ] Passwords are hashed using a suitable password-hashing algorithm.
- [ ] Access tokens have limited lifetimes.
- [ ] Refresh tokens are securely managed.
- [ ] Token signatures and claims are validated.
- [ ] Login endpoints are rate-limited.
- [ ] Logout and revocation are implemented where required.

### Authorization

- [ ] Every protected endpoint checks authentication.
- [ ] Administrative endpoints check appropriate roles.
- [ ] Object ownership is verified.
- [ ] Sensitive properties cannot be mass-assigned.
- [ ] Users cannot access other users' private records.

### Input Validation

- [ ] Request bodies are validated.
- [ ] Query parameters are validated.
- [ ] Path parameters are validated.
- [ ] Pagination limits are enforced.
- [ ] Sorting fields use allowlists.
- [ ] File uploads have size and type restrictions.

### Infrastructure

- [ ] HTTPS is enabled.
- [ ] Secrets are stored securely.
- [ ] Database ports are not unnecessarily exposed.
- [ ] Security headers are configured.
- [ ] Dependencies are regularly audited.
- [ ] Production error responses do not expose stack traces.

### Monitoring

- [ ] API requests are logged.
- [ ] Sensitive data is redacted.
- [ ] Authentication failures are monitored.
- [ ] Rate-limit events are monitored.
- [ ] Error rates are monitored.
- [ ] Webhook processing failures are tracked.

Express also provides official production security guidance covering TLS, Helmet, secure cookies, input handling, dependency security and brute-force protection. [Express.js](https://expressjs.com/en/advanced/best-practice-security/?utm_source=chatgpt.com)

---

# PART 19: INTERVIEW QUESTIONS AND ANSWERS

## 58. Basic Questions

### Q1. What is an API?

An API is an interface that allows different software systems to communicate and exchange data.

### Q2. What is REST?

REST is an architectural style for designing network-based applications around resources and standardized interactions.

### Q3. What is HTTP?

HTTP is a request-response protocol used for communication between clients and servers.

### Q4. What is the difference between PUT and PATCH?

PUT generally replaces a resource representation, whereas PATCH partially modifies a resource.

### Q5. What is the difference between POST and PUT?

POST is commonly used to create resources or perform operations. PUT replaces a resource at a known URI and is idempotent.

### Q6. What is an HTTP status code?

It is a three-digit code that communicates the result of an HTTP request.

### Q7. What is the difference between 401 and 403?

401 indicates missing or invalid authentication. 403 indicates that the authenticated client is not permitted to perform the requested action.

### Q8. What is statelessness in REST?

Each request must contain the information necessary for the server to process it without depending on hidden session context from previous requests.

### Q9. What is pagination?

Pagination divides large datasets into smaller responses.

### Q10. What is API versioning?

API versioning allows developers to introduce API changes while maintaining compatibility with existing clients.

## 59. Intermediate Questions

### Q11. What is idempotency?

An operation is idempotent when repeating it has the same intended effect as performing it once.

### Q12. What is rate limiting?

Rate limiting restricts the number of requests a client can make within a specified period.

### Q13. What is the difference between rate limiting and throttling?

Rate limiting enforces request limits, while throttling controls the rate at which requests are processed.

### Q14. What is JWT?

JWT is a compact token format used to represent claims between systems.

### Q15. Is JWT encrypted?

Not necessarily. A typical signed JWT is readable by anyone who obtains it. Its signature provides integrity and authenticity, not confidentiality.

### Q16. What is the difference between an access token and a refresh token?

An access token authorizes access to protected resources. A refresh token is used to obtain new access tokens.

### Q17. What is CORS?

CORS is a browser security mechanism that controls cross-origin access to resources.

### Q18. Does CORS protect an API from unauthorized access?

No. CORS is not authentication or authorization. Non-browser clients can call APIs without browser CORS enforcement.

### Q19. What is CSRF?

CSRF is an attack that tricks a user's browser into sending an unintended authenticated request.

### Q20. What is XSS?

XSS is a vulnerability that allows malicious scripts to execute in another user's browser.

## 60. Advanced Questions

### Q21. What is OAuth 2.0?

OAuth 2.0 is an authorization framework that allows clients to obtain limited access to protected resources.

### Q22. What is OpenID Connect?

OpenID Connect adds standardized authentication and identity claims on top of OAuth 2.0.

### Q23. What is PKCE?

PKCE is a security extension for OAuth authorization code flows that protects authorization codes using a verifier and challenge.

### Q24. What is BOLA?

Broken Object Level Authorization occurs when an API allows users to access objects without properly checking whether they are authorized to access those objects.

### Q25. What is GraphQL?

GraphQL is a query language and runtime that allows clients to request specific data fields through a schema-defined API.

### Q26. What is a GraphQL resolver?

A resolver is a function responsible for returning the data for a GraphQL field.

### Q27. What is the N+1 problem?

It occurs when retrieving a list of records triggers an additional database query for every record, resulting in excessive queries.

### Q28. How can you prevent the N+1 problem?

Use DataLoader, batching, eager loading and optimized database queries.

### Q29. What is a webhook?

A webhook is an HTTP callback that notifies another system when a particular event occurs.

### Q30. Why should webhook signatures be verified?

To confirm that the webhook came from a trusted provider and that its contents have not been altered.

### Q31. What is OpenAPI?

OpenAPI is a standard specification for describing HTTP APIs.

### Q32. What is Swagger?

Swagger is a collection of tools used to create, visualize and work with OpenAPI specifications.

### Q33. What is an API Gateway?

An API Gateway is an entry point that routes and manages requests to backend services.

### Q34. What is an API contract?

An API contract defines the expected requests, responses, data structures and behavior of an API.

### Q35. What is cursor-based pagination?

It uses a reference to the last returned record to retrieve the next set of results.

### Q36. Why are parameterized SQL queries important?

They separate SQL instructions from user-provided values, helping prevent SQL injection.

### Q37. What is an idempotency key?

It is a unique request identifier used to prevent duplicate processing when a client retries an operation.

### Q38. Why should JWT claims such as issuer and audience be validated?

To ensure that the token was issued by a trusted authority and is intended for the receiving application or API.

### Q39. What is refresh token rotation?

It is a strategy where each successful refresh operation issues a new refresh token and invalidates the previous one.

### Q40. Can GraphQL replace every REST API?

No. GraphQL and REST solve different problems. The correct approach depends on client requirements, API complexity, caching, performance and system architecture.

---

# PART 20: PRACTICAL ASSIGNMENTS

## 61. Assignment 1 – REST API Fundamentals

Create a Users API.

Required endpoints:

```http
GET /api/v1/users
GET /api/v1/users/:id
POST /api/v1/users
PUT /api/v1/users/:id
PATCH /api/v1/users/:id
DELETE /api/v1/users/:id
```

Requirements:

- Use Express.js.
- Use TypeScript.
- Connect MySQL.
- Implement validation.
- Use correct HTTP methods and status codes.

## 62. Assignment 2 – Advanced Pagination

Create:

```http
GET /api/v1/expenses
```

Support:

```text
?page=1
&limit=10
&sortBy=amount
&order=desc
&category=food
&startDate=2026-01-01
&endDate=2026-01-31
```

Requirements:

- Validate every query parameter.
- Restrict sorting fields.
- Enforce maximum pagination limits.
- Implement pagination metadata.

## 63. Assignment 3 – JWT Authentication

Implement:

```http
POST /api/auth/register
POST /api/auth/login
POST /api/auth/refresh
POST /api/auth/logout
GET /api/auth/profile
```

Requirements:

- Hash passwords.
- Generate access tokens.
- Implement refresh token rotation.
- Validate token claims.
- Revoke refresh tokens on logout.

## 64. Assignment 4 – RBAC

Create three roles:

```text
Admin
Manager
User
```

Implement permissions for:

- User management.
- Expense management.
- Reports.
- Administrative operations.

Also verify that users cannot access other users' private expenses.

## 65. Assignment 5 – API Security

Implement:

- Zod validation.
- Rate limiting.
- Helmet.
- Secure CORS configuration.
- SQL parameterization.
- Centralized error handling.
- Request size limits.

## 66. Assignment 6 – GraphQL API

Build a GraphQL API with:

```text
User
Expense
Category
```

Implement:

- Queries.
- Mutations.
- Resolvers.
- Authentication.
- Authorization.
- DataLoader.

## 67. Assignment 7 – Webhook Integration

Create:

```http
POST /api/webhooks/payment
```

Implement:

- Signature verification.
- Event ID validation.
- Duplicate-event detection.
- Idempotent processing.
- Webhook logs.

## 68. Assignment 8 – OpenAPI Documentation

Document all REST endpoints.

Include:

- Request schemas.
- Response schemas.
- Authentication.
- Error responses.
- Status codes.
- Query parameters.

## 69. Assignment 9 – API Testing

Write tests for:

- Successful requests.
- Invalid requests.
- Authentication failures.
- Authorization failures.
- Pagination.
- Rate limits.
- Duplicate payment requests.

## 70. Assignment 10 – Production API Project

Build a complete Expense Tracker API.

### Required Features

**Authentication**

- Registration.
- Login.
- JWT access token.
- Refresh token rotation.
- Logout.

**Expense Management**

- Create expense.
- Update expense.
- Delete expense.
- Get expenses.
- Filter expenses.
- Sort expenses.
- Paginate expenses.

**Security**

- RBAC.
- Object-level authorization.
- Input validation.
- Rate limiting.
- CORS.
- Secure headers.

**Documentation**

- OpenAPI.
- Swagger UI.

**Testing**

- Unit testing.
- Integration testing.
- API security testing.

**Advanced Features**

- GraphQL endpoint.
- Payment webhook simulation.
- Idempotency keys.
- Redis caching.

---

# PART 21: QUICK REVISION

## 71. Important Concepts Summary

| Concept | Key Point |
|---|---|
| API | Communication interface |
| REST | Resource-oriented architectural style |
| GraphQL | Client-driven data querying |
| RPC | Remote procedure invocation |
| HTTP | Request-response protocol |
| GET | Retrieve data |
| POST | Create or execute an operation |
| PUT | Replace resource representation |
| PATCH | Partially modify resource |
| DELETE | Delete resource |
| 200 | Successful request |
| 201 | Resource created |
| 204 | Successful response without body |
| 400 | Bad request |
| 401 | Authentication required or invalid |
| 403 | Access forbidden |
| 404 | Resource not found |
| 409 | Conflict |
| 422 | Validation or semantic error |
| 429 | Too many requests |
| 500 | Internal server error |
| Pagination | Divide large datasets |
| Rate Limiting | Restrict request volume |
| JWT | Compact claims token |
| OAuth 2.0 | Authorization framework |
| OIDC | Authentication and identity layer |
| PKCE | Authorization code protection |
| RBAC | Role-based authorization |
| ABAC | Attribute-based authorization |
| CORS | Browser cross-origin access control |
| CSRF | Forged authenticated browser request |
| XSS | Malicious script execution |
| BOLA | Missing object-level authorization |
| Webhook | Event notification through HTTP |
| OpenAPI | HTTP API description specification |
| Swagger | OpenAPI tooling ecosystem |
| API Gateway | Central request entry point |
| Idempotency Key | Helps prevent duplicate operations |

---

# PART 22: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

Use these resources for deeper study and implementation.

### HTTP and REST

- [RFC 9110 – HTTP Semantics](https://www.rfc-editor.org/info/rfc9110/?utm_source=chatgpt.com)
- [OWASP REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html?utm_source=chatgpt.com)

### API Security

- [OWASP API Security Top 10](https://owasp.org/projects/api-security-project?utm_source=chatgpt.com)
- [OWASP API Security Documentation](https://owasp.github.io/www-project-api-security/?utm_source=chatgpt.com)
- [Express Production Security Best Practices](https://expressjs.com/en/advanced/best-practice-security/?utm_source=chatgpt.com)

### Authentication

- [OAuth 2.0 Security Best Current Practice – RFC 9700](https://www.rfc-editor.org/rfc/rfc9700.html?utm_source=chatgpt.com)
- [OpenID Connect Core Specification](https://openid.net/specs/openid-connect-core-1_0.html?utm_source=chatgpt.com)

### API Documentation

- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.1.html?utm_source=chatgpt.com)
- [OpenAPI Initiative](https://www.openapis.org/?utm_source=chatgpt.com)

---

# PHASE 51 – COMPLETION CHECKLIST

Before considering this phase complete, make sure you can explain and implement the following.

### API Fundamentals

- [ ] Explain API architecture.
- [ ] Understand REST, GraphQL and RPC.
- [ ] Understand HTTP request and response structures.
- [ ] Understand HTTP methods.
- [ ] Understand HTTP status codes.
- [ ] Explain safe and idempotent methods.

### REST API Development

- [ ] Design resource-oriented URLs.
- [ ] Implement API versioning.
- [ ] Implement offset pagination.
- [ ] Implement cursor pagination.
- [ ] Implement filtering and sorting.
- [ ] Implement search APIs.
- [ ] Implement idempotency keys.

### Authentication and Authorization

- [ ] Understand session authentication.
- [ ] Understand JWT.
- [ ] Implement access tokens.
- [ ] Implement refresh token rotation.
- [ ] Understand API keys.
- [ ] Understand OAuth 2.0.
- [ ] Understand PKCE.
- [ ] Understand OpenID Connect.
- [ ] Implement RBAC.
- [ ] Implement object-level authorization.

### API Security

- [ ] Understand OWASP API Security Top 10.
- [ ] Implement input validation.
- [ ] Prevent SQL injection.
- [ ] Understand CORS.
- [ ] Understand CSRF.
- [ ] Understand XSS.
- [ ] Understand SSRF.
- [ ] Implement rate limiting.
- [ ] Secure webhook endpoints.

### Advanced API Development

- [ ] Understand GraphQL schemas.
- [ ] Implement GraphQL queries.
- [ ] Implement mutations.
- [ ] Implement resolvers.
- [ ] Understand the N+1 problem.
- [ ] Understand DataLoader.
- [ ] Implement webhooks.
- [ ] Understand OpenAPI.
- [ ] Generate API documentation.
- [ ] Write API tests.
- [ ] Design a production-ready API.

---

## What Comes Next?

**Phase 52 – Advanced Database Engineering, Database Performance Optimization and Distributed Data Management**

The next phase will cover:

1. Advanced Database Architecture.
2. Database Internals.
3. SQL Query Execution Plans.
4. Database Indexing Strategies.
5. B-Tree and Hash Indexes.
6. Composite and Covering Indexes.
7. Query Optimization.
8. Database Locking.
9. Isolation Levels.
10. Deadlocks.
11. Advanced Transactions.
12. Database Replication.
13. Read Replicas.
14. Database Sharding.
15. Partitioning.
16. Connection Pooling.
17. Database Caching.
18. Redis Advanced Concepts.
19. Database Backup and Recovery.
20. High Availability and Disaster Recovery.
21. Distributed Transactions.
22. Database Consistency Models.
23. CAP Theorem.
24. Database Performance Monitoring.

**Phase 51 is complete. When you're ready, say "OK, Phase 52" and we'll continue with detailed notes.**