# Next.js Complete Learning Roadmap
# Phase 1: Introduction to Next.js

**Level:** Beginner  
**Difficulty:** Easy → Intermediate  
**Technology:** Next.js 16.x  
**Architecture:** App Router  
**Goal:** Understand Next.js fundamentals, architecture, rendering strategies, and why modern applications use Next.js.

---

# 1. What is Next.js?

**Next.js is an open-source React framework used to build full-stack web applications.**

It is built on top of React and provides additional features that make developing web applications easier, faster, and more efficient.

React primarily focuses on building user interfaces, whereas Next.js provides additional capabilities such as:

- File-based routing
- Server-Side Rendering (SSR)
- Static Site Generation (SSG)
- Incremental Static Regeneration (ISR)
- Server Components
- Client Components
- API development
- Server Actions
- Image optimization
- Font optimization
- SEO optimization
- Caching and data fetching
- Application deployment support

### Example

Suppose you want to build an e-commerce application.

Using React alone, you need to configure several things yourself:

- Routing
- SEO configuration
- Rendering strategies
- Backend API integration
- Build configuration
- Performance optimization

With Next.js, many of these capabilities are already provided by the framework.

You can focus more on developing your application instead of configuring everything manually.

### Simple Definition

**React is a JavaScript library for building user interfaces, whereas Next.js is a React framework for building complete web applications.**

---

# 2. Why Was Next.js Introduced?

To understand why Next.js was introduced, first understand some limitations of traditional React applications.

Consider a traditional React application created using Vite.

When a browser requests the application:

1. Browser requests the website.
2. Server sends an HTML file.
3. Browser downloads JavaScript files.
4. Browser executes JavaScript.
5. React generates the UI.
6. Browser displays the content.

This approach is commonly called Client-Side Rendering (CSR).

### Problems with Traditional Client-Side Rendering

**1. SEO Challenges**

Initially, the HTML document may contain very little meaningful content.

Search engine crawlers need to execute JavaScript to discover dynamically rendered content, which can introduce additional complexity.

**2. Initial Loading Performance**

Users may experience a blank screen or loading indicator while JavaScript downloads and executes.

**3. No Built-in Backend Architecture**

React itself does not provide a complete backend framework.

You generally need a separate backend such as Node.js and Express.

**4. Data Fetching Complexity**

You need to manage API requests, loading states, error states, and other data-fetching concerns yourself.

**5. Rendering Flexibility**

Traditional React applications generally rely on client-side rendering, although React itself supports other rendering approaches.

### How Next.js Helps

Next.js provides solutions through:

- Server-Side Rendering
- Static Site Generation
- Server Components
- Built-in routing
- Server-side data fetching
- Route Handlers
- Image optimization
- Metadata management

**Important:** Next.js does not automatically make every application faster or SEO-friendly. Proper architecture and implementation are still necessary.

---

# 3. What is a React Framework?

Before understanding Next.js, we need to understand the difference between a library and a framework.

### What is a Library?

A library is a collection of reusable code that provides specific functionality.

You call the library when you need its functionality.

You generally control how and when to use it.

Examples:

- React
- Axios
- Lodash
- Day.js

### What is a Framework?

A framework provides a predefined structure and conventions for developing applications.

It generally controls important parts of the application's execution flow.

You write your code according to the framework's conventions.

Examples:

- Next.js
- Angular
- Django
- Laravel
- NestJS

### Library vs Framework

| Library | Framework |
|---|---|
| Provides specific functionality | Provides an application structure |
| Developer controls the flow | Framework controls much of the flow |
| Usually requires additional tools | Often provides multiple built-in capabilities |
| More architectural freedom | More predefined conventions |
| Example: React | Example: Next.js |

### Understanding Through an Example

In React:

You decide how to configure routing using a library such as React Router.

In Next.js:

You create folders and files according to its routing conventions, and Next.js automatically creates routes.

This is an example of framework-driven development.

---

# 4. React vs Next.js

This is one of the most important concepts.

Both React and Next.js are used to build web applications, but their responsibilities are different.

| Feature | React | Next.js |
|---|---|---|
| Type | JavaScript Library | React Framework |
| UI Development | Yes | Yes |
| File-Based Routing | Not built in | Yes |
| Server Components | Supported through React architecture | Integrated |
| Client Components | Yes | Yes |
| SSR | Requires additional setup or framework | Built-in support |
| SSG | Requires additional setup or framework | Supported |
| ISR | Not built in | Supported |
| API Development | Requires a backend or other solution | Route Handlers |
| Server Actions | React Server Functions support | Integrated |
| Image Optimization | External solution | Built-in component |
| SEO | Requires additional implementation | Built-in metadata APIs |
| Backend Integration | External backend | Can run server-side code |
| Deployment | Multiple hosting options | Multiple hosting options |

