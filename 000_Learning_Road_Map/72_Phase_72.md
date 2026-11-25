# PHASE 72 – Advanced Cloud Security, DevSecOps, Zero Trust Architecture and Enterprise Security Engineering

**Level:** Advanced → Expert  
**Domain:** Cybersecurity, Application Security, Cloud Security, DevSecOps, Identity Management and Enterprise Security Architecture.

**Primary Technologies and Standards:**

- OWASP Top 10
- OWASP API Security Top 10
- OAuth 2.0
- OpenID Connect
- JWT
- SAML
- AWS IAM
- AWS KMS
- AWS Secrets Manager
- AWS WAF
- Amazon GuardDuty
- Kubernetes Security
- Docker Security
- GitHub Actions
- SAST / DAST / SCA
- SBOM
- SLSA
- SIEM
- Zero Trust Architecture

---

# 1. Learning Objectives

By completing this phase, you should be able to:

1. Understand cybersecurity fundamentals and enterprise security architecture.
2. Identify common application and infrastructure vulnerabilities.
3. Implement secure authentication and authorization.
4. Understand OAuth 2.0, OpenID Connect, SAML and JWT.
5. Implement secure authentication in Node.js and Next.js applications.
6. Protect APIs against common attacks.
7. Understand Zero Trust Architecture.
8. Secure AWS infrastructure and cloud resources.
9. Implement secrets management and encryption.
10. Secure Docker containers and Kubernetes clusters.
11. Integrate security into CI/CD pipelines.
12. Understand SAST, DAST, SCA and penetration testing.
13. Generate and manage SBOMs.
14. Understand software supply chain security.
15. Implement DevSecOps practices.
16. Understand security monitoring and incident response.
17. Design enterprise-grade security architecture.

---

# SECTION 1 – CYBERSECURITY FUNDAMENTALS

## 2. What Is Cybersecurity?

Cybersecurity is the practice of protecting systems, networks, applications, infrastructure and data from unauthorized access, attacks, damage and disruption.

It protects:

- Applications.
- Databases.
- Servers.
- Networks.
- Cloud infrastructure.
- User accounts.
- Authentication systems.
- Business information.
- Personal information.
- Software supply chains.

### Example

Consider an e-commerce application.

It contains:

- User accounts.
- Passwords.
- Addresses.
- Payment information.
- Orders.
- Product information.
- Administrative functionality.

An attacker might attempt to:

1. Access another user's account.
2. Steal authentication tokens.
3. Modify order information.
4. Access confidential customer information.
5. Exploit an API vulnerability.
6. Compromise the application server.

Cybersecurity protects these resources through technical and organizational controls.

---

## 3. The CIA Triad

The CIA Triad is a fundamental information security model.

It consists of:

1. Confidentiality.
2. Integrity.
3. Availability.

### 3.1 Confidentiality

Confidentiality ensures that information is accessible only to authorized individuals or systems.

Example:

A customer should only be able to view their own personal information.

Security techniques:

- Authentication.
- Authorization.
- Encryption.
- Access control.
- Data classification.

### 3.2 Integrity

Integrity ensures that information remains accurate and has not been modified without authorization.

Example:

A customer should not be able to modify the price of an order.

Security techniques:

- Digital signatures.
- Cryptographic hashes.
- Access control.
- Audit logs.
- Database constraints.

### 3.3 Availability

Availability ensures that systems and information remain accessible when required.

Example:

An e-commerce website should remain available during normal traffic spikes and infrastructure failures.

Security techniques:

- Redundancy.
- Load balancing.
- Backups.
- Disaster recovery.
- DDoS protection.
- Fault tolerance.

### CIA Comparison

| Principle | Objective | Example Attack |
|---|---|---|
| Confidentiality | Protect information | Data theft |
| Integrity | Protect information accuracy | Unauthorized modification |
| Availability | Keep systems accessible | DDoS attack |

**Remember:** Most enterprise security controls protect one or more elements of the CIA Triad.

---

## 4. Important Security Terminology

| Term | Meaning |
|---|---|
| Threat | Potential cause of harm |
| Vulnerability | Weakness that can be exploited |
| Exploit | Technique or code that uses a vulnerability |
| Attack | An attempt to compromise a system |
| Risk | Potential impact and likelihood of harm |
| Asset | Something valuable that needs protection |
| Attack Surface | All possible entry points into a system |
| Threat Actor | Individual or group conducting an attack |
| Mitigation | Action that reduces risk |
| Security Control | Mechanism used to protect resources |
| Incident | Security event requiring investigation or response |
| Breach | Compromise involving unauthorized access or disclosure |

### Threat vs Vulnerability vs Risk

Example:

An application does not verify whether a user owns an order.

- Vulnerability: Missing object-level authorization.
- Threat: An attacker attempts to access another user's order.
- Exploit: The attacker changes the order ID in an API request.
- Risk: Confidential customer information is exposed.

---

## 5. Authentication vs Authorization

These are two of the most important security concepts.

### Authentication

Authentication verifies who a user or system is.

Example:

```text
User
 |
 v
Email + Password
 |
 v
Authentication System
 |
 v
Identity Verified
```

### Authorization

Authorization determines what an authenticated identity is allowed to do.

Example:

```text
Authenticated User
        |
        v
Authorization System
        |
   +----+----+
   |         |
   v         v
Read Orders  Admin Access
   |         |
  Yes        No
```

### Comparison

| Authentication | Authorization |
|---|---|
| Verifies identity | Verifies permissions |
| Answers: Who are you? | Answers: What can you access? |
| Happens before access is granted | Happens when access to a resource or operation is evaluated |
| Passwords, MFA, certificates | RBAC, ABAC, policies |

**Important:** Being authenticated does not mean a user is authorized to access every resource.

---

## 6. Identification, Authentication and Authorization

These are related but different processes.

### Identification

The user claims an identity.

Example:

```text
Email: user@example.com
```

### Authentication

The system verifies the identity.

Example:

```text
Password verification + MFA
```

### Authorization

The system checks permissions.

Example:

```text
Can this user delete this product?
```

### Complete Flow

```text
User
 |
 v
Identification
 |
 v
Authentication
 |
 v
Identity Established
 |
 v
Authorization
 |
 v
Access Granted / Denied
```

---

# SECTION 2 – SECURITY MODELS AND ZERO TRUST

## 7. What Is Zero Trust Architecture?

Zero Trust is a security architecture based on the principle that no user, device or service should automatically be trusted simply because it is inside an organization's network.

Traditional security often relies heavily on network boundaries.

Zero Trust focuses on protecting individual resources through explicit authentication, authorization and continuous evaluation.

The core principle is:

**Never grant implicit trust. Verify access explicitly.**

