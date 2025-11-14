---
name: api-design-patterns
description: Comprehensive REST and GraphQL API design patterns, best practices, OpenAPI specifications, versioning, authentication, error handling, pagination, rate limiting, and security. Use when designing APIs, creating endpoints, reviewing specifications, implementing authentication, or building scalable backend services.
---

# API Design Patterns & Best Practices

Master REST and GraphQL API design to build intuitive, scalable, secure, and maintainable APIs that delight developers.

## Level 1: Overview (Always Read First)

This skill provides comprehensive guidance on REST and GraphQL API design, covering everything from basic principles to advanced patterns. Use it when designing new APIs, refactoring existing ones, establishing design standards, or troubleshooting API issues.

## Prerequisites

- Understanding of HTTP protocol
- Basic knowledge of REST or GraphQL concepts
- Familiarity with JSON format
- Access to API development tools (Postman, Swagger, etc.)

## What This Skill Does

1. Guides REST and GraphQL API design decisions
2. Provides authentication and security patterns
3. Explains error handling and validation strategies
4. Covers pagination, filtering, and performance optimization
5. Demonstrates versioning and documentation approaches

---

## Level 2: Quick Start (For Fast Onboarding)

### Core REST Principles

**Resource-Oriented URLs:**
```http
GET    /api/v1/users          # List users
POST   /api/v1/users          # Create user
GET    /api/v1/users/{id}     # Get specific user
PUT    /api/v1/users/{id}     # Replace user
PATCH  /api/v1/users/{id}     # Update user
DELETE /api/v1/users/{id}     # Delete user
```

**Key Rules:**
- Use nouns, not verbs: `/users` not `/getUsers`
- HTTP methods define actions: GET, POST, PUT, PATCH, DELETE
- Hierarchical structure: `/users/{id}/orders`
- Plural for collections: `/users` not `/user`

### Common Status Codes

- `200 OK` - Successful request
- `201 Created` - Resource created
- `400 Bad Request` - Validation error
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Not authorized
- `404 Not Found` - Resource doesn't exist
- `500 Internal Server Error` - Server error

### Standard Response Format

```json
{
  "data": {
    "id": "user_123",
    "email": "user@example.com"
  }
}
```

**Error Response:**
```json
{
  "error": {
    "code": "INVALID_EMAIL",
    "message": "Email address is invalid",
    "field": "email"
  }
}
```

---

## Level 3: Reference Guides

- [rest-api.md](./references/rest-api.md) - Use when: Designing REST APIs, resource modeling, HTTP methods
- [graphql.md](./references/graphql.md) - Use when: Designing GraphQL schemas, queries, mutations
- [authentication.md](./references/authentication.md) - Use when: Implementing JWT, OAuth, API keys
- [error-handling.md](./references/error-handling.md) - Use when: Designing error responses, validation
- [pagination-filtering.md](./references/pagination-filtering.md) - Use when: Implementing pagination, search, filtering
- [security.md](./references/security.md) - Use when: Security best practices, input validation, CORS
- [performance.md](./references/performance.md) - Use when: Caching, optimization, field selection
- [async-webhooks.md](./references/async-webhooks.md) - Use when: Long-running tasks, webhooks
- [openapi.md](./references/openapi.md) - Use when: Writing API documentation

---

## When to Use This Skill

- Designing new REST or GraphQL APIs
- Refactoring existing APIs for better usability
- Establishing API design standards
- Reviewing API specifications before implementation
- Implementing authentication and authorization
- Creating API documentation (OpenAPI/Swagger)
- Troubleshooting API issues
- Planning API versioning strategy
- Implementing rate limiting and security
- Optimizing API performance

---

## Quick Decision Guide

### Choose REST when:
- Simple CRUD operations
- Need HTTP caching
- Working with files/binary data
- Team familiar with REST
- Mobile apps with strict bandwidth limits

### Choose GraphQL when:
- Complex data requirements
- Multiple client types (web, mobile, desktop)
- Need flexible queries
- Rapid frontend development
- Real-time updates (subscriptions)

---

## Common Pitfalls to Avoid

### REST
- ❌ Action-oriented endpoints (`/createUser`)
- ❌ Inconsistent status codes
- ❌ Missing pagination
- ❌ Using GET for state changes
- ❌ Returning sensitive data (passwords, tokens)
- ❌ No versioning strategy

### GraphQL
- ❌ N+1 query problems (missing DataLoaders)
- ❌ No depth/complexity limiting
- ❌ Generic error handling
- ❌ Over-fetching by providing too many fields
- ❌ No caching strategy

### Both
- ❌ Breaking changes without versioning
- ❌ Missing rate limits
- ❌ No API documentation
- ❌ Ignoring CORS configuration
- ❌ No monitoring/logging
- ❌ Inconsistent naming conventions

---

## Best Practices Summary

1. **Consistent naming** - Plural nouns, lowercase, hyphens
2. **Correct status codes** - 2xx success, 4xx client error, 5xx server error
3. **Version from day one** - Plan for breaking changes
4. **Always paginate** - Large collections need pagination
5. **Rate limiting** - Protect API with limits
6. **Security first** - HTTPS, input validation, parameterized queries
7. **Document everything** - Use OpenAPI/Swagger
8. **Handle errors gracefully** - Structured error responses
9. **Monitor and log** - Track usage, errors, performance
10. **Test thoroughly** - Unit, integration, and load testing

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Unclear endpoint design | Review REST principles in rest-api.md |
| Authentication issues | Check authentication.md for JWT/OAuth patterns |
| Performance problems | See performance.md for caching and optimization |
| Complex queries | Consider GraphQL approach in graphql.md |
| Security concerns | Review security.md checklist |
| Pagination not working | Check pagination-filtering.md for strategies |

---

**Remember**: A well-designed API is intuitive, secure, performant, and well-documented. Follow these patterns to create APIs that developers love to use.
