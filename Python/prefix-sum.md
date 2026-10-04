
# Prefix Sum in Python

## 1. Introduction

Prefix Sum is a technique used to calculate the sum of elements in a range efficiently.

Instead of calculating the sum repeatedly for every query, we preprocess the array and store cumulative sums.

This technique is useful for:
- Range sum queries
- Subarray sum problems
- Array problems
- Matrix sum queries
- Coding interviews

## 2. What Is a Prefix Sum?

A prefix sum at index `i` is the sum of all elements from index `0` through index `i`.

For example:

```python
numbers = [2, 4, 6, 8, 10]
```

The prefix sums are:

```text
Original array: [2, 4, 6, 8, 10]
Prefix sums:   [2, 6, 12, 20, 30]
```

Explanation:

- `2` = 2
- `6` = 2 + 4
- `12` = 2 + 4 + 6
- `20` = 2 + 4 + 6 + 8
- `30` = 2 + 4 + 6 + 8 + 10

## 3. Building a Prefix Sum Array

### Python Implementation

```python
def build_prefix_sum(numbers):
    prefix = []
    running_sum = 0

    for number in numbers:
        running_sum += number
        prefix.append(running_sum)

    return prefix


numbers = [2, 4, 6, 8, 10]

print(build_prefix_sum(numbers))
# [2, 6, 12, 20, 30]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(n)

We visit each element once and store one prefix sum per element.

## 4. Range Sum Query

### Problem

Given an array, find the sum of elements between indices `left` and `right`, inclusive.

For example:

```python
numbers = [2, 4, 6, 8, 10]
```

Find the sum from index `1` to index `3`.

The answer is:

```text
4 + 6 + 8 = 18
```

### Formula

Let `prefix[i]` represent the sum from index `0` through index `i`.

Then:

- If `left == 0`, the range sum is `prefix[right]`.
- Otherwise:

`range_sum = prefix[right] - prefix[left - 1]`

### Python Implementation

```python
def build_prefix_sum(numbers):
    prefix = []
    running_sum = 0

    for number in numbers:
        running_sum += number
        prefix.append(running_sum)

    return prefix


def range_sum(prefix, left, right):
    if not (0 <= left <= right < len(prefix)):
        raise ValueError("Invalid range")

    if left == 0:
        return prefix[right]

    return prefix[right] - prefix[left - 1]


numbers = [2, 4, 6, 8, 10]
prefix = build_prefix_sum(numbers)

print(range_sum(prefix, 1, 3))  # 18
print(range_sum(prefix, 0, 2))  # 12
print(range_sum(prefix, 2, 4))  # 24
```

### Complexity

- Building prefix sums: O(n)
- Each range sum query: O(1)
- Auxiliary space: O(n)

This is efficient when many range sum queries are performed on the same array.

## 5. Prefix Sum with Negative Numbers

Prefix sums also work when an array contains negative numbers.

```python
numbers = [3, -2, 5, -1, 6]
```

The prefix sums are:

```text
[3, 1, 6, 5, 11]
```

Implementation:

```python
def prefix_sum(numbers):
    result = []
    total = 0

    for number in numbers:
        total += number
        result.append(total)

    return result


print(prefix_sum([3, -2, 5, -1, 6]))
# [3, 1, 6, 5, 11]
```

The same range sum formula works with negative values.

## 6. Prefix Sum with a Leading Zero

Another common approach stores an initial zero.

For an array of length `n`, the prefix array has length `n + 1`.

### Example

```text
Original array: [2, 4, 6, 8]
Prefix array:   [0, 2, 6, 12, 20]
```

The range sum from index `left` to index `right`, inclusive, is:

`prefix[right + 1] - prefix[left]`

### Python Implementation

```python
def build_prefix(numbers):
    prefix = [0]

    for number in numbers:
        prefix.append(prefix[-1] + number)

    return prefix


def range_sum(prefix, left, right):
    if not (0 <= left <= right < len(prefix) - 1):
        raise ValueError("Invalid range")

    return prefix[right + 1] - prefix[left]


numbers = [2, 4, 6, 8, 10]
prefix = build_prefix(numbers)

print(prefix)  # [0, 2, 6, 12, 20, 30]
print(range_sum(prefix, 1, 3))  # 18
```

### Why Use a Leading Zero?

It makes the range sum formula uniform, including ranges that start at index `0`.

This version is especially convenient for interview problems.

## 7. Find the Equilibrium Index

### Problem

An equilibrium index is an index where the sum of elements to its left equals the sum of elements to its right.

For example:

```python
numbers = [1, 7, 3, 6, 5, 6]
```

Index `3` is an equilibrium index because:

```text
Left sum: 1 + 7 + 3 = 11
Right sum: 5 + 6 = 11
```

### Python Implementation

```python
def equilibrium_index(numbers):
    total = sum(numbers)
    left_sum = 0

    for index, number in enumerate(numbers):
        right_sum = total - left_sum - number

        if left_sum == right_sum:
            return index

        left_sum += number

    return -1


numbers = [1, 7, 3, 6, 5, 6]

