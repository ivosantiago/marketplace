---
name: ivosantiago-tailwind
description: Expert Tailwind CSS guidance for styling applications. Use when writing styles, creating responsive layouts, implementing dark mode, or building custom design systems. Covers utility patterns, customization, and performance.
---

# Tailwind CSS Best Practices

## Core Concepts

### Utility-First Approach
```tsx
// ✅ Good - Compose utilities
<button className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
  Click me
</button>

// ❌ Avoid - Custom CSS for common patterns
```

### Responsive Design
```tsx
// Mobile-first breakpoints
<div className="w-full md:w-1/2 lg:w-1/3 xl:w-1/4">
  {/* Full width on mobile, half on tablet, etc. */}
</div>

// Breakpoints: sm(640) md(768) lg(1024) xl(1280) 2xl(1536)
```

## Layout Patterns

### Flexbox
```tsx
// Center content
<div className="flex items-center justify-center min-h-screen">
  <Content />
</div>

// Space between
<div className="flex justify-between items-center">
  <Logo />
  <Nav />
</div>

// Responsive flex direction
<div className="flex flex-col md:flex-row gap-4">
  <Sidebar />
  <Main />
</div>
```

### Grid
```tsx
// Basic grid
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map(item => <Card key={item.id} />)}
</div>

// Complex layout
<div className="grid grid-cols-12 gap-4">
  <aside className="col-span-12 md:col-span-3">Sidebar</aside>
  <main className="col-span-12 md:col-span-9">Content</main>
</div>
```

## Spacing System

```tsx
// Padding: p-{size} pt pb pl pr px py
<div className="p-4 md:p-6 lg:p-8">

// Margin: m-{size} mt mb ml mr mx my
<div className="mt-4 mb-8">

// Gap (for flex/grid): gap-{size} gap-x gap-y
<div className="flex gap-4">

// Space between children: space-x space-y
<div className="space-y-4">
```

## Typography

```tsx
// Font size
<h1 className="text-4xl md:text-5xl lg:text-6xl font-bold">

// Font weight: font-thin light normal medium semibold bold extrabold black

// Line height: leading-none tight snug normal relaxed loose

// Text color with opacity
<p className="text-gray-600 dark:text-gray-300">
```

## Dark Mode

```tsx
// Enable in tailwind.config.ts
export default {
  darkMode: 'class', // or 'media'
}

// Usage
<div className="bg-white dark:bg-gray-900 text-black dark:text-white">
  <button className="bg-blue-500 dark:bg-blue-600 hover:bg-blue-600 dark:hover:bg-blue-700">
    Button
  </button>
</div>
```

## Custom Configuration

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

export default {
  content: ['./app/**/*.{ts,tsx}', './components/**/*.{ts,tsx}'],
  theme: {
    extend: {
      colors: {
        brand: {
          50: '#f0f9ff',
          500: '#0ea5e9',
          900: '#0c4a6e',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
      },
    },
  },
  plugins: [],
} satisfies Config
```

## Component Patterns

### Using cn() for Conditional Classes
```typescript
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

// Usage
<button className={cn(
  "px-4 py-2 rounded",
  variant === 'primary' && "bg-blue-500 text-white",
  variant === 'secondary' && "bg-gray-200 text-gray-800",
  disabled && "opacity-50 cursor-not-allowed"
)}>
```

## Best Practices

1. **Use Design Tokens** - Stick to Tailwind's spacing/color scales
2. **Extract Components** - Not utilities; reuse through React components
3. **Mobile First** - Start with mobile styles, add breakpoints for larger
4. **Avoid Arbitrary Values** - Use `[]` sparingly, prefer config extension
5. **Purge Unused** - Ensure content paths are configured correctly

