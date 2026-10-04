# Timeouts and Deadlines in Python

## 1. What Is a Timeout?

A timeout limits how long an operation is allowed to wait.

Examples:
- An HTTP request should not wait indefinitely for a server.
- A database query should have a reasonable execution limit.
- A thread should not wait forever for a lock.
- An LLM call should return or fail within an acceptable duration.

Without timeouts, stalled operations can consume resources and reduce application availability.

## 2. Timeout vs Deadline

### Timeout
A maximum waiting duration for one operation.

Example: allow an HTTP request to wait for at most 3 seconds.

### Deadline
An absolute point in time by which the entire operation should finish.

Example: a complete API workflow must finish within 10 seconds, including its retries and downstream calls.

A deadline helps prevent every nested operation from independently consuming its full timeout.

## 3. Use a Timeout With Requests

```python
import requests


def fetch_data(url):
    response = requests.get(
        url,
        timeout=(2, 5)
    )
    response.raise_for_status()
    return response.json()
```

The tuple specifies a connection timeout and a read timeout.

Important: the read timeout is generally a limit on waiting for data, not a strict total wall-clock deadline for the entire response.

## 4. Create a Deadline With time.monotonic()

Use a monotonic clock for elapsed-time calculations because wall-clock time can change.

```python
import time


def make_deadline(timeout_seconds):
    if timeout_seconds < 0:
        raise ValueError("Timeout cannot be negative")

    return time.monotonic() + timeout_seconds


def remaining_time(deadline):
    return max(0, deadline - time.monotonic())


deadline = make_deadline(5)

print(f"Remaining: {remaining_time(deadline):.2f} seconds")
```

## 5. Pass the Remaining Budget to Each Operation

```python
import time


def perform_step(name, timeout):
    if timeout <= 0:
        raise TimeoutError("No time remaining")

    # Replace this placeholder with a real operation that
    # supports a timeout.
    print(f"Running {name} with a {timeout:.2f}s budget")


def workflow(total_timeout):
    deadline = time.monotonic() + total_timeout

    for step in ["database", "external_api", "processing"]:
        remaining = deadline - time.monotonic()

        if remaining <= 0:
            raise TimeoutError("Workflow deadline exceeded")

        perform_step(step, remaining)


workflow(5)
```

This example demonstrates deadline propagation. The placeholder does not actually enforce a timeout; each real operation must support and honor the remaining budget.

## 6. Avoid Retrying Beyond the Deadline

```python
import time


def retry_until_deadline(operation, deadline):
    while True:
        remaining = deadline - time.monotonic()

        if remaining <= 0:
            raise TimeoutError("Deadline exceeded")

        try:
            return operation(timeout=remaining)

        except (TimeoutError, ConnectionError):
            if time.monotonic() >= deadline:
                raise TimeoutError("Deadline exceeded")


def example_operation(timeout):
    # A real implementation must enforce this timeout.
    print(f"Operation budget: {timeout:.2f}s")
    return "success"


deadline = time.monotonic() + 3
print(retry_until_deadline(example_operation, deadline))
```

This is a teaching example. In production, retries should have a bounded attempt count, a backoff policy, and careful exception handling. The operation must honor its timeout; passing a timeout argument alone cannot force arbitrary code to stop.

## 7. Timeouts in Concurrent Python

### Lock timeout

```python
from threading import Lock

lock = Lock()

if lock.acquire(timeout=1):
    try:
        print("Lock acquired")
    finally:
        lock.release()
else:
    print("Could not acquire lock in time")
```

### Future timeout

```python
from concurrent.futures import ThreadPoolExecutor, TimeoutError


def slow_task():
    import time
    time.sleep(2)
    return "finished"


with ThreadPoolExecutor() as executor:
    future = executor.submit(slow_task)

    try:
        print(future.result(timeout=1))
    except TimeoutError:
        print("Waiting for the result timed out")
```

A Future timeout stops waiting for the result; it does not necessarily stop the underlying task. Exiting the executor's context manager can still wait for submitted work to finish.

## 8. Common Mistakes

- Assuming a read timeout is a strict total request deadline.
- Retrying without a maximum attempt count.
- Giving every nested operation the full original timeout.
- Using wall-clock time for elapsed-time calculations.
- Assuming cancelling a Future always stops its task.
- Ignoring timeout errors from downstream services.
- Retrying operations that may cause duplicate side effects.

## 9. Interview Questions

1. What is the difference between a timeout and a deadline?
2. Why is time.monotonic() appropriate for elapsed-time measurements?
3. Why should a deadline be propagated to downstream calls?
4. What is the difference between a connection timeout and a read timeout?
5. Does a Future timeout terminate the underlying thread?
6. How should retry logic interact with a deadline?
7. Why are timeouts important in distributed systems?
8. How would you set timeouts for an API that calls an LLM and a database?

## 10. Practice Tasks

- [ ] Implement a deadline using time.monotonic().
- [ ] Pass the remaining time to nested operations.
- [ ] Add bounded retries within a deadline.
- [ ] Configure HTTP connection and read timeouts.
- [ ] Test expired deadlines.
- [ ] Explain why timeouts do not always cancel the underlying work.
