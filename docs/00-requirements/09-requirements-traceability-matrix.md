# Business Requirements Document (BRD)
## Document 09: Requirements Traceability Matrix (RTM)

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-09 |
| **Document Title** | End-to-End Requirements Traceability Matrix (RTM) |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.0.0-DRAFT |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Traceability Architecture & Coverage Verification

The Requirements Traceability Matrix (RTM) validates bidirectional alignment across the requirements baseline:
1. **Forward Traceability:** Ensures every Business Objective is decomposed into specific Features, Functional Requirements, Business Rules, and testable Workflows with zero orphaned concepts.
2. **Backward Traceability:** Ensures every Functional Requirement, Non-Functional Requirement, and Business Rule traces back to an approved Business Objective, preventing scope creep.
3. **Uncertainty Traceability:** Maps every Open Question (`OQ-xxx`) and Dependency (`DEP-xxx`) to the exact functional capabilities it impacts.

---

## 2. Master Requirements Traceability Matrix

| Objective ID | Feature ID | Functional Requirement ID | Applicable Business Rules | Related Workflow ID | Applicable NFR ID | Acceptance Criteria Summary | Priority | Approval Status | Key Dependencies & Open Questions |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :---: | :---: | :--- |
| **OBJ-002** | `FEAT-ADM-001` | `FR-ADM-001`<br>`FR-ADM-002` | `BR-ADM-001`<br>`BR-ADM-002` | `WF-016` | `NFR-SEC-002` | Admin can provision/suspend staff; least privilege strictly enforced. | `P0` | `CONFIRMED` | `DEP-006` |
| **OBJ-008** | `FEAT-ADM-002` | `FR-AUD-001`<br>`FR-AUD-002` | `BR-AUD-001` | `WF-016` | `NFR-SEC-002`<br>`NFR-OBS-001` | Immutable WORM audit trail logs all privileged mutations with reason code. | `P0` | `CONFIRMED` | None |
| **OBJ-001** | `FEAT-CUST-001` | `FR-CUST-001`<br>`FR-CUST-003` | `BR-CUST-001`<br>`BR-SEC-002` | `WF-001` | `NFR-SEC-002`<br>`NFR-SEC-003` | Mobile number signup verified via 6-digit SMS OTP within 5-min TTL. | `P0` | `CONFIRMED` | `DEP-003`, `OQ-010` |
| **OBJ-001** | `FEAT-CUST-002` | `FR-CUST-002` | `BR-CUST-002`<br>`BR-SHP-001` | `WF-001`<br>`WF-006` | `NFR-LOC-001` | Store multiple addresses; PIN code validated against courier registry. | `P0` | `CONFIRMED` | `DEP-002` |
| **OBJ-008** | `FEAT-CUST-003` | `FR-CUST-004` | `BR-DAT-001` | `WF-001` | `NFR-SEC-003` | Self-service data export and profile anonymization while retaining statutory financial records for eight financial years. | `P1` | `PROPOSED` | Legal/Security review of detailed privacy controls and acceptance evidence; OQ-020 Option A is confirmed |
| **OBJ-002** | `FEAT-CAT-001` | `FR-CAT-001` | `BR-CAT-001`<br>`BR-CAT-002`<br>`BR-CAT-003`<br>`BR-PRC-001` | `WF-002` | `NFR-SCL-002`<br>`NFR-INT-002` | Multi-variant electronics modeling (color, RAM, storage); unique SKUs. | `P0` | `CONFIRMED` | `DEP-005` |
| **OBJ-002** | `FEAT-CAT-002` | `FR-CAT-003` | `BR-CAT-001` | `WF-002` | `NFR-PERF-001` | High-resolution media upload; primary thumbnail designation; CDN delivery. | `P1` | `CONFIRMED` | `DEP-005` |
| **OBJ-002** | `FEAT-CTG-002` | `FR-CAT-002`<br>`FR-CTG-002` | `BR-CTG-002` | `WF-002` | `NFR-SCL-002` | Mandatory category specification schemas validated before publish. | `P1` | `CONFIRMED` | `DEP-005` |
| **OBJ-002** | `FEAT-BRD-001` | `FR-BRD-001` | `BR-BRD-001` | `WF-002` | `NFR-SCL-002` | Brand identity management; brands linked to active products cannot be deleted. | `P0` | `CONFIRMED` | `DEP-005` |
| **OBJ-002** | `FEAT-CTG-001` | `FR-CTG-001` | `BR-CTG-001` | `WF-002` | `NFR-SCL-002` | Hierarchical parent-child category tree; circular references blocked. | `P0` | `CONFIRMED` | None |
| **OBJ-003** | `FEAT-INV-001` | `FR-INV-001` | `BR-INV-001`<br>`BR-INV-002`<br>`BR-INV-004` | `WF-003`<br>`WF-005` | `NFR-INT-001`<br>`NFR-AVL-002` | Zero overselling; warehouse-scoped ATS computed as On-Hand - Reserved - Safety; warehouse locations and regional allocation are configuration-driven. | `P0` | `CONFIRMED` | `DEP-006`, `OQ-007` (site validation open) |
| **OBJ-003** | `FEAT-INV-002` | `FR-INV-002`<br>`FR-INV-003` | `BR-INV-003`<br>`BR-PAY-002` | `WF-006`<br>`WF-007` | `NFR-INT-001`<br>`NFR-PERF-002` | Atomic reservation on checkout with automated 15-minute expiration reaper. | `P0` | `CONFIRMED` | None |
| **OBJ-003** | `FEAT-INV-003` | `FR-INV-004` | `BR-INV-004` | `WF-003` | `NFR-OBS-002` | Automated low-stock replenishment exception alerts when ATS < Threshold. | `P1` | `PROPOSED` | None |
| **OBJ-001** | `FEAT-PRC-001` | `FR-PRC-001` | `BR-PRC-001`<br>`BR-PRC-002`<br>`BR-CHK-002` | `WF-002`<br>`WF-005` | `NFR-LOC-001` | Selling Price <= MRP at all times; cart revalidation notifies on price delta. | `P0` | `CONFIRMED` | `ASM-003`, `OQ-009` |
| **OBJ-008** | `FEAT-FIN-001` | `FR-FIN-001` | `BR-FIN-001` | `WF-006` | `NFR-LOC-001` | GST calculation (CGST/SGST/IGST); tax-inclusive storefront price display. | `P0` | `CONFIRMED` | `DEP-004`, `OQ-009` |
| **OBJ-008** | `FEAT-FIN-002` | `FR-FIN-002`<br>`FR-FIN-003` | `BR-FIN-002`<br>`BR-FIN-003` | `WF-007`<br>`WF-010` | `NFR-SEC-003` | Monotonic sequential tax invoices and credit notes generated in PDF. | `P0` | `CONFIRMED` | `DEP-004` |
| **OBJ-001** | `FEAT-SRCH-001` | `FR-SRCH-001`<br>`FR-SRCH-002` | `BR-SRCH-001`<br>`BR-SRCH-002` | `WF-004` | `NFR-PERF-001`<br>`NFR-INT-002` | Typo-tolerant search across titles, specs; faceted filtering in <= 200ms. | `P0` | `CONFIRMED` | None |
| **OBJ-001** | `FEAT-PDP-001` | `FR-PDP-001` | `BR-PDP-001` | `WF-004` | `NFR-PERF-001` | Interactive variant switcher updates price, images, and live stock in real-time. | `P0` | `CONFIRMED` | None |
| **OBJ-001** | `FEAT-CART-001` | `FR-CART-001`<br>`FR-CART-002` | `BR-CART-001`<br>`BR-CART-002`<br>`BR-CHK-002` | `WF-005` | `NFR-PERF-002` | Persistent user cart; guest cart merge on login; max purchase limits enforced. | `P0` | `CONFIRMED` | `OQ-011` |
| **OBJ-001** | `FEAT-WSH-001` | `FR-WSH-001` | `BR-WSH-001` | `WF-005` | `NFR-PERF-001` | Customer wishlist with real-time price change and stock status flags. | `P1` | `CONFIRMED` | None |
| **OBJ-001** | `FEAT-CHK-001` | `FR-CHK-001`<br>`FR-CHK-002` | `BR-CHK-001`<br>`BR-CHK-002`<br>`BR-INV-001` | `WF-006` | `NFR-PERF-002`<br>`NFR-INT-001` | Server-side total calculation; idempotency key prevents duplicate orders. | `P0` | `CONFIRMED` | `OQ-006` |
| **OBJ-004** | `FEAT-ORD-001` | `FR-ORD-001`<br>`FR-ORD-002`<br>`FR-ORD-003` | `BR-ORD-001`<br>`BR-ORD-002`<br>`BR-ORD-003`<br>`BR-ORD-004` | `WF-008`<br>`WF-009` | `NFR-SCL-001` | Validated state machine transitions; customer cancellation blocked once packed. | `P0` | `CONFIRMED` | None |
| **OBJ-005** | `FEAT-PAY-001` | `FR-PAY-001`<br>`FR-PAY-002` | `BR-PAY-001`<br>`BR-PAY-002`<br>`BR-PAY-003` | `WF-007` | `NFR-SEC-001`<br>`NFR-AVL-002` | Idempotent HMAC webhook ingestion; auto-refund if late payment on expired stock. | `P0` | `CONFIRMED` | `DEP-001`, `OQ-003`, `OQ-004` |
| **OBJ-005** | `FEAT-PAY-002` | `FR-PAY-003` | `BR-PAY-003` | `WF-014` | `NFR-SCL-002` | Daily automated reconciliation of gateway settlement statements vs orders. | `P1` | `CONFIRMED` | `DEP-001` |
| **OBJ-001** | `FEAT-PRM-001` | `FR-PRM-001`<br>`FR-PRM-002` | `BR-PRM-001`<br>`BR-PRM-002` | `WF-011` | `NFR-PERF-002` | Strict coupon non-stacking; proportional pro-rata line item discount distribution. | `P1` | `CONFIRMED` | None |
| **OBJ-006** | `FEAT-SHP-001` | `FR-SHP-001`<br>`FR-SHP-002` | `BR-SHP-001`<br>`BR-SHP-002` | `WF-008` | `NFR-AVL-002` | Provider-neutral shipment lifecycle for own-team, direct-courier and aggregator lanes; canonical internal Shipment ID; validated booking/tracking events and audit history. | `P0` | `CONFIRMED` | `DEP-002`, `OQ-005` (provider/site readiness remains open) |
| **OBJ-007** | `FEAT-RET-001` | `FR-RET-001`<br>`FR-RET-003` | `BR-RET-001`<br>`BR-RET-002` | `WF-010` | `NFR-AVL-002` | 7-day return window; reverse pickup; warehouse physical IMEI inspection. | `P1` | `CONFIRMED` | `DEP-002`, `OQ-008` |
| **OBJ-005** | `FEAT-RET-002` | `FR-RET-002` | `BR-RET-003`<br>`BR-FIN-003` | `WF-009`<br>`WF-010` | `NFR-SEC-001` | Automated refund remitted to original payment instrument; Credit Note linked. | `P0` | `CONFIRMED` | `DEP-001` |
| **OBJ-006** | `FEAT-NTF-001` | `FR-NTF-001` | `BR-NTF-001` | `WF-012` | `NFR-AVL-002` | SMS, Email and WhatsApp Business API channel scope selected for order lifecycle; event matrix, retries/fallback and provider readiness remain open. | `P0` | `CONFIRMED` | `DEP-003`, `OQ-015` (selection recorded; details open) |
| **OBJ-007** | `FEAT-REV-001` | `FR-REV-001` | `BR-REV-001` | `WF-017` | `NFR-ACC-001` | Phase 1 reviews for verified buyers; admin moderation before publication. Photo-review and detailed moderation policy remain open. | `P2` | `CONFIRMED` | `OQ-012` (Option B recorded; detail items open) |
| **OBJ-007** | `FEAT-SUP-001` | `FR-SUP-001` | `BR-SUP-001` | `WF-013` | `NFR-AVL-002` | Support ticket linked to Order ID and AWB with agent resolution history. | `P1` | `CONFIRMED` | None |
| **OBJ-009** | `FEAT-RPT-001` | `FR-RPT-001` | `BR-RPT-001` | `WF-014` | `NFR-PERF-003` | Standard reports plus real-time BI, predictive replenishment and customer cohort retention selected; KPI definitions, refresh, cadence, permissions, exports and validation remain open. | `P1` | `CONFIRMED` | `OQ-018` (Option B recorded; detail items open) |
| **OBJ-008** | `FEAT-AUD-001` | `FR-AUD-001`<br>`FR-AUD-002` | `BR-AUD-001` | `WF-016` | `NFR-SEC-002` | Immutable WORM audit trail logging all privileged administrative actions. | `P0` | `CONFIRMED` | None |
| **OBJ-002** | `FEAT-DAT-001` | `FR-DAT-001` | `BR-CAT-001`<br>`BR-PRC-001` | `WF-002` | `NFR-PERF-003` | Bulk catalog and opening-stock import with schema validation remains in scope; historical customer/order migration is excluded under OQ-019 Option A. | `P1` | `CONFIRMED` | `DEP-005` (import readiness) |
| **OBJ-010** | `FEAT-INT-001` | `FR-INT-001` | `BR-INT-001` | `WF-015` | `NFR-AVL-002` | Circuit breaker fault isolation and exponential backoff on third-party outages. | `P0` | `CONFIRMED` | None |
| **OBJ-008** | `FEAT-SEC-001` | `FR-SEC-001`<br>`FR-SEC-002` | `BR-SEC-001`<br>`BR-SEC-002` | `WF-007` | `NFR-SEC-001`<br>`NFR-SEC-002` | Zero cardholder PAN storage; tokenized checkout; bcrypt credential hashing. | `P0` | `CONFIRMED` | `DEP-001` |
| **OBJ-010** | `FEAT-OPS-001` | `FR-OPS-001` | `BR-OPS-001` | `WF-015` | `NFR-OBS-001`<br>`NFR-OBS-002` | Distributed correlation tracing (Trace ID) and real-time operational alerting. | `P0` | `CONFIRMED` | `OQ-022` |
| **OBJ-010** | `FEAT-REL-001` | `FR-REL-001` | `BR-REL-001` | N/A | `NFR-REL-001` | Automated CI testing gates and zero-downtime rolling production deployments. | `P1` | `CONFIRMED` | `OQ-021` |
| **OBJ-010** | `FEAT-BCM-001` | `FR-BCM-001` | `BR-BCM-001` | N/A | `NFR-AVL-001`<br>`NFR-BCM-001` | 99.9% uptime target plus automated backups with RPO <= 5 mins and RTO <= 2 hours; evidence remains unverified. | `P0` | `CONFIRMED` | `OQ-017` |
| **OBJ-001** | `FEAT-ADV-001` | `FR-ADV-001` | `BR-ADV-001` | `WF-002`<br>`WF-005` | `NFR-PERF-002` | Optional configurable electronics bundles with discounted pricing and atomic component-stock validation; remains a proposed P3 capability. | `P3` | `PROPOSED` | None |
| **OBJ-001** | `FEAT-ADV-002` | `FR-ADV-002` | `BR-ADV-002` | `WF-003`<br>`WF-012` | `NFR-AVL-002` | Automated customer notifications dispatched upon warehouse stock replenishment. | `P2` | `PROPOSED` | None |

