# Notification Template Management

Template lifecycles, administrative editing, locales, and variable definitions.

---

## 1. Template Structure & Multi-Locale Example

Templates are managed in `notification_db` with explicit locale declarations (`en-IN`, `ta-IN`):

### Example: `ORDER_SHIPPED` Template (Email - `en-IN`)
```text
Template: ORDER_SHIPPED
Channel: EMAIL
Locale: en-IN

Subject:
Your order {{orderNumber}} has shipped! 🚚

Body:
Hello {{customerName}},

Great news! Your order {{orderNumber}} has left our warehouse and is on its way to you.

Carrier: {{carrierName}}
Tracking Number: {{trackingNumber}}
Track live: {{trackingUrl}}

Estimated Delivery: {{estimatedDeliveryDate}}

Thank you for shopping with us!
```

### Example: `ORDER_SHIPPED` Template (Email - `ta-IN`)
```text
Template: ORDER_SHIPPED
Channel: EMAIL
Locale: ta-IN

Subject:
உங்கள் ஆர்டர் {{orderNumber}} அனுப்பப்பட்டுவிட்டது! 🚚

Body:
வணக்கம் {{customerName}},

மகிழ்ச்சியான செய்தி! உங்கள் ஆர்டர் {{orderNumber}} எங்கள் கிடங்கிலிருந்து புறப்பட்டு உங்கள் முகவரிக்கு வந்துகொண்டிருக்கிறது.

கூரியர்: {{carrierName}}
டிராக்கிங் எண்: {{trackingNumber}}
நேரலை கண்காணிப்பு: {{trackingUrl}}

எங்களுடன் இணைந்தமைக்கு நன்றி!
```
