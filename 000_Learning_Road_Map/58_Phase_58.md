# Phase 58 – Advanced Software Testing, Test Automation and Quality Engineering

**Level:** Advanced  
**Domain:** Software Testing, Test Automation, Quality Assurance and Software Reliability  
**Primary Technologies:** JavaScript, TypeScript, Node.js, Express.js, React.js, Next.js, MySQL, MongoDB, Jest, Vitest, Supertest, Playwright, Cypress and k6.

---

# 1. Learning Objectives

In this phase, you will learn how professional developers and QA engineers test applications, identify defects, automate testing and maintain software quality.

By completing this phase, you should understand:

1. Software Testing Fundamentals.
2. Software Quality Assurance (QA).
3. Quality Control (QC).
4. Manual Testing vs Automated Testing.
5. Software Testing Life Cycle (STLC).
6. Software Development Life Cycle (SDLC).
7. Testing Levels.
8. Testing Types.
9. Unit Testing.
10. Integration Testing.
11. Functional Testing.
12. Regression Testing.
13. Smoke Testing.
14. Sanity Testing.
15. System Testing.
16. Acceptance Testing.
17. Jest.
18. Vitest.
19. Node.js Test Runner.
20. Assertions.
21. Mocking and Stubbing.
22. Test Doubles.
23. Test Fixtures.
24. Test Coverage.
25. API Testing.
26. Supertest.
27. Database Testing.
28. End-to-End Testing.
29. Playwright.
30. Cypress.
31. Component Testing.
32. Contract Testing.
33. Performance Testing.
34. Load Testing.
35. Stress Testing.
36. Spike Testing.
37. Soak Testing.
38. k6 and JMeter.
39. Test-Driven Development (TDD).
40. Behavior-Driven Development (BDD).
41. Continuous Testing.
42. Testing in CI/CD Pipelines.
43. Test Automation Architecture.
44. Quality Engineering Best Practices.

---

# PART 1: SOFTWARE TESTING FUNDAMENTALS

## 2. What Is Software Testing?

Software Testing is the process of evaluating an application to identify defects and verify that it behaves according to its requirements.

The main objectives are:

- Identify bugs.
- Verify functionality.
- Validate business requirements.
- Improve application reliability.
- Prevent unexpected failures.
- Verify security and performance requirements.
- Ensure a good user experience.

### Example

Suppose you are developing an expense management application.

It has a feature for adding expenses.

Requirements:

1. Users can enter an expense amount.
2. Users can select a category.
3. Users can select a date.
4. Users can save the expense.
5. The application displays the saved expense.

Now consider these scenarios:

| Test Case | Expected Result |
|---|---|
| Enter amount 500 | Expense should be saved |
| Enter amount 0 | Validation error |
| Enter negative amount | Validation error |
| Leave amount empty | Validation error |
| Select valid category | Category should be saved |
| Submit without authentication | Request should be rejected |
| Submit duplicate transaction ID | Duplicate should be handled appropriately |

Testing checks whether the application behaves correctly in these situations.

## 3. What Is a Bug?

A Bug is an error, flaw or defect in software that causes unexpected or incorrect behavior.

Example:

You have the following function:

```javascript
function calculateTotal(price, quantity) {
  return price + quantity;
}
```

Expected:

```text
Price = 100
Quantity = 3

Expected Total = 300
```

Actual:

```text
100 + 3 = 103
```

The implementation is incorrect.

Correct implementation:

```javascript
function calculateTotal(price, quantity) {
  return price * quantity;
}
```

## 4. Bug vs Defect vs Error vs Failure

These terms are related but have slightly different meanings.

| Term | Meaning |
|---|---|
| Error | A human mistake |
| Defect | A flaw in a software artifact |
| Bug | Common informal term for a defect |
| Failure | Incorrect behavior observed when software executes |

Example:

1. A developer misunderstands a requirement.
2. The developer writes incorrect code.
3. The code contains a defect.
4. The application calculates the wrong amount.
5. The user experiences a failure.

## 5. What Is Software Quality?

Software Quality describes how well software satisfies its requirements and the needs of its users.

Important quality attributes include:

| Quality Attribute | Meaning |
|---|---|
| Correctness | Produces expected results |
| Reliability | Operates consistently |
| Performance | Responds efficiently |
| Security | Protects data and resources |
| Maintainability | Easy to modify |
| Usability | Easy to use |
| Scalability | Handles increased workload |
| Availability | Remains accessible |
| Compatibility | Works across supported environments |
| Accessibility | Usable by people with different abilities |

Testing contributes to software quality, but testing alone cannot guarantee that software is defect-free.

---

# PART 2: QUALITY ASSURANCE AND QUALITY CONTROL

## 6. What Is Quality Assurance (QA)?

Quality Assurance focuses on improving the processes used to develop software.

Its goal is to prevent defects.

Examples:

- Defining coding standards.
- Establishing testing strategies.
- Reviewing development processes.
- Defining quality gates.
- Establishing test automation practices.
- Improving development workflows.

QA is primarily process-oriented.

## 7. What Is Quality Control (QC)?

Quality Control focuses on evaluating the actual software product to identify defects.

Examples:

- Executing test cases.
- Testing APIs.
- Testing user interfaces.
- Verifying database operations.
- Checking application behavior.

QC is primarily product-oriented.

### QA vs QC

| Feature | QA | QC |
|---|---|---|
| Full Form | Quality Assurance | Quality Control |
| Focus | Development process | Software product |
| Objective | Prevent defects | Detect defects |
| Nature | Process-oriented | Product-oriented |
| Example | Define testing standards | Execute test cases |

### Important

QA and QC work together.

QA helps establish processes that reduce defects, while QC evaluates whether the resulting software meets expectations.

---

# PART 3: SOFTWARE TESTING LIFE CYCLE (STLC)

## 8. What Is STLC?

STLC stands for Software Testing Life Cycle.

It describes the activities performed during the testing process.

### STLC Phases

```text
Requirement Analysis
        |
        v
Test Planning
        |
        v
Test Case Development
        |
        v
Test Environment Setup
        |
        v
Test Execution
        |
        v
Defect Reporting
        |
        v
Test Closure
```

## 9. Requirement Analysis

In this phase, testers understand the requirements.

Example:

Requirement:

A user can register using an email address and password.

Questions:

- Is email mandatory?
- What is the minimum password length?
- Should duplicate emails be allowed?
- Is email verification required?
- What happens when registration fails?

The objective is to identify testable requirements.

## 10. Test Planning

Test planning defines how testing will be performed.

It includes:

- Testing scope.
- Testing objectives.
- Testing strategy.
- Testing tools.
- Resource allocation.
- Test schedule.
- Risks.
- Entry criteria.
- Exit criteria.

Example:

```text
Project: Expense Tracker

Testing Scope:
- User registration
- Login
- Add expense
- Update expense
- Delete expense
- Expense reports

Tools:
- Jest
- Supertest
- Playwright

Database:
- MySQL

Test Environment:
- Development / Staging
```

## 11. Test Case Development

A Test Case describes a specific scenario to verify application behavior.

Example:

| Field | Value |
|---|---|
| Test Case ID | TC_LOGIN_001 |
| Feature | Login |
| Scenario | Login with valid credentials |
| Preconditions | User account exists |
| Test Data | Valid email and password |
| Steps | Enter credentials and click Login |
| Expected Result | User should be logged in |
| Actual Result | To be recorded |
| Status | Pass/Fail |

### Test Case vs Test Scenario

A Test Scenario describes what needs to be tested.

A Test Case explains how to test it, including input data and expected results.

Example:

**Test Scenario:** Verify login functionality.

Possible test cases:

1. Login with valid credentials.
2. Login with invalid password.
3. Login with unregistered email.
4. Login with empty fields.
5. Login with malformed email.

## 12. Test Environment Setup

A Test Environment is the infrastructure in which tests are executed.

It may include:

- Operating system.
- Browser.
- Application version.
- Database.
- API server.
- Environment variables.
- Test accounts.
- Third-party service mocks.

Example:

```text
Frontend: Next.js
Backend: Node.js + Express
Database: MySQL
Browser: Chrome
Testing: Playwright
Environment: Staging
```

## 13. Test Execution

Testers execute test cases and compare actual results with expected results.

Possible outcomes:

- Passed.
- Failed.
- Blocked.
- Skipped.
- Not Executed.

## 14. Defect Reporting

When a test fails because of a product defect, a defect report is created.

A useful bug report contains:

- Bug ID.
- Title.
- Description.
- Environment.
- Steps to reproduce.
- Expected result.
- Actual result.
- Severity.
- Priority.
- Screenshots or logs.
- Reproducibility.

Example:

```text
Bug ID: BUG-101

Title:
Login fails with valid credentials.

Environment:
Chrome, Windows 11, Staging

Steps:
1. Open login page.
2. Enter valid email.
3. Enter valid password.
4. Click Login.

Expected:
User should navigate to dashboard.

Actual:
Application displays "Invalid credentials".

Severity:
High

Priority:
High
```

## 15. Test Closure

Test Closure evaluates the overall testing process.

It includes:

- Test execution summary.
- Passed and failed tests.
- Open defects.
- Test coverage.
- Risks.
- Lessons learned.
- Final testing report.

---

# PART 4: SOFTWARE DEVELOPMENT LIFE CYCLE (SDLC)

## 16. What Is SDLC?

SDLC stands for Software Development Life Cycle.

It defines the overall process of building and maintaining software.

### Common SDLC Phases

