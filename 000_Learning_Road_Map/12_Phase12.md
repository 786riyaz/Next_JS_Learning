# **Phase 12 – Server Actions and Data Mutations**



Phase 12

Intermediate to Advanced

Next.js 16.x



## **1. Learning objectives**

In this phase, you will learn how to perform server-side operations directly from React components using **Server Actions**.

By the end of this phase, you should understand:

1. What Server Actions are and why they are useful.
2. The `"use server"` directive.
3. Server Actions vs Route Handlers.
4. How to submit forms without manually creating API endpoints.
5. How to read and process `FormData`.
6. How to handle form validation and errors.
7. How to use `useActionState()`.
8. How to use `useFormStatus()`.
9. How to perform Create, Read, Update and Delete operations.
10. How to revalidate data using `revalidatePath()` and `revalidateTag()`.
11. How to secure Server Actions with authentication and authorization.
12. How to build a complete CRUD workflow using Server Actions.

## **2. What are Server Actions?**

A **Server Action** is an asynchronous server-side function that can be invoked from a Client Component or a form.

It allows you to execute server-side operations, such as:

- Inserting records into MongoDB or MySQL.
- Updating database records.
- Deleting records.
- Processing form submissions.
- Calling external services.
- Performing server-side business logic.

Traditionally, you might create a REST API endpoint and call it using `fetch()`.

With Server Actions, you can invoke a server-side function directly through React's form and action mechanisms.

### **Traditional approach using Route Handlers**

Imagine you want to create a product.

Your workflow might look like this:

**React Form**

fetch('/api/products')

**Route Handler**

POST /api/products

**Database**

HTTP response

**React Form receives the response**

### **Server Actions approach**

With a Server Action, you can simplify this workflow:

**React Form**

Invoke Server Action

**Server Action**

createProduct(formData)

**Database**

Action result

**React UI updates**

You don't need to manually create a separate REST endpoint just to handle this form submission.

**Important:** Server Actions still use an HTTP request internally. They are not ordinary functions that execute directly inside the browser. Next.js handles the server invocation and communication.

## **3. What is the&#x20;**`"use server"`**&#x20;directive?**

`"use server"` is a special directive that marks a function or module as containing Server Actions.

There are two common ways to use it.

### **Method 1: Function-level directive**

You can define a Server Action inside a Server Component.

```
export default function CreateProductPage() {
  async function createProduct(formData: FormData) {
    "use server";

    const name = formData.get("name");

    // Save product to database.
  }

  return (
    <form action={createProduct}>
      <input name="name" placeholder="Product name" />

      <button type="submit">
        Create Product
      </button>
    </form>
  );
}
```

Here:

- `createProduct()` is a Server Action.
- `"use server"` marks it as server-side.
- The form invokes it through the `action` attribute.
- `formData` contains the submitted form values.

### **Method 2: Module-level directive**

For larger applications, it is generally better to keep Server Actions in a separate file.

Create:

`src/app/products/actions.ts`

```
"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");

  // Save product to database.
}
```

Now you can import this action into a component:

```
import { createProduct } from "./actions";

export default function CreateProductPage() {
  return (
    <form action={createProduct}>
      <input name="name" placeholder="Product name" />

      <button type="submit">
        Create Product
      </button>
    </form>
  );
}
```

### **Important rules**

- A file with module-level `"use server"` can export Server Actions.
- Server Actions must be asynchronous functions.
- A Client Component can import an exported Server Action from a module marked `"use server"`.
- A Server Component can define a Server Action using a function-level directive.
- Do not put `"use server"` at the top of a Client Component to turn that component into a Server Component.
- Do not expose database credentials or secrets through action arguments or return values.

**Remember:** `"use client"` establishes a Client Component boundary, while `"use server"` marks server-side functions as Server Actions.

