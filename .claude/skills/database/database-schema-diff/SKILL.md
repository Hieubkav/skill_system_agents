---
name: database-schema-diff
description: |
  Leverages database-diff-tool plugin to compare schemas, generate migration scripts, and provide rollback procedures for PostgreSQL/MySQL. Use when comparing database schemas across environments, generating migration scripts, synchronizing databases, or creating rollback procedures. Activates on: "database diff", "schema comparison", "generate migration", "database synchronization", "/db-diff".
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 1.0.0
---

# Database Schema Diff

## Level 1: Overview

Perform production-grade database schema comparisons, generate safe migration scripts, and create rollback procedures. Simplifies keeping database schemas synchronized across environments while ensuring data integrity and minimizing downtime.

## Prerequisites

- Database credentials for source and target databases
- PostgreSQL or MySQL database access
- Backup of target database before applying migrations

## What This Skill Does

1. Compares schemas between two databases (PostgreSQL or MySQL)
2. Identifies differences in tables, columns, indexes, constraints, triggers
3. Generates safe migration scripts with transaction safety
4. Creates rollback procedures for error recovery
5. Provides validation recommendations before deployment

---

## Level 2: Quick Start

### Basic Usage

**Step 1: Compare schemas**
```bash
# Identify differences between development and production
Compare schemas: dev_db vs prod_db
```

**Step 2: Generate migration**
```bash
# Create migration script from differences
Generate migration from dev_db to staging_db
```

**Step 3: Review & validate**
- Review generated script for correctness
- Test in staging environment first
- Verify rollback procedure

**Step 4: Apply migration**
```bash
# Execute migration with transaction safety
Apply migration to target database
```

### Key Principles

- **Always backup first** - Never run migrations without backups
- **Test in staging** - Validate migrations before production
- **Transaction safety** - Migrations use transactions where possible
- **Rollback ready** - Every migration includes rollback script

---

## Common Use Cases

### Case 1: Sync Development to Production
Compare dev and production schemas, generate migration to update production with new tables, columns, or indexes added during development.

### Case 2: Environment Consistency
Ensure staging matches production schema exactly by comparing and generating synchronization scripts.

### Case 3: Schema Version Control
Track schema changes over time by generating migrations between versions and storing them in version control.

---

## Best Practices

1. **Backup First** - Always create database backup before migrations
2. **Test Migrations** - Run in test/staging before production
3. **Review Scripts** - Manually review generated SQL
4. **Small Iterations** - Frequent small migrations over large ones
5. **Document Changes** - Add comments explaining why changes were made
6. **Monitor Execution** - Watch migration progress for errors
7. **Verify Results** - Check data integrity after migration

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Connection failed | Verify database credentials and network access |
| Permission denied | Ensure user has SELECT on information_schema |
| Migration failed | Run rollback script, check logs, fix issue |
| Data loss warning | Review script carefully, may need custom migration |
| Timeout on large table | Split migration into smaller batches |

---

## Integration

- **CI/CD Pipelines** - Automate schema validation in deployment pipelines
- **Version Control** - Store migrations with application code
- **Monitoring Tools** - Alert on schema drift between environments
- **Documentation** - Generate schema change documentation automatically
