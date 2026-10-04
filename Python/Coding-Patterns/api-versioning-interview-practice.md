
# API Versioning — Interview Practice

## 1. What Is API Versioning?

API versioning allows developers to introduce changes to an API without unexpectedly breaking existing clients.

For example, mobile applications may continue using version 1 while newer applications use version 2.

## 2. Why Is API Versioning Needed?

- Maintain backward compatibility.
- Introduce new features safely.
- Change request or response formats.
- Deprecate old functionality gradually.
- Give clients time to migrate.

## 3. Common API Versioning Strategies

### A. URI Path Versioning

The version appears in the URL.

```text
GET /api/v1/users
GET /api/v2/users
```

**Advantages**
- Easy to understand.
- Easy to test in a browser or API client.
- Straightforward routing and documentation.

**Disadvantages**
- Creates different URLs for different versions.
- May require maintaining multiple routes.

### B. Query Parameter Versioning

The version is provided as a query parameter.

```text
GET /api/users?version=1
GET /api/users?version=2
```

**Advantages**
- Simple to implement.
- Keeps the main path unchanged.

**Disadvantages**
- Clients may accidentally omit the version.
- Version handling can be less visible.

### C. Header Versioning

The client specifies the version in an HTTP header.

```http
GET /api/users
API-Version: 2
```

**Advantages**
- Keeps URLs clean.
- Separates version selection from resource paths.

**Disadvantages**
- Less obvious when manually testing.
- Requires correct header configuration.

### D. Media Type Versioning

The version is included in the `Accept` header.

```http
Accept: application/vnd.example.v2+json
```

This approach uses content negotiation to select a representation.

## 4. Backward-Compatible vs. Breaking Changes

### Usually backward-compatible

- Adding an optional response field.
- Adding a new endpoint.
- Adding an optional request parameter.
- Introducing a new feature without changing existing behavior.

### Potentially breaking

- Removing a response field.
- Renaming a field.
- Changing a field's data type.
- Making an optional parameter required.
- Changing the meaning of an existing field.
- Changing authentication requirements.

Compatibility depends on how clients use the API, so even apparently small changes should be evaluated carefully.

## 5. Example: Two API Versions

### Version 1 response

```json
{
  "id": 101,
  "name": "Harshitha"
}
```

### Version 2 response

```json
{
  "id": 101,
  "full_name": "Harshitha",
  "email": "harshitha@example.com"
}
```

Renaming `name` to `full_name` can break existing clients. Keeping the old contract available while clients migrate can prevent unexpected failures.

## 6. Simple Flask Example

Install Flask:

```bash
pip install flask
```

Create `app.py`:

```python
from flask import Flask, jsonify

app = Flask(__name__)

users = {
    1: {
        "id": 1,
        "name": "Harshitha",
        "email": "harshitha@example.com"
    }
}


@app.get("/api/v1/users/<int:user_id>")
def get_user_v1(user_id):
    user = users.get(user_id)

    if user is None:
        return jsonify({"error": "User not found"}), 404

    return jsonify({
        "id": user["id"],
        "name": user["name"]
    })


@app.get("/api/v2/users/<int:user_id>")
def get_user_v2(user_id):
    user = users.get(user_id)

    if user is None:
        return jsonify({"error": "User not found"}), 404

    return jsonify({
        "id": user["id"],
        "full_name": user["name"],
        "email": user["email"]
    })


if __name__ == "__main__":
    app.run(debug=True)
```

Run the application:

```bash
python app.py
```

Test these URLs:

```text
http://127.0.0.1:5000/api/v1/users/1
http://127.0.0.1:5000/api/v2/users/1
```

Both versions use the same underlying user data but return different response contracts.

**Note:** This is a learning example. Disable Flask debug mode in production.

## 7. Deprecation and Migration

A responsible API migration generally follows these steps:

1. Announce the upcoming change.
2. Publish documentation for the new version.
3. Give clients a reasonable migration period.
4. Monitor usage of the older version.
5. Communicate a retirement date.
6. Retire the old version after the announced support period, subject to the service's policies and obligations.

Avoid removing an old version without understanding which clients still depend on it.

## 8. Best Practices

- Choose a consistent versioning strategy.
- Document every supported version.
- Avoid unnecessary breaking changes.
- Validate requests consistently across versions.
- Monitor errors and usage by version.
- Provide migration guides.
- Define a clear deprecation policy.
- Add automated tests for each supported contract.

## 9. Interview Questions

1. What is API versioning?
2. Why do APIs need versioning?
3. Explain URI, query parameter, and header versioning.
4. Which API versioning strategy would you choose and why?
5. What is backward compatibility?
6. Give three examples of breaking API changes.
7. How would you migrate clients from version 1 to version 2?
8. What is API deprecation?
9. How can automated tests help prevent API compatibility problems?
10. How would you identify clients still using an old API version?

## 10. Practice Tasks

- [ ] Implement two API versions using Flask.
- [ ] Add a new optional field without breaking version 1.
- [ ] Write tests for successful responses and missing users.
- [ ] Document the differences between version 1 and version 2.
- [ ] Write a migration plan for retiring version 1.

## Key Takeaway

API versioning helps teams evolve software while protecting existing clients. Good versioning combines a clear strategy, backward compatibility, documentation, testing, and a responsible deprecation process.
