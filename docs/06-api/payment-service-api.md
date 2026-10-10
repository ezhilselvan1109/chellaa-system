# Payment Service API Contract

Authoritative REST API contracts for `payment-service`.

---

## 1. POST /api/v1/payments/initiate

Initializes a payment gateway session and returns client tokens/order IDs for gateway checkout.

### Request Body
```json
{
  "orderId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "amount": "2016.00",
  "currency": "INR",
  "gatewayProvider": "RAZORPAY"
}
```

### Success Response: `201 Created`
```json
{
  "paymentId": "p9a8b7c6-d5e4-3f2a-1b0c-9d8e7f6a5b4c",
  "orderId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "gatewayOrderId": "order_rzp_9876543210",
  "status": "INITIATED",
  "gatewayKeyId": "rzp_live_sampleKey123",
  "amount": "2016.00",
  "currency": "INR"
}
```

---

## 2. POST /api/v1/payments/webhook

Cryptographically verified endpoint for gateway event callbacks.

### Headers
- `X-Razorpay-Signature: <HMAC_SHA256_HEX>`
- `Content-Type: application/json`

### Success Response: `200 OK`
```json
{ "received": true }
```
