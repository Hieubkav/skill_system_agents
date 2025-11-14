# Clean Architecture Examples

Real-world examples của Clean Architecture patterns.

---

## Example 1: User Management System

### File Structure

```
src/
├── domain/
│   ├── entities/
│   │   └── user.entity.ts
│   ├── value-objects/
│   │   ├── email.value-object.ts
│   │   └── uuidv7.value-object.ts
│   ├── ports/
│   │   ├── repositories/
│   │   │   └── user.repository.ts
│   │   ├── password-hasher.ts
│   │   ├── email-service.ts
│   │   └── event-bus.ts
│   └── exceptions/
│       ├── user-not-found.exception.ts
│       └── email-already-in-use.exception.ts
├── application/
│   ├── use-cases/
│   │   ├── create-user.use-case.ts
│   │   ├── get-user.use-case.ts
│   │   ├── update-user-profile.use-case.ts
│   │   └── deactivate-user.use-case.ts
│   ├── dtos/
│   │   ├── create-user.dto.ts
│   │   ├── user-response.dto.ts
│   │   └── update-user.dto.ts
│   └── mappers/
│       └── user.mapper.ts
└── infrastructure/
    ├── repositories/
    │   └── user.repository.impl.ts
    ├── adapters/
    │   ├── password-hasher.impl.ts
    │   ├── email-service.impl.ts
    │   └── event-bus.impl.ts
    ├── http/
    │   ├── controllers/
    │   │   └── user.controller.ts
    │   ├── schemas/
    │   │   └── user.schema.ts
    │   └── middleware/
    │       └── error-handler.middleware.ts
    ├── database/
    │   ├── drizzle/
    │   │   └── schema/
    │   │       └── users.schema.ts
    │   └── connection.ts
    └── container/
        └── main.ts
```

### Domain Layer - Entity

```typescript
// domain/entities/user.entity.ts
import { UUIDv7 } from "@/domain/value-objects/uuidv7.value-object"
import type { Email } from "@/domain/value-objects/email.value-object"

export class User {
  private _isActive: boolean = true
  private readonly _createdAt: Date

  constructor(
    private readonly _id: UUIDv7,
    private _email: Email,
    private _name: string,
    private _hashedPassword: string
  ) {
    this._createdAt = new Date()
  }

  // Business logic in entity
  deactivate(): void {
    if (!this._isActive) {
      throw new Error(`User ${this._id.toString()} is already inactive`)
    }
    this._isActive = false
  }

  updateProfile(name: string, email: Email): void {
    this._name = name
    this._email = email
  }

  isDeactivated(): boolean {
    return !this._isActive
  }

  // Getters (read-only)
  get id(): UUIDv7 { return this._id }
  get email(): Email { return this._email }
  get name(): string { return this._name }
  get hashedPassword(): string { return this._hashedPassword }
  get isActive(): boolean { return this._isActive }
  get createdAt(): Date { return this._createdAt }
}
```

### Domain Layer - Value Objects

```typescript
// domain/value-objects/email.value-object.ts
export class Email {
  private readonly value: string

  private constructor(value: string) {
    this.value = value
  }

  static create(value: string): Email {
    if (!this.isValid(value)) {
      throw new Error(`Invalid email: ${value}`)
    }
    return new Email(value)
  }

  private static isValid(email: string): boolean {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return emailRegex.test(email)
  }

  equals(other: Email): boolean {
    return this.value === other.value
  }

  toString(): string {
    return this.value
  }
}

// domain/value-objects/uuidv7.value-object.ts
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
      throw new Error(`Invalid UUID format: ${value}`)
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

  toString(): string {
    return this.value
  }

  toValue(): string {
    return this.value
  }
}

export type UserId = UUIDv7
```

### Domain Layer - Ports

```typescript
// domain/ports/repositories/user.repository.ts
import type { User } from "@/domain/entities/user.entity"
import type { UUIDv7 } from "@/domain/value-objects/uuidv7.value-object"

export interface UserRepository {
  save(user: User): Promise<void>
  findById(id: UUIDv7): Promise<User | undefined>
  findByEmail(email: string): Promise<User | undefined>
  delete(id: UUIDv7): Promise<void>
}

// domain/ports/password-hasher.ts
export interface PasswordHasher {
  hash(password: string): Promise<string>
  compare(password: string, hash: string): Promise<boolean>
}

// domain/ports/email-service.ts
import type { Email } from "@/domain/value-objects/email.value-object"

export interface EmailService {
  sendWelcomeEmail(email: Email, name: string): Promise<void>
  sendPasswordResetEmail(email: Email, resetLink: string): Promise<void>
}

// domain/ports/event-bus.ts
export interface DomainEvent {
  readonly occurredAt: Date
}

export interface EventBus {
  publish(event: DomainEvent): Promise<void>
}
```

