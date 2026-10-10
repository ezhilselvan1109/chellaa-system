# Testing Workflow

## 1. Purpose & Objective
Establish a disciplined, multi-layered quality assurance process across unit, integration, contract, end-to-end, concurrency, and chaos testing, ensuring zero untested features enter production.

## 2. Prerequisites & Inputs
- Feature requirements and acceptance criteria.
- Target service source code and test infrastructure (JUnit 5, Testcontainers, WireMock).
- Failure mode analysis and concurrency requirements.

## 3. Workflow Sequence

```text
Acceptance Criteria & Risk Analysis
                 |
                 v
Identify Test Levels (Unit, Integration, E2E, Chaos)
                 |
                 v
Define Success, Boundary & Negative Failure Cases
                 |
                 v
Author Unit Tests (Domain logic, calculations, states)
                 |
                 v
Author Integration Tests (Testcontainers PostgreSQL/Kafka)
                 |
                 v
Author Concurrency & Race Condition Tests
                 |
                 v
Execute Test Suite via CLI (`mvn test` / `npm test`)
                 |
                 v
Analyze Results & Capture Evidence
                 |
                 v
Regression & Flakiness Verification
                 |
                 v
Produce Test Verification Report
```

## 4. Responsible Skills
- `testing-engineering`
- `springboot-development`
- `react-development`
- `performance-engineering`

## 5. Completion Gate
- All new/modified domain logic has >85% unit test branch coverage.
- Testcontainers integration tests pass without flakiness or leaked resources.
- Concurrency test proves zero overselling under simulated concurrent checkouts.
- All tests execute cleanly via terminal tooling and exit with status code 0.
