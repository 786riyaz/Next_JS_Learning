# **Next.js Complete Learning Roadmap – Phase 9**

## **Server Components and Client Components – Deep Dive**

Phase 9

Intermediate–Advanced

Next.js 16.x

App Router

TypeScript

## **1. Learning objectives**

This is one of the most important phases in Next.js because understanding Server Components and Client Components is essential for building professional applications.

By the end of this phase, you should understand:

1. What React Server Components (RSC) are.
2. What Client Components are.
3. The difference between Server and Client Components.
4. The purpose of the `"use client"` directive.
5. Where components execute.
6. How Server and Client Components work together.
7. How to pass props between component types.
8. What serializable props mean.
9. What hydration is.
10. How client-side JavaScript affects performance.
11. What Server Components cannot do.
12. How to choose the right component architecture.

# **2. First, Understand the Problem Server Components Solve**

Before Server Components, let's understand a traditional React application.

In a typical client-rendered React application:

1. The browser downloads the HTML.
2. The browser downloads JavaScript.
3. React executes the JavaScript.
4. React renders the components.
5. The user interacts with the application.

For example:

```
// App.tsx

import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>Hello React</h1>

      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
    </div>
  );
}
```

The browser needs JavaScript to execute the React component and handle its interactions.

### **Potential problems with large client-rendered applications**

- More JavaScript can increase download and execution costs.
- Components that only display static data may not need browser-side JavaScript.
- Database access must happen through a separate backend/API layer.
- Sensitive server-side operations cannot safely run in the browser.

Next.js uses React Server Components to provide another way to render components.

# **3. What are React Server Components?**

**React Server Components (RSC)** are React components that execute in a server environment and produce a representation of the UI that can be sent to the client.

In Next.js App Router, components are Server Components by default unless you opt into Client Component behavior.

### **Example**

```
// app/products/page.tsx

export default async function ProductsPage() {
  const response = await fetch(
    "https://api.example.com/products"
  );

  const products = await response.json();

  return (
    <div>
      <h1>Products</h1>

      {products.map((product: any) => (
        <p key={product.id}>
          {product.name}
        </p>
      ))}
    </div>
  );
}
```

Notice something important:

- The component is an `async` function.
- It fetches data on the server.
- It does not need `"use client"`.
- It can access server-side resources through suitable server libraries.

### **How does it work?**

**Server Component execution**

**Next.js Server**

Executes the Server Component

Fetches data → Renders component

RSC payload and rendered output

**Browser**

Displays the resulting UI

The browser does not need to execute the Server Component itself.

**Important:** Server Components are not simply components that render HTML on a server. They participate in React's Server Component architecture, which uses the React Server Component payload to describe the rendered component tree and its relationships to Client Components.

# **4. What are Client Components?**

Client Components are components that participate in client-side React functionality.

They are used when you need browser-side interactions and features such as:

- `useState`
- `useEffect`
- Event handlers
- Browser APIs
- Interactive forms
- Client-side navigation hooks
- Interactive UI state

To define a Client Component in Next.js, add:

```
"use client";
```

at the top of the module.

### **Example**

```
// app/components/Counter.tsx

"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Counter: {count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

### **Why do we need&#x20;**`"use client"`**?**

Because the component needs client-side React functionality.

Without the directive, a component in the App Router is treated as a Server Component by default.

### **Important**

`"use client"` is a module boundary directive. It marks the module and its transitive dependencies as part of the client module graph.

It is not necessary to add `"use client"` to every component that is rendered inside a Client Component.

We will explore this distinction in detail later.

# **5. Server Components vs Client Components**

This is the most important comparison in this phase.

| Feature                                   | Server Component                             | Client Component                                                  |
| ----------------------------------------- | -------------------------------------------- | ----------------------------------------------------------------- |
| Default in App Router                     | Yes                                          | No                                                                |
| Executes on server                        | Yes                                          | Participates in browser-side rendering and hydration              |
| Can use `useState`                        | No                                           | Yes                                                               |
| Can use `useEffect`                       | No                                           | Yes                                                               |
| Can use event handlers                    | No                                           | Yes                                                               |
| Can directly access server-only resources | Yes, in suitable server code                 | No                                                                |
| Can use browser APIs                      | No                                           | Yes, in browser-side code                                         |
| Can fetch data                            | Yes                                          | Yes, through client-side mechanisms                               |
| Can access environment secrets            | Yes, with appropriate server-only protection | No                                                                |
| Can be async component                    | Yes                                          | Client Components cannot be declared as async function components |
| Requires `"use client"`                   | No                                           | Yes, at the client entry point                                    |

### **Simple rule**

Use a Server Component when you need:

- Database access.
- Server-side data fetching.
- Secure server-side logic.
- Static or data-driven UI without browser interactions.

Use a Client Component when you need:

- State.
- Effects.
- Event handlers.
- Browser APIs.
- Interactive UI.

# **6. Understanding the&#x20;**`"use client"`**&#x20;Directive**

Let's examine this directive more carefully.

```
"use client";
```

It must be placed at the top of a JavaScript or TypeScript module, before imports.

### **Correct**

```
"use client";

