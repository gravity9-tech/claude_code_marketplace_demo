---
name: doc-generator-output-templates
description: Output templates and response formats for documentation page generation. Defines JSON response structure, metadata schema, and error handling templates. Use when generating documentation pages and returning status.
user-invocable: false
---

# Doc Generator Output Templates

Response formats and error templates for the deepwiki-doc-generator agent.

## Response Structure

All responses follow this JSON structure:

```json
{
  "status": "success | partial | failed",
  "page_path": "relative/path/to/page.md",
  "file_written": true,
  "file_location": "/absolute/path/to/wiki/relative/path/to/page.md",
  "file_size_bytes": 4521,
  "metadata": { ... },
  "warnings": [],
  "sections_generated": []
}
```

### Status Values

| Status | Meaning | Use When |
|--------|---------|----------|
| `success` | All operations completed | File written, all diagrams generated |
| `partial` | Completed with issues | Some diagrams failed, missing source files |
| `failed` | Could not complete | Invalid input, file write error |

---

## Success Response

```json
{
  "status": "success",
  "page_path": "architecture/system-architecture.md",
  "file_written": true,
  "file_location": "/path/to/wiki/architecture/system-architecture.md",
  "file_size_bytes": 4521,
  "metadata": {
    "generated_at": "2026-01-22T10:30:00Z",
    "word_count": 1247,
    "section_count": 5,
    "code_blocks": 3,
    "diagrams_generated": 2,
    "diagrams": [
      {
        "type": "c4-context",
        "title": "System Context",
        "status": "generated",
        "lines": 12
      },
      {
        "type": "c4-container",
        "title": "Container Architecture",
        "status": "generated",
        "lines": 18
      }
    ],
    "external_links": 5,
    "internal_links": 8,
    "evidence_sources": [
      "app/main.py:1-50",
      "docker-compose.yml",
      "package.json"
    ]
  },
  "warnings": [],
  "sections_generated": [
    "System Context and Scope",
    "Architectural Layers",
    "Major Components",
    "Technology Stack Overview",
    "Key Design Decisions"
  ]
}
```

---

## Metadata Schema

```json
{
  "metadata": {
    "generated_at": "[ISO timestamp]",
    "word_count": "[number]",
    "section_count": "[number]",
    "code_blocks": "[number]",
    "diagrams_generated": "[number]",
    "diagrams": [
      {
        "type": "[diagram type]",
        "title": "[diagram title]",
        "status": "generated | failed | skipped",
        "lines": "[number of lines]"
      }
    ],
    "external_links": "[number]",
    "internal_links": "[number]",
    "evidence_sources": ["[file:line references]"]
  }
}
```

### Field Descriptions

| Field | Type | Description |
|-------|------|-------------|
| `generated_at` | string | ISO 8601 timestamp of generation |
| `word_count` | number | Total words in generated content |
| `section_count` | number | Number of sections written |
| `code_blocks` | number | Number of code blocks included |
| `diagrams_generated` | number | Successfully generated diagrams |
| `diagrams` | array | Details for each diagram |
| `external_links` | number | Links to external resources |
| `internal_links` | number | Cross-references to other wiki pages |
| `evidence_sources` | array | Source files cited in content |

---

## Front Matter Template

All generated pages include YAML front matter:

```yaml
---
title: [Page Title]
description: [Short description]
generated_at: [ISO timestamp]
commit: [commit hash if available]
last_updated: [timestamp]
word_count: [count]
diagrams: [count]
---
```

---

## Error Responses

### Invalid Input

Missing or invalid parameters:

```json
{
  "status": "failed",
  "error": "Missing required parameter: 'page_path'",
  "code": "INVALID_INPUT"
}
```

### File Write Error

Cannot write to destination:

```json
{
  "status": "failed",
  "error": "Cannot write file: {path}",
  "code": "FILE_WRITE_ERROR"
}
```

---

## Partial Success Responses

### Diagram Generation Failure

Some diagrams could not be generated:

```json
{
  "status": "partial",
  "page_path": "architecture/system-architecture.md",
  "file_written": true,
  "metadata": {
    "diagrams_generated": 1,
    "diagrams": [
      {
        "type": "c4-context",
        "title": "System Context",
        "status": "generated",
        "lines": 12
      },
      {
        "type": "c4-container",
        "title": "Container Architecture",
        "status": "failed",
        "error": "Insufficient component data"
      }
    ]
  },
  "warnings": [
    "Diagram generation failed for 'c4-container': Insufficient component data",
    "Proceeding without diagram"
  ]
}
```

### Source Files Not Found

Expected source files missing:

```json
{
  "status": "partial",
  "page_path": "architecture/system-architecture.md",
  "file_written": true,
  "warnings": [
    "Could not locate expected source files: [patterns]",
    "Proceeding with available evidence"
  ]
}
```

---

## Error Codes

| Code | Meaning | Resolution |
|------|---------|------------|
| `INVALID_INPUT` | Missing/invalid parameters | Check required parameters |
| `FILE_WRITE_ERROR` | Cannot write to destination | Check permissions and path |
| `SITEMAP_NOT_FOUND` | Cannot read sitemap | Verify sitemap_path |
| `NO_EVIDENCE` | No source files found | Check source_files patterns |

---

**Version:** 1.0
**Last Updated:** 2026-01-22
