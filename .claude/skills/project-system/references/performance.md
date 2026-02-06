# Performance Reference

Speed optimization, Core Web Vitals, and performance budgets.

---

## Core Web Vitals Targets

| Metric | Good | Needs Work | Poor |
|--------|------|------------|------|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5-4.0s | > 4.0s |
| INP (Interaction to Next Paint) | < 200ms | 200-500ms | > 500ms |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1-0.25 | > 0.25 |

---

## Performance Budget

| Resource | Budget |
|----------|--------|
| Total page weight | < 500 KB (compressed) |
| JavaScript bundle | < 200 KB (compressed) |
| CSS | < 50 KB |
| Largest image | < 200 KB |
| Fonts | < 100 KB |
| Time to Interactive | < 3.0s |
| First Contentful Paint | < 1.8s |

---

## Frontend Performance

### Bundle Size
- Code split by route (lazy load pages)
- Dynamic import for heavy components
- Tree shake unused code
- Analyze with bundlephobia / webpack-bundle-analyzer
- Avoid importing entire libraries for single functions

### Images
- Use modern formats (WebP, AVIF)
- Responsive images with srcset
- Lazy load below-the-fold images
- Explicit width/height to prevent CLS
- Use CDN for image delivery
- Compress to quality 80-85%

### Rendering
- Server-side render critical content
- Stream HTML where possible
- Avoid render-blocking resources
- Defer non-critical JavaScript
- Preload critical assets (fonts, hero image)

### Fonts
- Use font-display: swap
- Preload critical fonts
- Subset fonts to needed characters
- Limit font weights (2-3 max)
- Consider system font stack

### CSS
- Remove unused CSS
- Inline critical CSS
- Use content-visibility: auto for off-screen content
- Avoid layout thrashing (batch DOM reads/writes)

### React-Specific
- Memoize expensive computations (useMemo)
- Prevent unnecessary re-renders (React.memo, useCallback)
- Virtualize long lists (react-virtual, react-window)
- Use Suspense boundaries for data loading
- Avoid prop drilling through many levels

---

## Backend Performance

### Database Queries
- Add indexes for frequently queried columns
- Avoid N+1 queries (use eager loading / joins)
- Use pagination for large result sets
- Cache expensive queries
- Monitor slow query log
- Use EXPLAIN to analyze query plans

### Caching Strategy
```
Level 1: In-memory cache (request-level)
Level 2: Application cache (Redis/Memcached)
Level 3: CDN cache (static assets, API responses)
Level 4: Browser cache (Cache-Control headers)
```

### Cache Headers
```
Static assets:  Cache-Control: public, max-age=31536000, immutable
API responses:  Cache-Control: private, max-age=60
HTML pages:     Cache-Control: no-cache (or short max-age)
```

### API Response Times
| Endpoint Type | Target |
|---------------|--------|
| Simple read | < 50ms |
| Complex query | < 200ms |
| Write operation | < 300ms |
| Report generation | < 2000ms |
| File upload | < 5000ms |

### Connection Pooling
- Configure appropriate pool size for your load
- Close idle connections
- Set connection timeout
- Monitor pool utilization

---

## Monitoring

### What to Track
- Page load times (real user metrics)
- Core Web Vitals (field data)
- API response times (p50, p95, p99)
- Error rates
- Database query times
- Memory usage
- CPU utilization

### Alerting Rules
- LCP > 4s for 5 minutes: warning
- Error rate > 1% for 5 minutes: warning
- Error rate > 5% for 1 minute: critical
- API p95 > 1s for 5 minutes: warning
- Memory > 80% for 10 minutes: warning

---

## Performance Audit Checklist

### Frontend
```
[ ] JavaScript bundle < 200 KB
[ ] No render-blocking resources
[ ] Images optimized (WebP/AVIF)
[ ] Lazy loading for below-fold content
[ ] Fonts preloaded with font-display: swap
[ ] Critical CSS inlined
[ ] Code split by route
[ ] No layout shift (explicit dimensions)
```

### Backend
```
[ ] Database queries indexed
[ ] No N+1 queries
[ ] Caching configured
[ ] API responses < 200ms (p95)
[ ] Connection pooling configured
[ ] Pagination on list endpoints
```

### Infrastructure
```
[ ] CDN configured
[ ] Compression enabled (gzip/brotli)
[ ] HTTP/2 or HTTP/3 enabled
[ ] Cache headers set correctly
[ ] Static assets have long cache
```

---

## Audit Report Format

```markdown
## Performance Audit

### Core Web Vitals
- LCP: [value] [PASS/FAIL]
- INP: [value] [PASS/FAIL]
- CLS: [value] [PASS/FAIL]

### Bundle Analysis
- Total JS: [size]
- Largest chunk: [name] [size]
- Opportunities: [list]

### Server Performance
- API p50: [value]
- API p95: [value]
- Slow queries: [count]

### Recommendations
1. [Priority] [What to do] - [Expected impact]
2. [Priority] [What to do] - [Expected impact]
```