### Application Layer - DTOs

```typescript
// application/dtos/create-user.dto.ts
export interface CreateUserDto {
  email: string
  password: string
  name: string
}

// application/dtos/user-response.dto.ts
export interface UserResponseDto {
  id: string
  email: string
  name: string
  isActive: boolean
  createdAt: string
}

// application/dtos/update-user.dto.ts
export interface UpdateUserDto {
  name?: string
  email?: string
}
```

### Application Layer - Use Cases

```typescript
// application/use-cases/create-user.use-case.ts
import type { CreateUserDto, UserResponseDto } from "@/application/dtos/create-user.dto"
import type { UserRepository } from "@/domain/ports/repositories/user.repository"
import type { PasswordHasher } from "@/domain/ports/password-hasher"
import type { EmailService } from "@/domain/ports/email-service"
import type { EventBus } from "@/domain/ports/event-bus"
import { User } from "@/domain/entities/user.entity"
import { UUIDv7 } from "@/domain/value-objects/uuidv7.value-object"
import { Email } from "@/domain/value-objects/email.value-object"
import { UserMapper } from "@/application/mappers/user.mapper"
import { UserCreatedEvent } from "@/domain/events/user-created.event"

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
      throw new Error(`Email ${dto.email} is already in use`)
    }

    // 2. Create domain objects
    const userId = UUIDv7.generate()
    const email = Email.create(dto.email)
    const hashedPassword = await this.passwordHasher.hash(dto.password)

    const user = new User(userId, email, dto.name, hashedPassword)

    // 3. Persist
    await this.userRepository.save(user)

    // 4. Publish domain event
    await this.eventBus.publish(new UserCreatedEvent(user.id, user.email))

    // 5. Coordinate side effects
    await this.emailService.sendWelcomeEmail(user.email, user.name)

    // 6. Return DTO
    return UserMapper.toDto(user)
  }
}

// application/use-cases/get-user.use-case.ts
export class GetUserUseCase {
  constructor(private readonly userRepository: UserRepository) {}

  async execute(id: string): Promise<UserResponseDto> {
    const userId = UUIDv7.from(id)
    const user = await this.userRepository.findById(userId)

    if (!user) {
      throw new Error(`User with id ${id} not found`)
    }

    return UserMapper.toDto(user)
  }
}

// application/use-cases/update-user-profile.use-case.ts
export class UpdateUserProfileUseCase {
  constructor(
    private readonly userRepository: UserRepository
  ) {}

  async execute(
    id: string,
    dto: UpdateUserDto
  ): Promise<UserResponseDto> {
    const userId = UUIDv7.from(id)
    const user = await this.userRepository.findById(userId)

    if (!user) {
      throw new Error(`User with id ${id} not found`)
    }

    // Update domain object
    if (dto.name || dto.email) {
      const email = dto.email ? Email.create(dto.email) : user.email
      user.updateProfile(dto.name || user.name, email)
    }

    // Persist
    await this.userRepository.save(user)

    return UserMapper.toDto(user)
  }
}

// application/use-cases/deactivate-user.use-case.ts
export class DeactivateUserUseCase {
  constructor(
    private readonly userRepository: UserRepository,
    private readonly eventBus: EventBus
  ) {}

  async execute(id: string): Promise<void> {
    const userId = UUIDv7.from(id)
    const user = await this.userRepository.findById(userId)

    if (!user) {
      throw new Error(`User with id ${id} not found`)
    }

    // Business logic in entity
    user.deactivate()

    // Persist
    await this.userRepository.save(user)

    // Publish event
    await this.eventBus.publish(new UserDeactivatedEvent(user.id))
  }
}
```

### Application Layer - Mapper

```typescript
// application/mappers/user.mapper.ts
import { User } from "@/domain/entities/user.entity"
import type { UserResponseDto } from "@/application/dtos/user-response.dto"

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
      hashedPassword: user.hashedPassword,
      isActive: user.isActive,
      createdAt: user.createdAt
    }
  }
}
```

### Infrastructure Layer - Repository

