# **Phase 20 – Testing Next.js Applications**



Advanced Level

Next.js 16.x

App Router + TypeScript



## **1. Learning objectives**

Testing is an essential part of professional software development. It helps you identify bugs before users encounter them and makes your application safer to change.

By the end of this phase, you will understand how to:

1. Understand software testing fundamentals.
2. Differentiate unit, integration, and end-to-end testing.
3. Configure Vitest for a Next.js application.
4. Test utility functions and React components.
5. Use React Testing Library to test user interactions.
6. Test API Route Handlers.
7. Understand testing Server Components and Server Actions.
8. Use Playwright for browser automation and E2E testing.
9. Mock APIs, dependencies, and external services.
10. Generate test coverage reports.
11. Integrate automated tests into CI/CD pipelines.

## **2. What is software testing?**

Software testing is the process of verifying that an application behaves as expected.

Suppose you build a registration form.

It should:

- Accept a valid name.
- Accept a valid email address.
- Reject an invalid email address.
- Require a password.
- Display validation errors.
- Create a user after successful registration.

Without testing, you might manually check these behaviors every time you change the application.

With automated tests, you can write test cases once and execute them whenever required.

### **Example**

Suppose you have this function:

```
function add(a: number, b: number): number {
  return a + b;
}
```

You expect:

```
add(10, 20) === 30
```

A test verifies that expectation automatically.

```
expect(add(10, 20)).toBe(30);
```

If a future code change accidentally breaks the function, the test will detect it.

## **3. Types of software testing**

There are three major testing levels you should understand.

## **Testing pyramid**

**E2E tests**

Few, broader tests

**Integration tests**

Test connected components

**Unit tests**

Many, focused tests

A common testing strategy emphasizes many fast unit tests, fewer integration tests, and a smaller number of end-to-end tests.

### **3.1 Unit testing**

Unit testing verifies a small, isolated piece of code.

Examples:

- A function.
- A utility method.
- A validation function.
- A date formatter.
- A calculation function.

Example:

```
export function calculateTotal(
  price: number,
  quantity: number
): number {
  return price * quantity;
}
```

Test:

```
import { describe, it, expect } from "vitest";
import { calculateTotal } from "./calculateTotal";

describe("calculateTotal", () => {
  it("calculates the total price", () => {
    expect(calculateTotal(100, 3)).toBe(300);
  });
});
```

Here, you are testing only the calculation function.

You are not testing the database, API, browser, or complete application.

### **3.2 Integration testing**

Integration testing verifies that multiple parts of an application work together correctly.

For example, consider a login feature:

```
Login Form
    |
    v
Validation
    |
    v
Authentication Function
    |
    v
Database
    |
    v
Session Creation
```

An integration test might verify that submitting valid credentials invokes the authentication logic and produces the expected result.

Other examples:

- Component + API interaction.
- Route Handler + database repository.
- Form + validation + Server Action.
- Multiple connected service functions.

### **3.3 End-to-end testing (E2E)**

E2E testing verifies an entire application workflow from the user's perspective.

For example:

```
Open website
    |
    v
Navigate to login page
    |
    v
Enter email and password
    |
    v
Click Login
    |
    v
Authentication succeeds
    |
    v
Dashboard appears
```

An E2E test can perform these actions in a real browser and verify the visible result.

### **Comparison**

| Feature           | Unit            | Integration              | E2E                    |
| ----------------- | --------------- | ------------------------ | ---------------------- |
| Scope             | Small unit      | Multiple connected units | Complete workflow      |
| Speed             | Usually fast    | Medium                   | Usually slower         |
| Browser required  | No              | Not always               | Yes                    |
| Database required | Usually mocked  | Sometimes                | Depends on test setup  |
| Example           | Calculate total | Test login service       | Complete login flow    |
| Main purpose      | Verify logic    | Verify interactions      | Verify user experience |

## **4. Testing tools used with Next.js**

There are several testing tools available. You do not need to use all of them in every project.

