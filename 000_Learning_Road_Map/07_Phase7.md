# **Next.js Complete Learning Roadmap – Phase 7**

## **Loading UI, Suspense and Streaming**

Phase 7

Intermediate

Next.js 16.x

App Router

TypeScript

## **1. Learning objectives**

In this phase, you will learn how Next.js handles loading states while data or components are being rendered.

By the end of this phase, you should understand:

1. What loading UI is and why it matters.
2. How `loading.tsx` works.
3. What React Suspense is.
4. How Next.js streaming works.
5. What loading boundaries are.
6. How nested loading states work.
7. How to create skeleton loaders.
8. How to handle asynchronous Server Components.
9. How to show different loading states for different components.
10. How to build production-style loading experiences.

# **2. What is Loading UI?**

Loading UI is the interface displayed while an application is waiting for something to become available.

For example, imagine opening a product details page.

The page needs to:

1. Receive the request.
2. Fetch product information from the database.
3. Process the data.
4. Render the page.
5. Send the result to the browser.

During this process, the user may have to wait.

Instead of displaying a blank screen, you can display a loading indicator.

Without loading UI

Blank screen

Users don't know whether the page is working.

With loading UI

Loading products...

Users receive immediate visual feedback.

### **Why is loading UI important?**

- Improves user experience.
- Provides feedback during slow operations.
- Prevents users from thinking the application is broken.
- Makes navigation feel more responsive.
- Allows users to see available content while other content is loading.

# **3. Understanding&#x20;**`loading.tsx`

Next.js provides a special file convention called:

```
loading.tsx
```

It allows you to create a loading UI for a route segment.

### **Example folder structure**

```
app/
├── layout.tsx
├── page.tsx
│
└── products/
    ├── loading.tsx
    └── page.tsx
```

The `loading.tsx` file automatically creates a loading boundary for the corresponding route segment.

### **Step 1: Create the loading component**

```
// app/products/loading.tsx

export default function Loading() {
  return (
    <div>
      <h2>Loading products...</h2>
      <p>Please wait while we fetch the products.</p>
    </div>
  );
}
```

### **Step 2: Create an asynchronous page**

```
// app/products/page.tsx

async function getProducts() {
  await new Promise((resolve) => setTimeout(resolve, 3000));

  return [
    { id: 1, name: "Laptop" },
    { id: 2, name: "Mobile" },
    { id: 3, name: "Keyboard" },
  ];
}

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={product.id}>
          {product.name}
        </div>
      ))}
    </div>
  );
}
```

### **What happens?**

When a user navigates to `/products`:

1. Next.js starts rendering the route.
2. The products page waits for its asynchronous operation.
3. The loading UI can be displayed while the route is loading.
4. Once the page is ready, the actual page content replaces the loading UI.

**Loading lifecycle**

1. **User navigates to&#x20;**`/products`

   The route transition begins.
2. **Loading boundary appears**

   The loading component is displayed.
3. **Data fetching completes**

   The asynchronous page finishes rendering.
4. **Actual page appears**

   The loading UI is replaced with the page content.

**Important:** `loading.tsx` is not a function you manually call. Next.js automatically uses it as a loading boundary for its route segment.

# **4. How Does&#x20;**`loading.tsx`**&#x20;Work Internally?**

Conceptually, Next.js creates a React Suspense boundary around the route's page content.

You can think of it as similar to:

```
<Suspense fallback={<Loading />}>
  <ProductsPage />
</Suspense>
```

This is a simplified conceptual representation, not the exact source code Next.js generates.

### **Folder structure**

```
app/
└── products/
    ├── layout.tsx
    ├── loading.tsx
    └── page.tsx
```

Conceptually, the hierarchy is:

```
ProductsLayout
  └── Suspense Boundary
        ├── Fallback: Loading
        └── ProductsPage
```

### **Important distinction**

`loading.tsx` handles the loading state for the route segment. It does not automatically show a separate loading indicator for every individual component inside that page.

