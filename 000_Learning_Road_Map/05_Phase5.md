# **Next.js Complete Learning Roadmap**

# **Phase 5: Dynamic Routes and Route Parameters**



Beginner → Intermediate

Next.js 16.x



**Architecture:** App Router\
**Language:** TypeScript\
**Goal:** Learn how to create dynamic URLs, access route parameters, build dynamic pages, handle multiple parameters, and generate pages using database records.

In Phase 4, we learned how to create static routes such as:

```
/about
/contact
/products
```

But real-world applications need much more flexibility.

For example, consider an e-commerce application with 10,000 products.

Would you create 10,000 separate folders?

```
products/
├── 1/
│   └── page.tsx
├── 2/
│   └── page.tsx
├── 3/
│   └── page.tsx
...
```

Obviously, this is not practical.

Instead, we use **Dynamic Routes**.

# **1. What are Dynamic Routes?**

Dynamic Routes are routes in which one or more URL segments are determined dynamically using values from the URL.

For example:

```
/products/101
/products/102
/products/103
/products/104
```

All these URLs can use the same page component.

The only difference is the product ID.

### **Simple Definition**

**A Dynamic Route is a route that uses variable URL segments to display different content through a reusable page component.**

### **Real-World Examples**

| URL                     | Dynamic Value    |
| ----------------------- | ---------------- |
| `/products/101`         | Product ID = 101 |
| `/products/102`         | Product ID = 102 |
| `/users/riyaz`          | Username = riyaz |
| `/users/ahmed`          | Username = ahmed |
| `/blog/nextjs-learning` | Blog slug        |
| `/orders/5001`          | Order ID         |

### **Why Do We Need Dynamic Routes?**

Dynamic routes allow us to:

1. Create reusable pages.
2. Handle thousands of records.
3. Display database-driven content.
4. Avoid creating separate pages manually.
5. Build scalable applications.

# **2. Understanding Dynamic Route Syntax&#x20;**`[id]`

Next.js uses square brackets to define dynamic route segments.

Example:

```
[id]
```

Here, `id` is the name of the dynamic parameter.

### **Project Structure**

```
src/
└── app/
    │
    └── products/
        │
        └── [id]/
            └── page.tsx
```

This creates a dynamic route:

```
/products/[id]
```

It can match:

```
/products/101
/products/102
/products/103
/products/abc
```

The value inside the URL is available through the `id` parameter.

### **Important**

The name inside the brackets becomes the parameter name.

Examples:

```
[id]
[slug]
[username]
[productId]
[orderId]
```

# **3. Creating Your First Dynamic Route**

Let's create a product details page.

### **Step 1: Create Folder Structure**

```
src/
└── app/
    └── products/
        └── [id]/
            └── page.tsx
```

### **Step 2: Write the Page Component**

In Next.js 16, route `params` are asynchronous and should be awaited in Server Components.

`src/app/products/[id]/page.tsx`

```
type ProductPageProps = {
  params: Promise<{
    id: string;
  }>;
};

export default async function ProductPage({
  params,
}: ProductPageProps) {
  const { id } = await params;

  return (
    <div>
      <h1>Product Details</h1>

      <p>Product ID: {id}</p>
    </div>
  );
}
```

### **Step 3: Open Browser**

Visit:

```
http://localhost:3000/products/101
```

Output:

```
Product Details

Product ID: 101
```

Now visit:

```
http://localhost:3000/products/500
```

Output:

```
Product Details

Product ID: 500
```

The same component handles both URLs.

Dynamic route working

### **What Happens Internally?**

When the user requests:

```
/products/101
```

Next.js:

1. Matches the `/products` route segment.
2. Identifies `[id]` as a dynamic segment.
3. Extracts `101` from the URL.
4. Provides it through `params`.
5. Renders the corresponding page.

# **4. What are Route Parameters?**

Route parameters are values extracted from dynamic URL segments.

Consider:

```
/products/101
```

The dynamic segment is:

```
[id]
```

The route parameter is:

```
{
  id: "101"
}
```

### **Example**

URL:

```
/users/riyaz
```

Folder:

```
app/users/[username]/page.tsx
```

Parameter:

```
{
  username: "riyaz"
}
```

### **Important Characteristics**

- Route parameter values are strings.
- Parameters come from dynamic URL segments.
- Parameter names are determined by folder names.
- Parameters can be accessed in Server and Client Components.
- In Next.js 16 App Router, `params` is a Promise.

**Important:** A route parameter being a string does not mean the underlying database ID must be a string. You may need to validate and convert it.

# **5. Understanding params in Server Components**

Server Components are the default in the Next.js App Router.

You can directly receive `params` through the page component's props.

### **Example**

```
type UserPageProps = {
  params: Promise<{
    username: string;
  }>;
};

export default async function UserPage({
  params,
}: UserPageProps) {
  const { username } = await params;

  return (
    <div>
      <h1>User Profile</h1>

      <p>Username: {username}</p>
    </div>
  );
}
```

