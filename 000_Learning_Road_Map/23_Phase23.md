# **Phase 23 – Advanced Performance Optimization and Core Web Vitals**



Advanced Level

Next.js 16.x

App Router + TypeScript



## **1. Learning objectives**

In this phase, you will learn how to measure, diagnose, and improve the performance of a production Next.js application.

By the end, you should understand:

1. Core Web Vitals: LCP, INP, and CLS.
2. How React rendering affects application performance.
3. When and how to use `useMemo`, `useCallback`, and `React.memo`.
4. Code splitting and lazy loading.
5. How to reduce JavaScript bundle size.
6. How to optimize images, fonts, network requests, and APIs.
7. How to analyze performance using Lighthouse and Chrome DevTools.
8. How to monitor real-user performance in production.
9. How to identify and fix performance bottlenecks.

## **2. What is web performance optimization?**

Web performance optimization is the process of making a website:

- Load faster.
- Respond faster to user interactions.
- Render content without unexpected layout shifts.
- Use less JavaScript and fewer network resources.
- Remain responsive on low-powered devices and slower networks.

Consider an e-commerce application.

A user opens a product page. The page takes five seconds to display the main product image, clicking Add to Cart feels slow, and the layout shifts when the image finally loads.

The application may work correctly, but its user experience is poor.

Performance optimization addresses these problems systematically.

**Important principle:** Do not optimize based on assumptions. Measure first, identify the bottleneck, make a targeted change, and measure again.

## **3. Understanding Core Web Vitals**

Core Web Vitals are Google's user-experience metrics used to evaluate important aspects of a webpage's performance.

There are three primary metrics:

1. LCP – Largest Contentful Paint.
2. INP – Interaction to Next Paint.
3. CLS – Cumulative Layout Shift.

