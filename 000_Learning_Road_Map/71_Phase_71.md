# PHASE 71 – Advanced Cloud Architecture, Kubernetes, Container Orchestration and Enterprise DevOps Engineering

**Level:** Advanced → Expert  
**Domain:** Cloud Computing, Docker, Kubernetes, DevOps, Infrastructure as Code, GitOps and Production Engineering  
**Primary Technologies:** AWS, Docker, Kubernetes, Helm, Terraform, GitHub Actions, Argo CD, Istio, Prometheus and Grafana.

---

# 1. Learning Objectives

By completing this phase, you should be able to:

1. Understand enterprise cloud architecture and cloud-native applications.
2. Design scalable infrastructure using AWS.
3. Build optimized production Docker images.
4. Manage multi-container applications using Docker Compose.
5. Understand Kubernetes architecture and internal components.
6. Deploy Node.js and Next.js applications on Kubernetes.
7. Manage application configuration, secrets, networking and storage.
8. Implement automatic scaling and self-healing.
9. Package Kubernetes applications using Helm.
10. Understand service mesh architecture and Istio.
11. Build automated CI/CD pipelines using GitHub Actions.
12. Implement GitOps using Argo CD.
13. Provision cloud infrastructure using Terraform.
14. Design reliable, secure and cost-efficient cloud infrastructure.
15. Build an enterprise-style deployment architecture.

---

# SECTION 1 – ADVANCED CLOUD COMPUTING

## 2. What Is Cloud Computing?

Cloud computing is the delivery of computing resources over the internet.

Instead of purchasing and maintaining physical servers, organizations rent infrastructure and services from cloud providers.

These resources include:

- Virtual machines.
- Storage.
- Databases.
- Networking.
- Load balancers.
- Application hosting.
- Serverless computing.
- Monitoring.
- Security services.
- Machine learning infrastructure.

### Traditional Infrastructure

```text
Company
   |
   v
Physical Server
   |
   +---- Operating System
   |
   +---- Database
   |
   +---- Backend Application
   |
   +---- Frontend Application
```

The company manages the hardware, networking, operating system and applications.

### Cloud Infrastructure

```text
Company
   |
   v
Cloud Provider
   |
   +---- Virtual Machines
   |
   +---- Managed Databases
   |
   +---- Cloud Storage
   |
   +---- Networking
   |
   +---- Load Balancers
   |
   +---- Monitoring
```

The cloud provider manages the underlying physical infrastructure.

The customer manages the services according to the selected cloud service model.

---

## 3. Cloud Service Models

There are five important cloud service models.

| Model | Full Form | Description | Example |
|---|---|---|---|
| IaaS | Infrastructure as a Service | Rent computing infrastructure | AWS EC2 |
| PaaS | Platform as a Service | Deploy applications without managing most infrastructure | Render |
| SaaS | Software as a Service | Use ready-made software | Gmail |
| FaaS | Function as a Service | Execute individual functions on demand | AWS Lambda |
| CaaS | Container as a Service | Manage and deploy containerized workloads | AWS ECS |

### 3.1 IaaS – Infrastructure as a Service

You rent virtualized infrastructure.

Examples:

- AWS EC2.
- Azure Virtual Machines.
- Google Compute Engine.

You generally manage:

- Operating system.
- Runtime.
- Application.
- Dependencies.
- Security configuration.
- Application deployment.

Example:

```text
AWS EC2
   |
   +---- Ubuntu
   |
   +---- Node.js
   |
   +---- Express.js
   |
   +---- MySQL Client
   |
   +---- Your Application
```

You are responsible for configuring and maintaining the server operating system.

### 3.2 PaaS – Platform as a Service

The provider manages much of the underlying infrastructure.

You focus primarily on application development and deployment.

Examples:

- Render.
- Heroku.
- Google App Engine.
- AWS Elastic Beanstalk.

Typical deployment:

```text
Developer
   |
   v
GitHub Repository
   |
   v
PaaS Platform
   |
   +---- Build Application
   |
   +---- Install Dependencies
   |
   +---- Start Application
   |
   v
Public Application URL
```

### 3.3 SaaS – Software as a Service

You use an already-developed software product.

Examples:

- Gmail.
- Google Docs.
- Microsoft 365.
- Salesforce.

You do not manage the underlying application infrastructure.

### 3.4 FaaS – Function as a Service

FaaS allows developers to execute individual functions without managing servers.

Example:

```javascript
export async function handler(event) {
  return {
    statusCode: 200,
    body: JSON.stringify({
      message: "Hello from serverless!"
    })
  };
}
```

AWS Lambda executes the function when triggered.

Common triggers:

- HTTP requests.
- File uploads.
- Database events.
- Queue messages.
- Scheduled events.

### 3.5 CaaS – Container as a Service

CaaS provides infrastructure and services for deploying containerized applications.

Examples:

- Amazon ECS.
- Amazon EKS.
- Azure Container Apps.

You package applications into containers and deploy them through the platform.

### IaaS vs PaaS vs SaaS vs FaaS

| Feature | IaaS | PaaS | SaaS | FaaS |
|---|---|---|---|---|
| Infrastructure management | Customer | Provider | Provider | Provider |
| OS management | Customer | Provider | Provider | Provider |
| Application development | Customer | Customer | Provider | Customer |
| Server management | Customer | Mostly provider | Provider | Provider |
| Scaling | Customer/configured | Platform-assisted | Provider | Platform-assisted |
| Flexibility | High | Medium | Low | Function-level |

**Important:** These models represent different levels of abstraction and responsibility. They are not necessarily mutually exclusive.

---

## 4. Cloud Deployment Models

### 4.1 Public Cloud

Infrastructure is provided over the internet by a cloud provider.

Examples:

- AWS.
- Microsoft Azure.
- Google Cloud.

Advantages:

- Flexible scaling.
- Lower initial infrastructure investment.
- Global availability.
- Managed services.

### 4.2 Private Cloud

Cloud infrastructure is dedicated to one organization.

Commonly used when organizations require greater control over infrastructure.

### 4.3 Hybrid Cloud

Combines private infrastructure with public cloud services.

```text
          Hybrid Cloud
               |
       +-------+-------+
       |               |
       v               v
 Private Cloud     Public Cloud
       |               |
       v               v
 Internal DB       Application
 Internal Apps     Cloud Storage
```

### 4.4 Multi-Cloud

Using services from multiple cloud providers.

Example:

```text
AWS
 |
 +---- Backend API
 +---- Database

Google Cloud
 |
 +---- Machine Learning

Azure
 |
 +---- Enterprise Identity
```

**Hybrid cloud and multi-cloud are different concepts.**

- Hybrid cloud combines private and public infrastructure.
- Multi-cloud uses multiple cloud providers.

---

## 5. Cloud Regions and Availability Zones

Cloud providers operate infrastructure in geographical regions.

### Region

A geographical location containing cloud infrastructure.

Examples:

- Mumbai.
- Singapore.
- Frankfurt.
- Virginia.

### Availability Zone (AZ)

An isolated infrastructure location within a region.

A region generally contains multiple availability zones.

Architecture:

```text
              AWS Region
                  |
       +----------+----------+
       |          |          |
       v          v          v
      AZ-1       AZ-2       AZ-3
       |          |          |
     EC2-1      EC2-2      EC2-3
       |          |          |
       +----------+----------+
                  |
              Load Balancer
```

Why use multiple availability zones?

If one availability zone experiences an outage, workloads in another zone may continue operating.

### Edge Locations

Edge locations bring content closer to users.

They are commonly used by content delivery networks.

Example:

```text
Ahmedabad User
       |
       v
Nearby CDN Edge
       |
       v
Origin Server
```

AWS CloudFront uses a global edge network.

---

## 6. Cloud-Native Architecture

Cloud-native architecture means designing applications specifically to take advantage of cloud environments.

Important characteristics:

1. Microservices or modular services.
2. Containerization.
3. Automated deployment.
4. Dynamic infrastructure.
5. Horizontal scaling.
6. Observability.
7. Resilience.
8. Infrastructure as Code.

### Traditional Application

```text
Single Server
    |
    +---- Frontend
    +---- Backend
    +---- Database
    +---- Background Jobs
```

### Cloud-Native Application

```text
                 Load Balancer
                       |
             +---------+---------+
             |                   |
             v                   v
         Frontend             API Gateway
                                  |
                +-----------------+----------------+
                |                 |                |
                v                 v                v
            User API          Product API       Order API
                |                 |                |
                v                 v                v
            User DB           Product DB        Order DB
                                  |
                                  v
                               Redis
```

Each service can be deployed and scaled independently.

### Cloud-Native Design Principles

| Principle | Meaning |
|---|---|
| Statelessness | Application instances do not depend on local persistent state |
| Automation | Infrastructure and deployments are automated |
| Resilience | Applications recover from failures |
| Observability | Application behavior can be monitored |
| Scalability | Capacity can increase or decrease |
| Immutability | Deploy new application versions instead of manually modifying servers |

---

## 7. Stateless vs Stateful Applications

### Stateless Application

A stateless application does not depend on local memory or local disk to preserve user session information between requests.

Example:

```javascript
app.get("/products", async (req, res) => {
  const products = await Product.findAll();

  res.json(products);
});
```

Any healthy application instance can process the request.

### Stateful Application

A stateful application maintains persistent state.

Examples:

- Database servers.
- Distributed caches.
- Message brokers.
- Stateful workloads.

### Comparison

| Stateless | Stateful |
|---|---|
| No essential local session state | Maintains persistent state |
| Easier horizontal scaling | Requires state coordination |
| Instances are interchangeable | Instances may have identity |
| Commonly uses external storage | May use persistent volumes |

**Important:** Stateless does not mean an application has no state. It means persistent or session state is stored outside the individual application instance.

---

# SECTION 2 – CLOUD NETWORKING AND SECURITY

## 8. What Is a VPC?

VPC stands for Virtual Private Cloud.

It is an isolated virtual network inside a cloud provider.

AWS provides VPC services.

Example:

```text
                  AWS VPC
                10.0.0.0/16
                      |
          +-----------+-----------+
          |                       |
          v                       v
     Public Subnet           Private Subnet
     10.0.1.0/24             10.0.2.0/24
          |                       |
          v                       v
     Load Balancer             EC2 API
          |                       |
          +-----------------------+
                                  |
                                  v
                              RDS Database
```

### Important VPC Components

| Component | Purpose |
|---|---|
| VPC | Isolated virtual network |
| Subnet | Network subdivision |
| Route Table | Controls network routing |
| Internet Gateway | Provides internet connectivity for eligible public resources |
| NAT Gateway | Allows private subnet resources to initiate outbound internet connections |
| Security Group | Stateful virtual firewall |
| Network ACL | Stateless subnet-level firewall |
| VPC Peering | Connects two VPCs |
| VPC Endpoint | Provides private connectivity to supported services |

### Public vs Private Subnets

**Public subnet:** A subnet whose routing configuration provides a route to an Internet Gateway.

**Private subnet:** A subnet without a direct route to an Internet Gateway for direct public access.