```text
Requirement Gathering
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

Testing is one part of SDLC.

STLC focuses specifically on testing activities.

### SDLC vs STLC

| Feature | SDLC | STLC |
|---|---|---|
| Full Form | Software Development Life Cycle | Software Testing Life Cycle |
| Focus | Complete software development | Software testing |
| Activities | Requirements, design, coding, testing, deployment | Test planning, execution, defect reporting |
| Objective | Build and maintain software | Verify software quality |

---

# PART 5: TESTING LEVELS

## 17. What Are Testing Levels?

Testing Levels describe the different stages at which software is tested.

The four commonly recognized levels are:

1. Unit Testing.
2. Integration Testing.
3. System Testing.
4. Acceptance Testing.

### Testing Pyramid

```text
                 /\
                /  \
               / E2E\
              /------\
             / System \
            /----------\
           / Integration\
          /--------------\
         /   Unit Tests   \
        /__________________\
```

Unit tests are generally numerous and fast.

End-to-end tests are generally fewer because they execute complete workflows and involve more infrastructure.

---

## 18. Unit Testing

Unit Testing verifies the behavior of an individual unit of code.

A unit might be:

- A function.
- A method.
- A class.
- A small module.

### Example

```javascript
function calculateDiscount(price, percentage) {
  return price * percentage / 100;
}

module.exports = calculateDiscount;
```

Unit test:

```javascript
const calculateDiscount = require("./discount");

test("calculates 10 percent discount", () => {
  const result = calculateDiscount(1000, 10);

  expect(result).toBe(100);
});
```

### Advantages

- Fast execution.
- Easy debugging.
- Early defect detection.
- Better confidence during refactoring.
- Documents expected behavior.

### What Should You Test?

For the discount function:

```javascript
calculateDiscount(1000, 10);
calculateDiscount(500, 20);
calculateDiscount(0, 10);
calculateDiscount(100, 0);
calculateDiscount(100, 100);
```

Also consider invalid inputs if the function is expected to reject them.

---

## 19. Integration Testing

Integration Testing verifies whether multiple components work correctly together.

Example:

You have:

```text
Order Service
     |
     v
Order Repository
     |
     v
MySQL Database
```

An integration test can verify that:

1. An order is submitted.
2. The service calls the repository.
3. The repository saves the order.
4. The database contains the expected record.

### Unit vs Integration Testing

| Feature | Unit Testing | Integration Testing |
|---|---|---|
| Scope | Individual component | Multiple components |
| Database | Usually mocked or avoided | May use a real test database |
| Speed | Usually fast | Usually slower |
| Objective | Verify isolated behavior | Verify component interaction |
| Example | Test discount calculation | Test order creation with MySQL |

---

## 20. System Testing

System Testing evaluates the complete integrated application against its requirements.

Example:

Test the entire expense management application:

- Registration.
- Login.
- Expense creation.
- Expense updates.
- Expense deletion.
- Monthly reporting.
- Excel export.

It verifies the system as a whole.

## 21. Acceptance Testing

Acceptance Testing determines whether the software meets the acceptance criteria of its intended users or stakeholders.

Example:

A client requests an expense management system.

Acceptance criteria:

1. Users can add expenses.
2. Users can view monthly reports.
3. Users can export expenses to Excel.
4. Admins can manage users.

The client or authorized stakeholders verify that the system meets these requirements.

### Types

**User Acceptance Testing (UAT)**

Performed by users or business representatives to verify that the system satisfies business requirements.

**Alpha Testing**

Testing performed in a controlled environment, often by internal stakeholders.

**Beta Testing**

Testing performed by selected external users in a more realistic usage environment.

---

# PART 6: FUNCTIONAL AND NON-FUNCTIONAL TESTING

## 22. Functional Testing

Functional Testing verifies what the application does.

It checks application functionality against requirements.

Examples:

- User registration.
- Login.
- Password reset.
- Create expense.
- Update expense.
- Delete expense.
- Payment processing.

### Example

Requirement:

A user can add an expense.

Test:

```text
Input:
Amount: 500
Category: Food
Date: 2026-11-25

Expected:
Expense should be saved.
```

## 23. Non-Functional Testing

Non-Functional Testing verifies how well the application performs.

It evaluates quality attributes.

Examples:

- Performance.
- Security.
- Reliability.
- Scalability.
- Usability.
- Accessibility.
- Compatibility.

### Functional vs Non-Functional Testing

| Feature | Functional | Non-Functional |
|---|---|---|
| Focus | What the system does | How the system performs |
| Example | Login works | Login responds quickly |
| Objective | Verify behavior | Verify quality attributes |
| Testing | Feature validation | Performance, security, usability |

---

# PART 7: COMMON SOFTWARE TESTING TYPES

## 24. Smoke Testing

Smoke Testing is a preliminary test that checks whether the most important application functionality works.

Example after deployment:

- Application opens.
- Login page loads.
- User can log in.
- Dashboard loads.
- API is reachable.

The purpose is to determine whether the build is stable enough for more detailed testing.

## 25. Sanity Testing

Sanity Testing is a focused check that verifies whether a specific change or fix appears to work correctly.

Example:

A developer fixes the password reset feature.

Sanity testing checks:

- Password reset page opens.
- Valid reset token works.
- Password is updated.
- Invalid reset token is rejected.

### Smoke vs Sanity Testing

| Feature | Smoke Testing | Sanity Testing |
|---|---|---|
| Scope | Broad, shallow | Narrow, focused |
| Purpose | Check basic build stability | Verify a specific change |
| Timing | Often after a new build | Often after a targeted change |
| Coverage | Critical application functions | Changed or related functionality |

## 26. Regression Testing

Regression Testing verifies that existing functionality still works after code changes.

Example:

You modify the payment module.

You should test:

- Payment creation.
- Order creation.
- Order history.
- Refund processing.
- Invoice generation.

The objective is to identify unintended side effects.

### Why Is Regression Testing Important?

A change in one component can affect other components.

Automated regression tests help detect these issues early.

## 27. Retesting

Retesting means executing a previously failed test case again after a defect has been fixed.

Example:

1. Login test fails.
2. Developer fixes login.
3. Tester executes the same login test again.
4. If it passes, the defect may be marked fixed.

### Retesting vs Regression Testing

| Retesting | Regression Testing |
|---|---|
| Verifies a specific defect fix | Checks for unintended side effects |
| Focuses on previously failed tests | Covers existing related functionality |
| Confirms the fix | Confirms existing behavior remains intact |

## 28. Exploratory Testing

Exploratory Testing involves learning about the application and testing it simultaneously.

Instead of following only predefined test cases, testers explore the application to discover unexpected behavior.

Example:

While testing an expense application, you might:

- Submit the form multiple times.
- Refresh during submission.
- Open multiple browser tabs.
- Change the date repeatedly.
- Enter unusually large amounts.

This can reveal defects that predefined test cases missed.

## 29. Compatibility Testing

Compatibility Testing verifies that the application works across supported environments.

Examples:

- Chrome.
- Firefox.
- Edge.
- Safari.
- Windows.
- macOS.
- Android.
- iOS.

It may also include testing different screen sizes and resolutions.

## 30. Accessibility Testing

Accessibility Testing verifies whether people with different abilities can use the application.

Examples:

- Keyboard navigation.
- Screen reader compatibility.
- Accessible form labels.
- Sufficient color contrast.
- Appropriate semantic HTML.
- Meaningful alternative text.

For web applications, accessibility testing should include both automated checks and manual evaluation.

## 31. Security Testing

Security Testing identifies vulnerabilities and verifies security controls.

Examples:

- Authentication testing.
- Authorization testing.
- SQL injection testing.
- Cross-Site Scripting (XSS) testing.
- Cross-Site Request Forgery (CSRF) testing.
- Session management testing.
- Rate-limiting testing.
- Access control testing.

### Example: Authorization Test

Suppose User A owns an expense.

User B should not be able to access or modify User A's expense simply by changing the ID in the URL.

```text
GET /api/expenses/100
```

The API must verify that the authenticated user has permission to access expense `100`.

---

# PART 8: TESTING TECHNIQUES

## 32. Black Box Testing

Black Box Testing verifies application behavior without examining its internal implementation.

The tester focuses on:

- Inputs.
- Outputs.
- Requirements.
- Expected behavior.

Example:

Send a login request and verify its response.

The tester does not need to know how the authentication service is implemented internally.

## 33. White Box Testing

White Box Testing examines internal code structure and execution paths.

It may involve:

- Statement coverage.
- Branch coverage.
- Condition coverage.
- Path coverage.

Example:

A developer examines whether both branches of an `if` statement are tested.

```javascript
function getDiscount(isPremium) {
  if (isPremium) {
    return 20;
  }

  return 5;
}
```

Tests should verify both premium and non-premium cases.

## 34. Grey Box Testing

Grey Box Testing combines knowledge of internal system structure with external behavior testing.

Example:

A tester understands the database schema and API structure but tests the application through its external interfaces.

### Comparison

| Testing Type | Internal Knowledge | Main Focus |
|---|---|---|
| Black Box | Not required | Inputs and outputs |
| White Box | Required | Internal implementation |
| Grey Box | Partial | Internal knowledge plus external behavior |

---

# PART 9: TEST CASE DESIGN TECHNIQUES

## 35. Equivalence Partitioning

Equivalence Partitioning divides input data into groups that are expected to behave similarly.

Example:

A form accepts ages from 18 to 60.

Possible partitions:

| Partition | Example | Expected |
|---|---:|---|
| Below minimum | 15 | Invalid |
| Valid range | 30 | Valid |
| Above maximum | 65 | Invalid |

Instead of testing every possible age, select representative values from each partition.

## 36. Boundary Value Analysis

Boundary Value Analysis focuses on the edges of valid and invalid input ranges.

For an age field accepting 18–60:

Test:

```text
17
18
19
59
60
61
```

Boundary testing is useful because many input validation defects occur at range boundaries.

## 37. Decision Table Testing

Decision Table Testing is useful when output depends on multiple conditions.

Example:

A user can access a protected feature only when:

- Logged in.
- Has an admin role.

| Logged In | Admin Role | Access |
|---|---|---|
| No | No | Denied |
| No | Yes | Denied |
| Yes | No | Denied |
| Yes | Yes | Allowed |

This helps ensure all important combinations are tested.

## 38. State Transition Testing

State Transition Testing verifies how a system behaves when moving between states.

Example:

```text
Pending
   |
   v
