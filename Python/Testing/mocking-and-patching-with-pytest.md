# Mocking and Patching with Pytest

## 1. What Is Mocking?

Mocking replaces a real dependency with a controlled substitute during a test.

For example, an application may depend on:

* A database
* An external API
* An email service
* A payment gateway
* A file system operation

Calling these services during every unit test can make tests slow, expensive, or unreliable.

Mocking lets us test our application logic without invoking the real dependency.

## 2. What Is Patching?

Patching temporarily replaces an object or function at a particular location while a test runs.

Python provides `unittest.mock`, which includes:

* `Mock`: Creates a configurable mock object.
* `MagicMock`: Supports common Python magic methods.
* `patch`: Temporarily replaces an object.
* `patch.object`: Replaces an attribute on an existing object.
* `side_effect`: Configures exceptions or different results across calls.
* `return_value`: Defines the result of a mock call.

These tools work with pytest; you do not need an additional mocking package.

## 3. Your First Mock

```python
from unittest.mock import Mock

database = Mock()
database.get_user.return_value = {
    "id": 1,
    "name": "Harshitha"
}

user = database.get_user(1)

assert user["name"] == "Harshitha"
database.get_user.assert_called_once_with(1)
```

Here, `get_user()` does not query a real database. The mock returns the value configured in the test.

## 4. Mock an External API

Suppose your application calls an external service to retrieve a user's profile.

Create `profile_service.py`:

```python
import requests

def get_profile(user_id):
    response = requests.get(
        f"https://example.com/users/{user_id}",
        timeout=5
    )
    response.raise_for_status()
    return response.json()
```

Create `test_profile_service.py`:

```python
from unittest.mock import Mock, patch

from profile_service import get_profile

@patch("profile_service.requests.get")
def test_get_profile(mock_get):
    mock_response = Mock()
    mock_response.json.return_value = {
        "id": 1,
        "name": "Harshitha"
    }
    mock_response.raise_for_status.return_value = None

    mock_get.return_value = mock_response

    result = get_profile(1)

    assert result["name"] == "Harshitha"
    mock_get.assert_called_once_with(
        "https://example.com/users/1",
        timeout=5
    )
```

This test does not send a network request.

**Important:** Patch the name where your code looks it up. In this example, that is `profile_service.requests.get`.

## 5. Configure Different Results with `side_effect`

```python
from unittest.mock import Mock

service = Mock()
service.fetch.side_effect = [
    TimeoutError("Temporary failure"),
    {"status": "success"}
]

try:
    service.fetch()
except TimeoutError:
    pass

result = service.fetch()

assert result == {"status": "success"}
assert service.fetch.call_count == 2
```

An iterable `side_effect` returns successive values or raises successive exceptions. When the iterable is exhausted, further calls raise `StopIteration`.

You can also configure a mock to raise an exception on every call:

```python
from unittest.mock import Mock

service = Mock()
service.fetch.side_effect = TimeoutError("Unavailable")

try:
    service.fetch()
except TimeoutError:
    print("Handled the expected failure")
```

## 6. Mock a Database Dependency

Keep business logic separate from database access.

Create `user_service.py`:

```python
def get_user_name(repository, user_id):
    user = repository.get_user(user_id)

    if user is None:
        return "Unknown user"

    return user["name"]
```

Create `test_user_service.py`:

```python
from unittest.mock import Mock

from user_service import get_user_name

def test_get_user_name():
    repository = Mock()
    repository.get_user.return_value = {
        "id": 1,
        "name": "Harshitha"
    }

    result = get_user_name(repository, 1)

    assert result == "Harshitha"
    repository.get_user.assert_called_once_with(1)

def test_missing_user():
    repository = Mock()
    repository.get_user.return_value = None

    result = get_user_name(repository, 999)

    assert result == "Unknown user"
```

These are unit tests for business logic. They do not prove that the actual SQL or database schema works; use integration tests for that.

## 7. Use `monkeypatch` in Pytest

Pytest provides the `monkeypatch` fixture for temporarily changing attributes, environment variables, and other objects.

```python
import os

def get_database_url():
    return os.getenv("DATABASE_URL", "sqlite:///app.db")

def test_database_url(monkeypatch):
    monkeypatch.setenv(
        "DATABASE_URL",
        "sqlite:///test.db"
    )

    assert get_database_url() == "sqlite:///test.db"
```

Pytest restores the changed environment variable after the test finishes.

## 8. Mock vs. Real Dependency

| Situation                                     | Recommended approach                           |
| --------------------------------------------- | ---------------------------------------------- |
| Test business logic independently             | Mock the dependency                            |
| Verify SQL and constraints                    | Use a real test database                       |
| Test external API error handling              | Mock the HTTP response                         |
| Verify an API integration end to end          | Use a controlled integration environment       |
| Test configuration from environment variables | Use `monkeypatch`                              |
| Test a complex transaction                    | Use the actual database engine where practical |

Too much mocking can hide integration bugs. Mock only the dependencies that are outside the behavior you intend to test.

## 9. Common Mistakes

* Patching the wrong import location.
* Mocking the function being tested instead of its dependency.
* Asserting only the return value while ignoring important interactions.
* Using mocks to claim that SQL or database constraints work.
* Forgetting to configure exceptions and failure cases.
* Creating tests that depend on the order in which they run.

## 10. Interview Questions

1. What is mocking?
2. What is patching?
3. What is the difference between `Mock` and `MagicMock`?
4. What are `return_value` and `side_effect`?
5. Why should you patch an object where it is looked up?
6. What is the pytest `monkeypatch` fixture?
7. When should you use a real database instead of a mock?
8. How can excessive mocking make tests unreliable?

## 11. Practice Tasks

1. Mock a repository's `get_user()` method.
2. Test both successful and missing-user cases.
3. Mock an HTTP request and verify the timeout argument.
4. Configure a mock to raise `TimeoutError`.
5. Use `monkeypatch` to replace an environment variable.
6. Write one integration test that uses a real temporary SQLite database.
7. Explain which dependency you would mock when testing a payment service.
