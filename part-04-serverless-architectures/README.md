# Part 4 — Serverless Architectures

**Chapters 25–34 · Status: ✅ Complete**

← [Back to Main README](../Readme.md) · [← Part 3](../part-03-network-architectures/README.md) · [Part 5 →](../part-05-container-and-kubernetes-architectures/README.md)

---

## What This Part Covers

Serverless is not a cost strategy. It is an architectural model — one that trades infrastructure management for event-driven, stateless, pay-per-invocation compute. When the workload fits, it is genuinely transformative: no capacity planning, no patching, no idle cost. When it doesn't fit, it introduces cold start latency, execution time ceilings, and per-invocation costs that exceed a fixed fleet at sustained throughput.

This part covers the full serverless stack on AWS — REST and real-time APIs, event-driven systems, workflow orchestration, webhook processing, scheduled jobs, ETL pipelines, and multi-account patterns. Every chapter is honest about when serverless is the right choice and when it isn't, with cost break-even analysis, cold start mitigation strategies, and the operational discipline that makes serverless systems as observable and reliable as their EC2-based counterparts.

---

## Which Chapter Do You Need?

| If your situation is... | Read this chapter |
|---|---|
| Building a REST API without managing servers | [Ch. 25 — REST APIs](./chapter-25-rest-apis.md) |
| Building a system that reacts to events from multiple sources | [Ch. 26 — Event-Driven Systems](./chapter-26-event-driven-systems.md) |
| Decomposing a monolith into independently deployable Lambda functions | [Ch. 27 — Lambda Microservices](./chapter-27-lambda-microservices.md) |
| Orchestrating multi-step workflows with branching and error handling | [Ch. 28 — Step Functions](./chapter-28-step-functions.md) |
| Processing inbound webhooks from third-party services reliably | [Ch. 29 — Webhook Processing](./chapter-29-webhook-processing.md) |
| Building WebSocket or server-sent event APIs | [Ch. 30 — Real-Time APIs](./chapter-30-real-time-apis.md) |
| Running recurring jobs without managing cron infrastructure | [Ch. 31 — Scheduled Processing](./chapter-31-scheduled-processing.md) |
| Building data transformation pipelines without managing ETL servers | [Ch. 32 — Serverless ETL](./chapter-32-serverless-etl.md) |
| Routing events between AWS services and SaaS applications | [Ch. 33 — EventBridge Integration](./chapter-33-eventbridge-integration.md) |
| Running serverless workloads across multiple AWS accounts | [Ch. 34 — Multi-Account Serverless](./chapter-34-multi-account-serverless.md) |

---

## Chapters at a Glance

### Chapter 25 — REST APIs

**[Read →](./chapter-25-rest-apis.md)**

API Gateway + Lambda as the standard serverless REST API pattern — request routing, authentication, throttling, and Lambda integration, with the honest cost and cold start trade-offs that determine whether this pattern fits your workload's traffic profile.

**Key topics:** API Gateway REST vs. HTTP API, Lambda proxy integration, authorizers (JWT, Lambda, IAM), throttling and usage plans, request/response mapping, Lambda cold start mitigation with Provisioned Concurrency, API Gateway caching, custom domain names with ACM, deployment stages and canary releases, cost break-even vs. EC2/Fargate

---

### Chapter 26 — Event-Driven Systems

**[Read →](./chapter-26-event-driven-systems.md)**

The architecture for systems that react to things that happen — orders placed, files uploaded, records changed — rather than systems that wait for requests. SNS, SQS, EventBridge, and Lambda wired together into a reliable, observable, loosely coupled event processing platform.

**Key topics:** Event producer/consumer decoupling, SNS fan-out to SQS queues, EventBridge content-based routing, Lambda event source mappings, idempotent consumer design, DLQ strategy for failed events, event schema registry, ordering guarantees (and their absence), at-least-once vs. exactly-once delivery, observability for async systems

---

### Chapter 27 — Lambda Microservices

**[Read →](./chapter-27-lambda-microservices.md)**

Lambda functions as independently deployable microservices — each owning a bounded domain, with its own IAM role, its own deployment pipeline, and its own data store — without the container orchestration overhead of ECS or EKS.

**Key topics:** Function boundaries and domain ownership, per-function IAM least privilege, shared layer strategy for common dependencies, function-to-function communication patterns (sync vs. async), Lambda destinations, versioning and aliases for traffic shifting, monorepo vs. polyrepo deployment, Lambda Power Tuning for right-sizing, cold start impact at microservice scale

---

### Chapter 28 — Step Functions Orchestration

**[Read →](./chapter-28-step-functions.md)**

AWS Step Functions for orchestrating multi-step workflows where each step is a Lambda function, an AWS SDK call, or a human approval task — with built-in retry logic, error handling, branching, and a visual execution history that makes debugging async workflows tractable.

**Key topics:** Standard vs. Express workflows, state machine definition (ASL), task states, choice states, parallel and map states, error handling and retry configuration, activity tasks for human approval, SDK integrations (direct AWS API calls without Lambda), execution history and observability, Step Functions vs. SQS/Lambda choreography decision framework

---