Confirmed
   |
   v
Shipped
   |
   v
Delivered
```

Possible invalid transitions:

- Pending → Delivered.
- Delivered → Shipped.
- Cancelled → Confirmed.

Testing should verify both valid and invalid transitions.

---

# PART 10: AUTOMATED TESTING FUNDAMENTALS

## 39. What Is Test Automation?

Test Automation means using software tools and scripts to execute tests automatically.

Instead of manually repeating the same tests, developers create automated test scripts.

Example:

Manual testing:

1. Open browser.
2. Navigate to login page.
3. Enter email.
4. Enter password.
5. Click Login.
6. Verify dashboard.

Automated testing:

A script performs these steps and verifies the result.

### Benefits

- Faster repeated testing.
- Reduced manual effort.
- Consistent execution.
- Easier regression testing.
- CI/CD integration.
- Better test reporting.

### Limitations

Automation also introduces costs:

- Initial development effort.
- Test maintenance.
- Flaky tests.
- Environment management.
- Test data management.

Not every test should be automated.

---

# PART 11: JEST

## 40. What Is Jest?

Jest is a JavaScript testing framework.

It provides:

- Test execution.
- Assertions.
- Mocking.
- Code coverage.
- Snapshot testing.
- Test organization.

It is commonly used in JavaScript and React projects.

Official documentation:

https://jestjs.io/docs/getting-started

## 41. Install Jest

For a Node.js project:

```bash
npm install --save-dev jest
```

Update `package.json`:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

## 42. Write Your First Jest Test

Create:

```text
src/
  calculator.js

tests/
  calculator.test.js
```

`calculator.js`:

```javascript
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = {
  add,
  subtract
};
```

`calculator.test.js`:

```javascript
const {
  add,
  subtract
} = require("../src/calculator");

test("adds two numbers", () => {
  expect(add(10, 20)).toBe(30);
});

test("subtracts two numbers", () => {
  expect(subtract(20, 10)).toBe(10);
});
```

Run:

```bash
npm test
```

## 43. Jest Test Structure

```javascript
describe("Calculator", () => {
  test("addition", () => {
    expect(10 + 20).toBe(30);
  });

  test("subtraction", () => {
    expect(20 - 10).toBe(10);
  });
});
```

### Important Jest Functions

| Function | Purpose |
|---|---|
| `describe()` | Groups related tests |
| `test()` | Defines a test |
| `it()` | Alternative to `test()` |
| `expect()` | Starts an assertion |
| `beforeEach()` | Runs before every test |
| `afterEach()` | Runs after every test |
| `beforeAll()` | Runs before the test group |
| `afterAll()` | Runs after the test group |
| `jest.fn()` | Creates a mock function |
| `jest.spyOn()` | Spies on an existing method |

## 44. Jest Matchers

Matchers verify expected results.

```javascript
expect(10).toBe(10);

expect({ name: "John" }).toEqual({
  name: "John"
});

expect(true).toBeTruthy();

expect(false).toBeFalsy();

expect(null).toBeNull();

expect([1, 2, 3]).toContain(2);

expect("hello").toMatch(/hello/);

expect(() => {
  throw new Error("Failed");
}).toThrow("Failed");
```

### toBe vs toEqual

`toBe()` checks strict equality.

It is commonly used for primitive values and object identity.

`toEqual()` checks deep equality of object and array contents.

Example:

```javascript
const user1 = { name: "John" };
const user2 = { name: "John" };

expect(user1).toEqual(user2); // Pass

expect(user1).toBe(user2); // Fail
```

The two objects have equal properties but are different object references.

## 45. Testing Asynchronous Code

Node.js applications frequently use Promises and async/await.

Example function:

```javascript
async function getUser() {
  return {
    id: 1,
    name: "John"
  };
}
```

Test:

```javascript
test("gets user", async () => {
  const user = await getUser();

  expect(user.name).toBe("John");
});
```

Testing rejected promises:

```javascript
async function getInvalidUser() {
  throw new Error("User not found");
}

test("throws an error", async () => {
  await expect(
    getInvalidUser()
  ).rejects.toThrow("User not found");
});
```

---

# PART 12: MOCKING AND STUBBING

## 46. What Is Mocking?

Mocking replaces a real dependency with a controlled test implementation.

Suppose your service depends on a database.

You do not necessarily need a real database for every unit test.

You can mock the repository.

### Example

Service:

```javascript
class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }

  async getUser(id) {
    const user = await this.userRepository.findById(id);

    if (!user) {
      throw new Error("User not found");
    }

    return user;
  }
}
```

Test:

```javascript
describe("UserService", () => {
  test("returns a user", async () => {
    const mockRepository = {
      findById: jest.fn().mockResolvedValue({
        id: 1,
        name: "John"
      })
    };

    const userService = new UserService(
      mockRepository
    );

    const user = await userService.getUser(1);

    expect(user.name).toBe("John");

    expect(
      mockRepository.findById
    ).toHaveBeenCalledWith(1);
  });
});
```

### Why Is Mocking Useful?

- Avoid external API calls.
- Avoid database dependencies.
- Control error scenarios.
- Make tests faster.
- Test individual components in isolation.

## 47. What Is Stubbing?

A Stub provides predefined responses to a dependency.

Example:

```javascript
const userRepository = {
  findById: async () => ({
    id: 1,
    name: "John"
  })
};
```

It returns predictable data.

## 48. What Is Spying?

A Spy observes how a function is called.

It can track:

- Number of calls.
- Arguments.
- Return values.
- Invocation order.

Example:

```javascript
const logger = {
  log(message) {
    console.log(message);
  }
};

const spy = jest.spyOn(logger, "log");

logger.log("User created");

expect(spy).toHaveBeenCalledWith("User created");
```

### Mock vs Stub vs Spy

| Type | Purpose |
|---|---|
| Mock | Replaces dependency and can verify interactions |
| Stub | Provides predefined responses |
| Spy | Observes calls to an existing function |

These terms sometimes overlap in testing frameworks and testing literature.

---

# PART 13: VITEST

## 49. What Is Vitest?

Vitest is a modern JavaScript and TypeScript testing framework.

It integrates particularly well with Vite-based projects.

It provides:

- Test execution.
- Assertions.
- Mocking.
- Code coverage.
- Watch mode.
- Browser testing integrations.

Official documentation:

https://vitest.dev/guide/

## 50. Install Vitest

```bash
npm install -D vitest
```

Add scripts:

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

## 51. Vitest Example

`calculator.js`:

```javascript
export function add(a, b) {
  return a + b;
}
```

`calculator.test.js`:

```javascript
import { describe, it, expect } from "vitest";

import { add } from "./calculator";

describe("Calculator", () => {
  it("adds two numbers", () => {
    expect(add(10, 20)).toBe(30);
  });
});
```

Run:

```bash
npm run test:run
```

### Jest vs Vitest

| Feature | Jest | Vitest |
|---|---|---|
| Primary ecosystem | JavaScript testing | Vite-oriented testing |
| TypeScript | Supported through configuration/tooling | Strong TypeScript integration |
| Mocking | Built-in | Built-in |
| Watch mode | Yes | Yes |
| Code coverage | Yes | Yes |
| Vite integration | Possible, but not native | Native |
| Typical use | Node.js and React projects | Vite and modern frontend projects |

Both are useful. Choose based on the project ecosystem rather than assuming one is universally better.

---

# PART 14: NODE.JS BUILT-IN TEST RUNNER

## 52. What Is the Node.js Test Runner?

Node.js includes a built-in test runner through the `node:test` module.

It allows you to write tests without installing an external testing framework.

Official documentation:

https://nodejs.org/api/test.html

### Example

`calculator.test.js`:

```javascript
const test = require("node:test");
const assert = require("node:assert");

function add(a, b) {
  return a + b;
}