```typescript
// infrastructure/repositories/user.repository.impl.ts
import type { Database } from "drizzle-orm"
import { eq } from "drizzle-orm"
import { users } from "@/infrastructure/database/drizzle/schema/users.schema"
import type { UserRepository } from "@/domain/ports/repositories/user.repository"
import type { User } from "@/domain/entities/user.entity"
import type { UUIDv7 } from "@/domain/value-objects/uuidv7.value-object"
import { UUIDv7 as UUIDv7VO } from "@/domain/value-objects/uuidv7.value-object"
import { Email } from "@/domain/value-objects/email.value-object"
import { User as UserEntity } from "@/domain/entities/user.entity"

export class UserRepositoryImpl implements UserRepository {
  constructor(private readonly db: Database) {}

  async save(user: User): Promise<void> {
    const data = {
      id: user.id.toString(),
      email: user.email.toString(),
      name: user.name,
      hashedPassword: user.hashedPassword,
      isActive: user.isActive,
      createdAt: user.createdAt
    }

    const existing = await this.db
      .select()
      .from(users)
      .where(eq(users.id, data.id))
      .limit(1)

    if (existing.length > 0) {
      await this.db
        .update(users)
        .set(data)
        .where(eq(users.id, data.id))
    } else {
      await this.db.insert(users).values(data)
    }
  }

  async findById(id: UUIDv7): Promise<User | undefined> {
    const row = await this.db
      .select()
      .from(users)
      .where(eq(users.id, id.toString()))
      .limit(1)

    if (row.length === 0) return undefined

    return this.reconstructEntity(row[0])
  }

  async findByEmail(email: string): Promise<User | undefined> {
    const row = await this.db
      .select()
      .from(users)
      .where(eq(users.email, email))
      .limit(1)

    if (row.length === 0) return undefined

    return this.reconstructEntity(row[0])
  }

  async delete(id: UUIDv7): Promise<void> {
    await this.db
      .delete(users)
      .where(eq(users.id, id.toString()))
  }

  private reconstructEntity(row: any): User {
    return new UserEntity(
      UUIDv7VO.from(row.id),
      Email.create(row.email),
      row.name,
      row.hashedPassword
    )
  }
}
```

### Infrastructure Layer - Controllers

```typescript
// infrastructure/http/controllers/user.controller.ts
import type { HttpServer } from "@/domain/ports/http-server"
import { HttpMethod } from "@/domain/ports/http-server"
import type { CreateUserUseCase } from "@/application/use-cases/create-user.use-case"
import type { GetUserUseCase } from "@/application/use-cases/get-user.use-case"
import type { UpdateUserProfileUseCase } from "@/application/use-cases/update-user-profile.use-case"
import type { DeactivateUserUseCase } from "@/application/use-cases/deactivate-user.use-case"

export class UserController {
  constructor(
    private readonly httpServer: HttpServer,
    private readonly createUserUseCase: CreateUserUseCase,
    private readonly getUserUseCase: GetUserUseCase,
    private readonly updateUserProfileUseCase: UpdateUserProfileUseCase,
    private readonly deactivateUserUseCase: DeactivateUserUseCase
  ) {
    this.registerRoutes()
  }

  private registerRoutes(): void {
    // POST /users
    this.httpServer.route(HttpMethod.POST, "/users", async (context) => {
      try {
        const dto = context.req.valid("json")
        const user = await this.createUserUseCase.execute(dto)
        return context.json(user, 201)
      } catch (error) {
        return context.json({ error: error.message }, 400)
      }
    })

    // GET /users/:id
    this.httpServer.route(HttpMethod.GET, "/users/:id", async (context) => {
      try {
        const id = context.req.param("id")
        const user = await this.getUserUseCase.execute(id)
        return context.json(user)
      } catch (error) {
        return context.json({ error: error.message }, 404)
      }
    })

    // PUT /users/:id
    this.httpServer.route(HttpMethod.PUT, "/users/:id", async (context) => {
      try {
        const id = context.req.param("id")
        const dto = context.req.valid("json")
        const user = await this.updateUserProfileUseCase.execute(id, dto)
        return context.json(user)
      } catch (error) {
        return context.json({ error: error.message }, 400)
      }
    })

    // DELETE /users/:id
    this.httpServer.route(HttpMethod.DELETE, "/users/:id", async (context) => {
      try {
        const id = context.req.param("id")
        await this.deactivateUserUseCase.execute(id)
        return context.json({ success: true })
      } catch (error) {
        return context.json({ error: error.message }, 400)
      }
    })
  }
}
```

### Dependency Injection Setup

