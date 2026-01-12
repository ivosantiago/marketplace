---
name: ivosantiago-better-auth
description: Expert guidance for Better Auth authentication implementation. Use when implementing user authentication, sessions, OAuth providers, or any auth-related features. Covers setup, providers, and security patterns.
---

# Better Auth Implementation

## Installation & Setup

```bash
npm install better-auth
```

### Server Configuration
```typescript
// lib/auth.ts
import { betterAuth } from "better-auth"
import { drizzleAdapter } from "better-auth/adapters/drizzle"
import { db } from "@/db"

export const auth = betterAuth({
  database: drizzleAdapter(db, {
    provider: "pg",
  }),
  emailAndPassword: {
    enabled: true,
  },
  socialProviders: {
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    },
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
  },
})

export type Session = typeof auth.$Infer.Session
```

### API Route Handler
```typescript
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth"
import { toNextJsHandler } from "better-auth/next-js"

export const { GET, POST } = toNextJsHandler(auth)
```

### Client Configuration
```typescript
// lib/auth-client.ts
import { createAuthClient } from "better-auth/react"

export const authClient = createAuthClient({
  baseURL: process.env.NEXT_PUBLIC_APP_URL,
})

export const { signIn, signUp, signOut, useSession } = authClient
```

## Authentication Flows

### Email/Password Sign Up
```tsx
"use client"

import { signUp } from "@/lib/auth-client"
import { useState } from "react"

export function SignUpForm() {
  const [error, setError] = useState("")
  
  async function handleSubmit(formData: FormData) {
    const result = await signUp.email({
      email: formData.get("email") as string,
      password: formData.get("password") as string,
      name: formData.get("name") as string,
    })
    
    if (result.error) {
      setError(result.error.message)
    }
  }
  
  return (
    <form action={handleSubmit}>
      <input name="name" type="text" placeholder="Name" required />
      <input name="email" type="email" placeholder="Email" required />
      <input name="password" type="password" placeholder="Password" required />
      <button type="submit">Sign Up</button>
      {error && <p className="text-red-500">{error}</p>}
    </form>
  )
}
```

### Sign In
```tsx
"use client"

import { signIn } from "@/lib/auth-client"

export function SignInForm() {
  async function handleSubmit(formData: FormData) {
    await signIn.email({
      email: formData.get("email") as string,
      password: formData.get("password") as string,
    })
  }
  
  return (
    <form action={handleSubmit}>
      <input name="email" type="email" required />
      <input name="password" type="password" required />
      <button type="submit">Sign In</button>
    </form>
  )
}
```

### Social Sign In
```tsx
"use client"

import { signIn } from "@/lib/auth-client"

export function SocialSignIn() {
  return (
    <div className="space-y-2">
      <button onClick={() => signIn.social({ provider: "github" })}>
        Sign in with GitHub
      </button>
      <button onClick={() => signIn.social({ provider: "google" })}>
        Sign in with Google
      </button>
    </div>
  )
}
```

## Session Management

### useSession Hook
```tsx
"use client"

import { useSession } from "@/lib/auth-client"

export function UserNav() {
  const { data: session, isPending } = useSession()
  
  if (isPending) return <Skeleton />
  
  if (!session) {
    return <Link href="/login">Sign In</Link>
  }
  
  return (
    <div>
      <span>{session.user.name}</span>
      <button onClick={() => signOut()}>Sign Out</button>
    </div>
  )
}
```

### Server-Side Session
```typescript
// In Server Components or API routes
import { auth } from "@/lib/auth"
import { headers } from "next/headers"

export async function getSession() {
  const session = await auth.api.getSession({
    headers: await headers(),
  })
  return session
}
```

## Protected Routes

### Middleware
```typescript
// middleware.ts
import { auth } from "@/lib/auth"
import { NextResponse } from "next/server"

export async function middleware(request: Request) {
  const session = await auth.api.getSession({
    headers: request.headers,
  })
  
  if (!session && request.url.includes("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url))
  }
  
  return NextResponse.next()
}

export const config = {
  matcher: ["/dashboard/:path*"],
}
```

## Database Schema

Better Auth creates these tables automatically:
- `user` - User accounts
- `session` - Active sessions
- `account` - OAuth provider accounts
- `verification` - Email verification tokens

## Best Practices

1. **Secure Cookies** - Use httpOnly, secure, sameSite settings
2. **CSRF Protection** - Built into Better Auth
3. **Rate Limiting** - Implement for auth endpoints
4. **Password Requirements** - Enforce strong passwords
5. **Session Rotation** - Rotate tokens on sensitive actions

## Reference Documentation

For detailed API references, consult:
- Better Auth Documentation: https://www.better-auth.com/llms.txt