### Chapter 29 — Webhook Processing

**[Read →](./chapter-29-webhook-processing.md)**

The reliable pattern for receiving, validating, queuing, and processing inbound webhooks from third-party services — where the sender expects a fast HTTP 200 acknowledgment and the actual processing happens asynchronously, with retry handling for failures that occur after acknowledgment.

**Key topics:** API Gateway for fast acknowledgment, SQS for durable buffering, signature verification at the API layer, idempotency key handling for duplicate deliveries, DLQ for permanently failed events, webhook replay strategy, rate limiting for high-volume senders, monitoring webhook processing lag, handling third-party retry storms

---

### Chapter 30 — Real-Time APIs

**[Read →](./chapter-30-real-time-apis.md)**

WebSocket APIs with API Gateway for bidirectional real-time communication — chat, live dashboards, collaborative editing, push notifications — without managing persistent connection infrastructure.

**Key topics:** API Gateway WebSocket API, connect/disconnect/message route handlers, connection management with DynamoDB, broadcasting to multiple connections, connection lifecycle and timeout handling, scaling considerations for high connection counts, Lambda cold starts on WebSocket routes, AppSync as an alternative for GraphQL subscriptions, cost model for persistent connections

---

### Chapter 31 — Scheduled Processing

**[Read →](./chapter-31-scheduled-processing.md)**

EventBridge Scheduler and Lambda for running recurring jobs — nightly reports, hourly data syncs, daily cleanup tasks — without managing cron infrastructure, with reliable execution guarantees and built-in retry handling.

**Key topics:** EventBridge Scheduler vs. CloudWatch Events cron rules, flexible time windows for non-exact scheduling, Lambda as the execution target, Step Functions for multi-step scheduled workflows, handling overlapping executions, idempotent job design, monitoring for missed or failed executions, cost model vs. always-on EC2 cron

---

### Chapter 32 — Serverless ETL

**[Read →](./chapter-32-serverless-etl.md)**

Lambda and AWS Glue for extract-transform-load pipelines that process data from S3, databases, and streaming sources — without managing ETL servers, with pay-per-execution pricing and automatic scaling for variable data volumes.

**Key topics:** S3-triggered Lambda for lightweight transformations, AWS Glue for complex ETL with PySpark, Glue Data Catalog for schema management, Glue crawlers for automatic schema discovery, Lambda vs. Glue decision framework (data volume, transformation complexity), error handling and partial failure recovery, data quality validation, cost model

---

### Chapter 33 — EventBridge Integration

**[Read →](./chapter-33-eventbridge-integration.md)**

EventBridge as the integration backbone for routing events between AWS services, custom applications, and SaaS partners — with content-based filtering, schema registry, event replay, and archive capabilities that make it the most capable event routing layer in the AWS ecosystem.

**Key topics:** Default vs. custom event buses, event patterns and content-based filtering, schema registry and schema discovery, event archive and replay, pipes for point-to-point integrations, API destinations for HTTP targets, SaaS partner integrations, cross-account event routing, EventBridge vs. SNS decision framework, dead-letter queues for failed deliveries

---

### Chapter 34 — Multi-Account Serverless

**[Read →](./chapter-34-multi-account-serverless.md)**

The patterns for running serverless workloads across multiple AWS accounts — cross-account Lambda invocation, cross-account EventBridge event routing, shared API Gateway with per-account backends, and the IAM patterns that make cross-account serverless secure and auditable.

**Key topics:** Cross-account Lambda invocation via resource-based policies, cross-account EventBridge event buses, cross-account SQS queue access, shared API Gateway with Lambda backends in different accounts, cross-account Secrets Manager access, centralized observability for distributed serverless, deployment pipeline patterns for multi-account serverless

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| AWS Lambda | 25, 26, 27, 28, 29, 30, 31, 32, 33, 34 |
| Amazon API Gateway | 25, 29, 30, 34 |
| Amazon EventBridge | 26, 31, 33, 34 |
| Amazon SQS | 26, 29, 34 |
| Amazon SNS | 26 |
| AWS Step Functions | 28, 31 |
| Amazon DynamoDB | 27, 30 |
| AWS Glue | 32 |
| Amazon S3 | 32 |
| AWS AppSync | 30 |
| EventBridge Scheduler | 31 |
| EventBridge Pipes | 33 |

---

## Serverless vs. EC2/Fargate: When to Choose

| Signal | Lean Serverless | Lean EC2/Fargate |
|--------|----------------|-----------------|
| Traffic pattern | Spiky or idle-heavy | Sustained, predictable |
| Execution duration | Under 15 minutes | Long-running processes |
| Cold start tolerance | Acceptable | Latency-sensitive |
| Cost model | Pay-per-invocation preferred | Fixed baseline cheaper at scale |
| Operational preference | Minimize infrastructure management | Need full runtime control |

Every chapter in this part includes a cost break-even analysis and an explicit "when not to use this" section so you can make this decision with numbers, not instinct.

---

← [Back to Main README](../Readme.md) · [← Part 3](../part-03-network-architectures/README.md) · [Part 5 →](../part-05-container-and-kubernetes-architectures/README.md)
