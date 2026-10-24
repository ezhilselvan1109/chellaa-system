# Phase 1 Requirements Baseline Closure — Current-State Audit and Action Register

> **Status: WORKING AUDIT — NOT AN APPROVAL RECORD.**
>
> This document records the findings and closure work required before Phase 1 can be approved. It does not approve the BRD, authorize architecture, close operational dependencies, or claim that performance/security tests have run. It is maintained on branch `docs/phase1-baseline-closure` and records the four option selections explicitly supplied in the current conversation on 2026-10-24. This is a targeted closure update, not a full-package audit or approval record.

## 1. Governance state

| Gate | Current state | Evidence required to close |
|---|---|---|
| Phase 1 review | `PENDING_APPROVAL` | Authorized Business Owner review and recorded decision in `11-review-and-approval.md` |
| Requirements baseline | `NOT_APPROVED` | Requirements review, reconciled traceability/statuses, genuine sign-off |
| Architecture authorization | `NOT_GRANTED` | Phase 1 approval plus required technical/owner authorization |
| Software implementation | `NOT_STARTED` | Approved architecture and explicit implementation gate |
| DEP-002 shipping readiness | `OPEN` | Operational/provider/serviceability and end-to-end evidence listed in Document 08 |

**Rule:** Do not populate names, signatures, dates, checkboxes, hashes, test results or approvals unless the authorized person/evidence actually supplies them.

## 2. Current business-question status snapshot

Document 08 contains 22 OQs. After the selections recorded on 2026-10-24, the status distribution is:

- **17 marked `CONFIRMED`:** OQ-001–006, OQ-008–011, OQ-013–014, OQ-016–017, OQ-020–022.
- **4 with `OWNER_DECISION_RECORDED`:** OQ-012, OQ-015, OQ-018 and OQ-019. Their selected business scope is recorded; the detailed follow-up items below remain open where noted.
- **1 with a recorded planning decision and operational validation still open:** OQ-007 (four initial warehouses and regional inventory; actual sites/PIN codes not validated).

This is a documentation status snapshot, not a claim that the full BRD baseline is approved. Recorded option selections do not close operational dependencies, validate implementation, or replace formal approval in Document 11.

## 3. Decisions and unresolved questions

| ID | Current finding | Required action | Owner / evidence |
|---|---|---|---|
| OQ-007 / ASM-004 | Four initial warehouses with regional inventory are recorded; actual sites, addresses and serviceable PIN codes remain unvalidated. | Validate real sites, capacity, tax/legal implications, stock distribution and serviceability. Keep the central design location-agnostic/configuration-driven. | Operations/Finance evidence; DEP-002 remains open |
| OQ-012 / ADQ-016 | **Option B selected:** Phase 1 reviews for verified buyers only, with mandatory admin moderation before publication. | Specify moderation criteria, roles/permissions, appeals/rejection handling, photo-review policy, retention, abuse escalation and review edit/delete rules. | Business Owner / Product / Security |
| OQ-015 / ADQ-017 | **Option B selected:** SMS, Email and WhatsApp Business API for order lifecycle events. | Specify event-to-channel matrix, mandatory channels per event, retry/fallback order and timing, consent/preference handling where applicable, templates and delivery targets. Validate DEP-003 provider registration and channel tests. | Business Owner / Operations / Marketing; DEP-003 remains open |
| OQ-018 / ADQ-018 | **Option B selected:** standard daily reports plus real-time BI, predictive replenishment and customer cohort retention analysis. | Specify KPI definitions/formulas, data sources, refresh latency, cadence, permissions, exports, retention, predictive methodology and acceptance/validation criteria. | Business Owner / Finance / Product / Data |
| OQ-019 / ADQ-019 | **Option A selected:** no legacy customer/order history migration for initial launch; new catalog and opening stock use the separate bulk import capability. | Keep historical migration out of the current baseline. Validate catalog data quality and opening-stock reconciliation as separate DEP-005/inventory readiness work; reopen migration only through change control if a concrete legacy dataset is identified and approved. | Business Owner / Merchandising / Data owner; DEP-005 remains open |

### Other recorded owner selections that remain subject to baseline sign-off