Folder structure:

```
app/
└── users/
    └── [username]/
        └── page.tsx
```

URL:

```
/users/riyaz
```

Output:

```
User Profile

Username: riyaz
```

### **Why Do We Use await params?**

In Next.js 15 and later, request-related APIs such as route `params` became asynchronous.

Next.js 16 uses this asynchronous API.

Therefore:

```
const { username } = await params;
```

is the correct pattern.

### **What About Older Next.js Versions?**

Older versions used synchronous params in the App Router.

For example:

```
// Older synchronous pattern

type Props = {
  params: {
    id: string;
  };
};

export default function ProductPage({ params }: Props) {
  return <h1>{params.id}</h1>;
}
```

Do not use this as the standard pattern for our Next.js 16 learning project.

# **6. Accessing Params in Client Components**

Sometimes you need dynamic route parameters inside a Client Component.

For example:

- Product interaction.
- Client-side filtering.
- User profile editing.
- Interactive dashboards.

You can use the `useParams()` hook.

### **Example**

```
"use client";

import { useParams } from "next/navigation";

export default function ProductDetails() {
  const params = useParams<{ id: string }>();

  return (
    <div>
      <h1>Product Details</h1>

      <p>Product ID: {params.id}</p>
    </div>
  );
}
```

Folder:

```
app/
└── products/
    └── [id]/
        └── page.tsx
```

### **Server Params vs Client useParams**

| Server Component            | Client Component              |
| --------------------------- | ----------------------------- |
| Receives `params` as a prop | Uses `useParams()`            |
| `params` is a Promise       | Hook returns parameter object |
| Uses `await params`         | No await required             |
| No `use client` required    | Requires `use client`         |

### **Which Approach Should We Prefer?**

For simple data-driven pages, prefer accessing `params` in the Server Component.

This allows you to fetch database records directly from server-side code without exposing database credentials to the browser.

Use `useParams()` when you specifically need route parameters inside client-side interactive components.

# **7. Multiple Dynamic Route Segments**

Sometimes a URL contains multiple dynamic values.

Example:

```
/users/25/orders/500
```

Here we have:

- User ID = 25
- Order ID = 500

We can create a route using multiple dynamic folders.

### **Project Structure**

```
app/
└── users/
    └── [userId]/
        └── orders/
            └── [orderId]/
                └── page.tsx
```

### **Code**

```
type OrderPageProps = {
  params: Promise<{
    userId: string;
    orderId: string;
  }>;
};

export default async function OrderPage({
  params,
}: OrderPageProps) {
  const { userId, orderId } = await params;

  return (
    <div>
      <h1>Order Details</h1>

      <p>User ID: {userId}</p>

      <p>Order ID: {orderId}</p>
    </div>
  );
}
```

### **Generated Route**

```
/users/25/orders/500
```

Output:

```
Order Details

User ID: 25

Order ID: 500
```

### **Important Concept**

Each dynamic folder creates a separate parameter.

For example:

```
[category]/[productId]
```

Produces:

```
{
  category: "electronics",
  productId: "101"
}
```

# **8. What are Catch-All Routes?**

A Catch-All Route is used when you want to capture multiple URL segments dynamically.

Next.js provides this syntax:

```
[...slug]
```

Notice the three dots (`...`) inside the square brackets.

### **Why Do We Need Catch-All Routes?**

Suppose you are building a documentation website.

You may have URLs like:

```
/docs
/docs/getting-started
/docs/getting-started/installation
/docs/getting-started/installation/windows
```

You don't want to create separate folders for every possible documentation path.

Instead, you can use a Catch-All Route.

### **Project Structure**

```
app/
└── docs/
    └── [...slug]/
        └── page.tsx
```

### **Code Example**

```
type DocsPageProps = {
  params: Promise<{
    slug: string[];
  }>;
};

export default async function DocsPage({
  params,
}: DocsPageProps) {
  const { slug } = await params;

  return (
    <div>
      <h1>Documentation</h1>

      <p>Path: {slug.join("/")}</p>

      <ul>
        {slug.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

### **Example 1**

URL:

```
/docs/getting-started
```

Parameter:

```
{
  slug: ["getting-started"]
}
```

### **Example 2**

URL:

```
/docs/getting-started/installation
```

Parameter:

```
{
  slug: ["getting-started", "installation"]
}
```

### **Example 3**

URL:

```
/docs/getting-started/installation/windows
```

Parameter:

```
{
  slug: [
    "getting-started",
    "installation",
    "windows"
  ]
}
```

### **Important Characteristics**

- Catch-All Routes capture multiple URL segments.
- The parameter value is an array of strings.
- At least one segment must be present.
- It does not match the parent route without any additional segment.

For example, `[...slug]` matches:

```
/docs/getting-started
```

But does not match:

```
/docs
```

# **9. What are Optional Catch-All Routes?**

Optional Catch-All Routes are similar to Catch-All Routes, but they can also match the parent route without additional segments.

Syntax:

```
[[...slug]]
```

Notice the double square brackets.

### **Why Do We Need Optional Catch-All Routes?**

Suppose you are building a documentation application.

You want the same page component to handle:

```
/docs
/docs/getting-started
/docs/getting-started/installation
/docs/getting-started/installation/windows
```

An Optional Catch-All Route can handle all these URLs.

### **Project Structure**

```
app/
└── docs/
    └── [[...slug]]/
        └── page.tsx