Official reference: [Next.js – Updating Data](https://nextjs.org/docs/app/getting-started/updating-data?utm_source=chatgpt.com).

## **4. How Server Actions receive form data**

When you use a Server Action in a form's `action` attribute, Next.js automatically passes a `FormData` object to the function.

Consider this form:

```
<form action={createProduct}>
  <input type="text" name="name" />
  <input type="number" name="price" />

  <button type="submit">Create Product</button>
</form>
```

The browser submits the form fields as `FormData`.

### **What is FormData?**

`FormData` is a browser/Web API used to represent form fields and their values.

You can read values using:

- `formData.get("fieldName")`
- `formData.getAll("fieldName")`
- `formData.has("fieldName")`
- `formData.entries()`

Example:

```
"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");
  const price = formData.get("price");

  console.log(name);
  console.log(price);
}
```

**Important:** Values returned by `formData.get()` are `FormDataEntryValue | null`, meaning they can be strings, files or null. They are not automatically converted to numbers.

For example:

```
const price = Number(formData.get("price"));
```

You should still validate the result before saving it.

### **Handling checkbox values**

```
<form action={createProduct}>
  <input type="text" name="name" />

  <label>
    <input type="checkbox" name="featured" value="true" />
    Featured product
  </label>

  <button type="submit">Save</button>
</form>
```

Server Action:

```
"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");
  const featured = formData.get("featured") === "true";

  // Save validated values.
}
```

If the checkbox is unchecked, it is normally absent from the submitted form data.

## **5. Building your first complete Server Action**

Let's create a simple product form.

### **Folder structure**

```
src/
└── app/
    └── products/
        ├── page.tsx
        └── actions.ts
```

### **Step 1: Create the Server Action**

`src/app/products/actions.ts`

```
"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");
  const price = Number(formData.get("price"));

  if (typeof name !== "string" || !name.trim()) {
    throw new Error("Product name is required");
  }

  if (!Number.isFinite(price) || price <= 0) {
    throw new Error("Price must be greater than zero");
  }

  // Insert product into database.

  console.log({
    name,
    price,
  });
}
```

### **Step 2: Connect the action to a form**

`src/app/products/page.tsx`

```
import { createProduct } from "./actions";

export default function ProductsPage() {
  return (
    <main>
      <h1>Create Product</h1>

      <form action={createProduct}>
        <div>
          <label htmlFor="name">Product Name</label>
          <input
            id="name"
            name="name"
            type="text"
            required
          />
        </div>

        <div>
          <label htmlFor="price">Price</label>
          <input
            id="price"
            name="price"
            type="number"
            required
          />
        </div>

        <button type="submit">
          Create Product
        </button>
      </form>
    </main>
  );
}
```

That's it. You have connected a React form to a server-side function without manually creating a Route Handler.

**What happens when the user submits the form?**

1. The browser collects the form values.
2. The form invokes `createProduct`.
3. Next.js sends the action request to the server.
4. The Server Action executes.
5. The server validates the input.
6. The database operation takes place.
7. Next.js processes the action result and updates the UI as appropriate.

Forms using Server Actions from Server Components also support progressive enhancement: they can submit before client-side JavaScript has loaded.

## **6. Returning results from Server Actions**

A Server Action can return data to the component that invokes it.

Instead of throwing an error for every expected validation failure, it is often better to return a structured result.

Example:

```
"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");
  const price = Number(formData.get("price"));

  if (typeof name !== "string" || !name.trim()) {
    return {
      success: false,
      message: "Product name is required",
    };
  }

  if (!Number.isFinite(price) || price <= 0) {
    return {
      success: false,
      message: "Invalid product price",
    };
  }

  // Insert into database.

  return {
    success: true,
    message: "Product created successfully",
  };
}
```

This is useful for simple function calls, but to manage form state, validation messages and pending states together, React provides `useActionState()`.

## **7. Understanding&#x20;**`useActionState()`

`useActionState()` is a React Hook that allows you to manage the state returned by a Server Action.

It is particularly useful for:

- Displaying validation errors.
- Displaying success messages.
- Managing pending submissions.
- Returning structured results from forms.

Import it from React:

```
import { useActionState } from "react";
```

### **Basic syntax**

```
const [state, formAction, pending] = useActionState(
  action,
  initialState
);
```

| Return value | Meaning                                   |
| ------------ | ----------------------------------------- |
| `state`      | Latest result returned by the action      |
| `formAction` | Action to pass to the form                |
| `pending`    | Whether the action is currently executing |

### **Important difference from a normal Server Action**

When using `useActionState`, the action receives two arguments:

```
previousState
formData
```

Instead of:

```
formData
```

Let's build an example.

### **Step 1: Define the state type**

```
export type ProductState = {
  success: boolean;
  message: string;
};
```

### **Step 2: Create the Server Action**

`src/app/products/actions.ts`

```
"use server";

import type { ProductState } from "./types";

export async function createProduct(
  previousState: ProductState,
  formData: FormData
): Promise<ProductState> {
  const name = formData.get("name");
  const price = Number(formData.get("price"));

  if (typeof name !== "string" || !name.trim()) {
    return {
      success: false,
      message: "Product name is required",
    };
  }

  if (!Number.isFinite(price) || price <= 0) {
    return {
      success: false,
      message: "Please enter a valid price",
    };
  }

  // Save product to database.

  return {
    success: true,
    message: "Product created successfully",
  };
}
```

The `previousState` argument is available if the action needs to use the previous result. It is not necessary to use it in every action.

### **Step 3: Create a Client Component**

`src/app/products/product-form.tsx`

```
"use client";

import { useActionState } from "react";
import { createProduct } from "./actions";

const initialState = {
  success: false,
  message: "",
};

export default function ProductForm() {
  const [state, formAction, pending] = useActionState(
    createProduct,
    initialState
  );

  return (
    <form action={formAction}>
      <div>
        <label htmlFor="name">Product Name</label>
        <input
          id="name"
          name="name"
          type="text"
          required
        />
      </div>

      <div>
        <label htmlFor="price">Price</label>
        <input
          id="price"
          name="price"
          type="number"
          required
        />
      </div>

      <button type="submit" disabled={pending}>
        {pending ? "Creating..." : "Create Product"}
      </button>

      {state.message && (
        <p>{state.message}</p>
      )}
    </form>
  );
}
```

### **Step 4: Render the form**

`src/app/products/page.tsx`

```
import ProductForm from "./product-form";

export default function ProductsPage() {
  return (
    <main>
      <h1>Create Product</h1>
      <ProductForm />
    </main>
  );
}
```

### **Understand the flow**

**User submits form**

**useActionState invokes Server Action**

**Server Action validates and saves data**

**Action returns result**

**React updates state and displays message**

**Remember:** `useActionState()` is especially useful when a form needs to display server-returned validation errors or success messages.

Official reference: [React – useActionState](https://react.dev/reference/react/useActionState?utm_source=chatgpt.com).

## **8. Understanding&#x20;**`useFormStatus()`

`useFormStatus()` is another React Hook that helps you track the submission status of a form.

You can use it to:

- Disable the submit button.
- Display a loading message.
- Show a spinner.
- Prevent repeated submissions while the action is pending.

Import it from `react-dom`:

```
import { useFormStatus } from "react-dom";
```

### **Example**

Create a separate submit button component:

`src/app/products/submit-button.tsx`

```
"use client";

import { useFormStatus } from "react-dom";

export default function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button type="submit" disabled={pending}>
      {pending ? "Saving..." : "Save Product"}
    </button>
  );
}
```

Now use it inside the form:

`src/app/products/product-form.tsx`

```
import { createProduct } from "./actions";
import SubmitButton from "./submit-button";

export default function ProductForm() {
  return (
    <form action={createProduct}>
      <input
        type="text"
        name="name"
        placeholder="Product name"
        required
      />

      <input
        type="number"
        name="price"
        placeholder="Price"
        required
      />

      <SubmitButton />
    </form>
  );
}
```

### **Important rule**

`useFormStatus()` must be called inside a component that is rendered as a descendant of the form whose status you want to track.

This is incorrect:

```
export default function ProductForm() {
  const { pending } = useFormStatus();

  return (
    <form action={createProduct}>
      <button disabled={pending}>Save</button>
    </form>
  );
}
```

Why? The component itself is rendering the form. It is not a descendant of that form.

The correct approach is to create a child component such as `SubmitButton`.

### `useActionState()`**&#x20;vs&#x20;**`useFormStatus()`

| Feature                          | useActionState         | useFormStatus               |
| -------------------------------- | ---------------------- | --------------------------- |
| Package                          | `react`                | `react-dom`                 |
| Manages action result            | Yes                    | No                          |
| Provides pending state           | Yes                    | Yes                         |
| Accesses returned errors         | Yes, through state     | No                          |
| Needs a separate child component | No                     | Yes, for the form it tracks |
| Common purpose                   | Form state and results | Submit button loading state |

**Practical recommendation:** Use `useActionState()` when you need form results and validation messages. Use `useFormStatus()` when a child component needs to know whether its parent form is submitting.

Official references: [React – useFormStatus](https://react.dev/reference/react-dom/hooks/useFormStatus?utm_source=chatgpt.com) and [Next.js – Forms](https://nextjs.org/docs/app/guides/forms?utm_source=chatgpt.com).

## **9. Form validation using Zod**

Client-side validation improves the user experience, but server-side validation is essential for security and data integrity.

A user can bypass frontend validation and directly invoke a Server Action request.

Therefore, validate data inside the Server Action.

### **Step 1: Install Zod**

```
npm install zod
```

### **Step 2: Create a validation schema**

`src/lib/validations/product.ts`

```
import { z } from "zod";

export const productSchema = z.object({
  name: z
    .string()
    .trim()
    .min(2, "Name must contain at least 2 characters"),

  price: z
    .number()
    .positive("Price must be greater than zero"),

  category: z
    .string()
    .trim()
    .min(1, "Category is required"),
});

export type ProductInput = z.infer<typeof productSchema>;
```

### **Step 3: Validate inside the Server Action**

```
"use server";

import { productSchema } from "@/lib/validations/product";

export async function createProduct(
  previousState: {
    success: boolean;
    message: string;
    errors?: Record<string, string[]>;
  },
  formData: FormData
) {
  const rawData = {
    name: formData.get("name"),
    price: Number(formData.get("price")),
    category: formData.get("category"),
  };

  const result = productSchema.safeParse(rawData);

  if (!result.success) {
    const errors = result.error.flatten().fieldErrors;

    return {
      success: false,
      message: "Please correct the form errors",
      errors,
    };
  }

  const productData = result.data;

  // Save productData to database.

  return {
    success: true,
    message: "Product created successfully",
    errors: {},
  };
}
```

### **Why use&#x20;**`safeParse()`**?**

Zod provides two common validation approaches:

| Method        | Behavior                                                               |
| ------------- | ---------------------------------------------------------------------- |
| `parse()`     | Returns validated data or throws a validation error                    |
| `safeParse()` | Returns a success/error result without throwing for validation failure |

Example:

```
const result = productSchema.safeParse(data);

if (result.success) {
  console.log(result.data);
} else {
  console.log(result.error.issues);
}
```

For form submissions, `safeParse()` is particularly convenient because you can return validation errors as part of the action state.

### **Display field-level errors**

A Client Component can render the returned errors:

```
{state.errors?.name?.map((error) => (
  <p key={error}>{error}</p>
))}
```

You can use the same pattern for `price` and `category`.

**Important:** Never rely exclusively on HTML attributes such as `required`, `min` or `max`. Server-side validation must independently verify all important values.

## **10. Data mutations – Create, Read, Update and Delete**

A data mutation means changing data in a database.

The four common database operations are known as CRUD:

| Operation | Meaning          | Example              |
| --------- | ---------------- | -------------------- |
| Create    | Insert a record  | Add a product        |
| Read      | Retrieve records | Display products     |
| Update    | Modify a record  | Change product price |
| Delete    | Remove a record  | Delete a product     |

Server Actions are particularly useful for the Create, Update and Delete operations.

Reading data is generally better handled by Server Components and server-side data-access functions.

### **Recommended architecture**

```
src/
├── app/
│   └── products/
│       ├── page.tsx
│       ├── actions.ts
│       ├── product-form.tsx
│       └── [id]/
│           └── edit/
│               └── page.tsx
│
├── lib/
│   ├── mongodb.ts
│   └── validations/
│       └── product.ts
│
└── types/
    └── product.ts
```

Let's understand each operation.

### **A. Create a product**

```
"use server";

import clientPromise from "@/lib/mongodb";
import { productSchema } from "@/lib/validations/product";
import { revalidatePath } from "next/cache";

export async function createProduct(
  previousState: {
    success: boolean;
    message: string;
  },
  formData: FormData
) {
  const result = productSchema.safeParse({
    name: formData.get("name"),
    price: Number(formData.get("price")),
    category: formData.get("category"),
  });

  if (!result.success) {
    return {
      success: false,
      message: "Invalid product data",
    };
  }

  const client = await clientPromise;

  const db = client.db("mydb");

  await db.collection("products").insertOne({
    ...result.data,
    createdAt: new Date(),
    updatedAt: new Date(),
  });

  revalidatePath("/products");

  return {
    success: true,
    message: "Product created successfully",
  };
}
```

This example demonstrates the mutation and revalidation flow. A production action must also authenticate the user, authorize the operation, and handle database errors safely.

### **B. Update a product**

For an update action, you need to know which product should be modified.

You can pass additional arguments to a Server Action using `bind()`.

Example:

```
"use server";

export async function updateProduct(
  productId: string,
  formData: FormData
) {
  const name = formData.get("name");
  const price = Number(formData.get("price"));

  // Validate productId and form values.
  // Update database record.

  return {
    success: true,
    message: `Product ${productId} updated`,
  };
}
```

Now bind the ID in a Server Component:

```
import { updateProduct } from "./actions";

export default function EditProductForm({
  productId,
}: {
  productId: string;
}) {
  const updateProductWithId = updateProduct.bind(
    null,
    productId
  );

  return (
    <form action={updateProductWithId}>
      <input name="name" placeholder="Product name" />

      <input
        name="price"
        type="number"
        placeholder="Price"
      />

      <button type="submit">Update Product</button>
    </form>
  );
}
```

**Important:** `bind()` is a convenient way to pass additional arguments. However, bound arguments are not trusted just because they came from a Server Component. Always validate the ID and verify that the user is authorized to update that specific record.

### **C. Delete a product**

A delete action can also receive an ID:

```
"use server";

import { revalidatePath } from "next/cache";

export async function deleteProduct(productId: string) {
  // Authenticate and authorize the user.
  // Validate the ID.
  // Delete product from database.

  revalidatePath("/products");

  return {
    success: true,
    message: "Product deleted successfully",
  };
}
```

Call it from a form:

```
import { deleteProduct } from "./actions";

export default function DeleteProductButton({
  productId,
}: {
  productId: string;
}) {
  const deleteAction = deleteProduct.bind(null, productId);

  return (
    <form action={deleteAction}>
      <button type="submit">Delete</button>
    </form>
  );
}
```

For destructive operations, you should also consider a confirmation UI and prevent unauthorized or accidental deletions.

## **11. Revalidating data after mutations**

Imagine the following situation:

1. Your products page displays 10 products.
2. You create another product using a Server Action.
3. The database now contains 11 products.
4. But your displayed page may still be using cached data.

You need to ensure the relevant page or cached data is refreshed.

Next.js provides several APIs for this.

### **A.&#x20;**`revalidatePath()`

This invalidates cached data associated with a particular route path.

Import:

```
import { revalidatePath } from "next/cache";
```

Example:

```
"use server";

import { revalidatePath } from "next/cache";

export async function createProduct(formData: FormData) {
  // Insert product into database.

  revalidatePath("/products");

  return {
    success: true,
  };
}
```

This tells Next.js to revalidate the `/products` route.

You can also revalidate a dynamic path:

```
revalidatePath("/products/101");
```

You can target a route pattern and specify whether it represents a page or layout:

```
revalidatePath("/products/[id]", "page");
```

### **B.&#x20;**`revalidateTag()`

This invalidates cached data associated with a particular tag.

Imagine that your product data is displayed on multiple pages:

- `/products`
- `/dashboard`
- `/featured-products`

Instead of revalidating each route separately, you can tag the shared data.

Example of a cached function:

```
import { cacheLife, cacheTag } from "next/cache";

export async function getProducts() {
  "use cache";

  cacheLife("hours");
  cacheTag("products");

  // Fetch products from database.
}
```

After modifying products, call:

```
import { revalidateTag } from "next/cache";

revalidateTag("products", "max");
```

The `"max"` profile uses stale-while-revalidate behavior: stale data can be served while fresh data is generated in the background.

### **C.&#x20;**`updateTag()`

Next.js 16 provides `updateTag()` for immediate expiration of tagged cached data in Server Actions.

```
"use server";

import { updateTag } from "next/cache";

export async function updateProduct(formData: FormData) {
  // Update product in database.

  updateTag("products");

  return {
    success: true,
  };
}
```

This is useful when a user expects to see their own changes immediately.

### **Difference between these three APIs**

| API                         | What it invalidates        | Behavior                        |
| --------------------------- | -------------------------- | ------------------------------- |
| `revalidatePath()`          | A particular route path    | Revalidates that route          |
| `revalidateTag(tag, "max")` | Data associated with a tag | Stale-while-revalidate          |
| `updateTag(tag)`            | Data associated with a tag | Immediately expires tagged data |

**Which should you use?**

- Use `revalidatePath()` when a particular route needs refreshing.
- Use `revalidateTag()` when tagged data can tolerate background refreshing.
- Use `updateTag()` in a Server Action when the user should immediately see the updated data.

Important: `updateTag()` can only be used in Server Actions. `revalidateTag()` and `revalidatePath()` can also be used in Route Handlers.

Official documentation:

- [Next.js – Revalidating](https://nextjs.org/docs/app/getting-started/revalidating?utm_source=chatgpt.com)
- [Next.js – updateTag](https://nextjs.org/docs/app/api-reference/functions/updateTag?utm_source=chatgpt.com)
- [Next.js – revalidatePath](https://nextjs.org/docs/app/api-reference/functions/revalidatePath?utm_source=chatgpt.com)
- [Next.js – revalidateTag](https://nextjs.org/docs/app/api-reference/functions/revalidateTag?utm_source=chatgpt.com)

## **12. Redirecting after a Server Action**

Sometimes, after a successful operation, you want to send the user to another page.

For example:

- After creating a product, navigate to the products list.
- After creating an invoice, navigate to the invoice details.
- After updating a profile, navigate to the profile page.

Next.js provides `redirect()`.

Import:

```
import { redirect } from "next/navigation";
```

Example:

```
"use server";

import { redirect } from "next/navigation";
import { revalidatePath } from "next/cache";

export async function createProduct(formData: FormData) {
  // Validate data.
  // Insert product into database.

  revalidatePath("/products");

  redirect("/products");
}
```

### **Important behavior of&#x20;**`redirect()`

`redirect()` interrupts the current execution flow by throwing a framework-handled control-flow exception.

Therefore, avoid catching it accidentally inside a broad `try...catch`.

Incorrect pattern:

```
"use server";

import { redirect } from "next/navigation";

export async function createProduct(formData: FormData) {
  try {
    // Save product.

    redirect("/products");
  } catch (error) {
    return {
      success: false,
      message: "Something went wrong",
    };
  }
}
```

The catch block can accidentally intercept the redirect signal.

Better:

```
"use server";

import { redirect } from "next/navigation";

export async function createProduct(formData: FormData) {
  try {
    // Save product.
  } catch (error) {
    console.error(error);

    return {
      success: false,
      message: "Unable to create product",
    };
  }

  redirect("/products");
}
```

The redirect is outside the `try...catch`.

## **13. Using cookies inside Server Actions**

Server Actions can read, set and delete cookies using the Next.js `cookies()` API.

Import:

```
import { cookies } from "next/headers";
```

In Next.js 16, `cookies()` is asynchronous.

### **Read a cookie**

```
import { cookies } from "next/headers";

export async function exampleAction() {
  const cookieStore = await cookies();

  const token = cookieStore.get("token");

  console.log(token?.value);
}
```

### **Set a cookie**

```
"use server";

import { cookies } from "next/headers";

export async function setTheme(theme: string) {
  const cookieStore = await cookies();

  cookieStore.set("theme", theme, {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "lax",
    path: "/",
    maxAge: 60 * 60 * 24 * 7,
  });
}
```

### **Delete a cookie**

```
"use server";

import { cookies } from "next/headers";

export async function logout() {
  const cookieStore = await cookies();

  cookieStore.delete("session");
}
```

### **What do these cookie options mean?**

| Option     | Purpose                                        |
| ---------- | ---------------------------------------------- |
| `httpOnly` | Prevents JavaScript from reading the cookie    |
| `secure`   | Sends cookie only over HTTPS                   |
| `sameSite` | Controls cross-site cookie behavior            |
| `path`     | Defines the URL path where cookie is available |
| `maxAge`   | Defines cookie lifetime in seconds             |

For authentication sessions, secure cookie configuration is important. Do not store passwords or sensitive authentication data in ordinary client-readable cookies.

## **14. Authentication and security in Server Actions**

This is one of the most important topics in this phase.

A common misconception is:

> Server Actions are secure automatically because they execute on the server.

That is incorrect.

Server Actions are reachable through POST requests. A user does not need to interact with your visible form to attempt to invoke an action.

Therefore, every sensitive Server Action must perform its own authentication and authorization checks.

### **Authentication vs authorization**

| Concept        | Question         | Example                       |
| -------------- | ---------------- | ----------------------------- |
| Authentication | Who are you?     | Is the user logged in?        |
| Authorization  | What can you do? | Is the user an administrator? |

### **Example: Protecting a delete action**

```
"use server";

import { revalidatePath } from "next/cache";
import { getCurrentUser } from "@/lib/auth";

export async function deleteProduct(productId: string) {
  // 1. Authentication
  const user = await getCurrentUser();

  if (!user) {
    return {
      success: false,
      message: "Please log in first",
    };
  }

  // 2. Authorization
  if (user.role !== "admin") {
    return {
      success: false,
      message: "You are not authorized",
    };
  }

  // 3. Validate the product ID
  if (!/^[a-f\d]{24}$/i.test(productId)) {
    return {
      success: false,
      message: "Invalid product ID",
    };
  }

  // 4. Delete product from database
  // Ensure the operation succeeds.

  revalidatePath("/products");

  return {
    success: true,
    message: "Product deleted",
  };
}
```

This assumes `getCurrentUser()` securely verifies the user's session. It is illustrative rather than a complete authentication implementation.

### **Security checklist**

1. Authenticate the user inside every protected action.
2. Check the user's permissions.
3. Validate every argument and form field.
4. Verify ownership of the resource being modified.
5. Never trust a client-provided user ID or role.
6. Do not expose secrets in return values.
7. Use parameterized SQL queries.
8. Protect against unauthorized record modification.
9. Apply rate limiting to sensitive actions where appropriate.
10. Use secure session cookies and appropriate CSRF protections.

**Important:** Never assume that a hidden button, disabled input or frontend role check provides backend security.

## **15. Optimistic UI updates using&#x20;**`useOptimistic()`

Normally, after submitting a form, the user waits for the server to finish the operation before seeing the updated result.

An **optimistic update** updates the interface immediately, before the server confirms success.

For example, imagine a chat application:

1. User types a message.
2. User clicks Send.
3. The message appears immediately.
4. The server saves the message.
5. If the operation fails, the UI can reconcile with the actual server state.

React provides `useOptimistic()` for this.

### **Example**

```
"use client";

import { useOptimistic } from "react";
import { sendMessage } from "./actions";

type Message = {
  id: string;
  text: string;
};

export default function Chat({
  messages,
}: {
  messages: Message[];
}) {
  const [optimisticMessages, addOptimisticMessage] =
    useOptimistic(
      messages,
      (currentMessages, newMessage: Message) => [
        ...currentMessages,
        newMessage,
      ]
    );

  async function formAction(formData: FormData) {
    const text = formData.get("message");

    if (typeof text !== "string" || !text.trim()) {
      return;
    }

    const temporaryMessage = {
      id: crypto.randomUUID(),
      text,
    };

    addOptimisticMessage(temporaryMessage);

    await sendMessage(text);
  }

  return (
    <div>
      {optimisticMessages.map((message) => (
        <p key={message.id}>{message.text}</p>
      ))}

      <form action={formAction}>
        <input name="message" required />

        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

Server Action:

```
"use server";

export async function sendMessage(text: string) {
  // Validate message.
  // Save message in database.

  return {
    success: true,
  };
}
```

This is a simplified example. A production chat application should handle server failures, duplicate submissions and reconciliation with persisted messages.

### **Why use optimistic updates?**

- Faster perceived response.
- Better user experience.
- Useful for chat applications.
- Useful for likes, reactions and simple toggles.
- Useful for shopping cart interactions.

### `useOptimistic()`**&#x20;vs&#x20;**`useActionState()`

| Feature             | useOptimistic                        | useActionState                         |
| ------------------- | ------------------------------------ | -------------------------------------- |
| Purpose             | Update UI before server confirmation | Manage action result and pending state |
| Main benefit        | Immediate feedback                   | Validation messages and action state   |
| Server confirmation | Still required                       | Action result provides state           |
| Common use          | Chat messages, likes, cart           | Forms, validation, submissions         |

Official reference: [React – useOptimistic](https://react.dev/reference/react/useOptimistic?utm_source=chatgpt.com).

## **16. Calling Server Actions from event handlers**

Server Actions are not limited to HTML forms.

You can also invoke them from Client Component event handlers.

For example, suppose you want to mark a notification as read when a user clicks a button.

```
"use server";

export async function markAsRead(notificationId: string) {
  // Authenticate user.
  // Update notification in database.

  return {
    success: true,
  };
}
```

Client Component:

```
"use client";

import { useState, useTransition } from "react";
import { markAsRead } from "./actions";

export default function NotificationButton({
  notificationId,
}: {
  notificationId: string;
}) {
  const [isPending, startTransition] = useTransition();
  const [message, setMessage] = useState("");

  function handleClick() {
    startTransition(async () => {
      const result = await markAsRead(notificationId);

      setMessage(
        result.success ? "Marked as read" : "Unable to update"
      );
    });
  }

  return (
    <div>
      <button onClick={handleClick} disabled={isPending}>
        {isPending ? "Updating..." : "Mark as read"}
      </button>

      <p>{message}</p>
    </div>
  );
}
```

### **Why use&#x20;**`useTransition()`**?**

It allows React to track the pending state of an asynchronous transition.

It is useful when invoking a Server Action from a regular event handler rather than directly through a form's action.

Remember:

- `useActionState()` is useful for managing action results.
- `useFormStatus()` tracks a parent form's submission.
- `useTransition()` tracks transitions started through `startTransition()`.

## **17. Passing arguments to Server Actions**

Sometimes a Server Action needs additional data that is not directly part of the form.

For example:

- Product ID.
- Order ID.
- User ID.
- Record identifier.

One convenient approach is `bind()`.

```
const updateProductWithId = updateProduct.bind(
  null,
  productId
);
```

The action receives the bound argument first:

```
"use server";

export async function updateProduct(
  productId: string,
  formData: FormData
) {
  // Update product.
}
```

Another option is to use a hidden form field:

```
<form action={updateProduct}>
  <input
    type="hidden"
    name="productId"
    value={productId}
  />

  <input name="name" />

  <button type="submit">Update</button>
</form>
```

Then read it:

```
const productId = formData.get("productId");
```

**Security warning:** Hidden inputs are still client-controlled. A user can modify them. Validate the ID and verify resource ownership or permissions on the server.

## **18. Server Actions vs Route Handlers – Complete comparison**

This is a very important interview topic.

| Feature                | Server Actions                              | Route Handlers                       |
| ---------------------- | ------------------------------------------- | ------------------------------------ |
| Primary purpose        | Server-side mutations and actions           | HTTP APIs                            |
| File                   | `actions.ts` or function-level directive    | `route.ts`                           |
| Directive              | `"use server"`                              | None required                        |
| Invocation             | Form action or function call                | HTTP request                         |
| HTTP method internally | POST                                        | GET, POST, PUT, PATCH, DELETE, etc.  |
| Return type            | Serializable action result                  | HTTP Response                        |
| Form integration       | Built into React forms                      | Usually manual fetch/form submission |
| External consumers     | Not intended as general-purpose public APIs | Suitable for external consumers      |
| Webhooks               | Usually not the appropriate choice          | Appropriate                          |
| Mobile app API         | Not normally recommended                    | Appropriate                          |
| Cache revalidation     | Supported                                   | Supported                            |
| Typical use            | Create/update/delete through your UI        | REST API, webhooks, integrations     |

### **When should you choose each?**

**Choose Server Actions when:**

- You are building forms in your Next.js application.
- You need to perform database mutations from your UI.
- You want to return validation messages directly to React.
- You need form submission with progressive enhancement.
- You want to integrate mutations with Next.js revalidation.

**Choose Route Handlers when:**

- You need a REST API.
- A mobile application needs to access your backend.
- A third-party application needs an endpoint.
- You need a webhook.
- You need explicit HTTP method and response handling.

**Professional recommendation:** You do not need to choose only one. A real Next.js application can use both Route Handlers and Server Actions.

# **19. Complete practical project – Product Management System**



Main assignment

Real-world project



Now combine everything you learned into one application.

**Project:** Product Management System

**Technology stack:**

- Next.js 16
- TypeScript
- MongoDB
- Zod
- Server Actions
- React Hooks

## **Project structure**

```
src/
├── app/
│   └── products/
│       ├── page.tsx
│       ├── actions.ts
│       ├── product-form.tsx
│       ├── submit-button.tsx
│       ├── product-list.tsx
│       │
│       └── [id]/
│           └── edit/
│               └── page.tsx
│
├── lib/
│   ├── mongodb.ts
│   ├── auth.ts
│   └── validations/
│       └── product.ts
│
└── types/
    └── product.ts
```

## **Product fields**

| Field         | Type     | Validation                     |
| ------------- | -------- | ------------------------------ |
| `_id`         | ObjectId | MongoDB-generated              |
| `name`        | String   | Required, minimum 2 characters |
| `description` | String   | Required                       |
| `price`       | Number   | Greater than zero              |
| `category`    | String   | Required                       |
| `stock`       | Number   | Zero or greater                |
| `createdAt`   | Date     | Automatically generated        |
| `updatedAt`   | Date     | Automatically updated          |

## **Required functionality**

### **Task 1 – Display products**

- Fetch products from MongoDB.
- Display them in a table.
- Show name, price, category and stock.
- Implement pagination.
- Add search and category filters.

### **Task 2 – Create product**

- Build a product form.
- Create a Server Action.
- Read form data using `FormData`.
- Validate using Zod.
- Insert into MongoDB.
- Display success and validation messages.
- Revalidate the product list.

### **Task 3 – Update product**

- Create an edit page.
- Fetch existing product details.
- Prepopulate the form.
- Pass product ID using `bind()`.
- Validate submitted values.
- Update MongoDB.
- Revalidate the relevant pages.

### **Task 4 – Delete product**

- Create a delete button.
- Use a Server Action.
- Validate the product ID.
- Verify administrator permissions.
- Delete the record.
- Refresh the product list.

### **Task 5 – Form states**

Implement:

- Loading state.
- Success message.
- Validation errors.
- Disabled submit button.
- Empty state.
- Database error handling.

### **Task 6 – Security**

- Implement authentication.
- Allow only administrators to modify products.
- Validate all action arguments.
- Verify authorization on the server.
- Never trust client-provided user IDs or roles.

## **Assignment progress tracker**

**Project completion**

# **0%**

0 / 15 tasks completed

Create project folder structure

Configure MongoDB connection

Create product TypeScript type

Create Zod validation schema

Implement product listing

Implement create product Server Action

Build product creation form

Implement useActionState

Implement useFormStatus

Implement edit product page

Implement update product action

Implement delete product action

Add revalidatePath or tag-based revalidation

Add authentication and authorization

Test validation and error handling

Reset progress

# **20. Common mistakes in Server Actions**

| Mistake                                              | Correct approach                             |
| ---------------------------------------------------- | -------------------------------------------- |
| Forgetting `"use server"`                            | Mark the function or module appropriately    |
| Using a synchronous Server Action                    | Server Actions must be asynchronous          |
| Reading `formData` as ordinary JSON                  | Use `FormData` methods                       |
| Forgetting to convert numeric values                 | Use `Number()` and validate                  |
| Not validating on the server                         | Validate all incoming data                   |
| Trusting hidden form fields                          | Treat all client input as untrusted          |
| Not checking authentication                          | Verify session inside protected actions      |
| Not checking authorization                           | Verify permissions and resource ownership    |
| Forgetting revalidation                              | Use appropriate cache invalidation           |
| Calling `updateTag()` in a Route Handler             | Use `revalidateTag()` there                  |
| Using `useFormStatus()` in the form component itself | Put it in a descendant component             |
| Catching `redirect()` accidentally                   | Call redirect outside the relevant try/catch |
| Using Server Actions as public REST APIs             | Use Route Handlers for general HTTP APIs     |

# **21. Interview questions and answers**

## **Basic level**

**Q1. What are Server Actions in Next.js?**

Server Actions are asynchronous server-side functions that can be invoked from React components and forms to perform server-side operations.

**Q2. What is the&#x20;**`"use server"`**&#x20;directive?**

It marks a function or module as containing Server Actions.

**Q3. What is the difference between Server Components and Server Actions?**

Server Components render UI on the server. Server Actions execute server-side operations, commonly mutations such as creating, updating and deleting records.

**Q4. How do you connect a form to a Server Action?**

Using the `action` attribute:

```
<form action={createProduct}>
```

**Q5. What is FormData?**

FormData is a Web API that represents form fields and their submitted values.

**Q6. Do Server Actions require manually created API endpoints?**

No. Next.js handles the invocation internally.

## **Intermediate level**

**Q7. What is&#x20;**`useActionState()`**?**

It is a React Hook that manages the result of an action, its pending state and the form action.

**Q8. What is&#x20;**`useFormStatus()`**?**

It is a React Hook used by descendant components to access the pending status of their parent form.

**Q9. What is&#x20;**`useOptimistic()`**?**

It allows React to display an optimistic UI update before the server operation has completed.

**Q10. How do you pass an ID to a Server Action?**

You can use `bind()` or a hidden form field. The server must still validate the ID.

**Q11. What is&#x20;**`revalidatePath()`**?**

It invalidates cached data associated with a specified route path.

**Q12. What is the difference between&#x20;**`revalidateTag()`**&#x20;and&#x20;**`updateTag()`**?**

`revalidateTag()` supports stale-while-revalidate behavior, while `updateTag()` immediately expires tagged data and is designed for read-your-own-writes scenarios in Server Actions.

## **Advanced level**

**Q13. Can Server Actions be called from Client Components?**

Yes. Import an exported Server Action from a module marked with `"use server"`.

**Q14. Can Server Actions be invoked without using a form?**

Yes. They can also be called from event handlers and other supported action contexts.

**Q15. Are Server Actions secure automatically?**

No. Authentication, authorization and input validation must be implemented by the application.

**Q16. Which HTTP method is used internally to invoke Server Actions?**

POST.

**Q17. What is progressive enhancement in Server Actions?**

It allows forms to work before client-side JavaScript has loaded, including submissions from Server Components.

**Q18. Can we use MongoDB directly inside Server Actions?**

Yes. Server Actions execute on the server and can access server-side database modules.

**Q19. Why should we use Zod with Server Actions?**

To validate incoming data, enforce data types and constraints, and return structured validation errors.

**Q20. Should we use Server Actions instead of Route Handlers for every API?**

No. Server Actions are particularly useful for UI-driven mutations. Route Handlers are better suited to explicit HTTP APIs, external integrations and webhooks.

# **22. Phase 12 – Quick revision notes**

| Concept                        | Remember                          |
| ------------------------------ | --------------------------------- |
| Server Action                  | Asynchronous server-side function |
| Directive                      | `"use server"`                    |
| Form integration               | `<form action={action}>`          |
| Request data                   | `FormData`                        |
| Additional arguments           | `bind()`                          |
| Action state                   | `useActionState()`                |
| Pending form state             | `useFormStatus()`                 |
| Transition state               | `useTransition()`                 |
| Optimistic UI                  | `useOptimistic()`                 |
| Validation                     | Zod                               |
| Refresh a path                 | `revalidatePath()`                |
| Revalidate tagged data         | `revalidateTag()`                 |
| Immediately expire tagged data | `updateTag()`                     |
| Redirect                       | `redirect()`                      |
| Read cookies                   | `cookies().get()`                 |
| Set cookies                    | `cookies().set()`                 |
| Delete cookies                 | `cookies().delete()`              |
| Authentication                 | Verify user identity              |
| Authorization                  | Verify user permissions           |
| Database mutation              | Create, Update, Delete            |

## **23. Phase 12 completion checklist**

- What are Server Actions?
- What is `"use server"`?
- What is the difference between Server Actions and Route Handlers?
- How do you connect a form to a Server Action?
- How does FormData work?
- How do you return data from a Server Action?
- What is `useActionState()`?
- What is `useFormStatus()`?
- What is `useTransition()`?
- What is `useOptimistic()`?
- How do you pass arguments using `bind()`?
- How do you perform CRUD operations?
- How do you validate data using Zod?
- What is the difference between `revalidatePath()`, `revalidateTag()` and `updateTag()`?
- How do you implement authentication and authorization?
- How do you use cookies in Server Actions?

## **24. What's next?**

**Phase 13 – Middleware / Proxy and Request Interception**

Next

[Bên Trong Node.js (P4) - Express Framework – Ren](https://images.openai.com/static-rsc-4/rAB1aDopfrmFVJ281ZTod6n8qbtZQuBarY5tH_iailkqLZBu-ZJFR0-6ZoQ5FawlvUEoY7x-cg23L0KK5srwpcickJ05k2JKJSU_EPB6RwnWMf-IWOYeHT-x7W2Vtq3he0427ZQ0p_0hQJ0Xo7HqHpxlLffTfdp5pyQoI4q0jTg?purpose=inline)

[ren0503.github.io](https://ren0503.github.io/node-express-p1/?utm_source=chatgpt.com)

In the next phase, we will cover:

1. What is middleware and request interception?
2. Understanding the Next.js 16 `proxy.ts` convention.
3. How Proxy works in the request lifecycle.
4. Redirecting unauthenticated users.
5. Protecting dashboard routes.
6. Matching routes using `matcher`.
7. Reading and modifying request and response headers.
8. Cookies in Proxy.
9. Authentication checks and authorization limitations.
10. Proxy vs Middleware vs Route Handlers.
11. Real-world protected dashboard implementation.

When you're ready, say **OK, Phase 13**.

**Official references for Phase 12**

1. [Next.js – Mutating Data and Server Functions](https://nextjs.org/docs/app/getting-started/mutating-data?utm_source=chatgpt.com)
2. [Next.js – Forms and Server Actions](https://nextjs.org/docs/app/guides/forms?utm_source=chatgpt.com)
3. [Next.js – Revalidating Data](https://nextjs.org/docs/app/getting-started/revalidating?utm_source=chatgpt.com)
4. [Next.js 16 Upgrade Guide](https://nextjs.org/docs/app/guides/upgrading/version-16?utm_source=chatgpt.com)
5. [React – useActionState](https://react.dev/reference/react/useActionState?utm_source=chatgpt.com)
6. [React – useOptimistic](https://react.dev/reference/react/useOptimistic?utm_source=chatgpt.com)