import { useState } from "react";

export default function Button() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

### **Incorrect**

```
import { useState } from "react";

"use client";

export default function Button() {
  // ...
}
```

The directive must appear before imports.

### **What exactly happens when you add it?**

Suppose you have:

```
components/
├── Counter.tsx
├── Button.tsx
└── Card.tsx
```

And `Counter.tsx` contains:

```
"use client";

import Button from "./Button";
import Card from "./Card";

export default function Counter() {
  return (
    <Card>
      <Button />
    </Card>
  );
}
```

`Counter.tsx` is a Client Component entry point.

Its imported dependencies are included in the client module graph, unless a module is explicitly protected as server-only.

This means `Button.tsx` and `Card.tsx` are also part of that client graph in this import chain, even if they don't individually contain `"use client"`.

**Important:** `"use client"` is not a label you need to add to every descendant file. It establishes a boundary in the module dependency graph.

# **7. Where Do Server and Client Components Execute?**

This is a common source of confusion.

A Server Component executes on the server. A Client Component participates in client-side interactivity, but that does **not** mean it is rendered exclusively in the browser.

In Next.js, Client Components can also be pre-rendered on the server to generate the initial HTML.

### **Initial page load**

When a user opens a Next.js page for the first time, the process is broadly:

1. Next.js renders Server Components on the server.
2. Client Components are also pre-rendered to generate initial HTML.
3. Next.js sends HTML to the browser so the user can see the page.
4. The browser receives the React Server Component (RSC) payload.
5. The browser downloads the JavaScript needed by Client Components.
6. React hydrates the Client Components to make them interactive.

**Initial page load**

**1. Server**

Render Server Components and pre-render Client Components.

HTML + RSC payload + client JavaScript references

**2. Browser**

Displays the HTML and loads the required JavaScript.

**3. Hydration**

React attaches client-side behavior to Client Components.

**Interactive application**

Buttons, state, event handlers and effects work.

### **What happens during subsequent navigation?**

When navigating between routes using Next.js client-side navigation:

- Next.js can request the required RSC payload.
- Shared layouts can be preserved.
- The browser updates the relevant UI.
- Client Components remain interactive and are reconciled by React.

This is different from a traditional full-page navigation.

### **Important distinction**

| Concept                   | Meaning                                                                      |
| ------------------------- | ---------------------------------------------------------------------------- |
| Server Component          | Component rendered in the server environment                                 |
| Client Component          | Component included in the client module graph and hydrated for interactivity |
| Server-side pre-rendering | Producing initial HTML on the server                                         |
| Hydration                 | Making pre-rendered Client Component UI interactive in the browser           |

**Remember:** Client Component does not mean browser-only rendering.

# **8. What is Hydration?**

Hydration is the process in which React attaches event handling and other client-side behavior to HTML that has already been rendered.

Let's understand it with an example.

### **Client Component**

```
// app/components/Counter.tsx

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

### **What happens?**

**Step 1:** The server can pre-render the initial button HTML.

```
<button>Count: 0</button>
```

**Step 2:** The browser displays the button.

**Step 3:** React loads the required JavaScript and hydrates the component.

**Step 4:** The click handler becomes active.

**Step 5:** When the user clicks the button, the state updates and the UI displays:

```
Count: 1
```

### **Why is hydration important?**

Without hydration, the pre-rendered HTML can be visible, but the Client Component's React event handlers and interactive behavior are not yet attached.

### **Hydration mismatch**

A hydration mismatch occurs when the HTML generated during server rendering does not match what React expects during the initial client render.

Example of problematic code:

```
"use client";

export default function CurrentTime() {
  return <p>{new Date().toLocaleTimeString()}</p>;
}
```

The server and browser may produce different times, causing a mismatch.

### **Better approach**

If the value should only be calculated in the browser, render a consistent initial value and update it in an effect.

```
"use client";

