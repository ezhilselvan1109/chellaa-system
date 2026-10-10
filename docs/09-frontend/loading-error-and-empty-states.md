# UI States: Loading, Error, and Empty States

Standards for ensuring every view handles the four fundamental asynchronous UI states.

---

## 1. The Four Fundamental States

Every React component querying remote data must implement:

1. **Loading State**:
   - Content skeleton loaders matching the exact geometric layout of the target data (avoiding generic spinning wheels that cause layout shift).
2. **Error State**:
   - Inline alert component with human-readable error explanation and actionable "Retry" button.
3. **Empty State**:
   - Helpful illustration, informative explanation (e.g. "Your cart is empty"), and a clear Call-To-Action (CTA) button ("Continue Shopping").
4. **Success State**:
   - Clean, interactive presentation of retrieved data with micro-animations.
