# FBCLID Persistence

## 1. Overview
While UTM parameters provide human-readable campaign metadata, the `fbclid` (Facebook Click Identifier) is Meta's proprietary parameter used to accurately link an ad click to a downstream conversion. This document explains how MsingiPACK captures and persists the `fbclid` independently from UTMs.

## 2. Problem Being Solved
Meta's advertising algorithm relies heavily on matching site conversions back to ad impressions and clicks. When users navigate away from the landing page, the `fbclid` is stripped from the URL. If the conversion happens pages later, the Meta Pixel fires without the `fbclid`, severely degrading match quality, reducing ad attribution, and ultimately harming the algorithm's ability to optimize ad delivery.

## 3. FBCLID Role in Attribution
It is important to distinguish the roles:
* **UTM Parameters:** Standardized campaign metadata (used by GA4, internal analytics).
* **FBCLID:** Meta's specific click identifier (used exclusively by Meta to identify the exact user/click combination).

By preserving the `fbclid`, we ensure that even if Meta's native first-party cookie (`_fbc`) fails or drops, we have a redundant layer to pass back to the Pixel or Conversions API.

## 4. Capture Mechanism
`Meta Ad → fbclid appended to URL → Landing page → URL parsed → Persistence layer`
The same Initialization tag (`CHTML - UTM Persistence Guard`) that looks for UTMs simultaneously listens for the `fbclid` query parameter.

## 5. Persistence Mechanism
When an `fbclid` is detected, it is extracted and written to a dedicated first-party cookie, ensuring the click identifier survives internal navigation.

## 6. Cookie Scope and TTL
* **Cookie Name:** `saved_fbclid` (and/or Meta's native `_fbc`)
* **Domain Scope:** `.msingipack.com`
* **Path:** `/`
* **Expiration:** 30 days (`max-age = 2592000`)
* **Rationale:** Matches the 30-day UTM window, supporting delayed conversions typical of the course registration lifecycle.

## 7. Retrieval Mechanism
During a conversion event (like `purchase`), GTM retrieves the `saved_fbclid` value via a First-Party Cookie variable. 

## 8. Relationship with Meta Attribution
Meta natively attempts to create an `_fbc` cookie when an `fbclid` is present. Our custom persistence acts as an enforcement layer and ensures this identifier is explicitly formatted and available for our custom event payloads, particularly when crossing subdomains where native scripts might re-initialize and lose context.

## 9. Relationship with UTM Parameters
`fbclid` and UTM parameters are captured in parallel but serve different endpoints.
`Landing URL ?utm_source=fb&fbclid=123` -> Both are saved. UTMs go to GA4; FBCLID goes to the Meta Pixel. 

## 10. Downstream Conversion Usage
When a user completes a checkout on the Moodle platform:
`saved_fbclid → GTM Variable → Meta Pixel (Standard Event) → Meta Ad Manager`

## 11. Failure Conditions
* **No fbclid exists:** Occurs on direct visits, organic social clicks, or if the user has strict anti-tracking enabled.
* **iOS14+ limitations:** Apple's ATT framework may result in Meta withholding the `fbclid` from the URL entirely.
* **Browser stripping:** Some privacy-focused browsers actively strip known click identifiers (like `fbclid`) from URLs before the page even loads.

## 12. Validation
Tested using the Meta Pixel Helper extension and GTM Preview mode to confirm that the `fbclid` parameter successfully attaches to standard events (e.g., `CompleteRegistration`, `Purchase`) triggered on pages *other* than the landing page.

## 13. Limitations
The `fbclid` only links the event to Meta. It does not provide human-readable campaign names in GA4. It is strictly a platform-specific key.
