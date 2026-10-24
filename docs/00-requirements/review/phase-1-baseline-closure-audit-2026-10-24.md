# Phase 1 Requirements Baseline Closure — Current-State Audit and Action Register

> **Status: WORKING AUDIT — NOT AN APPROVAL RECORD.**
>
> This document records the findings and closure work required before Phase 1 can be approved. It does not approve the BRD, authorize architecture, close operational dependencies, or claim that performance/security tests have run. It is based on the repository contents inspected on branch `dev` on 2026-10-24 and the business-owner selections explicitly recorded in the current conversation history.

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

The current Document 08 has 22 OQs. Based on the current text, the status distribution is:

- **17 marked `CONFIRMED`:** OQ-001–006, OQ-008–011, OQ-013–014, OQ-016–017, OQ-020–022.
- **4 marked `UNDER_REVIEW`:** OQ-012, OQ-015, OQ-018, OQ-019.
- **1 recorded decision with operational validation still open:** OQ-007 (four initial warehouses and regional inventory; actual sites/PIN codes not validated).

This is a documentation status snapshot, not a claim that the full BRD baseline is approved. Some “confirmed” OQs depend on external commercial, legal, operational or technical evidence that remains open.

## 3. Decisions and unresolved questions

| ID | Current finding | Required action | Owner / evidence |
|---|---|---|---|
| OQ-007 / ASM-004 | Reconciled in this branch to four initial warehouses and regional inventory. Exact sites/PIN codes remain unvalidated. | Validate real sites, capacity, tax/legal implications, stock distribution and serviceability. Keep architecture location-agnostic/configuration-driven. | Operations/Finance evidence for real sites and serviceability; baseline approval remains pending |
| OQ-012 | Reviews and ratings remain `UNDER_REVIEW`. | Choose launch scope (include, defer, or exclude) and moderation/verified-purchase policy; update FR/BR/WF/RTM consistently. | Business Owner / Product |
| OQ-015 | Notification channels remain `UNDER_REVIEW`, while the RTM describes SMS, email and WhatsApp plus fallback. | Confirm required launch channels, which notifications are mandatory, fallback rules, template ownership and channel-specific dependencies. | Business Owner / Operations; DEP-003 evidence |
| OQ-018 | Reporting scope remains `UNDER_REVIEW`, while the RTM describes GMV, revenue, tax and stock-velocity reports as confirmed. | Confirm launch reports, roles, export requirements, retention and reconciliation requirements; align FEAT/FR/BR/WF/RTM statuses. | Business Owner / Finance |
| OQ-019 | Historical-data migration scope remains `UNDER_REVIEW`, while the RTM describes bulk spreadsheet catalog/inventory import as confirmed and links it to OQ-019. | Separate **legacy historical migration** from **new catalog/inventory bulk import**. Confirm whether legacy migration is needed; do not make bulk import depend on an unrelated question unless justified. | Business Owner / Merchandising / Data owner |

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
- Four initial warehouse locations and regional inventory are recorded as planning inputs; exact sites, service areas and serviceability are not validated.

## 4. Cross-document consistency findings

### 4.1 Stale audit and decision-register summaries

1. `review/brd-final-quality-audit.md` is dated 2026-10-18 and says 17 of 22 questions remain provisional / only five are confirmed. This conflicts with current Document 08 and later owner decisions recorded on 2026-10-24. Treat the older report as historical; do not use it as the current closure verdict.
2. `review/executive-decision-register.md` was updated in this branch to index ADQ-001 through ADQ-015, including the previously missing summary rows for ADQ-012 through ADQ-015.
3. ADQ-011 remains historical for delivery-provider mix and is explicitly superseded by ADQ-014; the summary row should continue to be visually marked as historical/superseded in any later cleanup.
4. OQ-007 and ASM-004 were reconciled in this branch to four initial warehouses and regional inventory, with exact sites/PIN codes pending validation.

### 4.2 Traceability and status mismatches to resolve

The current RTM contains rows whose status/dependency story needs reconciliation with Document 08:

- `FEAT-CUST-003` is `PROPOSED` and cites OQ-020 even though OQ-020 is `CONFIRMED`; verify whether the feature itself is proposed for another reason or update its status and dependency.
- `FEAT-ADV-001` is `PROPOSED` and cites OQ-014 even though OQ-014 excludes pre-orders/backorders. Verify whether this feature is actually a product bundle capability and remove the unrelated OQ-014 dependency if so.
- `FEAT-NTF-001` is `CONFIRMED` while OQ-015 is `UNDER_REVIEW`; mark only the undecided channel-specific portions as provisional or resolve the question.
- `FEAT-RPT-001` is `CONFIRMED` while OQ-018 is `UNDER_REVIEW`; reconcile the scope and status.
- `FEAT-DAT-001` is `CONFIRMED` while OQ-019 is `UNDER_REVIEW`; distinguish bulk catalog/inventory import from historical migration and correct the traceability link if needed.
- `FEAT-INV-001` cites OQ-007; update its implementation assumptions for four regional warehouses without hard-coding city names.
- Confirm that every FR, BR, WF and NFR linked to the five under-review questions has a status consistent with the decision register.

These are findings to resolve; this audit does not silently change the normative requirement status on behalf of the Business Owner.

### 4.3 NFR approval and evidence

A recorded business target is not proof that the system meets it. Review the NFR status and test method for:
- Performance and search/checkout latency targets.
- Sustained and peak order throughput.
- Availability, disaster recovery, RPO and RTO.
- Security, privacy, retention and audit requirements.
- Observability thresholds and operational response.

Where an NFR is marked `PROPOSED`, either obtain owner approval, revise it with the owner, or explicitly record it as deferred. Where it is confirmed as a target, record validation as `NOT_TESTED` until actual evidence exists.

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

1. Validate the reconciled OQ-007/ASM-004 four-warehouse/regional-inventory plan against actual sites without hard-coding city or PIN-code data into the architecture.
2. Obtain owner decisions or explicit deferrals for OQ-012, OQ-015, OQ-018 and OQ-019.
3. Reconcile the decision register and mark ADQ-011 as superseded for provider mix by ADQ-014.
4. Correct RTM status/dependency mismatches and recheck all linked FR/BR/WF/NFR records.
5. Re-audit all 12 core BRD files, identifier/link integrity, Mermaid syntax, acceptance criteria, scope boundaries and policy-value consistency.
6. Produce a current audit and remediation log; do not reuse the 2026-10-18 audit verdict as if it were a fresh audit.
7. Submit the package to the authorized Business Owner. Keep Phase 1 `PENDING_APPROVAL`, baseline `NOT_APPROVED`, architecture `NOT_GRANTED` until genuine sign-off is recorded.

## 8. Explicit non-claims

This document does **not** claim:
- Phase 1 or architecture approval.
- Validated warehouse addresses or serviceable PIN codes.
- Signed carrier/aggregator contracts or tested provider integrations.
- A live Razorpay merchant account or production payment verification.
- Passed load, security, DR, privacy or end-to-end tests.
- That any external dependency has been closed.

