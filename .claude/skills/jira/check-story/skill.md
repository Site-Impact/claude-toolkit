---
name: check-story
description: Checks whether a Jira story's acceptance criteria are met by reviewing the current code changes. Compares branch diff against each criterion and produces a pass/fail checklist. Use before completing a story to verify nothing was missed.
---

# Check Story Completion

Verifies that all acceptance criteria for a Jira story are satisfied by the current code changes on the branch.

## Prerequisites

- Atlassian MCP server must be configured
- On a feature branch with changes (not main/master)
- Branch name should contain the Jira issue key (e.g., `WRV-15-traffic-patterns`)
- The cloudId for this project is: ``

## Workflow

### 1. Identify the Story

- Parse the Jira issue key from the current branch name
  - Branch pattern: `{ISSUE-KEY}-description` or `{ISSUE-KEY}-description`
  - If no issue key is found in the branch name, check if an argument was provided
  - If still not found, ask the user for the issue key
- Fetch the Jira issue using `mcp__atlassian__getJiraIssue` to retrieve:
  - Summary
  - Description (contains acceptance criteria)
  - Status

### 2. Extract Acceptance Criteria

- Parse the acceptance criteria from the issue description
- Acceptance criteria are typically found after an "Acceptance Criteria" heading or as a bulleted list
- Each bullet point or checkbox item is a separate criterion
- If no clear acceptance criteria are found, use the story description and summary to infer what "done" means, and confirm with the user

### 3. Analyze Code Changes

- Get the full diff of the branch against the base branch (usually `main`):
  ```bash
  git diff main...HEAD
  ```
- Get the list of changed/added files:
  ```bash
  git diff main...HEAD --name-status
  ```
- Also check for uncommitted changes:
  ```bash
  git status -s
  ```
- Read relevant changed files to understand what was implemented
- Use Grep/Glob to search for specific implementations if the diff is large

### 4. Evaluate Each Criterion

For each acceptance criterion:
- Search the code changes for evidence that it is satisfied
- Read the relevant files if needed for deeper understanding
- Determine a verdict: **PASS**, **FAIL**, or **PARTIAL**
- Note the specific files/code that satisfy (or fail to satisfy) the criterion

### 5. Report Results

Present a clear checklist to the user:

```
## Story Check: {ISSUE-KEY} — {Summary}

| # | Criterion | Status | Evidence |
|---|-----------|--------|----------|
| 1 | Description of criterion | PASS/FAIL/PARTIAL | Brief evidence or what's missing |
| 2 | ... | ... | ... |

### Overall: READY / NOT READY

{If NOT READY, list what still needs to be done}
{If there are uncommitted changes, note that they exist}
```

### 6. Suggest Next Steps

Based on the results:
- **All PASS**: Suggest running `/complete-story` to commit, PR, and close
- **Some FAIL/PARTIAL**: List the remaining work items clearly
- **Uncommitted changes**: Note that uncommitted work exists on disk

## Arguments

- No arguments: Auto-detect issue key from branch name
- `ISSUE-KEY`: Explicitly specify the issue key (e.g., `WRV-15`)

## Example Invocation

User: `/check-story`

Claude should:
1. Detect `WRV-15` from branch `WRV-15-traffic-patterns`
2. Fetch the story and find 3 acceptance criteria
3. Analyze `git diff main...HEAD` plus any uncommitted changes
4. Report:
   - Criterion 1: PASS — found in `services/worker/app/engine/patterns.py`
   - Criterion 2: PASS — implemented in `executor.py`
   - Criterion 3: PARTIAL — JSON DSL types exist but no docs
5. Suggest what's left or recommend `/complete-story`

## Notes

- This is a read-only check — it does not modify any code, commits, or Jira state
- Always consider both committed and uncommitted changes on the branch
- If the diff is very large, focus on the files most relevant to each criterion rather than reading everything
- Be honest about uncertain verdicts — use PARTIAL when evidence is ambiguous