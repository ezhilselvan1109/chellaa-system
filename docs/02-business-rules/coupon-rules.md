# Coupon Business Rules

## 1. Coupon Structure & Discount Models

1. **Discount Formats**:
   - **Percentage Discount**: e.g., 15% off cart subtotal, subject to an optional `max_discount_amount` cap (e.g., maximum savings of ₹750).
   - **Flat Fixed Discount**: e.g., ₹500 off cart subtotal.
   - **Free Shipping Coupon**: Waives calculated courier shipping fees.
2. **Code Normalization**: Coupon codes are case-insensitive and stripped of whitespace (e.g. `diwali-2026` normalizes to `DIWALI2026`).

## 2. Eligibility & Validation Rules

1. **Minimum Cart Spend**: If `min_cart_value` is set (e.g. ₹1,999), the qualifying items' subtotal must meet or exceed this value.
2. **First-Order Only Rule**: If `first_order_only = true`, the system verifies that the authenticated customer has 0 prior completed/paid orders.
3. **Category / SKU Exclusions**: Coupons can whitelist or blacklist specific product categories, brands, or clearance items.
4. **Single Coupon Per Checkout**: A customer may apply at most **one** coupon code per order. Applying a new coupon automatically replaces any previously applied coupon.

## 3. Usage Caps & Concurrency Protection

1. **Global Usage Limit**: If a coupon has `max_redemptions = 1000`, the 1001st attempt is rejected.
2. **Per-Customer Usage Limit**: Default is `max_per_customer = 1` unless configured otherwise.
3. **Atomic Decrement Invariant**:
   - Coupon usage counts are decremented atomically using Redis counters or PostgreSQL row locking during the checkout commit phase.
   - If payment fails and the order is cancelled, the customer's redemption count is restored.
