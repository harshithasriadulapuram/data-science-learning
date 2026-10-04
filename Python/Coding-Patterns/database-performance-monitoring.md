# Database Performance Monitoring in Python

## 1. What Is Database Performance Monitoring?

Database performance monitoring is the process of measuring and analyzing database behavior to identify slow queries, resource bottlenecks, and connection problems.

Important metrics include:

* Query execution time
* Query throughput
* Error rate
* Active database connections
* Connection pool wait time
* Lock waits and deadlocks
* CPU, memory, and disk utilization

Monitoring helps identify problems before they cause widespread application failures.

## 2. Measure Query Execution Time

Python's `time.perf_counter()` is useful for measuring elapsed time.

```python
import sqlite3
import time

def measure_query(db_path="app.db"):
    connection = sqlite3.connect(db_path)

    try:
        start = time.perf_counter()

        connection.execute("SELECT 1").fetchone()

        elapsed = time.perf_counter() - start
        return elapsed
    finally:
        connection.close()

duration = measure_query()
print(f"Query elapsed time: {duration:.6f} seconds")
```

This measures the elapsed time for the operation, including some Python and database communication overhead. It is not a pure measurement of the database engine's internal execution time.

## 3. Create a Reusable Query Timer

```python
import time

def execute_with_timing(connection, sql, parameters=()):
    start = time.perf_counter()

    try:
        return connection.execute(sql, parameters)
    finally:
        elapsed = time.perf_counter() - start

        if elapsed > 0.5:
            print(f"Slow database operation: {elapsed:.3f}s")
```

Example:

```python
import sqlite3

with sqlite3.connect("app.db") as connection:
    cursor = execute_with_timing(
        connection,
        "SELECT * FROM users WHERE id = ?",
        (1,)
    )
    print(cursor.fetchone())
```

**Important:** This measures the time spent executing the statement, not necessarily the time required to fetch every row. For accurate end-to-end measurements, include fetching and any relevant processing.

## 4. Record Metrics Instead of Printing Everything

Production applications should generally use structured logging and metrics rather than printing every query.

Useful measurements include:

| Metric             | What it tells you                                  |
| ------------------ | -------------------------------------------------- |
| Average latency    | Typical query response time                        |
| p95 latency        | 95% of measured operations finish within this time |
| p99 latency        | 99% finish within this time                        |
| Queries per second | Query throughput                                   |
| Error rate         | Frequency of failed operations                     |
| Pool wait time     | Time spent waiting for a connection                |
| Lock wait time     | Time spent waiting for database locks              |

Percentiles are especially valuable because averages can hide a small number of extremely slow requests.

## 5. Find Slow Queries

A practical investigation process:

1. Identify the slow endpoint or operation.
2. Capture the relevant query and its parameters safely.
3. Measure execution and fetch time.
4. Inspect the query execution plan.
5. Check indexes and join conditions.
6. Look for excessive row retrieval or repeated queries.
7. Check lock waits and connection pool behavior.
8. Apply one change at a time and measure again.

Do not log passwords, access tokens, or sensitive personal data when collecting diagnostics.

## 6. Use SQLite's Query Plan

SQLite supports `EXPLAIN QUERY PLAN`.

```python
import sqlite3

connection = sqlite3.connect("app.db")

try:
    plan = connection.execute(
        """
        EXPLAIN QUERY PLAN
        SELECT * FROM users WHERE email = ?
        """,
        ("person@example.com",)
    ).fetchall()

    for row in plan:
        print(row)
finally:
    connection.close()
```

The plan helps you understand whether SQLite is scanning a table or using an index. Exact output depends on the schema, available indexes, and SQLite version.

## 7. Compare Performance Before and After Optimization

A simple benchmark:

```python
import statistics
import time

def benchmark(operation, runs=10):
    durations = []

    for _ in range(runs):
        start = time.perf_counter()
        operation()
        durations.append(time.perf_counter() - start)

    return {
        "runs": runs,
        "mean_seconds": statistics.mean(durations),
        "median_seconds": statistics.median(durations),
        "min_seconds": min(durations),
        "max_seconds": max(durations),
    }
```

Use the same dataset, query parameters, and environment when comparing changes. For reliable results, account for warm-up effects, caching, and background activity.

## 8. Common Performance Problems

### Missing indexes

Queries may scan many rows unnecessarily.

### N+1 queries

The application runs one query to retrieve records and then an additional query for each record.

### Excessive data retrieval

Fetching every column or row when only a small subset is required increases work and data transfer.

### Long-running transactions

Transactions that remain open can hold locks or delay other operations.

### Connection pool contention

Requests wait for available connections because the pool is exhausted or connections are held too long.

### Inefficient joins

Poor join conditions or unsuitable indexes can increase query cost.

## 9. Production Monitoring Checklist

* Track query latency and error rates.
* Monitor p95 and p99 latency.
* Track connection pool utilization and wait time.
* Inspect slow-query logs.
* Monitor locks and deadlocks.
* Review query plans for expensive operations.
* Establish alerts based on service objectives.
* Compare metrics before and after optimizations.
* Protect sensitive information in logs.

## 10. Interview Questions

1. What is database performance monitoring?
2. Why are p95 and p99 latency useful?
3. How can you measure elapsed time in Python?
4. What is the difference between query execution time and total request latency?
5. How does `EXPLAIN QUERY PLAN` help?
6. What is connection pool contention?
7. How can you detect an N+1 query problem?
8. Why should benchmarks use consistent conditions?
9. Which database metrics would you monitor in production?
10. How would you investigate a sudden increase in query latency?

## 11. Practice Tasks

1. Measure the elapsed time of a SQLite query.
2. Extend the benchmark function to report p95 latency.
3. Compare a query before and after adding an appropriate index.
4. Explain the difference between query latency and throughput.
5. Design a monitoring dashboard for a Python API backed by a database.
