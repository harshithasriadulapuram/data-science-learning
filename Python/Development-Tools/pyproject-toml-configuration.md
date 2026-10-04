
# Python Project Configuration with pyproject.toml

## 1. What Is pyproject.toml?

`pyproject.toml` is a configuration file used by Python projects to define project metadata, dependencies, build settings, and configuration for development tools.

It provides a standard location for settings that might otherwise be spread across multiple configuration files.

Common uses:
- Defining project name and version.
- Declaring Python version requirements.
- Managing dependencies.
- Configuring build systems.
- Setting up tools such as Ruff, pytest, and coverage.
- Supporting package building and distribution.

## 2. Why Is It Important?

Without a consistent configuration file, projects may contain many separate files:

```text
requirements.txt
setup.cfg
setup.py
tox.ini
pytest.ini
.flake8
```

Some projects still use these files, and they remain valid. However, `pyproject.toml` can consolidate many settings into one place.

Benefits include:
- Easier project maintenance.
- More consistent tooling.
- Clear dependency declarations.
- Simpler onboarding for contributors.
- Better support for modern Python packaging.

## 3. Basic Project Configuration

Example:

```toml
[project]
name = "sales-analyzer"
version = "0.1.0"
description = "A Python project for sales analysis"
requires-python = ">=3.11"
dependencies = [
    "pandas>=2.0",
    "numpy>=1.26",
]
```

### Explanation

- `name`: The distribution name of the project.
- `version`: The project's version.
- `description`: A short description.
- `requires-python`: Python versions supported by the project.
- `dependencies`: Runtime packages required by the project.

These dependency declarations specify minimum versions. They do not necessarily pin every dependency to one exact version.

## 4. Understand TOML Syntax

TOML stands for Tom's Obvious, Minimal Language.

Example:

```toml
project_name = "sales-analyzer"
debug = true
retry_count = 3

[database]
host = "localhost"
port = 5432
```

Important syntax rules:
- Key-value pairs use `key = value`.
- Strings use quotation marks.
- Booleans are `true` or `false`.
- Arrays use square brackets.
- Tables use section headers such as `[database]`.

TOML is configuration data, not Python code.

## 5. Configure Development Dependencies

Runtime dependencies are needed by the application.

Development dependencies are useful for testing, formatting, linting, and other development tasks.

Example:

```toml
[project]
name = "sales-analyzer"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "pandas",
    "numpy",
]

[dependency-groups]
dev = [
    "pytest",
    "pytest-cov",
    "ruff",
]
```

The `[dependency-groups]` table is supported by modern Python packaging tools, including uv.

Install dependencies with uv:

```bash
uv sync
```

Run tests:

```bash
uv run pytest
```

Run the linter:

```bash
uv run ruff check .
```

## 6. Configure Ruff

Ruff can check Python code for common errors and style issues and can format Python files.

Example:

```toml
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I"]

[tool.ruff.format]
quote-style = "double"
```

### Explanation

- `line-length`: Preferred maximum line length.
- `target-version`: Python version used when interpreting supported syntax.
- `E`: Pycodestyle errors.
- `F`: Pyflakes checks.
- `I`: Import sorting rules.
- `quote-style`: Preferred quotation style when formatting.

Run Ruff:

```bash
ruff check .
ruff format .
```

Review automatic changes before committing them.

## 7. Configure pytest

Example:

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "-ra"
```

### Explanation

- `testpaths`: Directories where pytest searches for tests.
- `python_files`: File naming pattern for test modules.
- `python_functions`: Function naming pattern for test functions.
- `addopts`: Default command-line options.

Example project structure:

```text
sales-analyzer/
├── src/
│   └── sales_analyzer/
│       └── __init__.py
├── tests/
│   └── test_sales.py
├── pyproject.toml
└── README.md
```

Run the tests:

```bash
pytest
```

## 8. Configure Test Coverage

If the project uses `pytest-cov`, configure it in the same file:

```toml
[tool.coverage.run]
branch = true
source = ["sales_analyzer"]

