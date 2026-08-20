# Part 10 — Modern Architecture Patterns

**Chapters 77–86 · Status: 🔒 Planned**

← [Back to Main README](../Readme.md) · [← Part 9](../part-09-industry-specific-architectures/README.md) · [Part 11 →](../part-11-security-reference-architectures/README.md)

---

## What This Part Covers

Modern architecture patterns are the vocabulary of distributed systems design. CQRS, event sourcing, saga, circuit breaker, strangler fig — these are not buzzwords. They are solutions to specific, recurring problems that appear when you scale a system beyond what a single database and a synchronous request/response model can handle.

This part covers ten patterns that appear repeatedly in production distributed systems on AWS — each chapter explaining not just what the pattern is, but the specific problem it solves, the AWS services that implement it, the failure modes it introduces, and the operational complexity it adds. Every pattern comes with an honest "when not to use this" section, because the most common mistake with these patterns is applying them to problems they weren't designed to solve.

---

## Which Chapter Do You Need?

| If your problem is... | Read this chapter |
|---|---|
| A monolith that needs to be decomposed into independent services | [Ch. 77 — Microservices](./chapter-77-microservices.md) |
| Read and write workloads with different scaling and model requirements | [Ch. 78 — CQRS](./chapter-78-cqrs.md) |
| Needing a complete, replayable history of every state change | [Ch. 79 — Event Sourcing](./chapter-79-event-sourcing.md) |
| Maintaining data consistency across multiple services without distributed transactions | [Ch. 80 — Saga Pattern](./chapter-80-saga-pattern.md) |
| Guaranteeing at-least-once event delivery from a database change | [Ch. 81 — Outbox Pattern](./chapter-81-outbox-pattern.md) |
| Preventing one slow or failing service from taking down the whole system | [Ch. 82 — Bulkhead](./chapter-82-bulkhead.md) |
| Stopping cascading failures when a downstream service is degraded | [Ch. 83 — Circuit Breaker](./chapter-83-circuit-breaker.md) |
| Migrating a monolith to microservices without a big-bang rewrite | [Ch. 84 — Strangler Fig](./chapter-84-strangler-fig.md) |
| Adding cross-cutting concerns (logging, auth, mTLS) without modifying services | [Ch. 85 — Sidecar Pattern](./chapter-85-sidecar-pattern.md) |
| Managing cross-cutting concerns at the entry point to a microservices system | [Ch. 86 — API Gateway Pattern](./chapter-86-api-gateway-pattern.md) |

---

## Chapters at a Glance

### Chapter 77 — Microservices

**[Read →](./chapter-77-microservices.md)** · 🔒 Coming soon

Decomposing a monolith into independently deployable services — each owning its domain, its data store, and its deployment pipeline — with the service boundary design, inter-service communication patterns, and operational discipline that makes microservices a genuine improvement over a monolith rather than a distributed monolith.

