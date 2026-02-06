# Complete Anti-Slop Reference

The definitive guide to AI-generated garbage. What it looks like, why it's bad, what to do instead.

---

## 1. Visual Slop

What AI defaults to when generating UI.

### The Slop

```
GRADIENTS
- Purple/blue/pink gradient backgrounds
- Gradient text on headings
- Gradient buttons
- Gradient borders

SHADOWS
- shadow-sm on everything
- shadow-md on cards
- shadow-lg because why not
- Multiple shadows stacked

SHAPES & DECORATION
- Icons inside colored circles/boxes
- Decorative blobs and waves
- Floating shapes in background
- Border + shadow + rounded + gradient together

ROUNDED CORNERS
- rounded-2xl on everything
- rounded-3xl because more = better
- rounded-full on squares
- Inconsistent radius across components

COLORS
- Rainbow colors without meaning
- Blue for everything
- Colored backgrounds on sections
- Badge colors that mean nothing

EMOJIS AS DESIGN
- 🚀 next to features
- ✨ for emphasis
- 💡 for tips
- 🎉 for success states
```

### The Fix

```
BACKGROUNDS
- bg-background (page)
- bg-card (cards)
- bg-muted (subtle sections)
- Solid colors only
- Gradients ONLY if brand-specified

SHADOWS
- None by default
- shadow-sm for dropdowns
- shadow-md for modals/popovers
- That's it

ICONS
- Inline with text
- Functional purpose only
- Consistent size (h-4 w-4 or h-5 w-5)
- No wrappers

CORNERS
- Pick ONE radius: rounded-md or rounded-lg
- Use consistently everywhere
- rounded-full only for avatars/pills

COLORS
- Semantic meaning only
- red = error/destructive
- green = success
- yellow = warning
- blue = info/link
- That's the whole palette
```

---

## 2. Layout Slop

AI loves templates.

### The Slop

```
THE TEMPLATE
Every page: Hero → 3 cards → features grid → testimonials → CTA
Every dashboard: 4 stat cards → chart → table
Every landing: big text → screenshot → bullet points

CARDS EVERYWHERE
- Single line of text? Card.
- Two items? Cards.
- Just need spacing? Card.
- Everything wrapped in rounded borders

CENTERING
- Center all the text
- Center the layout
- Center the buttons
- Nothing left-aligned

SPACING
- Massive padding everywhere
- py-24 between sections
- Items floating in white space
- Inconsistent gaps

NAVIGATION
- 15+ items in sidebar
- Mega menus with everything
- 3 levels deep
- Icons for every single item
```

### The Fix

```
LAYOUT FOLLOWS CONTENT
- Different content = different layout
- Ask: what does user need to see/do?
- Simplest structure that works

CONTAINERS
- Most things don't need a card
- Group with spacing, not boxes
- Cards only for: distinct items in a list, clickable surfaces

ALIGNMENT
- Left-align by default
- Reading direction matters
- Center only for: hero text, empty states, modals

SPACING
- Consistent scale (4, 8, 12, 16, 24, 32)
- Tighter than you think
- Related things closer together

NAVIGATION
- Max 5-7 main items
- Group related items
- Match user's mental model
- Don't show everything at once
```

---

## 3. Copy Slop

The words AI reaches for first.

### Banned Words

```
HYPE WORDS (never use)
- seamless
- revolutionary
- powerful
- innovative
- cutting-edge
- next-generation
- world-class
- game-changing
- disruptive
- groundbreaking
- state-of-the-art

CORPORATE SPEAK (never use)
- leverage
- synergy
- streamline
- elevate
- empower
- optimize
- maximize
- utilize (just say "use")
- facilitate
- implement (unless technical)

FILLER WORDS (never use)
- robust
- scalable (unless technical context)
- comprehensive
- dynamic
- flexible
- intuitive
- best-in-class
- holistic
- end-to-end
- turnkey
```

### Banned Patterns

```
OPENINGS (never start with)
- "In today's fast-paced world..."
- "As we all know..."
- "It goes without saying..."
- "In the ever-evolving landscape..."
- "Now more than ever..."

HYPE PHRASES (never use)
- "Transform your [X]"
- "Take your [X] to the next level"
- "Unlock the power of..."
- "Supercharge your..."
- "Revolutionize the way you..."
- "The future of [X] is here"
- "Say goodbye to [X] and hello to [Y]"

EMPTY PROMISES
- "Everything you need"
- "All-in-one solution"
- "The only [X] you'll ever need"
- "Works like magic"
```

