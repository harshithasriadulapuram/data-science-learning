
# Database Query Optimization

## 1. What Is Query Optimization?

Query optimization means improving SQL queries so they execute efficiently while returning the correct results.

The goals are to reduce:
- Query execution time
- Unnecessary disk reads
- CPU and memory usage
- Network data transfer
- Database load under concurrent requests

A query that returns the correct result can still be inefficient.

## 2. Avoid SELECT *

Instead of retrieving every column:

```sql
SELECT *
FROM employees;
```

Select only the columns you need:

```sql
SELECT employee_id, employee_name, salary
FROM employees;
```

Benefits:
- Less data transferred
- Less memory used by the application
- Potentially fewer disk reads
- Better use of covering indexes when applicable

## 3. Use Indexes Effectively

An index helps the database locate rows without necessarily scanning the entire table.

```sql
CREATE INDEX idx_employees_department
ON employees(department_id);
```

This index may help queries such as:

```sql
SELECT employee_id, employee_name
FROM employees
WHERE department_id = 10;
```

However, indexes also consume storage and can slow down inserts, updates, and deletes because the indexes may need maintenance.

An index is not automatically useful for every query.

## 4. Avoid Functions on Indexed Columns When Possible

Consider:

```sql
SELECT *
FROM employees
WHERE YEAR(hire_date) = 2025;
```

Applying a function to an indexed column can prevent efficient use of a normal index in some databases.

A range condition is often preferable:

```sql
SELECT employee_id, hire_date
FROM employees
WHERE hire_date >= '2025-01-01'
  AND hire_date < '2026-01-01';
```

This form can allow a range scan on an index over `hire_date`.

Date literal syntax and date types vary by database.

## 5. Understand Composite Indexes

A composite index contains multiple columns.

```sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, order_date);
```

This index is often useful for queries filtering by:
- `customer_id`
- `customer_id` and `order_date`

It may not be as useful for a query filtering only by `order_date`.

This is known as the **leftmost-prefix principle** in database systems such as MySQL.

Choose index column order based on query patterns, selectivity, sorting requirements, and the database engine.

## 6. Avoid Leading Wildcards When Appropriate

A search such as:

```sql
SELECT *
FROM employees
WHERE employee_name LIKE '%har';
```

usually cannot use a conventional B-tree index for an efficient prefix lookup.

A prefix search may be more index-friendly:

```sql
SELECT employee_id, employee_name
FROM employees
WHERE employee_name LIKE 'Har%';
```

Actual behavior depends on the database, collation, and query plan. Full-text or specialized indexes may help with substring searches.

## 7. Optimize JOIN Operations

Use joins that express the relationship you need.

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
JOIN departments AS d
    ON e.department_id = d.department_id;
```

Useful practices:
- Ensure join conditions are correct.
- Index columns when the workload benefits from it.
- Avoid accidental many-to-many joins that multiply rows.
- Return only required columns.
- Check whether filters can reduce the amount of data processed.

The database optimizer chooses the join algorithm and execution order. Writing a query in a particular textual order does not guarantee that execution order.

## 8. Filter Data Early

Suppose you need orders from one customer.

```sql
SELECT
    order_id,
    customer_id,
    order_date
