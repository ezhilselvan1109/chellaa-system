# Business Requirements Document (BRD)
## Document 10: Enterprise Business Glossary

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-10 |
| **Document Title** | Standardized Domain Definitions and Business Glossary |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.0.0-DRAFT |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Glossary Purpose & Usage Principles

This glossary defines standard business and technical terminology used across the Chellaa E-Commerce Platform requirements baseline. Its purpose is to ensure complete semantic consistency between business stakeholders, functional requirements, workflows, and subsequent software architecture specifications.

---

## 2. Standardized Business Terminology

### 2.1 Catalog, Merchandising & Products

#### Product (Parent Product)
* **Domain:** Catalog Merchandising
* **Business Definition:** The conceptual commercial offering representing an electronics item (e.g., "Apple iPhone 15 Pro", "Dell XPS 15 Laptop"). A parent product defines shared characteristics (brand, category, marketing copy, warranty duration) but is not directly purchasable if it has child variants.
* **Synonyms / Related Terms:** Base Product, Master Product, Parent Item.

#### Product Variant (Child Variant)
* **Domain:** Catalog Merchandising
* **Business Definition:** The specific, physical sellable configuration of a product distinguished by one or more variant dimensions (e.g., "iPhone 15 Pro, 256GB, Natural Titanium"). Each variant maintains its own unique SKU, barcode, price, and inventory balance.
* **Synonyms / Related Terms:** Sellable Variant, SKU Unit, Child Product.

#### SKU (Stock Keeping Unit)
* **Domain:** Catalog & Inventory
* **Business Definition:** An alphanumeric code assigned internally to uniquely identify a specific sellable variant for inventory tracking and warehouse operations (e.g., `APL-IP15P-256-NT`). Must be globally unique across the enterprise.
* **Synonyms / Related Terms:** Product Code, Item Number.

#### Barcode (EAN / UPC)
* **Domain:** Catalog & Warehousing
* **Business Definition:** A standardized machine-readable numerical code (typically 12-digit UPC-A or 13-digit EAN-13) printed on manufacturer retail packaging, scanned during inward receiving, picking, packing, and returns.
* **Synonyms / Related Terms:** Universal Product Code, GTIN, EAN-13.

#### Brand
* **Domain:** Merchandising
* **Business Definition:** The recognized commercial manufacturer or trademark under which goods are produced (e.g., Apple, Samsung, Sony, Dell, Lenovo).
* **Synonyms / Related Terms:** Manufacturer, Make.

#### Category Hierarchy
* **Domain:** Catalog Merchandising
* **Business Definition:** A tree-structured taxonomy used to organize products into logical browsing tiers (e.g., Root Category: `Electronics` $\rightarrow$ Parent: `Computers & Laptops` $\rightarrow$ Child: `Gaming Laptops`). Products can belong to multiple categories, but have exactly one primary leaf category.
* **Synonyms / Related Terms:** Taxonomy, Navigation Tree, Department.

#### Attribute & Specification Schema
* **Domain:** Catalog Merchandising
* **Business Definition:** Standardized technical parameters defined at the category level (e.g., "RAM Capacity", "Battery mAh", "Bluetooth Version") that must be populated for products belonging to that category to enable faceted search and customer comparison.
* **Synonyms / Related Terms:** Technical Specs, Product Attributes, Facets.

---

### 2.2 Pricing, Taxes & Financial Documents

#### Maximum Retail Price (MRP)
* **Domain:** Pricing & Legal Compliance
* **Business Definition:** The highest statutory price declared by the manufacturer at which a product can be sold to end consumers in India. The selling price on the platform can never legally exceed MRP.
* **Synonyms / Related Terms:** List Price, Compare-At Price, Sticker Price.

#### Selling Price (Offer Price)
* **Domain:** Pricing Management
* **Business Definition:** The actual gross price at which the retailer offers the variant for purchase to the consumer. Includes applicable statutory taxes and baseline merchant discounts.
* **Synonyms / Related Terms:** Buy Price, Storefront Price, Deal Price.

