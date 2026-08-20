# Part 3 — Network Architectures

**Chapters 15–24 · Status: ✅ Complete**

← [Back to Main README](../Readme.md) · [← Part 2](../part-02-core-infrastructure-architectures/README.md) · [Part 4 →](../part-04-serverless-architectures/README.md)

---

## What This Part Covers

Networking is the layer most architects get wrong once — and then never get wrong again. A poorly designed VPC, a missing Transit Gateway route, or a misconfigured Direct Connect failover path doesn't show up in a demo. It shows up at 2 a.m. during an incident, or in a compliance audit, or on a bill that's 40% higher than expected because cross-AZ traffic was never accounted for.

This part covers the full spectrum of AWS network architecture — from a single production VPC designed for enterprise compliance, through multi-account hub-and-spoke topologies, global WAN connectivity, edge delivery, and hybrid on-premises integration. Every chapter treats networking not as a prerequisite to get through quickly, but as a first-class architectural concern with its own security model, cost model, failure modes, and operational discipline.

If your organization is running more than one VPC, has on-premises connectivity requirements, or is building toward a multi-account landing zone, this part is where the foundational decisions get made.

---

## Which Chapter Do You Need?

| If your situation is... | Read this chapter |
|---|---|
| Designing a VPC for a single enterprise workload | [Ch. 15 — Enterprise VPC](./chapter-15-enterprise-vpc.md) |
| Connecting multiple VPCs across accounts or regions | [Ch. 16 — Hub-and-Spoke](./chapter-16-hub-and-spoke-networking.md) |
| Scaling beyond VPC peering to a managed transit layer | [Ch. 17 — Transit Gateway](./chapter-17-transit-gateway.md) |
| Managing a global multi-region network from a single control plane | [Ch. 18 — Cloud WAN](./chapter-18-cloud-wan.md) |
| Sharing centralized services (DNS, inspection, egress) across VPCs | [Ch. 19 — Shared Services VPC](./chapter-19-shared-services-vpc.md) |
| Exposing a service privately without VPC peering or routing changes | [Ch. 20 — PrivateLink](./chapter-20-privatelink-architecture.md) |
| Improving global application performance and availability | [Ch. 21 — Global Accelerator](./chapter-21-global-accelerator.md) |
| Delivering content and APIs at the edge with caching and WAF | [Ch. 22 — CloudFront Edge](./chapter-22-cloudfront-edge-architecture.md) |
| Connecting an on-premises network to AWS over the internet | [Ch. 23 — Hybrid VPN](./chapter-23-hybrid-vpn.md) |
| Connecting an on-premises network to AWS with dedicated bandwidth | [Ch. 24 — Direct Connect](./chapter-24-direct-connect-enterprise.md) |

---

## Chapters at a Glance

### Chapter 15 — Enterprise VPC

**[Read →](./chapter-15-enterprise-vpc.md)**

The foundational VPC design for a single enterprise workload — CIDR planning that won't need to be re-done in 18 months, subnet tier separation that satisfies a compliance auditor, routing tables that enforce the intended traffic flow, and the VPC endpoint strategy that eliminates unnecessary NAT Gateway cost and internet exposure for AWS API calls.

**Key topics:** CIDR block planning for growth, public/private/data subnet tiers per AZ, NAT Gateway per-AZ vs. shared trade-offs, Internet Gateway and route table design, VPC endpoints (gateway and interface), DNS resolution with Route 53 Resolver, flow log configuration and cost, security group vs. NACL strategy

---

### Chapter 16 — Hub-and-Spoke Networking

**[Read →](./chapter-16-hub-and-spoke-networking.md)**

The pattern for organizations running multiple VPCs across accounts that need controlled, auditable connectivity between them — without the management overhead of a full mesh of VPC peering connections that becomes unmanageable past a handful of VPCs.

**Key topics:** Hub VPC design, spoke VPC attachment patterns, centralized egress and ingress, shared services reachability, route summarization, security inspection insertion at the hub, cost model vs. full-mesh peering, AWS Resource Access Manager for cross-account sharing

---

### Chapter 17 — Transit Gateway

**[Read →](./chapter-17-transit-gateway.md)**

AWS Transit Gateway as the managed, scalable backbone for connecting VPCs, accounts, VPNs, and Direct Connect gateways through a single hub — replacing the operational complexity of VPC peering meshes with a centrally managed routing layer.

**Key topics:** TGW route tables and route domains, attachment types (VPC, VPN, Direct Connect Gateway, peering), inter-region TGW peering, centralized inspection with Gateway Load Balancer, multicast support, TGW Network Manager for visibility, cost per attachment and data processing

---

### Chapter 18 — Cloud WAN

**[Read →](./chapter-18-cloud-wan.md)**

AWS Cloud WAN as a managed global network service that builds a unified WAN across AWS regions and on-premises locations — abstracting the complexity of managing individual Transit Gateways per region into a single global policy-driven network.

**Key topics:** Global network and core network concepts, network policy as code (JSON), segments for traffic isolation, attachments (VPC, VPN, Connect), inter-region backbone routing, integration with Transit Gateway for migration, Cloud WAN vs. Transit Gateway decision framework

---

