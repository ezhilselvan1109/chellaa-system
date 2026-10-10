---
name: testing-engineering
description: >-
  Use this skill to design and execute comprehensive automated testing suites across unit, integration,
  contract, end-to-end, concurrency, and failure recovery dimensions using JUnit 5, Testcontainers, and MockMvc.
---

# Testing Engineering Skill

## 1. Purpose
Provides disciplined automated testing methodologies ensuring that every feature, business rule, and edge case is thoroughly validated by runnable, reproducible tests before completion claims are made.

## 2. When to Use It
- Designing test plans for new features or bug fixes.
- Writing unit tests for complex domain logic (pricing, discounts, tax calculations, inventory deduction).
- Authoring Testcontainers integration tests against real PostgreSQL and Kafka instances.
- Designing concurrency tests to verify race conditions (e.g. inventory overselling, simultaneous coupon redemptions).
- Verifying distributed failure and compensating transaction scenarios (Sagas).

## 3. Required Inputs
- Feature acceptance criteria (`docs/01-requirements/acceptance-criteria.md`).
- Target service source code and domain models.
- Concurrency and failure mode requirements (`docs/04-architecture/resilience-and-failure-handling.md`).

## 4. Procedure
1. **Apply the Testing Pyramid**:
   - Unit Tests (70%): Pure Java, fast execution, mocking external boundaries, testing domain invariants.
   - Integration Tests (20%): Spring Boot context with Testcontainers for PostgreSQL, Redis, and Kafka.
   - End-to-End / Contract Tests (10%): Spring Cloud Contract or Pact, verifying inter-service compatibility.
2. **Author Concurrency Tests**:
   - Use `ExecutorService` and `CountDownLatch` with 50+ concurrent threads attempting to reserve 10 units of stock.
   - Assert that exactly 10 reservations succeed and 40 fail cleanly with zero over-allocation.
3. **Author Failure Recovery Tests**:
   - Simulate payment gateway timeout: verify Order Service triggers compensating inventory release.
   - Simulate Kafka duplicate event: verify consumer skips execution without double-crediting or double-shipping.
4. **Execute Tests via Shell**:
   - Proactively run `./mvnw test` or targeted test commands.
   - Capture exact console logs and test report summaries.
5. **Verify Definition of Done**: Confirm all acceptance criteria pass.

## 5. Rules & Constraints
- NEVER claim a test passed without running it and verifying exit code 0.
- Never use `@Disabled` or `@Ignore` to bypass failing tests.
- Tests must be completely deterministic and free of race conditions or flaky timing assumptions (`awaitility` preferred over `Thread.sleep`).

## 6. Expected Outputs
- Runnable test classes (`*Test.java`, `*IT.java`).
- Verified execution reports with passing status.

## 7. Validation Checklist
- [ ] Are happy paths, boundary edges, and exception paths covered?
- [ ] Do integration tests cleanly initialize and tear down database tables?
- [ ] Is test coverage verified with Jacoco?

## 8. Common Failure Modes
- Using `Thread.sleep(1000)` leading to flaky tests on CI agents.
- Testing only happy paths and ignoring network timeouts and concurrency conflicts.

## 9. Authoritative Links
- [Testing Strategy](file:///d:/learning/Microservice/backend/docs/11-testing/testing-strategy.md)
- [Concurrency Testing](file:///d:/learning/Microservice/backend/docs/11-testing/concurrency-testing.md)
- [Failure Testing](file:///d:/learning/Microservice/backend/docs/11-testing/failure-testing.md)