test("adds two numbers", () => {
  assert.strictEqual(add(10, 20), 30);
});
```

Run:

```bash
node --test
```

### Why Use It?

- No external testing framework required.
- Native Node.js support.
- Suitable for simple test suites.
- Integrates with Node.js test execution.

### Jest vs Vitest vs Node Test Runner

| Feature | Jest | Vitest | Node Test Runner |
|---|---|---|---|
| External package | Yes | Yes | No |
| Assertions | Built-in | Built-in | Use `node:assert` |
| Mocking | Built-in | Built-in | Supported through Node APIs |
| Watch mode | Yes | Yes | Supported in modern Node.js versions |
| Vite integration | Limited | Excellent | Not specifically designed for Vite |
| Best fit | General JS testing | Vite projects | Native Node.js testing |

---

# PART 15: TEST FIXTURES AND TEST DATA

## 53. What Is a Test Fixture?

A Test Fixture is a predefined environment or set of data required to execute a test.

Example:

A login test requires an existing user.

Instead of manually creating the user every time, prepare test data.

```javascript
const testUser = {
  id: 1,
  name: "Test User",
  email: "test@example.com",
  password: "hashed_password"
};
```

A fixture may include:

- Database records.
- Mock objects.
- Test files.
- Configuration.
- Authentication tokens.

## 54. Test Setup and Teardown

Setup prepares the environment before testing.

Teardown cleans up after testing.

Example with Jest:

```javascript
describe("User Tests", () => {
  beforeAll(async () => {
    // Connect to test database
  });

  beforeEach(async () => {
    // Reset test data
  });

  afterEach(async () => {
    // Clean up test records
  });

  afterAll(async () => {
    // Close database connection
  });

  test("creates user", async () => {
    // Test logic
  });
});
```

### Important

Do not run tests against production databases.

Use a dedicated test database, isolated test schema or controlled test containers.

---

# PART 16: TEST COVERAGE

## 55. What Is Code Coverage?

Code Coverage measures how much of the source code is executed during testing.

It helps identify code areas that are not being exercised.

### Common Coverage Metrics

**1. Statement Coverage**

Measures the percentage of executable statements that were executed.

**2. Branch Coverage**

Measures the percentage of decision branches that were executed.

**3. Function Coverage**

Measures how many functions were called during testing.

**4. Line Coverage**

Measures how many executable lines were executed.

### Example

```javascript
function calculateDiscount(price, isPremium) {
  if (isPremium) {
    return price * 0.20;
  }

  return price * 0.05;
}
```

Test:

```javascript
test("calculates premium discount", () => {
  expect(
    calculateDiscount(1000, true)
  ).toBe(200);
});
```

The test executes only the premium branch.

The non-premium branch remains untested.

### Coverage Command

With Jest:

```bash
npx jest --coverage
```

With Vitest:

```bash
npx vitest run --coverage
```

### Example Coverage Report

```text
File             % Stmts  % Branch  % Funcs  % Lines
----------------------------------------------------
calculator.js      100       100      100      100
user.service.js     80        60       75       80
order.service.js    65        50       70       65
```

### Is 100% Coverage Necessary?

Not always.

100% coverage does not guarantee bug-free software.

A test can execute every line but still fail to verify the correct behavior.

Focus on meaningful test cases, critical business rules and important edge conditions.

---

# PART 17: API TESTING

## 56. What Is API Testing?

API Testing verifies whether an API behaves according to its requirements.

It checks:

- HTTP methods.
- Request parameters.
- Headers.
- Authentication.
- Authorization.
- Request body.
- Response body.
- HTTP status codes.
- Error responses.
- Response time.

### Example API

```http
POST /api/users
Content-Type: application/json
```

Request:

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

Expected response:

```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "John",
    "email": "john@example.com"
  }
}
```

## 57. What Should You Test in an API?

| Test Area | Example |
|---|---|
| HTTP Method | POST creates a resource |
| Status Code | Successful creation returns 201 |
| Request Validation | Missing email returns validation error |
| Authentication | Missing token is rejected |
| Authorization | Unauthorized users cannot access protected resources |
| Response Structure | Response contains expected fields |
| Database | Correct data is persisted |
| Error Handling | Server errors do not expose internals |
| Pagination | Page and limit behave correctly |
| Filtering | Filters return expected records |
| Sorting | Records are ordered correctly |
| Rate Limiting | Excessive requests are controlled |

---

# PART 18: SUPERTEST

## 58. What Is Supertest?

Supertest is a library used to test HTTP servers and APIs.

It is commonly used with Node.js and Express applications.

Documentation:

https://github.com/ladjs/supertest

### Install

```bash
npm install --save-dev supertest
```

## 59. Example Express Application

`app.js`:

```javascript
const express = require("express");

const app = express();

app.use(express.json());

app.get("/api/health", (req, res) => {
  res.status(200).json({
    success: true,
    message: "Server is running"
  });
});

app.post("/api/users", (req, res) => {
  const { name, email } = req.body;

  if (!name || !email) {
    return res.status(400).json({
      success: false,
      message: "Name and email are required"
    });
  }

  return res.status(201).json({
    success: true,
    data: {
      name,
      email
    }
  });
});

module.exports = app;
```

`server.js`:

```javascript
const app = require("./app");

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

Separating `app.js` and `server.js` makes it easier to import the Express application during tests without opening a network port unnecessarily.

## 60. Test the API

`app.test.js`:

```javascript
const request = require("supertest");

const app = require("./app");

describe("Health API", () => {
  test("returns server status", async () => {
    const response = await request(app)
      .get("/api/health");

    expect(response.status).toBe(200);

    expect(response.body.success).toBe(true);
  });
});
```

Test POST:

```javascript
describe("User API", () => {
  test("creates a user", async () => {
    const response = await request(app)
      .post("/api/users")
      .send({
        name: "John",
        email: "john@example.com"
      });

    expect(response.status).toBe(201);

    expect(response.body.data.name).toBe("John");
  });

  test("rejects missing email", async () => {
    const response = await request(app)
      .post("/api/users")
      .send({
        name: "John"
      });

    expect(response.status).toBe(400);
  });
});
```

### Important API Testing Best Practices

- Test success and failure scenarios.
- Test authentication and authorization.
- Test invalid input.
- Test edge cases.
- Verify response structure.
- Use isolated test data.
- Avoid depending on production services.
- Test actual persistence in integration tests.

---

# PART 19: DATABASE TESTING

## 61. What Is Database Testing?

Database Testing verifies that database operations work correctly and preserve data integrity.

It includes:

- Insert operations.
- Update operations.
- Delete operations.
- Read operations.
- Constraints.
- Relationships.
- Transactions.
- Indexes.
- Stored procedures where applicable.
- Data consistency.

### Example

Suppose you have:

```text
users
-----
id
name
email

expenses
--------
id
user_id
amount
category
```

Testing should verify:

1. Users can be created.
2. Duplicate emails are rejected when uniqueness is required.
3. Expenses reference valid users.
4. Expenses cannot contain invalid amounts.
5. Deleting a user follows the intended relationship policy.
6. Database transactions maintain consistency.

## 62. Database Testing Strategies

### Strategy 1: In-Memory Database

Use an in-memory database when its behavior sufficiently matches the behavior being tested.

Be careful: a different database engine may behave differently from your production database.

### Strategy 2: Test Database

Use a dedicated database instance or schema.

Example:

```text
Production:
expense_tracker

Testing:
expense_tracker_test
```

### Strategy 3: Test Containers

Run databases in containers for isolated integration testing.

Example:

```text
Jest
  |
  v
Node.js API
  |
  v
Temporary MySQL Container
```

Tools such as Testcontainers can help create and manage disposable test dependencies.

### Strategy 4: Database Transactions

Some tests can use transactions that are rolled back after execution.

This helps keep the test database clean.

However, this approach depends on the application's transaction behavior and how the test environment manages connections.

---

# PART 20: END-TO-END TESTING

## 63. What Is End-to-End Testing?

End-to-End (E2E) Testing verifies a complete application workflow from the user's perspective.

It tests interactions between multiple system components.

Example:

```text
Open Website
     |
     v
Register User
     |
     v
Login
     |
     v
Open Dashboard
     |
     v
Add Expense
     |
     v
View Expense
     |
     v
Logout
```

An E2E test verifies that the entire workflow works.

### Why Is E2E Testing Important?

A unit test might prove that the expense calculation function works.

An integration test might prove that the expense service can save data.

But an E2E test can verify that:

- The user can open the form.
- The form accepts input.
- The API receives the request.
- The database stores the record.
- The frontend displays the result.

---

# PART 21: PLAYWRIGHT

## 64. What Is Playwright?

Playwright is a browser automation and testing framework.

It supports:

- Chromium.
- Firefox.
- WebKit.

It provides capabilities for:

- End-to-end testing.
- Browser automation.
- UI testing.
- Cross-browser testing.
- Screenshots.
- Video recording.
- Trace recording.
- Network interception.

Official documentation:

https://playwright.dev/docs/intro

## 65. Install Playwright

For a new project:

```bash
npm init playwright@latest
```

The installer helps configure:

- Test directory.
- Browser installation.
- Playwright configuration.
- Example tests.

## 66. Playwright Project Structure

```text
project/
│
├── tests/
│   ├── login.spec.js
│   └── dashboard.spec.js
│
├── playwright.config.js
│
├── package.json
│
└── node_modules/
```

## 67. First Playwright Test

```javascript
const { test, expect } = require("@playwright/test");

test("homepage loads", async ({ page }) => {
  await page.goto("http://localhost:3000");

  await expect(page).toHaveTitle(/Home/);
});
```

Run:

```bash
npx playwright test
```

Run in headed mode:

```bash
npx playwright test --headed
```

Open the HTML report:

```bash
npx playwright show-report
```

## 68. Testing Login Functionality

```javascript
const { test, expect } = require("@playwright/test");

test("user can log in", async ({ page }) => {
  await page.goto("http://localhost:3000/login");

  await page.getByLabel("Email").fill(
    "john@example.com"
  );

  await page.getByLabel("Password").fill(
    "Password123"
  );

  await page.getByRole("button", {
    name: "Login"
  }).click();

  await expect(page).toHaveURL(/dashboard/);

  await expect(
    page.getByText("Welcome")
  ).toBeVisible();
});
```

### Important Playwright Locators

| Locator | Purpose |
|---|---|
| `getByRole()` | Locate by accessible role |
| `getByText()` | Locate by visible text |
| `getByLabel()` | Locate form controls by label |
| `getByPlaceholder()` | Locate by placeholder |
| `getByTestId()` | Locate by test ID |
| `locator()` | Use CSS or other supported selectors |

Prefer user-facing locators such as roles and labels where possible.

They generally produce more resilient tests than selectors based on internal CSS classes.

## 69. Playwright Assertions

```javascript
await expect(page).toHaveURL("/dashboard");

await expect(
  page.getByText("Success")
).toBeVisible();

await expect(
  page.getByRole("button")
).toBeEnabled();

await expect(
  page.locator(".error")
).toHaveCount(0);
```

## 70. Playwright Hooks

```javascript
test.beforeEach(async ({ page }) => {
  await page.goto("http://localhost:3000");
});

test.afterEach(async ({ page }) => {
  // Cleanup if required
});
```

Other useful hooks:

- `test.beforeAll()`
- `test.afterAll()`
- `test.beforeEach()`
- `test.afterEach()`

## 71. Playwright Configuration

Example:

```javascript
const { defineConfig } = require("@playwright/test");

module.exports = defineConfig({
  testDir: "./tests",

  fullyParallel: true,

  retries: 1,

  reporter: "html",

  use: {
    baseURL: "http://localhost:3000",

    headless: true,

    screenshot: "only-on-failure",

    trace: "retain-on-failure"
  }
});
```

This is a starting configuration. For CI, configure browser projects, worker counts, retries and artifact retention according to the available infrastructure.