import { useEffect, useState } from "react";

export default function CurrentTime() {
  const [time, setTime] = useState("");

  useEffect(() => {
    setTime(new Date().toLocaleTimeString());
  }, []);

  return <p>{time || "Loading time..."}</p>;
}
```

The server and initial browser render both display `"Loading time..."`. After hydration, the effect updates the time.

# **9. Server Components Can Render Client Components**

A Server Component can import and render a Client Component.

This is one of the most useful patterns in Next.js.

### **Example**

```
app/
├── page.tsx
└── components/
    └── Counter.tsx
```

### **Client Component**

```
// app/components/Counter.tsx

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

### **Server Component**

```
// app/page.tsx

import Counter from "./components/Counter";

export default function HomePage() {
  return (
    <main>
      <h1>Welcome to Next.js</h1>

      <p>This content is rendered by a Server Component.</p>

      <Counter />
    </main>
  );
}
```

### **What is happening?**

- `HomePage` is a Server Component.
- `Counter` is a Client Component.
- The Server Component renders the Client Component.
- The Counter becomes interactive in the browser.

**Component relationship**

**HomePage**

Server Component

**Counter**

Client Component

Interactive state and click handler

**Important:** Importing a Client Component into a Server Component does not turn the entire parent Server Component into a Client Component.

# **10. Can a Client Component Render a Server Component?**

This requires understanding the difference between the component import graph and the rendered component tree.

A Client Component should not directly import a Server Component and expect it to execute on the server.

For example, this is not a valid way to preserve Server Component behavior:

```
// ClientComponent.tsx

"use client";

import ServerComponent from "./ServerComponent";

export default function ClientComponent() {
  return <ServerComponent />;
}
```

Because `ServerComponent` is imported into the client module graph, it cannot remain a Server Component in that import relationship.

### **Correct approach: Component composition**

You can render a Server Component from a Server Component parent and pass it as a prop to a Client Component.

The most common example uses `children`.

### **Server Component**

```
// app/page.tsx

import ClientModal from "./components/ClientModal";
import ServerProductInfo from "./components/ServerProductInfo";

export default function Page() {
  return (
    <ClientModal>
      <ServerProductInfo />
    </ClientModal>
  );
}
```

### **Server Product Information**

```
// app/components/ServerProductInfo.tsx

export default async function ServerProductInfo() {
  const product = await getProduct();

  return (
    <div>
      <h2>{product.name}</h2>
      <p>{product.description}</p>
    </div>
  );
}
```

### **Client Modal**

```
// app/components/ClientModal.tsx

"use client";

import { useState } from "react";

export default function ClientModal({
  children,
}: {
  children: React.ReactNode;
}) {
  const [open, setOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setOpen(!open)}>
        Toggle Product Information
      </button>

      {open && (
        <div className="modal">
          {children}
        </div>
      )}
    </div>
  );
}
```

### **What happens here?**

1. The Server Component parent renders `ServerProductInfo`.
2. The product information is fetched on the server.
3. The rendered Server Component result is passed as `children`.
4. The Client Component controls whether that content is displayed.

The Client Component does not import or execute the Server Component itself.

**Important:** The `children` prop is a common example of passing a Server Component result into a Client Component. The same composition pattern can be used with other props.

# **11. Understanding the Component Import Graph**

This concept is especially important for professional Next.js development.

Consider this folder structure:

```
components/
├── Header.tsx
├── Navbar.tsx
├── SearchButton.tsx
└── ProductList.tsx
```

Suppose:

```
// Header.tsx

import Navbar from "./Navbar";
import ProductList from "./ProductList";

export default function Header() {
  return (
    <header>
      <Navbar />
      <ProductList />
    </header>
  );
}
```

Now imagine `Navbar.tsx` is a Client Component:

```
// Navbar.tsx

"use client";

import SearchButton from "./SearchButton";

export default function Navbar() {
  return (
    <nav>
      <SearchButton />
    </nav>
  );
}
```

The module graph looks like this:

**Import dependency graph**

**Header.tsx**

Server

**Navbar.tsx**

Client entry

**ProductList.tsx**

Server

**SearchButton.tsx**

Client dependency

The important rules:

- `Header.tsx` remains a Server Component.
- `Navbar.tsx` is a Client Component.
- `SearchButton.tsx` is included in the client module graph through `Navbar`.
- `ProductList.tsx` remains a Server Component because it is imported directly by the Server Component `Header`.

