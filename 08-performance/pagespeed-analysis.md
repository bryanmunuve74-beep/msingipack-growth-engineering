# PageSpeed Analysis

## 1. Objective
To evaluate the technical loading and rendering performance of `academy.msingipack.com` under emulated mobile conditions[cite: 2], identifying specific front-end bottlenecks that increase bounce rates, delay user engagement, and negatively impact campaign conversion metrics for prospective parents[cite: 1, 2].

## 2. Test Environment
* **Tooling:** Google PageSpeed Insights[cite: 2].
* **Target Environment:** `academy.msingipack.com`[cite: 1, 2].
* **Emulated Device:** Moto G Power (Mobile)[cite: 2].

## 3. Pages Tested
* Primary Mobile Landing / Course Enrolment Page (`academy.msingipack.com`)[cite: 1, 2].

## 4. Device / Network Conditions
Testing was executed using mobile emulation (Moto G Power) over throttled mobile network parameters to simulate real-world conditions for parents arriving via Meta Ads and referral links[cite: 1, 2].

## 5. Baseline Results
* **Performance:** 57 / 100 (Critical Optimisation Required)[cite: 2]
* **Accessibility:** 91 / 100 (Good)[cite: 2]
* **Best Practices:** 96 / 100 (Excellent)[cite: 2]
* **SEO:** 91 / 100 (Good)[cite: 2]

## 6. Core Web Vitals
* **Largest Contentful Paint (LCP):** 32.3 s (Failed)[cite: 2]
  * *User Journey Impact:* Severe loading bottleneck affecting the main hero content[cite: 2]. Prospective parents arriving from paid ads wait over 32 seconds for the primary banner image (`banner-img.jpeg`) to render, drastically increasing immediate page abandonment[cite: 1, 2].
* **First Contentful Paint (FCP):** 4.5 s (Failed)[cite: 2]
  * *User Journey Impact:* A 4.5-second blank or partial screen delay creates an initial perception of an unresponsive site before any meaningful content becomes visible[cite: 2].
* **Cumulative Layout Shift (CLS):** 0.00 (Passed)[cite: 2]
  * *User Journey Impact:* Excellent visual stability; elements do not unexpectedly shift during load[cite: 2].
* **Total Blocking Time (TBT):** 100 ms (Passed)[cite: 2]
  * *User Journey Impact:* Good main-thread responsiveness with minimal JavaScript execution delays[cite: 2].
* **Speed Index (SI):** 8.9 s (Failed)[cite: 2]
  * *User Journey Impact:* Slow visual population of above-the-fold content negatively affects perceived performance[cite: 2].

## 7. Loading Performance
Critical rendering path latency is heavily constrained by render-blocking resources contributing approximately 5,360 ms of delay[cite: 2]. The browser must fetch, parse, and execute resources such as `/theme/yui_combo.php`, `all.css`, and `polyfill.js` before displaying initial page elements to visitors[cite: 2].

## 8. Rendering Performance
Hero rendering is severely delayed because the primary LCP element (`banner-img.jpeg`) is loaded at 1536 × 1024 px but displayed at only ~388 × 259 px[cite: 2]. Additionally, the image lacks a `fetchpriority="high"` attribute, forcing the browser to treat critical top-of-funnel visual assets with standard network priority[cite: 2].

## 9. JavaScript / Main-Thread Issues
While main-thread blocking time is low (TBT 100 ms)[cite: 2], JavaScript transfers contain significant overhead:
* **Unused Core Code:** `core/first.js` transfers ~769 KB of unused JavaScript during initial load[cite: 2].
* **Legacy Polyfills:** `polyfill.js` serves ~37 KB of redundant compatibility code (e.g., `Array.from`, ES6+ math methods) already natively supported by modern mobile browsers[cite: 2].
* **Tracking Overhead:** Google Tag Manager (~130 KB) and Facebook Pixel (~62 KB) add unnecessary client-side payload prior to conversion[cite: 2].

## 10. Images / Media Issues
Image payload represents the single largest technical failure on the platform[cite: 2]. Oversized first-party images in the `/pix/` directory account for the vast majority of an uncompressed 41.8 MB initial transfer weight[cite: 2]:
* `child_mobile.jpg`: 15.3 MB[cite: 2]
* `fam_laptop.jpg`: 11.0 MB[cite: 2]
* `school_girl.jpg`: 9.7 MB[cite: 2]
* `boy_girl_tab.jpg`: 2.6 MB[cite: 2]

## 11. Accessibility / SEO Findings
While Accessibility (91/100) and SEO (91/100) maintain strong baseline scores, key issues degrade user navigation[cite: 2]:
* **Contrast Deficiencies:** Insufficient colour contrast on "Register" and "Log in" header buttons, `h1` hero headings, and step/pricing cards[cite: 2].
* **HTML Validation Errors:** `<hr>` elements directly nested inside `<ul>` lists within pricing components[cite: 2].
* **Heading Order:** Inconsistent heading progression (e.g., H5 → H4 → H5) weakening document structure[cite: 2].
* **Layout Sizing:** Hero and category images lack explicit `width` and `height` attributes[cite: 2].

## 12. Highest-Impact Issues
1. **41.8 MB Total Initial Payload:** Driven primarily by raw, uncompressed `/pix/` image files[cite: 2].
2. **32.3s LCP Delay:** Caused by oversized hero assets and lack of priority fetching[cite: 2].
3. **2,835px Mobile Scroll Distance:** Unnecessary vertical depth required to reach the primary enrolment button[cite: 1].
4. **5,360 ms Render-Blocking Latency:** Triggered by synchronous CSS and legacy YUI/polyfill scripts[cite: 2].

## 13. Recommended / Implemented Changes
* **Sticky Mobile CTA Deployment:** Prepared a fixed responsive bottom bar ("Enroll Child Today - Only KES 300") via Moodle `Additional HTML` to eliminate the 2,835px scroll gap[cite: 1].
* **LMS Boilerplate Suppression:** Suppressed side panels, navigation menus, and stacked footers for unauthenticated guests[cite: 1].
* **Asset Optimization Pipeline:** Resized and compressed primary image assets (`child_mobile.jpg`, `fam_laptop.jpg`, etc.)[cite: 2].
* **Hero LCP Prioritization:** Added explicit dimensions and fetch priority to `banner-img.jpeg`[cite: 2].
* **Script Optimization:** Deferred legacy polyfills and unused core JavaScript[cite: 2].

## 14. Post-Change Results
* **Target Performance Score:** > 85 / 100
* **Target LCP:** < 2.5s
* **Target Payload:** < 3.0 MB
* *(Validation scheduled following deployment of Moodle Site Administrator privileges)*[cite: 1]

## 15. Limitations
Assessment based on emulated Moto G Power synthetic lab testing in PageSpeed Insights[cite: 2]. Field Data (CrUX) and GA4 purchase validation require live campaign execution following M-PESA success redirect configuration[cite: 1].
