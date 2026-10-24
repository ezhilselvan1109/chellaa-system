# Generic Fulfilment Data Model and API Contracts

**Document ID:** ARCH-DEP-002-GENERIC-DRAFT  
**Status:** DRAFT_COMPLETE_PENDING_REVIEW  
**Governance:** Proposed design only. Not approved architecture. DEP-002 remains OPEN; Phase 1 review remains PENDING_APPROVAL; requirements baseline remains NOT_APPROVED; architecture approval remains NOT_GRANTED.  
**Scope:** Location-agnostic contracts for Warehouse, Inventory, Serviceability, Fulfilment Routing, and Shipment Provider.  
**Explicit non-goal:** No city, state, warehouse address, postal/PIN code, courier, aggregator, provider contract, or live integration is selected or assumed.

## 1. Design principles

1. **Configuration, not hard-coding:** locations, warehouses, service areas, providers, carrier capabilities, routing priorities, and rollout states are controlled configuration.
2. **Canonical internal identifiers:** Chellaa-owned IDs identify warehouses, inventory, fulfilment decisions, and shipments. External AWB, booking IDs, and provider shipment IDs remain secondary references.
3. **One internal shipment lifecycle:** own delivery, directly integrated courier partners, and aggregators map to common internal statuses and normalized events.
4. **Explicit eligibility:** only active warehouses, available inventory, validated serviceability, enabled provider lanes, and policy-compliant routes can be selected.
5. **Auditable decisions:** persist inputs, configuration versions, candidates, selected route, reason codes, and timestamps.
6. **Safe failure:** if no eligible route exists, do not promise delivery or create a shipment booking. Return a typed no-route outcome.
7. **Idempotent external effects:** booking, cancellation, tracking events, and COD remittance updates must be idempotent and deduplicated.
8. **Privacy and security:** minimize logging of recipient addresses, phone numbers, OTPs, payment credentials, and provider payloads. Store secrets in a secret manager.
9. **Versioned evolution:** API schemas and routing configuration are versioned. Orders and shipments retain the decision snapshot used at the time.
10. **No false readiness:** configuration alone does not prove operational readiness; activation requires evidence and authorized approval.

## 2. Logical ownership and boundaries

| Capability | Owns | Must not own |
|---|---|---|
| Warehouse Registry | Warehouse identity, protected address reference, status, capabilities, operating windows, handling limits | Stock quantities or provider tracking |
| Inventory | Quantities by warehouse and sellable SKU, stock status, reservations and releases | Delivery promises or carrier booking |
| Serviceability | Versioned destination coverage and lane eligibility for origin, destination, mode, and provider | Customer promise without routing/SLA calculation |
| Fulfilment Routing | Candidate evaluation, allocation policy, route decision and decision snapshot | Provider-specific protocol details |
| Shipment Provider Adapter | Provider connection, secret reference, capability mapping, booking/cancel/track and webhook normalization | Canonical order or inventory truth |
| Shipment Lifecycle | Canonical Shipment ID, shipment status, timeline, delivery proof references and exceptions | Provider-specific raw status as the sole source of truth |

These may initially be modules within one deployment or separate services later. This document does not mandate deployment topology.

## 3. Canonical identifiers and common conventions

- warehouseId, inventoryRecordId, reservationId, serviceabilityRuleId, providerId, providerConnectionId, fulfilmentDecisionId, and shipmentId are opaque stable IDs. Never encode a city or address into an ID.
- skuId or variantId is the catalogue-owned inventory key. Select and document one canonical key before implementation.
- providerShipmentRef and trackingNumber are external references, unique only within the provider connection unless the provider guarantees global uniqueness.
- Timestamps use ISO 8601 UTC instants. Operating windows include an IANA time-zone identifier.
- Money uses integer minor units plus ISO currency code; never use floating-point monetary values. Example: amountMinor 5000, currency INR.
- Quantities are non-negative integers unless catalogue policy explicitly supports fractional units.
- Mutable resources expose createdAt, updatedAt, and a version or ETag.
- List APIs use cursor pagination and stable sorting.
- Error envelope: { "code": "...", "message": "...", "requestId": "...", "details": {} }. Never expose secrets or sensitive provider payloads.

