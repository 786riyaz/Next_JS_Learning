# **Next.js Complete Learning Roadmap – Phase 6**

## **Nested Routes and Layouts**

Phase 6

Intermediate

Next.js 16.x

App Router

TypeScript

## **1. Learning objectives**

In this phase, you will learn how to create reusable layouts for different sections of a Next.js application.

By the end of this phase, you should understand:

- What layouts are and why they are important.
- How root layouts work.
- How nested layouts work.
- How layouts are shared between routes.
- The difference between `layout.tsx`, `page.tsx`, and `template.tsx`.
- How route groups organize layouts.
- How to create multiple root layouts.
- How to build dashboard, authentication, and admin layouts.

# **2. What is a Layout in Next.js?**

A **layout** is a UI component that wraps multiple pages and provides a shared structure.

For example, imagine an e-commerce website.

Every page might have:

- Navbar
- Logo
- Search bar
- Shopping cart
- Footer

Instead of writing this structure separately on every page, you can define it once in a layout.

[HomepageFrame — eCommerce Homepage Design Framework](https://images.openai.com/static-rsc-4/8uCePGHAoadf5i8OE9Py6goSFtSjJQRq6Xf3WxGMzA6khuY4nyBurLUhloBE2Ww2rcU2ReCAXXTOagH7_GATpv2D8OqeSmzdYK97p0GWfrZGUTgeR4vKkniKOGYs4R0eRKbuIzNciTe8pXZLaF5R2k5wscKXvJSf-oU3212KyQE?purpose=inline)

[illustratedheroicseries.com](https://illustratedheroicseries.com/grain-storage.html?utm_source=chatgpt.com)

### **Without layouts**

You might create:

```
// app/about/page.tsx

export default function AboutPage() {
  return (
    <>
      <Navbar />
      <main>
        <h1>About Us</h1>
      </main>
      <Footer />
    </>
  );
}
```

```
// app/contact/page.tsx

export default function ContactPage() {
  return (
    <>
      <Navbar />
      <main>
        <h1>Contact Us</h1>
      </main>
      <Footer />
    </>
  );
}
```

The same Navbar and Footer are repeated.

### **With layouts**

You can move shared components into `layout.tsx`.

```
// app/layout.tsx

import Navbar from "@/components/Navbar";
import Footer from "@/components/Footer";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <Navbar />

        <main>{children}</main>

        <Footer />
      </body>
    </html>
  );
}
```

Now your individual pages only need their own content.

```
// app/about/page.tsx

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

```
// app/contact/page.tsx

export default function ContactPage() {
  return <h1>Contact Us</h1>;
}
```

**Important:** The `children` prop represents the page or nested layout that Next.js renders inside the current layout.

# **3. Understanding the Root Layout**

The root layout is the highest-level layout in the App Router.

Its file name is:

```
app/layout.tsx
```

It is the parent layout for all routes under that `app` directory.

### **Example folder structure**

```
src/
└── app/
    ├── layout.tsx
    ├── page.tsx
    ├── about/
    │   └── page.tsx
    └── contact/
        └── page.tsx
```

### **Root layout code**

```
// src/app/layout.tsx

import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "My Website",
  description: "My Next.js application",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

### **Important rules**

1. The root layout must define `<html>` and `<body>` tags.
2. It is a Server Component by default.
3. It must be present in an App Router application.
4. It can export metadata.
5. It receives `children`.
6. It wraps all pages and nested layouts below it.

**Interview point:** A root layout is not just another reusable component. It defines the document structure of your application.

# **4. Understanding Nested Layouts**

A nested layout is a layout placed inside a route folder.

It applies only to that route segment and its descendants.

For example, consider this structure:

```
app/
├── layout.tsx
├── page.tsx
│
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    │
    ├── users/
    │   └── page.tsx
    │
    └── settings/
        └── page.tsx
```

Here:

- `app/layout.tsx` is the root layout.
- `app/dashboard/layout.tsx` is the dashboard layout.
- The dashboard layout applies to `/dashboard`, `/dashboard/users`, and `/dashboard/settings`.

### **Dashboard layout**

```
// app/dashboard/layout.tsx

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <header>
        <h1>Dashboard Header</h1>
      </header>

      <div className="dashboard-container">
        <aside>
          <nav>
            <a href="/dashboard">Overview</a>
            <a href="/dashboard/users">Users</a>
            <a href="/dashboard/settings">Settings</a>
          </nav>
        </aside>

        <main>{children}</main>
      </div>
    </div>
  );
}
```

### **Dashboard page**

```
// app/dashboard/page.tsx

export default function DashboardPage() {
  return <h2>Dashboard Overview</h2>;
}
```

### **Users page**

```
// app/dashboard/users/page.tsx

export default function UsersPage() {
  return <h2>Users Management</h2>;
}
```

### **Settings page**

```
// app/dashboard/settings/page.tsx

export default function SettingsPage() {
  return <h2>Dashboard Settings</h2>;
}
```

### **How nested layouts render**

**Root Layout**

app/layout.tsx

**Dashboard Layout**

dashboard/layout.tsx

**Dashboard Page**

dashboard/page.tsx

The nested layout is rendered inside the root layout.

For `/dashboard/users`, the hierarchy becomes:

```
RootLayout
  └── DashboardLayout
        └── UsersPage
```

For `/dashboard/settings`:

```
RootLayout
  └── DashboardLayout
        └── SettingsPage
```

The dashboard header and sidebar remain shared across all dashboard pages.

# **5. Layout Hierarchy with Multiple Nested Layouts**

You can nest layouts as deeply as your application requires.

### **Example**

```
app/
├── layout.tsx
│
└── admin/
    ├── layout.tsx
    ├── page.tsx
    │
    └── users/
        ├── layout.tsx
        ├── page.tsx
        │
        └── [id]/
            └── page.tsx
```

This creates three layout levels:

1. Root layout
2. Admin layout
3. Users layout

For the URL `/admin/users/101`, the hierarchy is:

```
RootLayout
  └── AdminLayout
        └── UsersLayout
              └── UserDetailsPage
```

### **Example: Admin layout**

```
// app/admin/layout.tsx

export default function AdminLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <h1>Admin Panel</h1>
      {children}
    </div>
  );
}
```

### **Example: Users layout**

```
// app/admin/users/layout.tsx

export default function UsersLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <section>
      <h2>User Management</h2>
      {children}
    </section>
  );
}
```

The User Details page will be rendered inside both layouts.

# **6. How Layouts Behave During Navigation**

One of the most important characteristics of layouts is that they are preserved during navigation between routes that share them.

Consider:

```
/dashboard
/dashboard/users
/dashboard/settings
```

All these routes share:

```
app/layout.tsx
app/dashboard/layout.tsx
```

When you navigate from `/dashboard` to `/dashboard/users`, Next.js can preserve the shared layout instead of recreating it.

### **Why is this useful?**

Suppose your dashboard layout contains a sidebar with an expanded/collapsed state.

```
"use client";

import { useState } from "react";

export default function DashboardSidebar() {
  const [collapsed, setCollapsed] = useState(false);

  return (
    <aside>
      <button onClick={() => setCollapsed(!collapsed)}>
        Toggle Sidebar
      </button>

      {!collapsed && <nav>Dashboard Navigation</nav>}
    </aside>
  );
}
```

If the sidebar is placed inside a shared dashboard layout, its state can remain intact while navigating between dashboard child routes.

### **Important behavior**

| Behavior                                              | Layout |
| ----------------------------------------------------- | ------ |
| Shared across child routes                            | Yes    |
| Preserved during shared-route navigation              | Yes    |
| Re-rendered as a new layout on every child navigation | No     |
| Can maintain client-side state                        | Yes    |
| Server Component by default                           | Yes    |

**Important:** Do not assume that a layout automatically receives fresh route-specific data on every navigation. Layouts are preserved and may not re-render for every child route transition. Use page-level data fetching or other appropriate patterns for data that must change with the route.

# **7. Difference Between&#x20;**`layout.tsx`**&#x20;and&#x20;**`page.tsx`

Both files are special Next.js file conventions, but their responsibilities are different.

| Feature                            | `layout.tsx`         | `page.tsx`                                   |
| ---------------------------------- | -------------------- | -------------------------------------------- |
| Purpose                            | Shared UI structure  | Route-specific UI                            |
| Required?                          | Root layout required | Required to make a route publicly accessible |
| Receives `children`                | Yes                  | No                                           |
| Shared across child routes         | Yes                  | No                                           |
| Can be nested                      | Yes                  | Not in the same way                          |
| Default component type             | Server Component     | Server Component                             |
| Preserved across shared navigation | Yes                  | Page content changes                         |

### **Example**

```
// app/layout.tsx

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <header>My Website</header>
        {children}
        <footer>Copyright 2026</footer>
      </body>
    </html>
  );
}
```

```
// app/about/page.tsx

export default function AboutPage() {
  return <h1>About Our Company</h1>;
}
```

The layout defines the shared structure. The page defines the content for `/about`.

# **8. Difference Between&#x20;**`layout.tsx`**&#x20;and&#x20;**`template.tsx`

This is an important interview topic.

Both files wrap child content, but they behave differently during navigation.

### **Layout**

A layout preserves its state and remains mounted when navigating between routes that share it.

### **Template**

A template creates a new component instance when its segment is navigated to. This means its state is reset and its effects run again when it remounts.

### **Example folder structure**

```
app/
├── layout.tsx
│
└── dashboard/
    ├── template.tsx
    ├── page.tsx
    └── users/
        └── page.tsx
```

### **Template example**

```
// app/dashboard/template.tsx

"use client";

import { useEffect } from "react";

export default function DashboardTemplate({
  children,
}: {
  children: React.ReactNode;
}) {
  useEffect(() => {
    console.log("Dashboard template mounted");
  }, []);

  return (
    <div className="dashboard-template">
      {children}
    </div>
  );
}
```

### **Layout vs Template**

| Feature                                        | Layout                     | Template                                             |
| ---------------------------------------------- | -------------------------- | ---------------------------------------------------- |
| Preserves state across shared child navigation | Yes                        | No                                                   |
| Preserves component instance                   | Yes                        | No                                                   |
| Remounts on navigation between child routes    | Generally no               | Yes, when its segment is navigated to                |
| Can wrap children                              | Yes                        | Yes                                                  |
| Use case                                       | Persistent sidebar, navbar | Reset state, trigger mount effects, enter animations |

### **When should you use a layout?**

Use layouts for:

- Navigation bars
- Sidebars
- Footers
- Shared dashboards
- Persistent application structure

### **When should you use a template?**

Use templates when you need:

- State to reset during navigation.
- Effects to run again when the segment is entered.
- A new component instance for each navigation.
- Certain enter animations to replay.

**Remember:** Use `layout.tsx` by default. Use `template.tsx` only when you specifically need remounting behavior.

# **9. Route Groups and Layouts**

Route groups allow you to organize routes without changing their URL structure.

A route group is created by placing a folder name inside parentheses.

Example:

```
app/
├── layout.tsx
│
├── (marketing)/
│   ├── layout.tsx
│   ├── about/
│   │   └── page.tsx
│   └── contact/
│       └── page.tsx
│
└── (dashboard)/
    ├── layout.tsx
    └── dashboard/
        └── page.tsx
```

### **Important concept**

The folder names `(marketing)` and `(dashboard)` do not appear in the URL.

| File path                            | URL          |
| ------------------------------------ | ------------ |
| `app/(marketing)/about/page.tsx`     | `/about`     |
| `app/(marketing)/contact/page.tsx`   | `/contact`   |
| `app/(dashboard)/dashboard/page.tsx` | `/dashboard` |

### **Why use route groups?**

Imagine your application has:

- Marketing website
- Dashboard
- Authentication pages
- Admin panel

Each section may need a different layout.

Route groups help organize these sections without adding unnecessary URL segments.

### **Marketing layout**

```
// app/(marketing)/layout.tsx

import Navbar from "@/components/Navbar";
import Footer from "@/components/Footer";

export default function MarketingLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <Navbar />
      <main>{children}</main>
      <Footer />
    </>
  );
}
```

### **Dashboard layout**

```
// app/(dashboard)/layout.tsx

import Sidebar from "@/components/Sidebar";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="flex">
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}
```

Now marketing routes and dashboard routes can have different layouts.

[Marketing company - Website design by Daniel Timofte on Dribbble](https://images.openai.com/static-rsc-4/DE9zWDOksesPeFwAnTSLob1Xzr815p5_25zW53Nmhwik3eRfxa43rqoLAP9CUnInceXkm4L7n4pN9-xYAZAWE4GSi-pAExVUpJcVPCQaypi_9bMny1KAqRuBkLiOLcmsLgMqs8fEElfqR6LyencxPZ9BDTrz0UbNDgYFJBXR8Q8?purpose=inline)

[E-commerce SaaS Dashboard UI Design by Juyed Ahmed on Dribbble](https://images.openai.com/static-rsc-4/DC72A8kjKrEH2RxxAY5ts0JukEiXZ0snYbmDYhooPlXfVoWKjmdf_3RoPt7bDi7wSkbxTrbjJP8rLpYDZjOoHAc_WjFPe289VThn4PnF2PHUIv0DUetWqs7wEbv19uMbVifTWN3wt-hXSk7ZhVwNZM8EDh3-OqsA_o_cdzCREj0?purpose=inline)

[CTA + Footer by Sahil Vhora on Dribbble](https://images.openai.com/static-rsc-4/Gr7-9iQ_5YsjWL8Om_ctP91GeiPOscaE-CWyO_eFTyBEqanGWyzWMtYADL3i4PEISHgX8TmhtF93__7IhlCboO7j2pI39UnLx6Ms_y9SY0bBUGI6GaNZQgyYetJESmN10zDfC1uWtZ61kTLHTllDhBS0_XbiDpLcsyHVPui5caE?purpose=inline)

[Prodexa — Product Analytics & Management Dashboard by Sumunar Design for Sumunar Studio on Dribbble](https://images.openai.com/static-rsc-4/XrFwJfQnGajTAvMLDZpHsDFVS8mhdz7zMIPItKunt6OaC8-NwsCBIGSSxof0cby66_h26RX_xpPlBDCjIHFBkXBX96-7Mz9H1BQ_A08IpZmTLxqx8AOWhzy1jdx1QQrJHQKjSs-iKz3EOwA3wQLiNiRa7Cc8weRi8CI6BoOenqA?purpose=inline)

[AI Marketing Automation Platform — SaaS Website Landing Page by Bhautik Domadiya on Dribbble](https://images.openai.com/static-rsc-4/gjIcwhqpmAOwxWztlyW3MAHybxAgErLXKwu78YSi2dqSrX90iSUYm-AMjgsmztri8FLScaHQVJFSCsjsXxfE69lvQL-MiqRoFnROdVyjpnWgAmxawNHPP_RQlA3UXiArrzqVtnZnXn43ZuOeDQprL9ii1iUxRbD0gojTm_Zo6wU?purpose=inline)

[Smart CRM Analytics Dashboard by Sajibur Rahman on Dribbble](https://images.openai.com/static-rsc-4/XFgWF3P5yvUeRQoGChbPn3tprgdDs8SNCEF-K-ZF6GBfr5vum_4NNXyRJVlZFHqb6LmsFESugKnLBgmp8iEpDpPTQ1YOf8apFc3AiBxNQBudmKUOsYV3oozlrqvhbiX1E0sO_fp4Ln_rV_fPZKHeq06mHnfOzkIaeNHZLOh0XvQ?purpose=inline)

[Use These 20 SaaS Landing Page Examples as a Benchmark](https://images.openai.com/static-rsc-4/jBCdi7dHGi1COlwWy1E_oDqKEYeGf1wHiXeCC1LFJ6fBtG4f-tAEsJIk9q66me6j_B_4J_g7h4p6D7NCm_ksvs6vcZYlLrsNNKNn_zk1L_1NthYQaXJBn8H03hxwd8hFxqaQ5gb2QKoyFxICzN3efLzoE4QdL8XdeYPzGwzuQ_4?purpose=inline)

[Neoxa - React SaaS Dashboard Template](https://images.openai.com/static-rsc-4/OIQCjp9p_RAB6QyXX-0S7eK8hu8CiAaqWRUrprQ0kbgQi1_NENqlRV_aEMvzL5XeAAw2wJ2FjTB7hUbwHwo-tHopg0u2ZqXPgSD5clyi34hECU1Z5AG1Lb2Y3UpqIM7Y0tQg-YT4SRtQdRzK-COxIF6ZtsLZnsFFC2YKkE-AaZw?purpose=inline)

[Saas Landing Page Design by UI/UX RABBI on Dribbble](https://images.openai.com/static-rsc-4/tNovxgoMTHI2j9s7kXYqFdvM2SAy-Zjjr-XHiAznyRR_hphzgvApQZEU7WdEYm4UDoRE9A1SEPQKHPNECa4qIJGhgY4KxCUBH65JovkFSkHj1c9qJ0ho-_1iIdiVAxDemZNaN0EZbafmTTqfXkjoVZoJkiuYXJHFRX-9mmx5vW8?purpose=inline)

[eCommerce Admin Dashboard by Gulraiz Khan on Dribbble](https://images.openai.com/static-rsc-4/liJVPWPF19oxw3dJQ8MJHo0gGfmTK6IrvUOYlnCE2zgDFn_9v46DkU5r-tLTPHfWMTC5f7L1An_TQN8_FLYfUS-rB3oV8kKbJ7Lwz6pAaNoBPYvKAFAobDsoQHHjvhT-FgafMx4oEa5mk9p8Ykn3EKzcwaeadFqmMJ8ODmbcfbY?purpose=inline)

[Productivity SaaS — Website Footer UI by Antara Jain on Dribbble](https://images.openai.com/static-rsc-4/bvp61UbQSaHfpZA2aV3oH32ySG3c0YcXlRdqqEraug9z0RvKlmEdK75DuKUJtCsTaRvl-DUL5_43jr8YixxCuc8C0pRuql29e0x68MhwHECTHWKq_QSP--7uwRE1cUA0R8wPr9mbwf3RYe4K9yVfBU5R6PGPBReiQ7ELOVDrqDI?purpose=inline)

[Devias Kit Pro](https://images.openai.com/static-rsc-4/hCiDmsbBdY38xgqH4Yjf9lIkgZlVeVPflUeyMu0mEccxmsxvdtfuKzjmhMrjafGNGB2EteS4Ka_zKIE0CgNYgfPraev_CPzgUxvvpwhyUOzs1f6VAlmI4iVRvplpzCoMufp8mQz2HIA11Tfek2ov-OKfm68CZ_v8nW02H1fm3ls?purpose=inline)

# **10. Multiple Root Layouts**

Next.js also supports multiple root layouts.

This is useful when different sections of an application need completely different document structures.

For example:

- Marketing website
- Admin application
- Authentication application

### **Folder structure**

```
app/
│
├── (marketing)/
│   ├── layout.tsx
│   ├── page.tsx
│   └── about/
│       └── page.tsx
│
├── (dashboard)/
│   ├── layout.tsx
│   └── dashboard/
│       └── page.tsx
│
└── (auth)/
    ├── layout.tsx
    ├── login/
    │   └── page.tsx
    └── register/
        └── page.tsx
```

Notice that there is no top-level `app/layout.tsx`.

Each route group contains its own root layout.

### **Marketing root layout**

```
// app/(marketing)/layout.tsx

export default function MarketingRootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className="marketing-theme">
        {children}
      </body>
    </html>
  );
}
```

### **Dashboard root layout**

```
// app/(dashboard)/layout.tsx

export default function DashboardRootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className="dashboard-theme">
        {children}
      </body>
    </html>
  );
}
```

### **Important rules**

1. Each root layout must contain `<html>` and `<body>`.
2. You should not have a top-level root layout when using this structure.
3. Each route group can have its own document structure.
4. Navigating between different root layouts causes a full page load.

### **One important distinction**

| Normal nested layouts                               | Multiple root layouts                           |
| --------------------------------------------------- | ----------------------------------------------- |
| Share a root layout                                 | Have separate root layouts                      |
| Can share document structure                        | Can define different document structures        |
| Shared layout navigation preserves common structure | Cross-root navigation causes a full page load   |
| Good for sections within one application shell      | Good for completely separate application shells |

# **11. Layouts and Client Components**

By default, layouts are Server Components.

That means you cannot directly use client-only hooks such as:

- `useState`
- `useEffect`
- `usePathname`
- `useRouter`

inside a Server Component layout.

For example, this is invalid:

```
// app/dashboard/layout.tsx

import { useState } from "react";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const [open, setOpen] = useState(false);

  return <div>{children}</div>;
}
```

It will cause an error because `useState` requires a Client Component.

### **Correct approach: Extract an interactive component**

```
// app/dashboard/components/Sidebar.tsx

"use client";

import { useState } from "react";

export default function Sidebar() {
  const [open, setOpen] = useState(true);

  return (
    <aside>
      <button onClick={() => setOpen(!open)}>
        Toggle
      </button>

      {open && <nav>Dashboard Menu</nav>}
    </aside>
  );
}
```

Now use it inside your Server Component layout.

```
// app/dashboard/layout.tsx

import Sidebar from "./components/Sidebar";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="dashboard">
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}
```

This approach keeps the layout as a Server Component while making only the sidebar interactive.

**Best practice:** Keep layouts as Server Components unless you have a specific reason to make part of them interactive.

# **12. Accessing the Current URL in a Layout**

A common question:

Can we directly access the current pathname inside a layout?

No. A Server Component layout does not receive the current pathname as a prop.

Instead, create a Client Component and use `usePathname()`.

```
// app/components/ActiveNavigation.tsx

"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";

export default function ActiveNavigation() {
  const pathname = usePathname();

  return (
    <nav>
      <Link
        href="/dashboard"
        className={
          pathname === "/dashboard" ? "active" : ""
        }
      >
        Dashboard
      </Link>

      <Link
        href="/dashboard/users"
        className={
          pathname === "/dashboard/users" ? "active" : ""
        }
      >
        Users
      </Link>
    </nav>
  );
}
```

Then use it inside your layout:

```
// app/dashboard/layout.tsx

import ActiveNavigation from "../components/ActiveNavigation";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <ActiveNavigation />
      {children}
    </div>
  );
}
```

### **Why does Next.js work this way?**

Layouts are preserved across navigation. If Next.js passed a changing pathname directly into a persistent Server Component layout, it would create a different lifecycle and data model.

Using a Client Component with `usePathname()` allows the navigation UI to respond to the current URL.

# **13. Layouts and Dynamic Route Parameters**

Layouts can also receive route parameters.

In Next.js 16, `params` is asynchronous and should be awaited.

### **Example structure**

```
app/
└── shop/
    └── [category]/
        ├── layout.tsx
        └── page.tsx
```

### **Layout code**

```
// app/shop/[category]/layout.tsx

export default async function CategoryLayout({
  children,
  params,
}: {
  children: React.ReactNode;
  params: Promise<{ category: string }>;
}) {
  const { category } = await params;

  return (
    <section>
      <header>
        <h1>
          {category.toUpperCase()} Products
        </h1>
      </header>

      {children}
    </section>
  );
}
```

For the URL:

```
/shop/electronics
```

The `category` parameter will be:

```
electronics
```

For:

```
/shop/clothing
```

It will be:

```
clothing
```

**Important:** Layouts are persistent. Do not rely on them to reactively update based on changing route parameters during navigation. If route-specific content must update on every navigation, place that logic in the relevant page or an appropriate Client Component.

# **14. Real-World Example: Complete E-commerce Layout Structure**

Let's combine what you have learned.

Imagine building an e-commerce application with:

- Home page
- About page
- Product listing
- Product details
- Dashboard
- User management
- Login
- Register

### **Folder structure**

```
src/
└── app/
    │
    ├── layout.tsx
    ├── page.tsx
    │
    ├── (marketing)/
    │   ├── layout.tsx
    │   ├── about/
    │   │   └── page.tsx
    │   └── products/
    │       ├── page.tsx
    │       └── [id]/
    │           └── page.tsx
    │
    ├── (auth)/
    │   ├── layout.tsx
    │   ├── login/
    │   │   └── page.tsx
    │   └── register/
    │       └── page.tsx
    │
    └── dashboard/
        ├── layout.tsx
        ├── page.tsx
        ├── users/
        │   └── page.tsx
        └── settings/
            └── page.tsx
```

### **URL mapping**

| File path                            | URL                   |
| ------------------------------------ | --------------------- |
| `app/page.tsx`                       | `/`                   |
| `(marketing)/about/page.tsx`         | `/about`              |
| `(marketing)/products/page.tsx`      | `/products`           |
| `(marketing)/products/[id]/page.tsx` | `/products/123`       |
| `(auth)/login/page.tsx`              | `/login`              |
| `(auth)/register/page.tsx`           | `/register`           |
| `dashboard/page.tsx`                 | `/dashboard`          |
| `dashboard/users/page.tsx`           | `/dashboard/users`    |
| `dashboard/settings/page.tsx`        | `/dashboard/settings` |

The root layout wraps the application. The marketing, authentication, and dashboard layouts provide additional section-specific structure.

# **15. Common Mistakes**

| Mistake                                                               | Correct approach                                             |
| --------------------------------------------------------------------- | ------------------------------------------------------------ |
| Forgetting `<html>` and `<body>` in the root layout                   | Always include both                                          |
| Creating multiple root layouts while keeping a top-level root layout  | Remove the top-level layout when using separate root layouts |
| Using `useState` directly in a Server Component layout                | Extract a Client Component                                   |
| Assuming route groups appear in URLs                                  | Parentheses-based group names are omitted from URLs          |
| Using `layout.tsx` when state must reset on navigation                | Consider `template.tsx`                                      |
| Repeating Navbar and Footer in every page                             | Put shared UI in a layout                                    |
| Assuming layouts rerender on every navigation                         | Understand layout preservation                               |
| Trying to access pathname directly from Server Component layout props | Use `usePathname()` in a Client Component                    |
| Using synchronous `params` in Next.js 16                              | Await `params`                                               |

# **16. Interview Questions and Answers**

## **Basic Level**

**Q1. What is a layout in Next.js?**

A layout is a reusable UI structure that wraps pages and nested layouts. It is used for common elements such as headers, navigation bars, sidebars, and footers.

**Q2. What is the root layout?**

The root layout is the top-level layout of an App Router application. It defines the document structure using `<html>` and `<body>`.

**Q3. What is the purpose of the&#x20;**`children`**&#x20;prop?**

The `children` prop represents the nested page or layout that should be rendered inside the current layout.

**Q4. Can we have nested layouts in Next.js?**

Yes. You can create a `layout.tsx` file inside any route segment. It wraps that segment's page and descendant routes.

**Q5. Are layouts Server Components or Client Components by default?**

Layouts are Server Components by default.

## **Intermediate Level**

**Q6. What is the difference between a layout and a page?**

A layout provides shared UI for multiple routes, while a page represents the unique content of a particular route.

**Q7. What is the difference between&#x20;**`layout.tsx`**&#x20;and&#x20;**`template.tsx`**?**

Layouts preserve their component instance across navigation between shared routes. Templates create a new instance when their segment is navigated to, resetting state and rerunning mount effects.

**Q8. What are route groups?**

Route groups are folders whose names are enclosed in parentheses. They organize routes and allow different layouts without affecting URL paths.

**Q9. Can a route group have its own layout?**

Yes. A route group can have its own `layout.tsx` to provide shared UI to its child routes.

**Q10. Can we create multiple root layouts?**

Yes. Each route group can have its own root layout containing `<html>` and `<body>`. A top-level root layout should not be present in that arrangement.

## **Advanced Level**

**Q11. Why are layouts preserved during navigation?**

Next.js preserves shared layouts to avoid unnecessarily recreating common UI and to maintain state across related routes.

**Q12. Can a Server Component layout access the current pathname?**

Not directly. You can use `usePathname()` inside a Client Component rendered within the layout.

**Q13. What happens when navigating between different root layouts?**

Navigation between different root layouts causes a full page load.

**Q14. Can a layout use React hooks such as&#x20;**`useState`**&#x20;and&#x20;**`useEffect`**?**

Not directly when it is a Server Component. You must make the component a Client Component or extract interactive functionality into a separate Client Component.

**Q15. Should we fetch route-specific data in a layout or a page?**

If the data belongs to a specific route and needs to update with that route, fetching it in the page is usually the better approach. Layouts are better suited to shared data and UI.

# **17. Practical Assignment**



Hands-on project

**Build a Multi-Layout Application**



Create a Next.js application with three main sections:

1. Marketing website
2. Authentication section
3. Admin dashboard

### **Task 1: Create the folder structure**

```
src/
└── app/
    ├── layout.tsx
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
    └── dashboard/
        ├── layout.tsx
        ├── page.tsx
        ├── users/
        │   └── page.tsx
        └── settings/
            └── page.tsx
```

### **Task 2: Marketing layout**

Create:

- Navbar
- Logo
- Home
- About
- Contact
- Footer

All marketing pages should share this layout.

### **Task 3: Authentication layout**

Create:

- Login page
- Register page
- Centered authentication container
- No marketing Navbar or Footer

### **Task 4: Dashboard layout**

Create:

- Sidebar
- Header
- Main content area
- Dashboard navigation

### **Task 5: Test navigation**

Verify:

- `/about` uses the marketing layout.
- `/login` uses the authentication layout.
- `/dashboard` uses the dashboard layout.
- `/dashboard/users` preserves the dashboard sidebar.
- `/dashboard/settings` preserves the dashboard sidebar.

### **Task 6: Test state preservation**

Add a collapsible sidebar using `useState`.

Navigate between dashboard pages and observe whether the sidebar state is preserved.

### **Task 7: Experiment with templates**

Replace the dashboard `layout.tsx` with a `template.tsx` in a separate experiment.

Observe the difference in component mounting and state preservation.

# **18. Quick Revision Notes**

| Concept               | Remember                                               |
| --------------------- | ------------------------------------------------------ |
| Layout                | Shared UI structure                                    |
| Root Layout           | Highest-level layout                                   |
| Nested Layout         | Layout inside another route segment                    |
| `children`            | Nested page or layout content                          |
| Layout persistence    | Shared layouts remain mounted during navigation        |
| Template              | Creates a new component instance on segment navigation |
| Route Group           | Organizes routes without changing URLs                 |
| Multiple Root Layouts | Separate document structures for different sections    |
| Server Component      | Default layout behavior                                |
| Client Component      | Required for client hooks and interactions             |
| `usePathname()`       | Access pathname in a Client Component                  |
| `params`              | Asynchronous in Next.js 16                             |

## **19. Phase 6 Completion Checklist**

**Your progress**

# **0%**

0 of 13 completed

Understand the purpose of layouts

Understand root layouts

Create nested layouts

Understand layout hierarchy

Understand layout persistence

Differentiate layout.tsx and page.tsx

Differentiate layout.tsx and template.tsx

Understand route groups

Create multiple root layouts

Use Client Components inside layouts

Access pathname using usePathname()

Understand dynamic params in layouts

Complete the multi-layout practical assignment

# **20. What's Next? – Phase 7**

Next phase

# **Loading UI and Streaming**

**Topics we will cover:**

1. What is loading UI in Next.js?
2. Understanding `loading.tsx`.
3. How Suspense works in React and Next.js.
4. Streaming and server rendering.
5. Loading boundaries.
6. Nested loading states.
7. Skeleton loaders.
8. `Suspense` with async Server Components.
9. Handling slow data fetching.
10. Real-world loading UI implementation.

When you are ready, say **"OK, Phase 7"** and we will continue with detailed notes and practical examples.