```

### **Code Example**

```
type DocsPageProps = {
  params: Promise<{
    slug?: string[];
  }>;
};

export default async function DocsPage({
  params,
}: DocsPageProps) {
  const { slug } = await params;

  return (
    <div>
      <h1>Documentation</h1>

      {slug ? (
        <p>{slug.join("/")}</p>
      ) : (
        <p>Documentation Home</p>
      )}
    </div>
  );
}
```

### **Example 1**

URL:

```
/docs
```

Parameter:

```
{
  slug: undefined
}
```

### **Example 2**

URL:

```
/docs/getting-started
```

Parameter:

```
{
  slug: ["getting-started"]
}
```

### **Example 3**

URL:

```
/docs/getting-started/installation
```

Parameter:

```
{
  slug: ["getting-started", "installation"]
}
```

### **Catch-All vs Optional Catch-All**

| Feature               | Catch-All   | Optional Catch-All       |
| --------------------- | ----------- | ------------------------ |
| Syntax                | `[...slug]` | `[[...slug]]`            |
| Multiple segments     | Yes         | Yes                      |
| Parent route matching | No          | Yes                      |
| Parameter type        | `string[]`  | `string[] \\| undefined` |
| Minimum segments      | One         | Zero                     |

**Route matching examples**

| **URL**           | `[...slug]` | `[[...slug]]` |
| ----------------- | ----------- | ------------- |
| /docs             | No match    | Match         |
| /docs/react       | Match       | Match         |
| /docs/react/hooks | Match       | Match         |

# **10. Understanding Dynamic Route Matching**

Now let's understand how Next.js decides which route should handle a URL.

Suppose your application has these routes:

```
app/
│
└── products/
    │
    ├── page.tsx
    │
    ├── new/
    │   └── page.tsx
    │
    ├── [id]/
    │   └── page.tsx
    │
    └── [...slug]/
        └── page.tsx
```

These routes can handle:

| URL                     | Matching Route                |
| ----------------------- | ----------------------------- |
| `/products`             | `products/page.tsx`           |
| `/products/new`         | `products/new/page.tsx`       |
| `/products/101`         | `products/[id]/page.tsx`      |
| `/products/101/reviews` | `products/[...slug]/page.tsx` |

### **What Happens If a Static and Dynamic Route Both Match?**

Consider:

```
/products/new
```

It could theoretically match:

```
products/new/page.tsx
```

or:

```
products/[id]/page.tsx
```

Next.js prioritizes the static route.

The route matching priority is broadly:

1. Static routes.
2. Dynamic routes.
3. Catch-All routes.

Optional Catch-All routes have lower priority than matching more specific routes.

### **Example**

```
products/
├── new/
│   └── page.tsx
│
└── [id]/
    └── page.tsx
```

When the user visits:

```
/products/new
```

Next.js renders the static `/products/new` route rather than interpreting `new` as the dynamic ID.

**Best Practice:** Avoid using static route names that conflict with the meaning of your dynamic parameters.

# **11. Static Routes vs Dynamic Routes vs Catch-All Routes**

Let's compare all route types.

| Route Type         | Syntax         | Example URL              |
| ------------------ | -------------- | ------------------------ |
| Static             | `about/`       | `/about`                 |
| Dynamic            | `[id]/`        | `/products/101`          |
| Catch-All          | `[...slug]/`   | `/docs/react/hooks`      |
| Optional Catch-All | `[[...slug]]/` | `/docs` or `/docs/react` |

### **Important Distinction**

There are two different meanings of the word *static* in Next.js.

**Static route** refers to a predefined URL path.

**Static rendering** refers to when HTML or route output is generated ahead of a request.

These are not the same thing.

For example:

```
/products/[id]
```

is a dynamic route pattern, but individual URLs from that pattern can still be statically generated using `generateStaticParams()`.

We will study rendering strategies in detail in Phases 14–16.

# **12. What is generateStaticParams()?**

`generateStaticParams()` is a Next.js function used to generate dynamic route parameters during the build process.

It allows Next.js to pre-render selected dynamic routes.

### **Why Do We Need It?**

Suppose you have a blog website with these articles:

```
/blog/nextjs-introduction
/blog/react-hooks
/blog/nodejs-basics
```

You want Next.js to generate these pages during the build process.

Instead of waiting for each request, you can provide the required route parameters through `generateStaticParams()`.

### **Project Structure**

```
app/
└── blog/
    └── [slug]/
        └── page.tsx