## 4. Generic data model

### 4.1 Warehouse

A warehouse is a fulfilment origin or stock-holding/dispatch node. The model supports any number of nodes and has no fixed geographic footprint.

| Field | Type | Required | Meaning |
|---|---|---:|---|
| warehouseId | string | yes | Chellaa canonical ID |
| code | string | yes | Unique operational code |
| displayName | string | yes | Admin-facing label |
| status | enum | yes | DRAFT, ACTIVE, PAUSED, CLOSED |
| addressRef | string | yes for dispatch | Reference to protected address record |
| geoPoint | object/null | no | Optional coordinates for estimates; never sole proof of serviceability |
| timeZone | string | yes | IANA time zone |
| capabilities | string[] | yes | e.g. STORAGE, PICK_PACK, RETURNS, COD_HANDOVER |
| operatingWindows | object[] | no | Day/time windows and exceptions |
| handlingCapacity | object | no | Configurable capacity units and limits |
| supportedModes | string[] | yes | OWN_DELIVERY, DIRECT_COURIER, AGGREGATOR |
| activationEvidence | object | required for ACTIVE | Checklist version, evidence references, validator and timestamp |
| version | integer | yes | Optimistic concurrency version |
| createdAt, updatedAt | timestamp | yes | Audit timestamps |

**Rules**
- Status changes never erase inventory or historical shipments.
- PAUSED or CLOSED warehouses cannot receive new reservations or routing allocations unless a separately approved exception exists.
- Deactivation must account for open reservations, unfulfilled orders, returns, and stock transfers/adjustments.
- Validate the actual address and serviceability before dispatch activation. Never infer serviceability from a warehouse name or city label.

### 4.2 Inventory

Inventory is keyed by warehouse and sellable catalogue unit. Keep physical stock distinct from sellable/available stock.

| Field | Type | Required | Meaning |
|---|---|---:|---|
| inventoryRecordId | string | yes | Canonical record ID |
| warehouseId | string | yes | Warehouse reference |
| skuId | string | yes | Sellable SKU/variant key |
| onHand | integer | yes | Physically recorded quantity |
| reserved | integer | yes | Quantity held by active reservations |
| allocated | integer | yes | Quantity committed to fulfilment/picking |
| damaged | integer | yes | Non-sellable damaged stock |
| safetyStock | integer | yes | Policy-protected quantity |
| version | integer | yes | Concurrency version |
| updatedAt | timestamp | yes | Last update |

Proposed initial derived formula:

    availableToPromise = max(0, onHand - reserved - allocated - damaged - safetyStock)

The treatment of quarantine, returns pending inspection, and inbound stock remains an open domain decision; do not count them as sellable by default.

**Reservation record**
- reservationId, orderId, warehouseId, items (skuId and quantity), status (HELD, COMMITTED, RELEASED, EXPIRED), expiresAt, idempotencyKey, version, and audit timestamps.
- Reserve required line quantities atomically for an allocation attempt or return a typed insufficient-stock result.
- Reservation expiry and release must be idempotent.
- Stock changes use append-only adjustment/movement records with reason, actor/source, correlation ID, and timestamp. Avoid silent quantity overwrites.

### 4.3 Serviceability

Serviceability determines whether a destination can be served from an origin through a fulfilment mode/provider lane under a ruleset. It is not merely a static location list.

| Field | Type | Required | Meaning |
|---|---|---:|---|
| serviceabilityRuleId | string | yes | Stable rule ID |
| version | integer | yes | Rule revision |
| status | enum | yes | DRAFT, VALIDATING, ACTIVE, SUSPENDED, RETIRED |
| originScope | object | yes | Warehouse ID or configured origin group |
| destinationScope | object | yes | Normalized postal, administrative, or geographic scope; country-specific and configurable |
| fulfilmentMode | enum | yes | OWN_DELIVERY, DIRECT_COURIER, AGGREGATOR |
| providerConnectionId | string/null | conditional | Required for provider-lane-specific rule |
| productConstraints | object | no | Weight, dimensions, category/restriction constraints |
| paymentConstraints | object | no | COD/online eligibility referencing existing payment policy |
| serviceLevel | object | no | Estimate/SLA class; not a guarantee unless configured |
| effectiveFrom, effectiveTo | timestamp/null | no | Rule validity window |
| validationEvidence | object | required to activate | Actual origin/address and provider coverage checks, test reference, validator and timestamp |
| createdAt, updatedAt | timestamp | yes | Audit and concurrency |

