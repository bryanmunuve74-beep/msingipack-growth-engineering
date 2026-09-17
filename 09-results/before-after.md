# Before & After

## 1. Comparison Method
This document separates **implementation changes** from **business outcomes**. A technical fix can be validated immediately; a change in conversion rate, revenue, ROAS, or CAC requires a defined post-deployment measurement period and cannot be attributed to the tracking implementation alone without appropriate causal evidence.

## 2. Measurement Periods
* **Before Period:** Historical baseline period documented in the business-context/KPI files.
* **After Period:** Not yet finalized for a complete post-deployment business comparison.
* **Reason:** The repository does not currently contain a complete evidence-backed post-deployment reporting period.

## 3. Tracking Architecture
* **Before:** Fragmented measurement, UI-based conversion triggers, attribution loss, and duplicate purchase behavior.
* **After:** Centralized GTM orchestration, standardized Data Layer events, persisted campaign metadata, verified purchase-event structure, and transaction-based deduplication logic.
* **Status:** Implementation change documented; long-term production impact not yet quantified.

## 4. Attribution
* **Before:** Campaign parameters could be lost during navigation and protected Moodle redirects.
* **After:** Campaign parameters are persisted in root-domain first-party cookies for the documented 30-day TTL.
* **Status:** Custom attribution persistence can be validated independently; native GA4 attribution impact requires a measured reporting period.

## 5. Registration Tracking
* **Before:** Registration measurement could fire from CTA intent.
* **After:** Registration is defined around confirmed Moodle account creation and the `sign_up` event.
* **Status:** Corrected event definition and test flow documented.

## 6. Activation Tracking
* **Before:** Registration and activation were not cleanly separated in the measurement model.
* **After:** Registration, login/activation, and purchase are treated as distinct funnel states.
* **Status:** Measurement model improved; cross-device identity remains a limitation.

## 7. Purchase Tracking
* **Before:** Page-load behavior could create duplicate purchase events.
* **After:** Verified purchase events require a stable transaction identifier and valid monetary value.
* **Status:** Architecture and validation documented; a complete post-deployment duplicate rate requires source evidence.

## 8. Payment Failure Tracking
* **Before:** Failed payment states were not consistently separated from successful purchases.
* **After:** `payment_failed` is modeled separately from `mpesa_purchase_success`.
* **Status:** Event architecture documented; long-term failure-rate impact not yet measured.

## 9. Deduplication
* **Before:** Historical baseline recorded 33 purchase events from 4 unique buyers.
* **After:** Transaction identity is used as the primary purchase reconciliation key, with retrigger protection intended to prevent unnecessary repeat dispatches.
* **Status:** Fix implemented/documented; do not claim a sustained 0% duplicate rate without post-deployment evidence.

## 10. Performance
* **Before:** PageSpeed analysis identified a ~41.8 MB mobile payload, 32.3s LCP, and substantial render-blocking resources.
* **After:** Performance recommendations were documented, but a completed post-deployment performance re-audit is not yet evidenced in this repository.

## 11. Data Quality
* **Before:** Conversion definitions and event triggers were not consistently tied to backend business states.
* **After:** Event definitions distinguish intent, registration, activation, payment failure, and verified purchase.

## 12. Before vs After Summary

| Area | Before | After | Status |
| :--- | :--- | :--- | :--- |
| Registration measurement | CTA intent could be counted | Confirmed Moodle account creation / `sign_up` | Implementation validated |
| Purchase identity | Could be missing on repeat page loads | Stable transaction identifier required | Implementation documented |
| Attribution persistence | Campaign parameters could disappear | Root-domain cookie persistence | Custom cookie flow validated |
| Payment failure | Weak separation from purchase | Dedicated `payment_failed` event | Implementation documented |
| Purchase duplication | 33 events from 4 unique buyers in historical baseline | Deduplication architecture implemented | Long-term rate pending |
| Mobile performance | ~41.8 MB payload / 32.3s LCP baseline | Optimization recommendations documented | Re-audit pending |
| Revenue impact | Historical measurement distorted | Measurement architecture improved | Business impact not yet measured |
| ROAS / CAC | Historical baseline available | Post-deployment effect not yet established | Pending |

## 13. Interpretation
The strongest validated outcomes in this phase are **measurement-quality improvements**, not claims of increased revenue or ROAS. Any commercial improvement should be reported separately after a sufficiently defined post-deployment period.
