# FastAPI Testing Guide

## Setup

```python
# conftest.py
import pytest
import asyncio
from httpx import AsyncClient
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

from app.main import app
from app.core.database import get_db, Base

TEST_DATABASE_URL = "sqlite+aiosqlite:///:memory:"

@pytest.fixture(scope="session")
def event_loop():
    loop = asyncio.get_event_loop_policy().new_event_loop()
    yield loop
    loop.close()

@pytest.fixture
async def db_session():
    engine = create_async_engine(TEST_DATABASE_URL)
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    
    session = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)
    async with session() as s:
        yield s

@pytest.fixture
async def client(db_session):
    async def override_get_db():
        yield db_session
    
    app.dependency_overrides[get_db] = override_get_db
    async with AsyncClient(app=app, base_url="http://test") as ac:
        yield ac
```

## Unit Tests

```python
# test_user_service.py
@pytest.mark.asyncio
async def test_create_user_success(db_session):
    service = UserService()
    user_in = UserCreate(
        email="test@example.com",
        password="testpass123",
        name="Test User"
    )
    
    user = await service.create_user(db_session, user_in)
    
    assert user.email == "test@example.com"
    assert user.name == "Test User"
    assert user.hashed_password != "testpass123"

@pytest.mark.asyncio
async def test_create_user_duplicate_email(db_session):
    service = UserService()
    user_in = UserCreate(email="test@example.com", password="test123")
    
    # Create first user
    await service.create_user(db_session, user_in)
    
    # Try to create second user with same email
    with pytest.raises(ValueError, match="Email already registered"):
        await service.create_user(db_session, user_in)
```

## Integration Tests

```python
# test_users_api.py
@pytest.mark.asyncio
async def test_create_user_api(client):
    response = await client.post(
        "/api/v1/users/",
        json={
            "email": "test@example.com",
            "password": "testpass123",
            "name": "Test User"
        }
    )
    
    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "test@example.com"
    assert "id" in data
    assert "password" not in data  # Never return password

@pytest.mark.asyncio
async def test_get_user_api(client):
    # First create a user
    create_response = await client.post(
        "/api/v1/users/",
        json={"email": "test@example.com", "password": "test123", "name": "Test"}
    )
    user_id = create_response.json()["id"]
    
    # Then get the user
    response = await client.get(f"/api/v1/users/{user_id}")
    assert response.status_code == 200
    assert response.json()["email"] == "test@example.com"

@pytest.mark.asyncio
async def test_get_nonexistent_user(client):
    response = await client.get("/api/v1/users/999")
    assert response.status_code == 404
    assert response.json()["detail"] == "User not found"
```

## Authentication Tests

```python
# test_auth.py
@pytest.mark.asyncio
async def test_login_success(client):
    # Create user first
    await client.post(
        "/api/v1/users/",
        json={"email": "test@example.com", "password": "test123", "name": "Test"}
    )
    
    # Login
    response = await client.post(
        "/api/v1/auth/login",
        data={"username": "test@example.com", "password": "test123"}
    )
    
    assert response.status_code == 200
    assert "access_token" in response.json()
    assert response.json()["token_type"] == "bearer"

@pytest.mark.asyncio
async def test_login_invalid_credentials(client):
    response = await client.post(
        "/api/v1/auth/login",
        data={"username": "test@example.com", "password": "wrong"}
    )
    
    assert response.status_code == 401
```

## Performance Tests

```python
@pytest.mark.asyncio
async def test_bulk_operations(client):
    # Create many users
    user_data = [
        {"email": f"test{i}@example.com", "password": "test123", "name": f"Test {i}"}
        for i in range(100)
    ]
    
    start_time = time.time()
    
    tasks = [client.post("/api/v1/users/", json=data) for data in user_data]
    responses = await asyncio.gather(*tasks)
    
    elapsed = time.time() - start_time
    assert all(r.status_code == 201 for r in responses)
    assert elapsed < 5.0  # Should complete within 5 seconds
```
