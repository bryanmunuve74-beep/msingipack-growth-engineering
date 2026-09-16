# Registration Tracking

## 1. Overview
This document outlines the architecture for measuring user registrations in MsingiPACK. It details the transition from measuring front-end registration intent (button clicks) to measuring verified, back-end account creation, establishing a single source of truth for user acquisition.

## 2. Original Measurement Problem
Historically, registration conversions were measured based on front-end user interactions. 

**The Old Model (Intent-based):**
CTA Click ➔ Meta Conversion Fired

This created a significant data discrepancy. A user clicking "Register" did not guarantee an account was created. Users could abandon the form, encounter validation errors, or fail CAPTCHAs, resulting in analytics platforms reporting more registrations than actually existed in the database.

**The New Model (Verification-based):**
CTA Click ➔ Registration Process ➔ Moodle DB Write Succeeds ➔ `sign_up` Event

## 3. Registration Conversion Definition
A registration is strictly defined as: **The successful creation of a new Moodle user account in the primary database.** 

We do not count form starts, validation steps, or CTA clicks as "registrations." The system state must permanently change before the conversion is claimed.

## 4. Intent vs. Verified Conversion
To maintain accurate funnels, the tracking architecture separates intent from conversion:
*   **Intent:** Tracked as `begin_registration` (CTA clicks, form interaction, registration start).
*   **Conversion:** Tracked as `sign_up` (Confirmed Moodle account creation).

This separation is mapped directly to our GTM trigger configurations.

## 5. Registration Source
**Moodle is the authoritative source of truth.** GTM and the dataLayer serve strictly as the measurement and orchestration layer. The conversion signal is only generated *after* Moodle confirms the database account creation and passes this state to the client-side dataLayer.

## 6. Trigger Condition
The registration event fires based on the following flow:
Successful account creation ➔ `sign_up` pushed to dataLayer ➔ GTM Custom Event Trigger

*   **Event Name:** `sign_up`
*   **Trigger Type:** Custom Event
*   **Firing Condition:** Event exactly matches `sign_up`
*   **Dependencies:** Successful Moodle registration handler response.

## 7. Event Schema
The `sign_up` event pushes the following parameters to the dataLayer:

| Parameter | Type | Purpose |
| :--- | :--- | :--- |
| `event` | string | Event name (`sign_up`) |
| `attribution_data` | string | Campaign context retrieved from local storage (if present) |

## 8. GTM Processing
1.  Moodle pushes the `sign_up` event to the `dataLayer`.
2.  The GTM Custom Event Trigger (`CE - sign_up`) fires.
3.  Tags for GA4 and Meta are fired simultaneously.

## 9. GA4 Mapping
*   **Trigger:** `CE - sign_up`
*   **GA4 Event Name:** `sign_up`
*   **Key Event Status:** Marked as a Key Event (Conversion) in GA4 settings.

## 10. Meta Mapping
*   **Trigger:** `CE - sign_up`
*   **Meta Event Name:** `CompleteRegistration`
*   *Note:* Meta receives this explicitly as a completed action, cleanly distinguishing it from generic pageviews or clicks.

## 11. Attribution Association
Upon a verified `sign_up`, GTM retrieves stored attribution parameters (as defined in `04-attribution/`) and appends them to the event payload. This ensures the verified Moodle registration is correctly credited to the originating campaign.

## 12. Failure / Edge Cases
The following scenarios distinguish business state from tracking state and will **not** trigger a `sign_up` event:
*   Form submitted but DB write fails.
*   Account already exists.
*   Required field validation fails on the backend.
*   User abandons the form midway.
*   Tracking is blocked by the user's browser (Event fails to fire after account creation, representing an acceptable margin of tracking loss in favor of zero false positives).

## 13. Validation
To validate this tracking setup:
1.  Complete a test registration.
2.  Verify the new account exists in the Moodle database.
3.  Confirm `sign_up` fired *exactly once* in the browser console / GTM Preview.
4.  Confirm the GA4 Realtime and Meta Events Manager received the signal.

## 14. Limitations
If a user registers using a device with strict ad-blockers, the Moodle account will be created, but the client-side `sign_up` event may be blocked. The Moodle database remains the ultimate source of truth for total user counts.
