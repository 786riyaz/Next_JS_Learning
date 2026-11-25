# Phase 44 – Advanced Cloud Networking, AWS VPC, DNS, Load Balancing and CDN

**Level:** Advanced  
**Domain:** Cloud Computing, AWS Networking and DevOps  
**Primary Cloud Platform:** Amazon Web Services (AWS)

---

# 1. Learning Objectives

By the end of this phase, you should be able to:

1. Understand computer networking fundamentals.
2. Work with IPv4, IPv6, CIDR notation and subnetting.
3. Design public, private and isolated subnets.
4. Understand AWS VPC architecture.
5. Configure Internet Gateways, NAT Gateways and route tables.
6. Understand Security Groups and Network ACLs.
7. Connect multiple VPCs using VPC Peering and Transit Gateway.
8. Access AWS services privately using VPC Endpoints.
9. Understand DNS and DNS record types.
10. Configure domains and DNS routing using Amazon Route 53.
11. Understand load balancing and AWS Elastic Load Balancing.
12. Configure Application Load Balancers and Network Load Balancers.
13. Understand SSL/TLS certificates and HTTPS.
14. Understand CDN architecture and Amazon CloudFront.
15. Protect applications using AWS WAF and AWS Shield.
16. Troubleshoot common cloud networking problems.
17. Design production-ready AWS network architectures.

---

# PART 1: COMPUTER NETWORKING FUNDAMENTALS

# 2. What Is Computer Networking?

Computer networking is the process of connecting multiple devices so they can communicate and exchange data.

Examples:

- Your laptop communicating with a web server.
- A frontend application communicating with a backend API.
- A backend server communicating with a database.
- Two AWS VPCs communicating with each other.
- A browser downloading images from a CDN.

Consider a typical web application:

```text
User Browser
     |
     | HTTP Request
     |
     v
Next.js Frontend
     |
     | API Request
     |
     v
Node.js Backend
     |
     | SQL Query
     |
     v
MySQL Database
```

Every communication between these components depends on networking.

## 2.1 Important Networking Terms

| Term | Meaning |
|---|---|
| Network | A group of connected devices |
| Host | A device connected to a network |
| Client | A device or application requesting a service |
| Server | A device or application providing a service |
| IP Address | A logical address identifying a network interface |
| MAC Address | A link-layer hardware address |
| Port | A number identifying a network service |
| Packet | A network-layer unit of data |
| Frame | A data-link-layer unit of data |
| Protocol | A set of communication rules |
| Router | A device that forwards packets between networks |
| Switch | A device that forwards frames within a local network |
| Firewall | A system that controls network traffic |

---

# 3. OSI Model

The OSI model describes network communication using seven conceptual layers.

OSI stands for **Open Systems Interconnection**.

| Layer | Name | Responsibility | Examples |
|---|---|---|---|
| 7 | Application | Application-level communication | HTTP, DNS, SMTP |
| 6 | Presentation | Data representation and encryption | TLS concepts, encoding |
| 5 | Session | Session management | Session control |
| 4 | Transport | End-to-end communication | TCP, UDP |
| 3 | Network | Logical addressing and routing | IP, routers |
| 2 | Data Link | Local network delivery | Ethernet, MAC |
| 1 | Physical | Transmission of raw bits | Cables, radio signals |

### Example: Opening a website

When you open:

```text
https://example.com
```

The communication involves several layers:

```text
Application:
HTTP Request

Transport:
TCP Connection

Network:
IP Addressing

Data Link:
Ethernet / Wi-Fi Frames

Physical:
Electrical or radio signals
```

The receiving server processes the data in the opposite direction.

This process is called **encapsulation and decapsulation**.

## 3.1 TCP/IP Model

In real-world networking, the TCP/IP model is commonly used.

| TCP/IP Layer | Related OSI Layers | Examples |
|---|---|---|
| Application | 5, 6, 7 | HTTP, HTTPS, DNS |
| Transport | 4 | TCP, UDP |
| Internet | 3 | IPv4, IPv6 |
| Network Access | 1, 2 | Ethernet, Wi-Fi |

You should understand both models, but focus more on TCP/IP when working with cloud infrastructure.

---

# 4. TCP vs UDP

TCP and UDP are transport-layer protocols.

## 4.1 TCP – Transmission Control Protocol

TCP provides reliable, ordered communication.

Features:

- Connection-oriented.
- Reliable data delivery.
- Error detection.
- Retransmission of lost data.
- Ordered delivery.
- Flow and congestion control.

Examples:

- HTTPS.
- HTTP/1.1 and HTTP/2 over TCP.
- SSH.
- MySQL connections.

### TCP Three-Way Handshake

Before exchanging application data, TCP establishes a connection.

```text
Client                         Server
  |                               |
  | -------- SYN ---------------> |
  |                               |
  | <------- SYN-ACK ------------ |
  |                               |
  | -------- ACK ---------------> |
  |                               |
  |        Connection Established |
```

The three steps are:

1. SYN: Client requests a connection.
2. SYN-ACK: Server acknowledges and responds.
3. ACK: Client acknowledges the response.

## 4.2 UDP – User Datagram Protocol

UDP is connectionless and does not guarantee delivery or ordering.

Features:

- No connection establishment.
- Lower protocol overhead.
- No built-in retransmission.
- No guaranteed ordering.

Examples:

- DNS queries commonly use UDP.
- Voice and video communication.
- Online gaming.
- Real-time streaming.

### TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Reliability | Yes | Not guaranteed |
| Ordering | Yes | Not guaranteed |
| Retransmission | Built in | Not built in |
| Overhead | Higher | Lower |
| Common use | HTTPS, SSH | DNS, real-time traffic |

**Important:** UDP is not automatically faster in every situation. Its suitability depends on application requirements and network conditions.

---

# 5. IP Addressing

An IP address identifies a network interface at the Internet Protocol layer.

There are two major IP versions:

1. IPv4
2. IPv6

## 5.1 IPv4

IPv4 uses 32-bit addresses.

Example:

```text
192.168.1.10
```

An IPv4 address contains four octets.

```text
192 . 168 . 1 . 10
```

Each octet contains 8 bits.

Therefore:

```text
8 + 8 + 8 + 8 = 32 bits
```

Each octet can represent values from:

```text
0 to 255
```

## 5.2 IPv6

IPv6 uses 128-bit addresses.

Example:

```text
2001:db8:abcd:0012:0000:0000:0000:0001
```

IPv6 was introduced primarily to provide a much larger address space.

It also provides other improvements in addressing and network operation.

---

# 6. Public IP vs Private IP

## 6.1 Public IP Address

A public IP address is globally routable on the internet.

Example:

```text
8.8.8.8
```

A public-facing web server may use a public IP address so internet users can reach it.

## 6.2 Private IP Address

Private IP addresses are used inside private networks.

The RFC 1918 IPv4 private ranges are:

| Range | CIDR |
|---|---|
| 10.0.0.0 – 10.255.255.255 | `10.0.0.0/8` |
| 172.16.0.0 – 172.31.255.255 | `172.16.0.0/12` |
| 192.168.0.0 – 192.168.255.255 | `192.168.0.0/16` |

Example:

```text
10.0.1.10
192.168.1.20
172.16.5.10
```

These addresses are not directly routable across the public internet.

### Real-world example

Suppose you deploy an application on AWS.

```text
Internet
   |
   v
Public Load Balancer
   |
   v
Private Application Server
   |
   v
Private MySQL Database
```

You might configure:

| Resource | IP Address Type |
|---|---|
| Load Balancer | Public-facing |
| Application Server | Private |
| Database Server | Private |

This reduces direct internet exposure.

---

# PART 2: CIDR AND SUBNETTING

# 7. What Is CIDR?

CIDR stands for **Classless Inter-Domain Routing**.

CIDR notation represents an IP network and its prefix length.

Example:

```text
192.168.1.0/24
```

Here:

- `192.168.1.0` is the network address.
- `/24` represents the network prefix length.
- The remaining 8 bits represent host addresses.

IPv4 contains 32 bits.

Therefore:

```text
Host bits = 32 - Prefix length
```

For `/24`:

```text
32 - 24 = 8 host bits
```

Total addresses:

```text
2^8 = 256
```

## 7.1 CIDR Examples

| CIDR | Total IPv4 Addresses | Traditional Usable Host Addresses |
|---|---:|---:|
| `/16` | 65,536 | 65,534 |
| `/20` | 4,096 | 4,094 |
| `/24` | 256 | 254 |
| `/25` | 128 | 126 |
| `/26` | 64 | 62 |
| `/27` | 32 | 30 |
| `/28` | 16 | 14 |

The traditional usable-host calculation excludes the network and broadcast addresses.

**AWS-specific exception:** AWS reserves five IPv4 addresses in every ordinary subnet, so the usable capacity differs from the traditional calculation.

For example, an AWS `/24` subnet has:

```text
256 total addresses
- 5 AWS-reserved addresses
= 251 usable addresses
```

AWS documents the five reserved IPv4 addresses, including the network address, VPC router address, DNS address, future-use address and broadcast address. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html?utm_source=chatgpt.com)

---

# 8. Understanding Subnet Masks

A subnet mask determines which bits represent the network portion and which represent the host portion.

Example:

```text
IP Address: 192.168.1.10
CIDR:       /24
Subnet Mask: 255.255.255.0
```

Binary representation:

```text
11111111.11111111.11111111.00000000
```

The first 24 bits represent the network.

The remaining 8 bits represent host addresses.

## 8.1 Common CIDR and Subnet Masks

| CIDR | Subnet Mask |
|---|---|
| `/8` | 255.0.0.0 |
| `/16` | 255.255.0.0 |
| `/24` | 255.255.255.0 |
| `/25` | 255.255.255.128 |
| `/26` | 255.255.255.192 |
| `/27` | 255.255.255.224 |
| `/28` | 255.255.255.240 |

