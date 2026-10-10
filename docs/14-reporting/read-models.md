# Materialized Read Models

Database schemas and de-normalized analytical tables in `reporting_db`.

---

## 1. Materialized Daily Sales Aggregate (`daily_sales_summary`)

```sql
CREATE TABLE daily_sales_summary (
    summary_date DATE NOT NULL,
    category_id UUID NOT NULL,
    order_count INT NOT NULL DEFAULT 0,
    items_sold_count INT NOT NULL DEFAULT 0,
    gross_revenue DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    discounts_given DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    net_revenue DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    tax_collected DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    refund_amount DECIMAL(14, 2) NOT NULL DEFAULT 0.00,
    PRIMARY KEY (summary_date, category_id)
);
```

---

## 2. SKU Velocity & Inventory Health Model (`sku_daily_velocity`)

```sql
CREATE TABLE sku_daily_velocity (
    snapshot_date DATE NOT NULL,
    sku VARCHAR(64) NOT NULL,
    units_sold INT NOT NULL DEFAULT 0,
    current_on_hand INT NOT NULL DEFAULT 0,
    days_of_inventory_remaining DECIMAL(5, 1),
    PRIMARY KEY (snapshot_date, sku)
);
```
