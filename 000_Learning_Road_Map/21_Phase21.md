# **Phase 21 – Advanced Next.js Concepts and Architecture**



Advanced Level

Next.js 16.x

App Router + TypeScript



## **1. Learning objectives**

In the previous phases, you learned the individual features of Next.js, including routing, Server Components, Client Components, data fetching, caching, authentication, deployment, and testing.

Now we will focus on combining these features to build **scalable, maintainable, production-ready Next.js applications**.

By the end of this phase, you will understand:

1. Advanced App Router architecture.
2. Parallel Routes.
3. Intercepting Routes.
4. Advanced routing patterns.
5. Route groups and layout architecture.
6. Advanced Server and Client Component composition.
7. Dependency management and application layering.
8. Scalable folder structures.
9. Design patterns for large Next.js applications.
10. Real-world application architecture and best practices.

## **2. What is application architecture?**

Application architecture describes how an application is organized and how its different parts communicate with each other.

Suppose you are building an e-commerce application.

It contains:

- Authentication.
- Products.
- Categories.
- Shopping cart.
- Orders.
- Payments.
- Admin dashboard.
- User dashboard.
- Notifications.

If you put all the code into a few large files, the application becomes difficult to maintain.

A better approach is to divide the application into logical modules.

### **Poor architecture**

```
app/
├── page.tsx
├── components.tsx
├── functions.ts
├── api.ts
└── database.ts
```

This structure might work for a small project, but as the application grows, it becomes difficult to locate code and maintain boundaries.

### **Better architecture**

```
src/
│
├── app/
│   ├── (store)/
│   ├── (auth)/
│   ├── admin/
│   ├── api/
│   └── layout.tsx
│
├── features/
│   ├── auth/
│   ├── products/
│   ├── cart/
│   ├── orders/
│   └── payments/
│
├── components/
│   ├── ui/
│   ├── layout/
│   └── shared/
│
├── lib/
│   ├── database/
│   ├── security/
│   └── utils/
│
├── hooks/
├── types/
└── config/
```

Here, each directory has a clear responsibility.

For example:

- `app/` manages routes and page composition.
- `features/` contains domain-specific functionality.
- `components/` contains reusable UI.
- `lib/` contains infrastructure and shared utilities.
- `hooks/` contains reusable React hooks.
- `types/` contains shared TypeScript definitions.

**Important:** Next.js does not force one universal folder structure. Choose an architecture that makes dependencies, responsibilities, and ownership clear.

## **3. Understanding advanced App Router architecture**

The App Router is based on the filesystem.

You already know these conventions:

| File            | Responsibility                  |
| --------------- | ------------------------------- |
| `page.tsx`      | Route UI                        |
| `layout.tsx`    | Shared layout                   |
| `template.tsx`  | Re-created layout wrapper       |
| `loading.tsx`   | Loading UI                      |
| `error.tsx`     | Error boundary                  |
| `not-found.tsx` | Not-found UI                    |
| `route.ts`      | HTTP Route Handler              |
| `default.tsx`   | Fallback UI for Parallel Routes |
| `proxy.ts`      | Request interception            |

Now let's understand how these conventions work together.

### **Example: E-commerce application**

```
app/
│
├── layout.tsx
│
├── page.tsx
│
├── products/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── loading.tsx
│   ├── error.tsx
│   │
│   └── [productId]/
│       └── page.tsx
│
├── cart/
│   └── page.tsx
│
├── checkout/
│   └── page.tsx
│
└── admin/
    ├── layout.tsx
    ├── page.tsx
    └── products/
        └── page.tsx
```

### **How does a request work?**

Suppose a user visits:

```
/products/123
```

Next.js identifies:

```
app/
└── products/
    └── [productId]/
        └── page.tsx
```

It then renders the matching route within its applicable layouts and boundaries.

Conceptually:

```
Root Layout
    |
    v
Products Layout
    |
    v
Product Detail Page
```

If the product page has a loading boundary, Next.js can also render its loading UI while the relevant content is being prepared.

This is the foundation on which the advanced routing patterns are built.

## **4. Route groups – Organizing routes without changing URLs**

Route groups allow you to organize routes into logical sections without adding those group names to the URL.

You create a route group by wrapping a folder name in parentheses.

Example:

```
app/
│
├── (marketing)/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── about/
│   │   └── page.tsx
│   └── contact/
│       └── page.tsx
│
├── (auth)/
│   ├── layout.tsx
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
│
└── (dashboard)/
    ├── layout.tsx
    ├── dashboard/
    │   └── page.tsx
    └── settings/
        └── page.tsx
```

### **URL mapping**

| File path                            | URL          |
| ------------------------------------ | ------------ |
| `app/(marketing)/page.tsx`           | `/`          |
| `app/(marketing)/about/page.tsx`     | `/about`     |
| `app/(marketing)/contact/page.tsx`   | `/contact`   |
| `app/(auth)/login/page.tsx`          | `/login`     |
| `app/(auth)/register/page.tsx`       | `/register`  |
| `app/(dashboard)/dashboard/page.tsx` | `/dashboard` |
| `app/(dashboard)/settings/page.tsx`  | `/settings`  |

Notice that the folder names `(marketing)`, `(auth)`, and `(dashboard)` do not appear in the URLs.

### **Why use route groups?**

Suppose your application has three different sections:

1. Public website.
2. Authentication pages.
3. Admin dashboard.

Each section may need a different layout.

Route groups let you organize them independently.

### **Example: Marketing layout**

```
// app/(marketing)/layout.tsx

import Header from "@/components/layout/Header";
import Footer from "@/components/layout/Footer";

export default function MarketingLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <Header />

      <main>{children}</main>

      <Footer />
    </>
  );
}
```

### **Example: Authentication layout**

```
// app/(auth)/layout.tsx

export default function AuthLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <main className="auth-container">
      {children}
    </main>
  );
}
```

### **Example: Dashboard layout**

```
// app/(dashboard)/layout.tsx

import Sidebar from "@/components/layout/Sidebar";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="dashboard-layout">
      <Sidebar />

      <main>{children}</main>
    </div>
  );
}
```

Now each group has its own layout without changing the URL structure.

### **Important rules**

1. Route groups do not affect URL paths.
2. You can use route groups to organize layouts.
3. Different route groups can have different layouts.
4. Do not create conflicting routes that resolve to the same URL.
5. Multiple root layouts are possible, but navigating between them causes a full page load.

**When should you use route groups?**

Use them when you need to organize routes by application section, layout, or responsibility without changing the public URL.

## **5. Parallel Routes**

Important advanced routing concept

Parallel Routes allow Next.js to render multiple independent pages or route sections simultaneously within the same layout.

They are especially useful for:

- Admin dashboards.
- Analytics dashboards.
- Social media feeds.
- Multi-panel interfaces.
- Independent navigation sections.
- Modal interfaces.

### **5.1 Understand the concept**

Imagine an admin dashboard with three sections:

1. Main dashboard.
2. Analytics panel.
3. Team activity panel.

You want all three sections to render together.

Without Parallel Routes, you might build one large page that fetches and renders everything.

With Parallel Routes, you can divide these sections into independent slots.