print(equilibrium_index(numbers))  # 3
```

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

This approach uses the total sum rather than storing a complete prefix array.

## 8. Find the Number of Subarrays with a Target Sum

### Problem

Count the number of contiguous subarrays whose sum equals a target.

The array may contain positive numbers, zeros, and negative numbers.

### Python Implementation

```python
def count_subarrays_with_sum(numbers, target):
    prefix_count = {0: 1}
    running_sum = 0
    count = 0

    for number in numbers:
        running_sum += number

        needed = running_sum - target
        count += prefix_count.get(needed, 0)

        prefix_count[running_sum] = (
            prefix_count.get(running_sum, 0) + 1
        )

    return count


print(count_subarrays_with_sum([1, 1, 1], 2))  # 2
print(count_subarrays_with_sum([1, -1, 0], 0))  # 3
```

### How It Works

Suppose the current prefix sum is `current_sum`.

We need an earlier prefix sum equal to:

`current_sum - target`

Each occurrence of that earlier prefix sum identifies a subarray ending at the current index whose sum equals the target.

The dictionary stores how many times each prefix sum has occurred.

### Complexity

- Time: O(n) on average
- Auxiliary space: O(n)

## 9. Difference Between Prefix Sum and Sliding Window

| Prefix Sum | Sliding Window |
|---|---|
| Stores cumulative sums | Maintains a current window |
| Useful for repeated range sum queries | Useful for many contiguous subarray and substring problems |
| Each range sum query can take O(1) after preprocessing | Many sliding-window problems can be solved in O(n) |
| Works with negative values for range sum calculations | Some sum-based window strategies require non-negative or positive values |

Both techniques can be useful for array and string problems. Choose based on the problem's requirements.

## 10. Two-Dimensional Prefix Sum

### Definition

A two-dimensional prefix sum supports efficient sum queries over rectangular regions of a matrix.

### Example

```text
Matrix:
1  2  3
4  5  6
7  8  9
```

### Python Implementation

```python
def build_2d_prefix(matrix):
    rows = len(matrix)

    if rows == 0:
        return [[0]]

    cols = len(matrix[0])

    if any(len(row) != cols for row in matrix):
        raise ValueError("Matrix must be rectangular")

    prefix = [
        [0] * (cols + 1)
        for _ in range(rows + 1)
    ]

    for row in range(1, rows + 1):
        for col in range(1, cols + 1):
            prefix[row][col] = (
                matrix[row - 1][col - 1]
                + prefix[row - 1][col]
                + prefix[row][col - 1]
                - prefix[row - 1][col - 1]
            )

    return prefix


def rectangle_sum(prefix, r1, c1, r2, c2):
    rows = len(prefix) - 1
    cols = len(prefix[0]) - 1

    if not (
        0 <= r1 <= r2 < rows
        and 0 <= c1 <= c2 < cols
    ):
        raise ValueError("Invalid rectangle")

    return (
        prefix[r2 + 1][c2 + 1]
        - prefix[r1][c2 + 1]
        - prefix[r2 + 1][c1]
        + prefix[r1][c1]
    )


matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

prefix = build_2d_prefix(matrix)

print(rectangle_sum(prefix, 0, 0, 1, 1))  # 12
print(rectangle_sum(prefix, 1, 1, 2, 2))  # 28
```

### Complexity

- Building the prefix matrix: O(rows × columns)
- Each rectangular sum query: O(1)
- Auxiliary space: O(rows × columns)

## 11. Common Mistakes

1. Confusing zero-based indices with prefix-array positions.
2. Forgetting to subtract the prefix before the range.
3. Using the wrong formula for inclusive range boundaries.
4. Forgetting the initial zero in a leading-zero prefix array.
5. Assuming prefix sums require a sorted array.
6. Forgetting that negative numbers are allowed in ordinary prefix sum calculations.
7. Updating prefix-sum frequencies before counting when the order would incorrectly include an empty subarray.

## 12. Interview Questions

### Beginner

1. What is a prefix sum?
2. How do you build a prefix sum array?
3. How can you calculate a range sum in O(1)?
4. Why is a leading zero useful?
5. Does prefix sum work with negative numbers?

### Intermediate

6. Find the equilibrium index.
7. Count subarrays with a target sum.
8. Find the number of subarrays whose sum is zero.
9. Answer multiple range sum queries.
10. Calculate sums over rectangular matrix regions.

### Advanced

11. Find the longest subarray with a target sum.
12. Count subarrays with a sum divisible by `k`.
13. Find the maximum subarray sum using prefix sums.
14. Solve range sum queries on a large matrix.
15. Combine prefix sums with hashing to solve subarray problems.

## 13. Coding Practice Checklist

- [ ] Build a prefix sum array.
- [ ] Answer a range sum query.
- [ ] Implement the leading-zero prefix method.
- [ ] Handle arrays containing negative numbers.
- [ ] Find an equilibrium index.
- [ ] Count subarrays with a target sum.
- [ ] Count zero-sum subarrays.
- [ ] Implement a two-dimensional prefix sum.
- [ ] Answer rectangular matrix sum queries.
- [ ] Explain the time and space complexity of each solution.

## 14. Key Takeaways

- Prefix Sum stores cumulative sums.
- It allows range sum queries in O(1) after O(n) preprocessing.
- A leading zero simplifies range-boundary calculations.
- Prefix sums combined with hash maps solve many subarray problems.
- Two-dimensional prefix sums make rectangular matrix queries efficient.
- Practise the formulas until you can derive them without memorizing blindly.
