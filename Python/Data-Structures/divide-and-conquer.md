
# Divide and Conquer in Python

## 1. What Is Divide and Conquer?

Divide and Conquer is an algorithm design technique that solves a large problem by breaking it into smaller subproblems, solving those subproblems, and combining their results.

It has three main steps:

1. **Divide:** Break the problem into smaller subproblems.
2. **Conquer:** Solve the smaller subproblems, usually recursively.
3. **Combine:** Combine the results to solve the original problem.

Common examples include:
- Binary Search
- Merge Sort
- Quick Sort
- Finding the maximum and minimum using recursion
- Counting inversions
- Closest pair of points

## 2. General Structure

```python
def divide_and_conquer(problem):
    # Base case
    if is_small_enough(problem):
        return solve_directly(problem)

    # Divide
    subproblems = divide(problem)

    # Conquer
    results = [
        divide_and_conquer(subproblem)
        for subproblem in subproblems
    ]

    # Combine
    return combine(results)
```

This is a conceptual template. The exact implementation depends on the problem.

## 3. Base Case

The base case stops recursion when the problem becomes small enough to solve directly.

For example, in Merge Sort, a list containing zero or one element is already sorted.

```python
if len(arr) <= 1:
    return arr
```

Without a correct base case, recursion may continue indefinitely until a recursion error occurs.

## 4. Example 1: Binary Search

Binary Search finds a target in a **sorted list** by repeatedly examining the middle element and eliminating half of the remaining search range.

### Code

```python
def binary_search(arr, target):
    left = 0
    right = len(arr) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1


numbers = [2, 4, 7, 10, 13, 18, 21]

print(binary_search(numbers, 13))  # 4
print(binary_search(numbers, 5))   # -1
```

This is the iterative version. Binary Search can also be implemented recursively.

### Recursive implementation

```python
def binary_search_recursive(arr, target, left, right):
    if left > right:
        return -1

    mid = left + (right - left) // 2

    if arr[mid] == target:
        return mid

    if arr[mid] < target:
        return binary_search_recursive(
            arr, target, mid + 1, right
        )

    return binary_search_recursive(
        arr, target, left, mid - 1
    )


numbers = [2, 4, 7, 10, 13, 18, 21]

print(binary_search_recursive(
    numbers, 13, 0, len(numbers) - 1
))  # 4
```

**Time complexity:** O(log n).

**Auxiliary space:**
- Iterative: O(1).
- Recursive: O(log n) for the recursion stack.

## 5. Example 2: Merge Sort

Merge Sort divides a list into two halves, recursively sorts both halves, and merges the sorted halves.

### Code

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2

    left_half = merge_sort(arr[:mid])
    right_half = merge_sort(arr[mid:])

    return merge(left_half, right_half)


def merge(left, right):
    result = []
    i = 0
    j = 0

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


numbers = [38, 27, 43, 3, 9, 82, 10]

print(merge_sort(numbers))
```

Output:

```text
[3, 9, 10, 27, 38, 43, 82]
```

### How it works

For `[8, 3, 5, 2]`:

1. Divide into `[8, 3]` and `[5, 2]`.
2. Divide again into single-element lists.
3. Merge `[8]` and `[3]` into `[3, 8]`.
4. Merge `[5]` and `[2]` into `[2, 5]`.
5. Merge the two sorted halves into `[2, 3, 5, 8]`.

**Time complexity:** O(n log n).

**Auxiliary space:** O(n) for the temporary lists and merging. This implementation also creates slices.

## 6. Example 3: Quick Sort

Quick Sort selects a pivot, partitions the elements around it, and recursively sorts the two partitions.

In this example, elements less than or equal to the pivot go to the left, and larger elements go to the right.

### Code

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr

    pivot = arr[len(arr) // 2]

    smaller = [x for x in arr if x < pivot]
    equal = [x for x in arr if x == pivot]
    larger = [x for x in arr if x > pivot]

    return quick_sort(smaller) + equal + quick_sort(larger)


numbers = [8, 3, 1, 7, 0, 10, 2, 3]

print(quick_sort(numbers))
```

Output:

```text
[0, 1, 2, 3, 3, 7, 8, 10]
```

This implementation handles duplicate values by collecting values equal to the pivot.

**Average time complexity:** O(n log n).

**Worst-case time complexity:** O(n²), for example when partitions repeatedly become very unbalanced.

**Auxiliary space:** O(n) or more across the recursive calls and temporary lists in this implementation. It is not an in-place implementation.

## 7. Example 4: Find the Maximum Element Recursively

Divide the list into smaller parts, find each part's maximum, and compare the results.

```python
def find_maximum(arr):
    if not arr:
        raise ValueError("arr must not be empty")

    if len(arr) == 1:
        return arr[0]

    mid = len(arr) // 2

    left_max = find_maximum(arr[:mid])
    right_max = find_maximum(arr[mid:])

    return max(left_max, right_max)


print(find_maximum([12, 5, 27, 8, 19]))  # 27
```

**Time complexity:** O(n).

**Auxiliary space:** O(log n) recursion depth, plus temporary slicing allocations. The slicing means the implementation creates additional intermediate lists.

For a simple maximum search, a loop is usually more memory-efficient. This example demonstrates the divide-and-conquer pattern.

