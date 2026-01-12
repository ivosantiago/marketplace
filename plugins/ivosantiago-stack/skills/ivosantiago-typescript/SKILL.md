---
name: ivosantiago-typescript
description: Expert TypeScript guidance for type-safe development. Use when writing TypeScript code, defining types/interfaces, handling generics, or ensuring type safety across the application. Covers advanced patterns and best practices.
---

# TypeScript Best Practices

## Core Type Patterns

### Prefer Interfaces for Objects
```typescript
// ✅ Good - Extendable, clear intent
interface User {
  id: string
  name: string
  email: string
}

// Use type for unions, intersections, primitives
type Status = 'pending' | 'active' | 'inactive'
type ID = string | number
```

### Strict Configuration
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": true
  }
}
```

## Utility Types

```typescript
// Partial - All properties optional
type PartialUser = Partial<User>

// Required - All properties required
type RequiredUser = Required<User>

// Pick - Select specific properties
type UserName = Pick<User, 'name' | 'email'>

// Omit - Exclude properties
type UserWithoutId = Omit<User, 'id'>

// Record - Key-value mapping
type UserRoles = Record<string, Role>

// ReturnType - Extract function return type
type Result = ReturnType<typeof fetchUser>

// Parameters - Extract function parameters
type Params = Parameters<typeof fetchUser>
```

## Generics

### Functions
```typescript
function first<T>(arr: T[]): T | undefined {
  return arr[0]
}

// With constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}
```

### Interfaces
```typescript
interface ApiResponse<T> {
  data: T
  error: string | null
  loading: boolean
}

// Usage
const response: ApiResponse<User[]> = await fetchUsers()
```

## Type Guards

```typescript
// Type predicate
function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'id' in value &&
    'email' in value
  )
}

// Discriminated unions
type Result<T> = 
  | { success: true; data: T }
  | { success: false; error: string }

function handleResult<T>(result: Result<T>) {
  if (result.success) {
    // TypeScript knows result.data exists
    console.log(result.data)
  } else {
    // TypeScript knows result.error exists
    console.error(result.error)
  }
}
```

## Zod Integration

```typescript
import { z } from 'zod'

// Define schema
const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1),
  email: z.string().email(),
  age: z.number().int().positive().optional(),
})

// Infer type from schema
type User = z.infer<typeof UserSchema>

// Validate at runtime
function processUser(input: unknown): User {
  return UserSchema.parse(input)
}
```

## Best Practices

1. **Avoid `any`** - Use `unknown` for truly unknown types
2. **Const Assertions** - Use `as const` for literal types
3. **Exhaustive Checks** - Use `never` for exhaustive switch statements
4. **No Non-null Assertions** - Avoid `!` operator, handle nulls properly
5. **Prefer Type Inference** - Let TypeScript infer when obvious

## Pattern: Branded Types

```typescript
// Prevent mixing similar types
type UserId = string & { __brand: 'UserId' }
type PostId = string & { __brand: 'PostId' }

function createUserId(id: string): UserId {
  return id as UserId
}

function getUser(id: UserId) { /* ... */ }

// Compile error: can't pass PostId to getUser
```
