# **Next.js Complete Learning Roadmap – Phase 10**

## **Data Fetching in Next.js**

Phase 10

Intermediate–Advanced

Next.js 16.x

App Router

TypeScript

## **1. Learning objectives**

Data fetching is one of the most important skills in full-stack Next.js development.

In this phase, you will learn:

1. What data fetching is.
2. How data fetching works in Next.js.
3. Fetching data using the native `fetch()` API.
4. Fetching data inside Server Components.
5. Fetching data inside Client Components.
6. Using `async` and `await`.
7. Understanding caching in Next.js.
8. Understanding cache control and revalidation.
9. Parallel and sequential data fetching.
10. Fetching data from REST APIs.
11. Fetching data from MongoDB and MySQL.
12. Error handling during data fetching.
13. Data fetching best practices.

# **2. What is Data Fetching?**

Data fetching means retrieving data from an external or internal source so that your application can display or process it.

Data can come from:

- REST APIs.
- GraphQL APIs.
- MongoDB.
- MySQL.
- PostgreSQL.
- External services.
- Third-party platforms.
- Files and other data sources.

### **Real-world example**

Imagine you are building an e-commerce application.

Your products are stored in MongoDB.

When a user opens:

```
/products
```

Your application needs to:

1. Connect to the database.
2. Retrieve product records.
3. Process the records.
4. Render product information.
5. Display the products to the user.

This entire process involves data fetching.

[Ecommerce seo: expert guide](https://images.openai.com/static-rsc-4/vx3TcEBRni8qyKOILlSh64MjyPFurBdUHcVJ2T9szWqkdGx80JBHZC1ptxGZsOLUPtk-R9y--tk5Ur0W84Jl6QFjDbaYE861wupAu_d4KtAT49EuNNm-T9rhcJVVTO4KAiVF7gsFnbzJ_hahe3MSTvSU3TmaPELmNY_tD-PNx80?purpose=inline)

[makdigitaldesign.com](https://makdigitaldesign.com/marketing/ecommerce-seo-year-what-changed-what-works/?utm_source=chatgpt.com)

### **Basic data fetching flow**

**Next.js Application**

Requests product data

**Data Source**

REST API / MongoDB / MySQL

**Retrieved Data**

JSON / database records

**Rendered UI**

Products displayed to the user

# **3. Understanding the Different Ways to Fetch Data**

Next.js supports several approaches to data fetching.

| Method                | Typical use case                                    |
| --------------------- | --------------------------------------------------- |
| Native `fetch()`      | Fetch data from REST APIs                           |
| Database client / ORM | Fetch directly from a database on the server        |
| Server Components     | Fetch data before rendering UI                      |
| Client Components     | Fetch data after or during browser-side interaction |
| Server Actions        | Perform server-side mutations                       |
| Route Handlers        | Create API endpoints                                |

Let's understand each approach.

# **4. Fetching Data Using the Native&#x20;**`fetch()`**&#x20;API**

JavaScript provides a built-in function called `fetch()`.

It allows you to make HTTP requests to APIs.

### **Basic syntax**

```
const response = await fetch("https://api.example.com/products");

const data = await response.json();
```

### **Understanding the code**

**Step 1: Make an HTTP request**

```
const response = await fetch("https://api.example.com/products");
```

This sends a request to the API.

**Step 2: Convert the response into JSON**

```
const data = await response.json();
```

This reads the response body and parses it as JSON.

### **Example API response**

```
[
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
```

After parsing, JavaScript receives an array of objects.

### **Important:&#x20;**`fetch()`**&#x20;does not automatically throw for HTTP errors**

For example, if the API returns HTTP 404 or 500, the `fetch()` promise normally resolves with a `Response` object.

You should check:

```
if (!response.ok) {
  throw new Error("Failed to fetch products");
}
```

A complete example:

```
const response = await fetch(
  "https://api.example.com/products"
);

if (!response.ok) {
  throw new Error("Failed to fetch products");
}

const products = await response.json();
```

### **Useful Response properties**

| Property              | Description                                     |
| --------------------- | ----------------------------------------------- |
| `response.ok`         | `true` when HTTP status is in the 200–299 range |
| `response.status`     | HTTP status code                                |
| `response.statusText` | Status description                              |
| `response.headers`    | Response headers                                |
| `response.url`        | Final response URL                              |
| `response.json()`     | Parses JSON response body                       |
| `response.text()`     | Reads response body as text                     |

# **5. Fetching Data Inside Server Components**

In the Next.js App Router, Server Components are the recommended starting point for many data-fetching operations.

Why?

- Data can be fetched directly on the server.
- Server-side credentials can remain private.
- You can avoid an unnecessary browser-to-your-own-API-to-database round trip.
- Data can be retrieved before the UI is rendered.

### **Example folder structure**

```
app/
└── products/
    └── page.tsx
```

### **Products page**

```
// app/products/page.tsx

type Product = {
  id: number;
  name: string;
  price: number;
};

export default async function ProductsPage() {
  const response = await fetch(
    "https://api.example.com/products"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  const products: Product[] = await response.json();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>₹{product.price}</p>
        </div>
      ))}
    </main>
  );
}
```

### **Important concepts**

1. The component is asynchronous.
2. `await fetch()` waits for the API response.
3. The response is parsed into JSON.
4. The retrieved data is used to render the page.
5. No `"use client"` directive is required.

**Remember:** An asynchronous Server Component can wait for data before rendering its dependent UI.

# **6. Understanding&#x20;**`async`**&#x20;and&#x20;**`await`

You need a good understanding of JavaScript Promises to understand Next.js data fetching.

### **What is&#x20;**`async`**?**

The `async` keyword makes a function return a Promise.

Example:

```
async function getData() {
  return "Hello";
}
```

Although it returns `"Hello"`, JavaScript wraps the result in a resolved Promise.

Conceptually:

```
function getData() {
  return Promise.resolve("Hello");
}
```

### **What is&#x20;**`await`**?**

The `await` keyword waits for a Promise to settle and gives you its fulfilled value or throws its rejection reason.

Example:

```
async function getData() {
  const result = await Promise.resolve("Hello");

  console.log(result);
}

getData();
```

Output:

```
Hello
```

### **Why do we use them in Next.js?**

Because API requests and database operations are asynchronous.

```
export default async function ProductsPage() {
  const response = await fetch(
    "https://api.example.com/products"
  );

  const products = await response.json();

  return <div>{products.length} products</div>;
}
```

Without awaiting the response, you would be working with a Promise rather than the resolved data.

# **7. Fetching Data with Dynamic Route Parameters**

You can fetch data using dynamic route parameters.

### **Folder structure**

```
app/
└── products/
    └── [id]/
        └── page.tsx
```

For the URL:

```
/products/101
```

The route parameter is:

```
id = "101"
```

### **Example**

```
// app/products/[id]/page.tsx

import { notFound } from "next/navigation";

type Product = {
  id: number;
  name: string;
  price: number;
};

export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  const response = await fetch(
    `https://api.example.com/products/${id}`
  );

  if (response.status === 404) {
    notFound();
  }

  if (!response.ok) {
    throw new Error("Failed to fetch product");
  }

  const product: Product = await response.json();

  return (
    <div>
      <h1>{product.name}</h1>
      <p>₹{product.price}</p>
    </div>
  );
}
```

### **Important points**

- Dynamic route parameters are asynchronous in Next.js 16.
- Use `await params`.
- Validate or constrain user-controlled parameters appropriately.
- Handle missing records with `notFound()`.

# **8. Fetching Data Inside Client Components**

Sometimes you need to fetch data from the browser.

Examples:

- Search suggestions while typing.
- Live notifications.
- User-triggered refresh.
- Data that depends on browser interaction.
- Periodically updated information.

For these use cases, you can use Client Components.

### **Example folder structure**

```
app/
└── components/
    └── ProductsClient.tsx
