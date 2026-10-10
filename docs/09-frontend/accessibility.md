# Frontend Accessibility (a11y)

Standards for WCAG 2.1 AA compliance across storefront and administration interfaces.

---

## 1. Core Accessibility Standards

1. **Semantic HTML**: Use correct elements (`<nav>`, `<main>`, `<article>`, `<button>`, `<header>`, `<footer>`). Never use `<div onClick="...">` for interactive triggers.
2. **Keyboard Navigation**:
   - Every interactive element (buttons, links, form inputs, modals, cart drawer) must be navigable using `Tab` and `Shift+Tab`.
   - Modals and drawers must implement focus traps: when open, focus is constrained within the modal, and pressing `Escape` closes the modal.
3. **Screen Reader Support**:
   - Icon-only buttons (cart icon, search magnifier, close buttons) must have `aria-label` (e.g. `aria-label="View shopping cart with 2 items"`).
   - Dynamic cart updates and validation errors use `aria-live="polite"` regions so screen readers announce changes automatically.
4. **Color Contrast**:
   - Contrast ratio of at least 4.5:1 for standard text and 3:1 for large headings (WCAG AA).