The foundational NIST reference is SP 800-207. [NIST](https://www.nist.gov/publications/zero-trust-architecture?utm_source=chatgpt.com)

### Traditional Network Security

```text
                  Internet
                     |
                     v
                Firewall
                     |
                     v
              Trusted Network
                     |
          +----------+----------+
          |          |          |
          v          v          v
        User       API       Database
```

Once inside the network, systems may receive broader access than necessary.

### Zero Trust Architecture

```text
                    User
                     |
                     v
              Identity Verification
                     |
                     v
              Device Verification
                     |
                     v
              Policy Evaluation
                     |
                     v
             Resource Authorization
                     |
                     v
                 API / DB
```

Every access request is evaluated according to identity, resource, context and policy.

### Important Zero Trust Principles

1. Verify explicitly.
2. Use least-privilege access.
3. Assume breach.
4. Authenticate users and workloads.
5. Validate device and workload identity.
6. Apply contextual access policies.
7. Monitor security events continuously.
8. Limit lateral movement.
9. Protect resources rather than trusting network locations.

---

## 8. Zero Trust Components

### 8.1 Policy Engine

Evaluates whether access should be permitted.

It considers:

- User identity.
- Device identity.
- Resource sensitivity.
- Authentication strength.
- Security policies.
- Contextual signals.

### 8.2 Policy Administrator

Coordinates the decision and establishes or terminates the access path.

### 8.3 Policy Enforcement Point

Enforces the access decision.

Example:

An API gateway or service proxy can act as an enforcement point.

### Architecture

```text
              Access Request
                    |
                    v
             Policy Enforcement
                    |
                    v
               Policy Engine
                    |
              Evaluate Context
                    |
                    v
             Policy Administrator
                    |
              +-----+-----+
              |           |
              v           v
            Allow        Deny
              |
              v
          Protected Resource
```

---

## 9. Zero Trust in Microservices

Consider an application with five services:

- User Service.
- Product Service.
- Order Service.
- Payment Service.
- Notification Service.

A traditional network-based design might assume that all internal services are trusted.

A Zero Trust design requires service identity and authorization.

```text
User Service
     |
     | Authenticated Request
     v
Order Service
     |
     | Authorized Request
     v
Payment Service
     |
     v
External Payment Provider
```

Security requirements:

- Every service has a workload identity.
- Internal communication uses authenticated channels.
- Services receive only required permissions.
- Sensitive operations require authorization.
- Security events are logged.
- Credentials are rotated.

### Practical Example

The Order Service should not have permission to delete user accounts simply because it can communicate with the User Service.

Network connectivity and application authorization are separate controls.

---

## 10. Zero Trust vs Traditional Security

| Traditional Security | Zero Trust |
|---|---|
| Relies heavily on network boundaries | Protects individual resources |
| Often trusts internal network traffic | Verifies access requests |
| Broad internal access may exist | Least privilege is enforced |
| Network location is a major trust factor | Identity and context are major factors |
| Security often focuses on perimeter | Security applies across the environment |

---

# SECTION 3 – OWASP APPLICATION SECURITY

## 11. What Is OWASP?

OWASP stands for Open Worldwide Application Security Project.

It is a nonprofit organization that publishes resources, standards and guidance for application security.

Important OWASP projects:

- OWASP Top 10.
- OWASP API Security Top 10.
- OWASP Application Security Verification Standard.
- OWASP Cheat Sheet Series.
- OWASP Dependency-Check.
- OWASP ZAP.

Official resources:

- [OWASP Official Website](https://owasp.org/?utm_source=chatgpt.com)
- [OWASP Top 10](https://top10.owasp.org/?trk=public_post-text&utm_source=chatgpt.com)
- [OWASP ASVS](https://asvs.owasp.org/?utm_source=chatgpt.com)

---

## 12. OWASP Top 10 – 2025

The OWASP Top 10:2025 identifies ten major categories of web application security risks.

The current list is: [OWASP Top 10](https://top10.owasp.org/?trk=public_post-text\&utm_source=chatgpt.com)

| Rank | Category | Description |
|---|---|---|
| A01 | Broken Access Control | Users access resources or functions without proper permission |
| A02 | Security Misconfiguration | Insecure application or infrastructure configuration |
| A03 | Software Supply Chain Failures | Vulnerabilities in software dependencies and delivery processes |
| A04 | Cryptographic Failures | Incorrect or insufficient protection of sensitive information |
| A05 | Injection | Untrusted input changes the meaning of commands or queries |
| A06 | Insecure Design | Security weaknesses introduced during architecture and design |
| A07 | Authentication Failures | Weak or incorrectly implemented authentication |
| A08 | Software or Data Integrity Failures | Failure to verify integrity and authenticity |
| A09 | Security Logging and Alerting Failures | Insufficient security monitoring and detection |
| A10 | Mishandling of Exceptional Conditions | Improper handling of errors and unusual operating conditions |

Let's understand each category.

### 12.1 A01 – Broken Access Control

Broken Access Control occurs when users can access resources or perform operations they are not authorized to access.

Example:

```http
GET /api/orders/1001
```

Suppose the order belongs to User A.

An attacker changes the ID:

```http
GET /api/orders/1002
```

If the application returns another user's order without checking ownership, it has an access control vulnerability.

#### Insecure Code

```javascript
app.get("/api/orders/:id", async (req, res) => {

  const order = await Order.findByPk(req.params.id);

  res.json(order);

});
```

The application checks whether the order exists but does not verify ownership.

#### Improved Code

```javascript
app.get("/api/orders/:id", authenticate, async (req, res) => {

  const order = await Order.findOne({
    where: {
      id: req.params.id,
      userId: req.user.id
    }
  });

  if (!order) {
    return res.status(404).json({
      message: "Order not found"
    });
  }

  res.json(order);

});
```

This example scopes the lookup to the authenticated user.

For administrative access, implement a separate explicit authorization policy.

**Important:** Never trust a user ID supplied by the client to establish ownership.

---

### 12.2 A02 – Security Misconfiguration

Security misconfiguration occurs when systems are deployed with insecure settings.

Examples:

- Default passwords.
- Publicly exposed databases.
- Debug mode enabled in production.
- Unnecessary open ports.
- Excessive CORS permissions.
- Public cloud storage buckets.
- Detailed stack traces exposed to users.

Example of an insecure CORS configuration:

```javascript
app.use(cors({
  origin: "*"
}));
```

This may be inappropriate for an application that uses authenticated browser requests.

A more restrictive configuration:

```javascript
app.use(cors({
  origin: [
    "https://example.com"
  ],
  methods: [
    "GET",
    "POST",
    "PUT",
    "DELETE"
  ],
  credentials: true
}));
```

The allowed origin list should contain only trusted application origins.

---

### 12.3 A03 – Software Supply Chain Failures

Modern applications depend on external packages.

Example:

```text
Your Application
      |
      +---- Express
      |
      +---- Sequelize
      |
      +---- JSON Web Token Library
      |
      +---- Logging Library
      |
      +---- Other Dependencies
```

A vulnerability in a dependency can affect your application.

Risks:

- Malicious packages.
- Compromised maintainers.
- Vulnerable versions.
- Dependency confusion.
- Typosquatting.
- Compromised build pipelines.
- Malicious deployment artifacts.

Security controls:

- Dependency scanning.
- Lockfiles.
- Package provenance.
- Trusted registries.
- SBOM.
- Signed artifacts.
- Controlled build environments.

---

### 12.4 A04 – Cryptographic Failures

Cryptographic failures happen when sensitive data is not properly protected.

Examples:

- Storing passwords in plaintext.
- Using outdated cryptographic algorithms.
- Weak encryption keys.
- Missing TLS.
- Hardcoded encryption keys.
- Reusing nonces incorrectly.
- Exposing encryption keys in source code.

Important distinction:

| Requirement | Correct Technique |
|---|---|
| Password storage | Password hashing |
| Data confidentiality | Encryption |
| Data integrity | Cryptographic hash or MAC |
| Authenticity | Digital signatures or authenticated protocols |

---

### 12.5 A05 – Injection

Injection occurs when untrusted input is interpreted as part of a command or query.

Common types:

- SQL Injection.
- NoSQL Injection.
- Command Injection.
- LDAP Injection.
- Template Injection.

Example of vulnerable SQL:

```javascript
const query = `
  SELECT * FROM users
  WHERE email = '${email}'
`;
```

An attacker may manipulate the input to change the SQL query.

#### Safer Approach: Parameterized Query

```javascript
const [users] = await db.execute(
  "SELECT * FROM users WHERE email = ?",
  [email]
);
```

Use parameterized queries or safe ORM query interfaces.

Input validation is also necessary, but validation alone does not replace parameterization.

---

### 12.6 A06 – Insecure Design

Insecure design means security was not properly considered during architecture and requirements planning.

Example:

A password reset system allows unlimited reset attempts.

Potential problems:

- Account enumeration.
- Brute-force attacks.
- Token reuse.
- Weak token generation.
- Missing expiration.
- No rate limiting.

A secure design should define:

- Threat models.
- Authentication requirements.
- Authorization policies.
- Abuse prevention.
- Data protection.
- Recovery processes.
- Security testing requirements.

---

### 12.7 A07 – Authentication Failures

Examples:

- Weak passwords.
- Missing MFA.
- Predictable session IDs.
- Long-lived access tokens.
- Missing session invalidation.
- Weak password reset mechanisms.
- Brute-force login attacks.

Security controls:

- Strong password hashing.
- MFA.
- Rate limiting.
- Secure session management.
- Token expiration.
- Refresh token rotation.
- Account recovery protection.

---

### 12.8 A08 – Software or Data Integrity Failures

Applications must verify the integrity and authenticity of important data and software artifacts.

Examples:

- Unsigned software updates.
- Unverified deployment artifacts.
- Insecure deserialization.
- Compromised build processes.
- Tampered packages.

Controls:

- Digital signatures.
- Artifact verification.
- Trusted build systems.
- Secure deployment pipelines.
- Provenance attestations.

---

### 12.9 A09 – Security Logging and Alerting Failures

Security systems must detect and record important security events.

Examples of events to log:

- Login failures.
- Password changes.
- MFA failures.
- Permission changes.
- Administrative actions.
- Sensitive data access.
- Repeated authorization failures.

Example:

```javascript
logger.warn({
  event: "authorization_failure",
  userId: req.user.id,
  resource: "order",
  resourceId: req.params.id,
  requestId: req.id
});
```

Do not log:

- Plaintext passwords.
- Access tokens.
- Refresh tokens.
- Secret keys.
- Full payment credentials.

Logs must also be protected against unauthorized modification and access.

---

### 12.10 A10 – Mishandling of Exceptional Conditions

Applications must handle unexpected states securely.

Examples:

- Database connection failures.
- Invalid input.
- Missing files.
- Network timeouts.
- Resource exhaustion.
- Unhandled promise rejections.
- Partial transaction failures.

Insecure behavior:

```javascript
app.get("/users", async (req, res) => {
  try {
    const users = await getUsers();
    res.json(users);

  } catch (error) {
    res.status(500).json({
      error: error.stack
    });
  }
});
```

This exposes internal stack information.

Improved approach:

```javascript
app.get("/users", async (req, res) => {
  try {
    const users = await getUsers();

    res.json(users);

  } catch (error) {

    logger.error({
      event: "users_fetch_failed",
      error
    });

    res.status(500).json({
      message: "Internal server error"
    });
  }
});
```

---

# SECTION 4 – API SECURITY

## 13. What Is API Security?

API security protects application programming interfaces against unauthorized access, data exposure, abuse and manipulation.

APIs commonly expose:

- Business operations.
- User information.
- Database resources.
- Administrative functions.
- Payment operations.
- Internal services.

A vulnerable API can expose the entire application.

---

## 14. OWASP API Security Top 10 – 2023

The OWASP API Security Top 10 identifies important API-specific risks.

| Rank | Risk | Explanation |
|---|---|---|
| API1 | Broken Object Level Authorization | Accessing another user's object |
| API2 | Broken Authentication | Weak authentication mechanisms |
| API3 | Broken Object Property Level Authorization | Accessing or modifying unauthorized properties |
| API4 | Unrestricted Resource Consumption | Excessive API resource usage |
| API5 | Broken Function Level Authorization | Accessing unauthorized functions |
| API6 | Unrestricted Access to Sensitive Business Flows | Abuse of important business processes |
| API7 | Server Side Request Forgery | Server makes unintended requests |
| API8 | Security Misconfiguration | Insecure API configuration |
| API9 | Improper Inventory Management | Unknown, outdated or undocumented APIs |
| API10 | Unsafe Consumption of APIs | Trusting third-party API responses without sufficient validation |

Source: [OWASP Foundation](https://owasp.org/projects/api-security-project?utm_source=chatgpt.com)

---

## 15. Broken Object Level Authorization (BOLA)

BOLA is one of the most important API vulnerabilities.

Example:

```http
GET /api/users/101
```

The authenticated user has permission to access User 101.

But the attacker changes the identifier:

```http
GET /api/users/102
```

If the API does not verify ownership or permission, another user's information may be exposed.

### Prevention

- Verify object ownership.
- Implement resource-level authorization.
- Never trust client-supplied IDs.
- Test access across multiple accounts.
- Enforce tenant isolation.

Example:

```javascript
const document = await Document.findOne({
  where: {
    id: req.params.id,
    ownerId: req.user.id
  }
});
```

---

## 16. Broken Function Level Authorization

A user may have permission to access ordinary API endpoints but not administrative operations.

Example:

```http
DELETE /api/admin/users/101
```

A regular user should not be allowed to execute this operation.

Implement role or permission checks.

```javascript
function requireRole(role) {
  return (req, res, next) => {

    if (!req.user?.roles?.includes(role)) {
      return res.status(403).json({
        message: "Forbidden"
      });
    }

    next();
  };
}
```

Usage:

```javascript
app.delete(
  "/api/admin/users/:id",
  authenticate,
  requireRole("admin"),
  deleteUser
);
```

For larger systems, use centralized permission policies instead of scattering role checks throughout controllers.

---

## 17. API Rate Limiting

Rate limiting restricts how frequently clients can call an API.

It protects against:

- Brute-force attacks.
- Credential stuffing.
- Excessive scraping.
- Resource exhaustion.
- Automated abuse.

Example using Express:

```javascript
import rateLimit from "express-rate-limit";

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,

  limit: 5,

  standardHeaders: "draft-8",

  legacyHeaders: false
});

app.post(
  "/api/login",
  loginLimiter,
  loginController
);
```

This example limits login attempts to five requests per 15-minute window per rate-limit key.

For production:

- Use a shared store such as Redis when running multiple application instances.
- Configure trusted proxy handling correctly.
- Consider account-based and IP-based controls.
- Return appropriate retry information.
- Avoid revealing whether an account exists.

The OWASP Node.js guidance recommends rate limiting sensitive authentication-related endpoints. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_security_cheat_sheet.html?utm_source=chatgpt.com)

---

## 18. API Input Validation

Never trust incoming API data.

Example:

```javascript
app.post("/api/users", async (req, res) => {

  const { name, email, age } = req.body;

  // Validate all incoming fields.

});
```

A validation library such as Zod can provide structured validation.

```typescript
import { z } from "zod";

const userSchema = z.object({

  name: z.string().min(2).max(100),

  email: z.string().email(),

  age: z.number().int().min(18).max(120)

});

app.post("/api/users", (req, res) => {

  const result = userSchema.safeParse(req.body);

  if (!result.success) {
    return res.status(400).json({
      message: "Invalid input",
      errors: result.error.issues
    });
  }

  const user = result.data;

  res.json(user);
});
```

### Important Validation Rules

Validate:

- Data types.
- Length.
- Numeric ranges.
- Required properties.
- Allowed enum values.
- File sizes.
- File types.
- Nested structures.
- Business constraints.

Use allowlists wherever possible.

---

# SECTION 5 – AUTHENTICATION ARCHITECTURE

## 19. Password Security

Passwords must never be stored in plaintext.

A password should be processed using a password hashing algorithm specifically designed to resist brute-force attacks.

Recommended algorithms include:

- Argon2id.
- bcrypt.
- scrypt.
- PBKDF2 where required by compatibility or compliance constraints.

OWASP recommends Argon2id for new password-storage implementations. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html?utm_source=chatgpt.com)

### Hashing vs Encryption

| Hashing | Encryption |
|---|---|
| One-way transformation | Reversible with the appropriate key |
| Used for password verification | Used for data confidentiality |
| No decryption operation | Decryption is possible |
| Example: Argon2id | Example: AES-GCM |

### Example Using bcrypt

Install:

```bash
npm install bcrypt
```

Hash a password:

```javascript
import bcrypt from "bcrypt";

const password = "StrongPassword123!";

const hashedPassword = await bcrypt.hash(password, 12);

console.log(hashedPassword);
```

Verify:

```javascript
const isValid = await bcrypt.compare(
  password,
  hashedPassword
);
```

For new systems, consider Argon2id and benchmark its parameters on your production infrastructure.

### Important Password Rules

- Never store plaintext passwords.
- Never use MD5 or SHA-1 for password storage.
- Never use ordinary SHA-256 as a password hashing replacement.
- Use a unique salt per password.
- Configure appropriate work factors.
- Support password reset securely.
- Rate-limit login and recovery endpoints.

---

## 20. Multi-Factor Authentication (MFA)

MFA requires more than one authentication factor.

Three common factor categories:

1. Something you know.
2. Something you have.
3. Something you are.

Examples:

| Factor | Example |
|---|---|
| Knowledge | Password |
| Possession | Security key or authenticator device |
| Inherence | Fingerprint or face recognition |

### Example Authentication Flow

```text
User
 |
 v
Enter Email + Password
 |
 v
Password Verification
 |
 v
MFA Challenge
 |
 +---- Authenticator Code
 |
 +---- Security Key
 |
 v
Authentication Complete
 |
 v
Create Session
```

### Types of MFA

- TOTP authenticator applications.
- Hardware security keys.
- Passkeys.
- Push-based authentication.
- SMS codes.

SMS-based MFA is generally weaker than phishing-resistant methods such as FIDO2 security keys and passkeys.

For sensitive applications, prefer phishing-resistant authentication where possible.

---

## 21. Session-Based Authentication

Session-based authentication stores session state on the server or in a server-side session store.

### Architecture

```text
Browser
   |
   v
Login Request
   |
   v
Backend
   |
   v
Verify Credentials
   |
   v
Create Session
   |
   v
Store Session
   |
   v
Send Session Cookie
```

The browser sends the session cookie with subsequent requests.

### Express Example

```javascript
import session from "express-session";

app.use(session({
  name: "app_session",

  secret: process.env.SESSION_SECRET,

  resave: false,

  saveUninitialized: false,

  cookie: {
    httpOnly: true,
    secure: true,
    sameSite: "lax",
    maxAge: 1000 * 60 * 30
  }
}));
```

This is a configuration example, not a complete production setup.

Production requirements:

- Use a persistent session store such as Redis.
- Use HTTPS.
- Rotate the session ID after authentication.
- Destroy sessions on logout.
- Configure cookie domain and path carefully.
- Protect state-changing requests against CSRF.
- Never use the default in-memory session store in production.

---

## 22. Secure Cookies

Cookies can store session identifiers or tokens.

Important attributes:

| Attribute | Purpose |
|---|---|
| HttpOnly | Prevents JavaScript access to the cookie |
| Secure | Sends cookie only over secure connections |
| SameSite | Controls cross-site cookie sending |
| Domain | Restricts applicable hosts |
| Path | Restricts applicable URL paths |
| Max-Age | Sets cookie lifetime |
| Expires | Sets expiration date |

Example:

```http
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Lax; Path=/
```

### HttpOnly

Helps reduce the risk of JavaScript stealing cookies through XSS.

It does not prevent the browser from sending the cookie automatically.

### Secure

Ensures the cookie is sent over HTTPS connections.

### SameSite

Helps control cross-site cookie behavior and can reduce CSRF exposure.

However, cookie configuration alone is not a complete CSRF defense.

---

# SECTION 6 – JWT SECURITY

## 23. What Is JWT?

JWT stands for JSON Web Token.

It is a compact token format commonly used to transmit claims between systems.

A JWT commonly contains three parts:

```text
HEADER.PAYLOAD.SIGNATURE
```

### Example Structure

```text
xxxxx.yyyyy.zzzzz
```

### Header

Contains metadata such as the signing algorithm and token type.

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
  "sub": "101",
  "role": "admin",
  "iat": 1795630000,
  "exp": 1795633600
}
```

### Signature

The signature protects the integrity and authenticity of the token, provided it is correctly verified.

**Important:** A signed JWT is not automatically encrypted.

Its header and payload are normally readable by anyone who possesses the token.

Do not put passwords, API secrets or confidential personal information in an ordinary signed JWT.

---

## 24. JWT Authentication Flow

```text
Browser
   |
   v
Login Request
   |
   v
Authentication Server
   |
   v
Verify Credentials
   |
   v
Create Access Token
   |
   v
Return Token
   |
   v
Browser Stores Token
   |
   v
API Request
   |
   v
Token Verification
   |
   v
Authorization
   |
   v
Protected Resource
```

---

## 25. JWT Example in Node.js

Install:

```bash
npm install jose
```

Create a token:

```javascript
import { SignJWT } from "jose";

const secret = new TextEncoder().encode(
  process.env.JWT_SECRET
);

const token = await new SignJWT({
  role: "user"
})
  .setProtectedHeader({
    alg: "HS256"
  })
  .setSubject("101")
  .setIssuedAt()
  .setExpirationTime("15m")
  .sign(secret);
```

Verify a token:

```javascript
import { jwtVerify } from "jose";

const { payload } = await jwtVerify(
  token,
  secret,
  {
    algorithms: ["HS256"]
  }
);

console.log(payload.sub);
console.log(payload.role);
```

### JWT Verification Rules

Always validate:

- Signature.
- Allowed algorithm.
- Expiration.
- Issuer.
- Audience.
- Required claims.
- Token type and intended use.

Do not accept an arbitrary algorithm specified by an untrusted token header.

---

## 26. Access Token vs Refresh Token

### Access Token

A short-lived credential used to access protected resources.

Example:

```text
Lifetime: 5–15 minutes
```

### Refresh Token

A longer-lived credential used to obtain new access tokens.

Example:

```text
Lifetime: Several days or another policy-defined period
```

The exact lifetime should be determined by application risk and session policy.

### Architecture

```text
             User Login
                 |
                 v
           Authentication
                 |
          +------+------+
          |             |
          v             v
     Access Token   Refresh Token
          |             |
          v             |
       API Access       |
                        |
                        v
                 Token Refresh
                        |
                        v
                 New Access Token
```

### Refresh Token Security

- Rotate refresh tokens.
- Detect token reuse.
- Store refresh-token identifiers or hashes securely.
- Revoke tokens on logout or account compromise.
- Use secure cookie storage where appropriate.
- Apply strict expiration policies.

For public OAuth clients, refresh-token rotation or sender-constrained refresh tokens are important protections under current OAuth security guidance. [RFC Editor](https://www.rfc-editor.org/rfc/rfc9700.html?lang=en\&utm_source=chatgpt.com)

---

## 27. JWT vs Session Authentication

| JWT | Session |
|---|---|
| Token carries claims | Session state is stored server-side |
| Often stateless verification | Requires session lookup |
| Revocation requires additional design | Session can be invalidated centrally |
| Useful for distributed APIs | Useful for browser-based applications |
| Token management is important | Session-store management is important |

**Important:** JWT is not automatically more secure or more scalable than sessions.

Choose based on application architecture, revocation requirements, browser security and operational needs.

---

# SECTION 7 – OAUTH 2.0, OPENID CONNECT AND SAML

## 28. What Is OAuth 2.0?

OAuth 2.0 is an authorization framework.

It allows an application to obtain limited access to protected resources on behalf of a user or another application.

Example:

You want to allow a third-party application to access selected resources from your account without sharing your password.

OAuth provides a delegated authorization mechanism.

### Main OAuth Components

| Component | Meaning |
|---|---|
| Resource Owner | User who owns the data |
| Client | Application requesting access |
| Authorization Server | Issues authorization tokens |
| Resource Server | Hosts protected resources |
| Access Token | Credential used to access resources |
| Refresh Token | Credential used to obtain new access tokens |
| Scope | Defines the requested access permissions |

### OAuth Architecture

```text
                 Resource Owner
                       |
                       v
                     Client
                       |
                       v
              Authorization Server
                       |
                       v
                 Access Token
                       |
                       v
                 Resource Server
                       |
                       v
                 Protected Data
```

---

## 29. OAuth 2.0 Authorization Code Flow

This is a common flow for browser-based applications using a backend-for-frontend or a confidential web client.

```text
User
 |
 v
Application
 |
 v
Authorization Server
 |
 v
User Login and Consent
 |
 v
Authorization Code
 |
 v
Application Backend
 |
 v
Token Exchange
 |
 v
Access Token
 |
 v
Resource Server
```

The application backend exchanges the authorization code for tokens.

### PKCE

PKCE stands for Proof Key for Code Exchange.

It protects the authorization code flow against code interception and injection attacks.

Important values:

- `code_verifier`
- `code_challenge`

Simplified process:

```text
Client
  |
  v
Generate code_verifier
  |
  v
Create code_challenge
  |
  v
Send challenge to Authorization Server
  |
  v
Receive authorization code
  |
  v
Exchange code + verifier
  |
  v
Authorization Server verifies
  |
  v
Issue tokens
```

Use Authorization Code Flow with PKCE for modern OAuth clients.

Current OAuth security best practices are specified in RFC 9700. They recommend PKCE, exact redirect URI matching and avoiding deprecated flows such as the Implicit and Resource Owner Password Credentials grants. [IETF Datatracker](https://datatracker.ietf.org/doc/html/rfc9700/?utm_source=chatgpt.com)

---

## 30. OAuth 2.0 Grant Types

Important OAuth flows:

| Flow | Purpose |
|---|---|
| Authorization Code + PKCE | User-facing applications |
| Client Credentials | Machine-to-machine communication |
| Device Authorization | Devices with limited input capabilities |
| Refresh Token | Obtain new access tokens |

Avoid using the following deprecated or insecure patterns:

- Implicit grant.
- Resource Owner Password Credentials grant.

### Client Credentials Flow

Used when one application needs to access another service without acting on behalf of an interactive user.

Example:

```text
Order Service
      |
      v
Authorization Server
      |
      v
Client Credentials
      |
      v
Access Token
      |
      v
Inventory Service
```

Use narrowly scoped machine identities and credentials.

---

## 31. What Is OpenID Connect?

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0.

OAuth 2.0 primarily addresses authorization.

OIDC adds standardized authentication and identity claims.

### Important OIDC Components

- ID Token.
- UserInfo endpoint.
- Issuer.
- Subject.
- Audience.
- Nonce.
- Discovery document.

### ID Token

An ID Token communicates authentication information about the user.

It is commonly represented as a JWT.

Example claims:

```json
{
  "iss": "https://identity.example.com",
  "sub": "user-101",
  "aud": "web-client",
  "exp": 1795633600,
  "iat": 1795630000
}
```

### OAuth vs OIDC

| OAuth 2.0 | OpenID Connect |
|---|---|
| Authorization framework | Authentication layer built on OAuth |
| Issues access tokens | Adds ID Tokens |
| Grants access to resources | Provides standardized identity information |
| Defines scopes and authorization | Defines identity claims and authentication |

### Important Rule

Do not treat an OAuth access token as an ID Token.

An access token is intended for resource access. An ID Token is intended to convey authentication information to the OIDC client.

---

## 32. What Is SAML?

SAML stands for Security Assertion Markup Language.

It is an XML-based standard commonly used for enterprise Single Sign-On (SSO).

Example:

An employee logs into a company identity provider and gains access to multiple enterprise applications.

### SAML Components

- Identity Provider (IdP).
- Service Provider (SP).
- SAML Assertion.
- Authentication Request.
- Response.

### SAML Architecture

```text
Employee
   |
   v
Enterprise Application
   |
   v
Identity Provider
   |
   v
Authentication
   |
   v
Signed SAML Assertion
   |
   v
Enterprise Application
   |
   v
Access Granted
```

### SAML vs OIDC

| SAML | OIDC |
|---|---|
| XML-based | JSON/JWT-based identity layer |
| Common in enterprise SSO | Common in modern web and mobile applications |
| Uses assertions | Uses ID Tokens |
| Browser-based federation | Browser and native application identity |
| Often integrated with enterprise identity systems | Commonly used by modern identity providers |

---

## 33. OAuth vs OIDC vs SAML

| Feature | OAuth 2.0 | OIDC | SAML |
|---|---|---|---|
| Main purpose | Authorization | Authentication and identity | Authentication and federation |
| Token format | Access tokens vary | JWT ID Token commonly | XML assertion |
| Typical use | API access | Login and identity | Enterprise SSO |
| Modern API integration | Yes | Yes | Less common |
| Enterprise SSO | Can participate | Yes | Very common |

---

# SECTION 8 – WEB APPLICATION SECURITY

## 34. Cross-Site Scripting (XSS)

XSS occurs when an attacker causes malicious JavaScript or other executable content to run in another user's browser.

### Example

An application displays user-generated content without proper output encoding or sanitization.

Potential impact:

- Session theft where tokens are accessible.
- Unauthorized actions.
- Data exposure.
- Content manipulation.
- Phishing.

### Types of XSS

1. Stored XSS.
2. Reflected XSS.
3. DOM-based XSS.

### Stored XSS

Malicious content is stored in the application.

Example:

```text
User submits malicious comment
        |
        v
Database stores content
        |
        v
Another user opens page
        |
        v
Unsafe rendering
```

### Prevention

- Context-aware output encoding.
- Safe templating.
- Avoid unsafe DOM APIs.
- Sanitize HTML when rich HTML is required.
- Apply Content Security Policy.
- Avoid inline JavaScript where possible.

Example:

```javascript
// Avoid:
element.innerHTML = userInput;

// Prefer:
element.textContent = userInput;
```

React escapes text content by default, but unsafe HTML rendering through `dangerouslySetInnerHTML` requires careful sanitization.

---

## 35. Cross-Site Request Forgery (CSRF)

CSRF occurs when a browser is tricked into sending an authenticated request to an application.

This is especially relevant when authentication relies on cookies that the browser sends automatically.

### Example

A user is logged into a banking application.

An attacker causes the browser to submit an unintended transfer request.

### Prevention

- CSRF tokens.
- SameSite cookie settings.
- Origin and Referer validation where appropriate.
- Re-authentication for sensitive operations.
- Avoid state-changing GET requests.

### Example CSRF Token Flow

```text
Browser
   |
   v
Request CSRF Token
   |
   v
Server Issues Token
   |
   v
Browser Submits Form + Token
   |
   v
Server Validates Token
   |
   v
Process Request
```

---

## 36. SQL Injection

SQL Injection occurs when untrusted input changes the structure or meaning of a SQL query.

Vulnerable example:

```javascript
const sql = `
  SELECT * FROM users
  WHERE email = '${email}'
`;
```

Safer:

```javascript
const [rows] = await db.execute(
  "SELECT * FROM users WHERE email = ?",
  [email]
);
```

### Prevention

1. Parameterized queries.
2. Safe ORM methods.
3. Input validation.
4. Least-privilege database accounts.
5. Restricted database network access.
6. Safe error handling.

---

## 37. NoSQL Injection

NoSQL injection can occur when untrusted data is interpreted as query operators or expressions.

Example of unsafe MongoDB code:

```javascript
const user = await User.findOne({
  email: req.body.email,
  password: req.body.password
});
```

If unvalidated objects are accepted, attackers may supply unexpected structures.

Better approach:

```javascript
const email = String(req.body.email);
const password = String(req.body.password);

const user = await User.findOne({
  email
});
```

Then verify the submitted password against the stored password hash.

Also validate request schemas and avoid accepting arbitrary query objects from clients.

---

## 38. Command Injection

Command injection occurs when untrusted input is passed into a system shell.

Dangerous example:

```javascript
import { exec } from "node:child_process";

exec(`convert ${req.query.filename} output.png`);
```

User-controlled input may alter the shell command.

Safer approach:

- Avoid shell execution when possible.
- Use a library or native API.
- Use `spawn` with fixed executable arguments when appropriate.
- Validate arguments against strict allowlists.
- Never allow user input to control arbitrary command options.

Example:

```javascript
import { spawn } from "node:child_process";

const child = spawn(
  "convert",
  [
    validatedInputPath,
    validatedOutputPath
  ],
  {
    shell: false
  }
);
```

The paths must still be validated and constrained to approved directories.

---

## 39. Server-Side Request Forgery (SSRF)

SSRF occurs when an attacker manipulates a server into making unintended requests.

Example:

An application allows users to submit an image URL.

```text
User
 |
 v
Backend Image Downloader
 |
 v
User-Supplied URL
```

If the backend can access internal services, an attacker may attempt to reach:

- Internal APIs.
- Private network resources.
- Cloud metadata services.
- Administrative endpoints.

### Prevention

- Prefer destination allowlists.
- Validate scheme and hostname.
- Resolve and inspect IP addresses.
- Block loopback, private and link-local ranges where inappropriate.
- Revalidate after redirects.
- Disable redirects or restrict their destinations.
- Use controlled outbound network policies.
- Never expose internal metadata endpoints.

OWASP's SSRF guidance explains destination validation and network-layer defenses. [OWASP Foundation](https://owasp.org/projects/api-security-project?utm_source=chatgpt.com)

---

## 40. Directory Traversal

Directory traversal occurs when attackers manipulate file paths to access files outside an intended directory.

Example:

```text
/download?file=../../etc/passwd
```

A vulnerable application might construct a path directly from user input.

Safer approach:

```javascript
import path from "node:path";

const uploadDirectory = path.resolve("uploads");

function getSafePath(filename) {

  const filePath = path.resolve(
    uploadDirectory,
    filename
  );

  if (
    filePath !== uploadDirectory &&
    !filePath.startsWith(uploadDirectory + path.sep)
  ) {
    throw new Error("Invalid file path");
  }

  return filePath;
}
```

In production, also:

- Reject path separators if filenames should be simple names.
- Use server-generated storage names.
- Avoid trusting uploaded filenames.
- Check symlink behavior.
- Enforce access control.

---

# SECTION 9 – ENCRYPTION AND CRYPTOGRAPHY

## 41. What Is Cryptography?

Cryptography protects information through mathematical techniques.

Major applications:

- Confidentiality.
- Integrity.
- Authentication.
- Non-repudiation.
- Secure key exchange.

Important cryptographic categories:

1. Symmetric encryption.
2. Asymmetric encryption.
3. Hashing.
4. Message Authentication Codes.
5. Digital signatures.

---

## 42. Symmetric Encryption

Symmetric encryption uses the same secret key for encryption and decryption.

Example:

```text
Plaintext
   |
   v
Encryption + Secret Key
   |
   v
Ciphertext
   |
   v
Decryption + Same Key
   |
   v
Plaintext
```

Common algorithm:

- AES.

For application data, authenticated encryption such as AES-GCM is commonly preferred.

### Example Use Cases

- Database field encryption.
- File encryption.
- Backup encryption.
- Application-level sensitive data protection.

---

## 43. Asymmetric Encryption

Asymmetric cryptography uses a key pair:

- Public key.
- Private key.

The public key can be shared.

The private key must be protected.

### Simplified Encryption Model

```text
Sender
   |
   v
Encrypt Using Public Key
   |
   v
Ciphertext
   |
   v
Recipient
   |
   v
Decrypt Using Private Key
```

Common algorithms:

- RSA.
- Elliptic Curve Cryptography.

Asymmetric cryptography is used in:

- Digital signatures.
- TLS.
- Identity certificates.
- Key exchange and key establishment.

Modern TLS uses more sophisticated combinations of cryptographic algorithms than simply encrypting all traffic with an RSA key.

---

## 44. Hashing

Hashing converts input data into a fixed-length digest.

Example:

```text
Input: Hello
       |
       v
Hash Function
       |
       v
Digest
```

Properties of a secure cryptographic hash function:

- Deterministic output.
- Fixed-length digest for a given algorithm.
- Preimage resistance.
- Second-preimage resistance.
- Collision resistance.

Common algorithms:

- SHA-256.
- SHA-384.
- SHA-512.
- SHA-3.

### Hashing vs Encryption

| Hashing | Encryption |
|---|---|
| One-way | Reversible with key |
| Integrity verification | Confidentiality |
| No decryption | Decryption |
| SHA-256 | AES-GCM |

**Important:** Do not use ordinary SHA-256 for password storage.

---

## 45. Digital Signatures

Digital signatures help verify authenticity and integrity.

A signer creates a signature using a private key.

The recipient verifies it using the corresponding public key.

```text
Original Data
     |
     v
Hash Data
     |
     v
Sign Digest with Private Key
     |
     v
Digital Signature
     |
     v
Verify Using Public Key
```

Use cases:

- Signed software releases.
- API authentication.
- Certificate infrastructure.
- Signed documents.
- Artifact provenance.

A digital signature does not encrypt the underlying document.

---

## 46. Encryption in Transit vs Encryption at Rest

### Encryption in Transit

Protects data while moving across networks.

Example:

HTTPS using TLS.

### Encryption at Rest

Protects stored data.

Examples:

- Encrypted database storage.
- Encrypted disk volumes.
- Encrypted backups.
- Encrypted object storage.

| Encryption Type | Protects |
|---|---|
| In Transit | Network communication |
| At Rest | Stored information |

Sensitive systems often require both.

---

## 47. TLS and HTTPS

TLS stands for Transport Layer Security.

It protects network communication through encryption, integrity protection and server authentication.

HTTPS is HTTP transported over TLS.

### Simplified TLS Handshake

```text
Client
  |
  v
Client Hello
  |
  v
Server Hello
  |
  v
Server Certificate
  |
  v
Certificate Validation
  |
  v
Key Establishment
  |
  v
Secure Session
  |
  v
Encrypted Communication
```

Important TLS concepts:

- Certificates.
- Certificate Authorities.
- Public/private keys.
- Cipher suites.
- Key exchange.
- Certificate validation.
- Certificate expiration.
- Mutual TLS.

### Mutual TLS (mTLS)

In ordinary server-authenticated TLS, the client verifies the server.

With mTLS, both parties authenticate using certificates.

Common use cases:

- Service-to-service communication.
- Enterprise APIs.
- Internal microservices.
- Zero Trust infrastructure.

---

# SECTION 10 – SECRETS MANAGEMENT

## 48. What Are Secrets?

Secrets are sensitive values used by applications and infrastructure.

Examples:

- Database passwords.
- API keys.
- JWT signing keys.
- OAuth client secrets.
- Private keys.
- Encryption keys.
- Cloud credentials.

Example `.env` file:

```env
DB_HOST=localhost
DB_USER=admin
DB_PASSWORD=secret-password
JWT_SECRET=secret-value
```

Never commit real `.env` files to Git.

Add them to `.gitignore`:

```text
.env
.env.*
!.env.example
```

An `.env.example` should contain placeholder values only.

---

## 49. Secrets Management Systems

Secrets managers store and control sensitive credentials.

Examples:

- AWS Secrets Manager.
- AWS Systems Manager Parameter Store.
- HashiCorp Vault.
- Azure Key Vault.
- Google Secret Manager.

### Architecture

```text
Application
    |
    v
Workload Identity
    |
    v
Secrets Manager
    |
    v
Retrieve Secret
    |
    v
Application Configuration
```

### Why Use a Secrets Manager?

- Centralized storage.
- Access control.
- Secret rotation.
- Audit trails.
- Reduced hardcoding.
- Controlled secret distribution.

### Best Practices

1. Never hardcode secrets.
2. Never commit secrets to Git.
3. Rotate credentials.
4. Restrict access.
5. Use workload identities.
6. Monitor secret access.
7. Separate development and production credentials.
8. Avoid printing secrets in logs.
9. Revoke compromised credentials immediately.

---

# SECTION 11 – AWS CLOUD SECURITY

## 50. AWS Identity and Access Management

AWS IAM manages identities and permissions.

Important components:

- IAM Users.
- IAM Groups.
- IAM Roles.
- IAM Policies.
- Trust Policies.
- Permission Boundaries.
- Service Control Policies.

### IAM Role

An IAM role provides temporary permissions to an identity or service that assumes it.

Example:

```text
EC2 Application
      |
      v
IAM Role
      |
      v
Permission Policy
      |
      v
S3 Bucket
```

Prefer roles and temporary credentials over long-lived access keys.

### Least Privilege Example

A backend application only needs to read files from a specific bucket.

It should receive only:

```text
s3:GetObject
```

for the required resource scope.

It should not receive:

```text
s3:*
iam:*
ec2:*
```

unless there is a specific and justified requirement.

---

## 51. AWS KMS

KMS stands for Key Management Service.

It helps create and manage cryptographic keys.

Important capabilities:

- Key creation.
- Key policies.
- Encryption operations.
- Decryption operations.
- Key rotation options.
- Audit integration.
- Envelope encryption.

### Envelope Encryption

Envelope encryption uses two levels of keys:

1. Data Encryption Key (DEK).
2. Key Encryption Key (KEK), or a KMS-managed key.

### Architecture

```text
          Plaintext Data
                |
                v
          Data Encryption Key
                |
                v
          Encrypt Data
                |
                v
         Encrypted Data
                |
                v
     Encrypt Data Encryption Key
                |
                v
        KMS-Managed Key
```

The encrypted data and encrypted data key can be stored together.

When decryption is required, the authorized application asks KMS to decrypt the data key and then uses it to decrypt the data.

This approach avoids directly using a long-lived master key for every data-encryption operation.

---

## 52. AWS Secrets Manager

AWS Secrets Manager provides centralized secret storage and management.

Example use cases:

- RDS credentials.
- API credentials.
- Database connection secrets.
- Third-party service credentials.

Architecture:

```text
Node.js Application
        |
        v
IAM Role
        |
        v
AWS Secrets Manager
        |
        v
Database Credentials
        |
        v
RDS Database
```

Use an IAM role with narrowly scoped access.

Avoid giving every application access to every secret.

---

## 53. AWS WAF

AWS WAF is a web application firewall.

It helps inspect and filter HTTP/HTTPS requests.

It can help protect against:

- Common web exploits.
- Malicious request patterns.
- Automated traffic.
- IP-based abuse.
- Suspicious request rates.

### Architecture

```text
          Internet
              |
              v
           AWS WAF
              |
              v
         Load Balancer
              |
              v
         Application
```

AWS WAF supports rules, managed rule groups and rate-based controls.

It does not replace secure application code or authorization checks.

---

## 54. AWS Shield

AWS Shield provides DDoS protection.

DDoS stands for Distributed Denial of Service.

An attacker attempts to overwhelm a service with traffic or requests.

### Example

```text
Attacker Network
      |
      +---- Massive Traffic
      |
      +---- Massive Traffic
      |
      +---- Massive Traffic
                |
                v
          Target Website
                |
                v
             Overloaded
```

AWS Shield provides DDoS protection capabilities for supported AWS resources.

WAF and Shield address different but complementary threat categories.

---

## 55. Amazon GuardDuty

Amazon GuardDuty is a threat detection service.

It analyzes supported data sources and activity signals to identify potentially malicious or suspicious behavior.

Examples of findings may involve:

- Suspicious API activity.
- Unusual network communication.
- Credential misuse indicators.
- Potential compromise.
- Malware-related activity on supported resources.

### Architecture

```text
AWS Environment
      |
      v
Security Signals
      |
      v
GuardDuty
      |
      v
Threat Findings
      |
      v
Security Response
```

---

## 56. AWS Security Hub

AWS Security Hub provides centralized security findings and security posture management across supported AWS services and integrations.

It helps security teams:

- Aggregate findings.
- Monitor security posture.
- Evaluate standards.
- Prioritize security issues.
- Coordinate remediation.

### AWS Security Architecture

```text
                  AWS Organization
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
          CloudTrail  GuardDuty   AWS Config
             |           |           |
             +-----------+-----------+
                         |
                         v
                   Security Hub
                         |
                         v
                  Security Team
```

AWS recommends combining preventive controls, least-privilege IAM, CloudTrail, GuardDuty, Security Hub and network security services in a layered security architecture. [AWS Documentation](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/phases.html?utm_source=chatgpt.com)

Official documentation:

- [AWS IAM](https://docs.aws.amazon.com/iam/?utm_source=chatgpt.com)
- [AWS KMS](https://docs.aws.amazon.com/kms/?utm_source=chatgpt.com)
- [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/?utm_source=chatgpt.com)
- [AWS WAF](https://docs.aws.amazon.com/waf/?utm_source=chatgpt.com)
- [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/?utm_source=chatgpt.com)
- [AWS Security Hub](https://docs.aws.amazon.com/securityhub/?utm_source=chatgpt.com)

---

# SECTION 12 – KUBERNETES AND CONTAINER SECURITY

## 57. Container Security

Container security protects:

- Container images.
- Running containers.
- Container registries.
- Runtime environments.
- Host systems.
- Container networks.
- Secrets and configuration.

### Common Container Risks

1. Running as root.
2. Using vulnerable base images.
3. Exposing unnecessary ports.
4. Embedding secrets in images.
5. Using privileged containers.
6. Mounting sensitive host directories.
7. Pulling untrusted images.
8. Ignoring image updates.
9. Running containers with excessive capabilities.

---

## 58. Secure Dockerfile

Example:

```dockerfile
FROM node:22-slim

ENV NODE_ENV=production

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY --chown=node:node . .

USER node

EXPOSE 3000

CMD ["node", "server.js"]
```

Security improvements:

- Uses a defined base image version.
- Installs production dependencies only.
- Runs as a non-root user.
- Uses a dedicated working directory.

For production, also:

- Pin the base image by digest when appropriate.
- Scan images.
- Remove unnecessary packages.
- Avoid copying secrets.
- Use a read-only filesystem when compatible.
- Apply resource limits.

---

## 59. Kubernetes Security Hardening

Kubernetes clusters require multiple security controls.

Important areas:

1. API server security.
2. Authentication.
3. Authorization.
4. Network security.
5. Pod security.
6. Secret management.
7. Image security.
8. Audit logging.
9. Node security.
10. Cluster upgrades.

### Secure Pod Example

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: secure-api

spec:
  automountServiceAccountToken: false

  containers:

    - name: api

      image: example/api:1.0

      securityContext:

        runAsNonRoot: true

        runAsUser: 10001

        allowPrivilegeEscalation: false

        readOnlyRootFilesystem: true

        capabilities:
          drop:
            - ALL
```

This disables automatic service-account token mounting for this Pod and applies restrictive container security settings.

Only disable token mounting when the workload does not need Kubernetes API access.

---

## 60. Kubernetes RBAC Security

RBAC controls what users and service accounts can do.

Example:

A monitoring application should only be allowed to read the resources it needs.

Avoid giving every application:

```text
cluster-admin
```

Instead, define narrow permissions.

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role

metadata:
  name: read-pods
  namespace: monitoring

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

The Role must then be connected to the appropriate identity using a RoleBinding.

---

## 61. Kubernetes Network Security

Use NetworkPolicies to restrict traffic between workloads.

Example architecture:

```text
Frontend
   |
   | Allowed
   v
Backend API
   |
   | Allowed
   v
Database

Frontend
   |
   | Denied
   v
Database
```

The objective is to restrict communication to required paths.

Remember that NetworkPolicy enforcement depends on CNI support.

---

## 62. Kubernetes Secret Security

Kubernetes Secrets are not automatically a complete secrets-management solution.

Security controls:

- Encryption at rest.
- RBAC restrictions.
- External secret management.
- Short-lived credentials where possible.
- Secret rotation.
- Audit logging.
- Restricted Pod access.

Never assume Base64 encoding provides confidentiality.

Official reference: [Kubernetes Security Checklist](https://kubernetes.io/docs/concepts/security/security-checklist/?utm_source=chatgpt.com)

---

# SECTION 13 – DEVSECOPS

## 63. What Is DevSecOps?

DevSecOps integrates security into the complete software development and deployment lifecycle.

Traditional software delivery:

```text
Development
     |
     v
Testing
     |
     v
Deployment
     |
     v
Security Review
```

Security is considered late.

DevSecOps:

```text
Planning
   |
   v
Secure Design
   |
   v
Secure Development
   |
   v
Automated Security Testing
   |
   v
Secure Build
   |
   v
Secure Deployment
   |
   v
Continuous Monitoring
```

Security becomes a continuous responsibility.

### DevSecOps Objectives

- Detect vulnerabilities early.
- Automate security testing.
- Protect source code.
- Secure dependencies.
- Verify build artifacts.
- Protect deployment pipelines.
- Monitor production systems.

NIST's Secure Software Development Framework provides practices that can be integrated into existing development lifecycles. [NIST Computer Security Resource Center](https://csrc.nist.gov/pubs/sp/800/218/final?utm_source=chatgpt.com)

---

## 64. Security Testing Types

Four important security testing categories:

1. SAST.
2. DAST.
3. SCA.
4. IAST.

### 64.1 SAST – Static Application Security Testing

SAST analyzes source code without executing the application.

Example:

```javascript
const query = "SELECT * FROM users WHERE email = '" + email + "'";
```

A static analyzer may identify unsafe SQL query construction.

Tools:

- Semgrep.
- SonarQube.
- CodeQL.
- Checkmarx.

### 64.2 DAST – Dynamic Application Security Testing

DAST tests a running application from the outside.

It sends requests and analyzes responses.

It can identify:

- Reflected XSS.
- Missing security headers.
- Some authentication issues.
- Certain injection vulnerabilities.
- Insecure server configuration.

Tools:

- OWASP ZAP.
- Burp Suite.

### 64.3 SCA – Software Composition Analysis

SCA analyzes third-party dependencies.

Example:

```text
package.json
    |
    v
Dependency Scanner
    |
    v
Identify Packages
    |
    v
Check Known Vulnerabilities
    |
    v
Generate Report
```

Tools:

- npm audit.
- Snyk.
- Dependabot.
- OWASP Dependency-Check.

### 64.4 IAST – Interactive Application Security Testing

IAST analyzes applications while they execute under instrumented testing conditions.

It combines aspects of static and dynamic testing.

---

## 65. SAST vs DAST vs SCA vs IAST

| Type | Input | Execution Required? | Main Purpose |
|---|---|---|---|
| SAST | Source code | No | Identify code weaknesses |
| DAST | Running application | Yes | Identify runtime vulnerabilities |
| SCA | Dependencies | Usually no application execution | Identify vulnerable packages |
| IAST | Instrumented running application | Yes | Analyze behavior during execution |

### Important

No single security scanner detects every vulnerability.

A mature security program combines:

- Code review.
- Automated scanning.
- Threat modeling.
- Manual testing.
- Dependency review.
- Runtime monitoring.

---

## 66. Security Testing in CI/CD

A secure CI/CD pipeline might look like:

```text
Developer Push
      |
      v
Source Code Checkout
      |
      v
Secret Scanning
      |
      v
SAST
      |
      v
Dependency Scanning
      |
      v
Unit Tests
      |
      v
Build Application
      |
      v
Container Build
      |
      v
Container Image Scan
      |
      v
Generate SBOM
      |
      v
Sign Artifact
      |
      v
Deploy to Staging
      |
      v
DAST
      |
      v
Approval
      |
      v
Production Deployment
```

The pipeline should define which findings block a release.

For example:

- Confirmed critical vulnerabilities may block deployment.
- High-risk findings require review.
- Lower-risk findings can be tracked with remediation deadlines.

Do not treat every scanner finding as equally important.

---

## 67. Example GitHub Actions Security Workflow

Create:

`.github/workflows/security.yml`

```yaml
name: Security Checks

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

permissions:
  contents: read
  security-events: write

jobs:

  security:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4

        with:
          node-version: 22

      - name: Install Dependencies
        run: npm ci

      - name: Dependency Audit
        run: npm audit --audit-level=high

      - name: Run Tests
        run: npm test

      - name: Build
        run: npm run build
```

This is a starting point.

A mature workflow should additionally include:

- Secret scanning.
- SAST.
- Container scanning.
- IaC scanning.
- SBOM generation.
- Artifact signing.
- Provenance verification.
- Protected deployment environments.

For production, pin third-party actions to verified commit SHAs rather than relying only on mutable version tags.

---

# SECTION 14 – SOFTWARE SUPPLY CHAIN SECURITY

## 68. What Is Software Supply Chain Security?

Software supply chain security protects everything involved in creating, building, distributing and deploying software.

It includes:

- Source code.
- Developers.
- Dependencies.
- Build tools.
- CI/CD systems.
- Container images.
- Artifact registries.
- Deployment systems.

### Software Supply Chain

```text
Developer
    |
    v
Source Code
    |
    v
Dependencies
    |
    v
Build System
    |
    v
Application Artifact
    |
    v
Container Image
    |
    v
Artifact Registry
    |
    v
Production Deployment
```

An attacker who compromises any part of this chain may compromise the final application.

---

## 69. Software Bill of Materials (SBOM)

SBOM stands for Software Bill of Materials.

It is an inventory of software components and dependencies.

Example:

```text
Application
    |
    +---- Express
    |
    +---- Sequelize
    |
    +---- MySQL Driver
    |
    +---- JWT Library
    |
    +---- Other Dependencies
```

### Why Is SBOM Important?

- Dependency visibility.
- Vulnerability investigation.
- License compliance.
- Supply chain transparency.
- Incident response.
- Software inventory management.

### Common SBOM Formats

1. CycloneDX.
2. SPDX.

CycloneDX is a widely used SBOM and software supply chain specification. [ecma-tc54.github.io](https://ecma-tc54.github.io/ECMA-424/?utm_source=chatgpt.com)

### Example CycloneDX Generation

One Node.js option is the CycloneDX npm tool.

```bash
npm install --save-dev @cyclonedx/cyclonedx-npm
```

Generate:

```bash
npx cyclonedx-npm \
  --output-file sbom.json
```

The exact command options can vary by installed tool version.

Official specification: [CycloneDX](https://cyclonedx.org/?utm_source=chatgpt.com)

---

## 70. SLSA Framework

SLSA stands for Supply-chain Levels for Software Artifacts.

It is a framework for improving software supply chain integrity.

It focuses on the security of the process used to produce software artifacts.

Important concepts:

- Build provenance.
- Build isolation.
- Source integrity.
- Build process security.
- Artifact verification.

### Example

When building a Docker image, the organization should be able to establish:

- Which source repository produced it.
- Which commit was used.
- Which build workflow executed.
- Which builder produced the artifact.
- Whether the artifact has been tampered with.

### SLSA Architecture

```text
Source Repository
       |
       v
Trusted Build System
       |
       v
Build Artifact
       |
       v
Provenance Attestation
       |
       v
Artifact Verification
       |
       v
Production Deployment
```

Official resource: [SLSA Framework](https://slsa.dev/?utm_source=chatgpt.com)

---

## 71. Artifact Signing

Artifact signing helps establish artifact authenticity and integrity.

Example:

```text
Docker Image
     |
     v
Digital Signature
     |
     v
Image Registry
     |
     v
Deployment System
     |
     v
Signature Verification
     |
     v
Deploy Verified Image
```

Tools include:

- Sigstore Cosign.
- Notation.
- Cloud-provider signing integrations.

### Why Sign Images?

Suppose an attacker replaces an image in a registry.

A deployment system that verifies trusted signatures can reject an unauthorized artifact.

### Best Practices

- Sign production artifacts.
- Verify signatures before deployment.
- Protect signing identities.
- Use trusted build provenance.
- Restrict registry access.
- Audit artifact publication.

---

# SECTION 15 – SECURITY SCANNING TOOLS

## 72. Important Security Tools

| Tool | Category | Purpose |
|---|---|---|
| Semgrep | SAST | Static code analysis |
| SonarQube | Code Quality/SAST | Detect code issues and vulnerabilities |
| GitHub CodeQL | SAST | Semantic code analysis |
| OWASP ZAP | DAST | Web application security testing |
| Burp Suite | DAST/Manual Testing | Web security testing |
| npm audit | SCA | Identify vulnerable npm dependencies |
| Snyk | SCA/Container/IaC | Find security vulnerabilities |
| Trivy | Container/IaC/SCA | Scan images, dependencies and configurations |
| Gitleaks | Secret Scanning | Detect exposed secrets |
| Checkov | IaC Scanning | Scan infrastructure configurations |
| Cosign | Artifact Signing | Sign and verify artifacts |
| Dependency-Check | SCA | Identify vulnerable dependencies |

### Example: npm Audit

```bash
npm audit
```

Fix automatically where appropriate:

```bash
npm audit fix
```

**Warning:** Do not blindly apply dependency updates in production. Review breaking changes, run tests and inspect the resulting dependency tree.

### Example: Trivy

Scan an image:

```bash
trivy image node-api:1.0
```

Scan a repository:

```bash
trivy fs .
```

Scan Kubernetes configuration:

```bash
trivy config ./kubernetes
```

---

# SECTION 16 – API GATEWAY AND EDGE SECURITY

## 73. API Gateway Security

An API Gateway acts as an entry point for API traffic.

It can centralize:

- Authentication.
- Authorization.
- Rate limiting.
- Request validation.
- TLS termination.
- Routing.
- Logging.
- API versioning.

Architecture:

```text
              Internet
                  |
                  v
              API Gateway
                  |
         +--------+--------+
         |        |        |
         v        v        v
      User API Product API Order API
```

### Security Responsibilities

An API Gateway can enforce common controls, but backend services must still verify authorization for sensitive resources and operations.

Do not assume that traffic passing through a gateway is automatically safe.

---

## 74. WAF vs API Gateway vs Firewall

| Technology | Main Purpose |
|---|---|
| Network Firewall | Controls network traffic |
| WAF | Filters HTTP/HTTPS requests |
| API Gateway | Routes and manages API requests |
| Application Authentication | Verifies identity |
| Application Authorization | Checks permissions |

These technologies are complementary.

---

## 75. DDoS Protection

DDoS attacks attempt to overwhelm services through large volumes of traffic or resource-intensive requests.

Common categories:

1. Volumetric attacks.
2. Protocol attacks.
3. Application-layer attacks.

### Protection Techniques

- CDN.
- WAF.
- Rate limiting.
- Load balancing.
- Traffic filtering.
- Request quotas.
- Autoscaling with capacity limits.
- Managed DDoS protection.
- Network segmentation.

**Important:** Autoscaling alone does not solve DDoS attacks. Without protective controls, scaling may increase infrastructure cost while the attack continues.

---

# SECTION 17 – SECURITY MONITORING AND SIEM

## 76. What Is SIEM?

SIEM stands for Security Information and Event Management.

It collects, correlates and analyzes security events from multiple systems.

Sources may include:

- Servers.
- Firewalls.
- Cloud services.
- Applications.
- Databases.
- Identity providers.
- Endpoint security systems.

### SIEM Architecture

```text
Applications
Servers
Firewalls
Cloud Services
Identity Systems
     |
     v
Log Collection
     |
     v
SIEM Platform
     |
     v
Event Correlation
     |
     v
Security Alerts
     |
     v
Security Operations Team
```

### Examples of SIEM Platforms

- Splunk Enterprise Security.
- Microsoft Sentinel.
- IBM QRadar.
- Elastic Security.

### Important SIEM Functions

- Log aggregation.
- Event correlation.
- Threat detection.
- Alert generation.
- Incident investigation.
- Security reporting.

---

## 77. IDS vs IPS

IDS stands for Intrusion Detection System.

IPS stands for Intrusion Prevention System.

### IDS

Detects suspicious activity and generates alerts.

### IPS

Can actively block or prevent detected malicious traffic.

| IDS | IPS |
|---|---|
| Detection-focused | Prevention-focused |
| Generates alerts | Can block traffic |
| Often deployed for monitoring | Often deployed inline |
| Does not necessarily block attacks | Can enforce prevention policies |

---

## 78. Security Logging

A secure application should record important security events.

Example:

```javascript
logger.info({
  event: "login_success",
  userId: user.id,
  ip: req.ip,
  requestId: req.id
});
```

Failed login:

```javascript
logger.warn({
  event: "login_failed",
  email: normalizedEmail,
  ip: req.ip,
  requestId: req.id
});
```

Administrative operation:

```javascript
logger.info({
  event: "admin_user_deleted",
  actorId: req.user.id,
  targetUserId: targetUser.id,
  requestId: req.id
});
```

Avoid recording credentials, authentication tokens and unnecessary sensitive personal information.

---

# SECTION 18 – VULNERABILITY MANAGEMENT

## 79. What Is Vulnerability Management?

Vulnerability management is a continuous process of identifying, assessing, prioritizing and remediating security weaknesses.

Typical lifecycle:

```text
Discover Assets
      |
      v
Identify Vulnerabilities
      |
      v
Assess Risk
      |
      v
Prioritize
      |
      v
Remediate
      |
      v
Verify Fix
      |
      v
Continuous Monitoring
```

### Important Activities

1. Asset inventory.
2. Vulnerability scanning.
3. Risk assessment.
4. Patch management.
5. Remediation tracking.
6. Verification.
7. Reporting.

---

## 80. CVE and CVSS

### CVE

CVE stands for Common Vulnerabilities and Exposures.

It provides standardized identifiers for publicly disclosed vulnerabilities.

Example:

```text
CVE-YYYY-NNNNN
```

### CVSS

CVSS stands for Common Vulnerability Scoring System.

It provides a standardized way to assess vulnerability severity.

Typical severity categories:

| Score | Severity |
|---|---|
| 0.0 | None |
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | Critical |

These are the commonly used CVSS severity bands.

### Important

CVSS is not the same as business risk.

A vulnerability's actual priority also depends on:

- Whether the affected component is exposed.
- Whether an exploit exists.
- Whether the vulnerable feature is reachable.
- The value of affected data.
- Business impact.
- Available mitigations.

---

# SECTION 19 – PENETRATION TESTING FUNDAMENTALS

## 81. What Is Penetration Testing?

Penetration testing is an authorized security assessment that evaluates whether vulnerabilities can be exploited.

It helps organizations understand real-world attack paths.

### Types of Penetration Testing

| Type | Description |
|---|---|
| Black Box | Tester has limited internal knowledge |
| White Box | Tester has extensive internal knowledge |
| Gray Box | Tester has partial internal knowledge |

### Penetration Testing Lifecycle

```text
Planning
   |
   v
Scope Definition
   |
   v
Information Gathering
   |
   v
Threat Modeling
   |
   v
Vulnerability Assessment
   |
   v
Controlled Validation
   |
   v
Reporting
   |
   v
Remediation
   |
   v
Retesting
```

### Common Testing Areas

- Authentication.
- Authorization.
- API security.
- Input validation.
- Session management.
- File uploads.
- Cloud configuration.
- Network exposure.
- Dependency vulnerabilities.

**Important:** Perform penetration testing only against systems you own or have explicit written authorization to test. Define scope, testing windows, allowed techniques and emergency contacts before testing.

---

# SECTION 20 – SECURITY INCIDENT RESPONSE

## 82. What Is a Security Incident?

A security incident is an event that compromises or threatens the confidentiality, integrity or availability of systems or data.

Examples:

- Stolen credentials.
- Unauthorized database access.
- Malware infection.
- Exposed API keys.
- Ransomware.
- Data exfiltration.
- Compromised container images.

---

## 83. Incident Response Lifecycle

A typical incident response lifecycle includes:

1. Preparation.
2. Detection and analysis.
3. Containment.
4. Eradication.
5. Recovery.
6. Post-incident improvement.

### Architecture

```text
          Security Alert
                |
                v
           Investigation
                |
                v
             Contain
                |
                v
            Eradicate
                |
                v
             Recover
                |
                v
          Lessons Learned
```

### Example: Exposed Database Credentials

Suppose a developer accidentally commits a production database password to a public Git repository.

Recommended response:

1. Treat the credential as compromised immediately.
2. Rotate the database password.
3. Revoke the exposed credential.
4. Check database access logs.
5. Investigate whether unauthorized access occurred.
6. Review related credentials and permissions.
7. Remove the exposed secret from active code and repository history where appropriate.
8. Introduce secret scanning.
9. Review the incident and improve controls.

Deleting the exposed file alone is insufficient because the credential may already have been copied.

---

# SECTION 21 – COMPLIANCE AND GOVERNANCE

## 84. What Is Security Compliance?

Security compliance means meeting applicable security, privacy and regulatory requirements.

Organizations may need to comply with:

- ISO/IEC 27001.
- SOC 2.
- PCI DSS.
- GDPR.
- HIPAA, where applicable.
- Applicable Indian data protection requirements.

### Important Standards

| Standard | Purpose |
|---|---|
| ISO 27001 | Information Security Management System |
| SOC 2 | Controls relevant to security and other trust service criteria |
| PCI DSS | Payment card data security |
| GDPR | European personal data protection |
| HIPAA | US healthcare information protection |
| NIST CSF | Cybersecurity risk management framework |

### Important Distinction

Security compliance and security effectiveness are related but not identical.

An organization may pass a compliance assessment while still having vulnerabilities.

Compliance should support a broader security program, not replace security engineering.

---

## 85. Security Governance

Security governance establishes how an organization manages security responsibilities and risk.

Important elements:

- Security policies.
- Risk management.
- Access management.
- Data classification.
- Security audits.
- Vendor assessments.
- Incident response.
- Business continuity.
- Security awareness.
- Compliance management.

### Example Security Governance Structure

```text
             Executive Leadership
                     |
                     v
               Security Policy
                     |
          +----------+----------+
          |                     |
          v                     v
    Security Team          Engineering Team
          |                     |
          v                     v
    Risk Management       Secure Development
          |                     |
          +----------+----------+
                     |
                     v
             Security Monitoring
```

---

# SECTION 22 – COMPLETE ENTERPRISE SECURITY PROJECT

## 86. Project: Secure Enterprise E-Commerce Platform

Now combine the concepts into one production-oriented security architecture.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + Express |
| Language | TypeScript |
| Database | MySQL |
| Cache | Redis |
| Authentication | OIDC / OAuth 2.0 |
| Authorization | RBAC + resource-level policies |
| Password Hashing | Argon2id |
| Containerization | Docker |
| Orchestration | Kubernetes |
| Cloud | AWS |
| Secrets | AWS Secrets Manager |
| Encryption | AWS KMS |
| Edge Security | CloudFront + AWS WAF |
| CI/CD | GitHub Actions |
| SAST | Semgrep or CodeQL |
| DAST | OWASP ZAP |
| SCA | npm audit / Snyk |
| Container Scanning | Trivy |
| SBOM | CycloneDX |
| Artifact Signing | Cosign |
| Monitoring | Prometheus + Grafana |
| Security Monitoring | GuardDuty + Security Hub |

---

## 87. Complete Security Architecture

```text
                         USERS
                           |
                           v
                       Route 53
                           |
                           v
                       CloudFront
                           |
                           v
                         AWS WAF
                           |
                           v
                    Application Gateway
                           |
                           v
                    Authentication
                      via OIDC
                           |
                           v
                    Authorization
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          User API      Product API    Order API
             |             |             |
             +-------------+-------------+
                           |
                  +--------+--------+
                  |                 |
                  v                 v
                MySQL              Redis
                  |
                  v
             Encrypted Storage
                  |
                  v
                AWS KMS
```

### Security Monitoring

```text
Applications
     |
     v
Structured Security Logs
     |
     v
Centralized Log Platform
     |
     +---- CloudTrail
     |
     +---- GuardDuty
     |
     +---- Security Hub
     |
     v
Security Monitoring
     |
     v
Incident Response
```

---

## 88. Secure Authentication Architecture

Use an established identity provider where possible.

Example:

```text
Browser
   |
   v
Next.js Application
   |
   v
Identity Provider
   |
   v
OIDC Authentication
   |
   v
Authorization Code + PKCE
   |
   v
Backend Token Validation
   |
   v
API Authorization
   |
   v
Protected Resource
```

Security requirements:

- Use PKCE.
- Validate issuer and audience.
- Validate token expiration.
- Verify token signatures.
- Use secure session cookies.
- Protect state-changing cookie-authenticated requests against CSRF.
- Implement MFA for sensitive accounts.
- Apply resource-level authorization.

---

## 89. Secure API Architecture

Every protected endpoint should follow a predictable security pipeline.

```text
Incoming Request
       |
       v
Request Validation
       |
       v
Authentication
       |
       v
Authorization
       |
       v
Business Validation
       |
       v
Database Operation
       |
       v
Security Logging
       |
       v
Response
```

Example:

```javascript
app.patch(
  "/api/orders/:id",

  authenticate,

  validateOrderUpdate,

  authorizeOrderOwner,

  updateOrderController
);
```

Each middleware should have a clearly defined responsibility.

---

## 90. Secure CI/CD Pipeline

A production security pipeline:

```text
Developer Push
      |
      v
Secret Scanning
      |
      v
SAST
      |
      v
Dependency Scanning
      |
      v
Automated Tests
      |
      v
Build Application
      |
      v
Build Docker Image
      |
      v
Container Security Scan
      |
      v
Generate SBOM
      |
      v
Sign Image
      |
      v
Publish Image
      |
      v
Deploy to Staging
      |
      v
DAST
      |
      v
Security Approval
      |
      v
Production Deployment
      |
      v
Continuous Monitoring
```

### Example Security Gates

| Check | Failure Action |
|---|---|
| Exposed production secret | Block pipeline |
| Critical exploitable dependency | Block or require explicit security approval |
| Failed authentication tests | Block pipeline |
| Critical container vulnerability | Block production release |
| Invalid artifact signature | Reject deployment |
| Failed staging security test | Stop promotion |

Define exception procedures with owners, expiration dates and remediation plans.

---

# SECTION 23 – SECURITY ARCHITECTURE BEST PRACTICES

## 91. Application Security Checklist

| Area | Best Practice |
|---|---|
| Authentication | Use strong authentication |
| Passwords | Use Argon2id or an appropriate password hashing algorithm |
| MFA | Protect sensitive accounts |
| Sessions | Secure cookies and session rotation |
| JWT | Validate signature, issuer, audience and expiration |
| Authorization | Enforce resource-level permissions |
| Input Validation | Validate all external input |
| SQL | Use parameterized queries |
| XSS | Use safe output encoding |
| CSRF | Protect cookie-authenticated state-changing requests |
| CORS | Allow only trusted origins |
| Rate Limiting | Protect sensitive endpoints |
| Logging | Record security-relevant events |
| Error Handling | Avoid exposing stack traces |
| Secrets | Use a secrets manager |
| TLS | Enforce HTTPS |

## 92. Cloud Security Checklist

| Area | Best Practice |
|---|---|
| IAM | Least privilege |
| Credentials | Prefer short-lived credentials |
| Network | Private subnets for sensitive resources |
| Storage | Restrict public access |
| Encryption | Encrypt sensitive data |
| Key Management | Protect keys using KMS or an equivalent system |
| Monitoring | Enable cloud audit logs |
| Threat Detection | Use managed threat detection |
| WAF | Protect public web applications |
| Backups | Test restoration |
| Patching | Keep systems updated |
| Governance | Apply account-level security policies |

## 93. DevSecOps Checklist

| Area | Best Practice |
|---|---|
| Source Code | Code review |
| Dependencies | SCA scanning |
| Secrets | Secret scanning |
| Static Analysis | SAST |
| Runtime Testing | DAST |
| Infrastructure | IaC scanning |
| Container | Image scanning |
| SBOM | Generate component inventory |
| Signing | Sign release artifacts |
| CI/CD | Restrict permissions |
| Authentication | Use OIDC for cloud deployment |
| Deployment | Verify artifacts |
| Monitoring | Continuous security monitoring |

---

# SECTION 24 – IMPORTANT SECURITY COMPARISONS

## 94. Authentication vs Authorization vs Accounting

| Concept | Meaning |
|---|---|
| Authentication | Who are you? |
| Authorization | What are you allowed to do? |
| Accounting | What did you do? |

This is often called the AAA security model.

## 95. Encoding vs Encryption vs Hashing

| Concept | Purpose | Reversible? |
|---|---|---|
| Encoding | Data representation | Yes |
| Encryption | Confidentiality | Yes, with key |
| Hashing | Integrity and one-way transformation | No practical reversal |

Example:

- Base64: Encoding.
- AES-GCM: Encryption.
- SHA-256: Hashing.

## 96. SAST vs DAST vs Penetration Testing

| SAST | DAST | Penetration Testing |
|---|---|---|
| Analyzes code | Tests running application | Evaluates security through authorized testing |
| Automated | Automated | Manual and automated |
| Finds code-level weaknesses | Finds runtime weaknesses | Evaluates exploitable attack paths |
| Usually early in development | During testing or staging | Throughout an assessment engagement |

## 97. OAuth vs JWT

| OAuth 2.0 | JWT |
|---|---|
| Authorization framework | Token format |
| Defines delegated authorization flows | Defines a claims representation |
| Can use multiple token formats | Often used to represent access or identity tokens |
| Can use opaque tokens | Does not define an authorization protocol |

**Important:** OAuth and JWT are not competing technologies.

OAuth can issue JWT-formatted access tokens.

## 98. WAF vs Firewall vs SIEM

| Technology | Purpose |
|---|---|
| Firewall | Controls network traffic |
| WAF | Filters web application requests |
| SIEM | Collects and analyzes security events |

## 99. AWS KMS vs AWS Secrets Manager

| KMS | Secrets Manager |
|---|---|
| Key management and cryptographic operations | Stores and manages secrets |
| Encryption/decryption operations | Credential retrieval and rotation |
| Manages cryptographic keys | Manages passwords, API keys and credentials |
| Supports envelope encryption | Can use KMS for secret encryption |

---

# SECTION 25 – INTERVIEW QUESTIONS

Below are 50 important interview questions.

## Beginner Level

1. What is cybersecurity?
2. Explain the CIA Triad.
3. What is the difference between threat and vulnerability?
4. What is authentication?
5. What is authorization?
6. What is encryption?
7. What is hashing?
8. What is HTTPS?
9. What is TLS?
10. What is OWASP?

## Intermediate Level

11. Explain the OWASP Top 10.
12. What is SQL Injection?
13. What is XSS?
14. What is CSRF?
15. What is SSRF?
16. What is directory traversal?
17. What is API rate limiting?
18. What is BOLA?
19. What is password hashing?
20. What is the difference between bcrypt and Argon2id?
21. What is JWT?
22. What is the difference between JWT and session authentication?
23. What are access tokens and refresh tokens?
24. What is MFA?
25. What are secure cookie attributes?

## Advanced Level

26. Explain OAuth 2.0.
27. Explain Authorization Code Flow with PKCE.
28. What is OpenID Connect?
29. What is the difference between OAuth and OIDC?
30. What is SAML?
31. Explain Zero Trust Architecture.
32. What is mTLS?
33. What is AWS KMS?
34. Explain envelope encryption.
35. What is AWS Secrets Manager?
36. What is AWS WAF?
37. What is GuardDuty?
38. What is Kubernetes RBAC?
39. What is container image scanning?
40. What is DevSecOps?

## Expert Level

41. Explain SAST, DAST, SCA and IAST.
42. How would you design a secure authentication architecture for a Next.js and Node.js application?
43. How would you protect a multi-tenant API against BOLA?
44. How would you implement Zero Trust between microservices?
45. How would you design secure secrets management for Kubernetes?
46. What is an SBOM and why is it important?
47. Explain software supply chain security.
48. How would you design a secure GitHub Actions deployment pipeline using AWS OIDC?
49. How would you respond to an exposed production API key?
50. Design a complete enterprise DevSecOps architecture for a cloud-native application.

---

# SECTION 26 – PRACTICAL ASSIGNMENTS

Complete these assignments progressively.

## Level 1 – Cybersecurity Fundamentals

1. Explain the CIA Triad with examples.
2. Differentiate threats, vulnerabilities and risks.
3. Explain authentication and authorization.
4. Create an application attack-surface diagram.
5. Explain the AAA security model.
6. Compare hashing and encryption.
7. Explain symmetric and asymmetric encryption.
8. Explain TLS and HTTPS.
9. Study the OWASP Top 10:2025.
10. Create a basic application threat model.

## Level 2 – Web Application Security

11. Identify a Broken Access Control vulnerability in a sample application.
12. Implement resource ownership checks.
13. Fix an insecure SQL query.
14. Implement input validation using Zod.
15. Configure secure CORS.
16. Implement centralized error handling.
17. Configure secure HTTP headers.
18. Implement safe HTML rendering.
19. Study XSS attack categories.
20. Implement a CSRF protection strategy.

## Level 3 – Authentication Security

21. Implement password hashing using bcrypt.
22. Implement password hashing using Argon2id.
23. Implement password verification.
24. Add login rate limiting.
25. Implement secure password reset.
26. Implement session-based authentication.
27. Configure HttpOnly cookies.
28. Configure Secure cookies.
29. Implement logout and session invalidation.
30. Implement MFA using an appropriate authentication provider.

## Level 4 – JWT and OAuth

31. Generate JWT access tokens.
32. Verify JWT signatures.
33. Validate JWT expiration.
34. Validate JWT issuer and audience.
35. Implement refresh token rotation.
36. Study OAuth Authorization Code Flow.
37. Implement PKCE in a test application.
38. Configure OIDC login.
39. Implement role-based authorization.
40. Study SAML-based enterprise SSO.

## Level 5 – API Security

41. Study the OWASP API Security Top 10.
42. Implement BOLA prevention.
43. Implement function-level authorization.
44. Add API rate limiting.
45. Add request-size restrictions.
46. Validate file uploads.
47. Implement SSRF protections in a URL-fetching feature.
48. Prevent directory traversal.
49. Implement secure API logging.
50. Create an API security test plan.

## Level 6 – Cryptography and Secrets

51. Generate and verify SHA-256 hashes.
52. Experiment with AES-GCM encryption.
53. Understand digital signatures.
54. Configure HTTPS for a local application.
55. Configure a secrets manager.
56. Remove hardcoded credentials from a project.
57. Implement secret rotation.
58. Study envelope encryption.
59. Configure AWS KMS in a test environment.
60. Implement secure database credential retrieval.

## Level 7 – AWS Security

61. Create an IAM role.
62. Create a least-privilege IAM policy.
63. Configure private and public subnets.
64. Restrict database network access.
65. Configure S3 Block Public Access.
66. Enable encryption for supported storage resources.
67. Store database credentials in Secrets Manager.
68. Configure AWS WAF for a test application.
69. Study GuardDuty findings.
70. Configure centralized security monitoring.

## Level 8 – Container and Kubernetes Security

71. Create a non-root Docker image.
72. Scan a Docker image using Trivy.
73. Remove unnecessary packages from an image.
74. Create a read-only container filesystem configuration.
75. Configure Kubernetes RBAC.
76. Create a restrictive NetworkPolicy.
77. Disable unnecessary service-account token mounting.
78. Configure Kubernetes Pod Security.
79. Secure Kubernetes Secrets.
80. Perform a Kubernetes security configuration review.

## Level 9 – DevSecOps and Supply Chain

81. Add SAST to GitHub Actions.
82. Add dependency scanning.
83. Add secret scanning.
84. Configure DAST using OWASP ZAP.
85. Add IaC scanning.
86. Generate an SBOM.
87. Sign a container image using Cosign.
88. Verify an image signature.
89. Understand build provenance.
90. Implement security gates in CI/CD.

## Level 10 – Enterprise Security Project

91. Create a secure Next.js authentication flow.
92. Create a secure Node.js API.
93. Implement RBAC and resource-level authorization.
94. Configure OAuth/OIDC authentication.
95. Implement MFA for privileged accounts.
96. Deploy the application on Kubernetes.
97. Configure secure secrets management.
98. Integrate AWS KMS.
99. Configure AWS WAF.
100. Configure security monitoring.
101. Add SAST, DAST and SCA.
102. Generate an SBOM.
103. Sign and verify container images.
104. Configure GitHub Actions with AWS OIDC.
105. Create a complete DevSecOps deployment pipeline.
106. Implement Zero Trust service communication.
107. Configure incident alerts.
108. Write an incident response procedure.
109. Conduct an authorized application security assessment.
110. Prepare an enterprise security architecture document.

---

# SECTION 27 – QUICK REVISION NOTES

| Concept | Important Point |
|---|---|
| Cybersecurity | Protection of digital systems and information |
| CIA Triad | Confidentiality, Integrity, Availability |
| Authentication | Verifies identity |
| Authorization | Determines permissions |
| Zero Trust | No implicit trust |
| OWASP | Application security resources and standards |
| Broken Access Control | Unauthorized resource or function access |
| SQL Injection | Manipulation of SQL queries |
| XSS | Malicious browser-side script execution |
| CSRF | Unintended authenticated browser request |
| SSRF | Server makes unintended requests |
| BOLA | Missing object-level authorization |
| MFA | Multiple authentication factors |
| Hashing | One-way transformation |
| Encryption | Reversible confidentiality protection |
| TLS | Secure network communication |
| JWT | Compact token format |
| OAuth 2.0 | Authorization framework |
| OIDC | Identity layer on OAuth 2.0 |
| SAML | XML-based federation and SSO standard |
| PKCE | Protection for authorization code flow |
| KMS | Cryptographic key management |
| Secrets Manager | Centralized secret storage and rotation |
| WAF | Web application request filtering |
| GuardDuty | Threat detection |
| SIEM | Security event analysis |
| IDS | Intrusion detection |
| IPS | Intrusion prevention |
| SAST | Static code security analysis |
| DAST | Dynamic application security testing |
| SCA | Dependency vulnerability analysis |
| IAST | Runtime-assisted application security analysis |
| SBOM | Software component inventory |
| SLSA | Software supply chain security framework |
| Cosign | Artifact signing and verification |
| DevSecOps | Security integrated into DevOps |
| CVE | Vulnerability identifier |
| CVSS | Vulnerability severity scoring |
| Penetration Testing | Authorized security assessment |
| Incident Response | Security incident handling |
| mTLS | Mutual TLS authentication |

---

# SECTION 28 – PHASE 72 COMPLETION CHECKLIST

### Cybersecurity Fundamentals

- [ ] Understand the CIA Triad.
- [ ] Understand threats, vulnerabilities and risks.
- [ ] Differentiate authentication and authorization.
- [ ] Understand Zero Trust Architecture.
- [ ] Understand security governance.

### Application Security

- [ ] Understand OWASP Top 10:2025.
- [ ] Understand API Security Top 10.
- [ ] Prevent SQL Injection.
- [ ] Prevent XSS.
- [ ] Prevent CSRF.
- [ ] Prevent SSRF.
- [ ] Prevent directory traversal.
- [ ] Implement resource-level authorization.
- [ ] Implement API rate limiting.

### Authentication

- [ ] Implement secure password hashing.
- [ ] Understand Argon2id and bcrypt.
- [ ] Implement session authentication.
- [ ] Understand JWT security.
- [ ] Implement refresh token rotation.
- [ ] Understand OAuth 2.0.
- [ ] Understand OIDC.
- [ ] Understand SAML.
- [ ] Understand PKCE.
- [ ] Understand MFA.

### Cryptography

- [ ] Understand symmetric encryption.
- [ ] Understand asymmetric encryption.
- [ ] Understand hashing.
- [ ] Understand digital signatures.
- [ ] Understand TLS.
- [ ] Understand mTLS.
- [ ] Understand encryption at rest.
- [ ] Understand encryption in transit.

### Cloud and Infrastructure Security

- [ ] Configure least-privilege IAM.
- [ ] Understand AWS KMS.
- [ ] Use AWS Secrets Manager.
- [ ] Understand AWS WAF.
- [ ] Understand AWS Shield.
- [ ] Understand GuardDuty.
- [ ] Understand Security Hub.
- [ ] Secure Docker images.
- [ ] Secure Kubernetes workloads.
- [ ] Implement Kubernetes RBAC.

### DevSecOps

- [ ] Understand SAST.
- [ ] Understand DAST.
- [ ] Understand SCA.
- [ ] Understand IAST.
- [ ] Integrate security into GitHub Actions.
- [ ] Generate SBOM.
- [ ] Understand SLSA.
- [ ] Sign container images.
- [ ] Verify deployment artifacts.
- [ ] Implement vulnerability management.

### Enterprise Security

- [ ] Design Zero Trust architecture.
- [ ] Design secure API architecture.
- [ ] Design cloud security architecture.
- [ ] Implement security monitoring.
- [ ] Understand SIEM.
- [ ] Understand incident response.
- [ ] Understand compliance requirements.
- [ ] Complete the enterprise security project.

---

# SECTION 29 – OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

Use these resources to study the topics in greater depth.

### 1. Application Security

1. [OWASP Top 10:2025](https://top10.owasp.org/?trk=public_post-text&utm_source=chatgpt.com)
2. [OWASP API Security Top 10](https://owasp.org/projects/api-security-project?utm_source=chatgpt.com)
3. [OWASP Application Security Verification Standard](https://asvs.owasp.org/?utm_source=chatgpt.com)
4. [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/?utm_source=chatgpt.com)
5. [OWASP Node.js Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_security_cheat_sheet.html?utm_source=chatgpt.com)
6. [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html?utm_source=chatgpt.com)

### 2. Zero Trust and Security Standards

7. [NIST Zero Trust Architecture – SP 800-207](https://www.nist.gov/publications/zero-trust-architecture?utm_source=chatgpt.com)
8. [NIST Secure Software Development Framework](https://csrc.nist.gov/pubs/sp/800/218/final?utm_source=chatgpt.com)
9. [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework?utm_source=chatgpt.com)

### 3. Authentication and Identity

10. [OAuth 2.0 Security Best Current Practice – RFC 9700](https://datatracker.ietf.org/doc/html/rfc9700/?utm_source=chatgpt.com)
11. [OpenID Connect Specifications](https://openid.net/developers/specs/?utm_source=chatgpt.com)
12. [JWT Introduction](https://jwt.io/introduction?utm_source=chatgpt.com)
13. [SAML Technical Overview](https://www.oasis-open.org/standard/saml/?utm_source=chatgpt.com)

### 4. Cloud Security

14. [AWS Security Documentation](https://docs.aws.amazon.com/security/?utm_source=chatgpt.com)
15. [AWS IAM Documentation](https://docs.aws.amazon.com/iam/?utm_source=chatgpt.com)
16. [AWS KMS Documentation](https://docs.aws.amazon.com/kms/?utm_source=chatgpt.com)
17. [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/?utm_source=chatgpt.com)
18. [AWS WAF Documentation](https://docs.aws.amazon.com/waf/?utm_source=chatgpt.com)
19. [Amazon GuardDuty Documentation](https://docs.aws.amazon.com/guardduty/?utm_source=chatgpt.com)

### 5. Container and Kubernetes Security

20. [Docker Security Documentation](https://docs.docker.com/engine/security/?utm_source=chatgpt.com)
21. [Kubernetes Security Documentation](https://kubernetes.io/docs/concepts/security/?utm_source=chatgpt.com)
22. [Kubernetes Security Checklist](https://kubernetes.io/docs/concepts/security/security-checklist/?utm_source=chatgpt.com)

### 6. DevSecOps and Supply Chain

23. [GitHub Actions Security Hardening](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions?utm_source=chatgpt.com)
24. [GitHub CodeQL Documentation](https://docs.github.com/en/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning-with-codeql?utm_source=chatgpt.com)
25. [Semgrep Documentation](https://semgrep.dev/docs/?utm_source=chatgpt.com)
26. [OWASP ZAP Documentation](https://www.zaproxy.org/docs/?utm_source=chatgpt.com)
27. [Trivy Documentation](https://trivy.dev/latest/docs/?utm_source=chatgpt.com)
28. [CycloneDX Specification](https://cyclonedx.org/?utm_source=chatgpt.com)
29. [SLSA Framework](https://slsa.dev/?utm_source=chatgpt.com)
30. [Sigstore Cosign Documentation](https://docs.sigstore.dev/cosign/?utm_source=chatgpt.com)

---

# PHASE 72 – FINAL SUMMARY

You have now covered the major components of enterprise security engineering.

The complete security lifecycle looks like this:

```text
                SECURITY PLANNING
                       |
                       v
                 THREAT MODELING
                       |
                       v
               SECURE ARCHITECTURE
                       |
                       v
               SECURE DEVELOPMENT
                       |
                       v
             APPLICATION SECURITY
                       |
                       v
               AUTHENTICATION
                       |
                       v
               AUTHORIZATION
                       |
                       v
                CLOUD SECURITY
                       |
                       v
                DEVSECOPS
                       |
                       v
             SECURITY AUTOMATION
                       |
                       v
              SECURE DEPLOYMENT
                       |
                       v
             CONTINUOUS MONITORING
                       |
                       v
              INCIDENT RESPONSE
                       |
                       v
              SECURITY IMPROVEMENT
```

### Five Important Takeaways

1. **Security must be designed into applications**, not added only after development.
2. **Authentication and authorization are different.** Verifying a user's identity does not automatically grant access to every resource.
3. **Zero Trust requires explicit verification and least privilege** across users, devices and services.
4. **DevSecOps automates security throughout the software lifecycle**, including code, dependencies, containers and deployments.
5. **Enterprise security is continuous.** Monitoring, vulnerability management, incident response and improvement are ongoing responsibilities.

---

# NEXT PHASE

## PHASE 73 – Advanced Software Architecture, Enterprise Design Patterns, Domain-Driven Design and Large-Scale Application Engineering

Topics planned:

1. Advanced Software Architecture Fundamentals.
2. Monolithic Architecture.
3. Modular Monolithic Architecture.
4. Microservices Architecture.
5. Service-Oriented Architecture (SOA).
6. Event-Driven Architecture.
7. Hexagonal Architecture.
8. Clean Architecture.
9. Onion Architecture.
10. Layered Architecture.
11. Domain-Driven Design (DDD).
12. Strategic DDD.
13. Tactical DDD.
14. Bounded Contexts.
15. Ubiquitous Language.
16. Aggregates and Aggregate Roots.
17. Entities and Value Objects.
18. Domain Events.
19. Application Services.
20. Domain Services.
21. Repository Pattern.
22. Unit of Work Pattern.
23. CQRS.
24. Event Sourcing.
25. Saga Pattern.
26. Outbox Pattern.
27. Anti-Corruption Layer.
28. Strangler Fig Pattern.
29. API Gateway Pattern.
30. Backend for Frontend (BFF).
31. Circuit Breaker Pattern.
32. Bulkhead Pattern.
33. Retry and Timeout Patterns.
34. Dependency Injection.
35. Inversion of Control.
36. Factory Pattern.
37. Strategy Pattern.
38. Adapter Pattern.
39. Decorator Pattern.
40. Observer Pattern.
41. Repository Pattern in Node.js.
42. Enterprise Application Architecture.
43. Multi-Tenant Architecture.
44. Plugin-Based Architecture.
45. Extensible Application Design.
46. Architecture Decision Records (ADR).
47. C4 Architecture Diagrams.
48. Software Architecture Documentation.
49. Enterprise E-Commerce Architecture Project.
50. Complete Scalable SaaS Architecture Project.

**Phase 72 is complete. Continue to Phase 73 when you're ready.**