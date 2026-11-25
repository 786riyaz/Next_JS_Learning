# Phase 46 – Kubernetes, Container Orchestration and Cluster Management

**Level:** Advanced  
**Domain:** DevOps, Container Orchestration, Cloud-Native Engineering  
**Primary Technologies:** Kubernetes, Docker, kubectl, Helm, AWS EKS

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand Kubernetes and container orchestration.
2. Understand Docker vs Kubernetes.
3. Understand Kubernetes cluster architecture.
4. Understand Control Plane and Worker Nodes.
5. Learn Kubernetes components:
   - API Server.
   - Scheduler.
   - Controller Manager.
   - etcd.
   - Kubelet.
   - Kube-proxy.
6. Understand Pods, ReplicaSets and Deployments.
7. Configure Kubernetes Services.
8. Understand ClusterIP, NodePort and LoadBalancer.
9. Work with Namespaces.
10. Manage application configuration using ConfigMaps and Secrets.
11. Understand Persistent Volumes and Persistent Volume Claims.
12. Work with Storage Classes.
13. Understand Kubernetes networking and DNS.
14. Configure Ingress and Gateway API.
15. Implement health checks and probes.
16. Configure resource requests and limits.
17. Understand Horizontal Pod Autoscaling.
18. Work with StatefulSets, DaemonSets, Jobs and CronJobs.
19. Manage applications using Helm.
20. Deploy Node.js and Next.js applications.
21. Understand Kubernetes security.
22. Monitor and troubleshoot Kubernetes clusters.
23. Understand Amazon EKS.
24. Design production-ready Kubernetes architectures.

---

# PART 1: INTRODUCTION TO KUBERNETES

# 2. What Is Kubernetes?

Kubernetes is an open-source container orchestration platform.

It automates the deployment, scaling, management and recovery of containerized applications.

Kubernetes is commonly abbreviated as:

```text
K8s
```

The abbreviation comes from the eight letters between K and s in Kubernetes.

You learned Docker in Phase 45.

Docker helps you build and run containers.

But imagine that you have a production application running on 20 servers with hundreds of containers.

You need to manage:

- Container deployment.
- Application availability.
- Container failures.
- Scaling.
- Load distribution.
- Networking.
- Storage.
- Configuration.
- Application updates.
- Recovery.

Managing all these things manually becomes complicated.

This is where Kubernetes becomes useful.

## 2.1 Why Do We Need Kubernetes?

Suppose you have a Node.js application.

Initially, you deploy one container:

```text
              User
                |
                v
          Node.js Container
```

Now imagine:

- 10,000 users access the application.
- The container crashes.
- The server runs out of memory.
- You need five application instances.
- You need to deploy a new application version without downtime.

Managing these operations manually is difficult.

Kubernetes automates many of these responsibilities.

### Without Kubernetes

```text
                 Users
                   |
                   v
              Load Balancer
                   |
          +--------+--------+
          |        |        |
          v        v        v
       Docker   Docker   Docker
       Server   Server   Server

       Manual Management
       Manual Scaling
       Manual Recovery
```

### With Kubernetes

```text
                   Users
                     |
                     v
               Load Balancer
                     |
                     v
              Kubernetes Cluster
                     |
          +----------+----------+
          |          |          |
          v          v          v
        Pod 1      Pod 2      Pod 3
          |          |          |
          v          v          v
       Container  Container  Container

          Kubernetes manages:
          - Deployment
          - Scaling
          - Recovery
          - Scheduling
          - Networking
```

## 2.2 Main Features of Kubernetes

| Feature | Description |
|---|---|
| Container Orchestration | Manages containerized applications |
| Self-Healing | Recreates or replaces failed workloads |
| Auto Scaling | Adjusts workload replicas based on configured policies |
| Load Distribution | Distributes traffic through Services |
| Service Discovery | Provides stable service addresses and DNS |
| Rolling Updates | Updates applications gradually |
| Rollbacks | Allows deployment revisions to be rolled back |
| Storage Management | Manages persistent storage integration |
| Configuration Management | Manages application configuration |
| Resource Management | Controls resource requests and limits |

