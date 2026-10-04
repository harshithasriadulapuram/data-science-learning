
# Python Logging Configuration and Best Practices

## 1. Why Configure Logging?

Logging records important events while an application runs.

A well-configured logging system helps developers:
- Investigate errors in production.
- Understand application behavior.
- Track external service failures.
- Monitor background jobs.
- Diagnose performance problems.

Python provides the built-in `logging` module.

For small scripts, basic logging configuration may be enough. Larger applications benefit from a consistent configuration shared across modules.

## 2. Understand Logging Levels

Python's standard logging levels are:

| Level | Numeric value | Purpose |
|---|---:|---|
| `DEBUG` | 10 | Detailed information for diagnosing problems |
| `INFO` | 20 | Normal application events |
| `WARNING` | 30 | Unexpected situations that do not stop execution |
| `ERROR` | 40 | An operation failed |
| `CRITICAL` | 50 | A serious failure that may prevent continued operation |

Example:

```python
import logging

logging.basicConfig(level=logging.DEBUG)

logging.debug("Debugging details")
logging.info("Application started")
logging.warning("Retrying a request")
logging.error("Request failed")
logging.critical("Application cannot continue")
```

The configured level determines which messages are eligible to be emitted. A handler may apply its own additional level filter.

## 3. Configure Logging With `basicConfig()`

For a simple application:

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(name)s | %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
)

logger = logging.getLogger(__name__)

logger.info("Application started")
logger.warning("Configuration value is missing")
```

Example output:

```text
2026-10-04 10:30:00 | INFO | __main__ | Application started
2026-10-04 10:30:00 | WARNING | __main__ | Configuration value is missing
```

The timestamps above are illustrative.

### Common Format Fields

- `%(asctime)s`: Time of the log record.
- `%(levelname)s`: Severity level.
- `%(name)s`: Logger name.
- `%(message)s`: Formatted log message.
- `%(filename)s`: Source filename.
- `%(lineno)d`: Source line number.

`basicConfig()` normally configures logging only when the root logger has no handlers. Existing application or framework configuration can therefore affect its behavior.

## 4. Use a Module-Level Logger

In reusable modules, prefer:

```python
import logging

logger = logging.getLogger(__name__)


def load_data() -> list[int]:
    logger.info("Loading data")

    data = [10, 20, 30]

    logger.info("Loaded %d records", len(data))
    return data
```

The `__name__` logger name helps identify where a message originated.

Use deferred formatting:

```python
logger.info("Loaded %d records", len(data))
```

rather than formatting the string before passing it to the logger:

```python
logger.info(f"Loaded {len(data)} records")
```

Deferred formatting can avoid unnecessary string formatting when a message is filtered out.

## 5. Log Exceptions Correctly

Use `logger.exception()` inside an exception handler when you want to include traceback information.

```python
import logging

logger = logging.getLogger(__name__)


def divide(a: float, b: float) -> float:
    try:
        return a / b
    except ZeroDivisionError:
        logger.exception("Cannot divide by zero")
        raise
```

The exception is logged and then re-raised so the caller can handle it.

Use `logger.exception()` within an active exception handler. Outside one, use `logger.error()` or another appropriate method.

Avoid catching exceptions just to log and silently ignore them. That can hide failures.

## 6. Write Logs to a File

Use `logging.FileHandler` when you want to store logs in a file.

```python
import logging

logging.basicConfig(
    filename="application.log",
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(message)s",
)

logger = logging.getLogger(__name__)

logger.info("Application started")
logger.error("A sample error occurred")
```

The log file is normally opened when logging begins to emit records, and its location is relative to the process's working directory unless an explicit path is provided.

Ensure the application has permission to write to the selected directory.

## 7. Configure Console and File Handlers

A handler controls where log records are sent.

For example, you can send informational messages to the console and retain a file log.

```python
import logging

logger = logging.getLogger("my_application")
logger.setLevel(logging.DEBUG)
logger.propagate = False

formatter = logging.Formatter(
    "%(asctime)s | %(levelname)s | %(name)s | %(message)s"
)

console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)
console_handler.setFormatter(formatter)

file_handler = logging.FileHandler("application.log", encoding="utf-8")
file_handler.setLevel(logging.DEBUG)
file_handler.setFormatter(formatter)

logger.addHandler(console_handler)
logger.addHandler(file_handler)

logger.debug("Detailed diagnostic information")
logger.info("Application started")
```

With this configuration:
- `DEBUG` records can be written to the file.
- `INFO` and higher-level records can be displayed in the console.
- `propagate = False` prevents these records from also propagating to ancestor loggers.

When configuring handlers in a reusable setup function, avoid adding duplicate handlers every time the function is called.

## 8. Rotate Log Files

A growing log file can consume excessive disk space.

Use `RotatingFileHandler` to limit the size of an individual log file and retain a specified number of backups.

```python
import logging
from logging.handlers import RotatingFileHandler

logger = logging.getLogger("my_application")
logger.setLevel(logging.INFO)
logger.propagate = False

handler = RotatingFileHandler(
    "application.log",
    maxBytes=1_000_000,
    backupCount=3,
    encoding="utf-8",
)

