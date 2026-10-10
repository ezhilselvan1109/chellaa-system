# Glossary

A unified lexicon of ubiquitous domain and technical terms used across documentation and codebases.

---

## 1. Domain Terminology

| Term | Definition |
| :--- | :--- |
| **Merchant / Owner** | The single enterprise entity operating the store and controlling all business operations. |
| **Customer** | An individual who creates an account or browses as a guest to purchase goods. |
| **Product** | The conceptual catalog entity representing a sellable commodity (e.g., "Men's Classic Cotton T-Shirt"). |
| **Product Variant** | A specific physical SKU belonging to a product representing distinct attributes such as Size, Color, or Material (e.g., "Men's Classic Cotton T-Shirt, Navy Blue, Size Large"). |
| **SKU (Stock Keeping Unit)** | The unique alphanumeric identifier for a specific purchasable variant, tracked in inventory. |
| **MRP (Maximum Retail Price)** | The manufacturer's declared maximum price printed on product packaging. |
| **Selling Price** | The current price offered to the consumer before cart-level coupons or taxes are applied. |
| **Inventory Allocation / Reservation** | A temporary hold placed on stock for a specific SKU during checkout to prevent overselling while payment is being processed. |
| **Available Stock** | Physical stock on hand minus currently active unexpired reservations. |
| **Safety Stock** | A buffer threshold below which a SKU is reported as low-stock to trigger reordering. |
| **Campaign** | A time-bounded promotional strategy (e.g. Flash Sale, Diwali Sale) offering category or product discounts. |
| **Coupon** | An alphanumeric code entered by a customer during checkout offering fixed or percentage discounts, subject to eligibility criteria. |
| **Cart** | An ephemeral or persistent collection of SKUs and quantities selected by a user prior to purchase. |
| **Guest Cart** | An anonymous cart stored in Redis/session before customer authentication. |
| **Cart Merge** | The process of harmonizing a guest cart into a customer's persistent database cart upon login. |
| **Checkout Saga** | The multi-service distributed transaction orchestrating stock reservation, tax calculation, coupon deduction, and payment authorization. |
| **Order** | The immutable legal contract created once checkout completes, capturing item snapshots, customer address, payment receipt, and fulfillment milestones. |
| **Invoice** | A legally binding tax document containing sequential invoice numbers, tax breakdown, and company GSTIN. |
| **Shipment / Fulfillment** | The physical packing and dispatching of ordered goods from a warehouse via a logistics carrier. |
| **Return** | A formal customer request to return delivered goods within the allowed return policy window. |
| **Refund** | The monetary reimbursement credited back to the customer's original payment method or store wallet. |

---

## 2. Technical & Architecture Terminology

| Term | Definition |
| :--- | :--- |
| **Bounded Context** | An explicit boundary within which a domain model applies, matching microservice ownership. |
| **Transactional Outbox** | A design pattern where events are written to an RDBMS table within the same ACID transaction as the business entity, then polled and published to Kafka. |
| **Saga Orchestrator** | A centralized coordinator (Order Service) that directs multi-service distributed workflows using commands, replies, and compensating actions. |
| **Compensating Action** | An operation that reverses the effect of a previously committed step in an incomplete saga (e.g., releasing reserved stock when payment fails). |
| **Idempotency Key** | A unique token passed in HTTP headers or event metadata allowing consumers to detect and safely ignore duplicate requests. |
| **Dead Letter Queue (DLQ)**| A Kafka topic where poison pills or messages exceeding maximum retry thresholds are routed for manual inspection. |
| **mTLS** | Mutual Transport Layer Security ensuring encrypted and authenticated inter-service communications. |
| **CQRS** | Command Query Responsibility Segregation, separating write transactions (PostgreSQL) from analytical read queries (Reporting Service). |