**Rules**
- Use a normalized destination object with country-specific adapters. Do not embed a fixed postal-code list in application code.
- Destination coverage and provider serviceability are separate checks.
- Activate a rule only after the actual origin/address and provider coverage are validated.
- Cache decisions with bounded TTL and rule/configuration version. Suspensions must invalidate or bypass stale positive cache entries.
- Return decision, reasonCodes, ruleVersion, and checkedAt, not just a boolean.

### 4.4 Fulfilment Routing

A fulfilment decision selects a warehouse, inventory allocation, mode, provider connection if relevant, and service level for an order.

| Field | Type | Required | Meaning |
|---|---|---:|---|
| fulfilmentDecisionId | string | yes | Canonical decision ID |
| orderId | string | yes | Order reference |
| status | enum | yes | EVALUATING, SELECTED, NO_ROUTE, RESERVATION_FAILED, CANCELLED |
| items | object[] | yes | SKU and required quantity |
| destinationRef | string | yes | Protected destination address reference |
| paymentMethod | enum | yes | Existing payment policy value |
| candidates | object[] | yes | Candidate origin/lane, eligibility, score and reason codes |
| selectedWarehouseId | string/null | conditional | Selected origin |
| selectedMode | enum/null | conditional | Own delivery, direct courier, or aggregator |
| selectedProviderConnectionId | string/null | conditional | Null where own delivery requires no provider connection |
| inventoryReservationIds | string[] | conditional | Reservations held by decision |
| routingPolicyVersion | string | yes | Exact policy/config version |
| serviceabilityRuleVersions | object[] | yes | Rule IDs and versions consulted |
| estimatedDispatchAt, estimatedDeliveryAt | timestamp/null | no | Estimates, not guarantees unless policy defines otherwise |
| reasonCodes | string[] | yes | Decision rationale |
| decidedAt | timestamp/null | conditional | Decision time |
| version | integer | yes | Concurrency |

**Proposed routing stages**
1. Validate order state, destination reference, payment/COD eligibility, product restrictions, and quantities.
2. Discover active warehouses with required handling capabilities.
3. Check inventory and attempt time-bounded reservations using an idempotency key.
4. Evaluate serviceability for each warehouse, mode, and provider lane.
5. Exclude suspended/unvalidated lanes and lanes failing COD, product, size, or weight constraints.
6. Rank remaining candidates using a versioned policy: inventory completeness, service level, capacity, total cost, reliability, and configurable business priority. Weights and tie-breaks require owner approval.
7. Commit the selected reservation and persist an immutable decision snapshot. Release non-selected reservations.
8. Create the canonical shipment and call the selected adapter asynchronously/idempotently.
9. On booking failure, retry by error classification. Reroute only under an explicit policy that safely releases/transfers inventory and records a new decision version.
10. If no candidate remains, return NO_ROUTE with reason codes; do not manufacture an ETA or booking.

Split shipments, partial fulfilment, backorders, inventory transfers, scoring weights, and reroute authority remain open decisions. Do not silently promise single-warehouse fulfilment if no warehouse can fulfil all lines.

### 4.5 Shipment Provider and Provider Connection

Separate provider identity from a configured connection/contract and from a shipment booking.

**Provider**
- providerId, displayName, providerType (OWN_FLEET, DIRECT_COURIER, AGGREGATOR), status, capabilities, adapterType, supportedEventSchemaVersion, and audit fields.
- This neutral record does not imply that any real provider is selected.

