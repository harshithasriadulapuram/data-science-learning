# Caching Interview Practice in Python

## 1. What Is Caching?

Caching stores the results of expensive operations so they can be reused instead of recalculated or fetched again.

Examples include:
- Storing results of expensive function calls.
- Caching database query results.
- Reusing API responses.
- Keeping frequently accessed data in memory.

Benefits:
- Faster responses.
- Fewer repeated computations.
- Reduced database and network load.

Trade-offs:
- Additional memory usage.
- Potentially stale data.
- Cache invalidation complexity.

## 2. Cache With a Dictionary

A dictionary can cache results for repeated inputs.

```python
def square_with_cache():
    cache = {}

    def square(n):
        if n not in cache:
            print("Computing...")
            cache[n] = n * n

        return cache[n]

    return square


square = square_with_cache()

print(square(5))  # Computing... then 25
print(square(5))  # 25; reused from cache
print(square(3))  # Computing... then 9
```

The dictionary avoids repeating the calculation for an input that has already been cached.

This example is useful for learning but is not necessary for a cheap operation such as squaring a small integer.

## 3. Use functools.lru_cache

Python provides a built-in decorator for caching function results.

```python
from functools import lru_cache


@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n < 2:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(10))  # 55
print(fibonacci.cache_info())
```

### How It Works

- Function results are cached by their arguments.
- Repeated calls with the same arguments can reuse cached results.
- `maxsize=128` limits the number of cached entries.
- `cache_info()` reports hits, misses, current size, and maximum size.

For this Fibonacci implementation, memoization reduces the number of distinct subproblems from exponential growth to O(n).

## 4. Use functools.cache

When an unbounded cache is acceptable, Python also provides `functools.cache`.

```from functools import cache


@cache
def factorial(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n <= 1:
        return 1

    return n * factorial(n - 1)


print(factorial(5))  # 120
```

An unbounded cache can grow indefinitely when given many distinct arguments. Choose it only when the number of distinct inputs is manageable.

## 5. Common Cache Eviction Policies

### LRU — Least Recently Used
Evicts the item that has not been accessed for the longest time.

Useful when recently accessed data is likely to be used again.

### LFU — Least Frequently Used
Evicts an item with the lowest access frequency, often breaking ties by recency.

Useful when frequently accessed items are likely to remain popular.

### FIFO — First In, First Out
Evicts the item that entered the cache earliest.

Simple, but it ignores how often or how recently an item is used.

## 6. Cache Invalidation

Cache invalidation means removing or refreshing cached data when it is no longer valid.

Common approaches:

- **TTL:** Expire an entry after a specified duration.
- **Explicit invalidation:** Delete an entry after an update.
- **Versioning:** Include a data version in the cache key.
- **Write-through:** Update the cache when writing to the underlying data store.
- **Cache-aside:** Fetch from the cache first, then load from the data source on a miss.

Example of a simple TTL cache:

```python
import time


class TTLCache:
    def __init__(self, ttl_seconds):
        if ttl_seconds < 0:
            raise ValueError("TTL cannot be negative")

        self.ttl_seconds = ttl_seconds
        self.data = {}

    def set(self, key, value):
        expires_at = time.monotonic() + self.ttl_seconds
        self.data[key] = (value, expires_at)

    def get(self, key):
        entry = self.data.get(key)

        if entry is None:
            return None

        value, expires_at = entry

        if time.monotonic() >= expires_at:
            del self.data[key]
            return None

        return value


cache = TTLCache(5)
cache.set("username", "Harshitha")

print(cache.get("username"))  # Harshitha
```

This simple implementation expires entries when they are accessed. It does not proactively clean up every expired entry, and it does not limit total cache size.

## 7. Cache-Aside Pattern

A typical cache-aside read follows these steps:

1. Check the cache.
2. If the data exists, return it.
3. Otherwise, fetch it from the database or source.
4. Store the result in the cache.
5. Return the result.

When underlying data changes, invalidate or refresh the corresponding cached entry.

## 8. Complexity Analysis

| Operation | Typical complexity |
|---|---|
| Dictionary lookup | O(1) average |
| Dictionary insertion | O(1) average |
| Dictionary deletion | O(1) average |
| LRU cache lookup | O(1) average |
| TTL check for one entry | O(1) |

These are average-case costs for dictionary-based operations; real cache performance also depends on memory, synchronization, serialization, and network latency.

## 9. Interview Questions

1. What is caching, and why is it useful?
2. What is the difference between LRU and LFU?
3. What is cache invalidation?
4. What is a cache hit versus a cache miss?
5. What does TTL mean?
6. Why can an unbounded cache be dangerous?
7. Explain memoization with an example.
8. What is the cache-aside pattern?
9. What happens when cached data becomes stale?
10. How would you cache API responses safely?

## 10. Practice Tasks

- [ ] Implement a dictionary-based cache.
- [ ] Use `lru_cache` to optimize a recursive function.
- [ ] Compare bounded and unbounded caches.
- [ ] Implement a TTL cache.
- [ ] Add a maximum-size limit to a cache.
- [ ] Explain cache invalidation strategies.
