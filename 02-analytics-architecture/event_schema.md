# MsingiPACK Event Schema
## Technical Specification & Data Layer Architecture (GTM-K75VD2V6)

### 1. Schema Principles
* **Standardized Data Layer:** Tracking data uses a consistent `window.dataLayer.push()` structure so GTM can process events deterministically.
* **Defensive Validation:** Financial values are validated before purchase measurement is sent.
* **Consistent Naming:** Event and parameter names use the documented GA4/GTM schema, including `transaction_id`, `value`, `currency`, and `failure_reason`.

### 2. Naming Convention
* **Custom Events:** Lowercase strings separated by underscores (`mpesa_purchase_success`, `payment_failed`, `sign_up`).
* **Parameter Keys:** Standard ecommerce and custom fields (`transaction_id`, `value`, `currency`, `failure_reason`, `saved_utm_source`).

### 3. Event Definitions

#### 3.1 Registration (`sign_up`)
* **Trigger Condition:** Fired after successful account creation in Moodle, rather than on a registration CTA click.
* **Business Value:** Represents completed account creation rather than user intent.

#### 3.2 Purchase (`mpesa_purchase_success`)
* **Trigger Condition:** Fired after successful M-PESA payment verification and availability of a unique transaction identifier.
* **Payload Example:**
```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  event: 'mpesa_purchase_success',
  value: 2400.00,
  currency: 'KES',
  transaction_id: 'NL810XX99'
});
```

The monetary value shown above is an illustrative example, not a fallback value.

#### 3.3 Payment Failed (`payment_failed`)
* **Trigger Condition:** Captured when the payment flow returns a known failure/cancellation state.
* **Payload Example:**
```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  event: 'payment_failed',
  failure_reason: 'User cancelled STK push'
});
```

#### 3.4 Login (`login`)
* **Trigger Condition:** Activated when a registered student or parent successfully logs into `academy.msingipack.com`.

#### 3.5 CTA Interaction (`enrollment_start`)
* **Trigger Condition:** Captured from the documented registration/enrollment CTA interaction.

### 4. Event Parameters
* `event`: Custom GTM event name.
* `transaction_id`: Unique M-PESA receipt identifier for verified purchases.
* `value`: Numeric transaction value.
* `currency`: `KES`.
* `failure_reason`: Description/category of a failed payment.
* **Attribution Parameters:** Saved UTM and click-identifier fields may be supplied from the attribution persistence layer.

### 5. Data Types
* **String:** Identifiers, event names, and error descriptors.
* **Number:** Financial amounts and quantities.
* **Array / Object:** Structured ecommerce item payloads where implemented.

### 6. Required vs Optional Parameters
* **Required for Purchase:** `event`, `transaction_id`, `value`.
* **Ecommerce/Enrichment:** `currency`, `items`, and saved attribution fields as applicable.

### 7. Event → GTM Mapping
Custom events are pushed into `window.dataLayer` and processed by `GTM-K75VD2V6` using Custom Event triggers and Data Layer Variables.

### 8. Event → GA4 Mapping
The internal `mpesa_purchase_success` event is mapped to the GA4 recommended `purchase` event. `transaction_id`, `value`, `currency`, and `items` are mapped to the corresponding ecommerce parameters where available.

GA4 purchase deduplication depends on a consistent `transaction_id`; the naming style itself is not the deduplication mechanism.

### 9. Event → Meta Mapping
The internal purchase event is mapped to the Meta Pixel `Purchase` event. The documented implementation uses the transaction identifier as the event identifier for the client-side purchase signal.

Meta CAPI/server-side deduplication is not part of the current implementation.

### 10. Transaction Identity
The transaction identifier is anchored to the M-PESA receipt code. It provides the key used to reconcile the analytics purchase with the financial transaction.

### 11. Deduplication
* **GA4:** Uses `transaction_id` as the ecommerce transaction identifier for duplicate purchase handling.
* **Meta:** Uses the documented event identifier for the client-side purchase signal. Any Pixel/CAPI event deduplication would require the corresponding server-side implementation and matching event IDs.

### 12. Validation Rules & Flow
Purchase values must be valid numeric values and transaction identifiers must be present. Invalid financial data should block the purchase signal rather than generate synthetic revenue.

The project includes manual GTM Preview, browser, and GA4 validation. Cypress automation is documented as a future regression-testing capability rather than a completed production test suite.

```
mpesa_purchase_success
    │
    ├──► transaction_id
    └──► value
           │
           ▼
          GTM
           │
     ┌─────┴─────┐
     ▼           ▼
    GA4        Meta Pixel
```
