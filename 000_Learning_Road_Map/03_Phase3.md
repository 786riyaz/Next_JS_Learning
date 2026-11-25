# Next.js Complete Learning Roadmap
# Phase 3: Next.js Project Structure and File Conventions

**Level:** Beginner → Intermediate  
**Difficulty:** Easy → Medium  
**Technology:** Next.js 16.x  
**Architecture:** App Router  
**Language:** TypeScript  
**Goal:** Understand every important folder, file, configuration, naming convention, and project organization strategy used in Next.js applications.

---

# 1. Understanding Next.js Project Structure

When you create a Next.js application using:

```bash
npx create-next-app@latest nextjs-learning
```

Next.js generates a predefined folder structure.

Since we selected the following configurations in Phase 2:

- TypeScript
- ESLint
- Tailwind CSS
- App Router
- src directory
- Import Alias (`@/*`)

Our project structure will look approximately like this:

```text
nextjs-learning/
│
├── node_modules/
│
├── public/
│
├── src/
│   │
│   └── app/
│       │
│       ├── favicon.ico
│       ├── globals.css
│       ├── layout.tsx
│       └── page.tsx
│
├── .git/
│
├── .gitignore
├── eslint.config.mjs
├── next-env.d.ts
├── next.config.ts
├── package.json
├── package-lock.json
├── postcss.config.mjs
├── tsconfig.json
└── README.md
```

Your actual structure may contain additional files depending on the Next.js version and installation options.

Let's understand everything one by one.

---

# 2. Understanding Top-Level Directories

A Next.js project generally contains several important directories.

| Directory | Purpose |
|---|---|
| `node_modules/` | Contains installed dependencies |
| `public/` | Contains static assets |
| `src/` | Contains application source code |
| `src/app/` | Contains App Router routes and layouts |
| `.next/` | Contains generated build and development artifacts |
| `.git/` | Contains Git repository information |

Let's understand each directory.

## 2.1 node_modules/

This directory contains all installed npm packages and their dependencies.

Example:

```text
node_modules/
│
├── next/
├── react/
├── react-dom/
├── typescript/
├── eslint/
├── tailwindcss/
└── ...
```

Suppose you install Axios:

```bash
npm install axios
```

npm downloads Axios and its required dependencies into `node_modules`.

Now you can use it:

```typescript
import axios from "axios";
```

### Important Points

1. It contains third-party packages.
2. It can become very large.
3. It is generally excluded from Git.
4. It can be recreated using `npm install`.
5. You should not manually modify its contents.

**Why don't we upload node_modules to GitHub?**

Because dependencies are already described in:

```text
package.json
package-lock.json
```

Other developers can install the dependencies themselves.

---

## 2.2 public/

The `public` directory stores static assets that you want to serve directly.

Examples:

- Images
- Logos
- Icons
- PDFs
- Videos
- Fonts
- Other static files

Example structure:

```text
public/
│
├── images/
│   ├── logo.png
│   ├── banner.jpg
│   └── profile.png
│
├── icons/
│   ├── facebook.svg
│   └── instagram.svg
│
├── documents/
│   └── resume.pdf
│
└── robots.txt
```

### How Do We Access Public Files?

Suppose you have:

```text
public/images/logo.png
```

You can access it through:

```text
/images/logo.png
```

Notice that you do not include `public` in the URL.

### Example: Using an Image

```tsx
export default function Home() {
  return (
    <div>
      <img src="/images/logo.png" alt="Logo" />
    </div>
  );
}
```

You can also use Next.js Image:

```tsx
import Image from "next/image";

export default function Home() {
  return (
    <Image
      src="/images/logo.png"
      alt="Company Logo"
      width={200}
      height={100}
    />
  );
}
```

### Important Rules

- Files inside `public` are accessible through URLs.
- The `public` folder must remain at the project root, even when using `src`.
- Do not put private files or secrets inside `public`.
- Files in `public` are not automatically optimized just because they are stored there.

**Example:**

| File Location | URL |
|---|---|
| `public/logo.png` | `/logo.png` |
| `public/images/banner.jpg` | `/images/banner.jpg` |
| `public/icons/home.svg` | `/icons/home.svg` |

---

## 2.3 src/

The `src` directory is an optional directory used to organize application source code.

Without `src`:

```text
project/
│
├── app/
├── components/
├── lib/
├── public/
└── package.json
```

With `src`:

```text
project/
│
├── src/
│   ├── app/
│   ├── components/
│   ├── lib/
│   └── hooks/
│
├── public/
├── package.json
└── next.config.ts
```

### Why Do We Use src?

It separates application code from configuration files.

For example:

**Application code:**

```text
src/
├── app/
├── components/
├── hooks/
├── lib/
└── utils/
```

