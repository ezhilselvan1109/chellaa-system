# Service Database Ownership Matrix

Defines database credentials, schemas, and store assignments across all microservices.

---

## 1. Ownership & Credential Table

| Service | Database Name | Default User | Port | Migration Tool | Store Engine |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `identity-service` | `identity_db` | `identity_user` | 5432 | Flyway | PostgreSQL 16 |
| `customer-service` | `customer_db` | `customer_user` | 5432 | Flyway | PostgreSQL 16 |
| `catalog-service` | `catalog_db` | `catalog_user` | 5432 | Flyway | PostgreSQL 16 |
| `search-service` | `products_idx` | `opensearch_app` | 9200 | OpenSearch Index Templates | OpenSearch 2.13 |
| `pricing-service` | `pricing_db` | `pricing_user` | 5432 | Flyway | PostgreSQL 16 & Redis |
| `promotion-service`| `promotion_db` | `promotion_user`| 5432 | Flyway | PostgreSQL 16 & Redis |
| `cart-service` | `cart_db` | `cart_user` | 5432 / 6379| Flyway | PostgreSQL 16 & Redis |
| `inventory-service`| `inventory_db` | `inventory_user`| 5432 | Flyway | PostgreSQL 16 & Redis |
| `order-service` | `order_db` | `order_user` | 5432 | Flyway | PostgreSQL 16 |
| `payment-service` | `payment_db` | `payment_user` | 5432 | Flyway | PostgreSQL 16 |
| `shipping-service` | `shipping_db` | `shipping_user` | 5432 | Flyway | PostgreSQL 16 |
| `return-service` | `return_db` | `return_user` | 5432 | Flyway | PostgreSQL 16 |
| `refund-service` | `refund_db` | `refund_user` | 5432 | Flyway | PostgreSQL 16 |
| `review-service` | `review_db` | `review_user` | 5432 | Flyway | PostgreSQL 16 |
| `notification-service`| `notification_db`| `notif_user` | 27017/5432| Flyway / Mongock | MongoDB & PostgreSQL |
| `reporting-service`| `reporting_db` | `reporting_user`| 5432 | Flyway | PostgreSQL 16 |
