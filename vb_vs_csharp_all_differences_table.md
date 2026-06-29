# VB.NET vs C# — Syntax Differences (Table)

| Concept | C# Syntax | VB.NET Syntax |
|--------|----------|---------------|
| Block structure | Uses braces `{}` | Uses `End ...` keywords |
| Statement end | `;` required | No terminator |
| Variable declaration | `int x = 10;` | `Dim x As Integer = 10` |
| Type inference | `var x = 5;` | `Dim x = 5` |
| If statement | `if (x > 0)` | `If x > 0 Then` |
| Else if | `else if` | `ElseIf` |
| For loop | `for (int i=0;i<5;i++)` | `For i As Integer = 0 To 4` |
| While loop | `while (...)` | `While ... End While` |
| Increment | `x++` | `x += 1` |
| String concat | `"a" + b` | `"a" & b` |
| Lambda | `x => x * x` | `Function(x) x * x` |
| Static | `static` | `Shared` |
| Object creation | `new Class()` | `New Class()` |
| Constructor | `ClassName()` | `Sub New()` |
| Inheritance | `class A : B` | `Class A Inherits B` |
| Override | `override` | `Overrides` |
| Virtual | `virtual` | `Overridable` |
| Base call | `base.Method()` | `MyBase.Method()` |
| Method hiding | `new` | `Shadows` |
| Equality | `==` | `=` |
| Reference equality | `==` / `ReferenceEquals()` | `Is` |
| Boolean AND | `&&` | `AndAlso` |
| Boolean OR | `||` | `OrElse` |
| Exception handling | `try / catch / finally` | `Try / Catch / Finally` |
| Null value | `null` | `Nothing` |
| Arrays | `int[] arr = new int[3];` | `Dim arr(2) As Integer` |
| Property | `{ get; set; }` | `Property ... End Property` |
| Methods | `void Test()` | `Sub Test()` |
| Functions | `int Test()` | `Function Test() As Integer` |
| Parameter by ref | `ref` | `ByRef` |
| Parameter by value | default | `ByVal` |
| Optional params | `int x = 5` | `Optional x As Integer = 5` |
| Imports | `using` | `Imports` |
| Comments | `//` `/* */` | `'` |
| Event handler | `+=` | `AddHandler` |

---

## Notes

- VB.NET is not case-sensitive
- C# is case-sensitive
- VB uses more explicit keywords
- C# is more concise

