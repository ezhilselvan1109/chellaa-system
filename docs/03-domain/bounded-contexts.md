# Bounded Contexts

This document formalizes the Bounded Contexts of the e-commerce domain, defining the ubiquitous language, context boundaries, and Context Mapping relationships.

---

## 1. Bounded Context Definitions

### 1.1 Identity & Access Context
- **Ubiquitous Language**: Account, Credentials, JWT Token, Refresh Token, Role, Scope, MFA, OTP, Lockout.
- **Responsibility**: Authenticating customers and staff, issuing signed cryptographic tokens, enforcing session lifecycles.

### 1.2 Customer Context
- **Ubiquitous Language**: Customer Profile, Address Book, Shipping Address, Billing Address, Communication Consent.
- **Responsibility**: Maintaining customer personal details, saved shipping destinations, and privacy consent preferences.

### 1.3 Product Catalog Context
- **Ubiquitous Language**: Product, Variant, SKU, Category, Brand, Attribute, Specification, Media Asset, Publishing Schedule.
- **Responsibility**: Managing the master catalog of merchandise, brand assets, and multi-media galleries.

### 1.4 Catalog Search Context
- **Ubiquitous Language**: Index Document, Query, Autocomplete Suggestion, Filter Facet, Relevance Score, Synonyms.
- **Responsibility**: Maintaining the derived OpenSearch index for high-speed catalog discovery.

### 1.5 Pricing Context
- **Ubiquitous Language**: MRP, Base Price, Selling Price, Price Schedule, Currency, HSN Code, GST Tax Rate.
- **Responsibility**: Authoritative computation of SKU prices, currency conversions, and tax breakdowns.

### 1.6 Promotion Context
- **Ubiquitous Language**: Campaign, Flash Sale, Coupon Code, Cart Subtotal, Discount Cap, Redemption Count, Stacking Rule.
- **Responsibility**: Managing marketing discount rules, validating promotional eligibility, and enforcing redemption limits.

### 1.7 Cart Context
- **Ubiquitous Language**: Cart, Cart Item, Guest Cart, Cart Merge, Line Subtotal, Saved for Later.
- **Responsibility**: Ephemeral customer and guest item selections before formal checkout.

### 1.8 Inventory Context
- **Ubiquitous Language**: Warehouse, Stock On-Hand, Stock Reservation, Safety Stock, Stock Movement, Stock Ledger.
- **Responsibility**: Tracking real-time physical inventory across warehouses and managing atomic allocation holds.

### 1.9 Order Context
- **Ubiquitous Language**: Order, Order Line Item, Order Snapshot, Order State Machine, Cancellation, Checkout Saga.
- **Responsibility**: Orchestrating checkout, preserving immutable order contracts, and managing order state transitions.

### 1.10 Payment Context
- **Ubiquitous Language**: Payment Session, Gateway Transaction, Webhook, HMAC Signature, Charge, Reconciliation.
- **Responsibility**: Tokenized integration with external payment gateways and cryptographic webhook verification.

### 1.11 Shipping & Logistics Context
- **Ubiquitous Language**: Shipment, Delivery Zone, Pincode Serviceability, Courier, AWB Tracking Number, Delivery POD.
- **Responsibility**: Generating carrier manifests, validating destination serviceability, and tracking package transit.

### 1.12 Return & Inspection Context
- **Ubiquitous Language**: Return Request, Return Policy Window, Return Reason, Reverse Pickup, QC Inspection.
- **Responsibility**: Enforcing the 7-day return policy and managing physical warehouse item grading.

### 1.13 Refund Context
- **Ubiquitous Language**: Refund, Payout, Credit Note, Clearing Account, Idempotent Refund Token.
- **Responsibility**: Executing ledger-backed electronic refunds to original payment methods.

### 1.14 Review & Rating Context
- **Ubiquitous Language**: Review, Star Rating, Verified Buyer, Helpful Vote, Moderation Queue.
- **Responsibility**: Capturing verified customer feedback and publishing aggregated product ratings.

### 1.15 Notification Context
- **Ubiquitous Language**: Notification Template, Channel Adapter, Locale, Delivery Status, Variable Interpolation.
- **Responsibility**: Templating and dispatching multi-channel communications (Email, SMS, WhatsApp, Push, In-App).

### 1.16 Reporting & Analytics Context
- **Ubiquitous Language**: Read Model, Aggregation, Daily Sales, Gross Merchandise Value, Inventory Velocity, Cohort.
- **Responsibility**: CQRS materialized analytical views derived asynchronously from domain event streams.

---

## 2. Context Map

```text
[Identity & Access] <--(Customer Token)-- [API Gateway]
                                                 |
[Customer Context] <--(Upstream/Downstream)-------+
[Product Catalog] ---> (Kafka Domain Events) ---> [Search Context]
[Product Catalog] ---> (Shared SKU Ref) --------> [Pricing Context]
[Pricing Context] ---> (Price Lookups) ---------> [Cart Context]
[Promotion Context] -> (Coupon Validation) -----> [Cart Context]
                                                 |
                                         (Checkout Trigger)
                                                 v
                                          [Order Context] (Saga Orchestrator)
                                           /      |       \
               (Reserve Stock)            /       |        \  (Authorize Payment)
                                         v        v         v
                     [Inventory Context]   [Shipping]    [Payment Context]
                                                  |              |
                                            (Delivered)     (Webhook Paid)
                                                  v              v
                                           [Return Context] [Notification]
                                                  |
                                            (QC Approved)
                                                  v
                                           [Refund Context]
```
