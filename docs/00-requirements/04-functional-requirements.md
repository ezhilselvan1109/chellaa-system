# Business Requirements Document (BRD)
## Document 04: Functional Requirements Specification

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-04 |
| **Document Title** | Detailed Functional Requirements Specification |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.0.0-DRAFT |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Specification Standards & Requirement Template

All functional requirements in this document strictly adhere to the formalized business engineering template. Each requirement describes *what* the platform must accomplish from a business perspective, avoiding premature technical solution prescriptions (such as database locking mechanisms, REST endpoints, or message broker topics).

---

## 2. Business Administration & Access Control

### FR-ADM-001: Administrative User Account & Role Assignment
* **Requirement ID:** `FR-ADM-001`
* **Requirement Title:** Manage Internal Operator Accounts and Role Permissions
* **Feature ID:** `FEAT-ADM-001`
* **Business Objective:** `OBJ-002`, `OBJ-008`
* **Requirement Statement:** The system must allow authorized administrators to create, update, suspend, and deactivate internal staff user accounts, and assign them strictly bounded business roles enforcing the principle of least privilege.
* **Description:** Provides administrative user lifecycle management. Standard predefined roles include: Catalog Manager, Inventory Operator, Fulfillment Specialist, Customer Support Representative, Finance Specialist, and System Administrator.
* **Primary Actor:** `ACT-002` (System Administrator)
* **Preconditions:** Administrator is authenticated with active administrative session and MFA.
* **Trigger:** Administrator submits request to create, update, or alter role assignments of a staff account.
* **Main Success Scenario:**
  1. Administrator submits operator details (name, corporate email, mobile number, assigned role).
  2. System validates email format and ensures uniqueness across staff accounts.
  3. System provisions the account in `PENDING_ACTIVATION` status and generates a secure one-time invitation link.
  4. Operator accesses link, sets a strong password complying with security policy, and activates account.
  5. System records account creation in the administrative audit log.
* **Alternative Scenarios:**
  * Administrator suspends an account: System immediately invalidates all active sessions for that user and marks status as `SUSPENDED`.
* **Exception and Failure Scenarios:**
  * Duplicate email: System rejects creation and displays an error indicating the email is already assigned.
  * Attempt to assign unauthorized role: System denies action and logs a security privilege violation alert.
