# **Phase 15 – Environment Variables, Configuration and Secrets Management**



Next.js 16.x

App Router

TypeScript

Intermediate



## **1. Learning Objectives**

By the end of this phase, you will understand how to:

1. Use environment variables in Next.js applications.
2. Understand the different `.env` files and their loading priority.
3. Differentiate between server-side and client-side environment variables.
4. Use the `NEXT_PUBLIC_` prefix correctly.
5. Secure database credentials, API keys, and authentication secrets.
6. Validate environment variables using Zod and TypeScript.
7. Configure environment variables for development, preview, and production.
8. Manage environment variables on Vercel and other hosting platforms.
9. Avoid common security mistakes involving secrets.

## **2. What Are Environment Variables?**

Environment variables are configuration values stored outside your application source code.

They allow your application to use different configuration values depending on where it runs.

For example, your application might connect to:

- A local MongoDB database during development.
- MongoDB Atlas during production.
- A test database during automated testing.

Instead of hardcoding these values in your source code, you store them in environment variables.

### **Without environment variables**

```
import mongoose from "mongoose";

await mongoose.connect(
  "mongodb+srv://admin:password123@cluster.mongodb.net/mydb"
);
```

**Problems:**

- Database credentials are exposed in source code.
- Changing the database requires changing code.
- Different environments become difficult to manage.
- Accidentally pushing credentials to GitHub can compromise your database.

### **With environment variables**

```
import mongoose from "mongoose";

await mongoose.connect(process.env.MONGODB_URI!);
```

The actual connection string is stored separately:

```
MONGODB_URI=mongodb+srv://admin:password123@cluster.mongodb.net/mydb
```

Now your code does not need to know the actual database credentials.

**Important:** Environment variables are configuration, not automatically secure storage. Their security depends on where they are stored, who can access them, and whether they are exposed to the browser.

## **3. Understanding&#x20;**`.env`**&#x20;Files**

Next.js supports multiple environment files.

| File                     | Purpose                            |
| ------------------------ | ---------------------------------- |
| `.env`                   | Default environment variables      |
| `.env.local`             | Local developer-specific variables |
| `.env.development`       | Development-specific variables     |
| `.env.production`        | Production-specific variables      |
| `.env.test`              | Test-specific variables            |
| `.env.development.local` | Local overrides for development    |
| `.env.production.local`  | Local overrides for production     |
| `.env.test.local`        | Local overrides for testing        |

### **Example project structure**

```
my-next-app/
│
├── src/
│   └── app/
│       ├── page.tsx
│       └── layout.tsx
│
├── public/
│
├── .env
├── .env.local
├── .env.development
├── .env.production
├── .env.test
│
├── .gitignore
├── next.config.ts
├── package.json
└── tsconfig.json
```

**Important:** Even when you use a `src` directory, keep all `.env` files in the project root, not inside `src/`.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **3.1&#x20;**`.env`

Contains default values that can be shared across environments.

```
APP_NAME=My Next App
DEFAULT_PAGE_SIZE=20
```

### **3.2&#x20;**`.env.local`

Contains local configuration, commonly including private credentials.

```
MONGODB_URI=mongodb://127.0.0.1:27017/myapp
SESSION_SECRET=some-long-random-secret
```

This file is normally ignored by Git.

### **3.3&#x20;**`.env.development`

Used when running:

```
npm run dev
```

Example:

```
DATABASE_NAME=myapp_development
API_BASE_URL=http://localhost:3000
```

### **3.4&#x20;**`.env.production`

Used for production builds and production execution when the production environment is active.

```
DATABASE_NAME=myapp_production
API_BASE_URL=https://example.com
```

### **3.5&#x20;**`.env.test`

Used when `NODE_ENV=test`, such as during automated tests.

```
DATABASE_NAME=myapp_test
```

One useful detail: `.env.test` is suitable for stable test defaults, while `.env.test.local` can hold local test overrides and should not be committed.

## **4. Environment Variable Loading Priority**

This is an important concept.

Next.js checks environment variables in the following order and uses the first value it finds:

**Environment variable precedence**

1. **1.&#x20;**`process.env`

   Values already provided by the running process or hosting platform.
2. **2.&#x20;**`.env.development.local`**&#x20;/&#x20;**`.env.production.local`**&#x20;/&#x20;**`.env.test.local`

   Environment-specific local overrides.
3. **3.&#x20;**`.env.local`

   General local overrides. Not loaded in the test environment.
4. **4.&#x20;**`.env.development`**&#x20;/&#x20;**`.env.production`**&#x20;/&#x20;**`.env.test`

   Environment-specific defaults.
