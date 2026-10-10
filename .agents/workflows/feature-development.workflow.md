# Feature Development Workflow

## 1. Purpose & Objective
Guide the end-to-end design, implementation, testing, verification, and documentation of a functional capability while maintaining architectural integrity, data ownership, and contract compatibility.

## 2. Prerequisites & Inputs
- Feature request or user story.
- Current repository state and authoritative documentation in `docs/`.
- Operating rules specified in `AGENTS.md`.

## 3. Workflow Sequence

```text
1. Feature Request & Scope Definition
                 |
                 v
2. Read AGENTS.md & Relevant Documentation
                 |
                 v
3. Clarify Ambiguities & Edge Cases
                 |
                 v
4. Refine Business Rules & Acceptance Criteria
                 |
                 v
5. Domain Design (Aggregates, Invariants)
                 |
                 v
6. Database Impact & Migrations Plan
                 |
                 v
7. API & Event Contract Impact Analysis
                 |
                 v
8. Security & Failure Analysis
                 |
                 v
9. Implementation Plan & Design Proposal
                 |
                 v
10. Approval Gate (For Major Architectural Changes)
                 |
                 v
11. Incremental Implementation
                 |
                 v
12. Automated Tests (Unit, Integration, Contract)
                 |
                 v
13. Build & Verification (Must Execute & Pass)
                 |
                 v
14. Code Review (Self-review against constraints)
                 |
                 v
15. Documentation & Contract Updates
                 |
                 v
16. Feature Matrix & Progress Update
                 |
                 v
17. Completion Report
```

## 4. Responsible Skills
- `requirements-analysis`
- `domain-design`
- `database-design`
- `api-design`
- `kafka-event-design`
- `springboot-development`
- `react-development`
- `security-engineering`
- `testing-engineering`
- `code-review`
- `documentation-engineering`

## 5. Completion Gate & Definition of Done
- All acceptance criteria satisfied and verified with passing automated test execution.
- Flyway/Liquibase migration script cleanly applies and rolls back.
- REST and Kafka contracts updated in `docs/06-api/` and `docs/07-events/`.
- Feature Matrix status updated to `Verified`.
- Zero compiler or linter errors.
