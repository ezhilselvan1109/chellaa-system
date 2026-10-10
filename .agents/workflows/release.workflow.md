# Release Workflow

## 1. Purpose & Objective
Coordinate safe, zero-downtime production releases by validating feature readiness, backward-compatible database migrations, contract stability, environment configurations, and rollback runbooks.

## 2. Prerequisites & Inputs
- Target release version tag.
- Feature Matrix status review (`docs/15-implementation/feature-matrix.md`).
- Clean CI build and test results.

## 3. Workflow Sequence

```text
Feature Matrix & Scope Review
              |
              v
Test Suite & CI Build Verification
              |
              v
Database Migration Compatibility Audit
              |
              v
Security, CVE & Secret Leak Scan
              |
              v
Environment Configuration & Secrets Review
              |
              v
Deployment Execution Plan & Health Check Criteria
              |
              v
Rollback Procedure & Automated Trigger Criteria
              |
              v
Canary / Blue-Green Deployment Execution
              |
              v
Post-Deployment Verification & Telemetry Audit
              |
              v
Changelog & Documentation Tagging
```

## 4. Responsible Skills
- `architecture-design`
- `security-engineering`
- `observability-engineering`
- `testing-engineering`
- `documentation-engineering`

## 5. Completion Gate
- All database migrations are backward-compatible (expand-and-contract pattern).
- Health checks (`/actuator/health`) return HTTP 200 UP.
- Synthetic smoke test verifies critical paths (Login, Catalog browse, Cart, Checkout).
- Zero spike in error rate (HTTP 5xx < 0.05%) in APM telemetry.
- `CHANGELOG.md` updated with release notes.
