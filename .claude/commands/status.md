---
description: Check current project progress, active agent, blockers, and milestone completion.
arguments:
  - name: view
    description: Type of status view (brief, blockers, metrics)
    required: false
---

# /status Command

> Check current project progress.

## Usage

```
/status             # Full status report
/status brief       # Quick summary
/status blockers    # Show only blockers
/status metrics     # Show quality metrics
```

## What This Shows

Read from `status.yaml` (source of truth).

### Full Status
```
## Project Status

**Project:** [name]
**Phase:** [current phase]
**Progress:** [X]% complete

### Progress by Phase
Planning:      ████████████████████ 100%
Design:        ████████████████████ 100%
Frontend:      ████████████░░░░░░░░  60%
Backend:       ████████░░░░░░░░░░░░  40%
Testing:       ░░░░░░░░░░░░░░░░░░░░   0%

### Current Work
Agent: [active agent]
Task: [current task]

### Milestones
[completed/pending with dates]

### Blockers
[any blockers or "None"]

### Recent Activity
[what was done recently]
```

### Brief Status
```
Phase: development | Progress: 52%
Current: Frontend working on dashboard
Blockers: None
```

## If No Status File

"No status.yaml found. Run `/init` to set up project tracking."

## Tips

- Run `/status` at the start of each session
- Check blockers before continuing work