- Domestic India launch; INR and English only.
- Online payments through Razorpay only; COD follows the separately recorded restriction.
- COD up to ₹10,000, delivery OTP and ₹50 non-refundable convenience fee.
- Seven-day return/refund/replacement policy with diagnostic inspection and serial/IMEI verification.
- GST-inclusive storefront prices with tax breakdown.
- Mobile-first SMS OTP; email optional.
- 100 orders/minute sustained and 1,000 orders/minute peak targets.
- 99.9% uptime, RPO ≤ 5 minutes and RTO ≤ 2 hours.
- Guest checkout with automatic account creation after mobile OTP verification.
- India-wide delivery target with phased activation; own delivery team, direct courier integrations and aggregator are permitted.
- Four initial warehouse locations and regional inventory are planning inputs; exact sites, service areas and serviceability are not validated.

The four selections above are business-scope decisions recorded in the current working branch. They do not by themselves approve the overall requirements baseline or architecture.

## 4. Cross-document consistency findings

### 4.1 Stale audit and decision-register summaries

1. `review/brd-final-quality-audit.md` is dated 2026-10-18 and says 17 of 22 questions remain provisional / only five are confirmed. Treat it as a historical snapshot, not the current closure verdict.
2. `review/executive-decision-register.md` now indexes ADQ-001 through ADQ-019. ADQ-016 through ADQ-019 record the selections for OQ-012, OQ-015, OQ-018 and OQ-019.
3. ADQ-011 remains historical for delivery-provider mix and is superseded by ADQ-014; preserve that distinction.
4. OQ-007 and ASM-004 remain aligned to four initial warehouses with regional inventory; actual sites/PIN codes remain unvalidated.

### 4.2 Remaining traceability and specification work

- Reconciled the `FEAT-CUST-003` / `FR-CUST-004` traceability mismatch: OQ-020 Option A is confirmed, so the functional requirement and RTM no longer present OQ-020 as an unresolved dependency. The feature/requirement remain `PROPOSED` pending explicit Legal/Security review of export contents, identity verification, anonymization coverage, exceptions, retention mapping and acceptance evidence.
- `FEAT-NTF-001`, `FEAT-REV-001` and `FEAT-RPT-001` now reflect the selected scope in the Feature Inventory and RTM. Their detailed requirements still contain explicitly open policy/specification items; do not treat those details as settled.
- Added `WF-017` for verified-buyer review submission and mandatory moderation; updated `FR-REV-001`, the RTM and Document 11's workflow count to reference 17 workflows. A complete cross-document link/identifier audit remains outstanding.
- OQ-015's selected channel set does not define the event-to-channel matrix, retry/fallback behavior or provider readiness. `DEP-003` remains open.
- OQ-018's selected advanced reporting scope does not define KPI formulas, freshness, permissions, exports, predictive methodology or validation evidence.
- OQ-012's selected verified-buyer/moderation scope does not decide photo reviews, appeals, retention, abuse escalation or edit/delete policy.
- OQ-019 is separate from `FEAT-DAT-001` / `FR-DAT-001`: bulk catalog/inventory import remains in scope; legacy customer/order history migration is excluded for the initial launch.
- Recheck all linked FR/BR/WF/NFR statuses, links and acceptance criteria during the final whole-package audit. This targeted closure pass is not evidence that every one of the 12 BRD documents has been fully audited.

### 4.3 NFR approval and evidence

A recorded business target is not proof that the system meets it. Review the NFR status and test method for:
- Performance and search/checkout latency targets.
- Sustained and peak order throughput.
- Availability, disaster recovery, RPO and RTO.
- Security, privacy, retention and audit requirements.
- Observability thresholds and operational response.

Where an NFR is marked `PROPOSED`, obtain owner approval, revise it with the owner, or explicitly record it as deferred. Where a target is selected or confirmed, record validation as `NOT_TESTED` until actual evidence exists.

## 5. Dependency register: readiness is separate from baseline approval

The six currently recorded dependencies remain open:

| ID | Dependency | Minimum closure evidence |
|---|---|---|
| DEP-001 | Razorpay merchant agreement | Merchant onboarding/contract status, sandbox and webhook evidence, settlement/refund/reconciliation checks, production-readiness approval |
| DEP-002 | Shipping operations readiness | Provider-neutral lifecycle; selected lanes/providers and contracts; verified sites/serviceability; booking/tracking/webhook tests; COD/remittance, returns and failed-delivery SOPs; end-to-end evidence |
| DEP-003 | SMS DLT / WhatsApp Business | Approved sender/templates and relevant account/registration evidence; delivery/OTP tests and fallback behavior |
| DEP-004 | GSTIN and invoice numbering | Finance/legal-approved GST/tax interpretation, HSN/tax mapping, invoice/credit-note numbering and sample validation |
| DEP-005 | Master catalog and brand data | Approved source data, SKU/variant schema, product media/specifications/warranty provenance and import validation |
| DEP-006 | Warehouse infrastructure/barcode hardware | Site/process readiness, barcode standards, device tests, inward/pick/pack/dispatch reconciliation |

A dependency can remain open while non-dependent documentation and sandbox work continue. Do not claim production readiness while a launch-critical dependency lacks evidence.

## 6. Acceptance criteria for Phase 1 closure

Phase 1 is ready to submit for formal approval only when all applicable criteria below have evidence:

- [ ] Every one of the 12 core BRD documents has a consistent version, owner, lifecycle status and scope.
- [ ] All 22 OQs are either explicitly decided or formally deferred with owner, reason, impact, revisit trigger and affected requirements.
- [ ] All assumptions are validated, rejected or explicitly accepted as time-bounded assumptions by the appropriate owner.
- [ ] All 6 dependencies have an accountable owner, required evidence, launch-criticality classification and status; open real-world work is not falsely marked complete.
- [ ] The 41 feature rows in the RTM reconcile to the feature inventory and all FR/BR/WF/NFR IDs exist.
- [ ] Traceability is bidirectional: no orphan feature/FR/BR/WF/NFR; each OQ and DEP points to all affected requirements; no stale or unrelated dependency links.
- [ ] Requirement/feature statuses match their unresolved decisions; no item is marked confirmed solely because it appears in a draft.
- [ ] Acceptance criteria are testable, unambiguous, and include normal, alternative, boundary, failure, authorization and concurrency cases where relevant.
- [ ] Cross-document policy values agree (COD limit/fee/OTP, return window, payment methods, tax-inclusive pricing, identity/guest checkout, delivery lifecycle and NFR targets).
- [ ] Scope exclusions consistently prohibit third-party marketplace/seller features.
- [ ] Every proposed NFR has an explicit decision status and verification method; test execution is separately evidenced.
- [ ] A fresh audit records checks actually performed, findings, remediation links, remaining risks and reviewer.
- [ ] Authorized Business Owner completes the genuine approval record in Document 11; no AI-generated signature or inferred approval.
- [ ] The baseline version/hash is recorded after approval so downstream architecture and implementation can pin to an immutable baseline.

## 7. Recommended closure sequence

1. Complete the remaining detailed policy/specification work for OQ-012, OQ-015 and OQ-018 without changing the selections already recorded.
2. Validate the OQ-007/ASM-004 four-warehouse/regional-inventory plan against actual sites, legal/tax implications, capacity and serviceability without hard-coding city or PIN-code data.
3. Keep OQ-019 legacy customer/order migration excluded; validate standard catalog/opening-stock import separately under DEP-005 and inventory readiness.
4. Complete the whole-package audit of all FR/BR/WF/NFR statuses, identifiers and links; the specific `FEAT-CUST-003` / OQ-020 mismatch has been reconciled.
5. Re-audit all 12 core BRD files, acceptance criteria, scope boundaries and cross-document policy values; distinguish decisions recorded from implementation/operational evidence.
6. Update this working audit with checks actually performed, findings, remediation links and remaining risks. Do not reuse the 2026-10-18 audit verdict as if it were a fresh audit.
7. Submit the package to the authorized Business Owner for formal review. Keep Phase 1 `PENDING_APPROVAL`, baseline `NOT_APPROVED`, architecture `NOT_GRANTED` until genuine sign-off is recorded.

## 8. Explicit non-claims

This document does **not** claim:
- Phase 1 or architecture approval.
- Validated warehouse addresses or serviceable PIN codes.
- Signed carrier/aggregator contracts or tested provider integrations.
- A live Razorpay merchant account or production payment verification.
- Passed load, security, DR, privacy or end-to-end tests.
- That any external dependency has been closed.

