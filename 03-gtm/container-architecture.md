# GTM Container Architecture (GTM-K75VD2V6)

## 1. Container Overview
This container (ID: `GTM-K75VD2V6`) manages the client-side analytics and marketing measurement layer for the MsingiPACK ecosystem. It coordinates Data Layer events, attribution persistence, GA4 events, Meta Pixel events, and supporting variables/custom JavaScript.

The architecture is intentionally separated from the financial source of truth: M-PESA/Bank confirms the transaction, Moodle represents application state, and GTM routes verified client-side measurement signals to analytics platforms.

## 2. Container Scope
* **Marketing Site:** `msingipack.com` (acquisition, landing pages, lead capture).
* **LMS / Academy:** `academy.msingipack.com` (registration, onboarding, M-PESA payment flows).

## 3. Architecture Principles
* **Priority-Driven Execution:** Foundational attribution capture executes before downstream conversion tags.
* **Defensive Parsing:** Monetary values are validated before a purchase event is allowed to fire.
* **Single-Responsibility Triggers:** Triggers are scoped to the event or interaction they actually measure.
* **No Synthetic Revenue:** Missing or invalid transaction data blocks a purchase signal rather than inventing a fallback value.
* **Client-Side Boundary:** GTM is used for browser-side orchestration. Meta CAPI and GA4 server-side Measurement Protocol delivery are future extensions, not part of the current container architecture.

## 4. Execution Priority
1. **Priority 100:** `CHTML - UTM Persistence Guard` (captures incoming campaign parameters).
2. **Standard Priority:** Base analytics/pixel tags.
3. **Event-Driven:** Registration, interaction, `mpesa_purchase_success`, and `payment_failed` events.

## 5. Dependency Relationships
The container relies on a strict flow of data from user/application state to outbound measurement.

### Purchase Flow
`mpesa_purchase_success` (Data Layer Push)
        ↓
`Custom Event - mpesa_purchase_success` (Trigger)
        ↓
`{{dlv - value}}`, `{{dlv - transaction_id}}`, `{{CJS - GA4 Ecommerce Items}}` (Variables)
        ↓
Validation of transaction_id + numeric value
        ↓
`GA4 Event - M-PESA Purchase` + `Pixel - M-PESA Purchase`
        ↓
GA4 & Meta Events Manager

### Attribution Flow
URL Query String
        ↓
`CHTML - UTM Persistence Guard` (Priority 100)
        ↓
Root-domain first-party cookie
        ↓
Cookie / attribution variable
        ↓
Conversion event enrichment

## 6. Illustrative User Journey
1. **Day 0:** User clicks a tagged Meta ad and lands on `msingipack.com?utm_source=facebook&fbclid=123`.
2. **Day 0:** The persistence tag stores the available identifiers in root-domain cookies.
3. **Day 5:** User returns to `academy.msingipack.com` and completes an M-PESA payment.
4. **Day 5:** The purchase event carries the verified transaction identifier and numeric value. Saved attribution fields may be attached as custom reporting parameters.

## 7. Purchase Data Integrity Rules
* `transaction_id` must be present and non-empty.
* `value` must resolve to a valid positive number before the purchase tag fires.
* A malformed amount or missing transaction identifier causes the purchase signal to be blocked and logged for investigation.
* No hardcoded KES 300 or KES 2400 fallback is used for financial reporting.

## 8. Attribution Reporting Boundary
Saved UTM values such as `saved_utm_source` are custom event parameters for analysis. They do **not** automatically overwrite GA4's native session/source attribution model.

Native GA4 attribution should be evaluated from the GA4 acquisition dimensions and configured measurement architecture; custom saved UTM fields should be treated as supporting diagnostic/reporting data.

## 9. Known Constraints & Business Risks
* Browser privacy controls and ad blockers can prevent client-side tags from firing.
* If a valid backend payment occurs but the browser never receives or executes the Data Layer event, analytics may undercount the financial source of truth.
* Cross-device journeys remain difficult to reconcile without a durable authenticated identity strategy.
* A future server-side implementation could reduce browser-side loss, but it is outside the current implementation scope.
