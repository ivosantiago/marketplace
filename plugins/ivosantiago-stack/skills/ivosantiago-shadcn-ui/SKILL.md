---
name: ivosantiago-shadcn-ui
description: Expert guidance for shadcn/ui component library. Use when building UI components, implementing forms, dialogs, tables, or any user interface elements. Covers component usage, customization, and accessibility patterns.
---

# shadcn/ui Component Library

## Overview

shadcn/ui is NOT a component library you install. It's a collection of reusable components you copy into your project and customize. Components are built on Radix UI primitives and styled with Tailwind CSS.

## Installation

```bash
# Initialize shadcn/ui
npx shadcn@latest init

# Add components as needed
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add form
npx shadcn@latest add dialog
```

## Core Components

### Button

```tsx
import { Button } from "@/components/ui/button"

<Button variant="default">Default</Button>
<Button variant="destructive">Destructive</Button>
<Button variant="outline">Outline</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>

<Button size="default">Default</Button>
<Button size="sm">Small</Button>
<Button size="lg">Large</Button>
<Button size="icon"><Icon /></Button>
```

### Card

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

<Card>
  <CardHeader>
    <CardTitle>Card Title</CardTitle>
    <CardDescription>Card description goes here</CardDescription>
  </CardHeader>
  <CardContent>
    <p>Card content</p>
  </CardContent>
  <CardFooter>
    <Button>Action</Button>
  </CardFooter>
</Card>;
```

### Dialog

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";

<Dialog>
  <DialogTrigger asChild>
    <Button>Open Dialog</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Dialog Title</DialogTitle>
      <DialogDescription>Dialog description</DialogDescription>
    </DialogHeader>
    <div>Dialog content here</div>
    <DialogFooter>
      <Button type="submit">Save</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>;
```

## Forms with React Hook Form + Zod

```tsx
"use client";

import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";

const formSchema = z.object({
  username: z.string().min(2).max(50),
  email: z.string().email(),
});

export function ProfileForm() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      username: "",
      email: "",
    },
  });

  function onSubmit(values: z.infer<typeof formSchema>) {
    console.log(values);
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
        <FormField
          control={form.control}
          name="username"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Username</FormLabel>
              <FormControl>
                <Input placeholder="johndoe" {...field} />
              </FormControl>
              <FormDescription>Your public display name.</FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />
        <Button type="submit">Submit</Button>
      </form>
    </Form>
  );
}
```

## Data Table

```tsx
import { DataTable } from "@/components/ui/data-table"
import { ColumnDef } from "@tanstack/react-table"

const columns: ColumnDef<User>[] = [
  {
    accessorKey: "name",
    header: "Name",
  },
  {
    accessorKey: "email",
    header: "Email",
  },
  {
    id: "actions",
    cell: ({ row }) => <Actions user={row.original} />,
  },
]

<DataTable columns={columns} data={users} />
```

## Theming

```css
/* globals.css */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    /* ... more CSS variables */
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    /* ... dark mode variables */
  }
}
```

## Composing Components on Top of shadcn

Instead of modifying shadcn components, compose your components on top of them while following shadcn's architecture guidelines for compatibility with tools like [tweakcn.com](https://tweakcn.com/).

### Composition Pattern

```tsx
// ✅ Good - Compose on top of shadcn components
// features/dashboard/components/metric-card.tsx
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { cn } from "@/lib/utils";

export function MetricCard({
  title,
  value,
  className,
}: {
  title: string;
  value: string;
  className?: string;
}) {
  return (
    <Card className={cn("", className)}>
      <CardHeader className="pb-2">
        <CardTitle className="text-sm font-medium text-muted-foreground">
          {title}
        </CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-2xl font-bold">{value}</p>
      </CardContent>
    </Card>
  );
}
```

### Standalone Components with shadcn Architecture

For components that don't use shadcn primitives, follow shadcn's styling patterns:

```tsx
// ✅ Good - Follows shadcn architecture (CSS variables, cn utility)
// features/dashboard/components/stat-badge.tsx
import { cn } from "@/lib/utils";

export function StatBadge({
  label,
  value,
  variant = "default",
  className,
}: {
  label: string;
  value: string;
  variant?: "default" | "success" | "warning";
  className?: string;
}) {
  return (
    <div
      className={cn(
        "inline-flex items-center rounded-md border px-2.5 py-0.5 text-xs font-semibold transition-colors",
        "focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2",
        {
          "border-border bg-background text-foreground": variant === "default",
          "border-green-500/50 bg-green-500/10 text-green-700 dark:text-green-400":
            variant === "success",
          "border-yellow-500/50 bg-yellow-500/10 text-yellow-700 dark:text-yellow-400":
            variant === "warning",
        },
        className
      )}
    >
      <span className="text-muted-foreground mr-1">{label}:</span>
      <span>{value}</span>
    </div>
  );
}
```

### Key Composition Principles

1. **Compose, Don't Modify** - Build on top of shadcn components, never edit `components/ui/` files
2. **Follow shadcn Patterns** - Use `cn()` utility, CSS variables, and same styling conventions
3. **Preserve Compatibility** - Keep `components/ui/` pristine for tools like [tweakcn.com](https://tweakcn.com/)
4. **Use Theme Variables** - Always use Tailwind CSS variables (`bg-background`, `text-foreground`, `border-border`, etc.)
5. **Place in Features** - Custom components belong in `features/{feature}/components/`, not `components/ui/`

## Best Practices

1. **Copy, Don't Import** - Components live in your codebase, customize freely
2. **Use the CLI** - `npx shadcn@latest add` for new components
3. **Compose, Don't Modify** - Build components on top of shadcn primitives, never edit `components/ui/` files directly
4. **Follow shadcn Architecture** - Use `cn()` utility, CSS variables, same styling patterns, and component structure conventions
5. **Extend via Composition** - Create wrapper/composite components that use shadcn components as building blocks
6. **Accessibility First** - Built on Radix, maintains a11y out of box. When composing, preserve accessibility features
7. **Consistent Styling** - Use CSS variables for theming to maintain compatibility with shadcn's theming system
8. **Preserve `components/ui/` Pristine** - Keep `components/ui/` exclusively for shadcn components to maintain compatibility with ecosystem tools like [tweakcn.com](https://tweakcn.com/)
9. **Feature-Based Components** - Place composed/custom components in `features/{feature}/components/` or shared `components/` (outside `ui/`)
10. **Use Tailwind Theme Variables** - Always use Tailwind CSS variables (`bg-background`, `text-foreground`, `border-border`, `ring-ring`, etc.) instead of hardcoded colors

## Reference Documentation

For component catalog and detailed docs, consult:

- shadcn/ui Documentation: https://ui.shadcn.com/llms.txt
