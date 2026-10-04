
# Python Profiling and Performance Optimization

## 1. What Is Profiling?

Profiling measures how a program uses execution time and other resources.

It helps identify performance bottlenecks before you attempt to optimize the code.

Common questions profiling can answer:
- Which function takes the most time?
- How many times is a function called?
- Which operations are unexpectedly expensive?
- Is a program spending time in Python code or waiting on external operations?

**Important:** Measure first, optimize second. Code that looks inefficient is not always the actual bottleneck.

## 2. Measure Execution Time With `timeit`

Python's `timeit` module measures how long a small piece of code takes to execute.

```python
import timeit

code = """
total = sum(range(1000))
"""

seconds = timeit.timeit(code, number=10_000)

print(f"Total time: {seconds:.6f} seconds")
print(f"Average time: {seconds / 10_000:.9f} seconds")
```

The example runs the statement 10,000 times.

Use `timeit` to compare small operations under similar conditions.

For example:

```python
import timeit

list_comprehension = """
squares = [x * x for x in range(1000)]
"""

loop_approach = """
squares = []
for x in range(1000):
    squares.append(x * x)
"""

print(
    "Comprehension:",
    timeit.timeit(list_comprehension, number=10_000),
)

print(
    "Loop:",
    timeit.timeit(loop_approach, number=10_000),
)
```

Results depend on the Python version, machine, and workload. Do not assume one approach is always faster without measuring.

## 3. Measure a Function With `perf_counter`

For timing a section of a running program, use `time.perf_counter()`.

```python
import time


def calculate_sum(n: int) -> int:
    return sum(range(n))


start = time.perf_counter()

result = calculate_sum(1_000_000)

elapsed = time.perf_counter() - start

print("Result:", result)
print(f"Elapsed time: {elapsed:.6f} seconds")
```

`perf_counter()` is a high-resolution performance counter suitable for measuring elapsed time.

Keep timing conditions consistent. Background processes, caching, and system load can affect results.

## 4. Profile a Program With `cProfile`

`cProfile` is Python's built-in deterministic profiler.

It records information such as:
- Number of function calls.
- Total time spent in a function.
- Cumulative time, including time spent in called functions.

Run a script with:

```bash
python -m cProfile -s cumulative app.py
```

The `-s cumulative` option sorts results by cumulative time.

You can also profile code programmatically:

```python
import cProfile


def calculate_sum(n: int) -> int:
    return sum(range(n))


def main() -> None:
    profiler = cProfile.Profile()

    profiler.enable()

    calculate_sum(1_000_000)

    profiler.disable()

    profiler.print_stats(sort="cumulative")


if __name__ == "__main__":
    main()
```

### Understanding Common Profile Columns

| Column | Meaning |
|---|---|
| `ncalls` | Number of calls |
| `tottime` | Time spent in the function itself, excluding subcalls |
| `percall` | Time per call, depending on the column |
| `cumtime` | Time spent in the function and its subcalls |
| `filename:lineno(function)` | Location and name of the function |

A function with high cumulative time may be expensive itself or may call other expensive functions. Investigate its callers and callees before deciding what to change.

## 5. Save Profiling Results

You can save profiler output for later analysis.

```python
import cProfile


def main() -> None:
    with cProfile.Profile() as profiler:
        sum(range(1_000_000))

    profiler.dump_stats("profile-results.prof")


if __name__ == "__main__":
    main()
```

A saved profile can be inspected later using Python's `pstats` module or compatible profiling tools.

Avoid committing large profiling artifacts unless they are intentionally needed for the project.

## 6. Profile Memory Usage With `tracemalloc`

Execution time is only one part of performance. Memory consumption matters too, especially when processing large datasets.

Python's `tracemalloc` module tracks memory allocations made by Python's memory allocator.

```python
import tracemalloc


def create_numbers() -> list[int]:
    return [number for number in range(100_000)]


tracemalloc.start()

numbers = create_numbers()

current, peak = tracemalloc.get_traced_memory()

print(f"Current traced memory: {current / 1024:.2f} KiB")
print(f"Peak traced memory: {peak / 1024:.2f} KiB")

tracemalloc.stop()
```

These values describe traced Python memory allocations, not necessarily the entire process's memory usage.

For comparing snapshots:

```python
import tracemalloc

tracemalloc.start()

before = tracemalloc.take_snapshot()

data = [number for number in range(100_000)]

after = tracemalloc.take_snapshot()

for statistic in after.compare_to(before, "lineno")[:5]:
    print(statistic)

tracemalloc.stop()
```

This can help identify source lines associated with increased traced allocations.

