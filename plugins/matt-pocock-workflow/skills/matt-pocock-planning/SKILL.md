---
name: matt-pocock-planning
description: Expert guidance on Matt Pocock's Claude Code workflow for AI-assisted development. Use when planning features, breaking down work into phases, preserving context across sessions, or optimizing Claude Code interactions. Covers multi-phase planning, GitHub-based context preservation, and efficient prompting techniques.
---

# Matt Pocock's Claude Code Workflow

A methodology for efficient AI-assisted development with Claude Code, focused on planning, context management, and extreme concision.

## Core Principles

### 1. Extreme Concision

**Rule**: Be extremely concise in all interactions. Sacrifice grammar for brevity.

```
❌ "Please create a new component that will display user information"
✅ "Create user info component"

❌ "I would like you to refactor this function to be more efficient"
✅ "Refactor for efficiency"
```

**Commit messages**:

```
❌ "Added a new feature that allows users to filter results"
✅ "Add result filtering"
```

### 2. Unresolved Questions

At end of every plan, list unresolved questions. Keep them concise.

```markdown
## Unresolved Questions

- Auth: session or JWT?
- Cache: Redis or in-memory?
- Rate limit: per-user or global?
```

### 3. GitHub CLI First

Use `gh` CLI as primary GitHub interface:

```bash
# Create issue with plan
gh issue create --title "Feature: user dashboard" --body-file plan.md

# Create PR
gh pr create --title "Add user dashboard" --body "Closes #123"

# View issue
gh issue view 123

# Add comment
gh issue comment 123 --body "Phase 1 complete"
```

### 4. Branch Naming

Prefix branches with user identifier:

```bash
git checkout -b matt/user-dashboard
git checkout -b matt/fix-auth-bug
git checkout -b matt/refactor-api
```

### 5. PR TODO Format

Use checkbox markdown in PR comments:

```markdown
- [ ] Add unit tests
- [ ] Update documentation
- [ ] Handle edge case for empty state
```

### 6. GitHub Issue Tagging

When tagging Claude in GitHub issues, use `@claude`.

### 7. Changesets

To add a changeset, write a new file to the `.changeset` directory:

```bash
# File: .changeset/0000-add-user-dashboard.md
---
"package-name": minor
---

Add user dashboard with profile editing
```

Name the file `0000-your-change.md` — decide your own file name based on the change.

## Multi-Phase Planning

### When to Use Multi-Phase

Use for features that:

- Require more than ~30 min of work
- Touch multiple files/systems
- Risk hitting context limits
- Need checkpoint commits

### Creating a Multi-Phase Plan

Prompt Claude with:

```
Plan this feature and make the plan multi-phase
```

### Phase Structure

```markdown
## Phase 1: Foundation

- Setup base types
- Create schema
- Add migrations

Checkpoint: Schema and types ready

## Phase 2: Core Logic

- Implement service layer
- Add validation
- Write queries

Checkpoint: Business logic complete

## Phase 3: API Layer

- Create endpoints
- Add middleware
- Handle errors

Checkpoint: API functional

## Phase 4: UI

- Build components
- Wire up forms
- Add loading states

Checkpoint: Feature complete
```

### Phase Execution

1. Execute one phase at a time
2. Stage and commit after each phase
3. Run `context` to check token usage
4. If context high, start new conversation with phase reference

```bash
# After completing phase
git add -A
git commit -m "Phase 1: foundation complete"

# Check context
context

# If high, new conversation:
"Continue from Phase 2 of issue #123"
```

## Context Preservation

### The Problem

Claude Code has finite context. Long sessions lose early information. Context resets lose all progress.

### The Solution: GitHub Issues as Memory

Store plans in GitHub issues for persistence:

```bash
# Create issue with plan
gh issue create \
  --title "Feature: User Dashboard" \
  --body "## Plan

### Phase 1: Database
- Add users table
- Create indexes
- Seed data

### Phase 2: API
- GET /users endpoint
- POST /users endpoint
- Validation

### Phase 3: UI
- User list component
- User form
- Error handling

## Unresolved Questions
- Pagination: offset or cursor?"
```

### Resuming After Reset

After context reset or new session:

```
Continue implementation of issue #123, starting at Phase 2
```

Claude will:

1. Read issue via `gh issue view 123`
2. Understand full plan
3. Continue from specified phase

### Updating Progress

Comment on issue as phases complete:

```bash
gh issue comment 123 --body "Phase 1 complete. Moving to Phase 2."
```

## Context Monitoring

### Check Token Usage

Run periodically:

```
context
```

### When to Reset

Consider new conversation when:

- Context > 70% used
- Responses getting slower
- Claude forgetting earlier context

### Clean Reset

Before resetting:

1. Commit all changes
2. Update GitHub issue with progress
3. Note current phase

After reset:

```
Continue from [Phase X] of issue #[number]
```

## Practical Examples

### Example 1: Small Feature

```
User: Add email validation to signup form

Claude: [implements directly, no plan needed]
```

### Example 2: Medium Feature

```
User: Add user profile page with edit functionality

Claude: [creates single-phase plan]

## Plan
1. Add profile route
2. Create ProfileForm component
3. Add update-profile action
4. Wire up form submission

## Unresolved Questions
- Include avatar upload?
```

### Example 3: Large Feature

```
User: Implement full notification system. Make plan multi-phase.

Claude: [creates multi-phase plan]

## Phase 1: Database
- notifications table
- user_notification_settings table
- indexes

## Phase 2: Core
- NotificationService
- Queue integration
- Templates

## Phase 3: Delivery
- Email sender
- Push notifications
- In-app notifications

## Phase 4: API
- GET /notifications
- POST /notifications/read
- Settings endpoints

## Phase 5: UI
- NotificationBell component
- NotificationList
- Settings page

## Unresolved Questions
- Real-time: WebSocket or polling?
- Batch digest emails?
- Notification categories?
```

## Best Practices

1. **Plan before code** — Always use plan mode for non-trivial features
2. **Commit often** — Stage/commit between phases
3. **Monitor context** — Check token usage regularly
4. **Use GitHub** — Store plans in issues for persistence
5. **Be concise** — Brevity saves tokens and time
6. **List questions** — Surface unknowns early
7. **Phase large work** — Break into context-window-sized chunks
8. **Clean checkpoints** — Each phase ends with working state

## Quick Reference

| Action              | Command/Pattern                          |
| ------------------- | ---------------------------------------- |
| Create branch       | `git checkout -b username/feature`       |
| Multi-phase plan    | "make the plan multi-phase"              |
| Store plan          | `gh issue create --body-file plan.md`    |
| Check context       | `context`                                |
| Resume after reset  | "Continue from Phase X of issue #Y"      |
| Mark phase complete | `gh issue comment N --body "Phase done"` |
| PR TODOs            | `- [ ] Task description`                 |
