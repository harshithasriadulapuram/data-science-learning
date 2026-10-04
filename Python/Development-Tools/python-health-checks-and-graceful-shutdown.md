
# Python Health Checks and Graceful Shutdown

## 1. What Are Health Checks?

Health checks help determine whether an application or service is operating correctly.

They are commonly used by:
- Container platforms.
- Load balancers.
- Orchestrators such as Kubernetes.
- Monitoring systems.
- Deployment platforms.
- Backend APIs.

A health check should provide useful information without creating unnecessary load or exposing sensitive details.

## 2. Types of Health Checks

### Liveness Check

A liveness check determines whether an application is still running and able to make progress.

If a service becomes irrecoverably stuck, a platform may restart it.

A liveness check should generally avoid depending on every external service. Otherwise, a temporary database outage could cause repeated application restarts.

### Readiness Check

A readiness check determines whether an application is ready to receive requests.

A service may be alive but not ready because it is still initializing or cannot access a required dependency.

An orchestrator or load balancer can stop routing new requests to an unready instance.

### Startup Check

A startup check allows an application sufficient time to initialize before liveness checks begin.

This is useful for services that load models, initialize connections, or perform other startup work.

## 3. Health Check Comparison

| Check | Main question | Typical action |
|---|---|---|
| Liveness | Is the process healthy enough to continue? | Restart if it is irrecoverably unhealthy |
| Readiness | Can this instance accept requests now? | Stop routing traffic temporarily |
| Startup | Has initialization completed? | Delay other health checks until startup succeeds |

These checks have different purposes and should not automatically use identical logic.

## 4. Build a Basic Health Check with FastAPI

Install the required packages:

```bash
pip install fastapi uvicorn
```

Create `main.py`:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/health/live")
def liveness_check() -> dict[str, str]:
    return {"status": "alive"}


@app.get("/health/ready")
def readiness_check() -> dict[str, str]:
    # Replace this placeholder with real readiness checks.
    return {"status": "ready"}
```

Start the application:

```bash
uvicorn main:app --reload
```

Open the endpoints:

```text
http://127.0.0.1:8000/health/live
http://127.0.0.1:8000/health/ready
```

The example demonstrates endpoint structure. The readiness endpoint should only return a successful response when the service is actually ready.

## 5. Implement Readiness Checks Carefully

Suppose an application requires a database before it can serve requests.

A readiness check may verify database connectivity using the application's existing connection-management strategy.

Conceptually:

```python
def is_ready() -> bool:
    return (
        application_initialized()
        and required_database_is_available()
    )
```

The functions above are placeholders for application-specific checks.

Important practices:
- Use short timeouts.
- Avoid expensive queries.
- Avoid opening unlimited new connections.
- Check only dependencies essential for serving requests.
- Avoid making every liveness check depend on the database.
- Do not expose database credentials or internal connection details in responses.

For high-traffic applications, consider caching a recent dependency-health result rather than executing an expensive check for every probe.

## 6. What Is Graceful Shutdown?

Graceful shutdown is the process of stopping an application in an orderly manner.

Instead of terminating immediately, the application gets an opportunity to:
- Stop accepting new work.
- Finish in-progress requests when possible.
- Stop background tasks.
- Close database connections.
- Flush logs and telemetry.
- Release resources.

Graceful shutdown reduces the risk of interrupted operations and resource leaks.

It cannot guarantee that every operation finishes. The process may still be terminated when its shutdown deadline expires.

## 7. Handle Shutdown Signals in Python

Python applications can receive operating-system signals such as `SIGINT` and `SIGTERM`.

A basic example:

```python
import signal
import threading

shutdown_event = threading.Event()


def request_shutdown(
    signum: int,
    frame: object,
) -> None:
    shutdown_event.set()


signal.signal(signal.SIGINT, request_shutdown)
signal.signal(signal.SIGTERM, request_shutdown)


def main() -> None:
    print("Application started")

    try:
        while not shutdown_event.is_set():
            # Perform a small unit of work.
            shutdown_event.wait(timeout=1)
    finally:
        print("Cleaning up application resources")


if __name__ == "__main__":
    main()
```

This example signals the main loop to stop. Real applications must perform their actual cleanup in the `finally` block or in their framework's shutdown lifecycle.

Avoid doing extensive cleanup directly inside a signal handler. Set a flag or event and let normal application flow perform the cleanup.

## 8. Close Resources Properly

Use context managers when possible.

For a file:

```python
with open("output.txt", "w", encoding="utf-8") as file:
    file.write("Application completed")
