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

The project focused on improving visibility across this customer journey and resolving measurement failures that made it difficult to distinguish genuine user conversions from frontend interactions, preserve campaign attribution, and reconcile digital analytics with actual payment activity.

---

## Business Problem

The existing measurement environment contained several structural gaps across the acquisition and conversion funnel.

Key issues identified included:

* Loss of campaign attribution between the marketing site and Moodle LMS.
* Registration events being associated with user intent rather than verified account creation.
* Weak visibility into post-registration activation.
* Duplicate or incomplete purchase event measurement.
* Disconnects between GA4, Meta, Moodle, and financial payment records.
* Front-end performance issues affecting the mobile user journey.

These issues reduced confidence in funnel analysis, campaign measurement, and revenue reporting.

---

## Project Objectives

The project was designed around four primary objectives:

1. Establish a reliable analytics and measurement architecture.
2. Preserve campaign attribution across the MsingiPACK web and LMS environments.
3. Measure verified registration and purchase events rather than relying on superficial UI interactions.
4. Validate analytics data against Moodle and payment records.

---

## What I Worked On

The implementation and analysis covered:

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
* GA4, Meta, Moodle, and payment-data reconciliation.
* Page performance and payload analysis.

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
      └── Meta
```

The detailed architecture is documented in [`02-analytics-architecture/`](02-analytics-architecture/).

---

## Repository Structure

```text
01-business-context/
```

Defines the business problem, funnel, leakage points, and success metrics.

```text
02-analytics-architecture/
```

Documents the measurement architecture, measurement plan, event schema, and data flow.

```text
03-gtm/
```

Documents the GTM container, tags, triggers, variables, and custom JavaScript implementation.

```text
04-attribution/
```

Documents UTM persistence, FBCLID persistence, cross-subdomain tracking, and the end-to-end attribution flow.

```text
05-conversion-tracking/
```

Documents registration, purchase, payment-failure, and deduplication architecture.

```text
06-debugging/
```

Documents observed problems, technical hypotheses, tests, and validation results.

```text
07-data-validation/
```

Documents reconciliation between GA4, Moodle, Meta, and payment data.

```text
08-performance/
```

Documents PageSpeed findings, payload analysis, and performance issues affecting the user journey.

```text
09-results/
```

Documents before/after comparisons, business implications, and project limitations.

```text
diagrams/
```

Contains editable Mermaid architecture diagrams used throughout the documentation.

```text
evidence/
```

Contains sanitized screenshots and non-sensitive supporting outputs.

---

## Key Measurement Principles

The implementation is based on several important distinctions:

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

The core conversion lifecycle is:

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
                GA4     Meta
```

Successful purchase measurement uses a transaction identifier to connect the analytics event to the underlying financial transaction.

See [`05-conversion-tracking/`](05-conversion-tracking/).

---

## Attribution Architecture

Campaign parameters are captured when users arrive from tagged acquisition links.

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

The attribution implementation is documented in [`04-attribution/`](04-attribution/).

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

Validation considers the relationship between:

* GA4
* Meta
* Moodle
* M-PESA / bank records
* GTM and Data Layer events

The detailed investigation and validation work is documented in [`06-debugging/`](06-debugging/) and [`07-data-validation/`](07-data-validation/).

---

## Performance Analysis

The performance work examines:

* Mobile loading performance.
* Core Web Vitals.
* Page payload size.
* Large media assets.
* JavaScript and rendering overhead.
* The relationship between technical performance and the user journey.

See [`08-performance/`](08-performance/).

---

## Results

Results are separated into:

* Observed and validated outcomes.
* Measurement improvements.
* Business implications.
* Modelled opportunities.
* Remaining limitations.

Where sufficient post-implementation data is unavailable, results are explicitly marked as not yet measured rather than estimated.

See [`09-results/`](09-results/).

---

## Evidence

Supporting evidence is stored in sanitized form under [`evidence/`](evidence/).

Evidence may include:

* GTM configuration screenshots.
* GTM Preview results.
* Browser Network / Application evidence.
* GA4 validation screenshots.
* Meta validation screenshots.
* Data reconciliation outputs.
* PageSpeed and performance results.

Production credentials, access tokens, payment credentials, and personally identifiable information are excluded.

---

## Project Status

The repository documents the investigation, architecture, implementation, debugging, validation, and performance analysis performed during the MsingiPACK Growth Engineering project.

Some commercial outcomes require additional post-deployment measurement periods before they can be treated as validated results.

---

## Documentation Navigation

Start with:

1. [`01-business-context/problem-definition.md`](01-business-context/problem-definition.md)
2. [`02-analytics-architecture/tracking-architecture.md`](02-analytics-architecture/tracking-architecture.md)
3. [`03-gtm/container-architecture.md`](03-gtm/container-architecture.md)
4. [`04-attribution/attribution-flow.md`](04-attribution/attribution-flow.md)
5. [`05-conversion-tracking/purchase.md`](05-conversion-tracking/purchase.md)
6. [`06-debugging/problems-found.md`](06-debugging/problems-found.md)
7. [`07-data-validation/reconciliation-methodology.md`](07-data-validation/reconciliation-methodology.md)
8. [`08-performance/performance-findings.md`](08-performance/performance-findings.md)
9. [`09-results/business-impact.md`](09-results/business-impact.md)

---

## Disclaimer

This repository is intended as a technical case study and documentation of the Growth Engineering work performed on the MsingiPACK platform.

Sensitive production information, credentials, and personally identifiable information should be removed or sanitized before public publication.
