# OmniChannel Retail Customer & Order Intelligence Platform

**Course/Week:** Big-Data Platforms and Architecture (Week 4 Capstone)  
**Team:** Capstone Project / Individual Lab Repository  
**Purpose:** Design and justify an end-to-end scalable big-data architecture for an omni-channel retail ecosystem handling high-velocity clickstream events, transactional order pipelines, and multi-tier analytical processing.

---

## 1. Problem Statement & Stakeholders
* **Current Situation:** The retail platform experiences growing friction between real-time operational responsiveness (fraud prevention, logistics tracking) and delayed batch financial reporting.
* **Affected Stakeholders:** Fraud Operations, Logistics Managers, Marketing Teams, and Executive Leadership.
* **Decision to Improve:** Enable sub-2-minute anomaly detection alongside trustworthy next-day financial aggregation without risking data loss or privacy non-compliance.
* **Measurable Outcome:** Reduction in payment chargeback rates and improved fulfillment SLA adherence.
* **Guardrail / Risk Measure:** Zero unmasked exposure of customer Personally Identifiable Information (PII) in analytical serving layers.

---

## 2. Scope & Datasets
* **Population, Geography & Time Horizon:** Global retail customer base, spanning a 3-year historical horizon.
* **Core Data Sources:**
  1. *Clickstream & Web Events* (Real-time JSON streams via Kafka).
  2. *Operational Orders & Inventory* (Relational CDC / micro-batches).
  3. *Customer Support & CRM* (Daily secure batch files).
* **Sensitivity & License:** Customer records and payment details are classified as *Restricted Personal Data* governed by GDPR/CCPA compliance standards.

---

## 3. Analytical Questions Summary
1. **Real-Time Fraud Detection (Operational):** Which user sessions show payment submission anomalies within the last 5 minutes? *(User: Fraud Operations Team)*
2. **Hourly Fulfillment Bottlenecks (Tactical):** Which regional warehouses exceed 30-minute processing delays? *(User: Logistics Managers)*
3. **Customer Lifetime Value LTV Segmentation (Strategic):** How do customer value tiers distribute across regions based on 12-month trailing spend? *(User: Marketing & Executives)*
4. **Category Profitability & Margin Trend (Historical):** What is the trailing 3-month moving average of net revenue per product category? *(User: Finance Directors)*

---

## 4. Preliminary Architecture & Data Path
The architecture follows a modern **Lakehouse / Separated Storage and Compute** pattern:
* **Ingestion:** Kafka message broker for streaming events; secure API/SFTP loaders for batch transactions.
* **Storage Zones (Cloud Object Storage):** 
  * *Bronze (Raw Zone):* Immutable source-aligned files.
  * *Silver (Validated Zone):* Pseudonymized, cleaned Parquet tables.
  * *Gold (Curated Zone):* Aggregated business-ready star schemas.
* **Processing & Serving:** Spark for distributed data engineering; serverless SQL engines / data warehouse for high-concurrency BI consumption.
* **Cross-Cutting Controls:** Centralized Data Catalog, Unity Catalog governance, RBAC least-privilege security, and automated lineage tracking.

*Note: The preliminary visual diagram is located under `architecture/preliminary-architecture.png` (editable source in `architecture/preliminary-architecture.drawio`).*

---

## 5. Repository Map & Documentation
* `architecture/` - Contains the preliminary architecture diagram (`.png` and `.drawio`).
* `docs/architecture-rationale.md` - Comprehensive rationale covering design drivers, storage, and compute choices.
* `docs/data-source-register.md` - Detailed specifications of all platform data sources.
* `docs/governance-privacy-access.md` - Security, privacy compliance, and RBAC matrix.
* `analysis/analytical-questions.md` - Structured analytical questions and decision frameworks.
* `CHANGELOG.md` - Record of repository evolution and design revisions.

---

## 6. Compliance & Safety Statement
*This repository contains no production secrets, API keys, passwords, connection strings, or unapproved raw personal data files.*