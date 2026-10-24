# Phase 2 Architecture Draft Reconciliation Audit — 2026-10-24

> **Status: WORKING REVIEW — NON-BINDING.** This audit records documentation reconciliation and identifier-level checks only. It is not Business Owner sign-off, architecture authorization, implementation approval, test evidence, or permission to promote architecture drafts.

## 1. Governance snapshot

- Phase 1 review: `PENDING_APPROVAL`.
- Requirements baseline: `NOT_APPROVED`.
- Architecture authorization: `NOT_GRANTED`.
- `DEP-002` shipping operations readiness: `OPEN`.
- PR #1 remains draft and unmerged.
- Document 11 Section 7 still has no human reviewer, review date, formal decision, comments, signature, or authorization hash. No such record was fabricated.
- All architecture files remain under `docs/00-requirements/review/`; none has been promoted to `docs/01-architecture/`.

## 2. Canonical workflow mapping

The authoritative workflow inventory is Document 06, `WF-001` through `WF-017`. The ARCH-04 sequence headings contain 17 unique workflow IDs and its workflow traceability matrix has 17 workflow rows. The additional shipment-tracking heading is explicitly a supporting flow for `WF-008`, not a new workflow ID.

| Workflow ID | Canonical title in Document 06 | ARCH-04 alignment |
| :--- | :--- | :--- |
| `WF-001` | Customer Registration & Verification | Customer registration / phone OTP |
| `WF-002` | Product Catalog Management & Publishing | Catalog publishing |
| `WF-003` | Inventory Inwarding & Stock Adjustment | Inventory receiving / stock adjustment |
| `WF-004` | Product Discovery, Search & Filtering | Discovery / search |
| `WF-005` | Cart Management & Price Revalidation | Cart and price/stock revalidation |
| `WF-006` | Checkout & Order Placement | Checkout / atomic stock hold |
| `WF-007` | Payment Processing & Gateway Handling | Payment processing / late webhook recovery |
| `WF-008` | Order Fulfillment, Packing & Dispatch | Pick, pack, shipment assignment and dispatch |
| `WF-009` | Order Cancellation & Inventory Release | Cancellation / stock release |
| `WF-010` | Return, Inspection & Refund Processing | Return, diagnostic inspection and refund |
| `WF-011` | Promotions & Coupon Redemption | Promotion and coupon redemption |
| `WF-012` | Multi-Channel Notification Delivery | Notification dispatch |
| `WF-013` | Customer Support Ticket Resolution | Support ticket intake and resolution |
| `WF-014` | Daily Financial Reconciliation & Reports | Reconciliation and reporting |
| `WF-015` | External Provider Outage Recovery | Provider failure/recovery; no automatic provider failover is inferred |
| `WF-016` | Administrative Access & Privileged Audit | Staff access, RBAC and audit |
| `WF-017` | Verified-Buyer Review Submission & Moderation | Purchase verification, moderation and publication |

The ARCH-12 feature-to-workflow crosswalk was corrected for cart (`WF-005`), inventory/fulfillment (`WF-003`, `WF-006`, `WF-008`, `WF-009`), catalog publishing (`WF-002`), and provider-neutral shipping (`WF-008` / `WF-015`). Wishlist and shipment tracking remain supporting flows, not invented standalone workflow IDs.

## 3. Recorded business-decision alignment

