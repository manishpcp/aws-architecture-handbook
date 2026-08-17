# Part 11 — Security Reference Architectures

**Chapters 87–94 · Status: 🔒 Planned**

← [Back to Main README](../Readme.md) · [← Part 10](../part-10-modern-architecture-patterns/README.md) · [Part 12 →](../part-12-resilience-operations-and-cost/README.md)

---

## What This Part Covers

Security is not a layer you add to an architecture after it's built. By the time a system is in production, the decisions that determine its security posture — IAM design, network topology, encryption key ownership, secrets management, logging strategy — have already been made. Retrofitting security controls onto a live system is always more expensive, more disruptive, and less complete than building them in from the start.

This part covers eight security reference architectures — each one a complete, production-grade security design for a specific security domain. Zero trust network architecture, multi-account security governance, identity and access management at enterprise scale, secrets management, encryption strategy, security operations center (SOC) architecture, threat detection, and compliance automation. Every chapter is written for the architect who needs to make these decisions correctly the first time, not the security consultant who audits them afterward.

---

## Which Chapter Do You Need?

| If your security challenge is... | Read this chapter |
|---|---|
| Eliminating implicit trust based on network location | [Ch. 87 — Zero Trust](./chapter-87-zero-trust.md) |
| Governing security across a multi-account AWS organization | [Ch. 88 — Multi-Account Security](./chapter-88-multi-account-security.md) |
| Managing human and workload identity at enterprise scale | [Ch. 89 — IAM Identity Center](./chapter-89-iam-identity-center.md) |
| Storing, rotating, and auditing secrets across a large system | [Ch. 90 — Secrets Management](./chapter-90-secrets-management.md) |
| Designing an encryption strategy that satisfies compliance and key control requirements | [Ch. 91 — Encryption](./chapter-91-encryption.md) |
| Building a security operations center on AWS | [Ch. 92 — SOC Operations](./chapter-92-soc-operations.md) |
| Detecting threats across an AWS organization automatically | [Ch. 93 — Threat Detection](./chapter-93-threat-detection.md) |
| Automating compliance evidence collection and control monitoring | [Ch. 94 — Compliance](./chapter-94-compliance.md) |

---

## Chapters at a Glance

### Chapter 87 — Zero Trust
**[Read →](./chapter-87-zero-trust.md)** · 🔒 Coming soon

Zero trust architecture on AWS — eliminating the assumption that anything inside the network perimeter is trustworthy, and replacing it with continuous verification of every request regardless of origin, using IAM authentication, mTLS between services, and least-privilege access enforced at every layer.

**Key topics:** Zero trust principles (verify explicitly, least privilege, assume breach), IAM-authenticated service-to-service calls, mTLS with AWS Private CA, VPC endpoint policies for AWS service access, AWS Verified Access for application access without VPN, identity-aware proxy patterns, micro-segmentation with security groups, continuous authorization vs. perimeter-based trust, zero trust maturity model

---

### Chapter 88 — Multi-Account Security
**[Read →](./chapter-88-multi-account-security.md)** · 🔒 Coming soon

Security governance across an AWS Organizations multi-account structure — Service Control Policies as non-overridable guardrails, centralized security tooling in a dedicated security account, and the account vending machine pattern that ensures every new account starts with the correct security baseline.

**Key topics:** AWS Organizations and Service Control Policies (SCPs) as guardrails, security account structure (management, security tooling, logging, audit), centralized GuardDuty and Security Hub across the organization, AWS Control Tower for account vending with security baseline, detective controls vs. preventive controls, cross-account CloudTrail aggregation, security finding aggregation and triage, account-level blast radius isolation

---

### Chapter 89 — IAM Identity Center
**[Read →](./chapter-89-iam-identity-center.md)** · 🔒 Coming soon

IAM Identity Center (formerly AWS SSO) as the enterprise identity layer for AWS — federating corporate identity providers, managing permission sets across hundreds of accounts, and providing the just-in-time access and session management that enterprise security teams require.

**Key topics:** IAM Identity Center architecture and identity source options (Active Directory, external IdP, built-in), permission sets and account assignments, attribute-based access control (ABAC) with identity provider attributes, MFA enforcement, session duration and re-authentication, just-in-time access with temporary elevated permissions, access reviews and certification, IAM Identity Center vs. IAM roles with federation, audit logging

---

### Chapter 90 — Secrets Management
**[Read →](./chapter-90-secrets-management.md)** · 🔒 Coming soon

A production secrets management architecture — storing, rotating, auditing, and distributing secrets (database credentials, API keys, certificates) across a multi-account, multi-service environment, with the rotation automation and access controls that eliminate static long-lived credentials entirely.

**Key topics:** AWS Secrets Manager for credential storage and rotation, rotation Lambda function design and testing, cross-account secret access patterns, Secrets Manager vs. Parameter Store decision framework, secret versioning and staged rotation, secrets in CI/CD pipelines (OIDC federation vs. stored secrets), certificate management with ACM and Private CA, secrets scanning in code repositories, audit logging for secret access

---

### Chapter 91 — Encryption
**[Read →](./chapter-91-encryption.md)** · 🔒 Coming soon

A comprehensive encryption architecture — data at rest, data in transit, and data in use — with the KMS key hierarchy, customer-managed key strategy, and envelope encryption patterns that satisfy compliance requirements for demonstrable key control without creating operational complexity that breaks systems.

