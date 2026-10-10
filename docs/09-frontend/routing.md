# Frontend Routing Architecture

React Router v6 configuration, layout nesting, code splitting, and protected route guards.

---

## 1. Route Organization (`customer-web`)

```typescript
export const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: "search", element: <SearchResultsPage /> },
      { path: "products/:slug", element: <ProductDetailPage /> },
      { path: "cart", element: <CartPage /> },
      {
        element: <ProtectedRoute requiredRole="ROLE_CUSTOMER" />,
        children: [
          { path: "checkout", element: <CheckoutPage /> },
          { path: "orders", element: <OrderHistoryPage /> },
          { path: "orders/:orderId", element: <OrderDetailPage /> },
          { path: "account", element: <AccountSettingsPage /> },
        ]
      }
    ]
  }
]);
```

---

## 2. Code Splitting & Lazy Loading

Every top-level page component is dynamically loaded via `React.lazy()` with route-level `Suspense` fallback skeletons, reducing initial bundle size to under 120KB gzipped.
