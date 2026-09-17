# Business Context: Problem Definition

**Document:** `01-business-context/problem-definition.md`  
**Project:** MsingiPack Academy CRO & Analytics Framework  
**Date:** August 2026[cite: 1]  

---

## Executive Summary

MsingiPack Academy faces severe performance tracking disconnects and conversion bottlenecks across its digital marketing funnel[cite: 1]. While paid acquisition campaigns (Meta Ads) drive high top-of-funnel reach (130,088 impressions) and ad clicks (7,486 clicks), operational infrastructure defects result in massive lead drop-offs, distorted analytics, and uncredited revenue[cite: 1].

Key underlying friction points include:
* **Moodle Security Redirects:** Mobile ad traffic intended for course previews (`preview_courses.php`) is systematically redirected to the generic homepage (`/`), stripping UTM parameters and driving an 11-second average session duration[cite: 1].
* **Superficial Tag Triggers:** Meta Ads conversion tags trigger on button click rather than verified database entry, masking a 53.9% drop-off between intent and account creation[cite: 1].
* **Post-Registration Activation Cliff:** 92.2% of registered users (3,183 out of 3,452) never log in after profile creation[cite: 1].
* **Flawed Attribution & Revenue Tracking:** GA4 underreports actual financial revenue (KES 9,900 logged vs. KES 19,333 actual M-PESA/Bank ledger) while duplicating purchase events for returning users (averaging 8.25 events per buyer)[cite: 1].

---

## Current State vs. Ideal State

| Dimension | Current State | Ideal State | Gap / Business Impact |
| :--- | :--- | :--- | :--- |
| **Landing Page Routing** | 87.2% of traffic lands on homepage (`/`) due to Moodle guest security redirects[cite: 1]. | Ad traffic lands directly on `preview_courses.php` with preserved UTM context[cite: 1]. | Confused users exit in 11 seconds; 74.5% of sessions mislabeled as 'Paid Other'[cite: 1]. |
| **Conversion Accuracy** | Meta fires registration conversions on CTA button clicks (7,486 reported)[cite: 1]. | Conversions fire strictly on confirmed Moodle database record creation (3,452 actual)[cite: 1]. | 53.9% phantom conversion reporting (4,034 unconfirmed leads billed as conversions)[cite: 1]. |
| **Lead Activation** | 92.2% idle rate (3,183 registered accounts never log in)[cite: 1]. | Automated post-signup activation sequence drives immediate portal entry[cite: 1]. | High-intent parents sit dormant in the database without completing enrollment[cite: 1]. |
| **Attribution Persistence** | Click IDs (`fbclid`) and UTM parameters stripped upon landing/redirect[cite: 1]. | Persistent 30-day cross-subdomain cookie tracking preserves attribution[cite: 1]. | Delayed sales attributed to generic 'Direct' or 'Organic' instead of Meta Ads[cite: 1]. |
| **Revenue Tracking** | GA4 logs KES 9,900.00; duplicate pixels fire 8.25x per buyer on refresh[cite: 1]. | Transaction-deduplicated pixel fires logging exact M-PESA/Bank revenue (KES 19,333.00)[cite: 1]. | Inability to evaluate true Return on Ad Spend (ROAS) or campaign profitability[cite: 1]. |

---

## Revenue Leakage Hypotheses

* **H1: Top-of-Funnel Landing Page Trap (Security Redirect)**  
  * **Mechanic:** Ad campaigns direct parents to inspect course content at `preview_courses.php`. Moodle’s default security configuration forces unauthenticated mobile visitors onto the root homepage (`/`)[cite: 1].  
  * **Impact:** 4,084 total sessions forced to homepage with an 11-second average engagement time[cite: 1]. Tracking parameters are stripped in the redirect chain, categorizing 4,179 sessions (74.5%) under a generic 'Paid Other' bucket in GA4[cite: 1].

* **H2: Mid-Funnel Sign-up Form Drop-Off & Tag Misalignment**  
  * **Mechanic:** The Meta Ads pixel fires a registration conversion when a user presses 'Register' or 'Get Started'[cite: 1].  
  * **Impact:** 4,034 prospective parents click the CTA but fail to complete multi-step profile creation, creating a 53.9% discrepancy between reported conversions (7,486) and actual database accounts (3,452)[cite: 1].

* **H3: Post-Registration Onboarding Bottleneck**  
  * **Mechanic:** Parents complete form registration but encounter friction (missing immediate credentials, lack of SMS/WhatsApp follow-up, or unclear next steps) before entering the learning environment[cite: 1].  
  * **Impact:** 3,183 out of 3,452 registered accounts (92.2%) remain idle and have never logged in, leaving active users at a critical low of 269 (7.8%)[cite: 1].

* **H4: Bottom-of-Funnel Attribution Decay & Duplicate Sales Events**  
  * **Mechanic:** Parent purchase cycles are delayed; parents inspect courses, leave, and return days later via Direct or Search routes[cite: 1]. GA4 fails to maintain `fbclid` parameters across subdomains[cite: 1]. Simultaneously, receipt page refreshes re-trigger the GA4 purchase tag[cite: 1].  
  * **Impact:** GA4 overcounts individual buyer transactions (33 purchase events across 4 buyers) while missing historical sales entirely, causing a KES 9,433.00 deficit against actual M-PESA payment receipts[cite: 1].

---

## Scope & Boundaries

**In-Scope:**
* Re-architecting Google Tag Manager (GTM) data layer triggers for Moodle LMS[cite: 1].
* Resolving Moodle mobile security redirect rules to allow direct access to `preview_courses.php`[cite: 1].
* Aligning Meta Ads and GA4 conversion triggers with verified backend database creation[cite: 1].
* Implementing persistent first-party cookie/session attribution (`fbclid`, UTM tracking across subdomains)[cite: 1].
* Setting up transaction ID deduplication on receipt pages for M-PESA and Bank payment flows[cite: 1].
* Granular form step tracking to isolate sign-up form abandonment points[cite: 1].

**Out-of-Scope:**
* Changes to Moodle core LMS PHP codebase beyond analytics hook integrations[cite: 1].
* Direct modifications to course video content or curriculum structure[cite: 1].
* Offline/manual sales activities not routed through M-PESA or Bank payment gateways[cite: 1].