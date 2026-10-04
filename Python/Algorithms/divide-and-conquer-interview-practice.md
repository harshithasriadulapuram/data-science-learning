
# Divide and Conquer — Interview Practice

## 1. What Is Divide and Conquer?

Divide and Conquer is an algorithm design technique that solves a problem by:

1. **Divide:** Break the problem into smaller subproblems.
2. **Conquer:** Solve the smaller subproblems, usually recursively.
3. **Combine:** Combine their results to solve the original problem.

Examples include Merge Sort, Quick Sort, Binary Search, and counting inversions.

## 2. Divide and Conquer vs Dynamic Programming

| Divide and Conquer | Dynamic Programming |
|---|---|
| Often solves independent subproblems | Often reuses overlapping subproblems |
| Usually uses recursion | Uses memoization or tabulation, often |
| Example: Merge Sort | Example: Fibonacci with memoization |

These techniques can overlap. For example, some divide-and-conquer algorithms can benefit from memoization.

## 3. Binary Search

**Problem:** Find the index of a target in a sorted array. Return `-1` if it is absent.

```python
def binary_search(nums, target):
    left = 0
    right = len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1


print(binary_search([1, 3, 5, 7, 9], 7))  # 3
print(binary_search([1, 3, 5, 7, 9], 4))  # -1
```

**Time complexity:** O(log n)  
**Auxiliary space:** O(1)

Binary search works only when the search space satisfies the required ordering condition.

## 4. Merge Sort

Merge Sort recursively splits an array into halves, sorts each half, and merges the sorted halves.

```python
def merge_sort(nums):
    if len(nums) <= 1:
        return nums

    mid = len(nums) // 2

    left = merge_sort(nums[:mid])
    right = merge_sort(nums[mid:])

    return merge(left, right)


def merge(left, right):
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


print(merge_sort([38, 27, 43, 3, 9, 82, 10]))
# [3, 9, 10, 27, 38, 43, 82]
```

**Time complexity:** O(n log n)  
**Auxiliary space:** O(n), excluding the recursion stack.

Merge Sort is stable when equal elements are taken from the left half first.

## 5. Quick Sort

Quick Sort selects a pivot, partitions elements around it, and recursively sorts the partitions.

```python
def quick_sort(nums):
    if len(nums) <= 1:
        return nums

    pivot = nums[len(nums) // 2]

    smaller = [x for x in nums if x < pivot]
    equal = [x for x in nums if x == pivot]
    greater = [x for x in nums if x > pivot]

    return quick_sort(smaller) + equal + quick_sort(greater)


print(quick_sort([5, 3, 8, 4, 2, 7, 1, 10]))
# [1, 2, 3, 4, 5, 7, 8, 10]
```

This readable version creates additional lists and is not an in-place implementation.

**Average time complexity:** O(n log n)  
**Worst-case time complexity:** O(n²)  
**Space complexity:** Depends on recursion depth and temporary lists; this implementation also allocates partition lists.

## 6. Count Inversions

An inversion is a pair of indices `(i, j)` such that:

```text
i < j and nums[i] > nums[j]
```

A modified Merge Sort can count inversions efficiently.

```python
def count_inversions(nums):
    def sort_and_count(arr):
        if len(arr) <= 1:
            return arr, 0

        mid = len(arr) // 2
        left, left_count = sort_and_count(arr[:mid])
        right, right_count = sort_and_count(arr[mid:])

        merged = []
        i = j = 0
        cross_count = 0

        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                merged.append(left[i])
                i += 1
            else:
                merged.append(right[j])
                cross_count += len(left) - i
                j += 1

        merged.extend(left[i:])
        merged.extend(right[j:])

        total = left_count + right_count + cross_count
        return merged, total

    _, count = sort_and_count(nums)
    return count


print(count_inversions([2, 4, 1, 3, 5]))  # 3
```

**Time complexity:** O(n log n)  
**Auxiliary space:** O(n), excluding recursion overhead.

## 7. Find the K-th Largest Element

The K-th largest element can be found using Quickselect, which uses partitioning similar to Quick Sort.

