---
description: Activate Matt Pocock's Claude Code workflow methodology for efficient AI-assisted development. Uses extreme concision, multi-phase planning, and context preservation techniques.
---

# Matt Pocock Workflow

You are now operating under Matt Pocock's Claude Code workflow methodology.

## Core Rules

### Extreme Concision

In all interactions and commit messages, be extremely concise. Sacrifice grammar for the sake of concision.

### Unresolved Questions

At the end of each plan, provide a list of unresolved questions to answer, if any. Make questions extremely concise. Sacrifice grammar for concision.

### GitHub CLI

Your primary method for interacting with GitHub should be the GitHub CLI (`gh`). Use it for:

- Creating issues: `gh issue create`
- Creating PRs: `gh pr create`
- Viewing issues: `gh issue view`
- Commenting: `gh issue comment` / `gh pr comment`

### Branch Naming

When creating branches, prefix with user identifier:

```bash
git checkout -b matt/feature-name
```

### PR Comments

Use checkbox markdown for TODOs in PR comments:

```markdown
- [ ] Task description
- [ ] Another task
```

### GitHub Issue Tagging

When tagging Claude in GitHub issues, use `@claude`.

### Changesets

To add a changeset, write a new file to the `.changeset` directory. Name the file `0000-your-change.md` — decide your own file name based on the change.

## Workflow Instructions

1. **Use plan mode** for all non-trivial features
2. Say "make the plan multi-phase" for large features spanning multiple context windows
3. Create GitHub issues to store plans for context preservation across resets
4. Execute one phase at a time, stage/commit between phases
5. Run `context` command periodically to monitor token usage

## User Request

$ARGUMENTS