### Example: Routing

**React with React Router**

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

You explicitly define routes.

**Next.js with App Router**

Project structure:

```text
app/
│
├── page.js
│
├── about/
│   └── page.js
│
└── contact/
    └── page.js
```

Next.js automatically creates routes based on the folder structure.

| File | URL |
|---|---|
| `app/page.js` | `/` |
| `app/about/page.js` | `/about` |
| `app/contact/page.js` | `/contact` |

No external routing library is required.

**Conclusion:** Next.js uses React for UI development and provides additional framework-level functionality.

---

# 5. What are the Main Features of Next.js?

Let's understand the important features.

### 5.1 File-Based Routing

Next.js automatically creates routes based on your project's folder and file structure.

Example:

```text
app/
├── page.js
├── about/
│   └── page.js
└── services/
    └── page.js
```

Routes:

```text
/
/about
/services
```

### 5.2 Server Components

Server Components allow React components to execute on the server.

Advantages:

- Direct database access from server-side code.
- Reduced client-side JavaScript.
- Access to server-side resources.
- Better separation of server and client responsibilities.

In the App Router, pages and layouts are Server Components by default.

### 5.3 Client Components

Client Components allow you to use browser-side functionality.

For example:

- useState
- useEffect
- Event handlers
- Browser APIs
- Interactive UI

You declare a Client Component using:

```jsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

We will study Server Components and Client Components in detail in Phases 9–11.

### 5.4 Server-Side Rendering

Next.js can render pages on the server and send HTML to the browser.

This can improve initial content delivery and search engine discoverability.

### 5.5 Static Site Generation

Next.js can generate HTML pages ahead of time.

These pages can then be served without generating their HTML from scratch for every request.

Useful for:

- Blogs
- Documentation
- Marketing websites
- Landing pages

### 5.6 API Development

Next.js allows you to create backend API endpoints using Route Handlers.

Example:

```text
app/
└── api/
    └── users/
        └── route.js
```

You can define GET, POST, PUT, PATCH and DELETE handlers.

### 5.7 Image Optimization

Next.js provides an Image component.

```jsx
import Image from "next/image";

export default function Home() {
  return (
    <Image
      src="/profile.jpg"
      alt="Profile"
      width={300}
      height={300}
    />
  );
}
```

It provides image optimization capabilities such as responsive sizing and optimized delivery.

### 5.8 SEO Optimization

Next.js provides Metadata APIs.

Example:

```jsx
export const metadata = {
  title: "Home Page",
  description: "Welcome to my website",
};

export default function Home() {
  return <h1>Welcome to Next.js</h1>;
}
```

You can configure:

- Page title
- Meta description
- Open Graph metadata
- Twitter metadata
- Canonical URLs
- Robots metadata

### 5.9 Built-in Font Optimization

Next.js provides `next/font` to optimize font loading.

Example:

```jsx
import { Geist } from "next/font/google";

const geist = Geist({
  subsets: ["latin"],
});

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className={geist.className}>
        {children}
      </body>
    </html>
  );
}
```

---

# 6. What is Client-Side Rendering (CSR)?

CSR stands for Client-Side Rendering.

In CSR, the browser executes JavaScript to generate the UI.

### How CSR Works

Consider a React application.

**Step 1:** Browser requests the website.

**Step 2:** Server sends HTML containing the application root.

Example:

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Website</title>
</head>
<body>
  <div id="root"></div>

  <script src="/assets/index.js"></script>
</body>
</html>
```

Notice that the root div is initially empty.

**Step 3:** Browser downloads JavaScript.

**Step 4:** React executes JavaScript.

**Step 5:** React generates components and updates the DOM.

**Step 6:** User sees the complete UI.

### CSR Flow

```text
Browser
   |
   | Request Website
   |
   v
Web Server
   |
   | Sends HTML + JS references
   |
   v
Browser
   |
   | Downloads JavaScript
   |
   v
JavaScript Execution
   |
   v
React Renders Components
   |
   v
DOM Updated
   |
   v
UI Displayed
```

### Advantages of CSR

