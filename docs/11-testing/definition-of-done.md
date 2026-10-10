# Definition of Done (DoD)

Engineering checklist required before any feature or change can be marked complete.

---

## 1. Feature Completion Checklist

A feature is complete and verified ONLY when:
- [ ] Requirements and acceptance criteria are documented in `docs/01-requirements/`.
- [ ] Business invariants (single-owner model, zero marketplace logic) strictly adhered to.
- [ ] Microservice data ownership boundaries respected (zero cross-database queries).
- [ ] Database migrations (Flyway) written, backward-compatible, and tested with Testcontainers.
- [ ] REST API endpoints documented with OpenAPI 3.1 and follow `RFC 7807` error standard.
- [ ] Kafka events follow CloudEvents JSON format with declared partition keys and outbox publishing.
- [ ] Idempotency implemented on state-mutating APIs and consumer handlers.
- [ ] Unit test branch coverage exceeds 85% via JaCoCo.
- [ ] Automated integration tests pass against real PostgreSQL/Kafka containers.
- [ ] Concurrency tests verify zero race conditions or over-allocation.
- [ ] Distributed failure recovery and compensating actions verified.
- [ ] CI pipeline build passes cleanly with zero warnings or linter errors.
- [ ] Feature Matrix (`docs/15-implementation/feature-matrix.md`) updated to `Verified`.