```

### **Code Example**

```
type BlogPageProps = {
  params: Promise<{
    slug: string;
  }>;
};

export async function generateStaticParams() {
  return [
    { slug: "nextjs-introduction" },
    { slug: "react-hooks" },
    { slug: "nodejs-basics" },
  ];
}

export default async function BlogPage({
  params,
}: BlogPageProps) {
  const { slug } = await params;

  return (
    <div>
      <h1>Blog Details</h1>

      <p>Blog Slug: {slug}</p>
    </div>
  );
}
```

### **What Happens During Build?**

When you execute:

```
npm run build
```

Next.js calls:

```
generateStaticParams()
```

It receives:

```
[
  { slug: "nextjs-introduction" },
  { slug: "react-hooks" },
  { slug: "nodejs-basics" }
]
```

Next.js can use these values to generate the corresponding route output during the build.

### **Generated Pages**

```
/blog/nextjs-introduction
/blog/react-hooks
/blog/nodejs-basics
```

### **Important Properties**

`generateStaticParams()`:

- Runs during the build process to generate route parameters.
- Can return multiple dynamic parameter combinations.
- Can be used with nested dynamic routes.
- Is not called again during ordinary ISR revalidation.
- Can be combined with `dynamicParams` to control how ungenerated paths are handled.

### **Understanding dynamicParams**

By default, Next.js can handle dynamic parameter values that were not returned by `generateStaticParams()`.

You can change this behavior:

```
export const dynamicParams = false;
```

Now only paths returned by `generateStaticParams()` are allowed for that route.

For example:

Allowed:

```
/blog/react-hooks
/blog/nodejs-basics
```

Not allowed:

```
/blog/unknown-topic
```

This results in a not-found response for an ungenerated parameter.

### **generateStaticParams vs useParams**

| generateStaticParams                                | useParams                        |
| --------------------------------------------------- | -------------------------------- |
| Generates route parameters for build-time rendering | Reads current route parameters   |
| Used in Server Components and route configuration   | Client Component hook            |
| Executes during build                               | Executes during client rendering |
| Returns parameter combinations                      | Returns current parameter values |

Official documentation: [generateStaticParams](https://nextjs.org/docs/app/api-reference/functions/generate-static-params?utm_source=chatgpt.com)

# **13. Dynamic Routes with Database Integration**

Now let's understand the most important real-world use of dynamic routing.

Suppose you are building an e-commerce application.

Your MongoDB database contains products.

Example collection:

```
[
  {
    "_id": "101",
    "name": "Laptop",
    "price": 65000
  },
  {
    "_id": "102",
    "name": "Mobile",
    "price": 25000
  },
  {
    "_id": "103",
    "name": "Keyboard",
    "price": 2000
  }
]
```

You want to display each product separately.

Instead of creating individual pages, you create one dynamic route:

```
/products/[id]
```

### **Application Flow**

```
User Visits /products/101
          |
          v
Next.js Identifies [id]
          |
          v
Extracts ID = 101
          |
          v
Server Queries Database
          |
          v
Finds Product 101
          |
          v
Renders Product Details
          |
          v
Browser Displays Product
```

### **Example with MongoDB**

First, create a reusable database connection.

`src/lib/mongodb.ts`

```
import { MongoClient } from "mongodb";

const uri = process.env.MONGODB_URI!;

const client = new MongoClient(uri);

export default client;
```

For a real application, you should use a cached connection pattern rather than creating a new client for every request. We will study that in the database integration phase.

Now create:

`src/app/products/[id]/page.tsx`

```
import client from "@/lib/mongodb";
import { ObjectId } from "mongodb";
import { notFound } from "next/navigation";

type ProductPageProps = {
  params: Promise<{
    id: string;
  }>;
};

export default async function ProductPage({
  params,
}: ProductPageProps) {
  const { id } = await params;

  if (!ObjectId.isValid(id)) {
    notFound();
  }

  const db = client.db("ecommerce");

  const product = await db
    .collection("products")
    .findOne({
      _id: new ObjectId(id),
    });

  if (!product) {
    notFound();
  }

  return (
    <div>
      <h1>{product.name}</h1>

      <p>Price: ₹{product.price}</p>
    </div>
  );
}
```

### **Important Notes**

1. Database queries are performed on the server.
2. The product ID comes from the dynamic URL.
3. We validate the MongoDB ObjectId before querying.
4. `notFound()` handles invalid or missing products.
5. Database credentials remain on the server.

**Important:** MongoDB ObjectIds are normally 24-character hexadecimal strings. The illustrative `"101"` IDs in the earlier collection example are not valid ObjectIds. In an actual MongoDB collection, use valid ObjectIds or query using a separate string-based product ID.

# **14. Dynamic Routes with MySQL**

Next.js also works with MySQL.

Suppose your MySQL database has:

```
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255),
    price DECIMAL(10,2)
);
```

Sample records:

| id  | name     | price |
| --- | -------- | ----- |
| 101 | Laptop   | 65000 |
| 102 | Mobile   | 25000 |
| 103 | Keyboard | 2000  |

You can create the same dynamic route:

```
/products/[id]
```

### **Database Connection**

`src/lib/db.ts`

```
import mysql from "mysql2/promise";

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
});

