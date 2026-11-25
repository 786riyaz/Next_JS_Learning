# **Phase 17 – Caching, Revalidation and Performance Optimization**



Next.js 16.x

App Router

TypeScript

Advanced



## **1. Learning Objectives**

By the end of this phase, you will understand:

1. What caching is and why applications need it.
2. The difference between browser caching and server-side caching.
3. Next.js caching architecture and its major caching mechanisms.
4. Request memoization and React `cache()`.
5. The modern Cache Components model using `"use cache"`.
6. `cacheLife()` and `cacheTag()`.
7. `revalidateTag()`, `updateTag()` and `revalidatePath()`.
8. Time-based and on-demand cache revalidation.
9. Caching database queries with MongoDB and MySQL.
10. Cache invalidation strategies.
11. Performance optimization techniques for Next.js applications.
12. How to identify and avoid common caching mistakes.

**Important version distinction:** Next.js 16 supports two caching approaches. The newer Cache Components model is enabled with `cacheComponents: true` and uses directives such as `"use cache"`. Applications using the previous caching model continue to use options such as `fetch` cache settings and route segment configuration. We will learn both, and distinguish them instead of mixing their APIs.&#x20;

## **2. What Is Caching?**

Caching is the process of storing previously computed or retrieved data so that it can be reused instead of performing the same expensive operation repeatedly.

Let's understand with a real-world example.

Suppose your Next.js application has a product listing page.

Every time a user opens:

```
/products
```

Your application performs:

1. A database connection.
2. A database query.
3. Data processing.
4. Server-side rendering.
5. Sending the response to the browser.

If 1,000 users open the same page, performing all these operations repeatedly can increase database load and response time.

Instead, you can cache the product data or rendered output.

**Without caching**

**1,000 user requests**

**1,000 database queries**

**Higher database load**

**With caching**

**1,000 user requests**

**Cache lookup**

Cache hit

**Reuse cached result**

Database query may be needed only when the cache is missing or invalidated.

### **Benefits of caching**

- Faster responses.
- Reduced database queries.
- Lower server workload.
- Better scalability.
- Reduced API calls.
- Potentially lower infrastructure costs.
- Improved user experience.

### **What is a cache hit and cache miss?**

| Term               | Meaning                                          |
| ------------------ | ------------------------------------------------ |
| Cache hit          | Requested data is found in the cache             |
| Cache miss         | Requested data is not found in the cache         |
| Cache entry        | Stored cached data                               |
| Cache key          | Identifier used to locate a cache entry          |
| Cache invalidation | Making an existing cache entry stale or unusable |
| Cache revalidation | Refreshing or marking cached data for refresh    |

## **3. Understanding the Different Types of Caching**

Caching can happen at different levels of your application.

### **3.1 Browser caching**

The browser can store resources locally.

Examples:

- Images
- CSS files
- JavaScript files
- Fonts
- API responses, depending on HTTP cache headers and application behavior

Suppose your website uses:

```
/public/logo.png
```

When a browser downloads the image, it may reuse the cached version on subsequent visits, depending on the caching headers.

This reduces unnecessary network requests.

### **3.2 CDN caching**

A CDN (Content Delivery Network) stores copies of resources on servers distributed across different geographic locations.

For example, a user in India can receive a cached image from a nearby CDN location instead of downloading it from a server in another country.

Examples:

- Vercel CDN
- Cloudflare CDN
- AWS CloudFront

### **3.3 Server-side data caching**

Your server stores the results of expensive operations.

For example:

```
const products = await Product.find({});
```

Instead of querying MongoDB for every request, your application can reuse cached results.

### **3.4 Full-page or route output caching**

The rendered output of a page can be cached so that the server does not need to repeat all rendering work for every visitor.

This is especially useful for pages whose content changes infrequently.

### **3.5 Request memoization**

The same request may be made more than once during a single server render.

Request memoization allows Next.js to reuse the result of matching `fetch()` requests within that rendering process.

### **Comparison**

| Cache type          | What it stores                    | Main benefit               |
| ------------------- | --------------------------------- | -------------------------- |
| Browser cache       | Browser resources and responses   | Faster repeat visits       |
| CDN cache           | Copies of resources or responses  | Faster delivery near users |
| Data cache          | Retrieved or computed data        | Fewer expensive operations |
| Route output cache  | Rendered page output              | Less repeated rendering    |
| Request memoization | Matching requests during a render | Avoid duplicate work       |

**Important:** These mechanisms have different lifetimes and invalidation rules. Do not assume that clearing one cache clears all the others.

## **4. Next.js Caching Architecture**

Before writing code, understand the two caching models available in Next.js 16.

**Next.js 16 caching models**

**Model A: Cache Components**

Modern approach

- Enable `cacheComponents: true`.
- Use `"use cache"`.
- Control lifetime with `cacheLife()`.
- Associate entries with `cacheTag()`.
- Revalidate using the modern APIs.

**Model B: Previous caching model**

Existing applications

- `fetch()` cache options.
- `next.revalidate`.
- `next.tags`.
- Route segment configuration.
- `unstable_cache()` for non-fetch data.

The modern model is the main focus of this phase. The previous model is also important because you may encounter it in existing projects and older tutorials.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2

### **4.1 Enable Cache Components**

Open your `next.config.ts` file:

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

Restart your development server after changing the configuration.

```
npm run dev
```

This enables the Cache Components model.

**What changes when you enable it?**

- Uncached data and runtime-dependent work are not automatically treated as cached.
- You explicitly choose what to cache using `"use cache"`.
- You control cache lifetimes using `cacheLife()`.
- You can invalidate cached data using tags and paths.

This makes caching more explicit and easier to reason about.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **5. The&#x20;**`"use cache"`**&#x20;Directive**

This is the central concept of the modern caching model.

The `"use cache"` directive tells Next.js to cache the output of a function or component.

