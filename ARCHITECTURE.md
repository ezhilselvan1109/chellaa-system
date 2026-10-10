# High-Level System Architecture

This document provides a consolidated architectural overview of the Single-Owner E-Commerce Platform. Detailed designs reside in `docs/04-architecture/`.

---

## 1. System Context & Topology

The platform is designed as an event-driven microservices ecosystem fronted by an API Gateway, separating Customer and Admin traffic, with asynchronous background workflows powered by Apache Kafka.

```
                      +-----------------------------+
                      |   Client Tier / Frontends   |
                      +-----------------------------+
                       /                           \
         (HTTPS/WSS)  /                             \  (HTTPS/MFA)
                     v                               v
           +--------------------+          +--------------------+
           | Customer Web (SPA) |          |  Admin Web (SPA)   |
           +--------------------+          +--------------------+
                     \                               /
                      \                             /
                       v                           v
              +------------------------------------------+
              |           API Gateway Cluster            |
              |   (Routing, SSL Term, Rate Limit, Auth)  |
              +------------------------------------------+
                                   |
    +------------------------------+------------------------------+
    |                              |                              |
    v                              v                              v
+------------------------+ +------------------------+ +------------------------+
|    Core Commerce       | |    Fulfillment         | |    Engagement          |
+------------------------+ +------------------------+ +------------------------+
| - Identity Service     | | - Inventory Service    | | - Review Service       |
| - Customer Service     | | - Shipping Service     | | - Notification Service |
| - Catalog Service      | | - Return Service       | | - Reporting Service    |
| - Pricing Service      | | - Refund Service       | +------------------------+
| - Promotion Service    | +------------------------+
| - Cart Service         |
| - Order Service (Saga) |
| - Payment Service      |
+------------------------+
    |                              |                              |
    +------------------------------+------------------------------+
                                   |
                                   v
             +--------------------------------------------+
             |             Event Streaming Bus            |
             |       (Apache Kafka - Distributed Log)     |
             +--------------------------------------------+
                                   |
         +-------------------------+-------------------------+
         |                         |                         |
         v                         v                         v
+-------------------+     +-------------------+     +-------------------+
| Search Index Sync |     | Async Analytics   |     | Multi-Channel     |
| (OpenSearch)      |     | (Reporting Read)  |     | Notifications     |
+-------------------+     +-------------------+     +-------------------+
```

---

## 2. Microservice Inventory & Core Responsibilities

| Service | Primary Responsibility | Data Store | Key Communication |
| :--- | :--- | :--- | :--- |
| **API Gateway** | Request routing, SSL termination, JWT token validation, rate-limiting | Redis | Synchronous HTTP |
| **Identity Service** | User credentials, password reset, MFA, refresh tokens, role assignment | PostgreSQL | Synchronous REST & Kafka Auth events |
| **Customer Service** | Profiles, saved addresses, communication preferences | PostgreSQL | Synchronous REST & Kafka Customer events |
| **Catalog Service** | Products, variants, SKU, categories, brands, specifications, media | PostgreSQL | REST & Kafka Catalog change events |
| **Search Service** | Full-text query, autocomplete, faceting, typo-tolerance | OpenSearch | Consumes Kafka; Serves REST queries |
| **Pricing Service** | Base pricing, currency calculation, tax computation, discount resolution | PostgreSQL & Redis | Synchronous REST (high throughput) |
| **Promotion Service**| Campaigns, flash sales, coupon verification, usage counters | PostgreSQL & Redis | REST & Kafka promotion events |
| **Cart Service** | Ephemeral guest & authenticated customer carts, real-time revalidation | Redis & PostgreSQL | Synchronous REST |
| **Inventory Service** | Multi-warehouse stock levels, reservations, stock adjustments, safety stock | PostgreSQL & Redis | REST & Kafka Stock events |
| **Order Service** | Order lifecycle, Checkout Saga Orchestrator, invoice snapshot generation | PostgreSQL | REST & Kafka Order events |
| **Payment Service** | Payment gateway integration, idempotency, webhook processing, signature check | PostgreSQL | REST, Webhooks & Kafka Payment events |
| **Shipping Service** | Delivery zones, courier dispatches, shipment tracking, label generation | PostgreSQL | REST & Kafka Shipping events |
| **Return Service** | Return authorization, return window validation, pickup scheduling | PostgreSQL | REST & Kafka Return events |
| **Refund Service** | Ledger-backed refund execution, idempotency, provider reconciliation | PostgreSQL | REST & Kafka Refund events |
| **Review Service** | Verified-purchase reviews, star ratings, content moderation | PostgreSQL | REST & Kafka Review events |
| **Notification Service**| Template engine, multi-channel dispatch (Email, SMS, WhatsApp, Push) | MongoDB / PostgreSQL | Consumes Kafka; Provider APIs |
| **Reporting Service**| De-normalized read models, sales dashboards, analytics, aggregation | PostgreSQL / ClickHouse | Consumes Kafka (CQRS read-model) |

---

## 3. Distributed Integration Patterns

### 3.1 Synchronous Communication (REST)
- Used for low-latency queries requiring immediate response (e.g., cart operations, search queries, payment initiation).
- Standardized RESTful contracts with JSON payload, semantic HTTP status codes, and `RFC 7807` Problem Details error formatting.
- Client-side resilience via Resilience4j (timeouts, retries with jitter, circuit breakers).

### 3.2 Asynchronous Communication (Apache Kafka)
- Used for cross-service workflow progression, notifications, analytics, and derived index synchronization.
- **Transactional Outbox Pattern**: Database mutations and domain events are committed atomically within the same local ACID transaction; an Outbox Relay reliably pushes to Kafka.
- **Idempotent Consumers**: Every consumer verifies event uniqueness against an `idempotency_log` table before executing side-effects.

### 3.3 Distributed Transactions: Orchestrated Saga Pattern
- Long-running distributed business processes (e.g., Checkout: Inventory Reservation -> Payment Authorization -> Order Confirmation) are coordinated by the **Order Service** as Saga Orchestrator.
- If any step fails (e.g., payment decline or timeout), compensating transactions are triggered in reverse order (e.g., release reserved inventory, cancel pending order).

---

## 4. Cross-Cutting Architectural Pillars

1. **Database-Per-Service**: Strict isolation. Cross-database foreign keys and cross-schema queries are prohibited.
2. **Zero-Trust Security**: Mutual TLS (mTLS) for inter-service communication; JWT tokens with fine-grained RBAC scopes for client requests.
3. **Observability by Design**: OpenTelemetry standards across all services, injecting `traceId` and `spanId` across HTTP headers (`traceparent`) and Kafka headers.
4. **Single-Owner Invariant**: All business boundaries reflect single-tenant owner operations across single or multiple owned warehouses.
