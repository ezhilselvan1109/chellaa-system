# Stakeholders and Personas

This document outlines key stakeholders and user personas interacting with the single-owner e-commerce platform.

---

## 1. Stakeholder Map

| Stakeholder Role | Primary Objective | Key Concerns |
| :--- | :--- | :--- |
| **Business Owner / Merchant** | Maximize revenue, profitability, brand trust, and operational efficiency. | Inventory holding costs, payment fraud, fulfillment SLAs, customer retention. |
| **Merchandising Team** | Curate appealing catalogs, set competitive pricing, run seasonal campaigns. | Rapid catalog updates, scheduled publishing, promotion conflict prevention. |
| **Warehouse & Fulfillment Team** | Fast, error-free order picking, packing, dispatching, and return processing. | Real-time stock visibility, accurate pick-lists, courier manifest automation. |
| **Customer Support Team** | Rapid resolution of customer inquiries, order issues, returns, and refunds. | Comprehensive order timeline, one-click refund initiation, clear communication logs. |
| **Finance & Accounting Team** | Accurate revenue recognition, tax compliance (GST), and provider reconciliation. | Audit-proof invoices, gateway fee reconciliation, refund ledger tracking. |
| **Engineering & Operations** | Platform uptime, low latency, zero data loss, automated CI/CD. | Observability, decoupled microservice deploys, fault tolerance during flash sales. |

---

## 2. User Personas

### Persona 1: Priya - The Mobile Consumer
- **Profile**: 28-year-old tech-savvy professional shopping primarily via smartphone.
- **Goals**: Rapid product discovery, clear sizing information, quick OTP login, instant UPI/card payment, real-time WhatsApp delivery updates.
- **Pain Points**: Slow checkout, expired promotions, clunky address entry, unexpected shipping fees at the last step.
- **Key Features Used**: Mobile-responsive storefront, autocomplete search, WhatsApp notifications, 1-click address selection, order tracking.

### Persona 2: Rajesh - The Operations & Merchandising Manager
- **Profile**: 36-year-old internal retail operations lead.
- **Goals**: Schedule a 3-day Diwali flash sale, upload 50 new apparel SKUs with photos, verify warehouse stock counts, monitor hourly sales.
- **Pain Points**: Clunky bulk upload tools, coupon code abuse eating margins, inventory count discrepancies between warehouse bins and online store.
- **Key Features Used**: Admin Web catalog manager, campaign scheduler, stock adjustment audit screens, live sales dashboard.

### Persona 3: Ananya - The Customer Care Representative
- **Profile**: 24-year-old support specialist handling inbound inquiries via phone and chat.
- **Goals**: Quickly look up a customer's order by phone number, check payment gateway status, approve a valid return request, issue an immediate refund.
- **Pain Points**: Having to check 3 different systems for courier tracking, gateway logs, and customer details.
- **Key Features Used**: Unified Admin customer 360 view, order audit timeline, return inspection queue, refund trigger button.
