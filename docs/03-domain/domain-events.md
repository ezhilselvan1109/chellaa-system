# Domain Events Catalog

This document specifies the authoritative domain events emitted across bounded contexts upon internal aggregate state mutations.

---

## 1. Domain Event Inventory

| Event Name | Producing Service | Kafka Topic | Primary Key | Description / Trigger |
| :--- | :--- | :--- | :--- | :--- |
| `CustomerCreated` | `identity-service` | `commerce.identity.customer-created.v1` | `customerId` | Emitted when a new customer completes verification. |
| `ProductCreated` | `catalog-service` | `commerce.catalog.product-created.v1` | `productId` | Emitted when a product is created or published. |
| `ProductUpdated` | `catalog-service` | `commerce.catalog.product-updated.v1` | `productId` | Emitted on title, media, or category edits. |
| `PriceChanged` | `pricing-service` | `commerce.pricing.price-changed.v1` | `sku` | Emitted when a SKU selling price is adjusted. |
| `CampaignStarted` | `promotion-service` | `commerce.promotion.campaign-started.v1` | `campaignId` | Emitted when a flash sale or promotion activates. |
| `CampaignEnded` | `promotion-service` | `commerce.promotion.campaign-ended.v1` | `campaignId` | Emitted when a campaign expires or budget depletes. |
| `InventoryReserved` | `inventory-service` | `commerce.inventory.inventory-reserved.v1` | `orderId` | Emitted when stock hold is successfully leased. |
| `InventoryReservationFailed` | `inventory-service` | `commerce.inventory.reservation-failed.v1` | `orderId` | Emitted when insufficient stock aborts checkout. |
| `InventoryReleased` | `inventory-service` | `commerce.inventory.inventory-released.v1` | `orderId` | Emitted when a hold expires or order cancels. |
| `OrderCreated` | `order-service` | `commerce.orders.order-created.v1` | `orderId` | Emitted when checkout begins with pending payment. |
| `PaymentInitiated` | `payment-service` | `commerce.payments.payment-initiated.v1` | `orderId` | Emitted when user initiates payment gateway session. |
| `PaymentCompleted` | `payment-service` | `commerce.payments.payment-completed.v1` | `orderId` | Emitted upon verified payment gateway webhook. |
| `PaymentFailed` | `payment-service` | `commerce.payments.payment-failed.v1` | `orderId` | Emitted on payment gateway decline or timeout. |
| `OrderConfirmed` | `order-service` | `commerce.orders.order-confirmed.v1` | `orderId` | Emitted when order transitions to `PAID`. |
| `OrderCancelled` | `order-service` | `commerce.orders.order-cancelled.v1` | `orderId` | Emitted when order is cancelled before dispatch. |
| `OrderShipped` | `shipping-service` | `commerce.shipping.order-shipped.v1` | `orderId` | Emitted when carrier picks up parcel and AWB is active. |
| `OrderDelivered` | `shipping-service` | `commerce.shipping.order-delivered.v1` | `orderId` | Emitted upon confirmed carrier delivery e-POD. |
| `ReturnRequested` | `return-service` | `commerce.returns.return-requested.v1` | `returnId` | Emitted when customer files a return claim. |
| `ReturnApproved` | `return-service` | `commerce.returns.return-approved.v1` | `returnId` | Emitted when return claim is verified for pickup. |
| `ReturnInspectionPassed`| `return-service` | `commerce.returns.inspection-passed.v1` | `returnId` | Emitted when warehouse QC approves return item. |
| `RefundCompleted` | `refund-service` | `commerce.refunds.refund-completed.v1` | `orderId` | Emitted when electronic refund reaches gateway. |
| `ReviewPublished` | `review-service` | `commerce.reviews.review-published.v1` | `productId` | Emitted when a verified review passes moderation. |
