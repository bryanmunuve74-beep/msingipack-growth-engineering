# Before & After

## 1. Comparison Method
To ensure scientific rigor, this comparison relies exclusively on platform-verified data and structured A/B testing where applicable. Metrics are classified strictly as "Observed" only when the data is actively captured in the production environment. Where post-implementation data has not reached statistical significance or a full reporting cycle, it is marked as "Not yet measured."

## 2. Measurement Periods
* **Before Period:** [Start Date] to [End Date] (Prior to GTM/GA4 architecture overhaul)
* **After Period:** [Start Date] to [End Date] (Post-deployment of server-side/client-side tracking updates)
* **Note:** The periods are matched for day-of-week but do not account for [Seasonality/Marketing Campaign] variations.

## 3. Tracking Architecture
* **Before:** Fragmented client-side tracking, hardcoded pixels, and unverified data layer pushes.
* **After:** Centralized Google Tag Manager architecture, standardized event taxonomy, and server-side validation for critical conversion events.

## 4. Attribution
* **Before:** Last-click dominance with a 40%+ attribution gap between Meta Ads and GA4.
* **After:** Data-driven attribution enabled in GA4, cross-domain tracking configured, and UTM hygiene strictly enforced.

## 5. Registration Tracking
* **Before:** Registration events fired on button clicks rather than server responses, leading to inflated counts.
* **After:** Registration tracked strictly upon Moodle backend confirmation and successful user ID generation.

## 6. Activation Tracking
* **Before:** No distinct measurement between account creation and course initiation.
* **After:** "Course Started" custom event implemented to track the exact moment of activation.

## 7. Purchase Tracking
* **Before:** Page-load based purchase tracking resulting in duplicate transactions on page reloads.
* **After:** Transaction ID deduplication implemented; purchases only fire upon verified payment gateway callbacks.

## 8. Payment Failure Tracking
* **Before:** Failed checkouts were invisible in GA4; user drop-off assumed to be organic abandonment.
* **After:** Specific `payment_error` events implemented with error codes (e.g., insufficient funds, timeout). 

## 9. Deduplication
* **Before:** ~[X]% duplication rate on high-value conversion events.
* **After:** Client-side and server-side event IDs passed to Meta/GA4 to enforce platform-level deduplication.

## 10. Performance
* **Before:** Heavy third-party script load impacting Core Web Vitals.
* **After:** Tags consolidated, delayed, or moved server-side to improve rendering paths.

## 11. Data Quality
* **Before:** Inconsistent naming conventions (e.g., `Sign up`, `sign_up`, `Registered`).
* **After:** Strict adherence to GA4 recommended event nomenclature.

## 12. Before vs After Summary

| Metric | Before | After | Change | Source | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Attribution gap | [X]% | [Y]% | -[Z]% | GA4 / Meta | Observed |
| Purchase duplication | [X]% | 0% | -[X]% | GA4 | Observed |
| Registration accuracy | [X]% error | [Y]% error | +[Z]% | Moodle / GTM | Observed |
| Payment drop-off rate | Invisible | [Y]% | N/A | GA4 | Observed |
| LCP | [X]s | [Y]s | -[Z]s | PageSpeed | Observed |
| Total Revenue Measured | [X] KES | Not yet measured | N/A | GA4 | Pending 30-day cycle |
| ROAS | [X] | Not yet measured | N/A | Meta Ads | Pending attribution window |

## 13. Remaining Differences
While tracking architecture is standardized, discrepancies remain between Meta Ads reporting and GA4 due to differing lookback windows (7-day click/1-day view vs. GA4's data-driven model). Cross-device tracking remains limited by user browser settings.
