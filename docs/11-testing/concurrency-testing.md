# Concurrency and Race Condition Testing

Automated multi-threaded concurrency test suites verifying inventory overselling prevention and coupon caps.

---

## 1. Concurrency Test Implementation

```java
@Test
void shouldPreventOversellingUnderHighConcurrency() throws InterruptedException {
    final String sku = "LIMITED-EDITION-SHIRT";
    final int initialStock = 5;
    final int threadCount = 50;
    
    inventoryService.initializeStock(sku, initialStock);

    ExecutorService executor = Executors.newFixedThreadPool(threadCount);
    CountDownLatch startLatch = new CountDownLatch(1);
    CountDownLatch endLatch = new CountDownLatch(threadCount);
    AtomicInteger successfulReservations = new AtomicInteger(0);
    AtomicInteger rejectedReservations = new AtomicInteger(0);

    for (int i = 0; i < threadCount; i++) {
        final UUID orderId = UUID.randomUUID();
        executor.submit(() -> {
            try {
                startLatch.await(); // Release all 50 threads simultaneously
                inventoryService.reserveStock(orderId, sku, 1);
                successfulReservations.incrementAndGet();
            } catch (InsufficientStockException e) {
                rejectedReservations.incrementAndGet();
            } finally {
                endLatch.countDown();
            }
        });
    }

    startLatch.countDown();
    endLatch.await(10, TimeUnit.SECONDS);

    // Verify strict invariants
    assertThat(successfulReservations.get()).isEqualTo(initialStock);
    assertThat(rejectedReservations.get()).isEqualTo(threadCount - initialStock);
    assertThat(inventoryService.getAvailableStock(sku)).isZero();
}
```
All pull requests modifying stock reservation logic must execute and pass this test suite.
