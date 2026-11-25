# **Phase 13 – Proxy (Formerly Middleware) and Request Interception**



Phase 13

Intermediate to Advanced

Next.js 16.x



## **1. Learning objectives**

In this phase, you will learn how to:

1. Understand request interception in Next.js.
2. Understand the difference between Middleware and Proxy.
3. Configure the `proxy.ts` file.
4. Understand how Proxy executes in the request lifecycle.
5. Protect dashboard routes.
6. Redirect unauthenticated users to a login page.
7. Use the `matcher` configuration.
8. Read and modify request and response headers.
9. Read and modify cookies.
10. Use `NextRequest` and `NextResponse`.
11. Implement URL redirects and rewrites.
12. Understand authentication and authorization limitations.
13. Build a protected dashboard using Proxy.

## **2. What is Middleware?**

Before understanding Proxy, let's understand Middleware.

In web development, **Middleware** is code that executes between an incoming request and the final route handler or page.

It can inspect a request and decide what should happen before the application processes it.

For example, imagine a user visits:

```
/dashboard
```

You want to check whether the user is logged in.

- If logged in, allow the request to continue.
- If not logged in, redirect the user to `/login`.

You could perform this check using request interception.

### **Traditional request flow without Middleware**

**Incoming HTTP Request**

GET /dashboard

**Next.js Route**

**Dashboard Page**

**HTTP Response**

### **Request flow with Middleware / Proxy**

**Incoming HTTP Request**

**Proxy – Request Interception**

Check cookies, headers, URL and other request information

**Allowed**

Continue to route

**Not allowed**

Redirect or respond

**Next.js Route / Page**

The important idea is that request interception lets you run logic before the requested route is completed.

## **3. Middleware vs Proxy in Next.js 16**

This is an important version-specific change.

In older Next.js versions, the convention was:

```
middleware.ts
```

Starting with **Next.js 16**, Middleware was renamed to **Proxy**.

The new convention is:

```
proxy.ts
```

The purpose remains similar, but the new name better communicates its role as a network request boundary.

### **Old vs new convention**

| Earlier versions        | Next.js 16         |
| ----------------------- | ------------------ |
| `middleware.ts`         | `proxy.ts`         |
| `middleware()` function | `proxy()` function |
| Middleware terminology  | Proxy terminology  |
| Edge Runtime convention | Node.js runtime    |

### **Old example**

```
// middleware.ts

import { NextResponse } from "next/server";

export function middleware() {
  return NextResponse.next();
}
```

### **Next.js 16 example**

```
// proxy.ts

import { NextResponse } from "next/server";

export function proxy() {
  return NextResponse.next();
}
```

The new `proxy.ts` convention uses the Node.js runtime. Do not try to configure its runtime using the old Middleware runtime configuration.

**Important:** If you are working with older tutorials, you may still see `middleware.ts`. For a new Next.js 16 project, learn and use `proxy.ts`.

Official documentation: [Next.js – Proxy file convention](https://nextjs.org/docs/app/api-reference/file-conventions/proxy?utm_source=chatgpt.com).

## **4. What is the&#x20;**`proxy.ts`**&#x20;file?**

Next.js uses a special file convention called `proxy.ts` to intercept incoming requests.

### **Where should you create it?**

If your project uses the `src` directory:

```
my-next-app/
│
├── src/
│   ├── app/
│   │   ├── page.tsx
│   │   ├── layout.tsx
│   │   └── dashboard/
│   │       └── page.tsx
│   │
│   └── proxy.ts
│
├── public/
├── package.json
└── next.config.ts
```

Without the `src` directory:

```
my-next-app/
│
├── app/
│   ├── page.tsx
│   └── dashboard/
│       └── page.tsx
│
├── proxy.ts
├── package.json
└── next.config.ts
```

**Important:** `proxy.ts` must be at the same level as `app` or `pages`, not inside `app`.

### **Basic Proxy example**

`src/proxy.ts`

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  console.log("Incoming request:", request.nextUrl.pathname);

  return NextResponse.next();
}
```

Here:

- `proxy()` is the request interception function.
- `request` represents the incoming request.
- `NextResponse.next()` allows the request to continue to its destination.

The function can also be asynchronous:

```
export async function proxy(request: NextRequest) {
  // Perform asynchronous operations if needed.

  return NextResponse.next();
}
```

Next.js supports either a named `proxy` export or a default export, but only one Proxy function per file.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **5. Understanding&#x20;**`NextRequest`**&#x20;and&#x20;**`NextResponse`

Proxy uses two important Next.js APIs.

### **A.&#x20;**`NextRequest`

`NextRequest` represents the incoming HTTP request.

You can access:

| Property                       | Purpose                     |
| ------------------------------ | --------------------------- |
| `request.url`                  | Complete request URL        |
| `request.nextUrl`              | Next.js extended URL object |
| `request.nextUrl.pathname`     | Current pathname            |
| `request.nextUrl.searchParams` | Query parameters            |
| `request.method`               | HTTP method                 |
| `request.headers`              | Incoming request headers    |
| `request.cookies`              | Incoming request cookies    |

Example:

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const pathname = request.nextUrl.pathname;
  const method = request.method;
  const userAgent = request.headers.get("user-agent");

  console.log({
    pathname,
    method,
    userAgent,
  });

  return NextResponse.next();
}
```