---

## 3. Scope Boundary & Anti-Scope Traceability

To ensure that strictly excluded features do NOT silently emerge during implementation, the following Anti-Scope mapping is maintained:

| Excluded Capability | Category | Reason for Exclusion | Architectural Protection Mechanism |
| :--- | :--- | :--- | :--- |
| **Third-Party Seller Registration** | Marketplace Feature | Single enterprise retail owner model. | No vendor registration APIs or tenant tables exist in data requirements. |
| **Vendor Portals & Dashboards** | Multi-Tenancy | Only internal enterprise staff operate administrative tools. | Single-tenant RBAC permissions; zero multi-vendor data partitioning. |
| **Multi-Vendor "Buy Box" / Listings** | Marketplace Merchandising | Single SKU has exactly one price and one owner. | Catalog schema links 1 SKU to 1 parent product and 1 enterprise owner. |
| **Marketplace Commissions** | Financial Accounting | 100% of retail revenue belongs to the enterprise owner. | Zero commission calculation or take-rate deduction logic in payment flows. |
| **Vendor Payouts & Escrow** | Banking Integration | No funds disbursed to third-party bank accounts. | Payment gateway disbursements route strictly to enterprise merchant account. |
| **Buyer-Seller Dispute Mediation** | Customer Care | Support is handled directly between customer and enterprise. | Support ticketing is internal-only; no multi-party arbitration workflows. |

