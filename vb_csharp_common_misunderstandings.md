# VB.NET vs C# — Common Syntax Misunderstandings

---

## 1. Assignment vs Comparison

### C#
```csharp
if (x == 5)
{
}
```

### VB.NET
```vb
If x = 5 Then
End If
```

---

## 2. String Concatenation

### C#
```csharp
string s = "Hello " + name;
```

### VB.NET
```vb
Dim s = "Hello " & name
```

---

## 3. Case Sensitivity

### C#
```csharp
int Value = 10;
int value = 20;
```

### VB.NET
```vb
Dim Value As Integer = 10
Dim value As Integer = 20
```

---

## 4. Nothing vs null

### C#
```csharp
string s = null;
```

### VB.NET
```vb
Dim s As String = Nothing
```

---

## 5. Boolean Operators

### C#
```csharp
if (a && b) { }
if (a || b) { }
```

### VB.NET
```vb
If a AndAlso b Then
End If

If a OrElse b Then
End If
```

---

## 6. Increment / Decrement

### C#
```csharp
x++;
x--;
```

### VB.NET
```vb
x += 1
x -= 1
```

---

## 7. Method Call Parentheses

### C#
```csharp
Console.WriteLine("Hello");
```

### VB.NET
```vb
Console.WriteLine("Hello")
Call Console.WriteLine("Hello")
```

---

## 8. Default Property Access

### C#
```csharp
var value = dict["key"];
```

### VB.NET
```vb
Dim value = dict("key")
```

---

## 9. Equality (Reference vs Value)

### C#
```csharp
if (obj1 == obj2) { }
```

### VB.NET
```vb
If obj1 Is obj2 Then
End If
```

---

## 10. Static vs Instance

### C#
```csharp
class X
{
    public static int A;
}
```

### VB.NET
```vb
Class X
    Public Shared A As Integer
End Class
```

---

## 11. Event Handling

### C#
```csharp
button.Click += OnClick;
```

### VB.NET
```vb
AddHandler button.Click, AddressOf OnClick
```

---

## 12. Method Hiding vs Overriding

### C#
```csharp
public new void Show() { }
public override void Show() { }
```

### VB.NET
```vb
Public Shadows Sub Show()
End Sub

Public Overrides Sub Show()
End Sub
```

---

## 13. Optional Parameters

### C#
```csharp
void Test(int x = 5) { }
```

### VB.NET
```vb
Sub Test(Optional x As Integer = 5)
End Sub
```

---

## 14. ByRef vs ByVal

### C#
```csharp
void Test(ref int x) { }
```

### VB.NET
```vb
Sub Test(ByRef x As Integer)
End Sub
```

---

## 15. Arrays Length vs Upper Bound

### C#
```csharp
int[] arr = new int[3];
```

### VB.NET
```vb
Dim arr(2) As Integer
```

---
