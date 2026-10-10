# Frontend State Management

Separation of Server State (TanStack Query) and Client State (Zustand).

---

## 1. Server State: TanStack Query (React Query)

- All remote data fetching, caching, deduplication, and background invalidation is handled by TanStack Query.
- `staleTime`: 5 minutes for product catalogs; 0 seconds for cart and stock queries.
- Optimistic updates used for adding items to cart and saving wishlist items.

---

## 2. Client State: Zustand

- Ephemeral UI state is managed via lightweight Zustand stores:
  - `useCartDrawerStore`: Open/closed state of sliding cart drawer.
  - `useFilterStore`: Selected search facet filters before user hits apply.
  - `useAuthStore`: Active customer profile and in-memory access token.
