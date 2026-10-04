
# Database Query Caching in Python

## 1. What Is Database Query Caching?

Database query caching stores the results of frequently requested queries so an application can reuse them instead of repeatedly querying the database.

For example, a product catalog may receive thousands of requests for the same product details. Caching can reduce database load and improve response time.

## 2. Why Use Caching?

- Reduce repeated database queries.
- Improve response times for frequently requested data.
- Reduce database resource usage.
- Help applications handle higher request volumes.

Caching is not suitable for every query. Frequently changing data and data requiring strict freshness need careful handling.

## 3. Common Caching Strategies

### Cache-Aside

The application checks the cache first.

1. Look for the requested data in the cache.
2. If found, return it.
3. If missing, query the database.
4. Store the result in the cache.
5. Return the result.

The application controls when to read and populate the cache.

### Read-Through

The cache layer loads missing data from the underlying data source on behalf of the application.

### Write-Through

A write updates the cache and underlying data source as part of the cache strategy.

### Write-Behind

A write is accepted by the cache first, and the underlying data source is updated later. This requires careful handling of failures and data durability.

## 4. Simple In-Memory Cache in Python

```python
import time

cache = {}


def get_product(product_id):
    now = time.time()

    cached = cache.get(product_id)

    if cached and cached["expires_at"] > now:
        return cached["value"]

    product = query_database(product_id)

    if product is None:
        return None

    cache[product_id] = {
        "value": product,
        "expires_at": now + 60,
    }

    return product
```

`query_database()` represents a database access function that you must implement.

This example demonstrates TTL-based caching. It is not production-ready: the dictionary is not shared across processes, has no size limit, and needs additional coordination for concurrent requests.

## 5. What Is TTL?

TTL means Time to Live.

It specifies how long a cached entry remains valid.

For example, a TTL of 60 seconds means the application considers the entry expired after 60 seconds.

A shorter TTL can reduce stale data but may cause more database queries. A longer TTL improves reuse but may serve older values for longer.

## 6. Cache Invalidation

Cache invalidation removes or refreshes cached data when the underlying information changes.

For example, after updating a product:

```python
def update_product(product_id, new_price):
    save_price_to_database(product_id, new_price)
    cache.pop(product_id, None)
```

The database update should succeed before invalidating the cache in this simple design.

For distributed systems, failures can occur between the database update and cache invalidation. More robust systems may use transactional outbox patterns, change events, or other synchronization strategies.

## 7. Using Redis

Redis is a commonly used external in-memory data store for caching.

Install its Python client:

```bash
pip install redis
```

Example:

```python
import json
import redis

client = redis.Redis(
    host="localhost",
    port=6379,
    decode_responses=True,
)


def get_product(product_id):
    key = f"product:{product_id}"

    cached = client.get(key)

    if cached is not None:
        return json.loads(cached)

    product = query_database(product_id)

    if product is not None:
        client.setex(
            key,
            60,
            json.dumps(product),
        )

    return product
```

This assumes `query_database()` returns a JSON-serializable value and Redis is running and reachable.

The example uses a 60-second TTL. Production code should also handle cache failures, serialization issues, and the freshness requirements of the data.

## 8. Cache Stampede

A cache stampede occurs when many requests attempt to load the same missing or expired cache entry simultaneously.

They may all query the database, producing a sudden load spike.

Possible solutions include:

- Request coalescing or single-flight loading.
- Distributed locks where appropriate.
- Randomized TTLs.
- Refreshing entries before expiration.
- Stale-while-revalidate strategies.

Choose a solution based on the workload and consistency requirements.

## 9. Cache-Aside Flow

```text
Request
   |
   v
Check Cache
   |
   +---- Hit ----> Return Cached Data
   |
   +---- Miss ---> Query Database
                       |
                       v
                  Store in Cache
                       |
                       v
                  Return Data
```

## 10. Common Problems

### Stale Data

The cache contains an older value than the database.

Possible solutions include shorter TTLs, invalidation, or event-driven updates.

### Cache Stampede

Many requests load the same missing entry simultaneously.

Use request coordination or refresh strategies.

### Cache Penetration

Requests repeatedly query the database for nonexistent records.

Possible mitigations include short-lived negative caching and input validation.

### Unbounded Memory Usage

A cache grows indefinitely.

Use an eviction policy, maximum size, or a dedicated caching system.

### Sensitive Data Exposure

Cached private data may be accessible to unauthorized callers if cache keys and access controls are designed poorly.

Include relevant authorization scope in cache design and avoid caching sensitive information without appropriate safeguards.

## 11. Cache vs. Database

| Feature | Cache | Database |
|---|---|---|
| Primary purpose | Faster repeated reads | Persistent data storage |
| Typical access | Very fast for in-memory hits | Depends on query and storage |
| Data lifetime | Often temporary | Usually persistent |
| Data freshness | May be stale | Source of truth in many designs |
| Storage capacity | Often more limited or costly per unit | Designed for durable storage |

A cache should not automatically be treated as the authoritative source of truth.

## 12. Interview Questions

### Q1. What is cache-aside?

The application checks the cache first and queries the underlying data source on a cache miss.

### Q2. What is TTL?

The duration for which a cache entry remains valid.

### Q3. What is cache invalidation?

Removing or refreshing cached data when it is no longer valid.

### Q4. What is a cache stampede?

A large number of requests simultaneously trying to regenerate the same missing or expired cache entry.

### Q5. Does caching always improve performance?

No. Cache lookups, serialization, invalidation, network calls, and low hit rates can add overhead.

## 13. Practice Tasks

1. Implement a dictionary-based TTL cache.
2. Add a maximum cache size.
3. Implement cache-aside using Redis.
4. Invalidate cached data after an update.
5. Simulate a cache miss and cache hit.
6. Explain how a cache stampede happens.
7. Decide which application data is safe to cache and for how long.

## Key Takeaways

- Caching reduces repeated database work.
- Cache-aside is a common application caching pattern.
- TTL and invalidation help manage freshness.
- Redis can provide a cache shared across application processes.
- Cache failures, concurrency, and sensitive data require careful handling.
