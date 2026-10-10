# Enterprise Single-Owner E-Commerce Platform

Welcome to the engineering repository for the large-scale, production-oriented, single-owner e-commerce platform.

## 1. Project Mission & Business Model

This repository is built from the ground up using professional software engineering practices, Domain-Driven Design (DDD), microservices architecture, event-driven communication (Apache Kafka), transactional outbox patterns, automated testing, and comprehensive AI-agent workflows.

### Single-Owner Business Model
- **Ownership**: Exactly one business owner operates the store and centrally controls the catalog, products, pricing, inventory, campaigns, promotions, orders, payments, shipments, returns, refunds, customer care, and analytics.
- **Customers**: End-consumers browse products, manage carts, checkout, track orders, request returns, and post reviews.
- **Administrators**: Internal staff and authorized operations personnel manage operations via an internal Admin Web application.
- **Strict Boundary**: This platform is **NOT A MARKETPLACE**. There is **NO** seller onboarding, no multi-vendor dashboard, no seller commission, no seller settlement, and no multi-vendor order splitting.
- **Warehousing**: Multiple physical warehouses are supported, but all facilities belong exclusively to the single business owner.

---

## 2. Technology Stack Overview

| Category | Technology | Primary Purpose |
| :--- | :--- | :--- |
| **Backend** | Java 21, Spring Boot 3.3+ | Robust enterprise microservices with strong typing and mature ecosystem |
| **Relational Database** | PostgreSQL 16 | ACID source of truth for transactions, orders, inventory, pricing, customers |
| **Document Store** | MongoDB 7.0 (Optional/Targeted) | Audit trail logs, notification rendering history where schemas are polymorphic |
| **Cache & Distributed State** | Redis 7.2 | Idempotency keys, distributed rate limiting, session cache, cart caching |
| **Event Streaming** | Apache Kafka 3.7 | Asynchronous event-driven domain communication, decoupled processing, outbox pub/sub |
| **Search Engine** | OpenSearch 2.13 / Elasticsearch | Full-text catalog search, faceting, typo-tolerance, autocomplete |
| **Object Storage** | MinIO / AWS S3 Compatible | Product imagery, spec sheets, downloadable PDF invoices, return inspection media |
| **Frontend** | React 18, TypeScript, TailwindCSS/Vanilla | Customer Web storefront & Admin Operations Portal |
| **Container & Orchestration**| Docker, Docker Compose, Kubernetes | Local reproducibility and scalable cloud-native deployments |
| **Observability** | OpenTelemetry, Prometheus, Grafana, Jaeger | Distributed tracing, unified structured metrics, health telemetry |

---

## 3. Repository Architecture & Layout

```text
.
├── AGENTS.md                  # Strict AI-agent operating rules, constraints, and instructions
├── README.md                  # Project overview and directory map
├── ARCHITECTURE.md            # High-level system architecture and integration topology
├── CHANGELOG.md               # Versioning and milestone changes
│
├── .agent/                    # Native agent customization and automation assets
│   ├── workflows/             # Deterministic SOP execution pipelines
│   └── skills/                # Domain-specific procedural playbooks
│
├── docs/                      # Authoritative Source of Truth Documentation
│   ├── 00-project/            # Vision, scope, personas, feature catalog, glossary
│   ├── 01-requirements/       # Functional and non-functional requirements, user stories
│   ├── 02-business-rules/     # Exhaustive business rules per domain context
│   ├── 03-domain/             # DDD aggregates, bounded contexts, entities, value objects
│   ├── 04-architecture/       # Microservices topology, Saga, Outbox, resilience, caching
│   ├── 05-database/           # Schemas, service database ownership, migrations, indexing
│   ├── 06-api/                # REST conventions, error formats, contracts per service
│   ├── 07-events/             # Kafka schemas, topic strategy, partitioning, dead-lettering
│   ├── 08-security/           # RBAC, JWT tokens, mTLS, data encryption, secrets
│   ├── 09-frontend/           # React customer & admin UI architecture, state management
│   ├── 10-notification/       # Multi-channel notification engine (Email, SMS, WhatsApp, Push)
│   ├── 11-testing/            # Testing pyramids, concurrency, chaos, and contract testing
│   ├── 12-infrastructure/     # Docker Compose, Kubernetes, CI/CD, local dev runbooks
│   ├── 13-observability/      # OpenTelemetry, structured logging, SLI/SLO dashboards
│   ├── 14-reporting/          # Event-driven CQRS read models, analytical aggregations
│   ├── 15-implementation/     # Feature matrix, roadmaps, task breakdowns, readiness
│   └── 16-adr/                # Architecture Decision Records (ADR-001 to ADR-012)
│
├── apps/                      # Frontend Applications
│   ├── customer-web/          # Storefront React SPA
│   └── admin-web/             # Operations Admin React SPA
│
├── services/                  # Spring Boot Microservices
│   ├── api-gateway/           # Spring Cloud Gateway routing & edge rate-limiting
│   ├── identity-service/      # IAM, auth tokens, MFA, credentials
│   ├── customer-service/      # Customer profiles, addresses, preferences
│   ├── catalog-service/       # Products, categories, brands, variants, media
│   ├── search-service/        # OpenSearch sync, search queries, faceting
│   ├── pricing-service/       # Base pricing, tiered pricing, tax calculations
│   ├── promotion-service/     # Campaigns, flash sales, coupon evaluation
│   ├── cart-service/          # Persistent customer & guest carts, live repricing
│   ├── inventory-service/     # Multi-warehouse stock, reservations, locking
│   ├── order-service/         # Order state machine, checkout saga orchestrator
│   ├── payment-service/       # Payment gateway adapter, webhooks, reconciliation
│   ├── shipping-service/      # Delivery zones, courier dispatches, tracking
│   ├── return-service/        # Return requests, inspection, approval
│   ├── refund-service/        # Ledger-backed refund execution
│   ├── review-service/        # Verified-buyer ratings and reviews moderation
│   ├── notification-service/  # Multi-channel dispatch, templating engine
│   └── reporting-service/     # Analytics, sales dashboards, inventory reports
│
├── packages/                  # Shared libraries and SDKs (e.g., event schemas, DTOs)
└── infrastructure/            # Local Docker Compose files, Helm charts, CI pipelines
```

---

## 4. Documentation Precedence & Philosophy

Documentation in this repository is **executable truth**:
1. No application code is written without prior approved architecture and API/Event contracts.
2. If code conflicts with documentation, documentation is reviewed or updated via formal ADR/PR.
3. Every microservice strictly owns its data store. Cross-service database queries are prohibited.
4. Asynchronous integration occurs via versioned Kafka events backed by the Transactional Outbox pattern.

For details on working within this repository as an AI agent or human engineer, read [AGENTS.md](file:///d:/learning/Microservice/backend/AGENTS.md).
