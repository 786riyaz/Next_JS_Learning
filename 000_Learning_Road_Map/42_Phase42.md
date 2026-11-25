# Phase 42 – Advanced CI/CD Pipelines, GitHub Actions and Automated Deployment

**Level:** Advanced  
**Domain:** DevOps, CI/CD, Automation and Production Deployment  
**Tools:** GitHub Actions, Git, Docker, Node.js, Next.js, AWS, Linux, SSH  
**Practical Stack:** Next.js, React, Node.js, Express, MongoDB, MySQL, Redis

---

# 1. Learning Objectives

By completing this phase, you will understand how professional development teams automate software testing, building and deployment.

You will learn to:

1. Understand CI/CD architecture and workflows.
2. Design advanced GitHub Actions pipelines.
3. Configure workflow triggers and event filters.
4. Manage jobs, steps, runners and dependencies.
5. Execute jobs in parallel and sequentially.
6. Configure environment variables and secrets.
7. Build reusable workflows and composite actions.
8. Use matrix builds for multiple Node.js versions.
9. Manage build artifacts and dependency caching.
10. Automate testing and quality checks.
11. Build and publish Docker images automatically.
12. Deploy applications to Linux VPS servers.
13. Understand automated AWS deployments.
14. Integrate deployment workflows with Vercel and Render.
15. Manage development, staging and production environments.
16. Implement SSH-based deployment automation.
17. Understand rolling, blue-green and canary deployments.
18. Implement rollback strategies.
19. Secure CI/CD pipelines.
20. Design a complete production CI/CD pipeline.

---

# 2. What Is CI/CD?

## 2.1 What Is CI/CD?

CI/CD stands for:

- CI – Continuous Integration
- CD – Continuous Delivery or Continuous Deployment

CI/CD is a software engineering practice that automates the process of validating, building, delivering and deploying applications.

Without CI/CD, developers may need to perform several steps manually:

```text
Developer writes code
        |
        v
Run tests manually
        |
        v
Build application manually
        |
        v
Connect to server
        |
        v
Upload files
        |
        v
Restart application
        |
        v
Check deployment
```

This process is repetitive and can introduce human errors.

With CI/CD:

```text
Developer pushes code
        |
        v
GitHub receives changes
        |
        v
CI/CD pipeline starts
        |
        v
Install dependencies
        |
        v
Run linting
        |
        v
Run automated tests
        |
        v
Build application
        |
        v
Deploy application
        |
        v
Verify deployment
```

Most of these operations happen automatically.

## 2.2 Continuous Integration (CI)

Continuous Integration is the practice of frequently integrating code changes into a shared repository and automatically validating them.

Typical CI operations:

1. Checkout source code.
2. Install dependencies.
3. Run linting.
4. Run unit tests.
5. Run integration tests.
6. Check TypeScript types.
7. Build the application.
8. Generate test reports.

Example:

Suppose three developers are working on an e-commerce application.

- Developer A implements authentication.
- Developer B implements product search.
- Developer C implements payment integration.

Whenever they create Pull Requests, the CI pipeline validates the changes.

This helps identify problems before merging.

## 2.3 Continuous Delivery

Continuous Delivery means the application is automatically built, tested and prepared for release.

However, production deployment may require manual approval.

Example:

```text
Code Push
    |
    v
Automated Tests
    |
    v
Production Build
    |
    v
Staging Deployment
    |
    v
Manual Approval
    |
    v
Production Deployment
```

## 2.4 Continuous Deployment

Continuous Deployment goes one step further.

Every change that successfully passes the required checks can automatically reach production.

```text
Code Push
    |
    v
Automated Tests
    |
    v
Build
    |
    v
Deploy to Production
    |
    v
Health Check
```

No separate manual production approval is required in the normal deployment path.

## 2.5 CI vs Continuous Delivery vs Continuous Deployment

| Feature | CI | Continuous Delivery | Continuous Deployment |
|---|---|---|---|
| Automated testing | Yes | Yes | Yes |
| Automated build | Yes | Yes | Yes |
| Release preparation | Not necessarily | Yes | Yes |
| Production deployment | Not required | Usually approval-based | Automated |
| Manual production approval | Not applicable | Common | Not normally required |

**Important:** Continuous Delivery and Continuous Deployment are different concepts. Continuous Delivery ensures that software is always ready to release; Continuous Deployment automatically releases qualifying changes.

---

# 3. CI/CD Architecture

A production CI/CD system commonly contains these components:

| Component | Responsibility |
|---|---|
| Source Control | Stores source code and version history |
| CI Server | Executes automated validation |
| Build System | Produces application artifacts |
| Test System | Executes automated tests |
| Artifact Registry | Stores build artifacts or container images |
| Deployment System | Deploys application releases |
| Monitoring System | Tracks application health |
| Rollback System | Restores a previous working release |

## 3.1 General Architecture

```text
             Developer
                 |
                 v
              Git Push
                 |
                 v
              GitHub
                 |
                 v
          GitHub Actions
                 |
        -------------------
        |        |        |
       Lint     Test     Build
        |        |        |
        -------------------
                 |
                 v
          Build Artifact
                 |
                 v
          Artifact Registry
                 |
                 v
             Deployment
                 |
        -------------------
        |        |        |
      Staging  Production  AWS
        |        |        |
        -------------------
                 |
                 v
             Monitoring
```

## 3.2 Why Separate CI and CD?

CI and CD can be implemented in one workflow or multiple workflows.

For larger projects, separating them provides several advantages:

- Easier troubleshooting.
- Independent permissions.
- Better deployment control.
- Environment-specific configuration.
- Manual approval before production.
- Reduced risk of accidental deployment.

A common structure:

```text
.github/
│
└── workflows/
    │
    ├── ci.yml
    ├── staging-deploy.yml
    ├── production-deploy.yml
    └── docker-publish.yml
```

---

# 4. GitHub Actions Architecture

GitHub Actions is an automation platform integrated with GitHub.

It executes workflows in response to repository events or scheduled/manual triggers.

## 4.1 What Is a Workflow?

A workflow is an automated process defined in a YAML file.

Example:

```text
.github/workflows/ci.yml
```

A workflow defines:

- When it runs.
- Which jobs it executes.
- Which machines execute those jobs.
- Which steps run.
- What permissions are required.

## 4.2 What Is a Runner?

A runner is the machine that executes a GitHub Actions job.

There are two major types.

### GitHub-Hosted Runner

GitHub provides and manages the execution environment.

Example:

```yaml
runs-on: ubuntu-latest
```

Other examples:

```yaml
runs-on: windows-latest
```

```yaml
runs-on: macos-latest
```

Advantages:

- No server maintenance.
- Easy configuration.
- Convenient for standard CI/CD.
- Fresh execution environment for each job.

### Self-Hosted Runner

A self-hosted runner is a machine that you manage and register with GitHub.

It could be:

- A Linux VPS.
- An AWS EC2 instance.
- An internal company server.
- A dedicated build machine.

Example:

```yaml
runs-on: self-hosted
```

You can also use labels:

```yaml
runs-on: [self-hosted, linux, x64]
```

**Security warning:** A self-hosted runner that executes untrusted Pull Request code can expose your server, credentials and internal network. Use isolated, ephemeral runners and strict access controls for sensitive workflows.

## 4.3 What Is a Job?

A job is a collection of steps executed on a runner.

Example:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Print Message
        run: echo "Building application"
```

A workflow can contain multiple jobs.

## 4.4 What Is a Step?

A step is an individual operation inside a job.

For example:

```yaml
steps:
  - name: Checkout Code
    uses: actions/checkout@v7

  - name: Print Message
    run: echo "Hello GitHub Actions"
```

A step can:

- Execute shell commands using `run`.
- Use an existing action through `uses`.
- Call a reusable workflow at the job level.

## 4.5 What Is an Action?

An action is a reusable automation component.

Example:

```yaml
- name: Checkout Repository
  uses: actions/checkout@v7
```

This action checks out your repository.

Other examples:

```yaml
uses: actions/setup-node@v7
```

```yaml
uses: actions/cache@v4
```

**Version note:** These examples use action versions available in the current documentation consulted for this phase. Check each action's official release page before adopting or upgrading it. For sensitive production workflows, pin third-party actions to reviewed full commit SHAs rather than relying only on mutable version tags. [GitHub](https://github.com/actions/setup-node?utm_source=chatgpt.com)

---

# 5. GitHub Actions Workflow Syntax

Let's understand the main elements of a workflow file.

Example:

```yaml
name: Application CI

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v7

      - name: Print Message
        run: echo "Running CI Pipeline"
