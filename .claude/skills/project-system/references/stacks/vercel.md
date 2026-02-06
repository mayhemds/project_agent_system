# Vercel Reference

## Project Setup

```bash
# Install Vercel CLI
npm i -g vercel

# Link to existing project or create new
vercel link

# Deploy preview
vercel

# Deploy to production
vercel --prod
```

## Environment Variables

Set via dashboard (Settings > Environment Variables) or CLI:

```bash
# Add variable for all environments
vercel env add STRIPE_SECRET_KEY

# Add for specific environment
vercel env add DATABASE_URL production

# Pull env vars to local .env.local
vercel env pull
```

**Scoping:** Variables can target Production, Preview, and/or Development separately. Use this to point preview deployments at staging databases.

**Naming convention:**
- `NEXT_PUBLIC_*` - Exposed to browser (public API keys, URLs)
- Everything else - Server-only (secrets, database URLs, API keys)

## Serverless Functions

Next.js API routes (`app/api/*/route.ts`) deploy as serverless functions automatically.

```ts
// app/api/process/route.ts
export const maxDuration = 30; // seconds (default: 10 on Hobby, 60 on Pro)
export const dynamic = "force-dynamic"; // opt out of caching

export async function POST(request: NextRequest) {
  const body = await request.json();
  const result = await processData(body);
  return NextResponse.json(result);
}
```

**Configuration limits (Pro plan):**
- Max duration: 60s (300s on Enterprise)
- Memory: 1024MB default, configurable up to 3008MB
- Payload size: 4.5MB request body

## Edge Functions

Run on Vercel's Edge Network (faster cold starts, limited Node.js APIs).

```ts
// app/api/geo/route.ts
export const runtime = "edge";

export async function GET(request: NextRequest) {
  const country = request.geo?.country ?? "Unknown";
  return NextResponse.json({ country });
}
```

**Edge middleware** (runs before every matched request):

```ts
// middleware.ts
export const config = { matcher: ["/dashboard/:path*"] };

export function middleware(request: NextRequest) {
  // Runs at the edge, before the request reaches your app
  const token = request.cookies.get("session");
  if (!token) return NextResponse.redirect(new URL("/login", request.url));
  return NextResponse.next();
}
```

**Edge limitations:** No Node.js `fs`, `net`, or native modules. Use `edge` runtime only for lightweight logic (auth checks, redirects, geolocation, A/B testing).

## Cron Jobs

```json
// vercel.json
{
  "crons": [
    {
      "path": "/api/cron/daily-report",
      "schedule": "0 9 * * *"
    },
    {
      "path": "/api/cron/cleanup",
      "schedule": "0 */6 * * *"
    }
  ]
}
```

```ts
// app/api/cron/daily-report/route.ts
export async function GET(request: NextRequest) {
  // Verify the request is from Vercel Cron
  const authHeader = request.headers.get("authorization");
  if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  await generateDailyReport();
  return NextResponse.json({ success: true });
}
```

Set `CRON_SECRET` as an environment variable. Cron jobs are available on Pro and Enterprise plans.

## Caching Strategies

```ts
// Static page (cached at build time, revalidated on demand)
export const revalidate = 3600; // revalidate every hour

// Force dynamic rendering (no cache)
export const dynamic = "force-dynamic";

// On-demand revalidation (from server action or route handler)
import { revalidatePath, revalidateTag } from "next/cache";

revalidatePath("/dashboard");       // Revalidate specific path
revalidateTag("projects");          // Revalidate by cache tag

// Fetch with cache tags
const data = await fetch(url, {
  next: { tags: ["projects"], revalidate: 600 },
});
```

**Cache headers for route handlers:**

```ts
export async function GET() {
  const data = await getData();
  return NextResponse.json(data, {
    headers: {
      "Cache-Control": "public, s-maxage=60, stale-while-revalidate=300",
    },
  });
}
```

## Domains and DNS

Configure via dashboard (Settings > Domains) or CLI:

```bash
vercel domains add example.com
```

**DNS setup for custom domains:**
- Apex domain (`example.com`): A record pointing to `76.76.21.21`
- Subdomain (`www.example.com`): CNAME record pointing to `cname.vercel-dns.com`

Vercel automatically provisions and renews SSL certificates.

## Preview Deployments

Every git push to a non-production branch creates a preview deployment with a unique URL.

- URL pattern: `project-name-git-branch-name-team.vercel.app`
- Each pull request gets a deployment comment with preview link
- Preview deployments use Preview environment variables
- Protect previews with Vercel Authentication (Settings > General > Password Protection)

**Preview branch environment variables** allow using staging databases and test API keys for non-production deployments.

## Analytics and Speed Insights

```tsx
// app/layout.tsx
import { Analytics } from "@vercel/analytics/react";
import { SpeedInsights } from "@vercel/speed-insights/next";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        {children}
        <Analytics />
        <SpeedInsights />
      </body>
    </html>
  );
}
```

```bash
npm install @vercel/analytics @vercel/speed-insights
```

**Analytics** tracks page views and custom events. **Speed Insights** measures Core Web Vitals (LCP, FID, CLS) from real user data.

```ts
// Track custom events
import { track } from "@vercel/analytics";
track("project_created", { plan: "pro" });
```

## Vercel Configuration

```json
// vercel.json
{
  "framework": "nextjs",
  "buildCommand": "next build",
  "installCommand": "npm ci",
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        { "key": "Access-Control-Allow-Origin", "value": "https://example.com" }
      ]
    }
  ],
  "redirects": [
    { "source": "/blog/:slug", "destination": "/posts/:slug", "permanent": true }
  ],
  "rewrites": [
    { "source": "/docs/:path*", "destination": "https://docs.example.com/:path*" }
  ]
}
```
