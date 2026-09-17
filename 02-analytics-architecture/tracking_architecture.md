# MsingiPACK Tracking Architecture

## 1. Overview
The MsingiPACK tracking architecture is engineered to provide end-to-end visibility across marketing acquisition, user onboarding, digital learning engagement, and financial conversion. This document outlines the structural blueprint of the measurement ecosystem, detailing how data flows from user touchpoints on public web properties into analytical destinations and financial sources of truth.

## 2. Business Requirements
* **Accurate Acquisition Tracking:** Capture marketing campaign performance, ad spend attribution, and traffic sources across digital channels.
* **Cross-Domain Session Continuity:** Maintain user session state and attribution identifiers when users transition from the public marketing site (`msingipack.com`) to the LMS portal (`academy.msingipack.com`).
* **LMS Engagement & Activation Measurement:** Track student progression, course enrollments, module completions, and activation milestones within Moodle.
* **Financial Reconciliation:** Reconcile digital conversion funnels with offline or alternative payment methods (M-PESA and bank transfers) to maintain a single source of truth for revenue.

## 3. Systems Involved

| System | Role |
| :--- | :--- |
| `msingipack.com` | Marketing, acquisition environment, and landing pages. |
| `academy.msingipack.com` | Moodle/LMS environment for student registration, activation, and course delivery. |
| Google Tag Manager (GTM) | Client-side tag orchestration, event capturing, and data routing. |
| Google Analytics 4 (GA4) | Behavioral measurement, product analytics, and funnel tracking. |
| Meta Pixel & CAPI | Advertising measurement, custom conversions, and ad optimization. |
| M-PESA / Bank | Primary payment source of truth and offline/mobile transaction reconciliation. |

## 4. Domain Architecture
MsingiPACK operates across a multi-subdomain architecture:

```
msingipack.com (Marketing & Acquisition)
       │
       │ user navigation & cross-domain tracking
       ▼
academy.msingipack.com (Moodle LMS Portal)
```

### Measurement Implications
* **Cookie Scope:** To prevent session fragmentation and drop-offs in attribution when users move from the marketing site to the LMS, all tracking cookies (`_ga`, `_fbp`, and attribution parameter stores) are scoped to the root domain (`.msingipack.com`).
* **Cross-Domain Linker:** GTM's cross-domain linker ensures client IDs are preserved across domain boundaries without stripping query parameters or resetting user sessions during authentication redirects into Moodle.

## 5. Tracking Architecture Diagram

```
                             USER
                              │
                              ▼
        msingipack.com / academy.msingipack.com
                              │
               ┌──────────────┴──────────────┐
               ▼                             ▼
        URL Parameters                  User Actions
     (UTM / FBCLID / GCLID)     (Clicks / Forms / LMS Events)
               │                             │
               ▼                             ▼
       Attribution Layer                 Data Layer
               │                     (window.dataLayer)
               └──────────────┬──────────────┘
                              │
                              ▼
                      Google Tag Manager
                              │
               ┌──────────────┼──────────────┐
               ▼              ▼              ▼
              GA4            Meta       Backend / CRM
               │              │              │
               └──────────────┼──────────────┘
                              │
                              ▼
            Business Data & M-PESA Reconciliation
```

## 6. Data Collection Layer
* **Data Layer Standard:** A standardized `window.dataLayer` object initializes across all pages to collect structured event data.
* **Automated Tracking:** Scroll depth, outbound link clicks, video engagement, and file downloads are tracked uniformly via GTM native triggers.
* **Custom Event Push:** Critical application events (e.g., course enrollment initiated, registration completed) push explicit JSON objects to the data layer with standardized parameters (item ID, price, user tier).

## 7. Processing Layer
* **Trigger Orchestration:** GTM manages all tag firing rules, ensuring consent requirements and page-readiness conditions are met before analytics scripts execute.
* **Data Enrichment:** Custom JavaScript variables extract URL parameters, user roles within Moodle, and session metadata to append to outgoing tracking payloads.

## 8. Destination Systems
* **Google Analytics 4 (GA4):** Receives raw event streams, user properties, and ecommerce metrics to build funnel reports, user journeys, and acquisition channel analyses.
* **Meta Pixel:** Receives core standard events (`PageView`, `Lead`, `Complete Registration`, `InitiateCheckout`, `Purchase`) optimized for ad campaign delivery and custom audience segmentation.

## 9. Attribution Architecture
* **Parameter Capture:** Marketing parameters (`utm_source`, `utm_medium`, `utm_campaign`, `gclid`, `fbclid`) are captured on landing page arrival.
* **Persistence:** Parameters are written to root-domain cookies (`.msingipack.com`) or browser local storage with a 30-day expiration window, ensuring touchpoint data survives multi-page browsing sessions and subdomain transitions into the LMS.

## 10. Conversion Architecture
Conversions are modeled hierarchically across the student lifecycle:
1. **Micro-Conversions:** Resource downloads, newsletter signups, account creation (`sign_up`).
2. **Macro-Conversions:** Course checkout initiation (`begin_checkout`), payment submission, and successful enrollment confirmation (`purchase`).

## 11. Identity / Transaction Architecture
* **Anonymous-to-Known Resolution:** When a user logs into `academy.msingipack.com`, their hashed user ID (UUID) is passed to the data layer and mapped to GA4 `user_id` properties.
* **Transaction Mapping:** M-PESA transaction codes and bank reference numbers captured during checkout are stored alongside the Moodle user ID and passed to analytics systems to reconcile digital intent with confirmed financial settlement.

## 12. Architecture Decisions
* **Root-Domain Cookie Strategy:** Utilized `.msingipack.com` scoping to ensure uncompromised attribution persistence between marketing pages and the Moodle instance.
* **Client-Side GTM Orchestration:** Chosen for rapid deployment and flexibility while maintaining lightweight scripts to protect page load speeds on mobile networks.

## 13. Known Limitations
* **Ad Blocker Interception:** Client-side tag execution can be blocked by browser extensions, impacting Meta Pixel and GA4 sample rates.
* **M-PESA Offline Latency:** Manual or delayed M-PESA transaction validations require secondary reconciliation loops between payment logs and database activation records.