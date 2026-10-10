# ADR-001: Microservices Architecture over Modular Monolith

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Lead Engineer

---

## 1. Context

The platform is envisioned as an enterprise-scale single-owner e-commerce platform designed to sustain up to 5,000 requests per second during promotional flash sales, support high concurrent checkouts without inventory overselling, and enable independent deployment of distinct commercial domains (Catalog, Search, Inventory, Orders, Payments, Notifications). We evaluated whether to build as a Modular Monolith or an Event-Driven Microservices ecosystem.

---

## 2. Options Considered

1. **Option A: Modular Monolith (Spring Boot Single Process)**
   - *Pros*: Simplest local development; single database transaction boundaries; zero network latency between modules.
   - *Cons*: Shared database creates tight schema coupling; inability to scale high-contention inventory locks independently from read-heavy catalog queries; blast radius of any module crash impacts the entire store.
2. **Option B: Event-Driven Microservices (Chosen)**
   - *Pros*: Complete data isolation per service; independent horizontal auto-scaling (scaling inventory independently during flash sales); isolated failure blast radius; clear domain boundaries.
   - *Cons*: Distributed transaction complexity (requires Sagas); network overhead; distributed tracing operational requirements.

---

## 3. Decision

Adopt **Option B (Event-Driven Microservices)** with 16 autonomous Spring Boot services integrated via synchronous REST and asynchronous Apache Kafka event streaming.

---

## 4. Consequences

- **Positive**: High scalability, independent deployability, fault isolation, strict data ownership.
- **Negative / Trade-offs**: Distributed transaction complexity managed via Saga Orchestrator in Order Service and Transactional Outbox pattern.

---

## 5. Related Documents
- [System Architecture](file:///d:/learning/Microservice/backend/docs/04-architecture/system-architecture.md)
- [Microservice Boundaries](file:///d:/learning/Microservice/backend/docs/03-domain/service-boundaries.md)