export default pool;
```

### **Dynamic Product Page**

`src/app/products/[id]/page.tsx`

```
import db from "@/lib/db";
import { notFound } from "next/navigation";

type ProductPageProps = {
  params: Promise<{
    id: string;
  }>;
};

export default async function ProductPage({
  params,
}: ProductPageProps) {
  const { id } = await params;

  if (!/^\d+$/.test(id)) {
    notFound();
  }

  const [rows] = await db.execute(
    "SELECT id, name, price FROM products WHERE id = ?",
    [Number(id)]
  );

  const products = rows as {
    id: number;
    name: string;
    price: number;
  }[];

  const product = products[0];

  if (!product) {
    notFound();
  }

  return (
    <div>
      <h1>{product.name}</h1>

      <p>Price: ₹{product.price}</p>
    </div>
  );
}
```

### **Why Use Parameterized SQL?**

Notice:

```
"SELECT id, name, price FROM products WHERE id = ?"
```

And:

```
[Number(id)]
```

This uses parameterized SQL.

It helps protect against SQL injection attacks.

Never directly concatenate untrusted URL parameters into SQL queries.

### **MongoDB vs MySQL**

| MongoDB                     | MySQL                             |
| --------------------------- | --------------------------------- |
| Document database           | Relational database               |
| Uses collections            | Uses tables                       |
| Uses ObjectId or custom IDs | Commonly uses integer or UUID IDs |
| Uses MongoDB queries        | Uses SQL queries                  |
| `findOne()`                 | `SELECT`                          |

**Important:** Dynamic routing works the same way with both databases. Only the database query and ID validation logic differ.

# **15. What is generateMetadata()?**

`generateMetadata()` is a Next.js function used to generate page metadata dynamically.

It is particularly useful for dynamic pages such as:

- Product details.
- Blog articles.
- User profiles.
- News articles.
- Category pages.

### **Why Do We Need Dynamic Metadata?**

Suppose you have three products:

```
/products/laptop
/products/mobile
/products/keyboard
```

You want each page to have a different title and description.

For example:

| Page     | Title               |
| -------- | ------------------- |
| Laptop   | Buy Laptop Online   |
| Mobile   | Buy Mobile Online   |
| Keyboard | Buy Keyboard Online |

You don't want to hardcode the same metadata for every product.

Instead, generate it using route parameters and product data.

### **Example**

`src/app/products/[id]/page.tsx`

```
import type { Metadata } from "next";

type ProductPageProps = {
  params: Promise<{
    id: string;
  }>;
};

export async function generateMetadata({
  params,
}: ProductPageProps): Promise<Metadata> {
  const { id } = await params;

  return {
    title: `Product ${id} | My Store`,
    description: `Explore product ${id} at My Store.`,
  };
}

export default async function ProductPage({
  params,
}: ProductPageProps) {
  const { id } = await params;

  return (
    <div>
      <h1>Product Details</h1>

      <p>Product ID: {id}</p>
    </div>
  );
}
```

### **How Does It Work?**

For:

```
/products/101
```

Generated metadata:

```
<title>Product 101 | My Store</title>
<meta
  name="description"
  content="Explore product 101 at My Store."
/>
```

For:

```
/products/102
```

Generated metadata:

```
<title>Product 102 | My Store</title>
<meta
  name="description"
  content="Explore product 102 at My Store."
