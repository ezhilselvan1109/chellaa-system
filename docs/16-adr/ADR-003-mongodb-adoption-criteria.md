# ADR-003: MongoDB Adoption Criteria and Targeted Usage

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Data Architect

---

## 1. Context

The master architecture mandates that MongoDB should NOT be added arbitrarily across microservices merely because it is popular. We evaluated whether any specific workloads legitimately require document-oriented modeling.

---

## 2. Options Considered

1. **Option A: PostgreSQL JSONB for Everything**
   - *Pros*: Single database technology across all services; simplified infrastructure operations.
   - *Cons*: For highly polymorphic schemas (e.g. multi-channel notification templates across 5 channels with dynamic UI action button schemas, and full administrative audit payload histories), document stores provide superior document versioning and native nested document validations.
2. **Option B: Targeted MongoDB for Specific Document Workloads (Chosen)**
   - *Pros*: Optimal developer velocity for polymorphic notification template catalogs (`notification_db`) and audit trail diff archives (`audit_db`).
   - *Cons*: Introduces MongoDB operational overhead (replica sets, backup tooling).

---

## 3. Decision

Adopt **MongoDB 7.0 targeted exclusively for `notification-service` templates/rendering logs and `audit-logging`**. All core transactional business entities (Orders, Inventory, Payments, Pricing) strictly remain in PostgreSQL.

---

## 4. Consequences

- **Positive**: Clean document modeling for rich notification templates without polluting relational schemas.
- **Negative**: Adds a MongoDB container in Docker Compose and Kubernetes clusters.

---

## 5. Related Documents
- [MongoDB Design](file:///d:/learning/Microservice/backend/docs/05-database/mongodb-design.md)
- [Notification Architecture](file:///d:/learning/Microservice/backend/docs/10-notification/notification-architecture.md)
