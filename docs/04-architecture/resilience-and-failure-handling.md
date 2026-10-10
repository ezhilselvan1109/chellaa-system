# Resilience and Failure Handling

Architectural strategies for circuit breakers, bulkheads, rate limiting, and graceful degradation.

---

## 1. Resilience Patterns (Resilience4j)

1. **Circuit Breakers**:
   - Wrap remote REST calls (e.g., Shipping Service, Payment Gateway).
   - Configuration: Sliding window size of 50 calls; opens if failure rate exceeds 50%; wait duration in open state is 10 seconds before half-open probe.
2. **Bulkheads**:
   - Limit concurrent executions per downstream integration to prevent thread pool starvation across unrelated paths.
   - Max concurrent calls to Payment Gateway: 50. Max concurrent calls to Logistics Carrier: 30.
3. **Distributed Rate Limiting (Redis Token Bucket)**:
   - Authentication Endpoints (`/api/v1/auth/*`): 10 requests per minute per IP.
   - Public Catalog Search (`/api/v1/search`): 120 requests per minute per IP.
   - Checkout Mutations (`/api/v1/orders/checkout`): 10 requests per minute per user.

---

## 2. Graceful Degradation Strategy

If non-critical supporting microservices fail:
- **Search Service Down**: Storefront falls back to querying Catalog Service PostgreSQL via indexed SQL fallback queries or cached top products.
- **Review Service Down**: Product detail page renders product specifications, pricing, and inventory with a banner: `"Reviews temporarily unavailable"`; checkout remains 100% operational.
- **Notification Service Down**: Kafka event bus safely queues notification events; shopping journeys proceed uninterrupted.
