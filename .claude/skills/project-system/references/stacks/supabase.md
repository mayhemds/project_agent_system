# Supabase Reference

## Client Setup

Two clients: one for server-side (service role or cookie-based auth), one for browser.

```ts
// lib/supabase/server.ts - Server client (Server Components, Server Actions, Route Handlers)
import { createServerClient } from "@supabase/ssr";
import { cookies } from "next/headers";

export async function createClient() {
  const cookieStore = await cookies();
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() { return cookieStore.getAll(); },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value, options }) =>
            cookieStore.set(name, value, options)
          );
        },
      },
    }
  );
}

// lib/supabase/browser.ts - Browser client (Client Components)
import { createBrowserClient } from "@supabase/ssr";

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  );
}
```

## Authentication

**Always use `getUser()`, never `getSession()` alone.** `getSession` reads from the JWT without server validation. `getUser` makes a round-trip to Supabase Auth to verify.

```ts
// Correct: validates with Supabase Auth server
const { data: { user }, error } = await supabase.auth.getUser();
if (!user) redirect("/login");

// Wrong: trusts JWT without verification - can be spoofed
const { data: { session } } = await supabase.auth.getSession(); // DO NOT USE for authorization
```

### Sign Up / Sign In

```ts
// Email + password sign up
const { data, error } = await supabase.auth.signUp({
  email: "user@example.com",
  password: "secure-password",
});

// Email + password sign in
const { data, error } = await supabase.auth.signInWithPassword({
  email, password,
});

// OAuth (Google, GitHub, etc.)
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: "google",
  options: { redirectTo: `${origin}/auth/callback` },
});

// Sign out
await supabase.auth.signOut();
```

### Auth Callback Route

```ts
// app/auth/callback/route.ts
import { NextResponse } from "next/server";
import { createClient } from "@/lib/supabase/server";

export async function GET(request: Request) {
  const { searchParams, origin } = new URL(request.url);
  const code = searchParams.get("code");
  const next = searchParams.get("next") ?? "/dashboard";

  if (code) {
    const supabase = await createClient();
    const { error } = await supabase.auth.exchangeCodeForSession(code);
    if (!error) return NextResponse.redirect(`${origin}${next}`);
  }

  return NextResponse.redirect(`${origin}/auth/error`);
}
```

## Middleware (Auth Refresh)

```ts
// middleware.ts
import { createServerClient } from "@supabase/ssr";
import { NextResponse, type NextRequest } from "next/server";

export async function middleware(request: NextRequest) {
  let supabaseResponse = NextResponse.next({ request });
  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() { return request.cookies.getAll(); },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value, options }) => {
            request.cookies.set(name, value);
            supabaseResponse.cookies.set(name, value, options);
          });
        },
      },
    }
  );

  // Refresh session - do not remove this line
  const { data: { user } } = await supabase.auth.getUser();

  // Protect routes
  if (!user && request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return supabaseResponse;
}

export const config = {
  matcher: ["/((?!_next/static|_next/image|favicon.ico|.*\\.(?:svg|png|jpg)$).*)"],
};
```

## Row Level Security (RLS)

Always enable RLS on every table. Without policies, no rows are accessible.

```sql
-- Enable RLS
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Users can read their own projects
CREATE POLICY "Users read own projects" ON projects
  FOR SELECT USING (auth.uid() = user_id);

-- Users can insert their own projects
CREATE POLICY "Users insert own projects" ON projects
  FOR INSERT WITH CHECK (auth.uid() = user_id);

-- Users can update their own projects
CREATE POLICY "Users update own projects" ON projects
  FOR UPDATE USING (auth.uid() = user_id);

-- Users can delete their own projects
CREATE POLICY "Users delete own projects" ON projects
  FOR DELETE USING (auth.uid() = user_id);

-- Public read access (e.g., published content)
CREATE POLICY "Public read published" ON posts
  FOR SELECT USING (published = true);
```

## Database Queries

```ts
const supabase = await createClient();

// Select with filters
const { data, error } = await supabase
  .from("projects")
  .select("id, name, created_at, tasks(id, title, status)")
  .eq("user_id", user.id)
  .order("created_at", { ascending: false })
  .limit(20);

// Insert
const { data, error } = await supabase
  .from("projects")
  .insert({ name: "New Project", user_id: user.id })
  .select()
  .single();

// Update
const { error } = await supabase
  .from("projects")
  .update({ name: "Updated Name" })
  .eq("id", projectId);

// Delete
const { error } = await supabase
  .from("projects")
  .delete()
  .eq("id", projectId);
```

## Storage

```ts
// Upload file
const { data, error } = await supabase.storage
  .from("avatars")
  .upload(`${user.id}/avatar.png`, file, {
    cacheControl: "3600",
    upsert: true,
  });

// Get public URL
const { data } = supabase.storage
  .from("avatars")
  .getPublicUrl(`${user.id}/avatar.png`);

// Download file
const { data, error } = await supabase.storage
  .from("documents")
  .download("report.pdf");
```

## Realtime Subscriptions

```ts
// Client component only
"use client";
import { useEffect } from "react";
import { createClient } from "@/lib/supabase/browser";

function useRealtimeProjects(userId: string) {
  const supabase = createClient();

  useEffect(() => {
    const channel = supabase
      .channel("projects-changes")
      .on("postgres_changes",
        { event: "*", schema: "public", table: "projects", filter: `user_id=eq.${userId}` },
        (payload) => { console.log("Change:", payload); }
      )
      .subscribe();

    return () => { supabase.removeChannel(channel); };
  }, [userId]);
}
```

## Edge Functions

```ts
// supabase/functions/hello/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";

serve(async (req) => {
  const { name } = await req.json();
  return new Response(JSON.stringify({ message: `Hello ${name}` }), {
    headers: { "Content-Type": "application/json" },
  });
});
```

Invoke from client:

```ts
const { data, error } = await supabase.functions.invoke("hello", {
  body: { name: "World" },
});
```
