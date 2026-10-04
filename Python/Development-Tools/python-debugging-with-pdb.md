
# Python Debugging with pdb

## 1. What Is Debugging?

Debugging is the process of finding, understanding, and fixing errors in a program.

Common types of errors include:

- **Syntax errors:** The code violates Python's syntax rules.
- **Runtime errors:** An error occurs while the program is executing.
- **Logical errors:** The program runs but produces an incorrect result.

For example:

```python
def calculate_average(numbers):
    return sum(numbers) / len(numbers)


print(calculate_average([]))
```

This raises a `ZeroDivisionError` because the list is empty.

Debugging helps us identify where a problem occurs and why.

## 2. What Is `pdb`?

`pdb` is Python's built-in interactive debugger.

It allows you to pause program execution, inspect variables, execute individual statements, and step through code.

No separate package installation is required.

## 3. Start Debugging With `breakpoint()`

Python provides the built-in `breakpoint()` function.

```python
def calculate_total(prices):
    subtotal = sum(prices)

    breakpoint()

    tax = subtotal * 0.18
    total = subtotal + tax

    return total


print(calculate_total([100, 200, 300]))
```

When Python reaches `breakpoint()`, execution pauses and opens an interactive debugger, usually `pdb`.

You can inspect variables before the next calculation.

To continue, enter:

```text
c
```

To quit debugging, enter:

```text
q
```

## 4. Important `pdb` Commands

| Command | Purpose |
|---|---|
| `n` | Execute the next line without stepping into a called function |
| `s` | Execute the next line, stepping into a called function when applicable |
| `c` | Continue execution until another breakpoint or program termination |
| `p expression` | Evaluate and print an expression |
| `pp expression` | Pretty-print an expression |
| `l` | Show nearby source code |
| `ll` | Show the current function or code block |
| `w` | Show the current stack |
| `u` | Move up one stack frame |
| `d` | Move down one stack frame |
| `b` | List breakpoints when used without arguments |
| `q` | Quit the debugger |

Example debugger session:

```text
(Pdb) p subtotal
600
(Pdb) p subtotal * 0.18
108.0
(Pdb) n
(Pdb) p total
708.0
(Pdb) c
```

The exact displayed values and source-line positions depend on where execution is paused.

## 5. Inspect Variables to Find Logical Errors

Consider this function:

```python
def calculate_discount(price, discount_percentage):
    discount = price * discount_percentage
    return price - discount


print(calculate_discount(1000, 10))
```

The intended discount is 10%, but the code treats `10` as a multiplier, not a percentage.

Debug it:

```python
def calculate_discount(price, discount_percentage):
    breakpoint()

    discount = price * discount_percentage
    return price - discount
```

Run the program and inspect the values:

```text
(Pdb) p price
1000
(Pdb) p discount_percentage
10
```

The bug becomes clear: convert the percentage to a fraction.

Correct implementation:

```python
def calculate_discount(price, discount_percentage):
    if not 0 <= discount_percentage <= 100:
        raise ValueError("Discount must be between 0 and 100")

    discount = price * discount_percentage / 100
    return price - discount


print(calculate_discount(1000, 10))
```

Output:

```text
900.0
```

## 6. Set Breakpoints Using `pdb`

You can launch a Python script under the debugger without adding `breakpoint()` to the source code.

```bash
python -m pdb app.py
```

The debugger starts with the script loaded and lets you step through execution.

You can also insert a breakpoint directly:

```python
import pdb

def process_data(data):
    pdb.set_trace()

    cleaned_data = [value for value in data if value is not None]
    return cleaned_data
```

`breakpoint()` is generally more convenient because it respects Python's breakpoint configuration.

## 7. Debug Exceptions

Use a debugger to inspect the state of a program when an exception occurs.

For example:

```python
def divide(a, b):
    return a / b


print(divide(10, 0))
```

Run the script with:

```bash
python -m pdb app.py
```

Step through the function and inspect the arguments to understand why division fails.

For an ordinary exception traceback, Python already provides useful information about the error and the lines involved. Use a debugger when you need to inspect the program's state interactively.

## 8. Debugging With `pdb` and `pytest`

You can use the debugger while investigating a failing test.

Example test:

```python
def test_calculate_discount():
    from app import calculate_discount

    assert calculate_discount(1000, 10) == 900
```

Run tests with debugging support:

```bash
python -m pytest --pdb
```

When a test fails, pytest can enter the debugger at the point of failure.

To enter the debugger at the start of a test:

```bash
python -m pytest --trace
```

Use these options when you need interactive investigation. They are not normally required for every test run.

## 9. Debugging Best Practices

1. Reproduce the error consistently.
2. Read the full traceback before changing code.
3. Identify the smallest section that causes the problem.
4. Inspect inputs, intermediate variables, and return values.
5. Check assumptions about data types and empty values.
6. Write a test that reproduces the bug.
7. Fix the underlying cause rather than hiding the exception.
8. Remove temporary debugging statements when they are no longer needed.

Avoid leaving interactive breakpoints in production code because they can unexpectedly pause execution.

## 10. `print()` Debugging vs `pdb`

| `print()` debugging | `pdb` debugging |
|---|---|
| Displays explicitly selected values | Allows interactive inspection |
| Simple for quick checks | Useful for complex execution paths |
| Requires adding print statements | Can inspect variables while paused |
| Often requires editing code repeatedly | Supports stepping through execution |
| Can clutter logs | Can be removed by removing the breakpoint |

Both techniques can be useful. Choose the simplest tool that helps identify the cause.

## 11. Common Mistakes

### Mistake 1: Inspecting the wrong variable

Check the current stack frame and confirm that execution is paused where you expect.

### Mistake 2: Confusing `n` and `s`

- `n` advances to the next line in the current frame.
- `s` steps into a called function when possible.

### Mistake 3: Leaving breakpoints in the code

Remove temporary breakpoints before committing code that should run unattended.

### Mistake 4: Fixing symptoms instead of causes

Understand why the incorrect value was produced before changing the implementation.

### Mistake 5: Ignoring the traceback

The traceback often reveals the failing operation and the call path that led to it.

## 12. Interview Questions

**Q1. What is debugging?**

The process of locating, understanding, and fixing errors in software.

**Q2. What is `pdb`?**

Python's built-in interactive debugger.

**Q3. What does `breakpoint()` do?**

It invokes the configured debugger at that point in the program.

**Q4. What is the difference between `step` and `next`?**

`step` can enter a called function, while `next` executes the next line in the current stack frame.

**Q5. How can you debug a failing pytest test?**

Run pytest with `--pdb` to enter the debugger when a test fails.

**Q6. Why are breakpoints risky in production?**

They may pause execution and require interactive input, disrupting unattended applications.

## 13. Practice Tasks

- [ ] Add `breakpoint()` to a simple function.
- [ ] Inspect arguments and intermediate variables.
- [ ] Practice `n`, `s`, `p`, and `c`.
- [ ] Debug a function that calculates a percentage incorrectly.
- [ ] Start a script using `python -m pdb`.
- [ ] Debug a failing test with `pytest --pdb`.
- [ ] Explain the difference between runtime and logical errors.
- [ ] Remove temporary breakpoints after fixing the bug.

## Key Takeaways

- `pdb` is Python's built-in interactive debugger.
- `breakpoint()` pauses execution at a chosen location.
- Inspecting intermediate values helps uncover logical errors.
- Pytest supports interactive debugging of failing tests.
- Always remove temporary breakpoints when they are no longer needed.
