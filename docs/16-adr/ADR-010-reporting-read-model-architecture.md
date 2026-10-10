# ADR-010: Event-Driven CQRS Read-Model Architecture for Reporting

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Data Architect

---

## 1. Context

Business owners and finance teams require real-time and historical dashboards (daily sales, category velocity, coupon usage, refund summaries). Running complex analytical aggregation queries directly on live transactional databases (`order_db`, `payment_db`) causes lock contention and degrades checkout throughput.

---

## 2. Options Considered

1. **Option A: Query Transactional Read Replicas Directly**
   - *Pros*: Simple setup; zero data transformation pipelines.
   - *Cons*: Highly normalized OLTP schemas require expensive multi-table joins across millions of rows; slow response times on multi-year reports.
2. **Option B: Event-Driven CQRS Materialized Reporting Store (Chosen)**
   - *Pros*: Dedicated `reporting_db` populated asynchronously via Kafka consumers; de-normalized daily summary tables; analytical queries execute in sub-10ms; zero impact on transactional databases.
   - *Cons*: Eventual consistency; requires midnight reconciliation cron to verify consistency with transactional ledgers.

---

## 3. Decision

Adopt **Option B (Event-Driven CQRS Reporting Service)** backed by PostgreSQL read-model tables, with a documented path to evaluate ClickHouse if data volume exceeds 50 million records.

---

## 4. Consequences

- **Positive**: Blazing fast analytical dashboards, completely insulated checkout databases, full historical backfill capabilities via Kafka replay.
- **Negative**: Reporting data is eventually consistent (typically $<5$ seconds lag).

---

## 5. Related Documents
- [Reporting Architecture](file:///d:/learning/Microservice/backend/docs/14-reporting/reporting-architecture.md)
- [Read Models](file:///d:/learning/Microservice/backend/docs/14-reporting/read-models.md)
