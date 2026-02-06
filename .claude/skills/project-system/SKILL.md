---
name: project-system
description: |
  Master project system for quality, standards, and coordination. Auto-triggers on:
  - Any UI/frontend work (enforces visual anti-slop)
  - Any copy/text work (enforces copy anti-slop)
  - Any data/dashboard work (enforces data anti-slop)
  - Project planning, status checks, task management
  - Code review, quality checks
  - When user mentions: standards, quality, anti-slop, review, init, status, build, ship
  This skill contains all anti-slop rules, project state management, quality pipeline, and coordination protocols.
---

# Project System

Master skill for project quality, standards, and multi-agent coordination.

## Quick Reference

**Anti-slop rules:** `references/anti-slop.md`
**Copy patterns:** `references/copy-patterns.md`
**UX patterns:** `references/ux-patterns.md`
**Testing:** `references/testing.md`
**Security:** `references/security.md`
**SEO:** `references/seo.md`
**Content strategy:** `references/content-strategy.md`
**Documentation patterns:** `references/docs-patterns.md`
**Performance:** `references/performance.md`
**Accessibility:** `references/accessibility.md`
**API design:** `references/api-design.md`
**Launch checklist:** `references/launch-checklist.md`
**Project templates:** `references/project-templates.md`
**Stack-specific:** `references/stacks/` (loaded based on config.yaml)

**State templates:** `assets/config.yaml`, `assets/status.yaml`

---

## Project State Management

### On Session Start

1. Look for `status.yaml` in project root
2. If found: Load it, report current status, continue from last phase
3. If not found: Ask user about project, offer to create with `/init`

### State File: status.yaml

Source of truth for project progress:
```yaml
project:
  name: ""
  description: ""

phase: not_started  # not_started | planning | design | development | testing | review | shipping
active_agent: null
progress: 0

milestones:
  - name: ""
    status: not_started  # not_started | in_progress | complete
    tasks:
      - name: ""
        status: not_started
        assigned_to: ""
        notes: ""

blockers: []

recent_activity:
  - date: ""
    action: ""

quality:
  last_review: null
  review_passed: false
  test_coverage: 0
```

### Config File: config.yaml

Project configuration all agents read:
```yaml
project:
  name: ""
  type: webapp  # webapp | saas | marketing-site | api | mobile-app | desktop-app
  description: ""

stack:
  frontend:
    framework: ""     # next, react, vue, svelte, astro, react-native, etc.
    styling: ""       # tailwind, css-modules, styled-components, etc.
    components: ""    # shadcn, radix, headless-ui, etc.
  backend:
    runtime: ""       # node, python, go, etc.
    framework: ""     # express, fastify, fastapi, gin, etc.
    database: ""      # postgres, mysql, mongodb, sqlite, etc.
    orm: ""           # prisma, drizzle, sqlalchemy, etc.
  infrastructure:
    hosting: ""       # vercel, aws, fly.io, railway, etc.
    auth: ""          # clerk, auth.js, supabase-auth, firebase-auth, etc.
    email: ""         # resend, sendgrid, ses, etc.
    payments: ""      # stripe, lemonsqueezy, etc.

audience:
  who: ""
  needs: ""
  context: ""

voice:
  tone: professional  # professional | casual | friendly | technical
  personality: ""

quality:
  test_coverage: 80
  accessibility: AA
  performance:
    lcp: 2.5s
    fid: 100ms
    cls: 0.1
```

### Decision Log: decisions.md

Append-only log of key decisions:
```markdown
## YYYY-MM-DD: [Decision Title]

**Decision:** What was decided
**Reason:** Why this approach
**Alternatives considered:** What else was evaluated
**Impact:** What this affects
```

---

## Anti-Slop Standards (Summary)

Full reference in `references/anti-slop.md`. Key rules:

### Visual
- NO gradient backgrounds (unless brand-specified)
- NO shadows on everything (only elevation: dropdowns, modals)
- NO icons in colored boxes
- NO decorative emojis
- Consistent border-radius (pick ONE: rounded-md or rounded-lg)

### Copy
**Banned words:** seamless, revolutionary, powerful, innovative, cutting-edge, leverage, synergy, streamline, elevate, empower, robust, best-in-class
**Banned patterns:** "Transform your X", "Take X to the next level", "In today's fast-paced"
**Rule:** Specific beats generic. Numbers beat adjectives.

### Interactions
- NO modals for editing → inline editing
- NO modals for details → expand/panel
- NO "Are you sure?" → undo toast
- NO success modals → toast notification

### Data/Dashboards
- EVERY stat needs context (vs what?)
- NO pie charts (use bar charts)
- EVERY chart needs title + explanation

### States
- Empty: explain + action (not "No data")
- Loading: skeleton for content, spinner for actions
- Error: what's wrong + how to fix + recovery

