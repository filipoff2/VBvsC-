# SQL Server Identity Usage Report

```sql
SELECT
    OBJECT_NAME(object_id) AS TableName,
    name AS ColumnName,
    CAST(last_value AS BIGINT) AS CurrentIdentity,
    2147483647 AS IntMax,
    2147483647 - CAST(last_value AS BIGINT) AS RemainingValues,
    CAST(
        CAST(last_value AS DECIMAL(20,2))
        / 2147483647 * 100
    AS DECIMAL(10,2)) AS PercentUsed
FROM sys.identity_columns
WHERE system_type_id = 56 -- INT
ORDER BY PercentUsed DESC;
```

## Columns

- **TableName** - table name.
- **ColumnName** - identity column name.
- **CurrentIdentity** - current identity value.
- **IntMax** - maximum INT value (2,147,483,647).
- **RemainingValues** - IDs remaining before overflow.
- **PercentUsed** - percentage of INT range already consumed.

## Purpose

Use this query to identify INT IDENTITY columns that are approaching overflow and may need migration to BIGINT.
