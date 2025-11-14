# Clean Architecture Workflow

Quy trình chi tiết xây dựng ứng dụng theo Clean Architecture.

---

## Layer 1: Domain Layer (Core)

**Mục đích**: Pure business logic, không external dependencies

### Entities (Business Objects)
- Có identity, lifecycle
- Encapsulate business rules
- NO external dependencies

```typescript
// ✅ Entities have behavior (rich domain model)
export class User {
  private _isActive: boolean = true
  private readonly _createdAt: Date

  constructor(
    private readonly _id: UUIDv7,
    private _email: Email,
    private _name: string
  ) {
    this._createdAt = new Date()
  }

  deactivate(): void {
    if (!this._isActive) {
      throw new UserAlreadyInactiveError(this._id)
    }
    this._isActive = false
  }

  get id(): UUIDv7 { return this._id }
  get email(): Email { return this._email }
  get isActive(): boolean { return this._isActive }
}
```

### Value Objects (Immutable)
- No identity, compared by value
- Validate constraints

```typescript
export class Email {
  private readonly value: string

  private constructor(value: string) {
    this.value = value
  }

  static create(value: string): Email {
    if (!this.isValid(value)) {
      throw new InvalidEmailError(value)
    }
    return new Email(value)
  }

  private static isValid(value: string): boolean {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return emailRegex.test(value)
  }

  equals(other: Email): boolean {
    return this.value === other.value
  }

  toString(): string {
    return this.value
  }
}
```

### UUIDv7 Value Object (Recommended)
- Time-ordered IDs
- Sequential writes (better B-tree performance)
- Sortable by creation time

```typescript
export class UUIDv7 {
  private readonly value: string

  private constructor(value: string) {
    this.value = value
  }

  static generate(): UUIDv7 {
    return new UUIDv7(Bun.randomUUIDv7())
  }

  static from(value: string): UUIDv7 {
    if (!this.isValid(value)) {
      throw new Error(`Invalid UUID: ${value}`)
    }
    return new UUIDv7(value)
  }

  private static isValid(value: string): boolean {
    const uuidRegex = /^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i
    return uuidRegex.test(value)
  }

  equals(other: UUIDv7): boolean {
    return this.value === other.value
  }

  toString(): string { return this.value }
  toValue(): string { return this.value }
}

export type UserId = UUIDv7
```

### Ports (Interfaces in Domain)
- Define boundaries
- NO "I" prefix
- Implemented in Infrastructure layer

```typescript
// domain/ports/repositories/user.repository.ts
export interface UserRepository {
  save(user: User): Promise<void>
  findById(id: UUIDv7): Promise<User | undefined>
  findByEmail(email: string): Promise<User | undefined>
}

// domain/ports/password-hasher.ts
export interface PasswordHasher {
  hash(password: string): Promise<string>
  compare(password: string, hash: string): Promise<boolean>
}

// domain/ports/email-service.ts
export interface EmailService {
  sendWelcomeEmail(email: Email): Promise<void>
}
```

### Domain Services
- When logic involves multiple entities
- Stateless, pure functions

```typescript
export class PricingService {
  calculateOrderTotal(
    order: Order,
    discountRules: DiscountRule[]
  ): Money {
    let total = Money.zero()

    for (const item of order.items) {
      total = total.add(item.price.multiply(item.quantity))
    }

    for (const rule of discountRules) {
      if (rule.appliesTo(order)) {
        total = total.subtract(rule.calculateDiscount(total))
      }
    }

    return total
  }
}
```

### Domain Events
- Publish when important business events occur
- Used for eventual consistency

```typescript
export class UserCreatedEvent {
  constructor(
    public readonly userId: UUIDv7,
    public readonly email: Email,
    public readonly occurredAt: Date = new Date()
  ) {}
}
```

---

## Layer 2: Application Layer (Use Cases)

**Mục đích**: Orchestrate business logic, implement use cases

