# Pagination, Filtering, and Sorting

Standards for querying collections across all microservice REST APIs.

---

## 1. Pagination Standards

1. **Offset-Based Pagination (UI Tables)**:
   - Parameters: `page` (1-indexed integer, default: `1`), `size` (integer, default: `20`, max: `100`).
   - Example: `GET /api/v1/products?page=2&size=20`.
2. **Cursor-Based Pagination (Infinite Scroll & High Volume)**:
   - Parameters: `cursor` (opaque base64 encoded token), `limit` (default: `20`, max: `100`).
   - Example: `GET /api/v1/orders?cursor=eyJjcmVhdGVkQXQiOiIyMDI2LTEwLTEwIn0&limit=50`.

---

## 2. Filtering & Sorting Syntax

- **Sorting**: `sort=<field>:(asc|desc)` (e.g. `sort=createdAt:desc,price:asc`).
- **Filtering**:
  - Equality: `status=PAID`
  - Range: `priceMin=500&priceMax=2000`
  - In-List: `brandIds=b1,b2,b3`
  - Boolean: `inStock=true`
