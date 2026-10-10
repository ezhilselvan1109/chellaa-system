# WhatsApp Business Channel Specification

Meta WhatsApp Business Platform API architecture, pre-approved templates, 24-hour service windows, and webhooks.

---

## 1. Meta WhatsApp Business API Rules

1. **24-Hour Customer Service Window**:
   - When a customer sends an inbound message to the merchant's business WhatsApp number, a 24-hour customer service window opens. Within this window, arbitrary customer service responses may be sent.
2. **Outbound Proactive Messages Outside 24-Hour Window**:
   - Outbound transactional notifications (e.g. `OrderShipped`, `OrderDelivered`) initiated by the merchant MUST strictly use **Meta-Approved Utility Templates**.
   - Free-form text messages outside the 24-hour window are blocked by Meta's API.
3. **Template Parameter Format**:
   - Meta templates declare numbered positional parameters: `{{1}}`, `{{2}}`, `{{3}}`.
   - The WhatsApp Channel Adapter maps named domain variables to positional slots:
     - `{{1}}` -> `customerName`
     - `{{2}}` -> `orderNumber`
     - `{{3}}` -> `trackingUrl`

---

## 2. Webhook Delivery & Status Callbacks

The Notification Service exposes an authenticated webhook endpoint for Meta/provider status callbacks:
- Event: `messages.status` (`sent` -> `delivered` -> `read` -> `failed`).
- Webhook signature verified using `X-Hub-Signature-256` SHA-256 HMAC and app secret.