| Decision area | Recorded source decision | Architecture alignment / remaining boundary |
| :--- | :--- | :--- |
| Launch market | India domestic launch; INR and English | Locale and currency assumptions align to the recorded Phase 1 scope. |
| Online payments | Razorpay only | No secondary payment gateway is assumed; `DEP-001` merchant onboarding and production validation remain open. |
| COD | Restricted COD up to ₹10,000, eligible/verified PIN codes, delivery OTP, ₹50 non-refundable fee | Eligibility, fee and delivery controls must remain enforced; operational evidence remains open. |
| Shipping | `ADQ-014` supersedes `ADQ-011` provider-exclusion clauses | Chellaa owns the canonical Shipping Management System/internal Shipment ID. Own-team, direct courier and aggregator lanes are permitted. No named provider, contract, serviceability or lane activation is implied; `DEP-002` remains open. |
| Warehouses | Four planned regional warehouses | Configuration-driven, location-agnostic warehouse and inventory model; actual sites, capacity, tax implications and PIN-code serviceability remain unvalidated. |
| Notifications | SMS, Email and WhatsApp Business API for order lifecycle events | Event-to-channel matrix, retries/fallback order/timing, preferences/consent and provider readiness remain open under `DEP-003`. |
| Reviews | Verified buyers only; admin moderation before publication | Detailed moderation policy, photo reviews, appeals, retention and edit/delete rules remain open. |
| Reporting | Standard reports plus real-time BI, predictive replenishment and customer cohort retention | KPI definitions, data sources, refresh/cadence, permissions, methodology and acceptance criteria remain open. |
| Migration | No legacy customer/order history migration; new catalog/opening-stock import remains in scope | Do not design a mandatory legacy-history migration path. Import quality and opening-stock reconciliation remain open. |
| Retention/privacy | OQ-020 Option A recorded: eight financial years of statutory financial retention plus appropriate profile/marketing-data anonymization | Legal/Security implementation details, exceptions, identity verification, exports and evidence remain open. |
| Capacity / reliability | 100 orders/minute sustained, 1,000 peak, 99.9% uptime, RPO ≤ 5 minutes, RTO ≤ 2 hours | Recorded targets are requirements inputs, not benchmark, availability or recovery-test evidence. |

ARCH-02's local decision table was replaced with a boundary statement because it reused `ADQ-001`–`ADQ-008` for meanings that conflicted with the canonical Executive Decision Register. Business-owner `ADQ` IDs are now governed only by that register; technical architecture decisions remain separate in ARCH-11.

## 4. Traceability checks

The source inventories and current architecture crosswalk were checked at identifier level:

| Identifier family | Source inventory | Architecture crosswalk / presence result | Status |
| :--- | ---: | ---: | :--- |
| Business features (`FEAT`) | 41 | 41/41 unique IDs present in ARCH-12 | Identifier inventory verified; semantic mapping pending |
| Functional requirements (`FR`) | 56 | 56/56 unique IDs listed in ARCH-12 FR crosswalk | Explicit mapping documented; semantic/reverse review pending |
| Business rules (`BR`) | 55 | 55/55 unique IDs listed in ARCH-12 BR crosswalk | Explicit mapping documented; semantic/reverse review pending |
| Business workflows (`WF`) | 17 | 17/17 unique IDs in ARCH-04 sequence headings and 17/17 rows in its workflow matrix | Identifier/title alignment verified; semantic source mapping pending |
| Non-functional requirements (`NFR`) | 18 | 18/18 unique IDs present after correcting `NFR-OPS-001` to canonical `NFR-OBS-001` | Identifier inventory verified; semantic mapping pending |
| Assumptions (`ASM`) | 10 | 10/10 explicitly indexed in ARCH-12 Section 5.3 | Source statuses retained; semantic mapping pending |
| Dependencies (`DEP`) | 6 | 6/6 explicitly indexed in ARCH-12 Section 5.4 | All six remain `OPEN` in the source register; presence is not readiness evidence |
| Open questions (`OQ`) | 22 | 22/22 indexed in ARCH-12 and the requirements RTM | Identifier inventory verified; source decision status remains authoritative |
| Executive decisions (`ADQ`) | 19 | 19/19 indexed in ARCH-12 | Identifier inventory verified; decision authority remains separate |

The source inventories were recalculated by extracting unique IDs from the current baseline documents on this branch. ARCH-04 contains 18 workflow-related headings because the shipment-tracking supporting flow is associated with `WF-008`; there are 17 unique canonical workflow IDs, and the workflow matrix has 17 distinct rows. ARCH-12 now explicitly indexes all 10 assumptions and all 6 dependencies rather than claiming unshown coverage. Its NFR crosswalk was corrected from the nonexistent `NFR-OPS-001` to canonical `NFR-OBS-001`. These are identifier-level checks only; semantic/reverse traceability remains open.

