# ADR-008: Transactional Outbox and Orchestrated Saga Patterns

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Distributed Systems Engineer

---

## 1. Context

Coordinating multi-service workflows (Checkout: Inventory Hold -> Payment Charge -> Order Confirm) without blocking two-phase commit (2PC) protocols requires distributed consistency mechanisms. Additionally, updating local database state and publishing events to Kafka must avoid dual-write inconsistencies.

---

## 2. Options Considered

1. **Option A: Choreographed Saga**
   - *Pros*: No centralized coordinator; services listen to peer events and execute next steps.
   - *Cons*: Cyclic dependencies; hard to track global state; complex rollback coordination; difficult to debug during production incidents.
2. **Option B: Orchestrated Saga with Transactional Outbox (Chosen)**
   - *Pros*: Centralized coordinator (`order-service`) explicitly tracks current saga step and issues commands/compensations; clear audit log; Transactional Outbox guarantees atomic publication from local database commits.
   - *Cons*: Orchestrator service becomes central to checkout coordination.

---

## 3. Decision

Adopt **Orchestrated Saga (Order Service as coordinator)** paired with **Transactional Outbox** across all publishing microservices.

---

## 4. Consequences

- **Positive**: Guaranteed message delivery, zero dual-write bugs, explicit compensating rollback paths on payment failures.
- **Negative**: Requires outbox table polling relays and saga state persistence tables.

---

## 5. Related Documents
- [Saga Pattern](file:///d:/learning/Microservice/backend/docs/04-architecture/saga-pattern.md)
- [Outbox Pattern](file:///d:/learning/Microservice/backend/docs/04-architecture/outbox-pattern.md)
