# Acceptance Criteria

This document codifies testable acceptance criteria using the Given-When-Then format across critical system capabilities.

---

## 1. Customer Authentication & Session

### AC-AUTH-01: Successful OTP Verification & Login
- **Given**: A registered customer with phone number `+919876543210` requests a login OTP.
- **When**: The customer submits the valid 6-digit OTP within 10 minutes of issuance.
- **Then**: The system responds with HTTP 200 OK, returns a valid JWT access token containing customer ID and `ROLE_CUSTOMER`, sets a secure `HttpOnly` refresh token cookie, and clears the used OTP.

### AC-AUTH-02: Expired OTP Submission
- **Given**: A customer received an OTP generated 11 minutes ago.
- **When**: The customer submits the expired OTP.
- **Then**: The system responds with HTTP 400 Bad Request (`RFC 7807`), error code `AUTH_OTP_EXPIRED`, and does not issue access tokens.

---

## 2. Cart & Guest Cart Merging

### AC-CART-01: Guest Cart Merging Upon Authentication
- **Given**: An anonymous visitor has SKU-101 (Quantity: 2) in their guest session cart.
- **And**: The customer's registered account already has SKU-101 (Quantity: 1) and SKU-102 (Quantity: 1) saved in the database cart.
- **When**: The customer logs into their account.
- **Then**: The system merges the carts so the customer now has SKU-101 (Quantity: 3) and SKU-102 (Quantity: 1), updates the database cart, invalidates the guest cart, and returns the unified cart.

### AC-CART-02: Cart Stock Depletion Warning
- **Given**: A customer has SKU-101 (Quantity: 3) in their cart.
- **When**: Available warehouse stock for SKU-101 drops to 1 before the customer checks out.
- **Then**: Loading the cart displays an alert `Only 1 item available in stock`, automatically clamps the cart item quantity to 1, and recalculates line subtotals.

---

## 3. Inventory Reservation & Concurrency

### AC-INV-01: Prevention of Overselling Under Concurrent Checkout
- **Given**: Exactly 5 units of SKU-500 are in available stock in Warehouse-1.
- **When**: 10 distinct customers concurrently trigger checkout for 1 unit of SKU-500 at the exact same millisecond.
- **Then**: Exactly 5 requests successfully receive an inventory reservation hold (HTTP 200/201).
- **And**: Exactly 5 requests are rejected with HTTP 409 Conflict and error code `INSUFFICIENT_STOCK`.
- **And**: The database available stock balance reaches exactly 0 with zero negative balances.

### AC-INV-02: Automatic Expiration of Unpaid Reservation
- **Given**: A customer reserved 2 units of SKU-300 during checkout at `10:00:00 UTC`.
- **When**: The clock reaches `10:15:01 UTC` and no payment confirmation has arrived.
- **Then**: The background reservation expiry sweeper marks the reservation as `EXPIRED`, releases 2 units back to available stock, and emits an `InventoryReservationExpired` event.

---

## 4. Checkout Saga & Idempotency

### AC-CHK-01: Idempotent Checkout Submission
- **Given**: A customer submits a checkout request with header `Idempotency-Key: e8a7c2b3-5491-4cf5-b12a-0bfa7d9e0341`.
- **When**: A network glitch causes the client to retransmit the exact same request with the same `Idempotency-Key` 500ms later.
- **Then**: The system does not create a duplicate order; it returns the exact same HTTP 201 response and existing `orderId` created by the first request.

### AC-CHK-02: Saga Compensation on Payment Failure
- **Given**: A customer creates order ORD-999 with 1 unit of SKU-200 reserved in inventory.
- **When**: The payment gateway returns a terminal failure webhook (`payment.failed`) or times out.
- **Then**: The Order Service Saga Orchestrator transitions the order to `CANCELLED`, issues a compensating command to Inventory Service to release the stock hold, and triggers a `PaymentFailed` notification to the customer.

---

## 5. Coupon Redemption Limits

### AC-PRO-01: Coupon Usage Cap Enforcement
- **Given**: Coupon `DIWALI500` has a global maximum redemption limit of 100 uses and has been redeemed 100 times.
- **When**: Customer-A attempts to apply `DIWALI500` to an eligible cart.
- **Then**: The system rejects the coupon with HTTP 422 Unprocessable Entity, error code `COUPON_GLOBAL_LIMIT_EXCEEDED`, and applies no discount.
