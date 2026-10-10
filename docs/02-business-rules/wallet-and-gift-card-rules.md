# Wallet and Gift Card Business Rules (Advanced Feature)

## 1. Feature Status & Dependency

> [!NOTE]
> Per the implementation roadmap (`docs/15-implementation/`), Wallet and Gift Cards are classified as **Phase 15 (Advanced Capabilities)**. Core e-commerce operations in Phases 1-14 operate with direct payment gateway transactions and standard refund rails.

## 2. Gift Card Invariants

1. **Purchasability & Delivery**:
   - Digital gift cards are purchased like standard catalog items and delivered electronically via email with a unique 16-character alphanumeric code and 6-digit PIN.
2. **Redemption & Balance**:
   - Customers redeem gift cards during checkout by entering code + PIN.
   - Gift card balances can be redeemed across multiple orders until depleted.
   - Unused balances expire after 12 months from issuance date in accordance with applicable consumer credit laws.
3. **Non-Refundable**:
   - Gift card purchases cannot be refunded or converted to cash.

## 3. Customer Store Credit Wallet Invariants

1. **Closed-Loop Wallet**:
   - The platform wallet is strictly a **Closed-Loop System**: funds can only be spent on merchandise sold within this single-owner store.
   - Wallet funds cannot be transferred to third-party bank accounts, peer-to-peer, or external merchants.
2. **Ledger Integrity & Double-Entry Accounting**:
   - A customer's wallet balance is NEVER stored as an unverified mutable integer column.
   - The balance is derived from an immutable double-entry ledger of transactions:
     - `CREDIT`: Store credit granted for approved return refund or merchant promotional compensation.
     - `DEBIT`: Redemption against checkout order total.
3. **Concurrency Locking on Redemption**:
   - Concurrent checkout attempts using the same wallet balance must lock the customer wallet ledger row (`SELECT ... FOR UPDATE`), preventing double-spending race conditions.
