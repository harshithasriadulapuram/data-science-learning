# Pytest Parametrization and Test Cases

## 1. What Is Parametrized Testing?

Parametrized testing lets you run the same test function against multiple sets of inputs and expected outputs.

Instead of writing separate tests for every case, you define the test cases once and let pytest execute them individually.

Benefits include:

* Less duplicated code
* Better coverage of edge cases
* Easier maintenance
* Clearer failure reports

## 2. Your First Parametrized Test

Create `calculator.py`:

```python
def add(a, b):
    return a + b
```

Create `test_calculator.py`:

```python
import pytest
from calculator import add

@pytest.mark.parametrize(
    "a, b, expected",
    [
        (2, 3, 5),
        (0, 0, 0),
        (-2, 2, 0),
        (-3, -4, -7),
    ],
)
def test_add(a, b, expected):
    assert add(a, b) == expected
```

Pytest runs `test_add()` once for each tuple.

The three parameter names correspond to the three values in each test case.

## 3. Test Multiple Edge Cases

Consider a function that divides two numbers.

```python
# calculator.py

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

Tests:

```python
import pytest
from calculator import divide

@pytest.mark.parametrize(
    "a, b, expected",
    [
        (10, 2, 5),
        (9, 3, 3),
        (-8, 2, -4),
        (5, 2, 2.5),
    ],
)
def test_divide(a, b, expected):
    assert divide(a, b) == expected

@pytest.mark.parametrize("a", [0, 1, -5, 100])
def test_divide_by_zero(a):
    with pytest.raises(
        ValueError,
        match="Cannot divide by zero",
    ):
        divide(a, 0)
```

This covers normal inputs, negative numbers, floating-point results, and invalid divisors.

## 4. Give Test Cases Descriptive IDs

Descriptive IDs make test reports easier to understand.

```python
import pytest

@pytest.mark.parametrize(
    "value, expected",
    [
        (0, True),
        (2, True),
        (7, False),
        (-4, True),
    ],
    ids=[
        "zero-is-even",
        "positive-even",
        "positive-odd",
        "negative-even",
    ],
)
def test_is_even(value, expected):
    assert value % 2 == 0 is expected
```

The assertion above is incorrect Python logic for comparing the Boolean result. Use this corrected test:

```python
def is_even(value):
    return value % 2 == 0

@pytest.mark.parametrize(
    "value, expected",
    [
        (0, True),
        (2, True),
        (7, False),
        (-4, True),
    ],
    ids=[
        "zero-is-even",
        "positive-even",
        "positive-odd",
        "negative-even",
    ],
)
def test_is_even(value, expected):
    assert is_even(value) is expected
```

Place the function and test in the appropriate application and test files when implementing this example.

## 5. Parametrize Exception Cases

You can test different invalid inputs with one function.

```python
import pytest

def validate_age(age):
    if not isinstance(age, int) or isinstance(age, bool):
        raise TypeError("Age must be an integer")

    if age < 0:
        raise ValueError("Age cannot be negative")

    return True

@pytest.mark.parametrize(
    "age, exception_type",
    [
        (-1, ValueError),
        (-20, ValueError),
        ("18", TypeError),
        (None, TypeError),
        (True, TypeError),
    ],
)
def test_invalid_age(age, exception_type):
    with pytest.raises(exception_type):
        validate_age(age)
```

Different cases can expect different exception types.

## 6. Parametrize Database Tests

Suppose you have a function that inserts a user's name into a database.

```python
# user_service.py

def normalize_name(name):
    return name.strip().title()
```

Tests:

```python
import pytest
from user_service import normalize_name

@pytest.mark.parametrize(
    "raw_name, expected",
    [
        ("harshitha", "Harshitha"),
        ("  ravi  ", "Ravi"),
        ("ANU", "Anu"),
        ("sai kumar", "Sai Kumar"),
    ],
)
def test_normalize_name(raw_name, expected):
    assert normalize_name(raw_name) == expected
```

This example tests input normalization without requiring a database. When testing actual inserts, use a temporary test database and parametrize the input records as needed.

## 7. Combine Parametrization with Fixtures

Fixtures provide shared setup, while parametrization supplies multiple test inputs.

```python
import pytest

@pytest.fixture
def allowed_roles():
    return {"admin", "editor", "viewer"}

@pytest.mark.parametrize(
    "role, expected",
    [
        ("admin", True),
        ("editor", True),
        ("viewer", True),
        ("unknown", False),
    ],
)
def test_role_is_allowed(allowed_roles, role, expected):
    assert (role in allowed_roles) is expected
```

Pytest supplies the fixture and each parameter set to the test.

## 8. Run Specific Parameterized Tests

Run all tests:

```bash
python -m pytest -v
```

Run a particular file:

```bash
python -m pytest test_calculator.py -v
```

Run tests matching a name:

```bash
python -m pytest -k "divide" -v
```

Run a specific parametrized case by its ID:

```bash
python -m pytest -k "positive-odd" -v
```

The `-k` option selects tests by matching their names and IDs.

## 9. Common Mistakes

* Providing a different number of values than parameter names.
* Repeating identical test cases unnecessarily.
* Forgetting important boundary cases.
* Using unclear test IDs.
* Sharing mutable test data between cases unintentionally.
* Parametrizing inputs but failing to verify meaningful behavior.
* Expecting mocks to validate real database constraints.

## 10. Interview Questions

1. What is parametrized testing?
2. How does `pytest.mark.parametrize` work?
3. What is the purpose of test IDs?
4. Can parameterized cases expect different exceptions?
5. How does parametrization work with fixtures?
6. What is the difference between a fixture and a parameter?
7. How can you run only one parameterized test case?
8. When should you use separate test functions instead?

## 11. Practice Tasks

1. Write parametrized tests for addition and subtraction.
2. Test a palindrome function with positive and negative examples.
3. Test invalid inputs with different expected exception types.
4. Add descriptive IDs to at least five test cases.
5. Parametrize database records using a temporary SQLite database.
6. Run the suite with `pytest -v` and inspect the individual cases.
7. Add boundary cases for empty strings, zero, negative numbers, and missing values.
