# **Next.js Complete Learning Roadmap – Phase 8**

## **Error Handling and Not Found Pages**

Phase 8

Intermediate

Next.js 16.x

App Router

TypeScript

## **1. Learning objectives**

In this phase, you will learn how to handle errors and invalid routes in a Next.js application.

By the end of this phase, you should understand:

1. What error handling is and why it matters.
2. How `error.tsx` works.
3. How React Error Boundaries work.
4. The difference between `error.tsx` and `global-error.tsx`.
5. How to use the `reset()` function.
6. How to handle errors in Server Components.
7. How to handle errors in Client Components.
8. How to use `not-found.tsx`.
9. How to use the `notFound()` function.
10. How `redirect()` and `permanentRedirect()` work.
11. How to create custom 404 and error pages.
12. How to implement production-ready error handling.

# **2. What is Error Handling?**

Error handling is the process of detecting, managing, and responding to errors that occur while an application is running.

Errors can occur for several reasons:

- Database connection failure.
- API request failure.
- Invalid user input.
- Missing records.
- Unauthorized access.
- Unexpected application bugs.
- Network problems.
- Server-side rendering failures.

For example, imagine a user opens a product page:

```
/products/101
```

Your application tries to fetch product `101` from MongoDB.

But suppose the database is unavailable.

Without error handling, the application may display a generic failure or an unhelpful error screen.

With error handling, you can display a meaningful message.

Without error handling

**Application Error**

Something failed.

Poor user experience.

With error handling

**Unable to load products**

Please try again.

Clear feedback and recovery action.

### **Why is error handling important?**

- Prevents unexpected crashes from producing confusing UI.
- Improves user experience.
- Helps users recover from temporary failures.
- Protects sensitive internal error details.
- Makes applications more reliable.
- Helps developers diagnose production issues.

# **3. Types of Errors in Next.js**

Before learning the Next.js error-handling files, understand the common categories of errors.

| Error type           | Example                             | Typical handling                       |
| -------------------- | ----------------------------------- | -------------------------------------- |
| Runtime error        | Accessing a property of `undefined` | Error boundary                         |
| Database error       | MongoDB connection failure          | `try...catch`, error boundary          |
| API error            | Server returns HTTP 500             | Check response and handle failure      |
| Validation error     | Invalid email address               | Form-level validation                  |
| Not Found            | Product does not exist              | `notFound()`                           |
| Authentication error | User is not logged in               | Redirect or unauthorized UI            |
| Authorization error  | User lacks permission               | Forbidden response or access-denied UI |

**Important:** Not every error should be handled by `error.tsx`.

For example, an invalid email address should normally produce a validation message near the input field, not an application-wide error screen.

# **4. Understanding&#x20;**`error.tsx`

Next.js provides a special file convention:

```
error.tsx
```

It creates an error boundary for a route segment and its nested content.

An error boundary catches errors thrown during rendering in its child component tree and displays a fallback UI.

### **Example folder structure**

```
app/
├── layout.tsx
├── page.tsx
│
└── products/
    ├── layout.tsx
    ├── error.tsx
    └── page.tsx
```

The `error.tsx` file handles errors in the products route segment and its descendants, subject to the error-boundary hierarchy.

### **Step 1: Create an error component**

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

### **Important points**

1. `error.tsx` must be a Client Component.
2. It receives an `error` prop.
3. It receives a `reset` function.
4. It displays fallback UI when a caught error occurs.
5. It can provide a retry action.

### **Why is&#x20;**`"use client"`**&#x20;required?**

Because error boundaries rely on React client-side behavior.

Next.js requires the `error.tsx` component to be a Client Component.

# **5. Understanding the&#x20;**`error`**&#x20;Prop**

The `error` prop contains information about the error that was caught.

Example:

```
export default function ProductsError({
  error,
}: {
  error: Error & { digest?: string };
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>

      <p>{error.message}</p>
    </div>
  );
}
```

### **Important properties**

