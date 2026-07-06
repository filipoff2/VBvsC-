# Semaphore-based Access Control (SemaphoreSlim + Data Lock) in VB.NET

## Overview

In a legacy VB6/.NET migration project, Semaphore-based access control is often used to prevent multiple threads from accessing shared data simultaneously.

```vb
Private Shared ReadOnly _semaphore As New SemaphoreSlim(1, 1)
```

The `(1,1)` means:

- Maximum 1 thread inside the critical section
- Works similarly to a mutex/lock
- Other threads wait until the current thread exits

---

## Why Use It?

Imagine multiple users/processes updating an AutoNumber mapping cache:

```vb
Private Shared _idMappings As New Dictionary(Of Integer, Integer)
```

Without synchronization:

```vb
_idMappings(oldId) = newId
```

Two threads could:

1. Read simultaneously
2. Update simultaneously
3. Corrupt state
4. Cause race conditions

---

## Basic VB Example

### Shared Resource

```vb
Private Shared ReadOnly _semaphore As New SemaphoreSlim(1, 1)
Private Shared _counter As Integer
```

### Protected Access

```vb
Public Shared Async Function IncrementCounterAsync() As Task

    Await _semaphore.WaitAsync()

    Try
        _counter += 1
        Console.WriteLine($"Counter = {_counter}")
    Finally
        _semaphore.Release()
    End Try

End Function
```

---

## SemaphoreSlim + Data Lock Pattern

```vb
Private Shared ReadOnly _semaphore As New SemaphoreSlim(1, 1)
Private Shared ReadOnly _dataLock As New Object()

Private Shared _records As New List(Of String)
```

### Write Operation

```vb
Public Shared Async Function AddRecordAsync(value As String) As Task

    Await _semaphore.WaitAsync()

    Try
        SyncLock _dataLock
            _records.Add(value)
        End SyncLock
    Finally
        _semaphore.Release()
    End Try

End Function
```

---

## Why Both?

### SemaphoreSlim Controls

- Concurrent requests
- Async operations
- Database migrations
- Background workers

### SyncLock Protects

- In-memory collections
- Dictionaries
- Lists
- Shared state

```text
SemaphoreSlim
     ↓
One worker enters room

SyncLock
     ↓
Worker touches shared data safely
```

---

## Migration Example – ID Generation

```vb
Private Shared ReadOnly _semaphore As New SemaphoreSlim(1, 1)
Private Shared _nextGeneratedId As Integer = 1000
```

Unsafe:

```vb
Dim id = _nextGeneratedId
_nextGeneratedId += 1
```

Safe:

```vb
Public Shared Async Function GetNextIdAsync() As Task(Of Integer)

    Await _semaphore.WaitAsync()

    Try
        Dim id = _nextGeneratedId
        _nextGeneratedId += 1
        Return id
    Finally
        _semaphore.Release()
    End Try

End Function
```

Results:

```text
Thread A -> 1000
Thread B -> 1001
Thread C -> 1002
```

---

## SemaphoreSlim vs SyncLock

### SyncLock

```vb
SyncLock _lockObj
    ' critical section
End SyncLock
```

Pros:

- Very fast
- Simple

Cons:

- Cannot use Await
- Blocks thread

### SemaphoreSlim

```vb
Await _semaphore.WaitAsync()

Try
    ' work
Finally
    _semaphore.Release()
End Try
```

Pros:

- Async-friendly
- Does not block waiting threads
- Good for I/O operations

Cons:

- Slightly slower than SyncLock

---

## Typical .NET 10 Pattern

```vb
Private Shared ReadOnly _semaphore As New SemaphoreSlim(1, 1)

Public Shared Async Function UpdateCacheAsync() As Task

    Await _semaphore.WaitAsync()

    Try
        ' Access shared cache
    Finally
        _semaphore.Release()
    End Try

End Function
```

---

## Rule of Thumb

- Async method (`Await`) → use `SemaphoreSlim`
- Synchronous in-memory operation → use `SyncLock`
- DAO emulation layer, cache updates, ID generation during migration → `SemaphoreSlim(1,1)` is often the safest choice because many operations eventually involve asynchronous database access.
