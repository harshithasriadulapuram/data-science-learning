
# Prefix Sum + Hash Map Interview Practice in Python

## 1. What Is Prefix Sum + Hash Map?

Prefix sum stores cumulative sums so that range sums can be calculated efficiently.

A hash map stores previously seen prefix sums and how often they occur.

Combining them helps solve problems involving:

- Counting subarrays with a target sum.
- Finding subarrays with a particular sum.
- Finding the longest subarray with a target sum.
- Counting subarrays whose sum is divisible by `k`.
- Finding balanced binary subarrays.
- Counting subarrays with equal numbers of two values.

This combination is especially powerful when an array contains negative numbers, because a standard sliding window may not work reliably.

## 2. The Prefix Sum Formula

For an array:

```python
nums = [2, 4, 1, 3]
```

Define `prefix[i]` as the sum of the first `i` elements, with `prefix[0] = 0`.

```text
prefix = [0, 2, 6, 7, 10]
```

The sum of elements from index `left` through index `right`, inclusive, is:

```text
prefix[right + 1] - prefix[left]
```

For example, the sum of `[4, 1]` is:

```text
prefix[3] - prefix[1] = 7 - 2 = 5
```

## 3. The Key Hash Map Idea

Suppose the current prefix sum is `current_sum`, and we want a subarray whose sum equals `target`.

We need an earlier prefix sum satisfying:

```text
current_sum - previous_prefix = target
```

Rearranging:

```text
previous_prefix = current_sum - target
```

Therefore, while processing each element, look up `current_sum - target` in a hash map.

This equation is the foundation of many prefix-sum interview problems.

## 4. Problem 1: Count Subarrays with Sum K

### Problem statement

Given an integer array `nums` and an integer `k`, return the number of non-empty contiguous subarrays whose sum equals `k`.

Example:

```python
nums = [1, 1, 1]
k = 2
```

Output:

```text
2
```

The qualifying subarrays are the first two elements and the last two elements.

### Solution

```python
def subarray_sum(nums, k):
    prefix_count = {0: 1}
    current_sum = 0
    count = 0

    for num in nums:
        current_sum += num

        needed = current_sum - k
        count += prefix_count.get(needed, 0)

        prefix_count[current_sum] = (
            prefix_count.get(current_sum, 0) + 1
        )

    return count


print(subarray_sum([1, 1, 1], 2))
print(subarray_sum([1, 2, 3], 3))
print(subarray_sum([1, -1, 0], 0))
```

Output:

```text
2
2
3
```

### Why initialize `{0: 1}`?

It represents the empty prefix with sum zero.

Without it, subarrays beginning at index zero would be missed whenever their sum equals `k`.

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)` in the worst case.

## 5. Problem 2: Longest Subarray with Sum K

### Problem statement

Find the maximum length of a contiguous subarray whose sum equals `k`.

Example:

```python
nums = [10, 5, 2, 7, 1, 9]
k = 15
```

Output:

```text
4
```

The subarray `[5, 2, 7, 1]` has sum `15` and length `4`.

### Solution

```python
def longest_subarray_sum_k(nums, k):
    first_index = {0: -1}
    current_sum = 0
    max_length = 0

    for i, num in enumerate(nums):
        current_sum += num
        needed = current_sum - k

        if needed in first_index:
            length = i - first_index[needed]
            max_length = max(max_length, length)

        # Keep only the earliest index for each prefix sum.
        if current_sum not in first_index:
            first_index[current_sum] = i

    return max_length


print(longest_subarray_sum_k([10, 5, 2, 7, 1, 9], 15))
print(longest_subarray_sum_k([1, -1, 5, -2, 3], 3))
```

Output:

```text
4
4
```

### Why keep the earliest index?

For the same prefix sum, an earlier index produces a longer subarray when paired with a later index.

Overwriting the earliest index can lead to an incorrect shorter answer.

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)` in the worst case.

## 6. Problem 3: Check Whether a Subarray with Sum K Exists

### Problem statement

Determine whether at least one non-empty contiguous subarray sums to `k`.

### Solution

```python
def has_subarray_sum_k(nums, k):
    seen_prefix = {0}
    current_sum = 0

    for num in nums:
        current_sum += num

        if current_sum - k in seen_prefix:
            return True

        seen_prefix.add(current_sum)

    return False


print(has_subarray_sum_k([4, 2, -3, 1, 6], 3))
print(has_subarray_sum_k([1, 2, 3], 10))
```

Output:

```text
True
False
```

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)`.

## 7. Problem 4: Count Subarrays Whose Sum Is Divisible by K

### Problem statement

Count non-empty subarrays whose sum is divisible by `k`.

Example:

```python
nums = [4, 5, 0, -2, -3, 1]
k = 5
```

Output:

```text
7
```

### Key idea

If two prefix sums have the same remainder when divided by `k`, their difference is divisible by `k`.

For each prefix sum, calculate:

```python
remainder = current_sum % k
```

Python's modulo operation handles negative numbers correctly when `k` is positive.

### Solution

```python
def subarrays_div_by_k(nums, k):
    if k <= 0:
        raise ValueError("k must be positive")

    remainder_count = {0: 1}
    current_sum = 0
    count = 0

    for num in nums:
        current_sum += num
        remainder = current_sum % k

        count += remainder_count.get(remainder, 0)
        remainder_count[remainder] = (
            remainder_count.get(remainder, 0) + 1
        )

    return count


print(subarrays_div_by_k([4, 5, 0, -2, -3, 1], 5))
```

Output:

```text
7
```

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(min(N, k))` distinct remainders at most.