**Configuration files:**

```text
next.config.ts
tsconfig.json
package.json
eslint.config.mjs
```

### Important Rule

Next.js supports either:

```text
app/
```

or:

```text
src/app/
```

If both root-level `app` and `src/app` exist, the root-level `app` takes precedence and `src/app` is ignored.

Keep one routing directory.

Official documentation: [Next.js src Folder](https://nextjs.org/docs/app/api-reference/file-conventions/src-folder?utm_source=chatgpt.com)

---

## 2.4 .next/

The `.next` directory is generated by Next.js.

It contains build artifacts and development-related generated files.

Example:

```text
.next/
│
├── cache/
├── server/
├── static/
└── ...
```

The exact contents depend on the Next.js version and build configuration.

### When Is It Created?

When you execute:

```bash
npm run dev
```

or:

```bash
npm run build
```

Next.js creates or updates the `.next` directory.

### Why Is It Important?

It contains generated information required by the framework.

For example:

- Compiled application output.
- Generated server files.
- Static assets.
- Build manifests.
- Cache data.

### Can We Delete .next?

Yes.

You can delete it when troubleshooting corrupted or stale build artifacts.

For example:

```bash
rmdir /s /q .next
```

Then restart:

```bash
npm run dev
```

Next.js will regenerate the required files.

**Important:** Never manually modify files inside `.next`.

---

# 3. Understanding the app Directory

The `app` directory is the most important directory in modern Next.js applications.

It contains:

- Pages
- Layouts
- Loading UI
- Error UI
- Route handlers
- Nested routes
- Components
- Route-specific logic

Example:

```text
src/
│
└── app/
    │
    ├── layout.tsx
    ├── page.tsx
    ├── globals.css
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

The `app` directory follows a file-system-based routing approach.

That means folders represent URL segments.

For example:

```text
app/
│
├── page.tsx
│
├── about/
│   └── page.tsx
│
└── products/
    └── page.tsx
```

Automatically creates:

| File | URL |
|---|---|
| `app/page.tsx` | `/` |
| `app/about/page.tsx` | `/about` |
| `app/products/page.tsx` | `/products` |

We will study routing in detail in Phase 4.

---

# 4. Understanding Important App Router Files

Next.js provides special file conventions.

These files have predefined meanings.

Let's understand the most important ones.

## 4.1 page.tsx

The `page.tsx` file defines the UI for a particular route.

Example:

```text
app/
│
└── page.tsx
```

Code:

```tsx
export default function HomePage() {
  return (
    <main>
      <h1>Welcome to Next.js</h1>
    </main>
  );
}
```

This creates:

```text
/
```

Another example:

```text
app/
│
└── about/
    └── page.tsx
```

Code:

```tsx
export default function AboutPage() {
  return (
    <h1>About Us</h1>
  );
}
```

This creates:

```text
/about
```

### Important Points

1. A page must have a default-exported React component.
2. It defines the UI for its route.
3. Pages are Server Components by default.
4. A folder alone does not make a route publicly accessible.

**Remember:** `page.tsx` makes a route segment publicly accessible.

---

## 4.2 layout.tsx

The `layout.tsx` file defines shared UI for multiple pages.

Suppose your application has:

- Homepage
- About page
- Contact page
- Products page

All pages need:

- Header
- Navigation
- Footer

Instead of writing the same code on every page, we can create a shared layout.

Example structure:

```text
app/
│
├── layout.tsx
├── page.tsx
│
├── about/
│   └── page.tsx
│
└── contact/
    └── page.tsx
```

Code:

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <header>
          <h1>My Website</h1>
        </header>

        <main>{children}</main>

        <footer>
          <p>Copyright 2026</p>
        </footer>
      </body>
    </html>
  );
}
```

### What is children?

`children` is a React prop containing the nested content passed into a component.

For example:

```tsx
<Layout>
  <HomePage />
</Layout>
```

Conceptually, `HomePage` becomes the layout's `children`.

### Why Do We Use Layouts?

Without layouts:

```text
Home Page
├── Header
├── Home Content
└── Footer

About Page
├── Header
├── About Content
└── Footer

Contact Page
├── Header
├── Contact Content
└── Footer
```

With layouts:

```text
Root Layout
│
├── Header
│
├── Page Content
│
└── Footer
```

This improves code reuse and maintainability.

### Important Points

- Every App Router application must have a root layout.
- Root layout must define `<html>` and `<body>`.
- Layouts can be nested.
- Layouts preserve state and remain interactive during navigation.
- Layouts do not receive the current URL as a prop.

We will study layouts deeply in Phase 6.

---

## 4.3 loading.tsx

The `loading.tsx` file defines a loading UI for a route segment.

Example:

```text
app/
│
├── products/
│   ├── loading.tsx
│   └── page.tsx
```

Code:

```tsx
export default function Loading() {
  return (
    <div>
      <h2>Loading products...</h2>
    </div>
  );
}
```

When the route is loading, Next.js can display this UI.

### Real-World Example

Imagine a product listing page that needs to fetch thousands of records.

Instead of leaving the user staring at an empty screen, you can show:

```text
Loading products...
```

You can also create skeleton screens.

We will cover this in Phase 8.

---

## 4.4 error.tsx

The `error.tsx` file defines an error boundary UI for a route segment.

Example:

```text
app/
│
└── products/
    ├── error.tsx
    └── page.tsx
```

Code:

```tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>

      <button onClick={() => reset()}>
        Try Again
      </button>
    </div>
  );
}
```

### Important Points

- `error.tsx` must be a Client Component.
- It handles errors in its route segment and descendants.
- It provides a reset function to attempt rendering again.
- It does not catch errors thrown by the layout in the same segment.

For root layout errors, Next.js provides `global-error.tsx`.

We will study error handling in Phase 8.

---

## 4.5 not-found.tsx

The `not-found.tsx` file defines a custom not-found UI.

Example:

```text
app/
│
├── not-found.tsx
└── page.tsx
```

Code:

```tsx
export default function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>

      <p>The requested page does not exist.</p>
    </div>
  );
}
```

You can also trigger this UI programmatically using:

```tsx
import { notFound } from "next/navigation";

export default function ProductPage() {
  const product = null;

  if (!product) {
    notFound();
  }

  return <h1>Product Details</h1>;
}
```

---

## 4.6 template.tsx

A template is similar to a layout, but it has different lifecycle behavior.

Example:

```text
app/
│
├── layout.tsx
└── template.tsx
```

### Layout vs Template

| Layout | Template |
|---|---|
| Preserves state across navigation | Creates a new instance when its segment is activated |
| Does not normally remount on navigation between child routes | Remounts when navigation activates it |
| Useful for shared persistent UI | Useful when state should reset |
| Can preserve interactive state | Creates new DOM elements and re-synchronizes effects |

Example use cases:

- Layout: Persistent navigation bar.
- Template: Page transition or route-specific state reset.

We will explore this later.

---

## 4.7 route.ts

The `route.ts` file is used to create backend API endpoints.

Example:

```text
app/
│
└── api/
    └── users/
        └── route.ts
```

Code:

```typescript
import { NextResponse } from "next/server";

export async function GET() {
  return NextResponse.json({
    message: "Users API",
  });
}
```

This creates an endpoint:

```text
/api/users
```

You can also define:

```typescript
export async function POST(request: Request) {
  const body = await request.json();

  return NextResponse.json({
    message: "User created",
    data: body,
  });
}
```

Supported HTTP methods include:

- GET
- POST
- PUT
- PATCH
- DELETE
- HEAD
- OPTIONS

We will study API development in Phase 24.

---

# 5. Complete App Router Special File Reference

Here is a useful reference table.

| File | Purpose |
|---|---|
| `page.tsx` | Defines route UI |
| `layout.tsx` | Defines shared layout |
| `template.tsx` | Defines remounting layout-like UI |
| `loading.tsx` | Defines loading UI |
| `error.tsx` | Defines route error boundary |
| `global-error.tsx` | Defines global error boundary UI |
| `not-found.tsx` | Defines not-found UI |
| `default.tsx` | Defines fallback UI for parallel routes |
| `route.ts` | Defines API endpoint |
| `middleware.ts` | Older request middleware convention |
| `proxy.ts` | Modern request proxy convention in Next.js 16 |

Additional conventions include metadata and image files, which we will study in the SEO phase.

Official reference: [Next.js File Conventions](https://github.com/vercel/next.js/blob/canary/docs/01-app/01-getting-started/02-project-structure.mdx?utm_source=chatgpt.com)

---

# 6. Understanding Configuration Files

Now let's understand the files available at the root of the project.

These files configure how your application works.

## 6.1 package.json

We already studied this in Phase 2.

It contains:

- Project name.
- Version.
- Dependencies.
- Development dependencies.
- npm scripts.

Example:

```json
{
  "name": "nextjs-learning",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint"
  }
}
```

---

## 6.2 package-lock.json

This file records the resolved dependency tree.

It helps ensure consistent installations across different environments.

Example:

```text
package.json
package-lock.json
```

Both files should generally be committed to Git.

---

## 6.3 next.config.ts

This is the primary configuration file for Next.js.

Example:

```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  /* configuration options */
};

export default nextConfig;
```

You can configure many things here.

Examples:

- Image optimization.
- Redirects.
- Rewrites.
- Headers.
- Experimental features.
- Build configuration.

### Example: Image Configuration

Suppose you want to display images from an external domain.

You can configure:

```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "images.unsplash.com",
      },
    ],
  },
};

export default nextConfig;
```

Now Next.js Image can use supported image URLs from that domain.

**Important:** Configuration changes generally require restarting the development server.

---

## 6.4 tsconfig.json

This file configures TypeScript.

Example:

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "strict": true,
    "noEmit": true,
    "moduleResolution": "bundler",
    "jsx": "preserve",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### Important Properties

| Property | Purpose |
|---|---|
| `target` | JavaScript output language target |
| `strict` | Enables stricter type checking |
| `noEmit` | Prevents TypeScript from emitting JavaScript files |
| `moduleResolution` | Controls module resolution |
| `jsx` | Controls JSX processing |
| `paths` | Configures import aliases |

### Understanding Import Alias

Without alias:

```tsx
import Header from "../../../components/Header";
```

With alias:

```tsx
import Header from "@/components/Header";
```

The `@/*` alias makes imports easier to maintain.

---

## 6.5 eslint.config.mjs

ESLint is a JavaScript and TypeScript linting tool.

It identifies potential problems and enforces coding rules.

Example:

```javascript
import { defineConfig } from "eslint/config";
import nextVitals from "eslint-config-next/core-web-vitals";

export default defineConfig([
  ...nextVitals,
]);
```

The actual generated configuration may contain additional settings.

### What Does ESLint Help With?

- Detecting unused variables.
- Identifying potential bugs.
- Maintaining coding consistency.
- Detecting certain React issues.
- Enforcing project rules.

Run:

```bash
npm run lint
```

This executes the ESLint script configured in package.json.

---

## 6.6 postcss.config.mjs

PostCSS is a CSS processing tool.

It allows CSS transformation through plugins.

For example:

- Tailwind CSS processing.
- CSS transformations.
- Vendor prefixing through suitable plugins.

A Tailwind CSS setup using the current PostCSS plugin can look like:

```javascript
const config = {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};

export default config;
```

The exact configuration depends on the Tailwind CSS version.

---

## 6.7 next-env.d.ts

This is a TypeScript declaration file generated and maintained by Next.js.

It provides TypeScript with framework-related type information.

Example:

```typescript
/// <reference types="next" />
/// <reference types="next/image-types/global" />
```

Its generated contents can vary.

**Important:** Do not manually edit this file.

It should generally not be committed to Git.

---

## 6.8 .gitignore

This file specifies which files and directories Git should ignore.

Example:

```text
node_modules/
.next/
out/
.env*
!.env.example
```

### Why Do We Need .gitignore?

Suppose you have:

```text
node_modules/
.next/
.env.local
```

These files should generally not be committed.

Reasons:

- `node_modules`: Large dependency directory.
- `.next`: Generated build output.
- `.env.local`: May contain private credentials.

### Important

Never commit private API keys, database credentials, or secret tokens to a public repository.

---

## 6.9 README.md

README.md contains documentation about your project.

It usually includes:

- Project introduction.
- Installation instructions.
- Technologies used.
- Development commands.
- Environment configuration.
- Deployment instructions.

Example:

```markdown
# Next.js Learning

This project is created to learn Next.js.

## Installation

npm install

## Development

npm run dev

## Production Build

npm run build
```

A good README helps other developers understand your project.

---

# 7. Understanding Environment Files

Environment files store configuration values that change between environments.

For example:

- Database URL.
- API keys.
- Authentication secrets.
- External service credentials.

Next.js supports several environment files.

| File | Purpose |
|---|---|
| `.env` | General environment variables |
| `.env.local` | Local environment variables |
| `.env.development` | Development environment variables |
| `.env.production` | Production environment variables |
| `.env.test` | Test environment variables |

### Example: .env.local

```env
DATABASE_URL=mongodb://localhost:27017/mydb

JWT_SECRET=my-secret-key

API_KEY=123456
```

Access them in server-side code:

```typescript
const databaseUrl = process.env.DATABASE_URL;
```

### What is NEXT_PUBLIC_?

By default, environment variables are intended for server-side use.

If you want to expose a variable to browser-side JavaScript, Next.js supports the `NEXT_PUBLIC_` prefix.

Example:

```env
NEXT_PUBLIC_API_URL=https://api.example.com
```

Access:

```typescript
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

### Important Security Rule

Never do this:

```env
NEXT_PUBLIC_DATABASE_PASSWORD=my-password
```

Why?

Because variables prefixed with `NEXT_PUBLIC_` can be included in the browser bundle.

Anyone can inspect the client-side JavaScript and discover the exposed value.

**Rule:**

- Private secrets → Server-side environment variables.
- Public configuration → `NEXT_PUBLIC_` variables.

Also remember that `NEXT_PUBLIC_` values are generally inlined into browser JavaScript during the build. Changing the environment variable on a server after building does not necessarily change the already-built client bundle.

---

# 8. Understanding File Naming Conventions

Next.js uses special naming conventions for certain files.

For example:

```text
page.tsx
layout.tsx
loading.tsx
error.tsx
not-found.tsx
route.ts
```

These names have framework-defined behavior.

You cannot arbitrarily rename them if you want their special functionality.

### Example

Correct:

```text
app/
└── about/
    └── page.tsx
```

Creates:

```text
/about
```

Incorrect for a route:

```text
app/
└── about/
    └── about.tsx
```

This file does not automatically create the `/about` route.

You need `page.tsx` to make the route accessible.

However, `about.tsx` can be a regular component file.

### Regular Component Files

You can name your own components however you prefer.

Examples:

```text
Header.tsx
Footer.tsx
Navbar.tsx
ProductCard.tsx
LoginForm.tsx
```

These are normal React component files, not Next.js special files.

---

# 9. Understanding Route Segments

Every nested folder inside `app` represents a route segment.

Example:

```text
app/
│
├── page.tsx
│
├── products/
│   ├── page.tsx
│   │
│   └── electronics/
│       └── page.tsx
│
└── about/
    └── page.tsx
```

Generated URLs:

| Folder Structure | URL |
|---|---|
| `app/page.tsx` | `/` |
| `app/about/page.tsx` | `/about` |
| `app/products/page.tsx` | `/products` |
| `app/products/electronics/page.tsx` | `/products/electronics` |

### Important Concept

A folder does not automatically become a publicly accessible page.

It needs a special file such as:

```text
page.tsx
```

or:

```text
route.ts
```

A `route.ts` creates an HTTP endpoint rather than a UI page.

We will study routing deeply in Phase 4.

---

# 10. Understanding Route Groups

Route groups allow you to organize routes without changing their URLs.

They use parentheses.

Example:

```text
app/
│
├── (marketing)/
│   ├── about/
│   │   └── page.tsx
│   │
│   └── contact/
│       └── page.tsx
│
└── (dashboard)/
    └── dashboard/
        └── page.tsx
```

Generated URLs:

```text
/about
/contact
/dashboard
```

Notice that:

```text
(marketing)
(dashboard)
```

Do not appear in the URL.

Route groups are useful for organizing large applications.

We will explore them in Phase 18.

---

# 11. Understanding Private Folders

Private folders are prefixed with an underscore.

Example:

```text
app/
│
├── _components/
│   ├── Header.tsx
│   └── Footer.tsx
│
├── page.tsx
└── about/
    └── page.tsx
```

The `_components` folder is excluded from routing.

### Why Do We Use Private Folders?

- Organizing internal components.
- Separating implementation details.
- Avoiding accidental route interpretation.
- Keeping route structure clean.

**Important:** App Router allows safe colocation of regular files inside route folders by default. Private folders are optional organizational tools, not mandatory for every component folder.

---

# 12. Understanding Colocation

Colocation means keeping related files close to the feature or route where they are used.

Suppose you have a product page.

You may organize it like this:

```text
app/
│
└── products/
    │
    ├── page.tsx
    ├── loading.tsx
    ├── error.tsx
    │
    ├── components/
    │   ├── ProductCard.tsx
    │   └── ProductList.tsx
    │
    └── utils/
        └── productHelpers.ts
```

All product-related files are located together.

### Advantages

- Easier maintenance.
- Better feature organization.
- Easier debugging.
- Reduced unnecessary navigation between folders.
- Better separation of application features.

### Important

Regular files inside `app` do not automatically become routes.

For example:

```text
app/products/components/ProductCard.tsx
```

does not create:

```text
/products/components/ProductCard
```

Only appropriate special route files create routes or endpoints.

---

# 13. Common Project Organization Strategies

There is no single mandatory folder structure for every Next.js project.

However, there are three popular approaches.

## Strategy 1: Keep Shared Code Outside app

```text
src/
│
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   └── about/
│       └── page.tsx
│
├── components/
│   ├── Header.tsx
│   ├── Footer.tsx
│   └── Button.tsx
│
├── hooks/
│   └── useAuth.ts
│
├── lib/
│   ├── db.ts
│   └── api.ts
│
├── utils/
│   └── helpers.ts
│
└── types/
    └── user.ts
```

### Advantages

- Clean separation between routing and shared application code.
- Easy to understand.
- Suitable for small and medium applications.

## Strategy 2: Keep Components Inside app

```text
src/
│
└── app/
    │
    ├── components/
    │   ├── Header.tsx
    │   └── Footer.tsx
    │
    ├── about/
    │   └── page.tsx
    │
    ├── products/
    │   └── page.tsx
    │
    ├── layout.tsx
    └── page.tsx
```

### Advantages

- Application code stays inside one directory.
- Simple for smaller applications.
- Easy to colocate route-specific files.

## Strategy 3: Feature-Based Architecture

This approach organizes the application around business features.

Example:

```text
src/
│
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── login/
│   │   └── page.tsx
│   └── dashboard/
│       └── page.tsx
│
├── features/
│   │
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── types/
│   │
│   ├── products/
│   │   ├── components/
│   │   ├── services/
│   │   ├── hooks/
│   │   └── types/
│   │
│   └── users/
│       ├── components/
│       ├── services/
│       └── types/
│
├── components/
│   ├── ui/
│   └── shared/
│
├── lib/
│   ├── db.ts
│   └── auth.ts
│
└── utils/
```

### Why Is Feature-Based Architecture Useful?

Suppose you are developing an e-commerce application.

You have different features:

- Authentication
- Products
- Orders
- Payments
- Users
- Reviews

Each feature maintains its own related components, hooks, services, and types.

This makes large applications easier to maintain.

### My Recommendation

For learning:

Start with Strategy 1.

For larger production applications:

Understand Strategy 3 and use it when the project's complexity justifies it.

---

# 14. Understanding Commonly Used Custom Folders

Next.js does not force you to create these folders.

They are conventional organizational choices.

| Folder | Purpose |
|---|---|
| `components/` | Reusable React components |
| `features/` | Feature-specific application code |
| `hooks/` | Custom React hooks |
| `lib/` | Libraries and shared infrastructure |
| `utils/` | Helper functions |
| `services/` | API and business service functions |
| `types/` | TypeScript types and interfaces |
| `constants/` | Constant values |
| `config/` | Application configuration |
| `styles/` | CSS and styling files |
| `context/` | React Context providers |
| `store/` | State management |
| `validators/` | Input validation schemas |
| `tests/` | Test files |

### Example

Suppose you have a reusable button.

```text
src/
└── components/
    └── Button.tsx
```

Code:

```tsx
type ButtonProps = {
  title: string;
};

export default function Button({
  title,
}: ButtonProps) {
  return (
    <button>
      {title}
    </button>
  );
}
```

You can use it in different pages:

```tsx
import Button from "@/components/Button";

export default function Home() {
  return (
    <div>
      <Button title="Login" />
      <Button title="Register" />
    </div>
  );
}
```

This is component reusability.

---

# 15. Complete Example: Professional Next.js Project Structure

Let's combine the concepts into a realistic application structure.

Imagine you are developing an e-commerce application.

```text
ecommerce-app/
│
├── public/
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── src/
│   │
│   ├── app/
│   │   │
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── globals.css
│   │   │
│   │   ├── products/
│   │   │   ├── page.tsx
│   │   │   └── [id]/
│   │   │       └── page.tsx
│   │   │
│   │   ├── cart/
│   │   │   └── page.tsx
│   │   │
│   │   ├── checkout/
│   │   │   └── page.tsx
│   │   │
│   │   ├── login/
│   │   │   └── page.tsx
│   │   │
│   │   └── api/
│   │       └── products/
│   │           └── route.ts
│   │
│   ├── components/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── Navbar.tsx
│   │   └── ProductCard.tsx
│   │
│   ├── features/
│   │   ├── auth/
│   │   ├── products/
│   │   ├── cart/
│   │   └── checkout/
│   │
│   ├── lib/
│   │   ├── db.ts
│   │   └── auth.ts
│   │
│   ├── hooks/
│   │   └── useCart.ts
│   │
│   ├── services/
│   │   └── productService.ts
│   │
│   ├── types/
│   │   └── product.ts
│   │
│   └── utils/
│       └── formatPrice.ts
│
├── .env.local
├── .gitignore
├── eslint.config.mjs
├── next.config.ts
├── package.json
├── package-lock.json
├── postcss.config.mjs
├── tsconfig.json
└── README.md
```

### Understanding This Architecture

| Directory | Responsibility |
|---|---|
| `app/` | Routing and route-specific UI |
| `components/` | Reusable UI components |
| `features/` | Business features |
| `lib/` | Database and infrastructure utilities |
| `hooks/` | Custom React hooks |
| `services/` | Data and API operations |
| `types/` | TypeScript definitions |
| `utils/` | Helper functions |
| `public/` | Static assets |

This is an example architecture, not a mandatory Next.js structure.

---

# 16. Important Differences: page.tsx vs layout.tsx vs template.tsx

These three files are frequently confused.

| Feature | page.tsx | layout.tsx | template.tsx |
|---|---|---|---|
| Purpose | Route-specific UI | Shared UI | Shared UI with remounting behavior |
| Default export required | Yes | Yes | Yes |
| Receives children | Not required | Yes | Yes |
| Can be nested | Yes | Yes | Yes |
| Preserves state across navigation | Depends on its lifecycle | Yes, normally | No, it remounts when activated |
| Used for | Page content | Persistent shared structure | Resetting state and effects |

### Simple Example

```text
app/
│
├── layout.tsx
│
├── template.tsx
│
├── page.tsx
│
└── about/
    └── page.tsx
```

Conceptually:

```text
Root Layout
    |
    v
Template
    |
    v
Page
```

The exact component hierarchy becomes more complex with nested layouts and templates.

Official reference: [Next.js Layouts and Pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages?utm_source=chatgpt.com)

---

# 17. Common Mistakes Developers Make

### Mistake 1: Creating Routes Without page.tsx

Incorrect:

```text
app/
└── about/
    └── About.tsx
```

This does not create `/about`.

Correct:

```text
app/
└── about/
    └── page.tsx
```

### Mistake 2: Putting public Inside src

Incorrect:

```text
src/
└── public/
```

Correct:

```text
public/
src/
```

The public directory belongs at the project root.

### Mistake 3: Exposing Secrets

Incorrect:

```env
NEXT_PUBLIC_DATABASE_URL=mongodb://user:password@localhost
```

Correct:

```env
DATABASE_URL=mongodb://user:password@localhost
```

Keep private values server-side.

### Mistake 4: Modifying .next Manually

Never edit generated files inside `.next`.

### Mistake 5: Using the Wrong Special File Names

Incorrect:

```text
app/
└── home.tsx
```

Correct:

```text
app/
└── page.tsx
```

### Mistake 6: Creating Both app and src/app

Avoid having both:

```text
app/
src/app/
```

Choose one routing directory.

### Mistake 7: Putting Everything Inside One Component

Avoid creating a huge page component containing all application logic.

Instead, divide functionality into reusable components and feature modules.

---

# 18. Phase 3 – Interview Questions

### Q1. What is the purpose of the app directory?

The app directory contains application routes, layouts, loading states, error boundaries, route handlers, and related application code.

### Q2. What is the purpose of page.tsx?

It defines the UI for a particular route.

### Q3. What is the purpose of layout.tsx?

It defines shared UI that wraps multiple pages within a route segment.

### Q4. What is the difference between page.tsx and layout.tsx?

A page defines route-specific content, whereas a layout defines a shared structure around that content.

### Q5. What is the purpose of public?

It stores static assets that can be accessed directly through URLs.

### Q6. What is the purpose of the src directory?

It separates application source code from root-level configuration files.

### Q7. What is the purpose of the .next directory?

It stores generated Next.js development and production artifacts.

### Q8. What is loading.tsx?

It defines a loading UI for a route segment.

### Q9. What is error.tsx?

It defines an error boundary UI for a route segment.

### Q10. What is not-found.tsx?

It defines a custom not-found UI.

### Q11. What is route.ts?

It defines an HTTP endpoint using Next.js Route Handlers.

### Q12. What is the difference between layout.tsx and template.tsx?

Layouts preserve their state across navigation, whereas templates create a new component instance when their segment is activated.

### Q13. What is a route group?

A route group is a folder wrapped in parentheses that organizes routes without affecting their URL paths.

### Q14. What is a private folder?

A folder prefixed with an underscore that is excluded from the routing system.

### Q15. What is colocation?

Colocation means keeping related components, utilities, styles, and other files close to the feature or route where they are used.

### Q16. Does every folder inside app create a route?

No. A route becomes publicly accessible through an appropriate special file such as page.tsx or route.ts.

### Q17. What is next.config.ts?

It is the main configuration file for a Next.js application.

### Q18. What is tsconfig.json?

It configures TypeScript compiler behavior and project-level TypeScript settings.

### Q19. What is the purpose of .gitignore?

It specifies which files and directories Git should ignore.

### Q20. What is the difference between components and features directories?

Components generally contain reusable UI elements, whereas features organize code around specific business functionality.

---

# 19. Practical Assignment – Phase 3

Now let's implement what we have learned.

Use the `nextjs-learning` project created in Phase 2.

### Task 1: Create the Following Folder Structure

Inside `src`, create:

```text
src/
│
├── app/
│
├── components/
│
├── features/
│
├── hooks/
│
├── lib/
│
├── services/
│
├── types/
│
└── utils/
```

### Task 2: Create Reusable Components

Create:

```text
src/components/
│
├── Header.tsx
├── Footer.tsx
└── Button.tsx
```

Header:

```tsx
export default function Header() {
  return (
    <header>
      <h1>Next.js Learning</h1>

      <nav>
        <a href="/">Home</a>
        <a href="/about">About</a>
      </nav>
    </header>
  );
}
```

Footer:

```tsx
export default function Footer() {
  return (
    <footer>
      <p>Copyright 2026 Next.js Learning</p>
    </footer>
  );
}
```

Button:

```tsx
type ButtonProps = {
  title: string;
};

export default function Button({
  title,
}: ButtonProps) {
  return <button>{title}</button>;
}
```

### Task 3: Use Components in layout.tsx

```tsx
import Header from "@/components/Header";
import Footer from "@/components/Footer";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <Header />

        <main>{children}</main>

        <Footer />
      </body>
    </html>
  );
}
```

### Task 4: Update page.tsx

```tsx
import Button from "@/components/Button";