### The Fix

```
BE SPECIFIC
"Powerful analytics" → "See which posts get the most clicks"
"Seamless integration" → "Connects to Slack in 2 clicks"
"Revolutionary platform" → "Create landing pages in 10 minutes"
"Streamline your workflow" → "Skip the copy-paste between apps"
"Leverage AI" → "AI writes your first draft"

USE NUMBERS
"Fast" → "Loads in 0.8 seconds"
"Affordable" → "Starts at $10/month"
"Popular" → "Used by 10,000 teams"
"Save time" → "Save 4 hours per week"

SAY WHAT IT DOES
Not what it "empowers" or "enables" or "helps you achieve"
Just what it does.
```

---

## 4. Interaction Slop

AI defaults to patterns that feel "complete" but annoy users.

### Modal Abuse

```
AI USES MODALS FOR:
- Editing anything
- Viewing details
- Creating new items
- Settings
- Confirmations ("Are you sure?")
- Success messages
- Forms with 10 fields
- Nested modals

ACTUALLY USE MODALS FOR:
- Login/auth (temporary, security)
- Lightbox (images, video)
- Legal/compliance (must acknowledge)
- Truly destructive + no undo (delete account)

That's it. 4 use cases.
```

### The Alternatives

```
EDITING
Slop: Click edit → modal opens → edit → save → modal closes
Fix: Click edit → inline form appears → save → done

VIEWING DETAILS
Slop: Click item → modal with details
Fix: Click item → expand row OR slide-out panel

CREATING
Slop: Click "New" → modal form
Fix: Dedicated page OR inline form at top of list

CONFIRMING ACTIONS
Slop: "Are you sure?" → Yes/No
Fix: Do action → Toast "Done. [Undo]"

SUCCESS
Slop: "Success!" modal with OK button
Fix: Toast notification, auto-dismiss

SETTINGS
Slop: Settings modal
Fix: Settings page with inline editing
```

### Form Slop

```
THE SLOP
- Every input same width
- Labels far from inputs
- Validation only on submit
- "Invalid input" (what's invalid?)
- Form clears on error
- No indication of required fields
- Placeholder as label
- 25 fields on one page

THE FIX
- Width matches content (email wide, zip narrow)
- Label directly above or beside input
- Validate on blur
- Errors say HOW to fix
- Preserve all input on error
- Required fields marked
- Placeholder is example, not label
- Break long forms into steps
```

---

## 5. Data & Dashboard Slop

AI loves showing numbers without meaning.

### The Slop

```
STAT CARDS
- 4 cards at top of every dashboard
- "1,234" (vs what? good or bad?)
- No trend, no comparison
- Random metrics nobody asked for

CHARTS
- Pie chart for everything
- Chart with no title
- No axis labels
- Rainbow colors
- 3D effects
- Legend with 15 items

DATA TABLES
- Every column shown
- No sorting
- No filtering
- Pagination but no count
- Actions hidden in menu
```

### The Fix

```
STATS NEED CONTEXT
"1,234" → "1,234 users (+12% vs last month)"
Show: vs goal, vs last period, trend direction
If number can't have context, question why it's shown

CHARTS NEED PURPOSE
Every chart must answer: "What question does this answer?"
Must have: title, axis labels, legend if multiple series
Bar charts > pie charts (always)
Semantic colors: red=bad, green=good

DATA LEADS TO ACTION
Click row → see details
Sort by what matters
Filter by what users need
Show total count
Actions visible, not buried
```

---

## 6. State Slop

AI forgets about empty, loading, and error states.

### Empty States

```
THE SLOP
- "No data found"
- "No results"
- Blank white space
- Sad emoji 😢
- "Oops, nothing here!"

THE FIX
- Explain what will appear here
- Provide action to add first item
- Different message for:
  - "You haven't created any yet" (action: create)
  - "No results match your search" (action: clear filters)
- Helpful, not apologetic

EXAMPLE
Bad: "No projects found 😢"
Good: "No projects yet. Create your first project to get started. [Create Project]"
```

### Loading States

```
THE SLOP
- Blank screen while loading
- Generic spinner forever
- Full page loader for one component
- No indication of what's loading
- Loader but content never appears

THE FIX
- Skeleton screens for layout/content
- Spinner for actions (button loading)
- Show WHAT is loading ("Loading projects...")
- Optimistic UI where safe (toggles, likes)
- Progressive loading (show content as it arrives)
- Timeout + error if too long
```

