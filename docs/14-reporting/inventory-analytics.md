# Inventory Analytics and Forecasting

Inventory turnover, days of inventory remaining (DIR), and stockout forecasting.

---

## 1. Inventory Forecasting Formulas

- **Run Rate (Sales Velocity)**: Rolling 7-day average units sold per day:
  $$\text{Daily Velocity} = \frac{\sum_{t=1}^{7} \text{Units Sold}_t}{7}$$
- **Days of Inventory Remaining (DIR)**:
  $$\text{DIR} = \frac{\text{Current Available Stock}}{\text{Daily Velocity}}$$
- **Automated Reorder Trigger**: When $\text{DIR} \le \text{Supplier Lead Time Days} + \text{Safety Buffer (3 days)}$, an automated restock alert is generated on the warehouse manager dashboard.
