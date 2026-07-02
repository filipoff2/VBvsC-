# DAO Emulation Layer Design

## Overview

In a migration scenario where a legacy VB6/.NET application was built around **DAO (Data Access Objects)** and **Microsoft Jet (.mdb)**, a complete rewrite of the data layer is often too risky and expensive. Instead, a **DAO emulation layer** acts as a compatibility bridge that allows existing code to continue using familiar DAO APIs while the actual data is stored in PostgreSQL or SQL Server.

---

## High-Level Architecture

```text
+------------------------------------------------+
| Legacy VB6 / .NET Application                  |
|                                                |
| OpenDatabase()                                 |
| OpenRecordset()                                |
| BeginTrans()                                   |
| MoveNext()                                     |
| Edit()/Update()                                |
+--------------------+---------------------------+
                     |
                     v
+------------------------------------------------+
| DAO Emulation Layer                            |
|                                                |
| Database                                       |
| Recordset                                      |
| QueryDef                                       |
| Workspace                                      |
| TableDef                                       |
| Field                                          |
+--------------------+---------------------------+
                     |
          +----------+-----------+
          |                      |
          v                      v
+----------------+     +----------------+
| PostgreSQL     |     | SQL Server     |
| Provider       |     | Provider       |
+----------------+     +----------------+
          |                      |
          +----------+-----------+
                     |
                     v
         ADO.NET / Npgsql / SqlClient
```

## Main Design Goals

### 1. Preserve Existing Code

Original DAO code:

```vb
Dim db As DAO.Database
Dim rs As DAO.Recordset

Set db = OpenDatabase("MyDb")
Set rs = db.OpenRecordset("SELECT * FROM Customer")

Do Until rs.EOF
    Debug.Print rs!Name
    rs.MoveNext
Loop
```

Should continue to work with minimal or no modification.

## Core Components

### Database

Responsibilities:

- Connection management
- SQL execution
- Transaction handling
- Schema metadata access

Example:

```csharp
public interface IDaoDatabase
{
    Recordset OpenRecordset(string sql);
    int Execute(string sql);
    void BeginTrans();
    void CommitTrans();
    void Rollback();
}
```

### Recordset

Simulates DAO features:

```vb
rs.MoveFirst
rs.MoveNext
rs.EOF
rs.Fields("Name")
rs.Edit
rs.Update
rs.AddNew
rs.Delete
```

Internally backed by:

```csharp
DbDataReader
```

or

```csharp
DataTable
```

depending on cursor type.

### QueryDef Emulation

Provides:

- Stored SQL statements
- Parameters
- Query execution abstraction

Mapped internally to:

```csharp
DbCommand
```

### Field Collection

Supports:

```vb
rs.Fields("CustomerName")
rs!CustomerName
```

through a field abstraction layer.

## Provider Abstraction

```csharp
interface ISqlDialect
{
    string ConvertSql(string jetSql);
    string QuoteIdentifier(string name);
}
```

Implementations:

- PostgresDialect
- SqlServerDialect

## Jet SQL Translation Layer

Example conversion:

Jet:

```sql
SELECT * FROM Customer
WHERE Name LIKE '*ABC*'
```

PostgreSQL / SQL Server:

```sql
SELECT * FROM Customer
WHERE Name LIKE '%ABC%'
```

Other translations include:

- Date literals
- Functions
- Wildcards
- Reserved words
- Identifier quoting

## Transaction Layer

DAO:

```vb
DBEngine.BeginTrans
DBEngine.CommitTrans
DBEngine.Rollback
```

Mapped to:

```csharp
DbTransaction
```

Special attention is required for:

- Nested transaction behavior
- Implicit transactions
- Locking expectations
- Isolation levels

## Collation Mapping

One of the most challenging areas.

The DAO emulation layer should normalize differences between:

- Microsoft Jet sorting rules
- SQL Server collations
- PostgreSQL ICU collations

Examples:

```sql
COLLATE "pl-x-icu"
```

```sql
COLLATE Latin1_General_CI_AI
```

## Locking and Concurrency

DAO compatibility may require emulation of:

- Pessimistic locking
- Optimistic locking
- Record version checks

Examples:

- SQL Server `ROWVERSION`
- PostgreSQL `xmin`

## Metadata Emulation

Expose DAO-style metadata:

```vb
db.TableDefs
db.Relations
db.Indexes
```

Using:

- INFORMATION_SCHEMA
- sys catalogs
- pg_catalog

## Performance Optimizations

Recommended optimizations:

- Prepared statement caching
- Connection pooling
- Query plan reuse
- Batch updates
- Bulk insert operations
- Recordset caching

## Recommended Internal Layering

```text
DAO API
   |
   v
Object Model Layer
(Database, Recordset)
   |
   v
Compatibility Layer
(SQL conversion)
   |
   v
Provider Layer
(PostgreSQL / SQL Server)
   |
   v
ADO.NET
   |
   v
Database
```

## Key Success Factors

1. Behavior compatibility over API compatibility.
2. Reliable Jet SQL to ANSI SQL translation.
3. Collation and sorting parity.
4. Transaction semantics preservation.
5. Recordset cursor emulation.
6. Efficient caching and connection management.
7. Provider-specific optimizations.
8. Extensive regression testing against MDB behavior.

The ultimate goal is to allow existing VB6/DAO applications to continue operating with minimal code changes while transparently using PostgreSQL or SQL Server as the underlying database engine.
