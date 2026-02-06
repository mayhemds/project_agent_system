---
description: Run comprehensive project review including code quality, security, accessibility, and performance checks.
arguments:
  - name: type
    description: Review type (code, security, a11y, performance, docs)
    required: false
---

# /review Command

> Run comprehensive project review.

## Usage

```
/review             # Full review (all checks)
/review code        # Code quality review only
/review security    # Security review only
/review a11y        # Accessibility review only
/review performance # Performance review only
/review docs        # Documentation review only
```

## What This Does

1. Activates review agents based on scope
2. Runs comprehensive checks
3. Documents findings with severity levels
4. Provides prioritized recommendations
5. Updates quality metrics in `status.yaml`

## Full Review Process

```
/review
  ↓
Code Review (reviewer agent)
  - Anti-slop compliance
  - Code quality and patterns
  - Error handling
  ↓
Security Review (security agent)
  - OWASP Top 10
  - Auth review
  - Dependency audit
  ↓
Accessibility Review (accessibility agent)
  - WCAG-AA compliance
  - Keyboard navigation
  - Screen reader compatibility
  ↓
Performance Review (performance agent)
  - Core Web Vitals
  - Bundle analysis
  - API response times
  ↓
Final Report with prioritized findings
```

## Review Output

```markdown
## Review Report

### Summary
- Critical: X | High: X | Medium: X | Low: X

### Code Review [PASS/FAIL]
[findings]

### Security Review [PASS/FAIL]
[findings]

### Accessibility Review [PASS/FAIL]
[findings]

### Performance Review [PASS/FAIL]
[findings]

### Recommendation
[Ship ready / Ship with conditions / Not ready]
```

## After Review

**All clear:** Proceed to `/ship`
**Issues found:** Fix critical/high issues, then `/review` again
