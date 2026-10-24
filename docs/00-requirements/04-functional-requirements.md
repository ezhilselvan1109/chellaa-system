# Business Requirements Document (BRD)
## Document 04: Functional Requirements Specification

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-04 |
| **Document Title** | Detailed Functional Requirements Specification |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.1.0-REMEDIATED |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Specification Standards & Requirement Template

All functional requirements in this document strictly adhere to the formalized business engineering template. Each requirement describes *what* the platform must accomplish from a business perspective, avoiding premature technical solution prescriptions (such as database locking mechanisms, REST endpoints, or message broker topics).

Every functional requirement specifies:
* **Requirement Identifiers & Metadata:** Stable IDs (`FR-xxx-xxx`), linked Feature IDs (`FEAT-xxx`), Business Objectives (`OBJ-xxx`), and Priority (`P0` to `P3`).
* **Actors & Lifecycle Triggers:** Primary actors, operational preconditions, and triggering events.
* **Scenarios:** Main success scenarios, alternative flows, and comprehensive exception/failure paths.
* **Governing Rules & Information:** Business rules enforced, required inputs, and expected business outcomes.
* **Acceptance Criteria:** Formal Given/When/Then criteria establishing definitive verification boundaries.
* **Uncertainty & Approval Tracking:** Explicit links to open questions (`OQ-xxx`) and operational workflows (`WF-xxx`).

---

## 2. Business Administration & Access Control

### <a id="fr-adm-001"></a>FR-ADM-001: Administrative User Account & Role Assignment
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

### <a id="fr-adm-002"></a>FR-ADM-002: Administrative Session Invalidation & Credential Rotation
* **Requirement ID:** `FR-ADM-002`
* **Requirement Title:** Immediate Administrative Session Invalidation & Mandatory Credential Expiry
* **Feature ID:** `FEAT-ADM-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must immediately revoke all active administrative sessions upon account role modification, status suspension, or password rotation, and enforce a 90-day mandatory password expiry for internal staff accounts.
* **Description:** Prevents stale session hijacking and unauthorized privilege persistence following operator departure or role change.
* **Primary Actor:** `ACT-002` (System Administrator), System (Automated)
* **Preconditions:** Administrative operator has active session or expired credentials.
* **Trigger:** Administrator modifies staff permissions, deactivates account, or credential reaches 90-day age limit.
* **Main Success Scenario:**
  1. Administrator updates role permissions or deactivates an operator account.
  2. System terminates all active refresh tokens and session identifiers for that user across all devices.
  3. Subsequent API calls using existing session tokens receive an immediate HTTP 401 Unauthorized response.
  4. System records the session termination event in the immutable audit log.
* **Alternative Scenarios:**
  * 90-Day Credential Expiry: System flags account as `PASSWORD_EXPIRED` during login and forces customer/operator through password rotation workflow before granting access.
* **Exception and Failure Scenarios:**
  * Revocation dispatch failure: If distributed token invalidation fails, platform logs a critical security alert and blocks user at the API gateway boundary.
* **Business Rules:** `BR-ADM-002`, `BR-SEC-002`.
* **Inputs and Business Information Required:** Operator ID, triggering action (role change, suspension, timeout), timestamp.
* **Expected Business Outcomes:** Immediate containment of compromised or revoked administrative credentials.
* **Dependencies:** `FEAT-ADM-001`, `FEAT-AUD-001`.
* **Acceptance Criteria:**
  * **Given** an operator with an active administrative session, **When** their account status is changed to `DEACTIVATED`, **Then** within 1 second all subsequent API requests from that session are rejected.
  * **Given** a staff account whose password was last rotated 91 days ago, **When** the operator attempts to authenticate, **Then** the system denies login and redirects to the mandatory password reset flow.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-016`.

---

## 3. Customer Account & Profile Management

