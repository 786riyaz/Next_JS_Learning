# **Phase 14 – Authentication, Session Management and Authorization**



Phase 14

Intermediate to Advanced

Next.js 16.x



## **1. Learning objectives**

Authentication and authorization are essential parts of almost every real-world web application.

In this phase, you will learn how to:

1. Understand authentication and authorization.
2. Build a registration and login system.
3. Hash passwords securely using bcrypt and Argon2.
4. Understand password hashing vs encryption.
5. Understand sessions and JWT.
6. Implement database-backed and stateless sessions.
7. Work with cookies and secure cookie configurations.
8. Create login and logout functionality.
9. Implement session expiration.
10. Protect pages, Server Actions and Route Handlers.
11. Implement Role-Based Access Control (RBAC).
12. Use a Data Access Layer (DAL).
13. Understand authentication libraries such as Auth.js.
14. Build a complete authentication system using Next.js and MongoDB.

## **2. What is Authentication?**

**Authentication** is the process of verifying the identity of a user.

In simple terms, authentication answers:

> Who are you?

For example, when you log in to an application using your email and password, the application verifies whether those credentials belong to a registered user.

Other authentication methods include:

- Email and password.
- Phone number and OTP.
- Google login.
- GitHub login.
- Microsoft login.
- Passkeys.
- Multi-factor authentication (MFA).

### **Example**

Imagine a user has registered with:

```
Email: user@example.com
Password: MyPassword123
```

When the user logs in, the application checks:

1. Does the email exist?
2. Does the supplied password match the stored password hash?
3. Is the account allowed to log in?
4. If successful, create an authenticated session.

The user can then access protected parts of the application.

## **3. What is Authorization?**

**Authorization** determines what an authenticated user is allowed to do.

It answers:

> What are you allowed to access or perform?

For example, imagine an e-commerce application with three types of users:

| User     | Permissions                                 |
| -------- | ------------------------------------------- |
| Customer | View products and place orders              |
| Manager  | Manage products and orders                  |
| Admin    | Manage users, products, orders and settings |

All three users may be authenticated, but they have different permissions.

### **Authentication vs Authorization**

| Authentication                         | Authorization                               |
| -------------------------------------- | ------------------------------------------- |
| Verifies identity                      | Checks permissions                          |
| Answers who you are                    | Answers what you can do                     |
| Happens before authorization           | Usually follows authentication              |
| Example: Login                         | Example: Admin-only access                  |
| Uses credentials or identity providers | Uses roles, permissions and ownership rules |

### **Real-world example**

A user successfully logs in.

They request:

```
/admin/users
```

The application performs two checks:

1. Authentication: Is the user logged in?
2. Authorization: Does the user have permission to manage users?

If the user is authenticated but does not have the admin role, access must be denied.

**Important:** Hiding the admin page link in the frontend is not authorization. The backend must enforce permissions.

## **4. Understanding the authentication workflow**

Let's understand a typical email-and-password authentication system.

**User Registration**

Name, email and password

**Password Hashing**

bcrypt or Argon2id

**Database**

Store user details and password hash

**User Login**

Email and password

**Verify Password**

Compare submitted password with stored hash

**Create Session**

Database session or signed token

**Set Secure Cookie**

Browser stores session identifier or token

**Access Protected Resources**

This is the basic architecture we will implement during this phase.

## **5. Understanding password hashing**

Before implementing registration and login, you must understand how passwords should be stored.

### **Why should we never store plain-text passwords?**

Suppose your users collection contains:

```
{
  "name": "John",
  "email": "john@example.com",
  "password": "MyPassword123"
}
```

This is dangerous.

If someone gains unauthorized access to your database, they can immediately read users' passwords.

A password should never be stored in plain text.

Instead, we use **password hashing**.

### **What is hashing?**

Hashing is a one-way transformation that converts input data into a fixed-format output called a hash.

For example:

**Original password**

`MyPassword123`

Password hashing algorithm + salt

**Stored password hash**

A long, encoded hash value

When a user logs in, the application does not decrypt the stored hash.

Instead, the password-hashing library verifies whether the submitted password matches it.

### **Hashing vs encryption**

| Hashing                   | Encryption                                      |
| ------------------------- | ----------------------------------------------- |
| Designed to be one-way    | Designed to be reversible with a key            |
| No decryption operation   | Can be decrypted with the appropriate key       |
| Used for password storage | Used for protecting data that must be recovered |
| Example: Argon2id         | Example: AES-GCM                                |

**Important:** Passwords should be hashed, not encrypted or stored in plain text.

### **What is a salt?**

A salt is a random value incorporated into password hashing.

It helps ensure that identical passwords do not produce identical stored hashes when using properly configured salted password-hashing algorithms.

Modern password-hashing libraries generally manage salts automatically.

## **6. bcrypt and Argon2**

Two commonly used password-hashing choices are:

- bcrypt
- Argon2

### **bcrypt**

bcrypt is a widely used password-hashing algorithm.

Install:

```
npm install bcrypt
npm install -D @types/bcrypt
```

Hash a password:

```
import bcrypt from "bcrypt";

const password = "MyPassword123";

const saltRounds = 12;

const hashedPassword = await bcrypt.hash(
  password,
  saltRounds
);

console.log(hashedPassword);
```

Verify a password:

```
const isValid = await bcrypt.compare(
  "MyPassword123",
  hashedPassword
);

console.log(isValid);
```

If the password matches, `isValid` will be `true`.

If it does not match, it will be `false`.

### **Understanding salt rounds**

```
const saltRounds = 12;
```

Salt rounds control bcrypt's computational work factor.

Higher values generally increase the time required to hash and verify passwords.

The appropriate value should be benchmarked on your production environment.

### **Argon2id**

Argon2 is a modern password-hashing algorithm. Argon2id combines resistance to different types of attacks and is generally a strong default choice for new password-storage systems.

Install:

```
npm install argon2
```

Hash a password:

```
import argon2 from "argon2";

const password = "MyPassword123";

const hashedPassword = await argon2.hash(password, {
  type: argon2.argon2id,
});
```

Verify a password:

```
const isValid = await argon2.verify(
  hashedPassword,
  "MyPassword123"
);
```

### **bcrypt vs Argon2id**