```

## 5.1 `name`

Defines the workflow name.

```yaml
name: Backend CI Pipeline
```

## 5.2 `on`

Defines workflow triggers.

```yaml
on:
  push:
    branches:
      - main
```

The workflow runs when a matching push event occurs.

## 5.3 `jobs`

Defines the jobs in the workflow.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
```

## 5.4 `runs-on`

Specifies the runner environment.

```yaml
runs-on: ubuntu-latest
```

## 5.5 `steps`

Defines the operations inside a job.

```yaml
steps:
  - name: Install Dependencies
    run: npm ci
```

## 5.6 `uses`

Runs a reusable action.

```yaml
- uses: actions/checkout@v7
```

## 5.7 `run`

Executes shell commands.

```yaml
- name: Build
  run: npm run build
```

## 5.8 `env`

Defines environment variables.

```yaml
env:
  NODE_ENV: production
```

## 5.9 `with`

Passes inputs to an action.

```yaml
- uses: actions/setup-node@v7
  with:
    node-version: 24
```

## 5.10 `needs`

Defines job dependencies.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Testing"

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying"
```

The `deploy` job waits for `test` to succeed.

Official reference: [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax). [GitHub Docs](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)

---

# 6. Workflow Triggers and Event Filters

A workflow can run in response to many different events.

## 6.1 Push Event

```yaml
on:
  push:
    branches:
      - main
```

Runs when changes are pushed to `main`.

## 6.2 Pull Request Event

```yaml
on:
  pull_request:
    branches:
      - main
```

Runs when a Pull Request targeting `main` receives a matching event.

## 6.3 Multiple Branches

```yaml
on:
  push:
    branches:
      - main
      - development
      - staging
```

## 6.4 Exclude Branches

```yaml
on:
  push:
    branches-ignore:
      - experimental
      - temporary
```

## 6.5 Path Filters

Run the workflow only when certain paths change.

```yaml
on:
  push:
    paths:
      - "frontend/**"
      - "backend/**"
      - ".github/workflows/**"
```

Exclude paths:

```yaml
on:
  push:
    paths-ignore:
      - "docs/**"
      - "*.md"
```

## 6.6 Scheduled Workflow

GitHub Actions supports scheduled workflows using cron expressions.

```yaml
on:
  schedule:
    - cron: "0 2 * * *"
```

This schedules the workflow daily at 02:00 UTC.

**Important:** Scheduled workflows use UTC. They can be delayed during periods of high GitHub load, and they are associated with the default branch.

## 6.7 Manual Workflow

Use `workflow_dispatch`.

```yaml
on:
  workflow_dispatch:
```

You can start this workflow manually through GitHub's Actions interface.

You can also define inputs:

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Deployment environment"
        required: true
        type: choice
        options:
          - staging
          - production
```

## 6.8 Release Event

```yaml
on:
  release:
    types:
      - published
```

This workflow runs when a GitHub Release is published.

## 6.9 Multiple Triggers

```yaml
on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  workflow_dispatch:
```

This workflow supports push, Pull Request and manual triggers.

**YAML tip:** If your YAML parser interprets the key `on` as a boolean, ensure you are using a YAML 1.2-compatible parser or the GitHub Actions workflow validator.

---

# 7. Jobs, Dependencies and Parallel Execution

## 7.1 Jobs Run in Parallel by Default

Consider:

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running lint"

  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running tests"

  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building application"
```

These three jobs can run in parallel.

```text
             Workflow
                 |
       ---------------------
       |         |         |
      Lint      Test      Build
       |         |         |
       ---------------------
```

## 7.2 Sequential Execution with `needs`

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Lint"

  test:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - run: echo "Test"

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: echo "Build"
```

Execution:

```text
Lint
  |
  v
Test
  |
  v
Build
```

## 7.3 Multiple Dependencies

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Lint"

  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Test"

  build:
    needs:
      - lint
      - test

    runs-on: ubuntu-latest

    steps:
      - run: echo "Build"
```

The build job waits for both lint and test jobs.

## 7.4 Job Conditions

You can control execution using `if`.

```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest

    steps:
      - run: echo "Deploying production"
```

## 7.5 Always Run Cleanup

```yaml
- name: Cleanup
  if: always()
  run: echo "Cleaning temporary files"
```

Other useful status functions include:

- `success()`
- `failure()`
- `cancelled()`
- `always()`

Use these carefully because conditions can override the normal success-dependent execution behavior.

---

# 8. Matrix Builds and Test Strategies

## 8.1 What Is a Matrix Build?

A matrix build executes a job using multiple combinations of configuration values.

Suppose your Node.js application must work with several Node.js versions.

Instead of creating separate jobs manually, use a matrix.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version:
          - 22
          - 24

    steps:
      - uses: actions/checkout@v7

      - uses: actions/setup-node@v7
        with:
          node-version: ${{ matrix.node-version }}

      - run: npm ci
      - run: npm test
```

GitHub creates separate job instances for the configured Node.js versions.

```text
             Matrix Job
                 |
          --------------
          |            |
       Node 22       Node 24
          |            |
         Test         Test
          |            |
          --------------
```

## 8.2 Multiple Matrix Dimensions

```yaml
strategy:
  matrix:
    node-version:
      - 22
      - 24

    os:
      - ubuntu-latest
      - windows-latest
```

This produces four combinations.

| Operating System | Node.js |
|---|---|
| Ubuntu | 22 |
| Ubuntu | 24 |
| Windows | 22 |
| Windows | 24 |

## 8.3 Exclude Combinations

```yaml
strategy:
  matrix:
    node-version:
      - 22
      - 24

    os:
      - ubuntu-latest
      - windows-latest

    exclude:
      - os: windows-latest
        node-version: 22
```

This removes the specified combination.

## 8.4 Include Additional Combinations

```yaml
strategy:
  matrix:
    node-version:
      - 22
      - 24

    include:
      - node-version: 24
        experimental: true
```

## 8.5 Fail-Fast

```yaml
strategy:
  fail-fast: false
  matrix:
    node-version:
      - 22
      - 24
```

With `fail-fast: false`, a failing matrix job does not automatically cancel the other running matrix jobs.

## 8.6 Max Parallel

```yaml
strategy:
  max-parallel: 2
  matrix:
    node-version:
      - 22
      - 24
      - 25
```

Limits the number of matrix jobs running simultaneously.

**Real-world use cases:**

- Test across Node.js versions.
- Test on Linux, Windows and macOS.
- Test against multiple database versions.
- Validate different application configurations.

---

# 9. Environment Variables and Secrets

Environment variables and secrets are essential in CI/CD.

## 9.1 What Is an Environment Variable?

An environment variable is a named value available to a running process.

Example:

```env
NODE_ENV=production
PORT=5000
```

In Node.js:

```javascript
console.log(process.env.NODE_ENV);
```

## 9.2 Define Workflow-Level Variables

```yaml
env:
  NODE_ENV: production

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Print Environment
        run: echo "$NODE_ENV"
```

## 9.3 Job-Level Variables

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    env:
      NODE_ENV: production

    steps:
      - run: echo "$NODE_ENV"
```

## 9.4 Step-Level Variables

```yaml
steps:
  - name: Build Application
    env:
      NODE_ENV: production

    run: npm run build
```

## 9.5 What Is a GitHub Secret?

A GitHub Secret is an encrypted value stored by GitHub and made available to authorized workflow executions.

Examples:

```text
DATABASE_URL
JWT_SECRET
DEPLOY_SSH_KEY
AWS_ROLE_ARN
DOCKERHUB_TOKEN
```

Reference a secret:

```yaml
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

## 9.6 GitHub Variables vs Secrets

| Feature | Variables | Secrets |
|---|---|---|
| Intended use | Non-sensitive configuration | Sensitive values |
| Example | Application URL | Database password |
| Access | `vars.NAME` | `secrets.NAME` |
| Encryption at rest | Not intended as secret storage | Yes |
| Masking in logs | Not guaranteed | GitHub attempts to redact known secret values |

