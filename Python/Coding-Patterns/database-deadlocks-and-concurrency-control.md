
# Database Deadlocks and Concurrency Control

## 1. What Is Concurrency?

Concurrency occurs when multiple transactions access or modify a database at the same time.

For example:
- Transaction A transfers money between accounts.
- Transaction B checks or updates one of those accounts.
- Both transactions may access the same records concurrently.

Concurrency control helps preserve data consistency while allowing transactions to execute safely.

## 2. What Is a Database Lock?

A lock controls access to a database resource while a transaction is using it.

### Shared Lock

A shared lock allows multiple transactions to read the same data, depending on the database's locking rules.

### Exclusive Lock

An exclusive lock generally prevents other transactions from modifying the locked resource while the lock is held.

Example SQL:

```sql
BEGIN;

SELECT *
FROM accounts
WHERE account_id = 1
FOR UPDATE;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

COMMIT;
```

`FOR UPDATE` requests row-level locks in databases that support this syntax, such as PostgreSQL.

Exact locking behavior varies by database and isolation level.

## 3. What Is a Deadlock?

A deadlock occurs when two or more transactions wait indefinitely for resources held by one another.

Consider two accounts, A and B.

1. Transaction T1 locks account A.
2. Transaction T2 locks account B.
3. T1 requests a lock on B and must wait.
4. T2 requests a lock on A and must wait.

Neither transaction can proceed without the other releasing its lock.

## 4. Deadlock Example

Conceptual SQL for Transaction T1:

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

-- Another transaction may lock account 2 here.

UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

COMMIT;
```

Transaction T2 might update account 2 first and then account 1.

If both transactions hold one lock and request the other's lock, a deadlock can occur.

This is a conceptual example. Whether a deadlock occurs depends on transaction timing and the database engine.

## 5. Four Necessary Conditions for Deadlock

The classical deadlock conditions are:

1. **Mutual exclusion:** A resource cannot be used simultaneously in an incompatible way.
2. **Hold and wait:** A transaction holds a resource while waiting for another.
3. **No preemption:** A resource cannot always be forcibly taken away from its holder.
4. **Circular wait:** Transactions form a cycle of dependencies.

Breaking at least one condition can prevent deadlocks under the relevant system design.

## 6. Deadlock Prevention

### Strategy 1: Acquire Locks in a Consistent Order

Always lock account IDs in ascending order, regardless of transfer direction.

For example, lock account 1 before account 2 in both transactions.

This reduces circular-wait risk.

### Strategy 2: Keep Transactions Short

Avoid:
- Long-running transactions.
- Network requests while holding database locks.
- Waiting for user input inside a transaction.
- Unnecessary updates.

### Strategy 3: Update Only Required Rows

Use precise `WHERE` conditions and appropriate indexes to avoid unnecessarily broad locking.

### Strategy 4: Retry Safely

Databases may detect deadlocks and abort one transaction. Applications should catch the relevant error and retry when appropriate.

Use a bounded retry count and a short backoff delay.

## 7. Deadlock Detection and Recovery

Database engines may detect a cycle in the lock-wait graph and abort one transaction to resolve the deadlock.

The application should:

1. Detect the database error.
2. Roll back the failed transaction if needed.
3. Retry the entire transaction when appropriate.
4. Stop after a bounded number of attempts.
5. Log repeated failures for investigation.

Do not retry every database error indiscriminately.

## 8. Python Example: Bounded Retry

This example demonstrates retry structure. The exception class is illustrative; replace it with the database driver's specific deadlock exception.

```python
import time


def run_with_retry(operation, max_attempts=3):
    for attempt in range(max_attempts):
        try:
            return operation()
        except DeadlockError:
            if attempt == max_attempts - 1:
                raise

            time.sleep(0.1 * (2 ** attempt))
```

Important:
- `DeadlockError` is a placeholder, not a built-in Python exception.
- The database transaction must be rolled back before retrying.
- `operation()` must be safe to retry.
- Real systems should consider jitter and appropriate transaction boundaries.

## 9. Optimistic vs. Pessimistic Concurrency

| Feature | Pessimistic | Optimistic |
|---|---|---|
| Main idea | Assume conflicts may occur | Assume conflicts are relatively uncommon |
| Approach | Lock resources | Check for conflicting changes |
| Best suited for | High-contention operations | Lower-contention workloads |
| Common trade-off | Waiting and lock contention | Retries when conflicts occur |

An optimistic update can use a version column:

```sql
UPDATE products
SET stock = 9,
    version = version + 1
WHERE product_id = 101
  AND version = 4;
```

If zero rows are updated, another transaction may have changed the record. The application should re-read and resolve the conflict.

## 10. Isolation Levels and Concurrency

Common SQL isolation levels include:

- **Read Uncommitted:** May permit dirty reads where supported.
- **Read Committed:** Prevents dirty reads.
- **Repeatable Read:** Provides stronger guarantees for repeated reads; exact behavior varies by database.
- **Serializable:** Aims to provide behavior equivalent to serial transaction execution.

Higher isolation does not automatically eliminate deadlocks. Serializable transactions can still require retries due to conflicts or serialization failures.

## 11. Deadlocks vs. Starvation

**Deadlock:** A group of transactions cannot proceed because each waits for another.

**Starvation:** A transaction repeatedly fails to obtain resources because other transactions keep taking priority.

Possible starvation mitigations include fair scheduling, bounded retries, and monitoring long wait times.

## 12. Interview Questions

### Q1. What is a database deadlock?

A situation where transactions wait for resources held by one another, preventing progress until the database resolves the cycle.

### Q2. How can deadlocks be reduced?

Acquire locks in a consistent order, keep transactions short, use appropriate indexes, and retry safely after database-detected deadlocks.

### Q3. Does an index prevent deadlocks?

No. Indexes can reduce the amount of data scanned and sometimes reduce locking, but deadlocks remain possible.

### Q4. What is the difference between deadlock and lock timeout?

A deadlock involves a circular dependency. A lock timeout occurs when a transaction waits longer than the configured limit. A timeout does not necessarily indicate a deadlock.

### Q5. Why must a failed transaction be rolled back before retrying?

The transaction may hold locks or have an aborted state. The application must restore a valid transaction state before starting the retry.

### Q6. Can retries cause duplicate operations?

Yes, if the operation is not designed to be safely retried. Use transaction boundaries, idempotency controls, and appropriate unique constraints where necessary.

## 13. Practice Tasks

1. Explain the four necessary conditions for deadlock.
2. Draw a deadlock involving two transactions and two rows.
3. Rewrite two transactions to acquire locks in a consistent order.
4. Explain how deadlocks differ from lock timeouts.
5. Implement bounded retry handling using a database driver's actual exception.
6. Explain optimistic concurrency using a version column.
7. Describe how isolation levels affect concurrent transactions.

## Key Takeaways

- Concurrency control protects database consistency.
- Locks coordinate access to shared resources.
- Deadlocks can arise from circular lock dependencies.
- Consistent lock ordering and short transactions reduce risk.
- Retry handling must be bounded and transaction-safe.
- Isolation levels, deadlocks, and timeouts are related but distinct concepts.
