# Python Virtual Environments and Dependency Management

## 1. What is a virtual environment?
A virtual environment is an isolated Python environment for a project. It allows a project to use its own installed packages without mixing them with packages from other projects.

## 2. Why use virtual environments?
- Avoid dependency conflicts between projects.
- Keep project requirements separate.
- Make environments easier to reproduce.
- Keep global Python installations cleaner.

## 3. Create a virtual environment
Run these commands from your project's root directory.

Windows:
```powershell
python -m venv .venv
```

macOS or Linux:
```bash
python3 -m venv .venv
```

This creates a `.venv` directory containing the environment.

## 4. Activate the environment
Windows PowerShell:
```powershell
.\.venv\Scripts\Activate.ps1
```

Windows Command Prompt:
```bat
.venv\Scripts\activate.bat
```

macOS or Linux:
```bash
source .venv/bin/activate
```

Your terminal usually displays `(.venv)` when the environment is active.

## 5. Install packages
With the environment activated:
```bash
python -m pip install pandas numpy scikit-learn
```

Using `python -m pip` helps ensure pip belongs to the selected Python interpreter.

## 6. Check installed packages
```bash
python -m pip list
```

Check which Python interpreter is running:

Windows:
```powershell
where.exe python
```

macOS or Linux:
```bash
which python
```

## 7. Save dependencies
To record installed packages and versions:
```bash
python -m pip freeze > requirements.txt
```

A requirements file may contain entries such as:
```text
numpy==2.0.0
pandas==2.2.0
```

These versions are examples only; use versions appropriate for your project.

## 8. Install dependencies from a file
After activating the environment:
```bash
python -m pip install -r requirements.txt
```

This installs the packages listed in `requirements.txt`, subject to platform and Python compatibility.

## 9. Deactivate the environment
```bash
deactivate
```

This exits the active virtual environment. It does not delete it.

## 10. Use virtual environments with Git
Usually, do not commit the `.venv` directory to GitHub. It can be large and is specific to a local environment.

Add this to your `.gitignore` file:
```gitignore
.venv/
__pycache__/
*.py[cod]
.env
```

Keep secrets such as API keys in environment variables or a local `.env` file, and never commit real credentials. If using `.env`, a project may need a package such as `python-dotenv` to load it.

## 11. A typical project setup
```bash
# Create the environment
python -m venv .venv

# Activate it using the command for your operating system

# Upgrade pip
python -m pip install --upgrade pip

# Install project packages
python -m pip install pandas streamlit scikit-learn

# Record installed packages
python -m pip freeze > requirements.txt
```

## 12. Common problems
- **Command not found:** Check that Python is installed and available on PATH.
- **Wrong Python version:** Activate the environment and check `python --version`.
- **Package not found in your environment:** Activate the correct environment and install the package with `python -m pip`.
- **PowerShell activation blocked:** Follow your organization's security policy or use Command Prompt; do not weaken security settings unnecessarily.
- **Dependency conflicts:** Review package version requirements and test a compatible set of versions.

## 13. Important points
- Create a separate environment for each project when practical.
- Activate the environment before installing or running project dependencies.
- Commit dependency files such as `requirements.txt`, not the `.venv` folder.
- Recreate the environment on another machine by installing the recorded requirements.
- `requirements.txt` records packages but does not guarantee identical environments across all operating systems.

## 14. Practice tasks
1. Create and activate a virtual environment.
2. Install NumPy and Pandas inside it.
3. Generate a `requirements.txt` file.
4. Create a new environment and install from that file.
5. Add `.venv/` to `.gitignore` and verify that Git does not track the environment folder.

## 15. Interview questions
1. What is a virtual environment in Python?
2. Why should each project have its own environment?
3. What is the purpose of `requirements.txt`?
4. What is the difference between installing packages globally and in a virtual environment?
5. Why should `.venv` usually be excluded from Git?
6. What is the purpose of `.gitignore`?
