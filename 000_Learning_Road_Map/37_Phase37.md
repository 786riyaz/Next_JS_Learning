# Phase 37 – Advanced DevOps, Docker, Kubernetes and Container Orchestration

**Level:** Advanced  
**Domain:** DevOps, Cloud Infrastructure and Deployment Engineering  
**Technology:** Docker · Docker Compose · Kubernetes · Helm · GitHub Actions · Next.js 16.x · Node.js · TypeScript

---

# 1. Learning Objectives

In the previous phases, you learned about backend scalability, distributed systems, monitoring, logging, and production operations.

Now we will study how to package, deploy, manage, and scale applications using containers and container orchestration.

Imagine you have developed an e-commerce application using:

- Next.js 16 for the frontend.
- Node.js and Express for the backend.
- MongoDB for persistent data.
- Redis for caching.
- BullMQ for background jobs.

You want to deploy this application to production.

You need to answer questions such as:

- How do you package the application?
- How do you make it run consistently on different machines?
- How do you deploy multiple application instances?
- How do you restart failed containers?
- How do you distribute traffic?
- How do you scale instances automatically?
- How do you manage environment variables and secrets?
- How do you deploy new versions without interrupting users?

Docker and Kubernetes help solve these problems.

By the end of this phase, you should understand how to:

1. Understand DevOps and containerization.
2. Build and manage Docker images and containers.
3. Write production-ready Dockerfiles.
4. Use Docker Compose for multi-container applications.
5. Configure Docker networking and volumes.
6. Secure and optimize containers.
7. Understand Kubernetes architecture.
8. Deploy applications using Pods, Deployments, and ReplicaSets.
9. Expose applications using Kubernetes Services and Ingress.
10. Manage ConfigMaps, Secrets, and persistent storage.
11. Configure automatic scaling and health probes.
12. Package Kubernetes applications using Helm.
13. Implement CI/CD pipelines for containerized applications.

# 2. Complete Topic Roadmap

| No. | Topic |
|---:|---|
| 1 | DevOps Fundamentals and Culture |
| 2 | Docker Architecture |
| 3 | Docker Images and Containers |
| 4 | Dockerfile and Multi-Stage Builds |
| 5 | Docker Compose |
| 6 | Docker Networking and Volumes |
| 7 | Container Security and Optimization |
| 8 | Kubernetes Architecture |
| 9 | Pods, Deployments and ReplicaSets |
| 10 | Services and Ingress |
| 11 | ConfigMaps and Secrets |
| 12 | Persistent Volumes and Storage |
| 13 | Horizontal Pod Autoscaling |
| 14 | Kubernetes Health Probes |
| 15 | Helm Charts |
| 16 | Container Orchestration |
| 17 | CI/CD with Docker and Kubernetes |
| 18 | Production Deployment Architecture |

---

# 3. DevOps Fundamentals and Culture

## 3.1 What is DevOps?

**DevOps** is a combination of development and operations practices that helps teams build, test, deploy, and maintain software more efficiently.

Traditionally, software development and infrastructure operations were handled by separate teams.

### Traditional software development workflow

```text
Development Team
       |
       | Develop application
       ↓
   Source Code
       |
       ↓
   Operations Team
       |
       | Configure server
       | Install dependencies
       | Deploy application
       |
       ↓
   Production
```

Problems with this approach:

- Developers and operations teams may have different priorities.
- Deployment processes may be manual.
- Environment configurations may differ.
- Bugs may appear only after deployment.
- Releases may take a long time.

### DevOps workflow

```text
Plan
  |
  ↓
Develop
  |
  ↓
Build
  |
  ↓
Test
  |
  ↓
Release
  |
  ↓
Deploy
  |
  ↓
Operate
  |
  ↓
Monitor
  |
  └──────── Feedback ────────→ Develop
```

DevOps encourages collaboration, automation, continuous feedback, and shared responsibility for software delivery.

## 3.2 Important DevOps concepts

### 1. Continuous Integration (CI)

Continuous Integration means frequently integrating code changes into a shared repository and automatically validating those changes.

For example:

A developer pushes code to GitHub.

An automated pipeline:

1. Installs dependencies.
2. Runs linting.
3. Runs tests.
4. Builds the application.
5. Reports whether the changes passed.

### 2. Continuous Delivery (CD)

Continuous Delivery means keeping software in a deployable state through automated build, testing, and release preparation.

Production deployment may still require manual approval.

### 3. Continuous Deployment

Continuous Deployment means successfully validated changes are automatically deployed to production without requiring a manual release approval.

### CI vs Continuous Delivery vs Continuous Deployment

| Concept | Meaning |
|---|---|
| CI | Automatically integrate and validate code changes |
| Continuous Delivery | Keep changes ready for release |
| Continuous Deployment | Automatically deploy successful changes to production |

## 3.3 Infrastructure as Code (IaC)

Infrastructure as Code means defining and managing infrastructure through machine-readable configuration rather than relying entirely on manual setup.

For example, instead of manually creating a Kubernetes Deployment through a dashboard, you define it in YAML:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ecommerce-api

spec:
  replicas: 3

  selector:
    matchLabels:
      app: ecommerce-api

  template:
    metadata:
      labels:
        app: ecommerce-api

    spec:
      containers:
        - name: api
          image: myregistry/ecommerce-api:v1
          ports:
            - containerPort: 4000
```

You can version-control this configuration and apply it repeatedly.

Other IaC tools include:

- Terraform.
- OpenTofu.
- AWS CloudFormation.
- Pulumi.
- Ansible (primarily configuration management and automation).

## 3.4 DevOps vs DevSecOps

DevSecOps integrates security practices throughout the software delivery lifecycle.

For example:

| DevOps activity | DevSecOps addition |
|---|---|
| Build Docker image | Scan image for vulnerabilities |
| Install dependencies | Scan dependencies |
| Deploy application | Apply least-privilege access |
| Store configuration | Secure secret management |
| Monitor application | Detect security incidents |

## 3.5 DevOps tools ecosystem

| Category | Common tools |
|---|---|
| Source control | Git, GitHub, GitLab |
| CI/CD | GitHub Actions, Jenkins, GitLab CI |
| Containers | Docker, Podman |
| Orchestration | Kubernetes |
| Infrastructure as Code | Terraform, OpenTofu |
| Configuration management | Ansible |
| Monitoring | Prometheus, Grafana |
| Logging | Loki, ELK |
| Cloud | AWS, Azure, Google Cloud |
| Package management | Helm |

**Important:** DevOps is not simply a collection of tools. It is an engineering approach involving automation, collaboration, reliability, and continuous improvement.

---

# 4. Docker Architecture

## 4.1 What is Docker?

**Docker** is a platform used to build, package, distribute, and run applications in containers.

A container packages an application with the dependencies and configuration needed to run it.

Imagine your Node.js application requires:

- Node.js runtime.
- npm packages.
- Application source code.
- Environment configuration.
- System libraries.

Without containerization, you might install and configure these dependencies separately on every server.

With Docker, you define the environment once and build an image from it.

That image can then be executed as a container on compatible systems.

## 4.2 Why do we need Docker?

Suppose your application works on your laptop but fails on your production server.

Possible reasons:

- Different Node.js versions.
- Different operating systems.
- Missing dependencies.
- Different system libraries.
- Incorrect environment configuration.

Docker helps standardize the runtime environment.

Example:

```text
Developer Laptop
       |
       ↓
   Docker Image
       |
       ├── Development Server
       |
       ├── Testing Environment
       |
       └── Production Server
```

The same application image can be promoted through different environments, while environment-specific configuration is supplied separately.

## 4.3 Docker architecture

Docker uses a client-server architecture.

The major components are:

1. Docker Client.
2. Docker Daemon.
3. Docker Images.
4. Docker Containers.
5. Docker Registry.

Architecture:

```text
             Docker Client
                  |
                  | Docker API
                  ↓
             Docker Daemon
              /    |    \
             /     |     \
            ↓      ↓      ↓
         Images Containers Networks
            |
            ↓
       Docker Registry
       Docker Hub / Private Registry
```

### A. Docker Client

The Docker Client is the command-line interface used to communicate with Docker.

Example:

```bash
docker build -t my-app .
docker run my-app
docker ps
docker stop my-app
```

The Docker CLI sends requests to the Docker daemon.

### B. Docker Daemon

The Docker daemon (`dockerd`) manages Docker objects and operations such as:

- Building images.
- Creating containers.
- Managing networks.
- Managing volumes.
- Communicating with container runtimes.

### C. Docker Registry

A Docker Registry stores and distributes Docker images.

Examples:

- Docker Hub
- Amazon Elastic Container Registry
- GitHub Container Registry
- Azure Container Registry
- Google Artifact Registry

Official website: [Docker Hub](https://hub.docker.com/?utm_source=chatgpt.com)

### D. Container Runtime

A container runtime is responsible for executing containers.

Docker Engine commonly uses containerd and lower-level OCI-compatible runtime components.

You do not need to manually manage these components for normal Docker usage.

## 4.4 Docker Engine vs Docker Desktop

| Docker Engine | Docker Desktop |
|---|---|
| Core container platform | Desktop application |
| Commonly installed on Linux servers | Commonly used on Windows and macOS |
| Provides daemon and APIs | Provides GUI and development integration |
| Runs containers | Helps develop and manage containers |

On Windows, Docker Desktop commonly uses WSL 2 or Hyper-V-based infrastructure depending on configuration.

## 4.5 Install Docker

For Windows development:

1. Install WSL 2 if required.
2. Install Docker Desktop.
3. Start Docker Desktop.
4. Open PowerShell or a terminal.
5. Verify the installation.

Commands:

```bash
docker --version
```

Check Docker information:

```bash
docker info
```

Run a test container:

```bash
docker run hello-world
```

The command downloads the image if needed, creates a container, and runs it.

Official documentation: [Docker installation documentation](https://docs.docker.com/get-started/get-docker/?utm_source=chatgpt.com)

---

# 5. Docker Images and Containers

## 5.1 What is a Docker image?

A Docker image is an immutable template used to create containers.

It contains the filesystem and metadata required to run an application.

Example:

```text
Node.js Image
     |
     ├── Node.js Runtime
     ├── npm
     ├── System Libraries
     └── Base Filesystem
```

You can create multiple containers from the same image.

## 5.2 What is a Docker container?

A container is a running or stopped instance created from an image.

Example:

```text
             Docker Image
                  |
          ┌───────┼───────┐
          ↓       ↓       ↓
      Container  Container Container
         1          2        3
```

Each container has its own isolated process and filesystem view, subject to the host's container runtime and kernel configuration.

## 5.3 Image vs Container

| Docker Image | Docker Container |
|---|---|
| Template | Instance created from image |
| Immutable by design | Has a writable container layer |
| Used to create containers | Executes the application |
| Can be stored in registries | Runs on a Docker host |
| Example: `node:22` | Running Node.js application |

## 5.4 What is a Dockerfile?

A Dockerfile is a text file containing instructions used to build a Docker image.

Example:

```dockerfile
FROM node:22-bookworm-slim

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

