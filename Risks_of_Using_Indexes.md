# Risks of Using Database Indexes

Indexes improve read performance, but they are not free. Many production performance problems come from having too many indexes rather than too few.

## Risks of Using Indexes

| Risk | Impact | Example |
|--------|---------|---------|
| Slower INSERTs | Every index must be updated when a row is inserted | Bulk import becomes slower |
| Slower UPDATEs | Updating indexed columns requires index maintenance | Changing CustomerId on millions of rows |
| Slower DELETEs | Index entries must also be removed | Large cleanup jobs take longer |
| Increased Storage | Indexes consume disk space | 100 GB table + 50 GB indexes |
| Fragmentation | Index structure becomes less efficient over time | Random inserts into B-Tree indexes |
| Longer Backups | More data to backup and restore | Database restore time increases |
| More Memory Usage | Frequently used indexes occupy cache memory | Less memory available for data pages |
| Bad Query Plans | Wrong index can cause optimizer mistakes | Index scan instead of seek |
| Index Maintenance Overhead | Rebuilds, VACUUM, REINDEX, or reorganizations required | DBA operational effort |

## Example: Insert Cost

Without indexes:

```sql
INSERT INTO Orders (...)
```

Database writes:

```text
Table
```

With five indexes:

```text
Table
+ Index 1
+ Index 2
+ Index 3
+ Index 4
+ Index 5
```

One business operation now requires multiple index updates.

## Too Many Indexes

Example:

```sql
IX_FirstName
IX_LastName
IX_Email
IX_City
IX_Country
IX_ZipCode
IX_Status
IX_Date
```

Symptoms:

- Slow writes
- Large database size
- Unused indexes
- Longer maintenance windows

## Index Fragmentation

Over time page splits can occur:

```text
Before:
1 2 3 4 5 6 7

After random inserts:
1 2 2.5 3 3.2 4 5
```

Effects:

- More I/O
- Slower scans
- Larger indexes

### SQL Server

Monitor with:

```sql
sys.dm_db_index_physical_stats
```

### PostgreSQL

Monitor with:

```sql
pg_stat_user_indexes
```

Maintenance operations:

```sql
VACUUM
REINDEX
```

## Wrong Index Selection

Example:

```sql
CREATE INDEX IX_Status
ON Orders(Status);
```

But query:

```sql
SELECT *
FROM Orders
WHERE CustomerId = 100;
```

The index may provide little or no benefit.

## Composite Index Order Mistakes

Index:

```sql
(CustomerId, OrderDate)
```

Good:

```sql
WHERE CustomerId = 10
```

Good:

```sql
WHERE CustomerId = 10
  AND OrderDate > '2025-01-01'
```

Often poor:

```sql
WHERE OrderDate > '2025-01-01'
```

Column order matters.

## SQL Server Specific Risk

Using random GUIDs as clustered keys can increase fragmentation and page splits.

Better alternatives:

```sql
INT IDENTITY
```

or sequential GUIDs.

## PostgreSQL Specific Risk

GIN indexes are powerful but can become large.

Example:

```sql
CREATE INDEX idx_json
ON orders USING GIN(metadata);
```

Pros:

- Fast JSON search
- Fast full-text search

Cons:

- Larger storage footprint
- Slower writes
- Increased maintenance

## Senior Engineer Takeaway

Create indexes based on real query patterns and always measure the impact.

The best index is not the one that makes a single query fastest. It is the one that improves overall system performance while balancing write overhead, storage costs, and maintenance effort.
