# Phase 47 – CI/CD Pipelines, GitHub Actions and DevOps Automation

**Level:** Advanced  
**Domain:** DevOps, CI/CD, Automation, Cloud Deployment  
**Primary Technologies:** GitHub Actions, Git, Docker, AWS, Kubernetes, Argo CD

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand CI/CD and the DevOps lifecycle.
2. Understand Continuous Integration, Continuous Delivery and Continuous Deployment.
3. Design automated CI/CD pipelines.
4. Understand GitHub Actions architecture.
5. Create workflows using YAML.
6. Understand events, jobs, steps, runners and actions.
7. Configure environment variables and GitHub Secrets.
8. Manage workflow artifacts.
9. Configure job dependencies and parallel execution.
10. Implement matrix builds.
11. Automate testing and linting.
12. Build Docker images automatically.
13. Push images to Docker Hub and Amazon ECR.
14. Deploy applications automatically to VPS and AWS EC2.
15. Deploy applications to Kubernetes.
16. Implement GitOps using Argo CD.
17. Understand rolling, blue-green and canary deployments.
18. Secure CI/CD pipelines.
19. Implement deployment approvals and production environments.
20. Build a complete CI/CD pipeline for a Next.js and Node.js application.

---

# PART 1: INTRODUCTION TO CI/CD

# 2. What Is CI/CD?

CI/CD stands for:

- CI – Continuous Integration.
- CD – Continuous Delivery.
- CD – Continuous Deployment.

CI/CD is a software development practice that automates the process of testing, building, delivering and deploying applications.

Imagine you are developing a Node.js application.

You make some code changes and push them to GitHub.

Traditionally, you might need to:

1. Download the latest code on your server.
2. Install dependencies.
3. Run tests.
4. Build the application.
5. Stop the old application.
6. Deploy the new version.
7. Restart the application.
8. Verify that everything works.

Performing these steps manually every time is inefficient and error-prone.

CI/CD automates these processes.

## 2.1 Without CI/CD

```text
Developer
    |
    v
Write Code
    |
    v
Push to GitHub
    |
    v
Manually Download Code
    |
    v
Install Dependencies
    |
    v
Run Tests
    |
    v
Build Application
    |
    v
Deploy Application
    |
    v
Restart Server
```

Every deployment requires manual work.

## 2.2 With CI/CD

```text
Developer
    |
    v
Write Code
    |
    v
Push to GitHub
    |
    v
GitHub Actions
    |
    v
Install Dependencies
    |
    v
Run Tests
    |
    v
Build Application
    |
    v
Build Docker Image
    |
    v
Deploy Automatically
    |
    v
Application Running
```

The pipeline automatically executes the configured steps.

### Main Benefits

| Benefit | Explanation |
|---|---|
| Automation | Reduces manual deployment work |
| Faster Delivery | Speeds up software releases |
| Early Bug Detection | Identifies problems during development |
| Consistency | Uses repeatable deployment processes |
| Reliability | Reduces manual mistakes |
| Collaboration | Makes team development easier |
| Quality | Automates tests and code checks |
| Traceability | Records pipeline and deployment history |

---

# 3. What Is Continuous Integration (CI)?

Continuous Integration is the practice of frequently integrating code changes into a shared repository and automatically validating those changes.

Suppose three developers are working on the same application:

```text
Developer A -> Login Feature
Developer B -> Payment Feature
Developer C -> Dashboard Feature
```

Each developer pushes code to GitHub.

A CI pipeline automatically:

1. Checks out the code.
2. Installs dependencies.
3. Runs linting.
4. Executes automated tests.
5. Builds the application.
6. Reports success or failure.

### CI Architecture

```text
Developer A
     |
Developer B
     |
Developer C
     |
     v
GitHub Repository
     |
     v
CI Pipeline
     |
     +----------------+
     |        |       |
     v        v       v
   Lint     Tests   Build
     |        |       |
     +--------+-------+
              |
              v
        CI Result
```

If a test fails, the team receives feedback before the code is merged or released.

### Example

Suppose a developer introduces this bug:

```javascript
function calculateTotal(price, quantity) {
    return price * quantity;
}

console.log(calculateTotal(100, undefined));
```

Expected result:

```text
100
```

Actual result:

```text
NaN
```

An appropriate automated test can detect this problem during CI.

**Important:** CI does not mean automatically deploying every code change. Its primary purpose is to validate integrated code.

---

# 4. What Is Continuous Delivery?

Continuous Delivery means that every validated change can be released to production through a repeatable, automated process.

The production release may still require manual approval.

Example:

```text
Developer
    |
    v
GitHub
    |
    v
CI Pipeline
    |
    v
Tests Passed
    |
    v
Build Application
    |
    v
Prepare Release
    |
    v
Production Approval
    |
    v
Deploy to Production
```

The application is always kept in a deployable state.

### Example

A team wants to deploy its application only after a manager approves the release.

The pipeline can automatically perform:

- Testing.
- Building.
- Docker image creation.
- Image publishing.
- Deployment preparation.

But production deployment waits for approval.

That is a typical Continuous Delivery workflow.

---

# 5. What Is Continuous Deployment?

Continuous Deployment takes automation one step further.

Every code change that passes the required checks can automatically reach production without requiring a separate manual release approval.

```text
Developer
    |
    v
GitHub
    |
    v
Automated Tests
    |
    v
Build
    |
    v
Security Checks
    |
    v
Deployment
    |
    v
Production
```

### Continuous Delivery vs Continuous Deployment

| Feature | Continuous Delivery | Continuous Deployment |
|---|---|---|
| Automated Testing | Yes | Yes |
| Automated Build | Yes | Yes |
| Release Preparation | Yes | Yes |
| Production Deployment | May require approval | Automatically triggered |
| Human Approval | Optional release gate | Not normally required for each release |
| Automation Level | High | Higher |

### Important Distinction

Continuous Delivery means the application is ready to deploy.

Continuous Deployment means successful changes are automatically deployed to production.

---

# 6. DevOps Lifecycle

DevOps combines development and operations practices to improve software delivery and system reliability.

A common DevOps lifecycle is:

```text
             PLAN
               |
               v
             CODE
               |
               v
             BUILD
               |
               v
             TEST
               |
               v
            RELEASE
               |
               v
            DEPLOY
               |
               v
            OPERATE
               |
               v
            MONITOR
               |
               +-----------> Feedback
```

### DevOps Stages

| Stage | Purpose | Example Tools |
|---|---|---|
| Plan | Plan features and tasks | Jira |
| Code | Develop application | VS Code, Git |
| Build | Compile and package | npm, Docker |
| Test | Validate application | Jest, Vitest, Playwright |
| Release | Prepare version | GitHub Releases |
| Deploy | Deploy application | GitHub Actions, Kubernetes |
| Operate | Run infrastructure | AWS, Linux |
| Monitor | Observe system health | Prometheus, Grafana |

DevOps is not a single tool or platform. It is a combination of culture, practices, processes and technologies.

---

# PART 2: CI/CD PIPELINE ARCHITECTURE

# 7. What Is a CI/CD Pipeline?

A CI/CD pipeline is an automated sequence of operations that takes source code and transforms it into a tested, deployable application.

Example:

```text
                  GitHub Repository
                          |
                          v
                      CI Trigger
                          |
                          v
                   Install Dependencies
                          |
                          v
                       Lint Code
                          |
                          v
                     Run Unit Tests
                          |
                          v
                    Build Application
                          |
                          v
                    Build Docker Image
                          |
                          v
                  Security Vulnerability Scan
                          |
                          v
                 Push Image to Registry
                          |
                          v
                    Deploy Application
                          |
                          v
                    Verify Deployment
```

## 7.1 Common Pipeline Stages

| Stage | Responsibility |
|---|---|
| Source | Retrieve application code |
| Install | Install dependencies |
| Lint | Check code quality |
| Test | Run automated tests |
| Build | Build application |
| Scan | Check for vulnerabilities |
| Package | Create deployable artifact |
| Publish | Push artifact to registry |
| Deploy | Release application |
| Verify | Confirm deployment health |

## 7.2 Pipeline Failure

Suppose your test stage fails.

```text
Install Dependencies
        |
        v
     Lint Code
        |
        v
    Run Tests
        |
        v
     FAILED
        |
        v
    Stop Pipeline
```

By default, a failed GitHub Actions step causes its job to fail and prevents subsequent steps from running, unless failure-handling conditions are configured.

This prevents an invalid application from progressing through the normal deployment pipeline.

---

# PART 3: INTRODUCTION TO GITHUB ACTIONS

# 8. What Is GitHub Actions?

GitHub Actions is an automation platform integrated into GitHub.

It allows you to automate:

- Testing.
- Building.
- Deployment.
- Code quality checks.
- Security scanning.
- Release creation.
- Repository maintenance.

Official documentation:

