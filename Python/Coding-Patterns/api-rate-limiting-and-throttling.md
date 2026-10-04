
# API Rate Limiting and Throttling

## 1. What Is Rate Limiting?

Rate limiting restricts how many requests a client can make during a defined period.

Example:

An API permits a client to make 100 requests per minute. Additional requests may be rejected until capacity becomes available.

Rate limiting helps:
- Prevent API abuse.
- Protect server resources.
- Reduce excessive traffic.
- Enforce usage quotas.
- Improve service reliability.

## 2. What Is Throttling?

Throttling controls how requests are handled when traffic becomes excessive.

Depending on the system, it may delay, queue, slow down, or reject requests.

**Difference:**
- Rate limiting defines a request allowance.
- Throttling controls traffic when that allowance or another capacity limit is reached.

The terms are sometimes used interchangeably in API documentation.

## 3. Common Rate-Limiting Algorithms

### A. Fixed Window

Count requests within fixed time intervals.

Example: 100 requests per minute.

**Problem:** A client may make 100 requests at the end of one minute and another 100 at the beginning of the next minute.

This can produce a burst of 200 requests over a short interval.

### B. Sliding Window Log

Store the timestamps of requests and count those within the most recent time window.

**Advantage:** Accurate sliding-window enforcement.

**Disadvantage:** Can consume substantial memory for high request volumes.

### C. Sliding Window Counter

Combine counts from adjacent windows to estimate requests in a rolling interval.

**Advantage:** Uses less memory than storing every request timestamp.

**Disadvantage:** May be approximate, depending on the implementation.

### D. Token Bucket

Tokens are added to a bucket at a configured rate. Each request consumes a token.

- If a token is available, allow the request.
- Otherwise, reject or defer it.
- A bucket capacity permits controlled bursts.

### E. Leaky Bucket

Requests are processed at a controlled rate, often through a queue.

Depending on the implementation, excess requests may be rejected when the queue is full.

## 4. HTTP 429 — Too Many Requests

When a client exceeds its request allowance, an API commonly returns:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
Content-Type: application/json
```

Example response body:

```json
{
  "error": "rate_limit_exceeded",
  "message": "Too many requests. Try again later."
}
```

`Retry-After` can communicate when the client should retry. Its value may be expressed as a delay in seconds or an HTTP date.

Some APIs also return headers describing the limit, remaining allowance, or reset time.

## 5. Simple In-Memory Flask Example

Install Flask:

```bash
pip install flask
```

Create `app.py`:

```python
from collections import defaultdict
from threading import Lock
from time import time

from flask import Flask, jsonify, make_response, request

app = Flask(__name__)

LIMIT = 5
WINDOW_SECONDS = 60

requests_by_client = defaultdict(list)
lock = Lock()


@app.get("/api/data")
def get_data():
    # Demonstration only: remote address is not a reliable identity
    # in every deployment, especially behind a proxy.
    client_id = request.remote_addr or "unknown"
    now = time()
    cutoff = now - WINDOW_SECONDS

    with lock:
        timestamps = requests_by_client[client_id]
        timestamps[:] = [
            timestamp for timestamp in timestamps
            if timestamp > cutoff
        ]

        if len(timestamps) >= LIMIT:
            retry_after = max(
                1,
                int(timestamps[0] + WINDOW_SECONDS - now + 0.999)
            )

            response = make_response(
                jsonify({
                    "error": "rate_limit_exceeded",
                    "message": "Too many requests. Try again later."
                }),
                429
            )
            response.headers["Retry-After"] = str(retry_after)
            return response

        timestamps.append(now)
        remaining = LIMIT - len(timestamps)

    response = make_response(jsonify({
        "message": "Request successful",
        "remaining_requests": remaining
    }))
    response.headers["X-RateLimit-Limit"] = str(LIMIT)
    response.headers["X-RateLimit-Remaining"] = str(remaining)

    return response


if __name__ == "__main__":
    app.run(debug=True)
```

### How it works

1. Identify the client.
2. Remove timestamps outside the rolling window.
3. Count requests still inside the window.
4. Return `429` if the limit has been reached.
5. Otherwise, record the request and return a successful response.

**Limitations:** This example is for learning. The data disappears when the process restarts, and multiple application workers do not share the same counter. Production systems commonly use a shared store such as Redis and a well-tested rate-limiting library. Client identity and proxy configuration must also be handled carefully.

## 6. Where Should Rate Limiting Be Applied?

Possible locations include:

- API gateway.
- Reverse proxy.
- Application middleware.
- Individual endpoints.
- Shared infrastructure such as Redis-backed middleware.

A gateway can enforce broad limits, while application-level rules can apply stricter limits to expensive operations.

## 7. Choosing a Rate-Limiting Key

Possible keys include:

- Authenticated user ID.
- API key.
- Organization or tenant ID.
- Client IP address.
- A combination of user and endpoint.

Choose the key based on the API's security and product requirements.

For example, an authenticated API may apply a user-level quota as well as an IP-based abuse limit.

Do not blindly trust client-supplied identity headers. Only trust proxy headers when the application is configured to accept them from known, trusted proxies.

## 8. Rate Limiting in Distributed Systems

When multiple API instances handle requests, each instance needs a consistent view of the applicable limit.

A shared store such as Redis can coordinate counters across instances.

Important considerations:
- Atomic counter updates.
- Expiration of old counters.
- Network failures and store outages.
- Consistent keys across instances.
- High-cardinality clients.
- Per-user and global limits.
- Whether to fail open or fail closed if the limiter becomes unavailable.

The correct failure strategy depends on the endpoint and its security requirements.

## 9. Client-Side Handling

When a client receives `429`:

1. Read the `Retry-After` header when present.
2. Wait before retrying.
3. Use exponential backoff when appropriate.
4. Add jitter to reduce synchronized retries.
5. Avoid retrying indefinitely.
6. Respect the API's documented quota rules.

Rate limiting should be paired with sensible retry behavior rather than aggressive repeated requests.

## 10. Interview Questions

1. What is API rate limiting?
2. How is throttling different from rate limiting?
3. Explain fixed-window and sliding-window algorithms.
4. How does the token bucket algorithm work?
5. Why does an API return HTTP 429?
6. What is the purpose of `Retry-After`?
7. Why might an in-memory limiter fail in a multi-worker deployment?
8. How can Redis help implement distributed rate limiting?
9. How would you rate-limit requests by authenticated user?
10. What are the trade-offs of IP-based rate limiting?
11. What is the difference between a per-user limit and a global limit?
12. How should a client respond to HTTP 429?

## 11. Practice Tasks

- [ ] Run the Flask example.
- [ ] Send six requests within one minute and inspect the responses.
- [ ] Confirm that the excess request receives HTTP 429.
- [ ] Inspect the `Retry-After` header.
- [ ] Add separate limits for two authenticated users.
- [ ] Write automated tests for allowed and rejected requests.
- [ ] Research how a Redis-backed limiter coordinates multiple application instances.

## Key Takeaway

A good API rate limiter protects service capacity without unnecessarily blocking legitimate users. Choose the algorithm, client identity, storage strategy, and retry policy according to the system's actual requirements.
