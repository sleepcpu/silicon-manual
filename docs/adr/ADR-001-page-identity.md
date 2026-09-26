# ADR-001: Page Identity

**Status:** Accepted  
**Scope:** Phase 1 PDF Page Normalization

## Decision

Each physical PDF page receives a stable, document-scoped `page_id` using a 1-based sequence:

```text
page-000001
page-000002
page-000003
...
```

`page_id` is independent from the PDF's internal zero-based page index and from any PDF page label.

The following concepts MUST be stored separately:

- `page_id`: Silicon Manual page identity.
- `pdf_page_index`: zero-based index used by PDF APIs.
- `pdf_page_number`: one-based physical page number.
- `pdf_page_label`: PDF-defined display label, when available.

Example:

```json
{
  "page_id": "page-000021",
  "pdf_page_index": 20,
  "pdf_page_number": 21,
  "pdf_page_label": "17"
}
```

## Rationale

Technical manuals may contain front matter, Roman-numbered pages, custom labels, or section-specific numbering. These concepts must not be conflated.

AI MUST NOT be responsible for determining page identity.

## Consequences

- Page artifacts remain addressable even when PDF labels are non-numeric.
- `page_id` is deterministic for a fixed source PDF and page ordering.
- Different source PDF bytes MUST be treated as different document sources.
