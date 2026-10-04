# Python Interview Coding Problems: Numbers

## 1. Check whether a number is even or odd
```python
def even_or_odd(n: int) -> str:
    return "Even" if n % 2 == 0 else "Odd"

print(even_or_odd(7))  # Odd
```
**Concept:** The modulo operator `%` returns the remainder. An even integer has remainder zero when divided by 2.

## 2. Find the largest of three numbers
```python
def largest_of_three(a, b, c):
    return max(a, b, c)

print(largest_of_three(10, 25, 17))  # 25
```
**Practice:** Solve the same problem using `if` and `elif`, without `max()`.

## 3. Check whether a number is positive, negative, or zero
```python
def classify_number(n):
    if n > 0:
        return "Positive"
    elif n < 0:
        return "Negative"
    return "Zero"
```

## 4. Find the factorial of a number
The factorial of a non-negative integer n is the product of all integers from 1 to n. By definition, `0! = 1`.

```python
def factorial(n: int) -> int:
    if n < 0:
        raise ValueError("n must be non-negative")

    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

print(factorial(5))  # 120
```
**Complexity:** O(n) time and O(1) auxiliary space for this iterative solution.

## 5. Check whether a number is prime
A prime number is an integer greater than 1 with exactly two positive divisors: 1 and itself.

```python
def is_prime(n: int) -> bool:
    if n < 2:
        return False

    divisor = 2
    while divisor * divisor <= n:
        if n % divisor == 0:
            return False
        divisor += 1

    return True

print(is_prime(17))  # True
print(is_prime(18))  # False
```
**Concept:** It is enough to check divisors up to the square root of n, using the condition `divisor * divisor <= n`.

## 6. Reverse the digits of a positive integer
```python
def reverse_number(n: int) -> int:
    reversed_n = 0
    while n > 0:
        digit = n % 10
        reversed_n = reversed_n * 10 + digit
        n //= 10
    return reversed_n

print(reverse_number(1234))  # 4321
```
This implementation is intended for non-negative integers. It returns `0` for input `0`; it does not preserve leading zeros in the reversed result.

## 7. Check whether a number is a palindrome
A palindrome reads the same forward and backward.

```python
def is_number_palindrome(n: int) -> bool:
    if n < 0:
        return False
    return str(n) == str(n)[::-1]

print(is_number_palindrome(121))  # True
print(is_number_palindrome(123))  # False
```

## 8. Find the sum of digits
```python
def sum_of_digits(n: int) -> int:
    n = abs(n)
    total = 0
    while n > 0:
        total += n % 10
        n //= 10
    return total

print(sum_of_digits(1234))  # 10
```

## 9. Generate the Fibonacci sequence
Each term after the first two is the sum of the previous two. This version returns the first `count` terms.

```python
def fibonacci(count: int) -> list[int]:
    if count < 0:
        raise ValueError("count must be non-negative")

    sequence = []
    a, b = 0, 1
    for _ in range(count):
        sequence.append(a)
        a, b = b, a + b
    return sequence

print(fibonacci(7))  # [0, 1, 1, 2, 3, 5, 8]
```

## 10. Find the greatest common divisor (GCD)
The GCD is the largest positive integer that divides both integers, with `gcd(0, 0)` conventionally returning 0 in Python's `math.gcd`.

```python
from math import gcd

print(gcd(24, 36))  # 12
```
**Practice:** Implement the Euclidean algorithm yourself using repeated remainder operations.

## Practice checklist
- [ ] Even or odd
- [ ] Largest of three numbers
- [ ] Positive, negative, or zero
- [ ] Factorial
- [ ] Prime number
- [ ] Reverse a number
- [ ] Number palindrome
- [ ] Sum of digits
- [ ] Fibonacci sequence
- [ ] GCD

## Interview preparation tips
1. Understand the input and expected output before coding.
2. Test normal cases, boundary cases, and invalid inputs.
3. Explain the logic aloud before writing the code.
4. Know the time and space complexity of your solution.
5. After understanding a solution, close it and implement it from memory.
