# Microsoft Jet Engine Limitations

When migrating legacy VB6/DAO/MDB applications to PostgreSQL or SQL Server, understanding Jet limitations is critical.

## Summary Table

| Area | Limitation | Impact |
|--------|------------|---------|
| Database Size | MDB files have practical size limits | Large datasets eventually hit storage constraints |
| Concurrency | Designed for small workgroups | Locking conflicts and reduced performance |
| Architecture | File-based database engine | Queries often execute on client machines |
| Scalability | Limited scalability | Difficult to support enterprise workloads |
| Security | Weaker security model than modern DBMSs | Higher operational risk |
| Stored Procedures | No true stored procedure support | Business logic moves into applications |
| Query Optimization | Basic optimizer | Complex queries may perform poorly |
| High Availability | No enterprise-grade clustering/failover | Limited fault tolerance |
| Multi-User Access | Locking limitations | Blocking and contention issues |
| Modern Features | Missing advanced database capabilities | Requires migration for modern requirements |

## 1. File-Based Architecture

Jet operates directly on a shared MDB file.

```text
VB6 Client
      |
      +----> Shared MDB File
      |
VB6 Client
```

Unlike PostgreSQL or SQL Server, Jet is not a true client-server database.

## 2. Limited Concurrency

Common symptoms:

- Lock conflicts
- Database in-use errors
- Performance degradation under load
- Network-related contention

Although Jet 4 introduced record-level locking, it remains far less scalable than modern database engines.

## 3. Corruption Risk

Because multiple users access the same file directly, network interruptions, workstation crashes, or power failures can corrupt the MDB file.

## 4. Performance Limitations

Jet works well for:

- Small desktop applications
- Small business systems
- Low concurrent user counts

Jet struggles with:

- Millions of rows
- Large reports
- High transaction volumes
- Enterprise workloads

## 5. DAO Dependency

Many legacy applications depend on:

```vb
DAO.Database
DAO.Recordset
```

Migration often requires a DAO emulation layer to preserve Recordset behavior and navigation patterns.

## 6. Missing Enterprise Features

Compared with PostgreSQL and SQL Server, Jet lacks:

- Partitioning
- Advanced indexing
- Materialized views
- Parallel query execution
- Native JSON features
- Advanced monitoring
- Query history and tuning tools
- Built-in HA/Failover

## Interview Summary

Microsoft Jet is a file-based database engine optimized for desktop and small workgroup applications. Its major limitations include file-based architecture, limited scalability, locking and concurrency challenges, database corruption risk, DAO dependencies, and the absence of modern enterprise database features. These are common reasons organizations migrate MDB-based solutions to PostgreSQL or SQL Server.