- Smooth client-side navigation.
- Excellent interactivity.
- Less server-side rendering work.
- Suitable for highly interactive applications.

### Disadvantages of CSR

- Initial rendering can be delayed.
- Large JavaScript bundles can affect performance.
- SEO may require additional consideration.
- Browser performs more rendering work.

### Where is CSR Useful?

- Admin dashboards
- Internal business applications
- Interactive tools
- Applications behind authentication

---

# 7. What is Server-Side Rendering (SSR)?

SSR stands for Server-Side Rendering.

In SSR, HTML is generated on the server and then sent to the browser.

### How SSR Works

Suppose a user opens:

```text
https://example.com/products
```

The process:

1. Browser requests the products page.
2. Next.js receives the request.
3. Server executes the required server-side rendering logic.
4. Server generates HTML.
5. Server sends HTML to the browser.
6. Browser displays the HTML.
7. JavaScript loads where required.
8. React hydrates the interactive components.

### SSR Flow

```text
Browser
   |
   | Request /products
   |
   v
Next.js Server
   |
   | Fetch Required Data
   |
   v
Generate HTML
   |
   | Send HTML Response
   |
   v
Browser
   |
   | Display HTML
   |
   v
JavaScript Loads
   |
   v
Hydration
   |
   v
Interactive UI
```

### Example

Imagine an e-commerce website.

A user visits a product page.

The server fetches product information:

```javascript
async function getProduct() {
  const response = await fetch(
    "https://api.example.com/products/1"
  );

  return response.json();
}
```

Then the server renders the product information into HTML.

```jsx
export default async function ProductPage() {
  const product = await getProduct();

  return (
    <div>
      <h1>{product.name}</h1>
      <p>Price: ₹{product.price}</p>
    </div>
  );
}
```

This is a Server Component that fetches data on the server.

**Important:** This example demonstrates server-side data fetching, but it does not by itself guarantee that the route uses request-time dynamic SSR. Rendering and caching behavior depend on the Next.js version and configuration.

### Advantages of SSR

- HTML can contain meaningful content before client-side JavaScript executes.
- Useful for frequently changing data.
- Supports personalized pages.
- Can improve initial content delivery.

### Disadvantages of SSR

- Requires server-side computation.
- Can increase server workload.
- Performance depends on data-fetching and server response times.

### Where is SSR Useful?

- E-commerce product pages
- News websites
- Personalized dashboards
- Frequently updated content

---

# 8. What is Static Site Generation (SSG)?

SSG stands for Static Site Generation.

In SSG, HTML pages are generated ahead of time, usually during the build process.

The generated HTML files are then served to users.

### How SSG Works

Suppose you have a blog application containing 100 articles.

During the build process:

1. Next.js fetches article data.
2. Next.js generates HTML pages.
3. HTML files are stored as build output.
4. User requests an article.
5. Server or CDN sends the pre-generated HTML.
6. Browser displays the content.

### SSG Flow

```text
Developer
   |
   | Build Application
   |
   v
Next.js Build Process
   |
   | Fetch Data
   |
   v
Generate Static HTML
   |
   v
Build Output
   |
   v
User Requests Page
   |
   v
Static HTML Served
   |
   v
Browser Displays Page
```

### Example

Consider a blog application.

```jsx
export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>
      <p>Welcome to our website.</p>
    </div>
  );
}
```

In Next.js App Router, a route can be statically rendered when its rendering requirements allow it.

### Advantages of SSG

- Very fast content delivery.
- Reduced server computation per request.
- Excellent for CDN caching.
- Useful for SEO.
- Good scalability.

### Disadvantages of SSG

- Content can become outdated until regenerated or redeployed.
- Not ideal for highly personalized content without additional mechanisms.
- Large numbers of pages can increase build time.

### Where is SSG Useful?

- Blogs
- Documentation websites
- Portfolio websites
- Company websites
- Landing pages

---

# 9. What is Incremental Static Regeneration (ISR)?

ISR stands for Incremental Static Regeneration.

It allows statically generated pages to be updated after deployment without requiring a complete application rebuild.

This is particularly useful when your website contains content that changes occasionally.

### Example

Suppose you have a blog website.

You publish an article today.

You want the page to be statically generated, but you also want Next.js to refresh the cached page periodically.

You can use ISR.

### Example Code

```jsx
export const revalidate = 60;

export default async function BlogPage() {
  const response = await fetch(
    "https://api.example.com/blogs"
  );

  const blogs = await response.json();

  return (
    <div>
      <h1>Latest Blogs</h1>

      {blogs.map((blog) => (
        <h2 key={blog.id}>{blog.title}</h2>
      ))}
    </div>
  );
}
```

