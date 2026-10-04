
# Monotonic Stack and Queue in Python

## 1. What Is a Monotonic Stack?

A monotonic stack is a stack that maintains elements in a specific order.

There are two main types:

- **Monotonic increasing stack:** Elements are maintained in increasing order from bottom to top.
- **Monotonic decreasing stack:** Elements are maintained in decreasing order from bottom to top.

When a new element violates the required order, we remove elements from the top until the order is restored.

Monotonic stacks are useful for:
- Next greater element
- Next smaller element
- Previous greater element
- Previous smaller element
- Daily temperatures
- Largest rectangle in a histogram
- Stock span problems

## 2. Why Do We Need a Monotonic Stack?

Consider this list:

```python
nums = [2, 1, 5, 3, 4]
```

Suppose we need to find the next greater element for every number.

A brute-force approach checks the elements to the right of each number. In the worst case, this takes O(n²) time.

A monotonic stack can solve the problem in O(n) time because each element is pushed and popped at most once.

## 3. Monotonic Increasing Stack

An increasing stack maintains elements in increasing order from bottom to top.

Example:

```text
Push 2: [2]
Push 4: [2, 4]
Push 3: [2, 3]
Push 5: [2, 3, 5]
```

When `3` arrives, `4` is removed because it violates the increasing order.

### Code

```python
def increasing_stack(nums):
    stack = []

    for num in nums:
        while stack and stack[-1] > num:
            stack.pop()

        stack.append(num)

    return stack


print(increasing_stack([2, 4, 3, 5]))
# [2, 3, 5]
```

This example maintains increasing values, not original indices. The exact comparison depends on the problem.

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 4. Monotonic Decreasing Stack

A decreasing stack maintains elements in decreasing order from bottom to top.

```python
def decreasing_stack(nums):
    stack = []

    for num in nums:
        while stack and stack[-1] < num:
            stack.pop()

        stack.append(num)

    return stack


print(decreasing_stack([5, 3, 4, 2]))
# [5, 4, 2]
```

When `4` arrives, `3` is removed because it violates the decreasing order.

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 5. Next Greater Element

Given a list, find the first element to the right of each element that is strictly greater than it. If none exists, return `-1`.

Input:

```python
nums = [2, 1, 2, 4, 3]
```

Output:

```text
[4, 2, 4, -1, -1]
```

### Code

```python
def next_greater_element(nums):
    n = len(nums)
    result = [-1] * n
    stack = []  # Stores indices

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            index = stack.pop()
            result[index] = num

        stack.append(i)

    return result


print(next_greater_element([2, 1, 2, 4, 3]))
# [4, 2, 4, -1, -1]
```

### How it works

1. Store indices whose next greater elements have not been found.
2. When a greater value arrives, resolve the indices it can answer.
3. Pop those indices and record the current value.
4. Push the current index.
5. Any unresolved positions remain `-1`.

We store indices rather than values because we need to know which result position to update.

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 6. Next Smaller Element

Find the first strictly smaller element to the right of each element.

```python
def next_smaller_element(nums):
    n = len(nums)
    result = [-1] * n
    stack = []

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] > num:
            index = stack.pop()
            result[index] = num

        stack.append(i)

    return result


print(next_smaller_element([4, 2, 5, 1, 3]))
# [2, 1, 1, -1, -1]
```

**Time complexity:** O(n).

**Auxiliary space:** O(n).

For equal values, the strict comparison matters. Using `>=` instead of `>` changes the behavior.

## 7. Previous Greater Element

Find the nearest strictly greater element to the left of each element.

Return `-1` if no such element exists.

```python
def previous_greater_element(nums):
    result = []
    stack = []

    for num in nums:
        while stack and stack[-1] <= num:
            stack.pop()

        result.append(stack[-1] if stack else -1)
        stack.append(num)

    return result


print(previous_greater_element([3, 1, 4, 2, 5]))
# [-1, 3, -1, 4, -1]
```

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 8. Daily Temperatures