---

# 9. Subnetting Practical Examples

Suppose you have:

```text
Network: 192.168.1.0/24
```

You want to divide it into four equal subnets.

A `/24` network contains 256 addresses.

Four equal subnets require:

```text
256 / 4 = 64 addresses per subnet
```

A subnet containing 64 addresses uses a `/26` prefix.

The resulting subnets are:

| Subnet | CIDR | Address Range |
|---|---|---|
| 1 | `192.168.1.0/26` | 192.168.1.0 – 192.168.1.63 |
| 2 | `192.168.1.64/26` | 192.168.1.64 – 192.168.1.127 |
| 3 | `192.168.1.128/26` | 192.168.1.128 – 192.168.1.191 |
| 4 | `192.168.1.192/26` | 192.168.1.192 – 192.168.1.255 |

Each subnet has 64 total addresses.

For a traditional IPv4 network, 62 are usable host addresses.

For an AWS subnet, five addresses are reserved, leaving 59 usable IPv4 addresses.

### Practical AWS CIDR Example

Suppose you create an AWS VPC:

```text
10.0.0.0/16
```

You can divide it into:

```text
VPC: 10.0.0.0/16

Public Subnet 1:  10.0.1.0/24
Public Subnet 2:  10.0.2.0/24

Private Subnet 1: 10.0.11.0/24
Private Subnet 2: 10.0.12.0/24

Database Subnet 1: 10.0.21.0/24
Database Subnet 2: 10.0.22.0/24
```

This gives you a structured network design.

**Important:** Subnet CIDR ranges inside the same VPC must not overlap. Plan CIDR ranges carefully, especially when you expect to connect multiple VPCs later. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html?utm_source=chatgpt.com)

---

# PART 3: AWS VPC ARCHITECTURE

# 10. What Is AWS VPC?

VPC stands for **Virtual Private Cloud**.

An AWS VPC is a logically isolated virtual network in which you launch AWS resources.

You control:

- IP address ranges.
- Subnets.
- Route tables.
- Internet connectivity.
- Security Groups.
- Network ACLs.
- VPC endpoints.
- Network connections to other VPCs.

Example:

```text
AWS Account
    |
    v
VPC: 10.0.0.0/16
    |
    +---------------------------+
    |                           |
    v                           v
Public Subnet              Private Subnet
    |                           |
    v                           v
Application LB             Node.js Server
                                |
                                v
                            MySQL RDS
```

VPC is the foundation of AWS network architecture.

Official documentation: [Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/?utm_source=chatgpt.com)

## 10.1 Important VPC Concepts

| Concept | Meaning |
|---|---|
| VPC | Isolated virtual network |
| Region | Geographic AWS area |
| Availability Zone | Isolated location within a Region |
| Subnet | IP address range inside a VPC |
| Route Table | Controls traffic routing |
| Internet Gateway | Enables internet connectivity for public IPv4 routes |
| NAT Gateway | Enables outbound IPv4 internet access from private subnets |
| Security Group | Stateful network firewall |
| Network ACL | Stateless subnet-level firewall |
| VPC Endpoint | Private connectivity to supported services |

## 10.2 VPC Scope

A VPC belongs to a specific AWS Region.

Subnets belong to individual Availability Zones.

Example:

```text
AWS Region: ap-south-1

VPC: 10.0.0.0/16
    |
    +-------------------------------+
    |                               |
    v                               v
Availability Zone A             Availability Zone B
    |                               |
    v                               v
Public Subnet A                 Public Subnet B
Private Subnet A                Private Subnet B
Database Subnet A               Database Subnet B
```

This design improves availability because the application can operate across multiple Availability Zones.

---

# 11. Public, Private and Isolated Subnets

A subnet's classification depends primarily on its routing configuration.

## 11.1 Public Subnet

A public subnet has a route to an Internet Gateway.

Example route:

```text
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         igw-123456
```

The default route sends IPv4 internet-bound traffic to the Internet Gateway.

However, having a public subnet does not automatically make every resource inside it accessible from the internet.

A resource generally also needs:

- A suitable public IPv4 address or another public addressing arrangement.
- A Security Group permitting the required traffic.
- A suitable Network ACL configuration.
- An application listening on the correct port.

## 11.2 Private Subnet

A private subnet does not have a direct route to an Internet Gateway.

It may use a NAT Gateway for outbound IPv4 internet access.

Example:

```text
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         nat-123456
```

Typical resources:

- Node.js backend.
- Next.js application servers.
- Worker servers.
- Internal services.

## 11.3 Isolated Subnet

An isolated subnet has no route to an Internet Gateway or NAT Gateway.

Typical resources:

- Database servers.
- Sensitive internal systems.
- Restricted workloads.

An isolated subnet can still communicate with other permitted VPC resources using local routes and applicable security controls.

### Comparison

| Feature | Public | Private | Isolated |
|---|---|---|---|
| Direct Internet Gateway route | Yes | No | No |
| NAT Gateway route | Optional | Optional | No |
| Publicly reachable resources | Possible | Not directly through IGW | Not directly through IGW |
| Typical resources | ALB, bastion | App servers | Databases |

**Important:** A subnet does not become public simply because its name contains the word `public`. Its route table determines its internet routing behavior. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/route-table-options.html?utm_source=chatgpt.com)

---

# 12. Internet Gateway (IGW)

An Internet Gateway enables communication between a VPC and the internet when the necessary routing and addressing requirements are satisfied.

Example:

```text
Internet
   |
   v
Internet Gateway
   |
   v
VPC
   |
   v
Public Subnet
   |
   v
EC2 Instance
```

## 12.1 How to Enable Internet Access

You need to:

1. Create an Internet Gateway.
2. Attach it to your VPC.
3. Create or update the public subnet route table.
4. Add a default route to the Internet Gateway.
5. Configure the resource's public addressing.
6. Configure Security Groups and Network ACLs.

Example:

```text
Destination: 0.0.0.0/0
Target:      igw-123456
```

For IPv6, a route may use:

```text
Destination: ::/0
Target:      igw-123456
```

An Internet Gateway alone does not make a subnet internet-accessible. The subnet's route table must direct traffic to it. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-igw.html?utm_source=chatgpt.com)

---

# 13. NAT Gateway

NAT stands for **Network Address Translation**.

A NAT Gateway allows resources in private subnets to initiate outbound IPv4 connections to external networks without allowing unsolicited internet connections to those private resources.

### Example

Suppose your Node.js backend runs in a private subnet.

It needs to:

- Download operating-system updates.
- Install packages.
- Call an external payment API.
- Connect to an external service.

But you do not want the backend server to have direct public internet access through an Internet Gateway.

You can use a NAT Gateway.

```text
                    Internet
                       |
                       |
                 Internet Gateway
                       |
                       |
                  NAT Gateway
                       |
                       |
                Private Route Table
                       |
                       |
                Private Subnet
                       |
                       |
                  Node.js Server
```

The private server initiates a connection.

The NAT Gateway translates its private source address for outbound IPv4 communication.

Return traffic is allowed for the established connection.

Unsolicited inbound connections from the internet are not forwarded to the private server through the NAT Gateway.

## 13.1 NAT Gateway Configuration

Typical architecture:

```text
VPC: 10.0.0.0/16

Public Subnet:
    NAT Gateway
    Route: 0.0.0.0/0 -> IGW

Private Subnet:
    Node.js Server
    Route: 0.0.0.0/0 -> NAT Gateway
```

## 13.2 NAT Gateway vs Internet Gateway

| Feature | Internet Gateway | NAT Gateway |
|---|---|---|
| Purpose | Internet connectivity | Outbound NAT connectivity |
| Typical placement | Attached to VPC | Public subnet |
| Used by | Public subnet resources | Private subnet resources |
| Allows unsolicited inbound connections to private resources | Not applicable as a NAT service | No |
| Requires public addressing | Resource needs appropriate public addressing for direct IPv4 internet access | NAT Gateway uses an Elastic IP for public NAT |

## 13.3 NAT Gateway vs NAT Instance

| Feature | NAT Gateway | NAT Instance |
|---|---|---|
| Type | Managed AWS service | EC2 instance |
| Maintenance | AWS-managed | User-managed |
| Scaling | Managed scaling | User-managed |
| Availability | Design across AZs | Design and manage failover |
| Administration | Lower | Higher |
| Operational responsibility | Lower | Higher |

### Production Consideration

A NAT Gateway is generally deployed per Availability Zone for resilient architectures.

For example:

```text
AZ-A:
Private Subnet A -> NAT Gateway A

AZ-B:
Private Subnet B -> NAT Gateway B
```

This avoids making private subnet outbound connectivity depend on a single NAT Gateway in another Availability Zone.

NAT Gateways incur charges. Consider VPC endpoints for supported AWS services to reduce unnecessary NAT processing and data-transfer costs.

---

# 14. Route Tables

A route table controls where network traffic from a subnet or gateway is directed.

Each route contains:

- Destination.
- Target.

Example:

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | Internet Gateway |
| `192.168.0.0/16` | VPC Peering |

The `local` route allows communication within the VPC's associated CIDR ranges.

## 14.1 Longest Prefix Match

When multiple routes match a destination, AWS uses the most specific matching route.

Example:

```text
0.0.0.0/0       -> NAT Gateway
10.20.0.0/16    -> VPC Peering
10.20.5.0/24    -> Specific Network Target
```

If traffic is going to:

```text
10.20.5.10
```

The `/24` route is more specific than `/16` and `/0`.

Therefore, the `/24` route takes precedence.

This is called **Longest Prefix Match**. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-route-tables.html?utm_source=chatgpt.com)

## 14.2 Route Table Example

Suppose you have:

```text
VPC CIDR: 10.0.0.0/16
```

Public subnet route table:

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | Internet Gateway |

Private subnet route table:

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | NAT Gateway |