5. **5.&#x20;**`.env`

   General defaults.

Highest priority is at the top.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **Example**

Suppose you have the same variable in two files.

`.env`:

```
DATABASE_NAME=myapp
```

`.env.development.local`:

```
DATABASE_NAME=myapp_local
```

When running in development, the resulting value is:

```
process.env.DATABASE_NAME;

// myapp_local
```

Because `.env.development.local` has higher priority.

### **Important:&#x20;**`NODE_ENV`

Next.js uses three standard `NODE_ENV` values:

| Value         | Meaning          |
| ------------- | ---------------- |
| `development` | Development mode |
| `production`  | Production mode  |
| `test`        | Testing mode     |

Do not use custom values such as:

```
NODE_ENV=staging
NODE_ENV=preview
```

Instead, use a separate variable:

```
APP_ENV=staging
```

`NODE_ENV` describes the application execution mode; `APP_ENV` can describe your deployment environment.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **5. Accessing Environment Variables Using&#x20;**`process.env`

Next.js loads supported `.env` files into Node.js's `process.env`.

You can access them using:

```
process.env.VARIABLE_NAME
```

### **Example**

`.env.local`:

```
APP_NAME=My Application
MONGODB_URI=mongodb://127.0.0.1:27017/myapp
PORT=3000
```

Access them in server-side code:

```
const appName = process.env.APP_NAME;
const databaseUrl = process.env.MONGODB_URI;
const port = process.env.PORT;

console.log(appName);
console.log(databaseUrl);
console.log(port);
```

### **Important: Environment variables are strings**

Even if you write:

```
PORT=3000
ENABLE_LOGS=true
```

JavaScript reads these values as strings.

```
console.log(typeof process.env.PORT);
// string

console.log(typeof process.env.ENABLE_LOGS);
// string
```

Convert values when necessary:

```
const port = Number(process.env.PORT);

const enableLogs =
  process.env.ENABLE_LOGS === "true";
```

Do not assume that environment variables are automatically converted into numbers or booleans.

## **6. Server-Side vs Client-Side Environment Variables**

This is one of the most important concepts in this phase.

Next.js applications contain two execution environments:

1. Server environment
2. Browser environment

Some environment variables must remain on the server because they contain sensitive information.

### **6.1 Server-side environment variables**

Examples:

```
MONGODB_URI=mongodb+srv://...
OPENAI_API_KEY=secret-api-key
SESSION_SECRET=long-random-secret
STRIPE_SECRET_KEY=secret-payment-key
```

These values should only be accessed from trusted server-side code.

Examples of server-side locations:

- Server Components
- Server Actions
- Route Handlers
- Proxy code running in the server environment
- Server-only utility modules

Example:

```
// app/dashboard/page.tsx

export default async function DashboardPage() {
  const databaseUrl = process.env.MONGODB_URI;

  return (
    <main>
      <h1>Dashboard</h1>
    </main>
  );
}
```

The database URL is read on the server. Do not pass it to a Client Component or include it in rendered output.

### **6.2 Client-side environment variables**

Sometimes the browser genuinely needs a configuration value.

Examples:

- Public API base URL
- Public analytics ID
- Public map configuration
- Public application name

For these values, Next.js supports the `NEXT_PUBLIC_` prefix.

```
NEXT_PUBLIC_APP_NAME=My Application
NEXT_PUBLIC_API_URL=https://api.example.com
```

You can access them in a Client Component:

```
"use client";

export default function HomePage() {
  const appName = process.env.NEXT_PUBLIC_APP_NAME;

  return <h1>{appName}</h1>;
}
```

### **6.3 What does&#x20;**`NEXT_PUBLIC_`**&#x20;actually do?**

When Next.js encounters a statically referenced environment variable beginning with `NEXT_PUBLIC_`, it can inline its value into the JavaScript bundle during the build.

For example:

```
NEXT_PUBLIC_APP_NAME=My Application
```

Code:

```
const name = process.env.NEXT_PUBLIC_APP_NAME;
```

The compiled client code effectively contains the public value.

**How the value reaches the browser**

`.env.local`

NEXT_PUBLIC_APP_NAME=My Application

**Next.js build process**

Reads and inlines the public value

**Browser JavaScript bundle**

Publicly visible

Users can inspect the value

**Never use&#x20;**`NEXT_PUBLIC_`**&#x20;for:**

- Database passwords
- Private API keys
- JWT secrets
- Session secrets
- Payment provider secret keys
- Private access tokens

A public prefix does not encrypt a value. It makes it available to browser code.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **6.4 Build-time behavior**

