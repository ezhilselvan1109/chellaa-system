---
name: domain-design
description: >-
  Use this skill to model Domain-Driven Design (DDD) bounded contexts, entities, value objects,
  aggregates, state machines, domain invariants, and domain events across the e-commerce platform.
---

# Domain Design Skill

## 1. Purpose
Provides a disciplined Domain-Driven Design (DDD) framework to model unambiguous business boundaries, aggregate roots, transactional invariants, value objects, and domain events.

## 2. When to Use It
- Designing a new microservice core domain.
- Modeling complex lifecycles (Order state machine, Inventory reservation, Return workflow).
- Defining immutable value objects (Money, Address, Sku, Quantity).
- Specifying domain events triggered upon state transitions.

## 3. Required Inputs
- Business rules from `docs/02-business-rules/`.
- Domain glossary and language (`docs/00-project/glossary.md`).
- Functional requirements from `docs/01-requirements/`.

## 4. Procedure
1. **Identify Bounded Context**: Delineate domain boundaries; avoid anemic or overlapping models.
2. **Identify Aggregate Roots**: Locate the transactional consistency boundary. An aggregate root encapsulates internal entities and enforces invariants.
3. **Model Value Objects**: Identify immutable concepts without identity (e.g., `Money(amount, currency)`, `Address`, `Dimension`).
4. **Define State Machines**: Map legal state transitions, terminal states, and guard conditions (e.g. Order: `PENDING_PAYMENT` -> `PAID` -> `PROCESSING` -> `SHIPPED` -> `DELIVERED`).
5. **Formulate Domain Events**: Name events in past tense representing immutable business facts (e.g., `OrderPlaced`, `InventoryReserved`, `PaymentFailed`).
6. **Publish Domain Models**: Update `docs/03-domain/`.

## 5. Rules & Constraints
- Aggregate boundaries must correspond to local ACID database transaction boundaries.
- Never modify multiple aggregates within a single local transaction across microservice boundaries.
- Money arithmetic must always encapsulate currency checks and rounding policies.

## 6. Expected Outputs
- Domain aggregate diagrams and entity schemas in `docs/03-domain/`.
- Domain event specifications in `docs/03-domain/domain-events.md`.

## 7. Validation Checklist
- [ ] Are aggregate roots strictly responsible for enforcing business invariants?
- [ ] Are state transitions strictly one-way or explicit reversible transitions?
- [ ] Are value objects completely immutable?

## 8. Common Failure Modes
- Building giant monolithic aggregates that lock large parts of the database.
- Anemic domain models where domain logic leaks into controllers or UI.
- Circular references between domain models.

## 9. Authoritative Links
- [Domain Model](file:///d:/learning/Microservice/backend/docs/03-domain/domain-model.md)
- [Aggregates](file:///d:/learning/Microservice/backend/docs/03-domain/aggregates.md)
- [Domain Events](file:///d:/learning/Microservice/backend/docs/03-domain/domain-events.md)
