# Contentful E-commerce QA | Manual & API Testing

**Personal QA case study · 11 October 2026 · Contentful e-commerce starter template**

I explored a sample product entry in Contentful, executed manual validation checks, investigated an unexpected publication failure using Chrome DevTools, and verified published versus draft data with automated assertions in Postman.

This is an independent learning project using sample content. It is **not** an official Contentful QA assignment or a confirmed Contentful product bug report.

## Results at a glance

| Item | Result |
|---|---|
| TC-001 · Required `Internal name` | **PASS** |
| TC-002 · `Internal name` max length | **PARTIAL**: 258 characters rejected; exact 256-character boundary not confirmed |
| TC-003 · Negative `Price` (-1) | **PASS** |
| TC-004 · Required `Price` | **PASS** |
| TC-005 · GET published entry via Delivery API | **PASS**, 5/5 Postman assertions |
| TC-006 · Draft vs published content | **PASS**, Preview 3/3; Delivery after publication 6/6 |
| BUG-001 · Hidden locale validation feedback | **Reproduced 2/2**, proposed UX improvement |

**Scope:** Contentful Web App, `page - Product` content type, `master` environment, English (`en-US`) and German (`de-DE`) locales. Sample product: `QA Test - Black dress`.

**Tools:** Contentful, Chrome DevTools (Network), Postman, HTTP/JSON, manual exploratory testing, negative testing, boundary-value analysis.

## Key finding: validation in a hidden locale

While publishing a duplicated product, I received a generic `Validation failed` message even though the **visible English `Name`** was valid. The response of the failed publication request in Chrome DevTools identified a **uniqueness conflict for `fields.name.de-DE`**, with the existing value `Schwarzes Kleid`. German was configured but hidden in the entry editor.

After enabling the German locale and changing its `Name` to a unique value, publication succeeded. I reproduced the same behavior on a second duplicated entry.

**Assessment:** uniqueness validation worked as intended; the UX improvement concerns the lack of clear feedback identifying an invalid field in a *hidden* locale. [Read the finding and proposed improvement](docs/BUG-001-hidden-locale-validation.md).

| Failed publication and API diagnosis | Successful publication after correction |
|---|---|
| ![Network response: unique validation in de-DE](evidence/BUG-001-02-network-unique-de-DE.png) | ![Contentful published after correction](evidence/BUG-001-04-published-after-correction.png) |

## API test: published versus draft

1. I published an initial product and verified its data using Content Delivery API (five Postman assertions passed).
2. I appended `QA-DRAFT-ONLY-TEST-006` to the English description **without publishing**.
3. Delivery API returned the older published description **without** the marker.
4. Preview API returned the modified description **with** the marker (three assertions passed).
5. After publishing, Delivery API returned the updated description **with** the marker (six assertions passed).

| Delivery API before publishing | Preview API before publishing | Delivery API after publishing |
|---|---|---|
| ![Delivery without marker](evidence/TC-006-02-delivery-before-publish.png) | ![Preview with marker](evidence/TC-006-03-preview-api.png) | ![Delivery with marker](evidence/TC-006-05-delivery-after-publish.png) |

## Repository contents

- [Test cases](docs/test-cases.md) — preconditions, procedures, expected and observed results.
- [Execution report](docs/test-report.md) — honest run-by-run status and evidence links.
- [BUG-001 finding](docs/BUG-001-hidden-locale-validation.md) — steps, response analysis, impact, suggestion, reproduction.
- [`evidence/`](evidence/) — screenshots from actual test execution, including the Network response and Postman assertions.
- [`postman/Contentful-Ecommerce-QA.postman_collection.json`](postman/Contentful-Ecommerce-QA.postman_collection.json) — reproducible requests and assertions reconstructed from this exercise.
- [`postman/Contentful-QA.example.postman_environment.json`](postman/Contentful-QA.example.postman_environment.json) — example variables **without credentials**.

## Running the API checks

1. Import the collection and the example environment into Postman.
2. Set `spaceId`, `environmentId`, `entryId`, `deliveryToken`, `previewToken`; keep access tokens private.
3. Set expected values (`expectedName`, `expectedPrice`, `expectedSlug`, `draftMarker`) to match a sample entry in your environment.
4. Run **TC-005** on a published entry. For **TC-006**, run the named requests at the matching publication stages. The *before publish* and *after publish* requests verify mutually exclusive states and should **not** be run together against an unchanged entry.

The Postman collection is a portable, credential-free representation of the requests and assertions executed during this case study.

**Security:** No actual API token is included in this repository. Never commit an environment export containing credentials or private authentication headers.

## Notes on interpretation

- A validation response is not automatically a bug. `Required`, min price `0`, and the `unique` rule were enforced correctly in the tested configuration.
- TC-002 is **not** reported as a successful 256-character boundary check: the attached evidence shows **258 characters** rejected and does not establish behavior at exactly 256.
- BUG-001 is a *reproduced usability finding / improvement suggestion*, not a claim that Contentful acknowledged or fixed a defect.
- API checks validate **one sample product and the observed request states**, not the entire Contentful service.