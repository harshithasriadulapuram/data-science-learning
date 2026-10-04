# Rate Limiter Interview Practice in Python

## 1. What Is a Rate Limiter?

A rate limiter controls how many requests a user or client can make within a period.

For example, an API might allow a user to make at most 5 requests in 10 seconds.

A rate limiter helps:
- Protect APIs from excessive traffic.
- Reduce accidental or abusive request bursts.
- Manage shared resources fairly.
- Enforce usage limits.

## 2. Common Rate-Limiting Algorithms

### Fixed Window Counter
Counts requests within fixed time windows.

Example: allow 5 requests per minute.

Advantage: simple and memory-efficient.
Limitation: a burst near a window boundary can allow many requests in a short period.

### Sliding Window Log
Stores the timestamps of recent requests.

Advantage: accurately counts requests in the rolling window.
Limitation: stores individual request timestamps.

### Sliding Window Counter
Estimates the rolling request count using current and previous window counters.

Advantage: less memory than a full timestamp log.
Limitation: it is an approximation.

### Token Bucket
Tokens are added at a fixed rate. Each request consumes one token.

Advantage: allows controlled bursts while limiting the long-term request rate.

### Leaky Bucket
Requests are processed or released at a controlled rate.

Advantage: smooths traffic.
Limitation: handling bursts depends on the specific queue and rejection policy.

## 3. Implement a Fixed Window Counter

This educational implementation uses a caller-supplied timestamp so the behavior is deterministic and easy to test.

```python
class FixedWindowRateLimiter:
    def __init__(self, limit, window_seconds):
        if limit <= 0 or window_seconds <= 0:
            raise ValueError("Limit and window must be positive")

        self.limit = limit
        self.window_seconds = window_seconds
        self.windows = {}

    def allow_request(self, user_id, now):
        window_id = int(now // self.window_seconds)

        key = (user_id, window_id)
        count = self.windows.get(key, 0)

        if count >= self.limit:
            return False

        self.windows[key] = count + 1
        return True


limiter = FixedWindowRateLimiter(limit=3, window_seconds=10)

print(limiter.allow_request("user-1", 1))   # True
print(limiter.allow_request("user-1", 2))   # True
print(limiter.allow_request("user-1", 3))   # True
print(limiter.allow_request("user-1", 4))   # False
print(limiter.allow_request("user-1", 10))  # True: new window
```

This version retains old window entries indefinitely. A production implementation should expire stale entries and protect shared state against concurrent requests.

## 4. Implement a Sliding Window Log

```python
from collections import defaultdict, deque


class SlidingWindowRateLimiter:
    def __init__(self, limit, window_seconds):
        if limit <= 0 or window_seconds <= 0:
            raise ValueError("Limit and window must be positive")

        self.limit = limit
        self.window_seconds = window_seconds
        self.requests = defaultdict(deque)

    def allow_request(self, user_id, now):
        timestamps = self.requests[user_id]
        cutoff = now - self.window_seconds

        # Remove requests outside the rolling window.
        while timestamps and timestamps[0] <= cutoff:
            timestamps.popleft()

        if len(timestamps) >= self.limit:
            return False

        timestamps.append(now)
        return True


limiter = SlidingWindowRateLimiter(3, 10)

print(limiter.allow_request("user-1", 1))   # True
print(limiter.allow_request("user-1", 2))   # True
print(limiter.allow_request("user-1", 3))   # True
print(limiter.allow_request("user-1", 4))   # False
print(limiter.allow_request("user-1", 11))  # True
```

The example treats the active interval as (now - window_seconds, now], removing timestamps at or before the cutoff.

## 5. Complexity Analysis

Let R be the number of request timestamps currently stored for a user.

| Operation | Fixed window | Sliding log |
|---|---|---|
| Request check | O(1) average | Amortized O(1) per request |
| Stored state | One counter per active window | One timestamp per accepted request |
| Main limitation | Window-boundary bursts | Higher memory use |

For the sliding log, each timestamp is appended once and removed at most once. A single request can still remove several expired timestamps.

## 6. Important Production Considerations

A real distributed rate limiter also needs to consider:

- **Concurrency:** Two requests must not both exceed the limit due to a race condition.
- **Multiple servers:** Per-process dictionaries do not share state.
- **Expiration:** Old counters and timestamps must be cleaned up.
- **Identity:** Decide whether limits apply per user, API key, IP address, or endpoint.
- **Failure behavior:** Decide what happens if the rate-limiting service is unavailable.
- **HTTP responses:** APIs commonly return status code 429 when a client exceeds its limit.
- **Time source:** Use a consistent clock strategy, especially in distributed systems.

Redis with atomic operations or Lua scripts is a common building block for distributed implementations.

## 7. Interview Questions

1. What is rate limiting, and why is it necessary?
2. Compare fixed window and sliding window approaches.
3. Why can fixed windows allow bursts at boundaries?
4. How does token bucket differ from leaky bucket?
5. Why is a local dictionary insufficient across multiple application servers?
6. How would you test the time-window boundaries?
7. How would you make request counting atomic?
8. How would you limit requests separately for each user?

## 8. Practice Tasks

- [ ] Implement a fixed window counter.
- [ ] Implement a sliding window log.
- [ ] Add cleanup for inactive users.
- [ ] Write unit tests for the exact cutoff boundary.
- [ ] Design a token bucket limiter.
- [ ] Explain how to share rate-limit state across multiple servers.