**Provider connection**
- providerConnectionId, providerId, environment (SANDBOX, PRODUCTION), status (DRAFT, VALIDATING, ACTIVE, SUSPENDED, RETIRED), credentialSecretRef, contractRef, serviceabilityConfigVersion, rateCardVersion, webhookConfigRef, capabilities, validationEvidence, and version.
- Never store credentials in plain text; store secret-manager references.
- Production activation requires contract/operational readiness, credentials, webhook authentication, booking/cancel/tracking tests, duplicate-event tests, reconciliation tests, and explicit approval.

**Provider booking reference**
- shipmentId, providerConnectionId, providerShipmentRef, trackingNumber, bookingStatus, requestId, idempotencyKey, bookedAt, lastSyncedAt, restricted rawStatusCode, normalizedStatus, and version.
- Store raw provider payloads only when needed, with access controls, retention limits, redaction, and payload-size limits.

**Canonical shipment status proposal**

    CREATED → READY_FOR_HANDOVER → BOOKING_PENDING → BOOKED → PICKED_UP → IN_TRANSIT → OUT_FOR_DELIVERY → DELIVERED

Exception states include BOOKING_FAILED, PICKUP_FAILED, DELIVERY_ATTEMPT_FAILED, DELIVERY_EXCEPTION, RETURN_TO_ORIGIN, CANCEL_PENDING, CANCELLED, LOST, and DAMAGED. Represent reverse logistics as a linked return shipment or explicit reverse leg, not by overwriting forward shipment history. Define a state-transition matrix before implementation.

**Provider events**
- Normalize providerEventId, providerConnectionId, shipmentId, providerShipmentRef, eventType, occurredAt, receivedAt, optional locationRef, reasonCode, deduplicationKey, payloadHash, and schemaVersion.
- Authenticate webhooks, validate signatures/timestamps, deduplicate on provider-specific event identity plus connection, and tolerate out-of-order delivery.
- An event must not regress canonical shipment state unless an explicit correction workflow permits it.
- COD collected, remitted, and reconciled are separately auditable financial events. Delivery status alone never implies COD remittance.

## 5. API contracts (proposed REST, versioned under /api/v1)

These are logical contracts, not existing endpoints. Writes require authentication/authorization, request IDs, validation, audit logging, and idempotency/concurrency controls.

### 5.1 Warehouse Registry

- POST /api/v1/warehouses — create a draft warehouse.
- GET /api/v1/warehouses/{warehouseId} — retrieve details.
- GET /api/v1/warehouses?status=ACTIVE&cursor=... — list/filter.
- PATCH /api/v1/warehouses/{warehouseId} — update permitted metadata using If-Match/version.
- POST /api/v1/warehouses/{warehouseId}/validate — record readiness checks; does not auto-approve production activation.
- POST /api/v1/warehouses/{warehouseId}/status-transitions — guarded transition with reason/evidence.
- GET /api/v1/warehouses/{warehouseId}/capabilities — handling and mode capabilities.

Example request (illustrative identifiers only):
    {
      "code": "NODE-001",
      "displayName": "Fulfilment Node 001",
      "addressRef": "addr_ref_opaque",
      "timeZone": "Asia/Kolkata",
      "capabilities": ["STORAGE", "PICK_PACK"],
      "supportedModes": ["DIRECT_COURIER", "AGGREGATOR"]
    }

### 5.2 Inventory

- GET /api/v1/inventory?warehouseId=...&skuId=... — query stock and availability.
- POST /api/v1/inventory/reservations — reserve stock; requires Idempotency-Key.
- GET /api/v1/inventory/reservations/{reservationId} — retrieve reservation.
- POST /api/v1/inventory/reservations/{reservationId}/commit — commit against a decision.
- POST /api/v1/inventory/reservations/{reservationId}/release — release held stock.
- POST /api/v1/inventory/adjustments — append a reason-coded adjustment with permission controls.
- GET /api/v1/inventory/movements?warehouseId=...&skuId=... — auditable movement history.

