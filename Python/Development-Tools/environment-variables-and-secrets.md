
# Environment Variables and Secrets in Python

## 1. What Are Environment Variables?

Environment variables are key-value pairs provided to a running process by its environment.

They help us configure applications without hardcoding settings directly into source code.

Common examples:
- API keys
- Database URLs
- Application environment (`development`, `testing`, `production`)
- Debug settings
- Service endpoints

```python
import os

app_environment = os.getenv("APP_ENV", "development")
print(app_environment)
```

`os.getenv()` returns the value of an environment variable. If it does not exist, it returns the supplied default.

## 2. Why Should We Use Environment Variables?

Avoid hardcoding configuration and sensitive values:

```python
# Bad practice: never commit real credentials
api_key = "my-real-secret-api-key"
```

Instead, read them from the environment:

```python
import os

api_key = os.getenv("API_KEY")

if not api_key:
    raise RuntimeError("API_KEY is not configured")
```

Benefits:
- Keeps secrets out of source code.
- Allows different configurations for development and production.
- Makes deployment easier.
- Reduces the risk of accidentally exposing credentials.

**Important:** Environment variables are not automatically encrypted. Access to the machine, process, logs, and deployment configuration must still be controlled.

## 3. Reading Environment Variables

Use `os.getenv()` when a value may be missing:

```python
import os

database_url = os.getenv("DATABASE_URL")
debug_mode = os.getenv("DEBUG", "false")

print(database_url)
print(debug_mode)
```

Use `os.environ` when you want to access a required variable directly:

```python
import os

database_url = os.environ["DATABASE_URL"]
```

If `DATABASE_URL` does not exist, this raises `KeyError`.

### Checking Whether a Variable Exists

```python
import os

if "API_KEY" in os.environ:
    print("API key is configured")
else:
    print("API key is missing")
```

Do not print the actual secret value.

## 4. Setting Environment Variables

### Windows PowerShell — Current Session

```powershell
$env:APP_ENV = "development"
$env:API_KEY = "your-test-key"
python app.py
```

These values apply to the current PowerShell session and its child processes.

### macOS or Linux — Current Shell

```bash
export APP_ENV="development"
export API_KEY="your-test-key"
python app.py
```

Use placeholder or test credentials when practicing. Never expose a real secret in screenshots or shared terminals.

## 5. Using a `.env` File

A `.env` file stores local configuration as key-value pairs.

Example `.env`:

```dotenv
APP_ENV=development
DEBUG=false
API_KEY=replace-with-your-local-test-key
DATABASE_URL=sqlite:///app.db
```

Install `python-dotenv`:

```bash
python -m pip install python-dotenv
```

Load the file:

```python
import os
from dotenv import load_dotenv

load_dotenv()

app_environment = os.getenv("APP_ENV", "development")
api_key = os.getenv("API_KEY")

print("Environment:", app_environment)
print("API key configured:", bool(api_key))
```

Notice that the program checks whether the API key exists without printing the secret.

### Recommended Project Structure

```text
my_project/
├── .env
├── .gitignore
├── requirements.txt
└── app.py
```

## 6. Never Commit `.env` Files

Add the following entries to `.gitignore`:

```gitignore
# Environment variables and secrets
.env
.env.*
!.env.example

# Python environment
.venv/
venv/
__pycache__/
*.py[cod]
```

A `.gitignore` file prevents matching untracked files from being added to Git by normal Git workflows. It does not remove files already tracked by Git.

If a secret was committed, deleting the file in a later commit is not enough. Revoke or rotate the exposed credential and follow the repository's secret-removal procedure.

## 7. Create a Safe `.env.example`

Commit an example file that documents the required variables without containing real credentials.

Example `.env.example`:

```dotenv
APP_ENV=development
DEBUG=false
API_KEY=
DATABASE_URL=sqlite:///app.db
```

Team members can copy `.env.example` to `.env` and fill in their own local values.

Never put real passwords, access tokens, or production credentials in the example file.

## 8. Validate Required Configuration

Centralize configuration validation instead of repeatedly checking variables throughout the application.