### Error States

```
THE SLOP
- "Something went wrong"
- "Error"
- "Oops!"
- Red banner at top of page
- Technical jargon / stack trace
- No way to recover

THE FIX
- Error near the problem (inline for fields)
- Plain language: what went wrong
- Actionable: how to fix it
- Recovery path: retry button, go back, contact support
- Log technical details server-side

EXAMPLE
Bad: "Error: ETIMEDOUT"
Good: "Couldn't save your changes. Check your internet connection and try again. [Retry]"
```

---

## 7. Code Slop

What AI-generated code looks like.

### Structure Slop

```
OVER-ENGINEERING
- Abstract factory for one button variant
- Context provider for 3 components
- Custom hook for a single useState
- 15 files for a contact form
- Utils folder with 50 functions

FOLDER CHAOS
- components/ui/buttons/primary/index.tsx
- Folders with one file
- 5 levels of nesting
- No clear organization principle

GOD COMPONENTS
- 500 line component
- 15 useState calls
- Does everything
- Impossible to test
```

### The Fix

```
SIMPLE BY DEFAULT
- Start with the obvious solution
- Add abstraction when you have 3+ examples
- One file until it needs to split
- Flat over nested

FILE STRUCTURE
- components/button.tsx (not components/ui/buttons/primary/index.tsx)
- Group by feature when large
- Max 2-3 levels deep

COMPONENT SIZE
- Under 200 lines preferred
- One job per component
- Extract when reused or too complex
- Named after what it does
```

### Code Quality Slop

```
COMMENTS
- "// This function gets users" above getUsersfunction
- Outdated comments
- Commented-out code left in
- No comments where actually needed

ERROR HANDLING
- try/catch that swallows errors
- catch (e) { console.log(e) }
- No error boundaries
- Same error message everywhere

LEFTOVERS
- console.log everywhere
- Unused imports
- Unused variables
- TODO comments from 6 months ago
- debugger statements
```

### The Fix

```
COMMENTS
- Explain WHY, not WHAT
- Delete commented code
- Update or delete outdated comments
- Complex logic gets explanation

ERROR HANDLING
- Catch specific errors
- Log details, show human message
- Different handling for different errors
- Error boundaries at route level

CLEAN CODE
- No console.log in production
- ESLint to catch unused code
- Address TODOs or delete them
- Review before commit
```

---

## 8. Response Slop

How AI talks.

### The Slop

```
OPENINGS
- "Great question!"
- "That's a really interesting question."
- "I'd be happy to help you with that!"
- "Absolutely!"
- "Sure thing!"
- "Of course!"

CLOSINGS
- "Let me know if you have any other questions!"
- "Hope this helps!"
- "Feel free to reach out if you need anything else!"
- "Happy to help further!"
- "Is there anything else you'd like to know?"

PADDING
- Repeating the question back
- "So, to summarize what you're asking..."
- Excessive caveats before answering
- "While I can't give specific advice..."
- "This is a complex topic, but..."

FORMATTING ABUSE
- Headers for a 3-line response
- Bullet points for 2 items
- **Bold** every other word
- Tables for simple lists
- Markdown in casual conversation
```

### The Fix

```
JUST ANSWER
- Start with the answer
- Skip the pleasantries
- No need to repeat the question
- Caveats only when genuinely necessary

NATURAL TONE
- Write like you talk
- Match the user's energy
- Formal question = formal answer
- Casual question = casual answer

FORMAT APPROPRIATELY
- Short answer = no formatting
- List of 5+ items = bullets okay
- Complex data = table okay
- Headers only for long documents

END CLEAN
- Stop when you're done
- Offer follow-up only if relevant
- No generic "let me know!"
```

---

## 9. Architecture Slop

Over-building from the start.

### The Slop

```
PREMATURE ABSTRACTION
- "Let's create a component library first"
- "We need a design system"
- "Let's set up a monorepo"
- Generic solutions for specific problems
- "What if we need to scale?"

FILE EXPLOSION
- services/userService.ts (one function)
- utils/helpers/stringHelpers/capitalize.ts
- types/interfaces/models/user/IUser.ts
- Barrel exports everywhere

CONFIGURATION OBSESSION
- Everything in config files
- Environment variables for everything
- "Make it configurable"
- Feature flags for everything

PATTERN WORSHIP
- Repository pattern for simple queries
- Factory pattern for one variation
- Observer pattern for one subscription
- Dependency injection framework for 10 files
```

### The Fix

