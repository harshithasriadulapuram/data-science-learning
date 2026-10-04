# GitHub Actions CI with Pytest

## 1. What Is Continuous Integration?

Continuous Integration (CI) is the practice of automatically checking code changes by running tests and other validation steps.

A CI workflow can run whenever you push commits or open a pull request.

Benefits include:

* Detecting bugs early
* Catching broken tests before merging
* Keeping the main branch more reliable
* Reducing repetitive manual testing
* Making project quality easier to demonstrate

## 2. What Is GitHub Actions?

GitHub Actions is a GitHub automation platform that runs workflows defined in YAML files.

A workflow can:

1. Check out the repository.
2. Install Python.
3. Install project dependencies.
4. Run tests.
5. Report success or failure.

## 3. Recommended Project Structure

For a small project:

```text
my-project/
├── app.py
├── requirements.txt
├── tests/
│   └── test_app.py
└── .github/
    └── workflows/
        └── tests.yml
```

Your existing project may use a different structure. Adjust the test command and dependency installation to match it.

## 4. Create a Requirements File

Create `requirements.txt` in your repository root.

Example:

```text
pytest
```

Add your application's actual runtime and testing dependencies as needed. For reproducible projects, use an appropriate dependency-management approach and controlled versions.

## 5. Create a GitHub Actions Workflow

Create the following file:

`.github/workflows/tests.yml`

```yaml
name: Python Tests

on:
  push:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          python -m pip install -r requirements.txt

      - name: Run tests
        run: python -m pytest -v
```

This is a learning example. Before using a workflow in a production repository, review action versions and update them according to your project's maintenance and security policy.

## 6. Understand the Workflow

### `name`

```yaml
name: Python Tests
```

Defines the workflow's display name.

### `on`

```yaml
on:
  push:
  pull_request:
```

Runs the workflow on pushes and pull requests.

### `jobs`

A workflow contains one or more jobs.

### `runs-on`

```yaml
runs-on: ubuntu-latest
```

Selects the GitHub-hosted runner environment.

### `steps`

Each step performs an action or executes a command.

### `actions/checkout`

Makes the repository's files available to the runner.

### `actions/setup-python`

Installs or selects the configured Python version.

### Test command

```bash
python -m pytest -v
```

Runs pytest and displays individual test results.

## 7. Add the Workflow Using GitHub's Website

1. Open your repository on GitHub.
2. Select **Add file** and then **Create new file**.
3. Enter `.github/workflows/tests.yml` as the filename.
4. Paste the YAML workflow.
5. Commit the file to your branch.
6. Open the **Actions** tab in your repository.
7. Select the Python Tests workflow to view its run.

GitHub should start a workflow when the commit triggers the configured event.

## 8. Understand Workflow Results

* **Success:** The configured steps completed successfully.
* **Failure:** A step failed, such as dependency installation or tests.
* **In progress:** The runner is still executing the workflow.
* **Skipped:** The job or step did not run because of its conditions or dependencies.

Open a failed run and inspect the step logs to identify the cause.

## 9. Common CI Problems

### Incorrect test command

Make sure pytest can discover your test files.

### Missing dependencies

Install all packages required by the application and tests.

### Incorrect working directory

Configure the workflow to run commands from the correct directory.

### Environment variables

Provide required test configuration through appropriate environment settings. Never commit secrets to the repository.

### Local and CI differences

A test may pass locally but fail on Linux because of path casing, operating-system differences, or missing files.

### Database assumptions

Use a dedicated test database or temporary database. Do not connect automated tests to production data.

## 10. Best Practices

* Run tests on every pull request.
* Keep CI steps small and understandable.
* Avoid storing credentials in source code.
* Use a predictable Python version.
* Keep dependency installation reproducible.
* Add linting and formatting checks when appropriate.
* Investigate failures instead of repeatedly rerunning them without understanding the cause.
* Review workflow permissions and third-party actions before production use.

## 11. Interview Questions

1. What is Continuous Integration?
2. What is GitHub Actions?
3. What is a workflow file?
4. What is the purpose of a runner?
5. What is the difference between a job and a step?
6. How can CI run tests automatically on a pull request?
7. Why might tests pass locally but fail in CI?
8. How should CI handle secrets?
9. Why should automated tests not use production data?
10. How would you debug a failed GitHub Actions run?

## 12. Practice Tasks

1. Create the workflow in a sample Python repository.
2. Push a commit and inspect the Actions run.
3. Intentionally break a test and observe the failure.
4. Fix the test and confirm the workflow passes.
5. Add a coverage command to the workflow.
6. Explain how CI helps prevent broken code from being merged.
