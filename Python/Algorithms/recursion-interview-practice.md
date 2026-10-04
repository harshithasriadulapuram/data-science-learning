
# Recursion — Interview Practice

## 1. What Is Recursion?

Recursion is a programming technique in which a function calls itself to solve a smaller version of the same problem.

Every recursive solution should have:

1. **Base case:** Stops the recursion.
2. **Recursive case:** Calls the function with a smaller or simpler input.
3. **Progress toward the base case:** Ensures the recursion eventually terminates.

## 2. Basic Example: Countdown

```python
def countdown(n):
    if n <= 0:
        print("Done!")
        return

    print(n)
    countdown(n - 1)


countdown(3)

# Output:
# 3
# 2
# 1
# Done!
```

The base case is `n <= 0`. Each recursive call decreases `n`.

## 3. Factorial

The factorial of a non-negative integer is:

\[
n! = n \times (n-1) \times \cdots \times 1
\]

Also, `0! = 1`.

### Recursive Solution

```python
def factorial(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n == 0 or n == 1:
        return 1

    return n * factorial(n - 1)


print(factorial(5))  # 120
```

### How It Works

```text
factorial(5)
= 5 * factorial(4)
= 5 * 4 * factorial(3)
= 5 * 4 * 3 * factorial(2)
= 5 * 4 * 3 * 2 * factorial(1)
= 120
```

**Time complexity:** O(n)  
**Auxiliary space:** O(n) due to the call stack.

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

The recurrence is:

```text
sum_n(n) = n + sum_n(n - 1)
sum_n(0) = 0
```

**Time complexity:** O(n)  
**Auxiliary space:** O(n).

## 5. Fibonacci Numbers

The Fibonacci sequence starts with `0, 1`. Each subsequent value is the sum of the previous two values.

### Simple Recursive Solution

```python
def fibonacci(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n <= 1:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(6))  # 8
```

This solution is easy to understand but repeats many calculations.

**Time complexity:** O(2^n) as a simple upper bound.  
**Auxiliary space:** O(n) due to recursion depth.

### Optimized with Memoization

```python
from functools import lru_cache


@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 0:
        raise ValueError("n must be non-negative")

    if n <= 1:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(50))  # 12586269025
```

**Time complexity:** O(n)  
**Space complexity:** O(n) for the cache and recursion stack.

## 6. Reverse a String Recursively

```python
def reverse_string(text):
    if len(text) <= 1:
        return text

    return reverse_string(text[1:]) + text[0]


print(reverse_string("python"))  # nohtyp
```

This version creates string slices and concatenated strings at each level.

**Time complexity:** O(n²) for this Python implementation.  
**Auxiliary space:** O(n²) in total for intermediate strings and recursive frames.

## 7. Check Whether a String Is a Palindrome

A palindrome reads the same forward and backward.

```python
def is_palindrome(text):
    if len(text) <= 1:
        return True

    if text[0] != text[-1]:
        return False

    return is_palindrome(text[1:-1])


print(is_palindrome("racecar"))  # True
print(is_palindrome("python"))   # False
```

This implementation uses string slicing, so the total time and intermediate memory usage can be O(n²).

## 8. Calculate a Number Raised to a Power

Use exponentiation by squaring to reduce the number of multiplications.

```python
def power(base, exponent):
    if exponent < 0:
        if base == 0:
            raise ValueError("Zero cannot have a negative exponent")

        return 1 / power(base, -exponent)

    if exponent == 0:
        return 1

    half = power(base, exponent // 2)

    if exponent % 2 == 0:
        return half * half

    return base * half * half


print(power(2, 10))   # 1024
print(power(2, -2))   # 0.25
```

**Time complexity:** O(log |exponent|)  
**Auxiliary space:** O(log |exponent|).

## 9. Binary Search Recursively

Given a sorted array, return the index of a target or `-1` if it is absent.

