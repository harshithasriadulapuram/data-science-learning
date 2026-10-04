# Database Connection Health Checks in Python

## 1. What Is a Database Health Check?

A database health check determines whether an application can communicate with its database and perform a simple operation.

Health checks help applications and monitoring systems detect database connectivity problems.

Common checks include:

* Can the application establish a connection?
* Can it execute a simple query?
* Does the query complete within a reasonable time?
* Is the database service available?

A successful health check does not guarantee that every database operation will succeed.

## 2. Liveness vs. Readiness

### Liveness Check

Answers: Is the application process running and able to continue?

A liveness check should generally avoid depending on the database. Otherwise, a temporary database outage could cause unnecessary application restarts.

### Readiness Check

Answers: Is the application ready to serve requests?

Readiness may include checking whether required database services are accessible.

| Check             | Purpose                                  | Database dependency       |
| ----------------- | ---------------------------------------- | ------------------------- |
| Liveness          | Detect an unhealthy application process  | Usually no                |
| Readiness         | Determine whether requests can be served | Often yes                 |
| Deep health check | Diagnose specific dependencies           | Yes, potentially multiple |

## 3. Basic SQLite Health Check

```python
import sqlite3

def database_is_healthy(db_path="app.db"):
    try:
        with sqlite3.connect(db_path, timeout=2) as connection:
            connection.execute("SELECT 1")
        return True
    except sqlite3.Error:
        return False

print(database_is_healthy())
```

`SELECT 1` is a lightweight query that checks whether a connection can execute SQL.

For SQLite, the connection context manager manages transaction completion or rollback; it does not itself close the connection. Use `contextlib.closing` when you want explicit automatic closure.

## 4. Health Check With Explicit Resource Cleanup

```python
import sqlite3
from contextlib import closing

def database_is_healthy(db_path="app.db"):
    try:
        with closing(
            sqlite3.connect(db_path, timeout=2)
        ) as connection:
            connection.execute("SELECT 1")
            return True
    except sqlite3.Error:
        return False
```

The connection is closed when the `with` block exits, including when a database exception occurs.

## 5. Returning Useful Health Information

A Boolean result is useful for simple checks. Monitoring systems may need a status and a reason.

```python
import sqlite3
from contextlib import closing

def database_health(db_path="app.db"):
    try:
        with closing(
            sqlite3.connect(db_path, timeout=2)
        ) as connection:
            connection.execute("SELECT 1")

        return {
            "status": "healthy",
            "dependency": "database"
        }

    except sqlite3.Error:
        return {
            "status": "unhealthy",
            "dependency": "database"
        }
```

Avoid returning raw database errors, credentials, connection strings, or internal infrastructure details from a public health endpoint.

## 6. Health Checks in FastAPI

```python
import sqlite3
from contextlib import closing

from fastapi import FastAPI, HTTPException

app = FastAPI()

def database_is_healthy():
    try:
        with closing(
            sqlite3.connect("app.db", timeout=2)
        ) as connection:
            connection.execute("SELECT 1")
        return True
    except sqlite3.Error:
        return False

@app.get("/health/live")
def liveness():
    return {"status": "alive"}

@app.get("/health/ready")
def readiness():
    if not database_is_healthy():
        raise HTTPException(
            status_code=503,
            detail="Required dependency unavailable"
        )

    return {"status": "ready"}
```

The liveness endpoint checks that the application can respond. The readiness endpoint verifies database access and returns HTTP 503 when the required dependency is unavailable.

This example uses SQLite for learning. Production applications should use the health-check approach appropriate to their database driver and connection pool.

## 7. Common Mistakes

* Running expensive queries in every health check.
* Checking the database too frequently.
* Exposing sensitive error details.
* Making liveness depend on every external service.
* Treating a successful health check as a guarantee that future queries will succeed.
* Forgetting to close connections.
* Creating excessive database load through aggressive monitoring.

## 8. Interview Questions

1. What is a database health check?
2. What is the difference between liveness and readiness?
3. Why is `SELECT 1` commonly used?
4. Why should health checks be lightweight?
5. Why might a readiness endpoint return HTTP 503?
6. Should liveness always check the database?
7. What information should a public health endpoint avoid exposing?
8. Why can a database pass a health check and still fail on a subsequent request?

## 9. Practice Tasks

1. Write a SQLite health-check function that returns `True` or `False`.
2. Add explicit connection cleanup.
3. Create separate liveness and readiness endpoints.
4. Test the readiness endpoint when the database is available and unavailable.
5. Explain why a health check should not execute a large reporting query.
