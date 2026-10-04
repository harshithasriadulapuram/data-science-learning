
# Binary Search Patterns — Interview Practice

## 1. What Is Binary Search?

Binary search efficiently searches a sorted search space by repeatedly eliminating half of the remaining candidates.

For a sorted list of n elements:

- Time complexity: O(log n).
- Iterative auxiliary space: O(1).
- Recursive auxiliary space: O(log n).

**Important:** Standard binary search requires the input to be sorted or the search space to have a suitable monotonic property.

## 2. Standard Binary Search

Problem: Find the index of a target in a sorted list. Return -1 if it does not exist.

```python
def binary_search(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left <= right:
        middle = left + (right - left) // 2

        if numbers[middle] == target:
            return middle
        elif numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return -1


print(binary_search([1, 3, 5, 7, 9], 7))  # 3
print(binary_search([1, 3, 5, 7, 9], 4))  # -1
```

### How it works

1. Calculate the middle index.
2. If the middle value equals the target, return its index.
3. If the middle value is smaller, search the right half.
4. Otherwise, search the left half.
5. Stop when the search interval becomes empty.

## 3. Find the First Occurrence

A sorted list may contain duplicates. To find the first occurrence, record a match and continue searching to the left.

```python
def first_occurrence(numbers, target):
    left = 0
    right = len(numbers) - 1
    answer = -1

    while left <= right:
        middle = left + (right - left) // 2

        if numbers[middle] == target:
            answer = middle
            right = middle - 1
        elif numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return answer


print(first_occurrence([1, 2, 2, 2, 4, 5], 2))  # 1
```

Time complexity: O(log n).

## 4. Find the Last Occurrence

When a match is found, continue searching to the right.

```python
def last_occurrence(numbers, target):
    left = 0
    right = len(numbers) - 1
    answer = -1

    while left <= right:
        middle = left + (right - left) // 2

        if numbers[middle] == target:
            answer = middle
            left = middle + 1
        elif numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return answer


print(last_occurrence([1, 2, 2, 2, 4, 5], 2))  # 3
```

Time complexity: O(log n).

## 5. Lower Bound and Upper Bound

These patterns are useful for duplicates and insertion positions.

### Lower bound

The first index where the value is greater than or equal to the target.

```python
def lower_bound(numbers, target):
    left = 0
    right = len(numbers)

    while left < right:
        middle = left + (right - left) // 2

        if numbers[middle] < target:
            left = middle + 1
        else:
            right = middle

    return left


print(lower_bound([1, 2, 2, 4, 6], 2))  # 1
print(lower_bound([1, 2, 4, 6], 3))     # 2
```

### Upper bound

The first index where the value is strictly greater than the target.

```python
def upper_bound(numbers, target):
    left = 0
    right = len(numbers)

    while left < right:
        middle = left + (right - left) // 2

        if numbers[middle] <= target:
            left = middle + 1
        else:
            right = middle

    return left


print(upper_bound([1, 2, 2, 4, 6], 2))  # 3
```

Both algorithms run in O(log n) time and O(1) auxiliary space.

## 6. Count the Occurrences of a Target

The number of occurrences can be calculated using the lower and upper bounds.

```python
def count_occurrences(numbers, target):
    return upper_bound(numbers, target) - lower_bound(
        numbers, target
    )


print(count_occurrences([1, 2, 2, 2, 4, 6], 2))  # 3
print(count_occurrences([1, 2, 4, 6], 3))        # 0
```

Time complexity: O(log n).

## 7. Search Insertion Position

Return the index where the target should be inserted to maintain sorted order.

```python
def search_insert(numbers, target):
    return lower_bound(numbers, target)


print(search_insert([1, 3, 5, 6], 5))  # 2
print(search_insert([1, 3, 5, 6], 2))  # 1
print(search_insert([1, 3, 5, 6], 7))  # 4
```

Time complexity: O(log n).

## 8. Search in a Rotated Sorted Array

A sorted array may have been rotated. For example:

`[4, 5, 6, 7, 0, 1, 2]`