**Key topics:** Domain-driven design for service boundary identification, synchronous (REST, gRPC) vs. asynchronous (events) inter-service communication, database-per-service pattern, service discovery with AWS Cloud Map, distributed tracing with X-Ray, independent deployment pipelines, microservices vs. monolith decision framework, organizational alignment (Conway's Law), operational complexity cost

---

### Chapter 78 — CQRS

**[Read →](./chapter-78-cqrs.md)** · 🔒 Coming soon

Command Query Responsibility Segregation — separating the write model (commands that change state) from the read model (queries that return data) — allowing each to be optimized, scaled, and evolved independently, with the eventual consistency model that bridges the two.

**Key topics:** Command and query model separation, write-side with Aurora/DynamoDB, read-side with OpenSearch/ElastiCache/DynamoDB, event-driven synchronization between write and read models, eventual consistency and read-your-writes patterns, CQRS with event sourcing (natural combination), when CQRS is justified vs. over-engineered, operational complexity of maintaining two models

---

### Chapter 79 — Event Sourcing

**[Read →](./chapter-79-event-sourcing.md)** · 🔒 Coming soon

Storing the complete history of state changes as an immutable sequence of events — rather than storing only current state — enabling full audit trails, temporal queries, event replay for rebuilding projections, and the ability to derive new read models from historical events without touching the source data.

**Key topics:** Event store design (append-only, immutable), event schema versioning and evolution, projection building from event streams, snapshot strategy for performance, event replay for rebuilding state, Kinesis and DynamoDB Streams as event store implementations, event sourcing with CQRS, when event sourcing is justified (audit requirements, temporal queries), operational complexity

---

### Chapter 80 — Saga Pattern

**[Read →](./chapter-80-saga-pattern.md)** · 🔒 Coming soon

Managing data consistency across multiple services in a distributed transaction — without two-phase commit — using a sequence of local transactions coordinated by either a central orchestrator (Step Functions) or choreographed through events, with compensating transactions for rollback when a step fails.

**Key topics:** Choreography vs. orchestration saga implementations, compensating transactions for rollback, Step Functions as a saga orchestrator, idempotency requirements for saga steps, failure handling and partial rollback, saga state visibility and debugging, distributed transaction alternatives (eventual consistency acceptance), when sagas are necessary vs. over-engineered

---

### Chapter 81 — Outbox Pattern

**[Read →](./chapter-81-outbox-pattern.md)** · 🔒 Coming soon

Guaranteeing that a database write and an event publication happen atomically — without distributed transactions — by writing the event to an outbox table in the same database transaction as the business data, then reliably delivering it to the message broker via a separate process.

**Key topics:** Outbox table design, transactional outbox with Aurora/RDS, CDC-based outbox delivery with DMS or Debezium, polling-based outbox delivery, exactly-once vs. at-least-once delivery semantics, idempotent consumers, outbox cleanup and retention, DynamoDB Streams as a native outbox alternative, operational monitoring for outbox lag

---

### Chapter 82 — Bulkhead

**[Read →](./chapter-82-bulkhead.md)** · 🔒 Coming soon

Isolating system components into separate resource pools — thread pools, connection pools, Lambda reserved concurrency, separate Auto Scaling Groups — so that a surge or failure in one component cannot exhaust shared resources and take down unrelated components.

**Key topics:** Thread pool isolation for synchronous services, Lambda reserved concurrency as a bulkhead, separate SQS queues per consumer type, database connection pool isolation with RDS Proxy, ALB target group separation, bulkhead sizing strategy, monitoring resource pool utilization, bulkhead vs. circuit breaker (complementary patterns), AWS service quota isolation per workload

---

### Chapter 83 — Circuit Breaker

**[Read →](./chapter-83-circuit-breaker.md)** · 🔒 Coming soon

Stopping cascading failures by detecting when a downstream service is degraded and short-circuiting calls to it — returning a fast failure or a cached fallback instead of waiting for a timeout — giving the downstream service time to recover without the caller accumulating thread/connection exhaustion.

**Key topics:** Circuit breaker states (closed, open, half-open), failure threshold and recovery timeout configuration, AWS App Mesh and Envoy circuit breaker configuration, application-level circuit breaker with Resilience4j/Hystrix, fallback strategies (cached response, degraded mode, static response), circuit breaker observability (state transitions, failure rates), circuit breaker vs. retry (when each is appropriate)

---

### Chapter 84 — Strangler Fig

**[Read →](./chapter-84-strangler-fig.md)** · 🔒 Coming soon

Migrating a monolith to microservices incrementally — by routing specific functionality to new services while the monolith continues to handle everything else — until the monolith is fully replaced without a big-bang rewrite and without a flag-day cutover.

**Key topics:** Routing layer design (API Gateway, ALB, reverse proxy), feature-by-feature migration sequencing, data migration strategy (shared database → separate databases), strangler fig with event interception, rollback strategy for each migration step, testing strategy for the hybrid monolith/microservices state, organizational change management, migration timeline and sequencing

---

### Chapter 85 — Sidecar Pattern

**[Read →](./chapter-85-sidecar-pattern.md)** · 🔒 Coming soon

Deploying a secondary container alongside the primary application container to handle cross-cutting concerns — logging, metrics collection, mTLS, service discovery, configuration management — without modifying the application code, enabling these concerns to be managed and updated independently.

**Key topics:** Sidecar container configuration in ECS task definitions and Kubernetes pods, AWS Distro for OpenTelemetry (ADOT) as a logging/metrics sidecar, Envoy proxy as a service mesh sidecar, AWS AppConfig agent as a configuration sidecar, sidecar lifecycle management, resource overhead of sidecar containers, sidecar vs. library vs. service for cross-cutting concerns

---

### Chapter 86 — API Gateway Pattern

**[Read →](./chapter-86-api-gateway-pattern.md)** · 🔒 Coming soon

The API Gateway as the single entry point to a microservices system — handling cross-cutting concerns (authentication, rate limiting, request routing, protocol translation, response aggregation) at the edge, so individual services don't need to implement them independently.

**Key topics:** Amazon API Gateway as the managed implementation, request routing to backend services, authentication and authorization at the gateway (JWT, Lambda authorizer, IAM), rate limiting and throttling, request/response transformation, API composition (aggregating multiple service calls), backend for frontend (BFF) pattern, API versioning strategy, gateway observability, API Gateway vs. ALB vs. service mesh for routing

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| Amazon API Gateway | 77, 84, 86 |
| AWS Step Functions | 80 |
| Amazon EventBridge | 77, 79, 80, 81 |
| Amazon SQS | 80, 81, 82 |
| Amazon Kinesis | 79 |
| Amazon DynamoDB Streams | 81 |
| AWS App Mesh / Envoy | 83, 85 |
| AWS X-Ray | 77, 83 |
| Amazon CloudWatch | 82, 83, 86 |
| AWS Cloud Map | 77 |
| Amazon Aurora / RDS | 78, 81 |
| Amazon OpenSearch | 78 |
| AWS DMS | 81 |

---

## Pattern Relationships

These patterns are not independent — they combine naturally:

| Combination | Why It Works |
|-------------|-------------|
| CQRS + Event Sourcing | Event sourcing provides the write model; CQRS separates the read projections |
| Saga + Outbox | Outbox guarantees reliable event delivery for saga step coordination |
| Microservices + Bulkhead + Circuit Breaker | Bulkhead isolates resources; circuit breaker stops cascading failures between services |
| Strangler Fig + API Gateway | API Gateway is the routing layer that makes incremental migration transparent to clients |
| Sidecar + Service Mesh | Service mesh is implemented via sidecars across all services |

---

← [Back to Main README](../Readme.md) · [← Part 9](../part-09-industry-specific-architectures/README.md) · [Part 11 →](../part-11-security-reference-architectures/README.md)
