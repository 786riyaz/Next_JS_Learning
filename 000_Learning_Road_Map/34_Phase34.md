# **Phase 34 – API Security, OWASP and Advanced Backend Security**



Advanced Level

Backend Security & Application Security



**Technology context:** Node.js, TypeScript, Express, Next.js 16 App Router, MongoDB, MySQL, Redis, JWT, Docker and production APIs.

## **1. Learning Objectives**

By the end of this phase, you should be able to:

1. Understand application security and common web vulnerabilities.
2. Learn the OWASP Top 10 security risks.
3. Prevent SQL Injection and NoSQL Injection.
4. Understand and prevent Cross-Site Scripting (XSS).
5. Prevent Cross-Site Request Forgery (CSRF).
6. Understand Server-Side Request Forgery (SSRF).
7. Prevent Broken Access Control and IDOR.
8. Secure authentication, sessions, cookies and JWTs.
9. Configure CORS and HTTP security headers.
10. Implement rate limiting and brute-force protection.
11. Validate input and encode output correctly.
12. Secure file uploads and manage application secrets.
13. Test APIs for vulnerabilities.
14. Create a production security checklist for Node.js and Next.js applications.

## **2. What Is Application Security?**

Application security refers to the practices, technologies, and processes used to protect software applications from unauthorized access, data theft, manipulation, and other attacks.

Consider an e-commerce application.

It may contain:

- User accounts.
- Passwords.
- Personal information.
- Product information.
- Shopping carts.
- Orders.
- Payment integrations.
- Admin dashboards.
- APIs.

If an attacker exploits a vulnerability, they might:

- Access another user's account.
- Read private customer information.
- Modify product prices.
- Create unauthorized orders.
- Steal session cookies.
- Upload malicious files.
- Access internal servers.
- Disrupt the application.

Application security aims to prevent these attacks and reduce their impact.

### **2.1 Security: Confidentiality, Integrity and Availability**

A fundamental security model is the CIA Triad.

## **CIA Triad**

**Confidentiality**

Prevent unauthorized access

**Integrity**

Prevent unauthorized modification

**Availability**

Keep services accessible

**Confidentiality**

Only authorized users should access protected information.

Example: A customer must not see another customer's order history.

**Integrity**

Data should not be modified by unauthorized users.

Example: A customer must not be able to change their order's payment status to `PAID` by editing a request.

**Availability**

Authorized users should be able to use the application when needed.

Example: An attacker should not be able to take down your API by sending millions of requests.

### **2.2 Authentication vs Authorization**

These are two different security concepts.

| Authentication                  | Authorization                          |
| ------------------------------- | -------------------------------------- |
| Who are you?                    | What are you allowed to do?            |
| Verifies identity               | Checks permissions                     |
| Login, password, JWT            | Roles, permissions, policies           |
| Happens before protected access | Evaluated for each protected operation |

Example:

A user successfully logs in. Authentication succeeds.

However, that user is not an administrator. If they attempt to delete another user's account, authorization must reject the operation.

**Remember:** A valid login does not mean unlimited access.

## **3. What Is OWASP?**

