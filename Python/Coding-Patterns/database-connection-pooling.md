
# Database Connection Pooling

## 1. What Is a Database Connection?

A database connection allows an application to communicate with a database.

For example, a Python application may connect to PostgreSQL or MySQL to:
- Retrieve user information
- Insert orders
- Update inventory
- Run reports

Creating a new connection can involve network communication, authentication, and session setup. Repeating this process for every query can add overhead.

## 2. What Is Connection Pooling?

**Connection pooling** maintains a reusable collection of database connections.

Instead of creating a new connection for every request, an application borrows a connection from the pool, performs its database work, and returns the connection for reuse.

Typical lifecycle:

1. The application requests a connection.
2. The pool provides an available connection.
3. The application executes queries.
4. The application commits or rolls back its transaction as appropriate.
5. The connection is returned to the pool.

Returning a connection usually means releasing it back to the pool, not physically closing the underlying database connection.

## 3. Why Is Connection Pooling Important?

Benefits include:

- Reduced connection-creation overhead
- Better reuse of established connections
- Controlled numbers of concurrent database connections
- Improved performance for applications handling repeated requests
- More predictable use of database resources

However, pooling does not automatically make every query faster. Poor queries, locks, and database bottlenecks still need attention.

## 4. How Does a Connection Pool Work?

Imagine a pool configured with five connections.

- Request A borrows connection 1.
- Request B borrows connection 2.
- Request C borrows connection 3.
- When A finishes, connection 1 becomes available again.
- A later request can reuse connection 1.

If all five connections are busy, another request may have to wait until a connection becomes available.

The pool may reject or time out requests if its limits are reached.

## 5. Important Pool Configuration

### Pool Size

The number of connections the pool tries to maintain or make available, depending on the library.

### Maximum Pool Size

The maximum number of connections the pool can create concurrently.

### Connection Timeout

How long a request waits to obtain a connection before failing.

### Idle Timeout

How long an unused connection can remain in the pool before being closed, depending on the implementation.

### Connection Lifetime

How long a connection may be reused before the pool replaces it.

### Health Checks

Mechanisms for detecting connections that are no longer usable.

Configuration names and exact behavior vary by library.

## 6. Connection Pooling in Python with SQLite

Python's standard `sqlite3` module does not provide a conventional configurable connection pool by itself. For a small demonstration, we can use a queue to reuse SQLite connections.

```python
import queue
import sqlite3


class SQLiteConnectionPool:
    def __init__(self, database, pool_size=3):
        self.connections = queue.Queue(maxsize=pool_size)

        for _ in range(pool_size):
            connection = sqlite3.connect(
                database,
                check_same_thread=False,
                timeout=5,
            )
            self.connections.put(connection)

    def acquire(self, timeout=5):
        return self.connections.get(timeout=timeout)

    def release(self, connection):
        self.connections.put(connection)

    def close_all(self):
        while not self.connections.empty():
            connection = self.connections.get_nowait()
            connection.close()


pool = SQLiteConnectionPool("example.db", pool_size=2)

connection = pool.acquire()

try:
    connection.execute(
        "CREATE TABLE IF NOT EXISTS notes "
        "(id INTEGER PRIMARY KEY, message TEXT)"
    )

    connection.execute(
        "INSERT INTO notes (message) VALUES (?)",
        ("Connection pooling example",),
    )

    connection.commit()

except sqlite3.Error:
    connection.rollback()
    raise

finally:
    pool.release(connection)
    pool.close_all()
```

This is a learning demonstration, not a production-ready pool.

A production pool also needs reliable shutdown coordination, connection health checks, transaction-state handling, and protection against closing connections while other workers are using them.

SQLite also has different concurrency characteristics from client-server databases such as PostgreSQL.

## 7. Connection Pooling with SQLAlchemy

SQLAlchemy provides connection pooling for supported database engines.

Install it:

```bash
pip install sqlalchemy
```

Example using SQLite:

```python
from sqlalchemy import create_engine, text

engine = create_engine(
    "sqlite:///example.db",
    pool_size=5,
    max_overflow=2,
    pool_timeout=30,
)

with engine.connect() as connection:
    result = connection.execute(
        text("SELECT 1")
    )
    print(result.scalar())
```