```python
def binary_search(nums, target, left=0, right=None):
    if right is None:
        right = len(nums) - 1

    if left > right:
        return -1

    mid = left + (right - left) // 2

    if nums[mid] == target:
        return mid

    if nums[mid] < target:
        return binary_search(nums, target, mid + 1, right)

    return binary_search(nums, target, left, mid - 1)


print(binary_search([1, 3, 5, 7, 9], 7))  # 3
print(binary_search([1, 3, 5, 7, 9], 4))  # -1
```

**Time complexity:** O(log n)  
**Auxiliary space:** O(log n) for the recursive call stack.

## 10. Sum of Digits

```python
def sum_digits(n):
    n = abs(n)

    if n < 10:
        return n

    return n % 10 + sum_digits(n // 10)


print(sum_digits(12345))  # 15
```

**Time complexity:** O(d), where `d` is the number of digits.  
**Auxiliary space:** O(d).

## 11. Greatest Common Divisor (GCD)

The Euclidean algorithm repeatedly replaces `(a, b)` with `(b, a % b)`.

```python
def gcd(a, b):
    if b == 0:
        return abs(a)

    return gcd(b, a % b)


print(gcd(48, 18))  # 6
print(gcd(20, 8))   # 4
```

**Time complexity:** O(log(min(|a|, |b|))) for nonzero inputs in the usual worst-case analysis.  
**Auxiliary space:** O(log(min(|a|, |b|))) for the recursive stack.

## 12. Generate All Subsequences

A subsequence preserves the order of elements but does not require them to be adjacent.

```python
def subsequences(text):
    result = []

    def generate(index, current):
        if index == len(text):
            result.append("".join(current))
            return

        # Exclude the current character.
        generate(index + 1, current)

        # Include the current character.
        current.append(text[index])
        generate(index + 1, current)
        current.pop()

    generate(0, [])
    return result


print(subsequences("ab"))
# ['', 'b', 'a', 'ab']
```

Each character creates two choices: include it or exclude it.

**Time complexity:** O(n * 2^n), including the cost of constructing the output strings.  
**Output space:** O(n * 2^n).

## 13. Recursion Tree

A recursion tree illustrates the calls made by a recursive function.

For Fibonacci:

```text
fibonacci(4)
├── fibonacci(3)
│   ├── fibonacci(2)
│   │   ├── fibonacci(1)
│   │   └── fibonacci(0)
│   └── fibonacci(1)
└── fibonacci(2)
    ├── fibonacci(1)
    └── fibonacci(0)
```

Notice that `fibonacci(2)` is calculated more than once. This is an example of overlapping subproblems and explains why memoization helps.

## 14. Common Recursion Mistakes

- Forgetting the base case.
- Using a base case that does not cover all valid inputs.
- Failing to reduce the problem size.
- Returning the wrong value from a recursive call.
- Forgetting to undo changes when using a shared list.
- Ignoring the recursion depth limit in Python.
- Assuming recursion is always more efficient than iteration.

## 15. Interview Practice Problems

### Beginner
- [ ] Factorial of a number
- [ ] Sum of numbers from 1 to n
- [ ] Fibonacci number
- [ ] Reverse a string
- [ ] Sum of digits
- [ ] Greatest common divisor

### Intermediate
- [ ] Recursive binary search
- [ ] Check whether a string is a palindrome
- [ ] Calculate power using exponentiation by squaring
- [ ] Generate all subsequences
- [ ] Generate all subsets

### Advanced
- [ ] Tower of Hanoi
- [ ] Generate permutations
- [ ] Solve the N-Queens problem
- [ ] Solve Sudoku using recursion
- [ ] Solve a maze using recursive search

## 16. Final Revision Checklist

- [ ] Identify the base case and recursive case.
- [ ] Trace recursive calls by hand.
- [ ] Explain the call stack.
- [ ] Analyze time and auxiliary space complexity.
- [ ] Compare recursion with iteration.
- [ ] Explain memoization and overlapping subproblems.
- [ ] Solve basic recursive problems without looking at solutions.
