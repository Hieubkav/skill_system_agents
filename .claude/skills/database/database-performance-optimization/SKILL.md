---
name: database-performance-optimization
description: Master database and SQL query optimization through index analysis, EXPLAIN plans, query profiling, N+1 pattern fixes, efficient pagination, and performance tuning. Identify bottlenecks, optimize slow queries, and implement indexing strategies for dramatically improved database performance. Use when debugging slow queries, analyzing workloads, improving query execution speed, or optimizing application performance.
allowed-tools: Read, Write, Edit, Grep, Glob, Execute
version: 2.0.0
---

# Database Performance Optimization

## Level 1: Overview

Comprehensive database and SQL query optimization covering index strategies, query profiling, EXPLAIN plan analysis, N+1 pattern elimination, and performance tuning to transform slow queries into lightning-fast operations.

## Prerequisites

- Database access with SELECT privileges
- Slow query logs or performance monitoring enabled
- Understanding of database schema and query patterns

## What This Skill Does

1. Identifies slow queries using database statistics and logs
2. Analyzes EXPLAIN plans to find bottlenecks
3. Recommends strategic indexes (simple, composite, partial)
4. Optimizes query structure and patterns
5. Eliminates N+1 queries and implements efficient pagination
6. Identifies and removes unused indexes
7. Provides monitoring and maintenance guidance

---

## Level 2: Quick Start

### Optimization Workflow

**Step 1: Identify slow queries**
```sql
-- PostgreSQL: Find slowest queries
SELECT query, mean_time, calls
FROM pg_stat_statements
ORDER BY mean_time DESC LIMIT 10;

-- Or enable slow query logging
-- Find queries taking > 2 seconds
```

**Step 2: Analyze with EXPLAIN**
```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE status = 'pending';

-- Look for:
-- ❌ Seq Scan (full table scan - BAD)
-- ✅ Index Scan (using index - GOOD)
-- ✅ Index Only Scan (best performance)
-- Check: Cost (lower = better), Rows (fewer = better), Actual Time
```

**Step 3: Add strategic indexes**
```sql
-- Simple index on WHERE column
CREATE INDEX idx_orders_status ON orders(status);

-- Composite index (order matters! Most selective first)
CREATE INDEX idx_orders_status_date 
  ON orders(status, created_at);

-- Partial index for subsets
CREATE INDEX idx_active_orders 
  ON orders(user_id) WHERE status = 'active';
```

**Step 4: Optimize query structure**
```sql
-- ❌ Bad: SELECT *
SELECT * FROM users WHERE id = 123;

-- ✅ Good: Select only needed columns
SELECT id, email, name FROM users WHERE id = 123;
```

**Step 5: Verify improvement**
```sql
-- Re-run EXPLAIN to confirm optimization
EXPLAIN ANALYZE your_optimized_query;
-- Verify: Lower cost, faster execution, using indexes
```

---

## Key Optimization Patterns

### Pattern 1: Eliminate N+1 Queries

**Problem**: Application makes 1+N queries in loop
```python
# ❌ Bad: N+1 problem (1 + 100 queries)
posts = Post.all()  # 1 query
for post in posts:
    print(post.author.name)  # N queries (100 times)
```

**Solution**: Use JOIN/eager loading
```python
# ✅ Good: Eager loading (1-2 queries total)
posts = Post.select_related('author').all()
for post in posts:
    print(post.author.name)  # No extra queries
```

### Pattern 2: Efficient Pagination

**Problem**: OFFSET slow for large offsets
```sql
-- ❌ Bad: OFFSET (scans 10,000 rows to skip them)
SELECT * FROM posts ORDER BY id LIMIT 20 OFFSET 10000;
```

**Solution**: Cursor-based pagination
```sql
-- ✅ Good: Cursor-based (direct seek)
SELECT * FROM posts WHERE id > 10000 ORDER BY id LIMIT 20;
```

### Pattern 3: Optimize Subqueries

**Problem**: Correlated subquery runs for each row
```sql
-- ❌ Bad: Correlated subquery
SELECT * FROM users WHERE id IN (
    SELECT DISTINCT user_id FROM orders WHERE total > 1000
);
```

**Solution**: Use JOIN
```sql
-- ✅ Good: JOIN (single pass)
SELECT DISTINCT u.* 
FROM users u 
JOIN orders o ON u.id = o.user_id 
WHERE o.total > 1000;
```

---

## Common Use Cases

### Case 1: Optimize Slow SELECT Query
Query taking 5 seconds. Run EXPLAIN, identify missing index on WHERE clause, create index, verify query now runs in 50ms.

### Case 2: Fix Slow Dashboard
Dashboard loads in 10 seconds. Analyze EXPLAIN plan, add composite index on filter columns, optimize query structure, dashboard now loads in 200ms.

### Case 3: Eliminate N+1 in API
API endpoint makes 500 queries for 100 items. Rewrite with JOINs and eager loading, reduce to 2 queries, 50x faster response.

### Case 4: Remove Unused Indexes
Database has 50 indexes but only 30 are used. Identify unused ones, verify not needed for constraints, drop them to improve write performance by 20%.

### Case 5: Optimize Report Generation
Monthly report takes 30 minutes. Create materialized view with pre-aggregated data, refresh nightly, report now generates in 5 seconds.

---

## Best Practices

### Index Management
- ✅ Index WHERE, JOIN, ORDER BY columns
- ✅ Use composite indexes for multi-column queries (order matters!)
- ✅ Create partial indexes for filtered subsets
- ✅ Monitor index usage regularly
- ❌ Don't over-index (each index slows INSERT/UPDATE/DELETE)
- ❌ Drop unused indexes

