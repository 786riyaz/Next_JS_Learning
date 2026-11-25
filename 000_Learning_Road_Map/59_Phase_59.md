# Phase 59 – Advanced Application Security, OWASP, Secure Coding and Ethical Hacking Fundamentals

**Level:** Advanced  
**Domain:** Cybersecurity, Application Security, Secure Software Development and API Security  
**Primary Technologies:** JavaScript, TypeScript, Node.js, Express.js, React.js, Next.js, MySQL, MongoDB, Docker, AWS, OWASP ZAP and Burp Suite.

## 1. Learning Objectives

In this phase, you will learn how to identify security vulnerabilities, protect applications against common attacks, implement secure authentication and authorization, and build secure full-stack applications.

By completing this phase, you should understand:

1. Cybersecurity Fundamentals.
2. Application Security.
3. Authentication vs Authorization.
4. Password Security.
5. Password Hashing and Salting.
6. JWT Security.
7. Session-Based Authentication.
8. OAuth 2.0.
9. OpenID Connect.
10. Multi-Factor Authentication.
11. Role-Based Access Control (RBAC).
12. Attribute-Based Access Control (ABAC).
13. OWASP Top 10:2025.
14. SQL Injection.
15. Cross-Site Scripting (XSS).
16. Cross-Site Request Forgery (CSRF).
17. Server-Side Request Forgery (SSRF).
18. Broken Access Control.
19. Security Misconfiguration.
20. Software Supply Chain Security.
21. Cryptographic Failures.
22. API Security.
23. CORS.
24. Content Security Policy (CSP).
25. HTTPS and TLS.
26. Rate Limiting.
27. Brute-Force Protection.
28. Secure File Uploads.
29. Secrets Management.
30. Dependency Vulnerability Scanning.
31. Secure Node.js Development.
32. Secure React and Next.js Development.
33. Database Security.
34. Docker and Container Security.
35. Security Testing.
36. SAST, DAST and SCA.
37. Security Auditing.
38. OWASP ZAP and Burp Suite Fundamentals.
39. Secure Deployment Best Practices.

**Important:** All practical security testing in this phase should be performed on applications you own or systems for which you have explicit authorization.

---

# PART 1: CYBERSECURITY FUNDAMENTALS

## 2. What Is Cybersecurity?

Cybersecurity is the practice of protecting computers, applications, networks, servers and data from unauthorized access, attacks, damage and theft.

It involves protecting three major areas:

1. Confidentiality.
2. Integrity.
3. Availability.

These three principles are collectively known as the **CIA Triad**.

## 3. CIA Triad

### 3.1 Confidentiality

Confidentiality ensures that sensitive information is accessible only to authorized people or systems.

Example:

A user should only be able to view their own expense records.

Security measures:

- Authentication.
- Authorization.
- Encryption.
- Access controls.
- Data masking.

### 3.2 Integrity

Integrity ensures that information remains accurate and has not been modified without authorization.

Example:

A user should not be able to change another user's transaction amount.

Security measures:

- Digital signatures.
- Hashing.
- Database constraints.
- Access controls.
- Audit logs.

### 3.3 Availability

Availability ensures that systems and services remain accessible when required.

Example:

Your e-commerce website should remain available during a product launch.

Security measures:

- Load balancing.
- Backups.
- Disaster recovery.
- DDoS protection.
- Monitoring.
- Redundant infrastructure.

### CIA Triad Summary

| Principle | Objective | Example |
|---|---|---|
| Confidentiality | Prevent unauthorized access | Protect user information |
| Integrity | Prevent unauthorized modification | Protect transaction amounts |
| Availability | Keep services accessible | Prevent service outages |

## 4. What Is a Security Vulnerability?

A Security Vulnerability is a weakness in software, infrastructure or configuration that could be exploited to compromise a system.

Examples:

- Weak passwords.
- SQL injection.
- Missing authorization checks.
- Exposed API keys.
- Insecure file uploads.
- Outdated dependencies.
- Improper error handling.

## 5. What Is an Exploit?

An Exploit is a method, technique or piece of code that takes advantage of a vulnerability.

Example:

A website contains an authorization vulnerability.

An attacker manipulates a resource ID and accesses another user's data.

The vulnerability is the missing authorization check.

The exploit is the technique used to take advantage of it.

## 6. What Is an Attack Surface?

An Attack Surface is the collection of entry points through which an attacker might interact with an application or system.

For a web application, it may include:

- Login forms.
- Registration forms.
- API endpoints.
- File uploads.
- Search fields.
- Password reset functionality.
- Webhooks.
- Admin panels.
- Third-party integrations.
- Database connections.

### Example

```text
                 Web Application
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
    Login API      Upload API       Search API
       |               |               |
       v               v               v
 Authentication     File System     Database
```

Every exposed component should be considered during security design and testing.

---

# PART 2: APPLICATION SECURITY FUNDAMENTALS

## 7. What Is Application Security?

Application Security, commonly called AppSec, focuses on protecting software applications from security vulnerabilities throughout their lifecycle.

It includes:

- Secure architecture.
- Secure coding.
- Authentication.
- Authorization.
- Input validation.
- Encryption.
- Security testing.
- Dependency management.
- Security monitoring.

### Application Security vs Cybersecurity

| Feature | Cybersecurity | Application Security |
|---|---|---|
| Scope | Broad security discipline | Application-focused security |
| Focus | Networks, devices, systems, data and applications | Software applications and APIs |
| Example | Firewall configuration | SQL injection prevention |
| Objective | Protect digital assets | Protect applications |

Application security is one part of cybersecurity.

## 8. Secure Software Development Life Cycle (Secure SDLC)

Secure SDLC integrates security activities into every stage of software development.

```text
Requirements
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
Monitoring & Maintenance
```

### Security Activities

| SDLC Phase | Security Activity |
|---|---|
| Requirements | Define security requirements |
| Design | Perform threat modeling |
| Development | Follow secure coding practices |
| Testing | Perform security testing |
| Deployment | Secure infrastructure and secrets |
| Maintenance | Patch vulnerabilities and monitor incidents |

## 9. What Is Threat Modeling?

Threat Modeling is a structured process for identifying potential threats, vulnerabilities and attack paths before they become real security problems.

It helps answer:

1. What are we building?
2. What can go wrong?
3. How can we prevent it?
4. How will we detect attacks?

### Example: Expense Tracker

Suppose you are developing an expense management application.

Assets:

- User credentials.
- Expense records.
- Personal information.
- Database.
- Authentication tokens.

Potential threats:

| Asset | Threat | Possible Mitigation |
|---|---|---|
| User account | Password guessing | Rate limiting and MFA |
| Expense records | Unauthorized access | Object-level authorization |
| Database | SQL injection | Parameterized queries |
| Authentication token | Token theft | Secure token handling |
| Uploaded receipts | Malicious files | File validation and storage isolation |

### STRIDE Threat Modeling

STRIDE is a framework for classifying security threats.

| Category | Meaning | Example |
|---|---|---|
| S – Spoofing | Pretending to be someone else | Stealing an account |
| T – Tampering | Unauthorized modification | Changing transaction data |
| R – Repudiation | Denying an action | Denying a transaction without audit evidence |
| I – Information Disclosure | Exposing sensitive information | Leaking user records |
| D – Denial of Service | Making services unavailable | Overloading an API |
| E – Elevation of Privilege | Gaining unauthorized permissions | Normal user becoming admin |

---

# PART 3: AUTHENTICATION AND AUTHORIZATION

## 10. What Is Authentication?

Authentication verifies the identity of a user or system.

It answers:

**Who are you?**

Example:

A user enters:

```text
Email: user@example.com
Password: ********
```

The server verifies the credentials.

If they are valid, the user is authenticated.

### Common Authentication Methods

- Password authentication.
- Session-based authentication.
- Token-based authentication.
- Multi-factor authentication.
- Biometric authentication.
- OAuth-based authentication.
- Passkeys.

## 11. What Is Authorization?

Authorization determines what an authenticated user is allowed to access or perform.

It answers:

**What are you allowed to do?**

Example:

Your application has three roles:

```text
Admin
Manager
Employee
```

Permissions:

| Feature | Admin | Manager | Employee |
|---|---|---|---|
| View Dashboard | Yes | Yes | Yes |
| Create Expense | Yes | Yes | Yes |
| Delete Any Expense | Yes | Limited | No |
| Manage Users | Yes | No | No |
| View All Reports | Yes | Yes | No |

### Authentication vs Authorization

| Authentication | Authorization |
|---|---|
| Verifies identity | Verifies permissions |
| Happens before access is granted | Determines access after identity is established |
| Uses credentials or authentication factors | Uses roles, permissions and policies |
| Example: Login | Example: Admin access |

**Important:** Hiding a button in React is not authorization. The backend must enforce access control on every protected operation.

---

# PART 4: PASSWORD SECURITY

## 12. Why Is Password Security Important?

Passwords are commonly used to protect user accounts.

Weak password storage can expose thousands or millions of user accounts if a database is compromised.

Never store passwords in plain text.

### Incorrect Approach

```javascript
const user = {
  email: "user@example.com",
  password: "mypassword123"
};
```

This is unsafe because anyone who obtains the database can read the password.

## 13. What Is Hashing?

Hashing converts input data into a fixed-length representation using a hash function.

Example:

```text
Input:
hello

Hash:
2cf24dba5fb0a30e26e83b2ac5b9e29e...
```

A cryptographic hash function is designed to make it computationally infeasible to recover the original input from the hash.

### Hashing vs Encryption

| Feature | Hashing | Encryption |
|---|---|---|
| Purpose | One-way transformation | Reversible protection |
| Reversible | No practical reversal | Yes, with the correct key |
| Key required | Not normally | Yes |
| Example | Password verification | Protecting stored documents |
| Common algorithms | SHA-256, SHA-3 | AES-GCM, ChaCha20-Poly1305 |

**Important:** SHA-256 is a cryptographic hash function, but it is not suitable by itself for password storage because it is too fast.

## 14. What Is Salting?

A Salt is a unique random value added to a password before password hashing.

It helps prevent attackers from using precomputed password-hash lookup tables.

Example:

```text
Password:
mypassword

Salt:
random_value_123

Password Hash:
Hash(password + salt)
```

Modern password hashing libraries generally generate and manage salts automatically.

You should not manually invent a password-salting scheme.

## 15. Password Hashing Algorithms