Example:

```yaml
env:
  APP_URL: ${{ vars.APP_URL }}
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

## 9.7 Where Can You Store Secrets?

GitHub supports several scopes:

1. Repository secrets.
2. Environment secrets.
3. Organization secrets.

### Repository Secret

Available to eligible workflows in the repository, subject to GitHub's access rules.

### Environment Secret

Associated with an environment such as:

```text
development
staging
production
```

A deployment job referencing that environment can access its secrets after applicable protection rules pass.

### Organization Secret

Can be shared with selected repositories within an organization.

## 9.8 Example: Database Secret

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v7

      - name: Run Tests
        env:
          DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}

        run: npm test
```

## 9.9 Important Security Rules

Never:

- Hardcode database passwords in YAML.
- Print secrets to logs.
- Store secrets in repository files.
- Pass secrets as command-line arguments unnecessarily.
- Expose secrets to untrusted Pull Request code.
- Reuse production credentials for development.

GitHub's masking is not a guarantee against every possible secret leak. Avoid transforming or encoding secrets and then printing them.

---

# 10. Reusable Workflows and Composite Actions

Large organizations often have many repositories.

Suppose you maintain:

```text
frontend-app
backend-api
admin-dashboard
mobile-backend
```

All four projects need similar CI workflows.

Copying the same workflow into every repository creates duplication.

GitHub Actions provides reusable workflows and composite actions.

## 10.1 What Is a Reusable Workflow?

A reusable workflow is a workflow that another workflow can call.

It is useful for sharing complete job-level automation.

Example structure:

```text
.github/
│
└── workflows/
    │
    ├── ci.yml
    └── reusable-node-ci.yml
```

### Reusable Workflow

`reusable-node-ci.yml`

```yaml
name: Reusable Node CI

on:
  workflow_call:
    inputs:
      node-version:
        required: true
        type: string

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v7

      - uses: actions/setup-node@v7
        with:
          node-version: ${{ inputs.node-version }}

      - run: npm ci
      - run: npm test
```

### Caller Workflow

`ci.yml`

```yaml
name: Main CI

on:
  push:
    branches:
      - main

jobs:
  node-ci:
    uses: ./.github/workflows/reusable-node-ci.yml

    with:
      node-version: "24"
```

The main workflow calls the reusable workflow.

## 10.2 Reusable Workflow With Secrets

A caller can pass specific secrets:

```yaml
jobs:
  test:
    uses: ./.github/workflows/reusable-node-ci.yml

    with:
      node-version: "24"

    secrets:
      TEST_DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}
```

The called workflow must declare the secret under `workflow_call.secrets`.

Avoid using `secrets: inherit` unless the called workflow genuinely needs access to all available secrets.

## 10.3 What Is a Composite Action?

A composite action packages multiple steps into one reusable action.

It is useful for sharing repeated sequences of commands.

Example:

```text
.github/
│
└── actions/
    │
    └── setup-node-project/
        │
        └── action.yml
```

`action.yml`:

```yaml
name: Setup Node Project

description: Install Node.js dependencies

inputs:
  node-version:
    description: Node.js version
    required: true

runs:
  using: composite

  steps:
    - name: Setup Node
      uses: actions/setup-node@v7
      with:
        node-version: ${{ inputs.node-version }}

    - name: Install Dependencies
      shell: bash
      run: npm ci
```

Use it:

```yaml
steps:
  - uses: actions/checkout@v7

  - name: Setup Project
    uses: ./.github/actions/setup-node-project
    with:
      node-version: "24"
```

## 10.4 Reusable Workflow vs Composite Action

| Feature | Reusable Workflow | Composite Action |
|---|---|---|
| Reuses | Complete workflows/jobs | Groups of steps |
| Defined in | `.github/workflows/` | `action.yml` |
| Called using | Job-level `uses` | Step-level `uses` |
| Can define runners | Yes, in called jobs | No |
| Best for | Shared CI/CD pipelines | Shared command sequences |

---

# 11. Build Artifacts and Caching

These are two different CI/CD concepts.

## 11.1 What Is a Build Artifact?

An artifact is a file or collection of files generated during a workflow.

Examples:

- Production build output.
- Test reports.
- Coverage reports.
- Compiled application packages.
- Deployment bundles.

Suppose a frontend build generates:

```text
.next/
```

You can upload the generated output as a workflow artifact.

## 11.2 Upload an Artifact

```yaml
- name: Upload Build Output
  uses: actions/upload-artifact@v4

  with:
    name: application-build
    path: .next/
```

## 11.3 Download an Artifact

```yaml
- name: Download Build Output
  uses: actions/download-artifact@v4

  with:
    name: application-build
    path: ./downloaded-build/
```

This allows one job to produce an artifact and another job to consume it.

**Important:** A `.next` directory is not automatically a complete deployable Next.js package. Its deployment requirements depend on the build mode, runtime dependencies and deployment target. For example, a standalone Next.js build requires the relevant standalone output and static assets.

## 11.4 What Is Dependency Caching?

Dependency caching stores reusable data to reduce repeated installation time.

For example, installing dependencies repeatedly can be expensive:

```bash
npm ci
```

A cache can preserve downloaded npm package data between workflow runs.

## 11.5 Cache Using setup-node

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v7

  with:
    node-version: 24
    cache: npm
```

The action caches package-manager data, not the entire `node_modules` directory.

## 11.6 Cache Using actions/cache

```yaml
- name: Cache npm
  uses: actions/cache@v4

  with:
    path: ~/.npm

    key: npm-${{ runner.os }}-${{ hashFiles('package-lock.json') }}

    restore-keys: |
      npm-${{ runner.os }}-
```

## 11.7 Cache vs Artifact

| Feature | Cache | Artifact |
|---|---|---|
| Main purpose | Speed up workflows | Store and transfer workflow outputs |
| Typical content | npm download cache | Build files and test reports |
| Reuse | Across workflow runs | Across jobs or for later download |
| Lifecycle | Evictable based on retention and usage | Controlled by artifact retention settings |
| Guaranteed release storage | No | No; artifact retention is finite |

**Important:** A cache is an optimization, not a reliable source of truth. The workflow must still work correctly when the cache is empty.

Also avoid allowing untrusted workflows to write to caches later consumed by privileged deployment workflows. Cache poisoning can compromise build integrity. [GitHub Docs](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)

---

# 12. Automated Testing Pipelines

Automated testing is one of the most important CI/CD stages.

A professional application should not be deployed merely because its code was pushed.

## 12.1 Types of Automated Tests

| Test Type | Purpose |
|---|---|
| Unit Test | Tests an individual function or module |
| Integration Test | Tests interactions between components |
| API Test | Tests HTTP endpoints |
| E2E Test | Tests complete user workflows |
| Regression Test | Checks that existing functionality still works |
| Performance Test | Evaluates response time and throughput |
| Security Test | Checks for security weaknesses |

## 12.2 Example Project Scripts

Suppose your `package.json` contains:

```json
{
  "scripts": {
    "lint": "eslint .",
    "typecheck": "tsc --noEmit",
    "test": "vitest run",
    "build": "next build"
  }
}
```

## 12.3 CI Workflow

Create:

```text
.github/workflows/ci.yml
```

Example:

```yaml
name: Application CI

on:
  push:
    branches:
      - main
      - development

  pull_request:
    branches:
      - main
      - development

permissions:
  contents: read

jobs:
  validate:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v7

      - name: Setup Node.js
        uses: actions/setup-node@v7
        with:
          node-version: 24
          cache: npm

      - name: Install Dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type Check
        run: npm run typecheck

      - name: Run Tests
        run: npm test

      - name: Build
        run: npm run build
