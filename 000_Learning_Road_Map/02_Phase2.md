# Next.js Complete Learning Roadmap
# Phase 2: Next.js Installation and Project Setup

**Level:** Beginner  
**Difficulty:** Easy  
**Technology:** Next.js 16.x  
**Architecture:** App Router  
**Operating System:** Windows 10/11  
**Package Manager:** npm  
**Goal:** Learn how to install Next.js, create your first project, understand configuration options, and run Next.js in development and production environments.

---

# 1. Prerequisites for Next.js

Before installing Next.js, you should have basic knowledge of the following technologies.

| Technology | Purpose |
|---|---|
| HTML | Structure of web pages |
| CSS | Styling web pages |
| JavaScript | Programming language |
| React.js | Building user interfaces |
| Node.js | JavaScript runtime |
| npm | Package manager |

### Why Do We Need Node.js?

Next.js uses Node.js to execute its server-side JavaScript code and development tools.

For example:

- Running the development server.
- Executing server-side code.
- Installing dependencies.
- Building production applications.
- Running backend functionality.

### Why Do We Need npm?

npm stands for Node Package Manager.

It helps us:

- Install packages.
- Manage dependencies.
- Execute project scripts.
- Manage package versions.

**Important:** Node.js and npm are required for local Next.js development.

---

# 2. Installing Node.js

Before installing Next.js, make sure Node.js is installed.

### Step 1: Download Node.js

Visit the official website:

[Node.js Official Website](https://nodejs.org/en?utm_source=chatgpt.com)

Download a supported LTS version.

**Next.js 16 minimum requirement: Node.js 20.9.0.** [nextjs.org](https://nextjs.org/docs/app/getting-started/installation?utm_source=chatgpt.com)

### Step 2: Install Node.js

Run the downloaded installer.

Follow these steps:

1. Accept the license agreement.
2. Select the installation directory.
3. Keep the default installation options.
4. Complete the installation.

npm is included with the Node.js installation.

### Step 3: Verify Installation

Open your terminal (CMD, PowerShell, or VS Code terminal).

Execute:

```bash
node -v
```

Example output:

```text
v22.16.0
```

Check npm:

```bash
npm -v
```

Example output:

```text
10.9.2
```

Your exact versions may differ.

### What is the Difference Between Node.js and npm?

| Node.js | npm |
|---|---|
| JavaScript runtime | Package manager |
| Executes JavaScript outside the browser | Installs and manages packages |
| Provides runtime APIs | Manages dependencies |
| Executes server-side code | Executes project scripts |

**Remember:** Node.js executes JavaScript, whereas npm manages packages and project commands.

---

# 3. What is create-next-app?

`create-next-app` is an official CLI tool provided by the Next.js ecosystem.

CLI stands for Command Line Interface.

It helps developers create a new Next.js application without manually configuring everything.

### What Does create-next-app Do?

When you create a Next.js project using this tool, it automatically:

1. Creates the project directory.
2. Initializes `package.json`.
3. Installs Next.js.
4. Installs React.
5. Installs React DOM.
6. Configures TypeScript if selected.
7. Configures ESLint if selected.
8. Configures Tailwind CSS if selected.
9. Sets up the App Router if selected.
10. Creates the initial project structure.

### Why Do We Use It?

Without `create-next-app`, you would need to configure the Next.js environment manually.

Using `create-next-app` makes project initialization easier.

---

# 4. Creating Your First Next.js Project

Now let's create our first application.

### Step 1: Open VS Code

Open Visual Studio Code.

### Step 2: Open Terminal

Use:

```text
Ctrl + `
```

Or navigate to:

```text
Terminal → New Terminal
```

### Step 3: Navigate to Your Project Directory

For example:

```bash
cd D:\Projects
```

You can choose any directory where you want to create your projects.

### Step 4: Execute the Installation Command

```bash
npx create-next-app@latest
```

Here is what each part means:

| Command | Meaning |
|---|---|
| `npx` | Executes a package without requiring a permanent global installation |
| `create-next-app` | Next.js project creation tool |
| `@latest` | Uses the latest published version of the tool |

The CLI will ask you several questions.

---

# 5. Understanding create-next-app Configuration Options

When you execute:

```bash
npx create-next-app@latest
```

You will be asked several questions.

Let's understand each one.

### Question 1: What is your project named?

Example:

```text
What is your project named? my-next-app
```

This determines your project folder name.

For example:

```text
D:\Projects\my-next-app
```

You can provide any valid project name.

### Question 2: Would you like to use the recommended Next.js defaults?

You may see options similar to:

```text
Yes, use recommended defaults

No, reuse previous settings

No, customize settings
```

If you choose recommended defaults, Next.js configures the recommended project setup automatically.

For learning purposes, I recommend understanding the customization options instead of blindly accepting defaults.

### Question 3: Would you like to use TypeScript?

Options:

```text
Yes / No
```

TypeScript is a programming language built on JavaScript that adds static type checking.

Example JavaScript:

```javascript
function add(a, b) {
  return a + b;
}
```

Example TypeScript:

```typescript
function add(a: number, b: number): number {
  return a + b;
}
```

TypeScript helps identify certain programming errors before runtime.

**Recommendation:** Choose Yes.

We will learn Next.js using TypeScript.

### Question 4: Which linter would you like to use?

Depending on your CLI version, you may see:

```text
ESLint
Biome
None
```

A linter analyzes your code and identifies potential problems.

For example:

```javascript
const name = "Riyaz"
console.log(name)
```

A linter can identify issues such as:

- Unused variables.
- Incorrect syntax patterns.
- Potential bugs.
- Inconsistent coding practices.

**Recommendation:** Choose ESLint.

We will study ESLint separately when discussing code quality and tooling.

### Question 5: Would you like to use React Compiler?

Some versions of the Next.js CLI offer this option.

React Compiler automatically optimizes certain React components and their rendering behavior.

It can reduce the need for manually applying some optimizations.

**Recommendation:** Choose No initially. We can explore it in the advanced optimization phase.

### Question 6: Would you like to use Tailwind CSS?

Options:

```text
Yes / No
```

Tailwind CSS is a utility-first CSS framework.

Example:

```jsx
<h1 className="text-3xl font-bold text-blue-500">
  Hello Next.js
</h1>
```

Instead of writing separate CSS classes, you use predefined utility classes.

**Recommendation:** Choose Yes.

We will study Tailwind CSS in detail later.

### Question 7: Would you like your code inside a src/ directory?

Options:

```text
Yes / No
```

This determines where your application source code will be stored.

**Without src:**

```text
my-next-app/
│
├── app/
├── public/
├── package.json
└── next.config.ts
```

**With src:**

```text
my-next-app/
│
├── src/
│   └── app/
│
├── public/
├── package.json
└── next.config.ts
```

Both approaches are valid.

The `src` directory helps separate application source code from configuration files.

**Recommendation:** Choose Yes.

### Question 8: Would you like to use App Router?

Options:

```text
Yes / No
```

Next.js provides two routing systems:

1. App Router
2. Pages Router

App Router is the modern routing architecture.

It supports:

- Server Components.
- Client Components.
- Layouts.
- Nested routing.
- Server Actions.
- Streaming.
- Suspense.

**Recommendation:** Choose Yes.

We will primarily learn App Router throughout this roadmap.

### Question 9: Would you like to customize the import alias?

Options:

```text
Yes / No
```

An import alias allows you to import files using shorter paths.

For example, without an alias:

```javascript
import Header from "../../../../components/Header";
```

With an alias:

```javascript
import Header from "@/components/Header";
```

Here, `@` represents a configured path alias, commonly pointing to the `src` directory or project root.

**Recommendation:** Choose Yes or keep the default `@/*` alias.

---

# 6. Recommended Project Configuration

For our learning journey, use the following configuration.

| Configuration | Selection |
|---|---|
| Project Name | nextjs-learning |
| TypeScript | Yes |
| Linter | ESLint |
| React Compiler | No |
| Tailwind CSS | Yes |
| src Directory | Yes |
| App Router | Yes |
| Import Alias | @/* |

Some newer CLI versions may show additional questions, such as Cache Components or AGENTS.md. We will discuss those when relevant rather than changing advanced settings without understanding them.

The exact prompts can change between Next.js releases. [nextjs.org](https://nextjs.org/docs/app/api-reference/cli/create-next-app?utm_source=chatgpt.com)

---

# 7. Understanding the Installation Command Internally

Let's understand another way to create a project.

```bash
npx create-next-app@latest nextjs-learning
```

This command directly provides the project name.

You can also use:

```bash
npx create-next-app@latest nextjs-learning --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
```

This creates a project with the specified options without asking every configuration question.

However, the available CLI flags can vary by version.

### What Happens Internally?

```text
Execute Command
      |
      v
npx
      |
      v
Download / Execute create-next-app
      |
      v
Create Project Directory
      |
      v
Initialize package.json
      |
      v
Install Dependencies
      |
      v
Configure Next.js
      |
      v
Create Initial Files
      |
      v
Project Ready
```

---

# 8. Understanding package.json

After creating the project, open:

```text
package.json
```

You will see something similar to:

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
  },
  "dependencies": {
    "next": "16.x",
    "react": "19.x",
    "react-dom": "19.x"
  },
  "devDependencies": {
    "typescript": "...",
    "eslint": "...",
    "tailwindcss": "..."
  }
}
```

This is an illustrative example. Your installed versions and dependencies may differ.

Let's understand the important properties.

### 8.1 name

```json
"name": "nextjs-learning"
```

Specifies the project name.

### 8.2 version

```json
"version": "0.1.0"
```

Specifies the project's version.

### 8.3 private

```json
"private": true
```

Prevents accidentally publishing this package to the npm registry.

### 8.4 scripts

```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "eslint"
}
```

Defines commands that can be executed using npm.

We will understand these scripts in detail later.

### 8.5 dependencies

```json
"dependencies": {
  "next": "...",
  "react": "...",
  "react-dom": "..."
}
```

These are packages required by your application.

### 8.6 devDependencies

```json
"devDependencies": {
  "typescript": "...",
  "eslint": "..."
}
```

These are packages generally used during development and build tooling.

---

# 9. Understanding Important Dependencies

Let's understand the three most important dependencies.

### 9.1 next

```bash
npm install next
```

The Next.js framework itself.

It provides:

- Routing.
- Rendering.
- Server functionality.
- Optimization.
- Build tools.

### 9.2 react

```bash
npm install react
```

React provides:

- Components.
- JSX.
- Hooks.
- UI development.
- Component composition.

### 9.3 react-dom

```bash
npm install react-dom
```

React DOM provides the integration between React and the browser DOM.

It handles rendering React components into the DOM and related browser integration.

### Relationship

```text
          Next.js
             |
             v
           React
             |
             v
          React DOM
             |
             v
        Browser DOM
```

This is a simplified relationship, not the complete internal execution architecture.

---

# 10. What is node_modules?

After installing dependencies, you will see a folder:

```text
node_modules/
```

This folder contains installed packages and their dependencies.

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

### Why Does node_modules Exist?

Suppose you install React:

```bash
npm install react
```

npm downloads React and its required dependencies into `node_modules`.

When you write:

```javascript
import React from "react";
```

Your build tooling resolves the React package from the installed dependencies.

### Important Points

1. `node_modules` can contain thousands of files.
2. It can consume significant disk space.
3. It is generally not committed to Git.
4. It can be recreated using `npm install`.

### Why Don't We Upload node_modules to GitHub?

Because dependencies are already described in:

```text
package.json
package-lock.json
```

Other developers can install them using:

```bash
npm install
```

This avoids uploading a huge dependency folder.

---

# 11. What is package-lock.json?

When you install dependencies using npm, npm creates:

```text
package-lock.json
```

It records the exact dependency tree and resolved package versions used by the installation.

### Example

Suppose your package.json contains:

```json
{
  "dependencies": {
    "react": "^19.0.0"
  }
}
```

The caret (`^`) allows compatible updates within the specified major version range.

However, your package-lock.json records the exact resolved version.

### Why is package-lock.json Important?

It helps ensure consistent dependency installation across different environments.

For example:

- Your laptop.
- Another developer's computer.
- Production server.
- CI/CD environment.

### package.json vs package-lock.json

| package.json | package-lock.json |
|---|---|
| Declares dependencies | Records resolved dependencies |
| Contains version ranges | Contains exact resolved versions |
| Defines scripts | Records dependency tree |
| Usually manually maintained | Automatically maintained by npm |

**Best Practice:** Commit both files to Git.

---

# 12. Running Your First Next.js Application

Now let's execute our application.

### Step 1: Open Project Directory

```bash
cd nextjs-learning
```

### Step 2: Start Development Server

```bash
npm run dev
```

This executes:

```bash
next dev
```

You should see output similar to:

```text
▲ Next.js

- Local: http://localhost:3000
- Network: http://192.168.x.x:3000

✓ Starting...
✓ Ready
```

### Step 3: Open Browser

Visit:

```text
http://localhost:3000
```

You should see the default Next.js starter page.

Congratulations! Your first Next.js application is running.

---

# 13. Understanding npm run dev

This is an important concept.

When you execute:

```bash
npm run dev
```

npm looks inside the `scripts` property of package.json.

Example:

```json
{
  "scripts": {
    "dev": "next dev"
  }
}
```

npm executes:

```bash
next dev
```

### What Does next dev Do?

It starts the Next.js development server.

The development server provides:

- Local development environment.
- Fast Refresh.
- Development error reporting.
- Development-specific optimizations.
- Turbopack by default in modern Next.js versions.

### What is Fast Refresh?

Fast Refresh is a development feature that updates your application when you modify code.

For example:

You have:

```jsx
export default function Home() {
  return <h1>Hello World</h1>;
}
```

Change it to:

```jsx
export default function Home() {
  return <h1>Hello Next.js</h1>;
}
```

Save the file.

The browser updates automatically without requiring a manual page refresh in most cases.

### Development Server vs Production Server

| Development Server | Production Server |
|---|---|
| Used during development | Used after production build |
| Detailed error messages | Production-oriented output |
| Fast Refresh | No Fast Refresh |
| Development tooling | Optimized production output |
| `next dev` | `next start` |

---

# 14. Understanding npm run build

Once your application is developed, you need to prepare it for production.

Execute:

```bash
npm run build
```

This executes:

```bash
next build
```

### What Does next build Do?

It prepares your application for production.

The build process includes:

- Compiling application code.
- Processing React components.
- Optimizing JavaScript and CSS.
- Generating route output.
- Performing relevant type checks.
- Preparing production artifacts.

The exact operations depend on your project configuration and Next.js version.

### Build Output

After a successful build, Next.js generates:

```text
.next/
```

This directory contains production build artifacts and generated application information.

**Important:** Do not manually modify `.next` files.

### Development vs Build

```text
Development:

npm run dev
      |
      v
Development Server
      |
      v
localhost:3000
```

```text
Production:

npm run build
      |
      v
Production Build
      |
      v
.next Directory
```

---

# 15. Understanding npm run start

After successfully building your application, execute:

```bash
npm run start
```

This executes:

```bash
next start
```

It starts the production server.

### Important

You cannot normally run `next start` before creating a production build.

First execute:

```bash
npm run build
```

Then:

```bash
npm run start
```

Open:

```text
http://localhost:3000
```

Your production application should now be running.

### Complete Production Workflow

```text
Write Code
    |
    v
npm run dev
    |
    v
Development and Testing
    |
    v
npm run build
    |
    v
Production Build Generated
    |
    v
npm run start
    |
    v
Production Server Running
```

---

# 16. Understanding Important Next.js Commands

Let's summarize the commands.

| Command | Purpose |
|---|---|
| `npx create-next-app@latest` | Create a Next.js project |
| `npm install` | Install dependencies |
| `npm run dev` | Start development server |
| `npm run build` | Generate production build |
| `npm run start` | Start production server |
| `npm run lint` | Run ESLint if configured |
| `npm install package-name` | Install a dependency |
| `npm uninstall package-name` | Remove a dependency |
| `npm update` | Update dependencies within allowed version ranges |
| `npm list` | Display installed packages |
| `npm outdated` | Check outdated dependencies |

**Next.js 16 note:** The old `next lint` command has been removed. Use ESLint directly through your npm script. [nextjs.org](https://nextjs.org/docs/app/getting-started/installation?utm_source=chatgpt.com)

---

# 17. Understanding Important Project Configuration Files

After installation, you will find several configuration files.

Let's understand their purpose.

### 17.1 next.config.ts

This is the main Next.js configuration file.

Example:

```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  /* config options here */
};

export default nextConfig;
```

You can configure:

- Image domains and remote patterns.
- Redirects.
- Rewrites.
- Headers.
- Experimental features.
- Build behavior.

We will cover this file in detail in upcoming phases.

### 17.2 tsconfig.json

This file configures TypeScript.

Example:

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "strict": true,
    "noEmit": true,
    "moduleResolution": "bundler",
    "jsx": "preserve"
  }
}
```

Actual configuration may contain additional options.

### 17.3 eslint.config.mjs

This file configures ESLint.

It defines rules for identifying code quality issues and potential programming mistakes.

### 17.4 postcss.config.mjs

This file configures PostCSS.

PostCSS processes CSS using plugins.

Tailwind CSS uses PostCSS integration in the standard setup.

### 17.5 .gitignore

This file specifies files and directories that Git should ignore.

Example:

```text
node_modules/
.next/
.env*
```

This prevents unnecessary or sensitive files from being committed.

### 17.6 .env.local

This file stores local environment variables.

Example:

```env
DATABASE_URL=mongodb://localhost:27017/mydb

API_SECRET=my-secret-key
```

You can access these variables in server-side Node.js code through:

```javascript
process.env.DATABASE_URL
```

**Security:** Never expose private secrets through variables prefixed with `NEXT_PUBLIC_`.

Variables prefixed with `NEXT_PUBLIC_` are intended to be included in browser-side JavaScript.

We will study environment variables and security in detail later.

---

# 18. Understanding Development and Production Environments

Next.js applications generally operate in two major environments during local development and deployment.

### Development Environment

Used while developing the application.

Command:

```bash
npm run dev
```

Characteristics:

- Fast Refresh.
- Development error reporting.
- Development tooling.
- No production build required.

### Production Environment

Used when the application is ready for users.

Commands:

```bash
npm run build
npm run start
```

Characteristics:

- Production build.
- Optimized assets.
- Production runtime behavior.
- No development Fast Refresh.

### Comparison

| Feature | Development | Production |
|---|---|---|
| Command | `npm run dev` | `npm run build` + `npm run start` |
| Purpose | Development | Deployment |
| Fast Refresh | Yes | No |
| Build required | No | Yes |
| Error messages | Detailed | Production-oriented |
| Optimization | Development-focused | Production-focused |

---

# 19. What is Turbopack?

Turbopack is a JavaScript and CSS bundler developed by Vercel for the modern frontend development ecosystem.

Next.js uses Turbopack as its default bundler for development and production builds in current versions.

### What is a Bundler?

A bundler processes application resources and dependencies to prepare them for execution.

For example, your application may contain:

```text
app/
components/
styles/
images/
node_modules/
```

A bundler processes the relevant resources and prepares the application.

### Why Do We Need Turbopack?

It is designed to provide:

- Fast development builds.
- Efficient incremental compilation.
- Improved development performance.
- Better handling of large projects.

### Turbopack vs Webpack

| Turbopack | Webpack |
|---|---|
| Developed by Vercel | Established JavaScript bundler |
| Written in Rust | Primarily JavaScript |
| Designed for incremental compilation | Mature bundling ecosystem |
| Default in modern Next.js | Can still be selected |

You can explicitly use Webpack in Next.js 16:

```bash
npm run dev -- --webpack
```

And for production:

```bash
npm run build -- --webpack
```

For now, continue using the default Turbopack configuration.

---

# 20. Manual Installation of Next.js

Normally, we use `create-next-app`.

However, you should also understand how to install Next.js manually.

### Step 1: Create a Folder

```bash
mkdir manual-next-app

cd manual-next-app
```

### Step 2: Initialize npm

```bash
npm init -y
```

This generates:

```text
package.json
```

### Step 3: Install Next.js and React

```bash
npm install next@latest react@latest react-dom@latest
```

### Step 4: Configure Scripts

Inside package.json:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

### Step 5: Create App Directory

```text
manual-next-app/
│
├── app/
│   ├── layout.js
│   └── page.js
│
└── package.json
```

### Step 6: Create layout.js

```jsx
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

### Step 7: Create page.js

```jsx
export default function Home() {
  return (
    <h1>Hello Next.js</h1>
  );
}
```

### Step 8: Start Application

```bash
npm run dev
```

Visit:

```text
http://localhost:3000
```

Your manually configured application should work.

**Why learn manual installation?**

It helps you understand what `create-next-app` does behind the scenes.

Official installation documentation: [Next.js Installation Guide](https://nextjs.org/docs/app/getting-started/installation?utm_source=chatgpt.com)

---

# 21. Common Installation Errors

Let's understand some common errors.

### Error 1: Node.js Version Not Supported

Example:

```text
You are using Node.js 18.x.
Next.js requires Node.js 20.9.0 or later.
```

**Solution:**

Install a supported Node.js version.

Verify:

```bash
node -v
```

### Error 2: npm is Not Recognized

Example:

```text
'npm' is not recognized as an internal or external command.
```

Possible reasons:

- Node.js is not installed.
- Node.js is missing from the PATH environment variable.

**Solution:**

Reinstall Node.js with PATH configuration enabled, then reopen your terminal.

### Error 3: Port Already in Use

Example:

```text
Error: Port 3000 is already in use.
```

Possible reason:

Another application is already using port 3000.

**Solution:**

You can use another port:

```bash
npm run dev -- -p 3001
```

Now visit:

```text
http://localhost:3001
```

### Error 4: Cannot Find Module

Example:

```text
Module not found: Can't resolve 'some-package'
```

Possible reason:

The required package is not installed.

Solution:

```bash
npm install some-package
```

### Error 5: Build Failed

Example:

```text
Failed to compile
```

Possible reasons:

- Syntax errors.
- Missing dependencies.
- Incorrect imports.
- TypeScript errors.
- Invalid configuration.

Read the complete terminal error to identify the actual issue.

---

# 22. Practical Assignment – Phase 2

Now it's time to perform practical work.

### Task 1: Install Node.js

Verify:

```bash
node -v
npm -v
```

### Task 2: Create a Next.js Project

Create a project named:

```text
nextjs-learning
```

Use these settings:

- TypeScript: Yes
- ESLint: Yes
- React Compiler: No
- Tailwind CSS: Yes
- src directory: Yes
- App Router: Yes
- Import Alias: @/*

### Task 3: Run Development Server

```bash
npm run dev
```

Open:

```text
http://localhost:3000
```

### Task 4: Modify the Homepage

Open:

```text
src/app/page.tsx
```

Replace its content with:

```tsx
export default function Home() {
  return (
    <main>
      <h1>Welcome to Next.js Learning</h1>

      <p>
        I am learning Next.js from beginner to advanced level.
      </p>
    </main>
  );
}
```

Save the file and observe the browser.

### Task 5: Generate Production Build

Stop the development server using:

```text
Ctrl + C
```

Execute:

```bash
npm run build
```

### Task 6: Run Production Server

```bash
npm run start
```

Open:

```text
http://localhost:3000
```

Verify that your production application works.

---

# 23. Important Interview Questions

**Q1. What is create-next-app?**

It is an official CLI tool used to create and configure Next.js applications.

**Q2. What is the minimum Node.js version required by Next.js 16?**

Node.js 20.9.0.

**Q3. What is npm?**

npm is a package manager used to install, manage, and execute JavaScript project dependencies and scripts.

**Q4. What is the purpose of package.json?**

It stores project metadata, dependencies, development dependencies, and npm scripts.

**Q5. What is package-lock.json?**

It records the exact resolved dependency tree to support consistent installations.

**Q6. What is node_modules?**

It is the directory containing installed packages and their dependencies.

**Q7. What is the difference between npm run dev and npm run build?**

`npm run dev` starts the development server, whereas `npm run build` generates the production build.

**Q8. What is npm run start?**

It starts the production Next.js server using the previously generated production build.

**Q9. What is Turbopack?**

Turbopack is a bundler designed for efficient development and production builds.

**Q10. What is the purpose of next.config.ts?**

It provides configuration options for the Next.js application.

**Q11. What is the purpose of the .next directory?**

It contains generated build artifacts and application output.

**Q12. What is the purpose of the src directory?**

It separates application source code from root-level configuration files and other project resources.

**Q13. What is the purpose of .gitignore?**

It tells Git which files and directories should be excluded from version control.

**Q14. What is the difference between dependencies and devDependencies?**

Dependencies are required by the application, while devDependencies are generally required for development and build tooling.

**Q15. Can we install Next.js without create-next-app?**

Yes. We can manually install Next.js, React, and React DOM and configure the application ourselves.

---

# 24. Phase 2 – Quick Revision Notes

| Concept | Description |
|---|---|
| Node.js | JavaScript runtime |
| npm | Package manager |
| npx | Executes npm packages |
| create-next-app | Next.js project creation tool |
| package.json | Project metadata and dependencies |
| package-lock.json | Exact dependency resolution |
| node_modules | Installed packages |
| next.config.ts | Next.js configuration |
| tsconfig.json | TypeScript configuration |
| .gitignore | Files ignored by Git |
| .env.local | Local environment variables |
| npm run dev | Development server |
| npm run build | Production build |
| npm run start | Production server |
| Turbopack | JavaScript and CSS bundler |
| .next | Generated build directory |
| src | Application source directory |

---

# 25. Phase 2 Completion Checklist

| Topic | Status |
|---|---|
| Next.js Prerequisites | Covered |
| Node.js Installation | Covered |
| npm Fundamentals | Covered |
| create-next-app | Covered |
| Project Creation | Covered |
| TypeScript Configuration | Covered |
| ESLint Configuration | Covered |
| Tailwind Configuration | Covered |
| App Router Configuration | Covered |
| Import Alias | Covered |
| package.json | Covered |
| package-lock.json | Covered |
| node_modules | Covered |
| Development Server | Covered |
| Production Build | Covered |
| Production Server | Covered |
| Turbopack | Covered |
| Manual Installation | Covered |
| Common Errors | Covered |
| Practical Assignment | Assigned |
| Interview Questions | Covered |

---

## Next Phase: Phase 3 – Next.js Project Structure and File Conventions

In Phase 3, we will deeply understand:

1. Complete Next.js project structure.
2. Root directory.
3. `src` directory.
4. `app` directory.
5. `public` directory.
6. `node_modules` directory.
7. `.next` directory.
8. `package.json`.
9. `package-lock.json`.
10. `next.config.ts`.
11. `tsconfig.json`.
12. `eslint.config.mjs`.
13. `postcss.config.mjs`.
14. `.gitignore`.
15. `.env` and `.env.local`.
16. `page.tsx` and `layout.tsx`.
17. Special Next.js files and their purposes.
18. File and folder naming conventions.
19. Project organization best practices.
20. Real-world Next.js project architecture.

**Phase 2 is complete.**

When ready, say **"OK, Phase 3"**.