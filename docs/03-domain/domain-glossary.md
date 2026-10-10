# Domain Glossary

Ubiquitous domain terminology mapped to microservice models and class names.

---

## Mapping Lexicon

| Domain Term | Owning Context | Java Class / Model | Database Entity |
| :--- | :--- | :--- | :--- |
| **Customer** | Identity & Customer | `com.ecommerce.customer.domain.Customer` | `customers` |
| **Address** | Customer Context | `com.ecommerce.customer.domain.Address` | `customer_addresses` |
| **Product** | Catalog Context | `com.ecommerce.catalog.domain.Product` | `products` |
| **Variant** | Catalog Context | `com.ecommerce.catalog.domain.ProductVariant` | `product_variants` |
| **SKU** | Cross-Domain Ref | `com.ecommerce.common.domain.Sku` (VO) | `sku` (VARCHAR) |
| **Price** | Pricing Context | `com.ecommerce.pricing.domain.PriceBookEntry`| `sku_prices` |
| **Campaign** | Promotion Context | `com.ecommerce.promotion.domain.Campaign` | `campaigns` |
| **Coupon** | Promotion Context | `com.ecommerce.promotion.domain.Coupon` | `coupons` |
| **Cart** | Cart Context | `com.ecommerce.cart.domain.Cart` | `carts` |
| **Cart Item** | Cart Context | `com.ecommerce.cart.domain.CartItem` | `cart_items` |
| **Stock Allocation**| Inventory Context | `com.ecommerce.inventory.domain.Allocation` | `stock_allocations` |
| **Order** | Order Context | `com.ecommerce.order.domain.Order` | `orders` |
| **Order Item** | Order Context | `com.ecommerce.order.domain.OrderItem` | `order_items` |
| **Payment Charge** | Payment Context | `com.ecommerce.payment.domain.PaymentCharge` | `payment_transactions` |
| **Shipment** | Shipping Context | `com.ecommerce.shipping.domain.Shipment` | `shipments` |
| **Return Claim** | Return Context | `com.ecommerce.return.domain.ReturnClaim` | `return_requests` |
| **Refund Entry** | Refund Context | `com.ecommerce.refund.domain.RefundLedger` | `refund_ledger_entries`|
| **Review** | Review Context | `com.ecommerce.review.domain.ProductReview` | `product_reviews` |
| **Notification** | Notification Context | `com.ecommerce.notification.domain.Message` | `notification_messages`|
