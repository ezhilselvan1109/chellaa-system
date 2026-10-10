# Return Business Rules

## 1. Eligibility & Return Window Invariants

1. **Return Window**:
   - Customers may request a return within exactly **7 calendar days** starting from the carrier-confirmed `DELIVERED` timestamp.
   - Once the 7-day window expires, the return action is permanently disabled on the storefront.
2. **Category Exceptions**:
   - Non-returnable categories: Innerwear, personal hygiene items, gift cards, and final clearance items explicitly tagged as `non_returnable = true`.
3. **Required Information**:
   - Return requests must include a valid return reason code (e.g. `SIZE_TOO_SMALL`, `DEFECTIVE_PRODUCT`, `WRONG_ITEM_RECEIVED`), customer notes, and at least 1 uploaded photograph of the item condition.

## 2. Return Workflow & Quality Inspection

1. **Approval Gate**:
   - Return requests enter a `PENDING_REVIEW` queue. Minor standard reasons (e.g. Size Exchange) can be auto-approved; damage claims are reviewed by Customer Support within 24 hours.
2. **Reverse Logistics Pickup**:
   - Upon approval, a reverse pickup shipment is generated with the courier.
   - Return item states: `RETURN_REQUESTED` -> `RETURN_APPROVED` -> `PICKUP_SCHEDULED` -> `IN_TRANSIT_TO_WAREHOUSE` -> `RECEIVED_AT_WAREHOUSE`.
3. **Warehouse Quality Inspection (QC)**:
   - Warehouse operators inspect received items and log QC results:
     - `PASS_RESTOCKABLE`: Item is clean, tagged, unused; automatically restocked into available inventory.
     - `PASS_DEFECTIVE`: Merchant defect; written off to damaged stock.
     - `REJECT_CUSTOMER_DAMAGED`: Item used/abused; return rejected, item returned to customer.
4. **Trigger to Refund**:
   - Successful QC inspection (`PASS_RESTOCKABLE` or `PASS_DEFECTIVE`) emits a `ReturnInspectionPassed` event, directly triggering the Refund Service.