```

### **Client Component**

```
"use client";

import { useEffect, useState } from "react";

type Product = {
  id: number;
  name: string;
  price: number;
};

export default function ProductsClient() {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function getProducts() {
      try {
        const response = await fetch("/api/products");

        if (!response.ok) {
          throw new Error("Failed to fetch products");
        }

        const data: Product[] = await response.json();

        setProducts(data);
      } catch {
        setError("Unable to load products.");
      } finally {
        setLoading(false);
      }
    }

    getProducts();
  }, []);

  if (loading) {
    return <p>Loading products...</p>;
  }

  if (error) {
    return <p>{error}</p>;
  }

  return (
    <div>
      {products.map((product) => (
        <p key={product.id}>
          {product.name} - ₹{product.price}
        </p>
      ))}
    </div>
  );
}
```

### **How does it work?**

1. The component initially renders with `loading = true`.
2. After mounting in the browser, `useEffect()` runs.
3. The browser sends a request to `/api/products`.
4. The response is parsed.
5. `setProducts()` updates the state.
6. React renders the updated product list.
7. If an error occurs, the error message is displayed.

### **Comparison: Server vs Client fetching**

| Feature                           | Server Component                               | Client Component                       |
| --------------------------------- | ---------------------------------------------- | -------------------------------------- |
| Fetch before initial UI rendering | Yes                                            | Not with the `useEffect` pattern shown |
| `useEffect` required              | No                                             | Often used                             |
| Loading state                     | `loading.tsx` or Suspense                      | React state or a data-fetching library |
| Direct database access            | Yes, with server libraries                     | No                                     |
| Browser APIs                      | No                                             | Yes                                    |
| User-triggered fetching           | Possible through supported server interactions | Yes                                    |
| Secrets                           | Can remain server-side                         | Must not be exposed                    |

**Recommendation:** Prefer Server Components for initial page data. Use Client Components when the fetching behavior genuinely depends on browser interactions.

# **9. Understanding Caching in Next.js**

Caching is one of the most important concepts in Next.js data fetching.

### **What is caching?**

Caching means storing previously retrieved data so that it can be reused instead of fetching it again.

Imagine your website has 10,000 products.

Every time someone opens `/products`, your application could query the database again.

That can cause:

- More database queries.
- Increased server workload.
- Slower responses.
- Higher infrastructure costs.

With caching, Next.js can reuse previously fetched or computed data according to your cache configuration.

### **Simple example**

**Without caching**

User request

Database query on every request

**With caching**

User request

Check cache first

Cache hit

Return cached data

Cache miss

Fetch from database

### **Important caching concepts**

| Term               | Meaning                                                       |
| ------------------ | ------------------------------------------------------------- |
| Cache              | Stored reusable data                                          |
| Cache hit          | Requested data is available in cache                          |
| Cache miss         | Requested data is not available in cache                      |
| Cache invalidation | Marking or removing cached data so fresh data can be obtained |
| Revalidation       | Refreshing cached data according to a rule                    |
| Cache lifetime     | How long cached data remains valid                            |

# **10. Next.js 16 Caching Models**

This is important because Next.js 16 introduced a unified caching approach called **Cache Components**.

You may encounter two caching approaches in Next.js 16 projects:

1. Cache Components model – enabled with `cacheComponents: true`.
2. Previous caching model – used when Cache Components are not enabled.

Do not mix their configuration patterns without understanding which model your project uses.

### **Cache Components model**

Enable it in `next.config.ts`:

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

With Cache Components enabled, you explicitly mark reusable cached work using the `"use cache"` directive.

### **Example**

```
// app/lib/products.ts

