# Python Linting and Formatting with Ruff

## 1. What Is Code Quality?

Code quality tools help developers identify common mistakes and maintain consistent coding standards.

Two important activities are:

* **Linting:** Detects potential bugs, unused imports, suspicious code, and style problems.
* **Formatting:** Automatically makes code follow a consistent layout.

Clean code is easier to review, maintain, debug, and collaborate on.

## 2. What Is Ruff?

Ruff is a fast Python linter and formatter.

It can help with:

* Detecting unused imports and variables
* Finding common programming mistakes
* Enforcing selected style rules
* Automatically fixing supported issues
* Formatting Python files consistently

Ruff can replace several separate tools for many common linting and formatting workflows.

## 3. Install Ruff

```bash
python -m pip install ruff
```

Verify the installation:

```bash
ruff --version
```

## 4. Check Your Python Code

Suppose `example.py` contains:

```python
import os
import math

def add(a,b):
    return a+b
```

Run the linter:

```bash
ruff check example.py
```

Ruff may report the unused imports and style violations according to its enabled rules.

## 5. Automatically Fix Supported Issues

```bash
ruff check --fix example.py
```

This fixes issues that Ruff can safely correct under the selected rules. Review the changes rather than assuming every issue can be fixed automatically.

## 6. Format Python Code

Run:

```bash
ruff format example.py
```

The code will be reformatted according to Ruff's formatting rules.

To check formatting without changing files:

```bash
ruff format --check example.py
```

## 7. Check an Entire Project

From the repository root:

```bash
ruff check .
```

Format the project:

```bash
ruff format .
```

Check formatting across the project:

```bash
ruff format --check .
```

For a larger repository, configure exclusions carefully so that generated files and virtual environments are not unnecessarily scanned.

## 8. Configure Ruff with `pyproject.toml`

Create or update `pyproject.toml` in your project root:

```toml
[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I"]

[tool.ruff.format]
quote-style = "double"
```

This example enables:

* `E`: Common pycodestyle errors
* `F`: Pyflakes checks, including many unused-import and undefined-name issues
* `I`: Import sorting rules

Ruff's formatter handles consistent formatting, while the selected lint rules determine which code issues are reported.

If `pyproject.toml` already exists, merge these settings into the existing file instead of replacing unrelated project configuration.

## 9. Ruff in a Development Workflow

A useful local workflow is:

```bash
ruff check --fix .
ruff format .
python -m pytest -v
```

This sequence checks and fixes supported lint issues, formats the code, and runs tests.

Review changes before committing them.

## 10. Add Ruff to GitHub Actions

After installing Ruff as a development dependency, add linting and formatting checks to your CI workflow.

Example steps:

```yaml
- name: Check lint rules
  run: ruff check .

- name: Check formatting
  run: ruff format --check .

- name: Run tests
  run: python -m pytest -v
```

Ensure the workflow installs Ruff before these steps execute.

CI checks help catch quality issues before code is merged.

## 11. Ruff vs. Black vs. Flake8

| Tool   | Main purpose                             |
| ------ | ---------------------------------------- |
| Ruff   | Linting, selected code fixes, formatting |
| Black  | Python code formatting                   |
| Flake8 | Linting and style checks                 |
| isort  | Import sorting                           |

Ruff can cover many workflows traditionally handled by these tools, though exact equivalence depends on the rules and plugins a project needs.

## 12. Common Mistakes

* Running lint checks without understanding the reported issue.
* Applying automatic fixes without reviewing the diff.
* Forgetting to install Ruff in CI.
* Having conflicting formatter configurations.
* Replacing an existing `pyproject.toml` instead of merging settings.
* Assuming clean lint output proves that the application is logically correct.

## 13. Interview Questions

1. What is linting?
2. What is code formatting?
3. What is Ruff?
4. What is the difference between linting and formatting?
5. What does `ruff check --fix` do?
6. How do you configure Ruff in `pyproject.toml`?
7. How can Ruff be integrated with GitHub Actions?
8. Does linting replace automated testing?

## 14. Practice Tasks

1. Install Ruff in a sample Python project.
2. Run `ruff check .`.
3. Fix supported linting issues.
4. Format your Python files.
5. Configure Ruff in `pyproject.toml`.
6. Add Ruff checks to GitHub Actions.
7. Explain why linting, formatting, and testing serve different purposes.
