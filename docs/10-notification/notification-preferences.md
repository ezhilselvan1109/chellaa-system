# Customer Notification Preferences

Customer opt-in/opt-out controls, channel granularity, and compliance rules.

---

## 1. Preferences Schema

Stored in `customer_db` table `customer_notification_preferences`:
```sql
CREATE TABLE customer_notification_preferences (
    customer_id UUID PRIMARY KEY,
    marketing_email_enabled BOOLEAN NOT NULL DEFAULT TRUE,
    marketing_sms_enabled BOOLEAN NOT NULL DEFAULT FALSE,
    marketing_whatsapp_enabled BOOLEAN NOT NULL DEFAULT FALSE,
    order_updates_whatsapp_enabled BOOLEAN NOT NULL DEFAULT TRUE,
    order_updates_sms_enabled BOOLEAN NOT NULL DEFAULT TRUE,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```
- Note: Core transactional email confirmations (`ORDER_CONFIRMATION`, `PASSWORD_RESET`) cannot be disabled.
