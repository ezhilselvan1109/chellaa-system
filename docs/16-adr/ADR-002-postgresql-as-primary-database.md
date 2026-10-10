# ADR-002: PostgreSQL 16 as Primary Relational Database

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Data Architect

---

## 1. Context

E-commerce core business records (Orders, Payments, Inventory stocks, Pricing, Customer addresses, Invoices) mandate strict ACID transactional guarantees, strong foreign key constraints, row-level locking for concurrency, and robust financial arithmetic. We evaluated PostgreSQL, MySQL, and DynamoDB.

---

## 2. Options Considered

1. **Option A: PostgreSQL 16 (Chosen)**
   - *Pros*: Best-in-class ACID compliance; powerful JSONB with GIN indexing; advanced indexing (B-Tree, Partial, Expression); native `SELECT FOR UPDATE` pessimistic locking; mature Flyway migration tooling.
   - *Cons*: Requires connection pooling management (HikariCP/PgBouncer) under high pod counts.
2. **Option B: MySQL 8.0**
   - *Pros*: Widespread adoption.
   - *Cons*: Inferior JSON querying capabilities compared to PostgreSQL JSONB; less flexible partial indexing.
3. **Option C: DynamoDB / NoSQL**
   - *Pros*: Seamless horizontal scaling.
   - *Cons*: High query design complexity; lacks native relational constraints; expensive transactional locks for double-entry financial ledgers.

---

## 3. Decision

Adopt **PostgreSQL 16** as the default relational source of truth for all transactional microservices.

---

## 4. Consequences

- **Positive**: Absolute data integrity, rock-solid ACID transactions, rich JSONB flexibility for variant snapshots and webhook payloads.
- **Negative**: Requires strict connection pool budgeting per pod to prevent PostgreSQL connection saturation.

---

## 5. Related Documents
- [PostgreSQL Design](file:///d:/learning/Microservice/backend/docs/05-database/postgresql-design.md)
- [Database Selection](file:///d:/learning/Microservice/backend/docs/05-database/database-selection.md)
