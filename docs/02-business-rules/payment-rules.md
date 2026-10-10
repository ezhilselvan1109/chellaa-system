# Payment Business Rules

## 1. Gateway Integration & Security

1. **Tokenized Gateways**:
   - The platform integrates PCI-DSS certified payment gateways (Razorpay, Stripe) using client-side SDKs / hosted checkout.
   - Raw payment card numbers, CVVs, or bank net-banking passwords MUST NEVER pass through or be stored in platform servers.
2. **Payment Session Initiation**:
   - Payment sessions have a strict expiration window matching the 15-minute checkout reservation hold.
   - Gateway order IDs (e.g. `order_rzp_123456`) are linked to internal `orderId`.

## 2. Webhook Verification & Asynchronous Confirmation

1. **Mandatory Cryptographic Verification**:
   - Payment confirmations arriving via webhook must verify the HMAC-SHA256 signature using the raw HTTP request body and gateway webhook secret. Requests failing signature verification are rejected with HTTP 400.
2. **Anti-Duplication Invariant**:
   - Webhooks are frequently retried by gateways. The Payment Service must record the gateway `payment_id` in a unique constraint table (`payment_transactions`) to ensure idempotent processing.
3. **No Reliance on Browser Redirect**:
   - A browser redirect back to the storefront is considered purely informational. An order transitions to `PAID` ONLY upon cryptographic webhook validation or direct backend polling of gateway status.

## 3. Payment Failure & Reconciliation

1. **Failure State Handling**:
   - If a payment is declined or cancelled by the user, the payment transaction is marked `FAILED` with provider error code (e.g. `INSUFFICIENT_FUNDS`, `AUTHENTICATION_FAILED`).
2. **Automated Reconciliation Cron**:
   - A background reconciliation job runs every 10 minutes to poll the gateway API for all transactions lingering in `INITIATED` status past 20 minutes, resolving orphaned payments.
