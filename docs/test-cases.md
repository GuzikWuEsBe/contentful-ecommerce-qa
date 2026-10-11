# Manual and API test cases

**System under test:** Contentful Web App, e-commerce starter, content type `page - Product`. Tests were performed on a duplicate of sample product `Black dress`, not the original entry. Default locale: `en-US`.

## TC-001 · Required Internal name

- **Type:** Negative / required-field validation
- **Precondition:** Writable test product with other required fields valid.
- **Steps:** Clear `Internal name`; attempt to publish.
- **Expected:** Block publication and identify the missing field.
- **Actual:** `Required`, generic publication validation error, publication blocked.
- **Result:** **PASS**. [Evidence](../evidence/TC-001-required-internal-name.webp).

## TC-002 · Internal name length validation

- **Type:** Boundary analysis / maximum field length.
- **Rule:** 256 characters maximum.
- **Steps executed:** Entered an over-limit string, tried to publish.
- **Expected:** Reject strings longer than 256; accept exactly 256 if other data valid.
- **Actual:** Screenshot shows **258 characters**, `Please shorten the text so it's no longer than 256 characters`; publication blocked.
- **Result:** **PARTIAL**. We have not reliably confirmed acceptance of exactly 256 characters. [Evidence](../evidence/TC-002-maxlength-258-characters.webp).

## TC-003 · Negative price

- **Type:** Negative / lower-bound validation.
- **Steps:** Set `Price = -1`; attempt publication.
- **Expected:** Reject values below configured minimum 0.
- **Actual:** `Please enter a number no less than 0` and generic publication error; publication blocked.
- **Result:** **PASS**. [Evidence](../evidence/TC-003-negative-price.webp).

## TC-004 · Required price

- **Type:** Negative / required-field validation.
- **Steps:** Clear `Price`; attempt publication.
- **Expected:** Publishing blocked and required-field message shown.
- **Actual:** `Required`, generic publication error; publication blocked.
- **Result:** **PASS**. [Evidence](../evidence/TC-004-required-price.webp).

## TC-005 · Published product through Content Delivery API

- **Type:** GET / API contract / data integrity.
- **Precondition:** Product is published and Postman environment is selected.
- **Steps:** GET `https://cdn.contentful.com/spaces/{{spaceId}}/environments/{{environmentId}}/entries/{{entryId}}` using private Delivery Bearer token; run Postman assertions.
- **Expected:** HTTP 200; expected entry ID, name, price, and slug.
- **Actual:** `200 OK`, **5/5 assertions passed**.
- **Result:** **PASS**. [Evidence](../evidence/TC-005-03-postman-assertions.webp).

## TC-006 · Draft and published content through two APIs

- **Type:** Content workflow / API integration.
- **Precondition:** Already-published product; draft changes can be saved without publication.
- **Steps:**
  1. Add `QA-DRAFT-ONLY-TEST-006` to English description; do not publish.
  2. GET the entry with Content Delivery API.
  3. GET the same entry with Content Preview API.
  4. Publish changes in Contentful.
  5. GET the entry again with Content Delivery API.
- **Expected:** Marker absent in Delivery before publication; present in Preview before publication; present in Delivery after publication.
- **Actual:** Delivery before: `200 OK`, marker absent. Preview before: `200 OK`, marker present, **3/3** assertions. After publication: success in editor; Delivery `200 OK`, marker present, **6/6** assertions.
- **Result:** **PASS**. [Draft UI](../evidence/TC-006-01-unpublished-change.webp) · [Delivery before](../evidence/TC-006-02-delivery-before-publish.webp) · [Preview](../evidence/TC-006-03-preview-api.webp) · [Delivery after](../evidence/TC-006-05-delivery-after-publish.webp).

## INV-001 / BUG-001 · Hidden locale validation diagnostics

- **Type:** Exploratory investigation / usability improvement proposal.
- **Actual:** Duplication retained an existing German `name` that violated uniqueness; generic UI failure did not identify hidden `de-DE`. Network response did. Enabling German, changing its value, and republishing worked.
- **Reproducibility:** 2/2 reported executions.
- **Result:** **Reproduced UX finding** (not an official Contentful issue). [Full report](BUG-001-hidden-locale-validation.md).
