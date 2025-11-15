---
name: fastapi-templates
description: Create production-ready FastAPI projects with async patterns, dependency injection, and comprehensive error handling. Use when building new FastAPI applications or setting up backend API projects.
---

# FastAPI Project Templates

Production-ready FastAPI project structures with async patterns, dependency injection, middleware, and best practices for building high-performance APIs.

## When to Use This Skill

- Starting new FastAPI projects from scratch
- Implementing async REST APIs with Python
- Building high-performance web services and microservices
- Creating async applications with PostgreSQL, MongoDB
- Setting up API projects with proper structure and testing

## Core Concepts

### 1. Project Structure

**Recommended Layout:**
```
app/
├── api/                    # API routes
│   ├── v1/
│   │   ├── endpoints/
│   │   │   ├── users.py
│   │   │   ├── auth.py
│   │   │   └── items.py
│   │   └── router.py
│   └── dependencies.py     # Shared dependencies
├── core/                   # Core configuration
│   ├── config.py
│   ├── security.py
│   └── database.py
├── models/                 # Database models
│   ├── user.py
│   └── item.py
├── schemas/                # Pydantic schemas
│   ├── user.py
│   └── item.py
├── services/               # Business logic
│   ├── user_service.py
│   └── auth_service.py
├── repositories/           # Data access
│   ├── user_repository.py
│   └── item_repository.py
└── main.py                 # Application entry
```

### 2. Dependency Injection

FastAPI's built-in DI system using `Depends`:
- Database session management
- Authentication/authorization
- Shared business logic
- Configuration injection

### 3. Async Patterns

Proper async/await usage:
- Async route handlers
- Async database operations
- Async background tasks
- Async middleware

## Quick Start

### Basic Setup

```python
# main.py
from fastapi import FastAPI, Depends
from fastapi.middleware.cors import CORSMiddleware
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    await database.connect()
    yield
    await database.disconnect()

app = FastAPI(title="API Template", version="1.0.0", lifespan=lifespan)

# CORS
app.add_middleware(CORSMiddleware, allow_origins=["*"])

# Include routers
from app.api.v1.router import api_router
app.include_router(api_router, prefix="/api/v1")
```

### Repository Pattern

```python
# Base repository - see references/implementation-patterns.md
# Service layer - see references/implementation-patterns.md
# API endpoints - see references/implementation-patterns.md
```

### Authentication

```python
# JWT setup - see references/implementation-patterns.md
# Dependencies - see references/implementation-patterns.md
```

## Testing

```python
# pytest setup - see references/testing-guide.md
# test examples - see references/testing-guide.md
```

## Resources

- [implementation-patterns.md](./references/implementation-patterns.md) - Complete code patterns
- [project-structure.md](./references/project-structure.md) - Detailed structure guide
- [testing-guide.md](./references/testing-guide.md) - Testing strategies
- [best-practices.md](./references/best-practices.md) - Best practices checklist

## Best Practices

1. **Async All The Way**: Use async for database, external APIs
2. **Dependency Injection**: Leverage FastAPI's DI system
3. **Repository Pattern**: Separate data access from business logic
4. **Service Layer**: Keep business logic out of routes
5. **Pydantic Schemas**: Strong typing for request/response
6. **Error Handling**: Consistent error responses
7. **Testing**: Test all layers independently

## Common Pitfalls to Avoid

- **Blocking Code in Async**: Using synchronous database drivers
- **No Service Layer**: Business logic in route handlers
- **Missing Type Hints**: Loses FastAPI's benefits
- **Ignoring Sessions**: Not properly managing database sessions
- **No Testing**: Skipping integration tests
- **Tight Coupling**: Direct database access in routes

---