#### Goods and Services Tax (GST)
* **Domain:** Taxation & Compliance
* **Business Definition:** The comprehensive indirect consumption tax applicable in India. Consists of Central GST (CGST) and State GST (SGST) for intra-state transactions, or Integrated GST (IGST) for inter-state interstate shipments.
* **Synonyms / Related Terms:** Statutory Tax, Indirect Tax.

#### HSN Code (Harmonized System of Nomenclature)
* **Domain:** Taxation & Compliance
* **Business Definition:** A standardized 6-to-8-digit commodity classification code used by tax authorities to classify goods for GST taxation rates (e.g., HSN `8517` for Mobile Phones).
* **Synonyms / Related Terms:** Tariff Code, Commodity Code.

#### Tax Invoice
* **Domain:** Financial Accounting
* **Business Definition:** An official, legally binding, sequentially numbered financial document issued by the enterprise upon confirmed payment, detailing buyer/seller GSTINs, HSN codes, itemized base prices, and statutory tax amounts.
* **Synonyms / Related Terms:** Bill of Sale, Commercial Invoice.

#### Credit Note
* **Domain:** Financial Accounting
* **Business Definition:** An official, sequentially numbered financial document issued by the enterprise to reverse taxable revenue and GST liability upon an approved customer order cancellation or product return.
* **Synonyms / Related Terms:** Refund Voucher, Credit Memorandum.

---

### 2.3 Inventory, Stock & Warehousing

#### On-Hand Inventory
* **Domain:** Inventory Management
* **Business Definition:** The total physical quantity of a specific SKU currently physically present within the enterprise warehouse bins.
* **Synonyms / Related Terms:** Physical Stock, Total Warehouse Count.

#### Reserved Inventory (Stock Hold)
* **Domain:** Inventory Management
* **Business Definition:** The quantity of a specific SKU temporarily held for orders currently in checkout or pending payment. Reserved stock cannot be purchased by other customers.
* **Synonyms / Related Terms:** Allocated Stock, Committed Units, Hold Quantity.

#### Safety Stock (Buffer Stock)
* **Domain:** Inventory Management
* **Business Definition:** A configured minimum threshold of physical stock held in reserve to absorb accidental warehouse damage, shrinkage, or physical inventory discrepancies.
* **Synonyms / Related Terms:** Buffer Threshold, Quarantine Minimum.

#### Available-to-Sell (ATS)
* **Domain:** Inventory Management
* **Business Definition:** The exact quantity of an SKU available for immediate customer purchase, calculated strictly as: $\text{On-Hand} - \text{Reserved} - \text{Safety Stock}$.
* **Synonyms / Related Terms:** Sellable Inventory, Net Available Stock.

#### Stockout / Overselling
* **Domain:** Inventory Management
* **Business Definition:** An operational failure where an order is accepted for an item that cannot be fulfilled due to lack of physical stock. The platform enforces zero tolerance ($0.00\%$) for overselling.
* **Synonyms / Related Terms:** Phantom Stock, Out-of-Stock Sale.

#### Inward Put-Away
* **Domain:** Warehouse Operations
* **Business Definition:** The physical and logical process of unloading supplier delivery consignments, inspecting packaging, scanning barcodes into quarantine, and shelving items into designated warehouse bin coordinates.
* **Synonyms / Related Terms:** Receiving, Dock-to-Stock.

---

### 2.4 Shopping Experience, Cart & Orders

#### Shopping Cart Snapshot
* **Domain:** Shopping Cart
* **Business Definition:** A temporary data store holding items selected by a customer for purchase. Cart items do NOT hold inventory reservations until checkout is initiated.
* **Synonyms / Related Terms:** Basket, Shopping Bag.

#### Idempotency Key
* **Domain:** Checkout & Payments
* **Business Definition:** A unique client-generated cryptographic token attached to checkout submissions and payment requests. Ensures that network retries or multiple button clicks execute the business transaction exactly once.
* **Synonyms / Related Terms:** Deduplication Token, Request Nonce.

#### Order
* **Domain:** Order Management
* **Business Definition:** The legal and commercial contract established between the customer and enterprise once checkout is submitted. An order records an immutable historical snapshot of purchased items, prices, shipping addresses, and applied taxes.
* **Synonyms / Related Terms:** Purchase Order, Sales Order.

