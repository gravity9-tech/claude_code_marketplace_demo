---
name: create-ticket
description: Creates a Jira ticket from a feature description with BDD acceptance criteria. Use when the user wants to create a ticket, file a bug, add a story, log a task, or write a Jira issue.
allowed-tools: mcp__atlassian__searchJiraIssuesUsingJql, mcp__atlassian__createJiraIssue, mcp__atlassian__getVisibleJiraProjects, mcp__atlassian__getAccessibleAtlassianResources, mcp__atlassian__search, AskUserQuestion
---

# Create Jira Ticket

## Purpose

Create a Jira ticket with BDD-formatted acceptance criteria, duplicate detection, and automatic issue type classification from a natural language feature description.

## Instructions

### Step 1: Get the Cloud ID

Call `getAccessibleAtlassianResources` to retrieve the Atlassian Cloud ID. You need this for all subsequent Jira API calls.

### Step 2: Determine the Jira Project

If the user specified a project key (e.g., "PROJ", "TEA"), use that directly.

If not specified, call `getVisibleJiraProjects` to list available projects, then use `AskUserQuestion` to ask the user which project to use. Present the project keys and names as options.

### Step 3: Classify the Issue Type

Determine the issue type from the user's description:

| Choose | When the description mentions... |
|--------|----------------------------------|
| **Bug** | Something is broken, not working, error, crash, regression, fix needed |
| **Story** | New user-facing feature, user flow, "as a user I want..." |
| **Task** | Technical work, refactoring, setup, configuration, chore, maintenance |

If ambiguous, default to **Story** for feature descriptions or **Task** for technical work.

### Step 4: Check for Duplicates

Search for existing tickets that might cover the same work:

1. Extract 2-3 key terms from the description
2. Call `searchJiraIssuesUsingJql` with a JQL query like:
   ```
   project = PROJ AND summary ~ "key term" AND status != Done ORDER BY created DESC
   ```
3. Also call `search` (Rovo Search) with the feature description for broader matching

If potential duplicates are found:
- Show the user the matching tickets (key, summary, status)
- Ask if they want to proceed with creation or if an existing ticket covers their need
- If the user says an existing ticket matches, stop and return that ticket's key

### Step 5: Write the Description in BDD Format

Structure the ticket description as follows:

```markdown
## Summary
[1-2 sentence summary of what this feature/fix/task accomplishes]

## Acceptance Criteria

**Scenario 1: [Descriptive scenario name]**
- **Given** [initial context or precondition]
- **When** [action the user or system takes]
- **Then** [expected observable outcome]

**Scenario 2: [Descriptive scenario name]**
- **Given** [initial context or precondition]
- **When** [action the user or system takes]
- **Then** [expected observable outcome]

[Add more scenarios as needed — typically 2-4 per ticket]

## Technical Notes
[Only include this section if there are implementation details, constraints, or dependencies worth noting. Omit if not relevant.]
```

Guidelines for writing acceptance criteria:
- Each scenario should be testable and verifiable
- Use concrete values where possible (e.g., "Given a cart with 3 items" not "Given a cart with items")
- Keep scenarios focused on one behavior each
- Write from the user's perspective for Stories, from the system's perspective for Bugs/Tasks
- For Bugs: first scenario should describe the expected correct behavior

### Step 6: Create the Ticket

Call `createJiraIssue` with:
- `cloudId`: from Step 1
- `projectKey`: from Step 2
- `issueTypeName`: from Step 3 ("Bug", "Story", or "Task")
- `summary`: A concise title (under 80 characters, imperative voice)
- `description`: The BDD-formatted description from Step 5

### Step 7: Report the Result

Display the created ticket to the user:

```
Ticket created: [KEY-123] — [Summary]
Type: [Bug/Story/Task]
URL: https://[site].atlassian.net/browse/[KEY-123]
```

## Best Practices

- Keep summaries short and action-oriented (e.g., "Add dark mode toggle to settings page")
- Write 2-4 acceptance criteria scenarios per ticket — enough to be clear, not so many it becomes a spec document
- Only include Technical Notes when there are genuine constraints or dependencies
- When checking for duplicates, search broadly — false negatives are worse than false positives
- If the user provides a very brief description, ask clarifying questions before creating the ticket

## Example

**User**: "Create a ticket for adding a search bar to the homepage that filters products by name"

**Assistant actions**:
1. Get Cloud ID from `getAccessibleAtlassianResources`
2. Ask user which project to use (shows available projects)
3. Classify as **Story** (new user-facing feature)
4. Search for duplicates: `project = PROJ AND summary ~ "search" AND status != Done`
5. No duplicates found — proceed
6. Create ticket with:

   **Summary**: Add product search bar to homepage

   **Description**:
   ```
   ## Summary
   Add a search bar to the homepage that allows users to filter the product grid by name in real time.

   ## Acceptance Criteria

   **Scenario 1: Search filters products by name**
   - **Given** the user is on the homepage with all products visible
   - **When** the user types "green" into the search bar
   - **Then** only products containing "green" in their name are displayed

   **Scenario 2: Empty search shows all products**
   - **Given** the user has typed a search query with filtered results
   - **When** the user clears the search bar
   - **Then** all products are displayed again

   **Scenario 3: No results found**
   - **Given** the user is on the homepage
   - **When** the user types a query that matches no products
   - **Then** a "No products found" message is displayed

   ## Technical Notes
   - Search should be case-insensitive
   - Filter client-side against already-loaded product data for instant results
   ```

7. Return: `Ticket created: PROJ-42 — Add product search bar to homepage`
