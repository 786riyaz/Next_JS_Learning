# **Next.js Complete Learning Roadmap**

# **Phase 4: Pages and Routing Fundamentals**



Beginner → Intermediate

Next.js 16.x



**Architecture:** App Router\
**Language:** TypeScript\
**Goal:** Understand how routing works in Next.js, create multiple pages, navigate between pages, and understand how Next.js handles navigation internally.

In Phase 3, we learned about the project structure and special files such as `page.tsx` and `layout.tsx`.

Now we will use those concepts to build a multi-page application.

# **1. What is Routing?**

Routing is the process of determining which page or content should be displayed based on the URL requested by the user.

Suppose you have a website:

```
https://example.com
```

It contains multiple pages:

```
https://example.com/
https://example.com/about
https://example.com/contact
https://example.com/products
https://example.com/services
```

Each URL represents a different route.

When a user visits a particular URL, the routing system determines which component should be rendered.

### **Real-World Example**

Consider an e-commerce website.

| URL           | Page             |
| ------------- | ---------------- |
| `/`           | Homepage         |
| `/products`   | Products listing |
| `/products/1` | Product details  |
| `/cart`       | Shopping cart    |
| `/checkout`   | Checkout page    |
| `/login`      | Login page       |

Routing connects URLs with the corresponding application pages.

### **Simple Definition**

**Routing is the mechanism that maps a URL path to a particular page or application view.**

# **2. What is File-Based Routing?**

File-Based Routing is a routing approach in which routes are automatically created based on the application's folder and file structure.

Next.js uses file-based routing.

Unlike React Router, where you generally define routes in JavaScript or JSX, Next.js uses special files and folders.

### **Example**

Project structure:

```
src/
└── app/
    │
    ├── page.tsx
    │
    ├── about/
    │   └── page.tsx
    │
    ├── contact/
    │   └── page.tsx
    │
    └── products/
        └── page.tsx
```

Next.js automatically creates these routes:

| File                    | Generated URL |
| ----------------------- | ------------- |
| `app/page.tsx`          | `/`           |
| `app/about/page.tsx`    | `/about`      |
| `app/contact/page.tsx`  | `/contact`    |
| `app/products/page.tsx` | `/products`   |

You don't need to manually register these routes.

### **How Does It Work?**

Consider:

```
app/
└── about/
    └── page.tsx
```

Next.js identifies:

1. `app` as the routing root.
2. `about` as a route segment.
3. `page.tsx` as the page component.
4. `/about` as the generated URL.

This is called file-system-based routing.

# **3. Creating Your First Route**

Let's create an About page.

### **Step 1: Open Your Project**

Open the project created in Phase 2.

```
nextjs-learning/
└── src/
    └── app/
```

### **Step 2: Create an about Folder**

```
src/
└── app/
    └── about/
```

### **Step 3: Create page.tsx**

```
src/
└── app/
    └── about/
        └── page.tsx
```

### **Step 4: Write the Component**

```
export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>

      <p>
        Welcome to our About page.
      </p>
    </div>
  );
}
```

### **Step 5: Open the Browser**

Visit:

```
http://localhost:3000/about
```

You will see your About page.

Route created

### **Important Rules**

- A route folder represents a URL segment.
- `page.tsx` makes that route publicly accessible.
- Folder names become URL segments.
- Next.js automatically discovers routes.
- The page component must be exported as the default export.

# **4. Creating Multiple Pages**

Let's create a website with multiple pages.

### **Project Structure**

```
src/
└── app/
    │
    ├── page.tsx
    │
    ├── about/
    │   └── page.tsx
    │
    ├── contact/
    │   └── page.tsx
    │
    ├── services/
    │   └── page.tsx
    │
    └── products/
        └── page.tsx
```

### **Homepage**

`src/app/page.tsx`

```
export default function HomePage() {
  return (
    <h1>Home Page</h1>
  );
}
```

### **About Page**

`src/app/about/page.tsx`

```
export default function AboutPage() {
  return (
    <h1>About Page</h1>
  );
}
```

### **Contact Page**

`src/app/contact/page.tsx`

```
export default function ContactPage() {
  return (
    <h1>Contact Page</h1>
  );
}
```

### **Services Page**

`src/app/services/page.tsx`

```
export default function ServicesPage() {
  return (
    <h1>Services Page</h1>
  );
}
```

### **Products Page**

`src/app/products/page.tsx`

```
export default function ProductsPage() {
  return (
    <h1>Products Page</h1>
  );
}
```

### **Generated Routes**

| URL         | Component    |
| ----------- | ------------ |
| `/`         | HomePage     |
| `/about`    | AboutPage    |
| `/contact`  | ContactPage  |
| `/services` | ServicesPage |
| `/products` | ProductsPage |

You have now created a five-page website without manually configuring a routing library.

# **5. Understanding URL Paths**

Before going deeper into routing, understand the structure of a URL.

Example:

```
https://example.com/products/electronics?category=laptop
```

A URL can contain multiple parts.

## **URL Structure**

Example URL