Common password hashing algorithms:

| Algorithm | Description |
|---|---|
| Argon2id | Modern memory-hard password hashing algorithm |
| bcrypt | Widely used adaptive password hashing algorithm |
| scrypt | Memory-hard password hashing algorithm |
| PBKDF2 | Password-based key derivation function |

OWASP currently recommends Argon2id for general password storage, with alternatives for compatibility and compliance requirements. Its published minimum Argon2id configuration is 19 MiB memory, 2 iterations and parallelism of 1. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html?utm_source=chatgpt.com)

### Never Use These Alone for Password Storage

- MD5.
- SHA-1.
- SHA-256.
- SHA-512.

These general-purpose hash functions are too fast for secure password storage.

## 16. Implement Password Hashing in Node.js

Install Argon2:

```bash
npm install argon2
```

### Hash Password

```javascript
const argon2 = require("argon2");

async function hashPassword(password) {
  const hashedPassword = await argon2.hash(password);

  return hashedPassword;
}
```

Usage:

```javascript
const password = "StrongPassword123!";

const hashedPassword = await hashPassword(password);

console.log(hashedPassword);
```

The returned encoded hash contains the information needed by the library to verify the password, including the salt and hashing parameters.

### Verify Password

```javascript
async function verifyPassword(password, hash) {
  return await argon2.verify(hash, password);
}
```

Usage:

```javascript
const isValid = await verifyPassword(
  "StrongPassword123!",
  hashedPassword
);

console.log(isValid);
```

### Registration Flow

```text
User Enters Password
        |
        v
Validate Password
        |
        v
Argon2id Hashing
        |
        v
Store Password Hash
        |
        v
Database
```

### Login Flow

```text
User Enters Password
        |
        v
Fetch Stored Hash
        |
        v
Verify Using Argon2
        |
        +----------------+
        |                |
        v                v
      Valid            Invalid
        |                |
        v                v
   Authenticate       Reject Login
```

### Password Best Practices

- Use Argon2id or another suitable adaptive password hashing algorithm.
- Never store plain-text passwords.
- Never log passwords.
- Never send passwords through URLs.
- Use HTTPS.
- Apply rate limiting.
- Support secure password reset.
- Consider MFA.
- Avoid unnecessarily restrictive password composition rules.
- Screen against commonly compromised passwords where appropriate.

---

# PART 5: JWT SECURITY

## 17. What Is JWT?

JWT stands for JSON Web Token.

It is a compact token format used to transmit claims between parties.

It is commonly used for authentication and API authorization.

Example:

```text
xxxxx.yyyyy.zzzzz
```

A JWT commonly contains three parts:

1. Header.
2. Payload.
3. Signature.

### JWT Structure

```text
HEADER.PAYLOAD.SIGNATURE
```

### Header

Contains metadata about the token.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

Contains claims.

```json
{
  "sub": "123",
  "email": "user@example.com",
  "role": "admin",
  "iat": 1764000000,
  "exp": 1764003600
}
```

### Signature

The signature allows the recipient to verify that the token was issued by a trusted signer and has not been modified.

## 18. Important JWT Claims

| Claim | Meaning |
|---|---|
| `iss` | Issuer |
| `sub` | Subject |
| `aud` | Audience |
| `exp` | Expiration time |
| `iat` | Issued-at time |
| `nbf` | Not-before time |
| `jti` | Unique token identifier |

## 19. Is JWT Encrypted?

Not necessarily.

A typical signed JWT is readable by anyone who possesses it.

For example, the payload might contain:

```json
{
  "email": "user@example.com",
  "role": "admin"
}
```

A signed JWT protects the integrity and authenticity of its claims, but does not automatically provide confidentiality.

**Never place passwords, private keys, API secrets or sensitive personal information in an ordinary signed JWT payload.**

## 20. Access Token vs Refresh Token

### Access Token

An Access Token is used to access protected resources.

Characteristics:

- Usually short-lived.
- Sent with API requests.
- Contains authorization-related claims or references.

### Refresh Token

A Refresh Token is used to obtain a new access token after the old access token expires.

Characteristics:

- Longer-lived.
- Must be protected carefully.
- Should support revocation and rotation.
- Should be stored in a secure manner.

### Comparison

| Feature | Access Token | Refresh Token |
|---|---|---|
| Purpose | Access protected APIs | Obtain new access tokens |
| Lifetime | Usually short | Usually longer |
| Usage | Frequent | Less frequent |
| Exposure Risk | High | High |
| Revocation | Depends on architecture | Should support revocation |

## 21. JWT Security Best Practices

1. Always verify the signature.
2. Explicitly allow only expected signing algorithms.
3. Validate issuer (`iss`).
4. Validate audience (`aud`).
5. Validate expiration (`exp`).
6. Validate token purpose and type.
7. Use strong cryptographic keys.
8. Do not expose tokens in logs.
9. Use secure token storage.
10. Implement revocation where necessary.

OWASP specifically warns against trusting algorithms or verification keys supplied by untrusted JWT headers. JWT validation should use trusted keys and explicit algorithm restrictions. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_Cheat_Sheet?utm_source=chatgpt.com)

### Common JWT Mistakes

- Accepting unsigned tokens.
- Ignoring expiration.
- Using weak secrets.
- Trusting the payload without verifying the signature.
- Putting sensitive information in the payload.
- Using one token for unrelated purposes.
- Forgetting refresh-token revocation.
- Storing long-lived tokens in JavaScript-accessible storage without considering XSS risk.

---

# PART 6: SESSION-BASED AUTHENTICATION

## 22. What Is Session-Based Authentication?

Session-Based Authentication stores authentication state on the server.

After successful login:

1. The server creates a session.
2. The server stores session information.
3. The server sends a session identifier to the browser.
4. The browser stores it in a cookie.
5. The browser sends the cookie with subsequent requests.
6. The server retrieves and verifies the session.

### Architecture

```text
Browser
   |
   | Login Credentials
   v
Node.js Server
   |
   v
Verify Credentials
   |
   v
Create Session
   |
   v
Session Store
   |
   v
Set Session Cookie
   |
   v
Browser
```

### Example Cookie

```http
Set-Cookie: sessionId=abc123;
HttpOnly;
Secure;
SameSite=Lax;
Path=/
```

### Important Cookie Attributes

| Attribute | Purpose |
|---|---|
| HttpOnly | Prevents JavaScript from reading the cookie |
| Secure | Sends cookie only over secure HTTPS connections |
| SameSite | Controls cross-site cookie sending behavior |
| Path | Restricts cookie path |
| Domain | Controls cookie domain scope |
| Max-Age | Sets cookie lifetime |

## 23. Session vs JWT Authentication

| Feature | Session-Based | JWT-Based |
|---|---|---|
| State | Usually server-side | Claims stored in token |
| Client Storage | Session cookie | Depends on implementation |
| Revocation | Usually straightforward through session store | Requires additional design |
| Scaling | Requires shared session infrastructure or sticky sessions | Can simplify some distributed validation |
| Expiration | Server-controlled session | Token expiration claim |
| Common Use | Traditional web applications | APIs and distributed systems |

Neither approach is automatically more secure.

Security depends on implementation, storage, lifecycle management and authorization checks.

---

# PART 7: OAUTH 2.0 AND OPENID CONNECT

## 24. What Is OAuth 2.0?

OAuth 2.0 is an authorization framework that allows an application to obtain limited access to resources on behalf of a user or another client.

Example:

You want to sign in to an application using an external identity provider.

The application redirects you to the provider.

After consent, the application can receive an authorization grant and obtain tokens according to the selected OAuth flow.

### Important OAuth Roles

| Role | Meaning |
|---|---|
| Resource Owner | Usually the user |
| Client | Application requesting access |
| Authorization Server | Issues authorization grants and tokens |
| Resource Server | Hosts protected resources |

## 25. OAuth 2.0 Authorization Code Flow

A common flow for web applications is Authorization Code Flow with PKCE.

```text
User
 |
 v
Client Application
 |
 v
Authorization Server
 |
 v
User Authentication
 |
 v
User Consent
 |
 v
Authorization Code
 |
 v
Client Exchanges Code
 |
 v
Access Token
 |
 v
Protected Resource
```

### What Is PKCE?

PKCE stands for Proof Key for Code Exchange.

It protects authorization code flows against certain authorization code interception attacks.

It uses:

- Code Verifier.
- Code Challenge.

Public clients, such as browser-based and mobile applications, should use PKCE.

Modern OAuth deployments should use established authorization server libraries and protocols rather than implementing the protocol manually.

## 26. What Is OpenID Connect?

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0.

OAuth 2.0 primarily addresses delegated authorization.

OpenID Connect adds standardized authentication and identity information through an ID token.

### OAuth vs OpenID Connect

| OAuth 2.0 | OpenID Connect |
|---|---|
| Authorization framework | Authentication and identity layer |
| Grants access to resources | Communicates authenticated identity |
| Uses access tokens | Uses ID tokens and commonly access tokens |
| Answers what access is granted | Helps establish who the user is |

**Important:** An ID token is intended for the client application. It should not automatically be treated as an API access token.

---

# PART 8: MULTI-FACTOR AUTHENTICATION

## 27. What Is MFA?

Multi-Factor Authentication requires a user to provide more than one independent authentication factor.

Common factors:

1. Something you know.
2. Something you have.
3. Something you are.

### Examples

| Factor | Example |
|---|---|
| Something you know | Password |
| Something you have | Hardware security key |
| Something you are | Fingerprint |

### Common MFA Methods

- Authenticator applications.
- Time-based One-Time Passwords (TOTP).
- Hardware security keys.
- Passkeys.
- SMS codes.

SMS-based MFA is generally weaker than phishing-resistant authentication methods.

Where possible, prefer passkeys or security keys based on FIDO standards.

### MFA Workflow

```text
Enter Email
    |
    v
Enter Password
    |
    v
Verify Password
    |
    v
Second Authentication Factor
    |
    v
Verify Second Factor
    |
    v
Access Granted
```

---

# PART 9: ACCESS CONTROL MODELS

## 28. What Is RBAC?

RBAC stands for Role-Based Access Control.

Permissions are assigned to roles, and users are assigned roles.

Example:

```text
Permissions
    |
    v
Admin Role
    |
    v
Admin User
```

### Example