## 72. Playwright Authentication

For tests that require login, Playwright supports saving authenticated browser state.

Example:

```javascript
await page.goto("/login");

await page.getByLabel("Email").fill(
  process.env.TEST_EMAIL
);

await page.getByLabel("Password").fill(
  process.env.TEST_PASSWORD
);

await page.getByRole("button", {
  name: "Login"
}).click();

await page.context().storageState({
  path: "playwright/.auth/user.json"
});
```

Subsequent tests can reuse this state through Playwright configuration.

**Security note:** Authentication state files may contain sensitive cookies or tokens. Do not commit them to Git.

---

# PART 22: CYPRESS

## 73. What Is Cypress?

Cypress is a testing framework commonly used for web application testing.

It provides:

- End-to-end testing.
- Component testing.
- Browser interaction.
- Network request interception.
- Assertions.
- Test debugging.

Official documentation:

https://docs.cypress.io/

## 74. Install Cypress

```bash
npm install --save-dev cypress
```

Open Cypress:

```bash
npx cypress open
```

Run tests in headless mode:

```bash
npx cypress run
```

## 75. Cypress Example

```javascript
describe("Login", () => {
  it("logs in successfully", () => {
    cy.visit("http://localhost:3000/login");

    cy.get('[name="email"]').type(
      "john@example.com"
    );

    cy.get('[name="password"]').type(
      "Password123"
    );

    cy.get("button[type='submit']").click();

    cy.url().should("include", "/dashboard");
  });
});
```

## 76. Playwright vs Cypress

| Feature | Playwright | Cypress |
|---|---|---|
| E2E Testing | Yes | Yes |
| Browser Support | Chromium, Firefox, WebKit | Major browser support with its own browser architecture |
| Multiple Browser Tabs | Supported | More limited than Playwright's model |
| Cross-Origin Workflows | Supported | Supported with constraints and specific APIs |
| Auto Waiting | Yes | Yes |
| Network Interception | Yes | Yes |
| Screenshots | Yes | Yes |
| Video | Supported | Supported |
| Component Testing | Available | Available |
| Language | JavaScript/TypeScript | JavaScript/TypeScript |

Both are capable tools.

Choose based on project requirements, team experience and the type of testing you need.

---

# PART 23: COMPONENT TESTING

## 77. What Is Component Testing?

Component Testing verifies an individual UI component independently or within a controlled testing environment.

Example:

A React component:

```jsx
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

export default Button;
```

A component test verifies:

- Button renders.
- Correct text appears.
- Click handler executes.
- Disabled state works.

### React Testing Library Example

Install:

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

Test:

```jsx
import { render, screen } from "@testing-library/react";

import userEvent from "@testing-library/user-event";

import Button from "./Button";

test("button handles click", async () => {
  const user = userEvent.setup();

  render(
    <Button onClick={() => {}}>
      Save
    </Button>
  );

  const button = screen.getByRole("button", {
    name: "Save"
  });

  await user.click(button);

  expect(button).toBeInTheDocument();
});
```

For interaction verification, you would typically provide a mock function and assert that it was called.

### Important React Testing Tools

- React Testing Library.
- Vitest.
- Jest.
- Playwright Component Testing.
- Cypress Component Testing.

---

# PART 24: CONTRACT TESTING

## 78. What Is Contract Testing?

Contract Testing verifies that two communicating systems follow an agreed interface contract.

This is especially useful in microservices architectures.

Example:

Your frontend expects:

```json
{
  "id": 1,
  "name": "John",
  "email": "john@example.com"
}
```

But the backend returns:

```json
{
  "userId": 1,
  "fullName": "John",
  "emailAddress": "john@example.com"
}
```

The API response no longer matches the frontend's expected contract.

Contract testing helps detect this kind of compatibility issue.

### Consumer-Driven Contract Testing

A consumer defines the API behavior it expects.

The provider verifies that it satisfies those expectations.

A well-known tool is Pact.

Documentation:

https://docs.pact.io/

### Contract Testing vs Integration Testing

| Contract Testing | Integration Testing |
|---|---|
| Verifies communication contracts | Verifies components work together |
| Focuses on interface compatibility | Focuses on combined behavior |
| Useful in distributed systems | Useful in monoliths and distributed systems |

---

# PART 25: PERFORMANCE TESTING

## 79. What Is Performance Testing?

Performance Testing evaluates how an application behaves under different workloads.

It measures:

- Response time.
- Throughput.
- Latency.
- Resource consumption.
- Error rate.
- Concurrent user handling.

### Important Performance Metrics

| Metric | Meaning |
|---|---|
| Response Time | Total time to receive a response |
| Latency | Delay before or during processing |
| Throughput | Number of requests or operations processed per unit of time |
| Requests Per Second (RPS) | Requests processed each second |
| Concurrent Users | Users active at the same time |
| Error Rate | Percentage of failed requests |
| CPU Usage | Processor consumption |
| Memory Usage | Memory consumption |

## 80. Load Testing

Load Testing checks how an application performs under an expected workload.

Example:

Your application is expected to support 1,000 concurrent users.

You simulate the expected workload and observe:

- API response times.
- Error rates.
- CPU utilization.
- Database load.

## 81. Stress Testing

Stress Testing pushes the application beyond its expected capacity to discover its breaking point and failure behavior.

Example:

Gradually increase the number of virtual users:

```text
1,000 users
2,000 users
5,000 users
10,000 users
20,000 users
```

Observe when:

- Response times increase significantly.
- Errors occur.
- Requests time out.
- The application becomes unavailable.

The objective is to understand system limits and recovery behavior.

## 82. Spike Testing

Spike Testing measures how the application responds to sudden increases or decreases in workload.

Example:

```text
Normal Load: 100 users

Sudden Spike: 10,000 users

Then:

Load returns to 100 users
```

This is useful for applications with unpredictable traffic.

## 83. Soak Testing

Soak Testing runs an application under sustained load for an extended period.

It helps identify:

- Memory leaks.
- Resource exhaustion.
- Connection leaks.
- Performance degradation.
- Long-running instability.

Example:

Run a workload continuously for 12 hours or 24 hours.

## 84. Volume Testing

Volume Testing checks how an application performs with large amounts of data.

Example:

Test an expense application with:

- 10 million expense records.
- 1 million users.
- Large transaction history.

It helps identify database and storage bottlenecks.

### Performance Testing Comparison

| Type | Main Objective |
|---|---|
| Load Testing | Expected workload |
| Stress Testing | Beyond expected capacity |
| Spike Testing | Sudden workload changes |
| Soak Testing | Long-duration workload |
| Volume Testing | Large data volumes |

---

# PART 26: K6 PERFORMANCE TESTING

## 85. What Is k6?

k6 is a performance testing tool used to simulate virtual users and generate workloads against applications.

It supports JavaScript-based test scripts.

Official documentation:

https://grafana.com/docs/k6/latest/

## 86. Install and Run k6

Follow the installation instructions for your operating system in the official documentation.

Create:

`load-test.js`

```javascript
import http from "k6/http";

import { check, sleep } from "k6";

export const options = {
  vus: 10,
  duration: "30s"
};

export default function () {
  const response = http.get(
    "http://localhost:3000/api/health"
  );

  check(response, {
    "status is 200": (r) => r.status === 200
  });

  sleep(1);
}
```

Run:

```bash
k6 run load-test.js
```

### Understanding the Script

```javascript
vus: 10
```

Simulates 10 virtual users.

```javascript
duration: "30s"
```

Runs the test for 30 seconds.

```javascript
http.get()
```

Sends an HTTP GET request.

```javascript
check()
```

Verifies response conditions.

```javascript
sleep(1)
```

Pauses execution for one second.

## 87. Advanced k6 Configuration

Example:

```javascript
import http from "k6/http";

import { check, sleep } from "k6";

export const options = {
  stages: [
    { duration: "30s", target: 10 },
    { duration: "1m", target: 50 },
    { duration: "30s", target: 100 },
    { duration: "1m", target: 100 },
    { duration: "30s", target: 0 }
  ],

  thresholds: {
    http_req_failed: ["rate<0.01"],
    http_req_duration: ["p(95)<500"]
  }
};

export default function () {
  const response = http.get(
    "http://localhost:3000/api/users"
  );

  check(response, {
    "status is 200": (r) => r.status === 200
  });

  sleep(1);
}
```

### Understanding Percentiles

`p(95)<500` means the 95th percentile of request duration should be below 500 milliseconds.

It does not mean every request must complete in less than 500 milliseconds.

### Why Are Percentiles Important?

Average response time can hide slow requests.

Consider:

```text
Request 1: 100 ms
Request 2: 120 ms
Request 3: 130 ms
Request 4: 150 ms
Request 5: 3000 ms
```

The average alone may not communicate the experience of slower users.

Percentiles provide a better understanding of response-time distribution.

---

# PART 27: APACHE JMETER

## 88. What Is JMeter?

Apache JMeter is a performance testing tool used to test web applications, APIs and other services.

It supports:

- Load testing.
- Stress testing.
- API testing.
- HTTP request simulation.
- Assertions.
- Performance reporting.

Official documentation:

https://jmeter.apache.org/

### Basic JMeter Workflow

```text
Test Plan
   |
   v
Thread Group
   |
   v
HTTP Request
   |
   v
Assertions
   |
   v
Listeners
   |
   v
Results
```

### Main Components

| Component | Purpose |
|---|---|
| Test Plan | Main test configuration |
| Thread Group | Defines virtual users and execution behavior |
| Sampler | Executes requests |
| HTTP Request | Sends HTTP requests |
| Assertion | Verifies expected results |
| Listener | Displays or records test results |
| Timer | Adds delays between requests |
| Controller | Controls execution flow |

### k6 vs JMeter

