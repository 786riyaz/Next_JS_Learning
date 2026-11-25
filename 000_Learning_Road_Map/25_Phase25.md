# **Phase 25 – Distributed Systems and Event-Driven Architecture**



Advanced Level

Next.js 16.x

Node.js + TypeScript + Backend Architecture



## **1. Learning objectives**

In the previous phase, we learned how to build scalable applications using modular architecture, Redis, background jobs, queues, and horizontal scaling.

Now we will go one step further and learn how large applications communicate when their components run independently.

In this phase, you will learn:

1. Distributed systems fundamentals.
2. Synchronous vs asynchronous communication.
3. Event-driven architecture.
4. Event producers and consumers.
5. Publish/Subscribe (Pub/Sub) architecture.
6. Message brokers.
7. RabbitMQ fundamentals.
8. Apache Kafka fundamentals.
9. Eventual consistency.
10. Distributed transactions.
11. The Saga pattern.
12. The Transactional Outbox pattern.
13. Event-driven microservices.
14. Real-world distributed system design.

By the end, you should understand how systems such as e-commerce platforms, banking applications, ride-sharing platforms, and large SaaS applications coordinate operations across multiple services.

## **2. What is a distributed system?**

A distributed system is a collection of independent computers or services that communicate over a network and work together to perform a larger task.

In a traditional application, everything might run inside one application process.

For example:

```
E-commerce Application
│
├── User Module
├── Product Module
├── Order Module
├── Payment Module
└── Notification Module
```

All modules belong to one application.

In a distributed architecture, these responsibilities can run as separate services:

[Free Software Architecture Diagram Generator | Create System Diagrams Online - Figviz](https://images.openai.com/static-rsc-4/5byxmMSG4rQ4xIYEuWWjn6T1uA573D6__OelbHw_8IsuZpAIhONhEGwIMH9Hpxdwzv6jmCHx_iL-rV-1E2cpCKkAXOZit2xaCFzTeuFUv9VCk6t701zZyJV9VDU3_1lJwh0zAb4KJy-0T0AY9GnuD_P54ZosL7kvya1AGRnAGhE?purpose=inline)

[figviz.com](https://figviz.com/tools/software-architecture-diagram-generator?utm_source=chatgpt.com)

```
                 Client
                   |
                   v
               API Gateway
                   |
        +----------+----------+
        |          |          |
        v          v          v
    User Service Product Service Order Service
        |          |          |
      User DB   Product DB   Order DB
                              |
                              v
                         Payment Service
                              |
                              v
                        Notification Service
```

Each service can run independently, potentially on different servers or infrastructure.

### **2.1 Real-world examples**

**E-commerce application**

- Product service manages products.
- Order service creates orders.
- Payment service processes payments.
- Notification service sends confirmation messages.

**Banking application**

- Account service manages accounts.
- Transaction service records transactions.
- Fraud detection service analyzes transactions.
- Notification service sends alerts.

**Ride-sharing application**

- User service manages customers and drivers.
- Location service tracks locations.
- Matching service finds nearby drivers.
- Payment service handles fares.

### **2.2 Why do we need distributed systems?**

As applications grow, a single application may become difficult to scale or operate.

Distributed systems can provide:

- Independent service deployment.
- Independent scaling.
- Separation of business responsibilities.
- Workload isolation.
- Independent technology choices.
- Better organizational ownership.

However, they introduce new problems:

- Network failures.
- Message duplication.
- Data inconsistency.
- Distributed transactions.
- Increased operational complexity.
- Difficult debugging.

**Important:** A distributed system is not automatically more reliable than a monolith. It introduces additional failure points that must be handled deliberately.

## **3. Synchronous vs asynchronous communication**

Services need to communicate with each other.

There are two fundamental communication approaches.

### **3.1 Synchronous communication**

Synchronous communication means the caller sends a request and waits for a response before continuing.

Example:

```
Order Service
     |
     | HTTP Request
     v
Payment Service
     |
     | Process Payment
     |
     v
Payment Response
     |
     v
Order Service continues
```

Example using Node.js:

```
export async function processPayment(orderId: string) {
  const response = await fetch(
    "https://payment-service.example.com/payments",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ orderId }),
    }
  );

  if (!response.ok) {
    throw new Error("Payment failed");
  }

  return response.json();
}
```

The order service waits for the payment service to respond.

**Advantages:**

- Simple request-response flow.
- Immediate response.
- Easier to understand.
- Useful when the caller needs the result immediately.

**Disadvantages:**

- Caller depends on the availability of the other service.
- Network latency affects response time.
- Failures can propagate.
- Long operations can cause timeouts.

### **3.2 Asynchronous communication**

Asynchronous communication means the sender does not need to wait for the receiver to finish processing the task.

Instead, it sends a message or event to a broker or queue.

```
Order Service
     |
     | Publish Event
     v
Message Broker
     |
     +----------------+
     |                |
     v                v
Payment Service  Notification Service
     |                |
     v                v
Process Payment   Send Email
```

Example:

```
await orderQueue.add("process-order", {
  orderId: "ORD-1001",
});
```

The application can continue without waiting for the worker to finish the entire process.

### **3.3 Comparison**

| Feature          | Synchronous             | Asynchronous             |
| ---------------- | ----------------------- | ------------------------ |
| Communication    | Request-response        | Message/event            |
| Waiting          | Caller waits            | Caller can continue      |
| Coupling         | Usually tighter         | Often looser             |
| Response         | Immediate response      | Processed later          |
| Failure handling | Request errors/timeouts | Retry and failure queues |
| Best for         | Immediate results       | Background workflows     |

### **3.4 When should you use each?**

Use synchronous communication when:

- A user needs immediate feedback.
- A service needs a result before continuing.
- The operation is short.
- The request-response relationship is straightforward.

Use asynchronous communication when:

- A task takes a long time.
- Multiple services need to react to an event.
- The sender should not depend on immediate receiver availability.
- Tasks need retries and independent processing.

**Real-world example:** When a customer places an order, the application may immediately return an order confirmation after saving the order. Sending emails, generating invoices, and updating analytics can happen asynchronously.

## **4. What is Event-Driven Architecture (EDA)?**

Event-Driven Architecture is an architectural approach in which components communicate by producing and consuming events.

An event represents something that has already happened.

Examples:

- `UserRegistered`
- `OrderCreated`
- `PaymentCompleted`
- `OrderShipped`
- `ProductStockUpdated`
- `InvoiceGenerated`

[What Is an Event-Driven Microservices Architecture? | Akamai](https://images.openai.com/static-rsc-4/cF8fdH-HXVHU3rVJW3TZRWmabanFUg8yVxH0h5MfbjxkQjBj9fhhbQciJDGSUn7f9zJ2Xm8tXDqOYftVPFIMT26F5EpOzl8BxkY-n2hmvu1Cxq4Sv7D3VBkeDhNyaV7sxzFMjuNz_fS4whI-WEdahv8LKvrUhBy2U8532VL57G8?purpose=inline)

[akamai.com](https://www.akamai.com/blog/edge/what-is-an-event-driven-microservices-architecture?utm_source=chatgpt.com)

### **4.1 Understand the concept**

Suppose a customer places an order.

The Order Service creates the order and publishes:

```
{
  "event": "OrderCreated",
  "orderId": "ORD-1001",
  "userId": "USER-101",
  "total": 2500
}
```

Different services can react to this event.

| Consumer             | Action                       |
| -------------------- | ---------------------------- |
| Payment Service      | Initiates payment processing |
| Notification Service | Sends order confirmation     |
| Inventory Service    | Reserves product stock       |
| Analytics Service    | Updates sales statistics     |

The Order Service does not need to directly call every consumer.

This reduces direct dependencies between services.

### **4.2 Event vs command**

This distinction is very important.

**Command:** A request asking a component to perform an action.

Examples:

- `CreateOrder`
- `ProcessPayment`
- `SendEmail`
- `CancelOrder`

**Event:** A notification that something has already happened.

Examples:

- `OrderCreated`
- `PaymentCompleted`
- `EmailSent`
- `OrderCancelled`

| Command                                 | Event                           |
| --------------------------------------- | ------------------------------- |
| Expresses intent                        | Describes a fact                |
| Usually addressed to a specific handler | Can have multiple consumers     |
| May succeed or fail                     | Records something that happened |
| Example: `CreateOrder`                  | Example: `OrderCreated`         |

Do not name a successful business event as if it were an unfulfilled command.

### **4.3 Event structure**

A production event should generally include metadata.

Example:

```
{
  "id": "evt-10001",
  "type": "OrderCreated",
  "version": 1,
  "occurredAt": "2026-11-25T10:00:00.000Z",
  "source": "order-service",
  "data": {
    "orderId": "ORD-1001",
    "userId": "USER-101",
    "total": 2500
  }
}
```

Important fields:

| Field        | Purpose                 |
| ------------ | ----------------------- |
| `id`         | Unique event identifier |
| `type`       | Event name              |
| `version`    | Event schema version    |
| `occurredAt` | Event occurrence time   |
| `source`     | Originating service     |
| `data`       | Business information    |

An event ID is especially useful for deduplication.

### **4.4 Event-driven workflow**

```
Customer places order
         |
         v
    Order Service
         |
         v
   OrderCreated Event
         |
         v
    Message Broker
         |
   +-----+------+------+
   |            |      |
   v            v      v
Inventory    Payment  Notification
 Service     Service   Service
   |            |      |
   v            v      v
Reserve      Process   Send email
 stock       payment
```

This architecture is useful when multiple independent services need to respond to the same business event.

## **5. Event producers, consumers, and message brokers**

Event-driven systems generally have three important components.

### **5.1 Producer**

A producer is an application or service that creates and publishes messages.

Example:

```
await publishEvent({
  type: "OrderCreated",
  orderId: "ORD-1001",
});
```

The Order Service is the producer in this example.

### **5.2 Consumer**

A consumer receives and processes messages.

Example:

```
async function handleOrderCreated(event: {
  orderId: string;
}) {
  console.log("Processing order:", event.orderId);

  // Reserve inventory.
  // Update inventory records.
}
```

The Inventory Service is a consumer.

### **5.3 Message broker**

A message broker is infrastructure that receives, routes, and delivers messages between producers and consumers.

Examples:

- RabbitMQ.
- Apache Kafka.
- Amazon SQS.
- Google Cloud Pub/Sub.

[Message Queues vs Message Brokers](https://images.openai.com/static-rsc-4/CcfF-c4NMYmDb_uamMH6m2mwMEme-KJLlHr_5-uxBwGYFO4aCGkqoP5EtCKgetLGkCwCU7c47NR9Kq_War8ohLG_3Xql8dp37t6RRQxMqSUiy5YT7uMy2s7dwXSR9Rg3oTZQ692tJdXzFovrjf2rqaAjh9FFrKc9Xgck7BsXvPM?purpose=inline)

[linkedin.com](https://www.linkedin.com/pulse/message-queues-vs-brokers-vivek-bansal-76vqc?utm_source=chatgpt.com)

### **5.4 Why use a message broker?**

Suppose an Order Service needs to notify five different services.

Without a broker:

```
Order Service
   |
   +----> Payment Service
   |
   +----> Inventory Service
   |
   +----> Email Service
   |
   +----> Analytics Service
   |
   +----> Shipping Service
```

The Order Service must know about every destination and handle communication with each one.

With a broker:

```
Order Service
      |
      v
 Message Broker
      |
      +----> Payment Service
      |
      +----> Inventory Service
      |
      +----> Email Service
      |
      +----> Analytics Service
      |
      +----> Shipping Service
```

The producer publishes an event. The broker routes it to interested consumers.

This reduces direct coupling between the producer and consumers.

### **5.5 Important messaging concepts**

| Concept           | Explanation                                       |
| ----------------- | ------------------------------------------------- |
| Producer          | Publishes a message                               |
| Consumer          | Processes a message                               |
| Broker            | Receives and routes messages                      |
| Queue             | Stores messages for consumers                     |
| Topic             | A named stream or channel of messages             |
| Exchange          | RabbitMQ component that routes messages           |
| Acknowledgment    | Confirmation that a message was processed         |
| Retry             | Attempts to process a failed message again        |
| Dead-letter queue | Stores messages that cannot be processed normally |
| Consumer group    | A group of consumers sharing message processing   |
| Offset            | A consumer's position in a Kafka partition        |

These terms are related, but they are not interchangeable. For example, a RabbitMQ exchange and a Kafka topic serve different roles.

## **6. Publish/Subscribe (Pub/Sub) architecture**

### **6.1 What is Pub/Sub?**

Publish/Subscribe is a messaging pattern where producers publish messages to a topic or channel, and interested subscribers receive those messages.

The producer does not need to know who the subscribers are.

[Understanding the Differences Between Message Queues and Streaming - Pulsar Neighborhood](https://images.openai.com/static-rsc-4/NIXksSMkPM4Rs01BbgZIPLO08F-HExHFwyNYsxhhEf6tJ1FPmAVfloCb4TCxoJhhho_zSlmaOBJ3RM2vcxXJ5BammPzDOjGCSXoiXAiaZe0PxUaSXc6Vd0kv_pFjzXKF69MiwubZeCxE3Jec_eePoIAKpXscTMnMMF93TcoMIso?purpose=inline)

[pulsar-neighborhood.io](https://pulsar-neighborhood.io/articles/understanding-the-differences-between-message-queues-and-streaming/?utm_source=chatgpt.com)

Example:

A customer registers on your application.

The User Service publishes:

```
{
  "type": "UserRegistered",
  "userId": "USER-101",
  "email": "customer@example.com"
}
```

Multiple subscribers react:

| Subscriber        | Action                   |
| ----------------- | ------------------------ |
| Email Service     | Sends welcome email      |
| Analytics Service | Records registration     |
| CRM Service       | Creates customer profile |
| Referral Service  | Checks referral rewards  |

### **6.2 Point-to-point vs Pub/Sub**

**Point-to-point messaging**

A message is placed in a queue and typically processed by one competing consumer.

```
Producer
   |
   v
 Queue
   |
   v
Consumer
```

With multiple competing consumers, each message is generally handled by one consumer in the group.

**Publish/Subscribe**

A publication is delivered to multiple subscriptions, depending on the broker's routing and subscription configuration.

```
             Publisher
                 |
                 v
               Topic
           /     |     \
          v      v      v
       Sub A   Sub B   Sub C
          |      |      |
          v      v      v
       Email  Analytics CRM
```

### **6.3 Comparison**

| Feature      | Point-to-point                      | Pub/Sub                                        |
| ------------ | ----------------------------------- | ---------------------------------------------- |
| Main purpose | Distribute work                     | Distribute events                              |
| Consumers    | Usually competing consumers         | Multiple independent subscribers               |
| Delivery     | One consumer per message in a queue | Multiple subscriptions can each receive a copy |
| Example      | Generate one invoice                | Notify several services of an order            |

### **6.4 When should you use Pub/Sub?**

Use Pub/Sub when multiple independent components need to react to the same event.

For example, after an order is created:

- Analytics records the order.
- Notification sends an email.
- Inventory reserves stock.
- Shipping prepares fulfillment.

Each subscriber can process the event independently.

**Important:** Pub/Sub does not automatically guarantee that all subscribers process messages successfully. Each subscription still needs appropriate delivery, retry, acknowledgment, and monitoring configuration.

## **7. RabbitMQ fundamentals**

### **7.1 What is RabbitMQ?**

**RabbitMQ**

A message broker that routes messages between producers and consumers.

[RabbitMQ official tutorials](https://www.rabbitmq.com/tutorials?utm_source=chatgpt.com)

RabbitMQ is commonly used for:

- Background job processing.
- Service-to-service messaging.
- Task distribution.
- Request/reply messaging.
- Asynchronous workflows.
- Reliable message delivery.

### **7.2 RabbitMQ architecture**

RabbitMQ has several important components.

[Messaging Patterns Explained: Pub-Sub, Queues, and Event Streams](https://images.openai.com/static-rsc-4/mHOdx0uTABp37rrf1Ns8q0PTNJkkAqzU2Nva98Vz2PbDgib_ydJJMKtqEiqg_vgdsIPPqSsuYJBnleLNuIdaE6N5gktzYRC9QmHGdLsf00e5Sfx5PjTQwO0m_ShYWqROzwdqz4ohrI29HFAgpmDoroi2fhqm8eBDSbwvH2IMjy0?purpose=inline)

[blog.bytebytego.com](https://blog.bytebytego.com/p/messaging-patterns-explained-pub?utm_source=chatgpt.com)

**1. Producer**

Creates and sends a message.

**2. Exchange**

Receives the message and decides which queue or queues should receive it.

**3. Binding**

Defines the relationship between an exchange and a queue.

**4. Queue**

Stores messages until consumers process them.

**5. Consumer**

Receives and processes messages.

### **7.3 Understand the message flow**

```
Producer
   |
   v
Exchange
   |
   | Routing Rules
   |
   v
Queue
   |
   v
Consumer
   |
   v
Acknowledge Message
```

An important distinction:

A producer usually publishes to an exchange, not directly to a queue. RabbitMQ then routes the message according to the exchange type and its bindings.

### **7.4 Types of RabbitMQ exchanges**

RabbitMQ has four commonly used exchange types.

[RabbitMQ Message Routing: Exchanges and Bindings Explained](https://images.openai.com/static-rsc-4/CSLWACa39LfB5gMhFy7vWdtOK8ckWstn8jxB8Taw4rT4FudToZyGcrVCx6uBpgBmDmuHzkyYC34yEPXDTTGlWHOyxhQO5OB-chIM6_tDN8vzwBY9F4wjH1uObwRB3KHOa60S0JFcBTjJFRqzwdVSKaz_YW1A5Li94PVBF3kFiXA?purpose=inline)

**1. Direct Exchange**

Routes messages using an exact routing-key match.

Example: `order.created` goes to a queue bound with `order.created`.

[RabbitMQ Exchange Types: 6 Categories Explained Easy | Hevo](https://images.openai.com/static-rsc-4/pFEwbk8jv3VwYnyvTbVzhqNNJcWgbmN5QV3hUXIBD1EAf2wM1I_4TnYMORysV2BS7q-zjzvq9iY_PSuYqDNsjgG4eKOEPPaUfSPC7uKJ02IMmuRrgmHW23PShxqM3Qd3CeRIr8edtCv_XNXw6pZKQMk01QWshafstL1djxlZ2u8?purpose=inline)

**2. Fanout Exchange**

Broadcasts a message to all queues bound to that exchange.

Example: Notify several independent services about an event.

[RabbitMQ Exchange Types Explained](https://images.openai.com/static-rsc-4/mykGxAN7UN-XKv5W1j1iMd-HasfZYWXeTwrHG-UEWefqmQAQV1w2_7b_zbBgoeaMMObxIBV7cCDWfjE9iqMYjRgUXdCiA8VBQIwGUxafXCbdQeVJaDVZO9bd8kYGRN0ZpVtnVLJ_C5NK2E3RwVjrv09zUsuCtfLOi7pI-3ZG_to?purpose=inline)

**3. Topic Exchange**

Routes messages using routing-key patterns.

Example: `order.*` can match `order.created` and `order.cancelled`.

[RabbitMQ Architecture | A Journey Through RabbitMQ Architecture](https://images.openai.com/static-rsc-4/FoVsolmTwORINUR6N37y11kSlv8niDVSw60_3z-NozJE6CgSOLL5irX2-qAh77mJYGOutg6qTJuJv43X3Ck_G5UlOIcBjxkw5DJudubc8zg_l9hd59uPXTiYt4GaEqbxzFtBfVxBjKGeVy_QPoHvTNhKcj2LpBUMvjDH2aNM6E8?purpose=inline)

**4. Headers Exchange**

Routes messages according to message headers rather than the routing key.

### **7.5 RabbitMQ practical example using Node.js**

We will create a simple order notification workflow.

Install the package:

```
npm install amqplib
npm install -D @types/amqplib
```

Run RabbitMQ locally using Docker:

```
docker run -d \
  --name rabbitmq \
  -p 5672:5672 \
  -p 15672:15672 \
  rabbitmq:4-management
```

The management interface is available at:

`http://localhost:15672`

The standard development credentials in this container image are `guest` / `guest`. Do not use these defaults as production credentials.

#### **Step 1: Create a connection helper**

`lib/rabbitmq.ts`:

```
import "server-only";

import amqp from "amqplib";

const RABBITMQ_URL =
  process.env.RABBITMQ_URL ?? "amqp://localhost";

export async function createRabbitMQConnection() {
  return amqp.connect(RABBITMQ_URL);
}
```

#### **Step 2: Create a producer**

`producers/order.producer.ts`:

```
import { createRabbitMQConnection } from "@/lib/rabbitmq";

export async function publishOrderCreated(order: {
  id: string;
  total: number;
}) {
  const connection = await createRabbitMQConnection();

  const channel = await connection.createChannel();

  const exchange = "orders";
  const routingKey = "order.created";

  await channel.assertExchange(exchange, "direct", {
    durable: true,
  });

  const message = {
    type: "OrderCreated",
    orderId: order.id,
    total: order.total,
    occurredAt: new Date().toISOString(),
  };

  const published = channel.publish(
    exchange,
    routingKey,
    Buffer.from(JSON.stringify(message)),
    {
      persistent: true,
      contentType: "application/json",
    }
  );

  if (!published) {
    await new Promise<void>((resolve) => {
      channel.once("drain", resolve);
    });
  }

  await channel.close();
  await connection.close();
}
```

This is a teaching example. Production publishers should reuse connections, handle connection recovery, and use publisher confirms when they need confirmation that RabbitMQ has accepted a message.

#### **Step 3: Create a consumer**

`consumers/order.consumer.ts`:

```
import { createRabbitMQConnection } from "@/lib/rabbitmq";

async function startConsumer() {
  const connection = await createRabbitMQConnection();

  const channel = await connection.createChannel();

  const exchange = "orders";
  const queue = "order-notifications";

  await channel.assertExchange(exchange, "direct", {
    durable: true,
  });

  await channel.assertQueue(queue, {
    durable: true,
  });

  await channel.bindQueue(
    queue,
    exchange,
    "order.created"
  );

  await channel.prefetch(10);

  await channel.consume(queue, async (message) => {
    if (!message) return;

    try {
      const data = JSON.parse(
        message.content.toString()
      );

      console.log("Received order event:", data);

      // Send an actual notification here.

      channel.ack(message);
    } catch (error) {
      console.error("Message processing failed", error);

      // Reject without requeueing.
      // A production system should configure dead-letter handling.
      channel.nack(message, false, false);
    }
  });
}

startConsumer().catch(console.error);
```

#### **Step 4: Publish an event**

```
await publishOrderCreated({
  id: "ORD-1001",
  total: 2500,
});
```

The consumer receives and processes the message.

### **7.6 RabbitMQ reliability concepts**

**Durable queue**

A durable queue survives a broker restart, provided the broker's data is persisted correctly.

**Persistent message**

A message can be marked persistent so the broker can persist it to disk.

**Acknowledgment**

A consumer acknowledges a message after successfully processing it.

```
channel.ack(message);
```

**Negative acknowledgment**

A consumer can reject or negatively acknowledge a message.

```
channel.nack(message, false, false);
```

**Dead-letter exchange**

Messages rejected, expired, or otherwise dead-lettered can be routed to a separate exchange and queue for investigation or later handling.

**Publisher confirms**

Publisher confirms allow the producer to know that RabbitMQ has accepted responsibility for published messages.

Durable queues, persistent messages, acknowledgments, and publisher confirms solve different parts of reliable delivery; none alone guarantees exactly-once business processing.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.rabbitmq.com\&sz=32)

RabbitMQ



### **7.7 When should you choose RabbitMQ?**

RabbitMQ is a good option when you need:

- Task distribution.
- Work queues.
- Flexible message routing.
- Background processing.
- Request/reply patterns.
- Broker-managed acknowledgments and retries.

## **8. Apache Kafka fundamentals**

### **8.1 What is Apache Kafka?**

[Running Kafka on Production. Apache Kafka is an open-source… | by Pradyumna Khawas | WorkIndia.in | Medium](https://images.openai.com/static-rsc-4/IdgpctULbez35q_RTbMO09b-y6gGSwj0M361O82eRjmf4iacIuOlU2NJe7tJ5nG3ML_-Ramx8HPhw_f2BkJ4-7fTax9B_pG2F5mYYn7fI3Wkxu6lEMjmv6TQdRCI7p2vkYr-RROSNxhp8BCekHH4j-k8olCh620FHns5GrtDyDM?purpose=inline)

**Apache Kafka**

A distributed platform for publishing, storing, and consuming event streams.

[Apache Kafka official documentation](https://kafka.apache.org/documentation/?utm_source=chatgpt.com)

Kafka is commonly used for:

- High-volume event streaming.
- Real-time analytics.
- Data pipelines.
- Event-driven microservices.
- Log aggregation.
- Event replay.
- Data integration between systems.

### **8.2 Kafka architecture**

[All things Kafka - DEV Community](https://images.openai.com/static-rsc-4/DqEEx-xDjrg5xHA14DxSX52W9GJHkrUypD-yPUoFpY9KkD5eA8pCHjWW8I54BlPfiagvxaX8lEElYTxs50EEeem53qqfevYzhlpkp6boL7E9CPD5rkGHcNHDkgfd-AKMLzUnihPTo3CSJ-X1XPWUaQAlBORYa1hIhbB9zzW5SZY?purpose=inline)

[dev.to](https://dev.to/alamfatima1999/all-things-kafka-3cdb?utm_source=chatgpt.com)

Important components:

**1. Producer**

Publishes events to Kafka.

**2. Topic**

A named stream in which events are stored.

Example:

```
orders
payments
users
inventory
```

**3. Partition**

A topic is divided into partitions to support parallelism and distribution.

**4. Broker**

A Kafka server that stores and serves topic partitions.

**5. Consumer**

Reads events from topics.

**6. Consumer group**

A group of consumers that cooperates to process topic partitions.

### **8.3 What is a Kafka topic?**

A topic is a logical stream of related events.

For example:

```
Topic: orders

Event 1: OrderCreated
Event 2: OrderPaid
Event 3: OrderShipped
Event 4: OrderDelivered
```

Unlike a typical work queue, Kafka retains events according to its configured retention policy. Consumers can read them and track their own positions.

### **8.4 What is a partition?**

A partition is an ordered sequence of events within a topic.

Example:

```
Topic: orders

Partition 0:
  Event 1
  Event 2
  Event 3

Partition 1:
  Event 4
  Event 5
  Event 6

Partition 2:
  Event 7
  Event 8
```

Partitions allow Kafka to distribute data and processing across brokers and consumers.

**Important:** Kafka guarantees ordering within a partition, not across all partitions of a topic.

If events with the same key are assigned to the same partition, their order is preserved within that partition.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kafka.apache.org\&sz=32)

Apache Kafka



### **8.5 What is a consumer group?**

A consumer group is a group of consumers that share the work of reading a topic.

Example:

```
Topic: orders
3 partitions

        Consumer Group
        /     |      \
       v      v       v
    Consumer A  B    C
       |        |     |
       v        v     v
      P0       P1    P2
```

In a single consumer group, each partition is assigned to at most one consumer at a time.

If a topic has three partitions, three consumers can process those partitions concurrently in that group.

Adding more consumers than available partitions does not automatically increase parallelism.

### **8.6 Kafka practical example using Node.js**

Install KafkaJS:

```
npm install kafkajs
```

Create a Kafka client:

`lib/kafka.ts`:

```
import "server-only";

import { Kafka } from "kafkajs";

export const kafka = new Kafka({
  clientId: "ecommerce-app",
  brokers: ["localhost:9092"],
});
```

For a local Kafka environment, start a Kafka broker using an appropriate development setup. The broker address and security configuration must match your environment.

#### **Step 1: Create a producer**

`producers/order.producer.ts`:

```
import { kafka } from "@/lib/kafka";

const producer = kafka.producer();

export async function publishOrderCreated(order: {
  id: string;
  userId: string;
  total: number;
}) {
  await producer.connect();

  await producer.send({
    topic: "orders",
    messages: [
      {
        key: order.id,
        value: JSON.stringify({
          type: "OrderCreated",
          orderId: order.id,
          userId: order.userId,
          total: order.total,
          occurredAt: new Date().toISOString(),
        }),
      },
    ],
  });
}
```

#### **Step 2: Create a consumer**

`consumers/order.consumer.ts`:

```
import { kafka } from "@/lib/kafka";

const consumer = kafka.consumer({
  groupId: "notification-service",
});

async function startConsumer() {
  await consumer.connect();

  await consumer.subscribe({
    topic: "orders",
    fromBeginning: false,
  });

  await consumer.run({
    eachMessage: async ({
      topic,
      partition,
      message,
    }) => {
      if (!message.value) return;

      const event = JSON.parse(
        message.value.toString()
      );

      console.log({
        topic,
        partition,
        event,
      });

      // Process the event here.
    },
  });
}

startConsumer().catch(console.error);
```

#### **Step 3: Publish an order event**

```
await publishOrderCreated({
  id: "ORD-1001",
  userId: "USER-101",
  total: 2500,
});
```

The consumer group reads the event and processes it.

This example leaves the producer connection open intentionally for a long-running process. In production, connect once, reuse the producer, and implement graceful shutdown and error handling.

### **8.7 Kafka offsets**

Kafka tracks consumer progress using offsets.

An offset identifies a record's position within a partition.

Example:

```
Partition 0

Offset 0 -> OrderCreated
Offset 1 -> OrderPaid
Offset 2 -> OrderShipped
Offset 3 -> OrderDelivered
```

A consumer group commits offsets to record its progress.

If a consumer restarts, it can resume from its committed offset, subject to the configured offset-reset policy and available retention.

### **8.8 Kafka retention and replay**

Suppose an analytics service has a bug and fails to process some events.

Because Kafka retains events according to its retention policy, the consumer can potentially reset its offsets and replay retained events.

This is valuable for:

- Rebuilding analytics.
- Reprocessing events after fixing a bug.
- Creating new read models.
- Recovering downstream systems.

Kafka is therefore more than a simple task queue. It is an event streaming platform with persistent, replayable event logs.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://kafka.apache.org\&sz=32)

Apache Kafka



## **9. RabbitMQ vs Apache Kafka**

This is one of the most common distributed-systems interview questions.

| Feature             | RabbitMQ                                                                   | Apache Kafka                                           |
| ------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------ |
| Primary model       | Message broker                                                             | Event streaming platform                               |
| Main concepts       | Exchanges, queues, bindings                                                | Topics, partitions, offsets                            |
| Message retention   | Usually until consumed/acknowledged or expired, depending on configuration | Configurable retention                                 |
| Replay              | Not its typical work-queue model                                           | Built-in through consumer offsets and retained records |
| Routing             | Rich exchange-based routing                                                | Topic and partition-based publishing                   |
| Parallel processing | Competing consumers                                                        | Consumer groups and partitions                         |
| Typical use         | Jobs, task distribution, routing                                           | Event streams, analytics, data pipelines               |
| Ordering            | Depends on queue and consumer configuration                                | Within a partition                                     |
| Best fit            | Work queues and flexible routing                                           | High-volume event streaming                            |

### **Which one should you choose?**

**Choose RabbitMQ when:**

- You need task queues.
- You need flexible routing.
- You are distributing background jobs.
- You need traditional message acknowledgment workflows.

**Choose Kafka when:**

- You need persistent event streams.
- You need event replay.
- You have high-volume data pipelines.
- Multiple consumer groups need to independently process the same events.
- You need partition-based scaling.

Neither is universally better. Choose based on delivery, routing, ordering, retention, throughput, and operational requirements.

## **10. Message delivery guarantees**

Distributed systems cannot assume that a message is delivered exactly once simply because the producer sent it once.

Messages can be lost, duplicated, delayed, or processed more than once depending on the system and failure conditions.

There are three commonly discussed delivery semantics.

### **10.1 At-most-once delivery**

A message is delivered zero or one time.

```
Producer
   |
   v
Broker
   |
   v
Consumer
```

If a message is lost, it is not necessarily delivered again.

**Advantage:** No duplicate delivery from retries.

**Disadvantage:** Messages can be lost.

Example use case: Some non-critical telemetry where occasional loss is acceptable.

### **10.2 At-least-once delivery**

A message is delivered one or more times until the system considers it successfully processed.

Example:

```
Broker
  |
  v
Consumer processes message
  |
  v
Consumer crashes before acknowledgment
  |
  v
Broker redelivers message
  |
  v
Consumer processes it again
```

The same message can be processed more than once.

**Advantage:** Helps avoid losing messages.

**Disadvantage:** Duplicate processing is possible.

This is why consumers should be idempotent.

### **10.3 Exactly-once semantics**

Exactly-once is a more complex guarantee.

It means that, within a defined system boundary and under specified conditions, processing has exactly-once effects.

For example, Kafka supports transactional and idempotent producer features that can provide exactly-once processing guarantees for certain Kafka-to-Kafka workflows.

However, this does not automatically guarantee exactly-once effects in an external database, payment provider, or email service.

External side effects still require careful transaction coordination and idempotency.

### **10.4 Comparison**

| Delivery semantic | Delivery count                      | Main concern                          |
| ----------------- | ----------------------------------- | ------------------------------------- |
| At-most-once      | Zero or one                         | Possible loss                         |
| At-least-once     | One or more                         | Possible duplicates                   |
| Exactly-once      | Exactly once within a defined scope | Complex implementation and boundaries |

### **10.5 What is an idempotent consumer?**

An idempotent consumer can process the same message repeatedly without creating duplicate business effects.

Suppose the consumer receives:

```
{
  "eventId": "evt-1001",
  "type": "PaymentCompleted",
  "orderId": "ORD-1001"
}
```

The consumer can maintain a record of processed event IDs.

Example using a relational database:

```
async function processPaymentEvent(event: {
  eventId: string;
  orderId: string;
}) {
  await db.$transaction(async (tx) => {
    const existing = await tx.processedEvent.findUnique({
      where: {
        eventId: event.eventId,
      },
    });

    if (existing) {
      return;
    }

    await tx.order.update({
      where: {
        id: event.orderId,
      },
      data: {
        paymentStatus: "paid",
      },
    });

    await tx.processedEvent.create({
      data: {
        eventId: event.eventId,
      },
    });
  });
}
```

The `eventId` should have a unique database constraint.

The transaction ensures that the business update and processed-event record are committed together.

If the same event arrives again, the consumer detects it and skips the duplicate business operation.

**Important:** Idempotency is especially important for payments, inventory updates, orders, and financial transactions.

## **11. Eventual consistency**

### **11.1 What is consistency?**

Consistency means that data across a system follows the application's correctness rules.

In a traditional database transaction, multiple changes can be committed atomically.

For example:

```
BEGIN TRANSACTION

Create Order
Update Inventory
Record Payment

COMMIT
```

All operations succeed together, or the transaction rolls back.

But in a distributed system, these operations may belong to separate services with separate databases.

A single ordinary database transaction cannot automatically make all those databases commit together.

### **11.2 What is eventual consistency?**

Eventual consistency means that different parts of a distributed system may temporarily contain different versions of data, but they are expected to converge to a consistent state if updates are successfully propagated and no newer conflicting updates intervene.

Example:

A customer places an order.

Immediately after order creation:

| Service              | Current state        |
| -------------------- | -------------------- |
| Order Service        | Order created        |
| Inventory Service    | Stock update pending |
| Analytics Service    | Sales update pending |
| Notification Service | Email pending        |

After asynchronous processing:

| Service              | Updated state     |
| -------------------- | ----------------- |
| Order Service        | Order created     |
| Inventory Service    | Stock reserved    |
| Analytics Service    | Sale recorded     |
| Notification Service | Confirmation sent |

The services do not update simultaneously, but the system converges as events are processed.

[Mastering Data Consistency Across Microservices](https://images.openai.com/static-rsc-4/4EysJ3G3o5QLVDErrbgySetqd1SwYjyF1q8rsjG7o6MNj_LZIS1lYReSGxpE-qwXTEXIWo6hiwo7VxvpjB6nA_5wCIGMEBdqaTW5v3dF0fXPXJR90d-YrAHobPUJ3uOQyZTzIgBgLZYqXHON7v3R7D-jhNtnGO7_N9kUuBvt35I?purpose=inline)

[blog.bytebytego.com](https://blog.bytebytego.com/p/mastering-data-consistency-across?utm_source=chatgpt.com)

### **11.3 Strong consistency vs eventual consistency**

| Feature         | Strong consistency                                              | Eventual consistency                   |
| --------------- | --------------------------------------------------------------- | -------------------------------------- |
| Data visibility | Updates are immediately consistent within the defined guarantee | Temporary differences are possible     |
| User experience | May require waiting for coordination                            | Can respond faster in some workflows   |
| Complexity      | Coordination can be expensive                                   | Requires handling stale data           |
| Common use      | Financial balances and critical invariants                      | Analytics and asynchronous projections |

This is a simplified comparison. Distributed databases offer different consistency models and guarantees, so always examine the specific system.

### **11.4 Real-world example**

Suppose an order has been created.

The customer immediately sees:

```
Order Status: Confirmed
Payment Status: Pending
Inventory Status: Processing
```

After a few seconds:

```
Order Status: Confirmed
Payment Status: Paid
Inventory Status: Reserved
```

The interface can communicate intermediate states rather than pretending every service has already completed its work.

### **11.5 When is eventual consistency acceptable?**

It is often suitable for:

- Analytics dashboards.
- Activity feeds.
- Search indexes.
- Notification delivery.
- Recommendation systems.
- Reporting systems.

It may be inappropriate without additional safeguards for:

- Available inventory during concurrent purchases.
- Financial balances.
- Payment authorization.
- Unique order constraints.

**Important:** Eventual consistency does not mean "ignore consistency." It means designing the application around temporary inconsistency and defining how the system reaches a correct final state.

## **12. Distributed transactions**

### **12.1 What is a distributed transaction?**

A distributed transaction is a business operation that involves multiple independent services or databases.

Example: Customer purchases a product.

The workflow may require:

1. Create an order.
2. Reserve inventory.
3. Charge payment.
4. Confirm the order.
5. Schedule shipping.

Each service may maintain its own database.

```
Order Service
     |
     v
Inventory Service
     |
     v
Payment Service
     |
     v
Shipping Service
```

What happens if payment fails after inventory has already been reserved?

This is the distributed transaction problem.

### **12.2 Why not use a normal database transaction?**

A normal database transaction typically protects operations within one database transaction boundary.

But the order, inventory, and payment services may use different databases and different external systems.

You cannot simply write:

```
BEGIN TRANSACTION;

-- Update Order DB
-- Update Inventory DB
-- Charge Payment Provider

COMMIT;
```

and expect one database to control every remote system.

### **12.3 Two-Phase Commit (2PC)**

Two-Phase Commit is a distributed transaction protocol.

It involves a coordinator and participants.

**Phase 1 – Prepare**

The coordinator asks participants whether they can commit.

**Phase 2 – Commit**

If all participants vote to commit, the coordinator tells them to commit. Otherwise, it tells them to abort.

[Distributed Transaction. As a software engineer, I was always… | by Mehdi Korda | Medium](https://images.openai.com/static-rsc-4/cjBRcupnNQU76Im3ozDUx5mtQjitkfjXprzeTQWGwT0OOhME5lGZ3rxlo9Y-t9CrGFCSmw39F5e18IF1Jnw7lRBPKc903xLQx9mTOXyksUjgF34iq6mLmPKEWJHrT1naqKmuyYYWFFw3hDh6eDtx84TVobTzQbqfc9R20_97kJE?purpose=inline)

[medium.com](https://medium.com/%40kordamehdi/distributed-transaction-af90487b69bd?utm_source=chatgpt.com)

Simplified workflow:

```
Coordinator
    |
    | PREPARE
    |
    +------> Database A
    |
    +------> Database B
    |
    +------> Database C

All participants ready?
    |
    +---- Yes ----> COMMIT
    |
    +---- No -----> ABORT
```

**Advantages:**

- Coordinates a transaction across participating systems.
- Can provide atomic commit when all participants support the protocol.

**Disadvantages:**

- Participants may remain blocked while waiting.
- Requires coordinator and participant support.
- Operational complexity increases.
- It is often unsuitable across independent microservices and external services.

For many microservice architectures, the Saga pattern is a more practical alternative.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.aws.amazon.com\&sz=32)

AWS Prescriptive Guidance+1



## **13. Saga pattern**

### **13.1 What is the Saga pattern?**

The Saga pattern manages a business transaction across multiple independent services by breaking it into a sequence of local transactions.

Each service commits its own local transaction.

If a later step fails, the workflow can execute compensating transactions to counteract the earlier completed steps.

[Why 2-Phase Commit Fails in Microservices — And How the Saga Pattern Saves the Day - DEV Community](https://images.openai.com/static-rsc-4/UA8K_hVNbsObE6pvUBwti7so25VirVEOO2KxNVUuDIcv8bq8GTjSRwNyjdgFx73L2KamrfO2MsJ6M5uCcoT13yNzYs6lo146ZIdhc-uCnMTqplpFjGCUKfT2jbomf0iyo8XCz6oSnV8X7nH349xOWrFPBBsu9faYSlaEe2c0kak?purpose=inline)

[dev.to](https://dev.to/akshay161099/why-2-phase-commit-fails-in-microservices-and-how-the-saga-pattern-saves-the-day-3f9p?utm_source=chatgpt.com)

### **13.2 Example: E-commerce order**

Suppose a customer purchases a product.

The workflow:

```
Step 1: Create Order
          |
          v
Step 2: Reserve Inventory
          |
          v
Step 3: Process Payment
          |
          v
Step 4: Confirm Order
```

Now suppose payment fails.

We need to compensate for previous operations:

```
Payment Failed
       |
       v
Release Inventory
       |
       v
Cancel Order
```

### **13.3 What is a compensating transaction?**

A compensating transaction is a business operation that counteracts a previously completed operation.

| Original operation   | Compensating operation            |
| -------------------- | --------------------------------- |
| Reserve inventory    | Release inventory                 |
| Create pending order | Cancel order                      |
| Charge payment       | Refund payment                    |
| Book hotel           | Cancel booking, subject to policy |

A compensation is not necessarily a literal rollback.

For example, a payment refund is a new financial transaction. It does not erase the original charge.

### **13.4 Saga choreography**

In choreography, services coordinate by publishing and consuming events.

There is no central workflow controller.

[Saga コレオグラフィパターン - AWS 規範ガイダンス](https://images.openai.com/static-rsc-4/1sw0sY0DKpabA2JR_wvmhGkJWC_ovO_ytw39oflwKWJZKxi2rpy8l9UwMWK38QOCWF5NT-KzOAjOeCZhS8m-pF6fKKULIOHtv6I_qu7O59asoIeNJHjWyMszHK8002LD-Rc9V4Os_ojLqxhlfv-ugVdtS4a3P4DtjMDBMKIsD9g?purpose=inline)

[docs.aws.amazon.com](https://docs.aws.amazon.com/ja_jp/prescriptive-guidance/latest/cloud-design-patterns/saga-choreography.html?utm_source=chatgpt.com)

Workflow:

```
Order Service
     |
     | OrderCreated
     v
Inventory Service
     |
     | InventoryReserved
     v
Payment Service
     |
     | PaymentCompleted
     v
Order Service
     |
     | OrderConfirmed
     v
Notification Service
```

If payment fails:

```
Payment Service
      |
      | PaymentFailed
      v
Inventory Service
      |
      | InventoryReleased
      v
Order Service
      |
      | OrderCancelled
```

Advantages:

- No central coordinator.
- Services react independently.
- Natural fit for event-driven communication.

Disadvantages:

- Workflow becomes difficult to understand as participants increase.
- Event dependencies can become complicated.
- Troubleshooting requires tracing events across services.

### **13.5 Saga orchestration**

In orchestration, a central coordinator manages the workflow.

[Building a serverless distributed application using a saga orchestration pattern | AWS Compute Blog](https://images.openai.com/static-rsc-4/NWJwmRCRDdFuSoOIdmmXJtSTiFhDDJRvwUptKNh2Bv_Lxi-r_k7iI08pB2zEog7_vaaX6jtH7lJvkxGrmjY2wLhWriedIZY5CkUHMeZICVLkg1ehWPu0kUzmtEvnpcbkX8GEkM_cP599J3lZj3sySwChle0cHhwlzDHi1XEuCQg?purpose=inline)

[aws.amazon.com](https://aws.amazon.com/blogs/compute/building-a-serverless-distributed-application-using-a-saga-orchestration-pattern/?utm_source=chatgpt.com)

Example:

```
           Order Saga
          Orchestrator
                |
       +--------+--------+
       |        |        |
       v        v        v
     Order  Inventory  Payment
    Service  Service   Service
```

The orchestrator decides which operation should run next.

Example:

```
1. Create Order
2. Reserve Inventory
3. Process Payment
4. Confirm Order

If payment fails:
5. Release Inventory
6. Cancel Order
```

Advantages:

- Centralized workflow visibility.
- Easier to understand complex business processes.
- Compensation logic can be managed in one place.

Disadvantages:

- The orchestrator requires reliable operation and recovery.
- It introduces another component.
- A poorly designed orchestrator can become tightly coupled to service internals.

### **13.6 Choreography vs orchestration**

| Feature           | Choreography               | Orchestration                 |
| ----------------- | -------------------------- | ----------------------------- |
| Coordinator       | No central coordinator     | Central orchestrator          |
| Communication     | Event-driven               | Commands and responses/events |
| Workflow logic    | Distributed among services | Centralized                   |
| Simple workflows  | Often suitable             | Also suitable                 |
| Complex workflows | Harder to follow           | Often easier to manage        |
| Main challenge    | Event dependencies         | Coordinator complexity        |

AWS describes choreography as event-based coordination and orchestration as coordination through a central controller.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.aws.amazon.com\&sz=32)

AWS Prescriptive Guidance+2



### **13.7 When should you use Saga?**

Use Saga when:

- A business operation spans multiple services.
- Each service owns its own database.
- A single ACID transaction cannot cover the workflow.
- Failures require compensating actions.
- The process may take longer than a normal HTTP request.

## **14. Transactional Outbox pattern**

This is one of the most important patterns in reliable event-driven architecture.

### **14.1 The dual-write problem**

Suppose an Order Service needs to:

1. Save an order in the database.
2. Publish an `OrderCreated` event to RabbitMQ or Kafka.

A naive implementation:

```
await db.order.create({
  data: {
    id: "ORD-1001",
    status: "pending",
  },
});

await publishOrderCreated({
  orderId: "ORD-1001",
});
```

What if the database operation succeeds, but the application crashes before publishing the event?

[Implementing the Outbox Pattern for Reliable Messaging in .NET Modular Monoliths Architecture | by Mehmet Ozkaya | Medium](https://images.openai.com/static-rsc-4/DQZuQoNW9hsx4EbfyZnxAc--JZhlzDwe7sHkmrOjk_XAw1128NJ6d-lx5oRYYLDtYKmA96H91ML64vf29vcVOpMQf7mLAI2at_Y-C74nZZCoJMp6Kxa35DwemLaeNEVGO76GBnpI5IvohHzuUkm59ysA6CmDrj1FS4GLQecDm8E?purpose=inline)

[mehmetozkaya.medium.com](https://mehmetozkaya.medium.com/implementing-the-outbox-pattern-for-reliable-messaging-in-net-modular-monoliths-architecture-8fa1a68835b0?utm_source=chatgpt.com)

Result:

- Order exists.
- Event was never published.
- Inventory and other services never learn about the order.

There is another failure scenario:

The event publishes successfully, but the database transaction rolls back.

Now downstream services may process an event about an order that does not exist.

This is called the dual-write problem.

### **14.2 How does the Transactional Outbox solve it?**

The Transactional Outbox pattern stores the business data and the event in the same database transaction.

A separate relay process publishes the stored event to the message broker.

[Reliable Event Delivery with the Transactional Outbox Pattern | by Bakti Pratama | Coffee☕ And Code💚 | Medium](https://images.openai.com/static-rsc-4/kKvmF_Vwpe6nWByMpNiHhjuxzXFzh9YJ7LkqtepKyrlllhTCOPwXiT89JLQqE18twXKv5SKv4Pb4Jqr5FMiGh7NHEUQko9E8OIsvZPzxUdNpP9Z4kBy0K2lMmnlxjlbBAtOvmIK8egLlT3ehmpWdLUCNkbtbdHksp2CauE7f1lc?purpose=inline)

[medium.com](https://medium.com/techtrends-digest/transactional-outbox-pattern-5f883d7c603c?utm_source=chatgpt.com)

Workflow:

```
Order Service
     |
     v
Database Transaction
     |
     +------> Save Order
     |
     +------> Save Outbox Event
                 |
                 v
            Transaction Commit
                 |
                 v
             Outbox Relay
                 |
                 v
            Message Broker
                 |
                 v
              Consumers
```

If the database transaction fails, neither the order nor its outbox event is committed.

If it succeeds, the event remains available for publication even if the relay is temporarily unavailable.

### **14.3 Example database schema**

```
CREATE TABLE outbox_events (
    id VARCHAR(100) PRIMARY KEY,
    event_type VARCHAR(100) NOT NULL,
    payload JSON NOT NULL,
    created_at TIMESTAMP NOT NULL,
    published_at TIMESTAMP NULL
);
```

### **14.4 Save order and event in one transaction**

Example using Prisma:

```
import { db } from "@/lib/db";

export async function createOrder() {
  const orderId = crypto.randomUUID();
  const eventId = crypto.randomUUID();

  await db.$transaction(async (tx) => {
    await tx.order.create({
      data: {
        id: orderId,
        status: "pending",
      },
    });

    await tx.outboxEvent.create({
      data: {
        id: eventId,
        eventType: "OrderCreated",
        payload: {
          orderId,
        },
      },
    });
  });

  return orderId;
}
```

Both records are saved atomically within the same database transaction.

### **14.5 Create an outbox relay**

A separate process retrieves unpublished events and publishes them.

Simplified pseudocode:

```
async function publishPendingEvents() {
  const events = await db.outboxEvent.findMany({
    where: {
      publishedAt: null,
    },
    take: 100,
  });

  for (const event of events) {
    await messageBroker.publish({
      id: event.id,
      type: event.eventType,
      data: event.payload,
    });

    await db.outboxEvent.update({
      where: {
        id: event.id,
      },
      data: {
        publishedAt: new Date(),
      },
    });
  }
}
```

This is a conceptual example, not a safe concurrent production relay.

A production implementation needs:

- Safe claiming or leasing of events.
- Retry and backoff.
- Publisher confirmation.
- Duplicate handling.
- Correct ordering where required.
- Monitoring of unpublished events.
- A cleanup or archival strategy.

### **14.6 Why can duplicate events still happen?**

Suppose:

1. The relay publishes an event successfully.
2. The relay crashes before marking it as published.
3. The relay restarts.
4. The same event is published again.

Therefore, the outbox pattern normally provides reliable publication with possible duplicate delivery—not automatic exactly-once processing.

Consumers should deduplicate events using their IDs and make business processing idempotent.

This is an essential production detail.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://microservices.io\&sz=32)

microservices.io+2



## **15. Event-driven architecture vs Event Sourcing**

These two concepts are related but are not the same.

### **15.1 Event-driven architecture**

Event-driven architecture describes how services communicate and react to events.

Example:

```
Order Service
      |
      v
OrderCreated Event
      |
      v
Notification Service
```

The order may still be stored in a normal database table or document.

### **15.2 Event Sourcing**

Event Sourcing is a data-storage approach in which application state is derived from a sequence of events.

Instead of storing only the current order state, the system stores the events that produced it.

Example:

```
OrderCreated
     |
     v
PaymentCompleted
     |
     v
OrderShipped
     |
     v
OrderDelivered
```

The current state is reconstructed by applying the events in sequence.

### **15.3 Example**

Suppose an order has these events:

```
[
  {
    "type": "OrderCreated",
    "status": "pending"
  },
  {
    "type": "PaymentCompleted",
    "status": "paid"
  },
  {
    "type": "OrderShipped",
    "status": "shipped"
  }
]
```

The current order status is `shipped`.

### **15.4 Event-driven vs Event Sourcing**

| Feature      | Event-driven architecture        | Event Sourcing                           |
| ------------ | -------------------------------- | ---------------------------------------- |
| Main purpose | Communication between components | Persisting state changes as events       |
| Storage      | Can use normal databases         | Event log is the source of truth         |
| Events       | Used for communication           | Used to reconstruct state                |
| Complexity   | Depends on architecture          | Often significantly higher               |
| Requirement  | Reacting to events               | Rebuilding application state from events |

You can build an event-driven system without using Event Sourcing.

You can also use Event Sourcing together with Pub/Sub and message brokers.

## **16. Real-world example: Complete order processing architecture**

Let's combine the concepts.

Suppose a customer purchases a product from an e-commerce website.

[Saga Pattern in Microservices: Distributed Transactions with Spring Boot, and Service Mesh | by Bhuman Soni | Dec, 2025 | Medium](https://images.openai.com/static-rsc-4/E1GCHDuEKYlJSbK84_zuO3DPCg3gHtYPp846v9csvPiY4FjI8HqoFaEtE8c97kBglzYijZUpYGKSANNTph3aiuFENOqOHEfSA3HXJJby-3U-8bsodRSsl7ZaMzxuWl0a6SAWXSSrU8u0Z-mtiWe8LxsCVArMVE2rFeAJluwDZfk?purpose=inline)

[medium.com](https://medium.com/%40bhuman.soni/saga-pattern-in-microservices-distributed-transactions-with-spring-boot-and-service-mesh-c352da074c45?utm_source=chatgpt.com)

### **Step 1 – Customer submits order**

Next.js receives the request.

The Order Service:

- Validates the request.
- Checks the authenticated customer.
- Creates a pending order.
- Stores an `OrderCreated` event in its outbox.

Both records are committed in one database transaction.

### **Step 2 – Outbox relay publishes the event**

The relay publishes `OrderCreated` to the message broker.

### **Step 3 – Inventory Service consumes the event**

It checks available stock and reserves the requested quantity.

If successful, it publishes:

```
{
  "type": "InventoryReserved",
  "orderId": "ORD-1001"
}
```

If unsuccessful, it publishes:

```
{
  "type": "InventoryReservationFailed",
  "orderId": "ORD-1001"
}
```

### **Step 4 – Payment Service processes payment**

After receiving the inventory reservation event, the Payment Service attempts payment.

If successful:

```
{
  "type": "PaymentCompleted",
  "orderId": "ORD-1001",
  "transactionId": "TXN-1001"
}
```

If unsuccessful:

```
{
  "type": "PaymentFailed",
  "orderId": "ORD-1001"
}
```

### **Step 5 – Order Service updates the order**

On payment success:

```
Order Status: Confirmed
Payment Status: Paid
```

On payment failure:

```
Order Status: Cancelled
Payment Status: Failed
```

### **Step 6 – Notification Service**

The Notification Service consumes relevant events.

It sends:

- Order confirmation.
- Payment confirmation.
- Payment failure notifications.

### **Complete workflow**

```
Customer
   |
   v
Next.js Application
   |
   v
Order Service
   |
   v
Database + Outbox
   |
   v
Message Broker
   |
   v
Inventory Service
   |
   v
InventoryReserved
   |
   v
Payment Service
   |
   +------ PaymentCompleted ------+
   |                              |
   v                              v
Order Service              Notification Service
   |                              |
   v                              v
Confirm Order               Send Confirmation
```

If payment fails, the Saga executes compensating operations, such as releasing inventory and cancelling the pending order.

**Architecture takeaway:** A reliable distributed workflow combines events, message delivery guarantees, idempotent consumers, local transactions, compensating actions, and observability.

## **17. Common distributed-system mistakes**

| Mistake                                                       | Why it is dangerous                                | Better approach                              |
| ------------------------------------------------------------- | -------------------------------------------------- | -------------------------------------------- |
| Assuming messages are delivered exactly once                  | Duplicate processing can occur                     | Idempotent consumers                         |
| Publishing events before committing database changes          | Events can describe rolled-back data               | Transactional Outbox                         |
| Publishing after commit without an outbox                     | Application can crash before publishing            | Transactional Outbox                         |
| No message acknowledgment strategy                            | Messages may be lost or repeatedly delivered       | Configure acknowledgments                    |
| No retry strategy                                             | Temporary failures become permanent                | Bounded retries and backoff                  |
| Infinite retries                                              | Failed messages can consume resources indefinitely | Dead-letter handling                         |
| Ignoring event ordering                                       | State transitions can become incorrect             | Partitioning, sequencing, or version checks  |
| Using Kafka like a simple queue without understanding offsets | Replay and consumer behavior become confusing      | Understand partitions and consumer groups    |
| Making consumers non-idempotent                               | Duplicate events can create duplicate operations   | Event ID deduplication                       |
| No event versioning                                           | Schema changes can break consumers                 | Versioned event contracts                    |
| Too many synchronous service calls                            | Latency and failure propagation                    | Asynchronous communication where appropriate |
| Using Saga without compensation logic                         | Partial business operations remain incomplete      | Define compensation for every relevant step  |
| No monitoring of queues                                       | Messages can remain unprocessed                    | Monitor queue depth, lag, and failures       |
| Treating Event Sourcing and EDA as identical                  | Leads to unnecessary architecture complexity       | Understand their different purposes          |

## **18. Interview questions and answers**

### **Basic-level questions**

**Q1. What is a distributed system?**

A distributed system is a collection of independent computers or services that communicate over a network to perform a larger task.

**Q2. What is Event-Driven Architecture?**

It is an architecture in which components communicate by publishing and consuming events representing things that have happened.

**Q3. What is an event?**

An event is an immutable representation of a fact that has already happened, such as `OrderCreated`.

**Q4. What is a message broker?**

A message broker receives, routes, and delivers messages between producers and consumers.

**Q5. What is a producer?**

A producer is a component that publishes messages or events.

**Q6. What is a consumer?**

A consumer is a component that receives and processes messages or events.

**Q7. What is Pub/Sub?**

Publish/Subscribe is a messaging pattern in which publishers send messages to topics or channels and subscribers receive messages they are interested in.

**Q8. What is RabbitMQ?**

RabbitMQ is a message broker that supports exchanges, queues, bindings, routing, and message acknowledgments.

**Q9. What is Apache Kafka?**

Apache Kafka is a distributed event streaming platform used for storing and processing high-volume event streams.

**Q10. What is a Kafka topic?**

A Kafka topic is a named stream of related events.

### **Intermediate-level questions**

**Q11. What is the difference between synchronous and asynchronous communication?**

Synchronous communication requires the caller to wait for a response. Asynchronous communication allows processing to continue independently of the receiver's completion.

**Q12. What is the difference between RabbitMQ and Kafka?**

RabbitMQ is commonly used for message routing and work queues. Kafka is designed around persistent, partitioned event streams and supports replay through consumer offsets.

**Q13. What is a Kafka partition?**

A partition is an ordered sequence of records within a topic. Partitions enable parallelism and distribution.

**Q14. What is a Kafka consumer group?**

A consumer group is a collection of consumers that share responsibility for processing topic partitions.

**Q15. What is an acknowledgment?**

An acknowledgment is a confirmation that a consumer has successfully processed a message, according to the broker's acknowledgment mechanism.

**Q16. What is eventual consistency?**

It is a consistency model in which different parts of a distributed system may temporarily have different data but are expected to converge over time.

**Q17. What is an idempotent consumer?**

It is a consumer that can process the same event repeatedly without creating unintended duplicate business effects.

**Q18. What is a Saga?**

Saga is a distributed transaction pattern that coordinates multiple local transactions and uses compensating transactions when necessary.

**Q19. What is the Transactional Outbox pattern?**

It stores a business change and its corresponding event in the same database transaction. A separate relay publishes the event to a broker.

**Q20. What is Event Sourcing?**

Event Sourcing is a data architecture in which application state is derived from a sequence of persisted events.

### **Advanced-level questions**

**Q21. Why is exactly-once processing difficult in distributed systems?**

Because messages and external operations can be retried, duplicated, or interrupted by failures. Exactly-once effects require coordination within a defined processing boundary and appropriate handling of external side effects.

**Q22. What is the dual-write problem?**

It occurs when an application must update a database and publish a message as two separate operations. A failure between them can leave the database and message broker inconsistent.

**Q23. How does the Transactional Outbox solve the dual-write problem?**

It stores the business change and event in one local database transaction. A separate relay later publishes the event.

**Q24. What is the difference between Saga choreography and orchestration?**

Choreography coordinates services through events without a central controller. Orchestration uses a central coordinator to manage the workflow.

**Q25. What happens if a Kafka consumer crashes after processing a message but before committing its offset?**

The message may be delivered again after the consumer restarts or its partition is reassigned. The consumer should be idempotent.

**Q26. How do you handle duplicate events?**

Use unique event IDs, a processed-event store, unique constraints, and atomic database transactions.

**Q27. How do you maintain ordering in Kafka?**

Use an appropriate message key so related events are assigned to the same partition. Kafka preserves order within each partition, not across all partitions.

**Q28. When would you choose RabbitMQ instead of Kafka?**

When the primary requirement is flexible message routing, task distribution, work queues, and traditional acknowledgment-based processing.

**Q29. When would you choose Kafka instead of RabbitMQ?**

When the system needs durable event streams, high-throughput processing, multiple independent consumer groups, and event replay.

**Q30. How would you design a reliable order-processing system?**

I would use:

1. A local database transaction for order creation.
2. A Transactional Outbox for reliable event publication.
3. A message broker for asynchronous communication.
4. Idempotent consumers.
5. A Saga for multi-service coordination.
6. Compensating operations for business failures.
7. Retry and dead-letter handling.
8. Monitoring and distributed tracing.

## **19. Practical assignment – Build an Event-Driven E-commerce Workflow**

Major hands-on assignment

Build an event-driven order processing system using:

- Next.js 16.
- Node.js and TypeScript.
- MongoDB or PostgreSQL.
- RabbitMQ.
- Redis (optional).
- BullMQ for comparison with broker-based messaging.

### **Part A – Distributed systems fundamentals**

- Understand synchronous communication.
- Understand asynchronous communication.
- Identify producers and consumers.
- Understand the role of a message broker.
- Create an architecture diagram.

### **Part B – RabbitMQ**

- Run RabbitMQ locally.
- Create a direct exchange.
- Create an order queue.
- Publish an `OrderCreated` event.
- Consume the event.
- Implement acknowledgment.
- Configure dead-letter handling.
- Test consumer failure and redelivery.

### **Part C – Kafka**

- Run Kafka locally.
- Create an `orders` topic.
- Configure multiple partitions.
- Create a producer.
- Create a consumer.
- Create a consumer group.
- Understand offsets.
- Test event replay.

### **Part D – Reliability**

- Implement event IDs.
- Implement idempotent consumers.
- Understand at-most-once and at-least-once delivery.
- Understand exactly-once processing boundaries.
- Implement retry handling.
- Test duplicate event delivery.

### **Part E – Distributed transactions**

- Create an Order Service.
- Create an Inventory Service.
- Create a Payment Service.
- Implement a Saga workflow.
- Implement compensation when payment fails.
- Implement the Transactional Outbox pattern.

### **Part F – Testing and monitoring**

- Simulate a broker failure.
- Simulate a consumer crash.
- Simulate payment failure.
- Verify that orders do not get duplicated.
- Monitor pending messages.
- Record event processing errors.

### **Expected workflow**

```
Create Order
     |
     v
OrderCreated Event
     |
     v
Reserve Inventory
     |
     v
Process Payment
     |
     +------ Success ------> Confirm Order
     |
     +------ Failure ------> Release Inventory
                                  |
                                  v
                            Cancel Order
```

## **20. Quick revision notes**

| Concept              | Remember                                                  |
| -------------------- | --------------------------------------------------------- |
| Distributed system   | Independent services communicating over a network         |
| Synchronous          | Caller waits for response                                 |
| Asynchronous         | Caller does not wait for full processing                  |
| Event                | Something that has already happened                       |
| Command              | Request to perform an action                              |
| Producer             | Publishes messages                                        |
| Consumer             | Processes messages                                        |
| Broker               | Routes and delivers messages                              |
| Pub/Sub              | One publication can reach multiple subscribers            |
| RabbitMQ             | Message broker                                            |
| Exchange             | Routes RabbitMQ messages                                  |
| Queue                | Stores messages                                           |
| Binding              | Connects exchange and queue                               |
| Kafka                | Event streaming platform                                  |
| Topic                | Named event stream                                        |
| Partition            | Ordered subset of a Kafka topic                           |
| Consumer group       | Consumers sharing topic processing                        |
| Offset               | Record position within a partition                        |
| At-most-once         | Zero or one delivery                                      |
| At-least-once        | One or more deliveries                                    |
| Idempotency          | Repeated processing has no unintended duplicate effect    |
| Eventual consistency | Data converges over time                                  |
| 2PC                  | Prepare and commit protocol                               |
| Saga                 | Multiple local transactions with compensation             |
| Choreography         | Services coordinate through events                        |
| Orchestration        | Central coordinator controls workflow                     |
| Outbox               | Database transactionally stores business change and event |
| Event Sourcing       | State is derived from persisted events                    |

### **Most important distinctions**

**RabbitMQ vs Kafka**

RabbitMQ focuses on message routing and work queues. Kafka focuses on persistent event streams and replay.

**Event-driven vs Event Sourcing**

Event-driven architecture uses events for communication. Event Sourcing uses events as the source of truth for application state.

**Choreography vs Orchestration**

Choreography uses event-driven coordination. Orchestration uses a central workflow coordinator.

**Saga vs Transactional Outbox**

Saga coordinates distributed business transactions. Outbox reliably publishes events associated with local database transactions.

## **21. Official documentation and learning resources**

1. [Apache Kafka – Official Documentation](https://kafka.apache.org/documentation/?utm_source=chatgpt.com) – Topics, partitions, producers, and consumers.
2. [RabbitMQ – Official Tutorials](https://www.rabbitmq.com/tutorials?utm_source=chatgpt.com) – Exchanges, queues, routing, and acknowledgments.
3. [AWS – Saga Pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html?utm_source=chatgpt.com) – Distributed transaction coordination.
4. [AWS – Saga Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html?utm_source=chatgpt.com) – Choreography and orchestration.
5. [Microservices.io – Transactional Outbox](https://microservices.io/patterns/data/transactional-outbox?utm_source=chatgpt.com) – Reliable event publication.
6. [AWS – Transactional Outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html?trk=public_post_comment-text\&utm_source=chatgpt.com) – Implementation considerations.
7. [AWS – Choosing a Coordination Approach](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-integrating-microservices/choosing-approach.html?utm_source=chatgpt.com) – Choreography vs orchestration.

## **22. Phase 25 completion checklist**

**Your learning progress**

# **0%**

0 of 23 completed

Understand distributed systems

Understand synchronous and asynchronous communication

Understand Event-Driven Architecture

Understand commands vs events

Understand producers, consumers and brokers

Understand Pub/Sub

Understand RabbitMQ architecture

Understand RabbitMQ exchanges

Create RabbitMQ producer and consumer

Understand message acknowledgments and dead-letter queues

Understand Kafka topics and partitions

Understand Kafka consumer groups and offsets

Create Kafka producer and consumer

Understand RabbitMQ vs Kafka

Understand at-most-once and at-least-once delivery

Understand idempotent consumers

Understand eventual consistency

Understand distributed transactions and 2PC

Understand Saga pattern

Understand choreography and orchestration

Understand Transactional Outbox

Understand Event Sourcing vs Event-Driven Architecture

Complete the e-commerce workflow assignment

Reset checklist

## **23. Next phase preview**

[Axon Framework で簡単にEventSourcing+CQRSなアプリケーションを作る - エムスリーテックブログ](https://images.openai.com/static-rsc-4/r_RiGoFiXcFwa3cFxmI-L98dlUt1Tmu9aB-WidAaydVV2zzmxlxuc7vHHvTDADVZgmQ2kmvcte6ipY_ri7kWlOy8CaRrQoW-YqhIV9sXsEdwb6tMsLw_hk9nYCGcwG_CRlMLSvgqrUlbVaPLr3R8O3yzte9m9vF8vzd8-VjFY6I?purpose=inline)

**Phase 26 – CQRS, Event Sourcing and Advanced Data Architecture**

Next Phase

Advanced backend architecture and data management

Planned topics:

1. What is CQRS?
2. Command and Query separation.
3. CQRS architecture.
4. Read models and write models.
5. Event Sourcing in depth.
6. Event stores.
7. Snapshots and event replay.
8. CQRS with Event Sourcing.
9. Projections and materialized views.
10. Schema evolution and event versioning.
11. Data synchronization.
12. Real-world CQRS architecture using Node.js and MongoDB.

**When you're ready, say: OK, Phase 26.**