Database subnet route table:

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |

---

# PART 4: NETWORK SECURITY

# 15. Security Groups

A Security Group is a stateful virtual firewall associated with resources such as EC2 network interfaces.

It controls inbound and outbound traffic.

### Example

Suppose your Node.js backend listens on port `3000`.

You want:

- The load balancer to access the backend on port 3000.
- The public internet to be unable to access the backend directly.

You can configure:

```text
Inbound Rules:

Type: Custom TCP
Port: 3000
Source: Application Load Balancer Security Group
```

This allows traffic from the load balancer's Security Group.

### Common Ports

| Port | Protocol | Purpose |
|---:|---|---|
| 22 | TCP | SSH |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 3000 | TCP | Node.js development/default app port |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 27017 | TCP | MongoDB |
| 6379 | TCP | Redis |

Do not expose these ports publicly without a specific, justified requirement.

For example, MySQL should generally not be accessible from:

```text
0.0.0.0/0
```

Instead, permit database access from the application server's Security Group.

## 15.1 Stateful Firewall

Security Groups are stateful.

Suppose:

1. Your application server sends a request to a database.
2. The database sends a response.
3. The response traffic is automatically allowed by the stateful connection tracking behavior.

You do not need to create a separate inbound rule for every response packet.

---

# 16. Network ACLs

Network ACL stands for **Network Access Control List**.

A Network ACL is a stateless firewall associated with a subnet.

It controls inbound and outbound traffic at the subnet level.

Features:

- Supports allow rules.
- Supports deny rules.
- Uses numbered rules.
- Evaluates rules in ascending numerical order.
- Is stateless.

Example:

```text
Inbound:

Rule 100: Allow TCP 443
Rule 110: Allow TCP 80
Rule 120: Deny all other traffic
```

Because Network ACLs are stateless, return traffic must also be allowed by the appropriate outbound or inbound rules.

This is particularly important for ephemeral ports used by client connections.

## 16.1 Security Group vs Network ACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Network interface/resource | Subnet |
| Stateful | Yes | No |
| Allow rules | Yes | Yes |
| Deny rules | No explicit deny rules | Yes |
| Rule evaluation | All applicable rules combined | Numbered order |
| Return traffic | Automatically permitted for tracked connections | Must be explicitly allowed |
| Common use | Primary resource firewall | Additional subnet-level control |

### Best Practice

Use Security Groups as your primary network access control mechanism.

Use Network ACLs as an additional layer when subnet-level filtering is required.

