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
| **OBJ-008** | `FEAT-CUST-003` | `FR-CUST-004` | `BR-DAT-001` | `WF-001` | `NFR-SEC-003` | Self-service data export & anonymization; 8-year tax records preserved. | `P1` | `PROPOSED` | `OQ-020` |
| **OBJ-002** | `FEAT-CAT-001` | `FR-CAT-001` | `BR-CAT-001`<br>`BR-CAT-002`<br>`BR-PRC-001` | `WF-002` | `NFR-SCL-002`<br>`NFR-INT-002` | Multi-variant electronics modeling (color, RAM, storage); unique SKUs. | `P0` | `CONFIRMED` | `DEP-005` |
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
| **OBJ-007** | `FEAT-REV-001` | `FR-REV-001` | `BR-REV-001` | `WF-004` | `NFR-ACC-001` | Phase 1 reviews for verified buyers; admin moderation before publication. Photo-review and detailed moderation policy remain open. | `P2` | `CONFIRMED` | `OQ-012` (Option B recorded; detail items open) |
| **OBJ-007** | `FEAT-SUP-001` | `FR-SUP-001` | `BR-SUP-001` | `WF-013` | `NFR-AVL-002` | Support ticket linked to Order ID and AWB with agent resolution history. | `P1` | `CONFIRMED` | None |
| **OBJ-009** | `FEAT-RPT-001` | `FR-RPT-001` | `BR-RPT-001` | `WF-014` | `NFR-PERF-003` | Standard reports plus real-time BI, predictive replenishment and customer cohort retention selected; KPI definitions, refresh, cadence, permissions, exports and validation remain open. | `P1` | `CONFIRMED` | `OQ-018` (Option B recorded; detail items open) |
| **OBJ-008** | `FEAT-AUD-001` | `FR-AUD-001`<br>`FR-AUD-002` | `BR-AUD-001` | `WF-016` | `NFR-SEC-002` | Immutable WORM audit trail logging all privileged administrative actions. | `P0` | `CONFIRMED` | None |
| **OBJ-002** | `FEAT-DAT-001` | `FR-DAT-001` | `BR-CAT-001`<br>`BR-PRC-001` | `WF-002` | `NFR-PERF-003` | Bulk catalog and opening-stock import with schema validation remains in scope; historical customer/order migration is excluded under OQ-019 Option A. | `P1` | `CONFIRMED` | `DEP-005` (import readiness) |
| **OBJ-010** | `FEAT-INT-001` | `FR-INT-001` | `BR-INT-001` | `WF-015` | `NFR-AVL-002` | Circuit breaker fault isolation and exponential backoff on third-party outages. | `P0` | `CONFIRMED` | None |
| **OBJ-008** | `FEAT-SEC-001` | `FR-SEC-001`<br>`FR-SEC-002` | `BR-SEC-001`<br>`BR-SEC-002` | `WF-007` | `NFR-SEC-001`<br>`NFR-SEC-002` | Zero cardholder PAN storage; tokenized checkout; bcrypt credential hashing. | `P0` | `CONFIRMED` | `DEP-001` |
| **OBJ-010** | `FEAT-OPS-001` | `FR-OPS-001` | `BR-OPS-001` | `WF-015` | `NFR-OBS-001`<br>`NFR-OBS-002` | Distributed correlation tracing (Trace ID) and real-time operational alerting. | `P0` | `CONFIRMED` | `OQ-022` |
| **OBJ-010** | `FEAT-REL-001` | `FR-REL-001` | `BR-REL-001` | N/A | `NFR-REL-001` | Automated CI testing gates and zero-downtime rolling production deployments. | `P1` | `CONFIRMED` | `OQ-021` |
| **OBJ-010** | `FEAT-BCM-001` | `FR-BCM-001` | `BR-BCM-001` | N/A | `NFR-BCM-001` | Continuous automated backups ensuring RPO <= 5 mins and RTO <= 2 hours. | `P0` | `CONFIRMED` | `OQ-017` |
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
