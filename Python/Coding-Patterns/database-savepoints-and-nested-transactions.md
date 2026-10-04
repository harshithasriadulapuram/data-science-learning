
# Database Savepoints and Nested Transactions in Python

## 1. What Is a Savepoint?

A savepoint marks a position inside a database transaction.

It allows an application to roll back part of a transaction without necessarily undoing all earlier work.

For example, an order-processing operation might:
1. Insert an order.
2. Insert order items.
3. Attempt an optional audit operation.
4. Roll back only the audit operation if it fails.
5. Commit the order if the remaining work is valid.

Whether an order should be committed after a particular failure depends on the application's business rules.

## 2. Basic SQL Savepoint Syntax

```sql
BEGIN;

INSERT INTO orders (order_id, customer_id)
VALUES (101, 5);

SAVEPOINT before_optional_work;

INSERT INTO audit_logs (message)
VALUES ('Order created');

ROLLBACK TO SAVEPOINT before_optional_work;

RELEASE SAVEPOINT before_optional_work;

COMMIT;
```

This example rolls back work performed after the savepoint. The order insertion remains part of the transaction and can be committed.

Savepoint syntax and exact behavior vary by database.

## 3. Python Example Using SQLite

SQLite supports savepoints.

```python
import sqlite3


def create_order(database_path, order_id, customer_id):
    connection = sqlite3.connect(database_path)

    try:
        connection.execute("BEGIN")

        connection.execute(
            """
            INSERT INTO orders (order_id, customer_id)
            VALUES (?, ?)
            """,
            (order_id, customer_id),
        )

        connection.execute(
            "SAVEPOINT optional_work"
        )

        try:
            connection.execute(
                """
                INSERT INTO audit_logs (message)
                VALUES (?)
                """,
                ("Order created",),
            )
        except sqlite3.Error:
            connection.execute(
                "ROLLBACK TO SAVEPOINT optional_work"
            )
        finally:
            connection.execute(
                "RELEASE SAVEPOINT optional_work"
            )

        connection.commit()

    except Exception:
        connection.rollback()
        raise

    finally:
        connection.close()
```

This example assumes the `orders` and `audit_logs` tables already exist.

The audit operation is optional in this example. If auditing is mandatory for your application, do not silently ignore its failure; roll back the entire transaction instead.

## 4. What Are Nested Transactions?

A nested transaction is a transaction-like operation executed inside another transaction.

Some database libraries support nested transaction APIs by using savepoints. This does not necessarily mean the database has independent, fully isolated transactions nested inside each other.

For example, SQLAlchemy supports nested transaction scopes through savepoints.

```python
from sqlalchemy import create_engine, text

engine = create_engine("sqlite:///app.db")

with engine.begin() as connection:
    connection.execute(
        text(
            "INSERT INTO logs(message) VALUES (:message)"
        ),
        {"message": "Main operation started"},
    )

    nested = connection.begin_nested()

    try:
        connection.execute(
            text(
                "INSERT INTO logs(message) VALUES (:message)"
            ),
            {"message": "Optional operation"},
        )

        nested.commit()

    except Exception:
        nested.rollback()
        raise
```

If an exception escapes the outer transaction block, the outer transaction is rolled back as well.

In this example, `begin_nested()` establishes a savepoint on supported database configurations.

## 5. Savepoint vs. Full Rollback

| Operation | Effect |
|---|---|
| `COMMIT` | Commits the transaction |
| `ROLLBACK` | Rolls back the active transaction |
| `SAVEPOINT name` | Marks a point within the transaction |
| `ROLLBACK TO SAVEPOINT name` | Undoes work after the savepoint |
| `RELEASE SAVEPOINT name` | Releases the savepoint |

Rolling back to a savepoint does not ordinarily end the entire transaction.

## 6. Important Rules

1. A savepoint exists within a transaction.
2. A savepoint does not independently commit the transaction.
3. A full rollback undoes uncommitted work in the transaction.
4. Releasing a savepoint does not commit the outer transaction.
5. Savepoint support and syntax vary across database systems.
6. Nested transaction APIs may have different semantics across libraries.

## 7. Common Mistakes

- Assuming a savepoint is the same as a separate transaction.
- Forgetting to release a savepoint when required.
- Swallowing errors that should abort the outer transaction.
- Assuming an inner commit makes changes permanently durable.
- Using savepoints without understanding the database driver's transaction behavior.

## 8. Interview Questions

### Q1. What is a savepoint?

A named marker within a transaction that permits partial rollback.

### Q2. Does releasing a savepoint commit the transaction?

No. Releasing a savepoint does not commit the outer transaction.

### Q3. What is the difference between a savepoint and a full rollback?

A savepoint rollback undoes work after a specified marker. A full rollback undoes the transaction's uncommitted work.

### Q4. Are nested transactions always independent transactions?

No. Many implementations use savepoints to provide nested transaction-like behavior within one outer transaction.

### Q5. When are savepoints useful?

They are useful when an operation contains optional or recoverable sub-operations that can be undone independently while preserving earlier work.

## 9. Practice Tasks

1. Create a SQLite transaction containing two inserts.
2. Add a savepoint between the inserts.
3. Roll back to the savepoint and inspect the results.
4. Compare savepoint rollback with full rollback.
5. Implement nested transaction handling using SQLAlchemy.
6. Explain when an audit failure should abort the entire transaction.
7. Verify that an outer rollback undoes changes made before and after a savepoint.

## Key Takeaways

- Savepoints support partial rollback inside a transaction.
- Releasing a savepoint does not commit the outer transaction.
- Nested transaction APIs commonly use savepoints.
- Transaction behavior depends on the database and library.
- Error handling must reflect the application's business rules.