The `"use client"` directive does not automatically convert every component in the application into a Client Component.

It marks a boundary in the module dependency graph.

# **12. Passing Props Between Server and Client Components**

Server Components can pass props to Client Components.

However, the props must be supported by React's serialization mechanism.

### **Example**

```
// app/page.tsx

import ProductCard from "./components/ProductCard";

export default async function Page() {
  const product = {
    id: 101,
    name: "Laptop",
    price: 75000,
  };

  return <ProductCard product={product} />;
}
```

### **Client Component**

```
// app/components/ProductCard.tsx

"use client";

import { useState } from "react";

type Product = {
  id: number;
  name: string;
  price: number;
};

export default function ProductCard({
  product,
}: {
  product: Product;
}) {
  const [added, setAdded] = useState(false);

  return (
    <div>
      <h2>{product.name}</h2>

      <p>₹{product.price}</p>

      <button onClick={() => setAdded(true)}>
        {added ? "Added" : "Add to Cart"}
      </button>
    </div>
  );
}
```

The Server Component passes product data to the Client Component.

The Client Component uses that data and adds interactive behavior.

### **Common serializable values**

React's Server Component transport supports a range of values, including:

- Strings
- Numbers
- Booleans
- `null`
- Arrays of supported values
- Plain objects containing supported values
- Some built-in JavaScript types supported by React's serialization protocol
- React elements and supported promises in the appropriate contexts

Avoid assuming that every JavaScript value can be passed across the boundary.

### **What about functions?**

Ordinary functions cannot be passed from a Server Component to a Client Component as normal props.

For example:

```
// This is not a valid ordinary Server-to-Client prop.

<ProductCard
  product={product}
  onClick={() => console.log("Clicked")}
/>
```

A normal function is not serializable.

For interactions, define the event handler inside the Client Component.

For server-side mutations, use Server Actions, which are a special supported mechanism.

# **13. What are Server Actions?**

Server Actions are asynchronous server-side functions that can be invoked from supported client interactions.

They are useful when a Client Component needs to perform an operation on the server, such as:

- Creating a product.
- Updating a user.
- Submitting a form.
- Deleting an order.
- Saving data to a database.

A Server Action can be defined using the `"use server"` directive.

### **Example**

```
// app/actions.ts

"use server";

export async function addProduct(formData: FormData) {
  const name = formData.get("name");

  // Validate input.
  // Save the product to the database.

  console.log("Product name:", name);
}
```

### **Client Component**

```
// app/components/AddProductForm.tsx

"use client";

import { addProduct } from "../actions";

export default function AddProductForm() {
  return (
    <form action={addProduct}>
      <input
        type="text"
        name="name"
        placeholder="Product name"
      />

      <button type="submit">
        Add Product
      </button>
    </form>
  );
}
```

### **What happens?**

1. The user submits the form.
2. The browser invokes the Server Action.
3. Next.js executes the action on the server.
4. The server performs the required operation.
5. The result is returned through the framework's action mechanism.

### **Important security point**

Server Actions are server-side entry points, not automatically protected business logic.

Always:

- Validate input.
- Verify authentication.
- Check authorization.
- Avoid trusting values submitted by the browser.
- Protect sensitive operations.

We will study Server Actions in depth in a later phase.

# **14. Server Component Limitations**

Server Components have some important limitations.

| Feature                           | Available in Server Components?        |
| --------------------------------- | -------------------------------------- |
| Database queries                  | Yes, through suitable server libraries |
| Server-side `fetch()`             | Yes                                    |
| Read server environment variables | Yes                                    |
| `useState`                        | No                                     |
| `useEffect`                       | No                                     |
| `onClick` event handler           | No                                     |
| `window`                          | No                                     |
| `document`                        | No                                     |
| `localStorage`                    | No                                     |
| Browser geolocation               | No                                     |
| Server-side authentication checks | Yes                                    |
| Async component functions         | Yes                                    |

### **Incorrect example**

```
// app/page.tsx

import { useState } from "react";

export default function Page() {
  const [count, setCount] = useState(0);

  return <button>{count}</button>;
}
```

This will fail because a Server Component cannot use `useState`.

### **Correct example**

