---
name: start-story
description: Automates starting work on a Jira story following team best practices. Fetches story details, transitions to In Progress, creates a feature branch, enters plan mode to design implementation, and documents the plan in Jira. Use when beginning work on a new Jira story or user story.
---

# Starting a Jira Story

This skill automates the workflow for starting work on a new Jira story, ensuring consistent practices across the team.

## Prerequisites

- Atlassian MCP server must be configured
- Git repository must be initialized
- User must have access to the Jira project

## Workflow Steps

When starting work on a Jira story, follow these steps in order:

### 1. Get Story Details

Fetch the Jira issue using `mcp__atlassian__getJiraIssue` to retrieve:
- Summary/title
- Description
- Acceptance criteria
- Current status
- Subtasks (if any)

Confirm with the user that this is the correct story before proceeding.
### 2. Transition to In Progress

- Use `mcp__atlassian__getTransitionsForJiraIssue` to get available transitions
- Use `mcp__atlassian__transitionJiraIssue` to move the story to "In Progress"
- The cloudId for this project is: ``

### 3. Create Feature Branch

- Ensure you're on the main branch and it's up to date:
  ```bash
  git checkout main && git pull origin main
  ```
- Create a feature branch using the pattern:
  ```bash
  git checkout -b feature/{ISSUE-KEY}-{short-description}
  ```
- Example: `feature/WRV-21-websocket-infrastructure`
- Use lowercase letters and hyphens in the description

### 4. Enter Plan Mode

- Use the `EnterPlanMode` tool to design the implementation approach
- The plan should include:
  - Overview of what will be implemented
  - Data flow diagrams (if applicable)
  - Implementation phases/steps
  - Files to create and modify
  - Key code structures
  - Testing strategy
  - Definition of done checklist

### 5. Document Plan in Jira

After the plan is approved:
- Use `mcp__atlassian__addCommentToJiraIssue` to add the implementation plan as a comment
- Format the plan in Markdown for readability

### 6. Create Subtasks (if needed)

If the story is complex:
- Break it down into subtasks using `mcp__atlassian__createJiraIssue`
- Each subtask should be independently testable
- Add subtasks to the todo list for tracking

### 7. Set Up Todo List

Use the `TodoWrite` tool to track:
- Each subtask or implementation step
- Mark the first task as `in_progress`

## Example Invocation

User: "Start story WRV-24"

Claude should:
1. Fetch WRV-24 details and display summary
2. Ask for confirmation
3. Transition to In Progress
4. Create branch `feature/WRV-24-test-run-controls`
5. Enter plan mode
6. After plan approval, add plan to Jira
7. Create subtasks if applicable
8. Initialize todo list

## Jira Best Practices

- Keep story status updated as work progresses
- Add comments for significant milestones
- Link PRs to stories when created
- Transition parent story to Code Review only after PR is merged

## Git Best Practices

- Keep commits atomic and well-described
- Reference Jira issue key in commit messages: `feat(WRV-24): description`
- Push branch before creating PR
- Use squash merge for cleaner history