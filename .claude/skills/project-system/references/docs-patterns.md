# Documentation Patterns Reference

Standards for writing clear technical documentation.

---

## Documentation Types

| Type | Purpose | Audience |
|------|---------|----------|
| README | Project overview, quick start | Everyone |
| API Docs | Endpoint reference | Developers |
| Guides | How to accomplish tasks | Users |
| Reference | Complete specifications | Developers |
| Tutorials | Step-by-step learning | New users |
| Changelog | What changed and when | Everyone |

---

## Writing Principles

### 1. Know Your Audience
- What do they already know?
- What are they trying to do?
- What's their skill level?

### 2. Be Direct
```
BAD:  "In order to configure the application, you will need to..."
GOOD: "To configure the app:"

BAD:  "It should be noted that..."
GOOD: "Note:"
```

### 3. Show, Don't Just Tell
```
BAD:  "The function accepts a string parameter"
GOOD: "The function accepts a string parameter:
       getUserById('user_123')"
```

---

## README Structure

```markdown
# Project Name

One-line description of what this does.

## Quick Start

Minimum steps to get running (3 commands max).

## Installation

Detailed installation steps.

## Usage

Basic usage examples with real values.

## Configuration

Configuration options (table format).

## API Reference

Link to full docs or inline reference.

## Contributing

How to contribute.

## License

License type.
```

### README Anti-Slop
- No badges nobody clicks
- No "A powerful, scalable solution for..."
- Real content, not placeholder text
- Quick start should actually work

---

## API Documentation

### Endpoint Template
```markdown
## POST /api/users

Create a new user.

### Request

**Headers:**
| Header | Required | Description |
|--------|----------|-------------|
| Authorization | Yes | Bearer token |

**Body:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| email | string | Yes | User email |
| name | string | No | Display name |

**Example:**
{
  "email": "user@example.com",
  "name": "Jane"
}

### Response

**Success (201):**
{
  "data": {
    "id": "user_123",
    "email": "user@example.com"
  }
}

**Error (400):**
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format"
  }
}
```

---

## Code Examples

### Good Examples
- Complete and runnable
- Show expected output
- Include error handling
- Use realistic data (not "foo", "bar")

### Bad Examples
- Incomplete snippets
- Placeholder values
- No output shown
- Missing error cases

---

## Component Documentation

```markdown
## ComponentName

Brief description of what it does.

### Props
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| title | string | required | Heading text |
| variant | 'default' | 'compact' | 'default' | Display style |

### Usage
[code example]

### Variants
[examples of each variant]

### Accessibility
- Keyboard: [behavior]
- Screen reader: [announcements]
- ARIA: [attributes used]
```

---

## Setup Guide Structure

```markdown
# Setup Guide

## Prerequisites
What you need before starting.

## Steps

### 1. [First Step]
What to do and why.

### 2. [Second Step]
What to do and why.

## Verify Installation
How to confirm it worked.

## Troubleshooting
Common problems and solutions.
```

---

## Changelog Format

```markdown
# Changelog

## [1.2.0] - 2025-01-15

### Added
- User profile editing
- Export to CSV

### Changed
- Dashboard loads 2x faster

### Fixed
- Login redirect loop on Safari
- Date picker timezone issue

### Removed
- Deprecated v1 API endpoints
```

Follow Keep a Changelog format.

---

## Style Guide

### Formatting
- Use code blocks for all code
- Use tables for options/parameters
- Use numbered lists for ordered steps
- Use bullet lists for unordered items

### Tone
- Second person ("you")
- Active voice
- Present tense
- Friendly but professional

### Common Terms
| Use | Don't Use |
|-----|-----------|
| select | click on |
| enter | type in |
| run | execute |
| app | application |

---

## Checklist

- [ ] Title clearly describes content
- [ ] Introduction explains purpose
- [ ] Examples are complete and tested
- [ ] Code snippets actually work
- [ ] All links work
- [ ] Tables are properly formatted
- [ ] No placeholder text
- [ ] Version/date included if relevant
- [ ] No AI filler ("It's worth noting that...")