## 8. Problem 5: Binary Subarrays with Sum

### Problem statement

Given a binary array containing only zeros and ones, count the non-empty subarrays whose sum equals a target.

Example:

```python
nums = [1, 0, 1, 0, 1]
goal = 2
```

Output:

```text
4
```

### Solution

```python
def binary_subarrays_with_sum(nums, goal):
    prefix_count = {0: 1}
    current_sum = 0
    count = 0

    for num in nums:
        current_sum += num
        count += prefix_count.get(current_sum - goal, 0)

        prefix_count[current_sum] = (
            prefix_count.get(current_sum, 0) + 1
        )

    return count


print(binary_subarrays_with_sum([1, 0, 1, 0, 1], 2))
```

Output:

```text
4
```

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)`.

This is a specialized form of counting subarrays with sum `k`.

## 9. Problem 6: Contiguous Array with Equal Zeros and Ones

### Problem statement

Given a binary array, find the maximum length of a contiguous subarray containing equal numbers of zeros and ones.

Example:

```python
nums = [0, 1, 0]
```

Output:

```text
2
```

### Key idea

Replace every zero with `-1` and every one with `+1`.

A subarray contains equal numbers of zeros and ones exactly when its transformed sum equals zero.

### Solution

```python
def find_max_length(nums):
    first_index = {0: -1}
    balance = 0
    max_length = 0

    for i, num in enumerate(nums):
        if num == 0:
            balance -= 1
        else:
            balance += 1

        if balance in first_index:
            max_length = max(
                max_length,
                i - first_index[balance]
            )
        else:
            first_index[balance] = i

    return max_length


print(find_max_length([0, 1, 0]))
print(find_max_length([0, 1, 0, 1, 1, 0]))
```

Output:

```text
2
6
```

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)`.

## 10. Problem 7: Count Subarrays with Equal Numbers of Even and Odd Values

### Problem statement

Count the number of non-empty contiguous subarrays containing equal numbers of even and odd values.

### Approach

Transform each even number into `+1` and each odd number into `-1`.

Every valid subarray has a transformed sum of zero.

Count equal prefix sums.

### Solution

```python
def count_equal_even_odd(nums):
    prefix_count = {0: 1}
    balance = 0
    count = 0

    for num in nums:
        if num % 2 == 0:
            balance += 1
        else:
            balance -= 1

        count += prefix_count.get(balance, 0)
        prefix_count[balance] = (
            prefix_count.get(balance, 0) + 1
        )

    return count


print(count_equal_even_odd([2, 1, 4, 3]))
```

Output:

```text
4
```

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)`.

## 11. Problem 8: Count Subarrays with XOR K

This is a related pattern using prefix XOR instead of prefix sum.

### Problem statement

Count the number of non-empty subarrays whose bitwise XOR equals `k`.

The identity used is:

```text
prefix_xor ^ previous_prefix_xor = k
```

Therefore:

```text
previous_prefix_xor = prefix_xor ^ k
```

### Solution

```python
def count_subarrays_xor_k(nums, k):
    prefix_count = {0: 1}
    current_xor = 0
    count = 0

    for num in nums:
        current_xor ^= num

        needed = current_xor ^ k
        count += prefix_count.get(needed, 0)

        prefix_count[current_xor] = (
            prefix_count.get(current_xor, 0) + 1
        )

    return count


print(count_subarrays_xor_k([4, 2, 2, 6, 4], 6))
```

Output:

```text
4
```

### Complexity

- Time: `O(N)` on average.
- Auxiliary space: `O(N)`.

## 12. Choosing the Right Prefix-Sum Pattern

| Requirement | Technique |
|---|---|
| Count subarrays with sum K | Prefix sum + frequency map |
| Find the longest subarray with sum K | Prefix sum + earliest index |
| Check whether a target-sum subarray exists | Prefix sum + set |
| Count sums divisible by K | Prefix remainder frequencies |
| Equal zeros and ones | Transform values + prefix sum |
| Count subarrays with XOR K | Prefix XOR + frequency map |

## 13. Common Mistakes

1. Forgetting to initialize the frequency map with `{0: 1}` for counting subarrays.
2. Updating the map before counting matches, which can incorrectly count an empty subarray when the target is zero.
3. Overwriting the earliest index when finding the longest subarray.
4. Using a standard sliding window when negative numbers invalidate its assumptions.
5. Forgetting that a subarray must be contiguous.
6. Confusing prefix sums with suffix sums.
7. Using the sum formula instead of the XOR identity in XOR problems.
8. Ignoring constraints such as whether `k` must be positive.

## 14. Interview Practice Questions

Solve these in order:

1. Subarray Sum Equals K.
2. Continuous Subarray Sum.
3. Maximum Size Subarray Sum Equals K.
4. Contiguous Array.
5. Binary Subarrays With Sum.
6. Subarray Sums Divisible by K.
7. Count Number of Nice Subarrays.
8. Count Subarrays With XOR K.
9. Maximum Sum of Two Non-Overlapping Subarrays.
10. Longest Well-Performing Interval.

## 15. Final Interview Checklist

Before considering this topic mastered, make sure you can:

- Derive the prefix-sum formula.
- Explain why `current_sum - target` is the needed previous prefix.
- Distinguish frequency maps from earliest-index maps.
- Count subarrays without enumerating every possible subarray.
- Handle negative values correctly.
- Adapt prefix sum to remainders and prefix XOR.
- Explain the expected `O(N)` time complexity.

**Key takeaway:** Prefix sum converts a subarray-sum condition into a relationship between two prefix values. A hash map lets you find or count matching earlier prefixes efficiently.
