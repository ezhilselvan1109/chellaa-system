---
name: react-development
description: >-
  Use this skill to build responsive, accessible, type-safe React 18+ and TypeScript frontend applications
  for both Customer Storefront and Admin Operations web apps, adhering to component architecture and state management standards.
---

# React Development Skill

## 1. Purpose
Guides frontend development using React 18, TypeScript, and modern state management patterns, creating accessible, responsive, performant user interfaces for Customer Web and Admin Web.

## 2. When to Use It
- Building or modifying features in `apps/customer-web/` or `apps/admin-web/`.
- Implementing UI components, design tokens, hooks, and pages.
- Integrating backend REST APIs via typed API clients (Axios/TanStack Query).
- Implementing authentication flows, session handling, and protected routes.
- Writing Vitest and Playwright component/E2E tests.

## 3. Required Inputs
- UI design specifications and user stories (`docs/01-requirements/`).
- API contracts from `docs/06-api/`.
- Frontend architecture blueprints (`docs/09-frontend/`).

## 4. Procedure
1. **Application Structure**:
   ```text
   apps/<customer-web|admin-web>/src/
   ├── api/             (Generated typed REST clients, interceptors)
   ├── assets/          (Images, icons, fonts)
   ├── components/      (Atomic UI components: Button, Input, Modal)
   ├── features/        (Feature modules: catalog, cart, checkout, orders)
   │   ├── components/
   │   ├── hooks/
   │   ├── services/
   │   └── types/
   ├── hooks/           (Shared custom React hooks)
   ├── layouts/         (Root, Auth, Dashboard layouts)
   ├── routes/          (React Router definitions, ProtectedRoute)
   └── stores/          (TanStack Query caches, Zustand client state)
   ```
2. **State Management**:
   - Server State: TanStack Query (React Query) for caching, revalidation, optimistic updates.
   - Client/UI State: Zustand for lightweight local state (e.g. cart drawer, active filters).
3. **API Integration & Resilience**:
   - Centralized Axios client with automatic Bearer token injection and 401 refresh token interceptors.
   - Exhaustive handling of Loading, Empty, Error, and Success states in every view.
4. **Accessibility (a11y)**:
   - Proper semantic HTML (`<main>`, `<nav>`, `<header>`, `<button>`).
   - ARIA labels on icon buttons and dynamic live regions for cart updates.
5. **Security**:
   - Sanitize HTML inputs; avoid `dangerouslySetInnerHTML`.
   - Store refresh tokens in HttpOnly cookies, never in localStorage.

## 5. Rules & Constraints
- No business logic inside visual UI components; extract to custom hooks (`useCart`, `useCheckout`).
- Maintain 100% TypeScript type safety; do not use `any`.
- Never bypass authorization checks on the client; UI role guards are UX enhancements, backend is source of truth.

## 6. Expected Outputs
- Type-safe React components and hooks.
- Passing Vitest and Playwright test specs.

## 7. Validation Checklist
- [ ] Are loading spinners and error messages shown gracefully on network failures?
- [ ] Is keyboard navigation working for modals, dropdowns, and drawers?
- [ ] Are forms validated client-side with Zod before submission?

## 8. Common Failure Modes
- Storing sensitive tokens in insecure browser `localStorage`.
- Unhandled pagination boundaries causing endless re-fetching loops.

## 9. Authoritative Links
- [Frontend Architecture](file:///d:/learning/Microservice/backend/docs/09-frontend/frontend-architecture.md)
- [Customer Application](file:///d:/learning/Microservice/backend/docs/09-frontend/customer-application.md)
- [Admin Application](file:///d:/learning/Microservice/backend/docs/09-frontend/admin-application.md)
