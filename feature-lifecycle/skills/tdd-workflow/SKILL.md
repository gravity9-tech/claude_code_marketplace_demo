---
name: tdd-workflow
description: Implements features using TDD red-green-refactor cycle. Use when the user wants to use TDD, write tests first, do test-driven development, red-green-refactor, or build a feature with tests.
allowed-tools: Read, Edit, Write, Glob, Grep, Bash
---

# TDD Workflow

## Purpose

Guide feature implementation through strict red-green-refactor cycles, writing a failing test first, making it pass with minimal code, then refactoring.

## Instructions

When the user describes a feature or behavior to implement, follow the TDD cycle below. Repeat the cycle for each discrete behavior until the feature is complete.

### Step 0: Detect the Testing Framework

Before starting, identify the project's test setup:

1. Look at existing test files using Glob to find patterns like `**/*.test.*`, `**/*.spec.*`, `**/test_*.py`
2. Check for config files: `jest.config.*`, `vitest.config.*`, `pytest.ini`, `playwright.config.*`
3. Read one existing test file to match the project's style (imports, assertion patterns, describe/it structure)
4. Determine the test run command from the project's `package.json`, `CLAUDE.md`, or `Makefile`

Announce to the user: the framework detected, the test command you'll use, and which file you'll add tests to (or create).

### Step 1: RED — Write a Failing Test

Write **one** test that describes the next behavior to implement.

**Test writing rules:**
- Test exactly one behavior per test
- Use a descriptive name that states expected behavior (e.g., `test_returns_empty_list_when_no_products_match` not `test_filter`)
- Follow Arrange-Act-Assert:
  - **Arrange**: Set up preconditions and inputs
  - **Act**: Call the function or perform the action under test
  - **Assert**: Verify the expected outcome
- Tests must be independent — no shared mutable state, no reliance on test execution order
- Import or reference the production code path where the implementation will live, even though it doesn't exist yet

**Run the test** using Bash to confirm it fails. Verify the failure is for the right reason:
- Expected: import error, function not found, assertion failed because feature doesn't exist
- Not expected: syntax error in the test, wrong test framework, unrelated crash

If the test fails for the wrong reason, fix the test and re-run before proceeding.

Tell the user: `RED: Test fails — [reason]`

### Step 2: GREEN — Write Minimum Code to Pass

Write the **minimum** production code needed to make the failing test pass.

**Green phase rules:**
- Only write code that is directly required by the failing test
- Hard-coded return values are acceptable if the test only checks one case
- Do not add error handling, validation, or features that no test demands yet
- Do not refactor yet — that's the next step
- Do not write additional tests — stay focused on making this one pass

**Run the test** to confirm it passes.

If it fails, fix the production code (not the test) and re-run. The test from the RED phase defines the contract — the code must meet it.

Tell the user: `GREEN: Test passes`

### Step 3: REFACTOR — Improve While Staying Green

Now improve the code you just wrote (and any related code) without changing behavior.

**Refactor targets:**
- Remove duplication (in both production code and tests)
- Improve variable and function names
- Extract helpers if logic is repeated
- Simplify conditionals
- Improve code structure and readability

**Refactor rules:**
- Only refactor when all tests are green
- Run tests after every change — if anything breaks, undo the last change immediately
- Do not add new behavior during refactoring
- Keep refactoring small and incremental

Tell the user: `REFACTOR: [what you improved]` or `REFACTOR: No changes needed — code is clean`

### Step 4: Repeat or Finish

After completing one cycle, assess progress:

- If the feature has more behaviors to implement, return to Step 1 with the next behavior
- If all described behaviors are covered, run the full test suite to check for regressions
- Present a summary when done

### Handling Multiple Behaviors

When the user describes a feature with multiple aspects, decompose it into a sequence of behaviors to implement one at a time. List them before starting:

```
Feature: [name]
Behaviors to implement:
1. [First behavior — simplest/foundational]
2. [Second behavior — builds on first]
3. [Third behavior — edge case or variation]
```

Start with the simplest or most foundational behavior. Each cycle builds on the last.

## Output Format

For each cycle, clearly label the phase:

```
── Cycle 1: [behavior being implemented] ──

RED: Writing test...
  [show test code]
  Running: [test command]
  Result: FAIL — [failure reason] (expected)

GREEN: Writing implementation...
  [show production code]
  Running: [test command]
  Result: PASS

REFACTOR: [what was improved, or "no changes needed"]
  Running: [test command]
  Result: PASS
```

After all cycles:

```
── Summary ──
Feature: [name]
Cycles: [N]
Tests written: [N]
Files modified: [list]
All tests passing: yes/no
```

## Best Practices

- One test, one behavior — resist the urge to test multiple things at once
- Let the test drive the design — don't plan the implementation before writing the test
- Keep the green phase minimal — perfection comes in refactoring, not in the first pass
- Run tests constantly — after every code change, no exceptions
- If you get stuck in red, the test might be too big — split it into a smaller behavior
- Match the project's existing test style and conventions
