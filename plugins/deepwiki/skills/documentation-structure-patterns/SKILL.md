---
name: documentation-structure-patterns
description: Documentation structure heuristics and sitemap patterns for different codebase types and team sizes. Covers horizontal (concern-based), vertical (domain-based), and hybrid patterns. Use when planning documentation organization.
user-invocable: false
---

# Documentation Structure Patterns

Guidelines for organizing technical documentation based on codebase characteristics and team structure.

## Pattern Selection Overview

| Pattern | Best For | Team Size | Example Projects |
|---------|----------|-----------|------------------|
| **Pattern A (Horizontal)** | Single codebase, clear concerns | 5-15 people | Fullstack SPA, monolithic API |
| **Pattern B (Vertical)** | Multiple domains, clear boundaries | 10-30 people | Multi-product platform, B2B SaaS |
| **Pattern C (Hybrid)** | Monorepo, microservices | 20+ people | Enterprise platform, large open source |

---

## Pattern A: Horizontal (Concern-Based)

**Use when:**
- Single codebase with clear separation of concerns
- Team of 5-15 people
- Traditional MVC or layered architecture
- Monolithic application or simple fullstack

**Characteristics:**
- Organized by technical concern (architecture, API, database, etc.)
- Easier for developers to find cross-cutting topics
- Works well when team members work across the stack

### Structure Template

```
wiki/
├── README.md                    # Project overview and quick links
├── overview/
│   ├── system-overview.md       # What the system does
│   ├── technology-stack.md      # Languages, frameworks, tools
│   └── glossary.md              # Domain terminology
├── architecture/
│   ├── system-architecture.md   # High-level design
│   ├── data-model.md            # Database schema
│   └── api-design.md            # API patterns and conventions
├── development/
│   ├── getting-started.md       # Local setup guide
│   ├── coding-standards.md      # Style guide
│   └── testing.md               # Test strategy
├── deployment/
│   ├── infrastructure.md        # Cloud resources
│   ├── ci-cd.md                 # Pipeline documentation
│   └── environments.md          # Dev/staging/prod details
└── operations/
    ├── monitoring.md            # Observability setup
    ├── runbooks.md              # Incident procedures
    └── security.md              # Security practices
```

### Navigation Pattern

```markdown
# Project Wiki

## Quick Links
- [Getting Started](development/getting-started.md)
- [Architecture Overview](architecture/system-architecture.md)
- [API Reference](architecture/api-design.md)

## Sections
| Section | Description |
|---------|-------------|
| [Overview](overview/) | System purpose and technology |
| [Architecture](architecture/) | Design and technical decisions |
| [Development](development/) | Setup and coding practices |
| [Deployment](deployment/) | Infrastructure and CI/CD |
| [Operations](operations/) | Monitoring and security |
```

---

## Pattern B: Vertical (Domain-Based)

**Use when:**
- Clear frontend/backend/integration separation
- 3+ external integrations
- Team of 10-30 people
- Multiple product domains or features

**Characteristics:**
- Organized by business domain or feature
- Easier for product teams to find relevant docs
- Works well when teams own specific domains

### Structure Template

```
wiki/
├── README.md                    # Project overview
├── overview/
│   ├── system-overview.md
│   └── technology-stack.md
├── domains/
│   ├── user-management/
│   │   ├── README.md            # Domain overview
│   │   ├── authentication.md
│   │   ├── authorization.md
│   │   └── user-profiles.md
│   ├── billing/
│   │   ├── README.md
│   │   ├── subscriptions.md
│   │   ├── payments.md
│   │   └── invoicing.md
│   └── notifications/
│       ├── README.md
│       ├── email.md
│       ├── push.md
│       └── in-app.md
├── integrations/
│   ├── stripe.md
│   ├── sendgrid.md
│   └── twilio.md
├── platform/
│   ├── architecture.md
│   ├── infrastructure.md
│   └── security.md
└── development/
    ├── getting-started.md
    └── contributing.md
```

### Navigation Pattern

```markdown
# Project Wiki

## Domains
Each domain has its own documentation section:

| Domain | Owner | Description |
|--------|-------|-------------|
| [User Management](domains/user-management/) | Auth Team | Authentication, profiles |
| [Billing](domains/billing/) | Payments Team | Subscriptions, payments |
| [Notifications](domains/notifications/) | Platform Team | Email, push, in-app |

## Cross-Cutting
- [Integrations](integrations/) — Third-party services
- [Platform](platform/) — Shared infrastructure
- [Development](development/) — Setup and standards
```

---

## Pattern C: Hybrid (Monorepo/Microservices)

**Use when:**
- Monorepo with multiple modules/services
- Microservices architecture
- Team of 20+ people with sub-teams
- Shared infrastructure with independent services

**Characteristics:**
- Combines concern-based and domain-based organization
- Each service/package has its own documentation
- Shared documentation for cross-cutting concerns

### Structure Template

