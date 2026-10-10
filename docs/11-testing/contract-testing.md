# Contract Testing

Consumer-Driven Contract testing using Spring Cloud Contract / Pact.

---

## 1. Inter-Service Contract Verification

To prevent breaking API changes between microservices:
1. Producer defines contract stubs in Groovy / YAML (e.g. `PricingService` defines `calculatePrice` contract).
2. Spring Cloud Contract Maven plugin generates HTTP WireMock stubs and publishes them to the local Maven repository / Artifactory.
3. Consuming services (`CartService`, `OrderService`) test against the generated WireMock stubs in isolation without needing the real producer running.
4. Producer builds verify that its own controllers satisfy all published contract stubs.
