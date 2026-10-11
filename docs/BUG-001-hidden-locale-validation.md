# BUG-001 — Hidden-locale validation failure lacks actionable feedback

**Classification:** Proposed UX bug / improvement request  
**Area:** Contentful Web App, product entry editor  
**Severity:** Minor (proposed; impact may increase for multilingual workflows)  
**Reproducibility:** **2/2** attempts using duplicated product entries  
**Status:** Reproduced locally; not reported to or confirmed by Contentful

## Preconditions

- Content type `page - Product` contains localized `Name` with uniqueness validation.
- English (`en-US`) and German (`de-DE`) are configured.
- Only English is enabled for display in the entry editor.
- The original product has German name `Schwarzes Kleid`.

## Steps to reproduce

1. Duplicate the existing product.
2. Set a unique, visible English name (for example `QA Test - Black dress`).
3. Leave the duplicated German `Name` unchanged and keep `de-DE` hidden in the entry editor.
4. Ensure other required fields have valid values.
5. Attempt to publish.

## Actual result

Publishing is blocked with a generic `Validation failed` message. The visible English `Name` looks valid; the UI feedback does not clearly identify the hidden German locale as the cause.

The failed publication request in **Chrome DevTools → Network → Response** provides the missing information:

```json
{
  "message": "Validation error",
  "details": {
    "errors": [
      {
        "name": "unique",
        "details": "Same field value present in other entry",
        "path": ["fields", "name", "de-DE"],
        "value": "Schwarzes Kleid"
      }
    ]
  }
}
```

*Excerpt omits request IDs and unrelated metadata.*

## Diagnosis and workaround

Duplicating the entry retained the German `Name`, which violated the uniqueness rule because another entry already had that value. **Uniqueness validation itself is correct.** The usability issue is that the source of the failure is hidden when `de-DE` is not enabled in the editor.

**Verified workaround:** enable `de-DE` under **Edit locales list**, change German `Name` to a unique value, and publish again. Publication succeeded. The same initial failure was reproduced on a second duplicate.

## Expected behavior / suggested improvement

The editor should identify the failing **field and locale**, even if the locale is hidden. Potential solutions:

- display a localized error such as `Name (de-DE): value already exists`;
- offer a link to reveal and focus the invalid localized field;
- notify the editor when hidden locales contain validation errors.

## Impact

Content editors may spend time troubleshooting fields that already appear valid or assume there is a generic server error. They should not need to inspect HTTP responses to identify a hidden invalid field.

## Evidence

1. [Generic validation message](../evidence/BUG-001-01-generic-ui-validation.webp)
2. [Network response identifying uniqueness violation in de-DE](../evidence/BUG-001-02-network-unique-de-DE.webp)
3. [German locale configured but hidden](../evidence/BUG-001-03-locale-visibility.webp)
4. [Successful publication after correction](../evidence/BUG-001-04-published-after-correction.webp)

## Limitations

This is a reproducible UX observation in the tested starter environment, **not** a claim of a security issue, a broken uniqueness rule, or an officially confirmed Contentful defect.