[The complete technical SEO audit checklist for higher rankings in 2026 | eesel AI](https://images.openai.com/static-rsc-4/xwoK9YvordXU9RGTAk62lFrzJC565LJsqKTFzFMe2xJqGolwh70K_n0NR_k8cuKfUjcEHg8pKEXQTSf3r8nkqqnwAVPzqx-jm8YleSK4pqNYBOJLA7EldJNFeOTV3sVzOjQSZVYjXmqpQKei3sIOqL14A6ZsmjlzsGwdgp7ZFQc?purpose=inline)

[eesel.ai](https://www.eesel.ai/blog/technical-seo-audit-checklist?utm_source=chatgpt.com)

### **3.1 LCP – Largest Contentful Paint**

LCP measures how long it takes for the largest visible content element in the viewport to render.

Common LCP elements include:

- Large product images.
- Hero images.
- Main headings.
- Large text blocks.
- Video poster images.

Example:

A user opens your homepage.

| Time     | Event                    |
| -------- | ------------------------ |
| 0 ms     | User opens the website   |
| 300 ms   | Header appears           |
| 700 ms   | Navigation appears       |
| 1,500 ms | Main heading appears     |
| 3,800 ms | Large hero image appears |

If the hero image is the largest content element, the LCP may be approximately 3.8 seconds.

**LCP thresholds**

| LCP                               | Rating            |
| --------------------------------- | ----------------- |
| 2.5 seconds or less               | Good              |
| More than 2.5 and up to 4 seconds | Needs improvement |
| More than 4 seconds               | Poor              |

#### **How to improve LCP**

1. Optimize the main image.
2. Avoid unnecessary client-side rendering for important initial content.
3. Reduce server response time.
4. Use appropriate image dimensions and formats.
5. Preload genuinely critical resources.
6. Reduce render-blocking CSS and JavaScript.
7. Use a CDN where appropriate.

Example using Next.js Image:

```
import Image from "next/image";

export default function Hero() {
  return (
    <section>
      <Image
        src="/hero.webp"
        alt="Featured products"
        width={1600}
        height={700}
        sizes="100vw"
        preload
      />
    </section>
  );
}
```

In Next.js 16, `preload` is the current Image component prop for preloading a critical image. Use it selectively for the actual LCP image, not every image on the page.

### **3.2 INP – Interaction to Next Paint**

INP measures how responsive a page is to user interactions.

It considers interactions such as:

- Clicking a button.
- Selecting a dropdown.
- Typing into an input.
- Opening a menu.
- Adding an item to a cart.

Example:

A user clicks the Add to Cart button.

The browser takes 500 ms before it displays any visual response.

That interaction feels slow.

**INP thresholds**

| INP                            | Rating            |
| ------------------------------ | ----------------- |
| 200 ms or less                 | Good              |
| More than 200 and up to 500 ms | Needs improvement |
| More than 500 ms               | Poor              |

#### **How to improve INP**

- Reduce expensive JavaScript execution.
- Avoid unnecessarily large React components.
- Break up long-running JavaScript tasks.
- Avoid expensive calculations during rendering.
- Keep event handlers lightweight.
- Use transitions for non-urgent React updates where appropriate.
- Load non-essential JavaScript only when required.

Example of a potentially expensive interaction:

```
"use client";

import { useState } from "react";

export default function SearchPage() {
  const [search, setSearch] = useState("");

  function handleSearch(value: string) {
    setSearch(value);

    // Avoid performing expensive synchronous work here.
    // For example, processing hundreds of thousands of records.
  }

  return (
    <input
      value={search}
      onChange={(event) => handleSearch(event.target.value)}
      placeholder="Search products..."
    />
  );
}
```

For large datasets, consider server-side search, debouncing, pagination, or Web Workers depending on the workload.

### **3.3 CLS – Cumulative Layout Shift**

CLS measures unexpected movement of visible page elements.

Example:

A user starts reading a product page. Suddenly, an image loads above the product description, pushing the description down.

This is a layout shift.

[Lighthouse score – use it to improve your app performance](https://images.openai.com/static-rsc-4/MnA0Wsv9VmvXvs-yUxFmDvsG9wt1oINtOezRxJpTfyKKn9houNigZsE5TRIIqm4063Dg0Wnq4rgT4A34RCLKj1jqC7hoJjyozzX4GBf-0mp8uwSYpk-3VQtpntueDDXN7yMLBVbjHTzmCED8oWyJ2Eqg4qgKqtpEMEeXy9oNa2U?purpose=inline)

[tsh.io](https://tsh.io/blog/how-to-keep-your-lighthouse-score-high-in-next-js-applications-a-checklist?utm_source=chatgpt.com)

**CLS thresholds**

| CLS                          | Rating            |
| ---------------------------- | ----------------- |
| 0.1 or less                  | Good              |
| More than 0.1 and up to 0.25 | Needs improvement |
| More than 0.25               | Poor              |

#### **Common causes of CLS**

1. Images without reserved dimensions.
2. Advertisements inserted dynamically.
3. Web fonts causing text reflow.
4. Content inserted above existing content.
5. Dynamically loaded components without reserved space.

#### **How to improve CLS**

Reserve space for images:

```
import Image from "next/image";

export default function ProductImage() {
  return (
    <Image
      src="/product.webp"
      alt="Product"
      width={600}
      height={600}
    />
  );
}
```

For a responsive container:

```
.product-image-container {
  aspect-ratio: 1 / 1;
  position: relative;
}
```

Use `fill` with a reserved container when needed:

```
<div className="product-image-container">
  <Image
    src="/product.webp"
    alt="Product"
    fill
    sizes="(max-width: 768px) 100vw, 50vw"
    style={{ objectFit: "contain" }}
  />
</div>
```

**Remember:** LCP measures loading, INP measures responsiveness, and CLS measures visual stability.

## **4. Understanding React rendering performance**

Before optimizing React applications, you need to understand how rendering works.

### **4.1 What causes a React component to re-render?**

A component can re-render when:

1. Its state changes.
2. Its parent component re-renders.
3. Its context value changes.
4. An external store or subscription it uses triggers an update.

Example:

```
"use client";

import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  console.log("Counter rendered");

  return (
    <div>
      <h2>{count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

Whenever `count` changes, React renders the component again.

This is normal behavior. A re-render is not automatically a performance problem.

### **4.2 Parent and child re-rendering**

Consider:

```
"use client";

import { useState } from "react";

function Child() {
  console.log("Child rendered");

  return <h2>Hello from Child</h2>;
}

export default function Parent() {
  const [count, setCount] = useState(0);

  console.log("Parent rendered");

  return (
    <div>
      <h1>{count}</h1>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <Child />
    </div>
  );
}
```

When the parent re-renders, React normally calls the `Child` component again too.

Even though `Child` does not use the parent's state, it can still render because its parent rendered.

For expensive child components, you may want to avoid unnecessary rendering.

This is where memoization becomes useful.

## **5. React memoization**

Memoization means storing the result of a computation or reusing a previously created value when its inputs have not changed.

React provides three commonly discussed APIs:

| API           | Purpose                                                        |
| ------------- | -------------------------------------------------------------- |
| `React.memo`  | Can skip re-rendering a component when its props are unchanged |
| `useMemo`     | Can reuse the result of a calculation                          |
| `useCallback` | Can reuse a function reference                                 |

These are performance optimizations, not requirements for every component.

### **5.1 React.memo**

`React.memo` allows React to skip rendering a component when its props are unchanged, subject to React's normal rendering behavior.

Without memo:

```
"use client";

import { useState } from "react";

function ProductList({ products }: { products: string[] }) {
  console.log("ProductList rendered");

  return (
    <ul>
      {products.map((product) => (
        <li key={product}>{product}</li>
      ))}
    </ul>
  );
}

export default function Page() {
  const [count, setCount] = useState(0);

  const products = ["Laptop", "Mouse", "Keyboard"];

  return (
    <div>
      <h1>{count}</h1>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <ProductList products={products} />
    </div>
  );
}
```

There is an important detail here: `products` is a new array on every parent render. Even if you wrap `ProductList` with `memo`, its props are not shallowly equal because the array reference changes.

A better implementation:

```
"use client";

import { memo, useState } from "react";

const ProductList = memo(function ProductList({
  products,
}: {
  products: string[];
}) {
  console.log("ProductList rendered");

  return (
    <ul>
      {products.map((product) => (
        <li key={product}>{product}</li>
      ))}
    </ul>
  );
});

const products = ["Laptop", "Mouse", "Keyboard"];

export default function Page() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>{count}</h1>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <ProductList products={products} />
    </div>
  );
}
```

Now the `products` reference remains stable, so `ProductList` can skip rendering when the parent updates `count`.

**Important:** `memo` compares props using shallow equality by default. It does not prevent a component from rendering when its own state or consumed context changes.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://react.dev\&sz=32)

React



### **5.2 useMemo**

`useMemo` caches the result of a calculation between renders when its dependencies have not changed.

Syntax:

```
const cachedValue = useMemo(() => {
  return calculation();
}, [dependencies]);
```

Example without `useMemo`:

```
"use client";

import { useState } from "react";

export default function ProductPage() {
  const [count, setCount] = useState(0);
  const [search, setSearch] = useState("");

  const products = [
    "Laptop",
    "Mouse",
    "Keyboard",
    "Monitor",
    "Headphones",
  ];

  const filteredProducts = products.filter((product) =>
    product.toLowerCase().includes(search.toLowerCase())
  );

  return (
    <div>
      <h2>Count: {count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
        placeholder="Search products"
      />

      {filteredProducts.map((product) => (
        <p key={product}>{product}</p>
      ))}
    </div>
  );
}
```

The filtering calculation runs on every render, including when only `count` changes.

With `useMemo`:

```
"use client";

import { useMemo, useState } from "react";

const products = [
  "Laptop",
  "Mouse",
  "Keyboard",
  "Monitor",
  "Headphones",
];

export default function ProductPage() {
  const [count, setCount] = useState(0);
  const [search, setSearch] = useState("");

  const filteredProducts = useMemo(() => {
    console.log("Filtering products");

    return products.filter((product) =>
      product.toLowerCase().includes(search.toLowerCase())
    );
  }, [search]);

  return (
    <div>
      <h2>Count: {count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
        placeholder="Search products"
      />

      {filteredProducts.map((product) => (
        <p key={product}>{product}</p>
      ))}
    </div>
  );
}
```

Now the filtering calculation is reused when `count` changes. It runs again when `search` changes.

**When should you use&#x20;**`useMemo`**?**

- Expensive filtering or sorting.
- Large data transformations.
- Expensive calculations.
- Creating stable object or array references passed to memoized children.

Do not use it for every variable. For small calculations, memoization may add complexity without meaningful performance benefits.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://react.dev\&sz=32)

React



### **5.3 useCallback**

`useCallback` caches a function definition between renders.

Syntax:

```
const cachedFunction = useCallback(() => {
  // Function logic
}, [dependencies]);
```

Consider this example:

```
"use client";

import { memo, useState } from "react";

const AddButton = memo(function AddButton({
  onAdd,
}: {
  onAdd: () => void;
}) {
  console.log("AddButton rendered");

  return <button onClick={onAdd}>Add Product</button>;
});

export default function ProductPage() {
  const [count, setCount] = useState(0);

  function handleAdd() {
    console.log("Product added");
  }

  return (
    <div>
      <h2>{count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <AddButton onAdd={handleAdd} />
    </div>
  );
}
```

The parent creates a new `handleAdd` function on every render. Therefore, `AddButton` receives a different function reference and its memoization is ineffective.

Use `useCallback`:

```
"use client";

import { memo, useCallback, useState } from "react";

const AddButton = memo(function AddButton({
  onAdd,
}: {
  onAdd: () => void;
}) {
  console.log("AddButton rendered");

  return <button onClick={onAdd}>Add Product</button>;
});

export default function ProductPage() {
  const [count, setCount] = useState(0);

  const handleAdd = useCallback(() => {
    console.log("Product added");
  }, []);

  return (
    <div>
      <h2>{count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <AddButton onAdd={handleAdd} />
    </div>
  );
}
```

Now `handleAdd` retains its reference between renders, so `AddButton` can skip unnecessary renders.

#### **useMemo vs useCallback vs memo**

| Feature       | What it caches                                          | Typical use                              |
| ------------- | ------------------------------------------------------- | ---------------------------------------- |
| `useMemo`     | A calculated value                                      | Filtered products                        |
| `useCallback` | A function reference                                    | Event handler passed to a memoized child |
| `memo`        | A component's rendered result under eligible conditions | Expensive child component                |

### **5.4 Important: React Compiler**

Modern React also supports React Compiler, which can automatically memoize components, values, and functions when the compiler is configured and enabled.

This reduces the need for manually adding `memo`, `useMemo`, and `useCallback` everywhere.

However:

- Do not assume React Compiler is enabled in every Next.js project.
- Do not add compiler directives without understanding the project's configuration.
- Continue to understand manual memoization because existing projects may not use the compiler.
- Always profile the application to confirm that an optimization is useful.

React's current documentation explicitly notes that the compiler can automatically handle many memoization cases.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://react.dev\&sz=32)

React+3



## **6. Code splitting and lazy loading**

### **6.1 What is code splitting?**

Code splitting means dividing JavaScript code into smaller bundles that can be loaded separately.

Imagine your application has:

- Homepage.
- Product listing.
- Product details.
- Admin dashboard.
- Analytics dashboard.
- Rich text editor.

If all the JavaScript is loaded immediately, the initial download can become unnecessarily large.

Code splitting allows the application to load code when it is needed.

[How to Render a Million Rows in React with React-Virtualized for High Performance | by Priyanka Daida | Medium](https://images.openai.com/static-rsc-4/7pEpJutfSEwzXnm_XO22GhkEMd8K9kWNHzJo2CbnThWRN2NPXGvkNEDx5SsoKnxJ_axa2XMU-2aT8R6Fy0v7v1yYrVzenumKk0_SV30nqWYNlBgoIipK2pneYo8h16uyTQFDirm9farEtfaaOmLgTWdiuOzrkiXt1JiU79hZlyk?purpose=inline)

[medium.com](https://medium.com/%40priyankadaida/how-to-render-a-million-rows-in-react-with-react-virtualized-for-high-performance-56733981c3ea?utm_source=chatgpt.com)

Next.js automatically performs route-level code splitting. It also supports lazy loading Client Components and libraries when appropriate.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **6.2 What is lazy loading?**

Lazy loading means delaying the loading of a resource until it is required.

For example, a rich text editor does not need to load when a user opens a page that only displays a list of blog posts.

You can load the editor when the user clicks Edit.

### **6.3 Using next/dynamic**

Next.js provides `next/dynamic` for dynamically importing components.

Example:

```
"use client";

import dynamic from "next/dynamic";
import { useState } from "react";

const AnalyticsChart = dynamic(
  () => import("@/components/AnalyticsChart"),
  {
    loading: () => <p>Loading chart...</p>,
  }
);

export default function Dashboard() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <h1>Dashboard</h1>

      <button onClick={() => setShowChart(true)}>
        View Analytics
      </button>

      {showChart && <AnalyticsChart />}
    </div>
  );
}
```

The chart component is loaded only when the user requests it.

### **6.4 Disable server-side rendering for a Client Component**

Some browser-only libraries depend on `window`, `document`, or other browser APIs.

You can disable server-side rendering for a dynamically imported Client Component:

```
"use client";

import dynamic from "next/dynamic";

const BrowserOnlyChart = dynamic(
  () => import("@/components/BrowserOnlyChart"),
  {
    ssr: false,
    loading: () => <p>Loading chart...</p>,
  }
);

export default function Dashboard() {
  return <BrowserOnlyChart />;
}
```

Important:

- `ssr: false` is supported for Client Components.
- Put this dynamic import inside a Client Component.
- Do not use it as a general performance shortcut. Server rendering is often beneficial.

### **6.5 React.lazy and Suspense**

React also provides `lazy()` and `Suspense`.

```
"use client";

import { lazy, Suspense, useState } from "react";

const Editor = lazy(() => import("@/components/Editor"));

export default function BlogPage() {
  const [showEditor, setShowEditor] = useState(false);

  return (
    <div>
      <button onClick={() => setShowEditor(true)}>
        Open Editor
      </button>

      {showEditor && (
        <Suspense fallback={<p>Loading editor...</p>}>
          <Editor />
        </Suspense>
      )}
    </div>
  );
}
```

### **6.6 next/dynamic vs React.lazy**

| Feature             | `next/dynamic`                              | `React.lazy`                  |
| ------------------- | ------------------------------------------- | ----------------------------- |
| Provided by         | Next.js                                     | React                         |
| Dynamic import      | Yes                                         | Yes                           |
| Loading fallback    | `loading` option or Suspense                | Suspense                      |
| Disable SSR         | Supports `ssr: false` for Client Components | No direct `ssr: false` option |
| Next.js integration | Yes                                         | React API used inside Next.js |

Next.js documents both approaches for lazy loading Client Components and libraries.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **7. Bundle size optimization**

### **7.1 What is a JavaScript bundle?**

A bundle is a generated JavaScript file or collection of files containing your application code and its dependencies.

For example, suppose your application imports:

```
import React from "react";
import ChartLibrary from "some-chart-library";
import DateLibrary from "some-date-library";
import EditorLibrary from "some-editor-library";
```

If large dependencies are included in the initial client bundle unnecessarily, the browser must download, parse, compile, and execute more JavaScript.

This can affect:

- Initial page loading.
- JavaScript execution time.
- INP.
- Memory usage.

### **7.2 How to reduce bundle size**

**1. Avoid unnecessary dependencies**

Before installing a package, check whether you actually need it.

**2. Import only what you use**

Prefer selective imports when the package supports them:

```
import { format } from "date-fns";
```

Instead of importing an entire library namespace unnecessarily:

```
import * as DateLibrary from "date-fns";
```

Actual tree-shaking depends on the package's module format and build setup.

**3. Lazy load large libraries**

```
"use client";

async function exportReport() {
  const ExcelJS = await import("exceljs");

  const workbook = new ExcelJS.Workbook();

  // Generate the workbook here.
}
```

This delays loading ExcelJS until the export function is called.

**4. Keep Server Components as Server Components**

A Server Component does not need its own client-side JavaScript to render.

Avoid placing `"use client"` at the root of your application unnecessarily.

**5. Avoid duplicate dependencies**

Check whether multiple packages introduce overlapping libraries or versions.

**6. Replace unnecessarily large libraries**

For example, if you only need a small date-formatting operation, consider whether a smaller utility or native JavaScript API is sufficient.

Next.js automatically applies optimizations such as code splitting and tree-shaking, but manual bundle analysis helps identify unusually large dependencies.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **7.3 Bundle analysis in Next.js 16**

The current Next.js package-bundling guide documents an experimental Turbopack Bundle Analyzer available in Next.js 16.1 and later. It also documents the Webpack-based `@next/bundle-analyzer` plugin.

The exact analyzer workflow depends on your Next.js version and bundler. Use the matching instructions in the official guide rather than assuming the Webpack plugin applies to a Turbopack build.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



Official guide: [Next.js – Optimizing package bundling](https://nextjs.org/docs/app/guides/package-bundling?utm_source=chatgpt.com).

## **8. Network and API performance optimization**

A website can have efficient React components and still be slow because of network requests or backend operations.

### **8.1 Avoid request waterfalls**

A request waterfall occurs when one request must finish before the next request can begin, even though they are independent.

Example of sequential fetching:

```
export default async function Dashboard() {
  const user = await getUser();

  const orders = await getOrders();

  const products = await getProducts();

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Orders: {orders.length}</p>
      <p>Products: {products.length}</p>
    </div>
  );
}
```

If each request takes 500 ms, the total waiting time could be approximately 1.5 seconds.

If requests are independent, use `Promise.all()`:

```
export default async function Dashboard() {
  const [user, orders, products] = await Promise.all([
    getUser(),
    getOrders(),
    getProducts(),
  ]);

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Orders: {orders.length}</p>
      <p>Products: {products.length}</p>
    </div>
  );
}
```

Now the three operations can run concurrently.

**Important:** Do not parallelize operations when one genuinely depends on the result of another. For example, you may need the user ID before requesting that user's orders.

### **8.2 Avoid unnecessary API calls**

Suppose a dashboard needs:

- Total orders.
- Total revenue.
- Total customers.

A poor implementation might make three separate requests even though the backend can calculate all three metrics in one query or endpoint.

A more efficient endpoint could return:

```
{
  "totalOrders": 120,
  "totalRevenue": 450000,
  "totalCustomers": 85
}
```

This can reduce request overhead, but avoid returning excessive data just to reduce the request count.

### **8.3 Use pagination**

Do not retrieve thousands of database records when the user only needs the first 20.

Example:

```
export async function getProducts(page: number) {
  const limit = 20;
  const skip = (page - 1) * limit;

  return Product.find()
    .skip(skip)
    .limit(limit)
    .lean();
}
```

For very large or frequently changing datasets, cursor-based pagination is often preferable to large offset values.

### **8.4 Use caching appropriately**

Next.js supports different caching and revalidation strategies.

For example, when using the Cache Components model:

```
import { cacheLife, cacheTag } from "next/cache";

export async function getPopularProducts() {
  "use cache";

  cacheLife("hours");
  cacheTag("popular-products");

  return Product.find({ isPopular: true }).lean();
}
```

Caching can reduce repeated database and API work.

However, do not cache user-specific information under a shared cache key. Cache keys and authorization boundaries must be designed carefully.

### **8.5 Avoid calling your own Route Handler from a Server Component**

An inefficient pattern:

```
export default async function Page() {
  const response = await fetch(
    "https://example.com/api/products"
  );

  const products = await response.json();

  return <ProductList products={products} />;
}
```

If this is your own Route Handler and the page is a Server Component, you may be adding an unnecessary HTTP request.

Prefer calling the shared server-side data-access function directly:

```
import { getProducts } from "@/lib/products";
import ProductList from "@/components/ProductList";

export default async function Page() {
  const products = await getProducts();

  return <ProductList products={products} />;
}
```

This avoids an extra HTTP round trip. The Next.js production guide recommends this approach.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **9. React and Next.js rendering optimization**

### **9.1 Keep Client Components small**

Consider this structure:

```
app/
├── dashboard/
│   └── page.tsx
components/
│   ├── Header.tsx
│   ├── Sidebar.tsx
│   ├── ProductList.tsx
│   └── AddToCartButton.tsx
```

Suppose only `AddToCartButton` requires browser interactivity.

Instead of making the entire page a Client Component, keep the page and product list on the server and make only the button a Client Component.

`components/AddToCartButton.tsx`:

```
"use client";

import { useState } from "react";

export default function AddToCartButton() {
  const [added, setAdded] = useState(false);

  return (
    <button onClick={() => setAdded(true)}>
      {added ? "Added" : "Add to Cart"}
    </button>
  );
}
```

`app/dashboard/page.tsx`:

```
import AddToCartButton from "@/components/AddToCartButton";

export default async function DashboardPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product) => (
        <article key={product.id}>
          <h2>{product.name}</h2>
          <AddToCartButton />
        </article>
      ))}
    </main>
  );
}
```

The interactive button needs client-side JavaScript. The page and its server-rendered product data do not need to become Client Components just because one child is interactive.

This helps keep the client JavaScript bundle smaller.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **9.2 Use Suspense for independent sections**

Suppose your dashboard has:

- User profile.
- Recent orders.
- Sales chart.

These sections may load independently.

You can use Suspense to show available content without waiting for every section:

```
import { Suspense } from "react";

