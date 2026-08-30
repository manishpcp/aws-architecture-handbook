# Part IX – Industry-Specific Architectures

# Chapter 75 — Education

*AWS Reference Architecture: Multi-Tenant Digital Learning Platform*

---

# 1. Executive Summary

## The Business Problem

Educational institutions and EdTech companies face a delivery problem that looks simple from the outside and is genuinely hard underneath.

- Students expect a consumer-grade experience: instant page loads, smooth video, no downtime during a live class.
- Institutions expect FERPA-grade data protection, auditable access logs, and predictable per-student costs.
- Faculty expect a system that works identically whether 200 students log in or 200,000 log in during the first week of a semester.
- Traffic is not steady. It is brutally spiky: enrollment week, assignment deadlines at 11:58 PM, final exam windows, and the first ten minutes of every scheduled live lecture.

A single-region, single-database, monolithic LMS (Learning Management System) built for a few thousand users will not survive this pattern. It will either fall over during finals week or bleed money by being permanently over-provisioned for a peak that only happens twelve times a year.

This chapter designs a **Multi-Tenant Digital Learning Platform** — an architecture pattern that generalizes across:

- K-12 school district portals
- Higher-education LMS platforms (course delivery, grading, assignments)
- Corporate/professional certification training platforms
- MOOC-style (Massive Open Online Course) providers
- Tutoring and assessment/proctoring platforms

## Architecture Objective

The objective is to build a platform that can:

1. Serve static course content (video lectures, PDFs, slides) at low latency to a globally distributed student base.
2. Run a stateless, horizontally scalable application tier for course logic, grading, discussion forums, and assignment submission.
3. Handle bursty, synchronous workloads — live class sessions, exam windows, grade-release events — without manual intervention.
4. Isolate tenant data (school districts, universities, corporate customers) while sharing infrastructure cost efficiently.
5. Protect student data in line with FERPA (Family Educational Rights and Privacy Act), COPPA (Children's Online Privacy Protection Act) where K-12 minors are involved, and, for global platforms, GDPR.
6. Support asynchronous, event-driven workflows: auto-grading, plagiarism scanning, video transcoding, notification delivery.
7. Provide institutions with usage and cost visibility, since many EdTech contracts are billed per-student or per-district.

## Why Organizations Adopt This Architecture

- **Enrollment-driven bursts are structurally unavoidable.** A district doesn't choose to have 40,000 students log in between 8:00 AM and 8:15 AM — that's when the school day starts. The architecture has to absorb the burst, not fight it.
- **Video is the dominant cost and complexity driver.** Recorded lectures, live classes, and screen-recorded tutorials require transcoding, adaptive bitrate streaming, and global edge delivery. Building this in-house from scratch is rarely justified.
- **Compliance is not optional.** Any platform touching K-12 student records in the United States must be FERPA-aligned by contract, not just by good intention. Getting this wrong ends procurement relationships immediately.
- **Multi-tenancy at scale requires deliberate isolation design.** A single school's data breach must never expose another school's data. This has to be designed at the data layer, not bolted on later.
- **Cost has to scale with revenue.** Most EdTech contracts are priced per student, per seat, or per district. Infrastructure cost that doesn't scale down between semesters erodes margin badly.

## Major Business Benefits

| Benefit | Description |
|---|---|
| Elastic capacity | Auto Scaling and serverless compute absorb enrollment-week and exam-week spikes without manual provisioning. |
| Global content delivery | CloudFront + regional edge caches deliver lecture video and course assets with low latency worldwide. |
| Compliance-ready foundation | Encryption, access logging, and data residency controls map directly to FERPA/COPPA/GDPR obligations. |
| Multi-tenant cost efficiency | Shared infrastructure with strict logical isolation reduces per-tenant infrastructure cost versus dedicated stacks. |
| Faster feature velocity | Event-driven, decoupled services let grading, notifications, and analytics evolve independently of the core LMS. |
| Predictable unit economics | Serverless and consumption-based services align infrastructure spend with actual student activity. |

## Typical Enterprise Scenarios

- A state-wide K-12 platform serving 300 school districts, each with independent rostering data, but sharing one platform.
- A university consolidating dozens of departmental Moodle/Canvas-style instances into a single managed LMS.
- A corporate learning and development platform delivering compliance training to 50,000 employees across a Fortune 500 company.
- An EdTech vendor selling a white-labeled assessment platform to multiple universities, each requiring its own branding, SSO, and grading policy.
- A MOOC provider running live cohort-based courses with synchronous video sessions for tens of thousands of concurrent learners.

> **Note:** This chapter treats "Education" broadly as a *vertical pattern* — the same architecture applies whether the tenant is a school district, a university department, or a corporate L&D team. What changes between deployments is tenancy configuration, compliance scope, and content type — not the underlying architecture.

---

# 2. Business Requirements

## Business Drivers

- Reduce infrastructure cost per active student.
- Guarantee uptime during high-stakes windows: exams, grade submission deadlines, live classes.
- Meet procurement/compliance requirements (FERPA, COPPA, state-level student data privacy laws, SOC 2).
- Support rapid onboarding of new institutional tenants without re-architecture.
- Enable data-driven product decisions through engagement analytics without compromising privacy.

## Functional Requirements

- Student and instructor authentication, including institutional SSO (SAML 2.0 / OIDC).
- Course catalog, enrollment, and roster management.
- Assignment submission, automated and manual grading.
- Video lecture delivery: on-demand (VOD) and live streaming.
- Discussion forums and messaging.
- Notifications: email, SMS, push, in-app.
- Gradebook and transcript generation.
- Reporting and analytics dashboards for administrators.
- Content authoring and versioning for instructors.
- Integration with third-party tools via LTI (Learning Tools Interoperability) where applicable.

## Non-Functional Requirements

- **Scalability:** Support 10x traffic spikes within minutes without manual intervention.
- **Availability:** 99.9%+ during academic terms; higher targets during scheduled exam windows.
- **Latency:** Sub-200ms API response time at p95 for core application interactions; video start time under 2 seconds globally.
- **Data isolation:** Strict tenant-level data segregation, enforced at the database and application layers.
- **Auditability:** Every access to student records must be logged and retrievable for compliance audits.
- **Data residency:** Some institutional contracts require data to remain within a specific country or region.

## Scalability Goals

| Dimension | Baseline | Peak | Peak Trigger |
|---|---|---|---|
| Concurrent users | 5,000 | 150,000+ | Semester start, exam week |
| API requests/sec | 500 | 20,000+ | Assignment deadline (last 15 minutes) |
| Concurrent live video sessions | 50 | 5,000+ | Scheduled live lectures |
| Assignment submissions/minute | 100 | 10,000+ | Deadline windows |

## Availability Requirements

- 99.9% platform availability (≈ 8.7 hours downtime/year) as a baseline SLA for most institutional contracts.
- 99.95%+ during defined "critical windows" (final exams, standardized testing periods) — often contractually enforced with financial penalties.
- Planned maintenance windows must avoid academic calendar peak periods; this has direct architectural implications for deployment strategy (zero-downtime deploys are mandatory, not optional).

## Latency Requirements

- Web/API interactions: p95 under 200ms, p99 under 500ms.
- Video start latency: under 2 seconds for VOD, under 4 seconds for live stream join.
- Real-time features (live polls, chat during lectures): under 300ms round trip.

## Compliance Requirements

- **FERPA** — governs access to and disclosure of U.S. student education records; applies to any institution receiving federal funding, and by extension to their vendors.
- **COPPA** — applies when the platform serves children under 13, common in K-12 deployments; requires parental consent mechanisms and strict limits on data collection.
- **State student privacy laws** — many U.S. states (e.g., California's SOPIPA) impose additional restrictions on how student data can be used, particularly for advertising.
- **GDPR** — applies where the platform serves EU residents, requiring data subject rights (access, erasure, portability) and lawful basis for processing.
- **SOC 2 Type II** — commonly required by institutional procurement as evidence of operational security controls.
- **Accessibility (WCAG 2.1 AA / Section 508)** — legally required for platforms serving U.S. public educational institutions.

## Security Expectations

- Encryption at rest and in transit for all student records.
- Role-based access control mapped to institutional roles (student, instructor, teaching assistant, administrator, guardian).
- Strong tenant isolation — no cross-tenant data leakage under any application bug or misconfiguration.
- Regular third-party penetration testing and vulnerability scanning.
- Immutable audit trail for all administrative and data-access actions.

## Recovery Objectives

| Metric | Target | Rationale |
|---|---|---|
| RPO (Recovery Point Objective) | ≤ 5 minutes | Grade and submission data loss is unacceptable beyond a few minutes. |
| RTO (Recovery Time Objective) | ≤ 30 minutes for critical services (auth, gradebook) | Extended outages during exam windows have contractual and reputational consequences. |
| RTO (non-critical: discussion forums, analytics) | ≤ 4 hours | Lower business impact tolerates longer recovery. |

## SLAs

- 99.9% monthly uptime for the core platform, with service credits for institutional customers below threshold.
- Support response SLAs tiered by severity: Sev-1 (platform down) — 15 minute response; Sev-2 (degraded) — 1 hour; Sev-3 (minor) — next business day.

## Expected Workload

- Steady-state: tens of thousands of daily active users across all tenants combined.
- Peak: hundreds of thousands of concurrent users during synchronized academic events (e.g., a state-wide standardized test window).
- Storage growth: video content is the dominant driver, often growing by terabytes per academic term per large institutional tenant.

## Expected Growth

- Multi-tenant SaaS platforms in education typically grow both in tenant count (new districts/universities onboarded) and in per-tenant usage (more courses, more video, more integrations) — the architecture must scale on both axes independently.

---

# 3. Architecture Overview

## Overall Design

This architecture follows a **layered, event-driven, multi-tenant SaaS pattern**:

1. **Edge layer** — CloudFront + WAF + Shield in front of everything, terminating TLS and absorbing DDoS/bot traffic before it reaches application infrastructure.
2. **Application layer** — containerized microservices on ECS Fargate behind an Application Load Balancer, handling course logic, enrollment, grading orchestration, and forums.
3. **Serverless event layer** — Lambda functions triggered by EventBridge/SQS/S3 events for auto-grading, notification dispatch, and video processing orchestration.
4. **Data layer** — Aurora PostgreSQL (multi-tenant relational data: enrollment, grades, roster) + DynamoDB (high-throughput session state, real-time quiz/poll data) + S3 (course content, video, submissions).
5. **Media pipeline** — S3 ingestion → MediaConvert (VOD transcoding) / MediaLive + MediaPackage (live streaming) → CloudFront distribution.
6. **Identity layer** — Amazon Cognito for student/instructor identity, federated with institutional SAML/OIDC identity providers.
7. **Observability and security layer** — CloudWatch, CloudTrail, GuardDuty, Security Hub, AWS Config running across the full stack.

## Architecture Philosophy

- **Separate the "hot path" from the "cold path."** Student-facing reads (course browsing, video playback) are cached aggressively at the edge. Writes (submissions, grades) go through a durable, auditable path with no shortcuts.
- **Assume burst, don't provision for average.** Every compute layer is either serverless or Auto Scaling; nothing is sized for "typical Tuesday" traffic.
- **Tenant isolation is a first-class design constraint, not an afterthought.** Every data access pattern includes a tenant identifier, enforced at the database row level and validated at the application layer.
- **Decouple ingestion from processing.** Video uploads, assignment submissions, and grading requests are queued, not processed synchronously in the request path.

## Core Components

- CloudFront distribution (global content delivery + API acceleration)
- AWS WAF + Shield Advanced (edge security)
- Application Load Balancer + ECS Fargate services (API and application logic)
- Amazon Cognito (identity, SSO federation)
- Aurora PostgreSQL Multi-AZ (transactional data)
- DynamoDB (session state, real-time interaction data)
- S3 (course content, submissions, video source/output)
- MediaConvert / MediaLive / MediaPackage (video pipeline)
- SQS + SNS + EventBridge (asynchronous messaging and orchestration)
- Lambda (event-driven processing: grading, notifications, moderation)
- OpenSearch Service (course/content search)
- CloudWatch, CloudTrail, GuardDuty, Security Hub, AWS Config (observability and security)
- KMS + Secrets Manager (encryption and secrets)

## How Components Interact

- Students and instructors authenticate through Cognito, which federates to institutional identity providers where configured.
- Read-heavy traffic (browsing courses, watching video) is served primarily from CloudFront's cache, minimizing origin load.
- Write traffic (submissions, forum posts, grade entries) flows through the ALB to ECS Fargate services, which write to Aurora and emit events to EventBridge.
- Video uploads land in S3, triggering an EventBridge rule that starts a MediaConvert job; the output is republished to a CloudFront-fronted delivery bucket.
- Grading, notification, and moderation logic runs asynchronously via Lambda, decoupled from the user-facing request path through SQS queues.

## High-Level Workflow

1. User authenticates via Cognito (federated SSO or native credentials).
2. User requests course content; CloudFront serves cached assets or proxies to the application tier.
3. User submits an assignment; the application tier validates, stores the submission in S3, and writes metadata to Aurora.
4. An event is emitted; Lambda functions handle auto-grading (where applicable), plagiarism scanning, and notification dispatch.
5. Grades are written back to Aurora and reflected in the gradebook; students are notified asynchronously.

## Request Lifecycle

Client request → Route 53 DNS resolution → CloudFront edge → WAF inspection → (cache hit: served from edge) or (cache miss: forwarded to ALB) → ECS Fargate service → Aurora/DynamoDB → response returned through the same path, with CloudWatch and X-Ray capturing latency and trace data at every hop.

## Response Lifecycle

Application response is generated by the relevant Fargate service, serialized as JSON (API) or streamed (video via MediaPackage), returned through CloudFront, and cached according to content-type-specific TTL policies — aggressive caching for static course assets, no caching for personalized API responses.

## Data Lifecycle

- **Ingestion:** Course content and video uploaded to S3 by instructors; assignments submitted by students.
- **Processing:** Video transcoded by MediaConvert; submissions scanned for plagiarism; auto-graded where applicable.
- **Storage:** Transactional data in Aurora; high-frequency interaction data in DynamoDB; content and media in S3 with lifecycle policies moving cold content to Glacier.
- **Archival:** Historical academic records retained per institutional and legal retention requirements, often 5–7 years, in S3 Glacier Deep Archive.
- **Deletion:** Data subject erasure requests (GDPR) or end-of-retention deletion executed through automated, auditable workflows.


---

# 4. AWS Services Used

Each service below is scoped to the role it plays in this architecture. Services not relevant to this pattern (e.g., pure batch HPC services) are intentionally excluded.

## Amazon EC2

- **Purpose:** Used sparingly in this architecture — primarily for self-managed components that don't fit the container/serverless model (e.g., a licensed third-party proctoring engine that requires persistent OS-level access).
- **Why selected:** Most compute in this design runs on Fargate or Lambda; EC2 is reserved for exceptions.
- **Alternatives:** ECS Fargate (preferred for most services), Lambda (preferred for event-driven work).
- **Limitations:** Requires patching, AMI management, and capacity planning — operational overhead the rest of this architecture deliberately avoids.
- **Pricing considerations:** On-Demand for unpredictable exception workloads; Reserved/Savings Plans if a steady-state EC2 footprint emerges.
- **Best practices:** Minimize EC2 footprint; treat any EC2 usage as a deliberate exception requiring justification in architecture review.

## Application Load Balancer (ALB)

- **Purpose:** Distributes HTTP/HTTPS traffic across ECS Fargate tasks; supports path-based and host-based routing for multi-service API composition.
- **Why selected:** Native integration with ECS, target group health checks, and WAF attachment.
- **Alternatives:** Network Load Balancer (not needed here — no raw TCP/UDP requirement), API Gateway (viable for pure Lambda backends, but this architecture mixes Fargate and Lambda under one routing layer).
- **Limitations:** Layer 7 only; no support for non-HTTP protocols.
- **Pricing considerations:** Billed per Load Balancer Capacity Unit (LCU); cost scales with connections, bandwidth, and rule evaluations.
- **Best practices:** Use host-based routing to separate tenant-specific subdomains where custom branding is required; enable access logging to S3.

## Amazon CloudFront

- **Purpose:** Global content delivery network for static assets, video, and API acceleration.
- **Why selected:** Deep integration with S3, MediaPackage, WAF, and Shield; essential for delivering video lectures with low latency worldwide.
- **Alternatives:** Third-party CDNs (Akamai, Cloudflare) — viable but lose native integration with the rest of the AWS media and security stack.
- **Limitations:** Cache invalidation has cost and latency implications; must be planned for content updates.
- **Pricing considerations:** Data transfer out is the dominant cost driver; using regional edge caches and long TTLs for immutable content reduces origin fetches.
- **Best practices:** Separate cache behaviors for static assets (long TTL), API responses (no cache or short TTL), and video (dedicated behavior aligned with MediaPackage origin).

## AWS Lambda

- **Purpose:** Executes event-driven workloads: auto-grading, notification dispatch, plagiarism-scan orchestration, thumbnail generation, webhook handling.
- **Why selected:** Scales automatically with event volume (e.g., a deadline-driven submission spike), no idle cost between academic terms.
- **Alternatives:** ECS Fargate tasks triggered by SQS (viable for longer-running jobs); Step Functions for complex multi-step workflows.
- **Limitations:** 15-minute maximum execution time; cold starts can affect latency-sensitive paths (mitigated with provisioned concurrency where needed).
- **Pricing considerations:** Pay-per-invocation and duration; extremely cost-efficient for the "spiky, then idle" pattern typical of academic calendars.
- **Best practices:** Keep functions single-purpose; use Step Functions for multi-stage grading/moderation pipelines rather than chaining Lambdas ad hoc.

## Amazon S3

- **Purpose:** Primary storage for course content, video source and output files, assignment submissions, and static web assets.
- **Why selected:** Durable (11 nines), scales without provisioning, integrates natively with CloudFront, MediaConvert, and Lambda event notifications.
- **Alternatives:** EFS (unnecessary — this workload is object-based, not POSIX file-based).
- **Limitations:** Not a database; requires careful key design to avoid request-rate hot-spotting at extreme scale.
- **Pricing considerations:** Lifecycle policies matter enormously here — video content ages quickly in relevance; move older recorded lectures to Infrequent Access and Glacier tiers.
- **Best practices:** Separate buckets by data classification (raw uploads, processed content, submissions) with distinct lifecycle and access policies per bucket.

## Amazon RDS / Aurora PostgreSQL

- **Purpose:** System of record for enrollment, gradebook, roster, and course catalog data — data that requires strong relational integrity and transactional consistency.
- **Why selected:** Aurora's Multi-AZ replication, read replica scaling, and PostgreSQL's rich data model (including row-level security for tenant isolation) fit the compliance and consistency needs of student records.
- **Alternatives:** Standard RDS PostgreSQL (fewer HA/scaling guarantees), DynamoDB (poor fit for the relational, ad hoc query needs of gradebook and reporting data).
- **Limitations:** Vertical scaling has ceilings; extreme write throughput requires sharding strategies beyond a single Aurora cluster.
- **Pricing considerations:** Aurora I/O-Optimized pricing is often more cost-effective than standard Aurora for read-heavy, high I/O reporting workloads common in gradebook/analytics queries.
- **Best practices:** Use PostgreSQL row-level security policies keyed on tenant ID as a defense-in-depth layer beneath application-level tenant filtering.

## Amazon DynamoDB

- **Purpose:** Stores high-throughput, low-latency data: active session state, live quiz/poll responses during lectures, real-time attendance tracking.
- **Why selected:** Single-digit millisecond latency at any scale, ideal for the bursty read/write pattern of a live lecture with thousands of simultaneous poll responses.
- **Alternatives:** ElastiCache (viable for pure caching, but DynamoDB's durability is needed for data that must survive beyond the session).
- **Limitations:** Not suited for complex relational queries or ad hoc reporting — those stay in Aurora.
- **Pricing considerations:** On-Demand capacity mode fits the unpredictable, bursty access pattern of live class events better than Provisioned capacity.
- **Best practices:** Design partition keys around tenant + session to avoid hot partitions during synchronized events (e.g., a state-wide exam starting at the same second for all students).

## Amazon SNS

- **Purpose:** Fan-out notification delivery — a single grading event can trigger email, push, and SMS notifications simultaneously.
- **Why selected:** Native fan-out to multiple subscriber types (Lambda, SQS, email, SMS, mobile push) from a single publish action.
- **Alternatives:** Direct Lambda-to-Lambda invocation (tighter coupling, harder to extend).
- **Limitations:** No message retention beyond delivery attempt window; not a substitute for a durable queue.
- **Best practices:** Use SNS for fan-out, SQS for durable work queues — don't conflate the two roles.

## Amazon SQS

- **Purpose:** Decouples submission ingestion, video processing triggers, and grading requests from the synchronous request path.
- **Why selected:** Durable, at-least-once delivery buffers deadline-driven submission spikes so downstream processing (grading, plagiarism scan) doesn't need to scale instantaneously in lockstep with ingestion.
- **Alternatives:** Kinesis Data Streams (better suited for ordered, high-throughput streaming analytics rather than discrete work items).
- **Limitations:** At-least-once delivery requires idempotent consumers.
- **Best practices:** Use dead-letter queues for every processing queue; alert on DLQ depth as a leading indicator of downstream failures.

## Amazon EventBridge

- **Purpose:** Central event bus connecting S3 upload events, application-emitted domain events (submission created, grade posted), and scheduled academic-calendar-driven jobs.
- **Why selected:** Schema registry and content-based routing let independent services (grading, analytics, notifications) subscribe to relevant events without tight coupling to the publisher.
- **Alternatives:** SNS/SQS alone (works, but lacks EventBridge's rule-based content filtering and third-party SaaS integration capability).
- **Best practices:** Define a versioned event schema per domain event type; avoid embedding business logic in event routing rules.

## AWS Identity and Access Management (IAM)

- **Purpose:** Governs all service-to-service and human access to AWS resources.
- **Why selected:** Foundational; every other service in this architecture depends on IAM for access control.
- **Best practices:** Least-privilege roles per service; no shared credentials; permission boundaries on any role that can create other IAM roles.

## Amazon VPC

- **Purpose:** Network isolation boundary for the application and data tiers.
- **Why selected:** Required for placing Aurora, ElastiCache, and internal Fargate services outside public reachability.
- **Best practices:** Private subnets for data and application tiers; public subnets only for the ALB and NAT Gateways.

## Amazon Route 53

- **Purpose:** DNS resolution, including latency-based and geolocation routing for institutions with data residency requirements.
- **Why selected:** Tight integration with CloudFront and ALB health checks for automated failover.
- **Best practices:** Use health-check-based failover routing for the API layer between regions in the DR configuration.

## Amazon CloudWatch

- **Purpose:** Metrics, logs, dashboards, and alarms across every layer of the platform.
- **Best practices:** Define SLO-aligned alarms (e.g., API p95 latency, video start failure rate) rather than only infrastructure-level metrics.

## AWS CloudTrail

- **Purpose:** Immutable audit log of all AWS API activity — a direct requirement for FERPA/SOC 2 audit evidence.
- **Best practices:** Enable organization-wide trails with log file validation; deliver to a dedicated, access-restricted logging account.

## AWS Config

- **Purpose:** Continuous compliance monitoring — detects configuration drift (e.g., an S3 bucket accidentally made public).
- **Best practices:** Use Conformance Packs mapped to FERPA/SOC 2 control sets; auto-remediate high-risk findings (e.g., public bucket access) where safe to do so.

## Amazon GuardDuty

- **Purpose:** Threat detection across account activity, network traffic, and S3 data access patterns.
- **Best practices:** Enable S3 Protection and Malware Protection given the volume of user-uploaded content (assignment submissions) flowing through this platform.

## AWS KMS

- **Purpose:** Encryption key management for data at rest across S3, Aurora, DynamoDB, and EBS.
- **Best practices:** Use customer-managed keys (CMKs) per tenant tier where contractual data isolation requires demonstrable separation of encryption boundaries.

## AWS Secrets Manager

- **Purpose:** Stores database credentials, third-party API keys (e.g., video proctoring vendor integrations), and SSO federation secrets.
- **Best practices:** Enable automatic rotation for database credentials; never inject secrets via environment variables in plaintext task definitions.

## AWS Systems Manager

- **Purpose:** Patch management for any remaining EC2 footprint, Parameter Store for non-secret configuration, Session Manager for auditable shell access without bastion hosts.
- **Best practices:** Use Parameter Store for configuration that changes per environment (dev/staging/prod) but isn't sensitive enough for Secrets Manager.

## Amazon Cognito

- **Purpose:** Identity provider for students, instructors, and administrators; federates with institutional SAML/OIDC providers (common for university Shibboeleth/Active Directory Federation Services deployments).
- **Why selected:** Native SAML/OIDC federation, built-in MFA, and integration with API Gateway/ALB for token validation, without building a custom identity service.
- **Alternatives:** Auth0, Okta — viable and sometimes preferred when institutions already standardize on them; Cognito is preferred here for tighter native AWS integration and per-MAU pricing that scales with actual usage.
- **Best practices:** Use Cognito User Pools per tenant tier (or a shared pool with tenant-scoped custom attributes) depending on isolation requirements; never store passwords outside Cognito's managed credential store.

## AWS Elemental MediaConvert / MediaLive / MediaPackage

- **Purpose:** MediaConvert transcodes uploaded lecture recordings into adaptive bitrate renditions; MediaLive ingests live class streams; MediaPackage packages both for delivery through CloudFront.
- **Why selected:** Purpose-built for exactly this workload; building custom transcoding infrastructure is rarely justified given the maturity of this managed pipeline.
- **Best practices:** Use MediaConvert job templates per content type (lecture recording vs. short-form clip) to standardize output renditions and control cost.

## Amazon OpenSearch Service

- **Purpose:** Full-text search across course catalogs, content libraries, and discussion forums.
- **Best practices:** Isolate index access by tenant using index-per-tenant or alias-based filtering, matching the isolation model used elsewhere in the data layer.

---

# 5. Complete Architecture Diagram

\`\`\`mermaid
flowchart TB
    subgraph Users["Users"]
        STU[Students]
        INS[Instructors]
        ADM[Administrators]
    end

    subgraph DNS["DNS & Identity"]
        R53[Route 53]
        COG[Cognito User Pools<br/>SAML/OIDC Federation]
    end

    subgraph Edge["Edge Layer"]
        WAF[AWS WAF]
        SHIELD[Shield Advanced]
        CF[CloudFront Distribution]
    end

    subgraph Networking["VPC"]
        ALB[Application Load Balancer]

        subgraph AppTier["Application Tier - Private Subnets"]
            SVC1[Course Service<br/>ECS Fargate]
            SVC2[Enrollment Service<br/>ECS Fargate]
            SVC3[Gradebook Service<br/>ECS Fargate]
            SVC4[Forum Service<br/>ECS Fargate]
        end

        NAT[NAT Gateway]
    end

    subgraph Messaging["Messaging & Events"]
        EB[EventBridge Bus]
        SQS1[Submission Queue]
        SQS2[Grading Queue]
        SNS1[Notification Topic]
    end

    subgraph Compute["Event-Driven Compute"]
        L1[Lambda: Auto-Grade]
        L2[Lambda: Plagiarism Scan]
        L3[Lambda: Notification Dispatch]
    end

    subgraph MediaPipeline["Media Pipeline"]
        S3RAW[S3: Raw Uploads]
        MC[MediaConvert]
        ML[MediaLive]
        MP[MediaPackage]
        S3OUT[S3: Processed Content]
    end

    subgraph Data["Data Layer - Private Subnets"]
        AUR[(Aurora PostgreSQL<br/>Multi-AZ)]
        DDB[(DynamoDB<br/>Session/Realtime)]
        OS[(OpenSearch<br/>Content Search)]
    end

    subgraph Storage["Storage"]
        S3SUB[S3: Submissions]
        S3STATIC[S3: Static Assets]
    end

    subgraph Security["Security & Secrets"]
        KMS[AWS KMS]
        SEC[Secrets Manager]
    end

    subgraph Monitoring["Monitoring & Audit"]
        CW[CloudWatch]
        CT[CloudTrail]
        GD[GuardDuty]
        CFG[AWS Config]
    end

    STU --> R53
    INS --> R53
    ADM --> R53
    R53 --> CF
    CF --> WAF
    WAF --> SHIELD
    STU -.auth.-> COG
    INS -.auth.-> COG
    ADM -.auth.-> COG

    CF --> ALB
    CF --> S3STATIC
    CF --> MP

    ALB --> SVC1
    ALB --> SVC2
    ALB --> SVC3
    ALB --> SVC4

    SVC1 --> AUR
    SVC2 --> AUR
    SVC3 --> AUR
    SVC4 --> DDB
    SVC1 --> OS

    SVC2 --> S3SUB
    SVC2 --> SQS1
    SQS1 --> L1
    SQS1 --> L2
    L1 --> SQS2
    L2 --> SQS2
    SQS2 --> SVC3

    SVC3 --> EB
    EB --> SNS1
    SNS1 --> L3

    S3RAW --> MC
    S3RAW --> ML
    MC --> S3OUT
    ML --> MP
    S3OUT --> CF

    AUR -.encrypted by.-> KMS
    DDB -.encrypted by.-> KMS
    S3SUB -.encrypted by.-> KMS
    SVC1 -.reads.-> SEC
    SVC2 -.reads.-> SEC
    SVC3 -.reads.-> SEC

    AppTier -.logs.-> CW
    Data -.logs.-> CW
    Networking -.audit.-> CT
    Networking -.threat detection.-> GD
    Networking -.compliance.-> CFG
\`\`\`

---

# 6. Component-by-Component Explanation

## CloudFront Distribution

- **Purpose:** Global edge delivery for static content, video, and API acceleration.
- **Responsibilities:** Cache static assets, serve video segments from MediaPackage origin, forward uncacheable API traffic to the ALB.
- **Inputs:** Requests from students/instructors globally; origin content from S3 and MediaPackage.
- **Outputs:** Cached or origin-fetched responses to clients.
- **Scaling:** Fully managed; scales automatically to any traffic volume.
- **High availability:** Multi-origin failover configured between primary and DR region origins.
- **Failure handling:** Origin failover group automatically shifts to secondary origin on sustained 5xx responses.
- **Dependencies:** S3, MediaPackage, ALB, WAF.
- **Security:** WAF attached at the distribution level; Origin Access Control restricts direct S3 access.
- **Monitoring:** CloudFront real-time logs and standard access logs shipped to CloudWatch/S3 for analysis.

## Application Load Balancer

- **Purpose:** Layer 7 routing across ECS Fargate services.
- **Responsibilities:** Health checking, path-based routing to course/enrollment/gradebook/forum services, TLS termination.
- **Inputs:** HTTPS traffic from CloudFront.
- **Outputs:** Routed requests to healthy Fargate targets.
- **Scaling:** Auto-scales LCU capacity with traffic.
- **High availability:** Deployed across a minimum of three Availability Zones.
- **Failure handling:** Unhealthy targets automatically removed from rotation based on health check thresholds.
- **Dependencies:** ECS Fargate target groups, ACM certificates.
- **Security:** Security groups restrict inbound to CloudFront's managed prefix list only.
- **Monitoring:** Target response time, HTTP 5xx count, healthy/unhealthy host count alarms.

## ECS Fargate Application Services

- **Purpose:** Stateless containerized services implementing course, enrollment, gradebook, and forum logic.
- **Responsibilities:** Business logic execution, request validation, tenant-scoped data access enforcement.
- **Inputs:** HTTP requests from the ALB.
- **Outputs:** JSON API responses; domain events published to EventBridge.
- **Scaling:** Target-tracking Auto Scaling on CPU utilization and request count per target.
- **High availability:** Tasks distributed across multiple AZs with a minimum task count per service.
- **Failure handling:** Failed tasks automatically replaced by ECS service scheduler; circuit breakers prevent cascading failures to downstream dependencies.
- **Dependencies:** Aurora, DynamoDB, Secrets Manager, EventBridge.
- **Security:** Task IAM roles scoped per service; no service has broader access than its specific data needs.
- **Monitoring:** Container Insights for CPU/memory; custom application metrics via CloudWatch embedded metric format.

## Aurora PostgreSQL Cluster

- **Purpose:** System of record for relational student, course, and grade data.
- **Responsibilities:** Transactional consistency for enrollment and grading operations; row-level security enforcement for tenant isolation.
- **Inputs:** Writes/reads from application services.
- **Outputs:** Query results; replication stream to read replicas.
- **Scaling:** Read replicas scale read capacity; Aurora Serverless v2 option for variable-load tenant tiers.
- **High availability:** Multi-AZ with automated failover to a replica within seconds.
- **Failure handling:** Automated failover; continuous backup to S3 with point-in-time recovery.
- **Dependencies:** KMS for encryption, Secrets Manager for credential rotation.
- **Security:** Encrypted at rest and in transit; row-level security policies scoped by tenant ID.
- **Monitoring:** Performance Insights, CloudWatch database metrics, slow query logging.

## DynamoDB Tables

- **Purpose:** Low-latency storage for session state and real-time interactive data (live polls, attendance).
- **Responsibilities:** Serve extremely high read/write throughput during synchronized live events.
- **Inputs/Outputs:** Key-value and query operations from application services and Lambda functions.
- **Scaling:** On-Demand capacity mode absorbs unpredictable spikes without pre-provisioning.
- **High availability:** Data automatically replicated across three AZs by design.
- **Failure handling:** Point-in-time recovery enabled; DynamoDB Streams feed downstream analytics without impacting primary table performance.
- **Dependencies:** KMS for encryption.
- **Security:** Fine-grained IAM policies restrict access per table and, where needed, per partition key prefix.
- **Monitoring:** Throttled request alarms, consumed capacity dashboards.

## Media Pipeline (MediaConvert / MediaLive / MediaPackage)

- **Purpose:** Transform raw video uploads and live feeds into adaptive-bitrate, CDN-deliverable streams.
- **Responsibilities:** Transcoding, packaging, DRM application where required by content licensing agreements.
- **Inputs:** Raw video files (S3) or live RTMP/SRT feeds.
- **Outputs:** HLS/DASH manifests and segments delivered via CloudFront.
- **Scaling:** MediaConvert scales job concurrency automatically; MediaLive channels are provisioned per concurrent live stream need.
- **High availability:** MediaLive supports standard (redundant) channel pipelines across two AZs for critical live events.
- **Failure handling:** Failed MediaConvert jobs trigger EventBridge alerts and automatic retry with backoff.
- **Dependencies:** S3, CloudFront, IAM.
- **Security:** Signed URLs/cookies restrict playback access to authenticated, entitled users.
- **Monitoring:** Job completion/failure metrics, live channel input loss alarms.

## Lambda Event Processors

- **Purpose:** Execute discrete, event-triggered business logic: auto-grading, plagiarism scanning, notification dispatch.
- **Responsibilities:** Idempotent processing of queued events; writing results back to Aurora/DynamoDB.
- **Inputs:** SQS messages, EventBridge events, S3 event notifications.
- **Outputs:** Processed results, downstream events, notifications dispatched via SNS.
- **Scaling:** Automatic concurrency scaling; reserved concurrency caps applied to protect downstream Aurora connection limits.
- **High availability:** Multi-AZ by default as a managed service.
- **Failure handling:** Dead-letter queues capture failed invocations for investigation and replay.
- **Dependencies:** SQS, Aurora (via RDS Proxy), DynamoDB, SNS.
- **Security:** Function-specific IAM execution roles; VPC-attached only where private resource access is required.
- **Monitoring:** Invocation error rate, duration, throttle count.

## Cognito User Pools

- **Purpose:** Central identity provider for all user types across tenants.
- **Responsibilities:** Authentication, MFA enforcement, SAML/OIDC federation with institutional identity providers, token issuance.
- **Inputs:** Login credentials or federated assertions from institutional IdPs.
- **Outputs:** JWTs used for API authorization.
- **Scaling:** Fully managed; scales to millions of users.
- **High availability:** Regionally resilient managed service.
- **Failure handling:** Managed by AWS; multi-region user pool replication considered for DR-critical deployments.
- **Dependencies:** Institutional SAML/OIDC providers.
- **Security:** MFA enforced for administrator and instructor roles at minimum; adaptive authentication for anomalous login detection.
- **Monitoring:** Failed authentication rate, federation error alarms.

---

# 7. End-to-End Request Flow

## Scenario: Student Submits an Assignment Before a Deadline

1. Student's browser resolves the platform domain via Route 53.
2. Request reaches the nearest CloudFront edge location.
3. CloudFront forwards the request through AWS WAF, which inspects for common web exploits and rate-limits abusive clients.
4. The request (an authenticated API call) is identified as uncacheable and forwarded to the origin — the Application Load Balancer.
5. The ALB performs a health check-based routing decision and forwards the request to a healthy ECS Fargate task running the Enrollment/Submission service.
6. The Fargate service validates the Cognito-issued JWT, extracting the student's tenant ID and identity claims.
7. The service verifies the assignment deadline has not passed and validates the submission payload.
8. The submission file is streamed to a tenant-scoped S3 prefix in the Submissions bucket, encrypted with a tenant-specific KMS key where contractually required.
9. Submission metadata (student ID, assignment ID, timestamp, S3 object key) is written to Aurora within a transaction.
10. A `submission.created` event is published to EventBridge.
11. EventBridge routes the event to the Submission Queue (SQS).
12. A Lambda function consumes the queue message and determines whether the assignment is eligible for auto-grading.
13. If eligible, the auto-grading Lambda executes grading logic and writes the result to Aurora; if not, the submission is flagged for manual instructor grading.
14. A second Lambda function runs plagiarism-scan orchestration asynchronously, independent of the grading path.
15. Once grading completes, a `grade.posted` event triggers the notification Lambda via SNS fan-out.
16. The student receives a push/email notification confirming submission and (if auto-graded) the grade.
17. Throughout the flow, CloudWatch captures latency at every hop; X-Ray traces the request across ALB, Fargate, and Lambda boundaries.
18. If any step fails (e.g., S3 write failure), the error is logged, the request returns a clear error response to the client, and — for asynchronous steps — the message is retried via SQS with exponential backoff before landing in a dead-letter queue for manual investigation.
19. CloudTrail records every AWS API call involved in this flow for audit purposes, satisfying FERPA access-logging requirements.

---

# 8. Deployment Flow

## Infrastructure Provisioning

- All infrastructure is defined in Terraform, organized into reusable modules: networking, data, application, media pipeline, identity, and observability.
- Environments (dev, staging, production) are separate AWS accounts under an AWS Organizations structure, each with its own Terraform state.

## Terraform Workflow

1. Engineer opens a pull request with infrastructure changes.
2. CI pipeline runs `terraform fmt -check`, `terraform validate`, and `tflint`.
3. `terraform plan` output is posted as a PR comment for review.
4. A second engineer reviews both the code change and the plan output.
5. On merge to main, CI runs `terraform apply` against the target environment using a dedicated deployment role.

## CI/CD Deployment

- Application code changes trigger a build pipeline: unit tests → container image build → vulnerability scan (Amazon ECR image scanning + third-party SCA tool) → push to ECR.
- Deployment pipeline updates the ECS task definition with the new image tag and initiates a rolling or blue-green deployment via AWS CodeDeploy.

## Blue-Green Deployment

- CodeDeploy provisions a parallel "green" target group running the new task definition.
- Traffic is shifted incrementally (e.g., 10% → 50% → 100%) with automated CloudWatch alarm checks (error rate, latency) gating each shift.
- If alarms breach thresholds during the shift, CodeDeploy automatically rolls back traffic to the "blue" (previous) target group.

## Rollback

- Application rollback: automatic via CodeDeploy alarm-triggered rollback, or manual redeploy of the previous task definition revision.
- Database schema rollback: all schema migrations are written to be backward-compatible for at least one release cycle (expand/contract pattern), avoiding the need for destructive rollback of live data.

## Secrets

- Database credentials, third-party API keys, and SAML signing certificates are stored in Secrets Manager and injected into ECS task definitions via secret references — never as plaintext environment variables in Terraform or task definitions.

## Configuration

- Non-sensitive, environment-specific configuration (feature flags, rate limits, tenant tier settings) is stored in Systems Manager Parameter Store, referenced by the application at startup and refreshed periodically.

## Validation

- Post-deployment smoke tests verify authentication, course browsing, and submission flows against synthetic canary accounts before a deployment is marked successful.
- Deployments outside a defined "safe window" (i.e., avoiding scheduled exam windows) require an additional manual approval gate.

---

# 9. Network Topology

## VPC

- A dedicated VPC per environment, sized to accommodate multi-AZ deployment across a minimum of three Availability Zones.

## CIDR

- Example: `10.20.0.0/16` for production, with `/20` subnets allocated per tier per AZ, leaving substantial headroom for future service growth.

## Public Subnets

- Host only the ALB and NAT Gateways. No application or data resources are placed in public subnets.

## Private Subnets

- **Application subnets:** Host ECS Fargate tasks and VPC-attached Lambda functions.
- **Data subnets:** Host Aurora, ElastiCache (if used), and DynamoDB VPC endpoints.

## NAT Gateway

- One NAT Gateway per AZ (not a single shared NAT Gateway) to avoid a cross-AZ single point of failure and to minimize cross-AZ data transfer charges.

## Internet Gateway

- Attached to the VPC to provide the public subnets' route to the internet; private subnets route outbound traffic through NAT Gateways only.

## Transit Gateway

- Used in multi-VPC deployments (e.g., a shared services VPC hosting CI/CD tooling and a security tooling VPC) to interconnect without full mesh VPC peering.

## Route Tables

- Distinct route tables per subnet tier; private application/data subnets have no direct route to the Internet Gateway.

## Network ACLs

- Stateless NACLs applied as a coarse-grained additional layer, primarily to explicitly deny known-bad CIDR ranges and enforce subnet-tier boundaries.

## Security Groups

- Layered security group model: ALB security group allows inbound 443 from CloudFront's managed prefix list only; application security group allows inbound only from the ALB security group; data-tier security groups allow inbound only from the application security group on their specific service ports.

## PrivateLink

- VPC endpoints (Interface and Gateway) for S3, DynamoDB, Secrets Manager, KMS, and SQS/SNS ensure traffic between application services and AWS services never traverses the public internet.

## Hybrid Connectivity

- For institutional tenants requiring integration with on-premises Student Information Systems (SIS), Site-to-Site VPN or Direct Connect is provisioned into a dedicated integration subnet, isolated from the core application network.

---

# 10. Identity and Access

## IAM Roles

- Distinct execution roles per ECS service and per Lambda function; no shared "application role" pattern.
- A separate deployment role, usable only by the CI/CD pipeline, scoped to exactly the resources it needs to modify.

## IAM Policies

- Written with explicit resource ARNs wherever feasible rather than wildcard resources; scoped further with condition keys (e.g., restricting S3 actions to objects under a tenant-specific prefix).

## Resource Policies

- S3 bucket policies enforce encryption-in-transit (deny requests without `aws:SecureTransport`) and restrict access to specific VPC endpoints for internal buckets.

## STS

- Cross-account access (e.g., a centralized logging account, a shared CI/CD account) uses STS `AssumeRole` with short-lived credentials rather than long-lived cross-account IAM users.

## Cross-Account Access

- Production, staging, and development live in separate AWS accounts; break-glass emergency access to production uses a time-bound, logged AssumeRole workflow requiring justification.

## Least Privilege

- Enforced through a combination of IAM Access Analyzer (to detect unused permissions) and quarterly access reviews for human IAM users and roles.

## Service Roles

- ECS task execution roles are separated from ECS task roles: the execution role only pulls images and reads secrets for container startup; the task role governs runtime AWS API access.

## Permission Boundaries

- Applied to any role capable of creating other IAM roles or policies (e.g., a platform-provisioning Lambda for tenant onboarding), preventing privilege escalation even if that role is compromised.

---

# 11. Security Architecture

## Encryption

- All data encrypted at rest: S3 (SSE-KMS), Aurora (KMS-encrypted storage), DynamoDB (KMS-encrypted tables).
- All data encrypted in transit: TLS 1.2+ enforced at CloudFront, ALB, and internal service-to-service communication.

## KMS

- Customer-managed keys used for tenant tiers with contractual data-isolation requirements; AWS-managed keys acceptable for lower-sensitivity, non-tenant-specific data.
- Key rotation enabled annually at minimum; access to key administration restricted to a small security team group.

## TLS

- TLS termination at CloudFront and ALB; re-encryption (TLS to the origin) enforced between CloudFront and ALB, and between ALB and Fargate tasks, for defense in depth.

## WAF

- Managed rule groups (Core Rule Set, Known Bad Inputs, SQLi) combined with custom rules rate-limiting login endpoints and blocking known scraper user agents targeting course content.

## Shield

- Shield Advanced enabled for the production CloudFront distribution and ALB, given the reputational and contractual cost of a DDoS-induced outage during an exam window.

## Secrets Manager

- All database credentials, third-party integration keys, and SAML signing keys stored here with automatic rotation configured for database credentials.

## Certificate Manager

- Public TLS certificates for CloudFront and ALB provisioned and auto-renewed via ACM; private certificates for internal mTLS where service-to-service authentication is required.

## GuardDuty

- Enabled with S3 Protection and Malware Protection, since the submissions bucket receives arbitrary user-uploaded files that must be scanned before being trusted.

## Inspector

- Continuous vulnerability scanning of ECR container images and any remaining EC2 instances.

## Security Hub

- Aggregates findings from GuardDuty, Inspector, and Config into a single view; used as the primary dashboard for the security team's daily triage.

## CloudTrail

- Organization-wide trail capturing all management and data events, delivered to a centralized, access-restricted logging account with log file integrity validation enabled.

## AWS Config

- Conformance packs aligned to FERPA-relevant controls (encryption enabled, public access blocked, logging enabled) with automated remediation for high-risk drift.

## Zero Trust

- No implicit trust granted based on network location alone; every service-to-service call is authenticated (IAM SigV4 or mTLS) regardless of whether it originates inside the VPC.

## Threat Model

- **Primary assets:** Student PII, grades, submitted assignment content, institutional roster data.
- **Primary threat actors:** Opportunistic credential-stuffing attackers, students attempting to access peers' grades or submissions, malicious file uploads disguised as assignments.

## Attack Vectors

| Vector | Description |
|---|---|
| Credential stuffing | Reused passwords from breached third-party sites tested against student login. |
| IDOR (Insecure Direct Object Reference) | A student manipulating a submission or grade ID to access another student's data. |
| Malicious file upload | An "assignment" submission containing malware or exploit payloads. |
| Tenant boundary bypass | Application bug allowing cross-tenant data access. |
| DDoS during exam window | Attack timed to disrupt a high-stakes assessment window. |

## Mitigations

| Attack Vector | Mitigation |
|---|---|
| Credential stuffing | MFA enforcement, adaptive authentication, WAF rate limiting on login endpoints. |
| IDOR | Server-side authorization checks on every object access, never relying on client-supplied IDs alone. |
| Malicious file upload | GuardDuty Malware Protection scanning on the submissions bucket; sandboxed file-type validation before processing. |
| Tenant boundary bypass | Row-level security in Aurora as a defense-in-depth layer beneath application-level checks. |
| DDoS during exam window | Shield Advanced, WAF rate-based rules, pre-scaled capacity ahead of known exam schedules. |

---

# 12. High Availability

## AZ Failures

- ECS Fargate tasks, Aurora, and ALB all span a minimum of three AZs; loss of a single AZ removes capacity but does not cause an outage.

## Instance Failures

- ECS service scheduler automatically replaces failed tasks; Aurora automatically fails over to a healthy replica within the same or a different AZ.

## Regional Failures

- Addressed via the DR strategy in Section 13; not absorbed automatically at the HA layer, which is scoped to intra-region resilience.

## Database Failures

- Aurora Multi-AZ automated failover typically completes within 30–60 seconds; read replicas allow read traffic to continue during failover for read-heavy operations (e.g., course browsing).

## Load Balancing

- ALB health checks (HTTP-based, checking a dedicated `/health` endpoint that verifies downstream dependency connectivity) remove unhealthy targets from rotation within the configured interval.

## Health Checks

- Deep health checks validate database connectivity and dependency availability, not just process liveness, to avoid routing traffic to a task that is "up" but non-functional.

## Failover

- Route 53 health-check-based failover routing directs traffic to the DR region's API endpoint if the primary region's health check fails sustained checks over a configured window.

---

# 13. Disaster Recovery

## Backup Strategy

- Aurora: continuous backup with point-in-time recovery, retained per compliance requirement (commonly 35 days minimum, longer for archival compliance).
- S3: versioning enabled on all buckets containing student-generated content; cross-region replication for submissions and gradebook-related content.
- DynamoDB: point-in-time recovery enabled on all tables.

## Snapshots

- Daily automated Aurora snapshots, copied cross-region, retained per institutional data retention contracts.

## Cross-Region Replication

- S3 CRR replicates submissions and processed course content to the DR region continuously.
- Aurora Global Database used for tenant tiers with the strictest RTO requirements, providing a warm standby with sub-second replication lag and fast promotion.

## Pilot Light

- The baseline DR posture: minimal standing infrastructure in the DR region (networking, Aurora Global Database secondary cluster), with application compute scaled up from zero on activation.

## Warm Standby

- Used for the highest-tier institutional contracts with the strictest RTO: a scaled-down but running copy of the application tier in the DR region, ready to absorb full traffic within minutes of promotion.

## Multi-Site / Active-Active

- Not the default posture for this architecture given cost — reserved for platform-wide deployments serving global, latency-sensitive live-class audiences where regional failover latency itself would be unacceptable.

## RPO / RTO by Tier

| Tier | RPO | RTO | DR Pattern |
|---|---|---|---|
| Standard | 15 minutes | 4 hours | Pilot light |
| Premium | 5 minutes | 30 minutes | Warm standby |
| Critical (state testing platforms) | Near-zero | 15 minutes | Aurora Global Database + warm standby |

---

# 14. Scalability

## Horizontal Scaling

- ECS Fargate services scale horizontally via target-tracking Auto Scaling policies keyed on CPU utilization and ALB request count per target.

## Vertical Scaling

- Used sparingly — primarily for Aurora writer instance class adjustments ahead of known peak periods (e.g., scaling up before a state-wide exam window), scheduled via automation rather than reactive manual changes.

## Auto Scaling

- Scheduled scaling actions pre-provision additional Fargate capacity ahead of known academic calendar events (semester start, exam weeks), supplementing reactive target-tracking scaling.

## Serverless Scaling

- Lambda and DynamoDB On-Demand scale automatically with zero pre-provisioning, which is precisely why they are used for the least predictable parts of the workload (live poll responses, notification fan-out).

## Database Scaling

- Aurora read replicas scale read capacity; Aurora Serverless v2 considered for lower-tier tenants with highly variable, unpredictable load patterns.

## Storage Scaling

- S3 scales inherently without provisioning; DynamoDB On-Demand scales storage and throughput together.

## Queue Scaling

- SQS scales inherently; Lambda concurrency scaling is the actual throughput-governing factor, tuned with reserved and provisioned concurrency settings per queue's criticality.

---

# 15. Performance Optimization

## Caching

- CloudFront caches static course assets and video segments aggressively; ElastiCache (Redis) used in front of Aurora for frequently accessed, slowly changing data such as course catalog listings.

## Compression

- Brotli/Gzip compression enabled at CloudFront and ALB for all compressible content types.

## CDN

- CloudFront's regional edge cache tier reduces origin load for semi-popular content that doesn't fit purely in edge POP caches.

## Database Optimization

- Read replicas offload reporting and analytics queries from the primary write instance; appropriate indexing reviewed quarterly against slow query logs.

## Connection Pooling

- RDS Proxy sits in front of Aurora for Lambda-originated connections, preventing connection exhaustion during high-concurrency grading bursts.

## Concurrency

- ECS services tuned for appropriate worker/thread concurrency per task to maximize throughput without exhausting downstream database connections.

## Async Processing

- Grading, plagiarism scanning, and notification dispatch are all asynchronous by design, keeping the synchronous submission path fast and simple.

---

# 16. Cost Optimization (FinOps)

## Estimated Monthly Cost by Deployment Size

| Component | Small (5K students) | Medium (50K students) | Enterprise (500K+ students) |
|---|---|---|---|
| CloudFront + data transfer | $800 | $6,500 | $55,000 |
| ECS Fargate | $600 | $4,200 | $32,000 |
| Aurora PostgreSQL | $700 | $3,800 | $24,000 |
| DynamoDB (On-Demand) | $150 | $1,200 | $9,500 |
| Lambda | $100 | $900 | $6,000 |
| S3 storage + requests | $300 | $2,500 | $18,000 |
| MediaConvert/MediaLive/MediaPackage | $400 | $3,500 | $28,000 |
| OpenSearch | $200 | $1,100 | $7,000 |
| Cognito (MAU-based) | $100 | $900 | $6,500 |
| WAF/Shield/GuardDuty/Config | $250 | $900 | $4,500 |
| **Estimated Total** | **~$3,600/mo** | **~$25,500/mo** | **~$190,500/mo** |

> **Note:** These figures are illustrative planning estimates, not quotes. Actual cost depends heavily on video minutes delivered, live-streaming concurrency, and data transfer patterns, which vary significantly by institution type.

## Major Cost Drivers

1. **CloudFront data transfer** — video is by far the largest driver at scale.
2. **MediaConvert/MediaLive** — transcoding cost scales with minutes of video processed, not just storage.
3. **Aurora** — driven by instance class and I/O, particularly during report-heavy periods (end-of-term grade exports).
4. **DynamoDB** — spikes sharply during synchronized live events; On-Demand pricing avoids overpaying for idle capacity between events.

## Optimization Opportunities

- **Reserved Instances / Savings Plans:** Apply Compute Savings Plans to the steady-state baseline portion of Fargate usage; leave burst capacity on On-Demand.
- **Spot:** Not used for user-facing services (unpredictable interruption is unacceptable for exam-window traffic); viable for non-time-sensitive batch jobs like nightly analytics aggregation if run on EC2/ECS rather than Fargate.
- **S3 Lifecycle:** Move recorded lecture video older than one academic term to S3 Infrequent Access; move content older than the retention-required window to Glacier Deep Archive.
- **Storage Classes:** Use S3 Intelligent-Tiering for submission content with unpredictable access patterns.
- **Rightsizing:** Quarterly review of Aurora instance classes and Fargate task CPU/memory allocation against actual utilization (Compute Optimizer recommendations).
- **Cost Allocation:** Tag every resource with `tenant-tier`, `environment`, and `cost-center` to attribute infrastructure spend back to specific institutional contracts.
- **Tagging:** Enforced via AWS Config rules that flag untagged resources for remediation.
- **Budgets:** Per-tenant-tier AWS Budgets with alert thresholds at 80% and 100% of forecasted monthly spend.
- **Cost Anomaly Detection:** Enabled at the account level to catch unexpected spend spikes (e.g., a runaway Lambda retry loop) before month-end billing surprises.

---

# 17. AI-Assisted Operations

## Amazon Q

- Used by the platform engineering team for querying CloudWatch logs in natural language during incident response (e.g., "show me all 5xx errors from the gradebook service in the last hour"), reducing mean time to diagnosis.

## Bedrock

- Powers optional platform features distinct from core infrastructure operations: automated feedback suggestions on written assignments, content summarization for accessibility, and adaptive quiz question generation — always with human (instructor) review before any grade-affecting output is finalized.

## AI Troubleshooting

- Amazon Q Developer assists engineers in root-causing production issues by correlating recent deployments, CloudWatch alarms, and X-Ray traces.

## Log Analysis

- Natural-language querying of aggregated logs accelerates triage during high-severity incidents, particularly valuable given the time pressure of exam-window outages.

## Incident Response

- AI-assisted runbook generation drafts initial incident timelines from CloudWatch/CloudTrail data, which the on-call engineer verifies and refines rather than writing from scratch under pressure.

## Cost Optimization

- AI-driven Cost Anomaly Detection surfaces unusual spend patterns without requiring an engineer to manually review dashboards daily.

## Capacity Planning

- Historical traffic pattern analysis (informed by academic calendar data) feeds scheduled scaling recommendations ahead of known peak periods.

## Architecture Review

- Amazon Q Developer assists in reviewing Terraform changes against Well-Architected Framework best practices as an additional review layer, not a replacement for human review.

## AI-Generated Terraform

- Used to scaffold boilerplate module structure for new tenant onboarding infrastructure, always reviewed and tested before merge — never applied directly to production without human review.

## AI-Generated Documentation

- Runbook and architecture documentation drafts are AI-assisted but human-verified, particularly for compliance-relevant documentation where accuracy is non-negotiable.

---

# 18. Terraform Implementation

## Provider and Backend Configuration

\`\`\`hcl

# providers.tf

terraform {
  required_version = ">= 1.7.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket         = "edu-platform-terraform-state-prod"
    key            = "core/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "edu-platform-terraform-locks"
    encrypt        = true
  }
}

provider "aws" {
  region = var.aws_region

  default_tags {
    tags = {
      Project     = "education-platform"
      Environment = var.environment
      ManagedBy   = "terraform"
    }
  }
}
\`\`\`

## Variables

\`\`\`hcl

# variables.tf

variable "environment" {
  description = "Deployment environment (dev, staging, production)"
  type        = string
}

variable "aws_region" {
  description = "Primary AWS region"
  type        = string
  default     = "us-east-1"
}

variable "vpc_cidr" {
  description = "CIDR block for the VPC"
  type        = string
  default     = "10.20.0.0/16"
}

variable "az_count" {
  description = "Number of Availability Zones to use"
  type        = number
  default     = 3
}

variable "aurora_min_capacity" {
  description = "Aurora Serverless v2 minimum ACU"
  type        = number
  default     = 0.5
}

variable "aurora_max_capacity" {
  description = "Aurora Serverless v2 maximum ACU"
  type        = number
  default     = 16
}

variable "fargate_min_task_count" {
  description = "Minimum number of Fargate tasks per service"
  type        = number
  default     = 3
}

variable "fargate_max_task_count" {
  description = "Maximum number of Fargate tasks per service"
  type        = number
  default     = 60
}
\`\`\`

## Networking Module (excerpt)

\`\`\`hcl

# modules/networking/main.tf

resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = { Name = "edu-platform-${var.environment}-vpc" }
}

resource "aws_subnet" "private_app" {
  count             = var.az_count
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 4, count.index)
  availability_zone = data.aws_availability_zones.available.names[count.index]

  tags = {
    Name = "edu-platform-${var.environment}-private-app-${count.index}"
    Tier = "application"
  }
}

resource "aws_subnet" "private_data" {
  count             = var.az_count
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 4, count.index + var.az_count)
  availability_zone = data.aws_availability_zones.available.names[count.index]

  tags = {
    Name = "edu-platform-${var.environment}-private-data-${count.index}"
    Tier = "data"
  }
}

resource "aws_subnet" "public" {
  count                   = var.az_count
  vpc_id                  = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4, count.index + (2 * var.az_count))
  availability_zone       = data.aws_availability_zones.available.names[count.index]
  map_public_ip_on_launch = true

  tags = { Name = "edu-platform-${var.environment}-public-${count.index}" }
}

resource "aws_nat_gateway" "main" {
  count         = var.az_count
  allocation_id = aws_eip.nat[count.index].id
  subnet_id     = aws_subnet.public[count.index].id

  tags = { Name = "edu-platform-${var.environment}-nat-${count.index}" }
}
\`\`\`

## Compute Module (ECS Fargate Service, excerpt)

\`\`\`hcl

# modules/compute/gradebook_service.tf

resource "aws_ecs_service" "gradebook" {
  name            = "gradebook-service"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.gradebook.arn
  desired_count   = var.fargate_min_task_count
  launch_type     = "FARGATE"

  network_configuration {
    subnets          = var.private_app_subnet_ids
    security_groups  = [aws_security_group.gradebook_service.id]
    assign_public_ip = false
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.gradebook.arn
    container_name    = "gradebook-service"
    container_port    = 8080
  }

  deployment_controller {
    type = "CODE_DEPLOY"
  }

  lifecycle {
    ignore_changes = [task_definition, desired_count]
  }
}

resource "aws_appautoscaling_target" "gradebook" {
  max_capacity       = var.fargate_max_task_count
  min_capacity       = var.fargate_min_task_count
  resource_id        = "service/${aws_ecs_cluster.main.name}/${aws_ecs_service.gradebook.name}"
  scalable_dimension = "ecs:service:DesiredCount"
  service_namespace  = "ecs"
}

resource "aws_appautoscaling_policy" "gradebook_cpu" {
  name               = "gradebook-cpu-target-tracking"
  policy_type        = "TargetTrackingScaling"
  resource_id        = aws_appautoscaling_target.gradebook.resource_id
  scalable_dimension = aws_appautoscaling_target.gradebook.scalable_dimension
  service_namespace  = aws_appautoscaling_target.gradebook.service_namespace

  target_tracking_scaling_policy_configuration {
    predefined_metric_specification {
      predefined_metric_type = "ECSServiceAverageCPUUtilization"
    }
    target_value       = 60
    scale_in_cooldown  = 300
    scale_out_cooldown = 60
  }
}
\`\`\`

## IAM Module (Task Role, excerpt)

\`\`\`hcl

# modules/iam/gradebook_task_role.tf

resource "aws_iam_role" "gradebook_task_role" {
  name = "edu-platform-${var.environment}-gradebook-task-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect    = "Allow"
      Principal = { Service = "ecs-tasks.amazonaws.com" }
      Action    = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_role_policy" "gradebook_task_policy" {
  name = "gradebook-task-policy"
  role = aws_iam_role.gradebook_task_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "secretsmanager:GetSecretValue"
        ]
        Resource = [var.gradebook_db_secret_arn]
      },
      {
        Effect = "Allow"
        Action = [
          "dynamodb:GetItem",
          "dynamodb:PutItem",
          "dynamodb:Query"
        ]
        Resource = [var.session_table_arn]
      },
      {
        Effect = "Allow"
        Action = [
          "events:PutEvents"
        ]
        Resource = [var.event_bus_arn]
      }
    ]
  })
}
\`\`\`

## Outputs

\`\`\`hcl

# outputs.tf

output "cloudfront_distribution_domain" {
  value = aws_cloudfront_distribution.main.domain_name
}

output "aurora_cluster_endpoint" {
  value     = aws_rds_cluster.main.endpoint
  sensitive = true
}

output "ecs_cluster_name" {
  value = aws_ecs_cluster.main.name
}
\`\`\`

> **Best practice:** Remote state is stored in S3 with DynamoDB-based state locking and versioning enabled on the state bucket. Each environment (dev/staging/production) uses a distinct state file key and, ideally, a distinct AWS account — never a shared state file across environments.

---

# 19. AWS CLI Examples

## Deployment

\`\`\`bash

# Force a new deployment of the gradebook service after pushing a new image

aws ecs update-service \
  --cluster edu-platform-prod \
  --service gradebook-service \
  --force-new-deployment

# Check the status of an ongoing CodeDeploy blue-green deployment

aws deploy get-deployment \
  --deployment-id d-XXXXXXXXX \
  --query "deploymentInfo.status"
\`\`\`

## Validation

\`\`\`bash

# Verify ECS service is stable after deployment

aws ecs wait services-stable \
  --cluster edu-platform-prod \
  --services gradebook-service

# Confirm Aurora cluster is available

aws rds describe-db-clusters \
  --db-cluster-identifier edu-platform-prod-aurora \
  --query "DBClusters[0].Status"
\`\`\`

## Monitoring

\`\`\`bash

# Pull recent CloudWatch alarms in ALARM state

aws cloudwatch describe-alarms \
  --state-value ALARM \
  --query "MetricAlarms[*].[AlarmName,StateReason]" \
  --output table

# Check DynamoDB throttled requests over the last hour

aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ThrottledRequests \
  --dimensions Name=TableName,Value=live-session-state \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 \
  --statistics Sum
\`\`\`

## Troubleshooting

\`\`\`bash

# Inspect why an ECS task stopped

aws ecs describe-tasks \
  --cluster edu-platform-prod \
  --tasks arn:aws:ecs:us-east-1:123456789012:task/edu-platform-prod/abcdef123456 \
  --query "tasks[0].stoppedReason"

# Check SQS dead-letter queue depth

aws sqs get-queue-attributes \
  --queue-url https://sqs.us-east-1.amazonaws.com/123456789012/grading-queue-dlq \
  --attribute-names ApproximateNumberOfMessages
\`\`\`

## Cleanup

\`\`\`bash

# Remove a decommissioned tenant's stale Parameter Store entries

aws ssm get-parameters-by-path \
  --path "/edu-platform/tenants/district-042/" \
  --query "Parameters[*].Name" --output text | \
  xargs -n1 aws ssm delete-parameter --name

# Empty and delete a staging environment's temporary S3 bucket

aws s3 rm s3://edu-platform-staging-temp --recursive
aws s3api delete-bucket --bucket edu-platform-staging-temp
\`\`\`

---

# 20. CI/CD Integration

## GitHub Actions

- Used for infrastructure (Terraform) and application pipelines: lint → test → plan/build → security scan → deploy.
- OIDC federation between GitHub Actions and AWS IAM eliminates the need for long-lived AWS access keys stored as GitHub secrets.

## GitLab / Jenkins

- Where institutions require on-premises CI due to procurement policy, the same pipeline stages are replicated in GitLab CI or Jenkins, authenticating to AWS via short-lived STS credentials rather than static keys.

## AWS CodePipeline

- Used as the deployment orchestrator for the ECS blue-green rollout stage specifically, integrating with CodeDeploy for traffic shifting, regardless of which CI system builds the artifact.

## Terraform Pipeline

1. `terraform fmt -check` and `terraform validate`.
2. `tflint` and `checkov` (policy-as-code security scanning).
3. `terraform plan` with output posted to the PR.
4. Manual approval gate for production applies.
5. `terraform apply` executed by a dedicated CI role with no human-held credentials.

## Validation

- Automated integration tests run against a staging environment that mirrors production topology (at reduced scale) before any production deployment is eligible.

## Security Scanning

- Container images scanned by Amazon ECR image scanning (Inspector-powered) and a software composition analysis tool for dependency vulnerabilities before push to the production image repository.

## Policy as Code

- Checkov and custom OPA (Open Policy Agent) rules enforce guardrails: no public S3 buckets, no unencrypted RDS instances, no IAM policies with `Resource: "*"` outside explicitly approved exceptions.

## Rollback

- Any deployment that trips a CloudWatch alarm during the CodeDeploy traffic shift is automatically rolled back without human intervention; Terraform infrastructure rollback is handled via `git revert` and re-apply, never manual console changes.

---

# 21. Monitoring

## CloudWatch

- Central metrics and alarming platform across all layers: infrastructure, application, and business-level (e.g., submission success rate).

## Dashboards

- Separate dashboards for: platform health (infra-level), academic-event readiness (pre-exam capacity checks), and executive/business metrics (active users, submission volume).

## Metrics

- Golden signals tracked per service: latency, traffic, errors, saturation — supplemented with education-specific metrics: submission success rate, video start failure rate, grading completion time.

## Logs

- Structured JSON logging from all Fargate services and Lambda functions, shipped to CloudWatch Logs and archived to S3 for long-term retention and Athena querying.

## Tracing

- AWS X-Ray traces requests across ALB → Fargate → Aurora/DynamoDB → Lambda boundaries, essential for diagnosing latency in the multi-hop grading pipeline.

## X-Ray

- Service map view used during incident response to quickly identify which hop in the submission/grading chain is contributing the most latency or errors.

## Alarms

- Tiered severity: Sev-1 alarms (auth failure, database unavailability) page on-call immediately; Sev-2 (elevated latency) notify the team channel; Sev-3 (informational) logged for trend review.

## Notifications

- PagerDuty (or Amazon SNS-integrated equivalent) for Sev-1/Sev-2; Slack integration for Sev-3 and deployment notifications.

## SLIs

- API availability, API latency (p95/p99), video start success rate, submission processing success rate.

## SLOs

- 99.9% API availability monthly; p95 API latency under 200ms; 99.5% submission processing success rate within 5 minutes of upload.

## Error Budgets

- Error budget consumption tracked per SLO; feature deployment velocity is deliberately throttled when error budget for a critical SLO (e.g., submission processing) is nearly exhausted, prioritizing reliability work instead.

---

# 22. Logging

## Centralized Logging

- All application and infrastructure logs aggregated into a centralized logging account, separate from the workload accounts, to preserve log integrity even if a workload account is compromised.

## CloudWatch Logs

- Primary destination for real-time log ingestion from ECS and Lambda; retention set per log group based on compliance requirements (typically 1 year for application logs, longer for audit-relevant logs).

## S3

- Long-term log archival destination; CloudWatch Logs subscription filters or export tasks move logs to S3 for cost-effective long-term retention beyond CloudWatch's economical retention window.

## Athena

- Used for ad hoc querying of archived logs during compliance audits or post-incident forensic analysis, querying directly against S3-stored logs without needing to reload them into a live system.

## OpenSearch

- Used for operational log search and dashboards where near-real-time full-text search across logs is needed (distinct from the OpenSearch instance used for course content search).

## Retention

- Application logs: 1 year. Audit logs (CloudTrail, access logs to student records): minimum 6 years, aligned with the longer end of institutional record-retention requirements.

## Audit Logging

- Every read and write to student PII is logged with the accessing identity, timestamp, and accessed resource — a direct FERPA requirement for demonstrating appropriate access control.

---

# 23. Operational Excellence

## Runbooks

- Documented, versioned runbooks for the highest-probability incidents: database failover, elevated submission failure rate, CDN cache poisoning, live-stream ingestion failure.

## Automation

- Routine operational tasks (tenant onboarding, certificate renewal validation, scheduled scaling ahead of known academic events) are automated rather than manually executed, reducing both toil and the chance of human error during high-stakes windows.

## Patch Management

- Container base images rebuilt and redeployed on a fixed weekly cadence plus emergency out-of-band patching for critical CVEs; any residual EC2 footprint patched via Systems Manager Patch Manager on a defined maintenance schedule.

## Maintenance

- Scheduled maintenance windows are deliberately planned around the academic calendar, never during exam weeks or enrollment periods, and always executed via zero-downtime deployment mechanisms.

## Incident Response

- Formal incident severity classification, a defined on-call rotation, and blameless post-incident reviews for every Sev-1/Sev-2 incident, with action items tracked to completion.

## Change Management

- All production changes flow through the CI/CD pipeline with peer review; emergency changes use an expedited but still-logged and still-reviewed break-glass process — never a manual console change without a paper trail.

---

# 24. Failure Scenarios

1. **Aurora writer instance failure during grade-submission deadline**
   - *Symptoms:* Elevated write latency, then connection errors from application services.
   - *Root cause:* Underlying hardware failure or resource exhaustion on the writer instance.
   - *Detection:* CloudWatch database connection alarm, Aurora failover event notification.
   - *Resolution:* Automated Multi-AZ failover promotes a replica; application reconnects via the cluster endpoint automatically.
   - *Prevention:* Right-sized instance class ahead of known peak periods; connection pooling via RDS Proxy to reduce connection-storm impact during failover.
2. **DynamoDB hot partition during a synchronized live poll**
   - *Symptoms:* Throttled requests, elevated latency for a subset of poll responses.
   - *Root cause:* Poor partition key design causing all responses for one lecture session to land on a single partition.
   - *Detection:* CloudWatch `ThrottledRequests` alarm on the affected table.
   - *Resolution:* Redesign partition key to include a random suffix or shard identifier for high-fan-in write patterns.
   - *Prevention:* Load-test partition key design against realistic synchronized-event traffic before launch.
3. **CloudFront cache poisoning from a misconfigured cache key**
   - *Symptoms:* Students seeing another tenant's course content briefly.
   - *Root cause:* Cache key does not include tenant identifier as a distinguishing factor for a personalized/tenant-scoped path.
   - *Detection:* Customer-reported incident; automated tenant-isolation canary tests.
   - *Resolution:* Immediate cache invalidation; fix cache policy to include the tenant header/cookie in the cache key.
   - *Prevention:* Mandatory review of cache key configuration for any new tenant-scoped endpoint.
4. **MediaLive channel failure during a scheduled live lecture**
   - *Symptoms:* Live stream freezes or drops for all viewers.
   - *Root cause:* Input source loss or channel-level failure.
   - *Detection:* MediaLive input loss CloudWatch alarm.
   - *Resolution:* Standard (redundant) channel pipeline automatically fails over to the second pipeline.
   - *Prevention:* Always use standard (dual-pipeline) MediaLive channels for scheduled, high-stakes live sessions rather than single-pipeline channels.
5. **SQS dead-letter queue accumulation for grading jobs**
   - *Symptoms:* Students report submissions not being graded.
   - *Root cause:* A malformed submission payload consistently fails Lambda processing, repeatedly retrying and eventually landing in the DLQ.
   - *Detection:* CloudWatch DLQ depth alarm.
   - *Resolution:* Investigate and fix the malformed-payload handling bug; replay DLQ messages after the fix is deployed.
   - *Prevention:* Input validation at the point of submission, before the message ever enters the processing queue.
6. **Cognito federation failure with an institutional SAML IdP**
   - *Symptoms:* An entire school district's users unable to log in.
   - *Root cause:* Institutional IdP certificate rotation not reflected in the Cognito SAML configuration.
   - *Detection:* Spike in federated authentication failures for a specific tenant.
   - *Resolution:* Update the SAML metadata/certificate in the Cognito identity provider configuration.
   - *Prevention:* Automated monitoring of upcoming IdP certificate expiration dates, with proactive outreach to institutional IT contacts.
7. **NAT Gateway bandwidth exhaustion during mass video upload window**
   - *Symptoms:* Elevated latency for outbound calls from application services (e.g., third-party plagiarism-scan API calls).
   - *Root cause:* A single NAT Gateway saturated by concurrent outbound traffic.
   - *Detection:* CloudWatch NAT Gateway bandwidth metrics approaching limits.
   - *Resolution:* Traffic naturally distributes once one-NAT-Gateway-per-AZ is correctly configured; if still saturated, review whether traffic should instead route via VPC endpoints.
   - *Prevention:* Use VPC endpoints for AWS service traffic (S3, DynamoDB, etc.) so it never counts against NAT Gateway bandwidth in the first place.
8. **ECS task definition rollout with a broken health check**
   - *Symptoms:* New deployment stuck, tasks continuously cycling.
   - *Root cause:* A code change broke the `/health` endpoint's dependency check.
   - *Detection:* CodeDeploy deployment failure alarm; ECS service event log showing repeated task failures.
   - *Resolution:* Automatic rollback via CodeDeploy alarm-based rollback configuration.
   - *Prevention:* Health endpoint changes require the same review rigor as any other production code path; smoke tests in staging must include health-check verification.
9. **Aurora storage growth approaching cluster limits**
   - *Symptoms:* Slower write performance as storage auto-scaling struggles to keep pace with growth rate.
   - *Root cause:* Unbounded growth of an audit-log table without archival.
   - *Detection:* CloudWatch storage growth trend alarm.
   - *Resolution:* Archive old audit records to S3 and purge from the live table.
   - *Prevention:* Define and automate a data archival policy for high-growth tables from day one.
10. **S3 request rate throttling on the submissions bucket during deadline spike**
    - *Symptoms:* Elevated 503 SlowDown errors on submission uploads in the final minutes before a deadline.
    - *Root cause:* All submissions landing under a single prefix, exceeding S3's per-prefix request rate before automatic partitioning catches up.
    - *Detection:* Application-level error rate spike correlated with S3 503 responses.
    - *Resolution:* Implement client-side retry with exponential backoff; review and adjust key-prefix design to better distribute request load.
    - *Prevention:* Design S3 key prefixes with sufficient entropy (e.g., hashed student ID prefix) ahead of known high-fan-in write events.
11. **WAF rule falsely blocking legitimate submission uploads**
    - *Symptoms:* A subset of students unable to submit assignments containing certain file types or content.
    - *Root cause:* An overly aggressive managed WAF rule flagging legitimate binary content as a potential exploit payload.
    - *Detection:* WAF sampled request logs showing blocked legitimate traffic; student support ticket spike.
    - *Resolution:* Add a scoped exception rule for the affected endpoint/content type.
    - *Prevention:* Run new/updated WAF managed rule groups in "count" mode against production traffic before switching to "block" mode.
12. **Cross-tenant data leakage due to missing row-level security policy on a new table**
    - *Symptoms:* An administrator from one district reports seeing another district's roster data.
    - *Root cause:* A newly added database table was not included in the standard row-level security policy rollout.
    - *Detection:* Customer-reported; ideally caught earlier by automated tenant-isolation test suite.
    - *Resolution:* Immediately apply row-level security policy; audit logs to determine scope of exposure; notify affected tenants per incident response and breach notification obligations.
    - *Prevention:* Row-level security policy application is a mandatory, automated step in the database migration pipeline for any new table containing tenant-scoped data.
13. **Live-class video buffering during a large synchronized viewing event**
    - *Symptoms:* Widespread buffering complaints during a state-wide broadcast lecture.
    - *Root cause:* CloudFront cache miss storm at stream start, overwhelming the MediaPackage origin.
    - *Detection:* CloudFront origin latency and error rate spike at stream start time.
    - *Resolution:* Pre-warm CloudFront cache ahead of scheduled large-audience events; increase MediaPackage origin capacity headroom.
    - *Prevention:* Treat any broadcast-scale live event as a capacity-planning exercise with a pre-event readiness checklist, not a routine live stream.
14. **Lambda concurrency limit reached during grading burst**
    - *Symptoms:* Grading Lambda invocations throttled; SQS queue depth grows.
    - *Root cause:* Account-level or reserved concurrency limit reached during an unusually large synchronized deadline event.
    - *Detection:* CloudWatch `Throttles` metric on the grading Lambda function.
    - *Resolution:* Request a service quota increase ahead of known large events; temporarily raise reserved concurrency for the affected function.
    - *Prevention:* Track Lambda concurrency headroom against projected peak enrollment growth as part of quarterly capacity review.
15. **Secrets Manager rotation breaking database connectivity**
    - *Symptoms:* Application services suddenly unable to connect to Aurora after a scheduled credential rotation.
    - *Root cause:* Rotation Lambda failed partway through, leaving credentials in an inconsistent state.
    - *Detection:* Spike in database authentication failures immediately following a scheduled rotation window.
    - *Resolution:* Manually complete or roll back the rotation using the previous credential version retained by Secrets Manager.
    - *Prevention:* Test rotation Lambda functions thoroughly in staging; schedule rotations outside peak traffic windows with monitoring specifically watching the rotation event.

---

# 25. Troubleshooting Guide

| Problem | Symptoms | Likely Cause | Diagnosis | AWS CLI Commands | Resolution |
|---|---|---|---|---|---|
| Students can't log in | 401/403 errors at login | Cognito federation misconfiguration or IdP outage | Check Cognito federation logs, verify IdP metadata | `aws cognito-idp describe-identity-provider --user-pool-id <id> --provider-name <name>` | Update SAML metadata; contact institutional IdP admin if issue is upstream |
| Video won't play | Buffering, playback error | MediaPackage origin failure or CloudFront cache miss storm | Check MediaPackage endpoint health, CloudFront origin error rate | `aws mediapackage describe-origin-endpoint --id <id>` | Fail over to backup MediaLive pipeline; pre-warm cache for future events |
| Submissions failing | 5xx errors on submit | S3 throttling or Aurora write contention | Check S3 503 rate, Aurora write latency | `aws s3api get-bucket-metrics-configuration --bucket <bucket>` | Adjust key prefix design; scale Aurora writer ahead of time |
| Grades not posting | Grading stuck "in progress" | SQS DLQ accumulation | Check DLQ message count | `aws sqs get-queue-attributes --queue-url <dlq-url> --attribute-names ApproximateNumberOfMessages` | Fix processing bug; replay DLQ messages |
| Slow API responses | p95 latency alarm firing | Aurora connection exhaustion or Fargate under-scaled | Check RDS Proxy connection count, ECS CPU utilization | `aws rds describe-db-proxy-targets --db-proxy-name <name>` | Scale Fargate service; verify RDS Proxy configuration |
| Cross-tenant data visible | Customer-reported data leak | Missing row-level security policy | Audit table policies against tenant isolation checklist | `aws rds-data execute-statement --resource-arn <arn> --sql "SELECT ..."` (via audit script) | Apply RLS policy immediately; run breach assessment |
| High AWS bill anomaly | Cost Anomaly Detection alert | Runaway Lambda retry loop or unoptimized CloudFront cache | Review Cost Explorer by service, check Lambda invocation counts | `aws ce get-cost-and-usage --time-period Start=...,End=... --granularity DAILY --metrics "UnblendedCost"` | Fix retry logic; add DLQ; review cache TTL settings |
| Live stream frozen for all viewers | Zero playback across all users | MediaLive input loss | Check MediaLive channel alarm state | `aws medialive describe-channel --channel-id <id>` | Failover to redundant pipeline; verify encoder input source |
| Deployment stuck | ECS tasks cycling | Broken health check endpoint | Check ECS service events, task stopped reason | `aws ecs describe-services --cluster <cluster> --services <service>` | Roll back via CodeDeploy; fix health check dependency |
| Notification not delivered | Students report missing email/push | SNS delivery failure or Lambda error | Check SNS delivery status logs, Lambda error rate | `aws sns get-topic-attributes --topic-arn <arn>` | Investigate SNS delivery logging; fix downstream Lambda bug |

---

# 26. Best Practices

1. Enforce tenant isolation at both the application layer and the database layer (row-level security) — never rely on a single layer alone.
2. Use Multi-AZ for every stateful component; single-AZ deployments are not acceptable for production education platforms.
3. Treat the academic calendar as a first-class input to capacity planning — schedule pre-scaling ahead of known peak events.
4. Never process video transcoding synchronously in the request path; always route through the event-driven media pipeline.
5. Use RDS Proxy in front of Aurora for any Lambda function that connects to the database, to avoid connection exhaustion.
6. Apply least-privilege IAM policies scoped to specific resource ARNs, not wildcard resources, for every service role.
7. Store all secrets in Secrets Manager with automatic rotation enabled; never hardcode credentials in task definitions.
8. Use signed URLs or signed cookies for all video content delivery to prevent unauthorized access to lecture recordings.
9. Enable GuardDuty Malware Protection on any S3 bucket that accepts user-uploaded files.
10. Design DynamoDB partition keys with synchronized-event traffic patterns explicitly in mind.
11. Implement automated tenant-isolation canary tests that run continuously against production.
12. Use blue-green deployments with automated alarm-based rollback for every production release.
13. Never schedule maintenance windows during exam periods or enrollment weeks.
14. Tag every resource with tenant tier, environment, and cost center for accurate cost attribution.
15. Use CloudFront cache behaviors that explicitly separate static, API, and video content — never a single catch-all behavior.
16. Enforce MFA for all instructor and administrator accounts at minimum.
17. Log every access to student PII with sufficient detail to satisfy a FERPA audit request.
18. Use Aurora Global Database for the highest-tier tenant contracts requiring near-zero RPO.
19. Validate all file uploads for type and content before they enter the processing pipeline, not after.
20. Keep Lambda functions single-purpose; use Step Functions for multi-stage workflows rather than Lambda-to-Lambda chaining.
21. Run WAF managed rule group updates in count mode before switching to block mode.
22. Use S3 lifecycle policies to move aging video content to cheaper storage tiers automatically.
23. Separate the CI/CD deployment IAM role from any human-usable role.
24. Require peer review on every Terraform change, with plan output visible in the pull request.
25. Use scheduled Auto Scaling actions to pre-provision capacity ahead of known academic events, supplementing reactive scaling.
26. Test disaster recovery failover at least twice per year, not just document it.
27. Use permission boundaries on any IAM role capable of creating other roles or policies.
28. Maintain separate AWS accounts per environment (dev/staging/production), never separate only by naming convention within a single account.
29. Track error budgets per SLO and throttle feature velocity when a critical SLO's error budget is nearly exhausted.
30. Review and rightsize Aurora and Fargate resource allocation quarterly using AWS Compute Optimizer recommendations.
31. Ensure accessibility (WCAG 2.1 AA) is validated as part of the standard QA process, not treated as a post-launch retrofit.
32. Build institutional SSO onboarding as a repeatable, templated Terraform module rather than a one-off manual configuration per tenant.

---

# 27. Anti-Patterns

1. **Single shared database schema with an application-level `WHERE tenant_id = ?` filter as the only isolation control.** Dangerous because any missed filter in any query path leaks cross-tenant data. Correct approach: layer row-level security beneath the application filter.
2. **Provisioning Aurora and Fargate for peak capacity year-round.** Dangerous because it wastes significant budget during the many weeks that aren't exam or enrollment periods. Correct approach: scheduled and reactive Auto Scaling aligned to the academic calendar.
3. **Processing video transcoding synchronously inside the upload API request.** Dangerous because it ties API availability to transcoding job duration and blocks the request thread for minutes. Correct approach: asynchronous event-driven media pipeline.
4. **Storing student PII in DynamoDB tables without encryption or fine-grained IAM access controls.** Dangerous because DynamoDB's flexible schema makes it easy to add sensitive fields without the same review rigor applied to relational schema changes. Correct approach: treat any table touching PII with the same encryption and access-control review as Aurora.
5. **Using a single NAT Gateway for the entire VPC.** Dangerous because it creates a cross-AZ single point of failure and cost inefficiency. Correct approach: one NAT Gateway per AZ.
6. **Hardcoding institutional SAML certificates directly in application code.** Dangerous because certificate rotation requires a code deployment instead of a configuration update, guaranteeing eventual outages at rotation time. Correct approach: store and manage federation configuration in Cognito, updated via Secrets Manager/Parameter Store.
7. **Allowing WAF managed rules straight to block mode without a count-mode observation period.** Dangerous because it can silently block legitimate student traffic (e.g., large file uploads) without warning. Correct approach: observe in count mode, review sampled requests, then switch to block.
8. **Treating disaster recovery as documentation rather than a tested capability.** Dangerous because an untested DR plan reliably fails exactly when it's needed most. Correct approach: scheduled DR failover drills with measured RTO/RPO against documented targets.
9. **Granting broad `s3:*` permissions to application task roles "to save time."** Dangerous because it violates least privilege and expands blast radius if the service is compromised. Correct approach: scope to specific bucket ARNs and specific actions.
10. **Deploying schema migrations that aren't backward-compatible with the currently running application version.** Dangerous because it breaks blue-green deployment's ability to run two versions simultaneously during traffic shift. Correct approach: expand/contract migration pattern.
11. **Relying solely on CloudFront default caching behavior for personalized API responses.** Dangerous because it can serve one student's data to another. Correct approach: explicit no-cache behavior for personalized/authenticated endpoints.
12. **Skipping load testing for synchronized-event traffic patterns (all students logging in at 8:00 AM sharp).** Dangerous because average-load testing doesn't reveal partition hot-spotting or connection-pool exhaustion under true burst conditions. Correct approach: load test explicitly simulating synchronized peak events.
13. **Using long-lived IAM access keys for CI/CD pipelines.** Dangerous because static credentials are a persistent theft target. Correct approach: OIDC federation with short-lived STS credentials.
14. **Ignoring accessibility requirements until a compliance complaint arrives.** Dangerous because it risks legal exposure for institutions serving public education and damages procurement relationships. Correct approach: WCAG 2.1 AA validation baked into the standard QA gate.
15. **Building custom video transcoding infrastructure instead of using MediaConvert/MediaLive.** Dangerous because it diverts engineering effort into solving an already-solved problem with worse reliability characteristics. Correct approach: use the managed media pipeline unless there's a truly novel requirement it can't satisfy.
16. **Allowing manual console changes to production infrastructure "just this once."** Dangerous because it creates drift between actual infrastructure state and the Terraform-defined state, undermining the reliability of future `plan`/`apply` operations. Correct approach: all changes through the reviewed CI/CD pipeline, even emergencies.
17. **Co-mingling audit logs with application debug logs in the same low-retention log group.** Dangerous because it risks losing compliance-relevant audit trail data to a short retention policy set for noisy debug logs. Correct approach: separate log groups with retention policies matched to their compliance requirements.
18. **Treating Lambda reserved concurrency as a "set once" configuration.** Dangerous because unreviewed concurrency limits become a silent bottleneck as tenant count and usage grow. Correct approach: review concurrency headroom as part of quarterly capacity planning.
19. **Deploying new features directly to 100% of traffic without canary/blue-green traffic shifting.** Dangerous because it maximizes blast radius of any regression, particularly costly during high-stakes academic windows. Correct approach: incremental traffic shifting with automated alarm-based rollback.
20. **Assuming Cognito's default settings meet COPPA requirements for K-12 deployments without explicit configuration review.** Dangerous because COPPA imposes specific parental consent and data-minimization requirements that aren't automatically satisfied by generic identity provider defaults. Correct approach: explicit compliance review of identity flows for any deployment serving children under 13.

---

# 28. Alternatives

## Alternative 1: Monolithic LMS on EC2 (Self-Managed)

- **Advantages:** Simpler mental model for small teams; full control over the stack.
- **Disadvantages:** Manual scaling, patching burden, poor fit for bursty academic traffic.
- **Cost:** Lower at very small scale; higher at moderate-to-large scale due to over-provisioning for peaks.
- **Operational complexity:** High — requires dedicated infrastructure ops staff.
- **Security:** Entirely the customer's responsibility, including patching and hardening.
- **Performance:** Acceptable at small scale; degrades sharply under synchronized load without significant manual intervention.

## Alternative 2: Fully Serverless (API Gateway + Lambda + DynamoDB only, no Fargate)

- **Advantages:** Near-zero idle cost; scales automatically to extreme bursts.
- **Disadvantages:** Complex relational reporting (gradebook analytics, transcript generation) is awkward on DynamoDB alone; Lambda cold starts can affect latency-sensitive interactive paths.
- **Cost:** Very low at low-to-moderate scale; can become expensive at sustained high, steady traffic compared to Fargate.
- **Operational complexity:** Lower infrastructure management, but higher application-level complexity to work around DynamoDB's query limitations.
- **Security:** Comparable; smaller network attack surface since there's no persistent compute to harden.
- **Performance:** Excellent for bursty, sporadic access; less ideal for latency-sensitive, sustained interactive sessions.

## Alternative 3: Kubernetes (EKS) Instead of ECS Fargate

- **Advantages:** Greater portability, richer ecosystem, useful if the organization already runs Kubernetes elsewhere.
- **Disadvantages:** Meaningfully higher operational overhead (cluster upgrades, node management even with managed node groups) for a workload that doesn't require Kubernetes-specific capabilities.
- **Cost:** Cluster management overhead adds cost and engineering time versus Fargate's fully managed model.
- **Operational complexity:** Substantially higher; requires dedicated platform engineering expertise.
- **Security:** Comparable if properly configured, but a larger surface area (cluster control plane, node OS) to secure.
- **Performance:** Comparable to Fargate for this workload profile; the difference is operational, not performance-driven.

## Alternative 4: Third-Party SaaS LMS (Canvas, Moodle Cloud, Blackboard) Instead of Building

- **Advantages:** Fastest time to market; no infrastructure to manage.
- **Disadvantages:** Limited customization, per-seat licensing costs that scale linearly with growth, less control over data residency and compliance posture.
- **Cost:** Lower upfront engineering cost; can become more expensive at scale due to per-seat licensing versus infrastructure-based cost scaling.
- **Operational complexity:** Lowest — vendor-managed.
- **Security:** Dependent entirely on the vendor's security posture and compliance certifications.
- **Performance:** Generally adequate, but customization for unique institutional workflows is constrained by the vendor's platform.

## Alternative 5: Multi-Cloud (AWS + a secondary cloud for video delivery)

- **Advantages:** Theoretical reduction in single-vendor dependency; potential cost arbitrage on egress-heavy video delivery.
- **Disadvantages:** Significant operational complexity maintaining two sets of tooling, security controls, and compliance evidence; identity federation and data synchronization across clouds add substantial engineering overhead.
- **Cost:** Rarely nets out cheaper once the added engineering and operational overhead is accounted for, except at very specific, very large egress volumes.
- **Operational complexity:** Highest of all alternatives listed.
- **Security:** Doubles the compliance and security surface area to manage and audit.
- **Performance:** Can improve regional video delivery in specific cases, but the operational cost usually outweighs the benefit for most institutional-scale deployments.

## Comparative Summary

| Alternative | Cost | Complexity | Best Fit |
|---|---|---|---|
| This architecture (ECS Fargate + serverless hybrid) | Moderate | Moderate | Growing multi-tenant EdTech platforms with compliance requirements |
| Monolithic EC2 | Low (small scale) | Low initially, high at scale | Very small, single-institution deployments |
| Fully serverless | Low | Moderate (application-level) | Highly bursty, lower-complexity reporting needs |
| Kubernetes (EKS) | Moderate-High | High | Organizations with existing Kubernetes investment/expertise |
| Third-party SaaS LMS | Low upfront, scales with seats | Lowest | Institutions without engineering capacity to build/operate a platform |
| Multi-cloud | High | Highest | Rarely justified for this workload profile |

---

# 29. Real Enterprise Case Study

## Company Profile

**Meridian Learning Collective** is a fictional composite EdTech provider serving 180 school districts and 40 higher-education partners across the United States, supporting approximately 620,000 active student accounts at peak enrollment.

## Business Problem

Meridian's original platform was a single-region, single-database Rails monolith on EC2, built when the company served 12 pilot districts. As the customer base grew to over 150 districts, the platform experienced:

- Repeated outages during the first week of each academic term, when nearly every district's students logged in within the same two-hour window.
- Video lecture delivery latency complaints from districts in regions far from the single hosting region.
- A near-miss data isolation incident during a database migration where a query bug briefly exposed one district's roster data to another district's administrator account.
- Rising infrastructure costs driven by permanent over-provisioning for peak capacity that was only needed a handful of weeks per year.

## Architecture Decisions

- Migrated from a single EC2 monolith to the ECS Fargate + serverless hybrid architecture described in this chapter.
- Adopted Aurora PostgreSQL with row-level security as a mandatory, non-optional layer beneath every tenant-scoped table.
- Moved video delivery to CloudFront + MediaConvert/MediaPackage, replacing a self-hosted transcoding pipeline.
- Implemented Cognito with SAML federation to replace a custom-built authentication system, reducing time-to-onboard new district SSO integrations from weeks to days.
- Introduced scheduled Auto Scaling aligned to each customer district's published academic calendar.

## Migration

- Executed as a phased, dual-write migration over five months: new tenants onboarded directly to the new architecture, while existing tenants were migrated in scheduled batches during low-traffic summer months.
- Legacy and new systems ran in parallel with a feature-flag-controlled traffic router directing each district to the appropriate backend during the transition.

## Challenges

- Underestimated the engineering effort required to retrofit row-level security onto an existing schema originally designed without tenant isolation as a first-class concern.
- Initial DynamoDB partition key design for live-poll data caused hot-partition throttling during the first large-scale live event on the new platform, requiring a rapid redesign.
- Coordinating migration scheduling across 180 districts' independently set academic calendars required a dedicated migration project management function.

## Lessons Learned

- Tenant isolation retrofits are dramatically more expensive than designing for isolation from day one — this became the strongest internal argument for treating the pattern as mandatory going forward.
- Load testing must simulate synchronized-event traffic patterns explicitly; generic average-load testing had not revealed the DynamoDB hot-partition risk.
- Academic calendar data became a genuinely strategic input to infrastructure capacity planning, not just a product feature.

## Results

- Eliminated term-start outages across two full academic years following migration completion.
- Reduced infrastructure cost per active student by a meaningful margin by replacing permanent peak provisioning with scheduled and reactive Auto Scaling.
- Reduced new-district SSO onboarding time from multiple weeks to under five business days.
- Passed a subsequent SOC 2 Type II audit and multiple district-level FERPA compliance reviews without material findings related to tenant data isolation.

---

# 30. Architecture Decision Record (ADR)

**ADR-075: Adopt ECS Fargate + Serverless Hybrid Architecture for the Education Platform**

## Context

The platform must serve a multi-tenant base of school districts, universities, and corporate learning customers with highly variable, calendar-driven traffic patterns, strict data isolation requirements, and FERPA/COPPA compliance obligations. The previous single-region EC2 monolith could not reliably absorb term-start and exam-week traffic spikes and lacked robust tenant isolation.

## Decision

Adopt a layered architecture combining ECS Fargate for core stateful-interaction services, Lambda for event-driven asynchronous processing, Aurora PostgreSQL with row-level security for transactional data, DynamoDB for high-throughput real-time interaction data, and the AWS Elemental media pipeline for video delivery — all fronted by CloudFront and WAF.

## Alternatives Considered

- Monolithic EC2 (rejected — does not meet scalability or tenant-isolation requirements).
- Fully serverless, Fargate-free architecture (rejected — poor fit for the relational reporting needs of gradebook/transcript features).
- Kubernetes/EKS (rejected — operational overhead not justified given no existing Kubernetes expertise or portability requirement).
- Third-party SaaS LMS (rejected — insufficient customization and data-residency control for the company's product differentiation strategy).

## Consequences

- **Positive:** Elastic scaling aligned to academic calendar events; strong tenant isolation via defense-in-depth (application + database layer); reduced infrastructure cost per active student; faster compliance audit outcomes due to built-in audit logging and encryption.
- **Negative:** Increased architectural complexity relative to the previous monolith, requiring investment in platform engineering expertise; migration effort for existing tenants; deeper AWS-specific service dependency (vendor lock-in trade-off).

## Risks

- Underestimating tenant isolation retrofit effort during migration (mitigated by phased migration and dedicated isolation test suite).
- Team unfamiliarity with event-driven debugging patterns initially increasing incident resolution time (mitigated by X-Ray tracing investment and runbook development).

## Review Date

This ADR is scheduled for review 18 months after initial production adoption, or sooner if a material change in tenant scale, compliance scope, or AWS service capability warrants reassessment.

---

# 31. Architecture Review Checklist

## Security

- [ ] All data encrypted at rest and in transit.
- [ ] Row-level security enforced on every tenant-scoped database table.
- [ ] WAF and Shield Advanced enabled on production CloudFront/ALB.
- [ ] GuardDuty Malware Protection enabled on user-upload buckets.
- [ ] No IAM policy uses wildcard resources without documented exception approval.
- [ ] Secrets Manager rotation enabled for all database credentials.

## Networking

- [ ] Application and data tiers reside exclusively in private subnets.
- [ ] One NAT Gateway per Availability Zone.
- [ ] VPC endpoints configured for S3, DynamoDB, Secrets Manager, KMS.
- [ ] Security groups follow least-privilege, layered access model.

## Operations

- [ ] Blue-green deployment with automated alarm-based rollback configured.
- [ ] Runbooks exist for the highest-probability incident scenarios.
- [ ] Disaster recovery failover tested within the last 6 months.
- [ ] Maintenance windows scheduled outside academic peak periods.

## Performance

- [ ] Load testing performed against synchronized-event traffic patterns.
- [ ] CloudFront cache behaviors correctly separate static, API, and video content.
- [ ] RDS Proxy configured for Lambda-to-Aurora connections.

## Scalability

- [ ] Scheduled Auto Scaling configured ahead of known academic calendar events.
- [ ] DynamoDB partition key design validated against high-fan-in write scenarios.
- [ ] Lambda concurrency headroom reviewed against peak projection.

## Reliability

- [ ] Multi-AZ deployment confirmed for all stateful components.
- [ ] RPO/RTO targets documented and validated per tenant tier.
- [ ] Dead-letter queues configured with alarms for every processing queue.

## Cost

- [ ] Resources tagged with tenant tier, environment, and cost center.
- [ ] S3 lifecycle policies configured for aging video content.
- [ ] Cost Anomaly Detection enabled at the account level.

## Compliance

- [ ] Audit logging captures all access to student PII.
- [ ] FERPA/COPPA-relevant AWS Config conformance packs enabled.
- [ ] Data retention and deletion workflows documented and automated.
- [ ] Accessibility (WCAG 2.1 AA) validated as part of the QA process.

---

# 32. Summary

## Business Value

This architecture converts an inherently spiky, compliance-sensitive workload into a platform that scales automatically with the academic calendar rather than fighting it. It reduces infrastructure cost per active student, protects against tenant data isolation failures through defense-in-depth, and provides the audit trail and encryption posture required for FERPA/COPPA-driven procurement requirements.

## Key Architecture Decisions

- ECS Fargate for stateful interactive services; Lambda for event-driven asynchronous processing.
- Aurora PostgreSQL with row-level security as a mandatory tenant isolation layer beneath application-level checks.
- DynamoDB for high-throughput, real-time interaction data during synchronized live events.
- Managed AWS Elemental media pipeline instead of custom-built video transcoding.
- Cognito with institutional SAML/OIDC federation for identity.

## Lessons Learned

- Tenant isolation must be designed in from the start; retrofitting it later is significantly more expensive and risky.
- Load testing must explicitly simulate synchronized academic events, not just average traffic growth.
- The academic calendar is a genuine architectural input, not just a scheduling detail.

## When to Use

- Multi-tenant EdTech platforms serving school districts, universities, or corporate L&D customers with compliance obligations and calendar-driven traffic spikes.
- Platforms delivering significant video content alongside transactional course/grading data.

## When Not to Use

- Very small, single-institution deployments where a third-party SaaS LMS or a simpler monolithic deployment meets requirements at lower engineering cost.
- Organizations without the platform engineering capacity to operate an event-driven, multi-service architecture responsibly.

---

# 33. Further Reading

- AWS Well-Architected Framework — https://aws.amazon.com/architecture/well-architected/
- AWS Whitepaper: Multi-Tenant SaaS Storage Strategies
- AWS Elemental MediaConvert Documentation
- AWS Elemental MediaLive Documentation
- Amazon Cognito Developer Guide — SAML and OIDC Federation
- Aurora PostgreSQL Row-Level Security documentation
- U.S. Department of Education — FERPA guidance
- Federal Trade Commission — COPPA compliance guidance
- Terraform AWS Provider Documentation
- AWS Reference Architecture Handbook — Chapter 22, CloudFront Edge Architecture (related content-delivery patterns)
- AWS Reference Architecture Handbook — Chapter 27, Lambda Microservices (related event-driven service patterns)
- AWS Reference Architecture Handbook — Chapter 59, SaaS Multi-Tenant (related tenancy model patterns)

---

# 34. Architect's Corner

## Why This Architecture Exists

- Experienced architects don't choose this pattern because it's fashionable — they choose it because education traffic breaks simpler designs in specific, predictable ways.
- The academic calendar creates traffic spikes that are far more extreme, and far more synchronized, than typical consumer SaaS traffic. A retail site's Black Friday spike is gradual and somewhat predictable in shape; a school's 8:00 AM login spike happens in minutes, every single school day.
- Simpler designs — a monolith on a fixed EC2 fleet, a single shared database with no isolation layer — fail in two specific ways: they either fall over during the spike, or they stay up only because they're permanently over-provisioned for a peak that happens a few dozen days per year.
- The multi-tenant compliance requirement is what pushes the data layer design specifically. FERPA and COPPA are not abstract concerns for this vertical — they are contractual gating requirements. A district's procurement team will ask, directly, how student data from one school is prevented from being visible to another school's administrators. "We filter by tenant ID in the application code" is not an answer that survives that conversation.
- Video is the other forcing function. Once lecture content and live classes become core to the product, the platform either builds on a mature managed media pipeline or spends enormous, ongoing engineering effort rebuilding what MediaConvert and MediaLive already solve well.

## When You SHOULD Choose This Architecture

- **Organization type:** A multi-tenant EdTech vendor, a state or district-level platform serving multiple schools, a university consolidating many departmental systems, or a corporate L&D platform serving a large, distributed workforce.
- **Company size:** Typically past the "single pilot customer" stage — once there are multiple institutional tenants, or the plan is to have multiple tenants within 12–18 months, the isolation and scaling investment pays for itself.
- **Traffic profile:** Bursty, calendar-driven, with clear peak windows (term start, exam periods, assignment deadlines) rather than smoothly distributed traffic.
- **Engineering maturity:** A team comfortable operating event-driven, multi-service architectures — this is not a "first production system" pattern for a brand-new engineering team.
- **Compliance requirements:** Any deployment touching U.S. K-12 student records, or any deployment with contractual FERPA/COPPA/SOC 2 obligations.
- **Budget considerations:** Organizations that can absorb moderate platform engineering investment in exchange for materially lower marginal cost per additional tenant or student over time.
- **Growth expectations:** Platforms expecting to grow both in tenant count and in per-tenant usage — the architecture is specifically built to scale on both axes independently.

## When You Should NOT Choose This Architecture

- **Unnecessary complexity scenarios:** A single institution building an internal tool for its own students only, with no plan to serve additional tenants, doesn't need multi-tenant isolation machinery — it adds cost and complexity without corresponding benefit.
- **Budget limitations:** Early-stage EdTech startups pre-product-market-fit are often better served by a simpler architecture (or a third-party SaaS LMS) until traffic and compliance requirements justify the investment.
- **Operational overhead:** Teams without dedicated platform engineering capacity will struggle to operate the event-driven debugging surface (SQS, EventBridge, Lambda, multiple data stores) this pattern introduces.
- **Team maturity limitations:** If the team has never operated a production system with asynchronous processing and eventual consistency, introducing all of it at once — multi-tenancy, event-driven processing, and video pipeline complexity — in a single build is a recipe for a rough first year.
- **Lower-cost alternatives:** A third-party SaaS LMS remains the right call for many single-institution deployments where customization and data-residency control aren't strategic differentiators.

## Hidden Trade-offs

- **Operational complexity:** Debugging a failed grade posting now means tracing a request across ALB, Fargate, EventBridge, SQS, and Lambda — a meaningfully larger surface than a single monolith's call stack.
- **Unexpected cloud costs:** Video egress and MediaConvert minutes are easy to underestimate during initial cost modeling and become the dominant line item at scale.
- **Troubleshooting difficulty:** Eventual consistency between the write path (Aurora) and the async processing path (Lambda/SQS) means "I submitted my assignment but don't see my grade yet" requires understanding which stage of the pipeline the submission is currently in.
- **Deployment complexity:** Blue-green deployment across multiple interdependent services requires careful sequencing to avoid API contract breaks between service versions during the traffic shift window.
- **Vendor lock-in:** Deep integration with Cognito, MediaConvert/MediaLive, and Aurora-specific features (row-level security patterns, Aurora Global Database) makes a future migration to another cloud provider a substantial undertaking.
- **Learning curve:** Engineers joining the team need to understand event-driven design patterns, not just typical CRUD API development, before they're fully productive.
- **Security implications:** More moving parts means more IAM roles, more service-to-service trust boundaries, and more surface area for a misconfiguration to introduce a vulnerability.
- **Maintenance burden:** Multiple data stores (Aurora, DynamoDB, OpenSearch) each require their own operational expertise, backup strategy, and monitoring approach.

## Common Architecture Review Questions

1. Why Aurora PostgreSQL instead of a fully serverless database like DynamoDB for all data?
2. Why not build this entirely serverless, with no ECS Fargate at all?
3. Why multiple Availability Zones instead of a single AZ with periodic backups?
4. Why not Kubernetes/EKS, given the industry trend toward it?
5. How exactly are secrets managed and rotated, end to end?
6. How is disaster recovery tested, and how often?
7. How is FERPA compliance demonstrated to an auditor, concretely?
8. How is COPPA parental consent handled for K-12 tenants with students under 13?
9. How is cost monitored and attributed per institutional tenant?
10. What prevents one tenant's data from being visible to another tenant, at every layer?
11. What happens if a MediaLive channel fails during a live, graded exam session?
12. What is the blast radius if a single IAM role is compromised?
13. How are schema migrations performed without downtime during an active academic term?
14. What is the actual measured RTO/RPO, based on the last DR drill, not the documented target?
15. How does the platform handle a synchronized traffic spike larger than any previously observed peak?
16. What is the process for onboarding a new institutional tenant's SSO integration?
17. How are WAF rules validated before being switched from count mode to block mode?
18. What is the data retention and deletion process for a tenant that ends their contract?
19. How is accessibility (WCAG 2.1 AA) validated, and by whom, before each release?
20. What is the plan if AWS Elemental services experience a regional outage during a scheduled live event?

## Production Pitfalls

1. **Problem:** Tenant isolation implemented only at the application query layer.
   **Business impact:** A single bug can leak an entire district's student data, triggering breach notification obligations and contract termination risk.
   **Technical impact:** Requires emergency patching, forensic audit of scope, and often a schema-level remediation under time pressure.
   **Recommended solution:** Row-level security as a mandatory second layer, enforced at the database, not optional.

2. **Problem:** DynamoDB partition keys not designed for synchronized-event write patterns.
   **Business impact:** Live poll or attendance features fail visibly during exactly the highest-visibility moments (a live, proctored session).
   **Technical impact:** Throttled writes, data loss for some responses, emergency partition key redesign under production pressure.
   **Recommended solution:** Explicit synchronized-event load testing before any live-interaction feature ships.

3. **Problem:** Video transcoding cost underestimated in initial financial modeling.
   **Business impact:** Gross margin erosion discovered only after several months of production video volume.
   **Technical impact:** Requires retroactive cost-optimization work (lifecycle policies, job template tuning) under budget pressure.
   **Recommended solution:** Model video cost using realistic minutes-per-student-per-term estimates before committing to pricing with institutional customers.

4. **Problem:** WAF managed rules pushed to block mode without an observation period.
   **Business impact:** Students unable to submit assignments during a deadline window, generating support escalations and trust damage with instructors.
   **Technical impact:** Emergency WAF rule rollback under time pressure.
   **Recommended solution:** Always run new managed rule groups in count mode against real production traffic first.

5. **Problem:** Disaster recovery plan never actually tested end to end.
   **Business impact:** A real regional outage reveals the DR plan doesn't work as documented, extending an outage during a high-stakes period.
   **Technical impact:** Ad hoc, improvised recovery under crisis conditions instead of following a validated runbook.
   **Recommended solution:** Scheduled DR failover drills at least twice a year, treated as seriously as a real incident.

6. **Problem:** Institutional SAML certificate rotation not tracked proactively.
   **Business impact:** An entire district's users locked out of the platform with no warning, often discovered by the district's IT staff before the platform team.
   **Technical impact:** Emergency federation reconfiguration and district relationship damage control.
   **Recommended solution:** Automated monitoring of upcoming IdP certificate expiration with proactive outreach.

7. **Problem:** Lambda functions with no reserved concurrency limits during a large synchronized grading burst.
   **Business impact:** A single tenant's large deadline event can starve other tenants' grading capacity from the shared account concurrency pool.
   **Technical impact:** Cross-tenant noisy-neighbor effect within the shared Lambda concurrency limit.
   **Recommended solution:** Reserved concurrency allocation reviewed against multi-tenant fairness, not just aggregate throughput.

8. **Problem:** Terraform state drift from manual console "quick fixes."
   **Business impact:** Loss of confidence in infrastructure-as-code as the source of truth, slowing future changes as engineers second-guess `terraform plan` output.
   **Technical impact:** Unexpected resource replacement or destruction on the next `apply`.
   **Recommended solution:** Zero-tolerance policy for manual console changes to production, enforced culturally and, where possible, via IAM restrictions.

9. **Problem:** Audit logs for student PII access mixed into general application debug logs with short retention.
   **Business impact:** Inability to produce required access history during a FERPA compliance audit.
   **Technical impact:** Emergency log-retention policy overhaul, potentially with gaps that can't be retroactively filled.
   **Recommended solution:** Dedicated audit log group with compliance-driven retention from day one.

10. **Problem:** No load testing performed against realistic synchronized-login patterns.
    **Business impact:** First real term-start event becomes the load test, in production, with real students affected.
    **Technical impact:** Reactive firefighting and emergency scaling during a highly visible event.
    **Recommended solution:** Synthetic synchronized-load testing as a standard pre-launch and pre-term gate.

11. **Problem:** Video content access not protected with signed URLs, relying only on "security through obscurity" of unlisted CloudFront URLs.
    **Business impact:** Licensed or paywalled lecture content can be shared and accessed outside the intended student population.
    **Technical impact:** Content licensing violations and potential vendor contract breaches for licensed third-party materials.
    **Recommended solution:** Signed URLs/cookies enforced for all non-public video content from day one.

12. **Problem:** Schema migrations that are not backward-compatible deployed alongside application changes in a single release.
    **Business impact:** Failed or partial deployments during a blue-green traffic shift cause visible errors for students mid-shift.
    **Technical impact:** Rollback complexity when the database schema has already changed but the rollback targets the previous application version.
    **Recommended solution:** Strict expand/contract migration discipline, decoupled from application release timing.

13. **Problem:** Cost allocation tags applied inconsistently across resources.
    **Business impact:** Inability to accurately determine per-tenant infrastructure cost, undermining pricing and margin analysis.
    **Technical impact:** Manual, error-prone cost reconciliation work at month-end.
    **Recommended solution:** Enforced tagging policy via AWS Config, validated automatically, not left to convention.

14. **Problem:** Accessibility treated as a post-launch item rather than a build-time requirement.
    **Business impact:** Legal exposure and lost public-sector procurement opportunities where accessibility compliance is a hard requirement.
    **Technical impact:** Expensive retrofit work across the entire UI codebase instead of incremental compliance during initial build.
    **Recommended solution:** WCAG 2.1 AA validation integrated into the standard QA gate from the first release.

15. **Problem:** Single NAT Gateway configuration inherited from an early, smaller-scale deployment and never revisited.
    **Business impact:** Outbound-traffic-dependent features (third-party plagiarism scanning, external LMS integrations) degrade under load, with no obvious root cause without deep investigation.
    **Technical impact:** Cross-AZ dependency and bandwidth bottleneck that's easy to miss in routine capacity review.
    **Recommended solution:** One NAT Gateway per AZ as a non-negotiable baseline, reviewed as part of any architecture audit.

## Lessons Learned

- **What usually causes delays:** Retrofitting tenant isolation onto an existing schema takes significantly longer than architects initially estimate — plan for it to be a multi-month effort, not a sprint.
- **Why migrations fail:** Migrations that don't account for each institutional tenant's independent academic calendar tend to collide with a tenant's active term, causing avoidable disruption.
- **Why monitoring is often insufficient:** Infrastructure-level metrics (CPU, memory) alone miss business-level failures like "grading pipeline stuck" — education-specific SLIs (submission success rate, grading completion time) need to be defined explicitly, not assumed to fall out of generic dashboards.
- **Why teams underestimate networking:** NAT Gateway design and VPC endpoint usage are frequently treated as "solved once" early in the project and never revisited as traffic and third-party integration count grow.
- **How IAM becomes overly complex:** Each new microservice tends to accumulate its own bespoke IAM policy over time; without periodic review, the policy set becomes difficult to reason about and audit.
- **How Terraform modules become difficult to maintain:** Per-tenant customization pressure (one district wants a slightly different configuration) tends to erode module reusability over time unless tenant-specific variation is deliberately confined to well-defined variables rather than module forks.

## Cost Surprises

- **Data transfer costs:** Cross-region replication for DR and multi-region video delivery both add data transfer charges that are easy to underestimate in initial modeling.
- **CloudFront costs:** Video-heavy platforms see CloudFront become one of the largest line items as content library size and viewing minutes grow — often surprising teams that modeled cost primarily around compute.
- **NAT Gateway costs:** Per-GB data processing charges through NAT Gateways add up quickly for any service making frequent outbound calls to third-party integrations (plagiarism scanning, external grading tools).
- **Logging costs:** Verbose debug-level logging left enabled in production, combined with long retention on high-volume log groups, becomes a meaningfully sized bill line over time.
- **Cross-AZ charges:** Data transfer between AZs (e.g., an application service in one AZ calling a database endpoint resolved to another AZ) is easy to overlook until it shows up as an unexplained cost anomaly.
- **Idle resources:** Development and staging environments left running with production-like Aurora instance sizes between test cycles.
- **Storage growth:** Recorded lecture video accumulates continuously; without lifecycle policies, storage cost grows roughly linearly with content library age and size.
- **Monitoring costs:** CloudWatch custom metrics and high-cardinality dimensions (e.g., per-student metrics) can generate unexpectedly large monitoring bills if not designed carefully.
- **Third-party licensing:** Plagiarism-detection and proctoring vendor integrations are often priced per-check or per-session, and this variable cost needs to be modeled alongside AWS infrastructure cost, not treated as a fixed line item.

## Security Blind Spots

- **IAM misconfigurations:** Overly broad task roles granted "temporarily" during development that are never tightened before production launch.
- **Overly permissive roles:** A shared "admin" role used across multiple services for convenience, dramatically expanding blast radius if any one service is compromised.
- **Encryption gaps:** New data stores or caching layers added later in the project lifecycle that miss the encryption-at-rest review applied to the original architecture.
- **Secret leakage:** Secrets accidentally committed to version control history, or logged in plaintext during debugging, even when Secrets Manager is used correctly elsewhere.
- **Insufficient logging:** Read-only access to student records not logged with the same rigor as write access, leaving audit gaps for "who viewed this student's grades."
- **Insufficient auditing:** Administrative actions (impersonating a student account for support purposes) not logged with sufficient detail to reconstruct exactly what an administrator saw or did.
- **Network exposure:** A debugging or internal admin tool accidentally deployed with a public-facing ALB listener instead of an internal one.
- **Supply chain risks:** Third-party npm/pip dependencies in Lambda functions not scanned as rigorously as the primary application container images.
- **Container security:** Base images not rebuilt on a regular cadence, allowing known CVEs to persist in production images for extended periods.
- **API security:** Internal service-to-service APIs assumed to be "trusted by network location" without independent authentication, violating zero-trust principles.

## Scaling Limits

- **Commonly encountered service quotas:** Lambda concurrent execution limits, RDS Proxy connection limits, DynamoDB On-Demand throughput ceilings during extreme synchronized events, CloudFront cache behavior count limits as tenant-specific customization grows.
- **Soft limits:** Most of the above are soft limits raisable via AWS Support — but only if requested proactively, well ahead of a known large event, not during the event itself.
- **Hard limits:** Aurora maximum storage size per cluster and maximum connections per instance class are effectively hard limits requiring architectural changes (sharding, read replica offloading) rather than a quota increase request.
- **Performance bottlenecks:** Aurora write throughput on a single writer instance becomes the ceiling for write-heavy synchronized events (mass grade posting) well before compute-layer scaling limits are reached.
- **Scaling bottlenecks:** RDS Proxy connection pool exhaustion during a Lambda concurrency burst, if the proxy's max connections aren't sized in proportion to Lambda's reserved concurrency.
- **Operational bottlenecks:** Human approval gates in the deployment pipeline (necessary for safety) can become a bottleneck during incident response if not paired with a well-rehearsed expedited emergency-change process.
- **Preparation:** Track quota utilization trends quarterly against tenant growth projections; request quota increases proactively ahead of known large-scale events (state testing windows, major enrollment periods) rather than reactively.

## Evolution Path

**Startup**
- Single-tenant or few-tenant deployment, monolithic or lightly service-oriented, single region, minimal automation.

↓

**Small Production**
- Multi-AZ introduced for the core database and application tier; basic CI/CD established; manual scaling still acceptable.

↓

**Highly Available**
- Full Multi-AZ across all stateful components; Auto Scaling introduced; blue-green deployment adopted; first formal DR plan established.

↓

**Microservices / Event-Driven**
- Application decomposed into the service boundaries described in this chapter; event-driven processing (EventBridge/SQS/Lambda) introduced to decouple grading, notifications, and media processing from the synchronous request path; row-level security formalized for multi-tenancy.

↓

**Multi-Region**
- Aurora Global Database or equivalent cross-region replication adopted for the highest-tier tenant contracts; Route 53 health-check-based failover established; DR drills become routine.

↓

**Global Enterprise**
- Regional data residency controls per tenant contract; multi-region active-active considered for latency-sensitive live-class audiences; dedicated platform engineering and FinOps functions fully staffed; per-tenant SLA tiers formalized with contractual RTO/RPO commitments.

## Decision Matrix

| Criteria | This Architecture | Monolithic EC2 | Fully Serverless | Kubernetes (EKS) | Third-Party SaaS LMS |
|---|---|---|---|---|---|
| Cost | 3 | 4 (small scale) / 2 (large scale) | 4 | 2 | 3 (small) / 2 (large) |
| Complexity | 3 | 4 | 3 | 2 | 5 |
| Performance | 4 | 2 | 4 | 4 | 3 |
| Reliability | 5 | 2 | 4 | 4 | 3 |
| Scalability | 5 | 1 | 5 | 4 | 2 |
| Security | 4 | 2 | 4 | 3 | 3 (vendor-dependent) |
| Operational Effort | 3 | 2 | 4 | 2 | 5 |
| Maintainability | 4 | 2 | 3 | 3 | 5 |
| Compliance | 5 | 2 | 4 | 3 | 3 (vendor-dependent) |
| Time to Market | 3 | 3 | 3 | 2 | 5 |
| Developer Experience | 4 | 3 | 3 | 3 | 5 |

*Scale: 1 (poor) to 5 (excellent). Scores reflect fit for a growing, multi-tenant, compliance-bound education platform specifically — not a universal ranking.*

**Overall Recommendation:** For any organization serving multiple institutional tenants with compliance obligations and calendar-driven traffic spikes, this architecture scores highest on the criteria that matter most for this vertical — reliability, scalability, and compliance — at a complexity and cost level that is justified once tenant count or scale crosses the threshold described in "When You SHOULD Choose This Architecture" above. Below that threshold, a third-party SaaS LMS or a simpler architecture remains the more rational choice.

## Final Recommendations from the Architect

- **Biggest success factor:** Treating tenant isolation as a database-layer guarantee, not an application-layer convention, from the very first schema design.
- **Biggest implementation risk:** Underestimating the effort required to load-test and validate behavior under truly synchronized traffic patterns — this is where education workloads differ most sharply from typical SaaS traffic assumptions.
- **First thing to build:** The row-level security model and tenant-isolation test suite, before a single feature is built on top of the schema.
- **First thing to automate:** Scheduled Auto Scaling aligned to the academic calendar — the highest-leverage automation for both reliability and cost.
- **First thing to monitor:** Submission and grading pipeline success rate, end to end — the business-level metric that actually reflects whether students and instructors are being served, not just whether servers are up.
- **First security control to enable:** MFA enforcement for instructor and administrator accounts, and GuardDuty Malware Protection on any user-upload bucket.
- **First FinOps recommendation:** Model video transcoding and CloudFront egress cost realistically before finalizing per-student or per-district pricing — this is the line item most likely to be underestimated.
- **First disaster recovery test:** A full Aurora failover drill during a genuinely low-traffic window, measured against the documented RTO/RPO targets, before the platform's first real academic term-start event.
- **Long-term maintenance advice:** Revisit IAM policies, Terraform module structure, and cost allocation tagging on a fixed quarterly cadence — these three things degrade quietly and continuously if left to accumulate without deliberate review, and each becomes exponentially harder to fix the longer it's deferred.
