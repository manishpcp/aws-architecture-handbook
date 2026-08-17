# Part 12 — Resilience, Operations & Cost

**Chapters 95–100 · Status: 🔒 Planned**

← [Back to Main README](../Readme.md) · [← Part 11](../part-11-security-reference-architectures/README.md)

---

## What This Part Covers

This is the final part of the handbook — and in many ways the most important one for organizations that have already built something and now need to operate it reliably, observe it accurately, control its cost, and evolve it without breaking it.

The six chapters in this part cover the cross-cutting concerns that apply to every architecture in this book: disaster recovery that has actually been tested, observability that surfaces real customer impact rather than just infrastructure metrics, FinOps discipline that makes cloud spend predictable and accountable, multi-region active-active for the workloads that genuinely need it, a reference landing zone that gives a multi-account AWS organization the right foundation from day one, and a forward-looking chapter on how to build cloud architecture that ages well as AWS services, organizational requirements, and technology patterns continue to evolve.

If you have read any other part of this book, this part is where the operational and financial discipline that makes those architectures sustainable over time lives.

---

## Which Chapter Do You Need?

| If your challenge is... | Read this chapter |
|---|---|
| Designing and testing a disaster recovery strategy | [Ch. 95 — Disaster Recovery](./chapter-95-disaster-recovery.md) |
| Building an observability platform that reflects real customer experience | [Ch. 96 — Observability Platform](./chapter-96-observability-platform.md) |
| Making cloud spend predictable, accountable, and optimized | [Ch. 97 — FinOps Architecture](./chapter-97-finops-architecture.md) |
| Running a workload that must survive a full regional AWS failure | [Ch. 98 — Multi-Region Active-Active](./chapter-98-multi-region-active-active.md) |
| Building the foundational AWS account structure for an organization | [Ch. 99 — Reference Landing Zone](./chapter-99-reference-landing-zone.md) |
| Designing architecture that remains maintainable and adaptable over time | [Ch. 100 — Future-Proof Cloud Architecture](./chapter-100-future-proof-cloud-architecture.md) |

---

## Chapters at a Glance

### Chapter 95 — Disaster Recovery
**[Read →](./chapter-95-disaster-recovery.md)** · 🔒 Coming soon

A complete disaster recovery architecture — covering all four DR strategy tiers (Backup and Restore, Pilot Light, Warm Standby, Multi-Site Active-Active), the AWS services that implement each tier, and the testing discipline that turns a documented RTO/RPO into a demonstrated, auditable capability rather than an untested hypothesis.

**Key topics:** DR strategy tier selection (RTO/RPO vs. cost trade-offs), AWS Backup for cross-region backup orchestration, Aurora Global Database for data tier DR, Route 53 health-check-based failover, CloudFormation/Terraform for infrastructure recreation in a DR region, DR testing methodology (tabletop, partial, full failover), RTO/RPO measurement and evidence collection, DR runbook design, AWS Elastic Disaster Recovery (DRS) for server replication, compliance requirements for DR testing frequency

---

### Chapter 96 — Observability Platform
**[Read →](./chapter-96-observability-platform.md)** · 🔒 Coming soon

A production observability platform built on the three pillars — metrics, logs, and traces — with the SLI/SLO framework, error budget tracking, and burn-rate alerting that connects infrastructure signals to real customer experience, and the centralized architecture that makes observability data available across a multi-account organization.

**Key topics:** CloudWatch as the metrics and logs backbone, AWS X-Ray for distributed tracing, CloudWatch Synthetics for outside-in availability monitoring, SLI definition (what actually measures customer experience), SLO target setting and error budget calculation, multi-window burn-rate alerting (Google SRE pattern), centralized observability account architecture, Amazon Managed Grafana for dashboards, Amazon Managed Service for Prometheus for Kubernetes metrics, OpenTelemetry for vendor-neutral instrumentation

---

### Chapter 97 — FinOps Architecture
**[Read →](./chapter-97-finops-architecture.md)** · 🔒 Coming soon

A FinOps architecture that makes cloud spend visible, accountable, and optimized — with the tagging strategy, cost allocation model, anomaly detection, rightsizing discipline, and commitment purchasing framework that turns AWS Cost Explorer from a bill into a management tool.

**Key topics:** Tagging strategy and enforcement (SCPs for required tags), cost allocation with AWS Cost Explorer and Cost and Usage Report (CUR), AWS Budgets for proactive spend alerts, Cost Anomaly Detection for unexpected spend spikes, rightsizing with AWS Compute Optimizer, Reserved Instance and Savings Plan strategy (what to commit, what to leave On-Demand), Spot Instance architecture for cost reduction, unit economics (cost per customer, cost per transaction), FinOps team model and accountability structure, showback vs. chargeback

---

### Chapter 98 — Multi-Region Active-Active
**[Read →](./chapter-98-multi-region-active-active.md)** · 🔒 Coming soon

