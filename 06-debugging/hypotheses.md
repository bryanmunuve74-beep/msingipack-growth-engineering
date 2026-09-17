# Debugging Hypotheses

## 1. Overview
This document translates observed MsingiPACK measurement symptoms into testable engineering hypotheses. Conclusions are limited to what was directly tested or supported by the documented investigation.

## 2. Hypothesis Method
Each hypothesis records the observation, expected mechanism, failure signature, evidence required, test, result, and conclusion.

## 3. H1 — Landing / Redirect Attribution Failure

### Observation
Paid traffic landing on protected Academy pages could lose campaign parameters after authentication redirects.

### Hypothesis
Moodle's authentication flow redirects unauthenticated requests and the original query parameters are not preserved in the resolved URL.

### Expected Failure Signature
The browser/network flow shows the original tagged request followed by a redirect to `/login/index.php` without the original campaign parameters.

### Test
Open a private browser window and request a protected Academy URL containing test UTM parameters. Inspect the redirect chain.

### Result
The documented test observed a 302 redirect to `/login/index.php` and the campaign parameters were absent from the final resolved URL.

### Conclusion
**Supported.**

## 4. H2 — Registration Measurement Misalignment

### Observation
The earlier registration measurement could fire from front-end intent rather than completed account creation.

### Hypothesis
The GTM registration trigger was bound to a CTA interaction instead of confirmed Moodle account creation.

### Test
Submit invalid/duplicate registration data and inspect GTM firing and Moodle account creation.

### Result
The documented test observed the registration tag firing from the click interaction while the server returned a validation error and no new account was created.

### Conclusion
**Supported.**

## 5. H3 — Post-Registration Activation Attribution Limitation

### Observation
Registration and later activation can occur as separate states.

### Hypothesis
Client-side attribution persistence can preserve campaign metadata, but it cannot by itself guarantee identity continuity when a user changes device/browser.

### Conclusion
**Supported as a measurement limitation.** This is not equivalent to proving that every activation starts a new GA4 session.

## 6. H4 — Purchase Deduplication Failure

### Observation
Historical analytics contained multiple purchase events for a small number of unique buyers.

### Hypothesis
Purchase measurement could be retriggered on success-page reloads when a stable transaction identifier was absent.

### Test
Inspect the purchase Data Layer event on the initial success render and after refreshing the success page.

### Result
The documented investigation observed duplicate purchase firing on reload and identified the lack of a reliable transaction identifier as the structural issue.

### Conclusion
**Supported.**

## 7. H5 — Native GA4 Session Continuity Requires Separate Validation

### Observation
Custom attribution cookies remain readable across the MsingiPACK subdomains.

### Hypothesis
Native GA4 session continuity should be validated independently from custom cookie persistence.

### Conclusion
**Supported as an architecture distinction.** A root-domain custom cookie is not itself proof of a continuous GA4 session.

## 8. Hypothesis Status Summary

| ID | Hypothesis | Status | Next Action |
| :--- | :--- | :--- | :--- |
| H1 | Redirect can strip campaign parameters | Supported | Preserve parameters before redirect / persist attribution |
| H2 | Registration trigger can count intent | Supported | Trigger from confirmed account creation |
| H3 | Cross-device activation attribution is limited | Supported limitation | Consider durable authenticated identity |
| H4 | Success-page reload can duplicate purchases | Supported | Use stable transaction identity and retrigger protection |
| H5 | Custom cookie persistence is separate from GA4 session continuity | Supported distinction | Validate GA4 configuration independently |
