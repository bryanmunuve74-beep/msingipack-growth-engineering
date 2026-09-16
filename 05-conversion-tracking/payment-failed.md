# Payment Failure Tracking

## 1. Overview
This document details the negative conversion path in MsingiPACK's checkout flow. Tracking payment failures is critical because a failed payment represents a user who reached a high-intent stage but was blocked by a systemic or financial barrier, distinct from a user who simply lost interest.

## 2. Business Purpose
Why track failures? It allows the business to differentiate between:
*   *"No one attempted to buy"* (A marketing/pricing problem)
*   *"People attempted to buy but payment failed"* (A friction, technical, or financial problem)

This distinction dictates whether interventions should focus on top-of-funnel marketing or bottom-of-funnel payment gateway optimizations.

## 3. Failure Definition
A payment failure is defined as: **An attempted payment that receives a failure, cancellation, timeout, or other explicitly unsuccessful status from the payment processor (M-PESA/Gateway).**

This is structurally different from *checkout abandonment*, where a user simply leaves the page without ever initiating a transaction.

## 4. Failure Sources
Based on the MsingiPACK payment architecture, failure states include:
*   **M-PESA Timeout:** The STK push was sent, but the user did not enter their PIN in time.
*   **M-PESA Cancellation:** The user explicitly canceled the STK prompt on their phone.
*   **Insufficient Funds:** The user's M-PESA balance was lower than the transaction value.
*   **Gateway Failure:** API timeouts or M-PESA system outages.

## 5. Failure Event
When the Moodle backend or payment handler receives a negative callback, it pushes the following event:
javascript
window.dataLayer.push({
  event: 'payment_failed',
  failure_reason: 'insufficient_funds', // or 'timeout', 'cancelled'
  value: 2400.00
});
## 6. Failure Parameters

| Parameter | Type | Purpose |
| :--- | :--- | :--- |
| `event` | string | Identifies the failure state (`payment_failed`) |
| `failure_reason` | string | Categorizes the specific M-PESA error |
| `value` | number | Captures the revenue that was lost at the final step |

## 7. GTM Processing
1. `dataLayer` receives `payment_failed`.
2. A Custom Event Trigger fires.
3. A Data Layer Variable captures the `failure_reason`.
4. Tags dispatch this context to GA4.

## 8. GA4 Mapping
* **GA4 Event Name:** `payment_failed` (Custom Event)
* **Parameters:** `failure_reason`, `value`.
* *Note:* This allows for Custom Explorations in GA4 to map Drop-off rates specifically tied to gateway errors.

## 9. Funnel Analysis
This event creates the following measurable funnel:
Checkout initiated ➔ Payment attempted ➔ **Payment failed** ➔ User does not become customer.

## 10. Failure Categorization
By grouping `failure_reason` parameters, engineering and support teams can automatically triage issues. A spike in `timeout` might indicate user hesitation, whereas a spike in `gateway_error` indicates an API outage requiring immediate technical intervention.

## 11. Validation
To validate:
1. Initiate a checkout.
2. Cancel the M-PESA STK prompt on the mobile device.
3. Verify the backend receives the cancellation callback.
4. Verify `payment_failed` (with `failure_reason: 'cancelled'`) is pushed to the `dataLayer` and dispatched to GA4.

## 12. Limitations
If the user loses internet connection immediately after entering their PIN, M-PESA will process the payment, but the client browser may not receive the success or failure state.
