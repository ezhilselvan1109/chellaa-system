# Complete Feature Inventory

This document provides a comprehensive inventory of all business features across the single-owner e-commerce platform.

---

## 1. Customer & Identity
- **CUST-01**: Customer self-registration (email/phone + password).
- **CUST-02**: Email and SMS OTP verification.
- **CUST-03**: Secure login with JWT issue and HTTP-only refresh cookie.
- **CUST-04**: Password reset flow with cryptographic time-limited tokens.
- **CUST-05**: Customer profile view and update (name, phone, avatar).
- **CUST-06**: Customer address book (multiple addresses, default shipping/billing flag).
- **CUST-07**: Communication preferences (Email, SMS, WhatsApp consent flags).
- **CUST-08**: Customer account security (active sessions, device logout, password change).
- **CUST-09**: Account deactivation and GDPR/privacy data deletion request.

## 2. Product Catalog Management
- **CAT-01**: Product creation and lifecycle management (Draft, Published, Archived).
- **CAT-02**: Product variant management (SKU, barcode, size, color, material, weight).
- **CAT-03**: Hierarchical category tree (multi-level categories, slug generation).
- **CAT-04**: Brand catalog (name, logo, description, brand stories).
- **CAT-05**: Dynamic attribute groups & custom specifications (e.g. Technical Specs, Fabric Care).
- **CAT-06**: Media asset management (high-res images, zoom thumbnails, video embeds).
- **CAT-07**: Product tags and curated collections (e.g. Summer Essentials, Best Sellers).
- **CAT-08**: Related, similar, and frequently-bought-together product associations.
- **CAT-09**: Scheduled publishing (auto-publish at future date/time).

## 3. Search & Discovery
- **SRC-01**: Full-text catalog search powered by OpenSearch with typo tolerance.
- **SRC-02**: Instant autocomplete and query suggestions.
- **SRC-03**: Faceted filtering by category, brand, price range, ratings, and attributes.
- **SRC-04**: Multi-criteria sorting (Relevance, Price Low/High, Newest, Top Rated).
- **SRC-05**: Popular searches and customer recent search history.
- **SRC-06**: Real-time search index synchronization via Kafka CDC events.

## 4. Pricing & Promotions
- **PRC-01**: Base MRP and selling price management per SKU.
- **PRC-02**: Scheduled future pricing updates.
- **PRC-03**: Tiered and volume-based pricing rules.
- **PRC-04**: GST and sales tax calculation engine by product tax category.
- **PRC-05**: Promotional campaign manager (Flash sales, seasonal clearance, festival sales).
- **PRC-06**: Coupon code engine (fixed discount, percentage, min cart value, max cap).
- **PRC-07**: Coupon eligibility rules (first-order only, category-specific, user-specific).
- **PRC-08**: Global and per-user coupon redemption caps.
- **PRC-09**: Deterministic discount stacking and conflict resolution rules.

## 5. Cart & Wishlist
- **CRT-01**: Add, update quantity, and remove SKU items in cart.
- **CRT-02**: Anonymous guest cart with automatic merging into customer cart on login.
- **CRT-03**: Save for later shelf.
- **CRT-04**: Real-time stock revalidation and price-change alerts in cart view.
- **CRT-05**: Cart-level coupon application and live discount preview.
- **CRT-06**: Estimated tax and shipping cost calculator.
- **CRT-07**: Customer wishlist management (multiple named lists, private/public).
- **CRT-08**: Move items between wishlist and cart.
- **CRT-09**: Back-in-stock and price-drop customer alerts.

## 6. Checkout & Inventory Allocation
- **CHK-01**: Step-by-step idempotent checkout flow.
- **CHK-02**: Shipping and billing address selection with pincode serviceability check.
- **CHK-03**: Courier shipping method selection (Standard, Express).
- **CHK-04**: Atomic inventory reservation across warehouses with 15-minute TTL hold.
- **CHK-05**: Automatic reservation expiration and stock release daemon.
- **CHK-06**: Final checkout order summary validation (preventing stale price submission).
- **CHK-07**: Duplicate checkout submission lock (Redis idempotency key).

