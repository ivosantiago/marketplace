---
name: ivosantiago-nextjs-backend
description: Expert guidance for Next.js backend development including API Routes, Server Actions, middleware, and server-side logic. Use when building APIs, handling form submissions, implementing middleware, or any backend functionality in Next.js.
---

# Next.js Backend Development

## API Routes (Route Handlers)

### Basic Structure
```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server'

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams
  const query = searchParams.get('query')
  
  // Fetch data
  return NextResponse.json({ data })
}

export async function POST(request: NextRequest) {
  const body = await request.json()
  
  // Process data
  return NextResponse.json({ success: true }, { status: 201 })
}
```

### Dynamic Routes
```typescript
// app/api/users/[id]/route.ts
export async function GET(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params
  // Fetch user by id
  return NextResponse.json({ user })
}
```

## Server Actions

### Form Handling
```typescript
// app/actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'

export async function createItem(formData: FormData) {
  const rawFormData = {
    name: formData.get('name'),
    email: formData.get('email'),
  }
  
  // Validate with zod
  // Insert into database
  
  revalidatePath('/items')
  redirect('/items')
}
```

### With Validation
```typescript
'use server'

import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
})

export async function register(formData: FormData) {
  const validatedFields = schema.safeParse({
    email: formData.get('email'),
    password: formData.get('password'),
  })

  if (!validatedFields.success) {
    return { error: validatedFields.error.flatten().fieldErrors }
  }

  // Proceed with registration
}
```

## Middleware

```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Authentication check
  const token = request.cookies.get('token')
  
  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
  
  return NextResponse.next()
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
}
```

## Error Handling

```typescript
// Consistent error responses
export async function GET(request: NextRequest) {
  try {
    const data = await fetchData()
    return NextResponse.json({ data })
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal Server Error' },
      { status: 500 }
    )
  }
}
```

## Best Practices

1. **Type Safety** - Always type request/response bodies
2. **Validation** - Use Zod for input validation
3. **Error Handling** - Consistent error response format
4. **Rate Limiting** - Implement for public APIs
5. **CORS** - Configure properly for external access

## Reference Documentation

For detailed API references, consult:
- Next.js Documentation: https://nextjs.org/docs/llms-full.txt
