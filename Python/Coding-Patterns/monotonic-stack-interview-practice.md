
# Monotonic Stack — Interview Practice

## 1. What Is a Monotonic Stack?

A monotonic stack is a stack that maintains its elements in a specific order.

There are two main types:

- **Monotonic increasing stack:** Elements increase from the bottom to the top.
- **Monotonic decreasing stack:** Elements decrease from the bottom to the top.

The stack is maintained by removing elements that violate the required order.

### Why Is It Useful?

Without a monotonic stack, some problems require repeatedly scanning the remaining array, leading to O(n²) time.

A monotonic stack can often solve these problems in O(n) time because each element is pushed and popped at most once.

---

## 2. Next Greater Element to the Right

Given an array, find the first greater element to the right of each element. If no greater element exists, return -1.

### Example

Input:

`[2, 1, 2, 4, 3]`

Output:

`[4, 2, 4, -1, -1]`

### Python Solution

```python
def next_greater_element(nums):
    result = [-1] * len(nums)
    stack = []

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            index = stack.pop()
            result[index] = num

        stack.append(i)

    return result


print(next_greater_element([2, 1, 2, 4, 3]))
# [4, 2, 4, -1, -1]
```

### Explanation

1. Store indices in the stack rather than values.
2. When the current number is greater than the number at the top index, the current number is the answer for that index.
3. Pop the resolved index and update the result.
4. Push the current index.
5. Any unresolved positions remain -1.

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

---

## 3. Next Greater Element to the Left

Find the nearest greater element to the left of every element.

```python
def next_greater_left(nums):
    result = [-1] * len(nums)
    stack = []

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] <= num:
            stack.pop()

        if stack:
            result[i] = nums[stack[-1]]

        stack.append(i)

    return result


print(next_greater_left([2, 1, 4, 3]))
# [-1, 2, -1, 4]
```

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

The stack contains indices of elements that may be answers for future elements.

---

## 4. Daily Temperatures

For each day, find how many days must pass before a warmer temperature. Return 0 if no warmer day exists.

```python
def daily_temperatures(temperatures):
    result = [0] * len(temperatures)
    stack = []

    for i, temperature in enumerate(temperatures):
        while (
            stack
            and temperatures[stack[-1]] < temperature
        ):
            previous_day = stack.pop()
            result[previous_day] = i - previous_day

        stack.append(i)

    return result


print(daily_temperatures([73, 74, 75, 71, 69, 72, 76, 73]))
# [1, 1, 4, 2, 1, 1, 0, 0]
```

### Key Idea

The stack stores indices of days that have not yet found a warmer temperature.

When a warmer temperature arrives, it resolves all earlier stack entries that are cooler.

---

## 5. Stock Span Problem

The stock span is the number of consecutive days ending today for which the stock price was less than or equal to today's price.

```python
def stock_span(prices):
    spans = []
    stack = []

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

### Explanation

The stack maintains indices of prices in decreasing order from bottom to top.

Prices less than or equal to the current price are removed. The nearest remaining greater price determines the span boundary.

---

## 6. Largest Rectangle in a Histogram

Given bar heights, find the largest rectangular area that can fit inside the histogram.

```python
def largest_rectangle_area(heights):
    stack = []
    max_area = 0

    for i in range(len(heights) + 1):
        current_height = (
            heights[i] if i < len(heights) else 0
        )

        while (
            stack
            and heights[stack[-1]] > current_height
        ):
            height = heights[stack.pop()]

            left_boundary = stack[-1] if stack else -1
            width = i - left_boundary - 1

            max_area = max(max_area, height * width)

        stack.append(i)

    return max_area


print(largest_rectangle_area([2, 1, 5, 6, 2, 3]))
# 10
```

### How It Works

When a shorter bar appears, the bars taller than it can no longer extend to the current index.

For each popped bar:

- Its height determines the rectangle height.
- The current index is its right boundary.
- The new stack top, or -1, determines its left boundary.
- Width = `right_boundary - left_boundary - 1`.

A final zero-height sentinel forces the remaining bars to be processed.

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

---

## 7. Trapping Rain Water

Given bar heights, calculate how much rainwater can be trapped.

This solution uses a monotonic stack to calculate water between boundaries.

```python
def trap_rain_water(height):
    stack = []
    water = 0

    for i, current_height in enumerate(height):
        while (
            stack
            and current_height > height[stack[-1]]
        ):
            bottom = stack.pop()

            if not stack:
                break

            left = stack[-1]
            width = i - left - 1

            bounded_height = (
                min(height[left], current_height)
                - height[bottom]
            )

            water += width * bounded_height

        stack.append(i)

    return water


print(trap_rain_water([0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]))
# 6
```

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

---

## 8. How to Recognize a Monotonic Stack Problem

Look for phrases such as:

- Next greater or next smaller element.
- Previous greater or previous smaller element.
- Nearest element satisfying a comparison.
- Days until a warmer temperature.
- Maximum rectangle in a histogram.
- Number of consecutive elements before a greater element.
- Calculate trapped water between boundaries.

Ask yourself:

1. Am I repeatedly searching left or right for a qualifying element?
2. Can I permanently discard elements that can no longer be answers?
3. Can a stack preserve the unresolved candidates in sorted order?

If yes, consider a monotonic stack.

---

## 9. Common Mistakes

1. Storing values when indices are required to calculate distances or widths.
2. Using `<` when the problem requires `<=`, or vice versa.
3. Forgetting that unresolved next-greater elements should remain -1.
4. Calculating histogram width incorrectly.
5. Assuming every monotonic stack problem uses the same increasing/decreasing condition.
6. Forgetting to process remaining elements after the main loop when the algorithm requires it.
7. Using a nested loop when each element can be processed with a monotonic stack.

---

## 10. Practice Problems

### Beginner

- Next Greater Element I.
- Next Greater Element to the Left.
- Daily Temperatures.
- Stock Span.

### Intermediate

- Next Greater Element II.
- Online Stock Span.
- Remove K Digits.
- Sum of Subarray Minimums.
- Asteroid Collision.

### Advanced

- Largest Rectangle in Histogram.
- Maximal Rectangle.
- Trapping Rain Water.
- Sum of Subarray Ranges.

---

## 11. Complexity Summary

For the standard monotonic stack technique:

- **Time complexity:** O(n), because each element is pushed and popped at most once.
- **Auxiliary space:** O(n) in the worst case.

The exact comparison operator and stack order depend on the problem.

## Final Takeaway

A monotonic stack is not simply a stack sorted for convenience. It is a way to maintain unresolved candidates so that elements that can no longer be useful are removed efficiently.