import { cacheLife } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheLife("hours");

  const response = await fetch(
    "https://api.example.com/products"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  return response.json();
}
```

### **Understanding&#x20;**`"use cache"`

```
"use cache";
```

It tells Next.js that the function or component's result should be cached.

In this example:

- `getProducts()` retrieves products.
- Next.js caches its result.
- `cacheLife("hours")` defines the cache lifetime profile.

### **Understanding&#x20;**`cacheLife()`

`cacheLife()` controls the cache duration.

Some built-in profiles:

| Profile   | Revalidation interval | Typical use                |
| --------- | --------------------- | -------------------------- |
| `seconds` | 1 second              | Frequently changing data   |
| `minutes` | 1 minute              | Frequently updated content |
| `hours`   | 1 hour                | Product catalog            |
| `days`    | 1 day                 | Daily content              |
| `weeks`   | 1 week                | Rarely changing content    |
| `max`     | 30 days               | Very stable content        |

These profiles also define client stale time and server expiration behavior, so the revalidation interval is not the only timing property.

### **Example: Cache a product listing**

```
// app/lib/products.ts

import { cacheLife } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheLife("hours");

  const response = await fetch(
    "https://api.example.com/products"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  return response.json();
}
```

Then use it in a Server Component:

```
// app/products/page.tsx

import { getProducts } from "@/app/lib/products";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div>
      <h1>Products</h1>

      {products.map((product: any) => (
        <p key={product.id}>{product.name}</p>
      ))}
    </div>
  );
}
```

**Important:** Cache Components are opt-in in Next.js 16. If you do not enable them, the previous caching model applies.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2

# **11. Understanding&#x20;**`fetch()`**&#x20;Cache Options**

Next.js extends the native `fetch()` API with caching options for server-side requests.

Two important options are:

- `force-cache`
- `no-store`

### **A.&#x20;**`cache: "force-cache"`

This tells Next.js to use its persistent Data Cache for the request.

```
const response = await fetch(
  "https://api.example.com/products",
  {
    cache: "force-cache",
  }
);
```

If a fresh matching cached response exists, Next.js can reuse it.

Otherwise, it fetches the resource and stores the successful response.

### **B.&#x20;**`cache: "no-store"`

This tells Next.js not to use the persistent Data Cache for that request.

```
const response = await fetch(
  "https://api.example.com/orders",
  {
    cache: "no-store",
  }
);
```

This is useful when the data must be fetched fresh at request time.

### **Comparison**

| Option        | Behavior                                                  | Example                           |
| ------------- | --------------------------------------------------------- | --------------------------------- |
| `force-cache` | Uses persistent cached response                           | Product catalog                   |
| `no-store`    | Fetches without persistent Data Cache                     | Frequently changing order status  |
| Default       | Depends on the active caching model and rendering context | Explicit configuration is clearer |

**Important Next.js 16 distinction:** Without Cache Components, `fetch` requests are not cached by default. With Cache Components enabled, use `"use cache"` and `cacheLife()` to explicitly cache the relevant function or component.

Do not assume that the default caching behavior is the same as older Next.js tutorials.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2

# **12. Time-Based Revalidation with&#x20;**`next.revalidate`

In the previous caching model, you can configure a fetch request to be revalidated after a specific duration.

### **Syntax**

```
fetch(url, {
  next: {
    revalidate: 3600,
  },
});
```

Here:

```
3600 seconds = 1 hour
```

### **Example**

```
// app/products/page.tsx