AWS also recommends this general security approach in its VPC security guidance. [AWS Documentation](https://docs.aws.amazon.com/en_en/vpc/latest/userguide/infrastructure-security.html?utm_source=chatgpt.com)

---

# 17. VPC Peering

VPC Peering connects two VPCs so their resources can communicate using private IP addresses.

Example:

```text
VPC A                         VPC B
10.0.0.0/16                   10.1.0.0/16
   |                             |
   v                             v
EC2 Server A  <------------>  EC2 Server B
```

You can use VPC Peering to connect:

- Development and shared-services VPCs.
- Application and analytics VPCs.
- Two separate AWS environments.

## 17.1 Important Characteristics

1. VPC CIDR ranges must not overlap.
2. A peering connection must be accepted by the other VPC owner when applicable.
3. Route tables must be updated on both sides.
4. Security Groups and Network ACLs must permit the traffic.
5. VPC Peering is not transitive.

### What Does Non-Transitive Mean?

Suppose:

```text
VPC A <----> VPC B
VPC B <----> VPC C
```

VPC A cannot automatically communicate with VPC C through VPC B.

You need another supported network design.

---

# 18. AWS Transit Gateway

AWS Transit Gateway provides centralized connectivity between multiple VPCs and supported external networks.

It is commonly used in hub-and-spoke architectures.

### Without Transit Gateway

```text
VPC A <----> VPC B
  |              |
  |              |
  +------------ VPC C
                 |
                 |
               VPC D
```

As the number of VPCs grows, managing multiple individual peering connections becomes complicated.

### With Transit Gateway

```text
             VPC A
                |
                |
VPC B ---- Transit Gateway ---- VPC C
                |
                |
             VPC D
```

The Transit Gateway acts as a central routing hub.

## 18.1 VPC Peering vs Transit Gateway

| Feature | VPC Peering | Transit Gateway |
|---|---|---|
| Architecture | Direct connection | Central hub |
| Transitive routing | No | Supported through routing configuration |
| Suitable for | Small number of VPCs | Many VPCs |
| Centralized management | Limited | Yes |
| Cost | Connection and data-transfer considerations | Attachment and data-processing charges may apply |

Use VPC Peering for straightforward point-to-point connectivity.

Use Transit Gateway when your network requires centralized, scalable routing.

Official reference: [AWS Transit Gateway Documentation](https://docs.aws.amazon.com/vpc/latest/tgw/?utm_source=chatgpt.com)

---

# 19. VPC Endpoints

A VPC Endpoint allows resources in your VPC to connect to supported AWS services without requiring a public internet path.

Example:

Your application server runs inside a private subnet.

It needs to access Amazon S3.

Without a VPC endpoint, the application might use a NAT Gateway to reach S3's public service endpoint.

With a suitable VPC endpoint, traffic can use private AWS networking.

```text
Private EC2 Instance
        |
        v
   VPC Endpoint
        |
        v
     Amazon S3
```

## 19.1 Types of VPC Endpoints

There are two major categories relevant to this architecture.

### A. Gateway Endpoint

Gateway endpoints are available for:

- Amazon S3.
- Amazon DynamoDB.

They are configured through route tables.

They do not require an Internet Gateway or NAT Gateway for access to those supported services.

AWS does not charge an additional endpoint usage fee for gateway endpoints, although other applicable service and data-transfer charges may still apply. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/gateway-endpoints.html?utm_source=chatgpt.com)

### B. Interface Endpoint

Interface endpoints use AWS PrivateLink.

They create private network interfaces with private IP addresses in your subnets.

They support private connectivity to many AWS services and supported endpoint services.

Example:

```text
Private EC2
    |
    v
Interface Endpoint
    |
    v
AWS Service
```

Interface endpoints generally have hourly and data-processing charges.

## 19.2 Gateway vs Interface Endpoint

| Feature | Gateway Endpoint | Interface Endpoint |
|---|---|---|
| Technology | Route-table target | PrivateLink |
| Uses ENI | No | Yes |
| Common services | S3, DynamoDB | Many AWS services |
| Configuration | Route tables | Subnets, endpoint policies and DNS |
| Additional endpoint charge | No | Generally yes |

**Important:** S3 and DynamoDB can also support interface endpoints. The gateway and interface endpoint options have different characteristics and pricing. [AWS Documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/gateway-endpoints.html?utm_source=chatgpt.com)

---

# PART 5: DNS AND AMAZON ROUTE 53

# 20. What Is DNS?

DNS stands for **Domain Name System**.

DNS translates domain names into IP addresses.

For example:

```text
google.com
```

is resolved to IP addresses that clients can use to establish network connections.

Without DNS, users would have to remember IP addresses for websites.

### DNS Resolution Flow

```text
User Browser
     |
     | example.com
     |
     v
DNS Resolver
     |
     v
Root DNS Server
     |
     v
TLD DNS Server
     |
     v
Authoritative DNS Server
     |
     v
IP Address
     |
     v
Web Server
```

The actual resolution process can involve caching at several stages, so a resolver may not need to contact every DNS level for every request.

---

# 21. Types of DNS Servers

## 21.1 Recursive DNS Resolver

A recursive resolver performs DNS lookup work on behalf of a client.

Examples include:

- ISP DNS resolvers.
- Google Public DNS.
- Cloudflare DNS.

## 21.2 Root DNS Server

Root DNS servers help direct queries toward the appropriate Top-Level Domain servers.

## 21.3 TLD DNS Server

TLD means Top-Level Domain.

Examples:

```text
.com
.org
.net
.in
```

A TLD server directs the resolver toward the authoritative DNS servers for a domain.

## 21.4 Authoritative DNS Server

An authoritative DNS server stores or serves the DNS records for a domain.

For example, it can provide the IP address associated with:

```text
api.example.com
```

---

# 22. DNS Record Types

DNS records provide different types of information about a domain.

| Record | Purpose | Example |
|---|---|---|
| A | Maps a name to an IPv4 address | `example.com -> 192.0.2.10` |
| AAAA | Maps a name to an IPv6 address | IPv6 address |
| CNAME | Maps one DNS name to another DNS name | `www -> example.com` |
| MX | Specifies mail servers | Mail provider hostname |
| TXT | Stores text information | SPF verification |
| NS | Specifies authoritative name servers | DNS provider |
| PTR | Reverse DNS lookup | IP to hostname |
| SRV | Identifies a service endpoint | Service, protocol and port |
| CAA | Specifies permitted certificate authorities | CA restriction |

## 22.1 A Record

Example:

```text
Type: A
Name: api.example.com
Value: 203.0.113.10
```

It maps the domain to an IPv4 address.

## 22.2 AAAA Record

Maps a domain to an IPv6 address.

## 22.3 CNAME Record

Example:

```text
Name: www.example.com
Type: CNAME
Value: example.com
```

It makes one DNS name an alias of another DNS name.

A CNAME generally cannot coexist with other record types at the same DNS name.

For example, a zone apex often requires an alias mechanism rather than a conventional CNAME.

## 22.4 MX Record

MX records specify which mail servers receive email for a domain.

## 22.5 TXT Record

TXT records store text data.

Common uses:

- Domain verification.
- SPF.
- DKIM.
- DMARC.
- Service verification.

## 22.6 TTL

TTL stands for **Time To Live**.

It specifies how long DNS responses can be cached.

Example:

```text
TTL: 300 seconds
```

A resolver may cache the record for five minutes.

A shorter TTL can make DNS changes visible sooner after cached records expire, but it can also increase DNS query volume.

**Important:** DNS changes are not always instantly visible. Existing cached records can remain in use until their TTL expires.

---

# 23. DNS Troubleshooting Commands

These commands are useful when diagnosing DNS issues.

### Windows

```powershell
nslookup example.com
```

```powershell
ipconfig /displaydns
```

```powershell
ipconfig /flushdns
```

### Linux

```bash
dig example.com
```

```bash
dig example.com A
```

```bash
dig example.com MX
```

```bash
dig example.com NS
```

```bash
host example.com
```

Example:

```bash
dig api.example.com
```

This helps you inspect DNS resolution results.

---

# 24. What Is Amazon Route 53?

Amazon Route 53 is AWS's managed DNS and domain registration service.

It supports:

- DNS hosting.
- Domain registration.
- DNS record management.
- Health checks.
- DNS-based routing policies.
- Integration with AWS services.

Official documentation: [Amazon Route 53 Developer Guide](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html?utm_source=chatgpt.com)

## 24.1 Hosted Zones

A hosted zone contains DNS records for a domain.

There are two main types.

### Public Hosted Zone

Used to manage DNS records that are resolvable through public DNS.

Example:

```text
example.com
```

You might create:

```text
example.com       -> Application Load Balancer
www.example.com   -> CloudFront
api.example.com   -> Application Load Balancer
```

### Private Hosted Zone

Used to manage DNS records for domains that should be resolved only within associated VPCs.

Example:

```text
internal.example.com
```

Records might include:

```text
database.internal.example.com
redis.internal.example.com
```

These names can be used by internal resources in associated VPCs.

## 24.2 Domain Registration vs DNS Hosting

These are different concepts.

| Concept | Responsibility |
|---|---|
| Domain Registration | Reserves ownership/registration of a domain |
| DNS Hosting | Manages DNS records for that domain |

You can register a domain with one provider and host its DNS records with another provider.

---

# 25. Route 53 Routing Policies

Route 53 supports multiple routing policies.

These policies determine how DNS queries are answered.

## 25.1 Simple Routing

Routes traffic to a single resource or a simple group of values.

Example:

```text
example.com -> One application endpoint
```

## 25.2 Weighted Routing

Distributes DNS responses according to configured weights.

Example:

```text
Version A: Weight 90
Version B: Weight 10
```

This can be useful for gradual traffic migration.

However, DNS caching means the actual request distribution may not exactly match the configured percentages.

## 25.3 Latency-Based Routing

Routes users toward the AWS Region that Route 53 determines can provide the lowest latency.

Example:

```text
User in India
     |
     v
Mumbai Region

User in Europe
     |
     v
Frankfurt Region
```

## 25.4 Failover Routing

Used for active-passive architectures.

Example:

```text
Primary Endpoint
       |
       | Healthy?
       |
       +---- Yes ----> Primary
       |
       +---- No -----> Secondary
```

## 25.5 Geolocation Routing

Routes traffic based on the geographic location associated with the DNS query.

Example:

```text
India Users  -> India Endpoint
US Users     -> US Endpoint
Europe Users -> Europe Endpoint
```

## 25.6 Geoproximity Routing

Routes traffic based on the geographic location of users and resources.

It supports a bias value that can expand or shrink the geographic area served by a resource.

## 25.7 Multivalue Answer Routing

Returns multiple healthy records, up to the supported limit, in response to DNS queries.

## 25.8 IP-Based Routing

Routes traffic based on client IP ranges that you configure.

## 25.9 AZ-Affinity Routing

Route 53 also supports Availability Zone affinity routing for supported configurations.

### Routing Policy Comparison

| Policy | Main Purpose |
|---|---|
| Simple | Single resource |
| Weighted | Traffic distribution by weight |
| Latency | Lowest-latency Region |
| Failover | Active-passive recovery |
| Geolocation | User geographic location |
| Geoproximity | User/resource geographic proximity |
| Multivalue | Multiple healthy records |
| IP-Based | Client IP ranges |
| AZ-Affinity | Availability Zone affinity |

Route 53 documents these routing policies and their use cases. [AWS Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html?utm_source=chatgpt.com)

---

# PART 6: LOAD BALANCING

# 26. What Is Load Balancing?

Load balancing distributes incoming traffic across multiple servers or application instances.

Suppose you have one Node.js backend.

```text
User Requests
     |
     v
Node.js Server
```

If thousands of users access the application simultaneously, one server may become overloaded.

You can deploy multiple backend instances:

```text
                  Users
                    |
                    v
               Load Balancer
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Server A  Server B  Server C
```

The load balancer distributes requests across available targets.

Benefits:

- Better availability.
- Improved scalability.
- Health-based traffic distribution.
- Reduced dependency on a single server.

---

# 27. Layer 4 vs Layer 7 Load Balancing

Load balancers can operate at different network layers.

## 27.1 Layer 4 Load Balancing

Layer 4 operates at the transport layer.

It uses information such as:

- Source IP.
- Destination IP.
- TCP port.
- UDP port.

It does not need to inspect HTTP application content to route a connection.

## 27.2 Layer 7 Load Balancing

Layer 7 operates at the application layer.

It can inspect HTTP information such as:

- Hostname.
- URL path.
- HTTP headers.
- HTTP methods.

Example:

```text
example.com/api/*      -> Backend API
example.com/admin/*    -> Admin Service
example.com/products/* -> Product Service
```

### Comparison

| Feature | Layer 4 | Layer 7 |
|---|---|---|
| OSI Layer | Transport | Application |
| Protocols | TCP, UDP, etc. | HTTP, HTTPS, etc. |
| Path-based routing | No | Yes |
| Host-based routing | No | Yes |
| Application awareness | Limited | Higher |

---

# 28. AWS Elastic Load Balancing (ELB)

AWS Elastic Load Balancing distributes traffic across multiple targets.

It supports:

1. Application Load Balancer (ALB).
2. Network Load Balancer (NLB).
3. Gateway Load Balancer (GWLB).
4. Classic Load Balancer (legacy generation).

For new application architectures, ALB and NLB are the most commonly relevant choices.

Official documentation: [AWS Elastic Load Balancing Documentation](https://docs.aws.amazon.com/elasticloadbalancing/?utm_source=chatgpt.com)

---

# 29. Application Load Balancer (ALB)

An Application Load Balancer operates at Layer 7.

It is designed for HTTP and HTTPS application traffic.

### Example

Suppose you have a backend with multiple services:

```text
api.example.com/users
api.example.com/products
api.example.com/orders
```

You can route requests to different target groups.

```text
                 Application Load Balancer
                           |
              +------------+------------+
              |            |            |
              v            v            v
          /users       /products     /orders
              |            |            |
              v            v            v
          User API     Product API   Order API
```

## 29.1 Important ALB Components

| Component | Meaning |
|---|---|
| Load Balancer | Receives incoming traffic |
| Listener | Listens on a configured port and protocol |
| Listener Rule | Defines how requests should be routed |
| Target Group | Contains backend targets |
| Target | EC2 instance, IP address, Lambda target or other supported target |
| Health Check | Checks target availability |

## 29.2 Listener Example

```text
Listener:
HTTPS

Port:
443

Certificate:
ACM Certificate

Default Action:
Forward to target-group-api
```

Another listener could redirect HTTP traffic:

```text
HTTP:80
   |
   v
Redirect to HTTPS:443
```

## 29.3 Target Groups

A target group contains registered backend targets.

Example:

```text
Target Group: node-backend

Target 1: EC2 Instance A
Target 2: EC2 Instance B
Target 3: EC2 Instance C
```

The load balancer routes requests to healthy targets.

## 29.4 Health Checks

Suppose your Node.js application exposes:

```text
GET /health
```

It returns:

```json
{
  "status": "ok"
}
```

You can configure the target group's health check to use:

```text
Protocol: HTTP
Path: /health
Port: Traffic Port
```

If a target fails health checks, the load balancer can stop routing new requests to it.

**Best practice:** A health endpoint should verify the application's essential operational state without becoming unnecessarily expensive or exposing sensitive internal information.

---

# 30. Network Load Balancer (NLB)

A Network Load Balancer operates primarily at Layer 4.

It is designed for TCP, UDP and TLS traffic.

Common use cases:

- High-performance network traffic.
- TCP-based services.
- UDP-based services.
- Applications requiring static IP addresses.
- Workloads needing Layer 4 load balancing.

Example:

```text
Users
  |
  v
Network Load Balancer
  |
  +---------+---------+
  |         |         |
  v         v         v
Server A  Server B  Server C
```

---

# 31. Gateway Load Balancer (GWLB)

Gateway Load Balancer is designed to deploy and scale virtual network appliances.

Examples:

- Firewalls.
- Intrusion detection systems.
- Intrusion prevention systems.
- Traffic inspection appliances.

It is particularly useful in network security architectures.

---

# 32. ALB vs NLB vs GWLB

| Feature | ALB | NLB | GWLB |
|---|---|---|---|
| Primary Layer | Layer 7 | Layer 4 | Network appliance integration |
| HTTP routing | Yes | No native HTTP path routing | No |
| Path-based routing | Yes | No | No |
| TCP/UDP handling | Application-oriented HTTP/HTTPS use | Yes | Appliance traffic forwarding |
| Typical use | Web applications and APIs | High-performance network services | Firewalls and inspection appliances |

AWS's current ELB documentation describes the different load balancer types and their use cases. [AWS Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/?utm_source=chatgpt.com)

---

# PART 7: SSL, TLS AND HTTPS

# 33. What Is HTTP?

HTTP stands for **Hypertext Transfer Protocol**.

It is used for communication between clients and web servers.

Example:

```http
GET /products HTTP/1.1
Host: example.com
```

HTTP by itself does not provide transport encryption.

## 33.1 What Is HTTPS?

HTTPS is HTTP communication protected by TLS.

TLS stands for **Transport Layer Security**.

It provides:

- Encryption.
- Integrity protection.
- Server authentication through certificates.
- Optional client authentication.

Example:

```text
http://example.com
https://example.com
```

HTTPS is the standard choice for modern production websites and APIs.

---

# 34. What Is an SSL/TLS Certificate?

A TLS certificate helps a client verify the identity of a server.

A certificate commonly contains:

- Domain names.
- Public key.
- Certificate issuer.
- Validity period.
- Digital signature.

Certificates are issued by Certificate Authorities (CAs).

Examples of certificate authorities include:

- Let's Encrypt.
- DigiCert.
- Amazon Trust Services.

## 34.1 TLS Handshake – Simplified

```text
Browser                         Server
   |                               |
   | -------- ClientHello -------> |
   |                               |
   | <------- ServerHello -------- |
   | <------- Certificate -------- |
   |                               |
   | Certificate Validation       |
   |                               |
   | <---- Key Establishment ----> |
   |                               |
   | ===== Encrypted Traffic ===== |
```

The exact handshake depends on the TLS version and selected cryptographic algorithms.

## 34.2 AWS Certificate Manager (ACM)

AWS Certificate Manager helps provision, manage and deploy TLS certificates for supported AWS services.

For example:

```text
User Browser
     |
     | HTTPS
     |
     v
Application Load Balancer
     |
     | HTTP or HTTPS
     |
     v
Node.js Backend
```

You can attach an ACM certificate to the ALB's HTTPS listener.

## 34.3 TLS Termination

TLS termination means that an intermediary, such as an ALB or CloudFront distribution, decrypts incoming TLS traffic.

Example:

```text
Browser
   |
   | HTTPS
   |
   v
ALB
   |
   | HTTP
   |
   v
Backend
```

This is one possible design.

For sensitive environments, you may also encrypt traffic between the load balancer and backend:

```text
Browser
   |
   | HTTPS
   |
   v
ALB
   |
   | HTTPS
   |
   v
Backend
```

Whether to use HTTP or HTTPS internally depends on your security requirements and network trust boundaries.

---

# PART 8: CDN AND AMAZON CLOUDFRONT

# 35. What Is a CDN?

CDN stands for **Content Delivery Network**.

A CDN distributes content through geographically distributed edge locations.

Its purpose is to deliver content to users with lower latency and reduced origin load.

Examples of CDN providers:

- Amazon CloudFront.
- Cloudflare.
- Akamai.

## 35.1 Why Do We Need a CDN?

Suppose your application server is located in Mumbai.

A user in the United States requests an image.

Without a CDN:

```text
US User
   |
   | Long-distance request
   |
   v
Mumbai Server
   |
   v
Image Response
```

With a CDN:

```text
US User
   |
   v
Nearby CDN Edge Location
   |
   v
Cached Image
```

If the requested object is already cached, the CDN can return it without contacting the origin.

Benefits:

- Lower latency.
- Reduced origin traffic.
- Better global content delivery.
- Improved scalability.
- Additional security integration options.

---

# 36. Amazon CloudFront

Amazon CloudFront is AWS's CDN service.

It distributes content through a global network of edge locations.

Official documentation: [Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html?utm_source=chatgpt.com)

## 36.1 Important CloudFront Concepts

| Concept | Meaning |
|---|---|
| Distribution | CloudFront configuration |
| Origin | Source from which CloudFront retrieves content |
| Edge Location | Location serving cached content |
| Regional Edge Cache | Additional caching layer |
| Cache Behavior | Rules controlling how requests are handled |
| Cache Policy | Defines cache key and TTL behavior |
| Origin Request Policy | Controls information forwarded to origin |
| Invalidation | Removes or refreshes cached objects |
| Signed URL | Provides controlled access to specific content |
| Signed Cookie | Provides controlled access to multiple resources |
| Origin Access Control | Restricts access to supported S3 origins |

## 36.2 CloudFront Architecture

```text
                  Users
                    |
                    v
              CloudFront CDN
                    |
          +---------+---------+
          |                   |
          v                   v
     Edge Location       Edge Location
          |                   |
          +---------+---------+
                    |
                    v
                  Origin
                    |
           +--------+--------+
           |                 |
           v                 v
         S3 Bucket         ALB
                             |
                             v
                         Application
```

CloudFront supports multiple origins, allowing different URL patterns to use different backend services.

---

# 37. CloudFront Origins

An origin is the source from which CloudFront retrieves content.

Common origins include:

- Amazon S3.
- Application Load Balancer.
- EC2-based web server.
- External HTTP server.

### Example

Suppose your application uses:

```text
example.com
```

You might configure:

| Request Path | Origin |
|---|---|
| `/images/*` | S3 |
| `/assets/*` | S3 |
| `/api/*` | ALB |
| `/*` | Next.js application origin |

This lets CloudFront distribute static content and forward dynamic application requests to the appropriate origin.

---

# 38. CloudFront Caching

Caching means storing a copy of a response for reuse.

Example:

A user requests:

```text
/images/logo.png
```

CloudFront checks whether the object is available in its cache.

If it is:

```text
Cache Hit
   |
   v
Return Cached Content
```

If it is not:

```text
Cache Miss
   |
   v
Request Origin
   |
   v
Receive Content
   |
   v
Cache Content
   |
   v
Return to User
```

## 38.1 Cache TTL

TTL determines how long content can remain cached.

Example:

```http
Cache-Control: public, max-age=3600
```

This suggests a one-hour browser cache lifetime.

CloudFront also has minimum, default and maximum TTL settings.

Be careful with minimum TTL: if it is greater than zero, CloudFront can cache an object for at least that period even when the origin sends `Cache-Control: no-cache`, `no-store` or `private`.

Therefore, configure caching carefully for authenticated and personalized content. [AWS Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/DownloadDistValuesCacheBehavior.html?utm_source=chatgpt.com)

## 38.2 Cache Invalidation

Suppose you update:

```text
/images/logo.png
```

But users continue receiving the old cached image.

You can create an invalidation:

```text
/images/logo.png
```

CloudFront will remove the relevant cached object as the invalidation propagates.

For frequently updated assets, versioned filenames are often more efficient:

```text
logo-v1.png
logo-v2.png
```

Or use content-hashed build assets.

## 38.3 Cache Key

A cache key determines which requests are treated as equivalent for caching.

It may include selected:

- Query strings.
- Cookies.
- Headers.

Forwarding unnecessary headers, cookies or query parameters can create many cache variants and reduce the cache hit ratio.

AWS recommends forwarding only the information required by the origin and cache design. [AWS Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/header-caching.html?utm_source=chatgpt.com)

---

# 39. CloudFront Origin Access Control (OAC)

Suppose you store images in an S3 bucket.

You want users to access those images through CloudFront, but you do not want the S3 bucket to be publicly accessible.

You can use **Origin Access Control (OAC)**.

Architecture:

```text
User
 |
 v
CloudFront
 |
 | Authenticated Origin Request
 |
 v
Private S3 Bucket
```

The S3 bucket policy permits the designated CloudFront distribution to access the bucket.

This prevents direct public access to the S3 origin while allowing CloudFront to serve the content.

AWS recommends OAC over the older Origin Access Identity approach for supported S3 bucket origins. [AWS Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html?utm_source=chatgpt.com)

---

# 40. CDN vs Load Balancer vs Reverse Proxy

These concepts are related but have different purposes.

| Feature | CDN | Load Balancer | Reverse Proxy |
|---|---|---|---|
| Main purpose | Content delivery and caching | Distribute traffic | Forward client requests |
| Global edge network | Common | Not necessarily | Not necessarily |
| Caching | Core feature | Not core purpose | Optional |
| Health checks | Service-dependent | Common | Configuration-dependent |
| Request routing | Yes | Yes | Yes |
| Example | CloudFront | ALB | Nginx |

A CDN can also perform reverse-proxy-like behavior.

An ALB can distribute traffic across application instances.

Nginx can operate as a reverse proxy and web server.

These technologies can work together.

---

# PART 9: WEB APPLICATION SECURITY

# 41. What Is AWS WAF?

AWS WAF stands for **AWS Web Application Firewall**.

It monitors and controls HTTP and HTTPS requests reaching supported resources.

You can use it to protect against common web application threats and abusive traffic patterns.

Examples:

- Malicious request patterns.
- SQL injection attempts.
- Cross-site scripting patterns.
- Excessive requests from a source.
- Suspicious traffic patterns.

## 41.1 AWS WAF Components

| Component | Meaning |
|---|---|
| Web ACL | Collection of rules |
| Rule | Defines matching conditions and actions |
| Rule Group | Collection of reusable rules |
| Managed Rule Group | AWS or third-party maintained rules |
| Rate-Based Rule | Limits traffic based on request rates |
| Scope-Down Statement | Restricts which requests a rule evaluates |

Example:

```text
Incoming HTTP Request
        |
        v
      AWS WAF
        |
        +---- Allowed ------> Application
        |
        +---- Blocked ------> Request Rejected
```

## 41.2 Rate-Based Rules

Suppose a client sends too many requests to your login API.

You can configure a rate-based rule to limit requests according to your selected aggregation criteria and evaluation window.

Example concept:

```text
Login endpoint:
Maximum configured request rate
per source IP
within an evaluation window
```

The exact threshold should be based on normal traffic patterns and business requirements.

AWS WAF supports rate-based rules for request-rate limiting. [AWS Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/waf-rule-statement-type-rate-based.html?utm_source=chatgpt.com)

---

# 42. AWS Shield

AWS Shield provides protection against Distributed Denial of Service (DDoS) attacks.

There are two main service tiers:

1. AWS Shield Standard.
2. AWS Shield Advanced.

## 42.1 DDoS Attack

DDoS stands for **Distributed Denial of Service**.

An attacker uses many sources to send excessive traffic to a target.

Example:

```text
Attacker Devices
      |
      +------+
      |      |
      v      v
   Massive Traffic
         |
         v
     Application
         |
         v
   Resource Exhaustion
```

The application may become unavailable because it cannot handle the traffic.

## 42.2 Shield Standard vs Advanced

| Feature | Shield Standard | Shield Advanced |
|---|---|---|
| DDoS protection | Yes | Yes |
| Availability | Included automatically for eligible AWS services | Additional subscription |
| Advanced protection capabilities | Basic managed protection | Additional advanced capabilities |
| Response support | Standard AWS support channels | Additional DDoS response capabilities |
| Cost | No additional charge | Paid service |

AWS WAF and Shield can work together to provide application-layer protections. [AWS Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/ddos-app-layer-web-ACL-and-rbr.html?utm_source=chatgpt.com)

**Important:** Neither WAF nor Shield replaces secure application development. You still need authentication, authorization, input validation, secure session management and application-level rate limiting.

---

# PART 10: AWS NETWORK TROUBLESHOOTING

# 43. Common Networking Problems

A production application may fail because of:

- Incorrect DNS records.
- Incorrect subnet routes.
- Missing Internet Gateway route.
- Missing NAT Gateway route.
- Security Group restrictions.
- Network ACL restrictions.
- Incorrect load balancer listener.
- Unhealthy target group.
- Invalid TLS certificate.
- Incorrect application port.
- Database connectivity restrictions.

A systematic troubleshooting process is essential.

## 43.1 Useful Linux Commands

### Check IP Address

```bash
ip addr
```

### Check Routing Table

```bash
ip route
```

### Check Listening Ports

```bash
ss -tulnp
```

### Test HTTP Connectivity

```bash
curl -v https://example.com
```

### Test DNS

```bash
dig example.com
```

### Test TCP Port Connectivity

```bash
nc -vz example.com 443
```

### Test Network Reachability

```bash
ping example.com
```

Note that ping uses ICMP, which may be blocked even when TCP or HTTPS connectivity works.

### Trace Network Path

```bash
traceroute example.com
```

Some environments use:

```bash
tracepath example.com
```

## 43.2 AWS Troubleshooting Services

| Service | Purpose |
|---|---|
| VPC Flow Logs | Records information about IP traffic to and from network interfaces |
| Reachability Analyzer | Analyzes network reachability between supported resources |
| CloudWatch | Metrics, logs and alarms |
| CloudTrail | Records AWS API activity |
| ELB Access Logs | Helps analyze load balancer requests |
| Route 53 Health Checks | Monitors configured endpoint health |
| Route 53 Query Logging | Helps inspect DNS queries in supported configurations |

---

# 44. Practical Network Troubleshooting Workflow

Suppose users report:

```text
https://api.example.com
```

is not working.

Follow this sequence.

### Step 1: Check DNS

```bash
dig api.example.com
```

Verify whether the domain resolves to the expected endpoint.

### Step 2: Check Connectivity

```bash
curl -v https://api.example.com
```

Inspect connection and TLS errors.

### Step 3: Check Route Tables

Verify:

- Correct subnet association.
- Correct default route.
- Correct Internet Gateway or NAT Gateway route.
- Correct route target.

### Step 4: Check Security Groups

Verify:

- Correct inbound ports.
- Correct source Security Group or IP range.
- Correct outbound rules.

### Step 5: Check Network ACLs

Verify:

- Inbound traffic.
- Outbound traffic.
- Ephemeral port rules where required.

### Step 6: Check Load Balancer

Verify:

- Listener.
- Listener rules.
- Target group.
- Target registration.
- Health check path.
- Target health status.

### Step 7: Check Application

On the backend server:

```bash
ss -tulnp
```

Check whether the application is listening on the expected port.

Check logs:

```bash
pm2 logs
```

### Step 8: Check TLS

Verify:

- Certificate validity.
- Certificate domain names.
- Certificate expiration.
- Correct listener certificate.
- HTTPS configuration.

This structured approach is much more effective than randomly changing AWS configurations.

---

# PART 11: TERRAFORM NETWORKING PRACTICE

You studied Terraform in Phase 43.

Now apply that knowledge to AWS networking.

# 45. Create an AWS VPC Using Terraform

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "production-vpc"
  }
}
```

This creates a VPC.

# 46. Create a Public Subnet

```hcl
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "ap-south-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet"
  }
}
```

# 47. Create an Internet Gateway

```hcl
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main-igw"
  }
}
```

# 48. Create a Public Route Table

```hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }

  tags = {
    Name = "public-route-table"
  }
}
```

Associate it with the subnet:

```hcl
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}
```

# 49. Create a Security Group

Example: allow HTTP and HTTPS.

```hcl
resource "aws_security_group" "web" {
  name        = "web-security-group"
  description = "Allow HTTP and HTTPS"
  vpc_id      = aws_vpc.main.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web-security-group"
  }
}
```

This example is suitable for a public-facing web entry point.

For an application server, prefer a separate Security Group that permits application traffic only from the load balancer's Security Group.

---

# 50. Create a Private Subnet

```hcl
resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.11.0/24"
  availability_zone = "ap-south-1a"

  tags = {
    Name = "private-subnet"
  }
}
```

A private subnet should not have a default route to an Internet Gateway.

If it requires outbound IPv4 internet connectivity, configure an appropriate NAT Gateway and private route table.

---

# 51. Create a Database Security Group

Suppose your database uses MySQL on port 3306.

```hcl
resource "aws_security_group" "database" {
  name        = "database-security-group"
  description = "Allow MySQL from application servers"
  vpc_id      = aws_vpc.main.id

  ingress {
    description     = "MySQL from application servers"
    from_port       = 3306
    to_port         = 3306
    protocol        = "tcp"
    security_groups = [aws_security_group.application.id]
  }

  tags = {
    Name = "database-security-group"
  }
}
```

This assumes you have created:

```hcl
aws_security_group.application
```

The database Security Group allows MySQL access from the application Security Group rather than from every public IP address.

---

# 52. Create an S3 Gateway VPC Endpoint

Example:

```hcl
resource "aws_vpc_endpoint" "s3" {
  vpc_id            = aws_vpc.main.id
  service_name      = "com.amazonaws.ap-south-1.s3"
  vpc_endpoint_type = "Gateway"

  route_table_ids = [
    aws_route_table.public.id
  ]

  tags = {
    Name = "s3-gateway-endpoint"
  }
}
```

In a real architecture, associate the endpoint with the route tables used by the private subnets that require S3 access.

This allows those resources to access S3 through the gateway endpoint rather than requiring a NAT Gateway for that traffic.

For current provider resource arguments, consult the official AWS provider documentation: [Terraform AWS Provider Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs?utm_source=chatgpt.com)

**Important:** The snippets above demonstrate individual resources, not a complete production-ready Terraform project. They omit some dependencies and configurations, such as NAT Gateway resources, multi-AZ subnet design, endpoint policies, application Security Groups, ALB resources and database resources.

---

# PART 12: PRODUCTION NETWORK ARCHITECTURE

# 53. Complete AWS Architecture for a Full-Stack Application

Let's design a production-style architecture for a web application built using:

- Next.js.
- Node.js.
- Express.
- MySQL.
- Redis.
- Amazon S3.

## 53.1 Architecture

```text
                         INTERNET
                             |
                             |
                             v
                       Route 53 DNS
                             |
                             |
                             v
                       AWS WAF
                             |
                             |
                             v
                       CloudFront
                             |
                +------------+------------+
                |                         |
                v                         v
             S3 Origin                Application LB
          Static Assets                   |
                                           |
                                  +--------+--------+
                                  |                 |
                                  v                 v
                              App Server A     App Server B
                                  |                 |
                                  +--------+--------+
                                           |
                                           v
                                      Redis Cache
                                           |
                                           v
                                      MySQL RDS
