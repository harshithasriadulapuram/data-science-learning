
# Database Optimistic and Pessimistic Locking

## 1. Why Do We Need Locking?

When multiple users or processes update the same database record, their operations can conflict.

For example, a product has 10 items in stock. Two customers attempt to purchase 7 items simultaneously.

Without appropriate concurrency control, both requests might read the original stock value and overwrite one another's changes.

Locking and concurrency-control techniques help prevent inconsistent updates.

## 2. Pessimistic Locking

Pessimistic locking assumes conflicts are sufficiently likely that the application should protect a record before modifying it.

A transaction acquires a lock and holds it while performing its operation.

### Example: Lock a Bank Account

PostgreSQL example:

```sql
BEGIN;

SELECT balance
FROM accounts
WHERE account_id = 101
FOR UPDATE;

UPDATE accounts
SET balance = balance - 500
WHERE account_id = 101;

COMMIT;
```

`SELECT ... FOR UPDATE` requests a row-level lock that prevents conflicting updates until the transaction finishes.

### Advantages

- Useful when conflicts are frequent.
- Prevents competing transactions from modifying locked rows simultaneously.
- Supports safe read-modify-write workflows.

### Disadvantages

- Transactions may wait for locks.
- Long transactions reduce concurrency.
- Deadlocks can occur.

Use pessimistic locking when conflicting updates must be coordinated before proceeding.

## 3. Optimistic Locking

Optimistic locking assumes conflicts are relatively uncommon.

Instead of holding a lock throughout the operation, the application checks whether the record changed before saving the update.

A common technique is a version column.

### Example Table

```sql
CREATE TABLE products (
    product_id INTEGER PRIMARY KEY,
    stock INTEGER NOT NULL,
    version INTEGER NOT NULL DEFAULT 0
);
```

Suppose the current record is:

| product_id | stock | version |
|---|---:|---:|
| 101 | 10 | 0 |

An application reads this record and remembers version `0`.

It then attempts to purchase two items:

```sql
UPDATE products
SET stock = stock - 2,
    version = version + 1
WHERE product_id = 101
  AND version = 0
  AND stock >= 2;
```

If one row is updated, the operation succeeded.

If zero rows are updated, the record may have changed, the product may not exist, or the stock condition may have failed. The application must re-read the record and determine the correct outcome.

### Why Does This Work?

Suppose two requests both read version `0`.

- Request A updates the record and changes the version to `1`.
- Request B attempts to update only where the version is still `0`.
- Request B updates zero rows because the version is now `1`.

The stale update is rejected instead of silently overwriting the newer state.

### Advantages

- Avoids holding a database lock throughout the application workflow.
- Works well when conflicts are uncommon.
- Detects stale updates.

### Disadvantages

- Conflicting requests need retry or conflict handling.
- Repeated conflicts can waste work.
- Applications must correctly check the affected-row count.

## 4. Python Example: Optimistic Locking

The following example uses SQLite and a version column.

```python
import sqlite3


def purchase_product(product_id, quantity, expected_version):
    connection = sqlite3.connect("shop.db")

    try:
        connection.execute("BEGIN")

        cursor = connection.execute(
            """
            UPDATE products
            SET stock = stock - ?,
                version = version + 1
            WHERE product_id = ?
              AND version = ?
              AND stock >= ?
            """,
            (
                quantity,
                product_id,
                expected_version,
                quantity,
            ),
        )

        if cursor.rowcount != 1:
            connection.rollback()
            return False

        connection.commit()
        return True

    except sqlite3.Error:
        connection.rollback()
        raise

    finally:
        connection.close()
```

This function assumes the `products` table already exists and the caller supplies the version it previously read.

A return value of `False` indicates that the update did not satisfy all conditions. The caller should re-read the product and distinguish a version conflict from insufficient stock or a missing product.

## 5. Pessimistic vs. Optimistic Locking

| Feature | Pessimistic Locking | Optimistic Locking |
|---|---|---|
| Main approach | Acquire locks before updating | Check for conflicts when updating |
| Conflicts | Coordinates access through locks | Detects stale updates |
| Waiting | Transactions may wait for locks | Conflicts may require retries |
| Best suited for | High-contention workflows | Low-contention workflows |
| Typical example | `SELECT ... FOR UPDATE` | Version-column update |
| Main risk | Lock contention and deadlocks | Repeated conflicts |

Neither approach is always better. The correct choice depends on the workload, database, and correctness requirements.

## 6. Important: Atomic Updates

Some operations do not require reading a value into application memory first.

For example:

```sql
UPDATE products
SET stock = stock - 1
WHERE product_id = 101
  AND stock >= 1;
```

The condition and update execute as one database statement. Check the affected-row count to determine whether the purchase succeeded.

This is often safer than reading stock into Python, calculating a new value, and writing it back without concurrency protection.

## 7. Common Mistakes

1. Reading a record and updating it later without checking whether it changed.
2. Forgetting to check the affected-row count.
3. Holding pessimistic locks while making external API calls.
4. Retrying indefinitely after repeated conflicts.
5. Assuming every database supports identical locking syntax.
6. Treating every failed update as a version conflict without checking other conditions.
7. Assuming optimistic locking alone prevents overselling when the update does not enforce a stock constraint.

## 8. Interview Questions

### Q1. What is optimistic locking?

A concurrency-control approach that checks whether a record has changed before accepting an update, commonly using a version number.

### Q2. What is pessimistic locking?

A technique that acquires locks to coordinate access before or during an update.

### Q3. Does optimistic locking prevent all database conflicts?

No. It detects specific conflicting updates. Applications must handle rejected updates, and other database constraints and transaction controls may still be necessary.

### Q4. Which approach is better for frequently updated records?

Pessimistic locking may be suitable when contention is high and waiting is acceptable. The best choice depends on transaction duration, database behavior, and performance requirements.

### Q5. What is a lost update?

A lost update occurs when one transaction overwrites another transaction's changes because it operates on stale data without adequate concurrency control.

### Q6. Can optimistic locking work without a version column?

Yes. Some implementations compare previously read values or use timestamps, but a dedicated version column is often clearer and more reliable.

## 9. Practice Tasks

1. Explain the difference between optimistic and pessimistic locking.
2. Implement a version-column update.
3. Handle a failed conditional update in Python.
4. Explain how an atomic stock decrement prevents overselling.
5. Identify a lost-update scenario.
6. Compare locking strategies for a ticket-booking application.
7. Explain why retry limits matter.

## Key Takeaways

- Pessimistic locking coordinates access by acquiring locks.
- Optimistic locking detects conflicting changes, often through version numbers.
- Atomic conditional updates are useful for inventory and counters.
- Always check whether an update actually affected a row.
- Select a strategy based on contention, correctness, and database behavior.