## 8. Example 5: Count Inversions

An inversion is a pair of indices `(i, j)` such that:

- `i < j`
- `arr[i] > arr[j]`

For example:

```text
[2, 4, 1, 3, 5]
```

The inversions are `(2, 1)`, `(4, 1)`, and `(4, 3)`, so the count is `3`.

A modified Merge Sort can count inversions efficiently.

### Code

```python
def count_inversions(arr):
    def sort_and_count(items):
        if len(items) <= 1:
            return items, 0

        mid = len(items) // 2

        left, left_count = sort_and_count(items[:mid])
        right, right_count = sort_and_count(items[mid:])

        merged = []
        i = 0
        j = 0
        split_count = 0

        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                merged.append(left[i])
                i += 1
            else:
                merged.append(right[j])
                split_count += len(left) - i
                j += 1

        merged.extend(left[i:])
        merged.extend(right[j:])

        total = left_count + right_count + split_count
        return merged, total

    return sort_and_count(arr)[1]


print(count_inversions([2, 4, 1, 3, 5]))  # 3
print(count_inversions([5, 4, 3, 2, 1]))  # 10
```

### Why add `len(left) - i`?

When `right[j] < left[i]`, every remaining element in the sorted left half is greater than `right[j]`. Each one forms an inversion with it.

**Time complexity:** O(n log n).

**Auxiliary space:** O(n), excluding the original input.

## 9. Divide and Conquer Recurrence Relations

A recurrence relation describes the running time of a recursive algorithm.

A common form is:

`T(n) = aT(n / b) + f(n)`

Where:
- `a` is the number of recursive subproblems.
- `n / b` is the approximate size of each subproblem.
- `f(n)` is the work done outside recursive calls.

Examples:

| Algorithm | Approximate recurrence | Time complexity |
|---|---|---|
| Binary Search | `T(n) = T(n/2) + O(1)` | O(log n) |
| Merge Sort | `T(n) = 2T(n/2) + O(n)` | O(n log n) |
| Balanced recursive maximum | `T(n) = 2T(n/2) + O(1)` | O(n) |
| Naive recursive Fibonacci | `T(n) = T(n-1) + T(n-2) + O(1)` | Exponential |

The Master Theorem can solve many recurrences of the form `T(n) = aT(n/b) + f(n)`, subject to its conditions.

## 10. Divide and Conquer vs Dynamic Programming

Both techniques solve smaller subproblems, but they are used differently.

| Feature | Divide and Conquer | Dynamic Programming |
|---|---|---|
| Main idea | Divide, solve, combine | Store and reuse subproblem results |
| Typical subproblems | Often independent | Often overlapping |
| Common implementation | Recursion | Memoization or tabulation |
| Example | Merge Sort | Fibonacci with memoization |
| Main benefit | Simplifies large problems | Avoids repeated calculations |

These are general patterns, not absolute rules. Some algorithms combine divide and conquer with memoization or other optimization techniques.

## 11. Common Mistakes

1. Forgetting the base case.
2. Dividing the problem incorrectly.
3. Failing to make progress toward the base case.
4. Combining results incorrectly.
5. Assuming every divide-and-conquer algorithm runs in O(n log n).
6. Ignoring the memory cost of list slicing.
7. Forgetting that Quick Sort has O(n²) worst-case time.
8. Using Binary Search on an unsorted list.
9. Confusing auxiliary space with total space including output.

## 12. Practice Problems

### Beginner
- Binary Search
- Find the maximum and minimum recursively
- Merge two sorted arrays
- Implement Merge Sort

### Intermediate
- Quick Sort
- Count Inversions
- Search in a Rotated Sorted Array
- Find the majority element using divide and conquer
- Find the kth largest element

### Advanced
- Closest Pair of Points
- Median of Two Sorted Arrays
- Count Smaller Numbers After Self
- Maximum Subarray using divide and conquer
- Strassen's Matrix Multiplication

## 13. Interview Questions

**Q1. What is divide and conquer?**

An algorithm design technique that divides a problem into smaller subproblems, solves them, and combines their results.

**Q2. What are the three main steps?**

Divide, conquer, and combine.

**Q3. Why is Merge Sort O(n log n)?**

It has O(log n) levels of division and O(n) work across each level for merging.

**Q4. What is the worst-case complexity of Quick Sort?**

O(n²), when partitions repeatedly become highly unbalanced.

**Q5. Why does Binary Search require sorted data?**

It relies on ordering to determine which half can be discarded.

**Q6. What is a recurrence relation?**

An equation describing an algorithm's running time in terms of smaller inputs.

**Q7. What is the difference between divide and conquer and dynamic programming?**

Divide and conquer typically solves smaller subproblems independently, while dynamic programming stores and reuses results of overlapping subproblems.

## 14. Final Checklist

- [ ] Explain divide, conquer, and combine.
- [ ] Write recursive Binary Search.
- [ ] Implement Merge Sort without copying the solution.
- [ ] Explain Quick Sort's average and worst-case complexity.
- [ ] Understand inversion counting using Merge Sort.
- [ ] Write and interpret recurrence relations.
- [ ] Explain why slicing can increase memory usage.
- [ ] Compare divide and conquer with dynamic programming.
