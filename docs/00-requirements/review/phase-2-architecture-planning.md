# Non-Binding Phase 2 Architecture Planning Package

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | `ARCH-PLAN-01` |
| **Title** | Non-Binding Phase 2 Architecture Planning & Governance Roadmap |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Repository** | [`ezhilselvan1109/chellaa-system`](https://github.com/ezhilselvan1109/chellaa-system) (Branch: `dev`) |
| **File Location** | `docs/00-requirements/review/phase-2-architecture-planning.md` |
| **Creation Date** | 2026-10-18 |
| **Author Role** | Principal Software Architect, Domain-Driven Design Specialist & Distributed Systems Engineer |
| **Governance Status** | **`NON_BINDING_PLANNING`** (Planning Artifact Only — Awaiting Formal Phase 1 Sign-Off) |
| **Phase 1 Baseline Status** | **`PENDING_APPROVAL`** (Maintained strictly per governance constraints) |

---

## 1. Purpose, Scope and Non-Binding Status

### 1.1 Purpose
This planning package defines the technical analysis plans, architectural evaluation frameworks, candidate domain boundaries, cross-cutting engineering strategies, comparative trade-offs, and deliverable acceptance criteria required before formal Phase 2 Technical Architecture design can begin for the Chellaa single-retailer e-commerce platform.

### 1.2 Non-Binding Status
* **Exploratory & Preparatory Only:** This artifact is strictly a preparatory planning document. It does **not** authorize final architectural decisions, select immutable production frameworks, prescribe mandatory microservice boundaries, create database schemas, or initiate software implementation.
* **Proposals Pending Authorization:** All technical patterns, structural options, and toolchain recommendations contained herein are explicitly designated as **`PROPOSED`**. None shall be treated as binding or approved until formal Phase 2 architecture design is authorized and individual Architecture Decision Records (ADRs) are ratified.
* **Strict Governance Gate:** Transitioning to formal technical architecture design under `docs/01-architecture/` is strictly prohibited until the Enterprise Business Owner executes the formal Phase 1 sign-off in [`docs/00-requirements/11-review-and-approval.md`](../11-review-and-approval.md).

### 1.3 Scope Boundaries
* **Strict Single-Retailer Invariant:** The architectural scope is exclusively confined to an enterprise owning and selling its proprietary inventory directly to customers. Zero third-party marketplace capabilities (such as external vendor onboarding, merchant administration portals, vendor commission ledgers, split payouts, multi-seller buy-boxes, or third-party inventory feeds) are evaluated or permitted.
* **Backend-First Implementation:** Architecture planning is focused entirely on backend business capabilities, data integrity, distributed transaction boundaries, and external integration resilience. Customer-facing web applications, mobile client apps, and administrative user interfaces are deferred to a subsequent frontend phase.

---

## 2. Current Project Lifecycle and Governance Restrictions

The governance state of the Chellaa platform remains strictly aligned with the baseline recorded in [`docs/00-requirements/11-review-and-approval.md`](../11-review-and-approval.md):

```markdown
### Official Governance State (Unmodified):
* Lifecycle Status: PENDING_APPROVAL
* Requirements Baseline: NOT_APPROVED (Subject to Human Review)
* Architecture Design Approval: NOT_GRANTED
* Software Implementation: NOT_STARTED
```

### Invariants Observed During Planning:
1. **Zero Status Changes:** No requirement, business rule, workflow, or deliverable has been transitioned to `APPROVED`.
2. **Zero Commercial Inventions:** No unresolved business question (`OQ-001` through `OQ-022`) has been decided on behalf of the business owner.
3. **No Premature Architecture Files:** No architectural specifications, C4 diagrams, or ADRs have been placed in `docs/01-architecture/`.
4. **No Code Implementation:** No source code, database migrations, API definitions, deployment manifests, or package installations have been performed.
5. **Traceability Preservation:** All architectural explorations maintain 100% backward traceability to the Phase 1 Business Requirements Document (BRD).

---

## 3. Architecture Objectives Derived from the BRD

The architectural planning is derived directly from the business objectives, core features, and non-functional requirements established in the Phase 1 BRD:

| Objective Category | Target Metrics & Constraints | Source Requirement IDs |
| :--- | :--- | :--- |
| **High Concurrency & Flash Sales** | Support a baseline of **100 orders/minute** with burst auto-scaling to **1,000 orders/minute** during promotional surges without inventory overselling or checkout degradation. | `NFR-SCL-001`, `FEAT-CHK-001`, `FEAT-INV-001`, `OQ-016` |
| **Catalog Scale & Discovery** | Maintain sub-second responsiveness across an active catalog of up to **100,000 SKUs** with rich multi-variant attributes and dynamic specifications. | `NFR-SCL-002`, `FEAT-CAT-001`, `FEAT-SRCH-001` |
| **Storefront Latency Budgets** | Faceted catalog search and browsing responses within **$\le 200\text{ms}$ ($p95$)**; complete checkout submission within **$\le 1,000\text{ms}$ ($p95$)**. | `NFR-PERF-001`, `NFR-PERF-002` |
| **High Availability & Business Continuity** | Core checkout availability of **99.9% uptime** during operational windows; automated database backups with **RPO $\le 5\text{ minutes}$** and **RTO $\le 2\text{ hours}$**. | `NFR-AVL-001`, `NFR-BCM-001`, `OQ-017` |
| **Zero Overselling & Stock Integrity** | Absolute stock integrity via atomic reservations during checkout; zero negative inventory balances under concurrent flash-sale load. | `BR-INV-001`, `BR-INV-002`, `BR-CHK-001`, `FR-INV-001` |
| **Financial & Statutory Compliance** | Sequential tax invoice generation with tamper-evident audit trails; full GST compliance (CGST/SGST/IGST); zero storage of sensitive cardholder PAN/CVV data. | `BR-FIN-001`, `BR-FIN-002`, `BR-SEC-001`, `NFR-SEC-001`, `DEP-004` |
| **Security & Data Privacy** | Cryptographic customer password hashing (bcrypt/Argon2id); AES-256 PII encryption at rest; automated PII redaction from operational log streams; strict staff RBAC. | `FR-SEC-001`, `NFR-SEC-002`, `NFR-SEC-003`, `BR-ADM-001`, `BR-ADM-002` |
| **Operational Traceability** | 100% of distributed transactions tagged with immutable W3C Trace IDs; Write-Once, Read-Many (WORM) audit logging of privileged back-office mutations. | `NFR-OBS-001`, `NFR-OBS-002`, `FR-AUD-001`, `BR-AUD-001`, `BR-OPS-001` |

---

## 4. Architecture Principles to Evaluate

The following fundamental engineering principles will be evaluated during formal Phase 2 architecture design to ensure systemic resilience, modularity, and operational maintainability:

1. **Domain-Driven Design (DDD) & Ubiquitous Language:** Align software boundaries directly with business capabilities. Formulate explicit Bounded Contexts with strictly enforced ubiquitous language shared across business stakeholders and engineering.
2. **Loose Coupling & High Cohesion:** Components within a domain boundary must be highly cohesive, collaborating to fulfill a single business lifecycle. Dependencies between distinct domains must be loosely coupled via published contracts (APIs and asynchronous events).
3. **Single Source of Truth & Authoritative Data Ownership:** Every business entity and transactional ledger must have exactly one authoritative owning domain. Direct cross-database joins or unauthorized writes across domain boundaries are strictly prohibited.
4. **Failure Isolation & Blast Radius Containment:** Architectural designs must guarantee that peripheral or non-critical failures (e.g., third-party SMS gateway downtime, recommendation engine degradation, or logistics webhook delays) cannot compromise the core checkout and payment funnel.
5. **Defense in Depth & Zero Trust Security:** Security controls must be implemented across all layers: perimeter API gateway validation, cryptographic inter-service authentication (mTLS), strict least-privilege Role-Based Access Control (RBAC), and ubiquitous payload sanitization.
6. **Evolutionary Architecture & Deferral of Irreversible Commitments:** Favor flexible, modular designs (e.g., well-modularized services or coarse-grained clusters) that allow progressive decoupling into fine-grained microservices as operational maturity, traffic scale, and organizational topology dictate, avoiding premature distributed complexity.

---

## 5. Candidate Business-Domain Decomposition

Based on the 41 business features in [`03-business-feature-inventory.md`](../03-business-feature-inventory.md) and 56 functional requirements in [`04-functional-requirements.md`](../04-functional-requirements.md), the Chellaa platform naturally decomposes into **candidate business domains**.

> [!IMPORTANT]
> **Candidate Business Domains are NOT an Approved Microservice List.**  
> A candidate domain represents a logical bounded context. In Phase 2, multiple candidate domains may be co-located within a single deployable service (such as a modular monolith or coarse-grained service cluster) depending on transactional coupling, operational complexity, and team size.

```mermaid
graph TD
    subgraph Identity & Governance
        D1["01. Identity & RBAC (IAM)"]
        D2["02. Customer Accounts & Profile"]
        D3["03. Audit & Compliance Logging"]
    end

    subgraph Product & Merchandising
        D4["04. Product Catalog & Variants"]
        D5["05. Category & Technical Specs"]
        D6["06. Brand Governance"]
        D7["07. Pricing & Governance"]
        D8["08. Search & Discovery"]
    end

    subgraph Commerce & Fulfillment Funnel
        D9["09. Cart & Saved Items"]
        D10["10. Checkout Orchestration"]
        D11["11. Inventory & Warehouse Operations"]
        D12["12. Promotions & Coupons"]
        D13["13. Orders & Order Lifecycle"]
        D14["14. Payments & Reconciliation"]
        D15["15. Tax & Invoicing"]
        D16["16. Shipping & Delivery"]
        D17["17. Returns, Replacements & Refunds"]
    end

    subgraph Engagement, Support & Operations
        D18["18. Notifications Relay"]
        D19["19. Customer Support"]
        D20["20. Reviews & Ratings"]
        D21["21. Reporting & Analytics"]
        D22["22. Bulk Data Exchange"]
        D23["23. External Integrations"]
        D24["24. Platform Operations"]
    end

    D10 -->|Holds Stock| D11
    D10 -->|Calculates Tax| D15
    D10 -->|Validates Discounts| D12
    D10 -->|Initiates Payment| D14
    D14 -->|Confirms| D13
    D13 -->|Triggers Fulfillment| D16
    D13 -->|Issues Invoice| D15
    D17 -->|Refunds Payment| D14
    D17 -->|Restocks Inventory| D11
```

### Detailed Candidate Domain Evaluation Matrix

| Domain ID & Name | Business Responsibility | Relevant Requirement IDs | Dependencies & Open Decisions | Possible Boundaries to Investigate |
| :--- | :--- | :--- | :--- | :--- |
| **01. Identity & Access Management (IAM)** | Administrative staff authentication, MFA enforcement, role-based access control (RBAC), and session lifecycle governance. | `FEAT-ADM-001`<br>`FEAT-ADM-001`<br>`FR-SEC-001`, `FR-SEC-002`<br>`BR-ADM-001`, `BR-ADM-002` | `DEP-006` (Key vault).<br>Evaluate whether administrative IAM shares the identity provider with retail customers or remains an isolated internal service. | Investigate separating internal staff IAM from customer authentication to isolate administrative credential blast radius. |
| **02. Customer Accounts** | Retail customer profile registration, mobile phone OTP verification, multi-address management, and account deactivation. | `FEAT-CUST-001`, `002`<br>`FR-CUST-001`, `FR-CUST-002`<br>`BR-CUST-001`, `BR-CUST-002` | `OQ-010` (Mobile OTP policy).<br>`DEP-003` (SMS Gateway). | Investigate coupling customer profile storage with customer auth tokens vs maintaining a dedicated customer identity boundary. |
| **03. Audit & Compliance** | Immutable, append-only Write-Once, Read-Many (WORM) logging of sensitive administrative mutations, hash-chain tamper detection. | `FEAT-ADM-002`<br>`FEAT-AUD-001`<br>`FR-AUD-001`, `FR-AUD-002`<br>`BR-AUD-001` | `OQ-020` (8-year retention policy). | Evaluate asynchronous message-driven audit capture vs synchronous transactional interception in shared libraries. |
| **04. Product Catalog & Variants** | Multi-variant electronics modeling (color, RAM, storage), SKU definitions, high-resolution media management, and product lifecycle states. | `FEAT-CAT-001`, `002`<br>`FR-CAT-001`, `FR-CAT-003`<br>`BR-CAT-001`–`003` | `DEP-005` (Object Storage CDN). | Evaluate whether dynamic digital asset transcoding requires a dedicated background media worker or external CDN processing. |
| **05. Category & Technical Specs** | Hierarchical category taxonomy trees, category-specific dynamic specification schemas (e.g., processor, battery life). | `FEAT-CTG-001`, `002`<br>`FR-CAT-002`<br>`FR-CTG-001`, `FR-CTG-002`<br>`BR-CTG-001`, `BR-CTG-002` | None. | Assess if category and dynamic specification schemas should reside in the same database schema as the Product Catalog. |
| **06. Brand Governance** | Authorized manufacturer brand profiles, logo assets, active product association guards to prevent orphan catalog items. | `FEAT-BRD-001`<br>`FR-BRD-001`<br>`BR-BRD-001` | `DEP-005`. | Investigate merging Brand Management into Catalog domain versus maintaining an independent master data boundary. |
| **07. Pricing & Governance** | MRP compare-at ceilings, selling prices, minimum margin alerts, currency governance, and cart price change notifications. | `FEAT-PRC-001`<br>`FR-PRC-001`<br>`BR-PRC-001`, `BR-PRC-002`<br>`NFR-LOC-001` | `OQ-002` (Currency scope),<br>`OQ-009` (Tax-inclusive pricing). | Determine if pricing is co-located with Catalog SKUs or maintained in an independent temporal pricing engine with audit history. |
| **08. Search & Discovery** | Full-text typo-tolerant search, multi-faceted filtering, catalog search index synchronization, and browsing sorting rules. | `FEAT-SRCH-001`<br>`FR-SRCH-001`, `FR-SRCH-002`<br>`BR-SRCH-001`, `BR-SRCH-002`<br>`NFR-PERF-001` | Search engine technology selection (Elasticsearch vs Meilisearch vs Postgres FTS). | Design CQRS read-model architecture populated asynchronously via domain change events (`product.updated`, `inventory.changed`). |
| **09. Cart & Saved Items** | Persistent registered customer carts, anonymous guest cart merging, SKU purchase caps, saved wishlist items. | `FEAT-CART-001`<br>`FEAT-WSH-001`<br>`FR-CART-001`, `FR-CART-002`<br>`FR-WSH-001`<br>`BR-CART-001`, `002` | `OQ-011` (Guest checkout support). | Evaluate storing cart state in an in-memory Redis cluster with TTL vs relational database persistence. |
| **10. Checkout Orchestration** | Idempotent checkout initiation, server-side recalculation, coordination of inventory holds, coupon locking, and payment intent. | `FEAT-CHK-001`, `002`<br>`FR-CHK-001`, `FR-CHK-002`<br>`BR-CHK-001`, `BR-CHK-002` | `OQ-006` (Cash on Delivery limits). | Evaluate implementing checkout as a stateless Saga orchestrator coordinating Inventory, Promotions, and Payment domains. |
| **11. Inventory & Warehouse Operations** | Centralized stock ledgers (Available-to-Sell, Reserved, Damaged), atomic checkout holds, low-stock threshold alerts, warehouse reconciliation. | `FEAT-INV-001`–`003`<br>`FR-INV-001`–`004`<br>`BR-INV-001`–`004` | `OQ-007` (Warehouse topology),<br>`OQ-016` (Peak throughput). | Analyze concurrency safety mechanisms (Redis distributed locks, Lua scripts vs Postgres `FOR UPDATE SKIP LOCKED`). |
| **12. Pricing & Promotions** | Coupon code validation, usage limits, non-stacking rules, pro-rata line-item discount distribution across order items. | `FEAT-PRM-001`<br>`FR-PRM-001`, `FR-PRM-002`<br>`BR-PRM-001`, `BR-PRM-002` | None. | Evaluate whether promotion evaluation can be executed statelessly by Checkout or requires an independent coupon ledger service. |
| **13. Orders & Order Lifecycle** | Order state machine, warehouse picking/packing, serial/IMEI scanning, customer cancellation windows, shipment consolidation. | `FEAT-ORD-001`<br>`FR-ORD-001`–`003`<br>`BR-ORD-001`–`004`<br>`NFR-SCL-001` | None. | Analyze separation between customer order lifecycle visibility and warehouse physical picking/packing fulfillment operations. |
| **14. Payments & Reconciliation** | Payment gateway intents, cryptographic webhook verification, late payment resolution, daily gateway settlement reconciliation. | `FEAT-PAY-001`, `002`<br>`FR-PAY-001`–`003`<br>`BR-PAY-001`–`003`<br>`NFR-SEC-001` | `DEP-001`<br>`OQ-003` (Payment methods),<br>`OQ-004` (Gateway shortlist). | Design gateway abstraction layer (Adapter Pattern) to allow seamless automated failover between primary and secondary providers. |
| **15. Tax & Invoicing** | Statutory GST calculation (CGST, SGST, IGST), sequential tax invoice PDF generation, credit note generation for returns. | `FEAT-FIN-001`, `002`<br>`FR-FIN-001`–`003`<br>`BR-FIN-001`–`003` | `DEP-004`<br>`OQ-001` (Domestic vs International),<br>`OQ-009` (Tax display). | Assess decoupling tax calculation (synchronous checkout dependency) from invoice PDF rendering (asynchronous background job). |
| **16. Shipping & Delivery** | Third-party courier booking, AWB allocation, shipping label generation, live tracking webhook ingestion, delivery state updates. | `FEAT-SHP-001`<br>`FR-SHP-001`, `FR-SHP-002`<br>`BR-SHP-001`, `BR-SHP-002` | `DEP-002`<br>`OQ-005` (Courier partners). | Evaluate third-party courier webhook rate limits and asynchronous tracking poller fallback models. |
| **17. Returns, Replacements & Refunds** | 7-day return request validation, reverse logistics pickup, warehouse technician inspection, replacement orders, automated source refund. | `FEAT-RET-001`, `002`<br>`FR-RET-001`–`003`<br>`BR-RET-001`–`003` | `DEP-001`, `DEP-002`<br>`OQ-008` (Return inspection). | Evaluate coupling between warehouse return inspection approval and payment refund execution Saga. |
| **18. Notifications** | Event-driven multi-channel transactional messaging across SMS, Email, and WhatsApp with automatic template fallbacks. | `FEAT-NTF-001`<br>`FR-NTF-001`<br>`BR-NTF-001`<br>`NFR-AVL-002` | `DEP-003`<br>`OQ-015` (Channel strategy). | Design completely asynchronous event consumer workers isolated from customer request pipelines. |
| **19. Customer Support** | Order-linked ticketing, customer inquiry messaging, support agent case history, 2-hour SLA tracking. | `FEAT-SUP-001`<br>`FR-SUP-001`<br>`BR-SUP-001` | None. | Evaluate building custom support ticketing vs integrating third-party SaaS customer support platforms via webhooks. |
| **20. Reviews & Ratings** | Verified-purchaser rating submission (1-5 stars), text reviews, administrative moderation queue, rating aggregation. | `FEAT-REV-001`<br>`FR-REV-001`<br>`BR-REV-001` | `OQ-012` (Customer reviews scope). | Evaluate read-model caching of approved review scores and review counts directly against catalog SKUs. |
| **21. Reporting & Analytics** | Executive sales reporting (GMV, AOV, Net Revenue), GST tax reconciliation ledgers, stock turnover velocity, audit dashboards. | `FEAT-RPT-001`<br>`FR-RPT-001`<br>`BR-RPT-001`<br>`NFR-PERF-003` | `OQ-018` (Reporting scope). | Design read-replica query strategies or dedicated analytical datamart to eliminate operational DB table locking. |
| **22. Bulk Data Exchange** | Asynchronous CSV/XLSX catalog, pricing, and inventory batch updates with atomic pre-validation and annotated error reports. | `FEAT-DAT-001`<br>`FR-DAT-001`<br>`BR-CAT-001`, `BR-PRC-001`<br>`NFR-PERF-003` | `OQ-019` (Greenfield catalog import). | Design asynchronous job worker with progress tracking and annotated error report generation. |
| **23. External Integrations** | Outbound third-party API adapters, circuit-breaker isolation, webhook ingress authentication, signature verification, and dead-lettering. | `FEAT-INT-001`<br>`FR-INT-001`<br>`BR-INT-001`, `BR-INT-001`<br>`NFR-AVL-002` | `DEP-001`, `DEP-002`, `DEP-003` | Investigate whether integration adapters live inside their respective business domains or within a shared integration gateway cluster. |
| **24. Platform Operations** | Distributed health checking (liveness/readiness), graceful degradation under downstream outages, circuit-breaker dashboards, and rate limits. | `FEAT-OPS-001`<br>`FR-OPS-001`, `FR-OPS-001`<br>`BR-OPS-001`, `BR-OPS-001`<br>`NFR-OBS-001`, `NFR-OBS-002` | Service mesh / API Gateway infrastructure. | Assess implementing operational controls via API gateway filters and middleware vs dedicated operational services. |

---

## 6. Questions to Resolve Before Defining Microservice Boundaries

Before carving out physical microservice repositories, deployment units, and network boundaries, the engineering team must resolve the following architectural design questions during Phase 2:

1. **Service Granularity & Team Topology:** Does the initial development team size justify managing 15+ independent microservice deployment pipelines, or would a **Modular Monolith** or **4–6 Coarse-Grained Service Clusters** (e.g., *Catalog & Merchandising*, *Commerce & Cart*, *Fulfillment & Logistics*, *Finance & Invoicing*, *Operations & Alerts*) deliver significantly higher development velocity with lower operational friction?
2. **Checkout & Reservation Transaction Boundary:** Should Checkout and Inventory be separate microservices communicating over network boundaries (requiring a distributed Saga / 2-phase reservation), or should atomic inventory reservation and order creation share a local transactional boundary to guarantee 100% overselling prevention during 1,000 OPM flash sales?
3. **Internal vs External Identity Boundaries:** Should internal administrative staff (RBAC with granular permissions) and retail customers (phone OTP authentication) share a unified identity service or remain completely separate systems to prevent staff credential compromise from impacting the customer-facing storefront?
4. **Data Sovereignty vs Reporting Performance:** If financial invoices, order records, and catalog data reside in isolated databases, what architecture will project executive sales and tax reports without incurring complex distributed queries across network boundaries?
5. **Operational Complexity vs Value:** Does every candidate domain warrant its own independent container, CI/CD pipeline, and database, or should domains with identical scaling characteristics and shared transactional lifecycles be co-located within cohesive services?

---

## 7. Data Ownership and Consistency Analysis Plan

### 7.1 Authoritative Data Ownership Mapping
To prevent distributed data corruption, every core domain entity must have exactly one authoritative owning service. Direct writes from foreign domains are strictly forbidden:

| Core Entity / Aggregate | Authoritative Owning Domain | Permitted Access Pattern for Foreign Domains |
| :--- | :--- | :--- |
| **Product, Variant, SKU** | Product Catalog Domain | Read via API / Search Read Model; mutated only by Catalog Admin API. |
| **Stock Ledger & Reservations** | Inventory Domain | Mutated only via Inventory atomic reservation API; read via Inventory API. |
| **Cart & Cart Items** | Cart Domain | Mutated by customer session; read during checkout initialization. |
| **Order & Order Line Items** | Orders Domain | Created by Checkout Saga; updated by Fulfillment & Returns events. |
| **Payment Intent & Transaction** | Payments Domain | Mutated only by Payment Gateway webhook handler; read by Order Saga. |
| **Tax Invoice & Credit Note** | Tax & Invoicing Domain | Generated sequentially; immutable once issued; read by Customer/Admin. |
| **Consignment & Tracking** | Shipping & Logistics Domain | Updated via Courier webhooks; read by Order Tracking API. |
| **Return Request & Inspection** | Returns & Refunds Domain | Mutated by technician inspection; triggers Refund & Restock events. |

### 7.2 Distributed Consistency Patterns
Because business transactions span multiple candidate domains, the following distributed consistency patterns will be analyzed during Phase 2:

* **Transactional Outbox Pattern:** To ensure atomic local database updates and domain event publishing without two-phase commit (2PC) overhead, services will write events to a local `outbox` table within the same ACID transaction. A dedicated relay worker will tail the outbox table and publish events to the message broker.
* **Saga Pattern (Orchestration vs Choreography):**
  * *Orchestrated Saga:* Proposed for Checkout and Order Placement. A central Checkout Orchestrator coordinates Inventory hold, Coupon lock, Payment authorization, and Order generation, executing compensating transactions (e.g., release inventory hold) upon failure.
  * *Choreographed Saga:* Proposed for post-order fulfillment (e.g., `order.paid` $\rightarrow$ triggers Logistics booking and Invoice generation independently).
* **Idempotency Standards:** Mandate client-supplied `Idempotency-Key` headers on all state-mutating HTTP POST/PUT endpoints (Checkout submission, Payment capture, Refund dispatch). The key will be tracked in a fast distributed store (e.g., Redis) with a 24-hour TTL.
* **CQRS Read Models:** Separate read models for high-velocity browsing (Catalog Search, Customer Order History) updated asynchronously via domain events to eliminate operational database contention.

---

## 8. API Contract Design Plan

### 8.1 API Protocol & Style Conventions
* **External Client-to-Backend:** RESTful HTTP/2 APIs using JSON payloads for all storefront and administrative interactions.
* **Inter-Service Communication:** Evaluate high-performance gRPC over HTTP/2 with Protocol Buffers for synchronous inter-cluster communication, falling back to internal REST APIs where simplicity is preferred.

### 8.2 Standardized Error Responses (RFC 7807)
All HTTP API endpoints will strictly adhere to **RFC 7807 (Problem Details for HTTP APIs)**, ensuring consistent error parsing across all services:
```json
{
  "type": "https://api.chellaa.com/errors/inventory-insufficient",
  "title": "Insufficient Stock",
  "status": 409,
  "detail": "SKU-ELC-001 has only 2 units available, 5 requested.",
  "instance": "/orders/checkout",
  "code": "INSUFFICIENT_STOCK",
  "timestamp": "2026-10-18T16:00:00Z"
}
```

### 8.3 API Governance & Standards
* **URI Versioning:** Explicit URI path versioning (e.g., `/api/v1/catalog/products`).
* **Pagination Standards:** Cursor-based pagination (`?cursor=...&limit=20`) for continuous feeds and high-velocity ledgers; limit/offset pagination (`?page=1&limit=50`) for administrative data tables.
* **Filtering & Sorting:** Standardized query parameters (e.g., `?sort=price_desc&category=smartphones`).
* **Contract Verification:** OpenAPI 3.0 specifications managed in source control; consumer-driven contract testing (e.g., Pact) evaluated to prevent breaking changes.

---

## 9. Event-Driven Communication Analysis Plan

### 9.1 Event Standard & Envelope
All asynchronous domain events published across services will adhere to the **CloudEvents v1.0** specification:
```json
{
  "specversion": "1.0",
  "type": "com.chellaa.commerce.order.placed",
  "source": "/services/order-service",
  "id": "evt-uuid-12345",
  "time": "2026-10-18T16:00:00Z",
  "datacontenttype": "application/json",
  "traceparent": "00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01",
  "data": {
    "order_id": "ORD-20261018-0001",
    "customer_id": "CUST-9876",
    "total_amount": 49999.00,
    "currency": "INR"
  }
}
```

### 9.2 Event Taxonomy
* **Domain Change Events:** Indicate state changes within an aggregate (e.g., `order.placed`, `inventory.reserved`, `price.updated`).
* **Integration Events:** Cross-boundary milestone triggers (e.g., `payment.webhook.received`, `courier.status.updated`).
* **Notification Requests:** Lightweight commands to external relays (e.g., `notification.sms.requested`).

### 9.3 Delivery Guarantees & Consumer Resilience
* **At-Least-Once Delivery:** All event consumers must be designed as **strictly idempotent** to tolerate duplicate message deliveries.
* **Partitioning Keys:** Messages must be partitioned by Aggregate Root ID (e.g., `order_id` or `sku_code`) to guarantee strict in-order processing per entity.
* **Dead-Letter Queues (DLQ):** Unprocessable or corrupt messages will be routed to a dead-letter queue after 3 failed retries, triggering alerts and administrative inspection tooling.

---

## 10. Security, Authentication and Authorization Analysis Plan

### 10.1 Customer & Staff Authentication
* **Customer Authentication:** Mobile phone + SMS OTP verification (`OQ-010`). Upon validation, issue short-lived cryptographically signed JSON Web Tokens (JWT - RS256, 15-minute expiry) with rotating refresh tokens stored securely.
* **Administrative Staff Authentication:** Multi-factor authentication (MFA) mandated for all administrative roles (`FEAT-ADM-001`). Public-key cryptography (JWKS) utilized by API Gateway to verify tokens statelessly.

### 10.2 Role-Based Access Control (RBAC)
* **Granular Permission Model:** Map explicit permissions to predefined roles (`SUPER_ADMIN`, `CATALOG_MANAGER`, `WAREHOUSE_OPERATOR`, `FINANCE_EXECUTIVE`, `SUPPORT_AGENT`) per `BR-ADM-001` and `BR-ADM-002`.
* **Zero Trust Gateway Enforcement:** API Gateway validates JWT signatures and injects sanitized identity headers (`X-User-Id`, `X-User-Roles`, `X-Permissions`) into internal network calls.

### 10.3 Sensitive Data Protection & Compliance
* **Zero Cardholder Storage:** No cardholder PAN, CVV, or banking credentials stored on Chellaa servers (`BR-SEC-001`, `NFR-SEC-001`). Payment entry handled via PCI DSS Level 1 certified gateway client SDKs / hosted fields.
* **Password Hashing:** Passwords hashed using industry-standard bcrypt (work factor $ge 12$) or Argon2id with unique cryptographic salts (`FR-SEC-001`, `NFR-SEC-002`).
* **PII Encryption & Log Redaction:** Customer personally identifiable information (PII) encrypted at rest using AES-256 (`NFR-SEC-003`). Interceptors in shared logging libraries automatically mask phone numbers, emails, and addresses from application logs.

---

## 11. Reliability, Idempotency, Retries and Failure-Handling Plan

### 11.1 Idempotency Framework
* State-mutating APIs (Checkout, Payment, Stock Adjustments, Return Approvals) enforce a unique `Idempotency-Key` header.
* Distributed lock acquired in Redis; if a duplicate request arrives while processing, return HTTP 409 (Conflict); if completed, replay the cached response.

### 11.2 Circuit Breakers & Bulkhead Isolation
* External third-party calls (Payment Gateways, Couriers, SMS Gateways) must be wrapped with circuit breakers (e.g., Resilience4j / Envoy) per `BR-INT-001` and `NFR-AVL-002`.
* Trip threshold: $> 50%$ failure rate over 20 requests trips the breaker to `OPEN`, fast-failing subsequent calls without consuming server threads.
* Separate thread pool bulkheads for critical customer paths vs background synchronization.

### 11.3 Retries & Exponential Backoff
* Retries permitted only on idempotent operations and transient network errors (e.g., HTTP 502, 503, 504, connection timeouts).
* Formula: $\text{WaitTime} = \min(\text{MaxWait}, \text{InitialWait} \times 2^{\text{attempt}}) + \text{Jitter}$, preventing retry storms and thundering herd effects.

### 11.4 Graceful Degradation
* If Search or Pricing clusters experience transient latency, the API Gateway degrades gracefully by serving cached catalog snapshots (`FR-OPS-001`, `BR-OPS-001`).
* Core checkout remains functional even if notification delivery or product review aggregation is temporarily unavailable.

---

## 12. Performance, Capacity and Disaster-Recovery Validation Plan

### 12.1 Sizing & Capacity Targets
* **Baseline Throughput:** 100 orders/minute sustained.
* **Peak Surge Capacity:** 1,000 orders/minute sustained during promotional events without error rate exceeding 0.1% (`NFR-SCL-001`, `OQ-016`).
* **Catalog Capacity:** Sized to store, index, and query 100,000 SKUs with dynamic specifications (`NFR-SCL-002`).

### 12.2 Storefront Latency Budgets
* Search & Faceted Filtering: $p95 \le 200\text{ms}$ under 5,000 concurrent browsing sessions (`NFR-PERF-001`).
* Checkout Submission: $p95 \le 1,000\text{ms}$ under peak load (`NFR-PERF-002`).
* Heavy Batch Imports: Process 5,000 SKU bulk updates within 15 minutes asynchronously without degrading storefront latency (`NFR-PERF-003`).

### 12.3 Disaster Recovery & Business Continuity
* **Backup Strategy:** Automated daily full backups, continuous Write-Ahead Log (WAL) archiving, and Point-In-Time Recovery (PITR) for relational databases.
* **RPO & RTO Targets:** Recovery Point Objective $\le 5\text{ minutes}$; Recovery Time Objective $\le 2\text{ hours}$ (`NFR-BCM-001`, `OQ-017`).
* **Validation Methodology:** Automated DR failover drills and synthetic load testing (Gatling / k6) scheduled during pre-launch staging.

---

## 13. Observability, Logging, Audit and Monitoring Plan

### 13.1 Distributed Tracing
* **W3C TraceContext:** Mandate standard `traceparent` and `tracestate` header propagation across all HTTP requests and messaging broker envelopes (`NFR-OBS-001`, `BR-OPS-001`).
* **Trace Propagation:** 100% of customer requests assigned an immutable Trace ID at the API Gateway, propagated through inter-service calls, database operations, and asynchronous event consumers.

### 13.2 Structured Logging & Privacy
* **Standard JSON Format:** All services emit JSON logs containing: `timestamp`, `level`, `service_name`, `trace_id`, `span_id`, `user_id` (if authenticated), and `message`.
* **Automated Log Scrubbing:** Regular expressions in log appenders automatically redact PAN, CVV, passwords, phone numbers, and email addresses.

### 13.3 Metrics, Dashboards & Alerting
* **RED Metrics:** Scrape Prometheus-compatible `/metrics` endpoints for Rate (requests/sec), Errors (failed requests/sec), and Duration (latencies $p50, p95, p99$) per service (`NFR-OBS-002`).
* **Health Probes:** Separate `/health/live` (liveness) and `/health/ready` (readiness) HTTP endpoints for container orchestrator management.
* **Alerting Thresholds:** Automated alerts (PagerDuty / Slack) triggered when error rate exceeds $2\%$ for 3 consecutive minutes or when circuit breakers trip to `OPEN`.

### 13.4 Privileged Mutation Audit Trail
* **WORM Audit Ledger:** Dedicated append-only table or immutable logging stream recording operator ID, client IP, timestamp, aggregate ID, action, before/after JSON delta, and justification reason (`FR-AUD-001`, `BR-AUD-001`).
* **Cryptographic Tamper Detection:** Hash-chaining between consecutive audit records to detect unauthorized ledger modifications (`FR-AUD-002`).

---

## 14. Deployment, CI/CD, Configuration and Secrets-Management Planning

### 14.1 Containerization Standards
* **OCI Compliance:** All deployable components packaged as lightweight, Open Container Initiative (OCI) compliant images (Docker).
* **Security Hardening:** Multi-stage container builds using minimal base images (Alpine / Distroless); non-root user execution; automated container vulnerability scanning (Trivy).

### 14.2 Configuration & Secrets Management
* **12-Factor App Methodology:** Externalized runtime configuration injected via environment variables or centralized configuration servers.
* **Secrets Governance:** Dedicated secrets manager (e.g., HashiCorp Vault / AWS Secrets Manager) with dynamic secret generation and automated credential rotation; zero plaintext credentials committed to Git.

### 14.3 CI/CD Quality Gates
* **Automated Pipelines:** Continuous integration pipeline running linter checks, static code analysis (SonarQube), unit tests, and integration test suites on every pull request (`NFR-REL-001`, `OQ-021`).
* **Zero-Downtime Releases:** Rolling deployment strategies or Blue/Green environments with automated rollback triggered upon health check probe failure.

---

## 15. External Integration Dependencies Mapping

The platform requires resilient integration with external service providers:

| Integration Domain | Dependency ID | Business Responsibility | Evaluated Candidate Providers | Failure Mitigation Strategy |
| :--- | :--- | :--- | :--- | :--- |
| **Payment Gateways** | `DEP-001` | Card, UPI, Net Banking, and Wallet payment intent generation and webhook capture. | Razorpay, PayU, Cashfree (`OQ-004`). | Adapter Pattern; automated multi-gateway failover; cryptographic webhook verification (`BR-PAY-002`). |
| **Courier & Logistics** | `DEP-002` | Automated AWB generation, shipping label PDF rendering, live tracking ingestion. | Shiprocket, Delhivery, Blue Dart (`OQ-005`). | Circuit breaker; asynchronous dispatch queue; tracking poll fallback. |
| **Messaging Services** | `DEP-003` | Transactional SMS OTP, dispatch updates, WhatsApp alerts, transactional email. | Twilio, Gupshup, AWS SNS / SES (`OQ-015`). | Asynchronous event relay; fallback to email if SMS delivery fails (`BR-NTF-001`). |
| **Statutory Tax Framework** | `DEP-004` | HSN-based GST tax calculation, CGST/SGST/IGST inter-state vs intra-state determination. | Internal rule table seeded from official GST schedules. | In-memory cached tax schedules; offline calculation capability. |
| **Media Asset Storage** | `DEP-005` | High-resolution product images, dimension charts, shipping labels, invoice PDFs. | Cloud Object Storage (S3-compatible) + Global CDN. | Pre-signed URL direct uploads; immutable versioned object keys. |
| **Identity & Vault Infrastructure** | `DEP-006` | Staff multi-factor authentication, cryptographic key rotation, token signing. | Enterprise Identity Provider / Key Vault. | Local caching of public JWKS keys for stateless JWT verification. |

---

## 16. Architecture Risks, Trade-Offs and Architectural Alternatives Analysis

The following design alternatives and technical risks must be rigorously evaluated in Phase 2 ADRs. All options are currently marked **`PROPOSED`** pending technical evaluation:

### 16.1 Architecture Style: Modular Monolith vs Coarse-Grained Services vs Fine-Grained Microservices

| Attribute | Option A: Modular Monolith | Option B: Coarse-Grained Services (Recommended Evaluation) | Option C: Fine-Grained Microservices |
| :--- | :--- | :--- | :--- |
| **Description** | Single deployable application with strictly enforced internal module boundaries (packages/namespaces) and isolated logical DB schemas. | 4 to 6 independently deployable service clusters grouped by core business lifecycle (Catalog, Commerce, Fulfillment, Finance, Operations). | 15 to 24 independent deployable microservices corresponding 1-to-1 with every candidate business domain. |
| **Transactional Simplicity** | **Very High:** Native ACID transactions across modules. Zero distributed Sagass required for checkout. | **High:** Local ACID transactions within clusters; Sagass limited to cross-cluster milestones (e.g., Payment to Order). | **Low:** Heavy reliance on distributed Sagass, two-phase commits, and compensating transactions for basic flows. |
| **Operational Overhead** | **Lowest:** Single CI/CD pipeline, single monitoring target, simplified local developer setup. | **Moderate:** Manageable Kubernetes deployments, distinct CI/CD pipelines for independent scaling. | **Highest:** Complex service mesh, distributed tracing overhead, network latency, multiple container registries. |
| **Flash Sale Scalability** | **Moderate:** Entire monolith must scale horizontally to handle traffic surges on checkout. | **High:** Checkout and Inventory clusters can scale independently to handle 1,000 OPM without scaling back-office. | **Highest:** Individual endpoints scale independently. |
| **Recommendation** | `PROPOSED for MVP evaluation` | `PROPOSED as Primary Baseline Candidate` | `NOT_RECOMMENDED for Initial Launch` |

### 16.2 Inter-Service Communication: Synchronous REST/gRPC vs Asynchronous Messaging

| Interaction Pattern | Evaluated Use Cases | Candidate Technologies | Evaluation Criteria | Status |
| :--- | :--- | :--- | :--- | :---: |
| **Synchronous Request/Response** | Client-to-Backend queries, cart calculations, address serviceability checks, payment session initialization. | REST over HTTP/2 with JSON, gRPC over HTTP/2 with Protobuf. | Low latency ($< 50\text{ms}$ inter-service), contract type-safety, client simplicity. | `PROPOSED` |
| **Asynchronous Event-Driven** | Post-order fulfillment, payment webhook fanout, inventory restock alerts, audit logging, notifications. | Apache Kafka, RabbitMQ, AWS SQS/SNS. | High throughput, fault isolation, decoupling, backpressure buffering. | `PROPOSED` |

### 16.3 Data Architecture: Database-Per-Service vs Logical Schema Isolation

| Approach | Description | Advantages | Trade-Offs & Risks | Status |
| :--- | :--- | :--- | :--- | :---: |
| **Physical Database-per-Service** | Completely independent database instances for each service cluster. | Maximum blast radius isolation; independent DB scaling and tuning. | High infrastructure cost; complex local developer setup; requires distributed reporting datamart. | `PROPOSED` |
| **Logical Schema Isolation (Shared Instance)** | Single managed database instance (e.g., PostgreSQL) with separate schemas and isolated database users per service. | Low infrastructure overhead; enforced logical boundaries; simplified backup and DR runbooks. | Shared connection pool and CPU/memory; risk of "noisy neighbor" contention during flash sales. | `PROPOSED (Primary for Launch)` |

### 16.4 Messaging Technology & Delivery Guarantees

| Technology Option | Delivery Guarantees | Strengths | Trade-Offs & Complexity | Status |
| :--- | :--- | :--- | :--- | :---: |
| **Apache Kafka** | At-least-once (with idempotent consumers); partition-based strict ordering. | Extreme throughput; distributed commit log; event replay capability. | High operational complexity (Zookeeper/KRaft, broker tuning, partition rebalancing). | `PROPOSED` |
| **RabbitMQ** | At-least-once; flexible routing keys; AMQP standard. | Mature routing exchanges; lightweight; simpler cluster operations. | Lower throughput than Kafka; message replay requires custom dead-letter storage. | `PROPOSED` |
| **Cloud-Native Managed Queues (AWS SQS/SNS)** | At-least-once; FIFO queues support strict ordering. | Zero operational overhead; fully managed auto-scaling; pay-per-use. | Vendor lock-in; FIFO throughput capped at 3,000 msgs/sec without batching. | `PROPOSED` |

### 16.5 Search & Reporting Read Models

| Architecture Pattern | Description | Advantages | Trade-Offs & Risks | Status |
| :--- | :--- | :--- | :--- | :---: |
| **Dedicated Search Engine (Elasticsearch / OpenSearch)** | Asynchronously indexed search cluster populated via change events. | Rich typo-tolerance, faceted search, sub-50ms query times at 100k SKUs. | Secondary data store to keep in sync; eventual consistency lag. | `PROPOSED` |
| **PostgreSQL Read Replicas + Full-Text Search (FTS)** | Read-only replica using Postgres `tsvector` and trigram indexing. | Zero additional infrastructure; immediate consistency; simplified operations. | Higher query latency on complex multi-faceted filters at high scale. | `PROPOSED (MVP Candidate)` |
| **Dedicated Analytical Datamart (ClickHouse / TimescaleDB)** | Analytical columnar database populated via CDC / outbox events for executive reports. | Lightning-fast aggregations on millions of sales rows; zero operational DB locking. | Additional data engineering pipeline to maintain. | `PROPOSED (Phase 3+)` |

### 16.6 Authentication & Authorization Approaches

| Approach | Description | Advantages | Trade-Offs & Risks | Status |
| :--- | :--- | :--- | :--- | :---: |
| **API Gateway Token Verification + Stateless Internal Services** | Gateway verifies JWT via JWKS, checks RBAC, and injects validated user headers downstream. | Services remain simple and decoupled from auth providers; high throughput. | Internal network must be secured to prevent header spoofing. | `PROPOSED (Recommended)` |
| **Decentralized Token Verification (Every Service Verifies JWT)** | Every service validates JWT signature using cached public JWKS keys. | Zero reliance on gateway for internal security; end-to-end cryptographic trust. | Repeated cryptographic verification CPU cost; library version management across services. | `PROPOSED` |

### 16.7 Deployment & Observability Options

| Option Category | Candidate Alternatives | Trade-Off Analysis | Status |
| :--- | :--- | :--- | :---: |
| **Container Orchestration** | Managed Kubernetes (EKS/GKE) vs Managed Container PaaS (AWS ECS / Cloud Run). | Kubernetes offers maximum portability but high management overhead; ECS/Cloud Run provides rapid setup with minimal maintenance for a small team. | `PROPOSED` |
| **Observability Toolchain** | OpenTelemetry + Prometheus/Grafana vs SaaS (Datadog / New Relic). | OpenTelemetry prevents vendor lock-in and open-source stacks reduce cost; SaaS eliminates self-hosting overhead but incurs unpredictable log ingestion fees. | `PROPOSED` |

### 16.8 Architectural Risk Matrix

| Risk ID | Description & Impact | Severity | Mitigation Strategy |
| :--- | :--- | :---: | :--- |
| **RISK-01** | **Premature Fine-Grained Microservices:** Splitting into 15–20 services before team scale causes severe latency, operational drag, and distributed transaction bugs. | **HIGH** | Evaluate Coarse-Grained Clusters or Modular Monolith in Phase 2 architecture design. |
| **RISK-02** | **Third-Party Payment / Courier Outages:** Payment gateway or courier API downtime blocks checkout or fulfillment. | **HIGH** | Implement Adapter Pattern, multi-gateway automated failover, and Resilience4j circuit breakers. |
| **RISK-03** | **Inventory Concurrency Contention:** High-concurrency flash sales (1,000 OPM) cause race conditions or overselling. | **CRITICAL** | Redis distributed holds or atomic Postgres `FOR UPDATE SKIP LOCKED` with strict timeout budgets. |
| **RISK-04** | **Unresolved Commercial Policies:** Unresolved open questions (`OQ-001` through `OQ-022`) force architecture rework. | **MEDIUM** | Explicitly map open decisions to architecture modules; design configurable strategy adapters. |

---

## 17. Requirements Traceability and Architecture Verification Strategy

### 17.1 Traceability Architecture
To maintain absolute integrity from requirements through technical specifications, formal Phase 2 architecture deliverables will enforce a bi-directional traceability strategy:
* Every functional requirement (`FR-001` to `FR-056`), business rule (`BR-001` to `BR-055`), and non-functional requirement (`NFR-001` to `NFR-018`) will map to an implementing architectural service, data entity, API contract, or infrastructure control.
* Deliverable `ARCH-12` will establish the formal **Master Architecture Traceability Matrix**.

### 17.2 Architecture Verification Strategy & Fitness Functions
During architecture design and future implementation, automated architectural fitness functions will be evaluated to verify that engineering rules are not violated:
* **Dependency Direction Enforcement:** Use tools (e.g., ArchUnit / dependency-cruiser) to ensure domain services depend only on shared contracts and never violate bounded context boundaries.
* **Circular Dependency Ban:** CI/CD static checks to detect and fail builds if circular dependencies arise between modules.
* **RFC 7807 Compliance Gate:** Automated API schema validation ensuring all error responses adhere to standard problem details.
* **Traceparent Header Audit:** Automated integration test assertions confirming 100% of inter-service calls propagate W3C TraceContext headers.

---

## 18. Phase 2 Architecture Deliverables & Acceptance Criteria

When formal Phase 2 Technical Architecture is authorized, the following 12 deliverables will be produced under `docs/01-architecture/`:

| ID | Title & Purpose | Required Inputs | Expected Outputs | Related BRD IDs | Dependencies & Open Decisions | Acceptance Criteria | Reviewer / Decision Owner | Approval Required |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :---: |
| **`ARCH-01`** | **Architecture Overview & System Context**<br>Define overall system boundaries, external utilities, and single-retailer constraints. | BRD Docs 00, 01, 02; approved charter. | C4 Level 1 (System Context) diagram; system responsibility boundary statement. | `OBJ-001`–`010`<br>`ACT-001`–`014` | Scope baseline;<br>Single-retailer invariant. | 100% single-retailer; zero marketplace concepts; all 14 actors mapped. | Principal Software Architect | **YES** |
| **`ARCH-02`** | **Domain Decomposition & Bounded Contexts**<br>Establish formal DDD bounded contexts, ubiquitous language dictionary, and domain models. | BRD Docs 03, 04, 10; Candidate Domains. | C4 Level 2 (Container) diagram; Bounded Context Map; Ubiquitous Language dictionary. | `FEAT-001`–`041`<br>All 56 FRs | Candidate Domains analysis;<br>Granularity choice. | Clean separation of concerns; zero circular domain dependencies; explicit context relationships. | DDD Specialist | **YES** |
| **`ARCH-03`** | **Service Decomposition & Responsibility Matrix**<br>Define physical deployable units (clusters or services) and component ownership. | `ARCH-02`, Candidate Alternatives analysis. | Service catalog; component responsibility matrix; service grouping rationale. | `FEAT-001`–`041`<br>`NFR-SCL-001` | `OQ-016` (Peak throughput);<br>Team topology. | Every feature in Doc 03 mapped to exactly one owning service; rationale for granularity documented. | Principal Software Architect | **YES** |
| **`ARCH-04`** | **Service Interaction & Dependency Diagrams**<br>Document inter-service sync and async interaction chains for primary business flows. | BRD Doc 06 (Workflows `WF-001` to `016`). | Sequence diagrams for Order Placement, Payment Webhook, Return Inspection, and Inventory Release. | `WF-001`–`016`<br>`BR-001`–`055` | `ARCH-03`;<br>Messaging tech selection. | All 16 workflows modeled; error paths, timeouts, and fallbacks explicitly diagrammed. | Distributed Systems Engineer | **YES** |
| **`ARCH-05`** | **Data Ownership & Distributed Consistency**<br>Formalize database ownership, entities per service, and Saga transaction coordination. | BRD Docs 04, 05; entity models. | Data ownership matrix; Saga flowcharts for Checkout and Refund; Outbox pattern specifications. | `FR-INV-001`<br>`FR-CHK-001`<br>`FR-PAY-001` | `ARCH-03`;<br>Database isolation choice. | Zero cross-service direct DB access; Saga compensating actions defined for every failure step. | Data Architect | **YES** |
| **`ARCH-06`** | **API & Event Contracts Architecture**<br>Define inter-service REST/gRPC conventions, OpenAPI specs, and domain event schemas. | BRD Docs 04, 06; domain events. | API design guide; event naming conventions; CloudEvents JSON schemas for core events. | All 56 FRs<br>`BR-INT-001` | `ARCH-03`;<br>Schema registry choice. | Adherence to RFC 7807 error format; backward compatibility strategy defined; idempotency mandated. | Integration Lead | **YES** |
| **`ARCH-07`** | **Security & Access Architecture**<br>Define identity tokens, RBAC enforcement, credential protection, and PII masking. | BRD Docs 01, 04, 07; `NFR-SEC-001`–`003`. | Security architecture document; JWT claims schema; RBAC permission matrix; PII redaction rules. | `FEAT-ADM-001`<br>`FR-SEC-001`<br>`BR-SEC-001` | `DEP-006` (Key vault);<br>`OQ-010` (Mobile OTP). | Zero PAN storage; bcrypt/Argon2id hashing; least-privilege RBAC mapped to internal roles. | Security Architect | **YES** |
| **`ARCH-08`** | **Resilience & Fault Isolation Strategy**<br>Define circuit breakers, retries, rate limits, and third-party outage isolation. | BRD Docs 04, 07; `NFR-AVL-002`, `FEAT-INT-001`. | Fault-tolerance specification; rate limiting policy; bulkhead and circuit breaker configuration parameters. | `BR-INT-001`<br>`NFR-AVL-002`<br>`FR-OPS-001` | `DEP-001`–`003` (Gateways);<br>Resilience framework. | Zero core checkout blockage on peripheral service outage; exponential backoff + jitter modeled. | Distributed Systems Engineer | **YES** |
| **`ARCH-09`** | **Observability & Operational Architecture**<br>Formalize distributed tracing, structured logging standards, metrics scraping, and alert rules. | BRD Docs 04, 07; `NFR-OBS-001`, `002`. | Observability architecture; W3C TraceContext guide; Prometheus metric catalog; WORM audit design. | `FR-AUD-001`<br>`BR-AUD-001`<br>`BR-OPS-001` | Toolchain selection;<br>`OQ-020` (Audit retention). | 100% transactions carry Trace ID; audit logs immutable; alerts defined for $>2%$ error rate. | SRE / DevOps Lead | **YES** |
| **`ARCH-10`** | **Deployment Topology & Infrastructure**<br>Document containerization, cloud deployment models, and backup/DR configurations. | BRD Doc 07; `NFR-SCL-001`, `NFR-BCM-001`. | Cloud infrastructure topology diagram; Kubernetes/ECS deployment strategy; backup runbook. | `NFR-SCL-001`<br>`NFR-BCM-001` | Cloud provider selection;<br>`OQ-016`, `OQ-017`. | Sized for 1,000 OPM peak; RPO $le 5$m; RTO $le 2$h supported by architecture. | Cloud Architect | **YES** |
| **`ARCH-11`** | **Architecture Decision Records (ADRs)**<br>Document formal technical choices, trade-offs, evaluated alternatives, and consequences. | All architecture trade-off analyses. | Versioned ADR files (`ADR-001` onwards) following Michael Nygard format. | All BRD requirements | Comparative alternatives analysis. | Every ADR includes Context, Drivers, Options Considered, Decision, and Consequences. | Principal Software Architect | **YES** |
| **`ARCH-12`** | **Requirements-to-Architecture Traceability**<br>Map every approved BRD requirement directly to its implementing architectural component. | BRD Doc 09; all Phase 2 deliverables. | Master Architecture Traceability Matrix (Excel / Markdown). | All 56 FRs, 55 BRs, 18 NFRs | Completion of `ARCH-01` through `ARCH-11`. | 100% of 56 FRs, 55 BRs, and 18 NFRs mapped to services, APIs, or infrastructure controls. | Traceability Lead | **YES** |

---

## 19. Architecture Review and Approval Gate

Moving from this **Non-Binding Planning Stage** to **Formal Phase 2 Technical Architecture Design** requires completion of the following formal governance checkpoints:

```markdown
### Phase 2 Entry Gate Checklist:
- [ ] 1. Formal Business Owner sign-off recorded in docs/00-requirements/11-review-and-approval.md Section 7.
- [ ] 2. Requirements baseline status transitioned from PENDING_APPROVAL to APPROVED.
- [ ] 3. Explicit decisions recorded for the 8 critical commercial questions:
         - OQ-001 (Domestic India vs Cross-border scope)
         - OQ-002 (INR Currency scope)
         - OQ-006 (Restricted COD threshold)
         - OQ-008 (7-day returns & physical inspection)
         - OQ-009 (Tax-inclusive MRP calculation)
         - OQ-010 (Mobile phone + SMS OTP identity primary key)
         - OQ-016 (1,000 OPM peak throughput sizing)
         - OQ-017 (99.9% uptime, RTO <= 2h, RPO <= 5m DR targets)
         OR explicit authorization granted to adopt provisional parameters as documented constraints.
- [ ] 4. Formal authorization granted by the project sponsor to establish the docs/01-architecture/ directory.
```

---

## 20. Recommended Implementation-Planning Approach for a Future Approved Architecture

Following formal Phase 2 Architecture approval, the eventual transition to **Phase 3 Implementation** should follow an incremental, vertical-slice methodology:

1. **Phase 3A — Foundation & Developer Experience:**
   * Shared libraries (structured logging, W3C tracing, RFC 7807 error handling, CloudEvents envelopes).
   * Local containerized development environment (Docker Compose with PostgreSQL, Redis, Message Broker).
   * CI/CD build and automated testing pipeline with static architecture quality gates.
2. **Phase 3B — Core Master Data & Identity:**
   * Identity & RBAC Service (internal staff auth, permission enforcement).
   * Customer Profile & Authentication Service.
   * Category Taxonomy, Brand Governance, and Product Catalog Services.
3. **Phase 3C — Inventory & Shopping Funnel:**
   * Real-Time Inventory Ledger & Atomic Reservation Service.
   * Cart & Pricing Services.
   * Checkout Orchestration & Order Placement Service (Saga Coordinator).
4. **Phase 3D — Financial Transactions & Fulfillment:**
   * Payment Gateway Integration Adapter & Webhook Reconciliation Service.
   * Statutory GST Tax & Sequential Invoicing Service.
   * Third-Party Courier Logistics & Consignment Booking Service.
5. **Phase 3E — Post-Order, Customer Care & Operations:**
   * Returns, Replacements & Source Refund Service.
   * Multi-Channel Transactional Notification Relay Service.
   * Customer Support Ticketing & Executive Reporting Services.
