# Part 9 — Industry-Specific Architectures

**Chapters 67–76 · Status: 🔒 Planned**

← [Back to Main README](../Readme.md) · [← Part 8](../part-08-enterprise-application-architectures/README.md) · [Part 10 →](../part-10-modern-architecture-patterns/README.md)

---

## What This Part Covers

Every industry has requirements that generic cloud architecture guides don't address — and discovering them during a compliance audit or a customer security review is expensive. A banking workload that doesn't account for PCI-DSS network segmentation. A healthcare system that stores PHI without a HIPAA Business Associate Agreement in place. A government workload that needs FedRAMP authorization but was built on services that aren't in scope.

This part covers production-grade reference architectures for ten regulated and high-stakes industries — each chapter built around the specific compliance frameworks, data sensitivity requirements, integration patterns, and operational constraints that define that industry's cloud architecture. The patterns here are not generic architectures with a compliance section bolted on. They are architectures designed from the ground up around the industry's actual requirements.

---

## Which Chapter Do You Need?

| If your industry is... | Read this chapter |
|---|---|
| Banking, payments, or financial services | [Ch. 67 — Banking](./chapter-67-banking.md) |
| Healthcare, hospitals, or health tech | [Ch. 68 — Healthcare](./chapter-68-healthcare.md) |
| Insurance (P&C, life, or health) | [Ch. 69 — Insurance](./chapter-69-insurance.md) |
| Federal, state, or local government | [Ch. 70 — Government](./chapter-70-government.md) |
| Retail, e-commerce, or omnichannel | [Ch. 71 — Retail](./chapter-71-retail.md) |
| Manufacturing, supply chain, or industrial IoT | [Ch. 72 — Manufacturing](./chapter-72-manufacturing.md) |
| Telecommunications or connectivity providers | [Ch. 73 — Telecommunications](./chapter-73-telecommunications.md) |
| Media, streaming, or content delivery | [Ch. 74 — Media Streaming](./chapter-74-media-streaming.md) |
| Education, EdTech, or learning platforms | [Ch. 75 — Education](./chapter-75-education.md) |
| Gaming, live services, or interactive entertainment | [Ch. 76 — Gaming](./chapter-76-gaming.md) |

---

## Chapters at a Glance

### Chapter 67 — Banking
**[Read →](./chapter-67-banking.md)** · 🔒 Coming soon

Architecture for banking and financial services workloads — core banking integration, payment processing, fraud detection, and the PCI-DSS, SOX, and GLBA compliance controls that every financial services architecture must demonstrate to regulators and auditors.

**Key topics:** PCI-DSS network segmentation (CDE isolation), SOX audit trail requirements, GLBA data protection controls, core banking system integration patterns, real-time payment processing with sub-100ms latency requirements, fraud detection with ML, financial data encryption (field-level for PAN/CVV), AWS GovCloud considerations, immutable audit logging, disaster recovery for financial systems (RTO/RPO commitments to regulators)

---

### Chapter 68 — Healthcare
**[Read →](./chapter-68-healthcare.md)** · 🔒 Coming soon

Architecture for healthcare workloads handling Protected Health Information (PHI) — HIPAA Security Rule compliance, HL7 FHIR API design, EHR integration, medical imaging storage, and the Business Associate Agreement (BAA) requirements that govern every AWS service used in a HIPAA-eligible workload.

**Key topics:** HIPAA-eligible AWS services and BAA scope, PHI encryption at rest and in transit, minimum necessary access for PHI, HL7 FHIR R4 API with Amazon HealthLake, EHR integration (Epic, Cerner) patterns, DICOM medical imaging with Amazon HealthImaging, de-identification pipelines, audit logging for PHI access, breach notification requirements, HITRUST CSF alignment

---

### Chapter 69 — Insurance
**[Read →](./chapter-69-insurance.md)** · 🔒 Coming soon

Architecture for insurance workloads — policy management, claims processing, underwriting automation, and the actuarial data platforms that support risk modeling, with the state-level regulatory compliance and data residency requirements that vary across jurisdictions.

**Key topics:** Policy lifecycle management, claims processing workflow automation, underwriting ML models, actuarial data platform design, telematics data ingestion for usage-based insurance, fraud detection for claims, state regulatory compliance and data residency, reinsurance data exchange, document management for policy documents and claims evidence, NAIC model law compliance

---

### Chapter 70 — Government
**[Read →](./chapter-70-government.md)** · 🔒 Coming soon

Architecture for federal, state, and local government workloads — FedRAMP authorization, FISMA compliance, AWS GovCloud deployment, and the specific security controls, audit requirements, and procurement constraints that govern government cloud deployments.

**Key topics:** FedRAMP authorization process and control families, FISMA compliance (NIST SP 800-53), AWS GovCloud (US) for controlled unclassified information (CUI), IL2/IL4/IL5 impact level requirements, CJIS compliance for criminal justice data, PIV/CAC authentication, continuous monitoring (ConMon) requirements, ATO (Authority to Operate) documentation, FedRAMP Marketplace service selection

---

