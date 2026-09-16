# Attribution Flow

## 1. Overview
From the moment a user clicks a Meta advertisement until they eventually purchase a course, a complex network of data transfers must occur to maintain attribution. This document serves as the master map, explaining how the mechanisms detailed in previous documents work together as a cohesive, end-to-end system.

## 2. Attribution Architecture
```text
                  META AD
                     │
                     ▼
               User clicks
                     │
                     ▼
            Landing URL
        ┌────────────┴────────────┐
        │                         │
     UTM data                  FBCLID
        │                         │
        └────────────┬────────────┘
                     ▼
              Capture Layer
                     │
                     ▼
        Attribution Persistence
                     │
             .msingipack.com
                     │
                     ▼
             Marketing Site
                     │
                     ▼
          academy.msingipack.com
                     │
                     ▼
                User action
                     │
                     ▼
             Registration
                     │
                     ▼
                Purchase
                     │
                     ▼
        Attribution available
        during conversion
```

## 3. Acquisition Touchpoint
The journey begins off-site. A user clicks a properly tagged advertisement. The ad platform directs them to the MsingiPACK site, appending standard `utm_*` parameters and platform-specific identifiers like `fbclid` to the URL.

## 4. Parameter Capture
Before the user clicks anything else, the `CHTML - UTM Persistence Guard` tag fires in GTM (Initialization priority). It parses the volatile URL string, extracting the marketing metadata before the user can navigate away and lose it.

## 5. Persistence
The extracted data is instantly committed to local browser storage via first-party cookies. Crucially, these cookies (`saved_utm_source`, `saved_fbclid`, etc.) are assigned a 30-day lifespan, ensuring the user has time to evaluate the courses before buying.

## 6. Cross-Subdomain Transfer
Because MsingiPACK uses `msingipack.com` for marketing and `academy.msingipack.com` for the LMS, the cookies are scoped to the root domain (`.msingipack.com`). When the user clicks from the marketing site to the academy, their browser seamlessly passes the persistent attribution cookies to the new subdomain.

## 7. Returning User Behavior
If the user leaves the site and returns via an organic search two days later, the 30-day cookies remain intact. The system recognizes the return visit but preserves the original paid acquisition context, preventing the eventual conversion from being falsely attributed to "Google Organic" or "Direct".

## 8. Conversion Association
When the user successfully executes a key action (e.g., `sign_up` or `purchase`), GTM fires conversion tags. During this process, GTM reads the persistent cookies and attaches the values (`utm_source`, `fbclid`) to the outbound network request.

## 9. Attribution Data Flow
* **To GA4:** UTM parameters are sent with the `purchase` event, allowing business leaders to see which specific campaigns generated revenue.
* **To Meta:** The `fbclid` is sent with the Pixel payload, providing Meta with deterministic data to optimize future ad delivery.

## 10. Example User Journey
* **Day 0:** User clicks a Meta ad. The URL contains `?utm_source=facebook&utm_medium=cpc&fbclid=abc123`. The persistence layer creates 30-day root-domain cookies.
* **Day 1:** User browses the marketing site, then leaves. The attribution cookie remains.
* **Day 3:** User types `academy.msingipack.com` directly into their browser to view a course preview. The cookie remains available.
* **Day 5:** User completes the `sign_up` form. The attribution data attaches to the registration event.
* **Day 7:** User completes a `purchase`. The event is triggered. GA4 successfully attributes the purchase to the Facebook CPC campaign from Day 0.

## 11. Failure Scenarios
The system is robust, but vulnerable to specific edge cases:
* **Scenario A (Direct visit):** No UTM/fbclid exists in the URL. Nothing is persisted. Conversion is tracked as Direct.
* **Scenario B (Redirect stripping):** If an ad points to a URL that immediately redirects (e.g., HTTP to HTTPS, or a defunct PHP page redirecting to homepage), parameters are lost before GTM loads. Attribution is degraded.
* **Scenario C (Cookie expiration):** A user clicks an ad, but takes 35 days to purchase. The 30-day TTL expires. The purchase lacks attribution.
* **Scenario D (Cross-device):** User clicks ad on Mobile Safari (Device A), but completes the purchase later on Desktop Chrome (Device B). Cookies cannot bridge devices; attribution is lost.
* **Scenario E (Cookie blocking):** User has strict browser extensions preventing first-party cookies. The persistence mechanism fails.

## 12. Validation
End-to-end functionality is validated via debugging tools (Network Tab, GTM Preview, Meta Pixel Helper), ensuring that data accurately moves from Step 1 (URL click) to Step 8 (Network conversion payload).

## 13. Measurement Limitations
This architecture ensures the availability of data on the client side. However, the ultimate attribution modeling (e.g., First-Click, Last-Click, Data-Driven) is determined downstream by the receiving platforms (GA4/Meta), which apply their own logic to the data we provide.