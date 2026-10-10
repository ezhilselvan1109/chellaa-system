# Domain Model

## 1. Domain-Driven Design (DDD) Overview

The platform is designed using strategic and tactical Domain-Driven Design (DDD). The problem space of enterprise e-commerce is partitioned into autonomous **Bounded Contexts**, each modeling distinct business aggregates, entities, value objects, and domain invariants.

## 2. Core Domain Map

```text
+-----------------------------------------------------------------------------------+
|                              Single-Owner E-Commerce                              |
+-----------------------------------------------------------------------------------+
                                          |
        +---------------------------------+---------------------------------+
        |                                 |                                 |
        v                                 v                                 v
+-----------------------+     +-----------------------+     +-----------------------+
| Core Commerce Context |     |  Fulfillment Context  |     |  Engagement Context   |
+-----------------------+     +-----------------------+     +-----------------------+
| - Customer Account    |     | - Warehouse Inventory |     | - Ratings & Reviews   |
| - Product Catalog     |     | - Shipping Logistics  |     | - Notifications       |
| - Pricing & Taxes     |     | - Returns & QC        |     | - Read-Model Analytics|
| - Promotions & Coupons|     | - Payment Refunds     |     +-----------------------+
| - Cart Management     |     +-----------------------+
| - Order Lifecycle     |
| - Payment Processing  |
+-----------------------+
```

## 3. Aggregate Roots & Transaction Boundaries

Each Aggregate Root defines a strict local ACID transactional consistency boundary:
1. **Customer Aggregate** (`customerId`): Manages authentication credentials, profile, and address book.
2. **Product Aggregate** (`productId`): Manages variant options, category links, specifications, and media.
3. **PriceBook Aggregate** (`priceBookId`): Manages base prices, tier discounts, and currency schedules.
4. **Campaign & Coupon Aggregate** (`campaignId`, `couponId`): Manages promotion rules, budgets, and redemption counters.
5. **Cart Aggregate** (`cartId`): Manages ephemeral item selections, quantities, and live price revalidations.
6. **InventoryItem Aggregate** (`sku`, `warehouseId`): Manages on-hand physical stock, active holds, and bin allocations.
7. **Order Aggregate** (`orderId`): Manages immutable purchase snapshot, payment confirmation, and status progression.
8. **PaymentTransaction Aggregate** (`paymentId`): Manages gateway attempts, tokens, webhook signatures, and ledger status.
9. **Shipment Aggregate** (`shipmentId`): Manages packing status, carrier AWB, pickup manifests, and tracking milestones.
10. **Return Aggregate** (`returnId`): Manages return requests, evidence photos, pickup dispatch, and warehouse QC inspection.
11. **Refund Aggregate** (`refundId`): Manages double-entry refund ledger, gateway payout execution, and credit note linkage.
12. **Review Aggregate** (`reviewId`): Manages verified purchase reviews, moderation status, and aggregate rating contributions.
