---
name: database-seeding
description: Generate realistic database seed data and test fixtures for development, testing, and demos. Creates users, products, orders, and custom schemas with Faker libraries while maintaining relational integrity. Use when populating databases, creating test fixtures, seeding development environments, generating demo data, or creating realistic test datasets.
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 1.0.0
---

# Database Seeding

## Level 1: Overview

Automate creation of realistic seed data and test fixtures for databases, ensuring relational integrity and data consistency across development and testing environments.

## Prerequisites

- Database connection and credentials
- Faker library or equivalent data generation tool
- Understanding of database schema and relationships

## What This Skill Does

1. Analyzes database schema, tables, columns, and relationships
2. Generates realistic data using Faker libraries
3. Maintains foreign key integrity across related tables
4. Creates seed scripts (SQL, migration files, ORM seeds)
5. Exports data in multiple formats (SQL, JSON, JavaScript, Python)

---

## Level 2: Quick Start

### Basic Usage

**Generate seed data:**
```bash
# Simple: 50 users
Create seed data for users table with 50 realistic users

# Complex: E-commerce with relationships
Generate test data for e-commerce: users, products, categories, orders

# Custom volume
Populate blog database with 100 users, 500 posts, 2000 comments
```

### Key Capabilities

**Database Seeding:**
- Production-like data for development databases
- Initial data for new installations
- Reset database to known state for testing
- Demo data for presentations

**Test Data Generation:**
- Diverse test cases including edge cases
- JSON, CSV, SQL, or framework-specific fixtures
- Load testing data
- Boundary condition testing

---

## Common Use Cases

### Case 1: Development Database Setup
Populate local development database with realistic users, products, and orders so developers can test features without manual data entry.

### Case 2: Automated Testing
Generate test fixtures with specific edge cases (empty values, max lengths, special characters) for comprehensive test coverage.

### Case 3: Demo Environment
Create demo database with sample data showcasing all application features for client presentations or user training.

---

## Best Practices

1. **Start Small** - Generate 10-50 records initially, scale gradually
2. **Respect Constraints** - Honor NOT NULL, UNIQUE, foreign key constraints
3. **Maintain Relationships** - Create parent records before child records
4. **Idempotency** - Design seeds to run multiple times safely
5. **Include Edge Cases** - Empty strings, null values, max lengths for testing
6. **Use Transactions** - Wrap seed operations in transactions for rollback
7. **Document Data** - Comment seed files explaining purpose and relationships

---

## Supported Frameworks

- **Laravel**: Database seeders with factories
- **Prisma**: TypeScript seed scripts
- **Django**: Management commands and fixtures
- **Rails**: db/seeds.rb files
- **Sequelize**: JavaScript/TypeScript seeders
- **SQLAlchemy**: Python seed scripts
- **Raw SQL**: INSERT statements

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Foreign key violation | Create parent records first, check references |
| Duplicate key error | Use unique values or UPSERT logic |
| Slow performance | Use batch inserts, reduce volume |
| Data type mismatch | Match Faker output to column data types |
| Memory issues | Process in smaller batches |

---

## Integration

- **Testing Frameworks** - Jest, Pytest, PHPUnit, Mocha
- **CI/CD Pipelines** - Automate seeding in test environments
- **Docker** - Include seeds in container initialization
- **Migration Tools** - Coordinate with schema migrations
