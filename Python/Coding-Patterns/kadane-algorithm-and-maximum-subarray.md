
# Kadane's Algorithm and Maximum Subarray in Python

## 1. What Is Kadane's Algorithm?

Kadane's Algorithm finds the maximum sum of a non-empty contiguous subarray in an array of integers.

A subarray is a continuous portion of an array.

Example:

```python
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

The maximum-sum subarray is:

```python
[4, -1, 2, 1]
```

Its sum is:

```text
6
```

The answer is `6`.

Kadane's Algorithm solves this problem in:

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 2. Understanding the Main Idea

At each position, we have two choices:

1. Extend the previous subarray by including the current number.
2. Start a new subarray from the current number.

Let:

- `current_sum` be the maximum sum of a subarray ending at the current position.
- `max_sum` be the maximum sum found anywhere so far.

The recurrence is:

```text
current_sum = max(nums[i], current_sum + nums[i])
max_sum = max(max_sum, current_sum)
```

Why does this work?

If extending the previous subarray produces a smaller sum than starting fresh, discard the previous subarray and start at the current element.

## 3. Basic Implementation

```python
def max_subarray_sum(nums):
    if not nums:
        raise ValueError("Input array must not be empty")

    current_sum = nums[0]
    max_sum = nums[0]

    for num in nums[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)

    return max_sum


nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
print(max_subarray_sum(nums))
```

Output:

```text
6
```

Initializing both variables with the first element ensures that an array containing only negative numbers is handled correctly.

## 4. Dry Run

Consider:

```python
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

| Current number | Current sum | Maximum sum |
|---:|---:|---:|
| -2 | -2 | -2 |
| 1 | 1 | 1 |
| -3 | -2 | 1 |
| 4 | 4 | 4 |
| -1 | 3 | 4 |
| 2 | 5 | 5 |
| 1 | 6 | 6 |
| -5 | 1 | 6 |
| 4 | 5 | 6 |

The maximum subarray sum is `6`.

## 5. Return the Actual Subarray

Sometimes an interviewer asks for the subarray itself, not just its sum.

We can track the starting and ending indices.

```python
def max_subarray(nums):
    if not nums:
        raise ValueError("Input array must not be empty")

    current_sum = nums[0]
    max_sum = nums[0]

    start = 0
    best_start = 0
    best_end = 0

    for i in range(1, len(nums)):
        if nums[i] > current_sum + nums[i]:
            current_sum = nums[i]
            start = i
        else:
            current_sum += nums[i]

        if current_sum > max_sum:
            max_sum = current_sum
            best_start = start
            best_end = i

    return max_sum, nums[best_start:best_end + 1]


nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
print(max_subarray(nums))
```

Output:

```text
(6, [4, -1, 2, 1])
```

Complexity:

- Time: `O(N)`
- Auxiliary space: `O(1)`, excluding the returned subarray.

## 6. Maximum Subarray: Brute Force vs. Kadane

### Brute force

Check every possible subarray and calculate its sum.

A straightforward implementation that sums each subarray independently takes `O(N³)` time.

An improved version maintains a running sum for each starting position and takes `O(N²)` time.

### Kadane's Algorithm

Kadane avoids examining every possible subarray separately.

It uses the best subarray ending at the previous position to calculate the best one ending at the current position.

| Approach | Time complexity | Auxiliary space |
|---|---|---|
| Independent subarray sums | `O(N³)` | `O(1)` |
| Running-sum brute force | `O(N²)` | `O(1)` |
| Kadane's Algorithm | `O(N)` | `O(1)` |

## 7. Maximum Circular Subarray Sum

In a circular array, the final element is adjacent to the first element.

Example:

```python
nums = [5, -3, 5]
```

The maximum circular subarray is `[5, 5]`, with sum `10`.

### Key idea

The answer is either:

1. The ordinary maximum subarray sum.
2. The total array sum minus the minimum subarray sum, representing a wrapping subarray.

Therefore:

```text
maximum circular sum =
max(maximum subarray sum,
    total sum - minimum subarray sum)
```

There is one important exception: if every element is negative, the second expression would incorrectly produce zero. In that case, return the ordinary maximum subarray sum.

### Implementation

