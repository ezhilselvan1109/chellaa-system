# Business Requirements Document (BRD)
## Document 07: Non-Functional Requirements (NFRs)

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-07 |
| **Document Title** | Non-Functional Requirements, Operational Quality Attributes & SLA Standards |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.0.0-DRAFT |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Quality Attribute Framework

This document defines the measurable quality standards, operational expectations, security baselines, and reliability criteria for the Chellaa E-Commerce Platform.

Quality attributes are defined independently of specific vendor implementations, establishing clear service level objectives (SLOs) and acceptance criteria against which subsequent architecture designs will be evaluated.

---

## 2. Detailed Non-Functional Requirements

### 2.1 Performance & Latency Expectations

#### NFR-PERF-001: Product Search & Discovery Response Latency
* **Quality Attribute:** Performance (Response Time)
* **Requirement Statement:** The system must execute product search queries and faceted filter requests in $\le 200\text{ms}$ at the 95th percentile ($p95$) and $\le 500\text{ms}$ at the 99th percentile ($p99$) under standard operational load.
* **Business Rationale:** Sub-second search response directly impacts customer browsing satisfaction and prevents abandonment in consumer electronics shopping.
* **Measurement Method:** Automated API response time instrumentation tracking time elapsed between HTTP request ingestion and response dispatch.
* **Target Value:** $p95 \le 200\text{ms}$; $p99 \le 500\text{ms}$.
* **Scope:** Public Storefront Search & Filter APIs.
* **Priority:** `P0`
* **Dependencies:** `FEAT-SRCH-001`.
* **Acceptance / Verification Method:** Performance load testing simulating 50 concurrent search queries across a 10,000-SKU catalog.
* **Approval Status:** `PROPOSED`

#### NFR-PERF-002: Checkout Submission & Reservation Latency
* **Quality Attribute:** Performance (Transaction Processing)
* **Requirement Statement:** Checkout submission, including server-side total calculation and atomic inventory reservation, must complete in $\le 1,000\text{ms}$ ($p95$) and $\le 2,500\text{ms}$ ($p99$).
* **Business Rationale:** Checkout delays induce customer anxiety, causing impatient users to hit "refresh" or "back", generating duplicate requests.
* **Measurement Method:** APM transaction tracing measuring execution latency from checkout button click to payment session response.
* **Target Value:** $p95 \le 1,000\text{ms}$; $p99 \le 2,500\text{ms}$.
* **Scope:** Checkout & Order Creation APIs.
* **Priority:** `P0`
* **Dependencies:** `FEAT-CHK-001`, `FEAT-INV-002`.
* **Acceptance / Verification Method:** Concurrency stress test under simulated flash-sale traffic (100 simultaneous checkouts).
* **Approval Status:** `PROPOSED`

#### NFR-PERF-003: Administrative Operations Latency
* **Quality Attribute:** Performance (Internal Operations)
* **Requirement Statement:** Standard administrative dashboard queries (order search, catalog editing, inventory adjustments) must respond in $\le 800\text{ms}$ ($p95$), with bulk data exports completing asynchronously within 60 seconds for datasets up to 50,000 records.
* **Business Rationale:** Ensures operational productivity for warehouse and customer service personnel.
* **Measurement Method:** Administrative portal transaction monitoring.
* **Target Value:** $p95 \le 800\text{ms}$ for standard queries; $\le 60\text{s}$ for bulk exports.
* **Scope:** Administrative APIs.
* **Priority:** `P1`
* **Dependencies:** `FEAT-ADM-001`, `FEAT-DAT-001`.
* **Acceptance / Verification Method:** Benchmark administrative queries against populated database with 500,000 historical orders.
* **Approval Status:** `PROPOSED`

---

### 2.2 Scalability & Capacity Planning