| Feature | k6 | JMeter |
|---|---|---|
| Interface | Code-first | GUI and CLI |
| Script Language | JavaScript-based | Java-based engine with GUI configuration |
| API Testing | Yes | Yes |
| Load Testing | Yes | Yes |
| Automation | CI/CD friendly | CI/CD friendly |
| Best Fit | Script-driven performance testing | GUI-oriented test planning and broad protocol testing |

---

# PART 28: TEST-DRIVEN DEVELOPMENT (TDD)

## 89. What Is TDD?

TDD stands for Test-Driven Development.

It is a development approach in which tests are written before the implementation.

TDD follows three main steps:

1. Red.
2. Green.
3. Refactor.

### TDD Workflow

```text
Write a Failing Test
        |
        v
       RED
        |
        v
Write Minimum Code
        |
        v
      GREEN
        |
        v
Refactor Code
        |
        v
      REPEAT
```

## 90. Example of TDD

Suppose you need a function that calculates the total price.

### Step 1: Write the Test First

```javascript
test("calculates total price", () => {
  expect(calculateTotal(100, 3)).toBe(300);
});
```

Initially, the test fails because the function does not exist.

This is RED.

### Step 2: Write Minimum Implementation

```javascript
function calculateTotal(price, quantity) {
  return price * quantity;
}
```

Now the test passes.

This is GREEN.

### Step 3: Refactor

You may improve the implementation while keeping all tests passing.

This is REFACTOR.

### Advantages of TDD

- Encourages testable design.
- Clarifies expected behavior.
- Helps identify defects early.
- Supports safe refactoring.
- Creates an automated regression suite.

### Important

TDD is a development methodology, not simply a testing tool.

---

# PART 29: BEHAVIOR-DRIVEN DEVELOPMENT (BDD)

## 91. What Is BDD?

BDD stands for Behavior-Driven Development.

It focuses on describing application behavior in a way that developers, testers and business stakeholders can understand.

BDD often uses structured natural-language scenarios.

### Given – When – Then

A common BDD format is:

- Given: Initial context.
- When: An action occurs.
- Then: Expected outcome.

Example:

```gherkin
Feature: User Login

Scenario: Login with valid credentials

Given the user has an existing account

When the user enters valid email and password

And clicks the login button

Then the user should be redirected to the dashboard
```

### Another Example

```gherkin
Feature: Expense Management

Scenario: Create an expense

Given the user is logged in

When the user enters amount 500

And selects the Food category

And submits the form

Then the expense should be saved

And the expense should appear in the expense list
```

### BDD Tools

- Cucumber.
- SpecFlow.
- Behave.
- Playwright with BDD integrations.

### TDD vs BDD

| Feature | TDD | BDD |
|---|---|---|
| Focus | Code behavior at a smaller level | Business behavior and system examples |
| Language | Usually programming code | Often structured natural language |
| Main Participants | Developers | Developers, testers and business stakeholders |
| Format | Red-Green-Refactor | Given-When-Then |
| Objective | Guide implementation with tests | Align implementation with expected behavior |

---

# PART 30: CONTINUOUS TESTING

## 92. What Is Continuous Testing?

Continuous Testing means executing appropriate automated tests throughout the software delivery lifecycle.

Instead of waiting until the end of development, tests run during development and deployment.

### Continuous Testing Workflow

```text
Developer Writes Code
         |
         v
      Git Push
         |
         v
    CI Pipeline
         |
         v
      Linting
         |
         v
     Unit Tests
         |
         v
  Integration Tests
         |
         v
    Build Application
         |
         v
    Deploy Staging
         |
         v
      E2E Tests
         |
         v
 Performance / Security Checks
         |
         v
    Production Release
```

Not every pipeline needs every test stage on every commit. Test selection should balance feedback speed, risk and execution cost.

## 93. Why Is Continuous Testing Important?

- Detects defects earlier.
- Reduces manual testing effort.
- Improves deployment confidence.
- Prevents regressions.
- Supports frequent releases.
- Improves collaboration between development and QA teams.

---

# PART 31: TESTING IN CI/CD PIPELINES

## 94. What Is a CI/CD Testing Pipeline?

A CI/CD testing pipeline automatically runs tests when code changes are pushed or a deployment is triggered.

You can implement this using GitHub Actions.

### Example Project Structure

```text
project/
│
├── src/
├── tests/
├── package.json
├── package-lock.json
└── .github/
    └── workflows/
        └── test.yml
```

### GitHub Actions Example

Create:

`.github/workflows/test.yml`

```yaml
name: Node.js Tests

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main

jobs:
  test:
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

      - name: Run Tests
        run: npm test
```

This is an illustrative workflow. In a real project, select a Node.js version supported by your application and pin third-party actions according to your organization's security policy.

### Pipeline Execution

```text
Git Push
   |
   v
GitHub Actions Starts
   |
   v
Install Dependencies
   |
   v
Run Automated Tests
   |
   +----------------+
   |                |
   v                v
 Passed           Failed
   |                |
   v                v
 Continue         Stop Pipeline
 Deployment
```

### Important

A failed critical test should generally prevent the corresponding deployment from proceeding.

---

# PART 32: TEST AUTOMATION ARCHITECTURE

## 95. What Is Test Automation Architecture?

Test Automation Architecture defines how automated tests are organized, maintained and executed.

A good automation framework should provide:

- Reusable components.
- Centralized configuration.
- Test data management.
- Reporting.
- Logging.
- Error handling.
- Environment management.
- Parallel execution where appropriate.

## 96. Page Object Model (POM)

Page Object Model is a design pattern used in UI test automation.

It separates page-specific selectors and interactions from test scenarios.

### Without POM

```javascript
test("login", async ({ page }) => {
  await page.goto("/login");

  await page.locator("#email").fill("test@example.com");

  await page.locator("#password").fill("Password123");

  await page.locator("#login").click();
});
```

If selectors change, many tests may need modifications.

### With POM

Folder structure:

```text
tests/
│
├── pages/
│   └── LoginPage.js
│
└── login.spec.js
```

`LoginPage.js`:

```javascript
class LoginPage {
  constructor(page) {
    this.page = page;

    this.emailInput = page.getByLabel("Email");

    this.passwordInput = page.getByLabel("Password");

    this.loginButton = page.getByRole("button", {
      name: "Login"
    });
  }

  async goto() {
    await this.page.goto("/login");
  }

  async login(email, password) {
    await this.emailInput.fill(email);

    await this.passwordInput.fill(password);

    await this.loginButton.click();
  }
}

module.exports = LoginPage;
```

`login.spec.js`:

```javascript
const { test, expect } = require("@playwright/test");

const LoginPage = require("./pages/LoginPage");

test("user logs in successfully", async ({ page }) => {
  const loginPage = new LoginPage(page);

  await loginPage.goto();

  await loginPage.login(
    "test@example.com",
    "Password123"
  );

  await expect(page).toHaveURL(/dashboard/);
});
```

### Benefits

- Reusable page interactions.
- Better maintainability.
- Cleaner test cases.
- Centralized selectors.
- Reduced duplication.

### Important

Do not turn Page Objects into giant classes containing all business logic. Keep them focused on page interactions and use separate helpers or fixtures for shared workflows.

---

# PART 33: FLAKY TESTS

## 97. What Is a Flaky Test?

A Flaky Test is a test that sometimes passes and sometimes fails without a relevant change to the application or test.

Example:

A test expects an element to appear immediately after clicking a button.

But the API takes different amounts of time to respond.

The test may pass sometimes and fail at other times.

### Common Causes

1. Race conditions.
2. Fixed time delays.
3. Unstable selectors.
4. Shared test data.
5. Network instability.
6. Tests depending on execution order.
7. Uncontrolled external services.
8. Incomplete cleanup.

### Bad Example

```javascript
await page.click("#submit");

await page.waitForTimeout(500);

await expect(
  page.locator(".success")
).toBeVisible();
```

The fixed delay may be too short or unnecessarily long.

### Better Example

```javascript
await page.click("#submit");

await expect(
  page.getByText("Success")
).toBeVisible();
```

Playwright's web-first assertions automatically wait for the expected condition within the configured timeout.

### How to Reduce Flaky Tests

- Prefer condition-based waiting.
- Use stable selectors.
- Isolate test data.
- Avoid shared mutable state.
- Mock unstable external dependencies where appropriate.
- Keep tests independent.
- Investigate repeated failures rather than blindly increasing retries.

---

# PART 34: TESTING STRATEGY FOR A FULL-STACK APPLICATION

## 98. Example: Expense Tracker Application

Let's combine the concepts into a practical testing strategy.

Technology stack:

```text
Frontend:
Next.js + React

Backend:
Node.js + Express

Database:
MySQL

Cache:
Redis

Testing:
Jest / Vitest
Supertest
Playwright
k6
```

### Unit Testing

Test:

- Expense calculations.
- Tax calculations.
- Discount logic.
- Date formatting.
- Validation functions.

Tools:

- Jest.
- Vitest.

### Integration Testing

Test:

- User service + database.
- Expense service + repository.
- Authentication + database.
- API + Redis.

Tools:

- Jest.
- Vitest.
- Testcontainers where useful.

### API Testing

Test:

- POST `/api/expenses`.
- GET `/api/expenses`.
- PUT `/api/expenses/:id`.
- DELETE `/api/expenses/:id`.

Tools:

- Supertest.

### E2E Testing

Test:

- User login.
- Add expense.
- Edit expense.
- Delete expense.
- Generate monthly report.
- Export Excel.

Tool:

- Playwright.

### Performance Testing

Test:

- 100 concurrent users.
- 500 concurrent users.
- 1,000 concurrent users.
- Large expense history.
- Dashboard response times.

Tool:

- k6.

### CI/CD Testing

```text
Developer Pushes Code
        |
        v
GitHub Actions
        |
        v
Install Dependencies
        |
        v
Linting
        |
        v
Unit Tests
        |
        v
Integration Tests
        |
        v
Build
        |
        v
Deploy to Staging
        |
        v
Playwright E2E Tests
        |
        v
Deployment Approval
        |
        v
Production
```