| Feature                       | bcrypt           | Argon2id                           |
| ----------------------------- | ---------------- | ---------------------------------- |
| Purpose                       | Password hashing | Password hashing                   |
| Salt support                  | Yes              | Yes                                |
| Work factor                   | Cost factor      | Memory, iterations and parallelism |
| Memory hardness               | Not memory-hard  | Memory-hard                        |
| Popularity                    | Very widely used | Modern recommended option          |
| Suitable for password storage | Yes              | Yes                                |

**Recommendation:** For a new application, consider Argon2id. bcrypt is also a valid choice when correctly configured.

Never build your own password-hashing algorithm.

The password-hashing examples above are educational. For production, tune the algorithm's cost parameters to your hosting environment. OWASP currently recommends Argon2id with at least 19 MiB memory, 2 iterations and parallelism 1; bcrypt is mainly a fallback or legacy choice, with a work factor of at least 10 and a 72-byte password limit.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series



## **7. Understanding session management**

After a user logs in successfully, how does the application remember that the user is authenticated when they visit another page?

This is the purpose of **session management**.

A session maintains a user's authenticated state across multiple HTTP requests.

Without session management, the application would need to ask for the user's password on every request.

### **Example without session management**

```
Request 1: GET /dashboard
Application: Who are you?

Request 2: GET /profile
Application: Who are you?

Request 3: GET /orders
Application: Who are you?
```

This would be impractical.

### **Example with session management**

```
1. User logs in.
2. Server verifies credentials.
3. Server creates a session.
4. Browser stores a session cookie.
5. Browser sends the cookie with subsequent requests.
6. Server verifies the session.
7. User can access permitted resources.
```

### **What is a session?**

A session is a mechanism for maintaining authenticated state.

It commonly contains information such as:

```
{
  userId: "123",
  role: "admin",
  expiresAt: "2026-12-01T10:00:00.000Z"
}
```

This is an illustrative session payload. You should not necessarily expose all these fields to the browser.

### **Two main session strategies**

1. Stateless sessions.
2. Database-backed sessions.

Let's understand both.

## **8. Stateless sessions**

A stateless session stores the session data or a signed/encrypted session token in the browser, commonly inside a cookie.

The server verifies the token on incoming requests without needing to look up a session record in the database every time.

A common implementation uses JWT or an encrypted session token.

### **How stateless sessions work**

**User logs in**

**Server verifies credentials**

**Server creates signed/encrypted token**

**Token stored in secure cookie**

**Browser sends cookie with later requests**

**Server verifies token and expiry**

### **Advantages**

- No session database lookup is necessarily required.
- Easy to scale across multiple application instances.
- Useful for distributed systems.
- Can be implemented using signed or encrypted tokens.

### **Disadvantages**

- Revoking a token before it expires can be difficult.
- Changes to user permissions may not immediately appear in existing tokens.
- Token theft can allow unauthorized access until the token expires or is otherwise invalidated.
- Token size and cookie size must be considered.

**Important:** Stateless does not mean the application has no server-side state anywhere. It means the authentication session itself can be verified without retrieving a session record from a central database on each request.

## **9. Database-backed sessions**

In a database-backed session, the server stores the session record in a database.

The browser generally receives a session identifier, usually in a secure cookie.

### **How database sessions work**

**User logs in**

**Server verifies credentials**

**Create session record in database**

**Send session identifier in secure cookie**

**Browser sends session identifier**

**Server looks up and validates session**

Example session record:

```
{
  "_id": "session-identifier",
  "userId": "user-123",
  "expiresAt": "2026-12-01T10:00:00.000Z",
  "createdAt": "2026-11-25T10:00:00.000Z"
}
```

### **Advantages**

- Sessions can be revoked immediately.
- You can log users out from all devices.
- You can track active sessions.
- You can manage session expiration centrally.
- User permissions can be checked against current database values.

### **Disadvantages**

- Requires database access for session validation.
- Requires session cleanup and expiration management.
- Adds database and infrastructure overhead.

### **Stateless vs database-backed sessions**

| Feature                          | Stateless                          | Database-backed                 |
| -------------------------------- | ---------------------------------- | ------------------------------- |
| Session storage                  | Token/cookie                       | Database                        |
| Database lookup per verification | Not necessarily                    | Generally yes                   |
| Immediate revocation             | More difficult                     | Easier                          |
| Multiple-device management       | Requires additional design         | Straightforward                 |
| Server-side session tracking     | Limited without extra storage      | Yes                             |
| Scaling                          | Convenient with token verification | Requires shared session storage |
| Common implementation            | Signed/encrypted token             | Random session ID and DB record |

**Recommendation:** Both approaches can be secure. Choose based on revocation requirements, infrastructure and application complexity. For applications requiring active-session management or immediate revocation, database-backed sessions are often a good choice.

Next.js discusses both strategies in its authentication guide.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js



## **10. What is JWT?**

JWT stands for **JSON Web Token**.

It is a compact token format commonly used to transmit claims between parties.

A JWT generally consists of three parts:

```
HEADER.PAYLOAD.SIGNATURE
```

Example structure:

```
xxxxx.yyyyy.zzzzz
```

### **A. Header**

The header describes the token type and signing algorithm.

Illustrative decoded header:

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### **B. Payload**

The payload contains claims.

Example:

```
{
  "sub": "user-123",
  "role": "admin",
  "iat": 1795600000,
  "exp": 1795603600
}
```

Common claims:

| Claim | Meaning                          |
| ----- | -------------------------------- |
| `sub` | Subject, usually user identifier |
| `iat` | Issued-at timestamp              |
| `exp` | Expiration timestamp             |
| `iss` | Issuer                           |
| `aud` | Intended audience                |

### **C. Signature**

The signature helps verify that the token was created by a trusted issuer and has not been modified.

### **Important: Is JWT encrypted?**

A standard signed JWT (JWS) is usually **not encrypted**.

Its header and payload are Base64URL-encoded, not hidden.

Anyone who obtains the token can generally decode and read its payload.

Therefore:

- Do not put passwords in JWT payloads.
- Do not put secret API keys in JWT payloads.
- Do not assume that a role claim is confidential.
- Use a signed token to verify integrity, not to hide information.

If confidentiality is required, use an appropriately configured encrypted token format such as JWE.

### **JWT vs session ID**

| JWT                                     | Random session ID                        |
| --------------------------------------- | ---------------------------------------- |
| Contains claims                         | Usually contains no meaningful user data |
| Can be verified using cryptography      | Usually requires database lookup         |
| Often used for stateless authentication | Commonly used for database sessions      |
| Revocation requires additional design   | Can be revoked by deleting its record    |
| Payload may be readable                 | Session details remain in the database   |

