---
description: Start or continue building the project. Activates the orchestrator to determine which agent should work next based on current status.
arguments:
  - name: target
    description: Optional target to focus on (design, frontend, backend, devops)
    required: false
---

# /build Command

> Start or continue building the project.

## Usage

```
/build              # Continue from current state
/build design       # Jump to design phase
/build frontend     # Work on frontend
/build backend      # Work on backend
/build devops       # Work on infrastructure
```

## What This Does

1. Loads current status from `status.yaml`
2. Activates the **Orchestrator**
3. Orchestrator determines next agent based on phase
4. Agent performs work
5. Quality pipeline runs after code is written
6. Status updated throughout

## Build Flow

```
/build
  ↓
Orchestrator loads status
  ↓
Determines phase & next agent
  ↓
Activates appropriate agent
  ↓
Agent performs work
  ↓
Quality pipeline (reviewer → tester → QA)
  ↓
Updates status
  ↓
Hands off to next agent (or completes)
```

## Phase Order

1. **Planning** → planner
2. **Design** → ux-designer
3. **Development** → frontend, backend, devops
4. **Testing** → tester
5. **Review** → security, accessibility, performance, reviewer
6. **Shipping** → reviewer (sign-off), devops (deploy)

## Multi-Session Resumption

```
Session 1: /build → Starts design, creates wireframes (session ends)
Session 2: /build → Reads status, continues from where it left off
Session 3: /build → Continues building...
```

The build command reads `status.yaml` at every start to know where to resume.

## Blockers

If blockers exist, the orchestrator will report them and offer options to resolve, skip, or change approach.

## Tips

- Check `/status` before building
- Address blockers promptly
- Let agents complete their work before switching
- Override with specific targets if needed (`/build frontend`)
