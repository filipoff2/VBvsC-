# VB.NET vs C# — Lambda, Static, New, Override Syntax

---

## Lambda Expressions

### C#
```csharp
Func<int, int> square = x => x * x;
var sum = (int a, int b) => a + b;
```

### VB.NET
```vb
Dim square As Func(Of Integer, Integer) = Function(x) x * x
Dim sum = Function(a As Integer, b As Integer) a + b
```

---

## Static Members

### C#
```csharp
class MathUtil
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}
```

### VB.NET
```vb
Class MathUtil
    Public Shared Function Add(a As Integer, b As Integer) As Integer
        Return a + b
    End Function
End Class
```

---

## Object Creation (new)

### C#
```csharp
var p1 = new Person();
var p2 = new Person("John");
```

### VB.NET
```vb
Dim p1 = New Person()
Dim p2 = New Person("John")
```

---

## Constructors

### C#
```csharp
class Person
{
    public string Name;

    public Person(string name)
    {
        Name = name;
    }
}
```

### VB.NET
```vb
Class Person
    Public Name As String

    Public Sub New(name As String)
        Me.Name = name
    End Sub
End Class
```

---

## Override Methods

### C#
```csharp
class Base
{
    public virtual void Speak()
    {
    }
}

class Child : Base
{
    public override void Speak()
    {
    }
}
```

### VB.NET
```vb
Class Base
    Public Overridable Sub Speak()
    End Sub
End Class

Class Child
    Inherits Base

    Public Overrides Sub Speak()
    End Sub
End Class
```

---

## Base Call

### C#
```csharp
class Child : Base
{
    public override void Speak()
    {
        base.Speak();
    }
}
```

### VB.NET
```vb
Class Child
    Inherits Base

    Public Overrides Sub Speak()
        MyBase.Speak()
    End Sub
End Class
```

---

## Method Hiding (new / Shadows)

### C#
```csharp
class Base
{
    public void Show() { }
}

class Child : Base
{
    public new void Show() { }
}
```

### VB.NET
```vb
Class Base
    Public Sub Show()
    End Sub
End Class

Class Child
    Inherits Base

    Public Shadows Sub Show()
    End Sub
End Class
```

---

## Static Class / Module

### C#
```csharp
static class Utils
{
    public static int X = 10;
}
```

### VB.NET
```vb
Module Utils
    Public X As Integer = 10
End Module
```

---

## Keyword Mapping

- Lambda: C# `=>` | VB `Function(...)`
- Static: C# `static` | VB `Shared`
- Object creation: C# `new` | VB `New`
- Override: C# `override` | VB `Overrides`
- Virtual: C# `virtual` | VB `Overridable`
- Base: C# `base` | VB `MyBase`
- Method hiding: C# `new` | VB `Shadows`

---
