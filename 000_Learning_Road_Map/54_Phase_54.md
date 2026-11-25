# Phase 54 – Advanced DevOps, Kubernetes, Service Mesh and Production Infrastructure

**Level:** Advanced  
**Category:** DevOps / Cloud Native Engineering / Container Orchestration  
**Primary Technologies:** Docker, Kubernetes, Helm, Istio, Argo CD  
**Additional Technologies:** Nginx, Prometheus, Grafana, OpenTelemetry, GitHub Actions

---

# 1. Learning Objectives

In Phase 53, we studied Microservices Architecture and Distributed Systems.

Now we will learn how to deploy, manage, scale, secure and monitor microservices in a production environment.

By the end of this phase, you should understand:

1. Advanced Docker Architecture.
2. Docker Networking.
3. Docker Volumes and Storage.
4. Multi-Stage Docker Builds.
5. Docker Security.
6. Kubernetes Architecture.
7. Kubernetes Cluster Components.
8. Pods, Deployments and ReplicaSets.
9. Services and Ingress.
10. Kubernetes Gateway API.
11. ConfigMaps and Secrets.
12. Persistent Volumes and Storage Classes.
13. Kubernetes Networking.
14. Horizontal Pod Autoscaling.
15. Resource Requests and Limits.
16. Liveness, Readiness and Startup Probes.
17. Helm Charts.
18. Kubernetes Operators.
19. Service Mesh Architecture.
20. Istio Fundamentals.
21. mTLS with Service Mesh.
22. Traffic Management.
23. Canary Deployments.
24. Blue-Green Deployments.
25. GitOps.
26. Argo CD.
27. Infrastructure Monitoring.
28. Production Logging and Tracing.
29. Kubernetes Security.
30. Production Deployment Architecture.

---

# PART 1: ADVANCED DOCKER ARCHITECTURE

## 2. Understanding Docker Architecture

Docker is a containerization platform that allows applications to run in isolated environments.

Before understanding Kubernetes, you should understand how Docker works internally.

### Docker Architecture

Docker uses a client-server architecture.

```text
                 Docker Client
                      |
                      |
                  Docker API
                      |
                      v
                 Docker Daemon
                  (dockerd)
                      |
          -------------------------
          |           |           |
          v           v           v
       Images     Containers   Networks
          |
          v
       Volumes
```

### Important Components

| Component | Responsibility |
|---|---|
| Docker Client | Accepts commands from users |
| Docker Daemon | Manages Docker objects |
| Docker Engine API | Allows clients to communicate with the daemon |
| Docker Image | Read-only template used to create containers |
| Docker Container | Running instance of an image |
| Docker Network | Enables container communication |
| Docker Volume | Provides persistent storage |
| Container Registry | Stores and distributes images |

### Example

When you execute:

```bash
docker run -d -p 3000:3000 my-node-app
```

Docker performs operations such as:

1. Checks whether the image exists locally.
2. Pulls the image if necessary.
3. Creates a container.
4. Configures its network.
5. Starts the container.
6. Maps host port `3000` to container port `3000`.

### Important Commands

```bash
# List running containers
docker ps

# List all containers
docker ps -a

# List images
docker images

# List networks
docker network ls

# List volumes
docker volume ls

# Container logs
docker logs container_name

# Execute a command inside a container
docker exec -it container_name sh

# Inspect a container
docker inspect container_name
```

---

## 3. Docker Networking

Docker networking allows containers to communicate with:

- Other containers.
- The host machine.
- External networks.
- The internet.

### Docker Network Types

| Network Type | Description |
|---|---|
| Bridge | Default network driver for standalone containers |
| Host | Container shares the host network namespace |
| None | Disables normal container networking |
| Overlay | Connects containers across Docker hosts |
| Macvlan | Assigns containers addresses on a physical network |

### 3.1 Bridge Network

A bridge network allows containers on the same Docker network to communicate.

```text
                Docker Host
                     |
               Bridge Network
                     |
          -----------------------
          |                     |
          v                     v
     Container A           Container B
       Port 3000              Port 4000
```

Create a network:

```bash
docker network create app-network
```

Run containers:

```bash
docker run -d \
  --name auth-service \
  --network app-network \
  my-auth-app
```

```bash
docker run -d \
  --name order-service \
  --network app-network \
  my-order-app
```

Now the Order Service can access Auth Service using its container name:

```text
http://auth-service:3000
```

This works because Docker provides DNS-based service discovery on user-defined bridge networks.

### 3.2 Host Network

In host networking mode, a container shares the host's network namespace.

```bash
docker run --network host nginx
```

The container does not use normal bridge-network port mapping.

For example, if Nginx listens on port 80, it uses the host's port 80 directly.

**Important:** Host networking behaves differently across Linux, Windows and macOS Docker environments. Do not assume identical behavior on every platform.

### 3.3 None Network

The container has no normal external network connectivity.

```bash
docker run --network none my-app
```

Useful for certain isolated processing tasks.

### 3.4 Overlay Network

Overlay networking allows containers on different Docker hosts to communicate through a shared virtual network.

```text
          Docker Host 1             Docker Host 2
          ------------              ------------
          Container A               Container B
               |                         |
               v                         v
          ---------------------------------------
                     Overlay Network
```

It is commonly associated with multi-host container networking and Docker Swarm.

### Docker Networking Best Practices

1. Use user-defined networks.
2. Avoid exposing every container port publicly.
3. Use internal networks for private service communication.
4. Use DNS names instead of hardcoded container IPs.
5. Separate application and database networks where appropriate.
6. Restrict external access to databases.

---

## 4. Docker Volumes and Storage

Containers are designed to be replaceable.

When a container is deleted, its writable layer is normally deleted too.

Therefore, persistent application data should not depend on the container's writable layer.

### What Is a Docker Volume?

A Docker volume provides persistent storage managed by Docker.

```text
Container
    |
    v
Docker Volume
    |
    v
Persistent Data
```

### Create a Volume

```bash
docker volume create mysql-data
```

Run MySQL:

```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=secret \
  -v mysql-data:/var/lib/mysql \
  mysql:8
```

Even if the container is removed, the volume can remain.

### Types of Docker Storage

| Storage Type | Description |
|---|---|
| Docker Volume | Storage managed by Docker |
| Bind Mount | Maps a host filesystem path into a container |
| tmpfs Mount | Stores temporary data in memory |

### Volume vs Bind Mount

**Volume:**

```bash
docker run -v mysql-data:/var/lib/mysql mysql:8
```

**Bind Mount:**

```bash
docker run -v ./data:/app/data my-app
```

### When Should You Use Them?

Use Docker volumes for persistent application data.

Use bind mounts for:

- Local development.
- Configuration files.
- Source-code synchronization.
- Host-managed files.

Use tmpfs for temporary sensitive or short-lived data that should not be written to persistent storage.

### Important Production Consideration

Docker volumes on one machine do not automatically provide distributed storage across a Kubernetes cluster.

For Kubernetes production workloads, persistent storage is usually provisioned through a storage system and PersistentVolumes.

---

# PART 2: MULTI-STAGE DOCKER BUILDS

## 5. What Is a Multi-Stage Docker Build?

A multi-stage Docker build uses multiple `FROM` instructions to create separate build and runtime stages.

Its main purpose is to keep the final image smaller and avoid including unnecessary build tools.

### Single-Stage Build

```dockerfile
FROM node:22

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

Problems:

- Development dependencies may remain.
- Build tools remain.
- The image may be unnecessarily large.
- The runtime contains files that are not needed.

### Multi-Stage Build

```dockerfile
# Stage 1: Build
FROM node:22-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build


# Stage 2: Production
FROM node:22-alpine AS production

WORKDIR /app

ENV NODE_ENV=production

COPY package*.json ./

RUN npm ci --omit=dev

COPY --from=builder /app/dist ./dist

EXPOSE 3000

CMD ["node", "dist/server.js"]
```

### How It Works

```text
          Builder Stage
                |
                |-- Install Dependencies
                |-- Copy Source Code
                |-- Build Application
                |
                v
          Build Artifacts
                |
                v
         Production Stage
                |
                |-- Install Production Dependencies
                |-- Copy Build Output
                |
                v
          Final Docker Image
```

The final image does not need to contain the complete builder environment.

Docker recommends multi-stage builds to separate build-time dependencies from the final runtime image. [Docker Documentation](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

### Benefits

1. Smaller final images.
2. Reduced attack surface.
3. Better deployment efficiency.
4. Faster image distribution in many environments.
5. Cleaner separation of development and production dependencies.

### Important: `.dockerignore`

Create a `.dockerignore` file.

```text
node_modules
.git
.env
.env.*
coverage
dist
npm-debug.log
```

Be careful: if your build requires a file, do not exclude it accidentally.

Never copy production secrets into the Docker build context.

### Additional Production Practices

- Pin base-image versions.
- Scan images for vulnerabilities.
- Run as a non-root user.
- Avoid unnecessary operating-system packages.
- Use BuildKit.
- Avoid embedding secrets in image layers.
- Rebuild images regularly to include security updates.

---

# PART 3: DOCKER SECURITY

## 6. Docker Security Fundamentals

Containers provide process isolation, but they are not equivalent to complete virtual-machine isolation.

A containerized application still shares the host kernel.

Therefore, Docker security is essential.

### 6.1 Run Containers as Non-Root

Avoid running applications as root.

Example Dockerfile:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY --chown=node:node . .

USER node

EXPOSE 3000

CMD ["node", "server.js"]
```

### 6.2 Avoid Privileged Containers

Avoid:

```bash
docker run --privileged my-app
```

Privileged containers receive substantially expanded access to host resources.

### 6.3 Limit Container Resources

```bash
docker run -d \
  --memory=512m \
  --cpus=1 \
  my-app
```

This limits memory and CPU consumption.

### 6.4 Never Hardcode Secrets

Avoid:

```dockerfile
ENV DATABASE_PASSWORD=mysecretpassword
```

Use a proper secret-management mechanism.

### 6.5 Image Scanning

Scan images for known vulnerabilities before deployment.

Tools include:

- Trivy.
- Docker Scout.
- Grype.

### 6.6 Image Signing and Provenance

Production supply-chain security can also include:

- Image signing.
- Build provenance.
- Software Bill of Materials (SBOM).
- Dependency scanning.
- Trusted registries.

### Docker Security Checklist

- [ ] Use trusted base images.
- [ ] Run as non-root.
- [ ] Avoid privileged containers.
- [ ] Restrict Linux capabilities.
- [ ] Avoid mounting sensitive host paths.
- [ ] Scan container images.
- [ ] Do not expose unnecessary ports.
- [ ] Protect secrets.
- [ ] Apply resource limits.
- [ ] Keep images updated.

---

# PART 4: KUBERNETES FUNDAMENTALS

## 7. What Is Kubernetes?

Kubernetes is an open-source container orchestration platform.

It automates the deployment, scaling and management of containerized applications.

Docker helps you package and run containers.

Kubernetes helps you manage containers across a cluster of machines.

### Why Do We Need Kubernetes?

Imagine you have 20 microservices.

Each service might need:

- Multiple container instances.
- Load balancing.
- Automatic recovery.
- Scaling.
- Service discovery.
- Configuration management.
- Storage management.
- Deployment automation.

Managing all of this manually becomes difficult.

Kubernetes automates many of these operations.

### Example Without Kubernetes

```text
Server 1
   |
   |-- Auth Container
   |-- Product Container

Server 2
   |
   |-- Order Container
   |-- Payment Container

Server 3
   |
   |-- Notification Container
```

You must manually manage:

- Container failures.
- Scaling.
- Deployment.
- Networking.
- Resource allocation.

### With Kubernetes

```text
                Kubernetes Cluster
                         |
             -------------------------
             |           |           |
             v           v           v
           Node 1      Node 2      Node 3
             |           |           |
             v           v           v
           Pods        Pods        Pods
             |           |           |
             -------------------------
```

Kubernetes continuously works toward the desired state described in your configuration.