### Chapter 19 — Shared Services VPC

**[Read →](./chapter-19-shared-services-vpc.md)**

The pattern for centralizing shared infrastructure — DNS resolvers, NAT egress, security inspection, Active Directory, internal certificate authorities — in a dedicated VPC that all workload VPCs consume, rather than duplicating these services per workload account.

**Key topics:** Centralized DNS with Route 53 Resolver endpoints, centralized NAT Gateway egress, centralized security inspection (AWS Network Firewall or third-party), shared Active Directory via AWS Managed Microsoft AD, internal PKI with AWS Private CA, cost allocation across consumers, Transit Gateway integration

---

### Chapter 20 — PrivateLink Architecture

**[Read →](./chapter-20-privatelink-architecture.md)**

AWS PrivateLink for exposing services privately across VPC and account boundaries without VPC peering, route table changes, or internet exposure — the cleanest pattern for SaaS providers exposing services to customers, and for internal platform teams exposing shared services to workload accounts.

**Key topics:** VPC endpoint services (provider side), interface VPC endpoints (consumer side), NLB as the PrivateLink entry point, DNS resolution for endpoint services, cross-account and cross-region PrivateLink, acceptance policies, PrivateLink vs. VPC peering vs. Transit Gateway decision framework, cost model

---

### Chapter 21 — Global Accelerator

**[Read →](./chapter-21-global-accelerator.md)**

AWS Global Accelerator for routing user traffic through AWS's private global network from the nearest edge location to the application origin — improving latency, availability, and failover speed for globally distributed users compared to standard internet routing.

**Key topics:** Anycast IP addresses and edge routing, listener and endpoint group configuration, health-check-driven failover between regions, traffic dial for weighted routing, client affinity, Global Accelerator vs. CloudFront decision framework, DDoS protection via Shield, cost model

---

### Chapter 22 — CloudFront Edge Architecture

**[Read →](./chapter-22-cloudfront-edge-architecture.md)**

CloudFront as a full edge platform — not just a CDN for static assets, but a programmable edge layer for security enforcement, request routing, authentication, and API acceleration, with WAF integration and Lambda@Edge for custom logic at the edge.

**Key topics:** Cache behaviors and cache policies, origin request policies, Lambda@Edge and CloudFront Functions for edge logic, WAF integration at the edge, origin failover groups, signed URLs and signed cookies for private content, real-time logs, cache invalidation strategy, cost model (data transfer tiers, request pricing)

---

### Chapter 23 — Hybrid VPN

**[Read →](./chapter-23-hybrid-vpn.md)**

Site-to-Site VPN for connecting on-premises networks to AWS over the public internet — the fastest path to hybrid connectivity, with redundant tunnels, BGP dynamic routing, and Transit Gateway integration for connecting multiple VPCs through a single VPN connection.

**Key topics:** Virtual Private Gateway vs. Transit Gateway attachment, redundant tunnel configuration, BGP vs. static routing, Dead Peer Detection, VPN monitoring with CloudWatch, accelerated VPN with Global Accelerator, VPN as Direct Connect backup, cost model, bandwidth and latency limitations

---

### Chapter 24 — Direct Connect Enterprise

**[Read →](./chapter-24-direct-connect-enterprise.md)**

AWS Direct Connect for dedicated, private network connectivity between on-premises data centers and AWS — the pattern for organizations with consistent high-bandwidth transfer requirements, latency-sensitive workloads, or compliance obligations that prohibit internet-traversing connectivity.

**Key topics:** Dedicated vs. hosted connections, Virtual Interfaces (public, private, transit), Direct Connect Gateway for multi-region access, LAG for aggregated bandwidth, resilience models (single location vs. dual location vs. maximum resilience), Direct Connect + VPN failover, cost model (port hours + data transfer), Direct Connect vs. VPN decision framework

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| Amazon VPC | 15, 16, 17, 18, 19, 20 |
| AWS Transit Gateway | 16, 17, 18, 19, 23, 24 |
| AWS Cloud WAN | 18 |
| AWS PrivateLink | 20 |
| AWS Global Accelerator | 21 |
| Amazon CloudFront | 22 |
| AWS WAF | 22 |
| Lambda@Edge / CloudFront Functions | 22 |
| AWS Site-to-Site VPN | 23 |
| AWS Direct Connect | 24 |
| AWS Network Firewall | 19 |
| Amazon Route 53 Resolver | 15, 19 |
| AWS Resource Access Manager | 16, 19 |

---

## Key Design Decisions This Part Helps You Make

- **VPC peering vs. Transit Gateway vs. Cloud WAN** — when each is the right tool and when the complexity is unjustified
- **PrivateLink vs. VPC peering** — for cross-account service exposure without routing complexity
- **VPN vs. Direct Connect** — cost, latency, compliance, and bandwidth trade-offs
- **CloudFront vs. Global Accelerator** — caching and edge logic vs. TCP/UDP acceleration and regional failover
- **Centralized vs. distributed egress** — shared NAT Gateway and inspection vs. per-VPC egress

---

← [Back to Main README](../Readme.md) · [← Part 2](../part-02-core-infrastructure-architectures/README.md) · [Part 4 →](../part-04-serverless-architectures/README.md)
