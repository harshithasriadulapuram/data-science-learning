# Retry Logic and Exponential Backoff in Python

## 1. What Is Retry Logic?

Retry logic means attempting an operation again when it fails temporarily.

Examples:
- An API request times out.
- A database connection temporarily fails.
- A network service returns a temporary error.
- A cloud service responds with a rate-limit error.

Retries can improve reliability, but retrying every error indefinitely can make an outage worse.

## 2. What Is Exponential Backoff?

Exponential backoff increases the waiting time after each failed attempt.

For example, with a base delay of 1 second:

| Failed attempt | Delay before next attempt |
|---|---:|
| 1 | 1 second |
| 2 | 2 seconds |
| 3 | 4 seconds |
| 4 | 8 seconds |

A common formula is:

```text
delay = min(max_delay, base_delay * 2 ** attempt)
```

In distributed systems, **jitter** adds randomness to the delay so many clients do not retry simultaneously.

## 3. Basic Retry Implementation

```python
import time


def retry_operation(operation, max_attempts=3, base_delay=1):
    if max_attempts < 1:
        raise ValueError("max_attempts must be at least 1")

    if base_delay < 0:
        raise ValueError("base_delay cannot be negative")

    for attempt in range(max_attempts):
        try:
            return operation()

        except Exception:
            if attempt == max_attempts - 1:
                raise

            delay = base_delay * (2 ** attempt)
            time.sleep(delay)


def unreliable_operation():
    print("Trying operation...")
    raise ConnectionError("Temporary connection failure")


try:
    retry_operation(unreliable_operation, max_attempts=3)
except ConnectionError as error:
    print("Operation failed:", error)
```

This example retries all exceptions for simplicity. Production code should normally retry only errors known to be transient.

## 4. Add Exponential Backoff With Jitter

```python
import random
import time


def retry_with_backoff(
    operation,
    max_attempts=5,
    base_delay=0.5,
    max_delay=10
):
    if max_attempts < 1:
        raise ValueError("max_attempts must be at least 1")

    if base_delay < 0 or max_delay < 0:
        raise ValueError("Delays cannot be negative")

    for attempt in range(max_attempts):
        try:
            return operation()

        except (TimeoutError, ConnectionError):
            if attempt == max_attempts - 1:
                raise

            exponential_delay = min(
                max_delay,
                base_delay * (2 ** attempt)
            )

            delay = random.uniform(0, exponential_delay)
            time.sleep(delay)
```

### How It Works

1. Attempt the operation.
2. If it succeeds, return the result.
3. If a retryable error occurs, calculate the backoff delay.
4. Add random jitter to spread out retries.
5. Retry until the maximum number of attempts is reached.
6. Raise the final error if all attempts fail.

This implementation uses full jitter: the actual delay is randomly selected between zero and the calculated maximum.

## 5. Retry Only Appropriate Errors

Not every failure is temporary.

Potentially retryable:
- Connection timeouts.
- Temporary connection failures.
- HTTP 429 responses, respecting the server's Retry-After guidance.
- Some HTTP 5xx responses.

Usually not retryable without changing the request:
- Invalid input.
- Authentication failures.
- Authorization failures.
- Requests to nonexistent resources.

The exact policy depends on the API or service.

## 6. Important Rule: Avoid Duplicate Side Effects

Suppose a payment request times out after the server processes the payment.

Retrying the request could charge the customer twice.

For operations that create side effects, use an idempotency key or another server-supported deduplication mechanism when available.

A timeout does not necessarily mean the server failed to complete the operation.

## 7. Testing Retry Logic Without Waiting

Inject a sleep function so tests can replace real delays.

```python
def retry_operation(
    operation,
    max_attempts=3,
    base_delay=1,
    sleep_fn=None
):
    if max_attempts < 1:
        raise ValueError("max_attempts must be at least 1")

    if base_delay < 0:
        raise ValueError("base_delay cannot be negative")

    if sleep_fn is None:
        sleep_fn = time.sleep

    for attempt in range(max_attempts):
        try:
            return operation()
        except (TimeoutError, ConnectionError):
            if attempt == max_attempts - 1:
                raise

            sleep_fn(base_delay * (2 ** attempt))
```

Example test:

```python
def test_retry_succeeds_after_failure():
    outcomes = iter([
        ConnectionError("Temporary failure"),
        "success"
    ])
    delays = []

    def operation():
        outcome = next(outcomes)

        if isinstance(outcome, Exception):
            raise outcome

        return outcome

    result = retry_operation(
        operation,
        max_attempts=3,
        base_delay=1,
        sleep_fn=delays.append
    )

    assert result == "success"
    assert delays == [1]
```

The example assumes the required imports and function definition are available in the same module.

## 8. Complexity and Reliability

If the operation takes T time and the retry policy waits for delays d1 through dk, total elapsed time includes:

```text
operation time + d1 + d2 + ... + dk
```

Retries increase the number of possible attempts, so always define:
- A maximum attempt count.
- A delay cap.
- A total request deadline where appropriate.
- A policy for retryable errors.

## 9. Interview Questions

1. What is retry logic?
2. Why is exponential backoff useful?
3. What is jitter, and why does it help?
4. Why should retries be bounded?
5. Which errors should be retried?
6. What is an idempotent operation?
7. Why can retrying a payment request be dangerous?
8. What is the purpose of a Retry-After header?
9. How would you test retries without sleeping?
10. What is the difference between retry count and timeout?

## 10. Practice Tasks

- [ ] Implement retries for a temporary connection error.
- [ ] Add exponential backoff.
- [ ] Add random jitter.
- [ ] Retry only selected exception types.
- [ ] Write tests using an injected sleep function.
- [ ] Explain how retries can amplify an outage.
- [ ] Describe how idempotency prevents duplicate side effects.
