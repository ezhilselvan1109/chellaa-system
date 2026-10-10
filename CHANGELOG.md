# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-10-10

### Added
- **Phase 0 Foundation**: Complete architecture, documentation, workflows, and skills baseline established.
- Root governance files: `AGENTS.md`, `README.md`, `ARCHITECTURE.md`, `CHANGELOG.md`.
- Comprehensive SOP workflows in `.agent/workflows/` (Documentation, Architecture, Feature Development, Microservices, Database, API, Events, Notification, Testing, Code Review, Bug Fix, Release).
- Antigravity agent skills in `.agent/skills/` with standard frontmatter and checklists.
- Exhaustive documentation structure in `docs/` covering:
  - 00: Project overview, scope, personas, feature catalog, glossary, open questions.
  - 01: Functional, non-functional requirements, user stories, acceptance criteria, traceability.
  - 02: Business rules across all commerce domains (single-owner model strictly enforced).
  - 03: Domain models, bounded contexts, aggregates, entities, domain events.
  - 04: Microservices architecture, Saga orchestrator, Outbox pattern, caching, resilience.
  - 05: Database ownership, PostgreSQL schemas, indexing, migrations, concurrency.
  - 06: REST API conventions, versioning, idempotency, service-specific API contracts.
  - 07: Kafka event streaming, CloudEvents envelope, schema contracts, DLQ strategies.
  - 08: Security architecture, RBAC, JWT tokens, threat models, secret management.
  - 09: Frontend architecture for Customer Web and Admin Web SPAs.
  - 10: Multi-channel Notification platform (Email, SMS, WhatsApp Business, Push, In-App).
  - 11: Testing pyramid, concurrency, failure, contract, and chaos testing strategies.
  - 12: Infrastructure, Docker Compose, Kubernetes, CI/CD, environments.
  - 13: Observability, OpenTelemetry, structured logging, SLI/SLO dashboards.
  - 14: Event-driven reporting read-models, CQRS, sales and inventory analytics.
  - 15: Feature matrix, service roadmap, dependency map, implementation readiness gate.
  - 16: Sequential Architecture Decision Records (ADR-001 through ADR-012).