Here:

```javascript
export const revalidate = 60;
```

It configures a 60-second revalidation interval for the route's cached data/rendering behavior.

Conceptually:

1. Next.js generates the page.
2. The page is cached.
3. Users receive the cached page.
4. After the revalidation interval, a subsequent request can trigger regeneration.
5. The updated page is made available.

**Important:** Time-based revalidation is not a guarantee that regeneration happens exactly every 60 seconds. It is request-driven and subject to the caching configuration.

### Advantages of ISR

- Updates static content without rebuilding the entire website.
- Combines static delivery with content freshness.
- Reduces unnecessary server computation.
- Useful for large content websites.

### Where is ISR Useful?

- News websites
- E-commerce product catalogs
- Blogs
- Real estate listings
- Content management systems

---

# 10. CSR vs SSR vs SSG vs ISR

This is an extremely important comparison.

| Feature | CSR | SSR | SSG | ISR |
|---|---|---|---|---|
| Full Name | Client-Side Rendering | Server-Side Rendering | Static Site Generation | Incremental Static Regeneration |
| Rendering Location | Browser | Server | Build time | Build time + regeneration |
| HTML Generation | Browser | Per request | Before requests | Initially + regeneration |
| Server Work | Relatively low | Per request | Mostly at build/regeneration | During regeneration |
| Data Freshness | Depends on client fetching | Can be request-time fresh | Depends on regeneration/deployment | Updated through revalidation |
| SEO | Requires consideration | Good support | Good support | Good support |
| Best Use Case | Interactive dashboards | Personalized content | Static content | Periodically changing content |

### Real-World Example

Imagine you are building an e-commerce website.

| Page | Suitable Rendering Strategy |
|---|---|
| Homepage | SSG / ISR |
| Product Listing | SSG / ISR / SSR |
| Product Details | SSG / ISR / SSR |
| User Profile | Dynamic SSR or CSR |
| Shopping Cart | Usually CSR or dynamic rendering |
| Admin Dashboard | Usually CSR or dynamic rendering |

Remember: Next.js allows different rendering strategies for different routes and components.

You do not have to use one rendering strategy for the entire application.

---

# 11. What is Hydration?

Hydration is one of the most important concepts in Next.js and React.

### Definition

**Hydration is the process through which React attaches event handlers and initializes interactivity on HTML that was already rendered on the server.**

Let's understand it with an example.

Suppose Next.js renders this component on the server:

```jsx
export default function Counter() {
  return (
    <button>
      Click Me
    </button>
  );
}
```

The server generates HTML:

```html
<button>Click Me</button>
```

The browser receives and displays this HTML.

At this point, the button is visible, but it does not have any click functionality.

Now React loads the required JavaScript and hydrates the component.

After hydration, React can attach the event handlers and make the component interactive.

### Hydration Example

```jsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

### Hydration Flow

```text
Server
   |
   | Render React Component
   |
   v
Generate HTML
   |
   v
Browser Receives HTML
   |
   v
HTML Displayed
   |
   v
JavaScript Downloaded
   |
   v
React Hydration
   |
   v
Event Handlers Attached
   |
   v
Interactive Application
```

### What is Hydration Mismatch?

A hydration mismatch occurs when the HTML generated on the server does not match what React expects during the initial client render.

Example:

```jsx
"use client";

export default function Home() {
  return (
    <h1>{Math.random()}</h1>
  );
}
```

The server and browser may generate different random numbers.

For example:

Server:

```html
<h1>0.12345</h1>
```

Browser:

```html
<h1>0.98765</h1>
```

This can produce a hydration mismatch.

**Best Practice:** Avoid using values that produce different output between server rendering and the initial client rendering.

---

# 12. How Next.js Works Internally (High-Level Overview)

Let's understand the overall architecture.

Next.js uses several technologies and processes.

### Important Components

**1. React**

Responsible for:

- Components
- JSX
- UI rendering
- Component composition
- State management

**2. Next.js Framework**

Responsible for:

- Routing
- Rendering orchestration
- Server-side functionality
- Build process
- Optimization
- Caching

**3. Node.js Runtime**

Can execute server-side Next.js application code.

**4. Browser**

Responsible for:

- Displaying HTML
- Executing client-side JavaScript
- Handling user interactions
- Updating the DOM

### Architecture Diagram

```text
                 USER
                   |
                   |
              Web Browser
                   |
                   | HTTP Request
                   |
                   v
             NEXT.JS SERVER
                   |
          +--------+--------+
          |                 |
          v                 v
      App Router       Route Handlers
          |                 |
          v                 v
    React Components    API Logic
          |
     +----+----+
     |         |
     v         v
   Server    Client
 Components Components
     |         |
     v         v
 Database   Browser
     |
     v
 Generate HTML / RSC Payload
     |
     v
 Send Response
     |
     v
 Browser Rendering
     |
     v
 Hydration (where needed)
     |
     v
 Interactive Application
