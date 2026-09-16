# Performance Findings

## 1. Executive Summary
A technical performance audit of `academy.msingipack.com` revealed a Mobile Performance Score of 57/100, driven by a 41.8 MB page payload and a 32.3-second Largest Contentful Paint (LCP)[cite: 2]. User experience friction is further compounded by a 2,835px scroll distance required for parents to reach the primary KES 300 enrolment call-to-action[cite: 1]. Resolving these technical defects is essential to protect marketing ROI and ensure paid campaign readiness[cite: 1, 2].

## 2. Key Performance Problems
* **Massive Payload Bloat:** 41.8 MB transferred on initial load due to raw, uncompressed `/pix/` images[cite: 2].
* **Delayed Hero Render (LCP 32.3s):** `banner-img.jpeg` loaded at 1536 × 1024 px for a 388 × 259 px display area without priority tags[cite: 2].
* **Excessive CTA Scroll Depth:** Mobile users must scroll 2,835 pixels past LMS boilerplate to convert[cite: 1].
* **Render-Blocking Latency:** 5,360 ms delay introduced by legacy scripts and CSS bundles[cite: 2].
* **Accessibility Deficiencies:** Low contrast on CTA buttons/headings and invalid HTML structures[cite: 2].

## 3. Root Causes
* Absence of automated image optimization and responsive delivery within Moodle[cite: 2].
* Default Moodle interface exposing complex learner navigation to unauthenticated guest visitors[cite: 1].
* Un-deferred delivery of legacy YUI frameworks and redundant polyfills (`polyfill.js`)[cite: 2].

## 4. User Experience Impact
Parents clicking paid ad campaigns experience a 4.5s blank screen (FCP) followed by a 32.3s delay before hero visuals load[cite: 2]. Upon loading, they are forced to navigate clutter (LMS headers, side blocks, footers) and scroll through 2,835 pixels of content, resulting in immediate bounce and abandonment[cite: 1].

## 5. Funnel Impact
* **Ad-to-Landing Drop-off:** High bounce rate between paid ad click and meaningful page view[cite: 1].
* **Conversion Obstruction:** Hidden CTA button reduces immediate KES 300 enrolment actions[cite: 1].
* **Analytics Blindspots:** GA4 purchase tracking cannot validate conversions without configured M-PESA success URL triggers[cite: 1].

## 6. Business Relevance
Marketing spend directed to `academy.msingipack.com` is undermined by front-end friction[cite: 1]. Optimizing mobile loading speed and deploying a sticky KES 300 enrolment CTA directly improves conversion rates, lowers Cost Per Acquisition (CPA), and ensures accurate GA4/Meta Ads attribution[cite: 1, 2].

## 7. Priority Ranking

| Finding | Evidence | Funnel Stage | User Impact | Business Relevance | Priority | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Oversized Image Payload** | 41.8 MB transfer weight (`child_mobile.jpg` 15.3 MB)[cite: 2] | Landing Page | Extreme load delays on mobile data[cite: 2] | Wastes ad spend via early bounces[cite: 1] | Critical | Action Required |
| **32.3s LCP Hero Delay** | `banner-img.jpeg` unoptimized at 1536x1024[cite: 2] | Top-of-Funnel | Hero content invisible for 32 seconds[cite: 2] | Destroys first impression & trust[cite: 1] | Critical | Action Required |
| **2,835px CTA Scroll Distance** | Mobile enrolment button buried below LMS blocks[cite: 1] | Consideration / Enrolment | Friction in finding enrolment CTA[cite: 1] | Lowers offer conversion rate[cite: 1] | High | Ready for Deployment[cite: 1] |
| **Render-Blocking CSS/JS** | 5,360 ms critical path latency[cite: 2] | Initial Load | 4.5s blank screen delay (FCP)[cite: 2] | Increases drop-off rate[cite: 1] | High | Action Required |
| **Unvalidated GA4 Purchases** | Missing M-PESA success redirect trigger[cite: 1] | Post-Payment / Enrolment | N/A (Tracking issue)[cite: 1] | Prevents ad optimization on real revenue[cite: 1] | High | Awaiting URL[cite: 1] |

## 8. Interventions

* **Problem 1: Excessive Mobile Scroll Depth (2,835px)**
  * *Evidence:* Mobile UX audit findings[cite: 1].
  * *User Impact:* Users struggle to locate the conversion action[cite: 1].
  * *Business Relevance:* Direct drop in KES 300 course enrolments[cite: 1].
  * *Priority:* High[cite: 1]
  * *Action:* Deploy Sticky Mobile CTA Bar ("Enroll Child Today - Only KES 300") via Moodle `Additional HTML`[cite: 1].

* **Problem 2: 41.8 MB Image Payload & 32.3s LCP**
  * *Evidence:* PageSpeed audit diagnostics[cite: 2].
  * *User Impact:* Severe loading frustration on cellular networks[cite: 2].
  * *Business Relevance:* High ad bounce rates and wasted acquisition costs[cite: 1].
  * *Priority:* Critical[cite: 2]
  * *Action:* Compress `/pix/` images (save ~26.1 MB), set hero image `fetchpriority="high"`, and enforce responsive sizing[cite: 2].

* **Problem 3: LMS Interface Clutter for Guests**
  * *Evidence:* Unauthenticated visitors see full Moodle navigation/footers[cite: 1].
  * *User Impact:* High cognitive load and visual distraction[cite: 1].
  * *Business Relevance:* Weakened landing page campaign conversion[cite: 1].
  * *Priority:* Medium[cite: 1]
  * *Action:* Suppress LMS boilerplate components for non-logged-in visitors and dock sidebar blocks[cite: 1].

* **Problem 4: Unvalidated GA4 Conversion Tracking**
  * *Evidence:* GA4 purchase tag lacking verification trigger[cite: 1].
  * *User Impact:* None[cite: 1].
  * *Business Relevance:* Inaccurate conversion reporting in Meta & Google Ads[cite: 1].
  * *Priority:* High[cite: 1]
  * *Action:* Configure GTM purchase trigger using M-PESA payment success redirect URL[cite: 1].

## 9. Results
Deployment of prepared HTML/CSS and GTM triggers is currently pending Moodle Site Administrator access and M-PESA redirect parameters[cite: 1]. Re-audit verification will follow deployment[cite: 1].

## 10. Remaining Issues
* **Pending Dependencies:** Granting of Moodle Site Admin privileges and provision of exact M-PESA post-payment redirect URL by technical team[cite: 1].
* **Long-Term Optimization:** Evaluating server-side caching and full CSS/JS minification pipelines within Moodle theme settings[cite: 1, 2].
