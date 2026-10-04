# Database Connection Leaks in Python

## 1. What Is a Connection Leak?

A database connection leak occurs when an application opens a connection but fails to release it when the connection is no longer needed.

Over time, leaked connections can exhaust the database's connection limit or the application's connection pool.

### Example of a potential leak

```python
import sqlite3

def get_users():
    connection = sqlite3.connect("app.db")
    cursor = connection.cursor()

    cursor.execute("SELECT * FROM users")
    return cursor.fetchall()
```

The function never explicitly closes the connection. Repeated calls can leave connections open longer than intended.

## 2. Use `try` and `finally`

A `finally` block runs whether the operation succeeds or raises an exception.

```python
import sqlite3

def get_users():
    connection = sqlite3.connect("app.db")

    try:
        cursor = connection.cursor()
        cursor.execute("SELECT * FROM users")
        return cursor.fetchall()
    finally:
        connection.close()
```

This ensures the connection is closed on both success and failure.

## 3. Use `contextlib.closing`

Python's `sqlite3.Connection` context manager handles transaction commit and rollback, but does not automatically close the connection.

Use `closing` when you want explicit cleanup:

```python
import sqlite3
from contextlib import closing

def get_users():
    with closing(sqlite3.connect("app.db")) as connection:
        cursor = connection.cursor()
        cursor.execute("SELECT * FROM users")
        return cursor.fetchall()
```

The connection closes when execution leaves the `with` block.

## 4. SQLAlchemy Session Cleanup

When using SQLAlchemy ORM, sessions should also be closed reliably.

```python
from sqlalchemy import create_engine, text
from sqlalchemy.orm import sessionmaker

engine = create_engine("sqlite:///app.db")
SessionLocal = sessionmaker(bind=engine)

def get_user_count():
    with SessionLocal() as session:
        result = session.execute(
            text("SELECT COUNT(*) FROM users")
        )
        return result.scalar_one()
```

The session context manager closes the session when the block exits. Transaction handling should be explicit when the operation writes data.

For a write transaction:

```python
def create_user(name):
    with SessionLocal.begin() as session:
        session.execute(
            text("INSERT INTO users (name) VALUES (:name)"),
            {"name": name}
        )
```

The transaction commits on success and rolls back if an exception occurs.

## 5. Why Connection Leaks Are Dangerous

* **Pool exhaustion:** Requests wait because all available connections are occupied.
* **Increased latency:** Database operations become slower while waiting for connections.
* **Resource exhaustion:** The database may reach its connection limit.
* **Application failures:** New requests may fail to obtain connections.
* **Hard-to-debug issues:** The application may work initially and fail only under sustained load.

## 6. Connection Leak vs. Connection Pool Exhaustion

| Connection leak                       | Connection pool exhaustion                |
| ------------------------------------- | ----------------------------------------- |
| Connections are not released properly | No connections are available for new work |
| A possible cause                      | A possible symptom                        |
| Can occur even without a pool         | Can occur even without leaks              |

Pool exhaustion can also result from legitimately long-running queries or a pool that is too small.

## 7. How to Prevent Connection Leaks

1. Use `try/finally` or appropriate context managers.
2. Close database cursors and connections when required by the driver.
3. Close ORM sessions reliably.
4. Configure connection pool limits and checkout timeouts.
5. Avoid holding connections while performing unrelated work.
6. Monitor active connections and pool checkout wait times.
7. Investigate long-running transactions.
8. Add tests for cleanup when exceptions occur.

## 8. Interview Questions

1. What is a database connection leak?
2. How does a connection leak affect application performance?
3. What is the difference between a connection leak and pool exhaustion?
4. Does a SQLite connection's `with` statement automatically close the connection?
5. How does `try/finally` prevent leaks?
6. How should SQLAlchemy sessions be managed?
7. What metrics help diagnose connection leaks?
8. Can a long-running query cause pool exhaustion without a leak?

## 9. Practice Tasks

1. Fix the leaking `get_users()` function using `try/finally`.
2. Rewrite it using `contextlib.closing`.
3. Create a SQLAlchemy session example with reliable cleanup.
4. Simulate an exception and explain why cleanup still runs.
5. Describe how you would investigate pool exhaustion in production.