export default async function ProductsPage() {
  const response = await fetch(
    "https://api.example.com/products",
    {
      next: {
        revalidate: 3600,
      },
    }
  );

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  const products = await response.json();

  return (
    <div>
      {products.map((product: any) => (
        <p key={product.id}>{product.name}</p>
      ))}
    </div>
  );
}
```

### **How does it work?**

1. Next.js retrieves the data.
2. The result is stored in the Data Cache.
3. The cached data can be reused.
4. After the revalidation interval, a later request can trigger a refresh.
5. The cache is updated with the latest successful response.

The exact behavior depends on the active caching model.

### **Common revalidation intervals**

| Seconds | Duration   |
| ------- | ---------- |
| `60`    | 1 minute   |
| `300`   | 5 minutes  |
| `1800`  | 30 minutes |
| `3600`  | 1 hour     |
| `86400` | 1 day      |

### **When should you use time-based revalidation?**

- Product catalogs.
- Blog posts.
- Public company information.
- Category lists.
- Public documentation.

Use it when data changes occasionally and a small delay before showing the newest data is acceptable.

# **13. Understanding On-Demand Revalidation**

Sometimes you don't want to wait for the cache lifetime to expire.

Imagine an admin updates a product.

You want the updated product to appear on the website immediately or as soon as possible.

This is called **on-demand revalidation**.

Next.js provides functions such as:

- `revalidateTag()`
- `updateTag()`
- `revalidatePath()`

### **Understanding cache tags**

A cache tag is a label attached to cached data.

It lets you invalidate or refresh related data without needing to know every route that uses it.

In the previous caching model, you can attach a tag to a fetch request:

```
const response = await fetch(
  "https://api.example.com/products",
  {
    next: {
      tags: ["products"],
    },
  }
);
```

Now this request is associated with the `products` tag.

### **Revalidate the tag**

```
import { revalidateTag } from "next/cache";

revalidateTag("products", "max");
```

The `"max"` profile uses stale-while-revalidate behavior: stale data can be served while fresh data is retrieved in the background.

For read-your-own-writes behavior in a Server Action, Next.js also provides `updateTag()`.

### **Example with a Server Action**

```
// app/actions.ts

"use server";

import { revalidateTag } from "next/cache";

export async function updateProduct() {
  // Update product in database.

  revalidateTag("products", "max");
}
```

### **Important**

`revalidateTag()` supports tag-based invalidation and refresh behavior. The second argument matters in current Next.js APIs; avoid relying on outdated examples that use only a single argument.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

# **14. Understanding&#x20;**`updateTag()`

`updateTag()` is useful when you want a user to see their own changes immediately after a Server Action.

For example:

1. Admin updates a product.
2. Product information is saved.
3. The related cached data is immediately expired.
4. The next read obtains fresh data.

### **Example**

```
// app/actions.ts

"use server";

import { updateTag } from "next/cache";

export async function updateProduct(formData: FormData) {
  const productId = formData.get("productId");

  // Validate and update the product in the database.

  updateTag(`product-${productId}`);
}
```

### `revalidateTag()`**&#x20;vs&#x20;**`updateTag()`

| Feature                | `revalidateTag()`              | `updateTag()`                  |
| ---------------------- | ------------------------------ | ------------------------------ |
| Purpose                | Refresh tagged cached data     | Immediately expire tagged data |
| Stale-while-revalidate | Supported with `"max"` profile | No                             |
| Server Actions         | Yes                            | Yes                            |
| Route Handlers         | Yes                            | No                             |
| Typical use            | Background refresh             | Read-your-own-writes           |

`updateTag()` is specifically intended for Server Actions.

# **15. Understanding&#x20;**`revalidatePath()`

`revalidatePath()` invalidates cached data associated with a particular route path.

### **Syntax**

```
import { revalidatePath } from "next/cache";

revalidatePath("/products");
```

### **Example**

Suppose an admin updates a product.

You want to refresh:

```
/products
```

You can use:

```
// app/actions.ts

"use server";

import { revalidatePath } from "next/cache";

export async function updateProduct(formData: FormData) {
  // Update product in database.

  revalidatePath("/products");
}
```

### **Tag vs Path**

| Feature    | `revalidateTag()`                  | `revalidatePath()`            |
| ---------- | ---------------------------------- | ----------------------------- |
| Based on   | Data tag                           | Route path                    |
| Purpose    | Revalidate related tagged data     | Revalidate a particular route |
| Useful for | Shared data used by multiple pages | A specific page or route      |
| Example    | `products`                         | `/products`                   |

### **When should you use each?**

Use `revalidateTag()` when the same data is used across multiple routes.

Use `revalidatePath()` when you want to refresh a particular route.

# **16. Direct Database Fetching in Server Components**

You do not always need to create a REST API before fetching database records.

A Server Component can directly call a database function.

For example, if you use MongoDB and Mongoose, you can query MongoDB from a server-only module.

### **Folder structure**

```
src/
├── app/
│   └── products/
│       └── page.tsx
│
├── lib/
│   ├── db.ts
│   └── products.ts
│
└── models/
    └── Product.ts
```

### **Database connection**

```
// src/lib/db.ts

import "server-only";

import mongoose from "mongoose";

export async function connectDB() {
  if (mongoose.connection.readyState >= 1) {
    return;
  }

  await mongoose.connect(
    process.env.MONGODB_URI!
  );
}
```

### **Product model**

```
// src/models/Product.ts

