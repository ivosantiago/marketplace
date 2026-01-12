---
name: ivosantiago-architecture
description: Expert guidance for full-stack application architecture. Use when designing system structure, organizing code, implementing patterns, or making architectural decisions. Covers project structure, separation of concerns, and scalability patterns.
---

# Full-Stack Architecture Patterns

## Feature-Scoped Architecture (Preferred)

Organize code by **feature modules** rather than by technical layer. Each feature is self-contained with its own actions, schemas, components, and utilities.

### Directory Structure

```
src/
├── features/                    # Business logic by domain
│   └── {feature}/
│       ├── actions/            # Server Actions (create-, update-, delete-)
│       ├── schemas/            # Zod validation schemas
│       ├── components/         # Feature-specific React components
│       ├── utils/              # Helper functions
│       ├── lib/                # Feature-specific libraries
│       ├── types/              # TypeScript types
│       └── templates/          # Templates (PDF, email, etc.)
│
├── app/                         # Routes only - minimal logic
│   └── (group)/route/          # Import from features, render UI
│
├── components/ui/               # shadcn/ui components ONLY (do not modify or add custom components here)
├── lib/                         # Shared utilities (auth, db client, helpers)
└── db/                          # Database schema and client
```

### Core Principles

1. **Routes are thin** - App routes import from features, they don't contain business logic
2. **Features are self-contained** - All related code lives together (actions + schemas + components)
3. **Shared code is explicit** - Only truly reusable code goes in `components/ui/` or `lib/`
4. **Server Actions colocate with features** - Not with routes
5. **Compose on top of shadcn/ui** - Instead of modifying shadcn components, compose your components on top of them while following shadcn's architecture guidelines (using `cn()` utility, CSS variables, same styling patterns). Keep `components/ui/` pristine for shadcn components only to maintain compatibility with tools like [tweakcn.com](https://tweakcn.com/)
6. **Feature components follow shadcn architecture** - When creating new components in feature directories, follow shadcn's patterns: use Tailwind CSS theme variables (e.g., `bg-background`, `text-foreground`, `border-border`), the `cn()` utility for className merging, and the same component structure conventions

### File Naming Conventions

- **Actions**: `create-{entity}.ts`, `update-{entity}.ts`, `delete-{entity}.ts`
- **Schemas**: `{entity}-schema.ts`
- **Components**: `{entity}-card.tsx`, `{entity}-grid.tsx`, `{entity}-dialog.tsx`

### Example Feature Structure

```
src/features/invoices/
├── actions/
│   ├── create-invoice.ts
│   ├── update-invoice.ts
│   ├── delete-invoice.ts
│   └── send-invoice.ts
├── schemas/
│   └── invoice-schema.ts
├── components/
│   ├── invoice-card.tsx
│   ├── invoice-grid.tsx
│   ├── invoice-dialog.tsx
│   └── invoice-form.tsx
├── utils/
│   └── calculate-totals.ts
├── templates/
│   └── invoice-pdf.tsx
└── types/
    └── index.ts
```

### Usage in Routes

```typescript
// app/(dashboard)/invoices/page.tsx
import { getInvoices } from "@/features/invoices/actions/get-invoices";
import { InvoiceGrid } from "@/features/invoices/components/invoice-grid";

export default async function InvoicesPage() {
  const invoices = await getInvoices();
  return <InvoiceGrid invoices={invoices} />;
}
```

---

## Alternative: Layer-Based Structure

For smaller projects or when team prefers traditional separation:

```
├── app/                    # Next.js App Router
│   ├── (auth)/            # Auth route group
│   │   ├── login/
│   │   └── register/
│   ├── (dashboard)/       # Protected routes
│   │   ├── layout.tsx
│   │   └── page.tsx
│   ├── api/               # API routes
│   │   └── [...]/
│   ├── layout.tsx
│   └── page.tsx
├── components/            # React components
│   ├── ui/               # shadcn/ui components
│   ├── forms/            # Form components
│   └── layouts/          # Layout components
├── lib/                   # Shared utilities
│   ├── auth.ts           # Auth configuration
│   ├── db.ts             # Database client
│   ├── utils.ts          # Helper functions
│   └── validations.ts    # Zod schemas
├── db/                    # Database layer
│   ├── schema.ts         # Drizzle schema
│   ├── migrations/       # SQL migrations
│   └── queries/          # Reusable queries
├── services/             # Business logic
│   ├── user.service.ts
│   └── post.service.ts
├── types/                # TypeScript types
│   └── index.ts
└── config/               # Configuration
    └── site.ts
```

## Layered Architecture

### Layer Responsibilities