Kubernetes uses declarative configuration and controllers to maintain the desired state of applications. [Kubernetes](https://kubernetes.io/docs/concepts/index.html?utm_source=chatgpt.com)

---

# 3. Docker vs Kubernetes

You should understand that Docker and Kubernetes are not direct replacements for each other.

Docker provides tools for building and running containers.

Kubernetes orchestrates containerized workloads across a cluster.

| Feature | Docker | Kubernetes |
|---|---|---|
| Main Purpose | Build and run containers | Orchestrate containerized workloads |
| Image Building | Yes | Normally uses prebuilt images |
| Container Management | Yes | Yes, through its runtime integration |
| Cluster Management | Not its primary purpose | Yes |
| Automatic Recovery | Basic restart mechanisms | Workload reconciliation |
| Scaling | Manually or through other tools | Built-in workload scaling mechanisms |
| Load Balancing | Docker networking and related tools | Kubernetes Services and integrations |
| Configuration | Environment variables and other mechanisms | ConfigMaps, Secrets and other resources |
| Storage | Volumes and mounts | PV, PVC, StorageClass and CSI |
| Production Use | Individual containers and simpler deployments | Distributed container orchestration |

### Relationship

```text
              Docker / Build Tools
                      |
                      v
                 Docker Image
                      |
                      v
                 Image Registry
                      |
                      v
                  Kubernetes
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
        Pod 1       Pod 2       Pod 3
          |           |           |
          v           v           v
       Container  Container   Container
```

**Important:** Kubernetes does not require Docker Engine as its container runtime. Modern Kubernetes clusters commonly use containerd or CRI-O through the Container Runtime Interface (CRI).

---

# PART 2: KUBERNETES ARCHITECTURE

# 4. What Is a Kubernetes Cluster?

A Kubernetes cluster is a group of machines that work together to run containerized applications.

A cluster contains two major components:

1. Control Plane.
2. Worker Nodes.

```text
                  Kubernetes Cluster
                           |
              +------------+------------+
              |                         |
              v                         v
         Control Plane              Worker Nodes
              |                         |
       +------+------+           +------+------+ 
       |      |      |           |      |      |
       v      v      v           v      v      v
     API   Scheduler etcd       Pod    Pod    Pod
   Server
```

## 4.1 Control Plane

The Control Plane manages the cluster.

Its responsibilities include:

- Managing cluster state.
- Scheduling workloads.
- Managing deployments.
- Monitoring resources.
- Maintaining desired state.
- Processing API requests.

## 4.2 Worker Nodes

Worker Nodes run application workloads.

They contain components such as:

- Kubelet.
- Container Runtime.
- Kube-proxy, where used.
- Pods.

Example:

```text
Worker Node
    |
    +----------------------+
    |                      |
    v                      v
  Pod A                  Pod B
    |                      |
    v                      v
Node.js                 MySQL
Container               Container
```

A production cluster generally has multiple worker nodes to improve availability and distribute workloads.

Official reference: [Kubernetes Cluster Architecture](https://kubernetes.io/docs/concepts/architecture/?utm_source=chatgpt.com)

---

# 5. Kubernetes Control Plane Components

The Control Plane contains several important components.

## 5.1 API Server

The API Server is the central communication interface of Kubernetes.

Its component name is:

```text
kube-apiserver
```

It exposes the Kubernetes API.

Whenever you execute:

```bash
kubectl get pods
```

The kubectl client communicates with the API Server.

Similarly, when you create a Deployment:

```bash
kubectl apply -f deployment.yaml
```

The request is sent to the API Server.

### Responsibilities

- Accept API requests.
- Authenticate users.
- Authorize operations.
- Validate resource configurations.
- Process API operations.
- Communicate with etcd.
- Expose cluster resources.

Architecture:

```text
Developer
    |
    v
 kubectl
    |
    v
API Server
    |
    v
  etcd
```

The API Server is the primary entry point for Kubernetes API operations.

## 5.2 etcd

etcd is a distributed key-value store.

Kubernetes uses it to store cluster state and configuration data.

Examples of information stored include:

- Deployments.
- Pods.
- Services.
- Secrets.
- ConfigMaps.
- Cluster configuration.

Conceptually:

```text
               Kubernetes
                    |
                    v
                 etcd
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Pods     Services  Deployments
```

### Important Points

- etcd is critical to cluster operation.
- It stores Kubernetes API data.
- Backups are essential for self-managed control planes.
- Access must be strongly restricted.
- Its data should be protected using encryption and appropriate access controls.

**Important:** Do not manually modify etcd data as a normal way of managing Kubernetes resources. Use the Kubernetes API.

## 5.3 Scheduler

The Scheduler decides which worker node should run a newly created Pod.

Component:

```text
kube-scheduler
```

Suppose you create a Pod.

```text
Pod Created
    |
    v
Scheduler
    |
    v
Select Suitable Node
    |
    v
Worker Node A
```

The scheduler considers factors such as:

- CPU requests.
- Memory requests.
- Node capacity.
- Node selectors.
- Affinity and anti-affinity.
- Taints and tolerations.
- Scheduling constraints.

### Example

Suppose:

| Node | Available CPU |
|---|---:|
| Node A | 0.5 CPU |
| Node B | 4 CPU |
| Node C | 2 CPU |

Your Pod requests:

```yaml
resources:
  requests:
    cpu: "1"
```

Node A cannot satisfy the request based on its available schedulable capacity.

The scheduler considers suitable nodes and selects one according to its scheduling process.

## 5.4 Controller Manager

The Controller Manager runs controllers that continuously observe the cluster and work to maintain the desired state.

Component:

```text
kube-controller-manager
```

Example:

You request three application replicas.

```yaml
replicas: 3
```

But only two Pods are running.

The relevant controllers work to create the missing Pod.

```text
Desired Replicas: 3
Actual Replicas: 2

       Controller
           |
           v
    Detect Difference
           |
           v
       Create Pod
           |
           v
 Actual Replicas: 3
```

This process is called **reconciliation**.

## 5.5 Cloud Controller Manager

The Cloud Controller Manager integrates Kubernetes with cloud providers.

It can help manage:

- Cloud load balancers.
- Node information.
- Cloud routes.
- Cloud-specific resources.

It is relevant when running Kubernetes on cloud infrastructure.

For example, AWS integrations allow Kubernetes resources to interact with AWS services.

### Control Plane Revision

| Component | Responsibility |
|---|---|
| API Server | Kubernetes API |
| etcd | Cluster state storage |
| Scheduler | Pod placement |
| Controller Manager | Reconciliation |
| Cloud Controller Manager | Cloud integration |

---

# 6. Kubernetes Worker Node Components

Every worker node requires components that allow it to run Pods.

## 6.1 Kubelet

Kubelet is an agent that runs on each worker node.

Its responsibility is to ensure that the containers described by assigned Pod specifications are running and healthy.

Example:

```text
Control Plane
      |
      v
API Server
      |
      v
Worker Node
      |
      v
    Kubelet
      |
      v
   Container
```

Kubelet communicates with the API Server and works with the container runtime.

## 6.2 Container Runtime

The container runtime executes containers.

Examples:

- containerd.
- CRI-O.

Kubernetes communicates with the runtime through the Container Runtime Interface (CRI).

## 6.3 Kube-proxy

Kube-proxy implements part of Kubernetes Service networking on nodes in clusters that use it.

It maintains networking rules that enable Service traffic to reach backend Pods.

Some network implementations provide equivalent Service functionality without running kube-proxy.

## 6.4 Node Components Summary

| Component | Responsibility |
|---|---|
| Kubelet | Manages Pods assigned to a node |
| Container Runtime | Runs containers |
| Kube-proxy | Implements Service networking where used |
| CNI Plugin | Provides Pod network connectivity |

Official reference: [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/?utm_source=chatgpt.com)

---

# PART 3: KUBERNETES INSTALLATION AND SETUP

# 7. Kubernetes Learning Environments

You do not need an AWS account to start learning Kubernetes.

You can run a local cluster.

Common options include:

1. Minikube.
2. Kind.
3. Docker Desktop Kubernetes.
4. Managed Kubernetes such as Amazon EKS.

## 7.1 Minikube

Minikube creates a local Kubernetes cluster.

It is suitable for learning:

- Pods.
- Deployments.
- Services.
- ConfigMaps.
- Volumes.
- Ingress.
- Other Kubernetes resources.

Official website:

[Minikube](https://minikube.sigs.k8s.io/docs/?utm_source=chatgpt.com)

### Install on Windows

You can install Minikube using the official installation instructions.

If using Windows Package Manager:

```powershell
winget install Kubernetes.minikube
```

Verify:

```powershell
minikube version
```

## 7.2 kubectl

`kubectl` is the Kubernetes command-line tool.

You use it to communicate with the Kubernetes API Server.

Install it using the official documentation:

[Install kubectl](https://kubernetes.io/docs/tasks/tools/?utm_source=chatgpt.com)

Verify:

```bash
kubectl version --client
```

## 7.3 Start Minikube

If Docker Desktop is running:

```bash
minikube start --driver=docker
```

Check cluster:

```bash
kubectl cluster-info
```

Check nodes:

```bash
kubectl get nodes
```

Expected output resembles:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   ...
```

## 7.4 Check Kubernetes Resources

```bash
kubectl get all
```

Check namespaces:

```bash
kubectl get namespaces
```

Check nodes:

```bash
kubectl get nodes -o wide
```

### Important

For learning, Minikube is a good starting point.

For production, you generally use a managed or properly administered Kubernetes cluster.

---

# PART 4: PODS

# 8. What Is a Pod?

A Pod is the smallest deployable compute object in Kubernetes.

A Pod contains one or more containers.

Containers inside the same Pod share:

- Network namespace.
- Pod IP address.
- Port space.
- Configured shared volumes.

They can communicate with each other through:

```text
localhost
```

### Single-Container Pod

Most application Pods contain one main application container.

```text
             Pod
              |
              v
       Node.js Container
```

### Multi-Container Pod

A Pod can contain multiple closely related containers.

```text
                  Pod
                   |
          +--------+--------+
          |                 |
          v                 v
     Application         Sidecar
      Container         Container
          |                 |
          +--------+--------+
                   |
              Shared Volume
```

For example:

- Application container.
- Logging sidecar.
- Proxy sidecar.

### Important

Containers inside a Pod are scheduled together and share the Pod's network identity.

They are not independent application replicas.

Official reference: [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/?utm_source=chatgpt.com)

---

# 9. Create Your First Pod

Create a file:

```text
nginx-pod.yaml
```

Add:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx:1.28
      ports:
        - containerPort: 80
```

This defines a Pod containing one Nginx container.

### Explanation

| Field | Meaning |
|---|---|
| `apiVersion` | Kubernetes API version |
| `kind` | Resource type |
| `metadata` | Resource information |
| `name` | Pod name |
| `spec` | Desired configuration |
| `containers` | Container definitions |
| `image` | Container image |
| `containerPort` | Port used by the application |

### Apply Manifest

```bash
kubectl apply -f nginx-pod.yaml
```

Check:

```bash
kubectl get pods
```

Detailed information:

```bash
kubectl describe pod nginx-pod
```

View logs:

```bash
kubectl logs nginx-pod
```

Delete:

```bash
kubectl delete pod nginx-pod
```

---

# 10. Important Pod Commands

| Command | Purpose |
|---|---|
| `kubectl get pods` | List Pods |
| `kubectl get pods -o wide` | Show additional information |
| `kubectl describe pod NAME` | Detailed Pod information |
| `kubectl logs NAME` | View Pod logs |
| `kubectl logs -f NAME` | Follow logs |
| `kubectl exec -it NAME -- sh` | Open shell inside a container |
| `kubectl delete pod NAME` | Delete Pod |
| `kubectl get pod NAME -o yaml` | View Pod configuration |

### Important Pod States

| Status | Meaning |
|---|---|
| Pending | Waiting for scheduling or setup |
| Running | Pod is running |
| Succeeded | All containers completed successfully |
| Failed | All containers terminated and at least one failed |
| Unknown | Pod state cannot be determined |

You may also encounter container states such as:

- `Waiting`
- `Running`
- `Terminated`
- `CrashLoopBackOff`
- `ImagePullBackOff`

These are useful when troubleshooting.

---

# PART 5: REPLICASETS AND DEPLOYMENTS

# 11. What Is a ReplicaSet?

A ReplicaSet maintains a specified number of identical Pod replicas.

Suppose your application needs three running instances.

```text
Desired Replicas: 3
```

You create a ReplicaSet.

```text
              ReplicaSet
                   |
          +--------+--------+
          |        |        |
          v        v        v
        Pod 1    Pod 2    Pod 3
```

If one Pod disappears, the ReplicaSet works to create another.

Example manifest:

```yaml
apiVersion: apps/v1

kind: ReplicaSet

metadata:
  name: nginx-replicaset

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:1.28
```

Apply:

```bash
kubectl apply -f replicaset.yaml
```

Check:

```bash
kubectl get replicasets
```

**Important:** In normal application deployments, you generally create Deployments rather than managing ReplicaSets directly.

---

# 12. What Is a Deployment?

A Deployment manages stateless application workloads through ReplicaSets.

It provides:

- Declarative application updates.
- Replica management.
- Rolling updates.
- Rollbacks.
- Deployment revision history.

Architecture:

```text
                 Deployment
                      |
                      v
                  ReplicaSet
                      |
            +---------+---------+
            |         |         |
            v         v         v
          Pod 1     Pod 2     Pod 3
```

A Deployment makes application management easier than creating and maintaining Pods manually.

Official reference: [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)

---

# 13. Create Your First Deployment

Create:

```text
nginx-deployment.yaml
```

Add:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:1.28

          ports:
            - containerPort: 80
```

Apply:

```bash
kubectl apply -f nginx-deployment.yaml
```

Check:

```bash
kubectl get deployments
```

Check Pods:

```bash
kubectl get pods
```

Expected:

```text
NAME                                READY
nginx-deployment-xxxx-abc           1/1
nginx-deployment-xxxx-def           1/1
nginx-deployment-xxxx-ghi           1/1
```

The exact generated Pod names will differ.

## 13.1 Scale Deployment

Suppose you want five replicas.

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Verify:

```bash
kubectl get pods
```

Now Kubernetes attempts to maintain five replicas.

## 13.2 Update Deployment Image

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.29
```

Check rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

## 13.3 View Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

## 13.4 Rollback Deployment

```bash
kubectl rollout undo deployment/nginx-deployment
```

## 13.5 Delete Deployment

```bash
kubectl delete deployment nginx-deployment
```

### Deployment vs ReplicaSet vs Pod

| Resource | Responsibility |
|---|---|
| Pod | Runs containers |
| ReplicaSet | Maintains Pod replica count |
| Deployment | Manages ReplicaSets and application rollout |

---

# PART 6: KUBERNETES SERVICES

# 14. Why Do We Need Services?

Pods are temporary.

When a Pod is recreated, its IP address may change.

Suppose your application has three backend Pods:

```text
Pod 1: 10.1.0.10
Pod 2: 10.1.0.11
Pod 3: 10.1.0.12
```

If one Pod is replaced, its IP address may change.

Your frontend should not need to track these changing addresses manually.

Kubernetes Services provide a stable network endpoint for a group of Pods.

```text
               Kubernetes Service
                       |
             +---------+---------+
             |         |         |
             v         v         v
           Pod 1     Pod 2     Pod 3
```

The Service selects Pods using labels and directs traffic to eligible endpoints.

Official reference: [Kubernetes Services and Networking](https://kubernetes.io/docs/concepts/services-networking/?utm_source=chatgpt.com)

---

# 15. Types of Kubernetes Services

The main Service types are:

1. ClusterIP.
2. NodePort.
3. LoadBalancer.
4. ExternalName.

## 15.1 ClusterIP

ClusterIP exposes a Service through an internal cluster IP address.

It is the default Service type.

Example:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: backend-service

spec:
  type: ClusterIP

  selector:
    app: backend

  ports:
    - port: 80
      targetPort: 3000
      protocol: TCP
```

### Explanation

```text
port: 80
```

The port exposed by the Service.

```text
targetPort: 3000
```

The port on the selected Pod.

### Architecture

```text
Frontend Pod
     |
     | backend-service:80
     |
     v
ClusterIP Service
     |
     v
Backend Pod Port 3000
```

ClusterIP is commonly used for internal communication.

## 15.2 NodePort

NodePort exposes a Service through a port on each node's IP address, subject to cluster networking and firewall rules.

Example:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: backend-nodeport

spec:
  type: NodePort

  selector:
    app: backend

  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30080
```

Now the Service can be reached through a node address and port:

```text
http://NODE_IP:30080
```

The default NodePort allocation range is generally:

```text
30000-32767
```

unless the cluster is configured differently.

## 15.3 LoadBalancer

LoadBalancer requests an external load balancer through the cluster's supported infrastructure integration.

Example:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: frontend-service

spec:
  type: LoadBalancer

  selector:
    app: frontend

  ports:
    - port: 80
      targetPort: 3000
```

On a supported cloud platform, the cloud integration may provision a load balancer.

### Architecture

```text
              Internet
                  |
                  v
            Cloud Load Balancer
                  |
                  v
             Kubernetes Service
                  |
           +------+------+
           |             |
           v             v
         Pod 1         Pod 2
```

On a local Minikube cluster, a LoadBalancer Service may remain pending until you use an appropriate local implementation or tunnel.

## 15.4 ExternalName

ExternalName maps a Kubernetes Service name to an external DNS name using a CNAME-style DNS record.

Example:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: external-database

spec:
  type: ExternalName
  externalName: database.example.com
```

This does not create a proxy or load balancer. It provides DNS-level aliasing.

### Service Comparison

| Service Type | Purpose | Typical Use |
|---|---|---|
| ClusterIP | Internal cluster access | Backend services |
| NodePort | Expose through node port | Testing and specific network setups |
| LoadBalancer | Cloud/external load balancer integration | Public application access |
| ExternalName | DNS alias to external hostname | External dependencies |

---

# PART 7: NAMESPACES

# 16. What Is a Namespace?

A Namespace provides a logical grouping of Kubernetes resources within a cluster.

Suppose you have one cluster used by three teams.

You can create:

```text
Kubernetes Cluster
       |
       +------------------+
       |        |         |
       v        v         v
      dev     staging   production
```

Each team can work with resources in its namespace.

### Common Uses

- Environment separation.
- Team isolation.
- Resource organization.
- Access-control boundaries.
- Resource quotas.

## 16.1 Create Namespace

```bash
kubectl create namespace development
```

Check:

```bash
kubectl get namespaces
```

## 16.2 Deploy Into Namespace

```bash
kubectl create deployment nginx \
--image=nginx:1.28 \
-n development
```

Check:

```bash
kubectl get pods -n development
```

## 16.3 Set Default Namespace

```bash
kubectl config set-context --current --namespace=development
```

Now commands use the selected namespace by default.

## 16.4 Delete Namespace

```bash
kubectl delete namespace development
```

Be careful: deleting a namespace also deletes the namespaced resources it contains.

### Important

Namespaces provide logical organization, not complete security isolation by themselves. Use RBAC, NetworkPolicies, quotas and other controls as appropriate.

---

# PART 8: CONFIGMAPS AND SECRETS

# 17. What Is a ConfigMap?

A ConfigMap stores non-sensitive configuration data.

Examples:

- Application environment.
- API endpoint.
- Feature flags.
- Log level.
- Service configuration.

Instead of hardcoding configuration into your application image, you can provide it separately.

Example:

```text
Application Image
       |
       v
Configuration
       |
       v
ConfigMap
```

## 17.1 Create ConfigMap

```bash
kubectl create configmap app-config \
--from-literal=APP_ENV=production \
--from-literal=LOG_LEVEL=info
```

View:

```bash
kubectl get configmaps
```

Inspect:

```bash
kubectl describe configmap app-config
```

## 17.2 ConfigMap YAML

```yaml
apiVersion: v1

kind: ConfigMap

metadata:
  name: app-config

data:
  APP_ENV: production
  LOG_LEVEL: info
  API_URL: https://api.example.com
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

## 17.3 Use ConfigMap in Deployment

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: node-app

spec:
  replicas: 2

  selector:
    matchLabels:
      app: node-app

  template:
    metadata:
      labels:
        app: node-app

    spec:
      containers:
        - name: backend
          image: myusername/node-app:1.0

          envFrom:
            - configMapRef:
                name: app-config
```

Now the application receives the ConfigMap entries as environment variables.

---

# 18. What Is a Kubernetes Secret?

A Secret stores sensitive configuration data.

Examples:

- Database passwords.
- API keys.
- Tokens.
- TLS certificates.

Example:

```text
Application Pod
       |
       v
     Secret
       |
       v
Database Password
```

## 18.1 Create Secret

```bash
kubectl create secret generic db-secret \
--from-literal=DB_USER=appuser \
--from-literal=DB_PASSWORD=change-this-password
```

View:

```bash
kubectl get secrets
```

Inspect metadata:

```bash
kubectl describe secret db-secret
```

## 18.2 Secret YAML

```yaml
apiVersion: v1

kind: Secret

metadata:
  name: db-secret

type: Opaque

stringData:
  DB_USER: appuser
  DB_PASSWORD: change-this-password
```

`stringData` allows you to supply plain text values in a manifest; Kubernetes converts them for storage through the API.

**Security warning:** This example is for learning only. Do not commit real credentials to Git.

## 18.3 Use Secret in Deployment

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: backend

spec:
  replicas: 2

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
          image: myusername/backend:1.0

          env:
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: DB_USER

            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: DB_PASSWORD
```

### ConfigMap vs Secret

| Feature | ConfigMap | Secret |
|---|---|---|
| Purpose | Non-sensitive configuration | Sensitive configuration |
| Examples | API URL, log level | Password, token |
| Encoding | Plain text values | Base64 representation in standard API representation |
| Encryption at rest | Depends on cluster configuration | Must be configured appropriately |
| Access control | RBAC | RBAC |
| Suitable for passwords | No | Yes, with proper security controls |

**Important:** Base64 is encoding, not encryption.

Kubernetes Secrets are not automatically secure merely because they are stored as Secret resources.

Production recommendations:

- Enable encryption at rest.
- Restrict RBAC permissions.
- Avoid storing secrets in Git.
- Consider external secret-management systems.
- Rotate credentials regularly.

---

# PART 9: KUBERNETES STORAGE

# 19. Why Does Kubernetes Need Persistent Storage?

Pods are temporary.

If a Pod is deleted and recreated, its container filesystem may not preserve application data.

This is a problem for databases.

Example:

```text
MySQL Pod
    |
    v
Database Files
    |
    v
Pod Deleted
    |
    v
Data May Be Lost
```

Kubernetes provides storage abstractions to manage persistent application data.

Important resources:

1. Volumes.
2. Persistent Volumes.
3. Persistent Volume Claims.
4. Storage Classes.

---

# 20. Kubernetes Volumes

A Volume provides storage that can be accessed by containers inside a Pod.

Example:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: app-pod

spec:
  containers:
    - name: app
      image: nginx:1.28

      volumeMounts:
        - name: app-storage
          mountPath: /usr/share/nginx/html

  volumes:
    - name: app-storage
      emptyDir: {}
```

`emptyDir` creates temporary storage associated with the Pod.

Its data survives an individual container restart while the Pod remains, but it is deleted when the Pod is removed.

---

# 21. Persistent Volume (PV)

A Persistent Volume represents storage available to a Kubernetes cluster.

It may be backed by:

- Cloud block storage.
- Network file storage.
- Other supported storage systems.

Example:

```text
Kubernetes Cluster
        |
        v
Persistent Volume
        |
        v
Storage System
```

A PV represents storage independently of the lifecycle of an individual Pod.

---

# 22. Persistent Volume Claim (PVC)

A Persistent Volume Claim is a request for storage by a workload.

Suppose your application needs:

```text
5 GB storage
```

You create a PVC requesting that capacity.

```text
Pod
 |
 v
PVC
 |
 v
PV
 |
 v
Physical / Cloud Storage
```

Example PVC:

```yaml
apiVersion: v1

kind: PersistentVolumeClaim

metadata:
  name: app-pvc

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 5Gi
```

Apply:

```bash
kubectl apply -f pvc.yaml
```

Check:

```bash
kubectl get pvc
```

## 22.1 Use PVC in Pod

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: storage-pod

spec:
  containers:
    - name: nginx
      image: nginx:1.28

      volumeMounts:
        - name: storage
          mountPath: /data

  volumes:
    - name: storage
      persistentVolumeClaim:
        claimName: app-pvc
```

Now the Pod mounts storage provided through the PVC.

---

# 23. StorageClass

A StorageClass defines how storage can be dynamically provisioned.

For example, a cloud storage provisioner can create a storage volume when a PVC requests one.

```text
             PVC
              |
              v
         StorageClass
              |
              v
        Storage Provisioner
              |
              v
         Storage Volume
              |
              v
              PV
```

StorageClass configuration depends on the cluster and storage driver.

For example, an AWS EKS cluster can use the Amazon EBS CSI driver to provision EBS-backed persistent storage.

### PV vs PVC vs StorageClass

| Resource | Responsibility |
|---|---|
| PV | Represents available persistent storage |
| PVC | Requests persistent storage |
| StorageClass | Defines dynamic provisioning behavior |

Official reference: [Kubernetes Storage Concepts](https://kubernetes.io/docs/concepts/storage/?utm_source=chatgpt.com)

---

# PART 10: KUBERNETES NETWORKING AND DNS

# 24. Kubernetes Networking Model

Kubernetes networking provides communication between:

- Containers inside a Pod.
- Different Pods.
- Services.
- Nodes.
- External clients.

Important networking concepts:

1. Pod IP.
2. Service IP.
3. Node IP.
4. Cluster DNS.
5. CNI Plugin.
6. NetworkPolicy.

## 24.1 Pod Networking

Each Pod receives its own IP address.

Containers inside the same Pod share the Pod's network namespace.

Example:

```text
Pod A: 10.244.0.10
Pod B: 10.244.0.11
Pod C: 10.244.0.12
```

Containers in Pod A can communicate using:

```text
localhost
```

because they share the Pod's network namespace.

## 24.2 CNI Plugin

CNI stands for Container Network Interface.

It defines an interface for configuring container network connectivity.

Kubernetes uses network plugins to provide Pod networking.

Examples:

- Amazon VPC CNI.
- Calico.
- Cilium.
- Flannel.

## 24.3 Kubernetes DNS

Kubernetes provides DNS-based service discovery.

Suppose you have:

```text
Service:
backend
```

inside:

```text
Namespace:
production
```

Other Pods in the same namespace can generally access it through:

```text
backend
```

A fully qualified service DNS name follows a form similar to:

```text
backend.production.svc.cluster.local
```

This allows services to communicate without manually tracking Pod IP addresses.

Example:

```text
Frontend Pod
     |
     v
backend.production.svc.cluster.local
     |
     v
Backend Service
     |
     v
Backend Pods
```

### Important

Service DNS names are stable, but individual Pod IP addresses can change when Pods are recreated.

---

# 25. NetworkPolicy

NetworkPolicy controls traffic between Pods and, depending on the implementation, traffic to or from external endpoints.

Example:

Suppose you have:

```text
Frontend Pods
Backend Pods
Database Pods
```

You want:

```text
Frontend -> Backend
Backend  -> Database
```

But not:

```text
Frontend -> Database
```

NetworkPolicies can enforce this type of segmentation.

Example concept:

```text
Frontend
    |
    v
Backend
    |
    v
Database
```

**Important:** NetworkPolicy resources require a networking implementation that enforces them. Creating a NetworkPolicy in a cluster without a supporting network plugin may have no effect.

Official reference: [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/?utm_source=chatgpt.com)

---

# PART 11: INGRESS AND GATEWAY API

# 26. What Is Ingress?

Ingress is a Kubernetes API resource that defines rules for HTTP and HTTPS traffic entering a cluster.

It commonly supports:

- Host-based routing.
- Path-based routing.
- TLS configuration.

Example:

```text
example.com/api/*      -> Backend Service
example.com/admin/*    -> Admin Service
example.com/*          -> Frontend Service
```

### Architecture

```text
                  Internet
                      |
                      v
                Ingress Controller
                      |
             +--------+--------+
             |                 |
             v                 v
        Frontend Service  Backend Service
             |                 |
             v                 v
          Frontend Pods     Backend Pods
```

## 26.1 Important: Ingress vs Ingress Controller

These are different.

**Ingress:** A Kubernetes API resource containing routing rules.

**Ingress Controller:** A controller that implements those rules by configuring an actual data plane.

Examples include:

- NGINX-based Ingress controllers.
- AWS Load Balancer Controller.
- Other supported ingress implementations.

Creating an Ingress resource without a suitable controller does not automatically expose your application.

## 26.2 Ingress YAML Example

The following is a simplified example for a cluster with a compatible Ingress controller.

```yaml
apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:
  name: app-ingress

spec:
  ingressClassName: nginx

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
                  number: 80

          - path: /api

            pathType: Prefix

            backend:
              service:
                name: backend-service

                port:
                  number: 80
```

### Path Types

| Path Type | Meaning |
|---|---|
| Exact | Exact path match |
| Prefix | Prefix-based path match |
| ImplementationSpecific | Behavior determined by the controller |

---

# 27. What Is Gateway API?

Gateway API is a newer Kubernetes networking API designed to provide more expressive traffic management than traditional Ingress.

It introduces resources such as:

- GatewayClass.
- Gateway.
- HTTPRoute.
- GRPCRoute.
- TCPRoute.
- TLSRoute.

The actual supported resources depend on the Gateway API version and implementation.

### Conceptual Architecture

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
          Frontend Service Backend Service
```

### Ingress vs Gateway API

| Feature | Ingress | Gateway API |
|---|---|---|
| HTTP routing | Yes | Yes |
| Host-based routing | Yes | Yes |
| Path-based routing | Yes | Yes |
| Advanced traffic management | Controller-dependent | More expressive API |
| Resource separation | Relatively simple | GatewayClass, Gateway and Routes |
| Extensibility | Limited compared with Gateway API | Designed for extensibility |

**Important current note:** The Kubernetes project has announced that the Ingress API is frozen. It remains supported, but new networking capabilities are being developed through Gateway API. For new projects, evaluate the Gateway API and the maturity of your chosen implementation. [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/?utm_source=chatgpt.com)

---

# PART 12: HEALTH CHECKS AND PROBES

# 28. Why Does Kubernetes Need Probes?

A container can be running even when its application is not functioning correctly.

Example:

```text
Container: Running
Application: Unresponsive
```

Kubernetes provides probes to evaluate application health.

There are three major probe types:

1. Liveness Probe.
2. Readiness Probe.
3. Startup Probe.

## 28.1 Liveness Probe

Checks whether the application is still functioning.

If the liveness probe repeatedly fails, Kubernetes restarts the affected container.

Example:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 3000

  initialDelaySeconds: 10
  periodSeconds: 10
```

## 28.2 Readiness Probe

Checks whether the application is ready to receive traffic.

If readiness fails, the Pod is generally removed from the ready endpoints of its Service until it becomes ready again.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 3000

  initialDelaySeconds: 5
  periodSeconds: 5
```

## 28.3 Startup Probe

Checks whether a slow-starting application has completed startup.

It prevents liveness and readiness probes from interfering with startup before the application has had sufficient time to initialize.

Example:

```yaml
startupProbe:
  httpGet:
    path: /health
    port: 3000

  periodSeconds: 5
  failureThreshold: 30
```

### Probe Comparison

| Probe | Purpose | Failure Behavior |
|---|---|---|
| Liveness | Is application functioning? | Container may be restarted |
| Readiness | Can application receive traffic? | Removed from ready Service endpoints |
| Startup | Has application finished starting? | Container may be restarted after threshold |

**Important:** Do not use the same expensive database-dependent check indiscriminately for every probe. Design endpoints around the purpose of each probe.

---

# PART 13: RESOURCE REQUESTS AND LIMITS

# 29. What Are Resource Requests?

Resource requests specify the amount of CPU and memory a container needs for scheduling.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
```

Meaning:

```text
CPU:    0.25 CPU
Memory: 256 MiB
```

The scheduler uses resource requests when deciding where to place Pods.

## 29.1 What Are Resource Limits?

Resource limits define maximum resource consumption.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

Meaning:

```text
Minimum scheduling request:
0.25 CPU
256 MiB memory

Maximum:
0.5 CPU
512 MiB memory
```

### CPU vs Memory Behavior

CPU limits are enforced through CPU throttling.

Memory limits can result in the container being terminated when it exceeds its allowed memory under applicable conditions, commonly resulting in an OOMKilled status.

### Best Practice

Set requests and limits according to measured application requirements rather than choosing arbitrary values.

---

# PART 14: AUTOSCALING

# 30. What Is Kubernetes Autoscaling?

Autoscaling adjusts workloads or infrastructure according to changing demand.

Kubernetes supports multiple autoscaling mechanisms.

Important examples:

1. Horizontal Pod Autoscaler.
2. Vertical Pod Autoscaler.
3. Cluster Autoscaler.
4. Node autoscaling solutions such as Karpenter.

## 30.1 Horizontal Pod Autoscaler (HPA)

HPA automatically adjusts the number of Pod replicas based on observed metrics.

Suppose:

```text
Minimum Replicas: 2
Maximum Replicas: 10
Target CPU: 70%
```

When demand increases, HPA can increase the replica count.

```text
Low Traffic:
Pod 1
Pod 2

High Traffic:
Pod 1
Pod 2
Pod 3
Pod 4
Pod 5
```

### HPA YAML Example

```yaml
apiVersion: autoscaling/v2

kind: HorizontalPodAutoscaler

metadata:
  name: backend-hpa

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend

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

This example assumes the relevant resource metrics API is available and that the target Deployment has CPU requests configured.

Check:

```bash
kubectl get hpa
```

## 30.2 Vertical Pod Autoscaler (VPA)

VPA helps adjust Pod resource requests and, depending on its configuration, limits.

It is useful when you need to understand or optimize application resource requirements.

## 30.3 Cluster Autoscaler

Cluster Autoscaler adjusts the number of nodes based on scheduling demand and supported infrastructure integration.

## 30.4 Karpenter

Karpenter is an open-source node provisioning and autoscaling system commonly used with Amazon EKS.

It can provision suitable compute capacity based on pending workload requirements and configured constraints.

### Autoscaling Comparison

| Mechanism | What It Scales |
|---|---|
| HPA | Number of Pod replicas |
| VPA | Pod resource requests and recommendations |
| Cluster Autoscaler | Number of cluster nodes |
| Karpenter | Node provisioning and consolidation |

**Important:** Pod autoscaling and node autoscaling solve different problems. HPA can create additional Pods, but those Pods still need sufficient node capacity.

---

# PART 15: STATEFULSETS, DAEMONSETS AND JOBS

# 31. What Is a StatefulSet?

A StatefulSet manages applications that need stable identities and persistent storage.

Common use cases:

- Databases.
- Distributed systems.
- Stateful services.
- Applications requiring stable network identities.

Example:

```text
StatefulSet: mysql
       |
       +-----------+
       |           |
       v           v
mysql-0          mysql-1
       |           |
       v           v
   Storage 0    Storage 1
```

Unlike ordinary Deployment Pods, StatefulSet Pods have stable ordinal identities.

Example:

```text
mysql-0
mysql-1
mysql-2
```

Each Pod can have its own persistent storage claim.

## 31.1 StatefulSet vs Deployment

| Feature | Deployment | StatefulSet |
|---|---|---|
| Main Use | Stateless workloads | Stateful workloads |
| Pod Identity | Generally interchangeable | Stable ordinal identity |
| Storage | Can use volumes | Commonly uses per-Pod persistent storage |
| Pod Naming | Generated names | Stable names |
| Scaling | Straightforward | Ordered lifecycle behavior by default |
| Examples | API servers | Databases and clustered systems |

**Important:** A StatefulSet does not automatically make an application database highly available. Database replication, backup and recovery must still be designed correctly.

---

# 32. What Is a DaemonSet?

A DaemonSet ensures that a Pod runs on each eligible node.

Typical uses:

- Log collectors.
- Monitoring agents.
- Security agents.
- Node-level networking components.

Example:

```text
Kubernetes Cluster
        |
   +----+----+
   |    |    |
   v    v    v
 Node A Node B Node C
   |    |    |
   v    v    v
 Agent Agent Agent
```

Example manifest:

```yaml
apiVersion: apps/v1

kind: DaemonSet

metadata:
  name: logging-agent

spec:
  selector:
    matchLabels:
      app: logging-agent

  template:
    metadata:
      labels:
        app: logging-agent

    spec:
      containers:
        - name: agent
          image: busybox:1.37

          command:
            - sh
            - -c
            - "while true; do sleep 3600; done"
```

This is only a learning example. A real logging agent would need the appropriate image, configuration, permissions and volume mounts.

---

# 33. What Is a Job?

A Job creates Pods that run until a specified task completes successfully.

Examples:

- Database migration.
- Data processing.
- Batch operation.
- One-time report generation.

Example:

```yaml
apiVersion: batch/v1

kind: Job

metadata:
  name: example-job

spec:
  template:
    spec:
      containers:
        - name: task
          image: busybox:1.37

          command:
            - sh
            - -c
            - "echo Hello Kubernetes"

      restartPolicy: Never

  backoffLimit: 3
```

Apply:

```bash
kubectl apply -f job.yaml
```

Check:

```bash
kubectl get jobs
```

---

# 34. What Is a CronJob?

A CronJob creates Jobs according to a schedule.

Example:

- Daily database cleanup.
- Hourly data synchronization.
- Weekly report generation.
- Scheduled backup operations.

Example:

```yaml
apiVersion: batch/v1

kind: CronJob

metadata:
  name: daily-job

spec:
  schedule: "0 2 * * *"

  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: task
              image: busybox:1.37

              command:
                - sh
                - -c
                - "echo Scheduled task executed"

          restartPolicy: Never
```

The schedule means:

```text
Every day at 02:00
```

Kubernetes CronJob schedules use cron expressions and the configured controller time zone behavior.

### Workload Resource Comparison

| Resource | Purpose |
|---|---|
| Pod | Runs containers |
| ReplicaSet | Maintains replicas |
| Deployment | Manages stateless application rollouts |
| StatefulSet | Manages stateful workloads |
| DaemonSet | Runs Pods on eligible nodes |
| Job | Runs tasks to completion |
| CronJob | Schedules recurring Jobs |

---

# PART 16: HELM

# 35. What Is Helm?

Helm is a package manager for Kubernetes.

It helps install, configure, upgrade and manage Kubernetes applications.

Without Helm, you might need to maintain many YAML files manually.

For example:

```text
my-application/
    deployment.yaml
    service.yaml
    configmap.yaml
    ingress.yaml
    hpa.yaml
```

Helm packages these resources into a reusable structure called a **Chart**.

### Helm Architecture

```text
               Helm
                |
                v
              Chart
                |
        +-------+-------+
        |       |       |
        v       v       v
   Deployment Service ConfigMap
        |       |       |
        +-------+-------+
                |
                v
          Kubernetes Cluster
```

## 35.1 Important Helm Concepts

| Concept | Meaning |
|---|---|
| Chart | Package containing Kubernetes templates and metadata |
| Release | Installed instance of a chart |
| Repository | Location where charts are stored |
| values.yaml | Default configuration values |
| Template | Parameterized Kubernetes manifest |
| Upgrade | Update an installed release |
| Rollback | Return to a previous release revision |

Official website:

[Helm Documentation](https://helm.sh/docs/?utm_source=chatgpt.com)

## 35.2 Install Helm

On Windows, you can use:

```powershell
winget install Helm.Helm
```

Verify:

```bash
helm version
```

## 35.3 Add a Helm Repository

Example:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update repositories:

```bash
helm repo update
```

## 35.4 Search Charts

```bash
helm search repo nginx
```

## 35.5 Install a Chart

For example, a suitable chart can be installed using:

```bash
helm install my-release bitnami/nginx
```

Check releases:

```bash
helm list
```

## 35.6 Upgrade Release

```bash
helm upgrade my-release bitnami/nginx
```

## 35.7 Rollback

```bash
helm rollback my-release 1
```

## 35.8 Uninstall

```bash
helm uninstall my-release
```

**Important:** Before installing a third-party chart in production, review its source, version, permissions, container images, resource settings and security configuration.

---

# PART 17: KUBERNETES MANIFESTS AND YAML

# 36. Understanding Kubernetes YAML

Kubernetes resources are commonly defined using YAML manifests.

A typical resource contains:

```yaml
apiVersion:
kind:
metadata:
spec:
```

### 36.1 apiVersion

Defines the API version used by the resource.

Example:

```yaml
apiVersion: apps/v1
```

### 36.2 kind

Defines the resource type.

Example:

```yaml
kind: Deployment
```

### 36.3 metadata

Contains resource information.

Example:

```yaml
metadata:
  name: backend
  labels:
    app: backend
```

### 36.4 spec

Defines the desired configuration.

Example:

```yaml
spec:
  replicas: 3
```

### 36.5 status

The Kubernetes system reports the observed state through the resource's status.

Conceptually:

```text
              Kubernetes Resource
                       |
             +---------+---------+
             |                   |
             v                   v
           spec                status
             |                   |
             v                   v
        Desired State       Actual State
```

Kubernetes controllers continuously work to reconcile actual state toward desired state.

---

# 37. Apply and Manage Manifests

Create resources:

```bash
kubectl apply -f deployment.yaml
```

Apply an entire directory:

```bash
kubectl apply -f ./k8s/
```

Delete resources defined in a file:

```bash
kubectl delete -f deployment.yaml
```

Validate YAML locally:

```bash
kubectl apply --dry-run=client -f deployment.yaml
```

Ask the API Server to validate without persisting:

```bash
kubectl apply --dry-run=server -f deployment.yaml
```

See the changes that would be applied:

```bash
kubectl diff -f deployment.yaml
```

**Best practice:** Keep Kubernetes manifests in version control. This allows infrastructure and deployment configuration to be reviewed and reproduced.

---

# PART 18: KUBERNETES COMMANDS

# 38. Important kubectl Commands

## Cluster Information

```bash
kubectl cluster-info
```

```bash
kubectl get nodes
```

```bash
kubectl get namespaces
```

## Pods

```bash
kubectl get pods
```

```bash
kubectl get pods -o wide
```

```bash
kubectl describe pod POD_NAME
```

## Deployments

```bash
kubectl get deployments
```

```bash
kubectl describe deployment DEPLOYMENT_NAME
```

```bash
kubectl scale deployment backend --replicas=5
```

## Services

```bash
kubectl get services
```

```bash
kubectl describe service SERVICE_NAME
```

## Logs

```bash
kubectl logs POD_NAME
```

```bash
kubectl logs -f POD_NAME
```

For a specific container in a multi-container Pod:

```bash
kubectl logs POD_NAME -c CONTAINER_NAME
```

## Execute Commands

```bash
kubectl exec -it POD_NAME -- sh
```

## Resource Information

```bash
kubectl get all
```

```bash
kubectl get events
```

```bash
kubectl top nodes
```

```bash
kubectl top pods
```

The `top` commands require a working metrics API, commonly provided by Metrics Server.

## Context Management

List contexts:

```bash
kubectl config get-contexts
```

Show current context:

```bash
kubectl config current-context
```

Switch context:

```bash
kubectl config use-context CONTEXT_NAME
```

### kubectl Quick Reference

| Command | Purpose |
|---|---|
| `kubectl get` | List resources |
| `kubectl describe` | Detailed resource information |
| `kubectl apply` | Create or update resources |
| `kubectl delete` | Delete resources |
| `kubectl logs` | View logs |
| `kubectl exec` | Execute commands in containers |
| `kubectl scale` | Change replica count |
| `kubectl rollout status` | Check deployment progress |
| `kubectl rollout history` | View deployment history |
| `kubectl rollout undo` | Roll back deployment |
| `kubectl top` | View resource metrics |
| `kubectl get events` | Inspect cluster events |

---

# PART 19: KUBERNETES SECURITY

# 39. Kubernetes Security Fundamentals

Kubernetes clusters require careful security configuration.

Important security areas include:

1. Authentication.
2. Authorization.
3. Network security.
4. Secret management.
5. Pod security.
6. Image security.
7. API Server security.
8. Cluster upgrades.

## 39.1 Authentication

Authentication verifies who is making a request.

Examples:

- Certificates.
- Cloud identity integration.
- Authentication plugins.
- External identity providers.

## 39.2 Authorization

Authorization determines what an authenticated identity is allowed to do.

Kubernetes commonly uses Role-Based Access Control (RBAC).

## 39.3 RBAC

RBAC stands for **Role-Based Access Control**.

Important resources:

- Role.
- ClusterRole.
- RoleBinding.
- ClusterRoleBinding.

### Role

Defines permissions within a namespace.

Example:

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

This Role allows the specified permissions on Pods in the `development` namespace.

### RoleBinding

Connects a Role to a user, group or ServiceAccount.

Example:

```yaml
apiVersion: rbac.authorization.k8s.io/v1

kind: RoleBinding

metadata:
  name: pod-reader-binding
  namespace: development

subjects:
  - kind: ServiceAccount
    name: developer
    namespace: development

roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Role vs ClusterRole

| Resource | Scope |
|---|---|
| Role | Namespace |
| ClusterRole | Cluster-wide or reusable permissions |
| RoleBinding | Binds permissions within a namespace |
| ClusterRoleBinding | Binds ClusterRole permissions cluster-wide |

## 39.4 ServiceAccounts

A ServiceAccount provides an identity for processes running inside Pods.

Example:

```yaml
apiVersion: v1

kind: ServiceAccount

metadata:
  name: backend-service-account
```

A Pod can use it:

```yaml
spec:
  serviceAccountName: backend-service-account
```

Use least-privilege permissions.

## 39.5 Pod Security

Important practices:

- Run containers as non-root.
- Drop unnecessary Linux capabilities.
- Avoid privileged containers.
- Use read-only root filesystems where possible.
- Restrict host namespace access.
- Set resource limits.
- Use appropriate Pod Security Standards.
- Scan images for vulnerabilities.

## 39.6 Network Security

Use NetworkPolicies to restrict unnecessary Pod-to-Pod communication.

For example:

```text
Frontend -> Backend
Backend  -> Database
```

Avoid unrestricted communication between all workloads.

## 39.7 Secret Security

- Restrict Secret access using RBAC.
- Enable encryption at rest.
- Rotate credentials.
- Avoid committing secrets to Git.
- Consider external secret-management solutions.

Official reference: [Kubernetes Security Concepts](https://kubernetes.io/docs/concepts/security/?utm_source=chatgpt.com)

---

# PART 20: MONITORING AND LOGGING

# 40. Why Do We Need Kubernetes Monitoring?

In production, you need visibility into:

- Cluster health.
- Node health.
- Pod health.
- CPU usage.
- Memory usage.
- Application errors.
- Network traffic.
- Deployment failures.

Monitoring helps identify issues before they become major incidents.

## 40.1 Metrics Server

Metrics Server collects resource usage metrics from Kubernetes nodes and Pods.

It supports basic resource metrics APIs used by commands such as:

```bash
kubectl top pods
```

It is also commonly used as a metrics source for CPU- and memory-based HPA configurations.

## 40.2 Prometheus

Prometheus is a monitoring and metrics collection system.

It can collect metrics from:

- Kubernetes components.
- Applications.
- Exporters.
- Infrastructure.

Website:

[Prometheus](https://prometheus.io/docs/introduction/overview/?utm_source=chatgpt.com)

## 40.3 Grafana

Grafana provides dashboards for visualizing metrics and other observability data.

Website:

[Grafana](https://grafana.com/docs/?utm_source=chatgpt.com)

## 40.4 Logging

Applications should produce structured logs.

Common logging solutions include:

- Fluent Bit.
- Fluentd.
- Elasticsearch.
- OpenSearch.
- Loki.
- CloudWatch.

### Monitoring Architecture

```text
             Kubernetes Cluster
                     |
          +----------+----------+
          |                     |
          v                     v
      Prometheus            Log Collector
          |                     |
          v                     v
        Grafana            Log Storage
          |                     |
          +----------+----------+
                     |
                     v
               Observability
```

---

# PART 21: TROUBLESHOOTING KUBERNETES

# 41. Common Kubernetes Problems

## 41.1 ImagePullBackOff

Meaning:

Kubernetes cannot successfully pull the container image.

Possible causes:

- Incorrect image name.
- Incorrect tag.
- Private registry authentication failure.
- Registry network problem.
- Image does not exist.

Troubleshooting:

```bash
kubectl describe pod POD_NAME
```

Check the Events section.

## 41.2 CrashLoopBackOff

Meaning:

The container repeatedly starts and exits.

Possible causes:

- Application crash.
- Incorrect startup command.
- Missing environment variables.
- Database connection failure.
- Incorrect configuration.

Check:

```bash
kubectl logs POD_NAME
```

If it restarted:

```bash
kubectl logs POD_NAME --previous
```

## 41.3 Pending Pod

Meaning:

The Pod has not reached the Running state.

Possible causes:

- Insufficient CPU.
- Insufficient memory.
- No suitable node.
- PVC not bound.
- Taints and tolerations mismatch.
- Scheduling constraints.

Check:

```bash
kubectl describe pod POD_NAME
```

## 41.4 Service Not Working

Possible causes:

- Incorrect selector.
- Incorrect port.
- No ready Pods.
- NetworkPolicy restrictions.
- CNI problem.

Check:

```bash
kubectl get services
```

```bash
kubectl get endpoints
```

For newer Kubernetes workflows, also inspect EndpointSlices:

```bash
kubectl get endpointslices
```

## 41.5 PVC Pending

Possible causes:

- No suitable PV.
- StorageClass missing.
- CSI driver problem.
- Storage capacity or topology constraints.

Check:

```bash
kubectl describe pvc PVC_NAME
```

## 41.6 Deployment Not Updating

Check:

```bash
kubectl rollout status deployment/backend
```

View history:

```bash
kubectl rollout history deployment/backend
```

Inspect Pods:

```bash
kubectl get pods
```

---

# PART 22: DEPLOYING NODE.JS APPLICATIONS ON KUBERNETES

# 42. Create a Node.js Application Deployment

You learned how to containerize Node.js applications in Phase 45.

Now deploy one to Kubernetes.

Assume you have pushed your image to a container registry:

```text
myusername/node-backend:1.0
```

## 42.1 Deployment YAML

Create:

```text
backend-deployment.yaml
```

Add:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: backend

spec:
  replicas: 3

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

          image: myusername/node-backend:1.0

          ports:
            - containerPort: 3000

          env:
            - name: PORT
              value: "3000"

          resources:
            requests:
              cpu: "250m"
              memory: "256Mi"

            limits:
              cpu: "500m"
              memory: "512Mi"

          readinessProbe:
            httpGet:
              path: /ready
              port: 3000

            initialDelaySeconds: 5
            periodSeconds: 5

          livenessProbe:
            httpGet:
              path: /health
              port: 3000

            initialDelaySeconds: 15
            periodSeconds: 10
```

This assumes the application implements `/ready` and `/health` endpoints.

Apply:

```bash
kubectl apply -f backend-deployment.yaml
```

Check:

```bash
kubectl get deployments
```

Check Pods:

```bash
kubectl get pods
```

## 42.2 Create Backend Service

Create:

```text
backend-service.yaml
```

Add:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: backend-service

spec:
  type: ClusterIP

  selector:
    app: backend

  ports:
    - port: 80
      targetPort: 3000
      protocol: TCP
```

Apply:

```bash
kubectl apply -f backend-service.yaml
```

Now other Pods can access the backend through:

```text
http://backend-service
```

If accessing from another namespace, use the appropriate fully qualified DNS name.

---

# PART 23: DEPLOYING NEXT.JS APPLICATIONS

# 43. Deploy Next.js Using Kubernetes

You learned how to create a production-oriented Next.js Docker image in Phase 45.

Now deploy that image to Kubernetes.

Assume your image is:

```text
myusername/nextjs-app:1.0
```

## 43.1 Next.js Deployment

Create:

```text
nextjs-deployment.yaml
```

Add:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: nextjs-frontend

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nextjs

  template:
    metadata:
      labels:
        app: nextjs

    spec:
      containers:
        - name: nextjs

          image: myusername/nextjs-app:1.0

          ports:
            - containerPort: 3000

          env:
            - name: NODE_ENV
              value: production

          resources:
            requests:
              cpu: "250m"
              memory: "256Mi"

            limits:
              cpu: "500m"
              memory: "512Mi"

          readinessProbe:
            httpGet:
              path: /
              port: 3000

            periodSeconds: 10

          livenessProbe:
            httpGet:
              path: /
              port: 3000

            periodSeconds: 20
```

Apply:

```bash
kubectl apply -f nextjs-deployment.yaml
```

## 43.2 Create Next.js Service

```yaml
apiVersion: v1

kind: Service

metadata:
  name: nextjs-service

spec:
  type: ClusterIP

  selector:
    app: nextjs

  ports:
    - port: 80
      targetPort: 3000
```

Apply:

```bash
kubectl apply -f nextjs-service.yaml
```

Now your Next.js application is accessible internally through:

```text
http://nextjs-service
```

For production, configure appropriate readiness and liveness endpoints rather than assuming every HTTP 200 response from `/` represents complete application health.

---

# PART 24: AMAZON EKS

# 44. What Is Amazon EKS?

Amazon EKS stands for **Amazon Elastic Kubernetes Service**.

It is AWS's managed Kubernetes service.

EKS allows you to run Kubernetes clusters without managing every control-plane component yourself.

Official documentation:

[Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)

## 44.1 Why Do We Need EKS?

You could install Kubernetes manually on EC2 instances.

However, managing a production control plane requires significant operational work.

EKS manages the Kubernetes control plane.

AWS handles responsibilities such as:

- Control-plane availability.
- API Server management.
- etcd management.
- Control-plane maintenance.
- Managed control-plane upgrades.

You remain responsible for much of the application and worker infrastructure configuration.

## 44.2 EKS Architecture

```text
                  AWS Region
                      |
                      v
                 Amazon EKS
                      |
             +--------+--------+
             |                 |
             v                 v
        EKS Control Plane   Worker Nodes
             |                 |
             |                 |
             v                 v
         API Server        EC2 Instances
             |                 |
             v                 v
            etcd              Pods
```

AWS manages the EKS control plane, while you select and configure suitable compute capacity for your workloads.

EKS supports several compute approaches:

- EKS Auto Mode.
- Managed Node Groups.
- Self-managed nodes.
- Fargate for supported workloads.
- Hybrid Nodes.

The operational responsibilities differ between these options. [AWS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/eks-architecture.html?utm_source=chatgpt.com)

---

# 45. EKS Managed Node Groups

A Managed Node Group is a group of EC2 instances managed through EKS.

AWS helps manage operations such as:

- Node provisioning.
- Node replacement.
- Managed scaling integration.
- Node group updates.

Example:

```text
              EKS Cluster
                   |
                   v
            Managed Node Group
                   |
          +--------+--------+
          |        |        |
          v        v        v
        EC2 A    EC2 B    EC2 C
          |        |        |
          v        v        v
         Pods     Pods     Pods
```

You still need to configure appropriate:

- Instance types.
- Scaling limits.
- IAM permissions.
- Network access.
- Kubernetes scheduling requirements.
- Upgrade strategy.

Official reference: [Create EKS Managed Node Groups](https://docs.aws.amazon.com/eks/latest/userguide/create-managed-node-group.html?utm_source=chatgpt.com)

---

# 46. EKS Auto Mode

EKS Auto Mode extends AWS management beyond the control plane.

It can automate aspects of:

- Compute provisioning.
- Networking.
- Load balancing.
- Storage.
- Node management.
- Scaling.

It is an important option to understand when evaluating modern EKS architectures.

However, it does not remove the need to understand Kubernetes workloads, resource requests, IAM, security and application configuration.

---

# 47. EKS Networking

Amazon EKS uses VPC networking.

The Amazon VPC CNI plugin provides Pod networking for nodes running on AWS infrastructure.

Conceptually:

```text
                  AWS VPC
                     |
              +------+------+
              |             |
              v             v
           Worker A      Worker B
              |             |
              v             v
            Pod A         Pod B
              |             |
              +------+------+
                     |
                 VPC Network
```

EKS networking considerations include:

- VPC CIDR capacity.
- Subnet IP availability.
- Pod IP allocation.
- Security Groups.
- Network policies.
- Load balancer integration.
- Private cluster access.
- NAT and VPC endpoint requirements.

The VPC CNI allocates Pod network addresses using AWS networking resources. Plan subnet capacity carefully, especially for clusters with many Pods. [AWS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/eks-networking.html?utm_source=chatgpt.com)

---

# 48. EKS with AWS Services

An EKS application may integrate with:

| AWS Service | Purpose |
|---|---|
| EC2 | Worker node compute |
| ECR | Container image registry |
| VPC | Network isolation |
| IAM | Identity and permissions |
| ALB/NLB | External traffic distribution |
| EBS | Persistent block storage |
| EFS | Shared file storage |
| Route 53 | DNS |
| CloudWatch | Logs and monitoring |
| Secrets Manager | Application secrets |
| S3 | Object storage |

### Example Architecture

```text
                       Internet
                          |
                          v
                       Route 53
                          |
                          v
                    AWS Load Balancer
                          |
                          v
                       Amazon EKS
                          |
               +----------+----------+
               |                     |
               v                     v
           Next.js Pods          Node.js Pods
                                     |
                            +--------+--------+
                            |                 |
                            v                 v
                         Redis            MySQL RDS
```

This architecture is one example; Redis and the database may be managed outside Kubernetes depending on operational requirements.

---

# PART 25: PRODUCTION KUBERNETES ARCHITECTURE

# 49. Full-Stack Application Architecture

Let's design a production-style system using:

- Next.js.
- Node.js.
- Express.
- MySQL.
- Redis.
- Kubernetes.
- AWS EKS.
- ECR.
- ALB.
- Route 53.
- CloudWatch.

### Architecture

```text
                         INTERNET
                             |
                             v
                         Route 53
                             |
                             v
                           AWS WAF
                             |
                             v
                    Application Load Balancer
                             |
                             v
                        EKS Cluster
                             |
               +-------------+-------------+
               |                           |
               v                           v
          Next.js Service             Backend Service
               |                           |
         +-----+-----+               +-----+-----+
         |     |     |               |     |     |
         v     v     v               v     v     v
       Pod   Pod   Pod             Pod   Pod   Pod
                                     |
                           +---------+---------+
                           |                   |
                           v                   v
                         Redis              MySQL RDS
```

### CI/CD Architecture

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
Build Docker Images
    |
    v
Security Scanning
    |
    v
Amazon ECR
    |
    v
Deploy to EKS
    |
    v
Kubernetes Deployment
    |
    v
Running Pods
```

### Production Best Practices

1. Deploy critical workloads across multiple Availability Zones.
2. Use managed control planes where appropriate.
3. Use separate namespaces for environments or teams where appropriate.
4. Set resource requests and limits.
5. Configure readiness, liveness and startup probes.
6. Use HPA where workload metrics justify it.
7. Configure node autoscaling.
8. Use RBAC with least privilege.
9. Configure NetworkPolicies.
10. Secure Secrets.
11. Use private worker subnets where appropriate.
12. Use private container registries.
13. Scan container images.
14. Monitor application and cluster health.
15. Maintain tested backup and recovery procedures.
16. Plan cluster and node upgrades.
17. Use version-controlled manifests or Helm charts.
18. Implement deployment rollback procedures.
19. Monitor cloud infrastructure costs.

AWS publishes an EKS Best Practices Guide covering security, networking, reliability, scalability, upgrades and cost optimization. [AWS Documentation](https://docs.aws.amazon.com/eks/latest/best-practices/?utm_source=chatgpt.com)

---

# PART 26: COMMON KUBERNETES MISTAKES

# 50. Mistakes to Avoid

| Mistake | Problem | Better Approach |
|---|---|---|
| Deploying Pods directly for production workloads | No higher-level rollout management | Use Deployments or suitable controllers |
| Not setting resource requests | Unpredictable scheduling | Define CPU and memory requests |
| No resource limits | Workloads may consume excessive resources | Set appropriate limits |
| Storing passwords in ConfigMaps | Sensitive data is exposed as ordinary configuration | Use Secrets or external secret systems |
| Assuming Secrets are encrypted automatically | Base64 is not encryption | Configure encryption at rest and RBAC |
| Using NodePort for every application | Exposes unnecessary node ports | Prefer suitable Service and Gateway/Ingress designs |
| No readiness probes | Traffic may reach unready Pods | Configure readiness probes |
| Using incorrect selectors | Services may have no endpoints | Match Service selectors to Pod labels |
| Using `latest` image tags | Unpredictable deployments | Use immutable tags or digests |
| Ignoring Pod logs | Difficult troubleshooting | Inspect logs and events |
| No persistent storage for databases | Data loss risk | Use suitable persistent storage |
| No NetworkPolicies | Excessive Pod-to-Pod connectivity | Apply network segmentation |
| Overprivileged ServiceAccounts | Excessive permissions | Apply least-privilege RBAC |
| No cluster upgrade plan | Unsupported or vulnerable cluster versions | Maintain an upgrade strategy |

---

# PART 27: INTERVIEW QUESTIONS AND ANSWERS

# 51. Basic Interview Questions

### Q1. What is Kubernetes?

Kubernetes is an open-source container orchestration platform used to deploy, scale and manage containerized applications.

### Q2. What is a Kubernetes cluster?

A Kubernetes cluster consists of a Control Plane and one or more Worker Nodes.

### Q3. What is a Pod?

A Pod is the smallest deployable compute object in Kubernetes and contains one or more containers.

### Q4. What is a Node?

A Node is a worker machine that runs Pods.

### Q5. What is the Control Plane?

The Control Plane manages the Kubernetes cluster and maintains its desired state.

### Q6. What is kubectl?

kubectl is the command-line tool used to communicate with the Kubernetes API Server.

### Q7. What is a Deployment?

A Deployment manages stateless application workloads through ReplicaSets and supports rolling updates and rollbacks.

### Q8. What is a ReplicaSet?

A ReplicaSet maintains a specified number of identical Pod replicas.

### Q9. What is a Service?

A Service provides a stable network endpoint for accessing a group of Pods.

### Q10. What is a Namespace?

A Namespace logically groups Kubernetes resources within a cluster.

---

# 52. Intermediate Interview Questions

### Q11. What is the difference between a Pod and a container?

A container runs an application process.

A Pod is the Kubernetes object that groups one or more containers and provides shared networking and storage configuration.

### Q12. What is the difference between Deployment and ReplicaSet?

A ReplicaSet maintains Pod replicas.

A Deployment manages ReplicaSets and provides declarative rollout and rollback capabilities.

### Q13. What is the difference between ClusterIP and NodePort?

ClusterIP provides internal cluster access.

NodePort exposes a Service through a port on each node.

### Q14. What is a LoadBalancer Service?

It requests external load balancer integration through a supported cloud or infrastructure provider.

### Q15. What is a ConfigMap?

A ConfigMap stores non-sensitive application configuration.

### Q16. What is a Secret?

A Secret stores sensitive configuration data such as passwords and tokens, subject to appropriate access and encryption controls.

### Q17. What is a Persistent Volume?

A Persistent Volume represents storage available to the Kubernetes cluster.

### Q18. What is a Persistent Volume Claim?

A PVC is a request for storage made by a workload.

### Q19. What is a StorageClass?

A StorageClass defines a storage provisioning class that can dynamically create persistent storage.

### Q20. What is Ingress?

Ingress is a Kubernetes API resource that defines HTTP and HTTPS routing rules into a cluster.

---

# 53. Advanced Interview Questions

### Q21. What happens when a Pod crashes?

If the Pod is managed by a Deployment or ReplicaSet, the controller observes the difference between the desired and actual replica counts and works to create a replacement.

### Q22. What happens when a Worker Node fails?

The Control Plane detects the node's unhealthy state.

Workloads may be rescheduled or recreated on suitable healthy nodes, subject to workload configuration, storage constraints and Kubernetes eviction behavior.

### Q23. What is the role of the Scheduler?

The Scheduler assigns unscheduled Pods to suitable worker nodes based on resource requirements and scheduling constraints.

### Q24. What is etcd?

etcd is the distributed key-value store used by Kubernetes to store cluster state.

### Q25. What is the difference between Liveness and Readiness probes?

Liveness checks whether the application should continue running.

Readiness checks whether the application should receive traffic.

### Q26. What is HPA?

Horizontal Pod Autoscaler automatically adjusts the number of Pod replicas based on observed metrics.

### Q27. What is StatefulSet?

StatefulSet manages stateful workloads requiring stable identities and commonly persistent storage.

### Q28. What is DaemonSet?

DaemonSet ensures that a Pod runs on each eligible node.

### Q29. What is Helm?

Helm is a package manager for Kubernetes that manages applications using charts and releases.

### Q30. What is Amazon EKS?

Amazon EKS is AWS's managed Kubernetes service.

---

# 54. Scenario-Based Interview Questions

### Q31. A Pod is in CrashLoopBackOff. How would you troubleshoot it?

I would:

1. Run `kubectl describe pod`.
2. Inspect container logs.
3. Check previous container logs.
4. Verify startup commands.
5. Check environment variables.
6. Verify database connectivity.
7. Inspect resource limits.
8. Check liveness and startup probes.

### Q32. A Service is not routing traffic to Pods. What would you check?

I would:

1. Check Service selectors.
2. Check Pod labels.
3. Verify Service port and targetPort.
4. Check Pod readiness.
5. Inspect EndpointSlices.
6. Check NetworkPolicies.
7. Test connectivity inside the cluster.

### Q33. How would you deploy a highly available Node.js application?

I would:

1. Create a Deployment with multiple replicas.
2. Distribute Pods across suitable nodes and Availability Zones.
3. Create a ClusterIP Service.
4. Expose it using an appropriate Gateway, Ingress or LoadBalancer implementation.
5. Configure readiness and liveness probes.
6. Set resource requests and limits.
7. Configure autoscaling.
8. Monitor logs and metrics.

### Q34. How would you store database credentials securely?

I would use Kubernetes Secrets with appropriate RBAC and encryption at rest, or integrate an external secret-management service such as AWS Secrets Manager.

### Q35. What happens if a container image does not exist?

The Pod may enter `ErrImagePull` and then `ImagePullBackOff` states.

I would check the image name, tag, registry permissions and network connectivity.

### Q36. What is the difference between HPA and Cluster Autoscaler?

HPA changes the number of application Pods.

Cluster Autoscaler changes the number of worker nodes when scheduling requirements and infrastructure conditions justify it.

### Q37. How would you deploy a new application version without downtime?

I would use a Deployment with a suitable rolling update strategy, configure readiness probes and monitor rollout status.

I would also maintain a rollback strategy.

### Q38. Why should databases not always run as Deployments?

Databases often require stable identities, persistent storage and ordered lifecycle management. StatefulSets may be more suitable for some database architectures, although managed database services are often preferable operationally.

---

# PART 28: PRACTICAL ASSIGNMENTS

# 55. Assignment 1 – Install Kubernetes Locally

Install:

- Docker Desktop.
- Minikube.
- kubectl.

Run:

```bash
minikube start --driver=docker
```

Verify:

```bash
kubectl get nodes
```

**Expected outcome:** A working local Kubernetes cluster.

---

# 56. Assignment 2 – Create Pods

Create:

- Nginx Pod.
- BusyBox Pod.
- Node.js Pod.

Practise:

- Applying manifests.
- Viewing logs.
- Describing Pods.
- Executing shell commands.
- Deleting Pods.

**Expected outcome:** Understand Pod lifecycle management.

---

# 57. Assignment 3 – Deployments and ReplicaSets

Create a Deployment with:

```text
Replicas: 3
Image: nginx
```

Practise:

- Scaling.
- Image updates.
- Rollout status.
- Rollout history.
- Rollback.

**Expected outcome:** Understand application deployment management.

---

# 58. Assignment 4 – Services

Create:

- ClusterIP Service.
- NodePort Service.
- LoadBalancer Service.

Test how each Service type exposes the application.

**Expected outcome:** Understand Kubernetes networking and traffic exposure.

---

# 59. Assignment 5 – Namespaces

Create:

```text
development
staging
production
```

Deploy separate applications into each namespace.

**Expected outcome:** Understand environment organization.

---

# 60. Assignment 6 – ConfigMaps and Secrets

Create a ConfigMap containing:

```text
APP_ENV
LOG_LEVEL
API_URL
```

Create a Secret containing:

```text
DB_USER
DB_PASSWORD
```

Inject them into a Node.js Pod.

**Expected outcome:** Understand application configuration management.

---

# 61. Assignment 7 – Persistent Storage

Create:

- Persistent Volume.
- Persistent Volume Claim.
- StorageClass, where supported.

Deploy a database workload using persistent storage.

Test data persistence after Pod recreation.

**Expected outcome:** Understand Kubernetes storage.

---

# 62. Assignment 8 – Health Checks

Add:

- Liveness Probe.
- Readiness Probe.
- Startup Probe.

Test the behavior by making the health endpoint fail.

**Expected outcome:** Understand workload health management.

---

# 63. Assignment 9 – Autoscaling

Configure:

```text
Minimum Replicas: 2
Maximum Replicas: 10
Target CPU: 70%
```

Install or enable the required metrics provider.

Generate application load and observe scaling.

**Expected outcome:** Understand HPA.

---

# 64. Assignment 10 – Helm

Install Helm.

Add a chart repository.

Install a chart.

Practise:

- Upgrade.
- Rollback.
- Uninstall.

**Expected outcome:** Understand Kubernetes package management.

---

# 65. Assignment 11 – Deploy Node.js and Next.js

Deploy:

```text
Next.js Frontend
Node.js Backend
```

Requirements:

- Separate Deployments.
- Separate Services.
- ConfigMaps.
- Secrets.
- Health checks.
- Resource requests and limits.

**Expected outcome:** Deploy a multi-service full-stack application.

---

# 66. Assignment 12 – Complete Kubernetes Project

Build:

```text
Next.js
Node.js
Express
MySQL
Redis
Nginx / Gateway
```

Use Kubernetes to manage the application.

Your project should include:

- Deployments.
- Services.
- ConfigMaps.
- Secrets.
- Persistent Storage.
- Probes.
- Resource Limits.
- HPA.
- Ingress or Gateway API.
- Namespaces.
- RBAC.

**Expected outcome:** Build a production-style Kubernetes application architecture.

---

# PART 29: QUICK REVISION

# 67. Kubernetes Concepts at a Glance

| Concept | Meaning |
|---|---|
| Kubernetes | Container orchestration platform |
| Cluster | Control Plane and Worker Nodes |
| Control Plane | Manages cluster state |
| Worker Node | Runs application workloads |
| API Server | Kubernetes API entry point |
| etcd | Cluster state storage |
| Scheduler | Assigns Pods to nodes |
| Controller Manager | Runs reconciliation controllers |
| Kubelet | Node agent |
| Kube-proxy | Service networking component, where used |
| CNI | Container network integration |
| Pod | Smallest deployable compute object |
| ReplicaSet | Maintains Pod replicas |
| Deployment | Manages stateless application rollouts |
| Service | Stable endpoint for Pods |
| ClusterIP | Internal Service access |
| NodePort | Service access through node ports |
| LoadBalancer | External load balancer integration |
| Namespace | Logical resource grouping |
| ConfigMap | Non-sensitive configuration |
| Secret | Sensitive configuration |
| PV | Persistent storage resource |
| PVC | Storage request |
| StorageClass | Storage provisioning configuration |
| Ingress | HTTP/HTTPS routing rules |
| Gateway API | Advanced Kubernetes traffic management API |
| NetworkPolicy | Controls network traffic |
| HPA | Scales Pod replicas |
| VPA | Adjusts Pod resource recommendations/requests |
| StatefulSet | Manages stateful workloads |
| DaemonSet | Runs Pods on eligible nodes |
| Job | Runs a task to completion |
| CronJob | Schedules recurring Jobs |
| Helm | Kubernetes package manager |
| EKS | AWS managed Kubernetes service |

---

# 68. Important kubectl Commands

| Command | Purpose |
|---|---|
| `kubectl cluster-info` | Cluster information |
| `kubectl get nodes` | List nodes |
| `kubectl get pods` | List Pods |
| `kubectl get deployments` | List Deployments |
| `kubectl get services` | List Services |
| `kubectl get namespaces` | List namespaces |
| `kubectl get all` | List common resource types |
| `kubectl describe pod` | Pod details |
| `kubectl logs` | View logs |
| `kubectl exec` | Execute commands |
| `kubectl apply -f` | Apply manifest |
| `kubectl delete -f` | Delete manifest resources |
| `kubectl scale` | Scale workload |
| `kubectl rollout status` | Check rollout |
| `kubectl rollout undo` | Rollback Deployment |
| `kubectl get events` | View events |
| `kubectl top pods` | Pod metrics |
| `kubectl top nodes` | Node metrics |
| `kubectl config get-contexts` | List contexts |
| `kubectl config use-context` | Switch cluster context |

---

# 69. Phase 46 Completion Checklist

### Kubernetes Fundamentals

- [ ] Kubernetes architecture.
- [ ] Docker vs Kubernetes.
- [ ] Control Plane.
- [ ] Worker Nodes.
- [ ] API Server.
- [ ] etcd.
- [ ] Scheduler.
- [ ] Controller Manager.
- [ ] Kubelet.
- [ ] Kube-proxy.
- [ ] Container Runtime.

### Workloads

- [ ] Pods.
- [ ] ReplicaSets.
- [ ] Deployments.
- [ ] StatefulSets.
- [ ] DaemonSets.
- [ ] Jobs.
- [ ] CronJobs.

### Networking

- [ ] ClusterIP.
- [ ] NodePort.
- [ ] LoadBalancer.
- [ ] Pod Networking.
- [ ] Kubernetes DNS.
- [ ] CNI.
- [ ] NetworkPolicy.
- [ ] Ingress.
- [ ] Gateway API.

### Configuration and Storage

- [ ] Namespaces.
- [ ] ConfigMaps.
- [ ] Secrets.
- [ ] Persistent Volumes.
- [ ] Persistent Volume Claims.
- [ ] Storage Classes.

### Scaling and Monitoring

- [ ] Liveness Probe.
- [ ] Readiness Probe.
- [ ] Startup Probe.
- [ ] Resource Requests.
- [ ] Resource Limits.
- [ ] HPA.
- [ ] VPA.
- [ ] Cluster Autoscaler.
- [ ] Prometheus.
- [ ] Grafana.
- [ ] Logging.

### Production and AWS

- [ ] Kubernetes Security.
- [ ] RBAC.
- [ ] ServiceAccounts.
- [ ] Helm.
- [ ] Amazon EKS.
- [ ] EKS Networking.
- [ ] ECR.
- [ ] AWS Load Balancer Integration.
- [ ] CloudWatch.
- [ ] Production Deployment.

---

# 70. Official Documentation and Learning Resources

| Topic | Documentation |
|---|---|
| Kubernetes Overview | [Kubernetes Documentation](https://kubernetes.io/docs/home/?utm_source=chatgpt.com) |
| Kubernetes Concepts | [Kubernetes Concepts](https://kubernetes.io/docs/concepts/index.html?utm_source=chatgpt.com) |
| Cluster Architecture | [Kubernetes Architecture](https://kubernetes.io/docs/concepts/architecture/?utm_source=chatgpt.com) |
| Kubernetes Components | [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/?utm_source=chatgpt.com) |
| Pods | [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/?utm_source=chatgpt.com) |
| Deployments | [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com) |
| Services | [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/?utm_source=chatgpt.com) |
| Storage | [Kubernetes Storage](https://kubernetes.io/docs/concepts/storage/?utm_source=chatgpt.com) |
| Networking | [Kubernetes Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/?utm_source=chatgpt.com) |
| Security | [Kubernetes Security](https://kubernetes.io/docs/concepts/security/?utm_source=chatgpt.com) |
| kubectl | [kubectl Documentation](https://kubernetes.io/docs/reference/kubectl/?utm_source=chatgpt.com) |
| Helm | [Helm Documentation](https://helm.sh/docs/?utm_source=chatgpt.com) |
| Minikube | [Minikube Documentation](https://minikube.sigs.k8s.io/docs/?utm_source=chatgpt.com) |
| Amazon EKS | [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/eks-architecture.html?utm_source=chatgpt.com) |
| EKS Best Practices | [AWS EKS Best Practices](https://docs.aws.amazon.com/eks/latest/best-practices/?utm_source=chatgpt.com) |
| EKS Networking | [AWS EKS Networking](https://docs.aws.amazon.com/eks/latest/userguide/eks-networking.html?utm_source=chatgpt.com) |
| EKS Security | [AWS EKS Security](https://docs.aws.amazon.com/eks/latest/best-practices/security.html?utm_source=chatgpt.com) |

---

# 71. Final Summary

In Phase 46, you learned how Kubernetes manages containerized applications across multiple machines.

The most important concept is:

**Kubernetes continuously works to maintain the desired state of your applications.**

For example, if you request three replicas:

```yaml
spec:
  replicas: 3
```

Kubernetes attempts to maintain three available application instances through its workload controllers.

### Complete Kubernetes Workflow

```text
             Developer
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
            Image Registry
                 |
                 v
          Kubernetes Cluster
                 |
                 v
              Deployment
                 |
                 v
              ReplicaSet
                 |
          +------+------+ 
          |      |      |
          v      v      v
        Pod 1  Pod 2  Pod 3
          |      |      |
          +------+------+
                 |
                 v
               Service
                 |
                 v
          External Traffic
```

You should now understand:

- How Kubernetes works.
- How its Control Plane manages a cluster.
- How Pods run containers.
- How Deployments manage application replicas.
- How Services provide stable networking.
- How ConfigMaps and Secrets manage configuration.
- How persistent storage works.
- How Ingress and Gateway API manage traffic.
- How probes monitor application health.
- How HPA scales workloads.
- How Helm manages Kubernetes applications.
- How EKS integrates Kubernetes with AWS.

### Most Important Topics to Master

Prioritize these concepts:

1. Kubernetes architecture.
2. Pods.
3. Deployments and ReplicaSets.
4. Services.
5. ConfigMaps and Secrets.
6. Persistent Volumes and PVCs.
7. Ingress and Gateway API.
8. Health checks.
9. Resource requests and limits.
10. HPA.
11. kubectl commands.
12. Amazon EKS.

---

# Next Phase Preview

## Phase 47 – CI/CD Pipelines, GitHub Actions and DevOps Automation

The next phase will cover:

1. What Is CI/CD?
2. Continuous Integration.
3. Continuous Delivery.
4. Continuous Deployment.
5. DevOps Lifecycle.
6. CI/CD Pipeline Architecture.
7. GitHub Actions.
8. GitHub Actions Workflows.
9. Workflow YAML.
10. Events and Triggers.
11. Jobs and Steps.
12. Runners.
13. Actions.
14. Environment Variables.
15. GitHub Secrets.
16. Workflow Artifacts.
17. Job Dependencies.
18. Matrix Builds.
19. Conditional Execution.
20. Caching Dependencies.
21. Automated Testing.
22. Linting and Code Quality.
23. Docker Image Build Pipelines.
24. Docker Hub Integration.
25. Amazon ECR Integration.
26. Automated Deployment to VPS.
27. Automated Deployment to AWS EC2.
28. Kubernetes Deployment Automation.
29. GitOps Fundamentals.
30. Argo CD.
31. Blue-Green Deployment.
32. Canary Deployment.
33. Rolling Deployment.
34. Pipeline Security.
35. CI/CD Monitoring.
36. Complete Full-Stack CI/CD Project.

**Phase 47 will connect GitHub, Docker, Kubernetes and cloud infrastructure into an automated software delivery workflow.**