import mongoose, { Schema, model, models } from "mongoose";

const ProductSchema = new Schema({
  name: {
    type: String,
    required: true,
  },

  price: {
    type: Number,
    required: true,
  },

  description: {
    type: String,
  },
});

const Product =
  models.Product || model("Product", ProductSchema);

export default Product;
```

### **Product data function**

```
// src/lib/products.ts

import "server-only";

import { connectDB } from "./db";
import Product from "@/models/Product";

export async function getProducts() {
  await connectDB();

  const products = await Product.find()
    .select("name price description")
    .lean();

  return products.map((product) => ({
    id: product._id.toString(),
    name: product.name,
    price: product.price,
    description: product.description,
  }));
}
```

### **Products page**

```
// src/app/products/page.tsx

import { getProducts } from "@/lib/products";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>₹{product.price}</p>
          <p>{product.description}</p>
        </div>
      ))}
    </div>
  );
}
```

### **Important advantages**

- No unnecessary internal HTTP request.
- Database credentials stay on the server.
- You can reuse database functions.
- Data fetching and rendering can be kept in a clear architecture.

### **Important security point**

Direct database access does not automatically make a query secure.

Always verify that the current user is allowed to access the requested records.

# **17. Fetching Data from MySQL**

The same principle applies to MySQL.

You can directly query MySQL from a Server Component or a server-only data-access module.

### **Example using&#x20;**`mysql2/promise`

```
// src/lib/mysql.ts

import "server-only";

import mysql from "mysql2/promise";

export const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  connectionLimit: 10,
});
```

### **Product data function**

```
// src/lib/products.ts

import "server-only";

import { pool } from "./mysql";

type Product = {
  id: number;
  name: string;
  price: number;
};

export async function getProducts(): Promise<Product[]> {
  const [rows] = await pool.execute(
    "SELECT id, name, price FROM products ORDER BY id DESC"
  );

  return rows as Product[];
}
```

### **Server Component**

```
// app/products/page.tsx

import { getProducts } from "@/lib/products";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>₹{product.price}</p>
        </div>
      ))}
    </div>
  );
}
```

**Important:** Use parameterized queries for user-provided values. Never concatenate raw user input into SQL query strings.

# **18. Sequential Data Fetching**

Sequential fetching means executing asynchronous operations one after another.

The next operation starts after the previous operation completes.

### **Example**

Imagine you need:

1. User information.
2. The user's orders.
3. The details of each order.

The order fetching depends on the user ID.

```
async function getDashboardData(userId: string) {
  const user = await getUser(userId);

  const orders = await getOrders(user.id);

  const orderDetails = await getOrderDetails(
    orders[0].id
  );

  return {
    user,
    orders,
    orderDetails,
  };
}
```

### **Execution flow**

```
getUser()
    |
    v
getOrders()
    |
    v
getOrderDetails()
    |
    v
Return result
```

### **When should you use sequential fetching?**

Use it when the next operation depends on the previous result.

Examples:

- Fetch user, then fetch orders using the user ID.
- Fetch category, then fetch its products.
- Authenticate user, then fetch their private profile.

# **19. Parallel Data Fetching**

Parallel fetching means starting independent asynchronous operations without waiting for each one to finish before starting the next.

JavaScript provides:

```
Promise.all()
```

### **Example**

Suppose your dashboard needs:

- User profile.
- Total sales.
- Recent orders.
- Notifications.

All four operations are independent.

```
async function getDashboardData(userId: string) {
  const [user, sales, orders, notifications] =
    await Promise.all([
      getUser(userId),
      getSales(userId),
      getOrders(userId),
      getNotifications(userId),
    ]);

  return {
    user,
    sales,
    orders,
    notifications,
  };
}
```

### **Execution flow**

**Parallel execution**

Start all operations

User profile

Concurrent

Sales

Concurrent

Orders

Concurrent

Notifications

Concurrent

All results available

### **Sequential vs Parallel**

| Feature           | Sequential                        | Parallel                            |
| ----------------- | --------------------------------- | ----------------------------------- |
| Execution         | One after another                 | Concurrent                          |
| Dependencies      | Suitable for dependent operations | Suitable for independent operations |
| Waiting time      | Often higher                      | Often lower                         |
| JavaScript method | Multiple `await` calls            | `Promise.all()`                     |

### **Performance example**

Suppose four independent API calls take:

| API           | Duration  |
| ------------- | --------- |
| User          | 1 second  |
| Sales         | 2 seconds |
| Orders        | 3 seconds |
| Notifications | 1 second  |

Sequential execution takes approximately 7 seconds.

Parallel execution takes approximately 3 seconds, assuming the operations overlap and there is no additional bottleneck.

**Important:** `Promise.all()` rejects when one of its promises rejects. If you need to collect every outcome even when some operations fail, consider `Promise.allSettled()`.

# **20. Avoiding Waterfalls with Suspense**

You learned Suspense in Phase 7.

Now let's connect it with data fetching.

Suppose your dashboard has three independent sections:

- Sales.
- Orders.
- Notifications.

You can fetch and render them independently.

```
// app/dashboard/page.tsx

