# Functional Requirements

This document specifies the core functional requirements of the single-owner e-commerce platform.

---

## 1. Customer & Authentication (FR-CUST)

- **FR-CUST-01**: The system shall permit users to register using an email address or mobile phone number paired with a secure password (minimum 8 characters, requiring mixed case, numbers, and symbols).
- **FR-CUST-02**: The system shall issue time-limited (10-minute validity) numeric OTPs for email and SMS phone verification.
- **FR-CUST-03**: The system shall authenticate valid credentials and return a signed RS256 JWT access token (15-minute TTL) and set a secure, HttpOnly, SameSite=Strict refresh token cookie.
- **FR-CUST-04**: The system shall permit customers to manage multiple shipping and billing addresses, designating exactly one primary default address per type.
- **FR-CUST-05**: The system shall allow customers to configure independent notification opt-in preferences for Email, SMS, and WhatsApp marketing channels, while keeping transactional notifications mandatory.

---

## 2. Catalog & Discovery (FR-CAT, FR-SRC)

- **FR-CAT-01**: The system shall model products with hierarchical category relationships, brand attributes, SKU variants, and dynamic attribute groups.
- **FR-CAT-02**: The system shall associate variants with specific options (e.g. Size: L, Color: Navy) where each variant maintains independent SKU, barcode, weight, and pricing.
- **FR-CAT-03**: The system shall support scheduled publishing of products and categories based on an admin-specified UTC timestamp.
- **FR-SRC-01**: The system shall provide full-text search across product titles, descriptions, categories, and tags using OpenSearch with Levenshtein distance 2 typo tolerance.
- **FR-SRC-02**: The system shall provide instant prefix-based autocomplete queries with sub-30ms response times.
- **FR-SRC-03**: The system shall allow filtering by category ID, brand ID, price range, average star rating, and variant attributes.
- **FR-SRC-04**: The system shall automatically synchronize catalog changes (creates, updates, deletes) to the search index via Kafka domain events within 2 seconds of database commit.

---

## 3. Pricing & Promotions (FR-PRC, FR-PRO)

- **FR-PRC-01**: The system shall calculate the effective selling price for a SKU by applying active flash sale campaigns, base discounts, and applicable GST tax rates.
- **FR-PRC-02**: The system shall maintain an immutable historical audit trail of all price adjustments with admin user attribution and effective timestamps.
- **FR-PRO-01**: The system shall evaluate coupon codes at checkout against eligibility criteria including: minimum cart subtotal, allowed product categories, customer first-order status, and global/per-user usage limits.
- **FR-PRO-02**: The system shall apply a deterministic discount stacking order: (1) Base Product Discount -> (2) Campaign Discount -> (3) Cart Coupon -> (4) Shipping Waiver. Competing campaign discounts shall apply only the highest discount percentage unless explicitly flagged as stackable.

---

## 4. Cart & Checkout (FR-CRT, FR-CHK)

- **FR-CRT-01**: The system shall allow unauthenticated visitors to maintain an anonymous guest cart stored in Redis with a 7-day inactivity expiration.
- **FR-CRT-02**: The system shall automatically merge items from a guest cart into the customer's persistent database cart upon login, handling duplicate SKUs by adding quantities up to available inventory limits.
- **FR-CRT-03**: The system shall revalidate stock availability and active pricing whenever the cart is loaded or modified, flagging price increases or depleted stock to the user.
- **FR-CHK-01**: The system shall require a valid `Idempotency-Key` header on all checkout submissions to prevent duplicate order placements.
- **FR-CHK-02**: The system shall atomically reserve inventory across specified warehouse nodes for a 15-minute window upon checkout initiation.
- **FR-CHK-03**: If payment is not completed within 15 minutes, an automated background reaper shall release the reserved inventory back to available stock.

---

## 5. Orders & Payments (FR-ORD, FR-PAY)

- **FR-ORD-01**: The system shall transition orders through a formal state machine: `PENDING_PAYMENT` -> `PAID` -> `PROCESSING` -> `SHIPPED` -> `DELIVERED`, or `CANCELLED`.
- **FR-ORD-02**: The system shall persist an immutable snapshot of item prices, product names, applied taxes, discounts, and customer shipping address at the exact moment of order placement.
- **FR-PAY-01**: The system shall initiate payment sessions via tokenized payment gateway APIs (Razorpay / Stripe) without handling raw card numbers.
- **FR-PAY-02**: The system shall verify HMAC signatures on incoming payment provider webhooks before marking orders as `PAID`.
- **FR-PAY-03**: If a payment fails or times out, the system shall execute compensating saga transactions to release reserved inventory and notify the customer.
- **FR-ORD-03**: Upon successful payment, the system shall generate an official GST tax invoice PDF and store it in object storage, providing a secure download link.

---

## 6. Fulfillment, Returns & Refunds (FR-SHP, FR-RET, FR-REF)

- **FR-SHP-01**: The system shall validate delivery pincodes against logistics serviceability zones before checkout completion.
- **FR-SHP-02**: The system shall assign logistics tracking numbers (AWB) and emit `OrderShipped` events when packages are handed to carriers.
- **FR-RET-01**: The system shall permit customers to initiate return requests within a 7-day policy window from the documented delivery timestamp.
- **FR-RET-02**: The system shall enforce warehouse inspection workflows where returned items are graded (Restockable, Damaged, Defective) before refund approval.
- **FR-REF-01**: The system shall initiate automated electronic refunds to the customer's original payment method upon return approval, tracking gateway refund IDs and reconciliation statuses.

---

## 7. Multi-Channel Notifications (FR-NOT)

- **FR-NOT-01**: The system shall dispatch order confirmations, payment receipts, shipment tracking links, and delivery updates via Email, SMS, and WhatsApp Business API.
- **FR-NOT-02**: The system shall render notifications using versioned, pre-compiled templates with strict variable validation against domain event payloads.
- **FR-NOT-03**: The system shall track delivery statuses (`ACCEPTED`, `SENT`, `DELIVERED`, `READ`, `FAILED`) via provider webhook ingestion.
