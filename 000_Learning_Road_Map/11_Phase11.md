# **Phase 11 – Route Handlers and API Development**



Phase 11

Intermediate

Next.js 16.x



## **1. Learning objectives**

By the end of this phase, you will understand how to:

1. Create REST APIs using Next.js App Router.
2. Handle HTTP methods such as GET, POST, PUT, PATCH and DELETE.
3. Read query parameters, dynamic route parameters and request bodies.
4. Return JSON responses with appropriate HTTP status codes.
5. Connect Route Handlers to MongoDB and MySQL.
6. Validate incoming API data.
7. Handle errors securely.
8. Implement authentication and authorization in API endpoints.
9. Test APIs using Postman, Thunder Client and `curl`.
10. Understand the difference between Route Handlers, Server Actions and Express APIs.

## **2. What are Route Handlers?**

A **Route Handler** allows you to create custom HTTP request handlers inside the Next.js App Router.

In simple terms:

- A React page displays a user interface.
- A Route Handler processes an HTTP request and returns an HTTP response.
- A Route Handler can communicate with databases, external APIs and other backend services.

For example, imagine you are building an e-commerce application.

Your frontend needs these operations:

| Operation                  | HTTP request | Endpoint            |
| -------------------------- | ------------ | ------------------- |
| Get all products           | GET          | `/api/products`     |
| Get one product            | GET          | `/api/products/101` |
| Create a product           | POST         | `/api/products`     |
| Update a product           | PUT          | `/api/products/101` |
| Partially update a product | PATCH        | `/api/products/101` |
| Delete a product           | DELETE       | `/api/products/101` |

You can implement all these endpoints using Next.js Route Handlers.

### **How Route Handlers work**

**Frontend / API Client**

Browser, React component, Postman or mobile app

HTTP request: GET /api/products

**Next.js Route Handler**

app/api/products/route.ts

Database query

**Database**

MongoDB / MySQL / PostgreSQL

JSON response

**Frontend receives the response**

The Route Handler runs on the server. Database credentials and server-side secrets should remain there rather than being exposed to the browser.

