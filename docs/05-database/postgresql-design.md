# PostgreSQL Database Design

Guidelines for data types, constraints, relational patterns, and concurrency locking in PostgreSQL 16.

---

## 1. Approved Data Types & Conventions

- **Identifiers**: `UUID` (UUIDv7 generated via application or `uuid-ossp` extension).
- **Strings**: `VARCHAR(N)` for bounded strings (e.g. `VARCHAR(255)`, `VARCHAR(50)`). Avoid unbounded `TEXT` unless storing long descriptions or markdown.
- **Financial Quantities**: `DECIMAL(14, 2)` or `NUMERIC(14, 2)` for currency values. Never use `FLOAT` or `DOUBLE PRECISION`.
- **Timestamps**: Strictly `TIMESTAMPTZ` (UTC).
- **Structured Dynamic Attributes**: `JSONB` with GIN indexing for polymorphic attribute values or raw webhook payloads.
- **Enumerations**: `VARCHAR(50)` with check constraints (e.g., `CHECK (status IN ('PENDING', 'PAID', 'SHIPPED'))`) preferred over PostgreSQL native `CREATE TYPE enum` to allow seamless zero-downtime migrations.

---

## 2. Service Schema DDL Examples

### 2.1 Order Service (`order_db`)
```sql
CREATE TABLE orders (
    order_id UUID PRIMARY KEY,
    order_number VARCHAR(32) NOT NULL UNIQUE,
    customer_id UUID NOT NULL,
    status VARCHAR(30) NOT NULL,
    subtotal_amount DECIMAL(14, 2) NOT NULL,
    discount_amount DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    shipping_amount DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    tax_amount DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    total_amount DECIMAL(14, 2) NOT NULL,
    currency VARCHAR(3) NOT NULL DEFAULT 'INR',
    shipping_address_snapshot JSONB NOT NULL,
    billing_address_snapshot JSONB NOT NULL,
    version INT NOT NULL DEFAULT 0,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE order_items (
    order_item_id UUID PRIMARY KEY,
    order_id UUID NOT NULL REFERENCES orders(order_id) ON DELETE CASCADE,
    sku VARCHAR(64) NOT NULL,
    product_title VARCHAR(255) NOT NULL,
    variant_attributes JSONB NOT NULL,
    unit_price DECIMAL(14, 2) NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    tax_rate DECIMAL(5, 2) NOT NULL,
    tax_amount DECIMAL(14, 2) NOT NULL,
    line_total DECIMAL(14, 2) NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_orders_customer ON orders(customer_id, created_at DESC);
CREATE INDEX idx_orders_status ON orders(status);
```

### 2.2 Inventory Service (`inventory_db`)
```sql
CREATE TABLE inventory_stocks (
    sku VARCHAR(64) NOT NULL,
    warehouse_id UUID NOT NULL,
    on_hand_quantity INT NOT NULL CHECK (on_hand_quantity >= 0),
    allocated_quantity INT NOT NULL CHECK (allocated_quantity >= 0),
    safety_stock_threshold INT NOT NULL DEFAULT 0,
    version INT NOT NULL DEFAULT 0,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (sku, warehouse_id)
);

CREATE TABLE stock_allocations (
    allocation_id UUID PRIMARY KEY,
    sku VARCHAR(64) NOT NULL,
    warehouse_id UUID NOT NULL,
    order_id UUID NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    status VARCHAR(30) NOT NULL, -- RESERVED, COMMITTED, RELEASED
    expires_at TIMESTAMPTZ NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_allocations_active ON stock_allocations(expires_at) WHERE status = 'RESERVED';
```