---

# PART 35: INTERVIEW QUESTIONS AND ANSWERS

## Beginner-Level Questions

### Q1. What is Software Testing?

Software Testing is the process of evaluating software to identify defects and verify that it meets specified requirements.

### Q2. What is a Bug?

A bug is a defect in software that causes incorrect or unexpected behavior.

### Q3. What is QA?

Quality Assurance focuses on improving development processes to prevent defects.

### Q4. What is QC?

Quality Control focuses on evaluating the software product to detect defects.

### Q5. What is STLC?

Software Testing Life Cycle describes the phases involved in software testing, from requirement analysis through test closure.

### Q6. What is the difference between SDLC and STLC?

SDLC covers the complete software development process, while STLC focuses specifically on testing activities.

### Q7. What is a Test Case?

A Test Case defines the inputs, execution steps and expected results used to verify a particular behavior.

### Q8. What is a Test Scenario?

A Test Scenario describes a functionality or situation that needs to be tested.

### Q9. What is Unit Testing?

Unit Testing verifies individual functions, methods or modules in isolation.

### Q10. What is Integration Testing?

Integration Testing verifies interactions between multiple components.

### Q11. What is System Testing?

System Testing verifies the complete integrated application against its requirements.

### Q12. What is Acceptance Testing?

Acceptance Testing determines whether the application meets business and user acceptance criteria.

## Intermediate-Level Questions

### Q13. What is Regression Testing?

Regression Testing verifies that existing functionality continues to work after code changes.

### Q14. What is Retesting?

Retesting executes previously failed test cases again after the corresponding defects have been fixed.

### Q15. What is Smoke Testing?

Smoke Testing performs broad, basic checks to determine whether a new build is stable enough for further testing.

### Q16. What is Sanity Testing?

Sanity Testing performs focused checks around a specific change or defect fix.

### Q17. What is Functional Testing?

Functional Testing verifies whether application features behave according to their requirements.

### Q18. What is Non-Functional Testing?

Non-Functional Testing verifies quality attributes such as performance, security, usability and reliability.

### Q19. What is Black Box Testing?

Black Box Testing evaluates software behavior without requiring knowledge of its internal implementation.

### Q20. What is White Box Testing?

White Box Testing examines internal code structure, logic and execution paths.

### Q21. What is Equivalence Partitioning?

It divides input data into groups expected to behave similarly, allowing representative values to be tested.

### Q22. What is Boundary Value Analysis?

It focuses on testing values at and around the boundaries of valid input ranges.

### Q23. What is Test Coverage?

Test Coverage measures how much of the source code or specified requirements are exercised by tests.

### Q24. Does 100% code coverage guarantee bug-free software?

No. Coverage measures execution, not whether every possible behavior or expected result has been verified.

## Advanced-Level Questions

### Q25. What is Jest?

Jest is a JavaScript testing framework that provides test execution, assertions, mocking and code coverage.

### Q26. What is Vitest?

Vitest is a JavaScript and TypeScript testing framework with strong integration into the Vite ecosystem.

### Q27. What is Supertest?

Supertest is a library for testing HTTP servers and API endpoints.

### Q28. What is Playwright?

Playwright is a browser automation and testing framework supporting Chromium, Firefox and WebKit.

### Q29. What is Cypress?

Cypress is a web testing framework commonly used for end-to-end and component testing.

### Q30. What is Mocking?

Mocking replaces dependencies with controlled implementations to isolate a component during testing.

### Q31. What is Stubbing?

Stubbing provides predefined responses to calls made by the component being tested.

### Q32. What is Spying?

Spying observes how an existing function is called, including its arguments and invocation count.

### Q33. What is TDD?

Test-Driven Development is a methodology in which tests are written before implementation, following Red-Green-Refactor.

### Q34. What is BDD?

Behavior-Driven Development describes expected application behavior using examples that developers, testers and business stakeholders can understand.

### Q35. What is Contract Testing?

Contract Testing verifies that communicating systems follow an agreed interface contract.

### Q36. What is Performance Testing?

Performance Testing evaluates application behavior under different workloads.

### Q37. What is Load Testing?

Load Testing verifies application performance under an expected workload.

### Q38. What is Stress Testing?

Stress Testing evaluates application behavior beyond its expected operating capacity.

### Q39. What is Spike Testing?

Spike Testing evaluates system behavior when workload changes suddenly.

### Q40. What is Soak Testing?

Soak Testing evaluates application stability under sustained load over an extended period.

## Scenario-Based Questions

### Q41. Your API works correctly, but the database does not save the expected data. Which testing type would help?

**Answer:** Integration Testing. It can verify the interaction between the API, service, repository and database.

### Q42. A developer changes the login module. What should you test?

**Answer:**

- Login functionality.
- Authentication.
- Session handling.
- Password validation.
- Protected routes.
- Related regression scenarios.

### Q43. Your Playwright test sometimes fails because the page takes longer to load. How would you fix it?

**Answer:**

Use condition-based waiting and Playwright's web-first assertions instead of fixed delays. Investigate whether the test depends on unstable external services.

### Q44. How would you test an API without starting the actual server on a fixed port?

**Answer:**

Export the Express application separately from the server startup logic and use Supertest to exercise the application directly.

### Q45. How would you test database-related business logic without using a real database?

**Answer:**

Inject a repository dependency and provide a mock or fake implementation for unit testing.

### Q46. Your application is slow with 5,000 concurrent users. Which testing approach would you use?

**Answer:**

Load Testing and Stress Testing. Use a tool such as k6 or JMeter to simulate the workload and identify bottlenecks.

### Q47. How would you test whether an API response is compatible with the frontend?

**Answer:**

Use API contract testing to verify that the provider's response matches the consumer's expected schema and behavior.

### Q48. How would you prevent broken code from reaching production?

**Answer:**

Implement CI/CD quality gates that run linting, unit tests, integration tests and relevant E2E tests before deployment.

### Q49. Your test suite passes locally but fails in GitHub Actions. What would you investigate?

**Answer:**

- Environment variable differences.
- Node.js version.
- Dependency installation.
- Database availability.
- Test execution order.
- File system assumptions.
- Time zone differences.
- Race conditions.
- External network dependencies.

### Q50. How would you design testing for a large enterprise application?

**Answer:**

Use a layered testing strategy:

1. Unit tests for business logic.
2. Integration tests for component interactions.
3. API tests for HTTP behavior.
4. Contract tests for service communication.
5. E2E tests for critical user workflows.
6. Performance tests for expected workloads.
7. Security testing for important attack surfaces.
8. CI/CD automation for continuous validation.

---

# PART 36: PRACTICAL HANDS-ON ASSIGNMENTS

## Level 1: Testing Fundamentals

| No. | Assignment | Expected Result |
|---|---|---|
| 1 | Write test scenarios for login | Complete scenario coverage |
| 2 | Create test cases for registration | Positive and negative cases |
| 3 | Test expense creation manually | Verify all validation rules |
| 4 | Create a bug report | Document reproducible defects |
| 5 | Perform boundary value analysis | Test input boundaries |
| 6 | Create an equivalence partition table | Identify input groups |

## Level 2: Unit and Integration Testing

| No. | Assignment | Expected Result |
|---|---|---|
| 7 | Install Jest in Node.js | Working test environment |
| 8 | Write calculator unit tests | Verified calculations |
| 9 | Test user service | Isolated service testing |
| 10 | Mock database repository | No real database dependency |
| 11 | Test async functions | Promise success and failure handling |
| 12 | Generate coverage reports | Coverage metrics |
| 13 | Install Vitest | Working Vitest environment |
| 14 | Use Node.js test runner | Native test execution |
| 15 | Create database integration tests | Verified persistence |

## Level 3: API Testing

| No. | Assignment | Expected Result |
|---|---|---|
| 16 | Install Supertest | Working API test environment |
| 17 | Test GET endpoints | Correct response verification |
| 18 | Test POST endpoints | Correct resource creation |
| 19 | Test PUT endpoints | Correct resource updates |
| 20 | Test DELETE endpoints | Correct resource deletion |
| 21 | Test authentication | Protected API verification |
| 22 | Test authorization | Role-based access verification |
| 23 | Test invalid input | Validation verification |
| 24 | Test API pagination | Correct pagination behavior |

## Level 4: Browser Automation

| No. | Assignment | Expected Result |
|---|---|---|
| 25 | Install Playwright | Browser automation setup |
| 26 | Automate login | Successful login workflow |
| 27 | Automate registration | Registration workflow |
| 28 | Automate expense creation | Complete form submission |
| 29 | Implement Page Object Model | Reusable page objects |
| 30 | Capture failure screenshots | Debugging artifacts |
| 31 | Configure Playwright reports | HTML reports |
| 32 | Run cross-browser tests | Chromium, Firefox and WebKit |
| 33 | Implement Cypress login test | Cypress automation |

## Level 5: Performance and CI/CD

| No. | Assignment | Expected Result |
|---|---|---|
| 34 | Install k6 | Performance testing setup |
| 35 | Test API with 10 virtual users | Basic load test |
| 36 | Test API with 100 virtual users | Increased load test |
| 37 | Perform stress testing | Identify system limits |
| 38 | Create a JMeter test plan | GUI-based load test |
| 39 | Implement TDD | Red-Green-Refactor |
| 40 | Create BDD scenarios | Given-When-Then specifications |
| 41 | Configure GitHub Actions | Automated test pipeline |
| 42 | Add test reports to CI | Test result visibility |

---

# PART 37: COMPLETE TESTING PROJECT

## Project: Automated Testing Framework for an Expense Tracker

Build an automated testing system for an existing Expense Tracker application.

### Application Stack

```text
Frontend:
Next.js + React

Backend:
Node.js + Express

Database:
MySQL

Cache:
Redis
```

### Testing Stack

