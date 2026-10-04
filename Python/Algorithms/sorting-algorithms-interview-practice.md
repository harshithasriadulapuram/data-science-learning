
# Sorting Algorithms — Interview Practice

## 1. What Is Sorting?

Sorting arranges elements in a specified order, usually ascending or descending.

Example:

```python
numbers = [5, 2, 8, 1, 3]
numbers.sort()
print(numbers)
# [1, 2, 3, 5, 8]
```

Sorting is important because it makes many operations easier, including binary search, duplicate detection, and interval processing.

## 2. Important Sorting Properties

### Stable sorting

A sorting algorithm is stable if elements with equal keys preserve their original relative order.

For example, consider records with scores:

```python
students = [
    ("Anu", 90),
    ("Bala", 80),
    ("Chitra", 90),
]
```

A stable sort by score keeps Anu before Chitra because they had equal scores and Anu appeared first.

### In-place sorting

An in-place algorithm uses very little additional memory, often O(1) auxiliary space under the usual convention.

The exact space requirement depends on the implementation. An algorithm can modify its input without necessarily using constant auxiliary space.

### Comparison-based sorting

These algorithms determine order through comparisons between elements.

Examples include merge sort, heap sort, insertion sort, and quicksort.

## 3. Sorting Algorithm Comparison

| Algorithm | Best time | Average time | Worst time | Typical auxiliary space |
|---|---|---|---|---|
| Bubble sort, optimized | O(n) | O(n²) | O(n²) | O(1) |
| Selection sort | O(n²) | O(n²) | O(n²) | O(1) |
| Insertion sort | O(n) | O(n²) | O(n²) | O(1) |
| Merge sort | O(n log n) | O(n log n) | O(n log n) | O(n) |
| Quicksort | O(n log n) | O(n log n) | O(n²) | O(log n) average stack |
| Heap sort | O(n log n) | O(n log n) | O(n log n) | O(1) for iterative in-place heap sort |

Space complexity varies with implementation. Recursive quicksort can use O(n) stack space in the worst case. Python's built-in sorting uses a different algorithm and implementation strategy.

## 4. Bubble Sort

Bubble sort repeatedly compares adjacent elements and swaps them if they are out of order.

```python
def bubble_sort(numbers):
    numbers = numbers.copy()
    n = len(numbers)

    for i in range(n):
        swapped = False

        for j in range(0, n - i - 1):
            if numbers[j] > numbers[j + 1]:
                numbers[j], numbers[j + 1] = (
                    numbers[j + 1],
                    numbers[j],
                )
                swapped = True

        if not swapped:
            break

    return numbers


print(bubble_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

Complexity:
- Best time: O(n), when already sorted.
- Average time: O(n²).
- Worst time: O(n²).
- Auxiliary space: O(n) for the copied list; the sorting procedure itself uses O(1) extra space.

Bubble sort is easy to understand but inefficient for large inputs.

## 5. Selection Sort

Selection sort repeatedly finds the smallest remaining element and places it at the next position.

```python
def selection_sort(numbers):
    numbers = numbers.copy()
    n = len(numbers)

    for i in range(n):
        minimum_index = i

        for j in range(i + 1, n):
            if numbers[j] < numbers[minimum_index]:
                minimum_index = j

        numbers[i], numbers[minimum_index] = (
            numbers[minimum_index],
            numbers[i],
        )

    return numbers


print(selection_sort([64, 25, 12, 22, 11]))
# [11, 12, 22, 25, 64]
```

Complexity:
- Best, average, and worst time: O(n²).
- Auxiliary space for the sorting procedure: O(1).
- This function uses O(n) additional space because it copies the input.

Selection sort performs relatively few swaps, but it still makes quadratic numbers of comparisons.

## 6. Insertion Sort

Insertion sort builds a sorted prefix one element at a time.

```python
def insertion_sort(numbers):
    numbers = numbers.copy()

    for i in range(1, len(numbers)):
        current = numbers[i]
        j = i - 1

        while j >= 0 and numbers[j] > current:
            numbers[j + 1] = numbers[j]
            j -= 1

        numbers[j + 1] = current

    return numbers


print(insertion_sort([5, 2, 4, 6, 1, 3]))
# [1, 2, 3, 4, 5, 6]
```

Complexity:
- Best time: O(n).
- Average and worst time: O(n²).
- Auxiliary space for the sorting procedure: O(1).
- The copying in this implementation adds O(n) space.

Insertion sort works well for small or nearly sorted inputs.

## 7. Merge Sort

Merge sort divides the list, sorts each half recursively, and merges the sorted halves.

```python
def merge_sort(numbers):
    if len(numbers) <= 1:
        return numbers.copy()

    middle = len(numbers) // 2

    left = merge_sort(numbers[:middle])
    right = merge_sort(numbers[middle:])

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

Complexity:
- Best, average, and worst time: O(n log n).
- Auxiliary space: O(n), including temporary lists and merged results.

Merge sort is stable when the merge step consistently takes the left element first on equal keys, as shown here.

## 8. Quicksort

Quicksort selects a pivot, partitions the elements around it, and recursively sorts the partitions.

