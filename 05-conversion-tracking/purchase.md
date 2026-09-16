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