FROM orders
WHERE customer_id = 101;
```

Filtering reduces the result set. The optimizer may push filters through parts of the query when it is valid and beneficial.

Do not add unnecessary subqueries simply to force filtering earlier.

## 9. Use EXISTS for Existence Checks

Suppose you need customers who have at least one order.

```sql
SELECT c.customer_id, c.customer_name
FROM customers AS c
WHERE EXISTS (
    SELECT 1
    FROM orders AS o
    WHERE o.customer_id = c.customer_id
);
```

`EXISTS` expresses the requirement clearly: only check whether a matching row exists.

An equivalent join may also work, but it can produce duplicate customer rows when a customer has multiple orders unless duplicates are handled.

Neither `EXISTS` nor `JOIN` is universally faster. Check the execution plan.

## 10. Avoid Unnecessary DISTINCT

```sql
SELECT DISTINCT department_id
FROM employees;
```

`DISTINCT` removes duplicate result rows and may require sorting or hashing.

Use it when unique results are required, not merely to hide duplicates caused by an incorrect join.

## 11. Pagination: OFFSET vs. Keyset

### OFFSET Pagination

```sql
SELECT order_id, order_date
FROM orders
ORDER BY order_id
LIMIT 20 OFFSET 10000;
```

Large offsets can be expensive because the database may still need to process or skip many rows.

### Keyset Pagination

```sql
SELECT order_id, order_date
FROM orders
WHERE order_id > 10000
ORDER BY order_id
LIMIT 20;
```

This can be more efficient for sequential navigation when the ordering column is indexed and stable.

The example assumes `order_id` is unique and increasing. Real applications should use a cursor that matches the chosen sort order.

## 12. Use EXPLAIN

`EXPLAIN` shows the database's planned execution strategy.

```sql
EXPLAIN
SELECT employee_id, employee_name
FROM employees
WHERE department_id = 10;
```

Some databases support execution-analysis commands such as `EXPLAIN ANALYZE`, which can execute the query and report actual runtime information.

Check for:
- Full table scans on large tables
- Index scans and range scans
- Estimated versus actual row counts
- Expensive joins
- Sorts and temporary operations
- Large differences between estimated and actual execution costs

A full table scan is not always bad. It may be the best plan for a small table or a query returning most of the rows.

## 13. Example: Before and After

Suppose you need employees hired during 2025.

### Less Index-Friendly Form

```sql
SELECT employee_id, employee_name
FROM employees
WHERE YEAR(hire_date) = 2025;
```

### Range-Based Form

```sql
SELECT employee_id, employee_name
FROM employees
WHERE hire_date >= '2025-01-01'
  AND hire_date < '2026-01-01';
```

An index on `hire_date` may help the range-based query. Confirm the benefit using the execution plan and representative data.

## 14. Transactions and Batch Operations

Executing thousands of individual database calls can create unnecessary network overhead.

When appropriate, use parameterized batch operations or bulk-loading features supported by your database driver.

Also:
- Keep transactions reasonably short.
- Avoid holding locks while performing unrelated work.
- Use connection pooling where appropriate.
- Choose batch sizes based on testing and operational constraints.

Large batches are not always better; they can increase lock duration and memory usage.

## 15. A Practical Optimization Workflow

1. Identify the slow query from monitoring or logs.
2. Measure its baseline latency.
3. Inspect the execution plan.
4. Check indexes, joins, filters, and returned columns.
5. Make one targeted change.
6. Compare execution time and resource use.
7. Test correctness under realistic data volumes.
8. Monitor performance after deployment.

Never assume that a query is faster simply because it looks shorter.

## 16. Interview Questions

1. What is query optimization?
2. Why should you avoid `SELECT *`?
3. How do indexes improve query performance?
4. What is a composite index?
5. Explain the leftmost-prefix principle.
6. Why can functions on indexed columns reduce index usage?
7. What is the difference between `EXISTS` and `JOIN`?
8. Why can large OFFSET values be expensive?
9. What does `EXPLAIN` do?
10. Is a full table scan always inefficient?
11. How can unnecessary `DISTINCT` affect performance?
12. How would you investigate a query that suddenly became slow?

## 17. Practice Tasks

1. Create an index for a frequently filtered column and inspect the query plan.
2. Rewrite a date-filter query using a half-open range.
3. Compare OFFSET and keyset pagination.
4. Write a query using `EXISTS` to find customers with orders.
5. Inspect a join that produces duplicate rows and fix its logic.
6. Record the execution plan before and after an optimization.
7. Explain why adding an index to every column is not a good strategy.

## Key Takeaways

- Optimize based on measurements, not assumptions.
- Indexes can accelerate reads but add storage and write costs.
- Composite index order matters.
- `EXPLAIN` helps reveal how a query will execute.
- Query correctness must be preserved during optimization.
- Always validate improvements with representative data.
