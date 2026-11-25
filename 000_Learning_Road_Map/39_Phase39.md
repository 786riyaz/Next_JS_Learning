# Phase 39 – Advanced Networking, DNS, TLS, Reverse Proxies and Network Security

**Level:** Advanced  
**Domain:** Computer Networking, Web Infrastructure, Network Security, Server Administration  
**Primary Technologies:** TCP/IP, DNS, HTTP/HTTPS, TLS, Nginx, Linux Networking  
**Practical Stack:** Next.js 16, Node.js, Express, MongoDB, MySQL, AWS, Docker

---

# 1. Learning Objectives

By completing this phase, you should understand:

1. Computer networking fundamentals.
2. The OSI and TCP/IP models.
3. IP addresses and subnetting.
4. Public vs private IP addresses.
5. TCP vs UDP.
6. HTTP vs HTTPS.
7. How DNS resolution works.
8. DNS records and propagation.
9. Domain names and nameservers.
10. TLS/SSL handshakes.
11. Digital certificates and Certificate Authorities.
12. Reverse proxy architecture.
13. Nginx advanced configuration.
14. Forward proxy vs reverse proxy.
15. Load-balancing algorithms.
16. WebSockets and network proxies.
17. Firewalls and network security.
18. Ports, sockets and network interfaces.
19. NAT and port forwarding.
20. Network troubleshooting tools.
21. Production network architecture.

---

# 2. Computer Networking Fundamentals

## 2.1 What Is a Computer Network?

A computer network is a collection of interconnected devices that communicate and exchange data.

These devices can include:

- Desktop computers.
- Laptops.
- Mobile phones.
- Servers.
- Routers.
- Switches.
- Network printers.
- IoT devices.

For example, when you open a website:

```text
Your Computer
     |
     v
   Router
     |
     v
   Internet
     |
     v
 Website Server
     |
     v
 HTML / CSS / JavaScript
     |
     v
Your Browser
```

Your computer sends a request through the network, and the server returns a response.

## 2.2 Why Do We Need Networking?

Networking allows computers to:

- Communicate with each other.
- Share files.
- Access websites.
- Connect to databases.
- Send emails.
- Stream videos.
- Access cloud infrastructure.
- Exchange data through APIs.

Consider a full-stack application:

```text
Browser
   |
   | HTTPS
   v
Next.js Frontend
   |
   | HTTP / HTTPS
   v
Express Backend
   |
   | Database Protocol
   v
MongoDB / MySQL
```

All these components communicate through networks, even when they run on the same machine.

## 2.3 Important Networking Terms

| Term | Meaning |
|---|---|
| Network | Connected devices that exchange data |
| Node | A device or endpoint participating in a network |
| Host | A device or system with network communication capabilities |
| Client | A system requesting a service |
| Server | A system providing a service |
| Protocol | Rules governing communication |
| Packet | A unit of network-layer data |
| Frame | A unit of data at the data-link layer |
| IP Address | Logical network address |
| MAC Address | Link-layer hardware address |
| Port | Identifies a transport-layer endpoint |
| Router | Forwards packets between networks |
| Switch | Connects devices within a local network |

### Real-World Example

When your React frontend calls an Express API:

```ts
const response = await fetch(
  "https://api.example.com/users"
);
```

Several networking concepts are involved:

1. DNS resolves `api.example.com`.
2. Your computer establishes a network connection.
3. TCP or QUIC transports the data.
4. TLS secures HTTPS communication.
5. HTTP carries the request.
6. The server processes it.
7. The response returns to the browser.

We will understand each step.

---

# 3. OSI Model and TCP/IP Model

Two important networking models are:

1. OSI Model.
2. TCP/IP Model.

These models help us understand how data travels across networks.

## 3.1 OSI Model

OSI stands for **Open Systems Interconnection**.

It is a conceptual model consisting of seven layers.

Each layer describes a different aspect of network communication.

### Seven Layers of the OSI Model

| Layer | Name | Main Responsibility | Examples |
|---|---|---|---|
| 7 | Application | Application-level communication | HTTP, DNS, SMTP |
| 6 | Presentation | Data representation, encoding and encryption concepts | TLS-related functions, JSON, UTF-8 |
| 5 | Session | Session establishment and management concepts | Session management |
| 4 | Transport | End-to-end transport | TCP, UDP |
| 3 | Network | Logical addressing and routing | IPv4, IPv6 |
| 2 | Data Link | Local network delivery | Ethernet, Wi-Fi, MAC |
| 1 | Physical | Transmission of raw bits | Cables, radio signals |

### Layer 7 – Application Layer

This layer provides network communication services to applications.

Examples:

- HTTP.
- HTTPS.
- DNS.
- SMTP.
- FTP.

When your browser requests:

```http
GET /products HTTP/1.1
Host: example.com
```

HTTP is operating at the application layer.

### Layer 6 – Presentation Layer

This layer is concerned with how data is represented.

Responsibilities traditionally associated with it include:

- Data encoding.
- Character representation.
- Serialization.
- Compression.
- Encryption.

Examples:

- UTF-8.
- JSON serialization.
- Data compression.
- Encryption-related operations.

**Important:** Real-world protocol stacks do not always map neatly onto individual OSI layers. TLS, for example, sits between application protocols and transport in common implementations rather than being exclusively a Layer 6 protocol.

### Layer 5 – Session Layer

This layer describes establishing, maintaining and terminating communication sessions.

In modern web systems, session management is implemented through several protocols and application mechanisms.

Examples:

- Authentication sessions.
- Session identifiers.
- Connection management.

### Layer 4 – Transport Layer

This layer provides communication between processes.

Important protocols:

- TCP.
- UDP.

It uses port numbers to identify application endpoints.

### Layer 3 – Network Layer

This layer handles logical addressing and routing.

Important protocols:

- IPv4.
- IPv6.

Routers operate primarily at this layer.

### Layer 2 – Data Link Layer

This layer handles communication across a local network link.

Examples:

- Ethernet.
- Wi-Fi link-layer mechanisms.
- MAC addresses.

Switches commonly operate at this layer.

### Layer 1 – Physical Layer

This layer handles physical transmission.

Examples:

- Ethernet cables.
- Fiber-optic cables.
- Radio signals.
- Electrical signals.

## 3.2 TCP/IP Model

The TCP/IP model is a practical networking model used to understand Internet communication.

A common four-layer representation is:

| TCP/IP Layer | Corresponding OSI Layers |
|---|---|
| Application | 5, 6, 7 |
| Transport | 4 |
| Internet | 3 |
| Network Access | 1, 2 |

```text
TCP/IP Model

Application
    |
Transport
    |
Internet
    |
Network Access
```

Examples:

| Layer | Protocols |
|---|---|
| Application | HTTP, HTTPS, DNS, SMTP |
| Transport | TCP, UDP |
| Internet | IPv4, IPv6, ICMP |
| Network Access | Ethernet, Wi-Fi |

## 3.3 Data Encapsulation

Encapsulation means adding protocol-specific information as data moves down the network stack.

Suppose your browser sends an HTTP request.

Conceptually:

```text
Application Layer
       |
       v
HTTP Data
       |
       v
Transport Layer
       |
       v
TCP Segment
       |
       v
Network Layer
       |
       v
IP Packet
       |
       v
Data Link Layer
       |
       v
Ethernet / Wi-Fi Frame
       |
       v
Physical Transmission
```

At the receiving system, the process is reversed.

This is called **decapsulation**.

## 3.4 Why Is the OSI Model Important?

It helps troubleshoot networking problems.

| Problem | Possible Layer |
|---|---|
| Cable disconnected | Physical |
| MAC or switch issue | Data Link |
| Incorrect IP or routing | Network |
| TCP connection refused | Transport |
| DNS resolution failure | Application |
| HTTP 404 | Application |
| TLS certificate error | Security/transport-related protocol handling |

