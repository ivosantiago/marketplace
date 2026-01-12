# Marketplace

A modern full-stack marketplace application.

## Tech Stack

- **Frontend**: Next.js (App Router), React, TypeScript
- **Styling**: Tailwind CSS, shadcn/ui
- **Backend**: Next.js API Routes, Server Actions
- **Database**: PostgreSQL, Drizzle ORM
- **Auth**: Better Auth
- **Deployment**: Vercel

## Getting Started

```bash
# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local

# Run database migrations
npm run db:migrate

# Start development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

## Claude Code Skills

This project includes custom Claude Code skills for development assistance. See [`.claude/README.md`](.claude/README.md) for details.

**Quick start:**

```
/ivosantiago-stack build a new feature
```

## Project Structure

```
├── src/
│   ├── app/              # Next.js App Router
│   ├── features/         # Feature modules
│   ├── components/ui/    # Shared UI components
│   ├── lib/              # Shared utilities
│   └── db/               # Database schema
├── .claude/              # Claude Code skills & commands
└── drizzle/              # Database migrations
```

## License

MIT
