---
name: ivosantiago-drizzle-postgres
description: Expert guidance for Drizzle ORM with PostgreSQL. Use when defining database schemas, writing queries, handling migrations, or implementing database operations. Covers type-safe queries, relations, and performance patterns.
---

# Drizzle ORM with PostgreSQL

## Schema Definition

### Basic Table
```typescript
// db/schema.ts
import { pgTable, text, timestamp, uuid, integer, boolean } from 'drizzle-orm/pg-core'

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  name: text('name').notNull(),
  email: text('email').notNull().unique(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull(),
})

export const posts = pgTable('posts', {
  id: uuid('id').primaryKey().defaultRandom(),
  title: text('title').notNull(),
  content: text('content'),
  published: boolean('published').default(false),
  authorId: uuid('author_id').references(() => users.id).notNull(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
})
```

### Relations
```typescript
import { relations } from 'drizzle-orm'

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}))

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, {
    fields: [posts.authorId],
    references: [users.id],
  }),
}))
```

### Enums
```typescript
import { pgEnum } from 'drizzle-orm/pg-core'

export const statusEnum = pgEnum('status', ['pending', 'active', 'inactive'])

export const orders = pgTable('orders', {
  id: uuid('id').primaryKey().defaultRandom(),
  status: statusEnum('status').default('pending'),
})
```

## Database Client Setup

```typescript
// db/index.ts
import { drizzle } from 'drizzle-orm/postgres-js'
import postgres from 'postgres'
import * as schema from './schema'

const connectionString = process.env.DATABASE_URL!

// For query purposes
const queryClient = postgres(connectionString)
export const db = drizzle(queryClient, { schema })

// Type exports
export type User = typeof schema.users.$inferSelect
export type NewUser = typeof schema.users.$inferInsert
```

## Queries

### Select
```typescript
// All users
const allUsers = await db.select().from(users)

// With conditions
const activeUsers = await db
  .select()
  .from(users)
  .where(eq(users.status, 'active'))

// With relations
const usersWithPosts = await db.query.users.findMany({
  with: {
    posts: true,
  },
})

// Single record
const user = await db.query.users.findFirst({
  where: eq(users.id, userId),
  with: { posts: true },
})
```

### Insert
```typescript
// Single insert
const [newUser] = await db
  .insert(users)
  .values({ name: 'John', email: 'john@example.com' })
  .returning()

// Batch insert
await db.insert(users).values([
  { name: 'Alice', email: 'alice@example.com' },
  { name: 'Bob', email: 'bob@example.com' },
])
```

### Update
```typescript
const [updated] = await db
  .update(users)
  .set({ name: 'Jane' })
  .where(eq(users.id, userId))
  .returning()
```

### Delete
```typescript
await db.delete(users).where(eq(users.id, userId))
```

## Migrations

```bash
# Generate migration
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate

# Push schema (dev only)
npx drizzle-kit push
```

### drizzle.config.ts
```typescript
import { defineConfig } from 'drizzle-kit'

export default defineConfig({
  schema: './db/schema.ts',
  out: './drizzle',
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
})
```

## Transactions

```typescript
await db.transaction(async (tx) => {
  const [user] = await tx
    .insert(users)
    .values({ name: 'John', email: 'john@example.com' })
    .returning()

  await tx.insert(posts).values({
    title: 'First Post',
    authorId: user.id,
  })
})
```

## Best Practices

1. **Use Transactions** - For multi-table operations
2. **Index Foreign Keys** - Add indexes for frequently queried columns
3. **Type Inference** - Use `$inferSelect` and `$inferInsert` for types
4. **Prepared Statements** - Use for repeated queries
5. **Connection Pooling** - Configure for production

## Reference Documentation

For detailed API references, consult:
- Drizzle ORM Documentation: https://orm.drizzle.team/llms-full.txt

