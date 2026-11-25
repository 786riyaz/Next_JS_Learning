# Phase 36 – Advanced Observability, Monitoring, Logging and Production Operations

(Advanced Level) (Production Engineering) (Next.js 16.x · TypeScript · Node.js · DevOps)

## 1\. Learning objectives

In the previous phase, you learned how to scale backend applications and design systems that handle increasing workloads.

Now we will focus on a different but equally important question\:

**How do you know whether your application is working correctly after it has been deployed to production?**

A backend application may be running without crashing, but it can still have serious problems\:

- API responses are becoming slower.
- Database queries are timing out.
- Background jobs are stuck in a queue.
- Payment requests are failing.
- Memory consumption is continuously increasing.
- One microservice is causing failures in another service.
- Users are experiencing errors that developers cannot reproduce locally.

Production observability helps you detect, investigate, and resolve these problems.

By the end of this phase, you should be able to\:

1. Design structured logging for Node.js applications.
2. Implement centralized logging.
3. Understand logs, metrics, and distributed traces.
4. Monitor application and infrastructure performance.
5. Use Prometheus and Grafana.
6. Instrument applications using OpenTelemetry.
7. Implement error tracking and performance monitoring.
8. Configure alerting and incident management.
9. Understand SLIs, SLOs, SLAs, and error budgets.
10. Build a production monitoring strategy for a Next.js and Node.js application.

## 2\. Complete topic roadmap

1. Observability Fundamentals
2. Logging Architecture
3. Structured Logging with Node.js
4. Log Levels and Log Management
5. Centralized Logging
6. Correlation IDs and Request Tracing
7. Metrics and Monitoring
8. Prometheus
9. Grafana
10. Distributed Tracing
11. OpenTelemetry
12. Application Performance Monitoring (APM)
13. Error Tracking
14. Alerting and Notifications
15. Incident Management
16. Production Debugging
17. Health Checks and Service Monitoring
18. SLIs, SLOs and SLAs
19. Error Budgets
20. Production Readiness and Operational Excellence

---

## 3\. Observability fundamentals

### 3.1 What is observability?

**Observability** is the ability to understand the internal state of a system by examining the data it produces, such as logs, metrics, and traces.

Consider a deployed e-commerce application.

A customer reports\:

> My checkout is taking 15 seconds, and sometimes the payment fails.

You need to investigate\:

- Which API is slow?
- Is the Node.js server overloaded?
- Is the database responding slowly?
- Is the payment provider having problems?
- Is the application retrying requests?
- Is the problem affecting all customers or only some?

Observability gives you the information needed to answer these questions.

### 3.2 Monitoring vs observability

These concepts are related but different.

**Monitoring** tells you whether predefined conditions are healthy or unhealthy.

For example\:

```
CPU Usage > 90%
API Error Rate > 5%
Database Connections > 90
```



Monitoring can trigger alerts when these conditions occur.

**Observability** helps you investigate why the problem occurred, including failures you did not anticipate when creating your dashboards and alerts.

| Monitoring | Observability |
|--------|--------|
| Tracks predefined conditions | Helps investigate system behavior |
| Answers whether something is wrong | Helps explain why it is wrong |
| Uses dashboards and alerts | Uses logs, metrics, traces, and context |
| Often threshold-oriented | Supports deeper investigation |
| Example: CPU is 95% | Example: Which requests caused CPU saturation? |

### 3.3 The three pillars of observability

The traditional observability model has three main pillars\:

**Logs**

What happened?

**Metrics**

How much / how often?

**Traces**

Where did time go?

---

Combined observability

Understand, investigate, and diagnose production behavior.

#### A. Logs

Logs are timestamped records of events that happen inside an application.

Example\:

```
2026-11-25 12:00:00 INFO User logged in
2026-11-25 12:00:05 INFO Order created
2026-11-25 12:00:08 ERROR Payment failed
```



Logs help answer\:

- What happened?
- When did it happen?
- Which user or request was involved?
- What error occurred?

#### B. Metrics

Metrics are numerical measurements collected over time.

Examples\:

```
CPU Usage: 75%
Memory Usage: 80%
HTTP Requests: 1,500/sec
Error Rate: 2.5%
Average API Latency: 120 ms
```



Metrics help identify trends, unusual behavior, and resource saturation.

#### C. Traces

A trace represents the journey of a request through an application or distributed system.

Example\:

```
User Request
     |
     ↓
API Gateway          10 ms
     |
     ↓
Order Service        40 ms
     |
     ↓
Payment Service    1,200 ms
     |
     ↓
Database             30 ms
```



The trace reveals that the payment service is responsible for most of the request duration.

### 3.4 Logs vs metrics vs traces

| Feature | Logs | Metrics | Traces |
|--------|--------|--------|--------|
| Data type | Events and records | Numerical measurements | Request execution paths |
| Main purpose | Investigate events | Monitor trends and health | Find latency and dependency problems |
| Typical example | Payment failed | Error rate = 2% | Payment API took 1.2 seconds |
| Storage | Log platform | Time-series database | Trace backend |
| Best use | Detailed debugging | Alerting and dashboards | Distributed request diagnosis |

**Important\:** These three pillars complement each other. They are not replacements for one another.

---

## 4\. Logging architecture

### 4.1 What is logging?

Logging is the process of recording important events, operations, warnings, and errors generated by an application.

For example, when a user places an order, your backend might record\:

```
Order request received
User authenticated
Inventory checked
Payment initiated
Payment successful
Order created
```



These records help developers understand what happened during an operation.

### 4.2 Why is logging important?

Logging helps you\:

- Investigate production errors.
- Track user activities relevant to business operations.
- Diagnose slow API requests.
- Understand application behavior.
- Identify security incidents.
- Troubleshoot database and external API failures.
- Audit important business operations.

Without logging, debugging production issues becomes difficult because you may not be able to reproduce the problem.

### 4.3 Development logging vs production logging

During development, you might use\:

```
console.log("User created");
console.error("Database connection failed");
```



This is acceptable for simple debugging.

However, production applications need a more structured approach.

| Development logging | Production logging |
|--------|--------|
| Often temporary | Designed for ongoing operation |
| Console output | Structured records |
| Local terminal | Centralized log platform |
| Limited context | Request IDs and service metadata |
| Manual inspection | Search, dashboards, alerts |
| Debug-focused | Operational and security-focused |

### 4.4 What should a production log contain?

A useful log record may contain\:

- Timestamp.
- Log level.
- Message.
- Service name.
- Environment.
- Request ID.
- Trace ID.
- HTTP method.
- Route.
- Status code.
- Execution duration.
- Error details, where safe.

Example\:

```
{
  "timestamp": "2026-11-25T12:00:00.000Z",
  "level": "error",
  "service": "order-service",
  "environment": "production",
  "message": "Payment processing failed",
  "requestId": "req-123",
  "orderId": "ORD-501",
  "statusCode": 502,
  "durationMs": 1200
}
```



This is a structured log because the information is stored as separate fields rather than one unstructured sentence.

### 4.5 What should never be logged?

Avoid logging sensitive information such as\:

- Passwords.
- Authentication tokens.
- Session cookies.
- API secrets.
- Database credentials.
- Full credit card details.
- Sensitive personal information.
- Private request bodies without a clear, safe need.

For example, do not log\:

```
console.log("Login request:", req.body);
```



The request body may contain a password.

Instead\:

```
console.info("Login attempt", {
  email: req.body.email,
  // Do not include the password.
});
```



Even email addresses and user identifiers may be sensitive in some environments. Apply data minimization, access controls, retention policies, and redaction.

---

## 5\. Structured logging with Node.js

### 5.1 What is structured logging?

Structured logging means storing log information in a predictable format, usually JSON.

Unstructured log\:

```
User 501 placed order ORD-123 successfully
```



Structured log\:

```
{
  "level": "info",
  "event": "order_created",
  "userId": "501",
  "orderId": "ORD-123"
}
```



The structured version is easier to filter and analyze.

For example, a log platform can search for all records where\:

```
event = "order_created"
```



Or\:

```
level = "error"
```



### 5.2 Why JSON logging?

JSON provides consistent key-value fields.

Benefits\:

1. Easy machine parsing.
2. Easy filtering.
3. Consistent log structure.
4. Better integration with logging platforms.
5. Easier correlation across services.
6. Easier automated alerting.

### 5.3 Popular Node.js logging libraries

| Library | Description |
|--------|--------|
| Pino | Fast structured JSON logger |
| Winston | Flexible logging and transport system |
| Bunyan | Structured JSON logging |
| `console` | Built-in basic logging |

