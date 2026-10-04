
# API Error Handling and Exception Design

## 1. What Is API Error Handling?

API error handling is the process of detecting failures and returning meaningful, consistent responses without exposing sensitive internal details.

Examples:
- Invalid request data.
- Missing resources.
- Authentication failures.
- Database errors.
- Unexpected server failures.

Good error handling helps clients understand what went wrong and how to respond.

## 2. HTTP Status Codes for Errors

| Status | Meaning | Example |
|---|---|---|
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Requested record does not exist |
| 409 | Conflict | Duplicate resource or conflicting state |
| 422 | Unprocessable Content | Valid syntax but invalid input semantics |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Unexpected server failure |
| 503 | Service Unavailable | Temporary service unavailability |

Choose status codes according to the API contract and the actual failure.

## 3. Design a Consistent Error Response

Example:

```json
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "The requested user does not exist."
  }
}
```

A more detailed response might include a request identifier:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid fields.",
    "details": [
      {
        "field": "email",
        "message": "A valid email address is required."
      }
    ],
    "request_id": "req-123"
  }
}
```

Do not return stack traces, database credentials, internal file paths, or other sensitive implementation details to clients.

## 4. Python Exceptions vs. API Errors

A Python exception represents an error or unusual condition inside the application.

An API error is the HTTP response the client receives.

For example, a missing dictionary key might raise `KeyError`, but the API should usually translate that into a suitable response instead of exposing the raw exception.

```python
def get_user(users, user_id):
    if user_id not in users:
        raise ValueError("User does not exist")

    return users[user_id]
```

For larger applications, define domain-specific exceptions rather than using a broad exception type for every failure.

## 5. Simple Flask Error Handling

Install Flask:

```bash
pip install flask
```

Create `app.py`:

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

users = {
    1: {"id": 1, "name": "Harshitha"}
}


@app.get("/api/users/<int:user_id>")
def get_user(user_id):
    user = users.get(user_id)

    if user is None:
        return jsonify({
            "error": {
                "code": "USER_NOT_FOUND",
                "message": "The requested user does not exist."
            }
        }), 404

    return jsonify(user), 200


@app.post("/api/users")
def create_user():
    data = request.get_json(silent=True)

    if not isinstance(data, dict):
        return jsonify({
            "error": {
                "code": "INVALID_JSON",
                "message": "A JSON object is required."
            }
        }), 400

    name = data.get("name")

    if not isinstance(name, str) or not name.strip():
        return jsonify({
            "error": {
                "code": "VALIDATION_ERROR",
                "message": "A non-empty name is required."
            }
        }), 400

    new_id = max(users, default=0) + 1

    user = {
        "id": new_id,
        "name": name.strip()
    }

    users[new_id] = user

    return jsonify(user), 201


@app.errorhandler(404)
def handle_not_found(error):
    return jsonify({
        "error": {
            "code": "NOT_FOUND",
            "message": "The requested resource was not found."
        }
    }), 404


@app.errorhandler(500)
def handle_internal_error(error):
    app.logger.error("Unexpected server error")

    return jsonify({
        "error": {
            "code": "INTERNAL_SERVER_ERROR",
            "message": "An unexpected error occurred."
        }
    }), 500


if __name__ == "__main__":
    app.run(debug=True)
```

**Important:** This is a learning example using in-memory data. Production applications need database transactions, stronger validation, logging with request identifiers, and careful handling of unexpected exceptions. Never run with debug mode enabled in production.

## 6. Using Custom Exceptions

Custom exceptions make application errors easier to distinguish.

```python
class UserNotFoundError(Exception):
    """Raised when a requested user does not exist."""


def find_user(users, user_id):
    user = users.get(user_id)

    if user is None:
        raise UserNotFoundError(
            f"User {user_id} does not exist"
        )

    return user
```

The API layer can catch `UserNotFoundError` and translate it into a `404` response.

Keep business logic separate from HTTP response construction where practical.

## 7. When Should You Catch Exceptions?

Catch an exception when you can handle it meaningfully, translate it into an appropriate application error, or add useful context.

Avoid this pattern:

```python
try:
    perform_operation()
except Exception:
    return {"error": "Something went wrong"}
```

It can hide programming bugs and remove useful diagnostic information.

Prefer handling expected exceptions specifically:

```python
try:
    age = int("invalid")
except ValueError:
    print("The value must be an integer.")
```

For unexpected failures, use centralized error handling and record diagnostic details securely.

## 8. Logging and Observability

Useful logs may include:
- Request or correlation ID.
- Endpoint and HTTP method.
- Error category.
- Exception details in protected server logs.
- Timing and dependency failures.

Avoid logging passwords, access tokens, API keys, or unnecessary personal data.

A client-facing error message and a server-side diagnostic log serve different purposes.

## 9. Best Practices

- Use consistent error response formats.
- Return suitable HTTP status codes.
- Validate input at system boundaries.
- Handle expected exceptions explicitly.
- Centralize handling of unexpected failures.
- Preserve diagnostic details in secure logs.
- Avoid exposing sensitive implementation details.
- Document errors in API documentation.
- Test both successful and failing requests.
- Keep error messages useful but safe.

## 10. Interview Questions

1. What is API error handling?
2. What is the difference between a Python exception and an HTTP error?
3. When should an API return 400, 404, or 500?
4. What is a custom exception?
5. Why should broad exception handling be avoided?
6. How would you design a standard error response?
7. Why should stack traces not be exposed to clients?
8. What information should be included in server logs?
9. How would you handle a database connection failure?
10. What is centralized exception handling?
11. How would you test API error responses?
12. How can request IDs help debug production failures?

## 11. Practice Tasks

- [ ] Return a structured 404 response for a missing user.
- [ ] Validate malformed or missing JSON.
- [ ] Create and handle a custom exception.
- [ ] Add consistent error codes to responses.
- [ ] Write tests for invalid input and missing resources.
- [ ] Add safe server-side logging for unexpected errors.
- [ ] Document the API's error response format.

## Key Takeaway

Reliable APIs handle failures deliberately. Use clear status codes, consistent error formats, appropriate exception handling, and secure logging to make systems easier to debug and safer to operate.