import { Suspense } from "react";

async function Sales() {
  const sales = await getSales();

  return <div>Sales: ₹{sales.total}</div>;
}

async function Orders() {
  const orders = await getOrders();

  return <div>Orders: {orders.length}</div>;
}

async function Notifications() {
  const notifications = await getNotifications();

  return <div>Notifications: {notifications.length}</div>;
}

export default function DashboardPage() {
  return (
    <main>
      <h1>Dashboard</h1>

      <Suspense fallback={<p>Loading sales...</p>}>
        <Sales />
      </Suspense>

      <Suspense fallback={<p>Loading orders...</p>}>
        <Orders />
      </Suspense>

      <Suspense fallback={<p>Loading notifications...</p>}>
        <Notifications />
      </Suspense>
    </main>
  );
}
```

Each component can suspend independently.

This allows Next.js to stream each section when it is ready rather than requiring all sections to finish first.

**Remember:**

- `Promise.all()` coordinates independent promises.
- Suspense controls the loading UI.
- Streaming allows ready UI to reach the browser progressively.

They solve related but different problems.

# **21. Handling Errors During Data Fetching**

Data fetching can fail for many reasons:

- Network failure.
- API returning an error status.
- Database unavailable.
- Invalid response.
- Timeout.

You should handle these failures deliberately.

### **Example**

```
// app/products/page.tsx

export default async function ProductsPage() {
  try {
    const response = await fetch(
      "https://api.example.com/products",
      {
        cache: "no-store",
      }
    );

    if (!response.ok) {
      throw new Error(
        `API request failed: ${response.status}`
      );
    }

    const products = await response.json();

    return (
      <div>
        <h1>Products</h1>

        {products.map((product: any) => (
          <p key={product.id}>{product.name}</p>
        ))}
      </div>
    );
  } catch (error) {
    console.error("Products fetching error:", error);

    throw new Error("Unable to load products.");
  }
}
```

The error can be caught by the nearest applicable `error.tsx` boundary.

### **Important distinction**

If an API returns a 404 because a product doesn't exist, you may want to call `notFound()` instead of throwing a generic error.

If a database server is unavailable, an error boundary may be appropriate.

# **22. Client-Side Data Fetching with SWR**

For more complex client-side data fetching, developers often use libraries such as:

- SWR.
- TanStack Query (React Query).

These libraries provide features such as:

- Caching.
- Background revalidation.
- Refetching.
- Loading and error states.
- Mutation support.
- Request deduplication.

### **What is SWR?**

SWR is a React data-fetching library developed by Vercel.

Its name comes from the stale-while-revalidate strategy.

### **Installation**

```
npm install swr
```

### **Example**

```
// app/components/ProductsClient.tsx

"use client";

import useSWR from "swr";

const fetcher = async (url: string) => {
  const response = await fetch(url);

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  return response.json();
};

