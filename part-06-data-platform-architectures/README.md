# Part 6 — Data Platform Architectures

**Chapters 43–50 · Status: ✅ Complete**

← [Back to Main README](../Readme.md) · [← Part 5](../part-05-container-and-kubernetes-architectures/README.md) · [Part 7 →](../part-07-ai-and-machine-learning-architectures/README.md)

---

## What This Part Covers

Data architecture decisions are the hardest to reverse. You can swap a compute tier, re-architect a deployment pipeline, or change a messaging pattern without touching your data. But choosing the wrong database, designing a schema that doesn't support your access patterns, or building a data lake without a governance model — these decisions compound over years and become the technical debt that defines a platform's ceiling.

This part covers the full data platform stack on AWS — relational databases, globally distributed Aurora, DynamoDB at scale, data lakes, lake houses, streaming analytics, data warehousing, and multi-database architectures. Every chapter covers not just how to provision the service, but how to design the data model, manage schema evolution, handle failure, control cost, and make the access-pattern-driven decisions that determine whether a data architecture ages well or becomes a liability.

---

## Which Chapter Do You Need?

| If your situation is... | Read this chapter |
|---|---|
| Running a relational database for a transactional application | [Ch. 43 — Relational Database](./chapter-43-relational-database.md) |
| Needing a relational database that spans multiple AWS regions | [Ch. 44 — Aurora Global Database](./chapter-44-aurora-global-database.md) |
| Building for single-digit millisecond latency at any scale | [Ch. 45 — DynamoDB](./chapter-45-dynamodb.md) |
| Storing and querying large volumes of raw and processed data | [Ch. 46 — Data Lake](./chapter-46-data-lake.md) |
| Combining a data lake with a data warehouse in a unified platform | [Ch. 47 — Lake House](./chapter-47-lake-house.md) |
| Processing and analyzing data streams in near real-time | [Ch. 48 — Streaming Analytics](./chapter-48-streaming-analytics.md) |
| Running complex analytical queries across large historical datasets | [Ch. 49 — Data Warehouse](./chapter-49-data-warehouse.md) |
| Using multiple database types for different access patterns in one system | [Ch. 50 — Multi-Database Architecture](./chapter-50-multi-database-architecture.md) |

---

## Chapters at a Glance

### Chapter 43 — Relational Database

**[Read →](./chapter-43-relational-database.md)** · ✅ Published

Amazon RDS and Aurora for transactional relational workloads — the production configuration, Multi-AZ setup, read replica strategy, connection pooling, schema migration approach, and the honest performance ceiling that tells you when to scale up, scale out, or reconsider the data model.

**Key topics:** RDS vs. Aurora decision framework, Multi-AZ configuration and failover behavior, read replica routing, RDS Proxy for connection pooling, Performance Insights for query analysis, automated backup and point-in-time recovery, parameter group tuning, storage type selection (gp3 vs. io1 vs. Aurora storage), schema migration with zero downtime, cost model

---

### Chapter 44 — Aurora Global Database

**[Read →](./chapter-44-aurora-global-database.md)** · ✅ Published

Aurora Global Database for a relational database that spans multiple AWS regions — with sub-second replication lag, regional read scaling, and the ability to promote a secondary region to primary in under a minute for disaster recovery or planned regional failover.

**Key topics:** Global cluster architecture (primary + secondary regions), replication lag characteristics and monitoring, read scaling from secondary regions, managed planned failover vs. unplanned failover, RPO/RTO characteristics, write forwarding from secondary regions, cost model (replication data transfer), Aurora Global Database vs. multi-region active-active trade-offs

---

### Chapter 45 — DynamoDB

**[Read →](./chapter-45-dynamodb.md)** · ✅ Published

DynamoDB for workloads that need single-digit millisecond latency at effectively unlimited scale — with the single-table design patterns, access pattern modeling, GSI strategy, and capacity planning that determine whether a DynamoDB implementation ages well or becomes a schema migration nightmare.

**Key topics:** Single-table design principles, partition key selection and hot partition avoidance, GSI and LSI design, on-demand vs. provisioned capacity with auto scaling, DynamoDB Accelerator (DAX) for microsecond reads, Streams for change data capture, TTL for automatic item expiration, point-in-time recovery, global tables for multi-region replication, cost model

---

### Chapter 46 — Data Lake

**[Read →](./chapter-46-data-lake.md)** · ✅ Published

S3 as the foundation of a data lake — with the ingestion patterns, storage organization, partitioning strategy, access control model, and query layer that turn a bucket of files into a governed, queryable, cost-efficient analytical platform.

