# Analytical Questions & Decision Framework: OmniChannel Retail Platform

This document outlines the core analytical questions designed to guide business decisions, operations, and strategic planning for the OmniChannel Retail platform. Each question specifies the target population, required data grain, freshness constraints, and intended user groups.

---

### Question 1: Real-Time Fraud & Anomaly Detection (Operational)
* **Analytical Question:** Which active user sessions exhibit high-velocity payment submission anomalies or multi-card testing patterns within the last 5 minutes?
* **Metric & Grain:** Count of failed payment attempts and velocity score per user session, evaluated at the real-time session/event grain (`session_id`).
* **Required Data:** Clickstream event stream (`clickstream_events`), active order attempts (`orders`), joined with historical risk profiling tables.
* **Freshness:** Real-time / Near-real-time (Maximum acceptable delay: under 2 minutes).
* **Decision & User:** **Fraud Operations Team** acts immediately to block compromised sessions or flag high-risk transactions before fulfillment. Success is measured by a reduction in chargeback rates while preserving legitimate checkout conversion.
* **Limitations:** Risk of false positives blocking legitimate customers during high-traffic promotional spikes (e.g., Black Friday).

---

### Question 2: Hourly Regional Fulfillment Bottlenecks (Tactical)
* **Analytical Question:** Which regional fulfillment centers are experiencing processing queue delays exceeding 30 minutes for completed orders during the current operating day?
* **Metric & Grain:** Average processing duration and backlog count per regional warehouse, evaluated at the hourly regional warehouse grain (`warehouse_id`, `hour`).
* **Required Data:** Order items (`order_items`), fulfillment logs (`fulfillment_status`), and warehouse metadata tables (`warehouses`).
* **Freshness:** Frequently refreshed (Maximum acceptable delay: every 15 to 30 minutes).
* **Decision & User:** **Logistics & Warehouse Managers** reallocate staff or shift shipping queues dynamically across regional distribution hubs to meet service-level agreements (SLAs).
* **Limitations:** Temporary network disruptions or scanner lags in physical warehouses can create artificial latency spikes in metrics.

---

### Question 3: Customer Lifetime Value (LTV) Segmentation (Strategic)
* **Analytical Question:** How do customer value tiers (Premium, High, Developing, Standard) distribute across geographic regions based on their 12-month trailing completed spend?
* **Metric & Grain:** Total completed spend, order counts, and customer count per value tier and region, evaluated at the individual customer grain (`customer_id`).
* **Required Data:** Historical orders (`orders`), customer profiles (`customers`), and regional mapping reference tables.
* **Freshness:** Daily batch update (Maximum acceptable delay: 24 hours / next-day financial report).
* **Decision & User:** **Marketing & Executive Teams** allocate promotional budgets, loyalty rewards, and targeted campaigns toward high-value customer segments.
* **Limitations:** Historical spend may not accurately predict future churn or macroeconomic purchasing shifts.

---

### Question 4: Category Profitability & Margin Trend Analysis (Historical)
* **Analytical Question:** What is the trailing 3-month moving average of net revenue and profit margin per product category across annual reporting periods?
* **Metric & Grain:** Monthly net revenue, cost of goods sold (COGS), and trailing 3-month moving average, evaluated at the calendar month and product category grain (`sales_month`, `category`).
* **Required Data:** Completed order items (`order_items`), product pricing and cost metadata (`products`), aggregated via window functions.
* **Freshness:** Daily/Monthly batch update (Maximum acceptable delay: 24 hours for daily financial closing).
* **Decision & User:** **Merchandising & Finance Directors** adjust inventory procurement contracts, discount thresholds, and product catalog positioning.
* **Limitations:** Seasonality and sudden supply-chain cost fluctuations can distort trailing multi-month moving averages if unadjusted.