```
┌─────────────────────────────────────┐
│         Presentation Layer          │  ← React Components, Pages
├─────────────────────────────────────┤
│         Application Layer           │  ← Server Actions, API Routes
├─────────────────────────────────────┤
│          Service Layer              │  ← Business Logic
├─────────────────────────────────────┤
│         Data Access Layer           │  ← Drizzle Queries
├─────────────────────────────────────┤
│            Database                 │  ← PostgreSQL
└─────────────────────────────────────┘
```

### Implementation Example

```typescript
// 1. Data Access Layer (db/queries/users.ts)
import { db } from "@/lib/db";
import { users } from "@/db/schema";
import { eq } from "drizzle-orm";

export const userQueries = {
  findById: (id: string) =>
    db.query.users.findFirst({ where: eq(users.id, id) }),

  findByEmail: (email: string) =>
    db.query.users.findFirst({ where: eq(users.email, email) }),

  create: (data: NewUser) => db.insert(users).values(data).returning(),
};

// 2. Service Layer (services/user.service.ts)
import { userQueries } from "@/db/queries/users";
import { hashPassword } from "@/lib/auth";

export const userService = {
  async createUser(data: CreateUserInput) {
    const existing = await userQueries.findByEmail(data.email);
    if (existing) {
      throw new Error("Email already exists");
    }

    const hashedPassword = await hashPassword(data.password);
    return userQueries.create({
      ...data,
      password: hashedPassword,
    });
  },

  async getUserProfile(id: string) {
    const user = await userQueries.findById(id);
    if (!user) throw new Error("User not found");
    return user;
  },
};

// 3. Application Layer (app/actions/user.ts)
("use server");

import { userService } from "@/services/user.service";
import { createUserSchema } from "@/lib/validations";

export async function registerUser(formData: FormData) {
  const validated = createUserSchema.parse({
    email: formData.get("email"),
    password: formData.get("password"),
    name: formData.get("name"),
  });

  return userService.createUser(validated);
}
```

## Design Patterns

### Repository Pattern

```typescript
// Generic repository interface
interface Repository<T, CreateInput, UpdateInput> {
  findById(id: string): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(data: CreateInput): Promise<T>;
  update(id: string, data: UpdateInput): Promise<T>;
  delete(id: string): Promise<void>;
}
```

### Factory Pattern for Services

```typescript
// Create services with dependencies injected
function createUserService(db: Database, auth: Auth) {
  return {
    async createUser(data: CreateUserInput) {
      // Implementation with injected dependencies
    },
  };
}
```

## Error Handling Strategy

```typescript
// Custom error classes
class AppError extends Error {
  constructor(
    message: string,
    public code: string,
    public statusCode: number = 400
  ) {
    super(message);
  }
}

class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, "NOT_FOUND", 404);
  }
}

class ValidationError extends AppError {
  constructor(message: string) {
    super(message, "VALIDATION_ERROR", 400);
  }
}

// Error handler for API routes
export function handleApiError(error: unknown) {
  if (error instanceof AppError) {
    return NextResponse.json(
      { error: error.message, code: error.code },
      { status: error.statusCode }
    );
  }

  console.error("Unexpected error:", error);
  return NextResponse.json({ error: "Internal server error" }, { status: 500 });
}
```

## Configuration Management

```typescript
// config/site.ts
export const siteConfig = {
  name: "My App",
  description: "Application description",
  url: process.env.NEXT_PUBLIC_APP_URL,
} as const;

// config/env.ts
import { z } from "zod";

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NEXT_PUBLIC_APP_URL: z.string().url(),
  AUTH_SECRET: z.string().min(32),
});

export const env = envSchema.parse(process.env);
```

## Best Practices

1. **Feature-First Organization** - Colocate related code by domain, not by type
2. **Thin Routes** - Keep app routes as thin wrappers that import from features
3. **Separation of Concerns** - Each layer/feature has single responsibility
4. **Fail Fast** - Validate inputs at boundaries with Zod schemas
5. **Consistent Error Handling** - Use custom error classes
6. **Environment Validation** - Validate env vars at startup
7. **Type Safety** - End-to-end types from DB to UI
8. **Explicit Sharing** - Only move code to shared locations when actually reused
9. **Compose on shadcn, Don't Modify** - Build components by composing on top of shadcn primitives in feature directories. Never modify `components/ui/` files. Follow shadcn's architecture (CSS variables, `cn()` utility, styling patterns) to maintain compatibility with ecosystem tools like [tweakcn.com](https://tweakcn.com/)
10. **Follow shadcn Architecture Patterns** - Use Tailwind CSS theme variables (`bg-background`, `text-foreground`, `border-border`, etc.), the `cn()` utility for className merging, and shadcn's component structure conventions in all composed components
