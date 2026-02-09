---
name: documentation-quality-checklist
description: Quality validation checklist for generated documentation pages. Covers content completeness, evidence backing, markdown syntax, diagram validation, and cross-reference consistency. Use before finalizing any documentation page.
user-invocable: false
---

# Documentation Quality Checklist

Validation criteria for generated documentation pages. Run through this checklist before writing any page to disk.

## Content Completeness

- [ ] All required sections are present
- [ ] All required diagrams are generated and embedded
- [ ] No TODO or placeholder text remains
- [ ] Introduction provides context and purpose
- [ ] Conclusion summarizes key points

## Evidence Backing

- [ ] Every major claim cites a source
- [ ] Code examples are real (from codebase, not invented)
- [ ] File references use correct paths
- [ ] Line numbers are accurate
- [ ] Evidence sources are listed in metadata

## Markdown Syntax

- [ ] Headings have proper hierarchy (h1 → h2 → h3)
- [ ] Code blocks include language specification
- [ ] Tables are properly formatted
- [ ] Lists use consistent formatting
- [ ] No broken markdown syntax

## Diagram Validation

- [ ] ASCII diagrams render correctly in monospace
- [ ] Diagram has descriptive title
- [ ] Diagram has explanatory caption
- [ ] Diagram content matches surrounding text
- [ ] Box widths are consistent

## Cross-References

- [ ] Internal links use relative paths: `[text](../../path/to/page.md)`
- [ ] Referenced pages exist in sitemap
- [ ] External links are properly formatted
- [ ] Bidirectional references where appropriate
- [ ] Breadcrumb navigation included if appropriate

## Word Count Guidelines

| Page Type | Target Range | Notes |
|-----------|--------------|-------|
| Overview pages | 500-1000 | Concise, high-level |
| Architecture pages | 1000-2000 | Detailed with diagrams |
| Component/service pages | 800-1500 | Focused on single topic |
| Workflow pages | 600-1200 | Step-by-step with diagrams |
| Reference pages | 300-800 | Tables and lists |

## Quick Validation

For rapid checks, verify these critical items:

1. **Required content exists** - All sections and diagrams present
2. **Evidence is real** - Code examples from actual codebase
3. **Links work** - Cross-references match sitemap
4. **Renders correctly** - Markdown syntax is valid

## Failure Criteria

A page should NOT be written if:

- Missing more than one required section
- No evidence sources cited
- Contains placeholder text (TODO, TBD, etc.)
- Markdown has syntax errors that break rendering
- Word count is below minimum for page type

---

**Version:** 1.0
**Last Updated:** 2026-01-22