```
START SIMPLE
- Solve the problem in front of you
- Add abstraction after 3 similar things
- Delete code easier than maintaining it
- "What's the simplest thing that works?"

FLAT STRUCTURE
- components/button.tsx
- lib/db.ts
- app/actions/user.ts
- Nest only when it helps navigation

INLINE FIRST
- Hardcode first, extract when repeated
- Colocate until it needs sharing
- Config for actual configuration, not preferences

PATTERNS WHEN NEEDED
- Pattern should solve a real problem
- Not "just in case"
- Not "this is how it's done"
- Simple functions often enough
```

---

## 10. Documentation Slop

AI writes docs to fill space.

### The Slop

```
USELESS README
- Project name repeated 3 times
- Badges nobody clicks
- "A powerful, scalable solution for..."
- Getting started: "npm install"
- That's it

MEANINGLESS COMMENTS
- /** Gets user */ getUser()
- // Loop through items (above a for loop)
- // Increment counter (above i++)
- JSDoc with no actual info

WALLS OF TEXT
- 500 word introduction
- No examples
- No code snippets
- Theory but no practice

OUTDATED
- Wrong API endpoints
- Old screenshots
- Deprecated features documented
- "Coming soon" from 2 years ago
```

### The Fix

```
README THAT HELPS
1. One line: what it does
2. Quick start: 3 commands to run
3. Example: screenshot or code
4. Docs link: for more detail

COMMENTS THAT ADD VALUE
- WHY, not WHAT
- Complex business logic explained
- Workarounds with links to issues
- Examples for non-obvious usage

SHOW DON'T TELL
- Code examples > descriptions
- Real values > placeholder text
- Working snippets > pseudocode
- Copy-paste should work

KEEP CURRENT
- Delete outdated sections
- Review docs when changing features
- Automated doc generation where possible
- Date on guides ("Updated Jan 2024")
```

---

## 11. Generation Slop

What AI leaves behind.

### The Slop

```
PLACEHOLDER CONTENT
- Lorem ipsum
- "Your content here"
- "Example Company"
- placeholder@email.com
- "John Doe"
- 555-555-5555
- 123 Main Street

TODO GRAVEYARD
- // TODO: implement this
- // FIXME: this is broken
- // HACK: temporary fix
- All from months ago
- Never addressed

EXAMPLE DATA IN PRODUCTION
- const users = ["Alice", "Bob"]
- Sample products
- Test API keys
- Debug data

GENERIC NAMES
- data, item, thing, stuff
- temp, tmp, x, y
- handler, manager, processor
- MyComponent, TestComponent
```

### The Fix

```
NO PLACEHOLDERS IN COMMITTED CODE
- Real content or real fallback
- Empty state > placeholder text
- Error if missing required content
- Review for placeholder patterns

TODOS ARE TASKS
- Create actual ticket/issue
- Include context in TODO
- Delete if not doing
- Time-box: fix in this PR or delete

MEANINGFUL NAMES
- users > data
- products > items  
- handleSubmit > handler
- onClick > handler
- Names describe content/purpose

REAL TEST DATA
- Faker for realistic fake data
- Seed scripts for development
- No hardcoded test data in source
```

---

## Quick Reference Card

Copy this for subagent prompts:

```
## ANTI-SLOP RULES

### Visual
- NO gradients (unless brand-specified)
- NO shadows on everything (only elevation)
- NO icons in colored boxes
- NO decorative emojis
- Consistent border-radius

### Copy
BANNED: seamless, revolutionary, powerful, innovative, cutting-edge, leverage, synergy, streamline, elevate, empower, robust, best-in-class
BANNED PATTERNS: "Transform your X", "Take X to the next level", "In today's fast-paced"
RULE: Specific > generic. Numbers > adjectives.

### Interactions  
- NO modals for editing → inline editing
- NO modals for details → expand/panel
- NO "Are you sure?" → undo toast
- NO success modals → toast notification

### Forms
- Width matches content
- Validate on blur
- Errors explain how to fix
- Never clear on error

### Data
- Every stat needs context (vs what?)
- No pie charts
- Every chart needs title + explanation

### States
- Empty: explain + action (not "No data 😢")
- Loading: skeleton for content, spinner for actions
- Error: what's wrong + how to fix + recovery

### Code
- Simple over clever
- Flat over nested
- Delete over comment-out
- Name things clearly
- No placeholders in committed code

### Responses
- Just answer (no "Great question!")
- Format only when helpful
- Stop when done (no "Let me know if...")
```
