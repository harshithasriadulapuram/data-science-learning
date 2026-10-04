# Python Decorators

## 1. What is a decorator?
A decorator is a function that takes another function, adds or changes its behavior, and returns a function. It lets us extend behavior without modifying the original function's code directly.

## 2. Functions are objects
Python functions can be assigned to variables and passed as arguments.

```python
def greet():
    print("Hello!")

say_hello = greet
say_hello()
```

## 3. A function inside another function
```python
def outer():
    def inner():
        print("Inside inner function")
    inner()

outer()
```

## 4. Returning a function
```python
def outer():
    def inner():
        print("Hello from inner")
    return inner

result = outer()
result()
```

`outer()` returns the function itself. `result()` calls that returned function.

## 5. Your first decorator
```python
def my_decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")
    return wrapper

@my_decorator
def greet():
    print("Hello!")

greet()
```

Output:
```text
Before function call
Hello!
After function call
```

`@my_decorator` is equivalent to `greet = my_decorator(greet)`.

## 6. Decorators with arguments
Use `*args` and `**kwargs` so the wrapper can accept different positional and keyword arguments.

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Calling function")
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def add(a, b):
    return a + b

print(add(3, 4))  # 7
```

Returning the result preserves the wrapped function's return value.

## 7. Preserve function metadata with wraps
Use `functools.wraps` to preserve useful information such as the original function's name and docstring.

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def greet(name):
    """Greet a person by name."""
    return f"Hello, {name}!"

print(greet("Harshitha"))
print(greet.__name__)  # greet
```

## 8. A practical example: timing a function
```python
from functools import wraps
from time import perf_counter

def measure_time(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = perf_counter()
        try:
            return func(*args, **kwargs)
        finally:
            elapsed = perf_counter() - start
            print(f"Execution time: {elapsed:.6f} seconds")
    return wrapper

@measure_time
def calculate_sum(n):
    return sum(range(n))

print(calculate_sum(100_000))
```

The `finally` block reports elapsed time even if the wrapped function raises an exception. The exception still propagates normally.

## 9. Common uses
- Logging function calls
- Measuring execution time
- Authentication and authorization checks
- Caching results
- Input validation
- Framework features such as route registration

## 10. Important points
- A decorator receives a function and returns a replacement callable, commonly a wrapper.
- `@decorator` syntax applies a decorator when the function is defined.
- Use `*args` and `**kwargs` for general-purpose wrappers.
- Use `return func(...)` to preserve the function's result.
- Use `functools.wraps` to preserve metadata.
- Decorators can also be designed to accept their own configuration arguments, but that requires an additional outer function.

## 11. Practice problems
1. Create a decorator that prints `Starting` before a function and `Finished` after it.
2. Create a decorator that logs the function name whenever it is called.
3. Create a decorator that counts how many times a function is called.
4. Create a decorator that measures execution time.
5. Apply a decorator to a function that accepts two arguments and returns their product.

## 12. Interview questions
1. What is a decorator in Python?
2. What does the `@` syntax do?
3. Why do decorators commonly use an inner `wrapper` function?
4. Why are `*args` and `**kwargs` useful in decorators?
5. What is the purpose of `functools.wraps`?
6. Name three practical uses of decorators.
