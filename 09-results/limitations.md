# Project Limitations

## 1. Historical Data Quality
The historical baseline contained measurement-quality issues, including duplicate purchase events, missing campaign parameters, and UI interactions that could be mistaken for conversions. Historical figures therefore need to be interpreted as a baseline with known data-quality limitations rather than a perfect control dataset.

## 2. Post-Deployment Measurement Window
A complete, evidence-backed post-deployment business reporting period is not yet included in the repository. Conversion-rate, CAC, AOV, ROAS, retention, and revenue comparisons should therefore remain pending until the same definitions are applied over a defined period after deployment.

## 3. Attribution Limitations
The implementation preserves campaign metadata across the documented MsingiPACK subdomains, but it does not remove the broader limitations of attribution. Cross-device journeys, anonymous-to-known identity changes, ad-platform modeling, and browser privacy controls can still create attribution gaps.

## 4. Client-Side Tracking Limitations
The current measurement architecture is primarily client-side. Browser restrictions, ad blockers, privacy extensions, consent settings, JavaScript failures, and network failures can prevent some analytics or advertising requests from being sent. Backend transaction records therefore remain the authoritative source for financial reconciliation.

## 5. Platform Reporting Differences
GA4, Meta, Moodle, and payment records use different identities, processing rules, attribution logic, and reporting windows. Their counts are not expected to match one-to-one. Reconciliation should use explicit matching rules rather than direct numerical equality.

## 6. Testing Limitations
The implementation was applied to repair measurement defects rather than as a controlled experiment. The repository therefore documents implementation validation and measurement improvements, not a randomized causal estimate of commercial impact.

## 7. Sample-Size / Outcome Limitations
Commercial outcomes such as retention, course completion, lifetime value, and sustained conversion improvement require sufficient post-deployment volume and a defined observation period. Those outcomes are not claimed in this phase without supporting evidence.

## 8. Revenue Reconciliation Limitations
Payment activity may be reflected at different times across Moodle, M-PESA, bank records, and analytics platforms. Pending transactions, asynchronous callbacks, manual reconciliation, or reporting delays can create temporary differences. Final financial reporting should be reconciled to M-PESA/Bank records rather than analytics events alone.

## 9. Causal Inference Limitations
Any future change in conversion rate, revenue, CAC, or ROAS should be interpreted alongside other changes that occurred during the same period, such as traffic mix, campaign spend, pricing, seasonality, product changes, and ad-platform learning. The tracking implementation alone should not be treated as proof of causal commercial uplift.

## 10. Unimplemented Server-Side Enhancements
GTM Server-Side, GA4 Measurement Protocol, and Meta Conversions API are documented as future options rather than completed components. They may reduce some browser-side measurement loss, but they would still require careful identity, event deduplication, consent, security, and backend integration design.

## 11. Privacy and Compliance
The public repository must exclude credentials, payment details, personally identifiable information, and other sensitive production data. Any user identifiers or enhanced-conversion data used in production should follow the applicable privacy, consent, retention, and security requirements.

## 12. Ongoing Maintenance
The architecture depends on third-party platforms, browser behavior, Moodle implementation details, and tracking APIs. Changes to any of these layers can affect event collection, attribution, or reconciliation. The measurement system should therefore be periodically re-tested after material platform or application changes.

## 13. Future Measurement Improvements
The next measurement phase should focus on completing a defined GA4 ↔ Moodle ↔ M-PESA/Bank reconciliation period, monitoring duplicate purchases by transaction ID, repeating the performance audit, and evaluating server-side measurement only where the remaining browser-side loss justifies the added complexity.
