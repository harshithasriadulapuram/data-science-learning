
# Pre-Commit Hooks in Python

## 1. What Are Pre-Commit Hooks?

A pre-commit hook is an automated check that runs before Git creates a commit.

It can check code formatting, identify common mistakes, validate files, and run selected tests.

For Python projects, pre-commit hooks commonly run tools such as:
- Ruff for linting and formatting checks
- pytest for tests
- detect-secrets or other secret-scanning tools
- Trailing-whitespace and end-of-file checks

**Why are they useful?**

They catch problems early, before code enters the project's commit history.

## 2. What Is the `pre-commit` Framework?

The `pre-commit` framework manages and runs hooks consistently across a project.

Install it in your development environment:

```bash
python -m pip install pre-commit
```

Check the installation:

```bash
pre-commit --version
```

## 3. Create the Configuration File

Create a file named `.pre-commit-config.yaml` in the project root.

Example configuration:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-merge-conflict

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.14.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

The version numbers above are examples of pinned releases. Before adopting this configuration, verify that the selected releases are compatible with your project and update them deliberately.

### What Do These Hooks Do?

| Hook | Purpose |
|---|---|
| `trailing-whitespace` | Removes unnecessary spaces at line endings |
| `end-of-file-fixer` | Ensures files end with a newline |
| `check-yaml` | Checks YAML syntax |
| `check-merge-conflict` | Detects unresolved merge-conflict markers |
| `ruff` | Finds Python code issues and can automatically fix supported ones |
| `ruff-format` | Formats Python code consistently |

## 4. Install the Git Hook

After creating the configuration file, run:

```bash
pre-commit install
```

This installs a Git hook that invokes the configured checks before commits.

From then on, the checks run when you commit files through Git.

**Note:** GitHub's website-based file editor does not run your local pre-commit hooks. They apply to commits made through a local Git checkout where the hook is installed.

## 5. Run Checks Manually

Run every configured hook against all files:

```bash
pre-commit run --all-files
```

Run only one hook:

```bash
pre-commit run ruff --all-files
```

Run the formatter hook:

```bash
pre-commit run ruff-format --all-files
```

Running the checks manually is useful before opening a pull request or configuring continuous integration.

## 6. Add pytest to the Workflow

Pre-commit hooks are usually best for fast checks. Running the full test suite on every commit can slow down development, so many teams run tests in continuous integration instead.

You can still add a local test hook if your project needs it:

```yaml
repos:
  - repo: local
    hooks:
      - id: pytest
        name: Run pytest
        entry: python -m pytest -q
        language: system
        pass_filenames: false
        always_run: true
```

Add this entry to the existing `repos` list rather than replacing other hooks.

The local hook uses the Python environment in which pre-commit runs. Make sure pytest is installed in that environment.

## 7. What Happens When a Hook Fails?

Suppose a file contains trailing whitespace.

When you attempt to commit:
1. The hook runs.
2. It identifies or fixes the problem.
3. The commit may be stopped.
4. You review the changes.
5. You stage the updated files and commit again.

Some hooks modify files automatically. Always review those modifications before committing.

## 8. How Does This Fit With CI?

Pre-commit hooks and continuous integration serve different purposes.

| Pre-commit hooks | Continuous integration |
|---|---|
| Usually run locally before a commit | Runs on a CI server after a push or pull request |
| Give quick feedback | Validates the pushed changes in a controlled environment |
| Can be bypassed | Can be enforced through repository branch protection and required checks |
| Useful for formatting and quick checks | Useful for tests, builds, security checks, and broader validation |

Do not rely exclusively on local hooks for critical checks because a developer can bypass them. Enforce essential validations in CI as well.

## 9. Common Mistakes

### Mistake 1: Forgetting to install the hook

Creating `.pre-commit-config.yaml` alone does not install the Git hook.

Run:

```bash
pre-commit install
```

### Mistake 2: Not testing all files

New hooks may uncover existing issues throughout the repository.

Run:

```bash
pre-commit run --all-files
```

### Mistake 3: Ignoring automatic changes

Review modified files and stage the intended changes before committing.

### Mistake 4: Using unpinned versions

Pin hook revisions so teammates run predictable versions. Update those revisions deliberately.

### Mistake 5: Assuming hooks cannot be bypassed

Local hooks are a convenience, not a security boundary. Use CI and repository rules for mandatory checks.

## 10. Interview Questions

**Q1. What is a pre-commit hook?**

An automated check that runs before a Git commit is created.

**Q2. Why use the pre-commit framework?**

It provides a consistent way to configure and execute checks across a development team.

**Q3. What is the difference between pre-commit and CI?**

Pre-commit usually provides fast local feedback; CI validates pushed changes in a controlled environment.

**Q4. Can a pre-commit hook modify files?**

Yes. Formatters and cleanup hooks can automatically modify files, which should then be reviewed and staged.

**Q5. Are local hooks enough to enforce code quality?**

No. They can be bypassed or omitted, so critical checks should also run in CI.

## 11. Practice Tasks

- [ ] Install the `pre-commit` package in a test project.
- [ ] Create `.pre-commit-config.yaml`.
- [ ] Configure whitespace and Ruff checks.
- [ ] Install the Git hook.
- [ ] Run all hooks against all files.
- [ ] Introduce a small formatting issue and observe the result.
- [ ] Explain how local hooks differ from GitHub Actions.
- [ ] Describe why critical checks must also run in CI.

## Key Takeaways

- Pre-commit hooks catch common problems before commits are created.
- The framework uses `.pre-commit-config.yaml` to define hooks.
- Run `pre-commit install` to activate local Git hooks.
- Use pinned hook versions and review automatic changes.
- Combine local checks with CI for reliable project quality.
