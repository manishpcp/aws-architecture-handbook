# Part 5 — Container & Kubernetes Architectures

**Chapters 35–42 · Status: ✅ Complete**

← [Back to Main README](../Readme.md) · [← Part 4](../part-04-serverless-architectures/README.md) · [Part 6 →](../part-06-data-platform-architectures/README.md)

---

## What This Part Covers

Containers solved the "works on my machine" problem. Kubernetes solved the "how do we run thousands of containers reliably" problem. Together they introduced a new problem: operational complexity that can consume an entire platform engineering team if the architecture isn't designed deliberately.

This part covers the full container and Kubernetes stack on AWS — from ECS Fargate for teams that want containers without cluster management, through production EKS with service meshes and GitOps, to multi-cluster federation, platform engineering internal developer platforms, batch processing, and GPU workloads for AI/ML inference. Every chapter is honest about the operational investment each pattern requires and when simpler alternatives are the better choice.

---

## Which Chapter Do You Need?

| If your situation is... | Read this chapter |
|---|---|
| Running containers without managing EC2 nodes or Kubernetes | [Ch. 35 — ECS Fargate](./chapter-35-ecs-fargate.md) |
| Running Kubernetes workloads on AWS with managed control plane | [Ch. 36 — Amazon EKS](./chapter-36-amazon-eks.md) |
| Managing service-to-service communication, mTLS, and observability in Kubernetes | [Ch. 37 — Service Mesh](./chapter-37-service-mesh.md) |
| Managing Kubernetes deployments declaratively via Git | [Ch. 38 — GitOps Platform](./chapter-38-gitops-platform.md) |
| Running workloads across multiple Kubernetes clusters | [Ch. 39 — Multi-Cluster Kubernetes](./chapter-39-multicluster-kubernetes.md) |
| Building an internal developer platform on top of Kubernetes | [Ch. 40 — Platform Engineering](./chapter-40-platform-engineering.md) |
| Running large-scale batch or parallel processing jobs in containers | [Ch. 41 — Batch Containers](./chapter-41-batch-containers.md) |
| Running GPU-accelerated AI/ML inference or training workloads | [Ch. 42 — GPU Workloads](./chapter-42-gpu-workloads.md) |

---

## Chapters at a Glance

### Chapter 35 — ECS Fargate

**[Read →](./chapter-35-ecs-fargate.md)** · ✅ Published

AWS Fargate on ECS as the managed container platform for teams that want the portability and consistency of containers without the operational burden of managing EC2 node groups or a Kubernetes control plane. The right starting point for most container workloads.

**Key topics:** ECS task definitions and service configuration, Fargate vs. EC2 launch type trade-offs, ALB integration for HTTP services, service discovery with AWS Cloud Map, ECS Exec for container access without SSH, auto scaling (target tracking on CPU/memory/ALB metrics), Fargate Spot for cost reduction on fault-tolerant workloads, ECR for image management, cost model vs. EKS

---

### Chapter 36 — Amazon EKS

**[Read →](./chapter-36-amazon-eks.md)** · ✅ Published

Amazon EKS as the managed Kubernetes platform for organizations that need the full Kubernetes ecosystem — Helm charts, custom controllers, the CNCF tooling landscape — with AWS managing the control plane and the operator managing the data plane.

**Key topics:** EKS managed node groups vs. Fargate profiles vs. self-managed nodes, cluster add-ons (CoreDNS, kube-proxy, VPC CNI), IAM Roles for Service Accounts (IRSA) for pod-level IAM, ALB Ingress Controller, EBS and EFS CSI drivers for persistent storage, cluster autoscaler vs. Karpenter, EKS Blueprints for opinionated cluster configuration, upgrade strategy, cost model

---

### Chapter 37 — Service Mesh

**[Read →](./chapter-37-service-mesh.md)** · ✅ Published

AWS App Mesh and Istio on EKS for managing service-to-service communication in a microservices architecture — mutual TLS, traffic shaping, circuit breaking, retries, and distributed tracing — without modifying application code.

**Key topics:** Sidecar proxy pattern (Envoy), mTLS between services, traffic shifting for canary deployments at the mesh layer, circuit breaker and retry configuration, distributed tracing with AWS X-Ray, observability (metrics, logs, traces) from the mesh, App Mesh vs. Istio decision framework, service mesh overhead and when it's justified

---

### Chapter 38 — GitOps Platform

**[Read →](./chapter-38-gitops-platform.md)** · ✅ Published

GitOps as the deployment model for Kubernetes — Git as the single source of truth for cluster state, with ArgoCD or Flux continuously reconciling the cluster toward the declared state, eliminating manual kubectl applies and providing an automatic audit trail of every change.

