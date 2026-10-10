# Transactions and Concurrency

Isolation levels, locking strategies, race conditions, and deadlocks in PostgreSQL.

---

## 1. Concurrency Control Mechanisms

1. **Optimistic Locking (`@Version`)**:
   - Used for low-contention entities: `Product`, `CustomerProfile`, `Order`.
   - Hibernate checks version column on update: `UPDATE orders SET status = ?, version = version + 1 WHERE order_id = ? AND version = ?`.
   - Throws `OptimisticLockException` if updated concurrently; application catches and returns HTTP 409 Conflict or retries.
2. **Pessimistic Locking (`SELECT FOR UPDATE`)**:
   - Used for high-contention, strictly bounded resources: Stock allocation in `inventory-service` and wallet ledger credits.
   - Blocks competing transactions until the current transaction commits or rolls back, guaranteeing zero negative stock balances.

---

## 2. Preventing Deadlocks

To avoid PostgreSQL deadlocks:
1. **Deterministic Lock Ordering**: When locking multiple items (e.g. allocating multiple SKUs in an order), sort SKUs alphanumerically before acquiring locks:
   ```java
   List<String> sortedSkus = orderItems.stream().map(OrderItem::sku).sorted().toList();
   for (String sku : sortedSkus) {
       inventoryRepository.findAndLockBySku(sku);
   }
   ```
2. **Statement Timeouts**: Set `statement_timeout = '3000ms'` to abort transactions stuck on deadlock locks rather than hanging indefinitely.
