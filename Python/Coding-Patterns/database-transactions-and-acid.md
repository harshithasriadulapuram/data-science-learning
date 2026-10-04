
# Database Transactions and ACID Properties

## 1. What Is a Database Transaction?

A **transaction** is a group of database operations treated as one logical unit of work.

For example, transferring ₹500 from Account A to Account B requires:
1. Deduct ₹500 from Account A.
2. Add ₹500 to Account B.

Both operations must succeed together. If one fails, the database should undo the changes.

## 2. ACID Properties

ACID stands for Atomicity, Consistency, Isolation, and Durability.

### Atomicity

A transaction either completes entirely or has no effect.

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 500
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 500
WHERE account_id = 2;

COMMIT;
```

If an operation fails before the transaction commits, use `ROLLBACK` to undo the uncommitted changes.

```sql
ROLLBACK;
```

### Consistency

A transaction must preserve database rules and constraints.

For example, an account balance might be required to remain non-negative.

```sql
CREATE TABLE accounts (
    account_id INT PRIMARY KEY,
    balance DECIMAL(10, 2) NOT NULL,
    CHECK (balance >= 0)
);
```

Consistency depends on valid application logic and correctly enforced database constraints.

### Isolation

Concurrent transactions should not interfere with one another in ways that violate the selected isolation level.

For example, two customers should not successfully purchase the last available item because both transactions incorrectly believe it is still in stock.

### Durability

Once a transaction commits successfully, its changes should survive a system restart or crash, subject to the database's durability configuration.

## 3. COMMIT and ROLLBACK

- `COMMIT`: Makes the transaction's changes permanent.
- `ROLLBACK`: Undoes uncommitted changes in the current transaction.

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 200
WHERE account_id = 1;

-- Confirm that the operation should be saved.
COMMIT;
```

To cancel instead:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 200
WHERE account_id = 1;

ROLLBACK;
```

**Important:** Transaction behavior and syntax can vary by database system and storage engine.

## 4. Problems Caused by Concurrent Transactions

### Dirty Read

A transaction reads another transaction's uncommitted changes.

If the other transaction rolls back, the first transaction has read data that was never committed.

### Non-Repeatable Read

A transaction reads the same row twice and gets different values because another transaction updates and commits the row between those reads.

### Phantom Read

A transaction repeats a query that matches a condition and sees additional or missing rows because another transaction inserts or deletes matching records.

### Lost Update

Two transactions read the same value and both update it. One update may overwrite the other's change.

For example:
- Initial stock: 10
- Transaction A reads 10.
- Transaction B reads 10.
- A sells 2 and writes 8.
- B sells 3 and writes 7.
- The final value becomes 7 instead of 5 if the updates overwrite each other.

Use suitable locking, atomic updates, or concurrency-control techniques to prevent such problems.

## 5. Transaction Isolation Levels

Common SQL isolation levels are:

| Isolation Level | General behavior |
|---|---|
| Read Uncommitted | Allows the weakest isolation; dirty reads may occur. |
| Read Committed | Prevents dirty reads. |
| Repeatable Read | Prevents dirty reads and non-repeatable reads under the SQL standard; phantom handling varies by implementation. |
| Serializable | Provides the strongest standard isolation, making concurrent execution equivalent to some serial execution. |

The exact behavior depends on the database engine and its implementation.

## 6. Example: Safe Inventory Update

Suppose a product has only 5 units remaining. We want to sell 2 units without allowing stock to become negative.

An atomic conditional update is one useful approach:

```sql
UPDATE products
SET stock = stock - 2
WHERE product_id = 101
  AND stock >= 2;
```

Check the number of rows affected:
- One row updated: the stock reduction succeeded.
- Zero rows updated: the product was missing or insufficient stock remained.

This avoids the simple read-then-write race that can happen when stock is checked separately from the update.

## 7. Transactions in Python with SQLite

```python
import sqlite3

connection = sqlite3.connect("bank.db")

try:
    connection.execute(
        "UPDATE accounts SET balance = balance - ? "
        "WHERE account_id = ?",
        (500, 1),
    )

    connection.execute(
        "UPDATE accounts SET balance = balance + ? "
        "WHERE account_id = ?",
        (500, 2),
    )

    connection.commit()
    print("Transfer completed.")

except sqlite3.Error as error:
    connection.rollback()
    print("Transfer failed:", error)

finally:
    connection.close()
```

This example assumes both account rows exist and that the application has suitable checks for insufficient funds and valid amounts. Production code should verify affected rows and enforce the required business rules.

Parameterized queries (`?`) help prevent SQL injection.

## 8. Savepoints

A savepoint allows part of a transaction to be rolled back without discarding all earlier work.

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

SAVEPOINT after_debit;

UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

-- Undo changes made after the savepoint if needed.
ROLLBACK TO SAVEPOINT after_debit;

COMMIT;
```

The exact syntax and support vary by database. Use savepoints only when partial rollback is appropriate for the business operation.

## 9. Deadlocks

A deadlock occurs when transactions wait for resources held by each other.

For example:
- Transaction A locks Row 1 and waits for Row 2.
- Transaction B locks Row 2 and waits for Row 1.

Neither can proceed until the database detects and resolves the deadlock, commonly by aborting one transaction.

Ways to reduce deadlocks:
- Access records in a consistent order.
- Keep transactions short.
- Avoid unnecessary locks.
- Handle deadlock errors and retry safely when appropriate.

## 10. Optimistic and Pessimistic Locking

### Pessimistic Locking

Assumes conflicts may happen and locks data to prevent conflicting modifications.

Example in databases that support this syntax:

```sql
SELECT *
FROM products
WHERE product_id = 101
FOR UPDATE;
```

Use this inside an appropriate transaction. Locking behavior varies by database.

### Optimistic Locking

Allows concurrent reads and detects conflicting updates using a version number.

```sql
UPDATE products
SET stock = 8,
    version = version + 1
WHERE product_id = 101
  AND version = 3;
```

If zero rows are updated, the version may have changed. The application should reload the current data and decide whether to retry.

## 11. Interview Questions

1. What is a database transaction?
2. Explain all four ACID properties with examples.
3. What is the difference between `COMMIT` and `ROLLBACK`?
4. What is a dirty read?
5. Explain non-repeatable reads and phantom reads.
6. What causes a lost update?
7. Compare the four standard isolation levels.
8. What is a deadlock, and how can it be reduced?
9. What is the difference between optimistic and pessimistic locking?
10. Why is an atomic conditional update safer than checking stock and updating it in separate steps?
11. What is a savepoint?
12. How do transactions help maintain consistency during a bank transfer?

## 12. Practice Tasks

1. Create two bank accounts and transfer money between them inside a transaction.
2. Simulate a failed transfer and roll back the changes.
3. Write an inventory update that cannot reduce stock below zero.
4. Explain how two concurrent transactions could cause a lost update.
5. Implement optimistic locking with a version column.
6. Describe a deadlock scenario and propose two ways to reduce its likelihood.

## Key Takeaways

- A transaction groups related database operations into one logical unit.
- ACID properties help protect correctness and reliability.
- `COMMIT` saves changes; `ROLLBACK` undoes uncommitted changes.
- Isolation levels control how concurrent transactions interact.
- Atomic updates, appropriate locking, and careful error handling prevent many data-integrity problems.
