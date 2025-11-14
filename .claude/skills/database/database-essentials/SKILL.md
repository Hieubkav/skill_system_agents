---
name: database-essentials
description: Work with MongoDB (document database, BSON, aggregation pipelines, Atlas) and PostgreSQL (relational database, SQL, psql, pgAdmin). Use when designing schemas, writing queries/aggregations, optimizing indexes, performing migrations, configuring replication/sharding, implementing backups, managing users/permissions, analyzing performance, or administering databases.
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 1.0.0
---

# Database Essentials

## Level 1: Overview

Unified guide for working with MongoDB (document-oriented) and PostgreSQL (relational) databases. Choose the right database for your use case and master both systems.

## Prerequisites

- Database installation (MongoDB/PostgreSQL)
- Basic understanding of databases
- Connection credentials

## What This Skill Does

1. Guides database selection (MongoDB vs PostgreSQL)
2. Provides setup and connection instructions
3. Shows CRUD operations for both databases
4. Demonstrates indexing and performance optimization
5. Covers administration tasks (users, backups, replication)

---

## Level 2: Quick Start

### Database Selection Guide

**Choose MongoDB when:**
- Schema flexibility with frequent changes
- Document-centric JSON/BSON data model
- Horizontal scaling (sharding)
- High write throughput (IoT, logging)
- Nested/hierarchical data

**Choose PostgreSQL when:**
- Strong consistency (ACID transactions)
- Complex relationships (joins, foreign keys)
- SQL requirement (team expertise, BI tools)
- Strict schema validation
- Complex analytical queries

### MongoDB Quick Start

```javascript
// Connect
mongosh "mongodb+srv://cluster.mongodb.net/mydb"

// CRUD
db.users.insertOne({ name: "Alice", age: 30 })
db.users.find({ age: { $gte: 18 } })
db.users.updateOne({ name: "Alice" }, { $set: { age: 31 } })
db.users.deleteOne({ name: "Alice" })

// Index
db.users.createIndex({ email: 1 })
```

### PostgreSQL Quick Start

```sql
-- Connect
psql -U postgres -d mydb

-- CRUD
INSERT INTO users (name, age) VALUES ('Alice', 30);
SELECT * FROM users WHERE age >= 18;
UPDATE users SET age = 31 WHERE name = 'Alice';
DELETE FROM users WHERE name = 'Alice';

-- Index
CREATE INDEX idx_users_email ON users(email);
```

---

## Common Use Cases

### Case 1: Content Management System
Use MongoDB for flexible content structures (blog posts, pages, media) that change frequently and have varying schemas.

### Case 2: E-Commerce Platform
Use PostgreSQL for orders, payments, inventory with strict ACID requirements and complex relationships between entities.

### Case 3: Real-Time Analytics
Use MongoDB for high-volume time-series data (logs, events) with horizontal scaling via sharding.

---

## Best Practices

**MongoDB:**
- Embed documents for 1-to-few relationships
- Reference documents for 1-to-many
- Index frequently queried fields
- Use aggregation pipeline for transformations
- Enable authentication and TLS

**PostgreSQL:**
- Normalize to 3NF, denormalize for performance
- Use foreign keys for integrity
- Index foreign keys and filter columns
- Use EXPLAIN ANALYZE for optimization
- Regular VACUUM and ANALYZE
- Connection pooling (pgBouncer)

---

## Key Differences

| Feature | MongoDB | PostgreSQL |
|---------|---------|------------|
| Data Model | Document (JSON/BSON) | Relational (Tables) |
| Schema | Flexible | Strict |
| Query Language | MQL | SQL |
| Joins | $lookup | Native |
| Scaling | Horizontal (sharding) | Vertical |
| Transactions | Multi-doc (4.0+) | Native ACID |

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Connection refused | Check service running, firewall, credentials |
| Slow queries | Add indexes, analyze EXPLAIN plan |
| Out of connections | Implement connection pooling |
| Disk space full | Archive/delete old data, increase storage |
| Replication lag | Check network, increase resources |

---

## Integration

- **ORMs** - Mongoose (MongoDB), Prisma, Sequelize, TypeORM
- **Admin Tools** - MongoDB Atlas, pgAdmin, DBeaver
- **Monitoring** - Datadog, New Relic, Prometheus
- **Backup** - mongodump/mongorestore, pg_dump/pg_restore
