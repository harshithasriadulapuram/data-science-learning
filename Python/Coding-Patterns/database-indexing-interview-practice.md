
# Database Indexing — Interview Practice

## 1. What Is a Database Index?

A database index is a data structure that helps the database find rows without scanning the entire table.

Think of a book's index: instead of reading every page, you look up a topic and find the relevant page numbers.

Example table:

| id | name | city |
|---:|---|---|
| 1 | Anu | Hyderabad |
| 2 | Ravi | Chennai |
| 3 | Maya | Hyderabad |
| 4 | Kiran | Bengaluru |

Without a suitable index, the database may scan many or all rows to find users in Hyderabad.

An index on `city` can help locate matching rows more efficiently.

## 2. Why Are Indexes Important?

Indexes can:
- Speed up filtering.
- Improve join performance.
- Help with sorting and grouping in suitable queries.
- Support uniqueness constraints.
- Reduce the amount of data examined.

However, indexes consume storage and add work to inserts, updates, and deletes.

An index is not guaranteed to improve every query.

## 3. Create an Index in SQL

Create a sample table:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    city VARCHAR(100),
    age INT
);
```

Create an index on `city`:

```sql
CREATE INDEX idx_users_city
ON users(city);
```

Now query users from Hyderabad:

```sql
SELECT id, name, city
FROM users
WHERE city = 'Hyderabad';
```

The database optimizer may choose to use the index, depending on table size, data distribution, available indexes, and estimated query cost.

Inspect the query plan with the database's `EXPLAIN` command.

## 4. Common Index Types

Exact implementations and names vary between database systems.

### A. B-Tree Index

A balanced tree-based index used by many relational databases.

Often useful for:
- Equality comparisons.
- Range conditions.
- Ordered retrieval.
- Prefix matching in supported circumstances.

Example:

```sql
CREATE INDEX idx_users_age
ON users(age);
```

```sql
SELECT *
FROM users
WHERE age BETWEEN 20 AND 30;
```

### B. Hash Index

Uses a hash structure to locate entries.

Typically suited to equality lookups when supported by the database, but generally not range queries or ordered retrieval.

### C. Composite Index

An index containing multiple columns.

```sql
CREATE INDEX idx_users_city_age
ON users(city, age);
```

This can help queries filtering by `city`, or by both `city` and `age`.

The order of columns matters.

### D. Unique Index

Prevents duplicate values for the indexed key, subject to the database's rules for `NULL` values.

```sql
CREATE UNIQUE INDEX idx_users_email
ON users(email);
```

Useful when email addresses must be unique.

### E. Full-Text Index

Supports text-search functionality beyond ordinary equality matching.

Its behavior and query syntax depend on the database system.

## 5. Composite Index and the Leftmost Prefix

Consider:

```sql
CREATE INDEX idx_city_age_name
ON users(city, age, name);
```

This index may help queries such as:

```sql
SELECT *
FROM users
WHERE city = 'Hyderabad';
```

```sql
SELECT *
FROM users
WHERE city = 'Hyderabad'
  AND age = 25;
```

It may also help a query filtering by `city` and `age` while returning `name`.

But it is generally less useful for a query filtering only by `age`, because `age` is not the leading column.

This is commonly called the **leftmost-prefix principle** for B-tree composite indexes. Actual optimizer behavior depends on the database and query.

## 6. Indexes and Query Performance

Compare these queries:

```sql
SELECT *
FROM users
WHERE city = 'Hyderabad';
```

```sql
SELECT *
FROM users
WHERE LOWER(city) = 'hyderabad';
```

A regular index on `city` may not be usable for the second query in the same way, because the query applies a function to the column.

Depending on the database, an expression index, normalized column, or appropriate collation may help.

Avoid assuming an index will be used merely because one exists.

## 7. Covering Index

A covering index contains all the columns needed by a query, allowing some databases to answer the query using the index without fetching the corresponding table rows.

Example index:

```sql
CREATE INDEX idx_users_city_name
ON users(city, name);
```

Query:

```sql
SELECT name
FROM users
WHERE city = 'Hyderabad';
```

This index may cover the query.

Covering indexes can reduce table access, but overly wide indexes consume additional storage and increase write costs.

## 8. When Can Indexes Hurt?

Indexes have trade-offs.

- More disk and memory usage.
- Slower writes due to index maintenance.
- Additional maintenance overhead.
- Potentially poor performance when many rows match a condition.
- Unnecessary indexes can increase operational complexity.

For example, indexing a column with very few distinct values may not help every query. Whether it helps depends on selectivity, data distribution, and the optimizer.

Do not create indexes on every column automatically.

## 9. How to Analyze a Slow Query

Follow this process:

1. Identify the slow query.
2. Inspect its execution plan using `EXPLAIN`.
3. Check whether it scans too many rows.
4. Review join conditions and filter predicates.
5. Check existing indexes and their column order.
6. Consider data distribution and selectivity.
7. Create or modify an index only when justified.
8. Compare execution plans and timings before and after.
9. Test the impact on write performance.

For some databases, `EXPLAIN ANALYZE` executes the query and reports actual execution details. Use it carefully, especially for statements that modify data.

## 10. Practical SQL Exercise

Create a table:

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    status VARCHAR(30),
    order_date DATE,
    total_amount DECIMAL(10, 2)
);
```

### Task 1: Find a customer's orders

```sql
SELECT *
FROM orders
WHERE customer_id = 101;
```

Potential index:

```sql
CREATE INDEX idx_orders_customer
ON orders(customer_id);
```

### Task 2: Find a customer's recent orders

```sql
SELECT *
FROM orders
WHERE customer_id = 101
ORDER BY order_date DESC;
```

Potential composite index:

```sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, order_date);
```

Whether this index fully supports the desired ordering depends on the database and index capabilities.

### Task 3: Filter by status and date

```sql
SELECT *
FROM orders
WHERE status = 'SHIPPED'
  AND order_date >= '2026-01-01';
```

Investigate whether an index on `(status, order_date)` or another design helps this workload. Use the execution plan and representative data to decide.

## 11. Interview Questions

1. What is a database index?
2. Why can an index speed up a query?
3. What are the disadvantages of indexes?
4. Explain B-tree and hash indexes.
5. What is a composite index?
6. Explain the leftmost-prefix principle.
7. What is a covering index?
8. What is a unique index?
9. Why might a database ignore an index?
10. How can indexes affect write performance?
11. How do you investigate a slow SQL query?
12. How would you choose an index for a frequently executed query?

## 12. Practice Checklist

- [ ] Create a single-column index.
- [ ] Create a composite index.
- [ ] Compare query plans before and after indexing.
- [ ] Test equality and range queries.
- [ ] Investigate an index that is not being used.
- [ ] Measure the effect of indexes on inserts and updates.
- [ ] Explain the trade-offs of a covering index.

## Key Takeaway

Indexes are a performance tool, not a guarantee. Choose them based on actual query patterns, inspect execution plans, and balance faster reads against storage and write overhead.
