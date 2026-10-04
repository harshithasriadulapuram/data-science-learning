# Database Testing with Pytest

## 1. What Is Database Testing?

Database testing verifies that application code interacts with a database correctly.

It helps check whether:

* Records are inserted, retrieved, updated, and deleted correctly.
* Constraints reject invalid data.
* Transactions commit or roll back correctly.
* Database errors are handled properly.
* Tests do not interfere with one another.

**Unit tests** often mock database dependencies, while **integration tests** use a real database to test actual SQL and database behavior.

## 2. Install the Required Packages

```bash
python -m pip install pytest
```

SQLite is included in Python's standard library, so this example does not require an additional database driver.

## 3. Create a Database Module

Create `app_db.py`:

```python
import sqlite3

def create_connection(db_path):
    return sqlite3.connect(db_path)

def create_users_table(connection):
    connection.execute("""
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY,
            name TEXT NOT NULL,
            email TEXT UNIQUE NOT NULL
        )
    """)
    connection.commit()

def add_user(connection, name, email):
    cursor = connection.execute(
        "INSERT INTO users (name, email) VALUES (?, ?)",
        (name, email)
    )
    connection.commit()
    return cursor.lastrowid

def get_user(connection, user_id):
    return connection.execute(
        "SELECT id, name, email FROM users WHERE id = ?",
        (user_id,)
    ).fetchone()
```

Parameterized queries keep user-supplied values separate from SQL syntax.

## 4. Write Tests Using a Temporary Database

Create `test_app_db.py`:

```python
import pytest

from app_db import (
    create_connection,
    create_users_table,
    add_user,
    get_user,
)

@pytest.fixture
def connection(tmp_path):
    db_path = tmp_path / "test.db"
    conn = create_connection(str(db_path))
    create_users_table(conn)

    yield conn

    conn.close()

def test_add_and_get_user(connection):
    user_id = add_user(
        connection,
        "Harshitha",
        "harshitha@example.com"
    )

    user = get_user(connection, user_id)

    assert user == (
        user_id,
        "Harshitha",
        "harshitha@example.com"
    )

def test_duplicate_email_is_rejected(connection):
    add_user(
        connection,
        "User One",
        "same@example.com"
    )

    with pytest.raises(Exception):
        add_user(
            connection,
            "User Two",
            "same@example.com"
        )

def test_missing_user_returns_none(connection):
    assert get_user(connection, 999) is None
```

The `tmp_path` fixture provides a separate temporary location for each test. This prevents tests from modifying your real application database.

For the duplicate-email test, you can improve precision by importing `sqlite3` and replacing `pytest.raises(Exception)` with `pytest.raises(sqlite3.IntegrityError)`.

## 5. Understand Pytest Fixtures

A fixture prepares resources needed by tests.

In the example:

1. Pytest creates a temporary database file.
2. The fixture opens a connection.
3. It creates the users table.
4. The test runs.
5. The fixture closes the connection.

Using `yield` separates setup from cleanup.

## 6. Test Transaction Rollback

```python
import sqlite3
import pytest

def test_transaction_rollback(tmp_path):
    db_path = str(tmp_path / "rollback.db")
    connection = sqlite3.connect(db_path)

    try:
        connection.execute(
            "CREATE TABLE accounts (balance INTEGER)"
        )
        connection.commit()

        with pytest.raises(sqlite3.IntegrityError):
            with connection:
                connection.execute(
                    "INSERT INTO accounts VALUES (100)"
                )
                connection.execute(
                    "INSERT INTO missing_table VALUES (200)"
                )

        count = connection.execute(
            "SELECT COUNT(*) FROM accounts"
        ).fetchone()[0]

        assert count == 0
    finally:
        connection.close()
```

When the second statement fails, the transaction context rolls back the first insert as well.

## 7. What Should You Test?

| Test area       | Example                                     |
| --------------- | ------------------------------------------- |
| CRUD            | Insert and retrieve a record                |
| Constraints     | Reject duplicate emails                     |
| Missing records | Return `None`                               |
| Transactions    | Roll back failed operations                 |
| Relationships   | Enforce foreign keys                        |
| Error handling  | Handle database exceptions                  |
| Integration     | Verify real SQL against the target database |
| Cleanup         | Close connections after failures            |

## 8. Common Mistakes

* Running tests against production data.
* Sharing one mutable database between unrelated tests.
* Forgetting to close connections.
* Catching overly broad exceptions in tests.
* Checking only successful cases.
* Assuming SQLite behavior always matches PostgreSQL or MySQL.
* Using mocks when the actual database behavior needs to be verified.

## 9. Run the Tests

From the directory containing `app_db.py` and `test_app_db.py`, run:

```bash
python -m pytest -v
```

Each test should pass if the code is saved correctly.

## 10. Interview Questions

1. What is database integration testing?
2. What is the difference between unit testing and integration testing?
3. What is a pytest fixture?
4. Why use a temporary database?
5. How do you test transaction rollback?
6. Why should tests assert specific exception types?
7. What is test isolation?
8. Why can SQLite tests differ from PostgreSQL tests?

## 11. Practice Tasks

1. Add an `update_user()` function and test it.
2. Add a `delete_user()` function and test it.
3. Test that an empty name is rejected if the schema requires it.
4. Replace broad exception handling in the tests with precise exception types.
5. Add a foreign key and test referential integrity.
6. Configure a separate integration-test suite for your production database engine.