```python
def max_circular_subarray_sum(nums):
    if not nums:
        raise ValueError("Input array must not be empty")

    total = sum(nums)

    max_current = max_global = nums[0]
    min_current = min_global = nums[0]

    for num in nums[1:]:
        max_current = max(num, max_current + num)
        max_global = max(max_global, max_current)

        min_current = min(num, min_current + num)
        min_global = min(min_global, min_current)

    # All elements are negative.
    if max_global < 0:
        return max_global

    return max(max_global, total - min_global)


print(max_circular_subarray_sum([5, -3, 5]))
print(max_circular_subarray_sum([-3, -2, -5]))
```

Output:

```text
10
-2
```

Complexity:

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 8. Maximum Product Subarray

Kadane's Algorithm for sums cannot be directly applied to products because multiplying by a negative number can turn a very small product into a large positive product.

Example:

```python
nums = [2, 3, -2, 4]
```

Output:

```text
6
```

The maximum-product subarray is `[2, 3]`.

### Key idea

Track both:

- The maximum product ending at the current position.
- The minimum product ending at the current position.

The minimum matters because a negative number can turn it into the maximum.

### Implementation

```python
def max_product_subarray(nums):
    if not nums:
        raise ValueError("Input array must not be empty")

    max_product = nums[0]
    min_product = nums[0]
    answer = nums[0]

    for num in nums[1:]:
        if num < 0:
            max_product, min_product = min_product, max_product

        max_product = max(num, max_product * num)
        min_product = min(num, min_product * num)

        answer = max(answer, max_product)

    return answer


print(max_product_subarray([2, 3, -2, 4]))
print(max_product_subarray([-2, 0, -1]))
```

Output:

```text
6
0
```

Complexity:

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 9. Maximum Sum of a Fixed-Length Subarray

This is a related problem, but it uses a different pattern: the fixed-size sliding window.

Find the maximum sum of any contiguous subarray of length `k`.

```python
def max_sum_fixed_window(nums, k):
    if k <= 0 or k > len(nums):
        raise ValueError("Invalid window size")

    window_sum = sum(nums[:k])
    best_sum = window_sum

    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        best_sum = max(best_sum, window_sum)

    return best_sum


print(max_sum_fixed_window([2, 1, 5, 1, 3, 2], 3))
```

Output:

```text
9
```

The subarray `[5, 1, 3]` has the maximum sum.

Complexity:

- Time: `O(N)`
- Auxiliary space: `O(1)`

## 10. Kadane vs. Sliding Window

| Feature | Kadane's Algorithm | Fixed-size Sliding Window |
|---|---|---|
| Typical goal | Maximum sum of any non-empty contiguous subarray | Maximum sum for windows of a specified size |
| Window size | Variable | Fixed |
| Main operation | Extend or restart a subarray | Add incoming, remove outgoing |
| Handles negative values | Yes | Yes, for a fixed-size sum |
| Time complexity | `O(N)` | `O(N)` |

## 11. Common Mistakes

1. Initializing the maximum sum to zero, which fails for all-negative arrays.
2. Forgetting that the problem requires a non-empty subarray.
3. Confusing maximum subarray sum with maximum subsequence sum.
4. Applying the ordinary maximum-sum recurrence directly to products.
5. Forgetting the all-negative special case in circular maximum subarray problems.
6. Losing the correct starting index when returning the actual subarray.
7. Using a fixed-size sliding window when the subarray length is not fixed.

## 12. Interview Practice Questions

Solve these in order:

1. Maximum Subarray.
2. Maximum Circular Subarray Sum.
3. Maximum Product Subarray.
4. Maximum Sum Circular Subarray.
5. Maximum Absolute Sum of Any Subarray.
6. Maximum Subarray Sum with One Deletion.
7. Maximum Sum of Two Non-Overlapping Subarrays.
8. Maximum Sum Rectangle in a 2D Matrix.
9. Maximum Subarray with a Length Constraint.
10. Best Time to Buy and Sell Stock — a related optimization problem.

## 13. Final Interview Checklist

Before considering this topic mastered, make sure you can:

- Explain why a subarray can be extended or restarted.
- Write Kadane's Algorithm from memory.
- Handle arrays containing only negative numbers.
- Return the actual subarray using indices.
- Explain the circular maximum subarray technique.
- Track both maximum and minimum products for product problems.
- Distinguish Kadane's Algorithm from fixed-size sliding windows.

**Key takeaway:** Kadane's Algorithm finds the best contiguous subarray by reusing the best result ending at the previous position, achieving linear time and constant auxiliary space.