**Key topics:** S3 storage organization (raw/processed/curated zones), partitioning strategy for query performance, AWS Glue Data Catalog for schema management, Athena for serverless SQL queries, Lake Formation for fine-grained access control, data formats (Parquet, ORC, Avro) and their trade-offs, lifecycle policies for cost management, data quality validation, ingestion patterns (batch vs. streaming)

---

### Chapter 47 — Lake House

**[Read →](./chapter-47-lake-house.md)** · ✅ Published

The lake house architecture combines a data lake (S3) with a data warehouse (Redshift) in a unified platform — where raw and historical data lives cost-efficiently in S3, hot analytical data lives in Redshift for fast query performance, and Redshift Spectrum bridges the two without data movement.

**Key topics:** Redshift Spectrum for querying S3 data from Redshift, data tiering strategy (what lives in Redshift vs. S3), Redshift data sharing for cross-cluster access, AWS Glue for ETL between lake and warehouse, unified governance with Lake Formation, query federation across data sources, cost optimization (Redshift reserved nodes vs. Serverless vs. Spectrum)

---

### Chapter 48 — Streaming Analytics

**[Read →](./chapter-48-streaming-analytics.md)** · ✅ Published

The architecture for processing and analyzing data streams in near real-time — clickstreams, IoT sensor data, application events, financial transactions — using Kinesis Data Streams, Kinesis Data Firehose, and Apache Flink on Amazon Managed Service for Apache Flink.

**Key topics:** Kinesis Data Streams shard capacity and scaling, Kinesis Data Firehose for delivery to S3/Redshift/OpenSearch, Managed Service for Apache Flink for stateful stream processing, windowing and aggregation patterns, late-arriving data handling, exactly-once processing semantics, MSK (Managed Kafka) as an alternative, Lambda for lightweight stream processing, cost model

---

### Chapter 49 — Data Warehouse

**[Read →](./chapter-49-data-warehouse.md)** · ✅ Published

Amazon Redshift as the managed data warehouse for complex analytical queries across large datasets — with the cluster configuration, distribution and sort key design, workload management, and query optimization strategies that determine whether Redshift delivers sub-second query performance or frustrating timeouts.

**Key topics:** Redshift Serverless vs. provisioned clusters, distribution styles (KEY, ALL, EVEN) and their impact on query performance, sort keys for range-restricted queries, workload management (WLM) queues, Redshift Advisor recommendations, AQUA for accelerated queries, data loading patterns (COPY command, Glue, Firehose), materialized views, cost model (reserved nodes vs. Serverless)

---

### Chapter 50 — Multi-Database Architecture

**[Read →](./chapter-50-multi-database-architecture.md)** · ✅ Published

The pattern for systems that use multiple database types — relational for transactional integrity, DynamoDB for high-throughput key-value access, ElastiCache for hot-read caching, OpenSearch for full-text search, and S3 for analytical queries — each serving the access pattern it's best suited for, with the data synchronization and consistency model that keeps them coherent.

**Key topics:** Polyglot persistence design, change data capture (CDC) with DMS and Debezium for cross-database synchronization, eventual consistency trade-offs, read-your-writes consistency patterns, cache invalidation strategies, search index synchronization, operational complexity of managing multiple data stores, when polyglot persistence is justified vs. over-engineered

---

## AWS Services Featured in This Part

| Service | Chapters |
|---------|---------|
| Amazon RDS | 43 |
| Amazon Aurora | 43, 44 |
| Amazon DynamoDB | 45, 50 |
| Amazon S3 | 46, 47, 48 |
| AWS Glue | 46, 47, 49 |
| Amazon Athena | 46, 47 |
| AWS Lake Formation | 46, 47 |
| Amazon Redshift | 47, 49 |
| Amazon Kinesis | 48 |
| Amazon MSK | 48 |
| Amazon OpenSearch Service | 50 |
| Amazon ElastiCache | 50 |
| AWS DMS | 50 |
| Amazon DynamoDB Accelerator (DAX) | 45 |

---

## Database Selection Quick Reference

| Workload | Recommended Service |
|----------|-------------------|
| Transactional, relational, ACID | Aurora / RDS |
| Key-value, high throughput, low latency | DynamoDB |
| Global relational with cross-region reads | Aurora Global Database |
| Ad hoc analytical queries on S3 | Athena |
| Complex analytics, BI dashboards | Redshift |
| Full-text search, log analytics | OpenSearch |
| Session cache, hot-read acceleration | ElastiCache |
| Large-scale stream processing | Kinesis / MSK + Flink |

The right answer is almost never "use one database for everything." Chapter 50 covers how to combine these services coherently without creating an operational nightmare.

---

← [Back to Main README](../Readme.md) · [← Part 5](../part-05-container-and-kubernetes-architectures/README.md) · [Part 7 →](../part-07-ai-and-machine-learning-architectures/README.md)
