# Cross-Subdomain Tracking

## 1. Overview
The most critical technical hurdle in the MsingiPACK tracking architecture is the transition between the marketing site and the learning management system (LMS). This document explains how attribution data successfully survives the movement from `msingipack.com` to `academy.msingipack.com`.

## 2. Domain Architecture
MsingiPACK operates across two primary environments:
* **`msingipack.com`**: Marketing / acquisition environment (Landing pages, information).
* **`academy.msingipack.com`**: Moodle / conversion environment (Registration, course consumption, checkout).

Because both are subdomains of the root domain, they can share first-party cookies if scoped correctly.

## 3. Original Tracking Problem
Historically, the user flow broke tracking:
`Ad click → msingipack.com (UTMs present) → navigation → academy.msingipack.com (UTMs stripped) → original campaign context unavailable at conversion`

## 4. Why Attribution Was Lost
Browsers treat subdomains as distinct hostnames. If a cookie is set strictly on `msingipack.com` or `www.msingipack.com`, it cannot be read by `academy.msingipack.com`. Furthermore, native URL parameters are lost the moment a user clicks a link to the academy, leaving the Moodle environment blind to the acquisition source.

## 5. Cookie Domain Strategy
To bridge the gap, the persistence layer explicitly sets the domain scope to the root.
* **Domain:** `.msingipack.com` (Note the leading dot).
* **Available to:** `msingipack.com`, `www.msingipack.com`, `academy.msingipack.com`, and any future subdomains.

## 6. Attribution Continuity
By elevating the cookie scope to the root domain, the architecture guarantees continuity:
`Initial campaign → utm / fbclid in URL → root-domain cookie created → marketing site browsing → user clicks to academy subdomain → cookie remains accessible → conversion fires with attribution`

## 7. GA4 Identity / Session Considerations
It is vital to note: **Attribution persistence ≠ GA4 session continuity**.
While our custom cookies ensure *campaign metadata* survives, GA4 still needs to recognize the *user* across subdomains to prevent starting a new session. This is achieved by ensuring GA4's native `_ga` cookie is also scoped to the root domain (which GA4 does automatically in most modern configurations) and ensuring GTM loads the identical GA4 Measurement ID across both environments.

## 8. User Navigation Flow
1. User lands on `www.msingipack.com/course-promo`.
2. Root-level cookies (`saved_utm_source`, etc.) are written.
3. User clicks "Enroll Now".
4. User lands on `academy.msingipack.com/login/signup.php`.
5. The `academy` GTM container reads the root-level cookies without interruption.

## 9. GTM Implementation
This architecture allows us to use standard First-Party Cookie variables in GTM on the academy subdomain. Because the cookie is root-scoped, `{{cookie - saved_utm_source}}` resolves successfully on Moodle pages without requiring complex URL pass-throughs or server-side syncs.

## 10. Known Redirect Risks
The most significant threat to this architecture involves application-level redirects.
Example: `User → preview_courses.php → Moodle redirect → homepage`.
If an ad points directly to a page that immediately issues a 301/302 redirect *before* GTM can load, the UTMs in the URL are destroyed before the persistence script can capture them. Our architecture requires the initial landing page to render fully so GTM can execute the `CHTML - UTM Persistence Guard`.

## 11. Validation
Validation confirmed that a cookie created on `www` could be read via `document.cookie` in the browser console while viewing `academy`. 

## 12. Limitations
This solution works seamlessly for subdomains (e.g., `academy.domain.com`), but cannot be used for cross-domain tracking (e.g., `msingipack.com` to `entirely-different-domain.com`). If MsingiPACK ever moves checkout to a third-party domain (like a standalone Shopify or Stripe checkout domain), this root-cookie strategy will fail and require GA4 linker parameters.