### **5.1 Basic example**

```
// src/lib/products.ts

export async function getProducts() {
  "use cache";

  const response = await fetch(
    "https://api.example.com/products"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  return response.json();
}
```

What happens?

1. A request calls `getProducts()`.
2. Next.js checks whether a matching cached result exists.
3. If a valid cached result exists, it can reuse it.
4. Otherwise, the function executes and its result is cached.

### **5.2 Use the cached function in a Server Component**

```
// app/products/page.tsx

import { getProducts } from "@/lib/products";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product: any) => (
        <div key={product.id}>
          {product.name}
        </div>
      ))}
    </main>
  );
}
```

Now the product data can be reused through the cache rather than fetched from the external API on every request.

### **5.3 Cache an entire component**

You can also cache a component.

```
// app/components/product-list.tsx

import { cacheLife } from "next/cache";

export async function ProductList() {
  "use cache";

  cacheLife("hours");

  const response = await fetch(
    "https://api.example.com/products"
  );

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

The function's output is cached according to the configured cache lifetime.

### **5.4 What determines a cache entry?**

Next.js uses the cached function or component identity and its inputs to distinguish cache entries.

For example:

```
export async function getProduct(id: string) {
  "use cache";

  const response = await fetch(
    `https://api.example.com/products/${id}`
  );

  return response.json();
}
```

Calls with different IDs produce different cached results:

```
getProduct("101");
getProduct("102");
```

The cached result for product `101` is not the same entry as the result for product `102`.

### **Important security note**

Cached functions can be shared across users when their cache keys are the same.

Do not cache personalized or permission-dependent results without designing the cache key and authorization behavior correctly.

For example, a user dashboard that returns private information should not accidentally share one user's cached data with another user.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **6. Understanding&#x20;**`cacheLife()`

`cacheLife()` controls how long a cached result remains usable and when it should be refreshed.

Import it from:

```
import { cacheLife } from "next/cache";
```

### **6.1 Basic example**

```
import { cacheLife } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheLife("hours");

  const response = await fetch(
    "https://api.example.com/products"
  );

  return response.json();
}
```

Here, `"hours"` is a built-in cache profile.

### **6.2 Built-in cache profiles**

Next.js provides profiles such as:

| Profile   | Typical use                                    |
| --------- | ---------------------------------------------- |
| `seconds` | Frequently changing information                |
| `minutes` | Short-lived content                            |
| `hours`   | Product catalogs or moderately changing data   |
| `days`    | Articles and relatively stable content         |
| `weeks`   | Rarely changing content                        |
| `max`     | Long-lived content with on-demand revalidation |

These profiles define different cache timing behavior. Exact durations are controlled by the profile configuration and Next.js defaults. Avoid assuming that every profile simply means "expire after this many hours or days."

### **6.3 Custom cache lifetime**

You can define the lifetime explicitly.

```
import { cacheLife } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheLife({
    stale: 60,
    revalidate: 300,
    expire: 3600,
  });

  const response = await fetch(
    "https://api.example.com/products"
  );

  return response.json();
}
```

Let's understand the three properties.

**Three cache lifetime properties**

`stale: 60`

# **60 seconds**

How long the client-side router can use cached data without checking the server.

`revalidate: 300`

# **5 minutes**

After this interval, a subsequent request can trigger background regeneration while cached content is served.

`expire: 3600`

# **1 hour**

Maximum cache lifetime. After expiration, the next request must wait for fresh content.

### **6.4 Understand the difference**

| Property     | Controls                                |
| ------------ | --------------------------------------- |
| `stale`      | Client-side router cache freshness      |
| `revalidate` | Server-side background refresh interval |
| `expire`     | Maximum server cache lifetime           |

When setting both `revalidate` and `expire`, the expiry must be longer than the revalidation interval.

### **6.5 Example timeline**

Suppose:

```
cacheLife({
  stale: 60,
  revalidate: 300,
  expire: 3600,
});
```

A simplified interpretation:

- During the first 60 seconds, the client router can reuse its cached result without checking the server.
- After the server revalidation interval, a later request can receive stale content while fresh content is generated in the background.
- After the expiry limit, the next request waits for fresh content.

The exact behavior depends on which cache layer is being consulted. `stale` is not the same as the HTTP `Cache-Control` header.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **7. Understanding&#x20;**`cacheTag()`

`cacheTag()` allows you to attach a label to cached data.

Why is this useful?

Imagine your application has:

```
/products
/products/101
/products/102
/products/103
```

All these pages use product information.

Suppose an administrator updates product `101`.

You want to invalidate the cache associated with that product without unnecessarily invalidating unrelated data.

This is where cache tags help.

### **7.1 Basic example**

```
// src/lib/products.ts

import { cacheTag } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheTag("products");

  const response = await fetch(
    "https://api.example.com/products"
  );

  return response.json();
}
```

The cached result is associated with the tag:

```
products
```

You can now revalidate it using that tag.

### **7.2 Multiple tags**

```
import { cacheTag } from "next/cache";

export async function getProduct(id: string) {
  "use cache";

  cacheTag("products");
  cacheTag(`product-${id}`);

  const response = await fetch(
    `https://api.example.com/products/${id}`
  );

  return response.json();
}
```

This cached data has two tags:

```
products
product-101
```

You can invalidate:

- All data tagged `products`.
- Only the data tagged `product-101`.

### **7.3 Cache tags with a database query**

```
// src/lib/products.ts

import "server-only";