**Important:** JWT is a token format, not an authentication system by itself. You still need credential verification, secure token storage, expiration and authorization checks.

## **11. Implementing stateless sessions with&#x20;**`jose`

For our Next.js application, let's use the `jose` library to create and verify encrypted sessions.

Install:

```
npm install jose
```

### **Step 1: Generate a secret**

Run this command in your terminal:

```
openssl rand -base64 32
```

Add the generated secret to `.env.local`:

```
SESSION_SECRET=your_generated_secret_here
```

Do not commit `.env.local` to GitHub.

### **Step 2: Create a session module**

`src/lib/session.ts`

```
import "server-only";

import { SignJWT, jwtVerify } from "jose";
import { cookies } from "next/headers";

const secret = process.env.SESSION_SECRET;

if (!secret) {
  throw new Error("SESSION_SECRET is not configured");
}

const key = new TextEncoder().encode(secret);

export type SessionPayload = {
  userId: string;
  role: "user" | "admin";
};

export async function encrypt(payload: SessionPayload) {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: "HS256" })
    .setIssuedAt()
    .setExpirationTime("7d")
    .sign(key);
}

export async function decrypt(token?: string) {
  if (!token) {
    return null;
  }

  try {
    const { payload } = await jwtVerify(token, key, {
      algorithms: ["HS256"],
    });

    if (
      typeof payload.userId !== "string" ||
      (payload.role !== "user" && payload.role !== "admin")
    ) {
      return null;
    }

    return payload as typeof payload & SessionPayload;
  } catch {
    return null;
  }
}
```

This creates a signed JWT, not an encrypted JWT. Its payload is readable by anyone who obtains the token, so only include non-secret claims.

### **Step 3: Create a session cookie**

Add these functions to `src/lib/session.ts`:

```
export async function createSession(
  userId: string,
  role: "user" | "admin"
) {
  const token = await encrypt({
    userId,
    role,
  });

  const cookieStore = await cookies();

  cookieStore.set("session", token, {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "lax",
    path: "/",
    maxAge: 60 * 60 * 24 * 7,
  });
}
```

### **Step 4: Verify the session**

```
export async function getSession() {
  const cookieStore = await cookies();

  const token = cookieStore.get("session")?.value;

  return decrypt(token);
}
```

### **Step 5: Delete the session**

```
export async function deleteSession() {
  const cookieStore = await cookies();

  cookieStore.delete("session");
}
```

Now you have basic functions for:

- Creating a signed token.
- Storing it in a cookie.
- Reading and verifying it.
- Deleting the cookie.

This is a learning implementation of stateless sessions. A production system should also consider token revocation, key rotation, session refresh, role changes and account suspension.

## **12. Understanding secure cookies**

Cookies are small pieces of data stored by the browser and sent with matching HTTP requests.

They are commonly used to store session identifiers or authentication tokens.

Example:

```
Set-Cookie: session=abc123
```

### **Important cookie options**

| Option     | Purpose                                           |
| ---------- | ------------------------------------------------- |
| `httpOnly` | Prevents JavaScript from reading the cookie       |
| `secure`   | Sends the cookie only over HTTPS                  |
| `sameSite` | Controls cross-site cookie sending                |
| `path`     | Defines the URL path for which the cookie is sent |
| `maxAge`   | Defines cookie lifetime in seconds                |
| `expires`  | Defines the cookie expiration date                |
| `domain`   | Defines the cookie's domain scope                 |

### **Understanding&#x20;**`httpOnly`

```
httpOnly: true
```

This prevents browser JavaScript from reading the cookie through `document.cookie`.

It helps reduce the impact of certain cross-site scripting attacks.

### **Understanding&#x20;**`secure`

```
secure: true
```

This restricts the cookie to secure HTTPS connections.

During local development, you might use:

```
secure: process.env.NODE_ENV === "production"
```

### **Understanding&#x20;**`sameSite`

There are three commonly used values:

| Value    | Behavior                                                  |
| -------- | --------------------------------------------------------- |
| `strict` | Cookie is restricted on cross-site navigations            |
| `lax`    | Allows certain top-level cross-site navigations           |
| `none`   | Allows cross-site cookie sending; requires `secure: true` |

For a typical first-party application session, `sameSite: "lax"` is often a reasonable starting point.

However, cookie settings must match your application's domain, deployment and authentication architecture.

### **Important security note**

`httpOnly` does not prevent every CSRF attack. Cookie-based authentication may require additional CSRF protections, especially for cross-site request scenarios.

Also, `sameSite` is not a substitute for correct authorization.

Official documentation: [MDN – HTTP Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies?utm_source=chatgpt.com).

## **13. Building user registration**

Now let's implement the registration workflow.

We will use:

- Next.js 16
- TypeScript
- MongoDB
- Argon2
- Zod
- Server Actions
- Secure session cookies

### **Suggested folder structure**

```
src/
├── app/
│   ├── register/
│   │   ├── page.tsx
│   │   └── register-form.tsx
│   │
│   ├── login/
│   │   ├── page.tsx
│   │   └── login-form.tsx
│   │
│   ├── dashboard/
│   │   └── page.tsx
│   │
│   └── actions/
│       └── auth.ts
│
├── lib/
│   ├── mongodb.ts
│   ├── session.ts
│   ├── dal.ts
│   └── validations/
│       └── auth.ts
│
└── types/
    └── user.ts
```

### **Step 1: Install dependencies**

```
npm install mongodb argon2 zod jose
```

### **Step 2: Create the user schema**

`src/lib/validations/auth.ts`

```
import { z } from "zod";

export const registerSchema = z.object({
  name: z.string().trim().min(2).max(100),

  email: z
    .string()
    .trim()
    .email()
    .max(254)
    .transform((value) => value.toLowerCase()),

  password: z.string().min(12).max(128),
});
```

The password length rule is an example policy. A production password policy should be based on your threat model, support long passphrases and avoid arbitrary complexity rules that harm usability.

### **Step 3: Create the registration Server Action**

`src/app/actions/auth.ts`

