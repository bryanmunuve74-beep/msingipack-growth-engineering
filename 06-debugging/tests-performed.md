# Tests Performed

## 1. Testing Strategy
Testing isolated specific behaviors across the MsingiPACK infrastructure (`msingipack.com` and `academy.msingipack.com`), validating the data layer and network requests rather than relying purely on delayed platform reporting.

## 2. Environment
*   **Platform:** MsingiPACK Staging Environment
*   **Tools:** GTM Preview Mode, Google Analytics Debugger, Chrome DevTools.

## 3. GTM Tests
Validated tag firing logic, specifically ensuring events align with the conceptual model: Click ≠ Registration ≠ Activation ≠ Purchase.

## 4. Attribution Tests
Simulated inbound traffic with UTM parameters to track session continuity through the Moodle authentication flow.

## 5. Registration Tests
Tested edge cases in the user creation flow: valid submissions, invalid submissions, and email duplication.

## 6. Purchase Tests
Processed test enrollments to verify data layer structure and deduplication logic upon return to the LMS.

## 7. Payment Failure Tests
Simulated failed enrollments to ensure the `purchase` tag is strictly isolated to successful routing blocks. (Verified: tags do not fire on failure).

## 8. Cross-Subdomain Tests
Navigated from the marketing site to the LMS to inspect `_gl` linker parameters and cookie persistence.

## 9. Browser / Device Tests
Executed core funnels on Chrome and Safari to ensure no immediate breakage from basic browser privacy controls.

## 10. Data Layer Tests
Inspected raw JavaScript object pushes for schema compliance with standard GA4 E-commerce structures.

## 11. GA4 Validation Tests
Monitored the DebugView in the GA4 property to ensure GTM payloads were successfully assigned to the correct session.

## 12. Meta Validation Tests
*(Out of Scope)* - Validation focused strictly on the core GA4 and Moodle tracking architecture for this phase.

## 13. Cypress / Automated Tests
Prepared baseline UI interaction scripts to automate future regression testing of the data layer.

## 14. Test Summary

| Test ID | Objective | Setup | Action | Expected | Actual | Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T-001 | Verify UTM persistence | Open `academy.msingipack.com` with UTMs | Request protected course | Parameters preserved | Parameters dropped | **Fail** |
| T-002 | Verify Registration Truth | Load registration form | Submit invalid data | No event fires | Event fires on CTA | **Fail** |
| T-003 | Verify Subdomain Linker | Land on `msingipack.com` | Click to academy | Client ID remains | Client ID resets | **Fail** |
| T-004 | Verify Purchase Dedup | Complete enrollment | Refresh success page | Deduplicated | Duplicate fires | **Fail** |
| T-005 | Verify Post-Fix UTM | Fix applied to Moodle | Request protected course | Parameters preserved | Parameters preserved | **Pass** |
| T-006 | Verify Post-Fix Reg | Fix applied to GTM | Submit invalid data | No event fires | No event fires | **Pass** |
