# Idempotency Interview Practice in Python

## 1. What Is Idempotency?

An operation is idempotent if repeating it with the same input produces the same intended effect as performing it once.

For example, setting a user's status to "active" repeatedly can be idempotent.

By contrast, adding 100 rupees to an account balance repeatedly is not idempotent.

## 2. Why Is Idempotency Important?

In distributed systems, a client may not know whether a request succeeded.

For example:
1. A client sends a payment request.
2. The server processes the payment.
3. The response is lost because of a network failure.
4. The client retries the request.

Without deduplication, the payment might be processed twice.

An idempotency mechanism allows the server to recognize that the retry belongs to the same logical operation.

## 3. Idempotency Key

An idempotency key is a unique identifier supplied for a logical request.

Example:

```text
POST /payments
Idempotency-Key: payment-order-123
```

When the same logical request is retried with the same key, the server should return the recorded result or otherwise prevent the operation from being applied twice.

The exact behavior depends on the API contract.

## 4. A Simple In-Memory Example

```python
class IdempotencyStore:
    def __init__(self):
        self.results = {}

    def execute(self, key, operation):
        if key in self.results:
            return self.results[key]

        result = operation()
        self.results[key] = result

        return result


store = IdempotencyStore()
balance = {"amount": 1000}


def make_payment():
    balance["amount"] -= 100
    return {"status": "success", "amount": 100}


first = store.execute("order-123", make_payment)
second = store.execute("order-123", make_payment)

print(first)
print(second)
print(balance["amount"])  # 900
```

The operation runs once in this sequential example. The second call returns the stored result.

**Important limitation:** This in-memory implementation is for learning only. It is not safe for concurrent requests, multiple server processes, crashes, or persistent financial transactions.

## 5. Production Design Considerations

A production API generally needs to:

1. Receive an idempotency key.
2. Associate the key with the authenticated client and operation.
3. Validate that the same key is not reused with a different request payload.
4. Atomically reserve or record the key.
5. Perform the business operation with appropriate transactional guarantees.
6. Store the result or operation status.
7. Return a consistent result for legitimate retries.
8. Define expiration and recovery behavior for incomplete operations.

A database uniqueness constraint or atomic storage operation can help prevent concurrent requests from processing the same key twice.

For financial operations, the idempotency record and business transaction need carefully designed consistency guarantees.

## 6. Idempotency vs Deduplication

- **Idempotency:** Repeating an operation has the same intended effect as doing it once.
- **Deduplication:** Detecting and suppressing duplicate events or requests.

Deduplication is one way to implement idempotent behavior, but the concepts are not identical.

## 7. HTTP Methods and Idempotency

HTTP defines GET, PUT, and DELETE as idempotent methods in terms of their intended effect. POST is not inherently idempotent.

An API can still implement application-level idempotency for POST requests using idempotency keys.

For example, deleting an already deleted resource should not produce an additional deletion effect, even if the server returns a different status response.

## 8. Common Failure Scenarios

### Same key, same request
Return the existing operation result or status.

### Same key, different request
Reject the request according to the API contract.

### Two requests arrive concurrently
Use atomic reservation and transaction rules to avoid duplicate processing.

### Server crashes during processing
Persist enough state to recover or safely retry the operation.

### Client retries after a timeout
The server should determine whether the original operation completed before applying it again.

## 9. Interview Questions

1. What is idempotency?
2. Why is it important in payment systems?
3. What is an idempotency key?
4. How is idempotency different from deduplication?
5. Why is an in-memory dictionary insufficient in a distributed application?
6. How would you handle two simultaneous requests with the same key?
7. What should happen if a key is reused with a different payload?
8. How do database transactions help?
9. How does idempotency work with retry logic?
10. Why does a network timeout not prove that an operation failed?

## 10. Practice Tasks

- [ ] Implement a simple idempotency store.
- [ ] Test repeated requests using the same key.
- [ ] Reject reuse of a key with a different payload.
- [ ] Explain how to handle concurrent requests.
- [ ] Design persistent idempotency records.
- [ ] Describe how retries and idempotency work together.