**Key topics:** ArgoCD vs. Flux comparison, application and cluster configuration separation, multi-environment promotion (dev → staging → prod), secrets management in GitOps (Sealed Secrets, External Secrets Operator), drift detection and automatic reconciliation, rollback via Git revert, RBAC for GitOps operators, integration with CI pipelines

---

### Chapter 39 — Multi-Cluster Kubernetes

**[Read →](./chapter-39-multicluster-kubernetes.md)** · ✅ Published

The patterns for running workloads across multiple Kubernetes clusters — for regional isolation, environment separation, blast radius reduction, or workload-type separation — with cross-cluster service discovery, traffic routing, and unified observability.

**Key topics:** Cluster federation patterns, cross-cluster service discovery, global load balancing across clusters (Route 53, Global Accelerator), cluster-per-environment vs. namespace-per-environment trade-offs, EKS multi-region active-active, cross-cluster secrets management, unified observability across clusters, Karmada and other federation tools

---

### Chapter 40 — Platform Engineering

**[Read →](./chapter-40-platform-engineering.md)** · ✅ Published

Building an Internal Developer Platform (IDP) on top of Kubernetes and AWS — a self-service layer that lets application teams deploy, scale, and operate their workloads without deep infrastructure expertise, while the platform team maintains guardrails, security controls, and operational standards.

**Key topics:** Platform team topology and responsibilities, self-service provisioning (Backstage, AWS Service Catalog), golden path templates, namespace-per-team isolation, resource quotas and limit ranges, policy enforcement (OPA/Gatekeeper, Kyverno), developer experience metrics, platform as a product mindset, cost showback per team

---

### Chapter 41 — Batch Containers

**[Read →](./chapter-41-batch-containers.md)** · ✅ Published

AWS Batch for running large-scale parallel and batch processing jobs in containers — genomics pipelines, financial simulations, media transcoding, ML training data preparation — with managed job queues, compute environments, and Spot Instance integration for cost-optimized execution.

**Key topics:** AWS Batch job definitions and job queues, managed vs. unmanaged compute environments, Spot Instance integration for cost reduction, array jobs for parallel processing, job dependencies and DAG workflows, EFS and S3 for shared storage, GPU instance support, Batch vs. Step Functions vs. ECS for batch workloads, cost model

---

### Chapter 42 — GPU Workloads

**[Read →](./chapter-42-gpu-workloads.md)** · ✅ Published

Running GPU-accelerated workloads on AWS — deep learning training, inference serving, video processing — on EC2 GPU instances, EKS with GPU node groups, or SageMaker, with the instance selection, driver management, and cost optimization strategies that make GPU infrastructure economically viable.

**Key topics:** GPU instance families (P4, P5, G5, Inf2), NVIDIA driver and CUDA management, EKS GPU node groups with device plugin, GPU sharing strategies (MIG, time-slicing), inference vs. training instance selection, Spot Instances for training cost reduction, AWS Inferentia and Trainium for cost-optimized inference, GPU utilization monitoring, cost model

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| Amazon ECS | 35, 41 |
| AWS Fargate | 35, 36 |
| Amazon EKS | 36, 37, 38, 39, 40, 42 |
| Amazon ECR | 35, 36 |
| AWS App Mesh | 37 |
| AWS Batch | 41 |
| Amazon EC2 (GPU instances) | 42 |
| AWS Inferentia / Trainium | 42 |
| AWS Cloud Map | 35 |
| Amazon EFS | 41, 42 |
| AWS X-Ray | 37 |
| Amazon CloudWatch | 35, 36, 37, 40 |

---

## ECS vs. EKS: The Honest Trade-off

| Dimension | ECS Fargate | EKS |
|-----------|------------|-----|
| Operational complexity | Low | High |
| Kubernetes ecosystem access | None | Full |
| Learning curve | Gentle | Steep |
| Control plane cost | None | ~$0.10/hr per cluster |
| Node management | None (Fargate) | Required (unless Fargate profile) |
| Best for | Teams wanting containers without K8s | Teams needing K8s ecosystem or portability |

If your team doesn't have existing Kubernetes expertise and doesn't need the Kubernetes ecosystem specifically, ECS Fargate (Chapter 35) is almost always the better starting point. EKS (Chapter 36) is the right choice when you need Kubernetes — not when you want containers.

---

← [Back to Main README](../Readme.md) · [← Part 4](../part-04-serverless-architectures/README.md) · [Part 6 →](../part-06-data-platform-architectures/README.md)