The architecture for workloads that must survive a complete AWS regional failure with near-zero RTO and RPO — running active traffic in multiple regions simultaneously, with the data replication, conflict resolution, and global traffic routing that makes multi-region active-active operationally viable rather than just theoretically possible.

**Key topics:** Global traffic routing with Route 53 latency-based and failover routing, Aurora Global Database for sub-second cross-region replication, DynamoDB Global Tables for multi-region key-value data, S3 Cross-Region Replication, regional deployment with identical Terraform modules, conflict resolution for concurrent writes, health-check-driven regional failover, data residency constraints on multi-region design, cost model (roughly 2x single-region), when multi-region active-active is actually justified vs. over-engineered

---

### Chapter 99 — Reference Landing Zone
**[Read →](./chapter-99-reference-landing-zone.md)** · 🔒 Coming soon

The foundational AWS account structure for an enterprise organization — the account hierarchy, organizational units, Service Control Policies, centralized logging, security tooling, and network architecture that every workload account inherits from day one, built on AWS Control Tower with customizations for enterprise requirements.

**Key topics:** AWS Organizations account hierarchy design, OU structure (security, infrastructure, workloads, sandbox), AWS Control Tower for account vending with guardrails, Account Factory for Terraform (AFT) for IaC-driven account provisioning, centralized logging account, security tooling account, network hub account, management account lockdown, SCP guardrails (deny root usage, require MFA, enforce encryption, restrict regions), identity integration with IAM Identity Center, landing zone customization and drift detection

---

### Chapter 100 — Future-Proof Cloud Architecture
**[Read →](./chapter-100-future-proof-cloud-architecture.md)** · 🔒 Coming soon

The final chapter — a synthesis of the principles, patterns, and decision frameworks from all 100 chapters into a forward-looking guide for building cloud architecture that remains maintainable, adaptable, and cost-effective as AWS services evolve, organizational requirements change, and technology patterns shift.

**Key topics:** Architecture evolution triggers (when to re-architect vs. optimize), avoiding premature optimization and over-engineering, managing technical debt in cloud infrastructure, keeping Terraform modules and AWS provider versions current, organizational architecture review cadence, architecture fitness functions for continuous validation, the role of AI-assisted architecture review, building an architecture practice within an engineering organization, the 10 decisions that most determine long-term cloud architecture health

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| AWS Backup | 95 |
| AWS Elastic Disaster Recovery | 95 |
| Amazon Route 53 | 95, 98 |
| Aurora Global Database | 95, 98 |
| DynamoDB Global Tables | 98 |
| Amazon CloudWatch | 96 |
| AWS X-Ray | 96 |
| CloudWatch Synthetics | 96 |
| Amazon Managed Grafana | 96 |
| Amazon Managed Service for Prometheus | 96 |
| AWS Cost Explorer | 97 |
| AWS Budgets | 97 |
| AWS Compute Optimizer | 97 |
| AWS Cost and Usage Report | 97 |
| AWS Organizations | 99 |
| AWS Control Tower | 99 |
| Account Factory for Terraform | 99 |
| IAM Identity Center | 99 |

---

## The Four DR Strategy Tiers at a Glance

| Strategy | RTO | RPO | Relative Cost | Best For |
|----------|-----|-----|--------------|---------|
| Backup and Restore | Hours | Hours | 1x | Internal tools, low-risk workloads |
| Pilot Light | 10s of minutes | Minutes | ~1.3x | Most production workloads |
| Warm Standby | Minutes | Seconds | ~1.7x | Business-critical applications |
| Multi-Region Active-Active | Near-zero | Near-zero | ~2x | 99.99%+ SLA, global latency requirements |

Chapter 95 covers all four tiers in depth. Chapter 98 covers the most complex tier — Multi-Region Active-Active — as a standalone architecture reference.

---

## FinOps Maturity Model

| Level | Characteristics | Chapter 97 Coverage |
|-------|----------------|-------------------|
| Crawl | Basic cost visibility, manual tagging, monthly bill review | Tagging strategy, Cost Explorer setup |
| Walk | Automated anomaly detection, team-level cost allocation, rightsizing program | Budgets, Cost Anomaly Detection, Compute Optimizer |
| Run | Unit economics, commitment purchasing strategy, FinOps as engineering discipline | Savings Plans, Spot architecture, showback/chargeback |

---

## This Is the End — and the Beginning

Chapter 100 closes the handbook, but the architectures in it are starting points, not destinations. Every system in this book will need to evolve — as traffic grows, as compliance requirements change, as AWS releases new services, and as the organization that operates it matures.

The most important thing this handbook can give you is not a specific architecture, but the decision framework to evaluate any architecture — including the ones that don't exist yet.

---

← [Back to Main README](../Readme.md) · [← Part 11](../part-11-security-reference-architectures/README.md)
