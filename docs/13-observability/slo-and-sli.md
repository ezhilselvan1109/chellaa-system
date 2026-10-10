# Service Level Indicators and Objectives (SLIs/SLOs)

Service level definitions, error budgets, and target reliability percentages.

---

## 1. Core SLIs and SLOs

| User Journey / Path | Service Level Indicator (SLI) | Service Level Objective (SLO) | Monthly Error Budget |
| :--- | :--- | :--- | :--- |
| **Storefront Browsing** | Proportion of successful (`2xx`/`3xx`) requests to `/api/v1/products/**` | **99.9%** availability over rolling 30 days | 0.1% (~43 minutes downtime) |
| **Catalog Query Latency**| Proportion of `/api/v1/search` requests completing in $<100\text{ms}$ | **95.0%** of requests over rolling 30 days | 5.0% slow requests |
| **Checkout Flow** | Proportion of checkout submissions completing successfully without 5xx | **99.95%** availability over rolling 30 days | 0.05% (~21.9 minutes downtime)|
| **Payment Processing** | Webhook acknowledgment and order confirmation within $<3\text{s}$ | **99.5%** of transactions over rolling 30 days| 0.5% delayed webhooks |
| **Notification Dispatch**| Transactional notification delivered to provider in $<15\text{s}$ | **99.0%** of notifications over rolling 30 days| 1.0% delayed dispatches |
