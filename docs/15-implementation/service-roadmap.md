# Service Development Roadmap

Delivery order and milestone sequence across individual microservices.

---

## 1. Microservice Delivery Waves

### Wave 1: Edge & Identity
1. `api-gateway`: Edge proxy, SSL termination, Redis rate limiter.
2. `identity-service`: IAM, RS256 token signer, OTP verification.
3. `customer-service`: Profiles, address book, consent flags.

### Wave 2: Catalog & Discovery
4. `catalog-service`: Master catalog, variants, categories, brands.
5. `search-service`: OpenSearch indexing, full-text queries, faceting.

### Wave 3: Commerce & Pricing
6. `pricing-service`: SKU prices, HSN tax calculation engine.
7. `promotion-service`: Flash sales, coupons, budget caps.
8. `cart-service`: Redis guest carts, customer cart merging.

### Wave 4: Fulfillment Core
9. `inventory-service`: Multi-warehouse stock, atomic reservation holds.
10. `order-service`: Order state machine, Checkout Saga Orchestrator.
11. `payment-service`: Payment gateway integration, webhooks.
12. `shipping-service`: Logistics carrier integration, AWB tracking.

### Wave 5: Post-Purchase & Engagement
13. `return-service`: Return requests, warehouse QC inspection.
14. `refund-service`: Ledger-backed electronic refunds.
15. `notification-service`: Multi-channel dispatch engine.
16. `review-service`: Verified-buyer ratings and reviews.
17. `reporting-service`: Asynchronous analytics and read models.