Consider:

```
NEXT_PUBLIC_API_URL=https://api-development.example.com
```

You build your application:

```
npm run build
```

Later, you deploy the same build to production and change the environment variable on the server to:

```
NEXT_PUBLIC_API_URL=https://api-production.example.com
```

The already-built client JavaScript can still contain the development URL.

Why?

Because `NEXT_PUBLIC_` values are inlined at build time. Changing the server's environment variable does not rewrite an existing client bundle.

**Rule:** If you change a `NEXT_PUBLIC_` value, rebuild the application.

## **7. Protecting Server-Only Modules**

A common mistake is accidentally importing a database utility into a Client Component.

For example:

```
src/
├── app/
│   ├── dashboard/
│   │   ├── page.tsx
│   │   └── DashboardClient.tsx
│
└── lib/
    └── db.ts
```

Suppose `db.ts` contains database credentials and connection logic.

You should explicitly mark this module as server-only.

### **7.1 Using&#x20;**`server-only`

Install the package if it is not already available:

```
npm install server-only
```

Create the database module:

```
// src/lib/db.ts

import "server-only";

import mongoose from "mongoose";

export async function connectDB() {
  if (mongoose.connection.readyState === 1) {
    return;
  }

  await mongoose.connect(process.env.MONGODB_URI!);
}
```

Now, if a Client Component tries to import this module, Next.js will report a build-time error.

### **Why is this useful?**

Consider this:

```
"use client";

import { connectDB } from "@/lib/db";

export default function Dashboard() {
  // ...
}
```

This is an invalid architecture. Client Components should not directly import database connection utilities.

Instead, use a Server Component, Server Action, or Route Handler to perform database operations.

**Remember:**

- `server-only` helps prevent accidental imports into the client module graph.
- It does not validate credentials.
- It does not replace authentication or authorization.
- It does not make a secret safe if you explicitly send it to the browser.

## **8. Environment Variables in Different Next.js Features**

Let's understand where environment variables can be accessed.

| Next.js feature        | Server variables                | `NEXT_PUBLIC_` variables |
| ---------------------- | ------------------------------- | ------------------------ |
| Server Components      | Yes                             | Yes                      |
| Client Components      | No direct private access        | Yes                      |
| Server Actions         | Yes                             | Yes                      |
| Route Handlers         | Yes                             | Yes                      |
| Proxy                  | Yes, subject to runtime support | Yes                      |
| Server utility modules | Yes                             | Yes                      |

The key distinction is whether code executes in a server or browser environment.

### **8.1 Server Component**

```
// app/profile/page.tsx

export default async function ProfilePage() {
  const apiKey = process.env.INTERNAL_API_KEY;

  const response = await fetch(
    "https://api.example.com/profile",
    {
      headers: {
        Authorization: `Bearer ${apiKey}`,
      },
    }
  );

  const profile = await response.json();

  return <h1>{profile.name}</h1>;
}
```

The private API key is used on the server and is not rendered into the page.

### **8.2 Route Handler**

```
// app/api/users/route.ts

import { NextResponse } from "next/server";

export async function GET() {
  const apiKey = process.env.INTERNAL_API_KEY;

  const response = await fetch(
    "https://api.example.com/users",
    {
      headers: {
        Authorization: `Bearer ${apiKey}`,
      },
    }
  );

  const users = await response.json();

  return NextResponse.json(users);
}
```

The browser requests your Route Handler. Your Route Handler communicates with the external API using the private key.

Do not return the API key in the response.

### **8.3 Server Action**

```
// app/actions.ts

"use server";

export async function getSecretData() {
  const apiKey = process.env.INTERNAL_API_KEY;

  // Use the private key on the server.
  // Return only the data the caller is authorized to receive.

  return { success: true };
}
```

### **8.4 Client Component**

```
"use client";

export default function HomePage() {
  const appName = process.env.NEXT_PUBLIC_APP_NAME;

  return <h1>{appName}</h1>;
}
```

A Client Component should not access a private server environment variable directly.

## **9. Type-Safe Environment Variables with Zod**

TypeScript does not automatically guarantee that your environment variables exist.

For example:

```
const databaseUrl = process.env.MONGODB_URI;
```

TypeScript generally treats this value as:

```
string | undefined
```

Why?

Because environment variables may be missing.

### **9.1 The unsafe approach**

```
const databaseUrl = process.env.MONGODB_URI!;
```

The `!` tells TypeScript:

> Trust me, this value is not undefined.

But it does not check whether the value actually exists at runtime.

If the variable is missing, your application can still fail.