```

## 53.2 Network Placement

```text
AWS Region: ap-south-1

VPC: 10.0.0.0/16

+---------------------------------------------------+
|                     AWS VPC                       |
|                                                   |
|   Availability Zone A       Availability Zone B   |
|                                                   |
|   Public Subnet A           Public Subnet B       |
|       |                         |                 |
|       +-------- Application Load Balancer -------+|
|                                                   |
|   Private Subnet A          Private Subnet B     |
|       |                         |                 |
|   App Server A              App Server B         |
|       |                         |                 |
|       +-----------+-------------+                 |
|                   |                               |
|                   v                               |
|              Redis / Cache                        |
|                                                   |
|   Database Subnet A         Database Subnet B    |
|       |                         |                 |
|       +---------- RDS MySQL ---------------------+|
|                                                   |
+---------------------------------------------------+
```

## 53.3 Request Flow

When a user visits:

```text
https://example.com
```

The flow is:

1. Route 53 resolves the domain.
2. The request reaches CloudFront.
3. AWS WAF evaluates the request according to the configured rules.
4. CloudFront serves cached content or forwards the request to an origin.
5. Dynamic requests reach the application load balancer.
6. The ALB forwards traffic to a healthy application server.
7. The application processes the request.
8. Redis may provide cached data.
9. MySQL RDS handles database queries.
10. The application returns a response.

## 53.4 Security Design

| Resource | Network Placement | Security |
|---|---|---|
| CloudFront | AWS edge network | HTTPS, WAF |
| ALB | Public subnets | HTTPS listener, restricted inbound rules |
| Node.js servers | Private subnets | Accept traffic from ALB Security Group |
| Redis | Private subnets | Accept traffic from application Security Group |
| MySQL RDS | Private database subnets | Accept traffic from application Security Group |
| S3 | AWS service | Private bucket and appropriate bucket policy |

### Production Recommendations

1. Use at least two Availability Zones for important application components.
2. Keep databases private.
3. Avoid direct public SSH access to application servers.
4. Use Systems Manager Session Manager for administrative access where appropriate.
5. Use Security Group references instead of broad IP ranges when possible.
6. Use TLS for external and sensitive internal communication.
7. Use CloudFront Origin Access Control for private S3 origins.
8. Use VPC endpoints for appropriate AWS service access.
9. Enable VPC Flow Logs.
10. Monitor load balancer target health.
11. Configure DNS health checks and failover where required.
12. Monitor NAT Gateway and data-transfer costs.
13. Avoid single points of failure.
14. Keep application secrets in a suitable secret-management service.

---

# PART 13: COMMON MISTAKES AND BEST PRACTICES

# 54. Common AWS Networking Mistakes

| Mistake | Why It Is a Problem | Correct Approach |
|---|---|---|
| Putting RDS in a public subnet | Increases exposure | Use private database subnets |
| Opening MySQL to `0.0.0.0/0` | Exposes the database port | Allow only application resources |
| Forgetting route table association | Subnet uses an unintended route table | Verify explicit or main route table association |
| Assuming IGW alone provides internet access | Routing and addressing are also required | Configure route and public addressing |
| Forgetting NAT Gateway routes | Private resources cannot reach external IPv4 destinations through NAT | Configure private route tables |
| Using one NAT Gateway for all AZs without considering failure | Creates cross-AZ dependency | Consider NAT Gateway per AZ |
| Overlapping VPC CIDRs | Makes connectivity difficult or impossible | Plan address ranges |
| Misconfiguring NACL return traffic | Connections fail | Allow appropriate return and ephemeral traffic |
| Incorrect ALB health check path | Targets become unhealthy | Verify health endpoint and response |
| Incorrect CloudFront caching | Stale or private content may be cached | Design cache policies carefully |
| Using public S3 origins unnecessarily | Increases exposure | Prefer OAC for supported S3 origins |
| Ignoring network costs | Unexpected AWS bills | Monitor NAT, endpoints and data transfer |

---

# 55. Networking Security Best Practices

## Network Design

- Use separate public, private and database subnets.
- Deploy critical workloads across multiple Availability Zones.
- Avoid overlapping CIDR ranges.
- Use the smallest practical network access permissions.

## Security Groups

- Avoid unnecessary inbound rules.
- Prefer Security Group references.
- Do not expose database ports publicly.
- Avoid unrestricted SSH access.

## Route Tables

- Keep routing simple and understandable.
- Review default routes.
- Use explicit route table associations when appropriate.
- Validate routes when connecting VPCs.

## DNS

- Use appropriate TTL values.
- Configure health checks where necessary.
- Use private hosted zones for internal service discovery.
- Keep domain ownership and DNS configuration secure.

## Load Balancing

- Use health checks.
- Distribute critical targets across Availability Zones.
- Configure HTTPS listeners.
- Monitor unhealthy targets.

## CDN

- Cache static assets appropriately.
- Avoid caching personalized responses.
- Use versioned static assets.
- Use OAC for private S3 origins.

## Monitoring

- Enable VPC Flow Logs.
- Monitor load balancer metrics.
- Monitor DNS health.
- Monitor application logs.
- Set CloudWatch alarms for important failures.

---

# PART 14: INTERVIEW QUESTIONS AND ANSWERS

# 56. Basic Interview Questions

### Q1. What is a VPC?

A VPC is a logically isolated virtual network in AWS where users can deploy resources and control IP addressing, subnets, routing and network security.

### Q2. What is a subnet?

A subnet is a range of IP addresses within a VPC. It is associated with an Availability Zone.

### Q3. What is the difference between public and private subnets?

A public subnet has a route to an Internet Gateway. A private subnet does not have a direct Internet Gateway route.

### Q4. What is CIDR?

CIDR is a method of representing IP networks using an IP address and a prefix length.

Example:

```text
10.0.0.0/16
```

### Q5. What is an Internet Gateway?

An Internet Gateway provides connectivity between a VPC and the internet when routing, addressing and security requirements are satisfied.

### Q6. What is a NAT Gateway?

A NAT Gateway allows private subnet resources to initiate outbound IPv4 connections to external networks without allowing unsolicited inbound connections to those resources.

### Q7. What is a Security Group?

A Security Group is a stateful virtual firewall associated with network interfaces and resources.

### Q8. What is a Network ACL?

A Network ACL is a stateless firewall that controls inbound and outbound traffic at the subnet level.

### Q9. What is DNS?

DNS translates domain names into information such as IP addresses and service records.

### Q10. What is a CDN?

A CDN distributes content through geographically distributed locations to reduce latency and origin load.

---

# 57. Intermediate Interview Questions

### Q11. What is the difference between an Internet Gateway and a NAT Gateway?

An Internet Gateway enables internet connectivity for appropriately configured VPC resources.

A NAT Gateway allows private subnet resources to initiate outbound IPv4 internet connections without exposing those resources to unsolicited inbound connections.

### Q12. What is the difference between Security Groups and Network ACLs?

Security Groups are stateful and operate at the network interface/resource level.

Network ACLs are stateless and operate at the subnet level.

### Q13. What is VPC Peering?

VPC Peering is a direct network connection between two VPCs that allows resources to communicate using private IP addresses.

### Q14. Why is VPC Peering non-transitive?

Because a peering connection does not automatically allow a VPC to route traffic through another peered VPC to a third VPC.

### Q15. What is Transit Gateway?

Transit Gateway is a centralized networking service that connects multiple VPCs and supported external networks using a hub-and-spoke architecture.

### Q16. What is a VPC Endpoint?

A VPC Endpoint enables private connectivity from a VPC to supported AWS services or endpoint services without requiring a public internet path.

### Q17. What is Route 53?

Route 53 is AWS's managed DNS and domain registration service.

### Q18. What is an ALB?

An Application Load Balancer is a Layer 7 load balancer that distributes HTTP and HTTPS traffic based on application-level information.

### Q19. What is an NLB?

A Network Load Balancer is a Layer 4 load balancer designed for TCP, UDP and TLS traffic.

### Q20. What is CloudFront?

CloudFront is AWS's CDN service that delivers content through globally distributed edge locations.

---

# 58. Advanced Interview Questions

### Q21. How would you design a highly available AWS web application?

I would:

1. Create a VPC across multiple Availability Zones.
2. Deploy public subnets for the application load balancer.
3. Deploy application servers in private subnets.
4. Use a managed database with suitable multi-AZ configuration.
5. Use Redis for caching where appropriate.
6. Use Route 53 for DNS.
7. Use CloudFront for content delivery.
8. Use WAF for application-layer traffic protection.
9. Configure monitoring and health checks.
10. Use infrastructure automation for repeatable deployment.

### Q22. What happens when a private EC2 instance needs internet access?

It can send outbound IPv4 traffic through a NAT Gateway in a public subnet, provided that the NAT Gateway has a valid path to an Internet Gateway and the private subnet route table points to it.

### Q23. How do you troubleshoot an EC2 instance that cannot access the internet?

I would check:

1. Subnet association.
2. Route table.
3. Internet Gateway or NAT Gateway.
4. Security Group.
5. Network ACL.
6. Public addressing where direct access is intended.
7. DNS resolution.
8. Operating-system firewall and routing.

### Q24. What is the difference between ALB and NLB?

ALB operates at Layer 7 and supports HTTP-aware routing such as host- and path-based routing.

NLB operates at Layer 4 and supports TCP, UDP and TLS traffic.

### Q25. What is TLS termination?

TLS termination is the process of decrypting TLS traffic at an intermediary such as a load balancer or CDN before forwarding it to the backend.

### Q26. What is a CloudFront cache invalidation?

It is a request to remove specified objects from CloudFront's cache so that subsequent requests retrieve updated content from the origin when needed.

### Q27. How do you secure an S3 bucket used by CloudFront?

I would:

1. Keep the bucket private.
2. Configure CloudFront Origin Access Control.
3. Configure an S3 bucket policy granting access to the intended distribution.
4. Block unnecessary public access.
5. Enable suitable encryption and logging.

### Q28. What is the difference between VPC Peering and Transit Gateway?

VPC Peering provides direct, non-transitive connectivity between two VPCs.

Transit Gateway provides centralized connectivity between multiple VPCs and supported networks.

### Q29. Why should a database be deployed in private subnets?

A private database subnet reduces direct exposure to the public internet. Database access can be restricted to approved application resources.

### Q30. How does a CDN improve application performance?

A CDN caches content closer to users, reducing the distance requests need to travel and lowering the number of requests reaching the origin.

---

# PART 15: PRACTICAL ASSIGNMENTS

These assignments will help you apply the concepts.

## Assignment 1: Networking Fundamentals

Learn and practise:

- IPv4 and IPv6.
- Public and private IP addresses.
- TCP and UDP.
- Ports.
- OSI model.
- DNS resolution.

**Expected outcome:** Explain how a browser communicates with a web server.

## Assignment 2: CIDR and Subnetting

Calculate:

1. Total addresses in `/16`.
2. Total addresses in `/24`.
3. Total addresses in `/26`.
4. Traditional usable addresses in `/27`.
5. AWS usable IPv4 addresses in `/24`.

Divide:

```text
10.0.0.0/16
```

into at least six non-overlapping subnets.

**Expected outcome:** Design a valid CIDR allocation plan.

## Assignment 3: Create an AWS VPC

Create:

```text
VPC: 10.0.0.0/16
```

Subnets:

```text
Public Subnet A: 10.0.1.0/24
Public Subnet B: 10.0.2.0/24

