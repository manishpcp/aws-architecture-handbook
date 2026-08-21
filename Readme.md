# 🏗️ AWS Architecture Handbook

### *100 Production-Ready Cloud Architectures — with Terraform, AI, Security, FinOps & Enterprise Design Patterns*

> **Publishing schedule:** One part per week. Star ⭐ this repo to get notified when new parts drop.

---

## 🎯 What This Book Is

This is not a collection of diagrams. Each of the 100 chapters is a **complete, production-grade architecture reference** that covers the full lifecycle of a real AWS workload — from business requirements and design decisions, through Terraform implementation, security hardening, cost optimization, failure scenarios, and operational runbooks.

Every chapter answers the questions that matter in a real architecture review:

- Why this design over the alternatives?
- What are the honest trade-offs and limitations?
- What does it cost, and how do you control that cost?
- What breaks, how do you detect it, and how do you recover?
- What does production-ready Terraform actually look like for this pattern?

If you have ever sat in an architecture review and wished someone had written down the reasoning behind the decisions — not just the diagram — this book is for you.

> This handbook is free and always will be. If it saves you hours of research, a bad architecture decision, or an awkward review meeting — consider buying me a coffee. It keeps new chapters coming every week.

[![Buy Me A Coffee](https://img.buymeacoffee.com/button-api/?text=Buy%20me%20a%20coffee&emoji=&slug=manishpcp&button_colour=FFDD00&font_colour=000000&font_family=Cookie&outline_colour=000000&coffee_colour=ffffff)](https://www.buymeacoffee.com/manishpcp)

---

## 👥 Who This Is For

| Role | What You'll Get |
|------|----------------|
| **Cloud Architects** | A decision framework and vocabulary for evaluating design trade-offs across 100 real patterns |
| **Senior Engineers** | Production-ready Terraform, AWS CLI examples, and failure-mode analysis for every architecture |
| **Engineering Managers** | Business-case framing, cost estimates, and risk/trade-off summaries for stakeholder conversations |
| **DevOps / Platform Engineers** | Deployment flows, CI/CD patterns, monitoring strategies, and operational runbooks |
| **Security Engineers** | Threat models, IAM patterns, encryption strategies, and compliance control mappings per architecture |
| **FinOps Practitioners** | Cost breakdowns, Savings Plan guidance, and cost-optimization levers for every pattern |

---

## 📐 What Every Chapter Covers

Each chapter follows the same battle-tested structure so you can navigate directly to what you need:

| Section | What You'll Find |
|---------|-----------------|
| **Executive Summary** | The business problem this architecture solves and when to use it |
| **Business Requirements** | NFRs, RPO/RTO targets, scalability goals, compliance requirements |
| **Architecture Overview** | Design philosophy, core components, and how they interact |
| **AWS Services Used** | Why each service was chosen, alternatives considered, limitations, pricing |
| **Architecture Diagram** | Full Mermaid diagram of the complete system |
| **End-to-End Request Flow** | Step-by-step walkthrough of a request through the system |
| **Deployment Flow** | CI/CD pipeline, blue-green/canary strategy, rollback procedures |
| **Network Topology** | VPC design, subnet layout, security groups, routing |
| **Identity and Access** | IAM roles, policies, least-privilege patterns, cross-account access |
| **Security Architecture** | Encryption, WAF, threat model, compliance controls |
| **High Availability** | AZ failure handling, instance failure, failover behavior |
| **Disaster Recovery** | Backup strategy, DR tier classification, tested RTO/RPO |
| **Scalability** | Auto Scaling, database scaling, queue scaling, known limits |
| **Performance Optimization** | Caching strategy, connection pooling, CDN, async offloading |
| **Cost Optimization** | Estimated monthly costs by size, Savings Plans, FinOps levers |
| **AI-Assisted Operations** | Amazon Q, Bedrock, AI-assisted troubleshooting and capacity planning |
| **Terraform Implementation** | Production-ready, modular IaC with best practices applied |
| **AWS CLI Examples** | Deployment validation, monitoring, troubleshooting, cleanup |
| **CI/CD Integration** | GitHub Actions, GitLab CI, CodePipeline patterns |
| **Monitoring** | CloudWatch dashboards, SLIs, SLOs, error budgets, burn-rate alerting |
| **Logging** | Centralized logging, retention strategy, Athena for ad hoc queries |
| **Operational Excellence** | Runbooks, patch management, change management, incident response |
| **Failure Scenarios** | 10–15 real failure modes with detection, resolution, and prevention |
| **Troubleshooting Guide** | Symptom → cause → diagnosis → CLI commands → resolution |
| **Best Practices** | 25–30 actionable, specific rules for this architecture |
| **Anti-Patterns** | What not to do, why it fails, and the correct approach |
| **Alternatives** | Comparison table of alternative architectures with trade-offs |
| **Real Enterprise Case Study** | A composite real-world scenario with challenges and lessons learned |
| **Architecture Decision Record** | A ready-to-use ADR template for this architecture |
| **Architecture Review Checklist** | Security, networking, operations, cost, compliance gates |
| **Architect's Corner** | Hidden trade-offs, scaling limits, evolution path, decision matrix |

> Writing 100 chapters at this depth takes a serious amount of time. If this structure is already more useful than most paid resources you've seen, a coffee goes a long way toward keeping the publishing schedule on track.

[![Buy Me A Coffee](https://img.buymeacoffee.com/button-api/?text=Buy%20me%20a%20coffee&emoji=&slug=manishpcp&button_colour=FFDD00&font_colour=000000&font_family=Cookie&outline_colour=000000&coffee_colour=ffffff)](https://www.buymeacoffee.com/manishpcp)

---

## 📦 Book Structure & Progress

| Symbol | Meaning |
|--------|---------|
| ✅ | Published & available |
| 🔜 | Coming soon (next up) |
| 🔒 | Planned |

---

## ✅ Part 1 — Foundations of AWS Reference Architectures

**Chapters 01–04 · Status: Complete**

> The vocabulary, principles, and decision frameworks every architect needs before touching a single service. Covers what a reference architecture actually is (not just a diagram), the AWS Well-Architected Framework as a scoring lens, Architecture Decision Records, and the core design principles that recur across all 100 chapters.

→ [Browse Part 1](./part-01-foundations-of-aws-reference-architectures/README.md)

| # | Chapter | Key Topics |
|---|---------|-----------|
| 01 | [Introduction to Production-Ready Architecture](./part-01-foundations-of-aws-reference-architectures/chapter-01-introduction-to-production-ready-architecture.md) | Reference architecture definition, Well-Architected Framework, architecture maturity levels, design principles |
| 02 | [AWS Building Blocks](./part-01-foundations-of-aws-reference-architectures/chapter-02-aws-building-blocks.md) | Core AWS service categories, service selection criteria, managed vs. self-managed trade-offs |
| 03 | [Enterprise Design Principles](./part-01-foundations-of-aws-reference-architectures/chapter-03-enterprise-design-principles.md) | Failure isolation, loose coupling, immutable infrastructure, least privilege, cost-awareness |
| 04 | [Architecture Documentation & ADRs](./part-01-foundations-of-aws-reference-architectures/chapter-04-architecture-documentation.md) | ADR format and lifecycle, documentation as code, review processes |

---

## ✅ Part 2 — Core Infrastructure Architectures

**Chapters 05–14 · Status: Complete**

> The battle-tested compute and infrastructure patterns that underpin every production AWS workload. From a single production EC2 instance to canary deployments — each chapter covers the full spectrum from when to use the pattern to how to operate it under real failure conditions.

→ [Browse Part 2](./part-02-core-infrastructure-architectures/README.md)

| # | Chapter | Key Topics |
|---|---------|-----------|
| 05 | [Single EC2 Production Architecture](./part-02-core-infrastructure-architectures/chapter-05-single-ec2-production-architecture.md) | EC2 Auto Recovery, AWS Backup, Session Manager, honest availability trade-offs |
| 06 | [Highly Available Multi-AZ Web Application](./part-02-core-infrastructure-architectures/chapter-06-highly-available-multi-az-web-application.md) | ALB, Multi-AZ ASG, Aurora Multi-AZ, 99.95% availability design |
| 07 | [Three-Tier Enterprise Architecture](./part-02-core-infrastructure-architectures/chapter-07-three-tier-enterprise-architecture.md) | Presentation, application, and data tier separation, network segmentation |
| 08 | [Auto Scaling Architecture](./part-02-core-infrastructure-architectures/chapter-08-auto-scaling-architecture.md) | Target tracking, step scaling, scheduled scaling, warm pools |
| 09 | [Shared Services Architecture](./part-02-core-infrastructure-architectures/chapter-09-shared-services-architecture.md) | Centralized networking, shared tooling, cross-account service consumption |
| 10 | [Bastionless Infrastructure with SSM](./part-02-core-infrastructure-architectures/chapter-10-bastionless-infrastructure-with-ssm.md) | Session Manager, no SSH keys, VPC endpoints, audit trail |
| 11 | [Golden AMI Architecture](./part-02-core-infrastructure-architectures/chapter-11-golden-ami-architecture.md) | EC2 Image Builder, AMI pipelines, hardened base images, patch baking |
| 12 | [Immutable Infrastructure](./part-02-core-infrastructure-architectures/chapter-12-immutable-infrastructure.md) | Replace-not-patch, pipeline-built AMIs, configuration drift elimination |
| 13 | [Blue-Green Infrastructure](./part-02-core-infrastructure-architectures/chapter-13-blue-green-infrastructure.md) | Zero-downtime deployments, traffic shifting, automated rollback |
| 14 | [Canary Infrastructure](./part-02-core-infrastructure-architectures/chapter-14-canary-infrastructure.md) | Weighted routing, progressive traffic shifting, automated rollback triggers |

---

## ✅ Part 3 — Network Architectures

**Chapters 15–24 · Status: Complete**

> VPCs, Transit Gateways, hybrid connectivity, and edge delivery — the foundational networking layer that every other architecture in this book runs on top of.

→ [Browse Part 3](./part-03-network-architectures/README.md)

| # | Chapter | Key Topics |
|---|---------|----------|
| 15 | [Enterprise VPC](./part-03-network-architectures/chapter-15-enterprise-vpc.md) | CIDR planning, subnet tiering, route tables, VPC endpoints, IPAM, Flow Logs |
| 16 | [Hub-and-Spoke Networking](./part-03-network-architectures/chapter-16-hub-and-spoke-networking.md) | Hub VPC design, centralized egress, cross-account sharing, RAM |
| 17 | [Transit Gateway](./part-03-network-architectures/chapter-17-transit-gateway.md) | TGW route tables, attachment types, inter-region peering, centralized inspection |
| 18 | [Cloud WAN](./part-03-network-architectures/chapter-18-cloud-wan.md) | Global network policy, segments, inter-region backbone, TGW migration |
| 19 | [Shared Services VPC](./part-03-network-architectures/chapter-19-shared-services-vpc.md) | Centralized DNS, NAT egress, Network Firewall, Private CA, cost allocation |
| 20 | [PrivateLink Architecture](./part-03-network-architectures/chapter-20-privatelink-architecture.md) | VPC endpoint services, NLB, cross-account exposure, DNS resolution |
| 21 | [Global Accelerator](./part-03-network-architectures/chapter-21-global-accelerator.md) | Anycast IPs, health-check failover, traffic dial, Shield integration |
| 22 | [CloudFront Edge Architecture](./part-03-network-architectures/chapter-22-cloudfront-edge-architecture.md) | Cache behaviors, Lambda@Edge, WAF at edge, signed URLs, origin failover |
| 23 | [Hybrid VPN](./part-03-network-architectures/chapter-23-hybrid-vpn.md) | Site-to-Site VPN, BGP routing, redundant tunnels, TGW attachment |
| 24 | [Direct Connect Enterprise](./part-03-network-architectures/chapter-24-direct-connect-enterprise.md) | Dedicated connections, Virtual Interfaces, DX Gateway, resilience models |

---

## ✅ Part 4 — Serverless Architectures

**Chapters 25–34 · Status: Complete**

> Lambda, API Gateway, EventBridge, and Step Functions — building systems that scale to zero and to infinity without managing servers.

→ [Browse Part 4](./part-04-serverless-architectures/README.md)

| # | Chapter | Key Topics |
|---|---------|----------|
| 25 | [REST APIs](./part-04-serverless-architectures/chapter-25-rest-apis.md) | API Gateway REST vs HTTP API, Lambda proxy, Cognito authorizer, cold starts, cost break-even |
| 26 | [Event-Driven Systems](./part-04-serverless-architectures/chapter-26-event-driven-systems.md) | SNS fan-out, SQS, EventBridge routing, idempotent consumers, DLQ strategy |
| 27 | [Lambda Microservices](./part-04-serverless-architectures/chapter-27-lambda-microservices.md) | Function boundaries, per-function IAM, Lambda layers, versioning and aliases |
| 28 | [Step Functions Orchestration](./part-04-serverless-architectures/chapter-28-step-functions.md) | Standard vs Express workflows, ASL, error handling, SDK integrations |
| 29 | [Webhook Processing](./part-04-serverless-architectures/chapter-29-webhook-processing.md) | Fast acknowledgment, SQS buffering, signature verification, DLQ, replay |
| 30 | [Real-Time APIs](./part-04-serverless-architectures/chapter-30-real-time-apis.md) | WebSocket API, connection management, DynamoDB, broadcasting, AppSync |
| 31 | [Scheduled Processing](./part-04-serverless-architectures/chapter-31-scheduled-processing.md) | EventBridge Scheduler, flexible time windows, idempotent jobs, missed execution monitoring |
| 32 | [Serverless ETL](./part-04-serverless-architectures/chapter-32-serverless-etl.md) | S3-triggered Lambda, AWS Glue, Data Catalog, Lambda vs Glue decision framework |
| 33 | [EventBridge Integration](./part-04-serverless-architectures/chapter-33-eventbridge-integration.md) | Custom event buses, schema registry, archive and replay, API destinations, Pipes |
| 34 | [Multi-Account Serverless](./part-04-serverless-architectures/chapter-34-multi-account-serverless.md) | Cross-account Lambda, cross-account EventBridge, shared API Gateway, centralized observability |

---

## ✅ Part 5 — Container & Kubernetes Architectures

**Chapters 35–42 · Status: Complete**

> ECS, EKS, service meshes, GitOps, and GPU workloads — containers done right at enterprise scale.

→ [Browse Part 5](./part-05-container-and-kubernetes-architectures/README.md)

| # | Chapter | Key Topics |
|---|---------|----------|
| 35 | [ECS Fargate](./part-05-container-and-kubernetes-architectures/chapter-35-ecs-fargate.md) | Task definitions, Fargate vs EC2 launch type, ALB integration, Cloud Map, Fargate Spot |
| 36 | [Amazon EKS](./part-05-container-and-kubernetes-architectures/chapter-36-amazon-eks.md) | Managed node groups, IRSA, ALB Ingress Controller, Karpenter, EKS Blueprints |
| 37 | [Service Mesh](./part-05-container-and-kubernetes-architectures/chapter-37-service-mesh.md) | Envoy sidecar, mTLS, traffic shifting, circuit breaking, X-Ray tracing |
| 38 | [GitOps Platform](./part-05-container-and-kubernetes-architectures/chapter-38-gitops-platform.md) | ArgoCD vs Flux, multi-environment promotion, drift detection, Sealed Secrets |
| 39 | [Multi-Cluster Kubernetes](./part-05-container-and-kubernetes-architectures/chapter-39-multicluster-kubernetes.md) | Cluster federation, cross-cluster service discovery, global load balancing, unified observability |
| 40 | [Platform Engineering](./part-05-container-and-kubernetes-architectures/chapter-40-platform-engineering.md) | Internal developer platform, Backstage, golden path templates, OPA/Gatekeeper, cost showback |
| 41 | [Batch Containers](./part-05-container-and-kubernetes-architectures/chapter-41-batch-containers.md) | AWS Batch, job queues, Spot integration, array jobs, DAG workflows |
| 42 | [GPU Workloads](./part-05-container-and-kubernetes-architectures/chapter-42-gpu-workloads.md) | GPU instance families, EKS GPU node groups, MIG/time-slicing, Inferentia, Trainium |

---

## ✅ Part 6 — Data Platform Architectures

**Chapters 43–50 · Status: Complete**

> Aurora, DynamoDB, data lakes, lake houses, streaming analytics, and multi-database patterns.

→ [Browse Part 6](./part-06-data-platform-architectures/README.md)

| # | Chapter | Key Topics |
|---|---------|----------|
| 43 | [Relational Database](./part-06-data-platform-architectures/chapter-43-relational-database.md) | RDS vs Aurora, Multi-AZ, read replicas, RDS Proxy, Performance Insights, zero-downtime migration |
| 44 | [Aurora Global Database](./part-06-data-platform-architectures/chapter-44-aurora-global-database.md) | Global cluster, sub-second replication, regional read scaling, managed failover, write forwarding |
| 45 | [DynamoDB](./part-06-data-platform-architectures/chapter-45-dynamodb.md) | Single-table design, partition key selection, GSI/LSI, DAX, Streams, global tables |
| 46 | [Data Lake](./part-06-data-platform-architectures/chapter-46-data-lake.md) | S3 zone architecture, Glue Data Catalog, Athena, Lake Formation, Parquet/ORC, lifecycle policies |
| 47 | [Lake House](./part-06-data-platform-architectures/chapter-47-lake-house.md) | Redshift Spectrum, data tiering, Redshift data sharing, Lake Formation governance |
| 48 | [Streaming Analytics](./part-06-data-platform-architectures/chapter-48-streaming-analytics.md) | Kinesis Data Streams, Firehose, Apache Flink, windowing, MSK, exactly-once semantics |
| 49 | [Data Warehouse](./part-06-data-platform-architectures/chapter-49-data-warehouse.md) | Redshift Serverless vs provisioned, distribution styles, sort keys, WLM, AQUA |
| 50 | [Multi-Database Architecture](./part-06-data-platform-architectures/chapter-50-multi-database-architecture.md) | Polyglot persistence, CDC with DMS, cache invalidation, search sync, eventual consistency |

---

## 🔜 Part 7 — AI & Machine Learning Architectures

**Chapters 51–58 · Status: Coming Soon**

> Bedrock, RAG, vector databases, AI agents, MLOps pipelines, and production-grade generative AI.

→ [Browse Part 7](./part-07-ai-and-machine-learning-architectures/README.md)

| # | Chapter | Status |
|---|---------|--------|
| 51 | [Generative AI Platform](./part-07-ai-and-machine-learning-architectures/chapter-51-generative-ai-platform.md) | 🔜 Coming Soon |
| 52 | [RAG Architecture](./part-07-ai-and-machine-learning-architectures/chapter-52-rag-architecture.md) | 🔜 Coming Soon |
| 53 | [Vector Database](./part-07-ai-and-machine-learning-architectures/chapter-53-vector-database.md) | 🔜 Coming Soon |
| 54 | [AI Chatbot](./part-07-ai-and-machine-learning-architectures/chapter-54-ai-chatbot.md) | 🔜 Coming Soon |
| 55 | [Model Serving](./part-07-ai-and-machine-learning-architectures/chapter-55-model-serving.md) | 🔜 Coming Soon |
| 56 | [AI Agent Architecture](./part-07-ai-and-machine-learning-architectures/chapter-56-ai-agent-architecture.md) | 🔜 Coming Soon |
| 57 | [Document Intelligence](./part-07-ai-and-machine-learning-architectures/chapter-57-document-intelligence.md) | 🔜 Coming Soon |
| 58 | [MLOps Pipeline](./part-07-ai-and-machine-learning-architectures/chapter-58-mlops-pipeline.md) | 🔜 Coming Soon |

---

## 🔜 Part 8 — Enterprise Application Architectures

**Chapters 59–66 · Status: Coming Soon**

> SaaS multi-tenancy, B2B platforms, CRM/ERP integration, API marketplaces, and identity federation.

→ [Browse Part 8](./part-08-enterprise-application-architectures/README.md)

| # | Chapter | Status |
|---|---------|--------|
| 59 | [SaaS Multi-Tenant](./part-08-enterprise-application-architectures/chapter-59-saas-multitenant.md) | 🔜 Coming Soon |
| 60 | [B2B SaaS](./part-08-enterprise-application-architectures/chapter-60-b2b-saas.md) | 🔜 Coming Soon |
| 61 | [CRM Platform](./part-08-enterprise-application-architectures/chapter-61-crm-platform.md) | 🔜 Coming Soon |
| 62 | [ERP Integration](./part-08-enterprise-application-architectures/chapter-62-erp-integration.md) | 🔜 Coming Soon |
| 63 | [API Marketplace](./part-08-enterprise-application-architectures/chapter-63-api-marketplace.md) | 🔜 Coming Soon |
| 64 | [Identity Federation](./part-08-enterprise-application-architectures/chapter-64-identity-federation.md) | 🔜 Coming Soon |
| 65 | [Enterprise Portal](./part-08-enterprise-application-architectures/chapter-65-enterprise-portal.md) | 🔜 Coming Soon |
| 66 | [Workflow Automation](./part-08-enterprise-application-architectures/chapter-66-workflow-automation.md) | 🔜 Coming Soon |

---

## 🔒 Part 9 — Industry-Specific Architectures

**Chapters 67–76 · Status: Planned**

> Regulated, compliance-heavy reference architectures for banking, healthcare, retail, gaming, and more.

→ [Browse Part 9](./part-09-industry-specific-architectures/README.md)

| # | Chapter | Status |
|---|---------|--------|
| 67 | [Banking](./part-09-industry-specific-architectures/chapter-67-banking.md) | 🔒 Planned |
| 68 | [Healthcare](./part-09-industry-specific-architectures/chapter-68-healthcare.md) | 🔒 Planned |
| 69 | [Insurance](./part-09-industry-specific-architectures/chapter-69-insurance.md) | 🔒 Planned |
| 70 | [Government](./part-09-industry-specific-architectures/chapter-70-government.md) | 🔒 Planned |
| 71 | [Retail](./part-09-industry-specific-architectures/chapter-71-retail.md) | 🔒 Planned |
| 72 | [Manufacturing](./part-09-industry-specific-architectures/chapter-72-manufacturing.md) | 🔒 Planned |
| 73 | [Telecommunications](./part-09-industry-specific-architectures/chapter-73-telecommunications.md) | 🔒 Planned |
| 74 | [Media Streaming](./part-09-industry-specific-architectures/chapter-74-media-streaming.md) | 🔒 Planned |
| 75 | [Education](./part-09-industry-specific-architectures/chapter-75-education.md) | 🔒 Planned |
| 76 | [Gaming](./part-09-industry-specific-architectures/chapter-76-gaming.md) | 🔒 Planned |

---

## 🔒 Part 10 — Modern Architecture Patterns

**Chapters 77–86 · Status: Planned**

> Microservices, CQRS, event sourcing, saga, circuit breaker, strangler fig, and more — the patterns behind resilient systems.

→ [Browse Part 10](./part-10-modern-architecture-patterns/README.md)

| # | Chapter | Status |
|---|---------|--------|
| 77 | [Microservices](./part-10-modern-architecture-patterns/chapter-77-microservices.md) | 🔒 Planned |
| 78 | [CQRS](./part-10-modern-architecture-patterns/chapter-78-cqrs.md) | 🔒 Planned |
| 79 | [Event Sourcing](./part-10-modern-architecture-patterns/chapter-79-event-sourcing.md) | 🔒 Planned |
| 80 | [Saga Pattern](./part-10-modern-architecture-patterns/chapter-80-saga-pattern.md) | 🔒 Planned |
| 81 | [Outbox Pattern](./part-10-modern-architecture-patterns/chapter-81-outbox-pattern.md) | 🔒 Planned |
| 82 | [Bulkhead](./part-10-modern-architecture-patterns/chapter-82-bulkhead.md) | 🔒 Planned |
| 83 | [Circuit Breaker](./part-10-modern-architecture-patterns/chapter-83-circuit-breaker.md) | 🔒 Planned |
| 84 | [Strangler Fig](./part-10-modern-architecture-patterns/chapter-84-strangler-fig.md) | 🔒 Planned |
| 85 | [Sidecar Pattern](./part-10-modern-architecture-patterns/chapter-85-sidecar-pattern.md) | 🔒 Planned |
| 86 | [API Gateway Pattern](./part-10-modern-architecture-patterns/chapter-86-api-gateway-pattern.md) | 🔒 Planned |

---

## 🔒 Part 11 — Security Reference Architectures

**Chapters 87–94 · Status: Planned**

> Zero trust, multi-account security, IAM Identity Center, secrets management, encryption, SOC operations, and compliance.

→ [Browse Part 11](./part-11-security-reference-architectures/README.md)

| # | Chapter | Status |
|---|---------|--------|
| 87 | [Zero Trust](./part-11-security-reference-architectures/chapter-87-zero-trust.md) | 🔒 Planned |
| 88 | [Multi-Account Security](./part-11-security-reference-architectures/chapter-88-multi-account-security.md) | 🔒 Planned |
| 89 | [IAM Identity Center](./part-11-security-reference-architectures/chapter-89-iam-identity-center.md) | 🔒 Planned |
| 90 | [Secrets Management](./part-11-security-reference-architectures/chapter-90-secrets-management.md) | 🔒 Planned |
| 91 | [Encryption](./part-11-security-reference-architectures/chapter-91-encryption.md) | 🔒 Planned |
| 92 | [SOC Operations](./part-11-security-reference-architectures/chapter-92-soc-operations.md) | 🔒 Planned |
| 93 | [Threat Detection](./part-11-security-reference-architectures/chapter-93-threat-detection.md) | 🔒 Planned |
| 94 | [Compliance](./part-11-security-reference-architectures/chapter-94-compliance.md) | 🔒 Planned |

---

## 🔒 Part 12 — Resilience, Operations & Cost

**Chapters 95–100 · Status: Planned**

> Disaster recovery, observability, FinOps, multi-region active-active, landing zones, and future-proof cloud architecture.

→ [Browse Part 12](./part-12-resilience-operations-and-cost/README.md)

| # | Chapter | Status |
|---|---------|--------|
| 95 | [Disaster Recovery](./part-12-resilience-operations-and-cost/chapter-95-disaster-recovery.md) | 🔒 Planned |
| 96 | [Observability Platform](./part-12-resilience-operations-and-cost/chapter-96-observability-platform.md) | 🔒 Planned |
| 97 | [FinOps Architecture](./part-12-resilience-operations-and-cost/chapter-97-finops-architecture.md) | 🔒 Planned |
| 98 | [Multi-Region Active-Active](./part-12-resilience-operations-and-cost/chapter-98-multi-region-active-active.md) | 🔒 Planned |
| 99 | [Reference Landing Zone](./part-12-resilience-operations-and-cost/chapter-99-reference-landing-zone.md) | 🔒 Planned |
| 100 | [Future-Proof Cloud Architecture](./part-12-resilience-operations-and-cost/chapter-100-future-proof-cloud-architecture.md) | 🔒 Planned |

---

## 🗺️ How to Navigate This Book

**If you're new to AWS architecture**, start with Part 1. Chapter 1 establishes the vocabulary and decision framework used throughout the entire book.

**If you're looking for a specific pattern**, jump directly to the relevant part. Each part README has a quick-reference guide to help you pick the right chapter.

**If you're preparing for an architecture review**, use the checklist at the end of the relevant chapter. Every chapter has a ready-to-use review checklist covering security, networking, operations, cost, and compliance.

**If you're evaluating whether a pattern fits your requirements**, go to the "Alternatives" and "Architect's Corner" sections of the relevant chapter. These sections explicitly compare the pattern against its alternatives and tell you when *not* to use it.

**If you're troubleshooting a live system**, go directly to the "Failure Scenarios" and "Troubleshooting Guide" sections of the relevant chapter.

---

## 🔗 Quick Reference by Use Case

| I need to... | Start here |
|---|---|
| Understand AWS architecture fundamentals | [Part 1](./part-01-foundations-of-aws-reference-architectures/README.md) |
| Deploy a production web application | [Ch. 06](./part-02-core-infrastructure-architectures/chapter-06-highly-available-multi-az-web-application.md) or [Ch. 07](./part-02-core-infrastructure-architectures/chapter-07-three-tier-enterprise-architecture.md) |
| Run a small internal tool cost-effectively | [Ch. 05](./part-02-core-infrastructure-architectures/chapter-05-single-ec2-production-architecture.md) |
| Eliminate bastion hosts | [Ch. 10](./part-02-core-infrastructure-architectures/chapter-10-bastionless-infrastructure-with-ssm.md) |
| Implement zero-downtime deployments | [Ch. 13](./part-02-core-infrastructure-architectures/chapter-13-blue-green-infrastructure.md) or [Ch. 14](./part-02-core-infrastructure-architectures/chapter-14-canary-infrastructure.md) |
| Design Auto Scaling for unpredictable traffic | [Ch. 08](./part-02-core-infrastructure-architectures/chapter-08-auto-scaling-architecture.md) |
| Standardize base images across a fleet | [Ch. 11](./part-02-core-infrastructure-architectures/chapter-11-golden-ami-architecture.md) |

---

## ⭐ Star & Watch

If this is useful, **star the repo** so you get notified when new parts drop.

Found an error or want to suggest a topic? Open an issue — all feedback is welcome.

---

## ☕ Support This Project

100 chapters. Every one written to the same depth as a paid architecture course. No paywalls, no newsletter gates, no upsells — just the content.

If this handbook has saved you time, helped you make a better architecture decision, or given you something concrete to bring into a review — consider buying me a coffee. It directly funds the next part.

[![Buy Me A Coffee](https://img.buymeacoffee.com/button-api/?text=Buy%20me%20a%20coffee&emoji=&slug=manishpcp&button_colour=FFDD00&font_colour=000000&font_family=Cookie&outline_colour=000000&coffee_colour=ffffff)](https://www.buymeacoffee.com/manishpcp)

---

*Built with ❤️ for architects who prefer decisions over opinions.*
