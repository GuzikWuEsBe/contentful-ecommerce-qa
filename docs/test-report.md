# Test execution report

**Date:** 2026-10-11  
**Environment:** Contentful Web App, `master`, e-commerce starter, `page - Product`  
**Product:** separate duplicated entry `QA Test - Black dress`  
**Tools:** Contentful editor; Chrome DevTools / Network; Postman  
**Locales:** `en-US`, `de-DE`

| Test ID | Outcome | Evidence |
|---|---|---|
| TC-001 | PASS, required `Internal name` prevented publishing | [Screenshot](../evidence/TC-001-required-internal-name.png) |
| TC-002 | PARTIAL, 258 characters rejected; exact 256 not verified | [Screenshot](../evidence/TC-002-maxlength-258-characters.png) |
| TC-003 | PASS, negative price rejected | [Screenshot](../evidence/TC-003-negative-price.png) |
| TC-004 | PASS, empty price rejected | [Screenshot](../evidence/TC-004-required-price.png) |
| TC-005 | PASS, 200 OK, 5/5 Postman assertions | [Screenshot](../evidence/TC-005-03-postman-assertions.png) |
| TC-006 | PASS, draft/published visibility comparison; 3/3 Preview and 6/6 final Delivery assertions | [Before](../evidence/TC-006-02-delivery-before-publish.png) · [Preview](../evidence/TC-006-03-preview-api.png) · [After](../evidence/TC-006-05-delivery-after-publish.png) |
| BUG-001 | Reproduced 2/2, UX feedback finding | [Network](../evidence/BUG-001-02-network-unique-de-DE.png) · [Report](BUG-001-hidden-locale-validation.md) |

**Summary:** Five complete test scenarios passed (TC-001, TC-003, TC-004, TC-005, TC-006); one scenario is partially verified (TC-002). One distinct UX issue was reproduced twice. No claim is made that Contentful has accepted a defect report.

## Observations

- **Validations work** for required `Internal name`, required `Price`, minimum price 0, and exceeding the 256-character maximum.
- **Localized uniqueness conflicts** were exposed by inspecting the failed publish request's response in DevTools. See BUG-001.
- **API content state** was consistent in this tested sequence: draft edits were visible to Preview before publication, while Delivery only showed the marker after publication.
- **Instrumentation:** Postman environment variables were required to resolve endpoint and authentication values; initial setup errors were corrected before executing the test cases, and are not counted as Contentful defects.

## Follow-up

- Retest the *exact* 256-character boundary with a generated 256-character string, in a valid entry, separately from the 257- and 258-character cases.
- Consider testing missing featured images and duplicate slugs in a future iteration (not covered in this run).
- If filing BUG-001 publicly, first verify current expected product behavior and follow the project's official feedback process.