For a request:

```
https://example.com/dashboard?tab=profile
```

You can access:

```
request.nextUrl.pathname;
// "/dashboard"

request.nextUrl.searchParams.get("tab");
// "profile"
```

### **B.&#x20;**`NextResponse`

`NextResponse` is used to control what happens after inspecting the request.

Its most important methods are:

| Method                    | Purpose                                            |
| ------------------------- | -------------------------------------------------- |
| `NextResponse.next()`     | Continue to the requested route                    |
| `NextResponse.redirect()` | Redirect to another URL                            |
| `NextResponse.rewrite()`  | Serve another route while keeping the original URL |
| `NextResponse.json()`     | Return a JSON response                             |

Example:

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  return NextResponse.next();
}
```

Official reference: [NextResponse API](https://nextjs.org/docs/app/api-reference/functions/next-response?utm_source=chatgpt.com).&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **6. Understanding&#x20;**`NextResponse.next()`

This method allows the request to continue through the normal Next.js routing process.

Example:

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const pathname = request.nextUrl.pathname;

  if (pathname === "/about") {
    console.log("User requested About page");
  }

  return NextResponse.next();
}
```

Request:

```
/about
```

Execution:

1. The user requests `/about`.
2. Proxy executes.
3. Proxy checks the pathname.
4. `NextResponse.next()` allows the request to continue.
5. Next.js renders the About page.

Use `NextResponse.next()` when you want to inspect or modify a request without blocking it.

## **7. Understanding&#x20;**`NextResponse.redirect()`

Redirect means sending the user to a different URL.

For example:

```
/dashboard → /login
```

This is useful when an unauthenticated user tries to access a protected page.

### **Basic example**

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  return NextResponse.redirect(
    new URL("/login", request.url)
  );
}
```

Every matched request will be redirected to `/login`.

### **Conditional redirect**

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const pathname = request.nextUrl.pathname;

  if (pathname === "/dashboard") {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  return NextResponse.next();
}
```

Now only `/dashboard` is redirected.

### **Preserve the original destination**

A better login experience is to remember which page the user originally wanted to visit.

For example:

```
/dashboard/settings
```

After login, the user should return to:

```
/dashboard/settings
```

You can add a query parameter:

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const loginUrl = new URL("/login", request.url);

  loginUrl.searchParams.set(
    "from",
    request.nextUrl.pathname
  );

  return NextResponse.redirect(loginUrl);
}
```

Result:

```
/login?from=%2Fdashboard%2Fsettings
```

The login flow can later use this destination after validating it as a safe internal path.

**Security note:** Never blindly redirect to an arbitrary URL supplied by a query parameter. Validate redirect destinations to prevent open-redirect vulnerabilities.

## **8. Understanding&#x20;**`NextResponse.rewrite()`

A rewrite serves a different route while preserving the URL visible in the browser.

This is different from a redirect.

### **Example**

Suppose the user visits:

```
/about
```

But you want Next.js to internally serve:

```
/company
```

You can write:

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  if (request.nextUrl.pathname === "/about") {
    return NextResponse.rewrite(
      new URL("/company", request.url)
    );
  }

  return NextResponse.next();
}
```

The browser still displays:

```
/about
```

But the content is served from:

```
/company
```

### **Redirect vs Rewrite**

**Redirect**

`/about`

`/company`

Browser URL changes to /company.

**Rewrite**

`/about`

`/company`

Browser URL remains /about, but /company provides the content.

| Feature                   | Redirect                           | Rewrite                       |
| ------------------------- | ---------------------------------- | ----------------------------- |
| Browser URL changes       | Yes                                | No                            |
| Destination route changes | Yes                                | Yes, internally               |
| User sees original URL    | No                                 | Yes                           |
| Common use                | Login redirects, old URL migration | URL masking, internal routing |

