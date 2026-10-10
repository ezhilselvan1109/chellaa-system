---
name: code-review
description: >-
  Use this skill to conduct thorough peer reviews on pull requests and code modifications,
  detecting architectural violations, security vulnerabilities, missing tests, unhandled failure modes, and contract drift.
---

# Code Review Skill

## 1. Purpose
Provides a systematic review checklist and defect discovery process to verify that every proposed code change adheres to architectural boundaries, security mandates, transaction safety rules, and test coverage criteria.

## 2. When to Use It
- Reviewing pull requests or git diffs before merging into main branches.
- Self-reviewing generated code changes before completing a task.
- Auditing microservice implementations for data leakages or cross-boundary dependencies.
- Verifying adherence to `AGENTS.md` rules.

## 3. Required Inputs
- Git diff or proposed code changes.
- Referenced feature requirements and acceptance criteria.
- Governing rules from `AGENTS.md`.

## 4. Procedure
1. **Scope & Requirement Alignment**:
   - Verify that the diff accomplishes the stated requirements without unrelated or extraneous changes.
2. **Architecture & Boundary Check**:
   - Verify that no marketplace constructs (seller dashboards, vendor commissions) were introduced.
   - Verify that no direct cross-database queries or shared repository entities were introduced.
3. **Security Audit**:
   - Verify zero hardcoded secrets or credentials.
   - Check authorization guards on endpoints (`@PreAuthorize`).
   - Validate input sanitization and protection against SQL injection / XSS.
4. **Data Integrity & Concurrency**:
   - Verify that database state mutations and Kafka event emissions use the Transactional Outbox pattern.
   - Verify optimistic or pessimistic locking on concurrent state mutations (e.g. inventory allocations).
5. **Test Coverage & Verification**:
   - Verify that new business logic is covered by unit and integration tests.
   - Check that tests were executed and passed.
6. **Report Generation**: Categorize findings by severity: `BLOCKER`, `MAJOR`, `MINOR`.

## 5. Rules & Constraints
- Any violation of `AGENTS.md` (e.g., cross-database query, missing test, marketplace logic) is an automatic `BLOCKER`.
- Never approve a change if automated test execution is absent.

## 6. Expected Outputs
- Detailed code review comment report with file citations and severity ratings.

## 7. Validation Checklist
- [ ] Are all foreign calls isolated outside `@Transactional` blocks?
- [ ] Are error responses following `RFC 7807` standard?
- [ ] Are database migrations backward-compatible?

## 8. Common Failure Modes
- Approving pull requests without checking if tests actually passed.
- Ignoring missing indexes on new foreign key columns.

## 9. Authoritative Links
- [AGENTS.md](file:///d:/learning/Microservice/backend/AGENTS.md)
- [Code Review Workflow](file:///d:/learning/Microservice/backend/.agent/workflows/code-review.workflow.md)