This demonstrates SQLAlchemy's connection-management API. SQLite has special pooling behavior depending on its database URL and configuration, so these settings should not be assumed to behave identically for every SQLite setup.

For a client-server database such as PostgreSQL, the pool settings can be useful for controlling connection concurrency.

Always close or release checked-out connections. The context manager handles this for the example.

## 8. Connection Pooling with PostgreSQL

A common Python driver is Psycopg.

Install Psycopg with its binary package:

```bash
pip install "psycopg[binary,pool]"
```

Example using Psycopg 3's pool API:

```python
from psycopg_pool import ConnectionPool

pool = ConnectionPool(
    conninfo=(
        "host=localhost "
        "dbname=mydb "
        "user=myuser "
        "password=mypassword"
    ),
    min_size=1,
    max_size=5,
    open=True,
)

try:
    with pool.connection() as connection:
        with connection.cursor() as cursor:
            cursor.execute("SELECT %s", (42,))
            print(cursor.fetchone())
finally:
    pool.close()
```

Replace the connection settings with your own database configuration.

For real applications, store credentials in environment variables or a secret manager rather than committing them to GitHub.

The pool context manager manages connection checkout and return. Transaction handling depends on the driver's transaction configuration and the context manager's behavior.

## 9. Pool Size and Database Limits

A pool that is too small can cause application requests to wait for connections.

A pool that is too large can overload the database with too many concurrent connections.

For example, suppose a service runs four application workers, each configured for a maximum of 20 database connections.

The theoretical application-side maximum is:

`4 × 20 = 80 connections`

If several services use the same database, calculate their combined connection limits as well. Include background workers, administrative connections, and other applications.

Do not select a pool size using a universal formula. Measure workload, concurrency, database capacity, and query latency.

## 10. Common Connection Pooling Problems

### Connection Leaks

An application checks out connections but does not return them.

**Solution:** Use context managers or `try/finally` blocks.

### Pool Exhaustion

All available connections are in use.

**Solution:** Investigate slow queries, long transactions, leaked connections, and pool capacity.

### Stale Connections

A connection becomes unusable after a network interruption or server restart.

**Solution:** Use supported health checks, recycling, and appropriate error handling.

### Long-Running Transactions

Connections remain checked out while transactions wait or perform unrelated work.

**Solution:** Keep transactions short and avoid non-database work while holding a connection unnecessarily.

### Too Many Connections

The application opens more connections than the database can handle efficiently.

**Solution:** Set sensible limits and consider a shared external pooler when appropriate.

## 11. Connection Pooling vs. Opening a New Connection

| Feature | New Connection Per Request | Connection Pooling |
|---|---|---|
| Connection setup | Repeated | Usually reused |
| Setup overhead | Potentially higher | Often lower |
| Resource control | More difficult | Centralized within the pool |
| Request behavior | May create connection storms | Can limit concurrency |
| Complexity | Simpler initially | Requires pool configuration and lifecycle management |

A pool adds complexity, but it is often useful in services that handle frequent database requests.

## 12. Interview Questions

1. What is database connection pooling?
2. Why is connection creation expensive?
3. What happens when all connections in a pool are busy?
4. What is pool exhaustion?
5. What is a connection leak?
6. How do minimum and maximum pool sizes differ?
7. Why can an excessively large pool hurt performance?
8. What is the difference between a connection pool and a database proxy?
9. How should database credentials be managed?
10. How would you investigate requests waiting too long for a connection?

## 13. Practice Tasks

1. Explain the lifecycle of a pooled connection.
2. Build a small demonstration using a supported pooling library.
3. Configure a maximum pool size and connection timeout.
4. Simulate multiple workers competing for a small pool.
5. Explain how a connection leak can exhaust the pool.
6. Estimate the combined connection limit across multiple application workers.
7. Document how you would monitor pool utilization and wait times.

## Key Takeaways

- Connection pooling reuses database connections.
- A connection should always be returned after use.
- Pool size must fit application concurrency and database capacity.
- Leaks, long transactions, and slow queries can exhaust a pool.
- Use a maintained library for production pooling rather than a simplistic custom implementation.
- Never commit real database passwords or connection secrets to a public repository.
