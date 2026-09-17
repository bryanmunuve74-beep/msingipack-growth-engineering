# MsingiPACK Event Schema
## Technical Specification & Data Layer Architecture (GTM-K75VD2V6)

### 1. Schema Principles
* **Standardized JSON Architecture:** All tracking data flows through a uniform `window.dataLayer.push()` specification to ensure deterministic event handling across platforms.
* **Defensive Runtime Parsing:** Financial and numerical values incorporate strict type checking (`parseFloat`, `isNaN`) to prevent silent JavaScript exceptions from halting conversion logging.
* **Strict Snake_Case Convention:** Parameter keys adhere strictly to lowercase `snake_case` formatting (e.g., `transaction_id`) to comply with native GA4 e-commerce ingestion rules.

### 2. Naming Convention
* **Custom Events:** Lowercase strings separated by underscores (`mpesa_purchase_success`, `payment_failed`, `sign_up`).
* **Parameter Keys:** Standardized ecommerce and custom dimensions (`transaction_id`, `value`, `currency`, `failure_reason`, `saved_utm_source`).

### 3. Event Definitions

#### 3.1 Registration (`sign_up`)
* **Trigger Condition:** Fired exclusively upon successful database account creation within the Moodle LMS backend, eliminating false positives from empty button taps.
* **Business Value:** Reflects genuine user onboarding rather than mere user intent.

#### 3.2 Purchase (`mpesa_purchase_success`)
* **Trigger Condition:** Executed upon successful Daraja STK push payment completion and transaction verification.
* **Payload Implementation:**
```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  'event': 'mpesa_purchase_success',
  'value': 2400.00,
  'transaction_id': 'NL810XX99'
});
```

#### 3.3 Payment Failed (`payment_failed`)
* **Trigger Condition:** Captured when an M-PESA STK push encounters a timeout, user cancellation, or insufficient funds.
* **Payload Implementation:**
```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  'event': 'payment_failed',
  'failure_reason': 'User cancelled STK push'
});
```

#### 3.4 Login (`login`)
* **Trigger Condition:** Activated when a registered student or parent logs into `academy.msingipack.com`.
* **Business Value:** Measures active platform engagement and identifies idle accounts.

#### 3.5 CTA Interaction (`enrollment_start`)
* **Trigger Condition:** Captured via case-insensitive regular expression matching on parent and school registration buttons.

### 4. Event Parameters
* `event`: String representing the custom GTM event name.
* `transaction_id`: String containing the unique M-PESA receipt code.
* `value`: Number representing the monetary transaction value.
* `currency`: String fixed as `KES`.
* `failure_reason`: String detailing specific error logs on failed payments.
* **Attribution Parameters:** `saved_utm_source`, `saved_utm_medium`, `saved_utm_campaign`, `fbclid`, `gclid` stored via 30-day 1st-party cookies (`.msingipack.com`).

### 5. Data Types
* **String:** Used for identifiers, event descriptors, error logs, and attribution tokens.
* **Number:** Used for financial amounts and numerical quantities.
* **Array / Object:** Used for structured e-commerce item payloads.

### 6. Required vs Optional Parameters
* **Required Parameters (Purchase):** `event`, `transaction_id`, `value`.
* **Optional / Enriched Parameters:** `currency`, `items`, attribution tokens (`saved_utm_*`).

### 7. Event → GTM Mapping
Custom events push directly into `window.dataLayer` and are processed inside container `GTM-K75VD2V6` using Custom Event triggers and variable extractors.

### 8. Event → GA4 Mapping
Mapped to GA4 e-commerce events where `transaction_id` maps to transaction ID, `value` maps to revenue, and `currency` maps to `KES` with native e-commerce aggregation enabled.

### 9. Event → Meta Mapping
Mapped to Meta Pixel standard events (`Purchase`), using the unique M-PESA receipt ID as the event ID for server/client deduplication.

### 10. Transaction Identity
Anchored strictly by the M-PESA receipt code (`transaction_id`), ensuring a 1-to-1 match against financial ledgers and eliminating discrepancies.

### 11. Deduplication
Meta event deduplication is enforced via event ID matching. GA4 duplicate transaction suppression is achieved by utilizing strict `snake_case` formatting and preventing accidental re-triggers on confirmation page reloads.

### 12. Validation Rules & Flow
Numerical fields must pass defensive parsing (`parseFloat`, `isNaN`). Automated Cypress end-to-end test suites (`analytics_governance.cy.js`) validate event schemas prior to production deployment.

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
    GA4        Meta
```