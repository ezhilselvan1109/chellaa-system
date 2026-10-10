# Topic Design and Registry

Topic naming conventions, retention policies, and registered topic catalog.

---

## 1. Naming Format

Standardized format: `<domain>.<entity>.<action>.v<version>` (e.g. `commerce.orders.order-created.v1`).

---

## 2. Topic Registry

| Topic Name | Partitions | Retention | Key | Producers | Consumers |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `commerce.identity.customer-created.v1` | 6 | 14 Days | `customerId` | `identity-service` | `customer-service`, `notification-service` |
| `commerce.catalog.product-updated.v1` | 6 | 14 Days | `productId` | `catalog-service` | `search-service`, `pricing-service` |
| `commerce.inventory.inventory-reserved.v1` | 6 | 14 Days | `orderId` | `inventory-service`| `order-service` |
| `commerce.orders.order-created.v1` | 6 | 14 Days | `orderId` | `order-service` | `payment-service`, `reporting-service` |
| `commerce.orders.order-confirmed.v1` | 6 | 14 Days | `orderId` | `order-service` | `shipping-service`, `notification-service`, `reporting-service` |
| `commerce.payments.payment-completed.v1` | 6 | 14 Days | `orderId` | `payment-service` | `order-service`, `notification-service` |
| `commerce.shipping.order-shipped.v1` | 6 | 14 Days | `orderId` | `shipping-service` | `order-service`, `notification-service` |
| `commerce.returns.return-approved.v1` | 6 | 14 Days | `returnId` | `return-service` | `shipping-service`, `notification-service` |
| `commerce.refunds.refund-completed.v1` | 6 | 14 Days | `orderId` | `refund-service` | `order-service`, `notification-service`, `reporting-service` |