```
"use client";

import { useState } from "react";

export default function Page() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

# **15. Client Component Limitations**

Client Components also have limitations.

| Feature                                                | Client Component                        |
| ------------------------------------------------------ | --------------------------------------- |
| `useState`                                             | Yes                                     |
| `useEffect`                                            | Yes                                     |
| Event handlers                                         | Yes                                     |
| Browser APIs                                           | Yes, in browser-safe execution contexts |
| Direct database connection                             | No                                      |
| Private server environment variables                   | No                                      |
| Direct access to server-only secrets                   | No                                      |
| Import Server Component as a server-rendered component | No                                      |
| Async function component                               | No                                      |

### **Important: Do not access browser APIs during initial rendering**

This is risky:

```
"use client";

export default function Theme() {
  const theme = localStorage.getItem("theme");

  return <p>{theme}</p>;
}
```

Why?

Client Components can be pre-rendered on the server during the initial page load. `localStorage` does not exist there.

### **Safer approach**

```
"use client";

import { useEffect, useState } from "react";

export default function Theme() {
  const [theme, setTheme] = useState("light");

  useEffect(() => {
    const savedTheme = localStorage.getItem("theme");

    if (savedTheme) {
      setTheme(savedTheme);
    }
  }, []);

  return <p>Theme: {theme}</p>;
}
```

The browser API is accessed inside `useEffect`, which runs in the browser after hydration.

# **16. The&#x20;**`"use client"`**&#x20;Boundary and Bundle Size**

Every Client Component entry point contributes to the client-side JavaScript module graph.

This matters because the browser has to download and execute the JavaScript required for client-side functionality.

Consider two approaches.

### **Approach A: Make the whole page a Client Component**

```
"use client";

import { useState } from "react";

export default function ProductPage() {
  const [quantity, setQuantity] = useState(1);

  return (
    <main>
      <h1>Laptop</h1>
      <p>Product description...</p>

      <button onClick={() => setQuantity(quantity + 1)}>
        Quantity: {quantity}
      </button>
    </main>
  );
}
```

Everything in this module belongs to the client module graph, including its imports.

### **Approach B: Keep the page on the server**

```
// app/products/[id]/page.tsx

import QuantitySelector from "./QuantitySelector";

export default async function ProductPage() {
  const product = await getProduct();

  return (
    <main>
      <h1>{product.name}</h1>

      <p>{product.description}</p>

      <QuantitySelector />
    </main>
  );
}
```

The interactive part is isolated:

```
// app/products/[id]/QuantitySelector.tsx

"use client";

import { useState } from "react";

export default function QuantitySelector() {
  const [quantity, setQuantity] = useState(1);

  return (
    <div>
      <button onClick={() => setQuantity(Math.max(1, quantity - 1))}>
        -
      </button>

      <span>{quantity}</span>

      <button onClick={() => setQuantity(quantity + 1)}>
        +
      </button>
    </div>
  );
}
```

### **Comparison**

| Approach                             | Client JavaScript                           |
| ------------------------------------ | ------------------------------------------- |
| Whole page is a Client Component     | More code may enter the client module graph |
| Only interactive component is Client | Smaller client-side boundary in many cases  |

**Best practice:** Keep the client boundary as small as practical.

This is often called the *island-style approach*: most of the page remains server-rendered, while individual interactive components run on the client.

# **17. Protecting Server-Side Code with&#x20;**`server-only`

Suppose you have a database connection file.

You don't want this file to accidentally be imported into a Client Component.

Next.js provides the `server-only` package for this purpose.

### **Example**

```
// lib/db.ts

import "server-only";

import mongoose from "mongoose";

export async function connectDB() {
  if (mongoose.connection.readyState === 1) {
    return;
  }

  await mongoose.connect(process.env.MONGODB_URI!);
}
```

Now, if a Client Component attempts to import this module, Next.js produces a build-time error.

### **Why is this useful?**

It helps prevent accidental exposure of:

- Database connection logic.
- API secrets.
- Private environment variables.
- Server-side authentication utilities.
- Internal business logic.

### **Protecting server-only modules**

```
// lib/auth.ts

import "server-only";

