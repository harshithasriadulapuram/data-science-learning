# Pytest Coverage and Coverage Reports

## 1. What Is Test Coverage?

Test coverage measures which parts of your source code execute when your tests run.

It helps identify code that your tests may not exercise.

Common coverage measurements include:

* **Statement coverage:** The percentage of executable statements that ran.
* **Branch coverage:** Whether both outcomes of conditional decisions were exercised.
* **Function coverage:** Which functions were called, when supported by the measurement tool.
* **Line coverage:** Which source lines were executed.

High coverage does not guarantee correct software. Tests must also check meaningful expected behavior.

## 2. Install Coverage Tools

Install pytest and pytest-cov:

```bash
python -m pip install pytest pytest-cov
```

`pytest-cov` integrates the coverage measurement tool with pytest.

## 3. Create a Sample Module

Create `calculator.py`:

```python
def add(a, b):
    return a + b


def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")

    return a / b


def classify_number(number):
    if number > 0:
        return "positive"

    if number < 0:
        return "negative"

    return "zero"
```

## 4. Write Tests

Create `test_calculator.py`:

```python
import pytest

from calculator import add, divide, classify_number


def test_add():
    assert add(2, 3) == 5


def test_divide():
    assert divide(10, 2) == 5


def test_divide_by_zero():
    with pytest.raises(ValueError):
        divide(10, 0)


@pytest.mark.parametrize(
    "number, expected",
    [
        (5, "positive"),
        (-3, "negative"),
        (0, "zero"),
    ],
)
def test_classify_number(number, expected):
    assert classify_number(number) == expected
```

These tests exercise the normal and exceptional paths of the sample functions.

## 5. Generate a Coverage Report

Run:

```bash
python -m pytest --cov=calculator --cov-report=term-missing
```

The report shows the statements executed, the coverage percentage, and source lines that were not executed.

The exact percentages depend on the code and tests in your project.

## 6. Understand the Report

Typical columns include:

| Column  | Meaning                        |
| ------- | ------------------------------ |
| Name    | Source file                    |
| Stmts   | Number of measured statements  |
| Miss    | Statements not executed        |
| Cover   | Statement coverage percentage  |
| Missing | Lines not covered by the tests |

For example, if 18 out of 20 measured statements execute, statement coverage is 90%.

## 7. Measure Branch Coverage

Statement coverage alone can miss important paths.

Consider:

```python
def is_adult(age):
    if age >= 18:
        return True

    return False
```

A test using `age=20` executes the true branch but does not test the false branch.

Use branch coverage:

```bash
python -m pytest --cov=calculator --cov-branch --cov-report=term-missing
```

For the `is_adult()` example, test both `age=20` and `age=16`.

Branch coverage helps reveal missing conditional outcomes.

## 8. Generate HTML Reports

Run:

```bash
python -m pytest --cov=calculator --cov-report=html
```

This creates an HTML report in the `htmlcov/` directory.

Open `htmlcov/index.html` in a browser to inspect file-level and line-level coverage.

You can also generate a machine-readable XML report:

```bash
python -m pytest --cov=calculator --cov-report=xml
```

XML reports are useful for CI systems and code-quality platforms.

## 9. Set a Minimum Coverage Threshold

You can make pytest fail if measured coverage falls below a target:

```bash
python -m pytest --cov=calculator --cov-fail-under=80
```

This command uses an 80% threshold as an example. Choose a threshold that fits your project rather than treating a specific percentage as universally correct.

## 10. Exclude Generated or Irrelevant Files

For larger projects, configure coverage in `pyproject.toml`:

```toml
[tool.coverage.run]
branch = true
source = ["app"]

[tool.coverage.report]
show_missing = true
skip_covered = true
omit = [
    "*/migrations/*",
]
```

Adjust the source directory and exclusions to match your repository. Avoid excluding application code merely to increase the reported percentage.

## 11. How to Improve Coverage Meaningfully

1. Identify uncovered lines and branches.
2. Add tests for normal behavior.
3. Test boundary values.
4. Test expected exceptions.
5. Verify rollback and cleanup behavior.
6. Test authorization and validation failures.
7. Include integration tests for important external dependencies.
8. Remove redundant tests that add no meaningful assertions.

Do not write tests solely to execute lines. Assert that the application behaves correctly.

## 12. Common Mistakes

* Treating 100% coverage as proof that software is bug-free.
* Measuring only statement coverage.
* Ignoring exception-handling paths.
* Testing only typical inputs.
* Excluding difficult-to-test code without justification.
* Confusing code execution with correct behavior.
* Running coverage against the wrong source directory.

## 13. Interview Questions

1. What is test coverage?
2. What is the difference between statement and branch coverage?
3. Does 100% coverage guarantee that an application has no bugs?
4. How do you generate a coverage report with pytest?
5. What is the purpose of `--cov-fail-under`?
6. Why are boundary tests important?
7. What is the purpose of an HTML coverage report?
8. How can you improve coverage without writing meaningless tests?

## 14. Practice Tasks

1. Install `pytest-cov`.
2. Create the calculator module and its tests.
3. Generate a terminal coverage report.
4. Add a test for a missing branch.
5. Generate an HTML report.
6. Set a minimum coverage threshold.
7. Explain why high coverage does not necessarily mean high test quality.