```
"use server";

import argon2 from "argon2";
import { redirect } from "next/navigation";
import { z } from "zod";

import clientPromise from "@/lib/mongodb";
import { registerSchema } from "@/lib/validations/auth";
import { createSession } from "@/lib/session";

export type AuthState = {
  success: boolean;
  message: string;
  errors?: Record<string, string[]>;
};

export async function register(
  previousState: AuthState,
  formData: FormData
): Promise<AuthState> {
  const result = registerSchema.safeParse({
    name: formData.get("name"),
    email: formData.get("email"),
    password: formData.get("password"),
  });

  if (!result.success) {
    return {
      success: false,
      message: "Please correct the form errors",
      errors: z.flattenError(result.error).fieldErrors,
    };
  }

  const { name, email, password } = result.data;

  try {
    const client = await clientPromise;
    const db = client.db("mydb");

    const existingUser = await db.collection("users").findOne({
      email,
    });

    if (existingUser) {
      return {
        success: false,
        message: "An account with this email already exists",
      };
    }

    const hashedPassword = await argon2.hash(password, {
      type: argon2.argon2id,
    });

    const user = {
      name,
      email,
      passwordHash: hashedPassword,
      role: "user" as const,
      createdAt: new Date(),
    };

    const insertResult = await db
      .collection("users")
      .insertOne(user);

    await createSession(
      insertResult.insertedId.toString(),
      "user"
    );
  } catch (error) {
    console.error("Registration error:", error);

    return {
      success: false,
      message: "Unable to create your account",
    };
  }

  redirect("/dashboard");
}
```

### **Important production improvements**

This example demonstrates the registration flow, but there are additional requirements for a production implementation:

1. Enforce a unique database index on the normalized email field. Checking for an existing user before insertion alone does not prevent simultaneous duplicate registrations.
2. Handle duplicate-key errors from MongoDB.
3. Add rate limiting to registration.
4. Consider email verification.
5. Never allow the registration form to choose its own role.
6. Consider abuse prevention and account recovery.

For example, create a unique email index:

```
await db.collection("users").createIndex(
  { email: 1 },
  { unique: true }
);
```

The unique index should be created during database setup or migration, not on every registration request.

### **Step 4: Create the registration form**

`src/app/register/register-form.tsx`

```
"use client";

import { useActionState } from "react";

import { register } from "@/app/actions/auth";

const initialState = {
  success: false,
  message: "",
};

export default function RegisterForm() {
  const [state, formAction, pending] = useActionState(
    register,
    initialState
  );

  return (
    <form action={formAction}>
      <div>
        <label htmlFor="name">Full Name</label>

        <input
          id="name"
          name="name"
          type="text"
          required
        />
      </div>

      <div>
        <label htmlFor="email">Email</label>

        <input
          id="email"
          name="email"
          type="email"
          required
        />
      </div>

      <div>
        <label htmlFor="password">Password</label>

        <input
          id="password"
          name="password"
          type="password"
          autoComplete="new-password"
          required
        />
      </div>

      {state.message && <p>{state.message}</p>}

      {state.errors?.name?.map((error) => (
        <p key={error}>{error}</p>
      ))}

      {state.errors?.email?.map((error) => (
        <p key={error}>{error}</p>
      ))}

      {state.errors?.password?.map((error) => (
        <p key={error}>{error}</p>
      ))}

      <button type="submit" disabled={pending}>
        {pending ? "Creating Account..." : "Register"}
      </button>
    </form>
  );
}
```

### **Step 5: Render the registration page**

`src/app/register/page.tsx`

```
import RegisterForm from "./register-form";

export default function RegisterPage() {
  return (
    <main>
      <h1>Create Account</h1>

      <RegisterForm />
    </main>
  );
}
```

Now you have a basic registration workflow:

1. User fills out the form.
2. Server Action validates the data.
3. MongoDB checks for an existing email.
4. Argon2 hashes the password.
5. MongoDB inserts the user.
6. A session cookie is created.
7. The user is redirected to the dashboard.

## **14. Building user login**

Registration creates a user account. Login verifies the user's credentials and creates a session.

### **Login workflow**

**User enters email and password**

**Find user by normalized email**

**Verify password using Argon2**

**Valid**

Create session

**Invalid**

Return error

### **Step 1: Create the login validation schema**

Add to `src/lib/validations/auth.ts`:

```
export const loginSchema = z.object({
  email: z
    .string()
    .trim()
    .email()
    .transform((value) => value.toLowerCase()),

  password: z.string().min(1),
});
```

### **Step 2: Create the login Server Action**

Add to `src/app/actions/auth.ts`:

```
import argon2 from "argon2";
import { loginSchema } from "@/lib/validations/auth";

export async function login(
  previousState: AuthState,
  formData: FormData
): Promise<AuthState> {
  const result = loginSchema.safeParse({
    email: formData.get("email"),
    password: formData.get("password"),
  });

  if (!result.success) {
    return {
      success: false,
      message: "Invalid email or password",
    };
  }

  const { email, password } = result.data;

  try {
    const client = await clientPromise;
    const db = client.db("mydb");

    const user = await db.collection("users").findOne({
      email,
    });

    if (
      !user ||
      typeof user.passwordHash !== "string" ||
      !(await argon2.verify(user.passwordHash, password))
    ) {
      return {
        success: false,
        message: "Invalid email or password",
      };
    }

    if (user.disabled === true) {
      return {
        success: false,
        message: "This account is unavailable",
      };
    }

    await createSession(
      user._id.toString(),
      user.role === "admin" ? "admin" : "user"
    );
  } catch (error) {
    console.error("Login error:", error);

    return {
      success: false,
      message: "Unable to log in right now",
    };
  }

  redirect("/dashboard");
}
```

This assumes `clientPromise` and `createSession` are imported from the modules created earlier in this phase.

**Security note:** Use a generic login error so the response does not reveal whether a particular email address is registered. For production, also add rate limiting, monitoring and protection against automated password guessing.

### **Step 3: Create the login form**

`src/app/login/login-form.tsx`

```
"use client";

import { useActionState } from "react";

import { login } from "@/app/actions/auth";

const initialState = {
  success: false,
  message: "",
};

export default function LoginForm() {
  const [state, formAction, pending] = useActionState(
    login,
    initialState
  );

  return (
    <form action={formAction}>
      <div>
        <label htmlFor="email">Email</label>

        <input
          id="email"
          name="email"
          type="email"
          autoComplete="email"
          required
        />
      </div>

      <div>
        <label htmlFor="password">Password</label>

        <input
          id="password"
          name="password"
          type="password"
          autoComplete="current-password"
          required
        />
      </div>

      {state.message && <p>{state.message}</p>}

      <button type="submit" disabled={pending}>
        {pending ? "Logging in..." : "Login"}
      </button>
    </form>
  );
}
```