### **9.2 Better approach: Zod validation**

Install Zod:

```
npm install zod
```

Create:

```
src/
└── lib/
    └── env.ts
```

Example:

```
// src/lib/env.ts

import "server-only";

import { z } from "zod";

const envSchema = z.object({
  MONGODB_URI: z.string().min(1),
  SESSION_SECRET: z.string().min(32),
  INTERNAL_API_KEY: z.string().min(1),
  NODE_ENV: z.enum([
    "development",
    "production",
    "test",
  ]),
});

const parsedEnv = envSchema.safeParse(process.env);

if (!parsedEnv.success) {
  console.error(
    "Invalid environment variables:",
    parsedEnv.error.flatten().fieldErrors
  );

  throw new Error(
    "Invalid server environment configuration"
  );
}

export const env = parsedEnv.data;
```

Now use:

```
import { env } from "@/lib/env";

console.log(env.NODE_ENV);

const databaseUrl = env.MONGODB_URI;
```

### **What did we achieve?**

Before validation:

```
process.env.MONGODB_URI;
// string | undefined
```

After successful validation:

```
env.MONGODB_URI;
// string
```

Zod validates the values at runtime and provides useful TypeScript types.

### **9.3 Validate public environment variables separately**

Avoid importing a server-only environment module into Client Components.

You can create a separate public configuration module:

```
// src/lib/public-env.ts

import { z } from "zod";

const publicEnvSchema = z.object({
  NEXT_PUBLIC_APP_NAME: z.string().min(1),
  NEXT_PUBLIC_API_URL: z.string().url(),
});

const parsed = publicEnvSchema.safeParse({
  NEXT_PUBLIC_APP_NAME:
    process.env.NEXT_PUBLIC_APP_NAME,

  NEXT_PUBLIC_API_URL:
    process.env.NEXT_PUBLIC_API_URL,
});

if (!parsed.success) {
  throw new Error("Invalid public environment configuration");
}

export const publicEnv = parsed.data;
```

Use it in a Client Component:

```
"use client";

import { publicEnv } from "@/lib/public-env";

export default function Header() {
  return <h1>{publicEnv.NEXT_PUBLIC_APP_NAME}</h1>;
}
```

Notice that the public module contains only values intended for the browser.

## **10. Using&#x20;**`next.config.ts`**&#x20;for Configuration**

Next.js provides a configuration file:

```
next.config.ts
```

Example:

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  reactStrictMode: true,
};

export default nextConfig;
```

### **10.1 The&#x20;**`env`**&#x20;option**

Next.js also provides an `env` option:

```
const nextConfig = {
  env: {
    customKey: "my-value",
  },
};

export default nextConfig;
```

You can access it using:

```
process.env.customKey;
```

However, this option is a legacy API and is no longer recommended for ordinary environment variable management.

**Important:** Values specified through `next.config.ts`'s `env` option are included in the JavaScript bundle, even without the `NEXT_PUBLIC_` prefix.

Never put database credentials, private API keys, or session secrets in this option. Use normal environment variables and your hosting provider's configuration instead.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **11. Creating a Proper&#x20;**`.env.example`**&#x20;File**

When working in a team, other developers need to know which environment variables your application requires.

However, you should never share your real secrets through Git.

The solution is `.env.example`.

### **Example&#x20;**`.env.local`

```
MONGODB_URI=mongodb+srv://real-user:real-password@cluster.mongodb.net/mydb

SESSION_SECRET=real-long-random-secret

INTERNAL_API_KEY=real-private-api-key

NEXT_PUBLIC_APP_NAME=My Application

NEXT_PUBLIC_API_URL=http://localhost:3000
```

### **Example&#x20;**`.env.example`

```
MONGODB_URI=

SESSION_SECRET=

INTERNAL_API_KEY=

NEXT_PUBLIC_APP_NAME=My Application

NEXT_PUBLIC_API_URL=http://localhost:3000
```

You commit `.env.example` to Git, but keep `.env.local` private.

### **11.1 Configure&#x20;**`.gitignore`

Make sure your `.gitignore` includes:

```
# Local environment files
.env*
!.env.example
```

This ignores environment files but allows the example template to be committed.

**Important:** Check your existing `.gitignore` before modifying it. A default Next.js project already includes environment file exclusions.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **11.2 What if you accidentally commit a secret?**

Deleting the secret from the latest commit is not sufficient if it has already been pushed.

Follow these steps:

1. Revoke or rotate the exposed API key immediately.
2. Change the database password if database credentials were exposed.
3. Generate a new session secret where appropriate.
4. Remove the exposed value from the repository and its history if necessary.
5. Review access logs for suspicious activity.
6. Update the environment variable in your hosting platform.
7. Redeploy the application.

The most important step is rotating the compromised credential. Removing it from Git history does not make an already-exposed secret safe.

## **12. Loading Environment Variables Outside Next.js**

Sometimes you need to access environment variables from standalone scripts.

For example:

- Database migration scripts
- Seed scripts
- Automated testing
- Background jobs
- Node.js utility scripts

Next.js provides the `@next/env` package.

### **Example**

Install it if needed:

```
npm install @next/env
```

Create:

```
scripts/
└── seed.ts
```

Example:

```
import { loadEnvConfig } from "@next/env";

