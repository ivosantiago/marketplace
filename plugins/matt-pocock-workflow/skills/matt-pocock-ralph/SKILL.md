---
name: matt-pocock-ralph
description: Expert guidance on the Ralph Wiggum approach for AI coding agents. Use when running long-running autonomous agents, designing PRD-driven workflows, or setting up feedback loops for AI coding. Covers task-based loops, progress tracking, and AFK agent orchestration.
---

# The Ralph Wiggum Approach

A devilishly simple technique for running coding agents that outperforms complex agent swarms and orchestrators. Credited to Jeffrey Huntley.

**Source**: [Matt Pocock's Ralph video](https://www.youtube.com/watch?v=_IK18goX4X8)

## Core Concept

Instead of multi-phase plans with explicit ordering, Ralph uses:

1. A **PRD** (Product Requirements Document) with tasks
2. A **for loop** that runs the agent repeatedly
3. A **progress.txt** file for memory across iterations
4. **Git commits** per completed task

The agent picks the highest priority incomplete task, completes it, marks it done, commits, and loops until all tasks pass.

## Why Ralph Works

Traditional multi-phase plans have problems:

- Hard to add tasks mid-sprint
- Must figure out exact ordering/dependencies
- Doesn't reflect how engineers actually work

Real engineers:

1. Look at Kanban board
2. Pick highest priority task
3. Complete it
4. Repeat

Ralph mimics this natural workflow.

## Implementation

### Directory Structure

```
plans/
├── ralph.sh           # Main loop script
├── ralph-once.sh      # Human-in-the-loop variant
├── prd.json           # Task list with passes flags
└── progress.txt       # LLM memory/learnings
```

### ralph.sh

```bash
#!/bin/bash
set -e

if [ -z "$1" ]; then
  echo "Usage: ralph.sh <max_iterations>"
  exit 1
fi

MAX_ITERATIONS=$1

for ((i=1; i<=MAX_ITERATIONS; i++)); do
  echo "=========================================="
  echo "Ralph iteration $i of $MAX_ITERATIONS"
  echo "=========================================="

  OUTPUT=$(claude -p "$(cat plans/prd.json)" "$(cat plans/progress.txt)" "
Steps to complete:
1. Find the highest priority feature to work on and work only on that feature. This should be the one YOU decide has highest priority, not necessarily first in list.
2. Implement the feature, ensuring type checks pass and tests pass.
3. Update the PRD with the work done (mark passes: true).
4. Append your progress to progress.txt. Use this to leave a note for the next person working in your codebase.
5. Make a git commit of that feature.
6. Only work on a single feature.

If while implementing the feature, you notice the PRD is complete, output RALPH_COMPLETE.
")

  echo "$OUTPUT"

  if [[ "$OUTPUT" == *"RALPH_COMPLETE"* ]]; then
    echo "PRD complete after $i iterations"
    exit 0
  fi
done

echo "Reached max iterations ($MAX_ITERATIONS)"
```

### ralph-once.sh (Human-in-the-loop)

```bash
#!/bin/bash
set -e

claude "$(cat plans/prd.json)" "$(cat plans/progress.txt)" "
Steps to complete:
1. Find the highest priority feature to work on and work only on that feature.
2. Implement the feature, ensuring type checks pass and tests pass.
3. Update the PRD with the work done.
4. Append your progress to progress.txt.
5. Make a git commit of that feature.
6. Only work on a single feature.
"
```

### prd.json

```json
{
  "features": [
    {
      "id": 1,
      "description": "User can create new project",
      "acceptance": "Form submits, project appears in list",
      "passes": true
    },
    {
      "id": 2,
      "description": "User can delete project with confirmation",
      "acceptance": "Confirmation dialog shows, project removed on confirm",
      "passes": false
    },
    {
      "id": 3,
      "description": "Project list shows loading state",
      "acceptance": "Skeleton UI while fetching",
      "passes": false
    }
  ]
}
```

### progress.txt

```
=== Iteration 1 ===
Implemented project creation form.
- Added CreateProjectDialog component
- Wired up to createProject server action
- Tests passing

Note for next: Delete functionality should add confirmation dialog before the deleteProject call.

=== Iteration 2 ===
Added delete confirmation dialog.
- Used AlertDialog from shadcn/ui
- Added tests for cancel and confirm flows

Next: Loading state needs Skeleton component from shadcn/ui.
```

## Key Principles

### 1. Small Tasks

Keep PRD items small and focused:

```json
// ❌ Too big
{
  "description": "Implement entire user management system",
  "passes": false
}

// ✅ Right size
{
  "description": "User list displays with pagination",
  "passes": false
},
{
  "description": "User search filters by name/email",
  "passes": false
},
{
  "description": "User delete shows confirmation",
  "passes": false
}
```

Small tasks = better code quality, less context consumed, clearer feedback.

### 2. Robust Feedback Loops

Essential for Ralph to produce working code:

```bash
# In your prompt, require:
pnpm type-check  # TypeScript must pass
pnpm test        # Unit tests must pass
```

From Anthropic's research: LLMs mark features complete without proper testing unless explicitly prompted to verify.

Add browser automation (Playwright MCP) for E2E verification when budget allows.

### 3. Git Commits Per Feature

Each loop iteration = one commit:

- LLM can query git history for context
- Easy to review/revert individual features
- Progress visible in commit log

### 4. Append-Only Progress

Tell LLM to **append** to progress.txt, not update:

```
❌ "Update progress.txt with your learnings"
   → LLM overwrites entire file

✅ "Append your progress to progress.txt"
   → LLM adds to end, preserving history
```

### 5. Priority Selection

Let LLM choose priority, not just first item:

```
❌ "Work on the first incomplete item"
   → Rigid, doesn't consider dependencies

✅ "Find the highest priority feature. This should be the one YOU decide has highest priority, not necessarily first in list."
   → LLM can reason about what to do next
```

## AFK vs Human-in-the-Loop

### AFK Ralph (Overnight)

```bash
# Run with max iterations, let it work
./plans/ralph.sh 20

# Optional: notification when done
./plans/ralph.sh 20 && notify-send "Ralph complete"
```

Best for:

- Well-defined PRD items
- Strong test coverage
- Overnight/background work

### Human-in-the-Loop Ralph

```bash
# Run once, review, run again
./plans/ralph-once.sh
# Review changes...
./plans/ralph-once.sh
# Review changes...
```

Best for:

- Difficult features needing steering
- Learning Ralph's capabilities
- Features requiring judgment calls

## PRD Design Tips

### Use Acceptance Criteria

```json
{
  "description": "Beats display as orange dots below clip",
  "acceptance": [
    "3 dots appear below clip when beat added",
    "Dots are orange colored (#f97316)",
    "Dots form ellipsis pattern horizontally"
  ],
  "passes": false
}
```

### Group Related Features

```json
{
  "features": [
    // Core functionality first
    { "id": 1, "description": "Beat can be added to clip", "passes": true },
    { "id": 2, "description": "Beat indicator shows below clip", "passes": false },
    { "id": 3, "description": "Beat adds gap at clip end", "passes": false },

    // Polish after core works
    { "id": 4, "description": "Beat indicator animates on hover", "passes": false }
  ]
}
```

### Include Context

```json
{
  "description": "Delete video shows confirmation dialog",
  "context": "Use AlertDialog from shadcn/ui, match existing delete patterns in codebase",
  "passes": false
}
```

## Comparison: Ralph vs Multi-Phase Plans

| Aspect            | Multi-Phase Plans         | Ralph                           |
| ----------------- | ------------------------- | ------------------------------- |
| Task ordering     | Explicit, upfront         | Dynamic, LLM decides            |
| Adding tasks      | Hard, must fit in plan    | Easy, just add to PRD           |
| Memory            | Plan file                 | progress.txt + git history      |
| Context usage     | One big plan              | Small task per iteration        |
| Human effort      | Design full plan          | Design requirements only        |
| Mental model      | Anal-retentive planner    | Product designer                |

## Best Practices

1. **Types are essential** — TypeScript catches errors between iterations
2. **Tests are essential** — Feedback loops prevent broken commits
3. **Keep tasks small** — Leave context budget for verification
4. **Use git** — Each commit = checkpoint, queryable history
5. **Append progress** — Don't lose learnings between iterations
6. **Set max iterations** — Backstop against infinite loops
7. **Review commits** — Ralph produces code, humans verify quality

## Quick Reference

| File           | Purpose                              |
| -------------- | ------------------------------------ |
| `ralph.sh`     | Main loop, runs N iterations         |
| `ralph-once.sh`| Single iteration, human review       |
| `prd.json`     | Task list with passes flags          |
| `progress.txt` | LLM memory, append-only learnings    |

| Command                    | Purpose                    |
| -------------------------- | -------------------------- |
| `./plans/ralph.sh 10`      | Run 10 iterations AFK      |
| `./plans/ralph-once.sh`    | Run once, review, repeat   |
| `git log --oneline`        | See completed features     |

## References

- [Jeffrey Huntley's original Ralph article](https://ghuntley.com/ralph/)
- [Anthropic: Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Matt Pocock's Ralph video](https://www.youtube.com/watch?v=_IK18goX4X8)

