# Trigger Documentation

## Trigger: Click - Get Started as Parent

### Trigger Type
Click - All Elements.

### Condition
`Click Text` | `matches RegEx (ignore case)` | `(?i)get started as parent`.

### Engineering Rationale
Previous strict string comparisons failed due to variations in button text casing (e.g., "Get started as parent" vs "Get Started As Parent"). Converting this to a case-insensitive RegEx match ensures 100% capture of button interactions regardless of text casing or nested DOM child elements.

---

## Trigger: Parent Registration CTA (Visibility)

### Trigger Type
Element Visibility.

### Condition
* **Selection Method:** CSS Selector (`a.btn-primary`).
* **Visibility Minimum:** 50% on screen.

### Engineering Rationale
Previously, this trigger suffered from a critical logic error: it referenced `Click Text`. Because element visibility triggers fire on scroll viewports where no click event exists, referencing click variables forced the condition to evaluate to false (zero-firing). This was resolved by stripping non-click filters and scoping purely to the CSS selector.

---

## Trigger: Custom Events (Transactional)

### 1. `Custom Event - mpesa_purchase_success`
* **Trigger Type:** Custom Event
* **Event Name:** `mpesa_purchase_success`
* **Rationale:** Listens for the backend Safaricom Daraja STK Push success callback. GTM has no native way to know an STK push was validated; it relies entirely on this explicit `dataLayer.push()` from the backend status handler.

### 2. `Custom Event - payment_failed`
* **Trigger Type:** Custom Event
* **Event Name:** `payment_failed`
* **Rationale:** Listens for background API payment errors, insufficient balance messages, or user cancellations. Handled as a strictly separate trigger from successful purchases to ensure analytics routing integrity.
