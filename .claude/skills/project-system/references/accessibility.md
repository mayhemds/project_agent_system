# Accessibility Reference

WCAG compliance, ARIA patterns, and assistive technology support.

---

## Core Principles (POUR)

### Perceivable
- Text alternatives for non-text content
- Captions for multimedia
- Sufficient color contrast
- Resizable text (up to 200%)

### Operable
- All functionality via keyboard
- Enough time to read/interact
- No seizure-inducing content (no flashing > 3/sec)
- Navigable structure

### Understandable
- Readable text
- Predictable behavior
- Input assistance (labels, errors, help)

### Robust
- Compatible with assistive tech
- Valid, semantic markup
- ARIA used correctly

---

## Color and Contrast

### Minimum Contrast Ratios
| Content Type | Ratio (AA) | Ratio (AAA) |
|--------------|-----------|-------------|
| Normal text (< 18px) | 4.5:1 | 7:1 |
| Large text (18px+ or 14px+ bold) | 3:1 | 4.5:1 |
| UI components, graphics | 3:1 | 3:1 |

### Don't Rely on Color Alone
```
BAD:  "Required fields are marked in red"
GOOD: "Required fields are marked with *"

BAD:  Error shown only with red border
GOOD: Error shown with icon + text + border
```

---

## Keyboard Navigation

### Focus Management
- Visible focus indicators (min 2px outline)
- Logical tab order (follows reading order)
- Focus trapping in modals/dialogs
- Return focus to trigger element after modal close
- Skip-to-main-content link

### Required Keys
| Action | Keys |
|--------|------|
| Navigate forward | Tab |
| Navigate backward | Shift + Tab |
| Activate button/link | Enter, Space |
| Close/Cancel | Escape |
| Navigate options | Arrow keys |
| Select option | Enter, Space |

### Skip Link
```html
<a href="#main" class="sr-only focus:not-sr-only">
  Skip to main content
</a>
```

---

## Semantic HTML

### Use Correct Elements
```
BAD:  <div onclick="...">Click me</div>
GOOD: <button onclick="...">Click me</button>

BAD:  <span class="heading">Title</span>
GOOD: <h2>Title</h2>
```

### Landmarks
```html
<header>       <!-- Banner -->
<nav>          <!-- Navigation -->
<main>         <!-- Main content -->
<aside>        <!-- Complementary -->
<footer>       <!-- Content info -->
<section>      <!-- Grouping with heading -->
```

### Heading Hierarchy
- One h1 per page
- Don't skip levels (h1 then h3)
- Headings describe content structure
- Use CSS for visual styling, not heading levels

---

## Forms

### Labels
```html
<!-- Explicit association -->
<label for="email">Email</label>
<input type="email" id="email" name="email">

<!-- Or wrapped -->
<label>
  Email
  <input type="email" name="email">
</label>
```

### Error Messages
```html
<input
  id="email"
  aria-invalid="true"
  aria-describedby="email-error"
>
<span id="email-error" role="alert">
  Enter a valid email address
</span>
```

### Required Fields
```html
<label for="name">
  Name <span aria-hidden="true">*</span>
</label>
<input id="name" required aria-required="true">
```

---

## Images

### Alt Text Guidelines
```html
<!-- Informative: describe content -->
<img src="chart.png" alt="Sales increased 25% from Q1 to Q2">

<!-- Decorative: empty alt -->
<img src="divider.png" alt="" role="presentation">

<!-- Linked: describe destination -->
<a href="/products">
  <img src="products.png" alt="View all products">
</a>

<!-- Complex: use figcaption -->
<figure>
  <img src="architecture.png" alt="System architecture overview">
  <figcaption>Three-tier architecture with React frontend, Node API, and PostgreSQL database.</figcaption>
</figure>
```

---

## Common ARIA Patterns

### Modal/Dialog
```html
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirm deletion</h2>
  <p>This action cannot be undone.</p>
  <button>Delete</button>
  <button>Cancel</button>
</div>
```
- Trap focus inside modal
- Return focus to trigger on close
- Close on Escape

### Tabs
```html
<div role="tablist" aria-label="Settings">
  <button role="tab" aria-selected="true" aria-controls="panel-1">General</button>
  <button role="tab" aria-selected="false" aria-controls="panel-2">Security</button>
</div>
<div role="tabpanel" id="panel-1">General settings...</div>
<div role="tabpanel" id="panel-2" hidden>Security settings...</div>
```
- Arrow keys navigate between tabs
- Tab key moves to panel content

### Menu
```html
<button aria-expanded="false" aria-haspopup="menu" aria-controls="menu-1">
  Options
</button>
<ul role="menu" id="menu-1" hidden>
  <li role="menuitem">Edit</li>
  <li role="menuitem">Delete</li>
</ul>
```

### Toast/Alert
```html
<div aria-live="polite" aria-atomic="true">
  Changes saved
</div>
```
- Use aria-live="polite" for non-urgent updates
- Use aria-live="assertive" for errors
- Use role="alert" for important announcements

### Toggle/Switch
```html
<button role="switch" aria-checked="false" aria-label="Dark mode">
  <span aria-hidden="true">Off</span>
</button>
```

---

## Motion and Animation

### Respect Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Safe Animation
- No flashing > 3 times per second
- Provide pause controls for auto-playing content
- Keep animations subtle and purposeful
- Parallax scrolling should respect prefers-reduced-motion

---

## Touch Targets

- Minimum size: 44x44px
- Minimum spacing: 8px between targets
- Consider fat-finger errors
- No hover-only interactions on mobile

---

## Testing Checklist

### Keyboard
```
[ ] All interactive elements focusable via Tab
[ ] Focus order matches visual order
[ ] Focus indicator visible at all times
[ ] Escape closes modals/menus
[ ] No keyboard traps
[ ] Skip-to-content link works
```

### Screen Reader
```
[ ] Page title announced correctly
[ ] Headings structure makes sense
[ ] Images have descriptive alt text
[ ] Form fields have labels
[ ] Errors are announced
[ ] Live regions update correctly
[ ] Landmarks present (nav, main, etc.)
```

### Visual
```
[ ] Color contrast passes (4.5:1 text, 3:1 UI)
[ ] Text resizable to 200% without breaking
[ ] Works without images
[ ] No info conveyed by color alone
[ ] Reduced motion respected
```

---

## Tools

- axe DevTools (browser extension)
- WAVE (browser extension)
- Lighthouse (Chrome DevTools)
- VoiceOver (Mac) / NVDA (Windows) / TalkBack (Android)
- Contrast checker: webaim.org/resources/contrastchecker
- HTML validator: validator.w3.org

---

## Audit Report Format

```markdown
## Accessibility Audit

### WCAG Level: [A / AA / AAA]

### Perceivable
- Color contrast: [PASS/FAIL] [details]
- Alt text: [PASS/FAIL] [details]
- Text resizing: [PASS/FAIL]

### Operable
- Keyboard navigation: [PASS/FAIL] [details]
- Focus management: [PASS/FAIL] [details]
- Touch targets: [PASS/FAIL]

### Understandable
- Form labels: [PASS/FAIL] [details]
- Error messages: [PASS/FAIL] [details]

### Robust
- Valid markup: [PASS/FAIL]
- ARIA usage: [PASS/FAIL] [details]

### Issues Found
1. [Severity] [WCAG criterion] - [description] - [fix]

### Recommendation
[Ship ready / Ship with conditions / Not ready]
```