## **9. Understanding the&#x20;**`matcher`**&#x20;configuration**

By default, Proxy can run on every request.

But you usually don't want to run authentication logic for every single asset or route.

For example, your application might contain:

```
/
 /login
 /signup
 /dashboard
 /dashboard/profile
 /dashboard/settings
 /products
 /api/products
 /_next/static/...
 /favicon.ico
```

You might only want Proxy to execute for dashboard routes.

The `matcher` configuration allows you to choose which paths should be intercepted.

### **A. Match one route**

```
export const config = {
  matcher: "/dashboard",
};
```

This targets `/dashboard`.

### **B. Match multiple routes**

```
export const config = {
  matcher: [
    "/dashboard",
    "/profile",
    "/settings",
  ],
};
```

### **C. Match nested routes**

```
export const config = {
  matcher: "/dashboard/:path*",
};
```

This matches:

```
/dashboard
/dashboard/profile
/dashboard/settings
/dashboard/settings/security
```

### **D. Match dashboard and admin sections**

```
export const config = {
  matcher: [
    "/dashboard/:path*",
    "/admin/:path*",
  ],
};
```

### **Understanding&#x20;**`:path*`

| Pattern             | Meaning                          |
| ------------------- | -------------------------------- |
| `/dashboard`        | Dashboard path                   |
| `/dashboard/:path`  | One additional segment           |
| `/dashboard/:path*` | Zero or more additional segments |
| `/dashboard/:path+` | One or more additional segments  |
| `/dashboard/:path?` | Zero or one additional segment   |

### **E. Exclude static assets**

A common pattern is:

```
export const config = {
  matcher: [
    "/((?!api|_next/static|_next/image|.*\\.png$).*)",
  ],
};
```

This excludes API paths, Next.js static assets, image optimization paths and `.png` files from this matcher.

You can customize the exclusions for your application.

### **Important matcher rules**

1. Matcher paths must begin with `/`.
2. Matcher values must be constants.
3. Do not generate matchers dynamically from environment variables.
4. A matcher can be a string or an array.
5. Matcher patterns support path parameters and regular expressions.

For example, this is not appropriate:

```
const protectedPath = "/dashboard";

export const config = {
  matcher: protectedPath,
};
```

The matcher should be written as a statically analyzable literal:

```
export const config = {
  matcher: "/dashboard",
};
```

Official reference: [Proxy matcher documentation](https://nextjs.org/docs/app/api-reference/file-conventions/proxy?utm_source=chatgpt.com).&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **10. Understanding the Next.js request execution order**

Proxy is executed before the requested route is rendered, but it is not literally the first possible routing configuration.

The documented execution order is:

1. **Headers from&#x20;**`next.config.ts`
2. **Redirects from&#x20;**`next.config.ts`
3. **Proxy (**`proxy.ts`**)**

   Request interception, rewrites and redirects
4. `beforeFiles`**&#x20;rewrites**
5. **Filesystem routes**

   Public files, app routes, pages, etc.
6. `afterFiles`**&#x20;rewrites**
7. **Dynamic routes**
8. **Fallback rewrites**

Why is this important?

Because it helps you understand when Proxy executes relative to route resolution and URL rewrites.

Another important detail: Server Functions are handled as POST requests to the route where they are used, rather than as independent routes in this execution chain. If your matcher excludes a path, it can also exclude Server Function calls associated with that path. Therefore, do not rely on Proxy as the only protection for Server Actions.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **11. Reading cookies inside Proxy**

Cookies are frequently used to maintain authentication sessions.

For example, after login, your server may set a session cookie:

```
session=encrypted-session-value
```

When the browser sends another request, it includes the applicable cookie.

Proxy can inspect incoming cookies through `request.cookies`.

### **Read a cookie**

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const session = request.cookies.get("session");

  console.log(session?.value);

  return NextResponse.next();
}
```

### **Check whether a cookie exists**

```
export function proxy(request: NextRequest) {
  const hasSession = request.cookies.has("session");

  console.log(hasSession);

  return NextResponse.next();
}
```

### **Get all cookies**

```
export function proxy(request: NextRequest) {
  const cookies = request.cookies.getAll();

  console.log(cookies);

  return NextResponse.next();
}
```

### **Set a response cookie**

You can also modify cookies sent back to the browser.

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const response = NextResponse.next();

  response.cookies.set("visited", "true", {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "lax",
    path: "/",
  });

  return response;
}
```