### Chapter 71 — Retail
**[Read →](./chapter-71-retail.md)** · 🔒 Coming soon

Architecture for retail and e-commerce workloads — product catalog, inventory management, order processing, and the peak traffic handling (Black Friday, Cyber Monday) that separates a retail architecture that works from one that becomes a news story.

**Key topics:** Product catalog with ElasticSearch/OpenSearch, inventory management with DynamoDB, order processing with Step Functions, peak traffic handling (10–50x baseline), PCI-DSS for payment processing, personalization with ML recommendations, omnichannel inventory synchronization, returns processing, loyalty program architecture, CDN strategy for product images and static assets

---

### Chapter 72 — Manufacturing
**[Read →](./chapter-72-manufacturing.md)** · 🔒 Coming soon

Architecture for manufacturing workloads — industrial IoT data ingestion from factory floor sensors, predictive maintenance ML models, supply chain visibility, and the OT/IT convergence patterns that connect operational technology (PLCs, SCADA) to cloud analytics.

**Key topics:** Industrial IoT with AWS IoT Core and Greengrass, time-series data with Amazon Timestream, predictive maintenance ML with SageMaker, supply chain visibility with event-driven architecture, OT/IT network segmentation, edge computing for low-latency factory floor decisions, digital twin architecture, MES (Manufacturing Execution System) integration, ERP integration for production data

---

### Chapter 73 — Telecommunications
**[Read →](./chapter-73-telecommunications.md)** · 🔒 Coming soon

Architecture for telecommunications workloads — network function virtualization, subscriber data management, real-time billing and mediation, and the ultra-low latency requirements of 5G core network functions deployed on AWS.

**Key topics:** Network function virtualization (NFV) on AWS, AWS Wavelength for ultra-low latency 5G edge compute, subscriber data management with DynamoDB, real-time billing and mediation pipelines, CDR (Call Detail Record) processing at scale, MVNO architecture, number portability integration, CALEA lawful intercept compliance, carrier-grade availability requirements (five nines)

---

### Chapter 74 — Media Streaming
**[Read →](./chapter-74-media-streaming.md)** · 🔒 Coming soon

Architecture for media and streaming workloads — video ingestion, transcoding, adaptive bitrate delivery, live streaming, and the content protection and rights management systems that media companies require for premium content distribution.

**Key topics:** AWS Elemental MediaConvert for VOD transcoding, AWS Elemental MediaLive for live streaming, Amazon CloudFront for global video delivery, adaptive bitrate streaming (HLS, DASH), DRM integration (Widevine, FairPlay, PlayReady), content origin architecture, thumbnail generation at scale, video analytics, CDN cost optimization for high-bandwidth content, latency targets for live vs. VOD

---

### Chapter 75 — Education
**[Read →](./chapter-75-education.md)** · 🔒 Coming soon

Architecture for education and EdTech workloads — learning management systems, video lecture delivery, student data platforms, and the FERPA compliance requirements that govern student data in US educational institutions.

**Key topics:** FERPA compliance for student data (PII protection, access controls, audit logging), LMS architecture (Canvas, Moodle integration patterns), video lecture delivery with CloudFront, live virtual classroom with Amazon Chime SDK, student data platform design, learning analytics with Kinesis and Redshift, accessibility compliance (WCAG 2.1), peak load handling for exam periods, multi-institution data isolation

---

### Chapter 76 — Gaming
**[Read →](./chapter-76-gaming.md)** · 🔒 Coming soon

Architecture for online gaming workloads — game server hosting, matchmaking, leaderboards, player data management, and the live service operations that keep a game running reliably for millions of concurrent players across global regions.

**Key topics:** Game server hosting with Amazon GameLift, matchmaking with FlexMatch, global leaderboards with DynamoDB and ElastiCache, player session management, in-game economy and virtual currency architecture, anti-cheat telemetry processing, live ops event architecture, game analytics pipeline, multi-region player data replication, DDoS protection for game servers, cost model for variable player populations

---

## Compliance Frameworks by Chapter

| Framework | Relevant Chapters |
|-----------|-----------------|
| PCI-DSS | 67, 71 |
| HIPAA | 68 |
| HITRUST CSF | 68 |
| SOX | 67 |
| GLBA | 67 |
| FedRAMP | 70 |
| FISMA / NIST 800-53 | 70 |
| FERPA | 75 |
| GDPR | 68, 69, 73, 75 |
| CJIS | 70 |

---

## AWS Specialized Services by Industry

| Service | Industry |
|---------|---------|
| Amazon HealthLake | Healthcare |
| Amazon HealthImaging | Healthcare |
| AWS GovCloud | Government |
| AWS Wavelength | Telecommunications |
| Amazon GameLift | Gaming |
| AWS Elemental MediaConvert | Media |
| AWS Elemental MediaLive | Media |
| AWS IoT Core / Greengrass | Manufacturing |
| Amazon Timestream | Manufacturing |

---

← [Back to Main README](../Readme.md) · [← Part 8](../part-08-enterprise-application-architectures/README.md) · [Part 10 →](../part-10-modern-architecture-patterns/README.md)
