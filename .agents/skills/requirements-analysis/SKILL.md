---
name: requirements-analysis
description: >-
  Use this skill to break down business goals into functional and non-functional requirements,
  write concrete user stories, define unambiguous acceptance criteria, and record assumptions and open questions.
---

# Requirements Analysis Skill

## 1. Purpose
Transforms business capabilities and customer needs into rigorous, testable functional and non-functional requirements, user stories, acceptance criteria, and traceability links.

## 2. When to Use It
- Scoping a new commercial capability or feature phase.
- Breaking down high-level user requests into detailed technical specifications.
- Identifying edge cases, validation rules, and non-happy paths.
- Updating `docs/01-requirements/` or documenting open questions in `docs/00-project/open-questions.md`.

## 3. Required Inputs
- Business scope and user problem statement.
- Target user persona (Customer or Internal Admin).
- Established business rules (`docs/02-business-rules/`).

## 4. Procedure
1. **Identify Personas & Actors**: Distinguish between Customer (authenticated or guest) and Administrator/Operations Staff.
2. **Define User Stories**: Format as: `As a <persona>, I want <goal> so that <business value>`.
3. **Draft Acceptance Criteria**: Formulate criteria using Given-When-Then format for happy paths, edge cases, and failure modes.
4. **Quantify Non-Functional Requirements**: Specify exact latency budgets (p95/p99), throughput (RPS), data durability, and concurrency tolerances.
5. **Traceability Mapping**: Map each requirement to its business rule, domain aggregate, owning service, and test category.
6. **Flag Unresolved Questions**: Record unknowns in `docs/00-project/open-questions.md` with trade-off analysis.

## 5. Rules & Constraints
- Requirements must never assume marketplace, multi-vendor, or seller onboarding capabilities.
- Acceptance criteria must be objectively verifiable via automated tests.
- Avoid vague terms (e.g. "fast", "scalable", "user-friendly"); provide concrete quantitative metrics.

## 6. Expected Outputs
- Requirements documents updated in `docs/01-requirements/`.
- Traceability rows added in `docs/01-requirements/requirements-traceability.md`.
- Open questions recorded in `docs/00-project/open-questions.md`.

## 7. Validation Checklist
- [ ] Are both happy and unhappy paths covered by acceptance criteria?
- [ ] Are concurrency and race condition scenarios addressed?
- [ ] Are error codes and expected failure messages specified?

## 8. Common Failure Modes
- Omitting guest user scenarios.
- Assuming infinite inventory or instantaneous payment confirmations.
- Silently guessing unstated tax or jurisdiction legal requirements.

## 9. Authoritative Links
- [Functional Requirements](file:///d:/learning/Microservice/backend/docs/01-requirements/functional-requirements.md)
- [Acceptance Criteria](file:///d:/learning/Microservice/backend/docs/01-requirements/acceptance-criteria.md)
- [Requirements Traceability](file:///d:/learning/Microservice/backend/docs/01-requirements/requirements-traceability.md)
