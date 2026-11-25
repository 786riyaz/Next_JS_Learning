# PHASE 75 – ADVANCED FRONTEND ARCHITECTURE, NEXT.JS ENTERPRISE ENGINEERING, RENDERING STRATEGIES, WEB PERFORMANCE AND APPLICATION SECURITY

**Level:** Advanced → Expert  
**Domain:** React, Next.js, Frontend Architecture, Rendering, State Management, Performance Engineering, Web Security, Testing and Production Deployment.

## Phase 75 – Learning Objectives

By the end of this phase, you should be able to:

1. Understand React's internal rendering and reconciliation architecture.
2. Understand React Fiber and how React schedules rendering work.
3. Design enterprise-grade React applications.
4. Understand React Server Components and Client Components.
5. Master Next.js rendering strategies.
6. Understand SSR, SSG, ISR, CSR and streaming.
7. Implement Suspense and loading boundaries.
8. Understand Next.js caching and revalidation.
9. Build advanced applications using Server Actions.
10. Design scalable frontend state management.
11. Use TanStack Query, Zustand and Redux Toolkit appropriately.
12. Optimize Core Web Vitals.
13. Optimize JavaScript bundles and loading performance.
14. Implement frontend security measures.
15. Protect applications against XSS, CSRF and related attacks.
16. Build secure authentication flows.
17. Implement automated frontend testing using Playwright.
18. Deploy and monitor production-grade Next.js applications.

---

# SECTION 1 – ADVANCED REACT ARCHITECTURE

## 1.1 Understanding React Architecture

React is a JavaScript library for building user interfaces.

Its architecture revolves around:

- Components.
- Props.
- State.
- Rendering.
- Reconciliation.
- Scheduling.
- DOM updates.

A simplified React architecture:

```text
                 React Application
                        |
                        v
                  Component Tree
                        |
                        v
                  React Renderer
                        |
                        v
                  Render Phase
                        |
                        v
                  Reconciliation
                        |
                        v
                  Commit Phase
                        |
                        v
                    Browser DOM
                        |
                        v
                   Browser Paint
```

### Important React concepts

| Concept | Responsibility |
|---|---|
| Components | Define UI elements |
| Props | Pass data between components |
| State | Store component-specific information |
| Hooks | Provide React functionality to components |
| Reconciliation | Determine what needs to change |
| Fiber | React's internal representation of rendering work |
| Scheduler | Helps coordinate rendering priorities |
| Renderer | Applies updates to the target environment |

---

## 1.2 React Render and Commit Phases

React updates the interface through two major phases.

### Phase 1: Render Phase

During this phase, React calls components and calculates what the UI should look like.

```jsx
function Counter() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <h1>{count}</h1>

            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
        </div>
    );
}
```

When the state changes:

1. React schedules an update.
2. React calls the relevant component functions.
3. React calculates the new UI representation.
4. React determines what needs to change.

### Phase 2: Commit Phase

React applies the necessary changes to the DOM.

```text
State Update
     |
     v
Render Phase
     |
     v
Calculate New UI
     |
     v
Reconciliation
     |
     v
Commit Phase
     |
     v
DOM Updates
     |
     v
Browser Paint
```

### Important distinction

Rendering does not necessarily mean updating the DOM.

A component may render again, but React may determine that no DOM changes are necessary.

React's official documentation explains rendering and committing as separate stages. [React](https://react.dev/learn/render-and-commit?utm_source=chatgpt.com)

---

# SECTION 2 – REACT RECONCILIATION AND FIBER ARCHITECTURE

## 2.1 What Is Reconciliation?

Reconciliation is the process React uses to compare the previous UI representation with the new one and determine the required updates.

Consider:

```jsx
function App() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <h1>Counter</h1>
            <p>{count}</p>
        </div>
    );
}
```

When `count` changes:

```text
Previous UI             New UI
-----------             -------
Counter                 Counter
0                       1
```

React calculates that only the paragraph's text content needs to change.

It does not need to recreate the entire DOM tree.

## 2.2 How React Identifies Components

React uses information such as:

- Element type.
- Position in the component tree.
- Keys.
- Component identity.

Consider:

```jsx
function App() {
    return (
        <div>
            <UserProfile />
            <UserSettings />
        </div>
    );
}
```

React can identify the elements based on their position and type.

Changing the component type at a particular position can cause React to unmount the previous component and mount another one.

## 2.3 Importance of Keys

Consider:

```jsx
const users = [
    { id: 1, name: "John" },
    { id: 2, name: "David" },
    { id: 3, name: "Alex" }
];

function UserList() {
    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>
                    {user.name}
                </li>
            ))}
        </ul>
    );
}
```

React uses keys to identify individual elements across renders.

### Why should you avoid index keys?

Problematic:

```jsx
users.map((user, index) => (
    <UserCard key={index} user={user} />
));
```

Suppose the list is:

```text
Before:
0 John
1 David
2 Alex
```

After deleting John:

```text
After:
0 David
1 Alex
```

React may associate existing component state with the wrong list item because the positions have changed.

Better:

```jsx
users.map(user => (
    <UserCard key={user.id} user={user} />
));
```

Use stable, unique identifiers.

