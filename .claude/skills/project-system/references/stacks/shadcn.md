# shadcn/ui Reference

## Overview

shadcn/ui is a collection of re-usable components copied into your project (not installed as a dependency). You own the code and customize it directly.

## Installation

```bash
# Initialize shadcn/ui in a Next.js project
npx shadcn@latest init

# Add specific components
npx shadcn@latest add button card dialog input label table tabs toast
```

Components are placed in `components/ui/`. The `cn` utility is created at `lib/utils.ts`.

## Component Usage

```tsx
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";

<Button variant="default">Save</Button>
<Button variant="secondary">Cancel</Button>
<Button variant="outline">Edit</Button>
<Button variant="ghost">Menu</Button>
<Button variant="destructive">Delete</Button>
<Button variant="link">Learn more</Button>

<Button size="sm">Small</Button>
<Button size="default">Default</Button>
<Button size="lg">Large</Button>
<Button size="icon"><TrashIcon className="h-4 w-4" /></Button>
```

## Theming with CSS Variables

All colors are defined as CSS variables in `app/globals.css`. Edit these to change the entire theme.

```css
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 240 10% 3.9%;
    --primary: 240 5.9% 10%;
    --primary-foreground: 0 0% 98%;
    --secondary: 240 4.8% 95.9%;
    --muted: 240 4.8% 95.9%;
    --muted-foreground: 240 3.8% 46.1%;
    --destructive: 0 84.2% 60.2%;
    --border: 240 5.9% 90%;
    --ring: 240 5.9% 10%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 240 10% 3.9%;
    --foreground: 0 0% 98%;
    /* ... dark variants */
  }
}
```

## Anti-Slop Component Rules

These conventions keep the UI clean and consistent.

**Buttons:**
- No gradient backgrounds. Use flat `variant="default"` with solid colors.
- No box-shadow on default buttons. Shadow belongs on cards and dialogs.
- Consistent border-radius: use `--radius` variable, do not mix rounded styles.
- Destructive actions use `variant="destructive"`, not red custom styles.

**Cards:**
- One shadow level: `shadow-sm`. No layered shadows, no `shadow-lg` on cards.
- No decorative borders or colored left-borders unless conveying status.
- Consistent padding: `p-6` is the standard card padding.

**Modals/Dialogs:**
- Use modals sparingly. Prefer inline editing, expandable sections, or undo toasts.
- Confirmation dialogs only for destructive, irreversible actions.
- Never use a modal for a single input field. Use inline editing instead.

**Toasts:**
- Use for transient feedback: "Saved", "Copied", "Deleted (Undo)".
- Include undo action for destructive operations when possible.
- Do not use toasts for errors that require user action (use inline error messages).

## Forms with react-hook-form

shadcn/ui provides form primitives that integrate with react-hook-form and Zod.

```tsx
"use client";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { Form, FormControl, FormDescription, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";

const schema = z.object({
  name: z.string().min(1, "Name is required").max(100),
  email: z.string().email("Enter a valid email address"),
});

type FormValues = z.infer<typeof schema>;

export function ProfileForm() {
  const form = useForm<FormValues>({
    resolver: zodResolver(schema),
    defaultValues: { name: "", email: "" },
  });

  async function onSubmit(values: FormValues) {
    await updateProfile(values);
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
        <FormField
          control={form.control}
          name="name"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Name</FormLabel>
              <FormControl>
                <Input placeholder="Your name" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input type="email" placeholder="you@example.com" {...field} />
              </FormControl>
              <FormDescription>Used for account notifications.</FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />
        <Button type="submit" disabled={form.formState.isSubmitting}>
          {form.formState.isSubmitting ? "Saving..." : "Save"}
        </Button>
      </form>
    </Form>
  );
}
```

## Data Table

```tsx
import { ColumnDef } from "@tanstack/react-table";
import { DataTable } from "@/components/ui/data-table";

const columns: ColumnDef<Project>[] = [
  { accessorKey: "name", header: "Name" },
  { accessorKey: "status", header: "Status",
    cell: ({ row }) => <Badge variant={row.original.status === "active" ? "default" : "secondary"}>{row.original.status}</Badge>,
  },
  { accessorKey: "createdAt", header: "Created",
    cell: ({ row }) => formatDate(row.original.createdAt),
  },
  { id: "actions",
    cell: ({ row }) => <ProjectActions project={row.original} />,
  },
];

<DataTable columns={columns} data={projects} />
```

## Common Component Patterns

```tsx
// Dialog (use only for meaningful interactions)
<Dialog>
  <DialogTrigger asChild>
    <Button variant="outline">Edit Profile</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Edit Profile</DialogTitle>
      <DialogDescription>Update your account details.</DialogDescription>
    </DialogHeader>
    {/* form content */}
    <DialogFooter>
      <Button variant="outline">Cancel</Button>
      <Button>Save</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>

// Dropdown menu
<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="ghost" size="icon"><MoreHorizontal className="h-4 w-4" /></Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent align="end">
    <DropdownMenuItem>Edit</DropdownMenuItem>
    <DropdownMenuItem>Duplicate</DropdownMenuItem>
    <DropdownMenuSeparator />
    <DropdownMenuItem className="text-destructive">Delete</DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>

// Skeleton loading
<div className="space-y-3">
  <Skeleton className="h-8 w-[200px]" />
  <Skeleton className="h-4 w-full" />
  <Skeleton className="h-4 w-[80%]" />
</div>
```
