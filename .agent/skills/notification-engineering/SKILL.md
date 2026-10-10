---
name: notification-engineering
description: >-
  Use this skill to design multi-channel notification systems (Email, SMS, WhatsApp Business, Push, In-App),
  manage templates, enforce consent/compliance rules, integrate provider adapters, and handle delivery tracking.
---

# Notification Engineering Skill

## 1. Purpose
Designs and operates a multi-channel notification platform delivering transactional and marketing messages across Email, SMS, WhatsApp Business, Push, and In-App channels while enforcing strict consent, rate limiting, and delivery observability.

## 2. When to Use It
- Adding new notification types triggered by domain events (`OrderShipped`, `OrderDelivered`).
- Creating or editing versioned message templates across multiple locales.
- Designing WhatsApp Business Platform API integrations and template approval workflows.
- Managing user communication preferences and marketing opt-out compliance.
- Handling delivery tracking, webhooks, retries, and dead-letter queues.

## 3. Required Inputs
- Triggering Kafka domain events (`docs/07-events/`).
- Channel technical constraints and provider API limits.
- Notification architecture specifications (`docs/10-notification/`).

## 4. Procedure
1. **Classify Message Category**:
   - **Transactional**: Order confirmations, password reset, shipment alerts (bypasses marketing opt-outs; cannot be disabled by customer).
   - **Marketing**: Promotional campaigns, price drop alerts, flash sale banners (requires explicit opt-in; honors unsubscribe instantly).
2. **Template Design & Versioning**:
   - Store templates with `templateId`, `channel`, `locale` (e.g. `en-IN`, `ta-IN`), and `version`.
   - Validate placeholders (e.g. `{{orderNumber}}`, `{{trackingUrl}}`) with strict type declarations.
3. **WhatsApp Business Integration**:
   - Pre-register template with Meta/provider with fixed parameter slots.
   - Restrict outbound customer-care messages to active 24-hour service windows or pre-approved utility templates.
4. **Provider Adapter Abstraction**:
   - Define common `NotificationChannelAdapter` interface: `send(NotificationRequest): NotificationResult`.
   - Implement concrete adapters (SendGrid/AWS SES for Email, Twilio/Gupshup for SMS/WhatsApp, Firebase Cloud Messaging for Push).
5. **Delivery State Machine**:
   - Transition states: `PENDING` -> `ACCEPTED` -> `SENT` -> `DELIVERED` -> `READ` or `FAILED`.
   - Ingest provider delivery status callbacks via webhook; deduplicate by provider message ID.
6. **Publish Notification Specs**: Update `docs/10-notification/`.

## 5. Rules & Constraints
- Order, Payment, and Shipping services must NEVER construct channel-specific message bodies or call provider APIs directly.
- Missing variables in template rendering must fail the dispatch before calling external providers.
- Never spam users: enforce channel-level rate limits per customer (e.g. max 1 marketing SMS per day).

## 6. Expected Outputs
- Template schemas and variable catalogs in `docs/10-notification/`.
- Provider adapter integration tests with WireMock.

## 7. Validation Checklist
- [ ] Does the dispatch pipeline check user opt-out status for marketing notifications?
- [ ] Are provider API keys abstracted through configuration and secret vaults?
- [ ] Does delivery failure trigger exponential backoff without blocking other notifications?

## 8. Common Failure Modes
- Hardcoding SMS strings inside the Order Service.
- Attempting to send unapproved, free-form text via WhatsApp Business outside the 24-hour customer service window.

## 9. Authoritative Links
- [Notification Architecture](file:///d:/learning/Microservice/backend/docs/10-notification/notification-architecture.md)
- [WhatsApp Channel](file:///d:/learning/Microservice/backend/docs/10-notification/whatsapp-channel.md)
- [Provider Abstraction](file:///d:/learning/Microservice/backend/docs/10-notification/provider-abstraction.md)
