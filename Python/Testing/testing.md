# Python Testing: unittest and pytest

## 1. What is software testing?
Software testing checks whether code behaves as expected. Tests help catch bugs, protect existing functionality, and make code safer to change.

## 2. What is a unit test?
A unit test checks a small unit of code, usually a function or method, in isolation from unrelated components.

```python
def add(a, b):
    return a + b

assert add(2, 3) == 5
assert add(-1, 1) == 0
```

An `assert` raises `AssertionError` when its condition is false. For a test suite, prefer a testing framework.

## 3. Python's built-in unittest
`unittest` is included in the Python standard library.

```python
import unittest


def add(a, b):
    return a + b


class TestAdd(unittest.TestCase):
    def test_positive_numbers(self):
        self.assertEqual(add(2, 3), 5)

    def test_negative_and_positive(self):
        self.assertEqual(add(-1, 1), 0)


if __name__ == "__main__":
    unittest.main()
```

Save this as `test_add.py` and run:
```bash
python test_add.py
```

## 4. Common unittest assertions
- `self.assertEqual(a, b)`: checks equality.
- `self.assertNotEqual(a, b)`: checks inequality.
- `self.assertTrue(condition)`: checks that a condition is true.
- `self.assertFalse(condition)`: checks that a condition is false.
- `self.assertIsNone(value)`: checks for `None`.
- `self.assertIn(item, collection)`: checks membership.
- `self.assertRaises(ErrorType)`: checks that an exception is raised.

Example:
```python
with self.assertRaises(ZeroDivisionError):
    10 / 0
```

This example belongs inside a `unittest.TestCase` method.

## 5. What is pytest?
`pytest` is a popular third-party Python testing framework. It supports ordinary test functions, detailed assertion reports, fixtures, and parameterized tests.

Install it:
```bash
python -m pip install pytest
```

## 6. Your first pytest test
Create `calculator.py`:
```python
def multiply(a, b):
    return a * b
```

Create `test_calculator.py` in the same directory:
```python
from calculator import multiply


def test_multiply_positive_numbers():
    assert multiply(3, 4) == 12


def test_multiply_by_zero():
    assert multiply(5, 0) == 0
```

Run the tests from the project directory:
```bash
python -m pytest
```

Pytest discovers files named `test_*.py` or `*_test.py` by default and functions named `test_*` in those files.

## 7. Testing exceptions with pytest
```python
import pytest


def divide(a, b):
    if b == 0:
        raise ValueError("Divisor must not be zero")
    return a / b


def test_divide_by_zero():
    with pytest.raises(ValueError, match="Divisor must not be zero"):
        divide(10, 0)
```

## 8. Fixtures
A fixture prepares data or resources for tests. Define it using `@pytest.fixture`.

```python
import pytest


@pytest.fixture
def sample_numbers():
    return [10, 20, 30]


def test_sum(sample_numbers):
    assert sum(sample_numbers) == 60
```

Pytest automatically supplies the fixture to the test that requests it by name.

## 9. Parameterized tests
Use `@pytest.mark.parametrize` to run one test against several inputs.

```python
import pytest


@pytest.mark.parametrize(
    "a, b, expected",
    [(2, 3, 5), (0, 0, 0), (-1, 1, 0)],
)
def test_add(a, b, expected):
    assert a + b == expected
```

## 10. Useful pytest commands
```bash
python -m pytest
python -m pytest -v
python -m pytest test_calculator.py
python -m pytest -k multiply
python -m pytest --tb=short
```

- `-v`: verbose test names and results.
- A filename: run tests from that file.
- `-k`: select tests by a name expression.
- `--tb=short`: use shorter tracebacks for failures.

## 11. Arrange, Act, Assert
A simple testing pattern is:
1. Arrange: prepare inputs and dependencies.
2. Act: call the function being tested.
3. Assert: verify the result.

```python
def test_add():
    # Arrange
    a, b = 2, 3

    # Act
    result = a + b

    # Assert
    assert result == 5
```

## 12. Testing in data science projects
Tests can check:
- Data-loading functions handle missing files correctly.
- Data-cleaning functions return expected columns and shapes.
- Feature transformations produce valid values.
- Model prediction functions return the expected format.
- API endpoints return appropriate responses.

Example:
```python
def clean_names(names):
    return [name.strip().title() for name in names]


def test_clean_names():
    assert clean_names([" anu ", "RAVI"]) == ["Anu", "Ravi"]
```

## 13. unittest vs pytest
| Feature | unittest | pytest |
|---|---|---|
| Installation | Built into Python | Install separately |
| Basic test style | TestCase classes and methods | Plain test functions or classes |
| Assertions | Methods such as `assertEqual` | Python `assert` statements |
| Fixtures | Setup and teardown methods | Fixture system |
| Parameterization | Often implemented with loops or extensions | Built-in `parametrize` support |

Both frameworks are useful. Choose based on project needs and team conventions.

## 14. Best practices
- Test expected results and important edge cases.
- Keep tests independent and repeatable.
- Use descriptive test names.
- Avoid tests that depend on external services unless intentionally testing integration.
- Do not remove or weaken a test merely because it fails; investigate the cause.
- Run tests after changes and before committing important code.

## 15. Practice problems
1. Write tests for addition, subtraction, multiplication, and division.
2. Test how your function handles zero and negative values.
3. Write a test for a function that raises an exception on invalid input.
4. Use a pytest fixture to provide sample data.
5. Use parameterized tests for several input-output combinations.
6. Write tests for one data-cleaning function from a project.

## 16. Interview questions
1. What is unit testing?
2. What is the difference between unittest and pytest?
3. How does pytest discover tests?
4. What is a fixture?
5. How do you test whether a function raises an exception?
6. What is parameterization in testing?
7. What is the Arrange-Act-Assert pattern?
8. Why are automated tests useful in machine learning projects?
