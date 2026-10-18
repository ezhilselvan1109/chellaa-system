# Phase 1 BRD Remediation Log & Change Record

| Document Attribute | Details |
| :--- | :--- |
| **Audit Document ID** | BRD-REM-01 |
| **Title** | Comprehensive Remediation Log, Document Modifications & Residual Items Record |
| **Target Repository** | `ezhilselvan1109/chellaa-system` (Branch: `dev`) |
| **Audit Target** | `docs/00-requirements/` (12 Baseline Documents) |
| **Audit Date** | 2026-10-18 |
| **Auditor** | Senior Business Analyst, Requirements Quality Engineer & Documentation Auditor |

---

## 1. Remediation Scope & Governance Principles

This remediation log records all substantive modifications applied to the Chellaa E-Commerce Business Requirements Documentation (BRD) to resolve the 25 defects identified in [brd-audit-report.md](./brd-audit-report.md).

### Governance Invariants Observed
1. **Preservation of Single-Retailer Model:** Zero multi-vendor or marketplace concepts were introduced.
2. **Zero Unauthorized Decision Invention:** Unresolved open questions were maintained as `PROPOSED` or `UNDECIDED`; no policy was finalized without business owner approval.
3. **Identifier Stability:** Existing identifier numbers and prefixes were strictly preserved; only missing referenced IDs were added.
4. **Link Portability:** All local machine `file:///` URLs were completely replaced with relative repository links.
5. **No Premature Architecture:** No microservice definitions, database schemas, or API contracts were added.

---

## 2. Summary of Document Modifications

| Target File | Primary Defects Addressed | Remediation Summary |
| :--- | :--- | :--- |
| **[00-business-overview.md](../00-business-overview.md)** | Portability, Status Consistency | Injected explicit HTML anchor tags (`<a id="..."></a>`), converted links to relative format, and confirmed `OBJ-010` is labeled `PROPOSED` (linked to `OQ-017`). |
| **[01-business-scope.md](../01-business-scope.md)** | Portability, Decision Alignment | Replaced all 9 machine-specific `file:///` URLs with relative links; aligned scope status labels (India `PROPOSED`, INR `PROPOSED`, GST `PROPOSED`, COD `UNDECIDED`). |
| **[02-stakeholders-and-actors.md](../02-stakeholders-and-actors.md)** | Link Portability, Persona Alignment | Fixed relative links; explicitly confirmed `ACT-010` (Guest Shopper) status as `PROPOSED` (linked to `OQ-011`). |
| **[03-business-feature-inventory.md](../03-business-feature-inventory.md)** | Traceability Alignment, Status Labels | Updated related requirement links to map to the expanded set of functional requirements; aligned feature statuses dependent on open questions (`FEAT-CUST-001`, `FEAT-BCM-001`). |
| **[04-functional-requirements.md](../04-functional-requirements.md)** | Critical Definition Gap (17 missing FRs) | Authored complete functional requirement specifications for all 17 missing IDs with Given/When/Then acceptance criteria; fixed `FR-FIN-001` tax calculation specification; replaced `file:///` links. |
| **[05-business-rules.md](../05-business-rules.md)** | Critical Definition Gap (18 missing BRs), Mermaid Syntax | Formally defined all 18 missing business rules (`BR-ADM-001/002`, `BR-CUST-001/002`, `BR-SRCH-001/002`, `BR-PDP-001`, `BR-WSH-001`, `BR-NTF-001`, `BR-REV-001`, `BR-SUP-001`, `BR-RPT-001`, `BR-INT-001`, `BR-OPS-001`, `BR-REL-001`, `BR-BCM-001`, `BR-ADV-001/002`); remediated Mermaid diagram syntax; updated rule summary table. |
| **[06-business-workflows.md](../06-business-workflows.md)** | Link Portability, Exception Paths | Replaced all 4 `file:///` URLs with relative links; clarified alternative path in `WF-007` for late payment arrival when stock is still available; ensured valid rule cross-references. |
| **[07-non-functional-requirements.md](../07-non-functional-requirements.md)** | Portability, Compliance Phrasing | Replaced `file:///` URLs; adjusted compliance statements from "guaranteed legal certification" to "architectural requirement to support compliance controls"; maintained `PROPOSED` status on performance, uptime, and RTO/RPO targets. |
| **[08-assumptions-dependencies-open-questions.md](../08-assumptions-dependencies-open-questions.md)** | Anchor Portability, Status Alignment | Injected explicit HTML anchor tags (`<a id="oq-xxx"></a>`, `<a id="asm-xxx"></a>`); aligned `ASM-007` (returns) to `PROPOSED` status matching `OQ-008`. |
| **[09-requirements-traceability-matrix.md](../09-requirements-traceability-matrix.md)** | Traceability Audit, Orphan Prevention | Synchronized 100% of functional requirements and business rules across the matrix; resolved `FEAT-CAT-002` mapping; verified zero broken links or orphaned requirements. |
| **[10-glossary.md](../10-glossary.md)** | Link & Reference Verification | Verified standard domain definitions; ensured terminology consistency across inventory, order states, and financial documents. |
| **[11-review-and-approval.md](../11-review-and-approval.md)** | Portability, Checkpoint Alignment | Replaced all 24 `file:///` URLs with relative links; updated deliverable checklist and open decision summary to reflect remediated baseline. |

