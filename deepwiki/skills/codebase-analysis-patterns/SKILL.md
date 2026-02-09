---
name: codebase-analysis-patterns
description: Comprehensive codebase analysis framework covering 14 technical areas including system overview, architecture, modules, integrations, workflows, and deployment patterns. Use when analyzing a codebase structure for documentation or understanding.
user-invocable: false
---

# Codebase Analysis Patterns

A systematic framework for analyzing codebases across 14 technical areas. This skill provides the analysis structure and signals to look for when examining a repository.

## Technical Areas

### 1. System Overview
**Purpose:** Understand the project at a high level.

**Signals to extract:**
- Project name (from package.json, pyproject.toml, go.mod, etc.)
- Project type: monolith, microservices, fullstack, library, CLI tool
- Purpose/domain: e-commerce, SaaS, developer tool, etc.
- Maturity level: prototype, MVP, production, legacy

**Detection patterns:**
```bash
# Check for project metadata
package.json, pyproject.toml, Cargo.toml, go.mod, pom.xml
README.md (first paragraph often describes purpose)
```

---

### 2. Technology Stack
**Purpose:** Identify languages, frameworks, and tools.

**Signals to extract:**
- Primary languages (by file extension distribution)
- Frameworks (React, Vue, Django, FastAPI, Spring, etc.)
- Databases (PostgreSQL, MongoDB, Redis, etc.)
- Cloud providers (AWS, GCP, Azure indicators)
- API styles (REST, GraphQL, gRPC, WebSocket)

**Detection patterns:**
```bash
# Language detection
*.ts, *.tsx → TypeScript
*.py → Python
*.go → Go
*.rs → Rust
*.java → Java

# Framework detection
next.config.* → Next.js
angular.json → Angular
settings.py + urls.py → Django
main.py + FastAPI import → FastAPI
```

---

### 3. Architecture Pattern
**Purpose:** Identify the structural design.

**Signals to extract:**
- Type: layered, hexagonal, microservices, serverless, event-driven
- Layers: presentation, business logic, data access, infrastructure
- Communication: sync HTTP, async messaging, event bus
- Design patterns: repository, factory, observer, CQRS, etc.

**Detection patterns:**
```
# Layered architecture signals
/controllers or /handlers → presentation layer
/services or /usecases → business logic layer
/repositories or /dal → data access layer

# Microservices signals
Multiple Dockerfiles or docker-compose services
/services/* with independent package files
API gateway configuration
```

---

### 4. Frontend Structure
**Purpose:** Understand client-side organization.

**Signals to extract:**
- Framework: React, Vue, Angular, Svelte, etc.
- State management: Redux, Zustand, Vuex, NgRx, etc.
- Build tool: Vite, Webpack, esbuild, Turbopack
- Entry point: index.html, main.tsx, App.vue
- Component organization: atomic, feature-based, page-based

**Detection patterns:**
```
# React patterns
/components, /hooks, /contexts
useState, useEffect, useContext imports

# State management
/store, /redux, /state directories
createStore, configureStore, create (zustand)
```

---

### 5. Backend Structure
**Purpose:** Understand server-side organization.

**Signals to extract:**
- Framework: Express, FastAPI, Django, Spring Boot, etc.
- API style: REST, GraphQL, gRPC
- Module decomposition: by feature, by layer, by domain
- Entry points: main.py, index.ts, Application.java

**Detection patterns:**
```
# API route patterns
/routes, /api, /endpoints directories
@app.route, @router.get, @GetMapping decorators

# Module patterns
/modules/*/  → feature-based
/domain/*/   → domain-driven
```

---

### 6. Module/Service Inventory
**Purpose:** Catalog major components.

**Signals to extract:**
- Module/service names
- Types: API, worker, scheduler, gateway
- Paths and entry points
- Dependencies (internal and external)
- Public interfaces (exported functions, API endpoints)

**Output format:**
| Module | Type | Path | Dependencies | Interface |
|--------|------|------|--------------|-----------|
| auth | API | /services/auth | db, redis | POST /login, /register |
| worker | Background | /workers/email | queue, smtp | processEmailQueue() |

---

### 7. Data Stores
**Purpose:** Identify persistence mechanisms.

**Signals to extract:**
- Databases: PostgreSQL, MySQL, MongoDB, DynamoDB
- Caches: Redis, Memcached
- Search: Elasticsearch, Algolia
- File storage: S3, GCS, local filesystem
- Purpose of each store

**Detection patterns:**
```
# Database configuration
DATABASE_URL, MONGO_URI environment variables
/migrations, /prisma, /drizzle directories
*.sql files, schema definitions

# Cache patterns
REDIS_URL, redis.createClient()
@Cacheable annotations
```