#### NFR-SCL-001: Peak Throughput & Flash Sale Concurrency
* **Quality Attribute:** Scalability (Throughput)
* **Requirement Statement:** The platform must support a baseline throughput of 100 orders per minute (OPM) and dynamically scale to accommodate promotional traffic spikes of up to 1,000 orders per minute without data corruption or inventory overselling.
* **Business Rationale:** Festive sales (e.g., Diwali, New Year) generate 10x traffic bursts where system crashes directly destroy revenue and customer trust.
* **Measurement Method:** Synthetic load generation simulating browsing-to-checkout funnels.
* **Target Value:** Baseline: 100 OPM; Peak: 1,000 OPM; Concurrent active browsing users: 10,000.
* **Scope:** Core Commerce Services (Catalog, Cart, Checkout, Inventory).
* **Priority:** `P0`
* **Dependencies:** `FEAT-INV-001`, `FEAT-CHK-001`.
* **Acceptance / Verification Method:** Distributed load test sustaining 1,000 OPM for 15 minutes with zero oversell incidents.
* **Approval Status:** `PROPOSED` (See [OQ-016](./08-assumptions-dependencies-open-questions.md#oq-016))

#### NFR-SCL-002: Catalog & Historical Order Volume Capacity
* **Quality Attribute:** Scalability (Data Capacity)
* **Requirement Statement:** The data persistence architecture must comfortably store and index up to 50,000 product SKUs and retain up to 5,000,000 historical order records with zero degradation in customer query latency.
* **Business Rationale:** Supports 5-year enterprise business growth without requiring database replatforming.
* **Measurement Method:** Database indexing and query execution plans against synthetic dataset.
* **Target Value:** 50,000 SKUs; 5,000,000 historical orders.
* **Scope:** Persistence & Search Engines.
* **Priority:** `P1`
* **Dependencies:** `FEAT-CAT-001`, `FEAT-ORD-001`.
* **Acceptance / Verification Method:** Query response benchmarks on pre-seeded 5M order database.
* **Approval Status:** `CONFIRMED`

---

### 2.3 Availability, Resilience & Fault Isolation

#### NFR-AVL-001: Platform Operational Availability (Uptime)
* **Quality Attribute:** Availability (Uptime)
* **Requirement Statement:** The customer-facing e-commerce platform must maintain an availability of $\ge 99.90\%$ during standard operations (excluding announced scheduled maintenance windows), equating to less than 8.76 hours of unplanned downtime per year.
* **Business Rationale:** Unplanned downtime during business hours results in immediate revenue loss and reputational damage.
* **Measurement Method:** External synthetic health check monitoring pinging core health endpoints every 60 seconds from multiple geographic regions.
* **Target Value:** $\ge 99.90\%$ Uptime.
* **Scope:** Customer Storefront, Browsing, and Checkout APIs.
* **Priority:** `P0`
* **Dependencies:** None.
* **Acceptance / Verification Method:** Monthly uptime calculation: $( \text{Uptime Hours} / \text{Total Hours} ) \times 100$.
* **Approval Status:** `PROPOSED` (See [OQ-017](./08-assumptions-dependencies-open-questions.md#oq-017))

#### NFR-AVL-002: Fault Isolation & Graceful Service Degradation
* **Quality Attribute:** Reliability & Fault Tolerance
* **Requirement Statement:** Failure of a non-critical peripheral service (such as product review submission, email dispatch, or analytics ingestion) must not impair core browsing, cart addition, checkout, or payment capture capabilities.
* **Business Rationale:** Prevents peripheral or third-party outages from crashing the primary revenue-generating commerce funnel.
* **Measurement Method:** Chaos engineering injection simulating service crashes on notification and analytics services.
* **Target Value:** Zero checkout disruption during peripheral service outage.
* **Scope:** System-wide Architecture.
* **Priority:** `P0`
* **Dependencies:** `FEAT-INT-001`.
* **Acceptance / Verification Method:** Intentionally terminate notification service during active load test; verify that customer checkout succeeds and notifications queue asynchronously.
* **Approval Status:** `CONFIRMED`

---

### 2.4 Data Integrity, Consistency & Freshness

#### NFR-INT-001: Zero Tolerance for Inventory Overselling
* **Quality Attribute:** Data Integrity (Strong Consistency)
* **Requirement Statement:** The system must guarantee strong consistency for Available-to-Sell (ATS) inventory allocation. The number of successfully confirmed orders for any SKU must never exceed physical available stock.
* **Business Rationale:** Overselling causes unfulfillable orders, customer cancellation backlash, and chargebacks.
* **Measurement Method:** Automated inventory reconciliation audit comparing physical inward counts against sum of sold units and current on-hand balance.
* **Target Value:** Exactly $0.00\%$ oversell incidents.
* **Scope:** Inventory & Checkout Engines.
* **Priority:** `P0`
* **Dependencies:** `BR-INV-001`, `BR-INV-002`.
* **Acceptance / Verification Method:** Concurrency stress test: 200 simultaneous purchase requests targeting an item with exactly 5 units in stock; verify that exactly 5 orders succeed and 195 receive out-of-stock rejections.
* **Approval Status:** `CONFIRMED`

#### NFR-INT-002: Search & Reporting Data Freshness (Eventual Consistency SLA)
* **Quality Attribute:** Data Consistency (Eventual Consistency)
* **Requirement Statement:** Modifications to catalog metadata, pricing, or product availability must propagate to search indices and customer storefront views within $\le 5\text{ seconds}$ under normal load, and within $\le 60\text{ seconds}$ during bulk catalog updates.
* **Business Rationale:** Ensures customers do not see stale prices or out-of-stock items as available for prolonged intervals.
* **Measurement Method:** Timestamp delta tracking between database catalog commit and appearance in search API response.
* **Target Value:** Standard: $\le 5\text{s}$; Bulk: $\le 60\text{s}$.
* **Scope:** Search Indexing Engine.
* **Priority:** `P1`
* **Dependencies:** `FEAT-SRCH-001`, `FEAT-CAT-001`.
* **Acceptance / Verification Method:** Automated test updating price in admin; polling search API until new price is reflected.
* **Approval Status:** `PROPOSED`

---

### 2.5 Security, Authentication & Data Privacy

#### NFR-SEC-001: Zero Cardholder Data Exposure (PCI-DSS Compliance)
* **Quality Attribute:** Security (Regulatory Compliance)
* **Requirement Statement:** The platform must strictly prohibit storing, logging, or caching raw Primary Account Numbers (PAN), CVVs, or cardholder PINs in application databases or filesystem logs. Card processing must use certified payment gateway tokenization.
* **Business Rationale:** Supports architectural compliance with PCI-DSS Self-Assessment Questionnaire (SAQ A) requirements by delegating card data capture to certified third-party payment gateways; reduces enterprise liability and PCI-DSS audit scope.
* **Measurement Method:** Automated static code analysis, database schema inspections, and dynamic log scanning.
* **Target Value:** Zero instances of raw PAN or CVV in storage.
* **Scope:** Platform-wide.
* **Priority:** `P0`
* **Dependencies:** `BR-SEC-001`, `FEAT-PAY-001`.
* **Acceptance / Verification Method:** Penetration testing and automated regex log scrapers verifying zero PAN/CVV matches.
* **Approval Status:** `CONFIRMED`

#### NFR-SEC-002: Credential Hashing and Session Security
* **Quality Attribute:** Security (Access Control)
* **Requirement Statement:** All user credentials must be stored using modern cryptographic key derivation functions (bcrypt with work factor $\ge 12$ or Argon2id). Administrative sessions must enforce idle timeouts of 15 minutes, with mandatory MFA for privileged roles.
* **Business Rationale:** Prevents credential exposure in the event of database exfiltration.
* **Measurement Method:** Security architecture review and automated credential verification audits.
* **Target Value:** 100% compliance with cryptographic standards; 15-minute administrative session timeout.
* **Scope:** Identity & Authentication Services.
* **Priority:** `P0`
* **Dependencies:** `FEAT-SEC-001`, `FEAT-ADM-001`.
* **Acceptance / Verification Method:** Security code review and automated session expiry testing.
* **Approval Status:** `CONFIRMED`

#### NFR-SEC-003: Personal Data Protection & Privacy Governance (DPDPA 2023)
* **Quality Attribute:** Security & Privacy
* **Requirement Statement:** The system must protect customer Personally Identifiable Information (PII) using AES-256 encryption at rest and TLS 1.3 in transit, support customer data export and anonymization workflows, and redact customer phone numbers/emails from centralized operational logs.
* **Business Rationale:** Provides architectural support for data protection controls aligned with the Indian Digital Personal Data Protection Act (DPDPA 2023); formal regulatory compliance requires organizational policy sign-off and legal validation.
* **Measurement Method:** Privacy audit checklist and automated log masking verification.
* **Target Value:** 100% PII log redaction; encrypted data stores.
* **Scope:** Customer Profile & Logging Systems.
* **Priority:** `P0`
* **Dependencies:** `BR-DAT-001`, `FEAT-CUST-003`.
* **Acceptance / Verification Method:** Verify customer phone numbers and emails appear masked (e.g., `+91-XXXXX-98124`) in application logs.
* **Approval Status:** `CONFIRMED`

---

### 2.6 Observability, Telemetry & Diagnostics

#### NFR-OBS-001: Distributed Traceability and Correlation Identifiers
* **Quality Attribute:** Observability (Distributed Tracing)
* **Requirement Statement:** Every inbound HTTP request, background job, and domain event must carry an immutable, unique Correlation Identifier (Trace ID) propagated across all asynchronous workflows, with structured JSON logging capturing the Trace ID.
* **Business Rationale:** Enables SRE and support staff to trace a customer checkout failure across multiple microservice hops within seconds.
* **Measurement Method:** Log ingestion inspection confirming 100% presence of Correlation ID in production logs.
* **Target Value:** 100% transaction coverage.
* **Scope:** All Services & Event Publishers.
* **Priority:** `P0`
* **Dependencies:** `FEAT-OPS-001`.
* **Acceptance / Verification Method:** Trigger an end-to-end checkout and search centralized logging for the single Trace ID, verifying complete event sequence is returned.
* **Approval Status:** `CONFIRMED`

#### NFR-OBS-002: Real-Time Operational Health Metrics & Alerting
* **Quality Attribute:** Observability (Monitoring)
* **Requirement Statement:** Services must expose standardized health check endpoints (liveness, readiness) and operational metrics (order velocity, payment failure rate, reservation expiration count) refreshed every $\le 15\text{ seconds}$, triggering automated high-severity alerts when error rates exceed $2\%$.
* **Business Rationale:** Proactive anomaly detection prevents prolonged unnoticed commercial degradation.
* **Measurement Method:** Prometheus metric scraping and alert rule evaluation.
* **Target Value:** Metric scrape interval: 15s; Alert dispatch: $< 60\text{s}$ from threshold breach.
* **Scope:** Platform-wide.
* **Priority:** `P0`
* **Dependencies:** `FEAT-OPS-001`.
* **Acceptance / Verification Method:** Simulate simulated payment error spike; verify high-severity alert fires to operations team within 60 seconds.
* **Approval Status:** `CONFIRMED`

---

### 2.7 CI/CD, Release Quality & Deployment Safety

#### NFR-REL-001: Automated Release Verification and Zero-Downtime Deployment
* **Quality Attribute:** Release Quality & Maintainability
* **Requirement Statement:** All software builds must pass automated quality gates (unit test pass rate 100%, line coverage $\ge 80\%$, zero critical static security vulnerabilities) before deployment qualification, and must support rolling or blue-green zero-downtime production deployments.
* **Business Rationale:** Eliminates regression defects and allows releasing bug fixes during business hours without interrupting customer shopping.
* **Measurement Method:** Automated CI pipeline execution logs.
* **Target Value:** 100% automated test pass rate; zero dropped HTTP connections during deployment.
* **Scope:** Build & Deployment Pipelines.
* **Priority:** `P1`
* **Dependencies:** `FEAT-REL-001`.
* **Acceptance / Verification Method:** Execute production deployment during active synthetic user checkout session; verify zero failed transactions.
* **Approval Status:** `CONFIRMED`

---

### 2.8 Backup, Disaster Recovery & Business Continuity

#### NFR-BCM-001: Recovery Point Objective (RPO) and Recovery Time Objective (RTO)
* **Quality Attribute:** Disaster Recovery & Business Continuity
* **Requirement Statement:** The data persistence infrastructure must guarantee automated daily snapshots and continuous transaction write-ahead logging (WAL) ensuring:
  * **Recovery Point Objective (RPO):** $\le 5\text{ minutes}$ (maximum potential committed transaction data loss in catastrophic regional data center destruction).
  * **Recovery Time Objective (RTO):** $\le 2\text{ hours}$ (maximum elapsed time to fully restore operational platform capabilities from backup in a disaster recovery site).
* **Business Rationale:** Guarantees business survival and minimizes financial loss during catastrophic infrastructure events.
* **Measurement Method:** Scheduled bi-annual disaster recovery simulation drill.
* **Target Value:** $\text{RPO} \le 5\text{ minutes}$; $\text{RTO} \le 2\text{ hours}$.
* **Scope:** All Transactional Databases.
* **Priority:** `P0`
* **Dependencies:** `FEAT-BCM-001`.
* **Acceptance / Verification Method:** Disaster recovery tabletop and automated restore drill from backup snapshot to staging cluster.
* **Approval Status:** `PROPOSED` (See [OQ-017](./08-assumptions-dependencies-open-questions.md#oq-017))

---

### 2.9 Usability, Accessibility & Internationalization

#### NFR-ACC-001: Accessibility Standards Compliance
* **Quality Attribute:** Usability & Accessibility
* **Requirement Statement:** All customer-facing digital touchpoints must conform to Web Content Accessibility Guidelines (WCAG) 2.1 Level AA standards, ensuring full keyboard navigability, appropriate color contrast ratios ($\ge 4.5:1$), and complete screen reader support.
* **Business Rationale:** Inclusivity for disabled users and compliance with modern digital accessibility regulations.
* **Measurement Method:** Automated accessibility testing tools (axe-core) combined with manual screen reader evaluation.
* **Target Value:** Zero WCAG 2.1 AA violations.
* **Scope:** Customer Storefront (to be implemented in frontend phase).
* **Priority:** `P1`
* **Dependencies:** None.
* **Acceptance / Verification Method:** Automated axe-core scan on rendered pages.
* **Approval Status:** `CONFIRMED`

#### NFR-LOC-001: National Localization & Date/Time Conventions
* **Quality Attribute:** Localization
* **Requirement Statement:** The platform must display currency formatted in Indian Rupees (INR - ₹) with standard national numbering (Lakhs and Crores where applicable), enforce Indian Standard Time (IST - UTC+05:30) for customer-facing order timestamps, and support national postal PIN codes.
* **Business Rationale:** Natural, trustworthy shopping experience for Indian domestic retail consumers.
* **Measurement Method:** Visual verification of currency and timestamp presentation across customer and administrative interfaces.
* **Target Value:** 100% adherence to INR currency formatting and IST timestamps.
* **Scope:** Storefront & Admin Display.
* **Priority:** `P0`
* **Dependencies:** None.
* **Acceptance / Verification Method:** Verify order history and invoices display currency as ₹ and timestamps in IST.
* **Approval Status:** `PROPOSED`

---

## 3. Summary of Non-Functional Requirements & Metrics

| NFR ID | Category | Metric / Target Parameter | Target Value | Baseline Status |
| :--- | :--- | :--- | :--- | :--- |
| **NFR-PERF-001** | Search Performance | Search Response Latency | $p95 \le 200\text{ms}$; $p99 \le 500\text{ms}$ | `PROPOSED` |
| **NFR-PERF-002** | Checkout Performance | Checkout Submission Latency | $p95 \le 1,000\text{ms}$; $p99 \le 2,500\text{ms}$ | `PROPOSED` |
| **NFR-PERF-003** | Admin Performance | Standard Admin Query Latency | $p95 \le 800\text{ms}$; Bulk $\le 60\text{s}$ | `PROPOSED` |
| **NFR-SCL-001** | Scalability | Peak Order Placement Throughput | 100 OPM (Base); 1,000 OPM (Peak) | `PROPOSED` |
| **NFR-SCL-002** | Scalability | Catalog & Order Data Volume | 50,000 SKUs; 5,000,000 Historical Orders | `CONFIRMED` |
| **NFR-AVL-001** | Availability | Production System Uptime | $\ge 99.90\%$ Unplanned Uptime | `PROPOSED` |
| **NFR-AVL-002** | Reliability | Fault Isolation & Graceful Degradation | Zero checkout impact on peripheral outage | `CONFIRMED` |
| **NFR-INT-001** | Data Integrity | Inventory Overselling Incidents | $0.00\%$ Overselling (Zero tolerance) | `CONFIRMED` |
| **NFR-INT-002** | Data Consistency | Catalog to Search Propagation Freshness | $\le 5\text{ seconds}$ (Standard) | `PROPOSED` |
| **NFR-SEC-001** | Security | Raw Cardholder Data Storage (PCI-DSS)| Zero raw PAN / CVV in database or logs | `CONFIRMED` |
| **NFR-SEC-002** | Security | Credential Security & Session Timeout| bcrypt / Argon2id; 15-min admin timeout | `CONFIRMED` |
| **NFR-SEC-003** | Privacy | PII Protection & Data Privacy (DPDPA)| AES-256 at rest; 100% PII log redaction | `CONFIRMED` |
| **NFR-OBS-001** | Observability | Distributed Correlation Traceability | 100% of transactions carry Trace ID | `CONFIRMED` |
| **NFR-OBS-002** | Observability | Operational Metric Scrape & Alerts | 15s scrape interval; $< 60\text{s}$ alert delay | `CONFIRMED` |
| **NFR-REL-001** | CI/CD Quality | Automated Quality Gates & Deployments | 100% test pass; Zero-downtime rolling | `CONFIRMED` |
| **NFR-BCM-001** | Disaster Recovery | Recovery Point & Recovery Time Obj. | $\text{RPO} \le 5\text{ mins}$; $\text{RTO} \le 2\text{ hours}$ | `PROPOSED` |
| **NFR-ACC-001** | Usability | Digital Accessibility Standard | WCAG 2.1 Level AA Compliance | `CONFIRMED` |
| **NFR-LOC-001** | Localization | Domestic Currency & Timezone | INR (₹) formatting; IST (UTC+05:30) | `PROPOSED` |
