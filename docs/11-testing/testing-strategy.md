# Testing Strategy

Multi-layered testing strategy, testing pyramid, coverage goals, and automation principles.

---

## 1. The Enterprise Testing Pyramid

```text
                  / \
                 / E2E \       (5%) - Playwright browser automation
                /-------\
               / Contract\     (10%) - Spring Cloud Contract / Pact
              /-----------\
             / Integration \   (25%) - Testcontainers (PostgreSQL, Kafka, Redis)
            /---------------\
           /   Unit Tests    \ (60%) - JUnit 5, AssertJ, Mockito (Domain logic)
          +-------------------+
```

---

## 2. Quality Gates & Thresholds

- **Branch Coverage Threshold**: Minimum 85% branch coverage on domain models, pricing calculations, and transaction managers via JaCoCo.
- **Deterministic Execution**: Zero flaky tests permitted in main branch CI. Tests must not depend on fixed timing delays (`awaitility` used for async assertions).
- **Automated Verification**: Every PR must run `./mvnw clean verify` and `npm test` exiting with status code 0.
