# Problems Found

## 1. Overview
This document inventories the technical problems discovered during the MsingiPACK tracking investigation. It separates observed symptoms from the technical causes supported by testing.

## 2. Problem Classification
Problems are categorized into:
* **Attribution & Routing:** Campaign parameters and measurement continuity across the marketing site and Moodle.
* **Registration & Funnel Logic:** UI intent being measured as a completed business outcome.
* **Data Integrity:** Duplicate or incomplete conversion measurement.
* **Performance:** Front-end loading issues identified in the separate performance audit.

## 3. Acquisition / Routing Problems
* **Symptom:** Paid traffic landing on protected course URLs could lose campaign parameters after Moodle authentication redirects.
* **Verified Cause:** The protected request can redirect unauthenticated users to `/login/index.php`; the original query parameters were not retained in the resolved URL.

## 4. Registration / Funnel Problems
* **Symptom:** Registration measurement could represent CTA intent rather than a completed account.
* **Verified Cause:** The earlier GTM registration trigger was tied to a front-end interaction instead of confirmed Moodle account creation.

## 5. Attribution Problems
* **Symptom:** Campaign metadata was not reliably available later in the LMS journey.
* **Verified Cause:** URL parameters disappear during navigation and were not previously persisted before the Moodle authentication flow.

## 6. Conversion Tracking Problems
* **Symptom:** Activation could not always be cleanly connected to the earlier acquisition context.
* **Verified Cause:** Registration and activation are distinct states, and client-side attribution persistence does not automatically create cross-device identity continuity.

## 7. Payment / Revenue Measurement Problems
* **Symptom:** Historical ecommerce reporting contained duplicate purchase events relative to unique transactions.
* **Verified Cause:** Purchase measurement could be retriggered by success-page reloads without reliable transaction identity.

## 8. Analytics Configuration Problems
* **Symptom:** Native GA4 session continuity required explicit validation across the marketing site and Academy.
* **Verified Cause:** GA4 measurement configuration and custom attribution persistence are separate mechanisms and must not be treated as interchangeable.

## 9. Performance Problems
* **Symptom:** The separate PageSpeed audit identified substantial mobile loading and rendering bottlenecks.
* **Verified Cause:** Large image payloads, render-blocking resources, and Moodle page structure contributed to the measured performance problems. Detailed findings are documented in `08-performance/`.

## 10. Data Integrity Problems
* **Symptom:** Historical GA4 purchase measurement contained duplicate transactions.
* **Verified Cause:** Purchase events were not consistently tied to a stable transaction identifier in the earlier implementation.

## 11. Problem Severity
* **Critical:** Registration measurement misalignment; campaign parameter loss on protected redirects.
* **High:** Historical purchase duplication and attribution continuity issues.
* **Medium:** Activation attribution limitations and performance-related conversion friction.

## 12. Problem Summary Table

| ID | Problem | System | Symptom | Business Impact | Severity | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| P-001 | Parameter stripping | Moodle | Paid traffic loses URL campaign parameters | Attribution loss | Critical | Fix implemented / validation documented |
| P-002 | Registration fires on CTA | GTM | Intent can be counted as registration | Inflated conversion counts | Critical | Fix implemented / validation documented |
| P-003 | Purchase duplicates | GA4/GTM | Multiple events for one transaction | Revenue measurement distortion | High | Transaction-ID architecture implemented |
| P-004 | Attribution/session continuity | GA4/GTM | Native session continuity cannot be assumed from custom cookies | Attribution uncertainty | High | Architecture/configuration documented |
| P-005 | Activation attribution | Moodle/GA4 | Cross-device journeys may lose continuity | Incomplete journey analysis | Medium | Partially addressed; ongoing limitation |
| P-006 | Mobile performance | Moodle | Heavy payload and delayed rendering | Landing-page friction | High | Documented; deployment/re-audit dependent on access |

## 13. Scope Boundary
Performance findings are not represented as tag-execution problems. They are a separate technical workstream documented under `08-performance/`.
