# ARCH-11 — Formal Architecture Decision Records (ADR Catalog)

> [!WARNING]
> **NON-BINDING — DRAFT ONLY — NOT APPROVED FOR IMPLEMENTATION**  
> This specification is an exploratory Phase 2 architecture drafting instrument prepared under `docs/00-requirements/review/`. It does **not** constitute an approved architecture design, does not commit the enterprise to physical infrastructure or frameworks, and does not authorize software implementation.  
> Transitioning to approved architecture under `docs/01-architecture/ARCH-11-architecture-decision-records.md` remains strictly blocked until the authorized Enterprise Business Owner signs off on [`docs/00-requirements/11-review-and-approval.md`](../11-review-and-approval.md) and ratifies the strategic decisions in [`docs/00-requirements/review/executive-decision-register.md`](./executive-decision-register.md).

---

## 1. Document Control

| Document Attribute | Specification Details |
| :--- | :--- |
| **Document ID** | `ARCH-11` |
| **Document Title** | Formal Architecture Decision Records (ADR) Catalog & Technical Governance |
| **Project Name** | Chellaa E-Commerce Platform |
| **Repository & Branch** | [`ezhilselvan1109/chellaa-system`](https://github.com/ezhilselvan1109/chellaa-system) &bull; Branch: `dev` |
| **File Location** | `docs/00-requirements/review/arch-11-architecture-decision-records-draft.md` |
| **Version** | `1.0.0-DRAFT` |
| **Lifecycle State** | **`DRAFT_COMPLETE_PENDING_REVIEW`** (Non-Binding Preparatory Draft) |
| **Target Promotion Path** | `docs/01-architecture/ARCH-11-architecture-decision-records.md` (Deferred pending formal executive sign-off) |
| **Author Roles** | Enterprise Architecture Lead, Principal Solution Architect & Technical Reviewer |
| **Target Reviewers** | Chief Technology Officer, Architecture Review Board (ARB), Lead Security Architect & SRE Lead |
| **Upstream Baseline Documents** | [`architecture-decision-analysis.md`](./architecture-decision-analysis.md), [`phase-2-open-decisions-and-evidence.md`](./phase-2-open-decisions-and-evidence.md) |
| **Upstream Architecture Artifacts** | `ARCH-01` through `ARCH-10` drafts under `docs/00-requirements/review/` |

### 1.1 Document Lifecycle & Governance Checkpoint

The Chellaa technical architecture governance framework enforces a strict six-stage lifecycle gate. The current progress status for `ARCH-11` is recorded below:

| Lifecycle Stage | Stage Status | Evaluation Criteria & Operational Verification |
| :--- | :---: | :--- |
| **1. Draft Completion** | **`COMPLETED`** | All 12 formal Architecture Decision Records (`ADR-001` through `ADR-012`) are fully articulated in standard MADR format. |
| **2. Requirements Traceability** | **`PENDING_RECONCILIATION`** | Previous coverage claims are withdrawn until the current source requirements and all 17 workflow mappings are independently reverified. | 100% bidirectional traceability established to all relevant business requirements, NFRs, and open questions. |
| **3. Technical Architecture Review** | **`PENDING_REVIEW`** | Subject to review and ratification by the Architecture Review Board (ARB) and CTO. |
| **4. Business Owner Policy Review** | **`PENDING_REVIEW`** | Subject to executive ratification of commercial policies in Document 11. |
| **5. Formal Baseline Approval** | **`NOT_APPROVED`** | Maintained strictly per Document 11 governance gate ([`11-review-and-approval.md`](../11-review-and-approval.md)). |
| **6. Implementation Authorization** | **`NOT_GRANTED`** | Strictly blocked pending Business Owner signature in Document 11 Section 7. |

### 1.2 Revision History

| Version | Date | Author Roles | Description of Changes & Governance Triggers |
| :---: | :---: | :--- | :--- |
| `0.1.0-PLAN` | 2026-10-18 | Principal Software Architect | Preliminary ADR planning in [`phase-2-architecture-planning.md`](./phase-2-architecture-planning.md). |
| `0.2.0-DEC` | 2026-10-18 | Solution & Database Architect | Detailed trade-off evaluations in [`architecture-decision-analysis.md`](./architecture-decision-analysis.md). |
| `0.3.0-EVID` | 2026-10-23 | Enterprise Architecture Lead | Empirical evidence requirements compiled in [`phase-2-open-decisions-and-evidence.md`](./phase-2-open-decisions-and-evidence.md). |
| `1.0.0-DRAFT` | 2026-10-24 | Enterprise Architecture Lead | Comprehensive `ARCH-11` draft: All 12 formal Architecture Decision Records (`ADR-001` to `ADR-012`) articulated in standardized MADR format with explicit non-binding status. |

---

## 2. ADR Governance & Status Management

Each Architecture Decision Record (ADR) in this catalog documents a vital technical selection, its context, evaluated alternatives, consequences, and validation criteria.

> [!IMPORTANT]
> **All ADRs are Formally OPEN and Non-Binding:**  
> In accordance with repository governance, all 12 ADRs in this draft are designated **`PROPOSED for Evaluation — OPEN`**. No technical decision is binding or authorized for production implementation until the Enterprise Business Owner signs [`docs/00-requirements/11-review-and-approval.md`](../11-review-and-approval.md) and the Architecture Review Board ratifies the decisions.

---

## 3. The 12 Formal Architecture Decision Records

---

### ADR-001: System Decomposition Granularity & Deployment Topology

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** The platform must support 100 sustained and 1,000 peak orders/minute during festive promotions ([`NFR-SCL-001`](../07-non-functional-requirements.md#nfr-scl-001)) while being developed and maintained by a small initial engineering team (2–6 developers).
* **Decision Drivers:**
  * Minimizing distributed transaction complexity (avoiding distributed 2PC).
  * Independent autoscaling of the checkout funnel under flash sale surges.
  * Operational manageability for an initial small development squad.
* **Considered Options:**
  1. *Option A:* Modular Monolith (Single deployable container, in-memory events).
  2. *Option B:* Coarse-Grained Service Clusters (5 deployable units: Merchandising, Commerce, Fulfillment, Finance, Platform).
  3. *Option C:* Fine-Grained Microservices (15–20 independently deployed services).
* **Decision Outcome:** **Option B (Coarse-Grained Service Clusters)** is proposed as the primary evaluation baseline. It provides independent scaling and failure isolation for checkout without the extreme operational drag of 20 microservices.
* **Consequences:**
  * *Positive:* 5 CI/CD pipelines; localized ACID transactions within clusters; critical checkout path shielded from platform peripheral failures.
  * *Negative:* Cross-cluster workflows require asynchronous Outbox CDC events and compensating Sagas.
* **Validation Evidence Required:** Local build benchmark and synthetic load simulation proving blast-radius containment.

---

### ADR-002: Inventory Atomic Concurrency Control Mechanism

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Flash sales on high-value electronics create extreme concurrency contention on single SKUs. The platform must guarantee zero overselling ($\text{ATS} \ge 0$ per [`NFR-INT-001`](../07-non-functional-requirements.md#nfr-int-001)) while maintaining a 15-minute temporary reservation hold ([`BR-INV-001`](../05-business-rules.md#br-inv-001)).
* **Decision Drivers:** Preventing lock wait timeouts, thread pool starvation, and negative inventory balances.
* **Considered Options:**
  1. *Option A:* Pessimistic Database Row Locking (`SELECT FOR UPDATE` in PostgreSQL).
  2. *Option B:* Distributed Redis In-Memory Hold via Atomic Lua Scripts + PostgreSQL Outbox Sync.
  3. *Option C:* Optimistic Database Concurrency (`version` column).
* **Decision Outcome:** **Option B (Redis Atomic Lua Hold)** is proposed for high-concurrency reservation, backed by PostgreSQL as the authoritative source of record.
* **Consequences:**
  * *Positive:* Sub-millisecond hold latency; zero database lock contention during flash sales; automated TTL key expiration.
  * *Negative:* Introduces Redis cluster high-availability dependency; requires outbox reconciliation sync.
* **Validation Evidence Required:** Synthetic 1,000 OPM flash reservation benchmark measuring lock timeout rates.

---

### ADR-003: Late Payment Webhook Ingestion & Dual-Path Automated Recovery

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Inbound payment capture webhooks from payment gateways may be delayed by network or banking issues and arrive after the 15-minute checkout stock hold has expired ([`BR-PAY-002`](../05-business-rules.md#br-pay-002)).
* **Decision Drivers:** Eliminating unfulfilled captured orders and preventing unauthorized inventory overselling.
* **Considered Options:**
  1. *Option A (Dual-Path Recovery):* If stock hold is still active, confirm order. If expired, automatically trigger an immediate full refund to the original source instrument.
  2. *Option B:* Force order placement into a backorder state regardless of inventory balance.
* **Decision Outcome:** **Option A (Dual-Path Recovery)** is proposed to protect the zero-overselling mandate.
* **Consequences:**
  * *Positive:* Zero manual customer support intervention; 100% compliance with inventory limits.
  * *Negative:* Customer may experience disappointment if refund is automated; requires automated notification.
* **Validation Evidence Required:** Automated integration test injecting simulated 20-minute webhook delays.

---

### ADR-004: Checkout Distributed Saga Coordination Pattern

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** The multi-step checkout workflow spans Commerce, Fulfillment, and Finance clusters ([`WF-006`](../06-business-workflows.md#wf-006), [`BR-CHK-001`](../05-business-rules.md#br-chk-001)).
* **Decision Drivers:** Ensuring forward progress, explicit compensation visibility, and easy operational auditing.
* **Considered Options:**
  1. *Option A:* Orchestrated Saga (Central state machine coordinator in Commerce Cluster).
  2. *Option B:* Choreographed Event-Driven Saga (Decentralized domain event listeners).
* **Decision Outcome:** **Option A (Orchestrated Saga)** is proposed for checkout and order placement.
* **Consequences:**
  * *Positive:* Centralized workflow visibility; clear compensation execution paths; simplified state debugging.
  * *Negative:* Commerce cluster acts as coordinator; requires tracking saga execution state.
* **Validation Evidence Required:** Chaos injection test verifying automated hold release upon payment intent failure.

---

### ADR-005: Database Architecture & Multi-Schema Isolation Strategy

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Microservice principles require data encapsulation, but managing 5 independent physical databases is cost-prohibitive for initial launch.
* **Decision Drivers:** Cost efficiency, strict schema boundaries, zero shared tables, connection pool isolation.
* **Considered Options:**
  1. *Option A:* Logical Schema Isolation on a Single Multi-AZ PostgreSQL Cluster.
  2. *Option B:* Dedicated Physical Database Instances per Service Cluster.
* **Decision Outcome:** **Option A (Logical Schema Isolation)** is proposed. 5 dedicated schemas (`merchandising_schema`, `commerce_schema`, `fulfillment_schema`, `finance_schema`, `platform_schema`) with dedicated database user roles.
* **Consequences:**
  * *Positive:* Reduced cloud infrastructure spend; simplified continuous WAL backup and PITR management.
  * *Negative:* Requires strict database user privilege governance to prevent cross-schema SQL joins.
* **Validation Evidence Required:** Connection pool saturation stress test under peak concurrent checkout load.

---

### ADR-006: Transactional Event Publishing Architecture

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Publishing domain events to Kafka directly from service application code creates dual-write failure vulnerabilities where the database commits but the message broker fails.
* **Decision Drivers:** Guaranteed at-least-once event delivery, zero dual-writes, sub-second latency.
* **Considered Options:**
  1. *Option A:* Transactional Outbox Pattern with Debezium Change Data Capture (CDC).
  2. *Option B:* Scheduled Outbox Table Poller.
  3. *Option C:* Direct Dual-Write from Application Code.
* **Decision Outcome:** **Option A (Outbox + Debezium CDC)** is proposed. Services write events atomically to local `outbox_events` tables; Debezium reads the PostgreSQL WAL stream.
* **Consequences:**
  * *Positive:* Zero dual-write inconsistencies; minimal application overhead; near real-time streaming.
  * *Negative:* Requires operating Debezium Kafka Connect workers.
* **Validation Evidence Required:** Debezium latency benchmark measuring WAL-to-Kafka delay under 1,000 events/second.

---

### ADR-007: Asynchronous Message Broker Selection

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** The platform requires an asynchronous messaging backbone for event publication, search index synchronization, and audit streaming.
* **Decision Drivers:** High throughput, consumer group horizontal scaling, message ordering per partition key, replayability.
* **Considered Options:**
  1. *Option A:* Apache Kafka / Amazon MSK.
  2. *Option B:* RabbitMQ.
  3. *Option C:* AWS SQS / SNS.
* **Decision Outcome:** **Option A (Apache Kafka / Amazon MSK)** is proposed.
* **Consequences:**
  * *Positive:* Partition-level FIFO ordering; event replay for audit and search re-indexing; high throughput.
  * *Negative:* Higher operational complexity than basic message queues.
* **Validation Evidence Required:** Partition key distribution test under 1,000 OPM surge.

---

### ADR-008: Customer vs Staff Identity & Access Governance Boundary

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Retail customer authentication relies on mobile phone OTPs, whereas staff administrative access requires corporate email, Argon2id passwords, and mandatory TOTP MFA ([`BR-ADM-001`](../05-business-rules.md#br-adm-001), [`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002)).
* **Decision Drivers:** Blast radius containment, privilege escalation defense, DPDPA PII compliance.
* **Considered Options:**
  1. *Option A:* Completely Segregated Customer and Staff Identity Services, Stores, and Signing Keys.
  2. *Option B:* Unified IAM Service with role-based segregation.
* **Decision Outcome:** **Option A (Completely Segregated Identity)** is proposed.
* **Consequences:**
  * *Positive:* Eliminates risk of customer credential compromise escalating to staff access; independent token keys.
  * *Negative:* Requires maintaining two authentication filters at the API Gateway.
* **Validation Evidence Required:** Threat model security review and penetration test of gateway token validation.

---

### ADR-009: Internal Gateway Token Defense & Signed Header Architecture

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Internal service clusters need verified user identity context without incurring the latency of decoding and verifying JWT signatures repeatedly on every microservice hop.
* **Decision Drivers:** Network performance, header spoofing defense, zero-trust perimeter.
* **Considered Options:**
  1. *Option A:* Gateway HMAC Signed Headers (`X-Gateway-Signature`).
  2. *Option B:* Decentralized JWT Validation on Every Internal Service.
* **Decision Outcome:** **Option A (Signed Gateway Headers)** is proposed.
* **Consequences:**
  * *Positive:* Sub-millisecond internal claims validation; prevents internal header injection; centralizes token expiry logic.
  * *Negative:* Requires shared HMAC secret governance across internal service clusters.
* **Validation Evidence Required:** Gateway cryptographic signing overhead and header spoofing penetration test.

---

### ADR-010: Catalog Search Read-Model Synchronization Engine

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Public catalog search requires sub-200ms latency, typo tolerance, and faceted filtering ([`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001)), which cannot be satisfied by direct OLTP relational database queries.
* **Decision Drivers:** Search query latency, typo tolerance, faceted counts, eventual consistency ($\le 5\text{s}$).
* **Considered Options:**
  1. *Option A:* Dedicated Elasticsearch / Amazon OpenSearch 2.x Managed Cluster.
  2. *Option B:* PostgreSQL Full-Text Search (FTS) on Read Replica.
* **Decision Outcome:** **Option A (Dedicated OpenSearch Cluster)** is proposed as an asynchronous CQRS read projection.
* **Consequences:**
  * *Positive:* Sub-200ms response times under 500 RPS; native fuzzy matching and dynamic facets.
  * *Negative:* Additional managed infrastructure cost; requires event-driven CDC indexing pipeline.
* **Validation Evidence Required:** 100,000 SKU search latency benchmark under 5,000 concurrent sessions.

---

### ADR-011: Executive Reporting & BI Data Isolation Strategy

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** Complex executive queries (GMV, net sales, refund take-rates) run for extended periods and must not compete for database locks with online checkout transactions ([`BR-RPT-001`](../05-business-rules.md#br-rpt-001)).
* **Decision Drivers:** Zero lock contention on transactional write DB, cost-effective reporting.
* **Considered Options:**
  1. *Option A:* Dedicated PostgreSQL Read Replica.
  2. *Option B:* Columnar Analytics Datamart (ClickHouse / Amazon Redshift).
* **Decision Outcome:** **Option A (PostgreSQL Read Replica)** is proposed for initial launch scale.
* **Consequences:**
  * *Positive:* Zero lock waits on primary DB; low infrastructure cost; reuses existing relational schemas.
  * *Negative:* May need migration to columnar store if historical data exceeds several hundred gigabytes.
* **Validation Evidence Required:** Analytical query replication lag test during simulated 1,000 OPM surge.

---

### ADR-012: Cloud Infrastructure Topology & Disaster Recovery Strategy

* **Status:** `PROPOSED for Evaluation — OPEN`
* **Context:** The platform requires $\ge 99.9\%$ uptime ([`NFR-AVL-001`](../07-non-functional-requirements.md#nfr-avl-001)) and strict disaster recovery targets ($\text{RTO} \le 2\text{h}$, $\text{RPO} \le 5\text{m}$ per [`NFR-BCM-001`](../07-non-functional-requirements.md#nfr-bcm-001)).
* **Decision Drivers:** Data residency compliance (India), cost, automated failover, disaster recovery RTO/RPO.
* **Considered Options:**
  1. *Option A:* Single-Region Multi-AZ Active-Active Topology in AWS Mumbai (`ap-south-1`) with Automated PITR.
  2. *Option B:* Multi-Region Active-Passive Topology across Mumbai and Hyderabad.
* **Decision Outcome:** **Option A (Single-Region Multi-AZ)** is proposed. Multi-AZ Aurora PostgreSQL with continuous WAL archiving satisfies $\text{RPO} \le 5\text{m}$ and $\text{RTO} \le 2\text{h}$ without multi-region replication cost.
* **Consequences:**
  * *Positive:* Low operational complexity; zero cross-region latency; eliminates data synchronization conflicts.
  * *Negative:* Vulnerable only to catastrophic total loss of all 3 AWS Mumbai availability zones simultaneously.
* **Validation Evidence Required:** Automated Aurora failover and PITR restore drill measuring exact recovery time.

---

## 4. Traceability to Baseline Business Requirements

| ADR ID | Decision Title | Baseline Requirement IDs | Target Governance Invariant |
| :---: | :--- | :--- | :--- |
| **`ADR-001`** | System Decomposition Granularity | [`NFR-SCL-001`](../07-non-functional-requirements.md#nfr-scl-001) | Coarse-Grained Clusters balance operational drag and surge scale. |
| **`ADR-002`** | Inventory Atomic Concurrency | [`BR-INV-001`](../05-business-rules.md#br-inv-001), [`NFR-INT-001`](../07-non-functional-requirements.md#nfr-int-001) | Zero overselling under 1,000 OPM flash sale contention. |
| **`ADR-003`** | Late Webhook Recovery Engine | [`BR-PAY-002`](../05-business-rules.md#br-pay-002), [`WF-007`](../06-business-workflows.md#wf-007) | Zero unfulfilled captured payments; automatic source refund. |
| **`ADR-004`** | Checkout Saga Coordination | [`BR-CHK-001`](../05-business-rules.md#br-chk-001), [`WF-006`](../06-business-workflows.md#wf-006) | Orchestrated state machine ensures clean compensating rollbacks. |
| **`ADR-005`** | Database Schema Isolation | [`FEAT-FIN-001`](../03-business-feature-inventory.md#feat-fin-001), [`NFR-SEC-003`](../07-non-functional-requirements.md#nfr-sec-003) | Zero shared tables; zero cross-schema SQL queries. |
| **`ADR-006`** | Transactional Event Outbox | [`BR-OPS-001`](../05-business-rules.md#br-ops-001), [`NFR-AVL-002`](../07-non-functional-requirements.md#nfr-avl-002) | Reliable Debezium CDC eliminates dual-write failures. |
| **`ADR-007`** | Asynchronous Event Broker | [`NFR-AVL-002`](../07-non-functional-requirements.md#nfr-avl-002), [`NFR-SCL-001`](../07-non-functional-requirements.md#nfr-scl-001) | Kafka provides ordered partition streams and event replay. |
| **`ADR-008`** | Customer vs Staff Identity | [`BR-ADM-001`](../05-business-rules.md#br-adm-001), [`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002) | Segregated credential stores and independent signing keys. |
| **`ADR-009`** | Signed Gateway Header Defense | [`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002), [`FR-SEC-002`](../04-functional-requirements.md#fr-sec-002) | HMAC-SHA256 signature validates internal request claims. |
| **`ADR-010`** | Search Read-Model Engine | [`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001), [`FEAT-SRCH-001`](../03-business-feature-inventory.md#feat-srch-001) | Sub-200ms faceted typo-tolerant catalog search queries. |
| **`ADR-011`** | Reporting Data Isolation | [`FEAT-RPT-001`](../03-business-feature-inventory.md#feat-rpt-001), [`BR-RPT-001`](../05-business-rules.md#br-rpt-001) | Zero query lock contention on transactional write database. |
| **`ADR-012`** | Cloud DR & Topology | [`NFR-BCM-001`](../07-non-functional-requirements.md#nfr-bcm-001), [`OQ-017`](../08-assumptions-dependencies-open-questions.md#oq-017) | $\text{RTO} \le 2\text{h}$, $\text{RPO} \le 5\text{m}$ via continuous WAL streaming in AWS Mumbai. |

---

## 5. Review and Acceptance Checklist

The checklist below governs the quality gate audit for `ARCH-11`:

- [x] 1. **Complete 12-ADR Catalog:** All 12 formal architecture decision records (`ADR-001` to `ADR-012`) articulated in standardized MADR format.
- [x] 2. **Explicit Non-Binding Status:** All ADRs marked `PROPOSED for Evaluation — OPEN` pending executive sign-off.
- [x] 3. **Empirical Evidence Criteria:** Specific benchmark and testing requirements defined for every decision.
- [x] 4. **Trade-Offs and Consequences:** Positive and negative consequences documented for all selected options.
- [x] 5. **Single-Retailer Anti-Scope Preserved:** Zero multi-vendor, marketplace, or seller commission architectures evaluated.
- [x] 6. **100% Traceability:** Mapped to declared BRD requirement identifiers.
- [x] 7. **Non-Binding Labeling:** Prominently marked as a non-binding draft pending formal Phase 1 sign-off.


---

## Cross-document reconciliation note (2026-10-24)

- Canonical workflow identifiers and titles are defined by `docs/00-requirements/06-business-workflows.md` (WF-001–WF-017). Stale mappings must not be treated as valid merely because a document previously claimed coverage.
- Recorded decisions are inputs, not implementation approval: online payments use Razorpay only; notifications use SMS, Email and WhatsApp Business API while channel/fallback policy remains open; reviews require verified buyers and admin moderation; advanced reporting is selected while specifications remain open; legacy customer/order history migration is out of scope while catalog and opening-stock import remain in scope; statutory financial records are retained for eight financial years subject to Legal/Security review of anonymization details.
- **Shipping decision conflict:** Current OQ-005 / ADQ-014 describes a hybrid provider model, while a separate owner instruction recorded in project context selects Chellaa's own Shipping Management System and excludes aggregators. This conflict is unresolved; no named courier/aggregator integration or provider-routing policy is approved by this note. Resolve before architecture sign-off.
- This draft remains non-binding. Phase 1 review is `PENDING_APPROVAL`, requirements baseline is `NOT_APPROVED`, and architecture implementation authorization is `NOT_GRANTED`. No promotion to `docs/01-architecture/` is authorized by this note.