| Property        | Description                                                                      |
| --------------- | -------------------------------------------------------------------------------- |
| `error.message` | Error message                                                                    |
| `error.name`    | Error type or name                                                               |
| `error.stack`   | Stack trace, when available                                                      |
| `error.digest`  | Optional identifier that can help correlate a server error with server-side logs |

### **Important security consideration**

In production, Next.js may hide sensitive server error details from the browser.

For server errors, the client-side error message may be generic, and `digest` can help correlate it with server logs.

**Best practice:** Do not expose database connection strings, API keys, internal SQL queries, or stack traces to users.

Instead, log detailed diagnostic information securely on the server and show a friendly message to the user.

# **6. Understanding the&#x20;**`reset()`**&#x20;Function**

The `reset()` function is provided by Next.js through the error boundary.

It attempts to recover by re-rendering the segment where the error occurred.

### **Example**

```
// app/dashboard/error.tsx

"use client";

export default function DashboardError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Dashboard failed to load.</h2>

      <button onClick={reset}>
        Retry
      </button>
    </div>
  );
}
```

### **What happens when the user clicks Retry?**

1. The `reset()` function is called.
2. Next.js attempts to render the failed segment again.
3. If the error no longer occurs, the normal UI appears.
4. If the error occurs again, the error fallback remains visible.

### **Important distinction**

`reset()` does not guarantee that the underlying problem will be fixed.

For example, if your database is still unavailable, retrying the render may fail again.

### **When should you use it?**

- Temporary API failures.
- Temporary network issues.
- Intermittent database problems.
- Recoverable rendering failures.

# **7. How Error Boundaries Work in Nested Routes**

You can create multiple error boundaries at different route levels.

### **Folder structure**

```
app/
├── layout.tsx
├── error.tsx
│
└── dashboard/
    ├── layout.tsx
    ├── error.tsx
    ├── page.tsx
    │
    └── users/
        ├── error.tsx
        └── page.tsx
```

Each boundary handles errors from its child subtree.

### **Error boundary hierarchy**

```
Root Layout
  |
  └── Root Error Boundary
        |
        └── Dashboard Layout
              |
              └── Dashboard Error Boundary
                    |
                    └── Users Page
                          |
                          └── Users Error Boundary
```

The nearest applicable error boundary handles a rendering error.

For example:

| Error location          | Error boundary                           |
| ----------------------- | ---------------------------------------- |
| Dashboard page          | `dashboard/error.tsx`                    |
| Users page              | `dashboard/users/error.tsx`              |
| Dashboard layout itself | Parent boundary, such as `app/error.tsx` |
| Root layout itself      | `global-error.tsx`                       |

**Important:** A segment's `error.tsx` does not catch errors thrown by that same segment's `layout.tsx`, because the boundary is placed inside that layout. The parent segment's error boundary handles such errors.

# **8. Understanding&#x20;**`global-error.tsx`

Next.js provides another special file:

```
global-error.tsx
```

It is used to handle errors in the root layout.

### **Why do we need it?**

Suppose your application has:

```
app/
├── layout.tsx
├── error.tsx
└── page.tsx
```

The root layout is outside the boundary created by `app/error.tsx`.

Therefore, if the root layout itself throws an error, `app/error.tsx` cannot catch it.

This is where `global-error.tsx` becomes useful.

### **Folder structure**

```
app/
├── layout.tsx
├── global-error.tsx
├── error.tsx
└── page.tsx
```

### **Example**

```
// app/global-error.tsx

"use client";

export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <html lang="en">
      <body>
        <h1>Something went wrong!</h1>

        <p>We encountered an unexpected application error.</p>

        <button onClick={reset}>
          Try Again
        </button>
      </body>
    </html>
  );
}
```

### **Important rules**

1. `global-error.tsx` must be a Client Component.
2. It handles errors that escape to the root-level boundary, including root layout errors.
3. It must define its own `<html>` and `<body>` tags.
4. It replaces the root layout when active.
5. It is useful for application-wide fallback UI.