/>
```

### **Dynamic Metadata with Database Data**

You can also fetch the product from your database and generate metadata from its actual information.

Conceptually:

```
export async function generateMetadata({ params }) {
  const { id } = await params;

  const product = await getProductById(id);

  if (!product) {
    return {
      title: "Product Not Found",
    };
  }

  return {
    title: `${product.name} | My Store`,
    description: product.description,
  };
}
```

Here, `getProductById()` represents your server-side database function.

**Important:** In production applications, share or reuse the data-fetching function between `generateMetadata()` and the page. Next.js can memoize compatible identical fetch requests, avoiding unnecessary duplicate network requests.

We will cover metadata and SEO comprehensively in Phase 35.

Official documentation: [generateMetadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata?utm_source=chatgpt.com)

# **16. Understanding Dynamic Route Rendering**

A common misconception is that every dynamic route automatically uses SSR.

That is not correct.

A dynamic route is a URL pattern. Its rendering strategy is a separate concept.

For example:

```
/products/[id]
```

This route can be rendered using different approaches depending on your configuration and data requirements.

### **Example 1: Static Generation**

You can use:

```
export async function generateStaticParams() {
  return [
    { id: "101" },
    { id: "102" },
    { id: "103" },
  ];
}
```

Next.js can pre-render these product pages during the build.

### **Example 2: Dynamic Rendering**

If your page depends on request-time information, such as cookies or headers, Next.js can render it dynamically.

### **Example 3: Cached Data with Revalidation**

You can fetch product information with a revalidation interval.

```
const response = await fetch(
  "https://api.example.com/products",
  {
    next: {
      revalidate: 60,
    },
  }
);
```

This configures a revalidation interval for the cached data.

### **Comparison**

| Approach          | Description                                      |
| ----------------- | ------------------------------------------------ |
| Static generation | Generate selected dynamic pages at build time    |
| Dynamic rendering | Generate route output when a request requires it |
| Cached data       | Reuse previously fetched data                    |
| Revalidation      | Refresh cached data or route output              |

**Important:** In Next.js 16, rendering and caching are influenced by the Cache Components configuration. Do not assume that every `fetch()` is automatically cached or that every dynamic URL is automatically request-time rendered.

We will study this in detail in Phases 12–17 and 36.

# **17. Real-World Example: Complete Product Details Application**

Let's combine everything.

Imagine you are building a product listing application.

Your database contains:

```
[
  {
    id: "101",
    name: "Laptop",
    price: 65000,
    category: "Electronics"
  },
  {
    id: "102",
    name: "Mobile",
    price: 25000,
    category: "Electronics"
  },
  {
    id: "103",
    name: "Keyboard",
    price: 2000,
    category: "Accessories"
  }
]
```

### **Step 1: Create Product Listing Page**

Structure:

```
src/
└── app/
    └── products/
        ├── page.tsx
        │
        └── [id]/
            └── page.tsx
```

`products/page.tsx`

```
import Link from "next/link";

const products = [
  {
    id: "101",
    name: "Laptop",
    price: 65000,
  },
  {
    id: "102",
    name: "Mobile",
    price: 25000,
  },
  {
    id: "103",
    name: "Keyboard",
    price: 2000,
  },
];

export default function ProductsPage() {
  return (
    <div>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product.id}>
          <h2>{product.name}</h2>

          <p>Price: ₹{product.price}</p>

          <Link href={`/products/${product.id}`}>
            View Details
          </Link>
        </div>
      ))}
    </div>
  );
}
```

### **Step 2: Create Dynamic Product Page**

`products/[id]/page.tsx`

```
import { notFound } from "next/navigation";

const products = [
  {
    id: "101",
    name: "Laptop",
    price: 65000,
    category: "Electronics",
  },
  {
    id: "102",
    name: "Mobile",
    price: 25000,
    category: "Electronics",
  },
  {
    id: "103",
    name: "Keyboard",
    price: 2000,
    category: "Accessories",
  },
];

type ProductPageProps = {
  params: Promise<{
    id: string;
  }>;
};

export default async function ProductPage({
  params,
}: ProductPageProps) {
  const { id } = await params;

  const product = products.find(
    (item) => item.id === id
  );

  if (!product) {
    notFound();
  }

  return (
    <div>
      <h1>{product.name}</h1>

      <p>Price: ₹{product.price}</p>

      <p>Category: {product.category}</p>
    </div>
  );
}
```

### **Step 3: Test the Application**

Open:

```
http://localhost:3000/products
```

You will see three products.

Click View Details for Laptop.

URL:

```
/products/101
```

Product page:

```
Laptop

Price: ₹65000

Category: Electronics
```

Click View Details for Mobile.

URL:

```
/products/102
```

Product page:

```
Mobile

Price: ₹25000

Category: Electronics
```

### **Step 4: Test Invalid Product ID**

Visit:

```
/products/999
```

Since product 999 does not exist, Next.js displays the not-found page.

### **What Did We Implement?**

- Static product listing route.
- Dynamic product details route.
- URL parameter extraction.
- Data lookup.
- Conditional not-found handling.
- Reusable page component.

This is the fundamental architecture used in many e-commerce applications.

# **18. Important Differences Between Dynamic Routing Functions**

Let's revise the functions and conventions we learned.

| Feature                  | Purpose                                                                  |
| ------------------------ | ------------------------------------------------------------------------ |
| `[id]`                   | Defines one dynamic segment                                              |
| `[...slug]`              | Defines a required catch-all route                                       |
| `[[...slug]]`            | Defines an optional catch-all route                                      |
| `params`                 | Provides route parameter values                                          |
| `useParams()`            | Reads route parameters in Client Components                              |
| `generateStaticParams()` | Generates dynamic route parameters for build-time rendering              |
| `generateMetadata()`     | Generates dynamic metadata                                               |
| `dynamicParams`          | Controls handling of parameters not returned by `generateStaticParams()` |
| `notFound()`             | Displays the not-found UI                                                |

# **19. Common Dynamic Routing Mistakes**

### **Mistake 1: Forgetting to Await Params**

Incorrect in Next.js 16:

```
export default function ProductPage({ params }) {
  return <h1>{params.id}</h1>;
}
```

Correct:

```
export default async function ProductPage({ params }) {
  const { id } = await params;

  return <h1>{id}</h1>;
}
```

### **Mistake 2: Treating Params as Numbers**

Incorrect assumption:

```
const id: number = params.id;
```

Route parameters are strings.

Correct:

```
const id = Number(params.id);
```

Validate the value before using it.

### **Mistake 3: Using useParams Without Client Component**

Incorrect:

```
import { useParams } from "next/navigation";

