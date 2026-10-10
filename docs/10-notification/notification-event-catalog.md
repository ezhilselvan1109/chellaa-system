# Notification Event Catalog

Mapping of triggering domain events to default channels, notification templates, and classification.

---

## 1. Event to Notification Mapping

| Triggering Domain Event | Template Key | Classification | Default Channels | Description |
| :--- | :--- | :--- | :--- | :--- |
| `CustomerCreated` | `WELCOME_CUSTOMER` | Marketing | Email | Account registration greeting & guide. |
| `OrderCreated` | `ORDER_CONFIRMATION` | Transactional | Email, SMS, WhatsApp | Itemized order confirmation & order number. |
| `PaymentFailed` | `PAYMENT_FAILED` | Transactional | Email, SMS | Notification of declined charge & retry link. |
| `OrderShipped` | `ORDER_SHIPPED` | Transactional | Email, WhatsApp | Carrier dispatch alert with live AWB link. |
| `OrderDelivered` | `ORDER_DELIVERED` | Transactional | Email, WhatsApp, Push| Delivery confirmation & 7-day return notice. |
| `ReturnApproved` | `RETURN_APPROVED` | Transactional | Email, WhatsApp | Return pickup schedule & courier details. |
| `RefundCompleted` | `REFUND_CONFIRMATION`| Transactional | Email, SMS, WhatsApp | Gateway refund confirmation & reference ID. |
| `PriceDrop` | `PRICE_DROP_ALERT` | Marketing | Push, In-App | Price reduction on customer's wishlist item. |
| `BackInStock` | `BACK_IN_STOCK_ALERT`| Marketing | Email, Push | Alert that out-of-stock item has restocked. |
