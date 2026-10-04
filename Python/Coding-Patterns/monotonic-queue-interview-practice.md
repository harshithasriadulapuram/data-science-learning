
# Monotonic Queue — Interview Practice

## 1. What Is a Monotonic Queue?

A monotonic queue is a queue-like data structure that maintains elements in increasing or decreasing order.

It is commonly implemented using Python's `collections.deque`.

Two main types:

- **Monotonic decreasing deque:** Values decrease from front to back. The front stores the maximum.
- **Monotonic increasing deque:** Values increase from front to back. The front stores the minimum.

Unlike a regular queue, a monotonic deque removes elements that cannot contribute to the answer.

## 2. Why Do We Need It?

Suppose we need the maximum value in every contiguous window of size `k`.

A straightforward solution scans each window separately, taking O(n × k) time.

A monotonic deque can solve the problem in O(n) time because each index enters and leaves the deque at most once.

## 3. Sliding Window Maximum

Given an array and a window size `k`, return the maximum value in every window.

Example:

```python
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3

# Output: [3, 3, 5, 5, 6, 7]
```

### Python Solution

```python
from collections import deque


def max_sliding_window(nums, k):
    if not nums or k <= 0 or k > len(nums):
        return []

    dq = deque()
    result = []

    for i, num in enumerate(nums):

        # Remove indices outside the current window.
        while dq and dq[0] <= i - k:
            dq.popleft()

        # Remove elements smaller than the current value.
        while dq and nums[dq[-1]] <= num:
            dq.pop()

        dq.append(i)

        # A complete window is now available.
        if i >= k - 1:
            result.append(nums[dq[0]])

    return result


print(max_sliding_window([1, 3, -1, -3, 5, 3, 6, 7], 3))
# [3, 3, 5, 5, 6, 7]
```

### How It Works

The deque stores **indices**, not just values.

For each element:

1. Remove indices that are outside the current window.
2. Remove indices from the back whose values are less than or equal to the current value.
3. Add the current index.
4. Once the first complete window is available, the front index identifies its maximum.

Why remove smaller values from the back? The current value is larger and appears later, so it will remain useful longer than those smaller values.

**Time complexity:** O(n)  
**Auxiliary space:** O(k)

## 4. Sliding Window Minimum

To find the minimum in each window, maintain an increasing deque instead.

```python
from collections import deque


def min_sliding_window(nums, k):
    if not nums or k <= 0 or k > len(nums):
        return []

    dq = deque()
    result = []

    for i, num in enumerate(nums):
        while dq and dq[0] <= i - k:
            dq.popleft()

        while dq and nums[dq[-1]] >= num:
            dq.pop()

        dq.append(i)

        if i >= k - 1:
            result.append(nums[dq[0]])

    return result


print(min_sliding_window([1, 3, -1, -3, 5, 3, 6, 7], 3))
# [-1, -3, -3, -3, 3, 3]
```

**Time complexity:** O(n)  
**Auxiliary space:** O(k)

## 5. Maximum of Every Window — Brute Force vs Optimized

### Brute-force approach

```python
def max_sliding_window_brute(nums, k):
    if not nums or k <= 0 or k > len(nums):
        return []

    return [
        max(nums[i:i + k])
        for i in range(len(nums) - k + 1)
    ]
```

Time complexity: O((n - k + 1) × k), or O(nk) as a general upper bound.

### Optimized approach

Use the monotonic deque implementation above.

Time complexity: O(n).

The optimized solution avoids scanning every element in every window.

## 6. Important Difference: Queue vs Monotonic Queue

| Feature | Regular Queue | Monotonic Queue |
|---|---|---|
| Ordering | Usually insertion order | Maintains value order |
| Main operations | Add and remove | Add, remove, discard dominated values |
| Typical use | BFS, task processing | Sliding-window maximum/minimum |
| Complexity | Usually O(1) per operation | O(1) amortized per deque operation |

A monotonic queue is not a replacement for every regular queue. It is useful when maintaining an extreme value over a changing window.

## 7. Common Mistakes

1. Storing values instead of indices when the window boundaries must be tracked.
2. Forgetting to remove expired indices from the front.
3. Using the wrong comparison operator for maximum versus minimum.
4. Reading the maximum from the back instead of the front.
5. Assuming every individual operation must be O(1) in the worst case. Each deque operation is O(1), while the full algorithm is O(n) amortized.
6. Forgetting edge cases such as an empty array, invalid `k`, or `k > len(nums)`.

## 8. When Should You Recognize This Pattern?

Look for problems asking for:

- Maximum or minimum in every fixed-size window.
- Running maximum or minimum over a recent range.
- Efficiently maintaining an extreme value while a window moves.
- Dynamic programming optimizations that need a maximum or minimum over a bounded range.

Not every sliding-window problem requires a monotonic queue. Use it when maintaining an ordered set of candidate extremes makes the solution more efficient.

## 9. Practice Problems

### Beginner

- Sliding Window Maximum.
- Sliding Window Minimum.

### Intermediate

- First Negative Integer in Every Window of Size K.
- Shortest Subarray with Sum at Least K.

### Advanced

- Constrained Subsequence Sum.
- Jump Game VI.
- Sliding Window Maximum with streaming input.

## 10. Interview Checklist

Before implementing the solution, ask:

1. Is the window fixed-size or bounded?
2. Do I need its maximum or minimum?
3. Can dominated candidates be safely removed?
4. Should the deque store indices?
5. Have I removed expired indices before reading the answer?
6. Can I prove each index enters and leaves the deque at most once?

### Final Takeaway

Use a **monotonic decreasing deque for window maximums** and a **monotonic increasing deque for window minimums**. Store indices whenever window boundaries matter.