#### Order Item Snapshot
* **Domain:** Order Management
* **Business Definition:** An immutable freeze of variant title, SKU, unit price, applied coupon discount, and tax rate captured at the exact moment of order placement. Insulates placed orders from subsequent catalog price updates.
* **Synonyms / Related Terms:** Historical Line Item.

---

### 2.5 Payments, Gateways & Logistics

#### Payment Authorization
* **Domain:** Payments
* **Business Definition:** The banking verification step where a customer's issuing bank verifies card credentials and reserves the payable amount on the customer's account without transferring funds yet.
* **Synonyms / Related Terms:** Pre-Auth, Hold.

#### Payment Capture
* **Domain:** Payments
* **Business Definition:** The transaction settlement step where authorized funds are formally transferred from the customer's account to the payment gateway / merchant account.
* **Synonyms / Related Terms:** Settlement, Charge.

#### Payment Reconciliation
* **Domain:** Financial Accounting
* **Business Definition:** The daily automated auditing process of matching payment gateway transaction references, fees, and bank deposits against internal confirmed order records to detect settlement variances.
* **Synonyms / Related Terms:** Gateway Settlement Audit, Two-Way Match.

#### Air Waybill (AWB)
* **Domain:** Shipping & Logistics
* **Business Definition:** A unique tracking number and barcode issued by a freight courier partner upon consignment booking, affixed to the parcel shipping label to track physical transportation milestones.
* **Synonyms / Related Terms:** Consignment Number, Courier Tracking ID, Docket Number.

#### Return to Origin (RTO)
* **Domain:** Logistics & Delivery
* **Business Definition:** The shipping process where a parcel is transported back to the enterprise warehouse after delivery fails (e.g., customer unavailable after 3 attempts or rejected COD order).
* **Synonyms / Related Terms:** Undelivered Return, Carrier Bounce.

#### Proof of Delivery (POD)
* **Domain:** Logistics & Delivery
* **Business Definition:** Formal verification that a parcel reached the customer, captured via recipient signature, delivery boy GPS geo-coordinates, or customer OTP verification at doorstep.
* **Synonyms / Related Terms:** Delivery Receipt, OTP Confirmation.

---

### 2.6 Post-Order, Governance & Operations

#### Reverse Logistics
* **Domain:** Returns & Refunds
* **Business Definition:** The coordinated physical pickup of returned or defective electronics from the customer's doorstep back to the enterprise fulfillment center for inspection.
* **Synonyms / Related Terms:** Return Transit, Inward Customer Pickup.

#### Role-Based Access Control (RBAC)
* **Domain:** Security & Administration
* **Business Definition:** A security governance framework restricting administrative actions based on authorized staff roles (Catalog Manager, Warehouse Operator, Customer Support, Finance Specialist, Administrator).
* **Synonyms / Related Terms:** Permission Scheme, Authorization Matrix.

#### Write-Once, Read-Many (WORM)
* **Domain:** Auditing & Compliance
* **Business Definition:** A data storage principle where audit logs and legal transaction records can be created and queried, but can never be modified, edited, or deleted by any user or administrator.
* **Synonyms / Related Terms:** Immutable Audit Trail, Append-Only Log.

#### Recovery Point Objective (RPO)
* **Domain:** Disaster Recovery
* **Business Definition:** The maximum acceptable duration of committed data loss during a catastrophic disaster (Target: $\le 5\text{ minutes}$).
* **Synonyms / Related Terms:** Maximum Data Loss Window.

#### Recovery Time Objective (RTO)
* **Domain:** Disaster Recovery
* **Business Definition:** The maximum acceptable elapsed duration between an outage event and the full restoration of operational platform services (Target: $\le 2\text{ hours}$).
* **Synonyms / Related Terms:** Target Restoration Time.

#### Correlation Identifier (Trace ID)
* **Domain:** Observability & SRE
* **Business Definition:** A unique identifier passed across all HTTP calls, message broker events, and background tasks belonging to a single user interaction, enabling distributed tracing across the system.
* **Synonyms / Related Terms:** Trace ID, Request ID, Correlation ID.