### **Difference between&#x20;**`error.tsx`**&#x20;and&#x20;**`global-error.tsx`

| Feature                                 | `error.tsx`                  | `global-error.tsx`         |
| --------------------------------------- | ---------------------------- | -------------------------- |
| Purpose                                 | Segment-level error handling | Root-level error handling  |
| Client Component required               | Yes                          | Yes                        |
| Catches errors in its own parent layout | No                           | Handles root layout errors |
| Requires `<html>` and `<body>`          | No                           | Yes                        |
| Can provide `reset()`                   | Yes                          | Yes                        |

# **9. Handling Errors in Server Components**

Server Components can perform asynchronous operations such as:

- Database queries.
- API requests.
- File operations.
- External service calls.

These operations can fail.

You can handle expected failures using `try...catch`.

### **Example: Database operation**

```
// app/products/page.tsx

import { getProducts } from "@/lib/products";

export default async function ProductsPage() {
  try {
    const products = await getProducts();

    return (
      <div>
        <h1>Products</h1>

        {products.map((product: any) => (
          <p key={product._id}>{product.name}</p>
        ))}
      </div>
    );
  } catch (error) {
    console.error("Product fetching failed:", error);

    throw new Error("Failed to load products");
  }
}
```

If the error is thrown during rendering, the nearest applicable error boundary can display its fallback UI.

### **Why use&#x20;**`try...catch`**?**

It allows you to:

1. Detect the failure.
2. Log the diagnostic information.
3. Handle expected failures.
4. Throw a suitable error for the nearest error boundary when recovery cannot happen locally.

### **Important security practice**

Do not blindly throw the original database or infrastructure error to the client.

Prefer logging the original error on the server and throwing a safe, generic error when appropriate.

# **10. Expected Errors vs Unexpected Errors**

This is one of the most important concepts in production applications.

Not every failure should be treated as an exception.

### **Expected errors**

These are normal situations that your application should anticipate.

Examples:

- Invalid email.
- Incorrect password.
- Product not found.
- Insufficient account balance.
- Form validation failure.
- User does not have permission.

These should usually be handled explicitly and returned as application state or an appropriate HTTP response.

### **Unexpected errors**

These are failures that indicate something went wrong unexpectedly.

Examples:

- Database server unavailable.
- Programming bug.
- Unexpected null reference.
- Unhandled external service failure.

These may be handled by throwing an error and allowing the nearest error boundary to display fallback UI.

### **Comparison**

| Feature                  | Expected error     | Unexpected error |
| ------------------------ | ------------------ | ---------------- |
| Predictable?             | Usually yes        | Usually no       |
| Example                  | Invalid password   | Database crash   |
| Typical handling         | Return error state | Throw exception  |
| User experience          | Specific message   | General fallback |
| Error boundary required? | Usually not        | Often useful     |

**Professional rule:** Handle expected failures explicitly. Use error boundaries as a safety net for unexpected rendering failures.

# **11. Handling Expected Errors with Server Actions**

Suppose you have a login form.

If the password is incorrect, you should not display a full-screen application error.

Instead, return a validation result.

In Next.js, Server Actions can return state that the form displays.

### **Example: Server Action**

```
// app/login/actions.ts

"use server";

export async function loginAction(
  prevState: { error: string | null },
  formData: FormData
) {
  const email = formData.get("email");
  const password = formData.get("password");

  if (!email || !password) {
    return {
      error: "Email and password are required.",
    };
  }

  // Perform authentication here.

  const isValid = false;

  if (!isValid) {
    return {
      error: "Invalid email or password.",
    };
  }

  return {
    error: null,
  };
}
```

### **Example: Client form**