export default function HomePage() {
  return (
    <div>
      <h1>Welcome to My Next.js Application</h1>

      <p>Learning Next.js from 0 to 100.</p>

      <Button title="Get Started" />
    </div>
  );
}
```

### Task 5: Create About Page

Create:

```text
src/app/about/page.tsx
```

Code:

```tsx
export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>

      <p>This is my first Next.js application.</p>
    </div>
  );
}
```

Now visit:

```text
http://localhost:3000/about
```

You should see your About page with the same Header and Footer.

This exercise demonstrates:

- Project organization.
- Component reusability.
- Import aliases.
- Root layouts.
- File-based routing.

---

# 20. Phase 3 – Quick Revision Notes

| File / Folder | Purpose |
|---|---|
| `node_modules/` | Installed dependencies |
| `public/` | Static assets |
| `src/` | Application source code |
| `src/app/` | App Router |
| `.next/` | Generated artifacts |
| `page.tsx` | Route UI |
| `layout.tsx` | Shared layout |
| `template.tsx` | Remounting layout-like component |
| `loading.tsx` | Loading UI |
| `error.tsx` | Error boundary |
| `global-error.tsx` | Global error boundary |
| `not-found.tsx` | Not-found UI |
| `default.tsx` | Parallel route fallback |
| `route.ts` | API endpoint |
| `next.config.ts` | Next.js configuration |
| `tsconfig.json` | TypeScript configuration |
| `eslint.config.mjs` | ESLint configuration |
| `postcss.config.mjs` | PostCSS configuration |
| `next-env.d.ts` | Next.js TypeScript declarations |
| `.gitignore` | Git ignore rules |
| `.env.local` | Local environment variables |
| `package.json` | Dependencies and scripts |
| `package-lock.json` | Resolved dependencies |
| `README.md` | Project documentation |

---

# 21. Phase 3 Completion Checklist

| Topic | Status |
|---|---|
| Next.js Project Structure | Covered |
| node_modules | Covered |
| public | Covered |
| src | Covered |
| .next | Covered |
| app Directory | Covered |
| page.tsx | Covered |
| layout.tsx | Covered |
| loading.tsx | Covered |
| error.tsx | Covered |
| not-found.tsx | Covered |
| template.tsx | Covered |
| route.ts | Covered |
| Configuration Files | Covered |
| Environment Files | Covered |
| File Naming Conventions | Covered |
| Route Segments | Covered |
| Route Groups | Covered |
| Private Folders | Covered |
| Colocation | Covered |
| Project Architecture | Covered |
| Common Mistakes | Covered |
| Interview Questions | Covered |
| Practical Assignment | Assigned |

---

# Next: Phase 4 – Pages and Routing Fundamentals

This is where we will start building actual multi-page Next.js applications.

Topics:

1. What is Routing?
2. What is File-Based Routing?
3. How Next.js Routing Works Internally.
4. Creating Your First Route.
5. Creating Multiple Pages.
6. Understanding URL Paths.
7. Nested Routes.
8. Route Segments.
9. Static Routes.
10. Creating Pages Using page.tsx.
11. Navigation Between Pages.
12. Understanding the Link Component.
13. Link vs HTML Anchor Tag.
14. Programmatic Navigation.
15. useRouter Hook.
16. usePathname Hook.
17. useSearchParams Hook.
18. useParams Hook.
19. Client-Side Navigation.
20. Understanding Next.js Navigation Internals.
21. Common Routing Mistakes.
22. Interview Questions.
23. Practical Routing Exercises.

**Phase 3 is complete.**

When ready, say **"OK, Phase 4"**.

Official documentation for further reference:

- [Next.js Project Structure](https://nextjs.org/docs/pages/getting-started/project-structure?utm_source=chatgpt.com)
- [Next.js App Router](https://nextjs.org/docs/app?utm_source=chatgpt.com)