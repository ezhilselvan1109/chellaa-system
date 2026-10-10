# Customer Storefront Application (`apps/customer-web`)

Feature modules, layouts, mobile responsiveness, and shopping user journeys.

---

## 1. Feature Modules

```text
apps/customer-web/src/features/
├── auth/            (Login, Registration, OTP modal, Password Reset)
├── catalog/         (Product Detail Page, Category Landing, Search Results)
├── cart/            (Cart Drawer, Item Quantity controls, Coupon input)
├── checkout/        (Address Selection, Delivery Options, Razorpay Checkout)
├── orders/          (Order History, Real-Time Timeline, Return Request Modal)
└── account/         (Profile, Saved Addresses, Notification Preferences)
```

---

## 2. Core Web Vitals Targets

- **Largest Contentful Paint (LCP)**: $\le 1.8$ seconds.
- **First Input Delay (FID)**: $\le 50$ milliseconds.
- **Cumulative Layout Shift (CLS)**: $\le 0.05$.
- Dynamic image sizing with modern WebP formats and explicit aspect ratio containers to prevent layout shift.
