# MS SQL Server vs PostgreSQL Profiling

## Executive Summary

This document compares SQL Server and PostgreSQL profiling, diagnostics, performance investigation, query analysis, connection monitoring, and troubleshooting approaches.

## Tool Mapping

| SQL Server | PostgreSQL | Purpose |
|------------|------------|---------|
| Query Store | pg_stat_statements | Historical query analysis |
| Extended Events | auto_explain | Slow query capture |
| DMVs | pg_stat_activity | Live monitoring |
| Actual Execution Plan | EXPLAIN ANALYZE | Query optimization |
| Activity Monitor | pgAdmin Dashboard | Live server visibility |
| Wait Statistics | wait_event views | Contention analysis |

## SQL Server Profiling

### Query Store

Use Query Store for:

- Query history
- Runtime statistics
- Plan regressions
- Deployment impact analysis
- Historical troubleshooting

### Extended Events

Use for:

- Deadlocks
- Blocking
- Long-running queries
- Timeout analysis
- CPU spike investigations

### Most Useful DMVs

```sql
SELECT * FROM sys.dm_exec_requests;
SELECT * FROM sys.dm_exec_sessions;
SELECT * FROM sys.dm_exec_query_stats;
SELECT * FROM sys.dm_os_wait_stats;
```

## PostgreSQL Profiling

### pg_stat_statements

Provides:

- Execution counts
- Average runtime
- Total runtime
- Query normalization
- I/O statistics

```sql
SELECT *
FROM pg_stat_statements
ORDER BY total_exec_time DESC;
```

### EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE customer_id = 10;
```

Look for:

- Sequential scans
- Missing indexes
- Expensive joins
- Incorrect row estimates

### auto_explain

```conf
auto_explain.log_min_duration = 500ms
```

Captures execution plans automatically for slow queries.

## Too Many Connections Investigation

### SQL Server

```sql
SELECT COUNT(*)
FROM sys.dm_exec_sessions;
```

### PostgreSQL

```sql
SELECT COUNT(*)
FROM pg_stat_activity;
```

```sql
SELECT application_name,
       COUNT(*)
FROM pg_stat_activity
GROUP BY application_name;
```

## N+1 Detection

Typical symptom:

One small query executed thousands of times.

### SQL Server

- Query Store
- Extended Events
- MiniProfiler

### PostgreSQL

- pg_stat_statements
- auto_explain

## Migration Recommendations

### SQL Server

Enable:

- Query Store
- Extended Events
- Deadlock capture
- Wait statistics monitoring

### PostgreSQL

In postgresql.conf:

```conf
shared_preload_libraries='pg_stat_statements,auto_explain'
```

```conf
auto_explain.log_min_duration = 500ms
```

## Performance Investigation Checklist

1. Verify connection counts.
2. Check top expensive queries.
3. Review execution plans.
4. Analyze index usage.
5. Investigate waits and locks.
6. Search for N+1 patterns.
7. Compare before/after deployments.
8. Measure improvements.

## Conclusion

SQL Server: Query Store + Extended Events.

PostgreSQL: pg_stat_statements + EXPLAIN ANALYZE + auto_explain.

These are the primary native tools for diagnosing database bottlenecks in production environments.
