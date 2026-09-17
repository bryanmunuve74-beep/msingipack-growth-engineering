# GA4 vs Moodle Validation

## 1. Objective
Compare GA4 measurement with Moodle application records to determine which differences are expected and which indicate tracking loss or implementation defects.

## 2. Systems Being Compared
* **Measurement System:** Google Analytics 4 (GA4)
* **Application Source of Truth:** Moodle LMS
* **Financial Source of Truth for purchases:** M-PESA / Bank records

## 3. Data Period
**Status:** Pending completion of an evidence-backed post-deployment reconciliation period.

Record the exact start/end dates when the source exports are available.

## 4. Metric Definitions
* **Registrations:** GA4 `sign_up` compared with confirmed Moodle account creation.
* **Logins:** GA4 `login` compared with Moodle authentication/login records, where available.
* **Purchases:** GA4 `purchase` events compared with Moodle enrollment/transaction records and then reconciled to M-PESA/Bank records.
* **Revenue:** GA4 ecommerce revenue compared with verified financial records; GA4 is not the financial source of truth.

## 5. Registration Comparison

| Metric | GA4 | Moodle | Difference | Expected? | Explanation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Registrations** | Pending | Pending | Pending | Pending | Requires matched source exports |

## 6. Login / Activation Comparison

| Metric | GA4 | Moodle | Difference | Expected? | Explanation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Logins** | Pending | Pending | Pending | Pending | Requires matched source exports |

## 7. Course Activity Comparison

| Metric | GA4 | Moodle | Difference | Expected? | Explanation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Course Views** | Pending | Pending | Pending | Pending | Metric definitions must be aligned first |
| **Completions** | Pending | Pending | Pending | Pending | Requires a defined Moodle completion source |

## 8. Purchase / Enrollment Comparison

| Metric | GA4 | Moodle | Financial Records | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Purchases** | Pending | Pending | Pending | Pending | Match using transaction identifiers |
| **Revenue** | Pending | Pending | Pending | Pending | Financial records remain authoritative |

## 9. Discrepancy Classification
Differences should be classified as:
* Missing measurement event
* Duplicate event
* Delayed event/reporting
* Attribution mismatch
* Identity mismatch
* Date/time mismatch
* Platform reporting difference
* Tracking failure

## 10. Reconciliation Method
1. Export the same reporting period from GA4 and Moodle.
2. Normalize timestamps and identifiers.
3. Match confirmed registrations and purchases.
4. For purchases, reconcile against M-PESA/Bank records.
5. Classify unmatched records.
6. Calculate variance only after the matching rules are documented.
7. Preserve sanitized evidence supporting the result.

## 11. Results
**Status: Not yet measured in this repository.**

No production reconciliation result is claimed until the underlying source exports and a defined reporting period are available.

## 12. Remaining Gaps
* Exact post-deployment reporting period has not been documented here.
* Course activity definitions require source-level alignment.
* Client-side tracking can undercount backend activity when tags are blocked.
* Cross-device identity can prevent perfect user-level matching.
