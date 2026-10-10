---
name: architecture-design
description: >-
  Use this skill to analyze system requirements, evaluate engineering trade-offs, define microservice
  boundaries, evaluate coupling, enforce data ownership, and author or update Architecture Decision Records (ADRs).
---

# Architecture Design Skill

## 1. Purpose
Provides standardized methodology for making, evaluating, and documenting microservice and distributed systems architecture decisions, ensuring high availability, strict data isolation, and alignment with the single-owner e-commerce business model.

## 2. When to Use It
- Defining or refining microservice boundaries.
- Introducing a new data store or communication pattern.
- Evaluating trade-offs between synchronous REST and asynchronous event streaming.
- Drafting or modifying Architecture Decision Records (ADRs).
- Reviewing system topology and resilience models (Sagas, Circuit Breakers, Outbox).

## 3. Required Inputs
- Functional and non-functional requirements (`docs/01-requirements/`).
- Bounded contexts and domain models (`docs/03-domain/`).
- Target SLAs, throughput, and consistency requirements.
- Core governance rules (`AGENTS.md`).

## 4. Procedure
1. **Analyze Requirements & Invariants**: Confirm the feature belongs to the single-owner business model; reject any multi-vendor or marketplace constructs.
2. **Evaluate Service Coupling**: Identify whether the capability belongs within an existing microservice aggregate or warrants an autonomous bounded context.
3. **Analyze Data Ownership**: Determine primary entity ownership. Verify that no foreign service will directly query the proposed data store.
4. **Choose Communication Pattern**:
   - Use synchronous REST for user-facing, immediate-consistency queries.
   - Use asynchronous Kafka events for state progression, notifications, analytics, and cross-service eventual consistency.
   - Use Orchestrated Sagas for multi-service write workflows (e.g., Checkout).
5. **Formulate Trade-off Analysis**: Compare at least 2 architecture alternatives against latency, complexity, cost, and resilience.
6. **Author ADR**: Document the decision in `docs/16-adr/ADR-XXX-<name>.md`.
7. **Update Architecture Blueprints**: Update `docs/04-architecture/` and `ARCHITECTURE.md`.

## 5. Rules & Constraints
- **Strict Single-Owner Model**: Never introduce seller onboarding, marketplace commissions, or vendor splits.
- **Strict Data Isolation**: Every microservice must own its private database. Cross-database queries are forbidden.
- **No Speculative Microservices**: Do not split services prematurely without clear domain separation or distinct scaling requirements.

## 6. Expected Outputs
- Completed ADR file in `docs/16-adr/`.
- Updated topology diagrams and narrative in `docs/04-architecture/`.

## 7. Validation Checklist
- [ ] Does the design respect the single-owner business invariant?
- [ ] Is data ownership 100% exclusive to a single service?
- [ ] Are failure modes, timeouts, and fallbacks explicitly defined?
- [ ] Does the ADR clearly articulate the negative consequences / trade-offs?

## 8. Common Failure Modes
- Creating microservices that share a database table.
- Using REST for distributed multi-step transactions without compensation logic.
- Neglecting outbox pattern when updating local state and publishing events.

## 9. Authoritative Links
- [AGENTS.md](file:///d:/learning/Microservice/backend/AGENTS.md)
- [System Architecture](file:///d:/learning/Microservice/backend/docs/04-architecture/system-architecture.md)
- [ADR Directory](file:///d:/learning/Microservice/backend/docs/16-adr/)
