# Custom JavaScript Documentation

## CJS - GA4 Ecommerce Items (`cjs_ga4_ecommerce_items.js`)

### Purpose
Constructs a structured GA4 `items` array from validated purchase data.

### Logic & Output
The script reads the purchase value from the Data Layer, validates it as a positive numeric value, and returns a GA4-compatible item structure:
* `item_id`: `MSINGI_ACADEMY_SUB`
* `item_name`: `MsingiPACK Academy Subscription`
* `item_category`: `Subscriptions`
* `quantity`: `1`
* `price`: validated transaction value

### Validation Behavior
If the Data Layer contains an empty, malformed, or non-positive purchase value, the function should return an invalid/empty result that prevents the purchase tag from sending a financial conversion.

A hardcoded monetary fallback is intentionally **not** used. A missing value is a data-quality failure that should be investigated rather than converted into synthetic revenue.

### Data Integrity Principle
The M-PESA/Bank transaction is the financial source of truth. GTM custom JavaScript is responsible for validating and formatting the value, not inventing a value when the source data is unavailable.
