# Semaphore-based Access Control (SemaphoreSlim + Data Lock) in C#

## Basic SemaphoreSlim

```csharp
private static readonly SemaphoreSlim _semaphore = new(1, 1);
```

Meaning:

- Initial count = 1
- Maximum count = 1
- Only one thread can enter the protected section at a time

---

## Simple Example

```csharp
private static readonly SemaphoreSlim _semaphore = new(1, 1);
private static int _counter = 0;

public static async Task IncrementCounterAsync()
{
    await _semaphore.WaitAsync();

    try
    {
        _counter++;
        Console.WriteLine($"Counter = {_counter}");
    }
    finally
    {
        _semaphore.Release();
    }
}
```

## Protecting Shared Data

```csharp
private static readonly Dictionary<int, int> _idMappings = [];
```

## SemaphoreSlim + lock

```csharp
private static readonly SemaphoreSlim _semaphore = new(1, 1);
private static readonly object _dataLock = new();

private static readonly List<string> _records = [];

public static async Task AddRecordAsync(string value)
{
    await _semaphore.WaitAsync();

    try
    {
        lock (_dataLock)
        {
            _records.Add(value);
        }
    }
    finally
    {
        _semaphore.Release();
    }
}
```

## ID Generation Example

```csharp
private static int _nextGeneratedId = 1000;

public static async Task<int> GetNextIdAsync()
{
    await _semaphore.WaitAsync();

    try
    {
        return _nextGeneratedId++;
    }
    finally
    {
        _semaphore.Release();
    }
}
```

## SemaphoreSlim vs lock

### lock

```csharp
private static readonly object _lock = new();

lock (_lock)
{
    // critical section
}
```

### SemaphoreSlim

```csharp
await _semaphore.WaitAsync();

try
{
    // critical section
}
finally
{
    _semaphore.Release();
}
```

## Interlocked Alternative

```csharp
private static int _nextId = 1000;

public static int GetNextId()
{
    return Interlocked.Increment(ref _nextId);
}
```

## Rule of Thumb

- Use `lock` for synchronous in-memory operations.
- Use `SemaphoreSlim` for asynchronous operations using `await`.
- Use `ConcurrentDictionary` for shared dictionaries.
- Use `Interlocked` for counters and sequence generation.

For VB6/Access to SQL Server or PostgreSQL migrations, SemaphoreSlim is commonly used around DAO emulation layers, caches, migration batches, synchronization jobs, and connection throttling.