A private subnet may use a NAT Gateway for outbound connectivity.

### Security Group vs Network ACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Resource/network interface | Subnet |
| Stateful | Yes | No |
| Rules | Allow rules | Allow and deny rules |
| Return traffic | Automatically permitted when connection is allowed | Must be explicitly considered |
| Typical usage | Protect EC2 instances and other resources | Additional subnet-level filtering |

---

## 9. Cloud Identity and Access Management

IAM stands for Identity and Access Management.

It controls who can access cloud resources and what actions they can perform.

Example:

```text
Developer
   |
   v
IAM Role
   |
   v
Permissions Policy
   |
   v
AWS S3
```

Example IAM policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::my-bucket/*"
      ]
    }
  ]
}
```

This policy permits reading objects from the specified S3 bucket.

### Important IAM Concepts

- Users.
- Groups.
- Roles.
- Policies.
- Permissions.
- Trust policies.
- Identity federation.

### Principle of Least Privilege

Give each user, application and service only the permissions it actually needs.

For example:

A backend application that only reads files from S3 should not receive permission to delete buckets or modify IAM policies.

---

## 10. Cloud Cost Optimization

Cloud infrastructure can become expensive without proper resource management.

Important techniques:

1. Right-size virtual machines.
2. Scale workloads based on demand.
3. Remove unused resources.
4. Use storage lifecycle policies.
5. Monitor network transfer costs.
6. Use reserved or savings-based pricing when appropriate.
7. Monitor database capacity.
8. Configure budgets and alerts.
9. Tag resources by project and environment.

Example resource tags:

```text
Project: ECommerce
Environment: Production
Owner: BackendTeam
Department: Engineering
```

**Important:** Kubernetes and microservices are not automatically cheaper than traditional deployments. They introduce infrastructure and operational overhead.

---

# SECTION 3 – ADVANCED DOCKER ENGINEERING

## 11. What Is Docker?

Docker is a platform for building, packaging and running applications inside containers.

A container packages:

- Application code.
- Runtime dependencies.
- Libraries.
- Configuration defaults.
- Required application files.

Example:

```text
Node.js Application
       |
       v
Docker Image
       |
       v
Docker Container
       |
       v
Running Application
```

### Docker vs Virtual Machine

| Feature | Docker Container | Virtual Machine |
|---|---|---|
| Architecture | Shares host kernel | Runs a guest OS |
| Startup | Usually fast | Usually slower |
| Size | Usually smaller | Usually larger |
| Isolation | Process-level isolation | Hardware virtualization boundary |
| OS | Container userspace | Full guest OS |
| Resource usage | Generally lower | Generally higher |

### Important Docker Internals

**Namespaces:** Provide isolation of resources such as processes, network interfaces and mount points.

**cgroups:** Control and account for resource usage, such as CPU and memory.

**Container Runtime:** Runs containers using the container image and runtime configuration.

**OCI:** Open Container Initiative, which defines standards for container images and runtimes.

---

## 12. Docker Images and Layers

A Docker image is an immutable template used to create containers.

Docker images are composed of layers.

Example:

```text
Application Image
       |
       +---- Application Layer
       |
       +---- Dependencies Layer
       |
       +---- Node.js Runtime Layer
       |
       +---- Base OS Layer
```

Docker can reuse unchanged layers during image builds.

This reduces build time and storage requirements.

### Important Commands

```bash
# Download an image
docker pull node:22

# List images
docker images

# Build image
docker build -t my-api:1.0 .

# Run container
docker run -d -p 3000:3000 my-api:1.0

# List running containers
docker ps

# List all containers
docker ps -a

# Stop container
docker stop container_id

# Remove container
docker rm container_id

# Remove image
docker rmi my-api:1.0
```

---

## 13. Dockerfile

A Dockerfile contains instructions to build a Docker image.

Example:

```dockerfile
FROM node:22-slim

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

### Important Dockerfile Instructions

| Instruction | Purpose |
|---|---|
| FROM | Defines the base image |
| WORKDIR | Sets the working directory |
| COPY | Copies files into the image |
| ADD | Copies files and supports additional extraction behavior |
| RUN | Executes build-time commands |
| ENV | Sets environment variables |
| ARG | Defines build-time variables |
| EXPOSE | Documents the container port |
| USER | Selects the runtime user |
| CMD | Defines the default command |
| ENTRYPOINT | Defines the main executable |

### RUN vs CMD vs ENTRYPOINT

**RUN:** Executes during image building.

**CMD:** Defines the default runtime command or arguments.

**ENTRYPOINT:** Defines the main executable for the container.

Example:

```dockerfile
FROM node:22-slim

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

CMD ["npm", "start"]
```

`npm ci` executes during image creation.

`npm start` executes when the container starts.

---

## 14. Multi-Stage Docker Builds

Multi-stage builds separate the build environment from the production runtime environment.

Why?

A production application usually does not need:

- TypeScript compiler.
- Development dependencies.
- Build tools.
- Source maps and temporary build artifacts.
- Unnecessary files.

### Example: Node.js TypeScript Application

```dockerfile
FROM node:22-slim AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY tsconfig.json ./

COPY src ./src

RUN npm run build

RUN npm prune --omit=dev


FROM node:22-slim AS production

WORKDIR /app

ENV NODE_ENV=production

COPY --from=builder /app/package*.json ./

COPY --from=builder /app/node_modules ./node_modules

COPY --from=builder /app/dist ./dist

USER node

EXPOSE 3000

CMD ["node", "dist/server.js"]
```

### Architecture

```text
             Docker Build
                  |
                  v
          Builder Stage
                  |
          +-------+-------+
          |               |
          v               v
       TypeScript      Dependencies
          |               |
          +-------+-------+
                  |
                  v
             Compile Code
                  |
                  v
             Production
                  |
          +-------+-------+
          |               |
          v               v
      Runtime Code   Production Packages
          |               |
          +-------+-------+
                  |
                  v
            Final Image
```

The production stage contains only the required runtime artifacts.

**Important:** The example assumes a standard TypeScript project that produces `dist/server.js`. Adapt the build and copy instructions to your actual project structure.

Docker recommends multi-stage builds, trusted base images and non-root execution for production images. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

---

## 15. Docker .dockerignore

The `.dockerignore` file excludes unnecessary files from the Docker build context.

Example:

```text
node_modules
.git
.env
.env.*
coverage
npm-debug.log
Dockerfile
docker-compose.yml
README.md
```

Why is this important?

- Smaller build context.
- Faster builds.
- Reduced accidental secret exposure.
- Less unnecessary data transferred to the builder.

Never include private credentials in the Docker build context unless there is a deliberate and secure reason.

---

## 16. Docker Networking

Docker containers need networking to communicate.

Important network drivers:

| Network | Description |
|---|---|
| Bridge | Default network type for many standalone container setups |
| Host | Shares the host network namespace |
| Overlay | Connects containers across multiple Docker hosts |
| None | Disables container networking |

### Bridge Network

Example:

```bash
docker network create app-network
```

Run API:

```bash
docker run -d \
  --name api \
  --network app-network \
  my-api:1.0
```

Run Redis:

```bash
docker run -d \
  --name redis \
  --network app-network \
  redis:7
```

The API can communicate with Redis using:

```text
redis:6379
```

It does not need to know Redis's dynamically assigned IP address.

---

## 17. Docker Volumes

Containers are generally replaceable.

Data that must survive container replacement should be stored outside the container's writable layer.

Docker provides:

1. Named volumes.
2. Bind mounts.
3. tmpfs mounts.

### Named Volume

```bash
docker volume create mysql-data
```

Use it:

```bash
docker run -d \
  --name mysql \
  -e MYSQL_ROOT_PASSWORD=example \
  -v mysql-data:/var/lib/mysql \
  mysql:8
```

### Bind Mount

```bash
docker run -d \
  -v ./uploads:/app/uploads \
  my-api:1.0
```

A bind mount connects a host filesystem path to a container path.

### Comparison

| Named Volume | Bind Mount | tmpfs |
|---|---|---|
| Managed by Docker | Uses host path | Stored in memory |
| Good for persistent data | Good for development | Temporary sensitive data |
| Easier to manage | Direct host access | Lost when stopped |

Docker Compose also supports named volumes and custom networks. [Docker Documentation](https://docs.docker.com/reference/compose-file/volumes/?utm_source=chatgpt.com)

---

## 18. Docker Compose Advanced

Docker Compose defines and manages multi-container applications.

Consider an application with:

- Node.js API.
- MySQL database.
- Redis cache.

Architecture:

```text
               Docker Compose
                     |
          +----------+----------+
          |          |          |
          v          v          v
        API         MySQL      Redis
          |          |          |
          +----------+----------+
                     |
                 Network
```

### Example compose.yaml

```yaml
services:

  api:
    build: .
    container_name: node-api

    ports:
      - "3000:3000"

    environment:
      NODE_ENV: development
      DB_HOST: mysql
      DB_PORT: 3306
      REDIS_HOST: redis

    depends_on:
      mysql:
        condition: service_healthy

    networks:
      - backend

  mysql:
    image: mysql:8

    environment:
      MYSQL_DATABASE: ecommerce
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}

    volumes:
      - mysql-data:/var/lib/mysql

    healthcheck:
      test:
        - CMD
        - mysqladmin
        - ping
        - -h
        - localhost
      interval: 10s
      timeout: 5s
      retries: 5

    networks:
      - backend

  redis:
    image: redis:7

    networks:
      - backend

volumes:
  mysql-data:

networks:
  backend:
```

### Commands

```bash
# Start services
docker compose up -d

# View running services
docker compose ps

# View logs
docker compose logs -f

# Stop services
docker compose down

# Rebuild images
docker compose build

# Start with rebuild
docker compose up -d --build

# Remove containers and volumes
docker compose down -v
```

**Warning:** `docker compose down -v` removes declared named volumes. Do not use it casually when the volumes contain important database data.

### Important: depends_on

`depends_on` controls startup ordering.

It does not automatically guarantee that a dependency is ready to accept application requests unless a suitable health condition is configured.

Even with health checks, applications should implement connection retries and reconnection handling.

---

# SECTION 4 – KUBERNETES FUNDAMENTALS

## 19. What Is Kubernetes?

Kubernetes is an open-source container orchestration platform.

It automates:

- Container deployment.
- Scaling.
- Scheduling.
- Service discovery.
- Load balancing.
- Application recovery.
- Rolling updates.
- Configuration management.

Imagine that you have 100 containers running across 10 servers.

Managing those containers manually becomes difficult.

Kubernetes manages the desired state of the application.

### Without Kubernetes

```text
Developer
   |
   +---- Manually Start Containers
   |
   +---- Monitor Servers
   |
   +---- Restart Failed Containers
   |
   +---- Configure Load Balancing
   |
   +---- Scale Applications
```

### With Kubernetes

```text
Developer
   |
   v
Kubernetes Cluster
   |
   +---- Schedule Containers
   |
   +---- Restart Failed Pods
   |
   +---- Maintain Replicas
   |
   +---- Expose Services
   |
   +---- Scale Applications
```

Kubernetes manages workloads through resources such as Deployments, StatefulSets and DaemonSets. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/?utm_source=chatgpt.com)

---

## 20. Kubernetes Architecture

A Kubernetes cluster consists of two major parts:

1. Control Plane.
2. Worker Nodes.

```text
                  Kubernetes Cluster
                         |
              +----------+----------+
              |                     |
              v                     v
         Control Plane          Worker Nodes
              |                     |
       +------+-------+       +-----+------+
       |      |       |       |            |
       v      v       v       v            v
    API     etcd   Scheduler  Node 1      Node 2
   Server                  +--------+   +--------+
       |                   | Pod A  |   | Pod C  |
       |                   | Pod B  |   | Pod D  |
       |                   +--------+   +--------+
       |
       v
Controller Manager
```

---

## 21. Kubernetes Control Plane

The control plane manages the Kubernetes cluster.

### 21.1 API Server

The API Server is the central communication interface of Kubernetes.

All major cluster operations interact with it.

Example:

```bash
kubectl get pods
```

The command communicates with the Kubernetes API Server.

### 21.2 etcd

etcd is a distributed key-value store.

Kubernetes uses it to store cluster state and configuration data.

Examples:

- Kubernetes objects.
- Cluster configuration.
- Resource metadata.
- Desired state.

Protect etcd carefully because loss or corruption can affect the entire cluster.

### 21.3 Scheduler

The scheduler selects an appropriate worker node for newly created Pods.

It considers factors such as:

- Available CPU.
- Available memory.
- Resource requests.
- Node selectors.
- Affinity rules.
- Taints and tolerations.
- Scheduling constraints.

### 21.4 Controller Manager

The Controller Manager runs controllers that continuously reconcile actual state with desired state.

Example:

You request three replicas.

```yaml
spec:
  replicas: 3
```

If one Pod fails, the relevant controller works toward restoring three replicas.

### 21.5 Cloud Controller Manager

This component integrates Kubernetes with cloud provider functionality.

For example:

- Cloud load balancers.
- Cloud routes.
- Node lifecycle integration.

---

## 22. Kubernetes Worker Nodes

Worker nodes execute application workloads.

Important components:

| Component | Purpose |
|---|---|
| kubelet | Ensures Pods and containers run according to assigned specifications |
| Container Runtime | Runs containers |
| kube-proxy | Implements Service networking behavior in clusters using its supported proxy modes |
| CNI Plugin | Provides Pod networking |

**Important:** Kubernetes networking implementations differ. Some CNI plugins provide or replace functionality traditionally associated with kube-proxy.

---

## 23. What Is a Kubernetes Pod?

A Pod is the smallest deployable unit in Kubernetes.

A Pod contains one or more containers.

Example:

```text
Pod
 |
 +---- Node.js API Container
 |
 +---- Logging Sidecar Container
 |
 +---- Shared Volumes
 |
 +---- Shared Network Namespace
```

Containers within the same Pod share:

- Network namespace.
- Pod IP address.
- Localhost networking.
- Configured shared volumes.

They do not automatically share their filesystem.

### Example Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: node-api

spec:
  containers:

    - name: api

      image: my-api:1.0

      ports:
        - containerPort: 3000
```

Deploy:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods
```

Describe:

```bash
kubectl describe pod node-api
```

View logs:

```bash
kubectl logs node-api
```

---

## 24. ReplicaSet

A ReplicaSet maintains a specified number of identical Pods.

Example:

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: api-replicas

spec:
  replicas: 3

  selector:
    matchLabels:
      app: node-api

  template:
    metadata:
      labels:
        app: node-api

    spec:
      containers:
        - name: api
          image: my-api:1.0
          ports:
            - containerPort: 3000
```

If a Pod fails, the ReplicaSet attempts to create another Pod.

### ReplicaSet Architecture

```text
             ReplicaSet
                  |
          +-------+-------+
          |       |       |
          v       v       v
        Pod 1   Pod 2   Pod 3
```

---

## 25. Deployment

A Deployment manages ReplicaSets and provides declarative application updates.

It is the most common Kubernetes resource for stateless application workloads.

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: node-api

spec:
  replicas: 3

  selector:
    matchLabels:
      app: node-api

  template:
    metadata:
      labels:
        app: node-api

    spec:
      containers:

        - name: api

          image: my-api:1.0

          ports:
            - containerPort: 3000
```

Deploy:

```bash
kubectl apply -f deployment.yaml
```

Check:

```bash
kubectl get deployments
kubectl get pods
```

Scale:

```bash
kubectl scale deployment node-api --replicas=5
```

### Rolling Updates

A Deployment can gradually replace old Pods with new Pods.

```text
Version 1.0

Pod A
Pod B
Pod C

      |
      v

Version 1.1 Deployment

Pod A - New
Pod B - Old
Pod C - Old

      |
      v

Pod A - New
Pod B - New
Pod C - Old

      |
      v

Pod A - New
Pod B - New
Pod C - New
```

Commands:

```bash
# Update image
kubectl set image deployment/node-api \
  api=my-api:2.0

# Check rollout
kubectl rollout status deployment/node-api

# View history
kubectl rollout history deployment/node-api

# Rollback
kubectl rollout undo deployment/node-api
```

---

## 26. StatefulSet

StatefulSet is designed for stateful workloads that require stable identity and, commonly, stable persistent storage.

Examples:

- Database clusters.
- Distributed databases.
- Stateful message brokers.
- Certain distributed storage systems.

### Deployment vs StatefulSet

| Deployment | StatefulSet |
|---|---|
| Commonly used for stateless workloads | Used for stateful workloads |
| Pods are interchangeable | Pods have stable identities |
| No inherent stable per-Pod storage | Can associate stable storage with Pods |
| Flexible replacement behavior | Ordered lifecycle behavior is available |

Example identities:

```text
mysql-0
mysql-1
mysql-2
```

A StatefulSet does not automatically turn a database into a highly available database. Database replication, backup and recovery still need to be designed.

---

## 27. DaemonSet

DaemonSet ensures that a Pod runs on each eligible node.

Typical use cases:

- Log collection.
- Monitoring agents.
- Node-level networking agents.
- Security agents.

Example:

```text
Kubernetes Cluster
       |
   +---+---+
   |       |
   v       v
 Node 1  Node 2
   |       |
   v       v
 Agent   Agent
```

---

## 28. Jobs and CronJobs

### Job

A Job runs a task until it completes successfully according to its configured completion requirements.

Example:

- Database migration.
- Report generation.
- Data processing.

### CronJob

A CronJob schedules Jobs using a cron expression.

Example:

```yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name: daily-report

spec:
  schedule: "0 2 * * *"

  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: report
              image: report-generator:1.0
              command:
                - node
                - report.js

          restartPolicy: OnFailure
```

This schedules a report job daily at 2 AM, using the cluster's scheduling interpretation.

---

# SECTION 5 – KUBERNETES CONFIGURATION AND NETWORKING

## 29. ConfigMaps

ConfigMaps store non-sensitive application configuration.

Example:

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: api-config

data:
  NODE_ENV: production
  PORT: "3000"
  LOG_LEVEL: info
```

Use in Deployment:

```yaml
envFrom:
  - configMapRef:
      name: api-config
```

Your Node.js application can access:

```javascript
console.log(process.env.NODE_ENV);
console.log(process.env.PORT);
```

---

## 30. Secrets

Kubernetes Secrets store sensitive configuration data.

Examples:

- Database passwords.
- API keys.
- Tokens.
- Certificates.

Example:

```yaml
apiVersion: v1
kind: Secret

metadata:
  name: database-secret

type: Opaque

stringData:
  DB_USER: app_user
  DB_PASSWORD: change-this-password
```

Use:

```yaml
envFrom:
  - secretRef:
      name: database-secret
```

### Important Security Warning

Kubernetes Secret values are commonly represented using Base64 encoding when serialized.

**Base64 is not encryption.**

For production environments:

- Enable encryption at rest for Kubernetes Secrets.
- Restrict access using RBAC.
- Avoid committing secret manifests to Git.
- Consider AWS Secrets Manager or another external secrets management solution.
- Use workload identity where supported.
- Rotate credentials.

---

## 31. Kubernetes Services

Pods are temporary.

Their IP addresses can change when they are replaced.

A Service provides a stable network endpoint for accessing a group of Pods.

### Service Architecture

```text
                API Service
                     |
             +-------+-------+
             |       |       |
             v       v       v
           Pod 1   Pod 2   Pod 3
```

### ClusterIP

The default Service type.

Used for internal cluster communication.

```yaml
apiVersion: v1
kind: Service

metadata:
  name: api-service

spec:
  type: ClusterIP

  selector:
    app: node-api

  ports:
    - port: 80
      targetPort: 3000
```

Other Pods can access the API through:

```text
http://api-service
```

### NodePort

Exposes a Service on a port on each eligible node.

```yaml
spec:
  type: NodePort
```

Useful for certain development and infrastructure setups, but not generally the preferred direct public exposure mechanism for production applications.

### LoadBalancer

Requests an external load balancer through a compatible cloud integration.

```yaml
spec:
  type: LoadBalancer
```

### ExternalName

Maps a Service name to an external DNS name.

### Service Comparison

| Type | Purpose |
|---|---|
| ClusterIP | Internal cluster access |
| NodePort | Exposes a port on cluster nodes |
| LoadBalancer | Exposes a service through a cloud or supported load balancer |
| ExternalName | DNS alias to an external hostname |

---

## 32. Ingress

Ingress manages HTTP and HTTPS routing into a Kubernetes cluster.

Example:

```text
                  Internet
                     |
                     v
              Ingress Controller
                     |
             +-------+-------+
             |               |
             v               v
         Frontend          Backend
             |               |
             v               v
         Next.js API      Express API
```

Example Ingress resource:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: app-ingress

spec:
  rules:

    - host: example.com

      http:
        paths:

          - path: /
            pathType: Prefix

            backend:
              service:
                name: frontend-service

                port:
                  number: 3000

          - path: /api
            pathType: Prefix

            backend:
              service:
                name: api-service

                port:
                  number: 80
```

**Important:** Creating an Ingress resource alone does not provide an operational HTTP entry point. An Ingress Controller must be installed and configured.

---

## 33. Gateway API

Gateway API is a newer, extensible Kubernetes networking API.

It provides more structured networking resources than the traditional Ingress model.

Important resources:

- GatewayClass.
- Gateway.
- HTTPRoute.
- GRPCRoute.
- TCPRoute.
- TLSRoute.

Conceptual architecture:

```text
              External Traffic
                     |
                     v
                  Gateway
                     |
              +------+------+
              |             |
              v             v
          HTTPRoute      HTTPRoute
              |             |
              v             v
          Frontend        Backend
```

### Ingress vs Gateway API

| Ingress | Gateway API |
|---|---|
| Simpler HTTP routing model | More expressive routing model |
| Older standard API | Newer extensible API |
| Limited built-in role separation | Supports role-oriented configuration |
| Commonly relies on annotations for extensions | Provides richer typed resources |

Ingress remains supported, but its API is frozen. Kubernetes recommends Gateway API for new functionality and more advanced routing requirements.

---

## 34. Kubernetes DNS

Kubernetes provides internal DNS-based service discovery.

Example:

```text
api-service
```

A Service can be addressed using its DNS name.

A fully qualified service DNS name commonly follows:

```text
service-name.namespace.svc.cluster.local
```

Example:

```text
api-service.production.svc.cluster.local
```

This is useful for communication between microservices.

---

## 35. Persistent Volumes and Persistent Volume Claims

### Persistent Volume (PV)

A Persistent Volume represents storage made available to the cluster.

### Persistent Volume Claim (PVC)

A Persistent Volume Claim is a request for storage by a workload.

### StorageClass

StorageClass describes how dynamic storage provisioning should work.

Architecture:

```text
             Application Pod
                    |
                    v
                   PVC
                    |
                    v
                    PV
                    |
                    v
              Cloud Storage
```

Example PVC:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: app-storage

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 10Gi
```

Mount it:

```yaml
volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: app-storage
```

Container configuration:

```yaml
volumeMounts:
  - name: storage
    mountPath: /app/uploads
```

### Storage Access Modes

| Mode | Meaning |
|---|---|
| ReadWriteOnce | Read-write mounting by a single node |
| ReadOnlyMany | Read-only mounting by multiple nodes |
| ReadWriteMany | Read-write mounting by multiple nodes |
| ReadWriteOncePod | Read-write mounting by a single Pod |

Actual support depends on the storage driver.

**Important:** Do not assume a database running in a container automatically has safe persistent storage. You must also design backups, recovery, replication and storage performance.

---

# SECTION 6 – KUBERNETES SCALING AND RELIABILITY

## 36. Resource Requests and Limits

Kubernetes uses resource requests and limits to manage container resource consumption.

### Requests

The resources Kubernetes uses when making scheduling decisions.

### Limits

The maximum resource usage permitted for a container.

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

CPU units:

```text
1000m = 1 CPU core
500m  = 0.5 CPU core
250m  = 0.25 CPU core
```

Memory units:

```text
256Mi
512Mi
1Gi
```

### Why They Matter

If requests are too high:

- Pods may remain unscheduled.
- Cluster utilization may decrease.

If memory limits are too low:

- Containers may be terminated due to memory exhaustion.

If CPU limits are too restrictive:

- Applications may experience CPU throttling.

---

## 37. Kubernetes Health Checks and Probes

Kubernetes supports three major container probes.

### Liveness Probe

Checks whether the container should be restarted.

### Readiness Probe

Checks whether the container should receive traffic.

### Startup Probe

Allows slow-starting applications time to initialize before liveness and readiness checks take effect.

Example:

```yaml
containers:

  - name: api

    image: my-api:1.0

    ports:
      - containerPort: 3000

    startupProbe:
      httpGet:
        path: /health/startup
        port: 3000

      periodSeconds: 5
      failureThreshold: 30

    livenessProbe:
      httpGet:
        path: /health/live
        port: 3000

      periodSeconds: 10
      failureThreshold: 3

    readinessProbe:
      httpGet:
        path: /health/ready
        port: 3000

      periodSeconds: 5
      failureThreshold: 3
```

### Node.js Health Endpoints

```javascript
app.get("/health/live", (req, res) => {
  res.status(200).json({
    status: "alive"
  });
});

app.get("/health/ready", async (req, res) => {
  try {
    await database.authenticate();

    res.status(200).json({
      status: "ready"
    });

  } catch {
    res.status(503).json({
      status: "not-ready"
    });
  }
});
```

In a real application, avoid making liveness depend on every external service. A temporary database outage should not necessarily cause Kubernetes to restart every API instance.

---

## 38. Horizontal Pod Autoscaler (HPA)

HPA automatically changes the number of Pod replicas based on metrics.

Example:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: api-hpa

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: node-api

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

Architecture:

```text
             Incoming Traffic
                    |
                    v
              Node.js API
                    |
                    v
             Metrics Server
                    |
                    v
                   HPA
                    |
             +------+------+
             |             |
             v             v
         Increase       Decrease
          Replicas       Replicas
```

HPA generally requires suitable resource metrics, such as the Metrics Server for CPU and memory metrics.

---

## 39. Cluster Autoscaler

Cluster Autoscaler adjusts the number of cluster nodes based on scheduling demand and node utilization.

Example:

```text
Pods Need More Capacity
          |
          v
Cluster Autoscaler
          |
          v
Add Worker Nodes
          |
          v
Schedule Pending Pods
```

### HPA vs Cluster Autoscaler

| HPA | Cluster Autoscaler |
|---|---|
| Scales Pods | Scales nodes |
| Changes replica count | Changes cluster capacity |
| Application-level scaling | Infrastructure-level scaling |

They can work together.

---

## 40. Kubernetes Scheduling

The scheduler determines where Pods should run.

Important scheduling concepts:

### Node Selector

Schedules a Pod onto nodes with matching labels.

### Node Affinity

Provides more expressive node selection rules.

### Pod Affinity

Places Pods near other matching Pods.

### Pod Anti-Affinity

Can spread Pods away from each other.

### Taints and Tolerations

Taints discourage or prevent Pods from scheduling onto particular nodes unless they tolerate the taint.

### Topology Spread Constraints

Help distribute Pods across topology domains such as zones or nodes.

### PodDisruptionBudget

Helps maintain availability during supported voluntary disruptions.

Example:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget

metadata:
  name: api-pdb

spec:
  minAvailable: 2

  selector:
    matchLabels:
      app: node-api
```

This requires at least two matching Pods to remain available during supported voluntary disruptions.

It does not prevent involuntary failures.

---

# SECTION 7 – KUBERNETES SECURITY

## 41. Kubernetes RBAC

RBAC stands for Role-Based Access Control.

It controls what authenticated identities can do within a Kubernetes cluster.

Important resources:

- Role.
- ClusterRole.
- RoleBinding.
- ClusterRoleBinding.
- ServiceAccount.

### Role

Defines permissions within a namespace.

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role

metadata:
  name: pod-reader
  namespace: production

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

### RoleBinding

Connects a Role to a user, group or ServiceAccount.

### ClusterRole

Defines permissions that can apply at cluster scope or be reused in namespace-scoped bindings.

### ClusterRoleBinding

Grants ClusterRole permissions at cluster scope.

**Best practice:** Use the narrowest scope and fewest permissions necessary.

---

## 42. Pod Security

Important security configurations:

- Run containers as non-root.
- Use read-only root filesystems when possible.
- Drop unnecessary Linux capabilities.
- Restrict privilege escalation.
- Use trusted container images.
- Scan dependencies and images.
- Apply NetworkPolicies.
- Use resource limits.
- Avoid privileged containers.

Example:

```yaml
securityContext:

  runAsNonRoot: true

  runAsUser: 10001

  allowPrivilegeEscalation: false

  readOnlyRootFilesystem: true

  capabilities:
    drop:
      - ALL
```

This configuration may require application-specific writable temporary directories and filesystem adjustments.

### Pod Security Standards

Kubernetes defines three Pod Security Standards:

1. Privileged.
2. Baseline.
3. Restricted.

Restricted is the most restrictive standard.

---

## 43. Kubernetes Network Policies

NetworkPolicy controls communication between Pods and network endpoints.

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: allow-api-ingress

spec:
  podSelector:
    matchLabels:
      app: node-api

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

This allows matching frontend Pods to access matching API Pods on TCP port 3000.

**Important:** NetworkPolicy enforcement depends on the installed networking implementation. Creating the resource does not guarantee enforcement if the CNI does not support it.

---

# SECTION 8 – HELM AND KUBERNETES OPERATORS

## 44. What Is Helm?

Helm is a package manager for Kubernetes.

It simplifies deploying and managing groups of Kubernetes resources.

Without Helm:

```text
deployment.yaml
service.yaml
configmap.yaml
ingress.yaml
hpa.yaml
```

You must manage multiple manifests manually.

With Helm:

```text
Application Chart
       |
       +---- Deployment
       +---- Service
       +---- ConfigMap
       +---- Ingress
       +---- HPA
```

### Helm Chart Structure

```text
node-api/
    |
    +---- Chart.yaml
    |
    +---- values.yaml
    |
    +---- templates/
    |       |
    |       +---- deployment.yaml
    |       +---- service.yaml
    |       +---- ingress.yaml
    |       +---- hpa.yaml
    |
    +---- charts/
```

### Chart.yaml

```yaml
apiVersion: v2

name: node-api

description: Node.js API Helm Chart

type: application

version: 1.0.0

appVersion: "1.0.0"
```

### values.yaml

```yaml
replicaCount: 3

image:
  repository: my-registry/node-api
  tag: "1.0.0"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80
  targetPort: 3000

resources:
  requests:
    cpu: 250m
    memory: 256Mi

  limits:
    cpu: 1000m
    memory: 512Mi
```

### Deployment Template

`templates/deployment.yaml`

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

        - name: node-api

          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"

          ports:
            - containerPort: {{ .Values.service.targetPort }}

          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

### Important Helm Commands

```bash
# Create chart
helm create node-api

# Validate chart
helm lint ./node-api

# Render templates locally
helm template my-api ./node-api

# Install chart
helm install my-api ./node-api

# Upgrade
helm upgrade my-api ./node-api

# Install or upgrade
helm upgrade --install my-api ./node-api

# List releases
helm list

# Rollback
helm rollback my-api 1

# Uninstall
helm uninstall my-api
```

Helm uses templates and configurable values to package Kubernetes resources. [Helm](https://helm.sh/docs/topics/charts/?utm_source=chatgpt.com)

**Security:** Never place production passwords or API keys directly into committed `values.yaml` files.

---

## 45. Kubernetes Operators

An Operator extends Kubernetes by adding application-specific operational knowledge.

It usually combines:

- Custom Resource Definitions (CRDs).
- Custom Resources.
- Controllers.
- Reconciliation logic.

Example:

A database operator might manage:

- Database deployment.
- Replication.
- Backups.
- Failover.
- Database upgrades.

### Operator Architecture

```text
       Developer
           |
           v
     Custom Resource
           |
           v
      Kubernetes API
           |
           v
        Operator
           |
           v
    Reconciliation Loop
           |
     +-----+-----+
     |     |     |
     v     v     v
   Pods  Storage Service
```

### Helm vs Operator

| Helm | Operator |
|---|---|
| Packages and installs resources | Automates application-specific lifecycle operations |
| Template-based deployment | Controller-based reconciliation |
| Good for repeatable installation | Good for complex operational workflows |
| Does not inherently manage application behavior after installation | Can continuously manage application behavior |

Use Helm for packaging and Operators when the application requires complex ongoing lifecycle automation.

---

# SECTION 9 – SERVICE MESH

## 46. What Is a Service Mesh?

A service mesh is an infrastructure layer that manages communication between services.

In a microservices architecture, services communicate with each other over the network.

Example:

```text
Frontend
   |
   v
API Gateway
   |
   +---- User Service
   |
   +---- Product Service
   |
   +---- Order Service
   |
   +---- Payment Service
```

As the number of services increases, managing communication becomes difficult.

A service mesh can provide:

- Service-to-service authentication.
- Mutual TLS.
- Traffic management.
- Retries.
- Timeouts.
- Telemetry.
- Traffic shifting.

---

## 47. Data Plane vs Control Plane

### Data Plane

Handles actual service traffic.

### Control Plane

Manages configuration and policy for the data plane.

Architecture:

```text
                Control Plane
                      |
                Service Policies
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
       Proxy       Proxy       Proxy
          |           |           |
          v           v           v
       Service A   Service B   Service C
```

---

## 48. Istio

Istio is a service mesh platform.

Important capabilities:

- Traffic routing.
- mTLS.
- Authentication.
- Authorization.
- Observability.
- Traffic shifting.
- Fault injection.

Istio commonly uses Envoy proxies to mediate application traffic.

Its architecture includes a control plane that configures data-plane proxies. [Istio](https://istio.io/latest/docs/ops/deployment/architecture/?utm_source=chatgpt.com)

### Example Traffic Splitting

Imagine two versions of your API:

```text
                 Incoming Requests
                        |
                        v
                  Istio Gateway
                        |
                 Traffic Routing
                        |
              +---------+---------+
              |                   |
              v                   v
          API v1 (90%)        API v2 (10%)
```

This allows controlled canary releases.

### Istio Use Cases

1. Service-to-service mTLS.
2. Traffic shifting.
3. Request routing.
4. Fault injection.
5. Retry and timeout policies.
6. Service-level telemetry.

Istio supports advanced traffic management, including staged traffic shifting and resilience features. [Istio](https://istio.io/latest/docs/concepts/traffic-management/?utm_source=chatgpt.com)

### Linkerd

Linkerd is another service mesh.

It focuses on:

- Service reliability.
- Secure communication.
- Observability.
- Traffic management.

### When Should You Use a Service Mesh?

Use it when you have enough service-to-service complexity to justify the additional infrastructure.

Avoid introducing it into a small application without a clear operational need.

A service mesh adds complexity, resource consumption and troubleshooting requirements.

---

# SECTION 10 – CI/CD AND GITHUB ACTIONS

## 49. What Is CI/CD?

CI/CD automates the process of validating, building, testing and deploying applications.

### CI – Continuous Integration

Developers frequently merge code changes into a shared repository.

Automated workflows validate those changes.

### CD – Continuous Delivery

Code is automatically prepared for release, but production deployment may require approval.

### Continuous Deployment

Validated changes are automatically deployed to production.

### Typical Pipeline

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
 Install Dependencies
      |
      v
 Lint and Type Check
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
 Security Scan
      |
      v
 Push Image to Registry
      |
      v
 Deploy Application
      |
      v
 Smoke Tests
```

---

## 50. GitHub Actions

GitHub Actions automates workflows inside GitHub repositories.

Important concepts:

| Concept | Meaning |
|---|---|
| Workflow | Automated process |
| Event | Trigger |
| Job | Group of steps |
| Step | Individual operation |
| Runner | Machine executing jobs |
| Action | Reusable workflow component |
| Secret | Protected configuration value |

### Example Workflow for Node.js

Create:

```text
.github/workflows/ci.yml
```

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

  build:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4

        with:
          node-version: 22
          cache: npm

      - name: Install Dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Test
        run: npm test

      - name: Build
        run: npm run build
```

### Important Events

- `push`
- `pull_request`
- `workflow_dispatch`
- `schedule`
- `release`

### Important Best Practices

- Pin third-party actions to verified commit SHAs for stronger supply-chain security.
- Use least-privilege workflow permissions.
- Protect production environments.
- Avoid exposing secrets in logs.
- Separate CI and deployment permissions.
- Use dependency caching carefully.
- Run automated tests before deployment.

---

## 51. Docker Build and Push Using GitHub Actions

Example workflow:

```yaml
name: Docker Build

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
        uses: actions/checkout@v4

      - name: Login to GHCR
        uses: docker/login-action@v3

        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and Push
        uses: docker/build-push-action@v6

        with:
          context: .
          push: true

          tags: |
            ghcr.io/${{ github.repository }}:${{ github.sha }}
```

This workflow builds a Docker image and pushes it to GitHub Container Registry.

In production, verify and pin the action versions to immutable commit SHAs and configure appropriate registry permissions.

---

## 52. GitOps

GitOps is an operational model where Git repositories represent the desired state of infrastructure and applications.

Instead of manually changing cluster resources, you update configuration in Git.

### Traditional Deployment

```text
Developer
    |
    v
CI/CD Pipeline
    |
    v
Direct Kubernetes Deployment
```

### GitOps Deployment

```text
Developer
    |
    v
Git Repository
    |
    v
GitOps Controller
    |
    v
Kubernetes Cluster
```

The GitOps controller continuously compares the actual cluster state with the desired state in Git.

If drift is detected, it can reconcile the cluster depending on its configuration.

### Benefits

- Version-controlled infrastructure.
- Auditable changes.
- Repeatable deployments.
- Drift detection.
- Easier rollback.
- Reduced manual cluster modifications.

---

## 53. Argo CD

Argo CD is a declarative GitOps continuous delivery tool for Kubernetes.

It monitors Git repositories and synchronizes application manifests with the cluster.

Architecture:

```text
               Git Repository
                     |
                     v
                  Argo CD
                     |
               Compare State
                     |
            +--------+--------+
            |                 |
            v                 v
       Desired State      Actual State
            |                 |
            +--------+--------+
                     |
                     v
               Synchronization
                     |
                     v
              Kubernetes Cluster
```

### Important Argo CD Concepts

- Application.
- Project.
- Repository.
- Sync.
- Health.
- Automated synchronization.
- Drift detection.
- Self-healing.

### Example Application Manifest

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application

metadata:
  name: node-api

  namespace: argocd

spec:

  project: default

  source:
    repoURL: https://github.com/example/platform.git
    targetRevision: main
    path: kubernetes/node-api

  destination:
    server: https://kubernetes.default.svc
    namespace: production

  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

This example configures Argo CD to track the application manifests in Git.

`prune: true` means resources removed from the desired configuration can be removed from the cluster.

Use it carefully.

`selfHeal: true` allows Argo CD to reconcile certain manual changes that cause drift.

---

# SECTION 11 – INFRASTRUCTURE AS CODE

## 54. What Is Infrastructure as Code?

Infrastructure as Code (IaC) means defining and managing infrastructure through machine-readable configuration.

Instead of manually creating infrastructure in a cloud console, you describe it in code.

Example:

```text
Terraform Configuration
          |
          v
      Terraform
          |
          v
      AWS Provider
          |
    +-----+-----+
    |     |     |
    v     v     v
   VPC   EC2   RDS
```

Benefits:

- Repeatability.
- Version control.
- Automation.
- Consistency.
- Reviewable infrastructure changes.
- Reduced manual configuration.

---

## 55. Terraform

Terraform is an Infrastructure as Code tool.

It uses declarative configuration to manage infrastructure.

Important concepts:

1. Providers.
2. Resources.
3. Data Sources.
4. Variables.
5. Outputs.
6. Modules.
7. State.
8. Backends.
9. Dependency Graph.

### 55.1 Provider

A provider allows Terraform to interact with an infrastructure platform.

Example:

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

### 55.2 Resource

A resource defines infrastructure to create or manage.

Example:

```hcl
resource "aws_instance" "app_server" {

  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  tags = {
    Name = "node-api-server"
  }
}
```

The AMI ID must be valid for the selected AWS region and architecture.

### 55.3 Variables

```hcl
variable "instance_type" {

  type    = string

  default = "t3.micro"
}
```

Use:

```hcl
resource "aws_instance" "app" {

  ami           = "ami-xxxxxxxx"
  instance_type = var.instance_type
}
```

### 55.4 Outputs

```hcl
output "instance_public_ip" {

  value = aws_instance.app.public_ip
}
```

### 55.5 Data Sources

Data sources retrieve information about existing infrastructure.

Example:

```hcl
data "aws_availability_zones" "available" {

  state = "available"
}
```

### 55.6 Modules

Modules group reusable Terraform configurations.

Example:

```text
terraform/
    |
    +---- main.tf
    |
    +---- variables.tf
    |
    +---- outputs.tf
    |
    +---- modules/
             |
             +---- vpc/
             |
             +---- database/
             |
             +---- compute/
```

---

## 56. Terraform Workflow

Important commands:

```bash
# Initialize providers and backend
terraform init

# Format configuration
terraform fmt

# Validate configuration
terraform validate

# Preview infrastructure changes
terraform plan

# Apply changes
terraform apply

# Destroy managed infrastructure
terraform destroy
```

### Terraform Execution Flow

```text
Write Terraform Code
         |
         v
terraform init
         |
         v
terraform fmt
         |
         v
terraform validate
         |
         v
terraform plan
         |
         v
Review Changes
         |
         v
terraform apply
         |
         v
Infrastructure Created
```

`terraform plan` previews changes, while `terraform apply` executes them. [HashiCorp Developer](https://developer.hashicorp.com/terraform/cli/commands/plan?utm_source=chatgpt.com)

---

## 57. Terraform State

Terraform maintains state to understand the relationship between configuration and real infrastructure.

Commonly:

```text
terraform.tfstate
```

The state file may contain sensitive infrastructure information.

Important rules:

1. Do not commit state files to public repositories.
2. Protect state access.
3. Use a remote backend for team workflows.
4. Enable state locking where supported.
5. Back up state.
6. Avoid manually editing state.
7. Never expose state contents in logs.

### Remote State

For AWS, a common design uses an S3 backend with suitable state-locking support and access controls.

Remote backends centralize state and may provide locking depending on backend capabilities. [HashiCorp Developer](https://developer.hashicorp.com/terraform/language/state/backends?utm_source=chatgpt.com)

### Terraform Workspaces

Workspaces allow multiple state instances for a configuration.

However, separate production and development environments may be better managed using separate configurations, directories or backends depending on organizational requirements.

---

# SECTION 12 – CLOUD DEPLOYMENT ARCHITECTURE

## 58. AWS Services for Full-Stack Applications

Important AWS services:

| Service | Purpose |
|---|---|
| EC2 | Virtual machines |
| ECS | Container orchestration |
| EKS | Managed Kubernetes |
| Lambda | Serverless functions |
| S3 | Object storage |
| RDS | Managed relational databases |
| DynamoDB | Managed NoSQL database |
| ElastiCache | Managed in-memory caching |
| CloudFront | Content delivery network |
| Route 53 | DNS |
| ALB | Application Load Balancer |
| IAM | Identity and access management |
| VPC | Virtual networking |
| CloudWatch | Monitoring and logs |
| ECR | Container image registry |
| Secrets Manager | Secret management |

---

## 59. ECS vs EKS vs EC2 vs Lambda

| Feature | EC2 | ECS | EKS | Lambda |
|---|---|---|---|---|
| Main purpose | Virtual machines | Container orchestration | Kubernetes orchestration | Serverless execution |
| Container support | Manual or configured | Native | Native | Supported through specific deployment options |
| Infrastructure control | High | Medium | High | Lower |
| Kubernetes knowledge | Not required | Not required | Required | Not required |
| Operational complexity | Depends on setup | Moderate | Higher | Often lower |
| Best use | Custom server workloads | AWS container workloads | Kubernetes-based platforms | Event-driven functions |

### Which Should You Choose?

**EC2:** When you need direct server control.

**ECS:** When you want AWS-native container orchestration without Kubernetes complexity.

**EKS:** When you need Kubernetes compatibility, ecosystem integrations or advanced orchestration capabilities.

**Lambda:** When your workload fits event-driven or function-oriented execution.

Do not select EKS simply because Kubernetes is popular. It introduces additional operational responsibilities.

---

## 60. Production Architecture for a Full-Stack Application

Consider an enterprise e-commerce application.

```text
                         Users
                           |
                           v
                       Route 53
                           |
                           v
                       CloudFront
                           |
                           v
                    Application LB
                           |
              +------------+------------+
              |                         |
              v                         v
         Next.js Frontend          API Gateway
                                        |
                              +---------+---------+
                              |         |         |
                              v         v         v
                          User API  Product API Order API
                              |         |         |
                              +---------+---------+
                                        |
                           +------------+------------+
                           |            |            |
                           v            v            v
                          RDS        Redis       S3 Storage
                           |
                           v
                     Database Backups
```

Possible AWS implementation:

- Route 53 for DNS.
- CloudFront for CDN.
- Application Load Balancer for HTTP traffic.
- EKS for Kubernetes workloads.
- RDS for MySQL or PostgreSQL.
- ElastiCache for Redis.
- S3 for object storage.
- ECR for container images.
- IAM for access control.
- CloudWatch for logs and metrics.

### Why Use RDS Instead of Running MySQL Inside Kubernetes?

For many enterprise applications, managed databases reduce operational responsibilities.

The cloud provider manages much of the database infrastructure.

You still need to manage:

- Database schema.
- Access permissions.
- Backups and recovery settings.
- Performance.
- Query optimization.
- High availability configuration.
- Cost.

---

# SECTION 13 – DEPLOYMENT STRATEGIES

## 61. Rolling Deployment

Gradually replaces old application instances with new ones.

```text
Old: A A A

Step 1: B A A

Step 2: B B A

Step 3: B B B
```

Advantages:

- Gradual deployment.
- Lower infrastructure duplication.
- Built into Kubernetes Deployments.

Risks:

- Old and new versions may run simultaneously.
- Database changes must remain backward-compatible.

---

## 62. Blue-Green Deployment

Maintains two application environments.

```text
              Load Balancer
                    |
              Active Traffic
                    |
              +-----+-----+
              |           |
              v           v
          Blue v1      Green v2
          Current      New Version
```

Once the green environment is verified, traffic switches to it.

Advantages:

- Fast traffic switching.
- Easier application rollback.
- Separate validation environment.

Disadvantages:

- Higher infrastructure cost.
- Database compatibility remains important.

---

## 63. Canary Deployment

Deploys a new version to a small percentage of traffic.

```text
                Incoming Traffic
                       |
                       v
                Traffic Router
                       |
               +-------+-------+
               |               |
               v               v
          Version 1         Version 2
             90%                10%
```

Monitor:

- Error rate.
- Latency.
- CPU usage.
- Business metrics.
- Application logs.

Increase traffic gradually if the new version behaves correctly.

---

# SECTION 14 – PRODUCTION OBSERVABILITY

## 64. What Is Observability?

Observability helps engineers understand the internal state of an application through its external outputs.

Three primary pillars:

1. Logs.
2. Metrics.
3. Traces.

### Logs

Record individual events.

Example:

```json
{
  "level": "error",
  "message": "Database connection failed",
  "service": "order-api",
  "timestamp": "2026-11-25T18:30:00Z"
}
```

### Metrics

Represent measurements over time.

Examples:

- CPU utilization.
- Memory utilization.
- HTTP request count.
- Error rate.
- Request duration.

### Traces

Show how a request travels across services.

```text
Frontend
   |
   v
API Gateway
   |
   v
Order Service
   |
   +---- Payment Service
   |
   +---- Inventory Service
   |
   v
Database
```

### Tools

| Tool | Purpose |
|---|---|
| Prometheus | Metrics collection and monitoring |
| Grafana | Dashboards and visualization |
| OpenTelemetry | Telemetry instrumentation and collection |
| Jaeger | Distributed tracing |
| Loki | Log aggregation |

### Production Monitoring

Monitor:

- Application availability.
- HTTP 4xx and 5xx errors.
- Request latency.
- Database connections.
- Queue depth.
- Pod restarts.
- Node utilization.
- Memory pressure.
- Deployment health.

---

# SECTION 15 – COMPLETE ENTERPRISE PROJECT

## 65. Project: Enterprise E-Commerce Platform on Kubernetes

Now combine the concepts into a real-world architecture.

### Project Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Backend | Node.js + Express |
| Language | TypeScript |
| Database | MySQL |
| Cache | Redis |
| Containerization | Docker |
| Local Environment | Docker Compose |
| Orchestration | Kubernetes |
| Cloud | AWS |
| Container Registry | Amazon ECR |
| Kubernetes Packaging | Helm |
| CI/CD | GitHub Actions |
| GitOps | Argo CD |
| Infrastructure as Code | Terraform |
| Monitoring | Prometheus + Grafana |
| Tracing | OpenTelemetry |

### 65.1 Project Architecture

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
                    Load Balancer
                           |
                           v
                   Kubernetes Gateway
                           |
              +------------+------------+
              |                         |
              v                         v
         Next.js Frontend          Backend API
                                        |
                         +--------------+--------------+
                         |              |              |
                         v              v              v
                     User API       Product API     Order API
                         |              |              |
                         +--------------+--------------+
                                        |
                             +----------+----------+
                             |                     |
                             v                     v
                           MySQL                  Redis
                             |
                             v
                        S3 Storage
```

### 65.2 Local Development Architecture

```text
                 Docker Compose
                       |
           +-----------+-----------+
           |           |           |
           v           v           v
        Next.js      Express      MySQL
                      API           |
                                    |
                                    v
                                  Redis
```

### 65.3 Kubernetes Production Architecture

```text
                 AWS Kubernetes Cluster
                          |
                 +--------+--------+
                 |                 |
                 v                 v
             Frontend           Backend
             Deployment         Deployment
                 |                 |
             3 Replicas         3 Replicas
                 |                 |
                 +--------+--------+
                          |
                       Services
                          |
                          v
                  External Managed DB
                          |
                          v
                     AWS RDS
```

---

## 66. Example Kubernetes Deployment for Node.js

Create:

`api-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: node-api

  namespace: production

spec:
  replicas: 3

  selector:
    matchLabels:
      app: node-api

  template:
    metadata:
      labels:
        app: node-api

    spec:

      containers:

        - name: api

          image: your-registry/node-api:1.0.0

          ports:
            - containerPort: 3000

          envFrom:
            - configMapRef:
                name: api-config

            - secretRef:
                name: api-secret

          resources:

            requests:
              cpu: 250m
              memory: 256Mi

            limits:
              cpu: 1000m
              memory: 512Mi

          readinessProbe:
            httpGet:
              path: /health/ready
              port: 3000

            initialDelaySeconds: 5
            periodSeconds: 10

          livenessProbe:
            httpGet:
              path: /health/live
              port: 3000

            initialDelaySeconds: 15
            periodSeconds: 20
```

Create the namespace:

```bash
kubectl create namespace production
```

Deploy:

```bash
kubectl apply -f api-deployment.yaml
```

Check:

```bash
kubectl get deployments -n production

kubectl get pods -n production

kubectl describe deployment node-api -n production
```

---

## 67. Example Kubernetes Service

Create:

`api-service.yaml`

```yaml
apiVersion: v1
kind: Service

metadata:
  name: api-service

  namespace: production

spec:
  type: ClusterIP

  selector:
    app: node-api

  ports:
    - port: 80
      targetPort: 3000
      protocol: TCP
```

Deploy:

```bash
kubectl apply -f api-service.yaml
```

The frontend or other internal services can communicate with the backend through:

```text
http://api-service.production.svc.cluster.local
```

---

## 68. Example CI/CD Workflow

The intended deployment flow:

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
Test Application
      |
      v
Build Docker Image
      |
      v
Push to ECR
      |
      v
Update Deployment Configuration
      |
      v
Git Repository
      |
      v
Argo CD
      |
      v
Kubernetes
```

A mature workflow should also include:

- Unit tests.
- Integration tests.
- Dependency scanning.
- Container image scanning.
- Image signing or provenance verification.
- Deployment approvals.
- Smoke tests.
- Rollback procedures.

### GitHub Actions and AWS Authentication

Avoid storing long-lived AWS access keys in GitHub repository secrets when possible.

Use OpenID Connect (OIDC) to allow GitHub Actions to assume a restricted AWS IAM role using short-lived credentials.

The IAM trust policy should restrict which repository, branch or environment can assume the role. [GitHub Docs](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)

---

## 69. Terraform Project Structure

A production-oriented Terraform repository might look like:

```text
terraform/
    |
    +---- main.tf
    |
    +---- provider.tf
    |
    +---- variables.tf
    |
    +---- outputs.tf
    |
    +---- backend.tf
    |
    +---- environments/
    |       |
    |       +---- dev/
    |       |
    |       +---- staging/
    |       |
    |       +---- production/
    |
    +---- modules/
            |
            +---- vpc/
            |
            +---- eks/
            |
            +---- rds/
            |
            +---- redis/
            |
            +---- iam/
```

### Suggested Infrastructure

Terraform should provision:

1. VPC.
2. Public and private subnets.
3. Route tables.
4. Internet Gateway.
5. NAT Gateway where required.
6. EKS cluster.
7. Worker node capacity.
8. RDS database.
9. Redis infrastructure.
10. IAM roles.
11. Security groups.
12. ECR repositories.
13. Monitoring-related resources.

**Important:** A complete production EKS Terraform deployment requires many configuration decisions. Treat the structure above as an architectural blueprint, not a ready-to-apply infrastructure configuration.

---

# SECTION 16 – ENTERPRISE RELIABILITY AND PRODUCTION READINESS

## 70. High Availability

High Availability (HA) means designing systems to minimize downtime.

Important techniques:

- Multiple application replicas.
- Multiple availability zones.
- Load balancing.
- Health checks.
- Database high availability.
- Automated recovery.
- Monitoring.
- Backup and restoration.

Example:

```text
               Load Balancer
                      |
          +-----------+-----------+
          |                       |
          v                       v
       AZ - 1                  AZ - 2
          |                       |
       API Pod                 API Pod
          |                       |
          +-----------+-----------+
                      |
                      v
                Managed Database
```

---

## 71. Disaster Recovery

Disaster Recovery (DR) is the process of restoring systems after a major failure.

Important concepts:

### RPO – Recovery Point Objective

The maximum acceptable amount of data loss measured in time.

Example:

```text
RPO = 15 minutes
```

This means the recovery design aims to limit data loss to approximately the last 15 minutes of changes.

### RTO – Recovery Time Objective

The maximum acceptable time required to restore service.

Example:

```text
RTO = 2 hours
```

This means the recovery design aims to restore service within two hours.

### DR Strategies

| Strategy | Description | Relative Cost |
|---|---|---|
| Backup and Restore | Restore from backups | Lower |
| Pilot Light | Maintain minimal core infrastructure | Medium |
| Warm Standby | Maintain a scaled-down working environment | Higher |
| Multi-Site Active/Active | Run services in multiple locations | Very high |

---

## 72. Graceful Shutdown

A production Node.js application should shut down cleanly when Kubernetes terminates a Pod.

Example:

```javascript
const server = app.listen(3000);

async function shutdown(signal) {
  console.log(`${signal} received`);

  server.close(async () => {
    try {
      await database.close();

      console.log("Application shutdown complete");

      process.exit(0);

    } catch (error) {
      console.error("Shutdown error:", error);

      process.exit(1);
    }
  });

  setTimeout(() => {
    console.error("Forced shutdown");

    process.exit(1);
  }, 10000).unref();
}

process.on("SIGTERM", () => {
  shutdown("SIGTERM");
});

process.on("SIGINT", () => {
  shutdown("SIGINT");
});
```

In a production implementation, also account for:

- In-flight requests.
- Background jobs.
- Message consumer acknowledgements.
- Database transactions.
- WebSocket connections.
- Shutdown timeout coordination.

---

# SECTION 17 – IMPORTANT COMPARISONS

## 73. Docker vs Kubernetes

| Docker | Kubernetes |
|---|---|
| Container platform and tooling | Container orchestration platform |
| Builds images | Schedules and manages workloads |
| Runs containers | Manages Pods and containers |
| Docker Compose manages local multi-container applications | Kubernetes manages cluster workloads |
| Does not provide full cluster orchestration by itself | Provides cluster orchestration |

Docker and Kubernetes are complementary technologies, not direct replacements for one another.

## 74. Docker Compose vs Kubernetes

| Docker Compose | Kubernetes |
|---|---|
| Simple multi-container management | Full container orchestration |
| Excellent for local development | Excellent for distributed deployments |
| Lower operational complexity | Higher operational complexity |
| Limited orchestration capabilities | Advanced scheduling and scaling |
| Single-host oriented by default | Cluster-based architecture |

## 75. Helm vs Terraform

| Helm | Terraform |
|---|---|
| Manages Kubernetes application resources | Manages infrastructure resources |
| Kubernetes package manager | Infrastructure as Code tool |
| Uses charts and templates | Uses providers, resources and modules |
| Deploys application workloads | Provisions cloud infrastructure |

## 76. GitOps vs Traditional CI/CD

| Traditional CI/CD | GitOps |
|---|---|
| Pipeline often pushes changes to the environment | Controller pulls desired state from Git |
| Deployment scripts execute changes | Reconciliation controller manages changes |
| Drift detection may require separate tooling | Drift detection is central to the model |
| Deployment logic often resides in pipeline | Desired state resides in Git |

---

# SECTION 18 – COMMON MISTAKES AND BEST PRACTICES

## 77. Docker Mistakes

| Mistake | Better Approach |
|---|---|
| Using `latest` tags indiscriminately | Pin image versions or digests |
| Running as root | Use non-root users |
| Including `.env` in images | Inject configuration securely |
| Using large images unnecessarily | Use multi-stage builds |
| Storing database data inside containers | Use persistent storage |
| Installing development dependencies in production | Install production dependencies only |
| Ignoring image vulnerabilities | Scan and update images |

## 78. Kubernetes Mistakes

| Mistake | Better Approach |
|---|---|
| Deploying standalone Pods for applications | Use Deployments or appropriate controllers |
| Not configuring resource requests | Define resource requests |
| No readiness probes | Configure readiness probes |
| Storing credentials in Git | Use secure secret management |
| Exposing databases publicly | Use private networking |
| Running everything in the default namespace | Organize workloads into namespaces |
| Ignoring RBAC | Apply least privilege |
| No backups | Establish and test backup procedures |
| Using static replica counts for variable traffic | Evaluate HPA and node autoscaling |

## 79. DevOps Mistakes

| Mistake | Better Approach |
|---|---|
| Manual production deployments | Automate deployment |
| Hardcoded cloud credentials | Use OIDC or workload identity |
| No infrastructure version control | Use Terraform or another IaC tool |
| No deployment rollback strategy | Define rollback procedures |
| No monitoring | Implement observability |
| Deploying without tests | Use CI quality gates |
| Unreviewed infrastructure changes | Review Terraform plans |
| No production approval controls | Use protected environments |

---

# SECTION 19 – INTERVIEW QUESTIONS

Below are 50 important interview questions.

## Beginner Level

1. What is cloud computing?
2. What is the difference between IaaS, PaaS and SaaS?
3. What is the difference between public cloud and private cloud?
4. What is an availability zone?
5. What is Docker?
6. What is a Docker image?
7. What is a Docker container?
8. What is a Dockerfile?
9. What is Docker Compose?
10. What is Kubernetes?

## Intermediate Level

11. What is the difference between Docker and Kubernetes?
12. What is a Kubernetes Pod?
13. What is a ReplicaSet?
14. What is a Deployment?
15. What is the difference between Deployment and StatefulSet?
16. What is a DaemonSet?
17. What is a Kubernetes Service?
18. What is the difference between ClusterIP and NodePort?
19. What is Ingress?
20. What is a ConfigMap?
21. What is a Kubernetes Secret?
22. What is a Persistent Volume?
23. What is a Persistent Volume Claim?
24. What is a StorageClass?
25. What is a Kubernetes Namespace?

## Advanced Level

26. Explain Kubernetes control plane architecture.
27. What is etcd?
28. How does Kubernetes scheduling work?
29. What is the difference between resource requests and limits?
30. Explain liveness, readiness and startup probes.
31. What is HPA?
32. What is Cluster Autoscaler?
33. What is the difference between HPA and Cluster Autoscaler?
34. What are taints and tolerations?
35. What are affinity and anti-affinity rules?
36. What is RBAC?
37. What is NetworkPolicy?
38. What is Helm?
39. What is a Kubernetes Operator?
40. What is a service mesh?

## Expert Level

41. Explain the difference between Ingress and Gateway API.
42. Explain Istio's control plane and data plane.
43. How does mTLS work in a service mesh?
44. Explain GitOps architecture.
45. How does Argo CD detect and correct configuration drift?
46. Explain Terraform state and remote backends.
47. How would you design a highly available EKS cluster?
48. How would you implement a secure CI/CD pipeline using GitHub Actions and AWS?
49. How would you implement zero-downtime Kubernetes deployments?
50. How would you design disaster recovery for a multi-region application?

---

# SECTION 20 – PRACTICAL ASSIGNMENTS

Complete these progressively.

## Level 1 – Cloud Fundamentals

1. Explain IaaS, PaaS and SaaS with examples.
2. Create a cloud architecture diagram for a Node.js application.
3. Compare AWS EC2, ECS and EKS.
4. Explain AWS Regions and Availability Zones.
5. Design a public and private subnet architecture.
6. Explain security groups and network ACLs.
7. Create an IAM policy for read-only S3 access.
8. Explain the shared responsibility model.
9. Design a cloud cost optimization strategy.
10. Explain hybrid cloud and multi-cloud.

## Level 2 – Docker

11. Install Docker.
12. Create a Node.js Dockerfile.
13. Build a Docker image.
14. Run a container.
15. Inspect container logs.
16. Create a custom Docker network.
17. Run a Node.js application and Redis on the same network.
18. Create a named volume.
19. Run MySQL using a persistent volume.
20. Create a `.dockerignore` file.

## Level 3 – Advanced Docker

21. Create a multi-stage Dockerfile.
22. Dockerize a TypeScript application.
23. Create a production Docker image using a non-root user.
24. Configure Docker health checks.
25. Create a Compose file with Node.js and MySQL.
26. Add Redis to the Compose architecture.
27. Configure service health checks.
28. Create separate frontend and backend networks.
29. Configure development and production Compose overrides.
30. Scan a Docker image for vulnerabilities.

## Level 4 – Kubernetes

31. Install Minikube or another local Kubernetes environment.
32. Learn basic `kubectl` commands.
33. Create a Pod.
34. Create a Deployment.
35. Scale a Deployment.
36. Create a ReplicaSet.
37. Configure rolling updates.
38. Perform a Deployment rollback.
39. Create a ConfigMap.
40. Create a Kubernetes Secret.

## Level 5 – Kubernetes Networking and Storage

41. Create a ClusterIP Service.
42. Create a NodePort Service.
43. Deploy an Ingress Controller.
44. Configure an Ingress resource.
45. Experiment with Gateway API.
46. Create a Persistent Volume Claim.
47. Mount persistent storage into a Pod.
48. Create a StatefulSet.
49. Configure Kubernetes DNS service discovery.
50. Create a NetworkPolicy.

## Level 6 – Kubernetes Production Engineering

51. Configure CPU and memory requests.
52. Configure resource limits.
53. Configure liveness probes.
54. Configure readiness probes.
55. Configure startup probes.
56. Implement HPA.
57. Configure PodDisruptionBudget.
58. Configure node affinity.
59. Configure Pod anti-affinity.
60. Configure RBAC.

## Level 7 – Helm and Service Mesh

61. Create a Helm chart.
62. Deploy a Node.js application using Helm.
63. Create separate development and production values files.
64. Upgrade a Helm release.
65. Roll back a Helm release.
66. Build a simple Kubernetes Operator conceptually.
67. Install Istio in a test cluster.
68. Configure service-to-service mTLS.
69. Implement traffic splitting.
70. Compare Istio and Linkerd.

## Level 8 – CI/CD and GitOps

71. Create a GitHub Actions CI workflow.
72. Run automated tests through GitHub Actions.
73. Build a Docker image through GitHub Actions.
74. Push an image to GitHub Container Registry.
75. Push an image to AWS ECR.
76. Configure GitHub Actions OIDC with AWS.
77. Deploy an application to Kubernetes through a pipeline.
78. Install Argo CD.
79. Configure an Argo CD Application.
80. Implement GitOps-based deployment.

## Level 9 – Terraform and AWS

81. Install Terraform.
82. Create an AWS provider configuration.
83. Create an EC2 instance using Terraform.
84. Create a VPC.
85. Create public and private subnets.
86. Configure security groups.
87. Create an S3 bucket.
88. Configure Terraform variables and outputs.
89. Create reusable Terraform modules.
90. Configure a remote Terraform backend.

## Level 10 – Enterprise Project

91. Create a Next.js frontend Docker image.
92. Create a Node.js API Docker image.
93. Configure MySQL and Redis for local development.
94. Create Kubernetes Deployments for frontend and backend.
95. Configure Kubernetes Services.
96. Implement HPA.
97. Create Helm charts for both applications.
98. Create a GitHub Actions CI/CD pipeline.
99. Configure Argo CD GitOps deployment.
100. Provision AWS infrastructure through Terraform.
101. Add Prometheus monitoring.
102. Create Grafana dashboards.
103. Integrate OpenTelemetry.
104. Implement a rolling deployment.
105. Implement a canary deployment.
106. Configure centralized logging.
107. Implement database backup and restoration.
108. Configure application graceful shutdown.
109. Perform a Kubernetes failure recovery exercise.
110. Document the complete production architecture.

---

# SECTION 21 – QUICK REVISION NOTES

| Concept | Important Point |
|---|---|
| Cloud Computing | Computing resources delivered over a network |
| IaaS | Infrastructure as a Service |
| PaaS | Platform as a Service |
| SaaS | Software as a Service |
| FaaS | Function as a Service |
| VPC | Isolated virtual network |
| Availability Zone | Isolated infrastructure location within a region |
| Docker | Containerization platform |
| Docker Image | Immutable container template |
| Docker Container | Running instance of an image |
| Dockerfile | Image build instructions |
| Docker Compose | Multi-container application management |
| Docker Volume | Persistent container data storage |
| Kubernetes | Container orchestration platform |
| Pod | Smallest deployable Kubernetes unit |
| ReplicaSet | Maintains a desired number of Pod replicas |
| Deployment | Manages stateless workloads and updates |
| StatefulSet | Manages stateful workloads |
| DaemonSet | Runs Pods on eligible nodes |
| Job | Runs a task to completion |
| CronJob | Schedules Jobs |
| ConfigMap | Non-sensitive configuration |
| Secret | Sensitive configuration storage |
| Service | Stable network endpoint |
| Ingress | HTTP/HTTPS routing API |
| Gateway API | Extensible Kubernetes networking API |
| PV | Persistent Volume |
| PVC | Persistent Volume Claim |
| StorageClass | Storage provisioning configuration |
| HPA | Scales Pods |
| Cluster Autoscaler | Scales cluster nodes |
| RBAC | Role-Based Access Control |
| NetworkPolicy | Controls Pod network traffic |
| Helm | Kubernetes package manager |
| Operator | Application-specific Kubernetes automation |
| Service Mesh | Service-to-service communication infrastructure |
| Istio | Service mesh platform |
| CI | Continuous Integration |
| CD | Continuous Delivery/Deployment |
| GitOps | Git-based declarative operations |
| Argo CD | Kubernetes GitOps delivery tool |
| IaC | Infrastructure as Code |
| Terraform | Infrastructure provisioning tool |
| RPO | Recovery Point Objective |
| RTO | Recovery Time Objective |
| HA | High Availability |
| DR | Disaster Recovery |

---

# SECTION 22 – PHASE 71 COMPLETION CHECKLIST

You should be able to confidently perform the following tasks before considering this phase complete.

### Cloud Computing

- [ ] Explain cloud service models.
- [ ] Design AWS networking architecture.
- [ ] Understand VPC, subnets and security groups.
- [ ] Configure IAM permissions.
- [ ] Understand availability zones and high availability.

### Docker

- [ ] Create Dockerfiles.
- [ ] Build and run images.
- [ ] Configure Docker networking.
- [ ] Configure volumes.
- [ ] Use multi-stage builds.
- [ ] Create Docker Compose environments.
- [ ] Optimize production images.

### Kubernetes

- [ ] Explain cluster architecture.
- [ ] Deploy Pods and Deployments.
- [ ] Manage ReplicaSets and StatefulSets.
- [ ] Configure Services.
- [ ] Configure Ingress and understand Gateway API.
- [ ] Configure ConfigMaps and Secrets.
- [ ] Configure persistent storage.
- [ ] Implement HPA.
- [ ] Configure health checks.
- [ ] Understand scheduling and RBAC.

### Advanced Kubernetes

- [ ] Create Helm charts.
- [ ] Understand Kubernetes Operators.
- [ ] Understand service mesh architecture.
- [ ] Understand Istio and Linkerd.
- [ ] Implement NetworkPolicies.
- [ ] Understand Pod security.

### DevOps

- [ ] Build GitHub Actions workflows.
- [ ] Build and push Docker images.
- [ ] Configure secure cloud authentication.
- [ ] Understand GitOps.
- [ ] Deploy applications using Argo CD.
- [ ] Provision infrastructure using Terraform.
- [ ] Implement deployment rollback.
- [ ] Understand monitoring and observability.

### Enterprise Architecture

- [ ] Design a production cloud architecture.
- [ ] Understand multi-AZ deployments.
- [ ] Design disaster recovery.
- [ ] Implement graceful shutdown.
- [ ] Understand autoscaling.
- [ ] Understand cost optimization.
- [ ] Build a complete Kubernetes deployment pipeline.

---

# SECTION 23 – OFFICIAL DOCUMENTATION

Use these resources for deeper learning and practical implementation.

### Cloud and AWS

1. [AWS Documentation](https://docs.aws.amazon.com/?utm_source=chatgpt.com)
2. [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html?utm_source=chatgpt.com)
3. [Amazon EC2](https://docs.aws.amazon.com/ec2/?utm_source=chatgpt.com)
4. [Amazon EKS](https://docs.aws.amazon.com/eks/?utm_source=chatgpt.com)
5. [Amazon ECS](https://docs.aws.amazon.com/ecs/?utm_source=chatgpt.com)
6. [AWS IAM](https://docs.aws.amazon.com/iam/?utm_source=chatgpt.com)
7. [AWS VPC](https://docs.aws.amazon.com/vpc/?utm_source=chatgpt.com)

### Docker

8. [Docker Documentation](https://docs.docker.com/?utm_source=chatgpt.com)
9. [Docker Build Best Practices](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)
10. [Docker Multi-Stage Builds](https://docs.docker.com/build/building/multi-stage?utm_source=chatgpt.com)
11. [Docker Compose Documentation](https://docs.docker.com/compose?utm_source=chatgpt.com)
12. [Docker Networking](https://docs.docker.com/compose/how-tos/networking?utm_source=chatgpt.com)

### Kubernetes

13. [Kubernetes Documentation](https://kubernetes.io/docs/home/?utm_source=chatgpt.com)
14. [Kubernetes Workloads](https://kubernetes.io/docs/concepts/workloads/?utm_source=chatgpt.com)
15. [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)
16. [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?utm_source=chatgpt.com)
17. [Kubernetes Gateway API](https://gateway-api.sigs.k8s.io/?utm_source=chatgpt.com)
18. [Kubernetes Storage](https://kubernetes.io/docs/concepts/storage/?utm_source=chatgpt.com)
19. [Kubernetes Security](https://kubernetes.io/docs/concepts/security/?utm_source=chatgpt.com)
20. [Kubernetes Autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/?utm_source=chatgpt.com)

### Helm and Service Mesh

21. [Helm Documentation](https://helm.sh/docs/?utm_source=chatgpt.com)
22. [Helm Charts](https://helm.sh/docs/topics/charts/?utm_source=chatgpt.com)
23. [Istio Documentation](https://istio.io/latest/docs/?utm_source=chatgpt.com)
24. [Istio Architecture](https://istio.io/latest/docs/ops/deployment/architecture/?utm_source=chatgpt.com)
25. [Linkerd Documentation](https://linkerd.io/2/overview/?utm_source=chatgpt.com)

### CI/CD and GitOps

26. [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
27. [GitHub Actions with AWS OIDC](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)
28. [Argo CD Documentation](https://argo-cd.readthedocs.io/en/stable/?utm_source=chatgpt.com)

### Terraform and Observability

29. [Terraform Documentation](https://developer.hashicorp.com/terraform/docs?utm_source=chatgpt.com)
30. [Terraform State and Backends](https://developer.hashicorp.com/terraform/language/state/backends?utm_source=chatgpt.com)
31. [Prometheus Documentation](https://prometheus.io/docs/?utm_source=chatgpt.com)
32. [Grafana Documentation](https://grafana.com/docs/?utm_source=chatgpt.com)
33. [OpenTelemetry Documentation](https://opentelemetry.io/docs/?utm_source=chatgpt.com)

---

# PHASE 71 – FINAL SUMMARY

In this phase, you studied the complete journey from cloud infrastructure to enterprise application deployment.

The overall engineering workflow looks like this:

```text
                APPLICATION DEVELOPMENT
                          |
                          v
                    Next.js + Node.js
                          |
                          v
                     Docker Images
                          |
                          v
                    Docker Registry
                          |
                          v
                      Kubernetes
                          |
             +------------+------------+
             |            |            |
             v            v            v
           Helm         Services     Storage
             |            |            |
             +------------+------------+
                          |
                          v
                     Cloud Platform
                          |
                          v
                       AWS EKS
                          |
              +-----------+-----------+
              |                       |
              v                       v
          GitHub Actions            Terraform
              |                       |
              v                       v
             CI/CD               Infrastructure
              |
              v
           Argo CD
              |
              v
          GitOps Deployment
              |
              v
       Prometheus + Grafana
              |
              v
          Observability
```

**The key takeaway:** Building an application is only one part of software engineering. Enterprise engineering also requires designing infrastructure, automating deployments, managing containers, securing workloads, monitoring systems and preparing for failures.

---

# NEXT PHASE

## PHASE 72 – Advanced Cloud Security, DevSecOps, Zero Trust Architecture and Enterprise Security Engineering

Topics will include:

1. Advanced Cybersecurity Fundamentals.
2. Network Security.
3. Application Security.
4. Cloud Security Architecture.
5. Zero Trust Architecture.
6. Identity and Access Management.
7. OAuth 2.0 and OpenID Connect.
8. SAML Authentication.
9. JWT Security.
10. Multi-Factor Authentication.
11. Secrets Management.
12. AWS Security Architecture.
13. Kubernetes Security Hardening.
14. Container Security.
15. Docker Image Security.
16. Software Supply Chain Security.
17. SAST and DAST.
18. Dependency Vulnerability Scanning.
19. Software Composition Analysis.
20. SBOM.
21. Code Signing.
22. Artifact Signing.
23. Secure CI/CD Pipelines.
24. DevSecOps Architecture.
25. WAF and DDoS Protection.
26. API Security.
27. Rate Limiting and Abuse Prevention.
28. Encryption at Rest and in Transit.
29. TLS and Certificate Management.
30. Key Management Systems.
31. AWS KMS.
32. Security Monitoring and SIEM.
33. Intrusion Detection and Prevention.
34. Vulnerability Management.
35. Penetration Testing Fundamentals.
36. OWASP Top 10.
37. OWASP API Security Top 10.
38. Security Incident Response.
39. Compliance and Governance.
40. Enterprise Zero Trust Security Project.

**Phase 71 is complete. Continue to Phase 72 when you're ready.**