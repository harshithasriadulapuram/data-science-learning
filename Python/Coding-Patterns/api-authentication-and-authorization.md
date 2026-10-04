
# API Authentication and Authorization

## 1. What Is Authentication?

Authentication verifies **who a user or client is**.

Examples:
- Logging in with a username and password.
- Verifying an API key.
- Validating an access token.

## 2. What Is Authorization?

Authorization determines **what an authenticated user is allowed to do**.

For example:
- A regular user can view their own profile.
- An administrator can manage users.
- A read-only client can view records but cannot modify them.

### Authentication vs. Authorization

| Authentication | Authorization |
|---|---|
| Verifies identity | Checks permissions |
| Answers "Who are you?" | Answers "What can you access?" |
| Often happens first | Usually follows authentication |
| Example: validating a token | Example: checking an admin role |

## 3. Common Authentication Methods

### A. API Keys

An API key identifies an application or client.

Example request header:

```http
X-API-Key: your-api-key
```

Use API keys carefully. They should not be embedded in public frontend code, and they should be rotated if exposed.

### B. Session-Based Authentication

1. The user submits login credentials.
2. The server verifies them.
3. The server creates a session.
4. The browser stores a session identifier in a cookie.
5. The server checks the session on subsequent requests.

Use secure cookie settings such as `HttpOnly`, `Secure`, and an appropriate `SameSite` policy.

### C. Token-Based Authentication

A client sends an access token with its request.

```http
Authorization: Bearer YOUR_ACCESS_TOKEN
```

The server validates the token before allowing access.

Tokens must be protected like credentials. Use HTTPS and avoid putting tokens in URLs.

### D. OAuth 2.0

OAuth 2.0 is an authorization framework that allows a client to obtain limited access to a resource on behalf of a user or itself.

It is commonly used when applications need delegated access to services.

### E. OpenID Connect (OIDC)

OpenID Connect builds on OAuth 2.0 and adds an identity layer for user authentication.

**Remember:** OAuth 2.0 and OpenID Connect are related, but they are not interchangeable.

## 4. HTTP Status Codes

| Status | Meaning | Example |
|---|---|---|
| 200 | OK | Request succeeded |
| 201 | Created | Resource created |
| 400 | Bad Request | Invalid request data |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated user lacks permission |
| 404 | Not Found | Resource does not exist |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Unexpected server failure |

A `401` generally means valid authentication is missing or absent. A `403` generally means the server understood the request but refuses access.

## 5. Simple Flask Authentication Example

Install dependencies:

```bash
pip install flask
```

Create `app.py`:

```python
import os
from functools import wraps

from flask import Flask, jsonify, request

app = Flask(__name__)

# Set this in your environment; never commit real secrets.
API_TOKEN = os.environ.get("API_TOKEN")


def require_token(route_function):
    @wraps(route_function)
    def wrapper(*args, **kwargs):
        if not API_TOKEN:
            return jsonify({"error": "Server authentication is not configured"}), 500

        auth_header = request.headers.get("Authorization", "")
        expected_header = f"Bearer {API_TOKEN}"

        if auth_header != expected_header:
            return jsonify({"error": "Unauthorized"}), 401

        return route_function(*args, **kwargs)

    return wrapper


@app.get("/api/profile")
@require_token
def get_profile():
    return jsonify({
        "id": 1,
        "name": "Harshitha"
    })


if __name__ == "__main__":
    app.run(debug=True)
```

Set a token in your terminal before running the application.

PowerShell:

```powershell
$env:API_TOKEN = "replace-with-a-long-random-test-token"
python app.py
```

Test the protected endpoint using an API client such as Postman, with this request header:

```http
Authorization: Bearer replace-with-a-long-random-test-token
```

Without the correct header, the endpoint returns `401`.

**Important:** This is a learning example, not a complete production authentication system. Production systems need robust token management, secure secret storage, HTTPS, appropriate logging, and carefully designed authorization rules. Disable debug mode in production.

## 6. Role-Based Access Control (RBAC)

RBAC assigns permissions through roles.

Example:

| Role | Permissions |
|---|---|
| Viewer | Read records |
| Editor | Read and update records |
| Admin | Manage users and settings |

A server must check permissions for every protected operation. Hiding a button in the frontend is not sufficient authorization.

## 7. Security Best Practices

- Always use HTTPS for real credentials and tokens.
- Never commit passwords, API keys, or access tokens to GitHub.
- Store secrets in environment variables or a dedicated secret manager.
- Use established authentication libraries and identity providers.
- Hash passwords with a purpose-built password-hashing algorithm.
- Apply least-privilege permissions.
- Validate authorization on the server for every protected resource.
- Expire, rotate, and revoke credentials appropriately.
- Rate-limit sensitive endpoints.
- Avoid returning sensitive details in error messages.
- Do not log raw passwords, tokens, or API keys.

## 8. Interview Questions

1. What is authentication?
2. What is authorization?
3. Explain the difference between `401` and `403`.
4. What is an API key?
5. What is the difference between session-based and token-based authentication?
6. What is a bearer token?
7. Explain OAuth 2.0 and OpenID Connect.
8. What is role-based access control?
9. Why should secrets never be committed to GitHub?
10. Why is frontend-only authorization insecure?
11. How would you protect an API endpoint?
12. What is the principle of least privilege?

## 9. Practice Tasks

- [ ] Create a protected Flask endpoint.
- [ ] Test requests with and without a valid token.
- [ ] Return the appropriate HTTP status codes.
- [ ] Add a simple role-permission check.
- [ ] Write tests for authenticated and unauthorized requests.
- [ ] Add a README section explaining how to configure the token safely.

## Key Takeaway

Authentication establishes identity; authorization controls access. Secure APIs need both, along with safe credential handling and server-side permission checks.