Explanation:

| Instruction | Purpose |
|---|---|
| `FROM` | Defines the base image |
| `WORKDIR` | Sets working directory |
| `COPY` | Copies files into the image |
| `RUN` | Executes commands during image build |
| `EXPOSE` | Documents the application port |
| `CMD` | Defines the default command when the container starts |

These instructions are described in the official Dockerfile reference. [Docker Documentation](https://docs.docker.com/reference/dockerfile?utm_source=chatgpt.com)

## 5.5 Build a Docker image

Suppose your project contains:

```text
my-node-app/
│
├── app.js
├── package.json
├── package-lock.json
└── Dockerfile
```

Build the image:

```bash
docker build -t my-node-app:v1 .
```

Explanation:

- `docker build`: Builds an image.
- `-t`: Assigns a name and tag.
- `my-node-app:v1`: Image name and version.
- `.`: Build context.

## 5.6 Run a container

```bash
docker run -d \
  --name my-node-container \
  -p 3000:3000 \
  my-node-app:v1
```

Explanation:

| Option | Meaning |
|---|---|
| `-d` | Run in detached mode |
| `--name` | Assign container name |
| `-p` | Publish host port to container port |
| Image name | Image used to create the container |

Port mapping:

```text
Host Machine
localhost:3000
      |
      ↓
Container Port 3000
      |
      ↓
Node.js Application
```

## 5.7 Important Docker commands

### Images

```bash
docker images
```

List images.

```bash
docker pull node:22-bookworm-slim
```

Download an image.

```bash
docker rmi my-node-app:v1
```

Remove an image.

### Containers

```bash
docker ps
```

Show running containers.

```bash
docker ps -a
```

Show all containers.

```bash
docker stop my-node-container
```

Stop a container.

```bash
docker start my-node-container
```

Start a stopped container.

```bash
docker restart my-node-container
```

Restart a container.

```bash
docker rm my-node-container
```

Remove a stopped container.

### Logs and inspection

```bash
docker logs my-node-container
```

View container logs.

```bash
docker logs -f my-node-container
```

Follow logs.

```bash
docker inspect my-node-container
```

Inspect container configuration.

```bash
docker stats
```

View resource usage.

```bash
docker exec -it my-node-container sh
```

Open a shell inside the container if the image includes one.

### 5.8 Docker image layers

Docker images are built from layers.

For example:

```dockerfile
FROM node:22-bookworm-slim

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

CMD ["npm", "start"]
```

Conceptually:

```text
Application Image
       |
       ├── Base Image Layer
       |
       ├── Working Directory Layer
       |
       ├── Dependency Installation Layer
       |
       ├── Application Files Layer
       |
       └── Image Configuration
```

Docker can reuse unchanged build layers, which makes repeated builds faster.

**Important:** A change in a Dockerfile instruction or its inputs may invalidate that layer and subsequent dependent layers.

This is why dependency manifests are commonly copied before application source files.

---

# 6. Dockerfile and Multi-Stage Builds

## 6.1 What is a Dockerfile?

A Dockerfile defines how an image is built.

It describes:

- Base operating system or runtime.
- Dependencies.
- Application files.
- Build commands.
- Runtime command.
- User and permissions.
- Exposed ports.

A well-designed Dockerfile makes the image reproducible, efficient, and easier to maintain.

## 6.2 Important Dockerfile instructions

### FROM

Selects the base image.

```dockerfile
FROM node:22-bookworm-slim
```

### WORKDIR

Defines the working directory.

```dockerfile
WORKDIR /app
```

### COPY

Copies files from the build context.

```dockerfile
COPY package.json package-lock.json ./
```

### RUN

Executes commands during the build.

```dockerfile
RUN npm ci
```

### ENV

Sets environment variables in the image.

```dockerfile
ENV NODE_ENV=production
```

Do not use `ENV` to embed secrets in an image.

### ARG

Defines build-time arguments.

```dockerfile
ARG APP_VERSION
```

Build:

```bash
docker build \
  --build-arg APP_VERSION=1.0.0 \
  -t my-app:1.0.0 .
```

Build arguments are not a safe secret-storage mechanism. Use BuildKit secret mounts for build-time credentials.

### EXPOSE

Documents the port used by the application.

```dockerfile
EXPOSE 3000
```

It does not publish the port to the host automatically.

### CMD

Defines the default command.

```dockerfile
CMD ["node", "server.js"]
```

### ENTRYPOINT

Defines the executable that runs when the container starts.

```dockerfile
ENTRYPOINT ["node"]
```

You can combine it with `CMD`:

```dockerfile
ENTRYPOINT ["node"]
CMD ["server.js"]
```

This executes:

```bash
node server.js
```

## 6.3 CMD vs ENTRYPOINT

| CMD | ENTRYPOINT |
|---|---|
| Provides default command or arguments | Defines the main executable |
| Easier to override at runtime | Usually remains the main executable |
| Can provide default arguments to ENTRYPOINT | Can be combined with CMD |

Example:

```dockerfile
ENTRYPOINT ["node"]
CMD ["app.js"]
```

Run:

```bash
docker run my-app
```

Equivalent to:

```bash
node app.js
```

Override CMD:

```bash
docker run my-app worker.js
```

Equivalent to:

```bash
node worker.js
```

## 6.4 What is a multi-stage build?

A multi-stage build uses multiple `FROM` instructions to separate build-time requirements from runtime requirements.

Suppose a Next.js application requires:

- Node.js.
- npm packages.
- TypeScript compilation.
- Next.js build tools.
- Production runtime files.

You do not need all development tools inside the final production image.

A multi-stage build allows you to build in one stage and copy only the necessary artifacts into the runtime stage.

### Single-stage build

```text
Node.js Base Image
       |
Dependencies
       |
Source Code
       |
Build Tools
       |
Production Application
```

Everything may remain in the final image.

### Multi-stage build

```text
Dependencies Stage
       |
       ↓
Build Stage
       |
       ↓
Production Runtime Stage
       |
       ↓
Small Production Image
```

Docker recommends multi-stage builds to separate build dependencies from the final runtime image. [GitHub](https://github.com/docker/docs/blob/main/content/manuals/build/building/multi-stage.md?utm_source=chatgpt.com)

## 6.5 Production Dockerfile for Next.js 16

First, configure standalone output in `next.config.ts`:

```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  output: "standalone",
};

export default nextConfig;
```

A production Dockerfile:

```dockerfile
# syntax=docker/dockerfile:1

FROM node:22-bookworm-slim AS base

WORKDIR /app


# -------------------------
# Dependencies Stage
# -------------------------

FROM base AS deps

COPY package.json package-lock.json ./

RUN npm ci


# -------------------------
# Build Stage
# -------------------------

FROM base AS builder

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules

COPY . .

ENV NEXT_TELEMETRY_DISABLED=1

RUN npm run build


# -------------------------
# Production Stage
# -------------------------

FROM base AS runner

WORKDIR /app

ENV NODE_ENV=production

ENV NEXT_TELEMETRY_DISABLED=1

RUN groupadd --system --gid 1001 nodejs \
    && useradd --system --uid 1001 --gid nodejs nextjs

COPY --from=builder /app/public ./public

COPY --from=builder --chown=nextjs:nodejs \
    /app/.next/standalone ./

COPY --from=builder --chown=nextjs:nodejs \
    /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT=3000

ENV HOSTNAME="0.0.0.0"

CMD ["node", "server.js"]
```

### Explanation

**Dependencies stage**

Installs dependencies using the lockfile.

```dockerfile
RUN npm ci
```

**Build stage**

Copies the source and builds the Next.js application.

```dockerfile
RUN npm run build
```

**Production stage**

Copies only the runtime files needed for the standalone server.

```dockerfile
COPY --from=builder /app/.next/standalone ./
```

It also copies static assets:

```dockerfile
COPY --from=builder /app/.next/static ./.next/static
```

Finally, it runs as a non-root user.

```dockerfile
USER nextjs
```

**Important:** This example assumes your project uses npm, has a `package-lock.json`, and does not need additional runtime files or packages. If your application uses native modules, monorepo workspaces, custom assets, or runtime-generated files, adjust the Dockerfile accordingly.

## 6.6 Create `.dockerignore`

A `.dockerignore` file excludes unnecessary files from the Docker build context.

Create:

```text
.dockerignore
```

Example:

```text
node_modules
.next
.git
.github

.env
.env.*
!.env.example

npm-debug.log

coverage

Dockerfile*
docker-compose*
README.md

*.log
```

Why exclude these files?

- `node_modules`: Dependencies should be installed inside the image.
- `.next`: Build output should be generated inside the build stage.
- `.git`: Git history is not needed in the runtime image.
- `.env`: Prevents accidental inclusion of local environment files.
- Logs and coverage: Usually unnecessary for the production image.

Docker recommends using `.dockerignore` to reduce build context size and avoid including unnecessary files. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

## 6.7 Build and run the Next.js image

Build:

```bash
docker build -t ecommerce-web:v1 .
```

Run:

```bash
docker run -d \
  --name ecommerce-web \
  -p 3000:3000 \
  ecommerce-web:v1
```

Open:

```text
http://localhost:3000
```

## 6.8 Docker build optimization

Recommended practices:

1. Use a trusted, appropriately sized base image.
2. Pin base image versions.
3. Copy package manifests before source files.
4. Use `npm ci` with a lockfile.
5. Use multi-stage builds.
6. Exclude unnecessary files using `.dockerignore`.
7. Run as a non-root user.
8. Avoid installing unnecessary packages.
9. Use BuildKit cache mounts when useful.
10. Rebuild images regularly for security updates.

Docker's official build best practices cover caching, base image selection, multi-stage builds, and non-root execution. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

---

# 7. Docker Compose

## 7.1 What is Docker Compose?

Docker Compose is a tool used to define and run multi-container applications using a YAML configuration file.

Suppose your application needs:

- Next.js frontend.
- Express backend.
- MongoDB database.
- Redis cache.

Without Docker Compose, you might need several separate `docker run` commands.

With Docker Compose, you can define all services in one file.

## 7.2 Why do we need Docker Compose?

Imagine this architecture:

```text
E-commerce Application
        |
        ├── Next.js
        |
        ├── Express API
        |
        ├── MongoDB
        |
        └── Redis
```

Docker Compose allows you to start the development stack with:

```bash
docker compose up -d
```

And stop it with:

```bash
docker compose down
```

## 7.3 Docker Compose architecture

```text
             Docker Compose
                    |
                    ↓
               compose.yaml
                    |
        ┌───────────┼───────────┐
        ↓           ↓           ↓
     Frontend      API       Database
        |           |           |
        └───────────┼───────────┘
                    |
                  Redis
```

## 7.4 Create a Docker Compose file

Project structure:

```text
ecommerce/
│
├── web/
│   └── Dockerfile
│
├── api/
│   └── Dockerfile
│
├── compose.yaml
└── .env
```

Example `compose.yaml`:

```yaml
services:

  web:
    build:
      context: ./web

    ports:
      - "3000:3000"

    depends_on:
      - api

    environment:
      API_INTERNAL_URL: http://api:4000

  api:
    build:
      context: ./api

    ports:
      - "4000:4000"

    environment:
      PORT: 4000
      MONGODB_URI: mongodb://mongo:27017/ecommerce
      REDIS_HOST: redis
      REDIS_PORT: 6379

    depends_on:
      - mongo
      - redis

  mongo:
    image: mongo:8

    volumes:
      - mongo_data:/data/db

  redis:
    image: redis:7

volumes:
  mongo_data:
```

These image tags are illustrative. For production, pin versions and preferably immutable image digests according to your update policy.

### Explanation

`services` defines the containers.

```yaml
services:
```

`web` represents the frontend.

```yaml
web:
  build:
    context: ./web
```

`api` represents the backend.

```yaml
api:
  build:
    context: ./api
```

`mongo` runs the database.

```yaml
mongo:
  image: mongo:8
```

`redis` runs the cache.

```yaml
redis:
  image: redis:7
```

`volumes` defines persistent storage.

```yaml
volumes:
  mongo_data:
```

## 7.5 Important Docker Compose commands

Start all services:

```bash
docker compose up
```

Start in background:

```bash
docker compose up -d
```

Build and start:

```bash
docker compose up --build -d
```

Stop services:

```bash
docker compose stop
```

Stop and remove containers and the Compose network:

```bash
docker compose down
```

Remove containers and named volumes too:

```bash
docker compose down -v
```

**Warning:** `docker compose down -v` removes declared named volumes and can delete database data. Use it carefully.

View running services:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f
```

View logs for one service:

```bash
docker compose logs -f api
```

Execute a command:

```bash
docker compose exec api sh
```

## 7.6 What is `depends_on`?

`depends_on` controls service startup and shutdown ordering.

Example:

```yaml
api:
  depends_on:
    - mongo
```

This ensures Compose starts the MongoDB service before starting the API service.

However, it does not automatically guarantee that MongoDB is ready to accept connections.

You can use health checks and a readiness condition where supported by the Compose implementation.

Example:

```yaml
services:
  mongo:
    image: mongo:8

    healthcheck:
      test:
        [
          "CMD",
          "mongosh",
          "--quiet",
          "--eval",
          "db.adminCommand('ping')"
        ]

      interval: 10s
      timeout: 5s
      retries: 5

  api:
    build: ./api

    depends_on:
      mongo:
        condition: service_healthy
```

The API should still implement its own database connection retry and failure handling.

## 7.7 Docker Compose vs Kubernetes

| Docker Compose | Kubernetes |
|---|---|
| Defines multi-container applications | Orchestrates containerized workloads across a cluster |
| Excellent for local development | Designed for cluster-scale operations |
| Simple configuration | More extensive configuration |
| Basic service management | Scheduling, reconciliation, scaling, rollout management |
| Limited built-in orchestration | Advanced orchestration |
| `compose.yaml` | Kubernetes manifests and APIs |

**When should you use Docker Compose?**

- Local development.
- Integration testing.
- Small self-hosted deployments.
- Running supporting services such as MongoDB and Redis locally.

**When should you use Kubernetes?**

- Multiple application workloads.
- Cluster-level scheduling.
- Automated recovery.
- Horizontal scaling.
- Complex deployment strategies.
- Infrastructure requiring orchestration across multiple nodes.

---

# 8. Docker Networking and Volumes

## 8.1 What is Docker networking?

Docker networking allows containers to communicate with one another and with external systems.

Example:

```text
Browser
   |
   ↓
localhost:3000
   |
   ↓
Next.js Container
   |
   ↓
Express API Container
   |
   ↓
MongoDB Container
```

Without networking, containers would not have a convenient mechanism for communicating with each other.

## 8.2 Types of Docker networks

### 1. Bridge Network

The default network driver commonly used for containers on a single Docker host.

Containers connected to the same user-defined bridge network can communicate using container names.

Example:

```bash
docker network create ecommerce-network
```

Run MongoDB:

```bash
docker run -d \
  --name mongo \
  --network ecommerce-network \
  mongo:8
```

Run an API container:

```bash
docker run -d \
  --name api \
  --network ecommerce-network \
  my-api:v1
```

The API can connect to MongoDB using:

```text
mongodb://mongo:27017/ecommerce
```

The hostname `mongo` resolves to the MongoDB container through Docker's user-defined network DNS.

### 2. Host Network

In host networking mode, a container shares the host network namespace.

Example:

```bash
docker run --network host nginx
```

Behavior differs by platform. On Linux, the container uses the host network namespace. Docker Desktop has platform-specific limitations.

### 3. None Network

Disables normal external network connectivity for the container, apart from loopback.

Example:

```bash
docker run --network none alpine
```

Useful for isolated workloads.

### 4. Overlay Network

Overlay networks connect containers across multiple Docker hosts, commonly in Docker Swarm environments.

## 8.3 Docker port mapping

Consider:

```bash
docker run -p 8080:3000 my-app
```

This means:

```text
Host Port: 8080
      |
      ↓
Container Port: 3000
```

You access the application using:

```text
http://localhost:8080
```

The application itself listens on port 3000 inside the container.

## 8.4 What is Docker volume?

A Docker volume provides persistent storage independent of a container's writable filesystem layer.

Suppose you run MongoDB inside a container.

Without persistent storage:

```text
MongoDB Container
       |
       ↓
Container Writable Layer
```

Removing the container also removes its writable layer.

With a volume:

```text
MongoDB Container
       |
       ↓
Docker Volume
       |
       ↓
Persistent Database Data
```

The volume can survive container replacement.

Docker recommends volumes as the preferred mechanism for persistent data generated by containers. [Docker Documentation](https://docs.docker.com/engine/storage/volumes/?utm_source=chatgpt.com)

## 8.5 Types of storage mounts

### A. Named Volume

Docker manages the volume.

Create:

```bash
docker volume create mongo_data
```

Use:

```bash
docker run -d \
  --name mongo \
  -v mongo_data:/data/db \
  mongo:8
```

### B. Bind Mount

Maps a directory from the host machine into a container.

Example:

```bash
docker run -d \
  --name node-app \
  -v ./src:/app/src \
  node:22-bookworm-slim
```

This is useful during development when you want source-code changes on the host to be visible inside the container.

Bind mounts are tightly coupled to host filesystem paths and can give the container write access to host files. Use read-only mounts when appropriate. [Docker Documentation](https://docs.docker.com/engine/storage/bind-mounts/?utm_source=chatgpt.com)

### C. tmpfs Mount

Stores temporary files in memory rather than persistent disk storage.

Example:

```bash
docker run --tmpfs /tmp my-app
```

Useful for temporary data that should not persist.

## 8.6 Named Volume vs Bind Mount

| Feature | Named Volume | Bind Mount |
|---|---|---|
| Storage location | Managed by Docker | Host filesystem path |
| Host access | Less direct | Direct |
| Portability | Better for container-managed data | Depends on host paths |
| Common use | Database files | Development source code |
| Management | Docker CLI/API | Host filesystem |

## 8.7 Example: MongoDB with Docker Compose

```yaml
services:
  mongo:
    image: mongo:8

    container_name: ecommerce-mongo

    ports:
      - "27017:27017"

    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

Start:

```bash
docker compose up -d
```

The database files are stored in the named volume.

**Important:** Persistent storage is not a backup strategy. You still need scheduled backups, retention, recovery testing, and appropriate off-host storage.

---

# 9. Container Security and Optimization

Containerization does not automatically make an application secure.

A poorly configured container can expose credentials, run with excessive privileges, or contain vulnerable dependencies.

## 9.1 Run containers as non-root

By default, some container images run their processes as root.

Running as root increases the impact of a container compromise.

Dockerfile:

```dockerfile
FROM node:22-bookworm-slim

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN chown -R node:node /app

USER node

CMD ["npm", "start"]
```

The Node official image provides a non-root `node` user in commonly used variants.

For a production image, ensure required files and directories have appropriate ownership.

## 9.2 Do not store secrets inside images

Bad:

```dockerfile
ENV DATABASE_PASSWORD=my-secret-password
```

This embeds the value into the image configuration.

Better:

Pass runtime configuration from the deployment environment.

```bash
docker run -d \
  -e DATABASE_HOST=mongo \
  -e DATABASE_PASSWORD="$DB_PASSWORD" \
  my-app
```

For production, use a proper secrets manager or orchestration secret mechanism.

Avoid printing secrets in commands, build logs, shell history, or CI output.

## 9.3 Read-only filesystem

Where possible, make the container filesystem read-only.

Example:

```bash
docker run \
  --read-only \
  --tmpfs /tmp \
  my-app
```

Your application must be designed so that it writes only to explicitly permitted temporary or persistent locations.

## 9.4 Drop unnecessary Linux capabilities

Containers should not receive privileges they do not need.

Example:

```bash
docker run \
  --cap-drop=ALL \
  my-app
```

Some applications need specific capabilities. Add only the minimum required.

## 9.5 Resource limits

Limit CPU and memory to prevent one container from consuming excessive host resources.

Example:

```bash
docker run -d \
  --memory=512m \
  --cpus=1.0 \
  my-app
```

## 9.6 Image scanning

Scan container images for known vulnerabilities.

Tools include:

- Trivy
- Docker Scout
- Grype

Example with Trivy:

```bash
trivy image my-app:v1
```

## 9.7 Container security checklist

- [ ] Use trusted base images.
- [ ] Pin image versions.
- [ ] Rebuild images regularly.
- [ ] Scan images for vulnerabilities.
- [ ] Run as a non-root user.
- [ ] Avoid privileged containers.
- [ ] Drop unnecessary capabilities.
- [ ] Avoid embedding secrets.
- [ ] Use read-only filesystems where possible.
- [ ] Limit CPU and memory.
- [ ] Minimize installed packages.
- [ ] Use `.dockerignore`.

Docker's image-building guidance recommends minimal trusted base images, regular rebuilds, non-root execution, and removal of unnecessary components. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

---

# 10. Kubernetes Architecture

## 10.1 What is Kubernetes?

**Kubernetes (K8s)** is an open-source container orchestration platform.

It automates the deployment, scaling, networking, and management of containerized applications.

Docker helps you build and run containers.

Kubernetes manages containerized workloads across a cluster of machines.

### Without Kubernetes

Imagine you have five application servers.

You need to manage:

- Application deployment.
- Container restarts.
- Traffic distribution.
- Scaling.
- Configuration.
- Health checks.
- Rolling updates.

You might perform much of this manually or with custom scripts.

### With Kubernetes

You declare the desired state of your application.

For example:

```text
I want 3 replicas of ecommerce-api
```

Kubernetes continuously works to maintain that desired state.

If one Pod fails, Kubernetes can create a replacement through its workload controllers.

## 10.2 What is a Kubernetes cluster?

A Kubernetes cluster is a group of machines managed by Kubernetes.

It contains:

1. Control Plane.
2. Worker Nodes.

Architecture:

```text
                  Kubernetes Cluster
                           |
              ┌────────────┴────────────┐
              ↓                         ↓
         Control Plane              Worker Nodes
              |                         |
       ┌──────┼──────┐             ┌─────┼─────┐
       ↓      ↓      ↓             ↓     ↓     ↓
     API   Scheduler Controllers  Node  Node  Node
   Server                       1     2     3
                                  |
                                  ↓
                              Application
                                Pods
```

## 10.3 Control Plane components

The control plane manages the cluster.

### A. kube-apiserver

The API server exposes the Kubernetes API.

It is the main entry point for cluster management operations.

When you run:

```bash
kubectl apply -f deployment.yaml
```

`kubectl` communicates with the API server.

### B. etcd

`etcd` is a distributed key-value store used to store Kubernetes cluster state and configuration.

It stores information such as:

- Cluster configuration.
- Kubernetes objects.
- Resource metadata.
- Cluster state.

### C. kube-scheduler

The scheduler selects a suitable worker node for Pods that have not yet been assigned to a node.

It considers:

- CPU and memory requirements.
- Node availability.
- Scheduling constraints.
- Affinity and anti-affinity.
- Taints and tolerations.

### D. kube-controller-manager

Runs controllers that continuously compare actual state with desired state.

For example:

If a Deployment requires three replicas but only two are available, the relevant controllers work to restore the desired state.

### E. cloud-controller-manager

Integrates Kubernetes with cloud-provider functionality when configured.

Examples include:

- Cloud load balancers.
- Cloud routes.
- Cloud node information.

## 10.4 Worker Node components

Worker nodes execute application workloads.

### A. kubelet

An agent running on each worker node.

It communicates with the control plane and ensures that assigned Pods are running according to their specifications.

### B. Container Runtime

Runs containers through the Container Runtime Interface (CRI).

Examples:

- containerd.
- CRI-O.

Docker Engine itself is not the standard Kubernetes container runtime integration in modern Kubernetes clusters.

### C. kube-proxy

Implements Service networking behavior on nodes in many Kubernetes deployments.

Some networking implementations use alternatives that do not require kube-proxy.

### Kubernetes architecture summary

| Component | Responsibility |
|---|---|
| API Server | Exposes Kubernetes API |
| etcd | Stores cluster state |
| Scheduler | Selects nodes for Pods |
| Controller Manager | Reconciles desired and actual state |
| kubelet | Manages Pods on a node |
| Container Runtime | Executes containers |
| kube-proxy | Implements Service networking in applicable configurations |

The official Kubernetes architecture documentation explains these control-plane and node components. [Kubernetes](https://kubernetes.io/docs/concepts/overview/components/?utm_source=chatgpt.com)

## 10.5 Kubernetes installation options for learning

You can learn Kubernetes locally using:

- Minikube
- Kind
- K3d
- Docker Desktop Kubernetes, if enabled and supported by your installation.

For cloud environments, managed Kubernetes offerings include:

- Amazon Elastic Kubernetes Service
- Azure Kubernetes Service
- Google Kubernetes Engine

For learning, Minikube or Kind is sufficient.

Official Kubernetes learning resources: [Kubernetes Documentation](https://kubernetes.io/docs/home/?utm_source=chatgpt.com)

---

# 11. Pods, Deployments and ReplicaSets

These are some of the most important Kubernetes concepts.

## 11.1 What is a Pod?

A **Pod** is the smallest deployable unit in Kubernetes.

A Pod contains one or more containers that share certain resources.

Containers in the same Pod share:

- Network namespace.
- Pod IP address.
- Port space.
- Configured shared volumes.

Example:

```text
Pod: ecommerce-api
       |
       ├── API Container
       |
       └── Log Sidecar Container
```

Containers in the same Pod can communicate using `localhost`.

However, containers in different Pods communicate through networking. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/?utm_source=chatgpt.com)

### Why does Kubernetes use Pods?

A Pod provides a unit for:

- Scheduling.
- Networking.
- Storage attachment.
- Health monitoring.
- Container lifecycle management.

For most ordinary web applications, one main application container per Pod is a good starting design.

## 11.2 Create a Pod

Create `pod.yaml`:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: ecommerce-api

  labels:
    app: ecommerce-api

spec:
  containers:
    - name: api

      image: nginx:1.27

      ports:
        - containerPort: 80
```

Apply:

```bash
kubectl apply -f pod.yaml
```

Check Pods:

```bash
kubectl get pods
```

Detailed information:

```bash
kubectl describe pod ecommerce-api
```

Delete:

```bash
kubectl delete pod ecommerce-api
```

This example uses NGINX to demonstrate Pod concepts.

## 11.3 What is a ReplicaSet?

A ReplicaSet maintains a specified number of matching Pods.

Suppose you need three API Pods.

```text
ReplicaSet
    |
    ├── Pod 1
    ├── Pod 2
    └── Pod 3
```

If one Pod is deleted, the ReplicaSet creates a replacement to restore the desired count.

Example:

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: ecommerce-api

spec:
  replicas: 3

  selector:
    matchLabels:
      app: ecommerce-api

  template:
    metadata:
      labels:
        app: ecommerce-api

    spec:
      containers:
        - name: api
          image: nginx:1.27
          ports:
            - containerPort: 80
```

Apply:

```bash
kubectl apply -f replicaset.yaml
```

A ReplicaSet maintains Pod replicas but does not provide the full rollout-management features of a Deployment.

## 11.4 What is a Deployment?

A Deployment manages application updates and ReplicaSets.

It provides:

- Desired replica management.
- Rolling updates.
- Rollback.
- Revision history.
- Declarative application updates.

Architecture:

```text
Deployment
    |
    ↓
ReplicaSet
    |
    ├── Pod 1
    ├── Pod 2
    └── Pod 3
```

In ordinary stateless application deployments, you generally create a Deployment rather than managing ReplicaSets directly.

## 11.5 Create a Deployment

Create `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ecommerce-api

spec:
  replicas: 3

  selector:
    matchLabels:
      app: ecommerce-api

  template:
    metadata:
      labels:
        app: ecommerce-api

    spec:
      containers:
        - name: api

          image: nginx:1.27

          ports:
            - containerPort: 80
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Check:

```bash
kubectl get deployments
```

Check ReplicaSets:

```bash
kubectl get replicasets
```

Check Pods:

```bash
kubectl get pods
```

## 11.6 Scale a Deployment manually

```bash
kubectl scale deployment ecommerce-api --replicas=5
```

Kubernetes will attempt to maintain five replicas.

Verify:

```bash
kubectl get pods
```

## 11.7 Rolling updates

Suppose you currently run:

```text
ecommerce-api:v1
```

You want to deploy:

```text
ecommerce-api:v2
```

With a rolling update, Kubernetes gradually replaces old Pods with new Pods.

```text
Old Pods
   |
   ↓
Start New Pods
   |
   ↓
Wait for Readiness
   |
   ↓
Remove Old Pods
   |
   ↓
New Version Running
```

Update the image:

```bash
kubectl set image deployment/ecommerce-api \
  api=myregistry/ecommerce-api:v2
```

Monitor rollout:

```bash
kubectl rollout status deployment/ecommerce-api
```

View rollout history:

```bash
kubectl rollout history deployment/ecommerce-api
```

Rollback:

```bash
kubectl rollout undo deployment/ecommerce-api
```

Kubernetes Deployments support rolling updates, rollback, and configurable `maxSurge` and `maxUnavailable` behavior. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)

## 11.8 Deployment vs ReplicaSet vs Pod

| Pod | ReplicaSet | Deployment |
|---|---|---|
| Runs containers | Maintains Pod count | Manages ReplicaSets |
| Smallest deployable unit | Maintains replicas | Manages application rollout |
| Does not independently recreate itself | Recreates missing Pods | Supports updates and rollback |
| Contains application containers | Controls matching Pods | Controls ReplicaSets |

---

# 12. Kubernetes Services and Ingress

## 12.1 What is a Kubernetes Service?

Pods are temporary.

Their IP addresses may change when Pods are recreated.

If an application directly connects to a specific Pod IP, it can break when that Pod is replaced.

A Kubernetes **Service** provides a stable network endpoint for accessing a group of Pods.

Example:

```text
              Express API
                   |
                   ↓
             API Service
              /       \
             ↓         ↓
          Pod 1      Pod 2
```

The Service selects Pods using labels and provides stable service discovery.

## 12.2 Why do we need Services?

Suppose your application has three backend Pods.

```text
Pod 1: 10.0.0.10
Pod 2: 10.0.0.11
Pod 3: 10.0.0.12
```

If Pod 1 is replaced, its IP address may change.

Instead of connecting directly to the Pod, clients connect to a Service.

```text
http://ecommerce-api
```

Kubernetes provides service discovery through cluster DNS for normal Services.

## 12.3 Types of Kubernetes Services

### A. ClusterIP

Exposes the Service inside the cluster.

```yaml
apiVersion: v1
kind: Service

metadata:
  name: ecommerce-api

spec:
  type: ClusterIP

  selector:
    app: ecommerce-api

  ports:
    - port: 4000
      targetPort: 4000
```

This is the default Service type.

Use it for internal communication.

Example:

```text
Next.js Pod
    |
    ↓
ecommerce-api:4000
    |
    ↓
API Pods
```

### B. NodePort

Exposes a Service through a port on each node.

Example:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: ecommerce-api

spec:
  type: NodePort

  selector:
    app: ecommerce-api

  ports:
    - port: 4000
      targetPort: 4000
      nodePort: 30080
```

Access through a reachable node IP:

```text
http://NODE_IP:30080
```

NodePort is commonly used for testing and certain network architectures, but it is not usually the preferred public application entry point.

### C. LoadBalancer

Requests an external load balancer through the cluster's infrastructure integration.

```yaml
apiVersion: v1
kind: Service

metadata:
  name: ecommerce-api

spec:
  type: LoadBalancer

  selector:
    app: ecommerce-api

  ports:
    - port: 80
      targetPort: 4000
```

On a supported cloud platform, the integration may provision an external load balancer.

Kubernetes itself does not automatically provide every external load-balancer implementation. [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)

### Service type comparison

| Type | Accessibility | Typical use |
|---|---|---|
| ClusterIP | Internal cluster | Internal service communication |
| NodePort | Node IP and port | Testing or specific external routing |
| LoadBalancer | External load balancer | Public or private cloud service exposure |
| ExternalName | DNS alias | Referencing an external hostname |

## 12.4 What is Ingress?

Ingress is a Kubernetes API resource that defines HTTP/HTTPS routing rules for external access to Services.

Example:

```text
             Internet
                 |
                 ↓
          Ingress Controller
                 |
        ┌────────┴────────┐
        ↓                 ↓
    /api/users          /
        |                |
        ↓                ↓
    API Service      Web Service
        |                |
        ↓                ↓
     API Pods         Web Pods
```

An Ingress resource defines routing rules.

An **Ingress Controller** implements those rules.

Examples of implementations include NGINX-based controllers and cloud-provider integrations.

### Important current Kubernetes distinction

The Kubernetes Ingress API is stable but frozen. The Kubernetes project recommends considering the newer Gateway API for future development. Ingress is not removed and remains supported. [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/?utm_source=chatgpt.com)

## 12.5 Example Ingress resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: ecommerce-ingress

spec:
  ingressClassName: nginx

  rules:
    - host: example.com

      http:
        paths:

          - path: /api
            pathType: Prefix

            backend:
              service:
                name: ecommerce-api
                port:
                  number: 4000

          - path: /
            pathType: Prefix

            backend:
              service:
                name: ecommerce-web
                port:
                  number: 3000
```

This example assumes an appropriately installed Ingress Controller that recognizes the `nginx` IngressClass.

## 12.6 Ingress vs Ingress Controller vs Service

| Component | Responsibility |
|---|---|
| Service | Stable endpoint for Pods |
| Ingress | Declares HTTP/HTTPS routing rules |
| Ingress Controller | Implements those rules |

**Remember:** Creating an Ingress resource without a compatible controller does not automatically make your application publicly accessible.

---

# 13. ConfigMaps and Secrets

## 13.1 What is a ConfigMap?

A ConfigMap is a Kubernetes object used to store non-confidential configuration data.

Examples:

- Application environment.
- Feature flags.
- Service URLs.
- Non-sensitive configuration.
- Logging levels.

Suppose your Node.js application needs:

```env
NODE_ENV=production
PORT=4000
LOG_LEVEL=info
```

You can store these values in a ConfigMap.

## 13.2 Create a ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: ecommerce-config

data:
  NODE_ENV: "production"
  PORT: "4000"
  LOG_LEVEL: "info"
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

Use it inside a Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ecommerce-api

spec:
  replicas: 2

  selector:
    matchLabels:
      app: ecommerce-api

  template:
    metadata:
      labels:
        app: ecommerce-api

    spec:
      containers:
        - name: api

          image: myregistry/ecommerce-api:v1

          ports:
            - containerPort: 4000

          envFrom:
            - configMapRef:
                name: ecommerce-config
```

Now the container receives the ConfigMap values as environment variables.

Kubernetes ConfigMaps separate non-confidential configuration from application images. [Kubernetes](https://kubernetes.io/docs/concepts/configuration/configmap/?utm_source=chatgpt.com)

## 13.3 What is a Kubernetes Secret?

A Secret is a Kubernetes object designed to store confidential configuration data.

Examples:

- Database passwords.
- API keys.
- Authentication credentials.
- TLS private keys.
- Certificates.

Example secret values:

```text
DB_USERNAME
DB_PASSWORD
JWT_SECRET
```

## 13.4 Create a Secret

Prefer creating sensitive values through a secure deployment mechanism rather than committing secret values into Git.

Example command:

```bash
kubectl create secret generic ecommerce-secret \
  --from-literal=DB_USERNAME=appuser \
  --from-literal=DB_PASSWORD="$DB_PASSWORD"
```

Avoid putting actual passwords directly in shell commands that may be retained in shell history or exposed through process inspection.

For production, use a managed secrets service or secure CI/CD secret integration where appropriate.

## 13.5 Use a Secret in a Deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ecommerce-api

spec:
  replicas: 2

  selector:
    matchLabels:
      app: ecommerce-api

  template:
    metadata:
      labels:
        app: ecommerce-api

    spec:
      containers:
        - name: api

          image: myregistry/ecommerce-api:v1

          envFrom:
            - secretRef:
                name: ecommerce-secret
```

The application can read the values using:

```ts
const username = process.env.DB_USERNAME;
const password = process.env.DB_PASSWORD;
```

## 13.6 ConfigMap vs Secret

| ConfigMap | Secret |
|---|---|
| Non-confidential configuration | Confidential configuration |
| No secrecy mechanism | Designed for sensitive values |
| Service URLs, log levels | Passwords, credentials, tokens |
| Stored as ordinary configuration data | Encoded in API representation and subject to secret-specific handling |

### Important Kubernetes Secret security warning

**Base64 encoding is not encryption.**

A Secret's `data` field uses Base64 encoding, which can be decoded easily.

For production:

- Enable encryption at rest for Secret data in etcd.
- Restrict access using RBAC.
- Apply least-privilege permissions.
- Avoid committing Secret manifests containing real credentials.
- Consider an external secrets manager.
- Rotate credentials periodically.

Kubernetes documentation explicitly discusses Secret access control and additional security measures. [Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/?utm_source=chatgpt.com)

---

# 14. Persistent Volumes and Storage

## 14.1 Why do we need persistent storage?

Containers are replaceable.

Suppose you run MongoDB inside a Kubernetes Pod.

```text
MongoDB Pod
    |
    ↓
Container Storage
```

If the Pod is deleted and recreated, data stored only in the container's writable layer is not a reliable persistent storage strategy.

You need storage independent of the Pod lifecycle.

## 14.2 What is PersistentVolume (PV)?

A PersistentVolume is a storage resource available to a Kubernetes cluster.

It represents storage provisioned by an administrator or dynamically by a storage system.

Examples:

- Cloud block storage.
- Network file systems.
- Storage provided through CSI drivers.

## 14.3 What is PersistentVolumeClaim (PVC)?

A PersistentVolumeClaim is a request for storage made by an application.

For example:

```text
Application Pod
      |
      ↓
PersistentVolumeClaim
      |
      ↓
PersistentVolume
      |
      ↓
Actual Storage
```

The application requests storage through a PVC instead of directly managing the underlying storage implementation.

## 14.4 What is StorageClass?

A StorageClass describes a class of storage and can enable dynamic provisioning.

For example:

```text
fast-ssd
standard-storage
high-performance-storage
```

The exact names and behavior depend on the cluster's storage configuration.

## 14.5 Create a PVC

Create `mongo-pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: mongo-pvc

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 10Gi
```

Apply:

```bash
kubectl apply -f mongo-pvc.yaml
```

This requests 10 GiB of storage.

Whether it binds successfully depends on the available PersistentVolumes or configured default StorageClass.

## 14.6 Use PVC in a Pod

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: mongo

spec:
  containers:
    - name: mongo

      image: mongo:8

      ports:
        - containerPort: 27017

      volumeMounts:
        - name: mongo-storage
          mountPath: /data/db

  volumes:
    - name: mongo-storage

      persistentVolumeClaim:
        claimName: mongo-pvc
```

The MongoDB container uses the PVC-backed storage.

## 14.7 Access modes

Common access modes include:

| Access mode | Meaning |
|---|---|
| ReadWriteOnce (RWO) | Read-write mounting by a single node |
| ReadOnlyMany (ROX) | Read-only mounting by multiple nodes |
| ReadWriteMany (RWX) | Read-write mounting by multiple nodes |
| ReadWriteOncePod (RWOP) | Read-write mounting by a single Pod, when supported |

These modes describe how a volume can be mounted, not necessarily how many application processes may access the data.

Actual support depends on the storage driver.

## 14.8 PV vs PVC vs StorageClass

| Component | Purpose |
|---|---|
| PV | Represents available storage |
| PVC | Requests storage |
| StorageClass | Defines storage provisioning characteristics |

Kubernetes separates storage provisioning from storage consumption through PVs, PVCs, and StorageClasses. [Kubernetes](https://v1-34.docs.kubernetes.io/docs/concepts/storage/persistent-volumes/?utm_source=chatgpt.com)

**Important:** Running a production database inside Kubernetes requires additional planning for backups, replication, failover, storage performance, and recovery. For many applications, a managed database service is operationally simpler.

---

# 15. Horizontal Pod Autoscaling (HPA)

## 15.1 What is autoscaling?

Autoscaling automatically adjusts the number of application instances according to workload demand.

Suppose you normally run three API Pods.

During a traffic spike, three Pods may not be enough.

You want Kubernetes to increase the number of Pods automatically.

When demand decreases, you want Kubernetes to reduce the replica count.

## 15.2 What is Horizontal Pod Autoscaler?

**Horizontal Pod Autoscaler (HPA)** automatically changes the replica count of supported workloads such as Deployments and StatefulSets.

Architecture:

```text
          Incoming Traffic
                 |
                 ↓
            API Service
                 |
        ┌────────┼────────┐
        ↓        ↓        ↓
      Pod 1    Pod 2    Pod 3
                 |
                 ↓
           Metrics Server
                 |
                 ↓
                HPA
                 |
                 ↓
          Adjust Replicas
```

HPA can use CPU, memory, and supported custom or external metrics. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/autoscaling/horizontal-pod-autoscale/?utm_source=chatgpt.com)

## 15.3 Manual scaling vs autoscaling

Manual:

```bash
kubectl scale deployment ecommerce-api \
  --replicas=5
```

Automatic:

HPA observes metrics and changes the replica count according to the configured target.

## 15.4 Configure resource requests and limits

HPA based on CPU utilization requires appropriate CPU resource requests.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"

  limits:
    cpu: "1000m"
    memory: "512Mi"
```

Explanation:

- `250m` CPU = 0.25 CPU core.
- `1000m` CPU = 1 CPU core.
- `256Mi` = 256 mebibytes of memory.

Requests influence scheduling and CPU utilization calculations.

Limits constrain resource usage according to Kubernetes resource behavior.

## 15.5 Create an HPA

Create `hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: ecommerce-api-hpa

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ecommerce-api

  minReplicas: 2
  maxReplicas: 10

  metrics:
    - type: Resource

      resource:
        name: cpu

        target:
          type: Utilization
          averageUtilization: 70
```

Apply:

```bash
kubectl apply -f hpa.yaml
```

Check:

```bash
kubectl get hpa
```

Detailed information:

```bash
kubectl describe hpa ecommerce-api-hpa
```

## 15.6 How does HPA work?

Suppose:

```text
Target CPU utilization: 70%
Current average utilization: 90%
```

The HPA controller observes the metrics and calculates a desired replica count.

If the current capacity is insufficient, Kubernetes increases the desired replica count.

The HPA controller periodically evaluates metrics rather than reacting continuously or instantaneously.

## 15.7 HPA requirements

For resource-based autoscaling, you generally need:

- Metrics Server.
- CPU or memory resource requests.
- Correct Deployment configuration.
- Working resource metrics APIs.

Check metrics:

```bash
kubectl top pods
```

Check nodes:

```bash
kubectl top nodes
```

The Kubernetes resource metrics pipeline commonly uses Metrics Server to provide CPU and memory metrics for HPA and `kubectl top`. [Kubernetes](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/?utm_source=chatgpt.com)

**Important:** HPA increases the number of Pods, but it cannot create unlimited infrastructure capacity. Your cluster must have sufficient node resources, or an additional node autoscaling mechanism must provide them.

---

# 16. Kubernetes Health Probes

## 16.1 What are Kubernetes probes?

Kubernetes probes allow the kubelet to check the health and readiness of containers.

There are three important types:

1. Liveness Probe.
2. Readiness Probe.
3. Startup Probe.

## 16.2 Liveness Probe

A liveness probe checks whether a container is functioning.

If it repeatedly fails, Kubernetes can restart the container.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health/live
    port: 4000

  initialDelaySeconds: 10
  periodSeconds: 10
  timeoutSeconds: 2
  failureThreshold: 3
```

## 16.3 Readiness Probe

A readiness probe checks whether the application should receive traffic.

If it fails, the Pod is marked unready and is removed from the eligible Service endpoints.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /health/ready
    port: 4000

  initialDelaySeconds: 5
  periodSeconds: 5
  timeoutSeconds: 2
  failureThreshold: 3
```

## 16.4 Startup Probe

A startup probe protects applications that take a long time to initialize.

Example:

```yaml
startupProbe:
  httpGet:
    path: /health/live
    port: 4000

  periodSeconds: 10
  failureThreshold: 30
```

This allows up to approximately five minutes for startup under these settings before the startup probe is considered failed.

After the startup probe succeeds, normal liveness and readiness probing can take over.

Kubernetes documents these probes as separate mechanisms with different operational purposes. [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/?utm_source=chatgpt.com)

## 16.5 Liveness vs Readiness vs Startup

| Probe | Main question | Failure behavior |
|---|---|---|
| Liveness | Is the process functioning? | Container may be restarted |
| Readiness | Can it receive traffic? | Pod removed from eligible Service endpoints |
| Startup | Has initialization completed? | Container may be restarted after startup failure |

## 16.6 Complete Deployment with probes

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ecommerce-api

spec:
  replicas: 2

  selector:
    matchLabels:
      app: ecommerce-api

  template:
    metadata:
      labels:
        app: ecommerce-api

    spec:
      containers:
        - name: api

          image: myregistry/ecommerce-api:v1

          ports:
            - containerPort: 4000

          resources:
            requests:
              cpu: "250m"
              memory: "256Mi"

            limits:
              cpu: "1000m"
              memory: "512Mi"

          startupProbe:
            httpGet:
              path: /health/live
              port: 4000
            periodSeconds: 10
            failureThreshold: 30

          livenessProbe:
            httpGet:
              path: /health/live
              port: 4000
            periodSeconds: 10
            failureThreshold: 3

          readinessProbe:
            httpGet:
              path: /health/ready
              port: 4000
            periodSeconds: 5
            failureThreshold: 3
```

The application must implement the health endpoints and listen on the correct interface and port.

**Common mistake:** Do not make liveness depend directly on every external service. A temporary database or payment-provider outage should not necessarily restart every API Pod.

---

# 17. Helm Charts

## 17.1 What is Helm?

**Helm** is a package manager for Kubernetes.

Kubernetes applications commonly require multiple YAML manifests:

- Deployment.
- Service.
- ConfigMap.
- HPA.
- Ingress.
- PersistentVolumeClaim.

Managing all these files separately can become repetitive.

Helm packages related Kubernetes resources into a reusable unit called a **Chart**.

## 17.2 Why do we need Helm?

Suppose you deploy the same application to:

- Development.
- Staging.
- Production.

The configuration differs:

| Configuration | Development | Staging | Production |
|---|---|---|---|
| Replicas | 1 | 2 | 5 |
| CPU request | 100m | 250m | 500m |
| Memory | 128Mi | 256Mi | 512Mi |
| Domain | dev.example.com | staging.example.com | example.com |

Without templating, you may maintain multiple nearly identical YAML files.

Helm allows reusable templates and environment-specific values.

## 17.3 Helm chart structure

Create a chart:

```bash
helm create ecommerce-api
```

Typical structure:

```text
ecommerce-api/
│
├── Chart.yaml
│
├── values.yaml
│
├── charts/
│
└── templates/
    │
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    ├── hpa.yaml
    └── _helpers.tpl
```

### Chart.yaml

Contains chart metadata.

Example:

```yaml
apiVersion: v2

name: ecommerce-api

description: Ecommerce API Helm Chart

type: application

version: 1.0.0

appVersion: "1.0.0"
```

### values.yaml

Contains default configuration values.

```yaml
replicaCount: 2

image:
  repository: myregistry/ecommerce-api
  tag: "1.0.0"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 4000

resources:
  requests:
    cpu: 250m
    memory: 256Mi

  limits:
    cpu: 1000m
    memory: 512Mi
```

### templates/deployment.yaml

Helm template example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: {{ .Release.Name }}

spec:
  replicas: {{ .Values.replicaCount }}

  selector:
    matchLabels:
      app: {{ .Release.Name }}

  template:
    metadata:
      labels:
        app: {{ .Release.Name }}

    spec:
      containers:
        - name: api

          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"

          imagePullPolicy: {{ .Values.image.pullPolicy }}

          ports:
            - containerPort: {{ .Values.service.port }}

          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

The values are substituted into the Kubernetes manifest when Helm renders the chart.

Helm uses `Chart.yaml`, `values.yaml`, and templates to package reusable Kubernetes configuration. [Helm](https://helm.sh/docs/topics/charts/?utm_source=chatgpt.com)

## 17.4 Helm commands

Install a chart:

```bash
helm install ecommerce ./ecommerce-api
```

List releases:

```bash
helm list
```

Upgrade:

```bash
helm upgrade ecommerce ./ecommerce-api
```

Install or upgrade:

```bash
helm upgrade --install ecommerce ./ecommerce-api
```

Uninstall:

```bash
helm uninstall ecommerce
```

View chart values:

```bash
helm show values ./ecommerce-api
```

Validate templates:

```bash
helm lint ./ecommerce-api
```

Render templates locally:

```bash
helm template ecommerce ./ecommerce-api
```

## 17.5 Environment-specific values

Create:

```text
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

Development:

```yaml
replicaCount: 1

image:
  tag: "dev"
```

Production:

```yaml
replicaCount: 5

image:
  tag: "1.0.0"
```

Deploy production:

```bash
helm upgrade --install ecommerce \
  ./ecommerce-api \
  -f values-prod.yaml
```

**Important:** Do not put production passwords or API keys in a Helm values file committed to Git. Use an appropriate secret-management mechanism.

---

# 18. Container Orchestration

## 18.1 What is container orchestration?

Container orchestration is the automated management of containerized applications across infrastructure.

It includes:

- Scheduling.
- Deployment.
- Scaling.
- Networking.
- Service discovery.
- Health monitoring.
- Failure recovery.
- Rolling updates.
- Configuration management.

## 18.2 Why is orchestration required?

Suppose you run an e-commerce platform with:

```text
Frontend: 3 containers
API: 5 containers
Workers: 4 containers
Redis: 1 container
```

Total:

```text
13 containers
```

Now imagine you need to:

- Deploy a new API version.
- Replace a failed container.
- Scale workers to 10.
- Distribute incoming traffic.
- Maintain configuration across instances.

Managing all of this manually becomes difficult.

Orchestration automates much of this work.

## 18.3 Docker Compose vs Kubernetes

| Feature | Docker Compose | Kubernetes |
|---|---|---|
| Container management | Multi-container application | Cluster-wide workloads |
| Scheduling | Basic host-level execution | Cluster scheduler |
| Self-healing | Limited restart behavior | Controllers reconcile desired state |
| Scaling | Manual service scaling | Manual and automatic workload scaling |
| Load balancing | Service networking | Services and external routing integrations |
| Rolling updates | More limited | Deployment strategies |
| Persistent storage | Docker volumes | PVs, PVCs, StorageClasses |
| Configuration | Environment variables and files | ConfigMaps and Secrets |
| Complexity | Lower | Higher |

## 18.4 Kubernetes desired-state model

One of Kubernetes' most important concepts is declarative desired state.

You define:

```yaml
spec:
  replicas: 3
```

This tells Kubernetes that you want three replicas.

Kubernetes continuously works toward that state.

```text
Desired State:
3 Pods

Actual State:
2 Pods

       |
       ↓
Kubernetes Controller
       |
       ↓
Create Replacement Pod

       |
       ↓

Actual State:
3 Pods
```

This process is called reconciliation.

## 18.5 Self-healing

Kubernetes can:

- Restart containers that fail.
- Replace Pods managed by controllers.
- Stop routing traffic to unready Pods.
- Schedule replacement Pods when resources are available.

However, self-healing does not mean every failure is automatically recoverable.

For example:

- A broken database migration may require manual intervention.
- An invalid image may repeatedly fail to start.
- Insufficient cluster capacity may prevent new Pods from being scheduled.
- Incorrect application logic is not fixed automatically.

## 18.6 Orchestration tools

| Tool | Purpose |
|---|---|
| Kubernetes | Container orchestration |
| Docker Swarm | Docker-native orchestration |
| Nomad | Workload orchestration |
| Amazon ECS | AWS container orchestration |
| Amazon EKS | Managed Kubernetes |
| Azure AKS | Managed Kubernetes |
| Google GKE | Managed Kubernetes |

---

# 19. CI/CD with Docker and Kubernetes

## 19.1 What is a CI/CD pipeline?

A CI/CD pipeline automates software validation, packaging, and deployment.

Suppose you push code to GitHub.

A pipeline can:

1. Install dependencies.
2. Run tests.
3. Build the application.
4. Build a Docker image.
5. Scan the image.
6. Push the image to a registry.
7. Deploy the image to Kubernetes.
8. Verify rollout health.

Architecture:

```text
Developer
    |
    ↓
GitHub Push
    |
    ↓
GitHub Actions
    |
    ├── Install Dependencies
    |
    ├── Run Tests
    |
    ├── Build Application
    |
    ├── Build Docker Image
    |
    ├── Scan Image
    |
    ├── Push Image
    |
    └── Deploy to Kubernetes
                 |
                 ↓
            Production
```

## 19.2 What is GitHub Actions?

GitHub Actions is a workflow automation platform integrated with GitHub repositories.

It can automate:

- Tests.
- Builds.
- Code quality checks.
- Container image builds.
- Deployment workflows.
- Scheduled maintenance tasks.

Official documentation: [GitHub Actions](https://docs.github.com/en/actions?utm_source=chatgpt.com)

## 19.3 Create a basic CI workflow

Project structure:

```text
my-project/
│
├── src/
├── package.json
├── package-lock.json
└── .github/
    └── workflows/
        └── ci.yml
```

Create `.github/workflows/ci.yml`:

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
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run lint
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Build application
        run: npm run build
```

This is a basic learning example. In production, review and pin third-party actions to verified commit SHAs according to your supply-chain security policy.

## 19.4 Build and push a Docker image

A production pipeline can build an image and push it to a container registry.

Conceptual workflow:

```yaml
name: Docker Build

on:
  push:
    branches:
      - main

jobs:
  build:

    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Login to GHCR
        run: |
          echo "${{ secrets.GITHUB_TOKEN }}" |
          docker login ghcr.io \
            -u "${{ github.actor }}" \
            --password-stdin

      - name: Build image
        run: |
          docker build \
            -t ghcr.io/${{ github.repository }}:${{ github.sha }} .

      - name: Push image
        run: |
          docker push \
            ghcr.io/${{ github.repository }}:${{ github.sha }}
```

This is an abbreviated example. A hardened workflow should also configure package naming and permissions correctly, handle lowercase registry names where required, scan the image, and use trusted action versions.

## 19.5 Deploy to Kubernetes

Once the image has been pushed to the registry, update the Deployment.

Example:

```bash
kubectl set image deployment/ecommerce-api \
  api=ghcr.io/my-org/ecommerce-api:COMMIT_SHA

kubectl rollout status deployment/ecommerce-api
```

The cluster must be authenticated to the registry and able to pull the image.

For production deployment, prefer a controlled GitOps or deployment workflow with environment approvals and restricted credentials.

## 19.6 What is GitOps?

GitOps is an operational approach in which Git repositories contain the desired configuration for applications and infrastructure.

A controller continuously compares the configuration in Git with the actual cluster state and reconciles differences.

Popular GitOps tools:

- Argo CD
- Flux CD

Conceptually:

```text
Git Repository
      |
      ↓
GitOps Controller
      |
      ↓
Kubernetes Cluster
      |
      ↓
Application State
```

The benefit is that deployment configuration becomes version-controlled and auditable.

---

# 20. Production Deployment Architecture

Now let's combine everything into a realistic deployment architecture.

## 20.1 Application requirements

Assume we are deploying an e-commerce application with:

- Next.js frontend.
- Express backend.
- MongoDB database.
- Redis cache.
- BullMQ workers.
- Product image storage.
- Payment integration.
- Email notifications.

## 20.2 High-level architecture

```text
                     Users
                       |
                       ↓
                      DNS
                       |
                       ↓
                  CDN / WAF
                       |
                       ↓
             External Load Balancer
                       |
                       ↓
                Kubernetes Gateway
                       |
              ┌────────┴────────┐
              ↓                 ↓
          Web Service       API Service
              |                 |
              ↓                 ↓
         Next.js Pods       API Pods
                                |
                  ┌─────────────┼─────────────┐
                  ↓             ↓             ↓
                Redis        MongoDB       Job Queue
                  |             |             |
                  |             |             ↓
                  |             |          Workers
                  |             |             |
                  └─────────────┴─────────────┘
                                |
                                ↓
                         External Services
```

This is a logical reference architecture. The actual network entry point may use an Ingress Controller or a Gateway API implementation, depending on the cluster.

## 20.3 Deployment components

| Component | Responsibility |
|---|---|
| CDN | Static content delivery |
| WAF | Web application traffic protection |
| Load Balancer | External traffic distribution |
| Gateway / Ingress | HTTP routing |
| Next.js Pods | Frontend rendering |
| API Pods | Backend business logic |
| Redis | Caching and shared state |
| MongoDB | Persistent application data |
| Queue | Background job distribution |
| Workers | Background processing |
| Object Storage | Images and documents |
| Prometheus | Metrics collection |
| Grafana | Dashboards |
| OpenTelemetry | Telemetry instrumentation |

## 20.4 Example Kubernetes project structure

```text
ecommerce-platform/
│
├── apps/
│   │
│   ├── web/
│   │   ├── src/
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   ├── api/
│   │   ├── src/
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   └── worker/
│       ├── src/
│       ├── Dockerfile
│       └── package.json
│
├── infrastructure/
│   │
│   ├── kubernetes/
│   │   ├── namespace.yaml
│   │   ├── web-deployment.yaml
│   │   ├── web-service.yaml
│   │   ├── api-deployment.yaml
│   │   ├── api-service.yaml
│   │   ├── worker-deployment.yaml
│   │   ├── configmap.yaml
│   │   └── hpa.yaml
│   │
│   └── helm/
│       └── ecommerce/
│
├── .github/
│   └── workflows/
│
└── README.md
```

## 20.5 Production deployment principles

1. Build immutable Docker images.
2. Tag images with unique versions or commit identifiers.
3. Avoid deploying the mutable `latest` tag as your release identifier.
4. Store configuration outside application images.
5. Store secrets in secure secret-management systems.
6. Use Deployments for stateless application workloads.
7. Configure CPU and memory requests.
8. Configure resource limits where appropriate.
9. Configure readiness and liveness probes.
10. Use Services for internal networking.
11. Use a Gateway or suitable Ingress Controller for external routing.
12. Configure HPA where workload metrics justify it.
13. Use persistent storage for stateful data.
14. Keep database backups independent of container lifecycle.
15. Configure monitoring, logging, and distributed tracing.
16. Automate testing and deployment.
17. Use controlled rollouts and rollback procedures.
18. Regularly scan and rebuild container images.

---

# 21. Important Kubernetes Troubleshooting Commands

These commands are essential when working with Kubernetes.

### Cluster information

```bash
kubectl cluster-info
```

```bash
kubectl get nodes
```

```bash
kubectl get namespaces
```

### Pods

```bash
kubectl get pods
```

```bash
kubectl get pods -o wide
```

```bash
kubectl describe pod POD_NAME
```

### Logs

```bash
kubectl logs POD_NAME
```

Follow logs:

```bash
kubectl logs -f POD_NAME
```

For a specific container:

```bash
kubectl logs POD_NAME -c CONTAINER_NAME
```

For a previous crashed container:

```bash
kubectl logs POD_NAME --previous
```

### Deployments

```bash
kubectl get deployments
```

```bash
kubectl describe deployment ecommerce-api
```

```bash
kubectl rollout status deployment/ecommerce-api
```

### Services

```bash
kubectl get services
```

```bash
kubectl describe service ecommerce-api
```

### Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

### Execute commands inside a Pod

```bash
kubectl exec -it POD_NAME -- sh
```

### Resource usage

```bash
kubectl top pods
```

```bash
kubectl top nodes
```

### Apply and delete manifests

```bash
kubectl apply -f deployment.yaml
```

```bash
kubectl delete -f deployment.yaml
```

### Common Pod states

| Status | Meaning |
|---|---|
| Pending | Pod has not started successfully; scheduling or setup may be incomplete |
| Running | Pod is bound to a node and containers are running |
| Succeeded | All containers completed successfully |
| Failed | All containers have terminated and at least one failed |
| CrashLoopBackOff | Container repeatedly crashes and restart attempts are backed off |
| ImagePullBackOff | Kubernetes cannot pull the requested image |
| ErrImagePull | Image pull failed |
| ContainerCreating | Container setup is in progress |

**Troubleshooting tip:** When a Pod is stuck, start with:

```bash
kubectl describe pod POD_NAME
```

Then inspect its events and logs. Do not assume every `Pending` Pod has the same cause.

---

# 22. Common DevOps and Kubernetes Mistakes

1. **Using Docker without understanding images and containers.** An image is the template; a container is its running instance.

2. **Putting everything in one large Docker image.** Use multi-stage builds and keep the runtime image minimal.

3. **Using `latest` for production releases.** Prefer explicit versions or immutable digests.

4. **Copying `.env` files into Docker images.** Provide runtime configuration securely.

5. **Running containers as root unnecessarily.** Prefer non-root execution.

6. **Assuming Docker containers automatically share data.** Configure volumes explicitly.

7. **Using bind mounts for every production storage requirement.** Choose storage based on lifecycle and performance needs.

8. **Assuming `EXPOSE` publishes a port.** Use `-p` or an appropriate network configuration.

9. **Assuming `depends_on` guarantees application readiness.** Add health checks and application-level retry logic.

10. **Confusing Pod and Deployment.** A Pod runs containers; a Deployment manages rollout and replica behavior.

11. **Connecting directly to Pod IPs.** Use Services for stable service discovery.

12. **Creating an Ingress without a controller.** An Ingress resource requires an implementation.

13. **Treating ConfigMaps as secret storage.** Use appropriate secret-management mechanisms for credentials.

14. **Assuming Base64 is encryption.** It is only an encoding.

15. **Ignoring resource requests.** Scheduling and HPA behavior depend on resource configuration.

16. **Using liveness probes to test every dependency.** This can trigger unnecessary restarts.

17. **Scaling Pods without sufficient node capacity.** Additional replicas may remain unscheduled.

18. **Assuming PVCs automatically provide backups.** Persistent storage and backup/recovery are separate requirements.

19. **Putting real secrets in Helm values committed to Git.** Keep confidential values outside ordinary source-controlled configuration.

20. **Deploying without rollout monitoring.** Always verify that new Pods become ready.

21. **Assuming Kubernetes automatically fixes application bugs.** Orchestration handles infrastructure-level lifecycle problems, not incorrect business logic.

22. **Using Kubernetes for every small application.** It introduces operational complexity and may be unnecessary.

---

# 23. Interview Questions and Answers

## Basic Level

**1. What is DevOps?**

DevOps is a set of practices and cultural principles that integrates software development and IT operations to improve software delivery, automation, and reliability.

**2. What is Docker?**

Docker is a platform used to build, distribute, and run applications in containers.

**3. What is a Docker image?**

A Docker image is an immutable template containing the application filesystem and configuration required to create containers.

**4. What is a Docker container?**

A Docker container is an isolated running or stopped instance created from an image.

**5. What is a Dockerfile?**

A Dockerfile is a text file containing instructions for building a Docker image.

**6. What is Docker Compose?**

Docker Compose is a tool used to define and run multi-container applications through YAML configuration.

**7. What is a Docker volume?**

A Docker volume is persistent storage managed by Docker that exists independently of a container's writable layer.

**8. What is Kubernetes?**

Kubernetes is an open-source container orchestration platform used to deploy, scale, and manage containerized workloads.

**9. What is a Pod?**

A Pod is the smallest deployable unit in Kubernetes and can contain one or more containers sharing certain resources.

**10. What is a Service in Kubernetes?**

A Service provides a stable network endpoint for accessing a group of Pods.

## Intermediate Level

**11. What is the difference between Docker image and container?**

An image is a template. A container is an instance created from that image.

**12. What is a multi-stage Docker build?**

It uses multiple build stages to separate build-time dependencies from the final runtime image.

**13. What is the difference between CMD and ENTRYPOINT?**

CMD provides a default command or arguments, while ENTRYPOINT defines the main executable.

**14. What is the difference between a named volume and a bind mount?**

A named volume is managed by Docker, while a bind mount maps a host filesystem path into a container.

**15. What is Docker networking?**

Docker networking allows containers to communicate with other containers, hosts, and external systems.

**16. What is a ReplicaSet?**

A ReplicaSet maintains a specified number of matching Pods.

**17. What is a Deployment?**

A Deployment manages ReplicaSets and provides declarative application updates, scaling, and rollback.

**18. What is the difference between ClusterIP and NodePort?**

ClusterIP exposes a Service inside the cluster, while NodePort exposes it through a port on each node.

**19. What is Ingress?**

Ingress is a Kubernetes API resource defining external HTTP/HTTPS routing rules to backend Services.

**20. What is a ConfigMap?**

A ConfigMap stores non-confidential configuration data for Kubernetes workloads.

**21. What is a Kubernetes Secret?**

A Secret is a Kubernetes object designed to store confidential configuration such as passwords and tokens.

**22. What is a PersistentVolumeClaim?**

A PVC is a request for persistent storage made by a Kubernetes workload.

## Advanced Level

**23. What is the difference between Docker Compose and Kubernetes?**

Docker Compose is primarily used to define and run multi-container applications, while Kubernetes provides cluster-level container orchestration, scheduling, scaling, and recovery.

**24. What is a rolling update?**

A rolling update gradually replaces old application instances with new ones while controlling service availability.

**25. What is HPA?**

Horizontal Pod Autoscaler automatically adjusts the replica count of a Kubernetes workload based on observed metrics.

**26. What is the difference between liveness and readiness probes?**

Liveness checks whether the container should be restarted. Readiness determines whether it should receive traffic.

**27. What is a startup probe?**

A startup probe allows an application sufficient time to initialize before normal liveness and readiness checks take effect.

**28. What is Helm?**

Helm is a Kubernetes package manager that uses reusable charts and configurable values to manage Kubernetes resources.

**29. What is a Kubernetes control plane?**

The control plane manages cluster state through components such as the API server, scheduler, controller manager, and etcd.

**30. What is etcd?**

etcd is a consistent and highly available key-value store used by Kubernetes to persist cluster state.

**31. What is GitOps?**

GitOps is an operational approach in which Git stores the desired system configuration and controllers reconcile the live environment with that configuration.

**32. What happens when a Kubernetes Pod crashes?**

The result depends on the Pod's controller and restart policy. A Deployment-managed workload will generally have a replacement Pod created to maintain the desired replica count.

**33. Why should containers be stateless where possible?**

Stateless containers are easier to replace, reschedule, and scale because application instances do not depend on local persistent state.

**34. What is the difference between a PV and a PVC?**

A PV represents available storage, while a PVC requests storage for an application.

**35. Does Kubernetes automatically provide a database backup?**

No. Persistent volumes provide storage, but database backups and recovery must be configured separately.

---

# 24. Practical Assignment

## Project: Deploy a Containerized E-commerce Application

Build and deploy a small production-style application using Docker and Kubernetes.

### Required technology stack

| Component | Technology |
|---|---|
| Frontend | Next.js 16 + TypeScript |
| Backend | Node.js + Express |
| Database | MongoDB |
| Cache | Redis |
| Background jobs | BullMQ |
| Containerization | Docker |
| Local orchestration | Docker Compose |
| Production orchestration | Kubernetes |
| Package management | Helm |
| CI/CD | GitHub Actions |

## Part 1 – Docker fundamentals

- [ ] Install Docker Desktop.
- [ ] Run `hello-world`.
- [ ] Create a Dockerfile for a Node.js application.
- [ ] Build an image.
- [ ] Run a container.
- [ ] Inspect the container.
- [ ] View container logs.
- [ ] Enter the container shell.
- [ ] Stop and remove the container.

## Part 2 – Production Dockerfile

- [ ] Create a multi-stage Next.js Dockerfile.
- [ ] Enable Next.js standalone output.
- [ ] Create `.dockerignore`.
- [ ] Run the container as a non-root user.
- [ ] Use runtime environment variables.
- [ ] Build and test the production image.
- [ ] Compare the image size with a simple single-stage image.

## Part 3 – Docker Compose

- [ ] Create `compose.yaml`.
- [ ] Add Next.js frontend.
- [ ] Add Express API.
- [ ] Add MongoDB.
- [ ] Add Redis.
- [ ] Configure service networking.
- [ ] Configure database persistence.
- [ ] Add service health checks.
- [ ] Run the complete application with `docker compose up -d`.

## Part 4 – Kubernetes basics

- [ ] Install Minikube or Kind.
- [ ] Configure `kubectl`.
- [ ] Create a namespace.
- [ ] Create an API Pod.
- [ ] Create a Deployment.
- [ ] Configure three replicas.
- [ ] Create a ClusterIP Service.
- [ ] Test internal networking.
- [ ] Perform a rolling update.
- [ ] Test rollback.

## Part 5 – Kubernetes configuration and storage

- [ ] Create a ConfigMap.
- [ ] Create a Secret.
- [ ] Inject configuration into Pods.
- [ ] Create a PersistentVolumeClaim.
- [ ] Mount storage into a Pod.
- [ ] Verify data persists across Pod recreation.

## Part 6 – Scaling and reliability

- [ ] Configure CPU and memory requests.
- [ ] Configure resource limits.
- [ ] Configure liveness probes.
- [ ] Configure readiness probes.
- [ ] Configure startup probes.
- [ ] Create an HPA.
- [ ] Generate test traffic.
- [ ] Observe scaling behavior.
- [ ] Verify recovery from a failed Pod.

## Part 7 – Helm

- [ ] Install Helm.
- [ ] Create a Helm chart.
- [ ] Configure `values.yaml`.
- [ ] Create Deployment and Service templates.
- [ ] Create development and production values files.
- [ ] Install the chart.
- [ ] Upgrade the release.
- [ ] Roll back or uninstall the release.

## Part 8 – CI/CD

- [ ] Create a GitHub Actions workflow.
- [ ] Run lint and tests.
- [ ] Build a Docker image.
- [ ] Scan the image.
- [ ] Push the image to GHCR.
- [ ] Deploy the new image to Kubernetes.
- [ ] Verify rollout health.
- [ ] Document the rollback process.

---

# 25. Quick Revision Notes

| Topic | Remember |
|---|---|
| DevOps | Collaboration, automation, and reliable delivery |
| CI | Automatically validate code changes |
| Continuous Delivery | Keep changes deployable |
| Continuous Deployment | Automatically deploy successful changes |
| Docker | Containerization platform |
| Docker Image | Immutable application template |
| Container | Running or stopped image instance |
| Dockerfile | Instructions for building an image |
| Docker Compose | Defines multi-container applications |
| Docker Volume | Persistent container storage |
| Bind Mount | Maps host filesystem into container |
| Multi-stage Build | Separates build and runtime environments |
| Kubernetes | Container orchestration platform |
| Cluster | Control plane and worker nodes |
| Pod | Smallest deployable Kubernetes unit |
| ReplicaSet | Maintains matching Pod replicas |
| Deployment | Manages ReplicaSets and rollouts |
| Service | Stable network endpoint for Pods |
| ClusterIP | Internal Service exposure |
| NodePort | Exposes Service through node ports |
| LoadBalancer | Requests external load balancing |
| Ingress | HTTP/HTTPS routing resource |
| ConfigMap | Non-confidential configuration |
| Secret | Confidential configuration object |
| PV | Persistent storage resource |
| PVC | Storage request |
| StorageClass | Defines storage provisioning |
| HPA | Automatically scales workload replicas |
| Liveness Probe | Checks whether a container should be restarted |
| Readiness Probe | Checks whether a Pod can receive traffic |
| Startup Probe | Checks whether application startup has completed |
| Helm | Kubernetes package manager |
| CI/CD | Automated validation and deployment |
| GitOps | Reconcile infrastructure with Git-defined desired state |

---

# 26. Official Documentation and Further Reading

These are the primary references for this phase:

1. **Docker Documentation**
   - [Docker Get Started](https://docs.docker.com/get-started/?utm_source=chatgpt.com)
   - [Dockerfile Reference](https://docs.docker.com/reference/dockerfile?utm_source=chatgpt.com)
   - [Docker Multi-Stage Builds](https://docs.docker.com/build/building/multi-stage?utm_source=chatgpt.com)
   - [Docker Build Best Practices](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)
   - [Docker Volumes](https://docs.docker.com/engine/storage/volumes/?utm_source=chatgpt.com)

2. **Kubernetes Documentation**
   - [Kubernetes Architecture](https://kubernetes.io/docs/concepts/overview/components/?utm_source=chatgpt.com)
   - [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/?utm_source=chatgpt.com)
   - [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
   - [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)
   - [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?utm_source=chatgpt.com)
   - [Kubernetes ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/?utm_source=chatgpt.com)
   - [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/?utm_source=chatgpt.com)
   - [Kubernetes HPA](https://kubernetes.io/docs/concepts/workloads/autoscaling/horizontal-pod-autoscale/?utm_source=chatgpt.com)
   - [Kubernetes Health Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/?utm_source=chatgpt.com)

3. **Helm Documentation**
   - [Helm Charts](https://helm.sh/docs/topics/charts/?utm_source=chatgpt.com)
   - [Helm Getting Started](https://helm.sh/docs/chart_template_guide/getting_started/?utm_source=chatgpt.com)

4. **GitHub Actions**
   - [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)

---

# 27. Phase Completion Checklist

**Your learning progress**

- [ ] DevOps Fundamentals and Culture
- [ ] Docker Architecture
- [ ] Docker Images and Containers
- [ ] Dockerfile and Multi-Stage Builds
- [ ] Docker Compose
- [ ] Docker Networking and Volumes
- [ ] Container Security and Optimization
- [ ] Kubernetes Architecture
- [ ] Pods, Deployments and ReplicaSets
- [ ] Services and Ingress
- [ ] ConfigMaps and Secrets
- [ ] Persistent Volumes and Storage
- [ ] Horizontal Pod Autoscaling
- [ ] Kubernetes Health Probes
- [ ] Helm Charts
- [ ] Container Orchestration
- [ ] CI/CD with Docker and Kubernetes
- [ ] Production Deployment Architecture

---

# Next Phase: Phase 38 – Cloud Computing, AWS Architecture and Production Infrastructure

The next phase will cover:

1. Cloud Computing Fundamentals
2. IaaS vs PaaS vs SaaS
3. Public vs Private vs Hybrid Cloud
4. AWS Global Infrastructure
5. IAM – Users, Roles and Policies
6. EC2 – Virtual Servers
7. VPC, Subnets and Route Tables
8. Security Groups and Network ACLs
9. Load Balancers and Auto Scaling Groups
10. S3 – Object Storage
11. RDS – Managed Relational Databases
12. MongoDB Atlas and Cloud Database Architecture
13. ElastiCache and Managed Redis
14. Route 53 and DNS
15. CloudFront and CDN
16. AWS Lambda and Serverless Computing
17. CloudWatch and Cloud Monitoring
18. AWS Secrets Manager
19. Infrastructure as Code with Terraform
20. Designing a Production-Ready AWS Architecture

**Phase 37 complete.**

When you're ready, say **"OK, Phase 38"** to continue.