loadEnvConfig(process.cwd());

console.log(process.env.MONGODB_URI);
```

This loads environment variables using Next.js's environment loading behavior.

For example, a script can then use the same database connection configuration as your application.

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1

## **13. Managing Different Deployment Environments**

A real application normally has more than one environment.

For example, imagine you are developing an e-commerce application.

**Three common deployment environments**

**Development**

Local

Used by developers to build and test features.

```
MONGODB_URI=mongodb://localhost:27017/shop_dev
APP_ENV=development
```

**Preview / Staging**

Testing deployment

Used to test changes before releasing them to users.

```
MONGODB_URI=mongodb+srv://.../shop_staging
APP_ENV=staging
```

**Production**

Live application

Used by actual customers.

```
MONGODB_URI=mongodb+srv://.../shop_production
APP_ENV=production
```

### **Why use separate databases?**

Suppose you are testing a delete operation.

If your development application connects to the production database, you might accidentally delete actual customer records.

Separate environments help prevent this.

Recommended separation:

| Configuration | Development    | Staging    | Production    |
| ------------- | -------------- | ---------- | ------------- |
| Database      | Development DB | Staging DB | Production DB |
| API keys      | Test keys      | Test keys  | Live keys     |
| Payment mode  | Sandbox        | Sandbox    | Live          |
| Logging       | Detailed       | Moderate   | Controlled    |
| Debugging     | Enabled        | Restricted | Restricted    |

Use separate credentials and restrict permissions for each environment.

## **14. Configuring Environment Variables on Vercel**

When you deploy a Next.js application to Vercel, you do not need to upload your `.env.local` file.

Instead, configure the environment variables through the Vercel dashboard.

### **Steps**

1. Open your Vercel dashboard.
2. Select your project.
3. Open **Settings**.
4. Select **Environment Variables**.
5. Add the variable name and value.
6. Select the environment where it should apply.
7. Save the configuration.
8. Redeploy your application.

[How to Handle Different Environments in a NextJS Application](https://images.openai.com/static-rsc-4/RrB0ybVdMgfWeGnyO_CVwKEh8zFCX0aM474VCB9kKFQ8vd4JPWexvWG-Zf0t6E670OkMK1AL5RgZrjQCPBy7G7v9dCfo9O2JUef_YRzjGNwfZFHXPfUx3Bb7P4OnvwLtEYaByn1Hv_MzmETY098xyZbgnGHr3GDlF2acPKKBqdI?purpose=inline)

[mdfaisal.com](https://www.mdfaisal.com/blog/how-to-handle-different-environments-in-a-nextjs-application?utm_source=chatgpt.com)

### **14.1 Vercel environments**

Vercel provides three standard environments:

| Environment | Purpose                                                  |
| ----------- | -------------------------------------------------------- |
| Development | Local development using Vercel's development environment |
| Preview     | Deployments from non-production branches                 |
| Production  | Live production deployment                               |

Vercel also supports custom environments for eligible plans and projects.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://vercel.com\&sz=32)

Vercel+1



### **Example**

You can configure:

**Production**

```
MONGODB_URI=mongodb+srv://.../production
```

**Preview**

```
MONGODB_URI=mongodb+srv://.../staging
```

**Development**

```
MONGODB_URI=mongodb://localhost:27017/development
```

The same code can now connect to different databases depending on the deployment environment.

### **Important: Redeployment**

Suppose you update:

```
INTERNAL_API_KEY=new-api-key
```

on Vercel.

Existing deployments do not automatically receive the new value. The changed configuration applies to new deployments.

You need to redeploy for the new value to take effect.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://vercel.com\&sz=32)

Vercel+1



### **14.2 Using Vercel CLI**

You can also pull your Vercel development environment variables into your local project.

```
npm install -g vercel
```

Link your project:

```
vercel link
```

Pull development variables:

```
vercel env pull .env.local
```

This is useful when your local environment should match the configuration of your deployed project.

## **15. Environment Variables on Other Hosting Platforms**

The same principle applies to other hosting providers.

### **Render**

In your Render dashboard:

1. Open your service.
2. Navigate to Environment.
3. Add your environment variables.
4. Save changes.
5. Redeploy or restart as required.

### **Hostinger VPS / Linux server**

You can configure environment variables through your deployment setup or service manager.

For example, if you use PM2, you can provide environment variables in your ecosystem configuration:

```
module.exports = {
  apps: [
    {
      name: "next-app",
      script: "npm",
      args: "start",
      env: {
        NODE_ENV: "production",
        APP_ENV: "production",
        PORT: 3000,
      },
    },
  ],
};
```

Do not put private credentials directly into a committed ecosystem configuration file. Use a secure deployment configuration or a protected server-side environment file.

**Remember:** Environment variables are not specific to Next.js. They are a general operating-system and application configuration mechanism. Next.js simply provides convenient support for loading them.

## **16. Environment Variable Security Best Practices**

These rules are important for real-world development.

### **Rule 1: Never expose private credentials**

Bad:

```
NEXT_PUBLIC_MONGODB_URI=mongodb+srv://...
```

Good:

```
MONGODB_URI=mongodb+srv://...
```

### **Rule 2: Never commit real secrets to GitHub**

Bad:

```
git add .env.local
git commit -m "Add database configuration"
```

Good:

```
git add .env.example
git commit -m "Add environment template"
```

### **Rule 3: Use separate credentials for different environments**

Do not use your production database credentials in development.

### **Rule 4: Never log secrets**

Bad:

```
console.log(process.env.MONGODB_URI);
console.log(process.env.INTERNAL_API_KEY);
```

Good:

```
console.log("Database configuration loaded");
```

### **Rule 5: Do not return secrets through API responses**

Bad:

```
export async function GET() {
  return Response.json({
    apiKey: process.env.INTERNAL_API_KEY,
  });
}
```

Good:

```
export async function GET() {
  const apiKey = process.env.INTERNAL_API_KEY;

  // Use the key internally.
  // Return only authorized application data.

  return Response.json({
    success: true,
  });
}
```

### **Rule 6: Avoid putting secrets in URLs**

Bad:

```
fetch(
  `https://api.example.com/users?apiKey=${process.env.INTERNAL_API_KEY}`
);
```

Better:

```
fetch("https://api.example.com/users", {
  headers: {
    Authorization: `Bearer ${process.env.INTERNAL_API_KEY}`,
  },
});
```

URLs can appear in browser history, server logs, analytics, and other monitoring systems.

### **Rule 7: Use least privilege**

A database account used by your application should have only the permissions it needs.

For example, a read-only reporting service should not use a database administrator account.

### **Rule 8: Validate environment variables**

Fail early if required configuration is missing.

### **Rule 9: Rotate credentials periodically and after exposure**

Replace compromised credentials immediately. Follow your organization's rotation policy for other secrets.

### **Rule 10: Never pass server secrets as Client Component props**

Bad:

```
<ClientDashboard
  apiKey={process.env.INTERNAL_API_KEY}
