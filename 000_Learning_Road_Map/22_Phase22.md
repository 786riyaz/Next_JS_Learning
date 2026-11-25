# **Phase 22 – Advanced Data Fetching, Server-Side Rendering and Rendering Strategies**



Advanced Level

Next.js 16.x

App Router + TypeScript



## **1. Learning objectives**

In the previous phases, you learned data fetching, caching, revalidation, Server Components, Client Components, and streaming.

Now we will study how Next.js decides **when and where a page or component should be rendered**.

By the end of this phase, you will understand:

1. Static Site Generation (SSG).
2. Server-Side Rendering (SSR).
3. Incremental Static Regeneration (ISR).
4. Client-Side Rendering (CSR).
5. Partial Prerendering (PPR).
6. Static and dynamic rendering decisions.
7. Rendering strategies for different page types.
8. Hybrid rendering architecture.
9. Performance trade-offs.
10. Real-world rendering strategy selection.

## **2. What is rendering?**

Rendering is the process of converting application components and data into the output that a browser can display.

For example, suppose you have this React component:

```
export default function HomePage() {
  return (
    <main>
      <h1>Welcome to My Website</h1>
      <p>This is my homepage.</p>
    </main>
  );
}
```

React and Next.js render this component into HTML that the browser can display.

But an important question is:

**When should this HTML be generated?**

There are different possibilities:

- Before a user requests the page.
- When a user requests the page.
- In the browser after JavaScript executes.
- Partially before the request, with some content generated later.

These approaches are the foundation of rendering strategies.

## **3. The four major rendering strategies**

There are four traditional rendering concepts you should understand.

**1. SSG – Static Site Generation**

Build time