## [**https://example.com/products/electronics?category=laptop**](https://example.com/products/electronics?category=laptop\&utm_source=chatgpt.com)

**Protocol**

**https\://**

Communication protocol

**Domain**

**example.com**

Website hostname

**Path**

**/products/electronics**

Resource or route path

**Query String**

**?category=laptop**

Additional URL parameters

### **Important URL Terminology**

| Term         | Meaning                          |
| ------------ | -------------------------------- |
| Protocol     | HTTP or HTTPS                    |
| Domain       | Website hostname                 |
| Pathname     | Path identifying a resource      |
| Query String | Additional parameters            |
| Hash         | Identifies a section or fragment |

Example:

```
https://example.com/products?page=2&limit=10
```

Here:

- Pathname: `/products`
- Query parameter `page`: `2`
- Query parameter `limit`: `10`

We will study dynamic routes and search parameters in later phases.

# **6. Understanding Nested Routes**

Nested routes are routes created by placing folders inside other route folders.

Suppose you are developing an e-commerce application.

You need:

```
/products
/products/electronics
/products/clothing
/products/books
```

You can create:

```
app/
│
└── products/
    │
    ├── page.tsx
    │
    ├── electronics/
    │   └── page.tsx
    │
    ├── clothing/
    │   └── page.tsx
    │
    └── books/
        └── page.tsx
```

### **Generated Routes**

| File                            | URL                     |
| ------------------------------- | ----------------------- |
| `products/page.tsx`             | `/products`             |
| `products/electronics/page.tsx` | `/products/electronics` |
| `products/clothing/page.tsx`    | `/products/clothing`    |
| `products/books/page.tsx`       | `/products/books`       |

### **More Complex Nested Routes**

Consider:

```
app/
│
└── dashboard/
    │
    ├── page.tsx
    │
    ├── users/
    │   ├── page.tsx
    │   │
    │   └── settings/
    │       └── page.tsx
    │
    └── analytics/
        └── page.tsx
```

Generated routes:

```
/dashboard
/dashboard/users
/dashboard/users/settings
/dashboard/analytics
```

### **Important Concept**

Every nested folder adds another URL segment.

For example:

```
app/dashboard/users/settings/page.tsx
```

Generates:

```
/dashboard/users/settings
```

# **7. What are Route Segments?**

A route segment is an individual part of a URL path.

Consider:

```
/dashboard/users/settings
```

This URL contains three segments:

```
dashboard
users
settings
```

Corresponding structure:

```
app/
└── dashboard/
    └── users/
        └── settings/
            └── page.tsx
```

**Route Segment Visualization**

**dashboard**

**users**

**settings**

Three URL segments create a nested route.

### **Segment vs Route**

These terms are related but not identical.

- Segment: One individual part of a URL path.
- Route: The complete path that identifies a page or endpoint.

Example:

```
/products/electronics/laptops
```

Segments:

1. products
2. electronics
3. laptops

Complete route:

```
/products/electronics/laptops
```

# **8. What are Static Routes?**

A static route is a route whose path is explicitly defined in the application structure.

Example:

```
app/
│
├── about/
│   └── page.tsx
│
├── contact/
│   └── page.tsx
│
└── products/
    └── page.tsx
```

Routes:

```
/about
/contact
/products
```

These paths are predefined.

### **Static Route Example**

```
export default function ContactPage() {
  return (
    <div>
      <h1>Contact Us</h1>
      <p>Email: contact@example.com</p>
    </div>
  );
}
```

The route is:

```
/contact
```

### **Static Route vs Dynamic Route**

| Static Route       | Dynamic Route                   |
| ------------------ | ------------------------------- |
| Path is predefined | Path contains variable segments |
| `/about`           | `/products/123`                 |
| `/contact`         | `/users/456`                    |
| `/products`        | `/blog/my-first-post`           |

We will study dynamic routing in Phase 5.

# **9. What is the Link Component?**

The `Link` component is a built-in Next.js component used to navigate between routes.

It is provided by:

```
import Link from "next/link";
```

It is an enhanced version of the HTML anchor (`<a>`) element.

### **Why Do We Need Link?**

Suppose you have two pages:

```
/
/about
```

You want users to navigate from the homepage to the About page.

You can use a normal HTML anchor:

```
<a href="/about">About</a>
```

But this can cause a full-page navigation.

Next.js provides `Link` to enable client-side navigation between application routes.

### **Link Example**

```
import Link from "next/link";

export default function HomePage() {
  return (
    <div>
      <h1>Home Page</h1>

      <Link href="/about">
        Go to About Page
      </Link>
    </div>
  );
}
```

Now clicking the link navigates to `/about`.

### **Link with Multiple Pages**

```
import Link from "next/link";

export default function HomePage() {
  return (
    <nav>
      <Link href="/">Home</Link>

      <Link href="/about">About</Link>

      <Link href="/contact">Contact</Link>

      <Link href="/products">Products</Link>
    </nav>
  );
}
```

### **Link Component Important Props**