The following implementation uses the last element as the pivot.

```python
def quick_sort(numbers):
    numbers = numbers.copy()

    def sort(left, right):
        if left >= right:
            return

        pivot = numbers[right]
        smaller = left

        for current in range(left, right):
            if numbers[current] <= pivot:
                numbers[smaller], numbers[current] = (
                    numbers[current],
                    numbers[smaller],
                )
                smaller += 1

        numbers[smaller], numbers[right] = (
            numbers[right],
            numbers[smaller],
        )

        sort(left, smaller - 1)
        sort(smaller + 1, right)

    sort(0, len(numbers) - 1)
    return numbers


print(quick_sort([10, 7, 8, 9, 1, 5]))
# [1, 5, 7, 8, 9, 10]
```

Complexity:
- Best and average time: O(n log n).
- Worst time: O(n²), for highly unbalanced partitions.
- Average recursion stack space: O(log n).
- Worst recursion stack space: O(n).
- The copying in this implementation requires O(n) additional space.

Choosing a poor pivot can make quicksort slow. Randomized pivot selection or better pivot strategies can reduce the likelihood of bad partitions.

## 9. Heap Sort

Heap sort uses a binary heap to repeatedly extract the largest element.

Python's `heapq` provides a min-heap. The implementation below demonstrates the same principle by using a max-heap built in place.

```python
def heap_sort(numbers):
    numbers = numbers.copy()
    n = len(numbers)

    def sift_down(root, end):
        while True:
            child = 2 * root + 1

            if child >= end:
                return

            if (
                child + 1 < end
                and numbers[child + 1] > numbers[child]
            ):
                child += 1

            if numbers[root] >= numbers[child]:
                return

            numbers[root], numbers[child] = (
                numbers[child],
                numbers[root],
            )
            root = child

    # Build a max-heap.
    for root in range(n // 2 - 1, -1, -1):
        sift_down(root, n)

    # Move the largest element to the end.
    for end in range(n - 1, 0, -1):
        numbers[0], numbers[end] = numbers[end], numbers[0]
        sift_down(0, end)

    return numbers


print(heap_sort([12, 11, 13, 5, 6, 7]))
# [5, 6, 7, 11, 12, 13]
```

Complexity:
- Best, average, and worst time: O(n log n).
- Auxiliary space for the sorting procedure: O(1).
- The copy used to preserve the input requires O(n) additional space.

Heap sort is not stable in its usual in-place form.

## 10. Python's Built-In Sorting

In real Python applications, prefer the built-in sorting tools unless you have a specific reason to implement a sorting algorithm yourself.

```python
numbers = [5, 2, 8, 1, 3]

ascending = sorted(numbers)
descending = sorted(numbers, reverse=True)

print(ascending)
# [1, 2, 3, 5, 8]

print(descending)
# [8, 5, 3, 2, 1]

numbers.sort()
print(numbers)
# [1, 2, 3, 5, 8]
```

- `sorted(iterable)` returns a new list.
- `list.sort()` sorts the list in place and returns `None`.
- Python's sorting algorithm, Timsort, is stable.
- Its worst-case time complexity is O(n log n).
- `sorted()` requires O(n) output storage.
- `list.sort()` generally uses temporary memory, so it should not be described as strictly O(1) auxiliary space.

### Sort objects by a key

```python
students = [
    {"name": "Anu", "score": 90},
    {"name": "Bala", "score": 75},
    {"name": "Chitra", "score": 85},
]

students_sorted = sorted(
    students,
    key=lambda student: student["score"],
    reverse=True,
)

print(students_sorted)
```

Use the `key` argument to sort records by a specific field.

## 11. Sorting Interview Questions

1. What is the difference between stable and unstable sorting?
2. Which sorting algorithms guarantee O(n log n) worst-case time?
3. Why can quicksort degrade to O(n²)?
4. Why is insertion sort suitable for nearly sorted data?
5. Why does merge sort require additional memory?
6. Is selection sort stable in its standard implementation?
7. What is the difference between `sorted()` and `.sort()`?
8. What algorithm does Python use for sorting?
9. Why might built-in sorting outperform a hand-written implementation?
10. When would you choose merge sort over quicksort?

## 12. Practice Problems

Try implementing these without looking at the solutions above.

1. Sort a list in ascending order using insertion sort.
2. Sort a list of tuples by the second element.
3. Merge two already sorted lists.
4. Count the number of inversions in a list.
5. Find the kth largest element.
6. Sort an array containing only 0s, 1s, and 2s.
7. Sort intervals by their starting point.
8. Merge overlapping intervals.
9. Determine whether a list is already sorted.
10. Explain the time and space complexity of each solution.

## 13. Final Checklist

- [ ] Implement bubble, selection, and insertion sort.
- [ ] Explain merge sort and its recurrence.
- [ ] Implement quicksort and explain pivot selection.
- [ ] Understand heap sort and heap operations.
- [ ] Compare stability, memory use, and worst-case time.
- [ ] Use Python's built-in sorting correctly.
- [ ] Solve sorting-based interview problems.
- [ ] State complexity for the actual implementation, including copying and recursion.
