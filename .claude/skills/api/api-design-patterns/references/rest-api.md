# REST API Design Patterns

Comprehensive guide to REST API design principles, HTTP methods, URL patterns, and best practices.

## Table of Contents

- [Resource-Oriented Design](#resource-oriented-design)
- [HTTP Methods and Semantics](#http-methods-and-semantics)
- [HTTP Status Codes](#http-status-codes)
- [URL Design Patterns](#url-design-patterns)
- [API Versioning](#api-versioning)
- [Request/Response Patterns](#requestresponse-patterns)

---

## Resource-Oriented Design

**Core Principles:**
- Use nouns, not verbs: `/users`, `/orders` (not `/getUsers`)
- HTTP methods define actions: GET, POST, PUT, PATCH, DELETE
- Hierarchical URLs: `/users/{id}/orders`
- Plural for collections: `/users` not `/user`

**Good Examples**:
```http
GET    /api/v1/users          # List users
POST   /api/v1/users          # Create user
GET    /api/v1/users/{id}     # Get specific user
PUT    /api/v1/users/{id}     # Replace user
PATCH  /api/v1/users/{id}     # Update user
DELETE /api/v1/users/{id}     # Delete user
```

**Bad Examples**:
```http
GET    /api/v1/getUsers       # Don't use verbs
POST   /api/v1/createUser     # HTTP method already indicates action
POST   /api/v1/updateUser     # Use PUT/PATCH instead
POST   /api/v1/deleteUser     # Use DELETE instead
```

---

## HTTP Methods and Semantics

### GET - Retrieve Resources
- **Purpose**: Retrieve a resource or collection
- **Safe**: Yes (no side effects)
- **Idempotent**: Yes (same result on repeated calls)
- **Cacheable**: Yes
- **Request body**: Not typically used
- **Success codes**: 200 OK, 304 Not Modified

```http
GET /api/v1/users/123
```

### POST - Create Resources
- **Purpose**: Create a new resource
- **Safe**: No (creates data)
- **Idempotent**: No (multiple calls create multiple resources)
- **Cacheable**: Rarely
- **Request body**: Yes (resource data)
- **Success codes**: 201 Created, 202 Accepted

```http
POST /api/v1/users
Content-Type: application/json

{
  "email": "user@example.com",
  "name": "John Doe"
}
```

### PUT - Replace Resources
- **Purpose**: Replace entire resource
- **Safe**: No (modifies data)
- **Idempotent**: Yes (repeated calls have same effect)
- **Cacheable**: No
- **Request body**: Yes (complete resource)
- **Success codes**: 200 OK, 204 No Content

```http
PUT /api/v1/users/123
Content-Type: application/json

{
  "email": "newemail@example.com",
  "name": "John Smith",
  "age": 30
}
```

### PATCH - Partial Update
- **Purpose**: Partial update of resource
- **Safe**: No (modifies data)
- **Idempotent**: Not necessarily (depends on implementation)
- **Cacheable**: No
- **Request body**: Yes (partial resource)
- **Success codes**: 200 OK, 204 No Content

```http
PATCH /api/v1/users/123
Content-Type: application/json

{
  "name": "John Smith"
}
```

### DELETE - Remove Resources
- **Purpose**: Delete a resource
- **Safe**: No (removes data)
- **Idempotent**: Yes (repeated calls have same effect)
- **Cacheable**: No
- **Request body**: Not typically used
- **Success codes**: 200 OK, 204 No Content, 202 Accepted

```http
DELETE /api/v1/users/123
```

---

## HTTP Status Codes

### Success Codes (2xx)

- **200 OK** - Successful GET, PUT, PATCH, or DELETE
- **201 Created** - Successful POST with resource creation
  - Include `Location` header with new resource URL
- **202 Accepted** - Request accepted for async processing
- **204 No Content** - Successful DELETE or update with no response body

### Client Error Codes (4xx)

- **400 Bad Request** - Malformed request, validation error
- **401 Unauthorized** - Authentication required or failed
- **403 Forbidden** - Authenticated but not authorized
- **404 Not Found** - Resource doesn't exist
- **405 Method Not Allowed** - HTTP method not supported
- **409 Conflict** - Resource conflict (duplicate, version mismatch)
- **422 Unprocessable Entity** - Valid syntax but semantic errors
- **429 Too Many Requests** - Rate limit exceeded

### Server Error Codes (5xx)

- **500 Internal Server Error** - Unexpected server error
- **502 Bad Gateway** - Upstream service failure
- **503 Service Unavailable** - Temporary overload or maintenance
- **504 Gateway Timeout** - Upstream timeout

---

## URL Design Patterns

### Collection and Resource URLs

```http
# Collection (plural)
GET /api/v1/users

# Single resource
GET /api/v1/users/123

# Sub-collection
GET /api/v1/users/123/orders

# Sub-resource
GET /api/v1/users/123/profile
```

### Nested Resources

```http
# Good - Clear hierarchy
GET /api/v1/users/123/orders/456

# Avoid deep nesting (max 2 levels)
# Bad - Too deep
GET /api/v1/companies/1/departments/2/teams/3/members/4
```

### Query Parameters

```http
# Filtering
GET /api/v1/users?role=admin&status=active

# Pagination
GET /api/v1/users?page=2&limit=20

# Sorting
GET /api/v1/users?sort=-created_at

# Searching
GET /api/v1/users?q=john

# Field selection
GET /api/v1/users?fields=id,email,name
```

---

## API Versioning

### URL Versioning (Recommended)
```http
GET /api/v1/users
GET /api/v2/users
```

**Pros**: Clear, easy to route, visible in logs, SEO-friendly  
**Cons**: Can lead to code duplication across versions

### Header Versioning
```http
GET /api/users
Accept: application/vnd.myapi.v1+json
API-Version: 2
```

**Pros**: Clean URLs, flexible  
**Cons**: Harder to test, less visible in logs

### Query Parameter Versioning
```http
GET /api/users?version=2
```

**Pros**: Simple to implement  
**Cons**: Can be overridden, not RESTful

### Version Management Rules
1. Never break backwards compatibility within same version
2. Deprecate old versions with advance notice (6-12 months)
3. Document clear migration guides between versions
4. Support at least 2 major versions simultaneously
5. Monitor usage of deprecated endpoints

---

## Request/Response Patterns

### Standard Request Format

**JSON Request Body**:
```json
{
  "email": "user@example.com",
  "name": "John Doe",
  "preferences": {
    "newsletter": true,
    "notifications": false
  }
}
```

**Query Parameters** (for filtering, pagination, sorting):
```http
GET /api/v1/users?role=admin&status=active&page=2&limit=20&sort=-created_at
```

### Standard Response Format

**Success Response**:
```json
{
  "data": {
    "id": "user_123",
    "email": "user@example.com",
    "name": "John Doe",
    "createdAt": "2025-10-16T10:30:00Z"
  }
}
```

**Collection Response with Pagination**:
```json
{
  "data": [
    { "id": 1, "name": "User 1" },
    { "id": 2, "name": "User 2" }
  ],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 156,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": true
  },
  "links": {
    "self": "/api/v1/users?page=2",
    "next": "/api/v1/users?page=3",
    "prev": "/api/v1/users?page=1",
    "first": "/api/v1/users?page=1",
    "last": "/api/v1/users?page=8"
  }
}
```

---

## Idempotency

### Idempotent Operations
- GET, PUT, DELETE: Always idempotent
- POST: Not idempotent by default

### Idempotency Keys for POST

```http
POST /api/v1/payments
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000

{
  "amount": 100.00,
  "currency": "USD"
}
```

**Server behavior**:
- First request: Process and return 201
- Duplicate requests with same key: Return cached response
- Different request with same key: Return 409 Conflict

---

## Best Practices

1. **Use nouns for resources** - `/users`, not `/getUsers`
2. **Plural for collections** - `/users`, not `/user`
3. **Hierarchical structure** - `/users/{id}/orders`
4. **Correct HTTP methods** - Match semantics
5. **Appropriate status codes** - 2xx/4xx/5xx properly
6. **Version your API** - `/api/v1/`
7. **Paginate collections** - Don't return everything
8. **Filter with query params** - `?status=active`
9. **Sort with query params** - `?sort=-created_at`
10. **Return links** - HATEOAS when helpful

---

## Common Mistakes to Avoid

- ❌ Using verbs in URLs: `/createUser`, `/deleteUser`
- ❌ Using GET for state changes
- ❌ Returning 200 for errors
- ❌ Deep nesting (>2 levels)
- ❌ Exposing internal IDs without thinking
- ❌ Not versioning from day one
- ❌ Inconsistent naming (camelCase vs snake_case)
- ❌ Missing pagination on large collections
- ❌ Not using proper HTTP status codes
- ❌ Tight coupling to database structure
