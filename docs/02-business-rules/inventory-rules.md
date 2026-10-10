# Inventory Business Rules

## 1. Stock Quantities & Invariants

1. **Inventory Balance Formula**:
   $$\text{Available Stock} = \text{Physical On-Hand Stock} - \text{Active Reservations} - \text{Damaged Stock}$$
2. **Non-Negative Invariant**:
   - `Available Stock` must NEVER fall below 0.
   - Any transaction attempting to decrement stock below 0 must abort with an `INSUFFICIENT_STOCK` error.
3. **Safety Stock Buffer**:
   - An administrator may configure a safety stock threshold (e.g. 3 units).
   - Once available stock hits this threshold, the public catalog marks the item as "Low Stock - Only 3 Left" and triggers an internal restock reorder notification.

## 2. Multi-Warehouse Stock Ownership

1. **Owner-Owned Warehouses**:
   - All physical warehouses are owned and operated by the merchant enterprise.
   - Each warehouse tracks inventory per SKU by location bin.
2. **Fulfillment Routing Preference**:
   - When an order is placed, stock reservation selects the warehouse closest to the shipping pincode possessing sufficient stock.
   - If no single warehouse has the full quantity for a multi-item order, split fulfillment across owned warehouses is initiated (separate package dispatches under the same order).

## 3. Concurrency & Allocation Locking

1. **Pessimistic Concurrency on Stock Deductions**:
   - Reservations on physical stock rows execute via PostgreSQL `SELECT ... FOR UPDATE` or atomic decrement queries (`UPDATE inventory SET available = available - :qty WHERE sku = :sku AND available >= :qty`).
   - This prevents overselling even when thousands of concurrent threads attempt to purchase the same flash-sale SKU simultaneously.
2. **Audit Ledger Movements**:
   - Every stock change must create an immutable entry in the `inventory_movement_log` recording: `sku`, `warehouse_id`, `change_qty`, `movement_type` (PURCHASE_RESERVE, PURCHASE_DEDUCT, RETURN_RESTOCK, DAMAGE_WRITEOFF, MANUAL_ADJUSTMENT), and `reference_id`.
