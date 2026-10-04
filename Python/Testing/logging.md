# Python Logging

## 1. What is logging?
Logging records events that occur while a program runs. It helps developers understand program behavior, diagnose errors, and monitor applications.

## 2. Why use logging instead of print()?
- `print()` is useful for quick debugging.
- Logging supports severity levels, timestamps, formatting, and output destinations.
- Logging can be configured without rewriting every logging statement.

## 3. The logging module
Python includes a built-in `logging` module.

```python
import logging

logging.basicConfig(level=logging.INFO)

logging.debug("Detailed diagnostic information")
logging.info("Application started")
logging.warning("Unexpected situation")
logging.error("An operation failed")
logging.critical("A serious failure occurred")
```

The default logging level is usually `WARNING`, so DEBUG and INFO messages are hidden unless the level is configured.

## 4. Logging levels
| Level | Purpose |
|---|---|
| DEBUG | Detailed diagnostic information |
| INFO | Confirmation that something is working as expected |
| WARNING | An unexpected situation or potential problem |
| ERROR | An operation failed |
| CRITICAL | A serious failure that may prevent continued operation |

The standard severity order is DEBUG, INFO, WARNING, ERROR, CRITICAL.

## 5. Configure logging
```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

logging.info("Program started")
logging.warning("Check the input data")
```

Common format fields include:
- `%(asctime)s`: timestamp
- `%(levelname)s`: severity level
- `%(message)s`: log message
- `%(name)s`: logger name

`basicConfig()` normally configures the root logger only if it has not already been configured by handlers.

## 6. Log to a file
```python
import logging

logging.basicConfig(
    filename="app.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

logging.info("Application started")
logging.error("Could not process a record")
```

Messages at INFO level and above are written to `app.log` in this configuration.

## 7. Use a named logger
For larger applications, create a logger for each module.

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

logger.info("Module initialized")
```

Applications can configure handlers and formatters centrally, typically in the entry-point module.

## 8. Log exceptions
Use `logger.exception()` inside an exception handler to include exception details and the traceback.

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

try:
    result = 10 / 0
except ZeroDivisionError:
    logger.exception("Calculation failed")
```

`logger.exception()` is intended to be called from an exception handler.

## 9. Lazy formatting
Pass variable values as logging arguments rather than formatting strings unnecessarily.

```python
logger = logging.getLogger(__name__)
user_id = 42
logger.info("Processing user_id=%s", user_id)
```

The logging system can defer message formatting until the message needs to be emitted.

## 10. Logging in a data science project
```python
import logging
import pandas as pd

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

try:
    df = pd.read_csv("data.csv")
    logger.info("Loaded %d rows and %d columns", len(df), len(df.columns))
except FileNotFoundError:
    logger.exception("Dataset file was not found")
    raise
```

This example records dataset-loading information and logs a traceback if the file is missing. It re-raises the error so the failure is not silently ignored.

## 11. Best practices
- Use meaningful messages that explain what happened.
- Choose a suitable severity level.
- Avoid logging passwords, API keys, personal data, or other secrets.
- Prefer named loggers in multi-module applications.
- Include tracebacks when diagnosing unexpected exceptions.
- Configure logging centrally for larger projects.
- Use rotating file handlers if log files could grow indefinitely.

## 12. Practice problems
1. Log a message at each of the five standard levels.
2. Configure logging to include timestamps and severity levels.
3. Write log messages to a file.
4. Log an exception caused by invalid numeric input.
5. Add logging to a CSV-loading script and record the number of rows loaded.

## 13. Interview questions
1. What is logging, and why is it useful?
2. What are the five standard logging levels?
3. What is the difference between `print()` and logging?
4. What does `logging.basicConfig()` do?
5. What is the difference between `logger.error()` and `logger.exception()`?
6. Why should applications avoid logging secrets?
7. What is a named logger, and why use `__name__`?