import { cacheTag } from "next/cache";
import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function getProduct(id: string) {
  "use cache";

  cacheTag("products");
  cacheTag(`product-${id}`);

  await connectDB();

  return Product.findById(id).lean();
}
```

Now the product query is cached and tagged.

**Important:** `cacheTag()` must be used within a cached scope. Enable Cache Components before using this approach.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **8. Revalidation: Updating Cached Data**

Caching improves performance, but cached data can become outdated.

Suppose your database contains:

```
Product: Gaming Laptop
Price: ₹75,000
```

An administrator updates the price:

```
Product: Gaming Laptop
Price: ₹70,000
```

But the cache still contains:

```
₹75,000
```

This is called stale data.

**Revalidation** is the process of refreshing or invalidating cached data so that subsequent requests can receive updated information.

Next.js provides three particularly important APIs:

1. `revalidateTag()`
2. `updateTag()`
3. `revalidatePath()`

## **9.&#x20;**`revalidateTag()`

`revalidateTag()` marks tagged cached data as stale so it can be refreshed when requested again.

Import:

```
import { revalidateTag } from "next/cache";
```

### **9.1 Basic example**

```
revalidateTag("products", "max");
```

What happens?

1. Next.js marks cached entries with the `products` tag as stale.
2. A later request uses the stale content while fresh data is fetched in the background.
3. The cache is updated with the fresh result.

This is called **stale-while-revalidate (SWR)**.

### **9.2 Real-world example: Updating a product**

```
// src/app/actions.ts

"use server";

import { revalidateTag } from "next/cache";
import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function updateProduct(
  id: string,
  price: number
) {
  await connectDB();

  await Product.findByIdAndUpdate(id, {
    price,
  });

  revalidateTag("products", "max");
  revalidateTag(`product-${id}`, "max");

  return {
    success: true,
  };
}
```

The relevant cached data is now marked stale.

### **9.3 When should you use it?**

Use `revalidateTag()` when:

- A blog post is updated.
- A product catalog changes.
- Documentation changes.
- Content can tolerate a short period of stale data.
- You want multiple pages using the same data to refresh together.

### **Important Next.js 16 change**

In Next.js 16, use the two-argument form:

```
revalidateTag("products", "max");
```

The old single-argument form:

```
revalidateTag("products");
```

is deprecated.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **10.&#x20;**`updateTag()`

`updateTag()` is designed for situations where the user should immediately see their own changes.

This is called **read-your-own-writes consistency**.

Suppose a user edits their profile:

```
Old name: Riyaz
New name: Riyaz Khan
```

After submitting the form, the user expects to see the new name immediately.

Serving stale cached profile data would create a confusing experience.

Use `updateTag()`.

### **10.1 Example**

```
// src/app/actions.ts

"use server";

import { updateTag } from "next/cache";
import { connectDB } from "@/lib/db";
import User from "@/models/User";

export async function updateUserName(
  userId: string,
  name: string
) {
  await connectDB();

  await User.findByIdAndUpdate(userId, {
    name,
  });

  updateTag(`user-${userId}`);

  return {
    success: true,
  };
}
```

The tagged cache entry is expired immediately, so the next read waits for fresh data.

### **10.2&#x20;**`updateTag()`**&#x20;vs&#x20;**`revalidateTag()`

| Feature              | `updateTag()`                                      | `revalidateTag()`                          |
| -------------------- | -------------------------------------------------- | ------------------------------------------ |
| Purpose              | Immediate freshness                                | Background refresh                         |
| Stale content served | No, after expiration                               | Yes, with `"max"`                          |
| Server Action        | Yes                                                | Yes                                        |
| Route Handler        | No                                                 | Yes                                        |
| Best use             | Profile updates, user edits, shopping cart changes | Blog posts, product catalog, documentation |

Remember this distinction

- User updates their profile and expects to see the new name immediately → `updateTag()`.
- Administrator updates a blog article and readers can temporarily see the previous version → `revalidateTag("posts", "max")`.

`updateTag()` is only available in Server Actions.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **11.&#x20;**`revalidatePath()`

`revalidatePath()` invalidates cached data associated with a specific route path.

Import:

```
import { revalidatePath } from "next/cache";
```

### **11.1 Basic example**

```
revalidatePath("/products");
```

This invalidates the cached output for the `/products` route.

### **11.2 Revalidate a dynamic route**

Suppose your routes are:

```
/products/101
/products/102
/products/103
```

You can invalidate one specific page:

```
revalidatePath("/products/101");
```

This targets the page for product `101`.

### **11.3 Revalidate a route pattern**

You can also invalidate a dynamic route pattern:

```
revalidatePath("/products/[id]", "page");
```

This targets pages matching that route pattern.

### **11.4 Revalidate a layout**

```
revalidatePath("/products", "layout");
```

This invalidates the layout at that segment and its nested layouts and pages.

### **11.5 Complete Server Action example**

```
// src/app/actions.ts

"use server";

import { revalidatePath } from "next/cache";
import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function updateProduct(
  id: string,
  price: number
) {
  await connectDB();

  await Product.findByIdAndUpdate(id, {
    price,
  });

  revalidatePath("/products");
  revalidatePath(`/products/${id}`);

  return {
    success: true,
  };
}
```

### **11.6&#x20;**`revalidatePath()`**&#x20;vs tag revalidation**

| `revalidatePath()`                                                    | `revalidateTag()`                          |
| --------------------------------------------------------------------- | ------------------------------------------ |
| Targets a route or layout                                             | Targets tagged cached data                 |
| Useful for specific pages                                             | Useful for shared data                     |
| Does not automatically invalidate every other page using the same tag | Can affect all entries carrying that tag   |
| Works in Server Actions and Route Handlers                            | Works in Server Actions and Route Handlers |

For example, if a product appears on the homepage, product listing, and product detail page, a shared product cache tag can be more precise than invalidating individual routes.

Next.js recommends tag-based revalidation where it fits the data model.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **12. Time-Based vs On-Demand Revalidation**

Next.js supports two main revalidation strategies.

### **12.1 Time-based revalidation**

The cache is refreshed according to a configured time interval.

Example:

```
import { cacheLife } from "next/cache";