export default function ProductPage() {
  const params = useParams();
  return <h1>{params.id}</h1>;
}
```

Correct:

```
"use client";

import { useParams } from "next/navigation";

export default function ProductPage() {
  const params = useParams<{ id: string }>();

  return <h1>{params.id}</h1>;
}
```

### **Mistake 4: Using Catch-All Parameters as Strings**

For:

```
[...slug]
```

The parameter is an array.

Correct:

```
const { slug } = await params;

const path = slug.join("/");
```

### **Mistake 5: Forgetting Invalid Database IDs**

Before querying a database, validate the URL parameter.

For example:

```
if (!/^\d+$/.test(id)) {
  notFound();
}
```

For MongoDB ObjectIds, use the appropriate ObjectId validation.

### **Mistake 6: Confusing Dynamic Routes with Dynamic Rendering**

A dynamic URL does not automatically mean request-time SSR.

The route structure and rendering strategy are separate concepts.

# **20. Phase 5 – Interview Questions**

## **Basic Questions**

**Q1. What are Dynamic Routes in Next.js?**

Dynamic Routes allow us to create reusable pages that handle different URL parameter values.

**Q2. How do we create a dynamic route?**

By using square brackets in the folder name.

Example:

```
app/products/[id]/page.tsx
```

**Q3. What is params in Next.js?**

`params` is an object containing values extracted from dynamic URL segments.

**Q4. What is the data type of route parameters?**

Route parameters are strings. Catch-all parameters are arrays of strings.

**Q5. Why do we use await params in Next.js 16?**

Because route parameters are provided asynchronously in the modern App Router.

**Q6. Can one dynamic route handle multiple URLs?**

Yes. For example, `/products/101` and `/products/102` can use the same dynamic page component.

## **Intermediate Questions**

**Q7. What is a Catch-All Route?**

A route that captures multiple URL segments using `[...slug]`.

**Q8. What is an Optional Catch-All Route?**

A route that captures multiple URL segments but also matches the parent route using `[[...slug]]`.

**Q9. What is the difference between&#x20;**`[id]`**&#x20;and&#x20;**`[...slug]`**?**

`[id]` captures one URL segment, whereas `[...slug]` captures one or more URL segments.

**Q10. What is generateStaticParams()?**

It generates dynamic route parameters during the build process so Next.js can pre-render selected pages.

**Q11. What is generateMetadata()?**

It generates page metadata dynamically using information such as route parameters and database records.

**Q12. What is dynamicParams?**

It controls whether dynamic route values not returned by `generateStaticParams()` can be handled.

**Q13. What is the difference between useParams() and params?**

`params` is passed to Server Component pages and is asynchronous, whereas `useParams()` is a Client Component hook.

**Q14. Can dynamic routes connect directly to MongoDB?**

Yes. Server Components can execute server-side database queries.

**Q15. Can dynamic routes connect to MySQL?**

Yes. They can use MySQL drivers or ORMs from server-side code.

## **Advanced Questions**

**Q16. Can dynamic routes be statically generated?**

Yes. We can use `generateStaticParams()` to pre-render selected dynamic routes.

**Q17. What happens if a requested product does not exist?**

We can call `notFound()` to display the appropriate not-found UI.

**Q18. What is the route matching priority?**

Static routes have priority over dynamic routes, and dynamic routes have priority over catch-all routes when matching applicable paths.

**Q19. Can we use multiple dynamic segments in a single route?**

Yes.

Example:

```
/users/[userId]/orders/[orderId]
```

**Q20. What is the difference between static routes and static rendering?**

Static routes describe predefined URL paths, whereas static rendering describes when the page output is generated.

**Q21. Does every dynamic route use SSR?**

No. Dynamic routes can also be statically generated.

**Q22. Can generateStaticParams() run again during ISR revalidation?**

No. It runs during the build process to generate route parameters, not during ordinary ISR revalidation.

**Q23. How do we prevent ungenerated dynamic paths from being accessed?**

Use:

```
export const dynamicParams = false;
```

alongside `generateStaticParams()`.

**Q24. Can we use database data inside generateMetadata()?**

Yes. It can fetch data on the server and generate metadata from database records.

**Q25. Why should we validate dynamic route parameters?**

Because URL parameters are user-controlled input. Validation helps prevent invalid queries, unexpected errors, and certain security vulnerabilities.

# **21. Practical Assignment – Phase 5**

Now implement a small dynamic application.

Hands-on project

**Project: Dynamic Blog Application**

### **Task 1: Create This Folder Structure**

```
src/
└── app/
    │
    ├── blog/
    │   ├── page.tsx
    │   │
    │   └── [slug]/
    │       └── page.tsx
    │
    └── docs/
        └── [...slug]/
            └── page.tsx
