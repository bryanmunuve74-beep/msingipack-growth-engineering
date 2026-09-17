# Meta vs GA4 Validation

## 1. Objective
To reconcile advertising measurement differences between Meta Ads Manager and Google Analytics 4, ensuring an accurate understanding of return on ad spend (ROAS) and user acquisition.

## 2. Systems Compared
* **Ad Platform:** Meta Ads Manager (Facebook/Instagram)
* **Web Analytics:** Google Analytics 4 (GA4)

## 3. Comparison Period
* **Start Date:** [YYYY-MM-DD]
* **End Date:** [YYYY-MM-DD]

## 4. Metric Definitions
**Important Context:** Meta Clicks $\neq$ GA4 Sessions | Meta Conversions $\neq$ GA4 Conversions | Meta-attributed Purchases $\neq$ Raw Payment Transactions

* **Meta Clicks vs GA4 Sessions:** Outbound clicks vs landing sessions after redirect, consent, ad-blockers.
* **Meta Conversions vs GA4 Conversions:** Different attribution windows and modeling.
* **Meta-attributed Purchases vs Raw Transactions:** View-through and 7-day click vs backend truth.

## 5. Traffic Comparison

| Metric | Meta (Outbound Clicks) | GA4 (Sessions from Meta) | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Traffic** | [X] | [Y] | [Z] | [e.g., Click drop-off, cookie consent denial] |

## 6. Registration / Lead Comparison

| Metric | Meta Leads | GA4 Sign-ups (Meta) | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Registrations** | [X] | [Y] | [Z] | [e.g., View-through vs Click-through] |

## 7. Purchase Comparison

| Metric | Meta Purchases | GA4 Purchases (Meta) | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Purchases** | [X] | [Y] | [Z] | [e.g., Cross-device tracking differences] |

## 8. Revenue Comparison

| Metric | Meta Conversion Value | GA4 Revenue (Meta) | Difference | Explanation |
| :--- | :--- | :--- | :--- | :--- |
| **Revenue** | [$X] | [$Y] | [$Z] | [e.g., GA4 data-driven vs Meta 7-day click] |

## 9. Attribution Differences
* **Meta Attribution Setting:** [e.g., 7-day click, 1-day view]
* **GA4 Attribution Model:** [e.g., Data-driven, Cross-channel last click]
* **Impact:** [How these settings cause the numbers to diverge]

## 10. Event Deduplication Effects
* [Explanation of how the Meta Conversions API (CAPI) and Pixel are deduplicating events, and if any overcounting is occurring.]

## 11. Discrepancies
* [Substantiated reasons for every meaningful discrepancy.]

## 12. Root Causes / Explanations
* [Deep dive into technical or methodological reasons for the gaps.]

## 13. Results
* [Summary of the true performance based on the reconciliation.]

## 14. Remaining Measurement Gaps
* [Areas where data loss is accepted or unavoidable, like iOS 14.5+ opt-outs.]

---
**Template Notes:** Fill dates, outbound clicks vs GA4 sessions, Meta vs GA4 leads/purchases/revenue, and attribution settings | **Key:** Meta 7d click 1d view vs GA4 Data-Driven