export async function getNews() {
  "use cache";

  cacheLife({
    stale: 60,
    revalidate: 300,
    expire: 3600,
  });

  const response = await fetch(
    "https://api.example.com/news"
  );

  return response.json();
}
```

This is useful when:

- News updates every few minutes.
- Product information changes occasionally.
- Blog content does not need to be updated immediately.
- You want to control server load.

### **12.2 On-demand revalidation**

You manually invalidate cached data after a particular event.

Example:

```
import { revalidateTag } from "next/cache";

revalidateTag("news", "max");
```

This is useful when:

- An administrator publishes a new article.
- A product is updated.
- A category is deleted.
- Content changes through a CMS webhook.

### **Comparison**

| Time-based                        | On-demand                                                   |
| --------------------------------- | ----------------------------------------------------------- |
| Refresh is based on time          | Refresh is triggered by an event                            |
| Uses `cacheLife()`                | Uses `revalidateTag()`, `updateTag()` or `revalidatePath()` |
| Good for periodic updates         | Good for content mutations                                  |
| Does not require a mutation event | Requires an explicit invalidation call                      |

**Best practice:** Use time-based caching as a sensible fallback, and on-demand revalidation when you know exactly when data changes.

## **13. Caching with the Previous Next.js Model**

You will encounter this approach in many existing Next.js projects.

It uses extended `fetch()` options rather than the `"use cache"` directive.

This model remains relevant for applications that have not enabled Cache Components.

### **13.1&#x20;**`cache: "force-cache"`

```
const response = await fetch(
  "https://api.example.com/products",
  {
    cache: "force-cache",
  }
);
```

This instructs Next.js to look for a matching persistent Data Cache entry and reuse it when available and fresh.

### **13.2&#x20;**`cache: "no-store"`

```
const response = await fetch(
  "https://api.example.com/products",
  {
    cache: "no-store",
  }
);
```

This opts out of the persistent Data Cache for this request.

Use it when the data needs to be fetched from the source on each server request.

Examples:

- Frequently changing inventory.
- Live account information.
- Sensitive user-specific data.
- Data that must not be served from a shared cache.

### **13.3&#x20;**`next.revalidate`

You can define how frequently cached data should be revalidated.

```
const response = await fetch(
  "https://api.example.com/products",
  {
    next: {
      revalidate: 300,
    },
  }
);
```

The value is in seconds.

Here:

```
300 seconds = 5 minutes
```

### **13.4 Comparison**

| Option                 | Behavior                                             |
| ---------------------- | ---------------------------------------------------- |
| `cache: "force-cache"` | Reuse persistent cached data                         |
| `cache: "no-store"`    | Do not use the persistent Data Cache                 |
| `next.revalidate: 300` | Revalidate cached data after the configured interval |
| `next.tags`            | Attach tags for on-demand invalidation               |

### **13.5 Tagging&#x20;**`fetch()`**&#x20;requests**

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

Then invalidate:

```
import { revalidateTag } from "next/cache";

revalidateTag("products", "max");
```

This is the previous model's equivalent of associating a cached data request with a tag.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **14. React&#x20;**`cache()`**&#x20;vs Next.js&#x20;**`"use cache"`

This distinction is extremely important.

Both involve caching, but they do not serve the same purpose.

### **14.1 React&#x20;**`cache()`

React provides a function called `cache()`.

It is useful for memoizing expensive function calls during a server render.

Example:

```
// src/lib/products.ts

import { cache } from "react";

export const getProduct = cache(
  async (id: string) => {
    console.log("Database query executed");

    return Product.findById(id).lean();
  }
);
```

Suppose your page and metadata generation both call:

```
getProduct("101");
```

Within the same React server render, React can reuse the memoized result instead of executing the function again.

### **14.2 Important: React&#x20;**`cache()`**&#x20;is request-scoped**

React's `cache()` does not provide persistent cross-request caching.

For example:

```
Request 1
  getProduct("101") → Execute query
  getProduct("101") → Reuse result

Request 2
  getProduct("101") → Execute query again
```

Each request has its own memoization scope.

### **14.3 Next.js&#x20;**`"use cache"`

```
import { cacheLife } from "next/cache";

export async function getProduct(id: string) {
  "use cache";

  cacheLife("hours");

  return Product.findById(id).lean();
}
```

This uses Next.js caching and can reuse the result across requests according to its cache configuration.

### **Comparison**

| React `cache()`                                       | Next.js `"use cache"`                   |
| ----------------------------------------------------- | --------------------------------------- |
| React API                                             | Next.js directive                       |
| Request-scoped memoization                            | Persistent Next.js cache                |
| Deduplicates repeated function calls during rendering | Caches function or component output     |
| No `cacheLife()` configuration                        | Supports `cacheLife()`                  |
| No `cacheTag()` integration                           | Supports `cacheTag()`                   |
| Useful for duplicate database reads in one render     | Useful for sharing data across requests |

### **When should you use them?**

- Avoid repeated database queries within one render → React `cache()`.
- Reuse database results across multiple requests → Next.js `"use cache"`.
- Need to invalidate cached results after a mutation → Next.js cache tags and revalidation APIs.

React's `cache()` is especially useful for ORM and database operations that do not use `fetch()`.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **15. Caching MongoDB Queries with Cache Components**

Let's implement a practical example using MongoDB.

Imagine your e-commerce application has a products collection.

### **15.1 Project structure**

```
src/
├── app/
│   ├── products/
│   │   ├── page.tsx
│   │   └── [id]/
│   │       └── page.tsx
│   │
│   └── actions.ts
│
├── lib/
│   ├── db.ts
│   └── products.ts
│
└── models/
    └── Product.ts
```

### **15.2 Enable Cache Components**

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

### **15.3 Create a cached product function**

```
// src/lib/products.ts

import "server-only";

import { cacheLife, cacheTag } from "next/cache";