```

## 12.4 Why Use `npm ci` Instead of `npm install`?

| `npm install` | `npm ci` |
|---|---|
| Designed for regular dependency installation and development | Designed for clean, reproducible CI installation |
| Can update package-lock.json when necessary | Requires a compatible lockfile |
| Can resolve dependency changes | Uses lockfile versions |
| May leave existing `node_modules` contents | Removes existing `node_modules` before installation |

For CI pipelines, `npm ci` is generally preferred when using npm and committing `package-lock.json`.

## 12.5 Integration Tests With Databases

Suppose your backend uses MongoDB.

You need a database available during testing.

Possible approaches:

1. Start a database using a GitHub Actions service container.
2. Start a temporary database using Docker Compose.
3. Use a dedicated test database.
4. Use an isolated database instance created for each test run.

Example using a MongoDB service container:

```yaml
jobs:
  integration-test:
    runs-on: ubuntu-latest

    services:
      mongodb:
        image: mongo:8
        ports:
          - 27017:27017

    steps:
      - uses: actions/checkout@v7

      - uses: actions/setup-node@v7
        with:
          node-version: 24

      - run: npm ci

      - name: Run Integration Tests
        env:
          MONGODB_URI: mongodb://localhost:27017/testdb
        run: npm run test:integration
```

The example assumes the application and test suite support that MongoDB version and connection configuration.

**Important:** A GitHub Actions service container is intended for the workflow. It is not a permanent production database.

---

# 13. Docker Image Build and Push Automation

Docker is frequently used in production CI/CD pipelines.

Instead of deploying raw source code, you can build a Docker image and publish it to a container registry.

## 13.1 What Is a Container Registry?

A container registry stores Docker and OCI container images.

Examples:

- Docker Hub.
- GitHub Container Registry (GHCR).
- Amazon Elastic Container Registry (ECR).
- Google Artifact Registry.
- Azure Container Registry.

## 13.2 Docker CI/CD Architecture

```text
Developer Push
      |
      v
   GitHub
      |
      v
GitHub Actions
      |
      v
Run Tests
      |
      v
Build Docker Image
      |
      v
Push Image
      |
      v
Container Registry
      |
      v
Production Server
      |
      v
Pull and Run Image
```

## 13.3 Example Dockerfile

For a Node.js application:

```dockerfile
FROM node:24-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY . .

EXPOSE 5000

CMD ["node", "server.js"]
```

This is a simple example. A real production Dockerfile should consider non-root execution, image size, native dependencies, health checks and build-stage separation.

## 13.4 Publish Image to GitHub Container Registry

Create:

```text
.github/workflows/docker-publish.yml
```

Example:

```yaml
name: Docker Publish

on:
  push:
    branches:
      - main

permissions:
  contents: read
  packages: write

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v7

      - name: Login to GHCR
        uses: docker/login-action@v4
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Set Lowercase Image Name
        id: image
        run: |
          echo "name=ghcr.io/${GITHUB_REPOSITORY,,}" >> "$GITHUB_OUTPUT"

      - name: Build and Push
        uses: docker/build-push-action@v7
        with:
          context: .
          push: true
          tags: |
            ${{ steps.image.outputs.name }}:${{ github.sha }}
            ${{ steps.image.outputs.name }}:latest
```

This workflow:

1. Checks out the repository.
2. Authenticates with GHCR.
3. Generates a lowercase image name.
4. Builds the Docker image.
5. Publishes the image with a commit SHA tag and a `latest` tag.

The exact action versions should be reviewed and pinned according to your security policy.

Docker maintains official documentation for this integration. [Docker Documentation](https://docs.docker.com/build/ci/github-actions?utm_source=chatgpt.com)

## 13.5 Why Use Commit SHA Tags?

Consider:

```text
ecommerce:latest
ecommerce:a1b2c3d4
ecommerce:f9e8d7c6
```

`latest` is convenient but mutable.

A commit-SHA tag identifies the exact image associated with a particular source revision.

For production deployments, prefer immutable versioned or digest-based references.

## 13.6 Image Digest

A container image digest identifies a specific image content.

Example:

```text
ghcr.io/username/ecommerce@sha256:...
```

Deploying by digest provides stronger release traceability than relying only on a mutable tag.

---

# 14. Automated Deployment to a Linux VPS

This is particularly important for developers who host applications on their own Linux servers.

Suppose you have:

- A Next.js frontend.
- An Express backend.
- A Linux VPS.
- Nginx as a reverse proxy.
- PM2 managing Node.js processes.
- MongoDB or MySQL as the database.

You want to deploy automatically whenever code is merged into `main`.

## 14.1 Traditional Manual Deployment

```text
Developer
   |
   v
Git Push
   |
   v
SSH Into VPS
   |
   v
Git Pull
   |
   v
Install Dependencies
   |
   v
Build Application
   |
   v
Restart PM2
```

## 14.2 Automated Deployment

```text
Developer Push
      |
      v
   GitHub
      |
      v
GitHub Actions
      |
      v
Run CI Checks
      |
      v
Connect to VPS
      |
      v
Deploy Application
      |
      v
Restart Application
      |
      v
Health Check
```

## 14.3 Prepare the VPS

Suppose your server contains:

```text
/var/www/ecommerce/
│
├── frontend/
│
└── backend/
```

The application should already be configured to run on the server.

For example:

```bash
pm2 start ecosystem.config.js
```

A production PM2 configuration might contain:

```javascript
module.exports = {
  apps: [
    {
      name: "ecommerce-api",
      script: "./server.js",
      instances: 1,
      exec_mode: "fork",
      env_production: {
        NODE_ENV: "production",
        PORT: 5000
      }
    }
  ]
};
```

The production environment should be configured securely on the VPS.

Do not store production `.env` files in GitHub.

## 14.4 Configure SSH Access

GitHub Actions needs a secure way to access the VPS.

Generate a dedicated SSH key pair:

```bash
ssh-keygen -t ed25519 -C "github-actions-deploy"
```

This creates:

```text
Private key
Public key
```

Add the public key to the deployment user's `authorized_keys` file on the VPS.

Store the private key as a GitHub secret:

```text
VPS_SSH_PRIVATE_KEY
```

Also configure:

```text
VPS_HOST
VPS_USER
VPS_PORT
```

Use a dedicated deployment account with only the permissions necessary for deployment.

## 14.5 Configure Host Key Verification

A secure SSH connection should verify the VPS host key.

On a trusted machine, obtain and verify the server's host key fingerprint.

Then store the verified known-hosts entry as a GitHub secret or managed configuration value.

Example secret:

```text
VPS_KNOWN_HOSTS
```

Do not blindly trust the output of `ssh-keyscan` without verifying the fingerprint through a trusted channel.

## 14.6 Create Deployment Script on VPS

Create:

```text
/var/www/ecommerce/deploy.sh
```

Example:

```bash
#!/usr/bin/env bash

set -Eeuo pipefail

APP_DIR="/var/www/ecommerce"

cd "$APP_DIR"

echo "Starting deployment..."

git fetch origin

git checkout main

git reset --hard origin/main

npm ci

npm run build

pm2 reload ecosystem.config.js --env production

echo "Deployment completed."
```

Make it executable:

```bash
chmod +x /var/www/ecommerce/deploy.sh
```

**Important:** This script is a simple deployment example. It assumes the server has the necessary source repository, Git credentials, dependencies and build configuration. It also uses `git reset --hard`, so it must not be used if the server contains uncommitted application changes that need to be preserved.

For production, consider deploying immutable release directories instead of building directly inside the live application directory.

## 14.7 GitHub Actions Deployment Workflow

Create:

```text
.github/workflows/deploy-vps.yml
```

Example:

```yaml
name: Deploy to VPS

on:
  push:
    branches:
      - main

permissions:
  contents: read

concurrency:
  group: production-deployment
  cancel-in-progress: false

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy Through SSH
        env:
          VPS_HOST: ${{ secrets.VPS_HOST }}
          VPS_USER: ${{ secrets.VPS_USER }}
          VPS_PORT: ${{ secrets.VPS_PORT }}
          VPS_PRIVATE_KEY: ${{ secrets.VPS_SSH_PRIVATE_KEY }}
          VPS_KNOWN_HOSTS: ${{ secrets.VPS_KNOWN_HOSTS }}

        run: |
          set -euo pipefail

          mkdir -p ~/.ssh
          chmod 700 ~/.ssh

          printf '%s\n' "$VPS_PRIVATE_KEY" > ~/.ssh/deploy_key
          chmod 600 ~/.ssh/deploy_key

          printf '%s\n' "$VPS_KNOWN_HOSTS" > ~/.ssh/known_hosts
          chmod 600 ~/.ssh/known_hosts

          ssh \
            -i ~/.ssh/deploy_key \
            -p "$VPS_PORT" \
            "$VPS_USER@$VPS_HOST" \
            "bash /var/www/ecommerce/deploy.sh"