```python
import os
from dotenv import load_dotenv

load_dotenv()


def get_required_env(name: str) -> str:
    value = os.getenv(name)

    if value is None or not value.strip():
        raise RuntimeError(
            f"Required environment variable is missing: {name}"
        )

    return value


def main() -> None:
    api_key = get_required_env("API_KEY")

    # Pass the key to the service that needs it.
    # Do not print or log the secret.
    print("API key is configured:", bool(api_key))


if __name__ == "__main__":
    main()
```

Failing early with a clear error is safer than letting the application fail unexpectedly later.

## 9. Environment Variables Are Strings

Environment variables are normally read as strings. Convert and validate them when needed.

```python
import os

port = int(os.getenv("PORT", "8000"))

debug_value = os.getenv("DEBUG", "false").strip().lower()
debug = debug_value in {"1", "true", "yes", "on"}

print("Port:", port)
print("Debug enabled:", debug)
```

For production applications, validate accepted values explicitly. Invalid configuration should produce a useful error rather than being silently accepted.

## 10. Managing Secrets in Production

For production systems, prefer a dedicated secrets manager or your cloud provider's secret-management service.

Examples include:
- AWS Secrets Manager
- Azure Key Vault
- Google Cloud Secret Manager
- HashiCorp Vault

Good practices:
1. Give each application only the permissions it needs.
2. Rotate credentials periodically and immediately after exposure.
3. Avoid placing secrets in source code, logs, error messages, or screenshots.
4. Restrict who can read deployment configuration.
5. Use separate credentials for development, testing, and production.
6. Never assume that a private GitHub repository makes committed secrets safe.

## 11. Common Mistakes

### Mistake 1: Printing secrets

```python
# Avoid
print(os.getenv("API_KEY"))
```

Prefer:

```python
print("API key configured:", bool(os.getenv("API_KEY")))
```

### Mistake 2: Assuming `.env` loads automatically

A `.env` file is not automatically loaded by Python. Use `python-dotenv` or your application's configuration mechanism.

### Mistake 3: Committing `.env`

Add it to `.gitignore` before staging files.

### Mistake 4: Using defaults for critical secrets

Do not silently replace a missing production API key or password with a dummy value. Validate required configuration and stop startup when it is missing.

### Mistake 5: Treating all environment values as booleans or numbers

Convert and validate strings explicitly.

## 12. Interview Questions

**Q1. What are environment variables?**

Configuration values supplied to a process by its execution environment.

**Q2. What is the difference between `os.getenv()` and `os.environ[]`?**

`os.getenv()` can return a default when a variable is missing. `os.environ[]` raises `KeyError` when the requested variable does not exist.

**Q3. What is a `.env` file?**

A local text file containing configuration values that can be loaded into the process environment by a suitable library or application configuration mechanism.

**Q4. Why should `.env` be added to `.gitignore`?**

To reduce the risk of committing local credentials and configuration secrets.

**Q5. Is a `.env` file secure by itself?**

No. It is plain text. File permissions, access controls, safe handling, and avoiding accidental exposure are still necessary.

**Q6. What should you do if an API key is accidentally pushed to GitHub?**

Revoke or rotate it immediately, investigate potential misuse, and remove the exposed secret from repository history when appropriate.

**Q7. How should production secrets be managed?**

Use a secrets manager or a secure platform-provided mechanism with restricted access and a rotation process.

## 13. Practice Tasks

- [ ] Read `APP_ENV` with a default value.
- [ ] Read a required `API_KEY` and raise an error if it is missing.
- [ ] Load local variables from a `.env` file.
- [ ] Add `.env` to `.gitignore`.
- [ ] Create a safe `.env.example`.
- [ ] Convert a `PORT` variable to an integer and validate it.
- [ ] Explain why secrets must not be committed to GitHub.
- [ ] Describe what to do after accidentally exposing a credential.

## Key Takeaways

- Keep configuration separate from application code.
- Read environment variables with `os.getenv()` or `os.environ`.
- Use `.env` files for local development when appropriate.
- Never commit real credentials.
- Validate required configuration at application startup.
- Use a dedicated secrets manager for production credentials.
