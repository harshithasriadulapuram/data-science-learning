
# Python Packaging and Distributions

## 1. What Is Python Packaging?

Python packaging is the process of organizing Python code and its metadata so that it can be built, installed, shared, and reused.

For example, you might write a utility package containing functions for cleaning data. Packaging allows another developer to install and import that code instead of copying individual files.

Packaging is useful for:
- Reusing code across projects.
- Sharing libraries with teammates.
- Distributing command-line applications.
- Publishing libraries to a package index.
- Managing project metadata and dependencies.

## 2. Module vs Package vs Distribution

These terms are related but different.

| Term | Meaning |
|---|---|
| Module | A Python file that can be imported |
| Package | A collection of related modules under a namespace |
| Distribution | An installable project released as a package artifact |
| Wheel | A built distribution format commonly ending in `.whl` |
| Source distribution | An archive containing source files and packaging metadata, commonly ending in `.tar.gz` |

A distribution can contain one or more Python packages or modules.

## 3. Example Project Structure

A modern project can use the `src` layout:

```text
sample-utils/
├── src/
│   └── sample_utils/
│       ├── __init__.py
│       └── calculations.py
├── tests/
│   └── test_calculations.py
├── pyproject.toml
└── README.md
```

The `src` directory contains the Python package. The `tests` directory contains automated tests.

The `src` layout helps prevent accidentally importing the source package directly from the project root when the package has not been installed.

## 4. Write the Package Code

Create `src/sample_utils/calculations.py`:

```python
def add_numbers(a: float, b: float) -> float:
    return a + b


def calculate_average(numbers: list[float]) -> float:
    if not numbers:
        raise ValueError("numbers cannot be empty")

    return sum(numbers) / len(numbers)
```

Create `src/sample_utils/__init__.py`:

```python
from .calculations import add_numbers, calculate_average

__all__ = ["add_numbers", "calculate_average"]
```

Now the package exposes two reusable functions.

## 5. Configure the Project With `pyproject.toml`

Create `pyproject.toml` in the project root:

```toml
[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"

[project]
name = "sample-utils"
version = "0.1.0"
description = "A small collection of reusable Python utilities"
readme = "README.md"
requires-python = ">=3.11"

[tool.setuptools.packages.find]
where = ["src"]
```

### What Do These Sections Mean?

- `[build-system]` specifies the build backend and its build requirements.
- `[project]` defines distribution metadata such as its name, version, and supported Python versions.
- `[tool.setuptools.packages.find]` tells setuptools to discover packages under `src`.

The distribution name `sample-utils` uses a hyphen, while the import package name `sample_utils` uses an underscore. These names do not have to be identical.

## 6. Install the Build Tool

From the project root, install the `build` package:

```bash
python -m pip install build
```

The `build` tool creates standard distribution artifacts using the project's configured build backend.

## 7. Build the Distribution

Run:

```bash
python -m build
```

A successful build typically creates:

```text
dist/
├── sample_utils-0.1.0-py3-none-any.whl
└── sample_utils-0.1.0.tar.gz
```

The exact filenames depend on the project's metadata and build configuration.

### Wheel

A wheel is a built distribution that can usually be installed without rebuilding the project from source.

### Source Distribution

A source distribution contains source files and metadata needed to build or install the project.

Both formats are commonly used when distributing Python libraries.

## 8. Install the Built Package

Install the wheel into the active Python environment:

```bash
python -m pip install dist/sample_utils-0.1.0-py3-none-any.whl
```

Then import the package:

```python
from sample_utils import add_numbers, calculate_average

print(add_numbers(10, 20))
print(calculate_average([10, 20, 30]))
```

Expected output:

```text
30
20.0
```

Use the actual wheel filename created by your build if it differs from the example.

For local development, you can instead install the project in editable mode:

```bash
python -m pip install -e .
```

Editable installation is useful because changes to source files are available without rebuilding and reinstalling the package after every edit.

