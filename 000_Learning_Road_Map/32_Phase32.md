# **Phase 32 – Caching Strategies, Redis and Distributed Caching**



Advanced Level

Backend & System Design



**Technology context:** Node.js, TypeScript, Redis, Next.js 16 App Router, MongoDB and MySQL.

## **1. Learning Objectives**

By the end of this phase, you will understand how to:

1. Explain caching and why production applications need it.
2. Distinguish cache hits, cache misses, and cache invalidation.
3. Understand browser, CDN, application, database, and distributed caching.
4. Work with Redis and its core data structures.
5. Integrate Redis with Node.js and Next.js.
6. Implement cache-aside, read-through, write-through, and write-behind patterns.
7. Handle TTL, expiration, eviction, and stale data.
8. Prevent cache stampedes and cache penetration.
9. Understand Redis Pub/Sub, transactions, Lua scripts, persistence, and high availability.
10. Design caching strategies for scalable applications and microservices.

## **2. What Is Caching?**

**Caching** is the process of temporarily storing frequently accessed data in a faster storage layer so that future requests can retrieve it more quickly.

Consider an e-commerce application.

When a user opens a product page, the application may need to:

1. Receive the HTTP request.
2. Query MongoDB or MySQL.
3. Retrieve product information.
4. Process the result.
5. Return the response.

If 10,000 users request the same product, repeatedly querying the database can create unnecessary load.

Instead, the application can store the product data in a cache, such as Redis.

**Without caching**

**10,000 user requests**

**Database receives 10,000 queries**

Repeated work and increased database load

**With caching**

**10,000 user requests**

**Redis cache**

Most requests receive cached data

**Database**

Queried when data is missing or needs refreshing

Caching can improve:

- **Response time:** Data can be retrieved from memory instead of performing expensive database operations.
- **Database performance:** Fewer repeated queries.
- **Scalability:** The application can handle more traffic with the same database resources.
- **Cost efficiency:** Less database and backend resource consumption.

**Important:** A cache is generally not the primary source of truth. Your database usually remains the authoritative storage for business data.

## **3. Cache Hit vs Cache Miss**

Two fundamental caching concepts are a cache hit and a cache miss.

### **3.1 Cache Hit**

A cache hit occurs when the requested data already exists in the cache and is usable.

Example:

```
User requests product 101
        |
        v
Check Redis
        |
        v
Product exists
        |
        v
Return cached product
```

The database does not need to be queried for that request.

### **3.2 Cache Miss**

A cache miss occurs when the requested data is not available in the cache.

Example:

```
User requests product 101
        |
        v
Check Redis
        |
        v
Product not found
        |
        v
Query MongoDB
        |
        v
Store result in Redis
        |
        v
Return product
```

### **3.3 Cache Hit Ratio**

The cache hit ratio measures how often requests find their data in the cache.

\\[ \text{Hit Ratio} = \frac{\text{Cache Hits}}{\text{Total Cache Requests}}\times100 \\]

Example:

- Total cache requests: 1,000
- Cache hits: 850
- Cache misses: 150

\\[ \text{Hit Ratio} = \frac{850}{1000}\times100 = 85\\% \\]

**Example cache performance**

# **85%**

Cache hit ratio

850 hits

150 misses

A high hit ratio is often desirable, but it does not automatically mean the caching strategy is good. You must also consider data freshness, memory usage, latency, and the cost of cache misses.

## **4. Different Types of Caching**

Caching can happen at multiple layers of an application. Understanding these layers is important because Redis is only one part of a complete caching architecture.

### **4.1 Browser Caching**

The browser stores certain resources locally so it does not need to download them on every visit.

Examples:

- Images
- CSS files
- JavaScript bundles
- Fonts
- Static documents

HTTP headers control browser caching.

```
Cache-Control: public, max-age=3600
```

This tells a browser that the response can be considered fresh for 3,600 seconds (one hour), subject to the applicable HTTP caching rules.

Another example:

```
Cache-Control: no-store
```

This tells compliant caches not to store the response.

Common directives:

| Directive         | Meaning                                        |
| ----------------- | ---------------------------------------------- |
| `public`          | May be stored by shared caches                 |
| `private`         | Intended for private caches, such as a browser |
| `max-age=3600`    | Freshness lifetime of 3,600 seconds            |
| `no-cache`        | May store, but must revalidate before reuse    |
| `no-store`        | Do not store the response                      |
| `must-revalidate` | Must revalidate once stale                     |

**Important distinction:** `no-cache` does not mean "do not cache." It means the stored response must be validated before it is reused.

### **4.2 CDN Caching**

A Content Delivery Network (CDN) caches content at geographically distributed edge locations.

For example, suppose your application server is hosted in Mumbai and a user accesses it from London.

Without a CDN, the request may need to travel to your origin server.

With a CDN, a cached image or static asset may be served from an edge location closer to the user.

