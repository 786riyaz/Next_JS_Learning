# Phase 45 – Docker, Containerization and Docker Compose

**Level:** Advanced  
**Domain:** DevOps, Containerization and Cloud Engineering  
**Primary Technologies:** Docker, Docker Engine, Docker Compose, Node.js, Next.js, MySQL and MongoDB

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand virtualization and containerization.
2. Understand Docker architecture and its components.
3. Install and configure Docker on Windows and Linux.
4. Understand Docker Images and Containers.
5. Work with Docker Hub and container registries.
6. Create custom Docker images using Dockerfiles.
7. Understand Docker image layers and build caching.
8. Manage containers using Docker CLI commands.
9. Understand Docker volumes and bind mounts.
10. Configure Docker networking.
11. Work with Docker Compose.
12. Containerize Node.js and Express applications.
13. Containerize Next.js applications.
14. Run MySQL and MongoDB inside containers.
15. Configure environment variables and secrets.
16. Create optimized images using multi-stage builds.
17. Implement Docker health checks.
18. Understand Docker logging and resource management.
19. Apply Docker security best practices.
20. Deploy containerized applications to production servers.
21. Integrate Docker with AWS infrastructure.

---

# PART 1: INTRODUCTION TO CONTAINERIZATION

# 2. What Is Virtualization?

Virtualization is a technology that allows multiple virtual machines to run on a single physical computer.

A virtual machine behaves like an independent computer with its own:

- Operating system.
- CPU allocation.
- RAM allocation.
- Storage.
- Network configuration.

A software layer called a **Hypervisor** manages virtual machines.

### Traditional Architecture

Suppose you have one physical server.

You want to run three applications:

- Application A requires Ubuntu.
- Application B requires Windows Server.
- Application C requires another Linux environment.

You can create three virtual machines.

```text
              Physical Server
                    |
                    v
                Hypervisor
                    |
        +-----------+-----------+
        |           |           |
        v           v           v
       VM 1        VM 2        VM 3
        |           |           |
        v           v           v
      Ubuntu      Windows     Ubuntu
        |           |           |
        v           v           v
       App A       App B       App C
```

Each virtual machine runs its own operating system.

### Advantages

- Strong isolation between virtual machines.
- Different operating systems can run on one physical server.
- Independent resource allocation.
- Useful for server consolidation.

### Disadvantages

- Higher resource consumption.
- Each VM needs an operating system.
- Booting a VM takes longer than starting a typical container.
- Managing many VMs can become complicated.

---

# 3. What Is Containerization?

Containerization is a technology that packages an application and its required dependencies into an isolated execution environment called a **container**.

A container typically includes:

- Application code.
- Runtime.
- Libraries.
- Dependencies.
- Configuration required by the application.

Unlike a traditional VM, a Linux container generally shares the host's Linux kernel rather than running a separate guest kernel.

### Container Architecture

```text
             Physical Server
                    |
                    v
                Host OS
                    |
                    v
               Container Runtime
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Container Container Container
          A         B         C
          |         |         |
          v         v         v
        App A     App B     App C
```

Containers are generally lighter than full virtual machines because they do not each require a separate guest operating system.

## 3.1 Virtual Machine vs Container

| Feature | Virtual Machine | Container |
|---|---|---|
| Virtualization | Hardware-level virtualization | OS-level isolation |
| Operating System | Separate guest OS | Shares host kernel in the usual Linux-container model |
| Size | Generally larger | Generally smaller |
| Startup | Usually slower | Usually faster |
| Resource Usage | Higher | Generally lower |
| Isolation | Separate guest OS boundary | Kernel-level isolation mechanisms |
| Portability | Good | Excellent when runtime and architecture are compatible |
| Typical Use | Full OS environments | Application deployment |

### Important Understanding

Containers are not simply lightweight virtual machines.

They use operating-system features such as:

- Namespaces.
- Control groups (cgroups).
- Linux capabilities.
- Filesystem isolation.

These mechanisms help isolate processes and control their access to system resources.

