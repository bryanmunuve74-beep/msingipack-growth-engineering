# Cross-Subdomain Tracking

## 1. Overview
MsingiPACK operates across `msingipack.com` and `academy.msingipack.com`. This document explains how custom attribution data is preserved between those subdomains and distinguishes that mechanism from native GA4 session/identity measurement.

## 2. Domain Architecture
* **`msingipack.com`**: Marketing/acquisition environment.
* **`academy.msingipack.com`**: Moodle/LMS environment for registration, learning, and payment.

Both hosts share the `msingipack.com` root domain, so appropriately scoped first-party cookies can be readable by both.

## 3. Original Tracking Problem
`Ad click → msingipack.com → navigation → academy.msingipack.com → campaign parameters no longer in URL`

The persistence layer addresses the loss of campaign metadata from the URL.

## 4. Cookie Domain Strategy
Custom attribution cookies are scoped to `.msingipack.com` with path `/`, allowing the marketing site and Academy subdomain to access the same stored campaign fields.

## 5. Attribution Continuity
`UTM/fbclid in URL → root-domain cookie → marketing site → Academy subdomain → conversion event can read saved values`

This preserves **campaign metadata**. It does not by itself guarantee that GA4 treats the journey as one session or assigns native acquisition credit to the stored values.

## 6. GA4 Identity / Session Considerations
**Attribution persistence ≠ GA4 session continuity.**

The custom cookies are separate from GA4's own client/session identifiers. Native GA4 cross-domain measurement should be validated through the GA4 configuration and linker behavior where applicable. The fact that two hosts can read a custom root-domain cookie should not be presented as proof that GA4 has maintained a single session.

## 7. User Navigation Flow
1. User lands on the marketing site with tagged campaign parameters.
2. Root-domain attribution cookies are written.
3. User navigates to `academy.msingipack.com`.
4. Academy pages can read the saved attribution values.
5. Conversion events may include those saved values as supporting parameters.

## 8. GTM Implementation
The Academy container can use first-party cookie variables to read the root-scoped attribution fields without requiring the values to remain in every URL.

## 9. Known Redirect Risks
If an application/server redirect occurs before the GTM persistence tag executes, the browser may never capture the original query parameters. This is especially relevant to protected Moodle URLs that redirect unauthenticated users.

## 10. Validation
The documented validation checks that an attribution cookie created on the marketing host remains readable from the Academy host. Separate GA4 session/linker validation is required when making claims about native GA4 session continuity.

## 11. Limitations
* Root-domain cookies work for these MsingiPACK subdomains but not for a separate registrable domain.
* Cookies do not bridge devices.
* Browser privacy controls can limit persistence.
* Third-party checkout domains would require a different measurement architecture.
