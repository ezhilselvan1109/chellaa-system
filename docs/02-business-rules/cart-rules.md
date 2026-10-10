# Cart Business Rules

## 1. Guest & Customer Cart Lifecycles

1. **Guest Cart**:
   - Identified by an opaque, cryptographically random `guestCartId` stored in a secure client cookie or header.
   - Persisted in Redis with an absolute Time-To-Live (TTL) of 7 days, refreshed on every modification.
2. **Customer Cart**:
   - Persisted in PostgreSQL (`cart_db`) linked to `customerId`.
   - Inactive customer carts remain stored for 90 days.
3. **Cart Merging Policy**:
   - When an unauthenticated visitor logs in with an active guest cart:
     - For items present only in guest cart: inserted into customer cart.
     - For items present in both: quantities are summed up to the maximum purchasable limit (max 10 units per SKU or available warehouse stock).
     - The guest cart is permanently invalidated in Redis.

## 2. Quantity Limits & Item Validation

1. **Max Item Limits**: Maximum of 10 units per SKU per order (to deter unauthorized bulk reselling) and maximum of 30 distinct SKUs per cart.
2. **Dynamic Stock Revalidation**:
   - Adding an item to a cart does NOT reserve inventory. Inventory is reserved only during formal Checkout.
   - Every time the cart is viewed, active stock levels are verified. If a SKU's available inventory drops below the cart quantity, the cart automatically clamps the quantity to available stock and notifies the customer.
3. **Price Freshness**:
   - Cart item prices are recalculated on load to reflect current promotional pricing. If a price changes while items sit in the cart, a banner displays: `"Price for [Item] changed from ₹1,200 to ₹1,400"`.

## 3. Save for Later & Wishlist Interaction

1. Customers may move items between the active cart and a "Saved for Later" list.
2. Saved for Later items do not contribute to cart totals, shipping estimates, or coupon threshold calculations.
