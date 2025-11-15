# FastAPI Best Practices

## Dependency Injection

```python
# Correct: Use FastAPI's DI system
@router.post("/users/")
async def create_user(
    user: UserCreate,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    return await user_service.create(db, user)

# Wrong: Manual dependency injection
@router.post("/users/")
async def create_user(user: UserCreate):
    db = SessionLocal()
    return await user_service.create(db, user)
```

## Async/Await Patterns

```python
# Correct: Always use await for async operations
async def get_user(db: AsyncSession, user_id: int):
    result = await db.execute(select(User).where(User.id == user_id))
    return result.scalars().first()

# Wrong: Blocking async operations
def get_user(db: AsyncSession, user_id: int):
    result = db.execute(select(User).where(User.id == user_id))
    return result.scalars().first()
```

## Error Handling

```python
# Use proper HTTP status codes
if not user:
    raise HTTPException(
        status_code=status.HTTP_404_NOT_FOUND,
        detail="User not found"
    )

# Validate input
try:
    user = await user_service.create(db, user_in)
except ValueError as e:
    raise HTTPException(
        status_code=status.HTTP_400_BAD_REQUEST,
        detail=str(e)
    )
```

## Database Transactions

```python
# Good: Proper session management
async def create_post_with_tags(
    post: PostCreate,
    tags: List[str],
    db: AsyncSession = Depends(get_db)
):
    try:
        db_post = await post_service.create(db, post)
        await tag_service.add_tags_to_post(db, db_post.id, tags)
        await db.commit()
        return db_post
    except Exception:
        await db.rollback()
        raise
```

## Response Models

```python
# Always define response models
@router.get("/users/{user_id}", response_model=UserResponse)
async def get_user(user_id: int, db: AsyncSession = Depends(get_db)):
    user = await user_service.get(db, user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

## Pagination

```python
class PaginationParams(BaseModel):
    skip: int = 0
    limit: int = Field(default=100, le=100)

@router.get("/users/", response_model=List[UserResponse])
async def list_users(
    pagination: PaginationParams = Depends(),
    db: AsyncSession = Depends(get_db)
):
    return await user_service.get_multi(
        db,
        skip=pagination.skip,
        limit=pagination.limit
    )
```
