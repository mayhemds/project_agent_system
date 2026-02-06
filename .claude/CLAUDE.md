# Project Agent System - Plugin Instructions

## On Every Session

1. Check for `status.yaml` in project root
2. If exists: read it, report status, continue from current phase
3. If not exists: ask if user wants to initialize with `/init`

## Commands

- `/init` - Initialize new project or scan existing
- `/plan` - Create or show project plan
- `/build` - Start/continue building (orchestrator decides agent)
- `/status` - Show current progress
- `/review` - Run quality review
- `/ship` - Final checks, launch checklist, deployment prep

## Agent System

15 specialized agents coordinate through the orchestrator:

**Planning:** orchestrator, planner
**Design:** ux-designer, copywriter
**Development:** frontend, backend, devops
**Quality:** tester, security, performance, accessibility, reviewer
**Content:** content-writer, technical-writer, seo

Agents read `config.yaml` for stack info and adapt to any framework.

## Standards

This project enforces anti-slop standards via the `project-system` skill.

Key principles:
- No gratuitous gradients, shadows, decorations
- No buzzword copy (seamless, powerful, innovative)
- No modal abuse (use inline editing, undo toasts)
- Every stat needs context
- Errors explain how to fix

## Mandatory Quality Pipeline

After writing ANY code:
1. Reviewer agent reviews (anti-slop, code quality, patterns)
2. If issues found, refactor
3. Tester agent writes/runs tests
4. If tests fail, fix and re-test
5. Final QA check
6. ONLY THEN mark task complete

Never skip this pipeline. Never mark code tasks as done without it.

## Key Files

- `config.yaml` - Project stack, goals, audience, quality targets
- `status.yaml` - Phase, progress, blockers (source of truth)
- `decisions.md` - Append-only decision log

## Reference Files

Universal: anti-slop, copy-patterns, ux-patterns, testing, security, seo, content-strategy, docs-patterns, performance, accessibility, api-design, launch-checklist, project-templates

Stack-specific (in references/stacks/): nextjs, supabase, tailwind, shadcn, typescript, vercel, resend, react-native, electron, stripe, prisma, docker, astro, vue