```

This example assumes:

- The VPS host and port are configured correctly.
- SSH host verification is configured.
- The deployment user can execute the deployment script.
- The VPS has its own authorized way to access the source repository.
- The application has already been configured on the VPS.

## 14.8 Health Check After Deployment

Add a health-check step after deployment:

```yaml
- name: Verify Deployment
  env:
    APP_URL: ${{ vars.PRODUCTION_HEALTH_URL }}

  run: |
    for attempt in $(seq 1 10); do
      if curl --fail --silent "$APP_URL"; then
        echo "Application is healthy"
        exit 0
      fi

      echo "Waiting for application..."
      sleep 5
    done

    echo "Health check failed"
    exit 1
```

Your backend should expose a suitable health endpoint.

Example:

```javascript
app.get("/health", (req, res) => {
  res.status(200).json({
    status: "ok"
  });
});
```

A simple health endpoint should not reveal secrets or internal infrastructure details.

---

# 15. Deployment Using Release Directories

Deploying directly into the currently running application directory can cause downtime.

A safer approach is to create a new release directory for every deployment.

## 15.1 Directory Structure

```text
/var/www/ecommerce/
│
├── releases/
│   │
│   ├── release-001/
│   ├── release-002/
│   └── release-003/
│
├── shared/
│   ├── .env
│   └── uploads/
│
└── current -> releases/release-003/
```

Here:

- `releases/` stores individual application releases.
- `shared/` stores persistent data.
- `current` points to the active release.

## 15.2 Deployment Process

```text
Download New Release
        |
        v
Create Release Directory
        |
        v
Install Dependencies
        |
        v
Build Application
        |
        v
Run Tests / Validation
        |
        v
Switch Current Symlink
        |
        v
Reload Application
        |
        v
Health Check
```

Example deployment commands:

```bash
RELEASE_ID="$(date +%Y%m%d%H%M%S)"

RELEASE_DIR="/var/www/ecommerce/releases/$RELEASE_ID"

mkdir -p "$RELEASE_DIR"
```

Copy or extract the release into that directory.

After building and validating:

```bash
ln -sfn "$RELEASE_DIR" /var/www/ecommerce/current
```

Then reload the application.

**Important:** A symlink switch alone does not guarantee zero downtime. Your process manager, application startup, Nginx configuration and health checks must also support a safe transition.

---

# 16. Automated Deployment to AWS

AWS provides several services that can be integrated into GitHub Actions.

Examples:

| AWS Service | Purpose |
|---|---|
| EC2 | Virtual machines |
| ECS | Container orchestration |
| ECR | Container image registry |
| EKS | Managed Kubernetes |
| S3 | Object storage |
| CloudFront | CDN |
| Lambda | Serverless functions |
| CodeDeploy | Application deployment automation |

## 16.1 GitHub Actions and AWS Architecture

```text
Developer Push
      |
      v
   GitHub
      |
      v
GitHub Actions
      |
      v
Run Tests
      |
      v
Build Application
      |
      v
Authenticate with AWS
      |
      v
Push Image to ECR
      |
      v
Deploy to ECS / EC2 / EKS
      |
      v
Health Check
```

## 16.2 Authentication: Access Keys vs OIDC

There are two common approaches.

### Approach A: Static AWS Access Keys

You create an IAM access key and store it as a GitHub secret.

This approach has drawbacks:

- Long-lived credentials.
- Rotation requirements.
- Risk of credential exposure.
- More difficult lifecycle management.

### Approach B: OpenID Connect (OIDC)

OIDC allows GitHub Actions to request temporary AWS credentials by assuming an IAM role.

This avoids storing long-lived AWS access keys in GitHub.

**OIDC is generally the recommended approach for GitHub Actions accessing AWS.** [GitHub](https://github.com/aws-actions/configure-aws-credentials?utm_source=chatgpt.com)

## 16.3 How OIDC Works

```text
GitHub Actions
      |
      v
Request OIDC Token
      |
      v
GitHub OIDC Provider
      |
      v
AWS IAM Validates Token
      |
      v
Assume IAM Role
      |
      v
Temporary AWS Credentials
      |
      v
Access Authorized AWS Resources
```

## 16.4 Configure AWS OIDC

The general setup involves:

1. Create an OIDC identity provider in AWS IAM.
2. Use GitHub's OIDC provider URL.
3. Configure the expected audience.
4. Create an IAM role.
5. Configure a restrictive trust policy.
6. Attach only the required AWS permissions.
7. Reference the role from GitHub Actions.

Example trust-policy structure:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::ACCOUNT_ID:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:OWNER/REPOSITORY:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

This is a conceptual example. Use the exact subject claim expected by your AWS IAM OIDC configuration and GitHub repository. Current GitHub OIDC subject formats can include additional organization and repository identifiers, and environment-based jobs have different subject claims. [GitHub](https://github.com/aws-actions/configure-aws-credentials?utm_source=chatgpt.com)

## 16.5 GitHub Actions AWS Authentication

Example:

```yaml
name: AWS Authentication

on:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  aws:
    runs-on: ubuntu-latest

    steps:
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v6
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: ap-south-1

      - name: Verify AWS Identity
        run: aws sts get-caller-identity
```

`id-token: write` allows the workflow to request an OIDC token. It does not itself grant permission to modify AWS resources.

The assumed IAM role determines which AWS operations are authorized.

The action's current documentation recommends OIDC and temporary credentials. [GitHub](https://github.com/aws-actions/configure-aws-credentials?utm_source=chatgpt.com)

## 16.6 Deploy Docker Image to AWS ECR

A typical workflow:

1. Authenticate with AWS using OIDC.
2. Log in to Amazon ECR.
3. Build the Docker image.
4. Tag the image.
5. Push it to ECR.
6. Deploy the new image to ECS, EKS or another runtime.

Example login:

```yaml
- name: Login to Amazon ECR
  id: login-ecr
  uses: aws-actions/amazon-ecr-login@v2
```

Example Docker image tagging:

```text
ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/ecommerce:COMMIT_SHA
```

You should also configure the IAM role with only the required ECR and deployment permissions.

---

# 17. Deployment to Vercel and Render

Not every deployment requires a custom VPS workflow.

Managed platforms provide their own deployment integrations.

## 17.1 Vercel Deployment

Vercel supports Git-based deployment workflows.

Typical process:

```text
Developer Push
      |
      v
GitHub Repository
      |
      v
Vercel Integration
      |
      v
Build Application
      |
      v
Deploy Preview / Production
```

Common deployment behavior:

| Git Event | Typical Deployment |
|---|---|
| Push to production branch | Production deployment |
| Push to another branch | Preview deployment |
| Pull Request | Preview deployment |

The exact behavior depends on project settings.

### Vercel Environment Variables

Vercel supports separate environment-variable configuration for:

- Development.
- Preview.
- Production.

A change to environment variables may require a new deployment before the updated values are used by a build or deployment.

Official reference: [Vercel Documentation](https://vercel.com/docs?utm_source=chatgpt.com)

## 17.2 Render Deployment

Render can also connect to GitHub repositories.

Typical workflow:

```text
Developer Push
      |
      v
GitHub
      |
      v
Render
      |
      v
Build
      |
      v