Private Subnet A: 10.0.11.0/24
Private Subnet B: 10.0.12.0/24
```

Configure:

- Internet Gateway.
- Public route tables.
- Private route tables.
- Subnet associations.

**Expected outcome:** Understand public and private routing.

## Assignment 4: Configure NAT Gateway

Create:

- NAT Gateway.
- Elastic IP.
- Private route table.
- Private subnet default route.

Verify that a private instance can initiate outbound IPv4 internet connections.

**Expected outcome:** Understand NAT-based outbound connectivity.

## Assignment 5: Security Groups and Network ACLs

Create:

```text
Web Security Group
Application Security Group
Database Security Group
```

Configure:

```text
Internet -> Load Balancer: 443
Load Balancer -> Backend: 3000
Backend -> MySQL: 3306
```

Ensure that the database is not publicly accessible.

**Expected outcome:** Implement layered network access controls.

## Assignment 6: DNS with Route 53

Create a hosted zone and configure:

```text
example.com
www.example.com
api.example.com
```

Practise:

- A records.
- AAAA records.
- CNAME records.
- TXT records.
- TTL.
- Hosted zones.

**Expected outcome:** Understand domain resolution and DNS management.

## Assignment 7: Load Balancing

Deploy two application instances.

Create:

- Application Load Balancer.
- Target Group.
- Listener.
- Health Check.

Verify that the ALB distributes requests to healthy targets.

**Expected outcome:** Understand production traffic distribution.

## Assignment 8: CloudFront

Create an S3 bucket.

Configure:

- CloudFront distribution.
- S3 origin.
- Origin Access Control.
- HTTPS.
- Cache behavior.

Verify that users can access content through CloudFront but cannot access the private S3 bucket directly.

**Expected outcome:** Understand secure CDN architecture.

## Assignment 9: Terraform Networking

Use Terraform to create:

- VPC.
- Public subnets.
- Private subnets.
- Internet Gateway.
- Route tables.
- Security Groups.
- VPC Endpoint.

**Expected outcome:** Automate network infrastructure.

## Assignment 10: Complete Production Architecture

Design an architecture for:

```text
Next.js
Node.js
Express
MySQL
Redis
S3
CloudFront
Route 53
AWS WAF
ALB
```

Your design must include:

- Multiple Availability Zones.
- Public and private subnets.
- Database isolation.
- Network security.
- DNS.
- Load balancing.
- CDN.
- Monitoring.

**Expected outcome:** Produce a production-style AWS network architecture.

---

# PART 16: QUICK REVISION

# 59. Important Concepts at a Glance

| Concept | Remember |
|---|---|
| IP Address | Logical network address |
| IPv4 | 32-bit addressing |
| IPv6 | 128-bit addressing |
| CIDR | Network prefix notation |
| Subnet | IP range inside a VPC |
| VPC | Isolated virtual network |
| Public Subnet | Has a route to IGW |
| Private Subnet | No direct IGW route |
| Isolated Subnet | No internet default route |
| IGW | VPC internet connectivity |
| NAT Gateway | Outbound IPv4 connectivity for private resources |
| Route Table | Controls network paths |
| Security Group | Stateful resource-level firewall |
| Network ACL | Stateless subnet-level firewall |
| VPC Peering | Direct connection between two VPCs |
| Transit Gateway | Central network hub |
| VPC Endpoint | Private service connectivity |
| DNS | Domain name resolution |
| Route 53 | AWS DNS service |
| ALB | Layer 7 load balancer |
| NLB | Layer 4 load balancer |
| GWLB | Network appliance load balancing |
| TLS | Secure communication protocol |
| ACM | AWS certificate management |
| CDN | Distributed content delivery |
| CloudFront | AWS CDN |
| OAC | Restricts access to supported S3 origins |
| WAF | Web application firewall |
| Shield | DDoS protection |

---

# 60. Important Ports to Remember

| Port | Protocol | Service |
|---:|---|---|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 6379 | TCP | Redis |
| 27017 | TCP | MongoDB |
| 3000 | TCP | Common Node.js application port |
| 8080 | TCP | Common alternative HTTP application port |

Port numbers are conventions, not guarantees that a service is actually running there.

---

# 61. Phase 44 Completion Checklist

Before moving to the next phase, make sure you can explain the following topics.

### Networking Fundamentals

- [ ] OSI Model.
- [ ] TCP/IP Model.
- [ ] TCP vs UDP.
- [ ] IPv4 vs IPv6.
- [ ] Public vs Private IP.
- [ ] CIDR notation.
- [ ] Subnet masks.
- [ ] Subnetting calculations.

### AWS VPC

- [ ] VPC architecture.
- [ ] Public Subnets.
- [ ] Private Subnets.
- [ ] Isolated Subnets.
- [ ] Internet Gateway.
- [ ] NAT Gateway.
- [ ] Route Tables.
- [ ] Security Groups.
- [ ] Network ACLs.
- [ ] VPC Peering.
- [ ] Transit Gateway.
- [ ] VPC Endpoints.

### DNS

- [ ] DNS resolution process.
- [ ] Recursive Resolver.
- [ ] Authoritative DNS.
- [ ] A and AAAA records.
- [ ] CNAME records.
- [ ] MX and TXT records.
- [ ] TTL.
- [ ] Route 53 Hosted Zones.
- [ ] Route 53 Routing Policies.

### Load Balancing

- [ ] Layer 4 vs Layer 7.
- [ ] Elastic Load Balancing.
- [ ] Application Load Balancer.
- [ ] Network Load Balancer.
- [ ] Gateway Load Balancer.
- [ ] Listeners.
- [ ] Target Groups.
- [ ] Health Checks.

### CDN and Security

- [ ] HTTP vs HTTPS.
- [ ] TLS Certificates.
- [ ] AWS Certificate Manager.
- [ ] TLS Termination.
- [ ] CDN Architecture.
- [ ] CloudFront Origins.
- [ ] CloudFront Caching.
- [ ] Cache Invalidation.
- [ ] Origin Access Control.
- [ ] AWS WAF.
- [ ] AWS Shield.

### Practical Skills

- [ ] Create a VPC.
- [ ] Configure subnets.
- [ ] Configure Internet Gateway.
- [ ] Configure NAT Gateway.
- [ ] Configure route tables.
- [ ] Configure Security Groups.
- [ ] Configure DNS.
- [ ] Deploy an ALB.
- [ ] Configure CloudFront.
- [ ] Automate network infrastructure with Terraform.

---

# 62. Official Documentation and Learning Resources

Use these official resources for deeper study and hands-on implementation.

| Topic | Documentation |
|---|---|
| AWS VPC | [Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/?utm_source=chatgpt.com) |
| CIDR and Subnetting | [AWS Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-sizing.html?utm_source=chatgpt.com) |
| Route Tables | [AWS Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html?utm_source=chatgpt.com) |
| Internet Gateway | [AWS Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-igw.html?utm_source=chatgpt.com) |
| Security Groups | [AWS VPC Security](https://docs.aws.amazon.com/en_en/vpc/latest/userguide/infrastructure-security.html?utm_source=chatgpt.com) |
| VPC Endpoints | [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/?utm_source=chatgpt.com) |
| Route 53 | [Amazon Route 53 Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-concepts.html?utm_source=chatgpt.com) |
| Routing Policies | [Route 53 Routing Policies](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html?icmpid=docs_console_unmapped&utm_source=chatgpt.com) |
| Load Balancing | [AWS Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/?utm_source=chatgpt.com) |
| CloudFront | [CloudFront Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/DownloadDistValuesCacheBehavior.html?utm_source=chatgpt.com) |
| S3 Origin Access Control | [CloudFront OAC Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html?utm_source=chatgpt.com) |
| AWS WAF | [AWS WAF Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/waf-rule-statement-type-rate-based.html?utm_source=chatgpt.com) |
| Terraform AWS Provider | [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs?utm_source=chatgpt.com) |

---

# 63. Final Summary

In Phase 44, you studied how cloud networking works and how AWS provides the infrastructure required to deploy secure, scalable and highly available applications.

The most important architectural concept to remember is:

```text
Internet
   |
   v