```
// app/login/LoginForm.tsx

"use client";

import { useActionState } from "react";
import { loginAction } from "./actions";

const initialState = {
  error: null as string | null,
};

export default function LoginForm() {
  const [state, formAction, pending] = useActionState(
    loginAction,
    initialState
  );

  return (
    <form action={formAction}>
      <input
        name="email"
        type="email"
        placeholder="Email"
      />

      <input
        name="password"
        type="password"
        placeholder="Password"
      />

      {state.error && <p>{state.error}</p>}

      <button disabled={pending}>
        {pending ? "Logging in..." : "Login"}
      </button>
    </form>
  );
}
```

### **What is happening?**

- The form submits data to a Server Action.
- The action validates the data.
- It returns an error state for invalid credentials.
- The Client Component displays that message.
- The entire page does not need to enter an error boundary.

**Note:** This is a simplified demonstration. A production login implementation must use a real authentication provider or secure password verification, validate inputs on the server, and establish a secure session.

# **12. What is&#x20;**`not-found.tsx`**?**

Sometimes an application does not encounter a technical error. Instead, the requested resource simply does not exist.

For example:

```
/products/999999
```

If product `999999` does not exist, displaying a generic error page is not appropriate.

You should display a 404 Not Found page.

Next.js provides:

```
not-found.tsx
```

### **Example folder structure**

```
app/
├── layout.tsx
├── not-found.tsx
├── page.tsx
│
└── products/
    ├── page.tsx
    └── [id]/
        └── page.tsx
```

### **Create a custom 404 page**

```
// app/not-found.tsx

import Link from "next/link";

export default function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>

      <p>The page you are looking for does not exist.</p>

      <Link href="/">
        Go to Home
      </Link>
    </div>
  );
}
```

### **What does it do?**

Next.js uses this file to render a not-found UI when the appropriate not-found handling is triggered.

# **13. Understanding the&#x20;**`notFound()`**&#x20;Function**

Creating `not-found.tsx` is not enough for every dynamic resource.

You also need to tell Next.js when a requested resource does not exist.

For this, Next.js provides:

```
import { notFound } from "next/navigation";
```

The `notFound()` function terminates rendering of the current route segment and displays the relevant not-found UI.

### **Example: Dynamic product page**

```
// app/products/[id]/page.tsx

import { notFound } from "next/navigation";

async function getProduct(id: string) {
  const products = [
    { id: "101", name: "Laptop" },
    { id: "102", name: "Mobile" },
  ];

  return products.find((product) => product.id === id);
}

export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  const product = await getProduct(id);

  if (!product) {
    notFound();
  }

  return (
    <div>
      <h1>{product.name}</h1>
      <p>Product ID: {product.id}</p>
    </div>
  );
}
```

### **What happens?**

For:

```
/products/101
```

The product exists, so Next.js displays the product page.

For:

```
/products/999
```

The product does not exist, so `notFound()` is called and the not-found UI is displayed.

**Product lookup flow**

User requests `/products/999`

Fetch product from database

Found

**Product page**

Not found

**404 UI**

### **Important points**

- `notFound()` is imported from `next/navigation`.
- It does not return a normal value.
- It throws a special internal control-flow signal.
- It stops rendering the current route segment.
- It displays the nearest relevant not-found UI.
- You should not catch and suppress its internal control-flow signal with a broad `try...catch`.

# **14. Nested&#x20;**`not-found.tsx`

You can create not-found UI at different route levels.

### **Folder structure**

```
app/
├── not-found.tsx
│
└── dashboard/
    ├── not-found.tsx
    ├── page.tsx
    │
    └── users/
        ├── not-found.tsx
        └── [id]/
            └── page.tsx
```

The nearest applicable not-found boundary is used when `notFound()` is triggered.

For example:

- General application not-found UI: `app/not-found.tsx`
- Dashboard-specific not-found UI: `app/dashboard/not-found.tsx`
- Users-specific not-found UI: `app/dashboard/users/not-found.tsx`

This lets you customize not-found messages for different parts of your application.

# **15. Difference Between&#x20;**`error.tsx`**&#x20;and&#x20;**`not-found.tsx`

This is a common interview question.

