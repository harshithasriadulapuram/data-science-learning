
# Time and Space Complexity in Python

## 1. What Is Time Complexity?

Time complexity describes how the number of operations an algorithm performs grows as the input size increases.

It does not directly measure the exact number of seconds a program takes.

We commonly express complexity using Big O notation.

## 2. Common Big O Complexities

| Complexity | Name | Example |
|---|---|---|
| O(1) | Constant | Accessing a list element by index |
| O(log n) | Logarithmic | Binary search on sorted data |
| O(n) | Linear | Traversing a list |
| O(n log n) | Linearithmic | Typical efficient comparison sorting |
| O(n²) | Quadratic | Two nested loops over the same input |
| O(2^n) | Exponential | Naive recursive Fibonacci |
| O(n!) | Factorial | Generating all permutations |

These describe growth rates; actual performance depends on implementation and input.

## 3. O(1) — Constant Time

The operation does not grow with the input size.

```python
numbers = [10, 20, 30, 40, 50]
print(numbers[2])  # 30
```

Accessing an element by list index takes O(1) time.

## 4. O(n) — Linear Time

The number of operations grows proportionally to the input size.

```python
def print_numbers(numbers):
    for number in numbers:
        print(number)

print_numbers([1, 2, 3, 4, 5])
```

The loop visits each element once.

**Time complexity:** O(n)

## 5. O(n²) — Quadratic Time

A nested loop can perform approximately n × n operations.

```python
def print_pairs(numbers):
    for first in numbers:
        for second in numbers:
            print(first, second)

print_pairs([1, 2, 3])
```

For n elements, this produces n² pairs.

**Time complexity:** O(n²)

## 6. O(log n) — Logarithmic Time

Binary search halves the search range at each step.

```python
def binary_search(numbers, target):
    left, right = 0, len(numbers) - 1

    while left <= right:
        middle = (left + right) // 2

        if numbers[middle] == target:
            return middle
        if numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return -1
```

The input must be sorted.

**Time complexity:** O(log n)  
**Auxiliary space:** O(1)

## 7. O(n log n) — Linearithmic Time

Many efficient comparison-based sorting algorithms have O(n log n) time complexity.

```python
numbers = [8, 3, 5, 1, 9, 2]
result = sorted(numbers)

print(result)  # [1, 2, 3, 5, 8, 9]
```

Python's sorting uses Timsort, which has O(n log n) worst-case time complexity and can perform in O(n) time on already sorted input.

## 8. O(2^n) — Exponential Time

Naive recursive Fibonacci repeatedly calculates the same values.

```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(6))  # 8
```

The running time grows exponentially.

## 9. What Is Space Complexity?

Space complexity describes how memory requirements grow as input size increases.

Auxiliary space refers specifically to extra memory used by an algorithm, excluding the input itself.

### Example: O(1) Auxiliary Space

```python
def find_sum(numbers):
    total = 0

    for number in numbers:
        total += number

    return total

print(find_sum([1, 2, 3, 4]))  # 10
```

The function uses a fixed number of additional variables.

**Auxiliary space:** O(1)

### Example: O(n) Auxiliary Space

```python
def double_numbers(numbers):
    result = []

    for number in numbers:
        result.append(number * 2)

    return result

print(double_numbers([1, 2, 3]))  # [2, 4, 6]
```

The result list grows with the input size.

**Auxiliary space:** O(n), excluding the input list.

## 10. Analysing Multiple Loops

### Two Separate Loops

```python
def example(numbers):
    for number in numbers:
        print(number)

    for number in numbers:
        print(number * 2)
```

The total work is approximately 2n. Constants are ignored in Big O notation.

**Time complexity:** O(n)

### Nested Loops

```python
def example(numbers):
    for first in numbers:
        for second in numbers:
            print(first, second)
```

**Time complexity:** O(n²)

## 11. Best, Average and Worst Cases

- **Best case:** The least work for a given input size.
- **Average case:** Expected work under a specified input distribution.
- **Worst case:** The greatest work for a given input size.

For linear search:
- Best case: O(1), if the first element matches.
- Worst case: O(n), if the target is last or absent.
- Average case: O(n), under common assumptions.

## 12. Important Rules

1. Drop constant factors: O(2n) becomes O(n).
2. Keep the fastest-growing term: O(n² + n) becomes O(n²).
3. Sequential operations are added; nested loops often multiply.
4. Binary search requires sorted input.
5. Recursion uses call-stack memory.
6. Hash table operations are generally O(1) on average, but can be O(n) in the worst case.
7. Analyse the actual implementation, not just the algorithm's name.

## Practice Questions

1. What is the time complexity of one loop over n elements?
2. What is the complexity of two nested loops over n elements?
3. Why is binary search O(log n)?
4. What is the difference between time and space complexity?
5. What is auxiliary space?
6. What is the time complexity of searching a list using `in`?
7. Why is naive recursive Fibonacci inefficient?
8. What is the average time complexity of dictionary lookup?
9. Analyse the time and auxiliary space complexity of a function that copies a list.
10. Explain Big O notation in an interview.
