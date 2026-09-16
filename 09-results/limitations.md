# Project Limitations

## 1. Data Limitations
Because the "Before" data contained fundamental flaws (duplicate events, missing parameters, and inflated clicks), a perfect 1:1 historical comparison is impossible. The "Before" baseline is mathematically skewed, meaning relative percentage improvements must be contextualized against data quality fixes.

## 2. Attribution Limitations
The implementation does not solve the inherent limitations of multi-touch attribution. Cross-device journeys (e.g., clicking an ad on a mobile device but completing the Moodle registration on a desktop 3 days later) are still likely to break without forced user authentication across all touchpoints.

## 3. Technical Limitations
Client-side tracking remains vulnerable to client-side environments. Users with strict ad-blockers, Brave browser, or aggressive privacy extensions will not be tracked in GA4 or Meta, meaning our observed data will always represent a slight undercount of true backend volume.

## 4. Platform Limitations
Both Meta and GA4 increasingly rely on modeled data to fill in gaps caused by iOS 14.5+ App Tracking Transparency (ATT) and Intelligent Tracking Prevention (ITP). The data visible in platform dashboards includes algorithmic estimations of conversions, which cannot be perfectly reconciled with exact backend accounting systems.

## 5. Testing Limitations
The deployment of the new tracking architecture was pushed live globally rather than as a split test (due to the necessity of fixing broken revenue data immediately). Therefore, some pre/post metric variations may be influenced by external variables rather than the technical implementation itself.

## 6. Sample-Size Limitations
At the time of writing, the "After" measurement period only spans [X] weeks. Long-term metrics—particularly those related to user retention, course completion, and Life Time Value (LTV)—have not reached sufficient volume to establish statistical significance. 

## 7. Revenue Measurement Limitations
Payment reconciliation between Moodle, the payment gateway, and GA4 can experience latency. Delayed webhooks or pending bank transfers mean that daily revenue figures in analytics platforms may lag backend financial reporting by 24–48 hours.

## 8. Causal Inference Limitations
While we observed a drop in CPAs and an improvement in ROAS post-implementation, these outcomes may be influenced by concurrent factors outside this project's scope, including seasonality, algorithmic learning phases on ad platforms, and changes in competitor ad spend.

## 9. Unimplemented Recommendations
Server-side tracking (via GTM Server-Side) was mapped as an opportunity but was deferred to Phase 2 due to infrastructure costs. Consequently, we are still wholly reliant on the client's browser to dispatch data to advertising endpoints.

## 10. Outstanding Risks
Changes to third-party cookie policies (e.g., Google Chrome's evolving Privacy Sandbox) and regional data regulations (e.g., Kenya Data Protection Act) represent ongoing risks to the current tracking architecture. The setup will require routine maintenance to remain compliant and functional.

## 11. Future Measurement Improvements
To close the remaining gap between front-end tracking and backend reality, the next logical step is to implement the Measurement Protocol for GA4 and the Conversions API (CAPI) for Meta. This will allow the Moodle server to send definitive conversion and refund data directly to analytics, bypassing the browser entirely.
