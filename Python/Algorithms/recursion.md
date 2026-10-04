
# Recursion in Python

## 1. What Is Recursion?

Recursion is a programming technique in which a function calls itself to solve a smaller version of a problem.

A recursive function usually needs two parts:

- **Base case:** Stops the recursion.
- **Recursive case:** Calls the function with a smaller or simpler input.

## 2. Simple Recursive Function

```python
def countdown(n):
    if n <= 0:
        print("Done!")
        return

    print(n)
    countdown(n - 1)

countdown(5)

# Output:
# 5
# 4
# 3
# 2
# 1
# Done!
```

The base case is `n <= 0`. Without a reachable base case, recursion may continue until Python raises `RecursionError`.

## 3. Factorial Using Recursion

The factorial of a non-negative integer n is the product of all positive integers up to n.

For example: 5! = 5 × 4 × 3 × 2 × 1 = 120.

```python
def factorial(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n == 0 or n == 1:
        return 1

    return n * factorial(n - 1)

print(factorial(5))  # 120
print(factorial(0))  # 1
```

**Time complexity:** O(n)  
**Call-stack space:** O(n)

## 4. Sum of Numbers from 1 to n

```python
def sum_n(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n == 0:
        return 0

    return n + sum_n(n - 1)

print(sum_n(5))  # 15
```

## 5. Fibonacci Sequence

Each Fibonacci number after the first two is the sum of the previous two numbers.

```python
def fibonacci(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n <= 1:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(6))  # 8
```

This simple recursive implementation takes exponential time, approximately O(2^n). It is useful for learning recursion, but inefficient for large inputs.

### A More Efficient Version Using Memoization

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci_fast(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n <= 1:
        return n

    return fibonacci_fast(n - 1) + fibonacci_fast(n - 2)

print(fibonacci_fast(6))  # 8
```

**Time complexity:** O(n) for a fresh calculation up to n, using cached results.  
**Space complexity:** O(n) for cached values and recursive calls.

## 6. Reverse a String Recursively

```python
def reverse_string(text):
    if len(text) <= 1:
        return text

    return reverse_string(text[1:]) + text[0]

print(reverse_string("python"))  # nohtyp
```

This slicing-based implementation can take O(n²) time due to repeated string creation.

## 7. Check Whether a String Is a Palindrome

A palindrome reads the same forwards and backwards.

```python
def is_palindrome(text):
    if len(text) <= 1:
        return True

    if text[0] != text[-1]:
        return False

    return is_palindrome(text[1:-1])

print(is_palindrome("madam"))  # True
print(is_palindrome("hello"))  # False
```

This simple version is case-sensitive and treats spaces and punctuation as characters.

## 8. Calculate the Power of a Number

Calculate x raised to a non-negative integer n.

```python
def power(x, n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n == 0:
        return 1

    return x * power(x, n - 1)

print(power(2, 4))  # 16
```

**Time complexity:** O(n)

### Faster Power Using Divide and Conquer

```python
def fast_power(x, n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n == 0:
        return 1

    half = fast_power(x, n // 2)
    result = half * half

    if n % 2 == 1:
        result *= x

    return result

print(fast_power(2, 10))  # 1024
```

**Time complexity:** O(log n)

## 9. Binary Search Using Recursion

The input list must be sorted.

```python
def binary_search_recursive(numbers, target, left, right):
    if left > right:
        return -1

    middle = (left + right) // 2

    if numbers[middle] == target:
        return middle

    if numbers[middle] < target:
        return binary_search_recursive(
            numbers, target, middle + 1, right
        )

    return binary_search_recursive(
        numbers, target, left, middle - 1
    )

numbers = [10, 20, 30, 40, 50]

print(binary_search_recursive(
    numbers, 40, 0, len(numbers) - 1
))  # 3
```

**Time complexity:** O(log n)  
**Call-stack space:** O(log n)

## 10. Recursion vs Iteration

| Feature | Recursion | Iteration |
|---|---|---|
| Approach | Function calls itself | Uses loops |
| Termination | Base case | Loop condition |
| Memory | Uses call stack | Often uses less extra memory |
| Common uses | Trees, backtracking | Traversals, repeated operations |
| Risk | Recursion depth limit | Infinite loop if condition never changes |

Python does not automatically optimise tail recursion. For many large computations, an iterative solution is more practical.

## Common Interview Questions

1. What is recursion?
2. What is a base case?
3. What happens when a base case is missing?
4. Explain the call stack.
5. What is the difference between recursion and iteration?
6. Why is naive recursive Fibonacci inefficient?
7. What is memoization?
8. How does recursive binary search work?

## Practice Challenges

1. Calculate the product of the digits of a number recursively.
2. Find the sum of digits of a positive integer.
3. Count the number of digits recursively.
4. Reverse an integer using recursion.
5. Find the greatest common divisor (GCD) recursively.
6. Solve the Tower of Hanoi problem.
7. Generate all permutations of a string.
