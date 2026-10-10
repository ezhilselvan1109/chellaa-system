# Frontend Testing Strategy

Unit, component, and End-to-End (E2E) testing standards for React applications.

---

## 1. Testing Pyramid for Frontends

- **Unit Tests (Vitest)**: Utility functions, money calculations, date formatters, custom hooks (`useCartSubtotal`).
- **Component Tests (React Testing Library)**: User interactions, form validations, loading states, accessibility assertions (`expect(screen.getByRole('button', { name: /checkout/i })).toBeEnabled()`).
- **End-to-End Tests (Playwright)**: Full browser automation verifying critical customer flows:
  - Search Product -> View Detail -> Add to Cart -> Apply Coupon -> Checkout -> Payment Success.
  - Test suites execute against local mock servers (MSW - Mock Service Worker) in CI.
