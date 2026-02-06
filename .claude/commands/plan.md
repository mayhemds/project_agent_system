---
description: Create a project plan from requirements. Use when starting a new project or need to define tech stack, milestones, and architecture.
arguments:
  - name: description
    description: What to build (e.g., "a SaaS dashboard for analytics")
    required: false
---

# /plan Command

> Create a project plan from requirements.

## Usage

```
/plan "Build a SaaS dashboard for analytics"
/plan "Create a marketing site for our product"
/plan                    # Show existing plan
```

## What This Does

1. Activates the **Planner** agent
2. Gathers requirements (may ask clarifying questions)
3. Selects technology stack
4. Defines project structure and architecture
5. Creates milestones
6. Updates `config.yaml` and `status.yaml`
7. Logs decisions in `decisions.md`
8. Prepares for design phase

## Process

### Step 1: Requirements
The planner will ask about:
- Project goals and target users
- Key features and constraints
- Timeline and technical preferences

### Step 2: Technology Selection
Recommends stack based on project type templates:
- `webapp.yaml` - Standard web application
- `saas.yaml` - SaaS product with billing
- `marketing-site.yaml` - Marketing/landing pages
- `api.yaml` - API/backend service
- `mobile-app.yaml` - React Native mobile app
- `desktop-app.yaml` - Electron desktop app

### Step 3: Planning Output
Creates/updates:
- `config.yaml` with stack and goals
- `status.yaml` with milestones
- `decisions.md` with architecture decisions

## If Plan Already Exists

Show the full plan from `status.yaml`:
- All phases with completion status
- Milestones with dates
- Current phase highlighted
- Blockers listed

## After Planning

- Run `/status` to see the plan
- Run `/build` to start execution
- Modify `config.yaml` directly if needed
