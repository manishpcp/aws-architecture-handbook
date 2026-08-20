# Part 2 — Core Infrastructure Architectures

**Chapters 05–14 · Status: ✅ Complete**

← [Back to Main README](../Readme.md) · [← Part 1](../part-01-foundations-of-aws-reference-architectures/README.md)

---

## What This Part Covers

These ten chapters cover the compute and infrastructure patterns that underpin virtually every production AWS workload. They are the patterns you reach for first — before containers, before serverless, before microservices — because they solve the most common class of problem: running a web application, an API, or a background processing system reliably, securely, and cost-effectively on AWS.

Each chapter is a complete, production-grade reference. Not a getting-started guide. Not a diagram with a caption. A full architecture with business requirements, Terraform, security controls, failure scenarios, cost estimates, and the honest trade-offs that most documentation skips.

The ten chapters in this part form a natural progression: from the simplest viable production deployment (a single EC2 instance, Chapter 05) through the standard highly available baseline (Multi-AZ, Chapter 06), to the deployment strategies that make changes safe (blue-green and canary, Chapters 13–14). You do not need to read them in order — each chapter stands alone — but the progression is intentional.

---

## Which Chapter Do You Need?

| If your situation is... | Read this chapter |
|---|---|
| Small internal tool, low traffic, cost is the priority | [Ch. 05 — Single EC2](./chapter-05-single-ec2-production-architecture.md) |
| Production web app, need 99.9%+ availability | [Ch. 06 — Multi-AZ Web App](./chapter-06-highly-available-multi-az-web-application.md) |
| Enterprise app with strict network segmentation requirements | [Ch. 07 — Three-Tier Enterprise](./chapter-07-three-tier-enterprise-architecture.md) |
| Traffic is unpredictable, need elastic scaling | [Ch. 08 — Auto Scaling](./chapter-08-auto-scaling-architecture.md) |
| Multiple teams sharing networking, tooling, or services | [Ch. 09 — Shared Services](./chapter-09-shared-services-architecture.md) |
| Want to eliminate SSH keys and bastion hosts | [Ch. 10 — Bastionless with SSM](./chapter-10-bastionless-infrastructure-with-ssm.md) |
| Need standardized, hardened base images across a fleet | [Ch. 11 — Golden AMI](./chapter-11-golden-ami-architecture.md) |
| Want to eliminate configuration drift entirely | [Ch. 12 — Immutable Infrastructure](./chapter-12-immutable-infrastructure.md) |
| Need zero-downtime deployments with instant rollback | [Ch. 13 — Blue-Green](./chapter-13-blue-green-infrastructure.md) |
| Want to validate changes on a small traffic slice before full rollout | [Ch. 14 — Canary](./chapter-14-canary-infrastructure.md) |

---

## Chapters at a Glance

### Chapter 05 — Single EC2 Production Architecture

**[Read →](./chapter-05-single-ec2-production-architecture.md)**

The deliberately minimal pattern for workloads that don't need — and can't justify — Multi-AZ redundancy. Not a hobbyist setup: a genuinely production-grade single-instance deployment with the same security posture, observability discipline, and disaster recovery rigor as any other system in this book, scoped honestly to a single point of compute.

**Key topics:** EC2 Auto Recovery, AWS Backup with cross-region copy, Systems Manager Session Manager as the sole access path, Pattern A vs. Pattern B deployment, honest 99.5% availability target, `prevent_destroy` Terraform guardrail, growth-ceiling trigger for graduating to Multi-AZ

**Availability target:** 99.5% · **DR strategy:** Backup and Restore · **Estimated cost:** ~$78–$240/month

**Use when:** Internal tools, early-stage SaaS pilots, staging environments, batch workloads, single-customer deployments without an uptime SLA

**Don't use when:** Any workload where a multi-hour outage has direct revenue or safety impact

---

### Chapter 06 — Highly Available Multi-AZ Web Application

**[Read →](./chapter-06-highly-available-multi-az-web-application.md)**