| Prop        | Purpose                          |
| ----------- | -------------------------------- |
| `href`      | Destination URL                  |
| `replace`   | Replaces current history entry   |
| `scroll`    | Controls scrolling behavior      |
| `prefetch`  | Controls route prefetching       |
| `target`    | Specifies where to open the link |
| `className` | Applies CSS classes              |
| `onClick`   | Handles click events             |

Example:

```
<Link
  href="/about"
  className="text-blue-500"
>
  About Us
</Link>
```

### **Understanding Prefetching**

Prefetching means loading some route resources before the user actually navigates to that route.

For example:

1. User opens the homepage.
2. Next.js detects a visible Link to `/about`.
3. In production, Next.js may prefetch the route.
4. User clicks About.
5. Navigation can happen faster because some resources are already available.

Example:

```
<Link href="/about" prefetch={false}>
  About
</Link>
```

This disables automatic prefetching for that Link.

**Important:** Prefetching behavior depends on the route type and production environment. Automatic prefetching is generally disabled in development.

Official documentation: [Next.js Link Component](https://nextjs.org/docs/app/api-reference/components/link?utm_source=chatgpt.com)

# **10. Link vs HTML Anchor Tag**

This is a frequently asked interview question.

Both are used to navigate between URLs, but their behavior can differ.

### **HTML Anchor**

```
export default function HomePage() {
  return (
    <a href="/about">
      About
    </a>
  );
}
```

The browser performs a normal document navigation.

### **Next.js Link**

```
import Link from "next/link";

export default function HomePage() {
  return (
    <Link href="/about">
      About
    </Link>
  );
}
```

Next.js handles navigation through its client-side routing system for internal routes.

### **Comparison**

| Feature                     | HTML Anchor      | Next.js Link                    |
| --------------------------- | ---------------- | ------------------------------- |
| Element                     | `<a>`            | Enhanced anchor                 |
| Full document navigation    | Normally yes     | Normally no for internal routes |
| Client-side navigation      | No, by itself    | Yes                             |
| Automatic prefetching       | No               | Supported                       |
| Browser history             | Yes              | Yes                             |
| External URLs               | Yes              | Yes                             |
| Internal Next.js navigation | Basic navigation | Framework-aware navigation      |

### **When Should We Use Each?**

Use `Link` for internal application navigation:

```
<Link href="/dashboard">
  Dashboard
</Link>
```

Use an anchor for ordinary external links:

```
<a
  href="https://www.google.com"
  target="_blank"
  rel="noopener noreferrer"
>
  Google
</a>
```

**Best Practice:** Prefer Next.js `Link` for navigation between internal routes.

# **11. Creating a Navigation Bar**

Let's create a reusable navigation component.

Create:

```
src/
└── components/
    └── Navbar.tsx
```

Code:

```
import Link from "next/link";

export default function Navbar() {
  return (
    <nav>
      <Link href="/">Home</Link>

      <Link href="/about">About</Link>

      <Link href="/services">Services</Link>

      <Link href="/products">Products</Link>

      <Link href="/contact">Contact</Link>
    </nav>
  );
}
```

Now import it into:

```
src/app/layout.tsx
```

```
import Navbar from "@/components/Navbar";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <Navbar />

        {children}
      </body>
    </html>
  );
}
```

Now your navigation bar will be displayed across the pages that use this root layout.

### **Result**

**Example Navigation**

Home

About

Services

Products

Contact

Illustrative navigation structure. Implement it using the Link component in your project.

# **12. What is Client-Side Navigation?**

Client-Side Navigation means changing the displayed page without performing a complete browser document reload.

Next.js uses client-side navigation for internal route transitions.

### **Traditional Navigation**

Suppose the user is on:

```
/home
```

They click a link to:

```
/about
```

With normal document navigation:

1. Browser sends a new request.
2. Server responds with a new HTML document.
3. Browser loads the new document.
4. The page is displayed again.

### **Next.js Client-Side Navigation**

With Next.js Link:

1. User clicks a Link.
2. Next.js intercepts the internal navigation.
3. It obtains the required route information.
4. React updates the relevant UI.
5. Browser history is updated.
6. The new page is displayed without a full document reload.

### **Visual Comparison**

**Traditional**

Browser request

New HTML document

Full page load

**Next.js Link**

Click Link

Client navigation

UI updated

### **Important**

Client-side navigation does not mean that the server is never involved.

Next.js can still request server-generated React Server Component payloads and other required resources during navigation.

The important difference is that the browser does not need to reload the entire HTML document for normal internal navigation.

# **13. What is Programmatic Navigation?**

Programmatic Navigation means navigating to another route through JavaScript code rather than requiring the user to click a Link.

Example:

Suppose a user submits a login form.

After successful login, you want to redirect the user to:

```
/dashboard
```

You can use programmatic navigation.

Next.js provides the `useRouter` hook.

### **Import**

```
import { useRouter } from "next/navigation";
```

### **Example**

```
"use client";

import { useRouter } from "next/navigation";

export default function LoginPage() {
  const router = useRouter();

  function handleLogin() {
    // Login logic

    router.push("/dashboard");
  }

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

### **Explanation**

```
const router = useRouter();
```

This creates access to Next.js navigation methods.

```
router.push("/dashboard");
```

Navigates the user to the dashboard.

### **Important Rule**

`useRouter()` is a Client Component hook.

Therefore, the component must include:

```
"use client";
```

### **Important useRouter Methods**

| Method              | Purpose                                          |
| ------------------- | ------------------------------------------------ |
| `router.push()`     | Navigate to another route                        |
| `router.replace()`  | Navigate without adding a history entry          |
| `router.back()`     | Go to the previous history entry                 |
| `router.forward()`  | Go to the next history entry                     |
| `router.refresh()`  | Refresh the current route's server-rendered data |
| `router.prefetch()` | Prefetch a route                                 |

Let's understand them individually.

# **14. Understanding router.push()**

`router.push()` navigates to another route and adds a new entry to browser history.

Example:

```
"use client";

import { useRouter } from "next/navigation";

export default function HomePage() {
  const router = useRouter();

  return (
    <button onClick={() => router.push("/about")}>
      Go to About
    </button>
  );
}
```

### **How It Works**

Suppose the browser history is:

```
/
```

After executing:

```
router.push("/about");
```

The history becomes:

```
/
/about
```

Now pressing the browser's Back button can take the user back to `/`.

### **Use Cases**

- Login redirection.
- Form submission.
- Checkout flow.
- Conditional navigation.

# **15. Understanding router.replace()**

`router.replace()` navigates to another route without adding a new browser history entry.

Example:

```
"use client";

import { useRouter } from "next/navigation";

export default function LoginPage() {
  const router = useRouter();

  function handleLogin() {
    router.replace("/dashboard");
  }

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

### **push vs replace**

**push()**

Adds history entry

Home → Login → Dashboard

Back can return to Login.

**replace()**

Replaces history entry

Home → Dashboard

Back skips the replaced Login entry.

### **When Should We Use replace()?**

- After successful authentication when you want to remove the login page from the current history entry.
- When correcting an invalid URL state.
- When a redirect should not create an additional history entry.

# **16. Understanding router.back() and router.forward()**

These methods navigate through browser history.

### **router.back()**

Moves to the previous history entry.

```
"use client";

import { useRouter } from "next/navigation";

export default function BackButton() {
  const router = useRouter();

  return (
    <button onClick={() => router.back()}>
      Go Back
    </button>
  );
}
```

### **router.forward()**

Moves to the next history entry if one exists.

```
"use client";

import { useRouter } from "next/navigation";

export default function ForwardButton() {
  const router = useRouter();

  return (
    <button onClick={() => router.forward()}>
      Go Forward
    </button>
  );
}
```

### **Important**

These methods use the browser's session history.

If no previous or next entry exists, there may be nowhere to navigate.

# **17. Understanding router.refresh()**

`router.refresh()` refreshes the current route.

It requests a new Server Component payload and merges the resulting UI without losing unaffected client-side state or browser scroll position.

Example:

```
"use client";

import { useRouter } from "next/navigation";

export default function RefreshButton() {
  const router = useRouter();

  return (
    <button onClick={() => router.refresh()}>
      Refresh Data
    </button>
  );
}
```

### **Real-World Example**

Suppose you have a dashboard displaying user information.

You update the user's profile.

After the update, you want the server-rendered data for the current route to be fetched again.

You can use:

```
router.refresh();
```

### **Important Difference**

`router.refresh()` does not necessarily clear the server-side Data Cache.

If the underlying data is cached, the refreshed UI may still receive cached data.

For actual cache invalidation, Next.js provides mechanisms such as:

- `revalidatePath()`
- `revalidateTag()`

We will cover those in the caching phases.

# **18. Understanding router.prefetch()**

`router.prefetch()` allows you to prefetch a route programmatically.

Example:

```
"use client";

import { useRouter } from "next/navigation";

export default function HomePage() {
  const router = useRouter();

  return (
    <button
      onMouseEnter={() => router.prefetch("/dashboard")}
      onClick={() => router.push("/dashboard")}
    >
      Open Dashboard
    </button>
  );
}
```

### **How Does It Work?**

1. User moves the mouse over the button.
2. `router.prefetch()` starts fetching route resources.
3. User clicks the button.
4. Navigation may be faster because some resources are already available.

### **Link Prefetch vs router.prefetch**

| Link Prefetch                         | router.prefetch                     |
| ------------------------------------- | ----------------------------------- |
| Declarative                           | Programmatic                        |
| Uses Link component                   | Uses useRouter hook                 |
| Automatically handles eligible routes | Developer controls when to prefetch |
| Suitable for normal navigation        | Useful for conditional prefetching  |

# **19. What is usePathname()?**

`usePathname()` is a Next.js hook used to get the current URL pathname.

It is provided by:

```
import { usePathname } from "next/navigation";
```

### **Example**

Suppose the current URL is:

```
https://example.com/products
```

The pathname is:

```
/products
```

### **Code Example**

```
"use client";

import { usePathname } from "next/navigation";

export default function CurrentPath() {
  const pathname = usePathname();

  return (
    <div>
      <h2>Current Path: {pathname}</h2>
    </div>
  );
}
```

Output:

```
Current Path: /products
```

### **Real-World Use Case: Active Navigation**

Suppose you want to highlight the navigation link corresponding to the current page.

```
"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";

export default function Navbar() {
  const pathname = usePathname();

  return (
    <nav>
      <Link
        href="/"
        className={pathname === "/" ? "active" : ""}
      >
        Home
      </Link>

      <Link
        href="/about"
        className={pathname === "/about" ? "active" : ""}
      >
        About
      </Link>

      <Link
        href="/products"
        className={pathname === "/products" ? "active" : ""}
      >
        Products
      </Link>
    </nav>
  );
}
```

### **Important Points**

- Returns the current pathname.
- Does not return the domain.
- Does not return query parameters.
- Must be used in a Client Component.

For example:

| Current URL        | usePathname()   |
| ------------------ | --------------- |
| `/`                | `/`             |
| `/about`           | `/about`        |
| `/products`        | `/products`     |
| `/products/123`    | `/products/123` |
| `/products?page=2` | `/products`     |

# **20. What is useSearchParams()?**

`useSearchParams()` is a Next.js hook used to read query parameters from the current URL.

It is provided by:

```
import { useSearchParams } from "next/navigation";
```

### **First Understand Query Parameters**

Consider this URL:

```
/products?page=2&limit=10&category=electronics
```

Here:

| Parameter | Value       |
| --------- | ----------- |
| page      | 2           |
| limit     | 10          |
| category  | electronics |

These are called query parameters or search parameters.

### **Example**

```
"use client";

import { useSearchParams } from "next/navigation";

export default function ProductsPage() {
  const searchParams = useSearchParams();

  const page = searchParams.get("page");
  const limit = searchParams.get("limit");
  const category = searchParams.get("category");

  return (
    <div>
      <h1>Products</h1>

      <p>Page: {page}</p>
      <p>Limit: {limit}</p>
      <p>Category: {category}</p>
    </div>
  );
}
```

Open:

```
http://localhost:3000/products?page=2&limit=10&category=electronics
```

Output:

```
Products

Page: 2
Limit: 10
Category: electronics
```

### **Important Methods**

`useSearchParams()` returns a read-only URLSearchParams-like object.

| Method       | Purpose                           |
| ------------ | --------------------------------- |
| `get()`      | Gets the first value              |
| `getAll()`   | Gets all values for a parameter   |
| `has()`      | Checks whether a parameter exists |
| `keys()`     | Returns parameter names           |
| `values()`   | Returns parameter values          |
| `entries()`  | Returns parameter key-value pairs |
| `toString()` | Returns the query string          |

Example:

```
const category = searchParams.get("category");

const hasPage = searchParams.has("page");
```

### **How to Update Search Parameters?**

You cannot directly modify the object returned by `useSearchParams()`.

You can create a new URLSearchParams object and navigate to the updated URL.

```
"use client";

import { useRouter, useSearchParams } from "next/navigation";

export default function Pagination() {
  const router = useRouter();
  const searchParams = useSearchParams();

  function goToPageTwo() {
    const params = new URLSearchParams(
      searchParams.toString()
    );

    params.set("page", "2");

    router.push(`/products?${params.toString()}`);
  }

  return (
    <button onClick={goToPageTwo}>
      Go to Page 2
    </button>
  );
}
```

### **Real-World Use Cases**

- Pagination.
- Search filters.
- Sorting.
- Category filtering.
- Product listings.
- Search results.

### **Important Next.js Detail**

For statically rendered routes, using `useSearchParams()` in a Client Component can require a surrounding Suspense boundary in production.

Example:

```
import { Suspense } from "react";
import ProductsFilter from "./ProductsFilter";

export default function ProductsPage() {
  return (
    <Suspense fallback={<p>Loading filters...</p>}>
      <ProductsFilter />
    </Suspense>
  );
}
```

We will explore Suspense and search parameters in greater detail later.

# **21. What is useParams()?**

`useParams()` is a Next.js hook used to access dynamic route parameters.

It is provided by:

```
import { useParams } from "next/navigation";
```

First, understand dynamic routes.

Suppose your application has these URLs:

```
/products/101
/products/102
/products/103
```

You do not want to create separate folders for every product.

Instead, you can create a dynamic route.

### **Project Structure**

```
app/
│
└── products/
    │
    └── [id]/
        └── page.tsx
```

Here:

```
[id]
```

represents a dynamic route segment.

### **Code Example**

```
"use client";

import { useParams } from "next/navigation";

export default function ProductPage() {
  const params = useParams();

  return (
    <div>
      <h1>Product Details</h1>

      <p>Product ID: {params.id}</p>
    </div>
  );
}
```

Now visit:

```
/products/101
```

Output:

```
Product Details

Product ID: 101
```

Visit:

```
/products/102
```

Output:

```
Product Details

Product ID: 102
```

The same page handles both URLs.

### **Important**

- `useParams()` reads dynamic route parameters.
- It returns an object.
- Parameter names come from the folder names.
- It must be used in a Client Component.

### **Multiple Dynamic Parameters**

Structure:

```
app/
└── users/
    └── [userId]/
        └── orders/
            └── [orderId]/
                └── page.tsx
```

URL:

```
/users/25/orders/500
```

Parameters:

```
{
  userId: "25",
  orderId: "500"
}
```

You can access them:

```
const params = useParams();

console.log(params.userId);
console.log(params.orderId);
```

We will study dynamic routes, catch-all routes, and route parameters in Phase 5.

# **22. useRouter vs usePathname vs useSearchParams vs useParams**

These four hooks are frequently confused.

Let's compare them.

| Hook                | Purpose                         | Example                 |
| ------------------- | ------------------------------- | ----------------------- |
| `useRouter()`       | Navigate and control navigation | `router.push("/about")` |
| `usePathname()`     | Get current pathname            | `/products/101`         |
| `useSearchParams()` | Read query parameters           | `?page=2`               |
| `useParams()`       | Read dynamic route parameters   | `{ id: "101" }`         |

### **Complete Example**

Suppose the current URL is:

```
https://example.com/products/101?page=2&category=electronics
```

The results would be:

**Current URL**

/products/101?page=2&category=electronics

**usePathname()**

**/products/101**

Returns pathname.

**useParams()**

**{ id: "101" }**

Returns dynamic route parameters.

**useSearchParams()**

**page = 2, category = electronics**

Returns query parameters.

**useRouter()**

**push(), replace(), back(), refresh()**

Controls navigation.

# **23. Understanding Navigation in Server Components vs Client Components**

This is an important Next.js concept.

Not all navigation functionality requires a Client Component.

Next.js provides different navigation mechanisms depending on where you are working.

### **Server Components**

You can use:

- `Link` component.
- `redirect()`.
- `permanentRedirect()`.

Example:

```
import { redirect } from "next/navigation";

export default async function DashboardPage() {
  const isAuthenticated = false;

  if (!isAuthenticated) {
    redirect("/login");
  }

  return <h1>Dashboard</h1>;
}
```

`redirect()` performs a server-side redirect.

It can also be used in supported Server Actions and other server contexts.

### **Client Components**

You can use:

- `Link`.
- `useRouter()`.
- `usePathname()`.
- `useSearchParams()`.
- `useParams()`.

Example:

```
"use client";

import { useRouter } from "next/navigation";

export default function DashboardButton() {
  const router = useRouter();

  return (
    <button onClick={() => router.push("/dashboard")}>
      Dashboard
    </button>
  );
}
```

### **Comparison**

| Server Component                  | Client Component                |
| --------------------------------- | ------------------------------- |
| Can use Link                      | Can use Link                    |
| Can use redirect()                | Can use useRouter()             |
| Can use permanentRedirect()       | Can use usePathname()           |
| Cannot use useRouter hook         | Can use useRouter hook          |
| Cannot use browser event handlers | Supports browser event handlers |

**Remember:** The `use client` directive is not required merely because you use the `Link` component.

# **24. How Next.js Navigation Works Internally**

Let's understand the high-level process.

Suppose a user is visiting:

```
/
```

And clicks:

```
<Link href="/about">About</Link>
```

The process is approximately:

```
User Clicks Link
       |
       v
Next.js Intercepts Navigation
       |
       v
Checks Available Prefetched Data
       |
       v
Fetches Required Route Data
       |
       v
Receives React Server Component Payload
       |
       v
Updates Client-Side Router State
       |
       v
React Updates Relevant UI
       |
       v
New Page Displayed
```

### **What is RSC Payload?**

RSC stands for React Server Components.

The RSC Payload contains serialized information representing the Server Component output and information needed to reconcile the client-side UI.

It is used by Next.js during server rendering and client-side navigation.

### **Why Does Next.js Use RSC Payload?**

It helps Next.js coordinate server-rendered components and client-side navigation without requiring a complete HTML document reload for every internal navigation.

We will study Server Components and RSC architecture in Phases 9–11.

# **25. Common Routing Mistakes**

### **Mistake 1: Missing page.tsx**

Incorrect:

```
app/
└── about/
    └── About.tsx
```

This does not create `/about`.

Correct:

```
app/
└── about/
    └── page.tsx
```

### **Mistake 2: Incorrect Import**

Incorrect:

```
import { Link } from "next/link";
```

Correct:

```
import Link from "next/link";
```

`Link` is a default export.

### **Mistake 3: Using useRouter from the Wrong Package**

Incorrect:

```
import { useRouter } from "next/router";
```

For the App Router, use:

```
import { useRouter } from "next/navigation";
```

`next/router` belongs to the older Pages Router.

### **Mistake 4: Forgetting use client**

Incorrect:

```
import { useRouter } from "next/navigation";

export default function Home() {
  const router = useRouter();

  return <button onClick={() => router.push("/about")}>About</button>;
}
```

Correct:

```
"use client";

import { useRouter } from "next/navigation";

export default function Home() {
  const router = useRouter();

  return (
    <button onClick={() => router.push("/about")}>
      About
    </button>
  );
}
```

### **Mistake 5: Using HTML Anchor for Every Internal Route**

You can use anchors, but Next.js `Link` is generally preferable for internal application navigation.

### **Mistake 6: Confusing Pathname with Query Parameters**

For:

```
/products?page=2
```

`usePathname()` returns:

```
/products
```

It does not return:

```
/products?page=2
```

Use `useSearchParams()` for query parameters.

# **26. Practical Assignment – Phase 4**

Now let's implement a small multi-page application.

**Project: Next.js Multi-Page Website**

### **Task 1: Create the Following Folder Structure**

```
src/
│
├── app/
│   │
│   ├── page.tsx
│   │
│   ├── about/
│   │   └── page.tsx
│   │
│   ├── contact/
│   │   └── page.tsx
│   │
│   ├── services/
│   │   └── page.tsx
│   │
│   └── products/
│       ├── page.tsx
│       │
│       ├── electronics/
│       │   └── page.tsx
│       │
│       └── clothing/
│           └── page.tsx
│
└── components/
    └── Navbar.tsx
```

### **Task 2: Create Navigation Component**

`src/components/Navbar.tsx`

```
"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";

export default function Navbar() {
  const pathname = usePathname();

  const links = [
    { name: "Home", path: "/" },
    { name: "About", path: "/about" },
    { name: "Contact", path: "/contact" },
    { name: "Services", path: "/services" },
    { name: "Products", path: "/products" },
  ];

  return (
    <nav>
      {links.map((link) => (
        <Link
          key={link.path}
          href={link.path}
          style={{
            marginRight: "20px",
            fontWeight:
              pathname === link.path ? "bold" : "normal",
          }}
        >
          {link.name}
        </Link>
      ))}
    </nav>
  );
}
```

### **Task 3: Add Navbar to Root Layout**

`src/app/layout.tsx`

```
import Navbar from "@/components/Navbar";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <Navbar />

        {children}
      </body>
    </html>
  );
}
```

### **Task 4: Create Products Page**

`src/app/products/page.tsx`

```
import Link from "next/link";

export default function ProductsPage() {
  return (
    <div>
      <h1>Products</h1>

      <Link href="/products/electronics">
        Electronics
      </Link>

      <br />

      <Link href="/products/clothing">
        Clothing
      </Link>
    </div>
  );
}
```

### **Task 5: Create Electronics Page**

`src/app/products/electronics/page.tsx`

```
export default function ElectronicsPage() {
  return (
    <div>
      <h1>Electronics</h1>

      <p>Welcome to Electronics Category.</p>
    </div>
  );
}
```

### **Task 6: Create Clothing Page**

`src/app/products/clothing/page.tsx`

```
export default function ClothingPage() {
  return (
    <div>
      <h1>Clothing</h1>

      <p>Welcome to Clothing Category.</p>
    </div>
  );
}
```

### **Task 7: Test All Routes**

Visit the following URLs:

```
http://localhost:3000/
http://localhost:3000/about
http://localhost:3000/contact
http://localhost:3000/services
http://localhost:3000/products
http://localhost:3000/products/electronics
http://localhost:3000/products/clothing
```

Verify that:

- All pages load correctly.
- Navbar appears on every page.
- Clicking navigation links works.
- Active navigation links are highlighted.
- Navigation does not cause a full document reload.

### **Task 8: Practice Programmatic Navigation**

Create a button that navigates to `/products` using `router.push()`.

Create another button that navigates to `/about` using `router.replace()`.

Also implement:

- Back button.
- Forward button.
- Refresh button.

# **27. Phase 4 – Interview Questions**

### **Basic Questions**

**Q1. What is routing?**

Routing is the mechanism that maps URL paths to application pages or endpoints.

**Q2. What is file-based routing?**

It is a routing approach in which routes are automatically created based on the folder and file structure.

**Q3. What is the purpose of page.tsx?**

It defines the UI for a particular route.

**Q4. What is a nested route?**

A nested route is a route created by placing folders inside other route folders.

**Q5. What is a route segment?**

A route segment is an individual part of a URL path.

**Q6. What is a static route?**

A route whose path is predefined in the application structure.

### **Intermediate Questions**

**Q7. What is the difference between Link and an anchor tag?**

Link supports Next.js client-side navigation and prefetching, whereas a normal anchor generally performs document navigation.

**Q8. What is client-side navigation?**

It is navigation that updates the application UI without reloading the complete HTML document.

**Q9. What is programmatic navigation?**

Navigation initiated through JavaScript code rather than a direct user-clicked link.

**Q10. What is useRouter()?**

It is a Next.js hook that provides navigation methods such as push, replace, back, forward, refresh, and prefetch.

**Q11. What is the difference between router.push() and router.replace()?**

`push()` adds a new browser history entry, whereas `replace()` replaces the current history entry.

**Q12. What is usePathname()?**

It returns the current URL pathname.

**Q13. What is useSearchParams()?**

It provides access to query parameters in the current URL.

**Q14. What is useParams()?**

It provides access to dynamic route parameters.

**Q15. Can we use useRouter() inside Server Components?**

No. `useRouter()` is a Client Component hook.

### **Advanced Questions**

**Q16. What is route prefetching?**

Route prefetching is the process of loading route resources before navigation to improve the navigation experience.

**Q17. What is router.refresh()?**

It requests a new Server Component payload for the current route and updates the UI.

**Q18. Does router.refresh() clear the server-side Data Cache?**

No. It does not necessarily invalidate cached server data.

**Q19. What is the difference between pathname and search parameters?**

Pathname identifies the route, whereas search parameters provide additional key-value information in the URL.

**Q20. What is RSC Payload?**

It is a serialized representation of React Server Component output and related information used by Next.js.

**Q21. Can we use Link inside Server Components?**

Yes. Link can be used in both Server and Client Components.

**Q22. Does client-side navigation mean the server is never involved?**

No. Next.js can request server-generated RSC payloads and other resources during client-side navigation.

**Q23. Which package should we import useRouter from in App Router?**

```
import { useRouter } from "next/navigation";
```

**Q24. Why do we use usePathname() in a Navbar?**

To identify the current route and highlight the corresponding navigation link.

**Q25. What is the difference between redirect() and router.push()?**

`redirect()` is used in supported server-side contexts and Server Actions, whereas `router.push()` is used for client-side programmatic navigation.

# **28. Quick Revision Notes**

| Concept             | Important Point                         |
| ------------------- | --------------------------------------- |
| Routing             | Maps URL to page                        |
| File-Based Routing  | Routes generated from files and folders |
| Nested Routing      | Routes inside other route folders       |
| Static Route        | Predefined URL                          |
| Route Segment       | Individual URL path segment             |
| `page.tsx`          | Defines route UI                        |
| `Link`              | Internal navigation                     |
| Anchor              | Normal URL navigation                   |
| Client Navigation   | Navigation without full document reload |
| `useRouter()`       | Navigation methods                      |
| `router.push()`     | Adds history entry                      |
| `router.replace()`  | Replaces history entry                  |
| `router.back()`     | Previous history entry                  |
| `router.forward()`  | Next history entry                      |
| `router.refresh()`  | Refreshes current route's server UI     |
| `router.prefetch()` | Programmatic route prefetching          |
| `usePathname()`     | Gets pathname                           |
| `useSearchParams()` | Reads query parameters                  |
| `useParams()`       | Reads dynamic route parameters          |
| `redirect()`        | Performs supported server-side redirect |
| RSC Payload         | Serialized Server Component information |

# **29. Phase 4 Completion Checklist**

| Topic                       | Status   |
| --------------------------- | -------- |
| Introduction to Routing     | Covered  |
| File-Based Routing          | Covered  |
| Creating First Route        | Covered  |
| Multiple Pages              | Covered  |
| URL Structure               | Covered  |
| Nested Routes               | Covered  |
| Route Segments              | Covered  |
| Static Routes               | Covered  |
| Link Component              | Covered  |
| Link vs Anchor              | Covered  |
| Navigation Bar              | Covered  |
| Client-Side Navigation      | Covered  |
| Programmatic Navigation     | Covered  |
| useRouter                   | Covered  |
| router.push                 | Covered  |
| router.replace              | Covered  |
| router.back                 | Covered  |
| router.forward              | Covered  |
| router.refresh              | Covered  |
| router.prefetch             | Covered  |
| usePathname                 | Covered  |
| useSearchParams             | Covered  |
| useParams                   | Covered  |
| Server vs Client Navigation | Covered  |
| Navigation Internals        | Covered  |
| Common Mistakes             | Covered  |
| Interview Questions         | Covered  |
| Practical Assignment        | Assigned |

# **Next: Phase 5 – Dynamic Routes and Route Parameters**

In Phase 5, we will learn how to create dynamic pages that work with different IDs, usernames, product slugs, and other URL parameters.

Topics:

1. What are Dynamic Routes?
2. Why Do We Need Dynamic Routes?
3. Dynamic Route Syntax `[id]`.
4. Creating Dynamic Pages.
5. Understanding params.
6. Accessing params in Server Components.
7. Accessing params in Client Components.
8. Multiple Dynamic Segments.
9. Catch-All Routes `[...slug]`.
10. Optional Catch-All Routes `[[...slug]]`.
11. Dynamic Route Matching.
12. Static vs Dynamic Routes.
13. Dynamic Routes with Database Integration.
14. Generating Dynamic Metadata.
15. generateStaticParams().
16. Dynamic Routes with MongoDB.
17. Dynamic Routes with MySQL.
18. Real-World Product Details Example.
19. Common Dynamic Routing Errors.
20. Interview Questions.
21. Practical Assignment.

**Phase 4 is complete.**

When you're ready, simply say **"OK, Phase 5"**.