### **Step 4: Create the login page**

`src/app/login/page.tsx`

```
import LoginForm from "./login-form";

export default function LoginPage() {
  return (
    <main>
      <h1>Login</h1>

      <LoginForm />
    </main>
  );
}
```

Now your login workflow is complete.

## **15. Implementing logout**

Logout means ending the authenticated session.

For our current stateless-session implementation, logout deletes the session cookie.

Add to `src/app/actions/auth.ts`:

```
import { deleteSession } from "@/lib/session";

export async function logout() {
  await deleteSession();

  redirect("/login");
}
```

Create a logout form:

```
import { logout } from "@/app/actions/auth";

export default function LogoutButton() {
  return (
    <form action={logout}>
      <button type="submit">Logout</button>
    </form>
  );
}
```

### **What happens during logout?**

1. User clicks Logout.
2. The Server Action runs.
3. The session cookie is deleted.
4. The user is redirected to `/login`.
5. Subsequent requests no longer carry that session cookie.

### **Important limitation**

Deleting a stateless JWT cookie only removes it from the current browser.

If a copy of the token has already been stolen, it may remain valid until expiry unless you implement revocation, a token denylist, a version check or another server-side invalidation mechanism.

This is one reason database-backed sessions can be useful.

## **16. Building a secure Data Access Layer**

We introduced the DAL in Phase 13. Now let's use it as the central place for secure session checks.

Create:

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

### **Create an admin verification function**

```
export async function verifyAdmin() {
  const session = await verifySession();

  if (session.role !== "admin") {
    redirect("/unauthorized");
  }

  return session;
}
```

### **Get current user details**

```
import clientPromise from "@/lib/mongodb";

export const getCurrentUser = cache(async () => {
  const session = await verifySession();

  const client = await clientPromise;

  const db = client.db("mydb");

  const user = await db.collection("users").findOne(
    {
      _id: new ObjectId(session.userId),
    },
    {
      projection: {
        passwordHash: 0,
      },
    }
  );

  if (!user) {
    redirect("/login");
  }

  return {
    id: user._id.toString(),
    name: user.name,
    email: user.email,
    role: user.role,
  };
});
```

Add this import to the file:

```
import { ObjectId } from "mongodb";
```

The `cache()` wrapper from React helps deduplicate repeated calls during the same server render.

### **Use it in a protected page**

`src/app/dashboard/page.tsx`

```
import { getCurrentUser } from "@/lib/dal";
import LogoutButton from "./logout-button";

export default async function DashboardPage() {
  const user = await getCurrentUser();

  return (
    <main>
      <h1>Dashboard</h1>

      <p>Welcome, {user.name}</p>

      <p>Email: {user.email}</p>

      <p>Role: {user.role}</p>

      <LogoutButton />
    </main>
  );
}
```

### **Why exclude&#x20;**`passwordHash`**?**

A user-facing component should receive only the information it needs.

This follows the principle of **Data Transfer Objects (DTOs)**.

For example:

```
{
  id: "123",
  name: "John",
  email: "john@example.com",
  role: "user"
}
```

Do not return the full database user document when it contains password hashes or other private information.

## **17. Implementing role-based access control (RBAC)**

RBAC stands for **Role-Based Access Control**.

It is an authorization model in which permissions are assigned to roles, and users are assigned those roles.

Example:

```
type Role = "user" | "manager" | "admin";
```

### **Example permission table**

| Permission      | User | Manager | Admin |
| --------------- | ---- | ------- | ----- |
| View products   | Yes  | Yes     | Yes   |
| Create products | No   | Yes     | Yes   |
| Update products | No   | Yes     | Yes   |
| Delete products | No   | No      | Yes   |
| Manage users    | No   | No      | Yes   |

### **Create a permission helper**

`src/lib/permissions.ts`

```
export const permissions = {
  user: [
    "products:read",
  ],

  manager: [
    "products:read",
    "products:create",
    "products:update",
  ],

  admin: [
    "products:read",
    "products:create",
    "products:update",
    "products:delete",
    "users:manage",
  ],
} as const;

export type Role = keyof typeof permissions;

export type Permission =
  (typeof permissions)[Role][number];

export function hasPermission(
  role: Role,
  permission: Permission
) {
  return permissions[role].includes(permission as never);
}
```

Example:

```
hasPermission("admin", "products:delete");
// true

hasPermission("user", "products:delete");
// false
```

### **Use permissions in a Server Action**

```
"use server";

import { verifySession } from "@/lib/dal";
import { hasPermission } from "@/lib/permissions";

export async function deleteProduct(productId: string) {
  const session = await verifySession();

  if (!hasPermission(session.role, "products:delete")) {
    return {
      success: false,
      message: "Permission denied",
    };
  }

  // Validate ID and delete from database.

  return {
    success: true,
    message: "Product deleted",
  };
}
```

This example assumes that the role returned by `verifySession()` is typed consistently with your permission model.

For production, be careful about stale roles in a stateless token. If administrators can have their roles changed or revoked, verify current permissions against trusted server-side data for sensitive operations.

## **18. Database-backed sessions with MongoDB**

Now let's understand how you would implement database-backed sessions.

### **Session collection**

You can create a MongoDB collection named:

```
sessions
```

Example document:

```
{
  "_id": "random-session-id",
  "userId": "65a123456789abcdef123456",
  "expiresAt": "2026-12-02T10:00:00.000Z",
  "createdAt": "2026-11-25T10:00:00.000Z"
}
```

### **How it works**

1. Generate a cryptographically random session ID.
2. Store the session ID, user ID and expiration in MongoDB.
3. Store the session ID in an HttpOnly cookie.
4. On later requests, read the cookie.
5. Find the matching session in MongoDB.
6. Check its expiration.
7. Load the user's current account status and permissions.
8. Reject invalid or expired sessions.

### **Generate a random session ID**

Use Node.js's cryptographic module:

```
import { randomBytes, createHash } from "node:crypto";

const sessionId = randomBytes(32).toString("hex");
```

The session ID is unpredictable and has 256 bits of random input.

### **Hash the session ID before database storage**

For additional protection, store a hash of the session identifier rather than the raw identifier.

