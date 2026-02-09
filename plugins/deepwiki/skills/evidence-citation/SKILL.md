---
name: evidence-citation
description: Evidence-backed documentation patterns with source code citations. Provides guidelines for citing source files, line numbers, and code examples in technical documentation. Use when writing documentation that needs to reference actual code.
user-invocable: false
---

# Evidence Citation Patterns

Guidelines for creating evidence-backed technical documentation. Every significant claim should be traceable to actual source code.

## Core Principle

**Never make claims without evidence.** Documentation that isn't grounded in actual code becomes stale and misleading. Always cite your sources.

## Citation Formats

### Single File Reference
Use when pointing to a specific file:

```markdown
The system uses FastAPI for the REST API (see `app/main.py`).
```

### Line Number Reference
Use when pointing to a specific location:

```markdown
Authentication is handled by the `verify_token` function (see `auth/jwt.py:42`).
```

### Line Range Reference
Use when referring to a block of code:

```markdown
The checkout workflow is implemented in `services/checkout.py:15-89`.
```

### Pattern/Glob Reference
Use when referring to multiple files:

```markdown
All API routes follow RESTful conventions (see `routes/**/*.ts`).
```

### Directory Reference
Use when referring to a module or component:

```markdown
User management is handled by the auth module (see `src/auth/`).
```

## Citation Levels

### Level 1: Inline Citation (Minimum)
For quick references where the reader can easily find the code:

```markdown
The system uses PostgreSQL for persistence (see `docker-compose.yml`).
```

### Level 2: Citation with Context (Recommended)
Include brief context about what to look for:

```markdown
Rate limiting is configured at 100 requests per minute (see `middleware/ratelimit.ts:23` — the `RATE_LIMIT` constant).
```

### Level 3: Citation with Code Sample (Best)
Include actual code for critical documentation:

```markdown
The system uses FastAPI for the REST API (see `app/main.py`):

\`\`\`python
from fastapi import FastAPI
app = FastAPI(title="MyAPI", version="1.0.0")
\`\`\`
```

## When to Use Each Level

| Situation | Citation Level | Example |
|-----------|----------------|---------|
| Well-known patterns | Level 1 | "Uses React hooks (see `hooks/`)" |
| Configuration values | Level 2 | "Timeout set to 30s (see `config.ts:15` — `REQUEST_TIMEOUT`)" |
| Critical architecture | Level 3 | Full code sample with file reference |
| Security mechanisms | Level 3 | Full code sample showing implementation |
| Complex algorithms | Level 3 | Full code sample with explanation |

## Evidence Gathering Process

### Step 1: Identify Claims
List all factual statements you're making:
- "The system uses X framework"
- "Authentication works via Y mechanism"
- "Data flows from A to B"

### Step 2: Find Evidence
For each claim, locate the source code that supports it:
```bash
# Search for framework usage
grep -r "from fastapi" --include="*.py"

# Find configuration
grep -r "DATABASE_URL" --include="*.env*"

# Locate specific functions
grep -rn "def authenticate" --include="*.py"
```

### Step 3: Verify Accuracy
Before citing, verify:
- [ ] File exists at the stated path
- [ ] Line numbers are current
- [ ] Code sample matches actual file content
- [ ] Claim accurately reflects what the code does

### Step 4: Add Citations
Insert citations using appropriate level based on importance.

## Common Citation Patterns

### Architecture Claims
```markdown
The application follows a layered architecture:
- **Presentation layer**: API routes (see `routes/`)
- **Business logic**: Service classes (see `services/`)
- **Data access**: Repository pattern (see `repositories/`)
```

### Technology Claims
```markdown
The frontend is built with React 18 and TypeScript (see `package.json:12-15`):

\`\`\`json
{
  "dependencies": {
    "react": "^18.2.0",
    "typescript": "^5.0.0"
  }
}
\`\`\`
```

### Configuration Claims
```markdown
The API rate limits requests to 1000/hour per IP (see `config/ratelimit.ts:8`):

\`\`\`typescript
export const RATE_LIMIT = {
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 1000
};
\`\`\`
```

### Workflow Claims
```markdown
The checkout process follows these steps (see `services/checkout.ts:45-120`):
1. Validate cart items (`validateCart()` at line 48)
2. Calculate totals (`calculateTotals()` at line 62)
3. Process payment (`processPayment()` at line 78)
4. Create order (`createOrder()` at line 95)
```

## Anti-Patterns to Avoid

### Vague References
```markdown
# Bad
The system handles authentication.

# Good
Authentication uses JWT tokens validated by middleware (see `middleware/auth.ts:15-30`).
```

### Outdated Line Numbers
```markdown
# Bad (if code has changed)
See `auth.ts:42` for the login function.

# Better (more resilient)
See the `handleLogin` function in `auth.ts`.
```

### Missing Critical Evidence
```markdown
# Bad
The API is secure.

# Good
The API implements security measures including:
- JWT authentication (see `middleware/auth.ts`)
- Rate limiting (see `middleware/ratelimit.ts`)
- Input validation (see `validators/`)
- HTTPS enforcement (see `server.ts:12`)
```

### Invented Code Samples
```markdown
# Bad (making up code)
\`\`\`python
# This is how it probably works
def authenticate(user):
    return check_password(user)
\`\`\`

# Good (actual code from repo)
\`\`\`python
# From auth/service.py:23-28
def authenticate(credentials: LoginCredentials) -> AuthResult:
    user = self.user_repo.find_by_email(credentials.email)
    if not user or not verify_password(credentials.password, user.password_hash):
        raise InvalidCredentialsError()
    return AuthResult(user=user, token=self.create_token(user))
\`\`\`
```

## Quality Checklist

Before finalizing documentation, verify:

- [ ] Every major claim has a citation
- [ ] File paths are correct and exist
- [ ] Line numbers are current (if used)
- [ ] Code samples are copied from actual files (not invented)
- [ ] Citations use appropriate level for the claim's importance
- [ ] Cross-references to other documentation pages are valid

---

**Version:** 1.0
**Last Updated:** 2026-01-22