**Key topics:** KMS key hierarchy (AWS-managed vs. customer-managed vs. customer-provided), envelope encryption pattern, key policies and grants, cross-account key access, automatic key rotation, KMS key deletion and recovery, encryption at rest for every AWS data service, TLS configuration (minimum versions, cipher suites), certificate management lifecycle, field-level encryption for sensitive data, AWS CloudHSM for FIPS 140-2 Level 3 requirements

---

### Chapter 92 — SOC Operations
**[Read →](./chapter-92-soc-operations.md)** · 🔒 Coming soon

Building a Security Operations Center on AWS — the tooling, processes, and automation that enable a security team to detect, investigate, and respond to security incidents across a large AWS environment, with the SIEM integration, playbook automation, and metrics that measure SOC effectiveness.

**Key topics:** Security Hub as the central finding aggregation layer, GuardDuty for threat detection, CloudTrail for API audit, VPC Flow Logs for network visibility, AWS Security Lake for centralized security data, SIEM integration (Splunk, Microsoft Sentinel), automated response with Security Hub custom actions and Lambda, incident response playbooks, SOC metrics (MTTD, MTTR), threat intelligence integration

---

### Chapter 93 — Threat Detection
**[Read →](./chapter-93-threat-detection.md)** · 🔒 Coming soon

Automated threat detection across an AWS organization — GuardDuty for anomaly-based detection, Inspector for vulnerability scanning, Macie for sensitive data discovery, and the custom detection rules and response automation that extend AWS-native detection to organization-specific threat models.

**Key topics:** GuardDuty finding types and severity triage, GuardDuty Malware Protection for EC2 and S3, Amazon Inspector for EC2 and container CVE scanning, Amazon Macie for S3 sensitive data discovery, custom threat detection with CloudWatch Logs metric filters and EventBridge rules, automated response to GuardDuty findings, threat intelligence feeds, detection coverage gaps and compensating controls, false positive management

---

### Chapter 94 — Compliance
**[Read →](./chapter-94-compliance.md)** · 🔒 Coming soon

Automating compliance evidence collection, control monitoring, and audit reporting across an AWS environment — so that SOC 2, PCI-DSS, HIPAA, and ISO 27001 audits are a continuous, automated process rather than a quarterly scramble to collect screenshots.

**Key topics:** AWS Config rules for continuous compliance monitoring, AWS Security Hub standards (CIS Foundations, AWS Foundational Security Best Practices, PCI-DSS), AWS Audit Manager for automated evidence collection, compliance-as-code with Config conformance packs, automated remediation for common violations, compliance dashboard design, evidence retention strategy, third-party auditor access patterns, compliance drift alerting

---

## AWS Security Services Reference

| Service | Primary Purpose | Chapters |
|---------|----------------|---------|
| AWS IAM | Identity and access control | 87, 88, 89 |
| IAM Identity Center | Enterprise SSO and permission management | 89 |
| AWS KMS | Encryption key management | 91 |
| AWS CloudHSM | FIPS 140-2 Level 3 key storage | 91 |
| AWS Secrets Manager | Secret storage and rotation | 90 |
| AWS Certificate Manager | TLS certificate lifecycle | 90, 91 |
| AWS Private CA | Internal PKI | 87, 90 |
| Amazon GuardDuty | Threat detection | 88, 92, 93 |
| Amazon Inspector | Vulnerability scanning | 93 |
| Amazon Macie | Sensitive data discovery | 93 |
| AWS Security Hub | Finding aggregation and compliance | 88, 92, 93, 94 |
| AWS Config | Configuration compliance | 88, 94 |
| AWS Audit Manager | Compliance evidence collection | 94 |
| AWS CloudTrail | API audit logging | 88, 92, 93 |
| AWS Security Lake | Centralized security data lake | 92 |
| AWS Verified Access | Zero trust application access | 87 |
| AWS Organizations / SCPs | Multi-account governance | 88 |
| AWS Control Tower | Account vending with security baseline | 88 |

---

## Security Controls by Compliance Framework

| Control Area | SOC 2 | PCI-DSS | HIPAA | ISO 27001 | Chapters |
|---|---|---|---|---|---|
| Encryption at rest | ✓ | ✓ | ✓ | ✓ | 91 |
| Encryption in transit | ✓ | ✓ | ✓ | ✓ | 87, 91 |
| Access control / least privilege | ✓ | ✓ | ✓ | ✓ | 87, 89 |
| Audit logging | ✓ | ✓ | ✓ | ✓ | 92 |
| Secrets management | ✓ | ✓ | ✓ | ✓ | 90 |
| Vulnerability management | ✓ | ✓ | ✓ | ✓ | 93 |
| Threat detection | ✓ | ✓ | ✓ | ✓ | 93 |
| Incident response | ✓ | ✓ | ✓ | ✓ | 92 |
| Network segmentation | ✓ | ✓ | ✓ | ✓ | 87, 88 |
| Compliance monitoring | ✓ | ✓ | ✓ | ✓ | 94 |

---

← [Back to Main README](../Readme.md) · [← Part 10](../part-10-modern-architecture-patterns/README.md) · [Part 12 →](../part-12-resilience-operations-and-cost/README.md)
