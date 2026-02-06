---
description: Initialize project - create state files, plan phases, or scan existing code
---

# /init Command

Initialize or scan a project.

## For New Project

Ask user:
1. What are you building? (one sentence)
2. Who is it for? (specific user)
3. What's the ONE thing it must do?
4. Timeline? (MVP deadline)
5. Any decisions already made? (stack, design, etc.)

Then:
1. Create `config.yaml` from template (see `.claude/skills/project-system/assets/`)
2. Create `status.yaml` from template
3. Create `decisions.md` (empty log)
4. Fill in project details from answers
5. Propose phase breakdown
6. Report: "Project initialized. Run `/status` to see plan, `/build` to start."

## For Existing Project

Ask user to share:
- Key files (package.json, schema, main components)
- What's already working
- What's left to build

Then:
1. Scan the codebase
2. Identify stack and patterns
3. Create `config.yaml` with detected stack
4. Create `status.yaml` with completed work marked
5. Propose remaining phases
6. Report status

## Phase Template

```
Phase 1: Foundation
- Project setup
- Database setup
- Auth flow
- Deploy pipeline

Phase 2: Core Feature
- [The ONE thing]

Phase 3: Supporting Features
- [Secondary features]

Phase 4: Polish
- Empty states
- Loading states
- Error handling
- Mobile responsive

Phase 5: Launch
- Landing page
- SEO
- Documentation
- Final review
```

## Output

After initialization:
- Show project summary
- Show phase breakdown
- Ask what to work on first
- Suggest: "Run `/plan` for detailed planning or `/build` to start building"
