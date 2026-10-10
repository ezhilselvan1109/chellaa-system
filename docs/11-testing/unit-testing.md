# Unit Testing Guidelines

Standards for pure Java domain unit tests using JUnit 5 and AssertJ.

---

## 1. Unit Testing Invariants

- **Isolation**: Domain unit tests must run purely in the JVM without starting the Spring context or Docker containers, completing in < 10 milliseconds per test.
- **Scope**:
  - Entity invariants, state machine transitions, and aggregate root methods.
  - Price calculations, discount allocations, GST tax de-aggregation, and coupon validation rules.
  - Value object immutability and arithmetic validation (`Money`, `Address`, `Sku`).
- **Frameworks**: JUnit 5, AssertJ, Mockito (restricted to outbound port boundaries).
