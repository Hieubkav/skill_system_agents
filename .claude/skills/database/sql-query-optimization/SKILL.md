---
name: sql-query-optimization
description: Master SQL query optimization, indexing strategies, and EXPLAIN analysis to dramatically improve database performance and eliminate slow queries. Learn proven patterns for fixing N+1 queries, implementing efficient pagination, and optimizing JOINs. Use when debugging slow queries, designing schemas, or optimizing application performance.
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 1.0.0
---

# SQL Query Optimization

## Level 1: Overview

Transform slow database queries into lightning-fast operations through systematic optimization, proper indexing, and query plan analysis using proven patterns.

## Prerequisites

- Access to database with SELECT privileges
- Slow query logs or sample queries
- Understanding of database schema

## What This Skill Does

1. Identifies slow queries using database statistics
2. Analyzes EXPLAIN plans to find bottlenecks
3. Recommends strategic indexes
4. Optimizes query structure and patterns
5. Implements advanced techniques (materialized views, partitioning)
6. Provides monitoring and maintenance guidance

---

## Level 2: Quick Start

### Optimization Workflow

**Step 1: Identify slow queries**
```sql
-- PostgreSQL: Find slowest queries
SELECT query, mean_time, calls
FROM pg_stat_statements
ORDER BY mean_time DESC LIMIT 10;
```

**Step 2: Analyze with EXPLAIN**
```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE status = 'pending';

-- Look for:
-- ❌ Seq Scan (full table scan)
-- ✅ Index Scan (using index)
-- Check: Cost, Rows, Actual Time
```

**Step 3: Add strategic indexes**
```sql
-- Simple index
CREATE INDEX idx_orders_status ON orders(status);

-- Composite index (order matters!)
CREATE INDEX idx_orders_status_date 
  ON orders(status, created_at);
```

**Step 4: Optimize query structure**
```sql
-- ❌ Bad: SELECT *
SELECT * FROM users WHERE id = 123;

-- ✅ Good: Select only needed columns
SELECT id, email, name FROM users WHERE id = 123;
```

### Key Optimization Patterns

**Pattern 1: Eliminate N+1 Queries**
```python
# ❌ Bad: N+1 problem
posts = Post.all()  # 1 query
for post in posts:
    print(post.author.name)  # N queries

# ✅ Good: Use JOIN/eager loading
posts = Post.select_related('author').all()  # 1 query
```

**Pattern 2: Efficient Pagination**
```sql
-- ❌ Bad: OFFSET (slow for large offsets)
SELECT * FROM posts ORDER BY id LIMIT 20 OFFSET 10000;

-- ✅ Good: Cursor-based
SELECT * FROM posts WHERE id > 10000 ORDER BY id LIMIT 20;
```

**Pattern 3: Optimize Subqueries**
```sql
-- ❌ Bad: Correlated subquery
SELECT * FROM users WHERE id IN (
    SELECT DISTINCT user_id FROM orders WHERE total > 1000
);

-- ✅ Good: JOIN
SELECT DISTINCT u.* 
FROM users u 
JOIN orders o ON u.id = o.user_id 
WHERE o.total > 1000;
```

---

## Common Use Cases

### Case 1: Fix Slow Dashboard Query
Dashboard loads in 10 seconds. Analyze EXPLAIN plan, identify missing indexes, add composite index on filter columns, query now runs in 200ms.

### Case 2: Eliminate N+1 in API
API endpoint makes 500 queries for 100 items. Rewrite with JOINs and eager loading, reduce to 2 queries, 50x faster response.

### Case 3: Optimize Report Generation
Monthly report takes 30 minutes. Create materialized view with pre-aggregated data, refresh nightly, report now generates in 5 seconds.

---

## Best Practices

**Index Management:**
- ✅ Index WHERE, JOIN, ORDER BY columns
- ✅ Use composite indexes for multi-column queries
- ✅ Create partial indexes for subsets
- ❌ Don't over-index (slows writes)
- ❌ Drop unused indexes

**Query Optimization:**
- ✅ Select only needed columns
- ✅ Filter before joining
- ✅ Use batch operations
- ✅ Implement cursor pagination
- ❌ Avoid SELECT *
- ❌ Don't use functions on indexed columns
- ❌ Avoid N+1 queries

**Maintenance:**
- ✅ Run ANALYZE regularly
- ✅ VACUUM to reclaim space
- ✅ Monitor slow query log
- ✅ Update statistics after bulk operations

---

## Common Pitfalls

1. **Over-Indexing** - Each index slows INSERT/UPDATE/DELETE
2. **Function on Column** - `WHERE LOWER(email) = 'x'` prevents index usage
3. **Implicit Conversion** - Type mismatch forces full scan
4. **Leading Wildcard** - `LIKE '%pattern'` can't use index
5. **OR Conditions** - May prevent index usage, use UNION instead
6. **Missing Statistics** - Run ANALYZE to update

---

## Quick Reference Commands

```sql
-- Check index usage
EXPLAIN ANALYZE your_query;

-- Find missing indexes (PostgreSQL)
SELECT tablename, seq_scan, idx_scan
FROM pg_stat_user_tables
WHERE seq_scan > idx_scan AND seq_scan > 1000;

-- Find unused indexes
SELECT indexname, idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0;

-- Update statistics
ANALYZE tablename;

-- Vacuum (PostgreSQL)
VACUUM ANALYZE tablename;
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Index not being used | Check data types match, avoid functions on column |
| Query slower after index | Index selectivity poor, consider partial index |
| Out of memory | Reduce result set, add LIMIT, increase work_mem |
| Slow writes | Too many indexes, remove unused ones |
| Deadlocks | Review transaction isolation, reduce lock scope |

---

## Integration

- **Monitoring** - New Relic, Datadog, Prometheus
- **Profiling** - pg_stat_statements, MySQL Performance Schema
- **Load Testing** - Find performance limits with realistic data
- **ORM** - Understand queries generated by ORM, optimize accordingly
