# DEP-002 — Proposed Multi-State Warehouse Pilot and PIN-Code Selection Plan

**Status:** DRAFT — PROPOSAL ONLY  
**Decision dependency:** DEP-002 remains `OPEN`  
**Governance:** This document does not approve Phase 1, the requirements baseline, architecture, warehouse leases, courier/aggregator integrations, or serviceability.  
**Business model:** Four initial regional fulfilment warehouses; regional inventory; medium-sized pilot; hybrid delivery channels permitted (own delivery team, direct courier partners, shipping aggregator).

## 1. Planning objective

Propose an operationally testable multi-state pilot using four regional fulfilment hubs. Candidate cities and PIN codes are starting points for validation, not committed facilities or active service areas.

The target is India-wide coverage over time. The initial launch must activate only validated PIN-code clusters where stock, a delivery lane, payment/COD eligibility, reverse logistics, customer support, and operational ownership are ready.

## 2. Proposed regional hubs and pilot geography

| Hub | Candidate city (not selected) | Initial pilot states/territories | Candidate urban clusters | Candidate PIN codes — NOT VALIDATED |
|---|---|---|---|---|
| South | Chennai | Tamil Nadu; later Karnataka and Kerala | T. Nagar, Velachery, Adyar/Tiruvanmiyur; later Bengaluru and Kochi urban clusters | Chennai: 600017 (T. Nagar), 600042 (Velachery), 600020 (Adyar), 600041 (Tiruvanmiyur) |
| West | Mumbai or Pune (choose one as the hub city after cost/site review) | Maharashtra; later Goa and Gujarat | Andheri East/Powai if Mumbai; central Pune/Hinjawadi if Pune | Mumbai candidates: 400069 (Andheri East), 400076 (Powai); Pune candidates: 411001 (Pune GPO area), 411057 (Hinjawadi area) |
| North | Delhi NCR (warehouse may be in Delhi, Gurugram, or Noida; exact city pending) | Delhi; selected Haryana and Uttar Pradesh clusters | Central/South Delhi, Gurugram, Noida | Delhi: 110001 (Connaught Place); Gurugram: 122008 (DLF Phase II); Noida: 201301 (candidate central Noida area) |
| East | Kolkata | West Bengal; later selected Odisha clusters | Kolkata central area and Salt Lake; later Bhubaneswar urban cluster | Kolkata: 700001 (Kolkata GPO area), 700064 (Salt Lake candidate) |

**Important PIN-code qualification:** The codes above are provisional research candidates only. A postal PIN code is not proof that Chellaa, a specific courier, or an aggregator will service the address. Do not publish these as serviceable or enable checkout against them until each code has been checked against the final warehouse address, official postal records, and provider serviceability APIs/files. Validate locality-to-PIN mappings against the India Post PIN-code lookup: https://dac.indiapost.gov.in/mypincode/home and the official India Post post-office locator: https://www.indiapost.gov.in/locate-postoffice.

## 3. Suggested rollout waves

### Wave 1 — controlled urban clusters

Start with a small set of dense urban clusters around each operationally ready hub:
- Chennai: select two to four clusters from the candidate areas above.
- West: select Mumbai **or** Pune first; do not assume both have a warehouse.
- North: select Delhi plus one nearby NCR cluster only if the warehouse and delivery lanes support it.
- East: select Kolkata central/Salt Lake clusters after address and provider validation.

The exact count of PIN codes per cluster is to be determined from address-level demand, delivery economics, warehouse capacity, and verified provider coverage. Do not set a launch count before those inputs exist.

### Wave 2 — adjacent metro/nearby-state expansion

Potential candidates, subject to a separate readiness gate:
- South: Bengaluru, Kochi, Hyderabad.
- West: Pune/Mumbai (the city not selected for the first western hub), Ahmedabad, Surat.
- North: additional Gurugram/Noida and selected nearby Uttar Pradesh/Haryana clusters.
- East: Bhubaneswar and selected nearby West Bengal/Odisha clusters.

### Wave 3 — broaden state coverage

Expand to further cities and states only after pilot performance meets agreed delivery, returns, cost, support, and COD-reconciliation thresholds. These thresholds and the state-level rollout sequence require owner/operations decisions.

## 4. PIN-code selection method

For every candidate PIN code, create one serviceability record and validate:

1. **Address identity:** Official PIN/post-office mapping and locality match; no city-only assumptions.
2. **Warehouse assignment:** Eligible fulfilment warehouse, available stock, dispatch cut-off, handling capacity, and inter-warehouse transfer rules.
3. **Delivery channel:** Own team, direct courier, and/or aggregator lanes actually available for that origin-destination pair.
4. **Provider capability:** Parcel/commodity restrictions, weight/dimension limits, tracking events, pickup schedule, delivery attempts, OTP proof of delivery, and exception handling.
5. **Payments:** Razorpay prepaid methods remain unchanged. COD is eligible only under existing policy (order value up to ₹10,000, eligible/verified PIN code, delivery OTP, ₹50 non-refundable convenience fee).
6. **Reverse logistics:** Return pickup/return-to-origin availability, serial/IMEI verification process, inspection destination, and customer communication.
7. **Economics and SLA:** Shipping/handling cost, expected transit time, delivery success, failed-delivery rate, return cost, and support burden.
8. **Operational evidence:** Test booking/pickup/tracking/delivery/exception/return and COD remittance reconciliation using the actual provider integration before activation.

## 5. Serviceability states

Use explicit states instead of a single unchecked boolean:

- `PROPOSED` — candidate only; not visible as serviceable to customers.
- `ADDRESS_VALIDATED` — postal/locality mapping verified.
- `PROVIDER_VALIDATED` — at least one approved operational lane confirmed with current evidence.
- `OPERATIONS_READY` — warehouse, inventory, customer support, returns, payments/COD and exception handling reviewed.
- `PILOT_ACTIVE` — activated under an approved pilot scope.
- `SUSPENDED` — temporarily disabled due to outage, SLA breach, or risk.
- `RETIRED` — no longer offered.

A state transition must retain who/what validated it, validation timestamp, source/version, and the reason for any suspension. Do not mark a PIN code `PILOT_ACTIVE` solely because a provider's public coverage list includes it.

## 6. Minimum pilot validation matrix

| Evidence item | Required before PIN activation? | Current status |
|---|---|---|
| Exact warehouse street address and operating hours | Yes | PENDING |
| Hub city decision for Mumbai vs Pune; exact NCR site | Yes | PENDING |
| Candidate PIN list checked against official postal data | Yes | PENDING |
| Direct courier/aggregator names, contracts, credentials, and origin-destination lane proof | Yes for the lane selected | PENDING |
| Own-team coverage, staffing, delivery radius, proof-of-delivery procedure | If own team is selected | PENDING |
| Rate card, pickup schedule, SLA, restrictions and escalation contacts | Yes per active lane | PENDING |
| Shipment lifecycle/event mapping, webhook/API deduplication, external AWB mapping | Yes before integration launch | PENDING |
| COD collection and remittance reconciliation tests | Yes wherever COD is enabled | PENDING |
| Reverse pickup, return-to-origin, serial/IMEI inspection workflow | Yes before returns-enabled activation | PENDING |
| End-to-end test evidence and operational sign-off | Yes | PENDING |
| Owner approval of pilot states, clusters, and activation thresholds | Yes | PENDING |

## 7. Required decisions and dependencies

1. Confirm whether the western hub is Mumbai or Pune.
2. Select the actual warehouse site/address for each hub, including the specific Delhi NCR city.
3. Confirm initial pilot states and urban clusters.
4. Identify own-team boundaries and courier/aggregator candidates; no provider is selected by this plan.
5. Define quantitative go/no-go thresholds for delivery SLA, delivery success, cost/order, return pickup, COD reconciliation, and customer-support handling.
6. Validate each candidate PIN code with official postal data and actual provider serviceability evidence.
7. Record owner/operations sign-off and update the decision register/readiness audit without changing unrelated governance statuses.

## 8. Governance boundary

- `DEP-002 = OPEN` until warehouse and provider readiness evidence has been validated.
- Phase 1 review remains `PENDING_APPROVAL`.
- Requirements baseline remains `NOT_APPROVED`.
- Architecture approval remains `NOT_GRANTED`.
- Architecture documents must not be promoted from the requirements review area into `docs/01-architecture/` until the established governance gates are passed.
- This is a planning document only; no software implementation, provider integration, live serviceability check, or end-to-end test is claimed.

## 9. Reference sources

- Government of India — PIN-code lookup: https://dac.indiapost.gov.in/mypincode/home
- India Post — Locate a post office: https://www.indiapost.gov.in/locate-postoffice
- Government Services Portal — postal PIN-code information service: https://services.india.gov.in/service/detail/find-postal-pincode-information

These references support postal-code verification only; they do not establish Chellaa delivery coverage or courier/aggregator availability.
