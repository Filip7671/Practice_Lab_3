# Governance, Privacy, and Access-Control Matrix: OmniChannel Retail Platform

This document outlines the cross-cutting security, data governance, privacy compliance, and role-based access control (RBAC) policies governing all layers of the retail big-data platform.

---

## 1. Governance & Data Ownership Framework
* **Data Ownership & Stewardship:** Domain data owners (Marketing, Logistics, Finance, Security) are legally and operationally responsible for data quality, schema evolution approvals, and compliance within their domains.
* **Catalog & Lineage:** All ingested and transformed datasets are registered in a centralized Data Catalog (e.g., Unity Catalog or equivalent metastore), maintaining automated lineage tracking from Raw to Curated zones.
* **Naming Conventions & Quality Gates:** Standardized naming conventions (`bronze_`, `silver_`, `gold_`) are enforced via CI/CD pipelines. Automated quality gates check for schema drift, missing primary keys, and volume anomalies before promoting data to downstream consumer layers.

---

## 2. Privacy & Data Protection Framework
* **Minimization & Purpose Limitation:** Data collection is strictly limited to fields necessary for operational execution and authorized analytical workloads.
* **Pseudonymization & Tokenization:** Personally Identifiable Information (PII) such as customer names, cleartext emails, and raw payment tokens are pseudonymized or tokenized immediately upon ingestion into the Validated (Silver) layer.
* **Consent & Deletion Propagation:** Right-to-be-forgotten (GDPR/CCPA) requests trigger automated deletion propagation workflows across curated storage zones and downstream analytical caches.

---

## 3. Access Control & Service Identities
* **Least Privilege Principle:** Human users and service identities are granted minimum necessary permissions based on their functional roles.
* **Raw Zone Restriction:** Direct human analyst access to the Raw (Bronze) zone is strictly prohibited. Raw data is accessible only by automated ingestion service accounts and platform engineers for debugging.
* **Audit Logging:** All access attempts, schema modifications, and administrative role assignments are logged to a centralized, immutable audit trail.

---

## 4. Governance, Privacy & Access Control Matrix

| Data Asset / Zone | Classification | Allowed Roles | Protection Mechanisms | Retention & Deletion Policy | Compliance & Audit Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Raw Clickstream & Orders** *(Bronze)* | Restricted Personal & Commercial | Ingestion service accounts; platform engineers only (No analysts). | AES-256 encryption at rest; TLS in transit; raw PII isolation. | Short-term raw retention (30 days); automated purge. | Catalog entry, access logs, ingestion pipeline health reports. |
| **Validated Customer Profiles** *(Silver)* | Confidential / Pseudonymized | Data Engineers, authorized BI Analysts (masked view). | Tokenization of identifiers; column-level masking for email/phone fields. | Retained for active account lifecycle + 1 year post-closure. | Lineage map, data quality test results, schema registry metadata. |
| **Curated Financial & LTV Tables** *(Gold)* | Internal Business / Aggregated | Finance Directors, Marketing Leads, Executive Management. | Aggregated data grain; elimination of raw cleartext PII; role-based row/column filters. | Retained according to statutory financial reporting rules (7 years). | Access audit logs, periodic access review sign-offs, financial closing reports. |
| **Real-Time Fraud Streams** | Operational Critical | Fraud Operations Team, automated streaming scoring engines. | Secure Kafka cluster encryption (SASL/SCRAM); tokenized session IDs. | 7-day rolling window in streaming buffer; aggregated metrics archived. | Real-time monitoring alerts, model scoring audit trails. |