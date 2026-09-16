# GTM Container Architecture (GTM-K75VD2V6)

## 1. Container Overview
This container (ID: GTM-K75VD2V6) manages the analytics, marketing tags, and custom JavaScript infrastructure for the Msingi PACK ecosystem. It is engineered to ensure measurement reliability by handling missing data gracefully, maintaining cross-subdomain attribution, and deduplicating e-commerce transactions.

## 2. Container Scope
* **Marketing Site:** `msingipack.com` (Acquisition, lead capture, value proposition).
* **LMS / Academy:** `academy.msingipack.com` (Registration, onboarding, M-PESA payment flows).

## 3. Architecture Principles
* **Priority-Driven Execution:** Foundational scripts (e.g., UTM persistence) must execute before standard pageview or interaction tags.
* **Defensive Parsing:** All monetary values undergo `parseFloat()` and `isNaN()` validation to prevent silent JS runtime exceptions during empty data layer states.
* **Single-Responsibility Triggers:** Triggers are scoped to exact interaction types (e.g., pure CSS selector matching for visibility, entirely decoupled from click-text).

## 4. Execution Priority
1. **Priority 100:** `CHTML - UTM Persistence Guard` (captures and stores query parameters).
2. **Standard Priority:** Base pixels (GA4 Configuration, Meta Base Pixel).
3. **Event-Driven:** Behavioral tags (Clicks, Visibility) and Transactional tags (`mpesa_purchase_success`, `payment_failed`).

## 5. Dependency Relationships
The container relies on a strict flow of data from interaction to outbound payload. 

### Purchase Flow
`mpesa_purchase_success` (DataLayer Push)
        ↓
`Custom Event - mpesa_purchase_success` (Trigger)
        ↓
`{{dlv - value}}`, `{{dlv - transaction_id}}`, `{{CJS - GA4 Ecommerce Items}}` (Variables)
        ↓
`GA4 Event - M-PESA Purchase` + `Pixel - M-PESA Purchase` (Tags)
        ↓
GA4 & Meta Events Manager (Destinations)

### Attribution Flow
URL Query String
        ↓
`CHTML - UTM Persistence Guard` (Priority 100)
        ↓
`.msingipack.com` 1st-Party Cookie
        ↓
`{{cjs - Saved UTM Source}}` (Variable)
        ↓
`GA4 Event - M-PESA Purchase` (Tag)

## 6. Illustrative User Journey
To demonstrate how this architecture maintains state across subdomains:
1. **Day 0:** User clicks a Facebook ad and lands on `msingipack.com?utm_source=facebook&fbclid=123`.
2. **Day 0 (Execution):** The Priority 100 CHTML tag reads the URL and writes `utm_source` and `fbclid` into a 1st-party cookie scoped to `.msingipack.com`.
3. **Day 5:** User returns directly to `academy.msingipack.com` and completes an M-PESA payment.
4. **Day 5 (Execution):** The GA4 Purchase tag reads the 1st-party cookie via the `{{cjs - Saved UTM Source}}` variable and appends it as a custom parameter (`saved_utm_source`). *Note: This feeds a custom dimension for Looker explorations; it does not automatically overwrite GA4's native session attribution model*.

## 7. Known Constraints & Business Risks
* **Hardcoded Financial Fallbacks:** To prevent JavaScript syntax errors (`SyntaxError`) causing total tracking failure on the Meta Pixel, `value` defaults to `300.00` if parsing fails. In the GA4 Items array, a missing price defaults to `2400.00`. 
* **Business Risk:** If the backend data layer frequently fails to populate `value`, the system will report false, hardcoded revenue metrics. This architecture prioritizes script execution stability over absolute data silence; robust backend validation of the data layer push is critical.
