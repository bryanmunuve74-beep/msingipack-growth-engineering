# Reconciliation Methodology - MsingiPACK

## 1. Purpose
To define the standardized framework, rules, and hierarchy used to validate data across all marketing, analytics, and backend systems. This ensures a consistent approach to answering: "Can we trust the numbers?"

## 2. Source-of-Truth Hierarchy
When data conflicts occur, truth is determined in this order:

| Priority | System | Role |
| :---: | :--- | :--- |
| **1** | **Payment Ledger** (Stripe/PayPal/Bank) | Ultimate financial truth / revenue reality |
| **2** | **Moodle Transaction & Account State** (LMS DB) | User & enrollment truth |
| **3** | **Measurement Platforms** (GA4/Meta/CAPI) | Observational layer - always secondary |

## 3. Systems Included
* **Payment Gateway:** Stripe
* **LMS:** Moodle
* **Analytics:** GA4
* **Advertising:** Meta Ads

## 4. Common Identifiers
* **Transaction IDs:** Payment $\rightarrow$ Moodle $\rightarrow$ GA4 (for deduplication)
* **User IDs / Hashed Emails:** Moodle $\rightarrow$ GA4 $\rightarrow$ Meta CAPI (for identity)
* **Session IDs / Client IDs:** GA4 `_ga` cookie, `_gl` linker, `fbclid`, `fbp`/`fbc`

## 5. Matching Rules
* [Rules for determining if Record A in Moodle matches Record B in GA4]
* **Recommended:** `transaction_id` exact match + `email` hash match $+72\text{h}$ time window

## 6. Time-Window Rules
* [Allowable time variance between systems. E.g., GA4 events may be logged up to 72 hours late due to offline batching.]
* **Stripe webhooks:** $T+0$ to $T+48\text{h}$
* **GA4:** $T+0$ to $T+72\text{h}$
* **Meta CAPI:** $T+0$ to $T+7\text{d}$ for attribution

## 7. Transaction Matching
* [Process for joining Stripe payouts with Moodle enrollments and GA4 purchase events.]
* **JOIN:** Stripe `charge_id` $\rightarrow$ Moodle `enrollment_id` $\rightarrow$ GA4 `transaction_id`

## 8. Registration Matching
* [Process for comparing Moodle user creation logs with GA4 `sign_up` events.]
* **MATCH:** Moodle `user_created_at` vs GA4 `sign_up` timestamp within 24h window, normalized to EAT

## 9. Attribution Matching
* [Rules for mapping UTM parameters and Meta `fbclid` to backend user records where possible.]
* Preserve UTMs via session cookie before Moodle `require_login()` 302 redirect, and persist to user record.

## 10. Duplicate Handling
* [How we identify and strip duplicate event fires (e.g., using `event_id` in CAPI).]
* **GA4:** `transaction_id` deduplication
* **Meta:** `event_id` + `event_time` deduplication between Pixel and CAPI

## 11. Missing Record Handling
* [Protocols for when a source-of-truth record (Moodle) has no corresponding measurement record (GA4).]
* **Classify as Tracking Failure:** if pattern $> 5\%$
* **Classify as Expected (ad-blocker):** if $< 5\%$ and random

## 12. Discrepancy Classification

| Type | Definition | Example |
| :--- | :--- | :--- |
| **Missing** | Event in truth not in measurement | Moodle enrollment exists, no GA4 purchase |
| **Duplicate** | Event recorded multiple times | Thank You page refresh without `transaction_id` |
| **Delayed** | Event outside expected window | GA4 purchase logged 3 days after Stripe |
| **Attribution Mismatch** | Wrong source/medium | UTM stripped by `require_login()` 302 $\rightarrow$ Direct |
| **Identity Mismatch** | Wrong user/session | Cross-device login breaks Client ID |
| **Date/Time Mismatch** | Timestamp variance | EAT vs UTC vs GA4 timezone |
| **Platform Reporting Difference** | Methodology gap | Meta view-through vs GA4 last-click |
| **Tracking Failure** | Structural break | Data layer push fails, pixel not firing |

## 13. Reconciliation Workflow
1. Export data from Source A and Source B.
2. Normalize timestamps and identifiers (UTC $\rightarrow$ EAT, lowercase emails, trim IDs).
3. Run match sequence (`VLOOKUP` / `SQL JOIN` on `transaction_id` + `hashed_email`).
4. Categorize unmatched rows based on Discrepancy Classification.
5. Document findings with variance % and root cause.

## 14. Example Reconciliation
* **Example:** Jan 2026 - GA4 reported 312 purchases, Moodle 298 enrollments, Stripe 295 charges. 14 duplicates removed via `transaction_id`, 3 missing due to ad-blockers (1% expected loss). Final variance: 1%.

## 15. Limitations
* Client-side tracking is vulnerable to Brave, uBlock.
* iOS ATT opt-outs.
* Safari ITP 7-day cookie cap.
* Consent Mode denials.

---
**Metadata:** Generated: 2026-05-13 | **Hierarchy:** Payment Ledger > Moodle DB > GA4/Meta | **Core:** `transaction_id` + hashed email + 72h window | **Goal Answer:** Can we trust the numbers?
