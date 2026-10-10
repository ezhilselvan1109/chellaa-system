# Order Business Rules

## 1. Order Lifecycle & State Machine

```text
                  +-------------------+
                  |  PENDING_PAYMENT  |
                  +-------------------+
                     /             \
       (Payment Fails)             (Payment Succeeds)
                   /                 \
                  v                   v
          +---------------+      +----------+
          |   CANCELLED   |      |   PAID   |
          +---------------+      +----------+
                                      |
                              (Order Accepted)
                                      |
                                      v
                             +------------------+
                             |    PROCESSING    |
                             +------------------+
                               /              \
             (Cancelled before dispatch)    (Dispatched)
                             /                  \
                            v                    v
                   +-----------------+   +----------------+
                   | CANCELLED (REF) |   |    SHIPPED     |
                   +-----------------+   +----------------+
                                                 |
                                            (Delivered)
                                                 |
                                                 v
                                        +----------------+
                                        |   DELIVERED    |
                                        +----------------+
                                                 |
                                            (Return Filed)
                                                 |
                                                 v
                                        +----------------+
                                        |    RETURNED    |
                                        +----------------+
```

## 2. Order Immutability & Snapshots

1. **Purchased Item Snapshot**:
   - Every order line item captures an immutable copy of the product title, SKU, variant attributes (e.g. "Size XL, Color Navy"), unit price, tax rate, applied discounts, and thumbnail URL at the instant of order creation.
2. **Tax & Address Immutability**:
   - The shipping address and customer tax profile are frozen at order creation. Future updates to the customer's profile do not affect existing orders.
3. **Sequential Order Numbers**:
   - Order numbers follow an enterprise format: `ORD-YYYYMMDD-XXXXXX` (e.g. `ORD-20261010-004821`), generated via a database sequence.

## 3. Order Cancellation Policy

1. **Customer Self-Service Cancellation**:
   - A customer may cancel an entire order or line item self-service only while the order is in `PAID` or `PROCESSING` state (before tracking AWB is generated).
   - Once the order transitions to `SHIPPED`, self-service cancellation is disabled; the customer must wait for delivery and initiate a standard Return request.
2. **Automated Refund on Cancellation**:
   - Cancelling a `PAID` order automatically triggers a full refund command to Refund Service and emits an `OrderCancelled` event to release inventory.