### Code
- Simple over clever
- Flat over nested
- No placeholders in committed code

---

## Mandatory Quality Pipeline

**Every piece of code MUST pass through this pipeline before being considered done:**

```
Code Written → Review → Refactor (if needed) → Test → QA → Done
```

### Pipeline Steps

1. **Code written** by dev agent (frontend, backend, devops)
2. **Reviewer agent** (read-only) reviews for:
   - Anti-slop compliance
   - Code quality and patterns
   - Security basics
   - Accessibility basics
3. **If issues found** → original dev agent refactors
4. **Tester agent** writes/runs tests
5. **If tests fail** → original dev agent fixes
6. **Final QA** → reviewer does final pass
7. **ONLY THEN** mark task complete in status.yaml

### Enforcement

- Orchestrator BLOCKS task completion until pipeline passes
- Dev agents are NOT done when code is written — they hand off to pipeline
- Reviewer agent flags issues with severity (critical, high, medium, low)
- Critical/high issues MUST be fixed before proceeding

---

## Agent Coordination

### Agent Roster

| Agent | Role | Can Write Code? |
|-------|------|----------------|
| orchestrator | Coordination, routing | No |
| planner | Requirements, milestones | No |
| ux-designer | Wireframes, flows, specs | No |
| frontend | UI implementation | Yes |
| backend | API, database, auth | Yes |
| devops | CI/CD, deployment, infra | Yes |
| tester | Tests, coverage | Yes |
| security | Security review | Read-only |
| performance | Performance audit | Read-only |
| accessibility | WCAG audit | Read-only |
| reviewer | Code review, QA | Read-only |
| copywriter | UI copy, microcopy | Yes (copy only) |
| content-writer | Blog posts, marketing | Yes (content only) |
| technical-writer | Docs, READMEs | Yes (docs only) |
| seo | Meta tags, structured data | Yes |

### Agent Activation

Orchestrator routes to agents based on phase:

| Phase | Primary Agents | Support Agents |
|-------|---------------|----------------|
| planning | planner | — |
| design | ux-designer | copywriter |
| development | frontend, backend, devops | copywriter, seo |
| testing | tester | security, performance, accessibility |
| review | reviewer | security, performance, accessibility |
| shipping | reviewer (sign-off), devops | technical-writer |

### Agent Briefing Template

When spawning a subagent, include:

```markdown
## Task
[Specific task description]

## Context
Project: [name] (from config.yaml)
Phase: [current phase]
Stack: [relevant stack info from config.yaml]
Files to work with: [list]

## Standards
- Read anti-slop rules from references/anti-slop.md
- Read stack-specific patterns from references/stacks/[stack].md

## Deliverables
[Expected output]
```

### Reviewing Subagent Output

Always check against anti-slop before accepting:
1. Visual slop? (gradients, shadows, icons in boxes)
2. Copy slop? (banned words, hype patterns)
3. Interaction slop? (modals for editing)
4. Data slop? (contextless stats, pie charts)

---

## Phase Workflow

### Standard Phase Order

```
not_started → planning → design → development → testing → review → shipping
```

### Phase Transition Rules

1. **planning → design**: Config.yaml populated, milestones defined
2. **design → development**: Wireframes done, component specs ready
3. **development → testing**: Core features complete, quality pipeline passing
4. **testing → review**: Tests written, coverage targets met
5. **review → shipping**: All reviews pass, no critical/high issues
6. **shipping → done**: Deployed, post-launch checklist started

---

## Commands Reference

| Command | Action |
|---------|--------|
| `/init` | Initialize project, create config.yaml + status.yaml |
| `/plan` | Create or show project plan |
| `/build` | Start/continue building (orchestrator decides agent) |
| `/status` | Report current phase, progress, blockers |
| `/review` | Run quality review (code, security, a11y, performance) |
| `/ship` | Final checks, launch checklist, deployment prep |

---

## Stack References

Stack-specific patterns are loaded based on `config.yaml`. Available stacks:

| Stack | Reference |
|-------|-----------|
| Next.js | `references/stacks/nextjs.md` |
| Supabase | `references/stacks/supabase.md` |
| Tailwind | `references/stacks/tailwind.md` |
| shadcn/ui | `references/stacks/shadcn.md` |
| TypeScript | `references/stacks/typescript.md` |
| Vercel | `references/stacks/vercel.md` |
| Resend | `references/stacks/resend.md` |
| React Native | `references/stacks/react-native.md` |
| Electron | `references/stacks/electron.md` |
| Stripe | `references/stacks/stripe.md` |
| Prisma | `references/stacks/prisma.md` |
| Docker | `references/stacks/docker.md` |
| Astro | `references/stacks/astro.md` |
| Vue | `references/stacks/vue.md` |

Agents read `config.yaml` for the project's stack, then consult the relevant stack reference files.