```text
Unit Testing:
Jest / Vitest

API Testing:
Supertest

E2E Testing:
Playwright

Component Testing:
React Testing Library

Performance Testing:
k6

CI/CD:
GitHub Actions
```

### Project Structure

```text
expense-tracker/
│
├── frontend/
│   ├── src/
│   └── tests/
│
├── backend/
│   ├── src/
│   └── tests/
│       ├── unit/
│       ├── integration/
│       └── api/
│
├── e2e/
│   ├── login.spec.js
│   ├── expense.spec.js
│   └── report.spec.js
│
├── performance/
│   └── load-test.js
│
├── .github/
│   └── workflows/
│       └── test.yml
│
└── README.md
```

### Required Test Scenarios

**Authentication**

- Valid login.
- Invalid password.
- Invalid email.
- Missing credentials.
- Expired authentication token.
- Unauthorized access.

**Expense Management**

- Create expense.
- Update expense.
- Delete expense.
- Create expense with invalid amount.
- Create expense without category.
- Prevent unauthorized expense access.

**Reports**

- Monthly report.
- Yearly report.
- Category-wise report.
- Empty report.
- Excel export.

**Performance**

- Dashboard under expected workload.
- Expense listing under expected workload.
- Report generation under expected workload.
- Concurrent API requests.

### Expected Final Result

You should have an application with:

- Automated unit tests.
- Integration tests.
- API tests.
- E2E tests.
- Performance tests.
- Test coverage reports.
- Automated CI/CD test execution.

---

# PART 38: QUICK REVISION NOTES

| Concept | Meaning |
|---|---|
| Software Testing | Evaluating software to identify defects |
| QA | Process-oriented quality assurance |
| QC | Product-oriented quality control |
| STLC | Software Testing Life Cycle |
| SDLC | Software Development Life Cycle |
| Test Case | Detailed test instructions and expected result |
| Test Scenario | Situation or functionality to test |
| Unit Testing | Testing individual units |
| Integration Testing | Testing component interactions |
| System Testing | Testing the complete system |
| Acceptance Testing | Verifying business acceptance criteria |
| Functional Testing | Testing application functionality |
| Non-Functional Testing | Testing quality attributes |
| Smoke Testing | Basic build stability checks |
| Sanity Testing | Focused verification after a change |
| Regression Testing | Checking existing functionality after changes |
| Retesting | Rechecking a previously failed test |
| Exploratory Testing | Investigating software through exploration |
| Black Box Testing | Testing without internal implementation knowledge |
| White Box Testing | Testing internal code structure |
| Equivalence Partitioning | Dividing inputs into representative groups |
| Boundary Value Analysis | Testing range boundaries |
| Jest | JavaScript testing framework |
| Vitest | Vite-oriented testing framework |
| Node Test Runner | Built-in Node.js testing capability |
| Mock | Controlled replacement dependency |
| Stub | Predefined test response |
| Spy | Observes function calls |
| Fixture | Predefined test data or environment |
| Code Coverage | Measures executed code |
| Supertest | HTTP API testing library |
| Playwright | Browser automation framework |
| Cypress | Web application testing framework |
| POM | Page Object Model |
| Contract Testing | Verifying interface compatibility |
| Load Testing | Testing expected workload |
| Stress Testing | Testing beyond expected capacity |
| Spike Testing | Testing sudden workload changes |
| Soak Testing | Testing sustained workload |
| k6 | Performance testing tool |
| JMeter | Performance testing tool |
| TDD | Test-Driven Development |
| BDD | Behavior-Driven Development |
| CI | Continuous Integration |
| CD | Continuous Delivery or Deployment |
| Flaky Test | Test with inconsistent results |

---

# PART 39: PHASE 58 COMPLETION CHECKLIST

### Testing Fundamentals

- [ ] Understand Software Testing.
- [ ] Understand QA and QC.
- [ ] Understand SDLC and STLC.
- [ ] Understand test cases and test scenarios.
- [ ] Understand test planning.
- [ ] Understand defect reporting.

### Testing Levels and Types

- [ ] Unit Testing.
- [ ] Integration Testing.
- [ ] System Testing.
- [ ] Acceptance Testing.
- [ ] Functional Testing.
- [ ] Non-Functional Testing.
- [ ] Smoke Testing.
- [ ] Sanity Testing.
- [ ] Regression Testing.
- [ ] Retesting.
- [ ] Exploratory Testing.
- [ ] Security Testing.
- [ ] Compatibility Testing.
- [ ] Accessibility Testing.

### Testing Techniques

- [ ] Black Box Testing.
- [ ] White Box Testing.
- [ ] Grey Box Testing.
- [ ] Equivalence Partitioning.
- [ ] Boundary Value Analysis.
- [ ] Decision Table Testing.
- [ ] State Transition Testing.

### Unit and Integration Testing

- [ ] Jest.
- [ ] Vitest.
- [ ] Node.js Test Runner.
- [ ] Assertions.
- [ ] Mocking.
- [ ] Stubbing.
- [ ] Spying.
- [ ] Test Fixtures.
- [ ] Test Setup and Teardown.
- [ ] Code Coverage.
- [ ] Database Testing.

### API and Browser Testing

- [ ] API Testing.
- [ ] Supertest.
- [ ] Authentication Testing.
- [ ] Authorization Testing.
- [ ] End-to-End Testing.
- [ ] Playwright.
- [ ] Cypress.
- [ ] Component Testing.
- [ ] Page Object Model.
- [ ] Contract Testing.

### Performance Testing

- [ ] Performance Testing.
- [ ] Load Testing.
- [ ] Stress Testing.
- [ ] Spike Testing.
- [ ] Soak Testing.
- [ ] Volume Testing.
- [ ] k6.
- [ ] JMeter.

### Advanced Testing

- [ ] TDD.
- [ ] BDD.
- [ ] Continuous Testing.
- [ ] CI/CD Testing.
- [ ] Test Automation Architecture.
- [ ] Flaky Test Management.
- [ ] Test Reporting.
- [ ] Testing Best Practices.

### Practical Implementation

- [ ] Build a Jest testing project.
- [ ] Build an API testing suite using Supertest.
- [ ] Build an E2E testing suite using Playwright.
- [ ] Implement Page Object Model.
- [ ] Perform API performance testing using k6.
- [ ] Configure automated testing using GitHub Actions.

---

# Official Documentation and Learning Resources

| Technology | Official Resource |
|---|---|
| Jest | https://jestjs.io/docs/getting-started |
| Vitest | https://vitest.dev/guide/ |
| Node.js Test Runner | https://nodejs.org/api/test.html |
| Supertest | https://github.com/ladjs/supertest |
| Playwright | https://playwright.dev/docs/intro |
| Cypress | https://docs.cypress.io/ |
| React Testing Library | https://testing-library.com/docs/react-testing-library/intro/ |
| k6 | https://grafana.com/docs/k6/latest/ |
| Apache JMeter | https://jmeter.apache.org/ |
| Pact | https://docs.pact.io/ |
| Cucumber | https://cucumber.io/docs/ |
| GitHub Actions | https://docs.github.com/en/actions |

---

# Phase 58 – Final Summary

In this phase, you learned how to test software at different levels, automate repetitive testing tasks, verify APIs, test browser workflows and evaluate application performance.

The most important concepts to remember:

1. **Unit Testing:** Test individual functions and components.
2. **Integration Testing:** Test interactions between components.
3. **E2E Testing:** Test complete user workflows.
4. **Regression Testing:** Ensure existing functionality continues to work.
5. **Jest/Vitest:** Test JavaScript and TypeScript code.
6. **Supertest:** Test Node.js HTTP APIs.
7. **Playwright/Cypress:** Automate browser-based testing.
8. **Mocking:** Replace dependencies during isolated tests.
9. **Code Coverage:** Measure how much code is exercised.
10. **Contract Testing:** Verify communication compatibility.
11. **k6/JMeter:** Evaluate application performance.
12. **TDD:** Write tests before implementation.
13. **BDD:** Describe behavior through business-readable scenarios.
14. **CI/CD Testing:** Automatically execute tests during software delivery.

### Recommended Learning Order

For practical full-stack development, I recommend learning these tools in this order:

| Order | Technology | Priority |
|---:|---|---|
| 1 | Jest or Vitest | Essential |
| 2 | Supertest | Essential for Node.js APIs |
| 3 | React Testing Library | Important for React |
| 4 | Playwright | Essential for E2E automation |
| 5 | k6 | Important for performance testing |
| 6 | GitHub Actions | Essential for CI/CD |
| 7 | Pact | Advanced, especially for microservices |
| 8 | JMeter | Useful for broader performance testing |

**Practical recommendation:** Build an automated testing suite for a Node.js + Next.js application. It will connect the testing concepts from this phase with the software architecture concepts from Phase 57.

---

# Next Phase Preview

## Phase 59 – Advanced Application Security, OWASP, Secure Coding and Ethical Hacking Fundamentals

Topics planned:

1. Cybersecurity Fundamentals.
2. Application Security Fundamentals.
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
13. OWASP Top 10.
14. SQL Injection.
15. Cross-Site Scripting (XSS).
16. Cross-Site Request Forgery (CSRF).
17. Server-Side Request Forgery (SSRF).
18. Broken Access Control.
19. Security Misconfiguration.
20. Insecure Deserialization.
21. API Security.
22. CORS.
23. Content Security Policy (CSP).
24. HTTPS and TLS.
25. Rate Limiting.
26. Brute-Force Protection.
27. Secure File Uploads.
28. Secrets Management.
29. Dependency Vulnerability Scanning.
30. Secure Node.js Development.
31. Secure React and Next.js Development.
32. Database Security.
33. Docker and Container Security.
34. Security Testing.
35. SAST, DAST and SCA.
36. Security Auditing.
37. OWASP ZAP and Burp Suite Fundamentals.
38. Secure Deployment Best Practices.

**Say "OK, Phase 59" when you're ready to continue.**