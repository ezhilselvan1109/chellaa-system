# Aggregates and Invariants

This document formalizes the Aggregate Roots across the platform and the domain invariants enforced by each aggregate.

---

## 1. Aggregate Root Specifications

### 1.1 Product Aggregate (`catalog-service`)
- **Root**: `Product`
- **Internal Entities**: `ProductVariant`, `ProductAttributeValue`, `ProductMedia`
- **Invariants**:
  - A Product must have at least one variant before it can be published.
  - All variants belonging to a product must have unique SKU codes and distinct attribute combinations (e.g. cannot have two Navy Large variants).
  - Deleting a product soft-deletes its variants.

### 1.2 Inventory Aggregate (`inventory-service`)
- **Root**: `InventoryItem` (Keyed by `sku` + `warehouseId`)
- **Internal Entities**: `StockAllocation`, `StockAdjustment`
- **Invariants**:
  - `availableQuantity = onHandQuantity - allocatedQuantity - damagedQuantity`.
  - `availableQuantity >= 0` must hold at the conclusion of every transaction.
  - Allocating stock requires an explicit lease expiration timestamp (default 15 minutes).

### 1.3 Cart Aggregate (`cart-service`)
- **Root**: `Cart`
- **Internal Entities**: `CartItem`, `AppliedCoupon`
- **Invariants**:
  - Item quantities must be positive integers ($1 \le \text{qty} \le 10$).
  - A cart can contain at most one applied coupon at any time.
  - Subtotals are recomputed whenever items or coupons change.

### 1.4 Order Aggregate (`order-service`)
- **Root**: `Order`
- **Internal Entities**: `OrderItem`, `OrderSnapshot`, `OrderTimelineEntry`
- **Invariants**:
  - Total order amount must match the sum of item line totals plus shipping fee minus coupon discounts plus taxes.
  - Order state transitions must follow legal paths; a `DELIVERED` order cannot transition to `CANCELLED`.
  - Once persisted, order line items and pricing snapshots are completely immutable.

### 1.5 Return Aggregate (`return-service`)
- **Root**: `ReturnRequest`
- **Internal Entities**: `ReturnItem`, `ReturnQualityCheck`, `ReturnTimeline`
- **Invariants**:
  - A return request must be submitted within 7 days of the order delivery timestamp.
  - An item can be marked for refund only if its QC inspection result is `PASS_RESTOCKABLE` or `PASS_DEFECTIVE`.