[OWASP Foundation | GSoC Organizations](https://images.openai.com/static-rsc-4/KfMtg_yuZzNGijqvFPMUDaHnCHT2r-8f7DJYeLXR_6SahRu6UZbHtW2nmLkzcJrQlLqyJOH3KExImpybGzV0qlVCxUMPrNgVKJw8VRnonLatTCVbNavQHZSPK_Ckwr6n8jIsQBo2Lqvb6ch48LVCPAC4D1PF6_c3CY3g-d8jGoA?purpose=inline)

**OWASP**

Open Worldwide Application Security Project

[Official OWASP website](https://owasp.org/?utm_source=chatgpt.com)

OWASP is a nonprofit organization that provides open resources, standards, tools, and guidance for improving application security.

One of its best-known resources is the **OWASP Top 10**, which identifies important categories of web application security risks.

For this phase, we will use the OWASP Top 10: 2025 edition as the reference for web application risks.

We will also cover API-specific risks using the OWASP API Security Top 10.

### **3.1 OWASP Top 10: 2025**

The current OWASP Top 10: 2025 contains these ten categories.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://top10.owasp.org\&sz=32)

OWASP Top 10+1



| ID  | Category                               | What it means                                                     |
| --- | -------------------------------------- | ----------------------------------------------------------------- |
| A01 | Broken Access Control                  | Users access resources or actions they should not                 |
| A02 | Security Misconfiguration              | Incorrect or insecure application configuration                   |
| A03 | Software Supply Chain Failures         | Compromised or vulnerable dependencies and build processes        |
| A04 | Cryptographic Failures                 | Sensitive data is inadequately protected                          |
| A05 | Injection                              | Untrusted input changes the behavior of commands or queries       |
| A06 | Insecure Design                        | Security requirements and protections are missing from the design |
| A07 | Authentication Failures                | Authentication mechanisms are weak or incorrectly implemented     |
| A08 | Software or Data Integrity Failures    | Software, updates, or data are not adequately verified            |
| A09 | Security Logging and Alerting Failures | Important security events are not properly logged or monitored    |
| A10 | Mishandling of Exceptional Conditions  | Errors and unexpected conditions are handled insecurely           |

We will cover these categories through practical backend vulnerabilities.

### **3.2 OWASP API Security Top 10**

Web application security and API security overlap, but API-specific vulnerabilities deserve separate attention.

The OWASP API Security Top 10: 2023 includes:&#x20;

[image](https://www.google.com/s2/favicons?domain=https://owasp.org\&sz=32)

OWASP Foundation



| ID    | Risk                                            |
| ----- | ----------------------------------------------- |
| API1  | Broken Object Level Authorization (BOLA)        |
| API2  | Broken Authentication                           |
| API3  | Broken Object Property Level Authorization      |
| API4  | Unrestricted Resource Consumption               |
| API5  | Broken Function Level Authorization             |
| API6  | Unrestricted Access to Sensitive Business Flows |
| API7  | Server-Side Request Forgery                     |
| API8  | Security Misconfiguration                       |
| API9  | Improper Inventory Management                   |
| API10 | Unsafe Consumption of APIs                      |

We will use these concepts throughout this phase.

## **4. SQL Injection (SQLi)**

Critical vulnerability

SQL Injection occurs when untrusted user input is incorporated into a SQL query in a way that changes the query's intended structure or behavior.

### **4.1 How SQL Injection happens**

Suppose your application has a login endpoint.

```
SELECT * FROM users
WHERE email = 'user@example.com'
AND password = 'password123';
```

Your Node.js application constructs this query using string concatenation.

**Vulnerable code:**

```
const email = req.body.email;
const password = req.body.password;

const query = `
  SELECT * FROM users
  WHERE email = '${email}'
  AND password = '${password}'
`;
```

The problem is that user input is being inserted directly into SQL syntax.

An attacker could submit specially crafted input that changes the meaning of the query.

For example, a malicious input might make a condition always true.

The database could then execute a query with unintended results.

[Manual Security Testing - How To Run It & Best Practices](https://images.openai.com/static-rsc-4/Z1XwS45g7lvlnttdESGvw8BUhOHSjGhkcmU2r_np-yYJQenxQhH_e1auPZJ3Y9V3jt9zAO2msVm7i1m_XT7gF0v7yQQ9brAK5dZEy8pLzgB1s6rt6hye_qP3RAsjfLPizrrB5akoDGA9IdU62XBXrJohDA-5W0LxGAv9mCPHRsQ?purpose=inline)

[globalapptesting.com](https://www.globalapptesting.com/blog/manual-security-testing?utm_source=chatgpt.com)

### **4.2 Consequences of SQL Injection**

Depending on the database account's privileges and the application's implementation, SQL injection can allow attackers to:

- Read confidential database records.
- Modify existing records.
- Delete data.
- Bypass authentication checks.
- Access administrative database functionality.
- Potentially compromise other systems connected to the database.

### **4.3 Prevention: Parameterized Queries**

The most important defense is to separate SQL structure from user-supplied values.

**Safe MySQL example using&#x20;**`mysql2`**:**

```
const [rows] = await pool.execute(
  `
    SELECT id, email, password_hash
    FROM users
    WHERE email = ?
  `,
  [email]
);
```

The `?` is a parameter placeholder.

The database driver binds the email as a value rather than treating it as SQL syntax.

**Important:** Never retrieve a password by comparing plaintext passwords in SQL. Retrieve the stored password hash, then verify the submitted password using a password-hashing library.

### **4.4 Safe Sequelize example**

```
const users = await User.findAll({
  where: {
    email: req.body.email,
  },
});
```

Sequelize generates parameterized database operations for ordinary structured queries.

For raw queries:

```
const users = await sequelize.query(
  `
    SELECT id, email
    FROM users
    WHERE email = :email
  `,
  {
    replacements: {
      email: req.body.email,
    },
    type: QueryTypes.SELECT,
  }
);
```

### **4.5 What about dynamic sorting?**

You cannot safely bind a column name or SQL keyword using an ordinary value placeholder.

For example, this is not a valid way to bind a column identifier:

```
SELECT * FROM products ORDER BY ?
```

Instead, use an allowlist:

```
const allowedSortColumns = {
  price: "price",
  name: "name",
  createdAt: "created_at",
} as const;

const requestedSort = req.query.sort;

const sortColumn =
  typeof requestedSort === "string" &&
  requestedSort in allowedSortColumns
    ? allowedSortColumns[
        requestedSort as keyof typeof allowedSortColumns
      ]
    : "created_at";

const [rows] = await pool.query(
  `SELECT * FROM products ORDER BY ${sortColumn}`
);
```

The interpolated identifier comes from a fixed, application-owned allowlist, not arbitrary user input.

### **4.6 SQL Injection prevention checklist**

- Use parameterized queries.
- Never concatenate untrusted input into SQL.
- Use allowlists for identifiers and sorting.
- Validate input types and formats.
- Use database accounts with minimum necessary privileges.
- Avoid exposing database error details to clients.
- Review stored procedures and raw ORM queries.

OWASP specifically recommends parameterization and allowlists for SQL components that cannot be bound as values.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+2



## **5. NoSQL Injection**

NoSQL Injection is a similar vulnerability affecting databases that do not use SQL, including MongoDB.

### **5.1 Vulnerable MongoDB login example**

Consider:

```
const user = await User.findOne({
  email: req.body.email,
  password: req.body.password,
});
```

This is problematic if the application accepts arbitrary objects from the request.

An attacker might send an object containing a MongoDB query operator instead of an ordinary string.

For example, the request may attempt to supply an operator such as `$ne`.

If the application passes that object directly into a query, the database may interpret it as query logic.

### **5.2 Prevention: Validate the input type**

Use Zod to enforce the expected structure.

```
import { z } from "zod";

const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).max(128),
});

const result = loginSchema.safeParse(req.body);

if (!result.success) {
  return res.status(400).json({
    message: "Invalid input",
  });
}

const { email, password } = result.data;
```

Now the application only accepts a string email and a string password.

### **5.3 Use password hashing**

Never store passwords as plaintext.

Example using Argon2:

```
npm install argon2
```

Registration:

```
import argon2 from "argon2";

const passwordHash = await argon2.hash(password);

await User.create({
  email,
  passwordHash,
});
```

Login:

```
const user = await User.findOne({ email });

if (!user) {
  return res.status(401).json({
    message: "Invalid credentials",
  });
}

const isValid = await argon2.verify(
  user.passwordHash,
  password
);

if (!isValid) {
  return res.status(401).json({
    message: "Invalid credentials",
  });
}
```

The example assumes the database model stores a `passwordHash` field and the request has already passed schema validation.

### **5.4 NoSQL injection prevention checklist**

1. Validate input with Zod or another schema validator.
2. Require expected primitive types.
3. Do not pass arbitrary request objects directly into MongoDB filters.
4. Avoid constructing queries from untrusted operators.
5. Never store plaintext passwords.
6. Use least-privilege database accounts.
7. Avoid exposing raw database errors.

## **6. Cross-Site Scripting (XSS)**

High-risk browser vulnerability

Cross-Site Scripting occurs when an application allows attacker-controlled content to execute as script in another user's browser.

Suppose your application allows users to post comments.

A malicious user submits content that contains HTML and JavaScript.

If your application renders that content unsafely, the browser may execute the injected script.

### **6.1 Types of XSS**

There are three major types.

**1. Stored XSS**

The malicious content is saved in a database and later displayed to other users.

Example:

```
Attacker
   |
   v
Submit malicious comment
   |
   v
Database
   |
   v
Another user opens comments
   |
   v
Malicious script executes
```

This is particularly dangerous because one stored payload can affect many users.

**2. Reflected XSS**

The malicious input arrives in a request and is reflected in the server's response without safe handling.

For example, a search page displays an untrusted query parameter as HTML.

**3. DOM-based XSS**

The vulnerability exists in client-side JavaScript that processes untrusted data and inserts it into an unsafe DOM location.

### **6.2 React's default protection**

React escapes text values rendered through JSX.

For example:

```
export default function Comment({
  comment,
}: {
  comment: string;
}) {
  return <p>{comment}</p>;
}
```

If the comment contains HTML markup, React treats it as text rather than executing it as HTML.

This is safer than directly inserting untrusted HTML.

### **6.3 Dangerous:&#x20;**`dangerouslySetInnerHTML`

```
export default function Comment({
  comment,
}: {
  comment: string;
}) {
  return (
    <div
      dangerouslySetInnerHTML={{
        __html: comment,
      }}
    />
  );
}
```

If `comment` is attacker-controlled, this can create an XSS vulnerability.

### **6.4 Prevention: HTML sanitization**

If your application genuinely needs to display user-authored HTML, sanitize it with a maintained HTML sanitizer such as DOMPurify.

Install:

```
npm install isomorphic-dompurify
```

Example:

```
import DOMPurify from "isomorphic-dompurify";

export default function RichContent({
  html,
}: {
  html: string;
}) {
  const safeHtml = DOMPurify.sanitize(html);

  return (
    <div
      dangerouslySetInnerHTML={{
        __html: safeHtml,
      }}
    />
  );
}
```

Sanitization should be configured for your actual content requirements. Avoid modifying the sanitized HTML afterward in ways that can reintroduce unsafe content.

### **6.5 Content Security Policy (CSP)**

CSP is a browser security mechanism that restricts which sources can provide scripts, styles, images, and other resources.

Example policy:

```
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none';
```

This is an illustrative restrictive policy, not a drop-in policy for every Next.js application. Framework-generated scripts, third-party integrations, inline scripts, and nonces may require additional configuration.

### **6.6 XSS prevention checklist**

- Prefer framework-safe rendering.
- Avoid inserting untrusted HTML.
- Sanitize HTML when rich text is genuinely required.
- Use contextual output encoding.
- Validate URLs before using them in links.
- Avoid `eval()` and unsafe DOM APIs.
- Deploy a suitable CSP.
- Use `HttpOnly` cookies to reduce JavaScript access to session cookies.

**Important:** `HttpOnly` cookies help protect session credentials from JavaScript access, but they do not prevent XSS itself. An injected script may still perform actions as the victim through their authenticated browser session.

OWASP emphasizes context-aware output encoding and sanitization rather than relying on input validation alone.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+1



## **7. Cross-Site Request Forgery (CSRF)**

CSRF occurs when an attacker causes a user's browser to send an unwanted authenticated request to an application where that user is already logged in.

### **7.1 How CSRF works**

Suppose a user logs in to:

```
https://bank.example.com
```

The browser stores an authenticated session cookie.

The user then visits a malicious website.

That website attempts to cause the browser to send an unwanted request to the bank.

If the browser automatically includes the bank's session cookie and the bank does not properly defend against CSRF, the action may be executed as the logged-in user.

[The Silent Imposter: Understanding and Preventing CSRF Attacks in Go | by Chamath_Pramodaya | Medium](https://images.openai.com/static-rsc-4/uowWSnu325_3Eks_7ySjxlCsxfTIiHQ5F9Wq2BBcO_kZciwI1y522vbYbVUipVgQVdupU9E7AowwhAYklMzD3eCidJhH2xS-GDNycIeWZO1Psw7Qo2GWCKSVr05lMbZ781vxXlLO756Lg4dXNyHN9I1f5R2f4B0hsugsy0q2T3k?purpose=inline)

[medium.com](https://medium.com/%40chamathrko22/the-silent-imposter-understanding-and-preventing-csrf-attacks-in-go-e5c011c22b4d?utm_source=chatgpt.com)

### **7.2 Why cookies create CSRF considerations**

Browsers automatically attach eligible cookies to requests based on cookie attributes and browser rules.

This is useful for session authentication, but it creates a risk when an application accepts state-changing requests without verifying their origin or intent.

### **7.3 Prevention: CSRF tokens**

A CSRF token is a unique, unpredictable value associated with a user's session or request context.

The application verifies that the token accompanies a state-changing request.

Conceptual example:

```
POST /api/profile/update
Content-Type: application/json
X-CSRF-Token: random-unpredictable-token
Cookie: session=...
```

The server verifies the token before accepting the operation.

### **7.4 SameSite cookie attribute**

Cookie settings can reduce cross-site cookie transmission.

```
res.cookie("session", sessionId, {
  httpOnly: true,
  secure: true,
  sameSite: "lax",
  path: "/",
});
```

Common values:

| Value    | Behavior                                             |
| -------- | ---------------------------------------------------- |
| `Strict` | Cookie is generally restricted to same-site contexts |
| `Lax`    | Allows certain top-level cross-site navigations      |
| `None`   | Allows cross-site cookie use; requires `Secure`      |

SameSite is a defense in depth, not a universal substitute for CSRF protection. Its suitability depends on your authentication architecture and cross-site requirements.

### **7.5 Other CSRF defenses**

- CSRF tokens.
- Origin and Referer validation where appropriate.
- SameSite cookies.
- Never use GET requests for state-changing operations.
- Require appropriate content types for API mutations.
- Reauthenticate for especially sensitive actions.

### **7.6 CSRF vs XSS**

| XSS                                               | CSRF                                                     |
| ------------------------------------------------- | -------------------------------------------------------- |
| Injected script executes in a trusted origin      | Victim's browser sends an unwanted authenticated request |
| Targets browser-side script execution             | Targets trust in automatically attached credentials      |
| Output encoding and sanitization are key defenses | CSRF tokens and cookie/origin controls are key defenses  |

OWASP recommends using CSRF tokens for state-changing requests in cookie-authenticated applications and treating SameSite as an additional defense.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series



## **8. Server-Side Request Forgery (SSRF)**

High-risk server vulnerability

SSRF occurs when an attacker tricks your backend server into making requests to unintended destinations.

The important distinction is that the request originates from your server, not directly from the attacker's browser.

### **8.1 How SSRF happens**

Imagine your application provides an API that downloads an image from a URL.

```
POST /api/download-image
Content-Type: application/json

{
  "url": "https://example.com/image.jpg"
}
```

Your backend receives the URL and fetches it:

```
const response = await fetch(req.body.url);
```

The problem is that the application trusts an arbitrary URL supplied by the user.

An attacker might attempt to make your server request:

- Internal application services.
- Private network addresses.
- Cloud instance metadata endpoints.
- Internal administrative interfaces.
- Services that are not exposed to the public internet.

[What Is Server-Side Request Forgery? | Akamai](https://images.openai.com/static-rsc-4/hhY3WojmmAKm_d5rre4xao9BJJxcaOJ6_ASB167nQU3MIPqAZxxpnBz9G3U77WPC6PMkz5MaMdHrKypdBUBrCuwpxbcC_5IbVkS_u4D7P6ur-aqzYbkJonZhP7MlPm2Ha6XX19hnEJ5qtU0wt12YeUt-ZIFl6NFGUrFLZW_aS6Y?purpose=inline)

[akamai.com](https://www.akamai.com/glossary/what-is-server-side-request-forgery?utm_source=chatgpt.com)

### **8.2 Prevention: Allowlist trusted domains**

If your application only needs to download images from a known image provider, allow only those domains.

```
const allowedHosts = new Set([
  "images.example.com",
  "cdn.example.com",
]);

function validateImageUrl(input: string) {
  const url = new URL(input);

  if (url.protocol !== "https:") {
    throw new Error("Only HTTPS URLs are allowed");
  }

  if (!allowedHosts.has(url.hostname)) {
    throw new Error("Domain not allowed");
  }

  if (url.username || url.password) {
    throw new Error("URL credentials are not allowed");
  }

  return url;
}
```

This is a basic validation layer, not a complete SSRF defense.

A production fetcher must also consider:

- DNS resolution and DNS rebinding.
- Private, loopback, link-local and reserved IP addresses.
- IPv4 and IPv6 representations.
- Redirects to disallowed destinations.
- Proxy behavior.
- Network-level egress restrictions.
- Response size and timeout limits.

For high-risk systems, enforce outbound network restrictions independently of application validation.

### **8.3 SSRF prevention checklist**

1. Avoid accepting arbitrary URLs when possible.
2. Allowlist trusted domains.
3. Allow only required protocols.
4. Reject private and internal network destinations.
5. Revalidate every redirect destination.
6. Apply DNS and IP address protections.
7. Set request timeouts.
8. Limit downloaded response sizes.
9. Restrict server outbound network access.
10. Do not expose internal service credentials to fetched destinations.

OWASP specifically highlights domain validation, allowlisting, DNS-related risks, and network-layer controls.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series



## **9. Broken Access Control and IDOR**

Very important

Broken Access Control occurs when an application does not correctly enforce what an authenticated user is allowed to access or modify.

One common example is **IDOR – Insecure Direct Object Reference**.

### **9.1 What is IDOR?**

Suppose your application has this endpoint:

```
GET /api/orders/1001
```

The logged-in user owns order `1001`.

Now the user changes the URL:

```
GET /api/orders/1002
```

If order `1002` belongs to another customer and your application returns it without checking ownership, you have an IDOR vulnerability.

[IDOR Mastery: From Basic ID Changes to Advanced Techniques That Pay $10K+ Bounties | by BugHunter’s Journal | Mar, 2026 | Medium](https://images.openai.com/static-rsc-4/e3Vz0OkDhb0adMAfa10AdorQY9muhZ5zMKh7twpihK52yPMSqt4VmHbHpEF_E0wqSTD5bYsv0Cjr9dxemPovevvNDKW8r58u5DkWAOBxUUre49dqoMruadP2kvkTVzlN1mxkp-UDZfmXd7xiLqEpt-rCw3r3HAPMKGaf3mkZLos?purpose=inline)

[medium.com](https://medium.com/%40bughuntersjournal/idor-mastery-from-basic-id-changes-to-advanced-techniques-that-pay-10k-bounties-1f106016e039?utm_source=chatgpt.com)

### **9.2 Vulnerable implementation**

```
app.get("/orders/:id", async (req, res) => {
  const order = await Order.findById(req.params.id);

  return res.json(order);
});
```

The application checks whether the order exists but does not check whether the authenticated user owns it.

### **9.3 Secure implementation**

Assume authentication middleware has already verified the user's identity and populated `req.user`.

```
app.get("/orders/:id", async (req, res) => {
  const order = await Order.findOne({
    _id: req.params.id,
    userId: req.user.id,
  });

  if (!order) {
    return res.status(404).json({
      message: "Order not found",
    });
  }

  return res.json(order);
});
```

Now the database query itself restricts the result to the authenticated user's orders.

Returning `404` rather than `403` can be useful when you do not want to reveal whether another user's resource exists. Choose the behavior consistently according to your API's disclosure policy.

### **9.4 Role-Based Access Control (RBAC)**

RBAC means permissions are assigned to roles.

Example:

| Role     | Permissions                                         |
| -------- | --------------------------------------------------- |
| Customer | View own profile and orders                         |
| Manager  | Manage products and view reports                    |
| Admin    | Manage users, products, and administrative settings |

Example middleware:

```
function allowRoles(...roles: string[]) {
  return (
    req: AuthenticatedRequest,
    res: Response,
    next: NextFunction
  ) => {
    if (!req.user || !roles.includes(req.user.role)) {
      return res.status(403).json({
        message: "Forbidden",
      });
    }

    next();
  };
}
```

Usage:

```
app.delete(
  "/users/:id",
  authenticate,
  allowRoles("ADMIN"),
  deleteUser
);
```

### **9.5 Object-level vs function-level authorization**

These are different checks.

**Object-level authorization**

Can this user access this particular order?

```
order.userId === req.user.id
```

**Function-level authorization**

Can this user execute this kind of operation?

```
req.user.role === "ADMIN"
```

A user might have permission to view their own orders but not to delete orders or manage other customers.

### **9.6 Deny by default**

A secure application should deny access unless permission is explicitly granted.

Do not implement logic such as:

```
if (user.isBlocked) {
  return res.status(403).end();
}

// Everyone else gets access.
```

Instead, explicitly define which roles or permissions can perform each operation.

### **9.7 Authorization checklist**

- Check authentication on every protected endpoint.
- Check object ownership for user-specific resources.
- Check role and permission requirements.
- Enforce authorization on the server.
- Do not trust user IDs or roles supplied in request bodies.
- Do not rely only on hidden frontend buttons.
- Apply authorization to downloads, exports, and file access.
- Deny by default.

OWASP recommends checking permissions on every request and adopting a deny-by-default approach.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+2



## **10. Authentication Security and JWT Security**

You studied authentication and session management in an earlier phase. Here we focus on common security failures.

### **10.1 JWT structure**

A JSON Web Token commonly has three parts:

```
HEADER.PAYLOAD.SIGNATURE
```

Example conceptual structure:

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Payload:

```
{
  "sub": "user-101",
  "role": "CUSTOMER",
  "iss": "auth-service",
  "aud": "ecommerce-api",
  "exp": 1790000000
}
```

The signature allows the receiving service to verify that the token was issued by a trusted signer and has not been modified.

**Important:** A normal signed JWT is not encrypted. Its payload can be decoded by anyone who obtains the token.

Never place passwords, payment secrets, or other confidential information in a normal JWT payload.

### **10.2 Common JWT security mistakes**

| Mistake                         | Risk                                         |
| ------------------------------- | -------------------------------------------- |
| No expiration                   | Token can remain usable too long             |
| Weak signing secret             | Token forgery risk                           |
| Accepting arbitrary algorithms  | Verification weaknesses                      |
| Not validating issuer           | Trusting tokens from the wrong issuer        |
| Not validating audience         | Token used in an unintended application      |
| Storing tokens insecurely       | Token theft                                  |
| No revocation strategy          | Stolen tokens remain usable until expiration |
| Trusting role from request body | Privilege escalation                         |

### **10.3 Verify JWT claims**

Use a well-maintained library such as `jose`.

```
import { jwtVerify } from "jose";

const secret = process.env.JWT_SECRET;

if (!secret) {
  throw new Error("JWT_SECRET is missing");
}

const key = new TextEncoder().encode(secret);

export async function verifyToken(token: string) {
  const { payload } = await jwtVerify(token, key, {
    algorithms: ["HS256"],
    issuer: "auth-service",
    audience: "ecommerce-api",
  });

  return payload;
}
```

The library verifies the signature and configured standard claims such as expiration, issuer, and audience.

For asymmetric signing, verify using a trusted public key and restrict acceptable algorithms.

### **10.4 Access token vs refresh token**

| Access token               | Refresh token                         |
| -------------------------- | ------------------------------------- |
| Used to access APIs        | Used to obtain new access tokens      |
| Usually short-lived        | Usually longer-lived                  |
| Sent to protected services | Sent to the refresh endpoint          |
| Limits exposure window     | Requires careful storage and rotation |

Refresh tokens should be protected, rotated where appropriate, and monitored for reuse.

### **10.5 Secure cookies**

For browser-based authentication, an HTTP-only secure cookie is often a suitable session transport.

```
res.cookie("__Host-session", sessionId, {
  httpOnly: true,
  secure: true,
  sameSite: "lax",
  path: "/",
  maxAge: 60 * 60 * 1000,
});
```

The `__Host-` prefix requires HTTPS, `Path=/`, and no `Domain` attribute in supporting browsers.

A session ID should be unpredictable, rotated after authentication and privilege changes, and validated against server-side session state where a database-backed session design is used.

### **10.6 Password storage**

Passwords must never be stored in plaintext.

Use a password-hashing algorithm designed to resist offline guessing:

- Argon2id
- scrypt
- bcrypt
- PBKDF2 when required by a particular compliance environment

Argon2id is a strong modern default. Its memory and time cost should be benchmarked for your production environment.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+1



## **11. CORS (Cross-Origin Resource Sharing)**

CORS is a browser-enforced mechanism that controls whether JavaScript running on one origin can read responses from another origin.

First, understand what an origin is.

An origin consists of:

```
Scheme + Host + Port
```

Examples:

```
http://localhost:3000
http://localhost:4000
https://example.com
https://api.example.com
```

These are different origins.

### **11.1 Why CORS exists**

Suppose:

```
Frontend: http://localhost:3000
Backend:  http://localhost:4000
```

Your frontend tries to call:

```
fetch("http://localhost:4000/api/products");
```

The browser applies its same-origin policy and checks whether the backend permits this cross-origin request.

### **11.2 Configure CORS in Express**

Install:

```
npm install cors
npm install -D @types/cors
```

Example:

```
import cors from "cors";

const allowedOrigins = new Set([
  "http://localhost:3000",
  "https://example.com",
]);

app.use(
  cors({
    origin(origin, callback) {
      if (!origin || allowedOrigins.has(origin)) {
        return callback(null, true);
      }

      return callback(new Error("Origin not allowed"));
    },
    credentials: true,
    methods: ["GET", "POST", "PUT", "PATCH", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"],
  })
);
```

For routes that must not be accessed by non-browser clients without an Origin header, add the appropriate authentication and request policy separately. CORS is not authentication.

### **11.3 CORS with credentials**

If you use cookies in cross-origin requests:

Frontend:

```
fetch("https://api.example.com/profile", {
  credentials: "include",
});
```

Backend:

```
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Credentials: true
```

You cannot use:

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

for credentialed browser requests.

### **11.4 What is a preflight request?**

For certain cross-origin requests, the browser first sends an `OPTIONS` request to check whether the actual request is permitted.

Example:

```
OPTIONS /api/products
Origin: https://example.com
Access-Control-Request-Method: DELETE
Access-Control-Request-Headers: authorization
```

The server responds with the allowed origin, method, and headers.

### **11.5 CORS misconceptions**

| Misconception                           | Reality |
| --------------------------------------- | ------- |
| CORS prevents all unauthorized requests | No      |
| CORS is authentication                  | No      |
| CORS protects APIs from Postman         | No      |
| CORS prevents CSRF                      | No      |
| Every API needs `*`                     | No      |

CORS primarily controls browser access to cross-origin responses. Your backend must still authenticate and authorize every request.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+1



## **12. HTTP Security Headers**

HTTP security headers instruct browsers how to handle your application's content and resources.

Important headers include:

| Header                      | Purpose                                      |
| --------------------------- | -------------------------------------------- |
| `Strict-Transport-Security` | Enforces HTTPS for future requests           |
| `Content-Security-Policy`   | Restricts permitted resource sources         |
| `X-Content-Type-Options`    | Prevents MIME sniffing                       |
| `Referrer-Policy`           | Controls referrer information                |
| `X-Frame-Options`           | Helps protect against clickjacking           |
| `Permissions-Policy`        | Controls access to selected browser features |

### **12.1 Use Helmet in Express**

Install:

```
npm install helmet
```

Configure:

```
import helmet from "helmet";

app.use(helmet());
```

Helmet sets several security-related HTTP headers. Review its defaults and configure CSP for your application rather than blindly copying a policy from another website.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://expressjs.com\&sz=32)

Express.js



### **12.2 Security headers in Next.js 16**

In `next.config.ts`:

```
import type { NextConfig } from "next";

const securityHeaders = [
  {
    key: "X-Content-Type-Options",
    value: "nosniff",
  },
  {
    key: "Referrer-Policy",
    value: "strict-origin-when-cross-origin",
  },
  {
    key: "X-Frame-Options",
    value: "DENY",
  },
  {
    key: "Strict-Transport-Security",
    value: "max-age=31536000",
  },
];

const nextConfig: NextConfig = {
  async headers() {
    return [
      {
        source: "/:path*",
        headers: securityHeaders,
      },
    ];
  },
};

export default nextConfig;
```

Only enable HSTS after HTTPS is correctly configured across the relevant deployment. Do not add `includeSubDomains` or `preload` unless every affected subdomain is HTTPS-ready and you understand the consequences.

Next.js supports setting response headers through the `headers()` configuration.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **12.3 CSP example**

An illustrative policy:

```
Content-Security-Policy:
  default-src 'self';
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none';
```

For a real Next.js application, build a policy that accounts for script nonces, styles, image sources, API connections, and required integrations.

## **13. Rate Limiting and Brute-Force Protection**

Rate limiting restricts how frequently a client can make requests.

It protects APIs from:

- Automated abuse.
- Password guessing.
- OTP guessing.
- Excessive requests.
- Resource exhaustion.
- Unwanted scraping.

### **13.1 What is brute-force attacking?**

A brute-force attack repeatedly attempts different credentials until a valid combination is found.

For example:

```
POST /api/auth/login

Attempt 1: password A
Attempt 2: password B
Attempt 3: password C
Attempt 4: password D
...
```

Without protection, an attacker can automate thousands of attempts.

### **13.2 Rate limiting in Express**

Install:

```
npm install express-rate-limit
```

Example:

```
import { rateLimit } from "express-rate-limit";

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  limit: 5,
  standardHeaders: "draft-8",
  legacyHeaders: false,
  message: {
    message: "Too many login attempts. Try again later.",
  },
});

app.use("/api/auth/login", loginLimiter);
```

This limits a client to five requests in a 15-minute window under the configured keying behavior.

For production, carefully configure the key generator, proxy trust, distributed storage, and failure policy.

### **13.3 Why Redis-backed rate limiting?**

If you have multiple Node.js instances:

```
Node Instance 1 -> Local limit counter
Node Instance 2 -> Local limit counter
Node Instance 3 -> Local limit counter
```

Each instance has a separate counter. An attacker may distribute requests across instances.

A shared Redis-backed limiter can maintain a common counter.

```
Node 1 ----+
Node 2 ----+---- Redis Rate Limit Store
Node 3 ----+
```

A common Redis-backed implementation is:

```
npm install rate-limit-redis
```

Example:

```
import { rateLimit } from "express-rate-limit";
import { RedisStore } from "rate-limit-redis";
import redis from "./config/redis";

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  limit: 5,
  store: new RedisStore({
    sendCommand: (...args: string[]) =>
      redis.sendCommand(args),
  }),
  message: {
    message: "Too many attempts",
  },
});
```

The example assumes `redis` is an already-connected `node-redis` client. Check the current library compatibility and configuration when installing it.

### **13.4 Different rate limits for different endpoints**

| Endpoint               | Example policy                         |
| ---------------------- | -------------------------------------- |
| Login                  | Strict                                 |
| OTP verification       | Very strict                            |
| Password reset         | Strict                                 |
| Public product listing | More generous                          |
| File upload            | Restrict count and size                |
| Admin operations       | Strict and monitored                   |
| Search                 | Limit request frequency and complexity |

The numbers depend on your application, user behavior, risk, and infrastructure.

### **13.5 Additional brute-force protections**

- Account-based throttling.
- IP-based throttling.
- Progressive delays.
- Temporary lockouts with careful abuse protections.
- MFA for sensitive accounts.
- Breached-password detection.
- Alerts for suspicious authentication patterns.
- Generic login error messages.

**Important:** IP-only rate limiting is insufficient. Attackers can distribute requests across many IP addresses, and shared IPs can represent many legitimate users.

Unrestricted resource consumption is a major API security risk. Limit request rates, payload sizes, pagination, batch operations, and expensive computations.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://wstg.owasp.org\&sz=32)

OWASP Web Security Testing Guide+1



## **14. Input Validation and Output Encoding**

Input validation checks whether incoming data follows the expected structure and business rules.

It is one of the most important defenses in backend development.

### **14.1 Why validation matters**

Suppose your API expects:

```
{
  "name": "Laptop",
  "price": 75000,
  "quantity": 10
}
```

But an attacker sends:

```
{
  "name": {},
  "price": "unlimited",
  "quantity": -999999
}
```

Without validation, invalid data may reach your database or business logic.

### **14.2 Validation with Zod**

Install:

```
npm install zod
```

Define a schema:

```
import { z } from "zod";

export const createProductSchema = z.object({
  name: z.string().trim().min(2).max(100),

  price: z.number().finite().positive(),

  quantity: z.number().int().min(0).max(100000),

  description: z.string().max(2000).optional(),
});
```

Validate the request:

```
const result = createProductSchema.safeParse(req.body);

if (!result.success) {
  return res.status(400).json({
    message: "Validation failed",
    errors: result.error.flatten(),
  });
}

const productData = result.data;
```

Use the validated object rather than the original untrusted request body.

### **14.3 Validation should cover more than data types**

You should validate:

- Required fields.
- Data types.
- String length.
- Numeric ranges.
- Email formats.
- Date formats.
- Enumerated values.
- File metadata.
- Business rules.
- Nested object structure.

For example, a user might submit a valid integer quantity of 1,000,000, but your business rules may allow a maximum purchase of only 20 units.

### **14.4 Input validation vs output encoding**

These solve different problems.

| Input validation        | Output encoding                         |
| ----------------------- | --------------------------------------- |
| Checks incoming data    | Makes data safe for its output context  |
| Rejects invalid formats | Prevents data being interpreted as code |
| Example: valid email    | Example: safely render a comment        |
| Zod, schema validators  | JSX escaping, contextual encoding       |

Validation does not replace SQL parameterization, HTML sanitization, or output encoding.

## **15. Password Security and Account Protection**

Passwords are among the most sensitive parts of an application.

### **15.1 Never store plaintext passwords**

Incorrect:

```
{
  "email": "user@example.com",
  "password": "myPassword123"
}
```

Correct approach:

```
{
  "email": "user@example.com",
  "passwordHash": "$argon2id$v=19$..."
}
```

### **15.2 Hashing vs encryption**

| Hashing                        | Encryption                       |
| ------------------------------ | -------------------------------- |
| One-way transformation         | Reversible with the correct key  |
| Used for password verification | Used when data must be recovered |
| No decryption operation        | Decryption is possible           |
| Argon2id                       | AES-GCM                          |

Passwords should generally be hashed, not encrypted.

Other confidential data that must later be recovered, such as certain API credentials or personal information, may require encryption at rest with appropriate key management.

### **15.3 Additional password protections**

- Enforce sensible password length limits.
- Block commonly used and compromised passwords.
- Avoid arbitrary frequent password changes without evidence of compromise.
- Use MFA for sensitive accounts.
- Require reauthentication for critical operations.
- Protect password reset workflows.
- Invalidate or rotate sessions after password changes.

### **15.4 Secure password reset flow**

```
User requests password reset
           |
           v
Generate random reset token
           |
           v
Store token hash with expiry
           |
           v
Send reset link
           |
           v
User submits new password
           |
           v
Verify token and expiry
           |
           v
Hash new password
           |
           v
Invalidate reset token
           |
           v
Revoke or rotate existing sessions
```

Do not expose whether an email address exists through password-reset responses.

## **16. File Upload Security**

File uploads can create serious security vulnerabilities if the application accepts arbitrary files.

Suppose your application allows users to upload:

- Profile pictures.
- Resume PDFs.
- Product images.
- Documents.
- Videos.

An attacker may try to upload:

- Executable files.
- HTML containing malicious scripts.
- Oversized files.
- Files with misleading extensions.
- Malformed files designed to exploit parsers.
- Files that overwrite or expose existing content.

### **16.1 Common file upload mistakes**

**Mistake 1: Trusting the file extension**

A file named:

```
profile.jpg
```

is not necessarily a JPEG image.

**Mistake 2: Trusting the Content-Type header**

A client can send a misleading MIME type.

**Mistake 3: Using the original filename**

This may introduce path traversal, collisions, or unexpected characters.

**Mistake 4: Unlimited file size**

An attacker may exhaust disk space, memory, or processing resources.

### **16.2 Secure upload strategy**

[How to Protect Your Web App from Malware via File Uploads - DEV Community](https://images.openai.com/static-rsc-4/DT2C4_G58knKYu_xCD6q3A-owmxCzLAwfUPlx48yFkIpH_MMD8iIWBbYOoyALInG1QYROg4x_k1_sFe4D4iVNijvd1AeGBi-KuvBg_3PhysjzvCGz5mBmA935DBgr2jfXT_4fhV6MUS894NWm3F33MqUw7OrmWj9uSpIYpRB1CY?purpose=inline)

[dev.to](https://dev.to/rajath_shiremath_523cd54/stop-accepting-malware-the-pragmatic-guide-to-secure-file-uploads-58go?utm_source=chatgpt.com)

A secure upload workflow:

1. Authenticate the user.
2. Check upload permissions.
3. Enforce file size and count limits.
4. Allow only necessary file formats.
5. Check actual file content, not only its extension.
6. Generate a random server-side filename.
7. Store the file outside the application webroot or in private object storage.
8. Scan potentially dangerous files.
9. Serve files through controlled access.
10. Log relevant security events.

### **16.3 Example using Multer**

Install:

```
npm install multer
npm install -D @types/multer
```

Basic size and file-count limits:

```
import multer from "multer";

const upload = multer({
  storage: multer.diskStorage({
    destination: "./private-uploads",

    filename: (_req, _file, callback) => {
      callback(null, `${crypto.randomUUID()}`);
    },
  }),

  limits: {
    fileSize: 5 * 1024 * 1024,
    files: 1,
  },

  fileFilter: (_req, file, callback) => {
    const allowedTypes = [
      "image/jpeg",
      "image/png",
      "application/pdf",
    ];

    if (!allowedTypes.includes(file.mimetype)) {
      return callback(
        new Error("File type not allowed")
      );
    }

    callback(null, true);
  },
});
```

This is only a first layer. `file.mimetype` comes from client-provided metadata and is not proof of the file's real content. Validate file signatures using an appropriate parser or file-type detection library, and scan files where risk warrants it.

### **16.4 File storage best practices**

- Use private object storage for confidential files.
- Keep upload storage outside public static directories.
- Use random filenames.
- Avoid executing uploaded content.
- Restrict download permissions.
- Set safe `Content-Disposition` and content types.
- Consider malware scanning.
- Protect against archive bombs and decompression bombs.
- Set quotas and upload limits.

OWASP recommends allowlisting required formats, validating actual content, generating application-controlled filenames, and restricting file size.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://cheatsheetseries.owasp.org\&sz=32)

OWASP Cheat Sheet Series+1



## **17. Secrets Management and Environment Security**

A secret is sensitive information that should not be publicly accessible.

Examples:

```
DATABASE_URL=...
JWT_SECRET=...
REDIS_PASSWORD=...
AWS_SECRET_ACCESS_KEY=...
PAYMENT_PROVIDER_SECRET=...
```

### **17.1 Never hardcode secrets**

Incorrect:

```
const JWT_SECRET = "my-super-secret-key";
```

Correct:

```
const JWT_SECRET = process.env.JWT_SECRET;

if (!JWT_SECRET) {
  throw new Error("JWT_SECRET is missing");
}
```

### **17.2 Never expose secrets in frontend code**

In Next.js, variables prefixed with `NEXT_PUBLIC_` are intended to be exposed to the browser bundle.

Incorrect:

```
NEXT_PUBLIC_DATABASE_PASSWORD=secret
NEXT_PUBLIC_JWT_SECRET=secret
```

Correct:

```
DATABASE_PASSWORD=secret
JWT_SECRET=secret
NEXT_PUBLIC_APP_URL=https://example.com
```

Use server-only modules for database clients and secret-handling code:

```
import "server-only";
```

### **17.3 Secret management best practices**

1. Store secrets in deployment environment settings or a secret manager.
2. Keep `.env.local` out of Git.
3. Commit only `.env.example` with placeholder values.
4. Rotate credentials when exposed.
5. Use separate secrets for development, testing, and production.
6. Give each service only the credentials it needs.
7. Never return secrets in API responses.
8. Avoid logging secrets.
9. Restrict who can access production credentials.
10. Use short-lived credentials where practical.

### **17.4 What if a secret is accidentally committed?**

Deleting the secret from the latest commit is not enough. It may remain in Git history or copied repositories.

A proper response is:

1. Revoke or rotate the compromised secret immediately.
2. Review access logs for suspicious usage.
3. Remove exposed credentials from the repository history where appropriate.
4. Check deployment logs and CI/CD artifacts.
5. Identify other places where the secret may have been copied.

## **18. Software Supply Chain Security**

Your application depends on external packages.

For example:

```
{
  "dependencies": {
    "express": "...",
    "mongoose": "...",
    "jsonwebtoken": "...",
    "zod": "..."
  }
}
```

Every dependency adds a potential security and maintenance risk.

### **18.1 Common supply chain risks**

- Vulnerable npm packages.
- Compromised packages.
- Typosquatting.
- Malicious installation scripts.
- Unmaintained dependencies.
- Compromised build pipelines.
- Leaked deployment credentials.

### **18.2 Check dependency vulnerabilities**

Run:

```
npm audit
```

Get a machine-readable report:

```
npm audit --json
```

Update packages carefully:

```
npm outdated
```

Do not blindly execute:

```
npm audit fix --force
```

It can introduce breaking dependency upgrades. Review the dependency changes, test the application, and verify compatibility.

### **18.3 Supply chain best practices**

- Commit your lockfile.
- Use `npm ci` in CI/CD.
- Remove unused dependencies.
- Review dependency maintainers and provenance.
- Keep dependencies updated.
- Use automated vulnerability scanning.
- Protect GitHub Actions secrets.
- Restrict permissions for CI/CD tokens.
- Review build scripts.
- Generate and retain software bills of materials (SBOMs) where appropriate.

### **18.4 Software Bill of Materials (SBOM)**

An SBOM is an inventory of software components and dependencies included in an application.

It helps organizations identify whether their software contains a dependency affected by a newly discovered vulnerability.

This is particularly relevant to OWASP Top 10:2025's Software Supply Chain Failures category.

## **19. Security Logging and Error Handling**

Security logging helps detect and investigate suspicious activity.

Important events to record include:

- Failed login attempts.
- Successful logins.
- Password reset requests.
- Permission denials.
- Administrative actions.
- Unusual request patterns.
- Sensitive account changes.
- Repeated validation failures.
- Suspicious file uploads.

### **19.1 Structured security logging**

Example:

```
logger.warn({
  event: "AUTHENTICATION_FAILED",
  userEmail: normalizedEmail,
  ip: req.ip,
  timestamp: new Date().toISOString(),
});
```

Do not log passwords, full authentication tokens, private keys, or sensitive request bodies.

### **19.2 Secure error responses**

Incorrect:

```
res.status(500).json({
  error: error.stack,
});
```

This can expose:

- Internal file paths.
- Database details.
- Package versions.
- Environment information.
- Internal implementation details.

Better:

```
logger.error({
  message: "Internal server error",
  error,
});

res.status(500).json({
  message: "Internal server error",
});
```

Detailed error information should be available through access-controlled internal logs, not public responses.

### **19.3 Mishandling exceptional conditions**

OWASP Top 10:2025 includes Mishandling of Exceptional Conditions.

Examples:

- Application continues after an essential security check fails.
- Database errors expose sensitive information.
- A failed payment is incorrectly marked successful.
- A timeout is interpreted as proof that an operation failed.
- Cleanup operations are skipped after an exception.
- A partially completed workflow leaves inconsistent business state.

**Security principle:** Fail safely, preserve system invariants, and make recovery behavior explicit.

## **20. Security in Next.js 16 App Router**

Next.js has several execution environments, and each has different security responsibilities.

You must secure:

- Server Components.
- Client Components.
- Route Handlers.
- Server Actions.
- Proxy.
- Database access.
- Authentication and authorization logic.

### **20.1 Server Components**

Server Components execute on the server, making them suitable for accessing private data and server-side services.

Example:

```
import "server-only";

import { db } from "@/lib/db";

export default async function AdminPage() {
  const users = await db.user.findMany();

  return (
    <main>
      <h1>Admin Dashboard</h1>
      <p>Total users: {users.length}</p>
    </main>
  );
}
```

However, being a Server Component does not automatically make a page secure.

You must verify the user's identity and permissions before retrieving or returning protected data.

### **20.2 Route Handler security**

A Route Handler is a public API endpoint if it is reachable through your application.

Example:

`app/api/admin/users/route.ts`

```
import { NextResponse } from "next/server";
import { requireAdmin } from "@/lib/auth";
import { db } from "@/lib/db";

export async function GET() {
  const user = await requireAdmin();

  if (!user) {
    return NextResponse.json(
      { message: "Forbidden" },
      { status: 403 }
    );
  }

  const users = await db.user.findMany({
    select: {
      id: true,
      email: true,
      createdAt: true,
    },
  });

  return NextResponse.json(users);
}
```

Here, `requireAdmin()` represents your server-side authentication and authorization function.

The database query intentionally excludes sensitive fields such as password hashes.

### **20.3 Server Action security**

A Server Action must be treated like a public-facing mutation endpoint.

Do not assume only your own frontend can call it.

Example:

```
"use server";

import { requireUser } from "@/lib/auth";
import { db } from "@/lib/db";
import { z } from "zod";

const profileSchema = z.object({
  name: z.string().trim().min(2).max(80),
});

export async function updateProfile(formData: FormData) {
  const user = await requireUser();

  const result = profileSchema.safeParse({
    name: formData.get("name"),
  });

  if (!result.success) {
    return {
      success: false,
      message: "Invalid profile information",
    };
  }

  await db.user.update({
    where: {
      id: user.id,
    },
    data: {
      name: result.data.name,
    },
  });

  return {
    success: true,
  };
}
```

Important security rules:

1. Authenticate inside the action.
2. Authorize the specific operation.
3. Validate all input.
4. Never trust client-provided user IDs or roles.
5. Avoid returning sensitive database records.
6. Handle errors without exposing internal details.

### **20.4 Proxy security**

In Next.js 16, request interception uses `proxy.ts`.

Proxy can be used for lightweight routing and authentication checks, but it should not be your only security layer.

Example:

```
import { NextRequest, NextResponse } from "next/server";

export function proxy(request: NextRequest) {
  const session = request.cookies.get("__Host-session");

  if (!session) {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*", "/admin/:path*"],
};
```

This only checks whether a cookie exists. It does not verify that the session is genuine or that the user is an administrator.

Perform full session verification and authorization in your server-side data-access layer and protected operations.

### **20.5 Use a Data Access Layer (DAL)**

A DAL centralizes sensitive data access and authorization logic.

Example structure:

```
src/
├── app/
│   ├── admin/
│   └── api/
│
├── lib/
│   ├── auth.ts
│   ├── db.ts
│   └── dal/
│       ├── users.ts
│       ├── orders.ts
│       └── products.ts
│
└── actions/
```

Example:

```
import "server-only";

import { requireAdmin } from "@/lib/auth";
import { db } from "@/lib/db";

export async function getAdminUsers() {
  await requireAdmin();

  return db.user.findMany({
    select: {
      id: true,
      email: true,
      role: true,
    },
  });
}
```

Now different parts of your application can use the same protected data-access function.

### **20.6 Next.js security rules**

| Area                  | Security responsibility                  |
| --------------------- | ---------------------------------------- |
| Server Components     | Protect server-side data access          |
| Client Components     | Never expose secrets                     |
| Route Handlers        | Authenticate, authorize, validate        |
| Server Actions        | Treat as callable mutation endpoints     |
| Proxy                 | Lightweight request interception         |
| DAL                   | Centralize authorization and data access |
| Environment variables | Protect private configuration            |
| Cookies               | Use appropriate security attributes      |

**Important:** Never place database credentials, private API keys, signing secrets, or authorization logic that must remain confidential in Client Components.

## **21. Security Testing and Vulnerability Scanning**

Writing secure code is important, but you must also test it.

Security testing looks for weaknesses in application logic, configuration, dependencies, authentication, and infrastructure.

### **21.1 Types of security testing**

| Testing type         | Purpose                                                             |
| -------------------- | ------------------------------------------------------------------- |
| SAST                 | Analyze source code for security weaknesses                         |
| DAST                 | Test a running application from the outside                         |
| Dependency scanning  | Detect vulnerable packages                                          |
| Secret scanning      | Detect exposed credentials                                          |
| Container scanning   | Detect vulnerable container components                              |
| Penetration testing  | Simulate attacks to identify exploitable weaknesses                 |
| API security testing | Test authentication, authorization, validation, and resource limits |

### **21.2 Useful security tools**

[File:OWASP ZAP logo.svg - Wikimedia Commons](https://images.openai.com/static-rsc-4/3Lt-Gs86DR-wfrCgGkj7eOALVCXSWng2_hGDgJtM5S-8t1-9-q3mr8sX2wqFUiKq6bdaTiWMy82uH4hd9qHt0Lo29ZzjLG-l-v7eSJHcYz2e90MpK1itTX3Qpud6lMwmxSE5NlPm06yqO5ReoqpleuVjA2bU7gRNmbsXemi7tQQ?purpose=inline)

**OWASP ZAP**

A web application security testing tool for identifying common vulnerabilities.

[Official website](https://www.zaproxy.org/?utm_source=chatgpt.com)

[Burp Suite](https://images.openai.com/static-rsc-4/eoeM4UpMB-KW3zJYHAlVTAFhTaEER7CGDgYY-BubLZc-9ANb-OH1xQoyKLhMsdw2K0LCxFclzDWSkvbkyuzeCFxeWIqz6dq-gGiaLd9lcRKK8epD1WRp8AijLmbRUVWQ6fClaKdBWvG5MbYtF2C-HzEy--wEtVXGo31j62Rf2ug?purpose=inline)

**Burp Suite**

A platform for intercepting, inspecting, and testing HTTP requests.

[Official website](https://portswigger.net/burp?utm_source=chatgpt.com)

[Snyk | 2023 Campus Forward Award Winner](https://images.openai.com/static-rsc-4/X_k44Bpu8S9_ZdQo8b5vPxnTf1cssJ-9ESFNm20Yk_eIveYt0kAP7p-vlwvttQQ1QQ8_bCP-JMENG332o6pIfC8FScke0t1Wk2gKLWu8jTierMOLSmzK01AEObMLOeba1hx5lc84ziHTddETwrw0CSQGbqDwh18y8KxzYI2rEC4?purpose=inline)

**Snyk**

Helps identify vulnerabilities in dependencies, source code, and containers.

[Official website](https://snyk.io/?utm_source=chatgpt.com)

[Rediscovering vulnerabilities with CodeQL MRVA | Zerotistic's blog](https://images.openai.com/static-rsc-4/QaO25mFFde1-Dm4C3lLk3ro34GUffAKCHMiM0UbBBaTLeSuxiLWcqC-XXOz-8H9U_O-qLKE5IjfzxF0n6_oWhb844MiF0awUWbZObFqcWhz60HqoUm-797At4Otu6lLyy22_4Ydv8WiLS14SmmOx1lTM4SbzUTc9TyQIVCmxkwA?purpose=inline)

**GitHub Code Security**

Provides dependency alerts, secret scanning, and code scanning capabilities depending on repository and plan configuration.

[Official documentation](https://docs.github.com/en/code-security?utm_source=chatgpt.com)

### **21.3 Test your own application**

For your development environment, test:

1. Can a customer access another customer's order?
2. Can an ordinary user access an admin API?
3. Can an unauthenticated user access protected endpoints?
4. Can a request submit unexpected object types?
5. Can an upload exceed the permitted size?
6. Can a user bypass rate limits?
7. Can a URL-fetching feature access internal destinations?
8. Do error responses expose stack traces?
9. Can a token be reused after logout when it should be revoked?
10. Can a user modify protected fields such as `role`, `isAdmin`, or `paymentStatus`?

Use an authorized test environment and test accounts. Never run security scans against systems you do not own or have permission to test.

## **22. Complete Security Architecture for a Production API**

Let's combine the concepts into one backend architecture.

[Swarms API Infrastructure: Technical Architecture Overview | by Kye Gomez | Medium](https://images.openai.com/static-rsc-4/HdaKpU-QD66nmLIKwMdojmiap3qHvILF3KyDQldjZpkpvRbzELzkgFkTGesO_S2mtzouNuDumsYeI2nyDcduFfkur6MvJui9JPm2n3pwHvqld2mdy7Rm6v_O-ltKZUX_QsgOcwGBwK_q1mleY8Nq6equS_OEUP0BBt1LR-U-h30?purpose=inline)

[medium.com](https://medium.com/%40kyeg/swarms-api-infrastructure-technical-architecture-overview-fca7c73bf462?utm_source=chatgpt.com)

```
                   Internet
                       |
                       v
                WAF / CDN Layer
                       |
                       v
                 API Gateway
                       |
           +-----------+-----------+
           |                       |
           v                       v
     Rate Limiting             CORS Policy
           |
           v
     Authentication
           |
           v
     Authorization
           |
           v
     Input Validation
           |
           v
      Business Logic
           |
       +---+---+
       |       |
       v       v
    Database  Redis
       |
       v
   Audit Logging
       |
       v
 Monitoring and Alerting
```

A real deployment may have security controls at multiple layers, and some operations occur in parallel. The diagram illustrates responsibilities, not a mandatory execution order.

### **Security responsibilities by layer**

| Layer          | Security controls                                   |
| -------------- | --------------------------------------------------- |
| CDN/WAF        | Traffic filtering, DDoS protections, managed rules  |
| API Gateway    | Routing, request limits, authentication integration |
| Application    | Authorization, validation, business rules           |
| Database       | Least privilege, parameterized queries, encryption  |
| Redis          | Private networking, authentication, access controls |
| Infrastructure | TLS, network restrictions, patching                 |
| Monitoring     | Audit events, alerts, incident investigation        |

## **23. Common Security Mistakes**

| Mistake                                  | Risk                                    | Correct approach                               |
| ---------------------------------------- | --------------------------------------- | ---------------------------------------------- |
| Trusting frontend validation             | Attackers bypass the UI                 | Validate on the server                         |
| Storing plaintext passwords              | Account compromise after database leak  | Argon2id or another suitable password hash     |
| Returning stack traces                   | Information disclosure                  | Generic client errors, protected logs          |
| Missing object ownership checks          | IDOR/BOLA                               | Verify authorization for each object           |
| Using arbitrary SQL strings              | SQL injection                           | Parameterized queries                          |
| Passing raw request objects into MongoDB | NoSQL injection                         | Validate and construct query objects           |
| Trusting uploaded MIME types             | Malicious files                         | Inspect actual file content                    |
| Allowing arbitrary server-side URLs      | SSRF                                    | Destination allowlists and egress restrictions |
| Wildcard credentialed CORS               | Cross-origin data exposure              | Explicit origin allowlist                      |
| No rate limits                           | Brute force and resource exhaustion     | Endpoint-specific limits                       |
| No HTTPS                                 | Interception and modification           | TLS throughout the relevant network path       |
| Hardcoding secrets                       | Credential leakage                      | Secret management                              |
| Checking only cookies in Proxy           | Unauthorized access                     | Verify sessions and permissions on the server  |
| Trusting Gateway authorization alone     | Internal authorization bypass           | Enforce permissions inside services            |
| Blind dependency upgrades                | Breaking changes or new vulnerabilities | Review, test, and update carefully             |

## **24. Interview Questions and Answers**

### **Basic Level**

**Q1. What is OWASP?**

OWASP is an organization that provides open resources and guidance to improve application security.

**Q2. What is the CIA Triad?**

Confidentiality, Integrity, and Availability. These are three fundamental objectives of information security.

**Q3. What is SQL Injection?**

SQL Injection is an attack in which untrusted input changes the intended structure or behavior of a SQL query.

**Q4. What is NoSQL Injection?**

NoSQL Injection occurs when untrusted input changes the intended behavior of a NoSQL database query.

**Q5. What is XSS?**

Cross-Site Scripting is a vulnerability that allows attacker-controlled scripts to execute in another user's browser.

**Q6. What is CSRF?**

Cross-Site Request Forgery tricks a user's browser into sending an unwanted authenticated request to a website.

**Q7. What is CORS?**

Cross-Origin Resource Sharing is a browser mechanism that controls which origins can access responses from another origin.

**Q8. What is rate limiting?**

Rate limiting restricts how frequently a client can make requests to an API.

### **Intermediate Level**

**Q9. What is the difference between stored, reflected, and DOM-based XSS?**

- Stored XSS: Malicious content is stored and later rendered.
- Reflected XSS: Malicious input is reflected in a response.
- DOM-based XSS: Unsafe client-side DOM manipulation causes script execution.

**Q10. What is IDOR?**

IDOR is an access-control vulnerability where manipulating an object identifier allows a user to access another user's resource.

**Q11. What is SSRF?**

SSRF occurs when an attacker tricks a backend server into making requests to unintended destinations.

**Q12. Why should passwords be hashed instead of encrypted?**

Password verification does not require recovering the original password. A strong password hash is designed to make offline guessing expensive, whereas encryption is reversible with the correct key.

**Q13. What is the difference between authentication and authorization?**

Authentication verifies identity. Authorization determines which resources and actions that identity can access.

**Q14. What is a CSRF token?**

It is an unpredictable value used to verify that a state-changing request was intentionally initiated through an authorized application context.

**Q15. Why is CORS not an authentication mechanism?**

CORS controls browser access to cross-origin responses. It does not prevent non-browser clients from making requests or replace server-side authorization.

### **Advanced Level**

**Q16. How would you prevent SQL Injection in Node.js?**

Use parameterized queries, safe ORM operations, allowlists for dynamic identifiers, input validation, and least-privilege database accounts.

**Q17. How would you secure a file-upload API?**

Authenticate the uploader, validate permissions, restrict file size and formats, inspect actual file content, generate random filenames, scan files where appropriate, and store uploads outside the public webroot.

**Q18. How do you prevent IDOR in an e-commerce application?**

Check object-level authorization on every request. For customer orders, query using both the order ID and the authenticated user's ID.

**Q19. What is the difference between JWT and a session?**

A JWT is a signed token containing claims that can be verified by a service. A session is server-managed authentication state referenced by a session identifier. JWTs and sessions have different storage, revocation, and scaling trade-offs.

**Q20. How do you protect a Next.js Server Action?**

Authenticate the user, authorize the operation, validate input, enforce business rules, and avoid trusting client-provided identity or permissions.

**Q21. How would you secure an API against brute-force attacks?**

Use endpoint-specific rate limiting, account-level throttling, progressive delays, MFA, monitoring, and secure password-reset workflows.

**Q22. What is CSP?**

Content Security Policy is an HTTP response header that restricts the sources and types of resources a browser can load or execute.

**Q23. What is the difference between encryption and hashing?**

Encryption is reversible with the appropriate key. Hashing is designed as a one-way transformation and is commonly used for password verification.

**Q24. What is a software supply chain attack?**

It is an attack that compromises software dependencies, build tools, development infrastructure, or distribution processes to affect the software or its users.

**Q25. What is the OWASP API Security Top 10?**

It is a list of major security risks specific to APIs, including broken object-level authorization, broken authentication, unrestricted resource consumption, SSRF, and unsafe API consumption.

## **25. Practical Assignment**

Major security project

Build a **Secure E-commerce REST API** using:

- Next.js 16 or Node.js + Express.
- TypeScript.
- MongoDB or MySQL.
- Zod.
- Argon2id.
- JWT or database-backed sessions.
- Redis.
- Helmet.
- Express Rate Limit.
- Multer.
- Docker.

### **Project structure**

```
secure-ecommerce-api/
│
├── src/
│   ├── config/
│   │   ├── db.ts
│   │   └── redis.ts
│   │
│   ├── middleware/
│   │   ├── authenticate.ts
│   │   ├── authorize.ts
│   │   ├── rateLimiter.ts
│   │   ├── validate.ts
│   │   └── errorHandler.ts
│   │
│   ├── schemas/
│   │   ├── auth.schema.ts
│   │   ├── user.schema.ts
│   │   └── product.schema.ts
│   │
│   ├── controllers/
│   ├── services/
│   ├── routes/
│   ├── utils/
│   │
│   └── server.ts
│
├── private-uploads/
├── .env.example
├── Dockerfile
└── package.json
```

### **Required features**

**Project progress**

# **0%**

0 of 17 tasks completed

Implement secure user registration and login.

Hash passwords using Argon2id.

Implement secure authentication and session/token validation.

Configure HTTP-only, Secure and appropriate SameSite cookies if using cookie authentication.

Add role-based authorization for customer and admin routes.

Prevent IDOR when accessing orders and user records.

Use Zod to validate all incoming request data.

Use parameterized SQL or safe MongoDB query construction.

Implement rate limiting for login, OTP, and password-reset endpoints.

Configure CORS with an explicit origin allowlist.

Configure Helmet and appropriate security headers.

Implement secure file uploads with file-size and content validation.

Store secrets in environment variables and keep them out of Git.

Implement generic client-facing error responses and structured security logs.

Test unauthorized access and cross-user resource access.

Scan dependencies and fix applicable security vulnerabilities.

Document the security controls and remaining risks.

Reset checklist

### **Security testing scenarios**

Test your application against these scenarios:

| Test                                          | Expected result                   |
| --------------------------------------------- | --------------------------------- |
| Login with incorrect password                 | Authentication rejected           |
| Access protected route without session        | `401 Unauthorized`                |
| Customer accesses another customer's order    | Access denied                     |
| Customer calls admin endpoint                 | `403 Forbidden`                   |
| Invalid request body                          | `400 Bad Request`                 |
| Too many login attempts                       | `429 Too Many Requests`           |
| Unsupported upload format                     | Upload rejected                   |
| Oversized file                                | Upload rejected                   |
| Disallowed browser origin                     | Cross-origin access not permitted |
| Internal URL supplied to URL-fetching feature | Request rejected                  |
| Database error occurs                         | No internal details exposed       |
| Missing authorization permission              | Access denied                     |

### **Expected outcome**

You should be able to explain not just how to implement each security feature, but also which vulnerability it prevents and what its limitations are.

## **26. Quick Revision Notes**

| Concept            | Remember                                                   |
| ------------------ | ---------------------------------------------------------- |
| CIA Triad          | Confidentiality, Integrity, Availability                   |
| Authentication     | Verifies identity                                          |
| Authorization      | Checks permissions                                         |
| OWASP              | Open application security project                          |
| SQL Injection      | Untrusted input changes SQL structure                      |
| NoSQL Injection    | Untrusted input changes NoSQL query behavior               |
| XSS                | Attacker-controlled script execution in browser            |
| Stored XSS         | Malicious content saved and later rendered                 |
| Reflected XSS      | Malicious input reflected in response                      |
| DOM XSS            | Unsafe client-side DOM processing                          |
| CSRF               | Unwanted authenticated browser request                     |
| SSRF               | Backend is tricked into making unintended requests         |
| IDOR               | Access another user's resource through an object reference |
| JWT                | Signed token containing claims                             |
| Argon2id           | Password hashing algorithm                                 |
| CORS               | Browser cross-origin response access control               |
| CSP                | Browser resource execution restrictions                    |
| Helmet             | Express security-header middleware                         |
| Rate limiting      | Restricts request frequency                                |
| Zod                | Runtime schema validation                                  |
| File validation    | Check content, type, size, and permissions                 |
| Secrets management | Protect private credentials                                |
| SAST               | Static application security testing                        |
| DAST               | Dynamic application security testing                       |
| SBOM               | Inventory of software components                           |
| Security logging   | Record security-relevant events                            |
| Least privilege    | Grant only necessary permissions                           |
| Deny by default    | Reject access unless explicitly allowed                    |

## **27. Official Documentation and Further Reading**

These are the most useful references for this phase:

1. [OWASP Top 10:2025](https://top10.owasp.org/?utm_source=chatgpt.com) – Current web application security risk categories.
2. [OWASP API Security Top 10](https://owasp.org/www-project-api-security/?utm_source=chatgpt.com) – API-specific security risks.
3. [SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com) – Parameterized queries and safe SQL.
4. [XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com) – Output encoding and sanitization.
5. [Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html?utm_source=chatgpt.com) – Secure session handling.
6. [SSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com) – Secure outbound requests.
7. [Authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html?utm_source=chatgpt.com) – Access control and least privilege.
8. [File Upload Security](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html?utm_source=chatgpt.com) – Safe file handling.
9. [Express Production Security](https://expressjs.com/en/advanced/best-practice-security.html?utm_source=chatgpt.com) – Express security recommendations.
10. [Next.js Authentication Guide](https://nextjs.org/docs/app/guides/authentication?utm_source=chatgpt.com) – Authentication and authorization guidance.
11. [Next.js Security Headers](https://nextjs.org/docs/app/api-reference/config/next-config-js/headers?utm_source=chatgpt.com) – Response header configuration.
12. [OWASP ZAP Documentation](https://www.zaproxy.org/docs/?utm_source=chatgpt.com) – Security testing.

## **Phase 34 Completion Checklist**

**Learning progress**

# **0%**

Application security and CIA Triad

OWASP Top 10:2025 and API Security Top 10

SQL Injection and NoSQL Injection

XSS and CSP

CSRF and secure cookies

SSRF prevention

Broken Access Control and IDOR

JWT and password security

CORS and HTTP security headers

Rate limiting and brute-force protection

Input validation and output encoding

File upload security

Secrets management

Software supply chain security

Security logging and error handling

Next.js 16 security

Security testing and vulnerability scanning

0 of 17 topics completed

## **Next Phase Preview**

Phase 35

# **Advanced Backend Architecture, Scalability and System Design**

We will focus on designing backend systems that can handle growing traffic, large datasets, and production workloads.

**Topics will include:**

1. Scalability Fundamentals
2. Vertical vs Horizontal Scaling
3. Load Balancing
4. Reverse Proxy Architecture
5. Stateless vs Stateful Applications
6. Database Scaling Strategies
7. Database Indexing and Query Optimization
8. Read Replicas and Replication
9. Database Sharding and Partitioning
10. Connection Pooling
11. CDN and Edge Architecture
12. Asynchronous Job Processing
13. Background Workers and Queues
14. Distributed Rate Limiting
15. Fault Tolerance and High Availability
16. Capacity Planning
17. System Design Fundamentals
18. Designing Real-World Scalable Applications

**When you're ready, say: OK, Phase 35.**