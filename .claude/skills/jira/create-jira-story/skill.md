---
name: create-jira-story
description: Creates a well-structured Jira story from requirements or discussion. Generates summary with user story format, dev notes with technical details, and QA-friendly acceptance criteria. Uses reference of other jira tickets if available. Use when you need to document new work as a Jira ticket.
---

# Creating a Jira Story

This skill helps create well-structured Jira stories that follow team conventions and provide clear context for developers and QA.

## Prerequisites

- Atlassian MCP server must be configured
- User must have access to the Jira project
- The cloudId for this project is: ``

## Story Format

All stories must follow this structure:

```markdown
## Summary
A paragraph (max 5 sentences) explaining the goal of the issue on a high level. Use user story template (As a [user] I want [action/goal] so that [benefit/value]).

## Dev Notes
- Bulleted list of technical Requirements, Code References, any information a dev would use to estimate or work on the issue

## Acceptance Criteria
1. Scenarios that must be verified by QA. If something cannot be verified by a QA via the UI make a note what that is and mention that they will need a developer to help.
```

**Important**: Do not separate sections with horizontal rules.

## Workflow Steps

### 1. Gather Requirements

- If there are uncommited or staged changes, check by running:
```bash
 git status
```
- If there are uncommited or staged changes, ask the user if they want to commit them.
- Then ask the user if the staged changes can be used as the basis for the new story, this should give the user an option to choose yes or *write something* to provider further context.


If the work for this ticket hasn't been started meaning there are no changes in that are staged or uncommited, ask the user for:
- What problem needs to be solved or feature added?
- Who is the target user?
- What is the expected behavior?
- Are there any technical constraints or preferences?
- Does this involve UI changes?

### 2. Draft the Story in Scratch Folder

Before creating in Jira, write the story to a markdown file in the `scratch` folder:
- Filename: `{issue-key-prefix}-{short-description}.md` (e.g., `AMP-story-campaign-export.md`)
- This allows the user to review and refine before submission

### 3. Review with User

Present the drafted story and ask:
- Does the summary capture the intent?
- Are the dev notes technically accurate?
- Are the acceptance criteria testable by QA?

### 4. Get Project Context

Use `mcp__atlassian__getVisibleJiraProjects` to:
- Confirm the target project
- Get available issue types

Use `mcp__atlassian__getJiraProjectIssueTypesMetadata` to:
- Get required fields for the issue type

### 5. Create the Jira Issue

Use `mcp__atlassian__createJiraIssue` with:
- `projectKey`: The project key (e.g., "AMP")
- `issueTypeName`: Usually "Story" or "Task"
- `summary`: A concise title (not the full Summary section)
- `description`: The full story content in markdown format

### 6. Confirm Creation

After successful creation:
- Display the issue key and link
- Ask if the user wants to start working on it (invoke start-story skill)

## Example Invocation

User: "Create a story for adding export functionality to the campaign table"

Claude should:
1. Ask clarifying questions about requirements
2. Draft the story in `scratch/AMP-story-campaign-export.md`
3. Present draft for review
4. After approval, create in Jira
5. Return issue key and offer to start the story

## Writing Guidelines

### Summary Section
- Start with user story format: "As a [user], I want [action] so that [benefit]"
- Keep to 5 sentences maximum
- Focus on the "why" not the "how"
- Be specific about the user role and their goal

### Dev Notes Section
- Include relevant file paths and code references
- Mention existing patterns to follow
- Note any dependencies or blockers
- Reference related issues if applicable
- Include API endpoints affected
- Mention state management considerations

### Acceptance Criteria Section
- Scenarios that must be verified by QA. If something cannot be verified by a QA via the UI make a note what that is and mention that they will need a developer to help.

## Example Story

```markdown
## Summary
As a campaign manager, I want to export campaign data to CSV so that I can analyze performance metrics in external tools like Excel. Currently there is no way to extract campaign data from the dashboard, requiring manual data entry for reporting purposes.

## Dev Notes
- Add export button to `src/components/Dashboard/dashboard-client-page.tsx`
- Use existing `useExportDataModalStore` pattern from `src/stores/`
- CSV generation should use the `papaparse` library (already installed)
- Include columns: Campaign Name, Status, Budget, Spend, Clicks, Conversions
- Respect current table filters when exporting
- Reference: Similar export exists in Campaign Monitor at `src/components/CampaignMonitor/ExportModal/`

## Acceptance Criteria
1. Export button appears in the dashboard toolbar next to the filter controls
2. Clicking export opens a modal with column selection checkboxes
3. User can select/deselect columns to include in export
4. Export respects current filter state (only exports visible campaigns)
5. Downloaded file is named `campaigns-export-{date}.csv`
6. Export works with 1000+ campaigns without browser freezing (Requires developer assistance to verify performance)
7. Error toast appears if export fails
```