### **Delete a response cookie**

```
export function proxy(request: NextRequest) {
  const response = NextResponse.next();

  response.cookies.delete("visited");

  return response;
}
```

**Important:** Reading a cookie does not prove that the user is authenticated. A user can send a fabricated cookie value. You must verify its signature or decrypt and validate the session.

Also, Proxy is not a replacement for your session-management system.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **12. Reading and modifying HTTP headers**

HTTP headers contain additional information about a request or response.

Examples:

```
Content-Type: application/json
Authorization: Bearer token
User-Agent: Chrome
Accept-Language: en-US
```

Proxy can read incoming request headers and attach headers to the outgoing response.

### **A. Read request headers**

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const userAgent = request.headers.get("user-agent");

  const authorization = request.headers.get("authorization");

  console.log({
    userAgent,
    authorization,
  });

  return NextResponse.next();
}
```

### **B. Set response headers**

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const response = NextResponse.next();

  response.headers.set("X-App-Version", "1.0");

  return response;
}
```

This adds a header to the HTTP response sent to the client.

### **C. Modify request headers forwarded to the destination**

Sometimes you want to pass additional information to the page or Route Handler.

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const requestHeaders = new Headers(request.headers);

  requestHeaders.set("x-current-path", request.nextUrl.pathname);

  return NextResponse.next({
    request: {
      headers: requestHeaders,
    },
  });
}
```

The destination route can then read the forwarded header:

```
import { headers } from "next/headers";

export default async function DashboardPage() {
  const headerStore = await headers();

  const pathname = headerStore.get("x-current-path");

  return <h1>Current path: {pathname}</h1>;
}
```

In Next.js 16, `headers()` is asynchronous.

### **Important distinction**

| Code                                          | What it modifies                             |
| --------------------------------------------- | -------------------------------------------- |
| `response.headers.set(...)`                   | Response headers sent to the browser         |
| `NextResponse.next({ request: { headers } })` | Request headers forwarded to the destination |
| `request.headers.get(...)`                    | Reads incoming request headers               |

**Security warning:** Do not blindly forward all incoming headers. Headers can contain cookies, authorization credentials and other sensitive information. Forward only explicitly required, trusted headers.

Also, do not confuse:

```
NextResponse.next({ headers })
```

with:

```
NextResponse.next({
  request: { headers },
})
```

The first sends headers to the client and is generally discouraged. The second forwards request headers upstream to the destination.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **13. Authentication using Proxy**

Now let's implement a practical authentication example.

Imagine your application has these pages:

```
/
 /login
 /signup
 /dashboard
 /dashboard/profile
 /dashboard/settings
```

Your requirements:

- Anyone can access `/`.
- Anyone can access `/login`.
- Anyone can access `/signup`.
- Only authenticated users can access `/dashboard`.
- Unauthenticated users should be redirected to `/login`.

### **Authentication flow**

**User requests /dashboard**

**Proxy reads session cookie**

**Valid session**

Continue to dashboard

**No valid session**

Redirect to login

### **Step 1: Understand the session cookie**

After a successful login, your authentication system should create a secure session and store it in a cookie.

For example:

```
session=encrypted-session-token
```

The cookie should ideally use:

```
{
  httpOnly: true,
  secure: true,
  sameSite: "lax",
  path: "/"
}
```

The session token should be signed or encrypted using a secure server-side secret.

Do not use a plain cookie such as:

```
loggedIn=true
```

That value can be fabricated by the client.

### **Step 2: Create a session verification function**

For illustration, assume you already have a secure `decrypt()` function that verifies and decodes your signed or encrypted session.

`src/lib/session.ts`

```
import "server-only";

// Illustrative function signature.
// Implement this using a trusted session/JWT library.

export async function decrypt(token?: string) {
  if (!token) {
    return null;
  }

  // Verify signature or decrypt the token.
  // Check expiry and other required claims.

  return null;
}
```

This is a placeholder, not a working authentication implementation. In a real application, implement the verification with a trusted authentication library or a properly configured cryptographic session system.

### **Step 3: Create&#x20;**`proxy.ts`

`src/proxy.ts`

```
import { NextRequest, NextResponse } from "next/server";
import { decrypt } from "@/lib/session";