```typescript
// infrastructure/container/main.ts
import { DIContainer } from "@/infrastructure/container/di-container"
import { HonoHttpServerAdapter } from "@/infrastructure/http/server/hono-http-server.adapter"
import { UserRepositoryImpl } from "@/infrastructure/repositories/user.repository.impl"
import { BcryptPasswordHasher } from "@/infrastructure/adapters/password-hasher.impl"
import { SendgridEmailService } from "@/infrastructure/adapters/email-service.impl"
import { EventBusImpl } from "@/infrastructure/adapters/event-bus.impl"
import { CreateUserUseCase } from "@/application/use-cases/create-user.use-case"
import { GetUserUseCase } from "@/application/use-cases/get-user.use-case"
import { UpdateUserProfileUseCase } from "@/application/use-cases/update-user-profile.use-case"
import { DeactivateUserUseCase } from "@/application/use-cases/deactivate-user.use-case"
import { UserController } from "@/infrastructure/http/controllers/user.controller"

export const createContainer = (): DIContainer => {
  const container = new DIContainer()

  // Infrastructure singletons
  const httpServerToken = Symbol("HttpServer")
  container.registerSingleton(httpServerToken, () => new HonoHttpServerAdapter())

  const dbToken = Symbol("Database")
  container.registerSingleton(dbToken, () => createDatabase())

  // Repositories
  const userRepositoryToken = Symbol("UserRepository")
  container.registerSingleton(userRepositoryToken, () => {
    const db = container.resolve(dbToken)
    return new UserRepositoryImpl(db)
  })

  // Adapters
  const passwordHasherToken = Symbol("PasswordHasher")
  container.registerSingleton(passwordHasherToken, () => new BcryptPasswordHasher())

  const emailServiceToken = Symbol("EmailService")
  container.registerSingleton(emailServiceToken, () => new SendgridEmailService())

  const eventBusToken = Symbol("EventBus")
  container.registerSingleton(eventBusToken, () => new EventBusImpl())

  // Use cases
  const createUserUseCaseToken = Symbol("CreateUserUseCase")
  container.registerSingleton(createUserUseCaseToken, () => {
    const userRepository = container.resolve(userRepositoryToken)
    const passwordHasher = container.resolve(passwordHasherToken)
    const emailService = container.resolve(emailServiceToken)
    const eventBus = container.resolve(eventBusToken)
    return new CreateUserUseCase(userRepository, passwordHasher, emailService, eventBus)
  })

  const getUserUseCaseToken = Symbol("GetUserUseCase")
  container.registerSingleton(getUserUseCaseToken, () => {
    const userRepository = container.resolve(userRepositoryToken)
    return new GetUserUseCase(userRepository)
  })

  const updateUserProfileUseCaseToken = Symbol("UpdateUserProfileUseCase")
  container.registerSingleton(updateUserProfileUseCaseToken, () => {
    const userRepository = container.resolve(userRepositoryToken)
    return new UpdateUserProfileUseCase(userRepository)
  })

  const deactivateUserUseCaseToken = Symbol("DeactivateUserUseCase")
  container.registerSingleton(deactivateUserUseCaseToken, () => {
    const userRepository = container.resolve(userRepositoryToken)
    const eventBus = container.resolve(eventBusToken)
    return new DeactivateUserUseCase(userRepository, eventBus)
  })

  // Controllers
  const userControllerToken = Symbol("UserController")
  container.registerSingleton(userControllerToken, () => {
    const httpServer = container.resolve(httpServerToken)
    const createUserUseCase = container.resolve(createUserUseCaseToken)
    const getUserUseCase = container.resolve(getUserUseCaseToken)
    const updateUserProfileUseCase = container.resolve(updateUserProfileUseCaseToken)
    const deactivateUserUseCase = container.resolve(deactivateUserUseCaseToken)
    return new UserController(
      httpServer,
      createUserUseCase,
      getUserUseCase,
      updateUserProfileUseCase,
      deactivateUserUseCase
    )
  })

  return container
}

// Bootstrap application
export const bootstrap = async (): Promise<void> => {
  const container = createContainer()

  const httpServer = container.resolve(Symbol("HttpServer"))
  const userController = container.resolve(Symbol("UserController"))

  await httpServer.listen(3000)
  console.log("✅ Application started on port 3000")
}
```

---

## Example 2: Order Management (Multi-Entity)

### Domain Layer - Multiple Entities