---

### 8. External Integrations
**Purpose:** Map third-party connections.

**Signals to extract:**
- Service names (Stripe, Twilio, SendGrid, etc.)
- Integration methods (SDK, REST API, webhook)
- Authentication (API keys, OAuth, certificates)
- Failure handling (retries, circuit breakers, fallbacks)

**Detection patterns:**
```
# SDK imports
import stripe, from '@stripe/stripe-js'
import twilio, from 'twilio'

# API client patterns
/integrations, /clients, /external directories
fetch('https://api.stripe.com/...')
```

---

### 9. Key Workflows
**Purpose:** Trace critical user journeys.

**Signals to extract:**
- Workflow names (checkout, onboarding, report generation)
- Actors (user, admin, system, scheduler)
- Steps and sequence
- Services involved
- Error scenarios and handling

**Detection patterns:**
```
# Workflow indicators
/workflows, /flows, /sagas directories
State machines, step functions
Transaction boundaries
```

---

### 10. Deployment Architecture
**Purpose:** Understand infrastructure and deployment.

**Signals to extract:**
- Containerization: Docker, Podman
- Orchestration: Kubernetes, ECS, Docker Compose
- IaC: Terraform, CloudFormation, Pulumi
- Environments: dev, staging, production
- CI/CD: GitHub Actions, GitLab CI, Jenkins

**Detection patterns:**
```
# Container configuration
Dockerfile, docker-compose.yml
.dockerignore

# IaC patterns
/terraform, /infrastructure, /infra directories
*.tf, *.yaml (CloudFormation), Pulumi.*

# CI/CD
.github/workflows/, .gitlab-ci.yml, Jenkinsfile
```

---

### 11. Testing & CI/CD
**Purpose:** Understand quality assurance approach.

**Signals to extract:**
- Test structure: unit, integration, e2e
- Test frameworks: Jest, Pytest, JUnit, Cypress
- Coverage targets and tools
- Linting and formatting: ESLint, Prettier, Black, Ruff
- Deployment process and stages

**Detection patterns:**
```
# Test directories
/tests, /__tests__, /spec, /e2e
*.test.ts, *.spec.py, *_test.go

# CI configuration
.github/workflows/*.yml
test, lint, build, deploy stages
```

---

### 12. Security Architecture
**Purpose:** Identify security mechanisms.

**Signals to extract:**
- Authentication: JWT, OAuth2, SAML, API keys
- Authorization: RBAC, ABAC, policies
- Secrets management: environment variables, Vault, AWS Secrets
- Encryption: at-rest, in-transit, field-level
- Security headers and middleware

**Detection patterns:**
```
# Auth patterns
/auth, /security directories
passport, next-auth, @nestjs/passport imports
JWT verification middleware

# Secrets
.env files (check .gitignore)
secrets/, vault configuration
```

---

### 13. File Structure & Entry Points
**Purpose:** Map the repository organization.

**Signals to extract:**
- Top-level directory purposes
- Critical entry points (main files, CLI entry)
- Configuration files and their roles
- Generated vs source directories

**Output format:**
```
/
├── src/           → Source code
├── tests/         → Test files
├── docs/          → Documentation
├── scripts/       → Build/deploy scripts
├── config/        → Configuration files
└── dist/          → Build output (generated)
```

---

### 14. Other Technical Areas
**Purpose:** Capture anything not covered above.

**Potential areas:**
- Observability: logging, metrics, tracing
- Feature flags and A/B testing
- Internationalization (i18n)
- Accessibility (a11y)
- Performance optimization patterns
- Background job processing
- Real-time features (WebSocket, SSE)

---

## Analysis Process

1. **Scan project structure** — Glob for directories and key files
2. **Detect technology** — Check manifest files (package.json, etc.)
3. **Identify entry points** — Find main files for each component
4. **Map dependencies** — Internal and external dependencies
5. **Extract architecture** — Recognize patterns from code structure
6. **Document modules/services** — Create inventory of major components
7. **Discover integrations** — Find external API calls and webhooks
8. **Map workflows** — Trace major user journeys through code
9. **Validate completeness** — Ensure all 14 areas are examined

## Completeness Scoring

| Coverage | Score | Action |
|----------|-------|--------|
| 12-14 areas populated | 85-100% | Proceed with documentation |
| 9-11 areas populated | 70-84% | Review gaps, may proceed |
| < 9 areas populated | < 70% | Request additional context |

---

**Version:** 1.0
**Last Updated:** 2026-01-22