Reservation request:
    {
      "orderId": "ord_opaque",
      "warehouseId": "wh_opaque",
      "items": [{"skuId": "sku_opaque", "quantity": 2}],
      "expiresAt": "2026-10-24T12:00:00Z",
      "idempotencyKey": "order-alloc-attempt-opaque"
    }

Return 201 for a new reservation, 200 for replay of the same idempotent request, and typed 409 for insufficient stock or conflicting reuse of a key.

### 5.3 Serviceability

- POST /api/v1/serviceability/checks — check destination against origin/mode/provider candidates.
- POST /api/v1/serviceability/rules — create a draft rule.
- GET /api/v1/serviceability/rules/{ruleId} — retrieve rule and version.
- PATCH /api/v1/serviceability/rules/{ruleId} — revise draft or create a new version; active versions should be immutable.
- POST /api/v1/serviceability/rules/{ruleId}/validate — attach validation evidence.
- POST /api/v1/serviceability/rules/{ruleId}/activate — guarded activation after evidence and authorized approval.
- POST /api/v1/serviceability/rules/{ruleId}/suspend — suspend with reason.

Check request:
    {
      "destination": {
        "countryCode": "IN",
        "postalCode": "POSTAL_CODE_PENDING_VALIDATION"
      },
      "items": [{"skuId": "sku_opaque", "quantity": 1}],
      "paymentMethod": "ONLINE",
      "candidates": [
        {"warehouseId": "wh_opaque", "fulfilmentMode": "DIRECT_COURIER", "providerConnectionId": "pc_opaque"}
      ]
    }

This is a schema example only. The postal code is a placeholder, not a validated service area.

Response shape:
    {
      "results": [{
        "warehouseId": "wh_opaque",
        "fulfilmentMode": "DIRECT_COURIER",
        "providerConnectionId": "pc_opaque",
        "decision": "ELIGIBLE",
        "reasonCodes": [],
        "ruleVersions": [{"serviceabilityRuleId": "sr_opaque", "version": 3}],
        "checkedAt": "2026-10-24T10:00:00Z"
      }],
      "configurationVersion": "svc-config-vN"
    }

### 5.4 Fulfilment Routing

- POST /api/v1/fulfilment/decisions — evaluate and persist a routing decision.
- GET /api/v1/fulfilment/decisions/{decisionId} — retrieve snapshot and reason codes.
- POST /api/v1/fulfilment/decisions/{decisionId}/confirm — confirm selected reservation/route if a two-step workflow is used.
- POST /api/v1/fulfilment/decisions/{decisionId}/cancel — cancel and release held reservations when safe.
- POST /api/v1/fulfilment/decisions/{decisionId}/reroute — controlled rerouting with reason and idempotency key.
- GET /api/v1/fulfilment/policies/{policyId}/versions/{version} — read immutable policy version.

Decision request:
    {
      "orderId": "ord_opaque",
      "destinationRef": "addr_ref_opaque",
      "paymentMethod": "COD",
      "items": [{"skuId": "sku_opaque", "quantity": 1}],
      "constraints": {
        "allowSplitShipment": false,
        "requiredCapabilities": ["PICK_PACK"]
      },
      "idempotencyKey": "order-fulfilment-attempt-opaque"
    }

Decision response:
    {
      "fulfilmentDecisionId": "fd_opaque",
      "status": "SELECTED",
      "selectedWarehouseId": "wh_opaque",
      "selectedMode": "DIRECT_COURIER",
      "selectedProviderConnectionId": "pc_opaque",
      "inventoryReservationIds": ["res_opaque"],
      "routingPolicyVersion": "route-policy-vN",
      "serviceabilityRuleVersions": [{"serviceabilityRuleId": "sr_opaque", "version": 3}],
      "estimatedDeliveryAt": null,
      "reasonCodes": ["ALL_ITEMS_AVAILABLE", "LANE_VALIDATED"],
      "decidedAt": "2026-10-24T10:00:00Z"
    }

For NO_ROUTE, return a valid evaluation resource with status NO_ROUTE and reason codes. Use 4xx for invalid input and 5xx for unexpected infrastructure failures.

### 5.5 Shipment Provider and shipment operations

