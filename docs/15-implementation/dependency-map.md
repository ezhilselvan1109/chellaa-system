# Microservice Dependency Map

Compile-time, runtime, and event-driven dependencies across services.

---

## 1. Inter-Service Dependency Graph

```text
[api-gateway]
    | (Synchronous Route)
    +---> [identity-service] (PostgreSQL: identity_db)
    +---> [customer-service] (PostgreSQL: customer_db)
    +---> [catalog-service]  (PostgreSQL: catalog_db)
    +---> [search-service]   (OpenSearch: products_idx)
    +---> [cart-service]     (Redis: cart_cache)
             | (REST)
             v
          [pricing-service]   (PostgreSQL: pricing_db)
             ^
             | (REST)
          [promotion-service] (PostgreSQL: promotion_db)
             ^
             | (REST)
          [order-service]     (PostgreSQL: order_db)
             |
             +---> (REST: Reserve) ---> [inventory-service] (PostgreSQL: inventory_db)
             +---> (REST: Charge)  ---> [payment-service]   (PostgreSQL: payment_db)
             |
             v (Kafka Events: OrderConfirmed)
          +--+------------------------+
          |                           |
          v                           v
     [shipping-service]      [notification-service]
             |                        |
             v (OrderDelivered)       v
      [return-service]        [reporting-service]
             |
             v (QC Passed)
      [refund-service]
```
Zero circular dependencies exist across the microservices ecosystem.
