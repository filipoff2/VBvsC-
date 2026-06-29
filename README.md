# VB.NET vs C# — Syntax Only Cheat Sheet

---

## Blocks

### C#
```csharp
if (true)
{
    // code
}
```

### VB.NET
```vb
If True Then
    ' code
End If
```

---

## Statement Termination

### C#
```csharp
int x = 10;
string name = "John";
```

### VB.NET
```vb
Dim x As Integer = 10
Dim name As String = "John"
```

---

## Variables

### C#
```csharp
int x = 10;
string name = "John";
var y = 5;
```

### VB.NET
```vb
Dim x As Integer = 10
Dim name As String = "John"
Dim y = 5
```

---

## Conditional Statements

### C#
```csharp
if (x > 0)
{
    // code
}
else if (x < 0)
{
    // code
}
else
{
    // code
}
```

### VB.NET
```vb
If x > 0 Then
    ' code
ElseIf x < 0 Then
    ' code
Else
    ' code
End If
```

---

## For Loop

### C#
```csharp
for (int i = 0; i < 5; i++)
{
    // code
}
```

### VB.NET
```vb
For i As Integer = 0 To 4
    ' code
Next
```

---

## While Loop

### C#
```csharp
while (x < 10)
{
    x++;
}
```

### VB.NET
```vb
While x < 10
    x += 1
End While
```

---

## Methods / Functions

### C#
```csharp
int Add(int a, int b)
{
    return a + b;
}
```

### VB.NET
```vb
Function Add(a As Integer, b As Integer) As Integer
    Return a + b
End Function
```

---

## Classes

### C#
```csharp
class Person
{
    public string Name;

    public void Speak()
    {
        Console.WriteLine(Name);
    }
}
```

### VB.NET
```vb
Class Person
    Public Name As String

    Public Sub Speak()
        Console.WriteLine(Name)
    End Sub
End Class
```

---

## Properties

### C#
```csharp
public string Name { get; set; }
```

### VB.NET
```vb
Public Property Name As String
```

---

## Arrays

### C#
```csharp
int[] arr = { 1, 2, 3 };
```

### VB.NET
```vb
Dim arr() As Integer = {1, 2, 3}
```

---

## String Concatenation

### C#
```csharp
string s = "Hello " + name;
```

### VB.NET
```vb
Dim s = "Hello " & name
```

---

## Lambda Expressions

### C#
```csharp
Func<int, int> square = x => x * x;
```

### VB.NET
```vb
Dim square = Function(x As Integer) x * x
```

---

## Exception Handling

### C#
```csharp
try
{
    // code
}
catch (Exception ex)
{
    // code
}
finally
{
    // code
}
```

### VB.NET
```vb
Try
    ' code
Catch ex As Exception
    ' code
Finally
    ' code
End Try
```

---

## Imports / Using

### C#
```csharp
using System;
```

### VB.NET
```vb
Imports System
```

---

## Comments

### C#
```csharp
// single line
/* multi line */
```

### VB.NET
```vb
' single line
```

---