[Next.js 14 Parallel Routes: A Detailed Guide](https://images.openai.com/static-rsc-4/8CKNV_oTV3ovGE71H9a-z3XFRoGOdwTrh_hiExnCFgxfbNf-HKqwe3jL1q4QdvHXe-xItlQH9UZyhDVlTQFv9JnFjGcs_mRT7S_nrKOr2ivzIcUyR41iywdE8e4wxDvJv2R8MWq8-QKqQPSagoO5aInlDUfDRUaF6xcVeuev5zk?purpose=inline)

[builder.io](https://www.builder.io/blog/nextjs-14-parallel-routes?utm_source=chatgpt.com)

### **5.2 What is a slot?**

A slot is a named area in a layout that receives independently routed content.

You create a slot using the `@folderName` convention.

Example:

```
app/
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    │
    ├── @analytics/
    │   ├── page.tsx
    │   └── default.tsx
    │
    └── @team/
        ├── page.tsx
        └── default.tsx
```

Here:

- `@analytics` is a slot.
- `@team` is another slot.
- `children` is the implicit slot for the normal page.

**Important:** Slot folder names do not become part of the URL.

For example:

```
/dashboard
```

does not become:

```
/dashboard/@analytics
```

### **5.3 Create the dashboard layout**

```
// app/dashboard/layout.tsx

export default function DashboardLayout({
  children,
  analytics,
  team,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <div>
      <h1>Admin Dashboard</h1>

      <section>
        {children}
      </section>

      <div className="dashboard-grid">
        <section>
          {analytics}
        </section>

        <section>
          {team}
        </section>
      </div>
    </div>
  );
}
```

Notice that the layout receives three props:

```
children
analytics
team
```

The `analytics` and `team` props correspond to the `@analytics` and `@team` folders.

### **5.4 Create the main dashboard page**

```
// app/dashboard/page.tsx

export default function DashboardPage() {
  return (
    <div>
      <h2>Dashboard Overview</h2>
      <p>Welcome to your admin dashboard.</p>
    </div>
  );
}
```

### **5.5 Create the analytics slot**

```
// app/dashboard/@analytics/page.tsx

export default function AnalyticsPage() {
  return (
    <div>
      <h2>Analytics</h2>
      <p>Total Revenue: ₹5,00,000</p>
      <p>Total Orders: 1,200</p>
    </div>
  );
}
```

### **5.6 Create the team slot**

```
// app/dashboard/@team/page.tsx

export default function TeamPage() {
  return (
    <div>
      <h2>Team Activity</h2>
      <p>Ali added a new product.</p>
      <p>Ahmed updated an order.</p>
    </div>
  );
}
```

### **5.7 Add default.tsx files**

This is particularly important in Next.js 16.

Every Parallel Route slot needs an explicit `default.tsx` fallback. The implicit `children` slot also needs a fallback when using this pattern.

Create:

```
// app/dashboard/@analytics/default.tsx

export default function DefaultAnalytics() {
  return null;
}
```

```
// app/dashboard/@team/default.tsx

export default function DefaultTeam() {
  return null;
}
```

And:

```
// app/dashboard/default.tsx

export default function DefaultDashboard() {
  return null;
}
```

In Next.js 16, missing required Parallel Route defaults can cause a build failure.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+2



### **5.8 How Parallel Routes work**

Conceptually, the layout receives:

```
DashboardLayout
│
├── children
│   └── Dashboard Overview
│
├── analytics
│   └── Analytics
│
└── team
    └── Team Activity
```

The result is a combined dashboard interface.

### **5.9 Soft navigation vs hard navigation**

You must understand this distinction.

**Soft navigation**

Soft navigation happens when you navigate using Next.js client-side navigation, such as `<Link>`.

Next.js can preserve the active state of individual slots.

For example:

```
Current dashboard:
Analytics → Revenue
Team → Activity

Navigate to another dashboard route

Analytics slot may preserve its active page.
Team slot may preserve its active page.
```

**Hard navigation**

Hard navigation happens when:

- You refresh the browser.
- You directly enter a URL.
- You open a URL in a new browser tab.

Next.js cannot always recover the previous active state of every slot.

It uses `default.tsx` for unmatched slots.

### **5.10 When should you use Parallel Routes?**

Use Parallel Routes when different parts of a screen need independent routing behavior.

Examples:

| Application        | Parallel Route use          |
| ------------------ | --------------------------- |
| Admin dashboard    | Analytics and team panels   |
| Social media       | Feed and activity panel     |
| Email application  | Inbox and message panel     |
| Project management | Tasks and activity          |
| E-commerce         | Main content and side panel |

**Official documentation:** [Next.js Parallel Routes](https://nextjs.org/docs/app/api-reference/file-conventions/parallel-routes?utm_source=chatgpt.com).

## **6. Intercepting Routes**

Important for modal-based navigation

Intercepting Routes allow you to display another route inside the current layout without completely replacing the current page.

A common example is opening a photo in a modal.

Imagine an image gallery.

You have:

```
/photos
```

When a user clicks a photo, you want to:

- Change the URL to `/photo/123`.
- Display the selected photo in a modal.
- Keep the gallery visible behind the modal.

But if the user directly visits `/photo/123`, you want a full photo detail page.

This is exactly the kind of problem Intercepting Routes solve.

### **6.1 Normal navigation vs intercepted navigation**

[Websites for photographers | online photo sales and proofing](https://images.openai.com/static-rsc-4/OnntonpT11AQUu7-hrj2kbVGFMxObKegAD9weC0CHzrqNWt4fKv-2jsisXBRp64KNkc9G1jwbYjUBlC8gVfzvGN4sP5RLlO2DtzjCr4lldWSdoi1I9SjYgCc4ojq4ITCOl4atNxj_gbPhROwiXzHvoZZMVi6V8JhlxXEdx6TWXE?purpose=inline)

**Normal navigation**

Gallery page is replaced by the photo detail page.

[Bootstrap Lightbox Multiple Images - Modal Gallery — CodeHim](https://images.openai.com/static-rsc-4/OGhlNvKAeJsUSxFICHloeMjsxg-sbCeejM0je9zrQiJUwscXOBXw4ebd0yr6wgwBCDCim9TPKlSUy1jQNXpRxi4vyc6zSW-Fqu7Sy8Gm49WyzFfMvzwASTE9ADIoTenzk3ynMCohiOhrl_8smSUm75yFjPyJGRSluiZhtO9MHlI?purpose=inline)

**Intercepted navigation**

Photo opens in a modal while the gallery stays visible.

### **6.2 Intercepting Route conventions**

Next.js provides special folder naming conventions.

| Convention | Meaning                             |
| ---------- | ----------------------------------- |
| `(.)`      | Match a segment at the same level   |
| `(..)`     | Match a segment one level above     |
| `(..)(..)` | Match two levels above              |
| `(...)`    | Match from the root `app` directory |

These conventions are based on route segments, not the number of physical folders.

**Important:** Parallel Route folders such as `@modal` do not count as route segments.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **6.3 Understand&#x20;**`(.)`

Suppose you have:

```
app/
├── feed/
│   └── page.tsx
│
└── photo/
    └── [id]/
        └── page.tsx
```

The photo route is:

```
/photo/123
```

Now suppose you want to intercept that route from the feed.

You can use:

```
app/
└── feed/
    ├── page.tsx
    │
    └── (.)photo/
        └── [id]/
            └── page.tsx
```

The `(.)` convention tells Next.js to intercept a route segment at the same route level.

### **6.4 Understand&#x20;**`(..)`

Suppose you have:

```
app/
├── photo/
│   └── [id]/
│       └── page.tsx
│
└── feed/
    └── page.tsx
```

If you want to intercept the `photo` route from within the `feed` route segment, you can use:

```
app/
└── feed/
    ├── page.tsx
    │
    └── (..)photo/
        └── [id]/
            └── page.tsx
```

Here, `(..)` matches one route segment above.

### **6.5 Understand&#x20;**`(...)`

`(...)` intercepts a route from the root of the `app` directory.

It is useful when the intercepted route is defined elsewhere in the application.

Example:

```
app/
├── photo/
│   └── [id]/
│       └── page.tsx
│
└── feed/
    ├── page.tsx
    └── (...)photo/
        └── [id]/
            └── page.tsx
```

### **6.6 Quick example**

Suppose your current route is:

```
app/feed/
```

You want to intercept:

```
/photo/123
```

The folder naming could be:

```
app/feed/(..)photo/[id]/page.tsx
```

Because `photo` is one route segment above `feed`.

## **7. Combining Parallel Routes and Intercepting Routes**

This is where these two concepts become especially powerful.

You can combine them to create URL-addressable modals.

Let's build a photo gallery example.

### **7.1 Expected behavior**

Suppose the user is on:

```
/feed
```

They click a photo.

The browser URL becomes:

```
/photo/123
```

But instead of replacing the feed, Next.js displays the photo in a modal.

If the user refreshes the browser at:

```
/photo/123
```

Next.js displays the full photo detail page.

**Expected navigation behavior**

Click photo from feed

Modal

Soft navigation → gallery remains visible behind the modal.

Direct URL or refresh

Full page

Hard navigation → standalone photo detail page.

### **7.2 Folder structure**

Create:

```
app/
│
├── layout.tsx
├── default.tsx
│
├── feed/
│   └── page.tsx
│
├── photo/
│   └── [id]/
│       └── page.tsx
│
└── @modal/
    ├── default.tsx
    ├── [...catchAll]/
    │   └── page.tsx
    │
    └── (.)photo/
        └── [id]/
            └── page.tsx
```

Let's understand the important parts:

| File                                | Purpose                                             |
| ----------------------------------- | --------------------------------------------------- |
| `app/layout.tsx`                    | Renders the main page and modal slot                |
| `app/@modal/default.tsx`            | Fallback when the modal slot has no active route    |
| `app/@modal/(.)photo/[id]/page.tsx` | Intercepts photo navigation and displays a modal    |
| `app/photo/[id]/page.tsx`           | Displays the standalone photo page                  |
| `app/@modal/[...catchAll]/page.tsx` | Matches other paths in the modal slot and clears it |
| `app/default.tsx`                   | Fallback for the implicit `children` slot           |

### **7.3 Create the root layout**

```
// app/layout.tsx

export default function RootLayout({
  children,
  modal,
}: {
  children: React.ReactNode;
  modal: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {children}

        {modal}
      </body>
    </html>
  );
}
```

Here:

- `children` renders the normal page.
- `modal` renders the intercepted route when it is active.

### **7.4 Create the default fallback**

```
// app/@modal/default.tsx

export default function Default() {
  return null;
}
```

Create the implicit children fallback:

```
// app/default.tsx

export default function Default() {
  return null;
}
```

### **7.5 Create a modal component**

This component is a Client Component because it uses `useRouter()`.

```
// components/PhotoModal.tsx

"use client";

import { useRouter } from "next/navigation";

export default function PhotoModal({
  children,
}: {
  children: React.ReactNode;
}) {
  const router = useRouter();

  return (
    <div
      className="modal-backdrop"
      onClick={() => router.back()}
    >
      <div
        className="modal-content"
        onClick={(event) => event.stopPropagation()}
      >
        <button onClick={() => router.back()}>
          Close
        </button>

        {children}
      </div>
    </div>
  );
}
```

For a production modal, also implement keyboard dismissal, focus management, accessible dialog semantics, and focus restoration. The example focuses on routing behavior.

### **7.6 Create the gallery page**

```
// app/feed/page.tsx

import Link from "next/link";

export default function FeedPage() {
  const photos = [
    { id: "1", title: "Mountain" },
    { id: "2", title: "Ocean" },
    { id: "3", title: "Forest" },
  ];

  return (
    <main>
      <h1>Photo Gallery</h1>

      {photos.map((photo) => (
        <div key={photo.id}>
          <Link href={`/photo/${photo.id}`}>
            {photo.title}
          </Link>
        </div>
      ))}
    </main>
  );
}
```

### **7.7 Create the standalone photo page**

```
// app/photo/[id]/page.tsx

export default async function PhotoPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  return (
    <main>
      <h1>Photo Details</h1>

      <p>Photo ID: {id}</p>
    </main>
  );
}
```

Remember that in Next.js 16, dynamic route `params` are asynchronous.

### **7.8 Create the intercepted photo page**

```
// app/@modal/(.)photo/[id]/page.tsx

import PhotoModal from "@/components/PhotoModal";

export default async function InterceptedPhotoPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  return (
    <PhotoModal>
      <h2>Photo Preview</h2>

      <p>Photo ID: {id}</p>
    </PhotoModal>
  );
}
```

### **7.9 Create the catch-all fallback**

```
// app/@modal/[...catchAll]/page.tsx

export default function CatchAll() {
  return null;
}
```

This makes the modal slot resolve to `null` for other matching navigation paths.

### **7.10 Test the behavior**

**Scenario A – Soft navigation**

1. Open `/feed`.
2. Click the Mountain link.
3. URL changes to `/photo/1`.
4. Photo appears inside a modal.
5. Click Close or use browser Back.

**Scenario B – Hard navigation**

1. Open a new browser tab.
2. Visit `/photo/1` directly.
3. The full Photo Details page appears.

This combination provides shareable URLs without losing the user's original context.

**Important:** The modal is not simply a React overlay. It is connected to the routing system, which is why browser history and direct navigation behave differently.

Official documentation:

- [Intercepting Routes](https://nextjs.org/docs/app/api-reference/file-conventions/intercepting-routes?utm_source=chatgpt.com)
- [Parallel Routes and modals](https://nextjs.org/docs/app/api-reference/file-conventions/parallel-routes?utm_source=chatgpt.com)

## **8. Advanced layout architecture**

In large applications, layouts are used to share persistent UI across related routes.

For example, an e-commerce application may have:

```
Application
│
├── Public Website
│   ├── Header
│   ├── Footer
│   └── Public Pages
│
├── Authentication
│   └── Authentication Pages
│
└── Admin Dashboard
    ├── Sidebar
    ├── Topbar
    └── Dashboard Pages
```

### **8.1 Nested layouts**

Example:

```
app/
│
├── layout.tsx
│
└── admin/
    ├── layout.tsx
    ├── page.tsx
    │
    └── products/
        ├── layout.tsx
        ├── page.tsx
        │
        └── [id]/
            └── page.tsx
```

The layout hierarchy for `/admin/products/123` is:

```
Root Layout
     |
     v
Admin Layout
     |
     v
Products Layout
     |
     v
Product Detail Page
```

Each layout wraps the content below it.

### **8.2 Why do layouts matter?**

Layouts allow you to:

- Share common UI.
- Preserve layout state across navigation.
- Avoid duplicating headers and sidebars.
- Organize application sections.
- Define loading and error boundaries at appropriate levels.

### **8.3 Layout persistence**

Suppose you have a dashboard sidebar with an expanded/collapsed state.

If the sidebar belongs to a persistent dashboard layout, navigating between dashboard child pages generally preserves that layout state.

This is one reason to put shared UI in layouts instead of repeating it in individual pages.

### **8.4 Multiple root layouts**

You can create separate root layouts for independent parts of an application.

Example:

```
app/
│
├── (marketing)/
│   ├── layout.tsx
│   ├── page.tsx
│   └── about/
│       └── page.tsx
│
└── (application)/
    ├── layout.tsx
    ├── dashboard/
    │   └── page.tsx
    └── settings/
        └── page.tsx
```

Each root layout must define its own:

```
<html>
  <body>
    {children}
  </body>
</html>
```

There is no top-level `app/layout.tsx` in this structure.

**Important:** Navigating between pages using different root layouts triggers a full page load rather than ordinary client-side navigation.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **9. Private folders and colocation**

Next.js allows you to organize files without making every folder a route.

### **9.1 Private folders**

A folder beginning with an underscore is a private folder.

Example:

```
app/
│
├── dashboard/
│   ├── page.tsx
│   │
│   └── _components/
│       ├── DashboardHeader.tsx
│       └── DashboardStats.tsx
│
└── products/
    ├── page.tsx
    │
    └── _lib/
        └── productHelpers.ts
```

The `_components` and `_lib` directories are excluded from routing.

### **9.2 Why use private folders?**

You may have components that belong exclusively to one route.

For example:

```
dashboard/
├── page.tsx
└── _components/
    ├── DashboardHeader.tsx
    └── DashboardStats.tsx
```

These components are related to the dashboard and don't need to be globally shared.

### **9.3 What is colocation?**

Colocation means keeping related files close to each other.

Example:

```
products/
│
├── page.tsx
├── loading.tsx
├── error.tsx
│
├── _components/
│   ├── ProductCard.tsx
│   └── ProductFilters.tsx
│
└── _lib/
    └── productHelpers.ts
```

This makes the product section easier to understand and maintain.

**Important:** A private folder is not a security mechanism. Sensitive server code should also use appropriate server-only boundaries, and authorization must be enforced on the server.

## **10. Feature-based architecture**

This is one of the most useful folder organization approaches for large applications.

Instead of organizing everything by technical type, you organize code by business feature.

### **10.1 Traditional layer-based structure**

```
src/
│
├── components/
│   ├── ProductCard.tsx
│   ├── ProductForm.tsx
│   └── CartItem.tsx
│
├── hooks/
│   ├── useCart.ts
│   └── useProducts.ts
│
├── services/
│   ├── productService.ts
│   ├── cartService.ts
│   └── orderService.ts
│
├── types/
│   ├── product.ts
│   ├── cart.ts
│   └── order.ts
│
└── utils/
    └── helpers.ts
```

This is organized by technical responsibility.

The problem is that files belonging to one feature are distributed across many folders.

Suppose you want to modify the product functionality. You may need to search in:

- Components.
- Hooks.
- Services.
- Types.
- Utils.

### **10.2 Feature-based structure**

Instead, organize by feature:

```
src/
│
├── app/
│   ├── products/
│   ├── cart/
│   └── orders/
│
├── features/
│   │
│   ├── products/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── schemas/
│   │   ├── types/
│   │   └── index.ts
│   │
│   ├── cart/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── types/
│   │   └── index.ts
│   │
│   └── orders/
│       ├── components/
│       ├── services/
│       ├── schemas/
│       └── types/
│
├── components/
│   ├── ui/
│   └── layout/
│
└── lib/
    ├── database/
    └── security/
```

Now all product-related functionality is inside:

```
features/products/
```

### **10.3 Example product feature**

```
features/
└── products/
    ├── components/
    │   ├── ProductCard.tsx
    │   ├── ProductList.tsx
    │   └── ProductForm.tsx
    │
    ├── services/
    │   ├── getProducts.ts
    │   └── createProduct.ts
    │
    ├── schemas/
    │   └── productSchema.ts
    │
    ├── types/
    │   └── product.ts
    │
    └── index.ts
```

Product type:

```
// features/products/types/product.ts

export type Product = {
  id: string;
  name: string;
  price: number;
  description: string;
};
```

Product schema:

```
// features/products/schemas/productSchema.ts

import { z } from "zod";

export const productSchema = z.object({
  name: z.string().min(2),
  price: z.number().positive(),
  description: z.string().min(5),
});
```

Product service:

```
// features/products/services/getProducts.ts

import "server-only";

import { connectDB } from "@/lib/database/mongodb";
import Product from "@/models/Product";

export async function getProducts() {
  await connectDB();

  return Product.find().lean();
}
```

Page:

```
// app/products/page.tsx

import { getProducts } from "@/features/products/services/getProducts";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product) => (
        <div key={String(product._id)}>
          {product.name}
        </div>
      ))}
    </main>
  );
}
```

This page is responsible for composing the UI. The feature service is responsible for retrieving products.

### **10.4 Benefits of feature-based architecture**

| Benefit             | Explanation                                       |
| ------------------- | ------------------------------------------------- |
| Better organization | Related code stays together                       |
| Maintainability     | Easier to update individual features              |
| Reusability         | Feature modules can expose reusable functionality |
| Scalability         | New features can be added independently           |
| Team collaboration  | Developers can work on separate modules           |
| Easier testing      | Tests can be colocated with feature code          |

### **10.5 Feature-based vs layer-based architecture**

| Layer-based                                    | Feature-based                          |
| ---------------------------------------------- | -------------------------------------- |
| Organized by technical type                    | Organized by business capability       |
| Components in one folder                       | Feature components grouped together    |
| Services in one folder                         | Feature services grouped together      |
| Can become difficult to navigate in large apps | Often easier to scale by domain        |
| Suitable for smaller applications              | Often suitable for larger applications |

Neither is universally superior. You can also combine both approaches.

For example, use feature-based organization for business functionality and shared technical folders for infrastructure and reusable UI.

## **11. Separation of concerns**

Separation of concerns means giving different parts of your application distinct responsibilities.

Consider a product page.

A poorly structured page might:

- Read the database.
- Validate data.
- Check permissions.
- Calculate prices.
- Render the UI.
- Handle errors.
- Send analytics events.

All in one file.

This creates tightly coupled code.

### **11.1 Separate responsibilities**

A better structure:

```
features/products/
│
├── components/
│   └── ProductList.tsx
│
├── services/
│   └── getProducts.ts
│
├── repositories/
│   └── productRepository.ts
│
├── schemas/
│   └── productSchema.ts
│
└── types/
    └── product.ts
```

Each layer has a different responsibility.

| Layer           | Responsibility                  |
| --------------- | ------------------------------- |
| UI / Components | Display information             |
| Services        | Implement application use cases |
| Repositories    | Communicate with data storage   |
| Schemas         | Validate input                  |
| Types           | Define data structures          |

### **11.2 Understand the request flow**

```
Page
  |
  v
Product Service
  |
  v
Product Repository
  |
  v
Database
```

The page does not need to know the details of MongoDB queries.

It calls a service, and the service uses a repository.

### **11.3 Repository example**

```
// features/products/repositories/productRepository.ts

import "server-only";

import { connectDB } from "@/lib/database/mongodb";
import Product from "@/models/Product";

export async function findAllProducts() {
  await connectDB();

  return Product.find().lean();
}
```

### **11.4 Service example**

```
// features/products/services/getProducts.ts

import "server-only";

import { findAllProducts } from "../repositories/productRepository";

export async function getProducts() {
  const products = await findAllProducts();

  return products;
}
```

### **11.5 Page example**

```
// app/products/page.tsx

import { getProducts } from "@/features/products/services/getProducts";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      {products.map((product) => (
        <p key={String(product._id)}>
          {product.name}
        </p>
      ))}
    </main>
  );
}
```

### **Why use this architecture?**

Suppose you later change your database from MongoDB to PostgreSQL.

If database-specific code is isolated in repositories, you can replace that layer without rewriting every page and component.

This is an example of reducing coupling.

**Note:** A repository layer is not mandatory for every Next.js project. For a small application, a separate service may be enough. Add abstraction when it solves a real maintenance or testing problem.

## **12. Data Access Layer (DAL)**

The Data Access Layer is particularly important for applications that contain authentication and sensitive data.

A DAL centralizes access to data and can enforce security checks before returning information.

You learned about DAL concepts in Phase 14. Now let's understand how they fit into a larger architecture.

### **12.1 What problem does a DAL solve?**

Imagine you have an order database.

Different parts of your application need to retrieve orders:

- User dashboard.
- Admin dashboard.
- Order detail page.
- API endpoints.

You should not duplicate database queries and authorization logic everywhere.

Instead, centralize them.

### **12.2 Example DAL structure**

```
src/
│
├── lib/
│   └── dal/
│       ├── auth.ts
│       ├── users.ts
│       └── orders.ts
│
└── app/
```

### **12.3 Create an order DAL function**

```
// lib/dal/orders.ts

import "server-only";

import { cache } from "react";

import { verifySession } from "./auth";

import { connectDB } from "@/lib/database/mongodb";

import Order from "@/models/Order";

export const getMyOrders = cache(async () => {
  const session = await verifySession();

  if (!session) {
    throw new Error("Unauthorized");
  }

  await connectDB();

  const orders = await Order.find({
    userId: session.userId,
  }).lean();

  return orders;
});
```

This function:

1. Verifies the current session.
2. Connects to the database.
3. Retrieves only the authenticated user's orders.
4. Returns the result.

### **12.4 Use the DAL from a page**

```
// app/orders/page.tsx

import { getMyOrders } from "@/lib/dal/orders";

export default async function OrdersPage() {
  const orders = await getMyOrders();

  return (
    <main>
      <h1>My Orders</h1>

      {orders.map((order) => (
        <p key={String(order._id)}>
          {String(order._id)}
        </p>
      ))}
    </main>
  );
}
```

### **12.5 Why use&#x20;**`server-only`**?**

```
import "server-only";
```

This package helps prevent a server-only module from being imported into a Client Component module graph.

It is useful for protecting code that contains:

- Database queries.
- Private environment variables.
- Authentication utilities.
- Secret API keys.
- Internal business logic.

### **Important security principle**

Do not rely only on Proxy, middleware, layouts, or hidden UI elements to protect data.

Perform authentication and authorization checks in the server-side data access or mutation layer.

## **13. Advanced Server and Client Component composition**

You already learned Server Components and Client Components in Phase 9.

Now let's study how to combine them in a scalable architecture.

### **13.1 The basic difference**

| Server Component                             | Client Component                           |
| -------------------------------------------- | ------------------------------------------ |
| Default component type                       | Requires `"use client"`                    |
| Can access server-side data sources          | Can use browser APIs                       |
| Can access server-only modules               | Cannot directly import server-only modules |
| Cannot use state hooks directly              | Can use state hooks                        |
| Cannot use event handlers in rendered markup | Can use event handlers                     |
| Good for server rendering and data access    | Good for interactivity                     |

### **13.2 Example architecture**

Suppose you are creating a product listing page.

You want:

- Server-side product fetching.
- Client-side filtering.
- Interactive search.
- Server-rendered product details.

A good composition is:

```
Products Page (Server Component)
        |
        v
Product List (Client Component)
        |
        ├── Search Input
        ├── Category Filter
        └── Product Cards
```

### **13.3 Server Component**

```
// app/products/page.tsx

import { getProducts } from "@/features/products/services/getProducts";

import ProductList from "@/features/products/components/ProductList";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <main>
      <h1>Products</h1>

      <ProductList products={products} />
    </main>
  );
}
```

### **13.4 Client Component**

```
// features/products/components/ProductList.tsx

"use client";

import { useState } from "react";

type Product = {
  id: string;
  name: string;
};

export default function ProductList({
  products,
}: {
  products: Product[];
}) {
  const [search, setSearch] = useState("");

  const filteredProducts = products.filter((product) =>
    product.name.toLowerCase().includes(search.toLowerCase())
  );

  return (
    <section>
      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
        placeholder="Search products"
      />

      {filteredProducts.map((product) => (
        <p key={product.id}>
          {product.name}
        </p>
      ))}
    </section>
  );
}
```

This example assumes the server has converted database records into serializable product objects before passing them to the Client Component.

### **13.5 Why is this architecture useful?**

The Server Component:

- Retrieves data securely.
- Avoids unnecessary browser-side data fetching.
- Keeps database credentials on the server.

The Client Component:

- Manages user interaction.
- Maintains search state.
- Filters displayed products.

This division helps avoid turning your entire page into a Client Component.

### **13.6 Composition pattern: Server Component inside Client Component**

You can also pass server-rendered content as a prop to a Client Component.

Example:

```
// app/dashboard/page.tsx

import InteractivePanel from "@/components/InteractivePanel";

import ServerContent from "@/components/ServerContent";

export default function DashboardPage() {
  return (
    <InteractivePanel>
      <ServerContent />
    </InteractivePanel>
  );
}
```

Client Component:

```
// components/InteractivePanel.tsx

"use client";

import { useState } from "react";

export default function InteractivePanel({
  children,
}: {
  children: React.ReactNode;
}) {
  const [visible, setVisible] = useState(true);

  return (
    <div>
      <button onClick={() => setVisible(!visible)}>
        Toggle Content
      </button>

      {visible && children}
    </div>
  );
}
```

Server Component:

```
// components/ServerContent.tsx

export default function ServerContent() {
  return (
    <div>
      <h2>Server-rendered content</h2>
    </div>
  );
}
```

The Client Component does not import the Server Component directly. The server page composes them and passes the rendered content through `children`.

This is a useful way to keep interactive wrappers small while retaining server-rendered content.

## **14. Dependency management and dependency injection**

These concepts become important when applications have many services and external dependencies.

### **14.1 What is a dependency?**

A dependency is something a module needs to perform its work.

For example:

```
export async function getProducts() {
  const products = await Product.find();

  return products;
}
```

This function depends on the `Product` model.

If the model is unavailable, the function cannot work.

### **14.2 What is tight coupling?**

Tight coupling happens when one module is strongly dependent on another specific implementation.

Example:

```
import Product from "@/models/Product";

export async function getProducts() {
  return Product.find();
}
```

This service directly depends on a particular database model.

It may become harder to test or replace that dependency.

### **14.3 What is dependency injection?**

Dependency Injection (DI) means providing a dependency to a module instead of making that module create or directly select its dependency.

Example:

```
type Product = {
  id: string;
  name: string;
};

type ProductRepository = {
  findAll: () => Promise<Product[]>;
};

export function createProductService(
  repository: ProductRepository
) {
  return {
    async getProducts() {
      return repository.findAll();
    },
  };
}
```

Here, `createProductService()` receives its repository as an argument.

It does not need to know whether the repository uses MongoDB, MySQL, or an in-memory array.

### **14.4 Provide a real repository**

```
const productRepository: ProductRepository = {
  async findAll() {
    return [
      { id: "1", name: "Laptop" },
      { id: "2", name: "Mouse" },
    ];
  },
};
```

Create the service:

```
const productService = createProductService(
  productRepository
);
```

Use it:

```
const products = await productService.getProducts();
```

### **14.5 Why is DI useful?**

You can supply different implementations.

Production:

```
const service = createProductService(
  mongoProductRepository
);
```

Testing:

```
const service = createProductService(
  mockProductRepository
);
```

The service logic stays the same.

### **When should you use DI?**

Useful when:

- A service has multiple external dependencies.
- You need easy unit testing.
- You want to switch infrastructure implementations.
- Your application has complex business logic.

You do not need a dependency injection framework for every Next.js application. Passing dependencies through function parameters is often sufficient.

## **15. Design patterns for Next.js applications**

Design patterns are reusable approaches to common software design problems.

You should understand the following patterns.

### **15.1 Container and presentational pattern**

This pattern separates data and application logic from UI rendering.

**Container component:**

Responsible for retrieving or preparing data.

**Presentational component:**

Responsible for displaying the data.

Example:

```
// app/products/page.tsx

import { getProducts } from "@/features/products/services/getProducts";

import ProductList from "@/features/products/components/ProductList";

export default async function ProductsPage() {
  const products = await getProducts();

  return <ProductList products={products} />;
}
```

Presentational component:

```
// features/products/components/ProductList.tsx

type Product = {
  id: string;
  name: string;
};

export default function ProductList({
  products,
}: {
  products: Product[];
}) {
  return (
    <div>
      {products.map((product) => (
        <p key={product.id}>
          {product.name}
        </p>
      ))}
    </div>
  );
}
```

This makes the UI easier to reuse and test.

### **15.2 Repository pattern**

The repository pattern separates database operations from business logic.

```
Service
   |
   v
Repository
   |
   v
Database
```

Example responsibilities:

- `productRepository.ts`: Fetch products.
- `orderRepository.ts`: Fetch orders.
- `userRepository.ts`: Fetch users.

You learned this pattern in the separation-of-concerns section.

### **15.3 Service layer pattern**

The service layer contains application use cases.

For example:

```
// features/orders/services/createOrder.ts

import "server-only";

export async function createOrder(
  userId: string,
  productId: string,
  quantity: number
) {
  if (quantity <= 0) {
    throw new Error("Invalid quantity");
  }

  // Check product availability
  // Calculate order total
  // Create order
  // Return result
}
```

The service coordinates business operations instead of putting all the logic inside a page or Route Handler.

### **15.4 Adapter pattern**

An adapter allows one interface to work with a different implementation.

Suppose you use different payment providers:

```
Application
    |
    v
Payment Interface
    |
    ├── Stripe Adapter
    ├── Razorpay Adapter
    └── Mock Payment Adapter
```

The application can use one common interface:

```
export interface PaymentProvider {
  charge(
    amount: number,
    currency: string
  ): Promise<{ transactionId: string }>;
}
```

One implementation might use a payment provider's SDK, while another uses a mock for tests.

This reduces dependence on a specific provider.

### **15.5 Strategy pattern**

The Strategy pattern allows you to select different algorithms or behaviors through a common interface.

For example, your e-commerce application may calculate shipping differently:

```
type ShippingStrategy = {
  calculate(amount: number): number;
};

const standardShipping: ShippingStrategy = {
  calculate() {
    return 50;
  },
};

const expressShipping: ShippingStrategy = {
  calculate() {
    return 150;
  },
};
```

Select the strategy:

```
function calculateShipping(
  strategy: ShippingStrategy,
  orderAmount: number
) {
  return strategy.calculate(orderAmount);
}
```

Usage:

```
const shipping = calculateShipping(
  expressShipping,
  5000
);
```

### **15.6 Facade pattern**

A facade provides a simplified interface to a more complex subsystem.

For example, you might create:

```
export async function registerNewUser(data: {
  name: string;
  email: string;
  password: string;
}) {
  // Validate input
  // Check duplicate email
  // Hash password
  // Create user
  // Create session
  // Return result
}
```

The caller does not need to coordinate all those operations individually.

### **Pattern comparison**

| Pattern                  | Main purpose                      |
| ------------------------ | --------------------------------- |
| Container/presentational | Separate data logic from UI       |
| Repository               | Isolate data access               |
| Service layer            | Centralize use cases              |
| Adapter                  | Connect different implementations |
| Strategy                 | Select interchangeable behavior   |
| Facade                   | Simplify complex operations       |
| Dependency Injection     | Supply dependencies from outside  |

**Important:** Design patterns are tools, not rules. Use them when they solve an actual design problem. Avoid creating unnecessary abstractions in small projects.

## **16. Shared components and design system architecture**

Large applications often contain hundreds of UI components.

If every feature creates its own button, input, modal, and dropdown, the application becomes inconsistent.

A design system helps solve this problem.

### **16.1 Recommended structure**

```
src/
│
├── components/
│   │
│   ├── ui/
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Modal.tsx
│   │   ├── Dropdown.tsx
│   │   └── Badge.tsx
│   │
│   ├── layout/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── Sidebar.tsx
│   │   └── Navbar.tsx
│   │
│   └── shared/
│       ├── EmptyState.tsx
│       ├── ErrorMessage.tsx
│       └── PageLoader.tsx
```

### **16.2 Component categories**

| Category           | Example            | Purpose                 |
| ------------------ | ------------------ | ----------------------- |
| UI components      | Button, Input      | Basic reusable controls |
| Layout components  | Header, Sidebar    | Application structure   |
| Shared components  | EmptyState, Loader | Common application UI   |
| Feature components | ProductCard        | Feature-specific UI     |

### **16.3 Example reusable button**

```
type ButtonProps = {
  children: React.ReactNode;
  variant?: "primary" | "secondary";
  disabled?: boolean;
  onClick?: () => void;
};

export default function Button({
  children,
  variant = "primary",
  disabled = false,
  onClick,
}: ButtonProps) {
  return (
    <button
      className={`button button-${variant}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

Usage:

```
<Button variant="primary">
  Save
</Button>
```

```
<Button variant="secondary">
  Cancel
</Button>
```

Now the button behavior and styling can remain consistent across the application.

## **17. Environment-aware application architecture**

Production applications often have different environments.

| Environment       | Purpose                             |
| ----------------- | ----------------------------------- |
| Development       | Local development                   |
| Test              | Automated testing                   |
| Preview / Staging | Validate releases before production |
| Production        | Real users                          |

Your application should separate environment-specific configuration from business logic.

### **Example structure**

```
src/
│
├── config/
│   ├── env.ts
│   ├── app.ts
│   └── database.ts
│
└── lib/
    └── database/
        └── mongodb.ts
```

Example environment validation:

```
// config/env.ts

import "server-only";

import { z } from "zod";

const envSchema = z.object({
  MONGODB_URI: z.string().min(1),
  JWT_SECRET: z.string().min(32),
  NODE_ENV: z.enum([
    "development",
    "test",
    "production",
  ]),
});

export const env = envSchema.parse(process.env);
```

Now your application can use:

```
import { env } from "@/config/env";

console.log(env.NODE_ENV);
```

In actual production code, avoid logging secret values.

### **Why validate environment variables?**

Without validation, a missing environment variable might cause an unexpected error much later in the application.

With validation, the application can fail early with a clear configuration error.

## **18. Scalability and maintainability principles**

As an application grows, you should follow a few important engineering principles.

### **18.1 Single Responsibility Principle (SRP)**

A module should have one primary responsibility.

Bad:

```
// One module does everything:
createUser();
sendEmail();
createSession();
generateInvoice();
updateInventory();
```

Better:

```
User Service
Email Service
Session Service
Invoice Service
Inventory Service
```

### **18.2 Don't Repeat Yourself (DRY)**

Avoid duplicating the same logic unnecessarily.

For example, if multiple pages need the same price formatting function, create a shared utility.

```
export function formatPrice(amount: number) {
  return new Intl.NumberFormat("en-IN", {
    style: "currency",
    currency: "INR",
  }).format(amount);
}
```

### **18.3 Keep Client Components small**

Avoid marking an entire application section as a Client Component when only one small interaction needs browser-side state.

Prefer:

```
Server Page
    |
    ├── Server Data
    |
    └── Small Client Component
```

This helps keep client-side JavaScript under control.

### **18.4 Keep business logic outside page components**

Instead of putting complicated operations directly inside `page.tsx`, place them in services or use-case modules.

### **18.5 Enforce server/client boundaries**

Use:

```
import "server-only";
```

for server-only modules.

Avoid importing database models or private configuration into Client Components.

### **18.6 Keep modules loosely coupled**

A feature should depend on a small, clear public interface rather than importing random internal files from another feature.

### **18.7 Avoid oversized components**

If a component has too many unrelated responsibilities, divide it into smaller components.

### **18.8 Avoid unnecessary global state**

Use local component state for local interactions. Use shared state only when multiple parts of the application genuinely need it.

For server data, consider whether fetching and caching through Next.js or a dedicated data-fetching library is more appropriate than putting everything into a global client store.

## **19. Example: Complete scalable e-commerce architecture**

Let's combine the concepts into one practical architecture.

## **E-commerce application architecture**

**Next.js App Router**

Routes, layouts, loading states, error boundaries

**Feature modules**

Auth, Products, Cart, Orders, Payments

**Shared UI**

Buttons, inputs, modals, navigation

**Application services and DAL**

Business rules, authorization, data access

**Infrastructure**

MongoDB, MySQL, payment provider, email, file storage

### **19.1 Recommended folder structure**

```
src/
│
├── app/
│   │
│   ├── (store)/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── products/
│   │   ├── cart/
│   │   └── checkout/
│   │
│   ├── (auth)/
│   │   ├── layout.tsx
│   │   ├── login/
│   │   └── register/
│   │
│   ├── admin/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── products/
│   │   └── orders/
│   │
│   ├── api/
│   │   ├── products/
│   │   └── orders/
│   │
│   ├── layout.tsx
│   └── globals.css
│
├── features/
│   │
│   ├── auth/
│   │   ├── components/
│   │   ├── services/
│   │   ├── schemas/
│   │   └── types/
│   │
│   ├── products/
│   │   ├── components/
│   │   ├── services/
│   │   ├── repositories/
│   │   ├── schemas/
│   │   └── types/
│   │
│   ├── cart/
│   │   ├── components/
│   │   ├── services/
│   │   └── types/
│   │
│   ├── orders/
│   │   ├── components/
│   │   ├── services/
│   │   ├── repositories/
│   │   └── types/
│   │
│   └── payments/
│       ├── services/
│       ├── providers/
│       └── types/
│
├── components/
│   ├── ui/
│   ├── layout/
│   └── shared/
│
├── lib/
│   ├── database/
│   ├── dal/
│   ├── security/
│   ├── cache/
│   └── utils/
│
├── hooks/
│
├── config/
│
└── types/
```

### **19.2 Understand the responsibility boundaries**

| Directory            | Responsibility                        |
| -------------------- | ------------------------------------- |
| `app/`               | Routing and page composition          |
| `features/`          | Business functionality                |
| `components/ui/`     | Reusable basic UI                     |
| `components/layout/` | Shared application layouts            |
| `lib/database/`      | Database connection and configuration |
| `lib/dal/`           | Secure data access                    |
| `lib/security/`      | Authentication and security utilities |
| `config/`            | Application configuration             |
| `hooks/`             | Reusable client-side hooks            |
| `types/`             | Shared TypeScript types               |

This is a reference architecture, not a mandatory Next.js convention. Adjust it based on application size and team needs.

## **20. Common architecture mistakes**

These are mistakes you should avoid when building large Next.js applications.

| Mistake                                | Problem                                 | Better approach                     |
| -------------------------------------- | --------------------------------------- | ----------------------------------- |
| Putting everything in `app/`           | Routing and business logic become mixed | Use feature modules                 |
| One giant component                    | Difficult to maintain and test          | Split by responsibility             |
| Excessive `"use client"`               | Increases client-side JavaScript        | Keep client boundaries small        |
| Database queries inside UI components  | Tight coupling                          | Use server-side services or DAL     |
| Duplicated business logic              | Inconsistent behavior                   | Centralize shared use cases         |
| Importing server code into client code | Security and build problems             | Enforce server-only boundaries      |
| Overengineering small features         | Unnecessary complexity                  | Add abstraction when needed         |
| Using global state for everything      | Difficult state management              | Choose state ownership carefully    |
| Ignoring authorization in services     | Potential security vulnerabilities      | Verify permissions on the server    |
| Creating too many tiny files           | Harder navigation without real benefit  | Group related logic sensibly        |
| Conflicting route groups               | Duplicate URL errors                    | Keep URL mappings unique            |
| Missing Parallel Route defaults        | Next.js 16 build failures               | Provide `default.tsx` for each slot |

### **Important distinction**

**Good architecture does not mean having the maximum number of folders, classes, services, and interfaces.**

Good architecture means:

- Clear responsibilities.
- Controlled dependencies.
- Maintainable code.
- Testable modules.
- Secure server/client boundaries.
- An organization that matches the application's actual complexity.

## **21. Interview questions and answers**

### **Basic questions**

**Q1. What is application architecture?**

Application architecture describes how an application's modules, components, services, and infrastructure are organized and how they interact.

**Q2. What are Route Groups in Next.js?**

Route Groups are folders wrapped in parentheses that organize routes without adding the group name to the URL.

**Q3. What are Parallel Routes?**

Parallel Routes allow multiple independently navigable route sections to render within the same layout using named slots.

**Q4. What are Intercepting Routes?**

Intercepting Routes allow Next.js to display a route within the current layout instead of performing a complete page transition. They are commonly used for URL-addressable modals.

**Q5. What is a private folder?**

A private folder begins with an underscore and is excluded from routing. It is useful for colocating route-specific components and utilities.

**Q6. What is colocation?**

Colocation means keeping related files close to each other, such as a page, its components, helpers, and tests.

### **Intermediate questions**

**Q7. What is feature-based architecture?**

Feature-based architecture organizes code according to business functionality, such as authentication, products, orders, and payments.

**Q8. What is separation of concerns?**

It is a software design principle in which different modules have distinct responsibilities.

**Q9. What is a repository pattern?**

The repository pattern separates data access operations from the business logic that uses them.

**Q10. What is a service layer?**

A service layer contains application use cases and business operations, such as creating an order or registering a user.

**Q11. What is a Data Access Layer?**

A DAL centralizes data access and can enforce authentication and authorization before returning sensitive data.

**Q12. Why should database logic not be placed directly in Client Components?**

Client Components execute in the browser and cannot safely access server-only resources such as database credentials. Database operations should be performed on the server.

**Q13. What is the difference between a Server Component and a Client Component?**

Server Components execute as part of server rendering and can access server-side resources. Client Components support browser-side interactivity, state, and event handlers.

**Q14. What is a design system?**

A design system is a collection of reusable UI components, design rules, and conventions that maintain consistency across an application.

### **Advanced questions**

**Q15. What is the difference between Parallel Routes and Intercepting Routes?**

Parallel Routes render multiple route slots within a shared layout. Intercepting Routes display a route within the current navigation context, commonly as a modal.

**Q16. Why do Parallel Routes need&#x20;**`default.tsx`**&#x20;in Next.js 16?**

Next.js needs a fallback when it cannot recover the active state of a slot during a hard navigation or refresh. Next.js 16 requires explicit default files for Parallel Route slots.

**Q17. Do&#x20;**`@slot`**&#x20;folders affect URL paths?**

No. They define named slots passed to a parent layout and are not URL segments.

**Q18. What is dependency injection?**

Dependency injection is a technique where a module receives its dependencies from outside rather than constructing or selecting them internally.

**Q19. What is tight coupling?**

Tight coupling occurs when one module depends heavily on the specific implementation details of another module, making changes and testing more difficult.

**Q20. What is the adapter pattern?**

The adapter pattern provides a compatible interface between an application and an external or different implementation.

**Q21. What is the strategy pattern?**

The strategy pattern allows different interchangeable algorithms or behaviors to be selected through a common interface.

**Q22. Why use&#x20;**`server-only`**?**

It helps prevent server-only modules from being imported into Client Component module graphs, protecting server-side code boundaries.

**Q23. Why should authorization be implemented in the DAL or server-side service?**

Because UI visibility and route-level checks are not sufficient security boundaries. Every sensitive data access or mutation must enforce the required permissions on the server.

**Q24. What is the benefit of feature-based architecture over a purely layer-based architecture?**

Feature-based architecture keeps related business functionality together, which can improve navigation, maintainability, and team ownership in large applications.

**Q25. Is repository pattern mandatory in Next.js?**

No. It is an architectural choice. Use it when it meaningfully separates data access from business logic or improves testability.

## **22. Practical assignments**

Use your existing Next.js knowledge to complete these projects.

### **Assignment 1 – Route organization**

Create an application with these sections:

- Marketing website.
- Authentication pages.
- User dashboard.
- Admin dashboard.
- Product management.
- Order management.

Use Route Groups and nested layouts.

### **Assignment 2 – Parallel Routes**

Build a dashboard with:

- Main dashboard content.
- Analytics slot.
- Team activity slot.
- Separate loading and error states where appropriate.
- `default.tsx` files for every required slot.

### **Assignment 3 – Intercepting Routes**

Build a photo gallery:

- Create a gallery page.
- Create dynamic photo detail routes.
- Open photos in modals through client-side navigation.
- Make modal URLs shareable.
- Support browser Back to close the modal.
- Support direct navigation to a full photo detail page.
- Test refresh behavior.

### **Assignment 4 – Feature-based architecture**

Create these feature modules:

```
features/
├── auth/
├── products/
├── cart/
└── orders/
```

Each module should contain the appropriate components, services, schemas, and types.

### **Assignment 5 – Separation of concerns**

- Move database queries into repositories or dedicated data-access functions.
- Move business operations into services.
- Add server-only boundaries.
- Add server-side authorization.
- Write unit tests for important services.
- Test the main workflows with Playwright.

### **Assignment 6 – Complete architecture project**

Build a small e-commerce application using:

| Layer          | Technology                   |
| -------------- | ---------------------------- |
| Frontend       | Next.js 16                   |
| Language       | TypeScript                   |
| UI             | React                        |
| Database       | MongoDB                      |
| Validation     | Zod                          |
| Authentication | Session-based authentication |
| Testing        | Vitest + Playwright          |
| Deployment     | Vercel or VPS                |

The application should contain:

- User registration and login.
- Product listing and details.
- Shopping cart.
- Order creation.
- Admin product management.
- Role-based authorization.
- Dashboard with Parallel Routes.
- Product preview using Intercepting Routes.

## **23. Quick revision notes**

| Concept                  | Remember                                             |
| ------------------------ | ---------------------------------------------------- |
| Application architecture | Organization of application modules and interactions |
| Route Groups             | `(folder)` – organization without URL changes        |
| Parallel Routes          | `@folder` – independent route slots                  |
| Intercepting Routes      | `(.)`, `(..)`, `(...)` – intercept navigation        |
| `default.tsx`            | Fallback for Parallel Route slots                    |
| Private folders          | `_folder` – excluded from routing                    |
| Colocation               | Keep related files together                          |
| Feature architecture     | Organize by business capability                      |
| Service layer            | Business use cases                                   |
| Repository pattern       | Data access abstraction                              |
| DAL                      | Centralized secure data access                       |
| Dependency Injection     | Supply dependencies externally                       |
| Adapter pattern          | Connect incompatible interfaces                      |
| Strategy pattern         | Interchangeable algorithms                           |
| Facade pattern           | Simplify complex subsystems                          |
| Separation of concerns   | Distinct responsibilities                            |
| `server-only`            | Protect server-side module boundaries                |
| Design system            | Consistent reusable UI                               |
| Loose coupling           | Reduce implementation dependencies                   |
| SRP                      | Single Responsibility Principle                      |
| DRY                      | Don't Repeat Yourself                                |

### **Important folder naming conventions**

```
(folder)       → Route Group
@folder        → Parallel Route slot
(.)folder      → Intercept at same route level
(..)folder     → Intercept one route level above
(..)(..)folder → Intercept two levels above
(...)folder    → Intercept from app root
_folder        → Private folder
```

## **24. Phase 21 completion checklist**

**Phase 21 progress**

# **0/20**

Understand advanced App Router architecture

Understand Route Groups

Create multiple layout sections

Understand Parallel Routes and slots

Configure default.tsx fallbacks

Understand soft navigation and hard navigation

Understand Intercepting Routes

Understand route interception conventions

Build a URL-addressable modal

Understand nested and multiple root layouts

Understand private folders and colocation

Implement feature-based architecture

Understand separation of concerns

Implement service and repository layers

Understand the Data Access Layer

Compose Server and Client Components

Understand dependency injection

Understand common design patterns

Create reusable design system components

Design a scalable e-commerce folder structure

Reset progress

## **25. What's next?**

Next phase

# **Phase 22 – Advanced Data Fetching, Server-Side Rendering and Rendering Strategies**

We will go deeper into how Next.js prepares and delivers pages to users.

Topics will include:

1. Static Site Generation (SSG).
2. Server-Side Rendering (SSR).
3. Incremental Static Regeneration (ISR).
4. Client-Side Rendering (CSR).
5. Partial Pre-Rendering and streaming.
6. Dynamic and static rendering decisions.
7. Rendering strategies for different page types.
8. Hybrid rendering architecture.
9. Performance trade-offs.
10. Real-world rendering strategy selection.

When you're ready, say **OK, Phase 22**.

### **Official documentation**

- [Parallel Routes](https://nextjs.org/docs/app/api-reference/file-conventions/parallel-routes?utm_source=chatgpt.com)
- [Intercepting Routes](https://nextjs.org/docs/app/api-reference/file-conventions/intercepting-routes?utm_source=chatgpt.com)
- [Route Groups](https://nextjs.org/docs/app/api-reference/file-conventions/route-groups?utm_source=chatgpt.com)
- [Layouts](https://nextjs.org/docs/app/api-reference/file-conventions/layout?utm_source=chatgpt.com)
- [Backend for Frontend](https://nextjs.org/docs/app/guides/backend-for-frontend?utm_source=chatgpt.com)