```python
import random


def kth_largest(nums, k):
    if not 1 <= k <= len(nums):
        raise ValueError("k must be between 1 and len(nums)")

    target = len(nums) - k
    left = 0
    right = len(nums) - 1

    while left <= right:
        pivot_index = random.randint(left, right)
        pivot = nums[pivot_index]

        nums[pivot_index], nums[right] = nums[right], nums[pivot_index]

        store = left

        for i in range(left, right):
            if nums[i] < pivot:
                nums[store], nums[i] = nums[i], nums[store]
                store += 1

        nums[store], nums[right] = nums[right], nums[store]

        if store == target:
            return nums[store]
        elif store < target:
            left = store + 1
        else:
            right = store - 1

    raise RuntimeError("Unexpected partition state")


print(kth_largest([3, 2, 1, 5, 6, 4], 2))  # 5
```

**Expected time complexity:** O(n)  
**Worst-case time complexity:** O(n²)  
**Auxiliary space:** O(1), excluding the input list and random pivot selection.

This function modifies the input list.

## 8. Maximum Subarray Sum Using Divide and Conquer

**Problem:** Find the maximum sum of a non-empty contiguous subarray.

```python
def max_subarray_sum(nums):
    if not nums:
        raise ValueError("Input must not be empty")

    def solve(left, right):
        if left == right:
            return nums[left]

        mid = (left + right) // 2

        left_best = solve(left, mid)
        right_best = solve(mid + 1, right)

        total = 0
        best_left_suffix = float("-inf")

        for i in range(mid, left - 1, -1):
            total += nums[i]
            best_left_suffix = max(best_left_suffix, total)

        total = 0
        best_right_prefix = float("-inf")

        for i in range(mid + 1, right + 1):
            total += nums[i]
            best_right_prefix = max(best_right_prefix, total)

        crossing_best = best_left_suffix + best_right_prefix

        return max(left_best, right_best, crossing_best)

    return solve(0, len(nums) - 1)


print(max_subarray_sum([-2, 1, -3, 4, -1, 2, 1, -5, 4]))
# 6
```

**Time complexity:** O(n log n)  
**Auxiliary space:** O(log n) for the recursion stack.

The maximum subarray is `[4, -1, 2, 1]`, whose sum is `6`.

## 9. Complexity Summary

| Algorithm | Best/Average Time | Worst Time |
|---|---|---|
| Binary Search | O(log n) | O(log n) |
| Merge Sort | O(n log n) | O(n log n) |
| Quick Sort | O(n log n) average | O(n²) |
| Quickselect | O(n) expected | O(n²) |
| Merge Sort inversion counting | O(n log n) | O(n log n) |
| Divide-and-conquer maximum subarray | O(n log n) | O(n log n) |

## 10. Interview Practice Problems

### Beginner
- [ ] Implement recursive binary search.
- [ ] Implement Merge Sort.
- [ ] Explain the divide, conquer, and combine steps.
- [ ] Merge two sorted arrays.

### Intermediate
- [ ] Implement Quick Sort.
- [ ] Count inversions in an array.
- [ ] Find the K-th largest element using Quickselect.
- [ ] Find the majority element using divide and conquer.

### Advanced
- [ ] Find the maximum subarray sum using divide and conquer.
- [ ] Find the median of two sorted arrays.
- [ ] Count reverse pairs.
- [ ] Solve a closest-pair-of-points problem.

## 11. Common Interview Questions

**Q1. What are the three stages of divide and conquer?**

Divide the problem, conquer the smaller subproblems, and combine their results.

**Q2. Why does Merge Sort run in O(n log n)?**

There are O(log n) levels of splitting, and merging across each level processes O(n) elements.

**Q3. Is Quick Sort always O(n log n)?**

No. Poorly balanced partitions can produce O(n²) worst-case time.

**Q4. What is the difference between Quick Sort and Quickselect?**

Quick Sort processes both partitions. Quickselect continues only into the partition containing the target rank.

**Q5. How is divide and conquer related to recursion?**

Recursion is a common implementation technique, but divide and conquer is the broader algorithm design strategy.

## 12. Final Revision Checklist

- [ ] Explain divide, conquer, and combine.
- [ ] Implement binary search.
- [ ] Implement Merge Sort and explain its complexity.
- [ ] Explain Quick Sort's worst case.
- [ ] Solve inversion counting using Merge Sort.
- [ ] Implement Quickselect.
- [ ] Solve maximum subarray sum using divide and conquer.
- [ ] Analyze recursion depth and auxiliary space.
