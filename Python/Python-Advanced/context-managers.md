# Python Context Managers

## 1. What is a context manager?
A context manager manages setup and cleanup around a block of code. It is commonly used for files, locks, and temporary resources.

## 2. The `with` statement
The `with` statement enters a managed context and ensures its cleanup when the block exits normally or because of an exception.

```python
with open("example.txt", "w", encoding="utf-8") as file:
    file.write("Hello, Python!")
```

The file is closed automatically when the block ends.

## 3. Why use a context manager?
Without `with`, you must remember to close a file yourself:

```python
file = open("example.txt", "w", encoding="utf-8")
try:
    file.write("Hello")
finally:
    file.close()
```

Using `with` is shorter and handles cleanup reliably.

## 4. Reading a file safely
```python
with open("example.txt", "r", encoding="utf-8") as file:
    content = file.read()

print(content)
```

The variable `file` refers to the opened file inside the block. The file is closed after the block exits.

## 5. Multiple resources
You can manage multiple resources in one `with` statement:

```python
with open("input.txt", encoding="utf-8") as source, \
     open("output.txt", "w", encoding="utf-8") as destination:
    destination.write(source.read())
```

This copies the text from one file to another and manages both files' cleanup.

## 6. How a context manager works
A class-based context manager commonly implements two special methods:
- `__enter__()` runs when the context is entered. Its return value is assigned after `as`, if used.
- `__exit__(exc_type, exc_value, traceback)` runs when the context exits, including when an exception occurs.

## 7. Creating a custom context manager
```python
class SimpleContext:
    def __enter__(self):
        print("Entering context")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Exiting context")
        return False

with SimpleContext():
    print("Inside context")
```

Output:
```text
Entering context
Inside context
Exiting context
```

Returning `False` (or `None`) from `__exit__` means an exception is not suppressed. Returning `True` can suppress an exception, so do that only deliberately.

## 8. Using `contextlib`
Python provides `contextlib.contextmanager` for creating a context manager with a generator function.

```python
from contextlib import contextmanager

@contextmanager
def managed_resource():
    print("Setup")
    try:
        yield "resource is ready"
    finally:
        print("Cleanup")

with managed_resource() as resource:
    print(resource)
```

The code before `yield` runs on entry; the `finally` block runs during cleanup.

## 9. Common use cases
- Opening and closing files
- Acquiring and releasing locks
- Managing database transactions or connections
- Temporarily changing settings
- Managing other resources that need reliable cleanup

## 10. Important points
- Prefer `with` when an API provides a context manager.
- Cleanup occurs when the managed block exits, including on exceptions.
- `__enter__` provides the value bound by `as`.
- `__exit__` can handle or suppress exceptions; suppression should be intentional.
- `contextlib` provides helpers for writing context managers.

## 11. Practice problems
1. Write text to a file using `with` and read it back.
2. Copy content from one file to another using two context-managed files.
3. Create a class-based context manager that prints a message on entry and exit.
4. Use `contextlib.contextmanager` to print setup and cleanup messages.
5. Raise an exception inside a custom context manager and observe whether it propagates.

## 12. Interview questions
1. What is a context manager?
2. Why is `with` useful when working with files?
3. What are `__enter__` and `__exit__`?
4. What does returning `True` from `__exit__` mean?
5. What does `contextlib.contextmanager` do?