async function RecentOrders() {
  const orders = await getRecentOrders();

  return <OrdersList orders={orders} />;
}

async function SalesChart() {
  const sales = await getSalesData();

  return <Chart data={sales} />;
}

export default function Dashboard() {
  return (
    <main>
      <h1>Dashboard</h1>

      <Suspense fallback={<p>Loading orders...</p>}>
        <RecentOrders />
      </Suspense>

      <Suspense fallback={<p>Loading chart...</p>}>
        <SalesChart />
      </Suspense>
    </main>
  );
}
```

Suspense boundaries allow the UI to stream progressively. They improve perceived loading experience when used appropriately; they do not automatically make the underlying database queries faster.

### **9.3 Avoid unnecessary state**

Not every value needs to be stored in React state.

Poor example:

```
const [firstName, setFirstName] = useState("Riyaz");
const [lastName, setLastName] = useState("Khan");
const [fullName, setFullName] = useState("Riyaz Khan");
```

`fullName` is derived from two other values.

Better:

```
const [firstName, setFirstName] = useState("Riyaz");
const [lastName, setLastName] = useState("Khan");

const fullName = `${firstName} ${lastName}`;
```

Derived values should generally be calculated during rendering rather than synchronized through additional state and effects.

## **10. Measuring and monitoring performance**

Optimization is incomplete if you cannot measure the result.

There are two important measurement approaches.

| Measurement | Meaning                        | Example                   |
| ----------- | ------------------------------ | ------------------------- |
| Lab data    | Controlled performance testing | Lighthouse                |
| Field data  | Actual user experience         | Real-user Core Web Vitals |

### **10.1 Lighthouse**

[16 outils d'analyse SEO et leur utilisation](https://images.openai.com/static-rsc-4/Gr0LcubkaEJQy153mY02zyIUN0m-H61ygx7gQIw9X8rOkWlb3a4_vnbn9G23X0BhZe1fsDiBmZEsC5pD3WzlQEOQH0D7maJyHgRCywwSm6KqK7eFtHK1bgUpFg_uhRimccVXsd7qbqTsnuFeTjl2dyUMx4ADZOkAoG8h5phy0vU?purpose=inline)

[blog.hubspot.fr](https://blog.hubspot.fr/marketing/outils-seo-analyser-site?utm_source=chatgpt.com)

Lighthouse audits a webpage and provides scores and recommendations for:

- Performance.
- Accessibility.
- Best practices.
- SEO.

**How to run Lighthouse:**

1. Open your website in Google Chrome.
2. Open Developer Tools using `F12`.
3. Open the Lighthouse panel.
4. Select the categories you want to audit.
5. Select Mobile or Desktop.
6. Run the audit.
7. Review the performance opportunities and diagnostics.

You can also use [PageSpeed Insights](https://pagespeed.web.dev/?utm_source=chatgpt.com) to test a public website.

Remember: Lighthouse is a lab test. Its score can vary depending on the test environment, and it does not replace real-user field data.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **10.2 Chrome DevTools Performance panel**

The Performance panel helps you investigate what is happening while the page loads or while a user interacts with it.

You can identify:

- Long-running JavaScript tasks.
- Expensive rendering.
- Layout and paint activity.
- Main-thread blocking.
- Network activity.
- Interaction delays.

**How to use it:**

1. Open Chrome DevTools.
2. Select the Performance panel.
3. Start recording.
4. Reload the page or perform the interaction you want to investigate.
5. Stop recording.
6. Inspect the Main thread and the recorded events.
7. Find long tasks or expensive functions.
8. Optimize the responsible code and record again.

You can also enable CPU and network throttling to approximate slower devices and connections.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://developer.chrome.com\&sz=32)

Chrome for Developers+1



Official guide: [Chrome DevTools – Performance panel](https://developer.chrome.com/docs/devtools/performance/overview?utm_source=chatgpt.com).

### **10.3 React Developer Tools Profiler**

The React Profiler helps identify which React components rendered and how much time they took during a recorded interaction.

It is useful for diagnosing:

- Unnecessary component renders.
- Expensive component trees.
- Components that update too frequently.
- Components that benefit from memoization.

You can also use React's `<Profiler>` programmatically:

```
"use client";

