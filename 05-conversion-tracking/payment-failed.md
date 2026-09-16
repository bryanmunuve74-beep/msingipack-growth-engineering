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

```javascript
window.dataLayer.push({
  event: 'payment_failed',
  failure_reason: 'insufficient_funds', // or 'timeout', 'cancelled'
  value: 2400.00
});
