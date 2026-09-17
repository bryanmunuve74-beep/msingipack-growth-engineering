# MsingiPACK Measurement Plan

## 1. Measurement Objectives
* Accurately track marketing acquisition channels and campaign performance from ad click to revenue conversion.
* Establish reliable cross-domain tracking and 30-day attribution persistence between `msingipack.com` and `academy.msingipack.com`.
* Measure authentic user onboarding, course registrations, and platform engagement within Moodle.
* Reconcile digital funnels with verified M-PESA and bank transactions to maintain a 1-to-1 revenue source of truth.

## 2. Funnel Stages
* **Awareness / Acquisition:** Ad click, landing page arrival, and campaign parameter (`utm_*`, `fbclid`, `gclid`) capture.
* **Consideration / Intent:** Course preview interaction (`preview_courses.php`) and CTA engagement on parent/school registration buttons.
* **Activation / Registration:** Completed account creation stored in the Moodle database (`sign_up`).
* **Monetization / Conversion:** M-PESA STK push execution and verified payment (`mpesa_purchase_success`).
* **Retention:** Active user login and ongoing portal engagement.

## 3. Business Questions

| Business Question | Measurement Event | Source | Destination |
| :--- | :--- | :--- | :--- |
| Are users reaching the site? | `page_view` | Website (`msingipack.com`) | GA4 |
| Are users showing registration intent? | CTA interaction / click | Website / GTM | GA4 / Meta |
| Are users actually registering? | Confirmed registration (`sign_up`) | Moodle | GA4 / Meta |
| Are users activating? | First login (`login`) | Moodle | GA4 |
| Are users purchasing? | Verified payment (`mpesa_purchase_success`) | M-PESA / Moodle | GA4 / Meta |
| Why are payments failing? | Failed payment (`payment_failed`) | Moodle / Payment Gateway | GA4 / Meta |

## 4. Events to Track
* `page_view`: Captured across all pages for session initialization and traffic source attribution.
* **CTA Click / Enrollment Start:** Case-insensitive regular expression tracking for parent and school registration button interactions.
* `sign_up` **(Registration):** Triggered exclusively upon successful database account creation in Moodle.
* `begin_checkout`: Initiated when users enter the active payment workflow.
* `mpesa_purchase_success`: Triggered on successful Daraja STK push with a unique transaction ID and parsed numeric value.
* `payment_failed`: Triggered upon M-PESA timeout, cancellation, or insufficient funds, capturing explicit error reasons.

## 5. Parameters to Capture
* **Attribution Parameters:** `saved_utm_source`, `saved_utm_medium`, `saved_utm_campaign`, `fbclid`, and `gclid` preserved via secure 1st-party cookies (`.msingipack.com`).
* **Ecommerce Parameters:** `transaction_id` (strict `snake_case`), `value` (parsed defensively via `parseFloat`), `currency` (`KES`), and structured items arrays.
* **Operational Parameters:** `failure_reason` for failed payments and user UUID upon login.

## 6. Conversion Definitions
* **Micro-Conversion:** Account registration completed (`sign_up`) verified by database record entry rather than empty button clicks.
* **Macro-Conversion:** Verified M-PESA subscription purchase (`mpesa_purchase_success`) tied to a unique receipt identifier.

## 7. Data Sources
* `msingipack.com` frontend DOM and URL query strings.
* `academy.msingipack.com` (Moodle LMS) frontend actions and user database triggers.
* Daraja M-PESA API and payment gateway confirmation logs.

## 8. Destination Platforms
* **Google Analytics 4 (GA4):** Behavioral data, ecommerce funnel tracking, and standardized revenue reporting via `GTM-K75VD2V6`.
* **Meta Events Manager (Pixel & CAPI):** Ad optimization, custom conversions, and deduplicated purchase events using `eventID`.

## 9. KPI Mapping
* **Acquisition Efficiency:** Cost Per Acquisition (CPA) calculated using deduplicated 1:1 transaction counts.
* **Funnel Conversion Rate:** Ratio of verified registrations (`sign_up`) to ad clicks, and verified purchases (`mpesa_purchase_success`) to total registrations.
* **Attribution Retention Rate:** Percentage of returning users correctly mapped to their original paid campaign via 30-day cookies.

## 10. Measurement Integrity Requirements
* **Defensive Parsing:** JavaScript wrappers (`parseFloat`, `isNaN`) must sanitize numerical transaction values to prevent runtime exceptions.
* **Deduplication:** Purchase events must utilize the M-PESA receipt ID (`transaction_id`) and Meta `eventID` to eliminate duplicate sales signals from page refreshes.
* **Cross-Domain Persistence:** Secure 1st-party cookies scoped to `.msingipack.com` must lock campaign parameters for 30 days.

## 11. Validation Requirements
* Automated Cypress E2E test suites (`analytics_governance.cy.js`) must verify cookie persistence, case-insensitive CTA triggers, and data layer event schemas on deployment.
* Manual validation must confirm 1-to-1 matching between M-PESA ledgers and GA4/Meta event logs.

## 12. Known Measurement Limitations
* Client-side tracking may be affected by browser ad blockers impacting sample rates.
* Manual or delayed offline payment reconciliations require synchronization between payment logs and active database records.