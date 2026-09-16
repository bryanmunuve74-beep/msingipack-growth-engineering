# Tag Documentation - Core Analytics

## Tag: GA4 Event - M-PESA Purchase

### Purpose
Transmits e-commerce purchase metrics directly to GA4 upon a successful M-PESA transaction.

### Business Purpose
Logs official revenue, transaction volume, and conversion rates in GA4 executive dashboards.

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
**Send Ecommerce Data:** Explicitly UNCHECKED. 
*Rationale:* Disabling conflicting automatic e-commerce sending prevents array key conflicts and duplicate transactions. Furthermore, custom event parameters like `saved_utm_source` are registered as custom dimensions in GA4 to provide real-time Looker visibility into channel performance, rather than overwriting the native GA4 attribution model.

---

## Tag: Pixel - M-PESA Purchase

### Purpose
Tracks purchase conversions to Meta Events Manager with fallback numeric parsing and server/client deduplication.

### Business Purpose
Feeds conversion data back to Meta Ads Manager for algorithmic ad targeting and ROAS calculation.

### Tag Type
Custom HTML.

### Trigger
`Custom Event - mpesa_purchase_success`.

### Firing Logic & Fallback Behavior
Extracts `{{dlv - value}}` and processes it through `parseFloat()`. If `isNaN()` returns true (due to an empty data layer state), the script forcefully assigns a fallback value of `300.00` to prevent runtime crashes.

### Dependencies
Requires **Pixel - Base Code** to load `window.fbq` prior to execution. Uses `{{dlv - transaction_id}}` as the `eventID` for deduplication.

---

## Tag: CHTML - UTM Persistence Guard

### Purpose
Captures URL search parameters upon initial landing and writes them to a 1st-party cookie scoped to the parent domain (`.msingipack.com`).

### Business Purpose
Preserves original ad campaign attribution metadata across multi-session and cross-subdomain user journeys, mitigating the issue of direct/none traffic being artificially inflated on the LMS subdomain.

### Tag Type
Custom HTML.

### Trigger
Initialization - All Pages.

### Key Configuration
* **Execution Priority:** 100.
* **Storage Mechanism:** Stored in first-party cookies.
* **Persistence Window:** A 30-day persistence window (`max-age=2592000`) was selected as the project's attribution design choice.
* **Security Flags:** `SameSite=Lax; Secure`.
* **Variables Captured:** `utm_source`, `utm_medium`, `utm_campaign`, `gclid`, `fbclid`, `ttclid`, `gbraid`, `wbraid`. Note: This tag purely handles the *storage* of identifiers like `fbclid` in a first-party cookie; it does not automatically facilitate Meta CAPI matching unless configured downstream.
