# Circuit Breaker Pattern in Python

## 1. What Is the Circuit Breaker Pattern?

The circuit breaker pattern prevents an application from repeatedly calling a service that is failing.

For example, suppose your Python application depends on an external API. If the API is unavailable, repeatedly calling it wastes time and resources.

A circuit breaker temporarily blocks calls after repeated failures, giving the service time to recover.

## 2. The Three States

### Closed
Requests are allowed through. Failures are counted.

### Open
Requests are rejected immediately without calling the dependency.

### Half-Open
After a waiting period, a limited number of trial requests are allowed. Success indicates recovery; failure opens the circuit again.

```text
CLOSED
  |
  | Failure threshold reached
  v
OPEN
  |
  | Recovery timeout elapsed
  v
HALF-OPEN
  |              |
  | Success      | Failure
  v              v
CLOSED           OPEN
```

## 3. A Simple Implementation

This educational implementation uses a failure threshold and recovery timeout. It assumes calls are made sequentially; production implementations need additional synchronization for concurrent requests.

```python
import time


class CircuitBreaker:
    def __init__(
        self,
        failure_threshold=3,
        recovery_timeout=5
    ):
        if failure_threshold < 1:
            raise ValueError("Threshold must be positive")

        if recovery_timeout < 0:
            raise ValueError("Timeout cannot be negative")

        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout

        self.failure_count = 0
        self.state = "CLOSED"
        self.opened_at = None

    def call(self, operation):
        now = time.monotonic()

        if self.state == "OPEN":
            if now - self.opened_at < self.recovery_timeout:
                raise RuntimeError("Circuit is OPEN")

            self.state = "HALF_OPEN"

        try:
            result = operation()

        except Exception:
            if self.state == "HALF_OPEN":
                self.state = "OPEN"
                self.opened_at = time.monotonic()
                self.failure_count = self.failure_threshold
            else:
                self.failure_count += 1

                if self.failure_count >= self.failure_threshold:
                    self.state = "OPEN"
                    self.opened_at = time.monotonic()

            raise

        self.failure_count = 0
        self.state = "CLOSED"
        self.opened_at = None

        return result


def successful_operation():
    return "Service response received"


breaker = CircuitBreaker(failure_threshold=3)

print(breaker.call(successful_operation))
# Service response received
```

## 4. How It Works

1. Calls pass through while the circuit is closed.
2. Each failure increments the failure count.
3. Once the threshold is reached, the circuit opens.
4. Calls are rejected while the recovery timeout has not elapsed.
5. After the timeout, the next call acts as a trial request.
6. A successful trial closes the circuit.
7. A failed trial opens the circuit again.

This implementation treats all exceptions as failures for simplicity. Production code should define which exceptions count as dependency failures and should not usually count application programming errors as service failures.

## 5. Circuit Breaker vs Retry

| Retry | Circuit breaker |
|---|---|
| Attempts a failed operation again | Temporarily blocks calls to a failing dependency |
| Useful for transient errors | Useful during sustained failures |
| Can increase traffic during an outage | Helps reduce repeated calls during an outage |
| Usually bounded by attempts or deadlines | Uses states and recovery rules |

Both patterns can be used together. Configure retries carefully so they do not overwhelm a failing service.

## 6. Circuit Breaker vs Rate Limiter

A rate limiter controls how frequently requests are accepted.

A circuit breaker controls whether requests are sent to a dependency based on its recent health.

They solve different problems.

## 7. Production Considerations

A production-quality circuit breaker may need:

- Thread-safe state transitions.
- A rolling failure window instead of a simple lifetime counter.
- Separate handling of timeouts and application errors.
- A limited number of half-open trial requests.
- Metrics, logs, and alerts.
- A fallback response where appropriate.
- Coordination rules for distributed applications.

The simple implementation above is intended for learning, not as a complete production library.

## 8. Interview Questions

1. What problem does the circuit breaker pattern solve?
2. Explain closed, open, and half-open states.
3. Why is the half-open state necessary?
4. How is a circuit breaker different from retry logic?
5. Why should not every exception count as a dependency failure?
6. How can a circuit breaker protect downstream services?
7. Why does concurrent access require synchronization?
8. Can retries and circuit breakers be combined?

## 9. Practice Tasks

- [ ] Implement the three circuit breaker states.
- [ ] Test what happens after three consecutive failures.
- [ ] Test that calls are rejected while the circuit is open.
- [ ] Test recovery after the timeout.
- [ ] Add a fake clock so tests do not need real delays.
- [ ] Decide which exceptions should count as dependency failures.
- [ ] Explain how this pattern complements retry logic.