**Important limitation:** Identifier presence and table enumeration do not prove every mapping is semantically correct. ARCH-04's workflow matrix directly maps only a subset of the full FR/BR inventories. Independent bidirectional source-to-architecture verification remains pending. No overall “100% semantic traceability” or “zero orphaned requirements” claim is made.

## 5. Cross-document remediation completed in this pass

- Replaced stale shipping-provider conflict language with the canonical `ADQ-014` provider model across the architecture drafts and reconciliation notes.
- Replaced a specific `SHIPROCKET` partner-code assumption with provider-neutral integration codes.
- Updated ARCH-01 planning-target status and shipping-readiness language; corrected stale claims that owner-selected throughput/availability targets await ratification.
- Corrected ARCH-02's business-decision namespace collision and made technical/legal readiness boundaries explicit.
- Corrected ARCH-04 workflow-matrix titles to match Document 06, restricted its payment actor to Razorpay, and preserved the extra shipment-tracking flow as supporting WF-008 work rather than a new workflow.
- Removed unapproved notification cross-channel fallback assumptions in ARCH-02 and ARCH-08; channel fallback order/timing remains open under OQ-015 / DEP-003.
- Removed Cashfree as a second payment-gateway option in ARCH-04, ARCH-07 and ARCH-08; Razorpay is the only recorded online gateway, with DEP-001 readiness still open.
- Corrected ARCH-12's NFR crosswalk from the nonexistent `NFR-OPS-001` to source-defined `NFR-OBS-001`; added explicit ASM-001–010 and DEP-001–006 source-index tables, preserving source statuses.
- Corrected the invalid `FR-OBS-001` reference to `FR-OPS-001`.
- Updated Document 09 with assumption/dependency/open-question crosswalks and corrected missing `BR-CAT-003` and `NFR-AVL-001` RTM references.
- Updated Document 11 to remove an unsupported 100% traceability claim and stale “decision still proposed” descriptions; the formal approval fields remain blank.
- Marked automated link/anchor validation and independent semantic/reverse traceability review as pending instead of claiming they were executed.

## 6. Remaining gates and blockers

1. **Phase 1 human approval:** The authorized Business Owner/review committee must verify all ten checkpoints in Document 11 and complete Section 7. AI must not populate the human signature/date/decision fields.
2. **Requirements RTM semantic review:** Independently verify every FR/BR/NFR/ASM/DEP/OQ row in both directions against source requirements and objectives. Resolve any semantic orphans or incorrect links.
3. **Architecture semantic review:** Review all 12 architecture drafts for source-to-design consistency, API/event contracts, data ownership, security/privacy details, provider-neutral shipping behavior, multi-warehouse allocation/split-shipment rules, and operational constraints.
4. **Automated document validation:** Relative Markdown link target paths were checked across the 15 reviewed architecture/governance documents: all 31 unique local target paths exist on this branch. Fragment-anchor correctness and Mermaid syntax/render validation remain pending; no claim is made that all anchors or diagrams have been validated.
5. **Operational and provider readiness:** `DEP-001` payment onboarding, `DEP-002` warehouse/shipping/site/PIN/provider readiness, `DEP-003` SMS DLT/WhatsApp registration, and remaining tax/catalog/warehouse dependencies require evidence from accountable owners.
6. **NFR evidence:** Load testing, availability measurement, disaster-recovery drills, security/privacy validation, and notification/provider tests remain unverified.
7. **Architecture authorization and promotion:** Keep architecture authorization `NOT_GRANTED`. Do not move any draft to `docs/01-architecture/` until formal requirements approval and explicit architecture authorization are recorded.

## 7. Final disposition

- Technical/documentation reconciliation: **partially completed; identifier crosswalks expanded and stale conflicts corrected**.
- Requirements baseline: **`NOT_APPROVED`**.
- Phase 1 review: **`PENDING_APPROVAL`**.
- Architecture authorization: **`NOT_GRANTED`**.
- Architecture promotion: **not performed**.
- Implementation, migrations, provider integrations, tests, load tests and DR drills: **not performed or not claimed by this documentation pass**.
