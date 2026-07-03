# SQL Server vs PostgreSQL Profiling & Performance Troubleshooting Guide

## Contents
1. Profiling Strategy
2. SQL Server Tooling
3. PostgreSQL Tooling
4. Wait Analysis
5. Locking & Blocking
6. Deadlocks
7. Query Plans
8. Index Analysis
9. N+1 Detection
10. Connection Pooling
11. Memory Issues
12. CPU Bottlenecks
13. I/O Bottlenecks
14. Migration Checklist
15. Practical Investigation Workflow

# 1. Profiling Strategy

When investigating performance issues:

1. Identify the symptom.
2. Measure before changing anything.
3. Find expensive queries.
4. Inspect execution plans.
5. Analyze waits.
6. Check indexes.
7. Validate improvements.

# 2. SQL Server Tooling

## Query Store

Best source for:
- Query history
- Runtime changes
- Plan regressions
- Deployment comparisons

## Extended Events

Use for:
- Deadlocks
- Long-running queries
- Timeouts
- Blocking analysis

## DMVs

```sql
SELECT * FROM sys.dm_exec_requests;
SELECT * FROM sys.dm_exec_sessions;
SELECT * FROM sys.dm_exec_query_stats;
SELECT * FROM sys.dm_os_wait_stats;
```

# 3. PostgreSQL Tooling

## pg_stat_statements

```sql
SELECT *
FROM pg_stat_statements
ORDER BY total_exec_time DESC;
```

Useful for identifying:
- Slow queries
- Frequently executed queries
- Resource hot spots

## EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE customer_id = 123;
```

## auto_explain

```conf
auto_explain.log_min_duration = 500ms
```

# 4. Wait Analysis

## SQL Server

Common waits:

- PAGEIOLATCH_* → slow storage
- CXPACKET / CXCONSUMER → parallelism
- LCK_* → locking
- WRITELOG → transaction log bottleneck

## PostgreSQL

Monitor:

```sql
SELECT *
FROM pg_stat_activity;
```

Focus on:
- wait_event
- wait_event_type

# 5. Locking & Blocking

## SQL Server

```sql
sp_who2
```

and:

```sql
SELECT *
FROM sys.dm_exec_requests;
```

## PostgreSQL

```sql
SELECT *
FROM pg_locks;
```

# 6. Deadlocks

## SQL Server

Capture via:
- Extended Events
- system_health session

## PostgreSQL

Deadlocks are written into logs.

# 7. Query Plans

Investigate:

- Table scans
- Large sorts
- Hash joins
- Key lookups
- Missing indexes

Red flags:

- Estimated rows significantly different from actual rows.
- Sequential scan on large tables.

# 8. Index Analysis

## Index Types

### SQL Server

- Clustered
- Nonclustered
- Filtered
- Columnstore

### PostgreSQL

- B-Tree
- Hash
- GiST
- GIN
- BRIN

## Missing Index Symptoms

- Frequent scans
- High logical reads
- Slow lookups

# 9. N+1 Problem Detection

Example:

```csharp
foreach(var customer in customers)
{
    LoadOrders(customer.Id);
}
```

Symptoms:
- Thousands of tiny queries
- Excessive DB traffic

Tools:
- Query Store
- MiniProfiler
- pg_stat_statements

# 10. Connection Pooling

## SQL Server

Common causes:
- Leaked DbContexts
- Long transactions
- Missing disposal

## PostgreSQL

Consider:
- Npgsql pooling
- PgBouncer

# 11. Memory Issues

Symptoms:

- Excessive paging
- Sort spills
- TempDB growth
- Temporary file growth

# 12. CPU Bottlenecks

Causes:

- Missing indexes
- Poor plans
- Scalar functions
- Large sorts

# 13. I/O Bottlenecks

Symptoms:

- Slow disk reads
- High latency
- Log write waits

Investigate:

- Storage metrics
- Scan-heavy queries

# 14. SQL Server to PostgreSQL Migration Checklist

## Before Migration

- Capture baseline metrics.
- Capture top SQL.
- Document indexes.
- Document batch jobs.

## After Migration

- Enable pg_stat_statements.
- Compare execution times.
- Compare query plans.
- Review connection usage.
- Optimize indexes.

# 15. Practical Investigation Workflow

## Application Slow

Step 1
Check connection count.

Step 2
Identify top queries.

Step 3
Review execution plans.

Step 4
Check waits.

Step 5
Check locks.

Step 6
Validate indexes.

Step 7
Retest.

# Senior Engineer Checklist

✅ Query Store enabled
✅ Extended Events enabled
✅ pg_stat_statements enabled
✅ auto_explain enabled
✅ Baselines collected
✅ Connection pooling reviewed
✅ N+1 issues checked
✅ Deadlocks reviewed
✅ Wait statistics analyzed
✅ Execution plans analyzed

This guide can be used as a quick reference during production troubleshooting and migration projects.
