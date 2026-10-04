
# SQL Isolation Levels and Transaction Anomalies

## 1. What Is Transaction Isolation?

Transaction isolation determines how concurrent database transactions can see each other's changes.

It is one of the four ACID properties.

Isolation helps prevent transactions from interfering with one another in ways that produce incorrect results.

## 2. Dirty Read

A dirty read occurs when a transaction reads data written by another transaction that has not committed.

Example:

1. Transaction A changes an account balance from 1,000 to 500.
2. Transaction B reads the balance as 500.
3. Transaction A rolls back.
4. The actual balance returns to 1,000.

Transaction B read a value that was never committed.

**Prevention:** Read Committed or stronger isolation generally prevents dirty reads.

## 3. Non-Repeatable Read

A non-repeatable read occurs when the same row is read twice in one transaction and returns different values because another transaction committed an update.

Example:

1. Transaction A reads a product price of 100.
2. Transaction B changes the price to 120 and commits.
3. Transaction A reads the same row again and sees 120.

The row changed during Transaction A.

**Prevention:** Repeatable Read or stronger isolation generally prevents this anomaly, subject to the database's isolation implementation.

## 4. Phantom Read

A phantom read occurs when repeating a query with a search condition returns a different set of rows because another transaction inserted or deleted matching records.

Example:

Transaction A runs:

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

Transaction B inserts an employee whose salary is 60,000 and commits.

Transaction A repeats the query and may see the new employee.

The query's result set has changed.

**Prevention:** Serializable isolation provides the strongest standard isolation guarantee. Exact phantom-read behavior depends on the database and isolation implementation.

## 5. Lost Update

A lost update occurs when concurrent transactions overwrite one another's changes.

Example:

1. A product quantity is 10.
2. Transaction A reads 10.
3. Transaction B reads 10.
4. Transaction A writes 8.
5. Transaction B writes 7 based on its stale value.

Transaction A's update may be lost.

Possible solutions include:
- Atomic SQL updates.
- Optimistic locking with a version column.
- Pessimistic row locking.
- Appropriate transaction isolation.

## 6. Write Skew

Write skew can occur when two transactions read the same set of records and update different rows based on the same condition, jointly violating a business rule.

Example:

A hospital requires at least one doctor to remain on call.

Two doctors are on call. Each transaction checks that two doctors are available and then marks a different doctor as off call.

If both transactions commit, no doctor remains on call.

Serializable isolation or a suitable database constraint or locking strategy can prevent this, depending on the design.

## 7. Four Standard Isolation Levels

| Isolation level | Dirty reads | Non-repeatable reads | Phantom reads |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed | Prevented | Possible | Possible |
| Repeatable Read | Prevented | Prevented | Possible in the SQL standard model |
| Serializable | Prevented | Prevented | Prevented |

**Important:** These are the standard SQL model's guarantees. Actual behavior differs by database. Some systems implement stronger guarantees at particular levels.

## 8. Setting Isolation Levels

The syntax varies by database. A common SQL form is:

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

This statement must be used in the appropriate transaction context for the database engine.

For PostgreSQL, for example:

```sql
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;

SELECT *
FROM accounts
WHERE account_id = 101;

COMMIT;
```

Serializable transactions can fail with serialization errors when concurrent operations cannot safely be serialized. Applications should be prepared to retry the entire transaction.

## 9. Choosing an Isolation Level

### Read Committed

A common choice for general-purpose applications when each statement should see committed data.

### Repeatable Read

Useful when a transaction needs a stable view of previously read rows. Database-specific behavior must be understood.

### Serializable

Useful when business rules require transactions to behave as though they ran sequentially.

It can reduce concurrency or require retries, so use it with an understanding of the workload.

## 10. Isolation vs. Locking

Isolation levels define guarantees about concurrent transaction behavior.

Locking is one mechanism databases use to implement those guarantees.

Other mechanisms include multiversion concurrency control (MVCC), which can allow readers and writers to proceed concurrently using different row versions.

Higher isolation does not mean every operation simply locks every row. Implementation details depend on the database.

## 11. Interview Questions

### Q1. What is a dirty read?

Reading uncommitted data written by another transaction.

### Q2. What is a non-repeatable read?

Reading the same row twice in one transaction and seeing different values after another transaction commits an update.

### Q3. What is a phantom read?

Repeating a query and seeing a changed set of rows because concurrent transactions changed which records match the condition.

### Q4. What is the difference between a phantom read and a non-repeatable read?

A non-repeatable read concerns changed values in an existing row. A phantom read concerns changes to the set of rows returned by a query.

### Q5. Does Serializable mean transactions literally run one at a time?

Not necessarily. The database may execute operations concurrently while guaranteeing behavior equivalent to some valid serial execution.

### Q6. Can Serializable transactions fail?

Yes. Some databases abort transactions that encounter serialization conflicts. Applications should retry the complete transaction when appropriate.

### Q7. What is MVCC?

Multiversion concurrency control maintains multiple versions of records so transactions can read an appropriate version without always blocking writers.

## 12. Practice Tasks

1. Define all four standard isolation levels.
2. Explain dirty reads using a bank account example.
3. Distinguish non-repeatable reads from phantom reads.
4. Construct a lost-update example.
5. Explain write skew using a business rule.
6. Compare Read Committed and Serializable.
7. Explain how MVCC supports concurrent access.
8. Research how PostgreSQL implements Repeatable Read.

## Key Takeaways

- Isolation controls how concurrent transactions interact.
- Dirty reads, non-repeatable reads, phantom reads, lost updates, and write skew are distinct anomalies.
- Serializable offers strong transaction guarantees but may require retries.
- Actual isolation behavior depends on the database engine.
- Choose isolation based on correctness requirements and workload.
