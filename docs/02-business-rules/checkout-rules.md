# Checkout Business Rules

## 1. Checkout State & Pre-Conditions

1. **Authentication Requirement**: Checkout requires an authenticated customer account. Anonymous guest checkout is not permitted; guest carts must convert via OTP or login before checkout initiation.
2. **Mandatory Checkout Inputs**:
   - Valid delivery address with a serviceable pincode.
   - Selected delivery shipping method.
   - Non-empty cart with verified in-stock items.
   - Client-generated `Idempotency-Key` UUID.

## 2. Distributed Checkout Coordination

1. **Atomic Price & Promotion Locking**:
   - The Order Service calls Pricing Service, Promotion Service, and Shipping Service to generate an authoritative final checkout summary.
   - Total calculation formula:
     $$\text{Final Total} = \sum(\text{Item MRP} - \text{Product Discounts}) - \text{Coupon Discount} + \text{Shipping Fee} + \text{Taxes}$$
2. **Inventory Reservation Hold**:
   - The Order Service requests an atomic stock reservation from the Inventory Service.
   - Stock is reserved for exactly **15 minutes**.
   - If payment is not completed within 15 minutes, the reservation expires and stock is released back into available pool.
3. **Transition to Order**:
   - Upon successful payment authorization, the checkout state terminates and the official immutable `Order` aggregate is persisted in `order_db`.
   - The customer's cart items are cleared.

## 3. Failure & Idempotency Rules

1. **Duplicate Request Protection**:
   - If two identical requests arrive with the same `Idempotency-Key`, the second request blocks on a Redis distributed lock. Once the first request completes, the second receives the exact cached result without duplicate inventory reservation.
2. **Recovery on Payment Failure**:
   - If the payment attempt fails or is declined, the reservation is immediately released (or kept for a 5-minute retry window if user chooses another payment method).