import { Profiler } from "react";

function onRender(
  id: string,
  phase: string,
  actualDuration: number
) {
  console.log({
    id,
    phase,
    actualDuration,
  });
}

export default function App() {
  return (
    <Profiler id="ProductList" onRender={onRender}>
      <ProductList />
    </Profiler>
  );
}
```

`actualDuration` measures the time spent rendering the profiled tree for that commit.

Note that profiling adds overhead, and ordinary production builds do not include profiling instrumentation by default.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://react.dev\&sz=32)

React



## **11. Monitoring Core Web Vitals in Next.js**

Next.js provides the `useReportWebVitals` hook.

It allows you to collect performance metrics and send them to an analytics endpoint.

### **Step 1: Create a Web Vitals component**

`app/_components/web-vitals.tsx`:

```
"use client";

import { useReportWebVitals } from "next/web-vitals";

export function WebVitals() {
  useReportWebVitals((metric) => {
    console.log({
      name: metric.name,
      value: metric.value,
      rating: metric.rating,
      id: metric.id,
    });
  });

  return null;
}
```

### **Step 2: Add it to the root layout**

`app/layout.tsx`:

```
import { WebVitals } from "./_components/web-vitals";

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>
        <WebVitals />
        {children}
      </body>
    </html>
  );
}
```

### **Step 3: Send the metrics to an analytics endpoint**

For example:

```
"use client";

