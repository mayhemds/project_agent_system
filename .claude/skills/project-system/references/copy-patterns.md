# Copy & Writing Patterns

Standards for UI copy, microcopy, and content.

---

## UI Copy Patterns

### Headlines

```
BAD:
"The Revolutionary Platform for Modern Teams"
"Powerful Solutions for Your Business Needs"

GOOD:
"Create landing pages in 10 minutes"
"Track expenses without the spreadsheet"
"Send invoices that get paid 2x faster"

PATTERN: [Verb] + [specific outcome] + [specific detail]
```

### Subheadlines

```
BAD:
"Leverage cutting-edge technology to streamline your workflow"

GOOD:
"No design skills needed. Pick a template, add your content, publish."

PATTERN: Expand on the how or the benefit. Stay specific.
```

### Button Labels

```
BAD:
"Submit", "Click Here", "Learn More"

GOOD:
"Create account", "Start free trial", "Download PDF", "Save changes"

PATTERN: [Verb] + [what happens]
```

### Form Labels

```
BAD:
"Input your electronic mail address"

GOOD:
"Email", "Company name", "Password"

PATTERN: Single word or short phrase.
```

### Error Messages

```
BAD:
"Invalid input", "Error", "Something went wrong"

GOOD:
"Enter a valid email address"
"Password must be at least 8 characters"
"This email is already registered. Try logging in instead."

PATTERN: [What's wrong] + [How to fix it]
```

### Empty States

```
BAD:
"No data found", "Nothing here yet 😢"

GOOD:
"No projects yet. Create your first project to get started."
"No results match your filters. Try adjusting your search."

PATTERN: [What's empty] + [What to do]
```

### Success Messages

```
BAD:
"Success!", "Your action was completed successfully!"

GOOD:
"Project created", "Changes saved", "Email sent to team@example.com"

PATTERN: [What happened] (+ [relevant detail])
```

### Loading States

```
BAD:
"Loading...", "Please wait..."

GOOD:
"Loading projects...", "Sending email...", "Saving changes..."

PATTERN: [Verb]ing [what]...
```

---

## Voice & Tone

### Professional
```
"Create your account"
"Your changes have been saved"
"Enter your email address"
```

### Casual
```
"Let's get you set up"
"All saved!"
"What's your email?"
```

### Technical
```
"Initialize repository"
"Authentication token expired"
"Configure environment variables"
```

---

## Terminology Rules

```
1. Pick ONE name per concept
2. Use it everywhere
3. Document in project-context.yaml

Example:
  term: "workspace"
  meaning: "Container for projects"
  wrong: ["space", "folder", "area", "container"]
```

---

## Content Types

### Blog Post Structure

```
TITLE: Specific, actionable
"How We Reduced Email Bounce Rate from 12% to 2%"

INTRO: Start with result or problem, not preamble
"Last month, 12% of our emails were bouncing. This week, it's 2%."

BODY:
- Specific steps
- Real examples
- Actual numbers
- Clear sections

CONCLUSION: Clear next action
"Start with step 1. If you get stuck, email me."
```

### Documentation Structure

```
README:
1. One line: what it does
2. Quick start: 3 commands to run
3. Example: screenshot or code
4. Docs link: for more detail

API DOCS:
- Endpoint
- Method
- Parameters (with types)
- Response (with example)
- Errors
```

---

## Headlines That Work

```
How [we/I] [achieved specific result]
→ "How We Grew to 10K Users Without Paid Ads"

[Number] [things] to [achieve outcome]
→ "5 Changes That Cut Our AWS Bill by 60%"

[Outcome] in [timeframe]
→ "Set Up CI/CD in 15 Minutes with GitHub Actions"

Why [we/I] [made decision]
→ "Why We Moved from Firebase to Supabase"
```

---

## Quick Reference

| Element | Pattern | Example |
|---------|---------|---------|
| Headline | Verb + outcome | "Send invoices in 30 seconds" |
| Button | Verb + object | "Create project" |
| Error | Problem + fix | "Enter a valid email" |
| Empty | What + action | "No items. Add your first." |
| Success | What happened | "Changes saved" |
| Loading | Verb-ing what | "Loading posts..." |