```javascript
const roles = {
  admin: [
    "user:create",
    "user:read",
    "user:update",
    "user:delete"
  ],

  manager: [
    "user:read",
    "expense:read",
    "expense:update"
  ],

  employee: [
    "expense:create",
    "expense:read_own"
  ]
};
```

### RBAC Middleware in Express

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

This is a basic role check. The `authenticate` middleware must independently verify the user's identity and populate `req.user` from trusted authentication data.

## 29. What Is ABAC?

ABAC stands for Attribute-Based Access Control.

It determines permissions based on attributes associated with:

- Users.
- Resources.
- Actions.
- Environment.

Example:

An employee can view an expense only if:

- They are authenticated.
- They own the expense.
- The expense belongs to their department.
- The expense is not restricted.

### RBAC vs ABAC

| Feature | RBAC | ABAC |
|---|---|---|
| Access Based On | Roles | Attributes and policies |
| Complexity | Generally simpler | Generally more complex |
| Example | Admin can delete users | Manager can approve department expenses under a limit |
| Flexibility | Role-oriented | Context-oriented |

---

# PART 10: OWASP TOP 10:2025

## 30. What Is OWASP?

OWASP stands for Open Worldwide Application Security Project.

It is a nonprofit organization that publishes resources, tools and guidance for improving software security.

One of its best-known resources is the OWASP Top 10.

