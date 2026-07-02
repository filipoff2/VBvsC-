# PostgreSQL vs SQL Server Index Types

| Index Type | SQL Server | PostgreSQL | Description |
|------------|------------|------------|------------|
| **Clustered Index** | Yes | No | Stores table rows physically in index key order. Only one per table because data can only be ordered one way. |
| **Nonclustered Index** | Yes | Similar concept | Separate structure containing indexed values and pointers to table rows. Used to speed up lookups without changing table storage. |
| **B-Tree** | Default | Default | Best general-purpose index for equality, range searches, sorting, and joins. Used for most OLTP workloads. |
| **Hash** | Limited use | Native | Optimized for exact-match (=) searches. Not useful for ranges or sorting. |
| **GIN** | No direct equivalent | Yes | Designed for indexing JSONB, arrays, and full-text search. Excellent when one row contains many searchable values. |
| **GiST** | Spatial equivalent exists | Yes | Flexible index framework commonly used for geospatial, nearest-neighbor, and advanced data types. |
| **SP-GiST** | No direct equivalent | Yes | Optimized for hierarchical and partitioned data structures such as IP addresses or geographic trees. |
| **BRIN** | No direct equivalent | Yes | Very small index storing metadata about page ranges. Ideal for huge tables with sequential data such as timestamps. |
| **Columnstore** | Yes | No native equivalent | Column-oriented storage optimized for analytics, reporting, and large aggregations. |
| **Filtered / Partial** | Filtered | Partial | Indexes only a subset of rows, reducing index size and improving performance for selective queries. |
| **Unique Index** | Yes | Yes | Enforces uniqueness of indexed values and prevents duplicate entries. |
| **Expression Index** | Via computed columns | Native | Indexes the result of an expression or function, e.g. LOWER(email). |

## Common Usage

| Scenario | Recommended Index |
|----------|------------------|
| Primary Key | B-Tree |
| Foreign Key | B-Tree |
| JOIN columns | B-Tree |
| WHERE Id = ? | B-Tree |
| WHERE Date BETWEEN | B-Tree |
| JSONB search | GIN |
| Full Text Search | GIN |
| GPS / Geography | GiST |
| Billions of log records | BRIN |
| Analytics / Data Warehouse (SQL Server) | Columnstore |

## Senior Interview Summary

In SQL Server, the most important distinction is Clustered vs Nonclustered indexes. In PostgreSQL, all indexes are effectively separate from table storage, with B-Tree being the default type. PostgreSQL additionally offers specialized indexes such as GIN for JSON and full-text search, GiST for spatial data, and BRIN for very large time-series tables.