```

### **Task 2: Create Blog Listing Page**

`src/app/blog/page.tsx`

Create a list containing at least five blog articles.

Each article should have:

- ID
- Title
- Slug
- Description

Use the `Link` component to navigate to the individual blog page.

### **Task 3: Create Dynamic Blog Page**

`src/app/blog/[slug]/page.tsx`

Requirements:

- Access the slug using `params`.
- Find the corresponding blog.
- Display its title.
- Display its description.
- Display a not-found page if the blog does not exist.

### **Task 4: Implement Dynamic Metadata**

Use `generateMetadata()` to generate:

- Dynamic title.
- Dynamic description.

### **Task 5: Implement Catch-All Documentation**

Use:

```
/docs/[...slug]
```

Display all received URL segments.

Test:

```
/docs/react
/docs/react/hooks
/docs/react/hooks/useState
```

### **Task 6: Implement Optional Catch-All**

Change the folder structure to:

```
/docs/[[...slug]]
```

Verify that both `/docs` and nested documentation URLs work.

### **Task 7: Implement generateStaticParams()**

Return at least three blog slugs.

Run:

```
npm run build
```

Observe how Next.js handles the generated routes.

### **Task 8: Database Integration – Optional Advanced Exercise**

Create a product details page using either:

- MongoDB
- MySQL

Requirements:

1. Accept a product ID through the URL.
2. Validate the ID.
3. Fetch the product from the database.
4. Display product details.
5. Display not-found UI for missing products.

# **22. Phase 5 – Quick Revision Notes**

| Concept                         | Syntax / Function        |
| ------------------------------- | ------------------------ |
| Dynamic Route                   | `[id]`                   |
| Catch-All Route                 | `[...slug]`              |
| Optional Catch-All              | `[[...slug]]`            |
| Route Parameters                | `params`                 |
| Client Route Parameters         | `useParams()`            |
| Static Route Generation         | `generateStaticParams()` |
| Dynamic Metadata                | `generateMetadata()`     |
| Disable Unlisted Dynamic Params | `dynamicParams = false`  |
| Not Found Handling              | `notFound()`             |
| Multiple Dynamic Segments       | `[userId]/[orderId]`     |

### **Remember These Four Examples**

```
/products/101
```

One dynamic parameter:

```
[id] = "101"
```

```
/users/25/orders/500
```

Two dynamic parameters:

```
{
  userId: "25",
  orderId: "500"
}
```

```
/docs/react/hooks
```

Catch-all parameter:

```
{
  slug: ["react", "hooks"]
}
```

```
/docs
```

Optional catch-all parameter:

```
{
  slug: undefined
}
```

# **23. Phase 5 Completion Checklist**

| Topic                          | Status   |
| ------------------------------ | -------- |
| Introduction to Dynamic Routes | Covered  |
| Dynamic Route Syntax           | Covered  |
| Route Parameters               | Covered  |
| Server Component Params        | Covered  |
| Client Component useParams     | Covered  |
| Multiple Dynamic Segments      | Covered  |
| Catch-All Routes               | Covered  |
| Optional Catch-All Routes      | Covered  |
| Dynamic Route Matching         | Covered  |
| Static vs Dynamic Routes       | Covered  |
| generateStaticParams           | Covered  |
| dynamicParams                  | Covered  |
| MongoDB Integration            | Covered  |
| MySQL Integration              | Covered  |
| generateMetadata               | Covered  |
| Dynamic Route Rendering        | Covered  |
| Real-World Product Application | Covered  |
| Common Mistakes                | Covered  |
| Interview Questions            | Covered  |
| Practical Assignment           | Assigned |

# **Next: Phase 6 – Nested Routes and Layouts**

In Phase 6, we will deeply understand how to build complex application layouts.

Topics:

1. What are Nested Routes?
2. What are Layouts?
3. Root Layout.
4. Nested Layouts.
5. Shared Layouts.
6. Layout Hierarchy.
7. How children Props Work.
8. Layout Persistence.
9. Nested Layout Rendering.
10. Layout vs Page.
11. Layout vs Template.
12. Multiple Layouts.
13. Dashboard Layout Architecture.
14. Authentication Layout.
15. Route Groups with Layouts.
16. Multiple Root Layouts.
17. Conditional Layouts.
18. Real-World E-commerce Layout.
19. Common Layout Mistakes.
20. Interview Questions.
21. Practical Assignment.

**Phase 5 is complete.**

When ready, say **"OK, Phase 6"**.