# ivosantiago's Full-Stack Skills for Claude Code

A collection of Claude Code skills and commands for building modern full-stack applications with a curated tech stack.

## Tech Stack

| Layer          | Technology                              |
| -------------- | --------------------------------------- |
| **Frontend**   | Next.js (App Router), React, TypeScript |
| **Styling**    | Tailwind CSS, shadcn/ui                 |
| **Backend**    | Next.js API Routes, Server Actions      |
| **Database**   | PostgreSQL, Drizzle ORM                 |
| **Auth**       | Better Auth                             |
| **Deployment** | Vercel                                  |

## Quick Start

Use the slash command to invoke all skills at once:

```
/ivosantiago-stack build a user dashboard with authentication
```

Claude will automatically apply relevant skills based on your request.

## Available Skills

### Core Framework

| Skill                         | Description                                            |
| ----------------------------- | ------------------------------------------------------ |
| `ivosantiago-nextjs-frontend` | App Router, Server/Client Components, routing, layouts |
| `ivosantiago-nextjs-backend`  | API Routes, Server Actions, middleware                 |
| `ivosantiago-typescript`      | Type patterns, generics, Zod integration               |

### Data & Auth

| Skill                          | Description                                        |
| ------------------------------ | -------------------------------------------------- |
| `ivosantiago-drizzle-postgres` | Schema definitions, queries, migrations, relations |
| `ivosantiago-better-auth`      | Authentication flows, sessions, OAuth providers    |

### UI & Styling

| Skill                   | Description                                   |
| ----------------------- | --------------------------------------------- |
| `ivosantiago-tailwind`  | Utility classes, responsive design, dark mode |
| `ivosantiago-shadcn-ui` | Component usage, forms, theming               |

### Architecture & DevOps

| Skill                      | Description                                        |
| -------------------------- | -------------------------------------------------- |
| `ivosantiago-architecture` | Feature-scoped structure, patterns, best practices |
| `ivosantiago-security`     | Input validation, auth security, OWASP practices   |
| `ivosantiago-vercel`       | Deployment, edge functions, caching strategies     |

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

These skills reference official documentation:

- **Next.js**: https://nextjs.org/docs/llms-full.txt
- **Drizzle ORM**: https://orm.drizzle.team/llms-full.txt
- **shadcn/ui**: https://ui.shadcn.com/llms.txt
- **Better Auth**: https://www.better-auth.com/llms.txt
- **Vercel**: https://vercel.com/docs/llms-full.txt

## Installation

### Prerequisites

1. **Install Claude Code CLI**

   ```bash
   # macOS / Linux
   curl -fsSL https://claude.ai/install.sh | sh

   # Or via npm
   npm install -g @anthropic-ai/claude-code
   ```

2. **Authenticate with Anthropic**

   ```bash
   claude login
   ```

### Option 1: Project-Level (Recommended)

Clone this repo and the `.claude/` folder is automatically available:

```bash
git clone <repo-url>
cd marketplace
claude
```

Skills in `.claude/skills/` are auto-discovered when Claude Code runs in this directory.

### Option 2: Personal/Global Installation

Copy skills to your personal config to use across all projects:

```bash
# Copy all skills
cp -r .claude/skills/* ~/.claude/skills/

# Copy the slash command
cp .claude/commands/* ~/.claude/commands/
```

### Verify Installation

In Claude Code, ask:

```
What Skills are available?
```

You should see all `ivosantiago-*` skills listed.

## Usage Examples

### Build a feature

```
/ivosantiago-stack create a blog with posts, comments, and user profiles
```

### Ask about specific topics

```
How do I set up Drizzle migrations?
```

→ Triggers `ivosantiago-drizzle-postgres`

```
Create a login form with email/password
```

→ Triggers `ivosantiago-better-auth` + `ivosantiago-shadcn-ui`

```
Add dark mode support
```

→ Triggers `ivosantiago-tailwind`

## File Structure

```
.claude/
├── commands/
│   └── ivosantiago-stack.md      # Main slash command
└── skills/
    ├── ivosantiago-architecture/
    ├── ivosantiago-better-auth/
    ├── ivosantiago-drizzle-postgres/
    ├── ivosantiago-nextjs-backend/
    ├── ivosantiago-nextjs-frontend/
    ├── ivosantiago-security/
    ├── ivosantiago-shadcn-ui/
    ├── ivosantiago-tailwind/
    ├── ivosantiago-typescript/
    └── ivosantiago-vercel/
```

## License

MIT
