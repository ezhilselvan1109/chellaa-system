# Campaign Business Rules

## 1. Campaign Types & Lifecycles

1. **Campaign Scope**:
   - **Flash Sales**: Short duration (hours/days), deep discounts on select SKUs with fixed promotional inventory allocations.
   - **Category / Brand Promotions**: Storewide percentage discounts across all products within a specific category or brand.
   - **Order Tier Campaigns**: Progressive cart discounts (e.g., "Spend ₹2,000 get 10% off; Spend ₹5,000 get 20% off").
2. **Lifecycle State Machine**:
   - `DRAFT`: In design; no discounts applied.
   - `SCHEDULED`: Configured for future start date.
   - `ACTIVE`: Actively evaluated on product views and carts.
   - `PAUSED`: Temporarily disabled by admin; discounts suspended.
   - `COMPLETED`: Expired naturally upon passing `end_time` or budget exhaustion.
   - `CANCELLED`: Manually terminated before scheduled end.

## 2. Campaign Budgeting & Usage Controls

1. **Promotional Budget Caps**:
   - A campaign may define a maximum monetary subsidy budget (e.g., maximum total discount of ₹5,00,000).
   - Once cumulative discounts granted reach the budget ceiling, the campaign state automatically transitions to `COMPLETED`.
2. **Promotional Stock Allocation**:
   - Flash sales can allocate a limited quantity of units (e.g., 50 units of SKU-101 at 50% off).
   - Once the promotional quota is sold out, subsequent orders for SKU-101 revert to the standard regular selling price.

## 3. Campaign Priority & Stacking Resolution

1. **Single Highest Discount Rule (Default)**:
   - If a SKU is eligible for multiple concurrent campaigns (e.g. 15% Brand Sale vs 20% Seasonal Sale), the customer receives the **highest single discount** (20%).
   - Competing campaigns do not stack additively unless both campaigns explicitly declare `allow_stacking = true`.
2. **Deterministic Evaluation Order**:
   - Flash Sale SKU override -> Category Campaign -> Cart Order Value Campaign.
