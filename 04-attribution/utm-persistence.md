# UTM Persistence

## 1. Overview
Campaign parameters are present on tagged acquisition URLs but can disappear as users navigate through the site. MsingiPACK captures selected campaign parameters and stores them in first-party cookies so the values remain available across the marketing site and Academy subdomain.

## 2. Problem Being Solved
Before the persistence layer, the documented failure path was:
`Campaign URL → user navigates → URL parameters disappear → downstream event has less campaign context`

The implementation addresses this by retaining campaign metadata separately from the URL.

## 3. Parameters Captured

| Parameter | Purpose | Captured? | Persisted? |
| :--- | :--- | :--- | :--- |
| `utm_source` | Campaign source | Yes | Yes |
| `utm_medium` | Marketing medium | Yes | Yes |
| `utm_campaign` | Campaign identifier | Yes | Yes |
| `fbclid` | Meta click identifier | Yes | Yes, documented separately |
| `gclid` | Google Ads click identifier | Implementation-dependent | Document according to the active container |
| `gbraid` / `wbraid` | Google identifiers | Implementation-dependent | Document according to the active container |
| `ttclid` | TikTok click identifier | Implementation-dependent | Document according to the active container |

## 4. Capture Mechanism
1. User arrives on a tagged landing URL.
2. GTM Initialization runs the `CHTML - UTM Persistence Guard`.
3. URL parameters are parsed.
4. Available values are written to the attribution cookies.

## 5. Persistence Mechanism
`utm_source`, `utm_medium`, and `utm_campaign` are stored in first-party cookies scoped to `.msingipack.com`.

This is an attribution persistence mechanism; it does not itself change GA4's native attribution processing.

## 6. Cookie Architecture
* **Cookie Names:** `saved_utm_source`, `saved_utm_medium`, `saved_utm_campaign`.
* **Domain Scope:** `.msingipack.com`.
* **Path:** `/`.
* **Secure Flag:** `true`.
* **Overwrite Behavior:** The current implementation updates the stored value when a later tagged visit supplies a new value. This should be treated as a **latest-touch persistence policy**, not first-touch storage.

## 7. Expiration / TTL
* **TTL:** 30 days (`max-age=2592000`).
* **Rationale:** The project selected a 30-day persistence window for the documented consideration-to-purchase journey.

The 30-day TTL does not guarantee 30 days of availability in every browser because privacy controls can shorten client-side storage lifetimes.

## 8. Retrieval Mechanism
1. First-party cookie
2. GTM cookie variable / custom reader
3. Conversion-event enrichment
4. Analytics payload

## 9. Downstream Usage
Persisted values may be attached to events such as `sign_up` and `purchase` as custom reporting fields.

These custom fields should not be described as overriding GA4's native session/source attribution. Native GA4 attribution remains controlled by GA4's own acquisition and attribution processing.

## 10. Failure Conditions
* No UTM exists: no new campaign values are written.
* Partial UTMs exist: only available values are updated.
* Cookie cannot be written: downstream custom attribution fields may be unavailable.
* Cookie expires: stored campaign metadata is no longer available.
* User changes device/browser: client-side cookies do not bridge devices.
* Immediate server-side redirect occurs before GTM can execute: the browser persistence layer cannot capture the original query parameters.

## 11. Validation
Validation should confirm:
`Tagged URL → cookie created → navigate to academy → cookie remains readable → conversion event contains the saved field`

The repository's debugging section documents the redirect/persistence tests separately.

## 12. Security / Privacy Considerations
The documented persistence layer stores campaign identifiers rather than email addresses or other direct PII.

## 13. Limitations
* Client-side storage is vulnerable to browser privacy controls.
* Cookies cannot bridge devices.
* Persistence of campaign metadata does not by itself guarantee native GA4 attribution to a campaign.
