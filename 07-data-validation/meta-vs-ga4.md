# Meta vs GA4 Validation

## 1. Objective
Document the reconciliation framework for comparing Meta Ads reporting with GA4 without assuming that either platform should exactly equal the underlying payment ledger.

## 2. Systems Compared
* **Advertising Platform:** Meta Ads Manager / Meta Pixel
* **Web Analytics:** Google Analytics 4
* **Financial Source of Truth:** M-PESA / Bank records

## 3. Comparison Period
**Status:** Pending completion of an evidence-backed reporting-period reconciliation.

Record the exact start/end dates when the relevant exports are available.

## 4. Metric Definitions
**Important context:** Meta outbound clicks ≠ GA4 sessions; Meta-attributed conversions ≠ GA4 conversions; Meta-attributed purchases ≠ raw payment transactions.

* **Traffic:** Meta-reported outbound clicks compared with GA4 sessions attributed to Meta-related acquisition, after accounting for redirects, blocked tags, and reporting definitions.
* **Registrations:** Meta conversion signals compared with GA4 `sign_up` events, where both are actually implemented and available.
* **Purchases:** Meta purchase signals compared with GA4 purchases and, separately, verified M-PESA/Bank transactions.
* **Revenue:** Meta-reported conversion value compared with GA4 revenue and financial records. Financial records remain authoritative.

## 5. Traffic Comparison

| Metric | Meta | GA4 | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Traffic** | Pending | Pending | Pending | Requires matched exports and reporting definitions |

## 6. Registration Comparison

| Metric | Meta | GA4 | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Registrations** | Pending | Pending | Pending | Requires evidence of the corresponding Meta conversion and GA4 event |

## 7. Purchase Comparison

| Metric | Meta | GA4 | Financial Records | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Purchases** | Pending | Pending | Pending | Match to verified transactions where possible |

## 8. Revenue Comparison

| Metric | Meta Conversion Value | GA4 Revenue | Financial Revenue | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Revenue** | Pending | Pending | Pending | Financial records are the reference point |

## 9. Attribution Differences
Record the actual configuration used during the comparison period rather than assuming a default:
* **Meta attribution setting:** Pending evidence.
* **GA4 attribution/reporting configuration:** Pending evidence.
* **Impact:** Explain the documented differences after the settings are verified.

## 10. Event Deduplication Effects
The current repository documents a client-side Meta Pixel purchase signal. Meta CAPI should **not** be included in a reconciliation as an implemented source unless a CAPI implementation and corresponding evidence exist.

Where multiple Meta signals exist in the future, compare their event identifiers and deduplication behavior explicitly.

## 11. Discrepancies
Classify meaningful differences as:
* Tracking loss
* Duplicate event
* Attribution/reporting-model difference
* Redirect/session difference
* Browser/privacy limitation
* Timing difference
* Financial/application-state difference

## 12. Results
**Status: Not yet measured in this repository.**

No final Meta-vs-GA4 performance conclusion is claimed until the source exports, reporting period, and attribution settings are documented.

## 13. Remaining Measurement Gaps
* Meta and GA4 use different measurement/reporting methodologies.
* Client-side tracking can miss events when browser controls block tags.
* Cross-device journeys can produce different attribution outcomes.
* Financial truth must be reconciled separately from platform-attributed conversion totals.
