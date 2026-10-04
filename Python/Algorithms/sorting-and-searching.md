
# Python Searching and Sorting Algorithms

## 1. Linear Search

Linear search checks elements one by one until the target is found.

```python
def linear_search(numbers, target):
    for index, number in enumerate(numbers):
        if number == target:
            return index
    return -1

print(linear_search([10, 20, 30, 40], 30))  # 2
print(linear_search([10, 20, 30], 50))      # -1
```

**Time complexity:** O(n)  
**Space complexity:** O(1)

## 2. Binary Search

Binary search repeatedly halves the search range. The input must be sorted.

```python
def binary_search(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left <= right:
        middle = (left + right) // 2

        if numbers[middle] == target:
            return middle
        elif numbers[middle] < target:
            left = middle + 1
        else:
            right = middle - 1

    return -1

print(binary_search([10, 20, 30, 40, 50], 40))  # 3
```

**Time complexity:** O(log n)  
**Space complexity:** O(1)

## 3. Bubble Sort

Bubble sort repeatedly swaps adjacent elements that are out of order.

```python
def bubble_sort(numbers):
    arr = numbers.copy()
    n = len(arr)

    for i in range(n):
        swapped = False

        for j in range(n - 1 - i):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True

        if not swapped:
            break

    return arr

print(bubble_sort([5, 2, 8, 1, 3]))
# [1, 2, 3, 5, 8]
```

**Time complexity:** O(n²) worst case; O(n) best case  
**Space complexity:** O(n) here because we copy the input list.

## 4. Selection Sort

Selection sort finds the smallest remaining element and places it in the next position.

```python
def selection_sort(numbers):
    arr = numbers.copy()

    for i in range(len(arr)):
        min_index = i

        for j in range(i + 1, len(arr)):
            if arr[j] < arr[min_index]:
                min_index = j

        arr[i], arr[min_index] = arr[min_index], arr[i]

    return arr

print(selection_sort([64, 25, 12, 22, 11]))
# [11, 12, 22, 25, 64]
```

**Time complexity:** O(n²)  
**Space complexity:** O(n) here because we copy the input list.

## 5. Insertion Sort

Insertion sort builds a sorted portion by inserting each element into its correct position.

```python
def insertion_sort(numbers):
    arr = numbers.copy()

    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1

        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1

        arr[j + 1] = key

    return arr

print(insertion_sort([5, 2, 4, 1, 3]))
# [1, 2, 3, 4, 5]
```

**Time complexity:** O(n²) worst case; O(n) best case  
**Space complexity:** O(n) here because we copy the input list.

## 6. Python's Built-in Sorting

In real applications, Python's built-in sorting is usually preferable to implementing a basic sorting algorithm yourself.

```python
numbers = [5, 2, 8, 1, 3]

print(sorted(numbers))  # [1, 2, 3, 5, 8]

numbers.sort()
print(numbers)  # [1, 2, 3, 5, 8]
```

- `sorted()` returns a new sorted list.
- `.sort()` sorts the existing list in place.
- Both support `reverse=True` for descending order.

## 7. Sort Strings by Length

```python
words = ["banana", "kiwi", "apple", "fig"]

result = sorted(words, key=len)
print(result)  # ['fig', 'kiwi', 'apple', 'banana']
```

Words of equal length retain their original relative order.

## 8. Find the Largest and Smallest Values

```python
numbers = [10, 4, 25, 7, 1]

print(max(numbers))  # 25
print(min(numbers))  # 1
```

These functions raise `ValueError` for an empty iterable unless a `default` is supplied.

## 9. Find the Second-Largest Distinct Value

```python
def second_largest(numbers):
    unique = set(numbers)

    if len(unique) < 2:
        return None

    ordered = sorted(unique)
    return ordered[-2]

print(second_largest([10, 5, 20, 20, 8]))  # 10
print(second_largest([4, 4]))              # None
```

## 10. Search Using a Python Built-in

```python
numbers = [10, 20, 30, 40]

print(30 in numbers)  # True
print(50 in numbers)  # False
```

Membership testing on a list takes O(n) time in the worst case. For repeated membership checks, a set may be more efficient on average.

## Algorithm Comparison

| Algorithm | Best time | Average time | Worst time |
|---|---|---|---|
| Linear search | O(1) | O(n) | O(n) |
| Binary search | O(1) | O(log n) | O(log n) |
| Bubble sort | O(n) | O(n²) | O(n²) |
| Selection sort | O(n²) | O(n²) | O(n²) |
| Insertion sort | O(n) | O(n²) | O(n²) |

## Practice Challenges

1. Implement binary search recursively.
2. Sort a list without using `sort()` or `sorted()`.
3. Find the first and last positions of a target in a sorted list.
4. Merge two sorted lists into one sorted list.
5. Count how many times a target occurs in a sorted list.
6. Find the kth-largest distinct element.