import { useReportWebVitals } from "next/web-vitals";

export function WebVitals() {
  useReportWebVitals((metric) => {
    const body = JSON.stringify(metric);

    if (navigator.sendBeacon) {
      navigator.sendBeacon("/api/analytics", body);
    } else {
      fetch("/api/analytics", {
        method: "POST",
        body,
        headers: {
          "Content-Type": "application/json",
        },
        keepalive: true,
      });
    }
  });

  return null;
}
```

The endpoint must be implemented to receive and store these metrics. Do not assume that adding this component automatically creates an analytics dashboard.

The callback should have a stable reference to avoid duplicate reporting. For production, consider defining it outside the component, as in the official example.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



Official documentation: [Next.js – useReportWebVitals](https://nextjs.org/docs/app/api-reference/functions/use-report-web-vitals?utm_source=chatgpt.com).

### **11.1 Vercel Speed Insights**

If your application is deployed on Vercel, you can also consider Vercel Speed Insights.

It provides real-user performance measurements, which complement lab testing with Lighthouse.

Official resource: [Vercel Speed Insights documentation](https://vercel.com/docs/speed-insights?utm_source=chatgpt.com).

## **12. Real-world performance optimization example**

Let's consider an e-commerce application built with Next.js, MongoDB, and React.

Suppose your product listing page has these problems:

| Problem                         | Possible cause                           | Recommended solution                      |
| ------------------------------- | ---------------------------------------- | ----------------------------------------- |
| Product images load slowly      | Large image files                        | `next/image`, responsive sizes            |
| Search feels slow               | Expensive filtering                      | Debouncing, server-side search, profiling |
| Clicking Add to Cart feels slow | Heavy synchronous JavaScript             | Simplify event handlers                   |
| Product list renders repeatedly | Unnecessary component updates            | Profile first; consider memoization       |
| Initial page is slow            | Large client bundle                      | Server Components, code splitting         |
| Dashboard takes too long        | Sequential requests                      | `Promise.all()` where independent         |
| Page content jumps              | Images or banners without reserved space | Dimensions and aspect ratios              |
| Analytics page loads slowly     | Large chart library                      | Dynamic import                            |
| Database queries repeat         | Missing or unsuitable caching            | Appropriate caching and indexing          |

### **Suggested optimized architecture**

```
app/
├── products/
│   ├── page.tsx
│   └── loading.tsx
│
├── dashboard/
│   └── page.tsx
│
components/
├── products/
│   ├── ProductList.tsx
│   ├── ProductCard.tsx
│   └── AddToCartButton.tsx
│
├── analytics/
│   └── AnalyticsChart.tsx
│
└── _components/
    └── web-vitals.tsx

