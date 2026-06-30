# VB.NET Access Database Advanced

## Connection String

```vb.net
Imports System.Data.OleDb

Dim connectionString As String =
    "Provider=Microsoft.ACE.OLEDB.12.0;Data Source=C:\Data\MyDb.accdb;"
```

## Using Parameterized Queries

```vb.net
Using con As New OleDbConnection(connectionString)

    Dim sql As String =
        "INSERT INTO Employees (FirstName, LastName, Salary) VALUES (?, ?, ?)"

    Using cmd As New OleDbCommand(sql, con)

        cmd.Parameters.AddWithValue("@p1", "John")
        cmd.Parameters.AddWithValue("@p2", "Smith")
        cmd.Parameters.AddWithValue("@p3", 50000)

        con.Open()
        cmd.ExecuteNonQuery()

    End Using
End Using
```

## Transactions

```vb.net
Using con As New OleDbConnection(connectionString)
    con.Open()
    Dim trans As OleDbTransaction = con.BeginTransaction()
    Try
        trans.Commit()
    Catch ex As Exception
        trans.Rollback()
    End Try
End Using
```

## DataAdapter and DataSet

```vb.net
Dim ds As New DataSet()
Using con As New OleDbConnection(connectionString)
    Dim da As New OleDbDataAdapter("SELECT * FROM Employees", con)
    da.Fill(ds, "Employees")
End Using
```

## Best Practices

- Use parameterized queries.
- Use Using blocks.
- Use transactions.
- Separate DAL from UI.
- Consider SQL Server for multi-user systems.