The standard production baseline for any web application or API that needs genuine high availability. Three Availability Zones, Application Load Balancer, Auto Scaling Group, Aurora Multi-AZ, ElastiCache, CloudFront, and WAF — the pattern that most production AWS workloads should start from.

**Key topics:** Multi-AZ ALB and ASG design, Aurora Multi-AZ with read replicas, ElastiCache for session state, CloudFront + WAF edge security, target-tracking Auto Scaling on request count, RDS Proxy for connection pooling, blue-green deployment with automated rollback

**Availability target:** 99.95% · **DR strategy:** Pilot Light (data tier) · **Estimated cost:** ~$365–$2,800/month

**Use when:** Any customer-facing web application or API with a meaningful uptime requirement

**Don't use when:** Workloads requiring 99.99%+ (go multi-region) or genuinely idle/spiky traffic (consider serverless)

---

### Chapter 07 — Three-Tier Enterprise Architecture

**[Read →](./chapter-07-three-tier-enterprise-architecture.md)**

The classic presentation → application → data tier separation, implemented with strict network segmentation, dedicated subnet tiers per AZ, and the security controls required by enterprise compliance regimes. The pattern for organizations where network topology must be demonstrable to an auditor.

**Key topics:** Strict subnet tier separation, security group chaining, network ACLs as a second defense layer, WAF at the presentation tier, database in isolated private subnets with no internet route, compliance control mapping (SOC 2, PCI-DSS, HIPAA)

**Availability target:** 99.95% · **Use when:** Regulated industries, enterprise compliance requirements, organizations with network-topology-based audit requirements

---

### Chapter 08 — Auto Scaling Architecture

**[Read →](./chapter-08-auto-scaling-architecture.md)**

A deep dive into Auto Scaling beyond the basics — target tracking vs. step scaling vs. scheduled scaling, warm pools for reducing scale-out latency, predictive scaling for known traffic patterns, and the specific metrics that actually reflect customer experience rather than just CPU utilization.

**Key topics:** Target tracking on ALBRequestCountPerTarget vs. CPU, step scaling for non-linear load patterns, scheduled scaling for predictable peaks, warm pools to pre-warm instances, predictive scaling, scale-in protection for stateful workloads, lifecycle hooks for graceful shutdown

**Use when:** Traffic variability exceeds what a fixed fleet can absorb cost-effectively, or scale-out latency is a customer-experience concern

---

### Chapter 09 — Shared Services Architecture

**[Read →](./chapter-09-shared-services-architecture.md)**

The pattern for organizations running multiple workloads or teams that need to share networking infrastructure, security tooling, CI/CD pipelines, or platform services without duplicating them per workload. The foundation for a platform engineering function.

**Key topics:** Centralized networking account, shared VPC via Resource Access Manager, centralized logging and security tooling, cross-account IAM patterns, shared CI/CD infrastructure, service catalog for self-service provisioning, cost allocation across consumers

**Use when:** Multiple teams or workloads need shared infrastructure, or a platform team is being established to reduce per-team operational burden

---

### Chapter 10 — Bastionless Infrastructure with SSM

**[Read →](./chapter-10-bastionless-infrastructure-with-ssm.md)**

Eliminating SSH keys and bastion hosts entirely using AWS Systems Manager Session Manager — a pattern that improves security posture, simplifies operations, and provides a better audit trail than traditional SSH-based access, with no open inbound ports required.

**Key topics:** Session Manager setup and VPC endpoint requirements, per-principal session logging to S3 and CloudWatch Logs, port forwarding for database access without a bastion, Run Command for fleet-wide operations, IAM-controlled access replacing key-based access, integration with existing identity providers

**Use when:** Any new production workload — this should be the default access pattern, not an upgrade

---

### Chapter 11 — Golden AMI Architecture

**[Read →](./chapter-11-golden-ami-architecture.md)**

A pipeline-driven approach to building, hardening, testing, and distributing base AMIs across an organization — so every instance launched anywhere in the fleet starts from a known-good, patched, security-hardened baseline rather than a public AMI configured post-boot.

**Key topics:** EC2 Image Builder pipelines, CIS benchmark hardening, Inspector integration for CVE scanning, AMI sharing across accounts via RAM, version tagging and lifecycle management, AMI deprecation and fleet refresh strategy, integration with Auto Scaling launch templates

