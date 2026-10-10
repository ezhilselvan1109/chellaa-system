# Notification Workflow

## 1. Purpose & Objective
Design, template, validate, dispatch, and track multi-channel customer communications (Email, SMS, WhatsApp Business, Push, In-App) triggered by domain events while respecting customer preferences, compliance, and provider rate limits.

## 2. Prerequisites & Inputs
- Triggering Kafka domain event (e.g. `OrderShipped`, `PaymentFailed`).
- Customer communication preferences and consent status.
- Channel requirements and provider constraints.

## 3. Workflow Sequence

```text
Domain Event Trigger Received
               |
               v
Notification Classification (Transactional vs Marketing)
               |
               v
Customer Preference & Consent Verification
               |
               v
Template Resolution (Channel, Locale, Version)
               |
               v
Variable Declaration & Safe Interpolation Validation
               |
               v
Channel Provider Selection & Adapter Invocation
               |
               v
Dispatch Execution & Provider Status Recording
               |
               v
Delivery Tracking & Webhook Processing
               |
               v
Retry with Jitter / Dead Letter Handling
               |
               v
Integration Verification & Audit Log Emission
```

## 4. Responsible Skills
- `notification-engineering`
- `kafka-event-design`
- `springboot-development`
- `security-engineering`
- `testing-engineering`

## 5. Completion Gate
- Templates declared with strict schema validation; missing variables fail safely before dispatch.
- Marketing notifications strictly bypass non-consenting users; transactional notifications bypass marketing opt-outs only if legally permitted.
- WhatsApp Business templates strictly adhere to Meta/provider approved template strings.
- Outbound attempts are logged with distinct statuses: `ACCEPTED`, `SENT`, `DELIVERED`, `READ`, `FAILED`.
- Channel adapter tests verify timeout handling and webhook signature verification.
