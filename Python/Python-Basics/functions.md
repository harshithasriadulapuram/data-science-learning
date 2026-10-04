# Python Functions

A function is a reusable block of code that performs a specific task. Functions help reduce repetition, improve readability, and make programs easier to test.

## 1. Defining and Calling a Function

Use `def` to define a function.

```python
def greet():
    print("Hello, Harshitha!")

greet()
```

Defining a function does not execute its body. Calling it does.

## 2. Parameters and Arguments

Parameters are names in the function definition. Arguments are the values passed when calling the function.

```python
def greet(name):
    print(f"Hello, {name}!")

greet("Harshitha")
```

Here, `name` is a parameter and `"Harshitha"` is an argument.

## 3. Return Values

`return` sends a result back to the caller. It also ends the current function call.

```python
def add(a, b):
    return a + b

result = add(10, 20)
print(result)  # 30
```

`print()` displays a value; `return` makes a value available to the calling code.

## 4. Default Parameters

A default value is used when an argument is omitted.

```python
def greet(name="Guest"):
    print(f"Hello, {name}!")

greet()
greet("Harshitha")
```

Parameters with defaults must come after parameters without defaults in a normal function definition.

## 5. Positional and Keyword Arguments

```python
def introduce(name, age):
    print(f"My name is {name} and I am {age}.")

introduce("Harshitha", 22)  # Positional arguments
introduce(age=22, name="Harshitha")  # Keyword arguments
```

Keyword arguments make calls easier to read.

## 6. Returning Multiple Values

```python
def calculate(a, b):
    return a + b, a - b

addition, subtraction = calculate(10, 4)
print(addition)     # 14
print(subtraction)  # 6
```

Python returns these values together as a tuple.

## 7. Variable Scope

Local variables are created inside a function and are normally accessible only there. Global variables are defined outside functions.

```python
message = "Hello"

def show_message():
    local_message = "Welcome"
    print(message)
    print(local_message)

show_message()
```

Prefer passing values as parameters and returning results rather than changing global variables unnecessarily.

## 8. *args and **kwargs

`*args` collects extra positional arguments into a tuple. `**kwargs` collects extra keyword arguments into a dictionary.

```python
def add_all(*args):
    return sum(args)

print(add_all(1, 2, 3, 4))  # 10
```

```python
def show_details(**kwargs):
    print(kwargs)

show_details(name="Harshitha", role="Student")
```

## 9. Lambda Functions

A lambda is a small anonymous function containing one expression.

```python
square = lambda number: number * number
print(square(5))  # 25
```

Use `def` for more complex functions that need multiple statements or clearer documentation.

## 10. Docstrings

A docstring explains what a function does.

```python
def square(number):
    """Return the square of a number."""
    return number * number
```

## 11. Type Hints

Type hints communicate the expected input and output types. Python does not automatically enforce them at runtime.

```python
def multiply(a: int, b: int) -> int:
    return a * b
```

## 12. Common Mistakes

- Forgetting parentheses when calling a function.
- Forgetting to pass a required argument.
- Using `print()` when the result needs to be returned.
- Forgetting that code after `return` in the same function call will not execute.
- Using inconsistent indentation.
- Returning a value but not storing or using it when needed.

## Practice Problems

1. Write a function to add two numbers.
2. Write a function that returns the largest of three numbers.
3. Write a function that checks whether a number is even.
4. Write a function to calculate factorial.
5. Write a function that counts vowels in a string.
6. Write a function that checks whether a string is a palindrome.
7. Write a function that returns the sum and average of a list of numbers.
8. Write a function that counts how many times each character occurs in a string.
9. Write a function that removes duplicates from a list while preserving order.
10. Write a function that checks whether a number is prime.

## Key Takeaways

- `def` defines a function.
- Arguments provide values to parameters.
- `return` sends results back to the caller.
- Default, positional, and keyword arguments support different calling styles.
- `*args` and `**kwargs` collect variable numbers of arguments.
- Local scope helps keep functions independent.
- Type hints and docstrings make functions easier to understand.
