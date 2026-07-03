# Assessing Microsoft Access AutoNumber Fields During MDB → SQL Server Migration

## Purpose

When migrating a Microsoft Access (Jet Engine) application to SQL Server, one important assessment area is AutoNumber field compatibility with SQL Server IDENTITY columns.

## 1. Check AutoNumber in Access UI

Open the table in Design View.

Example:

```text
Field Name    Data Type
-----------   ----------
CustomerID    AutoNumber
```

Check Field Properties:

```text
New Values:
  Increment
  Random
```

- Increment = sequential values
- Random = random unique values

## 2. Check Using DAO

```vb
If (fld.Attributes And dbAutoIncrField) <> 0 Then
    Debug.Print fld.Name & " is AutoNumber"
End If
```

## 3. Find All AutoNumber Fields

```vb
Dim tdf As DAO.TableDef
Dim fld As DAO.Field

For Each tdf In CurrentDb.TableDefs

    If Left$(tdf.Name, 4) <> "MSys" Then

        For Each fld In tdf.Fields

            If (fld.Attributes And dbAutoIncrField) <> 0 Then
                Debug.Print tdf.Name & "." & fld.Name
            End If

        Next
    End If

Next
```

Example Output:

```text
Customer.CustomerID
Orders.OrderID
Invoice.InvoiceID
```

## 4. Check Existing Data

```sql
SELECT
    MIN(ID) AS MinID,
    MAX(ID) AS MaxID,
    COUNT(*) AS RowsCount
FROM Customer;
```

Investigate:

- Gaps in numbering
- Duplicate values
- Imported records
- Current maximum ID

## 5. Search for Risky Legacy Patterns

Look for:

```sql
SELECT MAX(ID)+1
```

or

```vb
NewID = rs!ID + 1
```

These assumptions often fail after migration to SQL Server.

## 6. DAO Emulation Layer Validation Checklist

- AutoNumber → IDENTITY mapping
- Recordset.AddNew behavior
- Retrieval of generated IDs
- Concurrent inserts
- Transaction rollback behavior
- Identity reseeding after import
- Foreign key integrity
- Performance of generated ID retrieval

## Why the Investigation May Take 2–3 Weeks

Typical activities:

- MDB schema analysis
- AutoNumber inventory
- DAO code review
- Emulation layer testing
- Edge-case investigation
- Fixes and regression testing

## Key Risk

The most common issue is not SQL Server IDENTITY itself, but legacy VB6/DAO code that assumes AutoNumber values are sequential or predictable.