| Feature                   | `error.tsx`              | `not-found.tsx`                              |
| ------------------------- | ------------------------ | -------------------------------------------- |
| Purpose                   | Handles rendering errors | Handles missing resources/routes             |
| Trigger                   | Uncaught rendering error | `notFound()` or framework not-found handling |
| Client Component required | Yes                      | No                                           |
| Receives `error` prop     | Yes                      | No                                           |
| Receives `reset()`        | Yes                      | No                                           |
| Typical example           | Database failure         | Product does not exist                       |

### **Simple rule**

- Something went wrong technically → Error UI.
- Something does not exist → Not Found UI.

# **16. Understanding&#x20;**`redirect()`

Sometimes you want to send users from one route to another.

For example:

- Redirect unauthenticated users to login.
- Redirect users after submitting a form.
- Redirect an old URL to a new URL.

Next.js provides:

```
import { redirect } from "next/navigation";
```

### **Example**

```
// app/dashboard/page.tsx

import { redirect } from "next/navigation";

export default async function DashboardPage() {
  const isLoggedIn = false;

  if (!isLoggedIn) {
    redirect("/login");
  }

  return <h1>Welcome to Dashboard</h1>;
}
```

### **What happens?**

If `isLoggedIn` is false, Next.js redirects the user to `/login`.

The remaining rendering of that route segment stops.

### **Important points**

- `redirect()` can be used in Server Components.
- It can be used in Server Actions and Route Handlers.
- It throws a special internal control-flow signal.
- It should not be caught by a broad `try...catch` that suppresses the redirect.
- In Client Components, use the appropriate client-side navigation API such as `useRouter().push()` for event-driven navigation.

### **Example with a Server Action**

```
// app/login/actions.ts

"use server";

import { redirect } from "next/navigation";

export async function loginAction(formData: FormData) {
  // Validate credentials.
  // Create a secure session.

  redirect("/dashboard");
}
```

After a successful login, the user is redirected to the dashboard.

# **17. Understanding&#x20;**`permanentRedirect()`

Next.js also provides:

```
import { permanentRedirect } from "next/navigation";
```

It performs a permanent redirect.

### **Example**

```
import { permanentRedirect } from "next/navigation";

export default function OldProductPage() {
  permanentRedirect("/products/new-product");
}
```

This is useful when a resource has permanently moved to another URL.

### **Difference between&#x20;**`redirect()`**&#x20;and&#x20;**`permanentRedirect()`

| Feature                    | `redirect()`                 | `permanentRedirect()`    |
| -------------------------- | ---------------------------- | ------------------------ |
| Purpose                    | Temporary or normal redirect | Permanent redirect       |
| HTTP status                | Usually 307                  | Usually 308              |
| Permanent move             | No                           | Yes                      |
| Search engine implications | Not a permanent-move signal  | Signals a permanent move |
| Use case                   | Login redirect               | Permanently changed URL  |

### **Important note**

In Server Actions, redirect behavior may use a different HTTP status (commonly 303) because the action is part of a form submission flow.

# **18. Understanding the Difference Between&#x20;**`notFound()`**&#x20;and&#x20;**`redirect()`

Both stop the normal rendering flow, but they serve different purposes.

| Feature        | `notFound()`           | `redirect()`            |
| -------------- | ---------------------- | ----------------------- |
| Purpose        | Show not-found UI      | Navigate to another URL |
| Example        | Product does not exist | User must log in        |
| Typical result | 404 experience         | Redirect response       |
| Import         | `next/navigation`      | `next/navigation`       |

### **Example:&#x20;**`notFound()`

```
if (!product) {
  notFound();
}
```

### **Example:&#x20;**`redirect()`

```
if (!isAuthenticated) {
  redirect("/login");
}
```

# **19. Understanding&#x20;**`unauthorized()`**&#x20;and&#x20;**`forbidden()`

Next.js also provides HTTP access-control functions in supported versions of the framework.

They are:

```
import {
  unauthorized,
  forbidden,
} from "next/navigation";
```

These are distinct from redirects and not-found handling.

### `unauthorized()`

Used when the user is not authenticated.

Conceptually, this represents HTTP 401.

### `forbidden()`

Used when the user is authenticated but does not have permission to access a resource.

Conceptually, this represents HTTP 403.

### **Example**

```
// app/admin/page.tsx

import { forbidden, unauthorized } from "next/navigation";

export default async function AdminPage() {
  const user = await getCurrentUser();

  if (!user) {
    unauthorized();
  }

  if (user.role !== "admin") {
    forbidden();
  }

  return <h1>Admin Dashboard</h1>;
}
```

These functions require the corresponding Next.js experimental configuration in versions where they are still marked experimental. Check the documentation for your installed version before using them in production.

### **Comparison**

| Function         | Meaning                 | HTTP status |
| ---------------- | ----------------------- | ----------- |
| `notFound()`     | Resource not found      | 404         |
| `unauthorized()` | Authentication required | 401         |
| `forbidden()`    | Access denied           | 403         |
| `redirect()`     | Redirect to another URL | Usually 307 |

# **20. Real-World Example: Complete Product Error Handling**

Let's combine several concepts.

Imagine an e-commerce application that fetches products from MongoDB.

Requirements:

1. If the product exists, show its details.
2. If the product does not exist, show a 404 page.
3. If the database fails, show an error boundary.
4. Allow the user to retry.

### **Folder structure**

```
app/
└── products/
    ├── error.tsx
    ├── not-found.tsx
    └── [id]/
        └── page.tsx
```

### **Product fetching function**

```
// lib/products.ts

import { connectDB } from "./db";
import Product from "@/models/Product";

export async function getProduct(id: string) {
  await connectDB();

  return Product.findById(id)
    .select("name price description")
    .lean();
}
```

### **Product details page**

```
// app/products/[id]/page.tsx

import { notFound } from "next/navigation";
import { getProduct } from "@/lib/products";

export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  const product = await getProduct(id);

  if (!product) {
    notFound();
  }

  return (
    <div>
      <h1>{product.name}</h1>

      <p>{product.description}</p>

      <h2>₹{product.price}</h2>
    </div>
  );
}
```

### **Product error boundary**

```
// app/products/error.tsx

"use client";

export default function ProductError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h1>Unable to load product</h1>

      <p>
        Something went wrong while loading this product.
      </p>

      <button onClick={reset}>
        Retry
      </button>
    </div>
  );
}
```

### **Product not-found page**

```
// app/products/not-found.tsx

import Link from "next/link";

export default function ProductNotFound() {
  return (
    <div>
      <h1>Product Not Found</h1>

      <p>This product may have been removed.</p>

      <Link href="/products">
        Browse Products
      </Link>
    </div>
  );
}
```

### **How the application behaves**

| Scenario                             | Result                                              |
| ------------------------------------ | --------------------------------------------------- |
| Valid MongoDB ID and product exists  | Product details                                     |
| Valid MongoDB ID but product missing | Product not-found UI                                |
| Database connection fails            | Product error boundary                              |
| User clicks Retry                    | Next.js attempts to render the failed segment again |

**Production consideration:** A malformed MongoDB ObjectId may cause a database-library error. Validate its format before querying and decide whether an invalid ID should be treated as a not-found resource or a bad request.

# **21. Common Mistakes**

| Mistake                                                | Correct approach                                      |
| ------------------------------------------------------ | ----------------------------------------------------- |
| Forgetting `"use client"` in `error.tsx`               | Add the directive at the top                          |
| Using `error.tsx` for missing resources                | Use `notFound()`                                      |
| Assuming `error.tsx` catches errors in its own layout  | Use the parent boundary                               |
| Forgetting `<html>` and `<body>` in `global-error.tsx` | Include both tags                                     |
| Showing database details to users                      | Log securely and show a generic message               |
| Catching and suppressing `notFound()`                  | Let the Next.js control-flow signal propagate         |
| Catching and suppressing `redirect()`                  | Keep redirects outside broad catch blocks             |
| Using error boundaries for ordinary form validation    | Return validation state                               |
| Assuming `reset()` fixes the underlying error          | It only retries rendering                             |
| Using redirects as the only authorization mechanism    | Verify authentication and authorization on the server |