For many Node.js backend applications, Pino is a good starting choice.

### 5.4 Implement Pino

Install\:

```
npm install pino
npm install -D pino-pretty
```



Create `src/lib/logger.ts`\:

```
import pino from "pino";

export const logger = pino({
  level: process.env.LOG_LEVEL ?? "info",

  base: {
    service: "ecommerce-api",
    environment:
      process.env.NODE_ENV ?? "development",
  },

  timestamp: pino.stdTimeFunctions.isoTime,

  redact: {
    paths: [
      "password",
      "req.headers.authorization",
      "req.headers.cookie",
      "token",
      "accessToken",
      "refreshToken",
    ],
    censor: "[REDACTED]",
  },
});
```



Use the logger\:

```
import { logger } from "@/lib/logger";

logger.info("Application started");

logger.warn(
  {
    userId: "501",
  },
  "Unusual login activity"
);

logger.error(
  {
    orderId: "ORD-501",
    errorCode: "PAYMENT_FAILED",
  },
  "Payment processing failed"
);
```



Pino's logging methods accept structured objects and messages.

### 5.5 Development vs production output

In development, readable output is useful\:

```
node app.js | npx pino-pretty
```



In production, prefer structured JSON output\:

```
{
  "level": 30,
  "time": "2026-11-25T12:00:00.000Z",
  "service": "ecommerce-api",
  "msg": "Application started"
}
```



Production log collectors can parse JSON without requiring a human-readable formatter.

### 5.6 Logging in Express

Create a logging middleware\:

```
import { randomUUID } from "node:crypto";
import type {
  Request,
  Response,
  NextFunction,
} from "express";

import { logger } from "./logger";

export function requestLogger(
  req: Request,
  res: Response,
  next: NextFunction
) {
  const requestId = randomUUID();

  const startedAt = Date.now();

  res.setHeader("X-Request-ID", requestId);

  res.on("finish", () => {
    logger.info(
      {
        requestId,
        method: req.method,
        path: req.path,
        statusCode: res.statusCode,
        durationMs: Date.now() - startedAt,
      },
      "HTTP request completed"
    );
  });

  next();
}
```



Register it\:

```
import express from "express";
import { requestLogger } from "./middleware/request-logger";

const app = express();

app.use(requestLogger);
```



This produces a request log when the response finishes.

**Production improvement\:** Use an established request-context approach, such as `AsyncLocalStorage`, or an observability instrumentation library to propagate request and trace context across asynchronous operations. A request ID generated in middleware is not automatically available in every downstream service or worker.

---

## 6\. Log levels and log management

### 6.1 What are log levels?

Log levels indicate the severity or importance of an event.

Common levels\:

| Level | Meaning | Example |
|--------|--------|--------|
| `fatal` | Application cannot continue | Critical startup failure |
| `error` | An operation failed | Payment processing failed |
| `warn` | Something unusual happened | Retry threshold nearly reached |
| `info` | Normal operational event | Order created |
| `debug` | Detailed diagnostic information | Query parameters after safe filtering |
| `trace` | Very detailed execution information | Low-level internal operation |

Different libraries use slightly different naming and numeric values.

### 6.2 Example

```
logger.fatal("Application startup failed");

logger.error("Database operation failed");

logger.warn("Database connection pool near capacity");

logger.info("Order created successfully");

logger.debug("Executing product search");

logger.trace("Entering repository function");
```



### 6.3 Log level configuration

You can configure the log level through an environment variable\:

```
LOG_LEVEL=info
```



In development\:

```
LOG_LEVEL=debug
```



In production\:

```
LOG_LEVEL=info
```



Avoid enabling verbose trace-level logging in production without a specific reason. It can create large volumes of data, increase cost, and expose unnecessary information.

### 6.4 Log retention

Log retention defines how long logs are stored.

Example\:

| Log category | Illustrative retention |
|--------|--------|
| Debug logs | 3–7 days |
| Application logs | 30 days |
| Security audit logs | According to compliance policy |
| Critical operational logs | According to recovery and investigation needs |

These are example durations, not universal requirements. Actual retention should follow legal, security, privacy, and business requirements.

### 6.5 Log rotation

Log rotation prevents local log files from growing indefinitely.

For example\:

```
application.log
application.log.1
application.log.2
application.log.3
```



When one file reaches a configured size or age, the logging system rotates it.

In containerized environments, applications commonly write logs to stdout and stderr, leaving collection, rotation, and storage to the platform or log agent.

---

## 7\. Centralized logging

### 7.1 What is centralized logging?

Centralized logging means collecting logs from multiple application instances and services into one searchable system.

Consider an application with three backend servers\:

```
Server 1 ───┐
Server 2 ───┼──→ Centralized Logging Platform
Server 3 ───┘
```



Instead of logging into each machine separately, developers can search all application logs in one place.

### 7.2 Why do we need centralized logging?

Imagine\:

```
User request
     |
Load Balancer
     |
     ├── Server 1
     ├── Server 2
     └── Server 3
```



A user reports an error.

You do not know which server handled the request.

With centralized logging, you can search using a request ID and find the related records regardless of which server produced them.

### 7.3 Centralized logging architecture

**Log Collector / Agent**

Collect · Parse · Enrich · Forward

**Centralized Log Storage**

**Search · Dashboards · Alerts**

### 7.4 Popular centralized logging solutions

**1\. ELK Stack**

- Elasticsearch: Search and store indexed log data.
- Logstash: Collect and process log data.
- Kibana: Explore and visualize data.

**2\. Grafana Loki**

- Loki: Log aggregation system.
- Grafana: Visualization and log exploration.
- Promtail was historically a common log collector; Grafana Alloy is a current option for collecting and forwarding telemetry.

