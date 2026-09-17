# MsingiPACK Growth Engineering

A technical Growth Engineering case study covering conversion tracking, attribution, analytics architecture, GTM implementation, debugging, data validation, and performance analysis for the MsingiPACK digital learning platform.

---

## Project Overview

MsingiPACK operates across a public marketing environment and a Moodle-based learning platform:

```text
msingipack.com
      │
      ▼
academy.msingipack.com
      │
      ▼
Moodle LMS
      │
      ▼
Registration → Activation → Payment
```

The project focused on improving visibility across this customer journey and resolving measurement failures that made it difficult to distinguish genuine user conversions from frontend interactions, preserve campaign context, and reconcile digital analytics with actual payment activity.

---

## Business Problem

The existing measurement environment contained structural gaps across the acquisition and conversion funnel:

* Campaign parameters could be lost during navigation into Moodle.
* Registration measurement could represent user intent rather than confirmed account creation.
* Post-registration activation was not cleanly separated from registration.
* Purchase measurement could be duplicated by browser behavior.
* GA4, Meta, Moodle, and payment records were not consistently reconciled.
* Front-end performance issues created additional mobile user-journey friction.

These issues reduced confidence in funnel analysis, campaign measurement, and revenue reporting.

---

## Project Objectives

1. Establish a reliable analytics and measurement architecture.
2. Preserve campaign metadata across the MsingiPACK web and LMS environments.
3. Measure verified registration and purchase events rather than superficial UI interactions.
4. Validate analytics data against Moodle and payment records.

---

## What I Worked On

* Google Analytics 4 and Google Tag Manager architecture.
* Event and measurement schema design.
* Data Layer event architecture.
* UTM and FBCLID persistence.
* Cross-subdomain attribution continuity.
* Registration conversion tracking.
* M-PESA purchase tracking.
* Payment failure tracking.
* Transaction-based purchase deduplication.
* GTM tags, triggers, variables, and custom JavaScript.
* Analytics debugging and validation.
* GA4, Meta, Moodle, and payment-data reconciliation methodology.
* Page performance and payload analysis.

The current implementation is primarily client-side. Meta CAPI, GA4 Measurement Protocol, and GTM Server-Side are documented as future enhancements rather than completed components.

---

## High-Level Architecture

```text
User
 │
 ▼
MsingiPACK Marketing Site
 │
 ├── UTM / FBCLID
 │
 ▼
Attribution Persistence
 │
 ▼
Root-Domain Cookie
 │
 ▼
academy.msingipack.com
 │
 ▼
Moodle LMS
 │
 ├── Registration
 │
 ├── Activation
 │
 └── Payment
       │
       ▼
  M-PESA / Bank
       │
       ▼
  Verified Business Event
       │
       ▼
    Data Layer
       │
       ▼
      GTM
      ├── GA4
      └── Meta Pixel
```

---

## Repository Structure

* `01-business-context/` — business problem, funnel, leakage points, and success metrics.
* `02-analytics-architecture/` — measurement architecture, measurement plan, event schema, and data flow.
* `03-gtm/` — GTM container, tags, triggers, variables, and custom JavaScript.
* `04-attribution/` — UTM persistence, FBCLID persistence, cross-subdomain tracking, and attribution flow.
* `05-conversion-tracking/` — registration, purchase, payment failure, and deduplication architecture.
* `06-debugging/` — observed problems, hypotheses, tests, and validation results.
* `07-data-validation/` — reconciliation methodology and pending GA4/Moodle/Meta comparisons.
* `08-performance/` — PageSpeed findings, payload analysis, and performance issues.
* `09-results/` — implementation outcomes, business implications, and limitations.
* `diagrams/` — architecture diagrams.
* `evidence/` — sanitized supporting evidence.

---

## Key Measurement Principles

```text
CTA Click
    ≠
Registration

Registration
    ≠
Activation

Activation
    ≠
Purchase

Purchase Event
    ≠
Unique Financial Transaction

Analytics Platform
    ≠
Financial Source of Truth
```

The architecture therefore treats backend business states and verified payment records as authoritative while using GTM as the client-side measurement and orchestration layer.

---

## Conversion Architecture

```text
Registration
     │
     ▼
Activation
     │
     ▼
Checkout
     │
     ├───────────────┐
     ▼               ▼
Payment Failed    Payment Success
     │               │
     ▼               ▼
payment_failed   mpesa_purchase_success
                     │
                     ▼
                    GTM
                  /     \
                GA4    Meta Pixel
```

Successful purchase measurement uses a transaction identifier to connect the analytics event to the underlying financial transaction.

See [`05-conversion-tracking/`](05-conversion-tracking/).

---

## Attribution Architecture

```text
UTM / FBCLID
     │
     ▼
Capture
     │
     ▼
First-Party Persistence
     │
     ▼
.msingipack.com
     │
     ▼
academy.msingipack.com
     │
     ▼
Conversion
```

The attribution implementation preserves campaign metadata. It does not by itself override GA4's native attribution model or guarantee cross-device continuity.

See [`04-attribution/`](04-attribution/).

---

## Debugging & Validation

The project uses an investigation sequence of:

```text
Problem
   ↓
Hypothesis
   ↓
Test
   ↓
Evidence
   ↓
Fix
   ↓
Validation
```

Validation considers the relationship between GA4, Meta, Moodle, M-PESA/Bank records, GTM, and Data Layer events.

See [`06-debugging/`](06-debugging/) and [`07-data-validation/`](07-data-validation/).

---

## Performance Analysis

The performance work examines mobile loading performance, Core Web Vitals, page payload size, large media assets, JavaScript/rendering overhead, and their relationship to the user journey.

See [`08-performance/`](08-performance/).

---

## Results

Results are separated into:

* Observed implementation changes.
* Measurement improvements.
* Business implications.
* Modelled opportunities.
* Remaining limitations.

Where sufficient post-implementation data is unavailable, results are explicitly marked as not yet measured rather than estimated.

See [`09-results/`](09-results/).

---

## Evidence

Supporting evidence is stored in sanitized form under [`evidence/`](evidence/).

Production credentials, access tokens, payment credentials, and personally identifiable information are excluded.

---

## Project Status

The repository documents the investigation, architecture, implementation, debugging, validation methodology, and performance analysis performed during the MsingiPACK Growth Engineering project.

Some commercial outcomes require additional post-deployment measurement before they can be treated as validated results.

---

## Documentation Navigation

Start with:

1. [`01-business-context/problem-definition.md`](01-business-context/problem-definition.md)
2. [`02-analytics-architecture/tracking_architecture.md`](02-analytics-architecture/tracking_architecture.md)
3. [`03-gtm/container-architecture.md`](03-gtm/container-architecture.md)
4. [`04-attribution/attribution_flow.md`](04-attribution/attribution_flow.md)
5. [`05-conversion-tracking/purchase.md`](05-conversion-tracking/purchase.md)
6. [`06-debugging/problems-found.md`](06-debugging/problems-found.md)
7. [`07-data-validation/reconciliation-methodology.md`](07-data-validation/reconciliation-methodology.md)
8. [`08-performance/performance-findings.md`](08-performance/performance-findings.md)
9. [`09-results/business-impact.md`](09-results/business-impact.md)

---

## Disclaimer

This repository is intended as a technical case study and documentation of the Growth Engineering work performed on the MsingiPACK platform.

Sensitive production information, credentials, and personally identifiable information should be removed or sanitized before public publication.