---

## 4. Assumption, Dependency and Open-Question Traceability

> This section supplements the capability-level matrix above. The source register in Document 08 remains authoritative for the wording and current status of each ASM, DEP and OQ. A recorded owner selection is not the same as implementation readiness or formal baseline approval.

### 4.1 Assumptions (ASM)

| ID | Source assumption | Linked capabilities / requirements | Source status | Validation / follow-up |
| :--- | :--- | :--- | :--- | :--- |
| ASM-001 | Primary operational country is India. | OBJ-001, NFR-LOC-001, FEAT-SHP-001 | `PROPOSED` | Formal confirmation by Enterprise Business Owner. |
| ASM-002 | Transaction currency is Indian Rupee (INR - ₹) exclusively for Phase 1. | NFR-LOC-001, FEAT-FIN-001 | `PROPOSED` | Commercial policy sign-off. |
| ASM-003 | Retail display prices are tax-inclusive (MRP / Gross Selling Price). | OQ-009, FEAT-FIN-001, FEAT-PRC-001 | `PROPOSED` | Legal / Tax Advisor review. |
| ASM-004 | Initial planning model uses four warehouse locations with regional inventory; the central architecture must remain location-agnostic and configuration-driven. Exact sites and serviceable areas are not yet validated. | OQ-007, FEAT-INV-001, FEAT-SHP-001, DEP-002 | `OWNER_DECISION_RECORDED; SITE_VALIDATION_OPEN` | Validate each actual warehouse address, state/GST implications, inventory plan, staffing/capacity and serviceability before launch. |
| ASM-005 | Pre-orders and backorders are out of scope for the baseline release. | OQ-014, FEAT-INV-001, FR-INV-001 | `CONFIRMED` | Commercial merchandising policy review. |
| ASM-006 | Digital customer identity is primarily tied to mobile phone numbers. | OQ-010, FEAT-CUST-001, FR-CUST-001 | `PROPOSED` | Customer UX persona review. |
| ASM-007 | A standard 7-calendar-day return window applies to consumer electronics. | OQ-008, FEAT-RET-001, FR-RET-001 | `PROPOSED` | Business Owner policy sign-off (subject to [OQ-008](#oq-008)). |
| ASM-008 | External payment gateway will handle all cardholder credential entry (Hosted / Iframe). | OQ-004, FEAT-PAY-001, FR-PAY-001 | `CONFIRMED` | Security & Gateway contract review. |
| ASM-009 | Chellaa will own its Shipping Management System and may fulfil shipments through its own delivery team, directly integrated courier partners, and a shipping aggregator. | OQ-005, FEAT-SHP-001, FR-SHP-001, DEP-002 | `PROPOSED` | Validate the provider-neutral shipment lifecycle, provider selection/routing rules, commercial terms, integrations, and end-to-end tests. |
| ASM-010 | Cash on Delivery (COD) will be restricted to orders under ₹10,000 and verified PIN codes. | FEAT-AUD-001, FEAT-OPS-001, BR-AUD-001 | `PROPOSED` | Finance & Risk assessment. |

### 4.2 Dependencies (DEP)

| ID | Source dependency | Linked capabilities / requirements | Source status | Required follow-up |
| :--- | :--- | :--- | :--- | :--- |
| DEP-001 | Razorpay Merchant Agreement | FEAT-PAY-001, FR-PAY-001, FR-PAY-003 | `OPEN` | Initiate provider RFP immediately; evaluate sandbox APIs in parallel. |
| DEP-002 | Shipping Operations Readiness | FEAT-SHP-001, FR-SHP-001, FR-SHP-002, FEAT-RET-001 | `OPEN` | Select and validate pilot areas and providers; assign operational ownership; confirm provider contracts, API credentials, serviceability, fees/SLAs, COD/remittance, returns, support and fallback; run end-to-end tests before enabling each lane. |
| DEP-003 | Telecom SMS DLT Registration & WhatsApp Business API | FEAT-CUST-001, FEAT-NTF-001, FR-CUST-001, FR-NTF-001 | `OPEN` | Register enterprise entity and SMS templates on commercial telecom DLT portal immediately. |
| DEP-004 | Enterprise GSTIN & Invoicing Numbering Scheme | FEAT-FIN-001, FR-FIN-001 | `OPEN` | Obtain legal corporate tax opinion on HSN rate codes and sequential invoice numbering syntax. |
| DEP-005 | Master Electronics Catalog & Brand Specifications | FEAT-CAT-001, FEAT-DAT-001, FR-CAT-001, FR-DAT-001 | `OPEN` | Establish structured ingestion spreadsheet templates for supplier distributors. |
| DEP-006 | Physical Warehouse Infrastructure & Barcode Hardware | FEAT-INV-001, FR-INV-001, WF-003 | `OPEN` | Define standardized SKU barcode symbologies (Code 128 / EAN-13) before inwarding stock. |

### 4.3 Open Questions and Recorded Selections (OQ)

| ID | Source question | Linked capabilities / requirements | Source status | Recorded selection / remaining detail |
| :--- | :--- | :--- | :--- | :--- |
| OQ-001 | Operating Country Scope | OBJ-001, NFR-LOC-001, FEAT-SHP-001 | `CONFIRMED` | Owner-selected Option A: Domestic India only at launch |
| OQ-002 | Currencies & Languages | NFR-LOC-001, FEAT-FIN-001 | `CONFIRMED` | Owner-selected Option A: INR (₹) and English only |
| OQ-003 | Mandatory Payment Methods | FEAT-PAY-001, FEAT-CHK-001, OQ-006 | `CONFIRMED` | Owner-selected Option C: UPI, cards, net banking, plus COD under OQ-006 policy |
| OQ-004 | Payment Gateway Selection | FEAT-PAY-001, FR-PAY-001, DEP-001 | `CONFIRMED` | Owner-selected single gateway: Razorpay only |
| OQ-005 | Shipping Management Approach | FEAT-SHP-001, FR-SHP-001, FR-SHP-002, DEP-002 | `CONFIRMED` | Owner-selected Option C: own delivery team, direct courier partners and shipping aggregator permitted; Chellaa owns canonical Shipping Management System; provider/lane readiness remains open |
| OQ-006 | Cash on Delivery (COD) Policy | FEAT-PAY-001, FEAT-CHK-001, FR-CHK-001 | `CONFIRMED` | Owner-selected Option B: ₹10,000 cap, delivery OTP, ₹50 fee |
| OQ-007 | Warehouse Launch Topology | FEAT-INV-001, FEAT-SHP-001, DEP-002, DEP-006 | `OWNER_DECISION_RECORDED; SITE_VALIDATION_OPEN` | Four initial warehouse locations with regional inventory; exact sites/PIN codes pending validation; generic configurable design |
| OQ-008 | Returns & Inspection Policy | FEAT-RET-001, FEAT-RET-002, FR-RET-001 | `CONFIRMED` | Owner-selected Option B: 7-day return/refund with diagnostic gate |
| OQ-009 | Tax Structure & Display Prices | FEAT-FIN-001, FEAT-PRC-001, FR-FIN-001 | `CONFIRMED` | Owner-selected Option A: GST-inclusive display |
| OQ-010 | Customer Verification Method | FEAT-CUST-001, FR-CUST-001, DEP-003 | `CONFIRMED` | Owner-selected Option A: mobile SMS OTP; email optional |
| OQ-011 | Guest Checkout Support | FEAT-CART-001, FEAT-CHK-001, FR-CHK-001, WF-006 | `CONFIRMED` | Owner-selected Option B: guest checkout with phone-OTP auto-account |
| OQ-012 | Customer Reviews & Ratings | FEAT-REV-001, FR-REV-001, WF-017 | `OWNER_DECISION_RECORDED` | Option B: verified buyers only; admin moderation required before publication |
| OQ-013 | Loyalty & Store Credit | Phase 1 anti-scope: loyalty/store credit excluded | `CONFIRMED` | Out of scope for Phase 1 baseline |
| OQ-014 | Pre-orders & Backorders | FEAT-INV-001, FR-INV-001, WF-003 | `CONFIRMED` | Out of scope (Strict non-negative stock) |
| OQ-015 | Notification Channels | FEAT-NTF-001, FR-NTF-001, DEP-003, WF-012 | `OWNER_DECISION_RECORDED` | Option B: SMS, Email and WhatsApp Business API for order lifecycle events; fallback/event matrix open |
| OQ-016 | Peak Traffic & Order Capacity | NFR-SCL-001, FEAT-OPS-001, WF-015 | `CONFIRMED` | Owner-selected Option B: 100 OPM sustained; 1,000 OPM peak |
| OQ-017 | Availability, RTO & RPO Targets | NFR-AVL-001, NFR-BCM-001, FEAT-BCM-001, WF-015 | `CONFIRMED` | Owner-selected Option A: 99.9% uptime; RPO ≤ 5m; RTO ≤ 2h |
| OQ-018 | Required Business Reports | FEAT-RPT-001, FR-RPT-001, WF-014 | `OWNER_DECISION_RECORDED` | Option B: standard reports + real-time BI, predictive replenishment and customer cohort retention |
| OQ-019 | Legacy Data Migration Scope | FEAT-DAT-001, FR-DAT-001, DEP-005 | `OWNER_DECISION_RECORDED` | Option A: no legacy customer/order history migration; bulk catalog/opening-stock import remains in scope |
| OQ-020 | Data Retention & Privacy (DPDPA) | FEAT-CUST-003, FR-CUST-004, BR-DAT-001 | `CONFIRMED` | 8-year tax retention; anonymize profile PII |
| OQ-021 | Release Approval Governance | FEAT-REL-001, FR-REL-001, NFR-REL-001 | `CONFIRMED` | Automated CI with manual release gate |
| OQ-022 | Operational Incident Protocols | FEAT-OPS-001, FR-OPS-001, WF-015 | `CONFIRMED` | Automated Slack/SMS alerts for $> 2\%$ error |

### 4.4 Coverage Gate

- Source inventory counts: 41 FEAT IDs, 56 FR IDs, 55 BR IDs, 17 WF IDs, 18 NFR IDs, 10 ASM IDs, 6 DEP IDs and 22 OQ IDs.
- Identifier-level check: FEAT 41/41, FR 56/56, BR 55/55 after adding BR-CAT-003, WF 17/17, NFR 18/18 after adding NFR-AVL-001, DEP 6/6, ASM 10/10 and OQ 22/22 are explicitly indexed in this RTM.
- These are identifier-level checks only. The requirements baseline remains NOT_APPROVED until a reviewer verifies every mapping in both directions, checks the source links, and completes the formal approval record in Document 11.