# Success Metrics & KPI Framework

**Document:** `01-business-context/success-metrics.md`  
**Project:** MsingiPACK Academy CRO & Analytics Framework  
**Date:** August 2026[cite: 4]  

---

## 1. Purpose

This document defines the KPI framework for measuring the performance of the MsingiPACK Academy acquisition funnel, registration process, user activation, purchasing behavior, and marketing attribution[cite: 4]. The framework separates business outcomes, funnel performance, product activation, and measurement integrity so that improvements can be evaluated using reliable and clearly defined metrics[cite: 4].

A key principle is that a KPI should have:
* A clearly defined numerator and denominator[cite: 4]
* A consistent data source[cite: 4]
* A documented baseline[cite: 4]
* A realistic target[cite: 4]
* A measurement method that can be reproduced[cite: 4]

---

## 2. Primary Business KPIs

These metrics measure whether improvements to the acquisition and conversion funnel ultimately produce better commercial outcomes[cite: 4].

| KPI | Current Baseline | Target | Definition |
| :--- | :--- | :--- | :--- |
| **Session-to-Purchase Rate** | 0.45%[cite: 4] | 1.50%[cite: 4] | Valid purchases ÷ GA4 sessions[cite: 4] |
| **Average Order Value (AOV)** | KES 773.32[cite: 4] | KES 1,000[cite: 4] | Revenue ÷ valid purchases[cite: 4] |
| **Customer Acquisition Cost (CAC)** | KES 489.52[cite: 4] | < KES 286 *(adjusted)*[cite: 4] | Ad spend ÷ valid purchases[cite: 4] |
| **Return on Ad Spend (ROAS)** | 1.58x[cite: 4] | 3.50x[cite: 4] | Verified revenue ÷ ad spend[cite: 4] |

### Baseline Calculations
* **Session-to-Purchase Rate:** 25 valid payments ÷ 5,611 GA4 sessions = 0.45%[cite: 4]
* **AOV:** KES 19,333 revenue ÷ 25 payments = KES 773.32[cite: 4]
* **CAC:** KES 12,238 ad spend ÷ 25 payments = KES 489.52[cite: 4]
* **ROAS:** KES 19,333 revenue ÷ KES 12,238 ad spend = 1.58x[cite: 4]

### Measurement Consideration
The 0.45% figure should be described as a **Session-to-Purchase Rate**, rather than a generic "funnel conversion rate." Sessions are not equivalent to unique customers, so the denominator should remain explicit[cite: 4]. Where reliable user-level data becomes available, a separate User-to-Purchase Conversion Rate should also be reported[cite: 4].

---

## 3. Micro-Conversions & Activation Metrics

Business outcomes alone do not explain where users are being lost. These metrics identify the major points of friction between acquisition and purchase[cite: 4].

| KPI | Current Baseline | Target | Definition |
| :--- | :--- | :--- | :--- |
| **Ad Intent Clicks** | 7,486[cite: 4] | Improve quality[cite: 4] | Meta-reported tracked intent/CTA clicks[cite: 4] |
| **Ad Click-to-Account Rate** | 46.11%[cite: 4] | 70.00%[cite: 4] | Verified registrations ÷ ad CTA clicks[cite: 4] |
| **Post-Signup Activation Rate** | 7.79%[cite: 4] | 40.00%[cite: 4] | Activated users ÷ registrations[cite: 4] |
| **Activated-User-to-Purchase Rate** | 9.29%[cite: 4] | 15.00%[cite: 4] | Purchases ÷ activated users[cite: 4] |

### 3.1 Ad Intent Clicks
Meta recorded 7,486 intent/CTA clicks[cite: 4]. The previous framework described this as a 100% "Ad Click-to-Form Initiation Rate," which is technically incorrect because the same event is used as both the tracked action and the denominator[cite: 4]. Therefore, it is treated strictly as **Tracked Ad Intent Clicks = 7,486**[cite: 4]. A true click-to-form initiation rate should only be introduced when an independent form initiation event becomes available[cite: 4].

### 3.2 Ad Click-to-Account Rate
* **Baseline:** 46.11% (3,452 verified database accounts ÷ 7,486 ad CTA clicks)[cite: 4]  
* **Target:** 70.00%[cite: 4]  