Official documentation: [Kubernetes Concepts Documentation](https://kubernetes.io/docs/concepts/index.html?utm_source=chatgpt.com)

---

# PART 5: KUBERNETES ARCHITECTURE

## 8. Kubernetes Cluster Architecture

A Kubernetes cluster consists of:

1. Control Plane.
2. Worker Nodes.

```text
                 Kubernetes Cluster
                          |
             --------------------------
             |                        |
             v                        v
        CONTROL PLANE             WORKER NODES
             |                        |
      ---------------          ---------------
      |      |      |          |      |      |
      v      v      v          v      v      v
    API   Scheduler etcd      Kubelet Kubelet Kubelet
   Server                      Pods    Pods    Pods
      |
      v
 Controller Manager
```

## 9. Control Plane Components

The Control Plane manages the overall Kubernetes cluster.

### 9.1 API Server

The API Server is the main entry point to the Kubernetes control plane.

All major Kubernetes operations communicate with it.

Example:

```bash
kubectl get pods
```

The `kubectl` command communicates with the Kubernetes API Server.

Responsibilities:

- Exposes the Kubernetes API.
- Validates API requests.
- Authenticates and authorizes requests.
- Coordinates access to cluster state.

### 9.2 etcd

`etcd` is a distributed key-value store used to store Kubernetes cluster state.

It stores information such as:

- Cluster configuration.
- Kubernetes objects.
- Desired state.
- Resource metadata.

Think of etcd as the cluster's persistent state store.

### 9.3 Scheduler

The Scheduler decides which worker node should run a newly created Pod.

It considers factors such as:

- Available resources.
- Scheduling constraints.
- Node selectors.
- Affinity and anti-affinity.
- Taints and tolerations.

Example:

```text
New Pod
   |
   v
Scheduler
   |
   |-- Node 1: Insufficient CPU
   |
   |-- Node 2: Sufficient CPU
   |
   |-- Node 3: Not eligible
   |
   v
Select Node 2
```

### 9.4 Controller Manager

The Controller Manager runs controllers that monitor cluster state and attempt to reconcile it with the desired state.

Example:

You request three replicas.

```yaml
spec:
  replicas: 3
```

If only two Pods are running, the relevant controller works to create another Pod.

### 9.5 Cloud Controller Manager

In cloud environments, the Cloud Controller Manager integrates Kubernetes with cloud-provider infrastructure.

It can manage cloud-specific resources such as:

- Load balancers.
- Node lifecycle information.
- Cloud networking integration.
- Cloud storage integration.

---

## 10. Worker Node Components

A Worker Node runs application workloads.

### 10.1 Kubelet

The Kubelet runs on each worker node.

It ensures that the containers described in assigned Pod specifications are running.

### 10.2 Container Runtime

The container runtime runs containers.

Examples include:

- containerd.
- CRI-O.

Docker Engine is not required as the Kubernetes container runtime. Kubernetes uses the Container Runtime Interface (CRI).

### 10.3 kube-proxy

`kube-proxy` implements Service networking rules on nodes in many Kubernetes installations.

Some networking implementations provide alternative service-proxy functionality.

### Architecture Summary

| Component | Responsibility |
|---|---|
| API Server | Kubernetes API entry point |
| etcd | Stores cluster state |
| Scheduler | Assigns Pods to nodes |
| Controller Manager | Reconciles desired and actual state |
| Cloud Controller Manager | Cloud integration |
| Kubelet | Manages Pods on a node |
| Container Runtime | Runs containers |
| kube-proxy | Implements Service networking in applicable setups |

---

# PART 6: PODS, REPLICASETS AND DEPLOYMENTS

## 11. What Is a Pod?

A Pod is the smallest deployable unit in Kubernetes.

A Pod can contain one or more containers.

```text
              Pod
               |
       -----------------
       |               |
       v               v
   Container A     Container B
```

Containers inside the same Pod:

- Share the Pod network namespace.
- Can communicate using `localhost`.
- Can share mounted volumes.

### Example Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx:1.27
      ports:
        - containerPort: 80
```

Deploy it:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods
```

Inspect:

```bash
kubectl describe pod nginx-pod
```

### Important

In production, you normally manage application Pods through higher-level controllers such as Deployments rather than creating individual Pods manually.

---

## 12. What Is a ReplicaSet?

A ReplicaSet ensures that a specified number of Pod replicas exist.

Example:

```yaml
spec:
  replicas: 3
```

This means Kubernetes should maintain three matching Pods.

```text
              ReplicaSet
                   |
          -------------------
          |        |        |
          v        v        v
        Pod 1    Pod 2    Pod 3
```

If one Pod fails, the ReplicaSet creates a replacement when necessary.

---

## 13. What Is a Deployment?

A Deployment manages application rollouts and ReplicaSets.

It is the usual Kubernetes workload resource for stateless applications.

### Architecture

```text
              Deployment
                   |
                   v
               ReplicaSet
                   |
          -------------------
          |        |        |
          v        v        v
        Pod 1    Pod 2    Pod 3
```

### Deployment YAML

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
        - name: node-api

          image: my-node-api:v1

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
kubectl get replicasets
kubectl get pods
```

### Update a Deployment

Suppose you have:

```text
my-node-api:v1
```

Now you want to deploy:

```text
my-node-api:v2
```

Update the image:

```bash
kubectl set image deployment/node-api \
  node-api=my-node-api:v2
```

Check rollout:

```bash
kubectl rollout status deployment/node-api
```

Rollback:

```bash
kubectl rollout undo deployment/node-api
```

Kubernetes Deployments manage ReplicaSets and controlled updates to stateless workloads. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)

### Deployment vs ReplicaSet vs Pod

| Resource | Responsibility |
|---|---|
| Pod | Runs one or more containers |
| ReplicaSet | Maintains the desired Pod count |
| Deployment | Manages ReplicaSets and application rollouts |

---

# PART 7: KUBERNETES SERVICES AND NETWORKING

## 14. What Is a Kubernetes Service?

Pods are replaceable.

A Pod may be deleted and recreated with a different IP address.

If another application depends on a specific Pod IP, communication becomes unreliable.

A Kubernetes Service provides a stable network endpoint for a group of Pods.

### Architecture

```text
                Order Service
                      |
                      v
               Product Service
                      |
                      v
                 Kubernetes
                   Service
                      |
          -----------------------
          |          |          |
          v          v          v
        Pod 1      Pod 2      Pod 3
```

The client communicates with the Service rather than depending on individual Pod IPs.

### Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  name: node-api-service

spec:
  selector:
    app: node-api

  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000

  type: ClusterIP
```

### Important Fields

| Field | Meaning |
|---|---|
| `port` | Port exposed by the Service |
| `targetPort` | Port on the target Pod |
| `selector` | Selects matching Pods |
| `type` | Determines Service exposure behavior |

### Service Types

#### 1. ClusterIP

Default Service type.

Exposes the Service inside the cluster.

```yaml
type: ClusterIP
```

Useful for internal communication.

#### 2. NodePort

Exposes the Service through a port on each node.

```yaml
type: NodePort
```

#### 3. LoadBalancer

Requests an external load balancer through a supported infrastructure integration.

```yaml
type: LoadBalancer
```

#### 4. ExternalName

Maps a Kubernetes Service name to an external DNS name.

```yaml
type: ExternalName
externalName: database.example.com
```

Official documentation: [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)

---

## 15. Kubernetes DNS and Service Discovery

Kubernetes provides DNS-based discovery for Services.

Example:

```text
http://product-service
```

A Service can also be addressed using its namespace-qualified DNS name:

```text
product-service.ecommerce.svc.cluster.local
```

This is useful when one service needs to communicate with another.

Example:

```typescript
const PRODUCT_SERVICE_URL =
  "http://product-service:3000";
```

The application does not need to know the changing IP addresses of individual Pods.

---

## 16. What Is Kubernetes Ingress?

Ingress is a Kubernetes API resource that defines HTTP and HTTPS routing from outside the cluster to Services.

Example:

```text
                 Internet
                     |
                     v
                Ingress
                     |
          ---------------------
          |                   |
          v                   v
     Product Service     Order Service
          |                   |
          v                   v
        Pods                Pods
```

### Example Routing

```text
example.com/products
       |
       v
Product Service

example.com/orders
       |
       v
Order Service
```

### Example Ingress YAML

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
          - path: /products
            pathType: Prefix

            backend:
              service:
                name: product-service
                port:
                  number: 80

          - path: /orders
            pathType: Prefix

            backend:
              service:
                name: order-service
                port:
                  number: 80
```

**Important:** Creating an Ingress resource alone does not make it work. You need an Ingress Controller that implements it.

### Ingress vs Service

| Service | Ingress |
|---|---|
| Provides network access to Pods | Defines external HTTP/HTTPS routing |
| Supports multiple Service types | Routes HTTP/HTTPS traffic |
| Can expose internal applications | Commonly exposes web applications |
| Works at the Service networking layer | Operates as an HTTP routing entry point |

### Important Modern Development

The Kubernetes Ingress API is stable but frozen. Kubernetes documentation recommends considering the Gateway API for newer and more expressive traffic-routing requirements. [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/index.html?utm_source=chatgpt.com)

---

## 17. What Is Kubernetes Gateway API?

Gateway API is a collection of Kubernetes APIs designed for advanced traffic management.

It provides a more expressive model than traditional Ingress.

Important resources include:

- GatewayClass.
- Gateway.
- HTTPRoute.
- GRPCRoute.
- TCPRoute.

### Conceptual Architecture

```text
               Internet
                   |
                   v
                Gateway
                   |
           ------------------
           |                |
           v                v
       HTTPRoute         HTTPRoute
           |                |
           v                v
     Product Service   Order Service
```

### Gateway API vs Ingress

| Feature | Ingress | Gateway API |
|---|---|---|
| Routing | Primarily HTTP/HTTPS | Multiple route types |
| Role separation | More limited | Stronger role-oriented model |
| Extensibility | More limited | More expressive |
| Advanced traffic policies | Controller-specific extensions | Richer API model |
| Status | Stable, frozen API | Evolving API ecosystem |

The actual capabilities depend on the Gateway API implementation installed in your cluster.

---

# PART 8: CONFIGMAPS AND SECRETS

## 18. What Is a ConfigMap?

A ConfigMap stores non-sensitive configuration separately from application images.

Examples:

- Application mode.
- Service URLs.
- Feature flags.
- Log levels.

### ConfigMap YAML

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: app-config

data:
  NODE_ENV: production
  PORT: "3000"
  LOG_LEVEL: info
```

### Using ConfigMap in a Deployment

```yaml
spec:
  containers:
    - name: node-api
      image: my-node-api:v1

      envFrom:
        - configMapRef:
            name: app-config
```

Now the application can access:

```typescript
process.env.NODE_ENV;
process.env.PORT;
```

---

## 19. What Is a Kubernetes Secret?

A Secret stores sensitive configuration such as:

- Database credentials.
- API keys.
- Authentication tokens.
- Certificates.

### Create a Secret

```bash
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=strong-password
```

### Use Secret in a Deployment

```yaml
spec:
  containers:
    - name: node-api
      image: my-node-api:v1

      env:
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: username

        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
```

### ConfigMap vs Secret

| ConfigMap | Secret |
|---|---|
| Non-sensitive configuration | Sensitive configuration |
| Stores regular configuration | Stores credentials and sensitive values |
| Not intended for confidential data | Intended for sensitive data |
| Base64 is not required | Values are commonly represented using Base64 in YAML |

**Critical security point:** Kubernetes Secrets are not automatically encrypted merely because their values are Base64 encoded.

Use encryption at rest, appropriate RBAC, restricted access and preferably an external secret-management solution for production.

Kubernetes security documentation discusses Secret protection, encryption at rest, workload isolation and other cluster security controls. [Kubernetes](https://kubernetes.io/docs/concepts/security/?utm_source=chatgpt.com)

---

# PART 9: KUBERNETES STORAGE

## 20. Why Does Kubernetes Need Persistent Storage?

Pods are ephemeral.

A Pod can be deleted and recreated.

If important application data exists only in the container filesystem, it may be lost when the Pod is replaced.

Databases and stateful applications require persistent storage.

### Kubernetes Storage Architecture

```text
              Application Pod
                     |
                     v
              PersistentVolumeClaim
                     |
                     v
               PersistentVolume
                     |
                     v
              Physical / Cloud Storage
```

---

## 21. What Is a PersistentVolume?

A PersistentVolume (PV) represents storage available to a Kubernetes cluster.

The storage can be backed by:

- Cloud block storage.
- Network file systems.
- Local disks.
- Storage systems provided by infrastructure vendors.

## 22. What Is a PersistentVolumeClaim?

A PersistentVolumeClaim (PVC) is a request for storage.

A Pod can mount a PVC without needing to know the exact storage implementation.

### PVC Example

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: mysql-pvc

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 10Gi
```

### Use PVC in a Pod

```yaml
spec:
  containers:
    - name: mysql
      image: mysql:8

      volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql

  volumes:
    - name: mysql-storage

      persistentVolumeClaim:
        claimName: mysql-pvc
```

---

## 23. What Is a StorageClass?

A StorageClass defines how storage can be dynamically provisioned.

For example, a cloud provider may provide different storage classes:

```text
standard
premium
fast-ssd
```

Example:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass

metadata:
  name: fast-storage

provisioner: example.com/storage

parameters:
  type: fast-ssd
```

The actual provisioner and parameters depend on the storage provider.

### PV vs PVC vs StorageClass

| Resource | Purpose |
|---|---|
| PersistentVolume | Represents available storage |
| PersistentVolumeClaim | Requests storage |
| StorageClass | Defines storage provisioning behavior |

### StatefulSet

A StatefulSet is designed for workloads that need stable identities or persistent storage relationships.

Examples:

- Databases.
- Distributed databases.
- Stateful message brokers.

### Deployment vs StatefulSet

| Deployment | StatefulSet |
|---|---|
| Commonly used for stateless workloads | Used for stateful workloads |
| Pods are interchangeable | Pods have stable identities |
| Replica ordering is not guaranteed | Supports ordered identity and management |
| Storage is not inherently tied to each replica | Supports per-Pod persistent volume claims |

**Important:** StatefulSet does not automatically make a database highly available. Database replication, backups, failover and recovery must still be designed.

---

# PART 10: KUBERNETES RESOURCE MANAGEMENT

## 24. What Are Resource Requests and Limits?

Kubernetes allows you to define CPU and memory requirements for containers.

Two important settings are:

- Requests.
- Limits.

### Resource Requests

A request specifies the amount of a resource Kubernetes uses when scheduling a Pod.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
```

This requests:

- 250 millicores of CPU.
- 256 MiB of memory.

### Resource Limits

A limit defines the maximum resource amount a container can consume for supported resource types.

```yaml
resources:
  limits:
    cpu: "1"
    memory: "512Mi"
```

This specifies:

- Maximum CPU quota equivalent to one CPU core.
- Maximum memory of 512 MiB.

### Complete Example

```yaml
containers:
  - name: node-api
    image: my-node-api:v1

    resources:
      requests:
        cpu: "250m"
        memory: "256Mi"

      limits:
        cpu: "1"
        memory: "512Mi"
```

### What Happens When Limits Are Exceeded?

**CPU limit:**

The container can be throttled.

**Memory limit:**

The container may be terminated due to an out-of-memory condition.

### Requests vs Limits

| Requests | Limits |
|---|---|
| Used for scheduling decisions | Restricts resource consumption |
| Represents resource requirement | Represents maximum allowed usage |
| Helps Kubernetes place Pods | Helps control resource usage |

---

# PART 11: HORIZONTAL POD AUTOSCALING

## 25. What Is Horizontal Pod Autoscaling?

Horizontal Pod Autoscaler (HPA) automatically adjusts the number of Pod replicas based on observed metrics.

Example:

```text
Low Traffic:
2 Pods

Medium Traffic:
5 Pods

High Traffic:
10 Pods
```

### Architecture

```text
             Incoming Traffic
                    |
                    v
                Kubernetes
                    |
                    v
                  HPA
                    |
          --------------------
          |        |         |
          v        v         v
        Pod 1    Pod 2    Pod 3
```

HPA commonly uses CPU and memory metrics, and can also use custom or external metrics when the necessary metrics infrastructure is configured.

### HPA YAML Example

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: node-api-hpa

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

This configuration targets average CPU utilization of 70% of the relevant CPU requests.

### Important Requirements

For basic CPU and memory utilization scaling, you generally need Metrics Server installed and working.

### Useful Commands

```bash
kubectl get hpa
```

```bash
kubectl describe hpa node-api-hpa
```

```bash
kubectl autoscale deployment node-api \
  --cpu-percent=70 \
  --min=2 \
  --max=10
```

The last command is a convenient imperative way to create an HPA.

### Important

If an HPA manages the Deployment's replica count, avoid manually setting a conflicting replica count in your deployment workflow.

Kubernetes documents manual and automatic scaling, including the interaction between HPA and Deployment replica counts. [Kubernetes](https://kubernetes.io/docs/concepts/workloads/management/?utm_source=chatgpt.com)

---

# PART 12: KUBERNETES HEALTH CHECKS

## 26. What Are Kubernetes Probes?

Kubernetes uses probes to determine the health and availability of containers.

There are three main probes:

1. Liveness Probe.
2. Readiness Probe.
3. Startup Probe.

### 26.1 Liveness Probe

Determines whether the application is still functioning.

If a liveness probe repeatedly fails, Kubernetes can restart the container.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health/live
    port: 3000

  initialDelaySeconds: 10
  periodSeconds: 10
```

### 26.2 Readiness Probe

Determines whether the application is ready to receive traffic.

If readiness fails, the Pod is removed from the ready endpoints of its Service.

The container does not necessarily restart.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /health/ready
    port: 3000

  periodSeconds: 5
```

### 26.3 Startup Probe

Determines whether the application has completed its startup process.

It is particularly useful for applications that take a long time to initialize.

Example:

```yaml
startupProbe:
  httpGet:
    path: /health/startup
    port: 3000

  failureThreshold: 30
  periodSeconds: 10
```

While the startup probe has not succeeded, Kubernetes does not run the liveness or readiness probes for that container.

### Comparison

| Probe | Purpose | Failure Behavior |
|---|---|---|
| Liveness | Is the application alive? | Can restart container |
| Readiness | Can the application receive traffic? | Removes Pod from ready endpoints |
| Startup | Has startup completed? | Delays other probes; repeated failure can restart container |

### Real-World Example

Suppose your Node.js API starts but needs 30 seconds to connect to its database and load essential configuration.

Without a proper startup probe, Kubernetes may incorrectly restart it during initialization.

A startup probe provides time for initialization.

### Important Design Advice

Do not make liveness checks depend on every external dependency.

For example, if your database becomes temporarily unavailable, a liveness probe that fails because of that database could restart every application Pod unnecessarily.

Keep health endpoints aligned with the purpose of each probe.

---

# PART 13: KUBERNETES NETWORKING AND SECURITY

## 27. Kubernetes Network Model

Kubernetes networking provides communication between:

- Containers in the same Pod.
- Pods on different nodes.
- Services.
- External clients.

Each Pod receives a cluster IP address.

A Service provides a stable endpoint for accessing matching Pods.

```text
                  Cluster Network
                        |
           ---------------------------
           |                         |
           v                         v
        Node 1                    Node 2
           |                         |
       ---------                 ---------
       |       |                 |       |
       v       v                 v       v
     Pod A   Pod B              Pod C   Pod D
```

A Container Network Interface (CNI) plugin provides the cluster's Pod networking implementation.

Examples include:

- Calico.
- Cilium.
- Flannel.

---

## 28. What Is a Kubernetes NetworkPolicy?

NetworkPolicy controls permitted network traffic to and from selected Pods.

For example:

- Allow Order Service to communicate with Payment Service.
- Prevent unrelated Pods from accessing a database.
- Restrict outbound communication.

### Example

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: allow-order-service

spec:
  podSelector:
    matchLabels:
      app: payment-service

  policyTypes:
    - Ingress

  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: order-service

      ports:
        - protocol: TCP
          port: 3000
```

This policy allows matching Order Service Pods to access the selected Payment Service Pods on TCP port 3000.

**Important:** NetworkPolicy enforcement requires a networking implementation that supports it. Creating the YAML object alone does not guarantee enforcement. [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/?utm_source=chatgpt.com)

---

# PART 14: HELM CHARTS

## 29. What Is Helm?

Helm is a package manager for Kubernetes.

Kubernetes applications often require multiple YAML files.

For example:

```text
Deployment
Service
ConfigMap
Secret
Ingress
HPA
```

Maintaining these resources separately for multiple environments can become difficult.

Helm solves this by packaging Kubernetes resources into reusable Charts.

### Without Helm

```text
kubernetes/
    deployment.yaml
    service.yaml
    configmap.yaml
    ingress.yaml
```

You may need separate configurations for:

- Development.
- Testing.
- Staging.
- Production.

### With Helm

```text
node-api/
    Chart.yaml
    values.yaml
    templates/
        deployment.yaml
        service.yaml
        configmap.yaml
        ingress.yaml
```

---

## 30. Helm Chart Structure

### Chart.yaml

Contains chart metadata.

```yaml
apiVersion: v2

name: node-api

description: Node.js API Helm Chart

type: application

version: 1.0.0

appVersion: "1.0.0"
```

### values.yaml

Contains configurable values.

```yaml
replicaCount: 3

image:
  repository: my-node-api
  tag: "v1"

service:
  type: ClusterIP
  port: 80

containerPort: 3000
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
            - containerPort: {{ .Values.containerPort }}
```

### Install Chart

```bash
helm install node-api ./node-api
```

### Upgrade Chart

```bash
helm upgrade node-api ./node-api
```

### List Releases

```bash
helm list
```

### Uninstall

```bash
helm uninstall node-api
```

### Why Use Helm?

1. Reusable Kubernetes configurations.
2. Environment-specific values.
3. Easier installation and upgrades.
4. Versioned application packages.
5. Easier distribution of Kubernetes applications.

Official documentation: [Helm Documentation](https://helm.sh/docs/?utm_source=chatgpt.com)

---

# PART 15: KUBERNETES OPERATORS

## 31. What Is a Kubernetes Operator?

A Kubernetes Operator is a software extension that uses Kubernetes APIs and controllers to automate the management of a specific application or system.

Operators are especially useful for complex stateful applications.

Examples:

- Database operators.
- Kafka operators.
- Monitoring operators.
- Storage operators.

### Why Do We Need Operators?

Suppose you deploy a database.

Running a database container is only the beginning.

You may also need:

- Backups.
- Replication.
- Failover.
- Recovery.
- Version upgrades.
- Configuration changes.
- Storage management.

An Operator can automate these operational tasks.

### Operator Architecture

```text
              Kubernetes API
                    |
                    v
                Custom Resource
                    |
                    v
             Operator Controller
                    |
          ------------------------
          |          |           |
          v          v           v
       Database   Backup      Replication
       Management Management  Management
```

---

## 32. What Is a Custom Resource Definition?

A Custom Resource Definition (CRD) extends the Kubernetes API with a new resource type.

For example, a database operator might introduce a custom resource named:

```text
DatabaseCluster
```

Then you can define:

```yaml
apiVersion: example.com/v1
kind: DatabaseCluster

metadata:
  name: production-db

spec:
  replicas: 3
  version: "8"
```

The Operator watches this resource and attempts to create or maintain the requested database cluster.

### CRD vs Operator

| CRD | Operator |
|---|---|
| Defines a custom Kubernetes API resource | Implements operational behavior |
| Extends the Kubernetes API | Watches resources and reconciles state |
| Describes configuration | Automates application lifecycle |

**Important:** A CRD alone does not implement the business logic. A controller or Operator must process it.

---

# PART 16: SERVICE MESH ARCHITECTURE

## 33. What Is a Service Mesh?

A Service Mesh is an infrastructure layer that manages service-to-service communication.

In a microservices architecture, services communicate frequently.

You may need:

- Mutual TLS.
- Traffic routing.
- Retries.
- Timeouts.
- Circuit breaking.
- Metrics.
- Distributed tracing.
- Access policies.

Implementing all these capabilities separately in every service creates complexity.

A Service Mesh moves many communication responsibilities into dedicated infrastructure components.

### Without Service Mesh

```text
Order Service
    |
    | Application Code Handles:
    |-- Retries
    |-- Timeouts
    |-- TLS
    |-- Metrics
    |-- Traffic Policies
    |
    v
Payment Service
```

### With Service Mesh

```text
        Order Service
              |
              v
         Proxy A
              |
              | Managed Communication
              |
         Proxy B
              |
              v
        Payment Service
```

The application can focus more on business logic while the mesh manages supported networking concerns.

---

## 34. Service Mesh Architecture

A service mesh commonly has two major architectural layers.

1. Data Plane.
2. Control Plane.

### Data Plane

Handles actual service traffic.

It may consist of proxies deployed alongside workloads or as node-level infrastructure, depending on the mesh architecture.

### Control Plane

Manages configuration and policy distribution for the data plane.

### Architecture

```text
                Control Plane
                      |
          -------------------------
          |                       |
          v                       v
       Proxy A                 Proxy B
          |                       |
          v                       v
     Order Service          Payment Service
```

### Responsibilities

| Data Plane | Control Plane |
|---|---|
| Handles traffic | Configures traffic behavior |
| Applies network policies | Distributes configuration |
| Collects telemetry | Manages mesh configuration |
| Performs supported traffic operations | Coordinates proxies |

---

# PART 17: ISTIO FUNDAMENTALS

## 35. What Is Istio?

Istio is a service mesh platform that provides traffic management, security and observability for service-to-service communication.

Istio commonly uses Envoy proxies for data-plane traffic handling.

### Istio Architecture

```text
                  Istio Control Plane
                         |
                         v
                    istiod
                         |
              ----------------------
              |                    |
              v                    v
           Envoy A              Envoy B
              |                    |
              v                    v
        Order Service         Payment Service
```

### Important Components

| Component | Responsibility |
|---|---|
| istiod | Mesh configuration, identity and control-plane functions |
| Envoy | Data-plane proxy |
| Gateway | Manages ingress or egress traffic |
| VirtualService | Defines traffic routing rules |
| DestinationRule | Defines policies for destinations |
| PeerAuthentication | Configures inbound mTLS requirements |
| AuthorizationPolicy | Defines access-control policies |

### What Does Istio Provide?

1. Service-to-service encryption.
2. Traffic routing.
3. Load balancing policies.
4. Retries and timeouts.
5. Circuit-breaking capabilities.
6. Access control.
7. Metrics.
8. Distributed tracing integration.

Official documentation: [Istio Documentation](https://istio.io/latest/docs/?utm_source=chatgpt.com)

---

## 36. Sidecar Architecture

In the traditional sidecar model, a proxy runs alongside the application container in the same Pod.

```text
                  Kubernetes Pod
                        |
             ----------------------
             |                    |
             v                    v
       Application             Envoy
        Container              Proxy
             |                    |
             ----------------------
                        |
                        v
                Network Traffic
```

Application traffic is intercepted and handled through the proxy.

### Benefits

- Application code does not need to implement every mesh feature.
- Traffic policies can be applied centrally.
- Communication can be encrypted.
- Observability can be standardized.

### Limitations

- Additional resource consumption.
- More moving parts.
- Proxy configuration and upgrades require management.

---

## 37. Istio Ambient Mode

Istio also provides an ambient data-plane architecture.

Instead of requiring a sidecar in every application Pod, ambient mode uses node-level `ztunnel` proxies for Layer 4 security and connectivity.

Optional waypoint proxies provide additional Layer 7 capabilities.

```text
                 Kubernetes Node
                       |
             ---------------------
             |                   |
             v                   v
           ztunnel             ztunnel
             |                   |
             v                   v
        Application A       Application B
```

For advanced Layer 7 routing and authorization, traffic can also pass through waypoint proxies.

### Sidecar vs Ambient

| Feature | Sidecar Mode | Ambient Mode |
|---|---|---|
| Proxy placement | Sidecar in application Pod | Node-level ztunnel and optional waypoints |
| Application Pod changes | Sidecar injection | No per-Pod sidecar required |
| Layer 4 security | Supported | Supported through ztunnel |
| Layer 7 capabilities | Sidecar proxy | Waypoint proxy where required |
| Resource model | Proxy resources per Pod | Shared node-level infrastructure plus optional waypoints |

Istio documents both modes and explains when ambient mode requires waypoint proxies for advanced Layer 7 features. [Istio](https://istio.io/latest/docs/ambient/overview/?utm_source=chatgpt.com)

---

# PART 18: mTLS WITH ISTIO

## 38. What Is mTLS?

Mutual TLS means both sides authenticate each other using certificates.

In a microservices architecture, it helps establish trusted service identities.

### Normal TLS

```text
Client
   |
   | Verifies Server Certificate
   v
Server
```

### Mutual TLS

```text
Client
   |
   | Client Certificate
   |
   v
Server
   |
   | Server Certificate
   |
   v
Mutually Authenticated Connection
```

### Why Use mTLS?

- Encrypt service-to-service traffic.
- Authenticate workloads.
- Reduce the risk of impersonation.
- Support zero-trust security policies.

### Istio PeerAuthentication

Example:

```yaml
apiVersion: security.istio.io/v1
kind: PeerAuthentication

metadata:
  name: default

  namespace: ecommerce

spec:
  mtls:
    mode: STRICT
```

This requires incoming mesh traffic to use mTLS for workloads in the applicable namespace.

### Istio mTLS Modes

| Mode | Meaning |
|---|---|
| STRICT | Requires mTLS |
| PERMISSIVE | Accepts mTLS and plaintext |
| DISABLE | Disables mTLS enforcement |

The exact behavior and supported modes depend on the mesh data-plane configuration.

Istio documents `PeerAuthentication` as the mechanism for defining inbound mTLS requirements. [Istio](https://istio.io/latest/docs/reference/config/security/peer_authentication/?utm_source=chatgpt.com)

---

# PART 19: ISTIO TRAFFIC MANAGEMENT

## 39. What Is Istio Traffic Management?

Istio allows you to control how traffic moves between services.

Examples:

- Send traffic to a specific service version.
- Route a percentage of traffic to a new version.
- Configure timeouts.
- Configure retries.
- Perform canary releases.
- Control external traffic.

### VirtualService

A VirtualService defines routing rules.

Example:

```yaml
apiVersion: networking.istio.io/v1
kind: VirtualService

metadata:
  name: product-routing

spec:
  hosts:
    - product-service

  http:
    - route:
        - destination:
            host: product-service
            subset: v1
          weight: 90

        - destination:
            host: product-service
            subset: v2
          weight: 10
```

This routes approximately:

- 90% of matching traffic to version 1.
- 10% of matching traffic to version 2.

### DestinationRule

A DestinationRule defines policies for traffic to a destination.

It can define:

- Subsets.
- Load balancing.
- Connection pools.
- Outlier detection.
- TLS settings.

Example:

```yaml
apiVersion: networking.istio.io/v1
kind: DestinationRule

metadata:
  name: product-destination

spec:
  host: product-service

  subsets:
    - name: v1

      labels:
        version: v1

    - name: v2

      labels:
        version: v2
```

The VirtualService references these subsets.

### Important

VirtualService and DestinationRule are not interchangeable.

- VirtualService decides where traffic goes.
- DestinationRule defines policies for selected destinations and subsets.

Istio supports traffic routing, timeouts, retries and staged rollouts through its traffic-management model. [Istio](https://istio.io/latest/docs/concepts/traffic-management/?utm_source=chatgpt.com)

---

# PART 20: CANARY DEPLOYMENT

## 40. What Is Canary Deployment?

Canary deployment means releasing a new application version to a small percentage of traffic before expanding the rollout.

Example:

```text
Version 1 = 90%
Version 2 = 10%
```

If the new version works correctly, increase its traffic.

```text
Stage 1: v1 = 90%, v2 = 10%

Stage 2: v1 = 70%, v2 = 30%

Stage 3: v1 = 50%, v2 = 50%

Stage 4: v1 = 0%,  v2 = 100%
```

### Architecture

```text
                 Incoming Traffic
                         |
                         v
                    Traffic Router
                         |
               ---------------------
               |                   |
               v                   v
           Version 1            Version 2
              90%                  10%
```

### Why Use Canary Deployment?

- Detect production problems early.
- Reduce deployment risk.
- Monitor new-version performance.
- Roll back without replacing the entire environment.

### Important Metrics to Monitor

- Error rate.
- Request latency.
- CPU usage.
- Memory consumption.
- Business transaction success.
- Crash frequency.

Canary deployments should use measurable promotion and rollback criteria rather than arbitrary waiting periods.

---

# PART 21: BLUE-GREEN DEPLOYMENT

## 41. What Is Blue-Green Deployment?

Blue-Green deployment maintains two application environments:

- Blue: Current production version.
- Green: New version.

Only one environment receives normal production traffic at a time.

### Architecture

```text
                 Load Balancer
                       |
                       v
                Active Environment
                       |
              ---------------------
              |                   |
              v                   v
           BLUE                  GREEN
        Version 1             Version 2
```

Initially:

```text
Traffic --> BLUE
```

After testing Green:

```text
Traffic --> GREEN
```

### Benefits

1. Fast traffic switching.
2. Easier rollback.
3. Separate validation environment.
4. Reduced deployment downtime.

### Canary vs Blue-Green

| Canary | Blue-Green |
|---|---|
| Gradually shifts traffic | Switches traffic between environments |
| Releases to a small percentage first | New environment can be tested before switching |
| Useful for gradual risk reduction | Useful for controlled environment replacement |
| Requires traffic percentage management | Requires environment and routing management |

---

# PART 22: GITOPS

## 42. What Is GitOps?

GitOps is an operational approach in which Git repositories contain the desired state of infrastructure and application deployments.

The Git repository becomes the source of truth for deployment configuration.

### Traditional Deployment

```text
Developer
    |
    v
CI/CD Pipeline
    |
    v
kubectl apply
    |
    v
Kubernetes Cluster
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

The GitOps controller continuously compares the actual cluster state with the desired state stored in Git.

If they differ, the controller can reconcile the cluster.

### GitOps Benefits

- Version-controlled infrastructure.
- Auditable deployment changes.
- Easier rollback.
- Declarative configuration.
- Reduced manual cluster changes.
- Improved deployment consistency.

### Important Principles

1. Declarative configuration.
2. Version-controlled desired state.
3. Automated reconciliation.
4. Observable deployment state.
5. Controlled access to production changes.

---

# PART 23: ARGO CD

## 43. What Is Argo CD?

Argo CD is a declarative GitOps continuous-delivery tool for Kubernetes.

It monitors Git repositories and synchronizes Kubernetes clusters with the desired configuration.

### Argo CD Architecture

```text
                Git Repository
                      |
                      v
                  Argo CD
                      |
             -------------------
             |                 |
             v                 v
       Desired State       Live State
             |                 |
             -------------------
                      |
                      v
              Compare Differences
                      |
                      v
                Synchronization
                      |
                      v
              Kubernetes Cluster
```

### Important Argo CD Concepts

| Concept | Meaning |
|---|---|
| Application | Defines what should be deployed and where |
| Repository | Stores deployment configuration |
| Sync | Applies desired state to the cluster |
| OutOfSync | Live state differs from desired state |
| Synced | Live state matches desired state |
| Health | Indicates the operational health of resources |
| Auto-Sync | Automatically applies Git changes |

---

## 44. Example Argo CD Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application

metadata:
  name: node-api

  namespace: argocd

spec:
  project: default

  source:
    repoURL: https://github.com/example/k8s-config.git

    targetRevision: main

    path: apps/node-api

  destination:
    server: https://kubernetes.default.svc

    namespace: production

  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Explanation

`repoURL` identifies the Git repository.

`targetRevision` identifies the branch, tag or commit reference.

`path` identifies the directory containing Kubernetes manifests.

`destination` identifies the Kubernetes cluster and namespace.

`automated` enables automatic synchronization.

`prune` allows Argo CD to delete managed resources that have been removed from the desired configuration.

`selfHeal` allows Argo CD to correct certain live-state differences.

### Important Security Consideration

Automatic pruning can delete resources.

Use it carefully, especially in production.

Also, never commit unencrypted production credentials to Git.

Argo CD supports deploying Helm charts through its declarative application model. [argo-cd.readthedocs.io](https://argo-cd.readthedocs.io/en/stable/user-guide/helm/?utm_source=chatgpt.com)

---

# PART 24: CI/CD WITH GITHUB ACTIONS AND ARGO CD

## 45. How Do CI and GitOps Work Together?

A modern deployment pipeline can use:

- GitHub Actions for Continuous Integration.
- Container Registry for image storage.
- Argo CD for Continuous Delivery.

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
          -----------------------
          |          |          |
          v          v          v
        Build      Test       Scan
          |
          v
       Docker Image
          |
          v
     Container Registry
          |
          v
   Update Deployment Repository
          |
          v
        Argo CD
          |
          v
   Kubernetes Cluster
```

### CI Responsibilities

1. Install dependencies.
2. Run linting.
3. Run unit tests.
4. Run integration tests.
5. Build application.
6. Build Docker image.
7. Scan image.
8. Push image to registry.

### CD Responsibilities

1. Detect deployment configuration changes.
2. Compare desired and live state.
3. Synchronize Kubernetes resources.
4. Report deployment health.

### Example GitHub Actions Workflow

```yaml
name: Node API CI

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Install Dependencies
        run: npm ci

      - name: Run Tests
        run: npm test

      - name: Build Application
        run: npm run build

      - name: Build Docker Image
        run: docker build -t node-api:${{ github.sha }} .
```

This is a simplified CI workflow. A production pipeline must also configure registry authentication, vulnerability scanning, image publishing and deployment configuration updates.

### Why Separate CI and CD?

CI focuses on validating and packaging application code.

GitOps CD focuses on reconciling the desired deployment configuration with the Kubernetes cluster.

This separation provides a clear deployment audit trail.

---

# PART 25: KUBERNETES SECURITY

## 46. Kubernetes Security Fundamentals

Production Kubernetes environments require multiple layers of security.

Important areas include:

1. Authentication.
2. Authorization.
3. Network security.
4. Pod security.
5. Secret management.
6. Image security.
7. Supply-chain security.
8. Audit logging.

---

## 47. Role-Based Access Control (RBAC)

RBAC controls what users and service accounts can do in a Kubernetes cluster.

Important resources:

- Role.
- ClusterRole.
- RoleBinding.
- ClusterRoleBinding.

### Role Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role

metadata:
  name: pod-reader

  namespace: ecommerce

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

This Role grants read access to Pods in the specified namespace.

### RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding

metadata:
  name: pod-reader-binding

  namespace: ecommerce

subjects:
  - kind: ServiceAccount
    name: developer
    namespace: ecommerce

roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Role vs ClusterRole

| Role | ClusterRole |
|---|---|
| Namespaced resource | Cluster-wide resource definition |
| Permissions scoped to a namespace | Can define permissions for cluster-scoped or namespaced resources |
| Bound using RoleBinding | Can be bound using ClusterRoleBinding or RoleBinding |

### Principle of Least Privilege

Give each user and service account only the permissions it requires.

Avoid giving every application administrator-level cluster access.

---

## 48. Pod Security

Production Pods should follow security best practices.

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: secure-node-api

spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000

  containers:
    - name: node-api
      image: my-node-api:v1

      securityContext:
        allowPrivilegeEscalation: false

        capabilities:
          drop:
            - ALL

        readOnlyRootFilesystem: true
```

### Important Settings

| Setting | Purpose |
|---|---|
| `runAsNonRoot` | Prevents running as root |
| `runAsUser` | Sets the user ID |
| `allowPrivilegeEscalation` | Controls privilege escalation |
| `capabilities.drop` | Removes Linux capabilities |
| `readOnlyRootFilesystem` | Makes the container root filesystem read-only |

Some applications require writable temporary directories or specific capabilities. Test the security configuration before enforcing it.

### Pod Security Standards

Kubernetes provides Pod Security Standards with three policy levels:

- Privileged.
- Baseline.
- Restricted.

The Restricted profile applies stronger security controls.

---

## 49. Kubernetes Secrets Security

Avoid storing plaintext secrets in Git repositories.

Use:

- External Secrets Operator.
- HashiCorp Vault.
- AWS Secrets Manager.
- Azure Key Vault.
- Google Secret Manager.

### Recommended Flow

```text
             External Secret Manager
                       |
                       v
              Kubernetes Integration
                       |
                       v
              Kubernetes Secret
                       |
                       v
                 Application Pod
```

Also consider:

- Encryption at rest.
- RBAC restrictions.
- Secret rotation.
- Audit logging.
- Avoiding secret exposure through logs.

---

## 50. Kubernetes Security Best Practices

| Area | Recommendation |
|---|---|
| Authentication | Use trusted identity providers |
| Authorization | Configure RBAC |
| Containers | Run as non-root |
| Network | Apply NetworkPolicies |
| Images | Scan and verify images |
| Secrets | Use secure secret-management systems |
| Storage | Restrict storage access |
| API Server | Restrict access |
| Audit | Enable audit logging |
| Runtime | Monitor suspicious behavior |
| Supply Chain | Verify build and image provenance |

---

# PART 26: PRODUCTION LOGGING AND MONITORING

## 51. Why Is Monitoring Important?

A production application must be monitored continuously.

You need to know:

- Is the application running?
- Is the application responding?
- Is the application slow?
- Are requests failing?
- Are Pods restarting?
- Is CPU usage high?
- Is memory usage increasing?
- Are queues becoming overloaded?

### Common Monitoring Tools

| Tool | Purpose |
|---|---|
| Prometheus | Metrics collection |
| Grafana | Dashboards and visualization |
| Loki | Log aggregation |
| Elasticsearch | Search and log analytics |
| Jaeger | Distributed tracing |
| OpenTelemetry | Telemetry instrumentation |
| Alertmanager | Alert routing and management |

---

## 52. Prometheus

Prometheus is a monitoring and alerting toolkit.

It collects metrics from configured targets, commonly by scraping HTTP metrics endpoints.

Example application metrics:

```text
http_requests_total
http_request_duration_seconds
process_cpu_seconds_total
process_resident_memory_bytes
```

### Prometheus Architecture

```text
              Application
                   |
                   v
              /metrics
                   |
                   v
               Prometheus
                   |
          -------------------
          |                 |
          v                 v
      Alert Rules        Metrics DB
                            |
                            v
                          Grafana
```

### Example Metrics Endpoint

A Node.js application can expose a `/metrics` endpoint using a Prometheus client library.

Conceptual output:

```text
http_requests_total{method="GET",status="200"} 150
```

---

## 53. Grafana

Grafana is used to visualize metrics and create dashboards.

Example dashboard panels:

- CPU usage.
- Memory usage.
- Request rate.
- Error rate.
- Response latency.
- Pod restart count.
- HPA replica count.

### Example Dashboard

```text
----------------------------------------
           Kubernetes Dashboard
----------------------------------------

CPU Usage:             65%

Memory Usage:          72%

Running Pods:          24

Failed Pods:            1

Request Rate:        1200/min

Error Rate:            0.8%

----------------------------------------
```

---

## 54. Centralized Logging

Applications running across multiple Pods produce logs independently.

A centralized logging system collects them.

### Architecture

```text
      Application Pods
             |
      -----------------
      |       |       |
      v       v       v
    Logs    Logs    Logs
      |       |       |
      -----------------
             |
             v
        Log Collector
             |
             v
       Centralized Logs
             |
             v
          Dashboard
```

Common solutions:

- Fluent Bit.
- Grafana Loki.
- Elasticsearch.
- OpenSearch.

### Structured Logging Example

```json
{
  "level": "error",
  "service": "order-service",
  "traceId": "abc123",
  "requestId": "req-456",
  "message": "Payment service unavailable"
}
```

Structured logs make troubleshooting easier.

---

# PART 27: PRODUCTION DEPLOYMENT ARCHITECTURE

## 55. Complete Production Kubernetes Architecture

Let's combine everything.

Suppose you have an e-commerce microservices application.

### Architecture

```text
                         INTERNET
                             |
                             v
                       LOAD BALANCER
                             |
                             v
                      GATEWAY / INGRESS
                             |
                             v
                    KUBERNETES CLUSTER
                             |
          ---------------------------------------
          |                 |                   |
          v                 v                   v
       AUTH NS           ORDER NS            PRODUCT NS
          |                 |                   |
          v                 v                   v
      Auth Pods          Order Pods          Product Pods
          |                 |                   |
          v                 v                   v
       Auth DB            Order DB           Product DB


                     MESSAGE BROKER
                           |
           ---------------------------------
           |               |               |
           v               v               v
      INVENTORY       NOTIFICATION       PAYMENT
       SERVICE          SERVICE          SERVICE


                 OBSERVABILITY PLATFORM
                           |
           ---------------------------------
           |               |               |
           v               v               v
       Prometheus        Grafana       Central Logs
                           |
                           v
                    Distributed Tracing
```

### Infrastructure Components

| Component | Technology |
|---|---|
| Containerization | Docker |
| Orchestration | Kubernetes |
| Packaging | Helm |
| Service Mesh | Istio |
| GitOps | Argo CD |
| CI | GitHub Actions |
| Metrics | Prometheus |
| Dashboards | Grafana |
| Logging | Loki / Elasticsearch |
| Tracing | OpenTelemetry |
| Database | MySQL / PostgreSQL |
| Cache | Redis |
| Messaging | RabbitMQ / Kafka |

### Production Deployment Flow

```text
Developer
   |
   v
GitHub Push
   |
   v
GitHub Actions
   |
   |-- Install Dependencies
   |-- Run Tests
   |-- Build Application
   |-- Scan Image
   |-- Build Docker Image
   |
   v
Container Registry
   |
   v
Deployment Configuration Updated in Git
   |
   v
Argo CD
   |
   v
Kubernetes Cluster
   |
   |-- Deployments
   |-- Services
   |-- ConfigMaps
   |-- Secrets
   |-- HPA
   |-- Persistent Volumes
   |
   v
Istio Service Mesh
   |
   v
Production Application
   |
   v
Prometheus + Grafana + Logs + Traces
```

---

# PART 28: COMMON DEVOPS AND KUBERNETES MISTAKES

## 56. Mistakes to Avoid

| Mistake | Better Approach |
|---|---|
| Using `latest` for production images | Use immutable version tags or image digests |
| Running containers as root | Use non-root users |
| Storing secrets in Git | Use a secret manager |
| Using Pod IPs directly | Use Kubernetes Services |
| Creating individual Pods for production applications | Use Deployments or appropriate controllers |
| Setting unrealistic resource limits | Measure and tune resource requirements |
| Ignoring readiness probes | Configure correct readiness checks |
| Using liveness probes for every external dependency | Keep liveness checks focused on process health |
| Ignoring persistent storage | Use appropriate PV/PVC storage |
| Exposing databases publicly | Restrict network access |
| Giving every user cluster-admin access | Apply least-privilege RBAC |
| Ignoring network policies | Restrict service communication |
| Using Helm without understanding rendered YAML | Inspect templates and generated manifests |
| Introducing a service mesh unnecessarily | Evaluate operational overhead |
| Applying production changes manually | Use controlled GitOps workflows |
| Ignoring logs and metrics | Implement observability |
| Deploying without rollback planning | Define recovery procedures |
| Using canary deployment without monitoring | Establish measurable promotion criteria |

---

# PART 29: INTERVIEW QUESTIONS AND ANSWERS

## 57. Basic Questions

### Q1. What is Docker?

Docker is a containerization platform used to package and run applications in isolated environments.

### Q2. What is a Docker image?

A Docker image is a read-only template used to create containers.

### Q3. What is a Docker container?

A container is an isolated runtime instance created from an image.

### Q4. What is Docker networking?

It is the mechanism that allows containers to communicate with one another and external networks.

### Q5. What is a Docker volume?

A Docker volume provides persistent storage independent of a container's writable lifecycle.

### Q6. What is a multi-stage Docker build?

It uses multiple build stages to separate build-time dependencies from the final runtime image.

### Q7. What is Kubernetes?

Kubernetes is a container orchestration platform that automates deployment, scaling and management of containerized workloads.

### Q8. What is a Kubernetes cluster?

A Kubernetes cluster is a collection of control-plane and worker-node resources that run and manage containerized workloads.

### Q9. What is a Pod?

A Pod is the smallest deployable unit in Kubernetes.

### Q10. What is a Deployment?

A Deployment manages ReplicaSets and controlled updates for application workloads.

## 58. Intermediate Questions

### Q11. What is a ReplicaSet?

A ReplicaSet maintains the desired number of matching Pods.

### Q12. What is a Kubernetes Service?

A Service provides a stable network endpoint for accessing one or more Pods.

### Q13. What is ClusterIP?

ClusterIP exposes a Service within the Kubernetes cluster.

### Q14. What is NodePort?

NodePort exposes a Service through a port on cluster nodes.

### Q15. What is LoadBalancer?

It exposes a Service through an external load balancer supported by the infrastructure integration.

### Q16. What is Ingress?

Ingress defines HTTP and HTTPS routing from outside a cluster to internal Services.

### Q17. What is Gateway API?

Gateway API is a Kubernetes API family for configuring more expressive traffic routing and infrastructure relationships.

### Q18. What is ConfigMap?

A ConfigMap stores non-sensitive application configuration.

### Q19. What is a Kubernetes Secret?

A Secret is a Kubernetes resource intended for sensitive configuration.

### Q20. What is PersistentVolume?

A PersistentVolume represents storage available to a Kubernetes cluster.

## 59. Advanced Questions

### Q21. What is PersistentVolumeClaim?

A PVC is a request for storage made by a workload.

### Q22. What is StorageClass?

A StorageClass defines how storage can be dynamically provisioned.

### Q23. What is HPA?

Horizontal Pod Autoscaler automatically changes the number of replicas based on observed metrics.

### Q24. What are resource requests?

They specify the resource requirements used for Pod scheduling.

### Q25. What are resource limits?

They constrain the maximum resource consumption for supported resource types.

### Q26. What is a liveness probe?

It checks whether an application is functioning and can trigger a container restart when repeatedly failing.

### Q27. What is a readiness probe?

It checks whether an application is ready to receive traffic.

### Q28. What is a startup probe?

It checks whether an application has completed startup.

### Q29. What is Helm?

Helm is a package manager for Kubernetes.

### Q30. What is a Helm Chart?

A Helm Chart is a package containing Kubernetes resource templates and configuration.

### Q31. What is a Kubernetes Operator?

An Operator automates application-specific operational tasks through Kubernetes APIs and controllers.

### Q32. What is a CRD?

A Custom Resource Definition extends the Kubernetes API with a new resource type.

### Q33. What is a Service Mesh?

A Service Mesh is an infrastructure layer that manages service-to-service communication.

### Q34. What is Istio?

Istio is a service mesh platform providing traffic management, security and observability.

### Q35. What is Envoy?

Envoy is a proxy commonly used as a data-plane component in service mesh architectures.

### Q36. What is mTLS?

Mutual TLS authenticates both communicating parties using certificates.

### Q37. What is a VirtualService?

In Istio, a VirtualService defines traffic-routing rules for a destination.

### Q38. What is a DestinationRule?

It defines policies for traffic to a destination, including subsets and connection-related settings.

### Q39. What is Canary Deployment?

It releases a new application version to a small percentage of traffic before expanding the rollout.

### Q40. What is Blue-Green Deployment?

It maintains two environments and switches traffic from the old environment to the new one.

### Q41. What is GitOps?

GitOps is an approach that uses Git as the source of truth for declarative infrastructure and application configuration.

### Q42. What is Argo CD?

Argo CD is a GitOps continuous-delivery tool for Kubernetes.

### Q43. What is Prometheus?

Prometheus is a monitoring and alerting toolkit for collecting and querying metrics.

### Q44. What is Grafana?

Grafana is an observability platform used to visualize metrics and other telemetry.

### Q45. What is RBAC?

Role-Based Access Control defines permissions based on assigned roles.

### Q46. What is NetworkPolicy?

NetworkPolicy controls permitted network traffic to and from selected Pods.

### Q47. What is the difference between Docker and Kubernetes?

Docker is commonly used to build and run containers, while Kubernetes orchestrates containerized workloads across a cluster.

### Q48. What is the difference between Helm and Argo CD?

Helm packages and templates Kubernetes applications. Argo CD continuously reconciles Kubernetes clusters with desired configuration stored in Git.

### Q49. What is the difference between Ingress and Gateway API?

Ingress provides HTTP/HTTPS routing with a simpler model. Gateway API provides a more expressive and role-oriented traffic-management API.

### Q50. What is the difference between sidecar and ambient service mesh architectures?

Sidecar mode places a proxy alongside each application Pod. Ambient mode uses node-level proxies for Layer 4 functionality and optional waypoint proxies for additional Layer 7 capabilities.

---

# PART 30: PRACTICAL ASSIGNMENTS

## 60. Assignment 1 – Advanced Docker

Create a Node.js application and:

1. Write a Dockerfile.
2. Create a multi-stage build.
3. Add `.dockerignore`.
4. Run the application as a non-root user.
5. Configure environment variables.
6. Add a health endpoint.
7. Build and run the image.

## 61. Assignment 2 – Docker Networking

Create three containers:

```text
Auth Service
Product Service
Order Service
```

Requirements:

- Create a custom bridge network.
- Connect all services.
- Enable service-name-based communication.
- Restrict unnecessary exposed ports.

## 62. Assignment 3 – Docker Storage

Create a MySQL container.

Requirements:

- Create a named volume.
- Store MySQL data in that volume.
- Delete and recreate the container.
- Verify that database data persists.

## 63. Assignment 4 – Kubernetes Cluster

Set up a local Kubernetes cluster using:

- Minikube.
- Kind.
- Docker Desktop Kubernetes, if available.

Practice:

```bash
kubectl get nodes
kubectl get pods
kubectl get namespaces
kubectl get services
kubectl get deployments
```

## 64. Assignment 5 – Deploy a Node.js Application

Create:

- Deployment.
- ReplicaSet.
- Service.
- ConfigMap.
- Secret.

Deploy a Node.js API and access it through a Kubernetes Service.

## 65. Assignment 6 – Kubernetes Networking

Create:

- Product Service.
- Order Service.
- Payment Service.

Configure internal communication using Kubernetes DNS.

Add a NetworkPolicy that restricts access to Payment Service.

## 66. Assignment 7 – Persistent Storage

Deploy MySQL using:

- PersistentVolumeClaim.
- StorageClass.
- Persistent storage.

Delete the MySQL Pod and verify that its data persists.

## 67. Assignment 8 – Autoscaling

Configure HPA.

Requirements:

- Minimum replicas: 2.
- Maximum replicas: 10.
- CPU target: 70%.
- Generate test traffic.
- Observe scaling behavior.

## 68. Assignment 9 – Health Checks

Implement these endpoints in a Node.js application:

```text
/health/live
/health/ready
/health/startup
```

Configure all three Kubernetes probes.

Simulate:

- Application startup delay.
- Application unavailability.
- Readiness failure.

Observe how Kubernetes responds.

## 69. Assignment 10 – Helm

Create a Helm Chart for your Node.js application.

Requirements:

- Chart.yaml.
- values.yaml.
- Deployment template.
- Service template.
- ConfigMap template.
- Separate development and production values.

Install and upgrade the Chart.

## 70. Assignment 11 – Kubernetes Operator

Study an existing database Operator.

Understand:

- CRD.
- Custom Resource.
- Controller.
- Reconciliation loop.
- Status updates.

Create a simple demonstration CRD if you want to explore the Kubernetes extension model.

## 71. Assignment 12 – Istio Service Mesh

Install Istio in a local Kubernetes environment.

Practice:

- Sidecar injection or ambient enrollment.
- Service-to-service communication.
- mTLS.
- VirtualService.
- DestinationRule.
- AuthorizationPolicy.

## 72. Assignment 13 – Canary Deployment

Deploy two versions of a Node.js application.

Configure traffic distribution:

```text
Version 1 = 90%
Version 2 = 10%
```

Monitor error rates and gradually increase traffic to version 2.

## 73. Assignment 14 – GitOps with Argo CD

Create a Git repository containing Kubernetes manifests.

Install Argo CD.

Connect the repository.

Practice:

- Initial synchronization.
- Automated synchronization.
- Out-of-sync detection.
- Self-healing.
- Rollback using Git history.

## 74. Assignment 15 – Complete Production Deployment

Build and deploy a microservices application.

### Required Services

- Auth Service.
- Product Service.
- Order Service.
- Payment Service.
- Notification Service.

### Required Technologies

- Node.js.
- TypeScript.
- Docker.
- Kubernetes.
- Helm.
- Istio.
- Argo CD.
- Prometheus.
- Grafana.
- OpenTelemetry.

### Required Features

**Containerization**
- Multi-stage builds.
- Non-root containers.
- Image scanning.

**Kubernetes**
- Deployments.
- Services.
- ConfigMaps.
- Secrets.
- HPA.
- Persistent storage.
- Probes.

**Networking**
- Service discovery.
- Gateway API or Ingress.
- NetworkPolicies.

**Service Mesh**
- mTLS.
- Traffic routing.
- Canary deployment.

**GitOps**
- Git repository.
- Argo CD.
- Automated synchronization.

**Monitoring**
- Metrics.
- Logs.
- Distributed tracing.
- Alerts.

---

# PART 31: QUICK REVISION

## 75. Important Concepts Summary

| Concept | Key Point |
|---|---|
| Docker | Containerization platform |
| Docker Image | Read-only application template |
| Docker Container | Running instance of an image |
| Docker Daemon | Manages Docker objects |
| Bridge Network | Connects containers on a host |
| Overlay Network | Supports multi-host container networking |
| Docker Volume | Persistent storage |
| Multi-Stage Build | Separates build and runtime stages |
| Kubernetes | Container orchestration platform |
| Cluster | Collection of control-plane and worker resources |
| Control Plane | Manages cluster state |
| API Server | Kubernetes API entry point |
| etcd | Cluster state store |
| Scheduler | Assigns Pods to nodes |
| Controller Manager | Reconciles desired and actual state |
| Kubelet | Manages Pods on worker nodes |
| Pod | Smallest deployable Kubernetes unit |
| ReplicaSet | Maintains Pod replicas |
| Deployment | Manages application rollouts |
| Service | Stable network endpoint for Pods |
| ClusterIP | Internal Service exposure |
| NodePort | Node-level port exposure |
| LoadBalancer | External load-balancer exposure |
| Ingress | HTTP/HTTPS routing resource |
| Gateway API | Advanced Kubernetes traffic-routing APIs |
| ConfigMap | Non-sensitive configuration |
| Secret | Sensitive configuration resource |
| PersistentVolume | Available storage resource |
| PVC | Storage request |
| StorageClass | Storage provisioning configuration |
| HPA | Automatic Pod replica scaling |
| Resource Request | Scheduling resource requirement |
| Resource Limit | Resource consumption constraint |
| Liveness Probe | Checks application health |
| Readiness Probe | Checks traffic readiness |
| Startup Probe | Checks startup completion |
| Helm | Kubernetes package manager |
| Helm Chart | Reusable Kubernetes package |
| Operator | Application lifecycle automation |
| CRD | Extends Kubernetes API |
| Service Mesh | Manages service-to-service communication |
| Istio | Service mesh platform |
| Envoy | Data-plane proxy |
| mTLS | Mutual certificate-based authentication |
| VirtualService | Defines Istio traffic routing |
| DestinationRule | Defines destination policies |
| Canary | Gradual traffic rollout |
| Blue-Green | Switches between two environments |
| GitOps | Git-based declarative operations |
| Argo CD | GitOps delivery tool |
| Prometheus | Metrics collection |
| Grafana | Metrics visualization |
| RBAC | Role-based access control |
| NetworkPolicy | Controls Pod network traffic |

---

# PART 32: OFFICIAL DOCUMENTATION AND LEARNING RESOURCES

Use these resources for deeper practical learning.

### Docker

- [Docker Documentation](https://docs.docker.com/?utm_source=chatgpt.com)
- [Docker Multi-Stage Builds](https://docs.docker.com/build/building/multi-stage?utm_source=chatgpt.com)
- [Docker Build Best Practices](https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com)

### Kubernetes

- [Kubernetes Documentation](https://kubernetes.io/docs/?utm_source=chatgpt.com)
- [Kubernetes Architecture and Concepts](https://kubernetes.io/docs/concepts/index.html?utm_source=chatgpt.com)
- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
- [Kubernetes Services and Networking](https://kubernetes.io/docs/concepts/services-networking/?utm_source=chatgpt.com)
- [Kubernetes Security](https://kubernetes.io/docs/concepts/security/?utm_source=chatgpt.com)

### Helm

- [Helm Documentation](https://helm.sh/docs/?utm_source=chatgpt.com)

### Istio

- [Istio Documentation](https://istio.io/latest/docs/?utm_source=chatgpt.com)
- [Istio Traffic Management](https://istio.io/latest/docs/concepts/traffic-management/?utm_source=chatgpt.com)
- [Istio Ambient Mode](https://istio.io/latest/docs/ambient/overview/?utm_source=chatgpt.com)

### GitOps and Argo CD

- [Argo CD Documentation](https://argo-cd.readthedocs.io/en/stable/?utm_source=chatgpt.com)
- [Argo CD Helm Integration](https://argo-cd.readthedocs.io/en/stable/user-guide/helm/?utm_source=chatgpt.com)

### Monitoring

- [Prometheus Documentation](https://prometheus.io/docs/?utm_source=chatgpt.com)
- [Grafana Documentation](https://grafana.com/docs/?utm_source=chatgpt.com)
- [OpenTelemetry Documentation](https://opentelemetry.io/docs/?utm_source=chatgpt.com)

---

# PHASE 54 – COMPLETION CHECKLIST

Before considering this phase complete, make sure you understand the following.

### Docker

- [ ] Understand Docker architecture.
- [ ] Understand Docker networking.
- [ ] Understand bridge and overlay networks.
- [ ] Understand volumes and bind mounts.
- [ ] Create multi-stage Docker builds.
- [ ] Apply Docker security practices.

### Kubernetes

- [ ] Understand Kubernetes architecture.
- [ ] Understand Control Plane components.
- [ ] Understand Worker Node components.
- [ ] Create Pods.
- [ ] Create Deployments.
- [ ] Understand ReplicaSets.
- [ ] Create Services.
- [ ] Understand Ingress and Gateway API.
- [ ] Understand Kubernetes DNS.

### Configuration and Storage

- [ ] Create ConfigMaps.
- [ ] Create Secrets.
- [ ] Understand PersistentVolumes.
- [ ] Understand PersistentVolumeClaims.
- [ ] Understand StorageClasses.
- [ ] Understand StatefulSets.

### Scaling and Reliability

- [ ] Configure HPA.
- [ ] Understand resource requests.
- [ ] Understand resource limits.
- [ ] Configure liveness probes.
- [ ] Configure readiness probes.
- [ ] Configure startup probes.

### Advanced Kubernetes

- [ ] Create Helm Charts.
- [ ] Understand Operators.
- [ ] Understand CRDs.
- [ ] Understand Kubernetes networking.
- [ ] Configure NetworkPolicies.
- [ ] Understand Kubernetes RBAC.

### Service Mesh

- [ ] Understand Service Mesh architecture.
- [ ] Understand Istio.
- [ ] Understand Envoy.
- [ ] Understand sidecar architecture.
- [ ] Understand ambient architecture.
- [ ] Configure mTLS.
- [ ] Understand VirtualService.
- [ ] Understand DestinationRule.
- [ ] Implement canary traffic routing.

### DevOps and GitOps

- [ ] Understand Canary Deployment.
- [ ] Understand Blue-Green Deployment.
- [ ] Understand GitOps.
- [ ] Install Argo CD.
- [ ] Connect Git repository to Argo CD.
- [ ] Understand automated synchronization.
- [ ] Understand rollback strategies.

### Monitoring and Security

- [ ] Understand Prometheus.
- [ ] Understand Grafana.
- [ ] Understand centralized logging.
- [ ] Understand distributed tracing.
- [ ] Implement RBAC.
- [ ] Apply container security practices.
- [ ] Secure Kubernetes Secrets.
- [ ] Understand NetworkPolicies.

---

# What Comes Next?

## Phase 55 – Advanced Cloud Computing, AWS Architecture and Production Infrastructure

The next phase will cover:

1. Cloud Computing Fundamentals.
2. IaaS, PaaS and SaaS.
3. Public, Private and Hybrid Cloud.
4. AWS Global Infrastructure.
5. AWS Regions and Availability Zones.
6. AWS IAM and Identity Management.
7. EC2 Advanced Architecture.
8. Auto Scaling Groups.
9. Application Load Balancer.
10. Network Load Balancer.
11. VPC Architecture.
12. Public and Private Subnets.
13. Route Tables and Internet Gateways.
14. NAT Gateway.
15. Security Groups and Network ACLs.
16. Route 53 and DNS.
17. CloudFront CDN.
18. S3 Advanced Storage.
19. RDS and Multi-AZ Architecture.
20. Aurora Database.
21. ElastiCache and Redis.
22. AWS Lambda and Serverless Architecture.
23. API Gateway.
24. SQS and SNS.
25. EventBridge.
26. AWS ECR and ECS.
27. EKS – Managed Kubernetes.
28. AWS Secrets Manager.
29. CloudWatch and CloudTrail.
30. AWS Cost Optimization.
31. High Availability and Disaster Recovery.
32. Production-Grade AWS Architecture.

**Phase 54 is complete. When you're ready, say "OK, Phase 55" and we'll continue with detailed notes.**