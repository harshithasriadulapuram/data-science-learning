# Python Modules and Packages

Modules and packages help organize Python code so it can be reused and maintained.

## 1. What Is a Module?

A module is a Python file, usually ending in `.py`, that contains reusable code such as functions, classes, and variables.

Example file: `calculator.py`

```python
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b
```

## 2. Importing a Module

Suppose `calculator.py` is in the same directory as `main.py`.

```python
import calculator

print(calculator.add(10, 5))
```

Use the module name followed by a dot to access its members.

## 3. Import Specific Functions

```python
from calculator import add, subtract

print(add(10, 5))
print(subtract(10, 5))
```

This lets you use the imported names directly.

## 4. Import with an Alias

```python
import math as m

print(m.sqrt(25))
```

An alias provides an alternative name for an imported module or member.

## 5. Useful Standard Library Modules

```python
import math
print(math.sqrt(16))
print(math.ceil(4.2))

import random
print(random.randint(1, 10))

from datetime import date
print(date.today())

from pathlib import Path
print(Path("notes.md").suffix)
```

Other useful modules include `os`, `json`, `csv`, `statistics`, and `collections`.

## 6. The __name__ Variable

When a Python file runs as the program's entry point, its `__name__` is set to `"__main__"`. When imported, its `__name__` is normally the module name.

```python
def main():
    print("Program started")

if __name__ == "__main__":
    main()
```

This pattern lets a file be run directly or imported without automatically running its main program logic.

## 7. What Is a Package?

A package groups related modules in a directory. A common layout is:

```text
my_project/
    main.py
    utilities/
        __init__.py
        calculator.py
        text_tools.py
```

`__init__.py` is commonly used to mark a regular package and can contain package initialization code. Python also supports namespace packages without it.

## 8. Importing from a Package

```python
from utilities.calculator import add

print(add(3, 4))
```

The import path depends on the project's directory structure and how Python is launched.

## 9. Installing Third-Party Packages

Python includes a standard library, while third-party packages are installed separately.

```bash
python -m pip install requests
```

Then in Python:

```python
import requests

response = requests.get("https://example.com", timeout=10)
print(response.status_code)
```

Run installation commands in your terminal, not inside a Python script. The example requires an internet connection and the `requests` package.

## 10. Virtual Environments

Virtual environments isolate project dependencies.

Create one:

```bash
python -m venv .venv
```

Activate on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Activate on macOS or Linux:

```bash
source .venv/bin/activate
```

Install packages after activating the environment. A project can record dependencies in a `requirements.txt` file.

## 11. Common Import Errors

- `ModuleNotFoundError`: Python cannot find the requested module.
- `ImportError`: a requested name cannot be imported.
- Circular imports: modules depend on each other in a way that can interfere with initialization.
- Naming a file after a standard-library module, such as `random.py`, can shadow that module.

Check spelling, file paths, environment activation, and package installation when debugging imports.

## Practice Problems

1. Create a `calculator.py` module with add, subtract, multiply, and divide functions.
2. Import those functions into a separate `main.py` file.
3. Create a module containing string utility functions.
4. Use the `math`, `random`, and `datetime` modules.
5. Add a `main()` function and an `if __name__ == "__main__":` guard.
6. Organize two related modules inside a package directory.
7. Create a virtual environment and install one third-party package.

## Key Takeaways

- A module is a Python file containing reusable code.
- A package groups related modules.
- `import` and `from ... import ...` provide access to reusable code.
- The `__name__` guard separates direct execution from import behavior.
- Virtual environments help isolate project dependencies.
