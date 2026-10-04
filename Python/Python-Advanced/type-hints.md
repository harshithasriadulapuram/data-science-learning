# Python Type Hints and Annotations

## 1. What are type hints?
Type hints indicate the expected types of variables, function parameters, and return values. They improve readability and help tools detect potential mistakes. Python does not normally enforce these hints at runtime.

```python
def add(a: int, b: int) -> int:
    return a + b

print(add(10, 20))
```

Here, `a` and `b` are expected to be integers, and the function is expected to return an integer.

## 2. Variable annotations
```python
name: str = "Harshitha"
age: int = 22
score: float = 91.5
is_active: bool = True
```

These annotations communicate intended types; they do not automatically prevent assigning another type.

## 3. Function parameter and return types
```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

message = greet("Harshitha")
print(message)
```

Use `-> None` when a function is intended not to return a meaningful value.

```python
def display_message(message: str) -> None:
    print(message)
```

## 4. Type hints for collections
```python
names: list[str] = ["Anu", "Ravi"]
scores: dict[str, int] = {"Anu": 90, "Ravi": 85}
coordinates: tuple[float, float] = (17.4, 78.5)
unique_ids: set[int] = {1, 2, 3}
```

The built-in generic syntax shown here is supported in Python 3.9 and later.

## 5. Optional values
Use `str | None` when a value may be a string or `None` (Python 3.10+).

```python
def find_email(user_id: int) -> str | None:
    if user_id == 1:
        return "user@example.com"
    return None
```

`None` represents the absence of a value.

## 6. Union types
A union indicates that a value can have more than one type.

```python
def format_id(value: int | str) -> str:
    return str(value)
```

This function accepts either an integer or a string and returns a string.

## 7. Type aliases
A type alias gives a reusable name to a type expression.

```python
UserId = int


def get_user(user_id: UserId) -> str:
    return f"User {user_id}"
```

Aliases are useful when a type expression is long or used repeatedly.

## 8. Any versus object
`Any` allows a value to be treated as any type by a static type checker. `object` represents any Python object, but code generally must check its type before using type-specific operations.

```python
from typing import Any

anything: Any = 10
something: object = "hello"
```

Prefer specific types when possible because they make errors easier to detect.

## 9. Type hints with collections and iterables
```python
from collections.abc import Iterable

def total(values: Iterable[int]) -> int:
    return sum(values)

print(total([1, 2, 3]))
```

`Iterable[int]` indicates an iterable that yields integers. This accepts more than just lists, such as tuples and generators.

## 10. Type checking
Python does not automatically validate type hints when a function is called. Static analysis tools such as mypy and Pyright can inspect code for possible type errors.

```python
def multiply(a: int, b: int) -> int:
    return a * b

# A static type checker can flag this call:
# multiply("2", 3)
```

The comment prevents the example from being executed as part of the code.

## 11. Important points
- Type hints document intended types and improve editor support.
- They do not normally enforce types at runtime.
- `->` specifies the expected return type.
- `list[str]` describes a list of strings.
- `str | None` allows a string or None.
- Static type checkers can detect many type-related mistakes before execution.

## 12. Practice problems
1. Write a function that accepts two floats and returns a float.
2. Annotate a function that accepts a name and returns a greeting.
3. Create a function that accepts a list of integers and returns their sum.
4. Write a function that returns a string or None.
5. Add type hints to three functions from an earlier Python exercise.

## 13. Interview questions
1. What are type hints in Python?
2. Do type hints enforce types at runtime?
3. What does the arrow `->` mean in a function definition?
4. What is the difference between `Any` and `object`?
5. What is a union type?
6. What tools can check Python type hints?