```
const sessionHash = createHash("sha256")
  .update(sessionId)
  .digest("hex");
```

If the session database is leaked, an attacker does not immediately obtain usable raw session IDs from those records.

### **Create a session**

Illustrative implementation:

```
import "server-only";

import { randomBytes, createHash } from "node:crypto";
import { cookies } from "next/headers";
import clientPromise from "@/lib/mongodb";

export async function createDatabaseSession(userId: string) {
  const sessionId = randomBytes(32).toString("hex");

  const sessionHash = createHash("sha256")
    .update(sessionId)
    .digest("hex");

  const expiresAt = new Date(
    Date.now() + 7 * 24 * 60 * 60 * 1000
  );

  const client = await clientPromise;

  const db = client.db("mydb");

  await db.collection("sessions").insertOne({
    sessionHash,
    userId,
    expiresAt,
    createdAt: new Date(),
  });

  const cookieStore = await cookies();

  cookieStore.set("session", sessionId, {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "lax",
    path: "/",
    expires: expiresAt,
  });
}
```

### **Verify a database session**

```
import { createHash } from "node:crypto";
import { cookies } from "next/headers";
import clientPromise from "@/lib/mongodb";

export async function verifyDatabaseSession() {
  const cookieStore = await cookies();

  const sessionId = cookieStore.get("session")?.value;

  if (!sessionId) {
    return null;
  }

  const sessionHash = createHash("sha256")
    .update(sessionId)
    .digest("hex");

  const client = await clientPromise;

  const db = client.db("mydb");

  const session = await db.collection("sessions").findOne({
    sessionHash,
    expiresAt: { $gt: new Date() },
  });

  if (!session) {
    return null;
  }

  return {
    userId: session.userId,
    expiresAt: session.expiresAt,
  };
}
```

### **Logout from all devices**

With database-backed sessions, you can delete all sessions belonging to a user:

```
await db.collection("sessions").deleteMany({
  userId,
});
```

This makes all those database-backed sessions invalid.

### **Automatic expiration with MongoDB TTL**

You can create a TTL index:

```
await db.collection("sessions").createIndex(
  { expiresAt: 1 },
  { expireAfterSeconds: 0 }
);
```

MongoDB can then automatically remove expired session documents.

**Important:** TTL cleanup is asynchronous. It does not guarantee deletion at the exact expiration second. Your session verification must still explicitly check `expiresAt`.

## **19. Authentication libraries – Auth.js and other options**

Building authentication yourself is useful for learning, but production authentication involves many additional responsibilities:

- OAuth and social login.
- Session rotation.
- Account linking.
- Email verification.
- Password reset.
- Multi-factor authentication.
- Secure callbacks.
- CSRF protection.
- Account lockout and abuse prevention.

Authentication libraries can handle much of this complexity.

### **Popular options**