```typescript
// domain/entities/order.entity.ts
export class Order {
  private readonly _items: OrderItem[] = []
  private _status: OrderStatus = "pending"
  private readonly _createdAt: Date

  constructor(
    private readonly _id: OrderId,
    private readonly _userId: UserId
  ) {
    this._createdAt = new Date()
  }

  addItem(product: Product, quantity: number): void {
    if (quantity <= 0) {
      throw new Error("Quantity must be greater than 0")
    }
    this._items.push(new OrderItem(product, quantity))
  }

  submit(): void {
    if (this._status !== "pending") {
      throw new Error("Order can only be submitted from pending state")
    }
    if (this._items.length === 0) {
      throw new Error("Order must have at least one item")
    }
    this._status = "submitted"
  }

  get id(): OrderId { return this._id }
  get userId(): UserId { return this._userId }
  get items(): OrderItem[] { return [...this._items] }
  get status(): OrderStatus { return this._status }
  get createdAt(): Date { return this._createdAt }
}

// Value object for order item
export class OrderItem {
  constructor(
    private readonly product: Product,
    private readonly quantity: number
  ) {}

  getTotal(): Money {
    return this.product.price.multiply(this.quantity)
  }

  get productId(): ProductId { return this.product.id }
}

// Value object for money
export class Money {
  constructor(
    private readonly amount: number,
    private readonly currency: string = "USD"
  ) {
    if (amount < 0) throw new Error("Amount cannot be negative")
  }

  static zero(): Money {
    return new Money(0)
  }

  add(other: Money): Money {
    if (this.currency !== other.currency) {
      throw new Error("Cannot add different currencies")
    }
    return new Money(this.amount + other.amount, this.currency)
  }

  multiply(factor: number): Money {
    return new Money(this.amount * factor, this.currency)
  }

  equals(other: Money): boolean {
    return this.amount === other.amount && this.currency === other.currency
  }

  toJSON() {
    return {
      amount: this.amount,
      currency: this.currency
    }
  }
}
```

### Domain Service (Multi-Entity Logic)

```typescript
// domain/services/pricing.service.ts
export class PricingService {
  constructor(
    private readonly discountRules: DiscountRule[]
  ) {}

  calculateOrderTotal(order: Order): Money {
    let total = Money.zero()

    // Sum all items
    for (const item of order.items) {
      total = total.add(item.getTotal())
    }

    // Apply discount rules
    for (const rule of this.discountRules) {
      if (rule.appliesTo(order)) {
        const discount = rule.calculateDiscount(total)
        total = total.add(Money.zero().subtract(discount))
      }
    }

    return total
  }
}
```

---

## Testing Examples

### Unit Test - Entity

```typescript
import { describe, expect, it } from "bun:test"
import { Order } from "@/domain/entities/order.entity"
import { Product } from "@/domain/entities/product.entity"
import { OrderId, UserId, ProductId } from "@/domain/value-objects"

describe("Order Entity", () => {
  it("should create order with items", () => {
    const orderId = OrderId.generate()
    const userId = UserId.generate()
    const order = new Order(orderId, userId)

    const product = new Product(
      ProductId.generate(),
      "Laptop",
      Money.create(1000)
    )

    order.addItem(product, 2)

    expect(order.items).toHaveLength(1)
    expect(order.items[0].quantity).toBe(2)
  })

  it("should throw when submitting empty order", () => {
    const order = new Order(OrderId.generate(), UserId.generate())

    expect(() => order.submit()).toThrow()
  })

  it("should transition state to submitted", () => {
    const order = new Order(OrderId.generate(), UserId.generate())
    const product = new Product(
      ProductId.generate(),
      "Book",
      Money.create(20)
    )
    order.addItem(product, 1)

    order.submit()

    expect(order.status).toBe("submitted")
  })
})
```

### Integration Test - Use Case

```typescript
import { describe, expect, it, mock } from "bun:test"
import { CreateOrderUseCase } from "@/application/use-cases/create-order.use-case"

describe("CreateOrderUseCase", () => {
  it("should create order with validation", async () => {
    const mockOrderRepository = {
      save: mock(async () => {}),
      findById: mock(async () => undefined)
    }

    const mockProductRepository = {
      findById: mock(async (id) => ({
        id,
        name: "Laptop",
        price: { amount: 1000, currency: "USD" }
      }))
    }

    const useCase = new CreateOrderUseCase(
      mockOrderRepository as any,
      mockProductRepository as any
    )

    const result = await useCase.execute({
      userId: "user-123",
      items: [{ productId: "product-456", quantity: 2 }]
    })

    expect(mockOrderRepository.save).toHaveBeenCalledTimes(1)
    expect(result.status).toBe("submitted")
  })
})
```
