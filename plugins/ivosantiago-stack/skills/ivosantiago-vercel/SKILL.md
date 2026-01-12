---
name: ivosantiago-vercel
description: Expert guidance for Vercel deployment and configuration. Use when deploying applications, configuring environments, setting up domains, or optimizing for Vercel's platform. Covers deployment, edge functions, and performance optimization.
---

# Vercel Deployment Guide

## Project Configuration

### vercel.json
```json
{
  "buildCommand": "next build",
  "devCommand": "next dev",
  "installCommand": "npm install",
  "framework": "nextjs",
  "regions": ["iad1"],
  "functions": {
    "app/api/**/*.ts": {
      "maxDuration": 30
    }
  }
}
```

### Environment Variables

```bash
# Production variables (Vercel Dashboard)
DATABASE_URL=postgresql://...
AUTH_SECRET=your-secret-key
NEXT_PUBLIC_APP_URL=https://your-app.vercel.app

# Local development (.env.local - never commit)
DATABASE_URL=postgresql://localhost:5432/mydb
AUTH_SECRET=dev-secret-minimum-32-characters-long
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

## Deployment Strategies

### Git Integration (Recommended)
```bash
# Vercel automatically deploys on push
git push origin main          # Production deployment
git push origin feature/xyz   # Preview deployment
```

### CLI Deployment
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy to preview
vercel

# Deploy to production
vercel --prod

# Pull environment variables
vercel env pull .env.local
```

## Edge Functions

### Edge Runtime
```typescript
// app/api/edge-route/route.ts
export const runtime = 'edge'

export async function GET(request: Request) {
  // Runs at the edge, closer to users
  return new Response('Hello from the edge!')
}
```

### Middleware (Edge by Default)
```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Runs on edge for every matched request
  const country = request.geo?.country || 'US'
  
  // Add custom header
  const response = NextResponse.next()
  response.headers.set('x-country', country)
  
  return response
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
}
```

## Database Connections

### Connection Pooling with Neon
```typescript
// For serverless - use connection pooling
import { Pool, neonConfig } from '@neondatabase/serverless'
import { drizzle } from 'drizzle-orm/neon-serverless'
import ws from 'ws'

neonConfig.webSocketConstructor = ws

const pool = new Pool({ connectionString: process.env.DATABASE_URL })
export const db = drizzle(pool)
```

### Vercel Postgres
```typescript
import { sql } from '@vercel/postgres'
import { drizzle } from 'drizzle-orm/vercel-postgres'

export const db = drizzle(sql)
```

## Caching Strategies

### Static Generation (Default)
```typescript
// Automatically cached at build time
export default async function Page() {
  const data = await fetch('https://api.example.com/data')
  return <div>{/* render */}</div>
}
```

### Incremental Static Regeneration
```typescript
// Revalidate every 60 seconds
export const revalidate = 60

export default async function Page() {
  const data = await fetch('https://api.example.com/data')
  return <div>{/* render */}</div>
}
```

### On-Demand Revalidation
```typescript
// app/api/revalidate/route.ts
import { revalidatePath, revalidateTag } from 'next/cache'

export async function POST(request: Request) {
  const { path, tag, secret } = await request.json()
  
  if (secret !== process.env.REVALIDATION_SECRET) {
    return Response.json({ error: 'Invalid secret' }, { status: 401 })
  }
  
  if (path) revalidatePath(path)
  if (tag) revalidateTag(tag)
  
  return Response.json({ revalidated: true })
}
```

## Image Optimization

```typescript
// next.config.ts
export default {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'images.example.com',
      },
    ],
  },
}
```

```tsx
import Image from 'next/image'

<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority // Load immediately for LCP
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
/>
```

## Analytics & Monitoring

```typescript
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react'
import { SpeedInsights } from '@vercel/speed-insights/next'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
        <SpeedInsights />
      </body>
    </html>
  )
}
```

## Domain Configuration

```bash
# Add custom domain via CLI
vercel domains add example.com

# Or configure in vercel.json
{
  "redirects": [
    { "source": "/old-path", "destination": "/new-path", "permanent": true }
  ],
  "rewrites": [
    { "source": "/api/:path*", "destination": "https://api.example.com/:path*" }
  ]
}
```

## Performance Checklist

- [ ] Use ISR for frequently updated content
- [ ] Implement proper caching headers
- [ ] Optimize images with next/image
- [ ] Use Edge Functions for latency-sensitive routes
- [ ] Configure connection pooling for databases
- [ ] Enable Vercel Analytics
- [ ] Set appropriate function timeouts
- [ ] Use preview deployments for testing

## Reference Documentation

For detailed deployment guides, consult:
- Vercel Documentation: https://vercel.com/docs/llms-full.txt

