# Astro Reference

## Project Structure

```
src/
  pages/index.astro          # File-based routing
  pages/blog/[slug].astro    # Dynamic route
  layouts/BaseLayout.astro   # Page shells
  components/Header.astro    # Zero-JS by default
  components/Counter.tsx     # Interactive island
  content/config.ts          # Collection schemas
  content/blog/*.md          # Content files
public/favicon.svg
astro.config.mjs
```

## Pages

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import { getCollection } from 'astro:content';
const posts = (await getCollection('blog')).sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf());
---
<BaseLayout title="Home">
  <ul>
    {posts.map((post) => (
      <li><a href={`/blog/${post.slug}`}>{post.data.title}</a></li>
    ))}
  </ul>
</BaseLayout>
```

## Layouts

```astro
---
interface Props { title: string; description?: string }
const { title, description = 'Default' } = Astro.props;
---
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="description" content={description} />
  <title>{title}</title>
</head>
<body><slot /></body>
</html>
```

## Content Collections

```ts
// src/content/config.ts
import { defineCollection, z } from 'astro:content';
const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(), date: z.date(), description: z.string(),
    tags: z.array(z.string()).default([]), draft: z.boolean().default(false),
  }),
});
export const collections = { blog };
```

```astro
---
// src/pages/blog/[slug].astro
import { getCollection } from 'astro:content';
export async function getStaticPaths() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  return posts.map((post) => ({ params: { slug: post.slug }, props: { post } }));
}
const { Content } = await Astro.props.post.render();
---
<Content />
```

## Islands Architecture (Client Directives)

```astro
---
import Counter from '../components/Counter.tsx';
import Chart from '../components/Chart.tsx';
---
<Counter client:load />           <!-- Hydrate immediately -->
<Chart client:visible />          <!-- Hydrate when scrolled into view -->
<Search client:idle />            <!-- Hydrate on requestIdleCallback -->
<Menu client:media="(max-width: 768px)" />  <!-- Hydrate on media match -->
```

## SSG vs SSR vs Hybrid

```ts
// astro.config.mjs -- output: 'static' (default) | 'server' | 'hybrid'
export default defineConfig({ output: 'static' });
```

```astro
---
// Opt into SSR in hybrid mode
export const prerender = false;
const cookie = Astro.cookies.get('session');
---
```

## Integrations

```ts
// astro.config.mjs
import react from '@astrojs/react';
import tailwind from '@astrojs/tailwind';
import sitemap from '@astrojs/sitemap';
export default defineConfig({
  site: 'https://example.com',
  integrations: [react(), tailwind(), sitemap()],
});
```

## Image Optimization, Data Fetching, SEO

```astro
---
import { Image } from 'astro:assets';
import hero from '../assets/hero.jpg';
const data = await fetch('https://api.example.com/data').then(r => r.json());
const apiKey = import.meta.env.API_KEY;          // Server only
const publicId = import.meta.env.PUBLIC_APP_ID;  // Client accessible
---
<Image src={hero} alt="Hero" width={1200} />
```

```astro
---
// SEO component pattern
const { title, description } = Astro.props;
---
<title>{title}</title>
<meta name="description" content={description} />
<link rel="canonical" href={Astro.url.href} />
<meta property="og:title" content={title} />
```

## Key Conventions

- Default to zero JS: Astro components for static content, islands for interactivity
- Prefer `client:visible` over `client:load` unless interaction is above the fold
- Content collections for structured content (blog, docs, products)
- `getStaticPaths` for dynamic routes in SSG mode
- Prefix public env vars with `PUBLIC_`
- Use `<Image>` component for automatic optimization and modern formats
