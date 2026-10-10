# Entities and Value Objects

This document defines core domain Entities (objects with identity) and Value Objects (immutable descriptors without identity) across bounded contexts.

---

## 1. Value Objects

Value objects are immutable, equality-checked by value rather than identity, and encapsulate self-validating domain logic:

```java
// Money Value Object
public record Money(BigDecimal amount, Currency currency) {
    public Money {
        Objects.requireNonNull(amount, "Amount cannot be null");
        Objects.requireNonNull(currency, "Currency cannot be null");
        if (amount.compareTo(BigDecimal.ZERO) < 0) {
            throw new IllegalArgumentException("Amount cannot be negative");
        }
    }
    public Money add(Money other) {
        validateSameCurrency(other);
        return new Money(this.amount.add(other.amount), this.currency);
    }
    public Money subtract(Money other) {
        validateSameCurrency(other);
        return new Money(this.amount.subtract(other.amount), this.currency);
    }
    private void validateSameCurrency(Money other) {
        if (!this.currency.equals(other.currency)) {
            throw new IllegalArgumentException("Cannot operate across different currencies");
        }
    }
}
```

### Other Key Value Objects:
- **`Sku`**: Normalizes alphanumeric SKU codes (e.g. `TSHIRT-NAVY-L`).
- **`Address`**: Postal address (`recipientName`, `streetLine1`, `streetLine2`, `city`, `state`, `pincode`, `country`, `phone`).
- **`Dimensions`**: Physical parcel metrics (`lengthCm`, `widthCm`, `heightCm`, `weightGrams`).
- **`TaxBreakdown`**: Detailed tax components (`taxableAmount`, `cgstAmount`, `sgstAmount`, `igstAmount`, `ratePercent`).
- **`DiscountValue`**: Discount calculation specification (`type`: PERCENTAGE / FLAT, `value`: BigDecimal, `maxCap`: Money).
- **`TrackingNumber`**: Carrier Air Waybill descriptor (`carrierName`, `awbNumber`, `trackingUrl`).

---

## 2. Core Entities

Entities possess a persistent identity that endures across state changes:

- **`UserCredentials`** (`identity-service`): `userId`, `email`, `phone`, `passwordHash`, `accountStatus`, `failedLoginCount`, `lockedUntil`.
- **`CustomerProfile`** (`customer-service`): `customerId`, `fullName`, `avatarUrl`, `isEmailVerified`, `isPhoneVerified`.
- **`CustomerAddress`** (`customer-service`): `addressId`, `customerId`, `addressType`, `isDefault`, `addressData`.
- **`Product`** (`catalog-service`): `productId`, `title`, `slug`, `brandId`, `categoryId`, `description`, `status`.
- **`ProductVariant`** (`catalog-service`): `variantId`, `productId`, `sku`, `barcode`, `attributesJson`, `weightGrams`.
- **`InventoryAllocation`** (`inventory-service`): `allocationId`, `warehouseId`, `sku`, `quantity`, `orderId`, `status`, `expiresAt`.
- **`OrderLineItem`** (`order-service`): `orderItemId`, `orderId`, `sku`, `title`, `variantSnapshotJson`, `unitPrice`, `quantity`, `taxAmount`, `lineTotal`.
- **`PaymentTransaction`** (`payment-service`): `paymentId`, `orderId`, `gatewayProvider`, `gatewayOrderId`, `amount`, `status`, `webhookPayload`.
- **`ReturnRequest`** (`return-service`): `returnId`, `orderId`, `customerId`, `reasonCode`, `proofMediaUrls`, `status`, `qcResult`.
- **`NotificationMessage`** (`notification-service`): `messageId`, `recipientId`, `channel`, `templateId`, `renderedContent`, `deliveryStatus`.