handler.setFormatter(
    logging.Formatter(
        "%(asctime)s | %(levelname)s | %(message)s"
    )
)

logger.addHandler(handler)

for number in range(1000):
    logger.info("Processing record %d", number)
```

When the file reaches the configured size, it rotates and retains a limited number of backup files.

For time-based rotation, use `TimedRotatingFileHandler`.

In multi-process applications, do not assume that multiple processes writing through separate rotating handlers to the same file will coordinate safely. Consider centralized logging or a queue-based logging architecture.

## 9. Use `dictConfig()` for Larger Applications

The `logging.config.dictConfig()` function allows logging to be configured from a dictionary.

This is useful when an application has multiple loggers and handlers.

```python
import logging
import logging.config

LOGGING_CONFIG = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "standard": {
            "format": (
                "%(asctime)s | %(levelname)s | "
                "%(name)s | %(message)s"
            )
        }
    },
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "level": "INFO",
            "formatter": "standard",
            "stream": "ext://sys.stdout",
        }
    },
    "root": {
        "level": "INFO",
        "handlers": ["console"],
    },
}

logging.config.dictConfig(LOGGING_CONFIG)

logger = logging.getLogger(__name__)
logger.info("Logging configured successfully")
```

For larger systems, keep configuration separate from business logic and load it once during application startup.

Only load logging configuration from trusted sources. Some logging configuration mechanisms can instantiate classes or call factories.

## 10. Logging in Data Science and ML Projects

Logging is useful when building data pipelines, model-training scripts, and inference services.

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(message)s",
)

logger = logging.getLogger(__name__)


def train_model() -> None:
    logger.info("Starting model training")

    # Load data, preprocess, and train the model here.
    records_loaded = 1000
    logger.info("Loaded %d records", records_loaded)

    model_name = "RandomForest"
    logger.info("Training model: %s", model_name)

    logger.info("Model training completed")


if __name__ == "__main__":
    train_model()
```

Log useful operational events, such as:
- Dataset loading and validation.
- Training start and completion.
- Model version and selected configuration.
- Evaluation results.
- Failed API calls and retries.

Avoid logging sensitive data, full datasets, passwords, API keys, or private user information.

## 11. Logging Best Practices

1. Use a named logger in each module.
2. Configure logging centrally when possible.
3. Choose appropriate log levels.
4. Include useful context without exposing sensitive information.
5. Log exceptions with traceback details when helpful.
6. Avoid duplicate handlers and duplicate messages.
7. Use rotation or centralized log storage where appropriate.
8. Prefer structured logs when downstream systems need to query log fields.
9. Keep logs useful and avoid excessive output.
10. Do not treat logs as a replacement for metrics, tracing, or monitoring.

In containerized production systems, applications commonly write logs to standard output and standard error so the platform can collect and manage them.

## 12. Common Mistakes

### Mistake 1: Using `print()` for all diagnostics

`print()` is useful for simple scripts, but logging supports levels, formatting, handlers, and integration with monitoring systems.

### Mistake 2: Logging and swallowing exceptions

Record the error and preserve correct error-handling behavior. Re-raise or handle the exception intentionally.

### Mistake 3: Configuring logging repeatedly

Repeated handler setup can produce duplicate messages. Configure handlers in a controlled place, usually during startup.

### Mistake 4: Logging sensitive information

Never expose credentials or confidential data in logs.

### Mistake 5: Allowing logs to grow indefinitely

Use rotation, retention policies, or a centralized logging service.

### Mistake 6: Using the wrong log level

Use `DEBUG` for diagnostic detail, `INFO` for normal events, `WARNING` for unexpected situations, and `ERROR` or `CRITICAL` for failures of appropriate severity.

## 13. Interview Questions

**Q1. What is the difference between `print()` and logging?**

Logging supports severity levels, configurable destinations, formatting, and integration with operational monitoring.

**Q2. What is a logger?**

A logger creates log records and passes them to configured handlers.

**Q3. What is a logging handler?**

A handler determines where log records are sent, such as the console or a file.

**Q4. What is a formatter?**

A formatter controls how log records are rendered as text.

**Q5. What is `logger.exception()` used for?**

It logs a message with exception information, typically inside an exception handler.

**Q6. Why rotate log files?**

Rotation limits individual log-file growth and can retain a bounded number of backups.

**Q7. What is `dictConfig()`?**

It configures Python logging using a dictionary describing loggers, handlers, formatters, and related settings.

## 14. Practice Tasks

- [ ] Configure a logger with timestamps and severity levels.
- [ ] Create a module-level logger using `logging.getLogger(__name__)`.
- [ ] Log an exception with its traceback.
- [ ] Write messages to both the console and a file.
- [ ] Configure a rotating file handler.
- [ ] Configure logging with `dictConfig()`.
- [ ] Add useful logging to a small data-processing script.
- [ ] Explain why credentials should never appear in logs.

## Key Takeaways

- Python's `logging` module supports configurable application logging.
- Loggers create records, handlers route them, and formatters control their appearance.
- Centralized configuration improves consistency.
- File rotation helps manage disk usage.
- Good logs support debugging and operations without exposing sensitive information.