### <a id="fr-cust-001"></a>FR-CUST-001: Customer Registration & Phone Verification
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
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-010](./08-assumptions-dependencies-open-questions.md#oq-010).
* **Related Workflow IDs:** `WF-001`.

### <a id="fr-cust-002"></a>FR-CUST-002: Customer Address Book Management
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

### <a id="fr-cust-003"></a>FR-CUST-003: Customer Profile & Communication Preference Management
* **Requirement ID:** `FR-CUST-003`
* **Requirement Title:** Customer Profile Information and Communication Channels Governance
* **Feature ID:** `FEAT-CUST-001`
* **Business Objective:** `OBJ-001`, `OBJ-006`
* **Requirement Statement:** The system must allow registered customers to update their personal profile information (name, secondary email, alternate phone) and explicitly configure their communication channel preferences (SMS, Email, WhatsApp) for marketing and operational notifications.
* **Description:** Enables self-service profile maintenance and ensures compliance with consumer consent regulations regarding notification channels.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer is authenticated.
* **Trigger:** Customer navigates to profile settings and submits changes.
* **Main Success Scenario:**
  1. Customer edits profile fields or toggles notification channel preferences.
  2. System validates contact information formats and ensures updated email/phone is not in use by another active account.
  3. System records the consent timestamp and persists the updated profile.
  4. System returns success confirmation to the customer.
* **Alternative Scenarios:**
  * Customer updates primary mobile phone: System triggers OTP verification to the new number before applying the change.
* **Exception and Failure Scenarios:**
  * Duplicate email/phone: System denies update and notifies customer that the contact is already registered.
* **Business Rules:** `BR-CUST-001`, `BR-NTF-001`.
* **Inputs and Business Information Required:** Full name, email address, mobile number, notification preference flags (SMS, WhatsApp, Email).
* **Expected Business Outcomes:** Accurate customer master data and audited consent records.
* **Dependencies:** `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** an authenticated customer, **When** they disable WhatsApp promotional alerts in their profile, **Then** the notification engine suppresses promotional dispatches to that channel while maintaining transactional order alerts.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-001`.

### <a id="fr-cust-004"></a>FR-CUST-004: Customer Privacy, Data Export & Account Anonymization
* **Requirement ID:** `FR-CUST-004`
* **Requirement Title:** Customer Self-Service Data Export and Right-to-be-Forgotten Anonymization
* **Feature ID:** `FEAT-CUST-003`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must allow registered customers to download a complete machine-readable archive of their personal data and request account anonymization, while retaining necessary tax invoice records for statutory audit compliance.
* **Description:** Provides self-service privacy tools supporting compliance with data protection laws (DPDPA 2023) and customer data sovereignty.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer is authenticated and has no active orders in transit or pending returns.
* **Trigger:** Customer submits a data export or account erasure request from the privacy portal.
* **Main Success Scenario:**
  1. Customer requests data export: System compiles profile, addresses, orders, and reviews into a secure JSON/ZIP archive and delivers a time-limited download link.
  2. Customer requests account erasure: System verifies zero active transactions, prompts for re-authentication, and marks account status as `ANONYMIZED`.
  3. System scrubs PII (name, phone, email, saved addresses) while preserving historical financial ledger entries with an anonymized customer reference.
* **Alternative Scenarios:**
  * Customer has active orders: System rejects erasure request and informs customer that open orders must reach terminal status (`DELIVERED` or `CANCELLED`) before erasure.
* **Exception and Failure Scenarios:**
  * Re-authentication failure: System blocks privacy actions if identity cannot be cryptographically re-verified.
* **Business Rules:** `BR-DAT-001`, `BR-SEC-002`.
* **Inputs and Business Information Required:** Customer ID, password re-entry / OTP verification, confirmation reason code.
* **Expected Business Outcomes:** Full statutory compliance with personal data privacy mandates without violating financial record retention laws.
* **Dependencies:** `FEAT-FIN-002`, `FEAT-SEC-001`.
* **Acceptance Criteria:**
  * **Given** a customer with no active orders, **When** they confirm account anonymization, **Then** all PII fields in the customer record are scrambled within 24 hours, while statutory tax invoices retain the legally required audit snapshot.
* **Priority:** `P1`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-020](./08-assumptions-dependencies-open-questions.md#oq-020).
* **Related Workflow IDs:** `WF-001`.

---

## 4. Product Catalog, Categories & Electronics Merchandising

### <a id="fr-cat-001"></a>FR-CAT-001: Multi-Variant Electronics Product Creation & Governance
* **Requirement ID:** `FR-CAT-001`
* **Requirement Title:** Manage Multi-Variant Electronics Catalog Listings and SKU Attributes
* **Feature ID:** `FEAT-CAT-001`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must allow Catalog Managers to create, update, and manage electronics products with multi-dimensional variants (e.g., storage capacity, RAM, colorway), assigning each variant a globally unique Stock Keeping Unit (SKU) and manufacturer barcode.
* **Description:** Models complex electronics products with distinct physical variants, ensuring accurate pricing, inventory, and specification binding.
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Brand, category, and tax codes are pre-configured.
* **Trigger:** Catalog Manager creates or updates a product listing in the admin portal.
* **Main Success Scenario:**
  1. Catalog Manager creates parent product (title, brand, category, description).
  2. Manager defines variant dimensions (e.g., Color: Titanium Gray, Storage: 256GB).
  3. System generates distinct variant records requiring unique SKU, Barcode (EAN/UPC), MRP, Selling Price, and dimensions.
  4. System validates that Selling Price does not exceed MRP.
  5. Manager publishes product; system indexes listing for search and browsing.
* **Alternative Scenarios:**
  * Deactivating a variant: System marks variant inactive, preventing new checkout reservations while preserving historical order linkages.
* **Exception and Failure Scenarios:**
  * Duplicate SKU or Barcode: System rejects submission with a conflict error.
  * Selling Price > MRP: System strictly blocks saving with a statutory pricing error.
* **Business Rules:** `BR-CAT-001`, `BR-CAT-002`, `BR-PRC-001`.
* **Inputs and Business Information Required:** Title, brand ID, category ID, variant attributes (color, storage), unique SKU, EAN barcode, MRP, Selling Price, weight, package dimensions.
* **Expected Business Outcomes:** 100% SKU uniqueness and structured electronics variant presentation.
* **Dependencies:** `FEAT-BRD-001`, `FEAT-CTG-001`, `FEAT-FIN-001`.
* **Acceptance Criteria:**
  * **Given** a parent electronics product, **When** the Catalog Manager adds a variant with a pre-existing SKU, **Then** the system rejects creation with an "SKU Already Exists" error.
  * **Given** a product variant, **When** the Selling Price is entered as ₹75,000 and MRP is entered as ₹70,000, **Then** the system blocks submission with a validation error.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

### <a id="fr-cat-002"></a>FR-CAT-002: Category Technical Specification Schema Association
* **Requirement ID:** `FR-CAT-002`
* **Requirement Title:** Mandatory Category Specification Schema Validation on Product Publish
* **Feature ID:** `FEAT-CTG-002`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must enforce category-specific technical specification schemas (e.g., Screen Size, Battery Capacity, Processor, 5G Support), mandating that all required category attributes are populated before an electronics product can be published to the live storefront.
* **Description:** Guarantees technical specification completeness for consumer electronics, enabling accurate product comparison and filtering.
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Category specification schema is active.
* **Trigger:** Catalog Manager attempts to transition product status from `DRAFT` to `ACTIVE`.
* **Main Success Scenario:**
  1. Catalog Manager populates specification attribute fields defined by the category schema.
  2. System validates input data types, unit measurements (e.g., mAh, Inches, GB), and mandatory field presence.
  3. System approves schema validation and activates the product on the storefront.
* **Alternative Scenarios:**
  * Product saved as Draft: System allows saving incomplete specifications in draft state.
* **Exception and Failure Scenarios:**
  * Missing mandatory attribute: System blocks publishing and highlights missing specifications.
* **Business Rules:** `BR-CAT-002`, `BR-CTG-002`.
* **Inputs and Business Information Required:** Attribute key-value pairs conforming to category schema definition.
* **Expected Business Outcomes:** Rich, standardized technical specifications across all electronics catalog listings.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-CTG-002`.
* **Acceptance Criteria:**
  * **Given** a smartphone listing in `DRAFT`, **When** the manager attempts to publish without populating "Battery Capacity" (defined as mandatory in Smartphones schema), **Then** publishing is denied.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

### <a id="fr-cat-003"></a>FR-CAT-003: Product Digital Asset & Media Management
* **Requirement ID:** `FR-CAT-003`
* **Requirement Title:** High-Resolution Product Media Upload, Ordering & Primary Image Designation
* **Feature ID:** `FEAT-CAT-002`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must allow Catalog Managers to upload, reorder, delete, and designate primary thumbnail images and variant-specific gallery assets for all catalog items, validating image format, aspect ratio, and maximum file size.
* **Description:** Powers visual merchandising across product listing and detail pages with optimized, high-fidelity media assets.
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Product or variant record exists.
* **Trigger:** Catalog Manager uploads image assets to a product listing.
* **Main Success Scenario:**
  1. Catalog Manager uploads images (JPEG, PNG, WebP) up to 5MB each.
  2. System validates format and dimensions, generates responsive image sizes, and stores CDN references.
  3. Manager designates one asset as "Primary Thumbnail" and arranges sequence order.
  4. System persists media linkages and updates storefront asset delivery cache.
* **Alternative Scenarios:**
  * Manager assigns image to a specific color variant: Storefront dynamically displays that asset when the customer selects that variant.
* **Exception and Failure Scenarios:**
  * Corrupted file or invalid format (e.g., executable or PDF): System rejects upload immediately.
* **Business Rules:** `BR-CAT-001`, `BR-CAT-002`.
* **Inputs and Business Information Required:** Image files, display sequence index, variant assignment ID, primary thumbnail flag.
* **Expected Business Outcomes:** Fast-loading, high-resolution product imagery enhancing customer conversion.
* **Dependencies:** `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** a product with 5 images, **When** the manager reorders image 3 to position 1, **Then** image 3 becomes the primary catalog thumbnail on search and PDP views.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

### <a id="fr-brd-001"></a>FR-BRD-001: Brand Identity & Manufacturer Governance
* **Requirement ID:** `FR-BRD-001`
* **Requirement Title:** Electronics Brand Lifecycle Management and Deletion Protection
* **Feature ID:** `FEAT-BRD-001`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must allow Catalog Managers to maintain verified brand identities (e.g., Apple, Samsung, Sony) with official logos and warranty terms, strictly prohibiting deletion of any brand linked to active catalog products.
* **Description:** Maintains authoritative brand registry and protects relational catalog integrity.
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Operator is authenticated with catalog management permissions.
* **Trigger:** Catalog Manager creates, edits, or attempts deletion of a brand.
* **Main Success Scenario:**
  1. Catalog Manager submits brand details (name, official logo URL, support contact, standard warranty summary).
  2. System validates uniqueness of brand name.
  3. System creates brand record and makes it selectable during product creation.
* **Alternative Scenarios:**
  * Deactivating brand: Manager sets brand status to `INACTIVE`, hiding all associated products from storefront search while preserving historical records.
* **Exception and Failure Scenarios:**
  * Attempting to delete a brand linked to existing SKUs: System rejects operation with a referential integrity violation error.
* **Business Rules:** `BR-BRD-001`.
* **Inputs and Business Information Required:** Brand name, brand slug, logo asset, manufacturer support URL, default warranty terms.
* **Expected Business Outcomes:** Clean, verified brand taxonomy preventing orphaned catalog records.
* **Dependencies:** `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** a brand associated with 12 active products, **When** a manager attempts hard deletion, **Then** the system rejects the deletion with an explicit dependency warning.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

### <a id="fr-ctg-001"></a>FR-CTG-001: Hierarchical Category Taxonomy Management
* **Requirement ID:** `FR-CTG-001`
* **Requirement Title:** Multi-Level Category Tree Governance with Circular Reference Prevention
* **Feature ID:** `FEAT-CTG-001`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must support an arbitrary-depth hierarchical category taxonomy (e.g., Electronics > Audio > Wireless Earbuds) allowing managers to organize products, enforcing strict prevention of circular parent-child category loops.
* **Description:** Establishes the browsing taxonomy for storefront navigation and catalog categorization.
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Operator has catalog management permissions.
* **Trigger:** Catalog Manager creates or restructures category nodes.
* **Main Success Scenario:**
  1. Catalog Manager creates category node with title, slug, and optional parent category ID.
  2. System validates that the proposed parent relationship does not introduce a cyclic reference.
  3. System updates category tree index and publishes navigation breadcrumbs.
* **Alternative Scenarios:**
  * Moving a category branch: System moves category and all child nodes under a new parent node atomically.
* **Exception and Failure Scenarios:**
  * Circular dependency: If Category A is parent to Category B, attempting to assign Category B as parent to Category A is strictly rejected.
* **Business Rules:** `BR-CTG-001`.
* **Inputs and Business Information Required:** Category name, slug, parent category ID, display order, banner image.
* **Expected Business Outcomes:** Intuitive catalog navigation hierarchy with zero infinite loops.
* **Dependencies:** None.
* **Acceptance Criteria:**
  * **Given** existing Category A -> Category B, **When** an operator attempts to set Category A's parent as Category B, **Then** the system blocks the update with a cyclic hierarchy error.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

### <a id="fr-ctg-002"></a>FR-CTG-002: Dynamic Category Specification & Attribute Schemas
* **Requirement ID:** `FR-CTG-002`
* **Requirement Title:** Define Dynamic Technical Attribute Schemas per Category Node
* **Feature ID:** `FEAT-CTG-002`
* **Business Objective:** `OBJ-002`
* **Requirement Statement:** The system must allow Catalog Managers to define dynamic attribute schemas per category (attribute name, data type, unit of measure, mandatory flag, filterable flag), which automatically bind to all products mapped to that category.
* **Description:** Enables tailored specification templates for diverse electronics categories (e.g., Laptops require RAM/CPU; Televisions require Display Panel/Refresh Rate).
* **Primary Actor:** `ACT-003` (Catalog Manager)
* **Preconditions:** Category node exists.
* **Trigger:** Catalog Manager configures attribute definitions for a category.
* **Main Success Scenario:**
  1. Catalog Manager defines attributes for category (e.g., "Screen Refresh Rate", Type: Number, Unit: Hz, Filterable: True).
  2. System saves schema definition and associates it with the category ID.
  3. Products mapped to this category inherit these attributes in their specification editor.
* **Alternative Scenarios:**
  * Subcategory inheritance: Child categories automatically inherit specification schemas defined on parent categories unless overridden.
* **Exception and Failure Scenarios:**
  * Deleting an attribute actively used by live products: System requires migration or confirmation before schema field removal.
* **Business Rules:** `BR-CTG-002`.
* **Inputs and Business Information Required:** Attribute name, code, data type (String, Number, Boolean, Enum), allowed values, unit of measure, mandatory flag.
* **Expected Business Outcomes:** Highly structured, filterable electronics specifications.
* **Dependencies:** `FEAT-CTG-001`.
* **Acceptance Criteria:**
  * **Given** a schema attribute marked as `Filterable = True`, **When** products with this attribute are published, **Then** the search service exposes this attribute as a faceted filter on catalog listing pages.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`.

---

## 5. Inventory & Multi-Warehouse Operations

### <a id="fr-inv-001"></a>FR-INV-001: Prevent Ordering Unavailable Inventory (Oversell Prevention)
* **Requirement ID:** `FR-INV-001`
* **Requirement Title:** Enforce Zero Overselling via Available-to-Sell Inventory Ledger
* **Feature ID:** `FEAT-INV-001`
* **Business Objective:** `OBJ-003`
* **Requirement Statement:** The system must strictly prevent checkout and order placement for any item where the requested quantity exceeds the current Available-to-Sell (ATS) stock level (`ATS = On_Hand - Reserved - Safety_Stock`).
* **Description:** Protects the enterprise against overselling consumer electronics during flash sales and peak concurrent shopping bursts.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Product variant exists with inventory ledger record.
* **Trigger:** Customer initiates checkout or adds item to cart.
* **Main Success Scenario:**
  1. System checks ATS for requested SKU.
  2. ATS is greater than or equal to requested quantity.
  3. System permits proceeding to checkout.
* **Alternative Scenarios:**
  * Partial availability: ATS is 2, customer requests 5; system restricts addition to max available (2) and alerts customer.
* **Exception and Failure Scenarios:**
  * ATS is 0: System marks item "Out of Stock" on storefront, disables buy buttons, and blocks checkout attempt.
* **Business Rules:** `BR-INV-001`, `BR-INV-002`, `BR-INV-004`.
* **Inputs and Business Information Required:** SKU, requested quantity, warehouse ID.
* **Expected Business Outcomes:** Zero unfulfillable orders and zero customer dissatisfaction from stock cancellations.
* **Dependencies:** `FEAT-INV-002`.
* **Acceptance Criteria:**
  * **Given** a product SKU with `On_Hand = 10`, `Reserved = 8`, `Safety_Stock = 2`, **When** a customer attempts to purchase 1 unit, **Then** checkout is rejected because `ATS = 0`.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-003`, `WF-006`.

### <a id="fr-inv-002"></a>FR-INV-002: Real-Time Stock Reservation & Automated Expiration
* **Requirement ID:** `FR-INV-002`
* **Requirement Title:** Atomic Checkout Stock Reservation with 15-Minute Expiration Hold
* **Feature ID:** `FEAT-INV-002`
* **Business Objective:** `OBJ-003`
* **Requirement Statement:** The system must atomically reserve inventory upon customer checkout submission for a duration of exactly 15 minutes, automatically releasing reserved stock back to Available-to-Sell if payment confirmation is not received within the TTL window.
* **Description:** Guarantees inventory availability while a customer completes payment gateway interactions, while preventing indefinite inventory lockup from abandoned carts.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Customer proceeds to payment step of checkout with sufficient ATS.
* **Trigger:** Customer clicks "Proceed to Payment".
* **Main Success Scenario:**
  1. System executes atomic decrement of ATS and increment of Reserved count.
  2. System assigns a Hold ID with an exact 15-minute expiration timestamp.
  3. Customer completes payment within 15 minutes.
  4. System permanently commits the reservation into deducted on-hand stock upon payment capture.
* **Alternative Scenarios:**
  * Customer cancels payment session: System immediately cancels Hold ID and releases stock back to ATS without waiting for 15-minute TTL.
* **Exception and Failure Scenarios:**
  * Payment times out: System reaper releases hold after 15 minutes; order transitions to `PAYMENT_TIMEOUT`.
* **Business Rules:** `BR-INV-003`, `BR-PAY-002`.
* **Inputs and Business Information Required:** Order ID, SKU, reserved quantity, timestamp.
* **Expected Business Outcomes:** Frictionless checkout with zero double-selling and immediate stock recovery on abandonment.
* **Dependencies:** `FEAT-CHK-001`, `FEAT-PAY-001`.
* **Acceptance Criteria:**
  * **Given** an active stock reservation created at 10:00:00 AM, **When** no payment confirmation arrives by 10:15:01 AM, **Then** the reservation is expired and the reserved quantity is returned to Available-to-Sell stock.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`, `WF-007`.

### <a id="fr-inv-003"></a>FR-INV-003: Stock Reservation Expiration & Automatic Inventory Release
* **Requirement ID:** `FR-INV-003`
* **Requirement Title:** Automated Reservation Expiration Reaper and Inventory Replenishment Release
* **Feature ID:** `FEAT-INV-002`
* **Business Objective:** `OBJ-003`
* **Requirement Statement:** The system must run an automated background reconciliation process that sweeps expired inventory holds every $le 60	ext{ seconds}$, releasing reserved units back to Available-to-Sell and notifying downstream search indexers of renewed availability.
* **Description:** Provides continuous automated reclamation of inventory held by abandoned checkouts or dropped payment sessions.
* **Primary Actor:** System (Automated Background Scheduler)
* **Preconditions:** Inventory holds exist in the persistence layer.
* **Trigger:** Scheduled interval timer fires (every 60 seconds).
* **Main Success Scenario:**
  1. System queries for all active holds where `expires_at < current_timestamp`.
  2. System atomically increments Available-to-Sell stock by held quantity and decrements Reserved count.
  3. System marks Hold status as `EXPIRED`.
  4. System triggers catalog cache and search index update for newly available SKUs.
* **Alternative Scenarios:**
  * Zero expired holds found: Process logs heartbeat and exits cleanly.
* **Exception and Failure Scenarios:**
  * Database lock conflict during release: System retries release with exponential backoff and alerts if release fails after 3 attempts.
* **Business Rules:** `BR-INV-003`, `BR-PAY-002`.
* **Inputs and Business Information Required:** Current timestamp, Hold ID, SKU, quantity.
* **Expected Business Outcomes:** High inventory velocity and immediate restocking of unpaid items.
* **Dependencies:** `FEAT-INV-002`, `FEAT-SRCH-001`.
* **Acceptance Criteria:**
  * **Given** a reservation that expired at 14:00:00, **When** the reaper runs at 14:00:30, **Then** the SKU's ATS is incremented by the reserved quantity and the hold is flagged `EXPIRED`.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`, `WF-007`.

### <a id="fr-inv-004"></a>FR-INV-004: Low-Stock Threshold Alerting & Replenishment Triggers
* **Requirement ID:** `FR-INV-004`
* **Requirement Title:** Automated Low-Stock Exception Alerts and Warehouse Replenishment Prompts
* **Feature ID:** `FEAT-INV-003`
* **Business Objective:** `OBJ-003`
* **Requirement Statement:** The system must evaluate SKU Available-to-Sell stock levels after every order decrement, automatically triggering low-stock notification alerts to inventory managers when stock drops below the SKU-configured safety threshold.
* **Description:** Proactively warns warehouse operations of impending stockouts to prompt timely reorders and distributor replenishment.
* **Primary Actor:** System (Automated), `ACT-004` (Inventory / Warehouse Operator)
* **Preconditions:** SKU has configured low-stock threshold value.
* **Trigger:** Order confirmation decrements inventory below threshold.
* **Main Success Scenario:**
  1. System commits order deduction.
  2. System checks: `ATS <= Low_Stock_Threshold`.
  3. Condition is true; system flags SKU status as `LOW_STOCK`.
  4. System dispatches operational alert to inventory management console and email digest.
* **Alternative Scenarios:**
  * SKU reaches 0: System flags SKU as `OUT_OF_STOCK` and triggers high-severity stockout alert.
* **Exception and Failure Scenarios:**
  * Notification dispatch failure: System retains alert in admin dashboard notification tray.
* **Business Rules:** `BR-INV-004`.
* **Inputs and Business Information Required:** SKU, current ATS, configured threshold, warehouse ID.
* **Expected Business Outcomes:** Reduced out-of-stock downtime through proactive purchasing.
* **Dependencies:** `FEAT-INV-001`, `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** a SKU with threshold = 5 and current ATS = 6, **When** an order for 2 units is confirmed (new ATS = 4), **Then** a low-stock alert is generated within 10 seconds.
* **Priority:** `P1`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-003`.

---

## 6. Pricing, Tax & Financial Invoicing

### <a id="fr-prc-001"></a>FR-PRC-001: Price Validation & Cart Revalidation
* **Requirement ID:** `FR-PRC-001`
* **Requirement Title:** Enforce Selling Price Ceiling and Real-Time Cart Price Revalidation
* **Feature ID:** `FEAT-PRC-001`
* **Business Objective:** `OBJ-001`, `OBJ-008`
* **Requirement Statement:** The system must strictly enforce that product Selling Price never exceeds Maximum Retail Price (MRP), and must revalidate cart item prices against active catalog prices during checkout, blocking completion and alerting the customer if price discrepancies occur.
* **Description:** Ensures statutory consumer pricing compliance and prevents customers from purchasing items at stale cached prices.
* **Primary Actor:** System (Automated), `ACT-003` (Catalog Manager), `ACT-009` (Customer)
* **Preconditions:** Customer initiates checkout or manager edits catalog price.
* **Trigger:** Price update in admin, or customer submits checkout step.
* **Main Success Scenario:**
  1. System verifies cart unit prices match live catalog prices.
  2. All prices match; system proceeds with checkout calculation.
* **Alternative Scenarios:**
  * Price dropped: Live price is lower than cart price; system automatically updates cart to lower price and informs customer of additional savings.
* **Exception and Failure Scenarios:**
  * Price increased: Live price is higher than cart price; system halts checkout, updates cart price, and prompts customer to review and accept the updated total before proceeding.
  * Selling Price > MRP submitted in admin: System rejects update immediately.
* **Business Rules:** `BR-PRC-001`, `BR-PRC-002`, `BR-CHK-002`.
* **Inputs and Business Information Required:** SKU, cart unit price, live catalog Selling Price, MRP.
* **Expected Business Outcomes:** Strict legal pricing compliance and zero revenue leakage from stale carts.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-CHK-001`.
* **Acceptance Criteria:**
  * **Given** an item in a cart at ₹1,000 whose catalog price was changed to ₹1,200, **When** the customer clicks "Proceed to Checkout", **Then** the system blocks payment, updates the cart to ₹1,200, and displays a prominent price change notice.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-005`, `WF-006`.

### <a id="fr-fin-001"></a>FR-FIN-001: Statutory GST Tax Calculation & Price Breakdown
* **Requirement ID:** `FR-FIN-001`
* **Requirement Title:** Indian GST Statutory Tax Calculation and Regional Component Breakdown
* **Feature ID:** `FEAT-FIN-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must calculate statutory Indian Goods and Services Tax (GST) for every order line item based on destination shipping address and enterprise dispatch location, correctly breaking down taxes into intra-state (CGST + SGST) or inter-state (IGST) components.
* **Description:** Powers statutory financial compliance for domestic commerce, computing tax liabilities according to legal HSN rate schedules.
* **Primary Actor:** System (Automated Tax Calculation Engine)
* **Preconditions:** Enterprise warehouse state and customer delivery address state are resolved.
* **Trigger:** Checkout calculation or cart summary calculation requested.
* **Main Success Scenario:**
  1. System identifies shipping destination state and enterprise origin state.
  2. If Origin State == Destination State: System calculates CGST (50% of GST rate) and SGST (50% of GST rate).
  3. If Origin State != Destination State: System calculates IGST (100% of GST rate).
  4. System extracts base taxable value and tax amounts from tax-inclusive retail display prices:
     $$\text{Base Price} = \frac{\text{Gross Price}}{1 + \text{GST Rate}}$$
  5. System records exact tax component breakdowns in order summary.
* **Alternative Scenarios:**
  * Zero-rated or exempted accessory items: System applies 0% GST rate.
* **Exception and Failure Scenarios:**
  * Unresolved state mapping from PIN code: System halts checkout and prompts customer to verify address details.
* **Business Rules:** `BR-FIN-001`.
* **Inputs and Business Information Required:** SKU HSN code, GST rate percentage, origin state code, destination state code, gross item price.
* **Expected Business Outcomes:** 100% statutory tax audit compliance with accurate GST ledger postings.
* **Dependencies:** `FEAT-CUST-002`, `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** an intra-state order (Tamil Nadu to Tamil Nadu) with an 18% GST electronics item priced at ₹11,800 gross, **When** tax calculation runs, **Then** Base Value is ₹10,000, CGST is ₹900 (9%), SGST is ₹900 (9%), and IGST is ₹0.
  * **Given** an inter-state order (Tamil Nadu to Karnataka) with the same item, **When** tax calculation runs, **Then** Base Value is ₹10,000, IGST is ₹1,800 (18%), and CGST/SGST are ₹0.
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-002](./08-assumptions-dependencies-open-questions.md#oq-002), [OQ-009](./08-assumptions-dependencies-open-questions.md#oq-009).
* **Related Workflow IDs:** `WF-006`, `WF-007`.

### <a id="fr-fin-002"></a>FR-FIN-002: Sequential Tax Invoice Generation in PDF
* **Requirement ID:** `FR-FIN-002`
* **Requirement Title:** Monotonic Sequential Tax Invoice Generation and Tamper-Resistant PDF Rendering
* **Feature ID:** `FEAT-FIN-002`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must generate an official, immutable tax invoice with a strictly contiguous, monotonically increasing invoice numbering sequence immediately upon order payment confirmation, rendering a downloadable PDF complying with Indian GST invoicing standards.
* **Description:** Produces legal commercial tax invoices for customer purchase proof and statutory enterprise tax filings.
* **Primary Actor:** System (Automated Financial Invoicing Service)
* **Preconditions:** Order status transitions to `CONFIRMED` with verified payment capture.
* **Trigger:** Order confirmation event published.
* **Main Success Scenario:**
  1. System allocates next consecutive invoice number from enterprise financial sequence (e.g., `CHEL-2026-000491`).
  2. System compiles enterprise GSTIN, buyer billing address, line items, HSN codes, tax breakdown, and payment transaction reference.
  3. System renders permanent PDF invoice document and stores immutable copy in secure object storage.
  4. System attaches download link to customer order confirmation and archives record for statutory 8-year retention.
* **Alternative Scenarios:**
  * Customer re-downloads invoice from order history: System serves exact immutable pre-generated PDF.
* **Exception and Failure Scenarios:**
  * Sequence gap detection: If sequence allocation encounters database contention, transaction retries atomically to guarantee zero numbering gaps.
* **Business Rules:** `BR-FIN-002`.
* **Inputs and Business Information Required:** Order ID, payment reference ID, enterprise GSTIN, customer name/address, line item breakdown, HSN codes.
* **Expected Business Outcomes:** 100% compliant statutory invoicing with zero gap penalties during tax audits.
* **Dependencies:** `FEAT-PAY-001`, `FEAT-ORD-001`, `FEAT-FIN-001`.
* **Acceptance Criteria:**
  * **Given** consecutive confirmed orders, **When** invoices are generated, **Then** invoice numbers must be strictly sequential with zero duplicates or missing gaps.
  * **Given** an issued invoice, **When** an administrator or customer views the invoice at any future date, **Then** the financial figures and invoice content are identical to the original snapshot.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-007`.

### <a id="fr-fin-003"></a>FR-FIN-003: Credit Note Generation for Returns & Order Cancellations
* **Requirement ID:** `FR-FIN-003`
* **Requirement Title:** Statutory Credit Note Generation for Returned Items and Pre-Dispatch Cancellations
* **Feature ID:** `FEAT-FIN-002`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must automatically generate a formal GST-compliant Credit Note linked to the original Tax Invoice whenever an order cancellation or return refund is authorized, adjusting enterprise tax liabilities accordingly.
* **Description:** Maintains statutory accounting compliance for reverse transactions, ensuring tax liabilities are legally reduced when money is returned to consumers.
* **Primary Actor:** System (Automated), `ACT-007` (Finance Specialist)
* **Preconditions:** Order cancellation or return inspection is approved.
* **Trigger:** Refund authorization event.
* **Main Success Scenario:**
  1. System identifies original Tax Invoice number.
  2. System allocates next sequential Credit Note number (e.g., `CN-2026-000104`).
  3. System computes exact refunded taxable base and GST reversal amounts.
  4. System renders Credit Note PDF, links it to customer account, and archives record for tax filing.
* **Alternative Scenarios:**
  * Partial return: Credit Note reflects only the returned line items and their proportional taxes.
* **Exception and Failure Scenarios:**
  * Missing original invoice reference: Credit Note generation is blocked until historical invoice ID is verified.
* **Business Rules:** `BR-FIN-003`, `BR-RET-003`.
* **Inputs and Business Information Required:** Original Invoice ID, Refund ID, returned item list, refunded amounts, reason code.
* **Expected Business Outcomes:** Accurate tax reversal filings and transparent customer accounting.
* **Dependencies:** `FEAT-RET-002`, `FEAT-FIN-002`.
* **Acceptance Criteria:**
  * **Given** an approved customer return for a ₹10,000 item, **When** refund is executed, **Then** a sequential Credit Note is issued referencing the original Tax Invoice and detailing the exact GST reversal.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-009`, `WF-010`.

---

## 7. Product Search & Discovery

### <a id="fr-srch-001"></a>FR-SRCH-001: Full-Text Search and Faceted Filtering
* **Requirement ID:** `FR-SRCH-001`
* **Requirement Title:** Typo-Tolerant Product Search and Multi-Faceted Attribute Filtering
* **Feature ID:** `FEAT-SRCH-001`
* **Business Objective:** `OBJ-001`
* **Requirement Statement:** The system must provide typo-tolerant full-text search across product titles, descriptions, brands, and categories, and support multi-faceted filtering (price range, brand, color, specifications) returning results in $\le 200\text{ ms}$.
* **Description:** Drives customer product discovery through fast, responsive search and faceted navigation.
* **Primary Actor:** `ACT-009` (Customer), `ACT-010` (Guest Shopper)
* **Preconditions:** Products are indexed in the search engine.
* **Trigger:** Shopper enters keywords in search bar or applies faceted filters.
* **Main Success Scenario:**
  1. Shopper inputs query (e.g., "samsng oled").
  2. Search engine applies fuzzy phonetic matching and resolves typos to "Samsung OLED".
  3. System returns matching active products with relevant faceted filter counts.
  4. Shopper filters by "RAM: 16GB" and "Price: Under ₹80,000"; system refines results instantly.
* **Alternative Scenarios:**
  * Zero exact matches: System returns suggested related products based on category keywords.
* **Exception and Failure Scenarios:**
  * Search engine cluster timeout: System gracefully falls back to direct database query with basic keyword matching.
* **Business Rules:** `BR-SRCH-001`, `BR-SRCH-002`.
* **Inputs and Business Information Required:** Search query string, selected filter facets, sort parameter (price asc/desc, popularity, newest).
* **Expected Business Outcomes:** Rapid product findability maximizing conversion rates.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-CTG-002`.
* **Acceptance Criteria:**
  * **Given** a customer search for "iphoen", **When** search executes, **Then** Apple iPhone listings are returned as top results.
  * **Given** a search query, **When** results are returned, **Then** response latency is $\le 200\text{ ms}$ for 95% of queries under standard load.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-004`.

### <a id="fr-srch-002"></a>FR-SRCH-002: Search Index Synchronization & Faceted Attribute Filtering
* **Requirement ID:** `FR-SRCH-002`
* **Requirement Title:** Near-Real-Time Search Index Updates upon Catalog and Stock Mutations
* **Feature ID:** `FEAT-SRCH-001`
* **Business Objective:** `OBJ-001`, `OBJ-003`
* **Requirement Statement:** The system must synchronize catalog modifications (price changes, description edits, stock exhaustion, new variant publishing) to the search index within $\le 5\text{ seconds}$ during standard operations, ensuring shoppers do not encounter stale prices or out-of-stock listings.
* **Description:** Maintains high consistency between transactional catalog/inventory databases and the read-optimized search index.
* **Primary Actor:** System (Automated Indexing Pipeline)
* **Preconditions:** Catalog mutation committed to primary transactional store.
* **Trigger:** Domain event emitted (e.g., `ProductPriceUpdated`, `InventoryExhausted`).
* **Main Success Scenario:**
  1. Search ingestion pipeline consumes catalog update event.
  2. System transforms product entity into search document format.
  3. System commits update to search index within 5 seconds.
  4. Subsequent customer search queries immediately reflect the updated price or stock status.
* **Alternative Scenarios:**
  * Bulk catalog import: Bulk pipeline batches updates and completes index synchronization within 60 seconds.
* **Exception and Failure Scenarios:**
  * Index update failure: Pipeline retries with backoff and logs alert to monitoring console.
* **Business Rules:** `BR-SRCH-002`, `BR-CAT-001`.
* **Inputs and Business Information Required:** Product ID, mutated attribute set, timestamp.
* **Expected Business Outcomes:** Search accuracy preventing customer frustration from stale product listings.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-INV-001`.
* **Acceptance Criteria:**
  * **Given** a product price change committed in the catalog admin, **When** 5 seconds elapse, **Then** a search API query returns the new price in search results.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-002`, `WF-004`.

### <a id="fr-pdp-001"></a>FR-PDP-001: Product Details Page Merchandising & Real-Time Variant Switching
* **Requirement ID:** `FR-PDP-001`
* **Requirement Title:** Interactive Product Details Page (PDP) Merchandising and Dynamic Variant Resolution
* **Feature ID:** `FEAT-PDP-001`
* **Business Objective:** `OBJ-001`, `OBJ-002`
* **Requirement Statement:** The system must render comprehensive product details pages allowing shoppers to switch between variant options (color, storage, RAM) seamlessly, dynamically updating price, high-resolution media gallery, delivery estimate, and live stock availability in real time without full page reload.
* **Description:** Serves as the primary commercial conversion surface, providing consumers with accurate product specifications and immediate purchase capability.
* **Primary Actor:** `ACT-009` (Customer), `ACT-010` (Guest Shopper)
* **Preconditions:** Product is active and published.
* **Trigger:** Shopper navigates to a product URL or clicks a variant swatch.
* **Main Success Scenario:**
  1. Shopper selects a different variant attribute (e.g., switches from 128GB to 256GB).
  2. System resolves selected combination to specific variant SKU.
  3. System updates Selling Price, MRP, discount percentage, SKU-specific image gallery, and stock status ("In Stock" / "Only 2 Left").
  4. System updates "Add to Cart" and "Buy Now" button states accordingly.
* **Alternative Scenarios:**
  * Non-existent variant combination: If a specific combination is not manufactured, system greys out the option and prevents selection.
* **Exception and Failure Scenarios:**
  * Variant out of stock: System disables purchase buttons and displays "Notify Me" back-in-stock alert prompt.
* **Business Rules:** `BR-PDP-001`, `BR-PRC-001`, `BR-INV-001`.
* **Inputs and Business Information Required:** Product slug / ID, selected variant dimension options.
* **Expected Business Outcomes:** High customer engagement and seamless variant selection driving cart additions.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-INV-001`.
* **Acceptance Criteria:**
  * **Given** an active PDP, **When** a shopper clicks a different color swatch, **Then** the image gallery updates to show the selected color within 100ms.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-004`.

---

## 8. Shopping Cart & Checkout Orchestration

### <a id="fr-cart-001"></a>FR-CART-001: Shopping Cart Persistent Storage & Guest Cart Merge
* **Requirement ID:** `FR-CART-001`
* **Requirement Title:** Persistent Shopping Cart Operations and Guest Cart Merging upon Authentication
* **Feature ID:** `FEAT-CART-001`
* **Business Objective:** `OBJ-001`
* **Requirement Statement:** The system must persist customer shopping cart contents across browsing sessions and devices for registered users, and automatically merge anonymous guest carts into the authenticated user cart upon customer login without item loss.
* **Description:** Eliminates cart abandonment caused by login redirects and provides continuous multi-device shopping.
* **Primary Actor:** `ACT-009` (Customer), `ACT-010` (Guest Shopper)
* **Preconditions:** Customer adds items to cart while unauthenticated.
* **Trigger:** Customer logs in or completes registration.
* **Main Success Scenario:**
  1. Guest shopper adds items to anonymous cart (stored with session token).
  2. Shopper authenticates with their registered customer credentials.
  3. System retrieves existing saved cart for the authenticated user.
  4. System merges items from guest cart: for common SKUs, quantities are combined (up to max item purchase limit); unique SKUs are appended.
  5. System invalidates guest cart reference and displays merged cart to user.
* **Alternative Scenarios:**
  * Registered user logs in with empty existing cart: Guest cart items are directly assigned to user account.
* **Exception and Failure Scenarios:**
  * Merged quantity exceeds max purchase limit: System caps quantity at limit and informs user.
* **Business Rules:** `BR-CART-001`, `BR-CART-002`.
* **Inputs and Business Information Required:** Guest session ID, Customer ID, SKU list, quantities.
* **Expected Business Outcomes:** Seamless customer experience and higher conversion from guest browsing.
* **Dependencies:** `FEAT-CUST-001`.
* **Acceptance Criteria:**
  * **Given** a guest cart with SKU A (qty: 1), and a user cart with SKU B (qty: 1), **When** user logs in, **Then** the resulting cart contains both SKU A (qty: 1) and SKU B (qty: 1).
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-011](./08-assumptions-dependencies-open-questions.md#oq-011).
* **Related Workflow IDs:** `WF-005`.

### <a id="fr-cart-002"></a>FR-CART-002: Cart Maximum Item Quantities & Purchase Limits
* **Requirement ID:** `FR-CART-002`
* **Requirement Title:** Enforce SKU Maximum Purchase Limits and Commercial Quantity Caps
* **Feature ID:** `FEAT-CART-001`
* **Business Objective:** `OBJ-001`, `OBJ-003`
* **Requirement Statement:** The system must enforce a strict per-order purchase limit (default: maximum 5 units per SKU per customer order) to prevent bulk reseller hoarding and preserve inventory availability for genuine retail consumers.
* **Description:** Protects promotional consumer electronics from commercial bulk scalpers and ensures fair product distribution.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Item added to cart.
* **Trigger:** Customer attempts to increment item quantity in cart or during add-to-cart action.
* **Main Success Scenario:**
  1. Customer selects quantity $le 5$ (or SKU-specific promotional limit).
  2. System permits quantity update and recalculates cart totals.
* **Alternative Scenarios:**
  * Manager configures promotional flash sale limit of 1 unit: System strictly restricts quantity to 1 for that specific SKU.
* **Exception and Failure Scenarios:**
  * Customer attempts to set quantity > limit: System blocks increase and displays: "Maximum purchase limit for this item is X units per customer."
* **Business Rules:** `BR-CART-002`.
* **Inputs and Business Information Required:** SKU, requested quantity, configured SKU purchase limit.
* **Expected Business Outcomes:** Protection against unauthorized reseller scalping and broader consumer access.
* **Dependencies:** `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** an item with a limit of 2 units, **When** a shopper attempts to add a 3rd unit to their cart, **Then** the system rejects the addition with a purchase limit notification.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-005`.

### <a id="fr-wsh-001"></a>FR-WSH-001: Customer Wishlist Management & Saved Items
* **Requirement ID:** `FR-WSH-001`
* **Requirement Title:** Customer Wishlist Creation, Item Management and Real-Time Stock/Price Visibility
* **Feature ID:** `FEAT-WSH-001`
* **Business Objective:** `OBJ-001`
* **Requirement Statement:** The system must allow authenticated customers to save products to a personal wishlist, remove items, move items directly into the shopping cart, and view real-time price drops or stock status indicators on saved items.
* **Description:** Provides customers with a persistent save-for-later registry, supporting future purchasing intent.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer is authenticated.
* **Trigger:** Customer clicks wishlist heart icon on PDP or browsing page.
* **Main Success Scenario:**
  1. Customer adds SKU to wishlist.
  2. System associates SKU with customer wishlist profile.
  3. When customer views wishlist, system displays current live price, initial saved price, and live availability.
  4. Customer clicks "Move to Cart"; system transfers item to cart and removes from wishlist.
* **Alternative Scenarios:**
  * Unauthenticated shopper clicks wishlist: System prompts login and saves item immediately upon authentication.
* **Exception and Failure Scenarios:**
  * Item discontinued: Wishlist displays "Discontinued by Manufacturer" indicator.
* **Business Rules:** `BR-WSH-001`.
* **Inputs and Business Information Required:** Customer ID, SKU ID, saved timestamp.
* **Expected Business Outcomes:** Improved customer retention and purchase re-engagement.
* **Dependencies:** `FEAT-CUST-001`, `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** an authenticated customer with an item in their wishlist, **When** they click "Move to Cart", **Then** the item is added to their cart and removed from the wishlist.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-005`.

### <a id="fr-chk-001"></a>FR-CHK-001: Idempotent Checkout & Order Placement Validation
* **Requirement ID:** `FR-CHK-001`
* **Requirement Title:** Idempotent Checkout Orchestration and Server-Side Financial Validation
* **Feature ID:** `FEAT-CHK-001`
* **Business Objective:** `OBJ-001`, `OBJ-003`, `OBJ-008`
* **Requirement Statement:** The system must validate all checkout calculations server-side (items, promotions, taxes, shipping fees), mandate a unique client Idempotency Key to prevent double-charging on multiple clicks, and atomically create the order in `PENDING_PAYMENT` status with locked inventory.
* **Description:** Protects financial and inventory integrity during order creation, completely disregarding untrusted client-side mathematical values.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer has items in cart, selects serviceable delivery address, and submits checkout.
* **Trigger:** Customer clicks "Proceed to Payment".
* **Main Success Scenario:**
  1. Client generates unique Idempotency Key (UUID v4) and submits checkout payload.
  2. System checks idempotency registry: key is new.
  3. System re-fetches live catalog prices, recalculates promotions, evaluates taxes, and calculates shipping fee.
  4. System requests atomic stock reservation for 15 minutes (`FR-INV-002`).
  5. Reservation granted; system creates order in `PENDING_PAYMENT` status.
  6. System initiates payment gateway intent and returns session token to client.
* **Alternative Scenarios:**
  * Repeated submission with identical Idempotency Key: System identifies in-flight or completed checkout and returns existing pending order details without re-reserving stock or creating duplicate orders.
* **Exception and Failure Scenarios:**
  * Concurrent stock exhaustion: Inventory reservation denied; system aborts checkout and informs customer.
  * Price discrepancy: Live price changed during checkout; system halts checkout and requires cart refresh.
* **Business Rules:** `BR-CHK-001`, `BR-CHK-002`, `BR-INV-001`.
* **Inputs and Business Information Required:** Idempotency Key, customer ID, delivery address ID, cart ID, coupon code (optional).
* **Expected Business Outcomes:** Zero duplicate orders from double-clicking and 100% accurate server-verified financial totals.
* **Dependencies:** `FEAT-INV-002`, `FEAT-FIN-001`, `FEAT-PAY-001`.
* **Acceptance Criteria:**
  * **Given** a checkout submission, **When** the identical request payload and Idempotency Key is received twice within 60 seconds, **Then** only one order is created and only one inventory reservation is made.
  * **Given** a client payload claiming a total of ₹5,000, **When** server calculation resolves total to ₹6,200, **Then** the client total is ignored and ₹6,200 is used for payment creation.
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-006](./08-assumptions-dependencies-open-questions.md#oq-006).
* **Related Workflow IDs:** `WF-006`.

### <a id="fr-chk-002"></a>FR-CHK-002: Real-Time Cart Revalidation & Price Lock at Checkout
* **Requirement ID:** `FR-CHK-002`
* **Requirement Title:** Revalidate Cart Items and Lock Financial Totals During Checkout Session
* **Feature ID:** `FEAT-CHK-001`
* **Business Objective:** `OBJ-001`, `OBJ-008`
* **Requirement Statement:** The system must lock all financial totals (item unit prices, applied discounts, taxes, and delivery charges) once the order enters `PENDING_PAYMENT`, guaranteeing that the customer is charged the exact locked sum even if underlying catalog prices update during their 15-minute payment session.
* **Description:** Protects consumer trust by providing a guaranteed price lock during the active payment window.
* **Primary Actor:** System (Automated Checkout Engine)
* **Preconditions:** Order created in `PENDING_PAYMENT` with active stock reservation.
* **Trigger:** Payment gateway session initiation.
* **Main Success Scenario:**
  1. System snapshots financial values on the order entity.
  2. Catalog price of an ordered item is modified by an admin while customer is entering UPI PIN.
  3. Customer completes payment; system captures the exact snapshot price recorded on the order.
  4. Order confirms successfully at the agreed checkout price.
* **Alternative Scenarios:**
  * Payment session expires: Price lock is released; if customer resumes checkout, a new cart revalidation is executed against current catalog prices.
* **Exception and Failure Scenarios:**
  * Gateway charges an amount different from the locked order total: System marks transaction as mismatched, flags security alert, and does not confirm order.
* **Business Rules:** `BR-CHK-001`, `BR-PRC-001`.
* **Inputs and Business Information Required:** Order ID, locked amount snapshot, payment callback amount.
* **Expected Business Outcomes:** Predictable, legally sound checkout transactions.
* **Dependencies:** `FEAT-CHK-001`, `FEAT-PAY-001`.
* **Acceptance Criteria:**
  * **Given** an order created in `PENDING_PAYMENT` with locked total ₹24,999, **When** the catalog price changes to ₹26,999 prior to payment capture, **Then** the customer is charged ₹24,999 and the order is confirmed without error.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`, `WF-007`.

---

## 9. Payment Gateway Orchestration & Reconciliation

### <a id="fr-pay-001"></a>FR-PAY-001: Payment Gateway Webhook Ingestion & Delayed Payment Resolution
* **Requirement ID:** `FR-PAY-001`
* **Requirement Title:** Idempotent Cryptographic Webhook Processing and Late Payment Reconciliation
* **Feature ID:** `FEAT-PAY-001`
* **Business Objective:** `OBJ-005`, `OBJ-008`
* **Requirement Statement:** The system must process payment gateway webhooks idempotently after validating cryptographic HMAC signatures, and handle delayed payment captures arriving after stock reservation expiry according to deterministic stock availability rules.
* **Description:** Ensures reliable, tamper-evident payment reconciliation between third-party payment gateways and internal order records.
* **Primary Actor:** `ACT-011` (Payment Gateway), System (Automated)
* **Preconditions:** Gateway sends webhook event payload with cryptographic signature header.
* **Trigger:** Inbound webhook HTTP POST request received.
* **Main Success Scenario:**
  1. System verifies HMAC SHA-256 signature against registered gateway secret.
  2. System checks idempotency table for event ID: event is new.
  3. System matches payment gateway reference to internal Order ID.
  4. If reservation is active: System marks order `CONFIRMED`, commits stock deduction, issues tax invoice, and returns HTTP 200 OK.
* **Alternative Scenarios:**
  * Duplicate webhook received: System verifies signature, detects previously processed event ID, and immediately returns HTTP 200 OK without re-processing.
* **Exception and Failure Scenarios:**
  * Invalid signature: System rejects request with HTTP 401 Unauthorized and logs high-severity security alert.
  * Late Payment Arrival (Reservation Expired):
    * **Case A (Stock Available):** System re-reserves stock and confirms order.
    * **Case B (Stock Sold Out):** System cancels order, sets status `PAYMENT_RECEIVED_OUT_OF_STOCK`, and initiates immediate 100% automated refund to original payment source.
* **Business Rules:** `BR-PAY-001`, `BR-PAY-002`, `BR-PAY-003`.
* **Inputs and Business Information Required:** Webhook raw payload, signature header, gateway transaction ID, order reference, payment status.
* **Expected Business Outcomes:** 100% financial transaction accuracy and automated resolution of late payments.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-INV-002`, `FEAT-RET-002`.
* **Acceptance Criteria:**
  * **Given** an invalid HMAC webhook signature, **When** processed by the webhook endpoint, **Then** the system rejects with HTTP 401 and zero order state mutations occur.
  * **Given** a payment captured after the 15-minute hold where the last unit was purchased by another buyer, **When** webhook arrives, **Then** an automated 100% refund is initiated and status transitions to `PAYMENT_RECEIVED_OUT_OF_STOCK`.
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-003](./08-assumptions-dependencies-open-questions.md#oq-003), [OQ-004](./08-assumptions-dependencies-open-questions.md#oq-004).
* **Related Workflow IDs:** `WF-007`.

### <a id="fr-pay-002"></a>FR-PAY-002: Payment Gateway Failure Handling & Retry Orchestration
* **Requirement ID:** `FR-PAY-002`
* **Requirement Title:** Manage Payment Failures, Error Messaging and Customer Retry Opportunities
* **Feature ID:** `FEAT-PAY-001`
* **Business Objective:** `OBJ-005`
* **Requirement Statement:** The system must capture payment failure reasons reported by the gateway (e.g., insufficient funds, bank authentication declined, network timeout), present clear non-technical error guidance to the customer, and maintain the open inventory reservation for the remainder of the 15-minute window to permit payment retries.
* **Description:** Recovers potentially lost sales by allowing customers to retry payment with alternative payment methods without losing their reserved cart items.
* **Primary Actor:** System (Automated), `ACT-009` (Customer)
* **Preconditions:** Payment gateway returns a failure callback or declined webhook.
* **Trigger:** Customer transaction fails on gateway interface.
* **Main Success Scenario:**
  1. System logs payment failure code and friendly description.
  2. System records failed attempt under order audit trail.
  3. System displays retry interface to customer: "Payment declined by bank. Please try another card or UPI."
  4. Customer selects UPI, completes payment successfully within remaining TTL.
  5. Order transitions to `CONFIRMED`.
* **Alternative Scenarios:**
  * Customer abandons retry: 15-minute timer expires; system releases stock and marks order `PAYMENT_FAILED`.
* **Exception and Failure Scenarios:**
  * Fraud alert flagged by gateway: System blocks retry on that payment instrument and flags account.
* **Business Rules:** `BR-PAY-002`, `BR-INV-003`.
* **Inputs and Business Information Required:** Order ID, failure code, failure message, remaining TTL seconds.
* **Expected Business Outcomes:** Maximized payment recovery rates and improved customer conversion.
* **Dependencies:** `FEAT-PAY-001`, `FEAT-INV-002`.
* **Acceptance Criteria:**
  * **Given** an order with 8 minutes remaining on its reservation, **When** a credit card payment is declined, **Then** the reservation remains active and the customer is presented with alternative payment options.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-007`.

### <a id="fr-pay-003"></a>FR-PAY-003: Gateway Settlement Reconciliation & Discrepancy Reporting
* **Requirement ID:** `FR-PAY-003`
* **Requirement Title:** Automated Daily Gateway Settlement Reconciliation and Discrepancy Flagging
* **Feature ID:** `FEAT-PAY-002`
* **Business Objective:** `OBJ-005`, `OBJ-008`
* **Requirement Statement:** The system must perform an automated daily reconciliation between payment gateway settlement reports and internal order ledger entries, automatically identifying and flagging discrepancies (unsettled orders, fee variances, missing captures, unrecognized refunds) for financial review.
* **Description:** Guarantees enterprise financial control by matching bank settlement batches against commercial order records.
* **Primary Actor:** System (Automated), `ACT-007` (Finance Specialist)
* **Preconditions:** Payment gateway publishes daily settlement report via API or SFTP.
* **Trigger:** Daily reconciliation job executes (e.g., 02:00 AM IST).
* **Main Success Scenario:**
  1. System ingests gateway settlement statement for previous day.
  2. System matches each settlement transaction ID against internal payment records.
  3. System validates gross amount, gateway processing fees, GST on fees, and net settlement deposit.
  4. System marks matched orders as `RECONCILED`.
  5. System generates reconciliation summary report for Finance team.
* **Alternative Scenarios:**
  * Discrepancy identified (e.g., order confirmed but missing from gateway payout): System marks record as `RECONCILIATION_EXCEPTION` and routes alert to Finance Specialist.
* **Exception and Failure Scenarios:**
  * Settlement report missing or format corrupted: System raises alert to Finance Lead.
* **Business Rules:** `BR-PAY-003`.
* **Inputs and Business Information Required:** Gateway settlement report, internal order ledger, fee schedule.
* **Expected Business Outcomes:** 100% financial traceability and zero unaccounted revenue leakage.
* **Dependencies:** `FEAT-PAY-001`, `FEAT-RPT-001`.
* **Acceptance Criteria:**
  * **Given** a daily settlement report with 500 transactions, **When** reconciliation runs, **Then** 100% of matched orders transition to `RECONCILED` and any variance $> ₹1$ generates a financial exception alert.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-014`.

---

## 10. Order Management & Warehouse Fulfillment

### <a id="fr-ord-001"></a>FR-ORD-001: Order State Transitions and Warehouse Picking/Packing
* **Requirement ID:** `FR-ORD-001`
* **Requirement Title:** Validated Order State Machine and Warehouse Picking/Packing Lifecycle
* **Feature ID:** `FEAT-ORD-001`
* **Business Objective:** `OBJ-004`
* **Requirement Statement:** The system must enforce a deterministic, non-reversible order state machine (`PENDING_PAYMENT` $\rightarrow$ `CONFIRMED` $\rightarrow$ `PROCESSING` $\rightarrow$ `PACKED` $\rightarrow$ `SHIPPED` $\rightarrow$ `OUT_FOR_DELIVERY` $\rightarrow$ `DELIVERED`), strictly validating role authorizations and operational preconditions for each transition.
* **Description:** Governs the entire physical and logical lifecycle of customer orders from confirmation through doorstep delivery.
* **Primary Actor:** System (Automated), `ACT-004` (Warehouse / Fulfillment Specialist)
* **Preconditions:** Order exists in valid upstream state.
* **Trigger:** Operator scans barcode or system event occurs.
* **Main Success Scenario:**
  1. Payment captures; order enters `CONFIRMED`.
  2. Warehouse operator generates pick list; order enters `PROCESSING`.
  3. Operator picks physical units, verifies SKU and IMEI barcodes; order enters `PACKED`.
  4. Courier partner picks up parcel and scans AWB; order enters `SHIPPED`.
  5. Courier webhook reports out for delivery; order enters `OUT_FOR_DELIVERY`.
  6. Final delivery confirmed by courier; order enters terminal state `DELIVERED`.
* **Alternative Scenarios:**
  * Customer cancellation: Allowed strictly before order enters `PACKED` status (`FR-ORD-002`).
* **Exception and Failure Scenarios:**
  * Invalid transition attempt (e.g., trying to jump from `PROCESSING` directly to `DELIVERED`): System rejects operation with a state machine violation error.
  * Barcode mismatch during packing: System alerts operator that scanned device does not match order SKU.
* **Business Rules:** `BR-ORD-001`, `BR-ORD-002`, `BR-ORD-004`.
* **Inputs and Business Information Required:** Order ID, target state, operator ID, scanned SKU/serial barcodes, timestamp.
* **Expected Business Outcomes:** Complete operational visibility and zero shipping errors.
* **Dependencies:** `FEAT-INV-001`, `FEAT-SHP-001`.
* **Acceptance Criteria:**
  * **Given** an order in `CONFIRMED` state, **When** an operator initiates picking, **Then** the order successfully transitions to `PROCESSING` and a pick list is generated.
  * **Given** an order in `SHIPPED` state, **When** an API call attempts to transition state back to `PROCESSING`, **Then** the system strictly rejects the transition as illegal.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-008`.

### <a id="fr-ord-002"></a>FR-ORD-002: Customer Order Cancellation Prior to Dispatch
* **Requirement ID:** `FR-ORD-002`
* **Requirement Title:** Customer Self-Service Order Cancellation Prior to Packaging and Dispatch
* **Feature ID:** `FEAT-ORD-001`
* **Business Objective:** `OBJ-004`, `OBJ-005`
* **Requirement Statement:** The system must allow customers to self-service cancel an order while it remains in `CONFIRMED` or `PROCESSING` status, immediately releasing committed inventory and triggering an automated 100% refund, while strictly blocking cancellation once the order transitions to `PACKED` or `SHIPPED`.
* **Description:** Provides customer flexibility while preventing operational chaos and shipping wasted freight once warehouse packing has commenced.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Order status is `CONFIRMED` or `PROCESSING`.
* **Trigger:** Customer clicks "Cancel Order" in order management view.
* **Main Success Scenario:**
  1. Customer selects cancellation reason code from predefined list.
  2. System verifies order status is prior to `PACKED`.
  3. System transitions order to `CANCELLED`.
  4. System immediately releases inventory back to Available-to-Sell stock.
  5. System triggers automated refund dispatch to payment gateway (`FR-RET-002`).
  6. System issues statutory Credit Note (`FR-FIN-003`) and notifies customer via SMS/Email.
* **Alternative Scenarios:**
  * Administrator cancels order: System allows administrative cancellation with mandatory supervisor reason code.
* **Exception and Failure Scenarios:**
  * Order is already `PACKED` or `SHIPPED`: System blocks cancellation and informs customer: "Order is already packed for shipment and cannot be cancelled. You may refuse delivery or initiate a return upon receipt."
* **Business Rules:** `BR-ORD-003`, `BR-RET-003`, `BR-FIN-003`.
* **Inputs and Business Information Required:** Order ID, customer ID, cancellation reason code.
* **Expected Business Outcomes:** Immediate inventory restock and rapid refund processing without manual support intervention.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-RET-002`, `FEAT-INV-001`.
* **Acceptance Criteria:**
  * **Given** an order in `CONFIRMED` status, **When** the customer submits cancellation, **Then** order status becomes `CANCELLED`, inventory is restored to ATS, and refund is dispatched within 5 seconds.
  * **Given** an order in `PACKED` status, **When** the customer attempts cancellation, **Then** the system blocks the request.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-009`.

### <a id="fr-ord-003"></a>FR-ORD-003: Warehouse Order Picking, Packing & Manifest Handoff
* **Requirement ID:** `FR-ORD-003`
* **Requirement Title:** Warehouse Batch Pick Lists, Serial Number Verification and Manifest Handoff
* **Feature ID:** `FEAT-ORD-001`
* **Business Objective:** `OBJ-004`
* **Requirement Statement:** The system must generate consolidated warehouse pick lists sorted by bin location, enforce handheld barcode scanning of SKU and individual device serial numbers (IMEI/Serial) during packing, and generate official courier handover manifests.
* **Description:** Streamlines physical warehouse operations and establishes serialized device tracking for warranty and fraud prevention.
* **Primary Actor:** `ACT-004` (Fulfillment Specialist / Warehouse Operator)
* **Preconditions:** Orders exist in `CONFIRMED` status.
* **Trigger:** Warehouse operator initiates wave fulfillment batch.
* **Main Success Scenario:**
  1. System compiles wave pick list grouped by warehouse zone and bin location.
  2. Operator picks items and brings them to packaging bench.
  3. Operator scans order packing slip barcode.
  4. Operator scans product EAN barcode and unique device serial/IMEI barcode.
  5. System validates scanned serial against manufacturer format rules and binds IMEI to the order record.
  6. System generates shipping label and packing slip.
  7. Operator packs parcel, affixes label, and scans parcel onto courier handover manifest.
* **Alternative Scenarios:**
  * Non-serialized electronics accessories: System mandates SKU barcode scan but bypasses serial number prompt.
* **Exception and Failure Scenarios:**
  * Scanned serial already registered to another active order: System halts packing and raises duplicate serial security exception.
* **Business Rules:** `BR-ORD-004`, `BR-SHP-001`.
* **Inputs and Business Information Required:** Batch ID, Order ID, scanned SKU, scanned IMEI/Serial number, package weight.
* **Expected Business Outcomes:** Zero picking errors and 100% serialized tracking of high-value electronics.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-SHP-001`.
* **Acceptance Criteria:**
  * **Given** a smartphone order packing step, **When** the operator attempts to mark the order `PACKED` without scanning a valid IMEI, **Then** the system denies completion until a valid IMEI barcode is scanned.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-008`.

---

## 11. Shipping, Logistics & Delivery Tracking

### <a id="fr-shp-001"></a>FR-SHP-001: In-House Shipment Creation, Dispatch Assignment & Shipping Label
* **Requirement ID:** `FR-SHP-001`
* **Requirement Title:** Provider-Neutral Shipment Lifecycle, Shipment ID and Dispatch Manifest
* **Feature ID:** `FEAT-SHP-001`
* **Business Objective:** `OBJ-006`
* **Requirement Statement:** The system must create a unique internal Shipment ID for each eligible confirmed order, preserve a snapshot of the delivery address and package contents, support packing checks, generate an internal shipment/packing label with a scannable Shipment ID, and audit every custody handover. It must support own-team fulfilment and provider-neutral adapters for approved direct courier partners and a shipping aggregator. External AWB/booking references remain secondary to the internal Shipment ID.
* **Description:** Provides a canonical shipment record across Chellaa's own delivery operations, direct courier partners and an approved shipping aggregator. Provider-specific booking/AWB generation is performed only for enabled and validated provider lanes.
* **Primary Actor:** System, Fulfillment Specialist, Dispatcher / Route Coordinator, Delivery Associate.
* **Preconditions:** Order is confirmed and released for fulfillment; inventory is allocated; destination is within the approved internal service area.
* **Trigger:** Order is released for fulfillment or shipment is ready for dispatch.
* **Main Success Scenario:**
  1. System creates an idempotent Shipment ID and links it to the order, immutable address snapshot, package items, declared value, and COD due where applicable.
  2. Warehouse user scans order items and records package checks, weight/dimensions, packing operator, and timestamps.
  3. System generates an internal label/manifest reference with a scannable Shipment ID; when an approved external provider is selected, its label/AWB/reference is stored separately and linked to the internal Shipment ID.
  4. Dispatcher or configured routing rules assign the shipment to an enabled own-team, direct-courier, or aggregator lane and record the applicable custody/pickup evidence.
  5. The system records the internal Shipment ID, selected lane, provider booking reference if applicable, manifest, custody handover evidence, actor and timestamp.
* **Alternative Scenarios:**
  * Shipment creation request is retried: return the existing active Shipment ID rather than creating a duplicate.
  * Direct carrier integration is later approved: add a separately scoped adapter and carrier-specific identifiers without replacing the internal Shipment ID.
* **Exception and Failure Scenarios:**
  * Item/serial mismatch, package damage, missing delivery address, out-of-area PIN code, or missing assignment blocks dispatch and creates a tracked exception.
* **Business Rules:** `BR-SHP-001`, `BR-ORD-001`, `BR-ORD-002`.
* **Inputs and Business Information Required:** Order ID, destination/address snapshot, contact phone, item/SKU and serial/IMEI where applicable, package weight/dimensions, declared value, COD due, actor/assignment, and timestamps.
* **Expected Business Outcomes:** Each dispatched parcel has a unique internal identifier, traceable custody, assigned owner, and audit history without requiring a courier partner.
* **Dependencies:** `FEAT-ORD-001`, `DEP-002`, `DEP-006`.
* **Acceptance Criteria:**
  * **Given** an eligible confirmed order, **when** shipment creation is requested once or retried with the same idempotency key, **then** exactly one active Shipment ID is linked to that order.
  * **Given** a shipment ready for dispatch, **when** it is assigned and handed over, **then** the system records the delivery run, associate, manifest, both custody acknowledgements, and timestamps.
  * **Given** an unapproved or unavailable carrier integration, **when** shipment creation occurs, **then** the workflow remains fully usable without calling an external carrier API.
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-005](./08-assumptions-dependencies-open-questions.md#oq-005) and [DEP-002](./08-assumptions-dependencies-open-questions.md#dep-002).
* **Related Workflow IDs:** `WF-008`.

### <a id="fr-shp-002"></a>FR-SHP-002: Internal Delivery Tracking, Proof of Delivery & Customer Alerts
* **Requirement ID:** `FR-SHP-002`
* **Requirement Title:** Unified Shipment Milestones, Provider Tracking Events and Proof of Delivery
* **Feature ID:** `FEAT-SHP-001`
* **Business Objective:** `OBJ-006`
* **Requirement Statement:** The system must record authenticated own-team shipment milestone events and support validated API/webhook tracking updates from approved direct courier partners and a shipping aggregator. It must deduplicate external events, map provider-specific states/reasons to the common internal shipment lifecycle, and retain provider references, delivery attempts, proof of delivery, rescheduling, returns/RTO events, and customer alerts. Provider-specific integrations may be enabled only after their contracts, credentials, mapping, failure handling and end-to-end tests are approved.
* **Description:** Gives customers and operations staff a unified view of shipments across own-team, direct-courier and aggregator lanes, while preserving auditable delivery evidence and provider-specific exception details.
* **Primary Actor:** Delivery Associate, Dispatcher / Route Coordinator, Shipping Operations Lead, Customer Support Agent, System.
* **Preconditions:** Shipment has a valid internal Shipment ID and an assigned delivery run/associate.
* **Trigger:** Internal staff scan/action records a shipment milestone or delivery attempt.
* **Main Success Scenario:**
  1. System records own-team or provider shipment events with internal Shipment ID, provider reference where applicable, actor/source, timestamp, state, and optional location/reason metadata.
  2. System deduplicates external events, maps provider-specific statuses to approved internal states, validates allowed transitions, and appends the event to the shipment timeline.
  3. On out-for-delivery, system sends a customer notification with the customer-safe tracking link.
  4. On successful delivery, system validates required delivery OTP and records recipient confirmation and proof metadata before marking the order/shipment delivered.
  5. System sends delivery confirmation and starts the 7-calendar-day return eligibility window from the verified delivery timestamp.
* **Alternative Scenarios:**
  * Failed attempt: record a standardized reason, notify customer and Support, and allow a permitted retry up to three consecutive calendar-day attempts.
  * Third failed attempt: escalate to Shipping Operations Lead and record return-to-warehouse/RTO custody; no external carrier event is implied.
* **Exception and Failure Scenarios:**
  * Missing/invalid OTP or insufficient delivery evidence: block `DELIVERED` and open a dispute/exception.
  * Duplicate/out-of-order event: enforce idempotency and state-transition rules; preserve rejected event details for audit where appropriate.
  * Device/network outage: queue authorized offline event capture with device/user/time metadata and synchronize with duplicate protection; final delivered state remains subject to required proof validation.
* **Business Rules:** `BR-SHP-002`, `BR-NTF-001`, OQ-006 delivery OTP policy.
* **Inputs and Business Information Required:** Shipment ID, event type, authenticated actor, timestamp, attempt number, standardized reason code, delivery OTP validation result, recipient confirmation, and permitted proof metadata.
* **Expected Business Outcomes:** Internal tracking timeline, auditable delivery proof, proactive customer notifications, controlled failed-delivery retries, and visible return-to-warehouse status.
* **Dependencies:** `FEAT-SHP-001`, `FEAT-NTF-001`, `DEP-002`, `DEP-003`.
* **Acceptance Criteria:**
  * **Given** an assigned shipment, **when** an associate records a valid milestone, **then** the event is stored with actor and timestamp and the customer-safe timeline is updated.
  * **Given** a delivery attempt without valid required OTP/evidence, **when** the associate attempts to mark it delivered, **then** the transition is rejected and an exception is recorded.
  * **Given** a verified successful delivery, **when** the delivery event is accepted, **then** the order/shipment becomes `DELIVERED`, a confirmation notification is queued, and the 7-day return timer starts from the verified timestamp.
  * **Given** three failed attempts on consecutive calendar days, **when** the third failure is recorded, **then** a return-to-warehouse task is created and the Operations Lead is notified.
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED` (revised to in-house delivery model; requires domain-owner review).
* **Open Questions:** DEP-002 readiness checklist; any direct carrier integration requires a separate decision.
* **Related Workflow IDs:** `WF-008`.

---

## 12. Returns, Replacements & Refunds

### <a id="fr-ret-001"></a>FR-RET-001: Customer Return Request Lifecycle and Warehouse Inspection
* **Requirement ID:** `FR-RET-001`
* **Requirement Title:** Enforce 7-Day Return Eligibility and Warehouse Device Condition Inspection
* **Feature ID:** `FEAT-RET-001`
* **Business Objective:** `OBJ-007`
* **Requirement Statement:** The system must enforce a strict 7-calendar-day return eligibility window from delivery timestamp for consumer electronics, requiring customers to provide reason codes and defect photographs, and mandating physical warehouse inspection before refund approval.
* **Description:** Protects the enterprise from fraudulent returns while honoring genuine defect resolutions.
* **Primary Actor:** `ACT-009` (Customer), `ACT-004` (Warehouse Inspector)
* **Preconditions:** Order is in `DELIVERED` status within the 7-calendar-day eligibility window.
* **Trigger:** Customer submits return request from order history.
* **Main Success Scenario:**
  1. Customer selects eligible item, specifies reason code (Defective, Damaged in Transit, Wrong Item), and uploads clear photos.
  2. System validates request is within 7 days of delivery timestamp.
  3. System creates Return Merchandise Authorization (RMA) in `RETURN_REQUESTED` status.
  4. Reverse courier picks up parcel and transits to enterprise warehouse.
  5. Warehouse inspector scans returned device IMEI, inspects condition against checklist (original packaging, accessories, physical damage), and marks inspection as `APPROVED`.
  6. System triggers refund remittance (`FR-RET-002`).
* **Alternative Scenarios:**
  * Customer requests replacement: If stock is available, system creates replacement order at ₹0 instead of issuing refund.
* **Exception and Failure Scenarios:**
  * Return window expired: Customer attempts return on day 8; system rejects request with notice directing customer to manufacturer warranty service.
  * Inspection rejected: Inspector detects IMEI mismatch or customer-induced damage; system marks return `REJECTED`, attaches inspection photos, denies refund, and arranges return of device to customer.
* **Business Rules:** `BR-RET-001`, `BR-RET-002`.
* **Inputs and Business Information Required:** Order ID, item SKU, return reason code, defect description, customer photos, inspection outcome code.
* **Expected Business Outcomes:** Controlled return fraud and audited device condition tracking.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-SHP-001`.
* **Acceptance Criteria:**
  * **Given** an order delivered 8 days ago, **When** the customer attempts to submit a return, **Then** the platform blocks the submission with an "Eligibility Expired" notice.
  * **Given** an inwarded return with a mismatched IMEI, **When** the inspector records "IMEI Mismatch", **Then** the return is flagged `REJECTED` and refund processing is blocked.
* **Priority:** `P1`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-008](./08-assumptions-dependencies-open-questions.md#oq-008).
* **Related Workflow IDs:** `WF-010`.

### <a id="fr-ret-002"></a>FR-RET-002: Automated Refund Remittance to Source Instrument
* **Requirement ID:** `FR-RET-002`
* **Requirement Title:** Automated Refund Processing to Original Payment Instrument
* **Feature ID:** `FEAT-RET-002`
* **Business Objective:** `OBJ-005`
* **Requirement Statement:** The system must disburse approved refunds strictly to the customer's original payment source (Credit Card, Net Banking, UPI) via payment gateway refund APIs, completely prohibiting cash payouts for online payments.
* **Description:** Ensures compliance with anti-money laundering (AML) regulations and automates refund accounting.
* **Primary Actor:** System (Automated), `ACT-007` (Finance Specialist)
* **Preconditions:** Return inspection is `APPROVED` or order is cancelled prior to packaging.
* **Trigger:** Return approval or cancellation event.
* **Main Success Scenario:**
  1. System calculates refundable amount (pro-rata item price plus taxes).
  2. System issues payment gateway refund API call referencing original gateway payment ID.
  3. Gateway returns refund reference number and confirms initiation.
  4. System records refund transaction ID, generates Credit Note (`FR-FIN-003`), and transitions return status to `REFUND_COMPLETED`.
  5. System notifies customer via SMS/Email with refund reference.
* **Alternative Scenarios:**
  * Cash on Delivery (COD) order refund: System prompts customer for verified bank account details (IFSC, Account Number) or UPI VPA and initiates payout via gateway transfer API.
* **Exception and Failure Scenarios:**
  * Gateway refund API failure: System queues refund for automated retry with exponential backoff and alerts Finance Specialist if failure persists $> 24\text{ hours}$.
* **Business Rules:** `BR-RET-003`, `BR-FIN-003`.
* **Inputs and Business Information Required:** Order ID, Payment Gateway Ref ID, refund amount (₹), reason code.
* **Expected Business Outcomes:** Rapid, compliant customer refunds with complete financial auditability.
* **Dependencies:** `FEAT-PAY-001`, `FEAT-FIN-002`.
* **Acceptance Criteria:**
  * **Given** an approved return for an order paid via UPI, **When** refund processing triggers, **Then** the gateway refund API is called targeting the original UPI transaction ID and a Credit Note is generated.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-009`, `WF-010`.

### <a id="fr-ret-003"></a>FR-RET-003: Return Authorization & Reverse Courier Pickup Scheduling
* **Requirement ID:** `FR-RET-003`
* **Requirement Title:** Automated Reverse Logistics Consignment Booking and Pickup Scheduling
* **Feature ID:** `FEAT-RET-001`
* **Business Objective:** `OBJ-006`, `OBJ-007`
* **Requirement Statement:** The system must automatically schedule a reverse pickup with integrated courier logistics partners upon return approval, generating a reverse AWB and providing the customer with pickup tracking.
* **Description:** Automates reverse supply chain logistics, eliminating manual pickup booking by support staff.
* **Primary Actor:** System (Automated), `ACT-012` (Courier Partner)
* **Preconditions:** Customer return request meets initial policy validation.
* **Trigger:** Return request approval.
* **Main Success Scenario:**
  1. System transmits reverse pickup booking payload to courier partner API (customer address, package weight, return center address).
  2. Courier API allocates reverse AWB number and confirms pickup window (e.g., next business day).
  3. System attaches reverse AWB to RMA record and dispatches pickup instructions to customer via SMS/WhatsApp.
  4. Courier agent collects package, scans AWB, and transits parcel to enterprise warehouse.
* **Alternative Scenarios:**
  * Courier reverse pickup unserviceable: System alerts support agent to arrange alternative national carrier pickup.
* **Exception and Failure Scenarios:**
  * Customer cancels return: System calls courier API to cancel reverse pickup consignment.
* **Business Rules:** `BR-RET-002`, `BR-SHP-001`.
* **Inputs and Business Information Required:** RMA ID, customer address, package weight, courier partner code.
* **Expected Business Outcomes:** Fast reverse logistics turnaround and audited chain of custody for returned devices.
* **Dependencies:** `FEAT-RET-001`, `DEP-002`.
* **Acceptance Criteria:**
  * **Given** an approved return request, **When** reverse pickup booking executes, **Then** a reverse AWB is generated and pickup confirmation is dispatched to the customer within 10 seconds.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-010`.

---

## 13. Promotions, Coupons & Discounts

### <a id="fr-prm-001"></a>FR-PRM-001: Coupon Redemption Engine and Non-Stacking Enforcement
* **Requirement ID:** `FR-PRM-001`
* **Requirement Title:** Strict Promotional Coupon Non-Stacking and Usage Quota Governance
* **Feature ID:** `FEAT-PRM-001`
* **Business Objective:** `OBJ-001`, `OBJ-008`
* **Requirement Statement:** The system must enforce that promotional coupons cannot be stacked on a single order (maximum one coupon per order), validate global and per-customer redemption limits, and verify minimum order value requirements before applying discounts.
* **Description:** Protects enterprise gross margins from coupon abuse and unintended discount compounding.
* **Primary Actor:** `ACT-009` (Customer)
* **Preconditions:** Customer applies promo code in cart or checkout.
* **Trigger:** Customer submits coupon code.
* **Main Success Scenario:**
  1. Customer enters promo code (e.g., `SAVE10`).
  2. System checks code validity: active status, within start/end dates, global quota not exhausted.
  3. System checks customer redemption history: customer has not exceeded per-user limit (`BR-PRM-001`).
  4. System verifies cart meets minimum purchase threshold.
  5. System calculates discount amount (capped at maximum discount limit).
  6. System applies discount and updates order summary.
* **Alternative Scenarios:**
  * Replacing existing coupon: Customer applies code B while code A is active; system prompts customer to confirm replacement of code A.
* **Exception and Failure Scenarios:**
  * Code expired or quota exhausted: System rejects code with explicit error notice.
  * Attempting to stack coupons via API manipulation: System strictly accepts only the single most recently applied valid code.
* **Business Rules:** `BR-PRM-001`, `BR-PRM-002`.
* **Inputs and Business Information Required:** Coupon code, customer ID, cart total, item category IDs.
* **Expected Business Outcomes:** Predictable promotional spending and zero margin erosion from coupon stacking.
* **Dependencies:** `FEAT-CART-001`, `FEAT-CHK-001`.
* **Acceptance Criteria:**
  * **Given** an order with coupon A applied, **When** the customer applies coupon B, **Then** coupon A is removed and only coupon B is applied (zero stacking).
  * **Given** a coupon with a 1-time usage limit per user, **When** a customer attempts to redeem it a second time, **Then** the system rejects redemption with "Coupon already redeemed".
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-011`.

### <a id="fr-prm-002"></a>FR-PRM-002: Pro-Rata Item-Level Discount Allocation & Free Shipping Application
* **Requirement ID:** `FR-PRM-002`
* **Requirement Title:** Proportionally Allocate Cart-Level Discounts Across Eligible Line Items
* **Feature ID:** `FEAT-PRM-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must proportionally allocate cart-level promotional discounts across eligible order line items on a pro-rata value basis, ensuring accurate line-item taxable values and correct pro-rata refund calculations in the event of partial returns.
* **Description:** Ensures statutory tax compliance and accurate partial refund handling when basket-level discounts are applied.
* **Primary Actor:** System (Automated Calculation Engine)
* **Preconditions:** Valid coupon or basket discount applied to cart.
* **Trigger:** Order pricing calculation executed.
* **Main Success Scenario:**
  1. System calculates total eligible order discount (e.g., ₹1,000 off).
  2. System computes each eligible line item's percentage of total eligible cart value.
  3. System allocates discount proportionally across line items, rounding to nearest currency subunit (paise) with residual penny adjustment on the largest item.
  4. System calculates GST on the net discounted price of each line item.
* **Alternative Scenarios:**
  * Free Shipping promotion: System zeroes the delivery fee component without altering product line item prices.
* **Exception and Failure Scenarios:**
  * Ineligible items in cart: System excludes ineligible items (e.g., clearance items) from discount distribution.
* **Business Rules:** `BR-PRM-002`, `BR-FIN-001`.
* **Inputs and Business Information Required:** Line item prices, discount amount, eligibility flags.
* **Expected Business Outcomes:** Flawless financial integrity and audit-ready partial return calculations.
* **Dependencies:** `FEAT-PRM-001`, `FEAT-FIN-001`.
* **Acceptance Criteria:**
  * **Given** a cart with Item A (₹6,000) and Item B (₹4,000) and a ₹1,000 discount, **When** pro-rata allocation runs, **Then** Item A receives ₹600 discount (net ₹5,400) and Item B receives ₹400 discount (net ₹3,600).
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-006`, `WF-011`.

---

## 14. Notifications & Customer Communications

### <a id="fr-ntf-001"></a>FR-NTF-001: Multi-Channel Event-Driven Notification Delivery
* **Requirement ID:** `FR-NTF-001`
* **Requirement Title:** Event-Driven Asynchronous Notification Relay with Multi-Channel Fallback
* **Feature ID:** `FEAT-NTF-001`
* **Business Objective:** `OBJ-006`, `OBJ-007`
* **Requirement Statement:** The system must ingest domain events asynchronously and dispatch transactional customer notifications across SMS, WhatsApp, and Email, automatically falling back to SMS if WhatsApp delivery fails or times out within 60 seconds.
* **Description:** Guarantees critical transactional messaging (OTPs, order confirmations, dispatch alerts) reaches consumers promptly regardless of third-party gateway outages.
* **Primary Actor:** System (Automated Notification Engine)
* **Preconditions:** Domain event published with recipient contact data.
* **Trigger:** Critical lifecycle event published (e.g., `OrderConfirmedEvent`, `OTPGeneratedEvent`).
* **Main Success Scenario:**
  1. System listens to domain event and matches it to a registered notification template.
  2. System resolves customer preferred channel (e.g., WhatsApp).
  3. System dispatches message to primary channel gateway.
  4. Gateway confirms successful delivery; system logs delivery timestamp.
* **Alternative Scenarios:**
  * WhatsApp fallback to SMS: WhatsApp gateway reports customer unreachable or delivery times out after 60 seconds; system automatically dispatches message via national SMS gateway.
* **Exception and Failure Scenarios:**
  * All channels fail: System retries dispatch with exponential backoff and logs an operational alert.
* **Business Rules:** `BR-NTF-001`.
* **Inputs and Business Information Required:** Event type, recipient phone/email, template parameters (Order ID, tracking URL, OTP code).
* **Expected Business Outcomes:** 99.9% delivery rate for mission-critical transactional alerts.
* **Dependencies:** `DEP-003`, `FEAT-INT-001`.
* **Acceptance Criteria:**
  * **Given** a WhatsApp dispatch that fails or times out after 60 seconds, **When** failure is detected, **Then** the system automatically dispatches the identical notification via SMS within 5 seconds.
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-015](./08-assumptions-dependencies-open-questions.md#oq-015).
* **Related Workflow IDs:** `WF-012`.

---

## 15. Reviews, Ratings & Customer Feedback

### <a id="fr-rev-001"></a>FR-REV-001: Verified-Buyer Product Review Submission & Moderation Lifecycle
* **Requirement ID:** `FR-REV-001`
* **Requirement Title:** Verified-Buyer Review Submission, Rating Governance and Moderation Queue
* **Feature ID:** `FEAT-REV-001`
* **Business Objective:** `OBJ-007`
* **Requirement Statement:** The system must restrict product rating and review submission strictly to verified buyers who have completed a delivered purchase of that SKU, route all submissions through an administrative moderation queue, and compute aggregated star ratings.
* **Description:** Establishes trustworthy social proof while preventing fake reviews, competitor spam, and abusive content.
* **Primary Actor:** `ACT-009` (Customer), `ACT-006` (Customer Support / Moderator)
* **Preconditions:** Customer has an order containing the SKU in `DELIVERED` status.
* **Trigger:** Customer submits star rating (1-5) and written review from order history.
* **Main Success Scenario:**
  1. Customer submits rating, title, review body, and optional product photos.
  2. System verifies customer purchased the item and order is `DELIVERED`.
  3. System flags review as `VERIFIED_PURCHASER` and sets status to `PENDING_MODERATION`.
  4. Moderator reviews content in admin portal and clicks `APPROVED`.
  5. Review publishes to product page and system recalculates aggregate SKU rating.
* **Alternative Scenarios:**
  * Customer updates existing review: Review returns to moderation queue before updated text is published.
* **Exception and Failure Scenarios:**
  * Non-buyer attempts review submission: System blocks submission with error: "Only verified purchasers of this item may submit reviews."
  * Profanity / spam detected: Automated filter flags review as `AUTO_FLAGGED` for priority moderator review or auto-rejection.
* **Business Rules:** `BR-REV-001`.
* **Inputs and Business Information Required:** Order ID, SKU, star rating (1-5), review title, body text, optional photos.
* **Expected Business Outcomes:** Authentic consumer reviews enhancing brand credibility.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-CAT-001`.
* **Acceptance Criteria:**
  * **Given** a customer who has not purchased SKU X, **When** they attempt to submit a review for SKU X, **Then** submission is denied.
  * **Given** a submitted review, **When** approved by a moderator, **Then** it appears on the live PDP and the product's aggregate rating updates.
* **Priority:** `P2`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-012](./08-assumptions-dependencies-open-questions.md#oq-012).
* **Related Workflow IDs:** `WF-004`.

---

## 16. Customer Support & Service Requests

### <a id="fr-sup-001"></a>FR-SUP-001: Integrated Support Ticket Lifecycle Linked to Orders
* **Requirement ID:** `FR-SUP-001`
* **Requirement Title:** Contextual Customer Support Case Management Linked to Orders and Deliveries
* **Feature ID:** `FEAT-SUP-001`
* **Business Objective:** `OBJ-007`
* **Requirement Statement:** The system must allow customers to raise support tickets linked directly to specific Order IDs, consignment tracking numbers, or return requests, enabling support agents to review full operational history and communicate directly through an audited ticketing thread.
* **Description:** Streamlines customer inquiry resolution by providing support operators with instant commercial and fulfillment context.
* **Primary Actor:** `ACT-009` (Customer), `ACT-006` (Customer Support Representative)
* **Preconditions:** Customer is authenticated.
* **Trigger:** Customer clicks "Get Help with Order" or submits inquiry form.
* **Main Success Scenario:**
  1. Customer selects order, chooses issue category (Delivery Delay, Damaged Parcel, Invoicing), and types message.
  2. System generates ticket ID, attaches complete order snapshot and courier tracking history, and assigns to support queue.
  3. Support agent inspects context, enters response or initiates operational action.
  4. Customer receives notification and views reply in support portal.
  5. Ticket is resolved and marked `CLOSED`.
* **Alternative Scenarios:**
  * Agent escalates ticket to Finance or Warehouse supervisor with internal private note.
* **Exception and Failure Scenarios:**
  * SLA breach warning: If ticket remains unassigned $> 4\text{ hours}$, system raises priority to High and alerts support lead.
* **Business Rules:** `BR-SUP-001`.
* **Inputs and Business Information Required:** Customer ID, Order ID (optional), category code, message text, attachments.
* **Expected Business Outcomes:** Faster resolution times (MTTR) and higher customer satisfaction.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-SHP-001`.
* **Acceptance Criteria:**
  * **Given** an open ticket linked to Order 12345, **When** a support agent opens the ticket, **Then** current order status, line items, delivery tracking, and payment history are displayed in the context panel.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-013`.

---

## 17. Analytics, Auditing & Executive Reporting

### <a id="fr-rpt-001"></a>FR-RPT-001: Executive Sales, Margin & Inventory Velocity Reporting
* **Requirement ID:** `FR-RPT-001`
* **Requirement Title:** Executive Commercial Performance, Tax Liability and Inventory Velocity Analytics
* **Feature ID:** `FEAT-RPT-001`
* **Business Objective:** `OBJ-009`
* **Requirement Statement:** The system must aggregate operational and financial data into executive reporting dashboards, providing real-time visibility into Gross Merchandise Value (GMV), net revenue, statutory tax liabilities, return rates, and SKU inventory velocity, with CSV/Excel export capabilities.
* **Description:** Delivers commercial intelligence to leadership for inventory planning, financial reconciliation, and margin optimization.
* **Primary Actor:** `ACT-001` (Business Owner), `ACT-007` (Finance Lead)
* **Preconditions:** Operator has executive reporting permissions.
* **Trigger:** Operator opens reporting dashboard or schedules automated report export.
* **Main Success Scenario:**
  1. Operator selects date range and reporting dimension (Daily, Weekly, Monthly).
  2. System computes GMV, net sales, discounts, shipping collections, and refund deductions.
  3. System displays charts and tabular summaries.
  4. Operator clicks "Export CSV"; system generates downloadable data file.
* **Alternative Scenarios:**
  * Inventory Velocity Report: System calculates days-of-inventory-remaining per SKU based on 30-day run rate.
* **Exception and Failure Scenarios:**
  * High-volume aggregation queries must run against read replicas to prevent performance degradation on primary transactional databases.
* **Business Rules:** `BR-RPT-001`.
* **Inputs and Business Information Required:** Date filter, dimension keys, output format.
* **Expected Business Outcomes:** Data-driven purchasing decisions and clear financial oversight.
* **Dependencies:** `FEAT-ORD-001`, `FEAT-FIN-001`.
* **Acceptance Criteria:**
  * **Given** an export request for Monthly Sales, **When** generated, **Then** Gross Sales minus Discounts minus Refunds equals Net Revenue with 100% mathematical precision.
* **Priority:** `P1`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-018](./08-assumptions-dependencies-open-questions.md#oq-018).
* **Related Workflow IDs:** `WF-014`.

### <a id="fr-aud-001"></a>FR-AUD-001: Immutable Business Audit Trail & Sensitive Action Logging
* **Requirement ID:** `FR-AUD-001`
* **Requirement Title:** Immutable Write-Once Audit Logging for Privileged and Sensitive Business Actions
* **Feature ID:** `FEAT-ADM-002`, `FEAT-AUD-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must capture an immutable, tamper-evident audit record for every sensitive administrative mutation (price changes, manual refunds, inventory overrides, user role edits), recording operator ID, timestamp, IP address, before/after value snapshots, and mandatory business justification codes.
* **Description:** Establishes complete non-repudiation and accountability across all enterprise administrative operations.
* **Primary Actor:** System (Automated), `ACT-002` (System Administrator)
* **Preconditions:** Administrative operator performs a data mutation.
* **Trigger:** Sensitive mutation API request executed.
* **Main Success Scenario:**
  1. Operator submits price override or manual refund.
  2. System captures before state and proposed after state.
  3. System writes structured audit record to append-only, tamper-resistant audit store.
  4. System executes mutation only if audit write succeeds.
* **Alternative Scenarios:**
  * Authorized auditor searches audit log: System provides multi-criteria filtering by operator, SKU, order ID, or date range.
* **Exception and Failure Scenarios:**
  * Attempt to modify or delete audit log: System strictly prohibits modification or deletion (Write-Once, Read-Many / WORM).
* **Business Rules:** `BR-AUD-001`.
* **Inputs and Business Information Required:** Operator ID, role, action code, resource ID, before snapshot, after snapshot, justification note, IP address, timestamp.
* **Expected Business Outcomes:** 100% operational non-repudiation and fraud prevention.
* **Dependencies:** `FEAT-ADM-001`.
* **Acceptance Criteria:**
  * **Given** a price change from ₹10,000 to ₹9,000 by Operator A, **When** executed, **Then** an audit entry is created recording Operator A, old price, new price, and timestamp, which cannot be deleted by any user.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-016`.

### <a id="fr-aud-002"></a>FR-AUD-002: Administrative Audit Trail Export & Tampering Verification
* **Requirement ID:** `FR-AUD-002`
* **Requirement Title:** Cryptographic Audit Trail Export and Hash-Chained Tamper Verification
* **Feature ID:** `FEAT-ADM-002`, `FEAT-AUD-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must provide cryptographic verification of audit log integrity (e.g., cryptographic hash chains or digital signatures) and allow authorized enterprise auditors to export signed audit logs for statutory compliance audits.
* **Description:** Enables independent external auditors to verify that historical operational logs have not been retroactively altered.
* **Primary Actor:** `ACT-001` (Business Owner), `ACT-002` (System Administrator)
* **Preconditions:** Operator has executive compliance audit permissions.
* **Trigger:** Auditor initiates audit export or verification drill.
* **Main Success Scenario:**
  1. Auditor requests verification of audit log block for specified date range.
  2. System recalculates cryptographic hash chain across chronological records.
  3. Hashes match stored signatures; system returns "Audit Integrity Verified: Zero Tampering Detected".
  4. System exports signed archive for regulatory submission.
* **Alternative Scenarios:**
  * Scheduled integrity sweep: System runs nightly verification job and alerts if hash mismatch occurs.
* **Exception and Failure Scenarios:**
  * Hash mismatch detected: System triggers critical security incident alert to Business Owner.
* **Business Rules:** `BR-AUD-001`.
* **Inputs and Business Information Required:** Date range, auditor authorization credentials.
* **Expected Business Outcomes:** Defensible compliance records meeting regulatory evidentiary standards.
* **Dependencies:** `FEAT-AUD-001`.
* **Acceptance Criteria:**
  * **Given** an exported audit log dataset, **When** cryptographic verification is evaluated, **Then** any single altered character causes validation to fail.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-016`.

### <a id="fr-dat-001"></a>FR-DAT-001: Bulk Product Catalog & Inventory Spreadsheet Import/Export
* **Requirement ID:** `FR-DAT-001`
* **Requirement Title:** High-Volume Bulk Catalog and Inventory Spreadsheet Data Exchange
* **Feature ID:** `FEAT-DAT-001`
* **Business Objective:** `OBJ-002`, `OBJ-003`
* **Requirement Statement:** The system must allow Catalog and Inventory Managers to import and export bulk catalog listings, pricing adjustments, and inventory adjustments via standardized CSV/Excel spreadsheets, performing full schema and business rule validation before committing batch transactions.
* **Description:** Accelerates large-scale merchandising operations, allowing staff to update hundreds of SKUs or price points in a single operation.
* **Primary Actor:** `ACT-003` (Catalog Manager), `ACT-004` (Inventory Operator)
* **Preconditions:** Operator has bulk data exchange permissions.
* **Trigger:** Operator uploads spreadsheet or requests catalog export.
* **Main Success Scenario:**
  1. Operator uploads CSV with 500 SKU updates (SKU, Selling Price, On-Hand Stock).
  2. System validates row data formats, SKU existence, and business rules (e.g., Selling Price <= MRP).
  3. Validation passes; system applies updates in an atomic batch and logs audit entries.
  4. System provides summary report: "500 rows processed, 500 successful, 0 errors."
* **Alternative Scenarios:**
  * Partial row errors: System rejects invalid rows, applies valid rows (or rolls back entire batch based on operator selection), and returns an annotated error spreadsheet.
* **Exception and Failure Scenarios:**
  * Corrupted file structure: System rejects file immediately without processing any mutations.
* **Business Rules:** `BR-CAT-001`, `BR-PRC-001`, `BR-INV-001`.
* **Inputs and Business Information Required:** CSV/XLSX file, batch operation type (Catalog, Price, Inventory).
* **Expected Business Outcomes:** Operational efficiency in catalog maintenance without sacrificing data validation rigor.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-INV-001`.
* **Acceptance Criteria:**
  * **Given** an uploaded spreadsheet where row 10 has Selling Price > MRP, **When** bulk import validation runs, **Then** row 10 is flagged with an explicit pricing rule error.
* **Priority:** `P1`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-019](./08-assumptions-dependencies-open-questions.md#oq-019).
* **Related Workflow IDs:** `WF-002`.

---

## 18. Platform Security, Operational Resilience & Continuity

### <a id="fr-sec-001"></a>FR-SEC-001: Sensitive Credential Protection and Payment Tokenization
* **Requirement ID:** `FR-SEC-001`
* **Requirement Title:** Zero Cardholder Storage and Cryptographic Credential Protection
* **Feature ID:** `FEAT-SEC-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must strictly prohibit storing, processing, or transmitting raw credit/debit card numbers (PANs) or CVVs, delegating card data capture to certified third-party payment gateways, and must store all user passwords using salted cryptographic hashes (bcrypt or Argon2id).
* **Description:** Protects the enterprise against cardholder data compromise and isolates platform infrastructure from PCI-DSS Level 1 scope.
* **Primary Actor:** System (Automated Security Architecture)
* **Preconditions:** System receives customer authentication or checkout request.
* **Trigger:** Customer registers, logs in, or submits payment.
* **Main Success Scenario:**
  1. Customer enters card details on gateway-hosted iframe / checkout interface.
  2. Gateway tokenizes card and returns non-sensitive token to Chellaa platform.
  3. Chellaa platform stores only token and truncated last-4 digits for display.
  4. Passwords hashed with unique cryptographic salt before persistence.
* **Alternative Scenarios:**
  * Customer changes password: Old password hash replaced with newly derived hash.
* **Exception and Failure Scenarios:**
  * Raw PAN detected in incoming payload: System rejects request immediately, masks payload, and triggers high-severity security alert.
* **Business Rules:** `BR-SEC-001`, `BR-SEC-002`.
* **Inputs and Business Information Required:** Gateway payment token, password string.
* **Expected Business Outcomes:** Zero cardholder liability and robust protection against credential theft.
* **Dependencies:** `DEP-001`, `ASM-008`.
* **Acceptance Criteria:**
  * **Given** an audit of application databases and server logs, **When** searched for credit card PAN formats, **Then** zero raw card numbers or CVVs exist.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-001`, `WF-007`.

### <a id="fr-sec-002"></a>FR-SEC-002: Brute-Force Rate Limiting & Account Lockout Defense
* **Requirement ID:** `FR-SEC-002`
* **Requirement Title:** Automated Brute-Force Protection, API Rate Limiting and Account Lockout Governance
* **Feature ID:** `FEAT-SEC-001`
* **Business Objective:** `OBJ-008`
* **Requirement Statement:** The system must enforce automated rate limiting on public authentication and search endpoints, and temporarily lock any customer or operator account after 5 consecutive failed login attempts, requiring self-service OTP verification or administrator unlock to restore access.
* **Description:** Defends customer accounts and platform infrastructure against automated credential stuffing, brute-force password cracking, and scraping bots.
* **Primary Actor:** System (Automated Threat Defense)
* **Preconditions:** User submits login attempt.
* **Trigger:** Consecutive failed authentication credentials submitted.
* **Main Success Scenario:**
  1. User enters incorrect password.
  2. System increments failed attempt counter for account and IP address.
  3. If counter reaches 5: System locks account for 30 minutes, invalidates counter, and sends security alert to registered phone/email.
* **Alternative Scenarios:**
  * Successful login resets failed attempt counter to zero.
  * Customer unlocks account early by completing SMS OTP verification.
* **Exception and Failure Scenarios:**
  * IP-based DDoS or scraping burst: Gateway rate limiter returns HTTP 429 Too Many Requests to abusive IP addresses.
* **Business Rules:** `BR-SEC-002`.
* **Inputs and Business Information Required:** User identity, client IP address, user agent, attempt count.
* **Expected Business Outcomes:** Zero compromised customer accounts from automated brute-force attacks.
* **Dependencies:** `FEAT-SEC-001`, `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** an account with 4 failed login attempts, **When** a 5th incorrect password is submitted, **Then** the account is locked and all subsequent login attempts within 30 minutes are blocked.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-001`.

### <a id="fr-int-001"></a>FR-INT-001: Integration Circuit Breaking and Provider Outage Isolation
* **Requirement ID:** `FR-INT-001`
* **Requirement Title:** Integration Circuit Breaking, Timeout Budgets and Fault Isolation
* **Feature ID:** `FEAT-INT-001`
* **Business Objective:** `OBJ-010`
* **Requirement Statement:** The system must wrap all synchronous external service integrations (payment gateways, courier APIs, messaging gateways) with circuit breaker patterns and strict timeout budgets ($\le 3\text{ seconds}$), preventing downstream partner outages from exhausting platform server threads or crashing core commerce funnels.
* **Description:** Ensures platform resilience and fault containment when external SaaS providers suffer degradation or downtime.
* **Primary Actor:** System (Automated Integration Architecture)
* **Preconditions:** External integration call executed.
* **Trigger:** Downstream provider returns timeouts or 5xx server errors.
* **Main Success Scenario:**
  1. Circuit breaker monitors external call error rates.
  2. When error rate exceeds 50% over rolling window, circuit trips to `OPEN`.
  3. Subsequent requests fail fast immediately without waiting for timeouts.
  4. System executes graceful fallback (e.g., switches to secondary courier or queues notification).
  5. System periodically tests downstream health via `HALF_OPEN` probes.
* **Alternative Scenarios:**
  * Provider recovers: Probes succeed; circuit transitions back to `CLOSED`.
* **Exception and Failure Scenarios:**
  * Payment gateway outage: Circuit trips, checkout alerts customer to select alternative payment method or try again shortly.
* **Business Rules:** `BR-INT-001`.
* **Inputs and Business Information Required:** Provider endpoint, timeout threshold, error rate threshold.
* **Expected Business Outcomes:** Uninterrupted customer shopping during third-party service provider outages.
* **Dependencies:** `DEP-001`, `DEP-002`, `DEP-003`.
* **Acceptance Criteria:**
  * **Given** an external courier API experiencing 100% latency $> 5$ seconds, **When** 5 consecutive requests fail, **Then** the circuit breaker trips open, failing subsequent requests in $< 10\text{ ms}$ with fallback routing.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-015`.

### <a id="fr-ops-001"></a>FR-OPS-001: Platform Distributed Telemetry & Health Alerting
* **Requirement ID:** `FR-OPS-001`
* **Requirement Title:** Distributed Request Correlation Tracing and Real-Time Operational Alerting
* **Feature ID:** `FEAT-OPS-001`
* **Business Objective:** `OBJ-010`
* **Requirement Statement:** The system must propagate an immutable, globally unique Trace ID across all synchronous API requests and asynchronous event messages, exposing standardized health endpoints and operational metrics to trigger automated alerts when system error rates exceed 2%.
* **Description:** Enables instant end-to-end diagnostics and proactive detection of technical or commercial anomalies.
* **Primary Actor:** System (Automated Telemetry Framework), `ACT-002` (System Administrator)
* **Preconditions:** Inbound request arrives at platform gateway.
* **Trigger:** HTTP request received or scheduled background job started.
* **Main Success Scenario:**
  1. Gateway generates unique Trace ID and injects it into request context headers.
  2. All downstream services log operations using structured JSON with the Trace ID.
  3. When an exception occurs, SRE team queries centralized logs by Trace ID to see the exact sequence of events across services within seconds.
  4. Metric scraper monitors error rates; when error rate is normal ($< 2\%$), system operates in green state.
* **Alternative Scenarios:**
  * Error rate exceeds threshold: System dispatches automated PagerDuty / Slack alert to on-call engineer within 60 seconds.
* **Exception and Failure Scenarios:**
  * Logging pipeline backpressure: Asynchronous buffers prevent telemetry logging from slowing down customer transactions.
* **Business Rules:** `BR-OPS-001`.
* **Inputs and Business Information Required:** Trace ID, service name, timestamp, log level, message, duration.
* **Expected Business Outcomes:** Mean-Time-to-Detect (MTTD) $< 1$ minute and rapid incident triage.
* **Dependencies:** `FEAT-OPS-001`.
* **Acceptance Criteria:**
  * **Given** an end-to-end checkout transaction, **When** querying centralized logging for its Trace ID, **Then** 100% of involved service log records are returned in chronological sequence.
* **Priority:** `P0`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** See [OQ-022](./08-assumptions-dependencies-open-questions.md#oq-022).
* **Related Workflow IDs:** `WF-015`.

### <a id="fr-rel-001"></a>FR-REL-001: Automated Release Verification & Quality Gates
* **Requirement ID:** `FR-REL-001`
* **Requirement Title:** Automated CI/CD Quality Gates and Zero-Downtime Deployment Verification
* **Feature ID:** `FEAT-REL-001`
* **Business Objective:** `OBJ-010`
* **Requirement Statement:** The system must enforce automated build verification quality gates (100% unit test pass rate, $\ge 80\%$ test coverage, zero critical security vulnerabilities) and support zero-downtime rolling production deployments without dropping active customer shopping sessions.
* **Description:** Eliminates deployment-induced downtime and protects production stability through automated quality gates.
* **Primary Actor:** System (Automated CI/CD Pipeline), `ACT-002` (System Administrator)
* **Preconditions:** Code commit submitted to repository branch.
* **Trigger:** Pull request merge or release tag created.
* **Main Success Scenario:**
  1. Pipeline executes static analysis, unit tests, integration tests, and security scans.
  2. All quality gates pass.
  3. Pipeline deploys new version using rolling update or blue-green strategy.
  4. Platform drains connections gracefully from old instances and routes traffic to verified healthy instances.
  5. Deployment completes with zero customer HTTP 5xx errors.
* **Alternative Scenarios:**
  * Health check failure during deployment: Automated rollback triggers immediately, reverting traffic to previous stable version.
* **Exception and Failure Scenarios:**
  * Quality gate failure: Pipeline terminates build, blocks deployment, and alerts engineering team.
* **Business Rules:** `BR-REL-001`.
* **Inputs and Business Information Required:** Build artifact, test results, vulnerability scan report.
* **Expected Business Outcomes:** Frequent, reliable deployments without customer shopping disruptions.
* **Dependencies:** `FEAT-REL-001`.
* **Acceptance Criteria:**
  * **Given** an active customer checkout session during a production release, **When** deployment executes, **Then** the customer transaction completes successfully with zero dropped connections.
* **Priority:** `P1`
* **Requirement Status:** `CONFIRMED`
* **Open Questions:** See [OQ-021](./08-assumptions-dependencies-open-questions.md#oq-021).
* **Related Workflow IDs:** None.

### <a id="fr-bcm-001"></a>FR-BCM-001: Automated Backup Scheduling & Point-in-Time Recovery Verification
* **Requirement ID:** `FR-BCM-001`
* **Requirement Title:** Automated Continuous Database Backups and Point-in-Time Recovery
* **Feature ID:** `FEAT-BCM-001`
* **Business Objective:** `OBJ-010`
* **Requirement Statement:** The system must execute continuous automated database backups with write-ahead log archiving to achieve a Recovery Point Objective (RPO) $\le 5\text{ minutes}$, and maintain tested automated restoration procedures supporting a Recovery Time Objective (RTO) $\le 2\text{ hours}$ in disaster recovery scenarios.
* **Description:** Guarantees business survival and data restoration in the event of catastrophic data corruption or cloud provider data center failure.
* **Primary Actor:** System (Automated Backup Architecture), `ACT-002` (System Administrator)
* **Preconditions:** Database persistence clusters active.
* **Trigger:** Scheduled backup timer and continuous transaction log shipping.
* **Main Success Scenario:**
  1. System takes daily full snapshots and continuously ships write-ahead transaction logs to geo-redundant storage.
  2. Backups are encrypted at rest with AES-256.
  3. Scheduled automated restore drill restores database snapshot to isolated staging cluster and executes data consistency check.
  4. System confirms restore completed within 2 hours with data loss $< 5$ minutes.
* **Alternative Scenarios:**
  * Catastrophic failover: Infrastructure team triggers disaster recovery automation; platform provisions secondary region and restores data within 2 hours.
* **Exception and Failure Scenarios:**
  * Backup failure: Automated monitoring alerts SRE team within 15 minutes of missed backup.
* **Business Rules:** `BR-BCM-001`.
* **Inputs and Business Information Required:** Database transaction logs, snapshot ID, target restoration timestamp.
* **Expected Business Outcomes:** Guaranteed data protection and enterprise business continuity.
* **Dependencies:** `FEAT-BCM-001`.
* **Acceptance Criteria:**
  * **Given** a catastrophic database failure occurring at 14:00, **When** point-in-time recovery is performed, **Then** all transactions committed up to at least 13:55 are fully recovered (RPO $\le 5$ min) within 120 minutes (RTO $\le 2$ hr).
* **Priority:** `P0`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-017](./08-assumptions-dependencies-open-questions.md#oq-017).
* **Related Workflow IDs:** None.

---

## 19. Advanced Merchandising (Post-Launch Capabilities)

### <a id="fr-adv-001"></a>FR-ADV-001: Electronics Product Bundles & Accessory Kit Pricing
* **Requirement ID:** `FR-ADV-001`
* **Requirement Title:** Electronics Product Bundles and Dynamic Accessory Kit Discounting
* **Feature ID:** `FEAT-ADV-001`
* **Business Objective:** `OBJ-001`, `OBJ-002`
* **Requirement Statement:** The system must allow Catalog Managers to configure product accessory bundles (e.g., Smartphone + 65W Fast Charger + Protective Case) with discounted bundle pricing, dynamically verifying stock availability for all constituent items before permitting bundle checkout.
* **Description:** Increases Average Order Value (AOV) by encouraging consumers to purchase essential accessories with core electronics.
* **Primary Actor:** `ACT-003` (Catalog Manager), `ACT-009` (Customer)
* **Preconditions:** Individual products exist in catalog.
* **Trigger:** Catalog Manager creates bundle listing, or customer adds bundle to cart.
* **Main Success Scenario:**
  1. Manager defines bundle components and bundle selling price.
  2. Storefront displays bundle deal on product details page.
  3. Customer clicks "Add Bundle to Cart".
  4. System verifies Available-to-Sell stock for all bundle constituent SKUs.
  5. System reserves inventory for each constituent SKU atomically and adds bundle to cart with pro-rata discount breakdown.
* **Alternative Scenarios:**
  * One bundle component out of stock: System disables bundle purchase and displays "Bundle Unavailable - [Component Name] Out of Stock".
* **Exception and Failure Scenarios:**
  * Return of bundle item: Customer returns one item from bundle; refund calculated based on pro-rata bundle discount, preventing discount exploitation.
* **Business Rules:** `BR-ADV-001`, `BR-INV-001`, `BR-RET-003`.
* **Inputs and Business Information Required:** Bundle ID, constituent SKU list, bundle price, individual discount allocations.
* **Expected Business Outcomes:** Increased basket size and enhanced accessory sales velocity.
* **Dependencies:** `FEAT-CAT-001`, `FEAT-INV-001`.
* **Acceptance Criteria:**
  * **Given** a bundle composed of Item X (stock = 5) and Item Y (stock = 0), **When** a shopper views the bundle, **Then** the bundle buy button is disabled due to Item Y stock exhaustion.
* **Priority:** `P3`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** See [OQ-014](./08-assumptions-dependencies-open-questions.md#oq-014).
* **Related Workflow IDs:** `WF-002`, `WF-005`.

### <a id="fr-adv-002"></a>FR-ADV-002: Customer Back-in-Stock Notification Alerts
* **Requirement ID:** `FR-ADV-002`
* **Requirement Title:** Automated Customer Back-in-Stock Alerts upon Warehouse Replenishment
* **Feature ID:** `FEAT-ADV-002`
* **Business Objective:** `OBJ-001`, `OBJ-003`
* **Requirement Statement:** The system must allow shoppers to register for automated back-in-stock notifications on sold-out SKUs, and automatically dispatch transactional email/SMS/WhatsApp alerts to registered shoppers when new inventory is inwarded at the warehouse.
* **Description:** Recovers demand for out-of-stock electronics by notifying interested customers immediately upon replenishment.
* **Primary Actor:** `ACT-009` (Customer), System (Automated)
* **Preconditions:** Product variant is out of stock (`ATS = 0`).
* **Trigger:** Customer submits "Notify Me When Available" request, and warehouse later inwards stock.
* **Main Success Scenario:**
  1. Shopper submits email or phone number on out-of-stock PDP.
  2. System records subscription request linked to customer and SKU ID.
  3. Warehouse operator inwards new inventory stock for that SKU (`ATS > 0`).
  4. System notification batcher triggers back-in-stock alerts to subscribers with direct purchase link.
  5. System marks notification requests as fulfilled.
* **Alternative Scenarios:**
  * Low replenishment volume: If only 2 units are inwarded and 50 shoppers subscribed, system notifies first 5 subscribers (FIFO queue) to prevent customer frustration.
* **Exception and Failure Scenarios:**
  * Customer unsubscribes prior to replenishment: System removes notification record.
* **Business Rules:** `BR-ADV-002`, `BR-NTF-001`.
* **Inputs and Business Information Required:** Customer contact info, SKU ID, timestamp.
* **Expected Business Outcomes:** Immediate sales velocity upon restocking and improved customer satisfaction.
* **Dependencies:** `FEAT-INV-001`, `FEAT-NTF-001`.
* **Acceptance Criteria:**
  * **Given** a subscribed customer for SKU Z, **When** warehouse inwards 10 units of SKU Z, **Then** a back-in-stock alert is dispatched to the customer within 60 seconds.
* **Priority:** `P2`
* **Requirement Status:** `PROPOSED`
* **Open Questions:** None.
* **Related Workflow IDs:** `WF-003`, `WF-012`.
