# Variable Documentation

## Data Layer Variables

| Name | Type | Source Key | Expected Format | Purpose | Failure Behavior |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `dlv - transaction_id` | Data Layer Variable | `transaction_id` | String | Identifies the verified transaction for ecommerce reconciliation and deduplication. | Returns undefined; purchase should not fire without a valid ID. |
| `dlv - value` | Data Layer Variable | `value` | Number / numeric string | Provides the verified payment amount. | Returns undefined; purchase should not use a synthetic fallback. |
| `dlv - failure_reason` | Data Layer Variable | `failure_reason` | String | Captures the categorized reason for a failed payment. | Returns undefined when no failure reason is supplied. |
| `dlv - cta_location` | Data Layer Variable | `cta_location` | String | Identifies the location of a tracked CTA interaction where implemented. | Returns undefined when the event has no CTA location. |

## Attribution Variables

Persisted campaign fields are read from first-party cookies scoped to `.msingipack.com` and may be attached to downstream events as supporting attribution parameters.

## Validation Principle
Variables used for financial conversion measurement should fail closed: missing transaction identity or invalid monetary values should prevent a purchase event from being sent rather than generating synthetic revenue.
