# MongoDB Database Design (Targeted Document Store)

Use cases, collection designs, and document modeling for MongoDB 7.0.

---

## 1. Approved Document Workloads

Per ADR-003, MongoDB is approved **exclusively** for document workloads where strict polymorphic schemas or nested document variations make relational tables unwieldy:
1. **Notification Template & Rendering History (`notification_db`)**: Message templates across 5 channels (Email HTML, WhatsApp interactive buttons, Push deep links) and raw rendering logs.
2. **Administrative Audit Logs (`audit_db`)**: Full request/response payload diffs across administrative interventions.

---

## 2. Notification Document Schema

### Collection: `notification_templates`
```json
{
  "_id": "ORDER_SHIPPED_V1_EN",
  "templateKey": "ORDER_SHIPPED",
  "channel": "WHATSAPP",
  "locale": "en-IN",
  "version": 1,
  "status": "PUBLISHED",
  "whatsappProviderTemplateId": "meta_tpl_order_shipped_v1",
  "variables": [
    {"name": "customerName", "type": "STRING", "required": true},
    {"name": "orderNumber", "type": "STRING", "required": true},
    {"name": "trackingUrl", "type": "URL", "required": true}
  ],
  "content": {
    "header": "Order Shipped! 🚚",
    "body": "Hi {{customerName}}, your order {{orderNumber}} is on the way! Track here: {{trackingUrl}}",
    "footer": "Thank you for shopping with us."
  },
  "createdAt": "2026-10-10T10:00:00Z",
  "updatedAt": "2026-10-10T10:00:00Z"
}
```
Indexes: `{"templateKey": 1, "channel": 1, "locale": 1, "status": 1}`.