## 9. Test the Package

Create `tests/test_calculations.py`:

```python
import pytest

from sample_utils import add_numbers, calculate_average


def test_add_numbers() -> None:
    assert add_numbers(10, 20) == 30


def test_calculate_average() -> None:
    assert calculate_average([10, 20, 30]) == 20


def test_average_empty_list() -> None:
    with pytest.raises(ValueError):
        calculate_average([])
```

Install pytest if needed:

```bash
python -m pip install pytest
```

Run the tests:

```bash
python -m pytest
```

Testing helps verify that the packaged functionality behaves as expected.

## 10. Understand Version Numbers

Python projects commonly use versions such as:

- `0.1.0` — an early development release.
- `1.0.0` — a stable release milestone.
- `1.1.0` — a release adding backward-compatible functionality.
- `1.1.1` — a release containing backward-compatible fixes.

These examples follow Semantic Versioning conventions. Python projects often use the related Python versioning standard, PEP 440, for version identifiers.

Before releasing an update, consider whether the changes affect backward compatibility.

## 11. Publishing to a Package Index

Python packages can be distributed through PyPI, the Python Package Index.

A typical release workflow includes:

1. Check project metadata.
2. Run automated tests.
3. Build the wheel and source distribution.
4. Inspect the built artifacts.
5. Test installation in a clean environment.
6. Upload the artifacts using an authorized publishing workflow.

For practice, use TestPyPI, a separate test environment for package publishing.

Do not upload a package containing credentials, private datasets, or confidential code.

Use trusted publishing or another supported authentication method. Avoid embedding publishing tokens directly in scripts or source files.

## 12. Common Mistakes

### Mistake 1: Confusing the distribution name with the import name

The installable name and import name may differ.

### Mistake 2: Forgetting package discovery

Ensure the build backend knows where your Python package is located.

### Mistake 3: Testing only from the source directory

Install the built artifact into a clean environment to catch packaging and import issues.

### Mistake 4: Omitting required files

Check that the built distribution contains the files needed at runtime.

### Mistake 5: Publishing before testing

Run tests and verify installation before releasing an artifact.

### Mistake 6: Committing build artifacts unnecessarily

Generated `dist/`, `build/`, and metadata directories are usually excluded from source control unless the project intentionally tracks them.

Example `.gitignore` entries:

```gitignore
dist/
build/
*.egg-info/
```

## 13. Interview Questions

**Q1. What is Python packaging?**

The process of preparing Python code and metadata for installation, reuse, and distribution.

**Q2. What is the difference between a module and a package?**

A module is typically a Python file, while a package groups related modules under a namespace.

**Q3. What is a wheel?**

A built distribution format used to distribute installable Python projects.

**Q4. What is a source distribution?**

An archive containing source files and packaging metadata from which a project can be built or installed.

**Q5. What is `pyproject.toml`?**

A standard configuration file for Python project metadata, build-system requirements, and tool settings.

**Q6. What is an editable installation?**

An installation, commonly performed with `pip install -e .`, that lets developers work on a project without rebuilding it after every source change.

**Q7. Why test a package in a clean environment?**

It helps reveal missing dependencies, packaging mistakes, and imports that work only because of the development directory.

## 14. Practice Tasks

- [ ] Create a package using the `src` layout.
- [ ] Add two reusable functions.
- [ ] Configure `pyproject.toml`.
- [ ] Build a wheel and source distribution.
- [ ] Install the wheel into a virtual environment.
- [ ] Import the installed package from a different directory.
- [ ] Run tests against the package.
- [ ] Explain the difference between a wheel and a source distribution.

## Key Takeaways

- Packaging makes Python code reusable and distributable.
- `pyproject.toml` defines project metadata and build configuration.
- Wheels and source distributions are common distribution formats.
- Test built artifacts in a clean environment.
- Publish only after testing and checking for sensitive files.
