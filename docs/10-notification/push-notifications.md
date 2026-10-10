# Push Notification Channel Specification

Firebase Cloud Messaging (FCM), device registration, and web push standards.

---

## 1. Firebase Cloud Messaging (FCM) Integration

- **Device Token Registry**: Customer mobile apps and browsers register device tokens via `POST /api/v1/notifications/devices`.
- **Payload Structure**:
  - Title, body, thumbnail image URL, and deep-link click action (e.g. `/orders/ORD-12345`).
- **Token Invalidation**: FCM responses returning `UNREGISTERED` or `INVALID_ARGUMENT` automatically deactivate the corresponding device token in `customer_db`.