The objective is to reduce registration friction and ensure users arriving from paid campaigns can successfully complete account creation[cite: 4]. Potential intervention areas include:
* Registration form usability[cite: 4]
* Mobile UX[cite: 4]
* Form validation[cite: 4]
* Required-field friction[cite: 4]
* Authentication errors[cite: 4]
* Cross-domain/session continuity[cite: 4]
* Event implementation[cite: 4]
* Failed registration tracking[cite: 4]

---

## 4. Post-Signup Activation

* **Current Baseline:** 7.79% (269 active/logged-in users ÷ 3,452 registered accounts)[cite: 4]  
* **Target:** 40.00%[cite: 4]  

The proposed target assumes that onboarding improvements can substantially increase the number of registered users who return to and engage with the Academy[cite: 4]. Potential interventions include:
* Automated SMS onboarding[cite: 4]
* WhatsApp reminders[cite: 4]
* Welcome messaging[cite: 4]
* Clear next-step instructions[cite: 4]
* Direct links to the learner dashboard[cite: 4]
* Course-start prompts[cite: 4]
* Recovery campaigns for inactive registrants[cite: 4]

### Important Measurement Refinement
"At least one login" is a relatively weak definition of activation[cite: 4]. For future reporting, activation should preferably represent a meaningful product action:
> **Activated User:** Registered user who logs in and reaches the first meaningful learning activity or course page[cite: 4].

---

## 5. Activated-User-to-Purchase Rate

* **Current Baseline:** 9.29% (25 valid payments ÷ 269 activated users)[cite: 4]  
* **Target:** 15.00%[cite: 4]  

This metric measures the ability of the Academy to convert users who have demonstrated meaningful engagement into paying customers[cite: 4]. It is treated separately from the overall session-to-purchase rate because it represents a much later stage of the funnel[cite: 4].

---

## 6. Measurement Integrity KPIs

A Growth Engineering framework must establish whether the underlying measurement system can be trusted[cite: 4].

### 6.1 Attribution Classification Gap
* **Current Observation:** 74.50% of traffic identified as mislabeled/problematic[cite: 4]  
* **Target:** < 5.00% unassigned or incorrectly classified traffic[cite: 4]  

This metric distinguishes between unassigned traffic, incorrectly classified traffic, paid traffic appearing as 'Paid Other', campaign traffic losing UTM parameters, and traffic incorrectly attributed to 'Direct'[cite: 4]. 
* **Measurement Method:** Compare expected campaign source/medium information against GA4 channel classification using GA4, GTM, campaign parameters, and cross-domain attribution data[cite: 4].

---

## 7. Purchase Event Integrity

### 7.1 Purchase Event Multiplication
* **Observed Baseline:** 8.25 purchase events per buyer (33 purchase events ÷ 4 unique buyers)[cite: 4]  
* **Target:** 1.00 purchase event per transaction[cite: 4]  

This represents the average number of purchase events generated per buyer rather than an 8.25x duplicate rate[cite: 4]. A healthy implementation ensures:
$$\text{1 unique purchase event} = \text{1 valid transaction}$$

Duplicate events distort GA4 revenue, conversion reporting, Meta optimization, ROAS, campaign performance, automated bidding, and executive reporting[cite: 4].

---

## 8. KPI Measurement Framework