import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function getProducts() {
  "use cache";

  cacheLife("hours");
  cacheTag("products");

  await connectDB();

  return Product.find({
    isActive: true,
  })
    .select("name price image")
    .lean();
}
```

This function:

1. Connects to MongoDB.
2. Fetches active products.
3. Caches the result.
4. Uses an `hours` cache profile.
5. Associates the result with the `products` tag.

### **15.4 Use it in a Server Component**

```
// src/app/products/page.tsx

import { getProducts } from "@/lib/products";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      <div>
        {products.map((product: any) => (
          <div key={product._id.toString()}>
            <h2>{product.name}</h2>

            <p>₹{product.price}</p>
          </div>
        ))}
      </div>
    </main>
  );
}
```

### **15.5 Update product and invalidate the cache**

```
// src/app/actions.ts

"use server";

import { revalidateTag } from "next/cache";

import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function updateProductPrice(
  id: string,
  price: number
) {
  await connectDB();

  await Product.findByIdAndUpdate(id, {
    price,
  });

  revalidateTag("products", "max");

  return {
    success: true,
  };
}
```

The product list is now marked stale and can be refreshed on its next use.

**Security note:** This example caches a public catalog. Do not use the same pattern for user-specific query results without carefully handling user identity, authorization, and cache keys. Never assume that adding a user ID to a query alone makes every cache design safe.

## **16. What About Cookies, Headers and User-Specific Data?**

Some data depends on the current request.

For example:

```
const session = await cookies();
```

Or:

```
const headersList = await headers();
```

These APIs access request-specific information.

You should not read request-specific values directly inside a shared cached scope.

Instead, read them outside the cached function and pass only the necessary values as arguments when appropriate.

### **Example**

```
// app/dashboard/page.tsx

import { cookies } from "next/headers";
import { getDashboardData } from "@/lib/dashboard";

export default async function DashboardPage() {
  const cookieStore = await cookies();

  const userId =
    cookieStore.get("userId")?.value;

  if (!userId) {
    return <p>Please sign in.</p>;
  }

  const data = await getDashboardData(userId);

  return <h1>Welcome, {data.name}</h1>;
}
```

Cached function:

```
// src/lib/dashboard.ts

import "server-only";

import { cacheLife, cacheTag } from "next/cache";

export async function getDashboardData(userId: string) {
  "use cache";

  cacheLife("minutes");
  cacheTag(`dashboard-${userId}`);

  return db.user.findUnique({
    where: {
      id: userId,
    },
    select: {
      name: true,
    },
  });
}
```

Here, the user ID is passed into the cached function rather than reading cookies inside the cached scope.

**However:** For private user information, caching is optional, not automatically beneficial. Evaluate the sensitivity, authorization rules, cache-key isolation, and data freshness requirements before introducing shared caching. For highly sensitive data, fetching without a persistent shared cache may be the safer design.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **17. Caching in Route Handlers**

Route Handlers can also work with cached data.

For example:

```
// app/api/products/route.ts

import { NextResponse } from "next/server";
import { getProducts } from "@/lib/products";

export async function GET() {
  const products = await getProducts();

  return NextResponse.json(products);
}
```

Because `getProducts()` is a cached function, the Route Handler can reuse its cached result.

### **Previous model: Cache a GET Route Handler**

If you are using the previous caching model, you can configure caching on a GET Route Handler:

```
// app/api/products/route.ts

export const revalidate = 300;

export async function GET() {
  const response = await fetch(
    "https://api.example.com/products"
  );

  const products = await response.json();

  return Response.json(products);
}
```

This configures a five-minute revalidation interval for the route.

### **Important distinctions**

- `GET` Route Handlers can participate in caching when their configuration and behavior permit it.
- Request-specific APIs and dynamic behavior can affect caching.
- Do not assume every Route Handler response is automatically cached.
- `POST`, `PUT`, `PATCH` and `DELETE` handlers should be designed around their mutation behavior, not treated as cacheable reads.

With Cache Components enabled, prefer explicit caching of the data-fetching function rather than relying on older route segment configuration.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2



## **18. Cache Invalidation: A Complete Real-World Workflow**

Let's connect everything.

Imagine an administrator updates a product price.

**Product update workflow**

1. **Administrator updates product**

   Product price changes from ₹75,000 to ₹70,000.
2. **Server Action updates MongoDB**

   The database stores the new price.
3. **Invalidate relevant cached data**

   Call `updateTag()` or `revalidateTag()`, depending on freshness requirements.
4. **Next request reads the product**

   Next.js refreshes or expires the relevant cached result.
5. **Updated product is displayed**

   The page shows the new price when the fresh result is available.

### **Example: Complete Server Action**

```
// src/app/actions.ts

"use server";

import { updateTag } from "next/cache";
import { revalidatePath } from "next/cache";

import { connectDB } from "@/lib/db";
import Product from "@/models/Product";

export async function updateProduct(
  id: string,
  price: number
) {
  await connectDB();

  const product = await Product.findByIdAndUpdate(
    id,
    { price },
    { new: true }
  );

  if (!product) {
    return {
      success: false,
      message: "Product not found",
    };
  }

  // Immediately expire related cached data.
  updateTag("products");
  updateTag(`product-${id}`);

  // Refresh relevant route output.
  revalidatePath("/products");
  revalidatePath(`/products/${id}`);

  return {
    success: true,
    message: "Product updated successfully",
  };
}
```

Here, `updateTag()` is appropriate when the administrator expects the changed data to be visible immediately. `revalidatePath()` also targets the affected routes.

You should avoid invalidating everything unnecessarily. Choose the smallest set of tags and paths that covers the changed data.

## **19. Cache Invalidation Strategies**

Cache invalidation is one of the hardest parts of designing a caching system.

You need to decide when cached data should be refreshed.

### **Strategy 1: Time-based expiration**

Example:

```
cacheLife("hours");
```

Useful for data that changes occasionally.

### **Strategy 2: Tag-based invalidation**

Example:

```
revalidateTag("products", "max");
```

Useful when multiple pages share the same data.

### **Strategy 3: Path-based invalidation**

Example:

```
revalidatePath("/products");
```

Useful when you want to refresh a specific route.

### **Strategy 4: Immediate expiration**

Example:

```
updateTag("user-profile");
```

Useful when the user must immediately see their own updated data.

### **Strategy 5: Webhook-based invalidation**

Suppose you have a CMS that manages blog articles.

When an administrator publishes a new article, the CMS can send a webhook to your Next.js application.

Your application then invalidates the corresponding cache tag.

Example:

```
// app/api/revalidate/route.ts

