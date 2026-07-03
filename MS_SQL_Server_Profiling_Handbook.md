# Microsoft SQL Server Performance Profiling & Troubleshooting Handbook

## Contents
1. Architecture Overview
2. Query Store
3. Extended Events
4. Dynamic Management Views (DMVs)
5. Execution Plans
6. Wait Statistics
7. Locking & Blocking
8. Deadlocks
9. Index Tuning
10. Connection Pooling
11. Memory Diagnostics
12. CPU Diagnostics
13. TempDB Troubleshooting
14. N+1 Query Detection
15. Missing Index Analysis
16. Production Incident Workflow
17. Top Diagnostic Queries
18. Senior DBA Checklist

# 1. Architecture Overview

Core troubleshooting areas:

- CPU
- Memory
- Storage
- Network
- Query Plans
- Indexes
- Blocking
- Application Access Patterns

# 2. Query Store

Enable:

```sql
ALTER DATABASE MyDatabase
SET QUERY_STORE = ON;
```

Use Query Store to:

- Track regressions
- Compare plans
- Analyze deployments
- Find expensive queries
- Force good plans

# 3. Extended Events

Recommended replacements for SQL Profiler.

Capture:

- Deadlocks
- Long-running queries
- Timeouts
- Login failures
- Blocking chains

# 4. Important DMVs

## Active Requests

```sql
SELECT *
FROM sys.dm_exec_requests;
```

## Sessions

```sql
SELECT *
FROM sys.dm_exec_sessions;
```

## Connections

```sql
SELECT *
FROM sys.dm_exec_connections;
```

## Query Statistics

```sql
SELECT *
FROM sys.dm_exec_query_stats;
```

## Wait Statistics

```sql
SELECT *
FROM sys.dm_os_wait_stats;
```

# 5. Execution Plans

Always review:

- Index scans
- Table scans
- Key lookups
- Sorts
- Hash matches
- Parallelism operators

Warning signs:

- Estimated rows ≠ Actual rows
- Excessive logical reads
- Missing index recommendations

# 6. Wait Statistics

Most common waits:

## PAGEIOLATCH

Meaning:
- Storage latency
- Disk bottleneck

## WRITELOG

Meaning:
- Slow transaction log

## LCK_M_*

Meaning:
- Blocking
- Long transactions

## CXPACKET / CXCONSUMER

Meaning:
- Parallel query execution issues

# 7. Locking and Blocking

Find blockers:

```sql
EXEC sp_who2;
```

More advanced:

```sql
SELECT *
FROM sys.dm_exec_requests
WHERE blocking_session_id <> 0;
```

# 8. Deadlocks

Capture deadlocks using:

- system_health
- Extended Events

Typical causes:

- Inconsistent update order
- Long transactions
- Missing indexes

# 9. Index Tuning

## Clustered Index

Best for:

- Primary key access
- Range searches

## Nonclustered Index

Best for:

- Filtering
- Searching

## Filtered Index

Best for:

- Sparse datasets

## Columnstore Index

Best for:

- Reporting
- Analytics

# 10. Connection Pooling

Common problems:

- Connection leaks
- DbContext lifetime issues
- Long-running transactions

Check:

```sql
SELECT COUNT(*)
FROM sys.dm_exec_sessions;
```

# 11. Memory Diagnostics

Look for:

- Memory grants
- Sort spills
- Hash spills
- Buffer cache pressure

Useful DMVs:

```sql
SELECT *
FROM sys.dm_os_memory_clerks;
```

# 12. CPU Diagnostics

Find expensive queries:

```sql
SELECT TOP 20
       total_worker_time,
       execution_count
FROM sys.dm_exec_query_stats
ORDER BY total_worker_time DESC;
```

Causes:

- Missing indexes
- Scalar UDFs
- Poor cardinality estimates

# 13. TempDB Troubleshooting

Symptoms:

- Application slowdown
- PAGELATCH waits
- Large sorts

Best practices:

- Multiple data files
- Fast storage
- Monitor growth

# 14. N+1 Query Detection

Example:

```csharp
foreach(var customer in customers)
{
    db.Orders.Where(o => o.CustomerId == customer.Id);
}
```

Symptoms:

- Thousands of executions
- Small individual runtime
- Large cumulative runtime

Tools:

- Query Store
- MiniProfiler
- Extended Events

# 15. Missing Index Analysis

Find recommendations:

```sql
SELECT *
FROM sys.dm_db_missing_index_details;
```

Important:

Do not automatically create every suggested index.

Validate:

- Write impact
- Existing indexes
- Storage cost

# 16. Production Incident Workflow

Step 1
Check users affected.

Step 2
Check server resources.

Step 3
Review blocking.

Step 4
Review waits.

Step 5
Review top CPU queries.

Step 6
Review execution plans.

Step 7
Apply fix.

Step 8
Measure improvement.

# 17. Top Diagnostic Queries

## Active Requests

```sql
SELECT *
FROM sys.dm_exec_requests;
```

## Database File Usage

```sql
EXEC sp_spaceused;
```

## Open Transactions

```sql
DBCC OPENTRAN;
```

## Index Usage

```sql
SELECT *
FROM sys.dm_db_index_usage_stats;
```

# 18. Senior DBA Checklist

✅ Query Store enabled
✅ Extended Events configured
✅ Deadlock capture enabled
✅ Backups verified
✅ Index maintenance strategy
✅ Statistics maintenance strategy
✅ Blocking monitoring
✅ Wait statistics review
✅ Connection pool review
✅ Performance baseline captured
✅ TempDB reviewed
✅ Top SQL documented

## Quick Troubleshooting Matrix

| Symptom | Likely Cause |
|----------|-------------|
| High CPU | Missing index, poor plan |
| High IO | Table scans |
| Slow UI | N+1 queries |
| Timeouts | Blocking |
| Many connections | Pooling issue |
| Deadlocks | Transaction design |
| Memory pressure | Sort/hash spills |
| TempDB growth | Large sorts, temp tables |