lib/
├── products.ts
├── analytics.ts
└── database.ts
```

Recommended design:

1. Fetch product data in Server Components.
2. Keep product cards as Server Components if they do not need browser interaction.
3. Make only interactive controls Client Components.
4. Load analytics charts dynamically.
5. Optimize product images using `next/image`.
6. Use database indexes for frequently queried fields.
7. Use appropriate caching for public product data.
8. Measure LCP, INP, and CLS.
9. Profile expensive React rendering.
10. Monitor real-user performance after deployment.

## **13. Common performance optimization mistakes**

| Mistake                                          | Why it is a problem                                              |
| ------------------------------------------------ | ---------------------------------------------------------------- |
| Adding `useMemo` everywhere                      | Unnecessary complexity and maintenance                           |
| Adding `useCallback` to every function           | Often provides no meaningful benefit                             |
| Using `memo` without checking prop stability     | New object/function references can defeat memoization            |
| Making the entire application a Client Component | Increases client JavaScript unnecessarily                        |
| Loading every library immediately                | Increases initial bundle size                                    |
| Using `ssr: false` everywhere                    | Removes useful server rendering                                  |
| Loading all database records                     | Increases database, network, and rendering work                  |
| Making sequential independent API calls          | Creates request waterfalls                                       |
| Ignoring image dimensions                        | Can cause layout shifts                                          |
| Optimizing only for desktop                      | Mobile devices may perform much worse                            |
| Testing only in development mode                 | Development performance does not accurately represent production |
| Trusting Lighthouse alone                        | Lab scores do not represent every real user                      |

**Important:** A website with fewer React renders is not necessarily faster. You must measure the actual user experience.

## **14. Interview questions and answers**

### **Basic-level questions**

**Q1. What is web performance optimization?**

It is the process of improving a website's loading speed, responsiveness, rendering efficiency, and resource usage.

**Q2. What are Core Web Vitals?**

They are Google's user-experience metrics:

- LCP – Loading performance.
- INP – Interaction responsiveness.
- CLS – Visual stability.

**Q3. What is LCP?**

Largest Contentful Paint measures how quickly the largest visible content element renders.

**Q4. What is INP?**

Interaction to Next Paint measures how quickly a page responds visually to user interactions.

**Q5. What is CLS?**

Cumulative Layout Shift measures unexpected movement of visible page elements.

**Q6. What is memoization?**

Memoization is an optimization technique that reuses previously calculated results or references when their inputs have not changed.

**Q7. What is code splitting?**

Code splitting divides JavaScript into smaller bundles that can be loaded separately.

**Q8. What is lazy loading?**

Lazy loading delays loading a resource until it is needed.

### **Intermediate-level questions**

**Q9. What is the difference between&#x20;**`useMemo`**&#x20;and&#x20;**`useCallback`**?**

`useMemo` caches a calculated value, whereas `useCallback` caches a function reference.

**Q10. What is the difference between&#x20;**`React.memo`**&#x20;and&#x20;**`useMemo`**?**

`React.memo` memoizes a component's rendering based on its props. `useMemo` memoizes a calculated value inside a component.

**Q11. Why does a child component re-render when its parent re-renders?**

By default, React normally renders the child again when its parent renders. `memo` may allow React to skip that render if the child's props have not changed.

**Q12. What is a request waterfall?**

It occurs when requests are executed sequentially even though they could have been executed concurrently.

**Q13. How does&#x20;**`next/dynamic`**&#x20;improve performance?**

It allows Client Components to be dynamically imported, potentially reducing the JavaScript required for the initial page load.

**Q14. Why should we minimize Client Components in Next.js?**

Client Components require client-side JavaScript for interactivity. Keeping the client boundary small can reduce the amount of JavaScript shipped to the browser.

**Q15. What is bundle size?**

It refers to the size of generated JavaScript bundles that the browser needs to download and process.

### **Advanced-level questions**

**Q16. How would you optimize a slow Next.js dashboard?**

I would:

1. Measure performance with Lighthouse and Chrome DevTools.
2. Identify whether the bottleneck is server response, network, JavaScript, or rendering.
3. Parallelize independent data requests.
4. Optimize database queries and indexes.
5. Apply appropriate caching.
6. Use Server Components wherever possible.
7. Lazy load large charts and editors.
8. Profile React components.
9. Monitor real-user metrics after deployment.

**Q17. Does&#x20;**`useMemo`**&#x20;prevent component re-rendering?**

No. It only caches the result of a calculation. It does not independently prevent the component from rendering again.

**Q18. Does&#x20;**`React.memo`**&#x20;guarantee that a component never re-renders?**

No. It can skip rendering when props are unchanged, but the component can still render because of its own state, context, or other React updates.

**Q19. What is the difference between lab data and field data?**

Lab data is collected under controlled testing conditions. Field data represents actual user experiences on real devices and networks.

**Q20. How can you monitor Core Web Vitals in Next.js?**

Use `useReportWebVitals` to collect metrics and send them to an analytics endpoint or monitoring service.

**Q21. What is React Compiler?**

React Compiler is a build-time optimization tool that can automatically memoize components, values, and functions, reducing the need for manual memoization.

## **15. Practical assignment**

Hands-on project

Build a **Performance-Optimized Product Listing Application** using Next.js 16, TypeScript, and MongoDB.

### **Requirements**

**Part A – Rendering**

- Create a product listing page.
- Fetch products through a Server Component.
- Keep interactive components separate.
- Implement product search.
- Implement category filtering.

**Part B – React optimization**

- Identify an expensive calculation.
- Optimize it using `useMemo` if profiling justifies it.
- Use `memo` for an expensive child component if useful.
- Use `useCallback` when stable function references are beneficial.
- Compare performance before and after.

**Part C – Lazy loading**

- Create an analytics chart component.
- Load it using `next/dynamic`.
- Display a loading fallback.
- Test that the chart is not loaded until needed.

**Part D – Core Web Vitals**

- Optimize the main product image.
- Reserve image dimensions to avoid layout shifts.
- Check the LCP element.
- Test interaction responsiveness.
- Check CLS during page loading.

**Part E – Monitoring**

- Run Lighthouse.
- Use Chrome DevTools Performance panel.
- Add `useReportWebVitals`.
- Record your performance measurements.

### **Performance measurement sheet**

Record your results before and after optimization.

| Metric                 | Before      | After       |
| ---------------------- | ----------- | ----------- |
| LCP                    | Your result | Your result |
| INP                    | Your result | Your result |
| CLS                    | Your result | Your result |
| Lighthouse Performance | Your result | Your result |
| Initial JavaScript     | Your result | Your result |

Your goal is to improve actual performance, not simply to increase a Lighthouse score.

## **16. Quick revision notes**

| Topic                | Remember                                              |
| -------------------- | ----------------------------------------------------- |
| LCP                  | Largest visible content loading time                  |
| INP                  | Interaction responsiveness                            |
| CLS                  | Unexpected layout movement                            |
| `React.memo`         | Can skip component rendering when props are unchanged |
| `useMemo`            | Caches calculation results                            |
| `useCallback`        | Caches function references                            |
| React Compiler       | Can automate memoization                              |
| Code splitting       | Divides JavaScript into smaller bundles               |
| Lazy loading         | Loads resources when required                         |
| `next/dynamic`       | Dynamically imports components                        |
| `React.lazy`         | Dynamically imports React components                  |
| `Suspense`           | Displays fallback UI while content loads              |
| Bundle analysis      | Identifies large dependencies and modules             |
| Lighthouse           | Lab-based performance auditing                        |
| Chrome Performance   | Runtime and loading performance profiling             |
| React Profiler       | Measures React rendering                              |
| `useReportWebVitals` | Collects Web Vitals metrics                           |
| `Promise.all()`      | Runs independent asynchronous operations concurrently |

### **Important Core Web Vitals thresholds**

**LCP – Largest Contentful Paint**

# **≤ 2.5 s**

Good loading performance

**INP – Interaction to Next Paint**

# **≤ 200 ms**

Good interaction responsiveness

**CLS – Cumulative Layout Shift**

# **≤ 0.1**

Good visual stability

These targets are evaluated at the 75th percentile of page loads, separately for mobile and desktop.



[image](https://www.google.com/s2/favicons?domain=https://web.dev\&sz=32)

web.dev



## **17. Official documentation and learning resources**

These are useful references for revising this phase:

1. [Google – Web Vitals](https://web.dev/articles/vitals?authuser=115\&utm_source=chatgpt.com) – Core Web Vitals and measurement thresholds.
2. [Next.js – Production Checklist](https://nextjs.org/docs/app/guides/production-checklist?utm_source=chatgpt.com) – Production optimization practices.
3. [Next.js – Lazy Loading](https://nextjs.org/docs/app/guides/lazy-loading?utm_source=chatgpt.com) – Dynamic imports and lazy loading.
4. [Next.js – Package Bundling](https://nextjs.org/docs/app/guides/package-bundling?utm_source=chatgpt.com) – Bundle analysis and dependency optimization.
5. [Next.js – useReportWebVitals](https://nextjs.org/docs/app/api-reference/functions/use-report-web-vitals?utm_source=chatgpt.com) – Web Vitals monitoring.
6. [React – memo](https://react.dev/reference/react/memo?utm_source=chatgpt.com) – Component memoization.
7. [React – useMemo](https://react.dev/reference/react/useMemo?utm_source=chatgpt.com) – Value memoization.
8. [React – useCallback](https://react.dev/reference/react/useCallback?utm_source=chatgpt.com) – Function memoization.
9. [React – Profiler](https://react.dev/reference/react/Profiler?utm_source=chatgpt.com) – React rendering performance.
10. [Chrome DevTools – Performance](https://developer.chrome.com/docs/devtools/performance/overview?utm_source=chatgpt.com) – Browser performance analysis.

## **18. Phase 23 completion checklist**

**Your learning progress**

# **0%**

0 of 16 topics completed

Understand LCP, INP and CLS

Understand React rendering and re-rendering

Understand React.memo

Understand useMemo

Understand useCallback

Understand React Compiler

Understand code splitting

Understand lazy loading

Use next/dynamic and React.lazy

Understand JavaScript bundle optimization

Optimize API and database requests

Understand Suspense and rendering optimization

Use Lighthouse and Chrome DevTools

Understand React Profiler

Implement useReportWebVitals

Complete the product listing assignment

Reset checklist

## **19. Next phase preview**

[Akshay Bangar | Software Engineer Portfolio](https://images.openai.com/static-rsc-4/VJ0Qh6TyyDffQJxy7ZWk8A21MH3_orukEAqEf7A89dkdhyUYYxhVJH5HLUB_Q8-Md_60ifXHXNq6j7INwgOzdv3dDg-1XuY_2U1UrPoTL3pkTLAc4g9cYSJOO9-JtCafoewKYw26sj4_d5Ln4FKqLZxY-7rCA1x5WZWToQoHMJo?purpose=inline)

**Phase 24 – Advanced Full-Stack Architecture and Scalability**

Next Phase

Advanced Next.js application architecture

Planned topics:

1. Monolithic vs modular architecture.
2. Feature-based architecture.
3. Scalable folder structures.
4. Frontend and backend separation.
5. API architecture and service layers.
6. Database architecture and connection management.
7. Redis and distributed caching.
8. Background jobs and queues.
9. Rate limiting and API protection.
10. Scalability, maintainability, and production architecture.

**When you're ready, say: OK, Phase 24.**