---
name: finish-story
description: Automates finishing work on a Jira story. Updates the story with implementation notes, commits changes, pushes branch, opens PR against epic/main, monitors CI pipeline, fixes issues if needed, and merges when green. Use when completing work on a Jira story.
---

# Finishing a Jira Story

This skill automates the workflow for completing work on a Jira story, ensuring code quality and proper documentation.

## Prerequisites

- Atlassian MCP server must be configured
- Git repository must be initialized with changes to commit
- GitHub CLI (`gh`) must be authenticated
- User must have access to the Jira project and GitHub repo

## Workflow Steps

When finishing work on a Jira story, follow these steps in order:

### 1. Identify the Story

- Parse the current branch name to extract the Jira issue key (e.g., `WOC-75-...` → `WOC-75`)
- If not clear, ask the user for the issue key
- Fetch the Jira issue to confirm details

### 2. Review Changes

Run `git status` and `git diff --stat` to understand what's being committed:
- List modified files
- Identify new files
- Confirm with user if the changes look complete

### 3. Update Jira Story

Use `mcp__atlassian__addCommentToJiraIssue` to document:
- Summary of implementation
- Files changed
- Any technical decisions made
- Follow-up items or related tickets created

### 4. Commit Changes

Create a conventional commit with the following format:
```bash
git add <specific-files>
git commit -m "$(cat <<'EOF'
<type>(<scope>): <description>

- Bullet point summary of changes
- Another change
EOF
)"
```

**Commit types:** `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `ci`

If pre-commit hooks fail:
1. Review the errors
2. Fix the issues (usually formatting)
3. Re-stage and create a NEW commit (do not amend)

### 5. Push Branch

```bash
git push -u origin <branch-name>
```

### 6. Open Pull Request

Determine the base branch:
- Confirm with user which base branch before proceeding.

Create PR using GitHub CLI:
```bash
gh pr create --base <base-branch> --title "<commit-title>" --body "$(cat <<'EOF'
## Summary
<bullet points of changes>

## Changes
| File | Change |
|------|--------|
| `path/to/file` | Description |

## Test plan
- [ ] Test case 1
- [ ] Test case 2

Closes <ISSUE-KEY>
EOF
)"
```

### 7. Monitor CI Pipeline

Watch the GitHub Actions checks:
```bash
gh pr checks <PR-number> --watch
```

**If checks fail:**
1. Identify the failing check
2. Read the error logs: `gh run view <run-id> --log-failed`
3. Fix the issue locally
4. Commit the fix (new commit, not amend)
5. Push and repeat monitoring

**Continue until all checks pass.**

### 8. Merge PR

Once all checks are green:
```bash
gh pr merge <PR-number> --squash --delete-branch
```

### 9. Transition Jira to Done

- Use `mcp__atlassian__getTransitionsForJiraIssue` to get available transitions
- Use `mcp__atlassian__transitionJiraIssue` to move the story to "Code Review"
- The cloudId for this project is: ``

## Example Invocation

User: "Finish story" or "/finish-story"

Claude should:
1. Detect current branch and extract issue key
2. Show changes summary and confirm
3. Add implementation comment to Jira
4. Commit with conventional format
5. Push branch
6. Create PR against appropriate base
7. Monitor CI until green (fixing issues as needed)
8. Merge PR
9. Transition Jira to Done

## Error Handling

### Pre-commit Hook Failures
- Usually formatting (black, prettier)
- Re-stage modified files and create new commit

### CI Failures
- Read logs carefully
- Common issues: lint errors, type errors, test failures
- Fix locally, commit, push, re-monitor

### Merge Conflicts
- Alert the user
- Do not auto-resolve - require user guidance

## Best Practices

- Always use squash merge for cleaner history
- Delete branch after merge
- Ensure Jira is updated before marking Done
- Create follow-up tickets for out-of-scope items discovered during implementation