# API Design Reference

REST and GraphQL API design principles and conventions.

---

## REST API Conventions

### URL Structure
```
GET    /api/users              # List users
GET    /api/users/:id          # Get user
POST   /api/users              # Create user
PUT    /api/users/:id          # Replace user
PATCH  /api/users/:id          # Update user
DELETE /api/users/:id          # Delete user

# Nested resources
GET    /api/users/:id/posts    # Get user's posts
POST   /api/users/:id/posts    # Create post for user

# Actions (when CRUD doesn't fit)
POST   /api/users/:id/verify   # Verify user
POST   /api/orders/:id/cancel  # Cancel order
```

### Naming Conventions
```
GOOD: /api/users           # Plural nouns
GOOD: /api/user-settings   # Kebab-case
GOOD: /api/orders/:id      # IDs in path

BAD:  /api/getUsers        # No verbs
BAD:  /api/user_settings   # No underscores
BAD:  /api/Users           # No capitals
```

---

## Response Format

### Success Response
```json
{
  "data": {
    "id": "user_abc123",
    "email": "user@example.com",
    "name": "Jane Doe",
    "createdAt": "2025-01-15T10:00:00Z"
  }
}
```

### List Response
```json
{
  "data": [
    { "id": "1", "name": "Item 1" },
    { "id": "2", "name": "Item 2" }
  ],
  "meta": {
    "page": 1,
    "perPage": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

### Error Response
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      { "field": "email", "message": "Must be a valid email" },
      { "field": "password", "message": "Must be at least 8 characters" }
    ]
  }
}
```

---

## Status Codes

### Success
| Code | Meaning | Use Case |
|------|---------|----------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |

### Client Error
| Code | Meaning | Use Case |
|------|---------|----------|
| 400 | Bad Request | Validation errors |
| 401 | Unauthorized | Missing/invalid auth |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate resource |
| 422 | Unprocessable | Business logic error |
| 429 | Too Many Requests | Rate limited |

### Server Error
| Code | Meaning | Use Case |
|------|---------|----------|
| 500 | Internal Error | Unexpected server error |
| 503 | Unavailable | Service temporarily down |

---

## Pagination

### Offset Pagination
```
GET /api/posts?page=2&perPage=20
```
Response meta:
```json
{ "page": 2, "perPage": 20, "total": 150, "totalPages": 8 }
```

### Cursor Pagination
```
GET /api/posts?cursor=abc123&limit=20
```
Response meta:
```json
{ "nextCursor": "xyz789", "hasMore": true }
```

Use cursor pagination for:
- Real-time data (items added/removed frequently)
- Large datasets (offset gets slow)
- Infinite scroll UIs

---

## Filtering and Sorting

```
# Filtering
GET /api/posts?status=published&authorId=123

# Multiple values
GET /api/posts?tags=javascript,react

# Date ranges
GET /api/posts?createdAfter=2025-01-01&createdBefore=2025-02-01

# Sorting
GET /api/posts?sort=createdAt&order=desc

# Multiple sorts
GET /api/posts?sort=status,-createdAt
```

---

## Versioning

### URL Versioning (Recommended)
```
/api/v1/users
/api/v2/users
```

### Header Versioning
```
Accept: application/vnd.myapp.v1+json
```

---

## Authentication

### Bearer Token
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

### API Keys
```
X-API-Key: sk_live_abc123
```

Never put tokens/keys in URLs.

---

## Rate Limiting

### Response Headers
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
```

### 429 Response
```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Too many requests. Try again in 60 seconds.",
    "retryAfter": 60
  }
}
```

---

## Error Codes

```
UNAUTHORIZED        Missing or invalid authentication
INVALID_TOKEN       Token is invalid or expired
FORBIDDEN           Insufficient permissions
VALIDATION_ERROR    Request validation failed
NOT_FOUND           Resource not found
CONFLICT            Resource already exists
RATE_LIMITED        Too many requests
INTERNAL_ERROR      Internal server error
SERVICE_UNAVAILABLE Service temporarily unavailable
```

---

## API Documentation (OpenAPI)

```yaml
openapi: 3.0.0
info:
  title: My API
  version: 1.0.0

paths:
  /api/users:
    get:
      summary: List users
      parameters:
        - name: page
          in: query
          schema:
            type: integer
      responses:
        200:
          description: List of users
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'
    post:
      summary: Create user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUser'
      responses:
        201:
          description: User created
```

---

## Best Practices

### Do
- Use consistent naming (plural nouns, kebab-case)
- Return appropriate status codes
- Include pagination metadata
- Version your API
- Document every endpoint
- Validate all input
- Rate limit endpoints
- Use HTTPS

### Don't
- Use verbs in URLs
- Return 200 for errors
- Expose internal IDs (use UUIDs or prefixed IDs)
- Return sensitive data in responses
- Allow unbounded queries (always paginate)
- Change response format without versioning
- Return stack traces to clients

---

## Security Checklist

- [ ] Authentication required where needed
- [ ] Input validation on all endpoints
- [ ] Rate limiting configured
- [ ] CORS properly configured
- [ ] Sensitive data not in URLs
- [ ] Proper error messages (no stack traces)
- [ ] HTTPS enforced
- [ ] Request size limits set