/>
```

Even if the component does not display the key, passing it to a Client Component can expose it through client-side data.

## **17. Common Mistakes and Their Solutions**

| Mistake                                      | Why it is wrong                                   | Correct approach              |
| -------------------------------------------- | ------------------------------------------------- | ----------------------------- |
| Using `NEXT_PUBLIC_` for passwords           | Exposes secrets to browsers                       | Use server-only variables     |
| Keeping `.env` inside `src/`                 | Next.js looks for these files in the project root | Move `.env` files to root     |
| Committing `.env.local`                      | Exposes private configuration                     | Add it to `.gitignore`        |
| Using `!` on every variable                  | Hides missing-value errors from TypeScript        | Validate variables at runtime |
| Changing public variables without rebuilding | Existing client bundles retain old values         | Rebuild and redeploy          |
| Using production DB locally                  | Can damage live data                              | Use separate databases        |
| Returning secrets from Route Handlers        | Exposes credentials to callers                    | Return only authorized data   |
| Logging API keys                             | Credentials may enter log systems                 | Log safe status messages      |
| Using `NODE_ENV=staging`                     | Not a supported standard `NODE_ENV` value         | Use `APP_ENV=staging`         |

### **17.1 Dynamic access to public variables**

This is another subtle issue.

Avoid:

```
const key = "NEXT_PUBLIC_APP_NAME";

