# Requirements Traceability Matrix

This matrix establishes bidirectional traceability from high-level functional requirements down to domain aggregates, owning services, data stores, contracts, workflows, and automated test specifications.

---

## Traceability Mapping Table

| Req ID | Business Capability | Bounded Context | Owning Microservice | Authoritative Store | REST / Event Contract | Verification Test Suite |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **FR-CUST-01** | Customer Registration | Identity & Access | `identity-service` | PostgreSQL (`identity_db`) | `POST /api/v1/auth/register` | `IdentityServiceAuthTests.java` |
| **FR-CUST-02** | OTP Verification | Identity & Access | `identity-service` | Redis / PostgreSQL | `POST /api/v1/auth/verify-otp` | `OtpVerificationIT.java` |
| **FR-CUST-03** | JWT Session / Token | Identity & Access | `identity-service` | Redis / PostgreSQL | `POST /api/v1/auth/login` | `TokenLifecycleIT.java` |
| **FR-CUST-04** | Customer Address Book| Customer Profile | `customer-service` | PostgreSQL (`customer_db`) | `GET/POST /api/v1/customers/addresses` | `CustomerAddressIT.java` |
| **FR-CAT-01** | Product & Variant Mgmt | Product Catalog | `catalog-service` | PostgreSQL (`catalog_db`) | `POST /api/v1/products` | `ProductManagementIT.java` |
| **FR-CAT-03** | Scheduled Publishing | Product Catalog | `catalog-service` | PostgreSQL (`catalog_db`) | Event: `CatalogItemPublished` | `ScheduledPublishingIT.java`|
| **FR-SRC-01** | Full-Text Typo Search| Catalog Search | `search-service` | OpenSearch (`products_idx`) | `GET /api/v1/search` | `SearchQueryIT.java` |
| **FR-PRC-01** | Selling Price Calculation| Pricing Engine | `pricing-service` | PostgreSQL & Redis | `POST /api/v1/pricing/calculate` | `PriceCalculationTest.java` |
| **FR-PRO-01** | Coupon Redemption | Promotion Engine | `promotion-service` | PostgreSQL & Redis | `POST /api/v1/coupons/validate` | `CouponValidationIT.java` |
| **FR-CRT-01** | Guest Cart Management| Cart Lifecycle | `cart-service` | Redis (`cart_cache`) | `GET/POST /api/v1/carts/guest` | `GuestCartIT.java` |
| **FR-CRT-02** | Guest Cart Merge | Cart Lifecycle | `cart-service` | Redis & PostgreSQL | `POST /api/v1/carts/merge` | `CartMergeIT.java` |
| **FR-CHK-01** | Idempotent Checkout | Order Management | `order-service` | PostgreSQL (`order_db`) | `POST /api/v1/orders/checkout` | `CheckoutIdempotencyIT.java`|
| **FR-CHK-02** | Atomic Stock Hold | Inventory Mgmt | `inventory-service` | PostgreSQL & Redis | `POST /api/v1/inventory/reserve` | `InventoryReservationIT.java`|
| **NFR-SCAL-02**| Prevent Overselling | Inventory Mgmt | `inventory-service` | PostgreSQL (`inventory_db`)| Event: `StockReserved` | `OversellingConcurrencyIT.java`|
| **FR-ORD-01** | Order State Machine | Order Management | `order-service` | PostgreSQL (`order_db`) | Event: `OrderCreated`, `OrderPaid` | `OrderStateMachineTest.java`|
| **FR-PAY-01** | Tokenized Payment | Payment Processing| `payment-service` | PostgreSQL (`payment_db`) | `POST /api/v1/payments/initiate` | `PaymentInitiationIT.java` |
| **FR-PAY-02** | Webhook Sig Verify | Payment Processing| `payment-service` | PostgreSQL (`payment_db`) | `POST /api/v1/payments/webhook` | `PaymentWebhookSignatureIT.java`|
| **FR-SHP-01** | Serviceability & AWB | Shipping & Logistic| `shipping-service` | PostgreSQL (`shipping_db`) | `POST /api/v1/shipments/create` | `ShippingDispatchIT.java` |
| **FR-RET-01** | Return Request Policy| Return Management | `return-service` | PostgreSQL (`return_db`) | `POST /api/v1/returns/request` | `ReturnPolicyWindowIT.java` |
| **FR-REF-01** | Automated Gateway Refund| Refund Management| `refund-service` | PostgreSQL (`refund_db`) | `POST /api/v1/refunds/process` | `RefundExecutionIT.java` |
| **FR-NOT-01** | Multi-Channel Dispatch| Notification Platform| `notification-service` | MongoDB & PostgreSQL | Event: `NotificationRequested` | `NotificationChannelIT.java`|
| **FR-NOT-04** | WhatsApp Utility Msg | Notification Platform| `notification-service` | MongoDB (`templates_db`) | Provider: WhatsApp Cloud API | `WhatsAppAdapterIT.java` |