---

## 3. Detailed Trace of Modifications by File

### 3.1 Document 04 (`04-functional-requirements.md`) Additions
To close **DEF-08**, the following formal requirement blocks were authored in full, including actors, triggers, preconditions, main success paths, alternative paths, exception paths, business rules, inputs, outcomes, and Given/When/Then acceptance criteria:
1. `FR-ADM-002`: Administrative Audit Trail & Sensitive Action Logging
2. `FR-CUST-002`: Customer Password Security, Credential Hashing & Lockout
3. `FR-CUST-003`: Customer Delivery Address Book Management (standardized numbering)
4. `FR-CUST-004`: Customer Data Privacy & Statutory Anonymization (DPDPA 2023)
5. `FR-CAT-003`: Product Media & High-Resolution Asset Management
6. `FR-BRD-001`: Brand Catalog Governance & Product Associations
7. `FR-CTG-001`: Hierarchical Parent-Child Category Taxonomy
8. `FR-INV-003`: Real-Time Stock Reservation Lifecycle & Automated Expiration
9. `FR-INV-004`: Low-Stock Thresholds & Automated Replenishment Alerts
10. `FR-FIN-001`: Statutory Tax Calculation Engine (CGST, SGST, IGST)
11. `FR-FIN-002`: Sequential Tax Invoice Generation
12. `FR-FIN-003`: Official Credit Note Generation for Returns & Cancellations
13. `FR-SRCH-002`: Multi-Attribute Faceted Filtering & Search Sorting
14. `FR-PDP-001`: Product Details Page (PDP) Merchandising & Live Stock
15. `FR-CART-001`: Persistent Shopping Cart & Session Cart Merging
16. `FR-CART-002`: Maximum Purchase Quantity per SKU Enforcement
17. `FR-WSH-001`: Customer Wishlist & Price-Drop Monitoring
18. `FR-CHK-002`: Server-Side Total Recalculation & Idempotent Order Placement
19. `FR-ORD-002`: Customer Self-Service Cancellation Prior to Warehouse Dispatch
20. `FR-ORD-003`: Historical Order Snapshot Immutability
21. `FR-PAY-002`: Late Payment Arrival Resolution on Expired Reservations
22. `FR-PAY-003`: Automated Daily Payment Gateway Settlement Reconciliation
23. `FR-PRM-002`: Pro-Rata Line Item Promotional Discount Distribution
24. `FR-SHP-002`: Multi-Attempt Delivery & Return-to-Origin (RTO) Protocol
25. `FR-RET-003`: Automated Refund Remittance to Original Payment Instrument
26. `FR-REV-001`: Verified-Purchase Customer Reviews & Moderation Queue
27. `FR-DAT-001`: Bulk Spreadsheet Catalog & Inventory Data Ingestion
28. `FR-SEC-002`: Brute-Force Defense, Session Security & Credential Protection
29. `FR-OPS-001`: Platform Health Telemetry, Correlation Tracing & Alerts
30. `FR-REL-001`: Automated Release Verification Gates & Deployment Safety
31. `FR-BCM-001`: Disaster Recovery & Automated Database Restoration
32. `FR-ADV-001`: Product Bundles & Accessory Kit Inventory Allocation
33. `FR-ADV-002`: Back-in-Stock Customer Notification Alerts

