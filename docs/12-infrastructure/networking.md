# Networking Architecture

Container network topology, port allocations, and service discovery.

---

## 1. Port Allocation Table

| Service / Component | Container Internal Port | Local Host Mapping | Protocol |
| :--- | :--- | :--- | :--- |
| `api-gateway` | 8080 | 8080 | HTTP/2 |
| `customer-web` | 3000 | 3000 | HTTP |
| `admin-web` | 3001 | 3001 | HTTP |
| `identity-service` | 8081 | 8081 | HTTP |
| `customer-service` | 8082 | 8082 | HTTP |
| `catalog-service` | 8083 | 8083 | HTTP |
| `pricing-service` | 8084 | 8084 | HTTP |
| `promotion-service` | 8085 | 8085 | HTTP |
| `cart-service` | 8086 | 8086 | HTTP |
| `inventory-service` | 8087 | 8087 | HTTP |
| `order-service` | 8088 | 8088 | HTTP |
| `payment-service` | 8089 | 8089 | HTTP |
| `shipping-service` | 8090 | 8090 | HTTP |
| `return-service` | 8091 | 8091 | HTTP |
| `refund-service` | 8092 | 8092 | HTTP |
| `review-service` | 8093 | 8093 | HTTP |
| `notification-service` | 8094 | 8094 | HTTP |
| `reporting-service` | 8095 | 8095 | HTTP |
| `postgresql` | 5432 | 5432 | TCP |
| `redis` | 6379 | 6379 | TCP |
| `kafka` | 9092 | 9092 | TCP |
| `opensearch` | 9200 | 9200 | HTTP |
| `minio` | 9000 / 9001 | 9000 / 9001 | HTTP |