Route 53
   |
   v
CloudFront + WAF
   |
   v
Application Load Balancer
   |
   v
Private Application Servers
   |
   +----------+
   |          |
   v          v
  Redis     MySQL RDS
```

You should now understand how to:

- Design an AWS VPC.
- Divide a VPC into subnets.
- Configure public and private routing.
- Control network access.
- Connect multiple VPCs.
- Configure DNS.
- Distribute application traffic.
- Secure web communication using TLS.
- Deliver content through CloudFront.
- Protect web applications using WAF and Shield.
- Troubleshoot network connectivity.
- Automate AWS networking using Terraform.

**Important learning priority:** Focus particularly on CIDR, subnetting, route tables, Security Groups, NAT Gateways, DNS, ALB and CloudFront. These concepts are frequently used in real-world cloud infrastructure and DevOps engineering.

---

## Next Phase Preview

# Phase 45 – Docker, Containerization and Docker Compose

The next phase will cover:

1. Virtual Machines vs Containers.
2. What Is Docker?
3. Docker Architecture.
4. Docker Engine.
5. Docker Images.
6. Docker Containers.
7. Docker Registries.
8. Docker Hub.
9. Dockerfile.
10. Docker Image Layers.
11. Docker Build Context.
12. Docker Volumes.
13. Docker Bind Mounts.
14. Docker Networks.
15. Docker Bridge Network.
16. Docker Host Network.
17. Docker Compose.
18. Multi-Container Applications.
19. Dockerizing Node.js Applications.
20. Dockerizing Next.js Applications.
21. Dockerizing MySQL and MongoDB.
22. Docker Environment Variables.
23. Docker Secrets.
24. Multi-Stage Builds.
25. Docker Image Optimization.
26. Docker Security Best Practices.
27. Docker Logging and Troubleshooting.
28. Docker Health Checks.
29. Docker Resource Limits.
30. Docker Production Deployment.
31. Docker with Nginx.
32. Docker with AWS.
33. Practical Full-Stack Docker Project.

**Phase 45 will move from infrastructure networking into containerization, which is a fundamental skill for modern DevOps and cloud engineering.**