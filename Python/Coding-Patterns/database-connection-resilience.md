# Database Connection Resilience in Python

## 1. What Is Connection Resilience?

Database connection resilience is the ability of an application to handle temporary database failures safely and recover when possible.

Failures can happen because of:

* Network interruptions
* Database restarts
* Connection timeouts
* Connection pool exhaustion
* Temporary service unavailability

A resilient application handles recoverable failures without crashing immediately.

## 2. Important Principles

1. **Retry only transient failures:** A temporary network error may recover; invalid SQL usually will not.
2. **Limit retries:** Never retry indefinitely.
3. **Use exponential backoff:** Increase the waiting time between attempts.
4. **Set timeouts:** Prevent operations from hanging indefinitely.
5. **Close resources:** Release connections and cursors properly.
6. **Avoid unsafe retries:** Retrying a write after an uncertain outcome can duplicate an operation.

## 3. Retry With Exponential Backoff

```python
import random
import time

def retry_operation(operation, max_attempts=4, base_delay=0.5):
    for attempt in range(max_attempts):
        try:
            return operation()
        except TimeoutError:
            if attempt == max_attempts - 1:
                raise

            delay = base_delay * (2 ** attempt)
            delay += random.uniform(0, 0.2)  # jitter
            time.sleep(delay)
```

Example:

```python
attempts = 0

def unstable_operation():
    global attempts
    attempts += 1

    if attempts < 3:
        raise TimeoutError("Temporary failure")

    return "Operation succeeded"

result = retry_operation(unstable_operation)
print(result)
```

The operation succeeds on the third attempt.

**Important:** This example retries only `TimeoutError`. Real database applications should catch the specific transient exceptions provided by their database driver.

## 4. Retry Safety: Reads Versus Writes

### Reads

A failed read can often be retried safely, provided the operation has no additional side effects.

### Writes

Consider a payment request:

1. The application sends a payment.
2. The database commits the transaction.
3. The network connection fails before the application receives confirmation.
4. The application retries the payment.

The first payment may already have succeeded. Retrying without protection could charge the customer twice.

Use an **idempotency key**, a unique operation identifier, or a database uniqueness constraint to prevent duplicate effects.

## 5. SQLite Connection Example

```python
import sqlite3

def get_customer(customer_id):
    connection = sqlite3.connect(
        "app.db",
        timeout=5
    )

    try:
        cursor = connection.cursor()
        cursor.execute(
            "SELECT id, name FROM customers WHERE id = ?",
            (customer_id,)
        )
        return cursor.fetchone()
    finally:
        connection.close()
```

The `timeout` argument controls how long SQLite waits when a database lock prevents access. It is not a universal network timeout for every database driver.

## 6. Handling Database Errors

```python
import sqlite3

def create_customer(name):
    connection = sqlite3.connect("app.db")

    try:
        cursor = connection.cursor()
        cursor.execute(
            "INSERT INTO customers (name) VALUES (?)",
            (name,)
        )
        connection.commit()
    except sqlite3.Error:
        connection.rollback()
        raise
    finally:
        connection.close()
```

This example rolls back the transaction if a SQLite database operation fails, then re-raises the exception so the caller can handle it.

## 7. Production Checklist

* Configure connection and query timeouts.
* Use connection pooling where appropriate.
* Retry only errors classified as transient.
* Apply a maximum attempt count and an overall deadline.
* Add jitter to reduce synchronized retry storms.
* Roll back failed transactions.
* Make important writes idempotent.
* Log failures and retry attempts.
* Monitor connection failures and pool exhaustion.
* Avoid retrying at multiple application layers without a clear policy.

## 8. Interview Questions

1. What is database connection resilience?
2. Which errors should be retried?
3. Why is retrying every database exception dangerous?
4. What is exponential backoff?
5. Why is jitter useful?
6. Why can retrying a write create duplicate data?
7. How do timeouts differ from retry limits?
8. What should an application do if the commit succeeds but the response is lost?

## 9. Practice Tasks

1. Modify the retry function to accept a configurable maximum attempt count.
2. Add an overall deadline so retries stop after a time limit.
3. Log each retry attempt.
4. Write a test where an operation fails twice and succeeds on the third attempt.
5. Write a test proving that the final exception is raised after all attempts fail.
6. Explain how you would safely retry a database write.