For component-level loading, you can use React Suspense directly.

# **5. What is React Suspense?**

Suspense is a React feature that allows you to display fallback UI while a child component is waiting for something that suspends rendering.

It is commonly used with:

- Asynchronous Server Components.
- Lazy-loaded components.
- Streaming interfaces.
- Deferred data-dependent UI.

### **Basic syntax**

```
import { Suspense } from "react";

export default function Page() {
  return (
    <Suspense fallback={<p>Loading...</p>}>
      <SomeComponent />
    </Suspense>
  );
}
```

The `fallback` is displayed while the child is suspended.

### **Understanding the three important parts**

| Part            | Meaning                                   |
| --------------- | ----------------------------------------- |
| `Suspense`      | Defines a loading boundary                |
| `fallback`      | UI displayed while the child is suspended |
| Child component | Component whose rendering may suspend     |

### **Example with an asynchronous Server Component**

```
// app/page.tsx

import { Suspense } from "react";

async function UserProfile() {
  await new Promise((resolve) => setTimeout(resolve, 3000));

  return <h2>User profile loaded!</h2>;
}

export default function HomePage() {
  return (
    <main>
      <h1>My Application</h1>

      <Suspense fallback={<p>Loading profile...</p>}>
        <UserProfile />
      </Suspense>
    </main>
  );
}
```

### **What happens?**

- The main page can render immediately.
- The `UserProfile` component suspends while waiting.
- The fallback displays in its place.
- When the component is ready, React replaces the fallback with the profile.

**Key point:** Suspense allows one part of the UI to wait without necessarily blocking the entire page.

# **6.&#x20;**`loading.tsx`**&#x20;vs React Suspense**

Both provide loading experiences, but they are used at different levels.

| Feature                             | `loading.tsx`                     | React Suspense                    |
| ----------------------------------- | --------------------------------- | --------------------------------- |
| Type                                | Next.js file convention           | React component                   |
| Main purpose                        | Route-segment loading UI          | Component-level loading UI        |
| Automatically integrated by Next.js | Yes                               | No, you add it manually           |
| Fallback UI                         | Exported component                | `fallback` prop                   |
| Scope                               | Route segment                     | Specific component subtree        |
| Nested boundaries                   | Supported through route structure | Supported through nested Suspense |

### **When should you use&#x20;**`loading.tsx`**?**

Use it when an entire route segment needs a loading state.

Examples:

- Product listing page.
- Dashboard route.
- Blog listing page.
- Search results page.

### **When should you use Suspense?**

Use it when only a particular part of the page needs to wait.

Examples:

- User profile widget.
- Product reviews.
- Recommended products.
- Dashboard analytics chart.

# **7. What is Streaming in Next.js?**

Streaming is a rendering technique that allows the server to send the page to the browser in smaller portions as they become ready.

Without streaming, the browser may have to wait for the entire page to finish rendering before receiving its content.

With streaming, Next.js can send the parts that are ready while other parts are still loading.

### **Example: Dashboard page**

Imagine a dashboard containing:

- Header
- User information
- Sales statistics
- Recent orders
- Notifications

Suppose:

| Component        | Rendering time |
| ---------------- | -------------- |
| Header           | 100 ms         |
| User information | 300 ms         |
| Sales statistics | 3 seconds      |
| Recent orders    | 2 seconds      |
| Notifications    | 500 ms         |

Without independent loading boundaries, slow operations can delay the page's useful content.

With streaming, fast components can appear while slower components are still loading.

