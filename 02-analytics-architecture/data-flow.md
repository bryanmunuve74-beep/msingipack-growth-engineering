# MsingiPACK Data Flow Architecture
## End-to-End Data Lifecycle & Transition Specification

### 1. Overview
This document outlines the end-to-end data lifecycle across the MsingiPACK measurement ecosystem (`msingipack.com` and `academy.msingipack.com`). Unlike system architecture—which defines components—data flow details the precise sequential transformation, persistence, and routing of user actions from initial ad touchpoint to financial reconciliation and analytical destination platforms.

---

### Flow 1: Acquisition
Tracks how prospective users discover the platform from external ad networks and land on the public web property with campaign metadata intact.

```
Meta Ad
  │
  ▼
Click
  │
  ▼
URL parameters (utm_source, fbclid, etc.)
  │
  ▼
MsingiPACK (msingipack.com)
```

#### Transition Explanation
* **Meta Ad:** A prospective parent or school administrator views and clicks a targeted ad campaign on Meta (or Google).
* **Click:** The browser opens the destination URL, passing embedded tracking tokens in the query string (`utm_source`, `utm_medium`, `utm_campaign`, `fbclid`, `gclid`).
* **URL Parameters:** Upon landing, raw parameters are exposed in the browser's address bar before page scripts execute.
* **MsingiPACK:** The user lands on `msingipack.com`, where initialization scripts immediately capture and secure the traffic source data.

---

### Flow 2: Attribution
Ensures campaign parameters survive multi-page browsing sessions and cross-subdomain transitions into the learning management portal without being mislabeled as "Direct/None".

```
UTM / FBCLID
  │
  ▼
Persistence mechanism (Custom HTML Tag Priority 100)
  │
  ▼
Secure 1st-Party Cookie (.msingipack.com)
  │
  ▼
academy.msingipack.com (LMS Transition)
  │
  ▼
Conversion Attribution
```

#### Transition Explanation
* **UTM/FBCLID:** Incoming campaign identifiers are parsed from the landing page URL search parameters.
* **Persistence Mechanism:** A high-priority Custom HTML tag (`CHTML UTM Persistence Guard`, Priority 100) executes immediately on page load to extract the parameters.
* **Cookie:** The script writes these values into a secure, 30-day 1st-party browser cookie scoped explicitly to the root domain (`.msingipack.com`).
* **academy.msingipack.com:** When the user navigates from the marketing site to the Moodle LMS subdomain, the root-domain cookie travels with the request.
* **Conversion:** Even if the user leaves and returns days later, downstream conversion events read the stored attribution token, crediting the original ad campaign correctly.

---

### Flow 3: Registration
Measures authentic user onboarding by tying conversion events to confirmed database records rather than superficial button interactions.

```
User
  │
  ▼
Registration Form Submission
  │
  ▼
Moodle Backend Database
  │
  ▼
Confirmed Registration (sign_up)
  │
  ▼
Data Layer (window.dataLayer.push)
  │
  ▼
Google Tag Manager (GTM-K75VD2V6)
  │
  ▼
GA4 / Meta Platforms
```

#### Transition Explanation
* **User:** The prospective client fills out and submits the registration form on the academy portal.
* **Registration Form Submission:** Frontend inputs are submitted to the server for validation and account creation.
* **Moodle Backend Database:** The database successfully processes and writes the new user profile, confirming account creation.
* **Confirmed Registration (sign_up):** The backend fires a verified success state, eliminating false positives caused by empty button clicks.
* **Data Layer:** A structured event payload (`sign_up`) is pushed into `window.dataLayer`.
* **Google Tag Manager:** GTM captures the custom event via its trigger architecture.
* **GA4 / Meta:** Outgoing event tags route the verified registration data to Google Analytics 4 and Meta Events Manager for campaign optimization.

---

### Flow 4: Purchase
Reconciles digital payment completions with financial ledgers while preventing duplicate transaction reporting.

```
User
  │
  ▼
M-PESA Daraja STK Push Initiation
  │
  ▼
Payment Success (Daraja API Confirmation)
  │
  ▼
Moodle LMS Payment Handler
  │
  ▼
window.dataLayer.push({ mpesa_purchase_success })
  │
  ▼
GTM Custom Event Trigger (GTM-K75VD2V6)
  │
  ▼
Variables Resolve (Defensive parseFloat & transaction_id)
  │
  ▼
GA4 Purchase Tag + Meta Purchase Tag (deduplicated via eventID)
  │
  ▼
GA4 / Meta Destination Reporting
```

#### Transition Explanation
* **User:** The parent or student initiates a subscription payment at checkout.
* **M-PESA Daraja STK Push:** An automated payment prompt is sent to the user's mobile phone via the Safaricom Daraja API.
* **Payment Success:** The user authorizes the transaction, and Daraja returns a confirmed payment confirmation containing a unique receipt ID.
* **Moodle LMS Payment Handler:** The backend payment verification handler intercepts the successful transaction response.
* **window.dataLayer.push:** The server injects the `mpesa_purchase_success` event containing the unique M-PESA receipt (`transaction_id`) and subscription amount (`value`).
* **GTM Custom Event Trigger:** Google Tag Manager detects the custom event in the container (`GTM-K75VD2V6`).
* **Variables Resolve:** Custom JavaScript variables execute defensive parsing (`parseFloat`, `isNaN`) to sanitize numerical amounts and format strict `snake_case` parameters.
* **GA4 & Meta Purchase Tags:** Separate tags fire simultaneously; GA4 receives clean e-commerce metrics while Meta receives the purchase event deduplicated via the M-PESA receipt code (`eventID`).
* **GA4 / Meta Destination Reporting:** Executive dashboards reflect a precise 1-to-1 match against actual bank and M-PESA production statements.

---

### Flow 5: Failed Payment
Captures transaction drop-offs and error contexts to enable retargeting and funnel optimization.

```
User
  │
  ▼
M-PESA STK Push
  │
  ▼
Failure (Timeout / Insufficient Funds / Cancellation)
  │
  ▼
Moodle LMS Error Handler
  │
  ▼
window.dataLayer.push({ payment_failed })
  │
  ▼
GTM Custom Event Trigger
  │
  ▼
GA4 Error Event Stream
```

#### Transition Explanation
* **User:** The user initiates an M-PESA payment prompt but encounters an issue.
* **M-PESA STK Push:** The transaction request fails due to user cancellation, network timeout, or insufficient account balance.
* **Failure:** The payment gateway returns an error code or failure status.
* **Moodle LMS Error Handler:** The backend captures the specific failure response.
* **window.dataLayer.push:** A `payment_failed` event is pushed along with a dynamic `failure_reason` string.
* **GTM Custom Event Trigger:** GTM captures the failure event using a dedicated custom event trigger.
* **GA4 Error Event Stream:** The failure reason is logged into GA4 custom dimensions, allowing growth teams to analyze drop-off bottlenecks and deploy recovery workflows.