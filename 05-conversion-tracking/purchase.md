# Purchase Tracking

## 1. Overview
This document outlines the measurement of verified revenue events. It details how MsingiPACK tracks successful course enrollments via M-PESA, ensuring that analytics platforms only record purchases that have been financially verified and linked to unique transaction identifiers.

## 2. Original Measurement Problem
In previous iterations, purchase tracking was tied to front-end page loads or checkout button clicks. This led to high discrepancies where users loading a payment page, initiating an M-PESA STK prompt, or refreshing a receipt page generated false revenue in analytics. 

## 3. Purchase Definition
A purchase in the MsingiPACK architecture is strictly defined as:
**Purchase = Verified successful payment + Unique transaction identifier**

The system does NOT count the following as a purchase:
*   Checkout button clicks
*   Payment page views
*   M-PESA STK prompt appearances
*   Receipt page loads (without backend verification)

## 4. Source of Truth
Revenue measurement relies on a strict hierarchy of truth:
1.  **M-PESA / Bank:** Financial confirmation (The ultimate source of truth).
2.  **Moodle:** Application/account state (Course unlocked).
3.  **GTM:** Measurement orchestration.
4.  **GA4 / Meta:** Analytics destinations.

Analytics data is treated as a reflection of financial reality, not the reality itself.

## 5. Payment Confirmation Flow
The lifecycle of a verified purchase is as follows:
User ➔ Checkout ➔ M-PESA STK ➔ Payment result ➔ Moodle/Payment Handler ➔ Verified Success

Once verified:
Verified Success ➔ `mpesa_purchase_success` ➔ dataLayer ➔ GTM

## 6. Purchase Event
When Moodle confirms the M-PESA callback was successful, it pushes the following payload to the `dataLayer`:

javascript
window.dataLayer.push({
  event: 'mpesa_purchase_success',
  value: 2400.00,
  currency: 'KES',
  transaction_id: 'NL810XX99',
  items: [{
item_name: 'MsingiPACK CBC Grade 4',
item_id: 'COURSE-GR4',
price: 2400.00,
quantity: 1
}]
}); 
## 7. Transaction Identity
The core of our revenue tracking is the Transaction Identity. 
M-PESA receipt ➔ `transaction_id` ➔ GA4 transaction identifier ➔ Meta `eventID`

This identifier is critical because it guarantees:
* Unique transaction recognition
* Accurate revenue reconciliation with accounting
* A strong defense against duplicate event processing

## 8. Ecommerce Parameters
The event pushes the following required parameters:

| Parameter | Type | Required? | Purpose |
| :--- | :--- | :--- | :--- |
| `transaction_id` | string | Yes | Unique transaction (M-PESA receipt code) |
| `value` | number | Yes | Total revenue |
| `currency` | string | Yes | KES |
| `items` | array | Yes | Course/product details for ecommerce reporting |

## 9. GTM Processing
1. `dataLayer` receives `mpesa_purchase_success`.
2. GTM Custom Event Trigger fires.
3. Data Layer Variables extract `value`, `currency`, `transaction_id`, and `items`.
4. GA4 and Meta tags map these variables to their respective standardized schemas.

## 10. GA4 Mapping
The custom event translates to the standard GA4 schema:
`mpesa_purchase_success` ➔ GA4 `purchase`
* `transaction_id`
* `value`
* `currency`
* `items`

## 11. Meta Mapping
The custom event translates to the standard Meta schema:
`mpesa_purchase_success` ➔ Meta `Purchase`
* `value`
* `currency`
* `eventID` (mapped directly from `transaction_id` for deduplication)

## 12. Attribution Association
To understand marketing ROI, the purchase event is enriched with attribution data:
Purchase + Saved Attribution Data + `transaction_id` ➔ GA4 / Meta

## 13. Revenue Reconciliation
Technical measurement must tie back to business truth. The relationship is strictly 1:1:
**M-PESA receipt ↔ Moodle transaction ↔ GA4 `transaction_id` ↔ Meta `eventID`**

If GA4 reports a `transaction_id` that does not exist in M-PESA, the analytics data is invalid. If M-PESA has a receipt not in GA4, we have tracking loss.

## 14. Failure / Edge Cases
* **M-PESA Timeout:** User does not enter PIN. (Handled via `payment_failed`).
* **Network Failure post-payment:** Handled by backend cron jobs updating Moodle; requires server-side reconciliation, meaning client-side analytics might miss the real-time event.
* **Ad-blockers:** Browser blocks GTM, resulting in missing GA4/Meta data despite financial success.

## 15. Validation
To validate a purchase:
1. Execute 1 real payment.
2. Verify 1 financial transaction in M-PESA.
3. Verify 1 valid GA4 purchase (matching the exact value and M-PESA code).
4. Verify 1 Meta purchase signal (using M-PESA code as `eventID`).

## 16. Limitations
Client-side tracking cannot achieve 100% parity with financial databases due to browser privacy features. Analytics are used for directional marketing optimization, while the Moodle database remains the absolute ledger.
