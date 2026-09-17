# Purchase Tracking

## 1. Overview
This document defines how MsingiPACK measures verified purchase events. A purchase is represented only after successful payment verification and must be linked to a unique transaction identifier.

## 2. Original Measurement Problem
Earlier purchase measurement could be tied to frontend page loads or checkout interactions. This allowed receipt-page refreshes or other browser behavior to create duplicate analytics events.

## 3. Purchase Definition
**Purchase = Verified successful payment + Unique transaction identifier**

The system does not count the following as a purchase:
* Checkout button clicks
* Payment page views
* M-PESA STK prompt appearances
* Receipt page loads without backend/payment verification

## 4. Source of Truth
Revenue measurement uses this hierarchy:
1. **M-PESA / Bank:** financial transaction truth.
2. **Moodle:** application/account/enrollment state.
3. **GTM:** client-side measurement orchestration.
4. **GA4 / Meta:** analytics and advertising measurement destinations.

Analytics events are measurements of the underlying business event; they are not the financial ledger.

## 5. Payment Confirmation Flow
`User → Checkout → M-PESA STK → Payment result → Moodle/payment handler → Verified success`

Once the payment is verified:
`Verified success → mpesa_purchase_success → dataLayer → GTM`

## 6. Purchase Event
Illustrative payload:

```javascript
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
```

The values above are examples, not production fallback values.

## 7. Transaction Identity
`M-PESA receipt → transaction_id → GA4 transaction identifier`

The documented Meta Pixel implementation also uses the transaction identifier as the event identifier for the client-side purchase signal.

The transaction identifier is the key reconciliation field because it connects analytics measurement back to the financial transaction.

## 8. Ecommerce Parameters

| Parameter | Type | Required? | Purpose |
| :--- | :--- | :--- | :--- |
| `transaction_id` | string | Yes | Unique M-PESA receipt/reference |
| `value` | number | Yes | Verified transaction value |
| `currency` | string | Yes | `KES` |
| `items` | array | Recommended | Course/product details |

## 9. GTM Processing
1. `dataLayer` receives `mpesa_purchase_success`.
2. GTM Custom Event Trigger fires.
3. Data Layer Variables extract `value`, `currency`, `transaction_id`, and `items`.
4. Purchase tags validate the required fields.
5. GA4 and Meta Pixel receive their respective purchase payloads when validation succeeds.

## 10. GA4 Mapping
`mpesa_purchase_success → GA4 purchase`

Mapped ecommerce fields include:
* `transaction_id`
* `value`
* `currency`
* `items`

GA4 duplicate purchase handling relies on the transaction identifier, not on the event-name formatting convention.

## 11. Meta Mapping
`mpesa_purchase_success → Meta Purchase`

The current documented implementation uses:
* `value`
* `currency`
* event identifier derived from `transaction_id`

Meta CAPI is not part of the current implementation.

## 12. Attribution Association
Purchase events may include persisted campaign fields as supporting reporting parameters:
`Purchase + saved attribution data + transaction_id → GA4 / Meta Pixel`

These custom fields do not automatically replace either platform's native attribution model.

## 13. Revenue Reconciliation
The intended reconciliation chain is:
**M-PESA/Bank record ↔ Moodle transaction/enrollment ↔ GA4 `transaction_id`**

Meta purchase reporting can be compared separately as an advertising measurement layer.

If a GA4 transaction ID does not exist in the financial records, it requires investigation. If a verified financial transaction has no corresponding GA4 event, it represents potential measurement loss.

## 14. Failure / Edge Cases
* **M-PESA timeout/cancellation:** should generate `payment_failed`, not `purchase`.
* **Network failure after payment:** the financial transaction may succeed even if the browser-side event is missed.
* **Ad blockers/privacy controls:** can prevent client-side GA4/Meta delivery.
* **Missing transaction ID/value:** purchase measurement should be blocked rather than assigned a synthetic value.

## 15. Validation
A complete purchase validation should verify:
1. A test payment is financially successful.
2. The M-PESA/Bank record contains the expected transaction reference and value.
3. Moodle reflects the expected application state.
4. One valid GA4 purchase is received with the matching transaction ID/value.
5. The documented Meta Pixel purchase signal is received with the expected event identifier.

## 16. Limitations
Client-side tracking cannot guarantee 100% parity with financial records because browsers can block measurement. The financial record remains authoritative, while GA4 and Meta are measurement destinations.
