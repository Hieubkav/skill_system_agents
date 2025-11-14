---
name: orm-code-generator
description: |
  Generate ORM models and database schemas for TypeORM, Prisma, Sequelize, SQLAlchemy, Django ORM, Entity Framework, and Hibernate. Supports both database-to-code and code-to-database generation with entities, relationships, and validations. Use when creating ORM models, generating entities, creating migrations, or working with any ORM framework.
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 1.0.0
---

# ORM Code Generator

## Level 1: Overview

Automate creation of Object-Relational Mapping (ORM) models and database schemas for various frameworks, significantly accelerating backend development.

## Prerequisites

- Target ORM framework installed (TypeORM, Prisma, etc.)
- Understanding of database schema
- Project structure set up

## What This Skill Does

1. Identifies target ORM framework and language
2. Interprets database schema or model specifications
3. Generates ORM model code with entities and relationships
4. Includes validation rules and decorators
5. Creates configuration files for the ORM

---

## Level 2: Quick Start

### Basic Usage

**Step 1: Specify ORM and requirements**
```
Example: "Generate TypeORM entities for a blog with users, posts, and comments"
```

**Step 2: Define entities and relationships**
```typescript
// TypeORM entities with relationships
@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  email: string;

  @OneToMany(() => Post, post => post.user)
  posts: Post[];
}
```

**Step 3: Review and integrate**
- Check generated code
- Add custom business logic
- Test with database

### Supported ORMs

**JavaScript/TypeScript:**
- TypeORM - Decorators, repositories
- Prisma - Schema definition language
- Sequelize - Model classes

**Python:**
- SQLAlchemy - Declarative base
- Django ORM - Model classes

**C#:**
- Entity Framework - Code First

**Java:**
- Hibernate - JPA annotations

---

## Common Use Cases

### Case 1: Bootstrap New Project
Starting project, generate all ORM models from database schema specification, saving hours of boilerplate code writing.

### Case 2: Migrate to Different ORM
Converting from Sequelize to TypeORM. Generate equivalent TypeORM entities from existing Sequelize models.

### Case 3: Database-First Development
Existing database, generate ORM models that match current schema for new application layer.

---

## Best Practices

1. **Relationships**
   - Define clear relationship types (1-to-1, 1-to-many, many-to-many)
   - Use cascade options appropriately
   - Consider lazy vs eager loading

2. **Validation**
   - Add validation decorators (@IsEmail, @Length)
   - Validate at model level for consistency
   - Use database constraints as backup

3. **Naming Conventions**
   - Follow ORM framework conventions
   - Use singular for entity names (User, not Users)
   - Clear relationship property names

4. **Performance**
   - Add indexes for frequently queried fields
   - Use select/relations wisely to avoid N+1
   - Consider pagination for large datasets

5. **Testing**
   - Test models with actual database
   - Verify relationships work correctly
   - Test validation rules

---

## Generation Patterns

**Pattern 1: Entity with Relations**
```typescript
// One-to-many with User -> Posts
@OneToMany(() => Post, post => post.user)
posts: Post[];
```

**Pattern 2: Many-to-Many**
```typescript
// Many-to-many with Posts <-> Tags
@ManyToMany(() => Tag, tag => tag.posts)
@JoinTable()
tags: Tag[];
```

**Pattern 3: Validation**
```typescript
@Column()
@IsEmail()
@Length(5, 255)
email: string;
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Circular dependency | Use lazy relations or string references |
| Migration fails | Check column types match model definitions |
| N+1 query problem | Use eager loading or query builder joins |
| Validation not working | Ensure validation pipe enabled in framework |
| Type errors | Verify TypeScript types match database types |

---

## Integration

- **Migration Tools** - Generate migrations from model changes
- **Validation Libraries** - class-validator, Joi, Yup
- **Query Builders** - TypeORM query builder, Prisma client
- **Testing** - Jest, Mocha with test database
