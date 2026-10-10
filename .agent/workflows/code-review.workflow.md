# Code Review Workflow

## 1. Purpose & Objective
Perform rigorous, automated and peer code review of changes against architectural boundaries, security standards, data integrity invariants, test coverage, and documentation consistency.

## 2. Prerequisites & Inputs
- Git branch or change diff.
- Related requirements, user story, and acceptance criteria.
- Core rules codified in `AGENTS.md`.

## 3. Workflow Sequence

```text
Inspect Diff & Target Scope
              |
              v
Verify Requirements & Acceptance Criteria
              |
              v
Verify Architectural Boundaries & Data Ownership
              |
              v
Audit Security & Secrets Exposure
              |
              v
Verify Concurrency, Transaction & Failure Handling
              |
              v
Check Automated Test Coverage & Evidence
              |
              v
Audit Performance, Indexes & Query Efficiency
              |
              v
Verify Documentation & Contract Sync
              |
              v
Produce Findings Categorized by Severity
```

## 4. Severity Classification
- **BLOCKER**: Violation of core invariants (e.g. marketplace logic introduced, direct cross-service DB query, hardcoded secret, missing tests, unhandled race condition). Must be fixed before merge.
- **MAJOR**: Incomplete error handling, unindexed query, missing API contract update, loose validation.
- **MINOR**: Inconsistent naming, redundant code, style deviations, minor documentation typo.

## 5. Responsible Skills
- `code-review`
- `security-engineering`
- `architecture-design`
- `testing-engineering`

## 6. Completion Gate
- Zero BLOCKER or MAJOR issues unresolved.
- Verified test execution output provided.
- Documentation and Feature Matrix updated.