### Use Cases / Application Services
- Orchestrate domain objects
- Depend ONLY on Domain layer
- Return DTOs (never expose entities)

```typescript
// DTO (Data Transfer Object)
export interface CreateUserDto {
  email: string
  password: string
  name: string
}

export interface UserResponseDto {
  id: string
  email: string
  name: string
  isActive: boolean
  createdAt: string
}

// Use Case
export class CreateUserUseCase {
  constructor(
    private readonly userRepository: UserRepository,
    private readonly passwordHasher: PasswordHasher,
    private readonly emailService: EmailService,
    private readonly eventBus: EventBus
  ) {}

  async execute(dto: CreateUserDto): Promise<UserResponseDto> {
    // 1. Validate business rules
    const existingUser = await this.userRepository.findByEmail(dto.email)
    if (existingUser) {
      throw new EmailAlreadyInUseError(dto.email)
    }

    // 2. Orchestrate domain objects
    const userId = UUIDv7.generate()
    const email = Email.create(dto.email)
    const hashedPassword = await this.passwordHasher.hash(dto.password)

    const user = new User(userId, email, dto.name, hashedPassword)

    // 3. Persist via repository (port)
    await this.userRepository.save(user)

    // 4. Publish domain events
    await this.eventBus.publish(
      new UserCreatedEvent(user.id, user.email)
    )

    // 5. Coordinate side effects
    await this.emailService.sendWelcomeEmail(user.email)

    // 6. Return DTO
    return UserMapper.toDto(user)
  }
}
```

### Mappers
- Convert entities ↔ DTOs

```typescript
export class UserMapper {
  static toDto(user: User): UserResponseDto {
    return {
      id: user.id.toString(),
      email: user.email.toString(),
      name: user.name,
      isActive: user.isActive,
      createdAt: user.createdAt.toISOString()
    }
  }

  static toPersistence(user: User): any {
    return {
      id: user.id.toString(),
      email: user.email.toString(),
      name: user.name,
      isActive: user.isActive,
      createdAt: user.createdAt
    }
  }
}
```

---

## Layer 3: Infrastructure Layer (Adapters)

**Mục đích**: Implement technical details, external dependencies

### Repository Implementations
- Implement ports from Domain layer
- Use Drizzle ORM for database

```typescript
// infrastructure/repositories/user.repository.impl.ts
export class UserRepositoryImpl implements UserRepository {
  constructor(private readonly db: Database) {}

  async save(user: User): Promise<void> {
    const data = UserMapper.toPersistence(user)
    await this.db.insert(users).values(data)
  }

  async findById(id: UUIDv7): Promise<User | undefined> {
    const row = await this.db
      .select()
      .from(users)
      .where(eq(users.id, id.toString()))
      .limit(1)

    if (!row) return undefined

    return this.reconstructEntity(row)
  }

  async findByEmail(email: string): Promise<User | undefined> {
    const row = await this.db
      .select()
      .from(users)
      .where(eq(users.email, email))
      .limit(1)

    if (!row) return undefined

    return this.reconstructEntity(row)
  }

  private reconstructEntity(row: any): User {
    return new User(
      UUIDv7.from(row.id),
      Email.create(row.email),
      row.name,
      row.hashedPassword
    )
  }
}
```

### Adapter Implementations
- Cache, Logger, Queue, etc.

```typescript
// infrastructure/adapters/cache/redis-cache.adapter.ts
export class RedisCacheAdapter implements CacheService {
  constructor(private readonly redis: RedisClient) {}

  async get<T>(key: string): Promise<T | null> {
    const value = await this.redis.get(key)
    return value ? JSON.parse(value) : null
  }

  async set<T>(key: string, value: T, ttl?: number): Promise<void> {
    await this.redis.set(key, JSON.stringify(value), ttl)
  }
}

// infrastructure/adapters/logger/winston-logger.adapter.ts
export class WinstonLoggerAdapter implements Logger {
  private logger: winston.Logger

  log(message: string, context?: string): void {
    this.logger.info(message, { context })
  }

  error(message: string, error?: Error, context?: string): void {
    this.logger.error(message, { error, context })
  }
}
```