[Auth.js | Overview](https://images.openai.com/static-rsc-4/XcEy7ReiwV45bNKXmFN8OddKNQD3_EWFieC-pUcJqJt-WTsUCJpj6NEsULTE52NyuOQ_QM6UQ3vmnP2gS_ZsH64R7vWqflNnM6YPe8vjBLDkPlAgGvmjPE5UOay6Q8153NmHOEGkHluVoOrETdsGE_SgSJ7e_TrHl4LC71FTimo?purpose=inline)

**Auth.js**

Authentication library supporting Next.js, OAuth providers, credentials and session strategies.

[Official documentation](https://authjs.dev/?utm_source=chatgpt.com)

[Clerk | Authentication and User Management](https://images.openai.com/static-rsc-4/SmJgIv0job620CeDhrdoHfXa2tw7JHn6g24lkRPb1YJ7BfgAERzL5M5NxXbtOp_do1r_Irh4SEjqKp6P-nsnamdZOhIaP9_Z-1UkOknI7AqDJMRFd16o8jSUev1AFybhNkyeDjgi8u6f1Kww3esIHvimU5QXL9Iz-AbO5HjnbH8?purpose=inline)

**Clerk**

Managed authentication with ready-made UI, user management and organization features.

[Official website](https://clerk.com/?utm_source=chatgpt.com)

[Better Auth](https://images.openai.com/static-rsc-4/Zdv-9oipa254PJr4J-ZffcE2RoU93UH47GhbX2XykwsS6Z67TmmxBLKrSS0HvjINQVwCVrkIGOt1BY4KMfqhRQz5cIHhsxiM5haP6gyClABW7c8kWyiqJ0RX5KUEOG9c9XDt-CSO__5GruWqJk90-9dkgiScZr51GRRt2HCPnFI?purpose=inline)

**Better Auth**

TypeScript-first authentication framework with sessions, plugins and multiple authentication methods.

[Official website](https://www.better-auth.com/?utm_source=chatgpt.com)

[Vercel: Qué es, cómo funciona y para qué sirve en Vibe Coding](https://images.openai.com/static-rsc-4/aK3jlgb5koW8jvp9csdHApJIyRVc7aIX0rGbUfl1r9wb0KL55TAHB71QfhKpLr9GNeYQdC_B-MBye4IOKTJhH_gCEoYBCeA29ZRUVe_V3yW0wmgIhOtq2Pcjnytlmxl7ngmFU8NKi2N7g4kqGGHoVlZkXMiwo3gqsWgztUr7TYw?purpose=inline)

**Supabase Auth**

Managed authentication with password, OAuth and other identity options.

[Official documentation](https://supabase.com/docs/guides/auth?utm_source=chatgpt.com)

### **Which one should you learn?**

| Library/service | Best suited for                             |
| --------------- | ------------------------------------------- |
| Auth.js         | Flexible application-managed authentication |
| Clerk           | Managed authentication and ready-made UI    |
| Better Auth     | TypeScript-first application authentication |
| Supabase Auth   | Applications using Supabase services        |

For a Next.js developer, I recommend understanding custom authentication fundamentals first, then learning Auth.js or Better Auth.

## **20. Understanding Auth.js**

Auth.js is a popular authentication solution that integrates with Next.js.

It supports multiple authentication methods, including:

- OAuth providers.
- Email-based authentication.
- Credentials-based authentication.
- Database-backed sessions.
- JWT-based sessions.

### **Typical Auth.js architecture**

```
src/
├── auth.ts
│
└── app/
    ├── api/
    │   └── auth/
    │       └── [...nextauth]/
    │           └── route.ts
    │
    ├── login/
    │   └── page.tsx
    │
    └── dashboard/
        └── page.tsx
```

### **Install Auth.js**

The exact provider and database adapter dependencies depend on your authentication architecture.

Start with:

```
npm install next-auth
```

### **Create an Auth.js configuration**

`src/auth.ts`

```
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    GitHub,
  ],
});
```

### **Configure environment variables**

```
AUTH_SECRET=your_random_secret
AUTH_GITHUB_ID=your_github_client_id
AUTH_GITHUB_SECRET=your_github_client_secret
```

You must obtain the GitHub OAuth credentials from the GitHub developer settings.

### **Create the authentication Route Handler**

`src/app/api/auth/[...nextauth]/route.ts`

```
import { handlers } from "@/auth";

export const { GET, POST } = handlers;
```

### **Read the authenticated session**

```
import { auth } from "@/auth";

export default async function DashboardPage() {
  const session = await auth();

  if (!session?.user) {
    return <p>Please log in</p>;
  }

  return (
    <main>
      <h1>Welcome {session.user.name}</h1>
    </main>
  );
}
```

This is a basic GitHub OAuth example. A production configuration should also define the desired session strategy, user persistence, authorization callbacks and secure route/data checks.

### **Important distinction**

Auth.js is not the same as JWT.

- Auth.js is an authentication framework.
- JWT is a token format.
- Auth.js can use JWT-based or database-backed session strategies, depending on its configuration.

Official documentation: [Auth.js](https://authjs.dev/?utm_source=chatgpt.com).

## **21. Security best practices**

Authentication is security-sensitive. Remember these principles when developing production applications.

### **Password security**

1. Never store plain-text passwords.
2. Use Argon2id or another appropriate password-hashing algorithm.
3. Use unique salts.
4. Never log passwords.
5. Never return password hashes to frontend components.
6. Rate-limit registration and login endpoints.

### **Session security**

1. Use HttpOnly cookies.
2. Use Secure cookies in production.
3. Configure SameSite appropriately.
4. Set session expiration.
5. Invalidate sessions on logout.
6. Rotate sessions after login and privilege changes.
7. Plan for token revocation where required.

### **Application security**

1. Validate all input on the server.
2. Verify authentication on protected operations.
3. Enforce authorization at the data-access layer.
4. Verify resource ownership.
5. Protect against CSRF where applicable.
6. Use HTTPS in production.
7. Use generic login errors.
8. Implement account recovery securely.
9. Add MFA for sensitive applications.
10. Keep secrets outside source control.

OWASP's password and session security guidance is useful for production implementation.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+1



# **22. Practical assignment – Complete Authentication System**



Main assignment

Real-world project



Now build a complete authentication system using:

- Next.js 16
- TypeScript
- MongoDB
- Argon2id
- Zod
- `jose`
- Server Actions
- Secure cookies

## **Project structure**

```
src/
├── app/
│   ├── register/
│   │   ├── page.tsx
│   │   └── register-form.tsx
│   │
│   ├── login/
│   │   ├── page.tsx
│   │   └── login-form.tsx
│   │
│   ├── dashboard/
│   │   ├── page.tsx
│   │   └── profile/
│   │       └── page.tsx
│   │
│   ├── admin/
│   │   └── page.tsx
│   │
│   ├── unauthorized/
│   │   └── page.tsx
│   │
│   └── actions/
│       └── auth.ts
│
├── lib/
│   ├── mongodb.ts
│   ├── session.ts
│   ├── dal.ts
│   ├── permissions.ts
│   └── validations/
│       └── auth.ts
│
└── proxy.ts
```

## **Required functionality**

### **Task 1 – Registration**

- Register using name, email and password.
- Validate fields using Zod.
- Normalize email addresses.
- Hash passwords using Argon2id.
- Prevent duplicate email registration.
- Save users in MongoDB.
- Create a session after successful registration.

### **Task 2 – Login**

- Accept email and password.
- Find the user in MongoDB.
- Verify password using Argon2.
- Reject invalid credentials.
- Reject disabled accounts.
- Create an authenticated session.
- Redirect to the dashboard.

### **Task 3 – Logout**

- Delete the session cookie.
- Redirect to login.
- Verify that protected pages are inaccessible afterward.

### **Task 4 – Protected dashboard**

- Protect dashboard routes.
- Verify session on the server.
- Display the logged-in user's name and email.
- Do not expose password hashes.

### **Task 5 – Admin authorization**

- Implement user and admin roles.
- Allow only admins to access `/admin`.
- Verify permissions inside protected Server Actions.
- Do not rely exclusively on Proxy.

### **Task 6 – Session management**

- Implement session expiration.
- Handle invalid tokens.
- Handle expired sessions.
- Implement session refresh if required.
- Consider revocation and multi-device logout.

### **Task 7 – Security**

- Use secure cookies.
- Use environment variables for secrets.
- Implement login rate limiting.
- Use generic login error messages.
- Protect against unauthorized record access.
- Implement server-side validation.

## **Assignment progress tracker**

**Assignment completion**

# **0%**

0 of 20 tasks completed

Configure MongoDB and environment variables

Install Argon2, Zod and jose

Create user validation schemas

Create registration Server Action

Hash passwords with Argon2id

Prevent duplicate email registration with a unique index

Build registration form

Create login Server Action

Verify submitted passwords

Build login form

Implement signed session tokens

Configure secure HttpOnly cookies

Implement logout

Create the Data Access Layer

Protect dashboard pages

Implement user and admin roles

Protect admin operations

Implement session expiration

Add login rate limiting

Test invalid credentials and expired sessions

Reset checklist

# **23. Interview questions and answers**

## **Basic level**

**Q1. What is authentication?**

Authentication is the process of verifying a user's identity using credentials or another identity-verification method.

**Q2. What is authorization?**

Authorization determines what an authenticated user is allowed to access or perform.

**Q3. What is password hashing?**

Password hashing transforms a password into a one-way hash using a password-hashing algorithm.

**Q4. Why should we not store plain-text passwords?**

If the database is compromised, attackers could immediately obtain users' actual passwords.

**Q5. What is a salt?**

A salt is a unique random value used during password hashing to prevent identical passwords from producing identical hashes.

**Q6. What is bcrypt?**

bcrypt is a password-hashing algorithm designed to make password guessing computationally expensive.

**Q7. What is Argon2id?**

Argon2id is a memory-hard password-hashing algorithm suitable for password storage.

**Q8. What is a session?**

A session maintains a user's authenticated state across multiple HTTP requests.

## **Intermediate level**

**Q9. What is the difference between stateless and database-backed sessions?**

Stateless sessions can be verified using a token without retrieving a session record on every request. Database-backed sessions store session records centrally and generally require database verification.

**Q10. What is JWT?**

JWT is a compact token format used to transmit claims between parties.

**Q11. What are the three parts of a JWT?**

Header, payload and signature.

**Q12. Is JWT encrypted?**

A standard signed JWT is not encrypted. Its payload is Base64URL-encoded and can generally be read by anyone possessing the token.

**Q13. What is the purpose of HttpOnly?**

It prevents JavaScript from accessing a cookie through browser APIs such as `document.cookie`.

**Q14. What is the purpose of Secure?**

It restricts cookie transmission to secure HTTPS connections.

**Q15. What is SameSite?**

It controls whether a cookie is sent with cross-site requests.

**Q16. How do you implement logout?**

For a stateless cookie session, delete the session cookie. For a database-backed session, invalidate the database session and delete the cookie.

## **Advanced level**

**Q17. What is the difference between authentication and RBAC?**

Authentication verifies identity. RBAC controls permissions according to assigned roles.

**Q18. What is a Data Access Layer?**

A server-side layer that centralizes data access, authentication checks and authorization logic.

**Q19. Why is Proxy not sufficient for authentication security?**

Proxy can perform lightweight, early checks, but sensitive operations must independently verify sessions and permissions near the data source.

**Q20. How do you revoke a stateless JWT?**

You need additional mechanisms such as a token denylist, session version checks, short token lifetimes or a database-backed revocation mechanism.

**Q21. Why should passwords be hashed rather than encrypted?**

Password verification does not require recovering the original password. One-way hashing avoids storing a reversible representation.

**Q22. What is the difference between JWT and a session ID?**

JWT usually contains signed claims, whereas a random session ID is generally an opaque identifier used to retrieve a server-side session record.

**Q23. What is the difference between Auth.js and JWT?**

Auth.js is an authentication framework. JWT is a token format that Auth.js can use as part of a session strategy.

**Q24. What is session fixation?**

Session fixation is an attack in which an attacker causes a victim to use a session identifier known to the attacker. Rotating session identifiers after authentication helps prevent it.

**Q25. Why should we implement rate limiting on login?**

To reduce automated password guessing, credential stuffing and abuse of the authentication endpoint.

# **24. Phase 14 – Quick revision notes**

| Concept           | Remember                                      |
| ----------------- | --------------------------------------------- |
| Authentication    | Verifies user identity                        |
| Authorization     | Checks permissions                            |
| Password hashing  | One-way password transformation               |
| Salt              | Random value used in password hashing         |
| Argon2id          | Modern memory-hard password hashing algorithm |
| bcrypt            | Widely used password-hashing algorithm        |
| Session           | Maintains authenticated state                 |
| Stateless session | Session token verified cryptographically      |
| Database session  | Session stored in database                    |
| JWT               | JSON Web Token                                |
| JWS               | Signed JSON Web Token                         |
| JWE               | Encrypted JSON Web Token                      |
| Header            | JWT metadata                                  |
| Payload           | JWT claims                                    |
| Signature         | Integrity and authenticity verification       |
| HttpOnly          | Prevents JavaScript cookie access             |
| Secure            | HTTPS-only cookie transmission                |
| SameSite          | Cross-site cookie behavior                    |
| `cookies()`       | Next.js cookie API                            |
| `jose`            | JavaScript cryptographic token library        |
| `verifySession()` | Validates the user's session                  |
| DAL               | Centralized secure data access                |
| RBAC              | Role-Based Access Control                     |
| Auth.js           | Authentication framework                      |
| Logout            | Invalidates or removes the session            |
| MFA               | Multi-Factor Authentication                   |

# **25. Phase 14 completion checklist**

You should now be able to explain:

- What is authentication?
- What is authorization?
- What is password hashing?
- What is the difference between hashing and encryption?
- What is a salt?
- What are bcrypt and Argon2id?
- What is session management?
- What is the difference between stateless and database-backed sessions?
- What is JWT?
- What are the three parts of JWT?
- What is the difference between JWT and a session ID?
- How do cookies work?
- What are HttpOnly, Secure and SameSite?
- How do you implement registration?
- How do you implement login?
- How do you implement logout?
- What is RBAC?
- What is a Data Access Layer?
- What is Auth.js?
- Why is Proxy not sufficient as the only security layer?

## **26. What's next?**

**Phase 15 – Environment Variables, Configuration and Secrets Management**

Next

[What Are Environment Variables? (Complete Guide for Developers) | ADevGuide](https://images.openai.com/static-rsc-4/g0ZsKOvDeGBVTCoLvMyrqRelsL1IYq7FMmEjA4P94N__jjYJEGWjcmCxtrSgcWfVb8exVwNDmQBAxYdcapAV2CtYRtE8k99Jw6QOi4vpIAUsbYoHU_c18OzLTFzgYpK532U88nlcwbP1BG6NiypeBmyH6McHgeyX6Xdzf7zKcS0?purpose=inline)

[adevguide.com](https://adevguide.com/what-are-environment-variables/?utm_source=chatgpt.com)

In the next phase, we will cover:

1. What are environment variables?
2. `.env`, `.env.local`, `.env.development` and `.env.production`.
3. `process.env` in Next.js.
4. `NEXT_PUBLIC_` variables.
5. Server-side vs client-side environment variables.
6. Environment variables in Vercel and other hosting platforms.
7. Secrets management and security.
8. Type-safe environment configuration.
9. Environment validation using Zod.
10. Development, staging and production configurations.
11. Common environment variable errors.
12. Real-world deployment configuration.

When you're ready, say **OK, Phase 15**.

### **Official references**

1. [Next.js – Authentication Guide](https://nextjs.org/docs/app/guides/authentication?utm_source=chatgpt.com)
2. [OWASP – Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html?utm_source=chatgpt.com)
3. [Auth.js Documentation](https://authjs.dev/?utm_source=chatgpt.com)
4. [jose – JWT and JOSE library](https://github.com/panva/jose?utm_source=chatgpt.com)
5. [MDN – HTTP Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies?utm_source=chatgpt.com)