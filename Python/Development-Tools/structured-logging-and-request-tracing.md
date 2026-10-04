
# Structured Logging and Request Tracing in Python

## 1. What Is Structured Logging?

Structured logging records log events as organized fields rather than only as free-form text.

Traditional log:

```text
2026-10-04 ERROR Payment failed for order 123
```

Structured log:

```json
{
  "level": "ERROR",
  "event": "payment_failed",
  "order_id": "123",
  "service": "checkout"
}
```

Structured logs are easier for log platforms to filter, search, aggregate, and analyze.

Common fields include:
- Timestamp
- Log level
- Event name
- Service name
- Request ID
- User or order reference, when appropriate
- Error details

## 2. Why Structured Logging Matters

Structured logs help developers:
- Find errors quickly.
- Filter events by request or transaction.
- Investigate failures across services.
- Monitor application behavior.
- Build dashboards and alerts.
- Diagnose production incidents.

For example, instead of searching for a particular sentence, an engineer can filter all events where `request_id` matches a particular value.

## 3. Use Python's Logging Module

Python provides the built-in `logging` module.

```python
import logging

logger = logging.getLogger(__name__)

logger.info("Application started")
logger.warning("Retrying request")
logger.error("Payment processing failed")
```

Avoid using `print()` for production application logging because it lacks the standard logging levels, handler configuration, and filtering features.

## 4. Use Named Loggers

Create a logger for each module:

```python
import logging

logger = logging.getLogger(__name__)


def calculate_total(prices: list[float]) -> float:
    logger.info("Calculating total for %d items", len(prices))
    return sum(prices)
```

Using `__name__` identifies the module that emitted the message.

Use parameterized logging:

```python
logger.info("Processed order %s", order_id)
```

This is generally preferable to eagerly formatting the message with an f-string:

```python
logger.info(f"Processed order {order_id}")
```

Parameterized logging can defer formatting when the message is filtered out.

## 5. Emit JSON Logs

The following example uses Python's standard library to produce JSON-formatted log records.

```python
import json
import logging
from datetime import datetime, timezone


class JsonFormatter(logging.Formatter):
    def format(self, record: logging.LogRecord) -> str:
        event = {
            "timestamp": datetime.now(timezone.utc).isoformat(),
            "level": record.levelname,
            "logger": record.name,
            "message": record.getMessage(),
        }

        if hasattr(record, "request_id"):
            event["request_id"] = record.request_id

        if record.exc_info:
            event["exception"] = self.formatException(record.exc_info)

        return json.dumps(event)


handler = logging.StreamHandler()
handler.setFormatter(JsonFormatter())

logger = logging.getLogger("my_app")
logger.setLevel(logging.INFO)
logger.handlers.clear()
logger.addHandler(handler)
logger.propagate = False

logger.info(
    "Request completed",
    extra={"request_id": "req-123"},
)
```

Example output:

```json
{
  "timestamp": "2026-10-04T12:00:00+00:00",
  "level": "INFO",
  "logger": "my_app",
  "message": "Request completed",
  "request_id": "req-123"
}
```

The timestamp shown above is illustrative; the actual timestamp is generated when the record is formatted.

For larger applications, consider a maintained structured-logging library or the logging configuration already provided by your platform.

## 6. Understand Request IDs

A request ID is a unique identifier associated with a particular request.

Suppose a request passes through these components:

```text
Client
  |
  v
API Gateway
  |
  v
Python API
  |
  v
Database
  |
  v
External Payment Service
```

If each component records the same request ID, developers can correlate related events.

Example:

```text
INFO request_started request_id=req-abc
INFO database_query_completed request_id=req-abc
ERROR payment_failed request_id=req-abc
INFO request_completed request_id=req-abc
```

A request ID helps connect log events, but it does not by itself provide complete distributed tracing.

## 7. Pass Request Context to Log Records

For a small example, Python's `contextvars` can hold request-specific context.

This is useful in asynchronous applications because context variables are designed to work with asynchronous task contexts.

```python
import logging
from contextvars import ContextVar

request_id_var: ContextVar[str] = ContextVar(
    "request_id",
    default="-",
)


class RequestIdFilter(logging.Filter):
    def filter(self, record: logging.LogRecord) -> bool:
        record.request_id = request_id_var.get()
        return True


handler = logging.StreamHandler()
handler.addFilter(RequestIdFilter())

handler.setFormatter(
    logging.Formatter(
        "%(levelname)s request_id=%(request_id)s %(message)s"
    )
)

logger = logging.getLogger("request_app")
logger.setLevel(logging.INFO)
logger.handlers.clear()
logger.addHandler(handler)
logger.propagate = False


def process_request(request_id: str) -> None:
    token = request_id_var.set(request_id)

    try:
        logger.info("Processing request")
        logger.info("Request completed")
    finally:
        request_id_var.reset(token)


process_request("req-456")
```