Docker uses these technologies to provide containerization. [Docker Documentation](https://docs.docker.com/get-started/docker-overview?utm_source=chatgpt.com)

---

# PART 2: INTRODUCTION TO DOCKER

# 4. What Is Docker?

Docker is a platform for building, packaging, distributing and running applications inside containers.

Suppose you develop a Node.js application on your laptop.

Your environment:

```text
Node.js: 22
npm: 10
MySQL: 8
Operating System: Windows
```

You deploy it to a Linux server.

The server has:

```text
Node.js: 18
npm: 9
MySQL: 5.7
Operating System: Ubuntu
```

Your application might behave differently because the environments are different.

Docker helps solve this problem by packaging the application with its runtime and dependencies.

For example:

```text
Developer Laptop
      |
      v
Docker Image
      |
      v
Docker Registry
      |
      v
Production Server
      |
      v
Docker Container
```

The same image can be used across compatible Docker environments.

### Why Do We Need Docker?

1. Consistent development and production environments.
2. Easier application deployment.
3. Dependency isolation.
4. Simplified application scaling.
5. Easier CI/CD integration.
6. Faster environment setup.
7. Simplified microservice deployment.
8. Better portability.

Official documentation: [Docker Overview](https://docs.docker.com/get-started/docker-overview/)

---

# 5. Docker Architecture

Docker follows a client-server architecture.

Its important components include:

1. Docker Client.
2. Docker Daemon.
3. Docker Engine.
4. Container Runtime.
5. Docker Images.
6. Docker Containers.
7. Docker Registry.

### Architecture Diagram

```text
                Docker Client
                     |
                     |
               Docker CLI
                     |
                     v
                Docker API
                     |
                     v
              Docker Daemon
                 dockerd
                     |
          +----------+----------+
          |          |          |
          v          v          v
        Images    Containers  Networks
          |          |          |
          +----------+----------+
                     |
                     v
                 containerd
                     |
                     v
                   runc
                     |
                     v
                Linux Kernel
```

This is a simplified conceptual architecture.

## 5.1 Docker Client

The Docker Client is the interface used to interact with Docker.

You generally use the Docker CLI.

Example:

```bash
docker run nginx
```

The CLI sends a request to the Docker API.

## 5.2 Docker Daemon

The Docker daemon is a background service.

Its process is:

```text
dockerd
```

It manages Docker objects such as:

- Images.
- Containers.
- Networks.
- Volumes.

## 5.3 Docker Engine

Docker Engine is the core containerization platform.

It includes the Docker daemon, APIs and CLI components.

## 5.4 containerd

`containerd` is a container runtime component responsible for managing container lifecycle operations.

It handles tasks such as:

- Image transfer and storage.
- Container execution management.
- Container lifecycle operations.

## 5.5 runc

`runc` is a low-level container runtime that creates and runs containers according to the OCI runtime specification.

### Remember the Relationship

| Component | Responsibility |
|---|---|
| Docker CLI | User interface |
| Docker Daemon | Manages Docker objects |
| Docker Engine | Container management platform |
| containerd | Container lifecycle management |
| runc | Creates and starts containers |

---

# 6. What Is OCI?

OCI stands for **Open Container Initiative**.

It provides open standards for container technology.

Two important OCI specifications are:

1. Image Specification.
2. Runtime Specification.

These standards help container images and runtimes work across compatible implementations.

For example, an OCI-compatible image can be used by different container platforms that support the relevant image format.

---

# PART 3: DOCKER INSTALLATION

# 7. Installing Docker on Windows

Since you are working on Windows 11, Docker Desktop with the WSL 2 backend is a practical development setup.

### Requirements

Generally, you need:

- Supported Windows version.
- Hardware virtualization enabled.
- WSL 2 support.
- Sufficient RAM and disk space.

### Step 1: Install WSL

Open PowerShell as Administrator.

Run:

```powershell
wsl --install
```

Restart your computer if Windows asks you to.

Verify:

```powershell
wsl --version
```

Check installed distributions:

```powershell
wsl --list --verbose
```

### Step 2: Download Docker Desktop

Download Docker Desktop from the official website:

[Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

Install it and select the WSL 2 backend when prompted, if appropriate for your system.

### Step 3: Start Docker Desktop

Open Docker Desktop and wait until the Docker Engine is running.

### Step 4: Verify Installation

Open PowerShell:

```powershell
docker --version
```

Check Compose:

```powershell
docker compose version
```

Check Docker information:

```powershell
docker info
```

### Step 5: Run Your First Container

```powershell
docker run hello-world
```

Docker downloads the test image if it is not already available locally, creates a container and runs it.

If everything works, you will see a confirmation message.

Docker Desktop includes Docker Engine, Docker CLI and Docker Compose. [Docker Documentation](https://docs.docker.com/desktop/setup/install/windows-install/?utm_source=chatgpt.com)

---

# 8. Installing Docker on Ubuntu

For production Linux servers, Docker Engine is generally the appropriate option.

The following installation uses Docker's official Ubuntu package repository.

### Step 1: Update Packages

```bash
sudo apt update
```

### Step 2: Install Dependencies

```bash
sudo apt install ca-certificates curl
```

### Step 3: Add Docker's Official GPG Key

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
-o /etc/apt/keyrings/docker.asc
```

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Step 4: Add Docker Repository

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

### Step 5: Update Package Index

```bash
sudo apt update
```

### Step 6: Install Docker

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Step 7: Verify Docker

```bash
sudo systemctl status docker
```

### Step 8: Run Test Container

```bash
sudo docker run hello-world
```

These are based on Docker's official Ubuntu installation instructions. [Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com)

Official installation guide:

[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

**Security note:** Membership in the `docker` group grants highly privileged access and can effectively provide root-level control of the host. Do not add untrusted users to this group.

---

# PART 4: DOCKER IMAGES AND CONTAINERS

# 9. What Is a Docker Image?

A Docker Image is a read-only template used to create containers.

It contains:

- Application files.
- Runtime.
- Libraries.
- Dependencies.
- Filesystem layers.
- Metadata and startup configuration.

Example:

```text
node:22-alpine
```

This is a Node.js image based on Alpine Linux.

You can use it as the starting point for your Node.js application image.

### Image Architecture

```text
             Docker Image
                   |
        +----------+----------+
        |          |          |
        v          v          v
      Layer 1    Layer 2    Layer 3
        |          |          |
        v          v          v
     Base OS    Node.js    App Files
```

Images are immutable templates from which containers are created.

---

# 10. What Is a Docker Container?

A container is a running or stopped instance of a Docker image.

Example:

```text
Image:
nginx:latest

Container:
nginx-web-server
```

You can create multiple containers from the same image.

```text
                Docker Image
                  nginx
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Container Container Container
          A         B         C
```

Each container has its own runtime configuration and writable container layer.

### Image vs Container

| Feature | Image | Container |
|---|---|---|
| Definition | Template | Instance of an image |
| State | Immutable image layers | Has a writable layer |
| Execution | Not running | Can be running or stopped |
| Creation | Built or pulled | Created from an image |
| Storage | Image layers | Image layers plus container writable layer |

**Important:** A stopped container still exists. A container is not automatically deleted when it stops.

---

# 11. Docker Registry

A Docker Registry stores and distributes Docker images.

Examples:

- Docker Hub.
- Amazon Elastic Container Registry (ECR).
- GitHub Container Registry.
- Private Docker Registry.

### Image Distribution

```text
Developer Machine
       |
       | docker push
       v
Docker Registry
       |
       | docker pull
       v
Production Server
```

## 11.1 Docker Hub

Docker Hub is a popular container image registry.

Website:

[Docker Hub](https://hub.docker.com/)

You can:

- Download public images.
- Publish your own images.
- Store private repositories.
- Manage image versions.

## 11.2 Image Naming Convention

Example:

```text
username/my-node-app:1.0
```

Breakdown:

```text
username       -> Repository namespace
my-node-app    -> Image repository
1.0            -> Image tag
```

Another example:

```text
node:22-alpine
```

Here:

```text
node       -> Image name
22-alpine  -> Tag
```

### Important

Avoid relying on the `latest` tag for reproducible production deployments.

Prefer explicit version tags or immutable image digests.

---

# PART 5: BASIC DOCKER COMMANDS

# 12. Docker CLI Commands

These are some of the most frequently used Docker commands.

## 12.1 Check Docker Version

```bash
docker --version
```

## 12.2 Check Docker Information

```bash
docker info
```

## 12.3 Download an Image

```bash
docker pull nginx
```

This downloads the Nginx image.

## 12.4 List Images

```bash
docker images
```

Alternative:

```bash
docker image ls
```

## 12.5 Run a Container

```bash
docker run nginx
```

This creates and starts an Nginx container in the foreground.

## 12.6 Run in Detached Mode

```bash
docker run -d nginx
```

`-d` means detached mode.

The container runs in the background.

## 12.7 Assign Container Name

```bash
docker run -d --name my-nginx nginx
```

## 12.8 Map Ports

```bash
docker run -d -p 8080:80 nginx
```

This maps:

```text
Host Port:      8080
Container Port: 80
```

Open:

```text
http://localhost:8080
```

### Understanding Port Mapping

```text
Browser
   |
   | localhost:8080
   |
   v
Host Machine
   |
   | Port Mapping
   |
   v
Container Port 80
   |
   v
Nginx
```

General syntax:

```bash
docker run -p HOST_PORT:CONTAINER_PORT IMAGE
```

## 12.9 List Running Containers

```bash
docker ps
```

## 12.10 List All Containers

```bash
docker ps -a
```

## 12.11 Stop a Container

```bash
docker stop my-nginx
```

## 12.12 Start a Container

```bash
docker start my-nginx
```

## 12.13 Restart a Container

```bash
docker restart my-nginx
```

## 12.14 Remove a Container

```bash
docker rm my-nginx
```

If it is running:

```bash
docker rm -f my-nginx
```

## 12.15 Remove an Image

```bash
docker rmi nginx
```

## 12.16 View Container Logs

```bash
docker logs my-nginx
```

Follow logs:

```bash
docker logs -f my-nginx
```

## 12.17 Execute a Command Inside a Container

```bash
docker exec -it my-nginx bash
```

Some minimal images do not include Bash. In that case:

```bash
docker exec -it my-nginx sh
```

## 12.18 Inspect a Container

```bash
docker inspect my-nginx
```

This displays detailed container configuration.

## 12.19 View Container Resource Usage

```bash
docker stats
```

Displays resource usage such as:

- CPU.
- Memory.
- Network I/O.
- Block I/O.

## 12.20 View Docker Disk Usage

```bash
docker system df
```

### Command Revision Table

| Command | Purpose |
|---|---|
| `docker pull` | Download image |
| `docker images` | List images |
| `docker run` | Create and start container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker start` | Start stopped container |
| `docker stop` | Stop container |
| `docker restart` | Restart container |
| `docker rm` | Remove container |
| `docker rmi` | Remove image |
| `docker logs` | View logs |
| `docker exec` | Execute command inside container |
| `docker inspect` | Inspect object configuration |
| `docker stats` | Monitor resource usage |

---

# PART 6: DOCKERFILE

# 13. What Is a Dockerfile?

A Dockerfile is a text file containing instructions used to build a Docker image.

It defines:

- Base image.
- Working directory.
- Dependencies.
- Application files.
- Environment variables.
- Startup command.

Example:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

This Dockerfile creates an image for a Node.js application.

Official documentation: [Dockerfile Overview](https://docs.docker.com/build/concepts/dockerfile/)

---

# 14. Important Dockerfile Instructions

## 14.1 FROM

Defines the base image.

```dockerfile
FROM node:22-alpine
```

This tells Docker to start with a Node.js 22 image based on Alpine Linux.

A Dockerfile generally starts with a `FROM` instruction.

## 14.2 WORKDIR

Sets the working directory.

```dockerfile
WORKDIR /app
```

Subsequent relative paths generally use this directory.

## 14.3 COPY

Copies files from the build context into the image.

```dockerfile
COPY package.json ./
```

Copies `package.json` into `/app`.

## 14.4 RUN

Executes commands during image building.

```dockerfile
RUN npm install
```

This installs application dependencies while building the image.

## 14.5 EXPOSE

Documents the port the application is expected to listen on.

```dockerfile
EXPOSE 3000
```

**Important:** `EXPOSE` does not publish the port to the host. You still need `-p` or an equivalent port configuration.

## 14.6 CMD

Defines the default command executed when a container starts.

```dockerfile
CMD ["npm", "start"]
```

This is the exec form of CMD.

## 14.7 ENTRYPOINT

Defines the executable used when the container starts.

Example:

```dockerfile
ENTRYPOINT ["node"]
```

If combined with:

```dockerfile
CMD ["server.js"]
```

The effective command is:

```bash
node server.js
```

## 14.8 ENV

Sets environment variables in the image configuration.

```dockerfile
ENV NODE_ENV=production
```

Do not use `ENV` to store secrets that must remain confidential.

## 14.9 ARG

Defines build-time variables.

```dockerfile
ARG NODE_VERSION=22
```

Used during image building.

Example:

```dockerfile
FROM node:${NODE_VERSION}-alpine
```

Do not treat build arguments as a secure secret-storage mechanism.

## 14.10 ADD

Copies files and can perform additional operations, including extracting supported local archives.

Example:

```dockerfile
ADD archive.tar.gz /app/
```

For ordinary file copying, prefer `COPY`.

## 14.11 USER

Specifies the user used by subsequent instructions and the default container process.

```dockerfile
USER node
```

Running application processes as a non-root user is generally a security best practice.

## 14.12 VOLUME

Declares a mount point for persistent or externally mounted data.

```dockerfile
VOLUME ["/app/data"]
```

In modern deployments, explicitly configure named volumes or bind mounts when you need predictable storage management.

### Dockerfile Instruction Summary

| Instruction | Purpose |
|---|---|
| `FROM` | Base image |
| `WORKDIR` | Working directory |
| `COPY` | Copy files |
| `RUN` | Execute build commands |
| `EXPOSE` | Document application port |
| `CMD` | Default startup command |
| `ENTRYPOINT` | Main executable |
| `ENV` | Set environment variable |
| `ARG` | Build-time variable |
| `ADD` | Copy files with additional capabilities |
| `USER` | Set execution user |
| `VOLUME` | Declare mount point |

---

# PART 7: BUILDING YOUR FIRST DOCKER IMAGE

# 15. Create a Node.js Application

Let's create a basic Express application.

Project structure:

```text
docker-node-app/
│
├── package.json
├── package-lock.json
├── server.js
└── Dockerfile
```

### package.json

```json
{
  "name": "docker-node-app",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^5.1.0"
  }
}
```

Use a current compatible Express version and commit the generated lockfile.

### server.js

```javascript
const express = require("express");

const app = express();

const PORT = process.env.PORT || 3000;

app.get("/", (req, res) => {
    res.json({
        message: "Hello from Docker!",
        status: "success"
    });
});

app.get("/health", (req, res) => {
    res.json({
        status: "healthy"
    });
});

app.listen(PORT, "0.0.0.0", () => {
    console.log(`Server running on port ${PORT}`);
});
```

### Important: Why `0.0.0.0`?

Inside a container, your Node.js application should generally listen on:

```javascript
0.0.0.0
```

This allows it to accept connections through the container's network interfaces.

If you bind only to:

```javascript
localhost
```

or:

```javascript
127.0.0.1
```

the application may only accept connections originating inside its own network namespace.

---

# 16. Create Dockerfile

Create a file named:

```text
Dockerfile
```

Add:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

For reproducible installation, a project with a committed `package-lock.json` should normally use:

```dockerfile
RUN npm ci
```

instead of `npm install`.

### Step 1: Build the Image

Open the terminal inside the project directory.

Run:

```bash
docker build -t my-node-app:1.0 .
```

Explanation:

| Part | Meaning |
|---|---|
| `docker build` | Build an image |
| `-t` | Assign image name and tag |
| `my-node-app:1.0` | Image name and version |
| `.` | Current directory as build context |

### Step 2: Verify Image

```bash
docker images
```

You should see:

```text
REPOSITORY    TAG    IMAGE ID
my-node-app   1.0    ...
```

### Step 3: Run Container

```bash
docker run -d \
--name node-container \
-p 3000:3000 \
my-node-app:1.0
```

### Step 4: Test Application

Open:

```text
http://localhost:3000
```

Expected response:

```json
{
    "message": "Hello from Docker!",
    "status": "success"
}
```

### Step 5: Check Logs

```bash
docker logs node-container
```

### Step 6: Stop Container

```bash
docker stop node-container
```

You have successfully containerized a Node.js application.

---

# PART 8: DOCKER IMAGE LAYERS AND BUILD CACHE

# 17. What Are Docker Image Layers?

Docker images are built from filesystem layers.

Many Dockerfile instructions create layers.

Example:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

CMD ["npm", "start"]
```

Conceptually:

```text
             Final Image
                  |
                  v
             Application
               Files
                  |
                  v
            Dependencies
                  |
                  v
             Node.js Base
                Image
```

Each layer contributes filesystem changes or image configuration.

Layers can be reused across images and builds.

## 17.1 Docker Build Cache

Docker can reuse previously built layers when the relevant build inputs have not changed.

Suppose you have:

```dockerfile
COPY package*.json ./

RUN npm ci

COPY . .
```

If you only modify:

```text
server.js
```

Docker may reuse the dependency-installation layer because the package files have not changed.

This can significantly reduce build time.

### Bad Dockerfile Ordering

```dockerfile
COPY . .

RUN npm install
```

Any source-code change may invalidate the dependency installation layer.

### Better Dockerfile Ordering

```dockerfile
COPY package*.json ./

RUN npm ci

COPY . .
```

This improves caching.

### Build Cache Commands

```bash
docker build -t my-node-app .
```

Build without using cache:

```bash
docker build --no-cache -t my-node-app .
```

View build history:

```bash
docker history my-node-app
```

Docker recommends structuring Dockerfiles to maximize cache reuse. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

---

# PART 9: DOCKERIGNORE

# 18. What Is `.dockerignore`?

`.dockerignore` specifies files and directories that should be excluded from the Docker build context.

Create:

```text
.dockerignore
```

Example:

```text
node_modules
npm-debug.log
.git
.gitignore
.env
.env.*
.next
dist
coverage
Dockerfile
docker-compose.yml
```

The exact entries depend on your project.

### Why Is It Important?

Without `.dockerignore`, Docker may send unnecessary files to the builder.

Problems include:

- Larger build context.
- Slower builds.
- Unnecessary files in images.
- Accidental inclusion of secrets.
- Unnecessary dependency copying.

**Important:** `.dockerignore` prevents matching files from entering the build context. It is not a substitute for secure secret handling.

---

# PART 10: DOCKER VOLUMES AND STORAGE

# 19. Understanding Container Storage

By default, files written inside a container's writable layer are tied to that container.

Suppose you run MySQL inside a container.

```text
MySQL Container
      |
      v
Container Filesystem
      |
      v
Database Files
```

If you remove the container, its writable-layer data is removed with it.

This creates a problem for databases.

You need persistent storage.

Docker provides:

1. Volumes.
2. Bind Mounts.
3. tmpfs Mounts.

---

# 20. Docker Volumes

A Docker Volume is a storage mechanism managed by Docker.

Volumes are commonly used for:

- Database files.
- Uploaded files.
- Persistent application data.
- Shared data between containers.

### Create a Volume

```bash
docker volume create mysql-data
```

### List Volumes

```bash
docker volume ls
```

### Inspect Volume

```bash
docker volume inspect mysql-data
```

### Use Volume

```bash
docker run -d \
--name mysql-db \
-e MYSQL_ROOT_PASSWORD=example-password \
-v mysql-data:/var/lib/mysql \
mysql:8.4
```

Here:

```text
mysql-data
```

is the Docker volume.

```text
/var/lib/mysql
```

is the MySQL data directory inside the container.

The volume preserves database data when the container is removed and recreated.

**Security note:** The password above is only an illustrative example. Do not use hardcoded production credentials.

### Remove Volume

```bash
docker volume rm mysql-data
```

This removes the volume and its data.

Be careful: deleting a database volume can permanently destroy stored data.

---

# 21. Bind Mounts

A bind mount connects a directory or file on your host machine to a path inside the container.

Example:

```bash
docker run -d \
--name node-app \
-p 3000:3000 \
-v ./src:/app/src \
my-node-app:1.0
```

This maps:

```text
Host:
./src

Container:
/app/src
```

Changes made to the host directory can be reflected inside the container.

### Development Use Case

```text
Developer Laptop
      |
      v
Project Folder
      |
      | Bind Mount
      |
      v
Node.js Container
```

You can edit source code on your laptop while the application runs inside a container.

## 21.1 Volume vs Bind Mount

| Feature | Docker Volume | Bind Mount |
|---|---|---|
| Managed by | Docker | Host filesystem |
| Host path | Docker-managed location | Explicit host path |
| Common use | Persistent application data | Development source files |
| Portability | Easier to manage through Docker | Depends on host directory structure |
| Typical example | MySQL data | Node.js source code |

---

# 22. tmpfs Mounts

A tmpfs mount stores data in memory rather than persistent disk storage.

Example:

```bash
docker run -d \
--name temporary-app \
--tmpfs /app/temp \
my-node-app:1.0
```

Use cases:

- Temporary files.
- Sensitive temporary processing data.
- Data that does not need persistence.

Data in tmpfs is not retained after the container stops or is removed.

### Storage Revision

| Storage Type | Persistence | Managed By |
|---|---|---|
| Container writable layer | Container lifetime | Docker |
| Volume | Independent of container lifecycle | Docker |
| Bind Mount | Host filesystem lifetime | Host |
| tmpfs | Temporary memory storage | Host kernel |

---

# PART 11: DOCKER NETWORKING

# 23. What Is Docker Networking?

Docker networking allows containers to communicate with:

- Other containers.
- The host machine.
- External networks.
- Services running outside Docker.

Example:

```text
Browser
   |
   v
Host Machine
   |
   v
Docker Network
   |
   +-----------+
   |           |
   v           v
Node.js      MySQL
Container   Container
```

Docker provides several network drivers.

Important ones include:

1. Bridge.
2. Host.
3. None.
4. Overlay.
5. Macvlan.

---

# 24. Bridge Network

Bridge is commonly used for standalone containers on a single Docker host.

Example:

```bash
docker network create app-network
```

Run a container:

```bash
docker run -d \
--name node-app \
--network app-network \
my-node-app:1.0
```

Run another container:

```bash
docker run -d \
--name redis \
--network app-network \
redis:7
```

Containers connected to the same user-defined bridge network can communicate using container names as DNS names.

For example, the Node.js application can connect to:

```text
redis:6379
```

### Architecture

```text
            app-network
                 |
        +--------+--------+
        |                 |
        v                 v
    node-app             redis
    Container           Container
        |                 |
        +-----------------+
```

This is especially useful for application and database containers.

---

# 25. Host Network

Host networking allows a container to use the host's network namespace on supported Linux configurations.

Example:

```bash
docker run --network host nginx
```

In this mode, the container shares the host network stack.

Port publishing is not used in the same way as bridge networking.

**Important:** Docker Desktop on Windows and macOS does not provide exactly the same host-network behavior as native Linux Docker Engine. Verify platform-specific support before relying on it.

---

# 26. None Network

The `none` network disables normal external container networking.

Example:

```bash
docker run --network none alpine
```

The container has only limited local networking, including its loopback interface.

Useful for isolated workloads that do not require network communication.

---

# 27. Overlay Network

Overlay networking connects containers across multiple Docker hosts.

It is commonly associated with Docker Swarm.

Example architecture:

```text
             Overlay Network
                    |
        +-----------+-----------+
        |                       |
        v                       v
    Docker Host A           Docker Host B
        |                       |
        v                       v
    Container A             Container B
```

This enables communication across different Docker hosts through the overlay network.

---

# 28. Docker Network Commands

Create a network:

```bash
docker network create app-network
```

List networks:

```bash
docker network ls
```

Inspect network:

```bash
docker network inspect app-network
```

Connect container:

```bash
docker network connect app-network node-app
```

Disconnect container:

```bash
docker network disconnect app-network node-app
```

Remove network:

```bash
docker network rm app-network
```

### Network Driver Comparison

| Driver | Purpose |
|---|---|
| Bridge | Communication between containers on one Docker host |
| Host | Share host network namespace |
| None | Disable normal container networking |
| Overlay | Connect containers across Docker hosts |
| Macvlan | Give containers addresses on a physical network |

---

# PART 12: DOCKER COMPOSE

# 29. What Is Docker Compose?

Docker Compose is a tool for defining and managing multi-container applications.

Suppose your application requires:

- Next.js frontend.
- Node.js backend.
- MySQL database.
- Redis cache.

Without Docker Compose, you would need to run several long Docker commands and manage the network, volumes and environment variables separately.

Docker Compose allows you to define the complete application in a YAML file.

Example:

```text
Full-Stack Application
        |
        v
   Docker Compose
        |
   +----+----+----+
   |    |    |    |
   v    v    v    v
 Next  Node MySQL Redis
```

You can start the services with one command:

```bash
docker compose up -d
```

And stop them with:

```bash
docker compose down
```

Official documentation: [Docker Compose](https://docs.docker.com/compose/)

---

# 30. Docker Compose File

Docker Compose configuration is commonly stored in:

```text
compose.yaml
```

The older filename `docker-compose.yml` is also widely used.

Example:

```yaml
services:

  backend:
    image: node:22-alpine
    container_name: backend
    working_dir: /app
    volumes:
      - ./backend:/app
    command: npm start
    ports:
      - "3000:3000"
```

This defines one service named `backend`.

### Important Compose Concepts

| Property | Purpose |
|---|---|
| `services` | Defines application services |
| `image` | Image to use |
| `build` | Build image from source |
| `container_name` | Explicit container name |
| `ports` | Publish ports |
| `volumes` | Mount storage |
| `environment` | Set environment variables |
| `env_file` | Load environment variables from a file |
| `networks` | Configure networking |
| `depends_on` | Define service dependencies |
| `healthcheck` | Define container health checks |
| `restart` | Configure restart behavior |
| `command` | Override the default command |

---

# 31. Docker Compose Commands

Start services:

```bash
docker compose up
```

Start in background:

```bash
docker compose up -d
```

Build and start:

```bash
docker compose up --build
```

Stop services:

```bash
docker compose stop
```

Stop and remove Compose containers and networks:

```bash
docker compose down
```

View running services:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs
```

Follow logs:

```bash
docker compose logs -f
```

View a specific service:

```bash
docker compose logs -f backend
```

Build services:

```bash
docker compose build
```

Restart services:

```bash
docker compose restart
```

Execute command inside service:

```bash
docker compose exec backend sh
```

Validate and render Compose configuration:

```bash
docker compose config
```

---

# PART 13: FULL-STACK APPLICATION USING DOCKER COMPOSE

# 32. Create Node.js + MySQL Application

Now let's build a practical project.

We will run:

1. Node.js Express backend.
2. MySQL database.
3. Docker network.
4. Persistent MySQL volume.

### Project Structure

```text
docker-fullstack/
│
├── backend/
│   ├── package.json
│   ├── package-lock.json
│   ├── server.js
│   └── Dockerfile
│
├── compose.yaml
└── .env
```

## 32.1 Backend package.json

```json
{
  "name": "docker-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^5.1.0",
    "mysql2": "^3.14.0"
  }
}
```

Install dependencies and generate a lockfile:

```bash
npm install
```

## 32.2 Backend server.js

```javascript
const express = require("express");
const mysql = require("mysql2/promise");

const app = express();

app.use(express.json());

const PORT = process.env.PORT || 3000;

const pool = mysql.createPool({
    host: process.env.DB_HOST || "db",
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    waitForConnections: true,
    connectionLimit: 10
});

app.get("/", (req, res) => {
    res.json({
        message: "Docker Backend Running"
    });
});

app.get("/health", async (req, res) => {
    try {
        await pool.query("SELECT 1");

        res.status(200).json({
            status: "healthy",
            database: "connected"
        });
    } catch (error) {
        res.status(503).json({
            status: "unhealthy",
            database: "disconnected"
        });
    }
});

app.get("/users", async (req, res) => {
    try {
        const [rows] = await pool.query(
            "SELECT * FROM users"
        );

        res.json(rows);
    } catch (error) {
        console.error(error);

        res.status(500).json({
            message: "Database error"
        });
    }
});

app.listen(PORT, "0.0.0.0", () => {
    console.log(`Server running on port ${PORT}`);
});
```

## 32.3 Backend Dockerfile

Create:

```text
backend/Dockerfile
```

Add:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY --chown=node:node . .

ENV NODE_ENV=production

USER node

EXPOSE 3000

CMD ["node", "server.js"]
```

This example:

- Uses Node.js 22.
- Installs production dependencies.
- Runs as a non-root user.
- Exposes the application's intended port.

## 32.4 Environment File

Create:

```text
.env
```

Example:

```env
DB_HOST=db
DB_USER=appuser
DB_PASSWORD=change-this-password
DB_NAME=appdb
MYSQL_ROOT_PASSWORD=change-this-root-password
```

These are development placeholders.

Do not commit this file to Git.

Add it to `.gitignore`:

```text
.env
.env.*
```

You should also add `.env` to `.dockerignore` when building the backend image.

## 32.5 Create compose.yaml

```yaml
services:

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile

    container_name: node-backend

    ports:
      - "3000:3000"

    environment:
      PORT: 3000
      DB_HOST: db
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_NAME: ${DB_NAME}

    depends_on:
      db:
        condition: service_healthy

    restart: unless-stopped

    networks:
      - app-network

  db:
    image: mysql:8.4

    container_name: mysql-database

    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${DB_NAME}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASSWORD}

    volumes:
      - mysql-data:/var/lib/mysql

    healthcheck:
      test:
        - CMD-SHELL
        - mysqladmin ping -h localhost -uroot -p"$${MYSQL_ROOT_PASSWORD}" --silent
      interval: 10s
      timeout: 5s
      retries: 10
      start_period: 30s

    restart: unless-stopped

    networks:
      - app-network

volumes:
  mysql-data:

networks:
  app-network:
    driver: bridge
```

### Understanding This Architecture

```text
               Docker Compose
                     |
             app-network
                     |
          +----------+----------+
          |                     |
          v                     v
     Node Backend           MySQL Database
       Port 3000               Port 3306
          |                     |
          |                     v
          |                 mysql-data
          |
          v
     Host Port 3000
          |
          v
        Browser
```

### Important Concepts

**1. Service name as hostname**

The backend connects to MySQL using:

```env
DB_HOST=db
```

Why?

Because `db` is the Compose service name.

Docker Compose provides service discovery on its default or explicitly configured networks.

**2. MySQL port**

The MySQL container listens internally on:

```text
3306
```

We have not published port 3306 to the host.

Therefore, the database is reachable by services on `app-network`, but not through a host-published MySQL port.

**3. Persistent volume**

```yaml
volumes:
  - mysql-data:/var/lib/mysql
```

This preserves database files beyond the lifecycle of the container.

**4. Health check**

The database health check verifies that MySQL is responding.

**5. depends_on**

```yaml
depends_on:
  db:
    condition: service_healthy
```

This delays starting the backend until the database passes its configured health check.

It does not guarantee that the database will remain healthy forever. The application should still handle connection failures and retry where appropriate.

### Step 1: Start Application

From the project root:

```bash
docker compose up -d --build
```

### Step 2: Check Containers

```bash
docker compose ps
```

### Step 3: Check Logs

```bash
docker compose logs -f
```

### Step 4: Test Backend

Open:

```text
http://localhost:3000
```

Expected response:

```json
{
    "message": "Docker Backend Running"
}
```

### Step 5: Test Health Endpoint

```text
http://localhost:3000/health
```

### Step 6: Stop Containers

```bash
docker compose down
```

### Step 7: Start Again

```bash
docker compose up -d
```

Your MySQL data should remain available because it is stored in a named volume.

**Warning:** Do not run `docker compose down -v` unless you intentionally want to remove the Compose-managed volumes and their stored data.

---

# PART 14: DOCKERIZING NEXT.JS

# 33. Why Dockerize a Next.js Application?

A Next.js application may run in different environments:

- Local development.
- VPS.
- AWS EC2.
- Container platforms.
- Kubernetes clusters.

Docker helps package the application with its runtime and production dependencies.

### Basic Architecture

```text
User
 |
 v
Nginx / Load Balancer
 |
 v
Next.js Container
 |
 v
Backend API
```

For this example, we will create a production-oriented Next.js image using multi-stage builds.

## 33.1 Next.js Configuration

If you are using Next.js, enable standalone output.

In `next.config.js`:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
    output: "standalone"
};

module.exports = nextConfig;
```

If you use `next.config.ts` or another supported configuration format, make the equivalent change there.

Standalone output creates a smaller deployable server bundle containing the required runtime files and traced dependencies.

## 33.2 Next.js Dockerfile

```dockerfile
FROM node:22-alpine AS base

WORKDIR /app


# Dependency installation stage
FROM base AS deps

COPY package.json package-lock.json ./

RUN npm ci


# Build stage
FROM base AS builder

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules

COPY . .

ENV NEXT_TELEMETRY_DISABLED=1

RUN npm run build


# Production stage
FROM base AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

RUN addgroup --system --gid 1001 nodejs

RUN adduser --system --uid 1001 nextjs

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

### Important

This example assumes:

- You have a `package-lock.json`.
- Your Next.js configuration enables standalone output.
- Your project has the expected `public` directory.
- Your application builds successfully using the selected Node.js version.

If your project does not contain a `public` directory, adjust the Dockerfile accordingly.

## 33.3 Next.js .dockerignore

```text
node_modules
.next
.git
.env
.env.*
npm-debug.log
Dockerfile
compose.yaml
```

Be careful not to exclude files needed during the build.

## 33.4 Build Image

```bash
docker build -t nextjs-app:1.0 .
```

## 33.5 Run Container

```bash
docker run -d \
--name nextjs-container \
-p 3000:3000 \
nextjs-app:1.0
```

Open:

```text
http://localhost:3000
```

### Important Next.js Environment Variable Concept

There is an important distinction between:

**Build-time environment variables**

Some Next.js configuration and client-side variables are embedded into the generated build.

For example:

```env
NEXT_PUBLIC_API_URL=https://api.example.com
```

`NEXT_PUBLIC_` variables referenced in browser-side code are generally inlined during the build.

Changing the runtime environment variable after building does not automatically change those embedded browser-side values.

**Runtime environment variables**

Server-side variables can be supplied when the container starts, provided your application reads them at runtime.

Example:

```bash
docker run -d \
-e DATABASE_URL="..." \
-p 3000:3000 \
nextjs-app:1.0
```

Do not expose database credentials through `NEXT_PUBLIC_` variables.

---

# PART 15: DOCKERIZING MONGODB

# 34. Run MongoDB Using Docker

You can run MongoDB without installing the database directly on your operating system.

Example:

```bash
docker run -d \
--name mongodb \
-p 27017:27017 \
-v mongodb-data:/data/db \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=change-this-password \
mongo:8
```

This creates a MongoDB container.

### Explanation

| Configuration | Purpose |
|---|---|
| `-d` | Background execution |
| `--name mongodb` | Container name |
| `-p 27017:27017` | Publish MongoDB port |
| `-v mongodb-data:/data/db` | Persistent database storage |
| `MONGO_INITDB_ROOT_USERNAME` | Initial root username |
| `MONGO_INITDB_ROOT_PASSWORD` | Initial root password |

### Connect Using MongoDB Shell

If `mongosh` is installed on your host:

```bash
mongosh "mongodb://admin:change-this-password@localhost:27017/admin?authSource=admin"
```

For production, use a secure password and avoid exposing MongoDB publicly.

### Important

MongoDB initialization environment variables are generally applied when initializing an empty database directory.

Changing the environment variables later does not automatically update existing database credentials.

---

# PART 16: MULTI-STAGE BUILDS

# 35. What Is a Multi-Stage Build?

A multi-stage build allows you to use multiple `FROM` instructions in a single Dockerfile.

The purpose is to separate:

- Build dependencies.
- Development tools.
- Application compilation.
- Final production runtime.

Example:

```dockerfile
FROM node:22-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build


FROM node:22-alpine AS runner

WORKDIR /app

COPY --from=builder /app/dist ./dist

COPY --from=builder /app/package*.json ./

RUN npm ci --omit=dev

CMD ["node", "dist/server.js"]
```

### Architecture

```text
             Dockerfile
                 |
         +-------+-------+
         |               |
         v               v
    Build Stage      Runtime Stage
         |               |
         v               v
    Dev Dependencies  Production
    Source Code       Dependencies
    Build Tools       Built Files
         |               |
         +-------+-------+
                 |
                 v
          Final Docker Image
```

Only the files explicitly copied into the final stage are included from earlier stages.

### Benefits

- Smaller production images.
- Reduced attack surface.
- No unnecessary development dependencies.
- Better separation of build and runtime environments.
- Cleaner production deployment.

Docker recommends multi-stage builds for separating build environments from production output. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

---

# PART 17: ENVIRONMENT VARIABLES AND SECRETS

# 36. Environment Variables in Docker

Environment variables allow you to configure containers without hardcoding configuration values into application code.

Example:

```javascript
const PORT = process.env.PORT || 3000;

const DB_HOST = process.env.DB_HOST;
```

You can provide variables when starting a container.

```bash
docker run -d \
-e PORT=3000 \
-e DB_HOST=db \
my-node-app
```

## 36.1 Using an Environment File

Create:

```text
.env
```

Example:

```env
PORT=3000
DB_HOST=db
DB_NAME=appdb
```

Run:

```bash
docker run --env-file .env my-node-app
```

### Important Security Difference

Environment variables are convenient, but they are not a complete secrets-management solution.

Depending on the environment, secrets may be exposed through:

- Container inspection.
- Process environments.
- Application logs.
- Diagnostic tools.
- Configuration files.
- Build metadata.

Do not assume that placing a password in `.env` automatically makes it secure.

---

# 37. Docker Secrets

Docker Compose supports secrets for passing sensitive data to services.

Example:

Create:

```text
secrets/db_password.txt
```

Store the password inside this file.

Then configure:

```yaml
services:

  backend:
    image: my-node-app:1.0

    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

Inside the container, the secret is mounted at a path similar to:

```text
/run/secrets/db_password
```

Your application can read it from that file.

Example:

```javascript
const fs = require("fs");

const dbPassword = fs.readFileSync(
    "/run/secrets/db_password",
    "utf8"
).trim();
```

### Important Distinction

Compose file-backed secrets are mounted into containers as files, but they are not equivalent to the encrypted secret-management features of every orchestration platform.

For production, consider:

- AWS Secrets Manager.
- AWS Systems Manager Parameter Store.
- A managed secret-management system.
- Platform-specific secret-management facilities.

Never commit secret files to Git.

---

# PART 18: HEALTH CHECKS AND RESTART POLICIES

# 38. What Is a Docker Health Check?

A health check verifies whether an application inside a container is functioning correctly.

A container can be running but its application may be unhealthy.

Example:

```text
Container Status: Running
Application Status: Not Responding
```

Docker health checks help distinguish these states.

### Dockerfile Health Check Example

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

EXPOSE 3000

HEALTHCHECK \
--interval=30s \
--timeout=5s \
--start-period=10s \
--retries=3 \
CMD node -e "fetch('http://127.0.0.1:3000/health').then(r => process.exit(r.ok ? 0 : 1)).catch(() => process.exit(1))"

CMD ["npm", "start"]
```

The health check requests:

```text
http://127.0.0.1:3000/health
```

If it receives a successful response, the check succeeds.

### Health Check Parameters

| Parameter | Purpose |
|---|---|
| `--interval` | Time between checks |
| `--timeout` | Maximum time for one check |
| `--start-period` | Initial startup grace period |
| `--retries` | Consecutive failures before unhealthy status |

Check health:

```bash
docker ps
```

Inspect health details:

```bash
docker inspect node-container
```

**Important:** A health check reports container health. It does not automatically restart every unhealthy container. Restart policies and orchestrator behavior must be configured separately.

---

# 39. Docker Restart Policies

Docker supports restart policies.

| Policy | Meaning |
|---|---|
| `no` | Do not automatically restart |
| `always` | Always restart according to the policy |
| `unless-stopped` | Restart unless explicitly stopped |
| `on-failure` | Restart when the container exits with a non-zero status |

Example:

```bash
docker run -d \
--restart unless-stopped \
--name node-app \
my-node-app
```

Compose example:

```yaml
services:
  backend:
    image: my-node-app
    restart: unless-stopped
```

Restart policies are useful for handling unexpected process exits.

---

# PART 19: DOCKER RESOURCE MANAGEMENT

# 40. CPU and Memory Limits

Containers can consume host resources.

Suppose you have multiple Node.js containers.

Without resource controls, one application may consume too much memory or CPU.

You can configure limits.

### Memory Limit

```bash
docker run -d \
--memory=512m \
nginx
```

This sets a memory limit of 512 MB.

### CPU Limit

```bash
docker run -d \
--cpus=1.5 \
nginx
```

This limits the container to a CPU quota equivalent to 1.5 CPUs.

### Combined Example

```bash
docker run -d \
--name limited-app \
--memory=512m \
--cpus=1.5 \
my-node-app
```

### Docker Compose Example

```yaml
services:
  backend:
    image: my-node-app
    mem_limit: 512m
    cpus: 1.5
```

Verify the current Compose specification and implementation support when configuring deployment limits.

---

# PART 20: DOCKER LOGGING AND TROUBLESHOOTING

# 41. Docker Logs

Docker captures the output of the container's main process.

Example:

```bash
docker logs node-app
```

Follow logs:

```bash
docker logs -f node-app
```

Show the last 100 lines:

```bash
docker logs --tail 100 node-app
```

Show timestamps:

```bash
docker logs -t node-app
```

### Best Practice

Applications running in containers should generally send operational logs to standard output and errors to standard error.

For example:

```javascript
console.log("Application started");

console.error("Database connection failed");
```

This makes logs accessible through Docker's logging system.

---

# 42. Docker Logging Drivers

Docker supports multiple logging drivers.

Examples:

- `json-file`.
- `local`.
- `journald`.
- `syslog`.
- `fluentd`.
- `awslogs`.

The appropriate driver depends on your logging architecture.

For example, an AWS deployment may send logs to CloudWatch using a suitable logging configuration.

### Logging Example

```bash
docker run -d \
--log-driver=json-file \
--name node-app \
my-node-app
```

For production, configure log rotation and centralized collection.

---

# 43. Common Docker Errors

## Error 1: Port Already Allocated

Example:

```text
Bind for 0.0.0.0:3000 failed:
port is already allocated
```

Meaning:

Another process or container is already using the host port.

Solution:

Use another host port:

```bash
docker run -p 3001:3000 my-node-app
```

## Error 2: Container Exits Immediately

Possible causes:

- Application error.
- Incorrect startup command.
- Missing dependencies.
- Incorrect environment variables.

Check:

```bash
docker logs container-name
```

## Error 3: Cannot Connect to Database

Possible causes:

- Incorrect database hostname.
- Wrong credentials.
- Database not ready.
- Incorrect Docker network.
- Database not listening on expected port.

Check:

```bash
docker network ls
```

And:

```bash
docker inspect container-name
```

## Error 4: Image Build Failed

Check:

- Dockerfile syntax.
- File paths.
- Build context.
- Package installation errors.
- Network connectivity.
- Dependency lockfiles.

## Error 5: Container Cannot Access Host Files

Possible causes:

- Incorrect bind mount path.
- Incorrect file permissions.
- Wrong working directory.
- Incorrect Docker Desktop file-sharing configuration.

---

# PART 21: DOCKER SECURITY BEST PRACTICES

# 44. Docker Security

Docker improves application isolation, but containers are not automatically secure.

A poorly configured container can still create security risks.

Follow these practices.

## 44.1 Use Trusted Base Images

Prefer:

- Docker Official Images.
- Verified Publisher images.
- Trusted organization-maintained images.

Avoid downloading arbitrary images from unknown publishers.

## 44.2 Use Specific Image Versions

Avoid:

```dockerfile
FROM node:latest
```

Prefer:

```dockerfile
FROM node:22-alpine
```

For stronger reproducibility, pin an image digest when appropriate.

## 44.3 Run Containers as Non-Root

Avoid running application processes as root unless there is a justified requirement.

Example:

```dockerfile
USER node
```

## 44.4 Avoid Storing Secrets in Images

Do not do this:

```dockerfile
ENV DB_PASSWORD=my-password
```

Also avoid:

```dockerfile
COPY .env .
```

Use an appropriate runtime secret-management mechanism.

## 44.5 Use Read-Only Filesystems Where Possible

Example:

```bash
docker run --read-only nginx
```

Some applications need writable temporary directories. Configure only the necessary writable mounts.

## 44.6 Drop Unnecessary Linux Capabilities

Example:

```bash
docker run \
--cap-drop=ALL \
nginx
```

Add back only capabilities required by the application.

## 44.7 Avoid Privileged Containers

Avoid:

```bash
docker run --privileged
```

unless there is a specific and carefully reviewed need.

## 44.8 Scan Images for Vulnerabilities

Useful tools include:

- Docker Scout.
- Trivy.
- Grype.
- Snyk Container.

## 44.9 Protect the Docker Socket

The Docker socket provides powerful control over the Docker daemon.

Do not mount:

```text
/var/run/docker.sock
```

into untrusted containers.

Access to the Docker daemon can effectively provide control over the host.

## 44.10 Keep Docker Updated

Regularly update:

- Docker Engine.
- Docker Desktop.
- Base images.
- Application dependencies.
- Container runtime components.

Docker's official build guidance also recommends trusted base images, smaller images and multi-stage builds. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

---

# PART 22: DOCKER IN PRODUCTION

# 45. Development vs Production Docker Setup

The requirements for development and production environments are different.

| Area | Development | Production |
|---|---|---|
| Source code | Bind mounted | Usually packaged into image |
| Hot reload | Common | Usually disabled |
| Dependencies | Development dependencies included | Production dependencies |
| Logging | Local Docker logs | Centralized logging |
| Secrets | Local development configuration | Secret-management service |
| Image | Convenient development image | Optimized production image |
| User | May use development user | Prefer non-root |
| Health checks | Useful | Important |
| Resource limits | Optional | Carefully configured |
| Deployment | Local Compose | Managed deployment/orchestration |

## 45.1 Production Deployment Architecture

Consider your Next.js and Node.js application.

```text
                  INTERNET
                     |
                     v
                 Route 53
                     |
                     v
               Load Balancer
                     |
                     v
                 EC2 Server
                     |
                 Docker Engine
                     |
          +----------+----------+
          |                     |
          v                     v
    Next.js Container     Node.js Container
          |                     |
          +----------+----------+
                     |
                     v
                 MySQL RDS
```

In a production AWS environment:

- Use an Application Load Balancer.
- Run containers on suitable compute infrastructure.
- Keep application containers in private subnets where possible.
- Use RDS for managed database services when appropriate.
- Use CloudWatch for monitoring.
- Store images in Amazon ECR.
- Use AWS Secrets Manager or another suitable secrets service.
- Use a suitable orchestration platform when the deployment requires it.

---

# PART 23: DOCKER WITH NGINX

# 46. What Is Nginx?

Nginx is a web server and reverse proxy.

It can:

- Serve static files.
- Forward requests to backend services.
- Handle TLS termination.
- Route traffic.
- Perform load balancing.

You can run Nginx inside a Docker container.

### Architecture

```text
User Browser
     |
     v
Nginx Container
     |
     v
Node.js Container
     |
     v
MySQL Container
```

## 46.1 Docker Compose Example

```yaml
services:

  nginx:
    image: nginx:stable-alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - backend
    networks:
      - app-network

  backend:
    build: ./backend
    expose:
      - "3000"
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```

### Nginx Configuration

Create:

```text
nginx/default.conf
```

Add:

```nginx
server {
    listen 80;

    server_name localhost;

    location / {
        proxy_pass http://backend:3000;

        proxy_set_header Host $host;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header X-Forwarded-For
            $proxy_add_x_forwarded_for;

        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Request Flow

```text
Browser
   |
   | HTTP :80
   |
   v
Nginx Container
   |
   | Proxy Request
   |
   v
Backend Container
   |
   v
Express Application
```

Notice that the backend does not need a host-published port in this example.

Nginx can reach it through the shared Docker network.

---

# PART 24: DOCKER IMAGE REGISTRIES AND DEPLOYMENT

# 47. Push Your Docker Image to Docker Hub

Suppose you built:

```text
my-node-app:1.0
```

You want to publish it.

### Step 1: Create Docker Hub Repository

Create a repository on:

[Docker Hub](https://hub.docker.com/)

Example username:

```text
myusername
```

Repository:

```text
my-node-app
```

### Step 2: Tag Image

```bash
docker tag my-node-app:1.0 myusername/my-node-app:1.0
```

### Step 3: Authenticate

```bash
docker login
```

Use a suitable access token or credential mechanism.

### Step 4: Push Image

```bash
docker push myusername/my-node-app:1.0
```

### Step 5: Pull Image on Another Server

```bash
docker pull myusername/my-node-app:1.0
```

### Step 6: Run It

```bash
docker run -d \
--name production-app \
-p 3000:3000 \
myusername/my-node-app:1.0
```

Now the application can run on another compatible Docker host without manually installing its Node.js dependencies on that host.

---

# 48. Amazon Elastic Container Registry (ECR)

Amazon ECR is AWS's managed container image registry.

It allows you to:

- Store Docker images.
- Manage private repositories.
- Integrate with AWS IAM.
- Scan images for vulnerabilities.
- Integrate with ECS, EKS and other deployment workflows.

### ECR Architecture

```text
Developer Machine
       |
       v
Docker Build
       |
       v
Amazon ECR
       |
       v
EC2 / ECS / EKS
       |
       v
Running Container
```

### Typical Workflow

```text
1. Build Docker Image
2. Authenticate to ECR
3. Tag Image
4. Push Image
5. Pull Image on Deployment Environment
6. Start Container
```

Official documentation:

[Amazon ECR User Guide](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

---

# PART 25: DOCKER AND CI/CD

# 49. What Is CI/CD?

CI/CD stands for:

- Continuous Integration.
- Continuous Delivery or Continuous Deployment.

Docker images fit naturally into CI/CD workflows.

### Example Workflow

```text
Developer
    |
    v
Git Push
    |
    v
GitHub Repository
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
Push Image to Registry
    |
    v
Deploy to Server
```

## 49.1 Basic GitHub Actions Docker Build

Create:

```text
.github/workflows/docker-build.yml
```

Example:

```yaml
name: Docker Build

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build Docker Image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: false
          tags: my-app:latest
```

This is a basic image-build workflow.

For production, you would add:

- Automated tests.
- Dependency scanning.
- Image vulnerability scanning.
- Registry authentication.
- Versioned image tags.
- Deployment approvals.
- Rollback strategy.

Pin third-party GitHub Actions to reviewed immutable commit SHAs in security-sensitive production workflows.

---

# PART 26: DOCKER VS DOCKER COMPOSE VS KUBERNETES

# 50. Understanding the Differences

These technologies are related but solve different problems.

## Docker

Docker provides tools for building, distributing and running containers.

## Docker Compose

Docker Compose manages multi-container applications, commonly on one Docker host.

## Kubernetes

Kubernetes is a container orchestration platform that manages containerized workloads across a cluster.

### Comparison

| Feature | Docker Engine | Docker Compose | Kubernetes |
|---|---|---|---|
| Primary Purpose | Run containers | Manage multi-container apps | Orchestrate containers across clusters |
| Image Building | Yes, with BuildKit/Buildx | Uses images/build configuration | Usually uses prebuilt images |
| Multi-Container Apps | Possible manually | Yes | Yes |
| Cluster Management | Not its main purpose | No | Yes |
| Auto Scaling | Limited without orchestration | Not a general cluster autoscaler | Yes |
| Self-Healing | Basic restart mechanisms | Basic restart mechanisms | Advanced workload management |
| Service Discovery | Docker networking | Compose networking | Kubernetes Services and DNS |
| Typical Use | Container runtime | Local development and simpler deployments | Large-scale orchestration |

**Important:** Docker Compose is not a replacement for Kubernetes in large distributed production environments. However, many applications can run successfully with Compose on a single server.

---

# PART 27: COMPLETE DOCKER PROJECT

# 51. Build a Full-Stack Containerized Application

Now combine everything you have learned.

### Project Requirements

Create an application using:

- Next.js frontend.
- Node.js Express backend.
- MySQL database.
- Redis cache.
- Nginx reverse proxy.
- Docker Compose.

### Architecture

```text
                     USER
                       |
                       v
                    NGINX
                       |
              +--------+--------+
              |                 |
              v                 v
          Next.js            Express
          Frontend           Backend
                                |
                      +---------+---------+
                      |                   |
                      v                   v
                    MySQL               Redis
                   Database             Cache
```

### Suggested Project Structure

```text
fullstack-docker-project/
│
├── frontend/
│   ├── app/
│   ├── package.json
│   └── Dockerfile
│
├── backend/
│   ├── routes/
│   ├── controllers/
│   ├── models/
│   ├── package.json
│   └── Dockerfile
│
├── nginx/
│   └── default.conf
│
├── compose.yaml
├── .env
├── .dockerignore
└── .gitignore
```

### Required Features

1. Next.js frontend.
2. Express REST API.
3. MySQL database.
4. Redis caching.
5. Nginx reverse proxy.
6. Dockerfiles for frontend and backend.
7. Docker Compose configuration.
8. Persistent database volume.
9. Internal Docker networking.
10. Health checks.
11. Environment variables.
12. Non-root application users.
13. Production-oriented multi-stage builds.
14. Application logging.

### Expected Result

You should be able to start your complete application with:

```bash
docker compose up -d --build
```

And stop it with:

```bash
docker compose down
```

This project is particularly valuable because it combines full-stack development with DevOps deployment practices.

---

# PART 28: COMMON DOCKER MISTAKES

# 52. Mistakes to Avoid

| Mistake | Problem | Better Approach |
|---|---|---|
| Using `latest` everywhere | Unpredictable image changes | Pin versions or digests |
| Running everything as root | Increases security risk | Use non-root users |
| Storing secrets in Dockerfile | Secrets may leak into image layers or metadata | Use runtime secrets |
| No `.dockerignore` | Unnecessary files enter build context | Add `.dockerignore` |
| Installing dependencies after copying all source | Poor build caching | Copy package files first |
| Not using persistent volumes | Database data may disappear with container | Use named volumes |
| Exposing every port | Unnecessary network exposure | Publish only required ports |
| Connecting to `localhost` for another container | Refers to the current container | Use service name |
| No health checks | Harder to identify application health | Configure health checks |
| Ignoring logs | Difficult troubleshooting | Use standard output/error |
| Large production images | Slower deployments and larger attack surface | Use multi-stage builds |
| Using privileged containers | Excessive host access | Avoid privileged mode |
| Publishing database ports unnecessarily | Increases exposure | Use internal networks |
| Using `docker compose down -v` carelessly | Deletes named volumes | Preserve volumes unless intentionally removing data |

---

# PART 29: INTERVIEW QUESTIONS AND ANSWERS

# 53. Basic Interview Questions

### Q1. What is Docker?

Docker is a containerization platform used to build, package, distribute and run applications in isolated containers.

### Q2. What is a container?

A container is an isolated runtime instance created from an image.

### Q3. What is a Docker image?

A Docker image is a read-only template used to create containers.

### Q4. What is Dockerfile?

A Dockerfile is a text file containing instructions for building a Docker image.

### Q5. What is Docker Hub?

Docker Hub is a container image registry where users can publish, store and download images.

### Q6. What is Docker Compose?

Docker Compose is a tool for defining and managing multi-container applications using YAML configuration.

### Q7. What is a Docker volume?

A Docker volume is Docker-managed persistent storage that can exist independently of a container.

### Q8. What is a Docker network?

A Docker network allows containers to communicate with other containers and external systems.

### Q9. What is the purpose of EXPOSE?

`EXPOSE` documents the port an application is expected to listen on. It does not publish that port to the host.

### Q10. What is the difference between CMD and ENTRYPOINT?

`ENTRYPOINT` defines the main executable, while `CMD` provides default arguments or a default command. Their interaction depends on the Dockerfile form used.

---

# 54. Intermediate Interview Questions

### Q11. What is the difference between an image and a container?

An image is an immutable template. A container is an instance of that image with its own runtime configuration and writable layer.

### Q12. What is the difference between COPY and ADD?

`COPY` copies files from the build context or another build stage.

`ADD` supports additional behavior, such as extracting supported local archives and handling certain remote sources.

Prefer `COPY` for ordinary file copying.

### Q13. What is the difference between RUN and CMD?

`RUN` executes during image building.

`CMD` specifies the default command executed when a container starts.

### Q14. What is a Docker volume?

A Docker volume provides persistent storage managed by Docker.

### Q15. What is a bind mount?

A bind mount maps a specific host filesystem path into a container.

### Q16. What is Docker bridge networking?

Bridge networking allows containers connected to the same bridge network to communicate.

### Q17. Why should we use Docker Compose?

It simplifies multi-container application configuration and lifecycle management.

### Q18. What is a multi-stage build?

A multi-stage build separates build and runtime stages to produce smaller and cleaner final images.

### Q19. What is `.dockerignore`?

It specifies files and directories that should be excluded from the Docker build context.

### Q20. What is a Docker health check?

A health check periodically evaluates whether the application inside a container is functioning as expected.

---

# 55. Advanced Interview Questions

### Q21. How does Docker differ from a virtual machine?

A typical Linux container shares the host kernel and uses OS-level isolation, while a VM runs a separate guest operating system on virtualized hardware.

### Q22. What happens when you execute `docker run nginx`?

Generally:

1. Docker checks whether the image is available locally.
2. It pulls the image if needed.
3. It creates a container.
4. It configures its writable layer.
5. It configures networking.
6. It starts the container's main process.

### Q23. Why should Node.js listen on `0.0.0.0` inside a container?

Because binding to `0.0.0.0` allows the application to accept connections through the container's network interfaces rather than only through its loopback interface.

### Q24. How do containers communicate with each other?

Containers connected to the same suitable Docker network can communicate using container or service DNS names and their internal ports.

### Q25. Why should a database use a Docker volume?

Because container writable-layer data is removed when the container is removed. A volume provides storage independent of the container lifecycle.

### Q26. What is the difference between `docker stop` and `docker rm`?

`docker stop` stops a container.

`docker rm` removes a stopped container.

### Q27. What happens when you execute `docker compose down`?

Compose stops and removes the project's containers and networks. Named volumes are preserved unless you explicitly request their removal.

### Q28. Why are multi-stage builds useful?

They allow build tools and development dependencies to be excluded from the final runtime image.

### Q29. How do you reduce Docker image size?

- Use a suitable small base image.
- Use multi-stage builds.
- Install only production dependencies.
- Use `.dockerignore`.
- Remove unnecessary files.
- Avoid unnecessary packages.

### Q30. How would you deploy a Dockerized Node.js application to AWS?

A typical workflow:

1. Create a Dockerfile.
2. Build the image.
3. Test the container.
4. Push the image to Amazon ECR.
5. Deploy it to an appropriate compute platform.
6. Configure networking and security.
7. Configure health checks.
8. Monitor logs and metrics.
9. Automate future deployments through CI/CD.

---

# PART 30: PRACTICAL ASSIGNMENTS

# 56. Assignment 1 – Docker Installation

Install Docker on your Windows machine.

Verify:

```bash
docker --version
docker compose version
docker info
```

Run:

```bash
docker run hello-world
```

**Expected outcome:** Docker is installed and working.

---

# 57. Assignment 2 – Container Management

Run an Nginx container.

```bash
docker run -d \
--name nginx-test \
-p 8080:80 \
nginx
```

Practise:

- List containers.
- Stop container.
- Start container.
- Restart container.
- View logs.
- Execute commands inside the container.
- Remove container.

**Expected outcome:** Understand container lifecycle management.

---

# 58. Assignment 3 – Build a Node.js Image

Create a simple Express application.

Write:

- `package.json`
- `package-lock.json`
- `server.js`
- `Dockerfile`
- `.dockerignore`

Build:

```bash
docker build -t node-app:1.0 .
```

Run:

```bash
docker run -d -p 3000:3000 node-app:1.0
```

**Expected outcome:** Create and run a custom Docker image.

---

# 59. Assignment 4 – Docker Volumes

Run MySQL with a named volume.

```bash
docker volume create mysql-data
```

Start MySQL using that volume.

Create a database and table.

Insert some records.

Remove the container and recreate it using the same volume.

Verify that the data still exists.

**Expected outcome:** Understand persistent storage.

---

# 60. Assignment 5 – Docker Networking

Create a Docker network.

```bash
docker network create learning-network
```

Run two containers inside it.

Practise communication using container names.

**Expected outcome:** Understand container-to-container communication.

---

# 61. Assignment 6 – Docker Compose

Create a project containing:

```text
Node.js
MySQL
```

Requirements:

- Separate services.
- Shared network.
- MySQL volume.
- Environment variables.
- Health check.
- Restart policy.

Start:

```bash
docker compose up -d --build
```

**Expected outcome:** Run a multi-container application.

---

# 62. Assignment 7 – Dockerize Next.js

Take one of your existing Next.js applications.

Create:

- `Dockerfile`
- `.dockerignore`
- `compose.yaml`

Requirements:

- Production build.
- Standalone output.
- Multi-stage build.
- Non-root runtime user.
- Port configuration.
- Environment variables.

**Expected outcome:** Run a Next.js application inside a production-oriented container.

---

# 63. Assignment 8 – Full-Stack Application

Build:

```text
Next.js
Node.js
Express
MySQL
Redis
Nginx
```

Use Docker Compose to manage all services.

**Expected outcome:** Deploy a complete full-stack application using containers.

---

# 64. Assignment 9 – Docker Hub

Build an image and push it to Docker Hub.

Pull it on another machine or a fresh environment.

Run the application.

**Expected outcome:** Understand image distribution.

---

# 65. Assignment 10 – AWS Container Deployment

Create an AWS account environment suitable for learning.

Practise:

1. Create an ECR repository.
2. Build a Docker image.
3. Authenticate to ECR.
4. Push the image.
5. Deploy it to an appropriate AWS compute service.
6. Configure application networking.
7. Configure logs.
8. Configure health checks.

**Expected outcome:** Understand the Docker-to-AWS deployment lifecycle.

**Cost warning:** AWS resources such as EC2, load balancers, NAT Gateways and container services may incur charges. Review pricing and delete unused resources after your lab.

---

# PART 31: QUICK REVISION

# 66. Important Docker Concepts

| Concept | Meaning |
|---|---|
| Docker | Containerization platform |
| Container | Running or stopped instance of an image |
| Image | Read-only application template |
| Dockerfile | Instructions for building an image |
| Docker Engine | Core container platform |
| Docker Daemon | Background Docker service |
| Docker CLI | Command-line interface |
| containerd | Container lifecycle component |
| runc | Low-level container runtime |
| Docker Hub | Image registry |
| Docker Compose | Multi-container application management |
| Volume | Docker-managed persistent storage |
| Bind Mount | Host directory mounted inside container |
| Bridge | Common single-host network |
| Overlay | Multi-host container network |
| `.dockerignore` | Excludes files from build context |
| CMD | Default container command |
| ENTRYPOINT | Main container executable |
| EXPOSE | Documents intended container port |
| RUN | Executes during image build |
| COPY | Copies files into image |
| Multi-Stage Build | Separates build and runtime stages |
| Health Check | Checks container application health |
| ECR | AWS container image registry |
| Nginx | Web server and reverse proxy |

---

# 67. Important Docker Commands

| Command | Purpose |
|---|---|
| `docker --version` | Check Docker version |
| `docker info` | Docker system information |
| `docker pull` | Download image |
| `docker build` | Build image |
| `docker images` | List images |
| `docker run` | Create and start container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop` | Stop container |
| `docker start` | Start container |
| `docker restart` | Restart container |
| `docker rm` | Remove container |
| `docker rmi` | Remove image |
| `docker logs` | View logs |
| `docker exec` | Execute command inside container |
| `docker inspect` | Inspect object |
| `docker stats` | Resource monitoring |
| `docker volume ls` | List volumes |
| `docker network ls` | List networks |
| `docker compose up -d` | Start Compose application |
| `docker compose down` | Stop and remove Compose containers and networks |
| `docker compose logs` | View Compose logs |
| `docker compose ps` | List Compose services |

---

# 68. Phase 45 Completion Checklist

### Docker Fundamentals

- [ ] Virtualization.
- [ ] Containerization.
- [ ] VM vs Container.
- [ ] Docker Architecture.
- [ ] Docker Engine.
- [ ] Docker Daemon.
- [ ] containerd.
- [ ] runc.
- [ ] OCI.

### Docker Images and Containers

- [ ] Docker Images.
- [ ] Docker Containers.
- [ ] Docker Registries.
- [ ] Docker Hub.
- [ ] Image tags.
- [ ] Docker CLI.
- [ ] Container lifecycle.

### Dockerfile

- [ ] FROM.
- [ ] RUN.
- [ ] COPY.
- [ ] WORKDIR.
- [ ] CMD.
- [ ] ENTRYPOINT.
- [ ] EXPOSE.
- [ ] ENV.
- [ ] ARG.
- [ ] USER.
- [ ] VOLUME.
- [ ] `.dockerignore`.
- [ ] Image layers.
- [ ] Build cache.
- [ ] Multi-stage builds.

### Storage and Networking

- [ ] Docker Volumes.
- [ ] Bind Mounts.
- [ ] tmpfs.
- [ ] Bridge Network.
- [ ] Host Network.
- [ ] None Network.
- [ ] Overlay Network.
- [ ] Docker DNS.
- [ ] Port Mapping.

### Docker Compose

- [ ] Compose YAML.
- [ ] Services.
- [ ] Networks.
- [ ] Volumes.
- [ ] Environment Variables.
- [ ] Health Checks.
- [ ] Restart Policies.
- [ ] Multi-container applications.

### Application Containerization

- [ ] Node.js Application.
- [ ] Express Application.
- [ ] Next.js Application.
- [ ] MySQL.
- [ ] MongoDB.
- [ ] Redis.
- [ ] Nginx.

### Production and DevOps

- [ ] Docker Security.
- [ ] Non-root containers.
- [ ] Resource Limits.
- [ ] Logging.
- [ ] Image Optimization.
- [ ] Docker Hub.
- [ ] Amazon ECR.
- [ ] CI/CD Integration.
- [ ] Production Deployment.

---

# 69. Official Documentation and Learning Resources

| Topic | Official Documentation |
|---|---|
| Docker Overview | [Docker Overview](https://docs.docker.com/get-started/docker-overview/) |
| Docker Installation | [Docker Installation](https://docs.docker.com/engine/install/) |
| Windows Installation | [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/) |
| Dockerfile | [Dockerfile Reference](https://docs.docker.com/reference/dockerfile/) |
| Docker Build | [Docker Build Documentation](https://docs.docker.com/build/) |
| Build Best Practices | [Docker Build Best Practices](https://docs.docker.com/build/building/best-practices/) |
| Docker Volumes | [Docker Volumes](https://docs.docker.com/engine/storage/volumes/) |
| Docker Bind Mounts | [Docker Bind Mounts](https://docs.docker.com/engine/storage/bind-mounts/) |
| Docker Networking | [Docker Networking](https://docs.docker.com/engine/network/) |
| Docker Compose | [Docker Compose](https://docs.docker.com/compose/) |
| Docker Security | [Docker Security](https://docs.docker.com/engine/security/) |
| Docker Hub | [Docker Hub](https://hub.docker.com/) |
| Amazon ECR | [Amazon ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) |
| Next.js Docker Deployment | [Next.js Deployment Documentation](https://nextjs.org/docs/app/getting-started/deploying) |

---

# 70. Final Summary

In Phase 45, you learned how Docker works and how to use it to package and deploy applications.

The most important concept is:

**Docker allows you to package an application and its dependencies into a portable image, then run that image as a container in compatible environments.**

You should now understand this workflow:

```text
        Application Development
                  |
                  v
              Dockerfile
                  |
                  v
             Docker Build
                  |
                  v
              Docker Image
                  |
                  v
             Docker Registry
                  |
                  v
             Docker Pull
                  |
                  v
            Docker Container
                  |
                  v
          Running Application
```

You have also learned how to combine multiple containers:

```text
             Docker Compose
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Next.js    Node.js    MySQL
       Frontend   Backend    Database
                              |
                              v
                         Persistent
                           Volume
```

### Most Important Topics to Master

Prioritize these concepts:

1. Docker architecture.
2. Images vs containers.
3. Dockerfile instructions.
4. Docker image layers and caching.
5. Volumes and bind mounts.
6. Docker networking.
7. Docker Compose.
8. Multi-stage builds.
9. Dockerizing Node.js and Next.js.
10. Docker security and production deployment.

These are foundational skills for modern DevOps, cloud engineering and container-based application deployment.

---

## Next Phase Preview

# Phase 46 – Kubernetes, Container Orchestration and Cluster Management

The next phase will cover:

1. What Is Kubernetes?
2. Why Do We Need Kubernetes?
3. Docker vs Kubernetes.
4. Kubernetes Architecture.
5. Control Plane.
6. Worker Nodes.
7. API Server.
8. Scheduler.
9. Controller Manager.
10. etcd.
11. Kubelet.
12. Kube-proxy.
13. Pods.
14. ReplicaSets.
15. Deployments.
16. Services.
17. ClusterIP.
18. NodePort.
19. LoadBalancer Service.
20. Namespaces.
21. ConfigMaps.
22. Secrets.
23. Persistent Volumes.
24. Persistent Volume Claims.
25. Storage Classes.
26. Ingress.
27. Ingress Controllers.
28. Kubernetes Networking.
29. Kubernetes DNS.
30. Liveness and Readiness Probes.
31. Resource Requests and Limits.
32. Horizontal Pod Autoscaler.
33. StatefulSets.
34. DaemonSets.
35. Jobs and CronJobs.
36. Helm.
37. Kubernetes YAML Manifests.
38. kubectl Commands.
39. Deploying Node.js Applications.
40. Deploying Next.js Applications.
41. Kubernetes Security.
42. Monitoring and Logging.
43. Kubernetes on AWS using EKS.
44. Production Kubernetes Architecture.

**Phase 46 will take you from running individual containers to managing containerized applications across a cluster of machines.**