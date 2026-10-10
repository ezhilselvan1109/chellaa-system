# AGENTS.md — AI Agent Operating Rules & Governance

This document establishes the binding operating rules, architectural constraints, and behavioral contracts for any AI coding agent (including Antigravity) working in this repository.

---

## 1. Core Operating Rules

1. **Read Before Writing**: Always inspect relevant documentation (`docs/`) and existing code before proposing or modifying files.
2. **Inspect Existing Context**: Do not guess file structures, signatures, or conventions. Inspect the repository first.
3. **Strictly Single-Owner Business**:
   - **NEVER** introduce marketplace concepts.
   - **PROHIBITED**: Seller onboarding, seller dashboards, vendor commissions, seller payouts, multi-vendor cart splits, vendor accounts.
   - All catalog, inventory, pricing, campaigns, and orders belong solely to the single business owner.
   - Warehouses are single-owner fulfillment nodes.
4. **Service Boundary Sanctity**:
   - Never change microservice boundaries silently.
   - Never combine services or extract new services without an approved Architecture Decision Record (ADR).
5. **Absolute Data Ownership**:
   - Never query or write to another service's private database directly.
   - Inter-service communication must strictly use REST APIs (synchronous read/commands) or Apache Kafka events (asynchronous decoupled flows).
   - Shared database schemas across microservices are strictly forbidden.
6. **Technology Justification**:
   - Never introduce an arbitrary technology, library, or framework without a documented ADR showing trade-offs, operational complexity, and alternatives.
   - PostgreSQL is the default ACID transactional database. MongoDB is restricted to documented document workloads (e.g., polymorphic notification templates / audit logs). Redis is strictly for cache/ephemeral state.
7. **Strict Contract Adherence**:
   - Follow approved API contracts (`docs/06-api/`) and Kafka event schemas (`docs/07-events/`).
   - All events must be versioned, immutable JSON or Avro schemas with CloudEvents-compatible metadata.
8. **Schema Evolution via Migrations**:
   - Never modify a database schema with manual SQL scripts or ad-hoc adjustments.
   - Use Flyway or Liquibase migration scripts versioned in the service repository.
9. **Preserve Transactional Boundaries**:
   - Never perform remote network calls inside a local database ACID transaction.
   - Use the Transactional Outbox pattern to reliably publish Kafka events from database state changes.
10. **Mandatory Idempotency**:
    - All state-mutating REST APIs must support `Idempotency-Key` headers where duplicate requests are possible (e.g., checkout, payments, refunds).
    - All Kafka consumers must implement idempotent processing via message deduplication tables.
11. **Resilience & Distributed Failure Handling**:
    - Plan for network partitions, provider timeouts, and service restarts.
    - Implement Sagas (Orchestrated for Checkout/Orders) with explicit compensating transactions.
    - Implement Dead Letter Queues (DLQ) and exponential backoff for consumer failures.
12. **Secret Safety**:
    - NEVER hardcode secrets, API keys, passwords, private certificates, or production webhook secrets in source code, configuration files, or documentation.
    - Use environment variables and secrets manager abstractions.
13. **Verifiable Test Execution**:
    - NEVER claim a test passed unless it was actually executed via terminal tooling and returned exit code 0.
    - Provide test evidence with commands and results.
14. **Verifiable Feature Completion**:
    - Never mark a feature "Complete" or "Verified" in documentation or the feature matrix without verifying all acceptance criteria and automated tests.
    - A generated code file is NOT proof of completion.
15. **Test-Driven Changes**:
    - Every change to business logic must be accompanied by corresponding automated unit and integration tests covering both happy paths and edge cases.
16. **Documentation Synchronization**:
    - Whenever code or contracts change, update the corresponding documentation immediately in the same change set.
17. **Feature Matrix & Progress Tracking**:
    - Keep `docs/15-implementation/feature-matrix.md` and `docs/15-implementation/progress.md` accurate and up-to-date.
18. **Atomic, Reviewable Changes**:
    - Keep changes cohesive, small, well-documented, and scoped to the task at hand.
19. **Scope Discipline**:
    - Do not implement unrelated features, speculative abstractions, or out-of-scope code.
20. **Preserve Business Invariants**:
    - Never silently alter business rules (e.g., discount stacking, return windows, reservation timeouts) without approval.
21. **Clarify Ambiguities**:
    - If a requirement or business rule is missing or ambiguous, document the question in `docs/00-project/open-questions.md` or seek user clarification before writing flawed logic.
22. **Transparent Limitations**:
    - Report known edge cases, untested failure modes, or technical debt proactively.
23. **Preserve Conventions**:
    - Adhere to the established repository layout, coding style, naming conventions, and package architecture.
24. **No Premature Phase Completion**:
    - Never mark an architectural or implementation phase complete solely because placeholder files were created.

---

## 2. Source-of-Truth Hierarchy

When evaluating conflicting instructions or design choices, follow this precedence order:
1. **Explicit Project Constraints & Approved Business Rules** (Single-owner model, non-negotiable rules).
2. **Approved Architecture Decision Records** (`docs/16-adr/`).
3. **Approved Domain & Architecture Documentation** (`docs/02-business-rules/`, `docs/03-domain/`, `docs/04-architecture/`).
4. **Approved Database, API, and Event Contracts** (`docs/05-database/`, `docs/06-api/`, `docs/07-events/`).
5. **Engineering & Coding Standards** (`docs/11-testing/`, `docs/08-security/`).
6. **Reusable Skill & Workflow Playbooks** (`.agent/skills/`, `.agent/workflows/`).
7. **Individual Prompt Instructions** (unless they explicitly countermand higher tiers).

---

## 3. Invocation Protocol

Before executing any engineering task:
1. Identify the matching workflow in `.agent/workflows/`.
2. Inspect the required input skills in `.agent/skills/`.
3. Check the prerequisites and read the corresponding domain specifications.
4. Execute steps incrementally following the workflow sequence.
5. Verify outputs using automated checks.
6. Update documentation and progress tracking.
