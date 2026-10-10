# Microservice Boundaries & Evaluation

This document evaluates candidate microservices, establishes concrete service boundaries, and maps business responsibilities to architectural services.

---

## 1. Candidate Boundary Evaluation

To prevent excessive microservice fragmentation while preserving autonomous deployability and strict data ownership, the 18 candidate capabilities have been evaluated:

| Candidate Boundary | Architectural Decision | Justification |
| :--- | :--- | :--- |
| **API Gateway** | **Autonomous Service** (`api-gateway`) | Critical edge proxy for SSL termination, JWT verification, rate limiting, and routing. |
| **Identity vs. Customer** | **Separate Microservices** | Strict separation of security credentials/IAM (`identity-service`) from customer profile/address data (`customer-service`) isolates security breaches. |
| **Catalog vs. Search** | **Separate Microservices** | Relational ACID catalog writes (`catalog-service`) have completely different operational characteristics from distributed OpenSearch queries (`search-service`). |
| **Pricing vs. Promotion** | **Separate Microservices** | Base price and tax calculations (`pricing-service`) require ultra-low latency; marketing campaigns and coupon caps (`promotion-service`) have complex stateful rule engines. |
| **Cart Service** | **Autonomous Service** (`cart-service`) | High write frequency (add/remove items) with short-lived Redis session state; decoupled from permanent order state. |
| **Inventory Service** | **Autonomous Service** (`inventory-service`) | High concurrency locking on physical stock across warehouses; must scale independently during flash sales. |
| **Order Service** | **Autonomous Service** (`order-service`) | Critical core domain and Saga Orchestrator coordinating checkout, payment, and order fulfillment progression. |
| **Payment Service** | **Autonomous Service** (`payment-service`) | Encapsulates external payment gateway integrations, webhook signature cryptography, and gateway token management. |
| **Shipping Service** | **Autonomous Service** (`shipping-service`) | Integrates with 3PL logistics carriers, tracks AWBs, and manages warehouse dispatch manifests. |
| **Return vs. Refund** | **Separate Microservices** | Return workflow (`return-service`) manages customer claims and physical warehouse QC inspection; Refund workflow (`refund-service`) manages financial ledger credits and banking payouts. |
| **Review Service** | **Autonomous Service** (`review-service`) | High-volume user-generated content and moderation queues decoupled from transactional order pipelines. |
| **Notification Service** | **Autonomous Service** (`notification-service`) | Multi-channel communication engine (Email, SMS, WhatsApp, Push) driven asynchronously by Kafka domain events. |
| **Reporting Service** | **Autonomous Service** (`reporting-service`) | CQRS analytical read-model decoupled from transactional operational databases. |
| **Wishlist & Saved Items** | **Merged into Cart Service** | Low complexity; fits cleanly into customer shopping basket domain without warranting an autonomous microservice. |
| **Wallet & Gift Cards** | **Deferred to Phase 15** | Advanced capability; deferred until core commerce transactions are production-verified. |

---

## 2. Approved Service Inventory

The platform architecture formalizes **16 Core Microservices**:
1. `api-gateway`
2. `identity-service`
3. `customer-service`
4. `catalog-service`
5. `search-service`
6. `pricing-service`
7. `promotion-service`
8. `cart-service`
9. `inventory-service`
10. `order-service`
11. `payment-service`
12. `shipping-service`
13. `return-service`
14. `refund-service`
15. `review-service`
16. `notification-service`
17. `reporting-service` (Analytical CQRS)