### Query Optimization
- ✅ Select only needed columns (not SELECT *)
- ✅ Filter before joining (reduce row count early)
- ✅ Use LIMIT for pagination
- ✅ Batch operations when possible
- ✅ Implement cursor-based pagination
- ❌ Avoid functions on indexed columns (breaks index)
- ❌ Avoid N+1 queries (use JOINs/eager loading)
- ❌ Don't use leading wildcards in LIKE

### Maintenance
- ✅ Run ANALYZE regularly to update statistics
- ✅ VACUUM to reclaim space (PostgreSQL)
- ✅ Monitor slow query log
- ✅ Benchmark before and after changes
- ✅ Update statistics after bulk operations

---

## Common Bottlenecks

1. **Missing Indexes** → Full table scans (Seq Scan)
2. **Inefficient JOINs** → Large row counts, multiple passes
3. **N+1 Queries** → Loop queries in application code
4. **Large Result Sets** → No pagination or LIMIT
5. **Complex Subqueries** → Nested scans, correlated queries
6. **Unused Indexes** → Slow writes, wasted storage
7. **Function on Column** → `WHERE LOWER(email)` prevents index usage
8. **Implicit Conversion** → Type mismatch forces full scan
9. **Leading Wildcard** → `LIKE '%pattern'` can't use index

---

## Key Metrics & Indicators

### EXPLAIN Plan Indicators
- **Seq Scan** → Full table scan (slow, needs index)
- **Index Scan** → Using index (good)
- **Index Only Scan** → Index contains all data (best)
- **Cost** → Lower is better
- **Rows** → Fewer is better
- **Actual Time** → Real execution time

### Performance Thresholds
- Query time: Aim for <100ms
- Index scan ratio: Target >90%
- N+1 queries: Should be 0
- Cache hit ratio: Target >95%

---

## Quick Reference Commands

```sql
-- Analyze query performance
EXPLAIN ANALYZE your_query;

-- Find missing indexes (PostgreSQL)
SELECT tablename, seq_scan, idx_scan
FROM pg_stat_user_tables
WHERE seq_scan > idx_scan AND seq_scan > 1000;

-- Find unused indexes
SELECT indexrelname AS index_name, 
       idx_scan AS index_scans
FROM pg_stat_user_indexes
WHERE idx_scan = 0
  AND indexrelname NOT LIKE 'pg_%';

-- Update statistics
ANALYZE tablename;

-- Vacuum (PostgreSQL)
VACUUM ANALYZE tablename;

-- Check slow queries (PostgreSQL)
SELECT query, mean_time, calls
FROM pg_stat_statements
ORDER BY mean_time DESC LIMIT 20;
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Query still slow after index | Check if index is actually used (EXPLAIN), ensure data types match |
| Index not being used | Avoid functions on indexed column, check type matching |
| Query slower after index | Index selectivity poor, consider partial index or different columns |
| Out of memory | Reduce result set size, add LIMIT, increase work_mem |
| Write performance degraded | Too many indexes, remove unused ones |
| Deadlocks | Review transaction isolation levels, reduce lock scope |
| Slow JOINs | Add indexes on JOIN columns, filter before joining |
| N+1 still occurring | Use ORM eager loading, check for hidden queries |

---

## Common Pitfalls

1. **Over-Indexing** - Each index slows INSERT/UPDATE/DELETE operations
2. **Function on Column** - `WHERE LOWER(email) = 'x'` prevents index usage
3. **Implicit Conversion** - Type mismatch (e.g., VARCHAR vs INT) forces full scan
4. **Leading Wildcard** - `LIKE '%pattern'` can't use index efficiently
5. **OR Conditions** - May prevent index usage, consider UNION instead
6. **Missing Statistics** - Run ANALYZE after bulk operations to update planner stats
7. **Ignoring EXPLAIN** - Always verify query plans before and after optimization
8. **Not Monitoring** - Set up slow query logging and monitoring

---

## Integration & Tools

- **Monitoring** - New Relic, Datadog, Prometheus, Grafana
- **Profiling** - pg_stat_statements (PostgreSQL), Performance Schema (MySQL)
- **Query Logging** - Slow query logs for identifying problem queries
- **Load Testing** - Find performance limits with realistic data volumes
- **ORM Integration** - Understand queries generated by ORM (Django, Sequelize, Prisma)
- **APM Tools** - Application Performance Monitoring for end-to-end visibility

---

## Advanced Techniques

### Materialized Views
```sql
-- Create pre-aggregated view
CREATE MATERIALIZED VIEW monthly_sales_summary AS
SELECT product_id, SUM(amount) as total
FROM orders
WHERE created_at >= date_trunc('month', CURRENT_DATE)
GROUP BY product_id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW monthly_sales_summary;
```

### Partitioning
```sql
-- Partition large tables by date
CREATE TABLE orders_2024_01 PARTITION OF orders
FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

### Connection Pooling
- Use pgBouncer (PostgreSQL) or ProxySQL (MySQL)
- Reduce connection overhead
- Improve concurrency

---

## Summary

Effective database optimization requires:
1. ✅ Systematic identification of slow queries
2. ✅ EXPLAIN plan analysis to find bottlenecks
3. ✅ Strategic index creation and management
4. ✅ Query structure optimization
5. ✅ N+1 elimination and pagination strategies
6. ✅ Regular maintenance and monitoring
7. ✅ Continuous performance testing

**Result**: Transform 10-second queries into sub-100ms operations, improve application responsiveness, and reduce infrastructure costs.