You are given daily temperatures. For each day, determine how many days must pass until a warmer temperature occurs.

Input:

```python
temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
```

Output:

```text
[1, 1, 4, 2, 1, 1, 0, 0]
```

### Code

```python
def daily_temperatures(temperatures):
    n = len(temperatures)
    result = [0] * n
    stack = []  # Indices of unresolved days

    for i, temperature in enumerate(temperatures):
        while (
            stack
            and temperatures[stack[-1]] < temperature
        ):
            previous_day = stack.pop()
            result[previous_day] = i - previous_day

        stack.append(i)

    return result


print(daily_temperatures(
    [73, 74, 75, 71, 69, 72, 76, 73]
))
# [1, 1, 4, 2, 1, 1, 0, 0]
```

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 9. Stock Span Problem

The stock span for a day is the number of consecutive days ending on that day for which the price was less than or equal to today's price.

Input:

```python
prices = [100, 80, 60, 70, 60, 75, 85]
```

Output:

```text
[1, 1, 1, 2, 1, 4, 6]
```

### Code

```python
def stock_span(prices):
    spans = []
    stack = []  # Indices of previous greater prices

    for i, price in enumerate(prices):
        while stack and prices[stack[-1]] <= price:
            stack.pop()

        previous_greater = stack[-1] if stack else -1
        spans.append(i - previous_greater)

        stack.append(i)

    return spans


print(stock_span([100, 80, 60, 70, 60, 75, 85]))
# [1, 1, 1, 2, 1, 4, 6]
```

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 10. Largest Rectangle in a Histogram

Each bar in a histogram has a height and a width of `1`. Find the largest rectangular area.

Input:

```python
heights = [2, 1, 5, 6, 2, 3]
```

Output:

```text
10
```

The bars with heights `5` and `6` form a rectangle of area `5 × 2 = 10`.

### Code

```python
def largest_rectangle_area(heights):
    stack = []
    max_area = 0
    n = len(heights)

    for i in range(n + 1):
        current_height = 0 if i == n else heights[i]

        while (
            stack
            and heights[stack[-1]] > current_height
        ):
            height = heights[stack.pop()]

            if stack:
                width = i - stack[-1] - 1
            else:
                width = i

            max_area = max(max_area, height * width)

        stack.append(i)

    return max_area


print(largest_rectangle_area([2, 1, 5, 6, 2, 3]))
# 10
```

The extra iteration at `i == n` uses a height of zero to resolve all remaining positive-height bars.

**Time complexity:** O(n).

**Auxiliary space:** O(n).

## 11. What Is a Monotonic Queue?

A monotonic queue maintains elements in sorted order while supporting insertion and removal at both ends.

In many sliding-window problems, a deque stores indices in increasing or decreasing value order.

It helps find the minimum or maximum of each window in O(n) total time.

Python provides `collections.deque` for efficient operations at both ends.

## 12. Sliding Window Maximum

Given a list and a window size `k`, return the maximum element in each window.

Input:

```python
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
```

Output:

```text
[3, 3, 5, 5, 6, 7]
```

### Code

```python
from collections import deque


def sliding_window_maximum(nums, k):
    if k <= 0 or k > len(nums):
        return []

    result = []
    queue = deque()  # Stores indices in decreasing value order

    for i, num in enumerate(nums):
        # Remove indices outside the current window
        while queue and queue[0] <= i - k:
            queue.popleft()

        # Remove smaller or equal values from the back
        while queue and nums[queue[-1]] <= num:
            queue.pop()

        queue.append(i)

        # A complete window exists from index k - 1
        if i >= k - 1:
            result.append(nums[queue[0]])

    return result


print(sliding_window_maximum(
    [1, 3, -1, -3, 5, 3, 6, 7], 3
))
# [3, 3, 5, 5, 6, 7]
```

### Why does this work?

- The front of the deque holds the index of the maximum element.
- Smaller values behind a new larger value can be discarded because they cannot become the maximum while that larger value remains in the window.
- Expired indices are removed from the front.
- Each index enters and leaves the deque at most once.

