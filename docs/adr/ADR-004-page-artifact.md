# ADR-004: Page Artifact and Manifest

**Status:** Accepted  
**Scope:** Phase 1 PDF Page Normalization

## Decision

A normalized document MUST have:

```text
output/
├── manifest.json
├── pages/
│   ├── page-000001.png
│   ├── page-000002.png
│   └── ...
└── metadata/
    ├── page-000001.json
    ├── page-000002.json
    └── ...
```

A Page Artifact conceptually consists of:

```text
Page Identity
+
Source Identity
+
PDF Metadata
+
Rendering Metadata
+
Image Metadata
+
Coordinate Metadata
```

The source PDF SHA-256 MUST be recorded as provenance.

Each page metadata object MUST contain at least:

```text
document_id
page_id

pdf_page_index
pdf_page_number
pdf_page_label

pdf width
pdf height
pdf rotation

render DPI
render format
render color space

image width
image height

PDF coordinate convention
image coordinate convention
```

The manifest MUST be able to enumerate and resolve every generated page image and its metadata.

## Rationale

The Page Artifact is the stable contract between PDF preprocessing and downstream Vision AI.
