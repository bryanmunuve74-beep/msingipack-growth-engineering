# MsingiPACK Tracking Architecture

## 1. Overview
The MsingiPACK tracking architecture provides end-to-end visibility across marketing acquisition, user onboarding, learning engagement, and financial conversion. It separates business truth from client-side measurement so analytics events can be validated against Moodle and payment records.

## 2. Business Requirements
* **Accurate Acquisition Tracking:** Capture campaign parameters and traffic sources across digital channels.
* **Cross-Subdomain Attribution Continuity:** Preserve campaign metadata when users move from `msingipack.com` to `academy.msingipack.com`.
* **LMS Engagement & Activation Measurement:** Track registration, login, and relevant learning actions within Moodle.
* **Financial Reconciliation:** Reconcile digital conversion events with M-PESA and bank records.

## 3. Systems Involved

| System | Role |
| :--- | :--- |
| `msingipack.com` | Marketing and acquisition environment. |
| `academy.msingipack.com` | Moodle/LMS environment for registration, learning, and payment flows. |
| Google Tag Manager (GTM) | Client-side tag orchestration and event routing. |
| Google Analytics 4 (GA4) | Behavioral and ecommerce measurement. |
| Meta Pixel | Advertising conversion measurement. |
| M-PESA / Bank | Financial source of truth. |
| Moodle | Application, account, and enrollment state. |

Meta CAPI, GA4 Measurement Protocol, and GTM Server-Side are future architecture options rather than implemented components of this phase.

## 4. Domain Architecture

```text
msingipack.com (Marketing & Acquisition)
       │
       │ navigation
       ▼
academy.msingipack.com (Moodle LMS)
```

Both environments are subdomains of the same root domain. The custom attribution cookies are scoped to `.msingipack.com` so they can be read by both hosts.

### Measurement Implications
* **Attribution Cookie Scope:** Saved campaign parameters use root-domain first-party cookies.
* **GA4 Measurement:** Native GA4 identity/session continuity is a separate concern from custom attribution persistence and must be validated through the GA4 configuration and linker behavior where applicable.

## 5. Tracking Architecture Diagram

```text
                             USER
                              │
                              ▼
        msingipack.com / academy.msingipack.com
                              │
               ┌──────────────┴──────────────┐
               ▼                             ▼
        URL Parameters                  User Actions
       (UTM / FBCLID)              (Clicks / Forms / LMS Events)
               │                             │
               ▼                             ▼
       Attribution Layer                 Data Layer
               │                     (window.dataLayer)
               └──────────────┬──────────────┘
                              │
                              ▼
                      Google Tag Manager
                              │
                         ┌────┴────┐
                         ▼         ▼
                        GA4      Meta Pixel
                              │
                              ▼
                  Moodle / M-PESA Reconciliation
```

## 6. Data Collection Layer
* **Data Layer:** Structured application events are pushed to `window.dataLayer`.
* **GTM Native Tracking:** Standard interaction events can be collected using GTM triggers where appropriate.
* **Critical Events:** Registration and verified payment events use explicit application-level event pushes rather than treating UI clicks as business outcomes.

## 7. Processing Layer
* **Trigger Orchestration:** GTM determines when tags should fire.
* **Data Enrichment:** Custom variables can read persisted attribution data and validated event parameters.
* **Financial Validation:** Purchase measurement requires a valid transaction identifier and numeric value.

## 8. Destination Systems
* **GA4:** Receives behavioral and ecommerce events.
* **Meta Pixel:** Receives supported advertising conversion events.

Server-side delivery is not part of this phase.

## 9. Attribution Architecture
* **Parameter Capture:** `utm_source`, `utm_medium`, `utm_campaign`, and `fbclid` are captured where present.
* **Persistence:** Selected identifiers are written to `.msingipack.com` first-party cookies with a documented 30-day TTL.
* **Reporting Boundary:** Persisted fields can be sent as supporting event parameters; they do not automatically overwrite GA4's native attribution model.

## 10. Conversion Architecture
Conversions are modeled hierarchically:
1. **Intent:** CTA/enrollment interactions.
2. **Registration:** Confirmed `sign_up`.
3. **Activation:** Login/meaningful learning activity where implemented.
4. **Purchase:** Verified `mpesa_purchase_success` mapped to GA4 `purchase` and Meta `Purchase`.

## 11. Identity / Transaction Architecture
* **User Identity:** Authenticated identifiers may be used where the implementation supports them and privacy requirements are satisfied.
* **Transaction Identity:** The M-PESA receipt code is the primary transaction identifier used to reconcile analytics with the financial record.

## 12. Architecture Decisions
* **Root-Domain Cookie Strategy:** Used for custom campaign metadata persistence across the two MsingiPACK subdomains.
* **Client-Side GTM:** Used for the current implementation because it can be deployed within the existing web stack without introducing server-side infrastructure.

## 13. Known Limitations
* Browser privacy controls and ad blockers can prevent client-side tags from firing.
* Custom attribution cookies cannot bridge devices.
* A payment can succeed financially even if the browser-side measurement event is lost.
* A future server-side measurement layer could reduce browser-side loss but is outside this phase.
