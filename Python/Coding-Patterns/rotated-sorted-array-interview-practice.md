
# Binary Search on Rotated Sorted Arrays — Interview Practice

## 1. What Is a Rotated Sorted Array?

A rotated sorted array is created by moving some elements from the beginning of a sorted array to the end.

Example:

Original array:

`[1, 2, 3, 4, 5, 6, 7]`

After rotation:

`[4, 5, 6, 7, 1, 2, 3]`

The array is not completely sorted, but at least one half around the midpoint is sorted when all elements are distinct.

Binary search can use this property to eliminate half of the search space at each step.

---

## 2. Search in a Rotated Sorted Array

Given a rotated sorted array of distinct integers, return the index of the target. Return -1 if it is absent.

### Example

```python
print(search_rotated([4, 5, 6, 7, 0, 1, 2], 0))
# 4

print(search_rotated([4, 5, 6, 7, 0, 1, 2], 3))
# -1
```

### Solution

```python
def search_rotated(nums, target):
    left = 0
    right = len(nums) - 1

    while left <= right:
        mid = (left + right) // 2

        if nums[mid] == target:
            return mid

        # Left half is sorted.
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1

        # Right half is sorted.
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1

    return -1
```

### How It Works

1. Calculate the middle index.
2. If the middle value is the target, return its index.
3. Determine which half is sorted.
4. Check whether the target lies inside that sorted half.
5. Continue searching in the appropriate half.

**Time complexity:** O(log n)  
**Auxiliary space:** O(1)

**Assumption:** The input contains distinct elements.

---

## 3. Find the Minimum in a Rotated Sorted Array

Given a rotated sorted array of distinct integers, return its minimum element.

```python
def find_minimum(nums):
    if not nums:
        raise ValueError("Array must not be empty")

    left = 0
    right = len(nums) - 1

    while left < right:
        mid = (left + right) // 2

        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid

    return nums[left]


print(find_minimum([3, 4, 5, 1, 2]))
# 1

print(find_minimum([1, 2, 3, 4, 5]))
# 1
```

### Why Compare with the Rightmost Element?

- If `nums[mid] > nums[right]`, the minimum must be to the right of `mid`.
- Otherwise, the minimum is at `mid` or to its left.

The loop finishes when `left == right`, identifying the minimum.

**Time complexity:** O(log n)  
**Auxiliary space:** O(1)

---

## 4. Find the Number of Rotations

For a sorted array rotated to the right, the number of rotations equals the index of its minimum element, assuming distinct elements.

```python
def count_rotations(nums):
    if not nums:
        raise ValueError("Array must not be empty")

    left = 0
    right = len(nums) - 1

    while left < right:
        mid = (left + right) // 2

        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid

    return left


print(count_rotations([4, 5, 6, 7, 0, 1, 2]))
# 4

print(count_rotations([1, 2, 3, 4, 5]))
# 0
```

**Time complexity:** O(log n)  
**Auxiliary space:** O(1)

---

## 5. Search in a Rotated Array with Duplicates

When duplicate values are allowed, it may be impossible to determine which half is sorted from the endpoints.

For example:

`[1, 0, 1, 1, 1]`

If the left, middle, and right values are equal, shrink the search boundaries.

```python
def search_rotated_duplicates(nums, target):
    left = 0
    right = len(nums) - 1

    while left <= right:
        mid = (left + right) // 2

        if nums[mid] == target:
            return True

        # Ambiguous boundaries: remove one element
        # from each side to make progress.
        if (
            nums[left] == nums[mid]
            and nums[mid] == nums[right]
        ):
            left += 1
            right -= 1

        elif nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1

        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1

    return False


print(search_rotated_duplicates([2, 5, 6, 0, 0, 1, 2], 0))
# True

print(search_rotated_duplicates([1, 0, 1, 1, 1], 2))
# False
```

**Average and typical time:** Often O(log n).  
**Worst-case time:** O(n), because duplicates can force repeated boundary shrinking.  
**Auxiliary space:** O(1)

---

## 6. Search in a Rotated Array — Common Variations

### Variation A: Return the smallest element

Use binary search to find the minimum.

### Variation B: Count rotations

Find the index of the minimum.

### Variation C: Search with duplicates

Handle ambiguous boundaries by shrinking the search interval.

### Variation D: Find the rotation pivot

The pivot is the index of the minimum element for a standard right-rotated sorted array with distinct values.

### Variation E: Find a target in a rotated array

Identify the sorted half and determine whether the target lies within it.

---

## 7. Common Mistakes

1. Assuming the entire rotated array is sorted.
2. Forgetting that one half is sorted when elements are distinct.
3. Using incorrect inclusive or exclusive boundary conditions.
4. Confusing the minimum element's index with the number of rotations for other rotation conventions.
5. Assuming duplicate elements always allow O(log n) time.
6. Forgetting the empty-array edge case when finding the minimum.
7. Updating `left` or `right` without guaranteeing progress.

---

## 8. Practice Problems

### Beginner

- Search in Rotated Sorted Array.
- Find Minimum in Rotated Sorted Array.
- Count Rotations in a Sorted Array.

### Intermediate

- Search in Rotated Sorted Array II.
- Find the Rotation Count.
- Find the Single Element in a Sorted Array.

### Advanced

- Median of Two Sorted Arrays.
- Find Minimum in a Rotated Sorted Array with Duplicates.
- Search in a Nearly Sorted Array.

---

## 9. Interview Checklist

Before coding, ask:

1. Is the array sorted and then rotated?
2. Are duplicates allowed?
3. Must I return the index, value, or a Boolean?
4. Which half is sorted?
5. Does the target lie inside the sorted half?
6. Can I guarantee logarithmic time, or do duplicates allow a linear worst case?

### Final Takeaway

Binary search on a rotated sorted array works by identifying the sorted half and discarding the half that cannot contain the answer. With duplicates, some cases become ambiguous and may require linear time.