[Comparing SSG, SSR, and ISR: Which Rendering Strategy Fits Your Web Project?](https://images.openai.com/static-rsc-4/GYYcgWlHAd1M2klJft8Vf5k5d93lnpBDiajdhd84VSk4Bbjz6uGu_WdqeWTKuRJ-Y9z7Hpe8XKe-0yJtwfC9qkkiz7BHuckhcrCPBJvRgQArfay7zEXgS26Ukqh2dzO2t1ie56ViGczbGqc7urffZUSJRjl1Qe6PNECZetpUKoA?purpose=inline)

[linkedin.com](https://www.linkedin.com/pulse/comparing-ssg-ssr-isr-which-rendering-strategy-fits-your-web-5lw8f?utm_source=chatgpt.com)

HTML is generated ahead of time and reused for requests.

**2. SSR – Server-Side Rendering**

Request time

[Rendering Modes Explained - DEV Community](https://images.openai.com/static-rsc-4/gRwUso0eofGIaw1sqelqCJkTp1RbzozK_yJKkJIkekiod4ErrLev2_gWJ7gsKYFG46A5FyDS5pYjHwRyl_PdPPkLOydoz0mo5vlFNSmBOZ40OoL16ro8ItwjdlZm-O6c480CTYENrAqLJe4V7tYDoP0Lf2FVcxWRHK2P1eG1K4w?purpose=inline)

[dev.to](https://dev.to/andresilva-cc/rendering-modes-explained-2711?utm_source=chatgpt.com)

The server generates the relevant output when a request arrives.

**3. ISR – Incremental Static Regeneration**

Revalidation

[All about Next.js ISR and how to implement it | Contentful](https://images.openai.com/static-rsc-4/i7z2sDEDQP9VYs0PpMchSbQRmTGW24_lunScTEVUmVPM7B7Z1OWyY7EqVn0umpWOyOPa0vnT6MMgLuP7JN-XicnOrhFeSqgvRfH0rhBkb_qLMMZFmum7XWP4Uutz9HGwGWnGkUk4wqBZWa6yQ8mf3fJ2c7fIDyaHDz97On8M8yg?purpose=inline)

[contentful.com](https://www.contentful.com/blog/nextjs-isr/?utm_source=chatgpt.com)

Cached output is reused and refreshed according to a revalidation strategy.

**4. CSR – Client-Side Rendering**

Browser

[Client-Side Rendering (CSR): When to Use It | Feature-Sliced Design](https://images.openai.com/static-rsc-4/EJZ0bRw78CGse9wt4ciGHKZj20qcM2IPt559L-JraZT1dFK83fVXu_vHhudr9SgM-UXQN4lwrcHN9q_trlI8H-WmrKVTyD5hW5z5f5MuAW8PP2-ViCVjnyAkmu_ZAwiZiYA5CNQ8tUGd11xwaB45i7rxaLOWBKLkDV7fctAal5w?purpose=inline)

[feature-sliced.design](https://feature-sliced.design/blog/what-is-client-side-rendering?utm_source=chatgpt.com)

The browser executes JavaScript to fetch or update data and render interactive content.

These are useful conceptual models. In the App Router, Next.js can combine them at the component level rather than requiring one rendering strategy for an entire application.

Let's understand each one.

## **4. SSG – Static Site Generation**

SSG stands for **Static Site Generation**.

In SSG, Next.js generates the page output before users request it, commonly during the production build.

The generated output can then be reused for multiple visitors.

### **4.1 Understand the process**

Suppose you have a blog website with 100 articles.

During deployment:

```
Run next build
      |
      v
Fetch blog data
      |
      v
Generate HTML for articles
      |
      v
Store prerendered output
```

When users visit:

```
/blog/nextjs-basics
/blog/react-hooks
/blog/nodejs-guide
```

Next.js can serve the previously generated output instead of generating each page from scratch for every request.

### **4.2 Example: Static homepage**

```
// app/page.tsx

export default function HomePage() {
  return (
    <main>
      <h1>Welcome to My Blog</h1>

      <p>Learn programming with practical tutorials.</p>
    </main>
  );
}
```

This page does not depend on request-specific information or uncached runtime data.

It is a good candidate for prerendering.

### **4.3 Example: Static data fetching**

Suppose you have a public API:

```
https://api.example.com/posts
```

You want to fetch posts and generate a page from them.

```
// app/blog/page.tsx

async function getPosts() {
  const response = await fetch(
    "https://api.example.com/posts"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch posts");
  }

  return response.json();
}

export default async function BlogPage() {
  const posts = await getPosts();

  return (
    <main>
      <h1>Blog Posts</h1>

      {posts.map((post: { id: number; title: string }) => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </main>
  );
}
```

The exact rendering behavior depends on your Next.js caching configuration and whether the data is eligible for prerendering.

**Important:** In modern Next.js, do not assume that every `fetch()` request is cached automatically. You must understand the caching model being used.

### **4.4 Advantages of SSG**

- Fast response times.
- Reduced server computation per request.
- Excellent CDN distribution.
- Good SEO.
- Useful for public content that changes infrequently.

### **4.5 Disadvantages of SSG**

- Content may become outdated between regenerations or deployments.
- Build time can increase when generating thousands of pages.
- It is not appropriate for content that must be personalized for each visitor.

### **4.6 When should you use SSG?**

Good examples:

- Documentation websites.
- Marketing websites.
- Public landing pages.
- Company websites.
- Blog articles.
- Public product information.

## **5. Dynamic routes with static generation –&#x20;**`generateStaticParams()`

You already learned dynamic routes in Phase 5.

Now let's connect dynamic routing with static generation.

Suppose your website has:

```
/blog/[slug]
```

You have three articles:

```
/blog/nextjs
/blog/react
/blog/nodejs
```

You can generate these dynamic routes at build time using `generateStaticParams()`.

### **5.1 Example**

Folder structure:

```
app/
└── blog/
    └── [slug]/
        └── page.tsx
```

Code:

```
// app/blog/[slug]/page.tsx

const posts = [
  {
    slug: "nextjs",
    title: "Learn Next.js",
  },
  {
    slug: "react",
    title: "Learn React",
  },
  {
    slug: "nodejs",
    title: "Learn Node.js",
  },
];

export async function generateStaticParams() {
  return posts.map((post) => ({
    slug: post.slug,
  }));
}

export default async function BlogPost({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;

  const post = posts.find(
    (item) => item.slug === slug
  );

  if (!post) {
    return <h1>Post not found</h1>;
  }

  return (
    <article>
      <h1>{post.title}</h1>
    </article>
  );
}
```

### **5.2 What happens during the build?**

`generateStaticParams()` returns:

```
[
  { slug: "nextjs" },
  { slug: "react" },
  { slug: "nodejs" }
]
```

Next.js can generate the corresponding route output for:

```
/blog/nextjs
/blog/react
/blog/nodejs
```

### **5.3 What about an unknown slug?**

You can control what happens to dynamic parameters that were not generated.

For example:

```
export const dynamicParams = false;
```

With this setting, paths that were not generated by `generateStaticParams()` return a not-found result.

Without that restriction, additional paths may be rendered when requested, depending on the rendering configuration.

### **5.4 Important Cache Components difference**

When using the Next.js 16 Cache Components model, `generateStaticParams()` must return at least one parameter. An empty array causes a build error.

Without Cache Components, an empty array can be used in supported configurations to defer generation to runtime.

This distinction matters when following older Next.js tutorials.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **6. SSR – Server-Side Rendering**

SSR stands for **Server-Side Rendering**.

In SSR, the server generates the relevant page output when a request arrives.

This is useful when the page depends on information that is different for each request.

### **6.1 Understand the process**

Suppose a user opens their dashboard.

The dashboard needs:

- The current user's identity.
- Their recent orders.
- Their account information.
- Their permissions.

The server receives the request and retrieves the appropriate information.

```
User visits /dashboard
        |
        v
Server receives request
        |
        v
Read session information
        |
        v
Fetch user's data
        |
        v
Render HTML
        |
        v
Send response to browser
```

Different users can receive different results for the same URL.

### **6.2 Example: User-specific dashboard**

```
// app/dashboard/page.tsx

import { cookies } from "next/headers";

export default async function DashboardPage() {
  const cookieStore = await cookies();

  const sessionToken = cookieStore.get("session")?.value;

  if (!sessionToken) {
    return <h1>Please log in</h1>;
  }

  // Verify session and retrieve user information.
  const user = await getCurrentUser(sessionToken);

  return (
    <main>
      <h1>Welcome, {user.name}</h1>

      <p>Email: {user.email}</p>
    </main>
  );
}
```

Here:

```
await cookies()
```

reads request-specific cookie information.

The page's result depends on the current request, so it cannot simply be shared as the same personalized HTML for every visitor.

### **6.3 Advantages of SSR**

- Supports request-specific data.
- Useful for personalized pages.
- Can provide rendered HTML without waiting for client-side data fetching.
- Useful for authentication-dependent content.
- Good for data that must be fresh at request time.

### **6.4 Disadvantages of SSR**

- Requires server work for requests.
- Can increase response latency when database or external API calls are slow.
- Requires appropriate server capacity.
- Uncached data fetching can increase infrastructure costs.

### **6.5 When should you use SSR?**

Good examples:

- User dashboards.
- Account pages.
- Personalized recommendations.
- Request-specific search results.
- Pages that depend on session information.

**Important:** SSR does not mean the browser receives no JavaScript. Interactive Client Components can still be hydrated after the server sends the rendered output.

## **7. ISR – Incremental Static Regeneration**

ISR stands for **Incremental Static Regeneration**.

It is a strategy that combines the performance benefits of static output with the ability to refresh content without rebuilding the entire application.

Imagine you have a news website.

Articles change throughout the day, but you do not want to generate every article from scratch on every request.

ISR provides a way to reuse cached content and regenerate it periodically or on demand.

### **7.1 Understand the process**

Suppose your page has a revalidation interval of 60 seconds.

Conceptually:

```
First generation
      |
      v
Cache generated output
      |
      v
Serve cached content
      |
      v
Revalidation period passes
      |
      v
A new request arrives
      |
      v
Serve existing content
and regenerate in background
      |
      v
Store fresh output
```

This is the classic stale-while-revalidate behavior.

### **7.2 ISR using the previous App Router caching model**

If your project is not using Cache Components, you can use:

```
// app/news/page.tsx

export const revalidate = 60;

async function getNews() {
  const response = await fetch(
    "https://api.example.com/news",
    {
      next: {
        revalidate: 60,
      },
    }
  );

  if (!response.ok) {
    throw new Error("Failed to fetch news");
  }

  return response.json();
}

export default async function NewsPage() {
  const news = await getNews();

  return (
    <main>
      <h1>Latest News</h1>

      {news.map((item: { id: number; title: string }) => (
        <article key={item.id}>
          {item.title}
        </article>
      ))}
    </main>
  );
}
```

Here:

```
export const revalidate = 60;
```

sets a route-level revalidation interval.

And:

```
next: {
  revalidate: 60,
}
```

sets a revalidation interval for the fetch cache entry.

The two settings have related but distinct scopes.

### **7.3 ISR with Cache Components in Next.js 16**

Next.js 16 introduces Cache Components as a unified model for explicitly controlling what gets cached and what remains dynamic.

Enable it:

```
// next.config.ts

import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

Then create a cached data function:

```
// lib/news.ts

import { cacheLife, cacheTag } from "next/cache";

export async function getNews() {
  "use cache";

  cacheLife("minutes");

  cacheTag("news");

  const response = await fetch(
    "https://api.example.com/news"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch news");
  }

  return response.json();
}
```

Now the result is cached using the `minutes` cache profile.

This provides behavior similar to ISR, with explicit control over the cache lifetime.

### **7.4 On-demand revalidation**

Suppose an administrator publishes a new article.

You want the cached news data to become stale.

You can use:

```
import { revalidateTag } from "next/cache";

export async function publishNews() {
  // Save the new article.

  revalidateTag("news", "max");
}
```

This marks data tagged with `news` as stale so it can be refreshed when next requested.

For immediate expiration when a user should see their own change right away, `updateTag()` is another option available in Server Actions.

### **7.5 ISR vs SSR vs SSG**

| Feature                 | SSG                             | SSR                       | ISR                               |
| ----------------------- | ------------------------------- | ------------------------- | --------------------------------- |
| Generation              | Ahead of request                | During request            | Regeneration of cached output     |
| Cache reuse             | Yes                             | Not necessarily           | Yes                               |
| Freshness               | Depends on rebuild/revalidation | Request-time data         | Depends on revalidation           |
| Server work per request | Usually low                     | Required for dynamic work | Usually reduced                   |
| Best for                | Stable public content           | Personalized content      | Frequently updated public content |

**Important:** ISR is a useful rendering concept, but the exact APIs depend on the Next.js caching model. With Cache Components enabled, use `use cache`, `cacheLife`, and cache invalidation functions rather than relying on the old route segment `revalidate` configuration.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2



## **8. CSR – Client-Side Rendering**

CSR stands for **Client-Side Rendering**.

In CSR, the browser executes JavaScript to fetch data and update the UI.

This approach is common in highly interactive applications.

### **8.1 Understand the process**

Suppose a dashboard retrieves user activity from an API.

The process:

```
Browser opens page
       |
       v
JavaScript executes
       |
       v
API request is sent
       |
       v
Server returns JSON
       |
       v
React updates component state
       |
       v
New UI is rendered
```

### **8.2 Example using useEffect**

```
"use client";

import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function UsersPage() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchUsers() {
      try {
        const response = await fetch("/api/users");

        if (!response.ok) {
          throw new Error("Failed to fetch users");
        }

        const data = await response.json();

        setUsers(data);
      } catch {
        setError("Unable to load users");
      } finally {
        setLoading(false);
      }
    }

    fetchUsers();
  }, []);

  if (loading) {
    return <p>Loading users...</p>;
  }

  if (error) {
    return <p>{error}</p>;
  }

  return (
    <main>
      <h1>Users</h1>

      {users.map((user) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </main>
  );
}
```

### **8.3 Advantages of CSR**

- Good for interactive interfaces.
- Browser can update data without a full page reload.
- Useful for frequently changing client-side state.
- Supports browser-specific APIs.
- Can reduce repeated server-rendering work for some interactions.

### **8.4 Disadvantages of CSR**

- Content fetched only after JavaScript runs may appear later.
- Initial loading states can be visible.
- Search engine indexing can be less straightforward depending on the content and crawler.
- More application JavaScript may need to download and execute.

### **8.5 When should you use CSR?**

Good examples:

- Interactive dashboards.
- Live filters.
- Rich data grids.
- Client-side editors.
- Maps and interactive visualizations.
- User interface state that changes frequently.

### **8.6 CSR with SWR**

Instead of manually managing loading, error, and data state for every request, you can use a client-side data-fetching library such as SWR.

Install:

```
npm install swr
```

Example:

```
"use client";

import useSWR from "swr";

const fetcher = async (url: string) => {
  const response = await fetch(url);

  if (!response.ok) {
    throw new Error("Failed to fetch");
  }

  return response.json();
};

export default function UsersPage() {
  const { data, error, isLoading } = useSWR(
    "/api/users",
    fetcher
  );

  if (isLoading) {
    return <p>Loading...</p>;
  }

  if (error) {
    return <p>Something went wrong.</p>;
  }

  return (
    <main>
      {data.map((user: { id: number; name: string }) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </main>
  );
}
```

SWR also provides features such as:

- Revalidation.
- Focus-based refresh.
- Deduplication.
- Optimistic updates.
- Error retry.

**Important:** A Client Component is not necessarily CSR-only. Next.js can prerender Client Components on the server for the initial page load and then hydrate them in the browser. CSR describes where client-side data fetching or UI rendering occurs, not the mere presence of `"use client"`.

## **9. Static vs dynamic rendering in the App Router**

Now let's connect the traditional rendering concepts with modern Next.js.

In the App Router, Next.js uses two broad server-rendering approaches:

1. Prerendering.
2. Dynamic rendering.

### **9.1 Prerendering**

Prerendering means generating the route output ahead of the user's request.

It can happen:

- During the production build.
- During cache revalidation.

The generated output can be reused for later requests.

Examples:

- Public blog pages.
- Documentation pages.
- Marketing pages.
- Public product catalogs.

### **9.2 Dynamic rendering**

Dynamic rendering means generating request-specific output when a request arrives.

This is useful when a page depends on information such as:

- Cookies.
- Request headers.
- User sessions.
- Search parameters.
- Request-specific information.

For example:

```
import { cookies } from "next/headers";

export default async function ProfilePage() {
  const cookieStore = await cookies();

  const session = cookieStore.get("session");

  return (
    <main>
      <h1>My Profile</h1>
      <p>
        Session: {session ? "Available" : "Not available"}
      </p>
    </main>
  );
}
```

The output depends on the incoming request.

### **9.3 Important distinction: dynamic route vs dynamic rendering**

These two terms are not the same.

**Dynamic route:**

A route has a dynamic URL segment.

Example:

```
/products/[id]
```

**Dynamic rendering:**

The route output depends on request-time data or uncached runtime work.

A dynamic route can still be statically prerendered if its parameter values are known at build time and its content is eligible for prerendering.

For example:

```
/products/1
/products/2
/products/3
```

can be generated using `generateStaticParams()`.

This distinction is important in interviews and real-world development.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **10. Cache Components – The modern Next.js 16 rendering model**

Very important for Next.js 16

This is an important version-specific topic.

Earlier Next.js tutorials often teach SSG, SSR, ISR, and PPR as separate rendering modes.

Next.js 16 introduces **Cache Components**, which provide a more explicit way to combine cached and request-time content.

The central idea is:

- Cache content that can be reused.
- Keep request-specific content dynamic.
- Use Suspense boundaries to stream dynamic content into the page.

### **10.1 Enable Cache Components**

Open:

```
next.config.ts
```

Configure:

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

This enables:

- `"use cache"`.
- `cacheLife()`.
- `cacheTag()`.
- The unified Partial Prerendering behavior.

In Next.js 16, the previous experimental PPR configuration has been removed and Cache Components provide the unified approach.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2



### **10.2 Understand&#x20;**`"use cache"`

The `"use cache"` directive marks a page, component, or function as cacheable.

Example:

```
// lib/products.ts

import { cacheLife, cacheTag } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheLife("hours");

  cacheTag("products");

  const response = await fetch(
    "https://api.example.com/products"
  );

  if (!response.ok) {
    throw new Error("Failed to fetch products");
  }

  return response.json();
}
```

Now the function's result can be reused according to the configured cache lifetime.

### **10.3 Understand&#x20;**`cacheLife()`

`cacheLife()` controls how long cached content remains valid.

Some built-in profiles:

| Profile   | Revalidation period | Typical use                |
| --------- | ------------------- | -------------------------- |
| `seconds` | 1 second            | Frequently changing data   |
| `minutes` | 1 minute            | Frequently updated content |
| `hours`   | 1 hour              | Product catalogs           |
| `days`    | 1 day               | Daily content              |
| `weeks`   | 1 week              | Stable content             |
| `max`     | 30 days             | Rarely changing content    |

These profiles also have separate client stale and server expiration timings; the revalidation period is not the only cache setting.

Example:

```
import { cacheLife } from "next/cache";

export async function getCategories() {
  "use cache";

  cacheLife("days");

  return db.category.findMany();
}
```

This is useful when category information does not change frequently.

### **10.4 Understand&#x20;**`cacheTag()`

Cache tags let you group cached data so that you can invalidate it when something changes.

```
import { cacheTag } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheTag("products");

  return db.product.findMany();
}
```

Later:

```
import { revalidateTag } from "next/cache";

revalidateTag("products", "max");
```

This marks the tagged data as stale and allows it to refresh on a subsequent request.

### **10.5 Important security rule**

Do not read request-specific values directly inside a cached scope.

For example, reading the current user's cookies inside a shared cached function can create incorrect caching behavior.

Instead:

1. Read the cookie outside the cached scope.
2. Verify the user.
3. Pass only the necessary, safe value into the cached function if it is appropriate to cache per user.
4. Keep private data isolated by the cache key and authorization rules.

For sensitive personalized data, do not cache it across users.

Official references:

- [use cache](https://nextjs.org/docs/app/api-reference/directives/use-cache?utm_source=chatgpt.com)
- [cacheLife](https://nextjs.org/docs/app/api-reference/functions/cacheLife?utm_source=chatgpt.com)
- [cacheTag](https://nextjs.org/docs/app/api-reference/functions/cacheTag?utm_source=chatgpt.com)

## **11. PPR – Partial Prerendering**

Key advanced concept

PPR stands for **Partial Prerendering**.

It allows Next.js to prerender the static parts of a page while deferring dynamic parts until the request arrives.

This combines the benefits of static rendering and dynamic rendering.

### **11.1 Understand the problem**

Imagine an e-commerce homepage.

It contains:

- Website header.
- Product categories.
- Featured products.
- Current user's name.
- Personalized promotion.
- Shopping cart count.

Some information is shared by all users:

```
Header
Product categories
Featured products
```

Other information depends on the current user:

```
User name
Personalized promotion
Shopping cart count
```

If you render everything dynamically, even the static content waits for request-time work.

If you render everything statically, you cannot correctly personalize the page.

PPR solves this by separating the static and dynamic portions.

### **11.2 Understand the architecture**

## **Partial Prerendering**

Static shell – prerendered

**Website Header**

**Product Categories**

**Featured Products**

Can be generated ahead of time and reused.

Dynamic content – request time

**Personalized User Greeting**

**Shopping Cart Count**

Filled in when request-specific data becomes available.

The dynamic parts are represented by Suspense boundaries in the prerendered shell.

### **11.3 Understand the rendering process**

```
Production build
      |
      v
Generate static page shell
      |
      v
Store prerendered output
      |
      v
User requests the page
      |
      v
Send static shell immediately
      |
      v
Render dynamic sections
      |
      v
Stream dynamic HTML into the response
```

The user does not need to wait for every dynamic section before receiving the static shell.

### **11.4 Example: Static homepage with dynamic greeting**

Let's build this concept.

First, create a static header:

```
// components/Header.tsx

export default function Header() {
  return (
    <header>
      <h1>My Store</h1>

      <nav>
        <a href="/products">Products</a>
        <a href="/about">About</a>
      </nav>
    </header>
  );
}
```

Create a cached product list:

```
// components/FeaturedProducts.tsx

import { cacheLife } from "next/cache";

export default async function FeaturedProducts() {
  "use cache";

  cacheLife("hours");

  const products = await getFeaturedProducts();

  return (
    <section>
      <h2>Featured Products</h2>

      {products.map((product) => (
        <p key={product.id}>{product.name}</p>
      ))}
    </section>
  );
}
```

Create a request-time personalized component:

```
// components/PersonalGreeting.tsx

import { cookies } from "next/headers";

export default async function PersonalGreeting() {
  const cookieStore = await cookies();

  const session = cookieStore.get("session")?.value;

  if (!session) {
    return <p>Welcome, Guest!</p>;
  }

  const user = await getCurrentUser(session);

  return (
    <p>Welcome back, {user.name}!</p>
  );
}
```

Now compose the page:

```
// app/page.tsx

import { Suspense } from "react";

import Header from "@/components/Header";

import FeaturedProducts from "@/components/FeaturedProducts";

import PersonalGreeting from "@/components/PersonalGreeting";

export default function HomePage() {
  return (
    <main>
      <Header />

      <Suspense fallback={<p>Loading your account...</p>}>
        <PersonalGreeting />
      </Suspense>

      <FeaturedProducts />
    </main>
  );
}
```

### **11.5 What is happening here?**

| Component          | Rendering behavior                      |
| ------------------ | --------------------------------------- |
| `Header`           | Static                                  |
| `FeaturedProducts` | Cached                                  |
| `PersonalGreeting` | Request-time dynamic                    |
| `Suspense`         | Defines fallback and streaming boundary |

The personalized greeting reads cookies at request time.

Because it is inside a Suspense boundary, Next.js can keep it out of the static shell and stream it later.

### **11.6 Why is PPR useful?**

- Static content can be delivered quickly.
- Dynamic content can remain personalized.
- You can reduce the need to make an entire page dynamic.
- Different components can use different caching strategies.
- It improves perceived loading performance.

### **11.7 PPR in Next.js 16**

Older tutorials may show:

```
experimental: {
  ppr: true,
}
```

Do not copy that configuration into a Next.js 16 project.

Next.js 16 removed the old experimental PPR flag. Use:

```
const nextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

Cache Components provide the unified model for cached and runtime portions of the application.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2



## **12. Streaming and Suspense in rendering strategies**

You learned streaming in Phase 7. Now let's understand its role in modern rendering.

### **12.1 What is streaming?**

Streaming means sending parts of the response to the browser as they become ready instead of waiting for the entire page to finish rendering.

Consider a dashboard:

```
Dashboard
│
├── Header          Ready immediately
│
├── Account Info    Takes 1 second
│
├── Orders          Takes 3 seconds
│
└── Analytics       Takes 5 seconds
```

Without streaming, the page might wait for the slowest section.

With streaming, the ready portions can appear earlier.

### **12.2 Example**

```
import { Suspense } from "react";

import AccountInfo from "./AccountInfo";

import Orders from "./Orders";

import Analytics from "./Analytics";

export default function DashboardPage() {
  return (
    <main>
      <h1>Dashboard</h1>

      <Suspense fallback={<p>Loading account...</p>}>
        <AccountInfo />
      </Suspense>

      <Suspense fallback={<p>Loading orders...</p>}>
        <Orders />
      </Suspense>

      <Suspense fallback={<p>Loading analytics...</p>}>
        <Analytics />
      </Suspense>
    </main>
  );
}
```

Each component can suspend independently while its fallback is displayed.

### **12.3 What is the difference between streaming and PPR?**

| Streaming                           | PPR                                                           |
| ----------------------------------- | ------------------------------------------------------------- |
| Sends response chunks progressively | Combines a prerendered static shell with dynamic sections     |
| Uses Suspense boundaries            | Uses Suspense boundaries with prerendering                    |
| Can improve perceived loading       | Can improve both initial delivery and dynamic personalization |
| Can operate with dynamic rendering  | Adds a static/dynamic hybrid rendering model                  |

Streaming is a delivery technique. PPR is a rendering strategy that uses streaming to combine static and dynamic content.

### **12.4&#x20;**`loading.tsx`**&#x20;and Suspense**

A route-level `loading.tsx` automatically creates a loading boundary around the page content.

Example:

```
app/
└── dashboard/
    ├── page.tsx
    └── loading.tsx
```

`loading.tsx`:

```
export default function Loading() {
  return <p>Loading dashboard...</p>;
}
```

Use manual Suspense boundaries when different page sections need independent loading behavior.

## **13. Hybrid rendering**

Hybrid rendering means combining multiple rendering strategies within the same application or even within the same page.

This is a major strength of the Next.js App Router.

Suppose you are building an e-commerce website.

Different pages have different requirements.

| Page            | Recommended strategy          | Reason                              |
| --------------- | ----------------------------- | ----------------------------------- |
| Homepage        | Static + dynamic sections     | Public content plus personalization |
| About page      | Static                        | Rarely changes                      |
| Product listing | Cached/prerendered            | Shared public catalog               |
| Product details | Static or cached              | Public product information          |
| User dashboard  | Dynamic                       | User-specific information           |
| Admin dashboard | Dynamic                       | Authentication and permissions      |
| Shopping cart   | Dynamic or client-interactive | User-specific cart                  |
| Checkout        | Dynamic                       | Current order and payment state     |
| Search results  | Dynamic or client-interactive | Search query changes frequently     |

### **13.1 Example architecture**

```
E-commerce Application
│
├── Homepage
│   ├── Static Header
│   ├── Cached Products
│   └── Dynamic User Greeting
│
├── Product Listing
│   └── Cached Product Data
│
├── Product Details
│   └── Prerendered or Cached Content
│
├── User Dashboard
│   └── Request-specific Data
│
├── Shopping Cart
│   └── User-specific Data
│
└── Checkout
    └── Request-specific Processing
```

You do not need to choose one rendering strategy for the whole application.

You can choose based on the requirements of each route and component.

## **14. How to choose the correct rendering strategy**

This is one of the most important practical skills.

Before implementing a page, ask these questions:

1. Is the content identical for every visitor?
2. Does the content change frequently?
3. Does it depend on the current user?
4. Does it need to be available in the initial HTML?
5. Can the content be cached safely?
6. Does it require real-time interaction?

### **14.1 Decision table**

| Requirement                             | Suitable approach        |
| --------------------------------------- | ------------------------ |
| Public content, rarely changes          | SSG / prerendering       |
| Public content, updates periodically    | ISR-like caching         |
| User-specific server data               | Dynamic rendering        |
| Interactive browser state               | Client Components        |
| Static shell with personalized sections | PPR / Cache Components   |
| Search filters that update immediately  | Client-side state        |
| Public product catalog                  | Cached data              |
| Current account information             | Request-time server data |

### **14.2 Example: Blog website**

A blog website contains:

- Homepage.
- Article listing.
- Article details.
- Admin editor.

Recommended approach:

| Section         | Strategy                 |
| --------------- | ------------------------ |
| Homepage        | Prerendered or cached    |
| Article listing | Cached with revalidation |
| Article detail  | Prerendered/cached       |
| Search filter   | Client-side interaction  |
| Admin editor    | Dynamic and interactive  |

### **14.3 Example: Banking dashboard**

A banking dashboard contains:

- Account balance.
- Transaction history.
- User profile.
- Account settings.

Recommended approach:

| Section                | Strategy                                                  |
| ---------------------- | --------------------------------------------------------- |
| Public marketing pages | Static                                                    |
| Account balance        | Request-time data                                         |
| Transactions           | Request-time data or carefully controlled private caching |
| User profile           | Authenticated dynamic data                                |
| Account settings       | Dynamic                                                   |
| Interactive charts     | Client Components using server-provided data              |

Never serve one user's private financial information through a shared public cache.

## **15. Rendering and SEO**

SEO stands for Search Engine Optimization.

Rendering strategy can affect how quickly search engines and social crawlers can discover your content.

### **15.1 Why does rendering matter for SEO?**

Suppose you have a public article:

```
/blog/nextjs-rendering
```

Search engines need to discover:

- Article title.
- Article content.
- Meta description.
- Canonical URL.
- Structured data, where appropriate.

With server prerendering, the content can be included in the initial HTML response.

This makes it easier for crawlers that primarily inspect HTML to discover your content.

### **15.2 Example of a server-rendered article**

```
// app/blog/[slug]/page.tsx

export default async function BlogPage({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;

  const post = await getPost(slug);

  return (
    <article>
      <h1>{post.title}</h1>

      <p>{post.content}</p>
    </article>
  );
}
```

The article content is rendered on the server.

### **15.3 Does CSR mean bad SEO?**

Not necessarily.

Search engines may execute JavaScript, but relying entirely on client-side fetching can delay content discovery and make indexing less predictable.

For important public content, server prerendering is generally a safer approach.

### **15.4 SEO recommendations**

- Use server-rendered or prerendered public content.
- Provide descriptive page titles.
- Add meaningful meta descriptions.
- Use semantic HTML.
- Include canonical URLs where appropriate.
- Generate sitemaps.
- Avoid empty loading shells for important content.
- Ensure important content is accessible without requiring user interaction.

## **16. Rendering performance and Core Web Vitals**

Different rendering strategies influence the user experience.

You should understand the following performance metrics.

| Metric | Meaning                   | Good target         |
| ------ | ------------------------- | ------------------- |
| LCP    | Largest Contentful Paint  | 2.5 seconds or less |
| INP    | Interaction to Next Paint | 200 ms or less      |
| CLS    | Cumulative Layout Shift   | 0.1 or less         |

These are Google's recommended good thresholds, evaluated at the 75th percentile of page visits.

### **16.1 How rendering affects performance**

**SSG**

- Can serve prerendered HTML quickly.
- Reduces request-time rendering work.
- Works well with CDN caching.

**SSR**

- Can provide fresh, personalized HTML.
- May add server response latency.
- Benefits from efficient data fetching and streaming.

**ISR**

- Reuses cached output.
- Reduces repeated generation.
- Needs a suitable freshness policy.

**CSR**

- Enables interactive browser experiences.
- Can require additional JavaScript execution and data fetching.
- Needs good loading and error states.

**PPR**

- Can send static content before slower dynamic sections are ready.
- Can improve perceived loading performance.
- Requires suitable streaming support from the deployment environment.

### **16.2 Practical performance improvements**

1. Fetch independent data in parallel.
2. Avoid unnecessary client-side JavaScript.
3. Cache reusable public data.
4. Keep personalized data separate.
5. Use Suspense boundaries around slow components.
6. Optimize images and fonts.
7. Avoid large client-side bundles.
8. Use pagination for large data sets.
9. Measure performance using real data.
10. Avoid caching sensitive user-specific responses across users.

## **17. Understanding the Next.js 16 rendering models**

You should now be able to distinguish between the older rendering model and Cache Components.

### **Model A – Previous App Router caching model**

This is used by projects that have not enabled Cache Components.

Typical APIs include:

```
export const revalidate = 60;
```

```
fetch(url, {
  cache: "force-cache",
});
```

```
fetch(url, {
  cache: "no-store",
});
```

You can also use:

```
revalidatePath("/products");
```

And:

```
revalidateTag("products", "max");
```

### **Model B – Cache Components model**

This is enabled using:

```
const nextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

Typical APIs include:

```
"use cache";
```

```
cacheLife("hours");
```

```
cacheTag("products");
```

```
updateTag("products");
```

```
revalidateTag("products", "max");
```

### **Comparison**

| Previous model                                   | Cache Components model                |
| ------------------------------------------------ | ------------------------------------- |
| Route segment `revalidate`                       | `cacheLife()`                         |
| `fetch` cache options                            | `"use cache"`                         |
| `next.tags`                                      | `cacheTag()`                          |
| `dynamic` / `fetchCache` configuration           | Explicit cache and runtime boundaries |
| Experimental PPR configuration in older releases | Unified Cache Components behavior     |

**Important:** Do not mix old and new tutorials without checking which caching model your project uses. When Cache Components is enabled, route segment configurations such as `dynamic`, `revalidate`, and `fetchCache` are replaced by the new caching approach.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2



## **18. Common rendering mistakes**

| Mistake                                          | Problem                                                   | Better approach                                         |
| ------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------- |
| Using SSR for every page                         | Unnecessary server work                                   | Prerender reusable public content                       |
| Using SSG for personalized pages                 | Incorrect shared content                                  | Use request-specific rendering                          |
| Treating every dynamic route as SSR              | Dynamic URL does not automatically mean dynamic rendering | Use `generateStaticParams()` where appropriate          |
| Assuming every `fetch()` is cached               | Can lead to unexpected freshness and performance behavior | Configure caching deliberately                          |
| Using CSR for all public content                 | Content may appear late                                   | Prefer server prerendering for important public content |
| Caching private user information globally        | Can expose one user's data to another                     | Isolate private data and enforce authorization          |
| Putting all content behind one Suspense boundary | Slow sections block unrelated content                     | Use independent boundaries                              |
| Confusing PPR with streaming                     | They solve related but different problems                 | Understand the static shell and dynamic holes           |
| Using deprecated PPR configuration               | Build/configuration problems in Next.js 16                | Use Cache Components                                    |
| Overusing revalidation                           | Unnecessary cache invalidation                            | Define appropriate cache lifetimes                      |
| Not testing production builds                    | Rendering behavior may differ from development            | Test using `next build` and `next start`                |

## **19. Real-world example: Choose rendering strategies**

Suppose you are building a website with the following pages.

## **Example: E-commerce website**

[Electro Shop – E-commerce Website design by Wavespace – Web Design Agency for Wavespace - UI/UX Design Agency  on Dribbble](https://images.openai.com/static-rsc-4/OWW8z-rZ7MBMCjhDmEx9r-v5z6oKUtchawM8oFCppVYEL09whjsbDrvK7N2eMwwhockfdmGGuhY7zNCJYkpiXtlxc6QTOtVD4rEBba7gcZnJccnWDtTU4Kg3giCfTpXBOJgjTag11nfiljNsenIQciGV1o7S7cmNUgsBr9nC8-U?purpose=inline)

**Homepage**

Hybrid / PPR

Cached banners and product sections with personalized user information streamed separately.

[Hansoe - Ecommerce Sales Shoes by Muhammad Farhanudin on Dribbble](https://images.openai.com/static-rsc-4/Id7K2JNZ3yRo5ko9HWkjbWMB_Sxe6BAvTBc_qxQ5OFyLBs23stcmFnobwebsNikeaZH095khBhfbTXsG6n8x5usw0cZNG0LAPX4Q5Hpmj0qg1puFkIubjRkCEI0rTMij_o9Qg_uq3zO2WHZUiAsBSZtTsob3oc-C5AgYpBqcgcU?purpose=inline)

**Product listing**

Cached / Prerendered

Public catalog data can be cached and revalidated when products change.

[Modern eCommerce Website - Product Details Page by wincasoft for Wincasoft Official on Dribbble](https://images.openai.com/static-rsc-4/e9lm7jlfW1FhiQqdpzTp96vJH5Mf1YAh-ILWMtC321jyzuOvbRUl3B29rwY8QA9-dvxcHe_deYcep-xuTh1GgSXtuMxgdyaGBZ5qwtRauFo971jLpnAPHIY6AylK8oQubA85_rawrABVzy4oQpUenbvvRws9f9Bp9nvnsE86zwo?purpose=inline)

**Product details**

Static / Cached

Public product information can be prerendered or cached, with interactive cart controls.

[Kundenkonten – Hilfecenter](https://images.openai.com/static-rsc-4/f3soTJZi2PFg1GPknFMTwERJZcFM4-SovQHqif3tfpiPMaFQEiVy4KtNEPpmFb6KzudB5TtG82FzWPR8vWk7pMUl7MhpNzEhc0NU38E-nZU5GGf0ubY8TT0OLV4-gbdqoG7HTRbt_Jkd2_JaouXHsa8VCn-LtUznGxPmbj3tpxM?purpose=inline)

**User dashboard**

Dynamic

User-specific account data is retrieved after authentication.

[Checkout Payment Page :: Behance](https://images.openai.com/static-rsc-4/2rYV5c7ohVbPIOejdl1sGGQUg5JdaI7ykmKMnf6-ay4rqoPsjYMVQeLf1Mv-GbcCFW5n5IljmrJ_gndOasVGVA6liCE4IErxMVd_vJDc8DiC-11k_AEc1tbyHpY9JxrWTiIhyiZOOgWLTlMgV0Sd2fZiwq_CU4zqkJvw31n0Sws?purpose=inline)

**Checkout**

Dynamic

Order information, payment state, and user authorization require server-side verification.

### **Example implementation plan**

| Page            | Data source                | Rendering approach                       |
| --------------- | -------------------------- | ---------------------------------------- |
| Homepage        | Public content + session   | Cache Components + Suspense              |
| Products        | MongoDB product collection | Cached service                           |
| Product details | Product ID                 | `generateStaticParams()` where practical |
| Cart            | Authenticated user         | Server-side request data                 |
| Checkout        | Current order              | Request-time validation                  |
| Admin analytics | Authorized admin           | Dynamic data with streaming              |
| Search          | Search query               | Dynamic server results or CSR filters    |

This is how you make rendering decisions based on business requirements rather than applying one strategy everywhere.

## **20. Interview questions and answers**

### **Basic questions**

**Q1. What is SSG?**

Static Site Generation generates page output ahead of user requests, typically during the build process.

**Q2. What is SSR?**

Server-Side Rendering generates the relevant page output when a request arrives.

**Q3. What is ISR?**

Incremental Static Regeneration allows cached static output to be regenerated according to a time-based or on-demand revalidation strategy.

**Q4. What is CSR?**

Client-Side Rendering uses browser JavaScript to fetch or update data and render interactive UI.

**Q5. What is PPR?**

Partial Prerendering combines a prerendered static shell with dynamic sections that are rendered and streamed at request time.

**Q6. What is the difference between SSG and SSR?**

SSG generates reusable output ahead of requests, while SSR generates request-specific output when a request arrives.

### **Intermediate questions**

**Q7. What is&#x20;**`generateStaticParams()`**?**

It returns dynamic route parameters that Next.js can use to prerender routes ahead of time.

**Q8. What is the difference between a dynamic route and dynamic rendering?**

A dynamic route has a variable URL segment. Dynamic rendering means generating request-specific output. A dynamic route can still be prerendered.

**Q9. What is the benefit of ISR?**

It allows content to be reused from cache while still supporting periodic or on-demand updates without rebuilding the entire application.

**Q10. Why is CSR useful for dashboards?**

Dashboards often contain interactive filters, charts, and user-controlled state. Client-side rendering and data fetching can support these interactions effectively.

**Q11. What is the role of Suspense in streaming?**

Suspense defines a boundary where fallback UI can be shown while asynchronous content is not ready.

**Q12. Does a Client Component always mean CSR?**

No. Next.js can prerender Client Components on the server for the initial page load and hydrate them in the browser.

### **Advanced questions**

**Q13. What is the difference between streaming and PPR?**

Streaming progressively sends response content. PPR uses prerendering and Suspense boundaries to combine a static shell with dynamic content.

**Q14. How does Cache Components work in Next.js 16?**

It uses explicit caching directives and APIs such as `"use cache"`, `cacheLife()`, and `cacheTag()` to control reusable output while allowing request-time content.

**Q15. How would you render a personalized homepage efficiently?**

Prerender or cache the public sections, then place request-specific content such as a personalized greeting inside a Suspense boundary.

**Q16. Why should sensitive data not be placed in a shared cache?**

Because cached content may be reused across requests. Incorrect cache boundaries can expose one user's information to another user.

**Q17. When would you choose SSR over SSG?**

When content must be generated from request-specific data, such as session information, permissions, or personalized account details.

**Q18. When would you choose ISR-like caching over SSR?**

When content is public and reusable but does not need to be regenerated on every request, such as a frequently updated product catalog.

**Q19. What happens when you use&#x20;**`cookies()`**&#x20;inside a Server Component?**

It accesses request-specific cookie information and makes that part of the rendering dependent on the incoming request. With Cache Components, this runtime access should be handled outside cached scopes and placed behind an appropriate Suspense boundary.

**Q20. How does PPR improve perceived performance?**

It allows the server to send the prerendered shell without waiting for slower dynamic sections, which can stream in afterward.

## **21. Practical assignments**

Complete these exercises to understand rendering behavior through actual implementation.

### **Assignment 1 – Static generation**

- Create a blog application.
- Create dynamic article routes.
- Implement `generateStaticParams()`.
- Generate several article pages at build time.
- Test an unknown slug.
- Inspect the production build output.

### **Assignment 2 – Dynamic rendering**

- Create a user dashboard.
- Implement session-based authentication.
- Read cookies on the server.
- Fetch user-specific information.
- Verify that different users receive their own data.
- Ensure private data is not shared through a public cache.

### **Assignment 3 – ISR and revalidation**

- Create a public product listing.
- Configure a cache lifetime.
- Add a cache tag.
- Create an admin action that updates a product.
- Revalidate the product cache.
- Verify that updated information appears.

### **Assignment 4 – Client-Side Rendering**

- Create a searchable product list.
- Fetch products from an API.
- Implement loading states.
- Implement error states.
- Add filtering and sorting.
- Test user interactions.

### **Assignment 5 – Partial Prerendering**

- Enable `cacheComponents`.
- Create a static page shell.
- Add a cached product section.
- Add a personalized section using `cookies()`.
- Wrap the personalized section in Suspense.
- Verify the static and dynamic behavior in a production build.

### **Assignment 6 – Rendering strategy selection**

For each of these pages, decide which rendering approach is appropriate and explain why:

- Company homepage.
- Public blog article.
- Product catalog.
- User dashboard.
- Admin dashboard.
- Shopping cart.
- Checkout.
- Interactive analytics dashboard.

## **22. Quick revision notes**

| Concept                  | Remember                                         |
| ------------------------ | ------------------------------------------------ |
| SSG                      | Generate reusable output ahead of requests       |
| SSR                      | Generate output at request time                  |
| ISR                      | Reuse cached output and regenerate it            |
| CSR                      | Browser-side fetching and UI updates             |
| PPR                      | Static shell + dynamic sections                  |
| Prerendering             | Build-time or revalidation-time rendering        |
| Dynamic rendering        | Request-time rendering                           |
| `generateStaticParams()` | Generate dynamic paths ahead of time             |
| `dynamicParams`          | Control handling of ungenerated dynamic paths    |
| `cacheComponents`        | Enable Next.js 16 Cache Components               |
| `"use cache"`            | Mark output or data as cacheable                 |
| `cacheLife()`            | Define cache lifetime                            |
| `cacheTag()`             | Tag cached data                                  |
| `revalidateTag()`        | Mark tagged cache data stale or revalidate it    |
| `updateTag()`            | Immediately expire tagged data in Server Actions |
| `Suspense`               | Define fallback and streaming boundaries         |
| Streaming                | Send page content progressively                  |
| Hydration                | Attach React behavior to server-rendered HTML    |
| Hybrid rendering         | Combine multiple rendering strategies            |

### **Important configuration examples**

**Cache Components:**

```
const nextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

**Cached function:**

```
async function getProducts() {
  "use cache";

  return db.product.findMany();
}
```

**Cache lifetime:**

```
import { cacheLife } from "next/cache";

async function getProducts() {
  "use cache";

  cacheLife("hours");

  return db.product.findMany();
}
```

**Dynamic content:**

```
import { Suspense } from "react";

export default function Page() {
  return (
    <Suspense fallback={<p>Loading...</p>}>
      <DynamicContent />
    </Suspense>
  );
}
```

## **23. Phase 22 completion checklist**

**Phase 22 progress**

# **0/20**

Understand the rendering process

Understand SSG and prerendering

Generate dynamic routes using generateStaticParams

Understand SSR and request-time data

Understand ISR and cache revalidation

Understand CSR and browser-side fetching

Use SWR for client-side data fetching

Differentiate dynamic routes and dynamic rendering

Understand the previous App Router caching model

Understand Cache Components in Next.js 16

Configure cacheComponents

Use the use cache directive

Configure cacheLife profiles

Use cacheTag and revalidation

Understand Partial Prerendering

Build a static shell with dynamic content

Use Suspense for streaming

Understand hybrid rendering

Choose rendering strategies for real-world pages

Understand SEO and rendering performance

Reset progress

## **24. What's next?**

Next phase

# **Phase 23 – Advanced Performance Optimization and Web Vitals**

We will focus on making Next.js applications faster, more efficient, and more responsive.

Topics will include:

1. Core Web Vitals in depth.
2. LCP, INP, and CLS optimization.
3. React rendering performance.
4. Memoization with `useMemo` and `useCallback`.
5. React `memo`.
6. Code splitting and lazy loading.
7. Bundle analysis.
8. Reducing JavaScript bundle size.
9. Network and API optimization.
10. Performance profiling and real-world optimization.

When you're ready, say **OK, Phase 23**.

### **Official documentation**

- [generateStaticParams](https://nextjs.org/docs/app/api-reference/functions/generate-static-params?utm_source=chatgpt.com)
- [Next.js Linking and Navigating](https://nextjs.org/docs/app/getting-started/linking-and-navigating?utm_source=chatgpt.com)
- [Cache Components – use cache](https://nextjs.org/docs/app/api-reference/directives/use-cache?utm_source=chatgpt.com)
- [Revalidating cached data](https://nextjs.org/docs/app/getting-started/revalidating?utm_source=chatgpt.com)
- [Next.js 16 upgrade guide](https://nextjs.org/docs/app/guides/upgrading/version-16?utm_source=chatgpt.com)
- [Next.js production checklist](https://nextjs.org/docs/app/guides/production-checklist?utm_source=chatgpt.com)