# FBCLID Persistence

## 1. Overview
`fbclid` is a Meta click identifier that can be present on an ad landing URL. MsingiPACK captures and persists it separately from human-readable UTM campaign parameters so the identifier remains available during the documented client-side journey.

## 2. Problem Being Solved
The URL can lose `fbclid` as the user navigates away from the landing page. Persisting the value provides a stable copy for downstream measurement where the implementation explicitly uses it.

## 3. FBCLID Role in Attribution
* **UTM parameters:** Human-readable campaign metadata for analytics/reporting.
* **FBCLID:** Platform-specific click identifier associated with Meta traffic.

Persisting `fbclid` does not by itself guarantee Meta attribution or conversion matching. Meta's own browser identifiers and event-processing rules remain relevant.

## 4. Capture Mechanism
`Meta Ad → landing URL → fbclid query parameter → GTM persistence layer`

The documented `CHTML - UTM Persistence Guard` can capture `fbclid` alongside the UTM fields.

## 5. Persistence Mechanism
When present, the identifier is written to a first-party cookie so it remains available during navigation across the MsingiPACK subdomains.

## 6. Cookie Scope and TTL
* **Cookie Name:** `saved_fbclid`.
* **Domain Scope:** `.msingipack.com`.
* **Path:** `/`.
* **Expiration:** 30 days (`max-age=2592000`).

Meta's native `_fbc` behavior is separate from this custom cookie and should not be represented as something the custom cookie replaces.

## 7. Retrieval Mechanism
During a documented conversion event, GTM can read `saved_fbclid` through a cookie variable and make it available to the relevant client-side measurement payload if configured.

## 8. Relationship with Meta Attribution
The custom persistence layer is a supporting measurement mechanism. It does not itself implement Meta CAPI, server-side matching, or guarantee deterministic ad attribution.

## 9. Relationship with UTM Parameters
`fbclid` and UTM parameters are captured in parallel but serve different analytical purposes:
`utm_*` → campaign metadata
`fbclid` → Meta click identifier

## 10. Downstream Conversion Usage
The repository documents the intended path as:
`saved_fbclid → GTM variable → client-side Meta measurement`

Any server-side use would require a separate Meta CAPI implementation and is outside the current phase.

## 11. Failure Conditions
* No `fbclid` exists.
* Browser privacy controls strip or block the identifier.
* The initial URL redirects before GTM can capture it.
* Cookie storage is unavailable.
* User changes device/browser.

## 12. Validation
Validation should use a tagged test URL and GTM/browser inspection to confirm:
1. `fbclid` is present on landing.
2. `saved_fbclid` is created.
3. The cookie remains readable on the Academy subdomain.
4. The configured downstream event contains the expected value, if that field is part of the active implementation.

## 13. Limitations
`fbclid` is not a substitute for Meta's full attribution and matching system. Its presence in a client-side cookie does not by itself prove that Meta will attribute a conversion to a specific campaign.
