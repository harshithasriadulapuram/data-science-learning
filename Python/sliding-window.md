
# Sliding Window Technique in Python

## 1. Introduction

The Sliding Window technique is an algorithmic approach used to solve problems involving contiguous subarrays or substrings.

Instead of recalculating the result for every possible window, we maintain a window and update it as it moves through the data.

It can often reduce time complexity from O(n²) to O(n).

### Example

Given:

```python
numbers = [2, 1, 5, 1, 3, 2]
```

Find the maximum sum of any three consecutive elements.

The windows are:

- `[2, 1, 5]` → sum = 8
- `[1, 5, 1]` → sum = 7
- `[5, 1, 3]` → sum = 9
- `[1, 3, 2]` → sum = 6

The answer is `9`.

---

## 2. Types of Sliding Window

There are two main types:

1. Fixed-size sliding window
2. Variable-size sliding window

---

## 3. Fixed-Size Sliding Window

### Definition

A fixed-size window always contains a specified number of elements.

For example, if `k = 3`, every window contains exactly three elements.

### Problem: Maximum Sum of K Consecutive Elements

```python
def max_sum_subarray(numbers, k):
    if k <= 0 or k > len(numbers):
        raise ValueError("k must be between 1 and the array length")

    window_sum = sum(numbers[:k])
    max_sum = window_sum

    for right in range(k, len(numbers)):
        window_sum += numbers[right]
        window_sum -= numbers[right - k]

        max_sum = max(max_sum, window_sum)

    return max_sum


numbers = [2, 1, 5, 1, 3, 2]
print(max_sum_subarray(numbers, 3))  # 9
```

### How It Works

1. Calculate the sum of the first `k` elements.
2. Add the next element entering the window.
3. Subtract the element leaving the window.
4. Update the maximum sum.
5. Continue until the array ends.

### Complexity

- Time: O(n)
- Auxiliary space: O(1), excluding the temporary slice used to initialize the sum.

---

## 4. Average of Every K-Sized Window

### Problem

Return the average of every contiguous window of size `k`.

```python
def window_averages(numbers, k):
    if k <= 0 or k > len(numbers):
        raise ValueError("k must be between 1 and the array length")

    window_sum = sum(numbers[:k])
    averages = [window_sum / k]

    for right in range(k, len(numbers)):
        window_sum += numbers[right]
        window_sum -= numbers[right - k]
        averages.append(window_sum / k)

    return averages


print(window_averages([1, 3, 2, 6, -1, 4, 1, 8, 2], 5))
# [2.2, 2.0, 2.2, 3.6, 2.8]
```

### Complexity

- Time: O(n)
- Auxiliary space: O(n) for the output list.

---

## 5. Variable-Size Sliding Window

### Definition

A variable-size window expands or shrinks depending on a condition.

Typical steps:

1. Expand the right boundary.
2. Update the information maintained by the window.
3. While the window violates the condition, move the left boundary.
4. Record the best valid window.

This pattern is useful for problems involving sums, distinct elements, and substrings.

---

## 6. Smallest Subarray with Sum at Least a Target

### Problem

Given an array of positive integers and a target, find the minimum length of a contiguous subarray whose sum is at least the target.

Return `0` if no such subarray exists.

```python
def min_subarray_length(target, numbers):
    left = 0
    window_sum = 0
    min_length = float("inf")

    for right in range(len(numbers)):
        window_sum += numbers[right]

        while window_sum >= target:
            min_length = min(
                min_length,
                right - left + 1
            )

            window_sum -= numbers[left]
            left += 1

    return 0 if min_length == float("inf") else min_length


print(min_subarray_length(7, [2, 3, 1, 2, 4, 3]))
# 2
```

### Explanation

The target is `7`.

The subarray `[4, 3]` has a sum of `7` and length `2`.

No valid subarray has length `1`, so the minimum length is `2`.

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

**Important:** This approach requires positive numbers for the stated sliding-window logic. Negative numbers can invalidate the assumption that shrinking the window predictably reduces its sum.

---

## 7. Longest Substring Without Repeating Characters

### Problem

Given a string, find the length of the longest substring that contains no repeated characters.

```python
def longest_unique_substring(text):
    left = 0
    last_seen = {}
    max_length = 0

    for right, char in enumerate(text):
        if char in last_seen and last_seen[char] >= left:
            left = last_seen[char] + 1

        last_seen[char] = right

        max_length = max(
            max_length,
            right - left + 1
        )

    return max_length


print(longest_unique_substring("abcabcbb"))  # 3
print(longest_unique_substring("bbbbb"))     # 1
print(longest_unique_substring("pwwkew"))    # 3
```

### How It Works

For `"abcabcbb"`:

- `"abc"` contains no repeated characters.
- When another `a` appears, move the left boundary past the previous `a`.
- Continue updating the window.
- The maximum valid length is `3`.

### Complexity

