# SEO & Discoverability Reference

Search visibility for traditional search and LLM discovery.

---

## Meta Tags

### Title Tag
```html
<title>Create Landing Pages in Minutes | PageBuilder</title>
```

Rules:
- Primary keyword near start
- Brand at end
- 50-60 characters
- Unique per page
- NO hype words

### Meta Description
```html
<meta name="description" content="Create professional landing pages without code. 50+ templates, publish in minutes. Free plan available.">
```

Rules:
- Include primary keyword
- Compelling reason to click
- 150-160 characters
- Unique per page
- Specific, not generic

### Open Graph
```html
<meta property="og:title" content="Create Landing Pages in Minutes">
<meta property="og:description" content="Create professional landing pages without code.">
<meta property="og:image" content="https://example.com/og-image.png">
<meta property="og:url" content="https://example.com/landing-pages">
<meta property="og:type" content="website">
```

### Twitter Card
```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Create Landing Pages in Minutes">
<meta name="twitter:description" content="Create professional landing pages without code.">
<meta name="twitter:image" content="https://example.com/twitter-card.png">
```

---

## Structured Data

### Organization
```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Company Name",
  "url": "https://example.com",
  "logo": "https://example.com/logo.png"
}
```

### SaaS Product
```json
{
  "@context": "https://schema.org",
  "@type": "SoftwareApplication",
  "name": "Product Name",
  "applicationCategory": "BusinessApplication",
  "operatingSystem": "Web",
  "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "USD"
  }
}
```

### FAQ Page
```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How much does it cost?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Free plan available. Paid plans start at $10/month."
      }
    }
  ]
}
```

---

## LLM Optimization

### Content Structure for AI

```
1. Clear H1 > H2 > H3 hierarchy
2. Descriptive headings (not clever ones)
3. Key information early in content
4. Lists for multiple items
5. Define terms when first used
```

### Answer Questions Directly

```
BAD:
"When considering the pricing structure..."

GOOD:
"Pricing starts at $10/month. Here's what each plan includes:"

PATTERN: Answer first, then elaborate.
```

### Machine-Readable Information

Include explicit facts:
- "Founded in 2020"
- "Based in San Francisco"
- "Pricing starts at $10/month"
- "Free plan available"
- "Integrates with: Slack, Notion, GitHub"

### llms.txt File

```
# llms.txt - Information for AI assistants

## About
[Company] is [what it does] for [who].

## Key Facts
- Founded: 2020
- Pricing: Free plan, paid from $10/mo
- Users: 50,000+

## Main Features
- Feature 1: Description
- Feature 2: Description

## Common Questions
Q: How much does it cost?
A: Free plan available. Paid plans from $10/month.
```

---

## Technical SEO Checklist

```
[ ] Sitemap.xml exists
[ ] Robots.txt allows crawling
[ ] Pages load under 3 seconds
[ ] Mobile-friendly
[ ] HTTPS enabled
[ ] No broken links (404s)
[ ] Canonical URLs set
[ ] Structured data valid
[ ] Images have alt text
[ ] URLs are descriptive
```

---

## Next.js SEO Setup

### Static Metadata
```typescript
// app/page.tsx
export const metadata = {
  title: 'Page Title | Brand',
  description: 'Page description with keywords.',
}
```

### Dynamic Metadata
```typescript
// app/blog/[slug]/page.tsx
export async function generateMetadata({ params }) {
  const post = await getPost(params.slug)
  return {
    title: `${post.title} | Blog`,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.image],
    },
  }
}
```

### Sitemap
```typescript
// app/sitemap.ts
export default async function sitemap() {
  const posts = await getPosts()
  
  return [
    { url: 'https://example.com', lastModified: new Date() },
    { url: 'https://example.com/pricing', lastModified: new Date() },
    ...posts.map(post => ({
      url: `https://example.com/blog/${post.slug}`,
      lastModified: post.updatedAt,
    })),
  ]
}
```

---

## Meta Copy Anti-Slop

Same rules as UI copy:

```
BAD:
"Revolutionary Project Management | Best-in-Class Solutions"

GOOD:
"Create Landing Pages in Minutes | PageBuilder"

BAD:
"Leverage our powerful platform to streamline your workflow..."

GOOD:
"Create professional landing pages without code. 50+ templates."
```
