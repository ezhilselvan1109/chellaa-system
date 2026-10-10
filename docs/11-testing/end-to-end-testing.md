# End-to-End (E2E) Testing

Full-flow integration tests simulating real user actions across frontends and microservices.

---

## 1. Automated E2E Test Suite (Playwright)

Executed against a running Docker Compose environment in staging/CI:
1. **Flow 1 (Full Shopping Cycle)**: Customer registers via OTP -> Searches for item -> Adds to Cart -> Applies coupon -> Initiates checkout -> Mocks payment gateway success -> Asserts Order Confirmed screen -> Asserts invoice generated.
2. **Flow 2 (Fulfillment Cycle)**: Admin logs in -> Views order in triage queue -> Marks Picked & Packed -> Generates carrier AWB -> Updates status to Delivered.
3. **Flow 3 (Return & Refund Cycle)**: Customer initiates return within 7 days -> Admin QC approves return -> Asserts refund trigger emitted.