export async function proxy(request: NextRequest) {
  const pathname = request.nextUrl.pathname;

  const sessionToken = request.cookies.get("session")?.value;

  const session = await decrypt(sessionToken);

  const isAuthenticated = Boolean(session?.userId);

  const isDashboardRoute =
    pathname === "/dashboard" ||
    pathname.startsWith("/dashboard/");

  if (isDashboardRoute && !isAuthenticated) {
    const loginUrl = new URL("/login", request.url);

    loginUrl.searchParams.set("from", pathname);

    return NextResponse.redirect(loginUrl);
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*"],
};
```

### **Step 4: Test the behavior**

| User state    | Requested URL         | Result            |
| ------------- | --------------------- | ----------------- |
| Not logged in | `/`                   | Home page         |
| Not logged in | `/login`              | Login page        |
| Not logged in | `/dashboard`          | Redirect to login |
| Not logged in | `/dashboard/settings` | Redirect to login |
| Logged in     | `/dashboard`          | Dashboard         |
| Logged in     | `/dashboard/profile`  | Profile page      |

The login page is not matched by this Proxy configuration, so the redirect does not create a loop.

### **Important security limitation**

This is an **optimistic authentication check**.

It is useful for quickly redirecting users based on a verified session cookie.

However, Proxy should not be your only security layer. A sensitive database operation must independently verify the user's session and permissions near the data source.

For example, a user might be able to access a dashboard page but still not have permission to delete a particular record.

The official Next.js authentication guide recommends using Proxy for optional optimistic checks and a Data Access Layer (DAL) for secure checks.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **14. Protecting admin routes using roles**

Suppose your application has two user roles:

```
type UserRole = "user" | "admin";
```

Requirements:

- Normal users can access `/dashboard`.
- Administrators can access `/admin`.
- Normal users must not access `/admin`.

Your session might contain:

```
{
  userId: "123",
  role: "admin"
}
```

After verifying the session token, you can perform an optimistic role check.

Example:

```
import { NextRequest, NextResponse } from "next/server";
import { decrypt } from "@/lib/session";

export async function proxy(request: NextRequest) {
  const pathname = request.nextUrl.pathname;

  const token = request.cookies.get("session")?.value;

  const session = await decrypt(token);

  const isAuthenticated = Boolean(session?.userId);

  const isAdminRoute =
    pathname === "/admin" ||
    pathname.startsWith("/admin/");

  if (!isAuthenticated) {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  if (isAdminRoute && session?.role !== "admin") {
    return NextResponse.redirect(
      new URL("/unauthorized", request.url)
    );
  }

  return NextResponse.next();
}

export const config = {
  matcher: [
    "/dashboard/:path*",
    "/admin/:path*",
  ],
};
```

This is a convenience check, not definitive authorization.

**For sensitive admin operations, verify the user's current role against trusted server-side data.** A role claim in a token may be outdated if the user's permissions have changed.

For example, your delete action should independently check the user's role before deleting anything.

### **Authentication vs role-based authorization**

| Concept                          | Example                                            |
| -------------------------------- | -------------------------------------------------- |
| Authentication                   | Is the user logged in?                             |
| Authorization                    | Is the user allowed to access admin functionality? |
| Role-based access control (RBAC) | Admin, user, manager, editor                       |
| Resource ownership               | Can this user edit this particular record?         |

## **15. Proxy vs Data Access Layer (DAL)**

This is a critical architectural concept.

A common mistake is to put all authentication and authorization logic inside `proxy.ts`.

That is not recommended.

### **What is a Data Access Layer?**

A Data Access Layer is a server-side module that centralizes data retrieval and security checks.

It sits between your application and your database.

Example architecture:

**Server Components**

**Server Actions**

**Data Access Layer (DAL)**

Session verification, authorization and data access

**Database**

MongoDB / MySQL

**Proxy – Optional early request check**

### **Example DAL**

`src/lib/dal.ts`

```
import "server-only";

import { cache } from "react";
import { cookies } from "next/headers";
import { redirect } from "next/navigation";
import { decrypt } from "@/lib/session";

export const verifySession = cache(async () => {
  const cookieStore = await cookies();

  const token = cookieStore.get("session")?.value;

  const session = await decrypt(token);

  if (!session?.userId) {
    redirect("/login");
  }

  return {
    userId: session.userId,
    role: session.role,
  };
});
```

This example assumes `decrypt()` performs real cryptographic verification and checks session expiry.

### **Use DAL inside a Server Component**

```
import { verifySession } from "@/lib/dal";

export default async function DashboardPage() {
  const session = await verifySession();

  return (
    <main>
      <h1>Dashboard</h1>

      <p>User ID: {session.userId}</p>
    </main>
  );
}
```

### **Use DAL inside a Server Action**

```
"use server";

import { verifySession } from "@/lib/dal";

export async function deleteProduct(productId: string) {
  const session = await verifySession();

  if (session.role !== "admin") {
    return {
      success: false,
      message: "Permission denied",
    };
  }

  // Validate ID and delete from database.
}
```

### **Why is DAL important?**

- Centralizes authentication logic.
- Reduces duplicated session checks.
- Makes data access easier to secure.
- Helps enforce resource-level authorization.
- Can return only the necessary data through DTOs.

**Remember:** Proxy can help with early redirects, but secure authorization should happen inside the DAL, Server Actions and Route Handlers where sensitive data is accessed.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **16. Proxy vs Middleware vs Route Handlers vs Server Actions**

These concepts are related but serve different purposes.

| Feature                | Proxy                         | Route Handler           | Server Action                  |
| ---------------------- | ----------------------------- | ----------------------- | ------------------------------ |
| Main purpose           | Intercept requests            | Handle HTTP endpoints   | Perform server-side actions    |
| File                   | `proxy.ts`                    | `route.ts`              | `actions.ts`                   |
| HTTP methods           | Intercepts matching requests  | Explicit method exports | Invoked through POST mechanism |
| Redirect requests      | Yes                           | Can return redirects    | Can call `redirect()`          |
| Modify request headers | Yes                           | Normal request handling | Not its primary purpose        |
| Create REST APIs       | No                            | Yes                     | Not intended as general API    |
| Database mutations     | Not recommended               | Yes                     | Yes                            |
| Typical use            | Redirects, lightweight checks | REST APIs, webhooks     | Forms, CRUD operations         |

### **Which one should you use?**

- **Proxy:** To intercept requests and perform lightweight checks or redirects.
- **Route Handler:** To create explicit HTTP APIs.
- **Server Action:** To perform server-side mutations from your React UI.
- **DAL:** To centralize secure data access and authorization.

## **17. Logging and background work with&#x20;**`NextFetchEvent`

Proxy can also receive a second argument called `NextFetchEvent`.

It provides a method named `waitUntil()`.

This is useful when you want to perform lightweight background work, such as sending analytics or logging information.

Example:

```
import type {
  NextFetchEvent,
  NextRequest,
} from "next/server";

import { NextResponse } from "next/server";

export function proxy(
  request: NextRequest,
  event: NextFetchEvent
) {
  event.waitUntil(
    fetch("https://example.com/log", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        path: request.nextUrl.pathname,
      }),
    })
  );

  return NextResponse.next();
}
```

The request can continue while the background promise is allowed to finish.

Use this for lightweight analytics or logging, not long-running business operations.

The official API also documents Proxy's default Node.js runtime. You cannot set a separate `runtime` option in `proxy.ts`.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



# **18. Practical assignment – Build a Protected Dashboard**

Main assignment

Now build a protected dashboard using Next.js 16, TypeScript and Proxy.

### **Project requirements**

Create these pages:

```
src/
├── app/
│   ├── page.tsx
│   ├── login/
│   │   └── page.tsx
│   ├── unauthorized/
│   │   └── page.tsx
│   ├── dashboard/
│   │   ├── page.tsx
│   │   ├── profile/
│   │   │   └── page.tsx
│   │   └── settings/
│   │       └── page.tsx
│   └── admin/
│       └── page.tsx
│
├── lib/
│   ├── session.ts
│   └── dal.ts
│
└── proxy.ts
```

### **Task 1 – Create public pages**

Create:

- Home page.
- Login page.
- Unauthorized page.

These pages should be accessible without authentication.

### **Task 2 – Create protected pages**

Create:

- Dashboard.
- Dashboard profile.
- Dashboard settings.
- Admin dashboard.

### **Task 3 – Configure Proxy**

Implement:

- Read the session cookie.
- Verify the session token.
- Redirect unauthenticated users to `/login`.
- Preserve the original destination.
- Allow authenticated users to access dashboard routes.

### **Task 4 – Implement role-based access**

Create two roles:

```
type Role = "user" | "admin";
```

Requirements:

- Normal users can access the dashboard.
- Admins can access the dashboard and admin area.
- Normal users attempting to access `/admin` are redirected to `/unauthorized`.

### **Task 5 – Implement a DAL**

Create:

- `verifySession()`
- `verifyAdmin()`
- `getCurrentUser()`

Use these functions inside Server Components and sensitive Server Actions.

### **Task 6 – Security testing**

Test these cases:

| Test                                       | Expected result   |
| ------------------------------------------ | ----------------- |
| Visit `/dashboard` without login           | Redirect to login |
| Visit `/dashboard/profile` without login   | Redirect to login |
| Visit `/login` without login               | Allow             |
| Visit `/dashboard` as normal user          | Allow             |
| Visit `/admin` as normal user              | Deny              |
| Visit `/admin` as admin                    | Allow             |
| Call protected Server Action without login | Reject            |
| Call admin action as normal user           | Reject            |
| Use an invalid or expired session          | Reject            |

### **Assignment progress tracker**

**Assignment progress**

# **0%**

0 of 14 tasks completed

Create the public pages

Create dashboard and admin pages

Create the session verification function

Configure proxy.ts

Add dashboard route matching

Implement unauthenticated redirects

Preserve original destination in login URL

Implement user and admin roles

Implement the DAL

Protect Server Actions independently

Test invalid and expired sessions

Test unauthorized admin access

Verify static assets still load

Test the application in production mode

Reset checklist

# **19. Common mistakes in Proxy**

| Mistake                                                | Correct approach                                     |
| ------------------------------------------------------ | ---------------------------------------------------- |
| Creating `middleware.ts` in a new Next.js 16 project   | Use `proxy.ts`                                       |
| Putting `proxy.ts` inside `app`                        | Put it alongside `app` or `pages`                    |
| Exporting multiple Proxy functions                     | Export only one Proxy function                       |
| Forgetting to return `NextResponse.next()`             | Explicitly continue when no interception is required |
| Matching every static asset unnecessarily              | Configure an appropriate matcher                     |
| Redirecting login requests back to login               | Exclude login from the protected matcher             |
| Checking only whether a cookie exists                  | Verify the session cryptographically                 |
| Performing database queries for every request in Proxy | Use lightweight session checks                       |
| Using Proxy as the only authorization layer            | Verify permissions in the DAL and at the data source |
| Trusting a role directly from an unverified cookie     | Verify the session and authorization                 |
| Forwarding every incoming header                       | Forward only necessary, trusted headers              |
| Assuming Proxy replaces Server Actions security        | Secure every Server Action independently             |
| Setting `runtime` in `proxy.ts`                        | Use the default Node.js runtime                      |

# **20. Interview questions and answers**

## **Basic level**

**Q1. What is Proxy in Next.js?**

Proxy allows us to execute server-side logic before a request is completed. It can inspect requests, redirect, rewrite URLs and modify headers or cookies.

**Q2. What is the old Middleware filename?**

`middleware.ts`.

**Q3. What is the new filename in Next.js 16?**

`proxy.ts`.

**Q4. Where should we create&#x20;**`proxy.ts`**?**

At the project root or inside `src`, alongside the `app` or `pages` directory.

**Q5. What is&#x20;**`NextRequest`**?**

It represents the incoming HTTP request and provides Next.js-specific request properties.

**Q6. What is&#x20;**`NextResponse`**?**

It extends the standard Web Response API with methods for redirects, rewrites, cookies and request handling.

**Q7. What is the purpose of&#x20;**`NextResponse.next()`**?**

It allows the request to continue through Next.js routing.

**Q8. What is the purpose of&#x20;**`NextResponse.redirect()`**?**

It redirects the user to another URL.

## **Intermediate level**

**Q9. What is the difference between redirect and rewrite?**

Redirect changes the browser URL. Rewrite serves another route while preserving the original browser URL.

**Q10. What is&#x20;**`matcher`**?**

It specifies which request paths should execute Proxy.

**Q11. How do you protect nested dashboard routes?**

Use:

```
export const config = {
  matcher: "/dashboard/:path*",
};
```

**Q12. How do you access cookies inside Proxy?**

Using:

```
request.cookies.get("session")?.value;
```

**Q13. How do you modify response headers?**

Using:

```
const response = NextResponse.next();

