# Phase 2 — Technical Architecture Decisions & Evidence Register

> [!WARNING]
> **NON-BINDING EVALUATION ARTIFACT — NOT APPROVED FOR IMPLEMENTATION**  
> This register tracks the 12 candidate architectural technical decisions (`AD-001` through `AD-012`). All alternatives and preliminary proposals are strictly **`PROPOSED for Evaluation`** and remain **`OPEN`**. None shall be treated as binding or approved until formal Phase 2 architecture design is authorized following Phase 1 executive sign-off in [`docs/00-requirements/11-review-and-approval.md`](../11-review-and-approval.md).

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | `ARCH-DEC-EVID-01` |
| **Document Title** | Technical Architecture Decisions Register, Trade-Off Evaluations & Empirical Evidence Plan |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Repository** | [`ezhilselvan1109/chellaa-system`](https://github.com/ezhilselvan1109/chellaa-system) |
| **Target Branch** | `dev` |
| **File Location** | `docs/00-requirements/review/phase-2-open-decisions-and-evidence.md` |
| **Creation Date** | 2026-10-23 |
| **Author Roles** | Senior Solution Architect, Distributed Systems Engineer & Database Architect |
| **Governance Status** | **`NON_BINDING_ANALYSIS`** (Architecture Research & Evidence Tracking Only) |
| **Phase 1 Baseline Status** | **`PENDING_APPROVAL`** (Maintained strictly per governance constraints) |
| **Architecture Authorization** | **`NOT_GRANTED`** (Awaiting Business Owner Sign-Off in Document 11) |
| **Software Implementation** | **`NOT_STARTED`** (Strictly gated behind Phase 2 ratification) |

---

## 1. Master Architecture Decisions Tracking Register

| Decision ID | Decision Statement | Related BRD IDs | Options Under Evaluation | Proposal (Non-Binding) | Required Empirical Evidence / Review | Decision Owner | Current Status |
| :---: | :--- | :--- | :--- | :--- | :--- | :---: | :---: |
| **`AD-001`** | **System Decomposition Granularity** | [`NFR-SCL-001`](../07-non-functional-requirements.md#nfr-scl-001) | A: Modular Monolith<br>B: Coarse Clusters (4–6)<br>C: Fine Microservices (15–20) | Option B: Coarse Clusters | Developer onboarding velocity & CI/CD pipeline cost analysis. | Principal Solution Architect | **`OPEN`** |
| **`AD-002`** | **Inventory Concurrency Mechanism** | [`BR-INV-001`](../05-business-rules.md#br-inv-001)<br>[`BR-INV-002`](../05-business-rules.md#br-inv-002) | A: PostgreSQL `SELECT FOR UPDATE`<br>B: Redis Lua Distributed Hold<br>C: Optimistic Version Locking | Option B: Redis Lua + DB Sync | Synthetic 1,000 OPM flash-sale stress test measuring lock wait timeouts. | Distributed Systems Engineer | **`OPEN`** |
| **`AD-003`** | **Late Payment Webhook Arrival** | [`BR-PAY-003`](../05-business-rules.md#br-pay-003)<br>[`WF-007`](../06-business-workflows.md#wf-007) | A: Re-reserve if stock exists; auto-refund if depleted<br>B: Force backorder | Option A: Dual-Path Recovery | Webhook race condition simulation validating automated source refund trigger. | Enterprise Business Owner | **`OPEN`** |
| **`AD-004`** | **Checkout Saga Orchestration** | [`BR-CHK-001`](../05-business-rules.md#br-chk-001)<br>[`WF-006`](../06-business-workflows.md#wf-006) | A: Orchestrated Saga (State Machine)<br>B: Choreographed Event Saga | Option A: Orchestrated Saga | Compensation state machine prototype measuring partial failure recovery time. | Principal Solution Architect | **`OPEN`** |
| **`AD-005`** | **Database Architecture & Isolation** | [`FEAT-FIN-001`](../03-business-feature-inventory.md#feat-fin-001)<br>[`NFR-SEC-003`](../07-non-functional-requirements.md#nfr-sec-003) | A: Logical Schema Isolation (Shared Postgres)<br>B: Physical Database-per-Service | Option A: Logical Schema Isolation | Connection pool exhaustion test under peak concurrent checkout load. | Lead Database Architect | **`OPEN`** |
| **`AD-006`** | **Transactional Event Publishing** | [`BR-OPS-001`](../05-business-rules.md#br-ops-001)<br>[`NFR-AVL-002`](../07-non-functional-requirements.md#nfr-avl-002) | A: Outbox + Debezium CDC<br>B: Outbox + Scheduled Poller<br>C: Direct Dual-Write | Option A: Outbox + CDC Engine | Event relay latency and CDC operational overhead benchmark under load. | Distributed Systems Engineer | **`OPEN`** |
| **`AD-007`** | **Asynchronous Message Broker** | [`NFR-AVL-002`](../07-non-functional-requirements.md#nfr-avl-002)<br>[`NFR-SCL-001`](../07-non-functional-requirements.md#nfr-scl-001) | A: Apache Kafka / AWS MSK<br>B: RabbitMQ<br>C: AWS SQS/SNS | Option A: Apache Kafka | Partition sizing model for 1,000 OPM surge with in-order key distribution. | Distributed Systems Engineer | **`OPEN`** |
| **`AD-008`** | **Customer vs Staff Identity Boundary** | [`BR-ADM-001`](../05-business-rules.md#br-adm-001)<br>[`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002) | A: Completely separate auth services & keys<br>B: Unified identity service | Option A: Fully Segregated | Threat model and blast radius security review of staff credential compromise. | Lead Security Architect | **`OPEN`** |
| **`AD-009`** | **Internal Gateway Token Defense** | [`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002)<br>[`FR-SEC-002`](../04-functional-requirements.md#fr-sec-002) | A: Gateway signed headers (`X-Gateway-Signature`)<br>B: Decentralized JWT verification | Option A: Signed Gateway Headers | Gateway cryptographic signing overhead and header spoofing penetration test. | Lead Security Architect | **`OPEN`** |
| **`AD-010`** | **Catalog Search Read-Model Engine** | [`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001)<br>[`FEAT-SRCH-001`](../03-business-feature-inventory.md#feat-srch-001) | A: Dedicated OpenSearch Cluster<br>B: PostgreSQL Read Replica FTS | Option A: OpenSearch Cluster | Query latency benchmark with 100,000 SKUs under 5,000 concurrent sessions. | Principal Solution Architect | **`OPEN`** |
| **`AD-011`** | **Executive Reporting Data Isolation** | [`FEAT-RPT-001`](../03-business-feature-inventory.md#feat-rpt-001)<br>[`NFR-PERF-003`](../07-non-functional-requirements.md#nfr-perf-003) | A: PostgreSQL Read Replica<br>B: Columnar Datamart (ClickHouse) | Option A: PostgreSQL Read Replica | Long-running aggregation query impact on replication lag during peak checkout. | Lead Database Architect | **`OPEN`** |
| **`AD-012`** | **Cloud DR & Infrastructure Topology** | [`NFR-BCM-001`](../07-non-functional-requirements.md#nfr-bcm-001)<br>[`OQ-017`](../08-assumptions-dependencies-open-questions.md#oq-017) | A: Single-Region Multi-AZ Active-Active<br>B: Multi-Region Active-Passive | Option A: Single-Region Multi-AZ | Automated database failover and PITR drill measuring exact RTO and RPO. | Cloud Architect / SRE Lead | **`OPEN`** |

---

## 2. Detailed Technical Decision Profiles (AD-001 through AD-012)

---

### AD-001: System Decomposition Granularity & Team Topology
* **Problem Statement:** How to decompose the 20 candidate business domains into deployable physical units to satisfy the 1,000 OPM surge target without overwhelming a small initial engineering team (2–6 engineers).
* **Alternatives Evaluated:**
  1. *Option A (Modular Monolith):* Single deployable artifact with strictly enforced package boundaries and logical schema isolation. Zero network latency for inter-module calls. Full redeployment required for minor changes.
  2. *Option B (Coarse-Grained Clusters — Recommended for Evaluation):* 5 deployable service clusters grouped by business lifecycle (*Merchandising*, *Commerce*, *Fulfillment*, *Finance*, *Platform*). Enables independent scaling of high-throughput checkout without scaling back-office modules. Manageable operational overhead (5 CI/CD pipelines).
  3. *Option C (Fine-Grained Microservices):* 15–20 independent services. Causes severe operational drag, distributed transaction bugs, and network serialization latency for an initial team.
* **Risks & Trade-Offs:** Option C creates "distributed monolith" antipatterns. Option A risks thread exhaustion if unisolated background jobs spike. Option B delivers the optimal operational balance.
* **Required Evidence:** Comparative local build/test benchmark and cloud infrastructure cost projection for 5 clusters vs 15 containers.
* **Decision Owner:** Principal Solution Architect | **Status:** **`OPEN (Proposed: Option B)`**

---

### AD-002: Inventory Atomic Concurrency Control Mechanism
* **Problem Statement:** How to guarantee 100% zero overselling during 1,000 orders/minute promotional flash sales contending on high-demand electronics SKUs ([`BR-INV-001`](../05-business-rules.md#br-inv-001), [`BR-INV-002`](../05-business-rules.md#br-inv-002)).
* **Alternatives Evaluated:**
  1. *Option A (PostgreSQL `SELECT FOR UPDATE`):* Pessimistic row locking on stock ledger with a 2-second timeout ceiling. Native ACID guarantees; but contending 1,000 OPM on identical SKUs exhausts database connection pools and causes lock wait timeouts.
  2. *Option B (In-Memory Redis Distributed Hold via Lua Script — Recommended for Evaluation):* Atomic stock decrement in Redis via single-threaded Lua script with 15-minute TTL; asynchronous PostgreSQL synchronization via transactional outbox. Handles 50,000+ ops/sec with sub-millisecond response.
  3. *Option C (Optimistic Locking):* Update with version check. Under 1,000 OPM surge, $> 80\%$ of transactions fail validation and require retries, creating severe retry storms.
* **Required Evidence:** Synthetic Gatling/k6 load test simulating 1,000 concurrent orders/minute contending on 10 SKUs, comparing Redis Lua throughput against PostgreSQL connection pool exhaustion.
* **Decision Owner:** Distributed Systems Engineer | **Status:** **`OPEN (Proposed: Option B)`**

---

### AD-003: Late Payment Arrival & Stock Depletion Handling
* **Problem Statement:** What recovery path must be executed when a third-party payment gateway confirms payment after the 15-minute checkout hold has expired and physical stock has been depleted ([`BR-PAY-003`](../05-business-rules.md#br-pay-003)).
* **Alternatives Evaluated:**
  1. *Option A (Dual-Path Recovery — Recommended for Evaluation):* Re-allocate stock if physical units remain available; if inventory is completely depleted, transition order to `EXPIRED_OUT_OF_STOCK`, immediately trigger an **automated source refund** via the gateway API, and dispatch an urgent SMS alert to the customer.
  2. *Option B (Backorder Placement):* Place the customer on backorder. Prohibited by [`OQ-014`](../08-assumptions-dependencies-open-questions.md#oq-014) (`ASM-007` single enterprise warehouse inventory constraint).
* **Required Evidence:** Integration test simulating gateway webhook delivery after hold expiry, verifying automated refund API call and idempotency deduplication.
* **Decision Owner:** Enterprise Business Owner | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-004: Checkout Saga Orchestration Pattern
* **Problem Statement:** How to coordinate the multi-step checkout workflow across Inventory Hold, Coupon Lock, Payment Intent, and Order Confirmation ([`BR-CHK-001`](../05-business-rules.md#br-chk-001), [`WF-006`](../06-business-workflows.md#wf-006)).
* **Alternatives Evaluated:**
  1. *Option A (Orchestrated Saga — Recommended for Evaluation):* A central stateless Checkout Orchestrator executes a explicit state machine, coordinating steps sequentially and dispatching compensating transactions (e.g., release inventory hold) upon failure. Highly testable, auditable, and visible.
  2. *Option B (Choreographed Saga):* Services react asynchronously to peer domain events. Harder to trace, debug, and enforce timeout compensation across distributed networks.
* **Required Evidence:** State machine unit and integration tests validating compensation rollback when payment intent initialization times out.
* **Decision Owner:** Principal Solution Architect | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-005: Database Architecture & Data Isolation Strategy
* **Problem Statement:** How to enforce strict data ownership and isolation across candidate domains while minimizing database management complexity ([`FEAT-FIN-001`](../03-business-feature-inventory.md#feat-fin-001)).
* **Alternatives Evaluated:**
  1. *Option A (Logical Schema Isolation on Shared PostgreSQL — Recommended for Evaluation):* Single managed PostgreSQL cluster (Multi-AZ) with dedicated schemas (`catalog`, `commerce`, `fulfillment`, `finance`, `platform`) and distinct database user permissions. Direct cross-schema joins disabled. Unified Point-in-Time Recovery (PITR); lowest operational cost.
  2. *Option B (Physical Database-per-Service):* 5 separate managed PostgreSQL clusters. Maximum blast radius isolation; but doubles cloud hosting bills and complicates cross-domain reporting ETL.
* **Required Evidence:** Database connection pool stress testing measuring "noisy neighbor" contention between bulk catalog imports and checkout transactions.
* **Decision Owner:** Lead Database Architect | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-006: Transactional Event Publishing Pattern
* **Problem Statement:** How to guarantee atomic local database mutation and domain event publishing without two-phase commit (2PC) overhead ([`BR-OPS-001`](../05-business-rules.md#br-ops-001)).
* **Alternatives Evaluated:**
  1. *Option A (Transactional Outbox + Debezium CDC — Recommended for Evaluation):* Application writes events to a local `outbox` table within the same ACID transaction. Debezium reads the PostgreSQL Write-Ahead Log (WAL) and streams events to Kafka with zero application polling overhead.
  2. *Option B (Outbox + Scheduled Poller):* Background worker queries the `outbox` table every 1 second. Simple to implement, but adds database table query load and latency.
  3. *Option C (Direct Dual-Write):* Application writes to DB and publishes to broker sequentially. Highly vulnerable to partial failures and orphaned data loss.
* **Required Evidence:** Proof-of-concept measuring end-to-end event propagation latency from database commit to Kafka consumer under sustained 500 events/sec.
* **Decision Owner:** Distributed Systems Engineer | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-007: Asynchronous Messaging Broker Selection
* **Problem Statement:** Selecting the enterprise messaging infrastructure to support reliable domain event distribution and order lifecycle triggers ([`NFR-AVL-002`](../07-non-functional-requirements.md#nfr-avl-002)).
* **Alternatives Evaluated:**
  1. *Option A (Apache Kafka / AWS Managed MSK — Recommended for Evaluation):* High throughput, partition-based ordered delivery per entity key (`order_id`, `sku_code`), immutable distributed commit log, replayable event streams.
  2. *Option B (RabbitMQ):* Complex exchange/routing key model; excellent for point-to-point task queues, but lacks durable multi-subscriber replay buffers.
  3. *Option C (AWS SQS/SNS):* Fully managed serverless queues; simple operations, but lacks global partition order and introduces vendor lock-in.
* **Required Evidence:** Cost-benefit and partition sizing model for AWS MSK vs self-managed Kafka container instances.
* **Decision Owner:** Distributed Systems Engineer | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-008: Customer vs Administrative Staff Identity Segregation
* **Problem Statement:** How to architect authentication and authorization to prevent staff credential compromise from impacting the customer storefront ([`BR-ADM-001`](../05-business-rules.md#br-adm-001), [`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002)).
* **Alternatives Evaluated:**
  1. *Option A (Fully Segregated Identity Boundaries — Recommended for Evaluation):* Administrative staff authenticate via a dedicated IAM boundary enforcing enterprise email, strong password, and mandatory MFA. Customer storefront authenticates via mobile phone + SMS OTP. Separate cryptographic signing keys and separate database user tables.
  2. *Option B (Unified Identity Provider):* Single user table and single auth service with role flags. High risk: compromised customer database could expose administrative credential hashes.
* **Required Evidence:** Threat modeling and security architecture audit confirming zero blast radius overlap between storefront and administrative sessions.
* **Decision Owner:** Lead Security Architect | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-009: Internal Gateway Token Propagation & Defense
* **Problem Statement:** How internal service clusters verify identity and permissions without incurring redundant cryptographic JWT signature checks on every internal hop ([`NFR-SEC-002`](../07-non-functional-requirements.md#nfr-sec-002)).
* **Alternatives Evaluated:**
  1. *Option A (Signed Gateway Header Injection — Recommended for Evaluation):* API Gateway verifies incoming customer/staff JWT against JWKS once at the perimeter. Gateway injects sanitized headers (`X-User-Id`, `X-User-Roles`, `X-Permissions`) signed with an HMAC token (`X-Gateway-Signature`). Internal clusters verify the signature.
  2. *Option B (Decentralized JWT Verification):* Every internal service verifies the full JWT token independently. Incurs repeated public-key cryptographic overhead on every internal network hop.
* **Required Evidence:** Gateway cryptographic benchmark and internal header spoofing penetration test.
* **Decision Owner:** Lead Security Architect | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-010: Catalog Search Read-Model Technology Selection
* **Problem Statement:** How to deliver sub-200ms typo-tolerant search and multi-faceted filtering across 100,000 SKUs under 5,000 concurrent sessions ([`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001)).
* **Alternatives Evaluated:**
  1. *Option A (Dedicated OpenSearch / Elasticsearch Cluster — Recommended for Evaluation):* Inverted index, native multi-faceted aggregations, fuzzy typo-tolerance, horizontal read scaling. Asynchronously populated via domain events.
  2. *Option B (PostgreSQL Read Replica Full-Text Search):* Lower operational cost, but struggles with complex multi-attribute faceted filtering and deep sorting under high concurrent load.
* **Required Evidence:** Query latency benchmark under 5,000 concurrent simulated browsing sessions seeded with 100,000 electronics SKUs with dynamic specifications.
* **Decision Owner:** Principal Solution Architect | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-011: Executive Reporting Data Isolation Architecture
* **Problem Statement:** How to generate heavy daily executive sales, GMV, and GST tax liability reports without locking operational OLTP database tables during peak shopping hours ([`FEAT-RPT-001`](../03-business-feature-inventory.md#feat-rpt-001)).
* **Alternatives Evaluated:**
  1. *Option A (Dedicated PostgreSQL Read Replica — Recommended for Evaluation):* Asynchronous physical replication. Directs all reporting queries away from primary write instances. Simple, cost-effective, and sufficient for initial reporting volumes.
  2. *Option B (Columnar Datamart via ClickHouse):* Ultra-fast columnar aggregations, but introduces additional ETL pipeline complexity unnecessary for initial launch scale.
* **Required Evidence:** Measure PostgreSQL replication lag and query execution time during simulated 1,000 OPM checkout load while running executive daily sales aggregations.
* **Decision Owner:** Lead Database Architect | **Status:** **`OPEN (Proposed: Option A)`**

---

### AD-012: Cloud Infrastructure DR Topology & Recovery SLA
* **Problem Statement:** Defining the cloud infrastructure topology required to guarantee 99.9% uptime, RPO $\le 5\text{ minutes}$, and RTO $\le 2\text{ hours}$ ([`OQ-017`](../08-assumptions-dependencies-open-questions.md#oq-017), [`NFR-BCM-001`](../07-non-functional-requirements.md#nfr-bcm-001)).
* **Alternatives Evaluated:**
  1. *Option A (Single-Region Multi-AZ Active-Active — Recommended for Evaluation):* Services distributed across 3 Availability Zones within the primary AWS/GCP region. Managed database Multi-AZ with automatic failover ($\le 60\text{ seconds}$) and continuous WAL streaming to cloud object storage. Delivers RTO $\le 2\text{ hours}$ and RPO $\le 5\text{ minutes}$ at baseline cost.
  2. *Option B (Multi-Region Active-Passive):* Standby region with continuous cross-region replication. Guarantees near-zero RTO during total regional outages, but increases infrastructure costs by $> 80\%$.
* **Required Evidence:** Simulated database disaster recovery failover drill on staging measuring exact automated failover time and continuous WAL point-in-time restoration.
* **Decision Owner:** Cloud Architect / SRE Lead | **Status:** **`OPEN (Proposed: Option A)`**

---

## 3. Required Empirical Benchmarks & Security Reviews Master Plan

Before any candidate decision is promoted to an approved Architecture Decision Record (ADR) in `ARCH-11`, the following seven empirical validation procedures must be executed on a staging prototype:

| Validation Procedure | Target Decisions | Test Methodology & Tooling | Success Acceptance Criteria |
| :--- | :---: | :--- | :--- |
| **1. Flash-Sale Concurrency Benchmark** | `AD-002` | k6/Gatling simulating 1,000 orders/minute contending on 10 high-demand SKUs. | **Zero overselling**; Redis lock latency $\le 5\text{ms}$; zero DB connection starvation. |
| **2. Checkout Latency Load Test** | `AD-001`, `AD-004` | Simulated checkout submission under 100 sustained and 1,000 peak OPM. | Checkout submission $p95 \le 1,000\text{ms}$ ([`NFR-PERF-002`](../07-non-functional-requirements.md#nfr-perf-002)). |
| **3. Search Read-Model Benchmark** | `AD-010` | 100,000 SKUs seeded; 5,000 concurrent browsing sessions executing faceted queries. | Search response latency $p95 \le 200\text{ms}$ ([`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001)). |
| **4. Batch Catalog Import Benchmark** | `AD-005` | Background worker ingestion of 5,000 SKUs with dynamic specifications. | Import completion $\le 15\text{ minutes}$ with zero storefront browsing degradation ([`NFR-PERF-003`](../07-non-functional-requirements.md#nfr-perf-003)). |
| **5. Disaster Recovery & PITR Drill** | `AD-012` | Simulated primary database failure and Point-in-Time Recovery from continuous WAL. | Recovery Time Objective $\le 2\text{ hours}$; Recovery Point Objective $\le 5\text{ minutes}$ ([`NFR-BCM-001`](../07-non-functional-requirements.md#nfr-bcm-001)). |
| **6. Circuit Breaker Fault Injection Drill** | `AD-003`, `AD-007` | Synthetic 500ms+ latency and 60% failure rate injected into mock gateway and courier APIs. | Resilience4j breaker trips to `OPEN` within 20 requests; worker threads protected ([`NFR-AVL-002`](../07-non-functional-requirements.md#nfr-avl-002)). |
| **7. Security & Blast Radius Review** | `AD-008`, `AD-009` | Penetration test attempting administrative header spoofing and staff token forgery. | Internal clusters reject unsigned headers (HTTP 401); zero customer-to-staff elevation. |
