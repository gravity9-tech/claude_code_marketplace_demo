---
name: commit-and-merge
description: Commits changes on a feature branch and merges into local main after tests pass. All operations are local — never pushes or pulls. Use when the user wants to commit and merge, finish a feature branch, land a feature, merge to main, or complete a branch.
allowed-tools: Bash, AskUserQuestion
---

# Commit and Merge

## Purpose

Commit all changes on the current feature branch with a conventional commit message, run the test suite, and merge into the local main/master branch with `--no-ff`.

## CRITICAL RULE

**All operations are LOCAL ONLY.** Never run `git push`, `git pull`, `git fetch`, or any command that contacts a remote repository.

## Instructions

### Step 1: Verify Current Branch

Run:
```bash
git branch --show-current
```

Confirm the current branch is a feature branch (not `main` or `master`). If already on main/master, stop and inform the user:
> "You're on the main branch. Switch to a feature branch first."

Extract the **ticket key** from the branch name. Feature branches follow the format `feature/KEY-123-description`. The ticket key is the uppercase prefix with number (e.g., `PROJ-123`).

If no ticket key can be parsed from the branch name, ask the user with `AskUserQuestion`:
> "What's the Jira ticket key for this commit? (e.g., PROJ-123)"

### Step 2: Review Changes

Run:
```bash
git status --porcelain
```

If there are no changes (working tree clean, nothing staged), skip to Step 4 (there may be existing unpushed commits to merge).

Show the user a summary of what will be committed:
- Number of new files
- Number of modified files
- Number of deleted files

### Step 3: Commit Changes

Stage all changes:
```bash
git add -A
```

Generate a short description (under 50 characters) summarizing the changes. Examine the diff to understand what was done:
```bash
git diff --cached --stat
```

Commit with conventional commit format:
```bash
git commit -m "feat(TICKET-KEY): <short description>"
```

Use the appropriate prefix based on the changes:
- `feat` — new feature or functionality
- `fix` — bug fix
- `refactor` — code restructuring without behavior change
- `test` — adding or updating tests only
- `docs` — documentation only
- `chore` — maintenance, config, dependencies

Report: `Committed: feat(KEY-123): <description>`

### Step 4: Run Test Suite

Discover the project's test command by reading `CLAUDE.md`, `package.json`, `Makefile`, or similar project configuration files.

Run the full test suite.

If tests **fail**:
- Report which tests failed
- Stop — do not proceed with merge
- Tell the user: "Tests failed. Fix the failures before merging."

If tests **pass**:
- Report: `All tests passing`
- Proceed to next step

### Step 5: Confirm Review Status

Before merging, ask the user with `AskUserQuestion`:
> "Tests are passing. Has the code review been completed and approved?"

Options: "Yes, merge to main" and "No, stop here"

If the user says no, stop and report:
> "Commit and tests complete. Branch is ready to merge after review."

### Step 6: Detect Default Branch

Determine whether the repository uses `main` or `master`:
```bash
git rev-parse --verify main 2>/dev/null && echo main || echo master
```

### Step 7: Merge into Main

Store the current feature branch name, then switch to the default branch:

```bash
git checkout <default-branch>
```

**Do NOT run `git pull` — all operations are local only.**

Merge with no fast-forward to preserve the feature branch history:
```bash
git merge --no-ff <feature-branch> -m "Merge <feature-branch> into <default-branch>"
```

### Step 8: Handle Result

**If merge succeeds:**
```
Merge complete!
Branch: <feature-branch> → <default-branch>
Commit: feat(KEY-123): <description>
Tests: All passing
```

**If merge conflicts occur:**
1. Report the conflicting files:
   ```bash
   git diff --name-only --diff-filter=U
   ```
2. Abort the merge to leave the repo clean:
   ```bash
   git merge --abort
   ```
3. Switch back to the feature branch:
   ```bash
   git checkout <feature-branch>
   ```
4. Tell the user:
   > "Merge conflicts detected in: [file list]. The merge has been aborted and you're back on your feature branch. Resolve conflicts manually or rebase before retrying."

**Do NOT attempt to auto-resolve conflicts.**

## Best Practices

- All operations are local — never push, pull, or fetch from remote
- Always run tests before merging — never skip this step
- Use `--no-ff` to keep feature branch history visible in the git log
- Extract the ticket key from the branch name to avoid asking the user
- If merge conflicts occur, abort cleanly and report — let the user decide how to resolve
- Never use destructive git operations (force push, reset --hard, etc.)