export async function verifyUser() {
  // Verify the user's session on the server.
}
```

### **What about&#x20;**`client-only`**?**

There is also a `client-only` package for modules that should only be imported into Client Components.

For example, a module that depends on browser-specific functionality can use:

```
import "client-only";
```

**Remember:**

| Package       | Purpose                                             |
| ------------- | --------------------------------------------------- |
| `server-only` | Prevent accidental import into client module graphs |
| `client-only` | Prevent accidental import into server module graphs |

These packages help enforce architectural boundaries. They do not replace proper security checks.

# **18. Understanding the React Server Component Payload**

The React Server Component payload is also known as the RSC payload or Flight payload.

It is an important part of how Next.js communicates the rendered Server Component tree to the browser.

### **What does it contain?**

Conceptually, it includes information such as:

- Rendered Server Component results.
- References to Client Components.
- The props passed to Client Components.
- Information needed to reconcile the component tree.
- References to other relevant resources.

### **Simplified example**

Imagine this page:

```
// app/page.tsx

import Counter from "./Counter";

export default function Page() {
  return (
    <main>
      <h1>My Website</h1>
      <Counter initialCount={5} />
    </main>
  );
}
```

```
// app/Counter.tsx

"use client";

import { useState } from "react";

export default function Counter({
  initialCount,
}: {
  initialCount: number;
}) {
  const [count, setCount] = useState(initialCount);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

The Server Component renders the page and provides the Client Component reference and its initial props.

The browser uses the RSC payload and JavaScript module references to reconstruct and hydrate the interactive component tree.

### **Why is the RSC payload important?**

It enables Next.js to:

1. Render Server Components without sending their implementation to the browser.
2. Preserve the distinction between Server and Client Components.
3. Pass supported props to Client Components.
4. Update the UI during client-side navigation.
5. Integrate streaming with the component tree.

**Interview point:** The RSC payload is not simply the final HTML. It is a representation of the React component tree and the information required to integrate Server and Client Components.

# **19. Real-World Architecture: E-commerce Product Page**

Let's combine everything into a practical architecture.

Imagine an e-commerce product page containing:

- Product name.
- Product description.
- Product price.
- Product image.
- Quantity selector.
- Add to cart button.
- Product reviews.
- Related products.

Some sections need server-side functionality, while others need browser-side interactivity.

[E-commerce product detail page by Muslimbek Vosidiy  on Dribbble](https://images.openai.com/static-rsc-4/iKOfFmaNuxmLHTDKlRlioHDZQTqXw_4cfekru83rtyKsA7H61sYo8vSv2HbPl5rPR3axI-fQZROKj6aRH5rFrBFMIVVvlhZsBMPGqWvxD2eyszbi36iescm6ln8QhXy2WAngu3rNFeqJljSvMuph699mzlILwfXosL2gnkLz_44?purpose=inline)

[dribbble.com](https://dribbble.com/shots/16454334-E-commerce-product-detail-page?utm_source=chatgpt.com)

### **Recommended component structure**

```
app/
└── products/
    └── [id]/
        ├── page.tsx
        ├── ProductDetails.tsx
        ├── QuantitySelector.tsx
        ├── AddToCartButton.tsx
        ├── ProductReviews.tsx
        └── RelatedProducts.tsx
```

### **Component responsibilities**

| Component              | Type   | Reason                      |
| ---------------------- | ------ | --------------------------- |
| `page.tsx`             | Server | Fetch product data          |
| `ProductDetails.tsx`   | Server | Display product information |
| `QuantitySelector.tsx` | Client | Maintain quantity state     |
| `AddToCartButton.tsx`  | Client | Handle click interaction    |
| `ProductReviews.tsx`   | Server | Fetch and display reviews   |
| `RelatedProducts.tsx`  | Server | Fetch related products      |

### **Product page**

```
// app/products/[id]/page.tsx

import ProductDetails from "./ProductDetails";
import QuantitySelector from "./QuantitySelector";
import AddToCartButton from "./AddToCartButton";
import ProductReviews from "./ProductReviews";
import RelatedProducts from "./RelatedProducts";

import { getProduct } from "@/lib/products";

import { notFound } from "next/navigation";

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
    <main>
      <ProductDetails product={product} />

      <QuantitySelector />

      <AddToCartButton productId={product.id} />

      <ProductReviews productId={product.id} />

      <RelatedProducts productId={product.id} />
    </main>
  );
}
```

### **Quantity selector**

```
// app/products/[id]/QuantitySelector.tsx

"use client";

import { useState } from "react";

export default function QuantitySelector() {
  const [quantity, setQuantity] = useState(1);

  return (
    <div>
      <button
        onClick={() => setQuantity(Math.max(1, quantity - 1))}
      >
        -
      </button>

      <span>{quantity}</span>

      <button onClick={() => setQuantity(quantity + 1)}>
        +
      </button>
    </div>
  );
}
```

### **Add to cart button**

```
// app/products/[id]/AddToCartButton.tsx

"use client";

export default function AddToCartButton({
  productId,
}: {
  productId: string;
}) {
  function handleAddToCart() {
    console.log("Adding product:", productId);
  }

  return (
    <button onClick={handleAddToCart}>
      Add to Cart
    </button>
  );
}
```

This example demonstrates a useful architecture:

- Server Components handle data-heavy UI.
- Client Components handle interactive behavior.
- Only the required data crosses the Server-to-Client boundary.

For a production cart, the actual cart mutation should be validated and performed through a trusted server-side operation.

# **20. Common Mistakes**

| Mistake                                                       | Correct approach                                              |
| ------------------------------------------------------------- | ------------------------------------------------------------- |
| Adding `"use client"` to every component                      | Add it only where client functionality is needed              |
| Using `useState` in a Server Component                        | Move interactive logic into a Client Component                |
| Directly importing a Server Component into a Client Component | Use composition and pass rendered content as props            |
| Passing normal functions from Server to Client Components     | Use serializable props or supported Server Actions            |
| Accessing `window` or `localStorage` during initial rendering | Access browser APIs in browser-safe code, such as `useEffect` |
| Assuming Client Components render only in the browser         | Remember that they can be pre-rendered on the server          |
| Importing database modules into Client Components             | Keep database code server-only                                |
| Making the entire page a Client Component for one button      | Isolate the interactive component                             |
| Exposing secrets through `NEXT_PUBLIC_` variables             | Keep private secrets in server-only modules                   |
| Treating `"use client"` as a component-level visual label     | Understand it as a module graph boundary                      |

# **21. Interview Questions and Answers**

## **Basic Level**

**Q1. What are React Server Components?**

Server Components are React components that execute in a server environment and contribute to the rendered UI without requiring their implementation to be included in the browser's JavaScript bundle.

**Q2. What are Client Components?**

Client Components are components that participate in client-side React functionality, including state, effects, event handlers, and browser interactions.

**Q3. Which components are the default in the Next.js App Router?**

Server Components are the default.

**Q4. What is the purpose of&#x20;**`"use client"`**?**

It establishes a Client Component boundary in the module dependency graph.

**Q5. Can Server Components use&#x20;**`useState`**?**

No. React hooks such as `useState` and `useEffect` that require client-side behavior cannot be used in Server Components.

## **Intermediate Level**

**Q6. Can Server Components render Client Components?**

Yes. A Server Component can import and render a Client Component.

**Q7. Can Client Components import Server Components?**

Not while preserving Server Component execution. Instead, render the Server Component in a Server Component parent and pass the resulting content through a prop such as `children`.

**Q8. What is hydration?**

Hydration is the process of attaching React's client-side behavior to HTML that has already been rendered.

**Q9. Do Client Components execute only in the browser?**

No. They can be pre-rendered on the server for the initial page load and then hydrated in the browser.

**Q10. What is the difference between Server and Client Component rendering?**

Server Components execute on the server. Client Components participate in the client module graph and are hydrated for browser-side interactions.

## **Advanced Level**

**Q11. What is the RSC payload?**

It is a representation of the React Server Component tree, including rendered results, Client Component references, and supported props.

**Q12. Why should we minimize Client Components?**

Because their module dependencies can contribute to client-side JavaScript, which the browser must download and execute.

**Q13. Can we pass functions as props from Server Components to Client Components?**

Ordinary functions cannot be passed as regular serializable props. Supported Server Actions are a special exception.

**Q14. What is the difference between the module graph and component tree?**

The module graph represents import dependencies between files. The component tree represents how components are composed and rendered. The `"use client"` directive affects the module graph, while composition determines the rendered tree.

**Q15. What is&#x20;**`server-only`**?**

It is a package used to prevent a module from being imported into a Client Component module graph.

**Q16. Can Server Components access environment variables?**

Yes, Server Components and server-only modules can access private environment variables when properly configured. They should never expose secrets to client code.

**Q17. Why can accessing&#x20;**`localStorage`**&#x20;directly during initial rendering cause an error?**

Because Client Components can be pre-rendered on the server, where `localStorage` does not exist.

**Q18. What is the best architecture for a large Next.js application?**

Keep data fetching and server-side operations in Server Components, isolate interactivity in Client Components, and use composition to combine them.

# **22. Practical Assignment**



Hands-on project

**Build a Server and Client Component-Based E-commerce Application**



[Marketplace designs, themes, templates and downloadable graphic elements on Dribbble](https://images.openai.com/static-rsc-4/5eLySd7umyFEIX_5RcyH4rDtVdJXXqLkGN_A-a_P09Ha0Z__GqBT89CAnvOmthTdV346IAgU9yrqC-qbE5RAiZZzt-aUGUHodPqpEWL0fK2Ml-VTmaYertN7-L0dsRJpnjudsrBVpMB2tCgyH2PuyZLRmZ7kf_9lhWcxZZ5bgF8?purpose=inline)

[dribbble.com](https://dribbble.com/tags/marketplace?utm_source=chatgpt.com)

### **Task 1: Create this folder structure**

```
src/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   │
│   └── products/
│       ├── page.tsx
│       └── [id]/
│           └── page.tsx
│
├── components/
│   ├── Navbar.tsx
│   ├── ProductList.tsx
│   ├── ProductCard.tsx
│   ├── SearchBar.tsx
│   ├── AddToCartButton.tsx
│   └── QuantitySelector.tsx
│
└── lib/
    ├── db.ts
    └── products.ts
```

### **Task 2: Create Server Components**

Implement:

- Product listing page.
- Product details page.
- Product data fetching.
- Product card display.
- Database connection.

### **Task 3: Create Client Components**

Implement:

- Search input with state.
- Quantity selector.
- Add to cart button.
- Mobile navigation toggle.

### **Task 4: Practice component composition**

Create a Server Component that renders a Client Component and passes product information through props.

Then create a Client Component that receives server-rendered content through `children`.

### **Task 5: Practice serialization**

Try passing:

- String.
- Number.
- Boolean.
- Array.
- Plain object.
- Ordinary function.

Observe which values are supported across the Server-to-Client boundary.

### **Task 6: Practice&#x20;**`server-only`

Add:

```
import "server-only";
```

to your database module.

Try importing the database module into a Client Component and observe the error.

### **Task 7: Optimize your component architecture**

Review your application and answer:

1. Which components actually need `"use client"`?
2. Which components can remain Server Components?
3. Are database operations happening on the server?
4. Are you passing only necessary data to Client Components?
5. Are private environment variables protected?

# **23. Quick Revision Notes**

| Concept               | Key point                                                     |
| --------------------- | ------------------------------------------------------------- |
| Server Component      | Executes in a server environment                              |
| Client Component      | Participates in browser-side React interactivity              |
| Default component     | Server Component                                              |
| `"use client"`        | Establishes a client module boundary                          |
| `"use server"`        | Marks Server Actions or server functions, depending on usage  |
| Hydration             | Attaches client-side behavior to pre-rendered HTML            |
| RSC payload           | Representation of the Server Component tree                   |
| Component composition | Combining Server and Client Components                        |
| Serializable props    | Values supported by React's serialization protocol            |
| Server Actions        | Server-side operations invoked through supported interactions |
| `server-only`         | Protects server-only modules from client imports              |
| `client-only`         | Protects client-only modules from server imports              |
| Client bundle         | JavaScript required for client-side functionality             |
| Best practice         | Keep the client boundary as small as practical                |

# **24. Phase 9 Completion Checklist**

**Your progress**

# **0%**

0 of 19 completed

Understand React Server Components

Understand Client Components

Differentiate Server and Client Components

Understand the use client directive

Understand the module dependency graph

Understand where components execute

Understand initial page rendering

Understand hydration

Understand hydration mismatches

Render Client Components from Server Components

Understand Server Component composition through children

Understand the RSC payload

Understand serializable props

Understand Server Actions

Understand Server Component limitations

Understand Client Component limitations

Understand server-only and client-only

Understand client bundle optimization

Build the e-commerce practical assignment

# **25. What's Next? – Phase 10**

Next phase

# **Data Fetching in Next.js**

**Topics we will cover:**

1. Understanding data fetching in Next.js.
2. Fetching data using the native `fetch()` API.
3. Fetching data inside Server Components.
4. Fetching data inside Client Components.
5. Using `async` and `await`.
6. Understanding request caching.
7. Understanding revalidation.
8. Understanding parallel data fetching.
9. Understanding sequential data fetching.
10. Fetching data from REST APIs.
11. Fetching data from MongoDB and MySQL.
12. Error handling during data fetching.
13. Data fetching best practices.

When you're ready, say **"OK, Phase 10"** and we'll continue with detailed notes, practical code examples, and exercises.