Official documentation: [Next.js – Route Handlers](https://nextjs.org/docs/app/getting-started/route-handlers?utm_source=chatgpt.com).

## **3. The&#x20;**`route.ts`**&#x20;file convention**

Next.js uses a special file named `route.ts` to define Route Handlers.

Example project structure:

```
my-next-app/
│
├── src/
│   └── app/
│       ├── page.tsx
│       ├── layout.tsx
│       │
│       └── api/
│           ├── route.ts
│           │
│           └── products/
│               ├── route.ts
│               │
│               └── [id]/
│                   └── route.ts
│
├── package.json
└── next.config.ts
```

### **URL mapping**

| File location                        | API endpoint            |
| ------------------------------------ | ----------------------- |
| `app/api/route.ts`                   | `/api`                  |
| `app/api/products/route.ts`          | `/api/products`         |
| `app/api/users/route.ts`             | `/api/users`            |
| `app/api/products/[id]/route.ts`     | `/api/products/:id`     |
| `app/api/users/[id]/orders/route.ts` | `/api/users/:id/orders` |

**Important rules:**

- The filename must be `route.ts` or `route.js`.
- Export named HTTP method functions such as `GET` and `POST`.
- Do not use a default export for an HTTP method.
- A `route.ts` and `page.tsx` cannot exist at the same route segment because both would claim the same URL.
- Route Handlers do not automatically render React components or participate in layouts.

For example:

```
app/
└── products/
    ├── page.tsx
    └── route.ts
```

This is not allowed because both files claim the `/products` route.

But this is valid:

```
app/
└── products/
    ├── page.tsx
    └── [id]/
        └── route.ts
```

Here, the page owns `/products`, while the Route Handler owns `/products/:id`.

## **4. HTTP methods supported by Route Handlers**

Next.js supports these seven HTTP methods:

| Method  | Purpose                                                 | Example                   |
| ------- | ------------------------------------------------------- | ------------------------- |
| GET     | Retrieve data                                           | Get all products          |
| POST    | Create data                                             | Create a product          |
| PUT     | Replace/update a resource                               | Replace product details   |
| PATCH   | Partially update data                                   | Update only product price |
| DELETE  | Delete data                                             | Delete a product          |
| HEAD    | Retrieve response headers without the response body     | Check resource headers    |
| OPTIONS | Describe communication options supported by an endpoint | CORS preflight            |

If a client sends an unsupported HTTP method, Next.js returns `405 Method Not Allowed`.

### **GET – Retrieve data**

Create:

`src/app/api/products/route.ts`

```
import { NextResponse } from "next/server";

const products = [
  { id: 1, name: "Laptop", price: 75000 },
  { id: 2, name: "Mobile", price: 25000 },
];

export async function GET() {
  return NextResponse.json({
    success: true,
    data: products,
  });
}
```

Visit:

```
http://localhost:3000/api/products
```

Response:

```
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Laptop",
      "price": 75000
    },
    {
      "id": 2,
      "name": "Mobile",
      "price": 25000
    }
  ]
}
```

### **POST – Create data**

Add a POST handler in the same file:

```
import { NextRequest, NextResponse } from "next/server";

export async function POST(request: NextRequest) {
  const body = await request.json();

  const { name, price } = body;

  if (!name || typeof price !== "number" || price <= 0) {
    return NextResponse.json(
      {
        success: false,
        message: "Valid name and price are required",
      },
      { status: 400 }
    );
  }

  const newProduct = {
    id: Date.now(),
    name,
    price,
  };

  return NextResponse.json(
    {
      success: true,
      message: "Product created successfully",
      data: newProduct,
    },
    { status: 201 }
  );
}
```

Example request:

```
POST /api/products
Content-Type: application/json
```

Request body:

```
{
  "name": "Keyboard",
  "price": 1500
}
```

Response:

```
{
  "success": true,
  "message": "Product created successfully",
  "data": {
    "id": 1764000000000,
    "name": "Keyboard",
    "price": 1500
  }
}
```

This example stores data in memory only. It is for understanding the HTTP flow, not persistent production storage.

### **PUT – Replace a resource**

PUT is generally used when the client intends to replace the resource's representation.

```
export async function PUT(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
  const body = await request.json();

  const { name, price } = body;

  if (!name || typeof price !== "number" || price <= 0) {
    return Response.json(
      { success: false, message: "Invalid product data" },
      { status: 400 }
    );
  }

  // Update the product with this ID in your database.

  return Response.json({
    success: true,
    message: `Product ${id} updated`,
  });
}
```

### **PATCH – Partially update a resource**

PATCH is used when only selected fields need to change.

```
export async function PATCH(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
  const body = await request.json();

  // Update only the supplied fields in your database.

  return Response.json({
    success: true,
    message: `Product ${id} partially updated`,
    changes: body,
  });
}
```

For example, if you only want to update the price:

```
{
  "price": 65000
}
```

You do not need to send the product's name, description and every other field.

### **DELETE – Delete a resource**

```
export async function DELETE(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;

  // Delete product from database using id.

  return Response.json({
    success: true,
    message: `Product ${id} deleted`,
  });
}
```

### **HEAD and OPTIONS**

```
export async function HEAD() {
  return new Response(null, {
    status: 200,
    headers: {
      "X-API-Version": "1.0",
    },
  });
}

export async function OPTIONS() {
  return new Response(null, {
    status: 204,
    headers: {
      Allow: "GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS",
    },
  });
}
```

These methods are useful for HTTP compatibility, metadata checks and preflight handling. OPTIONS is particularly relevant when configuring CORS.

**Remember:** You can define multiple HTTP methods in one `route.ts` file.

## **5. Understanding&#x20;**`Request`**,&#x20;**`NextRequest`**,&#x20;**`Response`**&#x20;and&#x20;**`NextResponse`

These objects are used to read incoming requests and generate outgoing responses.

### **Native Web APIs**

Next.js Route Handlers support the standard Web APIs:

- `Request`
- `Response`
- `Headers`
- `URL`
- `FormData`

Example:

```
export async function GET(request: Request) {
  const url = new URL(request.url);

  const search = url.searchParams.get("search");

  return Response.json({
    search,
  });
}
```

### **Next.js extended APIs**

Next.js also provides:

- `NextRequest`
- `NextResponse`

Import them from:

```
import { NextRequest, NextResponse } from "next/server";
```

`NextRequest` extends the standard request functionality with convenient Next.js-specific properties such as `nextUrl` and cookie helpers.

`NextResponse` extends the standard response functionality with helpers for JSON, redirects, rewrites and cookies.

| Feature                 | Request / Response                | NextRequest / NextResponse |
| ----------------------- | --------------------------------- | -------------------------- |
| Standard HTTP handling  | Yes                               | Yes                        |
| Read request URL        | Yes                               | Yes                        |
| Access query parameters | Through `URL`                     | Through `nextUrl`          |
| Cookie helpers          | Standard headers/cookies handling | Convenient cookie helpers  |
| JSON response           | `Response.json()`                 | `NextResponse.json()`      |
| Redirect helpers        | Standard response construction    | Next.js redirect helper    |

**Recommendation:** Use native `Request` and `Response` when standard HTTP behavior is sufficient. Use `NextRequest` and `NextResponse` when their additional helpers are useful.

## **6. Reading query parameters**

Query parameters are values supplied after `?` in a URL.

Example:

```
/api/products?category=electronics&sort=price&page=2
```

Here:

- `category` = electronics
- `sort` = price
- `page` = 2

### **Using&#x20;**`NextRequest`

```
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;

  const category = searchParams.get("category");
  const sort = searchParams.get("sort");
  const page = searchParams.get("page");

  return NextResponse.json({
    category,
    sort,
    page,
  });
}
```

### **Using native&#x20;**`Request`

```
export async function GET(request: Request) {
  const url = new URL(request.url);

  const category = url.searchParams.get("category");

  return Response.json({
    category,
  });
}
```

### **Real-world example: Search products**

```
import { NextRequest, NextResponse } from "next/server";

const products = [
  { id: 1, name: "Dell Laptop", category: "electronics" },
  { id: 2, name: "Samsung Mobile", category: "electronics" },
  { id: 3, name: "Office Chair", category: "furniture" },
];

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;

  const category = searchParams.get("category");
  const search = searchParams.get("search");

  let result = products;

  if (category) {
    result = result.filter(
      (product) => product.category === category
    );
  }

  if (search) {
    result = result.filter((product) =>
      product.name.toLowerCase().includes(search.toLowerCase())
    );
  }

  return NextResponse.json({
    success: true,
    data: result,
  });
}
```

Test:

```
/api/products?category=electronics
```

Or:

```
/api/products?search=dell
```

**Important:** Query parameter values are strings. Convert and validate them before using them as numbers, dates or other types.

## **7. Reading dynamic route parameters**

This is especially important in Next.js 16.

Consider this structure:

```
src/
└── app/
    └── api/
        └── products/
            └── [id]/
                └── route.ts
```

The endpoint will be:

```
/api/products/101
/api/products/102
/api/products/103
```

The `[id]` folder represents a dynamic route segment.

### **Accessing&#x20;**`params`

In Next.js 16, `params` is asynchronous and must be awaited.

```
import { NextRequest, NextResponse } from "next/server";

export async function GET(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;

  return NextResponse.json({
    productId: id,
  });
}
```

Request:

```
GET /api/products/101
```

Response:

```
{
  "productId": "101"
}
```

### **Dynamic route parameters vs query parameters**

| Dynamic route parameter        | Query parameter                        |
| ------------------------------ | -------------------------------------- |
| Part of the URL path           | Comes after `?`                        |
| `/api/products/101`            | `/api/products?id=101`                 |
| Access through `params`        | Access through `searchParams`          |
| Commonly identifies a resource | Commonly filters or modifies a request |

Example:

```
/api/products/101?currency=INR
```

Here:

- `101` is a dynamic route parameter.
- `INR` is a query parameter.

You can access both in the same handler.

Official reference: [Route Handler API reference](https://nextjs.org/docs/app/api-reference/file-conventions/route?utm_source=chatgpt.com).

## **8. Reading the request body**

When a client sends data to an API, that data is usually placed in the HTTP request body.

Next.js supports standard request-body methods.

| Method                  | Purpose                         |
| ----------------------- | ------------------------------- |
| `request.json()`        | Read JSON data                  |
| `request.text()`        | Read plain text                 |
| `request.formData()`    | Read form data and file uploads |
| `request.arrayBuffer()` | Read binary data                |
| `request.blob()`        | Read data as a Blob             |

### **JSON body**

```
export async function POST(request: Request) {
  const body = await request.json();

  return Response.json({
    receivedData: body,
  });
}
```

### **FormData body**

```
export async function POST(request: Request) {
  const formData = await request.formData();

  const name = formData.get("name");
  const email = formData.get("email");

  return Response.json({
    name,
    email,
  });
}
```

### **File upload example**

```
export async function POST(request: Request) {
  const formData = await request.formData();

  const file = formData.get("file");

  if (!(file instanceof File)) {
    return Response.json(
      { message: "File is required" },
      { status: 400 }
    );
  }

  return Response.json({
    filename: file.name,
    size: file.size,
    type: file.type,
  });
}
```

This example reads file metadata. It does not yet save the uploaded file to disk or cloud storage.

**Important:** Reading the body consumes the request body. Do not try to call `request.json()` and then `request.text()` on the same request body.

## **9. Returning JSON responses and HTTP status codes**

An API should return a meaningful response and an appropriate HTTP status code.

### **Using&#x20;**`NextResponse.json()`

```
import { NextResponse } from "next/server";

export async function GET() {
  return NextResponse.json(
    {
      success: true,
      message: "Products fetched successfully",
      data: [],
    },
    {
      status: 200,
    }
  );
}
```

### **Using native&#x20;**`Response.json()`

```
export async function GET() {
  return Response.json(
    {
      success: true,
      data: [],
    },
    {
      status: 200,
    }
  );
}
```

Both approaches are supported.

### **Important HTTP status codes**

| Status | Meaning               | Typical use                                |
| ------ | --------------------- | ------------------------------------------ |
| 200    | OK                    | Successful GET or update                   |
| 201    | Created               | Resource successfully created              |
| 202    | Accepted              | Request accepted for processing            |
| 204    | No Content            | Successful operation with no response body |
| 400    | Bad Request           | Invalid request data                       |
| 401    | Unauthorized          | Missing or invalid authentication          |
| 403    | Forbidden             | Authenticated user lacks permission        |
| 404    | Not Found             | Resource does not exist                    |
| 405    | Method Not Allowed    | Unsupported HTTP method                    |
| 409    | Conflict              | Duplicate or conflicting resource          |
| 422    | Unprocessable Content | Semantically invalid input                 |
| 429    | Too Many Requests     | Rate limit exceeded                        |
| 500    | Internal Server Error | Unexpected server error                    |
| 503    | Service Unavailable   | Service temporarily unavailable            |

### **Setting custom headers**

```
export async function GET() {
  return Response.json(
    {
      message: "Success",
    },
    {
      status: 200,
      headers: {
        "X-API-Version": "1.0",
        "Cache-Control": "no-store",
      },
    }
  );
}
```

The `Cache-Control` header is an HTTP caching instruction. It is separate from Next.js's own data and route caching configuration.

## **10. Route Handlers vs Express.js APIs vs Server Actions**

You already know the general concept of backend APIs from Node.js and Express. Let's compare the approaches.

### **Route Handlers vs Express**

| Feature                          | Next.js Route Handlers             | Express.js         |
| -------------------------------- | ---------------------------------- | ------------------ |
| Framework                        | Next.js                            | Express            |
| Routing                          | File-based                         | Programmatic       |
| HTTP methods                     | Named exports                      | Router methods     |
| Request handling                 | `Request`, `NextRequest`           | Express `req`      |
| Response handling                | `Response`, `NextResponse`         | Express `res`      |
| Middleware                       | Next.js Proxy and other mechanisms | Express middleware |
| Database access                  | Supported                          | Supported          |
| Separate backend server required | Not necessarily                    | Usually yes        |
| Built-in React integration       | Yes                                | No                 |

Express example:

```
app.get("/api/products", (req, res) => {
  res.json({
    success: true,
    data: [],
  });
});
```

Next.js example:

```
export async function GET() {
  return Response.json({
    success: true,
    data: [],
  });
}
```

The goal is similar, but the routing and request-handling architecture are different.

### **Route Handlers vs Server Actions**

This distinction is important.

| Feature                | Route Handlers                             | Server Actions                                   |
| ---------------------- | ------------------------------------------ | ------------------------------------------------ |
| Main purpose           | Create HTTP endpoints                      | Perform server-side mutations/actions            |
| Explicit HTTP endpoint | Yes                                        | No public endpoint needs to be designed manually |
| Called using           | HTTP requests                              | Form actions or function calls                   |
| External API consumers | Suitable                                   | Not normally intended as a general API           |
| React form integration | Manual or fetch-based                      | Built-in form action integration                 |
| HTTP methods           | GET, POST, etc.                            | Framework-managed POST mechanism                 |
| Common use             | REST APIs, webhooks, external integrations | Form submissions, mutations                      |

**When should you use Route Handlers?**

- You need a REST API.
- A mobile application needs to communicate with your backend.
- A third-party application needs to consume your API.
- You need webhook endpoints.
- You need a public or private HTTP endpoint.

**When should you use Server Actions?**

- A React form submits data to the server.
- A user creates, updates or deletes data through your Next.js UI.
- You want a direct server-side mutation workflow integrated with React.

We will study Server Actions in Phase 12.

## **11. Connecting Route Handlers to MongoDB**

A real application should use a database instead of an in-memory array.

Let's create a basic MongoDB connection and use it in a Route Handler.

### **Suggested folder structure**

```
src/
├── app/
│   └── api/
│       └── products/
│           ├── route.ts
│           └── [id]/
│               └── route.ts
│
└── lib/
    └── mongodb.ts
```

Install MongoDB:

```
npm install mongodb
```

### **Step 1: Environment variable**

`.env.local`

```
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/mydb
```

Never prefix your database connection string with `NEXT_PUBLIC_`.

### **Step 2: Create database connection**

`src/lib/mongodb.ts`

```
import "server-only";

import { MongoClient } from "mongodb";

const uri = process.env.MONGODB_URI;

if (!uri) {
  throw new Error("MONGODB_URI is not defined");
}

const client = new MongoClient(uri);

const clientPromise = client.connect();

export default clientPromise;
```

This is a basic connection example. In a production application, you should also consider reusing the client across development hot reloads and configuring connection options appropriately.

### **Step 3: Fetch all products**

`src/app/api/products/route.ts`

```
import { NextResponse } from "next/server";
import clientPromise from "@/lib/mongodb";

export async function GET() {
  try {
    const client = await clientPromise;

    const db = client.db("mydb");

    const products = await db
      .collection("products")
      .find({})
      .toArray();

    return NextResponse.json({
      success: true,
      data: products,
    });
  } catch (error) {
    console.error("GET products error:", error);

    return NextResponse.json(
      {
        success: false,
        message: "Unable to fetch products",
      },
      { status: 500 }
    );
  }
}
```

### **Step 4: Insert a product**

Add this method to the same `route.ts` file:

```
import { NextRequest, NextResponse } from "next/server";
import clientPromise from "@/lib/mongodb";

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();

    const { name, price, category } = body;

    if (
      typeof name !== "string" ||
      !name.trim() ||
      typeof price !== "number" ||
      !Number.isFinite(price) ||
      price <= 0 ||
      typeof category !== "string" ||
      !category.trim()
    ) {
      return NextResponse.json(
        {
          success: false,
          message: "Invalid product data",
        },
        { status: 400 }
      );
    }

    const client = await clientPromise;

    const db = client.db("mydb");

    const result = await db.collection("products").insertOne({
      name: name.trim(),
      price,
      category: category.trim(),
      createdAt: new Date(),
    });

    return NextResponse.json(
      {
        success: true,
        message: "Product created successfully",
        data: {
          id: result.insertedId,
          name: name.trim(),
          price,
          category: category.trim(),
        },
      },
      { status: 201 }
    );
  } catch (error) {
    console.error("POST product error:", error);

    return NextResponse.json(
      {
        success: false,
        message: "Unable to create product",
      },
      { status: 500 }
    );
  }
}
```

### **Step 5: Get a product by MongoDB ObjectId**

`src/app/api/products/[id]/route.ts`

```
import { NextResponse } from "next/server";
import { ObjectId } from "mongodb";
import clientPromise from "@/lib/mongodb";

export async function GET(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  try {
    const { id } = await params;

    if (!ObjectId.isValid(id)) {
      return NextResponse.json(
        {
          success: false,
          message: "Invalid product ID",
        },
        { status: 400 }
      );
    }

    const client = await clientPromise;

    const db = client.db("mydb");

    const product = await db.collection("products").findOne({
      _id: new ObjectId(id),
    });

    if (!product) {
      return NextResponse.json(
        {
          success: false,
          message: "Product not found",
        },
        { status: 404 }
      );
    }

    return NextResponse.json({
      success: true,
      data: product,
    });
  } catch (error) {
    console.error("GET product error:", error);

    return NextResponse.json(
      {
        success: false,
        message: "Unable to fetch product",
      },
      { status: 500 }
    );
  }
}
```

**Important:** A MongoDB ObjectId is normally a 24-character hexadecimal value. A value such as `"101"` is not a valid ObjectId.

This example demonstrates the important flow:

1. Receive the request.
2. Read the dynamic ID.
3. Validate the ID.
4. Connect to MongoDB.
5. Query the collection.
6. Handle the not-found case.
7. Return a JSON response.

## **12. Connecting Route Handlers to MySQL**

You can also connect Route Handlers directly to MySQL using `mysql2`.

Install:

```
npm install mysql2
```

### **Step 1: Configure environment variables**

`.env.local`

```
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=mydb
```

### **Step 2: Create a connection pool**

`src/lib/mysql.ts`

```
import "server-only";

import mysql from "mysql2/promise";

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  connectionLimit: 10,
});

export default pool;
```

A connection pool reuses database connections rather than creating a completely new connection for every query.

### **Step 3: Fetch products**

`src/app/api/products/route.ts`

```
import { NextResponse } from "next/server";
import pool from "@/lib/mysql";

export async function GET() {
  try {
    const [rows] = await pool.execute(
      "SELECT id, name, price FROM products"
    );

    return NextResponse.json({
      success: true,
      data: rows,
    });
  } catch (error) {
    console.error("MySQL error:", error);

    return NextResponse.json(
      {
        success: false,
        message: "Database operation failed",
      },
      { status: 500 }
    );
  }
}
```

### **Step 4: Insert a product securely**

```
import { NextRequest, NextResponse } from "next/server";
import pool from "@/lib/mysql";

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();

    const { name, price } = body;

    if (
      typeof name !== "string" ||
      !name.trim() ||
      typeof price !== "number" ||
      !Number.isFinite(price) ||
      price <= 0
    ) {
      return NextResponse.json(
        { message: "Invalid product data" },
        { status: 400 }
      );
    }

    const [result] = await pool.execute(
      "INSERT INTO products (name, price) VALUES (?, ?)",
      [name.trim(), price]
    );

    return NextResponse.json(
      {
        success: true,
        message: "Product created successfully",
        data: result,
      },
      { status: 201 }
    );
  } catch (error) {
    console.error("MySQL insert error:", error);

    return NextResponse.json(
      { message: "Unable to create product" },
      { status: 500 }
    );
  }
}
```

### **Why use&#x20;**`?`**&#x20;placeholders?**

Consider this unsafe query:

```
const sql = `SELECT * FROM users WHERE email = '${email}'`;
```

If untrusted input is inserted directly into SQL strings, it can create a SQL injection vulnerability.

Use parameterized queries:

```
const [rows] = await pool.execute(
  "SELECT * FROM users WHERE email = ?",
  [email]
);
```

The database driver handles the parameter values separately from the SQL statement.



Best practice

&#x20;Always use parameterized SQL queries for user-provided values. Do not concatenate untrusted input into SQL statements.



## **13. Input validation using Zod**

Checking values manually works for small examples. But larger applications benefit from a dedicated validation library.

One popular choice is **Zod**.

Install:

```
npm install zod
```

### **Define a schema**

`src/lib/validations/product.ts`

```
import { z } from "zod";

export const productSchema = z.object({
  name: z.string().trim().min(1, "Name is required"),
  price: z.number().positive("Price must be positive"),
  category: z.string().trim().min(1, "Category is required"),
});
```

### **Validate incoming data**

```
import { NextRequest, NextResponse } from "next/server";
import { productSchema } from "@/lib/validations/product";

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();

    const result = productSchema.safeParse(body);

    if (!result.success) {
      return NextResponse.json(
        {
          success: false,
          errors: result.error.issues,
        },
        { status: 400 }
      );
    }

    const productData = result.data;

    // Save validated data to database.

    return NextResponse.json(
      {
        success: true,
        data: productData,
      },
      { status: 201 }
    );
  } catch {
    return NextResponse.json(
      {
        success: false,
        message: "Invalid JSON request body",
      },
      { status: 400 }
    );
  }
}
```

### **Why is Zod useful?**

- Validates incoming data.
- Checks data types.
- Supports required and optional fields.
- Supports minimum and maximum values.
- Provides structured validation errors.
- Can infer TypeScript types from schemas.

Example:

```
import { z } from "zod";

const userSchema = z.object({
  name: z.string().min(2),
  email: z.email(),
  age: z.number().int().min(18),
});

type User = z.infer<typeof userSchema>;
```

Now `User` is inferred from the schema instead of manually defining the same type twice.

## **14. Error handling in Route Handlers**

A good API should not expose internal server details to clients.

Use `try...catch` for operations that can fail, such as:

- Database queries.
- External API requests.
- Request body parsing.
- File operations.
- Third-party service calls.

Example:

```
export async function GET() {
  try {
    const data = await getProductsFromDatabase();

    return Response.json({
      success: true,
      data,
    });
  } catch (error) {
    console.error("API error:", error);

    return Response.json(
      {
        success: false,
        message: "Internal server error",
      },
      { status: 500 }
    );
  }
}
```

### **Avoid exposing internal errors**

Bad:

```
return Response.json({
  error: String(error),
});
```

This can expose database details, internal paths or other sensitive implementation information.

Better:

```
console.error(error);

return Response.json(
  {
    success: false,
    message: "Something went wrong",
  },
  { status: 500 }
);
```

Log detailed errors on the server, but return a safe message to the client.

### **Expected errors vs unexpected errors**

| Expected error           | Unexpected error            |
| ------------------------ | --------------------------- |
| Invalid input            | Database connection failure |
| Product not found        | Unexpected code exception   |
| Email already registered | Third-party service outage  |
| User lacks permission    | Unhandled internal failure  |

Expected errors should receive meaningful status codes such as 400, 404 or 409. Unexpected errors generally receive 500.

## **15. Authentication and authorization**

This is one of the most important parts of API development.

### **Authentication**

Authentication answers:

**Who is making this request?**

Examples:

- Is the user logged in?
- Is the session valid?
- Is the authentication token valid?

### **Authorization**

Authorization answers:

**Is this user allowed to perform this operation?**

For example:

- Can this user view the product?
- Can this user delete the product?
- Is this user an administrator?

### **Example: Protecting an API endpoint**

The following is a simplified illustration. Assume you have an authentication function that securely verifies the session.

```
import { NextResponse } from "next/server";
import { getCurrentUser } from "@/lib/auth";

export async function DELETE(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const user = await getCurrentUser();

  if (!user) {
    return NextResponse.json(
      { message: "Authentication required" },
      { status: 401 }
    );
  }

  if (user.role !== "admin") {
    return NextResponse.json(
      { message: "You are not allowed to delete products" },
      { status: 403 }
    );
  }

  const { id } = await params;

  // Delete product from database.

  return NextResponse.json({
    success: true,
    message: `Product ${id} deleted`,
  });
}
```

### **Important security rules**

1. Verify authentication on every protected endpoint.
2. Check authorization before performing sensitive operations.
3. Never trust a `userId` supplied by the client as proof of identity.
4. Never trust a client-provided role such as `"admin"`.
5. Validate all incoming data.
6. Keep database credentials and secret keys server-side.
7. Avoid exposing internal exception messages.
8. Use secure cookie settings when using cookie-based sessions.
9. Consider rate limiting sensitive endpoints.
10. Verify ownership when users access their own resources.

**Important:** Hiding an admin button in the frontend is not authorization. The backend must enforce permissions.

## **16. CORS – Cross-Origin Resource Sharing**

CORS is a browser security mechanism that controls whether a web page from one origin can access resources from another origin.

An origin consists of:

- Protocol
- Hostname
- Port

For example:

```
http://localhost:3000
http://localhost:4000
```

These are different origins because their ports differ.

If your frontend is hosted on one origin and your API on another, the browser may require appropriate CORS response headers.

### **Example: CORS headers**

```
const corsHeaders = {
  "Access-Control-Allow-Origin": "https://example.com",
  "Access-Control-Allow-Methods": "GET, POST, OPTIONS",
  "Access-Control-Allow-Headers": "Content-Type, Authorization",
};

export async function GET() {
  return Response.json(
    { message: "CORS-enabled API" },
    { headers: corsHeaders }
  );
}

export async function OPTIONS() {
  return new Response(null, {
    status: 204,
    headers: corsHeaders,
  });
}
```

This is a basic example. A production CORS implementation should use an explicit allowlist of trusted origins rather than accepting arbitrary origins.

### **Do you always need CORS?**

No.

If your frontend and API use the same origin, CORS configuration is generally unnecessary.

For example:

```
https://example.com
https://example.com/api/products
```

CORS is primarily relevant when a browser accesses an API hosted on a different origin.

Also remember: CORS is not authentication. It does not prevent non-browser clients such as scripts or Postman from calling your endpoint.

Official reference: [Route Handlers – CORS section](https://nextjs.org/docs/app/api-reference/file-conventions/route?utm_source=chatgpt.com).

## **17. Caching in Route Handlers**

Caching is important because APIs may be called frequently.

However, understand the current Next.js behavior carefully.

### **Default behavior**

In Next.js 15 and later, GET Route Handlers are not cached by default in the traditional Route Handler caching model.

You can explicitly opt into caching where appropriate.

For example, when using the traditional route segment configuration:

```
export const dynamic = "force-static";

export async function GET() {
  const data = {
    appName: "My Next.js Application",
  };

  return Response.json(data);
}
```

This opts the GET handler into static behavior under that caching model.

### **Next.js 16 and Cache Components**

If your project has this configuration:

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

GET Route Handlers follow the Cache Components model.

You can cache a database-fetching helper function:

```
import { cacheLife } from "next/cache";

export async function GET() {
  const products = await getProducts();

  return Response.json(products);
}

async function getProducts() {
  "use cache";

  cacheLife("hours");

  return await fetchProductsFromDatabase();
}
```

Notice that `"use cache"` is inside the helper function, not directly inside the Route Handler body.

### **Caching summary**

| Configuration                                     | Important behavior                                  |
| ------------------------------------------------- | --------------------------------------------------- |
| Traditional model                                 | GET handlers are not cached by default              |
| Traditional model with `dynamic = "force-static"` | Explicitly opts into static behavior                |
| Cache Components enabled                          | GET handlers can be prerendered when eligible       |
| Cache Components with `"use cache"`               | Cache an extracted function or other eligible scope |
| POST, PUT, PATCH, DELETE                          | Not cached as Route Handler responses               |

Do not blindly copy caching examples from older tutorials. First check whether your project uses Cache Components.

Official documentation:

- [Route Handlers and caching](https://nextjs.org/docs/app/getting-started/route-handlers?utm_source=chatgpt.com)
- [use cache directive](https://nextjs.org/docs/app/api-reference/directives/use-cache?utm_source=chatgpt.com)
- [Route Segment Config](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config?utm_source=chatgpt.com)

## **18. Calling a Route Handler from a Client Component**

You can call your API using the browser's `fetch()` function.

Example:

`src/app/products/page.tsx`

```
"use client";

import { useEffect, useState } from "react";

interface Product {
  _id: string;
  name: string;
  price: number;
}

export default function ProductsPage() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchProducts() {
      try {
        const response = await fetch("/api/products");

        if (!response.ok) {
          throw new Error("Failed to fetch products");
        }

        const result = await response.json();

        setProducts(result.data);
      } catch (error) {
        setError("Unable to load products");
      } finally {
        setLoading(false);
      }
    }

    fetchProducts();
  }, []);

  if (loading) return <p>Loading...</p>;

  if (error) return <p>{error}</p>;

  return (
    <div>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product._id}>
          <h3>{product.name}</h3>
          <p>₹{product.price}</p>
        </div>
      ))}
    </div>
  );
}
```

### **Important: Do Server Components need to call Route Handlers?**

Usually, no.

Suppose you have a Server Component that needs products from MongoDB.

Avoid this unnecessary internal HTTP request:

```
const response = await fetch(
  "http://localhost:3000/api/products"
);
```

Instead, call your database function directly:

```
const products = await getProductsFromDatabase();
```

Why?

- Avoids an unnecessary HTTP request.
- Reduces overhead.
- Makes server-side data access simpler.
- Avoids problems with constructing deployment-specific URLs.

**Recommended architecture:**

**Server Component**

Calls database/service function directly

**Shared server-side data-access function**

**Database**

MongoDB / MySQL

**Client Component or external application**

Calls the Route Handler using HTTP

**Route Handler**

**Shared server-side data-access function**

The same database/service function can be reused by both the Server Component and Route Handler.

## **19. Testing APIs using Postman and Thunder Client**

You should test your APIs independently before integrating them with the frontend.

Two popular tools are:

- Postman – [Official website](https://www.postman.com/?utm_source=chatgpt.com)
- Thunder Client – [Official website](https://www.thunderclient.com/?utm_source=chatgpt.com)

Let's test a product API.

### **GET request**

GET

**Fetch all products**

[http://localhost:3000/api/products](http://localhost:3000/api/products?utm_source=chatgpt.com)

**Expected response**

```
{
  "success": true,
  "data": []
}
```

### **POST request**

POST

**Create a product**

[http://localhost:3000/api/products](http://localhost:3000/api/products?utm_source=chatgpt.com)

**Headers**

```
Content-Type: application/json
```

**Body**

```
{
  "name": "Dell Laptop",
  "price": 75000,
  "category": "Electronics"
}
```

**Expected status:** `201 Created`

### **GET by ID**

```
GET http://localhost:3000/api/products/65a123456789abcdef123456
```

### **PATCH request**

```
PATCH http://localhost:3000/api/products/65a123456789abcdef123456
```

Body:

```
{
  "price": 70000
}
```

### **DELETE request**

```
DELETE http://localhost:3000/api/products/65a123456789abcdef123456
```

### **Testing using curl**

You can also test APIs directly from your terminal.

GET:

```
curl http://localhost:3000/api/products
```

POST:

```
curl -X POST http://localhost:3000/api/products \
-H "Content-Type: application/json" \
-d '{"name":"Laptop","price":75000,"category":"Electronics"}'
```

## **20. REST API design best practices**

When creating APIs, follow consistent naming and response conventions.

### **Use nouns for resource endpoints**

Good:

```
GET    /api/products
GET    /api/products/101
POST   /api/products
PATCH  /api/products/101
DELETE /api/products/101
```

Avoid unnecessarily using action names in URLs:

```
/api/getProducts
/api/createProduct
/api/deleteProduct
```

The HTTP method should communicate the operation.

### **Use consistent response structures**

For successful responses:

```
{
  "success": true,
  "message": "Products fetched successfully",
  "data": []
}
```

For errors:

```
{
  "success": false,
  "message": "Product not found"
}
```

### **Implement pagination**

For large datasets, avoid returning thousands of records in a single response.

Example:

```
/api/products?page=1&limit=10
```

A typical calculation is:

\\[ \text{offset}=(\text{page}-1)\times\text{limit} \\]

Example using MongoDB:

```
const page = Math.max(
  1,
  Number(searchParams.get("page")) || 1
);

const limit = Math.min(
  100,
  Math.max(1, Number(searchParams.get("limit")) || 10)
);

const skip = (page - 1) * limit;

const products = await db
  .collection("products")
  .find({})
  .skip(skip)
  .limit(limit)
  .toArray();
```

In production, validate the parsed numbers carefully and consider returning pagination metadata:

```
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "totalPages": 10
  }
}
```

### **Additional best practices**

1. Use appropriate HTTP methods.
2. Return meaningful HTTP status codes.
3. Validate every incoming request.
4. Use parameterized database queries.
5. Protect sensitive endpoints.
6. Implement pagination for large datasets.
7. Add rate limiting where appropriate.
8. Keep secrets in environment variables.
9. Log unexpected server errors.
10. Never expose database credentials or internal stack traces.
11. Avoid returning unnecessary sensitive fields.
12. Use a consistent API response format.

# **21. Common mistakes in Route Handlers**

| Mistake                                      | Correct approach                               |
| -------------------------------------------- | ---------------------------------------------- |
| Using `route.tsx`                            | Use `route.ts`                                 |
| Using default exports for GET/POST           | Use named exports                              |
| Accessing `params.id` synchronously          | Use `const { id } = await params`              |
| Forgetting to await `request.json()`         | Use `await request.json()`                     |
| Returning raw database errors                | Return safe error messages                     |
| Trusting client-provided roles               | Verify roles server-side                       |
| Concatenating user input into SQL            | Use parameterized queries                      |
| Using `GET` to create records                | Use POST                                       |
| Returning 200 for every error                | Use appropriate status codes                   |
| Exposing database URLs                       | Keep them in server-side environment variables |
| Calling your own API from a Server Component | Call the shared server-side function directly  |
| Assuming every GET handler is cached         | Understand the active caching model            |

# **22. Interview questions and answers**

## **Basic level**

**Q1. What are Route Handlers in Next.js?**

Route Handlers allow us to create custom HTTP request handlers inside the App Router using the `route.ts` or `route.js` file convention.

**Q2. Where do we create Route Handlers?**

Inside the `app` directory.

Example:

```
app/api/users/route.ts
```

**Q3. Which HTTP methods are supported?**

GET, POST, PUT, PATCH, DELETE, HEAD and OPTIONS.

**Q4. What is the difference between&#x20;**`page.tsx`**&#x20;and&#x20;**`route.ts`**?**

`page.tsx` renders a React UI page, whereas `route.ts` handles HTTP requests and returns HTTP responses.

**Q5. Can we use multiple HTTP methods in one Route Handler file?**

Yes. We can export multiple named functions from one `route.ts` file.

**Q6. What is the purpose of&#x20;**`NextResponse.json()`**?**

It creates a JSON HTTP response and allows us to specify status codes and response options.

## **Intermediate level**

**Q7. How do you access dynamic parameters in Next.js 16 Route Handlers?**

By awaiting the `params` promise:

```
export async function GET(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;
}
```

**Q8. What is the difference between PUT and PATCH?**

PUT generally replaces a resource representation, while PATCH modifies selected fields.

**Q9. How do you read query parameters?**

Using:

```
request.nextUrl.searchParams.get("search");
```

Or using the standard `URL` API.

**Q10. How do you read JSON request data?**

Using:

```
const body = await request.json();
```

**Q11. What is the difference between 401 and 403?**

- 401: The request lacks valid authentication.
- 403: The request is understood, but the user is not permitted to perform the operation.

**Q12. Are Route Handlers cached by default?**

GET Route Handlers are not cached by default under the traditional model introduced in Next.js 15. Next.js 16 also supports Cache Components, where eligible GET handlers can be prerendered and cached data can use `"use cache"`.

## **Advanced level**

**Q13. What is the difference between Route Handlers and Server Actions?**

Route Handlers provide explicit HTTP endpoints, while Server Actions provide server-side functions integrated with React forms and mutations.

**Q14. Why should we use parameterized SQL queries?**

To prevent SQL injection by keeping user-provided values separate from SQL statements.

**Q15. Can we access MongoDB directly inside a Route Handler?**

Yes. Route Handlers run on the server and can use server-side database drivers.

**Q16. Why should we avoid calling our own Route Handler from a Server Component?**

It creates an unnecessary HTTP request. Calling the underlying database or service function directly is generally more efficient.

**Q17. What is CORS?**

Cross-Origin Resource Sharing is a browser security mechanism that controls cross-origin requests.

**Q18. How do you secure a Route Handler?**

By verifying authentication, checking authorization, validating input, protecting secrets, using safe database queries and applying rate limits where appropriate.

**Q19. What happens when an unsupported HTTP method is called?**

Next.js returns `405 Method Not Allowed`.

**Q20. Can Route Handlers receive webhooks?**

Yes. A third-party service can send an HTTP request, commonly POST, to a Route Handler that processes the webhook payload.

# **23. Practical assignment – Build a Product REST API**

Main assignment

Build a complete CRUD API using **Next.js 16 + TypeScript + MongoDB**.

### **Project structure**

```
src/
├── app/
│   └── api/
│       └── products/
│           ├── route.ts
│           └── [id]/
│               └── route.ts
│
├── lib/
│   ├── mongodb.ts
│   └── validations/
│       └── product.ts
│
└── types/
    └── product.ts
```

### **Product schema**

```
{
  name: string;
  description: string;
  price: number;
  category: string;
  stock: number;
  createdAt: Date;
  updatedAt: Date;
}
```

### **Required API endpoints**

| Method | Endpoint             | Task                    |
| ------ | -------------------- | ----------------------- |
| GET    | `/api/products`      | Get all products        |
| GET    | `/api/products/[id]` | Get one product         |
| POST   | `/api/products`      | Create a product        |
| PUT    | `/api/products/[id]` | Replace product details |
| PATCH  | `/api/products/[id]` | Update selected fields  |
| DELETE | `/api/products/[id]` | Delete a product        |

### **Functional requirements**

**Task 1 – GET all products**

- Fetch products from MongoDB.
- Support category filtering.
- Support search by product name.
- Implement pagination.

Example:

```
/api/products?category=electronics&page=1&limit=10
```

**Task 2 – GET product by ID**

- Read the dynamic ID.
- Validate MongoDB ObjectId.
- Return 404 if the product does not exist.

**Task 3 – POST product**

- Read JSON request body.
- Validate data using Zod.
- Insert the product into MongoDB.
- Return 201.

**Task 4 – PUT product**

- Replace the product fields.
- Validate the entire replacement data.
- Return 404 if the product does not exist.

**Task 5 – PATCH product**

- Update only the supplied fields.
- Validate the supplied fields.
- Do not overwrite unspecified fields.

**Task 6 – DELETE product**

- Validate the ID.
- Delete the product.
- Return an appropriate response.

**Task 7 – Error handling**

- Handle invalid JSON.
- Handle invalid ObjectIds.
- Handle missing products.
- Handle database errors.
- Return appropriate HTTP status codes.

**Task 8 – Security**

- Protect create, update and delete endpoints.
- Allow only authenticated administrators to modify products.
- Validate every incoming request.

### **API testing checklist**

**Assignment progress**

# **0%**

0 of 13 tasks completed

Create the MongoDB connection module

Create the Product validation schema

Implement GET all products

Implement GET product by ID

Implement POST product

Implement PUT product

Implement PATCH product

Implement DELETE product

Add pagination and filtering

Add authentication and authorization

Test all endpoints using Postman

Test invalid IDs and invalid request bodies

Verify correct HTTP status codes

Reset checklist

# **24. Phase 11 – Quick revision notes**

| Concept                        | Remember                            |
| ------------------------------ | ----------------------------------- |
| Route Handler                  | Creates HTTP endpoints              |
| File convention                | `route.ts`                          |
| Location                       | Inside `app`                        |
| GET                            | Retrieve data                       |
| POST                           | Create data                         |
| PUT                            | Replace resource                    |
| PATCH                          | Partially update resource           |
| DELETE                         | Delete resource                     |
| HEAD                           | Headers without response body       |
| OPTIONS                        | Communication options / preflight   |
| NextRequest                    | Extended request API                |
| NextResponse                   | Extended response API               |
| `request.json()`               | Reads JSON request body             |
| `request.formData()`           | Reads form data                     |
| `request.nextUrl.searchParams` | Reads query parameters              |
| `await params`                 | Reads dynamic route parameters      |
| `Response.json()`              | Returns JSON response               |
| 200                            | Successful request                  |
| 201                            | Resource created                    |
| 400                            | Invalid request                     |
| 401                            | Authentication required             |
| 403                            | Permission denied                   |
| 404                            | Resource not found                  |
| 500                            | Internal server error               |
| Zod                            | Input validation                    |
| CORS                           | Browser cross-origin access control |
| Parameterized query            | Protects against SQL injection      |

## **25. Phase 11 completion checklist**

You should now be able to explain these concepts without referring to the notes:

- What are Route Handlers?
- How does `route.ts` work?
- What are the seven supported HTTP methods?
- What is the difference between GET, POST, PUT and PATCH?
- What are `NextRequest` and `NextResponse`?
- How do you read query parameters?
- How do you access dynamic route parameters in Next.js 16?
- How do you read JSON and FormData request bodies?
- How do you connect MongoDB and MySQL?
- Why is input validation necessary?
- What is the difference between authentication and authorization?
- What is CORS?
- What is the difference between Route Handlers and Server Actions?
- How does caching work in Route Handlers?

## **26. What's next?**

**Phase 12 – Server Actions and Data Mutations**

Next

[Practical Guide to nextjs server actions for 2026](https://images.openai.com/static-rsc-4/esKIUpTQBCy6Q99dZ_y0xbZX_PaaUqmy7KBxiKGCKUb1eM_5PZz2NeumLlN1tXkfqpiPk2TF93AQzveD5DvNnBQjIQHa88aIBMbqgw0UzboQFOtymoe32jsz4xsvFCZuQ8HImpIl40s9uTvPehH52livQVRjin2nP2tIv9QLyKI?purpose=inline)

[nextjsreactjs.com](https://nextjsreactjs.com/latest-article/nextjs-server-actions/?utm_source=chatgpt.com)

In the next phase, we will cover:

1. What are Server Actions?
2. `"use server"` directive.
3. Server Actions vs Route Handlers.
4. Form submission without manually creating API endpoints.
5. Handling form data.
6. `useActionState()` and `useFormStatus()`.
7. Data mutations – Create, Update and Delete.
8. Form validation using Zod.
9. Revalidation using `revalidatePath()` and `revalidateTag()`.
10. Authentication and security in Server Actions.
11. Real-world CRUD application.

When you're ready, say **OK, Phase 12**.