# ADR-006: Phase 1 Acceptance Criteria

**Status:** Accepted  
**Scope:** PDF Page Normalization

## Decision

Phase 1 is complete only when all of the following are satisfied:

1. Multi-page PDFs render without missing pages.
2. `page_id`, `pdf_page_index`, `pdf_page_number`, and `pdf_page_label` are represented separately.
3. Roman/custom/numeric PDF page labels are preserved when available.
4. Page rotations of 0°, 90°, 180°, and 270° are handled.
5. PDF-to-image coordinate conversion is deterministic.
6. Canonical evidence uses PDF coordinates.
7. Manifest and per-page metadata resolve every generated page.
8. Rendering configuration is fixed and recorded.
9. Source PDF identity is traceable using SHA-256.
10. Repeated rendering of identical source bytes with identical configuration produces equivalent artifacts.
11. Automated tests cover the above behavior.
12. Phase 1 has no external AI-service dependency.

## Definition of Done

The implementation is complete only when:

- all acceptance criteria pass;
- automated tests pass;
- the documented CLI works;
- generated artifacts conform to the implementation specification;
- no Phase 2/RI functionality is required for acceptance.

## Explicitly Out of Scope

- OCR
- Vision AI
- SMIR/RI
- translation
- RAG
- code generation
- IDE integration
- GUI
- adaptive rendering
- document semantic understanding