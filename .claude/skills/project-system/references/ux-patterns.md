# UX Patterns Reference

Design decisions and component selection.

---

## Choosing Layouts

```
Question: What does the user need to see/do on this page?

Answer drives layout:
- Quick scan of items → List/table
- Compare options → Side by side
- Complete a task → Form-focused
- See status → Dashboard
- Learn about product → Marketing flow

NOT: "What template should I use?"
```

---

## Component Selection

### Use the SIMPLEST option

```
Display text → Text (not card)
Group items → Spacing (not box)
Show list → List (not card grid)
Few options → Radio buttons (not dropdown)
Many options → Dropdown or search
Edit content → Inline editing (not modal)
Show details → Expand in place (not modal)
Confirm action → Undo toast (not modal)
```

### For displaying data

```
Single value → Text with label
List of items (simple) → Bulleted list
List of items (complex) → Table
Items need detail → Expandable rows
Comparison → Table or side-by-side
Trend over time → Line chart
Part of whole → Bar chart (NOT pie)
Single metric → Number with context
```

### For user input

```
Yes/No → Toggle or checkbox
One of 2-5 options → Radio buttons
One of many options → Dropdown
Multiple of many → Multi-select
Short text → Input
Long text → Textarea
Date → Date picker
File → Upload zone
```

### For actions

```
Primary action → Button (filled)
Secondary action → Button (outline)
Destructive action → Button (danger) + undo
Navigation → Link
```

---

## Navigation Patterns

### Top Navigation
```
When: Marketing sites, simple apps, 3-7 items
Structure: Logo | Nav Items | Actions
```

### Sidebar Navigation
```
When: Apps with many sections, dashboards
Mobile: Overlay or bottom nav
```

### Bottom Navigation (Mobile)
```
When: Mobile apps, 3-5 primary destinations
Rules: Maximum 5 items, icons + labels
```

### Tabs
```
When: Related content, mutually exclusive views
Rules: 2-6 tabs maximum, don't nest tabs
```

---

## Interaction Patterns

### Inline Editing (PREFERRED)
```
View mode:  Project Name [Edit]
Edit mode:  [Project Name    ] [Save] [Cancel]

Use instead of: Edit modals
```

### Slide-out Panel (PREFERRED)
```
When: Details, complex editing
Use instead of: Detail modals, edit modals
```

### Undo Toast (PREFERRED)
```
[Delete] → Item removed → Toast: "Deleted. [Undo]"

Use instead of: "Are you sure?" modals
```

### Expandable Row
```
▶ Row summary (click to expand)
▼ Row summary
  └─ Expanded details here
  
Use instead of: Detail modals
```

---

## Form Patterns

### Single Column Form (default)
```
Label
[Input                    ]

Label
[Input                    ]

            [Cancel] [Save]
```

### Inline Validation
```
[invalid@email    ]
⚠ Enter a valid email address

NOT: Validate only on submit
```

### Smart Input Widths
```
Email:     [                         ] (wide)
Phone:     [               ] (medium)
Zip:       [      ] (narrow)
```

---

## Feedback Patterns

### Toast Notifications
```
Position: Top-right or bottom-center
Duration: 3-5 seconds auto-dismiss
Action: Optional (Undo)
For: Success messages, confirmations
NOT for: Errors needing action
```

### Inline Messages
```
[Input with error    ]
⚠ This field is required

Position: Near the problem
```

### Empty States
```
┌─────────────────────────────────┐
│     No projects yet             │
│     Create your first project   │
│     to get started              │
│     [Create Project]            │
└─────────────────────────────────┘

Always: Explain + action
Never: Just "No data" or sad emoji
```

---

## Responsive Patterns

### Mobile-First
```
1. Design for mobile first
2. What's essential? Show that.
3. Add for larger screens, don't hide for smaller
```

### Touch Targets
```
Minimum: 44 x 44 pixels
Spacing: 8px between targets
```

### Layout Adaptation
```
Mobile: Single column, bottom nav
Tablet: Two columns
Desktop: Three+ columns, sidebar nav
```

---

## Accessibility Basics

### Color Contrast
```
Text: 4.5:1 minimum
Large text: 3:1 minimum
```

### Keyboard Navigation
```
All interactive elements focusable
Focus indicator visible
Tab order logical
```

### Screen Reader
```
Images have alt text
Form fields have labels
Buttons have names
Headings hierarchical
```