```

The file is closed when the block exits.

For database connections and HTTP clients, use their documented context managers or explicit cleanup methods.

Example using a generic cleanup structure:

```python
def run_application() -> None:
    resource = create_resource()

    try:
        start_work(resource)
    finally:
        resource.close()
```

The functions are placeholders. Replace them with the actual resource creation, work, and cleanup operations for your application.

For connection pools, close the pool using the library's documented shutdown method.

## 9. Graceful Shutdown in FastAPI

FastAPI supports application lifespan handlers for startup and shutdown work.

```python
from contextlib import asynccontextmanager

from fastapi import FastAPI


@asynccontextmanager
async def lifespan(app: FastAPI):
    # Initialize resources before serving requests.
    app.state.initialized = True

    try:
        yield
    finally:
        # Close application resources here.
        app.state.initialized = False


app = FastAPI(lifespan=lifespan)


@app.get("/health/live")
async def liveness_check() -> dict[str, str]:
    return {"status": "alive"}


@app.get("/health/ready")
async def readiness_check() -> dict[str, str]:
    if not app.state.initialized:
        from fastapi import HTTPException

        raise HTTPException(
            status_code=503,
            detail="Service is not ready",
        )

    return {"status": "ready"}
```

This example demonstrates FastAPI's lifespan mechanism. In a production service, initialize real resources before `yield` and close them in the `finally` block.

If initialization fails, the application should not report itself as ready.

## 10. Shutdown in Containerized Applications

A container platform may send `SIGTERM` before eventually forcing termination.

A typical shutdown sequence is:

1. Mark the instance as not ready.
2. Stop receiving new traffic.
3. Allow in-flight requests to finish within the available deadline.
4. Stop background jobs.
5. Close database pools and network clients.
6. Flush logs and telemetry.
7. Exit before the platform's termination deadline.

The exact sequence depends on the server, framework, and deployment platform.

Configure the application's shutdown timeout and the platform's termination grace period consistently.

## 11. Common Mistakes

### Mistake 1: Using one check for everything

Liveness, readiness, and startup checks answer different questions. Design them separately.

### Mistake 2: Restarting an application because a dependency is temporarily unavailable

A database outage should not automatically cause every application instance to restart. Use readiness checks and dependency-recovery logic appropriately.

### Mistake 3: Performing expensive work during every health check

Health endpoints should be fast and predictable. Reuse existing health state or use bounded checks when appropriate.

### Mistake 4: Ignoring shutdown deadlines

The application may be forcibly terminated if cleanup takes too long.

### Mistake 5: Forgetting background tasks

Stop schedulers, workers, consumers, and other background processes deliberately.

### Mistake 6: Returning internal details in health responses

Avoid exposing stack traces, credentials, hostnames, or detailed infrastructure information to unauthenticated callers.

## 12. Interview Questions

**Q1. What is a health check?**

A mechanism used to determine whether an application is alive, ready, or has completed startup.

**Q2. What is the difference between liveness and readiness?**

Liveness checks whether the process should continue running. Readiness checks whether it should receive traffic.

**Q3. What is a startup probe?**

A check that allows a slow-starting application time to initialize before other health checks become active.

**Q4. What is graceful shutdown?**

An orderly termination process that stops new work, handles in-flight operations where possible, and releases resources.

**Q5. Why should liveness checks avoid external dependencies?**

A temporary dependency outage could otherwise cause unnecessary restarts and make an incident worse.

**Q6. Why is `finally` useful during cleanup?**

It allows cleanup code to execute when control leaves a `try` block, including when an exception occurs.

**Q7. Does graceful shutdown guarantee that all requests finish?**

No. Requests may exceed deadlines, encounter errors, or be interrupted by forced termination.

## 13. Practice Tasks

- [ ] Create separate liveness and readiness endpoints.
- [ ] Return an appropriate unsuccessful status when the service is not ready.
- [ ] Add a startup initialization step.
- [ ] Add cleanup logic using `try/finally`.
- [ ] Handle shutdown requests with a signal or framework lifecycle hook.
- [ ] Close database pools and HTTP clients correctly.
- [ ] Test behavior when a required dependency is unavailable.
- [ ] Verify that health responses do not reveal sensitive information.
- [ ] Explain how graceful shutdown works in a container.

## Key Takeaways

- Liveness, readiness, and startup checks serve different purposes.
- Readiness determines whether an instance should receive traffic.
- Graceful shutdown reduces the risk of interrupted work and leaked resources.
- Use framework lifecycle hooks and resource cleanup methods where possible.
- Keep health checks fast, bounded, and secure.
- Test shutdown behavior under realistic deployment conditions.
