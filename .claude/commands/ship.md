---
description: Final checks and deployment preparation. Verifies all reviews passed, generates deployment guide, pre/post-launch checklists.
arguments:
  - name: action
    description: Specific action (checklist, deploy)
    required: false
---

# /ship Command

> Final checks and deployment preparation.

## Usage

```
/ship               # Full ship process
/ship checklist     # Show pre-ship checklist only
/ship deploy        # Generate deployment guide
```

## What This Does

1. Verifies all reviews passed
2. Runs the launch checklist (see `references/launch-checklist.md`)
3. Creates deployment checklist
4. Generates deployment guide
5. Marks project complete (or identifies blockers)

## Ship Process

```
/ship
  ↓
Verify Reviews (code, security, a11y)
  ↓
Launch Checklist
  - Legal, Branding, SEO
  - Performance, Responsive, Accessibility
  - Security, Error handling, Content
  - Infrastructure, Analytics
  ↓
Generate deployment guide + post-launch checklist
  ↓
Sign-Off: Ship or Not Ready
```

## Pre-Ship Checklist

```
### Code Quality
- [ ] Build succeeds
- [ ] All tests passing
- [ ] No linting/type errors

### Reviews
- [ ] Code review passed
- [ ] Security review passed
- [ ] Accessibility review passed

### Environment
- [ ] Environment variables documented
- [ ] Production config set
- [ ] Database migrations ready

### Infrastructure
- [ ] Hosting configured
- [ ] Domain/DNS/SSL ready
- [ ] Monitoring configured

### Documentation
- [ ] README updated
- [ ] API docs current
```

## Post-Launch Checklist

```
### First Hour
- [ ] All pages load
- [ ] Critical flows work
- [ ] Error tracking active
- [ ] Metrics flowing

### Day 1
- [ ] User feedback reviewed
- [ ] Analytics checked
- [ ] Hotfixes if needed

### Week 1
- [ ] Performance metrics reviewed
- [ ] Plan next iteration
```

## Tips

- Run `/review` before `/ship`
- Don't skip the launch checklist
- Have rollback plan ready
- Monitor closely after launch