## 7. Common Optimization Techniques

### A. Choose an Appropriate Data Structure

Repeated membership checks against a list can be expensive.

```python
values = list(range(100_000))

print(99_999 in values)
```

A set is often better for repeated membership checks:

```python
values = set(range(100_000))

print(99_999 in values)
```

Average-case set membership is typically O(1), while list membership is O(n). Actual performance depends on data size and workload.

### B. Avoid Repeating Expensive Calculations

```python
# Less efficient: calculates the sum on every iteration.
numbers = [10, 20, 30, 40]

for number in numbers:
    average = sum(numbers) / len(numbers)
    print(number, average)
```

Calculate the average once:

```python
numbers = [10, 20, 30, 40]

average = sum(numbers) / len(numbers)

for number in numbers:
    print(number, average)
```

### C. Use Generators for Streaming

When you do not need all values stored at once, a generator can reduce memory use.

```python
def read_numbers(n: int):
    for number in range(n):
        yield number


for number in read_numbers(5):
    print(number)
```

Generators produce values on demand. They are useful for streaming, but they are not automatically faster than lists in every situation.

### D. Use Vectorized Operations for Numerical Work

For large numerical datasets, NumPy operations can often outperform equivalent Python loops because much of the work is performed in optimized compiled code.

```python
import numpy as np

numbers = np.arange(1_000_000)

squared = numbers * numbers

print(squared[:5])
```

Vectorization can improve performance, though it may allocate additional arrays. Measure both speed and memory usage for your workload.

## 8. Big-O Complexity vs Profiling

Big-O notation describes how an algorithm's resource requirements scale as input size grows.

Profiling measures observed behavior for a particular workload and environment.

For example:
- O(n) describes linear growth.
- O(n²) describes quadratic growth.
- Profiling reveals which implementation is slow in practice.

A theoretically better algorithm may still be slower for small inputs due to constant overhead. Use complexity analysis and measurements together.

## 9. Benchmarking Best Practices

1. Use representative input data.
2. Run comparisons under similar conditions.
3. Repeat measurements rather than trusting one run.
4. Avoid including unrelated setup work unless it is part of the real workload.
5. Profile realistic program execution paths.
6. Check memory use as well as execution time.
7. Re-run tests after changing implementation details.
8. Keep the original implementation available for comparison when practical.

Do not optimize solely to improve a benchmark that does not represent actual application behavior.

## 10. Common Mistakes

### Mistake 1: Optimizing without measuring

First identify a measurable bottleneck.

### Mistake 2: Timing a function only once

One run can be affected by background work and system noise.

### Mistake 3: Confusing cumulative time with function-only time

`cumtime` includes subcalls; `tottime` excludes them.

### Mistake 4: Assuming generators are always faster

Generators often reduce memory use, but performance depends on the workload.

### Mistake 5: Ignoring memory consumption

An optimization that improves speed but dramatically increases memory use may be unsuitable.

### Mistake 6: Changing code without tests

Performance improvements must preserve correct behavior.

## 11. Interview Questions

**Q1. What is profiling?**

Profiling measures how a program spends execution time or uses resources to help identify bottlenecks.

**Q2. What is the difference between `timeit` and `cProfile`?**

`timeit` is useful for benchmarking small code snippets; `cProfile` analyzes function-call behavior across a program.

**Q3. What is the difference between `tottime` and `cumtime`?**

`tottime` excludes subcalls, while `cumtime` includes time spent in subcalls.

**Q4. What is `tracemalloc` used for?**

It tracks Python memory allocations and helps investigate allocation differences.

**Q5. Does better Big-O complexity always mean faster execution?**

No. Constant factors, input size, implementation details, and workload can affect observed performance.

**Q6. Why should you profile before optimizing?**

Profiling helps target the actual bottleneck instead of spending time improving code that has little impact.

## 12. Practice Tasks

- [ ] Measure a small operation with `timeit`.
- [ ] Time a function using `perf_counter`.
- [ ] Profile a script with `cProfile`.
- [ ] Explain `tottime` and `cumtime`.
- [ ] Compare list and set membership.
- [ ] Use `tracemalloc` to inspect memory allocations.
- [ ] Benchmark a Python loop against a NumPy operation.
- [ ] Explain the difference between Big-O analysis and profiling.

## Key Takeaways

- Measure performance before changing code.
- Use `timeit` for small benchmarks.
- Use `cProfile` to find expensive functions.
- Use `tracemalloc` to investigate Python memory allocations.
- Combine profiling, complexity analysis, and tests.
- Optimize for realistic workloads, not assumptions.
