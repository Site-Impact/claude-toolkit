---
name: quick-commit
description: Quick commit with conventional commit format. Auto-generates commit message from changes, supports type prefixes (feat, fix, chore, etc.). Use for fast commits without manual message writing.
---

# Quick Commit

This skill creates commits with proper conventional commit format, auto-generating messages from the changes.

## Prerequisites

- Git repository with changes to commit
- Changes should be related (atomic commits)

## Conventional Commit Format

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add OAuth2 login` |
| `fix` | Bug fix | `fix(api): handle null response` |
| `docs` | Documentation | `docs(readme): update setup steps` |
| `style` | Formatting | `style: fix indentation` |
| `refactor` | Code restructure | `refactor(db): simplify queries` |
| `test` | Adding tests | `test(auth): add login tests` |
| `chore` | Maintenance | `chore(deps): update packages` |
| `perf` | Performance | `perf(query): add index` |
| `ci` | CI/CD changes | `ci: add GitHub Actions` |

### Scope

Optional, indicates affected area:
- Service name: `auth`, `worker`, `frontend`
- Feature area: `api`, `db`, `ui`
- Issue key: `WRV-123`

## Workflow

### 1. Check Current State

```bash
git status
git diff --stat
```

### 2. Analyze Changes

Look at the diff to understand:
- What type of change (feature, fix, etc.)
- What area is affected (scope)
- What the change accomplishes

### 3. Determine Commit Type

Based on changes:
- New files with functionality → `feat`
- Modified files fixing issues → `fix`
- Test files only → `test`
- Config/build files → `chore`
- README/docs only → `docs`

### 4. Extract Scope

From file paths:
- `services/worker/*` → `worker`
- `frontend/src/*` → `frontend`
- `backend/api/*` → `api`
- Multiple areas → omit scope or use broader term

### 5. Generate Description

Summarize the change in imperative mood:
- "add user authentication"
- "fix null pointer in parser"
- "update dependencies"

### 6. Stage and Commit

```bash
git add <files>
git commit -m "$(cat <<'EOF'
type(scope): description

- Detail 1
- Detail 2
)"
```

## Example Invocations

- `/quick-commit` - Auto-detect type and generate message
- `/quick-commit feat` - Force feature type
- `/quick-commit fix auth` - Fix with auth scope
- `/quick-commit "custom message"` - Use provided message

## Arguments

- No args: Auto-detect everything
- `<type>`: Force commit type (feat, fix, etc.)
- `<type> <scope>`: Force type and scope
- `"<message>"`: Use exact message provided
- `--all` or `-a`: Stage all changes first
- `--amend`: Amend previous commit (with safety checks)

## Auto-Detection Rules

### Type Detection

| Changed Files | Likely Type |
|--------------|-------------|
| New feature code | `feat` |
| Existing code modified | `fix` or `refactor` |
| `*test*` files only | `test` |
| `*.md` files only | `docs` |
| `package.json`, `pyproject.toml` | `chore` |
| `.github/*`, `Dockerfile` | `ci` or `chore` |

### Scope Detection

| Path Pattern | Scope |
|--------------|-------|
| `services/<name>/*` | `<name>` |
| `frontend/*` | `frontend` |
| `backend/*` | `backend` |
| `shared/*` | `shared` |
| Mixed paths | Most common or omit |

## Safety Checks

Before committing:
- Warn if committing to main/master directly
- Warn if changes include potential secrets
- Warn if committing large binary files
- Confirm if staging all with `--all`

## Message Quality Rules

Good messages:
- Start with lowercase (after type)
- No period at end
- Imperative mood ("add" not "added")
- Under 72 characters for first line
- Explain "what" and "why", not "how"

## Notes

- Always review the generated message before confirming
- For complex changes, consider breaking into multiple commits
- Use `--amend` carefully (never on pushed commits)
- Include issue key in scope when applicable: `feat(WRV-123)`