Deploy
```

Render supports automatic deployments based on repository changes.

It also provides deployment configuration and environment variables.

Official reference: [Render Documentation](https://render.com/docs?utm_source=chatgpt.com)

## 17.3 Managed Deployment vs Custom CI/CD

| Feature | Managed Platform | Custom CI/CD |
|---|---|---|
| Initial setup | Usually easier | More configuration |
| Infrastructure management | Platform handles much of it | Developer manages more |
| Custom deployment process | Platform-dependent | Highly flexible |
| Server access | Limited by platform | Full control where permitted |
| Scaling | Platform-dependent | You control architecture |
| Maintenance | Less infrastructure maintenance | More responsibility |

**Important:** If Vercel or Render already handles Git-based deployment correctly, you do not need to recreate the entire deployment process in GitHub Actions. Use GitHub Actions for additional checks, testing, security scans or release orchestration where useful.

---

# 18. Development, Staging and Production Environments

Professional applications commonly use multiple environments.

## 18.1 Development

Used for active development.

Example:

```text
development.example.com
```

Characteristics:

- Frequent code changes.
- Debugging enabled where appropriate.
- Test databases.
- Development credentials.

## 18.2 Staging

Used to validate changes before production.

Example:

```text
staging.example.com
```

Characteristics:

- Similar configuration to production.
- Pre-release testing.
- Integration testing.
- User acceptance testing.
- Deployment verification.

## 18.3 Production

Used by real users.

Example:

```text
example.com
```

Characteristics:

- Strict access controls.
- Production database.
- Secure credentials.
- Monitoring and alerting.
- Controlled deployments.
- Backup and recovery procedures.

## 18.4 Environment Comparison

| Feature | Development | Staging | Production |
|---|---|---|---|
| Main purpose | Development | Pre-production validation | Real users |
| Data | Development/test data | Test or sanitized data | Real application data |
| Debugging | More permissive | Restricted | Restricted |
| Deployment | Frequent | Controlled | Carefully controlled |
| Secrets | Development secrets | Staging secrets | Production secrets |

Never use production credentials for development or staging.

## 18.5 GitHub Environments

GitHub Actions supports deployment environments.

Examples:

```text
development
staging
production
```

A job can reference an environment:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest

    environment: production

    steps:
      - run: echo "Deploying production"
```

You can configure:

- Environment secrets.
- Environment variables.
- Deployment branch restrictions.
- Required reviewers.
- Wait timers.
- Deployment protection rules.

A job referencing an environment must pass its configured protection rules before it can access that environment's secrets. [GitHub Docs](https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments?utm_source=chatgpt.com)

## 18.6 Manual Production Approval

Example:

```yaml
jobs:
  staging:
    runs-on: ubuntu-latest

    environment: staging

    steps:
      - name: Deploy Staging
        run: echo "Deploying staging"

  production:
    needs: staging

    runs-on: ubuntu-latest

    environment: production

    steps:
      - name: Deploy Production
        run: echo "Deploying production"
```

Configure the `production` environment in GitHub with required reviewers if your plan and repository visibility support that protection rule.

When the workflow reaches the production job, GitHub can pause it until approval is granted.

---

# 19. Deployment Strategies

A deployment strategy defines how a new application version replaces the existing version.

Three important strategies are:

1. Rolling Deployment.
2. Blue-Green Deployment.
3. Canary Deployment.

## 19.1 Rolling Deployment

A rolling deployment gradually replaces old application instances with new instances.

Suppose you have four instances:

```text
Version 1:
A1
A2
A3
A4
```

You update them gradually:

```text
Step 1:
A1(new) A2(old) A3(old) A4(old)

Step 2:
A1(new) A2(new) A3(old) A4(old)

Step 3:
A1(new) A2(new) A3(new) A4(old)

Step 4:
A1(new) A2(new) A3(new) A4(new)
```

Advantages:

- Gradual rollout.
- Lower resource overhead than full duplication.
- Supports controlled replacement.

Disadvantages:

- Old and new versions coexist temporarily.
- Requires backward-compatible APIs and database migrations.
- Rollback can be complicated if data formats change.

## 19.2 Blue-Green Deployment

Blue-Green deployment maintains two environments.

- Blue: Current production.
- Green: New version.

Example:

```text
             Load Balancer
                   |
              Blue Environment
              Version 1
```

Prepare Green:

```text
             Load Balancer
                   |
              Blue Environment
              Version 1

              Green Environment
              Version 2
```

After testing, switch traffic:

```text
             Load Balancer
                   |
              Green Environment
              Version 2
```

If problems occur, switch traffic back to Blue.

Advantages:

- Fast traffic switching.
- Easier application rollback.
- New version can be tested before full traffic migration.

Disadvantages:

- Requires additional infrastructure.
- Database compatibility remains important.
- Switching traffic does not reverse database changes.

## 19.3 Canary Deployment

Canary deployment sends a small percentage of traffic to the new version.

Example:

```text
Version 1: 95% Traffic
Version 2: 5% Traffic
```

After monitoring:

```text
Version 1: 50% Traffic
Version 2: 50% Traffic
```

Finally:

```text
Version 2: 100% Traffic
```

Advantages:

- Limits initial exposure.
- Allows real-world validation.
- Helps detect problems gradually.

Disadvantages:

- Requires traffic routing support.
- Requires reliable monitoring.
- Requires careful session and database compatibility.

## 19.4 Deployment Strategy Comparison

| Feature | Rolling | Blue-Green | Canary |
|---|---|---|---|
| Gradual replacement | Yes | No, typically a traffic switch | Yes |
| Extra infrastructure | Moderate | Often high | Depends |
| Traffic splitting | Not necessarily | Usually no percentage split required | Yes |
| Rollback | Depends on rollout state | Often fast | Gradual traffic reversal |
| Best use case | Multi-instance applications | Controlled version switching | Risk-managed releases |

---

# 20. Rollback Strategies

A successful CI/CD pipeline must plan for deployment failure.

## 20.1 What Is Rollback?

Rollback means returning an application to a previously working release.

Example:

```text
Release 1.0.0
      |
      v
Release 1.1.0
      |
      v
Release 1.2.0
      |
      v
Deployment Failure
      |
      v
Rollback to 1.1.0
```

## 20.2 Rollback With Docker

Suppose you deployed:

```text
ecommerce:a1b2c3
```

The previous working image was:

```text
ecommerce:d4e5f6
```

You can deploy the previous image again.

```bash
docker pull ghcr.io/username/ecommerce:d4e5f6
```

Then update the running service to use that exact image.

## 20.3 Rollback With Release Directories

Suppose:

```text
releases/
  release-001/
  release-002/
  release-003/

current -> release-003
```

To return to release 002:

```bash
ln -sfn /var/www/ecommerce/releases/release-002 \
  /var/www/ecommerce/current
```

Then reload the application using the appropriate process manager.

## 20.4 Database Rollback Is Different

Suppose version 1.2.0 changes a database column:

```text
username
```

to:

```text
display_name
```

If the new application writes only to `display_name`, rolling back to an application that expects `username` may break the system.

A safer migration approach is **expand and contract**:

1. Add the new database field.
2. Support both old and new fields.
3. Migrate existing data.
4. Update the application to use the new field.
5. Verify all consumers.
6. Remove the old field only after it is safe.

**Important:** Application rollback does not automatically roll back database migrations or external side effects such as sent emails and payment transactions.

---

# 21. Deployment Security and Supply Chain Security

CI/CD pipelines have access to source code, credentials and deployment infrastructure.

A compromised pipeline can compromise the entire application.

## 21.1 Use Least Privilege

Set workflow permissions explicitly.

Example:

```yaml
permissions:
  contents: read
```

If a workflow needs to publish packages:

```yaml
permissions:
  contents: read
  packages: write
```

Do not grant unnecessary permissions.

## 21.2 Use OIDC Instead of Long-Lived Cloud Keys

Prefer short-lived credentials for cloud access.

For AWS, use GitHub OIDC and narrowly scoped IAM roles.

## 21.3 Pin Third-Party Actions

Avoid blindly trusting mutable action tags in sensitive pipelines.

For example:

```yaml
uses: actions/checkout@v7
```

is readable, but a full commit SHA provides an immutable reference to the action code.

Use trusted sources, review updates and automate dependency maintenance.

## 21.4 Protect Secrets From Untrusted Pull Requests

Forked Pull Requests should be treated as untrusted code.

Be especially careful with:

```yaml
on:
  pull_request_target:
```

This event can run with elevated base-repository privileges depending on workflow configuration.

Do not check out and execute untrusted fork code in a privileged workflow.

GitHub's current `actions/checkout` documentation includes protections for this scenario. [GitHub](https://github.com/actions/checkout/blob/main/README.md?plain=1\&utm_source=chatgpt.com)

## 21.5 Dependency Security

Useful tools and practices include:

- Dependabot.
- Dependency review.
- npm audit.
- Software composition analysis.
- Lockfiles.
- SBOM generation.
- Vulnerability scanning.

## 21.6 Container Security

Scan Docker images for vulnerabilities.

Examples of tools:

- Trivy.
- Docker Scout.
- Grype.

Also:

- Use trusted base images.
- Avoid unnecessary packages.
- Run containers as non-root.
- Do not embed secrets in images.
- Use minimal runtime images.
- Sign or attest images when appropriate.

