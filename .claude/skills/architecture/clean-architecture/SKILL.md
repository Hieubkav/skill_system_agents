---
name: clean-architecture
description: Clean Architecture principles including layered architecture, dependency rule, and domain-driven design patterns. **ALWAYS use when working on backend code, ESPECIALLY when creating files, deciding file locations, or organizing layers (domain/application/infrastructure with HTTP).** Use proactively to ensure proper layer separation and dependency flow. Examples - "create entity", "add repository", "where should this file go", "clean architecture", "layered architecture", "use case", "repository pattern", "domain entities", "file location", "layer organization".
---

# Clean Architecture

You are an expert in Clean Architecture and Domain-Driven Design. You guide developers to structure applications with clear boundaries, testable business logic, and maintainable code following the Dependency Rule.

## Level 1: Overview

**The Dependency Rule**: Dependencies must point inward toward the domain.

```
┌─────────────────────────────────────┐
│   Infrastructure (External Concerns)│  ← DB, HTTP, Queue, Cache, APIs
└────────────┬────────────────────────┘
             │ depends on ↓
┌────────────▼────────────────────────┐
│   Application (Use Cases)           │  ← Business orchestration
└────────────┬────────────────────────┘
             │ depends on ↓
┌────────────▼─────────────────────────┐
│   Domain (Core Business)             │  ← Pure business logic
└──────────────────────────────────────┘
```

**Key Principles:**
- **Domain layer**: NO dependencies, pure TypeScript
- **Application layer**: Depends ONLY on Domain
- **Infrastructure layer**: Implements Domain ports
- **HTTP layer**: Part of Infrastructure, thin controllers

## Level 2: Quick Start

### 1. Domain Layer Files

**Entities** - Business objects with identity and lifecycle:
```
src/domain/entities/
├── user.entity.ts
├── order.entity.ts
└── product.entity.ts
```

**Value Objects** - Immutable, compared by value:
```
src/domain/value-objects/
├── email.value-object.ts
├── money.value-object.ts
└── uuidv7.value-object.ts (recommended for IDs)
```

**Ports** - Interface contracts (NO "I" prefix):
```
src/domain/ports/
├── repositories/
│   ├── user.repository.ts
│   └── order.repository.ts
├── password-hasher.ts
├── email-service.ts
└── event-bus.ts
```

### 2. Application Layer Files

**Use Cases** - Orchestrate domain objects:
```
src/application/use-cases/
├── create-user.use-case.ts
├── update-user.use-case.ts
└── deactivate-user.use-case.ts
```

**DTOs** - Data transfer objects, never expose entities:
```
src/application/dtos/
├── create-user.dto.ts
└── user-response.dto.ts
```

**Mappers** - Convert entities ↔ DTOs:
```
src/application/mappers/
└── user.mapper.ts
```

### 3. Infrastructure Layer Files

**Repositories** - Implement domain ports:
```
src/infrastructure/repositories/
└── user.repository.impl.ts (implements UserRepository port)
```

**Adapters** - External services (cache, logger, queue):
```
src/infrastructure/adapters/
├── cache/
├── logger/
└── queue/
```

**HTTP Layer** - Controllers, schemas, middleware:
```
src/infrastructure/http/
├── controllers/
├── schemas/ (Zod validation)
├── middleware/
└── plugins/
```

**Database** - Drizzle ORM, migrations, schemas:
```
src/infrastructure/database/
├── drizzle/schema/
├── migrations/
└── connection.ts
```

### 4. Create Entity with Value Objects

```typescript
// ✅ Domain layer (pure business logic)
export class User {
  constructor(
    private readonly _id: UUIDv7,
    private _email: Email,
    private _name: string
  ) {}

  deactivate(): void {
    if (!this._isActive) throw new Error("Already inactive")
    this._isActive = false
  }

  get id(): UUIDv7 { return this._id }
  get email(): Email { return this._email }
}
```

### 5. Create Repository Port

```typescript
// Domain layer port
export interface UserRepository {
  save(user: User): Promise<void>
  findById(id: UUIDv7): Promise<User | undefined>
}

// Infrastructure layer implementation
export class UserRepositoryImpl implements UserRepository {
  async save(user: User): Promise<void> {
    // Drizzle ORM implementation
  }
}
```

### 6. Create Use Case

```typescript
export class CreateUserUseCase {
  constructor(
    private readonly userRepository: UserRepository,
    private readonly passwordHasher: PasswordHasher
  ) {}

  async execute(dto: CreateUserDto): Promise<UserResponseDto> {
    // 1. Validate business rules
    const existing = await this.userRepository.findByEmail(dto.email)
    if (existing) throw new Error("Email in use")

    // 2. Create domain objects
    const userId = UUIDv7.generate()
    const email = Email.create(dto.email)
    const user = new User(userId, email, dto.name, await this.passwordHasher.hash(dto.password))

    // 3. Persist
    await this.userRepository.save(user)

    // 4. Return DTO
    return UserMapper.toDto(user)
  }
}
```

### 7. Create Controller (Auto-Registering)

```typescript
export class UserController {
  constructor(
    private readonly httpServer: HttpServer,
    private readonly createUserUseCase: CreateUserUseCase
  ) {
    this.registerRoutes()
  }

  private registerRoutes(): void {
    this.httpServer.route(HttpMethod.POST, "/users", async (c) => {
      const dto = c.req.valid("json")
      const user = await this.createUserUseCase.execute(dto)
      return c.json(user, 201)
    })
  }
}
```

## Level 3: References

- [workflow.md](./reference/workflow.md) - Detailed layer structure, implementation patterns, DI setup
- [examples.md](./reference/examples.md) - Complete working examples: User Management, Order Management, testing

### Key Concepts

**One Responsibility**: Each class/entity has one reason to change
**Testability**: Domain layer = zero dependencies, pure unit tests
**Ports & Adapters**: Domain defines interfaces, infrastructure implements them
**No Framework in Domain**: Domain layer is TypeScript-only, zero external dependencies

### Best Practices

- ✅ Keep domain layer pure (no imports from other layers)
- ✅ Use value objects for all IDs (UUIDv7 recommended)
- ✅ Make use cases orchestrate, not implement business logic
- ✅ Never expose entities in APIs (use DTOs)
- ✅ Use constructor injection, custom DI container
- ✅ Test domain layer first (no mocks needed)

### Anti-Patterns to Avoid

- ❌ Anemic domain model (no behavior in entities)
- ❌ Domain depending on infrastructure
- ❌ Fat controllers with business logic
- ❌ No clear layer separation
- ❌ Mixing DTOs with entities
- ❌ Services managing entities (behavior in entities)
