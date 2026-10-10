# Data Ownership and Service Isolation

This document formalizes the strict Database-per-Service ownership policy, eliminating cross-database couplings.

---

## 1. Absolute Data Ownership Principle

1. **Exclusive Store Authority**: Every microservice possesses exclusive ownership over its assigned data store. No other microservice is permitted to connect, query, or mutate tables belonging to a foreign service.
2. **Strict Encapsulation**: Data needed by foreign services must be accessed via published REST APIs or published Kafka domain events.
3. **Prohibited Anti-Patterns**:
   - Cross-database SQL joins (`JOIN order_db.orders ON customer_db.customers`).
   - Shared database schemas between microservices.
   - Bypassing a service to directly update another service's tables.

---

## 2. Service Database Ownership Inventory

| Microservice | Authoritative Data Store | Logical Database Name | Data Classification |
| :--- | :--- | :--- | :--- |
| `identity-service` | PostgreSQL 16 | `identity_db` | Highly Confidential (Credentials, MFA) |
| `customer-service` | PostgreSQL 16 | `customer_db` | Confidential / PII (Profiles, Addresses) |
| `catalog-service` | PostgreSQL 16 | `catalog_db` | Internal / Public (Products, Categories) |
| `search-service` | OpenSearch 2.13 | `catalog_search_idx` | Public / Derived (Search index documents)|
| `pricing-service` | PostgreSQL 16 & Redis | `pricing_db` | Confidential (Price rules, HSN taxes) |
| `promotion-service`| PostgreSQL 16 & Redis | `promotion_db` | Confidential (Campaigns, Coupons, Budgets)|
| `cart-service` | Redis 7.2 & PostgreSQL| `cart_cache` / `cart_db` | Ephemeral & Customer Baskets |
| `inventory-service`| PostgreSQL 16 & Redis | `inventory_db` | Critical / Operational (Stock levels) |
| `order-service` | PostgreSQL 16 | `order_db` | Critical / Financial (Orders, Line items)|
| `payment-service` | PostgreSQL 16 | `payment_db` | Critical / Financial (Charges, Webhooks) |
| `shipping-service` | PostgreSQL 16 | `shipping_db` | Operational (Shipments, AWBs) |
| `return-service` | PostgreSQL 16 | `return_db` | Operational (Return claims, QC reports) |
| `refund-service` | PostgreSQL 16 | `refund_db` | Critical / Financial (Refund ledgers) |
| `review-service` | PostgreSQL 16 | `review_db` | Public / UGC (Ratings, Reviews) |
| `notification-service`| MongoDB 7.0 & PostgreSQL| `notification_db` | Operational (Templates, Logs) |
| `reporting-service`| PostgreSQL 16 (CQRS) | `reporting_db` | Internal Analytics (Sales aggregates) |
