---
name: expand-ticket
description: Expands a Jira ticket into DEV and QA subtasks. Use when the user wants to break down a ticket, expand a story, create subtasks, split work into dev and testing tasks, or decompose a Jira issue.
allowed-tools: mcp__atlassian__getJiraIssue, mcp__atlassian__createJiraIssue, mcp__atlassian__getAccessibleAtlassianResources, mcp__atlassian__getJiraProjectIssueTypesMetadata
---

# Expand Jira Ticket

## Purpose

Break down a Jira ticket into [DEV] and [QA] subtasks based on its description and acceptance criteria.

## Instructions

### Step 1: Get the Cloud ID

Call `getAccessibleAtlassianResources` to retrieve the Atlassian Cloud ID.

### Step 2: Fetch the Parent Ticket

Call `getJiraIssue` with the ticket key provided by the user (e.g., "PROJ-42").

Extract from the response:
- **Summary** — what the ticket is about
- **Description** — the full description body
- **Project key** — for creating subtasks in the same project
- **Issue key** — to use as the `parent` when creating subtasks

If the ticket has no description or acceptance criteria, inform the user and stop. Suggest they add a description first or use `/create-ticket` to rewrite it.

### Step 3: Verify Subtask Type Exists

Call `getJiraProjectIssueTypesMetadata` for the project to find the correct subtask issue type name. Look for a type with `subtask: true` (commonly named "Subtask" or "Sub-task"). Use the exact name from the project's configuration.

### Step 4: Analyze and Plan Subtasks

Parse the ticket description to identify:

**For DEV subtasks** — break the implementation into discrete coding units:
- Look at the Summary and Technical Notes for implementation scope
- Each subtask should be a single, focused coding task one developer could pick up
- Think in terms of: data model changes, API endpoints, UI components, integration/wiring, configuration
- Order them logically (dependencies first)

**For QA subtasks** — map acceptance criteria to test tasks:
- Each Given/When/Then scenario in the acceptance criteria should become one QA subtask
- If the description doesn't use BDD format, derive testable scenarios from whatever description exists
- Include the test approach in each QA subtask description

### Step 5: Create DEV Subtasks

For each DEV subtask, call `createJiraIssue` with:
- `cloudId`: from Step 1
- `projectKey`: same as parent ticket
- `issueTypeName`: the subtask type name from Step 3
- `parent`: the parent ticket key (e.g., "PROJ-42")
- `summary`: `[DEV] <concise task description>`
- `description`: formatted as:

```markdown
## Task
[What to implement — 1-2 sentences]

## Details
- [Specific implementation detail]
- [Files or areas likely affected]
- [Any constraints or dependencies on other subtasks]
```

### Step 6: Create QA Subtasks

For each QA subtask, call `createJiraIssue` with:
- `cloudId`: from Step 1
- `projectKey`: same as parent ticket
- `issueTypeName`: the subtask type name from Step 3
- `parent`: the parent ticket key (e.g., "PROJ-42")
- `summary`: `[QA] <what to test>`
- `description`: formatted as:

```markdown
## Scenario
- **Given** [precondition from acceptance criteria]
- **When** [action]
- **Then** [expected outcome]

## Test Approach
- [How to verify — manual steps or automation strategy]
- [Edge cases to cover]
- [Test data requirements if any]
```

### Step 7: Report Results

Display a summary of all created subtasks:

```
Expanded PROJ-42 into N subtasks:

DEV:
  PROJ-43 — [DEV] <summary>
  PROJ-44 — [DEV] <summary>
  PROJ-45 — [DEV] <summary>

QA:
  PROJ-46 — [QA] <summary>
  PROJ-47 — [QA] <summary>
  PROJ-48 — [QA] <summary>
```

## Best Practices

- Keep DEV subtasks small enough for one developer to complete independently
- Every QA subtask should map to a testable scenario — if it can't be verified, rewrite it
- Don't create subtasks for trivial work (e.g., "[DEV] Create branch" or "[QA] Smoke test") — focus on meaningful units
- If the parent ticket is vague, create fewer, broader subtasks rather than guessing at specifics
- Aim for 2-5 DEV subtasks and 2-4 QA subtasks per ticket — more suggests the parent should be split into multiple tickets
