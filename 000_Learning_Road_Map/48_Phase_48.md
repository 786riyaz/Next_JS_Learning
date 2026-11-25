# Phase 48 – DevSecOps, Application Security and Infrastructure Security

**Level:** Advanced  
**Domain:** Cybersecurity, Application Security, DevOps, Cloud Security  
**Primary Technologies:** Node.js, Express, Next.js, OWASP, Docker, Kubernetes, AWS, GitHub Actions

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand DevSecOps and its relationship with DevOps.
2. Integrate security into the Software Development Life Cycle (SDLC).
3. Understand the OWASP Top 10:2025.
4. Identify and prevent SQL Injection.
5. Understand Cross-Site Scripting (XSS).
6. Prevent Cross-Site Request Forgery (CSRF).
7. Understand Server-Side Request Forgery (SSRF).
8. Implement secure authentication and authorization.
9. Understand password hashing and JWT security.
10. Configure HTTPS, TLS and security headers.
11. Implement secure CORS policies.
12. Secure REST APIs.
13. Implement rate limiting and request validation.
14. Manage application secrets securely.
15. Understand SAST, DAST and SCA.
16. Scan application dependencies and Docker images.
17. Implement container security.
18. Secure Kubernetes workloads.
19. Apply AWS IAM security best practices.
20. Secure CI/CD pipelines.
21. Understand security monitoring and incident response.
22. Build a complete DevSecOps pipeline.

---

# PART 1: INTRODUCTION TO DEVSECOPS

# 2. What Is DevSecOps?

DevSecOps stands for:

- Dev – Development.
- Sec – Security.
- Ops – Operations.

DevSecOps is an approach that integrates security practices throughout the software development and deployment lifecycle.

In traditional development, security testing is sometimes performed near the end of the project.

This can create problems because security vulnerabilities may already exist in the application architecture, source code and infrastructure.

DevSecOps makes security a continuous responsibility.

### Traditional Software Development

```text
Developer
    |
    v
Write Code
    |
    v
Build Application
    |
    v
Test Application
    |
    v
Deploy Application
    |
    v
Security Testing
```

Security is introduced relatively late.

### DevSecOps

```text
             PLAN
               |
               v
             CODE
               |
               v
            SECURITY
               |
               v
             BUILD
               |
               v
            SECURITY
               |
               v
             TEST
               |
               v
            SECURITY
               |
               v
            RELEASE
               |
               v
            DEPLOY
               |
               v
            MONITOR
               |
               v
         Continuous Feedback
```

Security is considered throughout the process.

### Benefits of DevSecOps

| Benefit | Explanation |
|---|---|
| Early Vulnerability Detection | Finds security issues before production |
| Reduced Security Costs | Fixing vulnerabilities early is often less expensive |
| Automated Security | Security checks run automatically |
| Better Compliance | Helps meet organizational security requirements |
| Reduced Risk | Reduces exposure to common attacks |
| Faster Delivery | Integrates security into existing development workflows |
| Better Collaboration | Developers, security engineers and operations teams share responsibility |

---

# 3. DevOps vs DevSecOps

| Feature | DevOps | DevSecOps |
|---|---|---|
| Main Focus | Development and operations collaboration | Development, security and operations collaboration |
| Automation | Build, test and deployment | Build, test, security and deployment |
| Security | May be handled separately | Integrated throughout the lifecycle |
| Vulnerability Scanning | Optional or separate | Integrated into pipelines |
| Dependency Scanning | Not necessarily included | Commonly included |
| Container Security | May be handled separately | Integrated into image lifecycle |
| Infrastructure Security | Infrastructure automation | Infrastructure automation with security controls |

**Important:** DevSecOps does not mean that developers alone become responsible for all security. Security remains a shared responsibility involving developers, security teams, infrastructure engineers and management.

---

# 4. Secure Software Development Life Cycle (SSDLC)

SDLC stands for Software Development Life Cycle.

It describes the stages involved in building and maintaining software.

A Secure SDLC integrates security requirements into those stages.

### Traditional SDLC

```text
Requirement Analysis
        |
        v
System Design
        |
        v
Development
        |
        v
Testing
        |
        v
Deployment
        |
        v
Maintenance
```

### Secure SDLC

```text
Security Requirements
        |
        v
Threat Modeling
        |
        v
Secure Architecture
        |
        v
Secure Coding
        |
        v
Security Testing
        |
        v
Secure Deployment
        |
        v
Security Monitoring
        |
        v
Continuous Improvement
```

### Security Activities at Each Stage

| SDLC Stage | Security Activity |
|---|---|
| Requirement Analysis | Define authentication, authorization and data protection requirements |
| System Design | Threat modeling and security architecture |
| Development | Secure coding and code reviews |
| Testing | SAST, DAST and penetration testing |
| Build | Dependency and container scanning |
| Deployment | Secure infrastructure and secrets management |
| Maintenance | Vulnerability patching and monitoring |

### What Is Threat Modeling?

Threat modeling is the process of identifying potential threats against an application before or during its development.

For example, suppose you are developing an expense tracker.

Your application contains:

- User authentication.
- Expense management.
- Database.
- Admin dashboard.
- REST APIs.

Potential threats include:

| Component | Potential Threat |
|---|---|
| Login | Brute-force attack |
| Database | SQL Injection |
| Expense API | Unauthorized data access |
| Admin Dashboard | Privilege escalation |
| File Upload | Malicious file upload |
| API | Denial-of-service attack |

Threat modeling helps identify these risks before they become production vulnerabilities.

A useful framework is STRIDE:

| Category | Meaning | Example |
|---|---|---|
| S | Spoofing | Attacker impersonates another user |
| T | Tampering | Unauthorized modification of data |
| R | Repudiation | User denies performing an action |
| I | Information Disclosure | Sensitive data exposure |
| D | Denial of Service | Making an application unavailable |
| E | Elevation of Privilege | Ordinary user gains admin privileges |

---

# PART 2: OWASP TOP 10

# 5. What Is OWASP?

OWASP stands for **Open Worldwide Application Security Project**.

It is a nonprofit organization focused on improving application security.

OWASP provides:

- Security guidelines.
- Testing methodologies.
- Security standards.
- Developer resources.
- Security tools.
- Vulnerability prevention techniques.