[🌐 How to Design a Content Delivery Network (CDN) from Scratch | by Yatin | System Design for Beginners | Medium](https://images.openai.com/static-rsc-4/4bl2Qy8z0iPD2288R8BQ57KAz1MCEVX9M2ngf-_BY-XHwYYGMNSRx_a-QowanHx4sISk232sZQ1TWHHWJcWvnALy0POl71lCZBm-mpMS1oYrdYERw4wfxLufUJZH6JIcvYKPppjDQTubX_2QnJA_DYF392DIL5VaixRH5D_sSJg?purpose=inline)

[medium.com](https://medium.com/system-design-for-beginners-roadmap-to-mastery/how-to-design-a-content-delivery-network-cdn-from-scratch-c3f9d0e54d2b?utm_source=chatgpt.com)

Common CDN providers include:

- Cloudflare
- Amazon CloudFront
- Fastly

CDN caching is particularly useful for static assets, images, downloadable files, and cacheable API responses.

### **4.3 Application-Level Caching**

Application-level caching stores data used by backend application logic.

For example:

```
GET /api/products/101
        |
        v
Node.js application
        |
        v
Redis
        |
   Cache hit?
    /     \
  Yes      No
   |        |
Return   MongoDB
data       |
           v
        Redis SET
           |
           v
        Return data
```

Redis is frequently used for this layer.

### **4.4 Database Caching**

Databases may maintain internal caches for frequently accessed data, indexes, and query-related operations.

For example, database engines can use memory to reduce repeated disk reads.

However, database internal caching is not the same as application-level Redis caching.

| Feature             | Database internal cache      | Redis application cache          |
| ------------------- | ---------------------------- | -------------------------------- |
| Managed by          | Database engine              | Application/infrastructure       |
| Main purpose        | Optimize database operations | Avoid repeated database work     |
| Application control | Usually limited              | Explicit keys, TTL, invalidation |
| Data access         | Through database queries     | Direct cache commands            |

### **4.5 In-Memory Caching**

In-memory caching stores data in RAM rather than repeatedly accessing slower storage.

Example using JavaScript:

```
const cache = new Map<string, unknown>();

cache.set("product:101", {
  id: 101,
  name: "Laptop",
  price: 75000,
});

const product = cache.get("product:101");
```

This is very fast, but there is an important limitation.

If you run three Node.js application instances:

```
Instance 1 -> Its own memory cache
Instance 2 -> Its own memory cache
Instance 3 -> Its own memory cache
```

Each instance maintains separate cache data.

Consequently:

- A value updated in instance 1 may remain stale in instance 2.
- Restarting an instance loses its in-memory cache.
- Cache memory is duplicated across instances.

This is one reason distributed caches such as Redis are useful.

## **5. What Is Redis?**

[How To Install Redis In Raspberry Pi - Redis Png,Raspbian Icon - free transparent png images - pngaaa.com](https://images.openai.com/static-rsc-4/8Fbuy2eoCLdltLVSk9wcmZ8L8OynKHTuFRgPWJsY1KhfiEqfU58EwEtYhi0wAUKjwBfgFuurkQyf7ZYn8y7NJvOFr12WpuGPAfqiiFEx-u_OSBwQM_BzRXfI3xroreS77Ct0lbDVHwV0EtjAvGqsN91aJqoTrIbECh2MyexffCo?purpose=inline)

**Redis**

In-memory data structure server

[Official Redis documentation](https://redis.io/docs/latest/?utm_source=chatgpt.com)

Redis is an in-memory data structure server that supports multiple data structures and operations.

It is commonly used for:

- Caching
- Session storage
- Rate limiting
- Real-time counters
- Leaderboards
- Pub/Sub messaging
- Queues and event processing
- Distributed coordination

Redis is often called an in-memory database because its primary data operations are performed in memory. It also supports persistence options, so it is not accurate to assume Redis data must always disappear after a restart.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Redis+1



### **5.1 Redis Architecture**

[Redis Explained: A Complete Beginner's Guide (2026) | Xcademia](https://images.openai.com/static-rsc-4/f1hcunl6dtN2H0HqlY6DRmO6F16vE_80pMBb0QCEbqId1cEhKKz3TGAEK-nUFcPzgUTWgsWCAWn-mCMrWYx5W2LPrKeKd_r3yMx1sZtoqmlTwEfLvGdkR1ZiefJZfkheuTc1btxORkErEVr-buuCU8aPZHSwUOkq8XIre0DWHRA?purpose=inline)

[xcademia.com](https://xcademia.com/insights/redis-explained-a-complete-beginner-s-guide?utm_source=chatgpt.com)

A simplified architecture:

```
       Next.js Application
               |
               v
        Node.js Backend
               |
               v
         Redis Client
               |
        Network Connection
               |
               v
          Redis Server
               |
        +------+------+
        |             |
        v             v
      RAM          Persistence
                   (Optional)
```

The application communicates with Redis through a Redis client library.

Redis commonly listens on port:

```
6379
```

### **5.2 How Redis Stores Data**

Redis uses keys to identify stored values.

For example:

```
product:101
user:25
session:abc123
cart:user:25
```

A simple Redis key-value pair:

```
Key:   product:101
Value: {"id":101,"name":"Laptop","price":75000}
```

You can retrieve the value using:

```
GET product:101
```

Redis commands can be executed using the Redis CLI or a programming-language client.

## **6. Redis Data Types**

Redis is not limited to simple key-value strings. It provides several native data structures.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs+1



### **6.1 Strings**

Strings are the simplest Redis data type.

Use cases:

- Cached JSON responses
- Tokens
- Counters
- Flags
- Simple values

```
SET username "riyaz"
GET username
```

Output:

```
"riyaz"
```

Incrementing a counter:

```
SET page_views 10
INCR page_views
GET page_views
```

Result:

```
11
```

### **6.2 Hashes**

Hashes store field-value pairs under a single Redis key.

They are useful for storing structured records.

```
HSET user:101 name "Ahmed" age 25 city "Ahmedabad"
```

Retrieve all fields:

```
HGETALL user:101
```

Retrieve one field:

```
HGET user:101 name
```

Use cases:

- User profiles
- Session information
- Product attributes

### **6.3 Lists**

Lists store ordered sequences of strings.

```
LPUSH notifications "Notification 1"
LPUSH notifications "Notification 2"
```

Retrieve items:

```
LRANGE notifications 0 -1
```

Use cases:

- Simple queues
- Recent activity
- Ordered collections

### **6.4 Sets**

Sets store unique values without maintaining a meaningful insertion order.

```
SADD user:101:skills "React" "Node.js" "MongoDB"
```

Check membership:

```
SISMEMBER user:101:skills "React"
```

Get all values:

```
SMEMBERS user:101:skills
```

Use cases:

- Unique tags
- Unique user IDs
- Membership tracking
- Deduplication

### **6.5 Sorted Sets**

Sorted sets store unique members associated with scores.

```
ZADD leaderboard 100 "user:101"
ZADD leaderboard 250 "user:102"
ZADD leaderboard 180 "user:103"
```

Retrieve highest scores first:

```
ZREVRANGE leaderboard 0 2 WITHSCORES
```

Use cases:

- Gaming leaderboards
- Ranking systems
- Priority queues
- Scheduled tasks

### **6.6 Streams**

Redis Streams provide an append-oriented event log with support for consumer groups.

```
XADD orders * orderId 101 status placed
```

Read entries:

```
XRANGE orders - +
```

Use cases:

- Event processing
- Activity logs
- Asynchronous workflows
- Consumer-group-based processing

### **Redis data type comparison**

| Data type  | Stores                  | Example use case |
| ---------- | ----------------------- | ---------------- |
| String     | Simple value            | Cache, counter   |
| Hash       | Field-value record      | User profile     |
| List       | Ordered sequence        | Simple queue     |
| Set        | Unique members          | Unique tags      |
| Sorted Set | Members with scores     | Leaderboard      |
| Stream     | Append-oriented entries | Event processing |

You should choose a Redis data type according to the operations your application needs, rather than storing everything in one format.

## **7. Installing and Configuring Redis**

Before integrating Redis with Node.js, you need a running Redis server.

You can use:

- Redis installed locally.
- Redis running inside Docker.
- A managed Redis service hosted by a cloud provider.

For local development, Docker is a convenient option.

### **7.1 Run Redis with Docker**

```
docker run -d \
  --name redis-server \
  -p 6379:6379 \
  redis:latest
```

Check the running container:

```
docker ps
```

Open the Redis CLI:

```
docker exec -it redis-server redis-cli
```

Test Redis:

```
PING
```

Expected response:

```
PONG
```

For production, pin a tested Redis image version rather than relying on the moving `latest` tag. Configure authentication, private networking, persistence where needed, and resource limits.

### **7.2 Basic Redis commands**

| Command              | Purpose                                  |
| -------------------- | ---------------------------------------- |
| `PING`               | Check server connectivity                |
| `SET key value`      | Store a value                            |
| `GET key`            | Retrieve a value                         |
| `DEL key`            | Delete a key                             |
| `EXISTS key`         | Check whether a key exists               |
| `EXPIRE key seconds` | Set expiration                           |
| `TTL key`            | Get remaining lifetime                   |
| `INCR key`           | Increment a numeric value                |
| `KEYS pattern`       | Find matching keys (avoid in production) |
| `SCAN cursor`        | Incrementally iterate over keys          |

Example:

```
SET product:101 "Laptop"
GET product:101

EXPIRE product:101 60
TTL product:101
```

The key will expire after approximately 60 seconds.

## **8. TTL – Time to Live and Expiration**

**TTL (Time to Live)** is the amount of time a Redis key is allowed to exist before it expires.

Suppose you cache product information for 10 minutes.

```
SET product:101 "Laptop" EX 600
```

Here:

- `product:101` is the key.
- `"Laptop"` is the value.
- `EX 600` sets the expiration to 600 seconds.

Other useful commands:

```
EXPIRE product:101 600
TTL product:101
PERSIST product:101
```

`PERSIST` removes the expiration from an existing key.

You can also use millisecond expiration:

```
SET otp:123456 "valid" PX 30000
```

This key expires after 30,000 milliseconds (30 seconds).

### **8.1 Why is TTL important?**

Imagine your application caches a product:

```
{
  "id": 101,
  "name": "Laptop",
  "price": 75000
}
```

The product price changes in the database to ₹70,000.

If the cached value never expires and you do not invalidate it, users may continue seeing the old price.

TTL places an upper bound on how long an unchanged cache entry can remain available, assuming it is not evicted or invalidated earlier.

Redis supports expiration on keys, and expiration timing is maintained across persistence operations.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs



### **8.2 TTL selection examples**

These are illustrative starting points, not universal rules.

| Data                         | Example TTL             |
| ---------------------------- | ----------------------- |
| Frequently changing stock    | 5–30 seconds            |
| Product information          | 5–15 minutes            |
| Public categories            | 1 hour                  |
| Blog article                 | 30–60 minutes           |
| OTP                          | 30–120 seconds          |
| Temporary verification state | 5 minutes               |
| User session                 | Based on session policy |

**Important:** Do not use TTL as the only security mechanism for sessions, OTPs, or authorization. Validate the actual state and permissions as required.

## **9. Redis Eviction Policies**

TTL and eviction are different concepts.

- **Expiration:** A key is removed when its configured lifetime ends.
- **Eviction:** Redis removes keys to free memory when the configured memory limit is reached.

Suppose:

```
Redis memory limit = 1 GB
Current memory usage = 1 GB
New data arrives
```

Redis needs a policy to decide what to remove or whether to reject writes.

### **Common eviction policies**

| Policy           | Meaning                                         |
| ---------------- | ----------------------------------------------- |
| `noeviction`     | Reject writes that need additional memory       |
| `allkeys-lru`    | Remove approximately least-recently-used keys   |
| `allkeys-lfu`    | Remove approximately least-frequently-used keys |
| `allkeys-random` | Remove random keys                              |
| `volatile-lru`   | LRU among keys with TTL                         |
| `volatile-lfu`   | LFU among keys with TTL                         |
| `volatile-ttl`   | Prefer keys with the shortest remaining TTL     |

Redis uses configurable eviction policies through `maxmemory-policy`.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs



### **LRU vs LFU**

**LRU – Least Recently Used**

Evicts keys that have not been accessed recently.

Example:

```
A accessed 2 seconds ago
B accessed 5 minutes ago
C accessed 2 hours ago
```

C is the likely eviction candidate.

**LFU – Least Frequently Used**

Evicts keys with relatively low access frequency.

Example:

```
A accessed 1,000 times
B accessed 500 times
C accessed 5 times
```

C is the likely eviction candidate.

### **Practical configuration example**

```
maxmemory 1gb
maxmemory-policy allkeys-lru
```

This is an example configuration, not a universal recommendation. Choose the policy based on the workload and the consequences of losing particular keys.

## **10. Integrating Redis with Node.js and TypeScript**

Now let's connect Redis to a real Node.js backend.

### **10.1 Install the Redis client**

```
npm install redis
```

The official Node.js client is called `node-redis`.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs+1



### **10.2 Project structure**

```
src/
├── config/
│   └── redis.ts
│
├── services/
│   └── product.service.ts
│
├── controllers/
│   └── product.controller.ts
│
├── routes/
│   └── product.routes.ts
│
└── server.ts
```

### **10.3 Configure Redis connection**

`src/config/redis.ts`

```
import { createClient } from "redis";

const redis = createClient({
  url: process.env.REDIS_URL,
});

redis.on("error", (error) => {
  console.error("Redis error:", error);
});

export async function connectRedis() {
  if (!redis.isOpen) {
    await redis.connect();
  }
}

export default redis;
```

Environment variable:

```
REDIS_URL=redis://localhost:6379
```

Connect once during application startup:

```
import { connectRedis } from "./config/redis";

async function startServer() {
  await connectRedis();

  // Start your Express application after Redis is ready.
}

startServer().catch((error) => {
  console.error("Application startup failed:", error);
  process.exit(1);
});
```

**Important:** Do not create a new Redis connection for every HTTP request. Reuse a managed client connection.

### **10.4 Store and retrieve JSON data**

Redis strings can store serialized JSON.

```
import redis from "../config/redis";

interface Product {
  id: number;
  name: string;
  price: number;
}

async function cacheProduct(product: Product) {
  const key = `product:${product.id}`;

  await redis.set(
    key,
    JSON.stringify(product),
    {
      EX: 600,
    }
  );
}

async function getCachedProduct(id: number) {
  const key = `product:${id}`;

  const result = await redis.get(key);

  if (!result) {
    return null;
  }

  return JSON.parse(result) as Product;
}
```

Here:

- `JSON.stringify()` converts an object to a JSON string.
- `JSON.parse()` converts the JSON string back to an object.
- `EX: 600` gives the cached value a 10-minute TTL.

For production systems, also handle malformed cached data, serialization compatibility, and cache failures.

## **11. Cache-Aside Pattern (Lazy Loading)**

Important pattern

Cache-aside is one of the most common caching patterns.

The application checks the cache first. If the data is missing, it queries the primary database and then stores the result in the cache.

### **11.1 Architecture**

[Redis Caching Strategies for High-Performance Applications | by Bakti Pratama | Medium](https://images.openai.com/static-rsc-4/7wK6iU1ZFOu-k6J5AdDskr7LyaD7uF8UtGxgwLPK05DDhfmOzseM55bWc2bsAJ9em05SwpEX2eKSDnb_x00ci8INM8_dThK8wrS5bk31LcaOmoPfSQ47vOLnPGVARoaAki4J7hhI67M9pYnuqRJuVjx2sYpMrFp7n3_EeCK2iSc?purpose=inline)

[baktiayp.medium.com](https://baktiayp.medium.com/redis-caching-strategies-for-high-performance-applications-01de386e6e43?utm_source=chatgpt.com)

```
Client Request
      |
      v
Application
      |
      v
Check Redis
      |
  +---+---+
  |       |
 Hit     Miss
  |       |
  v       v
Return  Query DB
Data      |
          v
       Store in Redis
          |
          v
       Return Data
```

### **11.2 Real-world implementation**

Suppose you have a product service using MongoDB.

`product.service.ts`

```
import redis from "../config/redis";
import ProductModel from "../models/product.model";

export async function getProductById(id: string) {
  const cacheKey = `product:${id}`;

  // Step 1: Check cache
  try {
    const cachedProduct = await redis.get(cacheKey);

    if (cachedProduct !== null) {
      return JSON.parse(cachedProduct);
    }
  } catch (error) {
    console.error("Redis read failed:", error);
    // Continue to the database.
  }

  // Step 2: Query database
  const product = await ProductModel.findById(id).lean();

  if (!product) {
    return null;
  }

  // Step 3: Store result in cache
  try {
    await redis.set(
      cacheKey,
      JSON.stringify(product),
      { EX: 600 }
    );
  } catch (error) {
    console.error("Redis write failed:", error);
  }

  // Step 4: Return result
  return product;
}
```

This example treats Redis as an optimization: if Redis is unavailable, the application can still read from MongoDB.

In a production implementation, validate the cached payload and account for database errors, serialization, and concurrent requests.

### **11.3 Cache invalidation after an update**

Suppose the product price changes.

A dangerous implementation is:

```
await ProductModel.findByIdAndUpdate(id, {
  price: 70000,
});
```

This updates the database but leaves the old cached value available.

A better basic strategy is:

```
export async function updateProduct(
  id: string,
  updates: Record<string, unknown>
) {
  const product = await ProductModel.findByIdAndUpdate(
    id,
    updates,
    {
      new: true,
      runValidators: true,
    }
  );

  if (!product) {
    return null;
  }

  try {
    await redis.del(`product:${id}`);
  } catch (error) {
    console.error("Cache invalidation failed:", error);
  }

  return product;
}
```

This is **write to database, then invalidate cache**.

The next read will retrieve the updated record from the database and populate the cache again.

### **Important consistency warning**

There is a failure window:

1. Database update succeeds.
2. Redis invalidation fails.
3. Old cached data remains available until it expires or is invalidated.

For high-impact data, you need a stronger invalidation design, retries, versioning, or an event-driven invalidation mechanism. The cache-aside approach and its trade-offs are documented by Redis.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs



## **12. Read-Through Caching**

In read-through caching, the application asks the caching layer for data, and the caching layer is responsible for loading missing data from the underlying data source.

Conceptually:

```
Application
     |
     v
Caching Layer
     |
  +--+--+
  |     |
 Hit   Miss
  |     |
  v     v
Return  Load from DB
        |
        v
     Cache result
        |
        v
     Return result
```

The difference is primarily responsibility:

- Cache-aside: Application explicitly implements the cache lookup and database fallback.
- Read-through: The caching layer or abstraction manages the fallback.

You may build a read-through abstraction yourself, but a normal Redis `GET` does not automatically query MongoDB when a key is missing.

## **13. Write-Through Caching**

In write-through caching, a write operation updates the cache and the underlying data store as part of the same logical workflow.

Conceptually:

```
Application
     |
     v
Write-through layer
     |
  +--+--+
  |     |
  v     v
Cache  Database
```

Example:

A product's price is updated. The write-through layer coordinates updating the database and the cached representation.

Advantages:

- Cache can remain warm after updates.
- Subsequent reads are less likely to encounter misses.

Challenges:

- More work on every write.
- Failure handling and ordering become important.
- Updating two systems does not automatically provide a distributed atomic transaction.

Do not assume a simple `Promise.all()` guarantees an atomic write across Redis and MongoDB.

## **14. Write-Behind (Write-Back) Caching**

In write-behind caching, the application writes to the cache first, and the cache layer asynchronously persists changes to the primary database later.

```
Application
     |
     v
Cache
     |
     v
Acknowledge write
     |
     v
Background worker
     |
     v
Database
```

Example:

A gaming application records frequently changing scores in a fast data store and periodically persists or processes them in the database.

Advantages:

- Very fast write acknowledgement.
- Can reduce database write pressure.
- Useful for high-volume aggregation workloads.

Risks:

- Data can be lost before persistence.
- Background processing must be reliable.
- Retries and duplicate processing must be handled.
- Database and cache can temporarily disagree.

**Critical rule:** Do not use a casual write-behind implementation for payments, financial transactions, or other business-critical records without durable event storage and an explicit recovery design.

### **Caching patterns comparison**

| Pattern       | Read behavior                              | Write behavior                                 |
| ------------- | ------------------------------------------ | ---------------------------------------------- |
| Cache-aside   | Application checks cache, falls back to DB | Usually DB update then cache invalidation      |
| Read-through  | Cache layer loads missing data             | Separate write strategy                        |
| Write-through | Reads use cache                            | Cache and DB updated in write workflow         |
| Write-behind  | Reads use cache                            | Cache updated first; DB updated asynchronously |

## **15. Cache Invalidation Strategies**

Cache invalidation means making cached data unavailable or refreshing it when the underlying data changes.

It is one of the hardest parts of caching because cached data can become stale.

### **Strategy 1: TTL-based invalidation**

Set an expiration time.

```
SET product:101 "Laptop" EX 600
```

After the expiration period, the key is no longer available.

Useful when a small amount of staleness is acceptable.

### **Strategy 2: Explicit invalidation**

Delete a key after a successful database update.

```
DEL product:101
```

Useful when an application knows exactly which cached entity changed.

### **Strategy 3: Update the cache**

Instead of deleting the cache key, replace its value after the database update.

```
await redis.set(
  `product:${product.id}`,
  JSON.stringify(product),
  { EX: 600 }
);
```

This can keep frequently accessed entries warm, but you must ensure the updated value is correct and no older concurrent operation overwrites it.

### **Strategy 4: Tag-based invalidation**

Associate related cache entries with a common tag or dependency.

For example:

```
product:101  -> products
product:102  -> products
product:103  -> products
```

Invalidating the `products` group can affect multiple entries.

Next.js provides `cacheTag()` and on-demand revalidation APIs for its own caching system. This is different from Redis key invalidation.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **Strategy 5: Versioned cache keys**

Include a version in the key.

```
product:101:v1
product:101:v2
```

After a change, the application can use a newer version.

This helps avoid some stale-key problems, although version coordination and old-key cleanup still need to be designed.

## **16. Cache Stampede, Cache Penetration and Cache Avalanche**

These three problems commonly appear in high-traffic applications.

### **16.1 Cache Stampede**

A cache stampede occurs when a popular key expires and many concurrent requests try to reload the same data from the database.

Example:

```
1,000 requests arrive simultaneously
             |
             v
     Redis key has expired
             |
       +-----+-----+
       |     |     |
       v     v     v
      DB    DB    DB ...
       |     |     |
       +-----+-----+
             |
       Heavy DB load
```

Instead of one database query, the application may generate hundreds of duplicate queries.

**Solution: Locking / single-flight loading**

Only one request should perform the database fetch for a particular key. Other requests can wait briefly, retry the cache, or use a stale value if the design allows it.

A simplified Redis lock:

```
const lockKey = `lock:product:${id}`;

const lock = await redis.set(
  lockKey,
  "locked",
  {
    NX: true,
    PX: 5000,
  }
);

if (lock === "OK") {
  try {
    // Load product from database.
    // Populate cache.
  } finally {
    await redis.del(lockKey);
  }
}
```

This is only a conceptual example. Production locks must use a unique ownership token and release the lock only if the stored token still belongs to the current owner. Otherwise, one request can accidentally delete another request's lock after the original lock expires.

Other approaches include:

- Request coalescing (single-flight).
- Stale-while-revalidate.
- Randomized TTL jitter.
- Probabilistic early refresh.

### **16.2 Cache Penetration**

Cache penetration occurs when requests repeatedly ask for data that does not exist.

Example:

```
GET /products/invalid-id-1
GET /products/invalid-id-2
GET /products/invalid-id-3
```

Every request misses Redis and queries the database.

Possible solutions:

**Negative caching**

Cache the fact that the record does not exist for a short time.

```
const NOT_FOUND = "__NOT_FOUND__";

const cached = await redis.get(cacheKey);

if (cached === NOT_FOUND) {
  return null;
}

if (cached !== null) {
  return JSON.parse(cached);
}

const product = await ProductModel.findById(id).lean();

if (!product) {
  await redis.set(cacheKey, NOT_FOUND, { EX: 30 });
  return null;
}
```

Be careful to validate IDs and authorization before treating a missing result as a cacheable negative result.

Other solutions:

- Input validation.
- Rate limiting.
- Bloom filters for large key spaces.
- Short-lived negative caching.

### **16.3 Cache Avalanche**

A cache avalanche occurs when many cached entries expire around the same time, causing a sudden increase in database traffic.

Example:

```
10:00 AM
    |
    v
Thousands of cached products expire
    |
    v
Large number of database requests
    |
    v
Database overload
```

Solutions:

- Add random TTL jitter.
- Warm critical cache entries before peak traffic.
- Use background refresh.
- Apply rate limits and load shedding.
- Scale database capacity appropriately.

Example TTL jitter:

```
const baseTTL = 600;
const jitter = Math.floor(Math.random() * 120);

await redis.set(
  "product:101",
  JSON.stringify(product),
  {
    EX: baseTTL + jitter,
  }
);
```

Now the TTL is between 600 and 719 seconds.

## **17. Redis with Next.js 16 App Router**

You can use Redis in a Next.js application to cache database results, store temporary state, manage rate limits, and maintain sessions.

However, an important distinction must be understood:

**Redis caching and Next.js caching are two different systems.**

### **17.1 Redis vs Next.js Cache Components**

| Feature                             | Redis                                          | Next.js Cache Components                                  |
| ----------------------------------- | ---------------------------------------------- | --------------------------------------------------------- |
| Main purpose                        | Shared data storage and caching                | Cache functions and components                            |
| Storage                             | Redis server memory, with optional persistence | Next.js-managed caching infrastructure                    |
| Access                              | Redis client commands                          | Next.js directives and APIs                               |
| Shared across application instances | Yes, when using the same Redis deployment      | Depends on the deployment cache handler and configuration |
| TTL                                 | Redis expiration                               | `cacheLife()`                                             |
| Invalidation                        | `DEL`, versioning, application logic           | `revalidateTag()`, `updateTag()`, `revalidatePath()`      |

Do not assume that calling `revalidateTag()` automatically deletes a Redis key. They are separate cache systems.

### **17.2 Enable Cache Components in Next.js 16**

In `next.config.ts`:

```
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

Next.js 16 introduced `cacheComponents` as the unified configuration for Cache Components.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **17.3 Redis connection in Next.js**

Install:

```
npm install redis
```

Create:

`src/lib/redis.ts`

```
import "server-only";

import { createClient } from "redis";

const redisUrl = process.env.REDIS_URL;

if (!redisUrl) {
  throw new Error("REDIS_URL is not configured");
}

const globalForRedis = globalThis as typeof globalThis & {
  redisClient?: ReturnType<typeof createClient>;
  redisConnection?: Promise<void>;
};

const redis =
  globalForRedis.redisClient ??
  createClient({ url: redisUrl });

if (process.env.NODE_ENV !== "production") {
  globalForRedis.redisClient = redis;
}

redis.on("error", (error) => {
  console.error("Redis error:", error);
});

export async function getRedis() {
  if (!redis.isOpen) {
    globalForRedis.redisConnection ??= redis.connect();
  }

  await globalForRedis.redisConnection;

  return redis;
}
```

This example reuses a Redis client and shares an in-progress connection attempt. In serverless deployments, connection reuse and connection limits require special attention; a managed Redis service or compatible connection pooling approach may be necessary.

### **17.4 Cache product data in Redis**

`src/lib/products.ts`

```
import "server-only";

import { getRedis } from "./redis";
import { db } from "./db";

export async function getProduct(id: string) {
  const redis = await getRedis();

  const cacheKey = `product:${id}`;

  const cached = await redis.get(cacheKey);

  if (cached !== null) {
    return JSON.parse(cached);
  }

  const product = await db.product.findUnique({
    where: { id },
  });

  if (!product) {
    return null;
  }

  await redis.set(
    cacheKey,
    JSON.stringify(product),
    { EX: 600 }
  );

  return product;
}
```

Here, `db` represents your server-side database client. The exact query depends on whether you use Prisma, MongoDB, Sequelize, or another database library.

### **17.5 Use Redis-backed data in a Server Component**

`app/products/[id]/page.tsx`

```
import { getProduct } from "@/lib/products";
import { notFound } from "next/navigation";

export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;

  const product = await getProduct(id);

  if (!product) {
    notFound();
  }

  return (
    <main>
      <h1>{product.name}</h1>
      <p>Price: ₹{product.price}</p>
    </main>
  );
}
```

Notice that `params` is awaited, following the Next.js 16 App Router convention.

### **17.6 Cache Components with&#x20;**`use cache`

Next.js can also cache a function's output using its own caching system.

```
import { cacheLife, cacheTag } from "next/cache";
import { db } from "./db";

export async function getCachedProducts() {
  "use cache";

  cacheLife("hours");
  cacheTag("products");

  return db.product.findMany();
}
```

This example uses Next.js Cache Components, not Redis.

The `cacheLife()` function controls the lifetime of the cached result, while `cacheTag()` associates a tag for later invalidation.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://nextjs.org\&sz=32)

Next.js+1



### **17.7 Invalidate Next.js cached data after mutation**

For example, after an administrator updates a product:

```
"use server";

import { updateTag } from "next/cache";
import { db } from "@/lib/db";

export async function updateProduct(
  id: string,
  name: string
) {
  await db.product.update({
    where: { id },
    data: { name },
  });

  updateTag("products");
}
```

`updateTag()` is useful when a Server Action needs immediate expiration of tagged data so that subsequent reads see fresh data.

For stale-while-revalidate behavior, use `revalidateTag(tag, "max")` where appropriate.

**Remember:** If your application also caches products in Redis, you must invalidate or update those Redis entries separately. The two cache layers need a coordinated consistency strategy.

## **18. Distributed Caching**

A distributed cache is a cache accessible by multiple application instances through a shared network service.

Imagine your application has three Node.js instances:

[Redis Caching: Unlocking Hyper-Performance and Scalability in Modern Architectures](https://images.openai.com/static-rsc-4/itknp2-0BhZmpfUWp3L61UTsH-9fTZBmFVPwR45ujDzHkCFJs9bwh9W6OsIIRPrPF6syV75Py80JIKu8sIyvUwLpadCjXRSO6oO1c_zKLNm7L-s_60Hyhh6TD7SzDseZYFgIhBszgnvPKAQiNiuRSvdlB31sWOJ_wCQYjH-GW4s?purpose=inline)

[linkedin.com](https://www.linkedin.com/pulse/redis-caching-unlocking-hyper-performance-scalability-shankar-ghate-0ayyf?utm_source=chatgpt.com)

```
            Load Balancer
                 |
       +---------+---------+
       |         |         |
       v         v         v
    Node 1    Node 2    Node 3
       |         |         |
       +---------+---------+
                 |
                 v
            Redis Server
                 |
                 v
             Database
```

All application instances access the same Redis deployment.

### **18.1 Local cache vs distributed cache**

| Feature                  | Local in-memory cache       | Distributed Redis cache                  |
| ------------------------ | --------------------------- | ---------------------------------------- |
| Storage                  | Application process RAM     | Shared Redis infrastructure              |
| Shared between instances | No                          | Yes                                      |
| Network communication    | Not required                | Required                                 |
| Restart behavior         | Lost when process restarts  | Depends on Redis persistence             |
| Scaling                  | Duplicated across instances | Shared across instances                  |
| Management               | Simple                      | Requires Redis operations and monitoring |

### **18.2 Why distributed caching matters**

Suppose you deploy your Node.js application on three servers.

Without distributed caching:

```
Server 1 -> Cache A
Server 2 -> Cache B
Server 3 -> Cache C
```

Each server may hold a different version of the same product.

With Redis:

```
Server 1 ----+
Server 2 ----+---- Redis
Server 3 ----+
```

All instances use a common cache.

This makes cache sharing and invalidation easier, although it does not automatically guarantee consistency with the database.

### **18.3 Distributed cache use cases**

- E-commerce product catalogs.
- Shared user sessions.
- API response caching.
- Rate limiting across servers.
- Shared feature flags.
- Real-time leaderboards.
- Distributed locks and coordination.

## **19. Redis Pub/Sub**

Redis Pub/Sub implements the Publish/Subscribe messaging pattern.

There are two main roles:

- **Publisher:** Sends a message to a channel.
- **Subscriber:** Listens to a channel and receives messages.

Example:

```
Publisher
    |
    v
Redis Channel: notifications
    |
    +----------+
    |          |
    v          v
Subscriber A  Subscriber B
```

### **19.1 Redis CLI example**

Subscriber:

```
SUBSCRIBE notifications
```

Publisher:

```
PUBLISH notifications "New product added"
```

Every currently subscribed client receives the published message.

### **19.2 Node.js Pub/Sub example**

Create a subscriber using a duplicated client connection:

```
import redis from "./redis";

const subscriber = redis.duplicate();

subscriber.on("error", (error) => {
  console.error("Subscriber error:", error);
});

await subscriber.connect();

await subscriber.subscribe(
  "notifications",
  (message) => {
    console.log("Received:", message);
  }
);
```

Publisher:

```
await redis.publish(
  "notifications",
  JSON.stringify({
    type: "PRODUCT_CREATED",
    productId: "101",
  })
);
```

### **19.3 Important limitation: Pub/Sub is not durable**

Redis Pub/Sub provides **at-most-once delivery**.

If a subscriber is disconnected when a message is published, it misses that message. Redis does not retain it for the subscriber to retrieve later.

For durable event processing and replay, consider Redis Streams and consumer groups instead.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs+2



| Redis Pub/Sub                                   | Redis Streams                                               |
| ----------------------------------------------- | ----------------------------------------------------------- |
| Live message delivery                           | Persistent stream entries                                   |
| No message history for disconnected subscribers | Entries can be read later                                   |
| At-most-once delivery                           | Supports at-most-once and at-least-once processing patterns |
| Useful for notifications                        | Useful for durable event processing                         |

## **20. Redis Transactions and Lua Scripts**

### **20.1 Redis Transactions**

Redis transactions group multiple commands into a single isolated execution sequence.

Important commands:

- `MULTI`
- `EXEC`
- `DISCARD`
- `WATCH`

Example:

```
MULTI

INCR account:101:balance
INCR account:102:balance

EXEC
```

Commands are queued after `MULTI` and executed when `EXEC` is called.

`DISCARD` cancels the queued transaction.

`WATCH` provides optimistic concurrency control: if a watched key changes before `EXEC`, the transaction is aborted.

Redis transactions provide serialized execution, but they are not equivalent to a relational database transaction with automatic rollback of every command when a runtime command error occurs. Validate commands and understand the error behavior.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs



### **20.2 Lua Scripts**

Redis supports Lua scripting to execute logic on the Redis server.

Example:

```
EVAL "return redis.call('INCR', KEYS[1])" 1 page_views
```

This increments `page_views` within a server-side script.

Lua scripts are useful when multiple Redis operations must execute atomically as one unit.

For example:

- Checking a lock and acquiring it.
- Validating a rate limit and incrementing a counter.
- Updating multiple related Redis keys.
- Performing conditional updates.

Redis guarantees atomic execution of a script. However, a long-running script blocks other Redis activity while it executes, so scripts should be short and efficient.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs+1



## **21. Redis Persistence**

Although Redis is primarily an in-memory data store, it supports persistence to disk.

Two important persistence mechanisms are RDB and AOF.

### **21.1 RDB – Redis Database Backup**

RDB persistence creates point-in-time snapshots of the Redis dataset.

Example:

```
Redis memory
     |
     v
Snapshot process
     |
     v
dump.rdb
```

Advantages:

- Compact backup files.
- Good for backups and recovery.
- Can provide faster restarts for large datasets.

Disadvantages:

- Changes since the last snapshot can be lost after a failure.
- Snapshotting has resource costs.

### **21.2 AOF – Append Only File**

AOF records write operations so Redis can reconstruct its data by replaying those operations during startup.

Example:

```
SET user:1 Ahmed
SET product:101 Laptop
INCR page_views
```

These write operations are recorded in the AOF.

Advantages:

- Can provide stronger durability than periodic snapshots.
- Configurable fsync behavior.
- Can reconstruct the dataset from logged writes.

Disadvantages:

- Can use more disk space.
- May involve additional disk I/O.
- Requires management and rewrite operations.

### **21.3 RDB vs AOF**

| Feature             | RDB                  | AOF                        |
| ------------------- | -------------------- | -------------------------- |
| Storage method      | Snapshots            | Write-operation log        |
| Main purpose        | Point-in-time backup | Reconstruct recent changes |
| File size           | Usually compact      | Usually larger             |
| Potential data loss | Since last snapshot  | Depends on fsync policy    |
| Recovery            | Load snapshot        | Replay operations          |

Redis also allows RDB and AOF together, or persistence to be disabled when Redis is being used strictly as a disposable cache.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs+1



**Important:** If Redis is only a cache, losing its contents should normally affect performance rather than destroy authoritative business data. If Redis stores important business state, design persistence, replication, backup, and recovery accordingly.

## **22. Redis High Availability and Scaling**

A single Redis server can become a bottleneck or a single point of failure.

Redis provides mechanisms to improve availability and distribute data.

### **22.1 Replication**

Redis replication allows a primary instance to replicate data to one or more replicas.

```
        Primary Redis
              |
        +-----+-----+
        |           |
        v           v
     Replica 1   Replica 2
```

A replica can be used for read scaling or as a failover candidate, depending on the architecture.

Redis replication is asynchronous, so a replica may temporarily lag behind the primary.

### **22.2 Redis Sentinel**

Redis Sentinel provides monitoring, notifications, and automatic failover for Redis deployments using replication.

If the primary fails, Sentinel can coordinate promotion of a replica.

However, asynchronous replication means failover can result in the loss of some acknowledged writes. Sentinel does not provide a guarantee that every acknowledged write survives failover.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://redis.io\&sz=32)

Docs



### **22.3 Redis Cluster**

Redis Cluster distributes data across multiple Redis nodes using hash slots.

```
Redis Cluster
    |
    +---- Node A
    |
    +---- Node B
    |
    +---- Node C
```

This is useful when a single Redis node cannot provide enough capacity.

### **Comparison**

| Feature               | Replication   | Sentinel          | Cluster                       |
| --------------------- | ------------- | ----------------- | ----------------------------- |
| Copies data           | Yes           | Uses replication  | Uses sharding and replication |
| Automatic failover    | Not by itself | Yes               | Yes, with cluster failover    |
| Distributes key space | No            | No                | Yes                           |
| Main purpose          | Replication   | High availability | Sharding and availability     |

## **23. Caching Strategies in Microservices**

In a microservices architecture, different services may have different caching requirements.

Consider an e-commerce system:

```
                 API Gateway
                      |
       +--------------+--------------+
       |              |              |
       v              v              v
 Product Service  User Service  Order Service
       |              |              |
       +--------------+--------------+
                      |
                   Redis
                      |
              Database Services
```

### **23.1 Shared Redis vs Separate Redis**

**Shared Redis deployment**

Multiple services access the same Redis infrastructure.

Advantages:

- Shared infrastructure.
- Easier infrastructure management.
- Lower operational overhead for smaller systems.

Disadvantages:

- Resource contention.
- Services can accidentally interfere with each other's keys.
- Different workloads may require different eviction policies.

**Separate Redis deployments**

Each service has its own Redis infrastructure.

Advantages:

- Better isolation.
- Independent scaling.
- Independent configuration.
- Reduced cross-service interference.

Disadvantages:

- More infrastructure cost.
- More monitoring and maintenance.

### **23.2 Use namespaced keys**

When multiple services share Redis, organize keys carefully.

```
product:101
product:102

user:101
user:102

order:501
order:502
```

You can also include an environment and service name:

```
production:product-service:product:101
production:user-service:user:101
```

This helps avoid key collisions.

### **23.3 Cache invalidation using events**

Suppose the Product Service updates a product.

It can publish an event:

```
{
  "event": "PRODUCT_UPDATED",
  "productId": "101"
}
```

Other services that maintain related cached data can react to that event.

Example:

```
Product Service
      |
      v
Update Database
      |
      v
Publish PRODUCT_UPDATED
      |
      v
Event Broker
      |
      +------------------+
      |                  |
      v                  v
Search Service      Recommendation Service
      |                  |
      v                  v
Invalidate Cache    Refresh Cache
```

For important invalidation events, do not depend solely on ephemeral Pub/Sub delivery. Use a durable event mechanism, such as a transactional outbox with a durable broker or Redis Streams, and make consumers idempotent.

## **24. Redis Monitoring and Performance Optimization**

Installing Redis is not enough. You must monitor its behavior in production.

### **24.1 Important metrics**

| Metric            | What it tells you                                |
| ----------------- | ------------------------------------------------ |
| Cache hit ratio   | How frequently requests find cached values       |
| Cache miss ratio  | How frequently requests need fallback loading    |
| Memory usage      | How much RAM Redis consumes                      |
| Evicted keys      | How many keys are removed due to memory pressure |
| Expired keys      | How many keys have expired                       |
| Command latency   | How long Redis commands take                     |
| Connected clients | Number of client connections                     |
| Network traffic   | Data transferred to and from Redis               |
| Replication lag   | How far replicas are behind the primary          |

### **24.2 Redis monitoring commands**

Check Redis server information:

```
INFO
```

Check memory:

```
INFO memory
```

Check statistics:

```
INFO stats
```

Check connected clients:

```
INFO clients
```

Check remaining TTL:

```
TTL product:101
```

Measure a command's execution time:

```
SLOWLOG GET 10
```

### **24.3 Monitor latency from Node.js**

```
const start = performance.now();

const value = await redis.get("product:101");

const duration = performance.now() - start;

console.log(`Redis GET took ${duration.toFixed(2)} ms`);
```

In a production application, collect latency histograms and percentiles such as P50, P95, and P99 rather than logging every request.

### **24.4 Redis performance best practices**

1. Reuse Redis connections.
2. Use sensible TTL values.
3. Avoid unnecessarily large cached objects.
4. Use appropriate Redis data structures.
5. Avoid expensive commands on large datasets.
6. Avoid `KEYS *` in production; use incremental `SCAN` where appropriate.
7. Use pipelining or batching when it reduces network round trips.
8. Monitor memory and eviction behavior.
9. Set connection and command timeouts.
10. Handle Redis failures gracefully.
11. Protect Redis with private networking and authentication.
12. Never expose Redis directly to the public internet.
13. Use TLS when required by your deployment architecture.
14. Use separate Redis credentials and permissions for different services.
15. Monitor cache hit ratio alongside database query volume.

## **25. Real-World Caching Architecture**

Let's combine the concepts into a practical e-commerce architecture.

[Caching Strategies: How We Broke Checkout By Caching Everything | Medium](https://images.openai.com/static-rsc-4/1dhfhV9SpkrJc02FpUqzhqK2xNM4pabXrYOHU9d2IX8xfUGBzaaHeI5msySr61D_bJlBAbVREgt161uSphUZjf8Pyco24fCXbG1W6lLzVdvb8OtGCJeKkE_R7zX5QGpxhLhOlReOBORZs9_2ZvE5BMlpwBOnxz_XZ6AeuUfsmZo?purpose=inline)

[medium.com](https://medium.com/%40anas-issath/we-cached-everything-and-made-it-worse-87c7522abcfc?utm_source=chatgpt.com)

```
                  User
                   |
                   v
              CDN / Browser
                   |
                   v
              Next.js App
                   |
                   v
             Node.js Backend
                   |
             +-----+-----+
             |           |
             v           v
           Redis      Database
             |           |
             |           |
             +-----------+
```

Example responsibilities:

| Layer         | Responsibility                      |
| ------------- | ----------------------------------- |
| Browser       | Cache static assets                 |
| CDN           | Cache public content near users     |
| Next.js       | Cache suitable server-rendered data |
| Redis         | Shared application-level cache      |
| MongoDB/MySQL | Store authoritative business data   |

### **Example: Product request**

A user requests:

```
GET /api/products/101
```

The backend:

1. Checks Redis.
2. If there is a cache hit, returns the cached product.
3. If there is a miss, queries MongoDB.
4. Stores the result in Redis with a TTL.
5. Returns the product.

### **Example: Product update**

An administrator updates the product price.

The backend:

1. Validates the update.
2. Updates MongoDB.
3. Invalidates the relevant Redis cache key.
4. Revalidates any relevant Next.js cache tags or paths.
5. Returns the result.

For a simple application, this may be sufficient. For a high-traffic system, use reliable invalidation, retries, versioning, and event-driven coordination to handle partial failures and concurrent updates.

## **26. Common Caching Mistakes**

| Mistake                                 | Why it is problematic                        | Better approach                                      |
| --------------------------------------- | -------------------------------------------- | ---------------------------------------------------- |
| No TTL                                  | Cache may remain stale indefinitely          | Define a freshness policy                            |
| Cache every database record             | Wastes memory                                | Cache frequently accessed data                       |
| No invalidation                         | Old data remains available                   | Invalidate or update after writes                    |
| Treat Redis as the database of record   | Cache loss can destroy business state        | Keep authoritative data in durable storage           |
| Create Redis connections per request    | Excessive connection overhead                | Reuse managed clients                                |
| Ignore Redis failures                   | Cache outage can break requests              | Design fallback behavior                             |
| Cache private data under shared keys    | May expose another user's data               | Include proper identity and authorization boundaries |
| Use `KEYS *` in production              | Can block Redis on large keyspaces           | Use `SCAN`                                           |
| Assume Pub/Sub is durable               | Disconnected consumers lose messages         | Use Streams or a durable broker                      |
| Assume `Promise.all()` is transactional | Partial writes are possible                  | Design explicit consistency and recovery             |
| Set the same TTL for everything         | Causes synchronized expiration               | Use appropriate TTL and jitter                       |
| Ignore memory limits                    | Can cause memory pressure or rejected writes | Configure and monitor eviction                       |

### **Particularly important: Never leak user-specific cached data**

Suppose two users access:

```
GET /api/profile
```

A dangerous cache key would be:

```
profile
```

Both users might receive the same cached value.

Instead:

```
profile:user:101
profile:user:102
```

Also perform authorization independently. A cache hit must never bypass access-control checks.

## **27. Interview Questions and Answers**

### **Basic Level**

**Q1. What is caching?**

Caching is the process of storing frequently accessed data in a faster storage layer to reduce response time and repeated database operations.

**Q2. What is Redis?**

Redis is an in-memory data structure server used for caching, session storage, counters, messaging, and other data-intensive operations.

**Q3. What is a cache hit?**

A cache hit occurs when requested data is available and usable in the cache.

**Q4. What is a cache miss?**

A cache miss occurs when the requested data is not available in the cache.

**Q5. What is TTL?**

TTL stands for Time to Live. It defines how long a cache key remains available before expiration.

**Q6. What is cache invalidation?**

Cache invalidation is the process of deleting or refreshing cached data when it is no longer valid.

**Q7. What is the difference between Redis and MongoDB?**

Redis is primarily an in-memory data structure server, whereas MongoDB is a document-oriented database commonly used as durable application storage.

### **Intermediate Level**

**Q8. What is cache-aside?**

The application checks the cache first. On a miss, it retrieves data from the database and stores the result in the cache.

**Q9. What is the difference between cache-aside and write-through?**

Cache-aside loads data on demand. Write-through updates the cache and primary data store as part of the write workflow.

**Q10. What is a distributed cache?**

A distributed cache is shared by multiple application instances over a network, allowing them to access common cached data.

**Q11. What is the difference between LRU and LFU?**

LRU evicts data based on recency of access. LFU evicts data based on access frequency.

**Q12. What is cache stampede?**

It occurs when many concurrent requests try to reload the same expired cache entry, potentially overwhelming the database.

**Q13. How can you prevent cache penetration?**

Use input validation, short-lived negative caching, rate limiting, and Bloom filters where appropriate.

**Q14. What is Redis Pub/Sub?**

It is a publish/subscribe messaging mechanism in which publishers send messages to channels and active subscribers receive them.

### **Advanced Level**

**Q15. What is the difference between Redis Pub/Sub and Redis Streams?**

Pub/Sub provides ephemeral at-most-once delivery. Streams store entries and support consumer groups and replay within the retained stream.

**Q16. How do you handle Redis failure?**

For an optional cache, fall back to the primary database when safe, while protecting the database through timeouts, concurrency limits, and load shedding. For Redis-backed critical state, use a defined recovery and availability strategy rather than silently ignoring the failure.

**Q17. How do you maintain cache consistency across multiple Node.js instances?**

Use a shared cache, explicit invalidation, versioning or event-driven invalidation, and reliable handling of update failures.

**Q18. Why is Redis not always a replacement for a database?**

Redis can persist data, but durability, query capabilities, transactional requirements, recovery, and operational characteristics differ from those of a purpose-built primary database.

**Q19. What is cache avalanche?**

It occurs when many cached entries expire around the same time and cause a sudden increase in requests to the primary data source.

**Q20. How would you design caching for a high-traffic e-commerce application?**

Use browser/CDN caching for suitable public content, Next.js caching for appropriate rendered data, Redis cache-aside for shared backend data, TTL and invalidation policies, and database-backed authoritative storage. Add monitoring, protection against stampedes, and reliable cache invalidation.

## **28. Practical Assignment**

Hands-on project

Build a **Redis-powered Product Caching System** using Node.js, TypeScript, Express, MongoDB, and Redis.

### **Project structure**

```
redis-product-cache/
│
├── src/
│   ├── config/
│   │   ├── db.ts
│   │   └── redis.ts
│   │
│   ├── models/
│   │   └── product.model.ts
│   │
│   ├── services/
│   │   └── product.service.ts
│   │
│   ├── controllers/
│   │   └── product.controller.ts
│   │
│   ├── routes/
│   │   └── product.routes.ts
│   │
│   └── server.ts
│
├── .env
├── .env.example
└── package.json
```

### **Required features**

**Assignment progress**

# **0%**

0 of 12 tasks completed

Install and configure Redis locally or through Docker.

Connect Redis to Node.js using node-redis.

Connect MongoDB to the application.

Create Product CRUD APIs.

Implement cache-aside for GET /products/:id.

Set a 10-minute TTL for product cache entries.

Invalidate Redis keys after product updates and deletions.

Implement short-lived negative caching for missing products.

Add Redis connection error handling and database fallback.

Measure cache hits, misses, and response latency.

Test simultaneous requests for an expired popular key.

Document the caching architecture and its failure behavior.

Reset checklist

### **Testing scenarios**

Test the following:

1. First product request: cache miss.
2. Second product request: cache hit.
3. Update product: cache invalidated.
4. Request after update: updated data returned.
5. Request for nonexistent product: negative cache hit.
6. Wait for TTL expiration: database fallback occurs again.
7. Redis unavailable: verify that your fallback strategy works.
8. Multiple concurrent requests: observe whether a cache stampede occurs.

### **Expected result**

You should be able to explain exactly what happens to a request as it travels through Express, Redis, and MongoDB.

## **29. Quick Revision Notes**

| Concept           | Remember                                          |
| ----------------- | ------------------------------------------------- |
| Caching           | Store frequently accessed data in a faster layer  |
| Cache hit         | Data found in cache                               |
| Cache miss        | Data not found in cache                           |
| Hit ratio         | Hits divided by total cache requests              |
| Redis             | In-memory data structure server                   |
| String            | Simple values, JSON, counters                     |
| Hash              | Field-value records                               |
| List              | Ordered sequence                                  |
| Set               | Unique values                                     |
| Sorted Set        | Values ordered by score                           |
| Stream            | Append-oriented event log                         |
| TTL               | Key expiration time                               |
| Eviction          | Remove keys to free memory                        |
| Cache-aside       | Application loads missing data                    |
| Read-through      | Cache layer loads missing data                    |
| Write-through     | Cache and database updated in write workflow      |
| Write-behind      | Database updated asynchronously after cache write |
| Cache stampede    | Many requests reload the same expired key         |
| Cache penetration | Repeated misses for nonexistent data              |
| Cache avalanche   | Many keys expire around the same time             |
| Pub/Sub           | Ephemeral message broadcasting                    |
| Redis Streams     | Persistent stream entries and consumer groups     |
| RDB               | Point-in-time snapshots                           |
| AOF               | Write-operation log                               |
| Sentinel          | High availability and failover                    |
| Cluster           | Sharding and distributed Redis deployment         |
| `cacheLife()`     | Controls Next.js Cache Components lifetime        |
| `cacheTag()`      | Tags Next.js cached data                          |
| `revalidateTag()` | Revalidates tagged Next.js cached data            |

## **30. Official Documentation and Further Reading**

Use these official references when implementing the concepts:

1. [Redis Documentation](https://redis.io/docs/latest/?utm_source=chatgpt.com) – Redis fundamentals and commands.
2. [Redis Data Types](https://redis.io/docs/latest/develop/data-types/?utm_source=chatgpt.com) – Strings, hashes, lists, sets, sorted sets, and streams.
3. [Redis Cache-Aside](https://redis.io/docs/latest/develop/use-cases/cache-aside/?utm_source=chatgpt.com) – Cache-aside implementation and stampede considerations.
4. [Redis Node.js Client](https://redis.io/docs/latest/develop/clients/nodejs/?utm_source=chatgpt.com) – Node.js integration.
5. [Redis Pub/Sub](https://redis.io/docs/latest/develop/pubsub/?utm_source=chatgpt.com) – Pub/Sub messaging and delivery semantics.
6. [Redis Persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/?utm_source=chatgpt.com) – RDB and AOF.
7. [Redis Eviction](https://redis.io/docs/latest/reference/eviction/?utm_source=chatgpt.com) – Memory management and eviction policies.
8. [Next.js Caching](https://nextjs.org/docs/app/getting-started/caching?utm_source=chatgpt.com) – Next.js caching concepts.
9. [Next.js cacheLife](https://nextjs.org/docs/app/api-reference/functions/cacheLife?utm_source=chatgpt.com) – Cache lifetime.
10. [Next.js cacheTag](https://nextjs.org/docs/app/api-reference/functions/cacheTag?utm_source=chatgpt.com) – Cache tagging and invalidation.

## **Phase 32 Completion Checklist**

**Your learning progress**

# **0%**

Caching fundamentals and cache hit ratio

Browser, CDN, application, and database caching

Redis architecture and data types

Redis installation and Node.js integration

TTL and eviction policies

Cache-aside and other caching patterns

Cache invalidation strategies

Stampede, penetration, and avalanche

Redis with Next.js 16 Cache Components

Distributed caching and microservices

Pub/Sub, Streams, transactions, and Lua

Persistence, replication, Sentinel, and Cluster

Monitoring, performance, and production best practices

0 / 13 topics completed

## **Next Phase Preview**

Phase 33

# **Microservices Architecture with Node.js**

We will learn how to design, build, communicate between, secure, and deploy independent backend services.

**Topics will include:**

1. Monolithic vs Microservices Architecture
2. Microservices Principles and Characteristics
3. Service Decomposition
4. API Gateway Pattern
5. Inter-Service Communication
6. Synchronous vs Asynchronous Communication
7. Service Discovery
8. Centralized Configuration
9. Distributed Authentication and Authorization
10. Database per Service Pattern
11. API Composition and Aggregation
12. Circuit Breaker and Retry Patterns
13. Distributed Tracing and Logging
14. Microservices with Node.js and TypeScript
15. Docker and Containerization
16. Microservices Deployment and Scaling
17. Monitoring and Fault Tolerance
18. Real-World E-commerce Microservices Architecture

**When you're ready, say: OK, Phase 33.**