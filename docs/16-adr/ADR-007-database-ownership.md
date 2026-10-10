# ADR-007: Strict Database-per-Service Ownership

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Lead Engineer

---

## 1. Context

In microservices architectures, data coupling is the most pernicious form of tight coupling. We evaluated whether to allow read-only cross-database joins or mandate 100% database encapsulation per service.

---

## 2. Options Considered

1. **Option A: Shared Relational Schema / Cross-Database Joins**
   - *Pros*: Simple cross-boundary reporting; zero data duplication.
   - *Cons*: Tight coupling; schema refactoring breaks foreign services; violates bounded contexts; locks tables across service domains.
2. **Option B: Strict Database-per-Service Isolation (Chosen)**
   - *Pros*: Autonomous microservices; services can refactor private tables without breaking clients; independent scaling and optimization; clear failure boundaries.
   - *Cons*: Data required across services must be synchronized via events or fetched via REST APIs.

---

## 3. Decision

Enforce **Strict Database-per-Service Ownership**. Every microservice owns its private database. No service may query or mutate another service's tables directly.

---

## 4. Consequences

- **Positive**: Complete domain encapsulation, zero accidental schema coupling, independent database migrations.
- **Negative**: Foreign key constraints cannot span microservice boundaries; data consistency across boundaries must be managed via Sagas and events.

---

## 5. Related Documents
- [Data Ownership](file:///d:/learning/Microservice/backend/docs/04-architecture/data-ownership.md)
- [Service Database Ownership Matrix](file:///d:/learning/Microservice/backend/docs/05-database/service-database-ownership.md)
