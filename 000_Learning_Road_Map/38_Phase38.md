# Phase 38 – Cloud Computing, AWS Architecture and Production Infrastructure

**Level:** Advanced  
**Domain:** Cloud Computing, AWS, Infrastructure Architecture, DevOps  
**Primary Cloud Provider:** Amazon Web Services (AWS)  
**Infrastructure as Code:** Terraform  
**Application Stack:** Next.js 16, TypeScript, Node.js, Express, MongoDB, MySQL, Redis, Docker

---

# 1. Learning Objectives

By completing this phase, you should understand how to:

1. Understand cloud computing and its service models.
2. Design infrastructure using AWS services.
3. Configure AWS IAM users, roles and policies.
4. Deploy applications on EC2.
5. Design secure VPC networks and subnets.
6. Configure load balancers and auto-scaling.
7. Store files using Amazon S3.
8. Deploy managed databases using RDS.
9. Connect MongoDB Atlas to AWS infrastructure.
10. Use Redis through Amazon ElastiCache.
11. Configure DNS using Route 53.
12. Deliver content through CloudFront.
13. Build serverless applications using Lambda.
14. Monitor infrastructure using CloudWatch.
15. Manage application secrets securely.
16. Automate infrastructure using Terraform.
17. Design a production-ready AWS architecture.

---

# 2. Cloud Computing Fundamentals

## 2.1 What Is Cloud Computing?

Cloud computing is the delivery of computing resources over the internet.

Instead of purchasing and maintaining physical servers, you rent computing resources from a cloud provider.

These resources include:

- Virtual machines
- Storage
- Databases
- Networking
- Load balancers
- Application hosting
- Monitoring systems
- Security services
- AI and machine learning services

### Traditional Infrastructure

Suppose you want to launch an e-commerce application.

You need:

1. A physical server.
2. RAM and CPU.
3. Storage devices.
4. Network equipment.
5. A static IP address.
6. Electricity and cooling.
7. Server maintenance.
8. Hardware replacement when something fails.

You must purchase and maintain all this infrastructure.

### Cloud Infrastructure

With AWS, you can rent a virtual server, configure storage, create databases and deploy applications.

You pay according to the services and resources you consume.

For example:

| Requirement | AWS Service |
|---|---|
| Virtual server | EC2 |
| File storage | S3 |
| Relational database | RDS |
| Redis cache | ElastiCache |
| DNS | Route 53 |
| CDN | CloudFront |
| Load balancing | Elastic Load Balancing |
| Serverless functions | Lambda |
| Monitoring | CloudWatch |
| Access management | IAM |

## 2.2 Important Cloud Computing Characteristics

### 1. On-Demand Self-Service

You can provision resources whenever you need them.

For example, you can create an EC2 instance through the AWS Console without contacting a hardware provider.

### 2. Broad Network Access

Cloud services are accessible over networks through standard mechanisms such as HTTP, HTTPS and APIs.

### 3. Resource Pooling

Cloud providers maintain large infrastructure pools and allocate resources to customers.

### 4. Rapid Elasticity

Resources can scale according to application demand.

For example:

- Normal traffic: 2 EC2 instances.
- High traffic: 8 EC2 instances.
- Traffic decreases: scale back to 2 instances.

### 5. Measured Service

Cloud providers measure resource consumption.

Examples include:

- Compute hours
- Storage consumption
- Data transfer
- Database capacity
- Requests

This supports usage-based billing.

## 2.3 Scalability vs Elasticity

| Scalability | Elasticity |
|---|---|
| Ability to handle increased workload | Ability to automatically adjust resources according to demand |
| Can involve manual changes | Often automated |
| Focuses on capacity growth | Focuses on matching capacity to demand |

**Example:**

You manually increase an EC2 instance from 2 CPUs to 8 CPUs. This is vertical scaling.

You configure an Auto Scaling Group to increase the number of EC2 instances when traffic rises. This is elastic horizontal scaling.

---

# 3. Cloud Service Models: IaaS, PaaS and SaaS

Cloud providers offer different levels of infrastructure management.

## 3.1 IaaS – Infrastructure as a Service

IaaS provides fundamental computing infrastructure.

You manage:

- Operating system
- Runtime
- Application
- Application dependencies
- Application configuration

The provider manages the physical infrastructure.

**Examples:**
- Amazon EC2
- Azure Virtual Machines
- Google Compute Engine

Example:

You create an EC2 Ubuntu instance and install:

```bash
sudo apt update

sudo apt install nginx

sudo apt install nodejs npm

sudo npm install -g pm2
```

You are responsible for configuring and maintaining the server environment.

## 3.2 PaaS – Platform as a Service

PaaS provides a managed application platform.

You generally focus on application code and configuration instead of managing the underlying operating system.

Examples:

- Heroku
- Render
- Google App Engine
- AWS Elastic Beanstalk

For example, you push your Node.js application to Render, configure environment variables and deploy it.

The platform handles much of the infrastructure management.

## 3.3 SaaS – Software as a Service

SaaS provides complete software applications over the internet.

You simply use the software.

Examples:

- Gmail
- Google Drive
- Microsoft 365
- Slack

You do not manage the application's infrastructure.

## 3.4 IaaS vs PaaS vs SaaS

| Feature | IaaS | PaaS | SaaS |
|---|---|---|---|
| Infrastructure management | Provider | Provider | Provider |
| OS management | Customer | Provider | Provider |
| Runtime management | Customer | Usually provider | Provider |
| Application management | Customer | Customer | Provider |
| User customization | High | Medium | Limited |
| Example | EC2 | Render | Gmail |

### Interview Question

**Q. Is AWS EC2 IaaS or PaaS?**

EC2 is primarily an IaaS service because it provides virtual computing infrastructure while giving customers control over the operating system and software stack.

---

# 4. Public, Private and Hybrid Cloud

## 4.1 Public Cloud

Public cloud infrastructure is operated by a cloud provider and shared across multiple customers through logically isolated environments.

Examples:

- AWS
- Microsoft Azure
- Google Cloud

Advantages:

- No physical infrastructure required.
- Flexible resource provisioning.
- Global availability.
- Usage-based pricing.

## 4.2 Private Cloud

Private cloud infrastructure is dedicated to a single organization.

It can be hosted in the organization's data center or through a third-party provider.

Advantages:

- Greater infrastructure control.
- Custom security requirements.
- Dedicated environment.

Disadvantages:

- Higher operational responsibility.
- Infrastructure management complexity.
- Potentially higher cost.

## 4.3 Hybrid Cloud

Hybrid cloud combines public cloud infrastructure with private infrastructure.

Example:

A financial organization may keep certain sensitive systems in a private data center while using AWS for application hosting and scalable computing.

| Type | Infrastructure |
|---|---|
| Public | Shared provider infrastructure with logical isolation |
| Private | Dedicated organizational environment |
| Hybrid | Combination of public and private infrastructure |

---

# 5. AWS Global Infrastructure

AWS is a cloud computing platform operated by Amazon.

It provides services for computing, storage, networking, databases, analytics, security and more.

Understanding AWS global infrastructure is important when designing highly available applications.

## 5.1 AWS Regions

A Region is a geographical area containing multiple isolated Availability Zones.

Examples of AWS Regions include:

- Mumbai – `ap-south-1`
- Hyderabad – `ap-south-2`
- Singapore – `ap-southeast-1`
- Tokyo – `ap-northeast-1`
- US East (N. Virginia) – `us-east-1`

For an application primarily serving users in India, Mumbai may be a suitable Region to evaluate.

However, your choice should also consider:

- Data residency requirements.
- Service availability.
- Pricing.
- Latency.
- Disaster recovery requirements.

## 5.2 Availability Zones (AZs)

An Availability Zone is an isolated location within an AWS Region.

A Region typically contains multiple Availability Zones.

Each AZ has independent infrastructure designed to reduce the impact of failures in other AZs.

Example:

```text
AWS Region: Mumbai (ap-south-1)
│
├── Availability Zone A
│   ├── EC2 Instance
│   ├── Application Server
│   └── Database Resources
│
├── Availability Zone B
│   ├── EC2 Instance
│   ├── Application Server
│   └── Database Resources
│
└── Availability Zone C
    ├── EC2 Instance
    ├── Application Server
    └── Database Resources
```

If one AZ experiences an outage, resources in another AZ may continue serving traffic.

## 5.3 Edge Locations

Edge locations are infrastructure locations used by services such as CloudFront to deliver content closer to users.

For example:

A user in Ahmedabad requests an image from your website.

Instead of fetching that image from your origin server every time, CloudFront may serve a cached copy from a nearby edge location.

This reduces latency and origin-server load.

## 5.4 Region vs Availability Zone vs Edge Location

| Concept | Meaning | Main Purpose |
|---|---|---|
| Region | Geographical AWS area | Regional deployment |
| Availability Zone | Isolated location inside a Region | High availability |
| Edge Location | Content delivery infrastructure | Low-latency content delivery |

---

# 6. AWS IAM – Identity and Access Management

IAM stands for **Identity and Access Management**.

It controls who can access AWS resources and what actions they can perform.

For example:

You have an AWS account containing:

- EC2 instances
- S3 buckets
- RDS databases
- Lambda functions

You want:

- Developer A to access EC2.
- Developer B to access S3.
- Application servers to access specific S3 buckets.
- Administrators to manage infrastructure.

IAM allows you to define these permissions.

## 6.1 IAM Users

