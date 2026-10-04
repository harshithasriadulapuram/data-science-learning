# Python Dependency Management

## 1. What is a dependency?
A dependency is a library or package that a project relies on. For example, a data science project may depend on NumPy, Pandas, and scikit-learn.

## 2. Why manage dependencies?
- Document what a project needs to run.
- Reduce version conflicts.
- Help teammates recreate the environment.
- Make deployment and debugging more predictable.

## 3. Install packages inside a virtual environment
Activate your project's virtual environment first, then run:

```bash
python -m pip install numpy pandas scikit-learn
```

Check the installed packages:
```bash
python -m pip list
```

## 4. Create requirements.txt
Record installed package versions:

```bash
python -m pip freeze > requirements.txt
```

Example contents (versions are illustrative):
```text
numpy==2.0.0
pandas==2.2.0
scikit-learn==1.5.0
```

`pip freeze` records installed distributions, including transitive dependencies. In a large environment, review the file to ensure it represents the project dependencies you intend to share.

## 5. Install from requirements.txt
On another machine or in a fresh virtual environment:

```bash
python -m pip install -r requirements.txt
```

Use compatible Python and operating-system versions when reproducing an environment.

## 6. Update a dependency
To upgrade a package:

```bash
python -m pip install --upgrade pandas
```

Then review and regenerate the requirements file if appropriate:

```bash
python -m pip freeze > requirements.txt
```

Test the project after updating packages because APIs or behavior may change between versions.

## 7. Check dependency health
```bash
python -m pip check
```

This checks whether installed packages have incompatible declared dependencies. It does not guarantee that the application itself works correctly.

## 8. Development dependencies
Some packages are needed only while developing or testing a project, such as pytest. You can document them separately, for example in `requirements-dev.txt`:

```text
-r requirements.txt
pytest
```

Install them with:
```bash
python -m pip install -r requirements-dev.txt
```

This is a simple convention; projects may use other dependency-management tools.

## 9. Example project structure
```text
my-project/
├── .venv/
├── src/
│   └── my_project/
│       └── __init__.py
├── tests/
│   └── test_example.py
├── requirements.txt
├── requirements-dev.txt
├── .gitignore
└── README.md
```

The `.venv/` directory is local and should usually be excluded from Git. The structure can vary by project.

## 10. Useful .gitignore entries
```gitignore
.venv/
__pycache__/
*.py[cod]
.env
.pytest_cache/
```

Never commit passwords, API keys, or other secrets. If a secret is accidentally committed, remove it from use and rotate it; deleting the visible file alone may not remove it from Git history.

## 11. Requirements files and other tools
- `requirements.txt`: a common pip-based way to list installable dependencies.
- `pyproject.toml`: modern project configuration and packaging metadata; tools can also use it to declare dependencies.
- Poetry, uv, and other tools provide additional workflows for dependency resolution and environment management.

Choose one approach that fits the project and document how to install dependencies.

## 12. Practical workflow
1. Create and activate a virtual environment.
2. Install only the packages needed for the project.
3. Run the application and tests.
4. Record dependencies in the chosen format.
5. Commit dependency files and `.gitignore`, not the environment directory.
6. Recreate the environment in a clean setup and test the documented installation steps.

## 13. Practice tasks
1. Create a virtual environment for a small Python project.
2. Install NumPy and Pandas.
3. Generate `requirements.txt` and inspect its contents.
4. Install those requirements into a second environment.
5. Run `python -m pip check`.
6. Add a `.gitignore` file and exclude `.venv/`.

## 14. Interview questions
1. What is a dependency in Python?
2. What does `pip freeze` do?
3. How do you install packages from `requirements.txt`?
4. Why should a project use a virtual environment?
5. What is the difference between runtime and development dependencies?
6. What is the purpose of `pyproject.toml`?
7. Why should `.venv/` and `.env` usually be excluded from Git?