**Time complexity:** O(n).

**Auxiliary space:** O(k).

## 13. Sliding Window Minimum

To find the minimum instead, maintain values in increasing order.

```python
from collections import deque


def sliding_window_minimum(nums, k):
    if k <= 0 or k > len(nums):
        return []

    result = []
    queue = deque()

    for i, num in enumerate(nums):
        while queue and queue[0] <= i - k:
            queue.popleft()

        while queue and nums[queue[-1]] >= num:
            queue.pop()

        queue.append(i)

        if i >= k - 1:
            result.append(nums[queue[0]])

    return result


print(sliding_window_minimum([1, 3, -1, -3, 5, 3, 6, 7], 3))
# [-1, -3, -3, -3, 3, 3]
```

**Time complexity:** O(n).

**Auxiliary space:** O(k).

## 14. Monotonic Stack vs Monotonic Queue

| Feature | Monotonic Stack | Monotonic Queue |
|---|---|---|
| Main structure | Stack | Usually a deque |
| Common operations | Push and pop from top | Add and remove from both ends |
| Typical purpose | Next greater/smaller elements | Sliding-window minimum/maximum |
| Example | Daily Temperatures | Sliding Window Maximum |
| Typical complexity | O(n) | O(n) |

A monotonic queue is not simply a normal FIFO queue. Its elements are deliberately removed to maintain monotonic order.

## 15. How to Recognize These Problems

Look for phrases such as:

- Next greater element
- Next smaller element
- Nearest greater element to the left
- Days until a warmer temperature
- Largest rectangle in a histogram
- Maximum or minimum in every sliding window
- Stock span

These clues often suggest a monotonic stack or deque.

## 16. Common Mistakes

1. Storing values when indices are needed.
2. Using the wrong comparison operator for equal elements.
3. Forgetting to remove expired indices from a sliding window.
4. Assuming a stack that stores values has the same behavior as one storing indices.
5. Using a normal list for frequent front removals instead of `deque`.
6. Forgetting that each element can be pushed and popped at most once in a standard monotonic-stack solution.
7. Incorrectly calculating the width in the histogram problem.
8. Failing to validate the window size.

## 17. Practice Problems

### Beginner
- Next Greater Element
- Next Smaller Element
- Previous Greater Element
- Daily Temperatures

### Intermediate
- Stock Span
- Sliding Window Maximum
- Sliding Window Minimum
- Sum of Subarray Minimums

### Advanced
- Largest Rectangle in Histogram
- Maximal Rectangle
- Trapping Rain Water
- Shortest Subarray with Sum at Least K
- Online Stock Span

## 18. Interview Questions

**Q1. What is a monotonic stack?**

A stack that maintains elements in increasing or decreasing order.

**Q2. Why can a monotonic stack solve some problems in O(n)?**

Each element is typically pushed once and popped at most once, so the total number of stack operations is linear.

**Q3. Why store indices?**

Indices help update result positions, calculate distances, and determine whether elements fall outside a sliding window.

**Q4. What is a monotonic queue?**

A deque-based technique that maintains an ordered set of candidate elements, often for sliding-window minimum or maximum queries.

**Q5. What is the difference between next greater and previous greater element?**

Next greater searches to the right; previous greater searches to the left.

**Q6. Why use `deque` in sliding-window problems?**

It supports efficient insertion and removal at both ends.

## 19. Final Checklist

- [ ] Explain increasing and decreasing stacks.
- [ ] Solve next greater and next smaller element problems.
- [ ] Implement Daily Temperatures.
- [ ] Implement Stock Span.
- [ ] Understand Largest Rectangle in Histogram.
- [ ] Explain why a deque is useful for sliding windows.
- [ ] Implement sliding-window maximum and minimum.
- [ ] Handle duplicate values and expired indices correctly.
- [ ] Analyze time and auxiliary space complexity.
