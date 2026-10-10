# Customer Business Rules

## 1. Registration & Identification Invariants

1. **Unique Identifiers**: A customer account must be uniquely identified by an immutable `customerId` (UUID v7). Both `email` and normalized `phoneNumber` (E.164 format, e.g. `+919876543210`) must be unique across the platform.
2. **Verification Requirements**:
   - Accounts created via email must be verified via an email activation link or 6-digit OTP before placing orders.
   - Accounts created via mobile number must be verified via an SMS OTP before placing orders.
3. **Password Security**:
   - Passwords must be at least 8 characters long, containing uppercase, lowercase, numeric, and special characters.
   - Passwords must be hashed using BCrypt (cost factor 12) or Argon2id. Plaintext passwords must never touch logs or persistent storage.
   - Account lockout: 5 consecutive failed login attempts lock the account for 15 minutes, triggering a security alert email.

## 2. Address Book Rules

1. **Address Capacity**: A customer may store up to 10 shipping addresses and 5 billing addresses.
2. **Default Address Invariant**: Exactly one shipping address must be designated as `is_default = true`. If a customer deletes their default address, the most recently updated remaining address is automatically promoted to default.
3. **Address Snapshotting**: Once an order is placed, subsequent edits or deletions to the customer's address book must NEVER mutate the shipping address snapshot stored inside existing orders.

## 3. Communication Preferences & Consent

1. **Granular Consent**: Customers may independently toggle marketing communications for Email, SMS, and WhatsApp.
2. **Transactional Mandatory Rule**: Transactional notifications (order confirmations, payment receipts, OTPs, delivery updates) cannot be disabled by the customer, as they are essential to contract fulfillment.
3. **Data Deletion / Privacy**: Upon verified customer deletion request, personal identifying data (name, email, phone) is anonymized in customer records. Order history records retain financial and tax compliance snapshots as required by law for 7 years.
