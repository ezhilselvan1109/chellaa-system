# Saga Pattern Architecture

This document details the Orchestrated Saga implementation coordinating the distributed Checkout and Order Lifecycle workflows.

---

## 1. Saga Orchestration Architecture

The **Order Service** acts as the central **Saga Orchestrator**, executing sequential distributed steps and coordinating compensating actions upon failure:

```text
[Client Checkout] 
       |
       v
+----------------------------------------------------------------------------+
|                          Order Service (Saga Orchestrator)                 |
+----------------------------------------------------------------------------+
   | (1) Command: Reserve Inventory
   v
[Inventory Service] ---> Success: InventoryReserved
   |
   +---> (2) Command: Initiate Payment
   v
[Payment Service] -----> Failure: PaymentDeclined / Timeout
   |
   +---> (3) Compensation Command: Release Inventory
   v
[Inventory Service] ---> Success: InventoryReleased
   |
   v
[Order Service] -------> State: CANCELLED (Customer Notified)
```

---

## 2. Checkout Saga Step Definitions

| Step | Forward Action | Compensating Action (Rollback) |
| :--- | :--- | :--- |
| **Step 1: Validate Customer & Cart** | Verify active account status and unexpired cart items. | None (Read-only validation). |
| **Step 2: Reserve Inventory** | Atomic 15-minute stock hold placed on SKUs in `inventory_db`. | Command: `ReleaseInventoryReservation` to return held units to available stock. |
| **Step 3: Lock Coupon Redemption** | Increment coupon redemption count in `promotion_db`. | Command: `ReleaseCouponRedemption` to decrement usage counter. |
| **Step 4: Authorize Payment** | Submit payment charge to payment gateway. | Command: `CancelPaymentSession` or `InitiateGatewayRefund`. |
| **Step 5: Confirm Order** | Mark order status `PAID`, clear customer cart, emit `OrderConfirmed`. | If order confirmation fails internally, trigger full gateway refund and release stock. |

---

## 3. Saga State Persistence

The Order Service persists the state of every running saga in an `order_saga_instances` table:
```sql
CREATE TABLE order_saga_instances (
    saga_id UUID PRIMARY KEY,
    order_id UUID NOT NULL,
    current_step VARCHAR(50) NOT NULL,
    status VARCHAR(30) NOT NULL, -- RUNNING, COMPLETED, COMPENSATING, FAILED
    payload JSONB NOT NULL,
    created_at TIMESTAMPTZ NOT NULL,
    updated_at TIMESTAMPTZ NOT NULL
);
```
