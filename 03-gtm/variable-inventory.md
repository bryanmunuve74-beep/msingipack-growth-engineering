# Variable Documentation

## Data Layer Variables

| Name | Type | Source Key | Expected Format | Purpose | Failure Behavior |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `dlv - transaction_id` | Data Layer Variable | `transaction_id` | String | Pipeline deduplication. | Returns undefined. Required for Meta `eventID` and GA4. |
| `dlv - value` | Data Layer Variable | `value` | Number / String | Tracks payment total. | Returns undefined. Shielded by CJS parsing wrappers. |
| `dlv - failure_reason` | Data Layer Variable | `failure_reason` | String | Captures form or payment validation errors for drop-off analysis. | Returns undefined. |
| `dlv - cta_location` | Data Layer Variable | `cta_location` | String | Identify exact hero, navbar, or footer CTA driving MQLs. | Returns undefined. |
