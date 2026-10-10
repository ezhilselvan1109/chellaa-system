# Redis Database Design

Data structures, key conventions, eviction policies, and TTL configurations in Redis 7.2.

---

## 1. Key Naming & TTL Standards

All keys follow hierarchical namespaced formatting: `<domain>:<entity>:<identifier>[:<sub-key>]`.

| Key Pattern | Redis Type | Purpose | TTL | Eviction Policy |
| :--- | :--- | :--- | :--- | :--- |
| `cart:guest:{guestCartId}` | HASH | Anonymous visitor shopping cart items | 7 days (sliding) | volatile-lru |
| `idemp:{idempotencyKey}` | STRING | Idempotent API lock & response payload | 60 seconds | volatile-lru |
| `ratelimit:{ip}:{endpoint}`| STRING | Sliding window rate limiting counter | 60 seconds | volatile-lru |
| `cache:product:{productId}`| STRING / JSON | Product detail page pre-serialized JSON | 15 minutes $\pm 10\%$ | allkeys-lru |
| `lock:stock:{sku}` | STRING (Redlock) | Distributed lock for stock allocation | 5 seconds | volatile-lru |
| `coupon:usage:{couponCode}`| STRING (INCR) | Atomic counter for coupon redemptions | Matches campaign end | noeviction |

---

## 2. Distributed Locking Implementation

Distributed locks for high-contention resources use the Redlock algorithm or atomic `SET resource_key my_random_value NX PX 5000`. Lock release requires a Lua script validating random token ownership to prevent unlocking expired locks.
