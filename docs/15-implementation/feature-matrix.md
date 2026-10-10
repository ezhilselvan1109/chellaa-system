# Feature Matrix

Comprehensive status, ownership, and contract mapping of all system capabilities.

---

## 1. Status Taxonomy
- `Proposed`: Ideated but unrefined.
- `Documented`: Fully specified across requirements, domain, schemas, and contracts (**Current Phase 0 Status**).
- `Approved`: Architecture and design formally signed off for implementation.
- `In Progress`: Active code authoring in progress.
- `Implemented`: Code written and passing local builds.
- `Verified`: Formally verified with passing automated test execution.
- `Blocked`: Execution prevented by unfulfilled dependency.
- `Deferred`: Explicitly scheduled for later phase.

---

## 2. Feature Matrix

| Feature ID | Feature Name | Requirements | Owning Service | Authoritative Store | REST / Event Contract | Applicable Workflow | Applicable Skills | Phase | Status | Prerequisites |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **F-01** | Architecture Baseline | Master Prompt | All Services | All Stores | ADR-001 - ADR-012 | Documentation | Architecture, Domain | 0 | **Verified** | None |
| **F-02** | Customer Registration | FR-CUST-01 | `identity-service` | `identity_db` | `POST /api/v1/auth/register` | Feature | SpringBoot, Security | 2 | **Documented** | Phase 1 Foundation |
| **F-03** | SMS/Email OTP Auth | FR-CUST-02 | `identity-service` | Redis / `identity_db` | `POST /api/v1/auth/verify-otp`| Feature | SpringBoot, Security | 2 | **Documented** | F-02 |
| **F-04** | JWT & Refresh Session | FR-CUST-03 | `identity-service` | `identity_db` | `POST /api/v1/auth/refresh` | API | Security, API Design | 2 | **Documented** | F-03 |
| **F-05** | Customer Address Book | FR-CUST-04 | `customer-service` | `customer_db` | `POST /api/v1/addresses` | Feature | SpringBoot, Database | 2 | **Documented** | F-04 |
| **F-06** | Product Catalog & SKUs| FR-CAT-01 | `catalog-service` | `catalog_db` | `POST /api/v1/products` | Feature | SpringBoot, Database | 3 | **Documented** | Phase 1 Foundation |
| **F-07** | OpenSearch Catalog Sync| FR-SRC-04 | `search-service` | OpenSearch | `commerce.catalog.updated.v1`| Event | Kafka, Database | 4 | **Documented** | F-06 |
| **F-08** | Full-Text Typo Search | FR-SRC-01 | `search-service` | OpenSearch | `GET /api/v1/search` | API | Performance, API Design | 4 | **Documented** | F-07 |
| **F-09** | SKU Price Calculation | FR-PRC-01 | `pricing-service` | `pricing_db` | `POST /api/v1/pricing/calc` | Feature | SpringBoot, Testing | 5 | **Documented** | F-06 |
| **F-10** | Promotional Coupons | FR-PRO-01 | `promotion-service`| `promotion_db` | `POST /api/v1/coupons/apply` | Feature | SpringBoot, Concurrency| 5 | **Documented** | F-09 |
| **F-11** | Guest & Customer Cart | FR-CRT-01 | `cart-service` | Redis / `cart_db` | `POST /api/v1/cart/items` | Feature | SpringBoot, Redis | 6 | **Documented** | F-09 |
| **F-12** | Guest Cart Merge | FR-CRT-02 | `cart-service` | Redis / `cart_db` | `POST /api/v1/cart/merge` | Feature | SpringBoot, Testing | 6 | **Documented** | F-04, F-11 |
| **F-13** | Atomic Stock Hold | FR-CHK-02 | `inventory-service`| `inventory_db` | `POST /api/v1/inventory/reserve`| Feature | Database, Concurrency | 7 | **Documented** | F-06 |
| **F-14** | Idempotent Checkout | FR-CHK-01 | `order-service` | `order_db` | `POST /api/v1/orders/checkout` | Feature | Architecture, Testing | 7 | **Documented** | F-11, F-13 |
| **F-15** | Checkout Saga Orchestrator| FR-ORD-01 | `order-service` | `order_db` | Saga State Machine | Feature | SpringBoot, Testing | 8 | **Documented** | F-13, F-14 |
| **F-16** | Tokenized Gateway Pay | FR-PAY-01 | `payment-service` | `payment_db` | `POST /api/v1/payments/init` | Feature | Security, API Design | 8 | **Documented** | F-15 |
| **F-17** | Webhook HMAC Verify | FR-PAY-02 | `payment-service` | `payment_db` | `POST /api/v1/payments/webhook`| Feature | Security, Testing | 8 | **Documented** | F-16 |
| **F-18** | Tax Invoice Generation | FR-ORD-03 | `order-service` | S3 / MinIO | Event: `OrderConfirmed` | Feature | SpringBoot, PDF | 8 | **Documented** | F-17 |
| **F-19** | Logistics Carrier AWB | FR-SHP-02 | `shipping-service` | `shipping_db` | `POST /api/v1/shipments` | Feature | SpringBoot, API Design | 9 | **Documented** | F-17 |
| **F-20** | 7-Day Return Policy | FR-RET-01 | `return-service` | `return_db` | `POST /api/v1/returns` | Feature | SpringBoot, Database | 10 | **Documented** | F-19 |
| **F-21** | Warehouse QC Inspection| FR-RET-02 | `return-service` | `return_db` | `POST /api/v1/returns/qc` | Feature | SpringBoot, Domain | 10 | **Documented** | F-20 |
| **F-22** | Automated Payout Refund| FR-REF-01 | `refund-service` | `refund_db` | `POST /api/v1/refunds` | Feature | SpringBoot, Security | 10 | **Documented** | F-21 |
| **F-23** | Multi-Channel Alerts | FR-NOT-01 | `notification-service`| MongoDB / PG | Event: `NotificationRequested` | Notification | Notification, Kafka | 11 | **Documented** | F-17 |
| **F-24** | WhatsApp Utility Templ | FR-NOT-04 | `notification-service`| MongoDB | Meta WhatsApp Cloud API | Notification | Notification, Testing | 11 | **Documented** | F-23 |
| **F-25** | Verified Buyer Reviews | FR-REV-01 | `review-service` | `review_db` | `POST /api/v1/reviews` | Feature | SpringBoot, Database | 12 | **Documented** | F-19 |
| **F-26** | Admin Operations Web | FR-ADM-01 | `apps/admin-web` | Client SPA | REST Endpoints | Feature | React, Security | 13 | **Documented** | F-06, F-15 |
| **F-27** | CQRS Sales Read-Model | FR-REP-01 | `reporting-service`| `reporting_db` | `commerce.orders.confirmed.v1`| Feature | Kafka, Database | 14 | **Documented** | F-17 |
| **F-28** | Closed-Loop Wallet | OQ-02 | `wallet-service` | `wallet_db` | Ledger Double-Entry | Feature | Security, Database | 15 | **Deferred** | F-22 |
| **F-29** | Chaos & Load Benchmarking| NFR-PERF-03 | All Services | All Stores | k6 5,000 RPS Test | Testing | Performance, Testing | 16 | **Documented** | F-15, F-27 |