- Time: O(n) on average.
- Auxiliary space: O(min(n, character-set size)) for the dictionary of last-seen positions.

---

## 8. Longest Substring with At Most K Distinct Characters

### Problem

Find the length of the longest substring containing at most `k` distinct characters.

```python
def longest_substring_k_distinct(text, k):
    if k <= 0:
        return 0

    left = 0
    frequency = {}
    max_length = 0

    for right, char in enumerate(text):
        frequency[char] = frequency.get(char, 0) + 1

        while len(frequency) > k:
            left_char = text[left]
            frequency[left_char] -= 1

            if frequency[left_char] == 0:
                del frequency[left_char]

            left += 1

        max_length = max(
            max_length,
            right - left + 1
        )

    return max_length


print(longest_substring_k_distinct("eceba", 2))  # 3
print(longest_substring_k_distinct("aa", 1))     # 2
```

### Complexity

- Time: O(n) on average.
- Auxiliary space: O(k) for the maintained frequency dictionary, up to the number of distinct characters.

---

## 9. Count Subarrays with a Product Less Than K

### Problem

Count contiguous subarrays whose product is strictly less than `k`.

This sliding-window solution assumes all array elements are positive integers.

```python
def count_subarrays_product_less_than_k(numbers, k):
    if any(number <= 0 for number in numbers):
        raise ValueError("All numbers must be positive")

    if k <= 1:
        return 0

    left = 0
    product = 1
    count = 0

    for right, number in enumerate(numbers):
        product *= number

        while product >= k and left <= right:
            product //= numbers[left]
            left += 1

        count += right - left + 1

    return count


print(count_subarrays_product_less_than_k([10, 5, 2, 6], 100))
# 8
```

### Why Add `right - left + 1`?

Once the current window is valid, every subarray ending at `right` and starting anywhere from `left` through `right` is also valid because all numbers are positive integers.

There are `right - left + 1` such subarrays.

### Complexity

- Time: O(n)
- Auxiliary space: O(1)

---

## 10. Sliding Window vs Brute Force

Suppose we need the maximum sum of `k` consecutive elements.

### Brute-Force Approach

```python
def max_sum_brute_force(numbers, k):
    if k <= 0 or k > len(numbers):
        raise ValueError("Invalid window size")

    best = float("-inf")

    for start in range(len(numbers) - k + 1):
        current_sum = sum(numbers[start:start + k])
        best = max(best, current_sum)

    return best
```

The repeated summation takes O(nk) time.

### Sliding Window Approach

After calculating the first window sum, each next window requires only one addition and one subtraction.

- Time: O(n)
- Auxiliary space: O(1), excluding initialization details.

For large arrays, this can be significantly faster.

---

## 11. Common Mistakes

1. Forgetting to remove the element that leaves the window.
2. Using `if` instead of `while` when the window may need repeated shrinking.
3. Confusing a substring with a subsequence.
4. Forgetting that subarrays and substrings must be contiguous.
5. Applying a sum-based shrinking strategy to arrays with negative values without proving it works.
6. Mishandling empty arrays or invalid window sizes.
7. Updating the answer before ensuring the window satisfies the required condition.

---

## 12. Interview Questions

### Beginner

1. What is the Sliding Window technique?
2. What is the difference between fixed-size and variable-size windows?
3. Find the maximum sum of `k` consecutive elements.
4. Calculate the average of each window of size `k`.
5. Explain why sliding windows can improve performance.

### Intermediate

6. Find the minimum-length subarray with sum at least a target.
7. Find the longest substring without repeating characters.
8. Find the longest substring containing at most `k` distinct characters.
9. Count subarrays whose product is less than `k`.
10. Find the maximum number of vowels in any substring of length `k`.

### Advanced

11. Find the minimum window substring containing all required characters.
12. Find all starting indices of anagrams of a pattern.
13. Find the longest repeating-character replacement substring under a replacement budget.
14. Find the minimum window containing every distinct character from the input.
15. Find the maximum number of consecutive ones after flipping at most `k` zeros.

---

## 13. Coding Practice Checklist

- [ ] Maximum sum of a fixed-size subarray.
- [ ] Average of every window of size `k`.
- [ ] Maximum number of vowels in a window.
- [ ] Minimum-length subarray with sum at least a target.
- [ ] Longest substring without repeated characters.
- [ ] Longest substring with at most `k` distinct characters.
- [ ] Count subarrays with product less than `k`.
- [ ] Find all anagrams of a pattern.
- [ ] Solve a minimum-window substring problem.
- [ ] Compare sliding-window and brute-force solutions.

---

## 14. Key Takeaways

- Sliding Window is especially useful for contiguous subarray and substring problems.
- Fixed-size windows maintain a constant number of elements.
- Variable-size windows expand and shrink according to conditions.
- Efficient sliding-window solutions often run in O(n) time.
- Always verify the assumptions about the input, especially when using sums or products.
- Practise explaining why the window can move forward without reconsidering every previous element.
