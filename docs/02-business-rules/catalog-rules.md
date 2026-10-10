# Catalog Business Rules

## 1. Catalog Hierarchy & Product Structure

1. **Product vs. Variant (SKU)**:
   - A **Product** represents the parent conceptual merchandise (e.g. "Heritage Oxford Shirt"). It holds common descriptions, brand association, category hierarchy, size charts, and generic tags.
   - A **Variant** represents the physical purchasable SKU (e.g. "Heritage Oxford Shirt - Navy - Medium"). Each variant possesses a globally unique SKU code, barcode (EAN/UPC), specific attributes (Size, Color), weight, and physical dimensions.
2. **Purchasability Invariant**: Customers purchase *Variants*, never abstract Products. Carts, orders, and inventory reservations reference `variantId` and `sku`.

## 2. Category & Brand Invariants

1. **Category Tree**: Categories form a strict Directed Acyclic Graph (DAG) tree with a maximum depth of 4 levels.
2. **Slug Uniqueness**: Category slugs and Product URL slugs must be unique across the platform to ensure SEO-safe routing.
3. **Product Assignment**: A product must belong to exactly one primary category, and may optionally belong to up to 5 secondary promotional categories.

## 3. Product Publishing Lifecycle

1. **State Machine**:
   - `DRAFT`: Under creation; visible only to merchandisers in Admin Web.
   - `SCHEDULED`: Approved for launch; published automatically when UTC system time reaches `publish_at`.
   - `PUBLISHED`: Visible in customer storefront and indexed in OpenSearch.
   - `ARCHIVED`: Hidden from search and navigation; remains accessible via direct link only for existing order history.
2. **Media Standards**:
   - Every published product must possess at least one primary high-resolution image (minimum 1000x1000px).
   - Images are processed into WebP format with thumbnails (150x150), medium (500x500), and large (1200x1200) renditions.
