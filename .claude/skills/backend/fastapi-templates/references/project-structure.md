# FastAPI Project Structure

## Recommended Layout

```
my_project/
├── app/                          # Application code
│   ├── __init__.py
│   ├── main.py                   # FastAPI app instance and configuration
│   │
│   ├── api/                      # API routes
│   │   ├── __init__.py
│   │   ├── deps.py               # Common dependencies
│   │   └── v1/                   # API version 1
│   │       ├── __init__.py
│   │       ├── router.py         # API router aggregation
│   │       └── endpoints/        # Route handlers
│   │           ├── __init__.py
│   │           ├── auth.py
│   │           ├── users.py
│   │           └── items.py
│   │
│   ├── core/                     # Core application config
│   │   ├── __init__.py
│   │   ├── config.py             # Settings and configuration
│   │   ├── security.py           # Security utils (JWT, password hashing)
│   │   └── database.py           # Database connection and session
│   │
│   ├── models/                   # SQLAlchemy models
│   │   ├── __init__.py
│   │   ├── base.py               # Base model class
│   │   ├── user.py
│   │   └── item.py
│   │
│   ├── schemas/                  # Pydantic schemas
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── item.py
│   │
│   ├── services/                 # Business logic
│   │   ├── __init__.py
│   │   ├── user_service.py
│   │   └── auth_service.py
│   │
│   └── repositories/             # Data access layer
│       ├── __init__.py
│       ├── base_repository.py
│       ├── user_repository.py
│       └── item_repository.py
│
├── alembic/                      # Database migrations
│   ├── versions/
│   ├── env.py
│   └── alembic.ini
│
├── tests/                        # Test suite
│   ├── __init__.py
│   ├── conftest.py               # Pytest configuration
│   ├── test_auth.py
│   ├── test_users.py
│   └── test_items.py
│
├── scripts/                      # Utility scripts
│   ├── init_db.py
│   └── create_admin.py
│
├── requirements.txt              # Dependencies
├── .env                          # Environment variables
├── .gitignore
├── README.md
└── pyproject.toml                # Project metadata and tool config
```

## Key Components Explained

### 1. API Layer (`app/api/`)
- **Route handlers**: Define endpoints, request/response validation
- **Dependencies**: Reusable components like authentication, DB session
- **Versioning**: Separate by version (v1, v2) for backward compatibility

### 2. Core Layer (`app/core/`)
- **Configuration**: Settings from environment variables
- **Security**: JWT handling, password utilities
- **Database**: Connection engine, session management

### 3. Models Layer (`app/models/`)
- **SQLAlchemy models**: Database table definitions
- **Relationships**: Foreign keys and table associations
- **Base classes**: Common fields and methods

### 4. Schemas Layer (`app/schemas/`)
- **Request schemas**: Input validation with Pydantic
- **Response schemas**: Output serialization
- **Data transfer**: Clean API contracts

### 5. Service Layer (`app/services/`)
- **Business logic**: Orchestrate operations between models
- **Complex operations**: Multi-step processes
- **Domain rules**: Validation specific to business needs

### 6. Repository Layer (`app/repositories/`)
- **Data access**: CRUD operations abstraction
- **Query logic**: Complex database queries
- **Base patterns**: Generic CRUD for all models

## File Organization Principles

### Separation of Concerns
- Each layer has a single responsibility
- Clear boundaries between components
- Dependencies flow inward (API → Service → Repository)

### Feature-Based Grouping
- Related functionality grouped together
- Easy to locate and modify features
- Reduced coupling between modules

### Testing Structure
- Mirror application structure
- Easy to find corresponding test files
- Clear test isolation

## Import Patterns

```python
# Good: Absolute imports from project root
from app.core.config import get_settings
from app.models.user import User
from app.schemas.user import UserCreate
from app.services.user_service import user_service

# Avoid: Relative imports
from ..core.config import get_settings
from ...models.user import User
```

## Configuration Management

```python
# app/core/config.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # Database
    DATABASE_URL: str
    
    # Security
    SECRET_KEY: str
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 30
    
    # API
    API_V1_STR: str = "/api/v1"
    
    class Config:
        env_file = ".env"

@lru_cache()
def get_settings() -> Settings:
    return Settings()
```

## Error Handling Structure

```python
# app/core/exceptions.py
class BusinessException(Exception):
    """Base exception for business logic errors."""
    pass

class NotFoundError(BusinessException):
    """Resource not found."""
    pass

class ValidationError(BusinessException):
    """Invalid input data."""
    pass

# app/api/endpoints/users.py
from app.core.exceptions import NotFoundError

@router.get("/{user_id}")
async def get_user(user_id: int):
    try:
        return await user_service.get(user_id)
    except NotFoundError:
        raise HTTPException(status_code=404, detail="User not found")
```