### 3.2 Document 05 (`05-business-rules.md`) Additions
To close **DEF-09**, the following formal business rules were authored in tabular format with statements, rationales, conditions, outcomes, exceptions, and severity ratings:
1. `BR-ADM-001`: Separation of Duties (Finance Specialist vs Fulfillment Agent)
2. `BR-ADM-002`: Immediate Invalidation of Active Sessions on Account Suspension
3. `BR-CUST-001`: Enterprise-Wide Uniqueness of Customer Mobile and Email
4. `BR-CUST-002`: Delivery Address Immutability for In-Transit Dispatched Consignments
5. `BR-CAT-003`: Mandatory High-Resolution Primary Image for Published Variants
6. `BR-SRCH-001`: Strict Exclusion of Draft and Inactive Products from Search Indices
7. `BR-SRCH-002`: Out-of-Stock Items Remain Searchable with Visual Stockout Badging
8. `BR-PDP-001`: Out-of-Stock Variant Add-to-Cart Action Disable
9. `BR-WSH-001`: Live Catalog Price Evaluation for Wishlist Saved Items
10. `BR-NTF-001`: Transactional Message Precedence over Marketing Opt-Outs
11. `BR-REV-001`: Verified-Buyer Gate for Customer Ratings and Reviews
12. `BR-SUP-001`: 2-Hour SLA for Critical Delivery and Damaged Item Complaints
13. `BR-RPT-001`: Sales Report Reconciliation with Invoiced and Confirmed Ledgers
14. `BR-INT-001`: Zero Silent Data Loss During Third-Party Provider Outages
15. `BR-OPS-001`: Mandatory Correlation Trace ID & PII Redaction in Structured Logs
16. `BR-REL-001`: Automated Testing Quality Gates & Zero-Downtime Deployments
17. `BR-BCM-001`: Disaster Recovery RTO ($\le 2\text{h}$) and RPO ($\le 5\text{m}$) Mandate
18. `BR-ADV-001`: Product Bundle Availability Governed by Lowest Component ATS
19. `BR-ADV-002`: Back-in-Stock Notification Throttling to Prevent Flash Re-Stockout

---

## 4. Policy Decisions Intentionally Maintained as Unresolved

In accordance with Audit Rule 3 ("Do not invent business decisions or change an open question to CONFIRMED without supporting approval evidence"), the following items remain provisionally labeled:
1. **Operating Jurisdiction:** India domestic operations remains `PROPOSED` pending Business Owner sign-off on [OQ-001](../08-assumptions-dependencies-open-questions.md#oq-001).
2. **Transaction Currency:** Indian Rupee (INR) remains `PROPOSED` pending [OQ-002](../08-assumptions-dependencies-open-questions.md#oq-002).
3. **Cash on Delivery (COD):** COD policy remains `UNDECIDED` / `UNDER_REVIEW` (Option B proposed) pending [OQ-006](../08-assumptions-dependencies-open-questions.md#oq-006).
4. **Returns Policy Window:** 7-calendar-day return window remains `PROPOSED` pending [OQ-008](../08-assumptions-dependencies-open-questions.md#oq-008).
5. **Tax Inclusivity:** Tax-inclusive retail pricing remains `PROPOSED` pending [OQ-009](../08-assumptions-dependencies-open-questions.md#oq-009).
6. **Customer Registration Rail:** Mobile-first with SMS OTP remains `PROPOSED` pending [OQ-010](../08-assumptions-dependencies-open-questions.md#oq-010).
7. **Performance & Throughput Targets:** 100 OPM baseline and 1,000 OPM peak remain `PROPOSED` pending [OQ-016](../08-assumptions-dependencies-open-questions.md#oq-016).
8. **Uptime & RTO/RPO Targets:** 99.9% uptime, RPO $\le 5$m, and RTO $\le 2$h remain `PROPOSED` pending [OQ-017](../08-assumptions-dependencies-open-questions.md#oq-017).

---

## 5. Residual Requirements Baseline Status

* **BRD Documentation Phase Lifecycle State:** `PENDING_APPROVAL`
* **Implementation Status:** `NOT_STARTED`
* **Architecture Approval:** `NOT_GRANTED`
* **Next Action Required:** Business Owner review of remediated documentation package and formal completion of approval block in [11-review-and-approval.md](../11-review-and-approval.md).
