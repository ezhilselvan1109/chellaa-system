# Shipping Business Rules

## 1. Logistics Zones & Serviceability

1. **Pincode Serviceability**:
   - Shipping destinations are checked against the merchant's 3PL courier serviceability database.
   - If a destination pincode is unserviceable, checkout is blocked with an informative message.
2. **Delivery Methods & Rates**:
   - **Standard Shipping**: Typically 3-5 business days. Free for orders above ₹999; flat fee of ₹79 for orders below ₹999.
   - **Express Shipping**: Next-day or 2-day delivery; flat fee of ₹199 where available.

## 2. Dispatch Manifests & Tracking

1. **AWB Assignment**:
   - When an order is packed at the fulfillment warehouse, the Shipping Service generates a shipment record and assigns an Air Waybill (AWB) tracking number from the contracted carrier (e.g. BlueDart, Delhivery).
2. **Carrier Webhooks**:
   - The Shipping Service ingests courier tracking webhooks to advance shipment milestones: `PICKED_UP`, `IN_TRANSIT`, `OUT_FOR_DELIVERY`, `DELIVERED`, `UNDELIVERED_ATTEMPT_1`, `RETURN_TO_ORIGIN`.
3. **Delivery Confirmation Invariant**:
   - An order transitions to `DELIVERED` only when the carrier emits a verified delivery timestamp or electronic proof of delivery (e-POD).
   - This delivery timestamp marks the official start of the 7-day customer return policy window.
