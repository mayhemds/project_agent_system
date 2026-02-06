# Launch Checklist

Comprehensive pre-launch verification. Run through this before going live.

---

## Legal and Compliance

```
[ ] Cookie consent banner (if using cookies/tracking)
[ ] Privacy policy page published
[ ] Terms of service page published
[ ] GDPR compliance (if serving EU users)
  [ ] Data processing agreement
  [ ] Right to deletion
  [ ] Data export capability
[ ] CCPA compliance (if serving California users)
[ ] Accessibility statement (recommended)
[ ] Copyright notice in footer
```

---

## Branding and Identity

```
[ ] Favicon (16x16, 32x32, 48x48)
[ ] Apple touch icon (180x180)
[ ] manifest.json / site.webmanifest
[ ] Open Graph image (1200x630)
[ ] Twitter card image (1200x600)
[ ] Logo in all required sizes
[ ] Consistent brand colors throughout
[ ] Consistent typography throughout
```

---

## SEO

```
[ ] Unique title tag per page (50-60 chars)
[ ] Unique meta description per page (150-160 chars)
[ ] Open Graph tags on all pages
[ ] Twitter card tags on all pages
[ ] sitemap.xml generated and submitted
[ ] robots.txt configured
[ ] Canonical URLs set
[ ] Structured data (JSON-LD) added
[ ] H1 tag on every page (one per page)
[ ] Alt text on all images
[ ] Descriptive URLs (not /page-1, /page-2)
[ ] 301 redirects for changed URLs
[ ] No broken links (404s)
[ ] llms.txt file (for AI discovery)
```

---

## Performance

```
[ ] Lighthouse performance score > 90
[ ] LCP < 2.5s
[ ] INP < 200ms
[ ] CLS < 0.1
[ ] Images optimized (WebP/AVIF, compressed)
[ ] Font loading optimized (font-display: swap)
[ ] JavaScript bundle < 200 KB (compressed)
[ ] Code splitting by route
[ ] Compression enabled (gzip/brotli)
[ ] CDN configured for static assets
[ ] Cache headers set correctly
[ ] No render-blocking resources
```

---

## Responsive and Mobile

```
[ ] Works on mobile (320px minimum)
[ ] Works on tablet (768px)
[ ] Works on desktop (1024px+)
[ ] Touch targets minimum 44x44px
[ ] No horizontal scroll on any screen size
[ ] Viewport meta tag set
[ ] Text readable without zooming
[ ] Forms usable on mobile
[ ] Navigation works on all sizes
```

---

## Accessibility

```
[ ] WCAG-AA color contrast (4.5:1 text, 3:1 UI)
[ ] Keyboard navigation works for all interactions
[ ] Focus indicators visible
[ ] Skip-to-content link
[ ] Alt text on all meaningful images
[ ] Form fields have labels
[ ] Error messages are descriptive
[ ] Screen reader testing done
[ ] Reduced motion respected
[ ] No auto-playing media
```

---

## Security

```
[ ] HTTPS enforced (HTTP redirects to HTTPS)
[ ] Security headers configured (CSP, HSTS, etc.)
[ ] No exposed API keys or secrets
[ ] Auth tested (login, logout, protected routes)
[ ] Input validation on all forms
[ ] XSS prevention verified
[ ] SQL injection prevention verified
[ ] Rate limiting on auth and API endpoints
[ ] File upload validation (if applicable)
[ ] CORS properly configured
```

---

## Error Handling

```
[ ] Custom 404 page
[ ] Custom 500 page
[ ] Error boundaries in place (React/frontend)
[ ] Errors show user-friendly messages
[ ] Errors explain how to fix or recover
[ ] Offline/no-connection state handled
[ ] Form errors preserve user input
[ ] API errors handled gracefully
```

---

## Content

```
[ ] No placeholder text (lorem ipsum, "Coming soon")
[ ] No "Example Company" or fake data
[ ] All images are real (no stock photo placeholders)
[ ] All links work (internal and external)
[ ] Spell check done
[ ] Anti-slop check passed (no banned words/patterns)
[ ] Contact information correct
[ ] Pricing information correct
[ ] Legal text reviewed
```

---

## Cross-Browser

```
[ ] Chrome (latest) tested
[ ] Firefox (latest) tested
[ ] Safari (latest) tested
[ ] Edge (latest) tested
[ ] iOS Safari tested
[ ] Chrome Android tested
```

---

## Social and Sharing

```
[ ] Open Graph preview correct (test with og debugger)
[ ] Twitter card preview correct
[ ] Share URL works
[ ] Social links point to correct profiles
```

---

## Analytics and Monitoring

```
[ ] Analytics installed (GA4, Plausible, etc.)
[ ] Goals/conversions configured
[ ] Key events defined and tracked
[ ] Error tracking active (Sentry, etc.)
[ ] Uptime monitoring configured
[ ] Performance monitoring active
```

---

## Infrastructure

```
[ ] DNS configured and propagated
[ ] SSL certificate active and valid
[ ] CDN configured
[ ] Backups configured (database)
[ ] Environment variables set for production
[ ] CI/CD pipeline working
[ ] Rollback plan documented
[ ] Monitoring alerts configured
[ ] Log aggregation set up
[ ] Database indexes optimized
```

---

## Post-Launch

### First Hour
```
[ ] All pages load correctly
[ ] Critical user flows work (signup, login, core feature)
[ ] Error tracking active and receiving events
[ ] Metrics flowing (analytics, performance)
[ ] No console errors in production
```

### Day 1
```
[ ] Check analytics for anomalies
[ ] Review error tracking for new issues
[ ] Check email delivery (if applicable)
[ ] Monitor server health
[ ] Address any user-reported issues
```

### Week 1
```
[ ] Performance metrics reviewed
[ ] User feedback collected
[ ] Hotfixes deployed if needed
[ ] Search engine indexing confirmed
[ ] Plan next iteration
```
