# Goals and Scope

## 1. Project Goals

1. **High Availability & Scale**: Sustain peak commercial flash-sale traffic up to 5,000 requests per second (RPS) with p95 catalog response times under 50ms and p99 checkout processing times under 200ms.
2. **Strict Financial & Inventory Integrity**: Ensure zero inventory overselling under high concurrent demand; maintain exact double-entry ledger consistency for payments, refunds, and discounts.
3. **Operational Autonomy**: Enable internal merchandising, marketing, warehouse, and customer-care teams to execute daily operations via a unified Admin Portal without requiring engineering intervention.
4. **Resilient Multi-Channel Communications**: Deliver transactional and marketing communications across Email, SMS, WhatsApp Business, Push, and In-App channels with real-time delivery tracking and fallback retries.
5. **Decoupled Architecture**: Maintain autonomous microservices with independent deployability, zero shared databases, and asynchronous Kafka-based event integration.

## 2. In-Scope Capabilities

- **Customer Identity & Access**: Self-registration, OTP/email verification, password reset, JWT session management, customer address book, communication preferences.
- **Product Catalog Management**: Products, variants, SKUs, category trees, brand catalogs, rich attributes, media galleries (images, videos), and scheduled publishing.
- **Discovery & Search**: Full-text search with typo tolerance, autocomplete suggestions, faceted filtering, dynamic sorting, and analytics-driven relevance tuning.
- **Pricing & Promotions Engine**: Scheduled base pricing, tiered pricing, percentage/fixed discounts, flash sale campaigns, coupon redemption with usage caps, and discount conflict resolution.
- **Cart & Wishlist**: Persistent authenticated carts, guest cart conversion, live price and stock revalidation, and multiple customer wishlists with price-drop alerts.
- **Checkout & Inventory**: Address selection, shipping rate calculations, tax estimation, coupon validation, atomic stock reservation across single/multiple warehouses, and idempotency guarantees.
- **Orders & Payments**: Comprehensive order state machine, tokenized payment gateway integration, webhook signature verification, reconciliation, and automated invoice PDF generation.
- **Fulfillment & Logistics**: Pincode serviceability checks, delivery zones, shipment tracking, courier integration, and dispatch manifests.
- **Returns & Refunds**: Return eligibility window validation, pickup scheduling, warehouse inspection, and automated refund processing to original payment methods.
- **Reviews & Ratings**: Verified-purchase customer reviews, image uploads, star ratings, and administrative moderation queues.
- **Multi-Channel Notifications**: Versioned templates, localized rendering (`en-IN`, `ta-IN`), provider adapters (Email, SMS, WhatsApp Business, Push), and delivery tracking.
- **Administrative Portal**: Complete management dashboards for products, pricing, campaigns, orders, inventory, customers, reviews, and reporting.
- **Reporting & Business Intelligence**: Event-driven read models for daily/monthly sales, gross merchandise value (GMV), average order value (AOV), inventory velocity, and customer retention.

## 3. Explicit Out-of-Scope Items (Non-Goals)

- **Marketplace / Multi-Vendor Features**: Seller registration, vendor portals, vendor commissions, vendor payouts, and multi-seller order routing are strictly excluded.
- **In-House Raw Payment Processing**: Storing raw credit card numbers or processing PANs directly is strictly prohibited. The system exclusively integrates tokenized payment gateways (e.g. Stripe, Razorpay) via hosted fields/APIs.
- **Decentralized Cryptocurrencies**: Blockchain payments, cryptocurrency wallets, and NFT tokens are out of scope.