# **22. Interview Questions and Answers**

## **Basic Level**

**Q1. What is error handling in Next.js?**

Error handling is the process of detecting and managing application failures and displaying appropriate fallback UI.

**Q2. What is&#x20;**`error.tsx`**?**

It is a special Next.js file convention that creates a route-segment error boundary.

**Q3. Why must&#x20;**`error.tsx`**&#x20;be a Client Component?**

Because Next.js error boundaries use client-side React error-boundary behavior and interactive recovery actions.

**Q4. What is the purpose of&#x20;**`reset()`**?**

It attempts to re-render the failed route segment so the application can recover.

**Q5. What is&#x20;**`not-found.tsx`**?**

It is a special file used to display a custom not-found UI.

## **Intermediate Level**

**Q6. What is the difference between&#x20;**`error.tsx`**&#x20;and&#x20;**`global-error.tsx`**?**

`error.tsx` handles errors within a route segment, while `global-error.tsx` handles root-level errors, including errors in the root layout.

**Q7. What is the purpose of&#x20;**`notFound()`**?**

It terminates rendering of the current route segment and displays the relevant not-found UI.

**Q8. Can we create nested error boundaries?**

Yes. You can create `error.tsx` files at different route levels.

**Q9. Can an error boundary catch errors thrown by event handlers?**

No. React error boundaries do not catch errors thrown in event handlers. Handle those errors with appropriate `try...catch` logic.

**Q10. Can&#x20;**`error.tsx`**&#x20;catch errors from Server Components?**

Yes. Errors thrown during rendering of Server Components can be caught by the nearest applicable error boundary.

## **Advanced Level**

**Q11. Why can't a segment's&#x20;**`error.tsx`**&#x20;catch errors from its own layout?**

Because the segment's error boundary is placed inside that layout. A parent error boundary must handle errors from the layout itself.

**Q12. What is the difference between&#x20;**`notFound()`**&#x20;and&#x20;**`redirect()`**?**

`notFound()` displays not-found UI, whereas `redirect()` sends the user to another URL.

**Q13. What is the difference between&#x20;**`redirect()`**&#x20;and&#x20;**`permanentRedirect()`**?**

`redirect()` generally uses a temporary redirect status, while `permanentRedirect()` uses a permanent redirect status.

**Q14. How should we handle expected errors in production?**

Return structured error states or appropriate responses instead of treating normal business conditions as unexpected exceptions.

**Q15. Why should we avoid displaying raw server errors to users?**

Raw server errors may expose sensitive implementation details, database information, or internal infrastructure.

# **23. Practical Assignment**



Hands-on project

**Build a Robust E-commerce Error-Handling System**



Create an e-commerce application with product routes, authentication, and an admin section.

