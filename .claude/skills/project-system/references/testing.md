# Testing Reference

Testing strategies, patterns, and quality verification.

---

## Test Pyramid

```
         ┌─────────┐
        /   E2E    \        ~10%  Slow, brittle, critical paths only
       /───────────\
      /  Integration \      ~20%  API, auth flows, form submissions
     /─────────────────\
    /     Unit Tests    \   ~70%  Fast, focused, business logic
   /─────────────────────\
```

## Coverage Targets

| Type | Target |
|------|--------|
| Statements | 80% |
| Branches | 75% |
| Functions | 80% |
| Lines | 80% |

Higher for APIs (90%+). Lower for marketing sites (50%+).

---

## Naming Conventions

```
Pattern: should [expected behavior] when [condition]

describe('UserService', () => {
  describe('createUser', () => {
    it('should create user when input is valid')
    it('should throw ValidationError when email is invalid')
    it('should throw ConflictError when email already exists')
  })
})
```

---

## Unit Testing

### AAA Pattern (Arrange, Act, Assert)

```
it('should increment counter', () => {
  // Arrange
  const counter = new Counter(0)

  // Act
  counter.increment()

  // Assert
  expect(counter.value).toBe(1)
})
```

### What to Unit Test

- Utility functions
- Validation logic
- Business rules
- Data transformations
- State management logic

### What NOT to Unit Test

- Framework code
- Simple getters/setters
- Third-party library behavior
- Implementation details

---

## Integration Testing

### API Endpoint Tests

```
describe('POST /api/users', () => {
  it('creates a user with valid data', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', name: 'Test' })

    expect(response.status).toBe(201)
    expect(response.body.data.email).toBe('test@example.com')
  })

  it('returns 400 for invalid email', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'not-an-email' })

    expect(response.status).toBe(400)
  })
})
```

### Auth Flow Tests

- Login with valid credentials → success
- Login with invalid credentials → error
- Access protected route without auth → 401
- Access other user's data → 403
- Token expiration → refresh or re-auth

---

## E2E Testing

### Critical Paths (always test)

- Sign up flow
- Login flow
- Main feature happy path
- Payment flow (if applicable)
- Logout

### Example (Playwright)

```
test('user can sign up', async ({ page }) => {
  await page.goto('/signup')
  await page.fill('[name="email"]', 'new@example.com')
  await page.fill('[name="password"]', 'password123')
  await page.click('[type="submit"]')
  await expect(page).toHaveURL('/dashboard')
})
```

### Accessibility E2E

```
test('page has no accessibility violations', async ({ page }) => {
  await page.goto('/')
  const results = await new AxeBuilder({ page }).analyze()
  expect(results.violations).toEqual([])
})
```

---

## Testing with Mocks

### Mock External Services

```
// Mock API calls, not internal logic
const mockApi = {
  getUser: jest.fn().mockResolvedValue({ id: '1', name: 'Test' }),
  createUser: jest.fn().mockResolvedValue({ id: '2', name: 'New' }),
}
```

### Mock Rules

- Mock at system boundaries (APIs, databases, file system)
- Don't mock internal functions
- Don't over-mock — if everything is mocked, you're testing nothing
- Use factories for test data, not hardcoded objects

---

## Test Quality Rules

### Do
- Test behavior, not implementation
- One assertion per test (focused)
- Use descriptive test names
- Test edge cases and error paths
- Keep tests fast (< 100ms for unit)
- Tests should be independent (no shared state)

### Don't
- Use sleep/delays (use waitFor patterns)
- Share state between tests
- Test private methods directly
- Write tests that pass when code is wrong
- Leave flaky tests in CI

---

## Anti-Slop Quick Check (2 minutes)

### Visual Scan (30 sec)
```
[ ] No gradients?
[ ] No shadows everywhere?
[ ] No icons in colored boxes?
[ ] Consistent border-radius?
```

### Interaction Check (30 sec)
```
[ ] No modals for editing?
[ ] No "Are you sure?" dialogs?
[ ] Undo pattern for deletes?
```

### Copy Scan (30 sec)
```
[ ] No "seamless", "powerful", "innovative"?
[ ] Specific claims, not hype?
```

### Data Check (30 sec)
```
[ ] Stats have context?
[ ] No pie charts?
[ ] Charts have titles?
```

---

## Pre-Launch Test Checklist

### Functionality
```
[ ] Sign up works
[ ] Login works
[ ] Logout works
[ ] Password reset works
[ ] Main feature works end-to-end
[ ] Error states handled
[ ] Empty states handled
[ ] Loading states present
```

### Security
```
[ ] Auth required for protected routes
[ ] Users only see own data
[ ] No secrets in client code
[ ] Input validated server-side
```

### Accessibility
```
[ ] Keyboard navigation works
[ ] Focus visible
[ ] Alt text on images
[ ] Labels on form fields
[ ] Color contrast 4.5:1+
```

### Performance
```
[ ] Page loads < 3s
[ ] Images optimized
[ ] No layout shift
```

---

## Test Plan Template

```markdown
## Test Plan: [Feature Name]

### Scope
What is being tested.

### Unit Tests
- [ ] [Function/module] - [behavior]
- [ ] [Function/module] - [error case]

### Integration Tests
- [ ] [Endpoint/flow] - [happy path]
- [ ] [Endpoint/flow] - [error path]

### E2E Tests
- [ ] [User flow] - [expected outcome]

### Edge Cases
- [ ] [Edge case description]

### Not Testing (and why)
- [What] - [reason]
```

---

## Review Output Format

```markdown
## QA Review: [Feature/Component]

### Status: PASS | CHANGES NEEDED | FAIL

### Anti-Slop
- Visual: pass/fail
- Copy: pass/fail
- Interactions: pass/fail

### Security
- Auth: pass/fail
- Authorization: pass/fail
- Validation: pass/fail

### Issues
1. [Critical] Description - How to fix
2. [Major] Description - How to fix

### Ready to Ship: Yes/No
```