response.headers.set("X-Version", "1.0");

return response;
```

**Q14. Can Proxy access query parameters?**

Yes, through:

```
request.nextUrl.searchParams;
```

## **Advanced level**

**Q15. What is the difference between authentication and authorization?**

Authentication verifies identity, while authorization determines which resources and operations the authenticated user is permitted to access.

**Q16. Should we perform database queries inside Proxy for authentication?**

Generally, avoid database checks in Proxy. Use lightweight, optimistic session checks there and perform secure verification near the data source.

**Q17. Is Proxy sufficient for protecting Server Actions?**

No. Every sensitive Server Action must independently verify authentication and authorization.

**Q18. What is a Data Access Layer?**

A server-side layer that centralizes data access, session verification and authorization.

**Q19. Which runtime does Proxy use in Next.js 16?**

Node.js runtime by default.

**Q20. Can we modify incoming request headers using Proxy?**

Yes. Use:

```
NextResponse.next({
  request: {
    headers: requestHeaders,
  },
});
```

**Q21. What is&#x20;**`waitUntil()`**?**

It allows a background promise to finish while the Proxy response proceeds.

**Q22. Why should we use a matcher?**

To avoid executing unnecessary Proxy logic on unrelated routes and static assets, and to precisely control coverage.

# **21. Phase 13 – Quick revision notes**

| Concept                 | Remember                                      |
| ----------------------- | --------------------------------------------- |
| Proxy                   | Intercepts incoming requests                  |
| Old convention          | `middleware.ts`                               |
| Next.js 16 convention   | `proxy.ts`                                    |
| Proxy function          | `proxy(request)`                              |
| Incoming request        | `NextRequest`                                 |
| Response handling       | `NextResponse`                                |
| Continue request        | `NextResponse.next()`                         |
| Redirect                | `NextResponse.redirect()`                     |
| Rewrite                 | `NextResponse.rewrite()`                      |
| URL pathname            | `request.nextUrl.pathname`                    |
| Query parameters        | `request.nextUrl.searchParams`                |
| Read cookies            | `request.cookies.get()`                       |
| Set response cookie     | `response.cookies.set()`                      |
| Delete response cookie  | `response.cookies.delete()`                   |
| Read headers            | `request.headers.get()`                       |
| Set response headers    | `response.headers.set()`                      |
| Forward request headers | `NextResponse.next({ request: { headers } })` |
| Route matching          | `config.matcher`                              |
| Authentication          | Verify session                                |
| Authorization           | Check permissions                             |
| DAL                     | Secure data access layer                      |
| Runtime                 | Node.js by default                            |
| Background work         | `event.waitUntil()`                           |

# **22. Phase 13 completion checklist**

You should now be able to explain these concepts independently:

- What is request interception?
- What is the difference between Middleware and Proxy?
- Where should `proxy.ts` be created?
- How does the Proxy function work?
- What are `NextRequest` and `NextResponse`?
- What does `NextResponse.next()` do?
- What is the difference between redirect and rewrite?
- What is the purpose of `matcher`?
- How do you match nested routes?
- How do you read cookies in Proxy?
- How do you read and modify HTTP headers?
- How do you protect dashboard routes?
- How do you implement role-based access checks?
- Why should Proxy not be the only security layer?
- What is a Data Access Layer?
- What is the purpose of `waitUntil()`?

## **23. What's next?**

**Phase 14 – Authentication, Session Management and Authorization**

Next

[How to Monitor Your Login and Authentication Flow | Webalert Blog](https://images.openai.com/static-rsc-4/9KRVcxKhT3uVAUIjB-PbR8SZBC_R1SDFdXh24W8soKiobF0YYJUjHBXTQdX1LzOyYPf8a69F2jQOUFZdnXJDOQt8rQ0dknPCMhhnbglBZM89NKTZM1HIPE1GqQR-XeAx2uRedRROYOIK_0qy6N5gsyX6dw4PZpMa4q9VIYPCNRU?purpose=inline)

[web-alert.io](https://web-alert.io/blog/login-authentication-flow-monitoring-guide?utm_source=chatgpt.com)

In the next phase, we will cover:

1. Authentication architecture in Next.js.
2. Registration and login workflows.
3. Password hashing using bcrypt or Argon2.
4. Sessions vs JWT.
5. Cookies and secure cookie configuration.
6. Stateless and database-backed sessions.
7. Authentication using Auth.js and authentication libraries.
8. Role-Based Access Control (RBAC).
9. Route and resource authorization.
10. Secure Data Access Layer implementation.
11. Logout and session expiration.
12. Complete authentication system with MongoDB.

When you're ready, say **OK, Phase 14**.

### **Official documentation**

1. [Next.js – Proxy file convention](https://nextjs.org/docs/app/api-reference/file-conventions/proxy?utm_source=chatgpt.com)
2. [Next.js – NextResponse API](https://nextjs.org/docs/app/api-reference/functions/next-response?utm_source=chatgpt.com)
3. [Next.js – Authentication guide](https://nextjs.org/docs/app/guides/authentication?utm_source=chatgpt.com)
4. [Next.js – Getting Started with Proxy](https://nextjs.org/docs/app/getting-started/proxy?utm_source=chatgpt.com)