# Shipline

By **Mayhemds**

A multi-agent orchestration framework for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). 15 specialized AI agents coordinate to plan, design, build, test, review, and ship software projects — with persistent state across sessions and a mandatory quality pipeline that prevents AI-generated garbage from reaching production.

**Framework-agnostic.** Works with Next.js, React Native, Electron, Astro, Vue, FastAPI, Go, or any other stack. Agents adapt based on your `config.yaml`.

**Drop-in portable.** Copy `.claude/` and `CLAUDE.md` into any project root. Run `/init`. Start building.

---

## Table of Contents

- [Quick Start](#quick-start)
- [What This Does](#what-this-does)
- [Full Example: Building a SaaS App](#full-example-building-a-saas-app)
- [Commands](#commands)
- [How It Works](#how-it-works)
- [The 15 Agents](#the-15-agents)
- [Quality Pipeline](#quality-pipeline)
- [Anti-Slop Standards](#anti-slop-standards)
- [Project Templates](#project-templates)
- [Reference Library](#reference-library)
- [State Files](#state-files)
- [Multi-Session Continuity](#multi-session-continuity)
- [Directory Structure](#directory-structure)
- [FAQ](#faq)

---

## Quick Start

### 1. Copy into your project

```bash
# Clone this repo
git clone https://github.com/your-username/project-agent-system.git

# Copy the system into your project
cp -r project-agent-system/.claude your-project/.claude
cp project-agent-system/CLAUDE.md your-project/CLAUDE.md
```

### 2. Open your project in Claude Code

```bash
cd your-project
claude
```

### 3. Initialize

```
/init
```

Claude will ask you five questions:
1. What are you building?
2. Who is it for?
3. What's the ONE thing it must do?
4. Timeline?
5. Any decisions already made?

It then creates three files in your project root: `config.yaml` (your stack and goals), `status.yaml` (progress tracker), and `decisions.md` (decision log).

### 4. Plan

```
/plan "Build a task management app with team collaboration"
```

The Planner agent selects a technology stack, defines milestones, and creates an architecture plan. It picks the right project template (webapp, saas, api, etc.) and adapts it to your needs.

### 5. Build

```
/build
```

The Orchestrator reads your current status and activates the right agent. First session might be the UX Designer creating wireframes. Next session, the Frontend agent starts coding. Each time you run `/build`, it picks up where you left off.

---

## What This Does

When you use Claude Code without this system, you get a single general-purpose AI that you have to micromanage. It doesn't remember what phase you're in, doesn't enforce quality standards, and has no structured workflow.

With this system, you get **15 specialized agents** that:

- **Hand off work to each other.** The Planner creates a plan, hands off to the UX Designer, who creates wireframes and hands off to the Frontend agent, who writes code and hands off to the Reviewer.

- **Persist state across sessions.** Close Claude Code, come back tomorrow, run `/build` — it reads `status.yaml` and picks up exactly where you left off.

- **Enforce quality automatically.** Every piece of code goes through a mandatory pipeline: code is reviewed for anti-slop compliance, refactored if needed, tested, and QA'd before being marked complete. You can't skip this.

- **Prevent AI slop.** A comprehensive anti-slop ruleset prevents the generic, over-decorated, buzzword-filled output that AI tools typically produce. No gradient backgrounds. No "seamless" or "revolutionary." No modals for everything. Specific beats generic.

- **Adapt to any stack.** Tell it you're using Vue + FastAPI + PostgreSQL and every agent adjusts its patterns. Tell it you're using React Native + Expo and it switches to mobile patterns. The framework-specific knowledge lives in 14 stack reference files that agents consult based on your `config.yaml`.

---

## Full Example: Building a SaaS App

Here's what a real session flow looks like, from zero to shipped product. Each section below represents a separate Claude Code session (you can close and reopen between any of them).

### Session 1: Initialize and Plan

```
You: /init

Claude (Orchestrator): I'll set up your project. A few questions:
  1. What are you building?

You: A client portal where freelancers can share project updates with clients

Claude: 2. Who is it for?

You: Freelance designers and developers who need to keep clients in the loop

Claude: 3. What's the ONE thing it must do?

You: Let freelancers post updates that clients can view without needing to log in

Claude: 4. Timeline?

You: MVP in 2 weeks

Claude: 5. Any decisions already made?

You: I want to use Next.js and Supabase

Claude (Orchestrator):
  Project initialized. Created:
  - config.yaml (Next.js + Supabase + Tailwind stack)
  - status.yaml (Phase: not_started)
  - decisions.md

  Run /plan to create a detailed plan, or /build to start.
```

```
You: /plan

Claude (Planner):
  Based on your requirements, I'm using the SaaS template with modifications.

  ## Architecture Decision
  **Decision:** Next.js App Router + Supabase + Tailwind + shadcn/ui
  **Reason:** Full-stack in one framework, Supabase handles auth + database +
  realtime, fast to MVP
  **Logged to:** decisions.md

  ## Milestones

  1. Foundation (Planning)
     - Database schema: projects, updates, clients, magic links
     - Auth strategy: Supabase Auth for freelancers, magic links for clients
     - File structure and routing

  2. Design Complete (Design)
     - Dashboard wireframe (freelancer view)
     - Client portal wireframe (public view)
     - Component specs
     - Update composer design

  3. Core Features (Development)
     - Supabase auth + RLS policies
     - Project CRUD
     - Update posting with rich text
     - Client magic link access

  4. Feature Complete (Development)
     - File attachments
     - Email notifications (Resend)
     - Client feedback/reactions
     - Settings page

  5. Quality Assured (Testing)
     - Unit tests (85%+)
     - Integration tests for auth flows
     - E2E for critical paths

  6. Ship Ready (Review)
     - Security review (RLS policies, magic link expiry)
     - Accessibility review
     - Documentation
     - Deployment to Vercel

  Updated status.yaml: Phase → planning, Progress → 10%
```

### Session 2: Design

```
You: /build

Claude (Orchestrator):
  ## Current Status
  Phase: planning → advancing to design
  Progress: 10%
  All planning tasks complete. Activating UX Designer.

Claude (UX Designer):
  ## Dashboard Layout (Freelancer View)

  Sidebar navigation:
  - Projects (list)
  - Settings

  Main area:
  - Project selector (top)
  - Update feed (reverse chronological)
  - "Post Update" button (top right)

  ## Update Composer
  Inline editor, not a modal. Opens below the button.
  - Rich text area
  - File attachment dropzone
  - "Post" and "Save Draft" actions

  ## Client Portal (Public View)
  - Project header (name, freelancer info)
  - Update feed (same layout, read-only)
  - Reaction buttons (thumbs up, comment)
  - No auth required — magic link grants access

  ## Anti-Slop Notes
  - No gradient hero sections
  - No "Welcome to your dashboard" — just show the projects
  - Empty state: "No updates yet. Post your first update." with a button
  - Loading: skeleton of the update feed, not a spinner

  Design specs ready. Handing off to Frontend.
  Updated status.yaml: Phase → design, Progress → 25%
```

### Session 3-5: Development

```
You: /build

Claude (Orchestrator):
  Phase: design → advancing to development
  Activating Frontend agent.

Claude (Frontend):
  Reading config.yaml... Stack: Next.js + Supabase + Tailwind + shadcn/ui
  Loading stack references: nextjs.md, supabase.md, tailwind.md, shadcn.md

  Building the dashboard layout...
  [Creates app/(app)/layout.tsx, app/(app)/dashboard/page.tsx,
   components/features/update-feed.tsx, components/features/update-composer.tsx,
   etc.]

  Code complete. Handing off to quality pipeline.

Claude (Reviewer):
  ## Review Results

  anti-slop: PASS
  code-quality: 2 issues
    - MEDIUM: Update composer component is 180 lines, consider splitting
    - LOW: Missing loading state for project list

  Routing back to Frontend for fixes.

Claude (Frontend):
  Fixed: Split composer into UpdateComposer + UpdateAttachments.
  Fixed: Added skeleton loading for project list.

Claude (Tester):
  Writing tests...
  - update-feed.test.tsx: 8 tests, all passing
  - update-composer.test.tsx: 6 tests, all passing
  - Coverage: 87%

Claude (Reviewer - Final QA):
  PASS. All issues resolved. Moving to next task.

  Updated status.yaml: Progress → 55%
```

### Session 6: Testing and Review

```
You: /build

Claude (Orchestrator):
  Phase: development → advancing to testing
  Core features complete. Activating Tester.

Claude (Tester):
  Running full test suite...
  Integration tests for auth flow: 12/12 passing
  E2E tests for magic link access: 3/3 passing
  Overall coverage: 86%

Claude (Security):
  ## Security Review

  - RLS policies: PASS (checked all tables)
  - Magic link expiry: WARNING — currently no expiry set
    Recommendation: Add 7-day expiry to magic links
  - Auth flow: PASS
  - Input validation: PASS (Zod schemas on all forms)

  1 issue to resolve before shipping.

Claude (Orchestrator):
  Routing magic link issue back to Backend agent.

Claude (Backend):
  Added 7-day expiry to magic link generation.
  Updated RLS policy for expired links.

  [Quality pipeline runs again on the fix]

Claude (Reviewer): PASS.
```

### Session 7: Ship

```
You: /ship

Claude (Orchestrator):
  Running final checks...

  Security review: PASSED
  Accessibility review: PASSED (AA compliant)
  Test coverage: 86% (target: 85%) PASSED
  Documentation: README written, API docs complete

  ## Pre-Launch Checklist
  - [x] Favicon and og:image set
  - [x] Meta descriptions on all pages
  - [x] Error boundaries in place
  - [x] 404 page created
  - [x] Environment variables documented
  - [x] Vercel project configured
  - [x] Database migrations ready
  - [ ] DNS configured (needs manual setup)

  ## Deploy Command
  Ready to deploy. Push to main and Vercel handles the rest.

  ## Post-Launch
  - Monitor error rates for first hour
  - Check Core Web Vitals after 24 hours
  - Review analytics after 1 week
```

That's the full flow. Seven sessions from zero to deployed, with quality enforced at every step.

---

## Commands

| Command | What It Does |
|---------|-------------|
| `/init` | Initialize project. Creates `config.yaml`, `status.yaml`, and `decisions.md`. Asks 5 questions about your project. |
| `/plan "description"` | Create a project plan. Selects tech stack, defines milestones, creates architecture. |
| `/build` | Continue building. Orchestrator reads status and activates the right agent. |
| `/build frontend` | Jump to a specific agent (also: `backend`, `design`, `devops`). |
| `/status` | Show current phase, progress percentage, blockers, and recent activity. |
| `/review` | Run a comprehensive review: code quality, security, accessibility, performance. |
| `/ship` | Final checks, launch checklist, deployment preparation. |

---

## How It Works

### The Loop

Every time you run `/build`, the same loop executes:

```
1. Orchestrator reads status.yaml
2. Determines current phase and next agent
3. Activates the agent with context (stack, files, constraints)
4. Agent performs specialized work
5. If code was written → quality pipeline runs (review → test → QA)
6. Status.yaml is updated
7. Orchestrator determines next step (continue, hand off, or complete)
```

### Phase Progression

Projects move through six phases, with quality gates between each:

```
not_started → planning → design → development → testing → review → shipping
```

**Planning to Design:** Config populated, milestones defined, architecture decided.

**Design to Development:** Wireframes done, component specs ready, design tokens set.

**Development to Testing:** Core features complete, quality pipeline passing on all code.

**Testing to Review:** Tests written and passing, coverage targets met.

**Review to Shipping:** All reviews pass (security, accessibility, performance), no critical issues.

### Agent Handoffs

When one agent finishes, the Orchestrator captures context and passes it to the next:

```yaml
handoff:
  from_agent: frontend
  to_agent: reviewer
  reason: "Dashboard components complete, ready for review"
  context:
    - Created 12 components in src/components/
    - Used shadcn/ui for all UI primitives
    - Implemented skeleton loading states
  files_ready:
    - src/app/(app)/dashboard/page.tsx
    - src/components/features/update-feed.tsx
```

---

## The 15 Agents

### Planning Agents

| Agent | What It Does |
|-------|-------------|
| **Orchestrator** | The coordinator. Reads status, routes to agents, manages handoffs, enforces quality gates. Never writes code. |
| **Planner** | Gathers requirements, selects tech stack, defines milestones, creates architecture. Active during `/plan` and the planning phase. |

### Design Agents

| Agent | What It Does |
|-------|-------------|
| **UX Designer** | Creates wireframes, user flows, component specs, interaction patterns. Decides layout and information hierarchy. Does not write code — writes specs that the Frontend agent implements. |
| **Copywriter** | Writes UI text: headlines, button labels, form labels, error messages, empty states, success messages. Follows anti-slop copy rules (no buzzwords, specific language). |

### Development Agents

| Agent | What It Does |
|-------|-------------|
| **Frontend** | Implements UI: components, pages, styling, client-side interactions, state management. Reads stack references for framework-specific patterns (Next.js, React Native, Vue, etc.). |
| **Backend** | Implements server-side: APIs, database schema, authentication, authorization, webhooks, email, background jobs. Reads stack references for database/ORM patterns. |
| **DevOps** | Infrastructure: CI/CD pipelines, Docker configs, deployment, environment management, monitoring, hosting configuration. |

### Quality Agents

| Agent | What It Does | Can Modify Code? |
|-------|-------------|-----------------|
| **Tester** | Writes and runs unit, integration, and E2E tests. Reports coverage. | Yes (test files) |
| **Security** | OWASP Top 10 review, auth audit, dependency check, data handling review. | No (read-only) |
| **Performance** | Core Web Vitals audit, bundle analysis, caching review, database query optimization. | No (read-only) |
| **Accessibility** | WCAG-AA compliance, ARIA patterns, keyboard navigation, screen reader testing. | No (read-only) |
| **Reviewer** | Code quality review, anti-slop compliance check, final QA sign-off. The gatekeeper. | No (read-only) |

### Content Agents

| Agent | What It Does |
|-------|-------------|
| **Content Writer** | Blog posts, marketing copy, case studies, content strategy. Not UI copy (that's Copywriter). |
| **Technical Writer** | READMEs, API documentation, setup guides, changelogs. |
| **SEO** | Meta tags, structured data, sitemap, robots.txt, Open Graph tags, llms.txt. |

---

## Quality Pipeline

Every piece of code must pass through this pipeline before being considered done:

```
Code Written → Reviewer → Refactor (if needed) → Tester → QA → Done
```

### How It Works

1. A dev agent (Frontend, Backend, or DevOps) writes code
2. The **Reviewer** agent examines it (read-only) for:
   - Anti-slop violations (gradient slop, copy slop, modal abuse)
   - Code quality issues (complexity, naming, patterns)
   - Security basics (exposed secrets, injection risks)
   - Accessibility basics (semantic HTML, ARIA)
3. If the Reviewer finds issues:
   - Critical/High severity: code goes back to the dev agent for refactoring
   - Medium/Low: logged but don't block
4. The **Tester** agent writes tests and runs them
5. If tests fail: code goes back to the dev agent
6. The **Reviewer** does a final QA pass
7. Only then is the task marked complete in `status.yaml`

### Why This Matters

Without this pipeline, AI-generated code tends to work but be mediocre: inconsistent patterns, missing edge cases, poor error messages, unnecessary complexity. The pipeline catches these issues before they accumulate.

The Orchestrator enforces this — it won't mark a task complete until the pipeline passes. You can't skip it.

---

## Anti-Slop Standards

"Slop" is the generic, over-decorated, buzzword-filled output that AI generates by default. This system has a comprehensive ruleset (860 lines in `references/anti-slop.md`) that every agent follows.

### Visual Slop (What AI Does vs. What We Do)

| AI Default | Our Standard |
|-----------|-------------|
| Gradient backgrounds everywhere | Solid colors: `bg-background`, `bg-card`, `bg-muted` |
| Shadows on every element | Shadows only for elevation: dropdowns, modals |
| Icons inside colored circles | Icons inline, no decorative containers |
| `rounded-2xl` on everything | One consistent radius: `rounded-md` or `rounded-lg` |
| Rainbow badge colors | Colors with semantic meaning only |
| Decorative emojis | No emojis in UI |

### Copy Slop

**Banned words:** seamless, revolutionary, powerful, innovative, cutting-edge, leverage, synergy, streamline, elevate, empower, robust, best-in-class

**Banned patterns:** "Transform your X", "Take X to the next level", "In today's fast-paced world"

**Rule:** Specific beats generic. Numbers beat adjectives. "Saves 2 hours per week" beats "Powerful time-saving solution."

### Interaction Slop

| AI Default | Our Standard |
|-----------|-------------|
| Modal for editing | Inline editing |
| Modal for details | Expand panel or navigate |
| "Are you sure?" confirmation | Undo toast (let them undo, don't ask permission) |
| Success modal | Toast notification |
| Full-page loading spinner | Skeleton of the content shape |

### Data Slop

| AI Default | Our Standard |
|-----------|-------------|
| Big number with no context | Number + comparison ("12% — up from 8% last month") |
| Pie chart | Bar chart (always) |
| Chart with no title | Title + one-sentence explanation |
| "No data" empty state | Explanation + action ("No projects yet. Create your first project.") |

---

## Project Templates

When you run `/plan`, the Planner selects a template based on your project type and uses it to scaffold milestones, suggest a stack, and set quality targets.

| Template | Best For | Test Coverage | Key Agents |
|----------|---------|--------------|------------|
| **webapp** | Standard web app | 80% | planner, ux-designer, frontend, tester, reviewer |
| **saas** | SaaS with billing | 85% | + backend, security, technical-writer |
| **marketing-site** | Landing pages, marketing | 50% | + content-writer, copywriter, seo |
| **api** | Backend API service | 90% | planner, backend, tester, security, technical-writer |
| **mobile-app** | React Native / Expo | 80% | planner, ux-designer, frontend, backend, tester |
| **desktop-app** | Electron / Tauri | 80% | planner, ux-designer, frontend, tester, security |

Each template includes:
- Suggested tech stack (override in `config.yaml`)
- Project directory structure
- Milestone breakdown with phases
- Quality targets (coverage, accessibility, performance)
- Required and optional agents
- Special considerations (e.g., app store submission for mobile, code signing for desktop)

---

## Reference Library

Agents consult reference files for domain-specific knowledge. There are two types:

### Universal References (13 files — always available)

| Reference | What's In It |
|-----------|-------------|
| `anti-slop.md` | 860-line guide to visual, copy, interaction, data, state, code, and architecture slop |
| `copy-patterns.md` | UI copy voice/tone, content types, writing rules |
| `ux-patterns.md` | Component selection, layout patterns, responsive design, mobile patterns |
| `testing.md` | Test pyramid, coverage targets, naming conventions, mock rules |
| `security.md` | OWASP Top 10, auth patterns, input validation, security headers |
| `performance.md` | Core Web Vitals targets, bundle optimization, caching strategies, database tuning |
| `accessibility.md` | WCAG-AA/AAA, ARIA patterns, keyboard nav, screen reader support |
| `api-design.md` | REST conventions, response formats, pagination, versioning, rate limiting |
| `seo.md` | Meta tags, structured data, sitemap, Open Graph, llms.txt |
| `content-strategy.md` | Blog structure, title formulas, content types, email content |
| `docs-patterns.md` | README structure, API docs, component docs, changelog format |
| `launch-checklist.md` | Pre-launch verification: legal, branding, SEO, performance, security, a11y, content, cross-browser |
| `project-templates.md` | Detailed phase breakdowns for all 6 project types |

### Stack References (14 files — loaded based on config.yaml)

| Stack | What's In It |
|-------|-------------|
| `nextjs.md` | App Router, server/client components, layouts, metadata, server actions, image optimization |
| `supabase.md` | Auth, RLS policies, database queries, storage, realtime, edge functions |
| `tailwind.md` | Utility patterns, responsive breakpoints, dark mode, animation, common pitfalls |
| `shadcn.md` | Component usage, theming, forms with react-hook-form + Zod, data tables |
| `typescript.md` | Type patterns, generics, Zod integration, React typing, utility types |
| `vercel.md` | Deployment, serverless functions, caching, cron jobs, preview deployments |
| `resend.md` | Email sending, React Email templates, webhooks, domain verification |
| `react-native.md` | Expo Router, NativeWind, AsyncStorage, push notifications, deep linking |
| `electron.md` | Main/renderer process, IPC, preload scripts, auto-update, packaging |
| `stripe.md` | Checkout, subscriptions, webhooks, customer portal, Payment Intents |
| `prisma.md` | Schema, migrations, queries, transactions, middleware, seeding |
| `docker.md` | Multi-stage Dockerfiles, Compose, networks, health checks, production checklist |
| `astro.md` | Content collections, islands architecture, SSG/SSR, image optimization |
| `vue.md` | Composition API, Pinia, Vue Router, Nuxt.js, composables |

Agents automatically load the relevant stack references based on what's in your `config.yaml`. If your config says `framework: next`, the Frontend agent reads `nextjs.md`. If it says `database: postgres` with `orm: prisma`, the Backend agent reads `prisma.md`.

---

## State Files

Three files in your project root maintain state across sessions:

### config.yaml

Your project's identity. Created by `/init`, refined by `/plan`.

```yaml
project:
  name: "ClientPortal"
  type: saas
  description: "Client portal for freelancers to share project updates"

stack:
  frontend:
    framework: next
    styling: tailwind
    components: shadcn
  backend:
    runtime: node
    database: postgres
    orm: prisma
  infrastructure:
    hosting: vercel
    auth: supabase-auth
    email: resend

audience:
  who: "Freelance designers and developers"
  needs: "Keep clients updated without meetings"

quality:
  test_coverage: 85
  accessibility: AA
  performance:
    lcp: 2.5s
    cls: 0.1
```

### status.yaml

The source of truth for progress. Updated by every agent after every action.

```yaml
phase: development
active_agent: frontend
progress: 55

milestones:
  - name: Foundation
    status: complete
  - name: Design Complete
    status: complete
  - name: Core Features
    status: in_progress
    tasks:
      - name: Authentication
        status: complete
        assigned_to: backend
      - name: Dashboard UI
        status: in_progress
        assigned_to: frontend
      - name: Update posting
        status: not_started

blockers: []

quality:
  last_review: 2026-02-05
  review_passed: true
  test_coverage: 87
```

### decisions.md

Append-only log of key decisions. Gives context across sessions.

```markdown
## 2026-02-01: Tech Stack

**Decision:** Next.js + Supabase + Tailwind + shadcn/ui
**Reason:** Full-stack in one framework, Supabase handles auth + DB + realtime
**Alternatives:** Remix + PlanetScale, SvelteKit + Drizzle
**Impact:** Determines all framework-specific patterns

## 2026-02-03: Client Access Strategy

**Decision:** Magic links (no client accounts)
**Reason:** Clients shouldn't need to create accounts just to view updates
**Alternatives:** Password-based accounts, OAuth
**Impact:** Simpler UX, need magic link expiry policy
```

---

## Multi-Session Continuity

This system is designed for projects that span many sessions. Here's how it works:

1. **Every session starts the same way.** The Orchestrator reads `status.yaml`, reports where you are, and proposes the next action.

2. **State is in files, not in memory.** When Claude Code's context window fills up or you close the terminal, nothing is lost. `status.yaml`, `config.yaml`, and `decisions.md` persist on disk.

3. **The `/build` command is resumable.** Run it in session 1, close Claude Code, run it again in session 2 — it picks up from the exact task where you left off.

4. **Decisions compound.** The `decisions.md` log gives each new session the context of all previous decisions, so agents don't re-debate settled questions.

```
Session 1:  /init → /plan                    (planning complete)
Session 2:  /build                            (design phase)
Session 3:  /build                            (frontend development)
Session 4:  /build                            (backend development)
Session 5:  /build                            (testing)
Session 6:  /review                           (security + accessibility)
Session 7:  /ship                             (deployment)
```

Each session reads the state files and continues from exactly where the last one stopped.

---

## Directory Structure

```
your-project/
├── CLAUDE.md                              # Entry point (Claude reads this first)
├── config.yaml                            # Your project config (created by /init)
├── status.yaml                            # Progress tracker (created by /init)
├── decisions.md                           # Decision log (created by /init)
└── .claude/
    ├── CLAUDE.md                          # Plugin instructions
    ├── agents/                            # 15 agent definitions
    │   ├── orchestrator.md
    │   ├── planner.md
    │   ├── ux-designer.md
    │   ├── frontend.md
    │   ├── backend.md
    │   ├── devops.md
    │   ├── tester.md
    │   ├── security.md
    │   ├── performance.md
    │   ├── accessibility.md
    │   ├── reviewer.md
    │   ├── copywriter.md
    │   ├── content-writer.md
    │   ├── technical-writer.md
    │   └── seo.md
    ├── commands/                           # 6 slash commands
    │   ├── init.md
    │   ├── plan.md
    │   ├── build.md
    │   ├── status.md
    │   ├── review.md
    │   └── ship.md
    ├── skills/
    │   └── project-system/                # Master skill
    │       ├── SKILL.md                   # Coordination protocols, anti-slop summary
    │       ├── assets/
    │       │   ├── config.yaml            # Template for project config
    │       │   └── status.yaml            # Template for progress tracker
    │       └── references/
    │           ├── anti-slop.md           # 860-line anti-slop guide
    │           ├── copy-patterns.md
    │           ├── ux-patterns.md
    │           ├── testing.md
    │           ├── security.md
    │           ├── performance.md
    │           ├── accessibility.md
    │           ├── api-design.md
    │           ├── seo.md
    │           ├── content-strategy.md
    │           ├── docs-patterns.md
    │           ├── launch-checklist.md
    │           ├── project-templates.md
    │           └── stacks/
    │               ├── nextjs.md
    │               ├── supabase.md
    │               ├── tailwind.md
    │               ├── shadcn.md
    │               ├── typescript.md
    │               ├── vercel.md
    │               ├── resend.md
    │               ├── react-native.md
    │               ├── electron.md
    │               ├── stripe.md
    │               ├── prisma.md
    │               ├── docker.md
    │               ├── astro.md
    │               └── vue.md
    └── templates/                         # 6 project type configs
        ├── webapp.yaml
        ├── saas.yaml
        ├── marketing-site.yaml
        ├── api.yaml
        ├── mobile-app.yaml
        └── desktop-app.yaml
```

**59 files total.** All self-contained in `.claude/`. No external dependencies. No scripts to run. No packages to install.

---

## FAQ

### Do I need to use all 15 agents?

No. The Orchestrator activates agents based on what your project needs. A simple marketing site might only use: planner, ux-designer, frontend, copywriter, seo, reviewer. An API service might only use: planner, backend, tester, security, technical-writer, reviewer. The project template determines which agents are required vs. optional.

### Can I override the agent's decisions?

Yes. You can:
- Edit `config.yaml` directly to change stack or settings
- Use `/build frontend` to jump to a specific agent
- Tell Claude to skip a phase or modify the plan
- Override any template defaults

The system provides structure, not a straitjacket.

### What if I'm using a stack that doesn't have a reference file?

Agents still work — they use universal principles (the 13 universal reference files). The stack references are a bonus that gives agents framework-specific code patterns and conventions. If you're using Django, for example, the Backend agent won't have a `django.md` reference but will still follow the universal `security.md`, `testing.md`, and `api-design.md` patterns.

You can also add your own stack reference by creating a file in `.claude/skills/project-system/references/stacks/your-stack.md`.

### How is this different from Cursor rules or other AI coding setups?

This is a **workflow system**, not just rules. Most AI coding setups give you a static set of instructions. This gives you:
- Persistent state across sessions (status.yaml)
- Specialized agents that hand off work to each other
- A mandatory quality pipeline that can't be skipped
- Phase-based project management with quality gates
- 860 lines of anti-slop rules tested against real AI output

### Can I use this with an existing project?

Yes. Run `/init` and tell Claude it's an existing project. It will scan your codebase, detect your stack, and create state files with completed work already marked. Then `/build` continues from where your project currently is.

### How do I add a new stack reference?

Create a markdown file in `.claude/skills/project-system/references/stacks/`. Follow the pattern of existing files: title, key patterns with code examples, conventions, and common pitfalls. Then update your `config.yaml` to reference the new stack, and agents will pick it up automatically.

### Does this work with Claude Code in VS Code / JetBrains?

Yes. The system is just files in `.claude/` — it works anywhere Claude Code works.

---

## License

MIT