- POST /api/v1/provider-connections — create draft connection.
- GET /api/v1/provider-connections/{connectionId} — read non-secret config and status.
- POST /api/v1/provider-connections/{connectionId}/validate — record/run contract, credentials, serviceability, webhook, and operational checks.
- POST /api/v1/provider-connections/{connectionId}/activate — guarded activation with explicit authorization and evidence.
- POST /api/v1/shipments — create canonical shipment from a selected decision.
- GET /api/v1/shipments/{shipmentId} — canonical shipment and timeline.
- POST /api/v1/shipments/{shipmentId}/book — idempotently request booking.
- POST /api/v1/shipments/{shipmentId}/cancel — request cancellation; distinguish requested from confirmed cancellation.
- POST /api/v1/provider-webhooks/{providerConnectionId} — authenticated event ingress with adapter-specific signature validation.
- POST /api/v1/shipments/{shipmentId}/sync — controlled status refresh where supported.
- GET /api/v1/shipments/{shipmentId}/events — normalized timeline.
- GET /api/v1/provider-connections/{connectionId}/health — integration health without secrets.

Booking request:
    {
      "shipmentId": "shp_opaque",
      "providerConnectionId": "pc_opaque",
      "package": {
        "weightGrams": 1200,
        "lengthMm": 250,
        "widthMm": 180,
        "heightMm": 80
      },
      "paymentCollection": {
        "mode": "COD",
        "amountMinor": 125000,
        "currency": "INR"
      },
      "idempotencyKey": "shipment-booking-opaque"
    }

The amount is illustrative only. The authoritative payable/COD amount must come from the order/payment ledger and comply with the existing COD cap, eligible postal-area, delivery OTP, and fee policies. Never trust a client-supplied amount as financial truth.

Booking response:
    {
      "shipmentId": "shp_opaque",
      "bookingStatus": "BOOKING_PENDING",
      "providerConnectionId": "pc_opaque",
      "providerShipmentRef": null,
      "trackingNumber": null,
      "requestId": "req_opaque"
    }

Booking is asynchronous. A timeout is an unknown outcome, not proof that no booking occurred. Reconcile using idempotency and provider lookup before attempting another booking.

## 6. Cross-cutting API rules

- **Idempotency:** require Idempotency-Key for reservation, fulfilment decision, booking, cancellation, and financial reconciliation mutations. Scope keys by authenticated client and operation; retain results for a documented period.
- **Concurrency:** use ETags or explicit versions for configuration writes. Reject stale updates with 412 Precondition Failed or documented 409.
- **Error codes:** WAREHOUSE_INACTIVE, INSUFFICIENT_STOCK, SERVICEABILITY_NOT_VALIDATED, NO_ELIGIBLE_PROVIDER_LANE, PROVIDER_TIMEOUT_UNKNOWN_OUTCOME, PROVIDER_BOOKING_REJECTED, IDEMPOTENCY_KEY_CONFLICT, INVALID_STATE_TRANSITION.
- **Correlation:** propagate requestId, correlationId, orderId, fulfilmentDecisionId, and shipmentId across async messages.
- **Events/outbox:** use a transactional outbox or equivalent reliable mechanism. Consumers are idempotent. Candidate internal events: InventoryReserved, InventoryReservationReleased, FulfilmentDecisionSelected, NoEligibleRouteFound, ShipmentBookingRequested, ShipmentBooked, ShipmentStatusChanged, ProviderConnectionSuspended.
- **Security:** RBAC/least privilege; separate read, configure, activate, and emergency-suspend permissions. Protect address data, audit admin actions, validate webhook signatures, rotate credentials, and rate-limit public endpoints.
- **Observability:** track reservation conflicts, no-route rate, booking success/latency, duplicate or invalid webhooks, stale tracking, COD mismatch, and manual interventions. Never use raw phone/address as metric labels.
- **Estimates:** store estimate source, calculation time, policy/config versions, and SLA class. Do not imply a guarantee unless business policy explicitly defines one.

## 7. Configuration lifecycle

Use a guarded lifecycle for warehouse, serviceability rule, routing policy, and provider connection:

    DRAFT → VALIDATING → ACTIVE → SUSPENDED → RETIRED

