
# Sliding Window Maximum in Python

## 1. Problem Statement

Given an array of integers `nums` and an integer `k`, return the maximum value in every contiguous window of size `k`.

### Example

```python
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
```

Output:

```python
[3, 3, 5, 5, 6, 7]
```

Explanation:

| Window | Maximum |
|---|---:|
| `[1, 3, -1]` | 3 |
| `[3, -1, -3]` | 3 |
| `[-1, -3, 5]` | 5 |
| `[-3, 5, 3]` | 5 |
| `[5, 3, 6]` | 6 |
| `[3, 6, 7]` | 7 |

## 2. Approach 1: Brute Force

For every window, examine all `k` elements and find its maximum.

```python
def max_sliding_window(nums, k):
    if not nums or k <= 0 or k > len(nums):
        return []

    result = []

    for i in range(len(nums) - k + 1):
        window = nums[i:i + k]
        result.append(max(window))

    return result


nums = [1, 3, -1, -3, 5, 3, 6, 7]
print(max_sliding_window(nums, 3))
```

Output:

```text
[3, 3, 5, 5, 6, 7]
```

### Complexity

- Time: `O(Nk)`
- Auxiliary space: `O(k)` for the temporary window, excluding the output.

This approach is easy to understand but becomes inefficient when both the array and window are large.

## 3. Approach 2: Monotonic Deque

A **monotonic deque** is a double-ended queue that maintains elements in a particular order.

For the sliding window maximum problem, we maintain a deque of array indices whose corresponding values are in decreasing order.

For example, if the deque represents values:

```text
[6, 3, 1]
```

the values decrease from front to back. The front always contains the largest value among the candidates currently stored.

### Important rules

1. Remove indices from the front if they are outside the current window.
2. Remove indices from the back while their values are less than or equal to the current value.
3. Add the current index to the back.
4. Once a complete window has formed, the value at the front is the maximum.

Why remove smaller values from the back?

If a new value is greater than an older value, the older value cannot be the maximum while the new value remains in the window. The new value is both larger and newer, so it will remain relevant for at least as long.

## 4. Optimal Python Implementation

```python
from collections import deque


def max_sliding_window(nums, k):
    if not nums or k <= 0 or k > len(nums):
        return []

    dq = deque()
    result = []

    for i in range(len(nums)):

        # Remove indices outside the current window.
        while dq and dq[0] <= i - k:
            dq.popleft()

        # Maintain decreasing order of values.
        while dq and nums[dq[-1]] <= nums[i]:
            dq.pop()

        # Add the current index.
        dq.append(i)

        # Record the maximum once the first window is complete.
        if i >= k - 1:
            result.append(nums[dq[0]])

    return result


nums = [1, 3, -1, -3, 5, 3, 6, 7]
print(max_sliding_window(nums, 3))
```

Output:

```text
[3, 3, 5, 5, 6, 7]
```

### Complexity

- Time: `O(N)` because each index is added to and removed from the deque at most once.
- Auxiliary space: `O(k)` for the deque.
- Output space: `O(N - k + 1)` for the result.

This is the optimal linear-time approach for the general problem.

## 5. Dry Run

Consider:

```python
nums = [1, 3, -1, -3, 5]
k = 3
```

The deque stores indices, not values. The table shows the corresponding values for readability.

| Index | Value | Deque values after processing | Action |
|---:|---:|---|---|
| 0 | 1 | `[1]` | Add 1 |
| 1 | 3 | `[3]` | Remove 1 because 3 is larger |
| 2 | -1 | `[3, -1]` | Add -1; first window maximum is 3 |
| 3 | -3 | `[3, -1, -3]` | Add -3; maximum is 3 |
| 4 | 5 | `[5]` | Remove smaller candidates; maximum is 5 |

Result:

```python
[3, 3, 5]
```

## 6. Why Store Indices Instead of Values?

Indices help us determine whether an element has moved outside the current window.

For a window ending at index `i`, the window begins at:

```python
i - k + 1
```

An index is outside the window when:

```python
index <= i - k
```

Therefore, we remove expired indices from the front of the deque.

## 7. Common Mistakes

1. Storing values instead of indices when expiration must be tracked.
2. Forgetting to remove indices that are outside the window.
3. Maintaining increasing order instead of decreasing order for a maximum query.
4. Adding results before the first complete window has formed.
5. Using `pop()` on the front instead of `popleft()`.
6. Assuming every window must be scanned completely.
7. Forgetting edge cases such as `k = 1`, `k = len(nums)`, or an empty array.

## 8. Variations to Practice

### Problem 1: Sliding Window Minimum

Maintain an increasing deque instead of a decreasing deque.

### Problem 2: First Negative Number in Every Window

Maintain the indices of negative values and remove indices that expire.

### Problem 3: Maximum of Every Window Using a Heap

Use a max-heap simulation with negative values in Python's `heapq`. Remove expired entries using their indices.

### Problem 4: Longest Subarray with Absolute Difference Within a Limit

Use two monotonic deques: one for the maximum and another for the minimum.

### Problem 5: Shortest Subarray with Sum at Least K

Explore prefix sums combined with a monotonic deque. This is a more advanced variation.

## 9. Interview Checklist

Before considering this pattern mastered, make sure you can:

- Explain why a deque is better than recalculating every maximum.
- Write the solution using `collections.deque` without referring to notes.
- Explain why each index is inserted and removed at most once.
- Derive the `O(N)` time complexity.
- Adapt the approach to find sliding-window minimums.
- Identify problems that require two monotonic deques.

## 10. Key Takeaway

**Sliding window + monotonic deque = efficient window maximum/minimum queries.**

Use a monotonic deque when you need to maintain a maximum or minimum over a moving window and want better performance than repeatedly scanning the window.