Official reference: [Cloudflare – Network Layers](https://developers.cloudflare.com/fundamentals/reference/network-layers/)

---

# 4. IP Addresses and Subnetting

## 4.1 What Is an IP Address?

IP stands for Internet Protocol.

An IP address identifies a network interface for communication using IP.

Example:

```text
192.168.1.10
```

IP addresses are used to route packets between networks.

There are two major versions:

1. IPv4.
2. IPv6.

## 4.2 IPv4

IPv4 uses 32-bit addresses.

Example:

```text
192.168.1.10
```

An IPv4 address consists of four octets.

```text
192 . 168 . 1 . 10
```

Each octet contains 8 bits.

Therefore:

```text
8 × 4 = 32 bits
```

Each octet can represent values from 0 to 255.

## 4.3 IPv6

IPv6 uses 128-bit addresses.

Example:

```text
2001:db8:85a3::8a2e:370:7334
```

IPv6 was introduced to provide a much larger address space and additional networking capabilities.

Comparison:

| IPv4 | IPv6 |
|---|---|
| 32-bit | 128-bit |
| Dotted decimal notation | Hexadecimal notation |
| `192.168.1.10` | `2001:db8::1` |
| Smaller address space | Much larger address space |
| NAT is common | NAT is generally not required for address conservation |

## 4.4 Public vs Private IP Addresses

### Public IP Address

A public IP address is globally routable on the public internet, subject to network and firewall configuration.

Example:

```text
203.0.113.10
```

This is a documentation-only example address, not a real public server address.

A public IP can be used to make a server reachable from the internet.

### Private IP Address

Private IP addresses are used within private networks.

Common IPv4 private ranges:

| Range | CIDR |
|---|---|
| `10.0.0.0 – 10.255.255.255` | `10.0.0.0/8` |
| `172.16.0.0 – 172.31.255.255` | `172.16.0.0/12` |
| `192.168.0.0 – 192.168.255.255` | `192.168.0.0/16` |

Example:

```text
192.168.1.10
```

Your laptop may receive this address from your Wi-Fi router.

### Important

A private IP address is not directly reachable over the public internet through normal public routing.

Private networks commonly use NAT when communicating with the internet.

## 4.5 Static vs Dynamic IP Address

### Static IP

An IP address that is intended to remain fixed.

Common uses:

- Production servers.
- Network infrastructure.
- Allowlisted services.
- Stable DNS destinations.

### Dynamic IP

An IP address assigned dynamically, commonly through DHCP.

Your home router may receive a dynamic public IP from your ISP.

| Static IP | Dynamic IP |
|---|---|
| Stable address | Can change |
| Useful for fixed infrastructure | Common for end-user networks |
| Often requires explicit provisioning | Commonly assigned through DHCP |

## 4.6 What Is a Subnet?

A subnet is a logical division of an IP network.

Suppose your network is:

```text
192.168.1.0/24
```

It contains 256 IPv4 addresses in total.

The `/24` notation means that 24 bits represent the network prefix.

The remaining 8 bits are available for host addressing.

### Understanding CIDR

CIDR stands for Classless Inter-Domain Routing.

Example:

```text
192.168.1.0/24
```

The `/24` indicates the network prefix length.

For ordinary IPv4 subnetting:

```text
Total addresses = 2^(32 - prefix length)
```

Examples:

| CIDR | Total IPv4 Addresses |
|---|---:|
| `/8` | 16,777,216 |
| `/16` | 65,536 |
| `/24` | 256 |
| `/25` | 128 |
| `/26` | 64 |
| `/27` | 32 |
| `/28` | 16 |
| `/30` | 4 |

For a traditional IPv4 subnet, the network and broadcast addresses are not assigned to normal hosts.

Some environments, including AWS VPC subnets, reserve additional addresses.

### Example

```text
Network: 192.168.1.0/24

Network Address:   192.168.1.0
First Host:        192.168.1.1
Last Host:         192.168.1.254
Broadcast Address: 192.168.1.255
```

There are 254 normally usable host addresses in this traditional `/24` example.

## 4.7 Subnetting Example

Suppose you have:

```text
192.168.1.0/24
```

You want to divide it into four equal subnets.

You borrow two host bits:

```text
/24 → /26
```

Each `/26` subnet contains 64 addresses.

The resulting networks are:

| Subnet | Network Address | Address Range |
|---|---|---|
| 1 | `192.168.1.0/26` | `.0 – .63` |
| 2 | `192.168.1.64/26` | `.64 – .127` |
| 3 | `192.168.1.128/26` | `.128 – .191` |
| 4 | `192.168.1.192/26` | `.192 – .255` |

Each traditional `/26` subnet has 62 usable host addresses.

### Why Is Subnetting Important?

Subnetting helps:

- Organize networks.
- Separate application and database systems.
- Control routing.
- Improve network security.
- Manage IP address allocation.
- Design AWS VPC infrastructure.

---

# 5. TCP vs UDP

TCP and UDP are transport-layer protocols.

Both allow applications to exchange data, but they work differently.

## 5.1 TCP – Transmission Control Protocol

TCP is a connection-oriented transport protocol.

It provides:

- Reliable byte-stream delivery.
- Ordered data delivery.
- Retransmission of lost data.
- Flow control.
- Congestion control.

Before normal TCP data exchange begins, the endpoints establish a connection.

### TCP Three-Way Handshake

TCP uses a three-way handshake to establish a connection.

The steps are:

1. SYN.
2. SYN-ACK.
3. ACK.

Diagram:

```text
Client                         Server
  |                               |
  | -------- SYN ---------------> |
  |                               |
  | <------ SYN + ACK ----------- |
  |                               |
  | -------- ACK ---------------> |
  |                               |
  |       Connection Established  |
```

### Step 1: SYN

The client sends a SYN packet to initiate a connection.

### Step 2: SYN-ACK

The server acknowledges the client's SYN and sends its own SYN.

### Step 3: ACK

The client acknowledges the server's SYN.

The TCP connection is now established.

### Where Is TCP Used?

- HTTP/1.1.
- HTTP/2.
- SSH.
- MySQL.
- PostgreSQL.
- Traditional WebSocket connections.

## 5.2 UDP – User Datagram Protocol

UDP is a connectionless transport protocol.

It does not provide TCP-style delivery guarantees, ordering or retransmission.

It has less protocol overhead.

### Where Is UDP Used?

- DNS queries in common cases.
- Online gaming.
- Voice communication.
- Video conferencing.
- Streaming protocols.
- QUIC.

Some applications implement their own reliability mechanisms on top of UDP.

## 5.3 TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Delivery guarantees | Yes, at transport level | No |
| Ordered delivery | Yes | No |
| Retransmission | Yes | No built-in retransmission |
| Flow control | Yes | No built-in flow control |
| Congestion control | Yes | No built-in congestion control |
| Overhead | Generally higher | Generally lower |
| Common use | Web, SSH, databases | DNS, gaming, QUIC |

## 5.4 Important: HTTP/3 Uses QUIC

HTTP/3 does not use TCP.

It uses QUIC, which runs over UDP and provides features such as:

- Secure transport.
- Connection establishment.
- Stream multiplexing.
- Congestion control.
- Connection migration.

This is an important distinction when studying modern web networking.

---

# 6. HTTP vs HTTPS

## 6.1 What Is HTTP?

HTTP stands for **Hypertext Transfer Protocol**.

It is an application-layer protocol used for communication between clients and servers.

Example:

```http
GET /products HTTP/1.1
Host: example.com
```

HTTP follows a request-response model.

```text
Client
  |
  | HTTP Request
  v
Server
  |
  | HTTP Response
  v
Client
```

## 6.2 What Is HTTPS?

HTTPS means HTTP over a secure transport connection using TLS.

It provides:

1. Encryption.
2. Authentication of the server's identity.
3. Integrity protection.

Without HTTPS, an attacker with a suitable position on the network may be able to observe or modify unprotected traffic.

HTTPS protects the communication channel.

However, HTTPS does not guarantee that the website itself is trustworthy or free from malicious code.

## 6.3 HTTP vs HTTPS

| HTTP | HTTPS |
|---|---|
| Unencrypted application traffic | HTTP protected by TLS |
| Default port 80 | Default port 443 |
| No TLS certificate required | TLS certificate required |
| Vulnerable to network eavesdropping and tampering | Protects traffic against these attacks when configured correctly |
| Not appropriate for sensitive production traffic | Standard choice for production web applications |

## 6.4 Common HTTP Methods

| Method | Purpose |
|---|---|
| GET | Retrieve data |
| POST | Submit data or perform an operation |
| PUT | Replace a resource representation |
| PATCH | Partially modify a resource |
| DELETE | Delete a resource |
| HEAD | Retrieve headers without the response body |
| OPTIONS | Describe communication options |

Example:

```ts
const response = await fetch(
  "https://api.example.com/users",
  {
    method: "GET",
  }
);

const users = await response.json();
```

## 6.5 HTTP Status Codes

| Status | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 301 | Moved Permanently |
| 302 | Found |
| 304 | Not Modified |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 405 | Method Not Allowed |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

### Important Difference

- **401:** Authentication is missing or invalid.
- **403:** The server understood the request but refuses authorization.
- **404:** The requested resource was not found.
- **502:** A gateway or proxy received an invalid response from an upstream server.
- **504:** A gateway or proxy did not receive a timely response from an upstream server.

## 6.6 HTTP/1.1 vs HTTP/2 vs HTTP/3

| Feature | HTTP/1.1 | HTTP/2 | HTTP/3 |
|---|---|---|---|
| Transport | TCP | TCP | QUIC over UDP |
| Multiplexing | Limited | Yes | Yes |
| Header compression | No built-in HPACK | HPACK | QPACK |
| Connection setup | TCP | TCP | QUIC |
| Head-of-line blocking | Can occur at HTTP request level | TCP-level blocking can still affect streams | Avoids TCP-level cross-stream blocking |
| Encryption | Optional at protocol level | Browser deployments use HTTPS | Uses TLS 1.3 integration |

### Important

HTTP/2 multiplexing allows multiple streams over one connection, but TCP packet loss can still delay data across streams.

HTTP/3 uses QUIC streams to reduce this kind of cross-stream blocking.

Official reference: [MDN – HTTP Specifications](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Resources_and_specifications)

---

# 7. DNS – Domain Name System

DNS stands for **Domain Name System**.

It translates domain names into information used to locate network services.

For example:

```text
google.com
```

must be resolved into an appropriate IP address before a client can connect to the service.

## 7.1 Why Do We Need DNS?

Imagine that you had to remember an IP address for every website.

Instead of:

```text
https://example.com
```

you would need to remember an IP address.

DNS allows people to use human-readable domain names.

## 7.2 DNS Resolution Process

Let's understand what happens when you open:

```text
https://www.example.com
```

Your computer needs to resolve the domain.

A simplified DNS resolution flow:

```text
User enters domain
        |
        v
Browser DNS Cache
        |
        v
Operating System Cache
        |
        v
Recursive DNS Resolver
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
IP Address Returned
        |
        v
Browser Connects to Server
```

### Step 1: Browser Cache

The browser may already have a cached DNS result.

If the result is valid, it may be reused.

### Step 2: Operating System Cache

If the browser does not have a suitable result, the operating system may have one.

### Step 3: Recursive Resolver

The client asks a recursive DNS resolver to resolve the domain.

This resolver may be provided by:

- Your ISP.
- Your organization.
- A public DNS provider.
- A local network service.

### Step 4: Root DNS Server

If the resolver needs to continue the lookup, it can query a root DNS server.

Root servers direct the resolver toward the appropriate Top-Level Domain infrastructure.

### Step 5: TLD DNS Server

TLD means Top-Level Domain.

Examples:

- `.com`
- `.org`
- `.net`
- `.in`

For `example.com`, the `.com` infrastructure directs the resolver toward the relevant authoritative nameservers.

### Step 6: Authoritative DNS Server

The authoritative server provides the DNS records for the domain.

Example:

```text
example.com → 203.0.113.10
```

### Step 7: Browser Connects

The resolver returns the answer.

The browser can now establish a connection to the destination.

**Important:** This is a simplified flow. DNS caches, forwarding resolvers, DNSSEC validation and other mechanisms can change the exact sequence.

## 7.3 Recursive vs Authoritative DNS Server

### Recursive Resolver

A recursive resolver obtains the answer on behalf of the client.

It may query other DNS servers and cache responses.

### Authoritative DNS Server

An authoritative DNS server provides the official DNS data for a domain or zone.

| Recursive Resolver | Authoritative Server |
|---|---|
| Finds the answer for the client | Provides authoritative records |
| May query other DNS servers | Stores or serves zone data |
| Usually caches responses | Provides authoritative answers |

## 7.4 DNS Record Types

DNS records define information associated with a domain.

| Record | Purpose | Example |
|---|---|---|
| A | IPv4 address | `example.com → 203.0.113.10` |
| AAAA | IPv6 address | `example.com → 2001:db8::10` |
| CNAME | Alias to another hostname | `www → example.com` |
| MX | Mail exchange server | `mail.example.com` |
| TXT | Text data | Domain verification |
| NS | Nameserver delegation | `ns1.example.net` |
| SOA | Zone authority information | Primary zone metadata |
| CAA | Restricts authorized certificate authorities | `letsencrypt.org` |

### A Record

Maps a hostname to an IPv4 address.

```text
example.com → 203.0.113.10
```

### AAAA Record

Maps a hostname to an IPv6 address.

```text
example.com → 2001:db8::10
```

### CNAME Record

Maps one hostname to another hostname.

```text
www.example.com → example.com
```

A CNAME cannot generally coexist with other ordinary record types at the same DNS owner name. DNS providers often offer special apex-alias features to work around the zone-apex limitation.

### MX Record

Used for email routing.

```text
example.com → mail.example.com
```

MX records also include priority values.

### TXT Record

Stores text information.

Common uses:

- Domain ownership verification.
- SPF.
- DKIM-related records.
- DMARC-related records.

### NS Record

Identifies the authoritative nameservers for a DNS zone.

### CAA Record

Allows a domain owner to specify which Certificate Authorities may issue certificates for the domain.

## 7.5 What Is TTL?

TTL stands for **Time To Live**.

It determines how long a DNS record may be cached.

Example:

```text
Record: example.com
Type: A
Value: 203.0.113.10
TTL: 3600 seconds
```

This means a DNS resolver may cache the response for up to one hour, subject to DNS caching rules.

### Why Is TTL Important?

Suppose you change your server's IP address.

If DNS records have a long TTL, some resolvers may continue using previously cached records until those records expire.

This is one reason DNS changes may not appear immediately everywhere.

## 7.6 DNS Propagation

DNS propagation is the common term used to describe how DNS changes become visible across different resolvers.

Example:

```text
Old IP: 203.0.113.10

New IP: 203.0.113.20
```

Some resolvers may return the new IP immediately, while others may continue returning the cached old IP.

DNS changes do not necessarily update every resolver simultaneously.

## 7.7 Domain vs DNS Hosting vs Web Hosting

These are different concepts.

| Term | Meaning |
|---|---|
| Domain Registrar | Company where you register a domain |
| DNS Hosting | Service managing DNS records |
| Web Hosting | Infrastructure running your website |
| Nameserver | Server providing authoritative DNS information |
| DNS Record | Information associated with a domain |

Example:

You register:

```text
example.com
```

Your DNS provider manages its records.

Your website runs on a Hostinger VPS.

The DNS A record points to your VPS public IP.

```text
example.com
      |
      v
DNS Provider
      |
      v
VPS Public IP
      |
      v
Nginx
      |
      v
Next.js Application
```

Official reference: [Cloudflare – DNS Record Types](https://developers.cloudflare.com/dns/manage-dns-records/reference/dns-record-types/)

---

# 8. Domain Names and Nameservers

## 8.1 What Is a Domain Name?

A domain name is a human-readable name used to identify internet resources.

Example:

```text
example.com
```

A domain is organized hierarchically.

```text
                 Root
                  |
                  v
                 .com
                  |
                  v
               example
                  |
                  v
             www.example.com
```

## 8.2 Domain Name Structure

Consider:

```text
api.example.com
```

It contains:

| Part | Meaning |
|---|---|
| `.` | DNS root |
| `com` | Top-Level Domain |
| `example` | Registered domain label |
| `api` | Subdomain |

## 8.3 What Is a Nameserver?

A nameserver provides DNS information for a domain or zone.

Example:

```text
ns1.example-dns.com
ns2.example-dns.com
```

When you configure your domain's nameservers at the registrar, you delegate DNS authority to the specified DNS provider.

### Nameserver vs DNS Record

A nameserver tells resolvers where to obtain authoritative DNS information.

A DNS record contains the actual information, such as an A record or MX record.

## 8.4 DNS Configuration Example

Suppose your application is running on a VPS with a public IP.

Your DNS zone could contain:

| Type | Name | Value |
|---|---|---|
| A | `@` | VPS public IPv4 |
| A | `www` | VPS public IPv4 |
| A | `api` | VPS public IPv4 |
| TXT | `@` | Verification value |
| MX | `@` | Mail server |

For a production deployment, configure the records according to the actual hosting and email providers.

---

# 9. TLS/SSL Handshake

TLS stands for **Transport Layer Security**.

It secures communication between clients and servers.

SSL is its predecessor and is obsolete for modern secure deployments.

Today, use TLS rather than old SSL protocols.

## 9.1 What Is TLS?

TLS provides:

1. Encryption.
2. Authentication.
3. Data integrity.

Example:

```text
Browser
   |
   | HTTPS
   |
   v
Website Server
```

TLS protects the communication between the client and the server.

## 9.2 What Is a TLS Certificate?

A TLS certificate is a digital document that binds a public key to a domain identity.

It contains information such as:

- Domain names.
- Public key.
- Issuer.
- Validity period.
- Certificate extensions.
- Digital signature.

Example:

```text
Domain: example.com

Issuer: Example Certificate Authority

Public Key: ...

Valid From: ...

Valid Until: ...

Digital Signature: ...
```

The certificate helps the client verify the server's identity.

## 9.3 Public Key and Private Key

TLS uses asymmetric cryptography during authentication and key establishment.

A key pair consists of:

- Public key.
- Private key.

### Public Key

Can be shared.

### Private Key

Must remain secret.

Example:

```text
Server
  |
  ├── Public Key
  |
  └── Private Key
```

The certificate contains the server's public key.

The corresponding private key remains under the server operator's control.

**Important:** In modern TLS 1.3, the certificate's public key is generally used for authentication and digital signatures. Ephemeral Diffie-Hellman key exchange is commonly used to establish shared session secrets.

## 9.4 TLS 1.3 Handshake

A simplified TLS 1.3 handshake:

```text
Client                              Server
  |                                   |
  | -------- ClientHello -----------> |
  |                                   |
  | <------- ServerHello ------------ |
  | <------- Encrypted Extensions --- |
  | <------- Certificate ------------ |
  | <------- CertificateVerify ----- |
  | <------- Finished --------------- |
  |                                   |
  | -------- Finished -------------> |
  |                                   |
  |       Secure Connection           |
```

### Step 1: ClientHello

The client sends information such as:

- Supported TLS versions.
- Supported cipher suites.
- Key share.
- Extensions.
- Server Name Indication (SNI), when applicable.

### Step 2: ServerHello

The server selects compatible cryptographic parameters and sends its key share.

### Step 3: Server Authentication

The server sends its certificate and proves possession of the corresponding private key through the handshake signature.

### Step 4: Client Verification

The client validates:

- Certificate chain.
- Certificate validity.
- Domain name.
- Trust chain.
- Other certificate constraints.

### Step 5: Secure Communication

Both parties derive shared traffic keys.

Subsequent application data is encrypted and integrity-protected.

**Important:** This describes a common certificate-authenticated TLS 1.3 handshake. Other modes and extensions can change some details.

## 9.5 What Is a Certificate Authority?

A Certificate Authority (CA) is an entity trusted to issue digital certificates.

Examples:

- Let's Encrypt.
- DigiCert.
- GlobalSign.

A browser or operating system maintains trusted CA certificates.

The server presents a certificate chain that the client validates against its trust store.

## 9.6 What Is Let's Encrypt?

Let's Encrypt is a free, automated Certificate Authority.

It uses the ACME protocol to automate certificate issuance and renewal.

For example, you can use Certbot on an Ubuntu server with Nginx.

A typical workflow:

```text
Domain Points to Server
          |
          v
Install Certbot
          |
          v
Verify Domain Control
          |
          v
Obtain Certificate
          |
          v
Configure Nginx
          |
          v
Enable HTTPS
```

Official references:

- [Let's Encrypt – How It Works](https://letsencrypt.org/how-it-works/)
- [Let's Encrypt – ACME Clients](https://letsencrypt.org/docs/client-options/)

---

# 10. Reverse Proxy Architecture

A reverse proxy is a server that receives client requests and forwards them to one or more backend servers.

It acts as an intermediary between clients and application servers.

## 10.1 Without Reverse Proxy

Suppose your Express application runs on port 3000.

The user accesses:

```text
http://203.0.113.10:3000
```

The application is directly exposed through that port.

```text
User
 |
 v
Express Application
Port 3000
```

This is simple, but it offers less flexibility for production infrastructure.

## 10.2 With Reverse Proxy

You install Nginx in front of the application.

```text
User
 |
 | HTTPS
 v
Nginx
Port 443
 |
 | HTTP
 v
Express Application
Port 3000
```

The user communicates with Nginx, and Nginx forwards the request to Express.

The backend port does not need to be publicly exposed.

## 10.3 Why Use a Reverse Proxy?

A reverse proxy can provide:

- HTTPS termination.
- Load balancing.
- Request routing.
- Compression.
- Buffering.
- Static file delivery.
- Access logging.
- Security controls.
- Rate limiting.
- WebSocket proxying.

## 10.4 Common Reverse Proxy Examples

- Nginx.
- HAProxy.
- Apache HTTP Server.
- Traefik.
- Envoy.

## 10.5 Reverse Proxy vs Load Balancer

A reverse proxy is an intermediary that forwards requests.

A load balancer distributes traffic among multiple targets.

A reverse proxy can also perform load balancing.

```text
User
 |
 v
Nginx Reverse Proxy
 |
 ├── Express Server A
 ├── Express Server B
 └── Express Server C
```

---

# 11. Forward Proxy vs Reverse Proxy

These are two important proxy architectures.

## 11.1 Forward Proxy

A forward proxy sits between clients and external servers.

Example:

```text
Internal Client
      |
      v
Forward Proxy
      |
      v
Internet
      |
      v
External Website
```

It represents or mediates traffic from clients.

Common uses:

- Corporate internet access control.
- Content filtering.
- Outbound traffic logging.
- Privacy and network policy enforcement.

## 11.2 Reverse Proxy

A reverse proxy sits in front of servers.

Example:

```text
Internet Users
      |
      v
Reverse Proxy
      |
      v
Backend Servers
```

Common uses:

- Website hosting.
- API routing.
- HTTPS termination.
- Load balancing.
- Backend protection.

## 11.3 Comparison

| Forward Proxy | Reverse Proxy |
|---|---|
| Represents clients | Represents backend servers |
| Sits in front of clients | Sits in front of servers |
| Controls outbound traffic | Controls incoming traffic |
| Hides client network details from destinations | Hides backend infrastructure from clients |
| Common in corporate networks | Common in web infrastructure |

### Interview Question

**Q. Is Nginx a reverse proxy or forward proxy?**

Nginx is commonly used as a reverse proxy, but it can also be configured for proxy-related use cases.

---

# 12. Nginx Advanced Configuration

Nginx is a high-performance web server and reverse proxy.

It can also perform load balancing and serve static content.

## 12.1 Nginx Architecture

```text
                 Internet
                    |
                    v
                  Nginx
                    |
          ┌─────────┴─────────┐
          |                   |
          v                   v
     Next.js App          Express API
      Port 3000            Port 4000
```

## 12.2 Install Nginx on Ubuntu

```bash
sudo apt update

sudo apt install nginx -y
```

Check status:

```bash
sudo systemctl status nginx
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Enable at startup:

```bash
sudo systemctl enable nginx
```

Check version:

```bash
nginx -v
```

## 12.3 Important Nginx Directives

| Directive | Purpose |
|---|---|
| `server` | Defines a virtual server |
| `listen` | Configures listening address and port |
| `server_name` | Defines hostname matching |
| `location` | Matches request URIs |
| `proxy_pass` | Forwards requests to upstream servers |
| `proxy_set_header` | Sets headers sent upstream |
| `upstream` | Defines a group of backend servers |
| `root` | Sets the document root |
| `index` | Defines default index files |
| `ssl_certificate` | Specifies certificate file |
| `ssl_certificate_key` | Specifies private key file |

## 12.4 Basic Reverse Proxy Configuration

Suppose your Next.js application runs on port 3000.

Create:

```bash
sudo nano /etc/nginx/sites-available/myapp
```

Configuration:

```nginx
server {
    listen 80;

    server_name example.com www.example.com;

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

Enable the configuration:

```bash
sudo ln -s \
/etc/nginx/sites-available/myapp \
/etc/nginx/sites-enabled/myapp
```

Test the configuration:

```bash
sudo nginx -t
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

Now:

```text
http://example.com
```

is forwarded to:

```text
http://127.0.0.1:3000
```

## 12.5 What Is `proxy_pass`?

`proxy_pass` specifies the backend destination.

Example:

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:4000;
}
```

When Nginx receives a request under `/api/`, it forwards the request to the specified backend.

**Important:** The trailing slash and URI specified in `proxy_pass` affect URI rewriting behavior.

For example:

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:4000/;
}
```

This configuration replaces the matching `/api/` prefix with `/` when constructing the upstream URI.

Always test the resulting path when changing `proxy_pass` syntax.

## 12.6 Configure Next.js and Express Together

Suppose:

- Next.js runs on port 3000.
- Express runs on port 4000.

Nginx configuration:

```nginx
server {
    listen 80;

    server_name example.com;

    location /api/ {
        proxy_pass http://127.0.0.1:4000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_set_header X-Forwarded-Proto $scheme;
    }

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

Architecture:

```text
                   Users
                     |
                     v
                   Nginx
                     |
          ┌──────────┴──────────┐
          |                     |
          v                     v
      /api/*                  /*
          |                     |
          v                     v
     Express API            Next.js
      Port 4000             Port 3000
```

This is useful when hosting a frontend and backend on the same domain.

---

# 13. Nginx Load Balancing Algorithms

Nginx can distribute incoming requests among multiple backend servers.

## 13.1 Upstream Servers

An upstream group defines backend servers.

Example:

```nginx
upstream backend {
    server 127.0.0.1:4001;

    server 127.0.0.1:4002;

    server 127.0.0.1:4003;
}
```

Then:

```nginx
server {
    listen 80;

    server_name api.example.com;

    location / {
        proxy_pass http://backend;
    }
}
```

Traffic is distributed across the upstream servers.

## 13.2 Round Robin

Round Robin distributes requests across servers in sequence.

Example:

```text
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
Request 4 → Server A
```

Nginx uses Round Robin by default for standard upstream groups.

## 13.3 Least Connections

The next request is sent to the server with the fewest active connections.

Configuration:

```nginx
upstream backend {
    least_conn;

    server 127.0.0.1:4001;

    server 127.0.0.1:4002;

    server 127.0.0.1:4003;
}
```

Useful when requests have significantly different processing durations.

## 13.4 IP Hash

IP Hash uses the client IP address to select an upstream server.

Configuration:

```nginx
upstream backend {
    ip_hash;

    server 127.0.0.1:4001;

    server 127.0.0.1:4002;

    server 127.0.0.1:4003;
}
```

This can provide a degree of session affinity.

However, it is not a replacement for shared session storage.

## 13.5 Weighted Load Balancing

You can assign different weights to servers.

```nginx
upstream backend {
    server 127.0.0.1:4001 weight=5;

    server 127.0.0.1:4002 weight=2;

    server 127.0.0.1:4003 weight=1;
}
```

The higher-weight server receives a larger share of traffic under weighted Round Robin.

## 13.6 Comparison

| Algorithm | Description |
|---|---|
| Round Robin | Distributes requests sequentially |
| Least Connections | Chooses server with fewer active connections |
| IP Hash | Uses client IP to select server |
| Weighted Round Robin | Distributes traffic according to server weights |

**Important:** Nginx Open Source supports several load-balancing methods. Some advanced methods and session-persistence capabilities are specific to Nginx Plus.

Official references:

- [NGINX – Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy)
- [NGINX – HTTP Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)

---

# 14. WebSockets and Network Proxies

WebSockets provide full-duplex communication over a persistent connection.

They are useful for:

- Chat applications.
- Live notifications.
- Multiplayer games.
- Live dashboards.
- Collaborative editing.

## 14.1 Why Are WebSockets Different from HTTP Polling?

With traditional HTTP request-response communication:

```text
Client → Request → Server
Client ← Response ← Server
```

For real-time communication, repeated polling may be inefficient.

WebSockets establish a persistent connection that both client and server can use to exchange messages.

```text
Client  <=================>  Server
         Persistent
         Connection
```

## 14.2 WebSocket Handshake

Traditional WebSocket connections begin with an HTTP Upgrade handshake.

A simplified request:

```http
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: ...
Sec-WebSocket-Version: 13
```

The server responds with:

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: ...
```

The connection then switches to the WebSocket protocol.

## 14.3 WebSocket Proxying with Nginx

When Nginx acts as a reverse proxy for WebSocket traffic, it must forward the appropriate upgrade headers.

Example:

```nginx
map $http_upgrade $connection_upgrade {
    default upgrade;

    '' close;
}

server {
    listen 80;

    server_name chat.example.com;

    location /socket.io/ {
        proxy_pass http://127.0.0.1:5000;

        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;

        proxy_set_header Connection $connection_upgrade;

        proxy_set_header Host $host;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_read_timeout 3600s;
    }
}
```

### Why Is This Required?

WebSocket communication needs an HTTP Upgrade handshake.

The proxy must preserve the relevant headers and use HTTP/1.1 for this traditional upgrade flow.

## 14.4 WebSocket vs Socket.IO

| WebSocket | Socket.IO |
|---|---|
| Protocol and browser API | Real-time communication library |
| Native browser support | Requires Socket.IO client |
| Low-level communication | Higher-level event-based API |
| No built-in automatic reconnection in browser API | Supports reconnection |
| No built-in rooms | Supports rooms |
| No built-in event acknowledgements | Supports acknowledgements |

**Important:** Socket.IO is not simply another name for WebSocket. It is a separate protocol and library that can use WebSocket as a transport.

---

# 15. Firewalls and Network Security

A firewall controls network traffic according to configured rules.

It helps prevent unauthorized connections.

## 15.1 What Is a Firewall?

A firewall can control:

- Incoming traffic.
- Outgoing traffic.
- IP addresses.
- Ports.
- Protocols.
- Network zones.

Example:

```text
Allow:
Port 80
Port 443

Restrict:
Port 22

Deny:
Unnecessary inbound ports
```

## 15.2 Types of Firewalls

### 1. Host-Based Firewall

Runs on an individual machine.

Examples:

- UFW.
- Windows Defender Firewall.
- Linux nftables.

### 2. Network Firewall

Protects a network boundary or segment.

Examples:

- AWS Network Firewall.
- Traditional hardware firewalls.
- Cloud network security appliances.

### 3. Web Application Firewall (WAF)

A WAF protects HTTP applications from certain application-layer attacks.

It can help detect or block:

- SQL injection patterns.
- Cross-site scripting patterns.
- Malicious request patterns.
- Automated bot traffic.

A WAF does not replace secure application code.

## 15.3 UFW – Uncomplicated Firewall

UFW is a firewall management tool commonly used on Ubuntu.

Check status:

```bash
sudo ufw status
```

Allow SSH:

```bash
sudo ufw allow 22/tcp
```

Allow HTTP:

```bash
sudo ufw allow 80/tcp
```

Allow HTTPS:

```bash
sudo ufw allow 443/tcp
```

Enable:

```bash
sudo ufw enable
```

Remove a rule:

```bash
sudo ufw delete allow 80/tcp
```

### Important

Before enabling UFW on a remote VPS, ensure that your SSH access is permitted.

Otherwise, you may lock yourself out.

## 15.4 Network Security Best Practices

1. Expose only required ports.
2. Use HTTPS.
3. Keep operating systems updated.
4. Restrict SSH access.
5. Use key-based authentication.
6. Disable unnecessary services.
7. Use private networking for databases.
8. Apply firewall rules.
9. Monitor network traffic.
10. Avoid exposing internal application ports publicly.

---

# 16. Ports, Sockets and Network Interfaces

These concepts are closely related but not identical.

## 16.1 What Is a Port?

A port identifies a transport-layer endpoint on a host.

Example:

```text
192.168.1.10:3000
```

Here:

- `192.168.1.10` is the IP address.
- `3000` is the port number.

A server can listen on a port for incoming connections.

## 16.2 Common Port Numbers

| Port | Service |
|---:|---|
| 20/21 | FTP data/control |
| 22 | SSH |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 27017 | MongoDB |
| 3000 | Common development server port |
| 4000 | Common custom API port |

These are common defaults, not mandatory port assignments.

## 16.3 What Is a Socket?

A socket is a communication endpoint used by a process.

For TCP, a connection is identified by information including:

- Source IP.
- Source port.
- Destination IP.
- Destination port.
- Transport protocol.

Example:

```text
Client:
192.168.1.10:53000

Server:
203.0.113.10:443

Protocol:
TCP
```

These values identify a particular network connection.

## 16.4 What Is a Network Interface?

A network interface connects a device or virtual machine to a network.

Examples:

- Ethernet interface.
- Wi-Fi interface.
- Loopback interface.
- Virtual network interface.

Linux examples:

```bash
ip addr
```

or:

```bash
ip link
```

## 16.5 What Is Localhost?

Localhost refers to the loopback interface.

Common IPv4 address:

```text
127.0.0.1
```

Common IPv6 address:

```text
::1
```

When your application connects to:

```text
http://127.0.0.1:3000
```

it connects to a service on the same network namespace.

### Important: Localhost in Docker

Inside a Docker container:

```text
127.0.0.1
```

usually refers to that container itself, not the host machine or another container.

For example, if Express and MongoDB are separate Docker Compose services, Express should usually connect using the MongoDB service name.

```text
mongodb://mongo:27017/mydb
```

rather than:

```text
mongodb://localhost:27017/mydb
```

---

# 17. NAT and Port Forwarding

## 17.1 What Is NAT?

NAT stands for **Network Address Translation**.

It modifies network address information as packets pass through a network device.

A common use case is allowing multiple private IPv4 devices to share a public IP address.

Example:

```text
Laptop: 192.168.1.10
Phone:  192.168.1.11
PC:     192.168.1.12

          |
          v
        Router
          |
          v
    Public IP Address
          |
          v
       Internet
```

The router tracks connections and translates addresses and often port numbers.

## 17.2 What Is PAT?

PAT stands for Port Address Translation.

It allows multiple internal connections to share a public IP address using different port mappings.

PAT is commonly used in home and office routers.

## 17.3 What Is Port Forwarding?

Port forwarding directs incoming traffic arriving at a particular public IP and port to an internal device.

Example:

```text
Public IP: 203.0.113.10
Port: 8080

        |
        v
Router
        |
        v
192.168.1.10:3000
```

A router may forward traffic arriving at:

```text
203.0.113.10:8080
```

to:

```text
192.168.1.10:3000
```

## 17.4 Port Forwarding vs Reverse Proxy

| Port Forwarding | Reverse Proxy |
|---|---|
| Network-level forwarding | Application-aware request forwarding |
| Commonly configured on routers/firewalls | Runs as a proxy server |
| Maps IP and port combinations | Can route using hostname, path and HTTP details |
| Does not inherently provide HTTP application routing | Supports HTTP routing and other proxy features |

### Example

A home router forwards:

```text
Public:8080 → Internal:3000
```

Nginx reverse proxy routes:

```text
example.com/api → Express:4000

example.com/ → Next.js:3000
```

These are different mechanisms.

---

# 18. Network Troubleshooting Tools

Network troubleshooting is an essential skill for backend and DevOps engineers.

You should understand how to identify:

- DNS problems.
- Connection failures.
- Port issues.
- Routing issues.
- TLS certificate problems.
- HTTP errors.
- Firewall blocks.

Let's examine important commands.

## 18.1 `ping`

Checks basic IP connectivity using ICMP Echo messages.

```bash
ping google.com
```

Example:

```text
64 bytes from ...: icmp_seq=1 ttl=... time=...
```

It can help identify connectivity and latency issues.

**Important:** A server may block ICMP traffic while still allowing HTTP or HTTPS. A failed ping does not automatically mean that the server is offline.

## 18.2 `traceroute`

Shows the network hops taken toward a destination.

Linux:

```bash
traceroute google.com
```

Windows:

```cmd
tracert google.com
```

Useful for investigating routing paths and where traffic may be delayed or filtered.

## 18.3 `nslookup`

Checks DNS resolution.

```bash
nslookup example.com
```

Query a specific DNS resolver:

```bash
nslookup example.com 8.8.8.8
```

## 18.4 `dig`

A more detailed DNS troubleshooting tool.

```bash
dig example.com
```

Query an A record:

```bash
dig example.com A
```

Query MX:

```bash
dig example.com MX
```

Query nameservers:

```bash
dig example.com NS
```

Query a specific resolver:

```bash
dig @8.8.8.8 example.com
```

## 18.5 `curl`

`curl` is used to make HTTP requests and inspect responses.

Check headers:

```bash
curl -I https://example.com
```

Verbose output:

```bash
curl -v https://example.com
```

Test a particular HTTP method:

```bash
curl -X POST \
https://api.example.com/users \
-H "Content-Type: application/json" \
-d '{"name":"Test"}'
```

Test a particular server IP while preserving hostname and TLS SNI:

```bash
curl --resolve example.com:443:203.0.113.10 \
https://example.com
```

This is particularly useful when testing a new server before changing DNS.

## 18.6 `netstat`

Displays network connections and listening ports.

Example:

```bash
netstat -tulnp
```

Modern Linux systems often use `ss` instead.

## 18.7 `ss`

Check listening ports:

```bash
ss -tulnp
```

Check TCP connections:

```bash
ss -tn
```

Check a particular port:

```bash
ss -tulnp | grep 3000
```

## 18.8 `lsof`

Check which process is using a port:

```bash
sudo lsof -i :3000
```

This helps identify port conflicts.

## 18.9 `openssl`

Inspect a TLS certificate:

```bash
openssl s_client \
-connect example.com:443 \
-servername example.com
```

View certificate details:

```bash
openssl s_client \
-connect example.com:443 \
-servername example.com \
</dev/null 2>/dev/null |
openssl x509 -noout -subject -issuer -dates
```

## 18.10 Troubleshooting Command Summary

| Tool | Purpose |
|---|---|
| `ping` | Basic IP connectivity |
| `traceroute` | Network path |
| `tracert` | Windows route tracing |
| `nslookup` | DNS lookup |
| `dig` | Detailed DNS lookup |
| `curl` | HTTP/HTTPS testing |
| `ss` | Socket and listening-port inspection |
| `netstat` | Network connection information |
| `lsof` | Process-to-port mapping |
| `openssl` | TLS certificate inspection |
| `ip addr` | Network interface addresses |
| `ip route` | Routing table |
| `tcpdump` | Packet capture |
| `mtr` | Combined route and packet-loss analysis |

---

# 19. Production Network Architecture

Now let's combine the concepts into a practical production deployment.

Suppose you deploy:

- Next.js 16 frontend.
- Express backend.
- MongoDB.
- MySQL.
- Redis.
- Nginx.
- AWS infrastructure.

## 19.1 High-Level Architecture

```text
                       USERS
                         |
                         v
                      DNS
                         |
                         v
                   CloudFront
                         |
                         v
                  Application WAF
                         |
                         v
                Load Balancer / ALB
                         |
                         v
                  Private Network
                         |
              ┌──────────┴──────────┐
              |                     |
              v                     v
          Next.js                Express
          Port 3000              Port 4000
              |                     |
              └──────────┬──────────┘
                         |
              ┌──────────┼──────────┐
              |          |          |
              v          v          v
             RDS       MongoDB     Redis
            MySQL       Atlas    ElastiCache
```

This is a conceptual design. The exact CloudFront, WAF and load-balancer placement depends on the chosen routing architecture.

## 19.2 Request Flow

Suppose a user opens:

```text
https://example.com/products
```

The process:

1. Browser checks its DNS cache.
2. DNS resolver resolves `example.com`.
3. Browser connects to the appropriate CloudFront or application endpoint.
4. TLS establishes a secure connection.
5. HTTP request is sent.
6. CloudFront evaluates its cache behavior.
7. If necessary, the request reaches the origin.
8. The load balancer selects a healthy application target.
9. Next.js handles the frontend request.
10. Express handles backend API requests.
11. Express communicates with the database or cache.
12. The response returns through the network.
13. The browser renders the result.

## 19.3 Example: API Request

The browser sends:

```ts
const response = await fetch(
  "https://example.com/api/products"
);
```

Possible routing:

```text
Browser
   |
   v
DNS
   |
   v
CloudFront / ALB
   |
   v
Nginx or Application Routing
   |
   v
Express API
   |
   v
Redis Cache
   |
   | Cache Miss
   v
MongoDB / MySQL
   |
   v
Express Response
   |
   v
Browser
```

## 19.4 Network Security Rules

A production design should avoid exposing unnecessary ports.

Example:

| Port | Access |
|---:|---|
| 80 | Public HTTP, normally redirected to HTTPS |
| 443 | Public HTTPS |
| 22 | Restricted administrative access |
| 3000 | Private application access only |
| 4000 | Private API access only |
| 3306 | Authorized database clients only |
| 27017 | Authorized MongoDB clients only |
| 6379 | Authorized Redis clients only |

Do not open database ports to the entire internet.

---

# 20. Common Networking Mistakes

| Mistake | Correct Understanding |
|---|---|
| Thinking DNS directly sends HTTP traffic | DNS resolves names; application connections happen afterward |
| Thinking a domain and hosting are the same | Domain registration and web hosting are separate services |
| Confusing public and private IPs | They have different routing properties |
| Thinking TCP and UDP are interchangeable | They provide different transport semantics |
| Assuming HTTPS means a website is trustworthy | HTTPS protects the connection, not the site's business behavior |
| Using an expired TLS certificate | Automate renewal and monitor certificate expiry |
| Exposing Node.js port 3000 publicly | Prefer controlled access through a reverse proxy or load balancer |
| Confusing forward and reverse proxies | They serve different network roles |
| Assuming Nginx automatically balances traffic | Configure an upstream group and appropriate routing |
| Forgetting WebSocket upgrade headers | Configure the proxy for WebSocket connections |
| Assuming localhost always means the host computer | It refers to the current network namespace |
| Ignoring DNS TTL | Cached DNS responses may remain in use |
| Opening MySQL and MongoDB ports publicly | Restrict database network access |
| Assuming ping failure means server failure | ICMP may be blocked independently |
| Ignoring connection timeouts | Configure appropriate upstream and application timeouts |

---

# 21. Interview Questions and Answers

## Basic Level

### Q1. What is computer networking?

Computer networking is the process of connecting devices so they can communicate and exchange data.

### Q2. What is an IP address?

An IP address is a logical address used to identify a network interface for IP communication.

### Q3. What is the difference between IPv4 and IPv6?

IPv4 uses 32-bit addresses, whereas IPv6 uses 128-bit addresses.

### Q4. What is DNS?

DNS translates domain names into information used to locate network services, commonly IP addresses.

### Q5. What is HTTP?

HTTP is an application-layer protocol used for communication between web clients and servers.

### Q6. What is HTTPS?

HTTPS is HTTP secured using TLS.

### Q7. What is TCP?

TCP is a connection-oriented transport protocol that provides reliable and ordered byte-stream delivery.

### Q8. What is UDP?

UDP is a connectionless transport protocol that provides datagram delivery without built-in reliability or ordering guarantees.

### Q9. What is a port?

A port identifies a transport-layer endpoint associated with an application or service.

### Q10. What is a subnet?

A subnet is a logical division of an IP network.

## Intermediate Level

### Q11. What is the OSI model?

The OSI model is a conceptual seven-layer model used to understand network communication.

### Q12. What is the difference between TCP and UDP?

TCP provides reliable, ordered delivery. UDP does not provide those guarantees and has lower protocol overhead.

### Q13. What is a reverse proxy?

A reverse proxy receives client requests and forwards them to backend servers.

### Q14. What is the difference between a reverse proxy and a forward proxy?

A forward proxy represents clients accessing external resources. A reverse proxy represents backend servers receiving client requests.

### Q15. What is a DNS A record?

An A record maps a hostname to an IPv4 address.

### Q16. What is a CNAME record?

A CNAME record maps one hostname to another canonical hostname.

### Q17. What is TTL in DNS?

TTL specifies how long DNS information may be cached.

### Q18. What is TLS?

TLS is a cryptographic protocol that provides secure communication through encryption, authentication and integrity protection.

### Q19. What is a Certificate Authority?

A Certificate Authority is a trusted entity that issues digital certificates.

### Q20. What is Nginx?

Nginx is a web server that can also function as a reverse proxy, load balancer and content delivery server.

## Advanced Level

### Q21. Explain the TCP three-way handshake.

The client sends SYN, the server responds with SYN-ACK, and the client sends ACK. This establishes the TCP connection.

### Q22. Explain DNS resolution.

A recursive resolver obtains DNS information by consulting cached information or querying the DNS hierarchy, including root, TLD and authoritative servers.

### Q23. How does HTTPS work?

HTTPS uses TLS to authenticate the server, establish shared cryptographic keys and protect HTTP communication.

### Q24. What is the difference between TLS and SSL?

SSL is an obsolete predecessor of TLS. Modern secure web applications should use current TLS versions.

### Q25. Why do we use Nginx in production?

Nginx can provide reverse proxying, HTTPS termination, load balancing, static file serving, buffering and request routing.

### Q26. What happens when you enter a domain in a browser?

The browser resolves the domain, establishes a connection, performs TLS negotiation for HTTPS, sends an HTTP request and processes the response.

### Q27. What is NAT?

NAT translates network address information as packets move between network boundaries.

### Q28. What is the difference between NAT and port forwarding?

NAT translates network addresses. Port forwarding is a specific configuration that maps incoming traffic on an external address and port to an internal destination.

### Q29. How would you troubleshoot a website that is not opening?

I would investigate:

1. DNS resolution.
2. Network reachability.
3. TCP connection.
4. Firewall rules.
5. TLS certificate.
6. Reverse proxy configuration.
7. Application health.
8. Server logs.

### Q30. Why does a website return 502 Bad Gateway?

A 502 response usually means a gateway or reverse proxy received an invalid response from an upstream server.

Possible causes include:

- Backend application is down.
- Incorrect upstream port.
- Connection refused.
- Invalid upstream response.
- Network configuration problem.

### Q31. Why does a website return 504 Gateway Timeout?

A 504 occurs when a gateway or proxy does not receive a timely response from an upstream server.

Possible causes include:

- Slow database query.
- Backend processing delay.
- Network latency.
- Incorrect timeout configuration.
- Overloaded application server.

### Q32. How do you configure WebSockets behind Nginx?

Configure HTTP/1.1 proxying and forward the `Upgrade` and `Connection` headers, along with appropriate timeouts.

### Q33. What is the difference between HTTP/2 and HTTP/3?

HTTP/2 runs over TCP, whereas HTTP/3 uses QUIC over UDP.

### Q34. Why should database ports not be publicly exposed?

Public database exposure increases the attack surface. Databases should generally accept connections only from authorized application networks.

### Q35. What is the difference between a socket and a port?

A port is a transport-layer identifier. A socket is a communication endpoint used by a process and may be associated with an IP address, port and transport protocol.

---

# 22. Practical Assignments

## Assignment 1: Understand Your Network

Use your own computer to inspect network configuration.

Windows commands:

```cmd
ipconfig
```

```cmd
ipconfig /all
```

Linux commands:

```bash
ip addr
```

```bash
ip route
```

Identify:

- [ ] Your local IPv4 address.
- [ ] Your default gateway.
- [ ] Your DNS resolver.
- [ ] Your network interface.
- [ ] Whether your address is private or public.

## Assignment 2: Practice DNS

Execute:

```bash
nslookup google.com
```

```bash
nslookup github.com
```

```bash
dig google.com
```

```bash
dig google.com MX
```

Identify:

- [ ] A records.
- [ ] AAAA records.
- [ ] MX records.
- [ ] NS records.
- [ ] TTL values.

## Assignment 3: Practice HTTP and HTTPS

Execute:

```bash
curl -I https://example.com
```

```bash
curl -v https://example.com
```

```bash
curl -I http://example.com
```

Understand:

- [ ] HTTP status codes.
- [ ] Response headers.
- [ ] Redirects.
- [ ] TLS connection information.
- [ ] Difference between HTTP and HTTPS.

## Assignment 4: Deploy Nginx Reverse Proxy

Create an Express application.

```ts
import express from "express";

const app = express();

app.get("/", (_req, res) => {
  res.send("Hello from Express");
});

app.get("/health", (_req, res) => {
  res.json({
    status: "healthy",
  });
});

app.listen(4000, () => {
  console.log("Server running on port 4000");
});
```

Start the application:

```bash
node app.js
```

Configure Nginx to forward requests to port 4000.

Verify:

```bash
curl http://localhost
```

Complete:

- [ ] Install Nginx.
- [ ] Start Express.
- [ ] Configure Nginx.
- [ ] Test reverse proxy.
- [ ] Configure domain.
- [ ] Configure HTTPS.

## Assignment 5: Configure Load Balancing

Run three Express instances on:

```text
4001
4002
4003
```

Use different responses:

```text
Server A
Server B
Server C
```

Configure an Nginx upstream group.

Test:

```bash
curl http://localhost
```

Observe how requests are distributed.

Complete:

- [ ] Configure upstream.
- [ ] Configure Round Robin.
- [ ] Configure Least Connections.
- [ ] Configure IP Hash.
- [ ] Test server failure behavior.

## Assignment 6: Practice WebSocket Proxying

Create a WebSocket or Socket.IO application.

Configure Nginx to forward WebSocket connections.

Complete:

- [ ] Create WebSocket server.
- [ ] Connect browser client.
- [ ] Configure Nginx.
- [ ] Forward upgrade headers.
- [ ] Test reconnection.
- [ ] Test long-lived connections.

## Assignment 7: Network Troubleshooting

Simulate common problems in a test environment.

Investigate:

- [ ] DNS resolution failure.
- [ ] Incorrect server port.
- [ ] Application not running.
- [ ] Firewall blocking traffic.
- [ ] Invalid TLS certificate.
- [ ] Nginx upstream connection failure.
- [ ] Backend timeout.

Use:

```bash
ping
traceroute
nslookup
dig
curl
ss
lsof
openssl
```

---

# 23. Quick Revision Notes

| Topic | Key Point |
|---|---|
| Networking | Communication between connected devices |
| OSI Model | Seven-layer conceptual model |
| TCP/IP | Practical Internet networking model |
| IPv4 | 32-bit addressing |
| IPv6 | 128-bit addressing |
| CIDR | Network prefix notation |
| Subnet | Logical IP network division |
| TCP | Reliable, ordered byte-stream transport |
| UDP | Connectionless datagram transport |
| HTTP | Web application protocol |
| HTTPS | HTTP protected by TLS |
| DNS | Resolves domain names |
| A Record | IPv4 address mapping |
| AAAA Record | IPv6 address mapping |
| CNAME | Hostname alias |
| MX | Email routing |
| TXT | Text-based DNS information |
| NS | Nameserver delegation |
| TTL | DNS cache duration |
| TLS | Secure communication protocol |
| SSL | Obsolete predecessor of TLS |
| CA | Certificate Authority |
| Reverse Proxy | Forwards incoming requests to backend servers |
| Forward Proxy | Mediates client outbound requests |
| Nginx | Web server and reverse proxy |
| Load Balancer | Distributes traffic across servers |
| WebSocket | Persistent full-duplex communication |
| Firewall | Controls network traffic |
| Port | Transport-layer endpoint identifier |
| Socket | Process communication endpoint |
| NAT | Network address translation |
| Port Forwarding | Maps external traffic to an internal destination |
| `ping` | Connectivity diagnostic |
| `dig` | DNS diagnostic |
| `curl` | HTTP/HTTPS testing |
| `ss` | Socket inspection |
| `openssl` | TLS inspection |

---

# 24. Phase 39 Completion Checklist

You should now be able to explain and practically work with:

- [ ] Computer networking fundamentals.
- [ ] OSI model and TCP/IP model.
- [ ] Data encapsulation and decapsulation.
- [ ] IPv4 and IPv6.
- [ ] Public and private IP addresses.
- [ ] CIDR and subnetting.
- [ ] TCP and UDP.
- [ ] TCP three-way handshake.
- [ ] HTTP methods and status codes.
- [ ] HTTP/1.1, HTTP/2 and HTTP/3.
- [ ] DNS resolution.
- [ ] DNS record types.
- [ ] DNS TTL and propagation.
- [ ] Domain names and nameservers.
- [ ] TLS handshake.
- [ ] Public and private keys.
- [ ] Digital certificates.
- [ ] Certificate Authorities.
- [ ] Let's Encrypt and ACME.
- [ ] Reverse proxy architecture.
- [ ] Forward proxy vs reverse proxy.
- [ ] Nginx configuration.
- [ ] Nginx load balancing.
- [ ] WebSocket proxy configuration.
- [ ] Firewalls and UFW.
- [ ] Ports and sockets.
- [ ] NAT and port forwarding.
- [ ] Network troubleshooting tools.
- [ ] Production network architecture.

---

# 25. Official Learning Resources

| Topic | Documentation |
|---|---|
| Network Layers | [Cloudflare Network Layers](https://developers.cloudflare.com/fundamentals/reference/network-layers/) |
| HTTP | [MDN HTTP Documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP) |
| HTTP Specifications | [MDN HTTP Specifications](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Resources_and_specifications) |
| DNS | [Cloudflare DNS Documentation](https://developers.cloudflare.com/dns/) |
| Nginx Reverse Proxy | [NGINX Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy) |
| Nginx Load Balancing | [NGINX Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/) |
| TLS Certificates | [Let's Encrypt](https://letsencrypt.org/) |
| TLS Certificate Process | [Let's Encrypt – How It Works](https://letsencrypt.org/how-it-works/) |
| TLS and Networking Standards | [RFC Editor](https://www.rfc-editor.org/) |
| Ubuntu Networking | [Ubuntu Server Documentation](https://ubuntu.com/server/docs/) |

---

# 26. What Comes Next?

## Phase 40 – Linux Administration, Shell Scripting and Server Management

In the next phase, we will study Linux from a server-administration and DevOps perspective.

Planned topics:

1. Linux Fundamentals and Architecture.
2. Linux Distributions.
3. Linux Kernel and Shell.
4. Linux File System Hierarchy.
5. Essential Linux Commands.
6. File and Directory Management.
7. File Permissions and Ownership.
8. Users and Groups.
9. Process Management.
10. System Services and systemd.
11. Package Management with APT.
12. Bash Shell Scripting.
13. Variables and Environment Variables.
14. Conditions and Loops.
15. Functions in Bash.
16. Cron Jobs and Scheduled Tasks.
17. Log Management and Log Rotation.
18. Disk and Memory Management.
19. Linux Networking Commands.
20. SSH and Secure Server Access.
21. Linux Security and Hardening.
22. Production Server Administration.
23. Linux Troubleshooting.

**Phase 39 completed.**

When you're ready, say **"OK, Phase 40"** to continue.