Loki indexes log metadata labels rather than indexing every word of every log line, which is an important difference from many full-text search systems. [grafana.com](https://grafana.com/docs/grafana/latest/datasources/loki/)[grafana.com](https://grafana.com/docs/loki/latest/visualize/grafana/)

**3\. Other options**

- Datadog
- New Relic
- Splunk
- AWS CloudWatch

### 7.5 Example: Searching production logs

Suppose you have the following log records\:

```
{
  "level": "error",
  "service": "payment-service",
  "requestId": "req-123",
  "message": "Payment provider timeout"
}
```



You can search\:

```
service = payment-service
level = error
```



Or\:

```
requestId = req-123
```



The second query can help you find all events associated with a particular request.

---

## 8\. Correlation IDs and request tracing

### 8.1 What is a correlation ID?

A correlation ID is an identifier used to associate related operations or log records.

Imagine a user places an order\:

```
Request enters API
      |
      ↓
Order Service
      |
      ↓
Payment Service
      |
      ↓
Inventory Service
      |
      ↓
Notification Service
```



Each service produces its own logs.

Without a shared identifier, it may be difficult to determine which logs belong to the same order operation.

A correlation ID helps connect those records.

Example\:

```
requestId = req-123
```



All related logs include the same ID.

### 8.2 Request ID vs Trace ID

These identifiers are related but not identical.

| Request ID | Trace ID |
|--------|--------|
| Identifies a request or business operation according to application design | Identifies a distributed trace |
| Often generated by application middleware | Generated or managed by tracing instrumentation |
| Can be used for log searches | Connects spans across services |
| Application-defined semantics | OpenTelemetry/W3C trace context conventions |

One request ID may be useful for a business operation that produces multiple distributed traces, especially when asynchronous jobs are involved.

### 8.3 Example of request ID propagation

```
Client
  |
  | X-Request-ID: req-123
  ↓
API Gateway
  |
  | X-Request-ID: req-123
  ↓
Order Service
  |
  | X-Request-ID: req-123
  ↓
Payment Service
  |
  | X-Request-ID: req-123
  ↓
Database
```



For application-generated IDs, validate or generate the identifier at the trusted boundary. Do not blindly accept arbitrary client-provided values without length and character restrictions.

### 8.4 Using AsyncLocalStorage

Node.js provides `AsyncLocalStorage`, which allows request context to be maintained across many asynchronous operations within an execution context.

Example\:

```
import { AsyncLocalStorage } from "node:async_hooks";

export const requestContext =
  new AsyncLocalStorage<{
    requestId: string;
  }>();
```



Middleware\:

```
import { randomUUID } from "node:crypto";
import type {
  Request,
  Response,
  NextFunction,
} from "express";

import { requestContext } from "./request-context";

export function contextMiddleware(
  req: Request,
  res: Response,
  next: NextFunction
) {
  const requestId = randomUUID();

  requestContext.run(
    { requestId },
    () => {
      res.setHeader("X-Request-ID", requestId);
      next();
    }
  );
}
```



Read the context\:

```
import { requestContext } from "./request-context";

export function logOrderCreated(orderId: string) {
  const context = requestContext.getStore();

  console.log({
    requestId: context?.requestId,
    orderId,
    message: "Order created",
  });
}
```



This is useful for request-scoped metadata.

**Important\:** `AsyncLocalStorage` does not automatically propagate context across separate processes, message queues, or remote services. You must explicitly propagate context through those boundaries.

### 8.5 Distributed trace context

For distributed tracing, OpenTelemetry commonly uses W3C Trace Context.

An HTTP request can contain\:

```
traceparent: 00-<trace-id>-<parent-span-id>-01
```



This header carries trace context between services.

OpenTelemetry context propagation allows services to create spans belonging to the same distributed trace. [opentelemetry.io](https://opentelemetry.io/docs/concepts/context-propagation/)[opentelemetry.io](https://opentelemetry.io/docs/concepts/signals/traces/)

---

## 9\. Metrics and monitoring

### 9.1 What are application metrics?

Metrics are numerical measurements collected over time to understand the health and performance of an application.

Example\:

```
http_requests_total = 100000
http_errors_total = 500
active_requests = 35
memory_usage_bytes = 524288000
```



Metrics are especially useful for dashboards, alerting, capacity planning, and performance analysis.

### 9.2 Four golden signals

A widely used monitoring model identifies four important signals for request-serving systems.

**Latency**

How long does a request take?

---

**Traffic**

How much demand is the system receiving?

---

**Errors**

How many requests are failing?

---

**Saturation**

How close are resources to their limits?

**Latency**

Measures how long an operation takes.

Examples\:

- API response time.
- Database query duration.
- Payment provider response time.

Monitor p50, p95, and p99, not just the average.

**Traffic**

Measures demand on the system.

Examples\:

- Requests per second.
- Active users.
- WebSocket connections.
- Jobs submitted per second.

**Errors**

Measures failed operations.

Examples\:

- HTTP 5xx responses.
- Failed payment operations.
- Database query errors.
- Background job failures.

**Saturation**

Measures how heavily a resource is utilized relative to its capacity.

Examples\:

- CPU utilization.
- Memory utilization.
- Database connection pool usage.
- Queue depth.
- Disk utilization.

### 9.3 Infrastructure metrics vs application metrics

| Infrastructure metrics | Application metrics |
|--------|--------|
| CPU usage | HTTP request count |
| Memory usage | API latency |
| Disk usage | Login failures |
| Network throughput | Order creation rate |
| Container restarts | Payment failure rate |
| Database connections | Background job duration |

You need both types to understand production behavior.

### 9.4 RED and USE methods

Two useful monitoring approaches\:

**RED method** is commonly applied to request-driven services\:

- Rate: Requests per second.
- Errors: Failed requests.
- Duration: Request latency.

**USE method** is commonly applied to infrastructure resources\:

- Utilization: How busy is the resource?
- Saturation: How much work is waiting?
- Errors: How many resource errors occurred?

For example, for a database\:

```
Utilization: CPU 85%
Saturation: Connection wait queue 20
Errors: Connection failures 12/min
```



### 9.5 Metrics collection example

Suppose your API handles\:

```
Total requests: 10,000
Failed requests: 200
```



Error rate\:

\[
Error\ Rate =
\frac{200}{10,000}\times100
=2\%
\]

This means 2% of requests failed.

However, choose the denominator carefully. For an availability SLI, you might count only eligible user-facing requests and exclude certain expected outcomes such as validation errors, depending on the service's SLO definition.

---

## 10\. Prometheus

### 10.1 What is Prometheus?

Prometheus is an open-source monitoring system that collects and stores time-series metrics.

A time series is a sequence of values associated with timestamps and identifying labels.

Example\:

```
http_requests_total{
  service="order-api",
  method="POST",
  status="201"
}
```



Prometheus is widely used to monitor\:

- Node.js applications.
- Containers.
- Kubernetes clusters.
- Databases.
- Servers.
- Microservices.

### 10.2 How Prometheus works

Prometheus generally uses a pull-based model: it periodically scrapes metrics exposed by configured targets.

**Node.js Application**

/metrics endpoint

Prometheus scrapes metrics

**Prometheus Server**

Scraping · Time-series storage · PromQL

**Grafana / Alerting**

The application exposes a metrics endpoint. Prometheus periodically requests it and stores the collected measurements.

### 10.3 Install Prometheus client for Node.js

Install\:

```
npm install prom-client
```



Create `src/lib/metrics.ts`\:

```
import {
  Registry,
  Counter,
  Histogram,
  collectDefaultMetrics,
} from "prom-client";

export const register = new Registry();

register.setDefaultLabels({
  service: "ecommerce-api",
});

collectDefaultMetrics({
  register,
});

export const httpRequestsTotal = new Counter({
  name: "http_requests_total",
  help: "Total HTTP requests",
  labelNames: ["method", "route", "status"] as const,
  registers: [register],
});

export const httpRequestDuration = new Histogram({
  name: "http_request_duration_seconds",
  help: "HTTP request duration in seconds",
  labelNames: ["method", "route"] as const,
  buckets: [0.05, 0.1, 0.2, 0.5, 1, 2, 5],
  registers: [register],
});
```



This creates\:

- A counter for HTTP requests.
- A histogram for HTTP request duration.
- Default process metrics.

### 10.4 Add metrics middleware in Express

```
import type {
  Request,
  Response,
  NextFunction,
} from "express";

import {
  httpRequestsTotal,
  httpRequestDuration,
} from "./lib/metrics";

export function metricsMiddleware(
  req: Request,
  res: Response,
  next: NextFunction
) {
  const endTimer = httpRequestDuration.startTimer();

  res.on("finish", () => {
    const route = req.route?.path ?? "unmatched";

    httpRequestsTotal.inc({
      method: req.method,
      route,
      status: String(res.statusCode),
    });

    endTimer({
      method: req.method,
      route,
    });
  });

  next();
}
```



Register the middleware after route definitions are considered when you need `req.route` to be available. In practice, route normalization and instrumentation order must be tested carefully. Never use arbitrary URL paths containing IDs as metric labels.

### 10.5 Create the metrics endpoint

```
import express from "express";
import { register } from "./lib/metrics";

const app = express();

app.get("/metrics", async (_req, res) => {
  res.setHeader(
    "Content-Type",
    register.contentType
  );

  res.end(await register.metrics());
});
```



Example metrics output\:

```
http_requests_total{
  method="GET",
  route="/products",
  status="200"
} 120

http_request_duration_seconds_count 120
```



### 10.6 Prometheus configuration

Create `prometheus.yml`\:

```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "ecommerce-api"
    static_configs:
      - targets:
          - "host.docker.internal:4000"
```



This example assumes Prometheus runs in Docker on a host where `host.docker.internal` resolves to the host machine. In a Docker Compose network, use the API service name and its internal port instead.

Prometheus periodically collects metrics from the configured target. The official getting-started guide demonstrates scrape configuration, target health, and metric querying. [prometheus.io](https://prometheus.io/docs/prometheus/latest/getting_started/)

### 10.7 Prometheus metric types

Prometheus has four core metric types\:

**1\. Counter**

A counter increases over time and may reset when the process restarts.

Example\:

```
http_requests_total
```



Use it for\:

- Total requests.
- Total errors.
- Total completed jobs.

**2\. Gauge**

A gauge can increase or decrease.

Example\:

```
active_requests
```



Use it for\:

- Current memory usage.
- Active connections.
- Queue depth.
- Current number of active requests.

**3\. Histogram**

A histogram records observations in configurable buckets and also exposes count and sum information.

Use it for\:

- Request duration.
- Database query duration.
- Response sizes.

**4\. Summary**

A summary records observations and calculates configured quantiles on the instrumented process, alongside count and sum.

Prometheus documents these metric types and their intended use. [prometheus.io](https://prometheus.io/docs/concepts/metric_types/)

### 10.8 PromQL basics

PromQL is Prometheus's query language.

Example\:

Total HTTP requests\:

```
http_requests_total
```



Requests per second over five minutes\:

```
rate(http_requests_total[5m])
```



Total errors\:

```
sum(http_requests_total{status=~"5.." })
```



Average request duration from a histogram\:

```
rate(http_request_duration_seconds_sum[5m])
/
rate(http_request_duration_seconds_count[5m])
```



p95 request duration using histogram buckets\:

```
histogram_quantile(
  0.95,
  sum by (le) (
    rate(http_request_duration_seconds_bucket[5m])
  )
)
```



PromQL supports instant and range queries, aggregation, and functions such as `rate()`\. [prometheus.io](https://prometheus.io/docs/prometheus/latest/querying/basics/)

### 10.9 Metric naming best practices

Prefer\:

```
http_requests_total
http_request_duration_seconds
node_memory_usage_bytes
```



Avoid\:

```
requests
time
memory
```



Also avoid using high-cardinality labels such as\:

```
userId="501"
email="user@example.com"
requestId="req-123"
```



Every unique label combination can create a separate time series. High-cardinality labels can significantly increase memory and storage costs. [prometheus.io](https://prometheus.io/docs/practices/naming/)[prometheus.io](https://prometheus.io/docs/practices/instrumentation/)

---

## 11\. Grafana

### 11.1 What is Grafana?

Grafana is a visualization and analytics platform used to build dashboards from monitoring and observability data sources.

It can display\:

- Prometheus metrics.
- Loki logs.
- Tempo traces.
- Database metrics.
- Cloud monitoring data.
- Other supported data sources.

### 11.2 Prometheus vs Grafana

| Prometheus | Grafana |
|--------|--------|
| Collects and stores metrics | Visualizes data |
| Scrapes metrics endpoints | Queries configured data sources |
| Provides PromQL | Provides dashboards and visualization |
| Can evaluate alerting rules | Provides alerting and notification workflows |
| Monitoring backend | Observability interface and platform |

They are commonly used together.

### 11.3 Grafana dashboard example

Imagine a dashboard for your e-commerce API.

**E-commerce API – Production** (Illustrative dashboard)

Requests / second

# 1,250

Current sample

Error rate

# 0.8%

Current sample

p95 latency

# 280 ms

Current sample

CPU utilization

# 62%

Current sample

Example values only; these are not live measurements.

A production dashboard should include\:

1. Request rate.
2. Error rate.
3. p50, p95, and p99 latency.
4. CPU and memory utilization.
5. Database connection usage.
6. Database query latency.
7. Queue depth and oldest job age.
8. Background job failure rate.
9. External dependency latency.

### 11.4 Dashboard design principles

- Put important user-facing metrics at the top.
- Use consistent units.
- Include meaningful time ranges.
- Avoid overcrowding.
- Add links to relevant logs and traces.
- Show thresholds and historical trends.
- Use the same service and environment labels across panels.

### 11.5 Grafana with Loki

Grafana Loki is a log aggregation system that integrates with Grafana.

A common architecture\:

```
Node.js Services
      |
      ↓
JSON Logs
      |
      ↓
Grafana Alloy / Collector
      |
      ↓
     Loki
      |
      ↓
   Grafana
```



Loki can also be used for log-based alerting when log queries are converted into numeric time series. [grafana.com](https://grafana.com/docs/grafana/latest/datasources/loki/)[grafana.com](https://grafana.com/docs/grafana/latest/datasources/loki/alerting/)

---

## 12\. Distributed tracing

### 12.1 What is distributed tracing?

Distributed tracing tracks a request as it travels through multiple services.

It is especially useful in\:

- Microservices.
- API gateways.
- External service integrations.
- Database-heavy applications.
- Asynchronous workflows.

### 12.2 What is a trace?

A trace represents the complete journey of an operation through a system.

Example\:

```
User Checkout Request
       |
       ↓
API Gateway
       |
       ↓
Order Service
       |
       ├── Inventory Service
       |
       └── Payment Service
                |
                ↓
             Database
```



Each part of the work is represented by a span.

### 12.3 What is a span?

A span represents one unit of work within a trace.

For example\:

```
Trace: Checkout

Span 1: POST /checkout
  |
  ├── Span 2: Validate cart
  |
  ├── Span 3: Reserve inventory
  |
  └── Span 4: Process payment
```



Each span can contain\:

- Span name.
- Trace ID.
- Span ID.
- Parent span ID.
- Start time.
- End time.
- Attributes.
- Events.
- Status.

OpenTelemetry describes traces as a collection of related spans, with parent-child relationships representing the execution hierarchy. [opentelemetry.io](https://opentelemetry.io/docs/concepts/signals/traces/)

### 12.4 Trace ID vs Span ID

**Trace ID\:** Identifies the complete distributed operation.

**Span ID\:** Identifies one individual operation within that trace.

Example\:

```
Trace ID: trace-123

Span 1: API Gateway
Span ID: span-001

Span 2: Order Service
Span ID: span-002
Parent: span-001

Span 3: Payment Service
Span ID: span-003
Parent: span-002
```



All spans belong to the same trace.

### 12.5 Why distributed tracing is important

Suppose your checkout request takes 2.5 seconds.

Without tracing, you might only know\:

```
POST /checkout = 2500 ms
```



With tracing\:

| Operation | Duration |
|--------|--------|
| API Gateway | 20 ms |
| Order Service | 100 ms |
| Inventory Service | 80 ms |
| Payment Service | 2,200 ms |
| Database | 100 ms |

Now you know that the payment service is responsible for most of the delay.

### 12.6 Sampling

Distributed tracing can generate a large amount of telemetry.

Sampling controls how many traces are collected and retained.

Examples\:

- Always sample during local development.
- Sample a percentage of normal production requests.
- Preserve important error traces.
- Use tail-based sampling when you want to make decisions after seeing the trace outcome.

Be careful with sampling policies: if they discard the traces you need for debugging, you lose diagnostic information.

---

## 13\. OpenTelemetry

### 13.1 What is OpenTelemetry?

OpenTelemetry is an open-source, vendor-neutral framework for generating, collecting, and exporting telemetry data.

It supports\:

- Traces.
- Metrics.
- Logs, with language-specific maturity differences.

It is not itself a complete storage and visualization backend.

Think of it as a standard way to instrument applications and send telemetry to supported platforms.

### 13.2 Why use OpenTelemetry?

Without a common instrumentation approach, every monitoring vendor may require a different integration.

OpenTelemetry helps standardize telemetry collection and export.

Benefits\:

1. Vendor-neutral instrumentation.
2. Automatic instrumentation for supported libraries.
3. Manual instrumentation for business operations.
4. Context propagation.
5. Consistent telemetry attributes.
6. Integration with multiple observability backends.

The OpenTelemetry JavaScript documentation currently lists traces and metrics as stable, while its logs implementation is still under development. [opentelemetry.io](https://opentelemetry.io/docs/languages/js/)[opentelemetry.io](https://opentelemetry.io/docs/languages/js/instrumentation/)

### 13.3 OpenTelemetry architecture

**Node.js Application**

Instrumentation · SDK · API

**OpenTelemetry Collector**

Receive · Process · Export

**Metrics**

Prometheus

**Traces**

Tempo / Jaeger

**Logs**

Loki

The Collector is an optional but common component. Applications can also export directly to a supported backend.

### 13.4 Install OpenTelemetry for Node.js

The following is a basic starting point for a Node.js Express application using TypeScript.

Install\:

```
npm install \
  @opentelemetry/api \
  @opentelemetry/sdk-node \
  @opentelemetry/auto-instrumentations-node \
  @opentelemetry/sdk-trace
```



Create `src/instrumentation.ts`\:

```
import { NodeSDK } from "@opentelemetry/sdk-node";

import {
  ConsoleSpanExporter,
} from "@opentelemetry/sdk-trace";

import {
  getNodeAutoInstrumentations,
} from "@opentelemetry/auto-instrumentations-node";

const sdk = new NodeSDK({
  traceExporter: new ConsoleSpanExporter(),

  instrumentations: [
    getNodeAutoInstrumentations(),
  ],
});

sdk.start();
```



Run instrumentation before the application\:

```
npx tsx --import ./src/instrumentation.ts src/server.ts
```



This example exports spans to the console for learning and debugging.

For production, configure an appropriate OTLP exporter or Collector endpoint instead of printing every span to the terminal.

The official OpenTelemetry Node.js guide demonstrates initialization before application code and automatic instrumentation for supported libraries such as Express. [opentelemetry.io](https://opentelemetry.io/docs/languages/js/getting-started/nodejs/)

### 13.5 Automatic vs manual instrumentation

**Automatic instrumentation**

Captures supported operations with little or no application code modification.

Examples\:

- Incoming HTTP requests.
- Outgoing HTTP calls.
- Supported database operations.
- Supported framework activity.

**Manual instrumentation**

Adds spans around important business operations that automatic instrumentation cannot understand.

Example\:

```
import {
  trace,
  SpanStatusCode,
} from "@opentelemetry/api";

const tracer = trace.getTracer("order-service");

export async function createOrder() {
  return tracer.startActiveSpan(
    "create-order",
    async (span) => {
      try {
        // Business logic
        const result = {
          orderId: "ORD-501",
        };

        span.setAttribute(
          "order.id",
          result.orderId
        );

        return result;
      } catch (error) {
        span.recordException(
          error as Error
        );

        span.setStatus({
          code: SpanStatusCode.ERROR,
        });

        throw error;
      } finally {
        span.end();
      }
    }
  );
}
```



This creates a custom span for an important business operation.

### 13.6 OpenTelemetry Collector

The Collector can receive telemetry from applications and process it before exporting it.

Its main component types include\:

- Receivers.
- Processors.
- Exporters.
- Connectors.
- Extensions.

Example conceptual pipeline\:

```
Node.js API
    |
    ↓
OTLP Receiver
    |
    ↓
Batch Processor
    |
    ↓
OTLP Exporter
    |
    ↓
Observability Backend
```



This separates application instrumentation from the destination where telemetry is stored.

---

## 14\. Application Performance Monitoring (APM)

### 14.1 What is APM?

**Application Performance Monitoring (APM)** is the practice of monitoring application performance, request behavior, errors, and dependencies in production.

APM platforms help developers understand\:

- Which endpoints are slow.
- Which database queries are expensive.
- Which external services are failing.
- How much time each request spends in different operations.
- How application performance changes over time.

### 14.2 What does an APM platform monitor?

| Area | Example |
|--------|--------|
| API performance | `/api/orders` takes 800 ms |
| Database performance | Query takes 500 ms |
| External services | Payment provider takes 1.5 seconds |
| Error tracking | 500 errors increased |
| Infrastructure | CPU usage reaches 90% |
| User experience | Page loads become slower |
| Dependencies | Redis connection failures |

### 14.3 Popular APM platforms



**Datadog**

Infrastructure monitoring, APM, logs, traces, and dashboards.

[Official website](https://www.datadoghq.com/)

---



**New Relic**

Application performance, distributed tracing, logs, and errors.

[Official website](https://newrelic.com/)

---



**Dynatrace**

Application, infrastructure, dependency, and service monitoring.

[Official website](https://www.dynatrace.com/)

---



**Sentry**

Exception tracking, stack traces, release health, and performance monitoring.

[Official website](https://sentry.io/)

### 14.4 APM vs OpenTelemetry

| APM platform | OpenTelemetry |
|--------|--------|
| Provides monitoring and analysis features | Provides instrumentation and telemetry standards |
| Often includes storage and dashboards | Does not itself provide a complete backend |
| May offer proprietary agents | Supports vendor-neutral instrumentation |
| Example: Datadog | Example: OpenTelemetry SDK |

You can instrument an application with OpenTelemetry and export the data to an APM platform that supports it.

### 14.5 APM in a Next.js application

For a Next.js 16 application, monitor\:

- Server-side rendering duration.
- Route Handler latency.
- Server Action execution.
- Database operations.
- External API requests.
- Server Component data fetching.
- Client-side navigation and browser errors.

Do not expose server-side telemetry endpoints or sensitive trace attributes to browser clients.

---

## 15\. Error tracking

### 15.1 What is error tracking?

Error tracking is the process of capturing, grouping, analyzing, and monitoring application errors.

Instead of discovering an error only when a customer complains, an error tracking platform can notify developers when an exception occurs.

Example\:

```
Error:
TypeError: Cannot read properties of undefined

File:
src/services/order.service.ts

Line:
45

Environment:
Production

Occurrences:
150

Affected users:
32
```



### 15.2 Why is error tracking important?

It helps answer\:

- What error occurred?
- Where did it occur?
- Which release introduced it?
- How frequently is it occurring?
- How many users are affected?
- Which request or trace is associated with it?

### 15.3 Sentry integration with Next.js

Sentry is a common error tracking solution for Next.js.

Install the SDK using its current setup wizard\:

```
npx @sentry/wizard@latest -i nextjs
```



The wizard creates configuration files appropriate to the project and SDK version.

A simplified server-side configuration concept\:

```
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.SENTRY_DSN,

  environment: process.env.NODE_ENV,

  tracesSampleRate: 0.1,
});
```



The sample rate above is illustrative. Select a rate appropriate to traffic volume, observability requirements, and cost.

### 15.4 Error tracking in Express

A basic Express error handler\:

```
import type {
  Request,
  Response,
  NextFunction,
} from "express";

import * as Sentry from "@sentry/node";

export function errorHandler(
  error: Error,
  req: Request,
  res: Response,
  _next: NextFunction
) {
  Sentry.captureException(error, {
    tags: {
      route: req.path,
      method: req.method,
    },
  });

  res.status(500).json({
    success: false,
    message: "Internal server error",
  });
}
```



For an actual application, initialize Sentry according to the current SDK setup instructions and register its Express integration in the correct middleware order. Do not expose internal stack traces to clients.

### 15.5 Error grouping

Suppose 1,000 requests generate this error\:

```
TypeError: Cannot read property 'email'
```



A tracking platform can group occurrences of the same underlying issue into one error issue.

This makes it easier to identify\:

- First occurrence.
- Most recent occurrence.
- Number of occurrences.
- Affected releases.
- Affected users.
- Relevant stack traces.

### 15.6 Error tracking vs logging

| Error tracking | Logging |
|--------|--------|
| Focuses on exceptions and issues | Records general application events |
| Groups repeated errors | Stores individual log events |
| Provides stack traces | Provides operational context |
| Tracks releases and affected users | Tracks service behavior |
| Example: Sentry | Example: Pino + Loki |

Use both. An error tracker does not replace operational logs.

### 15.7 Production error-handling principles

- Return safe error messages.
- Log detailed information only on the server.
- Attach request and trace identifiers.
- Capture unexpected exceptions.
- Avoid reporting expected validation failures as critical system errors.
- Remove secrets and sensitive data from event payloads.
- Track errors by release and environment.
- Configure alerting for significant increases in errors.

---

## 16\. Alerting and notifications

### 16.1 What is alerting?

Alerting automatically notifies the responsible team when a defined condition indicates a problem that needs attention.

Example\:

```
IF API error rate > 5%
FOR 5 minutes

THEN send an alert
```



Alerts can be sent through\:

- Email.
- Slack.
- Microsoft Teams.
- PagerDuty.
- Opsgenie.
- SMS or phone notifications.

### 16.2 What should you alert on?

Prefer alerts that indicate meaningful user impact or a serious risk to service health.

Examples\:

| Condition | Example alert |
|--------|--------|
| High error rate | 5xx error rate exceeds SLO threshold |
| High latency | p95 API latency exceeds target |
| Service unavailable | Critical health check fails |
| Database problem | Connection failures increase |
| Queue backlog | Oldest job exceeds acceptable age |
| Payment failure | Payment success rate drops |
| Storage issue | Disk capacity approaching limit |

Prometheus recommends alerting on symptoms associated with user impact rather than creating large numbers of alerts for every possible internal condition. [prometheus.io](https://prometheus.io/docs/practices/alerting/)

### 16.3 Example Prometheus alert rule

Create `alerts.yml`\:

```
groups:
  - name: api-alerts

    rules:
      - alert: HighApiErrorRate

        expr: |
          (
            sum(rate(http_requests_total{status=~"5.."}[5m]))
            /
            sum(rate(http_requests_total[5m]))
          ) > 0.05

        for: 5m

        labels:
          severity: critical

        annotations:
          summary: "High API error rate"
          description: "More than 5% of API requests are failing."
```



This is an illustrative rule. In production, handle zero traffic safely and define exactly which requests are included in the numerator and denominator.

### 16.4 Alert severity

A practical severity model\:

| Severity | Meaning | Example |
|--------|--------|--------|
| P0 / Critical | Major service outage or critical business impact | Checkout completely unavailable |
| P1 / High | Significant user impact | Payment failures increasing |
| P2 / Medium | Partial degradation | Admin reporting slow |
| P3 / Low | Minor issue | Non-critical background warning |

Severity definitions should be agreed upon by the organization.

### 16.5 Alert fatigue

Alert fatigue happens when developers receive too many unnecessary notifications.

Examples\:

- CPU briefly reaches 80%.
- A single request times out.
- A non-critical job fails and succeeds on retry.
- A warning appears once and resolves immediately.

When too many alerts are generated, engineers may start ignoring them.

Prevent alert fatigue by\:

1. Alerting on meaningful symptoms.
2. Setting sensible thresholds.
3. Using pending periods.
4. Grouping related alerts.
5. Suppressing duplicate notifications.
6. Defining clear ownership.
7. Including actionable information.

Grafana's Loki alerting documentation also recommends pending periods to avoid firing alerts on short-lived spikes. [grafana.com](https://grafana.com/docs/grafana/latest/datasources/loki/alerting/)

---

## 17\. Incident management

### 17.1 What is an incident?

An incident is an unplanned event that causes, or threatens to cause, disruption to a service or business operation.

Examples\:

- Website unavailable.
- Database outage.
- Payment provider integration broken.
- Sensitive data exposure.
- A deployment introduces widespread errors.
- Background jobs stop processing.

### 17.2 Incident management lifecycle

1. **Detection**
   
   Monitoring, alerts, or users identify the problem.
2. **Triage**
   
   Determine severity, impact, affected systems, and ownership.
3. **Mitigation**
   
   Reduce user impact using rollback, failover, traffic control, or another safe workaround.
4. **Resolution**
   
   Fix the underlying issue and restore normal service.
5. **Verification**
   
   Confirm that service behavior has returned to acceptable levels.
6. **Postmortem**
   
   Analyze causes and define preventive improvements.

### 17.3 Example production incident

Imagine your e-commerce website suddenly cannot process payments.

Incident\:

```
Time: 10:00 AM

Problem:
Payment API error rate increased to 70%.

Impact:
Customers cannot complete checkout.

Detection:
Prometheus alert triggered.

Investigation:
Distributed traces show payment provider timeouts.

Mitigation:
Temporarily disable non-essential payment retries
and route to an approved fallback if available.

Resolution:
Payment provider recovers.

Verification:
Successful payment rate returns to normal.
```



### 17.4 Incident response roles

In larger teams, responsibilities may be separated\:

- Incident Commander: Coordinates the response.
- Technical Lead: Investigates the technical failure.
- Communications Lead: Updates stakeholders and affected users.
- Scribe: Records important events and decisions.

One engineer may hold multiple roles in a smaller team.

### 17.5 Postmortem

A postmortem is a structured review after an incident.

It should include\:

1. Incident summary.
2. Impact.
3. Timeline.
4. Root causes and contributing factors.
5. Detection gaps.
6. Mitigation performed.
7. What went well.
8. What did not go well.
9. Corrective actions.
10. Owners and due dates.

A good postmortem focuses on improving systems and processes rather than blaming individuals.

---

## 18\. Production debugging

### 18.1 What is production debugging?

Production debugging is the process of diagnosing and resolving issues in a deployed application using production telemetry and controlled operational procedures.

It differs from local debugging because\:

- You may not reproduce the issue.
- Real users are affected.
- The dataset is larger.
- The environment is distributed.
- Direct changes can cause further problems.

### 18.2 A systematic debugging workflow

Suppose users report that the order API is slow.

Follow this sequence\:

**Step 1 – Identify the affected endpoint**

```
POST /api/orders
```



**Step 2 – Check metrics**

```
Request rate: Normal
Error rate: 3%
p95 latency: 3 seconds
```



**Step 3 – Inspect traces**

```
Order API: 3000 ms
Database: 2500 ms
Payment: 200 ms
Other operations: 300 ms
```



**Step 4 – Inspect database logs and query plans**

Check\:

- Slow queries.
- Missing or unsuitable indexes.
- Lock waits.
- Connection pool saturation.

**Step 5 – Identify the cause**

For example, a query performs a collection scan on a large orders collection.

**Step 6 – Apply a controlled fix**

- Add an appropriate index.
- Optimize the query.
- Test it.
- Deploy safely.

**Step 7 – Verify the result**

Compare latency before and after deployment.

### 18.3 Production debugging checklist

| Check | Question |
|--------|--------|
| Logs | What error occurred? |
| Metrics | When did the problem begin? |
| Traces | Which operation is slow? |
| Database | Are queries or connections overloaded? |
| Infrastructure | Is CPU, memory, or disk saturated? |
| Dependencies | Are external services healthy? |
| Deployment | Did a recent release introduce the issue? |
| Queue | Are background jobs accumulating? |

### 18.4 Common production debugging tools

- Chrome DevTools.
- Node.js Inspector.
- `node --inspect`\.
- Linux `top` and `htop`\.
- `free` and `df`\.
- `journalctl`\.
- Docker logs.
- PM2 logs.
- NGINX access and error logs.
- Prometheus and Grafana.
- Sentry.
- OpenTelemetry trace viewers.

**Important\:** Avoid attaching a debugger or executing risky diagnostic commands on a live production instance without understanding their performance and security implications.

---

## 19\. Health checks and service monitoring

### 19.1 What is a health check?

A health check is an endpoint or mechanism that reports whether an application or component is functioning sufficiently for a particular operational purpose.

Example\:

```
GET /health
```



Response\:

```
{
  "status": "ok"
}
```



Health checks are used by\:

- Load balancers.
- Container platforms.
- Orchestrators.
- Monitoring systems.
- Deployment platforms.

### 19.2 Types of health checks

**1\. Liveness check**

Answers\:

> Is the application process alive and functioning?

If liveness fails, an orchestrator may restart the process.

**2\. Readiness check**

Answers\:

> Is this instance ready to receive traffic?

An application might be alive but not ready because it is still initializing.

**3\. Startup check**

Answers\:

> Has the application completed its startup process?

Useful for applications with slow initialization.

### 19.3 Express health-check implementation

```
import express from "express";

const app = express();

let ready = false;

app.get("/health/live", (_req, res) => {
  res.status(200).json({
    status: "alive",
  });
});

app.get("/health/ready", async (_req, res) => {
  if (!ready) {
    return res.status(503).json({
      status: "not-ready",
    });
  }

  return res.status(200).json({
    status: "ready",
  });
});

async function start() {
  // Initialize required application resources here.

  ready = true;

  app.listen(4000);
}

start();
```



This is a simplified illustration. A real readiness check should reflect whether the instance can safely serve the relevant workload, and should use bounded dependency checks where appropriate.

### 19.4 Health check vs monitoring

| Health check | Monitoring |
|--------|--------|
| Reports current health state | Tracks behavior over time |
| Often used for routing and orchestration | Used for dashboards and alerts |
| Usually simple response | Can include many measurements |
| Example: readiness returns 200 | Example: p95 latency is 700 ms |

A service returning HTTP 200 from `/health` does not prove that every business operation is working correctly.

---

## 20\. SLIs, SLOs and SLAs

These are fundamental concepts in Site Reliability Engineering (SRE).

### 20.1 What is an SLI?

**SLI – Service Level Indicator**

An SLI is a quantitative measurement of a service's reliability or performance.

Examples\:

- Availability.
- Request success rate.
- Request latency.
- Data freshness.
- Background job completion time.

Example\:

```
Successful eligible requests
--------------------------------
Total eligible requests
```



If 99,500 out of 100,000 eligible requests succeed\:

\[
SLI = \frac{99,500}{100,000}\times100
\]

\[
SLI = 99.5\%
\]

### 20.2 What is an SLO?

**SLO – Service Level Objective**

An SLO is a target value for an SLI over a defined measurement window.

Example\:

```
Service availability SLO:
99.9% over a rolling 30-day window
```



This means the service aims to achieve at least 99.9% availability over the specified period, according to its SLI definition.

### 20.3 What is an SLA?

**SLA – Service Level Agreement**

An SLA is a formal agreement between a service provider and a customer that defines expected service levels and may specify remedies or service credits when commitments are not met.

Example\:

```
SLA:
99.9% monthly availability

If availability falls below the contractual target,
the customer may qualify for service credits.
```



### 20.4 SLI vs SLO vs SLA

| Concept | Meaning | Example |
|--------|--------|--------|
| SLI | Actual measurement | 99.95% availability |
| SLO | Internal or agreed target | 99.9% availability |
| SLA | Customer-facing agreement | 99.9% availability with service credits |

Remember\:

- SLI = What are we measuring?
- SLO = What target do we want?
- SLA = What have we formally promised?

### 20.5 Latency SLO example

Suppose your application defines\:

```
99% of eligible product API requests
must complete within 500 ms
over a rolling 30-day period.
```



The SLI measures the proportion of eligible requests that satisfy the latency objective.

The SLO defines the target and time window.

The definition must specify which requests are eligible, how timeouts are counted, and which endpoints are included.

### 20.6 Why are SLOs important?

SLOs help engineering teams\:

- Define reliability targets.
- Prioritize reliability work.
- Measure user experience.
- Evaluate deployment risk.
- Make informed trade-offs between feature development and operational stability.

---

## 21\. Error budgets

### 21.1 What is an error budget?

An error budget is the amount of unreliability a service can experience while still meeting its SLO.

Formula\:

\[
Error\ Budget = 1 - SLO
\]

Suppose\:

```
Availability SLO = 99.9%
```



Then\:

\[
Error\ Budget = 100\%-99.9\%=0.1\%
\]

The service can experience up to 0.1% eligible unavailability during the measurement window before missing this SLO.

### 21.2 Error budget calculation

Suppose\:

```
Total eligible requests: 1,000,000
Availability SLO: 99.9%
```



Allowed failed requests\:

\[
1,000,000 \times 0.001 = 1,000
\]

The error budget is 1,000 failed eligible requests under this simplified request-based availability model.

### 21.3 What happens when the error budget is exhausted?

An organization may decide to\:

- Pause risky feature releases.
- Prioritize reliability improvements.
- Fix recurring production issues.
- Improve deployment safety.
- Reduce dependency failure rates.
- Add stronger monitoring and recovery mechanisms.

The exact policy depends on the organization.

Google's SRE guidance describes error budgets as a mechanism for balancing feature velocity and service reliability, including policies for responding when the budget is exhausted. [sre.google](https://sre.google/workbook/error-budget-policy/)

### 21.4 Error budget example

Imagine your service has a 99.9% availability SLO.

**Illustrative error budget consumption**

Used **70%**

70% consumed 30% remaining

Illustrative policy dashboard, not live service data.

The team can use this information when deciding how much release risk is appropriate.

---

## 22\. Production readiness and operational excellence

### 22.1 What is production readiness?

Production readiness means an application is prepared to run reliably, securely, and maintainably in a real production environment.

A production-ready application should not only work when everything is healthy. It should also behave predictably when dependencies fail, traffic increases, or deployments go wrong.

### 22.2 Production readiness checklist

**Application**

- [ ] Structured logging.
- [ ] Centralized error handling.
- [ ] Request IDs and trace context.
- [ ] Input validation.
- [ ] Safe error responses.
- [ ] Request timeouts.
- [ ] Graceful shutdown.
- [ ] Appropriate retry policies.

**Monitoring**

- [ ] Application metrics.
- [ ] Infrastructure metrics.
- [ ] Logs collected centrally.
- [ ] Distributed tracing.
- [ ] Error tracking.
- [ ] Production dashboards.
- [ ] Alerting rules.

**Reliability**

- [ ] Health checks.
- [ ] Readiness checks.
- [ ] Database backups.
- [ ] Disaster recovery plan.
- [ ] Circuit breakers where appropriate.
- [ ] Queue failure handling.
- [ ] Deployment rollback strategy.

**Security**

- [ ] Secrets stored securely.
- [ ] Sensitive data redacted from logs.
- [ ] Access controls for monitoring platforms.
- [ ] Protected metrics endpoints.
- [ ] Log retention policy.
- [ ] Audit logging for critical operations.

**Operations**

- [ ] Incident response procedure.
- [ ] On-call ownership.
- [ ] Deployment procedure.
- [ ] Postmortem process.
- [ ] SLO definitions.
- [ ] Error budget policy.

### 22.3 Graceful shutdown in Node.js

When a process receives a termination signal, it should stop accepting new work and allow existing work to finish within a defined deadline.

Example\:

```
import http from "node:http";
import express from "express";

const app = express();

const server = http.createServer(app);

server.listen(4000);

async function shutdown(signal: string) {
  console.log(`${signal} received`);

  server.close(async (error) => {
    if (error) {
      console.error("Server shutdown error", error);
      process.exitCode = 1;
      return;
    }

    // Close database pools, Redis connections,
    // workers, and other application resources here.

    console.log("Application shut down");
    process.exitCode = 0;
  });
}

process.on("SIGTERM", () => {
  void shutdown("SIGTERM");
});

process.on("SIGINT", () => {
  void shutdown("SIGINT");
});
```



For production, add a shutdown deadline, stop queue consumers, handle in-flight jobs, close database and Redis clients, and ensure repeated signals cannot start conflicting shutdown sequences.

### 22.4 Deployment monitoring

After a new release, monitor\:

- Error rate.
- API latency.
- CPU and memory.
- Database performance.
- Queue processing.
- Payment success rate.
- User-facing failures.

If a new deployment introduces a serious regression, rollback may be safer than attempting an immediate live repair.

### 22.5 Observability architecture for your full-stack application

A practical architecture for a Next.js frontend and Node.js backend\:

**Next.js 16 + Express API + Workers**

Application instrumentation and structured logging

**Logs**

Pino

**Metrics**

Prometheus

**Traces**

OpenTelemetry

**Observability Backends**

Loki · Prometheus · Tempo / Jaeger · Sentry

**Grafana Dashboards + Alerting**

Production health · Incidents · SLO tracking

This is one possible stack, not a requirement to use every tool. OpenTelemetry can send telemetry to different supported backends, and Sentry or a commercial APM platform may cover overlapping functionality.

---

## 23\. Common mistakes in production observability

1\. **Using only `console.log()`\:** Basic console output is insufficient for a distributed production system.

2\. **Not using structured logging\:** Unstructured messages are harder to search and analyze.

3\. **Logging passwords and tokens\:** This creates serious security risks.

4\. **Not using request IDs\:** Related logs become difficult to correlate.

5\. **Confusing request IDs with trace IDs\:** They serve related but different purposes.

6\. **Monitoring only CPU and RAM\:** Application latency, errors, queue depth, and database performance are equally important.

7\. **Monitoring only average latency\:** p95 and p99 can reveal slow requests hidden by averages.

8\. **Creating excessive metric labels\:** High-cardinality labels can make monitoring expensive and inefficient.

9\. **Treating Prometheus and Grafana as the same product\:** Prometheus collects and stores metrics; Grafana visualizes data.

10\. **Using OpenTelemetry as a storage backend\:** OpenTelemetry provides instrumentation and telemetry pipelines, not a complete observability database.

11\. **Creating too many alerts\:** Excessive notifications lead to alert fatigue.

12\. **Ignoring alert ownership\:** Every important alert should have a clear response owner.

13\. **Not testing alerts\:** An alert that never gets tested may fail when an actual incident occurs.

14\. **Not configuring health checks correctly\:** Liveness and readiness checks serve different purposes.

15\. **Not monitoring background workers\:** An API may be healthy while its job queue is accumulating thousands of pending jobs.

16\. **Not implementing graceful shutdown\:** Deployments may interrupt active requests or jobs.

17\. **Keeping logs indefinitely\:** This can increase storage cost and create privacy or compliance problems.

18\. **Ignoring error budgets\:** Teams may continue risky releases despite deteriorating service reliability.

19\. **Not recording deployment versions\:** It becomes difficult to connect new failures with a specific release.

20\. **Not performing postmortems\:** Repeated incidents can occur because the underlying causes are never addressed.

---

## 24\. Interview questions and answers

### Basic level

**1\. What is observability?**

Observability is the ability to understand a system's internal behavior through the telemetry it generates, especially logs, metrics, and traces.

**2\. What are the three pillars of observability?**

- Logs.
- Metrics.
- Traces.

**3\. What is structured logging?**

Structured logging stores log information in a consistent machine-readable format, usually JSON.

**4\. What is centralized logging?**

Centralized logging collects logs from multiple application instances and services into one searchable platform.

**5\. What is a correlation ID?**

A correlation ID is an identifier used to associate related logs or operations.

**6\. What is Prometheus?**

Prometheus is an open-source monitoring system that collects and stores time-series metrics.

**7\. What is Grafana?**

Grafana is a visualization and analytics platform used to build dashboards from monitoring data sources.

**8\. What is APM?**

Application Performance Monitoring monitors application performance, errors, dependencies, and request execution.

**9\. What is error tracking?**

Error tracking captures, groups, and analyzes application exceptions to help developers diagnose problems.

**10\. What is a health check?**

A health check reports whether an application or component is functioning sufficiently for a particular operational purpose.

### Intermediate level

**11\. What is the difference between logs and metrics?**

Logs represent individual events, while metrics represent numerical measurements collected over time.

**12\. What is the difference between monitoring and observability?**

Monitoring detects predefined problems. Observability helps investigate system behavior and understand why problems occur.

**13\. What is the difference between a counter and a gauge in Prometheus?**

A counter increases monotonically except for resets, while a gauge can increase or decrease.

**14\. What is a histogram in Prometheus?**

A histogram records observations in buckets and exposes count and sum information, making it useful for analyzing request duration and other distributions.

**15\. What is distributed tracing?**

Distributed tracing follows a request across multiple services using traces and spans.

**16\. What is a span?**

A span represents one unit of work within a trace, such as an HTTP request or database operation.

**17\. What is OpenTelemetry?**

OpenTelemetry is a vendor-neutral framework for instrumenting applications and generating, collecting, and exporting telemetry.

**18\. What is the difference between automatic and manual instrumentation?**

Automatic instrumentation captures supported operations through instrumentation libraries. Manual instrumentation adds telemetry around application-specific operations.

**19\. What is the difference between liveness and readiness checks?**

Liveness checks whether the process is alive. Readiness checks whether it should receive traffic.

**20\. What is alert fatigue?**

Alert fatigue occurs when engineers receive so many unnecessary alerts that they begin ignoring notifications.

### Advanced level

**21\. What is the difference between SLI, SLO, and SLA?**

- SLI: Actual service-level measurement.
- SLO: Target for that measurement.
- SLA: Formal agreement with a customer about service levels and possible remedies.

**22\. What is an error budget?**

An error budget is the permitted amount of unreliability within an SLO measurement window.

**23\. What is context propagation?**

Context propagation transfers trace and other contextual information across service boundaries so that related operations can be correlated.

**24\. What is the difference between a trace ID and a span ID?**

A trace ID identifies an entire distributed operation. A span ID identifies one individual operation within that trace.

**25\. Why should metrics avoid high-cardinality labels?**

Each unique label combination creates another time series, potentially increasing memory usage, storage requirements, and query costs.

**26\. Why should logs not contain sensitive information?**

Logs may be accessible to multiple operators and stored in external platforms. Exposing credentials or personal information creates security and privacy risks.

**27\. What is an incident postmortem?**

A postmortem is a structured review of an incident that identifies causes, impact, detection gaps, and corrective actions.

**28\. What is the difference between average latency and p95 latency?**

Average latency is the arithmetic mean. p95 latency is the value below which 95% of observations fall.

**29\. Why is graceful shutdown important?**

It allows applications to stop accepting new work, finish or safely terminate in-flight operations, and close resources before exiting.

**30\. How would you debug a slow microservice architecture?**

I would examine service-level metrics, inspect distributed traces, correlate logs using trace or request IDs, identify slow dependencies, investigate database queries, and validate the fix through controlled testing.

---

## 25\. Practical assignment

(Recommended project) **Build a production observability system for your Node.js backend**

Use\:

- Next.js 16.x.
- Node.js and Express.
- TypeScript.
- Pino.
- Prometheus.
- Grafana.
- OpenTelemetry.
- Loki.
- Sentry.

### Part 1 – Structured logging

- [ ] Install Pino.
- [ ] Configure JSON logging.
- [ ] Configure log levels.
- [ ] Add service and environment metadata.
- [ ] Redact sensitive fields.
- [ ] Implement HTTP request logging.

### Part 2 – Request context

- [ ] Generate request IDs.
- [ ] Return request IDs in HTTP response headers.
- [ ] Propagate request IDs to internal services.
- [ ] Use `AsyncLocalStorage` for request context.
- [ ] Include context in logs.

### Part 3 – Metrics

- [ ] Install `prom-client`\.
- [ ] Create a `/metrics` endpoint.
- [ ] Track total HTTP requests.
- [ ] Track API response duration.
- [ ] Track error rates.
- [ ] Track active requests.
- [ ] Configure Prometheus scraping.

### Part 4 – Dashboards

- [ ] Install Grafana.
- [ ] Connect Prometheus as a data source.
- [ ] Create an API performance dashboard.
- [ ] Display request rate.
- [ ] Display error rate.
- [ ] Display p95 latency.
- [ ] Display CPU and memory utilization.

### Part 5 – Distributed tracing

- [ ] Install OpenTelemetry.
- [ ] Configure Node.js instrumentation.
- [ ] Add custom spans to important business operations.
- [ ] Configure context propagation.
- [ ] Export traces to an appropriate backend.
- [ ] Investigate a simulated slow API request.

### Part 6 – Error tracking

- [ ] Configure Sentry.
- [ ] Capture unexpected exceptions.
- [ ] Track errors by environment.
- [ ] Include relevant request context.
- [ ] Verify sensitive information is not captured.

### Part 7 – Alerting

- [ ] Create a high API error-rate alert.
- [ ] Create a high latency alert.
- [ ] Create a service availability alert.
- [ ] Configure notifications.
- [ ] Test the alerting process.

### Part 8 – Production operations

- [ ] Implement liveness and readiness endpoints.
- [ ] Implement graceful shutdown.
- [ ] Configure log retention.
- [ ] Document incident response procedures.
- [ ] Define one availability SLI and SLO.
- [ ] Calculate its error budget.

---

## 26\. Quick revision notes

| Topic | Remember |
|--------|--------|
| Observability | Understand internal system behavior |
| Monitoring | Detect predefined conditions |
| Logs | Individual event records |
| Metrics | Numerical measurements over time |
| Traces | End-to-end request execution path |
| Structured logging | Machine-readable log records |
| Pino | Node.js structured logger |
| Centralized logging | Logs from multiple services in one platform |
| Correlation ID | Connects related operations |
| Trace ID | Identifies a distributed trace |
| Span ID | Identifies an individual span |
| Prometheus | Metrics collection and storage |
| PromQL | Prometheus query language |
| Counter | Increasing cumulative measurement |
| Gauge | Value that can increase or decrease |
| Histogram | Observation distribution using buckets |
| Grafana | Dashboards and visualization |
| Loki | Log aggregation system |
| OpenTelemetry | Vendor-neutral instrumentation framework |
| APM | Application performance monitoring |
| Sentry | Error tracking and performance monitoring |
| Alerting | Notifications for important conditions |
| Incident | Unplanned service disruption |
| Postmortem | Review and improvement after an incident |
| Liveness | Is the process alive? |
| Readiness | Can it receive traffic? |
| SLI | Service-level measurement |
| SLO | Service-level target |
| SLA | Formal service-level agreement |
| Error budget | Permitted unreliability within an SLO |

## 27\. Official documentation and further reading

1. [OpenTelemetry – Node.js Getting Started](https://opentelemetry.io/docs/languages/js/getting-started/nodejs/?utm_source=chatgpt.com) — Node.js instrumentation.
2. [OpenTelemetry – Traces](https://opentelemetry.io/docs/concepts/signals/traces/?utm_source=chatgpt.com) — spans, trace IDs, and distributed tracing.
3. [Prometheus – Getting Started](https://prometheus.io/docs/prometheus/latest/getting_started/?utm_source=chatgpt.com) — scraping and monitoring configuration.
4. [Prometheus – PromQL Basics](https://prometheus.io/docs/prometheus/latest/querying/basics/?utm_source=chatgpt.com) — querying metrics.
5. [Prometheus – Metric Naming](https://prometheus.io/docs/practices/naming/?utm_source=chatgpt.com) — naming and cardinality best practices.
6. [Grafana – Loki](https://grafana.com/docs/grafana/latest/datasources/loki/?utm_source=chatgpt.com) — centralized logging.
7. [Prometheus – Alerting Practices](https://prometheus.io/docs/practices/alerting/?utm_source=chatgpt.com) — alert design.
8. [Google SRE – Error Budget Policy](https://sre.google/workbook/error-budget-policy/?utm_source=chatgpt.com) — error budgets and reliability decisions.

---

## 28\. Phase completion checklist

**Your learning progress**

# %

of  topics completed

[Reset checklist]

---

## Next phase: Phase 37 – Advanced DevOps, Docker, Kubernetes and Container Orchestration

The next phase will cover\:

1. DevOps Fundamentals and Culture
2. Docker Architecture
3. Docker Images and Containers
4. Dockerfile and Multi-Stage Builds
5. Docker Compose
6. Docker Networking and Volumes
7. Container Security and Optimization
8. Kubernetes Architecture
9. Pods, Deployments and ReplicaSets
10. Services and Ingress
11. ConfigMaps and Secrets
12. Persistent Volumes and Storage
13. Horizontal Pod Autoscaling
14. Kubernetes Health Probes
15. Helm Charts
16. Container Orchestration
17. CI/CD with Docker and Kubernetes
18. Production Deployment Architecture

**Phase 36 complete.** When you're ready, say **“OK, Phase 37”** to continue.