React also uses keys to determine whether component state should be preserved or reset. [React](https://react.dev/learn/rendering-lists?utm_source=chatgpt.com)

---

## 2.4 What Is React Fiber?

React Fiber is the internal reconciliation architecture introduced in React 16.

It represents rendering work as units that React can process and schedule.

### Why was Fiber introduced?

Older reconciliation approaches were more difficult to interrupt and prioritize.

Fiber provides a foundation for:

- Prioritized updates.
- Interruptible rendering.
- Concurrent rendering capabilities.
- Suspense integration.
- More flexible scheduling.

### Simplified Fiber architecture

```text
                React Application
                        |
                        v
                   Fiber Root
                        |
                        v
                  Fiber Tree
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
       Fiber A       Fiber B       Fiber C
          |             |             |
          v             v             v
       Children      Children      Children
                        |
                        v
                  Render Work
                        |
                        v
                  Commit Phase
                        |
                        v
                     DOM
```

A Fiber node stores information associated with a component or element, including its pending work, relationships in the tree and other reconciliation data.

### Important Fiber concepts

| Concept | Meaning |
|---|---|
| Fiber Node | Internal representation of a unit of work |
| Fiber Tree | Tree of Fiber nodes |
| Work-in-progress tree | Tree being prepared for a future commit |
| Current tree | Tree representing the committed UI |
| Scheduling | Determines when work should be processed |
| Priority | Helps determine which work should be handled first |

**Important:** Fiber is an internal implementation detail. You should understand its architecture, but application code should not depend on internal Fiber structures.

---

# SECTION 3 – REACT CONCURRENT RENDERING

## 3.1 What Is Concurrent Rendering?

Concurrent rendering allows React to prepare UI updates in a more flexible manner.

React can prioritize important interactions and allow certain rendering work to be interrupted.

For example:

Imagine a search page where users type into a search box while a large result list is being updated.

Without appropriate scheduling, expensive rendering can make typing feel slow.

Concurrent React features help manage such updates.

## 3.2 useTransition()

`useTransition()` allows you to mark certain state updates as non-urgent.

Example:

```jsx
import { useState, useTransition } from "react";

function SearchPage({ products }) {
    const [query, setQuery] = useState("");
    const [filteredProducts, setFilteredProducts] = useState(products);

    const [isPending, startTransition] = useTransition();

    function handleSearch(value) {
        setQuery(value);

        startTransition(() => {
            const result = products.filter(product =>
                product.name
                    .toLowerCase()
                    .includes(value.toLowerCase())
            );

            setFilteredProducts(result);
        });
    }

    return (
        <div>
            <input
                value={query}
                onChange={event => handleSearch(event.target.value)}
                placeholder="Search products"
            />

            {isPending && <p>Updating results...</p>}

            <ProductList products={filteredProducts} />
        </div>
    );
}
```

### Why useTransition?

It allows React to distinguish between:

- Urgent updates: typing into an input.
- Non-urgent updates: updating a large result list.

## 3.3 useDeferredValue()

`useDeferredValue()` allows a value used for rendering to lag behind the latest value.

Example:

```jsx
import { useState, useDeferredValue } from "react";

function SearchPage({ products }) {
    const [query, setQuery] = useState("");

    const deferredQuery = useDeferredValue(query);

    const filteredProducts = products.filter(product =>
        product.name
            .toLowerCase()
            .includes(deferredQuery.toLowerCase())
    );

    return (
        <>
            <input
                value={query}
                onChange={event => setQuery(event.target.value)}
            />

            <ProductList products={filteredProducts} />
        </>
    );
}
```

### useTransition vs useDeferredValue

| Feature | useTransition | useDeferredValue |
|---|---|---|
| Purpose | Marks updates as non-urgent | Defers a value |
| Controls | State updates inside transition | A value used for rendering |
| Pending state | Provides `isPending` | Does not provide an equivalent pending flag |
| Typical use | Non-urgent state updates | Expensive UI based on changing values |

Neither hook automatically makes expensive calculations faster. They help React schedule rendering work more effectively.

---

# SECTION 4 – ADVANCED REACT COMPONENT ARCHITECTURE

## 4.1 Component Composition

Component composition means building complex interfaces by combining smaller components.

Example:

```jsx
function Dashboard() {
    return (
        <DashboardLayout>
            <DashboardHeader />

            <DashboardContent>
                <Statistics />
                <RecentOrders />
                <RevenueChart />
            </DashboardContent>
        </DashboardLayout>
    );
}
```

Advantages:

- Reusability.
- Separation of concerns.
- Easier testing.
- Better maintainability.

## 4.2 Compound Components Pattern

Compound components work together to provide a flexible component API.

Example:

```jsx
function Tabs({ children }) {
    return <div className="tabs">{children}</div>;
}

Tabs.List = function TabList({ children }) {
    return <div className="tab-list">{children}</div>;
};

Tabs.Panel = function TabPanel({ children }) {
    return <div className="tab-panel">{children}</div>;
};
```

Usage:

```jsx
<Tabs>
    <Tabs.List>
        <button>Profile</button>
        <button>Settings</button>
    </Tabs.List>

    <Tabs.Panel>
        Profile information
    </Tabs.Panel>
</Tabs>
```

For a complete implementation, the components also need shared state or context to coordinate selection.

## 4.3 Controlled vs Uncontrolled Components

| Feature | Controlled | Uncontrolled |
|---|---|---|
| State | React state | DOM-managed state |
| Value access | State variable | Ref or form submission |
| Validation | Easy to integrate | Requires additional handling |
| Best use | Complex interactive forms | Simple inputs |

Controlled example:

```jsx
function Form() {
    const [name, setName] = useState("");

    return (
        <input
            value={name}
            onChange={event => setName(event.target.value)}
        />
    );
}
```

Uncontrolled example:

```jsx
function Form() {
    const inputRef = useRef(null);

    function handleSubmit(event) {
        event.preventDefault();

        console.log(inputRef.current.value);
    }

    return (
        <form onSubmit={handleSubmit}>
            <input ref={inputRef} />

            <button type="submit">
                Submit
            </button>
        </form>
    );
}
```

---

# SECTION 5 – REACT SERVER COMPONENTS

## 5.1 What Are React Server Components?

React Server Components (RSC) are components that render in a server environment rather than being executed as interactive components in the browser.

They can:

- Access server-side data sources.
- Query databases through server-side libraries.
- Access server-only modules.
- Render content without shipping their component implementation as browser JavaScript.

Server Components can be rendered during build time or through a server request.

React describes Server Components as components that render in an environment separate from the client application. [React](https://react.dev/reference/rsc/server-components?utm_source=chatgpt.com)

## 5.2 Server Components vs Client Components

| Feature | Server Components | Client Components |
|---|---|---|
| Execution | Server environment | Client environment after hydration, with possible server prerendering |
| Database access | Can access server-side data sources | Must communicate through a secure server boundary |
| useState | Not supported | Supported |
| useEffect | Not supported | Supported |
| Browser APIs | Not directly available | Available after client execution |
| Interactivity | Not directly interactive | Interactive |
| Client JavaScript | Component implementation is not shipped as client component code | Component code is included in the client bundle |
| Default in Next.js App Router | Yes | Must be explicitly declared |

## 5.3 Server Component Example

```tsx
// app/users/page.tsx

import { db } from "@/lib/database";

export default async function UsersPage() {
    const users = await db.user.findMany();

    return (
        <div>
            <h1>Users</h1>

            {users.map(user => (
                <div key={user.id}>
                    {user.name}
                </div>
            ))}
        </div>
    );
}
```

This component can retrieve data directly from a server-side database client.

There is no need to expose database credentials to the browser.

## 5.4 Client Component Example

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
    const [count, setCount] = useState(0);

    return (
        <button onClick={() => setCount(count + 1)}>
            Count: {count}
        </button>
    );
}
```

The `"use client"` directive marks the component as part of the client module graph.

### Important: The Server/Client Boundary

Consider:

```text
              Server Component
                     |
          +----------+----------+
          |                     |
          v                     v
       Database             Client Component
                                  |
                                  v
                              useState
                                  |
                                  v
                               onClick
```

You can import a Client Component into a Server Component.

However, a Client Component cannot directly import a Server Component as an ordinary nested component and expect it to execute on the server.

Instead, use composition patterns such as passing server-rendered content through a `children` prop.

Example:

```tsx
// app/page.tsx

import ClientPanel from "./ClientPanel";
import ServerContent from "./ServerContent";

export default function Page() {
    return (
        <ClientPanel>
            <ServerContent />
        </ClientPanel>
    );
}
```

The server renders `ServerContent` and passes its rendered representation through the component boundary.

### Best practices

1. Keep Client Components as small as practical.
2. Keep database access on the server.
3. Avoid adding `"use client"` to entire layouts unnecessarily.
4. Pass serializable props across the server/client boundary.
5. Do not pass secrets to Client Components.
6. Use Server Components for data-heavy, non-interactive UI.

---

# SECTION 6 – NEXT.JS ENTERPRISE ARCHITECTURE

## 6.1 Understanding Next.js App Router

The App Router uses the `app` directory to define application routing.

Example:

```text
app/
│
├── layout.tsx
├── page.tsx
├── loading.tsx
├── error.tsx
├── not-found.tsx
│
├── products/
│   ├── page.tsx
│   └── [id]/
│       └── page.tsx
│
├── dashboard/
│   ├── layout.tsx
│   ├── page.tsx
│   └── settings/
│       └── page.tsx
│
└── api/
    └── users/
        └── route.ts
```

### Important files

| File | Responsibility |
|---|---|
| `page.tsx` | Defines a route's UI |
| `layout.tsx` | Shared layout |
| `loading.tsx` | Loading UI |
| `error.tsx` | Error boundary UI |
| `not-found.tsx` | Not-found UI |
| `route.ts` | Route Handler |
| `template.tsx` | Layout-like wrapper with a new instance on navigation |
| `default.tsx` | Fallback for unmatched parallel route slots |

## 6.2 Enterprise Feature-Based Structure

For large applications, organize functionality into features.

```text
src/
│
├── app/
│   ├── (auth)/
│   ├── (dashboard)/
│   ├── products/
│   ├── orders/
│   └── api/
│
├── features/
│   ├── authentication/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── schemas/
│   │   └── types/
│   │
│   ├── products/
│   ├── orders/
│   └── inventory/
│
├── components/
│   ├── ui/
│   ├── shared/
│   └── layouts/
│
├── lib/
│   ├── database/
│   ├── auth/
│   ├── cache/
│   └── utils/
│
├── stores/
├── hooks/
├── types/
└── config/
```

### Why feature-based architecture?

Imagine an application with:

- Authentication.
- Products.
- Orders.
- Payments.
- Inventory.
- Reports.

A feature-based structure groups related components, services, schemas and types together.

This reduces coupling and makes large applications easier to maintain.

---

# SECTION 7 – NEXT.JS RENDERING STRATEGIES

Rendering strategy determines when and where HTML and UI content are generated.

This is one of the most important concepts in Next.js.

## 7.1 Client-Side Rendering (CSR)

In CSR, the browser executes JavaScript to generate or update the UI.

### Architecture

```text
          Browser Request
                 |
                 v
             Web Server
                 |
                 v
          HTML + JS Bundle
                 |
                 v
              Browser
                 |
                 v
          Execute JavaScript
                 |
                 v
            Fetch Data
                 |
                 v
            Render UI
```

Example:

```tsx
"use client";

import { useEffect, useState } from "react";

export default function Products() {
    const [products, setProducts] = useState([]);

    useEffect(() => {
        fetch("/api/products")
            .then(response => response.json())
            .then(setProducts);
    }, []);

    return (
        <div>
            {products.map(product => (
                <div key={product.id}>
                    {product.name}
                </div>
            ))}
        </div>
    );
}
```

Advantages:

- Interactive UI.
- Browser-based data fetching.
- Useful for private dashboard interfaces.

Disadvantages:

- Initial content may depend on JavaScript execution.
- Additional network requests may delay content.
- SEO can be more difficult if important content is not rendered appropriately.

## 7.2 Server-Side Rendering (SSR)

In SSR, the server renders HTML for a request.

### Architecture

```text
            Browser Request
                   |
                   v
               Next.js
                   |
                   v
             Fetch Data
                   |
                   v
             Render HTML
                   |
                   v
             Send Response
                   |
                   v
                Browser
                   |
                   v
               Hydration
                   |
                   v
             Interactive UI
```

Example concept:

```tsx
// Server Component

export default async function ProductsPage() {
    const response = await fetch(
        "https://api.example.com/products",
        {
            cache: "no-store"
        }
    );

    const products = await response.json();

    return <ProductList products={products} />;
}
```

This example explicitly requests fresh data rather than relying on a cached fetch response.

Advantages:

- Server-generated content.
- Suitable for request-specific data.
- Useful for personalized pages when configured correctly.

Disadvantages:

- Server work is required for each uncached request.
- Database and API latency can affect response time.
- Server capacity becomes important.

## 7.3 Static Site Generation (SSG)

SSG generates HTML ahead of time.

### Architecture

```text
             Build Process
                  |
                  v
             Fetch Data
                  |
                  v
             Generate HTML
                  |
                  v
             Static Files
                  |
                  v
                  CDN
                  |
                  v
                 Users
```

Example:

```tsx
// app/about/page.tsx

export default function AboutPage() {
    return (
        <main>
            <h1>About Our Company</h1>
        </main>
    );
}
```

A static, non-dynamic route can be prerendered by Next.js.

Advantages:

- Fast response delivery.
- CDN-friendly.
- Reduced runtime server work.
- Good for SEO-oriented content.

Disadvantages:

- Data may become stale until revalidation.
- Build-time generation can become expensive for very large datasets.

## 7.4 Incremental Static Regeneration (ISR)

ISR allows statically generated content to be regenerated or revalidated after deployment.

Example:

```tsx
// app/blog/page.tsx

export const revalidate = 60;

export default async function BlogPage() {
    const response = await fetch(
        "https://api.example.com/blog"
    );

    const posts = await response.json();

    return <BlogList posts={posts} />;
}
```

Conceptually, this allows cached page data to be refreshed according to the configured revalidation behavior.

### ISR workflow

```text
             User Request
                  |
                  v
             Cached Page
                  |
                  v
             Return HTML
                  |
                  v
          Revalidation Eligible
                  |
                  v
            Regenerate Data
                  |
                  v
             Update Cache
```

The precise caching and revalidation behavior depends on the Next.js version and configuration.

## 7.5 Rendering Strategy Comparison

| Strategy | When Rendering Happens | Typical Use |
|---|---|---|
| CSR | Browser | Interactive private interfaces |
| SSR | Server request | Dynamic or personalized pages |
| SSG | Build time | Static content |
| ISR | Build time plus revalidation | Frequently updated public content |
| Streaming SSR | Server streams content progressively | Pages with sections that become ready at different times |

Next.js supports static and dynamic rendering approaches through its routing and rendering architecture. [Next.js](https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering?utm_source=chatgpt.com)

---

# SECTION 8 – STREAMING SSR AND REACT SUSPENSE

## 8.1 What Is Streaming SSR?

Traditional SSR may wait for the complete page before sending the final HTML response.

Streaming SSR allows the server to send parts of the rendered page as they become available.

### Traditional SSR

```text
Request
   |
   v
Fetch All Data
   |
   v
Render Complete Page
   |
   v
Send HTML
```

### Streaming SSR

```text
Request
   |
   v
Render Page Shell
   |
   +------------------+
   |                  |
   v                  v
Send Header       Fetch Data
                      |
                      v
                 Send Content
                      |
                      v
                 Send Remaining UI
```

This can improve perceived loading performance.

## 8.2 React Suspense

Suspense allows a component tree to display fallback content while supported content is not ready.

Example:

```tsx
import { Suspense } from "react";

export default function Dashboard() {
    return (
        <main>
            <h1>Dashboard</h1>

            <Suspense fallback={<p>Loading statistics...</p>}>
                <Statistics />
            </Suspense>
        </main>
    );
}

async function Statistics() {
    const statistics = await getStatistics();

    return <div>{statistics.totalUsers}</div>;
}
```

### Suspense architecture

```text
                Dashboard
                    |
          +---------+---------+
          |                   |
          v                   v
        Header             Suspense
                               |
                       +-------+-------+
                       |               |
                       v               v
                   Fallback       Async Content
                  Loading UI       Statistics
                                       |
                                       v
                                  Loaded UI
```

## 8.3 Next.js loading.tsx

Example:

```text
app/
│
└── dashboard/
    ├── loading.tsx
    └── page.tsx
```

`loading.tsx`:

```tsx
export default function Loading() {
    return (
        <div className="loading">
            Loading dashboard...
        </div>
    );
}
```

Next.js can use this file to provide loading UI for the route segment.

## 8.4 Nested Suspense Boundaries

You can independently stream different parts of a page.

```tsx
import { Suspense } from "react";

export default function Dashboard() {
    return (
        <>
            <Header />

            <Suspense fallback={<p>Loading revenue...</p>}>
                <Revenue />
            </Suspense>

            <Suspense fallback={<p>Loading orders...</p>}>
                <RecentOrders />
            </Suspense>
        </>
    );
}
```

The two sections can become available independently.

React's Suspense documentation explains fallback rendering, nested boundaries and integration with streaming server rendering. [React](https://react.dev/reference/react/Suspense?utm_source=chatgpt.com)

---

# SECTION 9 – PARTIAL PRERENDERING

## 9.1 What Is Partial Prerendering?

Partial Prerendering (PPR) is an approach that combines prerendered static content with dynamically generated content.

Conceptually, a page can contain:

- A static shell.
- Dynamic user-specific sections.
- Suspense boundaries.
- Content streamed when ready.

### Architecture

```text
                 Page
                  |
          +-------+-------+
          |               |
          v               v
      Static Shell    Dynamic Section
          |               |
          v               v
      Cached HTML      Request Data
          |               |
          +-------+-------+
                  |
                  v
            Final Response
```

Example concept:

```tsx
import { Suspense } from "react";

export default function ProductPage() {
    return (
        <main>
            <h1>Product Store</h1>

            <ProductDescription />

            <Suspense fallback={<p>Loading availability...</p>}>
                <LiveAvailability />
            </Suspense>
        </main>
    );
}
```

The product description may be prerenderable, while live availability requires request-time data.

### Important version consideration

PPR and related prerendering capabilities have evolved across Next.js releases.

Do not assume that enabling a particular experimental option from an older tutorial is still the recommended setup.

Check the documentation for the exact Next.js version used in your project. React also documents prerendering and resumable rendering APIs at the lower-level framework integration layer. [React](https://react.dev/reference/react-dom/static/prerender?utm_source=chatgpt.com)

---

# SECTION 10 – ADVANCED NEXT.JS DATA FETCHING AND CACHING

## 10.1 Data Fetching in Server Components

Server Components can fetch data directly.

```tsx
async function ProductList() {
    const response = await fetch(
        "https://api.example.com/products"
    );

    if (!response.ok) {
        throw new Error("Failed to fetch products");
    }

    const products = await response.json();

    return (
        <div>
            {products.map(product => (
                <div key={product.id}>
                    {product.name}
                </div>
            ))}
        </div>
    );
}
```

Avoid unnecessary client-side API calls when the data can safely be retrieved directly on the server.

## 10.2 Parallel Data Fetching

Sequential requests can create a request waterfall.

Problem:

```tsx
const user = await getUser();
const orders = await getOrders();
const products = await getProducts();
```

Each operation waits for the previous one.

If the requests are independent, use:

```tsx
const [user, orders, products] = await Promise.all([
    getUser(),
    getOrders(),
    getProducts()
]);
```

This allows the operations to execute concurrently.

**Important:** Do not parallelize operations when one genuinely depends on the result of another.

## 10.3 Next.js Caching Concepts

Caching exists at several levels.

| Cache | Purpose |
|---|---|
| Request-level memoization | Avoid duplicate work within a render/request context |
| Data Cache | Reuse eligible fetched or cached data |
| Full Route Cache | Reuse prerendered route output where applicable |
| Client Router Cache | Reuse previously fetched route segments during navigation |
| Browser Cache | Reuse browser-stored assets and responses |
| CDN Cache | Distribute reusable content closer to users |

The exact behavior depends on the Next.js version, configuration, rendering mode and data-fetching API.

### Important: Next.js 15 and Next.js 16

Do not blindly copy older Next.js caching examples.

Next.js caching defaults and configuration have evolved. The behavior of `fetch`, route rendering and cache invalidation should be checked against the installed major version.

The Next.js 16 release is in Active LTS, while Next.js 15 is in Maintenance LTS according to the current support policy. [Next.js](https://nextjs.org/support-policy?utm_source=chatgpt.com)

## 10.4 Revalidation

Next.js provides mechanisms to invalidate or refresh cached content.

Examples include:

- `revalidatePath()`
- `revalidateTag()`
- `updateTag()` where supported by the installed version.

Example:

```tsx
"use server";

import { revalidatePath } from "next/cache";

export async function updateProduct() {
    // Update product in the database.

    revalidatePath("/products");
}
```

The exact invalidation behavior differs between APIs and versions.

## 10.5 Cache Design Best Practices

1. Cache public, reusable data where appropriate.
2. Avoid caching sensitive user-specific responses globally.
3. Design cache keys carefully.
4. Invalidate affected data after mutations.
5. Set appropriate expiration policies.
6. Understand browser, CDN and Next.js caches separately.
7. Test behavior in production mode.
8. Do not assume development-mode behavior matches production.

Official resource: [Next.js Documentation](https://nextjs.org/docs)

---

# SECTION 11 – SERVER ACTIONS AND SERVER FUNCTIONS

## 11.1 What Are Server Actions?

Server Actions allow supported asynchronous functions to execute on the server and be invoked from application UI.

They are useful for:

- Creating records.
- Updating records.
- Deleting records.
- Submitting forms.
- Performing server-side mutations.

Example:

```tsx
// app/actions.ts

"use server";

export async function createUser(formData: FormData) {
    const name = formData.get("name");

    // Validate input.
    // Authenticate user.
    // Authorize operation.
    // Save data to database.

    return {
        success: true
    };
}
```

Form:

```tsx
import { createUser } from "./actions";

export default function UserForm() {
    return (
        <form action={createUser}>
            <input
                type="text"
                name="name"
                required
            />

            <button type="submit">
                Create User
            </button>
        </form>
    );
}
```

## 11.2 Server Action Security

A Server Action is an externally invocable server entry point.

Never assume that only authorized UI users can invoke it.

Every sensitive action must independently verify:

1. Authentication.
2. Authorization.
3. Input validation.
4. Resource ownership.
5. Tenant access.
6. Business rules.

### Example

```tsx
"use server";

import { z } from "zod";
import { auth } from "@/lib/auth";
import { db } from "@/lib/database";

const schema = z.object({
    productId: z.string().min(1),
    name: z.string().min(2).max(100)
});

export async function updateProduct(formData: FormData) {
    const session = await auth();

    if (!session?.user) {
        throw new Error("Unauthorized");
    }

    const parsed = schema.safeParse({
        productId: formData.get("productId"),
        name: formData.get("name")
    });

    if (!parsed.success) {
        return {
            success: false,
            errors: parsed.error.flatten()
        };
    }

    const product = await db.product.findUnique({
        where: {
            id: parsed.data.productId
        }
    });

    if (!product) {
        throw new Error("Product not found");
    }

    if (product.ownerId !== session.user.id) {
        throw new Error("Forbidden");
    }

    await db.product.update({
        where: {
            id: product.id
        },
        data: {
            name: parsed.data.name
        }
    });

    return {
        success: true
    };
}
```

This is illustrative. Production code should use appropriate typed error handling and authorization policies rather than exposing internal error messages.

Next.js documents Server Actions as server-side mutation mechanisms integrated with forms and cache revalidation. [Next.js](https://nextjs.org/learn/dashboard-app/mutating-data?utm_source=chatgpt.com)

---

# SECTION 12 – ADVANCED ROUTING AND NAVIGATION

## 12.1 Dynamic Routes

Example:

```text
app/
│
└── products/
    └── [id]/
        └── page.tsx
```

Route:

```text
/products/123
```

The route parameter identifies the product.

## 12.2 Route Groups

Route groups organize routes without affecting their URL paths.

```text
app/
│
├── (auth)/
│   ├── login/
│   └── register/
│
├── (dashboard)/
│   ├── dashboard/
│   └── settings/
│
└── products/
```

Parentheses indicate a route group.

## 12.3 Parallel Routes

Parallel Routes allow multiple route segments to render simultaneously in the same layout.

Useful for:

- Dashboards.
- Multi-panel interfaces.
- Independent modals.
- Different dashboard sections.

## 12.4 Intercepting Routes

Intercepting Routes allow an application to display a route in another part of the interface under supported navigation conditions.

Example use case:

A user clicks a product card.

Instead of navigating to an entirely separate page, the product detail page opens in a modal while preserving the background listing.

### Routing comparison

| Feature | Purpose |
|---|---|
| Dynamic Routes | Handle variable URL segments |
| Route Groups | Organize routes without changing URLs |
| Parallel Routes | Render multiple route slots |
| Intercepting Routes | Display another route within the current navigation context |
| Middleware / Proxy | Perform request-level processing according to the current Next.js version |

---

# SECTION 13 – FRONTEND STATE MANAGEMENT ARCHITECTURE

## 13.1 Types of Frontend State

Not all state should be stored in the same place.

| State Type | Example | Recommended Approach |
|---|---|---|
| Local UI State | Modal open/closed | useState |
| Shared UI State | Sidebar visibility | Context or Zustand |
| Server State | Products from API | TanStack Query or SWR |
| URL State | Search and filters | URL search parameters |
| Form State | Form values and errors | React Hook Form or local state |
| Persistent State | Theme preference | Local storage or server persistence as appropriate |

### Important principle

Do not place every application value inside Redux or a global state store.

Use the simplest state-management mechanism that fits the problem.

## 13.2 Context API

Context allows values to be shared through a component tree.

Example:

```tsx
"use client";

import {
    createContext,
    useContext,
    useState
} from "react";

const ThemeContext = createContext(null);

export function ThemeProvider({ children }) {
    const [theme, setTheme] = useState("light");

    return (
        <ThemeContext.Provider value={{ theme, setTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

export function useTheme() {
    return useContext(ThemeContext);
}
```

Context is useful for relatively stable shared values.

However, changing a context value can cause consumers of that context to re-render.

For large, frequently updated state, consider a more targeted state-management solution.

## 13.3 Zustand

Zustand is a lightweight state-management library.

Example:

```tsx
import { create } from "zustand";

type CartStore = {
    count: number;
    increment: () => void;
    decrement: () => void;
};

export const useCartStore = create<CartStore>((set) => ({
    count: 0,

    increment: () =>
        set(state => ({
            count: state.count + 1
        })),

    decrement: () =>
        set(state => ({
            count: Math.max(0, state.count - 1)
        }))
}));
```

Usage:

```tsx
"use client";

import { useCartStore } from "@/stores/cart";

export default function CartCounter() {
    const count = useCartStore(state => state.count);

    return <h2>Cart: {count}</h2>;
}
```

## 13.4 Redux Toolkit

Redux Toolkit provides a structured approach to managing complex shared application state.

Example:

```tsx
import {
    createSlice,
    configureStore
} from "@reduxjs/toolkit";

const cartSlice = createSlice({
    name: "cart",

    initialState: {
        count: 0
    },

    reducers: {
        increment(state) {
            state.count += 1;
        },

        decrement(state) {
            state.count = Math.max(0, state.count - 1);
        }
    }
});

export const {
    increment,
    decrement
} = cartSlice.actions;

export const store = configureStore({
    reducer: {
        cart: cartSlice.reducer
    }
});
```

Redux Toolkit uses Immer internally to support convenient immutable update syntax.

## 13.5 TanStack Query

TanStack Query is designed for asynchronous server state.

It handles:

- Fetching.
- Caching.
- Refetching.
- Background updates.
- Request status.
- Mutations.
- Cache invalidation.

Example:

```tsx
"use client";

import { useQuery } from "@tanstack/react-query";

export function Products() {
    const { data, isLoading, error } = useQuery({
        queryKey: ["products"],

        queryFn: async () => {
            const response = await fetch("/api/products");

            if (!response.ok) {
                throw new Error("Failed to fetch products");
            }

            return response.json();
        }
    });

    if (isLoading) {
        return <p>Loading...</p>;
    }

    if (error) {
        return <p>Something went wrong</p>;
    }

    return (
        <div>
            {data.map(product => (
                <p key={product.id}>
                    {product.name}
                </p>
            ))}
        </div>
    );
}
```

## 13.6 State Management Comparison

| Feature | Context | Zustand | Redux Toolkit | TanStack Query |
|---|---|---|---|---|
| Local UI state | Possible | Yes | Yes | Not intended |
| Global client state | Yes | Yes | Yes | Not intended |
| Server-state caching | Not built in | Not built in | Additional tooling needed | Core feature |
| Async data fetching | Manual | Manual or middleware | Thunks/RTK Query | Core feature |
| Boilerplate | Low | Low | Moderate | Moderate |
| Best use | Shared stable values | Lightweight global state | Complex client state | API/server state |

### Recommended enterprise approach

```text
                 Application State
                        |
        +---------------+---------------+
        |               |               |
        v               v               v
    Local State     Global State    Server State
        |               |               |
        v               v               v
     useState        Zustand       TanStack Query
        |               |               |
        +---------------+---------------+
                        |
                        v
                    React UI
```

---

# SECTION 14 – FRONTEND PERFORMANCE OPTIMIZATION

## 14.1 What Is Frontend Performance?

Frontend performance describes how quickly and smoothly an application loads, displays content and responds to user interactions.

Important performance areas:

- Initial page load.
- JavaScript execution.
- Rendering.
- Network requests.
- Image delivery.
- Font loading.
- Interaction responsiveness.
- Layout stability.

## 14.2 React.memo()

`React.memo()` can prevent unnecessary re-rendering when a component receives unchanged props.

```tsx
import { memo } from "react";

const UserCard = memo(function UserCard({ user }) {
    return (
        <div>
            <h3>{user.name}</h3>
            <p>{user.email}</p>
        </div>
    );
});
```

However, memoization is not automatically beneficial.

It introduces comparison work and can complicate code if used unnecessarily.

## 14.3 useMemo()

`useMemo()` caches the result of a calculation between renders when its dependencies have not changed.

```tsx
const filteredProducts = useMemo(() => {
    return products.filter(product =>
        product.name.includes(search)
    );
}, [products, search]);
```

Use it when the calculation is expensive enough to justify memoization.

## 14.4 useCallback()

`useCallback()` caches a function definition between renders.

```tsx
const handleDelete = useCallback((id) => {
    setProducts(current =>
        current.filter(product => product.id !== id)
    );
}, []);
```

This is especially useful when passing callbacks to memoized child components or when stable function identity matters.

### Comparison

| Hook | Purpose |
|---|---|
| useMemo | Memoizes a computed value |
| useCallback | Memoizes a function reference |
| React.memo | Can skip component rendering when props are unchanged |

## 14.5 Code Splitting

Code splitting divides application JavaScript into smaller chunks.

Instead of loading everything at once, the application loads the code required for the current page or interaction.

Example:

```tsx
import dynamic from "next/dynamic";

const AnalyticsChart = dynamic(
    () => import("@/components/AnalyticsChart"),
    {
        loading: () => <p>Loading chart...</p>
    }
);

export default function Dashboard() {
    return (
        <div>
            <h1>Dashboard</h1>
            <AnalyticsChart />
        </div>
    );
}
```

Use lazy loading for components that are not required immediately, such as:

- Large charts.
- Rich text editors.
- Maps.
- Complex modal interfaces.
- Heavy visualization libraries.

## 14.6 Bundle Optimization

A JavaScript bundle contains code delivered to the browser.

Large bundles can cause:

- Slow downloads.
- More JavaScript parsing.
- More execution time.
- Increased memory consumption.

Optimization techniques:

1. Remove unused dependencies.
2. Use tree-shakeable imports.
3. Lazy-load large components.
4. Avoid unnecessarily large UI libraries.
5. Analyze bundle composition.
6. Split code by route and feature.
7. Keep Client Component boundaries small.

## 14.7 Image Optimization

Images often represent a large portion of webpage transfer size.

Next.js provides the Image component.

```tsx
import Image from "next/image";

export default function ProductImage() {
    return (
        <Image
            src="/products/laptop.jpg"
            alt="Laptop"
            width={600}
            height={400}
            priority
        />
    );
}
```

For an image that is likely to be the Largest Contentful Paint element, prioritize loading it appropriately.

For images below the fold, lazy loading is generally preferable.

Best practices:

- Use modern image formats where supported.
- Specify dimensions or aspect ratio.
- Serve responsive image sizes.
- Avoid oversized source images.
- Use appropriate compression.
- Prioritize the main above-the-fold image.
- Do not lazy-load the LCP image.

## 14.8 Font Optimization

Fonts can affect rendering performance and layout stability.

Next.js supports local and Google font integration through `next/font`.

Example:

```tsx
import { Inter } from "next/font/google";

const inter = Inter({
    subsets: ["latin"],
    display: "swap"
});

export default function RootLayout({ children }) {
    return (
        <html lang="en">
            <body className={inter.className}>
                {children}
            </body>
        </html>
    );
}
```

For production, verify font loading and rendering behavior with your actual deployment configuration.

---

# SECTION 15 – CORE WEB VITALS

Core Web Vitals are Google's user-experience metrics for evaluating loading, responsiveness and visual stability.

The three primary metrics are:

1. Largest Contentful Paint (LCP).
2. Interaction to Next Paint (INP).
3. Cumulative Layout Shift (CLS).

## 15.1 Largest Contentful Paint (LCP)

LCP measures when the largest visible content element is rendered.

Examples:

- Main hero image.
- Large heading.
- Main content image.

Good target:

| Rating | LCP |
|---|---:|
| Good | ≤ 2.5 seconds |
| Needs Improvement | 2.5–4 seconds |
| Poor | > 4 seconds |

### How to improve LCP

- Optimize server response time.
- Reduce render-blocking resources.
- Optimize hero images.
- Prioritize important resources.
- Improve CDN delivery.
- Avoid unnecessary client-side rendering for important content.

## 15.2 Interaction to Next Paint (INP)

INP measures responsiveness across user interactions.

Examples:

- Clicking buttons.
- Opening menus.
- Typing into inputs.
- Selecting filters.

Good target:

| Rating | INP |
|---|---:|
| Good | ≤ 200 ms |
| Needs Improvement | 200–500 ms |
| Poor | > 500 ms |

### How to improve INP

- Reduce long JavaScript tasks.
- Break up expensive calculations.
- Avoid unnecessary re-renders.
- Reduce event-handler complexity.
- Use transitions for non-urgent UI updates.
- Defer non-critical JavaScript.

## 15.3 Cumulative Layout Shift (CLS)

CLS measures unexpected visual movement during page loading and interaction.

Example:

```text
Initial Layout:

Header
Image
Button

After Image Loads:

Header
Large Image
Button moves downward
```

This can happen when image dimensions are not reserved.

Good target:

| Rating | CLS |
|---|---:|
| Good | ≤ 0.1 |
| Needs Improvement | 0.1–0.25 |
| Poor | > 0.25 |

### How to improve CLS

- Specify image dimensions.
- Reserve space for advertisements.
- Avoid injecting content above existing content.
- Use stable font-loading strategies.
- Avoid unexpected layout changes.

These thresholds are based on Google's Core Web Vitals guidance. [web.dev](https://web.dev/explore/learn-core-web-vitals?utm_source=chatgpt.com)

## 15.4 Core Web Vitals Summary

| Metric | Measures | Good Target |
|---|---|---:|
| LCP | Loading | ≤ 2.5 s |
| INP | Responsiveness | ≤ 200 ms |
| CLS | Visual stability | ≤ 0.1 |

---

# SECTION 16 – FRONTEND SECURITY ARCHITECTURE

Frontend security is not only about protecting the browser interface.

It also requires secure communication with backend systems and careful handling of user-controlled data.

## 16.1 Cross-Site Scripting (XSS)

XSS occurs when attackers inject malicious scripts into pages viewed by other users.

Example of dangerous content:

```html
<script>
    alert("XSS");
</script>
```

Imagine an application that displays user-submitted comments without proper output handling.

An attacker may submit malicious HTML or JavaScript.

### Types of XSS

| Type | Description |
|---|---|
| Stored XSS | Malicious content is stored and later displayed |
| Reflected XSS | Malicious content is reflected in a response |
| DOM-Based XSS | Client-side JavaScript introduces the vulnerability |

### Prevention

1. Escape untrusted output appropriately.
2. Avoid unsafe HTML injection.
3. Sanitize HTML when rich HTML is genuinely required.
4. Use Content Security Policy.
5. Keep dependencies updated.
6. Avoid unsafe DOM manipulation.

React normally escapes text inserted through JSX:

```jsx
function Comment({ message }) {
    return <p>{message}</p>;
}
```

Dangerous:

```jsx
function Comment({ message }) {
    return (
        <div
            dangerouslySetInnerHTML={{
                __html: message
            }}
        />
    );
}
```

Only use `dangerouslySetInnerHTML` with properly sanitized HTML.

---

## 16.2 Cross-Site Request Forgery (CSRF)

CSRF occurs when an attacker tricks a user's browser into sending an unwanted authenticated request to a website.

For example:

1. A user logs into a banking application.
2. The browser stores an authentication cookie.
3. The user visits a malicious website.
4. The malicious website attempts to submit a request to the banking application.
5. The browser may automatically attach the authentication cookie.

### CSRF protection

- Use appropriate `SameSite` cookie settings.
- Use anti-CSRF tokens where appropriate.
- Validate request origin where appropriate.
- Protect state-changing operations.
- Avoid relying on GET requests for mutations.

Example cookie:

```http
Set-Cookie: session=abc123; HttpOnly; Secure; SameSite=Lax; Path=/
```

Cookie settings must be chosen according to the application's authentication and cross-site requirements.

## 16.3 Content Security Policy (CSP)

CSP is a browser security mechanism that restricts which resources can be loaded or executed.

Example:

```http
Content-Security-Policy:
default-src 'self';
script-src 'self';
style-src 'self';
img-src 'self' https:;
connect-src 'self' https://api.example.com;
object-src 'none';
base-uri 'self';
frame-ancestors 'none';
```

### Important directives

| Directive | Purpose |
|---|---|
| default-src | Default resource policy |
| script-src | Controls script sources |
| style-src | Controls stylesheet sources |
| img-src | Controls image sources |
| connect-src | Controls fetch, XHR and related connections |
| font-src | Controls font sources |
| object-src | Controls object/embed sources |
| frame-ancestors | Controls which sites can embed the page |
| base-uri | Restricts document base URLs |

### CSP Best Practices

- Start with a carefully designed policy.
- Prefer nonces or hashes where inline scripts are required.
- Avoid broad use of `'unsafe-inline'`.
- Test using report-only mode.
- Monitor CSP violations.
- Ensure Next.js hydration and scripts are compatible with the policy.

---

# SECTION 17 – AUTHENTICATION AND AUTHORIZATION ARCHITECTURE

## 17.1 Authentication vs Authorization

| Feature | Authentication | Authorization |
|---|---|---|
| Purpose | Identifies the user | Determines permissions |
| Question | Who are you? | What can you access? |
| Example | Login | Admin permission |
| Typical mechanisms | Password, session, OAuth | Roles, policies, permissions |

## 17.2 Session-Based Authentication

```text
             User Login
                 |
                 v
           Backend Validation
                 |
                 v
          Create Session
                 |
                 v
          Set Secure Cookie
                 |
                 v
             Browser
                 |
                 v
         Authenticated Request
                 |
                 v
        Validate Session Cookie
                 |
                 v
           Allow / Reject
```

For browser applications, secure HttpOnly cookies are often a good choice for session identifiers.

## 17.3 JWT Authentication

JWTs are signed tokens containing claims.

Example payload:

```json
{
  "sub": "user_123",
  "role": "admin",
  "iat": 1795600000,
  "exp": 1795603600
}
```

A JWT is not automatically encrypted.

Do not place secrets or sensitive personal data in an ordinary signed JWT payload.

## 17.4 Access Token vs Refresh Token

| Feature | Access Token | Refresh Token |
|---|---|---|
| Purpose | Access protected resources | Obtain new access tokens |
| Lifetime | Usually shorter | Usually longer |
| Exposure impact | Limits access duration | Can maintain a session |
| Storage | Depends on architecture | Requires strong protection |
| Rotation | May be renewed | Should support secure rotation where applicable |

### Security best practices

- Use short-lived access tokens where appropriate.
- Rotate refresh tokens.
- Detect refresh-token reuse.
- Secure cookie configuration.
- Validate token issuer and audience.
- Use HTTPS.
- Implement logout and session revocation.
- Do not store authentication secrets in `localStorage` without understanding the XSS implications.

## 17.5 Role-Based Access Control (RBAC)

Example:

```text
             User
              |
       +------+------+
       |      |      |
       v      v      v
     Admin  Manager Customer
       |      |      |
       v      v      v
     All    Manage   View
   Permissions Orders Profile
```

Example permissions:

```typescript
type Role = "admin" | "manager" | "customer";

const permissions = {
    admin: [
        "users:read",
        "users:write",
        "orders:read",
        "orders:write"
    ],

    manager: [
        "orders:read",
        "orders:write"
    ],

    customer: [
        "orders:read:self"
    ]
};
```

Never depend only on hiding buttons in the frontend.

The backend must enforce authorization independently.

---

# SECTION 18 – FRONTEND API SECURITY

## 18.1 Input Validation

Validate user input both on the frontend and backend.

Frontend validation improves user experience.

Backend validation protects the actual application.

Example using Zod:

```typescript
import { z } from "zod";

const userSchema = z.object({
    name: z.string().min(2).max(100),

    email: z.string().email(),

    age: z.number().int().min(18).max(120)
});

const result = userSchema.safeParse({
    name: "John",
    email: "john@example.com",
    age: 25
});

if (!result.success) {
    console.log(result.error.flatten());
}
```

## 18.2 CORS

Cross-Origin Resource Sharing controls which browser origins are permitted to access certain resources.

Example:

```http
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

### Important

CORS is not an authentication mechanism.

It does not prevent all non-browser clients from calling your backend.

It primarily controls browser access to cross-origin responses.

## 18.3 Rate Limiting

Rate limiting restricts how frequently a client can perform operations.

Example:

```text
IP Address: 192.168.1.10

Allowed Requests: 100
Window: 60 seconds
```

When the limit is exceeded:

```http
HTTP/1.1 429 Too Many Requests
```

Use rate limiting for:

- Login attempts.
- Password reset.
- Search endpoints.
- Public APIs.
- Expensive operations.

For distributed deployments, use a centralized or shared rate-limiting mechanism rather than relying only on per-process memory.

---

# SECTION 19 – FRONTEND TESTING ARCHITECTURE

## 19.1 Types of Frontend Testing

| Test Type | Purpose |
|---|---|
| Unit Testing | Tests individual functions/components |
| Integration Testing | Tests interactions between components/modules |
| Component Testing | Tests component behavior |
| End-to-End Testing | Tests complete user workflows |
| Visual Regression Testing | Detects unexpected visual changes |
| Accessibility Testing | Detects accessibility issues |
| Performance Testing | Measures performance behavior |

## 19.2 Testing Pyramid

```text
                 E2E Tests
                /         \
               /           \
         Integration Tests
          /               \
         /                 \
              Unit Tests
```

Generally:

- Unit tests should be numerous and fast.
- Integration tests should cover important interactions.
- E2E tests should cover critical user journeys.

## 19.3 Playwright

Playwright is an end-to-end testing framework for web applications.

It supports:

- Chromium.
- Firefox.
- WebKit.
- Browser automation.
- Assertions.
- Screenshots.
- Traces.
- Parallel testing.
- Mobile emulation.

Official documentation: [Playwright](https://playwright.dev/docs/intro)

### Installation

```bash
npm init playwright@latest
```

## 19.4 Example Playwright Test

```typescript
import {
    test,
    expect
} from "@playwright/test";

test("user can log in", async ({ page }) => {
    await page.goto("http://localhost:3000/login");

    await page.getByLabel("Email").fill(
        "john@example.com"
    );

    await page.getByLabel("Password").fill(
        "password123"
    );

    await page.getByRole("button", {
        name: "Login"
    }).click();

    await expect(page).toHaveURL(
        /dashboard/
    );
});
```

Prefer accessible locators such as:

- `getByRole()`
- `getByLabel()`
- `getByText()`
- `getByPlaceholder()`

Avoid brittle selectors tied to deeply nested CSS structures.

## 19.5 Testing Accessibility

Automated accessibility tests can identify issues such as:

- Missing accessible labels.
- Invalid ARIA attributes.
- Duplicate IDs.
- Some contrast problems.

Example using axe:

```typescript
import { test, expect } from "@playwright/test";
import AxeBuilder from "@axe-core/playwright";

test("homepage accessibility", async ({ page }) => {
    await page.goto("http://localhost:3000");

    const results = await new AxeBuilder({
        page
    }).analyze();

    expect(results.violations).toEqual([]);
});
```

Automated accessibility testing cannot detect every accessibility issue. Manual keyboard, screen-reader and usability testing remain important. [Playwright](https://playwright.dev/docs/accessibility-testing?utm_source=chatgpt.com)

## 19.6 Testing Best Practices

1. Test critical user journeys.
2. Use stable locators.
3. Avoid arbitrary fixed delays.
4. Use isolated test data.
5. Mock external services when appropriate.
6. Test error states.
7. Test authentication and authorization.
8. Run tests in CI.
9. Capture traces for failures.
10. Include accessibility checks.

---

# SECTION 20 – NEXT.JS PRODUCTION DEPLOYMENT

## 20.1 Production Build

Run:

```bash
npm run build
```

Then:

```bash
npm run start
```

Do not evaluate production performance only through `npm run dev`.

Development mode includes additional tooling and behavior that can distort performance measurements.

## 20.2 Environment Variables

Example:

```env
DATABASE_URL=mysql://user:password@localhost:3306/app
AUTH_SECRET=your-secret
API_INTERNAL_URL=https://api.example.com
NEXT_PUBLIC_APP_URL=https://example.com
```

### Important distinction

Variables prefixed with `NEXT_PUBLIC_` are intended to be exposed to browser-side code.

Never place secrets in public environment variables.

## 20.3 Deployment Architecture

```text
                   Internet
                      |
                      v
                  Cloudflare
                      |
                      v
                   Nginx
                      |
                      v
                 Next.js App
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
        MySQL       Redis      Storage
          |
          v
      Backups
```

Possible deployment platforms:

- Vercel.
- AWS.
- Google Cloud.
- Azure.
- Docker-based VPS deployments.

## 20.4 Production Checklist

- [ ] Production build succeeds.
- [ ] Environment variables are validated.
- [ ] Database credentials are protected.
- [ ] Authentication is secure.
- [ ] Authorization is enforced server-side.
- [ ] CSP is configured.
- [ ] HTTPS is enabled.
- [ ] CORS is restricted appropriately.
- [ ] Rate limiting is configured.
- [ ] Images are optimized.
- [ ] Core Web Vitals are measured.
- [ ] Error monitoring is enabled.
- [ ] Logs do not contain secrets.
- [ ] Database backups are configured.
- [ ] CI tests pass.
- [ ] Dependency vulnerabilities are reviewed.
- [ ] Deployment rollback is possible.

---

# SECTION 21 – COMPLETE ENTERPRISE PROJECT

## Project: Enterprise E-Commerce Frontend Using Next.js

Build a complete enterprise-grade frontend connected to a backend API.

### Technology Stack

| Layer | Technology |
|---|---|
| Framework | Next.js |
| UI Library | React |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Form Handling | React Hook Form |
| Validation | Zod |
| Server State | TanStack Query |
| Client State | Zustand |
| Authentication | Secure session-based authentication |
| Testing | Playwright |
| Accessibility | axe-core |
| Monitoring | Web Vitals and error monitoring |

### 21.1 Application Modules

**Authentication**
- Login.
- Registration.
- Logout.
- Forgot password.
- Session management.
- Role-based UI.

**Products**
- Product listing.
- Product detail.
- Search.
- Filtering.
- Sorting.
- Cursor pagination.

**Shopping Cart**
- Add to cart.
- Remove from cart.
- Update quantity.
- Calculate totals.

**Checkout**
- Address selection.
- Shipping details.
- Payment integration.
- Order confirmation.

**User Dashboard**
- Profile.
- Orders.
- Addresses.
- Account settings.

**Admin Dashboard**
- Product management.
- Order management.
- User management.
- Sales analytics.
- Inventory overview.

### 21.2 Architecture

```text
                     Browser
                        |
                        v
                  Next.js App
                        |
           +------------+------------+
           |            |            |
           v            v            v
       Server       Client       Middleware /
     Components   Components     Proxy Layer
           |            |            |
           |            v            |
           |        Zustand          |
           |        Query Cache      |
           |                         |
           +------------+------------+
                        |
                        v
                    API Layer
                        |
                        v
                 Backend Services
                        |
             +----------+----------+
             |          |          |
             v          v          v
           MySQL      Redis     Storage
```

### 21.3 Performance Requirements

Example project targets:

| Metric | Target |
|---|---:|
| LCP | ≤ 2.5 seconds |
| INP | ≤ 200 ms |
| CLS | ≤ 0.1 |
| JavaScript bundle | Keep route-specific bundles lean |
| API error handling | Consistent and user-friendly |
| Accessibility | No known critical automated violations |
| E2E coverage | All critical user journeys |
| Authentication | Server-enforced authorization |

These are project targets, not guaranteed outcomes.

### 21.4 Architecture Rules

1. Keep server-only code separate from client code.
2. Avoid putting secrets in browser bundles.
3. Use Server Components for server-side data access.
4. Use Client Components only where browser interactivity is needed.
5. Keep server state separate from local UI state.
6. Use URL parameters for shareable filters and search.
7. Validate all mutations on the server.
8. Implement authorization on the backend.
9. Use caching only when data consistency permits it.
10. Measure performance before optimizing.

---

# SECTION 22 – INTERVIEW QUESTIONS

## Beginner-Level Questions

1. What is React?
2. What is the difference between React and Next.js?
3. What is a React component?
4. What is reconciliation?
5. What is the React render phase?
6. What is the commit phase?
7. What is React Fiber?
8. What is hydration?
9. What are Server Components?
10. What are Client Components?
11. What is the App Router?
12. What is SSR?
13. What is SSG?
14. What is CSR?
15. What is ISR?

## Intermediate-Level Questions

16. Explain React's reconciliation process.
17. Why are keys important in React?
18. What is concurrent rendering?
19. What is the difference between `useMemo` and `useCallback`?
20. What is the difference between `React.memo` and `useMemo`?
21. What is Suspense?
22. What is streaming SSR?
23. What is Partial Prerendering?
24. What is the difference between Server and Client Components?
25. What is a Server Action?
26. What are Route Groups?
27. What are Parallel Routes?
28. What is the difference between Context and Zustand?
29. What is TanStack Query?
30. What is code splitting?

## Advanced-Level Questions

31. Explain React Fiber architecture.
32. How does React determine whether to preserve component state?
33. What is the difference between `useTransition` and `useDeferredValue`?
34. How do Server Components reduce client JavaScript?
35. How does hydration work?
36. What causes hydration mismatches?
37. Explain SSR, SSG and ISR with practical examples.
38. How does streaming improve perceived performance?
39. How do Suspense boundaries affect rendering?
40. Explain the Next.js caching layers.
41. How do you invalidate cached data after a mutation?
42. How do you prevent unnecessary Client Components?
43. How would you design state management for an enterprise application?
44. How do you optimize a large Next.js bundle?
45. How do you improve LCP, INP and CLS?

## Expert-Level Questions

46. How would you design an enterprise Next.js architecture with hundreds of routes?
47. How would you identify and fix hydration mismatches?
48. How would you optimize a Next.js application with poor p95 interaction performance?
49. How would you design a secure Server Action architecture?
50. How would you prevent XSS and CSRF attacks?
51. How would you implement a robust CSP in a Next.js application?
52. How would you design secure session management?
53. How would you prevent cross-tenant data exposure through caching?
54. How would you architect a large application using React Server Components?
55. How would you test a complex e-commerce application using Playwright?
56. How would you measure real-user Core Web Vitals?
57. How would you reduce JavaScript execution time?
58. How would you design a frontend micro-frontend architecture?
59. How would you handle authentication across multiple frontend applications?
60. How would you design a highly available Next.js application?

---

# SECTION 23 – PRACTICAL ASSIGNMENTS

Complete these assignments progressively.

## Level 1 – React Architecture

1. Draw React's component architecture.
2. Explain the render and commit phases.
3. Create a component tree for an e-commerce application.
4. Demonstrate React reconciliation.
5. Create a list using stable keys.
6. Demonstrate why index keys can cause problems.
7. Demonstrate component state preservation.
8. Demonstrate state reset using keys.
9. Implement compound components.
10. Implement controlled and uncontrolled forms.

## Level 2 – Fiber and Concurrent React

11. Study the React Fiber architecture.
12. Explain the current and work-in-progress trees.
13. Implement `useTransition`.
14. Implement `useDeferredValue`.
15. Compare urgent and non-urgent updates.
16. Build a large searchable list.
17. Measure rendering performance.
18. Use React Profiler.
19. Optimize an expensive component.
20. Demonstrate the effects of unnecessary re-renders.

## Level 3 – Server Components

21. Build a Server Component.
22. Build a Client Component.
23. Fetch data directly inside a Server Component.
24. Implement a Client Component inside a Server Component.
25. Pass server-rendered content through `children`.
26. Demonstrate server-only module protection.
27. Identify hydration mismatches.
28. Build an interactive product listing.
29. Reduce unnecessary `"use client"` boundaries.
30. Design a Server/Client Component architecture.

## Level 4 – Next.js Architecture

31. Build an enterprise Next.js project structure.
32. Implement nested layouts.
33. Implement dynamic routes.
34. Implement route groups.
35. Implement Parallel Routes.
36. Implement Intercepting Routes.
37. Implement Route Handlers.
38. Implement loading boundaries.
39. Implement error boundaries.
40. Build an admin dashboard architecture.

## Level 5 – Rendering and Caching

41. Build a CSR page.
42. Build a server-rendered page.
43. Build a statically rendered page.
44. Implement ISR.
45. Implement streaming SSR.
46. Implement nested Suspense boundaries.
47. Implement parallel data fetching.
48. Implement cache revalidation.
49. Test cache behavior in production mode.
50. Design a page combining static and dynamic sections.

## Level 6 – State Management

51. Implement Context API.
52. Implement Zustand.
53. Implement Redux Toolkit.
54. Implement TanStack Query.
55. Build a shopping cart using Zustand.
56. Build a product query using TanStack Query.
57. Implement query invalidation.
58. Implement optimistic updates.
59. Store search filters in URL parameters.
60. Design an enterprise state-management strategy.

## Level 7 – Performance Optimization

61. Implement React.memo.
62. Implement useMemo.
63. Implement useCallback.
64. Compare performance with and without memoization.
65. Implement dynamic imports.
66. Analyze the JavaScript bundle.
67. Optimize a large image.
68. Optimize font loading.
69. Implement responsive images.
70. Measure LCP, INP and CLS.

## Level 8 – Security

71. Demonstrate a safe approach to rendering untrusted text.
72. Study stored XSS.
73. Study reflected XSS.
74. Study DOM-based XSS.
75. Implement secure form validation.
76. Configure a CSP.
77. Implement secure session cookies.
78. Implement CSRF protection where required.
79. Configure CORS.
80. Implement frontend role-based UI with backend authorization.
81. Implement rate limiting on sensitive APIs.
82. Audit dependencies for vulnerabilities.

## Level 9 – Testing and Deployment

83. Install Playwright.
84. Write a login E2E test.
85. Test product search.
86. Test cart functionality.
87. Test checkout.
88. Test unauthorized access.
89. Implement accessibility testing.
90. Test mobile responsiveness.
91. Run production build tests.
92. Configure CI.
93. Deploy a Next.js application.
94. Configure environment variables.
95. Implement production error monitoring.

## Level 10 – Enterprise Project

96. Build the complete e-commerce frontend.
97. Implement authentication.
98. Implement product search and filtering.
99. Implement shopping cart.
100. Implement checkout.
101. Implement the user dashboard.
102. Implement the admin dashboard.
103. Integrate the backend API.
104. Optimize rendering and caching.
105. Implement security controls.
106. Add Playwright tests.
107. Measure Core Web Vitals.
108. Deploy the application.
109. Configure production monitoring.
110. Complete a security and performance audit.

---

# SECTION 24 – QUICK REVISION

| Topic | Key Concept |
|---|---|
| React | UI library |
| Reconciliation | Determines required UI changes |
| Fiber | React's internal rendering architecture |
| Render Phase | Calculates the next UI |
| Commit Phase | Applies changes |
| Hydration | Connects React behavior to server-generated HTML |
| Concurrent Rendering | Allows more flexible rendering scheduling |
| useTransition | Marks updates as non-urgent |
| useDeferredValue | Defers a value used for rendering |
| React Server Components | Server-executed React components |
| Client Components | Components with client-side interactivity |
| App Router | Next.js routing architecture |
| CSR | Browser-side rendering |
| SSR | Request-time server rendering |
| SSG | Build-time static rendering |
| ISR | Static rendering with revalidation |
| Suspense | Displays fallback UI while supported content is pending |
| Streaming SSR | Sends rendered content progressively |
| PPR | Combines prerendered and dynamic sections |
| Server Actions | Server-side mutation functions |
| Zustand | Lightweight client state management |
| Redux Toolkit | Structured global client state |
| TanStack Query | Server-state management |
| Code Splitting | Separates JavaScript into smaller chunks |
| Tree Shaking | Removes unused code |
| Lazy Loading | Loads code when needed |
| LCP | Largest Contentful Paint |
| INP | Interaction to Next Paint |
| CLS | Cumulative Layout Shift |
| XSS | Cross-Site Scripting |
| CSRF | Cross-Site Request Forgery |
| CSP | Content Security Policy |
| CORS | Cross-Origin Resource Sharing |
| Authentication | Identifies users |
| Authorization | Determines permissions |
| RBAC | Role-Based Access Control |
| Playwright | Browser automation and E2E testing |
| Accessibility | Making applications usable by people with different abilities |

---

# SECTION 25 – COMPLETION CHECKLIST

### React Architecture
- [ ] I understand reconciliation.
- [ ] I understand React Fiber.
- [ ] I understand render and commit phases.
- [ ] I understand keys and component identity.
- [ ] I can implement compound components.
- [ ] I understand concurrent rendering.

### React Server Components
- [ ] I understand Server Components.
- [ ] I understand Client Components.
- [ ] I understand server/client boundaries.
- [ ] I can fetch data in Server Components.
- [ ] I understand hydration.
- [ ] I can avoid unnecessary Client Components.

### Next.js Architecture
- [ ] I understand App Router.
- [ ] I can design enterprise project structures.
- [ ] I can implement dynamic routes.
- [ ] I understand Parallel Routes.
- [ ] I understand Intercepting Routes.
- [ ] I can implement loading and error boundaries.

### Rendering
- [ ] I understand CSR.
- [ ] I understand SSR.
- [ ] I understand SSG.
- [ ] I understand ISR.
- [ ] I understand streaming SSR.
- [ ] I understand Suspense.
- [ ] I understand Partial Prerendering.

### State Management
- [ ] I can use Context.
- [ ] I can use Zustand.
- [ ] I understand Redux Toolkit.
- [ ] I can use TanStack Query.
- [ ] I can separate server state and client state.

### Performance
- [ ] I understand React memoization.
- [ ] I can implement code splitting.
- [ ] I can optimize image loading.
- [ ] I can optimize font loading.
- [ ] I can measure LCP.
- [ ] I can measure INP.
- [ ] I can measure CLS.

### Security
- [ ] I understand XSS.
- [ ] I understand CSRF.
- [ ] I can configure CSP.
- [ ] I understand secure cookie configuration.
- [ ] I understand session-based authentication.
- [ ] I can implement authorization checks.
- [ ] I understand CORS.
- [ ] I can implement input validation.

### Testing and Deployment
- [ ] I can write Playwright tests.
- [ ] I can test accessibility.
- [ ] I can test critical user journeys.
- [ ] I can deploy Next.js applications.
- [ ] I can configure production environment variables.
- [ ] I can monitor production performance.

---

# SECTION 26 – OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

| Technology | Official Documentation |
|---|---|
| React | [React Documentation](https://react.dev/learn) |
| React Server Components | [React Server Components](https://react.dev/reference/rsc/server-components) |
| React Suspense | [React Suspense](https://react.dev/reference/react/Suspense) |
| React Performance | [React Performance](https://react.dev/reference/react/Profiler) |
| Next.js | [Next.js Documentation](https://nextjs.org/docs) |
| Next.js Learn | [Next.js Learn](https://nextjs.org/learn) |
| Next.js Support | [Next.js Support Policy](https://nextjs.org/support-policy) |
| TypeScript | [TypeScript Documentation](https://www.typescriptlang.org/docs/) |
| TanStack Query | [TanStack Query](https://tanstack.com/query/latest/docs/framework/react/overview) |
| Zustand | [Zustand Documentation](https://zustand.docs.pmnd.rs/) |
| Redux Toolkit | [Redux Toolkit](https://redux-toolkit.js.org/introduction/getting-started) |
| Tailwind CSS | [Tailwind CSS](https://tailwindcss.com/docs) |
| Playwright | [Playwright Documentation](https://playwright.dev/docs/intro) |
| Web Vitals | [Core Web Vitals](https://web.dev/explore/learn-core-web-vitals) |
| MDN Web Security | [Web Security](https://developer.mozilla.org/en-US/docs/Web/Security) |
| OWASP | [OWASP Web Security](https://owasp.org/www-project-web-security-testing-guide/) |

---

# PHASE 75 COMPLETE

You have covered advanced React architecture, Fiber, concurrent rendering, Server Components, Next.js rendering strategies, streaming, caching, state management, performance engineering, Core Web Vitals, frontend security, testing and production deployment.

## Next Phase Preview

### PHASE 76 – Advanced Full-Stack Architecture, Enterprise Application Integration, Frontend-Backend Communication and Production System Engineering

Planned topics:

1. Advanced Full-Stack Architecture.
2. Frontend-Backend Communication Patterns.
3. Backend-for-Frontend (BFF).
4. API Gateway Architecture.
5. Monorepo Architecture.
6. Turborepo and Nx.
7. Shared TypeScript Packages.
8. Enterprise Authentication Integration.
9. OAuth 2.0 and OpenID Connect.
10. SSO Architecture.
11. Multi-Tenant Full-Stack Applications.
12. Advanced API Contract Design.
13. OpenAPI and Swagger.
14. Type-Safe API Integration.
15. tRPC Architecture.
16. Frontend and Backend Error Handling.
17. Distributed Cache Coordination.
18. Real-Time Full-Stack Applications.
19. WebSockets and Server-Sent Events Integration.
20. Message Queues and Event-Driven Full-Stack Systems.
21. File Upload and Media Processing Architecture.
22. Payment Integration Architecture.
23. Enterprise Search Architecture.
24. Full-Stack Security.
25. Full-Stack Observability.
26. Monorepo CI/CD.
27. Production Deployment Architecture.
28. Enterprise SaaS Application.
29. Complete Full-Stack System Design.
30. Production-Ready Enterprise Application Project.

**When you're ready, say: “OK, Continue to Phase 76.”**