At each iteration, at least one half is sorted. Determine which half contains the target.

```python
def search_rotated(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left <= right:
        middle = left + (right - left) // 2

        if numbers[middle] == target:
            return middle

        if numbers[left] <= numbers[middle]:
            if numbers[left] <= target < numbers[middle]:
                right = middle - 1
            else:
                left = middle + 1
        else:
            if numbers[middle] < target <= numbers[right]:
                left = middle + 1
            else:
                right = middle - 1

    return -1


print(search_rotated([4, 5, 6, 7, 0, 1, 2], 0))  # 4
```

Time complexity: O(log n), assuming distinct values.

With duplicates, the worst case can degrade to O(n).

## 9. Find the Minimum in a Rotated Sorted Array

```python
def find_minimum(numbers):
    if not numbers:
        raise ValueError("Input must not be empty")

    left = 0
    right = len(numbers) - 1

    while left < right:
        middle = left + (right - left) // 2

        if numbers[middle] > numbers[right]:
            left = middle + 1
        else:
            right = middle

    return numbers[left]


print(find_minimum([4, 5, 6, 7, 0, 1, 2]))  # 0
```

Time complexity: O(log n), assuming distinct values.

## 10. Binary Search on the Answer

Sometimes the problem does not ask you to search an existing list. Instead, it asks for the minimum or maximum value satisfying a condition.

This works when the condition is monotonic: once it becomes true, it remains true in the relevant search direction.

### Example: Minimum integer whose square is at least a target

```python
def minimum_integer_square_at_least(target):
    if target <= 0:
        return 0

    left = 1
    right = target

    while left < right:
        middle = left + (right - left) // 2

        if middle * middle >= target:
            right = middle
        else:
            left = middle + 1

    return left


print(minimum_integer_square_at_least(20))  # 5
```

Time complexity: O(log target).

The search space consists of possible answers rather than array indices.

## 11. Find the First True Position

Suppose a monotonic Boolean condition is false and then becomes true.

The goal is to find the first true position in a known finite range.

```python
def first_true(left, right, condition):
    while left < right:
        middle = left + (right - left) // 2

        if condition(middle):
            right = middle
        else:
            left = middle + 1

    if condition(left):
        return left

    return -1


answer = first_true(
    1,
    100,
    lambda number: number >= 37,
)

print(answer)  # 37
```

This implementation assumes the range is non-empty and the condition is monotonic. The final check handles the case where no position is true.

Time complexity: O(log(right - left + 1)) condition evaluations.

## 12. Common Binary Search Mistakes

1. Using binary search on unsorted data without a valid monotonic search property.
2. Using `left <= right` incorrectly.
3. Forgetting to update the search boundaries.
4. Returning immediately when the problem asks for the first or last occurrence.
5. Confusing lower bound with upper bound.
6. Creating an infinite loop by failing to shrink the interval.
7. Forgetting empty input.
8. Ignoring duplicate values in rotated arrays.
9. Using the wrong comparison for a boundary search.
10. Failing to define what the answer means when no match exists.

## 13. Interview Practice Problems

Try implementing each problem independently.

1. Find the first and last positions of a target.
2. Count target occurrences in a sorted list.
3. Find the insertion position of a target.
4. Search a rotated sorted array.
5. Find the minimum in a rotated sorted array.
6. Find the integer square root of a non-negative integer.
7. Find the first true value in a monotonic Boolean array.
8. Find a peak element.
9. Find the smallest capacity that can satisfy a workload limit.
10. Find the minimum speed needed to finish a task within a deadline.

## 14. Final Checklist

- [ ] Implement standard binary search.
- [ ] Implement first and last occurrence.
- [ ] Understand lower and upper bounds.
- [ ] Count duplicates in logarithmic time.
- [ ] Search rotated arrays.
- [ ] Find the minimum in a rotated array.
- [ ] Recognize monotonic conditions.
- [ ] Apply binary search to answer spaces.
- [ ] Explain the time and auxiliary space complexity.