An IAM User represents an identity in AWS.

A user can have:

- Console access.
- Programmatic access.
- Permissions through attached policies.

For example:

```text
IAM User: developer-01

Permissions:
- Read EC2 information
- View CloudWatch logs
- No permission to delete production resources
```

For human workforce access, AWS IAM Identity Center is generally preferable to creating individual long-lived IAM users.

## 6.2 IAM Groups

IAM Groups organize IAM users.

Example:

```text
AWS Account
│
├── Developers
│   ├── Developer A
│   ├── Developer B
│   └── Developer C
│
├── Administrators
│   ├── Admin A
│   └── Admin B
│
└── Testers
    ├── Tester A
    └── Tester B
```

You can attach permissions to groups instead of managing every user individually.

## 6.3 IAM Roles

An IAM Role is an identity that provides temporary permissions when assumed by an authorized principal.

Roles are especially important for AWS services.

Example:

Your EC2 application needs permission to upload files to S3.

You should not store AWS access keys directly inside your application.

Instead:

1. Create an IAM Role.
2. Attach an appropriate S3 policy.
3. Assign the role to the EC2 instance.
4. Use the AWS SDK.
5. Let the SDK obtain temporary credentials.

Example using AWS SDK for JavaScript:

```bash
npm install @aws-sdk/client-s3
```

```ts
import {
  S3Client,
  PutObjectCommand,
} from "@aws-sdk/client-s3";

const s3 = new S3Client({
  region: process.env.AWS_REGION,
});

export async function uploadFile(
  file: Buffer,
  key: string
) {
  await s3.send(
    new PutObjectCommand({
      Bucket: process.env.S3_BUCKET_NAME!,
      Key: key,
      Body: file,
      ContentType: "application/octet-stream",
    })
  );
}
```

Notice that no AWS access key or secret key is hardcoded.

The SDK can automatically use the credentials supplied through the EC2 instance role.

## 6.4 IAM Policies

IAM Policies define permissions.