### Database Layer
- Drizzle schemas, migrations, connections

```typescript
// infrastructure/database/drizzle/schema/users.schema.ts
export const users = pgTable('users', {
  id: varchar('id', { length: 36 }).primaryKey(),
  email: varchar('email', { length: 255 }).unique().notNull(),
  name: varchar('name', { length: 255 }).notNull(),
  hashedPassword: varchar('hashed_password', { length: 255 }).notNull(),
  isActive: boolean('is_active').default(true),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow()
})
```

---

## Layer 4: HTTP Layer (In Infrastructure)

**Mục đích**: Handle HTTP requests, framework-specific code

### Controllers (Self-Registering)
- Thin layer, delegates to use cases
- Auto-register routes in constructor

```typescript
// infrastructure/http/controllers/user.controller.ts
export class UserController {
  constructor(
    private readonly httpServer: HttpServer,
    private readonly createUserUseCase: CreateUserUseCase,
    private readonly getUserUseCase: GetUserUseCase
  ) {
    this.registerRoutes()
  }

  private registerRoutes(): void {
    // POST /users
    this.httpServer.route(
      HttpMethod.POST,
      '/users',
      async (context) => {
        try {
          const dto = context.req.valid('json')
          const user = await this.createUserUseCase.execute(dto)
          return context.json(user, 201)
        } catch (error) {
          return context.json(
            { error: error.message },
            500
          )
        }
      }
    )

    // GET /users/:id
    this.httpServer.route(
      HttpMethod.GET,
      '/users/:id',
      async (context) => {
        try {
          const id = context.req.param('id')
          const user = await this.getUserUseCase.execute(id)
          return context.json(user)
        } catch (error) {
          return context.json(
            { error: error.message },
            500
          )
        }
      }
    )
  }
}
```

### Schemas (Zod Validation)
- Validate HTTP contracts

```typescript
// infrastructure/http/schemas/user.schema.ts
import { z } from 'zod'

export const CreateUserSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(1)
})

export type CreateUserInput = z.infer<typeof CreateUserSchema>
```

### HttpServer Port (Domain Layer)
- Framework-agnostic interface

```typescript
// domain/ports/http-server.ts
export enum HttpMethod {
  GET = 'GET',
  POST = 'POST',
  PUT = 'PUT',
  DELETE = 'DELETE',
  PATCH = 'PATCH'
}

export type HttpHandler = (context: any) => Promise<Response | any>

export interface HttpServer {
  route(method: HttpMethod, url: string, handler: HttpHandler): void
  listen(port: number): Promise<void>
}
```

### HttpServer Implementation (Hono)
```typescript
// infrastructure/http/server/hono-http-server.adapter.ts
export class HonoHttpServerAdapter implements HttpServer {
  private app: Hono

  constructor() {
    this.app = new Hono()
  }

  route(
    method: HttpMethod,
    url: string,
    handler: HttpHandler
  ): void {
    switch (method) {
      case HttpMethod.GET:
        this.app.get(url, handler)
        break
      case HttpMethod.POST:
        this.app.post(url, handler)
        break
      case HttpMethod.PUT:
        this.app.put(url, handler)
        break
      case HttpMethod.DELETE:
        this.app.delete(url, handler)
        break
    }
  }

  async listen(port: number): Promise<void> {
    serve(this.app, { port })
  }
}
```

---

## Dependency Injection

### DI Container (Custom, no external libs)