[Vitest · GitHub](https://images.openai.com/static-rsc-4/CRg4-RUlP-T9ZFqvU7N-64XNXIPumgscwnfTsXesNX6dBH--pVjpXYdM_MupmdeHUmqXeGdJceB-Uxkr8uVdNTUq-r1-IpK7hWc16T403QR07NDn0Ih8ke29VyOJ_utn-X98mH7aaIxB7zXMf83DmM1tQXvWFO9ThoABLhz12TQ?purpose=inline)

**Vitest**

Used for unit tests and many integration tests. It provides a fast test runner and assertion APIs.

[Official website](https://vitest.dev/?utm_source=chatgpt.com)

[Migrating from enzyme to testing-library/react | geOps](https://images.openai.com/static-rsc-4/GCku5MREhao2874ZYlhArCCM7ObvYc28MqqETdBc_cDFh0DE1nkiRIWIminAqvwgHByWccg70jvD-76gwrkPC5hZMj-eag2riytMxuiLQ6W8RciGot-pAMDUe4DTjHdYnH1rH8UMW-NUt7za2ktldXSq52LbULu8KdDmmvyS4KE?purpose=inline)

**React Testing Library**

Used to render React components and test them through user-visible behavior.

[Official documentation](https://testing-library.com/docs/react-testing-library/intro/?utm_source=chatgpt.com)

[Playwright — Features and Comparison | by leedohyung28 | Medium](https://images.openai.com/static-rsc-4/F5rN5e9g7czoZDE6rw_2bNOJnk2-jLtlCtHULvQ3hPE-NN5S9gNSPvtQqtqweZjcb8nl9eUCbSaiV3CW6MhXqq8LniQ-dB9xJNfO5eplsSFRN7-jsAjaZaHgGA-B4VUqoW41RbAKKy5CUDfwSs_a1ZyRP7NdzSjt8NfLbpZxe1w?purpose=inline)

**Playwright**

Used to automate real browsers and test complete user workflows.

[Official website](https://playwright.dev/?utm_source=chatgpt.com)

[Test Component Rendering with Jest and Testing Library | egghead.io](https://images.openai.com/static-rsc-4/Ii1BHzT0uGZTw2E8yn4OvIGcBKFmsoEgIlvN3XaqEVZuyJM7s1kJ1aG7hpJ3JPsLojWIREPIl0aTfzmXAgZzg0vGD_WXMjDihymR1bI3K0WWZi1AR9J56SOClygYtzS5Z3Omx0YHdtQ8X14Hzvl-ETOQm4YYJW8j8_N0WZ-xPfE?purpose=inline)

**Jest**

Another popular testing framework, often used with React Testing Library.

[Official website](https://jestjs.io/?utm_source=chatgpt.com)

### **Which tools should you learn?**

For a modern Next.js App Router project, a practical combination is:

| Tool                          | Responsibility                       |
| ----------------------------- | ------------------------------------ |
| Vitest                        | Unit and integration tests           |
| React Testing Library         | React component tests                |
| `@testing-library/user-event` | Simulate realistic user interactions |
| Playwright                    | End-to-end browser tests             |

You can also use Jest instead of Vitest. Next.js officially documents both options.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+3



## **5. Understanding test terminology**

Before writing tests, learn these commonly used terms.

### **5.1 Test case**

A test case is an individual scenario that verifies a particular behavior.

Example:

```
Test: User can log in with valid credentials.

Input:
Email: user@example.com
Password: Password123

Expected:
User is redirected to dashboard.
```

### **5.2 Test suite**

A test suite is a group of related test cases.

```
describe("Login functionality", () => {
  it("accepts valid credentials", () => {
    // Test
  });

  it("rejects invalid credentials", () => {
    // Test
  });

  it("shows an error for missing credentials", () => {
    // Test
  });
});
```

### **5.3 Assertion**

An assertion checks whether the actual result matches the expected result.

```
expect(2 + 2).toBe(4);
```

Here:

- Actual result = `4`
- Expected result = `4`
- Assertion passes.

If the actual result is `5`, the test fails.

### **5.4 Test runner**

A test runner discovers and executes test files, reports results, and manages test execution.

Examples:

- Vitest.
- Jest.

### **5.5 Mock**

A mock replaces a real dependency with a controlled fake implementation.

For example, instead of calling a real payment API during a test, you can mock its response.

### **5.6 Fixture**

A fixture is reusable test data or setup used by one or more tests.

Example:

```
export const testUser = {
  name: "Test User",
  email: "test@example.com",
  password: "Password123",
};
```

### **5.7 Setup and teardown**

- Setup: Prepare the environment before a test.
- Teardown: Clean up after a test.

Examples include creating test records before a test and deleting them afterward.

## **6. Install and configure Vitest**

Let's configure Vitest in a Next.js 16 TypeScript project.

### **6.1 Install dependencies**

Run:

```
npm install -D vitest @vitejs/plugin-react jsdom @testing-library/react @testing-library/dom vite-tsconfig-paths
```

For realistic interactions, also install:

```
npm install -D @testing-library/user-event @testing-library/jest-dom
```

What are these packages?

| Package                       | Purpose                                    |
| ----------------------------- | ------------------------------------------ |
| `vitest`                      | Test runner and assertions                 |
| `@vitejs/plugin-react`        | React support in the Vite test environment |
| `jsdom`                       | Simulated browser DOM                      |
| `@testing-library/react`      | Render and test React components           |
| `@testing-library/dom`        | DOM querying utilities                     |
| `vite-tsconfig-paths`         | Support TypeScript path aliases            |
| `@testing-library/user-event` | Simulate user interactions                 |
| `@testing-library/jest-dom`   | Additional DOM assertions                  |

### **6.2 Create Vitest configuration**

Create:

```
vitest.config.mts
```

Code:

```
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [
    tsconfigPaths(),
    react(),
  ],

  test: {
    environment: "jsdom",
  },
});
```

**What is jsdom?**

`jsdom` provides a simulated browser DOM environment in Node.js.

For example, when testing a React component, you might need:

```
document.querySelector("button");
```

But Node.js does not provide a normal browser `document` object.

`jsdom` provides a simulated DOM so component tests can interact with rendered HTML without launching a full browser.

It is not a complete browser engine. For real browser behavior, use Playwright.

This configuration follows the Next.js Vitest setup guidance.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **6.3 Configure package.json scripts**

```
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "test": "vitest",
    "test:run": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```

Understand the commands:

| Command                 | Purpose                    |
| ----------------------- | -------------------------- |
| `npm run test`          | Run Vitest in watch mode   |
| `npm run test:run`      | Run tests once             |
| `npm run test:watch`    | Watch and rerun tests      |
| `npm run test:coverage` | Generate a coverage report |

To run tests:

```
npm run test
```

### **6.4 Recommended project structure**

You can colocate test files near the code they test or use dedicated test directories.

Example:

```
my-next-app/
│
├── app/
│   ├── page.tsx
│   ├── layout.tsx
│   └── dashboard/
│       └── page.tsx
│
├── components/
│   ├── Button.tsx
│   └── Button.test.tsx
│
├── lib/
│   ├── calculateTotal.ts
│   └── calculateTotal.test.ts
│
├── __tests__/
│   └── page.test.tsx
│
├── vitest.config.mts
├── package.json
└── tsconfig.json
```

Common naming conventions:

```
calculateTotal.test.ts
calculateTotal.spec.ts
```

Both `.test` and `.spec` are commonly used to identify test files. Choose one convention and apply it consistently.

## **7. Your first unit test with Vitest**

Let's create a real utility function and test it.

### **Step 1 – Create a function**

Create:

```
lib/calculateTotal.ts
```

```
export function calculateTotal(
  price: number,
  quantity: number
): number {
  return price * quantity;
}
```

### **Step 2 – Create its test file**

Create:

```
lib/calculateTotal.test.ts
```

```
import { describe, it, expect } from "vitest";

import { calculateTotal } from "./calculateTotal";

describe("calculateTotal", () => {
  it("calculates the total price", () => {
    const result = calculateTotal(100, 3);

    expect(result).toBe(300);
  });

  it("returns zero when quantity is zero", () => {
    const result = calculateTotal(100, 0);

    expect(result).toBe(0);
  });

  it("handles decimal prices", () => {
    const result = calculateTotal(10.5, 2);

    expect(result).toBe(21);
  });
});
```

### **Step 3 – Run the test**

```
npm run test:run
```

Expected result:

```
✓ lib/calculateTotal.test.ts

Test Files  1 passed (1)
Tests       3 passed (3)
```

The exact terminal formatting can vary by Vitest version.

### **Understand the syntax**

```
describe("calculateTotal", () => {
  // Group of related tests
});
```

`describe()` groups test cases.

```
it("calculates the total price", () => {
  // Test logic
});
```

`it()` defines one test case. `test()` is an alias.

```
expect(result).toBe(300);
```

`expect()` starts an assertion.

`toBe()` checks strict equality.

## **8. Common Vitest assertions**

You will use assertions frequently when writing tests.

| Assertion           | Purpose                       | Example                          |
| ------------------- | ----------------------------- | -------------------------------- |
| `toBe()`            | Strict equality               | `expect(2).toBe(2)`              |
| `toEqual()`         | Deep equality                 | `expect(obj).toEqual({id: 1})`   |
| `toBeTruthy()`      | Checks truthy value           | `expect(true).toBeTruthy()`      |
| `toBeFalsy()`       | Checks falsy value            | `expect(false).toBeFalsy()`      |
| `toBeNull()`        | Checks null                   | `expect(null).toBeNull()`        |
| `toBeDefined()`     | Checks not undefined          | `expect(value).toBeDefined()`    |
| `toContain()`       | Checks array/string inclusion | `expect(names).toContain("Ali")` |
| `toHaveLength()`    | Checks length                 | `expect(users).toHaveLength(3)`  |
| `toBeGreaterThan()` | Greater than                  | `expect(10).toBeGreaterThan(5)`  |
| `toBeLessThan()`    | Less than                     | `expect(3).toBeLessThan(5)`      |
| `toThrow()`         | Checks thrown error           | `expect(fn).toThrow()`           |

### `toBe()`**&#x20;vs&#x20;**`toEqual()`

This is a common interview question.

```
const user1 = {
  id: 1,
  name: "Ali",
};

const user2 = {
  id: 1,
  name: "Ali",
};
```

Using `toEqual()`:

```
expect(user1).toEqual(user2);
```

Passes because their object contents are equal.

Using `toBe()`:

```
expect(user1).toBe(user2);
```

Fails because these are two different object references.

Remember:

- `toBe()` checks strict equality.
- `toEqual()` checks deep equality of object or array contents.

### **Testing errors**

Suppose you have:

```
export function divide(a: number, b: number) {
  if (b === 0) {
    throw new Error("Cannot divide by zero");
  }

  return a / b;
}
```

Test:

```
import { expect, it } from "vitest";
import { divide } from "./divide";

it("throws an error when dividing by zero", () => {
  expect(() => divide(10, 0)).toThrow(
    "Cannot divide by zero"
  );
});
```

Notice that we pass a function to `expect()`, not the direct result of calling `divide()`.

## **9. Testing asynchronous functions**

Many Next.js applications use asynchronous functions for:

- Database queries.
- API calls.
- File operations.
- Authentication.
- External service integrations.

Vitest supports asynchronous tests using `async` and `await`.

### **Example**

Create:

```
// lib/getUser.ts

export async function getUser() {
  return {
    id: 1,
    name: "Test User",
  };
}
```

Test:

```
import { describe, it, expect } from "vitest";
import { getUser } from "./getUser";

describe("getUser", () => {
  it("returns a user", async () => {
    const user = await getUser();

    expect(user.id).toBe(1);
    expect(user.name).toBe("Test User");
  });
});
```

### **Testing rejected promises**

```
export async function fetchUser() {
  throw new Error("User not found");
}
```

Test:

```
it("throws when the user is unavailable", async () => {
  await expect(fetchUser()).rejects.toThrow(
    "User not found"
  );
});
```

Important: Always `await` asynchronous assertions. Otherwise, the test runner might finish before the assertion completes.

## **10. Mocking functions and dependencies**

Mocking is one of the most important concepts in testing.

Suppose your function calls an external payment service:

```
export async function processPayment(amount: number) {
  // Calls external payment provider
}
```

You should not make real payment transactions every time you run your tests.

Instead, mock the payment service.

### **10.1 What is a mock?**

A mock is a replacement for a real dependency that lets you control its behavior during testing.

Example:

```
import { vi, it, expect } from "vitest";

it("tests a mocked function", () => {
  const paymentMock = vi.fn();

  paymentMock(500);

  expect(paymentMock).toHaveBeenCalled();
  expect(paymentMock).toHaveBeenCalledWith(500);
});
```

Here:

```
vi.fn()
```

Creates a mock function.

### **10.2 Mock return values**

```
const getUserMock = vi.fn();

getUserMock.mockReturnValue({
  id: 1,
  name: "Ali",
});
```

Now:

```
getUserMock();
```

Returns:

```
{
  id: 1,
  name: "Ali"
}
```

### **10.3 Mock asynchronous functions**

```
const getUserMock = vi.fn();

getUserMock.mockResolvedValue({
  id: 1,
  name: "Ali",
});
```

This returns a resolved Promise.

For errors:

```
getUserMock.mockRejectedValue(
  new Error("Database unavailable")
);
```

### **10.4 Mock a module**

Suppose you have a service:

```
// lib/userService.ts

export async function getUserById(id: string) {
  // Real database query
}
```

Another module uses it:

```
// lib/userController.ts

import { getUserById } from "./userService";

export async function getUserProfile(id: string) {
  return await getUserById(id);
}
```

Test the controller without accessing the real database:

```
import { beforeEach, describe, expect, it, vi } from "vitest";

import { getUserProfile } from "./userController";
import { getUserById } from "./userService";

vi.mock("./userService", () => ({
  getUserById: vi.fn(),
}));

const mockedGetUserById = vi.mocked(getUserById);

describe("getUserProfile", () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  it("returns the user profile", async () => {
    mockedGetUserById.mockResolvedValue({
      id: "123",
      name: "Ali",
    });

    const result = await getUserProfile("123");

    expect(result.name).toBe("Ali");

    expect(mockedGetUserById).toHaveBeenCalledWith("123");
  });
});
```

The example assumes the service returns a user object with `id` and `name`.

### **Why use mocking?**

Without mocking:

```
Test
  |
  v
Controller
  |
  v
Real database
  |
  v
Database connection required
```

With mocking:

```
Test
  |
  v
Controller
  |
  v
Mocked service
  |
  v
Controlled response
```

Benefits:

- Tests run faster.
- Tests are predictable.
- You do not need real external services.
- You can simulate error conditions.
- You avoid unwanted side effects.

**Important:** Do not mock everything. If you mock the entire behavior you want to verify, the test becomes meaningless. Mock external boundaries while keeping the important application logic real.

## **11. Testing React components with React Testing Library**

Now let's move from testing ordinary functions to testing React components.

React Testing Library encourages you to test components the way users interact with them, instead of inspecting internal implementation details.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://testing-library.com\&sz=32)

Testing Library+1



### **11.1 Create a Button component**

```
// components/Button.tsx

type ButtonProps = {
  label: string;
  onClick: () => void;
};

export default function Button({
  label,
  onClick,
}: ButtonProps) {
  return (
    <button onClick={onClick}>
      {label}
    </button>
  );
}
```

### **11.2 Create a test**

```
// components/Button.test.tsx

import { describe, it, expect, vi } from "vitest";

import { render, screen } from "@testing-library/react";

import userEvent from "@testing-library/user-event";

import Button from "./Button";

describe("Button component", () => {
  it("renders the button label", () => {
    const onClick = vi.fn();

    render(
      <Button
        label="Save"
        onClick={onClick}
      />
    );

    expect(
      screen.getByRole("button", { name: "Save" })
    ).toBeInTheDocument();
  });

  it("calls onClick when clicked", async () => {
    const user = userEvent.setup();

    const onClick = vi.fn();

    render(
      <Button
        label="Save"
        onClick={onClick}
      />
    );

    await user.click(
      screen.getByRole("button", { name: "Save" })
    );

    expect(onClick).toHaveBeenCalledTimes(1);
  });
});
```

### **11.3 Configure jest-dom matchers**

The assertion:

```
.toBeInTheDocument()
```

comes from `@testing-library/jest-dom`.

Create:

```
// vitest.setup.ts

import "@testing-library/jest-dom/vitest";
```

Update your Vitest configuration:

```
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [
    tsconfigPaths(),
    react(),
  ],

  test: {
    environment: "jsdom",
    setupFiles: ["./vitest.setup.ts"],
  },
});
```

Now you can use assertions such as:

```
expect(element).toBeInTheDocument();

expect(button).toBeDisabled();

expect(input).toHaveValue("test@example.com");

expect(element).toHaveTextContent("Success");
```

### **11.4 Understanding&#x20;**`render()`**&#x20;and&#x20;**`screen`

`render()` mounts a React component into the simulated DOM.

```
render(<Button label="Save" onClick={onClick} />);
```

`screen` provides queries for finding elements in that DOM.

For example:

```
screen.getByRole("button");
```

Finds a button.

### **11.5 Different query methods**

| Query        | Behavior                                         |
| ------------ | ------------------------------------------------ |
| `getBy...`   | Returns element or throws immediately if missing |
| `queryBy...` | Returns element or `null`                        |
| `findBy...`  | Returns a Promise and waits for the element      |

Example:

```
screen.getByText("Welcome");
```

Use when the element should already exist.

```
screen.queryByText("Error");
```

Use when the element might not exist.

```
await screen.findByText("Data loaded");
```

Use when the element is expected to appear asynchronously.

### **11.6 Test user interactions**

The `user-event` library simulates realistic interactions.

Examples:

```
await user.click(button);
await user.type(input, "hello");
await user.clear(input);
await user.selectOptions(select, "india");
await user.keyboard("{Enter}");
```

Prefer `user-event` for normal user interactions. It generally models browser interaction more closely than directly dispatching individual DOM events.

### **11.7 What should you query first?**

Prefer accessible queries:

1. `getByRole()`
2. `getByLabelText()`
3. `getByPlaceholderText()`
4. `getByText()`
5. `getByTestId()` as a last resort

For example:

```
<button>Submit</button>
```

Prefer:

```
screen.getByRole("button", { name: "Submit" });
```

Instead of:

```
screen.getByTestId("submit-button");
```

This encourages accessible UI and tests the interface from the user's perspective.

## **12. Testing forms and validation**

Forms are among the most important things to test in a real application.

Suppose you have a registration form.

```
"use client";

import { useState } from "react";

export default function RegistrationForm() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  function handleSubmit(
    event: React.FormEvent<HTMLFormElement>
  ) {
    event.preventDefault();

    if (!email.includes("@")) {
      setError("Enter a valid email address");
      return;
    }

    setError("");
  }

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="email">Email</label>

      <input
        id="email"
        value={email}
        onChange={(event) => setEmail(event.target.value)}
      />

      <button type="submit">Register</button>

      {error && <p role="alert">{error}</p>}
    </form>
  );
}
```

Test:

```
import { describe, it, expect } from "vitest";

import { render, screen } from "@testing-library/react";

import userEvent from "@testing-library/user-event";

import RegistrationForm from "./RegistrationForm";

describe("RegistrationForm", () => {
  it("shows validation error for invalid email", async () => {
    const user = userEvent.setup();

    render(<RegistrationForm />);

    await user.type(
      screen.getByLabelText("Email"),
      "invalid-email"
    );

    await user.click(
      screen.getByRole("button", { name: "Register" })
    );

    expect(
      screen.getByRole("alert")
    ).toHaveTextContent("Enter a valid email address");
  });

  it("does not show error for a valid email", async () => {
    const user = userEvent.setup();

    render(<RegistrationForm />);

    await user.type(
      screen.getByLabelText("Email"),
      "user@example.com"
    );

    await user.click(
      screen.getByRole("button", { name: "Register" })
    );

    expect(
      screen.queryByRole("alert")
    ).not.toBeInTheDocument();
  });
});
```

What are we testing?

- The input accepts text.
- The form responds to submission.
- Invalid input displays an error.
- Valid input removes the error.

**Real-world note:** Client-side validation improves user experience, but it is not a security boundary. The server must validate input again.

## **13. Testing Next.js Route Handlers**

In Phase 11, you learned how to create API endpoints using `route.ts`.

Now let's test those endpoints.

Suppose you have:

```
app/
└── api/
    └── users/
        └── route.ts
```

### **13.1 Create a Route Handler**

```
// app/api/users/route.ts

export async function GET() {
  const users = [
    { id: 1, name: "Ali" },
    { id: 2, name: "Ahmed" },
  ];

  return Response.json(users);
}
```

### **13.2 Test the GET endpoint**

Create:

```
app/api/users/route.test.ts
```

```
import { describe, it, expect } from "vitest";

import { GET } from "./route";

describe("GET /api/users", () => {
  it("returns all users", async () => {
    const response = await GET();

    const data = await response.json();

    expect(response.status).toBe(200);

    expect(data).toHaveLength(2);

    expect(data[0].name).toBe("Ali");
  });
});
```

Notice that we are directly importing the Route Handler and calling its exported function.

We do not need to launch the Next.js server for this simple unit-level test.

### **13.3 Testing a POST endpoint**

Suppose your endpoint creates a user.

```
// app/api/users/route.ts

export async function POST(request: Request) {
  const body = await request.json();

  if (!body.name || !body.email) {
    return Response.json(
      { message: "Name and email are required" },
      { status: 400 }
    );
  }

  return Response.json(
    {
      message: "User created",
      user: body,
    },
    { status: 201 }
  );
}
```

Test:

```
import { describe, it, expect } from "vitest";

import { POST } from "./route";

describe("POST /api/users", () => {
  it("creates a user with valid data", async () => {
    const request = new Request(
      "http://localhost:3000/api/users",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          name: "Ali",
          email: "ali@example.com",
        }),
      }
    );

    const response = await POST(request);

    const data = await response.json();

    expect(response.status).toBe(201);

    expect(data.user.name).toBe("Ali");
  });

  it("rejects missing fields", async () => {
    const request = new Request(
      "http://localhost:3000/api/users",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          name: "Ali",
        }),
      }
    );

    const response = await POST(request);

    expect(response.status).toBe(400);
  });
});
```

### **13.4 What should you test in API endpoints?**

| Scenario                | Expected behavior        |
| ----------------------- | ------------------------ |
| Valid GET request       | Returns expected data    |
| Valid POST request      | Creates a resource       |
| Missing required fields | Returns 400              |
| Invalid data types      | Returns validation error |
| Unauthorized request    | Returns 401              |
| Forbidden request       | Returns 403              |
| Missing record          | Returns 404              |
| Unexpected server error | Returns 500              |

For endpoints that use databases, authentication, or external APIs, mock those dependencies in unit tests. Also write integration or E2E tests to verify that the pieces work together.

## **14. Testing database operations**

You should understand how to test functions that communicate with MongoDB or MySQL.

Suppose you have a service:

```
// lib/userService.ts

import { connectDB } from "./mongodb";
import User from "@/models/User";

export async function createUser(data: {
  name: string;
  email: string;
}) {
  await connectDB();

  return await User.create(data);
}
```

You have two broad testing approaches.

### **Approach A – Mock database operations**

Use this for unit tests.

```
Test
  |
  v
User Service
  |
  v
Mocked User Model
```

Benefits:

- Fast tests.
- No database setup.
- Easy to simulate duplicate email or database errors.

### **Approach B – Use a test database**

Use this for integration tests.

```
Test
  |
  v
User Service
  |
  v
Test MongoDB Database
```

You can verify:

- User creation.
- User retrieval.
- User updates.
- User deletion.
- Database constraints.
- Data persistence.

### **Important database testing rules**

1. Never run destructive tests against production.
2. Use a dedicated test database.
3. Clean up test records.
4. Use unique test identifiers where appropriate.
5. Avoid tests depending on execution order.
6. Do not share mutable test data between parallel tests without isolation.

For a MongoDB project, a dedicated database such as:

```
myapp_test
```

is preferable to your development or production database.

## **15. Testing Server Components**

This is particularly important in Next.js App Router.

Remember:

- Server Components are the default.
- Client Components use `"use client"`.
- Server Components can fetch data directly.
- Async Server Components can await database queries or other asynchronous operations.

Example:

```
// app/users/page.tsx

import { getUsers } from "@/lib/users";

export default async function UsersPage() {
  const users = await getUsers();

  return (
    <main>
      <h1>Users</h1>

      {users.map((user) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </main>
  );
}
```

### **Important limitation**

Vitest currently does not fully support rendering asynchronous Server Components as ordinary component unit tests.

Next.js recommends E2E tests for async Server Components.

You can still unit-test synchronous components and test the data-fetching functions separately.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **Recommended testing strategy**

Instead of trying to test the entire async Server Component directly:

**Test the data function:**

```
// lib/users.ts

export async function getUsers() {
  return [
    { id: "1", name: "Ali" },
    { id: "2", name: "Ahmed" },
  ];
}
```

Unit test the data function separately.

**Then test the actual rendered page with Playwright:**

```
import { test, expect } from "@playwright/test";

test("users page displays users", async ({ page }) => {
  await page.goto("/users");

  await expect(
    page.getByRole("heading", { name: "Users" })
  ).toBeVisible();

  await expect(
    page.getByText("Ali")
  ).toBeVisible();
});
```

This verifies the page through a real browser.

### **Why not test everything using only unit tests?**

Because a unit test may confirm that your data function works, but it does not necessarily verify:

- The page renders correctly.
- The layout is correct.
- Server rendering works.
- Navigation works.
- The user sees the expected output.

That is why different testing levels are necessary.

## **16. Testing Server Actions**

In Phase 12, you learned about Server Actions.

Example:

```
// app/actions.ts

"use server";

export async function createProduct(formData: FormData) {
  const name = formData.get("name");

  if (!name) {
    return {
      success: false,
      message: "Product name is required",
    };
  }

  // Save product to database

  return {
    success: true,
    message: "Product created",
  };
}
```

### **What should be tested?**

| Scenario              | Expected result           |
| --------------------- | ------------------------- |
| Product name missing  | Validation error          |
| Product name valid    | Product creation succeeds |
| Database error        | Controlled error response |
| Unauthorized user     | Access denied             |
| User lacks permission | Forbidden result          |

### **Testing a Server Action**

You can unit-test the exported function by calling it directly, provided you isolate or mock its external dependencies.

For example:

```
import { describe, it, expect } from "vitest";

import { createProduct } from "./actions";

describe("createProduct", () => {
  it("rejects an empty product name", async () => {
    const formData = new FormData();

    const result = await createProduct(formData);

    expect(result.success).toBe(false);

    expect(result.message).toBe(
      "Product name is required"
    );
  });
});
```

This tests the validation branch without making a database call.

For actions that use `cookies()`, `redirect()`, cache revalidation, authentication, or database operations, isolate and mock those dependencies as appropriate. Also verify the complete form submission through Playwright.

**Important:** A direct function test does not verify the complete Next.js Server Action request protocol. Browser-level tests are useful for that.

## **17. End-to-end testing with Playwright**

Very important

Now we move to one of the most useful testing tools for full-stack developers.

[UI Mode | Playwright](https://images.openai.com/static-rsc-4/w0UycS9BsTbjTKFMv8GK9XnqNG0O1OdClSA-AqcuUMdA9wjuhkTBflr7l0NiPq4NAAIlF6Fipso8Ogz2UTHWLwzZYPLKQRFMrRhWdcIPZ7hpBxN36SBJDdaqOtFHLbvF9mYEZ1d-8p1vYUBPrJlpZiCrNytnXd-4ipSn5fo_9OU?purpose=inline)

[Best Practices | Playwright](https://images.openai.com/static-rsc-4/gZq5zDYmQ7Z49xsEQYp1XkHLbvymAU4J2uBz-uehnH9OMDHuEuw4n-hdgPuZS9y1_hF9DsCTWHbwM72j0tHwEjv9VHI1feLfww8ZmxfkhonmWZmkFjNuelu22hvNgu3BiL9vBb8WcwTrDE-61nQ-XxI6aPr0OkDcsv6ykxENdbk?purpose=inline)

[Best Practices | Playwright](https://images.openai.com/static-rsc-4/s95mNAqYWqokJFioj16G2-8OzqOla9hgbEWoXWIv2fFVwvwbl4KPPvQBwJRYeykyjbHQfiUKKBxeIg3DkY2IibvNCPETRUhCId0d-AsGndJ1oXwuSzNuwbwA7QJCm3MiNoqIZnp3Nj9G_WBbj87LrWcddK6xajeHCYepfgnf_t0?purpose=inline)

[playwright 1.35.0 on Node.js NPM](https://images.openai.com/static-rsc-4/5SqXZbnCo3i9eWpYkN-uuFQMfOpkirPDBJSGEzX3G-KREbQm7BY1jEgzh8CHoeh1sOgXVEeVpjhxk7Hekr9iGENMEROHNEvGJmzT32s2uxTEbfjfvHQLU6YaoaPxVLDslk3wS34bzHY--V61TU6SpCPECP5VTBnvWzqvfCgAQ70?purpose=inline)

[Debugging Tests | Playwright](https://images.openai.com/static-rsc-4/VS96N8_JeNSoXhHl1OYoOFXz_UTVNDR7tYjzYIMCXdcYJDtPFRPPQr-lOzylWEHWW6wxXBbIO-u7b6Gst8d27UJUBp9KlDC3G7xyTCrvr3WqbZn8NnHY3qNsPiFih2gHyTq5F5iIkCs4Ut40R4lJ60Pg4D28MTVuHSC6CgsYhEc?purpose=inline)

[Mock APIs | Playwright .NET](https://images.openai.com/static-rsc-4/_E2o6AwhSM9FJwS133H9jCCgtOIYTGG7RdTw8dun9Xx9KjS1FU8Ly8_JXt_BTI2X9clUJWVJiESqIcqFzGhMIvO67TWDHrCl8aUu1tR9d6k34g9yGlgCkAWUmrU7O4W2QUc_KvgmP8OfgyO8HQMpTd34X-2sgyAGLLLTJywf4b4?purpose=inline)

Playwright automates real browsers and lets you interact with your application programmatically.

It supports:

- Chromium.
- Firefox.
- WebKit.

You can simulate a real user:

- Open a website.
- Click buttons.
- Enter text.
- Select options.
- Navigate between pages.
- Submit forms.
- Upload files.
- Verify results.

The official Next.js guide recommends Playwright for E2E testing, including testing asynchronous Server Components.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



### **17.1 Install Playwright**

Run:

```
npm init playwright
```

Follow the setup prompts.

Alternatively, install the package directly:

```
npm install -D @playwright/test
```

Install the browsers:

```
npx playwright install
```

If browser system dependencies are missing on Linux:

```
npx playwright install-deps
```

### **17.2 Understand the generated files**

A typical structure:

```
my-next-app/
│
├── app/
│
├── tests/
│   └── example.spec.ts
│
├── playwright.config.ts
│
└── package.json
```

The important files:

| File                   | Purpose                        |
| ---------------------- | ------------------------------ |
| `playwright.config.ts` | Browser and test configuration |
| `tests/`               | E2E test files                 |
| `*.spec.ts`            | Individual test specifications |

### **17.3 Configure Playwright**

Create or update:

```
// playwright.config.ts

import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "./tests",

  fullyParallel: true,

  reporter: "html",

  use: {
    baseURL: "http://localhost:3000",

    trace: "on-first-retry",

    screenshot: "only-on-failure",

    video: "retain-on-failure",
  },

  projects: [
    {
      name: "chromium",
      use: {
        ...devices["Desktop Chrome"],
      },
    },
  ],

  webServer: {
    command: "npm run dev",
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
    timeout: 120 * 1000,
  },
});
```

What does this configuration do?

- `testDir`: Location of test files.
- `fullyParallel`: Allows independent tests to run in parallel.
- `reporter`: Generates an HTML report.
- `baseURL`: Base address of your application.
- `trace`: Captures execution traces when a test is retried.
- `screenshot`: Saves screenshots on failures.
- `video`: Retains videos for failed tests.
- `webServer`: Starts your Next.js application before tests.

For an initial development setup, using `npm run dev` is convenient. For production-like testing, configure the web server to run `npm run build && npm run start` and use an appropriate build environment.

### **17.4 Add package.json scripts**

```
{
  "scripts": {
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:e2e:headed": "playwright test --headed",
    "test:e2e:debug": "playwright test --debug"
  }
}
```

## **18. Write your first Playwright test**

Suppose your application has:

```
app/
├── page.tsx
└── about/
    └── page.tsx
```

Home page:

```
// app/page.tsx

import Link from "next/link";

export default function HomePage() {
  return (
    <main>
      <h1>Home</h1>

      <Link href="/about">
        About
      </Link>
    </main>
  );
}
```

About page:

```
// app/about/page.tsx

export default function AboutPage() {
  return (
    <main>
      <h1>About Page</h1>
    </main>
  );
}
```

Create:

```
// tests/navigation.spec.ts

import { test, expect } from "@playwright/test";

test("user can navigate to about page", async ({ page }) => {
  await page.goto("/");

  await page.getByRole("link", {
    name: "About",
  }).click();

  await expect(page).toHaveURL("/about");

  await expect(
    page.getByRole("heading", {
      name: "About Page",
    })
  ).toBeVisible();
});
```

Run:

```
npm run test:e2e
```

Playwright will launch the configured browser, navigate to your website, click the link, and verify the resulting page.

### **18.1 Understand the Playwright syntax**

```
test("test description", async ({ page }) => {
  // Test logic
});
```

`test()` defines a test case.

`page` represents a browser tab.

```
await page.goto("/");
```

Navigates to a URL.

```
await page.getByRole("link", { name: "About" }).click();
```

Finds the accessible link and clicks it.

```
await expect(page).toHaveURL("/about");
```

Checks the current URL.

```
await expect(element).toBeVisible();
```

Checks that the element is visible.

## **19. Testing login and registration workflows**

Let's create a more realistic E2E example.

Suppose you have a login page:

```
/login
```

It contains:

- Email input.
- Password input.
- Login button.
- Error message.

### **19.1 Example login page**

```
// app/login/page.tsx

"use client";

import { useState } from "react";

export default function LoginPage() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [message, setMessage] = useState("");

  function handleLogin(event: React.FormEvent) {
    event.preventDefault();

    if (
      email === "admin@example.com" &&
      password === "Password123"
    ) {
      setMessage("Login successful");
    } else {
      setMessage("Invalid credentials");
    }
  }

  return (
    <main>
      <h1>Login</h1>

      <form onSubmit={handleLogin}>
        <label htmlFor="email">Email</label>

        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
        />

        <label htmlFor="password">Password</label>

        <input
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
        />

        <button type="submit">Login</button>
      </form>

      {message && <p role="status">{message}</p>}
    </main>
  );
}
```

This is a simplified UI example, not a production authentication implementation.

### **19.2 Test successful login**

```
// tests/login.spec.ts

import { test, expect } from "@playwright/test";

test("user can log in with valid credentials", async ({ page }) => {
  await page.goto("/login");

  await page.getByLabel("Email").fill(
    "admin@example.com"
  );

  await page.getByLabel("Password").fill(
    "Password123"
  );

  await page.getByRole("button", {
    name: "Login",
  }).click();

  await expect(
    page.getByRole("status")
  ).toHaveText("Login successful");
});
```

### **19.3 Test invalid login**

```
test("shows an error for invalid credentials", async ({ page }) => {
  await page.goto("/login");

  await page.getByLabel("Email").fill(
    "wrong@example.com"
  );

  await page.getByLabel("Password").fill(
    "wrong-password"
  );

  await page.getByRole("button", {
    name: "Login",
  }).click();

  await expect(
    page.getByRole("status")
  ).toHaveText("Invalid credentials");
});
```

### **19.4 What should be tested in a real authentication system?**

| Scenario                         | Expected result              |
| -------------------------------- | ---------------------------- |
| Valid credentials                | Authentication succeeds      |
| Invalid password                 | Login rejected               |
| Unknown email                    | Login rejected               |
| Empty fields                     | Validation message           |
| Unauthenticated dashboard access | Redirect or access denied    |
| Expired session                  | User must authenticate again |
| Logout                           | Session invalidated          |
| Unauthorized API request         | 401                          |
| Insufficient permissions         | 403                          |

For a real application, test actual server-side authentication and session behavior, not just a client-side success message.

## **20. Mocking API requests in Playwright**

Sometimes you want to test a page without depending on a real backend.

Playwright provides request interception.

Suppose your page fetches:

```
/api/products
```

You can mock that response.

```
import { test, expect } from "@playwright/test";

test("displays products from mocked API", async ({ page }) => {
  await page.route("**/api/products", async (route) => {
    await route.fulfill({
      status: 200,
      contentType: "application/json",
      body: JSON.stringify([
        {
          id: "1",
          name: "Laptop",
          price: 50000,
        },
        {
          id: "2",
          name: "Mouse",
          price: 1000,
        },
      ]),
    });
  });

  await page.goto("/products");

  await expect(
    page.getByText("Laptop")
  ).toBeVisible();

  await expect(
    page.getByText("Mouse")
  ).toBeVisible();
});
```

### **Why mock API requests?**

You can test different conditions:

**Successful response**

```
await route.fulfill({
  status: 200,
  body: JSON.stringify({ success: true }),
});
```

**Server error**

```
await route.fulfill({
  status: 500,
  body: JSON.stringify({
    message: "Internal server error",
  }),
});
```

**Unauthorized response**

```
await route.fulfill({
  status: 401,
  body: JSON.stringify({
    message: "Unauthorized",
  }),
});
```

This helps you test loading states, error states, and success states without requiring a real external service.

**Important:** Request interception in Playwright is especially useful for testing browser-side network requests. It does not automatically intercept every server-side request made by a Server Component on the Next.js server. For those, use dependency injection, test data, or server-side mocks.

## **21. Testing file uploads**

Next.js applications often support uploading:

- Profile pictures.
- Documents.
- Resumes.
- Product images.
- PDFs.

Playwright can test file upload interactions.

Suppose your page contains:

```
<input type="file" aria-label="Upload resume" />
```

Test:

```
import { test, expect } from "@playwright/test";

test("user can select a resume file", async ({ page }) => {
  await page.goto("/upload");

  await page.getByLabel("Upload resume").setInputFiles({
    name: "resume.pdf",
    mimeType: "application/pdf",
    buffer: Buffer.from("Test PDF content"),
  });

  await expect(
    page.getByText("resume.pdf")
  ).toBeVisible();
});
```

This example tests file selection and UI behavior. It does not prove that the file is a valid PDF or that your server safely stores it.

For real upload testing, also verify:

- File type validation.
- Maximum file size.
- Server-side validation.
- Storage success.
- Rejection of invalid files.
- Access control for uploaded documents.

## **22. Playwright browser interaction methods**

These are frequently used methods.

| Method                    | Purpose                  |
| ------------------------- | ------------------------ |
| `page.goto()`             | Navigate to URL          |
| `page.getByRole()`        | Find by accessible role  |
| `page.getByText()`        | Find by visible text     |
| `page.getByLabel()`       | Find form field by label |
| `page.getByPlaceholder()` | Find by placeholder      |
| `locator.click()`         | Click an element         |
| `locator.fill()`          | Fill input               |
| `locator.clear()`         | Clear input              |
| `locator.check()`         | Check checkbox           |
| `locator.uncheck()`       | Uncheck checkbox         |
| `locator.selectOption()`  | Select dropdown option   |
| `locator.setInputFiles()` | Upload file              |
| `locator.isVisible()`     | Check visibility         |
| `page.screenshot()`       | Capture screenshot       |
| `page.reload()`           | Reload page              |
| `page.goBack()`           | Navigate back            |

### **Example**

```
test("user can fill a registration form", async ({ page }) => {
  await page.goto("/register");

  await page.getByLabel("Name").fill("Ali");

  await page.getByLabel("Email").fill(
    "ali@example.com"
  );

  await page.getByLabel("Password").fill(
    "Password123"
  );

  await page.getByLabel("Accept terms").check();

  await page.getByRole("button", {
    name: "Register",
  }).click();

  await expect(
    page.getByText("Registration successful")
  ).toBeVisible();
});
```

## **23. Debugging Playwright tests**

Sometimes a test fails, and you need to understand why.

Playwright provides useful debugging tools.

### **23.1 Run tests with a visible browser**

```
npx playwright test --headed
```

This opens the browser window so you can observe the test.

### **23.2 Run in debug mode**

```
npx playwright test --debug
```

This opens the Playwright Inspector and lets you step through test execution.

### **23.3 Open the HTML report**

```
npx playwright show-report
```

The HTML report helps you inspect:

- Passed tests.
- Failed tests.
- Execution time.
- Error messages.
- Screenshots.
- Trace information.

### **23.4 Use traces**

In the configuration, we used:

```
trace: "on-first-retry"
```

A trace can contain:

- Actions performed.
- DOM snapshots.
- Network activity.
- Screenshots.
- Timing information.

This makes it easier to diagnose a test that fails intermittently.

### **23.5 Take a screenshot manually**

```
await page.screenshot({
  path: "screenshots/homepage.png",
  fullPage: true,
});
```

This captures the page and saves an image.

## **24. Code coverage**

Code coverage tells you how much of your application code is executed while tests run.

Suppose you have:

```
export function calculateDiscount(price: number) {
  if (price >= 1000) {
    return price * 0.1;
  }

  return 0;
}
```

There are two possible branches:

1. Price is at least 1000.
2. Price is below 1000.

If you test only:

```
expect(calculateDiscount(2000)).toBe(200);
```

You have tested only one branch.

You should also test:

```
expect(calculateDiscount(500)).toBe(0);
```

### **24.1 Types of coverage**

| Coverage type      | Meaning                                  |
| ------------------ | ---------------------------------------- |
| Statement coverage | Percentage of statements executed        |
| Branch coverage    | Percentage of decision branches executed |
| Function coverage  | Percentage of functions called           |
| Line coverage      | Percentage of lines executed             |

### **24.2 Configure Vitest coverage**

Install:

```
npm install -D @vitest/coverage-v8
```

Update `vitest.config.mts`:

```
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [
    tsconfigPaths(),
    react(),
  ],

  test: {
    environment: "jsdom",

    setupFiles: ["./vitest.setup.ts"],

    coverage: {
      provider: "v8",

      reporter: [
        "text",
        "html",
      ],

      include: [
        "app/**/*.{ts,tsx}",
        "components/**/*.{ts,tsx}",
        "lib/**/*.{ts,tsx}",
      ],

      exclude: [
        "**/*.test.{ts,tsx}",
        "**/*.spec.{ts,tsx}",
      ],
    },
  },
});
```

### **24.3 Generate coverage**

```
npm run test:coverage
```

Vitest generates a coverage report, including an HTML report if configured.

The default output directory is commonly:

```
coverage/
```

Open the generated HTML report in a browser to inspect which lines and branches were covered.

### **Important**

100% code coverage does not automatically mean your application is bug-free.

A test can execute every line without checking whether the output is correct.

Focus on meaningful assertions and important user behavior, not just the percentage.

[image](https://www.google.com/s2/favicons?domain=https://vitest.dev\&sz=32)

Vitest

## **25. Snapshot testing**

Snapshot testing captures a rendered output and compares it with a previously saved snapshot.

Example:

```
import { describe, it, expect } from "vitest";

import { render } from "@testing-library/react";

import Button from "./Button";

describe("Button snapshot", () => {
  it("matches the snapshot", () => {
    const onClick = () => {};

    const { container } = render(
      <Button label="Save" onClick={onClick} />
    );

    expect(container).toMatchSnapshot();
  });
});
```

The first run creates a snapshot file.

On future runs, Vitest compares the current output against the stored snapshot.

If the rendered output changes unexpectedly, the test can fail.

### **When should you use snapshots?**

Useful for:

- Stable, small output structures.
- Certain UI components.
- Serialized data structures.

Avoid relying heavily on large snapshots because they can become difficult to review and maintain.

For most component tests, assertions about accessible roles, labels, and visible behavior are more meaningful.

## **26. Integrating tests into CI/CD**

In Phase 19, you learned GitHub Actions and CI/CD.

Now let's integrate your tests into that workflow.

Suppose you want the following pipeline:

```
Developer pushes code
        |
        v
Install dependencies
        |
        v
Run ESLint
        |
        v
Run TypeScript checks
        |
        v
Run Unit Tests
        |
        v
Run Production Build
        |
        v
Run E2E Tests
        |
        v
Deployment approval / deployment
```

### **26.1 Configure package.json**

```
{
  "scripts": {
    "lint": "eslint .",
    "typecheck": "tsc --noEmit",
    "test:run": "vitest run",
    "build": "next build",
    "test:e2e": "playwright test"
  }
}
```

### **26.2 Create GitHub Actions workflow**

Create:

```
.github/workflows/ci.yml
```

```
name: Next.js Testing Pipeline

on:
  push:
    branches: [main, development]

  pull_request:
    branches: [main]

jobs:
  unit-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: TypeScript checks
        run: npm run typecheck

      - name: Run unit tests
        run: npm run test:run

      - name: Build application
        run: npm run build
```

This workflow automatically checks your application whenever the configured Git events occur.

### **26.3 Add Playwright E2E tests**

You can add a separate job:

```
  e2e-tests:
    needs: unit-tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      - name: Build application
        run: npm run build

      - name: Run E2E tests
        run: npm run test:e2e
```

For this example, configure your Playwright `webServer` to start the production server:

```
webServer: {
  command: "npm run start",
  url: "http://localhost:3000",
  reuseExistingServer: !process.env.CI,
  timeout: 120 * 1000,
},
```

The build job must run before this server starts. The separate E2E job above builds the application itself.

If your app requires environment variables at build or runtime, configure safe CI test values or protected GitHub secrets.

## **27. Testing strategy for a real Next.js project**

You should not test every feature using the same method.

Consider a full-stack application with:

- Registration.
- Login.
- Product management.
- Shopping cart.
- Checkout.
- Admin dashboard.

A sensible testing strategy:

| Feature            | Unit testing         | Integration testing     | E2E testing           |
| ------------------ | -------------------- | ----------------------- | --------------------- |
| Price calculations | Yes                  | Optional                | Optional              |
| Form validation    | Yes                  | Yes                     | Yes                   |
| Registration       | Validation functions | Registration service    | Complete registration |
| Login              | Auth utilities       | Auth and session logic  | Complete login        |
| Product CRUD       | Utility logic        | API and database        | Admin CRUD workflow   |
| Shopping cart      | Cart calculations    | Cart state and services | Add/remove products   |
| Checkout           | Payment calculations | Payment service         | Checkout workflow     |
| Dashboard          | Small components     | Data and permissions    | Dashboard access      |

### **How much should you test?**

A practical strategy:

- Test important business logic with unit tests.
- Test boundaries between components and services with integration tests.
- Test critical user journeys with E2E tests.
- Test security-sensitive behavior at both server and browser levels.

You do not need hundreds of slow E2E tests for every small function.

## **28. Common testing mistakes**

| Mistake                        | Why it is problematic                              | Better approach                              |
| ------------------------------ | -------------------------------------------------- | -------------------------------------------- |
| Testing only the happy path    | Bugs in error handling remain hidden               | Test success, failure and edge cases         |
| Testing implementation details | Tests break after harmless refactoring             | Test user-visible behavior                   |
| Using production database      | Can corrupt real data                              | Use isolated test databases                  |
| Mocking everything             | Tests may verify mocks rather than actual logic    | Mock external dependencies selectively       |
| Ignoring async assertions      | Tests may finish too early                         | Use `await`                                  |
| Using too many E2E tests       | Test suite becomes slow                            | Use unit and integration tests appropriately |
| Depending on test order        | Tests become unreliable                            | Isolate test data                            |
| Hardcoded waits                | Creates flaky tests                                | Use Playwright auto-waiting and assertions   |
| Testing only client validation | Server may still accept invalid data               | Validate on the server                       |
| Ignoring accessibility         | Tests may not reflect accessible user interactions | Prefer role and label queries                |
| Chasing 100% coverage          | High coverage can still miss bugs                  | Prioritize meaningful assertions             |

### **Important: Avoid fixed waits**

Bad:

```
await page.waitForTimeout(5000);
```

This waits five seconds regardless of whether the page is ready.

Better:

```
await expect(
  page.getByText("Dashboard")
).toBeVisible();
```

Playwright automatically waits for the expected condition within its timeout.

## **29. Interview questions and answers**

### **Basic questions**

**Q1. What is software testing?**

Software testing is the process of verifying that an application behaves according to its requirements.

**Q2. What is unit testing?**

Unit testing verifies an individual function, component, or small unit of code in isolation.

**Q3. What is integration testing?**

Integration testing verifies that multiple connected parts of an application work together correctly.

**Q4. What is E2E testing?**

End-to-end testing verifies complete user workflows through an environment that resembles real application usage.

**Q5. What is Vitest?**

Vitest is a JavaScript and TypeScript testing framework used for unit and integration testing.

**Q6. What is Playwright?**

Playwright is a browser automation framework used for end-to-end testing across Chromium, Firefox, and WebKit.

### **Intermediate questions**

**Q7. What is the difference between&#x20;**`toBe()`**&#x20;and&#x20;**`toEqual()`**?**

`toBe()` checks strict equality. `toEqual()` compares object and array contents recursively.

**Q8. What is mocking?**

Mocking replaces a real dependency with a controlled fake implementation to isolate the behavior being tested.

**Q9. What is jsdom?**

jsdom is a JavaScript implementation of browser DOM APIs that allows DOM-based tests to run in Node.js.

**Q10. What is React Testing Library?**

It is a library for testing React components through DOM queries and user-oriented interactions.

**Q11. What is the difference between&#x20;**`getBy`**,&#x20;**`queryBy`**, and&#x20;**`findBy`**?**

- `getBy`: Finds an existing element or throws.
- `queryBy`: Returns the element or `null`.
- `findBy`: Returns a Promise and waits for an element to appear.

**Q12. What is code coverage?**

Code coverage measures how much of the source code is executed during testing.

**Q13. What is a snapshot test?**

A snapshot test saves a representation of output and compares later output against it to detect unexpected changes.

### **Advanced questions**

**Q14. Can Vitest directly test asynchronous Server Components?**

Vitest currently does not fully support async Server Components as ordinary unit tests. Next.js recommends E2E testing for those components.

**Q15. How do you test a Next.js Route Handler?**

You can import the exported HTTP method function, create a standard `Request`, call the function, and assert the response. For complete HTTP behavior, use integration or E2E tests.

**Q16. How do you test Server Actions?**

Test the exported action's business logic directly where possible, mock external dependencies, and use browser-level tests to verify actual form submissions and server interactions.

**Q17. How do you test database operations?**

Use mocked database dependencies for unit tests and a dedicated test database for integration tests.

**Q18. What is the difference between&#x20;**`userEvent`**&#x20;and&#x20;**`fireEvent`**?**

`userEvent` simulates more realistic user interactions, while `fireEvent` dispatches individual DOM events directly.

**Q19. How do you prevent E2E tests from modifying production data?**

Use dedicated test environments, test credentials, isolated databases, and controlled test data.

**Q20. What is a flaky test?**

A flaky test is one that sometimes passes and sometimes fails without a relevant code change, often because of timing, shared state, or external dependencies.

**Q21. Why shouldn't you rely only on E2E tests?**

They are generally slower and more expensive to maintain than unit tests. Unit and integration tests provide faster, more focused feedback.

**Q22. How do you integrate testing into CI/CD?**

Configure automated workflows to install dependencies, run linting and type checks, execute unit and integration tests, build the application, and run E2E tests before deployment.

## **30. Practical assignments**

Use your own Next.js application to complete these exercises.

### **Assignment 1 – Vitest fundamentals**

- Install and configure Vitest.
- Create a utility function for calculating cart totals.
- Write tests for normal values.
- Write tests for zero quantity.
- Write tests for decimal values.
- Test invalid inputs.
- Run the complete test suite.

### **Assignment 2 – React component testing**

Create a reusable registration form.

- Test whether all fields render.
- Test whether inputs accept values.
- Test required field validation.
- Test invalid email validation.
- Test successful submission.
- Test error message visibility.

### **Assignment 3 – API testing**

Create a product API:

```
GET    /api/products
GET    /api/products/[id]
POST   /api/products
PATCH  /api/products/[id]
DELETE /api/products/[id]
```

Write tests for:

- Successful GET request.
- Product not found.
- Successful POST request.
- Invalid POST data.
- Successful PATCH request.
- Successful DELETE request.
- Unauthorized access.
- Database failure.

### **Assignment 4 – Playwright E2E testing**

Test these workflows:

- Homepage loads.
- User navigates to the login page.
- User submits invalid credentials.
- User logs in successfully.
- Dashboard becomes visible.
- User logs out.
- Dashboard is no longer accessible without authentication.

### **Assignment 5 – CI/CD testing**

- Add unit tests to GitHub Actions.
- Add ESLint checks.
- Add TypeScript checks.
- Add production build validation.
- Add Playwright E2E tests.
- Verify that a failing test makes the workflow fail.

## **31. Quick revision notes**

| Topic                 | Remember                      |
| --------------------- | ----------------------------- |
| Unit testing          | Tests individual units        |
| Integration testing   | Tests connected modules       |
| E2E testing           | Tests complete user workflows |
| Vitest                | Unit/integration test runner  |
| Playwright            | Browser automation and E2E    |
| React Testing Library | React component testing       |
| jsdom                 | Simulated DOM environment     |
| `describe()`          | Groups tests                  |
| `it()` / `test()`     | Defines a test case           |
| `expect()`            | Starts an assertion           |
| `vi.fn()`             | Creates a mock function       |
| `vi.mock()`           | Mocks a module                |
| `render()`            | Renders a React component     |
| `screen`              | Queries rendered DOM          |
| `userEvent`           | Simulates user interactions   |
| `page.goto()`         | Navigates a browser           |
| `page.getByRole()`    | Finds accessible elements     |
| `toBe()`              | Strict equality               |
| `toEqual()`           | Deep equality                 |
| Code coverage         | Measures executed code        |
| Snapshot testing      | Compares saved output         |
| Flaky test            | Inconsistent test result      |

### **Important commands**

```
# Vitest
npm run test
npm run test:run
npm run test:coverage

# Playwright
npm init playwright
npx playwright install

npm run test:e2e
npx playwright test --headed
npx playwright test --debug
npx playwright show-report

# TypeScript
npx tsc --noEmit

# ESLint
npm run lint
```

## **32. Phase 20 completion checklist**

**Phase 20 progress**

# **0/20**

Understand unit, integration and E2E testing

Understand test suites, assertions and fixtures

Install and configure Vitest

Write unit tests for utility functions

Test asynchronous functions

Understand mocks and mocked modules

Test React components using Testing Library

Test forms and validation

Test API Route Handlers

Understand database testing strategies

Understand Server Component testing limitations

Test Server Actions

Configure Playwright

Write browser automation tests

Test authentication workflows

Mock API requests

Test file uploads

Generate code coverage reports

Integrate tests into GitHub Actions

Understand flaky tests and debugging

Reset progress

## **33. What's next?**

Next phase

# **Phase 21 – Advanced Next.js Concepts and Architecture**

In this phase, we will move beyond individual features and learn how to design larger, maintainable Next.js applications.

Topics will include:

1. Advanced App Router architecture.
2. Parallel Routes.
3. Intercepting Routes.
4. Advanced routing patterns.
5. Route groups and layout architecture.
6. Advanced Server and Client Component composition.
7. Dependency management and application layering.
8. Scalable folder structures.
9. Design patterns for large Next.js applications.
10. Real-world architecture and best practices.

When you're ready, say **OK, Phase 21**.

### **Official documentation**

- [Next.js – Testing overview](https://nextjs.org/docs/app/guides/testing?utm_source=chatgpt.com)
- [Next.js – Vitest setup](https://nextjs.org/docs/app/guides/testing/vitest?utm_source=chatgpt.com)
- [Next.js – Playwright setup](https://nextjs.org/docs/app/guides/testing/playwright?utm_source=chatgpt.com)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/?utm_source=chatgpt.com)
- [Vitest code coverage](https://vitest.dev/guide/coverage?utm_source=chatgpt.com)