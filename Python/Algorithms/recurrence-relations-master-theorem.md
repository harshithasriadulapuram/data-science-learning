
# Recurrence Relations and Master Theorem

## 1. What Is a Recurrence Relation?

A recurrence relation describes the running time of a recursive algorithm in terms of the running time of smaller inputs.

For example, binary search reduces the input size by half during each recursive call.

Its time complexity can be represented as:

T(n) = T(n / 2) + O(1)

Here:
- T(n) is the running time for input size n.
- T(n / 2) is the time for the smaller recursive problem.
- O(1) is the work performed outside the recursive call.

Understanding recurrence relations helps us derive the complexity of recursive algorithms instead of guessing it.

## 2. Recurrence Relation for Binary Search

Consider recursive binary search.

```python
def binary_search(numbers, target, left, right):
    if left > right:
        return -1

    middle = (left + right) // 2

    if numbers[middle] == target:
        return middle

    if numbers[middle] < target:
        return binary_search(
            numbers, target, middle + 1, right
        )

    return binary_search(
        numbers, target, left, middle - 1
    )
```

At each step:
- The search interval is approximately halved.
- Only one recursive call is made.
- The comparison and index calculations take constant time.

Recurrence:

T(n) = T(n / 2) + O(1)

Result:

**Time complexity: O(log n)**

The recursion depth is O(log n), so the auxiliary stack space is also O(log n).

## 3. Recurrence Relation for Merge Sort

Merge sort divides the input into two halves, recursively sorts both halves, and merges the results.

```python
def merge_sort(numbers):
    if len(numbers) <= 1:
        return numbers

    middle = len(numbers) // 2

    left = merge_sort(numbers[:middle])
    right = merge_sort(numbers[middle:])

    result = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result.extend(left[i:])
    result.extend(right[j:])

    return result
```

The recurrence is:

T(n) = 2T(n / 2) + O(n)

Explanation:
- Two recursive calls process halves of the input.
- Merging the two sorted halves requires linear work.

Result:

**Time complexity: O(n log n)**

This implementation uses O(n) auxiliary space for temporary lists and merged results, with O(log n) recursion depth.

## 4. Recurrence Relation for Naive Fibonacci

```python
def fibonacci(n):
    if n <= 1:
        return n

    return fibonacci(n - 1) + fibonacci(n - 2)
```

The recurrence is:

T(n) = T(n - 1) + T(n - 2) + O(1)

Each call can produce two more calls, and many subproblems are recalculated.

The time complexity grows exponentially. A commonly used upper bound is O(2^n); a tighter bound is O(phi^n), where phi is the golden ratio.

The maximum recursion depth is O(n).

## 5. Recurrence Relation for a Single Recursive Call

Consider:

T(n) = T(n - 1) + O(1)

The input decreases by one at each recursive step.

Expanding the recurrence:

T(n) = T(n - 2) + O(1) + O(1)

T(n) = T(n - 3) + O(1) + O(1) + O(1)

After approximately n steps, the total work is linear.

**Result: O(n)**

This pattern appears in simple recursive traversals of a sequence.

## 6. What Is the Master Theorem?

The Master Theorem is a method for solving certain divide-and-conquer recurrences.

Its standard form is:

T(n) = aT(n / b) + f(n)

Where:
- a is the number of recursive subproblems.
- b is the factor by which each subproblem shrinks.
- f(n) is the work performed outside the recursive calls.

Assume a >= 1 and b > 1, with the usual regularity and asymptotic conditions required by the theorem.

Define:

d = log_b(a)

Compare f(n) with n^d to determine the applicable case.

## 7. Master Theorem — Case 1

If the non-recursive work grows polynomially slower than the recursive contribution:

f(n) = O(n^(d - epsilon))

for some epsilon > 0.

Then:

T(n) = Theta(n^d)

### Example

T(n) = 4T(n / 2) + O(n)

Here:
- a = 4
- b = 2
- f(n) = O(n)
- n^log_2(4) = n²

Since n grows polynomially slower than n²:

**Result: Theta(n²)**

## 8. Master Theorem — Case 2

If the non-recursive work has the same polynomial order as the recursive contribution:

f(n) = Theta(n^d)

then:

T(n) = Theta(n^d log n)

### Example

T(n) = 2T(n / 2) + O(n)

Here:
- a = 2
- b = 2
- d = log_2(2) = 1
- n^d = n

The non-recursive work is Theta(n).

**Result: Theta(n log n)**

This is the recurrence for merge sort.

## 9. Master Theorem — Case 3

If the non-recursive work grows polynomially faster:

f(n) = Omega(n^(d + epsilon))

for some epsilon > 0, and the regularity condition holds:

a f(n / b) <= c f(n)

for some constant c < 1 and sufficiently large n.

Then:

T(n) = Theta(f(n))

### Example

T(n) = 2T(n / 2) + O(n²)

Here:
- a = 2
- b = 2
- n^log_2(2) = n
- The non-recursive work grows quadratically.

**Result: Theta(n²)**

The regularity condition must be satisfied to apply this standard case.

## 10. Master Theorem Summary

| Case | Comparison | Result |
|---|---|---|
| 1 | f(n) is polynomially smaller than n^log_b(a) | Theta(n^log_b(a)) |
| 2 | f(n) has the same order as n^log_b(a) | Theta(n^log_b(a) log n) |
| 3 | f(n) is polynomially larger, with regularity satisfied | Theta(f(n)) |

Do not force every recurrence into the Master Theorem. It does not directly solve every recurrence, including many recurrences where subproblem sizes decrease by one or vary irregularly.

## 11. Practice Problems

Solve these before reading the answers.

### Problem 1

T(n) = T(n / 2) + O(1)

Answer: Theta(log n)

### Problem 2

T(n) = 2T(n / 2) + O(1)

Answer: Theta(n)

### Problem 3

T(n) = 2T(n / 2) + O(n)

Answer: Theta(n log n)

### Problem 4

T(n) = 4T(n / 2) + O(n)

Answer: Theta(n²)

### Problem 5

T(n) = 2T(n / 2) + O(n²)

Answer: Theta(n²), using Case 3.

### Problem 6

T(n) = T(n - 1) + O(1)

Answer: Theta(n), using expansion or another suitable method rather than the standard Master Theorem.

### Problem 7

T(n) = T(n - 1) + O(n)

Answer: Theta(n²), by summing the work over successive input sizes.

### Problem 8

T(n) = 3T(n / 2) + O(n)

Answer: Theta(n^log_2(3)), using Case 1.

## 12. How to Solve a Recurrence in an Interview

1. Identify the number of recursive calls.
2. Identify the size of each recursive subproblem.
3. Calculate the work outside the recursive calls.
4. Write the recurrence.
5. Check whether the Master Theorem applies.
6. Compare the growth of f(n) and n^log_b(a).
7. State the resulting complexity and explain the assumptions.
8. Analyze auxiliary stack space separately.

## 13. Final Checklist

- [ ] Write recurrences for binary search and merge sort.
- [ ] Explain why naive Fibonacci takes exponential time.
- [ ] Identify a, b, and f(n).
- [ ] Calculate log_b(a).
- [ ] Recognize all three standard Master Theorem cases.
- [ ] Check the regularity condition when using Case 3.
- [ ] Recognize recurrences that require another method.
- [ ] Distinguish time complexity from recursion stack space.

**Interview tip:** Do not memorize only the final answer. Explain how the number of recursive calls, subproblem size, and non-recursive work determine the result.
