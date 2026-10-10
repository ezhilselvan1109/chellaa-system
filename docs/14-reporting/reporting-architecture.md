# Reporting and Analytics Architecture

CQRS analytical architecture, decoupled event-driven ingestion, and materialized analytical stores.

---

## 1. CQRS Analytical Pipeline

```text
[Operational Microservices (Order, Payment, Inventory, Returns)]
                            |
                     (Transactional Outbox)
                            v
               [Apache Kafka Event Bus]
                            |
                            v
               [Reporting Service Consumer]
                            |
                 (Streaming Aggregations)
                            v
     [Reporting Database (reporting_db - PostgreSQL / Read-Model)]
                            |
                            v
     [Admin Web Analytics Dashboards & Scheduled CSV Exports]
```

---

## 2. Decoupling Transactional OLTP from Analytical OLAP

- Administrative analytics queries (e.g. "Calculate total revenue by category over the last 12 months grouped by week") NEVER run against the live `order_db` or `inventory_db`.
- These queries execute exclusively against `reporting_db` populated asynchronously via Kafka consumers, preventing analytical table locks from degrading customer checkout transactions.