Example policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-app-uploads/*"
    }
  ]
}
```

This policy allows reading and uploading objects inside the specified bucket.

It does not grant access to every S3 bucket.

### Important IAM Concepts

| Concept | Purpose |
|---|---|
| User | Individual AWS identity |
| Group | Collection of IAM users |
| Role | Assumable identity with temporary permissions |
| Policy | Defines allowed or denied actions |
| Principal | Identity that can perform an action |
| Resource | AWS object being accessed |

## 6.5 Least Privilege Principle

Give an identity only the permissions it needs.

Avoid:

```text
AdministratorAccess
```

for normal application workloads.

Prefer:

```text
Allow:
s3:GetObject
s3:PutObject

Resource:
arn:aws:s3:::my-app-uploads/*
```

**Important:** Protect the AWS root account with MFA. Avoid using root credentials for routine operations.

Official reference: [AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html?utm_source=chatgpt.com)

---

# 7. Amazon EC2 – Elastic Compute Cloud

EC2 is AWS's virtual server service.

It allows you to create virtual machines in the cloud.

You can install:

- Ubuntu
- Amazon Linux
- Windows Server
- Node.js
- Nginx
- Docker
- MongoDB
- MySQL
- Other application dependencies

## 7.1 EC2 Architecture

```text
Internet
   |
   v
EC2 Instance
   |
   ├── Operating System
   |
   ├── Nginx
   |
   ├── Node.js
   |
   ├── Express API
   |
   └── Next.js Application
```

## 7.2 Important EC2 Components

### 1. AMI – Amazon Machine Image

An AMI is a template used to launch EC2 instances.

It contains information such as:

- Operating system.
- Initial software configuration.
- Required block-device mappings.

Examples:

- Ubuntu AMI
- Amazon Linux AMI
- Windows Server AMI

### 2. Instance Type

An instance type defines the virtual hardware capacity.

It determines:

- vCPU
- RAM
- Network performance
- Storage characteristics

Example:

| Instance Type | General Category |
|---|---|
| `t3.micro` | General purpose |
| `t3.small` | General purpose |
| `m7i.large` | General purpose |
| `c7i.large` | Compute optimized |
| `r7i.large` | Memory optimized |

Exact availability and pricing depend on the Region and instance configuration.

### 3. Key Pair

A key pair can be used to authenticate SSH connections to supported Linux instances.

Example:

```bash
ssh -i my-server-key.pem ubuntu@EC2_PUBLIC_IP
```

Protect your private key. Never upload it to GitHub.

### 4. EBS – Elastic Block Store

EBS provides block storage volumes for EC2 instances.

Think of EBS as a virtual disk attached to an EC2 server.

You can use it for:

- Operating system storage.
- Application files.
- Database files.
- Logs.

EBS volumes are generally persistent independently of the EC2 instance lifecycle, subject to their configuration and deletion settings.

### 5. User Data

User Data lets you provide startup scripts when launching an EC2 instance.

Example:

```bash
#!/bin/bash

apt update -y

apt install nginx -y

systemctl enable nginx

systemctl start nginx
```

This script installs and starts Nginx when the instance initializes.

### 6. Security Group

A Security Group controls network traffic to and from an EC2 instance.

We will examine it in more detail under VPC networking.

## 7.3 Deploying a Node.js Application on EC2

A simplified deployment process:

**Step 1: Launch an EC2 instance**

Select:

- Ubuntu AMI.
- Suitable instance type.
- VPC and subnet.
- Security group.
- SSH key pair.

**Step 2: Connect through SSH**

```bash
ssh -i my-key.pem ubuntu@EC2_PUBLIC_IP
```

**Step 3: Install Node.js**

Use a supported Node.js installation method and select a version compatible with your application.

**Step 4: Clone the project**

```bash
git clone https://github.com/username/my-project.git

cd my-project
```

**Step 5: Install dependencies**

```bash
npm ci
```

**Step 6: Configure environment variables**

```bash
nano .env
```

Example:

```env
NODE_ENV=production
PORT=3000

MONGODB_URI=your-mongodb-uri

JWT_SECRET=your-secret
```

Do not commit this file to Git.

For production, use a managed secrets solution rather than relying only on manually maintained `.env` files.

**Step 7: Build the application**

```bash
npm run build
```

**Step 8: Start the application**

```bash
npm start
```

For an application requiring a process manager, you can use PM2:

```bash
npm install -g pm2

pm2 start npm --name my-app -- start

pm2 save
```

**Step 9: Configure Nginx**

Nginx can act as a reverse proxy:

```nginx
server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Configure HTTPS using a suitable certificate-management solution.

### Important

EC2 is not the only way to host a Next.js application on AWS.

Other options include:

- ECS with Fargate.
- EKS.
- AWS Amplify.
- Lambda-based deployments through compatible frameworks.
- Elastic Beanstalk.

The correct option depends on operational requirements and application architecture.

Official reference: [Amazon EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html?utm_source=chatgpt.com)

---

# 8. AWS VPC – Virtual Private Cloud

VPC stands for **Virtual Private Cloud**.

It is a logically isolated virtual network where you deploy AWS resources.

You can control:

- IP address ranges.
- Subnets.
- Routing.
- Internet connectivity.
- Network access.
- Private connectivity.

Think of a VPC as your own virtual network inside AWS.

## 8.1 Example VPC Architecture

Suppose you are developing an e-commerce application.

You create a VPC:

```text
VPC: 10.0.0.0/16
│
├── Public Subnet A
│   ├── Load Balancer
│   └── NAT Gateway
│
├── Public Subnet B
│   ├── Load Balancer
│   └── NAT Gateway
│
├── Private Application Subnet A
│   └── EC2 Application Server
│
├── Private Application Subnet B
│   └── EC2 Application Server
│
├── Private Database Subnet A
│   └── RDS
│
└── Private Database Subnet B
    └── RDS
```

The important concept is that application servers and databases do not need direct public internet access.

## 8.2 CIDR Block

CIDR stands for Classless Inter-Domain Routing.

It represents an IP address range.

Example:

```text
10.0.0.0/16
```

This VPC has 65,536 IPv4 addresses in its CIDR range.

You can divide it into smaller subnet ranges.

Example:

| Network | CIDR |
|---|---|
| Main VPC | `10.0.0.0/16` |
| Public Subnet A | `10.0.1.0/24` |
| Public Subnet B | `10.0.2.0/24` |
| Private App A | `10.0.11.0/24` |
| Private App B | `10.0.12.0/24` |
| Private DB A | `10.0.21.0/24` |
| Private DB B | `10.0.22.0/24` |

A `/24` IPv4 subnet contains 256 addresses, but AWS reserves five addresses in each IPv4 subnet.

## 8.3 Subnets

A subnet is a smaller IP address range inside a VPC.

A subnet belongs to exactly one Availability Zone.

Two common types are:

### Public Subnet

A subnet is considered public when its associated route table has a route to an Internet Gateway.

Example:

```text
0.0.0.0/0 → Internet Gateway
```

Resources in a public subnet still require appropriate public IP addressing and security rules to be directly reachable from the internet.

### Private Subnet

A private subnet does not provide direct internet routing through an Internet Gateway.

Private resources may use a NAT Gateway for outbound IPv4 internet access.

Example:

```text
Private EC2
     |
     v
NAT Gateway
     |
     v
Internet Gateway
     |
     v
Internet
```

This allows private instances to initiate outbound connections without allowing unsolicited inbound connections through the NAT Gateway.

## 8.4 Internet Gateway (IGW)

An Internet Gateway connects a VPC to the internet.

Example route:

```text
Destination: 0.0.0.0/0
Target: Internet Gateway
```

An Internet Gateway alone does not automatically make every instance publicly reachable.

The subnet routing, public IP addressing and security configuration must also permit connectivity.

## 8.5 NAT Gateway

NAT stands for Network Address Translation.

A NAT Gateway allows resources in private subnets to initiate outbound IPv4 connections to the internet.

Example:

Your private EC2 server needs to download an npm package.

It can connect through the NAT Gateway without exposing the EC2 server to direct inbound internet connections.

### IGW vs NAT Gateway

| Internet Gateway | NAT Gateway |
|---|---|
| Connects VPC to internet | Allows private resources outbound IPv4 internet access |
| Supports inbound and outbound connectivity subject to routing and security | Does not allow unsolicited inbound connections |
| Used by public subnet routes | Commonly used by private subnet routes |

NAT Gateways incur charges. Design their placement carefully.

## 8.6 Route Tables

A route table determines where network traffic should go.

Example:

```text
Public Route Table

Destination       Target
--------------------------------
10.0.0.0/16       Local
0.0.0.0/0         Internet Gateway
```

Private route table:

```text
Private Route Table

Destination       Target
--------------------------------
10.0.0.0/16       Local
0.0.0.0/0         NAT Gateway
```

## 8.7 VPC Endpoints

VPC Endpoints allow private connectivity to supported AWS services.

For example, an EC2 instance in a private subnet can access S3 without requiring a public internet route through a NAT Gateway.

Two important endpoint types:

| Endpoint | Description |
|---|---|
| Gateway Endpoint | Supports services such as S3 and DynamoDB |
| Interface Endpoint | Uses AWS PrivateLink for supported services |

Gateway endpoints for S3 and DynamoDB have no additional hourly endpoint charge, while interface endpoints have separate pricing.

This can be useful for reducing NAT-related costs. [AWS Documentation](https://docs.aws.amazon.com/wellarchitected/latest/framework/cost04-bp03.html?utm_source=chatgpt.com)

Official reference: [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html?utm_source=chatgpt.com)

---

# 9. Security Groups vs Network ACLs

Both control network traffic in AWS VPCs.

However, they operate at different levels.

## 9.1 Security Groups

Security Groups act as virtual firewalls associated with network interfaces and resources such as EC2 instances.

Example:

```text
Inbound Rules

Type        Port       Source
-----------------------------------
SSH         22         Your IP
HTTP        80         0.0.0.0/0
HTTPS       443        0.0.0.0/0
Custom      3000       ALB Security Group
```

Important:

- Security Groups are stateful.
- They support allow rules.
- Return traffic is automatically permitted.
- Multiple Security Groups can be associated with a network interface.

For production, avoid opening SSH to `0.0.0.0/0`. Prefer restricted administrative access or AWS Systems Manager Session Manager.

## 9.2 Network ACLs

Network ACLs operate at the subnet level.

They control inbound and outbound traffic.

Example:

```text
Inbound Rules

Rule Number    Type       Action
-----------------------------------
100            HTTP       ALLOW
110            HTTPS      ALLOW
120            SSH        DENY
*              ALL        DENY
```

Important:

- Network ACLs are stateless.
- They support allow and deny rules.
- Rules are evaluated in ascending rule-number order.
- Return traffic must be explicitly permitted.

## 9.3 Comparison

| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Network interface/resource | Subnet |
| Stateful | Yes | No |
| Allow rules | Yes | Yes |
| Deny rules | No | Yes |
| Rule evaluation | All applicable rules | Ordered rules |
| Return traffic | Automatically allowed | Must be explicitly allowed |

**Practical recommendation:** Use Security Groups as the primary access-control mechanism. Use Network ACLs as an additional subnet-level security layer when needed. [AWS Documentation](https://docs.aws.amazon.com/en_en/vpc/latest/userguide/infrastructure-security.html?utm_source=chatgpt.com)

---

# 10. Elastic Load Balancing and Auto Scaling

When your application receives more traffic, a single server may not be sufficient.

You can distribute incoming requests across multiple application servers.

This is the purpose of a Load Balancer.

## 10.1 What Is a Load Balancer?

A Load Balancer distributes incoming network requests across multiple targets.

Example:

```text
                Users
                  |
                  v
            Load Balancer
                  |
        ┌─────────┼─────────┐
        |         |         |
        v         v         v
      EC2-A     EC2-B     EC2-C
        |         |         |
        └─────────┼─────────┘
                  |
                  v
              Database
```

Benefits:

- Better traffic distribution.
- Improved availability.
- Easier horizontal scaling.
- Health-check-based routing.

## 10.2 AWS Load Balancer Types

AWS Elastic Load Balancing supports different load balancer types.

### 1. Application Load Balancer (ALB)

ALB works at the application layer (Layer 7).

It understands HTTP and HTTPS.

It supports routing based on:

- Hostname.
- URL path.
- HTTP headers.
- Query strings.

Example:

```text
example.com/api/*
       |
       v
Express API Target Group

example.com/*
       |
       v
Next.js Target Group
```

### 2. Network Load Balancer (NLB)

NLB operates primarily at Layer 4.

It handles TCP, UDP and supported transport protocols.

It is suitable for high-performance network traffic and applications requiring Layer 4 load balancing.

### 3. Gateway Load Balancer

Gateway Load Balancer is used to deploy and scale network virtual appliances, such as certain firewall and inspection systems.

## 10.3 Target Groups

A Target Group defines the destinations to which a load balancer forwards traffic.

Example:

```text
ALB
 |
 ├── Target Group: Next.js
 │      ├── EC2-A
 │      └── EC2-B
 │
 └── Target Group: Express API
        ├── EC2-C
        └── EC2-D
```

## 10.4 Health Checks

A load balancer periodically checks whether a target is healthy.

Example endpoint:

```http
GET /health
```

Express implementation:

```ts
import express from "express";

const app = express();

app.get("/health", (_req, res) => {
  res.status(200).json({
    status: "healthy",
  });
});
```

A successful health check allows the load balancer to consider the target eligible to receive traffic.

## 10.5 Auto Scaling Groups (ASG)

An Auto Scaling Group manages a collection of EC2 instances.

It can automatically add or remove instances according to configured scaling policies.

Example:

```text
Minimum Instances: 2
Desired Instances: 2
Maximum Instances: 10
```

Suppose CPU utilization increases significantly.

The Auto Scaling Group can launch additional instances.

When demand decreases, it can terminate unnecessary instances.

### Types of Scaling

| Type | Description |
|---|---|
| Manual Scaling | You change capacity yourself |
| Dynamic Scaling | Scaling based on metrics |
| Scheduled Scaling | Scaling at predefined times |
| Predictive Scaling | Uses forecasts to anticipate demand |

### Production Architecture

```text
Internet
   |
   v
Application Load Balancer
   |
   v
Auto Scaling Group
   |
   ├── EC2 Instance A
   ├── EC2 Instance B
   └── EC2 Instance C
```

**Important:** Auto Scaling requires your application to support multiple instances.

For example, do not store essential session data only in the local memory of one EC2 instance.

Use a shared session store or another suitable session architecture.

Official references:

- [Elastic Load Balancing Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html?utm_source=chatgpt.com)
- [EC2 Auto Scaling Documentation](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html?utm_source=chatgpt.com)

---

# 11. Amazon S3 – Simple Storage Service

Amazon S3 is an object storage service.

It is commonly used to store:

- Images.
- Videos.
- Documents.
- PDFs.
- Backups.
- Application uploads.
- Static website assets.
- Data exports.

## 11.1 What Is Object Storage?

In object storage, data is stored as objects inside buckets.

Example:

```text
S3 Bucket: my-ecommerce-assets
│
├── products/
│   ├── laptop.jpg
│   ├── mobile.jpg
│   └── headphones.jpg
│
├── users/
│   ├── profile-01.png
│   └── profile-02.png
│
└── documents/
    ├── invoice-01.pdf
    └── invoice-02.pdf
```

Each object has:

- Object key.
- Object data.
- Metadata.
- Associated properties.

## 11.2 Bucket

A bucket is a container for objects.

Example:

```text
my-ecommerce-assets
```

Bucket names must be globally unique within the relevant AWS partition.

## 11.3 Object Key

An object key identifies an object inside a bucket.

Example:

```text
products/laptop.jpg
```

S3 uses object keys rather than traditional filesystem directories. The `/` characters are part of the key and can be used to present a folder-like structure.

## 11.4 Uploading Files Using Node.js

Install the AWS SDK:

```bash
npm install @aws-sdk/client-s3
```

Create an S3 client:

```ts
import { S3Client } from "@aws-sdk/client-s3";

export const s3 = new S3Client({
  region: process.env.AWS_REGION,
});
```

Upload an object:

```ts
import { PutObjectCommand } from "@aws-sdk/client-s3";
import { s3 } from "./s3";

export async function uploadImage(
  file: Buffer,
  fileName: string
) {
  const key = `products/${fileName}`;

  await s3.send(
    new PutObjectCommand({
      Bucket: process.env.S3_BUCKET_NAME!,
      Key: key,
      Body: file,
      ContentType: "image/jpeg",
    })
  );

  return key;
}
```

This example assumes the application has already validated the uploaded file.

In production, also implement:

- File size limits.
- Actual file-type verification.
- Safe object-key generation.
- Authorization.
- Malware scanning where appropriate.
- Appropriate storage policies.

## 11.5 S3 Presigned URLs

A presigned URL provides temporary access to a specific S3 operation.

For example, you want users to upload profile images directly to S3.

Instead of routing the entire file through your backend:

1. Client requests an upload URL.
2. Backend verifies user permissions.
3. Backend generates a presigned URL.
4. Client uploads directly to S3.
5. Backend records the object key.

Example:

```bash
npm install @aws-sdk/s3-request-presigner
```

```ts
import { PutObjectCommand } from "@aws-sdk/client-s3";

import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

import { s3 } from "./s3";

export async function generateUploadUrl(
  key: string
) {
  const command = new PutObjectCommand({
    Bucket: process.env.S3_BUCKET_NAME!,
    Key: key,
    ContentType: "image/jpeg",
  });

  return getSignedUrl(s3, command, {
    expiresIn: 300,
  });
}
```

The URL expires after five minutes.

A presigned URL is a temporary bearer credential. Anyone who obtains it can use it within its permitted operation and validity period.

## 11.6 S3 Versioning

Versioning preserves multiple versions of an object.

Example:

```text
profile.jpg

Version 1
Version 2
Version 3
```

If an object is overwritten or deleted, earlier versions can remain recoverable, subject to versioning and lifecycle configuration.

## 11.7 S3 Storage Classes

S3 offers different storage classes for different access patterns.

| Storage Class | Typical Use |
|---|---|
| S3 Standard | Frequently accessed data |
| S3 Intelligent-Tiering | Data with changing or uncertain access patterns |
| S3 Standard-IA | Infrequently accessed data |
| S3 One Zone-IA | Infrequently accessed data where single-AZ storage is acceptable |
| S3 Glacier Instant Retrieval | Archive data requiring milliseconds retrieval |
| S3 Glacier Flexible Retrieval | Archive data with flexible retrieval times |
| S3 Glacier Deep Archive | Long-term archival |

## 11.8 S3 Security

Important practices:

- Enable Block Public Access unless public access is intentionally required.
- Use IAM policies and bucket policies.
- Enable encryption.
- Enable versioning where useful.
- Configure lifecycle policies.
- Use CloudTrail data events when object-level audit visibility is required.
- Use presigned URLs for temporary access.

**Important:** A bucket should not be made public simply because users need to view images.

A common production architecture is:

```text
User
 |
 v
CloudFront
 |
 v
Private S3 Bucket
```

CloudFront can access the bucket through Origin Access Control (OAC).

Official reference: [Amazon S3 User Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html?utm_source=chatgpt.com)

---

# 12. Amazon RDS – Relational Database Service

Amazon RDS is a managed relational database service.

It simplifies database operations such as:

- Provisioning.
- Backups.
- Patching.
- Monitoring.
- Replication.
- Recovery.

Supported database engines include:

- MySQL.
- PostgreSQL.
- MariaDB.
- Oracle.
- Microsoft SQL Server.
- Amazon Aurora.

## 12.1 Self-Managed Database vs RDS

Suppose you install MySQL directly on an EC2 instance.

You are responsible for:

- Installing MySQL.
- Configuring backups.
- Managing upgrades.
- Monitoring database health.
- Managing replication.
- Configuring recovery procedures.

With RDS, AWS manages many of these operational tasks.

However, you still manage:

- Database schema.
- Users and permissions.
- Query optimization.
- Connection management.
- Application-level security.
- Backup and recovery requirements.

## 12.2 RDS Architecture

```text
Next.js / Express Application
             |
             v
        RDS Endpoint
             |
             v
       MySQL Database
```

A production RDS database should generally be deployed in private subnets.

Your application servers can connect through private networking.

## 12.3 Multi-AZ Deployment

Multi-AZ deployments improve database availability.

A simplified architecture:

```text
Primary DB
   |
   | Synchronous replication
   |
   v
Standby DB
```

If the primary database becomes unavailable, RDS can perform a failover to a standby in another Availability Zone.

The standby is generally intended for availability and failover, not normal read traffic.

## 12.4 Read Replicas

Read replicas are used to scale read-heavy workloads.

Example:

```text
Express Application
        |
        v
   Primary Database
        |
        ├── Read Replica 1
        |
        └── Read Replica 2
```

Your application can route suitable read queries to replicas.

However, replication lag may mean that a replica does not immediately contain the latest committed data.

## 12.5 Multi-AZ vs Read Replica

| Feature | Multi-AZ | Read Replica |
|---|---|---|
| Primary purpose | High availability | Read scaling |
| Normal read traffic | Generally primary | Can serve reads |
| Failover | Managed failover capability | Depends on configuration and engine |
| Replication | Engine/deployment-specific synchronous or other supported mechanisms | Generally asynchronous for common RDS engines |
| Use case | Availability | Read-heavy workloads |

## 12.6 Connecting Node.js to RDS MySQL

Install:

```bash
npm install mysql2
```

Create a connection pool:

```ts
import mysql from "mysql2/promise";

export const db = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,

  waitForConnections: true,

  connectionLimit: 10,

  queueLimit: 0,
});
```

Use it:

```ts
const [rows] = await db.execute(
  "SELECT * FROM users WHERE id = ?",
  [userId]
);
```

### Why Use a Connection Pool?

Opening a new database connection for every HTTP request creates unnecessary overhead.

A connection pool manages reusable connections.

This becomes especially important when you run multiple EC2 instances.

Remember that the total number of possible connections is approximately influenced by:

```text
Application Instances × Pool Size
```

For example:

```text
5 EC2 instances × 10 connections = 50 connections
```

This must be considered when selecting database capacity.

Official reference: [Amazon RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html?utm_source=chatgpt.com)

---

# 13. MongoDB Atlas and Cloud Database Architecture

MongoDB Atlas is MongoDB's managed cloud database platform.

It allows you to deploy MongoDB clusters without managing the underlying database infrastructure yourself.

You can use Atlas with AWS-hosted applications.

## 13.1 MongoDB Atlas Architecture

```text
Next.js Application
        |
        v
Express API on AWS EC2
        |
        v
MongoDB Atlas
        |
        ├── Replica Set
        ├── Primary Node
        ├── Secondary Node
        └── Secondary Node
```

## 13.2 Replica Sets

A MongoDB replica set maintains multiple copies of the same data across different nodes.

Common configuration:

```text
Replica Set
│
├── Primary
│
├── Secondary
│
└── Secondary
```

The primary accepts writes.

Secondary nodes replicate the primary's data and can serve eligible reads depending on read preference.

If the primary becomes unavailable, an eligible secondary can be elected as the new primary.

## 13.3 Sharding

Sharding distributes data across multiple database servers.

It is used when a single replica set cannot meet the required storage or throughput capacity.

Example:

```text
MongoDB Sharded Cluster
│
├── Shard 1
│
├── Shard 2
│
└── Shard 3
```

A sharded cluster also uses routing and metadata components to manage distributed data access.

## 13.4 Connecting AWS EC2 to MongoDB Atlas

You have several connectivity options:

1. Public IP access list.
2. VPC peering.
3. AWS PrivateLink.

For production workloads, private connectivity is worth evaluating.

MongoDB recommends private endpoints for many new production deployments because they provide more granular network access than broad IP access-list configurations. [MongoDB](https://www.mongodb.com/docs/atlas/architecture/current/network-security/?utm_source=chatgpt.com)

### Example: Environment Variable

```env
MONGODB_URI=mongodb+srv://username:password@cluster.example.mongodb.net/app
```

Use this URI only on the server.

Never expose it through:

```env
NEXT_PUBLIC_MONGODB_URI=...
```

That would expose a database connection string to the browser.

### Important Connection Consideration

When deploying multiple application instances:

- Reuse MongoDB client connections.
- Configure connection pool limits.
- Avoid creating a new connection for every request.
- Monitor connection usage.
- Use private networking where appropriate.

Official reference: [MongoDB Atlas Documentation](https://www.mongodb.com/docs/atlas/?utm_source=chatgpt.com)

---

# 14. Amazon ElastiCache – Managed Redis

Amazon ElastiCache is a managed in-memory caching service.

It supports Redis OSS and Valkey, with supported deployment options depending on the service configuration.

It is useful for:

- Caching.
- Session storage.
- Rate limiting.
- Real-time application data.
- Distributed coordination.
- Frequently accessed data.

## 14.1 Why Use ElastiCache?

Suppose your application frequently executes:

```sql
SELECT * FROM products;
```

against the database.

If the same data is requested thousands of times, repeatedly querying the database may be unnecessary.

Instead:

```text
Client
  |
  v
Express API
  |
  v
Redis Cache
  |
  | Cache Miss
  v
RDS MySQL
```

The application checks Redis first.

If the data exists, it returns the cached result.

If not, it queries the database and stores the result in Redis.

## 14.2 Cache-Aside Example

```ts
const cachedProducts = await redis.get("products");

if (cachedProducts) {
  return JSON.parse(cachedProducts);
}

const products = await fetchProductsFromDatabase();

await redis.set(
  "products",
  JSON.stringify(products),
  {
    EX: 300,
  }
);

return products;
```

The cache entry expires after 300 seconds.

## 14.3 ElastiCache vs Self-Managed Redis

| ElastiCache | Self-Managed Redis |
|---|---|
| Managed service | You manage the server |
| Simplified operations | More infrastructure control |
| Managed monitoring integrations | You configure monitoring |
| Managed deployment options | You manage deployment and recovery |
| AWS-specific integration | Can run on many platforms |

**Important:** ElastiCache is generally deployed inside a VPC. Your application should connect through private network paths and appropriate security groups.

Official reference: [Amazon ElastiCache Documentation](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.html?utm_source=chatgpt.com)

---

# 15. Amazon Route 53 – DNS Management

Route 53 is AWS's scalable DNS and domain-management service.

DNS stands for Domain Name System.

DNS translates domain names into information that allows clients to locate services.

Example:

```text
example.com
     |
     v
DNS Resolution
     |
     v
IP Address / AWS Resource
     |
     v
Application
```

## 15.1 Common DNS Record Types

| Record | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address |
| AAAA | Maps a hostname to an IPv6 address |
| CNAME | Maps one hostname to another hostname |
| MX | Mail routing |
| TXT | Text records, verification, SPF, DKIM and other uses |
| NS | Name server records |
| SOA | Zone authority information |
| Alias | AWS-specific routing feature for supported AWS resources |

## 15.2 Hosted Zones

A hosted zone contains DNS records for a domain.

For example:

```text
example.com
│
├── A / Alias: example.com
│
├── A / Alias: www.example.com
│
├── A / Alias: api.example.com
│
└── MX: Mail Server
```

Route 53 supports public and private hosted zones.

### Public Hosted Zone

Used for DNS records accessible through public DNS.

### Private Hosted Zone

Used for DNS resolution within associated VPCs.

## 15.3 Routing Policies

Route 53 supports multiple routing policies.

| Routing Policy | Purpose |
|---|---|
| Simple | Basic DNS routing |
| Weighted | Distributes traffic according to assigned weights |
| Latency-based | Routes based on latency measurements |
| Failover | Supports primary/secondary failover routing |
| Geolocation | Routes based on user geographic location |
| Geoproximity | Routes based on geographic proximity and configured bias |
| IP-based | Routes based on client IP ranges |

Example:

You deploy your application in Mumbai and Singapore.

You may use latency-based routing to direct users to an appropriate regional endpoint.

Official reference: [Amazon Route 53 Developer Guide](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html?utm_source=chatgpt.com)

---

# 16. Amazon CloudFront – Content Delivery Network

CloudFront is AWS's Content Delivery Network (CDN).

A CDN distributes content through geographically distributed edge infrastructure.

It helps reduce latency and origin-server load.

## 16.1 Without CDN

Suppose your application is hosted in Mumbai.

A user in the United States requests an image.

```text
US User
   |
   v
Mumbai Server
   |
   v
Image Response
```

The request travels to your origin infrastructure.

## 16.2 With CloudFront

```text
US User
   |
   v
CloudFront Edge Location
   |
   ├── Cache Hit → Return Cached Content
   |
   └── Cache Miss
          |
          v
       Origin
```

The origin could be:

- S3.
- An Application Load Balancer.
- An EC2-based application.
- Another supported HTTP origin.

## 16.3 CloudFront Important Concepts

### Distribution

A CloudFront distribution defines how content is delivered.

### Origin

The source from which CloudFront retrieves content.

### Cache Behavior

Defines how CloudFront handles requests matching specific URL patterns.

Example:

```text
/images/*
```

This behavior can have a different cache policy from:

```text
/api/*
```

### TTL

Time to Live determines how long an object can remain cached under the applicable cache policy.

### Cache Invalidation

If content changes, you may need to invalidate cached objects.

Example:

```text
/images/logo.png
```

## 16.4 CloudFront with S3

A common architecture:

```text
User
 |
 v
CloudFront
 |
 v
Private S3 Bucket
```

Use Origin Access Control (OAC) to let CloudFront access a private S3 origin without making the bucket publicly accessible.

## 16.5 CloudFront vs Route 53

| Route 53 | CloudFront |
|---|---|
| DNS service | CDN |
| Resolves domain names | Delivers content |
| Supports routing policies | Supports caching and edge delivery |
| Manages DNS records | Manages distributions and origins |

Official reference: [Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html?utm_source=chatgpt.com)

---

# 17. AWS Lambda – Serverless Computing

AWS Lambda allows you to execute code without managing servers directly.

You upload your function code and configure its runtime, permissions and triggers.

AWS manages the underlying compute infrastructure.

## 17.1 What Is Serverless?

Serverless does not mean that servers do not exist.

It means the cloud provider manages much of the server provisioning and operational infrastructure.

You focus on:

- Function code.
- Events.
- Permissions.
- Configuration.
- Application logic.

## 17.2 Lambda Architecture

```text
Event Source
    |
    v
AWS Lambda
    |
    v
Execute Function
    |
    v
Return Result / Perform Action
```

Possible event sources include:

- API Gateway.
- S3.
- SQS.
- EventBridge.
- Other supported AWS services.

## 17.3 Lambda Example

A simplified Node.js Lambda function:

```ts
export const handler = async (event: unknown) => {
  console.log("Received event:", event);

  return {
    statusCode: 200,

    body: JSON.stringify({
      message: "Hello from AWS Lambda",
    }),
  };
};
```

This handler is suitable for an event or integration expecting a compatible response structure, such as a configured API Gateway integration.

## 17.4 Lambda Use Cases

### Image Processing

```text
User Uploads Image
       |
       v
       S3
       |
       v
     Lambda
       |
       v
Resize Image
       |
       v
Save Thumbnail to S3
```

### Automated Email Workflow

```text
New Order
    |
    v
EventBridge / SQS
    |
    v
Lambda
    |
    v
Email Service
```

### Scheduled Tasks

You can use EventBridge schedules to trigger Lambda functions.

Example:

- Generate daily reports.
- Clean temporary files.
- Process periodic data.
- Send scheduled notifications.

## 17.5 Cold Starts

A cold start occurs when Lambda needs to initialize an execution environment before processing an invocation.

Cold-start duration depends on factors such as:

- Runtime.
- Package size.
- Initialization work.
- Networking configuration.
- Runtime and service behavior.

You can reduce unnecessary initialization work and evaluate provisioned concurrency when predictable startup latency is important.

## 17.6 Lambda vs EC2

| EC2 | Lambda |
|---|---|
| Virtual server | Function execution environment |
| You manage OS and server configuration | AWS manages underlying compute infrastructure |
| Long-running processes supported | Execution is invocation-based |
| Pay for provisioned resources and related usage | Pricing is based on invocation and resource consumption |
| Suitable for persistent services | Suitable for event-driven workloads |

### Important

Lambda is not automatically the best choice for every backend.

Long-running WebSocket servers, persistent processes and workloads requiring specialized runtime control may be better suited to EC2, ECS or EKS.

Official reference: [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html?utm_source=chatgpt.com)

---

# 18. AWS CloudWatch – Monitoring and Logging

CloudWatch is AWS's monitoring and observability service.

It helps you understand the health and performance of your infrastructure and applications.

## 18.1 CloudWatch Components

### 1. CloudWatch Metrics

Metrics are numerical measurements collected over time.

Examples:

- EC2 CPU utilization.
- Network traffic.
- Request count.
- Database connections.
- Lambda duration.
- Lambda errors.

Example:

```text
EC2 CPU Utilization

Time          CPU
--------------------
10:00 AM      20%
10:05 AM      35%
10:10 AM      80%
10:15 AM      95%
```

### 2. CloudWatch Logs

CloudWatch Logs stores and organizes application and service logs.

Example:

```ts
console.log("Order created:", orderId);

console.error("Payment processing failed:", error);
```

You can configure application and infrastructure logs to be sent to CloudWatch.

### 3. CloudWatch Alarms

Alarms monitor metrics and trigger actions when configured conditions are met.

Example:

```text
Metric: CPU Utilization

Condition:
CPU > 80%

Evaluation:
For configured evaluation periods

Action:
Send SNS notification
```

### 4. CloudWatch Dashboards

Dashboards display important metrics in a single place.

Example dashboard:

```text
Production Monitoring

CPU Utilization
Memory Utilization
Network Traffic
HTTP Request Count
5xx Error Rate
Database Connections
Lambda Errors
```

## 18.2 CloudWatch vs CloudTrail

These services solve different problems.

| CloudWatch | CloudTrail |
|---|---|
| Monitoring and observability | AWS account activity auditing |
| Metrics and logs | API activity and account events |
| Alarms | Security and compliance investigations |
| Application performance | Who performed which AWS action |

Example:

- CloudWatch helps identify high EC2 CPU usage.
- CloudTrail helps investigate who modified an EC2 security group.

Official references:

- [Amazon CloudWatch Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html?utm_source=chatgpt.com)
- [AWS CloudTrail Documentation](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html?utm_source=chatgpt.com)

---

# 19. AWS Secrets Manager and Parameter Store

Applications require sensitive configuration.

Examples:

```env
DATABASE_PASSWORD=
JWT_SECRET=
PAYMENT_API_KEY=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
```

You should never hardcode secrets into application source code.

You also should not commit them to GitHub.

AWS provides services for storing configuration and secrets.

## 19.1 AWS Secrets Manager

Secrets Manager is designed for securely storing, retrieving and managing secrets.

Examples:

- Database credentials.
- API keys.
- Application credentials.
- Third-party service credentials.

It supports features such as:

- Encryption.
- Access control.
- Secret versioning.
- Rotation integrations for supported configurations.

## 19.2 AWS Systems Manager Parameter Store

Parameter Store stores configuration data and parameters.

It supports:

- String parameters.
- String lists.
- SecureString parameters.

Example:

```text
/app/production/database/host
/app/production/database/username
/app/production/database/password
/app/production/api/base-url
```

## 19.3 Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|---|---|---|
| Main purpose | Secrets lifecycle management | Configuration and parameter storage |
| Secure values | Yes | Yes, using SecureString |
| Secret rotation | Built-in rotation capabilities for supported integrations | Usually requires additional automation |
| Configuration storage | Yes | Yes |
| Cost | Separate pricing | Standard parameters have a different pricing model; advanced features may incur charges |

### Production Recommendation

Use Secrets Manager for credentials that require managed secret lifecycle and rotation capabilities.

Use Parameter Store for application configuration and suitable parameter-storage use cases.

Avoid putting secrets into:

- GitHub repositories.
- Docker images.
- Client-side environment variables.
- Logs.
- URLs.
- Public application responses.

Official references:

- [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html?utm_source=chatgpt.com)
- [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html?utm_source=chatgpt.com)

---

# 20. Infrastructure as Code (IaC) with Terraform

Infrastructure as Code means defining infrastructure using configuration files instead of creating everything manually.

For example, instead of manually creating:

- EC2 instances.
- VPCs.
- Subnets.
- Security Groups.
- S3 buckets.

You describe the desired infrastructure in code.

Terraform can then provision and manage those resources through provider APIs.

## 20.1 What Is Terraform?

Terraform is an Infrastructure as Code tool developed by HashiCorp.

It supports multiple infrastructure providers, including:

- AWS.
- Azure.
- Google Cloud.
- Kubernetes.
- Other platforms.

## 20.2 Why Use Terraform?

Imagine you need the same infrastructure for:

- Development.
- Testing.
- Staging.
- Production.

Creating everything manually introduces inconsistency.

With Terraform, you can define reusable infrastructure configurations.

Benefits:

- Repeatability.
- Version control.
- Automation.
- Infrastructure review.
- Reusable modules.
- Reduced manual configuration.

## 20.3 Terraform Important Concepts

| Concept | Description |
|---|---|
| Provider | Integrates Terraform with an infrastructure platform |
| Resource | Infrastructure object Terraform manages |
| Data Source | Reads information about existing infrastructure |
| Variable | Input to Terraform configuration |
| Output | Exposes useful values |
| State | Tracks managed infrastructure |
| Module | Reusable collection of Terraform configuration |
| Backend | Defines where Terraform state is stored |

## 20.4 Terraform Project Structure

```text
terraform/
│
├── main.tf
├── provider.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
│
└── modules/
    ├── networking/
    ├── ec2/
    ├── database/
    └── storage/
```

## 20.5 Installing Terraform

Follow the official installation instructions:

[Terraform Installation Guide](https://developer.hashicorp.com/terraform/install?utm_source=chatgpt.com)

Verify:

```bash
terraform version
```

Configure AWS authentication using an appropriate method, such as an AWS CLI profile or temporary credentials.

Avoid placing long-lived AWS access keys directly inside Terraform configuration.

## 20.6 Create Your First Terraform Configuration

Create:

```text
terraform/
│
├── provider.tf
└── main.tf
```

### provider.tf

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }

  required_version = ">= 1.5.0"
}

provider "aws" {
  region = "ap-south-1"
}
```

This example uses a Terraform AWS provider version constraint. Before applying it, verify the currently selected provider version and compatibility with your Terraform installation.

### main.tf

```hcl
resource "aws_s3_bucket" "app_bucket" {
  bucket = "my-unique-learning-bucket-2026"
}
```

Replace the example bucket name with a globally unique name before applying.

## 20.7 Terraform Workflow

The normal workflow:

```text
Write Configuration
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

### Step 1: Initialize

```bash
terraform init
```

Downloads provider plugins and initializes the working directory.

### Step 2: Format

```bash
terraform fmt
```

Formats configuration files.

### Step 3: Validate

```bash
terraform validate
```

Checks whether the configuration is syntactically and structurally valid.

### Step 4: Plan

```bash
terraform plan
```

Shows the changes Terraform proposes to make.

### Step 5: Apply

```bash
terraform apply
```

Applies the proposed infrastructure changes after approval.

### Step 6: Destroy

```bash
terraform destroy
```

Destroys resources managed by the current Terraform configuration.

**Warning:** Never run `terraform destroy` against production infrastructure without carefully reviewing its impact.

Terraform's plan and apply workflow is documented here: [HashiCorp Developer](https://developer.hashicorp.com/terraform/cli/commands/plan?utm_source=chatgpt.com)

## 20.8 Terraform Variables

Instead of hardcoding values:

```hcl
resource "aws_s3_bucket" "app_bucket" {
  bucket = "my-unique-learning-bucket-2026"
}
```

You can define a variable.

### variables.tf

```hcl
variable "bucket_name" {
  description = "S3 bucket name"

  type = string
}
```

### main.tf

```hcl
resource "aws_s3_bucket" "app_bucket" {
  bucket = var.bucket_name
}
```

### terraform.tfvars

```hcl
bucket_name = "my-unique-learning-bucket-2026"
```

Remember that `terraform.tfvars` should not be committed if it contains sensitive values.

## 20.9 Terraform State

Terraform maintains a state file to track the relationship between configuration and real infrastructure.

By default:

```text
terraform.tfstate
```

This file may contain sensitive information.

Do not:

- Commit state files to GitHub.
- Edit the state file manually.
- Give unrestricted access to the state backend.

For team environments, use a secure remote backend.

AWS S3 can be used as a Terraform state backend. Current backend configurations should be checked against the Terraform version being used; modern S3 backend configurations support state locking through the backend's supported mechanisms.

Remote state and locking help teams coordinate infrastructure changes. [HashiCorp Developer](https://developer.hashicorp.com/terraform/language/state/remote?utm_source=chatgpt.com)

### Important

Terraform is not just a deployment tool.

It manages the lifecycle of infrastructure resources.

You can use it to provision and update:

- Networking.
- Compute.
- Storage.
- Databases.
- IAM resources.
- Monitoring infrastructure.

Official reference: [Terraform Documentation](https://developer.hashicorp.com/terraform/docs?utm_source=chatgpt.com)

---

# 21. Production-Ready AWS Architecture

Now let's combine the concepts.

Suppose you are building a production e-commerce application using:

- Next.js 16.
- Node.js.
- Express.
- MongoDB.
- MySQL.
- Redis.
- Docker.

We want to deploy it using AWS.

## 21.1 Application Requirements

Our application needs:

1. Frontend.
2. Backend API.
3. Database.
4. Cache.
5. Image storage.
6. DNS.
7. HTTPS.
8. Load balancing.
9. Monitoring.
10. Backup and recovery.
11. Secure infrastructure.
12. Scalability.

## 21.2 High-Level Architecture

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
                     AWS WAF (Optional)
                           |
                           v
                 Application Load Balancer
                           |
             ┌─────────────┴─────────────┐
             |                           |
             v                           v
       Next.js Service              Express API
             |                           |
             └─────────────┬─────────────┘
                           |
             ┌─────────────┼─────────────┐
             |             |             |
             v             v             v
           RDS          Redis         MongoDB Atlas
          MySQL       ElastiCache
             |
             |
        S3 Storage
```

This is a conceptual architecture. In a real deployment, S3 uploads and static assets may be accessed through separate CloudFront behaviors or direct presigned upload flows.

## 21.3 Network Architecture

```text
AWS Region: Mumbai
│
└── VPC: 10.0.0.0/16
    │
    ├── Availability Zone A
    │   │
    │   ├── Public Subnet
    │   │   ├── Load Balancer
    │   │   └── NAT Gateway
    │   │
    │   ├── Private App Subnet
    │   │   └── Application Instance
    │   │
    │   └── Private DB Subnet
    │       └── RDS Resources
    │
    └── Availability Zone B
        │
        ├── Public Subnet
        │   └── Load Balancer
        │
        ├── Private App Subnet
        │   └── Application Instance
        │
        └── Private DB Subnet
            └── RDS Resources
```

The architecture uses multiple Availability Zones to improve resilience.

## 21.4 Frontend Layer

You have two broad deployment choices.

**Option A: Next.js on EC2 or ECS**

```text
CloudFront
    |
    v
Application Load Balancer
    |
    v
Next.js Containers
```

Useful when you need control over the Node.js runtime and application environment.

**Option B: Static Frontend on S3 and CloudFront**

```text
User
 |
 v
CloudFront
 |
 v
S3
```

This is appropriate for a statically exported frontend that does not require Next.js server-side runtime features.

A normal Next.js App Router application that uses Server Components, Server Actions or server-side rendering cannot simply be hosted as static files without architectural changes.

## 21.5 Backend Layer

Express API can run on:

- EC2.
- ECS with Fargate.
- EKS.

Example:

```text
Application Load Balancer
           |
           v
      Express API
           |
           v
     Backend Services
```

Use multiple instances or tasks for horizontal scaling.

## 21.6 Database Layer

Use RDS for relational data:

```text
Orders
Users
Payments
Transactions
```

Use MongoDB Atlas where document-oriented data is appropriate:

```text
Product Catalog
Flexible Product Metadata
Content Documents
```

Use ElastiCache for frequently accessed data.

Do not introduce multiple databases merely because the services are available. Choose each database based on application requirements.

## 21.7 Storage Layer

Use S3 for:

- Product images.
- User profile images.
- Invoices.
- PDF files.
- Exports.
- Backups.

Use CloudFront to distribute suitable content efficiently.

## 21.8 Security Layer

Use:

- IAM roles.
- Security Groups.
- Private subnets.
- HTTPS.
- AWS Secrets Manager.
- Encryption at rest.
- Encryption in transit.
- Restricted database access.
- CloudTrail.
- CloudWatch.
- AWS WAF where appropriate.

## 21.9 Monitoring Layer

Use CloudWatch to monitor:

- EC2 CPU.
- Application logs.
- ALB request count.
- HTTP 4xx and 5xx responses.
- RDS connections.
- Database performance.
- Lambda errors.

Configure alarms for important production metrics.

## 21.10 Backup and Disaster Recovery

Production infrastructure requires a recovery strategy.

Consider:

- RDS automated backups.
- Database snapshots.
- S3 versioning.
- S3 lifecycle policies.
- Cross-region backups where required.
- MongoDB Atlas backups.
- Terraform state backups and remote storage.
- Documented recovery procedures.
- Regular recovery tests.

**Important:** A successful backup configuration does not prove that recovery will work. Test restoration periodically.

---

# 22. AWS Cost Optimization

Cloud resources are not free simply because you do not own physical hardware.

AWS charges for different types of resource consumption.

## 22.1 Common Cost Categories

| Service | Possible Cost Factors |
|---|---|
| EC2 | Instance type, running time, storage, data transfer |
| EBS | Provisioned storage, performance configuration |
| S3 | Storage, requests, retrieval, data transfer |
| RDS | Instance capacity, storage, backups, data transfer |
| NAT Gateway | Hourly usage and processed data |
| CloudFront | Data transfer and requests |
| Lambda | Invocations and execution duration |
| ElastiCache | Node or capacity configuration and usage |
| Route 53 | Hosted zones, queries and supported health checks |

## 22.2 Important Cost Optimization Techniques

### 1. Right-Sizing

Do not use an unnecessarily large EC2 instance.

Choose resources based on actual workload requirements.

### 2. Auto Scaling

Scale resources according to demand.

### 3. S3 Lifecycle Policies

Move older data into suitable lower-cost storage classes.

### 4. Reserved Capacity and Savings Plans

Evaluate commitment-based pricing options for predictable workloads.

### 5. Reduce Unnecessary NAT Traffic

Use VPC endpoints where suitable.

### 6. Delete Unused Resources

Check for:

- Unused EC2 instances.
- Unattached EBS volumes.
- Unused Elastic IP addresses.
- Old snapshots.
- Unused load balancers.
- Unnecessary NAT Gateways.

### 7. Configure Budgets

Set AWS Budgets and billing alerts.

**Important:** A budget alert is not necessarily a hard spending cap. Configure service-specific controls and operational procedures to avoid unexpected charges.

---

# 23. AWS Well-Architected Framework

The AWS Well-Architected Framework provides guidance for evaluating cloud workloads.

It uses six pillars.

| Pillar | Meaning |
|---|---|
| Operational Excellence | Operate and improve workloads effectively |
| Security | Protect data, systems and infrastructure |
| Reliability | Recover from failures and meet availability requirements |
| Performance Efficiency | Use computing resources efficiently |
| Cost Optimization | Control costs while delivering business value |
| Sustainability | Reduce environmental impact and improve resource efficiency |

### Example: E-commerce Application

**Operational Excellence**

- Centralized logs.
- Deployment automation.
- Monitoring dashboards.
- Incident response procedures.

**Security**

- IAM least privilege.
- Encryption.
- Private databases.
- Secure secrets management.

**Reliability**

- Multiple Availability Zones.
- Automated backups.
- Health checks.
- Recovery testing.

**Performance Efficiency**

- CloudFront.
- Redis.
- Database query optimization.
- Appropriate instance sizing.

**Cost Optimization**

- Auto Scaling.
- S3 lifecycle policies.
- Resource right-sizing.
- Budget monitoring.

**Sustainability**

- Efficient resource utilization.
- Avoid unnecessary compute.
- Optimize data storage and transfer.

AWS documents the six pillars and the associated architectural practices in its Well-Architected Framework. [AWS Documentation](https://docs.aws.amazon.com/wellarchitected/?utm_source=chatgpt.com)

Official reference: [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html?utm_source=chatgpt.com)

---

# 24. Common AWS Mistakes

Avoid these mistakes when designing production infrastructure.

| Mistake | Correct Approach |
|---|---|
| Using root credentials for everyday work | Use IAM roles and appropriate identities |
| Giving every developer administrator access | Apply least privilege |
| Storing database credentials in source code | Use Secrets Manager or Parameter Store |
| Exposing RDS publicly without a strong reason | Prefer private database networking |
| Opening SSH to everyone | Restrict administrative access |
| Putting all resources in one AZ | Evaluate multi-AZ architecture |
| Using S3 public access unnecessarily | Keep buckets private and use controlled access |
| Running only one application server | Use redundancy where availability requires it |
| Ignoring database connection limits | Configure connection pools |
| Assuming backups guarantee recovery | Test restoration |
| Creating NAT Gateways without cost evaluation | Review routing and VPC endpoint alternatives |
| Using Terraform state in Git | Use a secure remote backend |
| Running Terraform apply without reviewing changes | Review the execution plan |
| Ignoring CloudWatch alarms | Monitor important production metrics |
| Assuming Lambda is suitable for every backend | Evaluate runtime and workload requirements |

---

# 25. Interview Questions and Answers

## Basic Level

### Q1. What is cloud computing?

Cloud computing is the delivery of computing resources such as servers, storage, networking and databases over the internet.

### Q2. What is AWS?

AWS is Amazon's cloud computing platform that provides infrastructure and managed services.

### Q3. What is EC2?

EC2 is AWS's virtual server service.

### Q4. What is S3?

S3 is an object storage service used to store files and other unstructured data.

### Q5. What is RDS?

RDS is a managed relational database service.

### Q6. What is IAM?

IAM manages identities and permissions for accessing AWS resources.

### Q7. What is a VPC?

A VPC is a logically isolated virtual network inside AWS.

### Q8. What is a Region?

A Region is a geographical area containing AWS infrastructure and multiple Availability Zones.

### Q9. What is an Availability Zone?

An Availability Zone is an isolated location within an AWS Region.

### Q10. What is CloudFront?

CloudFront is AWS's Content Delivery Network.

## Intermediate Level

### Q11. What is the difference between EC2 and Lambda?

EC2 provides virtual servers where you control the operating system and runtime.

Lambda executes code in managed, invocation-based compute environments.

### Q12. What is the difference between a public and private subnet?

A public subnet has a route to an Internet Gateway.

A private subnet does not have direct Internet Gateway routing.

### Q13. What is the difference between Security Groups and Network ACLs?

Security Groups operate at the network-interface/resource level and are stateful.

Network ACLs operate at the subnet level and are stateless.

### Q14. What is a NAT Gateway?

A NAT Gateway allows resources in private subnets to initiate outbound IPv4 connections to external networks.

### Q15. What is an Auto Scaling Group?

An Auto Scaling Group manages a collection of EC2 instances and adjusts capacity according to configured scaling policies.

### Q16. What is a Load Balancer?

A Load Balancer distributes incoming traffic across multiple targets.

### Q17. What is the difference between Multi-AZ and Read Replica?

Multi-AZ primarily supports availability and failover.

Read Replicas primarily support read scaling.

### Q18. What is a presigned URL?

A presigned URL provides temporary access to a specific S3 operation without exposing permanent AWS credentials.

### Q19. What is Terraform?

Terraform is an Infrastructure as Code tool used to define and manage infrastructure through configuration files.

### Q20. What is CloudWatch?

CloudWatch provides monitoring, metrics, logs, alarms and dashboards.

## Advanced Level

### Q21. How would you design a highly available AWS application?

I would deploy application resources across multiple Availability Zones, use a load balancer, configure Auto Scaling, keep databases in private subnets, enable suitable database availability and backup mechanisms, and monitor the system through CloudWatch.

### Q22. How would you securely connect EC2 to S3?

I would create an IAM role with the minimum required S3 permissions, attach the role to EC2 and use the AWS SDK's default credential provider chain.

### Q23. How would you reduce the cost of an AWS application?

I would right-size instances, use Auto Scaling, evaluate Savings Plans, optimize S3 storage classes, review NAT Gateway usage, configure caching and monitor resource consumption.

### Q24. How would you secure an RDS database?

I would:

- Deploy it in private subnets.
- Restrict inbound access through Security Groups.
- Enable encryption.
- Use managed secrets.
- Enable suitable backups.
- Apply least-privilege database permissions.
- Monitor database activity and performance.

### Q25. How would you deploy Next.js and Express on AWS?

One approach is to containerize both applications and deploy them using ECS with Fargate behind an Application Load Balancer.

I would use:

- Route 53 for DNS.
- CloudFront for content delivery.
- S3 for uploads.
- RDS or MongoDB Atlas for persistence.
- ElastiCache for caching.
- Secrets Manager for credentials.
- CloudWatch for monitoring.

### Q26. Why should databases generally be placed in private subnets?

Private subnets reduce direct internet exposure and allow database access to be restricted to authorized application resources.

### Q27. What is the difference between Route 53 and CloudFront?

Route 53 handles DNS resolution and routing.

CloudFront delivers content through a CDN.

### Q28. What is Infrastructure as Code?

Infrastructure as Code is the practice of defining and managing infrastructure through machine-readable configuration files.

### Q29. Why should Terraform state be stored remotely?

Remote state allows teams to share the same infrastructure state and, with a supported backend, coordinate operations through state locking.

### Q30. What is the AWS Well-Architected Framework?

It is an architectural framework that provides guidance for building secure, reliable, efficient and cost-effective cloud workloads.

---

# 26. Practical Assignment

## Project: Deploy a Production-Style E-commerce Application on AWS

Build and deploy an e-commerce application using the technologies covered in this phase.

### Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16 |
| Backend | Node.js + Express |
| Database | MySQL RDS |
| Document Database | MongoDB Atlas |
| Cache | Redis / ElastiCache |
| File Storage | Amazon S3 |
| CDN | CloudFront |
| DNS | Route 53 |
| Compute | EC2 or ECS |
| Load Balancing | Application Load Balancer |
| Monitoring | CloudWatch |
| Secrets | AWS Secrets Manager |
| IaC | Terraform |

## Assignment 1: AWS Fundamentals

- [ ] Create an AWS account.
- [ ] Secure the root account with MFA.
- [ ] Learn how IAM users, roles and policies work.
- [ ] Configure an appropriate IAM identity.
- [ ] Explore the AWS Management Console.
- [ ] Understand AWS Regions and Availability Zones.

## Assignment 2: Networking

- [ ] Create a custom VPC.
- [ ] Create public subnets.
- [ ] Create private application subnets.
- [ ] Create private database subnets.
- [ ] Configure route tables.
- [ ] Configure an Internet Gateway.
- [ ] Configure NAT where required.
- [ ] Configure Security Groups.
- [ ] Understand Network ACLs.

## Assignment 3: Compute

- [ ] Launch an EC2 instance.
- [ ] Connect using SSH or Session Manager.
- [ ] Install Node.js.
- [ ] Install Nginx.
- [ ] Deploy an Express application.
- [ ] Deploy a Next.js application.
- [ ] Configure HTTPS.

## Assignment 4: Storage and Databases

- [ ] Create an S3 bucket.
- [ ] Upload product images.
- [ ] Generate presigned upload URLs.
- [ ] Configure S3 versioning.
- [ ] Create an RDS MySQL database.
- [ ] Connect Express to RDS.
- [ ] Connect the application to MongoDB Atlas.
- [ ] Configure Redis caching.

## Assignment 5: Load Balancing and Scaling

- [ ] Create an Application Load Balancer.
- [ ] Create target groups.
- [ ] Configure health checks.
- [ ] Create an Auto Scaling Group.
- [ ] Deploy multiple application instances.
- [ ] Test load distribution.
- [ ] Test scaling policies.

## Assignment 6: CDN and DNS

- [ ] Configure a Route 53 hosted zone.
- [ ] Configure DNS records.
- [ ] Create a CloudFront distribution.
- [ ] Connect CloudFront to a private S3 origin.
- [ ] Configure Origin Access Control.
- [ ] Test content delivery.

## Assignment 7: Monitoring and Security

- [ ] Configure CloudWatch metrics.
- [ ] Configure CloudWatch Logs.
- [ ] Create CloudWatch alarms.
- [ ] Explore CloudTrail.
- [ ] Store application secrets in Secrets Manager.
- [ ] Review IAM permissions.
- [ ] Configure database backups.
- [ ] Test a database restoration procedure.

## Assignment 8: Terraform

- [ ] Install Terraform.
- [ ] Configure AWS provider.
- [ ] Create an S3 bucket using Terraform.
- [ ] Create a VPC using Terraform.
- [ ] Create subnets using Terraform.
- [ ] Create Security Groups.
- [ ] Run `terraform plan`.
- [ ] Run `terraform apply`.
- [ ] Configure remote state.
- [ ] Organize infrastructure into reusable modules.

**Cost warning:** AWS services can incur charges even when used for learning. Use small configurations, review pricing before provisioning, configure budgets, and destroy resources when finished. Remember that deleting an EC2 instance does not necessarily delete associated EBS volumes, snapshots, load balancers or other billable resources.

---

# 27. Quick Revision Notes

| Topic | Key Point |
|---|---|
| Cloud Computing | Computing resources delivered over the internet |
| IaaS | Infrastructure as a Service |
| PaaS | Platform as a Service |
| SaaS | Software as a Service |
| AWS Region | Geographical AWS area |
| Availability Zone | Isolated location within a Region |
| IAM | Identity and access management |
| EC2 | Virtual servers |
| AMI | Machine image used to launch EC2 |
| EBS | Block storage |
| VPC | Isolated virtual network |
| Subnet | IP range within a VPC |
| Internet Gateway | Connects VPC to the internet |
| NAT Gateway | Outbound internet access for private IPv4 resources |
| Route Table | Controls traffic routing |
| Security Group | Stateful network access control |
| Network ACL | Stateless subnet-level network access control |
| ALB | Application-layer load balancing |
| NLB | Network-layer load balancing |
| Auto Scaling | Automatically adjusts EC2 capacity |
| S3 | Object storage |
| RDS | Managed relational database |
| Multi-AZ | Database availability and failover |
| Read Replica | Read scaling |
| ElastiCache | Managed in-memory caching |
| Route 53 | DNS |
| CloudFront | CDN |
| Lambda | Serverless function execution |
| CloudWatch | Monitoring and logs |
| CloudTrail | AWS API activity auditing |
| Secrets Manager | Managed secrets |
| Terraform | Infrastructure as Code |
| Well-Architected | Six-pillar cloud architecture framework |

---

# 28. Phase 38 Completion Checklist

After studying this phase, you should be able to explain:

- [ ] What cloud computing is.
- [ ] The differences between IaaS, PaaS and SaaS.
- [ ] Public, private and hybrid cloud.
- [ ] AWS Regions, Availability Zones and Edge Locations.
- [ ] IAM users, groups, roles and policies.
- [ ] EC2 instances, AMIs, EBS and key pairs.
- [ ] VPC, CIDR, subnets and route tables.
- [ ] Internet Gateway and NAT Gateway.
- [ ] Security Groups vs Network ACLs.
- [ ] Load Balancers and Auto Scaling Groups.
- [ ] S3 buckets, objects and presigned URLs.
- [ ] RDS, Multi-AZ and Read Replicas.
- [ ] MongoDB Atlas connectivity.
- [ ] ElastiCache and Redis.
- [ ] Route 53 and DNS records.
- [ ] CloudFront and CDN.
- [ ] Lambda and serverless computing.
- [ ] CloudWatch and CloudTrail.
- [ ] Secrets Manager and Parameter Store.
- [ ] Terraform fundamentals.
- [ ] Production AWS architecture.
- [ ] AWS cost optimization.
- [ ] AWS Well-Architected Framework.

---

# 29. Official Learning Resources

Use these resources to deepen your understanding:

| Resource | Documentation |
|---|---|
| AWS Fundamentals | [AWS Documentation](https://docs.aws.amazon.com/?utm_source=chatgpt.com) |
| AWS Architecture | [AWS Architecture Center](https://aws.amazon.com/architecture/?utm_source=chatgpt.com) |
| AWS Training | [AWS Skill Builder](https://skillbuilder.aws/?utm_source=chatgpt.com) |
| AWS IAM | [IAM Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html?utm_source=chatgpt.com) |
| Amazon EC2 | [EC2 Documentation](https://docs.aws.amazon.com/ec2/?utm_source=chatgpt.com) |
| Amazon VPC | [VPC Documentation](https://docs.aws.amazon.com/vpc/?utm_source=chatgpt.com) |
| Amazon S3 | [S3 Documentation](https://docs.aws.amazon.com/s3/?utm_source=chatgpt.com) |
| Amazon RDS | [RDS Documentation](https://docs.aws.amazon.com/rds/?utm_source=chatgpt.com) |
| Amazon CloudFront | [CloudFront Documentation](https://docs.aws.amazon.com/cloudfront/?utm_source=chatgpt.com) |
| Terraform | [Terraform Documentation](https://developer.hashicorp.com/terraform/docs?utm_source=chatgpt.com) |
| MongoDB Atlas | [MongoDB Atlas Documentation](https://www.mongodb.com/docs/atlas/?utm_source=chatgpt.com) |

---

# 30. What Comes Next?

## Phase 39 – Advanced Networking, DNS, TLS, Reverse Proxies and Network Security

In the next phase, we will study networking concepts in greater depth:

1. Computer Networking Fundamentals
2. OSI Model and TCP/IP Model
3. IP Addresses and Subnetting
4. Public vs Private IP Addresses
5. TCP vs UDP
6. HTTP vs HTTPS
7. DNS Resolution Process
8. DNS Records and DNS Propagation
9. Domain Names and Nameservers
10. TLS/SSL Handshake
11. Digital Certificates and Certificate Authorities
12. Reverse Proxy Architecture
13. Nginx Advanced Configuration
14. Forward Proxy vs Reverse Proxy
15. Load Balancing Algorithms
16. WebSockets and Network Proxies
17. Firewalls and Network Security
18. Ports, Sockets and Network Interfaces
19. NAT and Port Forwarding
20. Network Troubleshooting Tools
21. Production Network Architecture

**Phase 38 completed.**

When you're ready, say **"OK, Phase 39"** to continue.