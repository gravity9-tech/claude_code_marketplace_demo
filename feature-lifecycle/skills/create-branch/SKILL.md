---
name: create-branch
description: Creates a Git feature branch from local main with standard naming. Use when the user wants to create a branch, start a feature branch, check out a new branch for a ticket, or begin work on a Jira issue.
allowed-tools: Bash, AskUserQuestion
---

# Create Feature Branch

## Purpose

Create a properly named Git feature branch from the local main branch and switch to it.

## Instructions

### Step 1: Gather Inputs

The user may provide a ticket key, a description, or both. Parse what's available:

- **Ticket key**: e.g., `PROJ-123`, `TEA-42` — an uppercase project prefix, hyphen, and number
- **Short description**: e.g., "add product rating", "fix cart total"

If the ticket key is missing, ask the user with `AskUserQuestion`:
> "What's the Jira ticket key? (e.g., PROJ-123)"

If the description is missing, ask the user with `AskUserQuestion`:
> "Give a short description for the branch (2-4 words, e.g., 'add product rating')"

### Step 2: Build the Branch Name

Format: `feature/<ticket-key>-<short-description>`

Normalize the description:
- Lowercase everything
- Replace spaces with hyphens
- Remove special characters (keep only `a-z`, `0-9`, hyphens)
- Collapse multiple hyphens into one
- Trim leading/trailing hyphens
- Limit to 4 words maximum — truncate if longer

Examples:
- `PROJ-42` + `"Add Product Search Bar"` → `feature/PROJ-42-add-product-search-bar`
- `PROJ-123` + `"Fix the cart total bug!!"` → `feature/PROJ-123-fix-the-cart-total`

### Step 3: Check for Clean Working Tree

Run:
```bash
git status --porcelain
```

If there are uncommitted changes, warn the user:
> "You have uncommitted changes. These will carry over to the new branch. Continue?"

Use `AskUserQuestion` with options "Continue" and "Cancel". If cancelled, stop.

### Step 4: Detect the Default Branch

Determine whether the local repository uses `main` or `master`:

```bash
git rev-parse --verify main 2>/dev/null && echo main || echo master
```

### Step 5: Create the Branch

Create the branch from the **local** default branch (do not fetch or pull from remote):

```bash
git checkout -b feature/<ticket-key>-<description> <default-branch>
```

If the branch name already exists, inform the user and ask if they want to switch to the existing branch or choose a different name.

### Step 6: Confirm

Report the result:

```
Branch created: feature/<ticket-key>-<description>
Based on: <default-branch>
```

## Best Practices

- Keep descriptions to 2-4 words — enough to identify the work, short enough to type
- Use lowercase and hyphens only — no underscores, camelCase, or special characters
- Branch from local main/master to avoid network dependencies and keep the workflow fast
