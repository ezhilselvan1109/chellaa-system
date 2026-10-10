# Tax and Invoice Business Rules

## 1. Tax Calculation Model (GST Compliance)

1. **Intra-State vs. Inter-State Supply**:
   - Supply origin is the fulfilling warehouse state code. Supply destination is the customer's delivery address state code.
   - **Intra-State (Same State)**: Tax is split equally into **CGST** (Central GST) and **SGST** (State GST) (e.g., 18% slab splits into 9% CGST + 9% SGST).
   - **Inter-State (Different States)**: Tax is charged fully as **IGST** (Integrated GST) (e.g., 18% IGST).
2. **HSN Code Mapping**:
   - Every product SKU is associated with an official Harmonized System of Nomenclature (HSN) code and corresponding tax slab (0%, 5%, 12%, 18%, 28%).
3. **Inclusive Pricing Reverse-Calculation**:
   - Because consumer prices are tax-inclusive:
     $$\text{Taxable Value} = \frac{\text{Selling Price}}{1 + \left(\frac{\text{GST Rate}}{100}\right)}$$
     $$\text{Total GST} = \text{Selling Price} - \text{Taxable Value}$$

## 2. Tax Invoice Invariants

1. **Sequential Invoice Numbering**:
   - Invoices must follow a strictly sequential, unique, gapless numbering scheme per financial year: `INV/YYYY-YY/XXXXXX` (e.g. `INV/2026-27/000142`).
   - Gaps in invoice sequences are audited and prohibited by statutory regulations.
2. **Mandatory Invoice Fields**:
   - Company Legal Name, Registered Address, Corporate GSTIN.
   - Customer Name, Shipping Address, Billing Address, Customer GSTIN (if B2B).
   - Invoice Date, Sequential Invoice Number, Order ID reference.
   - Itemized table with HSN codes, quantity, unit price, discounts, taxable value, CGST/SGST/IGST breakdown.
   - QR code containing IRN (Invoice Reference Number) for e-invoicing compliance where applicable.
3. **Invoice Immutability & Storage**:
   - Once generated, invoice PDFs are uploaded to immutable S3 object storage with versioning enabled. Invoices must be retained for at least 8 years.

## 3. Credit Notes & Debit Notes

1. **Credit Note Generation**:
   - A legally valid GST Credit Note is automatically generated whenever a refund is issued or an order is cancelled post-invoicing, referencing the original invoice number and stating reason for issuance.
