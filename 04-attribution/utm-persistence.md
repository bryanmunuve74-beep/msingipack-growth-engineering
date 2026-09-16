# UTM Persistence

## 1. Overview
Campaign parameters (UTMs) are appended to acquisition URLs to pass marketing context. However, these parameters natively exist only during the initial page load. As users navigate through the site, the URL updates and the parameters disappear. This document outlines how MsingiPACK captures and persists these parameters to ensure downstream conversions retain their original campaign context.

## 2. Problem Being Solved
Before this implementation, the attribution flow suffered from a fundamental disconnect:
`Meta/Google campaign → URL contains UTMs → User navigates site → Parameters disappear → Conversion has no campaign context`

This resulted in:
* Incorrect acquisition classification (paid traffic categorized as direct/referral).
* Attribution degradation across the funnel.
* Difficulty measuring true campaign performance.
* Inability to confidently connect paid traffic to revenue.

## 3. UTM Parameters Captured
The system specifically listens for and captures standard marketing parameters. 

| Parameter | Purpose | Captured? | Persisted? |
| :--- | :--- | :--- | :--- |
| `utm_source` | Campaign source (e.g., facebook, google) | Yes | Yes |
| `utm_medium` | Marketing medium (e.g., cpc, social) | Yes | Yes |
| `utm_campaign` | Campaign identifier / name | Yes | Yes |
| `gclid` | Google Ads click identifier | No* | No |
| `gbraid` / `wbraid` | iOS14+ Google identifiers | No* | No |
| `ttclid` | TikTok click identifier | No* | No |

*(Note: Click identifiers like GCLID are natively handled by platform-specific tags, but for our custom persistence architecture, we strictly manage UTMs and FBCLID. FBCLID is documented separately in `fbclid-persistence.md`)*

## 4. Capture Mechanism
When a user lands on the site:
1. The user arrives at the **Landing URL**.
2. The browser's `URLSearchParams` parses the query string.
3. UTM values are extracted from the URL.
4. The persistence script is triggered.

This is executed via GTM using the **CHTML - UTM Persistence Guard**:
* **Priority:** 100 (Ensuring it fires before conversion tags).
* **Trigger:** Initialization - All Pages.

## 5. Persistence Mechanism
Once extracted, the UTM parameters are immediately written to first-party cookies. 
`UTM parameters → saved_utm_source, saved_utm_medium, saved_utm_campaign → first-party cookie`

By migrating these values from the volatile URL to persistent storage, the user's acquisition context travels with them across subsequent pageviews.

## 6. Cookie Architecture
* **Cookie Names:** `saved_utm_source`, `saved_utm_medium`, `saved_utm_campaign`
* **Domain Scope:** `.msingipack.com` (Crucial for cross-subdomain tracking)
* **Path:** `/` (Available site-wide)
* **Secure Flag:** `true` (HTTPS only)
* **Overwrite Behavior:** If new UTMs are detected in a subsequent visit, the cookie values are overwritten with the latest touchpoint data.

## 7. Expiration / TTL
* **TTL:** 30 days (`max-age = 2592000`)
* **Rationale:** A 30-day window was chosen to encompass the typical MsingiPACK consideration-to-purchase cycle. If a user clicks an ad and returns to purchase within 30 days, the conversion will be attributed to that campaign. 

## 8. Retrieval Mechanism
Downstream retrieval happens automatically during user actions:
1. First-party Cookie
2. Cookie reader (GTM First-Party Cookie Variables)
3. e.g., `{{cookie - saved_utm_source}}`
4. Injected into GA4 or custom event payloads.

## 9. Downstream Usage
The retrieved values are utilized during key conversion events (like `sign_up` and `purchase`). 
`saved_utm_* variables → GTM conversion tags → Analytics payload → GA4`
This allows GA4 to map the conversion back to the original acquisition source, overriding the "direct" or "referral" session source that would normally occur deep in the academy.

## 10. Failure Conditions
* **No UTM exists:** Script executes but finds no parameters; existing cookies (if any) remain untouched.
* **Only partial UTMs exist:** Only the present parameters are updated.
* **Cookie cannot be written:** Browser blocks first-party cookies; attribution falls back to native in-session tracking (often resulting in direct/referral).
* **Cookie has expired:** If >30 days pass, the next visit is treated as a new acquisition source.
* **User changes browser/device:** Cookies are device/browser-specific. A mobile click followed by a desktop purchase breaks the persistence chain.

## 11. Validation
The mechanism was validated by simulating the user journey:
`Ad URL → landing page → verify cookie created in DevTools → navigate to academy → verify cookie still available → trigger conversion event → verify attribution fields in GTM network payload.`

## 12. Security / Privacy Considerations
The cookies strictly contain non-PII (Personally Identifiable Information) marketing metadata. No user identities, emails, or sensitive session tokens are stored in the UTM persistence layer.

## 13. Limitations
* Relies on client-side cookie storage.
* Vulnerable to strict browser privacy features (e.g., ITP on Safari) which may cap first-party cookie lifespans to 7 days or 24 hours regardless of our 30-day TTL setting.
