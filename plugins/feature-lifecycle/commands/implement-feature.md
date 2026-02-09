---
description: Run full feature lifecycle — plan in Jira, implement with TDD, review, and merge
allowed-tools: Task, AskUserQuestion, mcp__atlassian__getAccessibleAtlassianResources, mcp__atlassian__getVisibleJiraProjects
argument-hint: <feature description> [JIRA_PROJECT_KEY]
---

# Implement Feature

Orchestrate the complete feature implementation lifecycle: plan in Jira, implement with TDD, review code, and merge — all in one command.

## Arguments

- `$ARGUMENTS` — A feature description optionally followed by a Jira project key. Example: `Add product reviews with star ratings for PROJ`

## Workflow

Execute these phases sequentially. Each phase depends on the output of the previous one.

### Phase 0: PROJECT CHECK

Determine the Jira project key before proceeding:

1. Parse `$ARGUMENTS` to check if the last word looks like a Jira project key (all uppercase letters, 2-10 chars, e.g., `PROJ`, `TEAM`)
2. **If a project key is found**: extract it and use the remaining text as the feature description
3. **If NO project key is found**:
   - Call `mcp__atlassian__getAccessibleAtlassianResources` to get the Cloud ID
   - Call `mcp__atlassian__getVisibleJiraProjects` to list available projects
   - Use `AskUserQuestion` to ask the user which project to use, showing the available projects as options
   - Use the selected project key
4. Once confirmed, proceed with:
   - **Feature description**: the text portion of `$ARGUMENTS`
   - **Project key**: the confirmed Jira project key

### Phase 1: PLAN

Spawn the **planner** agent using the Task tool:

```
Task(
  subagent_type: "planner",
  prompt: "<feature description> for <PROJECT_KEY>",
  description: "Plan feature in Jira"
)
```

The planner will:
- Create a Jira ticket with BDD acceptance criteria
- Expand it into [DEV] and [QA] subtasks
- Explore the codebase and produce an implementation plan
- Return a ticket summary table with the parent key (e.g., `PROJ-42`)

**Capture from output:**
- The parent Jira ticket key (e.g., `PROJ-42`)
- The ticket summary table
- The full implementation plan text

**Error handling:** If the planner fails or does not produce a ticket key, STOP and report the error to the user. Do not proceed to Phase 2.

### Phase 2: IMPLEMENT

Spawn the **tdd-implementer** agent using the Task tool:

```
Task(
  subagent_type: "tdd-implementer",
  prompt: "<PARENT_TICKET_KEY from Phase 1>",
  description: "Implement feature with TDD"
)
```

The implementer will:
- Fetch subtasks from Jira for the parent ticket
- Create a single feature branch (`feature/KEY-100-description`)
- Implement each [DEV] subtask using TDD red-green-refactor
- Implement each [QA] subtask as automated tests
- Transition each subtask through the Jira board (To Do -> In Progress -> Done)
- Run the full test suite at the end

**Capture from output:**
- The feature branch name
- List of files created and modified
- List of tests written
- Test results (pass/fail counts)
- Any issues or blockers encountered

**Error handling:** If the implementer encounters issues, DO NOT stop. Capture whatever output is available and proceed to Phase 3 so the reviewer can document all issues.

### Phase 3: REVIEW

Spawn the **code-reviewer** agent using the Task tool:

```
Task(
  subagent_type: "code-reviewer",
  prompt: "Review parent ticket <PARENT_TICKET_KEY from Phase 1>. Implementation summary: <IMPLEMENTATION_SUMMARY from Phase 2>",
  description: "Review and merge code"
)
```

The reviewer will:
- Fetch the parent ticket and subtasks from Jira
- Review code for plan compliance, TDD discipline, code quality, and potential issues
- Issue a verdict: PASS / PASS WITH NOTES / FAIL
- If PASS or PASS WITH NOTES: commit and merge to local main
- If FAIL: report critical issues without merging
- Add a review comment to the parent Jira ticket

**Capture from output:**
- The review verdict
- Issues found (critical / warning / note)
- Merge status (merged / not merged / conflict)

### Phase 4: REPORT

After all three phases complete, output a final summary to the user:

```markdown
# Feature Implementation Complete

## Jira Ticket
- **Key**: [PARENT_KEY]
- **Summary**: [Ticket summary from planner]

## Ticket Summary

| Key | Type | Summary | Status |
|-----|------|---------|--------|
| [from planner output — parent + all subtasks with final statuses] |

## Implementation

- **Branch**: `feature/KEY-100-description`
- **Files created**: [list from implementer]
- **Files modified**: [list from implementer]
- **Tests written**: [count] backend, [count] frontend, [count] E2E

## Test Results
- Backend: X passed
- Frontend: X passed
- E2E: X passed

## Review Verdict: [PASS / PASS WITH NOTES / FAIL]

[If PASS WITH NOTES or FAIL, list the issues here]

## Merge Status: [Merged to main / Not merged — critical issues found]
```

## Error Handling

- **Planner fails**: Stop immediately. Report the error and suggest the user fix the issue and retry.
- **Implementer fails partially**: Continue to review phase. The reviewer will document what was and wasn't completed.
- **Reviewer finds critical issues**: Report them clearly. The code stays on the feature branch unmerged.
- **Merge conflict**: Report the conflict. The code stays on the feature branch unmerged.

## Constraints

- All phases run sequentially — never in parallel
- All git operations are local only — nothing is pushed to remote
- The planner MUST produce a Jira ticket key before implementation begins
- The implementer uses ONE branch for all subtasks
- The reviewer is the only phase that can commit and merge
