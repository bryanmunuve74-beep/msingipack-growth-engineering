# Reconciliation Methodology - MsingiPACK

## 1. Purpose
Define the framework used to validate measurement data across M-PESA/Bank records, Moodle, GA4, and Meta. The core question is: **Can the measurement numbers be trusted enough for the decision being made?**

## 2. Source-of-Truth Hierarchy
When systems disagree, the hierarchy is:

| Priority | System | Role |
| :---: | :--- | :--- |
| **1** | **M-PESA / Bank payment records** | Financial truth / settled transaction evidence |
| **2** | **Moodle** | Account, enrollment, and application-state truth |
| **3** | **GA4 / Meta** | Measurement and advertising observation layers |

GA4 and Meta should not be treated as replacements for the financial ledger.

## 3. Systems Included
* **Payment records:** M-PESA and bank records where applicable.
* **LMS:** Moodle.
* **Analytics:** GA4.
* **Advertising:** Meta Ads / Meta Pixel.

## 4. Common Identifiers
* **Transaction ID:** M-PESA receipt/reference → Moodle transaction/enrollment record → GA4 `transaction_id`.
* **User ID:** Moodle account identifier where appropriate and privacy-compliant.
* **Attribution identifiers:** UTM parameters, `fbclid`, and relevant GA4/Meta browser identifiers.

## 5. Matching Rules
For purchases, the strongest match is an exact transaction identifier. Do not require an email hash when the financial transaction identifier is sufficient.

For registrations, compare confirmed Moodle account creation records with GA4 `sign_up` events using available user/account identifiers and normalized timestamps.

## 6. Time-Window Rules
Use a documented time window appropriate to the system being compared rather than assuming a universal 72-hour delay.

For each reconciliation, record:
* Data extraction timestamps.
* GA4 property timezone.
* Moodle/server timezone.
* Payment-record timestamp convention.
* Any known reporting delay.

## 7. Transaction Matching
Recommended purchase reconciliation:

`M-PESA receipt/reference → Moodle transaction/enrollment → GA4 transaction_id`

Meta purchase signals can then be compared using their event identifier and reporting timestamp, but Meta attribution should not be treated as the financial transaction ledger.

## 8. Registration Matching
Compare:
`Moodle confirmed account creation → GA4 sign_up`

Classify differences rather than forcing the counts to match. Browser blocking, failed event delivery, duplicate attempts, and timestamp differences can create legitimate variance.

## 9. Attribution Matching
Preserved UTM/FBCLID fields can be compared with the acquisition information available in GA4 and Meta. Custom persisted fields are supporting evidence; they do not automatically override each platform's native attribution model.

## 10. Duplicate Handling
* **GA4:** Use the purchase `transaction_id` to identify duplicate ecommerce transactions.
* **Meta:** Use the configured event identifier for the documented client-side purchase signal. Pixel/CAPI deduplication should only be assessed when both signals actually exist.

## 11. Missing Record Handling
A missing analytics event for a valid backend transaction is classified as a potential tracking loss. The rate should be calculated from the actual reconciliation sample rather than using an arbitrary 5% threshold.

## 12. Discrepancy Classification

| Type | Definition | Example |
| :--- | :--- | :--- |
| **Missing** | Source-of-truth record has no corresponding measurement record | M-PESA transaction exists, no GA4 purchase |
| **Duplicate** | Same transaction represented more than once | Same `transaction_id` appears repeatedly |
| **Delayed** | Event/report appears later than the source record | Payment settles before analytics event is visible |
| **Attribution Mismatch** | Acquisition metadata differs between systems | Paid campaign appears as Direct |
| **Identity Mismatch** | Records cannot be reliably associated with the same user | Cross-device journey without durable identity |
| **Date/Time Mismatch** | Timestamp conventions differ | EAT vs UTC/property timezone |
| **Platform Reporting Difference** | Platforms apply different reporting/attribution rules | Meta-attributed purchase vs GA4 purchase |
| **Tracking Failure** | Technical event delivery failed | Data Layer event or tag did not fire |

## 13. Reconciliation Workflow
1. Export the relevant records from each source.
2. Normalize transaction IDs and timestamps.
3. Match financial records to Moodle records.
4. Match Moodle records to GA4 events.
5. Compare relevant Meta signals separately.
6. Categorize unmatched records.
7. Calculate variance and document the root cause.
8. Preserve the raw evidence and sanitized output used for the conclusion.

## 14. Example Structure
The following is a **methodology example, not MsingiPACK production data**:

`Payment records: N`
`Moodle records: N`
`GA4 unique transaction IDs: N`
`Matched: N`
`Missing: N`
`Duplicate: N`
`Unresolved: N`

The actual repository should only replace these placeholders when the underlying source exports are available.

## 15. Limitations
* Client-side tracking can be blocked.
* Cross-device journeys can break client-side attribution.
* Platform reporting uses different attribution/reporting methodologies.
* Delayed payment or application processing can temporarily create differences.