**Use when:** Organizations running EC2 fleets where patch consistency, security hardening, and audit evidence of base image provenance are requirements

---

### Chapter 12 — Immutable Infrastructure

**[Read →](./chapter-12-immutable-infrastructure.md)**

The principle that infrastructure components are never modified after deployment — they are replaced. No patching in place, no configuration changes on running instances, no SSH-and-fix. Every change produces a new artifact (AMI or container image) deployed via a pipeline, eliminating configuration drift entirely.

**Key topics:** Replace-not-patch discipline, pipeline-built AMIs as the only change mechanism, configuration drift detection and elimination, stateless compute design, EBS volume separation for data persistence across replacements, Terraform `create_before_destroy` lifecycle, immutable deployment patterns

**Use when:** Organizations that have experienced configuration drift incidents, or that need to demonstrate to auditors that running infrastructure matches its declared configuration

---

### Chapter 13 — Blue-Green Infrastructure

**[Read →](./chapter-13-blue-green-infrastructure.md)**

Zero-downtime deployments by maintaining two identical environments (blue and green) and shifting traffic between them — providing an instantaneous rollback path that a rolling update cannot match, and a safe validation window before the old environment is decommissioned.

**Key topics:** ALB listener rule-based traffic shifting, Auto Scaling Group swap pattern, CodeDeploy blue-green for ECS, bake period with automated CloudWatch alarm-triggered rollback, database schema migration strategies compatible with blue-green, cost of running two environments during the transition window

**Use when:** Any workload where deployment-introduced regressions are a real risk and rollback speed matters

---

### Chapter 14 — Canary Infrastructure

**[Read →](./chapter-14-canary-infrastructure.md)**

Progressive traffic shifting — routing a small percentage of real production traffic to a new version before committing to a full rollout. More granular than blue-green, better suited to validating changes against real user behavior rather than synthetic health checks alone.

**Key topics:** Weighted target group routing, Route 53 weighted records for DNS-level canary, CodeDeploy canary deployment configuration, automated rollback on CloudWatch alarm breach during the canary window, canary metrics selection (what signals actually indicate a bad deployment), feature flags as a complementary pattern

**Use when:** High-traffic applications where even a brief full-fleet bad deployment would have significant customer impact, or where synthetic health checks are insufficient to validate a change

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| Amazon EC2 | 05, 06, 07, 08, 11, 12, 13, 14 |
| Application Load Balancer | 05, 06, 07, 08, 13, 14 |
| Auto Scaling Groups | 06, 07, 08, 13, 14 |
| Amazon Aurora | 06, 07 |
| Amazon ElastiCache | 06, 07 |
| Amazon CloudFront + WAF | 06, 07 |
| AWS Systems Manager | 05, 09, 10, 11, 12 |
| EC2 Image Builder | 11, 12 |
| AWS Backup | 05 |
| AWS CodeDeploy | 13, 14 |
| Amazon Route 53 | 06, 07, 14 |
| AWS KMS | 05, 06, 07 |
| AWS Secrets Manager | 05, 06, 07 |
| Amazon CloudWatch | 05, 06, 07, 08, 13, 14 |
| AWS CloudTrail | 05, 06, 07, 10 |

---

## Cost Reference

| Chapter | Pattern | Estimated Monthly Cost |
|---------|---------|----------------------|
| 05 | Single EC2 (minimal) | ~$78 |
| 05 | Single EC2 (moderate) | ~$240 |
| 06 | Multi-AZ Web App (small) | ~$365 |
| 06 | Multi-AZ Web App (medium) | ~$2,800 |
| 06 | Multi-AZ Web App (enterprise) | ~$16,500+ |

> All figures are illustrative for us-east-1. Validate against the [AWS Pricing Calculator](https://calculator.aws) before presenting to stakeholders.

---

← [Back to Main README](../Readme.md) · [← Part 1](../part-01-foundations-of-aws-reference-architectures/README.md) · [Part 3 →](../part-03-network-architectures/README.md)
