# Order Service API Contract

Authoritative REST API contracts for `order-service`.

---

## 1. POST /api/v1/orders/checkout

Initiates the checkout saga, validates prices, reserves stock, and creates an order in `PENDING_PAYMENT` status.

### Request Headers
- `Authorization: Bearer <JWT_TOKEN>` (Required)
- `Idempotency-Key: <UUID>` (Required)
- `Content-Type: application/json`

### Request Body
```json
{
  "shippingAddressId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
  "billingAddressId": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
  "shippingMethod": "STANDARD",
  "couponCode": "FESTIVE10",
  "items": [
    {
      "sku": "TSHIRT-NAVY-L",
      "quantity": 2
    }
  ]
}
```

### Success Response: `201 Created`
- `Location: /api/v1/orders/f47ac10b-58cc-4372-a567-0e02b2c3d479`
```json
{
  "orderId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "orderNumber": "ORD-20261010-004821",
  "status": "PENDING_PAYMENT",
  "subtotal": { "amount": "2000.00", "currency": "INR" },
  "discount": { "amount": "200.00", "currency": "INR" },
  "shippingFee": { "amount": "0.00", "currency": "INR" },
  "tax": { "amount": "216.00", "currency": "INR" },
  "total": { "amount": "2016.00", "currency": "INR" },
  "paymentExpiresAt": "2026-10-10T14:45:00Z",
  "createdAt": "2026-10-10T14:30:00Z"
}
```

### Error Responses
- `409 Conflict`: Insufficient inventory stock or concurrent checkout in progress.
- `422 Unprocessable Entity`: Invalid or expired coupon code.
- `401 Unauthorized`: Missing or invalid customer JWT.