[Storekit - Multipurpose eCommerce Website Figma Template by HexNova](https://images.openai.com/static-rsc-4/RLhUwVBTnAAEZWjja07QaLEav5uEY1TK3a9vd12N5mX9pmPkcpSjTlgCKoFz6_8r0Jzp_YUaKa0FULTm36SA3OggSV0ASzljD3R5TTLntdCRP9XR1NRuGh3qu3lP2BXSuHHeTZOx3_LG3XZP2qjmLkZS3Rf-JbqQYBCm4WE6PMo?purpose=inline)

[themeforest.net](https://themeforest.net/item/storekit-multipurpose-ecommerce-website-figma-template/61809176?utm_source=chatgpt.com)

### **Task 1: Create this folder structure**

```
app/
├── layout.tsx
├── global-error.tsx
├── error.tsx
├── not-found.tsx
│
├── products/
│   ├── error.tsx
│   ├── not-found.tsx
│   ├── page.tsx
│   └── [id]/
│       └── page.tsx
│
├── login/
│   ├── page.tsx
│   └── actions.ts
│
└── dashboard/
    ├── error.tsx
    ├── page.tsx
    └── admin/
        └── page.tsx
```

### **Task 2: Create product handling**

Implement:

- Fetch product by ID.
- Show product details if found.
- Call `notFound()` if the product does not exist.
- Validate MongoDB ObjectIds before querying.

### **Task 3: Create error boundaries**

Implement:

- Root `error.tsx`.
- Products `error.tsx`.
- Dashboard `error.tsx`.
- `global-error.tsx`.

Each should have a useful error message and a retry button.

### **Task 4: Create custom 404 pages**

Implement:

- Global 404 page.
- Product-specific not-found page.

### **Task 5: Implement authentication redirects**

- If a user is not authenticated, redirect to `/login`.
- If login succeeds, redirect to `/dashboard`.
- If login fails, show a form-level validation message.

### **Task 6: Test failure scenarios**

| Test                                        | Expected result                           |
| ------------------------------------------- | ----------------------------------------- |
| Open existing product                       | Product details                           |
| Open missing product                        | 404 page                                  |
| Open malformed product ID                   | Appropriate invalid-ID/not-found handling |
| Simulate database failure                   | Error UI                                  |
| Click Retry                                 | Attempt to render again                   |
| Submit invalid login                        | Form-level error                          |
| Visit protected dashboard without a session | Redirect to login                         |

### **Task 7: Security improvements**

- Never expose database credentials.
- Never expose raw stack traces.
- Validate user input on the server.
- Verify authorization on the server.
- Log unexpected errors securely.

# **24. Quick Revision Notes**

| Concept               | Key point                                         |
| --------------------- | ------------------------------------------------- |
| `error.tsx`           | Route-segment error boundary                      |
| `global-error.tsx`    | Root-level error boundary                         |
| `reset()`             | Attempts to re-render a failed segment            |
| `error` prop          | Contains information about the caught error       |
| `not-found.tsx`       | Custom not-found UI                               |
| `notFound()`          | Triggers not-found handling                       |
| `redirect()`          | Redirects to another URL                          |
| `permanentRedirect()` | Permanently redirects                             |
| Expected errors       | Handle explicitly as application state            |
| Unexpected errors     | Throw and let an appropriate boundary handle them |
| `unauthorized()`      | Represents authentication-required handling       |
| `forbidden()`         | Represents access-denied handling                 |

# **25. Phase 8 Completion Checklist**

**Your progress**

# **0%**

0 of 18 completed

Understand error handling and its purpose

Understand different types of application errors

Create error.tsx

Understand the error prop

Understand reset()

Understand nested error boundaries

Create global-error.tsx

Understand Server Component error handling

Differentiate expected and unexpected errors

Handle expected errors using Server Actions

Create not-found.tsx

Use notFound() in dynamic routes

Understand nested not-found boundaries

Understand redirect()

Understand permanentRedirect()

Understand unauthorized() and forbidden()

Differentiate error.tsx and not-found.tsx

Complete the e-commerce error-handling assignment

# **26. What's Next? – Phase 9**

Next phase

# **Server Components and Client Components – Deep Dive**

**Topics we will cover:**

1. What are React Server Components?
2. What are Client Components?
3. Server Components vs Client Components.
4. Understanding the `"use client"` directive.
5. Component rendering and execution environments.
6. Server-to-client component composition.
7. Client-to-server component composition.
8. Passing props between Server and Client Components.
9. Serialization and serializable props.
10. Hydration and client-side JavaScript.
11. Server Component limitations.
12. Common architecture patterns.
13. Performance and security best practices.

When you're ready, say **"OK, Phase 9"** and we'll continue with detailed notes, code examples, and practical exercises.