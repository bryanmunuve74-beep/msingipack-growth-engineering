# Enhanced Funnel Lifecycle & Measurement Overview

This document provides a comprehensive audit of the end-to-end conversion funnel, highlighting real user volumes, stage-by-stage drop-off rates, technical root causes in user session behavior, and critical data health and attribution vulnerabilities.

---

## 1. Executive Summary & Funnel Lifecycle Matrix

The matrix below details the performance metrics and measurement health across all five major stages of the user acquisition and conversion lifecycle.

| Funnel Stage | Real User Volume | Conversion & Drop-off Rates | Session Behavior & Technical Root Cause | Analytics Status & Data Health |
| :--- | :--- | :--- | :--- | :--- |
| **1. Acquisition (Ad Clicks)** | 7,486 clicks[cite: 1] | Baseline | Healthy initial ad engagement (~14% CTR on reach)[cite: 2]. | **Distorted:** UTM parameters are stripped immediately downstream[cite: 2]. |
| **2. Course Evaluation (Preview)** | ~3,800 viewers | 46.2% redirect loss[cite: 2] | 11-second average duration (vs. 4m+ for organic). Moodle guest security forces 4,084 mobile ad sessions from `preview_courses.php` to homepage `/`[cite: 1, 2]. | **Broken:** Stripped `utm_*` and `fbclid` cause GA4 to misclassify 4,179 sessions (74.5%) as 'Paid Other' or 'Direct'[cite: 1, 2]. |
| **3. Lead Capture (Sign-Up)** | 3,452 registered[cite: 1] | 53.9% form drop-off[cite: 1, 2] | Multi-step mobile registration fatigue following a broken landing experience[cite: 2]. | **Overstated:** Meta pixel fires on "Register" button click, counting clicks instead of confirmed backend database writes[cite: 1, 2]. |
| **4. Portal Activation (First Login)** | 269 active users[cite: 1, 2] | 92.2% never log in[cite: 1, 2] | 3,183 accounts sit idle. No automated SMS, WhatsApp, or email onboarding nudges exist post-registration[cite: 1, 2]. | **Blindspot:** Tracking ceases after registration; active vs. idle users are unmonitored in ad channels[cite: 2]. |
| **5. Conversion (Payment)** | 25 paid buyers[cite: 2] | 9.3% conversion (of active users)[cite: 2, 4] | Successful M-PESA/Bank checkout for active users (KES 19,333.00 total)[cite: 1, 2]. | **Duplicated:** Receipt page re-renders log 33 GA4 purchase events for only 4 buyers (KES 9,900), missing 21 real payers entirely[cite: 1, 2]. |

---

## 2. Stage 2 Deep-Dive: Engagement & Attribution Leakage

### The 11-Second Engagement Crash
Analysis reveals a severe anomaly between paid traffic and organic/direct sessions:
* **Paid Ad Traffic:** Averages just 8–11 seconds in duration and 3.76–4.80 actions per session[cite: 2].
* **Direct / Organic Traffic:** Averages 4 minutes 1 second in duration and executes 56.44 actions per session[cite: 2].

### The Redirect Root Cause
The primary catalyst for this drop-off is an infrastructure and security misconfiguration within Moodle[cite: 2]:
1. Meta ad campaigns target `preview_courses.php` to show prospective users course previews[cite: 1, 2].
2. Moodle's guest security configuration blocks unauthenticated mobile sessions attempting to view this page[cite: 1, 2].
3. The system issues an automated HTTP 302 redirect, forcing mobile users away from the landing page onto the root homepage (`/`)[cite: 1, 2].
4. During the redirect chain, critical URL parameter data—including `utm_source`, `utm_medium`, and `fbclid`—is stripped completely[cite: 1, 2].

### Attribution Blindspot & Data Degradation
This architectural failure creates significant downstream attribution corruption[cite: 2]:
* 4,084 sessions are forcibly landed on the generic homepage instead of the intended course preview page[cite: 1, 2].
* As a result, Google Analytics 4 (GA4) misclassifies 4,179 sessions (74.5%) as generic 'Paid Other' or 'Direct'[cite: 1, 2].
* This completely masks true advertising Return on Ad Spend (ROAS) while over-crediting 'Direct' traffic for returning user visits[cite: 2].

---

## 3. Stage 3–5 Technical & Analytics Vulnerabilities

### Stage 3: Lead Capture Overstatement
The Meta Pixel is currently configured to trigger on the "Register" button click event rather than on verified backend database registration writes[cite: 2]. This over-reports registration conversions and misleads ad optimization algorithms[cite: 2].

### Stage 4: Post-Registration Activation Failure
A critical drop of 92.2% occurs immediately after registration, leaving 3,183 accounts idle[cite: 1, 2]. The absence of automated onboarding communication (SMS, WhatsApp, or email) creates an operational blackout, exacerbated by zero user-level tracking beyond the registration step[cite: 2].

### Stage 5: Conversion & Revenue Discrepancy
While 25 real users successfully completed M-PESA/Bank payments totaling KES 19,333.00, receipt page re-renders resulted in 33 duplicate GA4 purchase events logged for only 4 users (KES 9,900), leaving 21 actual paying users unrecorded in telemetry[cite: 1, 2].