# ivosantiago's Plugin Marketplace

A Claude Code plugin marketplace with full-stack development skills and AI workflow methodologies.

## Quick Install

```bash
# Add the marketplace
/plugin marketplace add ivosantiago/marketplace

# Install the plugin
/plugin install ivosantiago-stack@ivosantiago-marketplace
```

## Tech Stack

| Layer          | Technology                              |
| -------------- | --------------------------------------- |
| **Frontend**   | Next.js (App Router), React, TypeScript |
| **Styling**    | Tailwind CSS, shadcn/ui                 |
| **Backend**    | Next.js API Routes, Server Actions      |
| **Database**   | PostgreSQL, Drizzle ORM                 |
| **Auth**       | Better Auth                             |
| **Deployment** | Vercel                                  |

## Available Plugins

### ivosantiago-stack

Complete full-stack development toolkit with 10 specialized skills:

| Skill                          | Description                                            |
| ------------------------------ | ------------------------------------------------------ |
| `ivosantiago-nextjs-frontend`  | App Router, Server/Client Components, routing, layouts |
| `ivosantiago-nextjs-backend`   | API Routes, Server Actions, middleware                 |
| `ivosantiago-typescript`       | Type patterns, generics, Zod integration               |
| `ivosantiago-drizzle-postgres` | Schema definitions, queries, migrations, relations     |
| `ivosantiago-better-auth`      | Authentication flows, sessions, OAuth providers        |
| `ivosantiago-tailwind`         | Utility classes, responsive design, dark mode          |
| `ivosantiago-shadcn-ui`        | Component usage, forms, theming                        |
| `ivosantiago-architecture`     | Feature-scoped structure, patterns, best practices     |
| `ivosantiago-security`         | Input validation, auth security, OWASP practices       |
| `ivosantiago-vercel`           | Deployment, edge functions, caching strategies         |

**Slash Command**: `/ivosantiago-stack` — Invoke all skills at once

---

### matt-pocock-workflow

Matt Pocock's Claude Code workflow methodologies for efficient AI-assisted development. Based on his YouTube tutorials.

| Skill                  | Description                                                      |
| ---------------------- | ---------------------------------------------------------------- |
| `matt-pocock-planning` | Multi-phase planning, context preservation, GitHub CLI workflows |
| `matt-pocock-ralph`    | The Ralph Wiggum PRD-loop approach for autonomous agents         |

**Slash Command**: `/matt-workflow` — Activate Matt's workflow methodology

#### Core Rules

- **Extreme concision** — Sacrifice grammar for brevity in all interactions
- **GitHub CLI first** — Use `gh` as primary GitHub interface
- **Branch naming** — Prefix branches with username (e.g., `matt/feature`)
- **PR TODOs** — Use checkbox markdown: `- [ ] Task description`
- **Changesets** — Write to `.changeset/0000-your-change.md`
- **Tag Claude** — Use `@claude` in GitHub issues

#### Workflow Approaches

| Approach           | Use Case                        | Key File        |
| ------------------ | ------------------------------- | --------------- |
| Multi-phase plans  | Features spanning context windows | GitHub issues   |
| Ralph Wiggum (PRD) | Autonomous overnight agents     | `prd.json` loop |

**References**:
- [Claude Code Workflow](https://www.youtube.com/watch?v=kZ-zzHVUrO4)
- [Ralph Wiggum Approach](https://www.youtube.com/watch?v=_IK18goX4X8)

## Installation

### Prerequisites

1. **Install Claude Code**

   ```bash
   # macOS / Linux
   curl -fsSL https://claude.ai/install.sh | sh

   # Or via npm
   npm install -g @anthropic-ai/claude-code
   ```

2. **Login to Claude**

   ```bash
   claude login
   ```

### From GitHub (Recommended)

```bash
# In Claude Code, run:
/plugin marketplace add ivosantiago/marketplace

# Install plugins:
/plugin install ivosantiago-stack@ivosantiago-marketplace
/plugin install matt-pocock-workflow@ivosantiago-marketplace
```

### From Local Clone

```bash
# Clone the repository
git clone https://github.com/ivosantiago/marketplace.git
cd marketplace

# Add the local marketplace
/plugin marketplace add ./

# Install plugins
/plugin install ivosantiago-stack@ivosantiago-marketplace
/plugin install matt-pocock-workflow@ivosantiago-marketplace
```

### Verify Installation

```
/plugin list
```

You should see both plugins in the list.

## Usage

### Use the slash command

```
/ivosantiago-stack build a user dashboard with authentication
```

### Ask questions naturally

Claude will automatically use relevant skills:

```
How do I set up Drizzle migrations?
```

→ Uses `ivosantiago-drizzle-postgres`

```
Create a login form with email/password
```

→ Uses `ivosantiago-better-auth` + `ivosantiago-shadcn-ui`

## Project Structure (Feature-Scoped)

The skills teach Claude to organize code by **feature modules**:

```
src/
├── features/                    # Business logic by domain
│   └── {feature}/
│       ├── actions/            # Server Actions
│       ├── schemas/            # Zod validation
│       ├── components/         # Feature-specific UI
│       ├── utils/              # Helper functions
│       └── types/              # TypeScript types
│
├── app/                         # Routes only (thin)
├── components/ui/               # Shared UI (shadcn/ui)
├── lib/                         # Shared utilities
└── db/                          # Database schema
```

## Documentation References

These skills reference official LLM-friendly documentation:

- **Next.js**: https://nextjs.org/docs/llms-full.txt
- **Drizzle ORM**: https://orm.drizzle.team/llms-full.txt
- **shadcn/ui**: https://ui.shadcn.com/llms.txt
- **Better Auth**: https://www.better-auth.com/llms.txt
- **Vercel**: https://vercel.com/docs/llms-full.txt

## Marketplace Structure

```
.claude-plugin/
└── marketplace.json              # Marketplace catalog

plugins/
├── ivosantiago-stack/
│   ├── .claude-plugin/
│   │   └── plugin.json           # Plugin manifest
│   ├── commands/
│   │   └── ivosantiago-stack.md  # Slash command
│   └── skills/
│       ├── ivosantiago-architecture/
│       ├── ivosantiago-better-auth/
│       ├── ivosantiago-drizzle-postgres/
│       ├── ivosantiago-nextjs-backend/
│       ├── ivosantiago-nextjs-frontend/
│       ├── ivosantiago-security/
│       ├── ivosantiago-shadcn-ui/
│       ├── ivosantiago-tailwind/
│       ├── ivosantiago-typescript/
│       └── ivosantiago-vercel/
│
└── matt-pocock-workflow/
    ├── .claude-plugin/
    │   └── plugin.json           # Plugin manifest
    ├── commands/
    │   └── matt-workflow.md      # Slash command
    └── skills/
        ├── matt-pocock-planning/ # Multi-phase planning methodology
        └── matt-pocock-ralph/    # Ralph Wiggum PRD-loop approach
```

## Updating

To get the latest version:

```
/plugin marketplace update ivosantiago-marketplace
/plugin update ivosantiago-stack@ivosantiago-marketplace
/plugin update matt-pocock-workflow@ivosantiago-marketplace
```

## License

MIT