| Metric | Current Baseline | Target | Measurement Method | Primary Data Source |
| :--- | :--- | :--- | :--- | :--- |
| **Session-to-Purchase Rate** | 0.45%[cite: 4] | 1.50%[cite: 4] | Valid purchases ÷ GA4 sessions[cite: 4] | GA4 + M-PESA Ledger[cite: 4] |
| **Ad Intent Clicks** | 7,486[cite: 4] | Improve quality[cite: 4] | Tracked Meta intent clicks[cite: 4] | Meta Ads[cite: 4] |
| **Ad Click-to-Account Rate** | 46.11%[cite: 4] | 70.00%[cite: 4] | Verified registrations ÷ ad CTA clicks[cite: 4] | Moodle DB + GTM[cite: 4] |
| **Post-Signup Activation** | 7.79%[cite: 4] | 40.00%[cite: 4] | Activated users ÷ registrations[cite: 4] | Moodle DB + User Logs[cite: 4] |
| **Activated-User-to-Purchase Rate** | 9.29%[cite: 4] | 15.00%[cite: 4] | Valid purchases ÷ activated users[cite: 4] | Moodle + M-PESA[cite: 4] |
| **Attribution Classification Gap** | 74.50% problematic[cite: 4] | < 5.00%[cite: 4] | Incorrect/unassigned sessions ÷ total sessions[cite: 4] | GA4[cite: 4] |
| **Purchase Event Multiplication** | 8.25x[cite: 4] | 1.00x[cite: 4] | Purchase events ÷ unique transactions[cite: 4] | GA4 + GTM[cite: 4] |
| **AOV** | KES 773.32[cite: 4] | KES 1,000[cite: 4] | Revenue ÷ valid purchases[cite: 4] | M-PESA Ledger[cite: 4] |
| **CAC** | KES 489.52[cite: 4] | < KES 286[cite: 4] | Ad spend ÷ valid purchases[cite: 4] | Meta Ads + Ledger[cite: 4] |
| **ROAS** | 1.58x[cite: 4] | 3.50x[cite: 4] | Verified revenue ÷ ad spend[cite: 4] | M-PESA Ledger + Meta Ads[cite: 4] |

---

## 9. Target Consistency Check

The CAC and ROAS targets must be interpreted together rather than independently[cite: 4]. At a target AOV of KES 1,000[cite: 4]:
* If $\text{CAC} = \text{KES } 350$, then $\text{KES } 1,000 \div \text{KES } 350 \approx \text{2.86x ROAS}$[cite: 4].
* To achieve **3.50x ROAS** at KES 1,000 AOV, the implied CAC must be approximately **KES 286 or lower** ($\text{KES } 1,000 \div 3.50 \approx \text{KES } 285.71$)[cite: 4].

---

## 10. AOV Target Consideration

* **Current AOV:** KES 773.32[cite: 4]  
* **Target AOV:** KES 1,000 (~29.3% increase)[cite: 4]  

Achieving a KES 1,000 AOV when individual core course prices are lower requires commercial mechanisms such as bundles, multiple-course purchases, upsells, premium products, subscription plans, or additional learning products[cite: 4]. AOV should not be treated purely as a CRO metric unless product architecture supports it[cite: 4].

---

## 11. KPI Hierarchy

* **Layer 1 — Business Outcomes:** Revenue $\rightarrow$ Purchases $\rightarrow$ AOV $\rightarrow$ CAC $\rightarrow$ ROAS[cite: 4]  
* **Layer 2 — Funnel Conversion:** Ad Click $\rightarrow$ Registration $\rightarrow$ Activation $\rightarrow$ Purchase[cite: 4]  
* **Layer 3 — Product Activation:** Registration $\rightarrow$ Login $\rightarrow$ Learning Activity $\rightarrow$ Purchase[cite: 4]  
* **Layer 4 — Measurement Integrity:** Attribution $\rightarrow$ Event Accuracy $\rightarrow$ Transaction Deduplication $\rightarrow$ Cross-Domain Continuity[cite: 4]  

---

## 12. Recommended Reporting Cadence

* **Weekly Monitor:** Sessions, ad clicks, registrations, activation, purchases, conversion rates, purchase-event duplication, attribution anomalies[cite: 4].
* **Monthly Evaluate:** Revenue, AOV, CAC, ROAS, channel performance, funnel leakage, activation performance, measurement quality[cite: 4].
* **After Major Tracking Changes:** Perform a measurement validation before comparing new results with historical data across GTM triggers, GA4 events, Meta Pixel events, cross-domain tracking, purchase tracking, transaction IDs, and attribution parameters[cite: 4].

---

## 13. Success Definition

Success requires improvement across three distinct dimensions[cite: 4]:
1. **Commercial Performance:** Higher purchase conversion, higher revenue, lower CAC, higher ROAS, and sustainable AOV improvement[cite: 4].
2. **User Journey Performance:** Higher registration completion, higher activation, lower funnel abandonment, and higher activated-user purchase conversion[cite: 4].
3. **Measurement Reliability:** Correct campaign attribution, reliable cross-domain tracking, one purchase event per transaction, and full ledger reconciliation between GA4, Moodle, Meta, and M-PESA[cite: 4].