The `finally` block resets the context to prevent request-specific data from leaking into later work.

In web frameworks, middleware is often the appropriate place to set and reset request context.

## 8. Log Exceptions Correctly

Use `logger.exception()` inside an exception handler when a traceback is useful.

```python
import logging

logger = logging.getLogger(__name__)


def load_configuration() -> None:
    try:
        with open("settings.json", encoding="utf-8") as file:
            file.read()
    except OSError:
        logger.exception("Could not load configuration")
        raise
```

`logger.exception()` includes exception information and is intended for use inside an exception handler.

Do not silently swallow exceptions just because they have been logged. Decide whether to recover, retry safely, or propagate the error.

## 9. Choose Appropriate Log Levels

| Level | Typical use |
|---|---|
| DEBUG | Detailed diagnostic information |
| INFO | Normal application milestones |
| WARNING | Unexpected conditions that may be recoverable |
| ERROR | An operation failed |
| CRITICAL | A severe failure that may prevent continued operation |

Example:

```python
logger.debug("Cache lookup started")
logger.info("Order created")
logger.warning("Retrying after temporary failure")
logger.error("Report generation failed")
logger.critical("Required service is unavailable")
```

Do not log every event at `ERROR`. Incorrect log levels make alerts less useful.

## 10. Avoid Logging Sensitive Information

Never log:
- Passwords
- API keys
- Authentication tokens
- Full payment card details
- Private keys
- Sensitive personal information without a valid, controlled need

Avoid logging entire request headers or bodies without reviewing their contents.

Instead of:

```python
logger.info("Login details: %s", credentials)
```

Prefer:

```python
logger.info("Login attempt received")
```

If identifiers are needed for troubleshooting, use appropriate non-sensitive identifiers and follow your organization's data-handling requirements.

## 11. Log Events, Not Just Sentences

For production systems, use consistent event names.

Examples:
- `request_started`
- `request_completed`
- `database_query_failed`
- `cache_miss`
- `payment_authorization_failed`

A consistent event name makes it easier to count events and create dashboards.

A useful event should answer:
- What happened?
- Where did it happen?
- When did it happen?
- Which request or operation was involved?
- What action should be taken, if any?

Avoid excessively verbose logging that creates high storage costs or exposes sensitive data.

## 12. Request IDs vs Distributed Tracing

These concepts are related but different.

**Request ID**
- Identifies a request or operation.
- Helps correlate log records.
- Can be passed between services.

**Distributed tracing**
- Represents work as traces and spans.
- Shows relationships and timing across services.
- Can identify latency bottlenecks and failed components.

For distributed applications, OpenTelemetry is a common option for instrumentation and trace-context propagation.

Do not assume that a request ID is automatically compatible with a tracing system's trace ID.

## 13. Production Logging Checklist

Before deploying an application, check that:

- [ ] Log levels are configured appropriately.
- [ ] Timestamps include a consistent timezone, preferably UTC.
- [ ] Logs contain useful service and request context.
- [ ] Exceptions include useful diagnostic details.
- [ ] Sensitive information is excluded or safely redacted.
- [ ] Log volume is reasonable.
- [ ] Log rotation or centralized collection is configured where needed.
- [ ] Request context is reset after each request.
- [ ] Alerts are based on meaningful failure conditions.
- [ ] Logs do not replace metrics or distributed tracing.

## 14. Interview Questions

**Q1. What is structured logging?**

Recording log events as organized fields, such as JSON key-value pairs, instead of relying only on free-form text.

**Q2. What is a request ID?**

An identifier used to correlate events associated with a particular request.

**Q3. What is the difference between a request ID and a trace ID?**

A request ID is generally used to correlate application events. A trace ID identifies a distributed trace, which can contain multiple spans across services.

**Q4. Why use `logger.exception()`?**

It records a message along with exception information, including the traceback, when called inside an exception handler.

**Q5. Why should secrets not be logged?**

Logs may be retained, indexed, copied, and accessed by more people or systems than the original secret. Exposing secrets in logs can create a security incident.

**Q6. What is `contextvars` used for?**

It stores context-local values that work well with asynchronous task contexts, such as request-specific identifiers.

## 15. Practice Tasks

- [ ] Create a JSON logging formatter.
- [ ] Add timestamps and log levels to every record.
- [ ] Include a request ID in log records.
- [ ] Log an exception with its traceback.
- [ ] Add request context using `contextvars`.
- [ ] Ensure context is reset after each request.
- [ ] Review logs for sensitive information.
- [ ] Explain the difference between logging and distributed tracing.

## Key Takeaways

- Structured logs make production events easier to search and analyze.
- Named loggers and appropriate levels improve maintainability.
- Request IDs connect related log events.
- `contextvars` can carry request-specific context.
- Exceptions should be logged meaningfully without hiding failures.
- Never expose secrets in application logs.
- Use distributed tracing when you need to understand requests across multiple services.
