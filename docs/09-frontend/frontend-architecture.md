# Frontend Architecture

Architecture of React 18, TypeScript, and modern frontend application standards.

---

## 1. Application Separation

The platform architecture defines two separate Single Page Applications (SPAs):
1. **`apps/customer-web`**:
   - The retail storefront.
   - Optimized for Core Web Vitals, mobile-first responsiveness, SEO, and sub-second page transitions.
2. **`apps/admin-web`**:
   - The corporate operations portal.
   - Optimized for complex data tables, bulk catalog editing, order triage, and audit dashboards.

---

## 2. Shared Packages & Monorepo Boundaries

Shared logic between frontend apps is restricted to:
- `packages/api-client`: Generated TypeScript API client contracts and DTO types.
- `packages/design-tokens`: Shared color palettes, typography scales, and CSS variables.
- Component code is kept autonomous per application to prevent premature coupling between customer-facing UI and dense admin management screens.
