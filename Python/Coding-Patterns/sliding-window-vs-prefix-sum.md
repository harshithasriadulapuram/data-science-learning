
# Sliding Window vs Prefix Sum — Interview Practice

## 1. Why Are These Patterns Important?

Sliding Window and Prefix Sum are two common techniques for solving array and string problems efficiently.

Both can reduce repeated calculations, but they are useful in different situations.

- **Sliding Window:** Maintains information about a continuous range while moving its boundaries.
- **Prefix Sum:** Stores cumulative sums so that range sums can be calculated quickly.

The most important skill is recognizing which pattern fits the problem.

---

## 2. Sliding Window

Sliding Window is useful when working with contiguous subarrays or substrings.

### Example: Maximum Sum of a Subarray of Size K

```python
def max_sum_subarray(nums, k):
    if k <= 0 or k > len(nums):
        return None

    window_sum = sum(nums[:k])
    max_sum = window_sum

    for right in range(k, len(nums)):
        window_sum += nums[right]
        window_sum -= nums[right - k]
        max_sum = max(max_sum, window_sum)

    return max_sum


print(max_sum_subarray([2, 1, 5, 1, 3, 2], 3))
# Output: 9
```

### How It Works

For the array `[2, 1, 5, 1, 3, 2]` and `k = 3`:

1. First window: `[2, 1, 5]`, sum = 8.
2. Remove `2` and add `1`: `[1, 5, 1]`, sum = 7.
3. Remove `1` and add `3`: `[5, 1, 3]`, sum = 9.
4. Remove `5` and add `2`: `[1, 3, 2]`, sum = 6.

Maximum sum = `9`.

**Time complexity:** O(n)  
**Auxiliary space:** O(1)

---

## 3. Prefix Sum

Prefix Sum stores cumulative sums.

For an array:

`nums = [2, 4, 1, 5, 3]`

The prefix sum array is:

`prefix = [2, 6, 7, 12, 15]`

The sum of elements from index `left` to index `right`, inclusive, is:

`prefix[right] - prefix[left - 1]`

When `left == 0`, the range sum is simply `prefix[right]`.

### Example: Answer Range-Sum Queries

```python
def build_prefix_sum(nums):
    prefix = [0]

    for num in nums:
        prefix.append(prefix[-1] + num)

    return prefix


def range_sum(prefix, left, right):
    return prefix[right + 1] - prefix[left]


nums = [2, 4, 1, 5, 3]
prefix = build_prefix_sum(nums)

print(range_sum(prefix, 1, 3))
# Output: 10, because 4 + 1 + 5 = 10

print(range_sum(prefix, 0, 2))
# Output: 7, because 2 + 4 + 1 = 7
```

**Preprocessing time:** O(n)  
**Each range query:** O(1)  
**Auxiliary space:** O(n)

---

## 4. Key Differences

| Feature | Sliding Window | Prefix Sum |
|---|---|---|
| Main purpose | Maintain a moving range | Answer range-sum queries |
| Typical input | Contiguous subarrays or substrings | Arrays with range queries |
| Preprocessing | Often unnecessary | Usually O(n) |
| Range calculation | Update by adding/removing elements | Subtract cumulative sums |
| Typical space | O(1) for simple windows | O(n) |
| Negative numbers | Fixed windows work; variable-window conditions need care | Range sums work with negative numbers |
| Repeated queries | Depends on the problem | Very efficient for range sums |

---

## 5. When Should You Use Sliding Window?

Consider Sliding Window when:

- The problem involves a contiguous subarray or substring.
- You need a maximum, minimum, or count over a moving range.
- The window size is fixed.
- A variable window can expand or shrink according to a valid condition.

Examples:

- Maximum sum of a subarray of size K.
- Longest substring without repeating characters.
- Minimum-size subarray with sum at least a target, when the array contains positive numbers.
- Maximum number of vowels in a substring of length K.

**Important:** A variable-size sliding window based on increasing or decreasing sums generally cannot use the same simple logic when arbitrary negative numbers are present.

---

## 6. When Should You Use Prefix Sum?

Consider Prefix Sum when:

- You need many range-sum queries on an unchanged array.
- You need to count subarrays whose sum equals K.
- You need to calculate cumulative totals.
- You need to compare sums of different ranges efficiently.

Examples:

- Range-sum queries.
- Subarray Sum Equals K.
- Equilibrium index.
- Find the longest subarray with a target sum.
- Two-dimensional matrix range sums.

**Important:** Prefix Sum combined with a hashmap is especially useful for counting subarrays with a target sum, including arrays containing negative numbers.

---

## 7. Prefix Sum + Hashmap: Count Subarrays With Sum K

```python
def subarray_sum(nums, k):
    prefix_sum = 0
    count = 0
    frequency = {0: 1}

    for num in nums:
        prefix_sum += num

        count += frequency.get(prefix_sum - k, 0)

        frequency[prefix_sum] = (
            frequency.get(prefix_sum, 0) + 1
        )

    return count


print(subarray_sum([1, 1, 1], 2))
# Output: 2

print(subarray_sum([1, -1, 0], 0))
# Output: 3
```

### Why Does It Work?

Suppose the current prefix sum is `current_sum`.

If an earlier prefix sum was `current_sum - k`, then the elements between those two prefix positions sum to `k`.

Therefore:

`current_sum - previous_sum = k`

Rearranging:

`previous_sum = current_sum - k`

The hashmap stores how often each prefix sum has appeared.

**Time complexity:** O(n)  
**Auxiliary space:** O(n)

---

## 8. Common Interview Mistakes

1. Using a variable-size sliding window for arbitrary negative numbers without proving that the window condition is valid.
2. Forgetting that prefix-sum range queries use `prefix[right + 1] - prefix[left]` when a leading zero is included.
3. Forgetting to initialize the prefix-sum hashmap with `{0: 1}` when counting subarrays with sum K.
4. Updating the hashmap before counting matches, which can incorrectly count an empty subarray when `k = 0`.
5. Recalculating the entire window sum on every iteration instead of updating it incrementally.
6. Confusing a subarray, which is contiguous, with a subsequence, which need not be contiguous.

---

## 9. Practice Problems

### Beginner

- Maximum sum of a subarray of size K.
- Range Sum Query — Immutable.
- Find the equilibrium index.
- Maximum average subarray of size K.

### Intermediate

- Longest Substring Without Repeating Characters.
- Minimum Size Subarray Sum.
- Subarray Sum Equals K.
- Longest subarray with sum K.
- Binary Subarrays With Sum.

### Advanced

- Count subarrays divisible by K.
- Maximum sum circular subarray.
- Maximum sum rectangle in a 2D matrix.
- Shortest subarray with sum at least K, including negative values.

---

## 10. Interview Decision Checklist

Before coding, ask:

1. Does the problem involve a contiguous range?
2. Is the window size fixed or variable?
3. Are there negative numbers?
4. Do I need one answer or many range queries?
5. Can I update the current answer by removing the outgoing element and adding the incoming element?
6. Can prefix sums turn the condition into a lookup such as `current_sum - k`?

### Final Rule

- **Fixed-size contiguous range:** Usually consider Sliding Window.
- **Many range-sum queries:** Consider Prefix Sum.
- **Count subarrays with a target sum:** Consider Prefix Sum + Hashmap.
- **Variable-size window:** Check whether the problem's conditions allow the window to expand and shrink safely.
