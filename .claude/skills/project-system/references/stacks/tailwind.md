# Tailwind CSS Reference

## Utility Conventions

Tailwind applies styles through utility classes. Compose them directly on elements instead of writing custom CSS.

```tsx
// Layout
<div className="flex items-center justify-between gap-4">
<div className="grid grid-cols-3 gap-6">
<div className="relative h-full w-full">

// Sizing
<div className="h-10 w-10">        {/* 40px x 40px */}
<div className="h-screen w-full">  {/* viewport height, full width */}
<div className="max-w-2xl mx-auto"> {/* centered container, max 672px */}

// Typography
<h1 className="text-2xl font-semibold tracking-tight">
<p className="text-sm text-muted-foreground leading-relaxed">
```

## Spacing Scale

The default scale uses 4px increments: `1` = 4px, `2` = 8px, `4` = 16px, `6` = 24px, `8` = 32px, `12` = 48px, `16` = 64px.

```tsx
// Padding
<div className="p-4">       {/* 16px all sides */}
<div className="px-6 py-3"> {/* 24px horizontal, 12px vertical */}

// Margin
<div className="mt-8">      {/* 32px top margin */}
<div className="space-y-4">  {/* 16px vertical gap between children */}

// Gap (flexbox/grid)
<div className="flex gap-2"> {/* 8px gap between flex items */}
```

## Responsive Design (Mobile-First)

Breakpoints apply from the specified width upward. Write base styles for mobile, layer on larger screen overrides.

```tsx
// Mobile: stack. Tablet+: side by side. Desktop: 3 columns.
<div className="flex flex-col md:flex-row lg:grid lg:grid-cols-3 gap-4">

// Hide on mobile, show on desktop
<nav className="hidden md:flex">

// Different padding per breakpoint
<section className="p-4 md:p-8 lg:p-12">
```

Breakpoints: `sm` (640px), `md` (768px), `lg` (1024px), `xl` (1280px), `2xl` (1536px).

## Color Usage

Use semantic color tokens via CSS variables (integrates with shadcn/ui theming). Avoid hardcoded color values when a semantic token exists.

```tsx
// Semantic colors (preferred - adapt to light/dark mode automatically)
<div className="bg-background text-foreground">
<div className="bg-muted text-muted-foreground">
<div className="bg-primary text-primary-foreground">
<div className="bg-destructive text-destructive-foreground">
<div className="border-border">

// Accent / Card / Popover
<div className="bg-card text-card-foreground">
<div className="bg-accent text-accent-foreground">

// Direct Tailwind colors (use for one-off cases, illustrations, non-themed elements)
<div className="bg-blue-500 text-white">
<div className="text-emerald-600">
```

## Dark Mode

Use the `dark:` variant. With class-based dark mode, toggling `class="dark"` on `<html>` switches themes.

```tsx
// Manual overrides (rarely needed if using semantic tokens)
<div className="bg-white dark:bg-gray-900">
<p className="text-gray-900 dark:text-gray-100">

// Configure in tailwind.config.ts
export default {
  darkMode: "class",
  // ...
};
```

When using shadcn/ui semantic tokens (`bg-background`, `text-foreground`, etc.), dark mode is handled automatically through CSS variables. Manual `dark:` overrides are usually unnecessary.

## Custom Configuration

```ts
// tailwind.config.ts
import type { Config } from "tailwindcss";

const config: Config = {
  content: ["./app/**/*.{ts,tsx}", "./components/**/*.{ts,tsx}"],
  theme: {
    extend: {
      fontFamily: {
        sans: ["var(--font-inter)", "system-ui", "sans-serif"],
        mono: ["var(--font-jetbrains)", "monospace"],
      },
      maxWidth: {
        "content": "680px",
      },
      keyframes: {
        "fade-in": {
          from: { opacity: "0" },
          to: { opacity: "1" },
        },
      },
      animation: {
        "fade-in": "fade-in 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
};

export default config;
```

## Animation Utilities

```tsx
// Built-in transitions
<button className="transition-colors duration-150 hover:bg-primary/90">
<div className="transition-opacity duration-200 opacity-0 group-hover:opacity-100">

// Transform
<div className="hover:scale-105 transition-transform duration-200">

// tailwindcss-animate plugin (used by shadcn/ui)
<div className="animate-in fade-in-0 slide-in-from-bottom-4 duration-300">
<div className="animate-out fade-out-0 duration-200">
```

## Common Patterns

```tsx
// Card
<div className="rounded-lg border bg-card p-6 shadow-sm">

// Truncated text
<p className="truncate">Long text that gets cut off...</p>
<p className="line-clamp-2">Multi-line text clamped to 2 lines...</p>

// Focus ring (accessibility)
<button className="focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring">

// Interactive states
<button className="hover:bg-accent active:scale-[0.98] disabled:pointer-events-none disabled:opacity-50">

// Aspect ratio
<div className="aspect-video overflow-hidden rounded-lg">
  <img className="h-full w-full object-cover" />
</div>

// Scroll area
<div className="h-96 overflow-y-auto overscroll-contain">

// Screen reader only
<span className="sr-only">Close dialog</span>
```

## Pitfalls to Avoid

- **Do not use `@apply` in CSS files** unless extracting a component used 20+ times. Inline utilities are the intended workflow.
- **Do not nest breakpoints inside arbitrary values.** Write `md:w-[300px]`, not `w-[300px_md]`.
- **Avoid string concatenation for class names.** Use `clsx` or `cn` (from shadcn/ui `lib/utils`) for conditional classes:

```tsx
import { cn } from "@/lib/utils";

<div className={cn(
  "rounded-lg border p-4",
  isActive && "border-primary bg-primary/5",
  isDisabled && "opacity-50 pointer-events-none"
)} />
```

- **Do not combine conflicting utilities.** `flex` and `grid` on the same element will not work as expected.
- **Prefer `gap` over margin** for spacing between flex/grid children. Avoid `space-y-*` when `gap` works.
