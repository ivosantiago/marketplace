---
name: ivosantiago-nextjs-frontend
description: Expert guidance for Next.js frontend development using App Router, React Server Components, client components, layouts, routing, and UI patterns. Use when building UI components, pages, layouts, or any frontend features in Next.js. Integrates with Tailwind CSS and shadcn/ui.
---

# Next.js Frontend Development

## Core Principles

1. **App Router First** - Use the App Router (`app/` directory) for all new development
2. **Server Components by Default** - Components are Server Components unless they need interactivity
3. **Client Components When Needed** - Add `"use client"` only for interactivity, browser APIs, or React hooks

## Component Patterns

### Server Components (Default)
- Fetch data directly in components using `async/await`
- Access backend resources directly
- Keep sensitive data on the server
- Reduce client-side JavaScript

### Client Components
- Add `"use client"` directive at the top
- Use for: `useState`, `useEffect`, event handlers, browser APIs
- Keep them small and at the leaves of the component tree

## File Conventions

```
app/
├── layout.tsx        # Root layout (required)
├── page.tsx          # Home page
├── loading.tsx       # Loading UI
├── error.tsx         # Error boundary
├── not-found.tsx     # 404 page
└── [slug]/
    └── page.tsx      # Dynamic route
```

## Data Fetching

### Server Components
```typescript
async function Page() {
  const data = await fetch('https://api.example.com/data', {
    cache: 'force-cache',      // Static (default)
    // cache: 'no-store',      // Dynamic
    // next: { revalidate: 60 } // ISR
  })
  return <div>{/* render data */}</div>
}
```

### Server Actions
```typescript
'use server'

export async function submitForm(formData: FormData) {
  // Validate and process data
  // Interact with database
  // Revalidate cache if needed
  revalidatePath('/path')
}
```

## Routing Patterns

- **Parallel Routes**: `@folder` for simultaneous route rendering
- **Intercepting Routes**: `(.)folder` for modal patterns
- **Route Groups**: `(group)` for organization without affecting URL
- **Dynamic Segments**: `[param]`, `[...catchAll]`, `[[...optional]]`

## Performance Best Practices

1. Use `next/image` for optimized images
2. Use `next/font` for font optimization
3. Implement proper loading states with `loading.tsx`
4. Use `Suspense` boundaries strategically
5. Leverage streaming with `loading.tsx` and Suspense

## Reference Documentation

For detailed API references and advanced patterns, consult:
- Next.js Documentation: https://nextjs.org/docs/llms-full.txt
