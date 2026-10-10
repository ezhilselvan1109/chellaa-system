# Pricing Business Rules

## 1. Price Definition & Invariants

1. **MRP (Maximum Retail Price)**:
   - Legally mandated ceiling price printed on consumer goods packaging.
   - Selling price must NEVER exceed MRP under any circumstances: `sellingPrice <= mrp`.
2. **Base Selling Price**:
   - The regular retail price offered before cart coupons or flash discounts.
   - Must be a positive decimal quantity with exactly 2 decimal precision: `sellingPrice > 0`.
3. **Monetary Modeling**:
   - All price arithmetic must use `BigDecimal` with `RoundingMode.HALF_UP` in Java, or integer minor units (paise/cents) in databases to eliminate floating-point precision loss.
   - Currency is stored alongside every monetary value (`INR`, `USD`). Multi-currency conversions occur via explicit exchange rate lookup.

## 2. Dynamic & Scheduled Pricing

1. **Scheduled Price Overrides**: Merchandisers may schedule future price changes with `effective_from` and `effective_to` timestamps.
2. **Precedence**: When calculating SKU price:
   - If an active scheduled price override exists for the current UTC timestamp, it takes precedence over base price.
   - If an active flash campaign discount applies, it computes off the active selling price.
3. **Price Snapshotting**:
   - Once an item is added to an order, the price is locked in an immutable order item snapshot. Future catalog price changes do not retroactively alter confirmed orders.

## 3. Tax Inclusion / Exclusion Policy

1. **B2C Consumer Pricing**: Product catalog prices displayed to customers on the storefront are **inclusive of applicable GST** to provide transparent shopping.
2. **Invoice Tax De-aggregation**: During checkout and invoice generation, the base taxable value, CGST, SGST, or IGST amounts are calculated deterministically using the product's assigned HSN code and GST rate slab (e.g. 5%, 12%, 18%, 28%).
