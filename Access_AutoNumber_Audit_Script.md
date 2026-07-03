# Find All AutoNumber Fields in an Access MDB Database

## Purpose

This VBA script scans all user tables in an Access MDB database and identifies every AutoNumber column.

---

## Print Results to Immediate Window

```vb
Sub FindAllAutoNumberFields()

    Dim db As DAO.Database
    Dim tdf As DAO.TableDef
    Dim fld As DAO.Field

    Set db = CurrentDb()

    Debug.Print String(80, "-")
    Debug.Print "TABLE", vbTab, "COLUMN", vbTab, "AUTONUMBER"
    Debug.Print String(80, "-")

    For Each tdf In db.TableDefs

        ' Skip system tables
        If Left$(tdf.Name, 4) <> "MSys" Then

            For Each fld In tdf.Fields

                If (fld.Attributes And dbAutoIncrField) <> 0 Then
                    Debug.Print tdf.Name, vbTab, fld.Name, vbTab, "YES"
                End If

            Next fld

        End If

    Next tdf

End Sub
```

### Sample Output

```text
TABLE             COLUMN              AUTONUMBER
------------------------------------------------
Customers         CustomerID          YES
Orders            OrderID             YES
Invoices          InvoiceID           YES
```

---

## Save Results into an Access Table

```vb
Sub FindAllAutoNumberFieldsToTable()

    Dim db As DAO.Database
    Dim tdf As DAO.TableDef
    Dim fld As DAO.Field

    Set db = CurrentDb()

    On Error Resume Next
    db.Execute "DROP TABLE AutoNumberAudit"
    On Error GoTo 0

    db.Execute _
        "CREATE TABLE AutoNumberAudit (" & _
        "TableName TEXT(255), " & _
        "ColumnName TEXT(255))"

    For Each tdf In db.TableDefs

        If Left$(tdf.Name, 4) <> "MSys" Then

            For Each fld In tdf.Fields

                If (fld.Attributes And dbAutoIncrField) <> 0 Then

                    db.Execute _
                        "INSERT INTO AutoNumberAudit(TableName, ColumnName) " & _
                        "VALUES('" & Replace(tdf.Name, "'", "''") & "'," & _
                        "'" & Replace(fld.Name, "'", "''") & "')"

                End If

            Next fld

        End If

    Next tdf

    MsgBox "Audit completed."

End Sub
```

---

## Migration Checklist

For every AutoNumber field identified:

- Verify mapping to SQL Server IDENTITY
- Check current maximum value
- Validate foreign key relationships
- Search for MAX(ID)+1 patterns in legacy code
- Verify DAO Recordset.AddNew behavior
- Test concurrent inserts
- Validate identity reseeding after migration

---

## Typical Output Inventory

```text
TableName      ColumnName
-------------------------
Customers      CustomerID
Orders         OrderID
Invoices       InvoiceID
```