Official website: [OWASP](https://owasp.org/)

One of its most important projects is the OWASP Top 10.

---

# 6. OWASP Top 10:2025

The current OWASP Top 10 release is 2025.

It identifies ten major categories of web application security risks. [OWASP Foundation](https://owasp.org/projects/top-ten?lang=en\&utm_source=chatgpt.com)

| Rank | Category | Description |
|---|---|---|
| A01 | Broken Access Control | Users access resources they are not authorized to access |
| A02 | Security Misconfiguration | Incorrect or insecure application configuration |
| A03 | Software Supply Chain Failures | Vulnerabilities or compromises in dependencies and software delivery |
| A04 | Cryptographic Failures | Weak or incorrectly implemented cryptography |
| A05 | Injection | Untrusted input changes the behavior of an interpreter |
| A06 | Insecure Design | Security weaknesses introduced by architectural or design decisions |
| A07 | Authentication Failures | Weak authentication mechanisms |
| A08 | Software or Data Integrity Failures | Failure to verify the integrity of software or data |
| A09 | Security Logging and Alerting Failures | Inadequate logging, monitoring or alerting |
| A10 | Mishandling of Exceptional Conditions | Incorrect handling of unexpected conditions and failures |

Official reference: [OWASP Top 10:2025](https://top10.owasp.org/)

Let's understand the important categories.

## 6.1 A01 – Broken Access Control

Broken Access Control occurs when an application fails to properly enforce permissions.

Example:

Suppose your application has two users:

```text
User A:
ID = 101

User B:
ID = 102
```

User A requests:

```http
GET /api/users/101/expenses
```

This is legitimate if User A is allowed to access their own expenses.

But what if User A changes the URL?

```http
GET /api/users/102/expenses
```

If the backend returns User B's expenses without checking ownership, the application has an access-control vulnerability.

This is commonly called an IDOR-style vulnerability.

IDOR stands for Insecure Direct Object Reference.

### Incorrect Implementation

```javascript
app.get("/api/users/:userId/expenses", async (req, res) => {
    const expenses = await Expense.findAll({
        where: {
            userId: req.params.userId
        }
    });

    res.json(expenses);
});
```

Problem:

The application trusts the user ID supplied in the URL.

### Better Implementation

```javascript
app.get("/api/expenses", authenticate, async (req, res) => {
    const expenses = await Expense.findAll({
        where: {
            userId: req.user.id
        }
    });

    res.json(expenses);
});
```

Now the backend obtains the user ID from the authenticated identity rather than trusting a user-supplied ID.

### Prevention

1. Enforce authorization on the server.
2. Verify resource ownership.
3. Use role-based access control.
4. Apply least privilege.
5. Never rely only on frontend restrictions.
6. Test unauthorized resource access.

**Important:** Hiding an Edit or Delete button in React does not provide security. The backend must enforce the permission.

---

## 6.2 A02 – Security Misconfiguration

Security Misconfiguration occurs when systems or applications are configured insecurely.

Examples:

- Debug mode enabled in production.
- Default passwords.
- Public database access.
- Unnecessary open ports.
- Exposed stack traces.
- Missing security headers.
- Incorrect CORS configuration.
- Public cloud storage containing private data.

### Example

Incorrect Express configuration:

```javascript
app.use((err, req, res, next) => {
    res.status(500).json({
        error: err.stack
    });
});
```

This can expose internal implementation details.

Better:

```javascript
app.use((err, req, res, next) => {
    console.error(err);

    res.status(500).json({
        message: "Internal Server Error"
    });
});
```

In production, detailed error information should be logged securely rather than exposed to users.

---

## 6.3 A03 – Software Supply Chain Failures

Modern applications depend on third-party packages.

For example:

```text
Next.js Application
       |
       v
npm Packages
       |
       +------ React
       |
       +------ Express
       |
       +------ Axios
       |
       +------ Other Packages
```

A vulnerability in a dependency can affect your application.

Supply-chain risks include:

- Malicious packages.
- Compromised dependencies.
- Vulnerable libraries.
- Compromised build tools.
- Untrusted CI/CD Actions.
- Unverified container images.
- Compromised software distribution.

### Prevention

1. Review dependencies before installation.
2. Commit lockfiles.
3. Scan dependencies.
4. Update vulnerable packages.
5. Pin CI/CD Actions to verified commit SHAs.
6. Verify downloaded artifacts and container images.
7. Generate and retain software provenance where appropriate.
8. Restrict publishing permissions.

We will cover dependency scanning and software supply-chain security later in this phase.

---

## 6.4 A04 – Cryptographic Failures

Cryptographic Failures occur when sensitive information is not adequately protected.

Examples:

- Plaintext passwords.
- Weak encryption.
- Insecure password storage.
- Expired or invalid TLS certificates.
- Hardcoded encryption keys.
- Sensitive data transmitted over HTTP.
- Poor key management.

### Incorrect Password Storage

```javascript
const user = {
    email: "user@example.com",
    password: "mypassword123"
};
```

Never store passwords in plaintext.

Use secure password hashing.

We will study password hashing in detail later.

---

## 6.5 A05 – Injection

Injection occurs when untrusted input is interpreted as executable instructions.

Common types:

- SQL Injection.
- NoSQL Injection.
- OS Command Injection.
- LDAP Injection.
- Cross-Site Scripting.

Example SQL Injection:

```javascript
const query = `
    SELECT * FROM users
    WHERE email = '${email}'
`;
```

If untrusted input is concatenated into SQL, an attacker may alter the query's logic.

Use parameterized queries instead.

---

## 6.6 A06 – Insecure Design

Insecure Design means the application architecture does not adequately account for security requirements.

Example:

An expense tracker allows users to transfer money between accounts.

The design does not include:

- Transaction limits.
- Ownership validation.
- Fraud detection.
- Transaction audit logs.
- Idempotency protection.

Even if the code is syntactically correct, the system may still be insecure.

### Prevention

- Threat modeling.
- Security requirements.
- Abuse-case analysis.
- Rate limits.
- Transaction limits.
- Authorization design.
- Security reviews.

---

## 6.7 A07 – Authentication Failures

Authentication verifies the identity of a user.

Common failures include:

- Weak passwords.
- Missing MFA for sensitive accounts.
- Weak session management.
- Insecure password reset.
- Brute-force attacks.
- Poor JWT validation.
- Predictable session identifiers.

Prevention:

- Secure password hashing.
- MFA.
- Login rate limiting.
- Secure session cookies.
- Token expiration.
- Secure account recovery.
- Session revocation.

---

## 6.8 A08 – Software or Data Integrity Failures

Integrity means ensuring that software and data have not been modified unexpectedly.

Examples:

- Installing an unverified package.
- Deploying an untrusted Docker image.
- Accepting modified application artifacts.
- Using compromised build tools.
- Executing untrusted serialized data.

Prevention:

- Verify artifact signatures where applicable.
- Use trusted registries.
- Protect deployment pipelines.
- Verify checksums or cryptographic signatures.
- Restrict publishing access.
- Use immutable deployment artifacts.

---

## 6.9 A09 – Security Logging and Alerting Failures

Applications need security-related logs.

Examples of events worth recording:

- Failed login attempts.
- Repeated authentication failures.
- Permission-denied requests.
- Suspicious administrative activity.
- Changes to account permissions.
- Unusual API access.
- Security configuration changes.

However, never log:

- Plaintext passwords.
- Access tokens.
- Session IDs.
- Private keys.
- Full payment card details.

Logs should be protected against unauthorized access and tampering.

---

## 6.10 A10 – Mishandling of Exceptional Conditions

Applications must handle unexpected situations securely.

Examples:

- Database connection failure.
- Invalid request payload.
- Unexpected null values.
- External API timeout.
- Insufficient memory.
- Partial transaction failure.

Bad error handling can expose information or leave the system in an inconsistent state.

Example:

```javascript
try {
    await transferMoney();
} catch (error) {
    res.status(500).json({
        message: error.message
    });
}
```

Exposing raw internal error messages may reveal implementation details.

A better approach:

```javascript
try {
    await transferMoney();

    res.json({
        message: "Transfer completed"
    });
} catch (error) {
    console.error(error);

    res.status(500).json({
        message: "Unable to complete transfer"
    });
}
```

For financial operations, also use database transactions and appropriate recovery mechanisms.

---

# PART 3: SQL INJECTION

# 7. What Is SQL Injection?

SQL Injection is a vulnerability that occurs when untrusted input is inserted into a SQL query in a way that allows it to change the query's intended structure.

Suppose you have a login query:

```javascript
const query = `
    SELECT * FROM users
    WHERE email = '${email}'
    AND password = '${password}'
`;
```

The problem is that user input becomes part of the SQL statement.

This can allow an attacker to manipulate the query.

### Incorrect Approach

```javascript
const email = req.body.email;

const query = `
    SELECT * FROM users
    WHERE email = '${email}'
`;

const [rows] = await db.query(query);
```

The application constructs SQL by concatenating untrusted input.

### Correct Approach – Parameterized Queries

With `mysql2`:

```javascript
const email = req.body.email;

const query = `
    SELECT * FROM users
    WHERE email = ?
`;

const [rows] = await db.execute(query, [email]);
```

Here:

```javascript
?
```

is a parameter placeholder.

The database driver binds the supplied value separately from the SQL structure.

### Why Is This Safer?

The database treats the input as a value rather than interpreting it as SQL syntax.

### Comparison

| Feature | String Concatenation | Parameterized Query |
|---|---|---|
| Security | Vulnerable to SQL injection | Protects bound values against SQL injection |
| Query Construction | Mixes data and SQL syntax | Separates data and SQL syntax |
| Recommended | No | Yes |
| Maintainability | Poor | Better |

OWASP recommends parameterized queries and allow-listing for dynamic SQL structures that cannot be parameterized. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet?utm_source=chatgpt.com)

Official resource: [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)

## 7.1 SQL Injection with Sequelize

Sequelize provides parameter binding.

Example:

```javascript
const users = await sequelize.query(
    "SELECT * FROM users WHERE email = :email",
    {
        replacements: {
            email: req.body.email
        },
        type: QueryTypes.SELECT
    }
);
```

Alternatively, use model methods:

```javascript
const user = await User.findOne({
    where: {
        email: req.body.email
    }
});
```

### Important

Using an ORM does not automatically make every query safe.

Unsafe raw SQL, dynamically constructed query fragments and unsafe literal expressions can still introduce injection vulnerabilities.

---

# PART 4: CROSS-SITE SCRIPTING (XSS)

# 8. What Is XSS?

XSS stands for Cross-Site Scripting.

It occurs when an application places untrusted data into a web page in a way that allows executable content to run in a user's browser.

An attacker may use XSS to:

- Perform actions as the victim.
- Read information accessible to the injected script.
- Modify page content.
- Capture user input.
- Interact with application APIs using the victim's browser session.

### Example

Suppose a website allows users to submit comments.

A user submits content containing executable HTML or JavaScript.

If the application renders it unsafely, the browser may execute it.

### Three Main Types of XSS

| Type | Description |
|---|---|
| Stored XSS | Malicious content is stored and later displayed |
| Reflected XSS | Malicious input is reflected in a response |
| DOM-Based XSS | Client-side JavaScript handles untrusted input unsafely |

## 8.1 Stored XSS

Suppose a comments application stores user content in a database.

```text
User Input
    |
    v
Database
    |
    v
Website
    |
    v
Browser
```

If the content is rendered unsafely, every user who views the affected page may be exposed.

## 8.2 Reflected XSS

An application receives a request parameter and reflects it into a response without safe output handling.

For example:

```text
/search?q=user-input
```

If the search term is inserted into HTML unsafely, an XSS vulnerability may occur.

## 8.3 DOM-Based XSS

This occurs when client-side JavaScript uses untrusted data in dangerous DOM operations.

For example:

```javascript
const params = new URLSearchParams(location.search);

document.getElementById("result").innerHTML =
    params.get("message");
```

The problem is using `innerHTML` with untrusted content.

### Safer Approach

```javascript
const params = new URLSearchParams(location.search);

document.getElementById("result").textContent =
    params.get("message") ?? "";
```

`textContent` inserts text rather than parsing the value as HTML.

---

# 9. Preventing XSS in React and Next.js

React escapes text content by default.

Example:

```jsx
function Comment({ message }) {
    return <p>{message}</p>;
}
```

React renders the value as text.

However, you can bypass this protection with unsafe HTML rendering.

### Dangerous Example

```jsx
function Comment({ message }) {
    return (
        <div
            dangerouslySetInnerHTML={{
                __html: message
            }}
        />
    );
}
```

This is dangerous if `message` contains untrusted HTML.

### Better Approach

Use normal React rendering:

```jsx
function Comment({ message }) {
    return <div>{message}</div>;
}
```

If your application genuinely needs to display user-generated HTML:

1. Use a maintained HTML sanitization library.
2. Sanitize according to the expected HTML context.
3. Configure an appropriate Content Security Policy.
4. Avoid unsafe DOM APIs.
5. Test the sanitization behavior.

Example using DOMPurify:

```jsx
import DOMPurify from "dompurify";

function RichText({ html }) {
    const cleanHTML = DOMPurify.sanitize(html);

    return (
        <div
            dangerouslySetInnerHTML={{
                __html: cleanHTML
            }}
        />
    );
}
```

**Important:** DOMPurify is intended for browser-side HTML sanitization. For server-side rendering, use a properly configured server-side DOM environment and keep the sanitizer updated.

OWASP recommends context-aware output handling and warns that no single XSS defense solves every scenario. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet?utm_source=chatgpt.com)

Official resource: [OWASP XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

---

# PART 5: CROSS-SITE REQUEST FORGERY (CSRF)

# 10. What Is CSRF?

CSRF stands for Cross-Site Request Forgery.

It occurs when an attacker tricks an authenticated user's browser into sending an unwanted request to an application where that user is already authenticated.

Suppose a user is logged into:

```text
bank.example.com
```

The application uses an authentication cookie.

The user then visits an unrelated malicious website.

That website attempts to make the browser submit a request to the bank.

If the browser automatically attaches the authentication cookie and the application does not have appropriate CSRF protections, the unwanted action may succeed.

### Architecture

```text
User Logged Into Bank
         |
         v
Browser Has Session Cookie
         |
         v
Visits Malicious Website
         |
         v
Unwanted Request Sent
         |
         v
Bank Receives Request
         |
         v
CSRF Protection Missing?
         |
         v
Possible Unauthorized Action
```

### Example Scenario

A banking application has:

```http
POST /api/transfer
```

The user is already authenticated.

A malicious website attempts to make the browser send a transfer request.

The application must verify that the request is legitimate.

---

# 11. Preventing CSRF

Important defenses:

1. CSRF tokens.
2. Appropriate SameSite cookie settings.
3. Origin and Referer validation where appropriate.
4. Custom request headers for API designs.
5. Avoid state-changing GET requests.

### SameSite Cookies

Example:

```javascript
res.cookie("session", sessionId, {
    httpOnly: true,
    secure: true,
    sameSite: "lax"
});
```

Possible values:

| Value | Meaning |
|---|---|
| `Strict` | Cookie is restricted to same-site contexts |
| `Lax` | Allows certain cross-site top-level navigation requests |
| `None` | Allows cross-site use, but requires `Secure` |

SameSite is useful protection, but it should not be treated as a universal replacement for CSRF tokens.

### CSRF Token Concept

```text
Client Requests Page
       |
       v
Server Creates CSRF Token
       |
       v
Client Receives Token
       |
       v
Client Sends Token with State-Changing Request
       |
       v
Server Validates Token
       |
       v
Request Accepted or Rejected
```

For applications using cookie-based authentication, use an appropriate CSRF protection strategy.

**Important:** Bearer tokens explicitly supplied through an Authorization header have a different CSRF threat model from automatically attached authentication cookies. However, XSS can still compromise a user's browser session.

---

# PART 6: SERVER-SIDE REQUEST FORGERY (SSRF)

# 12. What Is SSRF?

SSRF stands for Server-Side Request Forgery.

It occurs when an attacker manipulates a server into making requests to unintended destinations.

Suppose your application provides a feature:

```text
Enter URL -> Generate Website Preview
```

The backend receives a URL and fetches its content.

Example:

```javascript
app.post("/preview", async (req, res) => {
    const response = await fetch(req.body.url);

    const data = await response.text();

    res.send(data);
});
```

This is dangerous if the server fetches arbitrary user-provided URLs.

An attacker may attempt to make the server access:

- Internal services.
- Private network addresses.
- Cloud metadata endpoints.
- Internal administrative interfaces.

### SSRF Architecture

```text
Attacker
   |
   v
Sends URL to Application
   |
   v
Backend Fetches URL
   |
   v
Internal Network Resource
   |
   v
Sensitive Information at Risk
```

## 12.1 Preventing SSRF

Use:

- Strict URL validation.
- Destination allow-lists.
- Domain and IP restrictions.
- Network egress restrictions.
- Redirect validation.
- DNS resolution safeguards.
- Timeouts and response-size limits.

### Safer Design

If your application only needs to retrieve content from approved websites, do not accept arbitrary destinations.

Example:

```javascript
const ALLOWED_HOSTS = new Set([
    "example.com",
    "api.example.com"
]);

function validateURL(input) {
    const url = new URL(input);

    if (url.protocol !== "https:") {
        throw new Error("Only HTTPS is allowed");
    }

    if (!ALLOWED_HOSTS.has(url.hostname)) {
        throw new Error("Host not allowed");
    }

    return url;
}
```

Then:

```javascript
app.post("/preview", async (req, res) => {
    try {
        const url = validateURL(req.body.url);

        const response = await fetch(url, {
            signal: AbortSignal.timeout(5000),
            redirect: "error"
        });

        if (!response.ok) {
            return res.status(502).json({
                message: "Unable to retrieve preview"
            });
        }

        const data = await response.text();

        res.json({
            content: data.slice(0, 10000)
        });
    } catch {
        res.status(400).json({
            message: "Invalid or unavailable URL"
        });
    }
});
```

This is still a simplified example. Production SSRF defenses must also consider DNS rebinding, resolved IP addresses, IPv4/IPv6 representations, proxies and redirects.

The safest approach is to avoid arbitrary server-side URL fetching when it is not necessary.

---

# PART 7: AUTHENTICATION AND AUTHORIZATION SECURITY

# 13. Authentication vs Authorization

These are two different security concepts.

### Authentication

Authentication answers:

**Who are you?**

Example:

A user enters:

```text
Email
Password
```

The application verifies their identity.

### Authorization

Authorization answers:

**What are you allowed to do?**

Example:

A user is authenticated but may not have permission to delete another user's account.

### Comparison

| Feature | Authentication | Authorization |
|---|---|---|
| Purpose | Verify identity | Verify permissions |
| Question | Who are you? | What can you access? |
| Example | Login | Admin-only endpoint |
| Happens | Before access decisions | After identity is established |
| Failure | Authentication failure | Access denied |

---

# 14. Role-Based Access Control (RBAC)

RBAC stands for Role-Based Access Control.

Permissions are assigned to roles, and users receive permissions through their assigned roles.

Example:

```text
Admin
  |
  +-- Create User
  +-- Delete User
  +-- View All Users
  +-- Manage Settings

Manager
  |
  +-- View Team
  +-- Manage Team Records

User
  |
  +-- View Own Profile
  +-- Update Own Profile
```

### Example User Object

```javascript
const user = {
    id: 101,
    email: "user@example.com",
    role: "admin"
};
```

### Express Middleware

```javascript
function authorize(...allowedRoles) {
    return (req, res, next) => {
        if (!req.user) {
            return res.status(401).json({
                message: "Authentication required"
            });
        }

        if (!allowedRoles.includes(req.user.role)) {
            return res.status(403).json({
                message: "Access denied"
            });
        }

        next();
    };
}
```

Usage:

```javascript
app.delete(
    "/api/users/:id",
    authenticate,
    authorize("admin"),
    deleteUser
);
```

### Important HTTP Status Codes

| Status | Meaning |
|---|---|
| 200 | Successful request |
| 201 | Resource created |
| 400 | Invalid request |
| 401 | Authentication required or failed |
| 403 | Authenticated but not authorized |
| 404 | Resource not found |
| 429 | Too many requests |
| 500 | Internal server error |

A system may deliberately return `404` instead of `403` in some situations to avoid revealing that a protected resource exists.

---

# PART 8: PASSWORD SECURITY

# 15. Why Should Passwords Never Be Stored in Plaintext?

Suppose your database contains:

| Email | Password |
|---|---|
| user1@example.com | password123 |
| user2@example.com | mypassword |
| user3@example.com | qwerty123 |

If an attacker obtains the database, they immediately obtain the users' passwords.

This is a serious security failure.

Instead, passwords should be stored using password hashing.

---

# 16. What Is Hashing?

Hashing is a one-way transformation that converts input data into a fixed-format digest.

Example:

```text
Input:
hello

Hash:
A cryptographic digest
```

A secure password hashing system is designed to make password guessing computationally expensive.

### Hashing vs Encryption

| Feature | Hashing | Encryption |
|---|---|---|
| Direction | One-way | Reversible with the key |
| Original Value | Not normally recoverable | Can be decrypted |
| Purpose | Password verification and integrity | Confidentiality |
| Key Required | Not normally for password hashing | Yes |
| Example | Argon2id | AES |

**Important:** Do not encrypt passwords when password hashing is appropriate. Passwords should normally be hashed using a dedicated password hashing algorithm.

---

# 17. Password Hashing Algorithms

Common password hashing algorithms:

- Argon2id.
- bcrypt.
- PBKDF2.

### Argon2id

Argon2id is a memory-hard password hashing algorithm.

It is designed to make large-scale password guessing more expensive.

### bcrypt

bcrypt is a widely used password hashing algorithm with a configurable work factor.

### PBKDF2

PBKDF2 applies repeated cryptographic operations to derive a password hash.

### Comparison

| Algorithm | Characteristics |
|---|---|
| Argon2id | Modern memory-hard option |
| bcrypt | Widely supported and established |
| PBKDF2 | Established, configurable iteration-based algorithm |
| SHA-256 | Not suitable by itself for password storage |

OWASP recommends dedicated password hashing algorithms such as Argon2id, bcrypt and PBKDF2 rather than fast general-purpose hashes such as SHA-256. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html?utm_source=chatgpt.com)

Official reference: [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)

---

# 18. Password Hashing with bcrypt in Node.js

Install:

```bash
npm install bcrypt
```

### Register User

```javascript
const bcrypt = require("bcrypt");

async function registerUser(password) {
    const saltRounds = 12;

    const passwordHash = await bcrypt.hash(
        password,
        saltRounds
    );

    console.log(passwordHash);
}
```

Store `passwordHash` in the database, not the original password.

### Login User

```javascript
async function loginUser(password, storedHash) {
    const isValid = await bcrypt.compare(
        password,
        storedHash
    );

    if (!isValid) {
        throw new Error("Invalid credentials");
    }

    return true;
}
```

### Important

- Use an appropriate work factor.
- Never store plaintext passwords.
- Never log passwords.
- Apply login rate limits.
- Use secure account recovery.
- Consider MFA for sensitive accounts.
- Protect the database and backup files.

For new systems, evaluate Argon2id as well as bcrypt.

---

# PART 9: JWT SECURITY

# 19. What Is JWT?

JWT stands for JSON Web Token.

It is a compact token format commonly used to transfer claims between systems.

A JWT commonly contains:

1. Header.
2. Payload.
3. Signature.

Structure:

```text
HEADER.PAYLOAD.SIGNATURE
```

### Example Payload

```json
{
  "sub": "101",
  "role": "user",
  "iat": 1795622400,
  "exp": 1795626000
}
```

The payload contains claims.

**Important:** A normal signed JWT is not encrypted. Its payload can generally be decoded by anyone who possesses the token.

Never store passwords, private keys or confidential personal information in an ordinary signed JWT payload.

---

# 20. How JWT Authentication Works

```text
User
 |
 v
Login Request
 |
 v
Backend Validates Credentials
 |
 v
Creates Signed JWT
 |
 v
Returns Token
 |
 v
Client Sends Token
 |
 v
Backend Verifies Signature and Claims
 |
 v
Access Granted or Denied
```

### JWT Generation

Install:

```bash
npm install jsonwebtoken
```

Example:

```javascript
const jwt = require("jsonwebtoken");

const token = jwt.sign(
    {
        sub: user.id,
        role: user.role
    },
    process.env.JWT_SECRET,
    {
        expiresIn: "15m",
        issuer: "my-application",
        audience: "my-application-users"
    }
);
```

### JWT Verification

```javascript
const decoded = jwt.verify(
    token,
    process.env.JWT_SECRET,
    {
        algorithms: ["HS256"],
        issuer: "my-application",
        audience: "my-application-users"
    }
);
```

### Important JWT Security Practices

1. Use a strong signing key.
2. Store keys securely.
3. Validate the signature.
4. Validate the expiration claim.
5. Validate issuer and audience when applicable.
6. Explicitly allow expected signing algorithms.
7. Implement token revocation or session invalidation where required.
8. Protect tokens from XSS and leakage.
9. Do not trust user roles supplied outside verified claims.
10. Consider asymmetric signing for distributed systems.

---

# 21. Access Tokens vs Refresh Tokens

An Access Token is used to access protected resources.

A Refresh Token is used to obtain new access tokens without requiring the user to log in again.

| Feature | Access Token | Refresh Token |
|---|---|---|
| Purpose | Access APIs | Renew authentication |
| Lifetime | Usually shorter | Usually longer |
| Usage | Sent to protected APIs | Sent to token endpoint |
| Security Impact | Access to protected resources | Can generate new access tokens |
| Storage | Protected client storage | Strongly protected storage |

### Typical Architecture

```text
User Login
    |
    v
Backend
    |
    +----------------+
    |                |
    v                v
Access Token    Refresh Token
    |                |
    v                v
API Access       Token Renewal
```

### Refresh Token Security

- Store refresh tokens securely.
- Rotate refresh tokens where appropriate.
- Detect reuse of rotated tokens.
- Revoke sessions when necessary.
- Apply expiration.
- Protect refresh endpoints.

For browser applications, secure HttpOnly cookies are commonly used for refresh-token storage, combined with appropriate CSRF protection.

---

# PART 10: HTTPS AND TLS

# 22. What Is HTTPS?

HTTPS stands for Hypertext Transfer Protocol Secure.

It is HTTP communication protected by TLS.

TLS stands for Transport Layer Security.

HTTPS helps protect communication between a client and a server.

### HTTP vs HTTPS

| Feature | HTTP | HTTPS |
|---|---|---|
| Encryption | No TLS encryption | TLS encryption |
| Confidentiality | Not provided by HTTP itself | Protects data in transit |
| Integrity | Not provided by HTTP itself | Protects against undetected modification in transit |
| Server Authentication | No TLS certificate authentication | Certificate-based server authentication |
| Recommended | Not for sensitive production traffic | Yes |

### HTTP Architecture

```text
Browser
   |
   v
HTTP Request
   |
   v
Network
   |
   v
Server
```

Without TLS, the communication is not protected by TLS.

### HTTPS Architecture

```text
Browser
   |
   v
TLS Connection
   |
   v
Encrypted HTTP Data
   |
   v
Server
```

---

# 23. How TLS Works

At a high level:

1. Client connects to the server.
2. Client and server negotiate TLS parameters.
3. Server presents its certificate.
4. Client validates the certificate and hostname.
5. Both sides establish shared session keys.
6. Application data is exchanged securely.

Modern TLS uses cryptographic mechanisms to establish session keys and protect application traffic.

### Important TLS Concepts

| Concept | Purpose |
|---|---|
| Certificate | Binds an identity to a public key |
| Public Key | Used in public-key cryptographic operations |
| Private Key | Secret key controlled by its owner |
| Certificate Authority | Issues and signs certificates |
| Handshake | Establishes secure communication parameters |
| Session Key | Protects the communication data |
| TLS Version | Defines the protocol version |

### Production Best Practices

- Use TLS 1.2 or TLS 1.3 as appropriate.
- Prefer TLS 1.3 where compatible.
- Use valid certificates.
- Renew certificates before expiration.
- Redirect HTTP to HTTPS.
- Avoid mixed content.
- Protect private keys.
- Disable obsolete protocols and weak cipher configurations.

---

# PART 11: SECURITY HEADERS

# 24. What Are HTTP Security Headers?

Security headers are HTTP response headers that instruct browsers to apply additional security protections.

Important headers include:

1. Content-Security-Policy.
2. Strict-Transport-Security.
3. X-Content-Type-Options.
4. Referrer-Policy.
5. Permissions-Policy.
6. Frame-related protections.

## 24.1 Content Security Policy (CSP)

CSP controls which content sources a browser is allowed to load or execute.

Example:

```http
Content-Security-Policy: default-src 'self';
```

This establishes a default policy allowing resources from the same origin.

A production policy needs to account for your application, fonts, scripts, images, APIs and other trusted resources.

Example:

```http
Content-Security-Policy:
  default-src 'self';
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none';
```

### Important

Do not blindly copy CSP policies into production.

A strict policy may break legitimate application functionality.

Use nonces or hashes where appropriate and test the policy before enforcing it.

## 24.2 Strict-Transport-Security (HSTS)

HSTS instructs browsers to use HTTPS for a domain for a specified period.

Example:

```http
Strict-Transport-Security: max-age=31536000
```

Only enable HSTS after HTTPS is correctly configured.

Be especially careful with `includeSubDomains` and preload settings.

## 24.3 X-Content-Type-Options

```http
X-Content-Type-Options: nosniff
```

This tells browsers not to perform certain MIME-type sniffing behaviors.

## 24.4 Referrer-Policy

```http
Referrer-Policy: strict-origin-when-cross-origin
```

Controls how much referrer information is sent with requests.

## 24.5 Frame Protection

A modern approach is:

```http
Content-Security-Policy: frame-ancestors 'none';
```

This prevents the page from being embedded in frames, subject to the policy's scope.

### Header Summary

| Header | Purpose |
|---|---|
| CSP | Restricts resource loading and script execution |
| HSTS | Enforces HTTPS in supporting browsers |
| X-Content-Type-Options | Reduces MIME-sniffing risks |
| Referrer-Policy | Controls referrer information |
| Permissions-Policy | Controls access to selected browser features |
| frame-ancestors | Controls who can embed a page |

---

# PART 12: CORS SECURITY

# 25. What Is CORS?

CORS stands for Cross-Origin Resource Sharing.

It is a browser security mechanism that controls whether a web page from one origin can access resources from another origin.

An origin consists of:

```text
Protocol + Host + Port
```

Example:

```text
https://frontend.example.com
```

Another origin:

```text
https://api.example.com
```

These are different origins because their hosts differ.

### Same-Origin vs Cross-Origin

| URL 1 | URL 2 | Same Origin? |
|---|---|---|
| `https://example.com/a` | `https://example.com/b` | Yes |
| `https://example.com` | `http://example.com` | No |
| `https://example.com` | `https://api.example.com` | No |
| `https://example.com` | `https://example.com:3000` | No |

### How CORS Works

```text
Frontend
    |
    v
Request to API
    |
    v
Browser Checks CORS Policy
    |
    v
API Response Headers
    |
    v
Browser Evaluates Permission
    |
    v
Response Available to JavaScript?
```

CORS is primarily enforced by browsers.

It is not a replacement for backend authentication or authorization.

---

# 26. Configure CORS in Express

Install:

```bash
npm install cors
```

### Incorrect Configuration

```javascript
app.use(cors({
    origin: "*",
    credentials: true
}));
```

This is invalid for credentialed browser requests because wildcard origins cannot be used with credentials.

### Better Configuration

```javascript
const cors = require("cors");

const allowedOrigins = [
    "https://myfrontend.com",
    "https://admin.myfrontend.com"
];

app.use(cors({
    origin: function (origin, callback) {
        if (!origin || allowedOrigins.includes(origin)) {
            return callback(null, true);
        }

        return callback(new Error("Origin not allowed"));
    },

    credentials: true
}));
```

For production, consider whether requests without an Origin header should be accepted. Non-browser clients may not send Origin, so CORS alone should never be used as an access-control mechanism.

---

# PART 13: API SECURITY

# 27. What Is API Security?

API Security means protecting APIs against unauthorized access, data exposure, abuse and attacks.

Common API security risks include:

- Broken Object Level Authorization.
- Broken Authentication.
- Excessive data exposure.
- Rate-limit bypass.
- Injection.
- SSRF.
- Improper input validation.
- Misconfigured CORS.
- Insecure API keys.

### Example API

```text
POST /api/login
GET  /api/users
GET  /api/users/:id
POST /api/expenses
PUT  /api/expenses/:id
DELETE /api/expenses/:id
```

Each endpoint needs appropriate authentication, authorization and validation.

---

# 28. Input Validation

Never trust user input.

Suppose your API expects:

```json
{
  "name": "Riyaz",
  "email": "user@example.com",
  "age": 25
}
```

Validate:

- Data types.
- Required fields.
- Maximum lengths.
- Allowed values.
- Formats.
- Business rules.

### Example with Zod

Install:

```bash
npm install zod
```

Create schema:

```javascript
const { z } = require("zod");

const userSchema = z.object({
    name: z.string().min(2).max(100),

    email: z.string().email(),

    age: z.number().int().min(18).max(120)
});
```

Validate:

```javascript
app.post("/api/users", (req, res) => {
    const result = userSchema.safeParse(req.body);

    if (!result.success) {
        return res.status(400).json({
            message: "Invalid request",
            errors: result.error.issues
        });
    }

    const userData = result.data;

    res.json({
        message: "Valid request",
        user: userData
    });
});
```

### Why Is Validation Important?

It helps prevent:

- Invalid data.
- Unexpected application behavior.
- Type confusion.
- Excessive input sizes.
- Invalid business operations.

**Important:** Validation is not a substitute for SQL parameterization, output encoding or authorization.

---

# 29. Rate Limiting

Rate Limiting restricts how many requests a client can make within a specified period.

Example:

```text
Maximum:
100 requests

Time:
1 minute
```

If the client exceeds the limit, the application can return:

```http
429 Too Many Requests
```

### Why Is Rate Limiting Important?

It helps protect against:

- Brute-force login attempts.
- API abuse.
- Excessive automated requests.
- Resource exhaustion.
- Some forms of denial-of-service activity.

### Express Rate Limiting

Install:

```bash
npm install express-rate-limit
```

Example:

```javascript
const rateLimit = require("express-rate-limit");

const loginLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,

    limit: 5,

    standardHeaders: "draft-8",

    legacyHeaders: false,

    message: {
        message: "Too many login attempts. Try again later."
    }
});

app.use("/api/login", loginLimiter);
```

For production systems with multiple application instances, use an appropriate shared store rather than relying only on each process's in-memory counters.

Also consider account-based throttling, IP-based throttling, device signals and abuse detection.

---

# 30. Request Size Limits

Attackers may send extremely large request bodies.

This can cause resource exhaustion.

Example:

```javascript
app.use(express.json({
    limit: "100kb"
}));
```

The limit should be appropriate for the application.

For file uploads, configure separate limits for:

- File size.
- File count.
- Allowed file types.
- Upload duration.
- Storage capacity.

---

# PART 14: SECURE FILE UPLOADS

# 31. Why Are File Uploads Dangerous?

File upload functionality introduces several security risks.

Examples:

- Executable files.
- Malicious documents.
- Oversized files.
- Path traversal.
- Malicious filenames.
- Stored XSS through uploaded content.
- Unauthorized file access.

### Incorrect Approach

Accepting every file without validation:

```javascript
app.post("/upload", upload.single("file"), (req, res) => {
    res.json({
        message: "Uploaded"
    });
});
```

The upload middleware and storage configuration need proper restrictions.

### Security Best Practices

1. Allow only required file types.
2. Limit file size.
3. Generate server-side filenames.
4. Do not trust the original filename.
5. Validate file content, not only extension.
6. Store files outside executable application directories.
7. Restrict access to private files.
8. Scan uploads where appropriate.
9. Prevent path traversal.
10. Use safe download headers.

### Important

Checking only:

```javascript
file.originalname.endsWith(".pdf")
```

is not sufficient.

A malicious file can have a misleading extension.

Validate file content using appropriate file-type detection and, where applicable, antivirus or content-scanning tools.

---

# PART 15: SECRETS MANAGEMENT

# 32. What Are Secrets?

Secrets are sensitive values used by applications and infrastructure.

Examples:

```text
DATABASE_PASSWORD
JWT_SECRET
AWS_ACCESS_KEY
API_KEY
SMTP_PASSWORD
PRIVATE_KEY
```

### Incorrect Approach

Hardcoding secrets:

```javascript
const dbPassword = "my-secret-password";
```

This creates the risk of accidental exposure through source control, logs or application artifacts.

### Better Approach

Use environment variables:

```javascript
const dbPassword = process.env.DB_PASSWORD;
```

Store the value outside the source code.

For local development:

```text
.env
```

Add it to `.gitignore`:

```gitignore
.env
.env.local
.env.production
```

### Important

A `.gitignore` rule does not remove a file that has already been committed.

If a real secret has been committed:

1. Revoke or rotate it immediately.
2. Investigate whether it was accessed.
3. Remove it from active code.
4. Clean repository history where appropriate.
5. Review logs and affected systems.

---

# 33. Secrets Management Tools

Common solutions include:

| Tool | Purpose |
|---|---|
| AWS Secrets Manager | Store and manage application secrets |
| AWS Systems Manager Parameter Store | Store configuration and secrets |
| HashiCorp Vault | Centralized secrets management |
| GitHub Secrets | Store CI/CD secrets |
| Kubernetes Secrets | Store sensitive Kubernetes configuration |
| Azure Key Vault | Manage Azure secrets, keys and certificates |

### Application Architecture

```text
Application
    |
    v
Secrets Manager
    |
    v
Retrieve Required Secret
    |
    v
Connect to Database
```

Avoid storing permanent credentials in source code or container images.

Prefer workload identities and temporary credentials where supported.

---

# PART 16: SAST, DAST AND SCA

# 34. What Is SAST?

SAST stands for Static Application Security Testing.

It analyzes application source code or related artifacts without requiring the application to be running.

Example:

```text
Source Code
     |
     v
SAST Scanner
     |
     v
Security Analysis
     |
     v
Potential Vulnerabilities
```

SAST can identify:

- Hardcoded secrets.
- Dangerous function usage.
- Some injection risks.
- Insecure coding patterns.
- Certain authorization mistakes.

### Common SAST Tools

- GitHub CodeQL.
- Semgrep.
- SonarQube.
- Checkmarx.

### Advantages

- Can run early in development.
- Finds issues before deployment.
- Integrates into CI pipelines.

### Limitations

- May produce false positives.
- Cannot always understand runtime behavior.
- Cannot guarantee that an application is secure.

---

# 35. What Is DAST?

DAST stands for Dynamic Application Security Testing.

It tests an application while the application is running.

Example:

```text
Running Application
        |
        v
DAST Scanner
        |
        v
Send Test Requests
        |
        v
Analyze Responses
        |
        v
Security Findings
```

DAST can help identify:

- XSS.
- Authentication problems.
- Misconfigurations.
- Some injection vulnerabilities.
- Exposed endpoints.

### Common DAST Tools

- OWASP ZAP.
- Burp Suite.
- Invicti.

### Important

Only run active security scans against systems you own or are explicitly authorized to test.

---

# 36. What Is SCA?

SCA stands for Software Composition Analysis.

It analyzes third-party dependencies and components used by an application.

Suppose your project has:

```text
Next.js Application
       |
       v
package.json
       |
       +---- React
       |
       +---- Axios
       |
       +---- Express
       |
       +---- Other Packages
```

SCA identifies dependencies and checks for known vulnerabilities.

### Common SCA Tools

- npm audit.
- Dependabot.
- Snyk.
- OWASP Dependency-Check.
- GitHub dependency review.

OWASP Dependency-Check is designed to identify publicly disclosed vulnerabilities in project dependencies. [OWASP Foundation](https://owasp.org/projects/dependency-check?utm_source=chatgpt.com)

---

# 37. SAST vs DAST vs SCA

| Feature | SAST | DAST | SCA |
|---|---|---|---|
| Full Form | Static Application Security Testing | Dynamic Application Security Testing | Software Composition Analysis |
| Target | Source code | Running application | Third-party dependencies |
| Requires Running Application | No | Yes | No |
| Finds | Insecure coding patterns | Runtime security problems | Known dependency vulnerabilities |
| Example Tool | CodeQL | OWASP ZAP | npm audit |

### Simple Understanding

```text
SAST:
Check how you wrote the code.

DAST:
Check how the running application behaves.

SCA:
Check which third-party packages you use.
```

A mature DevSecOps pipeline uses multiple testing methods because they detect different classes of problems.

---

# PART 17: AUTOMATED SECURITY SCANNING

# 38. npm audit

For Node.js projects, npm provides a dependency vulnerability auditing command.

Run:

```bash
npm audit
```

It checks dependencies against known vulnerability information.

To attempt remediation:

```bash
npm audit fix
```

Be careful with:

```bash
npm audit fix --force
```

It may introduce breaking dependency upgrades.

Review the changes, run tests and verify compatibility before accepting them.

### Why Is npm audit Important?

Suppose your application uses:

```text
express
axios
jsonwebtoken
```

One dependency may have a publicly disclosed vulnerability.

Dependency auditing helps identify known issues.

**Important:** A clean `npm audit` result does not prove that your application is secure. It only reports the issues detectable through its available vulnerability data and analysis.

---

# 39. GitHub Code Scanning

GitHub Code Scanning can integrate static analysis into your repository.

One option is CodeQL.

CodeQL analyzes source code to identify security vulnerabilities and coding errors.

Official resource: [GitHub Code Security](https://docs.github.com/en/code-security)

Typical workflow:

```text
Developer Push
      |
      v
GitHub Actions
      |
      v
CodeQL Analysis
      |
      v
Security Findings
      |
      v
Developer Reviews Results
```

A CodeQL workflow should use a current, supported action version and suitable permissions for your repository configuration.

---

# PART 18: DOCKER CONTAINER SECURITY

# 40. Why Is Container Security Important?

Docker containers package applications and dependencies into images.

A container image may contain:

- Operating system packages.
- Runtime.
- Application code.
- Dependencies.
- Configuration.

If any component is vulnerable or incorrectly configured, the container may introduce security risks.

### Common Container Security Risks

1. Running containers as root.
2. Using vulnerable base images.
3. Including unnecessary packages.
4. Exposing secrets in Docker images.
5. Using privileged containers.
6. Mounting sensitive host directories.
7. Using untrusted images.
8. Failing to scan images.

---

# 41. Run Docker Containers as Non-Root Users

### Incorrect Dockerfile

```dockerfile
FROM node:22

WORKDIR /app

COPY . .

RUN npm install

CMD ["node", "server.js"]
```

Depending on the base image and configuration, the application may run with excessive privileges.

### Better Dockerfile

```dockerfile
FROM node:22-bookworm-slim

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY --chown=node:node . .

USER node

EXPOSE 3000

CMD ["node", "server.js"]
```

### Explanation

```dockerfile
USER node
```

Runs the application as the non-root `node` user.

This reduces the impact of certain application compromises.

For production, also consider:

- Multi-stage builds.
- Minimal base images.
- Image scanning.
- Read-only root filesystems where possible.
- Dropping unnecessary Linux capabilities.
- Avoiding secrets in image layers.
- Using image digests for deployment.

---

# 42. Docker Secrets and Environment Variables

Never put credentials directly into a Dockerfile.

### Incorrect

```dockerfile
ENV DB_PASSWORD=my-password
```

This may expose credentials through image metadata or layers.

### Better

Provide secrets at runtime through an appropriate secrets-management mechanism.

For example, during local development:

```bash
docker run \
  --env-file .env \
  my-app
```

For production, prefer a platform-supported secrets integration rather than copying a `.env` file into an image.

### Important

Environment variables are not automatically secret. They may be visible to privileged processes, diagnostics or container inspection tools.

Use appropriate file-based or secrets-provider integrations where stronger isolation is required.

---

# PART 19: KUBERNETES SECURITY

# 43. Kubernetes Security Fundamentals

You learned Kubernetes architecture and deployment in Phase 46.

Now let's understand Kubernetes security.

Important areas:

1. Authentication.
2. Authorization.
3. RBAC.
4. Secrets.
5. Network Policies.
6. Pod Security Standards.
7. Container Security Context.
8. Image Security.
9. Audit Logging.

Official resource: [Kubernetes Security](https://kubernetes.io/docs/concepts/security/)

---

# 44. Kubernetes RBAC

RBAC stands for Role-Based Access Control.

It controls which users and service accounts can perform which actions on Kubernetes resources.

Example:

```text
Developer
   |
   v
Kubernetes API
   |
   v
RBAC Authorization
   |
   v
Check Permissions
   |
   v
Allow or Deny
```

### Role Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1

kind: Role

metadata:
  namespace: development
  name: pod-reader

rules:
  - apiGroups:
      - ""

    resources:
      - pods

    verbs:
      - get
      - list
      - watch
```

This Role permits the specified operations on Pods in the `development` namespace.

### RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1

kind: RoleBinding

metadata:
  name: pod-reader-binding
  namespace: development

subjects:
  - kind: User
    name: developer@example.com
    apiGroup: rbac.authorization.k8s.io

roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

The RoleBinding grants the Role's permissions to the specified user.

### Important

Use:

- Roles for namespace-scoped permissions.
- ClusterRoles for cluster-scoped or reusable permissions.
- RoleBindings for namespace-scoped assignments.
- ClusterRoleBindings for cluster-wide assignments.

Avoid granting `cluster-admin` to ordinary application users or workloads.

---

# 45. Kubernetes Secrets

Kubernetes Secrets store sensitive configuration.

Example:

```yaml
apiVersion: v1

kind: Secret

metadata:
  name: database-secret

type: Opaque

stringData:
  DB_USER: app_user
  DB_PASSWORD: replace-with-a-secret
```

Reference it in a Pod:

```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: database-secret
        key: DB_USER

  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: database-secret
        key: DB_PASSWORD
```

### Important Security Points

- Base64 is encoding, not encryption.
- Enable encryption at rest for Secret data.
- Restrict access through RBAC.
- Avoid committing actual Secret values to Git.
- Consider external secret-management systems.
- Avoid mounting service-account tokens into Pods that do not need them.

Kubernetes recommends protecting Secret data, restricting access and applying encryption at rest. [Kubernetes](https://kubernetes.io/docs/concepts/security/security-checklist/?utm_source=chatgpt.com)

---

# 46. Pod Security Standards

Kubernetes defines three Pod Security Standards:

1. Privileged.
2. Baseline.
3. Restricted.

| Standard | Purpose |
|---|---|
| Privileged | Allows unrestricted Pod configurations |
| Baseline | Prevents common privilege-escalation configurations |
| Restricted | Applies stronger Pod hardening requirements |

For application workloads, Restricted is a useful target when compatible with the application.

Official resource: [Kubernetes Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)

### Example Security Context

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: secure-pod

spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    seccompProfile:
      type: RuntimeDefault

  containers:
    - name: app
      image: nginx:stable

      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop:
            - ALL
```

This is a hardening example. The chosen image and application must support running with a read-only filesystem and the specified user.

### Important Fields

| Field | Purpose |
|---|---|
| `runAsNonRoot` | Prevents the container from running as root |
| `runAsUser` | Specifies the user ID |
| `allowPrivilegeEscalation` | Controls privilege escalation |
| `readOnlyRootFilesystem` | Makes the container root filesystem read-only |
| `capabilities.drop` | Removes Linux capabilities |
| `seccompProfile` | Applies a syscall restriction profile |

---

# 47. Kubernetes Network Policies

Network Policies control which Pods can communicate with other Pods or network destinations.

Example:

```yaml
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:
  name: backend-network-policy
  namespace: production

spec:
  podSelector:
    matchLabels:
      app: backend

  policyTypes:
    - Ingress

  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend

      ports:
        - protocol: TCP
          port: 3000
```

This example permits selected frontend Pods to access the backend Pods on TCP port 3000.

### Important

NetworkPolicy enforcement depends on the cluster's networking implementation.

Creating a NetworkPolicy does not guarantee that traffic will be restricted if the CNI implementation does not support it.

Kubernetes recommends restricting workload network access, applying appropriate Pod security and avoiding unnecessary privileges. [Kubernetes](https://kubernetes.io/docs/concepts/security/security-checklist/?utm_source=chatgpt.com)

---

# PART 20: AWS CLOUD SECURITY

# 48. AWS IAM Security

IAM stands for Identity and Access Management.

It controls access to AWS resources.

IAM allows you to define:

- Users.
- Groups.
- Roles.
- Policies.
- Permissions.
- Trust relationships.

### Example

Suppose a Node.js application needs access to S3.

You should grant it only the permissions it requires.

Avoid giving the application full administrator access.

### IAM Best Practices

1. Apply least privilege.
2. Use temporary credentials for workloads.
3. Enable MFA for human users.
4. Protect the root account.
5. Review unused credentials.
6. Use IAM roles instead of hardcoded credentials.
7. Audit permissions.
8. Use IAM Access Analyzer.
9. Restrict cross-account access.
10. Monitor suspicious activity.

AWS specifically recommends temporary credentials, MFA and least-privilege permissions. [Amazon Web Services, Inc.](https://aws.amazon.com/iam/resources/best-practices/?utm_source=chatgpt.com)

Official resource: [AWS IAM Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

# 49. AWS Security Groups

Security Groups act as virtual firewalls for supported AWS resources.

They control inbound and outbound traffic.

Example:

```text
EC2 Security Group
       |
       +---- HTTP  : 80
       |
       +---- HTTPS : 443
       |
       +---- SSH   : 22
```

### Insecure Configuration

Allowing SSH from everywhere:

```text
Port: 22
Source: 0.0.0.0/0
```

This unnecessarily exposes SSH to the internet.

### Better Configuration

Restrict SSH access to trusted IP ranges or use an appropriate administrative access solution such as AWS Systems Manager Session Manager.

### Recommended Rules

| Port | Purpose | Access |
|---|---|---|
| 80 | HTTP | Public only when required |
| 443 | HTTPS | Public when hosting a public website |
| 22 | SSH | Restricted administrative access |
| 3306 | MySQL | Only required application or database network sources |
| 5432 | PostgreSQL | Only required application or database network sources |

Do not expose database ports publicly unless there is a carefully justified requirement and additional security controls.

---

# 50. AWS RDS Security

For a production database:

- Use private subnets.
- Disable public accessibility unless necessary.
- Restrict security group access.
- Encrypt storage.
- Require TLS for database connections where supported.
- Use strong authentication.
- Store credentials in Secrets Manager.
- Enable backups.
- Monitor database activity.
- Restrict administrative access.

### Recommended Architecture

```text
Internet
   |
   v
Application Load Balancer
   |
   v
Application Server / EKS
   |
   v
Private Network
   |
   v
Private RDS Database
```

The database should not normally be directly accessible from the public internet.

---

# PART 21: CI/CD PIPELINE SECURITY

# 51. Why Must CI/CD Pipelines Be Secured?

CI/CD systems often have access to sensitive resources.

For example:

```text
GitHub Actions
     |
     +---- Source Code
     |
     +---- Docker Registry
     |
     +---- AWS Account
     |
     +---- Kubernetes Cluster
     |
     +---- Production Server
```

A compromised pipeline can become a route into production infrastructure.

### Important CI/CD Security Practices

- Use least-privilege workflow permissions.
- Pin third-party Actions to verified full commit SHAs.
- Restrict secrets.
- Use OIDC for cloud authentication.
- Protect production environments.
- Separate build and deployment permissions.
- Avoid exposing secrets to untrusted pull requests.
- Isolate self-hosted runners.
- Scan dependencies and images.
- Review deployment artifacts.
- Audit workflow changes.

These controls are consistent with GitHub and OWASP guidance for securing automation workflows. [OWASP Foundation](https://owasp.github.io/DevSecOpsGuideline/2-Process/2-2-Develop/2-2-1-Pre-commit/2-2-1-4-Repository-Hardening/?utm_source=chatgpt.com)

---

# 52. Add Security Scanning to GitHub Actions

Let's create a basic security pipeline for a Node.js application.

Create:

```text
.github/workflows/security.yml
```

Example:

```yaml
name: Security Pipeline

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

permissions:
  contents: read

jobs:
  dependency-audit:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4

        with:
          node-version: 22
          cache: npm

      - name: Install Dependencies
        run: npm ci

      - name: Run Dependency Audit
        run: npm audit --audit-level=high
```

This workflow checks the project's npm dependency vulnerability report.

### Important

- A failing audit should be investigated rather than automatically bypassed.
- Some vulnerabilities may require major dependency changes.
- A successful audit does not prove that the application is secure.
- Use SAST and DAST in addition to dependency auditing.

---

# 53. Add CodeQL to GitHub Actions

CodeQL can perform static code analysis.

A typical workflow structure looks like this:

```yaml
name: CodeQL Security Analysis

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  schedule:
    - cron: "30 2 * * 1"

permissions:
  contents: read
  security-events: write

jobs:
  analyze:
    runs-on: ubuntu-latest

    permissions:
      contents: read
      security-events: write
      actions: read

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3

        with:
          languages: javascript-typescript

      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3
```

This is an illustrative workflow. Confirm the current supported action versions, required permissions and repository settings before using it in production.

---

# PART 22: SECURITY MONITORING

# 54. What Is Security Monitoring?

Security Monitoring is the continuous observation of systems to identify suspicious activity, security incidents and potential attacks.

Examples:

- Multiple failed login attempts.
- Unusual API traffic.
- Unauthorized access attempts.
- Unexpected administrative actions.
- Unusual database activity.
- Suspicious network connections.
- Unexpected changes to infrastructure.

### Monitoring Architecture

```text
Application
    |
    v
Application Logs
    |
    v
Centralized Logging
    |
    v
Security Monitoring
    |
    v
Alerting System
    |
    v
Security Team
```

### Common Tools

| Tool | Purpose |
|---|---|
| AWS CloudWatch | AWS monitoring and logs |
| Prometheus | Metrics collection |
| Grafana | Metrics visualization |
| ELK Stack | Log analysis |
| OpenSearch | Search and analytics |
| Wazuh | Security monitoring |
| Splunk | Security information and event analysis |

---

# 55. What Is SIEM?

SIEM stands for Security Information and Event Management.

A SIEM platform collects and analyzes security-related information from multiple systems.

For example:

```text
Application Logs
       |
       v
Server Logs
       |
       v
Firewall Logs
       |
       v
Cloud Logs
       |
       v
       SIEM
       |
       v
Correlation and Analysis
       |
       v
Security Alerts
```

### What Does SIEM Do?

1. Collects logs.
2. Normalizes events.
3. Correlates related activity.
4. Identifies suspicious patterns.
5. Generates alerts.
6. Supports incident investigations.

### Example

Suppose the SIEM detects:

```text
10 failed login attempts
From the same IP
Within 30 seconds
```

It may generate a suspicious-login alert.

SIEM does not automatically stop every attack. It helps security teams detect and investigate suspicious activity.

---

# PART 23: INCIDENT RESPONSE

# 56. What Is a Security Incident?

A security incident is an event that compromises or threatens the confidentiality, integrity or availability of a system or its data.

Examples:

- Database breach.
- Stolen credentials.
- Ransomware infection.
- Unauthorized server access.
- Compromised Docker image.
- Exposed API key.
- Unauthorized data modification.

---

# 57. Incident Response Lifecycle

A common incident-response lifecycle includes:

1. Preparation.
2. Detection and Analysis.
3. Containment.
4. Eradication.
5. Recovery.
6. Lessons Learned.

### Architecture

```text
       Preparation
            |
            v
   Detection and Analysis
            |
            v
        Containment
            |
            v
        Eradication
            |
            v
          Recovery
            |
            v
      Lessons Learned
            |
            +-------> Preparation
```

### Example: Database Credential Leak

Suppose a developer accidentally commits a database password to GitHub.

#### Step 1 – Detection

Identify the exposed credential.

#### Step 2 – Containment

Immediately revoke or rotate the credential.

#### Step 3 – Investigation

Check:

- Database login history.
- Network activity.
- Access logs.
- Data modifications.
- Other exposed secrets.

#### Step 4 – Eradication

Remove the compromised credential from active configuration and fix the source of exposure.

#### Step 5 – Recovery

Deploy new credentials and verify database connectivity.

#### Step 6 – Lessons Learned

Improve:

- Secret scanning.
- Repository policies.
- Access permissions.
- Developer training.
- Incident response procedures.

**Important:** Deleting a secret from Git history does not make the old credential safe. Always revoke or rotate exposed credentials first.

---

# PART 24: COMPLETE DEVSECOPS PROJECT

# 58. Project – Secure Full-Stack Application

Let's combine your knowledge of:

- Next.js.
- Node.js.
- Express.
- MySQL.
- Docker.
- GitHub Actions.
- AWS.
- Kubernetes.

We will design a secure full-stack application.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + Express |
| Database | MySQL RDS |
| Authentication | JWT or secure sessions |
| Password Hashing | Argon2id or bcrypt |
| Validation | Zod |
| Security Headers | Helmet |
| Rate Limiting | express-rate-limit |
| Containerization | Docker |
| CI/CD | GitHub Actions |
| SAST | CodeQL |
| SCA | npm audit / Dependabot |
| DAST | OWASP ZAP |
| Image Scanning | Trivy |
| Infrastructure | AWS |
| Orchestration | Kubernetes |
| Monitoring | CloudWatch / Prometheus |

---

# 59. Recommended Project Structure

```text
secure-fullstack-app/
    frontend/
        app/
        components/
        lib/
        package.json

    backend/
        src/
            controllers/
            routes/
            middleware/
            services/
            validators/
            config/

        tests/
        package.json
        Dockerfile

    deployment/
        kubernetes/
            deployment.yaml
            service.yaml
            network-policy.yaml

    .github/
        workflows/
            ci.yml
            security.yml
            docker.yml
            deploy.yml

    .gitignore
    README.md
```

---

# 60. Secure Express Application Configuration

Install:

```bash
npm install express helmet cors express-rate-limit zod
```

Example:

```javascript
const express = require("express");

const helmet = require("helmet");

const cors = require("cors");

const rateLimit = require("express-rate-limit");

const app = express();

app.use(helmet());

app.use(express.json({
    limit: "100kb"
}));

const allowedOrigins = [
    "https://frontend.example.com"
];

app.use(cors({
    origin: allowedOrigins,
    credentials: true
}));

const apiLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    limit: 100,
    standardHeaders: "draft-8",
    legacyHeaders: false
});

app.use("/api", apiLimiter);

app.get("/health", (req, res) => {
    res.json({
        status: "healthy"
    });
});

app.listen(3000, () => {
    console.log("Server running");
});
```

### What Does This Configuration Provide?

| Configuration | Purpose |
|---|---|
| Helmet | Adds commonly recommended HTTP security headers |
| JSON Body Limit | Restricts request body size |
| CORS | Controls browser cross-origin access |
| Rate Limiter | Limits request frequency |
| Health Endpoint | Provides an application health check |

### Production Considerations

This is a starting configuration, not a complete security solution.

You still need:

- Authentication.
- Authorization.
- Input validation.
- CSRF protection where applicable.
- Secure session handling.
- Database protection.
- Logging.
- Error handling.
- HTTPS.
- Network security.

---

# 61. Secure Database Access

Use parameterized queries.

Example with MySQL:

```javascript
const [users] = await db.execute(
    `
    SELECT id, email, name
    FROM users
    WHERE email = ?
    `,
    [email]
);
```

Do not construct SQL using untrusted input.

Use a database account with only the permissions required by the application.

---

# 62. Complete DevSecOps Pipeline Architecture

```text
                       Developer
                           |
                           v
                     GitHub Repository
                           |
                           v
                     GitHub Actions
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          SAST Scan     SCA Scan     Unit Tests
             |             |             |
             +-------------+-------------+
                           |
                           v
                    Application Build
                           |
                           v
                     Docker Build
                           |
                           v
                    Container Scan
                           |
                           v
                      Amazon ECR
                           |
                           v
                    Deployment Stage
                           |
                           v
                       AWS EKS
                           |
                           v
                  Runtime Security
                           |
                           v
                  Security Monitoring
                           |
                           v
                    Alerting System
```

### Pipeline Security Gates

| Stage | Security Check |
|---|---|
| Pull Request | Code review and SAST |
| Dependency Installation | SCA |
| Build | Secure build configuration |
| Docker Build | Image scanning |
| Image Publishing | Registry permissions and provenance |
| Deployment | RBAC and environment approval |
| Runtime | Network policies and Pod security |
| Monitoring | Logging and alerting |

A security gate can prevent deployment when a policy-defined critical vulnerability is found.

However, severity thresholds, false positives, exceptions and remediation deadlines should be managed through a documented security policy.

---

# PART 25: INTERVIEW QUESTIONS AND ANSWERS

# 63. Basic Interview Questions

### Q1. What is DevSecOps?

DevSecOps integrates security practices into development and operations processes throughout the software lifecycle.

### Q2. What is OWASP?

OWASP is a nonprofit organization that provides resources, tools and standards for improving application security.

### Q3. What is the OWASP Top 10?

It is a standard awareness document describing major web application security risk categories.

### Q4. What is SQL Injection?

SQL Injection occurs when untrusted input changes the intended structure or behavior of a SQL query.

### Q5. What is XSS?

Cross-Site Scripting occurs when untrusted content is handled in a way that allows executable script content to run in a user's browser.

### Q6. What is CSRF?

CSRF is an attack that tricks an authenticated user's browser into submitting an unwanted request to an application.

### Q7. What is SSRF?

SSRF occurs when an attacker manipulates a server into making requests to unintended destinations.

### Q8. What is authentication?

Authentication verifies the identity of a user or system.

### Q9. What is authorization?

Authorization determines which resources or actions an authenticated identity is permitted to access.

### Q10. What is hashing?

Hashing is a one-way transformation that produces a digest from input data.

---

# 64. Intermediate Interview Questions

### Q11. What is the difference between hashing and encryption?

Hashing is designed to be one-way, whereas encryption is designed to be reversible using the appropriate key.

### Q12. Why should passwords be hashed?

Password hashing helps protect stored credentials if the password database is compromised.

### Q13. What is bcrypt?

bcrypt is a password hashing algorithm designed to make password guessing computationally expensive.

### Q14. What is JWT?

JWT is a compact token format used to represent claims between systems.

### Q15. Is JWT encrypted?

Not necessarily. A signed JWT normally provides integrity and authenticity, but its payload is not confidential.

### Q16. What is HTTPS?

HTTPS is HTTP communication protected by TLS.

### Q17. What is CORS?

CORS is a browser mechanism that controls whether a web page from one origin can access resources from another origin.

### Q18. What is CSP?

Content Security Policy is a browser security mechanism that restricts permitted content sources and script execution.

### Q19. What is rate limiting?

Rate limiting restricts the number of requests a client can make within a defined period.

### Q20. What is SAST?

SAST analyzes source code or related artifacts to identify potential security vulnerabilities without requiring the application to be running.

---

# 65. Advanced Interview Questions

### Q21. What is the difference between SAST, DAST and SCA?

- SAST analyzes source code.
- DAST analyzes a running application.
- SCA analyzes third-party dependencies.

### Q22. How would you prevent SQL Injection in Node.js?

I would use parameterized queries, ORM query methods and strict allow-lists for dynamic SQL structures. I would avoid concatenating untrusted input into SQL.

### Q23. How would you prevent XSS in React?

I would use React's normal escaped rendering, avoid unsafe DOM operations, sanitize HTML when necessary and apply an appropriate Content Security Policy.

### Q24. How would you protect an API against brute-force attacks?

I would implement rate limiting, authentication controls, monitoring, progressive delays and appropriate account protection.

### Q25. Why is storing JWTs in localStorage risky?

JavaScript running in the same origin can access localStorage. If an XSS vulnerability exists, an attacker may steal tokens stored there.

### Q26. What is the difference between 401 and 403?

401 indicates that authentication is missing or invalid.

403 indicates that the request is not authorized.

### Q27. How would you secure a Docker container?

I would use trusted minimal base images, run as a non-root user, scan images, avoid embedding secrets, drop unnecessary capabilities and restrict filesystem access.

### Q28. How would you secure Kubernetes?

I would implement RBAC, Pod Security Standards, Network Policies, Secret protection, image scanning, audit logging and least-privilege service accounts.

### Q29. What is the principle of least privilege?

It means giving an identity only the permissions necessary to perform its required tasks.

### Q30. What is a security incident?

A security incident is an event that compromises or threatens system confidentiality, integrity or availability.

---

# 66. Scenario-Based Interview Questions

### Q31. A developer accidentally commits a database password to GitHub. What should you do?

1. Immediately revoke or rotate the credential.
2. Investigate potential unauthorized access.
3. Review database and application logs.
4. Remove the secret from active configuration.
5. Clean repository history where appropriate.
6. Add secret scanning and prevention controls.

### Q32. Your application is vulnerable to SQL Injection. How would you fix it?

I would review unsafe query construction, replace string concatenation with parameterized queries, validate dynamic query structures using allow-lists and add regression tests.

### Q33. A user can access another user's expenses by changing the ID in the URL. What is the vulnerability?

This is a Broken Access Control vulnerability, commonly described as IDOR-style access.

I would enforce resource ownership on the backend.

### Q34. Your API is receiving thousands of requests per minute. What would you do?

I would investigate traffic patterns, implement rate limiting, use appropriate load balancing, apply abuse detection and scale infrastructure where necessary.

### Q35. How would you integrate security into GitHub Actions?

I would include:

- Static analysis.
- Dependency scanning.
- Secret scanning.
- Automated tests.
- Container image scanning.
- Least-privilege workflow permissions.
- Protected deployment environments.
- OIDC authentication.

### Q36. A Docker image contains a critical vulnerability. What would you do?

I would identify the vulnerable component, update the base image or dependency, rebuild the image, scan it again and prevent the vulnerable image from reaching production according to the organization's security policy.

### Q37. A Kubernetes Pod is running as root. Why is that dangerous?

A compromised process may have more privileges than necessary. I would assess whether root is required and configure the workload to run as a non-root user with restricted capabilities.

### Q38. Your application exposes database credentials in logs. What should you do?

I would rotate the exposed credentials, restrict log access, remove secret logging, investigate possible misuse and add tests or controls to prevent recurrence.

---

# PART 26: PRACTICAL ASSIGNMENTS

# 67. Assignment 1 – Understand OWASP Top 10

Study the OWASP Top 10:2025.

For each category:

1. Understand its meaning.
2. Identify a real-world example.
3. Identify its impact.
4. Understand prevention methods.

**Expected outcome:** Understand common application security risks.

---

# 68. Assignment 2 – Prevent SQL Injection

Create a Node.js application with MySQL.

Implement:

- User registration.
- Login.
- Search users.
- Update user information.

Use parameterized queries.

**Expected outcome:** Understand secure SQL query construction.

---

# 69. Assignment 3 – Prevent XSS

Create a React or Next.js comments application.

Requirements:

- Accept user comments.
- Display comments.
- Avoid unsafe HTML rendering.
- Add HTML sanitization if rich HTML is required.

**Expected outcome:** Understand safe output handling.

---

# 70. Assignment 4 – Implement CSRF Protection

Create an Express application using cookie-based authentication.

Implement:

- Secure session cookies.
- CSRF tokens.
- Origin validation where appropriate.
- Protected state-changing endpoints.

**Expected outcome:** Understand CSRF protection.

---

# 71. Assignment 5 – Implement Secure Authentication

Create an authentication system using:

- Node.js.
- Express.
- MySQL.
- bcrypt or Argon2id.
- JWT or secure sessions.

Requirements:

- Registration.
- Login.
- Password hashing.
- Token or session verification.
- Logout.
- Password reset.
- Login rate limiting.

**Expected outcome:** Build a secure authentication system.

---

# 72. Assignment 6 – Secure API

Create a REST API with:

- Input validation.
- Authentication.
- Authorization.
- Rate limiting.
- Error handling.
- Security headers.
- CORS restrictions.

**Expected outcome:** Understand practical API security.

---

# 73. Assignment 7 – Dependency Scanning

Create a Node.js application.

Run:

```bash
npm audit
```

Review the results.

Identify vulnerable dependencies and update them safely.

**Expected outcome:** Understand SCA.

---

# 74. Assignment 8 – SAST Integration

Integrate CodeQL or Semgrep into GitHub Actions.

Run automated source-code security analysis.

Review and resolve findings.

**Expected outcome:** Understand SAST.

---

# 75. Assignment 9 – Docker Security

Create a secure Dockerfile.

Requirements:

- Use a minimal suitable base image.
- Use a non-root user.
- Avoid hardcoded secrets.
- Use multi-stage builds where appropriate.
- Scan the final image.

**Expected outcome:** Understand container security.

---

# 76. Assignment 10 – Kubernetes Security

Create a secure Kubernetes Deployment.

Configure:

- Non-root execution.
- Security context.
- Resource limits.
- Network Policy.
- RBAC.
- Secret protection.
- Restricted Pod Security where compatible.

**Expected outcome:** Understand Kubernetes workload security.

---

# 77. Assignment 11 – AWS Security

Create a secure AWS architecture.

Requirements:

- IAM least privilege.
- Private RDS.
- Restricted Security Groups.
- Secrets Manager.
- Encryption.
- CloudWatch monitoring.

**Expected outcome:** Understand cloud security.

---

# 78. Assignment 12 – Complete DevSecOps Pipeline

Create a full-stack application.

Integrate:

```text
Next.js
Node.js
Express
MySQL
Docker
GitHub Actions
CodeQL
npm audit
Trivy
AWS ECR
AWS EKS
CloudWatch
```

Requirements:

1. Automated tests.
2. SAST.
3. SCA.
4. Docker image scanning.
5. Secure image publishing.
6. OIDC authentication.
7. Kubernetes deployment.
8. Runtime security.
9. Monitoring.
10. Incident response documentation.

**Expected outcome:** Build a security-integrated software delivery pipeline.

---

# PART 27: QUICK REVISION

# 79. Security Concepts at a Glance

| Concept | Meaning |
|---|---|
| DevSecOps | Development, security and operations integration |
| SSDLC | Secure Software Development Life Cycle |
| OWASP | Open Worldwide Application Security Project |
| SQL Injection | Manipulating SQL through untrusted input |
| XSS | Executing untrusted script content in a browser |
| CSRF | Tricking an authenticated browser into making unwanted requests |
| SSRF | Manipulating a server into making unintended requests |
| Authentication | Verifying identity |
| Authorization | Checking permissions |
| RBAC | Role-Based Access Control |
| Hashing | One-way transformation |
| Encryption | Reversible transformation using keys |
| JWT | JSON Web Token |
| HTTPS | HTTP protected by TLS |
| TLS | Transport Layer Security |
| CSP | Content Security Policy |
| CORS | Cross-Origin Resource Sharing |
| SAST | Static Application Security Testing |
| DAST | Dynamic Application Security Testing |
| SCA | Software Composition Analysis |
| SIEM | Security Information and Event Management |
| IAM | Identity and Access Management |
| MFA | Multi-Factor Authentication |
| WAF | Web Application Firewall |
| SSR | Server-Side Rendering |
| HSTS | HTTP Strict Transport Security |

---

# 80. Important Security Tools

| Category | Tools |
|---|---|
| SAST | CodeQL, Semgrep, SonarQube |
| DAST | OWASP ZAP, Burp Suite |
| SCA | npm audit, Dependabot, Snyk |
| Container Scanning | Trivy, Grype |
| Secret Scanning | GitHub Secret Scanning, Gitleaks |
| API Testing | Postman, Burp Suite |
| Infrastructure Scanning | Checkov, Trivy |
| Cloud Security | AWS Security Hub, IAM Access Analyzer |
| Monitoring | CloudWatch, Prometheus, Grafana |
| SIEM | Wazuh, Splunk, OpenSearch |

---

# 81. Phase 48 Completion Checklist

### DevSecOps Fundamentals

- [ ] DevSecOps.
- [ ] DevOps vs DevSecOps.
- [ ] Secure SDLC.
- [ ] Threat Modeling.
- [ ] STRIDE.
- [ ] OWASP Top 10:2025.

### Application Security

- [ ] SQL Injection.
- [ ] XSS.
- [ ] CSRF.
- [ ] SSRF.
- [ ] Broken Access Control.
- [ ] Input Validation.
- [ ] Secure File Uploads.
- [ ] API Security.

### Authentication Security

- [ ] Authentication vs Authorization.
- [ ] RBAC.
- [ ] Password Hashing.
- [ ] bcrypt.
- [ ] Argon2id.
- [ ] JWT.
- [ ] Access Tokens.
- [ ] Refresh Tokens.
- [ ] Session Security.

### Network and Browser Security

- [ ] HTTPS.
- [ ] TLS.
- [ ] Security Headers.
- [ ] CSP.
- [ ] HSTS.
- [ ] CORS.
- [ ] Rate Limiting.

### Security Testing

- [ ] SAST.
- [ ] DAST.
- [ ] SCA.
- [ ] npm audit.
- [ ] CodeQL.
- [ ] Dependency Scanning.
- [ ] Container Scanning.

### Infrastructure Security

- [ ] Docker Security.
- [ ] Kubernetes RBAC.
- [ ] Kubernetes Secrets.
- [ ] Pod Security Standards.
- [ ] Network Policies.
- [ ] AWS IAM.
- [ ] Security Groups.
- [ ] RDS Security.

### DevSecOps Operations

- [ ] CI/CD Security.
- [ ] Secrets Management.
- [ ] Security Monitoring.
- [ ] SIEM.
- [ ] Incident Response.
- [ ] Vulnerability Management.

---

# 82. Official Documentation and Learning Resources

These resources are useful for deeper study and practical implementation.

| Topic | Official Resource |
|---|---|
| OWASP Top 10 | [OWASP Top 10:2025](https://top10.owasp.org/) |
| Application Security Standard | [OWASP ASVS 5.0](https://asvs.owasp.org/) |
| SQL Injection | [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) |
| XSS | [OWASP XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| Password Storage | [OWASP Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) |
| Secure Development | [NIST SSDF](https://csrc.nist.gov/pubs/sp/800/218/final) |
| GitHub Security | [GitHub Code Security](https://docs.github.com/en/code-security) |
| Node.js Security | [Node.js Documentation](https://nodejs.org/en/learn/getting-started/security-best-practices) |
| Kubernetes Security | [Kubernetes Security](https://kubernetes.io/docs/concepts/security/) |
| Pod Security | [Kubernetes Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/) |
| AWS IAM | [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) |
| Docker Security | [Docker Security](https://docs.docker.com/engine/security/) |
| OWASP ZAP | [OWASP ZAP Documentation](https://www.zaproxy.org/docs/) |
| Trivy | [Trivy Documentation](https://trivy.dev/latest/) |
| CodeQL | [CodeQL Documentation](https://codeql.github.com/docs/) |

**Additional reference:** OWASP ASVS 5.0.0 is a useful security verification standard when you need to convert general security advice into testable application security requirements. [OWASP ASVS](https://asvs.owasp.org/?utm_source=chatgpt.com)

---

# 83. Final Summary

In Phase 48, you learned how to integrate security into application development, infrastructure management and software delivery.

The most important principle is:

**Security should not be a final testing activity. It should be built into the application architecture, development process, CI/CD pipeline and production infrastructure.**

### Complete DevSecOps Architecture

```text
                    Developer
                        |
                        v
                  GitHub Repository
                        |
                        v
                  GitHub Actions
                        |
            +-----------+-----------+
            |           |           |
            v           v           v
           SAST        SCA       Unit Tests
            |           |           |
            +-----------+-----------+
                        |
                        v
                  Build Application
                        |
                        v
                   Docker Build
                        |
                        v
                 Container Scanning
                        |
                        v
                   Amazon ECR
                        |
                        v
                    AWS EKS
                        |
              +---------+---------+
              |                   |
              v                   v
         Pod Security        Network Policy
              |                   |
              +---------+---------+
                        |
                        v
                 Running Application
                        |
                        v
                Security Monitoring
                        |
                        v
                 Incident Response
```

### Most Important Topics to Master

Prioritize these topics:

1. OWASP Top 10.
2. SQL Injection prevention.
3. XSS prevention.
4. CSRF and SSRF.
5. Authentication and authorization.
6. Password hashing.
7. JWT security.
8. HTTPS and TLS.
9. CORS and security headers.
10. API security.
11. SAST, DAST and SCA.
12. Docker security.
13. Kubernetes security.
14. AWS IAM security.
15. CI/CD security.
16. Security monitoring and incident response.

---

# Next Phase Preview

## Phase 49 – System Design, Software Architecture and Scalable Application Design

The next phase will cover:

1. What Is System Design?
2. Low-Level Design (LLD).
3. High-Level Design (HLD).
4. Functional and Non-Functional Requirements.
5. Scalability.
6. Availability.
7. Reliability.
8. Maintainability.
9. Performance.
10. Latency and Throughput.
11. Vertical vs Horizontal Scaling.
12. Monolithic Architecture.
13. Microservices Architecture.
14. Modular Monolith.
15. Service-Oriented Architecture.
16. Client-Server Architecture.
17. Load Balancing.
18. Reverse Proxy.
19. API Gateway.
20. Database Scaling.
21. SQL vs NoSQL Architecture.
22. Database Replication.
23. Database Sharding.
24. Caching Strategies.
25. Redis Architecture.
26. Message Queues.
27. Event-Driven Architecture.
28. Kafka and RabbitMQ.
29. CDN Architecture.
30. Rate Limiting Architecture.
31. Distributed Systems.
32. CAP Theorem.
33. Consistency Models.
34. Fault Tolerance.
35. Circuit Breaker Pattern.
36. Retry and Timeout Patterns.
37. Idempotency.
38. Distributed Transactions.
39. Observability.
40. Designing Scalable Full-Stack Applications.
41. Real-World System Design Case Studies.

**Phase 49 will focus on designing scalable, reliable and maintainable software systems, moving from individual application development to complete system architecture.**