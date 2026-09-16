# Custom JavaScript Documentation

## CJS - GA4 Ecommerce Items (`cjs_ga4_ecommerce_items.js`)

### Purpose
Constructs a structured GA4 items array while safeguarding against unparsed string values. 

### Logic & Output
The script extracts the data layer value, applies `parseFloat()`, and returns a strictly formatted array compliant with GA4 schema requirements:
* `item_id`: 'MSINGI_ACADEMY_SUB'
* `item_name`: 'MsingiPACK Academy Subscription'
* `item_category`: 'Subscriptions'
* `quantity`: 1

### Edge Cases & Fallback Behavior
If the data layer pushes an empty or malformed `value`, the `isNaN(rawValue)` check evaluates to true. The script defensively sets the item `price` to a hardcoded `2400.00`. 

*Validation Note:* This guarantees the GA4 tag will execute without throwing a JavaScript `SyntaxError`, but carries the business risk of reporting a default $2400.00 transaction in GA4 even if the actual user transaction failed or was fundamentally altered.