[tool.coverage.report]
show_missing = true
skip_covered = true
```

Run tests with coverage:

```bash
pytest --cov=sales_analyzer --cov-report=term-missing
```

Coverage measures which code was executed during tests. High coverage does not automatically mean that tests are correct or comprehensive.

## 9. Configure a Build System

If your project is distributed as a package, declare a build backend.

Example using Hatchling:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

This section tells packaging tools which backend to use to build the project.

The build backend must be installed or made available by the build frontend.

Other backends exist, including setuptools and Flit. Choose one that matches the project's requirements.

## 10. Optional Project Scripts

You can expose command-line entry points for an installable Python project.

Example:

```toml
[project.scripts]
sales-analyzer = "sales_analyzer.cli:main"
```

This assumes that the package contains a callable `main()` function in `sales_analyzer/cli.py`.

After installing the package into an environment, users can run:

```bash
sales-analyzer
```

The entry point must refer to a real importable module and callable function.

## 11. Environment Variables and Secrets

Do not store passwords, API keys, tokens, or other secrets directly in `pyproject.toml`.

Bad practice:

```toml
[tool.myapp]
api_key = "actual-secret-key"
```

Prefer environment variables or an appropriate secrets manager.

Example Python code:

```python
import os

api_key = os.environ["API_KEY"]
```

For local development, a `.env` file may be useful when handled by an appropriate library. Keep secret files out of version control.

## 12. Common Mistakes

### Mistake 1: Invalid TOML syntax

Incorrect:

```toml
debug = True
```

Correct:

```toml
debug = true
```

TOML booleans are lowercase.

### Mistake 2: Using an unsupported configuration key

Different tools recognize different settings. A valid TOML file can still contain an option that a specific tool does not support.

Check the documentation for each configured tool.

### Mistake 3: Confusing dependencies with development tools

Runtime packages and development-only packages serve different purposes. Separate them when your packaging workflow supports it.

### Mistake 4: Declaring a console script that does not exist

If a script points to a missing module or callable, the installed command will fail.

Verify the entry point before publishing a package.

### Mistake 5: Duplicating conflicting configuration

If a tool is configured in multiple files, it can become difficult to understand which settings take effect.

Consolidate configuration where practical, and check each tool's precedence rules.

## 13. Interview Questions

**Q1. What is pyproject.toml?**

A standardized configuration file for Python project metadata, packaging, dependencies, and development tools.

**Q2. What is the difference between runtime and development dependencies?**

Runtime dependencies are needed by the application. Development dependencies support tasks such as testing, linting, and formatting.

**Q3. What is the `[build-system]` section?**

It declares the build requirements and backend used to build a Python distribution.

**Q4. Can multiple tools use pyproject.toml?**

Yes. Tools commonly use sections under `[tool.<tool-name>]`.

**Q5. Is pyproject.toml a replacement for every configuration file?**

No. Many tools support it, but some still require or support separate configuration files.

**Q6. Should API keys be stored in pyproject.toml?**

No. Secrets should be supplied through environment variables or a dedicated secrets manager.

## 14. Practice Tasks

- [ ] Create a `pyproject.toml` file.
- [ ] Add project metadata and Python version requirements.
- [ ] Declare runtime dependencies.
- [ ] Add pytest and Ruff as development dependencies.
- [ ] Configure Ruff linting and formatting.
- [ ] Configure pytest test discovery.
- [ ] Run tests and lint checks.
- [ ] Explain the difference between project metadata and tool configuration.
- [ ] Identify where secrets should be stored.

## Key Takeaways

- `pyproject.toml` centralizes modern Python project configuration.
- `[project]` defines project metadata and dependencies.
- `[build-system]` identifies the package build backend.
- `[tool.*]` sections configure supported development tools.
- Keep secrets out of source-controlled configuration.
- Validate configuration by running the tools that consume it.
