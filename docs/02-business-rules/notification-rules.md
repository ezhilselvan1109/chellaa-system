# Notification Business Rules

## 1. Classification & Consent Invariants

1. **Transactional Notifications**:
   - Order confirmations, OTP codes, payment receipts, shipment dispatches, out-for-delivery alerts, refund approvals, password reset links.
   - **Rule**: Cannot be opted out by the customer. Sent immediately regardless of marketing consent settings.
2. **Marketing Notifications**:
   - Price-drop alerts, back-in-stock alerts, abandoned cart reminders, flash sale announcements.
   - **Rule**: Strictly require verified customer opt-in for the designated channel (Email, SMS, or WhatsApp). If customer consent is missing or revoked, marketing dispatch is blocked at the gateway.

## 2. Multi-Channel Routing Policies

1. **Email Channel**:
   - Sent for all major lifecycle milestones with rich HTML invoices and tracking links.
2. **SMS Channel**:
   - Reserved for high-urgency transactional updates (OTPs, delivery dispatch). Limited to 160 characters (GSM 7-bit) or DLT-registered templates in India.
3. **WhatsApp Channel**:
   - Outbound transactional notifications strictly utilize Meta-approved utility templates with declared parameter slots.
   - Outbound marketing messages adhere strictly to opt-in consent and frequency capping (maximum 1 marketing WhatsApp message per customer per 7-day period).

## 3. Rate Limiting & Quiet Hours

1. **Marketing Quiet Hours**:
   - Marketing notifications are strictly suppressed between `21:00` and `09:00` in the customer's local timezone. Scheduled messages queue until the morning window opens.
2. **Delivery Retry Limits**:
   - Failed transient notifications retry up to 3 times with exponential backoff with jitter (1m, 5m, 15m). After 3 failures, messages route to the Notification Dead Letter Queue (DLQ).