## 21.7 Supply Chain Security

A software supply chain includes:

```text
Source Code
    |
    v
Dependencies
    |
    v
Build Process
    |
    v
Artifacts
    |
    v
Container Registry
    |
    v
Deployment
```

Protect every stage.

Important concepts:

| Concept | Purpose |
|---|---|
| SBOM | Describes software components and dependencies |
| Provenance | Records where and how an artifact was built |
| Artifact Attestation | Provides verifiable claims about an artifact |
| Image Signing | Helps verify image authenticity |
| Dependency Scanning | Identifies vulnerable packages |

---

# 22. Advanced GitHub Actions Features

These concepts are useful in complex workflows.

## 22.1 Workflow Outputs

A job can expose an output for another job.

Example:

```yaml
jobs:
  prepare:
    runs-on: ubuntu-latest

    outputs:
      version: ${{ steps.version.outputs.value }}

    steps:
      - id: version
        run: echo "value=1.0.0" >> "$GITHUB_OUTPUT"

  deploy:
    needs: prepare
    runs-on: ubuntu-latest

    steps:
      - name: Print Version
        run: echo "${{ needs.prepare.outputs.version }}"
```

## 22.2 Step Outputs

Use:

```bash
echo "name=value" >> "$GITHUB_OUTPUT"
```

This creates an output for the current step.

## 22.3 Job Summary

GitHub Actions supports job summaries through:

```bash
echo "## Deployment Successful" >> "$GITHUB_STEP_SUMMARY"
```

You can write Markdown summaries.

## 22.4 Concurrency

Concurrency controls overlapping workflow or job executions.

Example:

```yaml
concurrency:
  group: production-deployment
  cancel-in-progress: false
```

This prevents multiple deployments in the same concurrency group from running simultaneously.

For CI on the same branch, you might instead use:

```yaml
concurrency:
  group: ci-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

This can cancel an older run when a newer commit arrives.

Use cancellation carefully for deployment workflows; interrupting an in-progress production deployment may leave infrastructure in an inconsistent state.

## 22.5 Timeout

Set a maximum job duration:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 20
```

This prevents jobs from running indefinitely.

## 22.6 Continue on Error

```yaml
- name: Optional Report
  continue-on-error: true
  run: npm run optional-report
```

Use this only for genuinely non-blocking tasks. Do not hide failures in required quality checks.

## 22.7 Workflow Dispatch Inputs

Manual workflows can receive parameters.

Example:

```yaml
on:
  workflow_dispatch:
    inputs:
      version:
        description: "Version to deploy"
        required: true
        type: string
```

Use:

```yaml
${{ inputs.version }}
```

Validate user-supplied inputs before using them in shell commands or deployment operations.

---

# 23. Complete Production CI/CD Pipeline

Now combine the concepts into one practical pipeline.

Suppose you are developing an application with:

```text
Next.js Frontend
Express Backend
MongoDB
Redis
Docker
GitHub
Linux VPS
```

Your desired workflow:

```text
                 Developer
                     |
                     v
                  Git Push
                     |
                     v
                   GitHub
                     |
                     v
              GitHub Actions
                     |
          ------------------------
          |          |           |
         Lint       Tests       Typecheck
          |          |           |
          ------------------------
                     |
                     v
                Build Docker
                     |
                     v
              Security Scan
                     |
                     v
              Push to GHCR
                     |
                     v
              Staging Deploy
                     |
                     v
              Health Check
                     |
                     v
              Production Approval
                     |
                     v
              Production Deploy
                     |
                     v
              Health Verification
                     |
                     v
               Deployment Done
```

## 23.1 Suggested Project Structure

```text
ecommerce-app/
│
├── frontend/
│   ├── app/
│   ├── components/
│   └── package.json
│
├── backend/
│   ├── controllers/
│   ├── routes/
│   ├── models/
│   └── package.json
│
├── docker/
│   ├── frontend.Dockerfile
│   └── backend.Dockerfile
│
├── .github/
│   │
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── docker-publish.yml
│   │   └── deploy.yml
│   │
│   └── actions/
│
├── docker-compose.yml
├── .dockerignore
├── .gitignore
└── README.md
```

## 23.2 CI Workflow

The CI workflow should:

- Run on Pull Requests.
- Install dependencies.
- Run linting.
- Run type checking.
- Run tests.
- Build frontend and backend.

## 23.3 Docker Workflow

The Docker workflow should:

- Build frontend and backend images.
- Tag images with commit SHAs.
- Scan images.
- Publish them to GHCR or ECR.

## 23.4 Deployment Workflow

The deployment workflow should:

- Deploy to staging first.
- Verify application health.
- Require approval for production if configured.
- Deploy the same tested image to production.
- Verify production health.
- Record the deployed image version.

**Important principle:** Ideally, build an artifact once and promote the exact same immutable artifact through staging and production. Do not rebuild separately for production if you need strong release reproducibility.

---

# 24. Common CI/CD Mistakes

| Mistake | Better Practice |
|---|---|
| Deploying without tests | Run automated tests before deployment |
| Using `npm install` indiscriminately in CI | Use `npm ci` with a lockfile |
| Hardcoding secrets | Use GitHub Secrets or secure identity federation |
| Using production DB in tests | Use isolated test databases |
| Deploying every branch to production | Restrict production deployment branches |
| Using mutable `latest` image only | Use immutable commit-SHA tags or digests |
| Running all jobs sequentially | Parallelize independent jobs |
| Repeating the same workflow in many repositories | Use reusable workflows |
| Ignoring deployment health | Run health checks |
| Using long-lived cloud access keys | Prefer OIDC |
| Running untrusted PR code with privileged secrets | Separate trusted and untrusted workflows |
| No rollback strategy | Keep previous known-good releases |
| Building on the live directory | Use release directories or immutable artifacts |
| Ignoring database migration compatibility | Use backward-compatible migration strategies |
| Using self-hosted runners without isolation | Use ephemeral and isolated runners |
| Cancelling production deployments carelessly | Configure concurrency and deployment recovery |

---

# 25. Interview Questions and Answers

## Basic-Level Questions

### Q1. What is CI/CD?

CI/CD is a software development practice that automates code integration, testing, building and application delivery or deployment.

### Q2. What is Continuous Integration?

Continuous Integration is the practice of frequently integrating code changes and automatically validating them.

### Q3. What is Continuous Delivery?

Continuous Delivery ensures that tested software is ready to be released, usually with a manual production approval step.

### Q4. What is Continuous Deployment?

Continuous Deployment automatically deploys qualifying changes to production after they pass the required checks.

### Q5. What is GitHub Actions?

GitHub Actions is GitHub's workflow automation platform used for testing, building and deploying applications.

### Q6. What is a workflow?

A workflow is an automated process defined in a YAML file.

### Q7. What is a runner?

A runner is the machine that executes GitHub Actions jobs.

### Q8. What is a job?

A job is a group of steps executed on a runner.

### Q9. What is an artifact?

An artifact is a file or output generated during a workflow, such as a build package or test report.

### Q10. What is a deployment environment?

A deployment environment represents a target such as development, staging or production.

## Intermediate-Level Questions

### Q11. What is the difference between GitHub-hosted and self-hosted runners?

GitHub-hosted runners are managed by GitHub. Self-hosted runners are managed by the organization or developer.

### Q12. What is a matrix build?

A matrix build executes jobs across multiple configuration combinations, such as different Node.js versions and operating systems.

### Q13. What is the difference between caching and artifacts?

Caching improves workflow performance by reusing data. Artifacts store outputs generated by a workflow.

### Q14. What is `needs` in GitHub Actions?

`needs` defines dependencies between jobs.

### Q15. What are GitHub Secrets?

GitHub Secrets are encrypted values used to store sensitive workflow configuration.

### Q16. What is a reusable workflow?

A reusable workflow is a workflow that another workflow can call to reuse jobs and automation.

### Q17. What is a composite action?

A composite action groups multiple steps into one reusable action.

### Q18. What is Docker image publishing?

It is the process of building a container image and uploading it to a container registry.

### Q19. Why is `npm ci` preferred in CI?

It performs a clean dependency installation using the lockfile, making CI dependency installation more reproducible.

### Q20. What is a health check?

A health check verifies whether an application or service is functioning as expected.

## Advanced-Level Questions

