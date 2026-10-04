
# Database Connection Management in Python

## 1. What Is Database Connection Management?

Database connection management is the process of creating, using, and releasing database connections safely.

A database connection allows a Python application to communicate with a database, execute queries, and manage transactions.

Good connection management helps prevent:
- Connection leaks
- Unnecessary resource usage
- Uncommitted transactions
- Locked resources
- Failures under concurrent workloads

## 2. Opening and Closing a Connection

A basic SQLite example:

```python
import sqlite3

connection = sqlite3.connect("app.db")

try:
    cursor = connection.execute("SELECT 1")
    print(cursor.fetchone())
finally:
    connection.close()
```

The `finally` block ensures that the connection is closed even if an exception occurs.

However, closing a connection immediately after every operation may not be the best approach for a high-traffic application. A connection pool may be more suitable.

## 3. Using a Context Manager

A context manager manages resource setup and cleanup.

Python's `with` statement is commonly used to ensure that resources are handled correctly.

```python
with open("notes.txt", "r", encoding="utf-8") as file:
    content = file.read()
```

The file is closed when the block exits, including when an exception occurs.

Database APIs have their own context-manager behavior. Do not assume that every database connection's `with` statement automatically closes the connection.

## 4. SQLite Connection Context Manager

For SQLite, the connection context manager manages transaction completion but does not close the connection.

```python
import sqlite3

connection = sqlite3.connect("app.db")

try:
    with connection:
        connection.execute(
            "INSERT INTO logs(message) VALUES (?)",
            ("Application started",),
        )
finally:
    connection.close()
```

The transaction is committed when the block succeeds and rolled back if an exception occurs.

The outer `finally` block closes the connection.

This distinction is important:
- Transaction management controls commit and rollback.
- Connection management controls the lifetime of the connection.

## 5. Using a Custom Context Manager

Python allows you to create your own context managers.

```python
import sqlite3
from contextlib import contextmanager


@contextmanager
def get_connection(database_path):
    connection = sqlite3.connect(database_path)

    try:
        yield connection
    finally:
        connection.close()


with get_connection("app.db") as connection:
    result = connection.execute("SELECT 1")
    print(result.fetchone())
```

The `yield` statement temporarily provides the connection to the caller.

After the `with` block finishes, execution resumes in the `finally` block and closes the connection.

## 6. Transaction-Safe Context Manager

A helper can separate connection cleanup from transaction handling.

```python
import sqlite3
from contextlib import contextmanager


@contextmanager
def database_transaction(database_path):
    connection = sqlite3.connect(database_path)

    try:
        yield connection
        connection.commit()
    except Exception:
        connection.rollback()
        raise
    finally:
        connection.close()
```

Usage:

```python
with database_transaction("app.db") as connection:
    connection.execute(
        "INSERT INTO logs(message) VALUES (?)",
        ("User logged in",),
    )
```

If the operation succeeds, the transaction commits. If an exception occurs, it rolls back and re-raises the exception.

This helper is illustrative. Production transaction management should account for nested transactions, connection pooling, database-specific behavior, and error handling.

## 7. Parameterized Queries

Always use parameterized queries when values come from variables or users.

Correct:

```python
user_id = 10

connection.execute(
    "SELECT * FROM users WHERE user_id = ?",
    (user_id,),
)
```

Avoid:

```python
query = (
    "SELECT * FROM users WHERE user_id = "
    + str(user_id)
)
```

Parameterized queries help prevent SQL injection and handle values correctly.

## 8. Connection Management with SQLAlchemy

SQLAlchemy provides engine and connection abstractions.

```python
from sqlalchemy import create_engine, text

engine = create_engine(
    "sqlite:///app.db"
)

with engine.connect() as connection:
    result = connection.execute(text("SELECT 1"))
    print(result.scalar())
```

When the block ends, the connection is returned to the pool managed by the engine, rather than necessarily closing the underlying physical connection.

For transactional work:

```python
from sqlalchemy import text

with engine.begin() as connection:
    connection.execute(
        text(
            "INSERT INTO logs(message) VALUES (:message)"
        ),
        {"message": "Application started"},
    )
```

The transaction commits on success and rolls back when an exception escapes the block.

## 9. Common Mistakes

1. Forgetting to close a connection.
2. Assuming transaction completion always closes the connection.
3. Catching an exception but failing to roll back.
4. Using string concatenation to build SQL with user input.
5. Keeping a transaction open during slow network requests.
6. Creating a new connection for every operation when pooling would be beneficial.
7. Sharing a connection across concurrent tasks without understanding driver constraints.

## 10. Interview Questions

### Q1. What is a context manager?

An object that manages setup and cleanup around a block of code, typically using `with`.

### Q2. What is the difference between closing a connection and committing a transaction?

Closing releases a connection resource. Committing makes the transaction's changes durable according to the database's guarantees.

### Q3. What does `finally` do?

It runs during normal control flow and exception propagation, making it useful for cleanup.

### Q4. Why use parameterized SQL?

It separates SQL structure from values and helps protect against SQL injection.

### Q5. What is the difference between SQLite and SQLAlchemy context managers?

SQLite's connection context manager handles transaction completion but does not close the connection. SQLAlchemy's `engine.connect()` context manager returns the connection to its pool when the block ends.

## 11. Practice Tasks

1. Open and close a SQLite connection safely.
2. Use `try`, `except`, and `finally` for database cleanup.
3. Write a custom context manager using `contextlib.contextmanager`.
4. Demonstrate transaction commit and rollback.
5. Execute a parameterized query.
6. Compare raw SQLite connection handling with SQLAlchemy.
7. Explain connection leaks and how to prevent them.

## Key Takeaways

- Separate connection lifecycle management from transaction management.
- Use context managers and `finally` blocks for reliable cleanup.
- Roll back failed transactions when necessary.
- Parameterize SQL queries.
- Understand whether a library closes a connection or returns it to a pool.
