# Next.js Reference

## Project Structure (App Router)

```
app/
├── layout.tsx          # Root layout (wraps all pages)
├── page.tsx            # Home route (/)
├── globals.css         # Global styles
├── (auth)/             # Route group (no URL segment)
│   ├── login/page.tsx  # /login
│   └── signup/page.tsx # /signup
├── dashboard/
│   ├── layout.tsx      # Nested layout for /dashboard/*
│   ├── page.tsx        # /dashboard
│   └── [id]/page.tsx   # /dashboard/:id (dynamic segment)
├── api/
│   └── webhooks/
│       └── route.ts    # API route handler
├── error.tsx           # Error boundary
├── loading.tsx         # Loading UI (Suspense boundary)
└── not-found.tsx       # 404 page
```

## Server vs Client Components

Server components are the default. Use `"use client"` only when needed.

```tsx
// Server component (default) - can fetch data, access server resources
export default async function DashboardPage() {
  const data = await getProjects(); // Direct DB/API call
  return <ProjectList projects={data} />;
}

// Client component - needed for interactivity, hooks, browser APIs
"use client";
import { useState } from "react";

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Use `"use client"` when you need:** useState, useEffect, onClick/onChange handlers, browser APIs (localStorage, IntersectionObserver), third-party client libraries.

**Keep server by default when:** fetching data, accessing backend resources, rendering static content, keeping sensitive logic server-side.

## Layouts

```tsx
// app/layout.tsx - Root layout (required, wraps everything)
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}

// app/dashboard/layout.tsx - Nested layout (persists across child navigations)
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex">
      <Sidebar />
      <main className="flex-1">{children}</main>
    </div>
  );
}
```

## Metadata API

```tsx
// Static metadata
export const metadata: Metadata = {
  title: "Page Title",
  description: "Page description for search engines",
};

// Dynamic metadata (based on params, data)
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const project = await getProject(params.id);
  return {
    title: project.name,
    description: project.summary,
    openGraph: { images: [project.imageUrl] },
  };
}
```

## Server Actions

```tsx
// Inline in server component
export default function CreateForm() {
  async function createProject(formData: FormData) {
    "use server";
    const name = formData.get("name") as string;
    await db.insert(projects).values({ name });
    revalidatePath("/projects");
    redirect("/projects");
  }

  return (
    <form action={createProject}>
      <input name="name" required />
      <button type="submit">Create</button>
    </form>
  );
}

// Separate file for reuse
// app/actions/projects.ts
"use server";
export async function deleteProject(id: string) {
  await db.delete(projects).where(eq(projects.id, id));
  revalidatePath("/projects");
}
```

## Data Fetching

```tsx
// Server component - fetch directly (auto-deduplicated)
async function ProjectPage({ params }: { params: { id: string } }) {
  const project = await db.query.projects.findFirst({
    where: eq(projects.id, params.id),
  });
  if (!project) notFound();
  return <ProjectDetail project={project} />;
}

// Parallel data fetching
async function Dashboard() {
  const [projects, stats] = await Promise.all([
    getProjects(),
    getStats(),
  ]);
  return <>{/* render both */}</>;
}
```

## Route Handlers (API Routes)

```tsx
// app/api/projects/route.ts
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  const limit = searchParams.get("limit") ?? "10";
  const data = await getProjects(Number(limit));
  return NextResponse.json(data);
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  const result = await createProject(body);
  return NextResponse.json(result, { status: 201 });
}
```

## Middleware

```tsx
// middleware.ts (project root)
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  // Example: redirect unauthenticated users
  const token = request.cookies.get("session");
  if (!token && request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }
  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*", "/api/:path*"],
};
```

## Image Optimization

```tsx
import Image from "next/image";

// Local image (auto width/height from import)
import heroImg from "@/public/hero.png";
<Image src={heroImg} alt="Hero section" priority />

// Remote image (must specify dimensions)
<Image src="https://example.com/photo.jpg" alt="User photo" width={400} height={300} />

// Fill container (parent must be position: relative)
<div className="relative h-64 w-full">
  <Image src={url} alt="Cover" fill className="object-cover" />
</div>
```

Configure remote image domains in `next.config.ts`:

```ts
const nextConfig = {
  images: {
    remotePatterns: [
      { protocol: "https", hostname: "example.com" },
    ],
  },
};
```
