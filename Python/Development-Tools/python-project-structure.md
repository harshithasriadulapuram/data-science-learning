
# Python Project Structure and Best Practices

## 1. Why Does Project Structure Matter?

A good project structure makes code easier to understand, test, maintain, and extend.

A poorly organized project often puts every function, configuration value, and dependency into one file. As the project grows, this becomes difficult to manage.

A well-organized project separates responsibilities into modules and packages.

Benefits:
- Improves readability and maintainability.
- Encourages code reuse.
- Makes testing easier.
- Separates application logic from configuration.
- Helps teammates understand the project quickly.

## 2. Example Python Project Structure

A small application might use this structure:

```text
my_project/
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── main.py
│       ├── config.py
│       └── utils.py
├── tests/
│   ├── __init__.py
│   └── test_utils.py
├── .env.example
├── .gitignore
├── pyproject.toml
├── README.md
└── requirements.txt
```

### Purpose of Each File

| File or directory | Purpose |
|---|---|
| `src/` | Contains the application package |
| `my_project/` | Main Python package |
| `__init__.py` | Marks a regular package and can expose selected package functionality |
| `main.py` | Application entry point |
| `config.py` | Configuration loading and validation |
| `utils.py` | Small reusable utility functions |
| `tests/` | Automated tests |
| `.env.example` | Documents required environment variables without real secrets |
| `.gitignore` | Lists files Git should ignore when they are untracked |
| `pyproject.toml` | Project metadata and tool configuration |
| `README.md` | Explains the project and how to run it |
| `requirements.txt` | Can list dependencies for pip installation |

This is an example, not a mandatory structure. Small scripts and libraries may need fewer files.

## 3. Separate Responsibilities Into Modules

A module is a Python file that contains related code.

For example, create `utils.py`:

```python
def calculate_average(numbers: list[float]) -> float:
    if not numbers:
        raise ValueError("numbers cannot be empty")

    return sum(numbers) / len(numbers)
```

Then import the function into `main.py`:

```python
from my_project.utils import calculate_average


def main() -> None:
    numbers = [10, 20, 30]
    average = calculate_average(numbers)
    print(f"Average: {average}")


if __name__ == "__main__":
    main()
```

Separating related functionality makes it easier to reuse and test code.

## 4. Use the Main Entry-Point Guard

Consider this pattern:

```python
if __name__ == "__main__":
    main()
```

Python sets `__name__` to `"__main__"` when a module is executed as the main program.

When the module is imported by another module, its `__name__` is normally the module's import name.

The guard prevents the entry-point function from running automatically just because the module is imported.

It does not prevent other top-level statements from executing during import.

## 5. Use `pyproject.toml` for Project Configuration

Modern Python projects commonly use `pyproject.toml` for project metadata and development-tool settings.

Example:

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "An example Python application"
requires-python = ">=3.11"
dependencies = []

[tool.pytest.ini_options]
testpaths = ["tests"]

[tool.ruff]
line-length = 88
```

This file centralizes selected project settings.

The `[project]` section follows modern Python packaging conventions. Tool-specific sections, such as `[tool.pytest.ini_options]` and `[tool.ruff]`, configure individual tools.

## 6. Keep Configuration Separate From Business Logic

Avoid scattering configuration values throughout your application.

For example:

```python
# Avoid scattering these values across many modules.
API_TIMEOUT = 30
MAX_RETRIES = 3
```

Instead, group related settings in one configuration module:

```python
import os

API_TIMEOUT = int(os.getenv("API_TIMEOUT", "30"))
MAX_RETRIES = int(os.getenv("MAX_RETRIES", "3"))
```

For larger applications, use a configuration class or a dedicated settings library.

Validate important settings during startup. Invalid values should produce clear errors.

Never hardcode real credentials or commit them to source control.

## 7. Separate Application Code and Tests

Keep tests outside the production package in a dedicated `tests/` directory.

Example `tests/test_utils.py`:

```python
import pytest

from my_project.utils import calculate_average


def test_calculate_average() -> None:
    assert calculate_average([10, 20, 30]) == 20


def test_calculate_average_with_one_number() -> None:
    assert calculate_average([5]) == 5


def test_calculate_average_with_empty_list() -> None:
    with pytest.raises(ValueError):
        calculate_average([])
```

Run the tests using:

```bash
python -m pytest
```

Tests should verify expected behavior, edge cases, and error handling.

## 8. Avoid Circular Imports

A circular import happens when modules depend on each other in a cycle.

For example:

- `module_a.py` imports `module_b.py`.
- `module_b.py` imports `module_a.py`.

This can cause partially initialized module errors or confusing dependencies.

Ways to reduce circular imports:
- Keep modules focused on one responsibility.
- Move shared functionality into a third module.
- Prefer a clear dependency direction.
- Avoid importing application entry-point modules from lower-level modules.

## 9. When Should You Create a Package?

A package groups related modules under a common namespace.

Example:

```text
my_project/
└── src/
    └── my_project/
        ├── __init__.py
        ├── data/
        │   ├── __init__.py
        │   └── loader.py
        ├── services/
        │   ├── __init__.py
        │   └── analyzer.py
        └── main.py
```

A structure like this can work well when an application has distinct responsibilities.

Do not create unnecessary directories for a tiny script. Structure should reduce complexity, not add it.

## 10. Common Project Organization Mistakes

### Mistake 1: Putting everything in one file

Large files become harder to understand and test.

### Mistake 2: Creating too many tiny modules

Excessive splitting can make simple logic difficult to follow.

### Mistake 3: Mixing tests with application logic

Separate tests help developers locate, run, and maintain them.

### Mistake 4: Committing virtual environments

Do not commit `.venv/` or `venv/`. Each developer can create a local environment.

### Mistake 5: Committing secrets

Keep real credentials out of source control and use appropriate configuration or secret-management mechanisms.

### Mistake 6: Running files from unexpected directories

Imports can behave differently depending on how Python is invoked and how the project is installed. For a package, prefer a consistent installation and execution approach.

## 11. Interview Questions

**Q1. What is a Python module?**

A Python module is a file containing Python code that can be imported and reused.

**Q2. What is a Python package?**

A package groups related modules under a common namespace. Regular packages commonly contain an `__init__.py` file, although Python also supports namespace packages.

**Q3. Why use `if __name__ == "__main__"`?**

It allows code to run when the module is executed directly without running that entry-point block when the module is imported.

**Q4. What is the purpose of `pyproject.toml`?**

It provides a standard place for project metadata, build-system configuration, and settings for development tools.

**Q5. Why separate tests from application code?**

It improves organization and makes it easier to execute, maintain, and expand automated tests.

**Q6. What is a circular import?**

It occurs when modules depend on each other in a cycle, potentially causing partially initialized modules or import errors.

## 12. Practice Tasks

- [ ] Create a small project with separate application and test directories.
- [ ] Move reusable functions into a module.
- [ ] Import those functions from another module.
- [ ] Add a main-entry-point guard.
- [ ] Create a `pyproject.toml` file.
- [ ] Write tests using pytest.
- [ ] Add `.venv/` and `.env` to `.gitignore`.
- [ ] Explain when a simple script should become a package.

## Key Takeaways

- Organize code around clear responsibilities.
- Use modules and packages to support reuse.
- Keep tests separate from application logic.
- Centralize configuration where practical.
- Use `pyproject.toml` for project metadata and tool settings.
- Prefer the simplest structure that supports the project's needs.
