# Python Exception Handling

Exception handling lets a program respond to errors that occur while it runs.

## 1. Errors and Exceptions

A syntax error occurs when Python cannot parse the code. An exception occurs when an operation fails during execution, such as dividing by zero or opening a missing file.

```python
# Syntax error example (do not run as valid code):
# if True print("Hello")

# Runtime exception:
# print(10 / 0)  # ZeroDivisionError
```

## 2. try and except

Put code that may raise an exception inside `try`. Handle expected errors in `except`.

```python
try:
    number = int(input("Enter an integer: "))
    print(100 / number)
except ValueError:
    print("Please enter a valid integer.")
except ZeroDivisionError:
    print("The number must not be zero.")
```

Handle specific exceptions whenever possible.

## 3. Handling Multiple Exceptions

```python
try:
    values = [10, 20, 30]
    index = int(input("Enter an index: "))
    print(values[index])
except ValueError:
    print("Enter an integer index.")
except IndexError:
    print("That index is outside the list.")
```

## 4. else Clause

The `else` block runs only when the `try` block finishes without raising an exception.

```python
try:
    number = int("25")
except ValueError:
    print("Conversion failed.")
else:
    print("Converted successfully:", number)
```

## 5. finally Clause

The `finally` block runs whether an exception occurred or not. It is often used for cleanup.

```python
try:
    print("Working...")
except Exception as error:
    print("An error occurred:", error)
finally:
    print("Finished.")
```

For file handling, `with open(...)` is generally preferred because it manages file closing automatically.

## 6. Accessing an Exception Message

Use `as` to bind the exception object to a variable.

```python
try:
    result = 10 / 0
except ZeroDivisionError as error:
    print("Error:", error)
```

## 7. Raising an Exception

Use `raise` when your code needs to signal an invalid operation or input.

```python
def calculate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative.")
    return age

print(calculate_age(22))
```

The caller can catch the exception if it needs to handle the error.

## 8. Custom Exceptions

Create a custom exception by subclassing `Exception`.

```python
class InsufficientBalanceError(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientBalanceError("Insufficient balance.")
    return balance - amount

try:
    print(withdraw(500, 700))
except InsufficientBalanceError as error:
    print(error)
```

Custom exceptions make application-specific errors clearer.

## 9. Common Built-in Exceptions

- `ValueError`: a value has the wrong form for an operation, such as `int("abc")`.
- `TypeError`: an operation is used with an inappropriate type.
- `ZeroDivisionError`: division by zero.
- `IndexError`: a sequence index is out of range.
- `KeyError`: a dictionary key is missing.
- `FileNotFoundError`: a requested file does not exist.
- `AttributeError`: an object lacks the requested attribute.
- `NameError`: a name is not defined.

## 10. Avoid Bare except

Avoid catching every exception without a reason:

```python
# Avoid this pattern:
# try:
#     risky_operation()
# except:
#     pass
```

It can hide bugs and make debugging difficult. Catch expected exceptions and handle them meaningfully.

## 11. Common Mistakes

- Catching an exception that the code cannot raise.
- Putting too much unrelated code inside one `try` block.
- Using `except Exception` when a specific exception is more appropriate.
- Silently ignoring an error.
- Assuming `finally` means the program can never terminate unexpectedly.

## Practice Problems

1. Convert user input to an integer and handle invalid input.
2. Divide two numbers and handle division by zero.
3. Access a list index and handle `IndexError`.
4. Read a file and handle a missing file.
5. Write a function that raises `ValueError` for negative input.
6. Create a custom exception for an invalid account balance.
7. Use `try`, `except`, `else`, and `finally` in one small program.

## Key Takeaways

- `try` contains code that may fail.
- `except` handles matching exceptions.
- `else` runs when no exception occurs in `try`.
- `finally` runs during normal exception handling regardless of success or failure.
- `raise` signals an exception.
- Custom exceptions represent errors specific to your application.
