# Phase 2 Architecture Draft Alignment Audit — 2026-10-24

> **Status: WORKING ARCHITECTURE REVIEW — NON-BINDING.**
>
> This audit begins the next workstream by checking the existing ARCH-01–ARCH-12 preparatory drafts against the current Requirements Baseline working branch. It does not approve the requirements baseline, authorize implementation, or promote architecture drafts into `docs/01-architecture/`.
>
> The user has indicated that owner review/approval is okay in chat. However, Document 11 still has an empty formal decision/sign-off record and PR #1 has no submitted GitHub review. Under the repository's own governance rules, do not infer or fabricate a formal approval record from this note.

## 1. Current gate and scope

- Working branch: `docs/phase1-baseline-closure`
- Requirements PR: [#1 — Phase 1 baseline closure](https://github.com/ezhilselvan1109/chellaa-system/pull/1), still open and draft at the time of this audit.
- Requirements status in Document 11: `PENDING_APPROVAL` / `NOT_APPROVED`.
- Architecture authorization: `NOT_GRANTED`.
- Architecture artifacts reviewed: `ARCH-01` through `ARCH-12`, all currently located under `docs/00-requirements/review/` as non-binding drafts.
- No software implementation, migrations, provider integrations, or architecture promotion is authorized by this audit.

## 2. Confirmed requirements decisions that architecture must reflect

| Requirement decision | Recorded selection | Architecture implication |
|---|---|---|
| OQ-007 / ADQ-015 | Four initial warehouse locations with regional inventory; actual sites and serviceable PIN codes remain unvalidated. | Use location-agnostic, configuration-driven warehouse and regional inventory models. Do not assume one warehouse or hard-code cities/PINs. |
| OQ-012 / ADQ-016 | Verified-buyer reviews in Phase 1; mandatory admin moderation before publication. | Model review verification and moderation states. Photo reviews, detailed moderation policy, appeals, retention and edit/delete rules remain open. |
| OQ-015 / ADQ-017 | SMS, Email and WhatsApp Business API selected for order lifecycle events. | Keep channel selection policy configurable. The event-to-channel matrix, retries/fallback order/timing, consent/preference handling and provider readiness remain open. Do not invent a WhatsApp-to-SMS or email fallback policy. |
| OQ-018 / ADQ-018 | Standard reports plus real-time BI, predictive replenishment and customer cohort retention analysis. | Include the selected scope as a requirement, but leave KPI formulas, data sources, refresh latency, permissions, cadence, model methodology and acceptance criteria open. |
| OQ-019 / ADQ-019 | No legacy customer/order history migration for launch; new catalog and opening-stock import remains in scope. | Do not design a mandatory legacy customer/order migration path. Keep catalog/opening-stock import separate. |
| OQ-020 | Option A confirmed: retain statutory financial records for eight financial years while allowing appropriate profile/marketing-data anonymization. | Treat the business decision as confirmed; architecture/security/legal implementation detail and evidence remain subject to review. |

## 2.1 Remediation performed in this working branch

- `ARCH-01`: replaced the single-central-warehouse assumption with four initial regional warehouse locations, kept exact sites/PIN codes unvalidated, aligned review mapping to `WF-017`, and removed an unapproved automatic email-fallback assumption.
- `ARCH-04`: changed the old 16-workflow completion/100%-traceability claims to `PENDING_RECONCILIATION`; relabeled the review sequence as `WF-017`. Full workflow title/ID mapping is still outstanding.
- `ARCH-12`: invalidated the old 260-element/16-workflow/100%-coverage claim, changed workflow total to 17, and marked the workflow coverage row and completion checks as pending re-verification.
- Phase 1 `FEAT-CUST-003` / `FR-CUST-004` / RTM traceability was reconciled separately: OQ-020 is confirmed; detailed privacy implementation remains proposed pending Legal/Security review.

These are partial draft corrections, not approval or evidence that all architecture mappings have been reconciled.

## 3. Findings requiring architecture-draft remediation

### A-01 — Warehouse topology conflict (Critical)

- `ARCH-01` still states a “Single Central Warehouse Baseline” and lists “Single Warehouse Topology” as an operational assumption.
- This conflicts with OQ-007 / ADQ-015, which records four initial warehouse locations with regional inventory.
- **Required:** align ARCH-01, ARCH-02, ARCH-03, ARCH-05, ARCH-08 and ARCH-12 to a generic, configurable multi-warehouse model. Actual sites, addresses, capacity and PIN-code serviceability must remain unvalidated.

### A-02 — Workflow identifier and count drift (Critical)

- The requirements workflow inventory now contains `WF-001` through `WF-017`.
- `ARCH-04` still claims coverage of 16 workflows. Its review sequence is labeled `WF-013`, although requirements now define `WF-013` as Customer Support Ticket Resolution and the review workflow as `WF-017`. Its traceability table also maps several workflow IDs to different business meanings than Document 06.
- `ARCH-12` still reports 16 workflows and claims 100% coverage of 260 baseline elements without recalculation after WF-017 was added.
- **Required:** reconcile every workflow title, ID, feature/FR/BR link, sequence diagram and coverage total against Document 06. Do not retain a 100% coverage claim until it is actually rechecked.

### A-03 — Notification fallback is over-specified (High)

- `ARCH-01` describes automatic fallback to email and other fixed retry/fallback behavior.
- OQ-015 selects channel scope but does not approve the per-event channel matrix, retries or fallback order/timing.
- **Required:** make channel policy configurable and mark provider configuration and delivery tests as open under `DEP-003`.

### A-04 — Reviews policy needs alignment (High)

- `ARCH-01` still treats OQ-012 as an unresolved decision and points to an incorrect workflow ID for reviews.
- `ARCH-04` places review moderation under `WF-013`, which conflicts with the requirements workflow inventory.
- **Required:** align the review flow to `WF-017`; keep verified-buyer gating and mandatory pre-publication moderation, while leaving detailed moderation policy open.

### A-05 — OQ-020 is decided, but implementation evidence is not (Medium)

- `ARCH-02`, `ARCH-05`, `ARCH-07` and `ARCH-09` cite OQ-020 in review/policy contexts.
- OQ-020 Option A is now confirmed. Its citations may remain as decision traceability, but the documents must distinguish the confirmed retention policy from still-open technical design, legal validation and operational evidence.
- **Required:** remove language that implies the business choice itself is still undecided; do not claim retention controls are deployed or tested.

### A-06 — Advanced reporting scope is not yet an implementation specification (Medium)

- OQ-018 Option B is selected, but report definitions and analytics acceptance criteria remain open.
- **Required:** reflect the selected scope in domain/data/event designs while keeping KPI formulas, freshness, permissions, predictive methodology and validation criteria explicitly provisional.

### A-07 — Legacy migration boundary (Medium)

- OQ-019 Option A excludes legacy customer/order history migration from launch. Standard catalog/opening-stock import remains in scope.
- **Required:** review ARCH-05, ARCH-06 and ARCH-12 for any legacy customer/order migration assumption and distinguish it from master-data import.

### A-08 — Decision and readiness statuses need systematic review (High)

- Some architecture drafts label confirmed business decisions as “Open Decision (Provisional)” or retain stale OQ references.
- Provider readiness, warehouse site validation, performance, DR, security and other operational/test evidence must not be represented as completed just because a business target is recorded.
- **Required:** audit every status and source link; separate `DECISION_RECORDED`, `PENDING_REVIEW`, `NOT_TESTED`, and `NOT_GRANTED` states clearly.

## 4. Recommended remediation order

1. Reconcile the workflow catalog and identifier mappings in ARCH-04 and ARCH-12 against the current 17-workflow requirements source.
2. Align warehouse, shipping and inventory topology across ARCH-01/02/03/05/08/10/12.
3. Align review moderation and notification policy across ARCH-01/02/04/06/07/08/09/12.
4. Align data retention and no-legacy-history-migration boundaries across ARCH-02/05/06/07/09/12.
5. Recalculate architecture traceability coverage only after all mappings are checked; do not use self-reported checklist boxes as proof of verification.
6. Complete the whole-package Phase 1 requirements audit and record formal approval in Document 11.
7. Only after the required written sign-off and architecture authorization, promote the reviewed architecture documents into `docs/01-architecture/`.

## 5. Explicit non-claims

- This is a targeted draft-alignment audit, not proof that every line in all 12 architecture drafts has been validated.
- No architecture document has been promoted to `docs/01-architecture/`.
- No architecture decision or implementation choice is approved by this audit.
- No tests, benchmarks, provider integrations or operational readiness checks are claimed as completed.
- Requirements baseline remains `NOT_APPROVED` and architecture authorization remains `NOT_GRANTED` until the formal governance record is completed.
