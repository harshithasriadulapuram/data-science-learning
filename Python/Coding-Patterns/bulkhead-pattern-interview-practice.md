# Bulkhead Pattern Interview Practice in Python

## 1. What Is the Bulkhead Pattern?

The bulkhead pattern isolates parts of an application so that a failure or overload in one part does not bring down the entire system.

The name comes from ship compartments: if one compartment floods, the others can remain protected.

### Example

Imagine a service that calls three external APIs:
- Payment API
- Product API
- Recommendation API

If recommendation requests become slow, they should not consume every available worker and prevent payment requests from being processed.

Bulkheads isolate resources such as threads, workers, connections, or concurrent request slots.

## 2. How It Works

1. Divide resources into separate pools or limits.
2. Assign each workload to its own resource boundary.
3. Reject or queue work when its allocated capacity is exhausted.
4. Keep failures in one workload from exhausting resources needed by others.

## 3. A Simple Python Example

A semaphore limits how many operations can run concurrently.

```python
from concurrent.futures import ThreadPoolExecutor
from threading import BoundedSemaphore


class Bulkhead:
    def __init__(self, max_concurrent):
        if max_concurrent < 1:
            raise ValueError("Capacity must be positive")

        self.semaphore = BoundedSemaphore(max_concurrent)

    def run(self, operation, *args, **kwargs):
        # Fail fast instead of waiting indefinitely for a slot.
        acquired = self.semaphore.acquire(blocking=False)

        if not acquired:
            raise RuntimeError("Bulkhead capacity exhausted")

        try:
            return operation(*args, **kwargs)
        finally:
            self.semaphore.release()


def fetch_product(product_id):
    return f"Product {product_id}"


product_bulkhead = Bulkhead(max_concurrent=2)

print(product_bulkhead.run(fetch_product, 101))
# Product 101
```

This example demonstrates the capacity limit. It does not by itself create a separate thread pool or isolate CPU, memory, network connections, and all other resources.

## 4. Isolating Different Workloads

```python
from concurrent.futures import ThreadPoolExecutor

payment_pool = ThreadPoolExecutor(max_workers=5)
recommendation_pool = ThreadPoolExecutor(max_workers=2)


def process_payment(payment_id):
    return f"Payment {payment_id} processed"


def get_recommendations(user_id):
    return f"Recommendations for {user_id}"


payment_future = payment_pool.submit(process_payment, 123)
recommendation_future = recommendation_pool.submit(
    get_recommendations,
    42
)

print(payment_future.result())
print(recommendation_future.result())

payment_pool.shutdown()
recommendation_pool.shutdown()
```

Separate pools give each workload its own worker capacity. Real applications should manage pool lifecycles centrally and configure timeouts and queue limits as needed.

## 5. Bulkhead vs Circuit Breaker vs Retry

| Pattern | Main purpose |
|---|---|
| Bulkhead | Isolates resources between workloads |
| Circuit breaker | Stops calls to a dependency that is failing |
| Retry | Attempts a transiently failed operation again |
| Rate limiter | Controls how many requests are accepted |

These patterns can work together, but none replaces the others.

## 6. Advantages

- Prevents one overloaded dependency from consuming all resources.
- Improves fault isolation.
- Protects critical operations from unrelated workloads.
- Makes resource allocation more predictable.

## 7. Limitations

- Requires careful capacity planning.
- Too many isolated pools can waste resources.
- A queue with no bound can still grow excessively.
- Isolation at the thread-pool level does not automatically isolate memory or CPU.
- Rejected work needs an appropriate error or fallback strategy.

## 8. Production Considerations

Consider:
- Separate worker pools for critical workloads.
- Bounded queues and explicit rejection behavior.
- Connection pool limits.
- Request deadlines and timeouts.
- Monitoring for saturation and rejected tasks.
- Graceful shutdown and resource cleanup.

For asynchronous Python applications, bounded semaphores and separately managed task groups can help enforce concurrency limits. Blocking work should not run directly on an event loop.

## 9. Interview Questions

1. What problem does the bulkhead pattern solve?
2. Why is resource isolation important in microservices?
3. How is a bulkhead different from a circuit breaker?
4. Why might separate thread pools be useful?
5. What can happen if task queues are unbounded?
6. What is the difference between limiting concurrency and limiting request rate?
7. How would you protect payment processing from a slow recommendation service?
8. What metrics would you monitor to detect bulkhead saturation?

## 10. Practice Tasks

- [ ] Implement a semaphore-based concurrency limit.
- [ ] Reject work when capacity is exhausted.
- [ ] Create separate worker pools for two workloads.
- [ ] Add bounded queues and timeouts.
- [ ] Explain how a bulkhead complements a circuit breaker.
- [ ] Design an isolation strategy for a Python API.
