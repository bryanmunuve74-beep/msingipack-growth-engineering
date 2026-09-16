# Problems Found

## 1. Overview
This document inventories the actual technical problems discovered during the MsingiPACK tracking integration. It distinguishes observed front-end symptoms from their verified technical root causes to ensure structural fixes to the architecture.

## 2. Problem Classification
Problems are categorized into three primary domains:
*   **Attribution & Routing:** Breakages in session continuity or parameter loss.
*   **Registration & Funnel Logic:** Misalignment between front-end tracking events and the Moodle database source of truth.
*   **Data Integrity:** Duplication of high-value conversion events.

## 3. Acquisition / Routing Problems
*   **Symptom:** Paid traffic landing on specific MsingiPACK course pages shows a near 100% bounce rate, while "Direct" traffic shows unusually high conversion rates.
*   **Verified Cause:** Moodle's authentication gate intercepts unauthenticated traffic requesting protected URLs. The HTTP 302 redirect to `academy.msingipack.com/login/index.php` strips query parameters (UTMs) before the page loads.

## 4. Registration / Funnel Problems
*   **Symptom:** Marketing analytics report significantly more registrations than the actual number of user accounts created in the Moodle backend.
*   **Verified Cause:** The GTM trigger for registration is bound to a front-end CTA click rather than confirmed account creation. Form validation errors and drop-offs trigger false conversions.

## 5. Attribution Problems
*   **Symptom:** Traffic moving from the main marketing site to the LMS loses its original acquisition source.
*   **Verified Cause:** Without proper cross-domain linking or root-domain cookie persistence, the session breaks upon crossing the boundary from `msingipack.com` to `academy.msingipack.com`.

## 6. Conversion Tracking Problems
*   **Symptom:** Activation (first login) is not being reliably tied back to the original acquisition source.
*   **Verified Cause:** Registration and activation are distinct steps. The lack of persistent tracking parameters during the Moodle account creation process orphans the subsequent activation event.

## 7. Payment / Revenue Measurement Problems
*   **Symptom:** E-commerce revenue in GA4 is inflated compared to actual database enrollment records.
*   **Verified Cause:** Users who refresh the Moodle course enrollment success page re-trigger the purchase event. 

## 8. Analytics Configuration Problems
*   **Symptom:** Cross-domain traffic is initiating new sessions.
*   **Verified Cause:** The GA4 web data stream is not configured to explicitly link `msingipack.com` and `academy.msingipack.com`.

## 9. Performance Problems
*   **Symptom:** Minor delays in tag execution on the registration page.
*   **Verified Cause:** No blocking performance issues found; data layer initialization is operating within acceptable thresholds (< 200ms).

## 10. Data Integrity Problems
*   **Symptom:** GA4 processes duplicate transactions.
*   **Verified Cause:** The data layer push is not passing a unique `transaction_id` from the Moodle database to deduplicate the hits.

## 11. Problem Severity
*   **Critical:** Registration logic failure (P-002), Subdomain session breaks (P-004).
*   **High:** Parameter stripping (P-001), Purchase duplication (P-003).
*   **Medium:** Orphaned activation events (P-005).

## 12. Problem Summary Table

| ID | Problem | System | Symptom | Business Impact | Severity | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| P-001 | Parameter stripping | Moodle | Paid traffic misclassified | Attribution loss | Critical | Fixed |
| P-002 | Registration fires on CTA | GTM | False conversions | Inflated lead count | Critical | Fixed |
| P-003 | Purchase duplicates | GA4 | Multiple events per buyer | Revenue distortion | High | Fixed |
| P-004 | Session break across subdomains | GA4 | Direct traffic inflation | Attribution loss | Critical | Fixed |
| P-005 | Orphaned activation | Moodle | Funnel drop-off | Incomplete user journey | Medium | Pending |