const value = process.env[key];
```

Next.js does not inline public environment variables through arbitrary dynamic access in the same way as direct references.

Prefer:

```
const value = process.env.NEXT_PUBLIC_APP_NAME;
```

Use static property references for public variables that need to be included in client bundles.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



## **18. Real-World Project Example**

Let's put these concepts together.

Imagine you are building a Next.js application with:

- MongoDB Atlas
- Session-based authentication
- An external AI API
- A public frontend URL

### **18.1 Project structure**

```
my-next-app/
│
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   └── users/
│   │   │       └── route.ts
│   │   └── page.tsx
│   │
│   └── lib/
│       ├── db.ts
│       ├── env.ts
│       └── public-env.ts
│
├── .env.example
├── .env.local
├── .gitignore
├── next.config.ts
└── package.json
```

### **18.2 Environment variables**

`.env.local`:

```
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/myapp

SESSION_SECRET=replace-with-a-long-random-secret

AI_API_KEY=replace-with-private-api-key

NEXT_PUBLIC_APP_NAME=My AI Application

NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

### **18.3 Server environment validation**

```
// src/lib/env.ts

import "server-only";

import { z } from "zod";

const schema = z.object({
  MONGODB_URI: z.string().min(1),
  SESSION_SECRET: z.string().min(32),
  AI_API_KEY: z.string().min(1),
});

export const env = schema.parse(process.env);
```

### **18.4 Public environment validation**

```
// src/lib/public-env.ts

import { z } from "zod";

const schema = z.object({
  NEXT_PUBLIC_APP_NAME: z.string().min(1),
  NEXT_PUBLIC_SITE_URL: z.string().url(),
});

export const publicEnv = schema.parse({
  NEXT_PUBLIC_APP_NAME:
    process.env.NEXT_PUBLIC_APP_NAME,

  NEXT_PUBLIC_SITE_URL:
    process.env.NEXT_PUBLIC_SITE_URL,
});
```

### **18.5 Database connection**

```
// src/lib/db.ts

import "server-only";

import mongoose from "mongoose";
import { env } from "./env";

export async function connectDB() {
  if (mongoose.connection.readyState === 1) {
    return;
  }

  await mongoose.connect(env.MONGODB_URI);
}
```

### **18.6 Client Component**

```
"use client";

import { publicEnv } from "@/lib/public-env";

export default function HomePage() {
  return (
    <main>
      <h1>{publicEnv.NEXT_PUBLIC_APP_NAME}</h1>
    </main>
  );
}
```

Notice the separation:

- `env.ts` contains private server configuration.
- `public-env.ts` contains public browser configuration.
- `db.ts` contains server-only database code.
- The Client Component only imports public configuration.

This is a clean configuration architecture for a real-world Next.js application.

## **19. Interview Questions and Answers**

### **Basic Level**

**Q1. What are environment variables?**

Environment variables are key-value pairs used to store application configuration outside the source code.

**Q2. Why do we use&#x20;**`.env.local`**?**

To store local configuration and developer-specific values, including private credentials, without committing them to Git.

**Q3. What is the purpose of&#x20;**`NEXT_PUBLIC_`**?**

It allows selected environment variables to be exposed to browser JavaScript through build-time inlining.

**Q4. Can we access environment variables in Server Components?**

Yes. Server Components can access private and public environment variables.

**Q5. Can Client Components access private environment variables?**

No. Private server variables should not be accessed or exposed in Client Components.

### **Intermediate Level**

**Q6. What is the difference between&#x20;**`.env`**&#x20;and&#x20;**`.env.local`**?**

`.env` provides general defaults, while `.env.local` provides local overrides and is normally excluded from Git.

**Q7. What is the environment variable loading priority in Next.js?**

1. `process.env`
2. `.env.$(NODE_ENV).local`
3. `.env.local` (except in test mode)
4. `.env.$(NODE_ENV)`
5. `.env`

**Q8. Are&#x20;**`NEXT_PUBLIC_`**&#x20;variables encrypted?**

No. They are intended to be publicly accessible and may be included in browser JavaScript.

**Q9. Why should we use Zod for environment variables?**

Zod validates required variables at runtime and provides TypeScript types for validated configuration.

**Q10. What happens when we change a&#x20;**`NEXT_PUBLIC_`**&#x20;variable after building the application?**

The existing client bundle does not automatically update. A new build is required.

### **Advanced Level**

**Q11. What is the difference between build-time and runtime environment variables?**

- Build-time variables are read while the application is being built.
- Runtime variables are read when server-side code executes.

Public variables are generally inlined at build time. Private variables can be read by server-side code at runtime, depending on how and when that code executes.

**Q12. Why should we use&#x20;**`import "server-only"`**?**