[21  SaaS Skeleton Screen UI Design Examples](https://images.openai.com/static-rsc-4/UI2hckOFUxk3YiHCEy_R0l1ynx6hIGxCYL1xjPj1txPK5zMl_CxGkQAXpJzeqorlU2IuXx5-6fnWc4wXc0rNoXupLrpbeJpRClZa_AD79fBZEo2Gu3nljJVDKi13WMbc1XBzSYrCaHaWzB_iQKLr-P1z63WwxV4d_N8bdqCD8Pk?purpose=inline)

[saasframe.io](https://www.saasframe.io/patterns/loading-screen?utm_source=chatgpt.com)

### **Visual representation**

**Traditional all-at-once rendering**

Wait for everything

Header — waiting

Sales chart — waiting

Orders — waiting

Page content becomes available after the required rendering work completes.

**Streaming with Suspense**

Available first

Header — loaded

Notifications — loaded

Still loading

Sales chart — loading

Orders — loading

Available content can be displayed before slower sections finish.

### **Why is streaming useful?**

1. Users can see content sooner.
2. Slow components do not have to block unrelated content.
3. You can create independent loading states.
4. It improves perceived performance.
5. It works naturally with React Suspense and Next.js Server Components.

**Important:** Streaming does not necessarily make the database query itself faster. It changes when rendered content becomes available to the browser.

# **8. Streaming with Multiple Suspense Boundaries**

Let's build a practical dashboard.

### **Folder structure**

```
app/
└── dashboard/
    └── page.tsx
```

### **Dashboard page**

```
// app/dashboard/page.tsx

import { Suspense } from "react";

async function SalesChart() {
  await new Promise((resolve) => setTimeout(resolve, 3000));

  return <h2>Sales Chart Loaded</h2>;
}

async function RecentOrders() {
  await new Promise((resolve) => setTimeout(resolve, 5000));

  return <h2>Recent Orders Loaded</h2>;
}

async function Notifications() {
  await new Promise((resolve) => setTimeout(resolve, 1000));

  return <h2>Notifications Loaded</h2>;
}

export default function DashboardPage() {
  return (
    <main>
      <h1>Dashboard</h1>

      <Suspense fallback={<p>Loading notifications...</p>}>
        <Notifications />
      </Suspense>

      <Suspense fallback={<p>Loading sales chart...</p>}>
        <SalesChart />
      </Suspense>

      <Suspense fallback={<p>Loading recent orders...</p>}>
        <RecentOrders />
      </Suspense>
    </main>
  );
}
```

### **What happens?**

Each Suspense boundary handles its own loading state.

| Component     | Expected loading duration | Result         |
| ------------- | ------------------------- | -------------- |
| Notifications | 1 second                  | Appears first  |
| Sales chart   | 3 seconds                 | Appears second |
| Recent orders | 5 seconds                 | Appears last   |

The page can show the available components independently.

This is a useful pattern for dashboards, analytics applications, and complex data-driven interfaces.

# **9. What are Loading Boundaries?**

A loading boundary is the boundary between the fallback UI and the content that is still loading.

For example:

```
<Suspense fallback={<p>Loading...</p>}>
  <UserProfile />
</Suspense>
```

Here:

- The `Suspense` component creates the boundary.
- The fallback is displayed while `UserProfile` suspends.
- The actual component replaces the fallback once it is ready.

### **Nested loading boundaries**

You can also place one Suspense boundary inside another.

```
import { Suspense } from "react";

async function UserDetails() {
  await new Promise((resolve) => setTimeout(resolve, 2000));

  return <p>User details loaded</p>;
}

async function UserActivity() {
  await new Promise((resolve) => setTimeout(resolve, 4000));

  return <p>User activity loaded</p>;
}

async function UserProfile() {
  return (
    <div>
      <h2>User Profile</h2>

      <Suspense fallback={<p>Loading user details...</p>}>
        <UserDetails />
      </Suspense>

      <Suspense fallback={<p>Loading user activity...</p>}>
        <UserActivity />
      </Suspense>
    </div>
  );
}

export default function Page() {
  return (
    <Suspense fallback={<p>Loading profile...</p>}>
      <UserProfile />
    </Suspense>
  );
}
```

In this example, the outer boundary handles suspension of `UserProfile`, while the inner boundaries handle suspension of its individual child components.

# **10. What are Skeleton Loaders?**

A skeleton loader is a placeholder that resembles the structure of the content being loaded.

Instead of showing a generic spinner, you display shapes that represent the future content.

**Spinner**

Generic loading feedback.

**Skeleton**

Represents the shape of future content.

### **Example: Product skeleton**

```
// app/products/loading.tsx

export default function ProductsLoading() {
  return (
    <div className="grid grid-cols-3 gap-6">
      {Array.from({ length: 6 }).map((_, index) => (
        <div
          key={index}
          className="animate-pulse space-y-3"
        >
          <div className="h-48 rounded bg-gray-200" />

          <div className="h-4 w-3/4 rounded bg-gray-200" />

          <div className="h-4 w-1/2 rounded bg-gray-200" />

          <div className="h-9 rounded bg-gray-200" />
        </div>
      ))}
    </div>
  );
}
```

### **Explanation**

- `animate-pulse`: Creates a pulsing animation using Tailwind CSS.
- `h-48`: Sets the height of the placeholder image.
- `w-3/4`: Sets the width of the placeholder text.
- `rounded`: Adds rounded corners.
- `bg-gray-200`: Defines the placeholder background.

### **Why use skeleton loaders?**

They help users understand the structure of the upcoming content and can make an application feel more responsive.

Common use cases:

- Product cards.
- Blog cards.
- Profile pages.
- Dashboard widgets.
- Tables.
- Search results.

# **11. Creating a Reusable Skeleton Component**

Instead of repeating skeleton markup, you can create reusable components.

### **Product skeleton component**

```
// components/ProductSkeleton.tsx

export default function ProductSkeleton() {
  return (
    <div className="animate-pulse space-y-3">
      <div className="h-48 bg-gray-200 rounded" />

      <div className="h-4 bg-gray-200 rounded w-3/4" />

      <div className="h-4 bg-gray-200 rounded w-1/2" />

      <div className="h-9 bg-gray-200 rounded" />
    </div>
  );
}
```

### **Loading page**

```
// app/products/loading.tsx

import ProductSkeleton from "@/components/ProductSkeleton";

export default function ProductsLoading() {
  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
      {Array.from({ length: 6 }).map((_, index) => (
        <ProductSkeleton key={index} />
      ))}
    </div>
  );
}
```

This makes your loading UI reusable and easier to maintain.

# **12. Loading UI with Database Operations**

Let's consider a real-world example where you fetch products from MongoDB.

### **Product fetching function**

```
// lib/products.ts

import { connectDB } from "./db";
import Product from "@/models/Product";

export async function getProducts() {
  await connectDB();

  const products = await Product.find()
    .select("name price image")
    .lean();

  return JSON.parse(JSON.stringify(products));
}
```

### **Products page**

```
// app/products/page.tsx

import { getProducts } from "@/lib/products";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div>
      <h1>Products</h1>

      <div className="grid grid-cols-3 gap-4">
        {products.map((product: any) => (
          <div key={product._id}>
            <h2>{product.name}</h2>
            <p>₹{product.price}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### **Loading UI**

```
// app/products/loading.tsx

export default function Loading() {
  return <p>Loading products from database...</p>;
}
```

### **Execution flow**

```
User visits /products
        |
        v
Next.js starts rendering
        |
        v
Products page fetches data
        |
        v
Database query is pending
        |
        v
Loading UI can be displayed
        |
        v
Database returns products
        |
        v
Products page renders
```

**Important:** In real applications, make sure your database connection is initialized safely and that returned data is serializable when it crosses Server Component and Client Component boundaries.

# **13. Handling Multiple Async Operations with&#x20;**`Promise.all()`

Sometimes a page needs data from multiple independent sources.

For example:

- User details.
- Orders.
- Notifications.

You can fetch them in parallel using `Promise.all()`.

```
async function getDashboardData() {
  const [user, orders, notifications] = await Promise.all([
    getUser(),
    getOrders(),
    getNotifications(),
  ]);

  return {
    user,
    orders,
    notifications,
  };
}
```

### **Sequential vs Parallel fetching**

**Sequential fetching:**

```
const user = await getUser();
const orders = await getOrders();
const notifications = await getNotifications();
```

The next operation starts after the previous one completes.

**Parallel fetching:**

```
const [user, orders, notifications] = await Promise.all([
  getUser(),
  getOrders(),
  getNotifications(),
]);
```

All three operations start without waiting for one another.

| Feature            | Sequential                  | Parallel               |
| ------------------ | --------------------------- | ---------------------- |
| Execution          | One after another           | Concurrent             |
| Total waiting time | Usually higher              | Usually lower          |
| Suitable for       | Dependent operations        | Independent operations |
| Function           | Multiple `await` statements | `Promise.all()`        |

### **Important distinction**

`Promise.all()` and Suspense solve different problems.

- `Promise.all()` coordinates multiple asynchronous operations.
- Suspense controls what UI is displayed while a component suspends.

You can combine them, but they are not interchangeable.

# **14. Parallel Data Fetching with Suspense**

Suppose your dashboard has three independent components.

Instead of waiting for all data before rendering the dashboard, you can place each component inside its own Suspense boundary.

```
// app/dashboard/page.tsx

import { Suspense } from "react";

async function UserDetails() {
  const user = await getUser();

  return <h2>{user.name}</h2>;
}

async function Orders() {
  const orders = await getOrders();

  return (
    <div>
      <h2>Recent Orders</h2>
      {orders.map((order: any) => (
        <p key={order.id}>{order.name}</p>
      ))}
    </div>
  );
}

async function Notifications() {
  const notifications = await getNotifications();

  return (
    <div>
      <h2>Notifications</h2>
      {notifications.map((item: any) => (
        <p key={item.id}>{item.message}</p>
      ))}
    </div>
  );
}

export default function DashboardPage() {
  return (
    <main>
      <h1>Dashboard</h1>

      <Suspense fallback={<p>Loading user...</p>}>
        <UserDetails />
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

Each component can resolve independently, so its own loading UI can be replaced as soon as its content is ready.

# **15.&#x20;**`loading.tsx`**&#x20;and Nested Routes**

Loading files can be created at multiple route levels.

### **Folder structure**

```
app/
├── layout.tsx
│
└── dashboard/
    ├── layout.tsx
    ├── loading.tsx
    ├── page.tsx
    │
    └── users/
        ├── loading.tsx
        └── page.tsx
```

Here:

- `dashboard/loading.tsx` provides loading UI for the dashboard segment.
- `dashboard/users/loading.tsx` provides a more specific loading UI for the users segment.

The nearest applicable loading boundary handles the relevant route content.

### **Dashboard loading**

```
// app/dashboard/loading.tsx

export default function DashboardLoading() {
  return <h2>Loading dashboard...</h2>;
}
```

### **Users loading**

```
// app/dashboard/users/loading.tsx

export default function UsersLoading() {
  return <h2>Loading users...</h2>;
}
```

### **Important concept**

Nested layouts and loading boundaries work together.

A route segment's loading UI is associated with its segment and can be used while that segment's page and nested content are being prepared.

# **16. What is the Difference Between Loading UI and Error UI?**

Loading and error handling are two different states.

| Feature      | Loading UI                | Error UI                                          |
| ------------ | ------------------------- | ------------------------------------------------- |
| Purpose      | Shows waiting state       | Shows failure state                               |
| Next.js file | `loading.tsx`             | `error.tsx`                                       |
| Trigger      | Suspended/loading content | Uncaught rendering error in the relevant boundary |
| Example      | Loading products          | Failed to load products                           |
| User action  | Usually wait              | Often retry                                       |

### **Example error boundary**

```
// app/products/error.tsx

"use client";

export default function ProductsError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>

      <p>We could not load the products.</p>

      <button onClick={() => reset()}>
        Try Again
      </button>
    </div>
  );
}
```

We will study error handling in more detail in a later phase.

For now, remember:

- `loading.tsx` handles loading.
- `error.tsx` handles errors.
- `not-found.tsx` handles not-found UI.

# **17. Common Mistakes**

| Mistake                                                             | Correct approach                                                     |
| ------------------------------------------------------------------- | -------------------------------------------------------------------- |
| Expecting `loading.tsx` to show for every component independently   | Use component-level Suspense boundaries                              |
| Using Suspense without a fallback                                   | Provide meaningful fallback UI                                       |
| Making every loading state a generic spinner                        | Use skeletons where appropriate                                      |
| Expecting Suspense to speed up database queries                     | Suspense controls UI rendering, not database execution speed         |
| Using `Promise.all()` for dependent queries                         | Use sequential fetching when later queries depend on earlier results |
| Making the entire page wait for unrelated data                      | Use independent Suspense boundaries                                  |
| Forgetting that loading components are Server Components by default | Add `"use client"` only when client-side functionality is needed     |
| Assuming all asynchronous code automatically displays a fallback    | The work must suspend within the relevant Suspense boundary          |

# **18. Interview Questions and Answers**

## **Basic Level**

**Q1. What is loading UI in Next.js?**

Loading UI is a temporary interface displayed while a route or component is waiting for asynchronous work to complete.

**Q2. What is&#x20;**`loading.tsx`**?**

It is a special Next.js file convention that automatically provides a loading UI for a route segment.

**Q3. What is React Suspense?**

Suspense is a React component that displays fallback UI while its child component is suspended.

**Q4. What is a skeleton loader?**

A skeleton loader is a placeholder that resembles the structure of the content being loaded.

**Q5. Where should we place&#x20;**`loading.tsx`**?**

Inside the route segment folder where we want to define its loading UI.

## **Intermediate Level**

**Q6. What is the difference between&#x20;**`loading.tsx`**&#x20;and Suspense?**

`loading.tsx` is a Next.js convention that automatically creates a loading boundary for a route segment. Suspense is a React component that allows developers to define loading boundaries manually.

**Q7. What is streaming in Next.js?**

Streaming allows the server to send rendered page content progressively instead of waiting for every part to finish.

**Q8. Can we use multiple Suspense boundaries on one page?**

Yes. Each boundary can manage the loading state of a different component or section.

**Q9. What is a loading boundary?**

It is a boundary that separates fallback UI from content that is still loading.

**Q10. Can Suspense work with asynchronous Server Components?**

Yes. An asynchronous Server Component can suspend while waiting for data, allowing its Suspense fallback to be displayed.

## **Advanced Level**

**Q11. Does Suspense make API requests faster?**

No. Suspense does not speed up the underlying request. It controls how the UI responds while asynchronous work is pending.

**Q12. What is the advantage of streaming over waiting for the complete page?**

Streaming allows ready portions of a page to appear while slower portions continue rendering.

**Q13. What is the difference between&#x20;**`Promise.all()`**&#x20;and Suspense?**

`Promise.all()` runs independent asynchronous operations concurrently and waits for all of them to resolve. Suspense manages the fallback UI for suspended rendering.

**Q14. Can we create nested Suspense boundaries?**

Yes. Nested boundaries allow different parts of a component tree to have independent loading states.

**Q15. When should we use skeleton loaders instead of spinners?**

Skeleton loaders are particularly useful when the structure of the upcoming content is predictable, such as product cards, profile sections, and tables.

# **19. Practical Assignment**



Hands-on project

**Build a Dashboard with Streaming Loading UI**



Create a dashboard containing four independent sections.

[Arise - Bootstrap 5 Admin Dashboard #353820 - TemplateMonster](https://images.openai.com/static-rsc-4/mwnaMdi-tq98Rxt0w-vHEOuCPKcFWXqGvgOPC4hbvRMLif5-vusgJZ9SdsrcUdpNmaI1v5WTy9YXlbKhbM8k1154SkREtOH5p0Wq3wyJrCunX2FfHFdP8dCVobtVxUYuKkFqArdLFKi2gOwNcvLHDh-y-PmH26eujFKwG197ybw?purpose=inline)

[templatemonster.com](https://www.templatemonster.com/admin-templates/arise-bootstrap-admin-dashboard-353820.html?utm_source=chatgpt.com)

### **Task 1: Create this folder structure**

```
app/
└── dashboard/
    ├── layout.tsx
    ├── loading.tsx
    ├── page.tsx
    │
    ├── users/
    │   ├── loading.tsx
    │   └── page.tsx
    │
    └── orders/
        ├── loading.tsx
        └── page.tsx
```

### **Task 2: Build dashboard sections**

Create these components:

- User Profile
- Sales Statistics
- Recent Orders
- Notifications

### **Task 3: Simulate asynchronous operations**

Use different delays:

| Component        | Delay     |
| ---------------- | --------- |
| User Profile     | 1 second  |
| Sales Statistics | 3 seconds |
| Recent Orders    | 5 seconds |
| Notifications    | 2 seconds |

### **Task 4: Implement Suspense**

Wrap each component in its own Suspense boundary.

### **Task 5: Create skeleton loaders**

Create separate skeleton components for:

- User profile
- Statistics cards
- Orders table
- Notifications

### **Task 6: Implement route-level loading UI**

Create:

- `dashboard/loading.tsx`
- `dashboard/users/loading.tsx`
- `dashboard/orders/loading.tsx`

### **Task 7: Experiment**

Try the following:

1. Remove Suspense boundaries and observe the difference.
2. Add a single Suspense boundary around the whole dashboard.
3. Add separate Suspense boundaries around each component.
4. Change the asynchronous delays.
5. Replace spinner loading UI with skeleton loaders.

Observe how the user experience changes.

# **20. Quick Revision Notes**

| Concept          | Key point                                 |
| ---------------- | ----------------------------------------- |
| Loading UI       | Temporary UI while content is loading     |
| `loading.tsx`    | Automatic route-segment loading UI        |
| Suspense         | React loading boundary                    |
| Fallback         | UI shown while a child suspends           |
| Streaming        | Progressive delivery of rendered UI       |
| Loading boundary | Separates fallback from suspended content |
| Skeleton loader  | Placeholder resembling actual content     |
| `Promise.all()`  | Runs independent promises concurrently    |
| Nested Suspense  | Multiple independent loading boundaries   |
| `error.tsx`      | Handles errors                            |
| `not-found.tsx`  | Handles not-found states                  |

## **21. Phase 7 Completion Checklist**

**Your progress**

# **0%**

0 of 15 completed

Understand loading UI and its purpose

Understand loading.tsx

Understand how loading boundaries work

Understand React Suspense

Differentiate loading.tsx and Suspense

Understand streaming in Next.js

Create multiple Suspense boundaries

Understand nested Suspense boundaries

Build skeleton loaders

Create reusable skeleton components

Fetch database data in async Server Components

Understand Promise.all() and parallel fetching

Understand nested route loading states

Differentiate loading UI and error UI

Complete the dashboard practical assignment

# **22. What's Next? – Phase 8**

Next phase

# **Error Handling and Not Found Pages**

**Topics we will cover:**

1. Understanding error handling in Next.js.
2. `error.tsx` and Error Boundaries.
3. `global-error.tsx`.
4. The `reset()` function.
5. Handling errors in Server Components.
6. Handling errors in Client Components.
7. `not-found.tsx`.
8. The `notFound()` function.
9. `redirect()` and `permanentRedirect()`.
10. Custom 404 and error pages.
11. Production error-handling best practices.

When ready, say **"OK, Phase 8"** to continue.