[GitHub Actions Documentation](https://docs.github.com/en/actions)

### Example

Suppose you have a Next.js project.

You push code to GitHub:

```bash
git add .
git commit -m "Add login feature"
git push origin main
```

GitHub Actions detects the push and runs the configured workflow.

```text
Developer
    |
    v
git push
    |
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    v
Install Dependencies
    |
    v
Run Tests
    |
    v
Build Next.js
    |
    v
Workflow Result
```

You do not need to log into a server to execute the CI checks.

---

# 9. GitHub Actions Architecture

GitHub Actions uses several important concepts.

1. Workflow.
2. Event.
3. Job.
4. Step.
5. Action.
6. Runner.

### Architecture

```text
GitHub Repository
       |
       v
    Workflow
       |
       v
      Event
       |
       v
      Jobs
       |
       +-------------+
       |             |
       v             v
     Job A         Job B
       |             |
       v             v
    Runner         Runner
       |             |
   +---+---+     +---+---+
   |       |     |       |
   v       v     v       v
 Step 1  Step 2 Step 1  Step 2
```

Let's understand each component.

---

# 10. What Is a Workflow?

A Workflow is an automated process defined in a YAML file.

For example:

```text
.github/
    workflows/
        ci.yml
```

A workflow defines:

- When automation should run.
- Which jobs should execute.
- Which runners should execute jobs.
- Which steps should run.
- What permissions and environment are required.

A repository can contain multiple workflows.

Example:

```text
.github/workflows/
    ci.yml
    deploy-staging.yml
    deploy-production.yml
    security-scan.yml
```

Each workflow can have a different responsibility.

---

# 11. What Is an Event?

An Event is something that triggers a workflow.

Examples:

- Push.
- Pull Request.
- Issue creation.
- Release publication.
- Scheduled execution.
- Manual execution.
- Workflow completion.

### Common GitHub Actions Events

| Event | Description |
|---|---|
| `push` | Code pushed to a branch |
| `pull_request` | Pull Request activity |
| `workflow_dispatch` | Manual workflow execution |
| `schedule` | Scheduled workflow execution |
| `release` | Release event |
| `issues` | Issue activity |
| `workflow_run` | Another workflow completes |
| `delete` | Branch or tag deleted |

Official reference: [GitHub Actions Events](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows)

---

# 12. What Is a Job?

A Job is a collection of steps that execute on a runner.

Example:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Print Message
        run: echo "Running tests"
```

Here:

```yaml
test:
```

is the job identifier.

And:

```yaml
runs-on: ubuntu-latest
```

defines the runner environment.

A workflow can contain multiple jobs.

Example:

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Linting"

  test:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Testing"

  build:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Building"
```

By default, independent jobs can run in parallel.

---

# 13. What Is a Step?

A Step is an individual operation inside a Job.

A step can:

- Run a shell command.
- Execute a GitHub Action.
- Install software.
- Run tests.
- Build an application.
- Deploy an application.

Example:

```yaml
steps:
  - name: Print Hello
    run: echo "Hello"

  - name: Print Current Directory
    run: pwd

  - name: List Files
    run: ls -la
```

Steps within a job execute sequentially by default.

---

# 14. What Is a Runner?

A Runner is the machine that executes a GitHub Actions job.

There are two major categories:

1. GitHub-hosted runners.
2. Self-hosted runners.

## 14.1 GitHub-Hosted Runners

GitHub provides managed virtual machine environments.

Examples:

```yaml
runs-on: ubuntu-latest
```

```yaml
runs-on: windows-latest
```

```yaml
runs-on: macos-latest
```

These are useful for common CI/CD workloads.

## 14.2 Self-Hosted Runners

A self-hosted runner is a machine that you manage and configure.

It can be:

- A Linux server.
- A Windows machine.
- An EC2 instance.
- An on-premises server.

Example:

```text
GitHub Repository
       |
       v
GitHub Actions
       |
       v
Self-Hosted Runner
       |
       v
Your Infrastructure
```

### GitHub-Hosted vs Self-Hosted

| Feature | GitHub-Hosted | Self-Hosted |
|---|---|---|
| Infrastructure | Managed by GitHub | Managed by you |
| Maintenance | Less operational work | Your responsibility |
| Customization | Supported environments | More control |
| Private Network Access | Requires supported networking setup | Can access permitted internal resources |
| Security Maintenance | GitHub manages runner infrastructure | You must maintain the machine |
| Best Use | Standard CI/CD | Specialized or private workloads |

**Security warning:** Never run untrusted pull-request code on a self-hosted runner that has access to sensitive infrastructure. Isolate runners and treat them as privileged systems.

Official reference: [GitHub Actions Runners](https://docs.github.com/en/actions/concepts/runners)

---

# 15. What Is an Action?

An Action is a reusable unit of automation.

Instead of writing every operation manually, you can use existing Actions.

For example:

```yaml
- name: Checkout Repository
  uses: actions/checkout@v4
```

This Action checks out your repository code.

Another example:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4

  with:
    node-version: 22
```

This Action configures Node.js in the runner environment.

**Important:** The examples use readable major-version tags for learning. In production, review third-party Actions and pin them to verified full commit SHAs to reduce supply-chain risk.

GitHub recommends pinning Actions to full commit SHAs and restricting workflow permissions. [GitHub Docs](https://docs.github.com/en/code-security/tutorials/secure-your-organization/protect-against-threats?utm_source=chatgpt.com)

---

# PART 4: YOUR FIRST GITHUB ACTIONS WORKFLOW

# 16. Create Your First Workflow

Let's create a simple workflow.

Inside your project, create:

```text
.github/
    workflows/
        hello.yml
```

Add:

```yaml
name: My First Workflow

on:
  push:
    branches:
      - main

jobs:
  hello:
    runs-on: ubuntu-latest

    steps:
      - name: Print Greeting
        run: echo "Hello from GitHub Actions!"

      - name: Show Current Directory
        run: pwd

      - name: List Files
        run: ls -la
```

### Explanation

```yaml
name: My First Workflow
```

Defines the workflow name.

```yaml
on:
  push:
    branches:
      - main
```

Runs the workflow when code is pushed to `main`.

```yaml
jobs:
  hello:
```

Defines a job named `hello`.

```yaml
runs-on: ubuntu-latest
```

Uses a GitHub-hosted Ubuntu runner.

```yaml
steps:
```

Defines the operations to execute.

## 16.1 Push Workflow to GitHub

```bash
git add .github/workflows/hello.yml

git commit -m "Add first GitHub Actions workflow"

git push origin main
```

Open your GitHub repository.

Navigate to:

```text
Repository
    |
    v
Actions
    |
    v
My First Workflow
```

You should see the workflow execution.

---

# 17. Workflow YAML Structure

A typical workflow looks like this:

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Run Command
        run: echo "Building application"
```

### Important Workflow Fields

| Field | Purpose |
|---|---|
| `name` | Workflow name |
| `on` | Workflow triggers |
| `jobs` | Defines jobs |
| `runs-on` | Runner environment |
| `steps` | Job operations |
| `uses` | Execute an Action |
| `run` | Execute shell commands |
| `with` | Pass Action inputs |
| `env` | Define environment variables |
| `permissions` | Configure token permissions |
| `needs` | Define job dependencies |
| `if` | Conditional execution |

---

# PART 5: TRIGGERS AND EVENTS

# 18. Push Trigger

Run a workflow whenever code is pushed to `main`.

```yaml
on:
  push:
    branches:
      - main
```

You can also target multiple branches:

```yaml
on:
  push:
    branches:
      - main
      - develop
      - staging
```

## 18.1 Pull Request Trigger

```yaml
on:
  pull_request:
    branches:
      - main
```

This runs when a Pull Request targeting `main` has relevant activity.

Common uses:

- Code validation.
- Unit tests.
- Linting.
- Build verification.

## 18.2 Manual Trigger

Use:

```yaml
on:
  workflow_dispatch:
```

Now the workflow can be started manually from GitHub Actions.

You can add inputs:

```yaml
name: Manual Deployment

on:
  workflow_dispatch:
    inputs:
      environment:
        description: Select deployment environment
        required: true
        type: choice

        options:
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Display Environment
        run: echo "Deploying to ${{ inputs.environment }}"
```

## 18.3 Scheduled Trigger

You can run workflows using cron expressions.

Example:

```yaml
on:
  schedule:
    - cron: "0 2 * * *"
```

This schedules execution daily at 02:00 UTC.

Remember that GitHub Actions scheduled workflows use UTC.

Scheduled execution may be delayed during high load.

## 18.4 Multiple Triggers

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  workflow_dispatch:
```

This workflow can run for pushes, relevant pull requests and manual execution.

**Important:** YAML interprets some words as special values in certain parsers. GitHub's documented workflow examples use `on:` directly; when validating workflow YAML with external tools, use a GitHub Actions-aware parser.

---

# PART 6: NODE.JS CI PIPELINE

# 19. Automate Node.js Testing

Let's create a CI pipeline for a Node.js application.

Suppose your project structure is:

```text
node-app/
    src/
    tests/
    package.json
    package-lock.json
```

Your `package.json` contains:

```json
{
  "scripts": {
    "start": "node src/index.js",
    "test": "jest",
    "lint": "eslint ."
  }
}
```

Your workflow will:

1. Checkout the code.
2. Install Node.js.
3. Install dependencies.
4. Run linting.
5. Run tests.

Create:

```text
.github/workflows/node-ci.yml
```

Add:

```yaml
name: Node.js CI

on:
  push:
    branches:
      - main

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

      - name: Run Lint
        run: npm run lint

      - name: Run Tests
        run: npm test

      - name: Build Application
        run: npm run build --if-present
```

### Important: npm ci vs npm install

| Command | Purpose |
|---|---|
| `npm install` | Installs dependencies and may update the lockfile |
| `npm ci` | Installs dependencies using the lockfile in a clean, reproducible manner |

For CI pipelines, `npm ci` is usually preferred when a valid `package-lock.json` is committed.

### Why Use `actions/setup-node`?

It configures the required Node.js version.

It also supports caching npm dependencies.

This can reduce installation time across workflow runs.

---

# 20. Automate Next.js CI

Suppose your Next.js project has:

```text
nextjs-app/
    app/
    public/
    package.json
    package-lock.json
    next.config.ts
```

Your `package.json` might contain:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint ."
  }
}
```

Create:

```text
.github/workflows/nextjs-ci.yml
```

Add:

```yaml
name: Next.js CI

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:
  build:
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

      - name: Run Lint
        run: npm run lint

      - name: Build Next.js Application
        run: npm run build
```

Now every relevant push or Pull Request can trigger an automated build.

**Important:** If your Next.js build requires environment variables, configure suitable non-production test values or GitHub Secrets. Never hardcode real credentials into the workflow.

---

# PART 7: JOB DEPENDENCIES AND PARALLEL EXECUTION

# 21. Understanding Job Dependencies

By default, independent jobs can execute in parallel.

Example:

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Linting"

  test:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Testing"

  build:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Building"
```

The jobs do not have an explicit dependency relationship.

## 21.1 Use needs

Suppose you want:

```text
Lint
  |
  v
Test
  |
  v
Build
  |
  v
Deploy
```

Use `needs`.

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest

    steps:
      - run: echo "Linting"

  test:
    needs: lint
    runs-on: ubuntu-latest

    steps:
      - run: echo "Testing"

  build:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - run: echo "Building"

  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - run: echo "Deploying"
```

### Parallel Jobs with a Final Dependency

You can also use:

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
    runs-on: ubuntu-latest
    steps:
      - run: echo "Build"

  deploy:
    needs:
      - lint
      - test
      - build

    runs-on: ubuntu-latest

    steps:
      - run: echo "Deploy"
```

Here:

```text
       Lint
         |
       Test
         |
       Build
         |
         v
       Deploy
```

More accurately, the first three jobs run in parallel, and `deploy` waits for all three to succeed.

---

# PART 8: ENVIRONMENT VARIABLES AND SECRETS

# 22. What Are Environment Variables?

Environment variables store configuration values that applications and commands can access at runtime.

Examples:

```text
NODE_ENV=production
PORT=3000
APP_ENV=staging
```

In GitHub Actions, you can define environment variables at different levels.

## 22.1 Workflow-Level Variables

```yaml
name: CI Pipeline

env:
  NODE_ENV: test
  APP_NAME: my-node-app

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Print Variables
        run: |
          echo "Environment: $NODE_ENV"
          echo "Application: $APP_NAME"
```

## 22.2 Job-Level Variables

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    env:
      NODE_ENV: production

    steps:
      - run: echo "$NODE_ENV"
```

## 22.3 Step-Level Variables

```yaml
steps:
  - name: Run Application
    env:
      PORT: 3000

    run: echo "Port is $PORT"
```

### Scope Comparison

| Scope | Availability |
|---|---|
| Workflow | All jobs and steps in the workflow |
| Job | All steps in that job |
| Step | Only the specified step |

---

# 23. What Are GitHub Secrets?

Secrets store sensitive values used by workflows.

Examples:

- Docker Hub passwords.
- AWS credentials.
- SSH private keys.
- API tokens.
- Database credentials.

You should never hardcode real credentials in workflow files.

### Incorrect Approach

```yaml
env:
  DB_PASSWORD: my-real-password
```

This exposes credentials in source code.

### Correct Approach

Store the credential in GitHub Secrets.

Repository navigation:

```text
GitHub Repository
       |
       v
Settings
       |
       v
Secrets and Variables
       |
       v
Actions
       |
       v
New Repository Secret
```

For example:

```text
DOCKER_USERNAME
DOCKER_PASSWORD
```

Use them:

```yaml
steps:
  - name: Login to Docker Hub
    uses: docker/login-action@v3

    with:
      username: ${{ secrets.DOCKER_USERNAME }}
      password: ${{ secrets.DOCKER_PASSWORD }}
```

### GitHub Secrets Best Practices

1. Never commit credentials to Git.
2. Use minimum required permissions.
3. Rotate credentials.
4. Restrict secrets to appropriate environments.
5. Avoid printing secrets in logs.
6. Do not expose deployment secrets to untrusted pull-request workflows.
7. Prefer short-lived credentials through OIDC when supported.

Official reference: [GitHub Secrets](https://docs.github.com/en/actions/concepts/security/secrets)

---

# 24. GitHub Variables vs Secrets

GitHub provides both Variables and Secrets.

| Feature | Variables | Secrets |
|---|---|---|
| Purpose | Non-sensitive configuration | Sensitive configuration |
| Example | APP_ENV | API_TOKEN |
| Visibility | Generally readable in workflow configuration | Protected and masked where applicable |
| Access | `vars` context | `secrets` context |

Example:

```yaml
env:
  APP_ENV: ${{ vars.APP_ENV }}
```

Secret:

```yaml
env:
  API_TOKEN: ${{ secrets.API_TOKEN }}
```

Remember that masking is not a complete security boundary. Avoid exposing secrets through command output, generated artifacts or other indirect channels.

---

# PART 9: ARTIFACTS AND CACHING

# 25. What Is a Workflow Artifact?

An artifact is a file or collection of files produced during a workflow run and stored for later use.

Examples:

- Build output.
- Test reports.
- Screenshots.
- Coverage reports.
- Compiled files.
- Deployment packages.

Suppose your build produces:

```text
dist/
```

You can upload this directory as an artifact.

## 25.1 Upload Artifact

```yaml
steps:
  - name: Build Application
    run: npm run build

  - name: Upload Build Artifact
    uses: actions/upload-artifact@v4

    with:
      name: application-build
      path: dist/
```

## 25.2 Download Artifact

A later job can download the artifact.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Create Build Directory
        run: |
          mkdir dist
          echo "Application Build" > dist/index.txt

      - name: Upload Artifact
        uses: actions/upload-artifact@v4

        with:
          name: application-build
          path: dist/

  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Download Artifact
        uses: actions/download-artifact@v4

        with:
          name: application-build

      - name: Display Artifact
        run: ls -la
```

### Important

Separate GitHub-hosted jobs normally run on separate runner instances.

Do not assume that files created in one job automatically exist in another job.

Artifacts provide a way to transfer files between jobs.

Official reference: [Workflow Artifacts](https://docs.github.com/en/actions/concepts/workflows-and-actions/workflow-artifacts)

---

# 26. What Is Dependency Caching?

Dependency caching stores reusable files to reduce repeated installation work.

For example:

```bash
npm ci
```

may take time because it downloads dependencies.

Caching can improve subsequent workflow runs.

With `actions/setup-node`, npm caching can be configured:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4

  with:
    node-version: 22
    cache: npm
```

This configures caching based on the dependency lockfile.

### Artifact vs Cache

| Feature | Artifact | Cache |
|---|---|---|
| Purpose | Preserve workflow output | Reuse dependencies or generated data |
| Typical Example | Build package | npm cache |
| Sharing Between Jobs | Yes, through artifact actions | Depends on cache configuration |
| Retention | Configurable artifact retention | Managed by cache policies |
| Primary Goal | Transfer or retain output | Improve workflow performance |

**Important:** Do not use a dependency cache as a substitute for transferring a required build artifact between jobs.

Official reference: [GitHub Actions Dependency Caching](https://docs.github.com/en/actions/concepts/workflows-and-actions/dependency-caching)

---

# PART 10: MATRIX BUILDS

# 27. What Is a Matrix Build?

A Matrix Build allows the same job to execute with multiple configurations.

Suppose you want to test your Node.js application on:

```text
Node.js 20
Node.js 22
Node.js 24
```

Instead of creating three jobs manually, use a matrix.

Example:

```yaml
name: Node.js Matrix Testing

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version:
          - 20
          - 22
          - 24

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4

        with:
          node-version: ${{ matrix.node-version }}

      - name: Install Dependencies
        run: npm ci

      - name: Run Tests
        run: npm test
```

GitHub creates separate job executions for the matrix combinations.

### Architecture

```text
             Matrix Job
                 |
       +---------+---------+
       |         |         |
       v         v         v
    Node 20   Node 22   Node 24
       |         |         |
       v         v         v
     Tests     Tests     Tests
```

### When Is Matrix Useful?

- Testing multiple Node.js versions.
- Testing multiple operating systems.
- Testing multiple browser versions.
- Testing different runtime configurations.
- Validating compatibility.

**Important:** Use supported Node.js versions appropriate for your application and dependency compatibility.

---

# PART 11: CONDITIONAL EXECUTION

# 28. What Is Conditional Execution?

Sometimes you want a job or step to execute only when a condition is satisfied.

GitHub Actions supports the `if` keyword.

Example:

```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main'

    runs-on: ubuntu-latest

    steps:
      - run: echo "Deploying production"
```

This job runs only for the `main` branch.

## 28.1 Condition Based on Event

```yaml
- name: Deploy
  if: github.event_name == 'push'

  run: echo "Deployment triggered by push"
```

## 28.2 Always Execute

```yaml
- name: Print Final Message
  if: always()

  run: echo "Workflow step finished"
```

## 28.3 Execute After Failure

```yaml
- name: Failure Notification
  if: failure()

  run: echo "Pipeline failed"
```

### Common Conditional Functions

| Function | Purpose |
|---|---|
| `success()` | Previous required steps or dependencies succeeded |
| `failure()` | A previous step or required job failed |
| `always()` | Run regardless of previous success or failure, subject to cancellation behavior |
| `cancelled()` | Workflow was cancelled |
| `contains()` | Checks whether a value contains another value |
| `startsWith()` | Checks a string prefix |
| `endsWith()` | Checks a string suffix |

---

# PART 12: DOCKER BUILD AND PUSH AUTOMATION

# 29. Automate Docker Image Building

You learned Docker in Phase 45.

Now let's integrate Docker with GitHub Actions.

Suppose your project contains:

```text
node-app/
    Dockerfile
    package.json
    src/
```

You want every successful push to `main` to:

1. Build a Docker image.
2. Log in to Docker Hub.
3. Push the image to Docker Hub.

### Architecture

```text
Developer
    |
    v
GitHub
    |
    v
GitHub Actions
    |
    v
Docker Build
    |
    v
Docker Image
    |
    v
Docker Hub
```

---

# 30. Create Docker Hub Secrets

Open your GitHub repository.

Navigate to:

```text
Settings
    |
    v
Secrets and Variables
    |
    v
Actions
```

Create:

```text
DOCKER_USERNAME
DOCKER_PASSWORD
```

Use an appropriately scoped Docker Hub access token rather than your account password.

---

# 31. Docker Build and Push Workflow

Create:

```text
.github/workflows/docker.yml
```

Add:

```yaml
name: Docker Build and Push

on:
  push:
    branches:
      - main

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v3

        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v6

        with:
          context: .
          push: true

          tags: |
            ${{ secrets.DOCKER_USERNAME }}/node-app:latest
            ${{ secrets.DOCKER_USERNAME }}/node-app:${{ github.sha }}
```

### Explanation

`docker/setup-buildx-action` configures Docker Buildx.

`docker/login-action` authenticates with Docker Hub.

`docker/build-push-action` builds and pushes the image.

### Important: Image Tags

The workflow creates two tags:

```text
node-app:latest
```

And:

```text
node-app:<commit-sha>
```

The commit SHA provides a traceable image identifier.

For production, prefer immutable tags or image digests rather than relying only on `latest`.

---

# PART 13: AUTOMATING AWS ECR

# 32. What Is Amazon ECR?

Amazon ECR stands for **Amazon Elastic Container Registry**.

It is AWS's managed container image registry.

You can store:

- Docker images.
- OCI container images.
- Application images.
- Versioned deployment images.

Official documentation:

[Amazon ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

### Architecture

```text
GitHub Actions
      |
      v
Build Docker Image
      |
      v
Authenticate with AWS
      |
      v
Amazon ECR
      |
      v
Store Image
      |
      v
Amazon EKS
```

---

# 33. GitHub Actions with AWS OIDC

To authenticate GitHub Actions with AWS, you should generally prefer OpenID Connect (OIDC) over long-lived AWS access keys.

### Why?

Traditional authentication often uses:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

These are long-lived credentials.

If leaked, an attacker may use them until they are revoked or expire according to the credential type.

OIDC allows GitHub Actions to obtain short-lived AWS credentials by assuming an IAM role under configured trust conditions.

### Architecture

```text
GitHub Actions
      |
      v
GitHub OIDC Provider
      |
      v
AWS IAM Role
      |
      v
Temporary AWS Credentials
      |
      v
Amazon ECR
```

### Basic Setup Requirements

You need:

1. An IAM OIDC identity provider for GitHub.
2. An IAM role with a trust policy.
3. A role policy allowing the required ECR operations.
4. GitHub workflow permission to request an OIDC token.
5. Correct repository and branch or environment restrictions in the trust policy.

Official reference: [GitHub Actions OpenID Connect](https://docs.github.com/en/actions/concepts/security/openid-connect)

## 33.1 Example AWS ECR Workflow

The following example assumes:

- An ECR repository already exists.
- The IAM OIDC provider is configured.
- The IAM role trusts the correct GitHub repository and branch.
- The role has only the permissions needed to push to the target repository.

```yaml
name: Build and Push to ECR

on:
  push:
    branches:
      - main

permissions:
  contents: read
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4

        with:
          role-to-assume: arn:aws:iam::123456789012:role/github-ecr-role
          aws-region: ap-south-1

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build and Push Image
        env:
          REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          REPOSITORY: node-app
          IMAGE_TAG: ${{ github.sha }}

        run: |
          docker build -t $REGISTRY/$REPOSITORY:$IMAGE_TAG .
          docker push $REGISTRY/$REPOSITORY:$IMAGE_TAG
```

Replace:

```text
123456789012
```

with your AWS account ID.

Also ensure that the IAM role and repository permissions are configured correctly.

### Important Security Points

- Restrict the OIDC trust policy to the intended repository.
- Restrict the branch or deployment environment where appropriate.
- Do not grant unrestricted AWS administrator permissions.
- Use a dedicated deployment role.
- Do not expose production credentials to pull-request workflows.
- Use immutable image tags.

AWS documents the ECR authentication and GitHub OIDC integration in its official guides. [GitHub Docs](https://docs.github.com/en/code-security/tutorials/secure-your-organization/protect-against-threats?utm_source=chatgpt.com)

---

# PART 14: AUTOMATED DEPLOYMENT TO VPS

# 34. What Is Automated VPS Deployment?

Suppose you have a Node.js application hosted on an Ubuntu VPS.

Traditionally, you might connect manually:

```bash
ssh user@SERVER_IP
```

Then:

```bash
cd /var/www/my-app

git pull origin main

npm install

npm run build

pm2 restart my-app
```

This process can be automated through GitHub Actions.

### Architecture

```text
Developer
    |
    v
GitHub Push
    |
    v
GitHub Actions
    |
    v
SSH Connection
    |
    v
Ubuntu VPS
    |
    v
Pull Latest Code
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

---

# 35. Configure SSH Deployment

You need:

- VPS IP address.
- SSH username.
- SSH private key.
- Application directory.
- Correct server permissions.

Store these values as GitHub Secrets:

```text
VPS_HOST
VPS_USER
VPS_SSH_KEY
```

Do not put private SSH keys directly in the workflow file.

### Example Workflow

```yaml
name: Deploy Node.js to VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Deploy Through SSH
        uses: appleboy/ssh-action@v1

        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}

          script: |
            cd /var/www/my-app

            git pull origin main

            npm ci

            npm run build

            pm2 restart my-app
```

This is an educational example. The SSH Action must be reviewed and pinned to a verified full commit SHA for production use.

### Production Improvements

A stronger production deployment should also:

1. Restrict SSH access.
2. Use a dedicated deployment user.
3. Restrict the SSH key to the necessary host and permissions.
4. Run tests before deployment.
5. Verify the application after restart.
6. Keep a previous working release for rollback.
7. Avoid deploying directly from arbitrary branches.
8. Avoid running untrusted scripts with production privileges.

**Important:** A deployment pipeline should not blindly run `git pull` and restart an application without validation and recovery planning.

---

# PART 15: DEPLOYMENT TO AWS EC2

# 36. Automated Deployment to EC2

Suppose you have an EC2 instance running:

```text
Ubuntu
Nginx
Node.js
PM2
```

You want GitHub Actions to deploy your application automatically.

### Architecture

```text
GitHub Repository
       |
       v
GitHub Actions
       |
       v
Build and Test
       |
       v
SSH to EC2
       |
       v
Deploy Application
       |
       v
PM2 Restart
       |
       v
Nginx
       |
       v
Users
```

The deployment process is similar to VPS deployment.

However, you should consider AWS-specific access management and networking.

### Recommended Improvements

- Use a dedicated IAM role for AWS operations.
- Restrict EC2 security group SSH access.
- Consider AWS Systems Manager Session Manager for administrative access.
- Use immutable release directories.
- Keep deployment logs.
- Add health checks.
- Implement rollback.

### EC2 vs EKS Deployment

| Feature | EC2 | EKS |
|---|---|---|
| Deployment Target | Virtual machine | Kubernetes cluster |
| Application Management | PM2, systemd, Docker | Kubernetes resources |
| Scaling | ASG or manual | HPA and node autoscaling |
| Networking | VPC and EC2 networking | Kubernetes and VPC networking |
| Complexity | Lower for simpler applications | Higher |
| Best Use | Traditional application hosting | Container orchestration |

---

# PART 16: DEPLOYMENT TO KUBERNETES

# 37. Automating Kubernetes Deployment

You learned Kubernetes in Phase 46.

Now connect GitHub Actions with Kubernetes.

Suppose your application image is pushed to ECR:

```text
123456789012.dkr.ecr.ap-south-1.amazonaws.com/node-app:COMMIT_SHA
```

Your workflow should:

1. Run tests.
2. Build Docker image.
3. Push image to ECR.
4. Authenticate to the Kubernetes cluster.
5. Update the Deployment.
6. Wait for rollout completion.

### Architecture

```text
Developer
    |
    v
GitHub
    |
    v
GitHub Actions
    |
    +-------------------+
    |                   |
    v                   v
   Tests            Docker Build
                        |
                        v
                      ECR
                        |
                        v
                     EKS
                        |
                        v
                   Deployment
                        |
                        v
                       Pods
```

## 37.1 Example Kubernetes Deployment Workflow

This example assumes an EKS cluster and an appropriately configured IAM role.

```yaml
name: Deploy to EKS

on:
  push:
    branches:
      - main

permissions:
  contents: read
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4

        with:
          role-to-assume: arn:aws:iam::123456789012:role/github-eks-deploy
          aws-region: ap-south-1

      - name: Configure kubectl
        run: |
          aws eks update-kubeconfig \
            --region ap-south-1 \
            --name production-cluster

      - name: Update Deployment Image
        run: |
          kubectl set image deployment/backend \
            backend=123456789012.dkr.ecr.ap-south-1.amazonaws.com/node-app:${GITHUB_SHA} \
            -n production

      - name: Verify Deployment
        run: |
          kubectl rollout status deployment/backend \
            -n production \
            --timeout=180s
```

### Important Requirements

This workflow assumes:

- The image has already been built and pushed to ECR.
- The Deployment already exists.
- The container name is `backend`.
- The `production` namespace exists.
- AWS IAM permissions allow the required EKS authentication.
- Kubernetes RBAC authorizes the identity to update the Deployment.
- The image tag exists in ECR.

A production pipeline should usually include the image build and publish stages before deployment.

### Security Recommendation

Do not give the GitHub deployment role unrestricted cluster-admin permissions.

Grant only the permissions required for the intended namespace and resources.

---

# PART 17: ENVIRONMENTS AND APPROVALS

# 38. What Are GitHub Environments?

GitHub Environments allow you to define deployment targets such as:

```text
development
staging
production
```

Each environment can have:

- Environment-specific secrets.
- Environment-specific variables.
- Deployment branch restrictions.
- Required reviewers.
- Deployment protection rules.

Example:

```text
GitHub Actions
      |
      v
   Workflow
      |
      +-----------+
      |           |
      v           v
   Staging    Production
      |           |
      v           v
  Auto Deploy  Approval
                  |
                  v
              Deployment
```

Official reference: [GitHub Deployment Environments](https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments)

## 38.1 Configure Production Environment

In GitHub:

```text
Repository
    |
    v
Settings
    |
    v
Environments
    |
    v
New Environment
    |
    v
production
```

Configure the required reviewers and branch restrictions where supported by your plan and repository configuration.

## 38.2 Use Environment in Workflow

```yaml
name: Production Deployment

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest

    environment:
      name: production

    steps:
      - name: Deploy Application
        run: echo "Deploying to production"
```

If approval protection rules are configured, the job waits until the requirements are satisfied.

### Why Are Approvals Useful?

Suppose you have:

```text
Development -> Automatic
Staging -> Automatic
Production -> Manual Approval
```

This provides automation while preserving a human release gate for production.

---

# PART 18: RELEASE MANAGEMENT

# 39. What Is a Software Release?

A software release is a specific version of an application made available for deployment or distribution.

Examples:

```text
v1.0.0
v1.1.0
v1.2.0
v2.0.0
```

Semantic Versioning commonly follows:

```text
MAJOR.MINOR.PATCH
```

| Component | Meaning |
|---|---|
| MAJOR | Incompatible changes |
| MINOR | Backward-compatible functionality |
| PATCH | Backward-compatible bug fixes |

Example:

```text
1.4.2
```

Means:

- Major: 1
- Minor: 4
- Patch: 2

## 39.1 Git Tags

Create a tag:

```bash
git tag v1.0.0
```

Push it:

```bash
git push origin v1.0.0
```

GitHub can use tags to trigger release workflows.

## 39.2 Release Trigger

```yaml
name: Release Pipeline

on:
  push:
    tags:
      - "v*"

jobs:
  release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Display Release Tag
        run: echo "${GITHUB_REF_NAME}"
```

A more complete release pipeline can build, test, package and publish the application.

---

# PART 19: GITOPS

# 40. What Is GitOps?

GitOps is an operational approach in which Git repositories act as the source of truth for the desired state of infrastructure and applications.

Instead of directly changing Kubernetes resources manually, you store the desired configuration in Git.

A GitOps controller continuously compares:

```text
Desired State in Git
          |
          v
     GitOps Controller
          |
          v
   Actual Cluster State
```

If the actual state differs from the desired state, the controller can report the difference and, depending on configuration, reconcile it.

### Traditional CI/CD

```text
GitHub Actions
      |
      v
Build Image
      |
      v
kubectl apply
      |
      v
Kubernetes Cluster
```

Here, the pipeline directly deploys resources to Kubernetes.

### GitOps

```text
Developer
    |
    v
Application Repository
    |
    v
CI Pipeline
    |
    v
Build Docker Image
    |
    v
Push Image to Registry
    |
    v
Update Deployment Repository
    |
    v
GitOps Controller
    |
    v
Kubernetes Cluster
```

The GitOps controller watches the configuration repository and reconciles the cluster.

### Benefits of GitOps

- Version-controlled deployment configuration.
- Auditable changes.
- Easier rollback through Git history.
- Separation between build and deployment.
- Automated reconciliation.
- Reduced need for CI pipelines to hold direct cluster deployment credentials.

---

# 41. What Is Argo CD?

Argo CD is a declarative GitOps continuous delivery tool for Kubernetes.

It continuously compares the desired application state stored in Git with the live state of the Kubernetes cluster.

Official documentation:

[Argo CD Documentation](https://argo-cd.readthedocs.io/en/stable/)

### Architecture

```text
                Git Repository
                      |
                      v
                  Argo CD
                      |
             +--------+--------+
             |                 |
             v                 v
        Desired State      Live State
             |                 |
             +--------+--------+
                      |
                      v
                 Reconciliation
                      |
                      v
                Kubernetes Cluster
```

## 41.1 Argo CD Application

An Argo CD Application defines:

- Source repository.
- Path containing manifests or chart configuration.
- Target cluster.
- Target namespace.
- Sync policy.

Example:

```yaml
apiVersion: argoproj.io/v1alpha1

kind: Application

metadata:
  name: backend-app

  namespace: argocd

spec:
  project: default

  source:
    repoURL: https://github.com/example/k8s-config.git

    targetRevision: main

    path: backend

  destination:
    server: https://kubernetes.default.svc

    namespace: production

  syncPolicy:
    automated:
      prune: false
      selfHeal: true
```

This is a simplified example.

The repository must contain valid Kubernetes manifests, and Argo CD must be installed and configured.

### Important Fields

| Field | Purpose |
|---|---|
| `repoURL` | Git repository |
| `targetRevision` | Branch, tag or commit |
| `path` | Directory containing application configuration |
| `destination` | Target cluster |
| `namespace` | Target namespace |
| `automated` | Enables automatic synchronization |
| `prune` | Allows deletion of resources removed from Git |
| `selfHeal` | Reconciles certain manual live-cluster changes |

### Important: Pruning

If you enable:

```yaml
prune: true
```

Argo CD can delete resources that are no longer defined in Git.

This is powerful but potentially destructive.

Use it only when you understand the consequences and have appropriate safeguards.

Argo CD's automated sync and self-healing behavior are described in its official documentation. [argo-cd.readthedocs.io](https://argo-cd.readthedocs.io/en/latest/user-guide/auto_sync/?utm_source=chatgpt.com)

---

# 42. CI/CD vs GitOps

| Feature | Traditional CI/CD | GitOps |
|---|---|---|
| Source of Truth | Often application repository and pipeline configuration | Git-based desired-state repository |
| Deployment | Pipeline pushes changes | Controller reconciles desired state |
| Cluster Access | Pipeline commonly needs deployment access | Controller operates inside or with access to the cluster |
| Drift Detection | Depends on tooling | Built into GitOps controller behavior |
| Rollback | Pipeline or deployment mechanism | Revert configuration commit or use supported controller features |
| Common Tool | GitHub Actions | Argo CD, Flux |

**Important:** GitOps does not replace CI.

A common architecture uses GitHub Actions for CI and Argo CD for CD.

---

# PART 20: DEPLOYMENT STRATEGIES

# 43. What Is a Deployment Strategy?

A Deployment Strategy describes how a new application version replaces or runs alongside an existing version.

The goal is to release changes while controlling:

- Downtime.
- Deployment risk.
- Traffic distribution.
- Rollback complexity.
- Resource usage.

Important strategies:

1. Recreate.
2. Rolling Update.
3. Blue-Green Deployment.
4. Canary Deployment.

---

# 44. Recreate Deployment

In a Recreate deployment, the existing application instances are terminated before new instances are created.

```text
Version 1
    |
    v
Stop All Old Pods
    |
    v
Start Version 2
    |
    v
Version 2 Running
```

### Advantages

- Simple.
- Useful when versions cannot run simultaneously.
- Avoids mixed-version operation.

### Disadvantages

- Causes downtime.
- Not suitable for applications requiring continuous availability.

Example Kubernetes configuration:

```yaml
spec:
  strategy:
    type: Recreate
```

---

# 45. Rolling Deployment

Rolling Update gradually replaces old Pods with new Pods.

Suppose you have four replicas.

```text
Initial:
V1 V1 V1 V1

Update:
V1 V1 V1 V2

Next:
V1 V1 V2 V2

Next:
V1 V2 V2 V2

Final:
V2 V2 V2 V2
```

### Advantages

- Gradual rollout.
- Usually avoids application downtime when configured correctly.
- Built into Kubernetes Deployments.
- Supports rollout monitoring and rollback.

### Disadvantages

- Old and new versions may run simultaneously.
- Requires backward-compatible application and database changes.
- Deployment capacity must accommodate the rollout.

## 45.1 Kubernetes Rolling Update Configuration

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: backend

spec:
  replicas: 4

  strategy:
    type: RollingUpdate

    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0

  selector:
    matchLabels:
      app: backend

  template:
    metadata:
      labels:
        app: backend

    spec:
      containers:
        - name: backend
          image: myusername/backend:2.0
```

### Explanation

`maxSurge: 1`

Allows one additional Pod above the desired replica count during the rollout.

`maxUnavailable: 0`

Prevents the Deployment controller from intentionally making any desired replica unavailable during the rollout.

The cluster still needs sufficient capacity, and readiness probes must be correctly configured.

Kubernetes uses `maxSurge` and `maxUnavailable` to control rolling updates. [Kubernetes](https://kubernetes.io/docs/tasks/run-application/update-deployment-rolling/?utm_source=chatgpt.com)

---

# 46. Blue-Green Deployment

Blue-Green Deployment maintains two application environments:

- Blue – Current production version.
- Green – New version.

Example:

```text
             Load Balancer
                   |
                   v
             Blue Environment
                 V1
```

You deploy the new version separately:

```text
Blue Environment: V1
Green Environment: V2
```

After testing Green, switch traffic.

```text
             Load Balancer
                   |
                   v
            Green Environment
                  V2
```

### Architecture

Before deployment:

```text
Users
  |
  v
Blue V1
```

During deployment:

```text
Users
  |
  v
Blue V1

Green V2
```

After traffic switching:

```text
Users
  |
  v
Green V2

Blue V1
(Available for rollback)
```

### Advantages

- Fast traffic switching.
- Easy application rollback.
- New version can be tested before receiving production traffic.

### Disadvantages

- Requires additional infrastructure capacity.
- Database compatibility is complicated.
- Traffic switching must be coordinated.

### Real-World Example

Suppose your current application runs on:

```text
backend-v1
```

You deploy:

```text
backend-v2
```

After validating version 2, you update the load balancer or Service routing to send traffic to version 2.

If problems occur, you can switch traffic back to version 1.

---

# 47. Canary Deployment

Canary Deployment gradually introduces a new version to a small percentage of users.

Example:

```text
Version 1: 95%
Version 2: 5%
```

Monitor:

- Error rate.
- Response time.
- CPU usage.
- Memory usage.
- Business metrics.

If the new version performs well, increase traffic.

```text
Stage 1:
V1 = 95%
V2 = 5%

Stage 2:
V1 = 75%
V2 = 25%

Stage 3:
V1 = 50%
V2 = 50%

Stage 4:
V1 = 0%
V2 = 100%
```

### Advantages

- Limits exposure to new-version problems.
- Allows production validation.
- Supports gradual traffic migration.

### Disadvantages

- Requires traffic-splitting capabilities.
- Monitoring must be reliable.
- More complex than a normal rolling update.

### Important

A simple Kubernetes Service selecting both stable and canary Pods does not guarantee exact percentage-based traffic splitting.

For controlled traffic weights, use a suitable Gateway API implementation, service mesh, ingress controller or cloud load balancer integration.

Kubernetes documentation describes canary deployments and rolling update strategies. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/management/?utm_source=chatgpt.com)

---

# 48. Deployment Strategy Comparison

| Feature | Recreate | Rolling | Blue-Green | Canary |
|---|---|---|---|---|
| Downtime | Usually yes | Can avoid it | Usually avoidable | Usually avoidable |
| New Version Exposure | Full replacement | Gradual Pod replacement | After traffic switch | Small percentage initially |
| Rollback | Redeploy old version | Rollback revision | Switch traffic back | Reduce or remove canary traffic |
| Infrastructure Cost | Lower | Moderate | Higher | Moderate to high |
| Complexity | Low | Moderate | High | High |
| Best Use | Simple applications | Most stateless applications | Critical releases | High-risk releases |

---

# PART 21: PIPELINE SECURITY

# 49. Why Is CI/CD Security Important?

A CI/CD pipeline often has access to:

- Source code.
- Repository tokens.
- Container registries.
- Cloud infrastructure.
- Production environments.
- Deployment credentials.

If an attacker compromises the pipeline, they may be able to compromise the application or infrastructure.

Therefore, CI/CD security is essential.

---

# 50. GitHub Actions Security Best Practices

## 50.1 Least-Privilege Permissions

Avoid granting broad permissions unnecessarily.

Example:

```yaml
permissions:
  contents: read
```

For an OIDC-based AWS job:

```yaml
permissions:
  contents: read
  id-token: write
```

Grant only the permissions required by each job.

## 50.2 Pin Third-Party Actions

Avoid blindly trusting third-party Actions.

For production, prefer a verified full commit SHA:

```yaml
- uses: actions/checkout@<VERIFIED_FULL_COMMIT_SHA>
```

The SHA must be a real, reviewed commit identifier for the Action version you intend to use.

## 50.3 Protect Production Secrets

Use GitHub Environments.

Separate:

```text
STAGING_SECRETS
PRODUCTION_SECRETS
```

Require approval for sensitive production deployments.

## 50.4 Avoid Exposing Secrets to Pull Requests

Be particularly careful with workflows triggered by pull requests from forks.

Do not provide untrusted code with access to production credentials.

## 50.5 Use OIDC

Prefer short-lived cloud credentials through OIDC rather than long-lived static cloud access keys.

## 50.6 Scan Dependencies

Use tools such as:

- Dependabot.
- npm audit.
- Snyk.
- Trivy.
- CodeQL.

These help identify different categories of vulnerabilities.

## 50.7 Scan Docker Images

Example:

```bash
trivy image myusername/backend:1.0
```

Image scanning can identify known vulnerabilities in operating system packages and application dependencies.

## 50.8 Protect Deployment Branches

Protect branches such as:

```text
main
production
release/*
```

Require appropriate reviews and successful status checks.

GitHub's security guidance recommends least-privilege permissions and pinning third-party Actions to full commit SHAs. [GitHub Docs](https://docs.github.com/en/code-security/tutorials/secure-your-organization/protect-against-threats?utm_source=chatgpt.com)

---

# PART 22: CI/CD MONITORING

# 51. Monitoring CI/CD Pipelines

A successful pipeline does not guarantee that a deployed application is healthy.

You need to monitor both pipeline execution and the application after deployment.

Important metrics:

| Metric | Purpose |
|---|---|
| Build Duration | Measures build performance |
| Test Failure Rate | Measures software quality |
| Deployment Frequency | Measures release frequency |
| Deployment Failure Rate | Measures deployment reliability |
| Lead Time for Changes | Measures delivery speed |
| Mean Time to Recovery | Measures recovery speed |
| Application Error Rate | Measures production reliability |
| Response Time | Measures user experience |

### Important DevOps Metrics: DORA

DORA metrics are commonly used to assess software delivery performance.

The four key metrics are:

1. Deployment Frequency.
2. Lead Time for Changes.
3. Change Failure Rate.
4. Failed Deployment Recovery Time.

These metrics help teams understand delivery speed and stability together.

### Example Monitoring Architecture

```text
              GitHub Actions
                    |
                    v
              Pipeline Logs
                    |
                    v
             Monitoring System
                    |
                    v
                 Dashboard
                    |
                    v
                 Alerts

              Application
                    |
                    v
             Metrics and Logs
                    |
                    v
              Prometheus
                    |
                    v
                 Grafana
```

---

# PART 23: COMPLETE FULL-STACK CI/CD PROJECT

# 52. Project – Next.js + Node.js + Docker + AWS + Kubernetes

Now let's combine the technologies you have learned.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + Express |
| Database | MySQL RDS |
| Cache | Redis |
| Containerization | Docker |
| Source Control | GitHub |
| CI/CD | GitHub Actions |
| Image Registry | Amazon ECR |
| Orchestration | Amazon EKS |
| Traffic Management | AWS Load Balancer |
| DNS | Route 53 |
| Monitoring | CloudWatch |
| GitOps | Argo CD |

---

# 53. Complete Architecture

```text
                         Developer
                             |
                             v
                       GitHub Repository
                             |
                             v
                       GitHub Actions
                             |
              +--------------+--------------+
              |                             |
              v                             v
          Frontend CI                   Backend CI
              |                             |
              v                             v
         Run Tests                     Run Tests
              |                             |
              v                             v
        Build Next.js                 Build Node.js
              |                             |
              v                             v
         Docker Build                 Docker Build
              |                             |
              v                             v
              +--------------+--------------+
                             |
                             v
                         Amazon ECR
                             |
                             v
                       Deployment Repo
                             |
                             v
                          Argo CD
                             |
                             v
                         Amazon EKS
                             |
              +--------------+--------------+
              |                             |
              v                             v
         Next.js Pods                  Node.js Pods
                                            |
                                 +----------+----------+
                                 |                     |
                                 v                     v
                              Redis                 MySQL RDS
```

---

# 54. Recommended Repository Structure

You can maintain the application and deployment configuration in separate repositories.

### Application Repository

```text
fullstack-app/
    frontend/
        app/
        public/
        package.json
        Dockerfile

    backend/
        src/
        tests/
        package.json
        Dockerfile

    .github/
        workflows/
            frontend-ci.yml
            backend-ci.yml
            docker-build.yml

    README.md
```

### Deployment Repository

```text
deployment-config/
    environments/
        development/
            frontend/
            backend/

        staging/
            frontend/
            backend/

        production/
            frontend/
            backend/

    argocd/
        frontend-app.yaml
        backend-app.yaml
```

This separation allows application developers to build and test software while deployment configuration is managed independently.

---

# 55. Complete Pipeline Workflow

A recommended production-oriented process:

### Step 1 – Developer Pushes Code

```bash
git add .
git commit -m "Add payment API"
git push origin main
```

### Step 2 – GitHub Actions Starts

The workflow detects the push.

### Step 3 – Install Dependencies

```bash
npm ci
```

### Step 4 – Run Linting

```bash
npm run lint
```

### Step 5 – Run Tests

```bash
npm test
```

### Step 6 – Build Application

```bash
npm run build
```

### Step 7 – Build Docker Image

```bash
docker build -t backend:1.0 .
```

### Step 8 – Push Image to ECR

```text
Amazon ECR
```

### Step 9 – Update Deployment Configuration

Update the image tag in the deployment repository.

Example:

```yaml
image:
  repository: 123456789012.dkr.ecr.ap-south-1.amazonaws.com/backend
  tag: "COMMIT_SHA"
```

This example is a values-file structure for a Helm chart; the exact format depends on your chart.

### Step 10 – Argo CD Detects the Change

Argo CD compares the Git repository with the live Kubernetes cluster.

### Step 11 – Deploy to EKS

Argo CD synchronizes the desired configuration.

### Step 12 – Verify Application

Check:

```bash
kubectl get pods -n production
```

Check rollout:

```bash
kubectl rollout status deployment/backend -n production
```

Check logs:

```bash
kubectl logs deployment/backend -n production
```

### Step 13 – Monitor

Monitor:

- Application errors.
- CPU usage.
- Memory usage.
- Response time.
- Deployment health.

---

# PART 24: COMMON CI/CD MISTAKES

# 56. Mistakes to Avoid

| Mistake | Problem | Better Approach |
|---|---|---|
| No automated tests | Bugs reach deployment stages | Add unit and integration tests |
| Using `npm install` blindly in CI | Dependency resolution may vary | Prefer `npm ci` with a committed lockfile |
| Hardcoding secrets | Credentials leak through Git | Use GitHub Secrets or OIDC |
| Giving excessive permissions | Larger security impact | Use least privilege |
| Using `latest` image tags | Difficult version tracking | Use immutable tags or digests |
| Deploying every branch to production | Uncontrolled releases | Protect production deployment triggers |
| No rollback strategy | Difficult incident recovery | Maintain release history |
| No deployment verification | Failed deployment may go unnoticed | Add health checks |
| Rebuilding images during deployment | Inconsistent artifacts | Build once and promote the same image |
| No dependency caching | Slower pipelines | Configure appropriate caching |
| Sharing production secrets with PRs | Credential exposure | Restrict secrets and use protected environments |
| Running untrusted code on privileged self-hosted runners | Infrastructure compromise | Isolate and restrict runners |
| No monitoring | Problems remain undetected | Implement observability |
| Direct manual cluster changes | GitOps drift | Manage desired state through Git |

---

# PART 25: INTERVIEW QUESTIONS AND ANSWERS

# 57. Basic Interview Questions

### Q1. What is CI/CD?

CI/CD is a software delivery practice that automates code integration, testing, building and application deployment.

### Q2. What is Continuous Integration?

Continuous Integration is the practice of frequently integrating code changes and automatically validating them.

### Q3. What is Continuous Delivery?

Continuous Delivery ensures that validated application changes are ready for release, often with a manual production approval step.

### Q4. What is Continuous Deployment?

Continuous Deployment automatically releases successful changes to production.

### Q5. What is GitHub Actions?

GitHub Actions is GitHub's automation platform used to build workflows for testing, building and deploying applications.

### Q6. What is a Workflow?

A Workflow is an automated process defined in a YAML file.

### Q7. What is a Runner?

A Runner is the machine that executes GitHub Actions jobs.

### Q8. What is a Job?

A Job is a collection of steps executed on a runner.

### Q9. What is a Step?

A Step is an individual operation within a Job.

### Q10. What is an Action?

An Action is a reusable unit of automation.

---

# 58. Intermediate Interview Questions

### Q11. What is the difference between a Job and a Step?

A Job contains multiple steps.

A Step is an individual operation executed inside a job.

### Q12. What is the difference between GitHub-hosted and self-hosted runners?

GitHub-hosted runners are managed by GitHub.

Self-hosted runners are managed by the organization or developer.

### Q13. What is a GitHub Actions Secret?

A Secret is a protected configuration value used to provide sensitive information to workflows.

### Q14. What is a workflow artifact?

An artifact is a file or collection of files generated during a workflow run and stored for later use.

### Q15. What is dependency caching?

Dependency caching stores reusable files to reduce repeated dependency installation work.

### Q16. What is a matrix build?

A matrix build executes a job across multiple configuration combinations.

### Q17. What is `needs` in GitHub Actions?

`needs` defines dependencies between jobs.

### Q18. What is the purpose of `workflow_dispatch`?

It allows a workflow to be manually triggered.

### Q19. What is the difference between `npm install` and `npm ci`?

`npm install` installs dependencies and may update the lockfile.

`npm ci` installs dependencies from the lockfile in a clean and reproducible manner.

### Q20. What is Docker image publishing?

It is the process of pushing a built container image to a registry such as Docker Hub or Amazon ECR.

---

# 59. Advanced Interview Questions

### Q21. How would you design a CI/CD pipeline for a Node.js application?

I would configure:

1. Source checkout.
2. Node.js setup.
3. Dependency installation.
4. Linting.
5. Unit tests.
6. Integration tests.
7. Application build.
8. Docker image build.
9. Image security scanning.
10. Image publishing.
11. Deployment.
12. Health verification.

### Q22. How would you deploy an application to AWS EKS using GitHub Actions?

I would:

1. Build and test the application.
2. Build the Docker image.
3. Authenticate to AWS using OIDC.
4. Push the image to ECR.
5. Update the Kubernetes deployment configuration.
6. Deploy through GitOps or a suitably restricted Kubernetes deployment identity.
7. Verify the rollout.

### Q23. Why is OIDC preferred over long-lived AWS access keys?

OIDC provides a way to obtain short-lived credentials without storing long-lived AWS access keys in GitHub Secrets.

### Q24. What is GitOps?

GitOps is an approach where Git stores the desired application and infrastructure configuration, and a controller reconciles the actual environment with that desired state.

### Q25. What is Argo CD?

Argo CD is a declarative GitOps continuous delivery tool for Kubernetes.

### Q26. What is the difference between CI/CD and GitOps?

CI/CD describes the automated software integration and delivery process.

GitOps is an approach to managing deployment and infrastructure state through Git as the source of truth.

They are commonly used together.

### Q27. What is a blue-green deployment?

It uses two environments: one running the current version and another running the new version. Traffic is switched after the new version is validated.

### Q28. What is a canary deployment?

It introduces a new application version to a small percentage of traffic before gradually increasing exposure.

### Q29. What is a rolling deployment?

It gradually replaces old application instances with new ones.

### Q30. What is the purpose of a GitHub Environment?

It provides deployment-specific configuration, secrets, branch restrictions and approval protection rules.

---

# 60. Scenario-Based Interview Questions

### Q31. Your CI pipeline passes, but deployment fails. How would you troubleshoot?

I would:

1. Inspect the deployment job logs.
2. Verify credentials and permissions.
3. Check the target environment.
4. Verify image availability.
5. Check infrastructure connectivity.
6. Inspect Kubernetes events if deploying to EKS.
7. Review application and deployment logs.

### Q32. How would you prevent untested code from reaching production?

I would configure:

- Required Pull Request reviews.
- Branch protection.
- Automated CI checks.
- Required status checks.
- Protected production environments.
- Deployment approval rules.

### Q33. How would you implement zero-downtime deployment?

I would use an appropriate deployment strategy such as rolling update or blue-green deployment.

I would also configure readiness probes, sufficient capacity, health checks and backward-compatible database migrations.

### Q34. How would you secure a GitHub Actions pipeline?

I would:

- Use least-privilege permissions.
- Use OIDC for cloud authentication.
- Pin third-party Actions to verified SHAs.
- Protect deployment environments.
- Restrict secrets.
- Scan dependencies and container images.
- Isolate self-hosted runners.

### Q35. A new release introduces a production bug. What would you do?

I would:

1. Assess the impact.
2. Stop further rollout if possible.
3. Roll back or shift traffic to the previous stable release.
4. Verify application health.
5. Investigate logs and metrics.
6. Fix the underlying problem.
7. Add regression tests.
8. Release again through the normal pipeline.

---

# PART 26: PRACTICAL ASSIGNMENTS

# 61. Assignment 1 – Create Your First GitHub Actions Workflow

Create:

```text
.github/workflows/hello.yml
```

Requirements:

- Trigger on push.
- Use Ubuntu runner.
- Print a message.
- List project files.

**Expected outcome:** Understand workflow execution.

---

# 62. Assignment 2 – Node.js CI Pipeline

Create a Node.js application.

Configure:

- Node.js setup.
- `npm ci`.
- ESLint.
- Jest or another test framework.
- Application build.

**Expected outcome:** Automate Node.js testing.

---

# 63. Assignment 3 – Next.js CI Pipeline

Create a Next.js application.

Configure:

- Checkout.
- Node.js setup.
- Dependency caching.
- Linting.
- Production build.

**Expected outcome:** Automate Next.js validation.

---

# 64. Assignment 4 – Multi-Job Pipeline

Create three jobs:

```text
Lint
Test
Build
```

Run them in parallel.

Then create a fourth job:

```text
Deploy
```

Make it depend on all three.

**Expected outcome:** Understand parallel jobs and dependencies.

---

# 65. Assignment 5 – GitHub Secrets

Create GitHub Secrets for:

```text
DOCKER_USERNAME
DOCKER_PASSWORD
```

Use them in a workflow.

Verify that credentials are not hardcoded in YAML.

**Expected outcome:** Understand secure configuration.

---

# 66. Assignment 6 – Artifact Management

Create a workflow that:

1. Builds a Node.js application.
2. Creates a `dist` directory.
3. Uploads it as an artifact.
4. Downloads it in a separate job.

**Expected outcome:** Understand artifact transfer.

---

# 67. Assignment 7 – Matrix Build

Test a Node.js application against multiple Node.js versions.

Example:

```text
Node 20
Node 22
Node 24
```

**Expected outcome:** Understand matrix execution.

---

# 68. Assignment 8 – Docker Automation

Create a Docker image using GitHub Actions.

Push it to Docker Hub.

Use:

- Docker Buildx.
- Docker Login Action.
- Docker Build and Push Action.

**Expected outcome:** Automate container image publishing.

---

# 69. Assignment 9 – AWS ECR Automation

Configure:

- AWS IAM OIDC provider.
- IAM role.
- ECR repository.
- GitHub Actions workflow.

Build and push a Docker image to ECR.

**Expected outcome:** Understand secure AWS authentication and image publishing.

---

# 70. Assignment 10 – Automated VPS Deployment

Create a workflow that:

1. Runs tests.
2. Connects to a VPS through SSH.
3. Deploys the application.
4. Restarts PM2.
5. Verifies the application.

**Expected outcome:** Automate traditional server deployments.

---

# 71. Assignment 11 – Kubernetes Deployment

Create a workflow that:

1. Builds a Docker image.
2. Pushes it to ECR.
3. Updates an EKS Deployment.
4. Checks rollout status.

**Expected outcome:** Automate Kubernetes deployment.

---

# 72. Assignment 12 – GitOps with Argo CD

Install Argo CD in a Kubernetes cluster.

Create a deployment repository.

Configure an Argo CD Application.

Change the desired application version in Git and observe synchronization.

**Expected outcome:** Understand GitOps-based deployment.

---

# 73. Assignment 13 – Complete CI/CD Project

Build a full-stack application with:

```text
Next.js
Node.js
Express
MySQL
Redis
Docker
GitHub Actions
AWS ECR
AWS EKS
Argo CD
```

Requirements:

- Automated tests.
- Automated build.
- Docker image publishing.
- Image versioning.
- GitOps deployment.
- Staging environment.
- Production approval.
- Rolling or canary deployment.
- Monitoring.
- Rollback strategy.

**Expected outcome:** Build an end-to-end automated software delivery system.

---

# PART 27: QUICK REVISION

# 74. CI/CD Concepts at a Glance

| Concept | Meaning |
|---|---|
| CI | Continuous Integration |
| CD | Continuous Delivery or Deployment |
| Pipeline | Automated sequence of operations |
| Workflow | Automated GitHub Actions process |
| Event | Workflow trigger |
| Job | Collection of steps |
| Step | Individual operation |
| Action | Reusable automation unit |
| Runner | Machine executing a job |
| Secret | Protected sensitive value |
| Variable | Non-sensitive configuration |
| Artifact | Workflow output |
| Cache | Reusable dependency data |
| Matrix | Multiple job configurations |
| Environment | Deployment target |
| OIDC | Short-lived identity federation |
| GitOps | Git-based desired-state management |
| Argo CD | Kubernetes GitOps tool |
| Rolling Update | Gradual replacement |
| Blue-Green | Two environments with traffic switching |
| Canary | Gradual traffic exposure |
| ECR | AWS container image registry |
| EKS | AWS managed Kubernetes service |

---

# 75. Important GitHub Actions YAML Reference

| Keyword | Purpose |
|---|---|
| `name` | Workflow name |
| `on` | Trigger |
| `push` | Push event |
| `pull_request` | Pull Request event |
| `workflow_dispatch` | Manual trigger |
| `schedule` | Scheduled execution |
| `jobs` | Define jobs |
| `runs-on` | Select runner |
| `steps` | Define steps |
| `uses` | Execute an Action |
| `run` | Execute shell command |
| `with` | Provide Action inputs |
| `env` | Define environment variables |
| `secrets` | Access secrets |
| `vars` | Access variables |
| `needs` | Job dependencies |
| `if` | Conditional execution |
| `strategy` | Job execution strategy |
| `matrix` | Multiple configurations |
| `permissions` | Token permissions |
| `environment` | Deployment environment |
| `concurrency` | Control concurrent workflow runs |

---

# 76. Important Commands

### Git

```bash
git add .
git commit -m "Update application"
git push origin main
```

### Node.js

```bash
npm ci
npm run lint
npm test
npm run build
```

### Docker

```bash
docker build -t my-app:1.0 .
```

```bash
docker login
```

```bash
docker push myusername/my-app:1.0
```

### Kubernetes

```bash
kubectl get pods
```

```bash
kubectl get deployments
```

```bash
kubectl rollout status deployment/backend
```

```bash
kubectl rollout undo deployment/backend
```

### AWS

```bash
aws sts get-caller-identity
```

```bash
aws ecr describe-repositories --region ap-south-1
```

```bash
aws eks update-kubeconfig \
  --region ap-south-1 \
  --name production-cluster
```

---

# 77. Phase 47 Completion Checklist

### CI/CD Fundamentals

- [ ] CI.
- [ ] Continuous Delivery.
- [ ] Continuous Deployment.
- [ ] DevOps Lifecycle.
- [ ] CI/CD Architecture.
- [ ] Pipeline Stages.

### GitHub Actions

- [ ] Workflows.
- [ ] Events.
- [ ] Jobs.
- [ ] Steps.
- [ ] Actions.
- [ ] Runners.
- [ ] Workflow YAML.
- [ ] Push Trigger.
- [ ] Pull Request Trigger.
- [ ] Manual Trigger.
- [ ] Scheduled Trigger.

### Workflow Configuration

- [ ] Environment Variables.
- [ ] GitHub Secrets.
- [ ] GitHub Variables.
- [ ] Job Dependencies.
- [ ] Parallel Execution.
- [ ] Matrix Builds.
- [ ] Conditional Execution.
- [ ] Workflow Artifacts.
- [ ] Dependency Caching.

### Automated Testing and Building

- [ ] Node.js CI.
- [ ] Next.js CI.
- [ ] Linting.
- [ ] Unit Testing.
- [ ] Application Build.
- [ ] Docker Build.

### Deployment Automation

- [ ] Docker Hub.
- [ ] Amazon ECR.
- [ ] AWS OIDC.
- [ ] VPS Deployment.
- [ ] EC2 Deployment.
- [ ] Kubernetes Deployment.
- [ ] GitHub Environments.
- [ ] Production Approval.

### Advanced DevOps

- [ ] GitOps.
- [ ] Argo CD.
- [ ] Rolling Deployment.
- [ ] Blue-Green Deployment.
- [ ] Canary Deployment.
- [ ] Pipeline Security.
- [ ] Monitoring.
- [ ] Rollback.

---

# 78. Official Documentation and Learning Resources

| Topic | Official Documentation |
|---|---|
| GitHub Actions | [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com) |
| Workflow Syntax | [Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com) |
| Workflow Events | [Workflow Events](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows?utm_source=chatgpt.com) |
| GitHub Runners | [GitHub Runners](https://docs.github.com/en/actions/concepts/runners?utm_source=chatgpt.com) |
| GitHub Secrets | [GitHub Secrets](https://docs.github.com/en/actions/concepts/security/secrets?utm_source=chatgpt.com) |
| OIDC | [GitHub OIDC](https://docs.github.com/en/actions/concepts/security/openid-connect?utm_source=chatgpt.com) |
| Deployment Environments | [GitHub Environments](https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments?utm_source=chatgpt.com) |
| Docker Build Actions | [Docker Build Actions](https://docs.docker.com/build/ci/github-actions/?utm_source=chatgpt.com) |
| Amazon ECR | [Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com) |
| AWS EKS | [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com) |
| Argo CD | [Argo CD Documentation](https://argo-cd.readthedocs.io/en/stable/?utm_source=chatgpt.com) |
| Kubernetes Deployments | [Kubernetes Deployment Strategies](https://kubernetes.io/docs/concepts/workloads/management/?utm_source=chatgpt.com) |
| Prometheus | [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/?utm_source=chatgpt.com) |
| Grafana | [Grafana Documentation](https://grafana.com/docs/?utm_source=chatgpt.com) |

---

# 79. Final Summary

In Phase 47, you learned how to automate the software delivery lifecycle.

The central concept is:

**Write code once, validate it automatically, build a consistent release artifact, and deploy that artifact through a controlled process.**

### Complete CI/CD Workflow

```text
                    Developer
                        |
                        v
                   Write Code
                        |
                        v
                   Git Commit
                        |
                        v
                   GitHub Push
                        |
                        v
                 GitHub Actions
                        |
              +---------+---------+
              |                   |
              v                   v
          Run Lint             Run Tests
              |                   |
              +---------+---------+
                        |
                        v
                  Build Application
                        |
                        v
                  Build Docker Image
                        |
                        v
                 Security Scan Image
                        |
                        v
                 Push Image to ECR
                        |
                        v
                  Update Git Config
                        |
                        v
                     Argo CD
                        |
                        v
                    AWS EKS
                        |
                        v
                  Running Application
                        |
                        v
                     Monitoring
```

### Most Important Topics to Master

Prioritize these:

1. CI/CD fundamentals.
2. GitHub Actions architecture.
3. Workflow YAML.
4. Node.js and Next.js CI pipelines.
5. Jobs, Steps and Runners.
6. GitHub Secrets and Variables.
7. Docker Build and Push.
8. AWS ECR integration.
9. AWS OIDC authentication.
10. Automated VPS deployment.
11. Kubernetes deployment automation.
12. GitOps and Argo CD.
13. Rolling, Blue-Green and Canary deployments.
14. CI/CD security.
15. Production pipeline architecture.

---

# Next Phase Preview

## Phase 48 – DevSecOps, Application Security and Infrastructure Security

The next phase will cover:

1. What Is DevSecOps?
2. DevOps vs DevSecOps.
3. Security in the Software Development Lifecycle.
4. Security by Design.
5. OWASP Top 10.
6. SQL Injection.
7. Cross-Site Scripting (XSS).
8. Cross-Site Request Forgery (CSRF).
9. Server-Side Request Forgery (SSRF).
10. Broken Access Control.
11. Authentication and Authorization Security.
12. JWT Security.
13. Password Hashing.
14. HTTPS and TLS.
15. Secure HTTP Headers.
16. CORS Security.
17. API Security.
18. Rate Limiting.
19. Secrets Management.
20. Dependency Vulnerability Scanning.
21. SAST and DAST.
22. Software Composition Analysis (SCA).
23. Container Security.
24. Docker Image Scanning.
25. Kubernetes Security.
26. AWS IAM Security.
27. Infrastructure Security.
28. CI/CD Pipeline Security.
29. Security Monitoring.
30. Incident Response.
31. Vulnerability Management.
32. Complete DevSecOps Pipeline Project.

**Phase 48 will focus on integrating security into the entire software development and deployment lifecycle.**