```typescript
// infrastructure/container/main.ts
export class DIContainer {
  private singletons = new Map<Symbol, any>()
  private factories = new Map<Symbol, () => any>()

  registerSingleton<T>(token: Symbol, factory: () => T): void {
    this.factories.set(token, factory)
  }

  registerTransient<T>(token: Symbol, factory: () => T): void {
    this.factories.set(token, factory)
  }

  resolve<T>(token: Symbol): T {
    if (this.singletons.has(token)) {
      return this.singletons.get(token)
    }

    const factory = this.factories.get(token)
    if (!factory) {
      throw new Error(`No provider found for token: ${token.toString()}`)
    }

    const instance = factory()
    this.singletons.set(token, instance)
    return instance
  }
}

// Setup
const container = new DIContainer()

// Register infrastructure
const httpServerToken = Symbol('HttpServer')
container.registerSingleton(httpServerToken, () => new HonoHttpServerAdapter())

const dbToken = Symbol('Database')
container.registerSingleton(dbToken, () => createDatabase())

// Register repositories
const userRepositoryToken = Symbol('UserRepository')
container.registerSingleton(userRepositoryToken, () => {
  const db = container.resolve(dbToken)
  return new UserRepositoryImpl(db)
})

// Register use cases
const createUserUseCaseToken = Symbol('CreateUserUseCase')
container.registerSingleton(createUserUseCaseToken, () => {
  const userRepository = container.resolve(userRepositoryToken)
  const passwordHasher = new BcryptPasswordHasher()
  const emailService = new SendgridEmailService()
  return new CreateUserUseCase(
    userRepository,
    passwordHasher,
    emailService
  )
})

// Register controllers
const userControllerToken = Symbol('UserController')
container.registerSingleton(userControllerToken, () => {
  const httpServer = container.resolve(httpServerToken)
  const createUserUseCase = container.resolve(createUserUseCaseToken)
  return new UserController(httpServer, createUserUseCase)
})
```

---

## Testing Strategy

### Domain Layer Tests (No Dependencies)
```typescript
import { describe, expect, it } from "bun:test"
import { User } from "@/domain/entities/user.entity"
import { Email } from "@/domain/value-objects/email.value-object"
import { UUIDv7 } from "@/domain/value-objects/uuidv7.value-object"

describe("User Entity", () => {
  it("should deactivate user", () => {
    const userId = UUIDv7.generate()
    const email = Email.create("user@example.com")
    const user = new User(userId, email, "John", "hashed")

    user.deactivate()

    expect(user.isActive).toBe(false)
  })

  it("should throw when deactivating inactive user", () => {
    const userId = UUIDv7.generate()
    const email = Email.create("user@example.com")
    const user = new User(userId, email, "John", "hashed")
    user.deactivate()

    expect(() => user.deactivate()).toThrow()
  })
})
```

### Application Layer Tests (Mocked Dependencies)
```typescript
import { describe, expect, it, mock } from "bun:test"
import { CreateUserUseCase } from "@/application/use-cases/create-user.use-case"

describe("CreateUserUseCase", () => {
  it("should create user", async () => {
    const mockRepository = {
      save: mock(async () => {}),
      findByEmail: mock(async () => undefined)
    }

    const useCase = new CreateUserUseCase(
      mockRepository as any,
      mockPasswordHasher as any,
      mockEmailService as any
    )

    const dto = {
      email: "test@example.com",
      password: "password123",
      name: "Test"
    }

    const result = await useCase.execute(dto)

    expect(mockRepository.save).toHaveBeenCalledTimes(1)
    expect(result.email).toBe("test@example.com")
  })
})
```

---

## Key Principles Summary

1. **Dependency Rule**: Dependencies point inward toward domain
2. **Domain-Driven**: Business logic in domain layer (entities, value objects)
3. **Use Cases**: Orchestrate domain objects, return DTOs
4. **Ports & Adapters**: Interfaces in domain, implementations in infrastructure
5. **Testability**: Domain layer pure, application layer mockable
6. **Framework-Agnostic**: Domain layer has no framework dependencies
