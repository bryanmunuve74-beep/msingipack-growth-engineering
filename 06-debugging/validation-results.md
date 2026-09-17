# Validation Results

## 1. Overview
This document summarizes the documented validation work and distinguishes completed manual checks from future automation or measurements that are not yet evidenced in the repository.

## 2. Attribution Validation
* **Fix:** Campaign parameters are captured/persisted before the relevant Moodle redirect where the page can execute the persistence layer.
* **Validation:** The documented test confirmed that the persisted values remained available through the tested authentication flow.

## 3. Registration Validation
* **Fix:** Registration measurement was changed from CTA intent toward confirmed Moodle account creation.
* **Validation:** The documented invalid-registration test confirmed that the earlier click-based false positive no longer represented a successful registration in the corrected flow.

## 4. Purchase Validation
* **Fix:** Purchase measurement requires a verified success event and stable transaction identity.
* **Validation:** The documented purchase tests focus on the Data Layer payload, transaction identity, and duplicate behavior.

## 5. Payment Failure Validation
* **Fix:** `payment_failed` is treated separately from `mpesa_purchase_success`.
* **Validation:** Failure scenarios are tested to ensure a failed payment does not produce a purchase event.

## 6. Deduplication Validation
* **Fix:** Purchase events use a stable `transaction_id` and retrigger protection is intended to prevent unnecessary repeat dispatches.
* **Validation:** The documented reload test observed the historical duplicate behavior and was used to validate the transaction-identity fix. Exact post-deployment platform counts should be supported by evidence before claiming zero duplicates over a reporting period.

## 7. Cross-Subdomain Validation
* **Fix:** Custom attribution cookies are scoped to `.msingipack.com`.
* **Validation:** The documented browser test confirmed that the custom attribution cookie could be read from the Academy subdomain.
* **Boundary:** This validates custom cookie continuity, not by itself continuous GA4 session attribution.

## 8. Automated Test Results
Cypress scripts are documented as a future regression-testing capability. The repository does not currently contain sufficient evidence to claim a completed production Cypress suite is green.

## 9. Failed / Partial Tests
* **Cross-device activation:** remains limited because browser cookies do not bridge devices.
* **Long-term business impact:** requires additional post-deployment measurement.
* **Meta platform reconciliation:** requires a completed evidence-backed comparison rather than assumptions about platform attribution.

## 10. Final Validation Status

| Problem | Hypothesis | Test | Result | Validation Status |
| :--- | :--- | :--- | :--- | :--- |
| Campaign parameter loss | H1 | Redirect/persistence test | Supported and fix documented | Passed for tested flow |
| Registration intent vs account creation | H2 | Invalid registration test | False click conversion removed in corrected flow | Passed for tested flow |
| Purchase duplication | H4 | Success-page reload test | Transaction identity introduced | Fix implemented; long-term count pending |
| Custom attribution continuity | H5 | Cross-subdomain cookie test | Cookie readable on Academy | Passed for custom cookie continuity |

## 11. Remaining Issues
* Native GA4 session continuity should be evidenced separately from custom cookie persistence.
* A longer post-deployment reconciliation period is required before claiming business-level improvement.
* Server-side measurement remains a future enhancement.