### Q21. What is OIDC in GitHub Actions?

OIDC allows GitHub Actions to authenticate with external identity providers, such as AWS IAM, and obtain temporary credentials.

### Q22. Why is OIDC preferred over static AWS keys?

OIDC avoids storing long-lived AWS access keys in GitHub and allows temporary credentials with role-based permissions.

### Q23. What is a blue-green deployment?

Blue-Green deployment maintains two environments and switches traffic from the old version to the new version after validation.

### Q24. What is a canary deployment?

Canary deployment gradually sends traffic to a new application version before fully releasing it.

### Q25. What is a rolling deployment?

A rolling deployment replaces old application instances with new instances gradually.

### Q26. What is a rollback?

Rollback restores an application to a previous working release.

### Q27. Why should you use immutable Docker image tags?

They make it easier to identify and deploy the exact image produced by a particular build.

### Q28. Why should production and staging use separate secrets?

Separate credentials reduce the risk of development or staging workflows accessing production infrastructure.

### Q29. What is the difference between application rollback and database rollback?

Application rollback restores application code or artifacts. Database rollback involves restoring or reversing data/schema changes and requires separate planning.

### Q30. What is software supply chain security?

It protects the software development and delivery process, including source code, dependencies, build systems, artifacts and deployment infrastructure.

---

# 26. Practical Assignments

Complete the following exercises.

## Assignment 1: Create a CI Workflow

Create:

```text
.github/workflows/ci.yml
```

Requirements:

- Run on Pull Requests.
- Checkout code.
- Set up Node.js.
- Install dependencies.
- Run ESLint.
- Run TypeScript checks.
- Run tests.
- Build the application.

## Assignment 2: Implement Matrix Testing

Test your application using:

```text
Node.js 22
Node.js 24
```

Configure GitHub Actions to run both combinations.

## Assignment 3: Configure GitHub Secrets

Create:

```text
TEST_DATABASE_URL
JWT_SECRET
```

Use them in your testing workflow.

Never print their values.

## Assignment 4: Create a Reusable Workflow

Create a reusable Node.js CI workflow.

Call it from another workflow.

## Assignment 5: Build and Publish a Docker Image

Requirements:

- Create a Dockerfile.
- Build the image using GitHub Actions.
- Tag it with the Git commit SHA.
- Push it to GHCR.
- Verify the image appears in the registry.

## Assignment 6: Deploy to a VPS

Requirements:

- Configure a dedicated deployment user.
- Configure SSH authentication.
- Verify the host key.
- Store SSH credentials in GitHub Secrets.
- Create a deployment script.
- Deploy the application.
- Verify the application with a health check.

## Assignment 7: Configure Staging and Production

Create GitHub environments:

```text
staging
production
```

Configure separate secrets and restrict production deployment.

## Assignment 8: Implement Rollback

Keep at least two release versions.

Demonstrate how to switch back to the previous working release.

## Assignment 9: AWS OIDC Integration

Create an AWS IAM OIDC provider and role.

Configure GitHub Actions to assume the role.

Verify the identity using:

```bash
aws sts get-caller-identity
```

Use a narrowly scoped IAM policy.

## Assignment 10: Complete CI/CD Project

Build a complete pipeline for an e-commerce application.

Requirements:

- GitHub repository.
- Pull Request workflow.
- Automated linting.
- Automated tests.
- Production build.
- Docker image.
- Container registry.
- Staging deployment.
- Production approval.
- Production deployment.
- Health check.
- Rollback strategy.

---

# 27. Quick Revision Notes

| Concept | Important Point |
|---|---|
| CI | Automatically validates integrated code |
| Continuous Delivery | Keeps software ready for release |
| Continuous Deployment | Automatically deploys qualifying changes |
| Workflow | Automated YAML process |
| Event | Trigger that starts a workflow |
| Runner | Machine executing a job |
| Job | Collection of steps |
| Step | Individual operation |
| Action | Reusable automation component |
| `needs` | Job dependency |
| Matrix | Multiple configuration combinations |
| Secrets | Sensitive workflow values |
| Variables | Non-sensitive configuration |
| Reusable Workflow | Reuses complete workflow jobs |
| Composite Action | Reuses a sequence of steps |
| Artifact | Workflow output |
| Cache | Reusable data for performance |
| Docker Registry | Stores container images |
| OIDC | Short-lived identity-based authentication |
| Staging | Pre-production environment |
| Production | Environment serving real users |
| Rolling Deployment | Gradual instance replacement |
| Blue-Green | Switches traffic between two environments |
| Canary | Gradually exposes new version to users |
| Rollback | Restores previous working release |
| Health Check | Verifies application availability |
| SBOM | Software component inventory |
| Provenance | Records how an artifact was produced |

---

# 28. Completion Checklist

- [ ] Understand CI/CD fundamentals.
- [ ] Understand CI vs Continuous Delivery vs Continuous Deployment.
- [ ] Understand CI/CD architecture.
- [ ] Understand GitHub Actions.
- [ ] Understand workflows, events, jobs, steps and runners.
- [ ] Configure push and Pull Request triggers.
- [ ] Configure scheduled and manual workflows.
- [ ] Use path and branch filters.
- [ ] Configure job dependencies.
- [ ] Run jobs in parallel.
- [ ] Implement matrix builds.
- [ ] Configure environment variables.
- [ ] Manage GitHub Secrets.
- [ ] Create reusable workflows.
- [ ] Create composite actions.
- [ ] Upload and download artifacts.
- [ ] Configure dependency caching.
- [ ] Automate linting and testing.
- [ ] Run database integration tests.
- [ ] Build Docker images automatically.
- [ ] Publish images to GHCR or ECR.
- [ ] Deploy applications to Linux VPS.
- [ ] Understand AWS OIDC authentication.
- [ ] Understand Vercel and Render deployments.
- [ ] Configure staging and production environments.
- [ ] Understand rolling deployments.
- [ ] Understand blue-green deployments.
- [ ] Understand canary deployments.
- [ ] Implement rollback procedures.
- [ ] Secure CI/CD workflows.
- [ ] Design a complete production CI/CD pipeline.

---

# 29. Official Documentation and Learning Resources

### GitHub Actions

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)
- [Reusable Workflows](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows)
- [GitHub Actions Security](https://docs.github.com/en/actions/security-for-github-actions)
- [Deployment Environments](https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments)

### Docker

- [Docker Build with GitHub Actions](https://docs.docker.com/build/ci/github-actions/)
- [Docker Build Push Action](https://github.com/docker/build-push-action)

### AWS

- [AWS GitHub Actions Credentials](https://github.com/aws-actions/configure-aws-credentials)
- [GitHub OIDC With AWS](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)

### Deployment Platforms

- [Vercel Documentation](https://vercel.com/docs)
- [Render Documentation](https://render.com/docs)

---

# Phase 42 Completed

You have now covered **Advanced CI/CD Pipelines, GitHub Actions and Automated Deployment**.

The most important practical skills from this phase are:

1. Building CI workflows.
2. Automating tests and production builds.
3. Managing secrets and deployment environments.
4. Publishing Docker images.
5. Deploying applications to VPS and AWS.
6. Using OIDC for secure cloud authentication.
7. Implementing production deployment strategies.
8. Building rollback and health-check mechanisms.

## Next: Phase 43 – Infrastructure as Code (IaC), Terraform and Cloud Infrastructure Automation

Upcoming topics:

1. Infrastructure as Code Fundamentals.
2. Declarative vs Imperative Infrastructure.
3. Terraform Architecture.
4. Terraform Installation and Configuration.
5. Providers and Resources.
6. Terraform Variables and Outputs.
7. Terraform State Management.
8. Terraform Modules.
9. Terraform Workspaces.
10. Terraform Provisioners and Data Sources.
11. Terraform with AWS.
12. Creating EC2 Instances Using Terraform.
13. Creating VPCs, Subnets and Security Groups.
14. Creating RDS and S3 Infrastructure.
15. Remote State and State Locking.
16. Terraform Plans and Lifecycle.
17. Infrastructure Automation with GitHub Actions.
18. Ansible Fundamentals.
19. Configuration Management.
20. Terraform vs Ansible.
21. Production Infrastructure Architecture.
22. IaC Security and Best Practices.

**When you're ready, say: "OK, Phase 43".**