Not every transition is legal; implement an explicit transition matrix. Activation evidence should include:
- Operational ownership and readiness.
- Actual origin/address validation where relevant.
- Inventory and fulfilment workflow checks.
- Destination/lane serviceability evidence.
- Provider contract, credentials, booking/cancel/tracking verification where relevant.
- Webhook security, duplicate/out-of-order handling, and outage/fallback tests.
- COD collection/remittance reconciliation where COD is supported.
- Approver identity, timestamp, checklist version, and evidence references.

Changes to active routing or serviceability create new versions. Existing decisions retain their version snapshot; emergency suspension blocks future use without rewriting history.

## 8. Acceptance criteria

1. Adding, removing, or pausing a warehouse requires configuration only; no application code change or city-specific branch.
2. No fixed city names, warehouse count, or postal-code list is required by the core model.
3. An inactive warehouse or one without activation evidence cannot route orders.
4. Inventory reservations prevent overselling under concurrency and are idempotent.
5. A destination is eligible only when an active matching rule and selected provider lane support it.
6. Routing output is reproducible from persisted inputs and configuration versions.
7. No eligible candidate returns NO_ROUTE and reason codes without booking a shipment.
8. Own delivery, direct courier, and aggregator adapters map to one canonical shipment lifecycle.
9. Duplicate/out-of-order webhooks do not duplicate effects or incorrectly regress status.
10. Unknown booking outcomes are reconciled before retrying a new booking.
11. COD amount and eligibility come from authoritative order/payment policy; delivery does not imply remittance settlement.
12. Configuration activation, suspension, and admin changes are auditable.
13. Tests cover concurrency, idempotency, invalid transitions, suspended lanes, provider outages, and cache invalidation.
14. Examples contain no real or implied warehouse addresses or validated serviceability claims.

## 9. Minimum test matrix

| Area | Scenario | Expected result |
|---|---|---|
| Warehouse | Inactive warehouse considered for routing | Excluded with reason code |
| Inventory | Concurrent reservations exceed available quantity | No oversell; losing attempt gets typed conflict |
| Inventory | Same idempotency key replayed | Same result; no duplicate hold |
| Serviceability | No active matching rule | Ineligible with serviceability reason |
| Serviceability | Rule suspended after positive cache | Refresh/fail closed; stale positive not trusted |
| Routing | Several eligible lanes | Decision records policy versions and rationale |
| Routing | No eligible route | NO_ROUTE; no provider booking |
| Provider | Booking timeout | Reconcile unknown outcome before retry |
| Provider | Duplicate webhook | One effective transition |
| Provider | Out-of-order status event | State machine prevents invalid regression |
| Provider | Connection suspended | No new bookings through connection |
| COD | Delivered but remittance absent | Delivered shipment; COD remains unsettled |
| Governance | Activation evidence incomplete | Activation rejected |
| Security | Invalid webhook signature | Rejected/audited; no shipment mutation |

## 10. Open decisions before implementation

- Service ownership and deployment boundaries.
- Canonical catalogue key and catalogue integration contract.
- Split shipments, partial fulfilment, backorders, and stock transfers in initial release.
- Reservation TTL, release policy, and order/payment timing.
- Routing weights, tie-breaks, manual override authority, and rerouting rules.
- Destination normalization/address-validation provider and country-specific schema.
- Warehouse capacity model and capacity reservation.
- Provider contracts, rates, SLA, capabilities, connection ownership, and support model.
- Webhook signature scheme and event retention per selected provider.
- Canonical shipment transition matrix and reverse shipment model.
- COD remittance/reconciliation ledger ownership and discrepancy handling.
- Data privacy and audit retention.
- Production activation approver and evidence standard.

## 11. Governance note

This is a generic, location-agnostic proposal created for review. It does not close DEP-002, validate any physical warehouse or postal area, select any provider, establish contracts, prove integration readiness, or approve architecture. Do not move this document to docs/01-architecture/ until applicable requirements and architecture governance gates are satisfied.
