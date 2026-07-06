# SQL Server Bottleneck Diagnosis Tutorial Using First Responder Kit

## Goal

Learn how to use:

- sp_Blitz
- sp_BlitzFirst
- sp_BlitzCache
- sp_BlitzIndex
- sp_BlitzWho

to identify real SQL Server performance bottlenecks.

---

# 1. Install First Responder Kit

Download:

https://www.brentozar.com/first-aid/

Open:

Install-All-Scripts.sql

in SSMS and execute it.

The installation creates procedures such as:

- sp_Blitz
- sp_BlitzFirst
- sp_BlitzCache
- sp_BlitzIndex
- sp_BlitzWho

---

# 2. Diagnostic Workflow

Always work in this order:

1. sp_Blitz
2. sp_BlitzFirst
3. sp_BlitzWho
4. sp_BlitzCache
5. sp_BlitzIndex
6. Query Store

This moves from server-level problems to query-level problems.

---

# 3. Step 1 - Server Health Check

Run:

```sql
EXEC sp_Blitz;
```

Look for:

- Priority 1-50 findings
- Corruption warnings
- Missing backups
- Memory settings
- TempDB warnings
- Security issues

Questions answered:

- Is SQL Server configured correctly?
- Are there obvious dangerous settings?
- Is the server healthy?

Example findings:

- Auto Close enabled
- Auto Shrink enabled
- Max memory not configured
- Missing backups

Fix these before deeper analysis.

---

# 4. Step 2 - Real-Time Bottleneck Analysis

Run:

```sql
EXEC sp_BlitzFirst;
```

This is usually the most useful command.

It shows:

- CPU pressure
- Memory pressure
- Wait statistics
- Blocking
- I/O problems
- Network waits

Common waits:

## Blocking

```text
LCK_M_X
LCK_M_S
```

Meaning:

Transactions are waiting on locks.

Check:

```sql
EXEC sp_BlitzWho;
```

---

## Slow Storage

```text
PAGEIOLATCH_SH
PAGEIOLATCH_EX
```

Meaning:

Reads from disk are slow.

Possible causes:

- Slow SSD
- Very large table scans
- Missing indexes

---

## Transaction Log Bottleneck

```text
WRITELOG
```

Meaning:

SQL Server is waiting for transaction log writes.

Check:

- Log file location
- Log autogrowth
- Large transactions

---

## Parallelism Issues

```text
CXPACKET
CXCONSUMER
```

Meaning:

Queries are using parallel plans.

Investigate:

- MaxDOP
- Expensive queries

---

# 5. Step 3 - Find Active Sessions

Run:

```sql
EXEC sp_BlitzWho;
```

Shows:

- Running queries
- Blocking chains
- Open transactions
- Wait types
- CPU usage

Look for:

- Long-running sessions
- Blocked sessions
- Sleeping sessions with open transactions

Example:

```text
Session 75 blocking 12 other sessions
```

Usually that is a bottleneck.

---

# 6. Step 4 - Find Expensive Queries

Run:

```sql
EXEC sp_BlitzCache;
```

This identifies:

- Highest CPU queries
- Longest duration queries
- Highest reads
- Highest writes

Start with:

```sql
EXEC sp_BlitzCache
    @SortOrder = 'cpu';
```

Then:

```sql
EXEC sp_BlitzCache
    @SortOrder = 'reads';
```

Then:

```sql
EXEC sp_BlitzCache
    @SortOrder = 'duration';
```

What to look for:

- Table scans
- Missing indexes
- Key lookups
- High-cost joins
- Parameter sniffing

Typical symptom:

```text
One query consuming 80% of CPU
```

That query becomes your first tuning candidate.

---

# 7. Step 5 - Index Health

Run:

```sql
EXEC sp_BlitzIndex;
```

Look for:

- Missing indexes
- Duplicate indexes
- Unused indexes
- Large indexes

Typical bottleneck:

```text
Large table
+
No index on WHERE clause
=
Table Scan
```

sp_BlitzIndex highlights these quickly.

---

# 8. Combine with Query Store

Enable:

```sql
ALTER DATABASE YourDatabase
SET QUERY_STORE = ON;
```

Useful reports:

- Top Resource Consuming Queries
- Highest CPU Queries
- Regressed Queries

Query Store gives historical evidence.

sp_Blitz tools provide investigation details.

Together they are extremely powerful.

---

# Example Investigation

Users complain:

```text
Application is slow
```

Step 1:

```sql
EXEC sp_BlitzFirst;
```

Result:

```text
PAGEIOLATCH_SH
```

Storage or large scans are suspected.

Step 2:

```sql
EXEC sp_BlitzCache;
```

Result:

```text
Query on Customer table doing 20 million logical reads
```

Step 3:

```sql
EXEC sp_BlitzIndex;
```

Result:

```text
Missing index on CustomerId
```

Step 4:

Create index.

Result:

```text
Query drops from 45 seconds to 200 ms
```

Bottleneck resolved.

---

# Cheat Sheet

## Overall Health

```sql
EXEC sp_Blitz;
```

## Current Bottlenecks

```sql
EXEC sp_BlitzFirst;
```

## Active Queries

```sql
EXEC sp_BlitzWho;
```

## Expensive Queries

```sql
EXEC sp_BlitzCache;
```

## Index Problems

```sql
EXEC sp_BlitzIndex;
```

## Historical Analysis

```text
Query Store
```

---

# Recommended Order

```text
sp_Blitz
    ↓
sp_BlitzFirst
    ↓
sp_BlitzWho
    ↓
sp_BlitzCache
    ↓
sp_BlitzIndex
    ↓
Query Store
```

This workflow is used by many SQL Server performance specialists because it moves logically from server health to the root-cause query.