import { NextRequest, NextResponse } from "next/server";
import { revalidateTag } from "next/cache";

export async function POST(request: NextRequest) {
  const secret = request.headers.get("authorization");

  if (
    secret !== `Bearer ${process.env.REVALIDATION_SECRET}`
  ) {
    return NextResponse.json(
      { message: "Unauthorized" },
      { status: 401 }
    );
  }

  const body = await request.json();

  if (body.type === "post.updated") {
    revalidateTag("posts", "max");
  }

  return NextResponse.json({
    revalidated: true,
  });
}
```

**Important:** Validate the webhook secret and the event payload. In a production integration, also validate the event type, resource identifier, and signature mechanism supported by the CMS. Do not let arbitrary callers invalidate unrestricted cache tags.

## **20. Performance Optimization in Next.js**

Caching is only one part of performance optimization.

A fast Next.js application also needs good rendering, data fetching, asset delivery, and bundle management.

### **20.1 Avoid sequential data fetching when possible**

Bad:

```
export default async function Dashboard() {
  const user = await getUser();

  const orders = await getOrders();

  const products = await getProducts();

  return <div>Dashboard</div>;
}
```

The operations happen sequentially.

If the queries are independent, they can run in parallel.

Better:

```
export default async function Dashboard() {
  const [user, orders, products] =
    await Promise.all([
      getUser(),
      getOrders(),
      getProducts(),
    ]);

  return <div>Dashboard</div>;
}
```

This can reduce total waiting time.

Do not use parallel fetching when one query depends on the result of another.

### **20.2 Use streaming with Suspense**

Suppose your dashboard has:

- User profile
- Recent orders
- Analytics

The analytics query may be slow.

You can stream the page in sections instead of waiting for everything.

```
import { Suspense } from "react";

export default function DashboardPage() {
  return (
    <main>
      <h1>Dashboard</h1>

      <Suspense fallback={<p>Loading profile...</p>}>
        <Profile />
      </Suspense>

      <Suspense fallback={<p>Loading orders...</p>}>
        <RecentOrders />
      </Suspense>

      <Suspense fallback={<p>Loading analytics...</p>}>
        <Analytics />
      </Suspense>
    </main>
  );
}

async function Profile() {
  const user = await getUser();

  return <p>{user.name}</p>;
}

async function RecentOrders() {
  const orders = await getOrders();

  return <p>{orders.length} orders</p>;
}

async function Analytics() {
  const analytics = await getAnalytics();

  return <p>{analytics.revenue}</p>;
}
```

This improves perceived performance because users can see available sections while slower sections are still loading.

### **20.3 Use&#x20;**`next/image`

Avoid unnecessarily large image files.

```
import Image from "next/image";

export default function ProductCard() {
  return (
    <Image
      src="/products/laptop.jpg"
      alt="Gaming laptop"
      width={400}
      height={300}
    />
  );
}
```

Next.js Image Optimization can resize and optimize supported images for different display sizes.

### **20.4 Use dynamic imports for heavy components**

For large client-side components that are not required immediately, use lazy loading.

```
"use client";

import dynamic from "next/dynamic";

const Chart = dynamic(
  () => import("./HeavyChart"),
  {
    loading: () => <p>Loading chart...</p>,
  }
);

export default function Dashboard() {
  return <Chart />;
}
```

This can reduce the initial JavaScript needed by the page.

### **20.5 Use Server Components by default**

Server Components help keep server-only code and dependencies out of the client JavaScript bundle.

Use Client Components when you need:

- State.
- Event handlers.
- Effects.
- Browser APIs.
- Interactive UI.

### **20.6 Avoid unnecessary database queries**

Use:

- Proper database indexes.
- Projection to retrieve only required fields.
- Pagination for large collections.
- Query optimization.
- Caching where appropriate.
- Connection reuse.

Example:

```
const products = await Product.find({
  isActive: true,
})
  .select("name price image")
  .limit(20)
  .lean();
```

This retrieves only selected fields and limits the number of documents.

## **21. Cache Performance: How to Decide What to Cache**

Not all data should be cached.

The correct strategy depends on how frequently the data changes and whether it is user-specific.

| Data                        | Recommended approach                                     |
| --------------------------- | -------------------------------------------------------- |
| Public product catalog      | Cache with time-based and tag-based revalidation         |
| Blog articles               | Cache with longer lifetime and on-demand revalidation    |
| Static categories           | Cache for longer periods                                 |
| Public company information  | Cache for long periods                                   |
| Live stock availability     | Short lifetime or fresh server request                   |
| User profile                | Consider user-specific cache keys or no persistent cache |
| Shopping cart               | Usually fresh, user-specific data                        |
| Payment status              | Retrieve fresh and verify with trusted payment provider  |
| Private banking information | Avoid shared caching; use secure data access             |
| Real-time notifications     | Use an appropriate real-time or short-lived strategy     |

### **A practical rule**

Ask these three questions before caching any data:

1. Does the data change frequently?
2. Can multiple users safely receive the same cached result?
3. How quickly must an update become visible?

Your answers should determine the cache lifetime, cache key, and invalidation strategy.

## **22. Important Difference: Previous Model vs Cache Components**

This comparison is particularly important for Next.js 16 developers.

| Feature           | Previous model                        | Cache Components                    |
| ----------------- | ------------------------------------- | ----------------------------------- |
| Enable caching    | Fetch options and route configuration | `cacheComponents: true`             |
| Cache a function  | `unstable_cache()`                    | `"use cache"`                       |
| Cache lifetime    | `next.revalidate`                     | `cacheLife()`                       |
| Cache tags        | `next.tags`                           | `cacheTag()`                        |
| Invalidate tags   | `revalidateTag()`                     | `revalidateTag()` / `updateTag()`   |
| Invalidate paths  | `revalidatePath()`                    | `revalidatePath()`                  |
| Database caching  | `unstable_cache()`                    | `"use cache"`                       |
| Dynamic rendering | Route segment configuration           | Explicit cached and uncached scopes |

### **One important migration rule**

When Cache Components is enabled, older route segment options such as:

```
export const dynamic = "force-dynamic";
export const revalidate = 300;
export const fetchCache = "force-cache";
```

are replaced by the newer explicit caching approach.

For example, instead of:

```
export const revalidate = 3600;

