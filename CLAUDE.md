# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## What This Is

A multi-agent orchestration framework for Claude Code. Provides 15 specialized agents (planner, frontend, backend, tester, security, etc.) that hand off work to each other, with state persisted in YAML files for multi-session continuity.

**Framework-agnostic.** Works with any tech stack: Next.js, React Native, Electron, Astro, Vue, FastAPI, Go, etc. Agents adapt based on `config.yaml`.

**Drop-in portable.** Copy `.claude/` and this `CLAUDE.md` into any project root, run `/init`, start building.

## Commands

- `/init` - Initialize project (creates config.yaml, status.yaml, decisions.md)
- `/plan "description"` - Create project plan from requirements
- `/build` - Start or continue building (orchestrator delegates to agents)
- `/build frontend` - Jump to specific agent
- `/status` - Check current progress, blockers
- `/review` - Run code, security, accessibility, performance review
- `/ship` - Final checks, launch checklist, deployment prep

## Architecture

```
.claude/
├── agents/          # 15 agent definitions
├── commands/        # 6 slash commands
├── skills/          # Domain knowledge
│   └── project-system/
│       ├── SKILL.md           # Master skill (anti-slop, coordination)
│       ├── assets/            # config.yaml + status.yaml templates
│       └── references/        # 13 universal + 14 stack-specific guides
└── templates/       # 6 project type configs
```

Project state (created in your project root by /init):
- `config.yaml` - Tech stack, goals, audience, quality targets
- `status.yaml` - Phase, progress, blockers, milestones (source of truth)
- `decisions.md` - Append-only decision log with rationale

## Workflow

1. **Orchestrator** reads `status.yaml`, determines which agent to activate
2. **Active agent** performs specialized work, updates status
3. **Quality pipeline** runs after code: reviewer -> refactor -> tester -> QA
4. **Handoff** occurs when agent completes or needs different expertise
5. **Quality gates** block phase transitions until standards met

## Agent Roster (15)

| Agent | Role | Writes Code? |
|-------|------|-------------|
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
| reviewer | Code review, QA, sign-off | Read-only |
| copywriter | UI copy, microcopy | Yes (copy) |
| content-writer | Blog posts, marketing | Yes (content) |
| technical-writer | Docs, READMEs | Yes (docs) |
| seo | Meta tags, structured data | Yes |

## Project Templates

| Template | Use For |
|----------|---------|
| webapp | Standard web application |
| saas | SaaS product with billing |
| marketing-site | Marketing/landing pages |
| api | API/backend service |
| mobile-app | React Native / mobile |
| desktop-app | Electron / desktop |

## Critical Rules

1. **Always read `status.yaml` first** - never assume state
2. **Update status after actions** - keep current for session continuity
3. **Log decisions** in `decisions.md` with rationale
4. **Apply anti-slop standards** - no banned words, no gradient slop, no modal abuse
5. **Run quality pipeline** - code is NOT done until reviewed and tested
6. **Follow handoff protocol** - update status when switching agents

## Mandatory Quality Pipeline

Every piece of code must pass:
```
Code Written -> Reviewer -> Refactor (if needed) -> Tester -> QA -> Done
```

Never skip this. Never mark code tasks as done without it.

## Anti-Slop (Key Rules)

- NO gradient backgrounds, shadows everywhere, icons in colored boxes
- NO "seamless", "revolutionary", "powerful", "innovative" or other banned words
- NO modals for editing (use inline editing), no "Are you sure?" (use undo toast)
- EVERY stat needs context, EVERY chart needs a title, NO pie charts
- Specific beats generic. Numbers beat adjectives.

Full reference: `.claude/skills/project-system/references/anti-slop.md`

## Starting Work

Load the orchestrator: `.claude/agents/orchestrator.md`

It reads status and activates the appropriate agent based on current phase.
