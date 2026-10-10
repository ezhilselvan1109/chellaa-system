# Notification Service Architecture

Architecture of the asynchronous multi-channel notification engine (Email, SMS, WhatsApp Business, Push, In-App).

---

## 1. Notification Processing Pipeline

```text
               +--------------------------------------------+
               | Kafka Domain Event (e.g. OrderShipped.v1)  |
               +--------------------------------------------+
                                     |
                                     v
               +--------------------------------------------+
               |            Notification Engine             |
               +--------------------------------------------+
                                     |
                                     v
               +--------------------------------------------+
               | 1. Customer Preferences & Consent Check    |
               +--------------------------------------------+
                                     |
                                     v
               +--------------------------------------------+
               | 2. Template Resolution (Channel & Locale)  |
               +--------------------------------------------+
                                     |
                                     v
               +--------------------------------------------+
               | 3. Variable Validation & Safe Rendering    |
               +--------------------------------------------+
                                     |
                  +------------------+------------------+
                  |                  |                  |
                  v                  v                  v
          [Email Adapter]     [SMS Adapter]     [WhatsApp Adapter]
            (AWS SES)          (Twilio)           (Meta Cloud API)
                  |                  |                  |
                  +------------------+------------------+
                                     |
                                     v
               +--------------------------------------------+
               | 4. Delivery Tracking & Webhook Processing  |
               +--------------------------------------------+
```

---

## 2. Decoupling Principle

The Order, Payment, and Shipping services must NEVER assemble channel-specific message bodies, email subjects, or talk to external notification gateways directly.
- Microservices emit clean domain facts (`OrderShipped { orderId, trackingUrl, customerPhone }`).
- The Notification Service autonomously resolves message bodies, formats, localization (`en-IN` vs `ta-IN`), and delivery channels.
