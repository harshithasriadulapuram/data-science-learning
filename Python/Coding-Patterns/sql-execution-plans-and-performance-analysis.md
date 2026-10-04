
# SQL Execution Plans and Performance Analysis

## 1. What Is a Query Execution Plan?

A query execution plan describes how a database intends to retrieve or modify data for a SQL statement.

The database optimizer evaluates possible strategies and chooses a plan based on factors such as:
- Available indexes
- Table sizes and statistics
- Filter conditions
- Join conditions
- Estimated number of rows
- Database-specific cost estimates

Understanding execution plans helps identify why a query is slow.

## 2. EXPLAIN

`EXPLAIN` displays the database's planned execution strategy.

```sql
EXPLAIN
SELECT employee_id, employee_name
FROM employees
WHERE department_id = 10;
```

Depending on the database, the output may include:
- Access method
- Index selection
- Estimated rows
- Estimated cost
- Join order
- Sort or aggregation operations

The exact output format differs across MySQL, PostgreSQL, SQLite, SQL Server, and other database systems.

## 3. EXPLAIN ANALYZE

Some databases support `EXPLAIN ANALYZE` or an equivalent command that executes a query and reports actual execution statistics.

For example, PostgreSQL supports:

```sql
EXPLAIN ANALYZE
SELECT employee_id, employee_name
FROM employees
WHERE department_id = 10;
```

Actual execution plans can help compare estimated and observed behavior.

**Warning:** Because `EXPLAIN ANALYZE` executes the statement, use caution with `UPDATE`, `DELETE`, and other statements that change data. Consult your database's documentation before analyzing write operations.

## 4. Table Scan vs. Index Scan

### Table Scan

A table scan examines table rows to find matching records.

It may be reasonable when:
- The table is small.
- Most rows must be returned.
- The optimizer determines that scanning is cheaper than using an index.

### Index Scan or Index Lookup

An index can help locate matching records without examining every table row.

It is often useful when:
- The filter is selective.
- The index matches the query conditions.
- The cost of retrieving matching rows is lower than scanning the table.

An index scan is not automatically faster than a table scan.

## 5. Estimated Rows vs. Actual Rows

The optimizer estimates how many rows an operation will process.

For example:

- Estimated rows: 100
- Actual rows: 100,000

A large difference can indicate inaccurate statistics or data distributions that the optimizer has difficulty estimating.

Potential remedies include:
- Refreshing database statistics.
- Reviewing filter selectivity.
- Checking correlated columns.
- Evaluating indexes.
- Rewriting the query when appropriate.

Do not assume that adding an index alone will solve a cardinality-estimation problem.

## 6. Understanding Query Cost

A database may assign estimated costs to operations.

These costs help the optimizer compare plans. They are not necessarily milliseconds or a direct measurement of wall-clock time.

For example, a plan with a higher estimated cost may still run faster in a particular environment because of caching, concurrency, or estimation errors.

Use actual timings and resource measurements alongside plan estimates.

## 7. Analyze a Slow Filter Query

Suppose the query is:

```sql
SELECT employee_id, employee_name
FROM employees
WHERE department_id = 10;
```

First, inspect the plan:

```sql
EXPLAIN
SELECT employee_id, employee_name
FROM employees
WHERE department_id = 10;
```

If the query frequently filters by `department_id`, test an index:

```sql
CREATE INDEX idx_employees_department
ON employees(department_id);
```

Then inspect the plan again.

Compare:
- Estimated rows
- Chosen access method
- Actual elapsed time, when available
- I/O and CPU usage
- Performance under representative workloads

An index may not help if most employees belong to department 10.

## 8. Analyze JOIN Performance

Consider:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
JOIN departments AS d
    ON e.department_id = d.department_id;
```

Investigate:
- Whether the join condition uses appropriate keys.
- Whether the join creates an unexpectedly large number of rows.
- Whether indexes help the workload.
- Whether table statistics are current.
- Whether the chosen join strategy fits the table sizes.

Common join algorithms include:
- Nested-loop join
- Hash join
- Merge join

Availability and plan selection depend on the database engine.

### Nested-Loop Join

Often useful when one input is small and the other can be searched efficiently for each row.

### Hash Join

Builds a hash table from one input and probes it with rows from the other. Often useful for equality joins on larger inputs.

### Merge Join

Processes inputs in join-key order. It can be efficient when suitable ordering is already available or can be obtained economically.

## 9. Sorts and Temporary Operations

A query may require sorting:

```sql
SELECT employee_id, salary
FROM employees
ORDER BY salary DESC;
```

An index may help with ordering in suitable cases, but the result depends on the index definition and the execution plan.

Large sorts can consume memory and may spill to disk.

Investigate:
- The number of rows being sorted
- Whether a compatible index exists
- Whether the sort is necessary
- Whether the query returns more data than required

## 10. Query Plan Optimization Workflow

Use a consistent process:

1. Identify a slow query using monitoring or query logs.
2. Record its baseline latency.
3. Inspect its execution plan.
4. Compare estimated and actual row counts.
5. Look for expensive scans, joins, sorts, and repeated operations.
6. Form one specific optimization hypothesis.
7. Test the change on representative data.
8. Compare correctness, latency, CPU, and I/O.
9. Test under realistic concurrency.
10. Monitor the query after deployment.

Change one important factor at a time when possible. This makes results easier to interpret.

## 11. Common Mistakes

- Assuming every table scan is bad.
- Assuming an index always improves performance.
- Treating estimated cost as actual execution time.
- Ignoring inaccurate row estimates.
- Comparing timings measured under very different cache conditions.
- Running an analyzing command on a write statement without understanding its effects.
- Optimizing a query before measuring the actual bottleneck.

## 12. Interview Questions

1. What is a query execution plan?
2. What is the difference between `EXPLAIN` and `EXPLAIN ANALYZE`?
3. What is a table scan?
4. When can an index scan be slower than a table scan?
5. What are estimated rows and actual rows?
6. What is cardinality estimation?
7. Explain nested-loop, hash, and merge joins.
8. Why might a query perform poorly despite having indexes?
9. What causes a sort operation to spill to disk?
10. How would you investigate a query that became slow after a data increase?

## 13. Practice Tasks

1. Run `EXPLAIN` on a query that filters employees by department.
2. Compare the plan before and after adding an index.
3. Inspect a join between customers and orders.
4. Find a query where the estimated row count differs significantly from the actual row count.
5. Compare a query with and without an unnecessary sort.
6. Write a short report explaining the bottleneck, proposed fix, and measured result.

## Key Takeaways

- Execution plans reveal how databases process queries.
- Estimated costs are not the same as elapsed time.
- Actual row counts help expose estimation problems.
- Indexes and join strategies should be evaluated in context.
- Measure performance before and after every meaningful optimization.
