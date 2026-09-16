# Payload Analysis

## 1. Objective
To audit the total asset weight and network transfer composition of `academy.msingipack.com`, identifying specific bandwidth bottlenecks hindering mobile conversion performance[cite: 1, 2].

## 2. Measurement Method
* **Audit Tool:** Google PageSpeed Insights (Mobile - Emulated Moto G Power)[cite: 2].
* **Scope:** Initial page load network waterfall and asset breakdown[cite: 2].

## 3. Total Page Payload
* **Total Initial Data Transfer:** ~41.8 MB[cite: 2]
* **Impact:** Extremely excessive payload for mobile networks, creating severe network congestion, slow LCP, and elevated drop-off rates for paid ad traffic[cite: 1, 2].

## 4. Resource Breakdown
The vast majority of the payload consists of uncompressed raster images located within Moodle's `/pix/` asset directory, supplemented by render-blocking scripts and unused styling[cite: 2].

| Asset Category | Estimated Size / Weight | Key Contributor | Diagnostic Impact |
|---|---|---|---|
| **Images / Media** | ~38.6 MB | `/pix/` directory images | Delays LCP to 32.3s; monopolizes network throughput[cite: 2]. |
| **Core JavaScript** | ~800 KB | `core/first.js`, `polyfill.js` | ~769 KB unused code; unnecessary legacy polyfills[cite: 2]. |
| **Unused CSS** | ~309 KB | `all.css`, theme CSS | Adds critical path latency[cite: 2]. |
| **Third-Party Scripts** | ~192 KB | GTM (~130 KB), Meta Pixel (~62 KB) | Increases parse times before page readiness[cite: 2]. |

## 5. Largest Resources

| Resource | Size | Type | Impact | Action |
| :--- | :--- | :--- | :--- | :--- |
| `child_mobile.jpg` | 15.3 MB | Image | Extreme mobile transfer bloat; stalls rendering | Compress & resize (Est. saving: 10.9 MB)[cite: 2] |
| `fam_laptop.jpg` | 11.0 MB | Image | Blocks downstream asset downloads | Compress & resize (Est. saving: 7.4 MB)[cite: 2] |
| `school_girl.jpg` | 9.7 MB | Image | Delays visual page completion | Compress & resize (Est. saving: 6.8 MB)[cite: 2] |
| `boy_girl_tab.jpg` | 2.6 MB | Image | Excessive network overhead | Compress & resize (Est. saving: 1.0 MB)[cite: 2] |
| `core/first.js` | ~769 KB | JavaScript | Unused JS execution & parsing overhead | Code split & defer non-essential functions[cite: 2] |
| `yui_combo.php` & `all.css` | ~309 KB | CSS/JS | Contributes to 5,360 ms render-blocking latency | Inline critical CSS; defer YUI combos[cite: 2] |

## 6. Images
The landing page transfers over 38 MB in unoptimized image assets[cite: 2]. The primary LCP element, `banner-img.jpeg`, is delivered at 1536 × 1024 px but rendered at ~388 × 259 px on mobile viewports[cite: 2]. Furthermore, images in `/pix/` lack modern formatting (WebP/AVIF) and explicit `width`/`height` attributes[cite: 2].

## 7. JavaScript
Core JavaScript includes ~769 KB of unreferenced code in `core/first.js`[cite: 2]. Additionally, `polyfill.js` serves ~37 KB of legacy features (`Array.from`, ES6+ math functions) that modern mobile devices execute natively, adding wasted bytes to every request[cite: 2].

## 8. CSS
The platform transfers ~309 KB of unused CSS during initial render[cite: 2]. Synchronous CSS loading blocks the primary rendering thread, delaying First Contentful Paint (FCP) to 4.5 seconds[cite: 2].

## 9. Fonts / Other Assets
Theme font resources and icon bundles contribute minor weight but suffer from render-blocking execution paths within Moodle's default appearance settings[cite: 1, 2].

## 10. Third-Party Resources
* **Google Tag Manager:** ~130 KB[cite: 2]
* **Facebook Pixel:** ~62 KB[cite: 2]
* *Impact:* Executes synchronously during early page lifecycle, consuming network slots prior to main content paint[cite: 2].

## 11. Network Waterfall Findings
Render-blocking resources (`yui_combo.php`, `all.css`, `polyfill.js`) introduce a 5,360 ms critical latency gap before the browser begins rendering visible DOM elements[cite: 2]. Uncompressed images subsequently saturate connection threads, stalling page completion[cite: 2].

## 12. Mobile Impact
Prospective parents on 3G/4G connections face a 41.8 MB transfer barrier[cite: 2]. Combined with a required 2,835px vertical scroll distance to reach the enrolment CTA, mobile user bounce rates escalate significantly prior to conversion[cite: 1, 2].

## 13. Largest Optimization Opportunities
1. **Image Compression & Resizing:** Save ~26.1 MB across the top 4 image assets alone[cite: 2].
2. **Hero Image Optimization:** Scale `banner-img.jpeg` to display size (388 × 259 px) and assign `fetchpriority="high"`[cite: 2].
3. **Polyfill Elimination:** Remove redundant 37 KB `polyfill.js` library[cite: 2].
4. **Unused Code Removal:** Strip ~309 KB unused CSS and ~769 KB unused JavaScript[cite: 2].

## 14. Changes Implemented
* Sticky Mobile CTA HTML/CSS prepared for deployment under Moodle `Additional HTML`[cite: 1].
* GA4 Purchase Event configured for KES 300 conversion tracking[cite: 1].
* Suppressed Moodle guest boilerplate recommendations compiled[cite: 1].

## 15. Validation
A secondary PageSpeed Insights audit will be executed upon receiving Moodle Site Administrator privileges and payment redirect specifications[cite: 1].
