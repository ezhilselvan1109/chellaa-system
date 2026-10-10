# Refund Business Rules

## 1. Refund Amounts & Pro-Rata Deductions

1. **Full vs. Partial Returns**:
   - If an entire order is returned or cancelled before shipping: 100% of the paid amount (including standard shipping charges) is refunded.
   - If only a subset of items in a multi-item order is returned: the refund amount equals the item price minus any pro-rated cart coupon discount. Original shipping fees are non-refundable for partial returns.
2. **Pro-Rata Discount Clawback**:
   - If an applied coupon required a minimum spend of ₹2,000, and a partial return drops the net retained order value below ₹2,000, the coupon discount is clawed back proportionally from the refund amount.

## 2. Refund Processing & Payment Channels

1. **Primary Destination**:
   - By default, all refunds are credited back to the **original payment method** (UPI, Credit Card, NetBanking) via the payment gateway refund API.
2. **Gateway Processing SLA**:
   - Gateway refunds are initiated within 24 hours of return QC approval.
   - Bank processing typically takes 3-7 business days for Credit Cards and 24-48 hours for UPI.
3. **Idempotent Refund Execution**:
   - All refund API requests to gateways must pass a deterministic `refund_id` or idempotency token derived from the return ID to prevent double-refunding under network retry conditions.

## 3. Refund Ledger Auditability

1. **Double-Entry Balance Accounting**:
   - Every refund is recorded with debit/credit entries in the Refund Service database:
     - `DEBIT: Customer Refund Clearing Account`
     - `CREDIT: Gateway Settlement Account`
2. **Credit Notes**:
   - Every approved refund triggers the automated generation of an official GST Credit Note linked to the original tax invoice.
