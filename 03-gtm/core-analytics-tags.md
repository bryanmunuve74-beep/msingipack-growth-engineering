# Tag Documentation - Core Analytics

## Tag: GA4 Event - M-PESA Purchase

### Purpose
Transmits a verified ecommerce purchase event to GA4 after the `mpesa_purchase_success` event is received.

### Business Purpose
Measures transaction count and revenue in GA4 while keeping the financial source of truth in M-PESA/Bank records.

### Tag Type
GA4 Event (Event Name: `purchase`).

### Trigger
`Custom Event - mpesa_purchase_success`.

### Variables Used
* `transaction_id`: `{{dlv - transaction_id}}`
* `value`: `{{dlv - value}}`
* `currency`: `KES`
* `saved_utm_source`: `{{cjs - Saved UTM Source}}`
* `items`: `{{CJS - GA4 Ecommerce Items}}`

### Destination
Google Analytics 4.

### Key Configuration
**Send Ecommerce Data:** Explicitly UNCHECKED in the documented configuration.

The purchase tag sends the required ecommerce parameters explicitly. Custom attribution fields such as `saved_utm_source` are supporting reporting fields and do not replace GA4's native acquisition or attribution dimensions.

### Validation Requirements
The purchase tag should only fire when:
* `transaction_id` is present and non-empty.
* `value` is a valid positive number.
* The Data Layer event represents a verified successful payment.

If these conditions are not met, the purchase signal should be blocked rather than assigned a synthetic revenue value.

---

## Tag: Pixel - M-PESA Purchase

### Purpose
Tracks verified purchase conversions to Meta Events Manager using a stable event identifier for deduplication.

### Business Purpose
Provides Meta with purchase conversion signals for measurement and campaign optimization.

### Tag Type
Custom HTML.

### Trigger
`Custom Event - mpesa_purchase_success`.

### Firing Logic
The tag reads `{{dlv - value}}` and validates that it resolves to a valid numeric amount. Invalid or missing values should prevent the purchase signal from being sent.

### Dependencies
Requires **Pixel - Base Code** to have initialized `window.fbq` before execution. The documented implementation uses `{{dlv - transaction_id}}` as the Meta event identifier.

This is a client-side Pixel implementation. Meta CAPI/server-side delivery is not part of the current implementation and is documented as a future enhancement.

---

## Tag: CHTML - UTM Persistence Guard

### Purpose
Captures URL campaign parameters on landing and writes them to first-party cookies scoped to `.msingipack.com`.

### Business Purpose
Preserves campaign metadata across navigation and subdomain transitions so downstream conversion events can retain supporting attribution context.

### Tag Type
Custom HTML.

### Trigger
Initialization - All Pages.

### Key Configuration
* **Execution Priority:** 100.
* **Storage Mechanism:** First-party cookies.
* **Persistence Window:** 30 days (`max-age=2592000`).
* **Security Flags:** `SameSite=Lax; Secure`.
* **Variables Captured:** `utm_source`, `utm_medium`, `utm_campaign`, `gclid`, `fbclid`, `ttclid`, `gbraid`, `wbraid`, subject to the current implementation/configuration.

The tag only persists identifiers. It does not by itself implement Meta CAPI matching or server-side conversion delivery.
