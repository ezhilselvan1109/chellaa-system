# ADR-009: Multi-Channel Notification Provider Abstraction Layer

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Integration Lead

---

## 1. Context

Delivering customer communications across Email, SMS, WhatsApp Business, Push, and In-App channels involves multiple third-party vendors (AWS SES, Twilio, Meta Cloud API, Firebase FCM). Direct tight coupling between domain services and specific vendor SDKs creates vendor lock-in and brittle failure handling.

---

## 2. Options Considered

1. **Option A: Direct Provider Calls from Domain Services**
   - *Pros*: Faster initial prototyping.
   - *Cons*: Disastrous coupling; Order Service contains Twilio SMS code; changing email vendor requires editing Order and Payment services; impossible to centralize consent management.
2. **Option B: Autonomous Notification Service with SPI Adapters (Chosen)**
   - *Pros*: Domain services emit generic domain facts (`OrderShipped`); Notification Service handles templating, localization (`ta-IN`), opt-in checking, and provider dispatch via pluggable `NotificationChannelAdapter` interfaces.
   - *Cons*: Adds an asynchronous hop before notification dispatch.

---

## 3. Decision

Adopt **Option B**: Autonomous `notification-service` encapsulating channel adapters and Meta WhatsApp approved utility templates.

---

## 4. Consequences

- **Positive**: Zero vendor lock-in, centralized consent and quiet hours suppression, provider failover capabilities.
- **Negative**: Requires maintaining template versioning and webhook ingestion pipelines.

---

## 5. Related Documents
- [Notification Architecture](file:///d:/learning/Microservice/backend/docs/10-notification/notification-architecture.md)
- [Provider Abstraction](file:///d:/learning/Microservice/backend/docs/10-notification/provider-abstraction.md)
