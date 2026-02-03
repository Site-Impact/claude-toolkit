![Claude Code](https://mintlify.s3.us-west-1.amazonaws.com/anthropic/logo/light.svg)

# Claude Code Toolkit

A shared repository of Claude Code Agents, Skills, and related configurations available to all development teams.

## What's Included

- **Skills** - Reusable skill definitions for common workflows
- **Agents** - Sub-agent configurations for specialized tasks

## Documentation

For more information on how to use and create these resources, see the official Claude Code documentation:

- [Sub-Agents](https://code.claude.com/docs/en/sub-agents) - Learn how to create and configure sub-agents
- [Skills](https://code.claude.com/docs/en/skills) - Learn how to create and use skills

## Usage Guide

To use these skills and agents in your project, copy them to your project's `.claude` directory:

```
your-project/
├── .claude/
│   ├── agents/
│   │   └── (copy agents here)
│   └── skills/
│       └── (copy skills here)
└── ...
```

**Example:** To add the Jira skills to your project:
```bash
# From your project root
mkdir -p .claude/skills/jira
cp -r /path/to/claude-toolkit/.claude/skills/jira/* .claude/skills/jira/
```

Skills and agents placed in your project's `.claude` directory will be available only within that project.

## Jira Skills

This repository includes Jira-related skills for story management. To use these skills, you will need:

1. Jira MCP server configured - See [MCP Setup](https://code.claude.com/docs/en/mcp) for instructions
2. Your Jira project's `cloudId` - To find it, visit: `https://your-domain.atlassian.net/_edge/tenant_info`