export default function ProductsClient() {
  const {
    data,
    error,
    isLoading,
  } = useSWR("/api/products", fetcher);

  if (isLoading) {
    return <p>Loading products...</p>;
  }

  if (error) {
    return <p>Unable to load products.</p>;
  }

  return (
    <div>
      {data.map((product: any) => (
        <p key={product.id}>{product.name}</p>
      ))}
    </div>
  );
}
```

### **Why use SWR?**

SWR can manage client-side data state and automatically revalidate data according to its configuration.

### **When should you use it?**

- Live search.
- Frequently updated dashboards.
- Notifications.
- User-specific client-side data.
- Browser-driven filtering and refreshing.

### **Server Components vs SWR**

| Feature                 | Server Components                 | SWR                                                                            |
| ----------------------- | --------------------------------- | ------------------------------------------------------------------------------ |
| Main environment        | Server                            | Client                                                                         |
| Initial data rendering  | Yes                               | Usually fetched after client component mounts unless fallback data is supplied |
| Browser interactions    | No                                | Yes                                                                            |
| Client cache            | Not its main role                 | Yes                                                                            |
| Background revalidation | Via Next.js cache strategies      | Built in                                                                       |
| Database access         | Directly through server libraries | Through an API/server endpoint                                                 |

**Best practice:** Don't add SWR or React Query to every page automatically. Use them when client-side data synchronization genuinely provides value.

# **23. When Should You Use Server Fetching vs Client Fetching?**

Let's make the decision clearer.

| Situation                         | Recommended approach                             |
| --------------------------------- | ------------------------------------------------ |
| Product listing page              | Server Component                                 |
| Product details page              | Server Component                                 |
| Blog page                         | Server Component                                 |
| SEO-sensitive page                | Server Component                                 |
| Database query                    | Server Component / server-only data function     |
| Search suggestions while typing   | Client Component                                 |
| Live notification updates         | Client Component + data-fetching library         |
| User clicks Refresh               | Client Component or supported server interaction |
| Dashboard initial data            | Server Component, optionally streamed            |
| Frequently polling browser widget | Client Component + SWR or TanStack Query         |

### **A practical rule**

Start with Server Components for initial data.

Move fetching to the client only when the application needs client-specific behavior, such as interactive filtering, live updates, or repeated user-triggered requests.

# **24. Common Mistakes**

| Mistake                                                                       | Correct approach                                                    |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Forgetting to check `response.ok`                                             | Check HTTP status before parsing                                    |
| Assuming HTTP 404 automatically throws                                        | Handle non-2xx responses explicitly                                 |
| Fetching initial page data unnecessarily in `useEffect`                       | Prefer Server Components when appropriate                           |
| Using `cache: "force-cache"` for rapidly changing data                        | Choose a suitable freshness strategy                                |
| Assuming Next.js 16 always caches fetch requests by default                   | Understand the active caching model                                 |
| Running independent API calls sequentially                                    | Use `Promise.all()`                                                 |
| Running dependent queries in parallel                                         | Fetch sequentially when dependencies exist                          |
| Calling your own API from a Server Component just to access your own database | Query the database through a server-only data-access module         |
| Exposing database credentials to Client Components                            | Keep secrets in server-only modules                                 |
| Using Suspense as a replacement for `Promise.all()`                           | Understand their different purposes                                 |
| Ignoring API and database failures                                            | Handle expected errors and use error boundaries for unexpected ones |
| Caching user-specific private data without proper cache isolation             | Design caching and authorization carefully                          |

# **25. Interview Questions and Answers**

## **Basic Level**

**Q1. What is data fetching?**

Data fetching is the process of retrieving data from a source such as an API, database, or external service.

**Q2. How can we fetch data in a Server Component?**

Make the component asynchronous and use `await` with `fetch()` or a database/ORM operation.

**Q3. Can we fetch data in Client Components?**

Yes. We can use `useEffect`, event handlers, or client-side libraries such as SWR and TanStack Query.

**Q4. What is the difference between&#x20;**`fetch()`**&#x20;and&#x20;**`response.json()`**?**

`fetch()` makes the HTTP request and returns a Response object. `response.json()` reads and parses its response body as JSON.

**Q5. Does&#x20;**`fetch()`**&#x20;throw automatically for HTTP 404 or 500?**

No. It generally resolves with a Response object. You should check `response.ok` or `response.status`.

## **Intermediate Level**

**Q6. Why is Server Component data fetching recommended?**

It allows data retrieval on the server, keeps credentials private, and can avoid unnecessary client-side request layers.

**Q7. What is caching?**

Caching stores reusable data so it does not have to be retrieved again for every request.

**Q8. What is revalidation?**

Revalidation updates cached data according to a time-based or event-based strategy.

**Q9. What is the difference between&#x20;**`force-cache`**&#x20;and&#x20;**`no-store`**?**

`force-cache` uses the persistent Data Cache, while `no-store` bypasses that cache for the request.

**Q10. What is parallel data fetching?**

It means starting independent asynchronous operations concurrently, commonly using `Promise.all()`.

## **Advanced Level**

**Q11. What is sequential data fetching?**

It is when one asynchronous operation completes before the next dependent operation begins.

**Q12. What is the difference between&#x20;**`revalidateTag()`**&#x20;and&#x20;**`revalidatePath()`**?**

`revalidateTag()` targets cached data associated with a tag, while `revalidatePath()` targets cached data associated with a route path.

**Q13. What is&#x20;**`updateTag()`**?**

It immediately expires tagged cached data and is designed for read-your-own-writes behavior in Server Actions.

**Q14. What is Cache Components in Next.js 16?**

It is the caching model introduced in Next.js 16 using `cacheComponents: true`, `"use cache"`, and APIs such as `cacheLife()` and `cacheTag()`.

**Q15. Can Server Components directly query MongoDB?**

Yes. They can use server-side database clients and ORMs, provided those modules remain server-only and the operations are properly secured.

**Q16. What is SWR?**

SWR is a React library for client-side data fetching, caching, and background revalidation.

**Q17. What is the difference between SWR and Next.js Data Cache?**

SWR manages client-side data state and revalidation. Next.js Data Cache manages server-side cached fetch results in the relevant caching model.

**Q18. Why should we avoid unnecessary internal API calls from Server Components?**

Because they can introduce an unnecessary HTTP round trip. Directly calling a server-side data-access function is often simpler and more efficient.

# **26. Practical Assignment**



Hands-on project

**Build a Complete Product Data Fetching System**



Create an e-commerce application using Next.js, TypeScript, and MongoDB or MySQL.

[Creá tu tienda online y página web en Argentina con IA, gestión empresarial y Wishopai Punto de Venta incluidos | Wishopai](https://images.openai.com/static-rsc-4/ZBhQJt2TWMVf5Y7C2v9HVt0_iQHSXSoiT54Cm3Oxw1QxIzJqYJ7pyg8kk_c8nisyszWZ-Z1xayAcD3aJwnGeLYJ7AJaU3W-xIMZegrpxYw-hP1GkAqydU2tmRmGPeiS1v683iMVgk3Epa7VEeaWDAHOb4mAXkXI4vvHo1Qs3oGE?purpose=inline)

[wishopai.com](https://wishopai.com/ar/?utm_source=chatgpt.com)

### **Task 1: Create the folder structure**

```
src/
├── app/
│   ├── layout.tsx
│   │
│   ├── products/
│   │   ├── page.tsx
│   │   └── [id]/
│   │       └── page.tsx
│   │
│   ├── dashboard/
│   │   └── page.tsx
│   │
│   └── api/
│       └── products/
│           └── route.ts
│
├── components/
│   ├── ProductCard.tsx
│   ├── ProductList.tsx
│   ├── SearchBar.tsx
│   └── DashboardStats.tsx
│
└── lib/
    ├── db.ts
    └── products.ts