## 7. Orders & Payments
- **ORD-01**: Order creation and immutable purchase snapshot generation.
- **ORD-02**: Order state machine (Pending Payment, Paid, Processing, Shipped, Delivered, Cancelled).
- **ORD-03**: Customer order history with timeline tracking.
- **ORD-04**: Customer-initiated self-service cancellation (before dispatch).
- **ORD-05**: Tokenized payment gateway initiation (Razorpay/Stripe).
- **ORD-06**: Cryptographic webhook signature verification and async payment confirmation.
- **ORD-07**: Payment failure handling and automatic saga compensation (stock release).
- **ORD-08**: Payment reconciliation job for orphaned/pending transactions.
- **ORD-09**: Automated GST tax invoice PDF generation and storage in S3.

## 8. Fulfillment, Shipping, Returns & Refunds
- **SHP-01**: Warehouse allocation and fulfillment routing.
- **SHP-02**: Logistics courier dispatch manifest and AWB tracking number assignment.
- **SHP-03**: Real-time shipment milestone tracking (Dispatched, In Transit, Out for Delivery, Delivered).
- **RET-01**: Return request initiation with policy window check (e.g. 7 days from delivery).
- **RET-02**: Return reason capture, proof image upload, and administrative approval queue.
- **RET-03**: Return pickup scheduling and tracking.
- **RET-04**: Warehouse return inspection and item condition grading.
- **REF-01**: Automated refund trigger to original payment method or customer wallet.
- **REF-02**: Partial refund calculations for returned items.
- **REF-03**: Gateway refund tracking, reconciliation, and customer confirmation.

## 9. Customer Reviews & Ratings
- **REV-01**: Verified-purchase rating and review submission.
- **REV-02**: Customer photo and video upload with reviews.
- **REV-03**: Administrator content moderation queue (Approve, Reject, Flag).
- **REV-04**: Helpful vote counters and abuse reporting.
- **REV-05**: Aggregate star rating calculation and breakdown charts.

## 10. Multi-Channel Notifications
- **NOT-01**: Versioned, localized message templates (`en-IN`, `ta-IN`).
- **NOT-02**: Transactional email dispatch via SES/SendGrid.
- **NOT-03**: Transactional SMS dispatch via Twilio/Gupshup.
- **NOT-04**: WhatsApp Business API notification dispatch using approved utility templates.
- **NOT-05**: Push notification dispatch via Firebase Cloud Messaging (FCM).
- **NOT-06**: In-app customer notification inbox with read/unread tracking.
- **NOT-07**: Delivery tracking webhooks and retry with exponential backoff.

## 11. Administration Operations Portal
- **ADM-01**: Single-sign-on (SSO) and RBAC for internal staff (`ROLE_ADMIN`, `ROLE_OPERATOR`, `ROLE_SUPPORT`).
- **ADM-02**: Real-time sales, order, and fulfillment operations dashboard.
- **ADM-03**: Full catalog, pricing, and campaign management screens.
- **ADM-04**: Warehouse inventory management, stock adjustments, and transfers.
- **ADM-05**: Order management, manual hold/release, and shipping label generation.
- **ADM-06**: Customer support CRM view (order lookup, timeline, refund trigger).
- **ADM-07**: Multi-channel notification template editor and preview tool.
- **ADM-08**: Immutable audit log of all administrative actions.

## 12. Reporting & Analytics
- **REP-01**: Daily, weekly, monthly, and annual sales and revenue reports.
- **REP-02**: Inventory turnover, low-stock forecasts, and stockout frequency.
- **REP-03**: Campaign and coupon performance analytics (ROI, redemption rates).
- **REP-04**: Customer acquisition, churn, and Average Order Value (AOV) metrics.
- **REP-05**: Return and refund root cause analysis by SKU/category.