It prevents a server-only module from accidentally being imported into the Client Component module graph.

**Q13. Why is&#x20;**`process.env.MONGODB_URI!`**&#x20;not completely safe?**

The TypeScript non-null assertion only suppresses a type warning. It does not check whether the variable exists at runtime.

**Q14. What is the difference between&#x20;**`NODE_ENV`**&#x20;and&#x20;**`APP_ENV`**?**

`NODE_ENV` represents the standard Node.js execution mode (`development`, `production`, or `test`). `APP_ENV` can represent application-specific deployment environments such as staging.

**Q15. Why should we use separate databases for development and production?**

To prevent development operations, testing, and accidental data modifications from affecting live production data.

## **20. Practical Assignment**

Hands-on practice

Build a secure environment configuration system for a Next.js application.

### **Task 1: Create environment files**

Create:

```
.env.local
.env.example
```

Add these variables:

```
MONGODB_URI=
SESSION_SECRET=
AI_API_KEY=
NEXT_PUBLIC_APP_NAME=
NEXT_PUBLIC_SITE_URL=
```

### **Task 2: Configure Git**

- Ensure `.env.local` is ignored by Git.
- Ensure `.env.example` is tracked by Git.
- Confirm no real credentials are committed.

### **Task 3: Implement environment validation**

Create:

```
src/lib/env.ts
src/lib/public-env.ts
```

Requirements:

- Use Zod for validation.
- Keep private and public variables separate.
- Use `server-only` in private configuration.
- Validate required values.
- Display useful errors if configuration is invalid.

### **Task 4: Implement database configuration**

Create:

```
src/lib/db.ts
```

Requirements:

- Connect to MongoDB using `MONGODB_URI`.
- Keep database connection logic server-only.
- Never expose database credentials to the browser.

### **Task 5: Test environment separation**

- Configure local development values.
- Configure different staging values.
- Configure production values.
- Verify that each deployment uses the intended database.
- Test what happens when a required variable is missing.

### **Task 6: Deploy**

Deploy the application to Vercel.

- Add private environment variables through project settings.
- Add public environment variables.
- Configure Production and Preview values separately.
- Redeploy after changing environment variables.
- Inspect the browser bundle and Network tab to verify that private credentials are not exposed.

**Security requirement:** Use placeholder credentials for the assignment. Never share your real database URI, API keys, or session secret.

## **21. Quick Revision Notes**

| Concept                     | Remember                                    |
| --------------------------- | ------------------------------------------- |
| Environment variable        | External application configuration          |
| `process.env`               | Access environment variables in Node.js     |
| `.env`                      | General defaults                            |
| `.env.local`                | Local overrides                             |
| `.env.development`          | Development configuration                   |
| `.env.production`           | Production configuration                    |
| `.env.test`                 | Test configuration                          |
| `NEXT_PUBLIC_`              | Makes a variable available to browser code  |
| `server-only`               | Prevents accidental client imports          |
| Zod                         | Runtime environment validation              |
| `.env.example`              | Safe configuration template                 |
| `.gitignore`                | Prevents local secrets from being committed |
| `NODE_ENV`                  | Standard execution mode                     |
| `APP_ENV`                   | Custom deployment environment               |
| `next.config.ts` `env`      | Legacy compile-time configuration option    |
| Vercel environment settings | Configure values per deployment environment |

## **22. Phase 15 Completion Checklist**

**Your learning progress**

### **0/13**

Understand environment variables and their purpose

Understand all major .env file types

Understand environment variable loading priority

Use process.env correctly

Understand NEXT_PUBLIC\_ and build-time inlining

Separate server-only and public configuration

Use server-only to protect private modules

Validate environment variables using Zod

Create .env.example and configure .gitignore

Understand NODE_ENV and APP_ENV

Configure Vercel environment variables

Understand secret management and rotation

Complete the practical assignment

Reset checklist

## **23. Next Phase Preview**

Next: Phase 16

# **Metadata, SEO and Open Graph**

**Topics we will cover:**

1. What is SEO and how search engines work?
2. Static metadata using `metadata` object.
3. Dynamic metadata using `generateMetadata()`.
4. Title templates and title inheritance.
5. Meta description, keywords and robots.
6. Open Graph metadata and social sharing previews.
7. Twitter/X card metadata.
8. Canonical URLs and metadataBase.
9. Dynamic OG images using `ImageResponse`.
10. Sitemap, robots.txt and favicon.
11. SEO best practices for Next.js applications.
12. Practical SEO implementation.

**When you're ready, say:&#x20;**`OK, Phase 16`**.**