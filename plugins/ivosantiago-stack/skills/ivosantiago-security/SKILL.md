---
name: ivosantiago-security
description: Expert security guidance for full-stack applications. Use when implementing authentication, authorization, input validation, or any security-related features. Covers OWASP best practices, secure coding patterns, and common vulnerability prevention.
---

# Security Best Practices

## Input Validation

### Always Validate Server-Side
```typescript
// lib/validations.ts
import { z } from 'zod'

export const userInputSchema = z.object({
  email: z.string().email().max(255),
  password: z.string().min(8).max(100),
  name: z.string().min(1).max(100).regex(/^[a-zA-Z\s]+$/),
})

// In Server Action or API Route
export async function createUser(input: unknown) {
  const validated = userInputSchema.parse(input) // Throws on invalid
  // Proceed with validated data
}
```

### Sanitize Output
```tsx
// React automatically escapes, but be careful with:
// ❌ Dangerous
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ Safe - let React escape
<div>{userContent}</div>

// For rich text, use a sanitizer
import DOMPurify from 'isomorphic-dompurify'
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(content) }} />
```

## SQL Injection Prevention

### Drizzle ORM (Safe by Default)
```typescript
// ✅ Safe - Drizzle uses parameterized queries
const user = await db.query.users.findFirst({
  where: eq(users.email, userInput),
})

// ❌ Never do this
const user = await db.execute(
  `SELECT * FROM users WHERE email = '${userInput}'` // VULNERABLE
)
```

## Authentication Security

### Password Hashing
```typescript
import { hash, verify } from '@node-rs/argon2'

// Hash password before storing
const hashedPassword = await hash(password, {
  memoryCost: 19456,
  timeCost: 2,
  outputLen: 32,
  parallelism: 1,
})

// Verify password
const isValid = await verify(hashedPassword, inputPassword)
```

### Session Security
```typescript
// Secure cookie settings
const sessionCookie = {
  httpOnly: true,      // Prevent XSS access
  secure: true,        // HTTPS only
  sameSite: 'lax',     // CSRF protection
  maxAge: 60 * 60 * 24 * 7, // 7 days
  path: '/',
}
```

## Authorization

### Role-Based Access Control
```typescript
// middleware.ts
const protectedRoutes = {
  '/admin': ['admin'],
  '/dashboard': ['user', 'admin'],
}

export async function middleware(request: NextRequest) {
  const session = await getSession()
  const path = request.nextUrl.pathname
  
  for (const [route, roles] of Object.entries(protectedRoutes)) {
    if (path.startsWith(route)) {
      if (!session) {
        return NextResponse.redirect(new URL('/login', request.url))
      }
      if (!roles.includes(session.user.role)) {
        return NextResponse.redirect(new URL('/unauthorized', request.url))
      }
    }
  }
  
  return NextResponse.next()
}
```

### Resource-Level Authorization
```typescript
// Always verify ownership
export async function updatePost(postId: string, userId: string, data: UpdatePostInput) {
  const post = await db.query.posts.findFirst({
    where: eq(posts.id, postId),
  })
  
  if (!post) throw new NotFoundError('Post')
  if (post.authorId !== userId) throw new ForbiddenError('Not authorized')
  
  return db.update(posts).set(data).where(eq(posts.id, postId))
}
```

## CSRF Protection

### Server Actions (Built-in)
Next.js Server Actions have built-in CSRF protection.

### For API Routes
```typescript
// Use CSRF tokens for state-changing operations
import { csrf } from '@/lib/csrf'

export async function POST(request: Request) {
  const token = request.headers.get('X-CSRF-Token')
  if (!csrf.verify(token)) {
    return NextResponse.json({ error: 'Invalid CSRF token' }, { status: 403 })
  }
  // Process request
}
```

## Rate Limiting

```typescript
// lib/rate-limit.ts
import { Ratelimit } from '@upstash/ratelimit'
import { Redis } from '@upstash/redis'

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'),
})

// In API route
export async function POST(request: Request) {
  const ip = request.headers.get('x-forwarded-for') ?? 'anonymous'
  const { success, limit, reset, remaining } = await ratelimit.limit(ip)
  
  if (!success) {
    return NextResponse.json(
      { error: 'Too many requests' },
      { 
        status: 429,
        headers: {
          'X-RateLimit-Limit': limit.toString(),
          'X-RateLimit-Remaining': remaining.toString(),
          'X-RateLimit-Reset': reset.toString(),
        },
      }
    )
  }
  
  // Process request
}
```

## Security Headers

```typescript
// next.config.ts
const securityHeaders = [
  { key: 'X-DNS-Prefetch-Control', value: 'on' },
  { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains' },
  { key: 'X-Frame-Options', value: 'SAMEORIGIN' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Referrer-Policy', value: 'origin-when-cross-origin' },
  { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' },
]

export default {
  async headers() {
    return [{ source: '/:path*', headers: securityHeaders }]
  },
}
```

## Environment Variables

```typescript
// ❌ Never expose secrets to client
NEXT_PUBLIC_SECRET_KEY=xxx  // WRONG - exposed to browser

// ✅ Keep secrets server-side only
DATABASE_URL=xxx
AUTH_SECRET=xxx

// Validate at startup
import { z } from 'zod'

const serverEnvSchema = z.object({
  DATABASE_URL: z.string(),
  AUTH_SECRET: z.string().min(32),
})

serverEnvSchema.parse(process.env) // Fails fast if missing
```

## Security Checklist

- [ ] Input validation on all user inputs
- [ ] Parameterized queries (Drizzle handles this)
- [ ] Password hashing with Argon2 or bcrypt
- [ ] Secure session cookies (httpOnly, secure, sameSite)
- [ ] CSRF protection for state-changing operations
- [ ] Rate limiting on authentication endpoints
- [ ] Authorization checks on all protected resources
- [ ] Security headers configured
- [ ] Environment variables properly secured
- [ ] Dependencies regularly updated