export default async function Page() {
  const data = await getData();

  return <div>{data.name}</div>;
}
```

Use:

```
import { cacheLife } from "next/cache";

export default async function Page() {
  "use cache";

  cacheLife("hours");

  const data = await getData();

  return <div>{data.name}</div>;
}
```

The newer model uses explicit cache boundaries rather than relying on route-wide caching settings.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **23. Common Caching Mistakes**

| Mistake                                           | Problem                                                 | Solution                                        |
| ------------------------------------------------- | ------------------------------------------------------- | ----------------------------------------------- |
| Caching everything                                | Unnecessary storage and potentially stale data          | Cache selectively                               |
| Using `use cache` without `cacheComponents`       | Cache Components are not enabled                        | Enable the configuration                        |
| Using `updateTag()` in a Route Handler            | It is restricted to Server Actions                      | Use `revalidateTag()`                           |
| Using deprecated `revalidateTag(tag)`             | Old single-argument API                                 | Use `revalidateTag(tag, "max")`                 |
| Forgetting `cacheTag()`                           | Cannot selectively invalidate that cached result by tag | Add appropriate tags                            |
| Caching private user data carelessly              | Can expose another user's information                   | Design authorization and cache keys carefully   |
| Using very long cache lifetimes for inventory     | Users may see outdated availability                     | Use shorter lifetimes or fresh reads            |
| Invalidating too many paths                       | Unnecessary cache refreshes                             | Prefer targeted tag invalidation                |
| Caching payment status                            | May display outdated transaction information            | Verify payment state with the trusted provider  |
| Assuming React `cache()` persists across requests | It is request-scoped                                    | Use Next.js persistent caching when appropriate |
| Using caching instead of database indexes         | Does not fix inefficient queries                        | Optimize database queries too                   |
| Forgetting to invalidate after mutations          | Users may see outdated data                             | Add invalidation to the mutation workflow       |

## **24. Interview Questions and Answers**

### **Basic Level**

**Q1. What is caching?**

Caching is the process of storing previously retrieved or computed data so it can be reused instead of performing the same operation repeatedly.

**Q2. What is a cache hit?**

When requested data is available in the cache.

**Q3. What is a cache miss?**

When requested data is not available in the cache.

**Q4. Why is caching important?**

It reduces response time, database queries, server workload and infrastructure costs.

**Q5. What is cache invalidation?**

Making cached data stale or unusable so that updated data can be fetched.

### **Intermediate Level**

**Q6. What is the&#x20;**`"use cache"`**&#x20;directive?**

It tells Next.js to cache the output of a function or component when Cache Components are enabled.

**Q7. What is&#x20;**`cacheLife()`**?**

It configures how long cached results remain usable and when they should be refreshed.

**Q8. What is&#x20;**`cacheTag()`**?**

It associates a tag with cached data so that the data can be selectively invalidated later.

**Q9. What is&#x20;**`revalidateTag()`**?**

It marks cached entries associated with a tag as stale, allowing them to be refreshed according to the selected cache life profile.

**Q10. What is&#x20;**`updateTag()`**?**

It immediately expires tagged cached data and is designed for read-your-own-writes behavior in Server Actions.

**Q11. What is&#x20;**`revalidatePath()`**?**

It invalidates cached data associated with a specific page or layout path.

**Q12. What is React&#x20;**`cache()`**?**

It memoizes function results during a server render so repeated calls can reuse the same result.

### **Advanced Level**

**Q13. What is the difference between&#x20;**`revalidateTag()`**&#x20;and&#x20;**`updateTag()`**?**

`revalidateTag()` with `"max"` uses stale-while-revalidate behavior, while `updateTag()` expires the cache immediately and is restricted to Server Actions.

**Q14. What is the difference between React&#x20;**`cache()`**&#x20;and Next.js&#x20;**`"use cache"`**?**

React `cache()` provides request-scoped memoization. Next.js `"use cache"` provides persistent caching controlled through Next.js cache profiles and tags.

**Q15. What is stale-while-revalidate?**

A caching strategy where the user receives previously cached content while fresh content is fetched in the background.

**Q16. Why should we not cache every database query?**

Some information is sensitive, user-specific or changes frequently. Caching it incorrectly can cause stale results or security problems.

**Q17. How do you cache MongoDB queries in Next.js 16?**

With Cache Components enabled, use `"use cache"` inside a server-side data-access function and configure its lifetime with `cacheLife()`.

**Q18. How do you invalidate a product catalog after updating a product?**

Associate the catalog query with a tag such as `products`, then call:

```
revalidateTag("products", "max");
```

Or use `updateTag("products")` inside a Server Action when immediate freshness is required.

**Q19. What is the difference between browser caching and server-side caching?**

Browser caching stores resources or responses on the user's device. Server-side caching stores data or generated output on the server infrastructure.

**Q20. Does caching replace database indexing?**

No. Caching reduces repeated operations, while indexes improve the efficiency of database queries. They solve different performance problems.

## **25. Practical Assignment**

Advanced hands-on project

Build a caching system for a Next.js e-commerce application using MongoDB.

### **Task 1: Enable Cache Components**

- Configure `cacheComponents: true`.
- Restart the development server.
- Create a server-only data-access module.

### **Task 2: Cache product data**

Create:

```
src/lib/products.ts
```

Requirements:

- Create `getProducts()`.
- Use `"use cache"`.
- Configure `cacheLife()`.
- Add a `products` cache tag.
- Create `getProductById(id)`.
- Add an individual product tag.

### **Task 3: Display cached data**

Create:

```
src/app/products/page.tsx
src/app/products/[id]/page.tsx
```

Requirements:

- Display all active products.
- Display individual product details.
- Reuse cached database queries.
- Add appropriate loading and error UI.

### **Task 4: Implement cache invalidation**

Create:

```
src/app/actions.ts
```

Requirements:

- Implement product creation.
- Implement product updating.
- Implement product deletion.
- Invalidate the product catalog tag.
- Invalidate the individual product tag.
- Use `updateTag()` when immediate freshness is needed.
- Use `revalidateTag()` when stale-while-revalidate is acceptable.

### **Task 5: Understand caching behavior**

Test the following:

- Open the products page twice.
- Observe whether cached data is reused.
- Update a product price.
- Verify that the cache is invalidated.
- Verify that the updated price is displayed.
- Test with a different product ID.
- Verify that unrelated cached products remain usable.

### **Task 6: Performance optimization**

- Use `Promise.all()` for independent queries.
- Use database indexes where appropriate.
- Use pagination.
- Use `next/image`.
- Avoid unnecessary Client Components.
- Use Suspense for slow sections.
- Measure performance before and after optimization.

## **26. Quick Revision Notes**

| Concept            | Remember                                              |
| ------------------ | ----------------------------------------------------- |
| Caching            | Reuse previously computed or fetched data             |
| Cache hit          | Data found in cache                                   |
| Cache miss         | Data not found in cache                               |
| Cache Components   | Modern Next.js caching model                          |
| `cacheComponents`  | Enables Cache Components                              |
| `"use cache"`      | Caches a function or component                        |
| `cacheLife()`      | Configures cache lifetime                             |
| `stale`            | Client router cache freshness                         |
| `revalidate`       | Server background refresh interval                    |
| `expire`           | Maximum server cache lifetime                         |
| `cacheTag()`       | Associates tags with cached data                      |
| `revalidateTag()`  | Marks tagged data stale                               |
| `updateTag()`      | Immediately expires tagged data                       |
| `revalidatePath()` | Invalidates a route or layout                         |
| React `cache()`    | Request-scoped memoization                            |
| `force-cache`      | Reuse persistent Data Cache in previous model         |
| `no-store`         | Opt out of persistent Data Cache                      |
| `next.revalidate`  | Time-based fetch revalidation in previous model       |
| `next.tags`        | Tags for cached fetch requests in previous model      |
| `Promise.all()`    | Runs independent asynchronous operations concurrently |
| Suspense           | Enables streaming of asynchronous UI                  |

## **27. Phase 17 Completion Checklist**

**Your learning progress**

### **0/20**

Understand caching and its benefits

Understand browser, CDN and server caching

Understand Next.js 16 caching models

Enable Cache Components

Use the use cache directive

Understand cache keys and cache boundaries

Use cacheLife() and its three timing properties

Use cacheTag() for selective invalidation

Understand revalidateTag()

Understand updateTag()

Understand revalidatePath()

Compare time-based and on-demand revalidation

Understand previous-model fetch caching

Understand React cache() vs use cache

Implement MongoDB caching

Understand caching with cookies and user-specific data

Implement cache invalidation after mutations

Understand Route Handler caching

Optimize fetching, images and components

Complete the practical assignment

Reset checklist

## **28. Next Phase Preview**

Next: Phase 18

# **Images, Fonts, Scripts and Static Assets Optimization**

**Topics we will cover:**

1. Understanding the `public` folder.
2. Image optimization using `next/image`.
3. Image dimensions, aspect ratios and responsive images.
4. `fill`, `sizes`, `priority` and loading strategies.
5. Remote images and `remotePatterns`.
6. Image placeholders and blur effects.
7. Font optimization using `next/font`.
8. Local fonts and Google Fonts.
9. Script optimization using `next/script`.
10. Script loading strategies.
11. Static assets and file organization.
12. Performance and Core Web Vitals.
13. Practical asset optimization in a Next.js application.

**When you're ready, say&#x20;**`OK, Phase 18`**.**

### **Official documentation for this phase**

- [Next.js Cache Components](https://nextjs.org/docs/app/api-reference/config/next-config-js/cacheComponents?utm_source=chatgpt.com)
- [use cache directive](https://nextjs.org/docs/app/api-reference/directives/use-cache?utm_source=chatgpt.com)
- [cacheLife()](https://nextjs.org/docs/app/api-reference/functions/cacheLife?utm_source=chatgpt.com)
- [cacheTag()](https://nextjs.org/docs/app/api-reference/functions/cacheTag?utm_source=chatgpt.com)
- [revalidateTag()](https://nextjs.org/docs/app/api-reference/functions/revalidateTag?utm_source=chatgpt.com)
- [updateTag()](https://nextjs.org/docs/app/api-reference/functions/updateTag?utm_source=chatgpt.com)
- [revalidatePath()](https://nextjs.org/docs/app/api-reference/functions/revalidatePath?utm_source=chatgpt.com)
- [Caching without Cache Components](https://nextjs.org/docs/app/guides/caching-without-cache-components?utm_source=chatgpt.com)