```

### **Task 2: Implement server-side fetching**

Create:

- `getProducts()`
- `getProductById(id)`
- `getFeaturedProducts()`
- `getProductsByCategory(category)`

Use your database directly from server-only modules.

### **Task 3: Implement REST API fetching**

Fetch data from a public API and display it using a Server Component.

Remember to check `response.ok`.

### **Task 4: Implement client-side fetching**

Create a search component that fetches matching products when the user types.

### **Task 5: Implement caching**

Experiment with:

- `cache: "force-cache"`.
- `cache: "no-store"`.
- `next.revalidate` in the previous caching model.
- `"use cache"` and `cacheLife()` with Cache Components enabled.

### **Task 6: Implement revalidation**

Create a Server Action that updates a product and then:

- Uses `updateTag()` for immediate cache expiration.
- Uses `revalidateTag()` for stale-while-revalidate behavior.
- Uses `revalidatePath()` to refresh a particular route.

### **Task 7: Implement parallel fetching**

Create a dashboard that fetches:

- Total products.
- Total users.
- Total orders.
- Total revenue.

Use `Promise.all()` when these operations are independent.

### **Task 8: Implement error handling**

- Handle API errors.
- Handle database failures.
- Display loading UI.
- Display error UI.
- Display 404 UI for missing products.

### **Task 9: Performance review**

Answer these questions:

1. Which data should be cached?
2. Which data must be fresh on every request?
3. Which operations can run in parallel?
4. Which operations depend on previous results?
5. Which components should fetch data on the server?
6. Which components genuinely require client-side fetching?

# **27. Quick Revision Notes**

| Concept             | Key point                                     |
| ------------------- | --------------------------------------------- |
| Data fetching       | Retrieving data from an API or database       |
| `fetch()`           | Makes HTTP requests                           |
| `response.json()`   | Parses JSON response                          |
| `async`             | Declares an asynchronous function             |
| `await`             | Waits for a Promise to settle                 |
| Server fetching     | Retrieves data in Server Components           |
| Client fetching     | Retrieves data in Client Components           |
| Caching             | Reuses stored data                            |
| `force-cache`       | Uses persistent Data Cache                    |
| `no-store`          | Bypasses persistent Data Cache                |
| `revalidate`        | Controls when cached data is refreshed        |
| `cacheLife()`       | Defines Cache Components lifetime             |
| `"use cache"`       | Marks a function or component as cacheable    |
| `revalidateTag()`   | Revalidates tagged data                       |
| `updateTag()`       | Immediately expires tagged data               |
| `revalidatePath()`  | Revalidates a route path                      |
| `Promise.all()`     | Runs independent operations concurrently      |
| Sequential fetching | Executes dependent operations in order        |
| SWR                 | Client-side fetching and revalidation library |
| Direct DB access    | Use server-only database modules              |

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+3

# **28. Phase 10 Completion Checklist**

**Your progress**

# **0%**

0 of 24 completed

Understand data fetching

Understand native fetch API

Check HTTP response status

Fetch data in Server Components

Understand async and await

Fetch using dynamic route parameters

Fetch data in Client Components

Understand server-side caching

Understand Cache Components in Next.js 16

Understand force-cache and no-store

Understand time-based revalidation

Understand cache tags

Understand revalidateTag()

Understand updateTag()

Understand revalidatePath()

Fetch directly from MongoDB

Fetch directly from MySQL

Understand sequential fetching

Understand parallel fetching

Use Promise.all() and Promise.allSettled()

Use Suspense with data fetching

Understand SWR

Handle API and database errors

Complete the product data fetching assignment

# **29. What's Next? – Phase 11**

Next phase

# **Route Handlers and API Development**

**Topics we will cover:**

1. What are Route Handlers?
2. Understanding `route.ts`.
3. Creating REST APIs in Next.js.
4. GET, POST, PUT, PATCH and DELETE.
5. Understanding `NextRequest` and `NextResponse`.
6. Dynamic API routes.
7. Query parameters.
8. Request body and response handling.
9. HTTP status codes.
10. Connecting Route Handlers to MongoDB.
11. Connecting Route Handlers to MySQL.
12. API validation and error handling.
13. Authentication and authorization in APIs.
14. Testing APIs using Postman.
15. REST API best practices.

When you're ready, say **"OK, Phase 11"** and we'll continue with detailed notes, code examples, and practical exercises.