```

This is a simplified conceptual architecture. Actual rendering and response handling involve additional framework internals.

---

# 13. When Should We Use Next.js?

Next.js is particularly useful when developing applications that benefit from server-side capabilities, routing conventions, and flexible rendering.

### Recommended Use Cases

| Application | Why Next.js? |
|---|---|
| E-commerce | SEO, product pages, server-side data fetching |
| Blog | Static rendering and SEO |
| Portfolio | Static rendering and metadata |
| SaaS Application | Full-stack architecture |
| Admin Dashboard | React UI and backend integration |
| CRM | Server-side data access |
| News Website | Rendering flexibility |
| Real Estate Website | SEO and dynamic content |
| Educational Platform | Authentication and server-side logic |

### Example

Suppose you are developing an e-commerce application.

You need:

- Product pages
- Product search
- Authentication
- Shopping cart
- Payment integration
- Admin panel
- SEO
- Backend APIs

Next.js can handle much of the frontend and backend application architecture within one project.

You can also integrate external services and databases.

---

# 14. When Should We Not Use Next.js?

Next.js is powerful, but it is not necessary for every project.

### Situations Where React + Vite May Be Better

**1. Small Interactive Applications**

For example:

- Calculator
- To-do application
- Simple expense calculator

**2. Internal Dashboard**

If your application is only accessible to authenticated employees and SEO is irrelevant, a React SPA may be sufficient.

**3. Simple Frontend Projects**

If you only need a frontend that communicates with an existing backend API, React with Vite may be simpler.

**4. Applications Without Server-Side Requirements**

If you do not need server rendering, server-side data access, or Next.js routing conventions, Next.js may introduce unnecessary complexity.

**Important:** Next.js can still be used in these scenarios. The point is to select the architecture that best matches the application's requirements.

---

# 15. Next.js vs Other React Frameworks

Let's briefly compare Next.js with other popular React development approaches.

| Feature | Next.js | React + Vite | Remix / React Router Framework |
|---|---|---|---|
| Type | React Framework | Build Tool + React Library | React Framework |
| File-Based Routing | Yes | Requires additional library | Supported |
| SSR | Yes | Requires additional setup | Yes |
| SSG | Yes | Requires additional setup | Different approach |
| Backend Features | Yes | Requires separate backend | Yes |
| Server Components | Integrated | Not provided by Vite itself | Depends on framework support |
| Best For | Full-stack React apps | Client-side applications | Full-stack React apps |

### What is Vite?

Vite is a frontend build tool.

It provides:

- Development server
- Fast development experience
- Module bundling
- Production builds

Vite itself is not a React framework.

You can use Vite with React to build applications.

### What is React Router?

React Router is a routing library for React.

It also provides framework capabilities through its framework mode.

We will not go deeply into other frameworks because our primary focus is Next.js.

---

# 16. Next.js Architecture: Important Terminology

Before proceeding to the next phase, understand these terms.

| Term | Meaning |
|---|---|
| App Router | Modern routing system based on the `app` directory |
| Pages Router | Older routing system based on the `pages` directory |
| Server Component | Component rendered using server-side React capabilities |
| Client Component | Component that supports client-side interactivity |
| Route Handler | Backend HTTP endpoint in Next.js |
| Server Action | Server-side function that can be invoked through React/Next.js mechanisms |
| Layout | Shared UI structure for multiple routes |
| Page | UI associated with a particular route |
| Middleware / Proxy | Request interception and processing mechanism |
| Hydration | Connecting React interactivity to existing server-rendered HTML |
| RSC Payload | Serialized representation of React Server Component output |
| Streaming | Sending rendered UI progressively rather than waiting for everything |
| Revalidation | Updating cached data or rendered output |

We will cover each of these concepts in detail in upcoming phases.

---

# 17. Important Interview Questions

### Q1. What is Next.js?

Next.js is an open-source React framework used to build full-stack web applications. It provides features such as routing, server-side rendering, static generation, server components, API development, and optimization.

### Q2. Is Next.js a library or a framework?

Next.js is a React framework.

React is a JavaScript library for building user interfaces.

### Q3. What is the difference between React and Next.js?

React focuses on UI development, whereas Next.js extends React with routing, server-side functionality, rendering strategies, caching, and optimization features.

### Q4. What is CSR?

Client-Side Rendering is a rendering approach where JavaScript executes in the browser to generate the UI.

### Q5. What is SSR?

Server-Side Rendering is an approach where HTML is generated on the server and sent to the browser.

### Q6. What is SSG?

Static Site Generation is the process of generating HTML pages ahead of time, usually during the build process.

### Q7. What is ISR?

Incremental Static Regeneration allows statically generated pages to be updated after deployment through revalidation.

### Q8. What is hydration?

Hydration is the process through which React attaches event handlers and initializes interactivity on existing server-rendered HTML.

### Q9. Can Next.js be used as a backend?

Yes. Next.js supports backend functionality through Route Handlers, Server Actions, and server-side application code.

### Q10. Does Next.js replace React?

No. Next.js is built on React and uses React for UI development.

### Q11. What is the App Router?

The App Router is Next.js's modern routing system based on the `app` directory.

### Q12. Does Next.js support both SSR and SSG?

Yes. Next.js supports multiple rendering strategies, including dynamic server rendering and static generation.

### Q13. Is Next.js good for SEO?

Yes. Next.js provides server rendering, static generation, and metadata APIs that help build SEO-friendly applications.

### Q14. What is the difference between SSR and SSG?

SSR generates HTML when a request is processed, whereas SSG generates HTML ahead of time.

### Q15. Can we use Next.js with MongoDB and MySQL?

Yes. Next.js server-side code can communicate with MongoDB, MySQL, PostgreSQL, and other databases using their respective drivers or ORMs.

---

# 18. Phase 1 – Practical Exercises

Try answering these questions yourself.

**Conceptual Questions**

1. What is Next.js, and why do we use it?
2. What is the difference between a library and a framework?
3. Why is Next.js built on top of React?
4. What problems does Next.js solve?
5. What is the difference between CSR and SSR?
6. What is the difference between SSR and SSG?
7. What is ISR, and why do we need it?
8. What is hydration?
9. Can Next.js replace Node.js and Express completely?
10. Can we connect MongoDB directly with Next.js?

**Scenario-Based Questions**

11. You are building a blog website. Which rendering strategy would you consider and why?

12. You are building an admin dashboard. Would you choose CSR or SSR?

13. You are building an e-commerce website with thousands of products. Which rendering strategies would you consider?

14. You have a website where product prices change frequently. How would you ensure users see updated prices?

15. You are building a portfolio website. Would you choose React + Vite or Next.js? Explain your decision.

---

# 19. Phase 1 – What You Should Understand

After completing this phase, you should be comfortable with:

- What Next.js is.
- Why Next.js exists.
- React vs Next.js.
- Library vs Framework.
- Main Next.js features.
- CSR, SSR, SSG and ISR.
- Hydration.
- High-level Next.js architecture.
- When to use Next.js.

### Phase 1 Completion Checklist

| Topic | Status |
|---|---|
| Introduction to Next.js | Covered |
| Why Next.js? | Covered |
| Library vs Framework | Covered |
| React vs Next.js | Covered |
| Next.js Features | Covered |
| CSR | Covered |
| SSR | Covered |
| SSG | Covered |
| ISR | Covered |
| Hydration | Covered |
| Architecture | Covered |
| Interview Questions | Covered |
| Practical Exercises | Assigned |

---

## Next: Phase 2 – Next.js Installation and Project Setup

In Phase 2, we will cover:

1. Prerequisites for Next.js.
2. Node.js and npm requirements.
3. What is `create-next-app`?
4. Installing Next.js.
5. Creating your first Next.js project.
6. Understanding installation options.
7. App Router vs Pages Router during setup.
8. JavaScript vs TypeScript configuration.
9. ESLint configuration.
10. Tailwind CSS configuration.
11. Running the development server.
12. Understanding `npm run dev`.
13. Production build using `npm run build`.
14. Running production using `npm start`.
15. Understanding Next.js development and production environments.

**Phase 1 is complete.** 

When you're ready, say **"OK, Phase 2"**.