```
wiki/
├── README.md                    # Monorepo overview
├── overview/
│   ├── system-overview.md
│   ├── architecture.md          # System-wide architecture
│   └── technology-stack.md
├── services/
│   ├── api-gateway/
│   │   ├── README.md
│   │   ├── routing.md
│   │   └── authentication.md
│   ├── user-service/
│   │   ├── README.md
│   │   ├── api.md
│   │   └── data-model.md
│   ├── order-service/
│   │   ├── README.md
│   │   ├── api.md
│   │   └── workflows.md
│   └── notification-service/
│       ├── README.md
│       └── channels.md
├── packages/
│   ├── shared-ui/
│   │   └── README.md
│   ├── common-utils/
│   │   └── README.md
│   └── api-client/
│       └── README.md
├── infrastructure/
│   ├── kubernetes.md
│   ├── terraform.md
│   └── ci-cd.md
└── development/
    ├── getting-started.md
    ├── local-development.md
    └── service-template.md
```

### Navigation Pattern

```markdown
# Monorepo Wiki

## Services
| Service | Team | Port | Description |
|---------|------|------|-------------|
| [API Gateway](services/api-gateway/) | Platform | 3000 | Routing, auth |
| [User Service](services/user-service/) | Identity | 3001 | User management |
| [Order Service](services/order-service/) | Commerce | 3002 | Order processing |
| [Notification Service](services/notification-service/) | Platform | 3003 | Messaging |

## Shared Packages
| Package | Description |
|---------|-------------|
| [shared-ui](packages/shared-ui/) | React components |
| [common-utils](packages/common-utils/) | Utility functions |
| [api-client](packages/api-client/) | Generated API client |

## Platform
- [Infrastructure](infrastructure/) — Kubernetes, Terraform
- [Development](development/) — Setup, contributing
```

---

## Pattern Selection Heuristics

### Decision Tree

```
START
  │
  ├─ Is it a monorepo with multiple services/packages?
  │   ├─ YES → Pattern C (Hybrid)
  │   └─ NO ↓
  │
  ├─ Are there 3+ distinct business domains?
  │   ├─ YES → Pattern B (Vertical)
  │   └─ NO ↓
  │
  └─ Default → Pattern A (Horizontal)
```

### Signals for Each Pattern

**Pattern A signals:**
- Single `package.json` or `requirements.txt`
- `/src` with `/components`, `/services`, `/models` structure
- One deployment unit
- Small team (< 15 people)

**Pattern B signals:**
- Multiple feature directories (`/features/*` or `/domains/*`)
- Different teams own different areas
- Clear bounded contexts
- Multiple integration points

**Pattern C signals:**
- Multiple `package.json` files (monorepo)
- `/services/*` or `/packages/*` structure
- Docker Compose with multiple services
- Kubernetes deployments for multiple apps
- Shared libraries/packages

---

## Naming Conventions

### Folder Names
- Use `kebab-case`: `user-management`, `api-gateway`
- Match codebase naming where possible
- Be descriptive but concise

### File Names
- Use `kebab-case.md`: `system-architecture.md`
- Pattern: `<area>-<topic>.md` or `<domain>-<feature>.md`
- Keep under 30 characters when possible

### Section Organization
Each folder should have:
- `README.md` — Overview and navigation
- Topic files — Specific documentation
- No more than 7-10 files per folder (split if larger)

---

## Page Planning Guidelines

For each page in your sitemap, define:

```markdown
#### Page: `path/to/page.md`

**Purpose:** [1-2 sentences: what this page covers and why it matters]

**Required sections:**
- Section 1: [Description]
- Section 2: [Description]
- Code examples: Yes/No
- Tables: Yes/No

**Required diagrams:**
- [c4-context | c4-container | sequence | deployment | class | integration]

**Relevant source files:**
- `src/path/to/file.ts` — [Why relevant]
- `src/path/**/*.ts` — [Pattern/folder relevance]

**Cross-references:**
- Links to: [related pages]
- Linked from: [pages that reference this]
```

---

## Navigation Rules

### Index/Hub Pages
Every folder needs a `README.md` that:
1. Explains what the section covers
2. Lists all pages with brief descriptions
3. Provides quick links to common tasks

### Breadcrumb Navigation
All pages should include: `Home > [Section] > [Page]`

```markdown
*[Home](../README.md) > [Architecture](./README.md) > System Architecture*

# System Architecture
...
```

### Cross-References
- Always use relative paths: `[text](../path/to/page.md)`
- Verify links exist in sitemap
- Make references bidirectional where appropriate

### Versioning
Include in front matter:
```yaml
---
title: Page Title
generated_at: 2026-01-22T10:00:00Z
commit: abc123 (if available)
last_updated: 2026-01-22T10:00:00Z
---
```

---

## Scope Guidelines

| Project Size | Recommended Pages | Pattern |
|--------------|-------------------|---------|
| Small (< 10k LOC) | 5-10 pages | Pattern A |
| Medium (10k-100k LOC) | 10-20 pages | Pattern A or B |
| Large (100k+ LOC) | 15-30 pages | Pattern B or C |
| Monorepo | 20-50 pages | Pattern C |

**Warning signs of over-documentation:**
- More than 50 pages
- Pages with < 200 words
- Duplicate content across pages
- Pages that haven't been updated in 6+ months

---

**Version:** 1.0
**Last Updated:** 2026-01-22