**Version note:** The current released web application list is OWASP Top 10:2025, not the older 2021 edition. It contains ten categories covering major application security risks. [OWASP Top 10](https://top10.owasp.org/?trk=public_post-text\&utm_source=chatgpt.com)

Official resource:

[OWASP Top 10:2025](https://top10.owasp.org/2025/?utm_source=chatgpt.com)

## 31. OWASP Top 10:2025 Categories

| No. | Category | Meaning |
|---|---|---|
| A01 | Broken Access Control | Users access resources or actions without permission |
| A02 | Security Misconfiguration | Insecure application or infrastructure configuration |
| A03 | Software Supply Chain Failures | Risks in dependencies, build systems and software delivery |
| A04 | Cryptographic Failures | Inadequate protection of sensitive data |
| A05 | Injection | Untrusted input changes the meaning of commands or queries |
| A06 | Insecure Design | Security weaknesses originating in design decisions |
| A07 | Authentication Failures | Weaknesses in identity verification |
| A08 | Software or Data Integrity Failures | Failure to verify the integrity and trustworthiness of software or data |
| A09 | Security Logging and Alerting Failures | Security events are not adequately logged or acted upon |
| A10 | Mishandling of Exceptional Conditions | Incorrect handling of abnormal conditions and failures |

Let's understand each category.

---

## 32. A01:2025 – Broken Access Control

Broken Access Control occurs when users can perform actions or access resources beyond their permissions.

### Example

Suppose an API endpoint is:

```http
GET /api/users/100
```

A user changes the ID:

```http
GET /api/users/101
```

If the server returns another user's private information without checking authorization, the application has an access control vulnerability.

This is commonly called an Insecure Direct Object Reference (IDOR), although modern OWASP terminology discusses this under broader access-control weaknesses.

### Prevention

- Verify resource ownership.
- Enforce authorization on the backend.
- Use deny-by-default access policies.
- Validate every sensitive operation.
- Do not rely on frontend restrictions.
- Test horizontal and vertical privilege escalation.

### Secure Example

```javascript
async function getExpense(req, res) {
  const expense = await Expense.findByPk(
    req.params.id
  );

  if (!expense) {
    return res.status(404).json({
      message: "Expense not found"
    });
  }

  if (expense.userId !== req.user.id) {
    return res.status(403).json({
      message: "Access denied"
    });
  }

  return res.json(expense);
}
```

For production systems, enforce this ownership condition in the data-access query where practical, and apply additional role or tenant policies where needed.

---

## 33. A02:2025 – Security Misconfiguration

Security Misconfiguration happens when applications, servers or infrastructure are configured insecurely.

Examples:

- Debug mode enabled in production.
- Default admin credentials.
- Public database access.
- Exposed environment files.
- Unnecessary open ports.
- Missing security headers.
- Verbose error messages.
- Insecure CORS configuration.

### Prevention

- Disable debug mode in production.
- Remove default credentials.
- Use secure HTTP headers.
- Restrict network access.
- Review cloud permissions.
- Use secure environment configurations.
- Regularly audit deployment settings.

---

## 34. A03:2025 – Software Supply Chain Failures

Software Supply Chain Failures involve risks in the software components, tools and processes used to build and distribute applications.

Your Node.js project might depend on hundreds of npm packages.

If one dependency is compromised, your application may become vulnerable.

### Example

```json
{
  "dependencies": {
    "express": "^5.0.0",
    "some-library": "^1.0.0"
  }
}
```

A dependency or one of its transitive dependencies may contain a vulnerability.

Other risks include:

- Typosquatting.
- Malicious npm packages.
- Compromised package maintainers.
- Lockfile poisoning.
- Compromised build tools.
- Untrusted CI/CD actions.

### Prevention

Run:

```bash
npm audit
```

Check outdated packages:

```bash
npm outdated
```

Install from the lockfile:

```bash
npm ci
```

Additional practices:

- Review dependency changes.
- Use lockfiles.
- Keep dependencies updated.
- Scan dependencies in CI/CD.
- Minimize unnecessary packages.
- Protect package publishing credentials.
- Review third-party GitHub Actions.

The Node.js security guidance specifically discusses malicious dependencies, lockfiles, dependency confusion, `npm audit` and the use of `npm ci`. [nodejs.org](https://nodejs.org/learn/getting-started/security-best-practices?utm_source=chatgpt.com)

---

## 35. A04:2025 – Cryptographic Failures

Cryptographic Failures occur when sensitive information is not adequately protected through cryptography.

Examples:

- Storing passwords in plain text.
- Using weak encryption.
- Using outdated protocols.
- Exposing encryption keys.
- Using insecure random number generation.
- Improper certificate validation.

### Prevention

- Use HTTPS.
- Use modern TLS.
- Hash passwords using Argon2id or another suitable password hashing algorithm.
- Use authenticated encryption.
- Protect encryption keys.
- Use cryptographically secure random generators.
- Avoid inventing custom cryptographic algorithms.

### Example: Secure Random Token

Node.js:

```javascript
const crypto = require("node:crypto");

const token = crypto.randomBytes(32).toString("hex");

console.log(token);
```

Use `crypto.randomBytes()` for security-sensitive random tokens instead of `Math.random()`.

---

## 36. A05:2025 – Injection

Injection occurs when untrusted input is interpreted as part of a command, query or expression.

Common forms:

- SQL Injection.
- NoSQL Injection.
- OS Command Injection.
- LDAP Injection.
- Template Injection.

### SQL Injection Example

Suppose you construct a query like this:

```javascript
const email = req.body.email;

const query = `
  SELECT * FROM users
  WHERE email = '${email}'
`;
```

The problem is that untrusted input is directly inserted into the SQL statement.

An attacker may manipulate the query's meaning.

### Secure Approach: Parameterized Queries

Using MySQL2:

```javascript
const [rows] = await db.execute(
  "SELECT * FROM users WHERE email = ?",
  [email]
);
```

The database driver handles the value as a parameter instead of treating it as SQL syntax.

### Sequelize Example

```javascript
const user = await User.findOne({
  where: {
    email: req.body.email
  }
});
```

Use ORM parameterization correctly, and do not build raw SQL using untrusted input.

### Prevention

- Use parameterized queries.
- Validate inputs.
- Avoid unsafe dynamic SQL.
- Apply least-privilege database permissions.
- Do not rely on escaping alone.

---

## 37. A06:2025 – Insecure Design

Insecure Design refers to security weaknesses built into the architecture or business logic.

Example:

An expense application allows users to delete any expense because the original design never considered resource ownership.

Even if the code is technically implemented as specified, the design itself is insecure.

### Prevention

- Perform threat modeling.
- Define security requirements.
- Identify abuse cases.
- Apply least privilege.
- Enforce business rules on the backend.
- Design secure workflows.
- Conduct architecture reviews.

---

## 38. A07:2025 – Authentication Failures

Authentication Failures involve weaknesses in identifying and verifying users.

Examples:

- Weak password policies.
- Missing MFA for sensitive accounts.
- Poor session management.
- Weak password reset processes.
- No brute-force protection.
- Improper token validation.

### Prevention

- Use secure password hashing.
- Implement rate limiting.
- Use MFA.
- Protect session cookies.
- Validate authentication tokens.
- Implement secure password reset.
- Monitor suspicious login behavior.

---

## 39. A08:2025 – Software or Data Integrity Failures

These failures occur when an application trusts software, updates or data without adequately verifying integrity.

Examples:

- Installing unverified software updates.
- Trusting unsigned update packages.
- Processing untrusted serialized data.
- Accepting untrusted CI/CD artifacts.
- Using compromised build outputs.

### Prevention

- Verify software artifacts.
- Protect build pipelines.
- Use trusted package registries.
- Apply dependency integrity checks.
- Verify signatures where appropriate.
- Protect deployment credentials.

---

## 40. A09:2025 – Security Logging and Alerting Failures

Security Logging and Alerting Failures occur when security events are not adequately recorded, monitored or escalated.

Examples:

- Failed login attempts are not logged.
- Repeated authorization failures go unnoticed.
- Admin changes are not recorded.
- Suspicious activity generates no alert.
- Security logs can be modified without detection.

### Prevention

Log important security events:

- Login success.
- Login failure.
- Password changes.
- Account lockouts.
- Permission changes.
- Administrative operations.
- Suspicious API access.

### Example

```javascript
function logSecurityEvent(event) {
  console.log({
    timestamp: new Date().toISOString(),
    type: event.type,
    userId: event.userId,
    ip: event.ip,
    action: event.action
  });
}
```

This is only a simplified example. Production systems should use structured logging, centralized log storage, access controls and alerting.

**Never log passwords, session cookies, access tokens or other secrets.**

---

## 41. A10:2025 – Mishandling of Exceptional Conditions

This category covers weaknesses in how applications handle abnormal situations.

Examples:

- Application exposes internal stack traces.
- Payment processing fails but the order is marked successful.
- A failed authorization check accidentally allows access.
- An application continues with incomplete security validation.
- Database errors leave partially updated records.

### Example

Incorrect:

```javascript
try {
  await processPayment();
} catch (error) {
  console.log(error);

  return res.json({
    success: true
  });
}
```

This reports success even though payment processing failed.

Correct:

```javascript
try {
  await processPayment();

  return res.status(200).json({
    success: true
  });
} catch (error) {
  logger.error({
    message: "Payment processing failed",
    error
  });

  return res.status(500).json({
    success: false,
    message: "Payment could not be processed"
  });
}
```

In payment systems, also design for idempotency, transaction consistency and reconciliation.

---

# PART 11: CROSS-SITE SCRIPTING (XSS)

## 42. What Is XSS?

Cross-Site Scripting is a vulnerability in which untrusted content is executed as JavaScript in another user's browser.

It can allow attackers to:

- Manipulate web pages.
- Perform actions in the victim's browser.
- Read accessible page data.
- Steal information exposed to JavaScript.
- Abuse authenticated sessions when protections are insufficient.

### Types of XSS

1. Stored XSS.
2. Reflected XSS.
3. DOM-Based XSS.

## 43. Stored XSS

Stored XSS occurs when malicious content is saved by an application and later rendered unsafely to other users.

Example:

A user submits a comment.

The application stores it.

Another user opens the comment page, and the browser interprets the content as executable HTML or JavaScript.

### Prevention

- Context-aware output encoding.
- Safe HTML sanitization when rich HTML is required.
- Avoid unsafe DOM APIs.
- Use Content Security Policy as an additional defense.

## 44. Reflected XSS

Reflected XSS occurs when untrusted input from a request is immediately included in a response without appropriate encoding.

Example:

A search page reflects the user's search term directly into HTML.

If the application treats the input as executable markup, it may create an XSS vulnerability.

## 45. DOM-Based XSS

DOM-Based XSS occurs when client-side JavaScript uses untrusted data in an unsafe DOM operation.

Example of unsafe code:

```javascript
const params = new URLSearchParams(location.search);

const name = params.get("name");

document.querySelector("#output").innerHTML = name;
```

The application inserts untrusted input as HTML.

### Safer Approach

```javascript
const params = new URLSearchParams(location.search);

const name = params.get("name");

document.querySelector("#output").textContent = name;
```

`textContent` treats the value as text rather than HTML markup.

## 46. React and XSS

React escapes text values by default.

Example:

```jsx
function Comment({ comment }) {
  return <div>{comment}</div>;
}
```

React treats `comment` as text.

However, this does not mean every React application is automatically safe.

Be especially careful with:

```jsx
<div
  dangerouslySetInnerHTML={{
    __html: userContent
  }}
/>
```

Only use this with appropriately sanitized HTML.

### XSS Prevention Checklist

- Avoid unsafe `innerHTML`.
- Avoid unsafe `dangerouslySetInnerHTML`.
- Sanitize untrusted HTML using a maintained sanitizer.
- Validate URLs before using them in links.
- Apply a restrictive CSP.
- Keep dependencies updated.
- Avoid evaluating untrusted JavaScript.

---

# PART 12: CROSS-SITE REQUEST FORGERY (CSRF)

## 47. What Is CSRF?

Cross-Site Request Forgery occurs when an attacker tricks an authenticated user's browser into sending an unwanted request to a trusted application.

It commonly affects applications that use automatically attached authentication cookies.

### Example

Suppose a user is logged into a banking website.

A malicious website causes the user's browser to send an unwanted state-changing request to the banking application.

If the banking application does not validate that the request was intentionally initiated by the user, the action may succeed.

### CSRF Prevention

- Use CSRF tokens where appropriate.
- Configure SameSite cookies.
- Validate request origins where appropriate.
- Require authentication and authorization.
- Never use GET requests for state-changing operations.
- Use appropriate custom request headers for AJAX requests.

OWASP explains that SameSite cookies provide defense in depth but do not replace proper CSRF protection in all deployments. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com)

## 48. CSRF Token Example

A simplified Express example using a CSRF middleware:

```javascript
const express = require("express");

const session = require("express-session");

const csrf = require("csurf");

const app = express();

app.use(express.urlencoded({
  extended: false
}));

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "lax"
  }
}));

const csrfProtection = csrf();

app.get("/form", csrfProtection, (req, res) => {
  res.json({
    csrfToken: req.csrfToken()
  });
});

app.post("/submit", csrfProtection, (req, res) => {
  res.json({
    success: true
  });
});
```

This is an educational example, not a complete production configuration. The `csurf` package is archived; choose a currently maintained CSRF implementation compatible with your framework and session architecture.

### Important Distinction

CSRF and XSS are different.

| XSS | CSRF |
|---|---|
| Executes attacker-controlled script in a trusted origin | Tricks a browser into sending an unwanted authenticated request |
| Targets browser-side execution | Targets authenticated request behavior |
| Often mitigated with output encoding and sanitization | Often mitigated with CSRF tokens and cookie protections |

---

# PART 13: SERVER-SIDE REQUEST FORGERY (SSRF)

## 49. What Is SSRF?

Server-Side Request Forgery occurs when an attacker can influence server-side requests to unintended destinations.

Example:

Your application allows users to submit a URL to retrieve an image.

```text
User submits URL
       |
       v
Node.js Backend
       |
       v
Fetches URL
       |
       v
Returns Image
```

If the server accepts arbitrary URLs, an attacker may attempt to make it contact:

- Internal services.
- Private network addresses.
- Cloud metadata endpoints.
- Localhost services.
- Administrative interfaces.

### Unsafe Example

```javascript
app.get("/fetch-image", async (req, res) => {
  const response = await fetch(req.query.url);

  const data = await response.arrayBuffer();

  res.send(Buffer.from(data));
});
```

The application fetches a user-controlled URL without sufficient restrictions.

### Prevention

1. Prefer an allowlist of trusted destinations.
2. Restrict protocols to HTTPS where possible.
3. Reject private and loopback IP destinations.
4. Validate resolved IP addresses.
5. Disable redirects or revalidate every redirect destination.
6. Apply outbound network restrictions.
7. Set connection and response timeouts.
8. Limit response sizes.
9. Do not allow users to control internal service URLs.

A simple hostname allowlist is not always sufficient because DNS resolution and redirects can change the destination. SSRF defenses should be implemented at both the application and network layers.

---

# PART 14: API SECURITY

## 50. What Is API Security?

API Security focuses on protecting application programming interfaces from unauthorized access, data exposure and abuse.

Common API security risks include:

- Broken Object Level Authorization.
- Broken Authentication.
- Broken Object Property Level Authorization.
- Unrestricted Resource Consumption.
- Broken Function Level Authorization.
- Sensitive Business Flow Abuse.
- SSRF.
- Security Misconfiguration.
- Improper API Inventory Management.
- Unsafe Consumption of APIs.

These are the ten categories in the OWASP API Security Top 10:2023. [OWASP Foundation](https://owasp.org/projects/api-security-project?utm_source=chatgpt.com)

Official resource:

[OWASP API Security Top 10](https://owasp.org/www-project-api-security/?utm_source=chatgpt.com)

## 51. API Security Best Practices

### 1. Authentication

Verify user identity on protected endpoints.

### 2. Authorization

Verify whether the user is allowed to perform the requested operation.

### 3. Input Validation

Validate:

- Request body.
- Query parameters.
- Path parameters.
- Headers.

### 4. Rate Limiting

Restrict excessive requests.

### 5. HTTPS

Protect data in transit.

### 6. Secure Error Handling

Avoid exposing:

- Stack traces.
- SQL queries.
- Internal paths.
- Secrets.
- Database connection details.

### 7. Resource Limits

Apply limits to:

- Request body size.
- File uploads.
- Pagination size.
- Query execution time.
- Concurrent operations.

### 8. API Versioning

Maintain clear API versions and retire unsupported endpoints safely.

### 9. Object-Level Authorization

Verify that the current user can access the requested resource.

### 10. Logging and Monitoring

Record important security events and generate alerts for suspicious patterns.

---

# PART 15: CORS

## 52. What Is CORS?

CORS stands for Cross-Origin Resource Sharing.

It is a browser security mechanism that controls whether a web page from one origin can read responses from another origin.

An origin consists of:

```text
Scheme + Host + Port
```

Example:

```text
https://example.com
https://api.example.com
```

These are different origins because their hosts differ.

## 53. Same-Origin Policy

Browsers enforce the Same-Origin Policy.

It generally restricts JavaScript on one origin from reading resources belonging to another origin unless the relevant browser security rules permit it.

CORS allows a server to declare which origins may read its responses.

## 54. CORS Example

Suppose your frontend runs on:

```text
http://localhost:3000
```

Your backend runs on:

```text
http://localhost:5000
```

These are different origins because their ports differ.

### Express CORS Configuration

Install:

```bash
npm install cors
```

Configure:

```javascript
const cors = require("cors");

const allowedOrigins = [
  "http://localhost:3000",
  "https://example.com"
];

app.use(cors({
  origin: allowedOrigins,
  credentials: true
}));
```

### Important CORS Rules

- Allow only trusted origins.
- Avoid reflecting arbitrary origins.
- Do not combine wildcard origins with credentialed requests.
- Keep allowed methods and headers limited.
- Do not treat CORS as authentication.
- Do not treat CORS as authorization.

OWASP explicitly notes that CORS is not access control and does not prevent CSRF. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/CORS_Security_Cheat_Sheet.html?utm_source=chatgpt.com)

---

# PART 16: HTTPS AND TLS

## 55. What Is HTTP?

HTTP stands for Hypertext Transfer Protocol.

It is a protocol used for communication between clients and servers.

Example:

```http
GET /api/users HTTP/1.1
Host: example.com
```

## 56. What Is HTTPS?

HTTPS is HTTP communication protected using TLS.

It provides:

- Confidentiality.
- Integrity.
- Server authentication.

### HTTP vs HTTPS

| HTTP | HTTPS |
|---|---|
| No TLS protection | TLS-protected communication |
| Data may be intercepted or modified in transit | Data is encrypted and integrity-protected |
| No TLS server authentication | Server certificate is validated |
| Uses port 80 by default | Uses port 443 by default |

## 57. What Is TLS?

TLS stands for Transport Layer Security.

It provides secure communication over a network.

It helps protect data from:

- Eavesdropping.
- Tampering.
- Certain impersonation attacks.

### TLS Handshake Overview

```text
Client
  |
  | ClientHello
  v
Server
  |
  | ServerHello + Certificate
  v
Client Validates Certificate
  |
  v
Key Establishment
  |
  v
Secure Communication
```

### Best Practices

- Use TLS 1.2 or TLS 1.3.
- Prefer modern cipher suites.
- Use valid certificates.
- Redirect HTTP to HTTPS.
- Protect private keys.
- Enable HSTS where appropriate.
- Avoid mixed content.

---

# PART 17: SECURITY HEADERS AND CSP

## 58. What Are HTTP Security Headers?

HTTP Security Headers are response headers that help browsers apply additional security restrictions.

Important headers:

| Header | Purpose |
|---|---|
| Content-Security-Policy | Restricts permitted content sources |
| Strict-Transport-Security | Enforces HTTPS for future requests |
| X-Content-Type-Options | Prevents MIME-type sniffing |
| Referrer-Policy | Controls referrer information |
| Permissions-Policy | Controls access to browser features |
| frame-ancestors | Controls which pages may embed the application |

## 59. What Is Content Security Policy (CSP)?

CSP is a browser security mechanism that controls which sources are allowed to provide scripts, styles, images and other content.

It can reduce the impact of certain XSS vulnerabilities.

Example:

```http
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none';
```

This is an illustrative policy. Real applications often need additional directives for images, styles, fonts, API connections and framework-specific scripts.

### Important CSP Directives

| Directive | Purpose |
|---|---|
| `default-src` | Default source policy |
| `script-src` | JavaScript sources |
| `style-src` | CSS sources |
| `img-src` | Image sources |
| `font-src` | Font sources |
| `connect-src` | Fetch, XHR and WebSocket destinations |
| `object-src` | Plugin/object sources |
| `frame-ancestors` | Permitted embedding parents |
| `base-uri` | Permitted base URL sources |

## 60. Helmet in Express

Helmet helps configure HTTP security headers.

Install:

```bash
npm install helmet
```

Use:

```javascript
const helmet = require("helmet");

app.use(helmet());
```

Helmet applies a set of security-related headers.

Review its configuration and your application's requirements before deploying restrictive policies.

---

# PART 18: RATE LIMITING AND BRUTE-FORCE PROTECTION

## 61. What Is Rate Limiting?

Rate Limiting restricts how many requests a client can make during a specified time window.

Example:

```text
Maximum 100 requests per minute
```

If a client exceeds the configured limit, the server can return:

```http
429 Too Many Requests
```

### Why Is Rate Limiting Important?

It helps protect against:

- Brute-force attempts.
- API abuse.
- Excessive resource consumption.
- Automated scraping.
- Certain denial-of-service patterns.

## 62. Express Rate Limiting

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

app.use("/api/auth/login", loginLimiter);
```

This is a basic example. Production systems should consider account-level throttling, IP-based limits, distributed storage and the risk of attackers locking out legitimate users.

## 63. What Is a Brute-Force Attack?

A Brute-Force Attack attempts many possible credentials or combinations until a valid one is found.

Example:

An attacker repeatedly guesses passwords for a user account.

### Prevention

- Rate limiting.
- MFA.
- Strong password hashing.
- Suspicious login detection.
- Account protection mechanisms.
- Progressive delays where appropriate.
- Secure password reset.

---

# PART 19: SECURE FILE UPLOADS

## 64. Why Are File Uploads Dangerous?

File uploads allow users to send files to your application.

If the application does not validate them properly, attackers may upload:

- Executable files.
- Malicious scripts.
- Oversized files.
- Files with misleading extensions.
- Files containing malicious content.
- Files designed to exploit vulnerable parsers.

### Example

Your application allows users to upload expense receipts.

Expected formats:

```text
.jpg
.jpeg
.png
.pdf
```

Do not trust the filename or the browser-supplied Content-Type header alone.

## 65. Secure File Upload Practices

1. Set maximum file size.
2. Allow only required file types.
3. Validate file signatures (magic bytes).
4. Generate random server-side filenames.
5. Store uploads outside executable application directories.
6. Prevent uploaded files from being executed.
7. Use malware scanning where appropriate.
8. Apply authorization checks.
9. Use private object storage for sensitive files.
10. Restrict access through controlled download endpoints.

### Example with Multer

Install:

```bash
npm install multer
```

Example:

```javascript
const multer = require("multer");

const upload = multer({
  storage: multer.memoryStorage(),

  limits: {
    fileSize: 2 * 1024 * 1024
  },

  fileFilter: (req, file, callback) => {
    const allowedTypes = [
      "image/jpeg",
      "image/png",
      "application/pdf"
    ];

    if (!allowedTypes.includes(file.mimetype)) {
      return callback(
        new Error("Unsupported file type")
      );
    }

    callback(null, true);
  }
});
```

**Important:** This example checks the declared MIME type, which can be spoofed. A production implementation must also inspect file signatures and validate the actual file format.

---

# PART 20: SECRETS MANAGEMENT

## 66. What Is a Secret?

A Secret is sensitive information used to authenticate or authorize access to systems and services.

Examples:

- Database passwords.
- API keys.
- JWT signing keys.
- OAuth client secrets.
- AWS credentials.
- Encryption keys.
- Private certificates.

## 67. What Is an Environment Variable?

An Environment Variable stores configuration outside application source code.

Example `.env`:

```env
PORT=5000

DB_HOST=localhost
DB_USER=root
DB_PASSWORD=strong_password

JWT_SECRET=very_long_random_secret
```

Node.js:

```javascript
require("dotenv").config();

const port = process.env.PORT;

const jwtSecret = process.env.JWT_SECRET;
```

### Important

Environment variables help separate configuration from source code, but they are not automatically a complete secrets-management solution.

They can still leak through:

- Process inspection.
- Logs.
- Crash dumps.
- Incorrect CI configuration.
- Misconfigured deployment systems.

## 68. Secret Management Best Practices

- Never commit `.env` files.
- Add `.env` to `.gitignore`.
- Use separate secrets for development and production.
- Rotate compromised credentials.
- Restrict secret access.
- Use a secrets manager for production workloads.
- Never expose backend secrets to frontend code.
- Never hardcode API keys in repositories.

### Example `.gitignore`

```gitignore
.env
.env.local
.env.production
.env.*
!.env.example
```

### Example `.env.example`

```env
PORT=

DB_HOST=
DB_USER=
DB_PASSWORD=

JWT_SECRET=
```

An example file should contain placeholders, never real credentials.

---

# PART 21: SECURE NODE.JS DEVELOPMENT

## 69. Node.js Security Best Practices

Node.js applications should be protected at multiple layers.

### 1. Input Validation

Use a validation library such as Zod.

Install:

```bash
npm install zod
```

Example:

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
const result = userSchema.safeParse(req.body);

if (!result.success) {
  return res.status(400).json({
    message: "Invalid input",
    errors: result.error.issues
  });
}
```

### 2. Secure Error Handling

Do not expose internal errors to clients.

Incorrect:

```javascript
res.status(500).json({
  error: error.stack
});
```

Better:

```javascript
logger.error(error);

res.status(500).json({
  message: "Internal server error"
});
```

### 3. Use Helmet

```javascript
app.use(require("helmet")());
```

### 4. Apply Rate Limiting

Protect login and sensitive endpoints.

### 5. Validate Request Size

```javascript
app.use(express.json({
  limit: "100kb"
}));
```

Choose a limit appropriate to your application.

### 6. Use Parameterized Database Queries

Avoid building SQL statements from user input.

### 7. Secure Cookies

Use:

- HttpOnly.
- Secure.
- SameSite.
- Appropriate expiration.

### 8. Protect Dependencies

```bash
npm audit
```

### 9. Configure Server Timeouts

Node.js servers should have appropriate request and connection timeouts to reduce exposure to resource exhaustion.

### 10. Use Least Privilege

Run applications with only the operating-system and database permissions they require.

The official Node.js security guidance covers application-level threats, denial-of-service concerns, dependency risks and server configuration. [nodejs.org](https://nodejs.org/learn/getting-started/security-best-practices?utm_source=chatgpt.com)

---

# PART 22: SECURE REACT AND NEXT.JS DEVELOPMENT

## 70. React Security Best Practices

React provides some protection against XSS by escaping values rendered as text.

However, developers must still avoid unsafe operations.

### Important Practices

- Avoid unsafe `dangerouslySetInnerHTML`.
- Sanitize HTML when rich HTML rendering is required.
- Validate user-provided URLs.
- Never store backend secrets in frontend code.
- Do not rely on client-side authorization.
- Avoid exposing sensitive API responses.
- Validate all protected operations on the server.

## 71. Next.js Security Best Practices

Next.js applications may contain both client-side and server-side code.

This distinction is important.

### Server-Side Secrets

Correct:

```javascript
const dbPassword = process.env.DB_PASSWORD;
```

Use this in server-only code.

Incorrect:

```env
NEXT_PUBLIC_DB_PASSWORD=secret123
```

Variables with the `NEXT_PUBLIC_` prefix are intended to be exposed to browser-side code.

Never use this prefix for secrets.

### Server Actions

Server Actions are server-side operations, but they are still entry points that require security checks.

Always:

- Authenticate the user.
- Authorize the requested action.
- Validate input.
- Protect sensitive operations.
- Avoid trusting client-provided IDs or roles.

### Example

```javascript
"use server";

export async function deleteExpense(expenseId) {
  const user = await getAuthenticatedUser();

  if (!user) {
    throw new Error("Unauthorized");
  }

  const expense = await findExpense(expenseId);

  if (!expense) {
    throw new Error("Expense not found");
  }

  if (expense.userId !== user.id) {
    throw new Error("Forbidden");
  }

  await removeExpense(expenseId);
}
```

This illustrates the authorization logic. In production, use your established authentication and database abstractions, and consider combining the ownership check with the deletion query to avoid time-of-check/time-of-use problems.

### Important

Client components and hidden UI elements are not security boundaries.

The server must enforce access control.

---

# PART 23: DATABASE SECURITY

## 72. What Is Database Security?

Database Security protects stored information from unauthorized access, modification, deletion and exposure.

### Important Database Security Practices

**1. Least Privilege**

Give database accounts only the permissions they need.

**2. Parameterized Queries**

Prevent SQL injection.

**3. Strong Authentication**

Use secure database credentials.

**4. Network Restrictions**

Do not expose database ports publicly unless absolutely necessary.

**5. Encryption**

Protect sensitive information at rest and in transit.

**6. Backups**

Maintain secure backups and test restoration.

**7. Auditing**

Record important database access and administrative activity.

**8. Data Minimization**

Avoid storing unnecessary personal information.

## 73. MySQL Security Example

Avoid:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'app_user'@'%';
```

A production application should not normally have unrestricted access to every database.

Instead, grant only the permissions required for the application.

Illustrative example:

```sql
GRANT SELECT, INSERT, UPDATE, DELETE
ON expense_tracker.*
TO 'app_user'@'localhost';
```

Actual permissions should match the application's operational requirements.

### MongoDB Security

Important practices:

- Enable authentication.
- Restrict network access.
- Use role-based database permissions.
- Avoid public database exposure.
- Use TLS where appropriate.
- Protect connection strings.
- Keep MongoDB updated.
- Configure backups securely.

---

# PART 24: DOCKER AND CONTAINER SECURITY

## 74. What Is Container Security?

Container Security protects container images, runtime environments, configurations and container orchestration infrastructure.

### Common Container Security Risks

- Running containers as root.
- Using vulnerable base images.
- Exposing unnecessary ports.
- Storing secrets in Dockerfiles.
- Using privileged containers unnecessarily.
- Installing unnecessary packages.
- Using outdated images.

## 75. Docker Security Best Practices

### 1. Use a Non-Root User

Example Dockerfile:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY . .

RUN addgroup -S appgroup && \
    adduser -S appuser -G appgroup

USER appuser

EXPOSE 3000

CMD ["node", "server.js"]
```

This is an illustrative Dockerfile; verify that the application files and runtime directories have appropriate ownership and permissions.

### 2. Avoid Secrets in Dockerfiles

Incorrect:

```dockerfile
ENV API_KEY=secret123
```

Secrets should be supplied through an appropriate secret-management mechanism at runtime or build time.

### 3. Scan Container Images

Use image vulnerability scanners to identify vulnerable packages and base images.

### 4. Minimize Image Size

Use minimal base images and multi-stage builds where appropriate.

### 5. Restrict Container Permissions

Avoid unnecessary privileged mode and capabilities.

---

# PART 25: SECURITY TESTING

## 76. What Is Security Testing?

Security Testing evaluates an application to identify vulnerabilities and verify that security controls work correctly.

It includes:

- Authentication testing.
- Authorization testing.
- Input validation testing.
- Session testing.
- API security testing.
- Dependency testing.
- Configuration testing.
- Infrastructure testing.

## 77. Common Security Testing Types

| Testing Type | Purpose |
|---|---|
| Vulnerability Assessment | Identify potential weaknesses |
| Penetration Testing | Safely validate exploitable weaknesses under authorization |
| Security Audit | Review security controls and practices |
| Configuration Review | Identify insecure configurations |
| Authentication Testing | Verify identity controls |
| Authorization Testing | Verify access controls |
| API Security Testing | Assess API vulnerabilities |
| Dependency Scanning | Identify vulnerable dependencies |

### Vulnerability Assessment vs Penetration Testing

| Vulnerability Assessment | Penetration Testing |
|---|---|
| Identifies potential vulnerabilities | Validates security weaknesses through controlled testing |
| Often broad | Often focused and deeper |
| Usually produces a vulnerability list | Produces findings with demonstrated impact |
| May be largely automated | Usually combines manual and automated work |

---

# PART 26: SAST, DAST AND SCA

## 78. What Is SAST?

SAST stands for Static Application Security Testing.

It analyzes source code or compiled artifacts without executing the application.

It can identify issues such as:

- Hardcoded secrets.
- Dangerous function usage.
- Injection risks.
- Insecure coding patterns.

Examples of tools:

- Semgrep.
- CodeQL.
- SonarQube.

## 79. What Is DAST?

DAST stands for Dynamic Application Security Testing.

It tests a running application from the outside.

It can identify:

- XSS.
- Security misconfiguration.
- Insecure HTTP headers.
- Authentication weaknesses.
- Certain injection vulnerabilities.

Examples:

- OWASP ZAP.
- Burp Suite.

## 80. What Is SCA?

SCA stands for Software Composition Analysis.

It analyzes third-party packages and dependencies.

It helps identify:

- Known vulnerabilities.
- Outdated packages.
- Risky dependencies.
- License issues.

Examples:

- npm audit.
- Dependabot.
- Snyk.
- OWASP Dependency-Check.

### SAST vs DAST vs SCA

| Feature | SAST | DAST | SCA |
|---|---|---|---|
| Full Form | Static Application Security Testing | Dynamic Application Security Testing | Software Composition Analysis |
| Analyzes | Source code | Running application | Third-party dependencies |
| Execution Required | Usually no | Yes | Usually no |
| Example | CodeQL | OWASP ZAP | npm audit |
| Main Focus | Code weaknesses | Runtime vulnerabilities | Dependency risks |

---

# PART 27: OWASP ZAP

## 81. What Is OWASP ZAP?

OWASP ZAP stands for Zed Attack Proxy.

It is an application security testing tool used to assess web applications.

It provides:

- Passive scanning.
- Active scanning.
- Proxy-based testing.
- Spidering.
- API scanning.
- Security reports.

Official resource:

[OWASP ZAP Documentation](https://www.zaproxy.org/docs/?utm_source=chatgpt.com)

### Basic Workflow

```text
Start Local Application
        |
        v
Open OWASP ZAP
        |
        v
Configure Browser Proxy
        |
        v
Browse Application
        |
        v
Capture Requests
        |
        v
Analyze Traffic
        |
        v
Run Authorized Scans
        |
        v
Review Findings
        |
        v
Fix Vulnerabilities
```

### Passive Scanning

Passive scanning analyzes traffic without intentionally modifying requests to exploit vulnerabilities.

### Active Scanning

Active scanning sends test requests intended to identify vulnerabilities.

Only run active scans against systems you own or are explicitly authorized to assess.

---

# PART 28: BURP SUITE

## 82. What Is Burp Suite?

Burp Suite is a web application security testing platform.

It is widely used by security professionals to inspect and test HTTP traffic.

Official resource:

[Burp Suite Documentation](https://portswigger.net/burp/documentation?utm_source=chatgpt.com### Important Components| Component | Purpose ||---|---|| Proxy | Intercepts HTTP traffic || Repeater | Manually resends modified requests || Intruder | Automates customized request testing || Scanner | Identifies security vulnerabilities in supported editions || Decoder | Encodes and decodes data || Comparer | Compares data || Sequencer | Analyzes token randomness |### Example Workflow```textBrowser   |   vBurp Proxy   |   vApplication   |   vInspect HTTP Request   |   vReview Response   |   vIdentify Security Issues```### ImportantBurp Suite and OWASP ZAP are testing tools, not substitutes for secure architecture, code review and continuous security practices.---# PART 29: SOFTWARE SUPPLY CHAIN SECURITY## 83. What Is Software Supply Chain Security?Software Supply Chain Security protects the complete process through which software is developed, built, distributed and deployed.It includes:- Source code.- Dependencies.- Build tools.- CI/CD workflows.- Container images.- Artifact repositories.- Deployment credentials.### Common Risks- Malicious npm packages.- Compromised GitHub Actions.- Leaked repository secrets.- Vulnerable Docker base images.- Compromised package maintainers.- Unverified release artifacts.### Best Practices1. Use dependency lockfiles.2. Review package changes.3. Run automated vulnerability scans.4. Pin important CI/CD dependencies.5. Protect GitHub secrets.6. Restrict repository write permissions.7. Scan container images.8. Verify artifacts.9. Maintain software bills of materials where appropriate.## 84. What Is an SBOM?SBOM stands for Software Bill of Materials.It is an inventory of software components and dependencies included in a product.Example:```textApplication  |  +-- Express  |  +-- MySQL2  |  +-- Zod  |  +-- JSON Web Token Library  |  +-- Other Dependencies```SBOMs help organizations understand what software components they use and assess vulnerability exposure.---# PART 30: SECURE DEPLOYMENT BEST PRACTICES## 85. Production Security ChecklistSuppose you deploy a Next.js + Node.js application on an AWS EC2 instance or Hostinger VPS.Consider the following:### Application- [ ] Disable production debug mode.- [ ] Validate all incoming data.- [ ] Implement authentication.- [ ] Implement backend authorization.- [ ] Configure secure cookies.- [ ] Apply rate limiting.- [ ] Configure security headers.- [ ] Avoid exposing stack traces.- [ ] Keep dependencies updated.### Server- [ ] Keep operating system updated.- [ ] Disable unnecessary services.- [ ] Restrict SSH access.- [ ] Use firewall rules.- [ ] Use HTTPS.- [ ] Configure reverse proxy correctly.- [ ] Use non-root application users.- [ ] Monitor application logs.### Database- [ ] Restrict database network access.- [ ] Use a dedicated database account.- [ ] Apply least privilege.- [ ] Enable authentication.- [ ] Encrypt connections where appropriate.- [ ] Maintain backups.- [ ] Test database restoration.### Infrastructure- [ ] Protect cloud credentials.- [ ] Restrict security group rules.- [ ] Avoid publicly exposing internal services.- [ ] Use secrets management.- [ ] Monitor infrastructure.- [ ] Configure backup and recovery.- [ ] Scan container images.---# PART 31: COMPLETE SECURE NODE.JS API ARCHITECTURE## 86. Example Secure Application ArchitectureConsider a Node.js and Express API.```text                 Client                   |                   v              HTTPS / TLS                   |                   v              Reverse Proxy                   |                   v             Express Server                   |          +--------+--------+          |        |        |          v        v        v       Helmet   Rate     CORS                Limiter          |        |        |          +--------+--------+                   |                   v             Authentication                   |                   v              Authorization                   |                   v             Input Validation                   |                   v             Business Logic                   |                   v             Data Repository                   |                   v                Database```### Security Layers| Layer | Responsibility ||---|---|| HTTPS | Protect data in transit || Reverse Proxy | Network-level controls and request handling || Helmet | Security-related HTTP headers || Rate Limiter | Restrict excessive requests || CORS | Control browser cross-origin response access || Authentication | Verify identity || Authorization | Verify permissions || Validation | Validate untrusted inputs || Business Logic | Enforce application rules || Repository | Access data safely || Database | Protect persistent information |The important principle is **defense in depth**: security should not depend on one middleware, one library or one infrastructure component.---# PART 32: INTERVIEW QUESTIONS AND ANSWERS## Beginner-Level Questions### Q1. What is Cybersecurity?Cybersecurity is the practice of protecting systems, applications, networks and data from unauthorized access, attacks and damage.### Q2. What is the CIA Triad?It represents Confidentiality, Integrity and Availability.### Q3. What is Application Security?Application Security focuses on identifying and preventing vulnerabilities in software applications throughout their lifecycle.### Q4. What is a Security Vulnerability?A security vulnerability is a weakness that could be exploited to compromise a system.### Q5. What is an Exploit?An exploit is a technique or code that takes advantage of a vulnerability.### Q6. What is Authentication?Authentication verifies the identity of a user or system.### Q7. What is Authorization?Authorization determines what an authenticated user is permitted to access or do.### Q8. What is Hashing?Hashing is a one-way transformation that produces a fixed-length representation of input data.### Q9. What is Salting?Salting adds a unique random value to a password before hashing to protect against precomputed hash attacks.### Q10. What is JWT?JWT is a compact token format used to carry claims between parties.## Intermediate-Level Questions### Q11. What is the difference between hashing and encryption?Hashing is designed to be one-way, whereas encryption is reversible using the correct key.### Q12. Why should passwords not be stored in plain text?A database breach would expose users' actual passwords, potentially compromising accounts across multiple services.### Q13. Why is bcrypt or Argon2 preferred over SHA-256 for password storage?Adaptive password hashing algorithms are intentionally expensive to compute, making large-scale password guessing more difficult.### Q14. What is session-based authentication?It is an authentication approach where the server maintains session state and the client generally stores a session identifier in a cookie.### Q15. What is the difference between access tokens and refresh tokens?Access tokens authorize API access, while refresh tokens obtain new access tokens.### Q16. What is OAuth 2.0?OAuth 2.0 is an authorization framework for delegated access to protected resources.### Q17. What is OpenID Connect?OpenID Connect is an identity layer built on OAuth 2.0 that provides standardized authentication information.### Q18. What is MFA?Multi-Factor Authentication requires two or more independent authentication factors.### Q19. What is RBAC?Role-Based Access Control assigns permissions to roles and users to those roles.### Q20. What is ABAC?Attribute-Based Access Control makes authorization decisions using attributes of users, resources, actions and environments.## Advanced-Level Questions### Q21. What is SQL Injection?SQL Injection occurs when untrusted input changes the intended structure or meaning of a SQL query.### Q22. How can SQL Injection be prevented?Use parameterized queries, safe ORM methods, input validation and least-privilege database accounts.### Q23. What is XSS?Cross-Site Scripting is a vulnerability that causes untrusted content to execute as script in another user's browser.### Q24. What are the three main types of XSS?Stored XSS, Reflected XSS and DOM-Based XSS.### Q25. What is CSRF?CSRF tricks an authenticated user's browser into sending an unwanted request to a trusted application.### Q26. What is SSRF?SSRF occurs when an attacker can influence server-side requests to unintended destinations.### Q27. What is CORS?CORS is a browser mechanism that allows servers to specify which origins may read their responses.### Q28. Does CORS prevent unauthorized API access?No. CORS is not authentication or authorization. Every sensitive API request must be independently authenticated and authorized.### Q29. What is CSP?Content Security Policy restricts permitted sources of scripts and other resources to reduce certain browser-based attacks.### Q30. What is HTTPS?HTTPS is HTTP protected using TLS.### Q31. What is Rate Limiting?Rate Limiting restricts how many requests a client can make within a defined period.### Q32. What is OWASP?OWASP is an organization that publishes resources and guidance for improving application security.### Q33. What is OWASP Top 10?It is a widely used awareness document identifying major web application security risks.### Q34. What is SAST?Static Application Security Testing analyzes source code or artifacts without executing the application.### Q35. What is DAST?Dynamic Application Security Testing evaluates a running application from the outside.### Q36. What is SCA?Software Composition Analysis identifies security and licensing risks in third-party dependencies.### Q37. What is Penetration Testing?Penetration Testing is an authorized security assessment that safely validates whether vulnerabilities can be exploited.### Q38. What is an SBOM?A Software Bill of Materials is an inventory of software components and dependencies.### Q39. What is Defense in Depth?Defense in Depth uses multiple independent security controls so that failure of one control does not compromise the entire system.### Q40. What is Least Privilege?Least Privilege means providing users, applications and services only the permissions necessary to perform their tasks.## Scenario-Based Questions### Q41. Your application stores passwords using SHA-256. What would you change?Use a dedicated password hashing algorithm such as Argon2id, bcrypt or scrypt, with suitable parameters and unique salts.### Q42. A user can access another user's expense by changing the ID in the API URL. What is the problem?This is a Broken Access Control vulnerability, commonly an IDOR-style issue. The backend needs to verify resource ownership or the applicable access policy.### Q43. Your API is receiving thousands of login requests. What would you implement?Rate limiting, account-level protections, MFA, suspicious activity monitoring and appropriate bot protection.### Q44. Your frontend works on localhost but cannot access your backend in production. What would you investigate?- CORS configuration.- Allowed origins.- HTTPS configuration.- Credentials settings.- Cookie SameSite attributes.- Network connectivity.### Q45. A developer accidentally commits an AWS secret to GitHub. What should happen?1. Revoke or rotate the exposed credential immediately.2. Investigate possible unauthorized usage.3. Review access logs.4. Remove the secret from the repository history where appropriate.5. Update secrets management.6. Add secret scanning and prevention controls.Removing the secret from Git history does not make an exposed credential safe. Rotation is essential.### Q46. A React application uses `dangerouslySetInnerHTML`. What should you check?Determine whether the HTML is trusted and properly sanitized. Prefer safe text rendering wherever possible.### Q47. Your application allows users to fetch images from arbitrary URLs. What vulnerability might exist?SSRF. Restrict destinations, validate resolved IPs, control redirects and enforce outbound network restrictions.### Q48. Your database is accessible from every public IP address. What is the problem?This is a security misconfiguration. Restrict network access to trusted application servers and use authentication and least-privilege accounts.### Q49. How would you secure a Next.js and Node.js application?Implement:- Secure authentication.- Backend authorization.- Input validation.- Parameterized queries.- Secure cookies.- HTTPS.- Rate limiting.- CSP and security headers.- Dependency scanning.- Secrets management.- Logging and monitoring.### Q50. What is the difference between SAST, DAST and SCA?SAST analyzes application code, DAST tests a running application and SCA analyzes third-party software components.---# PART 33: PRACTICAL HANDS-ON ASSIGNMENTS## Level 1 – Security Fundamentals| No. | Assignment | Expected Result ||---|---|---|| 1 | Understand CIA Triad | Explain all three principles || 2 | Identify application attack surfaces | Document exposed entry points || 3 | Create a STRIDE threat model | Identify possible threats || 4 | Compare authentication and authorization | Explain their differences || 5 | Create an application security checklist | Document security requirements |## Level 2 – Authentication and Password Security| No. | Assignment | Expected Result ||---|---|---|| 6 | Implement Argon2 password hashing | Store password hashes || 7 | Implement password verification | Verify login credentials || 8 | Implement session authentication | Secure session management || 9 | Implement JWT authentication | Protected API endpoints || 10 | Implement refresh-token rotation | Controlled token renewal || 11 | Implement logout and session revocation | Invalidate authentication state || 12 | Implement MFA | Additional authentication factor || 13 | Implement RBAC | Role-based permissions || 14 | Implement ownership checks | Prevent unauthorized resource access |## Level 3 – Web Application Security| No. | Assignment | Expected Result ||---|---|---|| 15 | Study SQL injection prevention | Parameterized database queries || 16 | Identify unsafe HTML rendering | XSS prevention || 17 | Implement CSRF protection | Protected state-changing requests || 18 | Implement SSRF defenses | Restricted outbound requests || 19 | Configure secure CORS | Trusted origins only || 20 | Configure CSP | Restrictive content policy || 21 | Configure HTTPS | Secure transport || 22 | Add security headers | Helmet configuration || 23 | Implement rate limiting | API abuse protection |## Level 4 – Backend and Infrastructure Security| No. | Assignment | Expected Result ||---|---|---|| 24 | Secure Express error handling | No internal error leakage || 25 | Implement file upload validation | Safe file handling || 26 | Configure `.env` security | Protected configuration || 27 | Configure MySQL permissions | Least privilege || 28 | Secure MongoDB | Authentication and network restrictions || 29 | Secure Docker containers | Non-root execution || 30 | Scan npm dependencies | Identify vulnerable packages || 31 | Review AWS security groups | Restricted network access || 32 | Configure production logging | Security event visibility |## Level 5 – Security Testing| No. | Assignment | Expected Result ||---|---|---|| 33 | Install OWASP ZAP | Local security testing environment || 34 | Inspect API traffic | Understand requests and responses || 35 | Perform authorized passive scanning | Security observations || 36 | Perform authorized active scanning | Vulnerability findings || 37 | Learn Burp Proxy | Inspect HTTP traffic || 38 | Perform SAST | Identify source-code issues || 39 | Perform DAST | Identify runtime issues || 40 | Perform SCA | Identify dependency risks || 41 | Generate an SBOM | Dependency inventory || 42 | Create a security report | Document findings and remediation |---# PART 34: COMPLETE SECURITY PROJECT## Project: Secure Full-Stack Expense Management ApplicationBuild a secure expense management application using the concepts from this phase.### Technology Stack```textFrontend:Next.js + React + TypeScriptBackend:Node.js + ExpressDatabase:MySQLAuthentication:Session-Based or JWT-Based AuthenticationValidation:ZodPassword Hashing:Argon2idSecurity:HelmetRate LimitingCORSHTTPSTesting:JestSupertestPlaywrightSecurity Assessment:OWASP ZAP```### Required Features**1. Authentication**- User registration.- Secure password hashing.- Login.- Logout.- Password reset.- Session or token management.- MFA.**2. Authorization**- Admin role.- Manager role.- Employee role.- Permission checks.- Resource ownership checks.**3. Expense Management**- Create expense.- Read expense.- Update expense.- Delete expense.- Generate reports.- Export Excel.**4. API Security**- Input validation.- SQL injection prevention.- Rate limiting.- Secure CORS.- Error handling.- Authentication middleware.- Authorization middleware.**5. File Security**- Receipt uploads.- File size restrictions.- File type validation.- File signature verification.- Secure storage.- Authorized downloads.**6. Infrastructure Security**- HTTPS.- Secure environment variables.- Database access restrictions.- Secure Docker configuration.- Dependency scanning.- Security logging.### Expected Architecture```text                 Next.js Frontend                        |                        v                    HTTPS                        |                        v                 Express Backend                        |             +----------+----------+             |          |          |             v          v          v         Helmet      Rate       CORS                     Limiting             |          |          |             +----------+----------+                        |                        v                 Authentication                        |                        v                  Authorization                        |                        v                 Input Validation                        |                        v                  Business Logic                        |                        v                 Repository Layer                        |                        v                    MySQL DB```### Final Security TestingVerify that:- Users cannot access other users' expenses.- Employees cannot access admin operations.- Passwords are never stored in plain text.- Authentication tokens are validated correctly.- SQL injection attempts do not change query behavior.- Untrusted content is rendered safely.- Cross-site requests cannot perform unauthorized actions.- File uploads follow strict validation rules.- API requests are rate-limited.- Secrets are not exposed to frontend code.- Internal errors are not leaked to users.- Dependencies are regularly scanned.---# PART 35: QUICK REVISION NOTES| Concept | Meaning ||---|---|| Cybersecurity | Protection of digital systems and information || CIA Triad | Confidentiality, Integrity, Availability || Vulnerability | Security weakness || Exploit | Technique that takes advantage of a vulnerability || Attack Surface | Potential application entry points || Threat Modeling | Identifying threats before implementation || STRIDE | Threat classification framework || Authentication | Verifying identity || Authorization | Checking permissions || Hashing | One-way transformation || Salting | Adding unique random data before password hashing || Argon2id | Adaptive password hashing algorithm || JWT | Compact token format || Access Token | Token used to access protected resources || Refresh Token | Token used to obtain new access tokens || Session | Server-managed authentication state || OAuth 2.0 | Delegated authorization framework || OpenID Connect | Authentication and identity layer || MFA | Multiple independent authentication factors || RBAC | Role-Based Access Control || ABAC | Attribute-Based Access Control || OWASP | Open Worldwide Application Security Project || SQL Injection | Injection into SQL queries || XSS | Cross-Site Scripting || CSRF | Cross-Site Request Forgery || SSRF | Server-Side Request Forgery || CORS | Cross-Origin Resource Sharing || CSP | Content Security Policy || HTTPS | HTTP protected by TLS || TLS | Transport Layer Security || Rate Limiting | Restricting request frequency || SAST | Static Application Security Testing || DAST | Dynamic Application Security Testing || SCA | Software Composition Analysis || SBOM | Software Bill of Materials || OWASP ZAP | Web application security testing tool || Burp Suite | Web application security testing platform || Least Privilege | Minimum necessary permissions || Defense in Depth | Multiple layers of security controls |---# PART 36: PHASE 59 COMPLETION CHECKLIST### Cybersecurity Fundamentals- [ ] Understand Cybersecurity.- [ ] Understand CIA Triad.- [ ] Understand Security Vulnerabilities.- [ ] Understand Exploits.- [ ] Understand Attack Surfaces.- [ ] Understand Threat Modeling.- [ ] Understand STRIDE.### Authentication and Authorization- [ ] Authentication.- [ ] Authorization.- [ ] Password Hashing.- [ ] Password Salting.- [ ] Argon2id.- [ ] bcrypt.- [ ] JWT.- [ ] Access Tokens.- [ ] Refresh Tokens.- [ ] Session Authentication.- [ ] OAuth 2.0.- [ ] OpenID Connect.- [ ] MFA.- [ ] RBAC.- [ ] ABAC.### OWASP and Common Vulnerabilities- [ ] OWASP Top 10:2025.- [ ] Broken Access Control.- [ ] Security Misconfiguration.- [ ] Software Supply Chain Failures.- [ ] Cryptographic Failures.- [ ] Injection.- [ ] Insecure Design.- [ ] Authentication Failures.- [ ] Software or Data Integrity Failures.- [ ] Security Logging and Alerting Failures.- [ ] Mishandling of Exceptional Conditions.- [ ] SQL Injection.- [ ] XSS.- [ ] CSRF.- [ ] SSRF.### API and Web Security- [ ] API Security.- [ ] CORS.- [ ] CSP.- [ ] HTTPS.- [ ] TLS.- [ ] Security Headers.- [ ] Rate Limiting.- [ ] Brute-Force Protection.- [ ] Secure File Uploads.- [ ] Secrets Management.### Secure Development- [ ] Secure Node.js Development.- [ ] Secure React Development.- [ ] Secure Next.js Development.- [ ] Database Security.- [ ] Docker Security.- [ ] Dependency Scanning.- [ ] Secure Deployment.- [ ] Logging and Monitoring.### Security Testing- [ ] Security Testing.- [ ] Vulnerability Assessment.- [ ] Penetration Testing Fundamentals.- [ ] SAST.- [ ] DAST.- [ ] SCA.- [ ] OWASP ZAP.- [ ] Burp Suite.- [ ] SBOM.- [ ] Security Auditing.### Practical Implementation- [ ] Implement secure registration.- [ ] Implement password hashing.- [ ] Implement authentication.- [ ] Implement authorization.- [ ] Implement RBAC.- [ ] Protect APIs.- [ ] Prevent SQL injection.- [ ] Prevent XSS.- [ ] Implement CSRF defenses.- [ ] Configure CORS.- [ ] Configure CSP.- [ ] Implement rate limiting.- [ ] Secure file uploads.- [ ] Scan dependencies.- [ ] Perform an authorized security assessment.---# Official Documentation and Learning Resources| Resource | Official Documentation ||---|---|| OWASP Top 10:2025 | url) |
| OWASP API Security | [OWASP API Security](https://owasp.org/www-project-api-security/?utm_source=chatgpt.com) |
| OWASP Cheat Sheet Series | [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/?utm_source=chatgpt.com) |
| Node.js Security | [Node.js Security Best Practices](https://nodejs.org/learn/getting-started/security-best-practices?utm_source=chatgpt.com) |
| Password Storage | [OWASP Password Storage Guide](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html?utm_source=chatgpt.com) |
| JWT Security | [OWASP JWT Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_Cheat_Sheet?utm_source=chatgpt.com) |
| CSRF Prevention | [OWASP CSRF Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com) |
| CORS Security | [OWASP CORS Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/CORS_Security_Cheat_Sheet.html?utm_source=chatgpt.com) |
| OWASP ZAP | [OWASP ZAP Documentation](https://www.zaproxy.org/docs/?utm_source=chatgpt.com) |
| Burp Suite | [Burp Suite Documentation](https://portswigger.net/burp/documentation?utm_source=chatgpt.com) |
| Next.js | [Next.js Documentation](https://nextjs.org/docs?utm_source=chatgpt.com) |
| Express.js | [Express Documentation](https://expressjs.com/?utm_source=chatgpt.com) |

---

# Phase 59 – Final Summary

In this phase, you learned how to protect web applications, APIs, databases and infrastructure from common security vulnerabilities.

The most important concepts are:

1. **CIA Triad:** Confidentiality, Integrity and Availability.
2. **Authentication:** Verifies who the user is.
3. **Authorization:** Determines what the user can do.
4. **Password Security:** Use Argon2id or another suitable password hashing algorithm.
5. **JWT Security:** Verify signatures, claims, expiration and token purpose.
6. **Session Security:** Protect session identifiers using secure cookie attributes.
7. **OAuth 2.0:** Provides delegated authorization.
8. **OpenID Connect:** Adds authentication and identity information.
9. **RBAC/ABAC:** Control access using roles or attributes.
10. **OWASP Top 10:** Understand major application security risks.
11. **SQL Injection:** Use parameterized queries.
12. **XSS:** Render untrusted content safely.
13. **CSRF:** Protect authenticated state-changing requests.
14. **SSRF:** Restrict server-side outbound requests.
15. **CORS:** Configure trusted browser origins.
16. **CSP:** Restrict permitted content sources.
17. **Rate Limiting:** Reduce API abuse and brute-force attempts.
18. **Secrets Management:** Protect credentials and private keys.
19. **SAST/DAST/SCA:** Integrate security testing into development.
20. **Defense in Depth:** Implement multiple layers of security controls.

### Recommended Practical Learning Order

| Order | Topic | Priority |
|---:|---|---|
| 1 | Password Hashing with Argon2id | Essential |
| 2 | Authentication and Authorization | Essential |
| 3 | JWT and Session Security | Essential |
| 4 | SQL Injection Prevention | Essential |
| 5 | XSS and CSRF Prevention | Essential |
| 6 | API Security and RBAC | Essential |
| 7 | CORS, CSP and Security Headers | Important |
| 8 | Rate Limiting and File Security | Important |
| 9 | OWASP ZAP and Burp Suite | Advanced |
| 10 | SAST, DAST and SCA | Advanced |

---

# Next Phase Preview

## Phase 60 – Advanced System Design, Scalability, High Availability and Distributed Systems

Topics planned:

1. System Design Fundamentals.
2. Low-Level Design (LLD).
3. High-Level Design (HLD).
4. Functional and Non-Functional Requirements.
5. Scalability.
6. Vertical Scaling.
7. Horizontal Scaling.
8. Load Balancing.
9. Reverse Proxy.
10. Caching Strategies.
11. Redis Architecture.
12. CDN Architecture.
13. Database Replication.
14. Database Sharding.
15. Database Partitioning.
16. SQL vs NoSQL Architecture.
17. Database Indexing Strategies.
18. CAP Theorem.
19. ACID Properties.
20. BASE Architecture.
21. Distributed Systems Fundamentals.
22. Microservices Architecture.
23. Monolithic vs Microservices Architecture.
24. API Gateway.
25. Service Discovery.
26. Message Queues.
27. Event-Driven Architecture.
28. Kafka Fundamentals.
29. RabbitMQ Fundamentals.
30. Eventual Consistency.
31. Distributed Transactions.
32. Saga Pattern.
33. Circuit Breaker Pattern.
34. Retry and Timeout Patterns.
35. Idempotency.
36. Rate Limiting Architecture.
37. High Availability.
38. Fault Tolerance.
39. Disaster Recovery.
40. Observability.
41. System Design Case Studies.
42. URL Shortener Architecture.
43. WhatsApp-Like Chat Application Architecture.
44. E-Commerce System Architecture.
45. Video Streaming Architecture.

**Say "OK, Phase 60" when you're ready to continue.**