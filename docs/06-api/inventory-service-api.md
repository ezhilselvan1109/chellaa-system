# Inventory Service API Contract

Authoritative REST API contracts for `inventory-service`.

---

## 1. POST /api/v1/inventory/reserve

Atomically reserves requested stock quantities across warehouses for 15 minutes.

### Request Body
```json
{
  "orderId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "reservationTtlSeconds": 900,
  "items": [
    { "sku": "TSHIRT-NAVY-L", "quantity": 2 }
  ]
}
```

### Success Response: `201 Created`
```json
{
  "reservationId": "c8b7a6d5-e4f3-2a1b-0c9d-8e7f6a5b4c3d",
  "orderId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "status": "RESERVED",
  "expiresAt": "2026-10-10T14:45:00Z",
  "allocations": [
    {
      "sku": "TSHIRT-NAVY-L",
      "warehouseId": "w1-chennai-central",
      "quantity": 2
    }
  ]
}
```

### Error Responses
- `409 Conflict`: Insufficient stock available for one or more SKUs (`INSUFFICIENT_STOCK`).
