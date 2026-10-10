# Architecture Rationale: OmniChannel Retail Platform

This document details the engineering and architectural decisions underpinning the OmniChannel Retail big-data platform, addressing performance, scalability, cost-efficiency, governance, and operational resilience.

---

### 1. Design Drivers
* **Volumes & Peak Rates:** Sustained ingestion of 500 clickstream events/sec scaling to 2,500 events/sec during peak promotional windows (e.g., Black Friday). Batch order volume scaling to ~10 GB/day.
* **Latency Classes:** Real-time operational fraud detection requires sub-2-minute latency. Tactical fulfillment analytics require 15-to-30-minute refreshes. Strategic financial reporting operates on a 24-hour batch cycle.
* **Compliance & Budget:** Strict GDPR/CCPA privacy requirements for personal and financial data. Cloud infrastructure costs must scale elastically with demand without maintaining idle peak-provisioned hardware clusters.

---

### 2. Ingestion Architecture
* **Decision:** Hybrid ingestion architecture combining a distributed message broker (Apache Kafka) for real-time streaming clickstream events with Change Data Capture (CDC) and secure file transfer (SFTP) micro-batches for relational orders and CRM data.
* **Handling Retries & Ordering:** Kafka partitions events by `session_id` to guarantee intra-session event ordering. Idempotent consumers and unique transaction keys prevent duplicate processing during network retries or stream replays.
* **Rejected Alternative:** Direct relational database writing for clickstream events. *Reason:* Relational databases would suffer severe locking contention and write bottlenecks under high event concurrency.

---

### 3. Cloud Object Storage Design
* **Decision:** Cloud Object Storage (e.g., AWS S3 / Google Cloud Storage) structured into three distinct zones: Raw / Bronze (immutable source-aligned JSON/CSV files), Validated / Silver (cleaned, pseudonymized Parquet tables), and Curated / Gold (aggregated business models and star schemas).
* **Partitioning & Lifecycle:** Data is partitioned by event date (`year/month/day`) and regional store ID to optimize analytical partition pruning. Object lifecycle policies automatically transition raw historical logs to cost-effective cold storage tiers after 90 days.
* **Rejected Alternative:** Monolithic relational database storage for all historical landing files. *Reason:* Prohibitive storage costs and lack of flexible schema-on-read exploration capabilities.

---

### 4. Storage and Compute Separation
* **Decision:** Complete architectural separation between durable cloud object storage and elastic compute clusters (e.g., serverless SQL warehouses and ephemeral Spark worker pools).
* **Trade-offs:** Separating storage from compute allows independent scaling and prevents compute failures from risking durable storage. However, it introduces remote network access overhead, mitigated effectively through columnar formats (Parquet) and localized caching.
* **Rejected Alternative:** On-premise Hadoop clusters with co-located HDFS storage and compute. *Reason:* High operational burden of hardware capacity planning and inability to scale storage independently from compute.

---

### 5. Scale-Out Choices
* **Decision:** Horizontal auto-scaling for both streaming consumers (Kafka/Spark Structured Streaming) and analytical query execution engines (serverless slots/compute clusters).
* **Skew & Hot Keys:** Addressed by salting high-frequency keys (e.g., massive regional flash sales) and enforcing columnar partitioning rules to avoid single-node processing bottlenecks and small-file performance degradation.

---

### 6. Processing and Serving Layer
* **Decision:** Multi-engine processing strategy utilizing Spark for heavy distributed data engineering transformations (Bronze to Silver/Gold pipelines) and a cloud data warehouse / lakehouse SQL endpoint (BigQuery / Databricks SQL) for low-latency BI and dashboard serving.
* **Control Enforcement:** Views and semantic layer security filters ensure business users access curated Gold tables without bypassing governance or exposing underlying raw PII fields.

---

### 7. Governance, Privacy, and Access Control
* **Decision:** Centralized Data Catalog integration with automated lineage tracking. Tokenization and column-level masking applied during the Bronze-to-Silver transformation stage. Strict least-privilege RBAC separating service ingestion accounts from human analyst roles.

---

### 8. Operations and Cost Management
* **Decision:** Orchestration via managed workflow schedulers (e.g., Airflow / Managed Workflows) with automated dependency mapping, retry backoffs, task checkpointing, and alerting on pipeline failures. Cost budgets and query slot limits are enforced at the project and department level to prevent runaway query expenses.