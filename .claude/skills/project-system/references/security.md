# Security Reference

Security best practices for web and mobile applications.

---

## OWASP Top 10 Summary

| Risk | Prevention |
|------|------------|
| Broken Access Control | Auth checks on all endpoints, verify ownership |
| Cryptographic Failures | Use bcrypt/Argon2, HTTPS, don't store secrets in code |
| Injection | Parameterized queries, input validation |
| Insecure Design | Threat modeling, least privilege |
| Security Misconfiguration | Secure defaults, disable debug in prod |
| Vulnerable Components | Keep dependencies updated, audit regularly |
| Auth Failures | Rate limiting, strong passwords, MFA |
| Data Integrity | Verify signatures, secure CI/CD |
| Logging Failures | Log security events, protect logs |
| SSRF | Validate URLs, allowlist domains |

---

## Authentication

### Password Storage

Always use bcrypt or Argon2. Never SHA-256, MD5, or plaintext.

```
// Example: Argon2
const hashed = await hash(password)
const isValid = await verify(hashed, inputPassword)
```

### JWT Best Practices
- Use RS256, not HS256
- Short expiration (15 min for access tokens)
- Refresh token rotation (one-time use)
- Store secret in environment, not code
- Validate all claims (iss, aud, exp)

### Session Security
```
Cookie settings:
  httpOnly: true           # Prevents XSS access
  secure: true             # HTTPS only
  sameSite: 'strict'       # Prevents CSRF
  maxAge: 15 * 60 * 1000   # 15 minutes
```

### Auth Checklist
- [ ] Passwords hashed with bcrypt/Argon2
- [ ] JWT expiration set (short-lived)
- [ ] Secure cookie flags set
- [ ] Rate limiting on auth endpoints
- [ ] Account lockout after failed attempts
- [ ] Session invalidation on logout

---

## Authorization

### Verify Ownership

Every data access must verify the requesting user owns the resource:

```
async function getPost(postId, userId) {
  const post = await db.post.findUnique({ where: { id: postId } })

  if (post.authorId !== userId) {
    throw new ForbiddenError('Not authorized')
  }

  return post
}
```

### Role-Based Access
```
function authorize(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      throw new ForbiddenError()
    }
    next()
  }
}
```

### Authorization Checklist
- [ ] Permission checks on all endpoints
- [ ] No direct object references exposed
- [ ] Roles/permissions enforced server-side (not just client)
- [ ] Users can ONLY access their own data
- [ ] Every query filters by user_id or checks ownership

---

## Input Validation

### Validate All Input

Validate at the API boundary. Trust nothing from the client.

```
// Use a schema library (Zod, Joi, Yup, Pydantic, etc.)
const schema = z.object({
  email: z.string().email().max(255),
  password: z.string().min(8).max(128),
  name: z.string().max(100).optional(),
})

const input = schema.parse(request.body)
```

### SQL Injection Prevention
- Use parameterized queries or an ORM
- Never concatenate user input into SQL strings
- Use prepared statements for raw queries

### XSS Prevention
- Use framework's built-in escaping (React, Vue, etc. escape by default)
- Never render raw HTML from user input
- Sanitize HTML if you must render user content (use DOMPurify)
- Set Content-Security-Policy headers

### Input/Output Checklist
- [ ] All input validated server-side
- [ ] Parameterized queries used
- [ ] Output encoded/escaped
- [ ] File uploads validated (type, size)
- [ ] No raw HTML rendering of user content

---

## Sensitive Data

### Don't Log
```
BAD:  logger.info('Login', { email, password })
GOOD: logger.info('Login attempt', { email })
```

### Don't Expose in Responses
```
BAD:  return res.json(user)  // Includes password hash
GOOD: const { password, ...safe } = user; return res.json(safe)
```

### Don't Put in URLs
```
BAD:  /api/users?token=secret123
GOOD: Authorization: Bearer secret123
```

### Don't Put in Client Code
```
BAD:  const API_KEY = "sk_live_..."  // In frontend bundle
GOOD: Use server-side environment variables
```

---

## Security Headers

Essential headers for web applications:

```
Content-Security-Policy: default-src 'self'; script-src 'self'
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

Use helmet.js (Node), secure_headers (Ruby), or equivalent for your framework.

---

## Rate Limiting

```
General endpoints:   100 requests / 15 min
Auth endpoints:      5 requests / 15 min
API endpoints:       1000 requests / hour
File uploads:        10 requests / min
Password reset:      3 requests / hour
```

Return proper headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
```

---

## Dependency Security

```bash
# Check for vulnerabilities
npm audit           # Node.js
pip audit           # Python
go vuln check       # Go
cargo audit         # Rust

# Fix automatically
npm audit fix

# Deep analysis
npx snyk test
```

Run dependency audits in CI. Block PRs with critical vulnerabilities.

---

## CORS Configuration

```
// Only allow your own domains
const corsOptions = {
  origin: ['https://yourapp.com', 'https://app.yourapp.com'],
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  credentials: true,
}
```

Never use `origin: '*'` with `credentials: true`.

---

## Infrastructure Checklist

- [ ] HTTPS enforced (redirect HTTP to HTTPS)
- [ ] Security headers configured
- [ ] CORS properly configured
- [ ] Rate limiting enabled
- [ ] Dependencies up to date
- [ ] Debug mode off in production
- [ ] Error messages don't expose internals
- [ ] Logs don't contain sensitive data
- [ ] Secrets in environment variables, not code
- [ ] Database access restricted to app servers only

---

## Incident Response

1. **Detect** - Monitor logs for anomalies
2. **Contain** - Isolate affected systems
3. **Investigate** - Determine scope and cause
4. **Remediate** - Fix vulnerability
5. **Communicate** - Notify affected users if needed
6. **Learn** - Post-mortem and improvements

---

## Finding Report Format

```yaml
finding:
  title: ""
  severity: critical | high | medium | low
  category: auth | injection | access_control | data_exposure | config
  description: ""
  location:
    file: ""
    line: 0
  impact: ""
  recommendation: ""
  references: []
```