* **Business Rules:** `BR-ADM-001`, `BR-ADM-002`, `BR-SEC-002`.
* **Inputs and Business Information Required:** Full name, corporate email address, mobile number, role identifier, business justification note.
* **Expected Business Outcomes:** Staff members have access strictly to their operational domains; zero unauthorized cross-domain actions.
* **Dependencies:** `FEAT-AUD-001`, `FEAT-SEC-001`.
* **Acceptance Criteria:**
  * **Given** an authenticated System Administrator, **When** they create a new staff account with a unique email and valid role, **Then** the account is created and an activation dispatch is triggered.
  * **Given** an active staff account, **When** the administrator marks the account as `SUSPENDED`, **Then** the operator cannot authenticate, and any existing active session tokens are immediately rejected.
  * **Given** a staff user assigned the "Catalog Manager" role, **When** they attempt to authorize a customer refund, **Then** the system strictly denies the operation with an authorization failure.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-016`.

---

## 3. Customer Account & Profile Management

### FR-CUST-001: Customer Registration & Phone Verification
* **Requirement ID:** `FR-CUST-001`
* **Requirement Title:** Customer Registration with Multi-Factor Verification
* **Feature ID:** `FEAT-CUST-001`
* **Business Objective:** `OBJ-001`, `OBJ-008`
* **Requirement Statement:** The system must allow retail customers to register an account using their mobile number and/or email address, mandating cryptographic OTP verification before account activation.
* **Description:** Establishes the customer identity. Prevents fraudulent account generation by verifying ownership of the communication channel.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer is not currently logged in.
* **Trigger:** Customer submits registration form on storefront.
* **Main Success Scenario:**
  1. Customer enters mobile phone number, full name, and password.
  2. System validates phone number format (E.164 / 10-digit national format) and verifies it is not registered to an active account.
  3. System generates a secure 6-digit OTP with a 5-minute validity window and triggers SMS dispatch.
  4. Customer enters OTP within the validity window.
  5. System validates OTP, activates the customer account, establishes an authenticated session, and records marketing consent timestamps.
* **Alternative Scenarios:**
  * Customer registers via Email: System issues an email verification link or email OTP.
* **Exception and Failure Scenarios:**
  * Expired OTP: System rejects verification, informs the customer that the OTP has expired, and offers a "Resend OTP" button.
  * Exceeded OTP attempts: If 3 incorrect OTP entries occur, the OTP is invalidated and a 15-minute cooldown is enforced.
  * Existing Phone Number: System blocks registration and prompts user to log in or use account recovery.
* **Business Rules:** `BR-CUST-001`, `BR-SEC-002`, `BR-NTF-001`.
* **Inputs and Business Information Required:** Mobile phone number, full name, email address (optional at signup), password, marketing consent opt-in flag.
* **Expected Business Outcomes:** High registration conversion with 100% verified mobile identities.
* **Dependencies:** `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** a new customer with a valid, unregistered phone number, **When** they submit registration, **Then** a 6-digit OTP is generated and dispatched via SMS within 5 seconds.
  * **Given** an issued OTP, **When** the customer submits the correct code within 5 minutes, **Then** the account status transitions to `ACTIVE`.
  * **Given** an entered OTP, **When** the code is submitted after 5 minutes, **Then** the system rejects activation with an "OTP Expired" error.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** See [OQ-010](file:///d:/learning/Microservice/backend/docs/00-requirements/08-assumptions-dependencies-open-questions.md#oq-010).
* **Related Workflow IDs:** `WF-001`.

### FR-CUST-002: Customer Address Book Management
* **Requirement ID:** `FR-CUST-002`
* **Requirement Title:** Multiple Delivery Address Book with Serviceability Validation
* **Feature ID:** `FEAT-CUST-002`
* **Business Objective:** `OBJ-001`, `OBJ-006`
* **Requirement Statement:** The system must allow registered customers to store, update, delete, and designate default shipping and billing addresses, validating PIN-code serviceability at address creation.
* **Description:** Provides customers with an address repository to streamline repeat checkout.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer is authenticated.
* **Trigger:** Customer adds or edits an address in their profile or during checkout.
* **Main Success Scenario:**
  1. Customer inputs recipient name, contact phone, street address, landmark, PIN code, city, and state.
  2. System validates PIN code against courier serviceability registry.
  3. Customer designates address as "Default Shipping" or "Default Billing".
  4. System persists address linked to customer profile.
* **Alternative Scenarios:**
  * Customer marks a new address as default: System automatically removes default flag from previously designated default address.
* **Exception and Failure Scenarios:**
  * Unserviceable PIN code: System alerts customer that the entered PIN code is currently outside delivery coverage, but permits saving for profile reference while restricting checkout delivery.
* **Business Rules:** `BR-CUST-002`, `BR-SHP-001`.
* **Inputs and Business Information Required:** Recipient name, mobile number, address line 1, address line 2, landmark, postal PIN code, city, state, country, address type (Home/Work).
* **Expected Business Outcomes:** Accurate postal data capture reducing courier misdirection and RTO rates.
* **Dependencies:** `FEAT-SHP-001`.
* **Acceptance Criteria:**
  * **Given** an authenticated customer, **When** they add a new address and select "Make Default", **Then** the new address is saved and any previous default address loses default status.
  * **Given** an address record, **When** a customer attempts to delete an address currently associated with an in-transit order, **Then** the address is marked inactive for future orders while preserving the historical snapshot on the active order.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-001`, `WF-006`.

---

## 4. Product Catalog & Electronics Merchandising

### FR-CAT-001: Multi-Variant Electronics Product Creation & Governance
* **Requirement ID:** `FR-CAT-001`
* **Requirement Title:** Manage Multi-Variant Electronics Products
* **Feature ID:** `FEAT-CAT-001`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must allow catalog managers to define parent consumer electronics products and configure sellable variants across multi-dimensional axes (storage, color, RAM, processor), with each variant maintaining a distinct SKU, barcode, price, and status.
* **Description:** Core merchandising engine for consumer electronics. Allows creation of base products (e.g., "Galaxy S24 Ultra") and distinct child variants (e.g., "512GB Titanium Gray").
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Relevant Brand and Category exist and are active.
* **Main Success Scenario:**
  1. Catalog manager enters product title, brand, primary category, model number, description, and warranty terms.
  2. Manager defines variant dimensions (e.g., Color = [Phantom Black, Cream]; Storage = [256GB, 512GB]).
  3. System generates variant matrix and prompts manager to assign unique SKU, barcode (EAN/UPC), Selling Price, and MRP for each variant.
  4. Manager saves product in `DRAFT` status, reviews preview, and submits for publication.
  5. System transitions product and variants to `PUBLISHED` status, publishing them to search and discovery indices.
* **Alternative Scenarios:**
  * Single product without variants (e.g., USB Cable): System creates a simple product with one default sellable SKU.
* **Exception and Failure Scenarios:**
  * Duplicate SKU or Barcode: System rejects submission and flags the exact duplicate SKU conflicting with existing inventory.
  * Selling Price exceeds MRP: System rejects submission with a pricing validation error.
* **Business Rules:** `BR-CAT-001`, `BR-CAT-002`, `BR-PRC-001`.
* **Inputs and Business Information Required:** Product name, brand ID, category ID, description, variant attribute key-values, SKU codes, barcodes, MRP, Selling Price, dimensions, weight, warranty details.
* **Expected Business Outcomes:** 100% SKU uniqueness, rich product discovery, structured technical data.
* **Dependencies:** `FEAT-BRD-001`, `FEAT-CTG-001`, `FEAT-PRC-001`.
* **Acceptance Criteria:**
  * **Given** valid parent product metadata, **When** a catalog manager configures 2 color options and 2 storage options, **Then** exactly 4 distinct child variant records are generated, each requiring a unique SKU.
  * **Given** an attempt to create a variant with an existing SKU, **When** saved, **Then** the system rejects the operation with a "Duplicate SKU" error.
  * **Given** a product in `DRAFT` status, **When** queried via public storefront APIs, **Then** the product is completely omitted from customer search and category browsing.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

### FR-CAT-002: Category Technical Specification Schema Association
* **Requirement ID:** `FR-CAT-002`
* **Requirement Title:** Enforce Category-Specific Technical Attribute Schemas
* **Feature ID:** `FEAT-CTG-002`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must enforce category-defined technical specification schemas, ensuring that products assigned to a category capture mandatory technical attributes before publication.
* **Description:** Guarantees data consistency across electronics categories (e.g., all smartphones must have Battery Capacity, Screen Resolution, Processor; all Laptops must have Graphics Card, RAM Type).
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Category specification schema is configured.
* **Main Success Scenario:**
  1. Manager selects category "Laptops" for a new product.
  2. System dynamically renders specification input form containing defined attributes (CPU, RAM, Storage Type, Display Size, OS).
  3. Manager populates values for all attributes marked as "Mandatory".
  4. System validates data types (e.g., Display Size is numeric, OS is select option) and saves specifications.
* **Alternative Scenarios:**
  * Optional attributes left blank: System permits publication but flags specification completeness score as partial.
* **Exception and Failure Scenarios:**
  * Missing mandatory attribute: System blocks product publication and highlights missing required specifications.
* **Business Rules:** `BR-CTG-002`.
* **Inputs and Business Information Required:** Category ID, attribute ID, attribute value, unit of measurement (GB, GHz, Inches).
* **Expected Business Outcomes:** Standardized, high-quality technical specifications facilitating faceted search and customer comparison.
* **Dependencies:** `FEAT-CTG-001`.
* **Acceptance Criteria:**
  * **Given** a category with mandatory attribute "Battery Capacity (mAh)", **When** an administrator attempts to publish a product without providing this value, **Then** the publication is blocked with a validation error.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

---

## 5. Inventory & Multi-Warehouse Operations

### FR-INV-001: Prevent Ordering Unavailable Inventory (Oversell Prevention)
* **Requirement ID:** `FR-INV-001`
* **Requirement Title:** Strict Inventory Availability Validation and Oversell Prevention
* **Feature ID:** `FEAT-INV-001`
* **Business Objective:** `OBJ-003`
* **Requirement Statement:** The system must prevent an order from being placed or confirmed when the requested quantity cannot be fulfilled from current Available-to-Sell (ATS) inventory.
* **Description:** Foundational inventory protection rule. Eliminates overselling by computing real-time available stock and blocking checkout if stock is insufficient.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Product variant exists with inventory balance records.
* **Trigger:** Customer attempts to add item to cart, initiate checkout, or complete order placement.
* **Main Success Scenario:**
  1. Customer requests quantity $Q$ of variant SKU.
  2. System calculates current Available-to-Sell stock: $\text{ATS} = \text{On-Hand} - \text{Reserved} - \text{Safety Stock}$.
  3. System verifies $Q \le \text{ATS}$.
  4. System permits operation and reserves requested quantity during checkout initiation.
* **Alternative Scenarios:**
  * Partial stock available: If customer requests 3 units but ATS is 2, system notifies customer that only 2 units are available and adjusts requested quantity with customer consent.
* **Exception and Failure Scenarios:**
  * Zero stock ($\text{ATS} = 0$): System blocks checkout initiation, disables "Add to Cart", and marks item as "Out of Stock".
  * Concurrent checkout contention: Two customers attempt to buy the last unit simultaneously; system processes the first request to lock reservation, and immediately fails the second with an "Out of Stock" notification.
* **Business Rules:** `BR-INV-001`, `BR-INV-002`, `BR-INV-003`.
* **Inputs and Business Information Required:** Variant SKU, requested quantity, fulfillment location ID.
* **Expected Business Outcomes:** Exactly zero oversold orders; zero unfulfillable cancellations due to inventory phantom stock.
* **Dependencies:** `FEAT-INV-002`, `FEAT-CHK-001`.
* **Acceptance Criteria:**
  * **Given** a variant with $\text{On-Hand} = 10$, $\text{Reserved} = 8$, $\text{Safety Stock} = 1$ ($\text{ATS} = 1$), **When** a customer requests quantity 2, **Then** the system rejects the checkout request with an insufficient stock notification.
  * **Given** a variant with $\text{ATS} = 1$, **When** two concurrent checkout requests attempt to reserve 1 unit simultaneously, **Then** exactly one reservation succeeds, and the other receives an out-of-stock rejection.
  * **Given** an inventory record, **When** any operation updates stock balance, **Then** the resulting physical available stock cannot be negative.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-005`, `WF-006`.

### FR-INV-002: Real-Time Stock Reservation & Automated Expiration
* **Requirement ID:** `FR-INV-002`
* **Requirement Title:** Checkout Stock Reservation Lifecycle and Automated Expiration
* **Feature ID:** `FEAT-INV-002`
* **Business Objective:** `OBJ-003`, `OBJ-004`
* **Requirement Statement:** The system must temporarily reserve inventory for all items in an order upon checkout initiation for a configurable time window (e.g., 15 minutes), automatically releasing the reserved stock back to ATS if payment is not completed before the expiration window.
* **Description:** Manages the temporary holding of stock while a customer interacts with the external payment gateway.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Customer submits checkout with sufficient ATS stock.
* **Trigger:** Checkout initiated; transition to payment selection.
* **Main Success Scenario:**
  1. System creates an inventory reservation record linking Order ID, SKU, Quantity, and Expiration Timestamp ($T_{\text{now}} + 15\text{ mins}$).
  2. System increments `Reserved` stock balance, which decrements `ATS` by the reserved quantity.
  3. Customer completes payment within 15 minutes.
  4. Payment confirmation triggers conversion of `Reserved` stock into permanently allocated/deducted stock.
  5. Reservation record transitions to `COMMITTED`.
* **Alternative Scenarios:**
  * Customer cancels checkout: System immediately cancels reservation and returns stock to ATS.
* **Exception and Failure Scenarios:**
  * Payment timeout / Abandoned checkout: Expiration timestamp passes without payment confirmation. Background reservation reaper automatically releases the reservation, decrements `Reserved`, and restores `ATS`.
* **Business Rules:** `BR-INV-003`, `BR-PAY-002`.
* **Inputs and Business Information Required:** Order reference, SKU list, reserved quantities, reservation TTL parameter.
* **Expected Business Outcomes:** Stock is protected during active payment, while abandoned checkouts never permanently lock up inventory.
* **Dependencies:** `FEAT-CHK-001`, `FEAT-PAY-001`.
* **Acceptance Criteria:**
  * **Given** a reservation created at 10:00:00 with a 15-minute TTL, **When** no payment confirmation is received by 10:15:01, **Then** the reservation status becomes `EXPIRED` and ATS stock is immediately incremented by the reserved quantity.
  * **Given** an active reservation, **When** a valid payment success webhook is processed, **Then** the reservation is permanently committed and on-hand stock is deducted.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`, `WF-007`.

---

## 6. Pricing, Tax & Financial Invoicing

### FR-PRC-001: Price Validation & Cart Revalidation
* **Requirement ID:** `FR-PRC-001`
* **Requirement Title:** Enforce Pricing Rules and Cart Price Change Revalidation
* **Feature ID:** `FEAT-PRC-001`
* **Business Objective:** `OBJ-001`, `OBJ-008`
* **Requirement Statement:** The system must enforce that Selling Price never exceeds MRP, and must revalidate cart item prices at checkout initiation, notifying the customer of any price fluctuations that occurred while items were in the cart.
* **Description:** Protects legal retail pricing boundaries and ensures customer transparency when prices change between adding to cart and checking out.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Products exist in customer cart.
* **Trigger:** Customer navigates to checkout screen.
* **Main Success Scenario:**
  1. System compares price stored in cart snapshot against active catalog selling price for each line item.
  2. If prices match, checkout proceeds with current active price.
* **Alternative Scenarios:**
  * Price changed (increased or decreased): System updates cart with new active price, highlights the modified items, displays a notification ("The price of [Product] has changed from ₹12,999 to ₹11,999"), and requires explicit customer acknowledgement before proceeding.
* **Exception and Failure Scenarios:**
  * Administrative attempt to set $\text{Selling Price} > \text{MRP}$: System rejects catalog price update with a validation error.
* **Business Rules:** `BR-PRC-001`, `BR-CART-001`.
* **Inputs and Business Information Required:** Cart item SKU, cart snapshot price, catalog active price, catalog MRP.
* **Expected Business Outcomes:** Zero price discrepancies between checkout display and payment gateway authorization amount.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-CART-001`.
* **Acceptance Criteria:**
  * **Given** an item added to cart at ₹1,000, **When** the catalog price changes to ₹1,200 before checkout, **Then** the checkout system updates the cart total and requires customer re-confirmation.
  * **Given** a catalog price update request, **When** Selling Price is set to ₹5,500 and MRP is ₹5,000, **Then** the update is rejected.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-005`, `WF-006`.

### FR-FIN-001: Sequential Tax Invoice & Credit Note Generation
* **Requirement ID:** `FR-FIN-001`
* **Requirement Title:** Automated Sequential Tax Invoice and Credit Note Issuance
* **Feature ID:** `FEAT-FIN-002`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must generate a legally compliant, sequentially numbered, tamper-evident tax invoice upon order payment confirmation, and issue an official credit note upon return/refund approval, retaining immutable financial records for statutory auditing.
* **Description:** Manages statutory financial document creation. Enforces strict sequential numbering and itemized tax breakdown (CGST, SGST, IGST).
* **Primary Actor:** System (Automated), `ACT-007` (Finance Officer)
* **Preconditions:** Order payment status is `PAID` or Return status is `APPROVED`.
* **Trigger:** Payment confirmation event or refund authorization event.
* **Main Success Scenario:**
  1. System allocates the next strictly sequential invoice number from the financial sequence generator (e.g., `INV-2026-00042`).
  2. System captures immutable snapshot of seller GSTIN, buyer billing/shipping address, item HSN codes, taxable base values, and applicable tax rates.
  3. System compiles and renders a PDF invoice, stores it in secure object storage, and attaches the download link to the customer order record.
  4. System notifies customer that the invoice is ready for download.
* **Alternative Scenarios:**
  * Partial return approved: System allocates a sequential Credit Note number (e.g., `CN-2026-00012`) referencing the original invoice number, detailing refunded items and reversed tax amounts.
* **Exception and Failure Scenarios:**
  * Sequence generator contention: System ensures atomic incrementing with zero duplicate invoice numbers under concurrent traffic.
* **Business Rules:** `BR-FIN-001`, `BR-FIN-002`, `BR-FIN-003`.
* **Inputs and Business Information Required:** Order ID, payment transaction ID, customer billing info, HSN codes, tax breakdown, seller corporate metadata.
* **Expected Business Outcomes:** 100% tax compliance; zero missing or duplicated invoice numbers.
* **Dependencies:** `FEAT-PAY-001`, `FEAT-ORD-001`.
* **Acceptance Criteria:**
  * **Given** a confirmed and paid order, **When** invoice generation executes, **Then** an invoice is generated with a unique, monotonically increasing sequential number.
  * **Given** an issued invoice, **When** an administrator or customer attempts to modify invoice line items or tax rates, **Then** the system strictly prohibits edits to the generated document.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`, `WF-010`.

---

## 7. Product Search & Discovery

### FR-SRCH-001: Full-Text Search and Faceted Filtering
* **Requirement ID:** `FR-SRCH-001`
* **Requirement Title:** Full-Text Search with Multi-Attribute Faceted Navigation
* **Feature ID:** `FEAT-SRCH-001`
* **Business Objective:** `OBJ-001`, `OBJ-002`
* **Requirement Statement:** The system must provide typo-tolerant full-text search across product titles, descriptions, brands, categories, and SKUs, allowing customers to dynamically filter results across category-specific facets (RAM, storage, brand, price range).
* **Description:** Primary customer discovery engine. Delivers relevant search results in $< 200\text{ms}$.
* **Primary Actor:** `ACT-009` (Customer), `ACT-010` (Guest)
* **Preconditions:** Products are in `PUBLISHED` status and indexed.
* **Trigger:** User enters query in search bar or selects a category filter.
* **Main Success Scenario:**
  1. User enters query (e.g., "iphone 15 pro 256gb").
  2. System executes search matching title, brand, and variant attributes with fuzzy typo tolerance.
  3. System returns matching product list along with calculated aggregation facets (available brands, storage options, price ranges, rating counts).
  4. User clicks facet "RAM: 8 GB"; system refines results to matching subset instantly.
* **Alternative Scenarios:**
  * Zero exact matches: System returns suggested alternatives ("Did you mean...?") based on closest phonetic or synonym matches.
* **Exception and Failure Scenarios:**
  * Search service failure: System gracefully degrades to basic database title search, preserving catalog browsing.
* **Business Rules:** `BR-SRCH-001`, `BR-SRCH-002`.
* **Inputs and Business Information Required:** Search query string, selected facet key-values, sort option, pagination offset/limit.
* **Expected Business Outcomes:** High search relevance, low zero-result rates, fast customer discovery.
* **Dependencies:** `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** published products in catalog, **When** a user searches with a minor typo (e.g., "samsng"), **Then** relevant Samsung products are returned.
  * **Given** unpublished or draft products, **When** searched by exact title or SKU, **Then** they are strictly excluded from search results.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-004`.

---

## 8. Shopping Cart & Checkout Orchestration

### FR-CHK-001: Idempotent Checkout & Order Placement Validation
* **Requirement ID:** `FR-CHK-001`
* **Requirement Title:** Secure Checkout Orchestration with Idempotency Protection
* **Feature ID:** `FEAT-CHK-001`
* **Business Objective:** `OBJ-001`, `OBJ-004`, `OBJ-005`
* **Requirement Statement:** The system must validate delivery address serviceability, active item pricing, promotional discounts, and inventory availability during checkout, enforcing idempotent order submission to prevent duplicate orders or multiple charges.
* **Description:** Orchestrates the transition from shopping cart to formal order. Enforces server-side recalculation and idempotency key checks.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Cart contains at least one active item; delivery address is selected.
* **Trigger:** Customer clicks "Proceed to Payment".
* **Main Success Scenario:**
  1. Client sends checkout request with unique client-generated Idempotency Key.
  2. Server verifies that the Idempotency Key has not been previously processed.
  3. Server recalculates subtotal, applies verified coupon discount, calculates shipping fee and taxes based on shipping address.
  4. Server verifies ATS stock for all items and creates atomic stock reservation (`FR-INV-002`).
  5. System creates an Order record in `PENDING_PAYMENT` status.
  6. System returns payment gateway session parameters to client for payment execution.
* **Alternative Scenarios:**
  * Duplicate submission (e.g., customer double-clicks button or network retries): System detects existing Idempotency Key, halts duplicate processing, and returns the existing pending checkout response.
* **Exception and Failure Scenarios:**
  * Price change or stockout detected during checkout: System halts order placement, updates cart, and alerts customer to the variance.
  * Delivery address unserviceable: System halts checkout and prompts customer to select a different delivery address.
* **Business Rules:** `BR-CHK-001`, `BR-CHK-002`, `BR-PRC-001`, `BR-INV-001`.
* **Inputs and Business Information Required:** Idempotency Key, customer ID, delivery address ID, billing address ID, cart ID, coupon code (optional).
* **Expected Business Outcomes:** Exactly one order created per customer purchase intent; zero duplicate orders.
* **Dependencies:** `FEAT-CART-001`, `FEAT-INV-002`, `FEAT-FIN-001`, `FEAT-PRM-001`.
* **Acceptance Criteria:**
  * **Given** an identical checkout request submitted twice with the same Idempotency Key, **When** processed, **Then** only one Order is created, and both requests receive the identical order reference.
  * **Given** an order submission where client attempts to pass a tampered order total, **When** processed, **Then** the server ignores the client value, recalculates the true total server-side, and authorizes only the verified amount.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`.

---

## 9. Payment Gateway Orchestration & Reconciliation

### FR-PAY-001: Payment Gateway Webhook Ingestion & Delayed Payment Resolution
* **Requirement ID:** `FR-PAY-001`
* **Requirement Title:** Resilient Payment Gateway Webhook Handling and Status Reconciliation
* **Feature ID:** `FEAT-PAY-001`
* **Business Objective:** `OBJ-005`
* **Requirement Statement:** The system must securely ingest, cryptographically verify, and idempotently process asynchronous payment gateway webhooks, resolving pending transactions and handling late payment success after reservation expiration.
* **Description:** Manages external payment outcomes. Guarantees that payment state transitions are handled reliably even during network dropouts or delayed callbacks.
* **Primary Actor:** `ACT-011` (Payment Gateway), System (Automated)
* **Preconditions:** Order created in `PENDING_PAYMENT` status with active payment attempt.
* **Trigger:** Payment gateway dispatches webhook notification (Payment Success, Payment Failed, Payment Authorized).
* **Main Success Scenario:**
  1. System receives webhook payload and verifies cryptographic HMAC signature using gateway secret.
  2. System checks whether payment event ID has already been processed (Idempotency check).
  3. System matches payment transaction reference against internal Order ID.
  4. System transitions Order status from `PENDING_PAYMENT` to `CONFIRMED`.
  5. System permanently commits inventory reservation (`FR-INV-002`), triggers invoice generation (`FR-FIN-001`), and queues order for warehouse fulfillment.
* **Alternative Scenarios:**
  * Payment Failed webhook: System marks payment attempt as `FAILED`, leaves order open for retry, or transitions to `CANCELLED` upon expiration.
* **Exception and Failure Scenarios:**
  * Invalid signature: System rejects webhook with HTTP 401 and logs security alert.
  * Delayed Payment Success after Stock Expiration: Customer pays, but gateway webhook arrives after the 15-minute reservation expired and the stock was sold to another customer. System detects insufficient stock, transitions order to `PAYMENT_RECEIVED_OUT_OF_STOCK`, and immediately triggers an automated full refund workflow (`FR-RET-002`).
* **Business Rules:** `BR-PAY-001`, `BR-PAY-002`, `BR-PAY-003`.
* **Inputs and Business Information Required:** Webhook signature header, payment transaction ID, order reference ID, payment amount, currency, status code.
* **Expected Business Outcomes:** 100% payment accuracy; zero lost payments; automated handling of race-condition edge cases.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-INV-002`, `FEAT-RET-002`.
* **Acceptance Criteria:**
  * **Given** an identical payment success webhook received 3 times due to gateway retries, **When** processed, **Then** the order is transitioned to `CONFIRMED` once, and subsequent events are acknowledged with HTTP 200 without duplicate order processing.
  * **Given** a payment success webhook where the underlying stock reservation has expired and inventory is zero, **When** processed, **Then** an automated refund is created, the order is cancelled, and the customer is notified.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-007`.

---

## 10. Order Management & Warehouse Fulfillment

### FR-ORD-001: Order State Transitions and Warehouse Picking/Packing
* **Requirement ID:** `FR-ORD-001`
* **Requirement Title:** Enforce Order Lifecycle State Machine and Warehouse Handover
* **Feature ID:** `FEAT-ORD-001`
* **Business Objective:** `OBJ-004`, `OBJ-006`
* **Requirement Statement:** The system must enforce valid order state transitions (`PENDING_PAYMENT` $\rightarrow$ `CONFIRMED` $\rightarrow$ `PROCESSING` $\rightarrow$ `PACKED` $\rightarrow$ `DISPATCHED` $\rightarrow$ `DELIVERED`), preventing illegal state jumps and coordinating warehouse picklist and packing slip generation.
* **Description:** Governs order operational progression. Ensures warehouse personnel follow standardized operational steps.
* **Primary Actor:** `ACT-005` (Fulfillment Specialist)
* **Preconditions:** Order status is `CONFIRMED`.
* **Trigger:** Warehouse operator accesses fulfillment queue.
* **Main Success Scenario:**
  1. Operator selects confirmed orders and generates consolidated Warehouse Picklist.
  2. Order status transitions to `PROCESSING`.
  3. Operator physically picks items, scans SKU barcodes to verify accuracy, and generates customer Packing Slip.
  4. Order status transitions to `PACKED`.
  5. System triggers courier consignment booking (`FR-SHP-001`).
* **Alternative Scenarios:**
  * Order cancellation requested while `CONFIRMED`: System permits immediate cancellation, releases inventory, and triggers automated refund.
* **Exception and Failure Scenarios:**
  * Attempt to cancel order in `DISPATCHED` status: System blocks self-service cancellation and informs customer that order has already left the warehouse and must follow return procedures.
  * Barcode mismatch during packing: System flags wrong-item error and prevents packing slip generation until correct item is scanned.
* **Business Rules:** `BR-ORD-001`, `BR-ORD-002`, `BR-ORD-003`.
* **Inputs and Business Information Required:** Order ID, operator ID, scanned SKU barcodes, warehouse bin location.
* **Expected Business Outcomes:** 100% packing accuracy; transparent fulfillment progression.
* **Dependencies:** `FEAT-INV-001`, `FEAT-SHP-001`.
* **Acceptance Criteria:**
  * **Given** an order in `PACKED` status, **When** an operator attempts to transition it directly to `DELIVERED` bypassing `DISPATCHED`, **Then** the transition is blocked by the state machine.
  * **Given** an order in `PROCESSING` status, **When** customer requests cancellation, **Then** fulfillment is aborted, items are returned to warehouse stock, and refund is initiated.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-008`, `WF-009`.

---

## 11. Shipping, Logistics & Delivery Tracking

### FR-SHP-001: Courier Consignment Booking & AWB Tracking Integration
* **Requirement ID:** `FR-SHP-001`
* **Requirement Title:** Automated Courier Booking, Shipping Label Issuance, and Tracking
* **Feature ID:** `FEAT-SHP-001`
* **Business Objective:** `OBJ-006`
* **Requirement Statement:** The system must integrate with courier logistics partner APIs to automatically book consignments upon packing, generate printable shipping labels with scannable AWB barcodes, and ingest real-time delivery tracking events.
* **Description:** Automates outbound shipping logistics and milestone visibility for both customers and operations.
* **Primary Actor:** `ACT-005` (Fulfillment Specialist), `ACT-012` (Courier Partner)
* **Preconditions:** Order status is `PACKED`.
* **Trigger:** Fulfillment operator confirms packing completion.
* **Main Success Scenario:**
  1. System submits consignment booking to courier partner API with parcel weight, dimensions, and customer address.
  2. Courier API returns Air Waybill (AWB) number and printable shipping label PDF.
  3. System attaches AWB number to order record and renders label for operator to affix to parcel.
  4. Courier picks up parcel; operator scans manifest; order transitions to `DISPATCHED`.
  5. Inbound courier webhooks (In-Transit, Out for Delivery, Delivered) automatically update order tracking timeline and dispatch customer notifications.
* **Alternative Scenarios:**
  * Delivery failed (Customer unavailable): Courier updates status to `DELIVERY_ATTEMPTED`; system alerts customer and schedules automatic re-attempt (up to 3 attempts).
* **Exception and Failure Scenarios:**
  * Courier API outage: System queues booking in an asynchronous retry queue with exponential backoff and alerts logistics manager if unfulfilled after 30 minutes.
  * Undeliverable after 3 attempts: Courier initiates Return to Origin (RTO); order transitions to `RTO_IN_TRANSIT`.
* **Business Rules:** `BR-SHP-001`, `BR-SHP-002`.
* **Inputs and Business Information Required:** Order ID, package weight (kg), dimensions (LxWxH cm), destination PIN code, carrier partner credentials.
* **Expected Business Outcomes:** Automated shipping workflow; sub-minute label generation; complete shipment traceability.
* **Dependencies:** `FEAT-INT-001`, `FEAT-ORD-001`, `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** an order in `PACKED` status, **When** consignment booking succeeds, **Then** an AWB number is stored and order transitions to `DISPATCHED` only upon manifest handover scan.
  * **Given** a courier webhook indicating `DELIVERED`, **When** received and verified, **Then** order transitions to `DELIVERED`, delivery timestamp is captured, and the return eligibility window opens.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-008`.

---

## 12. Returns, Replacements & Refunds

### FR-RET-001: Customer Return Request Lifecycle and Warehouse Inspection
* **Requirement ID:** `FR-RET-001`
* **Requirement Title:** Manage Return Requests, Reverse Logistics, and Warehouse Quality Inspection
* **Feature ID:** `FEAT-RET-001`
* **Business Objective:** `OBJ-007`, `OBJ-003`
* **Requirement Statement:** The system must allow customers to submit return/replacement requests for eligible delivered orders within a configured return policy window (e.g., 7 days), track reverse logistics pickup, and mandate physical quality inspection at the warehouse before refund authorization.
* **Description:** Governs reverse logistics for consumer electronics. Enforces policy deadlines and prevents fraudulent returns of used or swapped items.
* **Primary Actor:** `ACT-009` (Customer), `ACT-004` (Warehouse Operator)
* **Preconditions:** Order status is `DELIVERED` and current timestamp $\le \text{Delivery Timestamp} + 7\text{ days}$.
* **Trigger:** Customer submits return request on delivered order.
* **Main Success Scenario:**
  1. Customer selects delivered item, chooses return reason (e.g., "Defective item", "Wrong color sent"), uploads mandatory verification photos, and selects preference (Replacement or Refund).
  2. System validates return window eligibility and approves return pickup.
  3. Reverse logistics courier collects item and delivers parcel to enterprise warehouse.
  4. Warehouse operator inspects item: verifies serial number/IMEI matches shipped product, checks physical condition, and grades item.
  5. Operator submits inspection grade: `APPROVED_DEFECTIVE` or `APPROVED_RESTOCKABLE`.
  6. System triggers automated refund (`FR-RET-002`) or creates a replacement fulfillment order.
* **Alternative Scenarios:**
  * Inspection rejected (e.g., customer returned an old phone with mismatched serial number, or item shows physical customer damage): Operator marks inspection as `REJECTED` with photographic proof and explanation note. Parcel is returned to customer; refund is denied.
* **Exception and Failure Scenarios:**
  * Request submitted past 7 days: System disables return button and displays "Return window expired" message.
* **Business Rules:** `BR-RET-001`, `BR-RET-002`, `BR-RET-003`.
* **Inputs and Business Information Required:** Order ID, item SKU, return reason code, customer photos, inspection grade, inspector user ID.
* **Expected Business Outcomes:** High customer satisfaction on genuine defects; zero financial losses to return fraud.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-INV-001`, `FEAT-RET-002`.
* **Acceptance Criteria:**
  * **Given** an order delivered 8 days ago, **When** customer attempts to initiate return, **Then** the system blocks the request.
  * **Given** a returned product received at warehouse, **When** inspector verifies matching serial number and undamaged state, **Then** inspection approval initiates refund processing within 1 business hour.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** See [OQ-008](file:///d:/learning/Microservice/backend/docs/00-requirements/08-assumptions-dependencies-open-questions.md#oq-008).
* **Related Workflow IDs:** `WF-010`.

### FR-RET-002: Automated Refund Remittance to Source Instrument
* **Requirement ID:** `FR-RET-002`
* **Requirement Title:** Automated Refund Calculation and Remittance to Original Payment Source
* **Feature ID:** `FEAT-RET-002`
* **Business Objective:** `OBJ-005`, `OBJ-008`
* **Requirement Statement:** The system must automatically calculate eligible refund amounts (pro-rata item base price, item taxes, deducting non-refundable shipping fees where applicable) and disburse refunds directly to the customer's original payment source via payment gateway APIs.
* **Description:** Executes financial disbursement for cancelled or returned purchases. Prevents manual refund leakage.
* **Primary Actor:** System (Automated), `ACT-007` (Finance Officer)
* **Preconditions:** Return inspection approved or order cancelled prior to warehouse dispatch.
* **Trigger:** Return approval event or order cancellation event.
* **Main Success Scenario:**
  1. System calculates refundable amount based on line-item price minus itemized coupon discounts plus applicable line taxes.
  2. System verifies payment gateway transaction reference from original order.
  3. System calls payment gateway Refund API requesting remittance of exact calculated amount to original card/UPI account.
  4. Gateway confirms refund initiation with a gateway refund reference number.
  5. System generates official Credit Note (`FR-FIN-001`), marks refund status as `COMPLETED`, and notifies customer via SMS/Email with refund reference.
* **Alternative Scenarios:**
  * Refund on Cash on Delivery (COD) order: System prompts customer to securely provide verified bank account / UPI VPA for direct bank transfer.
* **Exception and Failure Scenarios:**
  * Gateway refund failure (e.g., customer closed credit card account): System transitions refund to `RETRY_QUEUED` and alerts finance officer for manual bank wire transfer.
* **Business Rules:** `BR-RET-003`, `BR-FIN-003`.
* **Inputs and Business Information Required:** Order ID, payment transaction ID, refund amount, reason code, authorized officer ID (if manual override).
* **Expected Business Outcomes:** Rapid customer refund turnaround; 100% auditable accounting records.
* **Dependencies:** `FEAT-PAY-001`, `FEAT-FIN-001`, `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** an approved return for an item paid via UPI, **When** refund processes, **Then** an API call is made to the payment gateway targeting the original transaction ID, and a Credit Note is generated.
  * **Given** an order where a ₹500 discount was applied across two items, **When** one item is returned, **Then** the refund reflects only the proportional discounted price paid, not the full pre-discount price.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-009`, `WF-010`.

---

## 13. Promotions, Coupons & Discounts

### FR-PRM-001: Coupon Redemption Engine and Non-Stacking Enforcement
* **Requirement ID:** `FR-PRM-001`
* **Requirement Title:** Promotional Coupon Validation, Usage Limits, and Discount Allocation
* **Feature ID:** `FEAT-PRM-001`
* **Business Objective:** `OBJ-001`, `OBJ-008`
* **Requirement Statement:** The system must validate coupon codes against eligibility criteria (validity window, minimum cart value, category/brand restrictions, per-customer usage limits), distribute discounts proportionally across line items, and strictly prohibit unauthorized coupon stacking.
* **Description:** Promotional engine driving customer incentives while enforcing margin protection rules.
* **Primary Actor:** `ACT-009` (Customer), `ACT-008` (Marketing Manager)
* **Preconditions:** Promotional coupon configured and active.
* **Trigger:** Customer applies coupon code in shopping cart or checkout.
* **Main Success Scenario:**
  1. Customer enters coupon code (e.g., `TECHFEST10`).
  2. System verifies coupon status: active, current time within start/end dates, total usage limit not exceeded.
  3. System verifies customer eligibility: user has not exceeded individual redemption limit (e.g., max 1 redemption per customer).
  4. System verifies cart meets minimum order value (e.g., cart total $\ge ₹20,000$).
  5. System calculates discount amount (e.g., 10% off up to a maximum cap of ₹2,500).
  6. System proportionally allocates discount across eligible line items and updates order summary.
* **Alternative Scenarios:**
  * Customer removes coupon: System recalculates order totals with standard active prices.
* **Exception and Failure Scenarios:**
  * Attempt to apply second coupon: System rejects with "Only one promotional coupon can be applied per order" (`BR-PRM-001`).
  * Cart value falls below threshold after quantity reduction: System automatically invalidates applied coupon and alerts customer.
* **Business Rules:** `BR-PRM-001`, `BR-PRM-002`.
* **Inputs and Business Information Required:** Coupon code, customer ID, cart line items, cart subtotal.
* **Expected Business Outcomes:** Promotional sales growth with strict financial safeguards against coupon exploitation.
* **Dependencies:** `FEAT-CART-001`, `FEAT-CHK-001`.
* **Acceptance Criteria:**
  * **Given** an order with an active coupon applied, **When** a user enters a second discount coupon, **Then** the system rejects the second code unless the user chooses to replace the existing one.
  * **Given** a coupon with a minimum order value of ₹10,000, **When** applied to a cart of ₹8,500, **Then** the coupon application is rejected with an explanatory message.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-011`.

---

## 14. Notifications & Customer Communications

### FR-NTF-001: Multi-Channel Event-Driven Notification Delivery
* **Requirement ID:** `FR-NTF-001`
* **Requirement Title:** Asynchronous Multi-Channel Transactional Notification Relay
* **Feature ID:** `FEAT-NTF-001`
* **Business Objective:** `OBJ-006`, `OBJ-007`
* **Requirement Statement:** The system must dispatch standardized, personalized transactional notifications across SMS, Email, and WhatsApp for critical business events (account verification, order confirmation, dispatch with tracking link, delivery confirmation, refund processed), tracking delivery statuses and retrying on transient provider failures.
* **Description:** Manages automated customer communication. Separates critical transactional messages from promotional marketing.
* **Primary Actor:** System (Automated), `ACT-013` (Messaging Gateway)
* **Preconditions:** Business event triggered with recipient contact information.
* **Trigger:** Domain event published (e.g., `OrderConfirmedEvent`, `ShipmentDispatchedEvent`).
* **Main Success Scenario:**
  1. System matches event to approved notification template.
  2. System injects contextual parameters (Customer Name, Order Number, AWB Tracking URL, Item Count).
  3. System determines channel routing (SMS for OTPs; Email + WhatsApp for Order Confirmation and Tracking).
  4. System dispatches message to messaging gateway API.
  5. Gateway confirms delivery; system records delivery timestamp in notification log.
* **Alternative Scenarios:**
  * WhatsApp delivery fails or user not on WhatsApp: System automatically falls back to standard SMS notification within 60 seconds.
* **Exception and Failure Scenarios:**
  * Gateway transient failure: System queues message for retry with exponential backoff (up to 3 retries over 15 minutes).
* **Business Rules:** `BR-NTF-001`.
* **Inputs and Business Information Required:** Template ID, recipient phone/email, parameter map, message priority (High/Normal).
* **Expected Business Outcomes:** Real-time customer awareness; drastic reduction in customer anxiety inquiries.
* **Dependencies:** `FEAT-INT-001`.
* **Acceptance Criteria:**
  * **Given** a confirmed order event, **When** processed, **Then** both an email containing the tax invoice PDF link and an SMS/WhatsApp message containing the tracking reference are dispatched within 10 seconds.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** See [OQ-015](file:///d:/learning/Microservice/backend/docs/00-requirements/08-assumptions-dependencies-open-questions.md#oq-015).
* **Related Workflow IDs:** `WF-012`.

---

## 15. Customer Support & Service Requests

### FR-SUP-001: Integrated Support Ticket Lifecycle Linked to Orders
* **Requirement ID:** `FR-SUP-001`
* **Requirement Title:** Customer Service Ticket Management Linked to Order History
* **Feature ID:** `FEAT-SUP-001`
* **Business Objective:** `OBJ-007`
* **Requirement Statement:** The system must provide integrated support ticketing, allowing customers to raise inquiries directly against specific orders and enabling support representatives to investigate order histories, add internal notes, and track resolution SLAs.
* **Description:** Centralizes customer dispute and inquiry handling.
* **Primary Actor:** `ACT-006` (Customer Support Agent), `ACT-009` (Customer)
* **Preconditions:** Customer has an active or historical order.
* **Trigger:** Customer submits help request from order details screen.
* **Main Success Scenario:**
  1. Customer selects order, selects issue category (e.g., "Delayed Delivery", "Missing Item", "Technical Inquiry"), and types description.
  2. System generates unique ticket number (e.g., `TKT-2026-8812`) and links it to Order ID and Customer ID.
  3. Support agent views unified dashboard displaying customer contact info, order status, courier AWB tracking status, and payment logs.
  4. Agent communicates resolution to customer via ticket interface and updates status to `RESOLVED`.
* **Alternative Scenarios:**
  * Ticket escalated: Agent reassigns ticket to Warehouse Supervisor or Finance Specialist for specialized review.
* **Exception and Failure Scenarios:**
  * Unresolved ticket exceeds SLA threshold (e.g., 24 hours): System automatically alerts Customer Support Lead.
* **Business Rules:** `BR-SUP-001`.
* **Inputs and Business Information Required:** Order ID, ticket category, customer description, internal resolution notes, agent ID.
* **Expected Business Outcomes:** Fast resolution times; complete historical record of customer communications.
* **Dependencies:** `FEAT-ORD-001`.
* **Acceptance Criteria:**
  * **Given** an open support ticket, **When** a support agent accesses the ticket, **Then** the system presents the complete live order status, payment history, and courier tracking timeline on the single ticket view.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-013`.

---

## 16. Analytics, Auditing & Executive Reporting

### FR-RPT-001: Executive Sales, Margin & Inventory Velocity Reporting
* **Requirement ID:** `FR-RPT-001`
* **Requirement Title:** Executive Commercial Performance and Inventory Velocity Reports
* **Feature ID:** `FEAT-RPT-001`
* **Business Objective:** `OBJ-009`, `OBJ-008`
* **Requirement Statement:** The system must generate operational and financial reports covering gross revenue, net revenue, average order value, category sales velocity, return rates, and stock replenishment recommendations, filterable by date range and exportable to CSV/Excel.
* **Description:** Delivers core business intelligence for executive decision-making.
* **Primary Actor:** `ACT-001` (Business Owner), `ACT-007` (Finance Officer)
* **Preconditions:** Historical transaction and inventory records exist.
* **Trigger:** Operator requests report via administrative dashboard.
* **Main Success Scenario:**
  1. User selects report type (e.g., "Sales by Brand", "Inventory Turnover", "Tax Liability Summary"), date range, and export format.
  2. System queries reporting data models and compiles metrics.
  3. System renders report visualizations and provides download of exportable spreadsheet.
* **Alternative Scenarios:**
  * Scheduled daily digest: System automatically compiles previous day's GMV, order count, and payment success rate and emails executive summary to the Business Owner at 07:00 AM daily.
* **Exception and Failure Scenarios:**
  * Large date range query: System executes reporting queries asynchronously to prevent performance degradation on live transactional operations.
* **Business Rules:** `BR-RPT-001`, `BR-AUD-001`.
* **Inputs and Business Information Required:** Report type, date range start/end, brand/category filters, export format.
* **Expected Business Outcomes:** Clear visibility into commercial margins, fast-moving vs stagnant SKUs, and tax liabilities.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-FIN-001`, `FEAT-INV-001`.
* **Acceptance Criteria:**
  * **Given** a requested sales report for a completed month, **When** generated, **Then** the reported net sales amount strictly equals the sum of confirmed payment revenues minus authorized refunds and tax components.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** See [OQ-018](file:///d:/learning/Microservice/backend/docs/00-requirements/08-assumptions-dependencies-open-questions.md#oq-018).
* **Related Workflow IDs:** `WF-014`.

### FR-AUD-001: Immutable Business Audit Trail & Sensitive Action Logging
* **Requirement ID:** `FR-AUD-001`
* **Requirement Title:** Immutable Logging of Privileged Administrative and Commercial Actions
* **Feature ID:** `FEAT-AUD-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must record an immutable, write-once audit log entry for every privileged administrative modification (catalog price updates, manual inventory count adjustments, role assignments, order cancellations, and exceptional refunds), capturing the operator identity, timestamp, IP address, previous value, new value, and mandatory reason code.
* **Description:** Protects enterprise assets against unauthorized manipulation or insider fraud.
* **Primary Actor:** `ACT-002` (System Administrator), `ACT-001` (Business Owner)
* **Preconditions:** Staff operator performs a mutation on business data.
* **Trigger:** Administrative state change or data mutation.
* **Main Success Scenario:**
  1. Operator modifies catalog selling price from ₹49,999 to ₹44,999.
  2. System prompts for mandatory change reason code (e.g., "Weekend Flash Sale Promo").
  3. System commits price change and atomically writes audit log record containing operator ID, timestamp, before/after snapshot, IP, and reason.
  4. Audit record is appended to immutable audit repository.
* **Alternative Scenarios:**
  * Querying audit history: Authorized executive reviews chronological log of all changes applied to a specific SKU or user account.
* **Exception and Failure Scenarios:**
  * Attempt to delete or edit audit record: Operation is strictly prohibited at software and database levels.
* **Business Rules:** `BR-AUD-001`.
* **Inputs and Business Information Required:** Operator ID, entity type, entity ID, action type, before snapshot, after snapshot, client IP, reason code.
* **Expected Business Outcomes:** Total traceability; forensic audit readiness for internal and external regulatory reviews.
* **Dependencies:** None.
* **Acceptance Criteria:**
  * **Given** a price change executed by an administrator, **When** the audit log is inspected, **Then** an unalterable record exists showing the exact old price, new price, user ID, timestamp, and justification.
  * **Given** any administrative role, **When** attempting to execute a `DELETE` or `UPDATE` on the audit log repository, **Then** the operation is rejected.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-016`.

---

## 17. Platform Security, Operational Resilience & Continuity

### FR-SEC-001: Sensitive Credential Protection and Payment Tokenization
* **Requirement ID:** `FR-SEC-001`
* **Requirement Title:** Zero Raw Cardholder Storage and Strong Credential Security
* **Feature ID:** `FEAT-SEC-001`
* **Business Objective:** `OBJ-008`, `OBJ-010`
* **Requirement Statement:** The system must strictly prohibit storing raw customer payment card details or CVVs within platform databases, requiring tokenized gateway integrations, and must enforce strong cryptographic hashing for all customer and operator credentials.
* **Description:** Core security and compliance requirement. Enforces zero cardholder liability and modern password hashing standards.
* **Primary Actor:** System (Automated), `ACT-002` (Administrator)
* **Preconditions:** Customer submits payment credentials during checkout.
* **Trigger:** Checkout payment step.
* **Main Success Scenario:**
  1. Customer enters card details into secure payment gateway iframe/SDK directly communicating with PCI-DSS certified gateway.
  2. Platform receives only a masked token (e.g., `tok_visa_4242`) and card metadata (brand: Visa, last 4 digits: 4242).
  3. Platform persists only the token and transaction ID.
* **Exception and Failure Scenarios:**
  * Attempt to pass raw PAN/CVV through application backend APIs: System gateway filters immediately reject request and log security alert.
* **Business Rules:** `BR-SEC-001`, `BR-SEC-002`.
* **Inputs and Business Information Required:** Gateway transaction token, masked card representation.
* **Expected Business Outcomes:** Zero PCI-DSS scope liability for core platform; complete immunity against database card breaches.
* **Dependencies:** `FEAT-PAY-001`.
* **Acceptance Criteria:**
  * **Given** any transaction executed on the platform, **When** database records are audited, **Then** zero raw primary account numbers (PAN), CVVs, or bank account PINs exist anywhere in persistent storage or application logs.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-007`.

### FR-INT-001: Integration Circuit Breaking and Provider Outage Isolation
* **Requirement ID:** `FR-INT-001`
* **Requirement Title:** External Provider Fault Isolation and Circuit Breaker Resilience
* **Feature ID:** `FEAT-INT-001`
* **Business Objective:** `OBJ-010`
* **Requirement Statement:** The system must isolate external third-party integration points (payment gateways, courier APIs, SMS gateways) using timeouts, retry queues, and circuit breakers, ensuring that a third-party outage never causes cascading failure of the core commerce platform.
* **Description:** Ensures architectural resilience against volatile external dependencies.
* **Primary Actor:** `ACT-014` (SRE / Operations), System (Automated)
* **Preconditions:** External integration calls executed.
* **Trigger:** External provider experiences elevated latency or error rates.
* **Main Success Scenario:**
  1. System calls courier API to generate shipping label with a 3-second timeout.
  2. Courier API responds successfully within 400ms; shipment is booked.
* **Alternative Scenarios:**
  * Transient timeout: System retries call using exponential backoff up to 3 times before queuing operation for background worker retry.
* **Exception and Failure Scenarios:**
  * Sustained provider outage (e.g., 50% failure rate over 20 calls): Circuit breaker trips to `OPEN`. System immediately diverts requests to secondary provider or queues requests asynchronously without hanging customer-facing threads, preventing thread pool exhaustion.
* **Business Rules:** `BR-INT-001`.
* **Inputs and Business Information Required:** Provider endpoint, timeout threshold, failure rate threshold, retry limit.
* **Expected Business Outcomes:** 99.9% platform availability regardless of external vendor downtime.
* **Dependencies:** None.
* **Acceptance Criteria:**
  * **Given** an external SMS provider experiencing complete outage, **When** customer requests an OTP, **Then** the primary failure is isolated within 2 seconds, and the request fails over to the secondary messaging provider or returns a clean error without freezing the checkout funnel.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-015`.
